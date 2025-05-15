Base .NET project setup

1- Creat a new project using `Blank Solution` option

2- Add a new project to the solution, in this case we used the `Web Api` option

3- At the solution level, right click in the solution and select `Add` -> `New EditorConfig`
	-this file is used to define code style and formatting rules for the solution.

4- Inside the `Solution Items` folder, add a new .xml file called `Directory.Build.props`
	- **`<TargetFramework>net9.0</TargetFramework>`**  
		Sets the Target Framework Moniker (TFM) to .NET 9.0 for every project under this directory.

	- **`<ImplicitUsings>enable</ImplicitUsings>`**  
		Turns on global implicit `using` directives (e.g. `System`, `System.Collections.Generic`, etc.), so you don’t have to import them in every file.

	- **`<Nullable>enable</Nullable>`**  
		Enables nullable reference types across all projects, activating `?` annotations and null-safety warnings.

	- **`<AnalysisLevel>latest</AnalysisLevel>`**  
		Uses the newest “warning wave” of built-in .NET analyzers (CAxxxx).  
		- You’ll automatically get any new rules/updates shipped with the SDK.

	- **`<AnalysisMode>All</AnalysisMode>`**  
		Enables **all** built-in Roslyn analyzers (both code-quality and code-style), not just the “recommended” subset.

	- **`<TreatWarningsAsErrors>true</TreatWarningsAsErrors>`**  
		Promotes **all compiler warnings** (CSxxxx) to errors, failing the build on any warning.

	- **`<CodeAnalysisTreatWarningsAsErrors>true</CodeAnalysisTreatWarningsAsErrors>`**  
		Promotes **all code-analysis warnings** (CAxxxx) to errors as well.

	- **`<EnforceCodeStyleInBuild>true</EnforceCodeStyleInBuild>`**  
		Runs code-style rules (IDExxxx) during MSBuild, surfacing violations as build warnings or errors.


	- If we do this, we can then remove the `TargetFramework` and `Nullable` properties from the project files.

5- Inside the `Solution Items` folder, we will add a .xml file called `Directory.Packages.props`
	- This file is used to define package references that are shared across all projects in the solution~. So this way we can manage all of the packages versions in one place.
	If we need to use a package in any of the projects, we can just add the package reference to the project file and it will use the version defined in this file.

	-> PackageVersion: first we define a package version in the `Directory.Packages.props` file
	-> PackageReference: then we add the package reference to the project file, and it will use the version defined in the `Directory.Packages.props` file.

6- For code quality, we will add the nuget package `SonarAnalyzer.CSharp` to the solution. This package is used to analyze the code and find bugs, vulnerabilities, and code smells. It is a static code analysis tool that helps to improve the quality of the code.
	- This package is added to the `Directory.Packages.props` file, so it will be used in all projects in the solution.

6.1- To enable this package to work in every project, we can do the following:
	- Go to `Directory.Build.props` and add this inside the <Project> tag:
		`<ItemGroup Condition="'$(MSBuildProjectExtension)' != .dcproj">
		<PackageReference Include="SonarAnalyzer.CSharp">
			<PrivateAssets>all</PrivateAssets>
			<IncludeAssets>runtime; build; native; contentfiles; analyzers; buildtransitive</IncludeAssets>
		</PackageReference>
	</ItemGroup>`
	- .dcproj is referencing a docker compose project, so we don't want to run the analyzer in that project
	- this will add a package reference to all of the projects in the solution, except for the docker compose project
	- what's inside the package reference tag is created by the `SonarAnalyzer.CSharp` package

7- To add containerization, go the project you want, in this case we will add a Dockerfile to the Web API project.
	- Right click on the project and select `Add` -> `Docker Support`
	- Select `Linux` as the OS
	- This will add a Dockerfile to the project, and a .dockerignore file
	- The Dockerfile is used to build the image for the project, and the .dockerignore file is used to ignore files that are not needed in the image.
	- The Dockerfile is already configured to use the `mcr.microsoft.com/dotnet/aspnet:9.0` image as the base image, and it will copy the project files to the image and run the project

8- To add a docker compose file to the solution, right click on the project you want, in this case it will be the `Web.Api` and select `Add` -> `Docker Compose`
	- This will add a docker-compose.yml file to the solution, and a .dockerignore file
	- The docker-compose.yml file is used to define the services that will be run in the containers, and the .dockerignore file is used to ignore files that are not needed in the image
	- The docker-compose.yml file is already configured to use the `mcr.microsoft.com/dotnet/aspnet:9.0` image as the base image, and it will copy the project files to the image and run the project
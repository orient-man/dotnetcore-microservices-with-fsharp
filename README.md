Writing microservices in .NET Core 1.1 using F#
===

## Step 1: Basic project structure

    # create folder for solution
    mkdir AdderService
    cd AdderService

    # create empty solution
    dotnet new sln -n AdderService

    # create service project of type... console
    mkdir src
    cd src
    dotnet new console -lang F# -n AdderService

    # create unit test project (we will use xUnit)
    dotnet new xunit -lang F# -n AdderService.Tests

    # add both projects to solution
    cd ..
    dotnet sln add .\src\AdderService\AdderService.fsproj
    dotnet sln add .\src\AdderService.Tests\AdderService.Tests.fsproj

Now we can test if it builds and runs:

    dotnet restore
    dotnet build
    dotnet run --project .\src\AdderService\AdderService.fsproj

Should output:

    Hello World from F#!

## Step 2:

Replace NuGet with [Paket](https://fsprojects.github.io/Paket/). Download `paket.bootstrapper.exe` from https://github.com/fsprojects/Paket/releases/latest and put in `.paket` folder.
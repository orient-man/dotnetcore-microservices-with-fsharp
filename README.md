Writing microservices in .NET Core 1.1 using F#
===

Hello fellow dev! Are you ready to leave your comfort zone? Are your ready to:

* Leave Visual Studio behind?
* Embrace command line?
* Use open source tools instead of Microsoft blessed ones?
* Try new language (F#)?

If you answered yes then follow me! :)

## Setup

What you need to start?

* Windows, Mac or Linux
* [.NET Core SDK 1.1](https://www.microsoft.com/net/download/core)
* Text editor. I recommend [Vim](http://www.vim.org/) (with [F# bindings](https://github.com/fsharp/vim-fsharp)) or [Visual Studio Code](https://code.visualstudio.com/) (with [Ionide](http://ionide.io/) plugin)

## Step 1: Create basic project structure

We will use ``dotnet`` CLI tool for that task:

    # create folder for solution
    mkdir AdderService
    cd AdderService

    # create empty solution
    dotnet new sln -n AdderService

    # create service project of type... console (which will become Microservice)
    mkdir src
    cd src
    dotnet new console -lang F# -n AdderService

    # create unit test project (we will use xUnit template)
    dotnet new xunit -lang F# -n AdderService.Tests

    # add both projects to solution
    cd ..
    dotnet sln add .\src\AdderService\AdderService.fsproj
    dotnet sln add .\src\AdderService.Tests\AdderService.Tests.fsproj

Now we can test if it builds and runs (`dotnet` tool generates projects that contain some basic code):

    dotnet restore
    dotnet build
    dotnet run --project .\src\AdderService\AdderService.fsproj
    # should output:
    Hello World from F#!

We can also run tests:

    dotnet test .\src\AdderService.Tests\AdderService.Tests.fsproj

## Step 2: Migrate from NuGet to Paket

Managing dependencies with [Paket](https://fsprojects.github.io/Paket/) is so much easier than with NuGet that it's not even funny anymore. Just give it try!

To migrate project:

* Create a `.paket` folder in the root of your solution.
* Download the latest [paket.bootstrapper.exe](https://github.com/fsprojects/Paket/releases/latest) into that folder.
* Run `.paket\paket.bootstrapper.exe`. This will download the latest `paket.exe` (only the former should be committed to your repo).
* Run `.paket\paket.exe convert-from-nuget` to migrate.

Now all your references will be kept in simple text files: ``paket.dependecies``
(for solution) and ``paket.references`` (per project).

Restoring packages with ``dotnet restore`` should still work.
Writing microservices in .NET Core with F# (Part I)
===

## Intro

Hello fellow dev! Are you ready to step out of your comfort zone? Are your ready to:

* Leave Visual Studio behind
* Embrace command line
* Use open source tools instead of Microsoft blessed ones
* Try new language (F#)

If you answered "yes" then follow me! :)

## Prerequisites

What you need to start:

* Windows, Mac or Linux machine
* [.NET Core SDK 1.1](https://www.microsoft.com/net/download/core)
* Text editor. I recommend [Vim](http://www.vim.org/) (with [F# bindings](https://github.com/fsharp/vim-fsharp)) or [Visual Studio Code](https://code.visualstudio.com/) (with awesome [Ionide](http://ionide.io/) plugin)

## Step 1: Create basic project structure

We will use ``dotnet`` CLI tool for that task. Just remember to change your
 [slash orientation](https://twitter.com/reverentgeek/status/789135336437800960) if you are
 on Mac/Linux:

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

## Step 2: Migration from NuGet to Paket

Managing dependencies with [Paket](https://fsprojects.github.io/Paket/) is so much easier than with NuGet that it's not even funny anymore. Just give it try!

Why Paket?

* Neihter store package version in the physical path nor put references in your project files (project.json is dead, long live Paket!)
* Checks for conflicting versions i.e. same package in two projects but with different versions
* Allow to specify only [root dependencies](https://fsprojects.github.io/Paket/paket-simplify.html) and manages transitive dependencies internally
* Allow to have a dependency on [single file from GitHub or Gist](https://fsprojects.github.io/Paket/github-dependencies.html)
* It's super-duper fast
* Last but not least it [simplifies working with scripts](https://fsprojects.github.io/Paket/paket-generate-load-scripts.html) (we will learn about *.fsx files later)

To migrate project:

* Create a `.paket` folder in the root of your solution.
* Download the latest [paket.bootstrapper.exe](https://github.com/fsprojects/Paket/releases/latest) into that folder.
* Mac/Linux: At the time of writing Paket still needs [Mono](http://www.mono-project.com/download/) to run (it's not .NET Core app yet).
* Run `.paket\paket.bootstrapper.exe`. This will download the latest `paket.exe` (only the former should be committed to your repo).
* Run `.paket\paket.exe convert-from-nuget` to migrate.

Now all your references will be kept in simple text files: ``paket.dependecies`` and ``paket.references``.

Restoring packages with ``dotnet restore`` should still work.

Take a look at `paket.depnendecies` which contains top level dependencies from all projects in the solution:

    source https://dotnet.myget.org/F/dotnet-core/api/v3/index.json
    source https://dotnet.myget.org/F/cli-deps/api/v3/index.json
    source https://api.nuget.org/v3/index.json

    nuget FSharp.Core >= 4.1.0
    nuget FSharp.NET.Sdk >= 1.0.0
    nuget Microsoft.NET.Test.Sdk 15.0.0
    nuget xunit 2.2.0
    nuget xunit.runner.visualstudio 2.2.0

For for the curious: the ``paket.lock`` file holds the concrete dependency resolution of all direct and transitive dependencies of your project:

    FRAMEWORK: NETCORE11
    NUGET
      remote: https://api.nuget.org/v3/index.json
        FSharp.Core (4.1.12)
        FSharp.NET.Sdk (1.0.3)
        Microsoft.NET.Test.Sdk (15.0)
          Microsoft.TestPlatform.TestHost (>= 15.0)
        Microsoft.TestPlatform.TestHost (15.0)
        xunit (2.2)
          xunit.assert (2.2)
          xunit.core (2.2)
        xunit.assert (2.2)
        xunit.core (2.2)
          xunit.extensibility.core (2.2)
          xunit.extensibility.execution (2.2)
        xunit.extensibility.core (2.2)
        xunit.extensibility.execution (2.2)
        xunit.runner.visualstudio (2.2)

The ``paket.references`` files specifies dependencies only for particular project. Your project files will be kept clear! There is only one line indicating that Paket is being used:

    <Import Project="..\..\.paket\Paket.Restore.targets" />

Because we will be targeting only .NET Core 1.1 (not full .NET Framework) we can make things little bit faster by adding one line at the beginning:

    framework: netcoreapp1.1

and run `.paket\paket.exe install`.

## Wrap up

What we have done:

* Created cross platform app with unit tests using only command line tools
* Introduced Paket dependency manager

In the next part we will upgrade our "Hello World" to full-fledged microservice. Stay tuned.

PS You can find source code on [GitHub](https://github.com/orient-man/dotnetcore-microservices-with-fsharp/tree/part-1).
# DevOps Engineering
DevOps is considered a fundamental part of a microservice based system. This section will be discuss the tools and methodologies put in practice by the team.

## DVCS Strategy
Having a large set of repositories, each typically maintained by a single developer (or at most two using pair-programming), the chosen branching strategy is **Trunk-based**. This simplifies the management of the repositories by individual developers, while still giving them the possibility to occasionally open feature/hotfix branches if needed.

## Licenses
Each piece of software made is provided under the [MIT License](https://en.wikipedia.org/wiki/MIT_License), which makes it [FOSS](https://en.wikipedia.org/wiki/Free_and_open-source_software).

## Versioning
Each of the softwares developed is versioned with the Semantic Versioning system. Git annotated tags were used to declare new versions and trigger the release system.
A [tool](https://github.com/EasyDesk/action-semver-checkout/blob/master/git-compute-version.sh) was implemented to search for the latest tag and generate a version for the build automation.

## Software Build Lifecycle
Every step of the software build lifecycle was automated through Git and [GitHub Actions](https://docs.github.com/en/actions).
GitHub Actions offers hosted containers (called _runners_) that run many kind of scripts divided in flows and tasks, in an imperative fashion.

Many script reuse techniques are possible. The team employed:

- Custom Actions
- Reusable Workflows
- Repository templating

### Principles adopted
These are the principles adopted for every repository:

- Commits should adhere at the _Imperative tense, impersonal_ form.
- Versions are managed with git tags, specifically in the form `v#.#.#`.
- At each push, software build and tests are performed.
- When a new tag is pushed, after a successful test job, a release is performed.
- Releases happen on the GitHub Release page and trigger a deploy.
- Azure Cloud was the deploy destination for each software components, so that part of the workflow is shared between similar projects.
- Consistency between .NET compilation target, runtime target, build-time dotnet version.

### GitHub Actions Workflows
Workflows define the automation processes of a repository. An example of a workflow that was created by the team and was used in this project can be found [here](https://github.com/e-scooter-2077/azure-function-template/blob/master/.github/workflows/ci.yml).

Workflows are composed by jobs which can run concurrently or in sequence based on a graph of dependencies between each other. Each job is composed by a sequence of steps to be made. Steps can fail. When it happens, normally the job is interrupted and flagged as failed. Steps can run scripts or actions.

Every action takes care of one build step. Some custom actions aggregate repeated steps into a single one (see [Composite Actions](https://docs.github.com/en/actions/creating-actions/creating-a-composite-action)).

??? info "Actions the team developed in first person"

    Some members of the team had previously developed custom GitHub Actions for their projects and reused them for EScooter.
    These are:

    * [Markdown Docs](https://github.com/ldeluigi/markdown-docs) - Generates a website from markdown files.
    * [SemVer Checkout](https://github.com/EasyDesk/action-semver-checkout) - Checkouts the full history of a repo to determine the software version with semantic versioning.
    * [SemVer Release](https://github.com/EasyDesk/action-semver-release) - Creates a GitHub release of the software artifacts using its semantic version, generating a changelog.
    * [Dotnet Build](https://github.com/EasyDesk/action-dotnet-build) - Runs dotnet restore & dotnet build.
    * [Dotnet Test](https://github.com/EasyDesk/action-dotnet-test) - Runs the tests with dotnet.
    * [Dotnet Publish](https://github.com/EasyDesk/action-dotnet-publish) - Produces a folder with the final `.exe` file and all its dependencies.

#### Reusable workflows
After the release of [reusable workflows](https://docs.github.com/en/actions/learn-github-actions/reusing-workflows) by GitHub, DRY principles were applied extensively on CI/CD code too. Two unique workflow file were created so that each piece of software implemented with .NET would reference them instead of repeating the same code and configuration. One was created for [Azure Functions CI/CD](https://github.com/e-scooter-2077/azure-functions-ci) to include delivery on the cloud resource, one for [Dekstop applications and Hosted Runners CI/CD](https://github.com/e-scooter-2077/csharp-app-ci).

#### Workflow Design
The two main workflows the team designed are:

- [Azure Functions CI](https://github.com/e-scooter-2077/azure-functions-ci/blob/master/.github/workflows/azure-functions-ci.yml)
- [C# App CI](https://github.com/e-scooter-2077/csharp-app-ci)

They both start with a `build` job (Continuous Integration) consisting of checkout, building (compiling + linking),
unit testing, publishing (artifact generation) and artifact uploading (artifacts on GitHub aren't volatile, they are kept for about a day based on the configuration).

The `release` (Continuous Delivery) job is run only if a new tag respecting Semantic Versioning gets pushed. This job creates a GitHub release and uploads the binaries.

The Azure Functions CI provides also the `deploy` job (Continuous Deployment), which uploads the function to the relative slot on Azure Cloud.

The `analyze` job, present in both the workflows, builds the sources with CodeQL and runs all the default code quality and security queries, provided by the CodeQL team, reporting results in the Security tab of the repository. The outcome of the analysis is ignored for the purposes of build success, because CodeQL proved to be slightly unstable and too computational expensive (time and memory) to be waited every time.

??? info "Other reusable workflows adopted"
    Microservices CI/CD uses the following workflows:

    - [Microservice Build](https://github.com/EasyDesk/microservice-cicd)
    - [Microservice Azure Deploy](https://github.com/EasyDesk/microservice-azure-deploy)

    These workflows were developed by some members of the team for a different project, and reused within the Rent service repository.

### Repository templates
Repository templates allow a faster bootstrap phase of a project, scaffolding a _hello world_ version of the program to be developed. This includes the automatic generation of the build lifecycle from the very first commit.

The template used for every EScooter **Azure Function** can be found [here](https://github.com/e-scooter-2077/azure-function-template).

??? info "Microservice template"

    A template used for microservices but made for a different project can be found [here](https://github.com/EasyDesk/easydesk-microservice-template).
    It makes use of the [Clean Architecture](https://github.com/EasyDesk/easydesk-clean-architecture) library developed by some members of the team for every microservice based on the Clean Architecture and .NET.


### Other tools
A script was created for the _Git Bash_ that adds a `git release` command. The purpose is to quickly create the new patch, new minor or new major version of some software based on the repository history and semantic versioning. The code can be found [here](https://github.com/francescodente/git-release).


## Quality control
Every C# project was configured with a consistent style rulesheet inside the `.editorconfig` file. [StyleCop Analyzers](https://github.com/DotNetAnalyzers/StyleCopAnalyzers) takes care of enforncing the style designed both during development and during automated builds.

!!! check
    Style warnings are treated as errors.

### Static Code Analysis
Static code analysis for the C# language is performed automatically within the CI pipeline for every Azure function project. Reports are put under the Security tab of the relative GitHub project. The tool used for the analysis is [CodeQL](https://codeql.github.com/). Both **security** and **code quality** alerts are enabled.

!!! info
    CodeQL security and code quality warnings and errors are ignored by the workflows and are simply treated as suggestions. The reason is the instability of the CodeQL suite (bot the software and the default ruleset) which isn't always updated/fixed in time.

## Documentation Tools
Automated documentation generation is provided of both the overall design of the system and the API of every microservice. Microservices use a Swagger plugin for ASP.NET (see the [Rent Service API docs](https://escooter-rent-service.azurewebsites.net/swagger/index.html) as an example).

## Ignored aspects

### Public repository (NuGet)
Given the fact that the EScooter project didn't include libraries, but only single-purpose services and functions, NuGet wasn't used.

### Dependency inspection/automatic management
The team voluntarily ignored any real-time dependency and vulnerability management because of the finite scope of the project. GitHub Dependabot creates automatic pull requests that suggest dependency updates. The team wanted to avoid receiving those PRs forever.

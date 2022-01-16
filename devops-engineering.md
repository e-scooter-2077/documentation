# DevOps Engineering
DevOps is considered a fundamental part of a microservice based system. In this section, we will discuss the tools and methodologies put in practice by the team.

## DVCS Strategy
Having a large set of repositories, each typically maintained by a single developer (or at most two using pair-programming), the chosen branching strategy is **Trunk-based**. This simplifies the management of the repositories by individual developers, while still giving them the possibility to occasionally open feature/hotfix branches if needed.

## Licenses
Each piece of software made is provided under the [MIT License](https://en.wikipedia.org/wiki/MIT_License), which makes it [FOSS](https://en.wikipedia.org/wiki/Free_and_open-source_software).

## Versioning
Each of the softwares developed is versioned with the Semantic Versioning system. We used git annotated tags to declare new versions, and based our release system on that.
We created a [tool](https://github.com/EasyDesk/action-semver-checkout/blob/master/git-compute-version.sh) that searches for the latest tag and generates a version for our build automation.

## Software Build Lifecycle
Every step of the software build lifecycle was automated through Git and [GitHub Actions](https://docs.github.com/en/actions).
GitHub Actions offers hosted containers (called _runners_) that run many kind of scripts divided in flows and tasks, in an imperative fashion.

Many script reuse techniques are possible. We used:

- Custom Actions
- Repository templating

### Principles adopted
These are the principles we adopted for every repository:

- Commits should adhere at the _Imperative tense, impersonal_ form.
- Versions are managed with git tags, specifically in the form `v#.#.#`.
- At each push, software build and tests are performed.
- When a new tag is pushed, after a successful test job, a release is performed.
- Releases happen on the GitHub Release page and trigger a deploy.
- We used Azure Cloud as our deploy destination for each software components, so that part of the workflow is shared between similar projects.
- Consistency between .NET compilation target, runtime target, build-time dotnet version.

### GitHub Actions Workflows
Workflows define the automation processes of a repository. An example of a workflow we created and used can be found [here](https://github.com/e-scooter-2077/azure-function-template/blob/master/.github/workflows/ci.yml).

Workflows are composed by jobs which can run concurrently or in sequence based on a graph of dependencies between each other. Each job is composed by a sequence of steps to be made. Steps can fail, when it happens normally the job is interrupted and flagged as failed. Steps can run scripts or actions.

Every action takes care of one build step. Some custom actions aggregate repeated steps into a single one (see [Composite Actions](https://docs.github.com/en/actions/creating-actions/creating-a-composite-action)).

??? info "Actions we developed in first person"

    Some members of the team had previously developed custom GitHub Actions for their projects and reused them for EScooter.
    These are:

    * [Markdown Docs](https://github.com/ldeluigi/markdown-docs) - Generates a website from markdown files.
    * [SemVer Checkout](https://github.com/EasyDesk/action-semver-checkout) - Checkouts the full history of a repo to determine the software version with semantic versioning.
    * [SemVer Release](https://github.com/EasyDesk/action-semver-release) - Creates a GitHub release of the software artifacts using its semantic version, generating a changelog.
    * [Dotnet build](https://github.com/EasyDesk/action-dotnet-build) - Runs dotnet restore & dotnet build.
    * [Microservice Build Job](https://github.com/EasyDesk/job-microservice-build) - Runs multiple actions to build a microservice for test and release.
    * [Publish Webapp on Azure Job](https://github.com/EasyDesk/job-azure-publish-webapp) - Deploys a microservice/webapp application on Azure Cloud.
    * [Dotnet Test](https://github.com/EasyDesk/action-dotnet-test) - Runs the tests with dotnet.

#### Reusable workflows
After the release of [reusable workflows](https://docs.github.com/en/actions/learn-github-actions/reusing-workflows) by GitHub we decided to apply DRY principles extensively between all the repositories, so we created two singleton workflow file that each piece of software implemented with .NET would call. One for [Azure Functions CI/CD](https://github.com/e-scooter-2077/azure-functions-ci), one for [Dekstop applications and Hosted Runners CI/CD](https://github.com/e-scooter-2077/csharp-app-ci).

#### Workflow Design
The two main workflows the team designed are:

- [Azure Functions CI](https://github.com/e-scooter-2077/azure-functions-ci/blob/master/.github/workflows/azure-functions-ci.yml)
- [C# App CI](https://github.com/e-scooter-2077/csharp-app-ci)

They both start with a `build` job (Continuous Integration) consisting of checkout, building (compiling + linking),
unit testing, publishing (artifact generation) and artifact uploading (artifacts on GitHub aren't volatile, they are kept for about a day based on the configuration).

The `release` (Continuous Delivery) job is run only if a new tag respecting Semantic Versioning gets pushed. This job creates a GitHub release and uploads the binaries.

The Azure Functions CI provides also the `deploy` job (Continuous Deployment), which uploads the function to the relative slot on Azure Cloud.

The `analyze` job, present in both the workflows, builds the sources with CodeQL and runs all the default code quality and security queries, provided by the CodeQL team, reporting results in the Security tab of the repository. The outcome of the analysis is ignored for the purposes of build success, because CodeQL proved to be slightly unstable and too computational expensive (time and memory) to be waited every time.

### Repository templates
Repository templates allow a faster bootstrap phase of a project, scaffolding a hello world version of the program to be developed. This includes the automatic generation of the build lifecycle from the very first commit.

The template we made for every EScooter **Azure Function** can be found [here](https://github.com/e-scooter-2077/azure-function-template).

??? info "Microservice template"

    A template we used for microservices but we made for a different project can be found [here](https://github.com/EasyDesk/easydesk-microservice-template).
    It makes use of the [Clean Architecture](https://github.com/EasyDesk/easydesk-clean-architecture) library we made.


### Other tools
We created a script for the Git Bash that adds a `git release` command to git to quickly create new patch, new minor or new major versions of our software based on the repository history. The code can be found [here](https://github.com/francescodente/git-release).


## Quality control
We configured each C# project with a consistent style rulesheet inside the `.editorconfig` file. [StyleCop Analyzers](https://github.com/DotNetAnalyzers/StyleCopAnalyzers) takes care of enforncing the style we designed both during development and during automated builds.

!!! check
    Style warnings are treated as errors.

### Static Code Analysis
Static code analysis for the C# language is performed automatically within the CI pipeline for every azure function project. Reports are put under the Security tab of the relative GitHub project. The tool used for the analysis is [CodeQL](https://codeql.github.com/). Both security and code quality alerts are enabled.

## Documentation Tools
We automated documentation generation of both the architecture of the system (in this repository) and the API of our microservices with a Swagger plugin for ASP.NET (see the [Rent Service API docs](https://escooter-rent-service.azurewebsites.net/swagger/index.html) as an example).

## Ignored aspects

### Public repository (NuGet)
Given the fact that we didn't develop libraries, but only single-purpose services and functions, we didn't use NuGet.

### Dependency inspection/automatic management
We voluntarily ignored the real-time dependency and vulnerability management because of the finite scope of the project. We instead used automatic dependancy and vulnerability management offered by GitHub (such as Dependabot) in some previously made actions, such as [Markdown Docs](https://github.com/ldeluigi/markdown-docs), which Luca Deluigi is actively maintaining as one of his OS projects. See the [dependabot configuration file](https://github.com/ldeluigi/markdown-docs/blob/master/.github/dependabot.yml). Dependabot creates automatic pull requests that suggest dependency updates. We wanted to avoid receiving those PRs forever.

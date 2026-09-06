# Hello ASP.NET Developer Guide

## Table of Contents

- [Project Setup](#project-setup)
- [(TS01) Retrieve Greeting Count via GET Request](#retrieve-greeting-count-via-get-request-ts01)
- [(TS02) Create Greeting via POST Request](#create-greeting-via-post-request-ts02)
- [Prepare the First Release](#prepare-the-first-release)
- [Release](#release)
- [Document the Project](#document-the-project)
- [Testing (optional, explore on your own)](#testing-optional-explore-on-your-own)
- [Appendix](#appendix)
  - [Continuing on another computer](#continuing-on-another-computer)
  - [Signing in to GitHub with a token](#signing-in-to-github-with-a-token)
  - [Backing up unfinished work](#backing-up-unfinished-work)
  - [Feature Finish and pull requests](#feature-finish-and-pull-requests)
  - [Shipping a fix after a release (hotfix)](#shipping-a-fix-after-a-release-hotfix)
  - [Removing a stray .git folder](#removing-a-stray-git-folder)
  - [Creating the repo without the GitHub CLI](#creating-the-repo-without-the-github-cli)
  - [If the class diagram doesn't render](#if-the-class-diagram-doesnt-render)
  - [Free JetBrains license for students](#free-jetbrains-license-for-students)

## Project Setup

1. **Open Rider and create a new solution.**
   - Solution already open: `File` → `New Solution...`
   - On the Welcome screen (no solution open yet): click **New Solution**, or `File` → `New Solution...` if that screen shows a `File` menu

   In the wizard's sidebar, pick **Web**, then fill in:
   - Solution name: `hello-asp-net-developer`
   - Project name: `Acme.Hello.Platform`
   - Solution directory: any local path you prefer
   - Target framework: `net10.0`
   - Language: `C#`
   - Template: `Web API`
   - **Advanced Settings** (collapsed by default, click to expand):
     - Make sure **"Disable OpenAPI"** is unchecked: this project uses .NET's native OpenAPI document generation, wired up by the wizard itself
     - Uncheck **"Use controllers"** and check **"Use minimal APIs"** (the two aren't mutually exclusive in the dialog, but this project is Minimal APIs only, no MVC controllers anywhere)
     - Make sure **"Don't use top-level statements"** is unchecked: unchecked means top-level statements ARE used, `Program.cs` is just the statements themselves, no `class Program { static void Main(...) }` wrapper to write by hand
   - Click **Create**

   **Note:** every IDE action in this guide comes with a menu path and a keyboard shortcut, and the shortcuts are the ones from Rider's **IntelliJ** keymap. Rider ships with a Visual Studio keymap by default, so switch it once: open `Settings` (`Rider → Settings` on macOS, `File → Settings` on Windows) → `Keymap` and pick **`IntelliJ`** from the dropdown at the top (on a Mac it may read `IntelliJ (macOS)`). If a shortcut ever does something unexpected, use the menu path instead.

2. **Add Scalar and set the project properties.** Open Rider's **Terminal** tool window (bottom toolbar); it opens at the solution root by default. Run:

   ```
   dotnet add Acme.Hello.Platform package Scalar.AspNetCore --version 2.16.20
   ```

   This adds the `<PackageReference>` to `Acme.Hello.Platform.csproj` for you, no manual XML to paste. The wizard's `Microsoft.AspNetCore.OpenApi` package already generates the OpenAPI document itself, so this is the only NuGet dependency this project needs to add by hand: Scalar renders that document as an interactive page.

   Now switch Solution Explorer to **File System** view (the dropdown at the top of the panel; **Solution** view, the default, doesn't show the `.csproj`). Open `Acme.Hello.Platform.csproj` and add two more properties inside the existing `<PropertyGroup>`:

   ```xml
   <LangVersion>14</LangVersion>
   <Version>0.1.0-preview</Version>
   ```

   `LangVersion` is set explicitly because this project uses C# 14 features, the `field` keyword and pattern matching among them.

   **Note:** the dropdown at the top of Solution Explorer switches between two layouts:
   - **File System** view mirrors the folders on disk. Use it to add plain files: anything under `docs/`, `.gitignore`, `README.md`, `LICENSE.md`, `CHANGELOG.md`, and to edit the `.csproj` directly.
   - **Solution** view shows the logical project. Use it to add C# classes/interfaces/records.

   Each step below says which view it needs.

   **Note:** a `-preview` (or `-alpha` / `-beta` / `-rc`) suffix is NuGet's way of marking a version "prerelease", the closest .NET equivalent to Maven's `-SNAPSHOT`. Starting below `1.0.0` signals early development: the code's structure and behavior can still change freely from one version to the next. `## Release` later walks through dropping the suffix and jumping to `1.0.0`.

3. **Point the browser launch at Scalar.** Still in **File System** view, open `Properties/launchSettings.json`. `launchBrowser` is what makes `dotnet run` (or Rider's own Run button) open a browser automatically once the app starts; `launchUrl` is the page it opens, relative to that profile's own `applicationUrl`. Neither profile the wizard generates opens the right page: set `launchBrowser` to `true` in both, and point `launchUrl` at Scalar in both. `<port>` below is a placeholder: use the port number already in that profile's own `applicationUrl`, right above it in the file, never type `<port>` literally.

   In the `http` profile, change `launchBrowser` to `true` and change `launchUrl`:

   ```json
   "launchBrowser": true,
   "launchUrl": "http://localhost:<port>/scalar/v1"
   ```

   In the `https` profile, change `launchBrowser` to `true` and add `launchUrl`. This profile's `applicationUrl` lists two URLs separated by `;` (the app listens on both); use the `https` one, the first, not the `http` fallback after the semicolon:

   ```json
   "launchBrowser": true,
   "launchUrl": "https://localhost:<port>/scalar/v1"
   ```

   **Note:** don't paste a `launchSettings.json` from anywhere else, including this guide. The wizard assigns each project its own local ports (`applicationUrl`); overwriting the whole file would replace yours with someone else's.

4. **Open `Program.cs` and delete the wizard's sample.** It already exists, generated with top-level statements and a sample `/weatherforecast` endpoint plus a `WeatherForecast` record. Delete both the endpoint mapping and the record; keep `WebApplication.CreateBuilder(args)` / `builder.Build()` / `app.Run()`, nothing to retype there.

5. **Wire up Scalar in `Program.cs`.** Leaving **"Disable OpenAPI"** unchecked back in step 1 already got the wizard to write `builder.Services.AddOpenApi();` near the top, and further down, `if (app.Environment.IsDevelopment()) { app.MapOpenApi(); }`. Keep both exactly as generated, they produce the OpenAPI document itself; Scalar (added as a package in step 2) still needs to be told to render it. Add a `using` at the top of the file:

   <details>
   <summary>Program.cs (addition)</summary>

   ```csharp
   using Scalar.AspNetCore;
   ```
   </details>

   And this, inside that same `if (app.Environment.IsDevelopment())` block, right after `app.MapOpenApi();`:

   <details>
   <summary>Program.cs (addition, inside the IsDevelopment block)</summary>

   ```csharp
   app.MapScalarApiReference(options =>
   {
       options.WithTitle("Hello ASP.NET Developer API")
              .WithTheme(ScalarTheme.DeepSpace)
              .WithDefaultHttpClient(ScalarTarget.CSharp, ScalarClient.HttpClient);
   });
   ```
   </details>

   **Note:** the bare `MapScalarApiReference()` (no options) works too, it renders the same page with Scalar's own defaults. The options here just set the page title, a dark theme, and which language the generated request examples default to.

   **Note:** without this line, `/scalar/v1` (the page `launchSettings.json` opens in step 3) 404s, the app never maps a route for it, even though the OpenAPI document at `/openapi/v1.json` works fine on its own.

6. **Clear the wizard's sample request from `Acme.Hello.Platform.http`.** It already exists, generated with a `/weatherforecast` request; delete it. This `.http` file is how you'll verify each endpoint by hand, in place of a `Main`-style test script.

7. **Create `docs/user-stories.md`.** Still in **File System** view: right-click the solution root → `Add` → `File` → type `docs/user-stories.md` → Enter.

   **Tip:** typing the `docs/` prefix creates that folder too.

   <details>
   <summary>docs/user-stories.md</summary>

   ```markdown
   # User Stories

   This document contains the technical stories for the `hello-asp-net-developer` REST API from the perspective of a developer interacting with it through HTTP requests.
   The stories are simplified to focus on basic scenarios by giving examples of the input and expected output.

   ## Requirement Traceability Matrix

   | User Story | Scenario                              | Implementation                                                     |
   |------------|-----------------------------------------|---------------------------------------------------------------------|
   | TS01       | Scenario 1: Retrieve Greeting Count     | `GreetingEndpoints` GET `/api/v1/greetings`, `IGreetingCounter`     |
   | TS01       | Scenario 2: Personalized vs Anonymous   | `IGreetingCounter` (`PersonalizedCount`, `AnonymousCount`)          |
   | TS02       | Scenario 1: Anonymous Greeting          | `DeveloperAssembler`, `GreetDeveloperAssembler`, `Developer`        |
   | TS02       | Scenario 2: Personalized Greeting       | `GreetingEndpoints` POST `/api/v1/greetings`, `DeveloperAssembler`  |
   | TS02       | Scenario 3: Whitespace Handling         | `PersonName`                                                        |
   | TS02       | Scenario 4: Invalid Name Length         | `GreetDeveloperRequest` (`[StringLength]`)                          |

   ## TS01: Retrieve Greeting Count via GET Request
   **As a developer**, I want to retrieve the total number of greetings that have been generated by the system, broken down by personalized and anonymous, so that I can track API usage.

   ### Acceptance Criteria
   - **Scenario 1: Retrieve Greeting Count**
       - **Given** the system has processed several greeting requests, personalized or anonymous,
       - **When** the developer requests the greeting count via GET,
       - **Then** the developer receives a response containing the total number of greetings generated.

   - **Scenario 2: Personalized vs Anonymous Breakdown**
       - **Given** the system has processed a mix of personalized and anonymous greeting requests,
       - **When** the developer requests the greeting count via GET,
       - **Then** the response also contains the personalized count and the anonymous count separately, and their sum equals the total.

   ## TS02: Create Greeting via POST Request
   **As a developer**, I want to create a greeting, with or without providing my name, so that I can generate a greeting and receive a unique identifier.

   ### Acceptance Criteria
   - **Scenario 1: Anonymous Greeting**
       - **Given** a developer has not provided a first name or a last name, or provided them blank,
       - **When** the developer submits a greeting creation request via POST,
       - **Then** the developer receives a creation confirmation with a unique identifier, the full name "Anonymous ASP.NET Developer", and the message "Welcome Anonymous ASP.NET Developer", and the anonymous greeting count increments.

   - **Scenario 2: Personalized Greeting**
       - **Given** a developer has provided a valid first name (up to 35 characters) and last name (up to 40 characters),
       - **When** the developer submits a greeting creation request via POST,
       - **Then** the developer receives a creation confirmation containing a unique identifier (UUID v7), the full name, and a personalized message, and the personalized greeting count increments.

   - **Scenario 3: Whitespace Handling**
       - **Given** a developer has provided a first name and last name with extra whitespace (e.g., " John " and " Doe "),
       - **When** the developer submits a greeting creation request via POST,
       - **Then** the developer receives a creation confirmation with the whitespace trimmed (e.g., full name "John Doe").

   - **Scenario 4: Invalid Name Length**
       - **Given** a developer has provided a first name exceeding 35 characters or a last name exceeding 40 characters,
       - **When** the developer submits a greeting creation request via POST,
       - **Then** the developer receives a validation error indicating the name is too long, and neither greeting count increments.
   ```
   </details>

8. **Look at the architecture before writing any code.**
   - Real projects rarely start from a blank slate: the course already sets DDD as part of the Definition of Done. What's ahead is learning to read a given architecture and implement it well.
   - Install the **plantuml4idea** plugin so the diagram renders: `Settings` → `Plugins` → `Marketplace` → search `plantuml4idea` → `Install` (macOS: `Rider → Settings`; Windows: `File → Settings`).
   - Still in **File System** view: right-click the solution root → `Add` → `File` → type `docs/class-diagram.puml` → Enter.

   `Profiles` is a single bounded context. `Developer` is its entity: it has identity, a `Guid`, and a `Name` (`PersonName`, a value object) that's never missing, every person has one. A developer who doesn't reveal theirs gets the well-known `PersonName.Anonymous` instead of a `null`, so `Developer.IsAnonymous` (comparing against that same well-known value) is what tells the two cases apart, not a null check. `IGreetingCounter` is a small domain service: the greeting total belongs to no single `Developer`, so it lives in its own service rather than as a field on the entity, modeled as an interface with a thread-safe implementation (`Internal`) so the endpoints depend on the abstraction. The REST layer (`Resources` / `Assemblers` / `GreetingEndpoints`) translates between the domain and the outside world.

   <details>
   <summary>docs/class-diagram.puml</summary>

   ```
   @startuml

   package "Acme.Hello.Platform.Profiles.Domain.Model.ValueObjects" {
     class PersonName <<readonly record struct>> {
       +string FirstName
       +string LastName
       +string FullName
       +{static} PersonName Anonymous
     }
   }

   package "Acme.Hello.Platform.Profiles.Domain.Model.Entities" {
     class Developer {
       +Guid Id
       +PersonName Name
       +bool IsAnonymous
       +string GetFullName()
     }
   }

   package "Acme.Hello.Platform.Profiles.Domain.Services" {
     interface IGreetingCounter {
       +int PersonalizedCount
       +int AnonymousCount
       +int TotalCount
       +void IncrementPersonalized()
       +void IncrementAnonymous()
     }
   }

   package "Acme.Hello.Platform.Profiles.Domain.Services.Internal" {
     class GreetingCounter {
       -int _personalizedCount
       -int _anonymousCount
       +int PersonalizedCount
       +int AnonymousCount
       +int TotalCount
       +void IncrementPersonalized()
       +void IncrementAnonymous()
     }
   }

   package "Acme.Hello.Platform.Profiles.Interfaces.Rest.Assemblers" {
     class DeveloperAssembler {
       +static Developer ToEntityFromRequest(GreetDeveloperRequest request)
     }
     class GreetDeveloperAssembler {
       +static GreetDeveloperResponse ToResponseFromEntity(Developer developer)
     }
   }

   package "Acme.Hello.Platform.Profiles.Interfaces.Rest.Resources" {
     class GreetDeveloperRequest {
       +string? FirstName
       +string? LastName
     }
     class GreetDeveloperResponse {
       +Guid Id
       +string FullName
       +string Message
     }
     class GetGreetingCountResponse <<record>> {
       +int GreetingCount
       +int PersonalizedCount
       +int AnonymousCount
     }
   }

   package "Acme.Hello.Platform.Profiles.Interfaces.Rest" {
     class GreetingEndpoints <<static>> {
       +static IEndpointRouteBuilder MapGreetingEndpoints(this IEndpointRouteBuilder app)
     }
   }

   Developer *-- "1" PersonName : contains
   GreetingCounter ..|> IGreetingCounter : implements

   DeveloperAssembler ..> GreetDeveloperRequest : uses
   DeveloperAssembler ..> Developer : creates
   GreetDeveloperAssembler ..> Developer : uses
   GreetDeveloperAssembler ..> GreetDeveloperResponse : creates

   GreetingEndpoints ..> IGreetingCounter : uses
   GreetingEndpoints ..> DeveloperAssembler : uses
   GreetingEndpoints ..> GreetDeveloperAssembler : uses
   GreetingEndpoints ..> GreetDeveloperRequest : uses
   GreetingEndpoints ..> GetGreetingCountResponse : creates

   @enduml
   ```
   </details>

   Rider shows a rendered preview beside the source. If it shows an error instead, see [Appendix: If the class diagram doesn't render](#if-the-class-diagram-doesnt-render).

9. **Set up `.gitignore`.** Still in **File System** view: right-click the solution root → `Add` → `File` → type `.gitignore` → Enter. If Rider already left one at the solution root, open that one and replace its contents instead.

   <details>
   <summary>.gitignore</summary>

   ```
   bin/
   obj/
   /packages/
   riderModule.iml
   /_ReSharper.Caches/
   .DS_Store
   .idea/
   *.user
   .vs/
   ```
   </details>

   **Note:**
   - `.idea/` and `.vs/` have to be ignored **before** the first commit: the IDE constantly rewrites files inside them (indexing, installing plugins), which would otherwise leave the working tree dirty every time you go to commit.
   - `.DS_Store` is macOS Finder metadata, `*.user` is per-developer Rider settings; neither belongs in shared history.

10. **Enable Git and make the first commit.** Open Rider's **Terminal** tool window (bottom toolbar); it opens at the solution root (`hello-asp-net-developer/`, the folder with the `.sln` file) by default.

    **Note:** check the terminal prompt is at the solution root, not inside `Acme.Hello.Platform/`, before running anything below. `git init` acts on the current folder, so from a subfolder the repo lands in the wrong place.

    ```
    git init -b main
    git config user.name "Your Name"
    git config user.email "your.email@example.com"
    git add .
    git commit -m "chore: initial commit."
    ```

    **Note:**
    - `-b main` names the first branch `main` explicitly: without it, the name depends on each machine's git configuration, and it needs to be `main` to match what **Git Flow Helper** expects when it runs `Init`.
    - `git config` without `--global` scopes the identity to just this repo.
    - Ran it from a subfolder by mistake? See [Appendix: Removing a stray .git folder](#removing-a-stray-git-folder).

11. **Connect to GitHub.**

    **Sign in first.**
    - Install the GitHub CLI once.
      - macOS:
        ```
        brew install gh
        ```
      - Windows:
        ```
        winget install --id GitHub.cli
        ```
        No `winget`? Grab the installer from [cli.github.com](https://cli.github.com/).
    - Check it installed:
      ```
      gh --version
      ```
      If you get "command not found", close and reopen the terminal (the installer only updates the PATH for new sessions).
    - On a shared machine, sign out whoever used it last:
      ```
      gh auth logout
      ```
    - Sign in:
      ```
      gh auth login
      ```
      Answer its four prompts:
      - *Where do you use GitHub?* → `GitHub.com`
      - *What is your preferred protocol for Git operations on this host?* → `HTTPS`
      - *Authenticate Git with your GitHub credentials?* → `Yes`
      - *How would you like to authenticate GitHub CLI?* → `Login with a web browser`

      It shows a one-time code (like `3155-2B43`) and waits at `Press Enter to open https://github.com/login/device...`. Copy the code, press Enter, paste it into the page that opens, click **Authorize**. Answering `Yes` to the third prompt also configures git, so a later `git push` won't ask for credentials.

    **Create your own GitHub organization first.** Everything below pushes to an organization that is yours, never the course's.
    - If you don't have one, go to [github.com/organizations/plan](https://github.com/organizations/plan), pick the **Free** plan, choose an account name.
    - That account name is your `<org>` in the command below: if the organization is `acme-labs`, the repo ends up at `github.com/acme-labs/hello-asp-net-developer`.

    **Create the private repo and push.** One command. Replace `<org>` with your organization's name.
    - Check the terminal is at the solution root first (the folder with the `.sln` file): `--source=.` acts on the current folder.
    - Run:

      ```
      gh repo create <org>/hello-asp-net-developer --private --source=. --remote=origin --push --description "An ASP.NET Core Minimal API illustrating Object-Oriented Programming and Domain-Driven Design through a greeting endpoint for developers, personalized or anonymous."
      ```

    - It creates the private repo in your org, adds it as `origin`, pushes `main`, and sets the About text (GitHub's own repo-level summary, separate from `README.md`).

    **Note:** no GitHub CLI? Create the repo on the website and push by hand: see [Appendix: Creating the repo without the GitHub CLI](#creating-the-repo-without-the-github-cli).

12. **Install the Git Flow Helper plugin.**
    - macOS: `Rider → Settings → Plugins → Marketplace → search "Git Flow Helper"` → `Install`
    - Windows: `File → Settings → Plugins → Marketplace → search "Git Flow Helper"` → `Install`

13. **Initialize Git Flow.** Git Flow Helper pushes through Rider's own GitHub connection, not the terminal's. Register **your** account there first, and make sure it's the only one.
    - Get your token, copy what it prints (this is the same token your terminal git already uses):

      ```
      gh auth token
      ```

    - Open `Settings` → `Version Control` → `GitHub` (macOS: `Rider → Settings`; Windows: `File → Settings`).
    - If any account is already listed (a shared machine may still have someone else's), select each one and click `−` to remove it. The list must be empty before you add yours.
    - Click `+` → `Log In with Token...` (not `Log In via GitHub...`, whose browser sign-in produces an OAuth token your organization blocks for third-party apps).
    - Paste the `gh auth token` value, click `Add Account`. Close `Settings`.
    - Click the **Git Flow Helper** widget in the status bar → `Init`.
    - The branch-prefix fields (`Main`, `Develop`, `Feature`, `Release`, `Hotfix`) are pre-filled with sensible defaults; click `OK`.

    This creates a `develop` branch from `main` and pushes it. From here on, `main` is only touched through a Release, never worked on directly.

    **Note:** the current branch name should show in the status bar (bottom-right, a branch icon followed by the name). If nothing shows there, right-click an empty area of the status bar → check `Git Branch` in the widget list.

---

## Retrieve Greeting Count via GET Request ([TS01](./user-stories.md))

1. **Start the feature.** Git Flow Helper widget → `Feature` → `Feature Start` → **Feature description** `retrieve-greeting-count-via-get` → `OK`. Creates and switches you to `feature/retrieve-greeting-count-via-get`.

2. **Create the `IGreetingCounter` interface.** Switch Solution Explorer to **Solution** view (this section adds C# types). Right-click `Acme.Hello.Platform` → `Add` → `Class/Interface` → type `Profiles/Domain/Services/IGreetingCounter` in the **Name** field, select `Interface` → Enter (the folders don't exist yet; typing the path creates them together with the type). It tracks the greetings the whole system has generated, and it belongs to no single `Developer`. Two counts, `PersonalizedCount` and `AnonymousCount`, plus `TotalCount`, their sum, computed rather than its own field. ADR-0006 in `## Document the Project` covers why two counts, not one.

   <details>
   <summary>IGreetingCounter.cs</summary>

   ```csharp
   namespace Acme.Hello.Platform.Profiles.Domain.Services;

   public interface IGreetingCounter
   {
       int PersonalizedCount { get; }
       int AnonymousCount { get; }
       int TotalCount { get; }
       void IncrementPersonalized();
       void IncrementAnonymous();
   }
   ```
   </details>

   **Note:** the greeting counts are application-wide state that isn't tied to any one entity, so they live in their own service, not as fields on `Developer`. ADR-0001 in `## Document the Project` covers the domain-service choice.

   <details>
   <summary>IGreetingCounter.cs (Full file with XML doc)</summary>

   ```csharp
   namespace Acme.Hello.Platform.Profiles.Domain.Services;

   /// <summary>
   /// Domain service interface for tracking and retrieving greeting metrics, personalized and
   /// anonymous counted separately.
   /// </summary>
   public interface IGreetingCounter
   {
       /// <summary>
       /// Gets the current number of personalized greetings made to any developer.
       /// </summary>
       int PersonalizedCount { get; }

       /// <summary>
       /// Gets the current number of anonymous greetings.
       /// </summary>
       int AnonymousCount { get; }

       /// <summary>
       /// Gets the total number of greetings, personalized plus anonymous.
       /// </summary>
       int TotalCount { get; }

       /// <summary>
       /// Increments the personalized greeting count in a thread-safe manner.
       /// </summary>
       void IncrementPersonalized();

       /// <summary>
       /// Increments the anonymous greeting count in a thread-safe manner.
       /// </summary>
       void IncrementAnonymous();
   }
   ```
   </details>

   ```
   git add .
   git commit -m "feat(greeting-counter): add greeting counter service interface."
   ```

3. **Create the `GreetingCounter` implementation.** Right-click `Acme.Hello.Platform` → `Add` → `Class/Interface` → type `Profiles/Domain/Services/Internal/GreetingCounter` → Enter (`Class` is selected by default). `Internal` here is a plain sub-namespace, a folder-level convention, not the C# `internal` access modifier: it signals "implementation detail, depend on `IGreetingCounter` instead". Two plain `int` fields, one per count, each read through `Volatile.Read`, written through `Interlocked.Increment`, not a `lock`: this counter is read and written from every request that hits `GreetingEndpoints`, and only atomic operations are safe under concurrent requests without paying for a full lock. `TotalCount` is computed, `PersonalizedCount + AnonymousCount`, never its own field, so it can never drift out of sync with the two it's made of.

   <details>
   <summary>GreetingCounter.cs</summary>

   ```csharp
   namespace Acme.Hello.Platform.Profiles.Domain.Services.Internal;

   public class GreetingCounter : IGreetingCounter
   {
       private int _personalizedCount;
       private int _anonymousCount;

       public int PersonalizedCount => Volatile.Read(ref _personalizedCount);

       public int AnonymousCount => Volatile.Read(ref _anonymousCount);

       public int TotalCount => PersonalizedCount + AnonymousCount;

       public void IncrementPersonalized() => Interlocked.Increment(ref _personalizedCount);

       public void IncrementAnonymous() => Interlocked.Increment(ref _anonymousCount);
   }
   ```
   </details>

   **Note:** `internal` in the namespace signals that callers reach this class only through the `IGreetingCounter` interface, never by referencing it directly. ADR-0002 in `## Document the Project` covers the thread-safety choice.

   <details>
   <summary>GreetingCounter.cs (Full file with XML doc)</summary>

   ```csharp
   namespace Acme.Hello.Platform.Profiles.Domain.Services.Internal;

   /// <summary>
   /// In-memory, thread-safe implementation of the <see cref="IGreetingCounter"/> service.
   /// </summary>
   public class GreetingCounter : IGreetingCounter
   {
       private int _personalizedCount;
       private int _anonymousCount;

       /// <inheritdoc />
       public int PersonalizedCount => Volatile.Read(ref _personalizedCount);

       /// <inheritdoc />
       public int AnonymousCount => Volatile.Read(ref _anonymousCount);

       /// <inheritdoc />
       public int TotalCount => PersonalizedCount + AnonymousCount;

       /// <inheritdoc />
       public void IncrementPersonalized() => Interlocked.Increment(ref _personalizedCount);

       /// <inheritdoc />
       public void IncrementAnonymous() => Interlocked.Increment(ref _anonymousCount);
   }
   ```
   </details>

   ```
   git add .
   git commit -m "feat(greeting-counter): add thread-safe greeting counter implementation."
   ```

4. **Create the `GetGreetingCountResponse` resource.** Right-click `Acme.Hello.Platform` → `Add` → `Class/Interface` → type `Profiles/Interfaces/Rest/Resources/GetGreetingCountResponse`, select `Record` → Enter. A `record(int GreetingCount, int PersonalizedCount, int AnonymousCount)`: the total, plus the breakdown that motivated splitting the counter in the first place.

   <details>
   <summary>GetGreetingCountResponse.cs</summary>

   ```csharp
   namespace Acme.Hello.Platform.Profiles.Interfaces.Rest.Resources;

   /// <summary>
   /// A record representing the response for a greeting count request.
   /// </summary>
   /// <param name="GreetingCount">The total number of greetings, personalized plus anonymous.</param>
   /// <param name="PersonalizedCount">The number of personalized greetings.</param>
   /// <param name="AnonymousCount">The number of anonymous greetings.</param>
   public record GetGreetingCountResponse(int GreetingCount, int PersonalizedCount, int AnonymousCount);
   ```
   </details>

   ```
   git add .
   git commit -m "feat(greetings): add get greeting count response resource."
   ```

5. **Create `GreetingEndpoints`, GET only for now.** Right-click `Acme.Hello.Platform` → `Add` → `Class/Interface` → type `Profiles/Interfaces/Rest/GreetingEndpoints` → Enter. A `static` extension method class, `MapGreetingEndpoints(this IEndpointRouteBuilder app)`: groups every `/api/v1/greetings` route in one place, instead of `Program.cs` growing a new `app.MapGet`/`app.MapPost` call inline for every feature. Resolve `IGreetingCounter` as a parameter (ASP.NET Core injects it from the container, the same idea as constructor injection, just for a delegate instead of a class), build the response from the counter's three read properties.

   <details>
   <summary>GreetingEndpoints.cs (so far)</summary>

   ```csharp
   using Acme.Hello.Platform.Profiles.Domain.Services;
   using Acme.Hello.Platform.Profiles.Interfaces.Rest.Resources;

   namespace Acme.Hello.Platform.Profiles.Interfaces.Rest;

   public static class GreetingEndpoints
   {
       public static IEndpointRouteBuilder MapGreetingEndpoints(this IEndpointRouteBuilder app)
       {
           var group = app.MapGroup("/api/v1/greetings")
               .WithTags("Greetings");

           group.MapGet("", (IGreetingCounter greetingCounter) =>
               {
                   var response = new GetGreetingCountResponse(greetingCounter.TotalCount, greetingCounter.PersonalizedCount, greetingCounter.AnonymousCount);
                   return Results.Ok(response);
               })
               .WithName("GetGreetingCount")
               .Produces<GetGreetingCountResponse>()
               .WithSummary("Retrieves the count of greetings made to any developer, broken down by personalized and anonymous.");

           return app;
       }
   }
   ```
   </details>

   ```
   git add .
   git commit -m "feat(greetings): add greeting endpoints with get count route."
   ```

6. **Register the counter and map the endpoints in `Program.cs`.** Register `IGreetingCounter`/`GreetingCounter` as a singleton (one shared instance for the whole app's lifetime, since the count is a single, app-wide total, not something to recreate per request), then call `app.MapGreetingEndpoints()`.

   <details>
   <summary>Program.cs (so far)</summary>

   ```csharp
   // Program.cs (so far)
   builder.Services.AddSingleton<IGreetingCounter, GreetingCounter>();

   // ...

   app.MapGreetingEndpoints();
   ```
   </details>

   ```
   git add .
   git commit -m "feat(main): register greeting counter and map greeting endpoints."
   ```

7. **Verify.** Run the app and fire the GET scenario from `Acme.Hello.Platform.http`. Nothing has incremented either counter yet, so the response should read `{"greetingCount": 0, "personalizedCount": 0, "anonymousCount": 0}`.

   <details>
   <summary>Acme.Hello.Platform.http (GET scenario)</summary>

   ```http
   @HostAddress = http://localhost:<port>

   ### GET - Greeting count
   GET {{HostAddress}}/api/v1/greetings
   Accept: application/json
   ```
   </details>

   **Note:** the wizard generates `Acme.Hello.Platform.http` with the variable named `Acme.Hello.Platform_HostAddress`, after the project's own name. Rename it to `HostAddress` (both the `@HostAddress = ...` declaration and every `{{HostAddress}}` reference below it): a dot in a variable name breaks Rider's HTTP Client, `{{Acme.Hello.Platform_HostAddress}}` fails with "Environment is not selected. Cannot resolve variable", since the client tries to parse the dots as property access.

   **Note:** `<port>` on the `@HostAddress` line is a placeholder. The wizard picks a random local port per project and already wrote yours into the file, so use that one; open `Properties/launchSettings.json` to see it. Wherever else this guide shows a literal `localhost` port, it's this guide's own project's, not yours.

   ```
   git add .
   git commit -m "test(greetings): add an http scenario for the get endpoint."
   ```

8. **Publish and finish the feature.** Git Flow Helper widget → `Feature` → `Feature Publish`, then → `Feature Finish` (`Integrate Immediately`, `Keep remote branch when finished` unchecked). Merges into `develop` and pushes it too.

---

## Create Greeting via POST Request ([TS02](./user-stories.md))

1. **Start the feature.** Git Flow Helper widget → `Feature` → `Feature Start` → **Feature description** `create-greeting-via-post` → `OK`. Creates and switches you to `feature/create-greeting-via-post`.

2. **Create the `Developer` entity, fields only for now.** Right-click `Acme.Hello.Platform` → `Add` → `Class/Interface` → type `Profiles/Domain/Model/Entities/Developer` → Enter. Two members: `Id` (`Guid`), the developer's identity, and `Name` (`PersonName`). Every person has a name, so it's never optional here either: a developer who doesn't reveal one still gets a real `PersonName`, the well-known one `PersonName` defines in the next step.

   <details>
   <summary>Developer.cs (fields only)</summary>

   ```csharp
   namespace Acme.Hello.Platform.Profiles.Domain.Model.Entities;

   public class Developer
   {
       public Guid Id { get; } = Guid.CreateVersion7();

       public PersonName Name { get; }
   }
   ```
   </details>

   **Tip:** `PersonName` shows red, it doesn't exist yet. That's expected: you create it in the next step. Leave the `using` out for now; the IDE adds it once the type exists, or `Option+Enter` (macOS) / `Alt+Enter` (Windows) on the red name.

   **Note:** the entity is the anchor of the model, so you read and build it first, then the value object it needs. `Id` is `Guid.CreateVersion7()`, a time-ordered UUID v7, unlike `Guid.NewGuid()`'s fully random v4; every developer gets one, named or anonymous. ADR-0004 in `## Document the Project` covers that choice.

   **Note:** no commit here. The file doesn't compile on its own yet (`PersonName` doesn't exist), so this skeleton goes in with the `PersonName` commit in the next step.

3. **Create the `PersonName` value object.** Right-click `Acme.Hello.Platform` → `Add` → `Class/Interface` → type `Profiles/Domain/Model/ValueObjects/PersonName`, select `Record Struct` → Enter. A **`readonly record struct`**, like every value object in this project: each name validates and trims itself in its own `init` accessor, and a blocked parameterless constructor stops `new PersonName()` from ever producing an unvalidated instance. `default(PersonName)` still exists at the CLR level (every struct has one) and stays just as unreachable; for "no name given", `PersonName` defines its own well-known value instead, `Anonymous`, a real, fully validated instance rather than a `null` or an unvalidated one.

   <details>
   <summary>PersonName.cs</summary>

   ```csharp
   namespace Acme.Hello.Platform.Profiles.Domain.Model.ValueObjects;

   public readonly record struct PersonName
   {
       public string FirstName
       {
           get => field ?? string.Empty;
           init
           {
               ArgumentException.ThrowIfNullOrWhiteSpace(value);
               field = value.Trim();
           }
       }

       public string LastName
       {
           get => field ?? string.Empty;
           init
           {
               ArgumentException.ThrowIfNullOrWhiteSpace(value);
               field = value.Trim();
           }
       }

       public PersonName() => throw new InvalidOperationException("PersonName must be initialized with a first and a last name.");

       public PersonName(string firstName, string lastName)
       {
           FirstName = firstName;
           LastName = lastName;
       }

       public string FullName => $"{FirstName} {LastName}".Trim();

       public static readonly PersonName Anonymous = new("Anonymous", "ASP.NET Developer");
   }
   ```
   </details>

   **Note:** a blank or missing name is not this type's problem to solve; `PersonName` only ever represents a name that *is* known, and only a valid one, `Anonymous` included, it's built through the same validating constructor as any other name. Whether a name was provided at all is decided one level up, in `DeveloperAssembler` (TS02 step 7): construct a `PersonName` from the request when there is one, reach for `PersonName.Anonymous` otherwise. That split is why the anonymous-greeting behavior (ADR-0005) never has to weaken this type's own invariant, and why `Developer.Name` is never optional either.

   <details>
   <summary>PersonName.cs (Full file with XML doc)</summary>

   ```csharp
   namespace Acme.Hello.Platform.Profiles.Domain.Model.ValueObjects;

   /// <summary>
   /// Represents a person's name as a value object in the domain model.
   /// Encapsulates first and last names with validation and trimming behavior.
   /// </summary>
   public readonly record struct PersonName
   {
       /// <summary>
       /// The first name, trimmed of whitespace.
       /// </summary>
       /// <exception cref="ArgumentException">Thrown when the first name is null or blank.</exception>
       public string FirstName
       {
           get => field ?? string.Empty;
           init
           {
               ArgumentException.ThrowIfNullOrWhiteSpace(value);
               field = value.Trim();
           }
       }

       /// <summary>
       /// The last name, trimmed of whitespace.
       /// </summary>
       /// <exception cref="ArgumentException">Thrown when the last name is null or blank.</exception>
       public string LastName
       {
           get => field ?? string.Empty;
           init
           {
               ArgumentException.ThrowIfNullOrWhiteSpace(value);
               field = value.Trim();
           }
       }

       /// <summary>
       /// Prevents parameterless construction of <see cref="PersonName"/>.
       /// </summary>
       /// <exception cref="InvalidOperationException">Always thrown because both names are required.</exception>
       public PersonName() => throw new InvalidOperationException("PersonName must be initialized with a first and a last name.");

       /// <summary>
       /// Initializes a new instance of PersonName with first and last names.
       /// </summary>
       /// <param name="firstName">The person's first name, it must not be null or blank.</param>
       /// <param name="lastName">The person's last name, it must not be null or blank.</param>
       /// <exception cref="ArgumentException">Thrown if either name is null or blank.</exception>
       public PersonName(string firstName, string lastName)
       {
           FirstName = firstName;
           LastName = lastName;
       }

       /// <summary>
       /// Returns the full name by concatenating first and last names with a space.
       /// </summary>
       public string FullName => $"{FirstName} {LastName}".Trim();

       /// <summary>
       /// The well-known name for a developer who did not provide one. Every person has a name,
       /// so a <see cref="Domain.Model.Entities.Developer"/> greeted without one still gets a real,
       /// valid <see cref="PersonName"/>, never a <c>null</c> one: they just didn't reveal theirs.
       /// </summary>
       public static readonly PersonName Anonymous = new("Anonymous", "ASP.NET Developer");
   }
   ```
   </details>

   ```
   git add .
   git commit -m "feat(person-name): add person name value object."
   ```

4. **Complete the `Developer` entity.** With `PersonName` in place, add two convenience constructors: one that takes a `PersonName` directly, one that takes two strings and delegates to `new PersonName(firstName, lastName)` (still validating, still throws on a blank name when called directly), and a parameterless one for an anonymous developer that delegates to `PersonName.Anonymous` instead of leaving `Name` unset. Add `IsAnonymous`, comparing `Name` against that same well-known value (a `record struct` compares by value, so `==` just works), and `GetFullName()`, which now always returns a real string, there's no `null` case left.

   <details>
   <summary>Developer.cs (with its constructors)</summary>

   ```csharp
   using Acme.Hello.Platform.Profiles.Domain.Model.ValueObjects;

   namespace Acme.Hello.Platform.Profiles.Domain.Model.Entities;

   public class Developer
   {
       public Guid Id { get; } = Guid.CreateVersion7();

       public PersonName Name { get; }

       public bool IsAnonymous => Name == PersonName.Anonymous;

       public Developer(PersonName name)
       {
           Name = name;
       }

       public Developer(string firstName, string lastName) : this(new PersonName(firstName, lastName))
       {
       }

       public Developer() : this(PersonName.Anonymous)
       {
       }

       public string GetFullName() => Name.FullName;
   }
   ```
   </details>

   **Note:** `Developer` never repeats a blank or length check, `PersonName` already owns those, when a `PersonName` is actually being constructed. ADR-0004 covers the UUID v7 choice; ADR-0005 covers why an anonymous `Developer` is a legitimate value here, not an error, and why it's modeled as a real `PersonName` (`Anonymous`) rather than a `null`.

   <details>
   <summary>Developer.cs (Full file with XML doc)</summary>

   ```csharp
   using Acme.Hello.Platform.Profiles.Domain.Model.ValueObjects;

   namespace Acme.Hello.Platform.Profiles.Domain.Model.Entities;

   /// <summary>
   /// Represents a Developer entity in the domain model, with an auto-generated ID and a name:
   /// a developer greeted without providing one gets the well-known <see cref="PersonName.Anonymous"/>,
   /// never a missing one.
   /// </summary>
   public class Developer
   {
       /// <summary>
       /// Gets the unique identifier for the developer, a time-ordered UUID v7. Every developer
       /// gets one, named or anonymous.
       /// </summary>
       public Guid Id { get; } = Guid.CreateVersion7();

       /// <summary>
       /// Gets the developer's person name value object. Always present: <see cref="PersonName.Anonymous"/>
       /// stands in when the developer didn't provide one.
       /// </summary>
       public PersonName Name { get; }

       /// <summary>
       /// Gets a value indicating whether this developer didn't reveal a name.
       /// </summary>
       public bool IsAnonymous => Name == PersonName.Anonymous;

       /// <summary>
       /// Initializes a new instance of the Developer class with a name.
       /// </summary>
       /// <param name="name">The developer's person name value object.</param>
       public Developer(PersonName name)
       {
           Name = name;
       }

       /// <summary>
       /// Initializes a new instance of the Developer class with first and last names.
       /// </summary>
       /// <param name="firstName">The developer's first name, it must not be null or blank.</param>
       /// <param name="lastName">The developer's last name, it must not be null or blank.</param>
       public Developer(string firstName, string lastName) : this(new PersonName(firstName, lastName))
       {
       }

       /// <summary>
       /// Initializes a new instance of the Developer class with no name provided: anonymous.
       /// </summary>
       public Developer() : this(PersonName.Anonymous)
       {
       }

       /// <summary>
       /// Returns the full name by delegating to the PersonName value object.
       /// </summary>
       public string GetFullName() => Name.FullName;
   }
   ```
   </details>

   ```
   git add .
   git commit -m "feat(developer): add developer entity with a person name value object that's never missing."
   ```

5. **Create the `GreetDeveloperRequest` resource.** Right-click `Acme.Hello.Platform` → `Add` → `Class/Interface` → type `Profiles/Interfaces/Rest/Resources/GreetDeveloperRequest`, select `Record` → Enter. `record(string? FirstName, string? LastName)`. Both are optional: no `[Required]` here, since a missing or blank name is a legitimate anonymous request, not an error. `[StringLength]` still applies, though, a name that is *present* but too long is still rejected.

   <details>
   <summary>GreetDeveloperRequest.cs</summary>

   ```csharp
   using System.ComponentModel.DataAnnotations;

   namespace Acme.Hello.Platform.Profiles.Interfaces.Rest.Resources;

   /// <summary>
   /// A record representing a request to greet a developer.
   /// Both names are optional: leaving them out, or blank, greets the developer anonymously.
   /// A name that is present but too long is still rejected.
   /// </summary>
   /// <param name="FirstName">The developer's first name, optional.</param>
   /// <param name="LastName">The developer's last name, optional.</param>
   public record GreetDeveloperRequest(
       [property: StringLength(35, ErrorMessage = "First name cannot exceed 35 characters")]
       string? FirstName,
       [property: StringLength(40, ErrorMessage = "Last name cannot exceed 40 characters")]
       string? LastName);
   ```
   </details>

   `[StringLength]` alone doesn't reject anything by itself, it's just metadata on the type until something reads it. In `Program.cs`, add `builder.Services.AddValidation()` next to the other `builder.Services.Add...` calls: this is what makes ASP.NET Core actually enforce every `DataAnnotations` attribute on a Minimal API parameter, rejecting an invalid request with a `400` before an endpoint's own body ever runs.

   <details>
   <summary>Program.cs (addition)</summary>

   ```csharp
   // Program.cs (addition)
   builder.Services.AddValidation();
   ```
   </details>

   ```
   git add .
   git commit -m "feat(greetings): add greet developer request resource with length validation."
   ```

6. **Create the `GreetDeveloperResponse` resource.** Right-click `Acme.Hello.Platform` → `Add` → `Class/Interface` → type `Profiles/Interfaces/Rest/Resources/GreetDeveloperResponse`, select `Record` → Enter. `record(Guid Id, string FullName, string Message)`. Nothing here is optional: `Id` is never null, every developer gets a real identity; `FullName` is never null either, `PersonName.Anonymous`'s `"Anonymous ASP.NET Developer"` is a real value, safe to show as-is for an anonymous greeting.

   <details>
   <summary>GreetDeveloperResponse.cs</summary>

   ```csharp
   namespace Acme.Hello.Platform.Profiles.Interfaces.Rest.Resources;

   /// <summary>
   /// A record representing the response for a greeting request.
   /// Contains the developer's ID, full name, and a personalized message.
   /// </summary>
   /// <param name="Id">The unique identifier of the developer, always present.</param>
   /// <param name="FullName">The developer's full name, always present: the well-known "Anonymous ASP.NET Developer" for an anonymous greeting.</param>
   /// <param name="Message">The greeting message.</param>
   public record GreetDeveloperResponse(Guid Id, string FullName, string Message);
   ```
   </details>

   ```
   git add .
   git commit -m "feat(greetings): add greet developer response resource."
   ```

7. **Create the `DeveloperAssembler`.** Right-click `Acme.Hello.Platform` → `Add` → `Class/Interface` → type `Profiles/Interfaces/Rest/Assemblers/DeveloperAssembler` → Enter. Static `ToEntityFromRequest(GreetDeveloperRequest)`. This is the one place that decides: both names present and non-blank → construct a `PersonName` (and it validates); anything else → an anonymous `Developer`. Never a validation failure at this point, `GreetDeveloperRequest`'s own `[StringLength]` already rejected a too-long name with a `400` before this runs.

   <details>
   <summary>DeveloperAssembler.cs</summary>

   ```csharp
   using Acme.Hello.Platform.Profiles.Domain.Model.Entities;
   using Acme.Hello.Platform.Profiles.Domain.Model.ValueObjects;
   using Acme.Hello.Platform.Profiles.Interfaces.Rest.Resources;

   namespace Acme.Hello.Platform.Profiles.Interfaces.Rest.Assemblers;

   /// <summary>
   /// Assembler class to convert a GreetDeveloperRequest into a Developer entity.
   /// </summary>
   public static class DeveloperAssembler
   {
       /// <summary>
       /// Converts a GreetDeveloperRequest into a Developer entity: named when both first and
       /// last name are present, anonymous otherwise. A too-long name never reaches this point,
       /// <see cref="GreetDeveloperRequest"/>'s own validation already rejected it with a 400.
       /// </summary>
       /// <param name="request">The request containing the first and last names.</param>
       /// <returns>A named <see cref="Developer"/> when both names are present, an anonymous one otherwise.</returns>
       public static Developer ToEntityFromRequest(GreetDeveloperRequest request) =>
           !string.IsNullOrWhiteSpace(request.FirstName) && !string.IsNullOrWhiteSpace(request.LastName)
               ? new Developer(new PersonName(request.FirstName, request.LastName))
               : new Developer();
   }
   ```
   </details>

   **Note:** this is the counterpart to `GreetDeveloperAssembler` (next step): one assembler per direction, request → entity here, entity → response there. Neither one duplicates the other's decision.

   ```
   git add .
   git commit -m "feat(greetings): add developer assembler."
   ```

8. **Create the `GreetDeveloperAssembler`.** Right-click `Acme.Hello.Platform` → `Add` → `Class/Interface` → type `Profiles/Interfaces/Rest/Assemblers/GreetDeveloperAssembler` → Enter. Static `ToResponseFromEntity(Developer)`. `Id` and `FullName` are always the developer's own, `developer.GetFullName()` never returns `null`, `PersonName.Anonymous` already reads fine as-is. Only the message text branches on `developer.IsAnonymous`.

   <details>
   <summary>GreetDeveloperAssembler.cs</summary>

   ```csharp
   using Acme.Hello.Platform.Profiles.Domain.Model.Entities;
   using Acme.Hello.Platform.Profiles.Interfaces.Rest.Resources;

   namespace Acme.Hello.Platform.Profiles.Interfaces.Rest.Assemblers;

   /// <summary>
   /// Assembler class to convert a Developer entity into a GreetDeveloperResponse.
   /// </summary>
   public static class GreetDeveloperAssembler
   {
       /// <summary>
       /// Converts a Developer entity into a GreetDeveloperResponse: a personalized greeting when
       /// the developer revealed a name, an anonymous one otherwise. Every developer keeps its own
       /// identifier and full name either way, <see cref="Developer.GetFullName"/> is never null;
       /// only the message text differs.
       /// </summary>
       /// <param name="developer">The developer entity to convert.</param>
       /// <returns>A GreetDeveloperResponse with the personalized or anonymous greeting details.</returns>
       public static GreetDeveloperResponse ToResponseFromEntity(Developer developer) =>
           new(developer.Id, developer.GetFullName(),
               developer.IsAnonymous
                   ? "Welcome Anonymous ASP.NET Developer"
                   : $"Congrats {developer.GetFullName()}! You are an ASP.NET Developer");
   }
   ```
   </details>

   ```
   git add .
   git commit -m "feat(greetings): add greet developer assembler."
   ```

9. **Add the POST endpoint to `GreetingEndpoints`.** Build the `Developer` through `DeveloperAssembler`, then increment the matching count, `IncrementAnonymous()` when `developer.IsAnonymous`, `IncrementPersonalized()` otherwise, before returning `201 Created`. `.ProducesValidationProblem()` documents the `400` that `AddValidation()` (step 5) can already produce for this route, no further wiring needed here.

   <details>
   <summary>GreetingEndpoints.cs (addition, add it inside `MapGreetingEndpoints`)</summary>

   ```csharp
   group.MapPost("", (GreetDeveloperRequest request, IGreetingCounter greetingCounter) =>
       {
           var developer = DeveloperAssembler.ToEntityFromRequest(request);
           if (developer.IsAnonymous)
           {
               greetingCounter.IncrementAnonymous();
           }
           else
           {
               greetingCounter.IncrementPersonalized();
           }
           var response = GreetDeveloperAssembler.ToResponseFromEntity(developer);
           return Results.Created($"/api/v1/greetings/{developer.Id}", response);
       })
       .WithName("CreateGreeting")
       .Produces<GreetDeveloperResponse>(StatusCodes.Status201Created)
       .ProducesValidationProblem()
       .WithSummary("Creates a greeting for a developer, personalized or anonymous.");
   ```
   </details>

   `Program.cs` has been built in fragments across both user stories; here's the whole file as it stands now, with every piece from TS01 and TS02 in place:

   <details>
   <summary>Program.cs (Full file)</summary>

   ```csharp
   using Acme.Hello.Platform.Profiles.Domain.Services;
   using Acme.Hello.Platform.Profiles.Domain.Services.Internal;
   using Acme.Hello.Platform.Profiles.Interfaces.Rest;
   using Scalar.AspNetCore;

   var builder = WebApplication.CreateBuilder(args);

   builder.Services.AddOpenApi();
   builder.Services.AddValidation();

   builder.Services.AddSingleton<IGreetingCounter, GreetingCounter>();

   var app = builder.Build();

   if (app.Environment.IsDevelopment())
   {
       app.MapOpenApi();
       app.MapScalarApiReference(options =>
       {
           options.WithTitle("Hello ASP.NET Developer API")
                  .WithTheme(ScalarTheme.DeepSpace)
                  .WithDefaultHttpClient(ScalarTarget.CSharp, ScalarClient.HttpClient);
       });
   }

   app.MapGreetingEndpoints();

   app.Run();
   ```
   </details>

   <details>
   <summary>GreetingEndpoints.cs (Full file with XML doc)</summary>

   ```csharp
   using Acme.Hello.Platform.Profiles.Domain.Services;
   using Acme.Hello.Platform.Profiles.Interfaces.Rest.Assemblers;
   using Acme.Hello.Platform.Profiles.Interfaces.Rest.Resources;

   namespace Acme.Hello.Platform.Profiles.Interfaces.Rest;

   /// <summary>
   /// Extension methods for mapping greeting REST endpoints.
   /// </summary>
   public static class GreetingEndpoints
   {
       /// <summary>
       /// Maps the greeting endpoints to the specified endpoint route builder.
       /// </summary>
       /// <param name="app">The endpoint route builder.</param>
       /// <returns>The endpoint route builder for chaining.</returns>
       public static IEndpointRouteBuilder MapGreetingEndpoints(this IEndpointRouteBuilder app)
       {
           var group = app.MapGroup("/api/v1/greetings")
               .WithTags("Greetings");

           group.MapGet("", (IGreetingCounter greetingCounter) =>
               {
                   var response = new GetGreetingCountResponse(greetingCounter.TotalCount, greetingCounter.PersonalizedCount, greetingCounter.AnonymousCount);
                   return Results.Ok(response);
               })
               .WithName("GetGreetingCount")
               .Produces<GetGreetingCountResponse>()
               .WithSummary("Retrieves the count of greetings made to any developer, broken down by personalized and anonymous.");

           group.MapPost("", (GreetDeveloperRequest request, IGreetingCounter greetingCounter) =>
               {
                   var developer = DeveloperAssembler.ToEntityFromRequest(request);
                   if (developer.IsAnonymous)
                   {
                       greetingCounter.IncrementAnonymous();
                   }
                   else
                   {
                       greetingCounter.IncrementPersonalized();
                   }
                   var response = GreetDeveloperAssembler.ToResponseFromEntity(developer);
                   return Results.Created($"/api/v1/greetings/{developer.Id}", response);
               })
               .WithName("CreateGreeting")
               .Produces<GreetDeveloperResponse>(StatusCodes.Status201Created)
               .ProducesValidationProblem()
               .WithSummary("Creates a greeting for a developer, personalized or anonymous.");

           return app;
       }
   }
   ```
   </details>

   ```
   git add .
   git commit -m "feat(greetings): add post endpoint with request validation."
   ```

10. **Verify.** Fire the POST scenarios from `Acme.Hello.Platform.http` (no names / valid names / extra whitespace / a name over the length limit), then a final GET: `personalizedCount` should be `2` (the valid-names and whitespace requests), `anonymousCount` should be `1`, and `greetingCount` their sum, `3` (the too-long one is rejected and never reaches either counter).

    <details>
    <summary>Acme.Hello.Platform.http (POST scenarios)</summary>

    ```http
    ### POST - Anonymous
    POST {{HostAddress}}/api/v1/greetings
    Content-Type: application/json

    {}

    ### POST - Personalized
    POST {{HostAddress}}/api/v1/greetings
    Content-Type: application/json

    { "firstName": "John", "lastName": "Doe" }

    ### POST - Whitespace
    POST {{HostAddress}}/api/v1/greetings
    Content-Type: application/json

    { "firstName": " John ", "lastName": " Doe " }

    ### POST - Name too long (rejected)
    POST {{HostAddress}}/api/v1/greetings
    Content-Type: application/json

    { "firstName": "A very long first name that exceeds the thirty five character limit", "lastName": "Doe" }

    ### GET - Greeting count (reflects the 3 successful POSTs above, anonymous included)
    GET {{HostAddress}}/api/v1/greetings
    Accept: application/json
    ```
    </details>

    ```
    git add .
    git commit -m "test(greetings): add http scenarios for the post endpoint."
    ```

11. **Publish and finish the feature.** Git Flow Helper widget → `Feature` → `Feature Publish`, then → `Feature Finish` (`Integrate Immediately`, `Keep remote branch when finished` unchecked). Merges into `develop` and pushes it too.

---

## Prepare the First Release

**All of this happens on `develop`:** `Feature Finish` leaves you there. These are the last steps before tagging `v1.0.0`: one end-to-end check, then the files a public repo needs.

1. **Run both endpoints end to end.** Start the app and, from `Acme.Hello.Platform.http`: GET the count (`0`), POST anonymously, POST a couple of valid greetings, POST an invalid one (rejected, the count doesn't move), GET the count again and confirm it grew by exactly the number of successful POSTs. Then check every scenario in `docs/user-stories.md` against what the app actually does. No automated test drives these HTTP scenarios end to end, so this manual run is the acceptance check.

2. **Add `LICENSE.md`.** Still in **File System** view: right-click the solution root → `Add` → `File` → type `LICENSE.md` → Enter. The README's badge links to it, so it goes in first.

   <details>
   <summary>LICENSE.md</summary>

   ```markdown
   # MIT License

   Copyright © 2026 Web Applications Developer Team

   Permission is hereby granted, free of charge, to any person getting a copy
   of this software and associated documentation files (the "Software"), to deal
   in the Software without restriction, including without limitation the rights
   to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
   copies of the Software, and to permit persons to whom the Software is
   furnished to do so, subject to the following conditions:

   The above copyright notice and this permission notice shall be included in all
   copies or significant portions of the Software.

   THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
   IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
   FITNESS FOR A PARTICULAR PURPOSE, AND NONINFRINGEMENT. IN NO EVENT SHALL THE
   AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES, OR OTHER
   LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT, OR OTHERWISE, ARISING FROM,
   OUT OF, OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
   SOFTWARE.
   ```
   </details>

   Same way, right-click the solution root → `Add` → `File` → type `README.md` → Enter:

   <details>
   <summary>README.md</summary>

   ````markdown
   # Hello ASP.NET Developer (`hello-asp-net-developer`)

   [![.NET](https://img.shields.io/badge/.NET-10-purple.svg)](https://dotnet.microsoft.com/)
   [![C#](https://img.shields.io/badge/C%23-14-blue.svg)](https://learn.microsoft.com/dotnet/csharp/)
   [![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](LICENSE.md)

   `hello-asp-net-developer` is a sample ASP.NET Core Minimal API illustrating Object-Oriented Programming and Domain-Driven Design in a single bounded context (`Profiles`): a greeting endpoint that works for both a named and an anonymous developer.

   ---

   ## Technical Stack & Modern Features

   - **Runtime & Framework**: .NET 10.0 (C# 14.0), ASP.NET Core Minimal APIs
   - **C# 14 & .NET 10 Features**:
     - **`field` Keyword**: property validation and null-safe fallback without an explicit private backing field (`PersonName`).
     - **Struct Parameterless Constructor Safety**: `PersonName`, a `readonly record struct`, throws `InvalidOperationException` from `new PersonName()`.
     - **UUIDv7 Identifiers**: time-ordered identifiers via `Guid.CreateVersion7()` (`Developer.Id`), even for an anonymous developer.
     - **Minimal API Validation**: `builder.Services.AddValidation()`, a .NET 10 built-in, no separate validation package.
     - **Modern Throw Helpers**: `ArgumentException.ThrowIfNullOrWhiteSpace`, not hand-written null checks.

   ---

   ## Solution Structure

   ```text
   hello-asp-net-developer/
   ├── Acme.Hello.Platform/                    # Main API project
   │   └── Profiles/                           # Profiles Bounded Context
   │       ├── Domain/
   │       │   ├── Model/
   │       │   │   ├── Entities/               # Developer
   │       │   │   └── ValueObjects/           # PersonName
   │       │   └── Services/                   # IGreetingCounter, GreetingCounter (Internal)
   │       └── Interfaces/Rest/
   │           ├── Assemblers/                 # DeveloperAssembler, GreetDeveloperAssembler
   │           ├── Resources/                  # GreetDeveloperRequest/Response, GetGreetingCountResponse
   │           └── GreetingEndpoints.cs        # Maps GET/POST /api/v1/greetings
   ├── docs/                                   # Architecture & requirements documentation
   │   ├── class-diagram.puml                  # PlantUML domain model class diagram
   │   └── user-stories.md                     # User stories (TS01-TS02) & Requirements Traceability Matrix
   ├── CHANGELOG.md                            # Project release notes & version history
   ├── LICENSE.md                              # Project license
   └── README.md                               # Project overview & guide
   ```

   ---

   ## Bounded Contexts & Domain Model

   ### `Acme.Hello.Platform.Profiles`
   - **`Developer`** (*Entity*): identity (`Guid`, UUID v7) plus a `PersonName` that's never missing; a developer who doesn't reveal one gets the well-known `PersonName.Anonymous` instead.
   - **`PersonName`** (*Value Object*): validated `readonly record struct`; even its well-known `Anonymous` placeholder is a fully validated instance, never a blank or unvalidated one.
   - **`IGreetingCounter`** (*Domain Service*): thread-safe, personalized and anonymous greetings tracked separately, never collapsed into a single total.

   ---

   ## Key Domain Rules & Design Invariants

   - **Anonymous is a value, not an absence**: `PersonName` always validates and throws when constructed, `Anonymous` included; "no name" is modeled as `Developer.Name` holding that well-known `PersonName`, never as `null`.
   - **Anonymous greetings are legitimate, not rejected**: a missing or blank name produces a `201` anonymous greeting; a name that is present but too long is still rejected with a `400`.
   - **Every developer has a real identity**: `Guid.CreateVersion7()` runs for every `Developer`, personalized or anonymous, never a nullable ID.
   - **Personalized and anonymous counts tracked separately**: `TotalCount` is always computed as their sum, never its own field, so it can't drift out of sync.
   - **Thread-safe counting**: `Interlocked`/`Volatile` on each backing field, not a `lock`.

   ---

   ## Project Documentation

   | Document | Description |
   | :--- | :--- |
   | [**User Stories & RTM**](docs/user-stories.md) | User stories (TS01-TS02) and Requirements Traceability Matrix. |
   | [**Class Diagram**](docs/class-diagram.puml) | PlantUML class diagram of the `Profiles` bounded context. |
   | [**Changelog**](CHANGELOG.md) | Version history and release notes. |
   | [**License**](LICENSE.md) | Project licensing information (MIT). |

   ---

   ## Getting Started

   ### Prerequisites
   - [.NET 10 SDK](https://dotnet.microsoft.com/download) (or later)

   ### Build the Solution
   ```bash
   dotnet build
   ```

   ### Run the Application
   ```bash
   dotnet run --project Acme.Hello.Platform
   ```

   ### Access the API
   Port `5195` below is this project's own; check `Properties/launchSettings.json` for yours if it's different.
   - Scalar UI: `http://localhost:5195/scalar/v1`
   - Or send requests directly, for example:
     ```bash
     curl -X POST http://localhost:5195/api/v1/greetings \
          -H "Content-Type: application/json" \
          -d '{"firstName": "John", "lastName": "Doe"}'
     ```
   ````
   </details>

   ```
   git add .
   git commit -m "docs: add license and readme."
   git push
   ```

---

## Release

**Still on `develop`, right where *Prepare the First Release* left off.** Both features are merged, plus the license and README. `main` shouldn't stay permanently behind `develop`: close the loop with a release.

1. **Start the release.** Git Flow Helper widget → `Release` → `Release Start` → **Version description** `v1.0.0` → `OK`. Creates and switches you to `release/v1.0.0`.

   **Note:** the release name carries a `v` prefix (`v1.0.0`), matching the git tag it becomes on finish. The `.csproj` `<Version>` stays plain (`1.0.0`), and so does the `CHANGELOG.md` heading (`## [1.0.0]`): NuGet and Keep a Changelog conventions don't use the prefix.

2. **Bump the version.** A release branch needs at least one commit of its own, or the merge into `develop` is a no-op. Still in **File System** view, open `Acme.Hello.Platform.csproj`, change `<Version>0.1.0-preview</Version>` to `<Version>1.0.0</Version>`.

   ```
   git add .
   git commit -m "chore(release): bump version to 1.0.0."
   ```

3. **Add `CHANGELOG.md`.** Right-click the solution root → `Add` → `File` → type `CHANGELOG.md` → Enter.

   <details>
   <summary>CHANGELOG.md</summary>

   ```markdown
   # Changelog

   All notable changes to this project will be documented in this file.

   The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/),
   and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

   ## [1.0.0] - 2026-09-04

   ### Added
   - TS01: Retrieve Greeting Count via GET Request.
   - TS02: Create Greeting via POST Request, personalized or anonymous.
   - `IGreetingCounter` domain service (thread-safe, `Interlocked`/`Volatile`-backed) tracking personalized and anonymous greeting counts separately, plus their total.
   - `Developer` entity with UUID v7 identifiers and a `PersonName` value object that's never missing (`PersonName.Anonymous` stands in when one isn't given).
   - Length validation on `GreetDeveloperRequest` (`[StringLength]`); a missing name is anonymous, not an error.
   - Requirement Traceability Matrix in `docs/user-stories.md`, Architecture Decision Records in `docs/adrs.md`.
   ```
   </details>

   ```
   git add .
   git commit -m "docs: add changelog for 1.0.0."
   ```

   **Note:** don't push here. This commit rides to the remote with `Release Publish` in the next step, together with the version bump; it's also what gives `Release Finish` a real commit to merge into `develop`.

   **Note:** the `## [version] - date` line uses the date you finish the release, `YYYY-MM-DD`.

4. **Publish and finish the release.**
   - Git Flow Helper widget → `Release` → `Release Publish` (pushes `release/v1.0.0` with both commits).
   - Git Flow Helper widget → `Release` → `Release Finish`.

   `Release Finish` merges `release/v1.0.0` into `main` (tagging it `v1.0.0`), merges it into `develop`, pushes both, and deletes the release branch. `main` and `develop` are back in sync.

5. **Publish the GitHub Release.**
   - On GitHub: **Releases** → **Draft a new release**.
   - Tag: pick the existing `v1.0.0` (do not create a new one).
   - Release title: `Version 1.0.0` (the title spells it out; the tag keeps the `v` prefix).
   - Description: the release notes below.
   - Click **Publish release**.

   <details>
   <summary>Release notes (1.0.0)</summary>

   ```markdown
   ## 🚀 Added

   - **TS01: Retrieve Greeting Count via GET Request**: `GET /api/v1/greetings` returns the number of greetings generated, broken down into personalized and anonymous.
   - **TS02: Create Greeting via POST Request**: `POST /api/v1/greetings` greets a developer, personalized when a first and last name are given, anonymous otherwise, each with a `201 Created` confirmation and its own UUID v7 identifier.
   - `IGreetingCounter` domain service: thread-safe via `Interlocked`/`Volatile`, personalized and anonymous greetings counted separately, total computed from the two.
   - `PersonName` value object (`readonly record struct`, always validated, blocked parameterless constructor) with a well-known `Anonymous` value, and the `Developer` entity built over it.
   - Length validation on `GreetDeveloperRequest` (`[StringLength]` plus `builder.Services.AddValidation()`): a too-long name is a `400`; a missing or blank name is an anonymous greeting, not an error.
   - OpenAPI document via `Microsoft.AspNetCore.OpenApi`, rendered by Scalar at `/scalar/v1`.
   - Project `README.md` and MIT license; `docs/user-stories.md` with a Requirement Traceability Matrix, `docs/class-diagram.puml`, `docs/adrs.md`; `CHANGELOG.md` to track version history going forward.
   ```
   </details>

   **Tip:** the same works from the command line: `gh release create v1.0.0 --title "Version 1.0.0" --notes-file CHANGELOG.md` (the [GitHub CLI](https://cli.github.com/), authenticated once via `gh auth login`). `--notes-file` takes any Markdown file; `CHANGELOG.md` works here since the tag already exists.

6. **Back on `develop`, pick the `-preview` suffix back up.**
   - Still in **File System** view, in `Acme.Hello.Platform.csproj`: `<Version>1.0.0</Version>` → `<Version>1.0.1-preview</Version>`, so `develop` doesn't sit on an already-tagged version.
   - Then:

   ```
   git add .
   git commit -m "chore(dev): set development version to 1.0.1-preview."
   git push
   ```

---

## Document the Project

**Still on `develop`, no Git Flow feature needed:** writing down decisions already made across both features, and shipping the record of them.

1. **Generate the XML documentation from the comments already in your code.** Still in **File System** view, add one property to `Acme.Hello.Platform.csproj`, in the same `<PropertyGroup>` as `<Version>`:

   ```xml
   <GenerateDocumentationFile>true</GenerateDocumentationFile>
   ```

   Then:

   ```
   dotnet build
   ```

   Open `Acme.Hello.Platform/bin/Debug/net10.0/Acme.Hello.Platform.xml`. Every `<summary>`/`<param>`/`<returns>`/`<exception>` comment written across both user stories turns into a real, structured XML file, exactly what IntelliSense reads to show tooltips.

   **Note:** if you ever see a `CS1591` warning ("missing XML comment for publicly visible member") on a type here, it means some public member is missing its own `<summary>`, a real, honest gap the flag surfaces, not a sign anything is broken. Every type in this project is fully documented, so the build stays clean.

2. **Add these six Architecture Decision Records (ADRs) to a single `docs/adrs.md` file.** Right-click the `docs` folder → `Add` → `File` → `adrs.md`. They document every decision made so far, across both user stories, in one sitting:
   - why the greeting count is a domain service, not a field on `Developer`
   - why the counter is thread-safe via `Interlocked`/`Volatile`, not a `lock`
   - why `PersonName` is a value object, and specifically a `readonly record struct`
   - why the identifier is a UUID v7
   - why a missing or blank name produces an anonymous greeting instead of a rejected request
   - why personalized and anonymous greetings are counted separately, not as one total

   **Note:** the standard ADR format is **Status**, **Context**, **Decision Drivers**, **Considered Options**, **Decision**, **Consequences**. Look it up if it's unfamiliar.

   <details>
   <summary>docs/adrs.md</summary>

   ````markdown
   # Architecture Decision Records

   # ADR-0001: Greeting Count as a Domain Service

   **Status:** Accepted

   ## Context

   The number of greetings the system has generated is application-wide state (see ADR-0006 for why it's tracked as two counts, personalized and anonymous, rather than one). It doesn't belong to any one `Developer`, and more than one `Developer` can exist without ever affecting it.

   ## Decision Drivers
   - The count's lifetime and scope (the whole app) don't match any single entity's lifetime and scope (one developer).
   - `GreetingEndpoints` needs to depend on an abstraction, not a concrete counting strategy, to keep the door open for a different implementation later (e.g. a persisted counter).

   ## Considered Options
   1. A domain service (`IGreetingCounter`), registered as a singleton *(Chosen)*
   2. A static field on `Developer`
   3. A field on `GreetingEndpoints` itself

   ## Decision

   `IGreetingCounter` is a small domain service interface in `Profiles.Domain.Services`, implemented by `GreetingCounter` in `Profiles.Domain.Services.Internal` and registered as a singleton in `Program.cs`. `GreetingEndpoints` takes it as a parameter; ASP.NET Core injects the registered instance.

   ## Consequences

   **Positive:**
   - `Developer` stays a pure representation of one developer, with no state that doesn't belong to it.
   - Swapping the counting strategy (e.g. persisting it) only touches `GreetingCounter`, never its callers.

   **Negative:**
   - One more type and one more registration, for what a static field would do in fewer lines, an intentional trade against the coupling a static field creates.

   ---

   # ADR-0002: Thread-Safe Counter via Interlocked and Volatile

   **Status:** Accepted

   ## Context

   `GreetingCounter` is read and written by every request that reaches `GreetingEndpoints`. ASP.NET Core handles requests concurrently, so more than one thread can call an `Increment*` method or read a count at the same time.

   ## Decision Drivers
   - A plain `int++` is not one operation, it's read-modify-write; two concurrent increments can both read the same value and both write back the same result, silently losing one.
   - A full `lock` around a single `int` is more machinery than the problem needs.

   ## Considered Options
   1. `Interlocked.Increment` / `Volatile.Read` on a plain `int` field, one per count *(Chosen)*
   2. `lock (_gate) { _count++; }`
   3. `System.Threading.Semaphore`

   ## Decision

   `_personalizedCount` and `_anonymousCount` are each a plain `int`, incremented with `Interlocked.Increment(ref _field)` and read with `Volatile.Read(ref _field)`. Both are single atomic CPU-level operations: no two increments can interleave, and a read can never observe a half-written value. `TotalCount` is computed as `PersonalizedCount + AnonymousCount`, never its own field: a third field updated alongside the other two would need its own synchronization to avoid drifting out of sync with them.

   ## Consequences

   **Positive:**
   - Correct under concurrent load, without the overhead of acquiring and releasing a lock on every request.

   **Negative:**
   - `Interlocked`/`Volatile` only protect this one `int`; a more complex piece of shared state would need a different tool (a `lock`, or an immutable snapshot swap).

   ---

   # ADR-0003: PersonName as a Value Object (readonly record struct)

   **Status:** Accepted

   ## Context

   A developer's name is two strings with no identity of their own, always used together, and with rules (non-blank, trimmed) that apply the same way everywhere a name shows up.

   ## Decision Drivers
   - Wrapping `firstName`/`lastName` in their own type closes a primitive-obsession gap: the rules live in one place, not duplicated across every constructor that needs a name.
   - This project's value objects are `readonly record struct`s throughout (no heap allocation, value semantics for free); the risk that shape carries, an unvalidated `default(PersonName)`, is closed off by a blocked parameterless constructor rather than ever exposing that default.

   ## Considered Options
   1. `readonly record struct PersonName`, validating in its `init` accessors, with a blocked parameterless constructor *(Chosen)*
   2. A `record` class `PersonName` (reference type, no `default` state to worry about, but heap-allocated)
   3. Two loose `string` fields directly on `Developer`

   ## Decision

   `PersonName` is a `readonly record struct` with a blocked parameterless constructor (`public PersonName() => throw new InvalidOperationException(...)`) and two validating `init` accessors (`ArgumentException.ThrowIfNullOrWhiteSpace`, then trim). `PersonName` itself is never asked to represent "no name" as an unvalidated or missing state; how a `Developer` without one is represented is ADR-0005's concern, not this type's.

   ## Consequences

   **Positive:**
   - Consistent with every other value object in this codebase (struct, not class).
   - `PersonName`'s own invariant ("always a real name") never has to bend to accommodate the anonymous case; that concern lives one level up.

   **Negative:**
   - Two constructors to remember (the blocked no-arg one exists purely to fail loudly), a small amount of ceremony compared to a `record` class.

   ---

   # ADR-0004: UUID v7 for Developer Identifiers

   **Status:** Accepted

   ## Context

   Every `Developer`, named or anonymous, needs a unique identifier. This is a different concern from whether the developer revealed a name (ADR-0005): `Id` identifies the greeting occurrence itself, a real, distinct business event every time, not the person behind it. Whether that person chose to be identified is `PersonName`'s concern, not `Id`'s.

   ## Decision Drivers
   - A time-ordered identifier sorts and indexes better than a fully random one, without leaking any real-world sequence information the way an auto-increment integer would.
   - Unlike `PersonName` (ADR-0005, `Anonymous` as a well-known Special Case value), `Id` cannot reuse that pattern for the anonymous case: identity only means something when it's unique per instance. A fixed placeholder `Id` shared by every anonymous greeting would make them indistinguishable from each other, the opposite of what an identifier is for. Leaving it `null` instead would be just as wrong on an Entity, whose defining trait *is* having identity.

   ## Considered Options
   1. `Guid.CreateVersion7()`, a time-ordered UUID v7, generated fresh for every `Developer` including anonymous ones *(Chosen)*
   2. `Guid.NewGuid()`, a fully random UUID v4
   3. `Guid? Id`, `null` for an anonymous developer, rejected: an Entity's `Id` is what makes it an Entity; a sometimes-absent one undermines that for no real benefit
   4. A fixed, well-known `Id` value for every anonymous developer (a Special Case, mirroring `PersonName.Anonymous`), rejected: it would collapse every distinct anonymous greeting into one shared, non-unique identity, the opposite of identity's purpose

   ## Decision

   `Developer.Id` is assigned with `Guid.CreateVersion7()` in every constructor, anonymous included, never `null` and never a shared placeholder: it identifies this specific greeting occurrence, independent of whether a name was given.

   ## Consequences

   **Positive:**
   - Lexicographically sortable by creation time; better index locality than a v4 GUID in anything backed by a B-tree.
   - Every greeting, personalized or anonymous, is its own distinct, referenceable business event, consistent with counting each one separately (ADR-0006).

   **Negative:**
   - Requires .NET 9+ (`Guid.CreateVersion7()` didn't exist before); not a concern here, this project targets `net10.0`.

   ---

   # ADR-0005: Anonymous Greetings Instead of Rejecting Missing Names

   **Status:** Accepted

   ## Context

   The first draft of TS02 rejected a request with a missing or blank name outright (`400`, matching the sibling `hello-spring-boot-developer` project's TS02). Revisiting the requirement: greeting an anonymous developer is a legitimate use case here, not invalid input, an anonymous request is not an error, it's simply a request that doesn't reveal a name.

   ## Decision Drivers
   - "No name provided" and "an invalid name provided" are different situations and deserve different responses: the first is a legitimate case (greet them anonymously), the second is still a real error (a name over the length limit is rejected).
   - Every person has a name; modeling "didn't reveal one" as `Developer.Name` being absent (`null`) would misstate that as "doesn't have one."
   - Whatever handles this should not weaken `PersonName`'s own invariant (it never represents an unvalidated name).
   - An anonymous greeting is a real, successfully processed request, not a no-op; it belongs in the greeting counts, not just in a response body no one is tracking.

   ## Considered Options
   1. Drop `[Required]` but keep `[StringLength]` on `GreetDeveloperRequest`; branch on presence of both names in `DeveloperAssembler`/`GreetDeveloperAssembler`, using a well-known `PersonName.Anonymous` value instead of a `null` `Developer.Name`; count every processed request *(Chosen)*
   2. Keep rejecting a missing name with `[Required]` (`400`), matching `hello-spring-boot-developer`
   3. Accept a missing name but only increment the counter for a personalized greeting
   4. Model "no name" as `Developer.Name` being `PersonName?` (nullable), rejected: a person always has a name, so a nullable `PersonName` on `Developer` reads as "some developers have no name" when what's actually optional is whether they revealed one, not whether they have one

   ## Decision

   `GreetDeveloperRequest` keeps `[StringLength]` (a too-long name is still rejected) but drops `[Required]` (a missing or blank name is not an error). `PersonName` defines its own well-known value for this case, `Anonymous` (`"Anonymous"`, `"ASP.NET Developer"`), a real, fully validated instance, not a sentinel that bends the type's own rules. `DeveloperAssembler.ToEntityFromRequest` constructs a `Developer` from a `PersonName` built from the request when both names are present and non-blank, or from `PersonName.Anonymous` otherwise (`new Developer()` reaches for it). `Developer.IsAnonymous` compares `Name` against that same well-known value. `GreetDeveloperAssembler.ToResponseFromEntity` always passes `developer.GetFullName()` through to the response, never `null`, it only branches on `developer.IsAnonymous` for the message text: `"Welcome Anonymous ASP.NET Developer"` or a personalized one. `GreetingEndpoints` increments the matching count, `IncrementAnonymous()` or `IncrementPersonalized()` (ADR-0006), based on that same flag: every request that reaches this point already passed validation and is a real, counted greeting, personalized or anonymous.

   ## Consequences

   **Positive:**
   - Every developer, anonymous or personalized, gets a real `Guid` identity in its response, no `Guid?` needed on the response type.
   - `PersonName` keeps its own invariant untouched, and `Developer.Name` is never optional either: an anonymous developer still holds a real, valid `PersonName`, just not one that came from the request. `GreetDeveloperResponse.FullName` follows the same rule, plain `string`, never `null`: the well-known `Anonymous` value is safe to show as-is, so the assembler needs no special case to hide it.

   **Negative:**
   - This project's TS02 now behaves differently from `hello-spring-boot-developer`'s TS02 for the same kind of input (missing name): a deliberate, documented divergence, not an oversight.
   - A real developer literally named "Anonymous ASP.NET Developer" would be indistinguishable from an anonymous one; accepted as a theoretical, not practical, risk for a project at this scale.

   ---

   # ADR-0006: Track Personalized and Anonymous Greetings Separately

   **Status:** Accepted

   ## Context

   ADR-0005 made an anonymous greeting a legitimate, counted request. Once both kinds of greeting count toward the same total, "how many were personalized versus how many were anonymous" becomes a real, distinct question a consumer of `GET /api/v1/greetings` would want answered, one a single total can't answer on its own.

   ## Decision Drivers
   - Personalized and anonymous traffic are different signals (developer adoption versus anonymous pings); collapsing them into one number throws that distinction away permanently.
   - Whatever tracks them shouldn't let the total drift out of sync with the two parts under concurrent updates.
   - A boolean parameter (`Increment(bool isAnonymous)`) says nothing at the call site about which branch is which; two named methods do.

   ## Considered Options
   1. `IGreetingCounter` exposes `PersonalizedCount`, `AnonymousCount`, and a computed `TotalCount`, with `IncrementPersonalized()` / `IncrementAnonymous()` *(Chosen)*
   2. A single `Increment(bool isAnonymous)` method and a single stored total, with the breakdown reconstructed elsewhere
   3. Two entirely separate domain services, one per kind of greeting

   ## Decision

   `IGreetingCounter` keeps its two atomic backing fields (ADR-0002) but exposes three read properties, `PersonalizedCount`, `AnonymousCount`, and `TotalCount` (the sum, never its own field), plus two increment methods named for what they count. `GreetingEndpoints`' POST handler calls the one matching `developer.IsAnonymous`; `GetGreetingCountResponse` returns all three numbers, `GreetingCount` still the total, so existing consumers reading just that field see no change.

   ## Consequences

   **Positive:**
   - The breakdown is available with no risk of drifting from the total, since the total is never stored, only computed.
   - `GetGreetingCountResponse` stays backward-compatible in spirit: `GreetingCount` keeps meaning the same thing it always did.

   **Negative:**
   - `IGreetingCounter` grew from two members to five; `GreetingEndpoints`' POST handler now branches once (`if (developer.IsAnonymous)`) instead of calling one unconditional `Increment()`.
   ````
   </details>

   ```
   git add .
   git commit -m "docs(adr): add architecture decision records."
   git push
   ```

3. **Update `README.md`** now that the ADRs exist. Replace the file from `## Prepare the First Release` with the version below: this adds `see ADR-NNNN` links throughout and an Architecture Decision Records row in `## Project Documentation`.

   <details>
   <summary>README.md</summary>

   ````markdown
   # Hello ASP.NET Developer (`hello-asp-net-developer`)

   [![.NET](https://img.shields.io/badge/.NET-10-purple.svg)](https://dotnet.microsoft.com/)
   [![C#](https://img.shields.io/badge/C%23-14-blue.svg)](https://learn.microsoft.com/dotnet/csharp/)
   [![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](LICENSE.md)

   `hello-asp-net-developer` is a sample ASP.NET Core Minimal API illustrating Object-Oriented Programming and Domain-Driven Design in a single bounded context (`Profiles`): a greeting endpoint that works for both a named and an anonymous developer.

   ---

   ## Technical Stack & Modern Features

   - **Runtime & Framework**: .NET 10.0 (C# 14.0), ASP.NET Core Minimal APIs
   - **C# 14 & .NET 10 Features**:
     - **`field` Keyword**: property validation and null-safe fallback without an explicit private backing field (`PersonName`).
     - **Struct Parameterless Constructor Safety**: `PersonName`, a `readonly record struct`, throws `InvalidOperationException` from `new PersonName()`.
     - **UUIDv7 Identifiers**: time-ordered identifiers via `Guid.CreateVersion7()` (`Developer.Id`), even for an anonymous developer.
     - **Minimal API Validation**: `builder.Services.AddValidation()`, a .NET 10 built-in, no separate validation package.
     - **Modern Throw Helpers**: `ArgumentException.ThrowIfNullOrWhiteSpace`, not hand-written null checks.

   ---

   ## Solution Structure

   ```text
   hello-asp-net-developer/
   ├── Acme.Hello.Platform/                    # Main API project
   │   └── Profiles/                           # Profiles Bounded Context
   │       ├── Domain/
   │       │   ├── Model/
   │       │   │   ├── Entities/               # Developer
   │       │   │   └── ValueObjects/           # PersonName
   │       │   └── Services/                   # IGreetingCounter, GreetingCounter (Internal)
   │       └── Interfaces/Rest/
   │           ├── Assemblers/                 # DeveloperAssembler, GreetDeveloperAssembler
   │           ├── Resources/                  # GreetDeveloperRequest/Response, GetGreetingCountResponse
   │           └── GreetingEndpoints.cs        # Maps GET/POST /api/v1/greetings
   ├── docs/                                   # Architecture & requirements documentation
   │   ├── adrs.md                             # Architecture Decision Records (ADR-0001 through ADR-0006)
   │   ├── class-diagram.puml                  # PlantUML domain model class diagram
   │   └── user-stories.md                     # User stories (TS01-TS02) & Requirements Traceability Matrix
   ├── CHANGELOG.md                            # Project release notes & version history
   ├── LICENSE.md                              # Project license
   └── README.md                               # Project overview & guide
   ```

   ---

   ## Bounded Contexts & Domain Model

   ### `Acme.Hello.Platform.Profiles`
   - **`Developer`** (*Entity*): identity (`Guid`, UUID v7) plus a `PersonName` that's never missing; a developer who doesn't reveal one gets the well-known `PersonName.Anonymous` instead (see [ADR-0004](docs/adrs.md#adr-0004-uuid-v7-for-developer-identifiers) and [ADR-0005](docs/adrs.md#adr-0005-anonymous-greetings-instead-of-rejecting-missing-names)).
   - **`PersonName`** (*Value Object*): validated `readonly record struct`; even its well-known `Anonymous` placeholder is a fully validated instance, never a blank or unvalidated one (see [ADR-0003](docs/adrs.md#adr-0003-personname-as-a-value-object-readonly-record-struct)).
   - **`IGreetingCounter`** (*Domain Service*): thread-safe, personalized and anonymous greetings tracked separately, never collapsed into a single total (see [ADR-0001](docs/adrs.md#adr-0001-greeting-count-as-a-domain-service), [ADR-0002](docs/adrs.md#adr-0002-thread-safe-counter-via-interlocked-and-volatile), and [ADR-0006](docs/adrs.md#adr-0006-track-personalized-and-anonymous-greetings-separately)).

   ---

   ## Key Domain Rules & Design Invariants

   - **Anonymous is a value, not an absence**: `PersonName` always validates and throws when constructed, `Anonymous` included; "no name" is modeled as `Developer.Name` holding that well-known `PersonName`, never as `null` (see [ADR-0003](docs/adrs.md#adr-0003-personname-as-a-value-object-readonly-record-struct)).
   - **Anonymous greetings are legitimate, not rejected**: a missing or blank name produces a `201` anonymous greeting; a name that is present but too long is still rejected with a `400` (see [ADR-0005](docs/adrs.md#adr-0005-anonymous-greetings-instead-of-rejecting-missing-names)).
   - **Every developer has a real identity**: `Guid.CreateVersion7()` runs for every `Developer`, personalized or anonymous, never a nullable ID (see [ADR-0004](docs/adrs.md#adr-0004-uuid-v7-for-developer-identifiers)).
   - **Personalized and anonymous counts tracked separately**: `TotalCount` is always computed as their sum, never its own field, so it can't drift out of sync (see [ADR-0006](docs/adrs.md#adr-0006-track-personalized-and-anonymous-greetings-separately)).
   - **Thread-safe counting**: `Interlocked`/`Volatile` on each backing field, not a `lock` (see [ADR-0002](docs/adrs.md#adr-0002-thread-safe-counter-via-interlocked-and-volatile)).

   ---

   ## Project Documentation

   | Document | Description |
   | :--- | :--- |
   | [**Architecture Decision Records (ADRs)**](docs/adrs.md) | Six architectural decisions (ADR-0001 through ADR-0006). |
   | [**User Stories & RTM**](docs/user-stories.md) | User stories (TS01-TS02) and Requirements Traceability Matrix. |
   | [**Class Diagram**](docs/class-diagram.puml) | PlantUML class diagram of the `Profiles` bounded context. |
   | [**Changelog**](CHANGELOG.md) | Version history and release notes. |
   | [**License**](LICENSE.md) | Project licensing information (MIT). |

   ---

   ## Getting Started

   ### Prerequisites
   - [.NET 10 SDK](https://dotnet.microsoft.com/download) (or later)

   ### Build the Solution
   ```bash
   dotnet build
   ```

   ### Run the Application
   ```bash
   dotnet run --project Acme.Hello.Platform
   ```

   ### Access the API
   Port `5195` below is this project's own; check `Properties/launchSettings.json` for yours if it's different.
   - Scalar UI: `http://localhost:5195/scalar/v1`
   - Or send requests directly, for example:
     ```bash
     curl -X POST http://localhost:5195/api/v1/greetings \
          -H "Content-Type: application/json" \
          -d '{"firstName": "John", "lastName": "Doe"}'
     ```
   ````
   </details>

   ```
   git add .
   git commit -m "docs(readme): document the architecture decisions."
   git push
   ```

---

## Testing (optional, explore on your own)

Everything above already shipped as `1.0.0`. This section is optional: it adds a unit test project covering the domain model and the counter, then leaves you a starting point to go further.

1. **Add the test project.** Switch Solution Explorer to **Solution** view. Right-click the solution root → `Add` → `New Project...` → `xUnit Test Project` → name it `Acme.Hello.Platform.Tests`, same solution directory, then add the reference to the main project:

   ```
   dotnet add Acme.Hello.Platform.Tests reference Acme.Hello.Platform
   ```

2. **Create each test file below**, in the matching folder under `Acme.Hello.Platform.Tests` (create it the same way as any other folder if it doesn't exist yet): `PersonNameTests.cs`, `DeveloperTests.cs`, `GreetingCounterTests.cs`. They cover, between them:
   - `PersonName`'s validation, trimming, and equality.
   - `Developer`'s three constructors, including the anonymous one, and that invalid names still throw when constructed directly.
   - `GreetingCounter`'s thread safety under concurrent increments.

   These already exist in this project's own repository under `Acme.Hello.Platform.Tests/`, one class per domain type, exactly the shape described above; read them there rather than retyping them here.

3. **Run the suite.**

   ```
   dotnet test
   ```

4. **Update `README.md`** now that the test suite exists. Replace the file from `## Document the Project` with the version below: this adds the `Tests` badge, the `Testing Framework` bullet, the `Acme.Hello.Platform.Tests` entry in `## Solution Structure`, and a `### Run the Automated Test Suite` step under `## Getting Started`.

   <details>
   <summary>README.md</summary>

   ````markdown
   # Hello ASP.NET Developer (`hello-asp-net-developer`)

   [![.NET](https://img.shields.io/badge/.NET-10-purple.svg)](https://dotnet.microsoft.com/)
   [![C#](https://img.shields.io/badge/C%23-14-blue.svg)](https://learn.microsoft.com/dotnet/csharp/)
   [![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](LICENSE.md)
   [![Tests](https://img.shields.io/badge/Tests-passing-brightgreen.svg)](Acme.Hello.Platform.Tests)

   `hello-asp-net-developer` is a sample ASP.NET Core Minimal API illustrating Object-Oriented Programming and Domain-Driven Design in a single bounded context (`Profiles`): a greeting endpoint that works for both a named and an anonymous developer.

   ---

   ## Technical Stack & Modern Features

   - **Runtime & Framework**: .NET 10.0 (C# 14.0), ASP.NET Core Minimal APIs
   - **Testing Framework**: xUnit with `Microsoft.NET.Test.Sdk`
   - **C# 14 & .NET 10 Features**:
     - **`field` Keyword**: property validation and null-safe fallback without an explicit private backing field (`PersonName`).
     - **Struct Parameterless Constructor Safety**: `PersonName`, a `readonly record struct`, throws `InvalidOperationException` from `new PersonName()`.
     - **UUIDv7 Identifiers**: time-ordered identifiers via `Guid.CreateVersion7()` (`Developer.Id`), even for an anonymous developer.
     - **Minimal API Validation**: `builder.Services.AddValidation()`, a .NET 10 built-in, no separate validation package.
     - **Modern Throw Helpers**: `ArgumentException.ThrowIfNullOrWhiteSpace`, not hand-written null checks.

   ---

   ## Solution Structure

   ```text
   hello-asp-net-developer/
   ├── Acme.Hello.Platform/                    # Main API project
   │   └── Profiles/                           # Profiles Bounded Context
   │       ├── Domain/
   │       │   ├── Model/
   │       │   │   ├── Entities/               # Developer
   │       │   │   └── ValueObjects/           # PersonName
   │       │   └── Services/                   # IGreetingCounter, GreetingCounter (Internal)
   │       └── Interfaces/Rest/
   │           ├── Assemblers/                 # DeveloperAssembler, GreetDeveloperAssembler
   │           ├── Resources/                  # GreetDeveloperRequest/Response, GetGreetingCountResponse
   │           └── GreetingEndpoints.cs        # Maps GET/POST /api/v1/greetings
   ├── Acme.Hello.Platform.Tests/               # Automated xUnit test suite (25 tests)
   ├── docs/                                   # Architecture & requirements documentation
   │   ├── adrs.md                             # Architecture Decision Records (ADR-0001 through ADR-0006)
   │   ├── class-diagram.puml                  # PlantUML domain model class diagram
   │   └── user-stories.md                     # User stories (TS01-TS02) & Requirements Traceability Matrix
   ├── CHANGELOG.md                            # Project release notes & version history
   ├── LICENSE.md                              # Project license
   └── README.md                               # Project overview & guide
   ```

   ---

   ## Bounded Contexts & Domain Model

   ### `Acme.Hello.Platform.Profiles`
   - **`Developer`** (*Entity*): identity (`Guid`, UUID v7) plus a `PersonName` that's never missing; a developer who doesn't reveal one gets the well-known `PersonName.Anonymous` instead (see [ADR-0004](docs/adrs.md#adr-0004-uuid-v7-for-developer-identifiers) and [ADR-0005](docs/adrs.md#adr-0005-anonymous-greetings-instead-of-rejecting-missing-names)).
   - **`PersonName`** (*Value Object*): validated `readonly record struct`; even its well-known `Anonymous` placeholder is a fully validated instance, never a blank or unvalidated one (see [ADR-0003](docs/adrs.md#adr-0003-personname-as-a-value-object-readonly-record-struct)).
   - **`IGreetingCounter`** (*Domain Service*): thread-safe, personalized and anonymous greetings tracked separately, never collapsed into a single total (see [ADR-0001](docs/adrs.md#adr-0001-greeting-count-as-a-domain-service), [ADR-0002](docs/adrs.md#adr-0002-thread-safe-counter-via-interlocked-and-volatile), and [ADR-0006](docs/adrs.md#adr-0006-track-personalized-and-anonymous-greetings-separately)).

   ---

   ## Key Domain Rules & Design Invariants

   - **Anonymous is a value, not an absence**: `PersonName` always validates and throws when constructed, `Anonymous` included; "no name" is modeled as `Developer.Name` holding that well-known `PersonName`, never as `null` (see [ADR-0003](docs/adrs.md#adr-0003-personname-as-a-value-object-readonly-record-struct)).
   - **Anonymous greetings are legitimate, not rejected**: a missing or blank name produces a `201` anonymous greeting; a name that is present but too long is still rejected with a `400` (see [ADR-0005](docs/adrs.md#adr-0005-anonymous-greetings-instead-of-rejecting-missing-names)).
   - **Every developer has a real identity**: `Guid.CreateVersion7()` runs for every `Developer`, personalized or anonymous, never a nullable ID (see [ADR-0004](docs/adrs.md#adr-0004-uuid-v7-for-developer-identifiers)).
   - **Personalized and anonymous counts tracked separately**: `TotalCount` is always computed as their sum, never its own field, so it can't drift out of sync (see [ADR-0006](docs/adrs.md#adr-0006-track-personalized-and-anonymous-greetings-separately)).
   - **Thread-safe counting**: `Interlocked`/`Volatile` on each backing field, not a `lock` (see [ADR-0002](docs/adrs.md#adr-0002-thread-safe-counter-via-interlocked-and-volatile)).

   ---

   ## Project Documentation

   | Document | Description |
   | :--- | :--- |
   | [**Architecture Decision Records (ADRs)**](docs/adrs.md) | Six architectural decisions (ADR-0001 through ADR-0006). |
   | [**User Stories & RTM**](docs/user-stories.md) | User stories (TS01-TS02) and Requirements Traceability Matrix. |
   | [**Class Diagram**](docs/class-diagram.puml) | PlantUML class diagram of the `Profiles` bounded context. |
   | [**Changelog**](CHANGELOG.md) | Version history and release notes. |
   | [**License**](LICENSE.md) | Project licensing information (MIT). |

   ---

   ## Getting Started

   ### Prerequisites
   - [.NET 10 SDK](https://dotnet.microsoft.com/download) (or later)

   ### Build the Solution
   ```bash
   dotnet build
   ```

   ### Run the Application
   ```bash
   dotnet run --project Acme.Hello.Platform
   ```

   ### Access the API
   Port `5195` below is this project's own; check `Properties/launchSettings.json` for yours if it's different.
   - Scalar UI: `http://localhost:5195/scalar/v1`
   - Or send requests directly, for example:
     ```bash
     curl -X POST http://localhost:5195/api/v1/greetings \
          -H "Content-Type: application/json" \
          -d '{"firstName": "John", "lastName": "Doe"}'
     ```

   ### Run the Automated Test Suite
   ```bash
   dotnet test
   ```
   ````
   </details>

   ```
   git add .
   git commit -m "docs(readme): document the test suite."
   git push
   ```

5. **From here, it's on you.** Add a test for `DeveloperAssembler`/`GreetDeveloperAssembler` (neither is covered above, the domain layer is), or break a validation rule on purpose and confirm a test catches it.

---

## Appendix

### Continuing on another computer

- Install [Rider](https://www.jetbrains.com/rider/) and the .NET 10 SDK.
- Sign in to `gh` again: `gh auth login` (see [Project Setup step 11](#project-setup)).
- Clone: `gh repo clone <org>/hello-asp-net-developer`, or from Rider's Welcome screen: `Clone Repository`, paste `https://github.com/<org>/hello-asp-net-developer.git`.
- Open the solution; Rider restores NuGet packages automatically on first open.
- Plugins live in the IDE, not the repo, so reinstall Git Flow Helper (Project Setup step 12) and plantuml4idea (Project Setup step 8) if this machine doesn't have them.
- Register your GitHub account in the IDE (Project Setup step 13): get the token with

  ```
  gh auth token
  ```

  and add it under `Settings` → `Version Control` → `GitHub`.

### Signing in to GitHub with a token

The guide uses `gh auth login` (Project Setup step 11), which is the simplest way. If you can't install `gh`, GitHub also accepts a Personal Access Token.

1. GitHub → `Settings` → `Developer settings` → `Personal access tokens` → `Generate new token (classic)`. Use **classic**, not "Fine-grained tokens": fine-grained tokens need an organization owner's approval before they work.
2. Fill in:
   - **Note:** `UPC` (a label to recognize it later).
   - **Expiration:** the default is fine.
   - **Scopes:** check only the top-level `repo` checkbox.
3. Click **Generate token**, then copy it somewhere safe (a password manager) before navigating away. GitHub shows it **only once**. The IDE GitHub account (Project Setup step 13) needs it too.

### Backing up unfinished work

Mid-feature and need to stop? Commit what you have, even if incomplete, and push the feature branch:

```
git add .
git commit -m "wip: <short description of where you stopped>."
git push -u origin <branch-name>
```

`wip` (work in progress) isn't one of the conventional commit types this guide otherwise uses; it's a deliberate signal that this commit is a checkpoint, not a finished unit of work, expected to be rewritten or squashed later.

### Feature Finish and pull requests

This guide's `Feature Finish` merges straight into `develop` with no pull request, appropriate for a solo project. A team would instead:

1. `Feature Publish` (pushes the branch, no `Feature Finish`).
2. Open a pull request on GitHub, `feature/xxx` → `develop`.
3. Review, then merge through GitHub, not through Git Flow Helper.

### Shipping a fix after a release (hotfix)

A `Hotfix` is for a defect in a release that is already tagged and published, when the correction shouldn't wait for the next feature to ship (a wrong value in `LICENSE.md`, a bug found right after tagging). It branches from `main`, not `develop`, and merges back into both.

**Note:** Git Flow Helper's `Hotfix` menu is greyed out while you're on `develop`. Check out `main` first: branch widget in the status bar (bottom-right) → `main` → `Checkout`.

1. **Start the hotfix.** Git Flow Helper widget → `Hotfix` → `Hotfix Start` → **Version description** `v1.0.1` (the next patch number after the release you're fixing) → `OK`. Creates and switches you to `hotfix/v1.0.1`.

2. **Make the fix, then record it.**
   - Correct whatever is wrong.
   - In `Acme.Hello.Platform.csproj`, bump `<Version>`, for example `1.0.0` → `1.0.1`.
   - In `CHANGELOG.md`, add an entry above the previous one:

     ```markdown
     ## [1.0.1] - YYYY-MM-DD

     ### Fixed
     - <one line describing the fix>
     ```

3. **Commit.** Don't push; `Hotfix Publish` does that next.

   ```
   git add .
   git commit -m "fix: <short description>"
   ```

4. **Publish and finish the hotfix.**
   - Git Flow Helper widget → `Hotfix` → `Hotfix Publish` (pushes `hotfix/v1.0.1`).
   - Git Flow Helper widget → `Hotfix` → `Hotfix Finish` (`Integrate Immediately`, `Keep remote branch when finished` unchecked).

   `Hotfix Finish` merges `hotfix/v1.0.1` into `main` (tagging it `v1.0.1`), merges it into `develop`, pushes both, and deletes the branch.

5. **Publish the GitHub Release.**
   - On GitHub: **Releases** → **Draft a new release**.
   - Tag: pick the existing `v1.0.1` (do not create a new one).
   - Release title: `Version 1.0.1`.
   - Description: the release notes below.
   - **Set as the latest release** checked; **Set as a pre-release** unchecked.
   - Click **Publish release**.

   <details>
   <summary>Release notes (1.0.1)</summary>

   ```markdown
   ## 🐛 Fixed

   - <one line describing the fix>
   ```
   </details>

**Note:** if the `Hotfix` menu stays greyed even from `main`, a dropped connection can leave the plugin in a stale state; re-run `Init` from the widget (same prefixes, harmless) or restart Rider. The plain-git equivalent works too: branch `hotfix/v1.0.1` from `main`, commit, `git merge --no-ff` it into both `main` (then `git tag -a v1.0.1 -m "Version 1.0.1"`) and `develop`, delete the branch, then `git push origin main develop v1.0.1`.

### Removing a stray .git folder

Ran `git init` from inside `Acme.Hello.Platform/` instead of the solution root? Remove the stray repository and start over from the solution root:

- macOS/Linux:
  ```
  rm -rf Acme.Hello.Platform/.git
  ```
- Windows (PowerShell):
  ```
  Remove-Item -Recurse -Force Acme.Hello.Platform\.git
  ```

### Creating the repo without the GitHub CLI

No `gh`? Do the whole thing through the GitHub website plus plain `git`.

1. Authenticate git first, since `gh auth login` isn't available: follow [Signing in to GitHub with a token](#signing-in-to-github-with-a-token).
2. On GitHub, inside your organization, create an empty **private** repo named `hello-asp-net-developer`, with no README, license, or `.gitignore` (this project already has all three).
3. On the repo's "Quick setup" page, copy the **HTTPS** clone URL, the one ending in `.git`, not the address-bar URL.
4. From the solution root, add the remote and push:

   ```
   git remote add origin https://github.com/<org>/hello-asp-net-developer.git
   git push -u origin main
   ```

5. On GitHub, set the repo's **About** description manually (gear icon next to "About" on the repo page):

   ```
   An ASP.NET Core Minimal API illustrating Object-Oriented Programming and Domain-Driven Design through a greeting endpoint for developers, personalized or anonymous.
   ```

### If the class diagram doesn't render

- Confirm the **plantuml4idea** plugin is installed and enabled (`Settings` → `Plugins` → `Installed`).
- The plugin needs a local Java runtime and Graphviz to render; if it reports either missing, install a JDK and Graphviz, then restart Rider.
- As a fallback, paste the diagram's content into the [PlantUML web server](https://www.plantuml.com/plantuml/uml/) to render it in a browser.

### Free JetBrains license for students

Rider is free for students through the [JetBrains Student Pack](https://www.jetbrains.com/community/education/#students): apply with a school email address, or upload proof of enrollment if your school email isn't recognized. Approval usually takes a few minutes.

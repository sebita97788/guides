# Hello Vue Developer Guide

## Table of Contents

- [Project Setup](#project-setup)
- [(US001) Register a Developer](#register-a-developer-us001)
- [(US002) Greet the Last Registered Developer](#greet-the-last-registered-developer-us002)
- [(US003) Track Valid Registrations](#track-valid-registrations-us003)
- [(US004) Defer Registration](#defer-registration-us004)
- [(US005) Clear the Registration Form](#clear-the-registration-form-us005)
- [Prepare the First Release](#prepare-the-first-release)
- [Release](#release)
- [Document the Project](#document-the-project)
- [Testing (optional, explore on your own)](#testing-optional-explore-on-your-own)
- [Appendix](#appendix)
  - [Continuing on another computer](#continuing-on-another-computer)
  - [Signing in to GitHub with a token](#signing-in-to-github-with-a-token)
  - [Creating the repo without the GitHub CLI](#creating-the-repo-without-the-github-cli)
  - [Backing up unfinished work](#backing-up-unfinished-work)
  - [Feature Finish and pull requests](#feature-finish-and-pull-requests)
  - [Removing a stray .git folder](#removing-a-stray-git-folder)
  - [Fixing file or folder permissions](#fixing-file-or-folder-permissions)
  - [If the class diagram doesn't render](#if-the-class-diagram-doesnt-render)
  - [Free JetBrains license for students](#free-jetbrains-license-for-students)

## Project Setup

1. **Make sure Node.js 24.20 LTS (or newer) is installed.** npm comes bundled with it.
   - First check what you already have. In a terminal:

     ```
     node --version
     ```

     ```
     npm --version
     ```

   - If `node --version` prints `v24.20.x` or higher, you are done with this step, skip to step 2.
   - If it prints an older `v24` (like `v24.14.x`), or anything else, or "command not found", install or update Node:
     - macOS:

       ```
       brew update
       ```

       ```
       brew install node@24
       ```

     - Windows: download the **Node.js 24 LTS** installer from [nodejs.org/en/download](https://nodejs.org/en/download) (the page shows "LTS" and "Current" side by side, pick the one labeled **LTS**, confirm the version number reads `24.20` or higher) and run it.
   - Check again, in a new terminal:

     ```
     node --version
     ```

     `node --version` must now read `v24.20.x` or higher.

   **Note:** this project needs at least `24.20`, not just "any `24`": older `24.x` patches (`24.14` in particular) have caused real problems on lab machines with the rest of this course's toolchain. `24.20` is the floor, a newer patch is fine too.

   **Note:** on some lab Macs, `brew install node@24` finishes with no error but `node --version` still shows an older `v24.x.x` (for example `v24.14.0`), it still matches "starts with `v24`", so it is easy to miss, this is exactly the case above. Homebrew installs a versioned formula like `node@24` "keg-only", without linking it onto your `PATH`, so whatever `node` was already there (another Homebrew formula, or the system one) keeps running. `brew update` alone does not fix an already-installed keg-only formula still on `PATH`. Point your shell at the versioned install instead:

   ```
   echo 'export PATH="/opt/homebrew/opt/node@24/bin:$PATH"' >> ~/.bash_profile
   ```

   **You must restart the terminal for this to take effect**, close the terminal window or tab entirely and open a new one (an already-open tab keeps the old `PATH` even after this command runs; if you have WebStorm open, restart its Terminal tool window too). Then check `node --version` again. If your terminal runs zsh instead (macOS's current default shell), use `~/.zshrc` in place of `~/.bash_profile`.

   If a later step complains it cannot find Node's headers or libraries while compiling something, also export these (once per terminal session, or add them to the same profile file):

   ```
   export LDFLAGS="-L/opt/homebrew/opt/node@24/lib"
   export CPPFLAGS="-I/opt/homebrew/opt/node@24/include"
   ```

   **Note:** `24` is the LTS line this guide targets, `24.20` and up is what it requires. [nodejs.org](https://nodejs.org) shows the current LTS major on its front page; this project runs fine on a newer LTS too.

   **Note:** on a shared lab Mac, every student uses the same `alumnos` account, so if a previous student ran an `npm install` command with `sudo` at some point, its npm cache (`~/.npm`) is now owned by `root` instead of `alumnos`. When that happens, every later `npm install` fails with an `EACCES` permission error, even on a machine where Node itself is installed correctly. This does not happen on every machine, so fix it now, before the first `npm install` in step 2, running it does nothing if the cache was already fine:

   ```
   sudo chown -R alumnos:staff ~/.npm
   ```

   On your own Mac, use your own account instead of `alumnos`:

   ```
   sudo chown -R "$(whoami):staff" ~/.npm
   ```

   Never fix a permission error by adding more `sudo`, it only moves the ownership problem to the next command. This is a macOS-only fix, Windows does not use this permission model, `npm install` there fails differently, from a read-only folder, which is fixed through the folder's `Properties` dialog, not the terminal.

2. **Create the project.** Two ways, pick one. Either leaves the same scaffold on disk.

   **Option A, from the terminal.** In the folder where you keep your projects (on the lab Macs, `~/Documents`), run:

   ```
   npm create vite@latest hello-vue-developer -- --template vue
   ```

   This is Vite's own scaffolding tool; `--template vue` skips the interactive framework/variant prompts (plain JavaScript, not TypeScript). It still asks one question:

   ```
   Install with npm and start now? … yes / no
   ```

   Answer **No**, `npm install` runs as its own explicit step next, so it is clear what it does and when. Then:

   ```
   cd hello-vue-developer
   ```

   ```
   npm install
   ```

   Unlike some scaffolding tools, `npm create vite` does **not** initialize git for you, that stays an explicit step, in step 9 below.

   Open the folder in WebStorm afterward: `File` → `Open`.

   **Option B, from WebStorm.** `File` → `New Project`. In the left list under **Generators**, pick **Vite** (it scaffolds through the same `create-vite` template as Option A, framework-agnostic, so the wizard asks for the framework itself).
   - **Location:** your projects folder plus `hello-vue-developer` at the end.
   - **Node runtime:** leave at its detected default.
   - **Vite:** leave this dropdown at its default value, `npx create-vite`.
   - **Template:** pick `Vue` from the dropdown.
   - Make sure **Use TypeScript template** is unchecked, this project is plain JavaScript.
   - **Create**, then run `npm install` in the WebStorm terminal if the wizard did not do it for you.

   **Note:** on a shared lab Mac, either option can fail with a permissions error, because a previous account owns files under your home folder or the new project folder. Take ownership, then re-run the failed command. The lab account is `alumnos`, group `staff`:

   ```
   sudo chown -R alumnos:staff ~/Documents
   ```

   Then the project folder itself, since the recursive command above sometimes still leaves it unwritable. Use the path where you actually created it:

   ```
   sudo chown -R alumnos:staff ~/Documents/hello-vue-developer
   ```

   The paths are examples: put your own project location in (for instance `~/Documents/<your-nrc>/hello-vue-developer`). On your own Mac, use your account instead of `alumnos` (run `whoami`). Full details, including the `npm install` case and the Windows equivalent, are in [Appendix: Fixing file or folder permissions](#fixing-file-or-folder-permissions).

3. **Adjust the project metadata in `package.json`.** Open it. Leave `name`, `type`, `scripts`, `dependencies`, and `devDependencies` exactly as the scaffold wrote them; only touch the top:
   - Change `"version": "0.0.0"` to `"version": "0.1.0"`.
   - Add `"description"`, `"author"`, and `"license"` right after `"version"`:

     ```json
     "description": "A Hello Developer project for Vue.js",
     "author": "Web Applications Development Team",
     "license": "MIT",
     ```

   **Note:** starting at `0.1.0`, below `1.0.0`, signals early development: the structure and behavior can still change freely from one version to the next. `## Release` at the end of this guide bumps it to `1.0.0`, the first version meant to stay stable.

4. **Replace the wizard's starter page.** The scaffold ships a demo counter (`src/components/HelloWorld.vue`, wired into `src/App.vue`). This project builds its own components instead.
   - Rename `src/App.vue` to `src/app.vue`. Right-click the file → `Refactor` → `Rename`, or rename it from the File System view and fix the import by hand.

     **Note:** on Windows, and on a Mac with the default file system, file names are not case-sensitive, so a rename that only changes the case (`App.vue` to `app.vue`) can silently do nothing, the file stays `App.vue`. If that happens, rename it twice, first to any different name, then to the final one:
     - `App.vue` → `App2.vue`
     - `App2.vue` → `app.vue`
   - Delete `src/components/HelloWorld.vue` (and the now-empty `src/components` folder).
   - Open `src/main.js`.

     **Note:** if you used `Refactor` → `Rename` in the previous bullet, WebStorm already rewrote this import for you, the file already matches the block below, there is nothing to change. If you renamed the file outside the IDE instead (Finder, Explorer, a plain `mv`), the import still reads `./App.vue`, edit it by hand to match, or `npm run dev` fails to find the file.

     <details>
     <summary>src/main.js</summary>

     ```javascript
     import {createApp} from 'vue'
     import './style.css'
     import App from './app.vue'

     createApp(App).mount('#app')
     ```
     </details>

   - Open `src/app.vue` and clear it down to an empty shell, this is where every feature below wires in its component:

     <details>
     <summary>src/app.vue (empty shell)</summary>

     ```vue
     <script setup>
     </script>

     <template>
     </template>

     <style>
     </style>
     ```
     </details>

   **Note:** `setup` in `<script setup>` refers to the Composition API's `setup()` function. Before it existed, a component's logic was split across separate options, `data`, `methods`, `computed`, and so on (the Options API, still valid but not what this project uses). The Composition API replaces all of that with one function, `setup()`, where you write the component's state and behavior together, then `return` whatever the template needs. `<script setup>` is compiler sugar for exactly that: everything declared at the top level of the block, an import, a `ref`, a function, is compiled into that `setup()` function for you, and every one of those bindings is exposed to the template automatically, no `return` statement and no `export default { setup() { ... } }` wrapper to write by hand.

   ```
   git add .
   git commit -m "chore: replace the wizard's starter page with an empty shell."
   ```

   **Note:** no repository exists yet (step 10 creates one), so this commit, like every commit before then, is staged only in your head, not run for real until then. Keep reading, the git commands throughout this guide are exactly what you will run once the repository exists.

5. **Check the toolchain runs.** In the WebStorm terminal (`View` → `Tool Windows` → `Terminal`):

   ```
   npm run dev
   ```

   Open `http://localhost:5173/` in a browser: a blank page renders (the empty shell from the previous step), with no errors in the browser console or the terminal. Stop the server with `Ctrl+C`.

6. **Create `docs/user-stories.md`.** Right-click the project root → `New` → `File` → type `docs/user-stories.md` → Enter.

   **Tip:** typing the `docs/` prefix creates that folder too.

   <details>
   <summary>docs/user-stories.md</summary>

   ```markdown
   # User Stories

   This document contains the user stories for the Hello Vue Developer application.

   ## Requirement Traceability Matrix

   | User Story | Scenario                                       | Implementation                                                                 |
   |------------|-------------------------------------------------|---------------------------------------------------------------------------------|
   | US001      | Register with a valid full name                 | `PersonName`, `DeveloperId`, `Developer`, `DeveloperRegistration`               |
   | US001      | Reject a spaces-only or partial name             | `PersonName.isValid()`, `DeveloperRegistration` (error message)                 |
   | US002      | Greet the last registered developer              | `DeveloperGreeting`, `Developer.fullName`, `Developer.isRegisterable()`         |
   | US002      | No greeting before any registration              | `app.vue` (`hasRegistered`)                                                     |
   | US003      | Count only valid registrations                   | `Developer.isRegisterable()`, `DeveloperCountShow`, `app.vue` (`developerCount`)|
   | US004      | Defer registration with "Later"                  | `DeveloperRegistration.deferRegistration()`, `app.vue` (`resetRegisteredDeveloperInfo`) |
   | US005      | Clear the form without changing the greeting     | `DeveloperRegistration.clearFields()`                                          |

   ## US001: Register a Developer

   **As a** developer learning Vue,
   **I want** to register with my first and last names,
   **so that** I can be recognized as a Vue developer.

   **Acceptance Criteria**:
   - **Given** no developer is currently registered, **when** a developer provides a valid first name and last name, **then** the system records the developer's details and acknowledges their registration.
   - **Given** no developer is currently registered, **when** a developer provides a first name or last name containing only spaces, **then** the system rejects the registration and indicates valid names are required.
   - **Given** no developer is currently registered, **when** a developer provides only a first name, **then** the system rejects the registration and indicates both names are required.
   - **Given** no developer is currently registered, **when** a developer provides only a last name, **then** the system rejects the registration and indicates both names are required.
   - **Given** no developer is currently registered, **when** a developer provides neither first name nor last name, **then** the system rejects the registration and indicates both names are required.

   ## US002: Greet the Last Registered Developer

   **As a** developer learning Vue,
   **I want** to be welcomed after registering,
   **so that** I feel encouraged to continue exploring Vue.

   **Acceptance Criteria**:
   - **Given** a developer has registered with valid name details, **when** the system processes the registration, **then** the system greets the developer using their full name.
   - **Given** no developer has registered, **when** the system starts, **then** the system does not provide any greeting.
   - **Given** a developer was previously greeted, **when** another developer registers with valid names, **then** the system greets the new developer using their full name.

   ## US003: Track Valid Registrations

   **As a** stakeholder monitoring participation,
   **I want** to know the number of developers who have registered with valid names,
   **so that** I can track engagement accurately.

   **Acceptance Criteria**:
   - **Given** the system is tracking registered developers, **when** a developer registers with valid name details, **then** the system increments the count of registered developers by one.
   - **Given** the system is tracking registered developers, **when** a developer attempts to register with missing or spaces-only names for either field, **then** the system does not increment the count.
   - **Given** multiple developers have registered with valid names, **when** a new developer registers with valid names, **then** the system increments the count to reflect all valid registrations.

   ## US004: Defer Registration

   **As a** developer learning Vue,
   **I want** to defer registering my details,
   **so that** I can explore the application without committing immediately.

   **Acceptance Criteria**:
   - **Given** the system allows registration, **when** a developer chooses to defer registration, **then** the system clears any current registration details and acknowledges the deferral.
   - **Given** a developer was previously registered, **when** a developer chooses to defer registration, **then** the system stops acknowledging the previous registration.
   - **Given** the system has deferred a registration, **when** a developer attempts to register later, **then** the system allows a new registration with valid names.

   ## US005: Clear the Registration Form

   **As a** developer learning Vue,
   **I want** to clear my input without affecting the current greeting or count,
   **so that** I can start over without committing or deferring.

   **Acceptance Criteria**:
   - **Given** the developer has entered some input and has not yet registered, **when** the developer chooses to clear the input, **then** the input fields are cleared and the greeting remains whatever it was before (anonymous, since no registration occurred).
   - **Given** the developer has registered a name and later enters new input without submitting it, **when** the developer chooses to clear the input, **then** the input fields are cleared and the previously registered greeting and count are left untouched.
   ```
   </details>

   **Note:** the Requirement Traceability Matrix already names classes that do not exist yet. That is expected: the matrix records the plan, the sections below build exactly what it points at.

7. **Look at the architecture before writing any code.**
   - Real projects rarely start from a blank slate: the course already sets DDD and this bounded-context split as part of the Definition of Done. What is ahead is learning to read a given architecture and implement it well.
   - Install the **plantuml4idea** plugin so the diagram renders: `File` → `Settings` → `Plugins` → `Marketplace` → search `plantuml4idea` → `Install`. Restart the IDE if prompted.
   - Right-click the `docs` folder → `New` → `File` → type `class-diagram.puml` → Enter. WebStorm shows a rendered preview beside the source.

   Two areas: `greetings` (the `Developer` entity and `DeveloperId` value object, plus the three `.vue` components) and a `shared` kernel (`PersonName`, usable by any future context that deals with people, and the UUID utility). `Developer` receives a `DeveloperId` only once its `PersonName` is valid, so the presence of an ID marks a registered developer. This layered, bounded-context split is ADR-0001 in `## Document the Project`.

   <details>
   <summary>docs/class-diagram.puml</summary>

   ```
   @startuml Hello Vue Developer Class Diagram

     class "«Component»\nApp" as App {
       -registeredDeveloper: Developer
       -developerCount: Number
       -hasRegistered: Boolean
       -- methods --
       +updateRegisteredDeveloperInfo(payload: Object)
       +resetRegisteredDeveloperInfo()
       +updateDeveloperCount(developer: Developer)
     }



   package "shared" {
     package "domain.model" {
       class PersonName {
         -firstName: String
         -lastName: String
         +firstName: String
         +lastName: String
         +fullName: String
         +equals(other: PersonName): Boolean
         +isFullyNamed(): Boolean
       }
     }
   }

   package "greetings" {
     package "domain.model" {
       class Developer {
         -id: DeveloperId
         -name: PersonName
         +id: DeveloperId
         +name: PersonName
         +fullName: String
         +isRegisterable(): Boolean
         +isIdentified(): Boolean
       }

       class DeveloperId {
         -value: String
         +value: String
         +equals(other: DeveloperId): Boolean
         +{static} build(): DeveloperId
         +toString(): String
       }

       Developer "1" *-- "1" PersonName : name
       Developer "1" *-- "0..1" DeveloperId : id
     }

     package "presentation.components" {
       class "«Component»\nDeveloperRegistration" as DeveloperRegistration {
         -firstName: String
         -lastName: String
         -errorMessage: String
         -- methods --
         +submitRegistrationRequest()
         +deferRegistration()
         +clearFields()
         -- emits --
         developer-registered(payload: Object)
         registration-deferred(payload: Object)
       }

       class "«Component»\nDeveloperGreeting" as DeveloperGreeting {
         +developer: Developer
         +greeting: String
       }

       class "«Component»\nDeveloperCountShow" as DeveloperCountShow {
         +developerCount: Number
       }
     }

   }

   ' Relationships
   App *-down-> DeveloperRegistration : contains
   App *-down-> DeveloperGreeting : contains
   App *-down-> DeveloperCountShow : contains
   DeveloperRegistration -up-> App : emits developer-registered
   DeveloperRegistration -up-> App : emits registration-deferred
   DeveloperGreeting --> Developer : uses
   DeveloperGreeting --> PersonName : uses
   App --> Developer : uses
   App --> PersonName : uses
   App --> DeveloperId : uses
   DeveloperGreeting --> DeveloperId : uses
   DeveloperGreeting <-- App : passes developer
   DeveloperCountShow <-- App : passes developerCount

   @enduml
   ```
   </details>

   If it shows an error instead of a diagram, see [Appendix: If the class diagram doesn't render](#if-the-class-diagram-doesnt-render).

   **Note:** there is nothing to commit yet. The repository is initialized in step 9, and this file goes in with the rest of your work in that first commit.

8. **Confirm the wizard's `.gitignore`.** Vite already generated one at the project root. Open it and check it matches the block below; the template is what this project needs, so there is normally nothing to change.

   <details>
   <summary>.gitignore</summary>

   ```
   # Logs
   logs
   *.log
   npm-debug.log*
   yarn-debug.log*
   yarn-error.log*
   pnpm-debug.log*
   lerna-debug.log*

   node_modules
   dist
   dist-ssr
   *.local

   # Editor directories and files
   .vscode/*
   !.vscode/extensions.json
   .idea
   .DS_Store
   *.suo
   *.ntvs*
   *.njsproj
   *.sln
   *.sw?
   ```
   </details>

   **Note:** `.gitignore` has to exist before the first commit. `node_modules/` and the build output in `dist/` are large, regenerated, and never belong in history. `.idea` must be ignored too: WebStorm rewrites files inside it constantly (indexing, installing plugins), which otherwise leaves the working tree dirty at commit time.

9. **Initialize the local repository.**
    - The WebStorm **Terminal** opens at the project root; confirm the prompt shows the `hello-vue-developer` folder.
    - Run:

      ```
      git init -b main
      git config user.name "Your Name"
      git config user.email "your.email@example.com"
      git add .
      git commit -m "chore: initial commit."
      ```

    **Note:**
    - `-b main` names the first branch `main`. Without it the name depends on each machine's git config, and it needs to be `main` for **Git Flow Helper** to recognize it later.
    - `git config` without `--global` scopes the identity to this repo, so it will not affect anyone else on a shared machine.
    - This first commit includes everything from steps 1-8: the empty shell, `docs/user-stories.md`, and `docs/class-diagram.puml`.
    - Ran it from a subfolder by mistake? See [Appendix: Removing a stray .git folder](#removing-a-stray-git-folder).

10. **Connect to GitHub.**

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

      It shows a one-time code (like `3155-2B43`) and waits at `Press Enter to open https://github.com/login/device...`. Copy the code, press Enter, paste it into the page that opens, click **Authorize**. Answering `Yes` to the third prompt also configures git, so a later `git push` will not ask for credentials.

    **Create your own GitHub organization first.** Everything below pushes to an organization that is yours, never the course's.
    - If you do not have one, go to [github.com/organizations/plan](https://github.com/organizations/plan), pick the **Free** plan, choose an account name.
    - That account name is your `<org>` in the command below (angle brackets not typed): if the organization is `acme-labs`, the repo ends up at `github.com/acme-labs/hello-vue-developer`.

    **Create the private repo and push.** One command. Replace `<org>` with your organization's name.
    - Check the terminal is at the project root (the folder with `package.json`) first: `--source=.` acts on the current folder.
    - Run:

      ```
      gh repo create <org>/hello-vue-developer --private --source=. --remote=origin --push --description "A Vue.js application demonstrating core development concepts, including component architecture, reactive state management, and Domain-Driven Design (DDD) principles."
      ```

    - It creates the private repo in your org, adds it as `origin`, pushes `main`, and sets the About text (GitHub's own repo-level summary, separate from `README.md`).

    **Note:** no GitHub CLI? Create the repo on the website and push by hand: see [Appendix: Creating the repo without the GitHub CLI](#creating-the-repo-without-the-github-cli).

11. **Install the Git Flow Helper plugin.**
    - `File` → `Settings` → `Plugins` → `Marketplace` tab.
    - Search `Git Flow Helper`, click `Install`.
    - Restart the IDE if prompted.

12. **Initialize Git Flow.** Git Flow Helper pushes through WebStorm's own GitHub connection, not the terminal's. Register **your** account there first, and make sure it is the only one.
    - Get your token, copy what it prints (this is the same token your terminal git already uses):

      ```
      gh auth token
      ```

    - Open `File` → `Settings` → `Version Control` → `GitHub`.
    - If any account is already listed (a shared machine may still have someone else's), select each one and click `−` to remove it. The list must be empty before you add yours.
    - Click `+` → `Log In with Token...` (not `Log In via GitHub...`, whose browser sign-in produces an OAuth token your organization blocks for third-party apps).
    - Paste the `gh auth token` value, click `Add Account`. Close `Settings`.
    - Click the **Git Flow Helper** widget in the status bar → `Init`.
    - The branch-prefix fields (`Main`, `Develop`, `Feature`, `Release`, `Hotfix`) are pre-filled with sensible defaults; click `OK`.

    This creates a `develop` branch from `main` and pushes it. From here on, `main` is only touched through a Release, never worked on directly.

    **Note:** the current branch name should show in the status bar (bottom-right, a branch icon followed by the name). If nothing shows there, right-click an empty area of the status bar → check `Git Branch` in the widget list.

---

## Register a Developer ([US001](./user-stories.md))

A visitor types a first and last name and clicks **Register**. This story builds the domain from the ground up, a `PersonName` value object, the UUID utility, a `DeveloperId` value object, and the `Developer` entity, then the registration form that uses them.

1. **Start the feature.** Git Flow Helper widget → `Feature` → `Feature Start` → **Feature description** `register-developer` → `OK`. Creates and switches you to `feature/register-developer`.

2. **Create the `Developer` entity, fields only for now.** Right-click `src` → `New` → `JavaScript File` → type `greetings/domain/model/developer.entity` → Enter (WebStorm adds the `.js` and creates the folders). This is the aggregate the whole story is about, everything below gets built as this entity needs it.

   **Note:** file names below carry a type suffix, `.value-object.js`, `.entity.js`, this project's own convention for making the kind of domain object obvious from the file name alone, not a requirement of JavaScript or Vue.

   <details>
   <summary>src/greetings/domain/model/developer.entity.js (fields only)</summary>

   ```javascript
   export class Developer {
       _id;
       _name;
   }
   ```
   </details>

   **Note:** `_id` / `_name`, not `#id` / `#name`. Vue wraps values placed in a `ref()` or passed as a prop in a `Proxy`, and reading a native `#field` through that `Proxy` throws `TypeError`, the read only works against the exact original instance. The `_` prefix marks these internal by convention instead, which the `Proxy` has no trouble with. ADR-0003 in `## Document the Project` has the full reasoning. This is the only exception in this project's JavaScript to the `#`-private-fields convention used elsewhere in the course, and it is deliberate. Every domain class below follows the same `_` convention.

3. **Add `Developer`'s constructor.** It builds a `PersonName` from the given names, then assigns an identity only when that name is valid.

   <details>
   <summary>src/greetings/domain/model/developer.entity.js (constructor)</summary>

   ```javascript
   export class Developer {
       _id;
       _name;

       constructor(firstName, lastName) {
           const providedName = new PersonName(firstName, lastName);
           this._id = providedName.isValid() ? DeveloperId.build() : null;
           this._name = providedName;
       }
   }
   ```
   </details>

   **Note:** no `import` for `PersonName` or `DeveloperId`, neither file exists yet. WebStorm shows both names unresolved, that clears once each is created below, typing the name again or `Alt+Enter` on it adds the import for you.

   **Note:** an incomplete or empty name never throws here. `PersonName` accepts anything (step 4), and this constructor only asks `providedName.isValid()` to decide the `id`: `true` gets a real `DeveloperId`, `false` gets `null`. A `Developer` with `_id === null` is not an error, it is what an anonymous or partially-registered developer looks like in this domain. This is ADR-0005 in `## Document the Project`.

   **Note:** no commit here. The file does not run yet, `PersonName` and `DeveloperId` don't exist.

4. **Create the `PersonName` value object, undocumented.** Right-click `src` → `New` → `JavaScript File` → type `shared/domain/model/person-name.value-object` → Enter (WebStorm adds the `.js` and creates the folders). A value object is small enough to write whole: fields, constructor, accessors, and equality, all in this one step. It trims both names on the way in; an empty or all-whitespace name becomes `""`, never `null` or `undefined`. `fullName` joins whichever parts are present; `equals()` compares by value; `isValid()` (an alias for `isFullyNamed()`) is the invariant the rest of the domain relies on: both names present.

   <details>
   <summary>src/shared/domain/model/person-name.value-object.js (so far)</summary>

   ```javascript
   export class PersonName {
       _firstName;
       _lastName;

       constructor(firstName, lastName) {
           const trimmedFirstName = firstName?.trim() || "";
           const trimmedLastName = lastName?.trim() || "";
           this._firstName = trimmedFirstName;
           this._lastName = trimmedLastName;
       }

       get firstName() {
           return this._firstName;
       }

       get lastName() {
           return this._lastName;
       }

       get fullName() {
           return [this._firstName, this._lastName].filter(name => name.length > 0).join(" ");
       }

       equals(other) {
           return other instanceof PersonName &&
               this._firstName === other.firstName &&
               this._lastName === other.lastName;
       }

       isValid() {
           return this.isFullyNamed();
       }

       isFullyNamed() {
           return this._firstName.length > 0 && this._lastName.length > 0;
       }
   }
   ```
   </details>

   **Note:** `firstName?.trim() || ""` combines two operators. `?.` is optional chaining, if `firstName` is `null` or `undefined`, it stops right there and evaluates to `undefined` instead of throwing on `.trim()`. `||` is a fallback, if what is on its left is falsy, `undefined`, an empty string, `0`, and so on, it evaluates to the right side instead. Together: call `.trim()` only if there is something to call it on, and fall back to `""` either way, whether `firstName` was missing or trimming it left nothing. The result is always a string, never `null` or `undefined`. Spaces do not render reliably in a table or in regular text, so the table below marks each one with `·`:

   | raw `firstName` passed in | `_firstName` after the constructor |
   |---|---|
   | `"··Ada··"` | `"Ada"` |
   | `"···"` (only spaces) | `""` |

   **Note:** `fullName` builds an array with both names, drops the empty ones with `.filter(name => name.length > 0)`, then glues whatever is left with `.join(" ")`. `.join(" ")` places its argument between every pair of array elements, so two elements get exactly one space between them, and one element gets none, there is no pair to separate. It always works off the already-trimmed fields from the constructor, so it never has to deal with stray spaces itself. `·` again marks the one space `.join(" ")` inserts:

   | `_firstName` | `_lastName` | after `.filter(...)` | `fullName` |
   |---|---|---|---|
   | `"Ada"` | `"Lovelace"` | `["Ada", "Lovelace"]` | `"Ada·Lovelace"` |
   | `"Ada"` | `""` | `["Ada"]` | `"Ada"` |
   | `""` | `""` | `[]` | `""` |

   **Note:** no commit here, `PersonName` works but is undocumented, the next step adds that.

5. **Add `PersonName`'s doc comments.** It does not change again after this, so it gets its doc comments now, right after the last method, not spread across the steps that added each one.

   <details>
   <summary>src/shared/domain/model/person-name.value-object.js (Full file with doc comments)</summary>

   ```javascript
   /**
    * Represents a person's name as a Value Object.
    */
   export class PersonName {
       /**
        * The first name of the person.
        * @type {string}
        * @private
        */
       _firstName;

       /**
        * The last name of the person.
        * @type {string}
        * @private
        */
       _lastName;

       /**
        * Creates a new PersonName instance.
        * @param {string} firstName - The first name.
        * @param {string} lastName - The last name.
        */
       constructor(firstName, lastName) {
           const trimmedFirstName = firstName?.trim() || "";
           const trimmedLastName = lastName?.trim() || "";
           this._firstName = trimmedFirstName;
           this._lastName = trimmedLastName;
       }

       /**
        * Gets the first name.
        * @returns {string}
        */
       get firstName() {
           return this._firstName;
       }

       /**
        * Gets the last name.
        * @returns {string} The last name.
        */
       get lastName() {
           return this._lastName;
       }

       /**
        * Gets the full name.
        * @returns {string} The full name, which is a combination of first and last names.
        */
       get fullName() {
           return [this._firstName, this._lastName].filter(name => name.length > 0).join(" ");
       }

       /**
        * Compares this PersonName with another for equality.
        * @param {PersonName} other - The other PersonName to compare.
        * @returns {boolean} true if the PersonNames are equal, false otherwise.
        */
       equals(other) {
           return other instanceof PersonName &&
               this._firstName === other.firstName &&
               this._lastName === other.lastName;
       }

       /**
        * Checks if both first and last names are present.
        * @returns {boolean} true if both names are present, false otherwise.
        */
       isValid() {
           return this.isFullyNamed();
       }

       /**
        * Checks if both first and last names are present.
        * @returns {boolean} true if both names are present, false otherwise.
        */
       isFullyNamed() {
           return this._firstName.length > 0 && this._lastName.length > 0;
       }
   }
   ```
   </details>

   ```
   git add .
   git commit -m "feat(shared): add PersonName value object."
   ```

6. **Add the `uuid` dependency.** `DeveloperId` needs time-ordered identifiers. Add the package the proper way, from the terminal, not by hand-editing `package.json`:

   ```
   npm install uuid
   ```

   **Note:** this is a runtime dependency (the domain calls it directly, not just in tests), so it belongs in `dependencies`, which is where a plain `npm install <package>` puts it.

7. **Create the shared `uuid` utility.** Right-click `src` → `New` → `JavaScript File` → type `shared/domain/uuid` → Enter (WebStorm adds the `.js`). It wraps the `uuid` package so the rest of the code never names a UUID version or calls the library directly.

   <details>
   <summary>src/shared/domain/uuid.js</summary>

   ```javascript
   import {v7 as uuidv7, validate as uuidIsValid, version as getUUIDVersion} from 'uuid';

   const UUID_VERSION_7 = 7;

   /**
    * Generates a new UUID v7.
    * @returns {string | Uint8Array} The generated UUID v7.
    */
   export const generateUUID = () => {
     return uuidv7();
   };

   /**
    * Checks if a given string is a valid UUID.
    * @param {string} uuid - The UUID to validate.
    * @returns {boolean} true if the UUID is valid, false otherwise.
    */
   export const isValidUUID = (uuid) => {
     return uuidIsValid(uuid) && getUUIDVersion(uuid) === UUID_VERSION_7;
   }
   ```
   </details>

   **Note:** `isValidUUID` checks two things, not one: that the string parses as a UUID at all, and that it is specifically version 7. A syntactically valid v4 UUID from somewhere else in a real system would fail this check, on purpose. `UUID_VERSION_7` names that `7`, no magic number in the comparison.

   ```
   git add .
   git commit -m "feat(shared): add uuid generation utility."
   ```

8. **Create `DeveloperId`, undocumented.** Right-click `src` → `New` → `JavaScript File` → type `greetings/domain/model/developer-id.value-object` → Enter (WebStorm adds the `.js`). Fields, constructor, accessors, the factory, and equality, all in one step, same as `PersonName`. Unlike `PersonName`, this constructor throws: a `DeveloperId` that exists at all is guaranteed to wrap a well-formed UUID v7. `build()` is the only way this project ever creates one from scratch; the constructor stays available for reconstructing one from a known-good stored value.

   <details>
   <summary>src/greetings/domain/model/developer-id.value-object.js (so far)</summary>

   ```javascript
   import {generateUUID, isValidUUID} from "../../../shared/domain/uuid.js";

   export class DeveloperId {
       _value;

       constructor(value) {
           if (!isValidUUID(value)) {
               throw new Error(`Invalid UUID: ${value}`);
           }
           this._value = value;
       }

       get value() {
           return this._value;
       }

       static build() {
           return new DeveloperId(generateUUID());
       }

       equals(other) {
           return other instanceof DeveloperId && this._value === other.value;
       }

       toString() {
           return this._value;
       }
   }
   ```
   </details>

   **Note:** no commit here, `DeveloperId` works but is undocumented, the next step adds that.

9. **Add `DeveloperId`'s doc comments.**

   <details>
   <summary>src/greetings/domain/model/developer-id.value-object.js (Full file with doc comments)</summary>

   ```javascript
   import {generateUUID, isValidUUID} from "../../../shared/domain/uuid.js";

   /**
    * Represents a Universally Unique Identifier (UUID) as a Value Object.
    */
   export class DeveloperId {

       /**
        * The UUID string value.
        * @type {string}
        * @private
        */
       _value;

       /**
        * Creates a new DeveloperId instance.
        * @param {string} value - The UUID string.
        * @throws {Error} If the value is not a valid UUID.
        */
       constructor(value) {
           if (!isValidUUID(value)) {
               throw new Error(`Invalid UUID: ${value}`);
           }
           this._value = value;
       }

       /**
        * Gets the UUID string value.
        * @returns {string}
        */
       get value() {
           return this._value;
       }

       /**
        * Generates a new random UUID v7.
        * @returns {DeveloperId}
        */
       static build() {
           return new DeveloperId(generateUUID());
       }

       /**
        * Compares this DeveloperId with another for equality.
        * @param {DeveloperId} other - The other DeveloperId to compare.
        * @returns {boolean}
        */
       equals(other) {
           return other instanceof DeveloperId && this._value === other.value;
       }

       /**
        * Returns the string representation of the UUID.
        * @returns {string}
        */
       toString() {
           return this._value;
       }
   }
   ```
   </details>

   ```
   git add .
   git commit -m "feat(greetings): add DeveloperId value object."
   ```

10. **Add `Developer`'s name accessors.**

    <details>
    <summary>src/greetings/domain/model/developer.entity.js (name accessors)</summary>

    ```javascript
    get name() {
        return this._name;
    }

    get fullName() {
        return this._name ? this._name.fullName : "Unknown";
    }
    ```
    </details>

11. **Add `Developer`'s identity accessors and the registration checks.** Every method the entity needs, together, still undocumented.

    <details>
    <summary>src/greetings/domain/model/developer.entity.js (so far)</summary>

    ```javascript
    import {PersonName} from "../../../shared/domain/model/person-name.value-object.js";
    import {DeveloperId} from "./developer-id.value-object.js";

    export class Developer {
        _id;
        _name;

        constructor(firstName, lastName) {
            const providedName = new PersonName(firstName, lastName);
            this._id = providedName.isValid() ? DeveloperId.build() : null;
            this._name = providedName;
        }

        get name() {
            return this._name;
        }

        get fullName() {
            return this._name ? this._name.fullName : "Unknown";
        }

        isRegisterable() {
            return this._name ? this._name.isValid() : false;
        }

        get id() {
            return this._id;
        }

        isIdentified() {
            return this._id !== null;
        }
    }
    ```
    </details>

    **Note:** `isRegisterable()` and `isIdentified()` read as the same thing from two angles, one asks the name ("is this a full name?"), the other asks the entity's own state ("did this instance get an ID?"). They agree by construction, since the constructor assigns the ID exactly when the name is valid. Components read whichever question fits what they are asking.

    **Note:** no commit here, `Developer` works but is undocumented, the next step adds that.

12. **Add `Developer`'s doc comments.**

    <details>
    <summary>src/greetings/domain/model/developer.entity.js (Full file with doc comments)</summary>

    ```javascript
    import {PersonName} from "../../../shared/domain/model/person-name.value-object.js";
    import {DeveloperId} from "./developer-id.value-object.js";

    /**
     * Represents a Developer entity with a unique identifier and a name.
     */
    export class Developer {

        /**
         * @type {DeveloperId|null}
         * @private
         */
        _id;

        /**
         * @type {PersonName}
         * @private
         */
        _name;

        /**
         * Creates a new Developer instance.
         * @param {string} firstName - The developer's first name.
         * @param {string} lastName - The developer's last name.
         */
        constructor(firstName, lastName) {
            const providedName = new PersonName(firstName, lastName);
            this._id = providedName.isValid() ? DeveloperId.build() : null;
            this._name = providedName;
        }


        /**
         * Gets the developer's name.
         * @returns {PersonName}
         */
        get name() {
            return this._name;
        }

        /**
         * Gets the developer's full name, or "Unknown" if missing.
         * @returns {string}
         */
        get fullName() {
            return this._name ? this._name.fullName : "Unknown";
        }

        /**
         * Checks if the developer is registerable (has a full name).
         * @returns {boolean}
         */
        isRegisterable() {
            return this._name ? this._name.isValid() : false;
        }


        /**
         * Gets the developer's ID.
         * @returns {DeveloperId|null}
         */
        get id() {
            return this._id;
        }

        /**
         * Checks if the developer is identified.
         * @returns {boolean}
         */
        isIdentified() {
            return this._id !== null;
        }

    }
    ```
    </details>

    ```
    git add .
    git commit -m "feat(developer): add developer entity."
    ```

13. **Create the `DeveloperRegistration` component, script only for now.** Right-click `src` → `New` → `Vue Single-File Component`. A dropdown asks `Composition API` or `Options API`, pick `Composition API`. Type the full path starting from the bounded context, `greetings/presentation/components/developer-registration` → Enter, WebStorm creates the folders and adds the `.vue` extension. Start with the form's local state and the events it can emit. Only `developer-registered` for now, `registration-deferred` joins in US004.

    <details>
    <summary>src/greetings/presentation/components/developer-registration.vue (so far)</summary>

    ```vue
    <script setup>
    import {ref} from 'vue';

    const firstName = ref("");
    const lastName = ref("");
    const errorMessage = ref("");

    const emit = defineEmits(['developer-registered']);
    </script>

    <template>
    </template>

    <style scoped>
    </style>
    ```
    </details>

    **Note:** no `import` for `Developer` yet, nothing in this step uses it, that import joins in step 15 once `submitRegistrationRequest()` actually needs it.

    **Note:** `ref('')` holds a plain, writable reactive value, `firstName.value` reads it, `firstName.value = x` (or, from a template, `v-model`) writes it. `defineEmits([...])` declares the events a component may emit and is itself a compiler macro, it needs no import.

14. **Add `clearFields()`.** A small helper, reused by the submit handler now and by the Later and Clear buttons in later stories.

    <details>
    <summary>src/greetings/presentation/components/developer-registration.vue (clearFields)</summary>

    ```javascript
    function clearFields() {
      firstName.value = "";
      lastName.value = "";
      errorMessage.value = "";
    }
    ```
    </details>

15. **Add `submitRegistrationRequest()`.** Builds a `Developer` from the current input and lets the entity itself decide whether that counts as a registration.

    <details>
    <summary>src/greetings/presentation/components/developer-registration.vue (submitRegistrationRequest)</summary>

    ```javascript
    function submitRegistrationRequest() {
      const developer = new Developer(firstName.value, lastName.value);
      if (developer.isRegisterable()) {
        emit("developer-registered", { developer });
        clearFields();
        errorMessage.value = "";
      } else {
        errorMessage.value = "Please provide both first name and last name.";
      }
    }
    ```
    </details>

    **Note:** the component never re-implements the "both names required" rule, it asks `developer.isRegisterable()`, the same question `Developer` answers everywhere else in the app. The event payload is `{ developer }`, the whole entity, not raw strings, so whoever listens gets the ID and the validated name for free.

16. **Write `DeveloperRegistration`'s template.** A heading, the form with both inputs bound two-way with `v-model`, a **Register** button, and the error message. This is a checkpoint, not the final file, the `Later` and `Clear` buttons join `.actions` in US004 and US005, and the doc comments only go on once the component is complete.

    <details>
    <summary>src/greetings/presentation/components/developer-registration.vue (template)</summary>

    ```vue
    <template>
      <!-- Developer Registration Form -->
      <div>
        <h2>New Developer</h2>
        <div>
          <form @submit.prevent="submitRegistrationRequest">
            <div class="field">
              <label for="firstName">First Name:</label><input id="first-name" v-model="firstName" type="text"/>
            </div>
            <div class="field">
              <label for="lastName">Last Name:</label><input id="last-name" v-model="lastName" type="text"/>
            </div>
            <div class="actions">
              <button type="submit">Register</button>
            </div>
          </form>
          <p v-if="errorMessage" class="error" role="alert">{{ errorMessage }}</p>
        </div>
      </div>
    </template>
    ```
    </details>

    **Note:** no commit here, there is no `<style scoped>` yet, the next step adds that.

17. **Write `DeveloperRegistration`'s styles.**

    <details>
    <summary>src/greetings/presentation/components/developer-registration.vue (styles)</summary>

    ```vue
    <style scoped>
    button {
      margin-right: 10px;
      padding: 8px 16px;
      cursor: pointer;
    }

    .error {
      color: #d32f2f;
      margin-top: 10px;
      font-size: 14px;
    }

    .field {
      margin-bottom: 10px;
    }

    .actions {
      margin-top: 10px;
    }

    label {
      margin-right: 5px;
    }
    </style>
    ```
    </details>

    **Note:** `scoped` on `<style>` makes these rules apply only to this component's own template, not to every `button` or `label` anywhere in the app. Vue does this by tagging each element this component renders with a unique attribute at build time, and rewriting the selectors above to match only elements carrying that attribute. Without `scoped`, `button { ... }` here would style every button in the whole application.

    **Note:** these rules are generic (`button`, `.field`, `.actions`, `.error`, `label`), not written per button. They already cover the Later and Clear buttons that show up in later stories, so this stylesheet is never touched again.

18. **Put `DeveloperRegistration` together.**

    <details>
    <summary>src/greetings/presentation/components/developer-registration.vue (so far)</summary>

    ```vue
    <script setup>
    import {ref} from 'vue';
    import {Developer} from "../../domain/model/developer.entity.js";

    const firstName = ref("");
    const lastName = ref("");
    const errorMessage = ref("");

    const emit = defineEmits(['developer-registered']);

    function clearFields() {
      firstName.value = "";
      lastName.value = "";
      errorMessage.value = "";
    }

    function submitRegistrationRequest() {
      const developer = new Developer(firstName.value, lastName.value);
      if (developer.isRegisterable()) {
        emit("developer-registered", { developer });
        clearFields();
        errorMessage.value = "";
      } else {
        errorMessage.value = "Please provide both first name and last name.";
      }
    }
    </script>

    <template>
      <!-- Developer Registration Form -->
      <div>
        <h2>New Developer</h2>
        <div>
          <form @submit.prevent="submitRegistrationRequest">
            <div class="field">
              <label for="firstName">First Name:</label><input id="first-name" v-model="firstName" type="text"/>
            </div>
            <div class="field">
              <label for="lastName">Last Name:</label><input id="last-name" v-model="lastName" type="text"/>
            </div>
            <div class="actions">
              <button type="submit">Register</button>
            </div>
          </form>
          <p v-if="errorMessage" class="error" role="alert">{{ errorMessage }}</p>
        </div>
      </div>
    </template>

    <style scoped>
    button {
      margin-right: 10px;
      padding: 8px 16px;
      cursor: pointer;
    }

    .error {
      color: #d32f2f;
      margin-top: 10px;
      font-size: 14px;
    }

    .field {
      margin-bottom: 10px;
    }

    .actions {
      margin-top: 10px;
    }

    label {
      margin-right: 5px;
    }
    </style>
    ```
    </details>

    **Note:** `import {Developer}` finally joins here, this is the first point where all the pieces are shown together and `submitRegistrationRequest()` genuinely needs it to run.

    ```
    git add .
    git commit -m "feat(developer-registration): add registration form."
    ```

19. **Add `app.vue`'s script.** `app.vue` is small, so the whole `<script setup>` block goes in one step: the state and the handler together.

    <details>
    <summary>src/app.vue (script)</summary>

    ```vue
    <script setup>
    import DeveloperRegistration from "./greetings/presentation/components/developer-registration.vue";
    import {ref} from "vue";

    const registeredDeveloper = ref(null);

    function updateRegisteredDeveloperInfo(payload) {
      registeredDeveloper.value = payload.developer;
    }
    </script>
    ```
    </details>

    **Note:** no commit here, the template does not use any of this yet.

20. **Add `app.vue`'s template.**

    <details>
    <summary>src/app.vue (template)</summary>

    ```vue
    <template>
      <h1>Hello Vue Developer Application</h1>
      <developer-registration
          @developer-registered="updateRegisteredDeveloperInfo"
      />
    </template>
    ```
    </details>

    **Note:** no commit here, the two pieces are not shown together yet.

21. **Put `app.vue` together.** Nothing visible acknowledges the registration yet beyond the form clearing, the greeting is US002.

    <details>
    <summary>src/app.vue (so far)</summary>

    ```vue
    <script setup>
    import DeveloperRegistration from "./greetings/presentation/components/developer-registration.vue";
    import {ref} from "vue";

    const registeredDeveloper = ref(null);

    function updateRegisteredDeveloperInfo(payload) {
      registeredDeveloper.value = payload.developer;
    }
    </script>

    <template>
      <h1>Hello Vue Developer Application</h1>
      <developer-registration
          @developer-registered="updateRegisteredDeveloperInfo"
      />
    </template>

    <style>
    </style>
    ```
    </details>

    ```
    git add .
    git commit -m "feat(app): add state management for developer registration."
    ```

22. **Run it.** `npm run dev`, open `http://localhost:5173/`. Register "Jane" and "Smith": the form clears with no error. Try just "Jane", or two fields of spaces, or nothing at all: the error message appears and nothing is emitted. Stop the server with `Ctrl+C`.

23. **Publish and finish the feature.** Git Flow Helper widget → `Feature` → `Feature Publish`, then → `Feature Finish` (`Integrate Immediately`, `Keep remote branch when finished` unchecked). Merges into `develop` and pushes it too.

---

## Greet the Last Registered Developer ([US002](./user-stories.md))

Once someone registers, the app greets them by name and shows their ID. Before that, it says nothing.

1. **Start the feature.** Git Flow Helper widget → `Feature` → `Feature Start` → **Feature description** `greet-registered-developer` → `OK`. Creates and switches you to `feature/greet-registered-developer`.

2. **Create the `DeveloperGreeting` component, props and the computed greeting.** Right-click `src` → `New` → `Vue Single-File Component`. `Composition API` in the dropdown, then type the full path starting from the bounded context, `greetings/presentation/components/developer-greeting` → Enter. It takes one required prop, the `Developer` to greet, and derives the message from it.

   <details>
   <summary>src/greetings/presentation/components/developer-greeting.vue (so far)</summary>

   ```vue
   <script lang="js" setup>
   import {Developer} from "../../domain/model/developer.entity.js";
   import {computed} from "vue";

   const { developer } = defineProps({
     developer: {
       type: Developer,
       required: true,
     },
   });

   const greeting = computed(() => {
     return (developer && developer.isRegisterable())
         ? `Congrats ${developer.fullName}! Now you are a Vue Developer, identified by ID: ${developer.id}`
         : "Welcome Anonymous Developer";
   });
   </script>

   <template>
   </template>

   <style scoped>
   </style>
   ```
   </details>

   **Note:** `const { developer } = defineProps({ ... })` is Vue 3.5's **reactive props destructuring**: `developer` stays reactive even though it was pulled out of the object `defineProps()` returned, there is no need to write `props.developer` everywhere. ADR-0002 in `## Document the Project`.

   **Note:** `${developer.id}` inside the template string calls `DeveloperId`'s own `toString()`, so the raw UUID prints, not `[object Object]`.

3. **Add `DeveloperGreeting`'s template.**

   <details>
   <summary>src/greetings/presentation/components/developer-greeting.vue (template)</summary>

   ```vue
   <template>
     <p>{{ greeting }}</p>
   </template>
   ```
   </details>

   **Note:** no commit here, `DeveloperGreeting` is complete but undocumented, the next step adds that.

4. **Put `DeveloperGreeting` together, with its doc comments.** It does not change again after this, so it gets its doc comments now.

   <details>
   <summary>src/greetings/presentation/components/developer-greeting.vue (Full file with doc comments)</summary>

   ```vue
   <script lang="js" setup>
   /**
    * DeveloperGreeting component
    *
    * @component
    * @name developer-greeting
    * @description
    * Greets a developer based on their name. If both names are empty, displays 'Welcome Anonymous Developer'.
    * Otherwise, congratulates the developer by their full name.
    *
    * @prop {Developer} developer - The developer entity to greet.
    *
    * @example
    * <developer-greeting :developer="developerInstance" />
    * // Renders: Congrats Ada Lovelace! Now you are a Vue Developer
    *
    * <developer-greeting />
    * // Renders: Welcome Anonymous Developer
    */
   import {Developer} from "../../domain/model/developer.entity.js";
   import {computed} from "vue";

   const { developer } = defineProps({
     developer: {
       type: Developer,
       required: true,
     },
   });

   /**
    * Computes the greeting message for the developer.
    * @type {import('vue').ComputedRef<string>}
    */
   const greeting = computed(() => {
     return (developer && developer.isRegisterable())
         ? `Congrats ${developer.fullName}! Now you are a Vue Developer, identified by ID: ${developer.id}`
         : "Welcome Anonymous Developer";
   });
   </script>

   <template>
     <p>{{ greeting }}</p>
   </template>

   <style scoped>
   </style>
   ```
   </details>

   ```
   git add .
   git commit -m "feat(developer-greeting): add developer greeting component."
   ```

5. **Show the greeting in `app.vue`.** Track whether anyone has registered yet, and only mount `<developer-greeting>` once they have, before the first registration, the component would otherwise render with no valid `developer` prop at all.

   <details>
   <summary>src/app.vue (so far)</summary>

   ```vue
   <script setup>
   import DeveloperRegistration from "./greetings/presentation/components/developer-registration.vue";
   import DeveloperGreeting from "./greetings/presentation/components/developer-greeting.vue";
   import {ref} from "vue";

   const registeredDeveloper = ref(null);
   const hasRegistered = ref(false);

   function updateRegisteredDeveloperInfo(payload) {
     registeredDeveloper.value = payload.developer;
     hasRegistered.value = true;
   }
   </script>

   <template>
     <h1>Hello Vue Developer Application</h1>
     <developer-registration
         @developer-registered="updateRegisteredDeveloperInfo"
     />
     <developer-greeting v-if="hasRegistered" :developer="registeredDeveloper"/>
   </template>

   <style>
   </style>
   ```
   </details>

   **Note:** `v-if="hasRegistered"` removes `<developer-greeting>` from the DOM entirely until it is true, rather than rendering it with `developer` pointing at nothing. `hasRegistered` starts `false` and only this one line ever sets it `true`.

   ```
   git add .
   git commit -m "feat(app): wire developer greeting into the app."
   ```

6. **Run it.** `npm run dev`. On load, nothing renders below the form (no greeting at all). Register "Ada" and "Lovelace": "Congrats Ada Lovelace! Now you are a Vue Developer, identified by ID: ..." appears, with a UUID. Stop the server with `Ctrl+C`.

7. **Publish and finish the feature.** Git Flow Helper widget → `Feature` → `Feature Publish`, then → `Feature Finish` (`Integrate Immediately`, `Keep remote branch when finished` unchecked).

---

## Track Valid Registrations ([US003](./user-stories.md))

A running count of how many developers have registered with a valid name, shown next to the greeting.

1. **Start the feature.** Git Flow Helper widget → `Feature` → `Feature Start` → **Feature description** `track-developer-registrations` → `OK`. Creates and switches you to `feature/track-developer-registrations`.

2. **Create the `DeveloperCountShow` component, props only.** Right-click `src` → `New` → `Vue Single-File Component`. `Composition API` in the dropdown, then type the full path starting from the bounded context, `greetings/presentation/components/developer-count-show` → Enter. One required prop, no local state.

   <details>
   <summary>src/greetings/presentation/components/developer-count-show.vue (so far)</summary>

   ```vue
   <script setup>
   const { developerCount } = defineProps({
     developerCount: {
       type: Number,
       required: true,
     },
   });
   </script>

   <template>
   </template>

   <style scoped>
   </style>
   ```
   </details>

   **Note:** no commit here, `DeveloperCountShow` is undocumented and has no template yet.

3. **Add `DeveloperCountShow`'s template.**

   <details>
   <summary>src/greetings/presentation/components/developer-count-show.vue (template)</summary>

   ```vue
   <template>
     <div>
       <p>Developers registered: {{ developerCount }}</p>
       <p><b>Note:</b> Unknown developers are not considered.</p>
     </div>
   </template>
   ```
   </details>

   **Note:** no commit here, `DeveloperCountShow` works but is undocumented, the next step adds that.

4. **Put `DeveloperCountShow` together, with its doc comments.**

   <details>
   <summary>src/greetings/presentation/components/developer-count-show.vue (Full file with doc comments)</summary>

   ```vue
   <script lang="js" setup>
   /**
    * DeveloperCountShow component
    *
    * @component
    * @name developer-count-show
    * @description
    * Displays the number of registered developers. Unknown developers are not considered in the count.
    *
    * @prop {number} developerCount - The number of developers registered.
    *
    * @example
    * <developer-count-show :developer-count="5" />
    * // Renders: Developers registered: 5
    */
   const { developerCount } = defineProps({
     developerCount: {
       type: Number,
       required: true,
     },
   });
   </script>

   <template>
     <div>
       <p>Developers registered: {{ developerCount }}</p>
       <p><b>Note:</b> Unknown developers are not considered.</p>
     </div>
   </template>

   <style scoped>
   </style>
   ```
   </details>

   ```
   git add .
   git commit -m "feat(developer-count): add developer count show component."
   ```

5. **Count registrations in `app.vue`.** `updateDeveloperCount()` only increments for a developer that is actually registerable, an invalid attempt never reaches this counter because `DeveloperRegistration` never emits for one.

   <details>
   <summary>src/app.vue (so far)</summary>

   ```vue
   <script setup>
   import DeveloperRegistration from "./greetings/presentation/components/developer-registration.vue";
   import DeveloperGreeting from "./greetings/presentation/components/developer-greeting.vue";
   import DeveloperCountShow from "./greetings/presentation/components/developer-count-show.vue";
   import {ref} from "vue";

   const registeredDeveloper = ref(null);
   const developerCount = ref(0);
   const hasRegistered = ref(false);

   function updateRegisteredDeveloperInfo(payload) {
     registeredDeveloper.value = payload.developer;
     hasRegistered.value = true;
     updateDeveloperCount(payload.developer);
   }

   function updateDeveloperCount(developer) {
     if (developer.isRegisterable()) {
       developerCount.value++;
     }
   }
   </script>

   <template>
     <h1>Hello Vue Developer Application</h1>
     <developer-registration
         @developer-registered="updateRegisteredDeveloperInfo"
     />
     <developer-greeting v-if="hasRegistered" :developer="registeredDeveloper"/>
     <developer-count-show :developer-count="developerCount"/>
   </template>

   <style>
   </style>
   ```
   </details>

   **Note:** `updateDeveloperCount` takes the already-built `Developer` from the event payload, it never re-parses `firstName`/`lastName`, there is only ever one place (the entity's own constructor) that turns raw input into a `Developer`. No `import {Developer}` here, nothing in this plain code references the class itself, only the JSDoc-typed parameter in the final documented version needs it, that import joins there.

   ```
   git add .
   git commit -m "feat(app): add developer count tracking."
   ```

6. **Run it.** `npm run dev`. Register two valid developers: the count reaches 2. Attempt a registration with only one name: it is rejected by US001's validation before it ever reaches the counter. Stop the server with `Ctrl+C`.

7. **Publish and finish the feature.** Git Flow Helper widget → `Feature` → `Feature Publish`, then → `Feature Finish` (`Integrate Immediately`, `Keep remote branch when finished` unchecked).

---

## Defer Registration ([US004](./user-stories.md))

A **Later** button lets the visitor drop a pending registration: the form clears and the app goes back to the anonymous state, even if a developer was registered before.

1. **Start the feature.** Git Flow Helper widget → `Feature` → `Feature Start` → **Feature description** `defer-registration` → `OK`. Creates and switches you to `feature/defer-registration`.

2. **Add the deferral event and its handler to `DeveloperRegistration`.**

   <details>
   <summary>src/greetings/presentation/components/developer-registration.vue (deferRegistration)</summary>

   ```javascript
   function deferRegistration() {
     emit("registration-deferred", {developer: null});
     clearFields();
     errorMessage.value = "";
   }
   ```
   </details>

   Add `'registration-deferred'` to the `defineEmits([...])` array.

   **Note:** the payload is `{ developer: null }`, on purpose. It has the same shape as `developer-registered`'s payload (an object with a `developer` key), so both handlers in `app.vue` can stay simple, but the value says "there is no developer", which is exactly what deferring means.

3. **Add the `Later` button to `DeveloperRegistration`'s template.** It grows again in US005 (the `Clear` button), so this is a checkpoint, not the final file, still without doc comments.

   <details>
   <summary>src/greetings/presentation/components/developer-registration.vue (template)</summary>

   ```vue
   <template>
     <!-- Developer Registration Form -->
     <div>
       <h2>New Developer</h2>
       <div>
         <form @submit.prevent="submitRegistrationRequest">
           <div class="field">
             <label for="firstName">First Name:</label><input id="first-name" v-model="firstName" type="text"/>
           </div>
           <div class="field">
             <label for="lastName">Last Name:</label><input id="last-name" v-model="lastName" type="text"/>
           </div>
           <div class="actions">
             <button type="submit">Register</button>
             <button type="button" @click="deferRegistration">Later</button>
           </div>
         </form>
         <p v-if="errorMessage" class="error" role="alert">{{ errorMessage }}</p>
       </div>
     </div>
   </template>
   ```
   </details>

   **Note:** no changes to `<script setup>` or `<style scoped>` here, only the template grows a second button.

   ```
   git add .
   git commit -m "feat(developer-registration): add defer registration handler and later button."
   ```

4. **Add `resetRegisteredDeveloperInfo()` and wire the deferral event in `app.vue`.** Reset the state to exactly what it was before any registration.

   <details>
   <summary>src/app.vue (so far)</summary>

   ```vue
   <script setup>
   import DeveloperRegistration from "./greetings/presentation/components/developer-registration.vue";
   import DeveloperGreeting from "./greetings/presentation/components/developer-greeting.vue";
   import DeveloperCountShow from "./greetings/presentation/components/developer-count-show.vue";
   import {ref} from "vue";

   const registeredDeveloper = ref(null);
   const developerCount = ref(0);
   const hasRegistered = ref(false);

   function updateRegisteredDeveloperInfo(payload) {
     registeredDeveloper.value = payload.developer;
     hasRegistered.value = true;
     updateDeveloperCount(payload.developer);
   }

   function resetRegisteredDeveloperInfo() {
     registeredDeveloper.value = null;
     hasRegistered.value = false;
   }

   function updateDeveloperCount(developer) {
     if (developer.isRegisterable()) {
       developerCount.value++;
     }
   }
   </script>

   <template>
     <h1>Hello Vue Developer Application</h1>
     <developer-registration
         @developer-registered="updateRegisteredDeveloperInfo"
         @registration-deferred="resetRegisteredDeveloperInfo"
     />
     <developer-greeting v-if="hasRegistered" :developer="registeredDeveloper"/>
     <developer-count-show :developer-count="developerCount"/>
   </template>

   <style>
   </style>
   ```
   </details>

   **Note:** `resetRegisteredDeveloperInfo` does not touch `developerCount`. Deferring resets the current greeting, not the running total, US003's count only ever goes up, it tracks how many valid registrations happened, not how many are currently "active".

   **Note:** no commit here, `app.vue` is undocumented, the next step adds that.

5. **Put `app.vue` together, with its doc comments.** `app.vue` does not change again after this, this is the final file.

   <details>
   <summary>src/app.vue (Full file with doc comments)</summary>

   ```vue
   <script lang="js" setup>
   /**
    * App component
    *
    * @component
    * @name app
    * @description
    * The main application part. Manages developer registration, greeting, and count.
    * Handles events from DeveloperRegistration and updates state accordingly.
    *
    * @example
    * <app />
    */
   import DeveloperRegistration from "./greetings/presentation/components/developer-registration.vue";
   import DeveloperGreeting from "./greetings/presentation/components/developer-greeting.vue";
   import DeveloperCountShow from "./greetings/presentation/components/developer-count-show.vue";
   import {Developer} from "./greetings/domain/model/developer.entity.js";
   import {ref} from "vue";

   /**
    * The registered developer entity.
    * @type {import('vue').Ref<Developer|null>}
    */
   const registeredDeveloper = ref(null);

   /**
    * The number of developers registered (excluding unknown developers).
    * @type {import('vue').Ref<number>}
    */
   const developerCount = ref(0);

   /**
    * Whether a developer has registered.
    * @type {import('vue').Ref<boolean>}
    */
   const hasRegistered = ref(false);

   /**
    * Handles the 'developer-registered' event. Updates developer info and count.
    * @param {{ developer: Developer }} payload - The registered developer payload.
    * @returns {void}
    */
   function updateRegisteredDeveloperInfo(payload) {
     registeredDeveloper.value = payload.developer;
     hasRegistered.value = true;
     updateDeveloperCount(payload.developer);
   }

   /**
    * Handles the 'registration-deferred' event. Resets developer info.
    * @returns {void}
    */
   function resetRegisteredDeveloperInfo() {
     registeredDeveloper.value = null;
     hasRegistered.value = false;
   }

   /**
    * Increments the developer count if the developer is not 'Unknown'.
    * @param {Developer} developer - The developer to check.
    * @returns {void}
    */
   function updateDeveloperCount(developer) {
     if (developer.isRegisterable()) {
       developerCount.value++;
     }
   }
   </script>

   <template>
     <h1>Hello Vue Developer Application</h1>
     <developer-registration
         @developer-registered="updateRegisteredDeveloperInfo"
         @registration-deferred="resetRegisteredDeveloperInfo"
     />
     <developer-greeting v-if="hasRegistered" :developer="registeredDeveloper"/>
     <developer-count-show :developer-count="developerCount"/>
   </template>

   <style>
   </style>
   ```
   </details>

   ```
   git add .
   git commit -m "feat(app): reset to anonymous on deferred registration."
   ```

6. **Run it.** `npm run dev`. Register "Jane Smith", then type "John" and "Doe" and click **Later**: the form clears, the greeting and count disappear (back to the pre-registration state), and no error shows. Stop the server with `Ctrl+C`.

7. **Publish and finish the feature.** Git Flow Helper widget → `Feature` → `Feature Publish`, then → `Feature Finish` (`Integrate Immediately`, `Keep remote branch when finished` unchecked).

---

## Clear the Registration Form ([US005](./user-stories.md))

A **Clear** button empties the inputs without touching the current greeting or count: no event reaches the parent, so a registered developer stays greeted and an anonymous one stays anonymous. `clearFields()` already exists from US001, so this feature is template-only.

1. **Start the feature.** Git Flow Helper widget → `Feature` → `Feature Start` → **Feature description** `clear-registration-form` → `OK`. Creates and switches you to `feature/clear-registration-form`.

2. **Add the `Clear` button to `DeveloperRegistration`'s template.**

   <details>
   <summary>src/greetings/presentation/components/developer-registration.vue (template)</summary>

   ```vue
   <template>
     <!-- Developer Registration Form -->
     <div>
       <h2>New Developer</h2>
       <div>
         <form @submit.prevent="submitRegistrationRequest">
           <div class="field">
             <label for="firstName">First Name:</label><input id="first-name" v-model="firstName" type="text"/>
           </div>
           <div class="field">
             <label for="lastName">Last Name:</label><input id="last-name" v-model="lastName" type="text"/>
           </div>
           <div class="actions">
             <button type="submit">Register</button>
             <button type="button" @click="deferRegistration">Later</button>
             <button type="button" @click="clearFields">Clear</button>
           </div>
         </form>
         <p v-if="errorMessage" class="error" role="alert">{{ errorMessage }}</p>
       </div>
     </div>
   </template>
   ```
   </details>

   **Note:** `@click="clearFields"` calls the helper directly, no new emit. Nothing in `app.vue` ever hears about a clear, which is exactly why it leaves the current greeting and count untouched.

   **Note:** no commit here, `DeveloperRegistration` is undocumented, the next step adds that.

3. **Put `DeveloperRegistration` together, with its doc comments.** It does not change again after this, this is the finished file.

   <details>
   <summary>src/greetings/presentation/components/developer-registration.vue</summary>

   ```vue
   <script setup>
   /**
    * DeveloperRegistration component
    *
    * @component
    * @name developer-registration
    * @description
    * This component is responsible for registering a new developer. It provides buttons to register,
    * defer registration (later), or clear the form. It emits 'developer-registered' or 'registration-deferred' events
    * based on user actions. Shows feedback for invalid registration attempts.
    *
    * @example
    * <developer-registration @developer-registered="updateRegisteredDeveloperInfo" @registration-deferred="resetRegisteredDeveloperInfo" />
    */
   import {ref} from 'vue';
   import {Developer} from "../../domain/model/developer.entity.js";

   /**
    * The first name input for the developer registration form.
    * @type {import('vue').Ref<string>}
    */
   const firstName = ref("");

   /**
    * The last name input for the developer registration form.
    * @type {import('vue').Ref<string>}
    */
   const lastName = ref("");

   /**
    * The error message to display for invalid registration attempts.
    * @type {import('vue').Ref<string>}
    */
   const errorMessage = ref("");

   /**
    * Emits events for registration actions.
    * The 'developer-registered' event is emitted with the developer entity when registration is successful.
    * The 'registration-deferred' event is emitted with a null developer when the user chooses to defer registration.
    * @type {(event: 'developer-registered' | 'registration-deferred',
    * payload: { developer: Developer | null }) => void}
    */
   const emit = defineEmits(['developer-registered', 'registration-deferred']);

   /**
    * Handles the registration form submission. Emits 'developer-registered' if valid, otherwise sets an error message.
    * @returns {void}
    */
   function submitRegistrationRequest() {
     const developer = new Developer(firstName.value, lastName.value);
     if (developer.isRegisterable()) {
       emit("developer-registered", { developer });
       clearFields();
       errorMessage.value = "";
     } else {
       errorMessage.value = "Please provide both first name and last name.";
     }
   }

   /**
    * Defers the registration process by emitting 'registration-deferred' event with empty names. Clears the form and error message.
    * @returns {void}
    */
   function deferRegistration() {
     emit("registration-deferred", {developer: null});
     clearFields();
     errorMessage.value = "";
   }

   /**
    * Clears the input fields and error message.
    * @returns {void}
    */
   function clearFields() {
     firstName.value = "";
     lastName.value = "";
     errorMessage.value = "";
   }
   </script>

   <template>
     <!-- Developer Registration Form -->
     <div>
       <h2>New Developer</h2>
       <div>
         <form @submit.prevent="submitRegistrationRequest">
           <div class="field">
             <label for="firstName">First Name:</label><input id="first-name" v-model="firstName" type="text"/>
           </div>
           <div class="field">
             <label for="lastName">Last Name:</label><input id="last-name" v-model="lastName" type="text"/>
           </div>
           <div class="actions">
             <button type="submit">Register</button>
             <button type="button" @click="deferRegistration">Later</button>
             <button type="button" @click="clearFields">Clear</button>
           </div>
         </form>
         <p v-if="errorMessage" class="error" role="alert">{{ errorMessage }}</p>
       </div>
     </div>
   </template>

   <style scoped>
   button {
     margin-right: 10px;
     padding: 8px 16px;
     cursor: pointer;
   }

   .error {
     color: #d32f2f;
     margin-top: 10px;
     font-size: 14px;
   }

   .field {
     margin-bottom: 10px;
   }

   .actions {
     margin-top: 10px;
   }

   label {
     margin-right: 5px;
   }
   </style>
   ```
   </details>

   ```
   git add .
   git commit -m "feat(developer-registration): add clear button."
   ```

4. **Run it.** `npm run dev`. Register "Jane Smith", type a different name, click **Clear**: the fields empty and the greeting still reads "Congrats Jane Smith! ...". Do the same without registering first: the greeting stays absent. Stop the server with `Ctrl+C`.

5. **Publish and finish the feature.** Git Flow Helper widget → `Feature` → `Feature Publish`, then → `Feature Finish` (`Integrate Immediately`, `Keep remote branch when finished` unchecked).

---

## Prepare the First Release

**All of this happens on `develop`:** `Feature Finish` leaves you there. These are the last steps before tagging `1.0.0`: one end-to-end check, then the files a public repo needs. Every real public repo ships a `LICENSE.md`, a `README.md`, and a `CONTRIBUTING.md`, but none of them belonged at Project Setup, back then there was nothing to describe.

1. **Run all five scenarios end to end.** `npm run dev`, then in the browser: confirm nothing greets you on load; register a valid name and check the personalized greeting with the ID and the count reaching 1; try a one-name or spaces-only registration and check the error message, with the count unchanged; click **Later** and check the reset to anonymous; register again, type a new name, click **Clear**, and check the greeting and count held. Then check every scenario in `docs/user-stories.md` against what the app actually does. No automated test drives these end to end, so this manual run is the acceptance check. Stop the server with `Ctrl+C`.

2. **Add `LICENSE.md`.** Right-click the project root → `New` → `File` → type `LICENSE.md` → Enter. The README's badge links to it, so it goes in first.

   <details>
   <summary>LICENSE.md</summary>

   ```markdown
   # MIT License

   Copyright © 2026 Web Applications Development Team

   Permission is hereby granted, free of charge, to any person obtaining a copy
   of this software and associated documentation files (the "Software"), to deal
   in the Software without restriction, including without limitation the rights
   to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
   copies of the Software, and to permit persons to whom the Software is
   furnished to do so, subject to the following conditions:

   The above copyright notice and this permission notice shall be included in all
   copies or substantial portions of the Software.

   THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
   IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
   FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
   AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
   LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
   OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
   SOFTWARE.
   ```
   </details>

   ```
   git add .
   git commit -m "docs: add license."
   git push
   ```

3. **Add `README.md`.** Same way, right-click the project root → `New` → `File` → type `README.md` → Enter.

   <details>
   <summary>README.md</summary>

   ````markdown
   # Hello Vue Developer (`hello-vue-developer`)

   [![Vue Version](https://img.shields.io/badge/vue-3.5.42-4fc08d.svg)](https://vuejs.org/)
   [![Vite Version](https://img.shields.io/badge/vite-8.2.2-646cff.svg)](https://vitejs.dev/)
   [![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE.md)

   ## Overview
   A Vue.js application demonstrating core development concepts, including component architecture, reactive state management, and Domain-Driven Design (DDD) principles.

   **Author**: Web Applications Development Team

   ---

   ## Features
   - **Developer Registration**: Register with first and last names.
   - **Dynamic Greeting**: Personalized welcome messages for registered developers, including unique IDs.
   - **Engagement Tracking**: Real-time count of valid registrations.
   - **Flexible Workflow**: Option to defer registration, or clear the form without affecting the current greeting.
   - **Robust Validation**: Domain-level enforcement of name requirements.

   ## Technologies Used
   - **Framework**: [Vue.js 3.5+](https://vuejs.org/) (Composition API)
   - **Build Tool**: [Vite 8+](https://vitejs.dev/)
   - **Language**: JavaScript (ESNext)
   - **Styling**: Standard CSS (within SFCs)
   - **Identity**: [UUID v7](https://github.com/uuidjs/uuid)

   ## Architecture
   The project follows a **Domain-Driven Design (DDD)** inspired structure to separate concerns and ensure maintainability:

   - **`src/greetings`**: The primary Bounded Context.
       - **`domain`**: the `Developer` entity and the `DeveloperId` value object.
       - **`presentation`**: Vue components for the UI.
   - **`src/shared`**: Shared kernel.
       - **`domain`**: the `PersonName` value object and the `uuid` identity-generation utility.

   ## Documentation
   - **[User Stories](docs/user-stories.md)**: Detailed functional requirements and acceptance criteria.
   - **[Architecture Decision Records](docs/adrs.md)**: Log of key architectural decisions and their justifications.
   - **[Class Diagram](docs/class-diagram.puml)**: PlantUML visualization of the system architecture.
   - **[Changelog](CHANGELOG.md)**: History of notable changes following "Keep a Changelog" standards.

   ## Setup & Installation

   ### Prerequisites
   - **Node.js**: `v22` or higher
   - **npm**: `v11` or higher

   ### Getting Started
   1. **Clone the repository**:
      ```bash
      git clone <repository-url>
      cd hello-vue-developer
      ```

   2. **Install dependencies**:
      ```bash
      npm install
      ```

   3. **Launch development server**:
      ```bash
      npm run dev
      ```

   4. **Access the application**:
      Open [http://localhost:5173](http://localhost:5173) in your browser.

   ## Development Commands
   | Command           | Description                              |
   |:------------------|:------------------------------------------|
   | `npm run dev`     | Starts Vite development server with HMR. |
   | `npm run build`   | Builds the application for production.   |
   | `npm run preview` | Previews the production build locally.   |

   ## Contributing
   See [CONTRIBUTING.md](CONTRIBUTING.md).

   ## License
   MIT, see [LICENSE.md](LICENSE.md).
   ````
   </details>

   ```
   git add .
   git commit -m "docs: add readme."
   git push
   ```

4. **Add `CONTRIBUTING.md`,** linked from the README.

   <details>
   <summary>CONTRIBUTING.md</summary>

   ````markdown
   # Contributing to Hello Vue Developer

   Thank you for your interest in contributing to the **Hello Vue Developer** project! This document outlines the standards and workflows we follow to maintain high code quality and architectural integrity.

   ## Table of Contents
   - [Architectural Principles](#architectural-principles)
     - [Domain-Driven Design (DDD)](#domain-driven-design-ddd)
     - [Object-Oriented Programming (OOP)](#object-oriented-programming-oop)
     - [Vue 3.5 & Composition API](#vue-35--composition-api)
   - [Development Workflow](#development-workflow)
     - [Git Flow](#git-flow)
     - [Conventional Commits](#conventional-commits)
     - [Semantic Versioning](#semantic-versioning)
   - [Coding Standards](#coding-standards)
   - [Documentation](#documentation)

   ---

   ## Architectural Principles

   ### Domain-Driven Design (DDD)
   We follow a layered architecture organized by Bounded Contexts.
   - **Greetings Context**: Contains the core business logic (`Developer` entity, `DeveloperId` value object).
   - **Shared Kernel**: Contains cross-cutting value objects and utilities (`PersonName`, UUID generation).
   - **Layers**: Maintain a strict separation between the **Domain Layer** (pure JavaScript, no Vue import) and the **Presentation Layer** (`.vue` components).

   ### Object-Oriented Programming (OOP)
   - **Encapsulation**: internal fields use the `_` prefix, not native `#` private fields, because Vue's Proxy-based reactivity cannot read `#` fields through a wrapped instance (see `docs/adrs.md`, ADR-0003). Access still goes through getters.
   - **Invariants**: value objects validate themselves in their constructor (`DeveloperId`) or expose an `isValid()` check (`PersonName`); `Developer` only assigns an identity once its `PersonName` is valid.
   - **Identity**: entities are identified by a `DeveloperId` value object and compared with `.equals()`, never by reference or raw string.

   ### Vue 3.5 & Composition API
   - **`<script setup>`** for every component.
   - **Reactive props destructuring**: destructure `defineProps()` directly (`const { x } = defineProps({ ... })`); do not wrap it in `toRefs()`.
   - **Domain entities as props**: a component that needs a `Developer` receives the entity itself, not its raw fields.

   ---

   ## Development Workflow

   ### Git Flow
   We follow a simplified Git Flow model:
   - `main`: Reflects the latest stable production state.
   - `develop`: The main branch for ongoing development.
   - `feature/*`: Short-lived branches for specific features or bug fixes.
   - `release/*`: Preparation for new production releases.

   ### Conventional Commits
   Commit messages must follow the [Conventional Commits](https://www.conventionalcommits.org/) specification, lowercase, with a period at the end:
   `type(scope): description.`

   **Types:**
   - `feat`: A new feature.
   - `fix`: A bug fix.
   - `docs`: Documentation only changes.
   - `refactor`: A code change that neither fixes a bug nor adds a feature.
   - `test`: Adding missing tests or correcting existing tests.
   - `chore`: Changes to the build process or auxiliary tools and libraries.

   **Scopes:** the file, class, or component touched, for example `developer`, `developer-registration`, `developer-greeting`, `developer-count-show`, `app`, `docs`.

   Example: `feat(developer): add developer entity.`

   ### Semantic Versioning
   The project adheres to [Semantic Versioning (SemVer)](https://semver.org/): `MAJOR.MINOR.PATCH`.
   - **MAJOR**: Incompatible API changes.
   - **MINOR**: Add functionality in a backwards-compatible manner.
   - **PATCH**: Backwards-compatible bug fixes.

   ---

   ## Coding Standards
   - **Constants**: `UPPER_SNAKE_CASE` for module-level constants.
   - **Variables/Methods**: `lowerCamelCase`.
   - **Classes**: `UpperCamelCase`.
   - File names: `lower-kebab-case`, with a role suffix for domain files (`.entity.js`, `.value-object.js`).

   ---

   ## Documentation
   - **Architecture Decisions**: New significant architectural choices must be documented in `docs/adrs.md`.
   - **Traceability**: Ensure functional requirements are mapped in the Requirement Traceability Matrix (RTM) within `docs/user-stories.md`.
   - **Changelog**: Update `CHANGELOG.md` for every release following the "Keep a Changelog" format.
   ````
   </details>

   ```
   git add .
   git commit -m "docs: add contributing guidelines."
   git push
   ```

---

## Release

**Still on `develop`.** Every feature plus the license, README, and contributing guide are merged. `main` shouldn't stay permanently behind `develop`: close the loop with a release.

1. **Start the release.** Git Flow Helper widget → `Release` → `Release Start` → **Version description** `v1.0.0` → `OK`. Creates and switches you to `release/v1.0.0`.

   **Note:** the release name carries a `v` prefix (`v1.0.0`), matching the git tag it becomes on finish. The `package.json` `"version"` stays plain (`1.0.0`), and so does the `CHANGELOG.md` heading (`## [1.0.0]`): npm and Keep a Changelog conventions don't use the prefix.

2. **Bump the version.** A release branch needs at least one commit of its own, or the merge into `develop` is a no-op. Open `package.json`, change `"version": "0.1.0"` to `"version": "1.0.0"`.

   ```
   git add .
   git commit -m "chore(release): bump version to 1.0.0."
   ```

3. **Add `CHANGELOG.md`.** Right-click the project root → `New` → `File` → type `CHANGELOG.md` → Enter.

   <details>
   <summary>CHANGELOG.md</summary>

   ```markdown
   # Changelog

   All notable changes to this project will be documented in this file.

   The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/),
   and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

   ## [1.0.0] - 2026-09-11

   ### Added
   - `greetings` bounded context (`Developer` entity, `DeveloperId` value object) and a `shared` kernel (`PersonName` value object, UUID v7 utility).
   - `DeveloperRegistration` component: register with a first and last name, defer with "Later", or clear the form (US001, US004, US005).
   - `DeveloperGreeting` component: shows "Welcome Anonymous Developer" by default and a personalized greeting with the developer's ID once a valid name is registered (US002).
   - `DeveloperCountShow` component: running count of valid registrations only (US003).
   - `docs/user-stories.md` with a Requirement Traceability Matrix, `docs/class-diagram.puml`, `docs/adrs.md`.
   - `README.md`, MIT `LICENSE.md`.

   ### Design notes
   - `Developer` only receives a `DeveloperId` once `PersonName.isValid()` holds (both names present), so the presence of an ID marks a registered developer.
   - Internal fields use the `_` convention, not native `#` private fields, because Vue 3's Proxy-based reactivity cannot read `#` fields through a wrapped instance (ADR-0003).
   - Identifiers are UUID v7 (time-ordered), generated in the shared kernel so the version is decided in one file.
   ```
   </details>

   ```
   git add .
   git commit -m "docs: add changelog for 1.0.0."
   git push
   ```

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
   - **Set as the latest release** checked; **Set as a pre-release** unchecked.
   - Click **Publish release**.

   <details>
   <summary>Release notes (1.0.0)</summary>

   ```markdown
   ## 🚀 Added

   - **US001:** register with a first and last name; `PersonName.isValid()` requires both, and the `DeveloperRegistration` component shows a message when they are missing or spaces-only.
   - **US002:** the `DeveloperGreeting` component shows "Welcome Anonymous Developer" until a valid registration, then a personalized greeting with the developer's UUID v7 ID.
   - **US003:** the `DeveloperCountShow` component tracks a running count of valid registrations only.
   - **US004:** a `Later` action clears the form and resets the app to the anonymous state.
   - **US005:** a `Clear` action empties the form without changing the current greeting or count.
   - `Developer` entity: conditional identity, a `DeveloperId` is assigned only once its `PersonName` is valid.
   - `DeveloperId` and `PersonName` value objects; `generateUUID()` / `isValidUUID()` in the shared kernel, backed by UUID v7.
   - Internal fields use the `_` convention rather than native `#` private fields, so these objects work correctly wrapped in Vue's reactivity (ADR-0003).
   - `README.md`, MIT `LICENSE.md`, `CHANGELOG.md`; `docs/user-stories.md` with a Requirement Traceability Matrix, `docs/class-diagram.puml`, `docs/adrs.md`.
   ```
   </details>

6. **Back on `develop`, move to the next development version.**
   - In `package.json`: `"version": "1.0.0"` → `"version": "1.0.1"`, so `develop` doesn't sit on an already-tagged version. The next change decides whether it becomes `1.0.1` (a fix) or `1.1.0` (a feature).
   - Then:

   ```
   git add .
   git commit -m "chore(dev): set development version to 1.0.1."
   git push
   ```

---

## Document the Project

**Still on `develop`, no feature branch:** writing down decisions already made.

1. **Add the Architecture Decision Records** to a single `docs/adrs.md`. Right-click the `docs` folder → `New` → `File` → type `adrs.md` → Enter. Five decisions, written in one sitting: the bounded-context layout, reactive props destructuring, the `_` fields decision (the one that matters most here), identity as a value object, and the mandatory-full-name rule.

   <details>
   <summary>docs/adrs.md</summary>

   ````markdown
   # Architecture Decision Records

   # ADR-0001: Bounded Context Layout (DDD-lite)

   **Status:** Accepted

   ## Context

   A standard flat structure (all components and logic in one folder) works for a handful of files, but it hides which code owns which concept and invites tight coupling as the project grows.

   ## Decision Drivers

   - Domain logic (what a valid name is, when a developer counts as registered) should live apart from the Vue components that present it.
   - A concept used by more than one future context (a person's name) should not live inside the context that happens to use it first.

   ## Considered Options

   1. A `greetings` bounded context (`domain` + `presentation`) plus a `shared` kernel for cross-cutting value objects *(Chosen)*
   2. A single flat `src/components` and `src/models` split, no bounded-context boundary
   3. One folder per Vue component, domain logic inlined in each `<script setup>`

   ## Decision

   Two areas: **`src/greetings`** is the bounded context, with `domain/model` (the `Developer` entity and `DeveloperId` value object) and `presentation/components` (the three `.vue` components). **`src/shared`** is the shared kernel: `domain/model` holds `PersonName` (usable by any future context that deals with people) and `domain/uuid.js` holds identity generation.

   ## Consequences

   **Positive:**
   - `Developer`, `DeveloperId`, and `PersonName` are plain JavaScript, testable with no Vue runtime.
   - A second bounded context could reuse `PersonName` and the `uuid` utility without importing anything from `greetings`.

   **Negative:**
   - More folders than a two-component app strictly needs. The layout pays off as soon as a second context or a second entity appears.

   ---

   # ADR-0002: Vue 3.5 Reactive Props Destructuring

   **Status:** Accepted

   ## Context

   Vue 3.5 lets `<script setup>` destructure `defineProps()` directly (`const { developer } = defineProps({ developer: { type: Developer, required: true } })`) and keep each destructured binding reactive, tracking it like `props.developer` would. Earlier Vue versions required either `props.x` everywhere or `toRefs(defineProps(...))` to keep a destructured prop reactive.

   ## Decision Drivers

   - Reading `developer` directly, instead of `props.developer`, is closer to plain JavaScript and easier to read in a `computed()`.
   - The project targets Vue 3.5+, so the newer syntax is available everywhere.

   ## Considered Options

   1. Reactive props destructuring, `const { x } = defineProps({ ... })` *(Chosen)*
   2. `const props = defineProps({ ... })`, then `props.x` throughout
   3. `const props = defineProps({ ... })` then `const { x } = toRefs(props)`

   ## Decision

   Every component destructures `defineProps()` directly. `DeveloperCountShow` and `DeveloperGreeting` use this form; no component wraps `defineProps()` in `toRefs()`.

   ## Consequences

   **Positive:**
   - Less ceremony than `toRefs()`, and no risk of destructuring away reactivity by mistake, which was a real footgun in older Vue.

   **Negative:**
   - Requires Vue 3.5+; a project pinned to an older 3.x release would need `toRefs()` instead.

   ---

   # ADR-0003: Semi-Private Fields (`_`) Instead of Native Private Fields (`#`)

   **Status:** Accepted

   ## Context

   Native JavaScript private fields (`#field`) give real runtime encapsulation and are the convention in this course's other JavaScript projects. Vue 3's reactivity, though, wraps objects passed into `ref()` or `reactive()` in a `Proxy`. A method that reads `this.#field` throws `TypeError: Cannot read private member from an object whose class did not declare it` when `this` is that `Proxy`, because a private-field read is a direct internal-slot check against the exact receiver, and the receiver Vue hands back is the Proxy, not the original instance.

   ## Decision Drivers

   - Domain entities (`Developer`) and value objects (`DeveloperId`, `PersonName`) get stored in `ref()`s and passed as component props, so they are exactly the objects Vue's reactivity wraps.
   - Encapsulation should not come at the cost of the app crashing the first time a reactive `Developer` calls one of its own getters.

   ## Considered Options

   1. `_field` convention (not enforced by the language, but invisible to the Proxy machinery) *(Chosen)*
   2. Native `#field` private fields
   3. Closures over local variables instead of a class

   ## Decision

   Every internal field on `Developer`, `DeveloperId`, and `PersonName` uses the `_` prefix (`_id`, `_name`, `_value`, `_firstName`, `_lastName`), never `#`. Access is still funneled through getters; nothing outside the class reads `_field` directly, `_` marks it internal by convention rather than by the runtime.

   ## Consequences

   **Positive:**
   - These objects work correctly wrapped in Vue's reactivity, as `ref()` values and as component props, with no special-casing.
   - The same objects would also work unwrapped (a plain `new Developer(...)` outside of Vue), so the domain layer has no hidden Vue dependency.

   **Negative:**
   - `_field` is reachable at runtime from outside the class (`someDeveloper._name` compiles and runs). Nothing in this codebase does that, but the language does not stop it. For the same reason, this project also skips `Object.freeze(this)` on these objects (used elsewhere in the course's plain-JavaScript projects): freezing is unnecessary extra ceremony here, since nothing mutates a `_field` after construction, and it is left out to keep this one exception to the `#`-fields convention easy to spot.

   ---

   # ADR-0004: Identity as a Value Object (`DeveloperId`)

   **Status:** Accepted

   ## Context

   A raw UUID string for identity leads to primitive obsession: nothing stops a `ProductId` string from being passed where a `DeveloperId` is expected, and validation (is this actually a well-formed UUID?) ends up duplicated at every boundary that receives one.

   ## Decision Drivers

   - An identifier should carry its own validation and be a distinct type from every other identifier.
   - The identifier should sort and index well if this ever talks to a real datastore.

   ## Considered Options

   1. `DeveloperId` value object wrapping a UUID v7 string, with its own validation *(Chosen)*
   2. A raw `string` field on `Developer`
   3. A numeric auto-increment counter

   ## Decision

   `DeveloperId` wraps a `string`, validated in its constructor with `isValidUUID()` (checks both that it parses as a UUID and that it is version 7). `DeveloperId.build()` is the only way to mint a new one, it generates via `generateUUID()` in the shared kernel. `Developer` holds a `DeveloperId | null`, never a bare string.

   ## Consequences

   **Positive:**
   - `new DeveloperId('not-a-uuid')` throws immediately, at the boundary, instead of a malformed ID surfacing later.
   - The UUID version (currently v7, time-ordered) is decided in one file (`shared/domain/uuid.js`) and can change without touching `DeveloperId` or `Developer`.

   **Negative:**
   - Comparing two IDs needs `.equals()` (or `.value`), not `===`, since they are objects, not primitives.

   ---

   # ADR-0005: Both Names Required to Register

   **Status:** Accepted

   ## Context

   A `Developer` could, in principle, be considered registered with just a first name, or with any non-empty input. The business rule for this project's user stories is stricter: registration, the greeting, and the count all depend on one consistent definition of "has a real name."

   ## Decision Drivers

   - The greeting, the running count, and the identity assignment must all agree on what counts as a registered developer, one rule, checked once.
   - Partial input (a first name with no last name) is common while a visitor is still typing, and must not be treated as a completed registration.

   ## Considered Options

   1. `PersonName.isValid()` requires both first and last name to be non-blank after trimming; `Developer` only assigns a `DeveloperId` when the provided `PersonName` is valid *(Chosen)*
   2. Allow registration with just a first name
   3. Validate in `DeveloperRegistration` only, and let `Developer` trust whatever it is given

   ## Decision

   `PersonName.isValid()` (an alias for `isFullyNamed()`) is `true` only when both `firstName` and `lastName` have length after trimming. `Developer`'s constructor builds a `PersonName` first, then assigns `_id = providedName.isValid() ? DeveloperId.build() : null`, so an incomplete name never gets an identity. `isRegisterable()` and `isIdentified()` both read off that same one decision.

   ## Consequences

   **Positive:**
   - One rule, checked in one place (`PersonName`), decides registration everywhere it matters: the entity's identity, the greeting, and the count.
   - A `Developer` built from partial input is a legitimate, harmless object (never null, never throws) that simply is not registerable yet.

   **Negative:**
   - A visitor who only wants to give a first name has no way to register partially. That is the intended behavior for this course project, not an oversight.
   ````
   </details>

   ```
   git add .
   git commit -m "docs: add architecture decision records."
   git push
   ```

2. **Confirm the Requirement Traceability Matrix in `docs/user-stories.md`** maps every scenario to what implements it. It was added at Project Setup; check each row still points at the right class now that all the code exists.

---

## Testing (optional, explore on your own)

Everything above already shipped as `1.0.0`. Vite's `vue` template comes with no test setup at all, this section adds one and a real unit suite for the domain entity, then leaves you a starting point to go further.

1. **Add Vitest.** From the terminal:

   ```
   npm install --save-dev vitest
   ```

   **Note:** `vitest` reads and extends the project's own `vite.config.js`, so no separate config file is required.

2. **Turn on global test APIs.** Open `vite.config.js` and add a `test` block, so `describe`, `it`, and `expect` are available in every `.spec.js` with no import.

   <details>
   <summary>vite.config.js</summary>

   ```javascript
   import { defineConfig } from 'vite'
   import vue from '@vitejs/plugin-vue'

   // https://vite.dev/config/
   export default defineConfig({
     plugins: [vue()],
     test: {
       globals: true,
     },
   })
   ```
   </details>

3. **Add a test script.** Open `package.json` and add it to `"scripts"`:

   ```json
   "test": "vitest run",
   ```

   ```
   git add .
   git commit -m "chore: add vitest."
   ```

4. **Test the `Developer` entity.** It is plain JavaScript, so it needs no Vue runtime and no `@vue/test-utils`. Right-click `src` → `New` → `JavaScript File` → type the full path starting from the bounded context, `greetings/domain/model/developer.entity.spec` → Enter (WebStorm adds the `.js`). Cover: the anonymous default, a fully-named developer, a partially-named one, identity, and equality's absence (there is no `Developer.equals()`, only `DeveloperId.equals()`).

   <details>
   <summary>src/greetings/domain/model/developer.entity.spec.js</summary>

   ```javascript
   import {Developer} from './developer.entity.js';

   describe('Developer', () => {
     it('is not registerable or identified with no name at all', () => {
       const developer = new Developer('', '');

       expect(developer.isRegisterable()).toBe(false);
       expect(developer.isIdentified()).toBe(false);
       expect(developer.id).toBeNull();
       expect(developer.fullName).toBe('Unknown');
     });

     it('is registerable and identified once both names are present', () => {
       const developer = new Developer('Ada', 'Lovelace');

       expect(developer.isRegisterable()).toBe(true);
       expect(developer.isIdentified()).toBe(true);
       expect(developer.id).not.toBeNull();
       expect(developer.fullName).toBe('Ada Lovelace');
     });

     it('is not registerable with only one name', () => {
       expect(new Developer('Ada', '').isRegisterable()).toBe(false);
       expect(new Developer('', 'Lovelace').isRegisterable()).toBe(false);
     });

     it('trims whitespace-only names down to nothing', () => {
       const developer = new Developer('   ', '   ');

       expect(developer.isRegisterable()).toBe(false);
       expect(developer.fullName).toBe('Unknown');
     });

     it('assigns a UUID v7 identifier once registerable', () => {
       const developer = new Developer('Grace', 'Hopper');
       const uuidV7 = /^[0-9a-f]{8}-[0-9a-f]{4}-7[0-9a-f]{3}-[89ab][0-9a-f]{3}-[0-9a-f]{12}$/i;

       expect(developer.id.toString()).toMatch(uuidV7);
     });
   });
   ```
   </details>

   ```
   git add .
   git commit -m "test(developer): add developer entity tests."
   ```

5. **Run the suite** and confirm it is green:

   ```
   npm test
   ```

6. **From here, it is on you.** Add a scenario the suite does not cover yet: unit tests for `PersonName` and `DeveloperId` on their own, or, with `npm install --save-dev @vue/test-utils` and Vitest's `jsdom` environment, a mounted-component test asserting `DeveloperRegistration` emits `developer-registered` with a valid `Developer` on submit and stays silent (only setting `errorMessage`) on an invalid one.

---

## Appendix

Reference notes for situations that come up now and then. Skip past this on a normal run and come back when you hit one of them.

### Continuing on another computer

Once you have pushed your work it is on GitHub, so you can carry on from any machine.

1. **Sign in, then clone.**
   - Sign in once, so both git and the IDE can reach the private repo:

     ```
     gh auth login
     ```

   - Clone in a terminal in the folder where you keep your projects (`<org>` is your organization's name, no angle brackets):

     ```
     gh repo clone <org>/hello-vue-developer
     ```

   - Open the `hello-vue-developer` folder in WebStorm afterward (`File` → `Open`). Or, from the JetBrains Welcome screen, `Clone Repository`, paste `https://github.com/<org>/hello-vue-developer.git`, pick a folder, `Clone`.

2. **Restore the dependencies.** A clone has no `node_modules/`:

   ```
   npm install
   ```

3. **Reinstall the tools that live outside the repo.** Plugins live in the IDE, not the repo: reinstall the Git Flow Helper plugin (Project Setup step 11) and the plantuml4idea plugin (Project Setup step 7) if this machine does not have them.

4. **Reinstate Git Flow.**
   - Check out `develop` before anything else: a fresh clone only has `main` as a local branch. Do it before `Init`, so Git Flow Helper registers against the existing `develop` instead of creating a new one.

     ```
     git checkout develop
     ```

   - Register your GitHub account in the IDE (Project Setup step 12): get the token with

     ```
     gh auth token
     ```

     then in `Settings` → `Version Control` → `GitHub`, remove any account already listed, then `+` → `Log In with Token...` → paste the token.
   - Run Git Flow `Init` from the widget and accept the defaults; the Git Flow settings live in the repo's local git config, which a clone does not copy.

5. **Get onto your feature branch,** only if you stopped partway through one:

   ```
   git checkout feature/<name>
   git pull
   ```

   **Note:** seeing only `main` locally right after a clone is normal. Every remote branch was downloaded; the checkouts above are what turn them into local branches.

### Signing in to GitHub with a token

The guide uses `gh auth login` (Project Setup step 10), which is the simplest way. If you can't install `gh`, GitHub also accepts a Personal Access Token.

**On a shared machine, clear any cached credential first** so a plain push doesn't run as whoever signed in last:

```
git credential reject
```

Then type these two lines, Enter after each, and Enter once more on the empty line to finish:

```
protocol=https
host=github.com
```

1. When `git push` asks for credentials, generate one: GitHub → `Settings` → `Developer settings` → `Personal access tokens` → `Generate new token (classic)`.
   - Note (the label): `UPC`.
   - Expiration: leave the default (`30 days`).
   - Scopes: check only the top-level `repo` checkbox (covers everything a push needs); leave the rest unchecked.
   - Click **Generate token**, then copy it somewhere safe (a password manager) before navigating away. GitHub shows it **only once**. The IDE GitHub account (Project Setup step 12) needs it too.
2. Back in the terminal where the push is waiting:
   - **macOS:** type your GitHub username, then paste the token as the password (nothing shows as you paste, that's normal).
   - **Windows:** in the "Connect to GitHub" window, pick the `Token` tab and paste it there.
3. It is cached after this, so it won't ask again for the rest of the project.

**Note:** use `(classic)`, not "Fine-grained tokens": fine-grained tokens need the organization owner to approve them first, which can leave you waiting. With `repo` scope the token reaches every repo your account can, so reuse it across the other course projects.

### Creating the repo without the GitHub CLI

No `gh`? Do the whole thing through the GitHub website plus plain `git`.

1. Authenticate git first, since `gh auth login` isn't available: follow [Signing in to GitHub with a token](#signing-in-to-github-with-a-token).
2. On GitHub, inside your organization, create an empty **private** repo named `hello-vue-developer`, with no README, license, or `.gitignore` (this project already has all three).
3. On the repo's "Quick setup" page, copy the **HTTPS** clone URL, the one ending in `.git` (`https://github.com/<org>/hello-vue-developer.git`, `<org>` is your organization's name), not the address-bar URL.
4. From the project root, add the remote and push:

   ```
   git remote add origin https://github.com/<org>/hello-vue-developer.git
   git push -u origin main
   ```

5. On the repo page, click the gear next to **About** and paste the same description text the `gh repo create` command in Project Setup uses.

### Backing up unfinished work

Commits stay on your machine until you push them. If you have to stop before a feature is finished, commit your progress and run `Feature Publish` from the Git Flow Helper widget (or a plain push, if the branch is already published):

```
git push
```

Later, check out the branch on any machine and pull to continue:

```
git pull
```

A feature doesn't have to be one commit.

### Feature Finish and pull requests

`Feature Finish` with `Integrate Immediately` merges your branch straight into `develop`. A real team does this through a pull request instead: open one from your feature branch into `develop` (`Feature Publish` already put the branch on GitHub), wait for the build and a reviewer, then merge it. The git steps are the same; `Integrate Immediately` just skips the review, which you can't do on your own anyway.

### Removing a stray .git folder

If `git init` ran from a subfolder instead of the project root, a `.git` folder is now sitting in that subfolder. `.git` is hidden, so reveal it first:

- **macOS (Finder):** press `Cmd+Shift+.`
- **Windows (File Explorer):** turn on `View` → `Show` → `Hidden items`

Delete that `.git` folder like any other folder. Or, from a terminal opened in the folder that holds it, run the line for your shell:

```
rm -rf .git                        # macOS / Linux / Git Bash
Remove-Item -Recurse -Force .git   # Windows PowerShell
rmdir /s /q .git                   # Windows Command Prompt
```

Then initialize from the project root as step 9 describes:

```
git init -b main
```

### Fixing file or folder permissions

On a shared lab machine, `npm install` or the project folder itself can end up owned by a different account. The symptoms:

- Files created outside the IDE (from the terminal) don't show up in WebStorm's **Project** tool window, or the IDE can't save over them.
- `npm install` fails with `EACCES`, or only works with `sudo` (which then makes the problem worse, because the files it writes are owned by `root`).

Fix the ownership of the whole project tree in one go. On the lab Macs the login account is `alumnos` and its group is `staff`:

```
sudo chown -R alumnos:staff {CHANGE_WITH_YOUR_PATH}
```

For example, if the project is in `~/Documents/hello-vue-developer`:

```
sudo chown -R alumnos:staff ~/Documents/hello-vue-developer
```

On your own Mac, use your own account instead of `alumnos` (run `whoami` to see it):

```
sudo chown -R "$(whoami):staff" ~/Documents/hello-vue-developer
```

If `npm` itself has been run with `sudo` before, its cache is root-owned too:

```
sudo chown -R alumnos:staff ~/.npm
```

Then delete `node_modules` and reinstall **without** `sudo`:

```
rm -rf node_modules
npm install
```

On Windows this rarely happens; if a file is read-only, right-click it → `Properties` → uncheck `Read-only`. The lasting fix on any machine is to keep Node installed through a per-user version manager (`nvm`, `fnm`, or `volta`) so `npm install` never needs elevated rights. Don't keep adding `sudo`.

### If the class diagram doesn't render

The plantuml4idea plugin needs a local Java runtime and Graphviz to render some diagrams. If `docs/class-diagram.puml` shows an error instead of a diagram, install a JDK and Graphviz, then restart WebStorm.

On macOS:

```
brew install graphviz
```

On Windows, download and run the installer from [graphviz.org](https://graphviz.org/download/).

### Free JetBrains license for students

WebStorm is free for students through the [JetBrains Student Pack](https://www.jetbrains.com/community/education/#students): apply with a school email address, or upload proof of enrollment if your school email isn't recognized. Approval usually takes a few minutes. The license covers the whole JetBrains suite and renews each year you're enrolled.

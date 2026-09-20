# CatchUp Guide

## Table of Contents

- [Project Setup](#project-setup)
- [(US001) Browse News Sources](#browse-news-sources-us001)
- [(US002) View Articles](#view-articles-us002)
- [(US003) Engage with Ethical and Inclusive Features](#engage-with-ethical-and-inclusive-features-us003)
- [(US004) Interact with Articles and Sources](#interact-with-articles-and-sources-us004)
- [Prepare the First Release](#prepare-the-first-release)
- [Release](#release)
- [Appendix](#appendix)
  - [Continuing on another computer](#continuing-on-another-computer)
  - [Signing in to GitHub with a token](#signing-in-to-github-with-a-token)
  - [Creating the repo without the GitHub CLI](#creating-the-repo-without-the-github-cli)
  - [Backing up unfinished work](#backing-up-unfinished-work)
  - [Feature Finish and pull requests](#feature-finish-and-pull-requests)
  - [Removing a stray .git folder](#removing-a-stray-git-folder)
  - [Fixing file or folder permissions](#fixing-file-or-folder-permissions)
  - [If a PlantUML diagram doesn't render](#if-a-plantuml-diagram-doesnt-render)
  - [Free JetBrains license for students](#free-jetbrains-license-for-students)

## Project Setup

1. **Make sure Node.js 24.20 LTS (or newer) is installed.** npm comes bundled with it.

   **Mac:**

   ```
   node --version
   ```

   - If `node --version` prints `v24.20.x` or higher, you are done with this step, skip to step 2.
   - Otherwise, install it with Homebrew. Check Homebrew itself is installed first:

     ```
     brew --version
     ```

     No output, or `command not found: brew`? Install it:

     ```
     /bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
     ```

     The installer prints one or two `echo` commands near the end, under "Next steps", that add Homebrew to your `PATH`, they differ by chip (Apple Silicon vs Intel) and shell. Run exactly the ones it shows you, then close the terminal and open a new one. Confirm it worked:

     ```
     brew --version
     ```

     Now install Node:

     ```
     brew install node@24
     ```

   **Windows:** download the **Node.js 24 LTS** installer from [nodejs.org/en/download](https://nodejs.org/en/download) (the page shows "LTS" and "Current" side by side, pick the one labeled **LTS**, confirm the version number reads `24.20` or higher) and run it.

   Confirm it:

   ```
   node --version
   ```

   `node --version` must now read `v24.20.x` or higher.

   **Note:** this project needs at least `24.20`, not just "any `24`". This course standardizes on the same Node version across every lab project, including the Angular ones, so if you already installed `24.20` or newer for another course project, there is nothing to do here.

   **Note:** on some lab Macs, `brew install node@24` finishes with no error but `node --version` still shows an older `v24.x.x` (for example `v24.14.0`), it still matches "starts with `v24`", so it is easy to miss, this is exactly the case above. Homebrew installs a versioned formula like `node@24` "keg-only", without linking it onto your `PATH`, so whatever `node` was already there (another Homebrew formula, or the system one) keeps running. `brew update` alone does not fix an already-installed keg-only formula still on `PATH`. Point your shell at the versioned install instead:

   Run both of these, one per shell config file, so this works no matter which shell your terminal actually reads, zsh is the default on current macOS but older setups (or ones changed by hand) still use bash:

   ```
   echo 'export PATH="/opt/homebrew/opt/node@24/bin:$PATH"' >> ~/.zshrc
   ```

   ```
   echo 'export PATH="/opt/homebrew/opt/node@24/bin:$PATH"' >> ~/.bash_profile
   ```

   **You must restart the terminal for this to take effect**, close the terminal window or tab entirely and open a new one (an already-open tab keeps the old `PATH` even after this command runs; if you have WebStorm open, restart its Terminal tool window too). Then check `node --version` again.

   If a later step complains it cannot find Node's headers or libraries while compiling something, also export these (once per terminal session, or add them to the same profile file):

   ```
   export LDFLAGS="-L/opt/homebrew/opt/node@24/lib"
   export CPPFLAGS="-I/opt/homebrew/opt/node@24/include"
   ```

   **Note:** `24` is the LTS line this guide targets, `24.20` and up is what it requires. [nodejs.org](https://nodejs.org) shows the current LTS major on its front page; this project runs fine on a newer LTS too.

   **Note:** on a shared lab Mac, every student uses the same account, so if a previous student ran an `npm install` command with `sudo` at some point, its npm cache (`~/.npm`) is now owned by `root` instead of the account you're on. When that happens, every later `npm install` fails with an `EACCES` permission error, even on a machine where Node itself is installed correctly. Run it now, before the first `npm install` in step 2, whether or not you think it applies to you, it's a no-op if the cache was already fine, so there's no downside to always running it:

   ```
   sudo chown -R "$(whoami):$(id -gn)" ~/.npm
   ```

   `$(whoami)` and `$(id -gn)` resolve to whoever is actually logged in and their own primary group, on a lab Mac that's the shared lab account, on your own Mac it's you, same command either way.

   Don't fix an `EACCES` error by running `npm install` itself with `sudo`, that only moves the ownership problem to whatever it writes next, `chown` above is the actual fix; `sudo` is fine, even necessary, for the `chown` command itself. This is a macOS-only fix, Windows does not use this permission model, `npm install` there fails differently, from a read-only folder, which is fixed through the folder's `Properties` dialog, not the terminal. If `npm install` still fails with `EACCES` once a real project exists, see [Appendix: Fixing file or folder permissions](#fixing-file-or-folder-permissions) for the full escalation.

2. **Create the project.** Two ways, pick one. Either leaves the same scaffold on disk.

   **Option A, from the terminal.** Keep every project from this course together in one `wa-projects` folder, inside `Documents`. Create it if it does not exist yet, then `cd` into it:

   ```
   mkdir -p ~/Documents/wa-projects
   ```

   ```
   cd ~/Documents/wa-projects
   ```

   Then create the project there:

   ```
   npm create vite@latest catch-up -- --template vue
   ```

   This is Vite's own scaffolding tool; `--template vue` skips the interactive framework/variant prompts (plain JavaScript, not TypeScript). It still asks one question:

   ```
   Install with npm and start now? … yes / no
   ```

   Answer **No**, `npm install` runs as its own explicit step next. Then:

   ```
   cd catch-up
   ```

   ```
   npm install
   ```

   Unlike some scaffolding tools, `npm create vite` does **not** initialize git for you, that stays an explicit step, in step 4 below.

   Then open it in the editor:

   ```
   webstorm .
   ```

   No `webstorm` command? Open WebStorm and use `File` → `Open` to pick the `catch-up` folder you just created.

   **Option B, from WebStorm.** `File` → `New Project`. In the left list under **Generators**, pick **Vite** (it scaffolds through the same `create-vite` template as Option A, framework-agnostic, so the wizard asks for the framework itself).
   - **Location:** `~/Documents/wa-projects/catch-up` (WebStorm creates the `wa-projects` folder too if it does not exist yet; the last path segment becomes the project name).
   - **Node runtime:** leave at its detected default.
   - The **Vite** dropdown: leave it at its default value, `npx create-vite`.
   - **Template:** pick `Vue` from the dropdown.
   - **Make sure "Use TypeScript template" is unchecked**, this project is plain JavaScript.
   - **Create**, then run `npm install` in the WebStorm terminal if the wizard did not do it for you. WebStorm opens the project automatically, nothing else to do here.

   **Note:** on a shared lab Mac, either option can fail with a permissions error, because a previous account owns files under your home folder or the new project folder. Take ownership, then re-run the failed command, `$(whoami)`/`$(id -gn)` resolve to whoever is actually logged in and their primary group, the same command works whether that's the shared lab account or your own:

   ```
   sudo chown -R "$(whoami):$(id -gn)" ~/Documents/wa-projects/catch-up
   ```

   Full details, including the `npm install` case and the Windows equivalent, are in [Appendix: Fixing file or folder permissions](#fixing-file-or-folder-permissions).

3. **Update the wizard's `.gitignore`.** Before the repository exists, so the very first commit already ignores the right things instead of tracking a few files this project doesn't want, then having to untrack them later. Vite already generated one at the project root; every section is exactly what this project needs except one, the editor section ignores everything under `.vscode/` except one file (`!.vscode/extensions.json`), which is how a WebStorm-only project can still end up with a stray `.vscode/` folder tracked on GitHub. Ignore the whole folder instead.

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
   .vscode/
   .idea
   .DS_Store
   *.suo
   *.ntvs*
   *.njsproj
   *.sln
   *.sw?
   ```
   </details>

   **Note:** no commit here, no repository exists yet, this edit rides into the first commit the next step makes.

4. **Initialize the local repository.** Right here, on the wizard output plus the `.gitignore` fix, before touching anything else, so every change from this point on gets its own commit instead of piling up into one at the end.

   ```
   git init -b main
   git config user.name "Your Name"
   git config user.email "your.email@example.com"
   ```

   See [git-from-repo-root trap](#removing-a-stray-git-folder) if this ever ends up run from the wrong folder.

   ```
   git add .
   git commit -m "chore: initial commit."
   ```

5. **Adjust the project metadata in `package.json`.** Open it. Leave `name`, `type`, `scripts`, `dependencies`, and `devDependencies` exactly as the scaffold wrote them; only touch the top:
   - Change `"version": "0.0.0"` to `"version": "0.0.1"`.
   - Add `"description"`, `"author"`, and `"license"` right after `"version"`:

     ```json
     "description": "News app to help you catch up on the latest headlines.",
     "author": "Web Applications Development Team",
     "license": "MIT",
     ```

   **Note:** starting at `0.0.1`, well below `1.0.0`, signals early development: the structure and behavior can still change freely from one version to the next. `## Release` at the end of this guide bumps it to `1.0.0`, the first version meant to stay stable.

   ```
   git add .
   git commit -m "chore: update project metadata."
   ```

6. **Replace the wizard's starter page.** The scaffold ships a demo counter (`src/components/HelloWorld.vue`, wired into `src/App.vue`). This project builds its own components instead.
   - Rename `src/App.vue` to `src/app.vue`. Right-click the file → `Refactor` → `Rename`, or rename it from the File System view and fix the import by hand.

     **Note:** on Windows, and on a Mac with the default file system, file names are not case-sensitive, so a rename that only changes the case (`App.vue` to `app.vue`) can silently do nothing, the file stays `App.vue`. If that happens, rename it twice, first to any different name, then to the final one:
     - `App.vue` → `App2.vue`
     - `App2.vue` → `app.vue`
   - Delete `src/components/HelloWorld.vue` (and the now-empty `src/components` folder).
   - Delete the `src/assets` folder too, it only held the demo page's images and nothing in this project uses it.
   - Open `src/main.js` and update the import to match the renamed file:

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

   - Open `src/app.vue` and clear it down to an empty shell, this is where the layout wires in:

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

   - Open `src/style.css` and replace its whole content. The scaffold's stylesheet centers `#app` in a fixed 1126px column with side borders and centered text, which would squeeze the layout this project builds; this one leaves the page full width:

     <details>
     <summary>src/style.css</summary>

     ```css
     :root {
       font-family: system-ui, Avenir, Helvetica, Arial, sans-serif;
       line-height: 1.5;
       font-weight: 400;
     }

     body {
       margin: 0;
     }

     #app {
       width: 100%;
       margin: 0;
       padding: 0;
     }
     ```
     </details>

   - Open `index.html` and give the page its real title, the scaffold's is `catch-up`; the tab now reads `CatchUp - Your Daily News`:

     <details>
     <summary>index.html</summary>

     ```html
     <!doctype html>
     <html lang="en">
       <head>
         <meta charset="UTF-8" />
         <link rel="icon" type="image/svg+xml" href="/favicon.svg" />
         <meta name="viewport" content="width=device-width, initial-scale=1.0" />
         <title>CatchUp - Your Daily News</title>
       </head>
       <body>
         <div id="app"></div>
         <script type="module" src="/src/main.js"></script>
       </body>
     </html>
     ```
     </details>

   ```
   git add .
   git commit -m "chore: replace the wizard's starter page with an empty shell, a plain stylesheet and a real page title."
   ```

7. **Add PrimeVue.** This project's UI components (`pv-button`, `pv-drawer`, `pv-card`, and the rest) come from it, not from hand-rolled markup.

   ```
   npm install primevue @primeuix/themes primeicons primeflex
   ```

   **Note:** `primevue` is the component library itself; `@primeuix/themes` is its theming engine (this project uses the `Material` preset); `primeicons` and `primeflex` are its icon font and CSS utility classes, both used throughout the templates below (`pi pi-share-alt`, `flex`, `gap-2`, and so on).

   ```
   git add .
   git commit -m "chore: add PrimeVue dependency."
   ```

8. **Add vue-i18n.** The language switcher and every translated string this app shows depend on it.

   ```
   npm install vue-i18n
   ```

   ```
   git add .
   git commit -m "chore: add vue-i18n dependency."
   ```

9. **Add axios.** The HTTP client this project's API calls use, instead of the browser's built-in `fetch`.

   ```
   npm install axios
   ```

   **Note:** three separate installs (PrimeVue, vue-i18n, axios), not one combined command. Each one is its own concern, and if any single install fails (a flaky network on a lab machine, for instance), you know exactly which dependency to retry, not which one of several to suspect.

   ```
   git add .
   git commit -m "chore: add axios dependency."
   ```

10. **Get a NewsAPI.org API key.**
    - Go to [newsapi.org/register](https://newsapi.org/register).
    - Fill in the form: **First name**, **Email address**, **Choose a password**, **You are...** (pick `Individual`), check the box agreeing to the terms.
    - Submit the form. Your account page shows your API key, a 32-character string, copy it.

11. **Get a Logo.dev publishable key.**
    - Go to [logo.dev](https://logo.dev/) and create a free account.
    - Open the dashboard's **API Keys** page (`logo.dev/dashboard/api-keys`). Your **publishable key** is the one prefixed `pk_`, copy that one, not the `sk_` secret key next to it.

    **Note:** `pk_` keys are meant to sit in client-side code, that is exactly what this app does with it, a browser calling `img.logo.dev` directly. The `sk_` secret key is for server-to-server calls this app never makes, never put it here.

12. **Get a PrimeVue Community license key.** PrimeVue 22 and up needs a license key even for free use, the library paints a banner over the whole app without one.
    - Go to [primeui.dev/licenses/community](https://primeui.dev/licenses/community) and confirm you're eligible (the free Community license covers individuals, students, non-profits, and small organizations under specific revenue/headcount thresholds listed on that page).
    - Registration is self-service, based on your own confirmation of eligibility, no manual approval step. Copy the license key it issues you.

    **Note:** a Community key needs renewing once a year to reconfirm eligibility, with a 30-day grace period after it expires. Verification happens offline, the library never phones home to check it.

13. **Add the environment variables.** This project talks to three real external services, NewsAPI.org, Logo.dev, and PrimeVue's own license check. Right-click the project root → `New` → `File` → type `.env.development` → Enter.

    <details>
    <summary>.env.development</summary>

    ```
    VITE_NEWS_API_KEY="0d5b87d6eed74a768b7f2f7a3ca1bafb"
    VITE_NEWS_API_URL="https://newsapi.org/v2"
    VITE_LOGO_API_URL="https://img.logo.dev"
    VITE_LOGO_PUBLISHABLE_API_KEY="pk_bufKzaXPQFeNkMz5gxZWAA"
    VITE_SOURCES_ENDPOINT_PATH="/sources"
    VITE_TOP_HEADLINES_ENDPOINT_PATH="/top-headlines"
    VITE_PRIME_UI_LICENSE_KEY="eyJpZCI6IjZlODA0NjNhLTJkMGMtNGI2ZC1iYmI1LTAwYjk3OWFkMGFmNCIsInByb2R1Y3QiOiJwcmltZXVpIiwidGllciI6ImNvbW11bml0eSIsInR5cGUiOiJkZXYiLCJpYXQiOjE3ODk1NTQ5MzAsImV4cCI6MTgyMTA5MDkzMH0.yvULBRGTn5hRzalLkmTf6BZaJYSwrK2LS6hLxTtO9fI0W2sgsCFpfcVHjfbqEQQe3i84X_KEZQv-WAQbRj9IAg"
    ```
    </details>

    Do the same for `.env.production`, same content.

    <details>
    <summary>.env.production</summary>

    ```
    VITE_NEWS_API_KEY="0d5b87d6eed74a768b7f2f7a3ca1bafb"
    VITE_NEWS_API_URL="https://newsapi.org/v2"
    VITE_LOGO_API_URL="https://img.logo.dev"
    VITE_LOGO_PUBLISHABLE_API_KEY="pk_bufKzaXPQFeNkMz5gxZWAA"
    VITE_SOURCES_ENDPOINT_PATH="/sources"
    VITE_TOP_HEADLINES_ENDPOINT_PATH="/top-headlines"
    VITE_PRIME_UI_LICENSE_KEY="eyJpZCI6IjZlODA0NjNhLTJkMGMtNGI2ZC1iYmI1LTAwYjk3OWFkMGFmNCIsInByb2R1Y3QiOiJwcmltZXVpIiwidGllciI6ImNvbW11bml0eSIsInR5cGUiOiJkZXYiLCJpYXQiOjE3ODk1NTQ5MzAsImV4cCI6MTgyMTA5MDkzMH0.yvULBRGTn5hRzalLkmTf6BZaJYSwrK2LS6hLxTtO9fI0W2sgsCFpfcVHjfbqEQQe3i84X_KEZQv-WAQbRj9IAg"
    ```
    </details>

    All three keys above are disposable demo keys, shown so you see the exact shape each provider issues (NewsAPI.org: 32 lowercase hex characters; Logo.dev: `pk_` followed by a token; PrimeVue: a signed JWT), not something to keep using. Replace all three with the keys from your own accounts, from steps 10, 11, and 12. None of these three services are optional, the app calls all of them, and a demo key shared by the whole class will run out of quota fast.

    **Note:** `.env.development` and `.env.production` hold working keys here because this is a teaching project on a scaffold Vite already ignores real secrets from (`*.local` in `.gitignore` covers `.env.local`, the file meant for a key you do not want committed at all). A real production app would keep every key out of source control; treat these two files the same way you would treat any other credential, once you swap in your own keys, do not paste a key you were not personally issued into a repository other people can see.

    ```
    git add .
    git commit -m "chore: add environment variable files."
    ```

14. **Declare the environment variable types.** Right-click `src` → `New` → `File` → type `vite-env.d.ts` → Enter. Vite exposes the values from `.env.development` and `.env.production` through `import.meta.env`, and this declaration file tells the editor which `VITE_*` variables exist and that each one is a `string`, so `import.meta.env.VITE_NEWS_API_KEY` autocompletes and a typo gets flagged. It changes nothing at runtime.

    <details>
    <summary>src/vite-env.d.ts</summary>

    ```typescript
    /// <reference types="vite/client" />
    interface ImportMetaEnv {
      readonly VITE_NEWS_API_KEY: string;
      readonly VITE_NEWS_API_URL: string;
      readonly VITE_LOGO_API_URL: string;
      readonly VITE_LOGO_PUBLISHABLE_API_KEY: string;
      readonly VITE_SOURCES_ENDPOINT_PATH: string;
      readonly VITE_TOP_HEADLINES_ENDPOINT_PATH: string;
      readonly VITE_PRIME_UI_LICENSE_KEY: string;
    }

    interface ImportMeta {
      readonly env: ImportMetaEnv;
    }
    ```
    </details>

    **Note:** the seven names match, one to one, the variables in the two `.env` files from the previous step. Add a variable to `.env.*` and you add its line here.

    ```
    git add .
    git commit -m "chore: add Vite environment variable types."
    ```

15. **Look at the architecture, then model the system at the C4 Context level.**
    - Real projects rarely start from a blank slate: the course already sets DDD and this bounded-context split as part of the Definition of Done. What is ahead is learning to read a given architecture and implement it well.
    - Install the **plantuml4idea** plugin so every diagram in this guide renders: `File` → `Settings` → `Plugins` → `Marketplace` → search `plantuml4idea` → `Install`. Restart the IDE if prompted.
    - Right-click the `docs` folder → `New` → `Directory` → type `c4` → Enter.

    Two bounded contexts: **`news`** (`Article`/`Source` entities, the store, the API clients, every news-specific component) and a **`shared`** kernel (`Url`/`DateTime`/`StringValidator`, the error interceptor, the components every context could reuse).

    The five steps below draw that architecture at increasing zoom, using the **C4 model** (Context, Container, Component, Code): each level answers a different question about the same system, and stays deliberately silent about anything one level deeper. This first one is the outermost, most zoomed-out view: one box for the whole system, the people who use it, and the other systems it talks to. Nothing about what is inside CatchUp shows up here at all.

    <details>
    <summary>docs/c4/context.puml</summary>

    ```
    @startuml "Context"
    !includeurl https://raw.githubusercontent.com/plantuml-stdlib/C4-PlantUML/master/C4_Context.puml

    title CatchUp - Context Diagram

    Person(user, "User", "A person who browses news sources and reads their articles")
    System(catchup, "CatchUp", "Lets a user browse news sources, read their top headlines, switch languages, and share articles")
    System_Ext(newsapi, "NewsAPI.org", "Provides news sources and their top headlines")
    System_Ext(logodev, "Logo.dev", "Resolves a source's logo from its website domain")

    Rel(user, catchup, "Browses sources and reads articles using [HTTPS]")
    Rel(catchup, newsapi, "Fetches sources and articles from [HTTPS]")
    Rel(catchup, logodev, "Fetches source logos from [HTTPS]")
    @enduml
    ```
    </details>

    **Note:** `!includeurl` fetches the C4 macro definitions (`Person`, `System`, `System_Ext`, `Rel`, ...) from a public GitHub URL at render time, this needs internet access, unlike `class-diagram.puml`'s plain PlantUML which needs none. If it shows an error instead of a diagram, see [Appendix: If a PlantUML diagram doesn't render](#if-a-plantuml-diagram-doesnt-render).

    ```
    git add .
    git commit -m "docs: add C4 context diagram."
    ```

16. **Model the system at the C4 Container level.** One level in: the separately runnable pieces inside CatchUp, each one something you could deploy and run on its own. Still nothing about what is inside any one of them.

    <details>
    <summary>docs/c4/containers.puml</summary>

    ```
    @startuml "Containers"
    !includeurl https://raw.githubusercontent.com/plantuml-stdlib/C4-PlantUML/master/C4_Container.puml

    title CatchUp - Container Diagram

    Person(user, "User", "A person who browses news sources and reads their articles")
    System_Ext(newsapi, "NewsAPI.org", "Provides news sources and their top headlines")
    System_Ext(logodev, "Logo.dev", "Resolves a source's logo from its website domain")

    System_Boundary(catchup, "CatchUp") {
        Container(web, "Web Application", "Nginx", "Serves the compiled Single Page Application to the user's browser")
        Container(spa, "Single Page Application", "Vue, PrimeVue", "Lets the user browse sources, read articles, switch languages, and share articles, all in the browser")
    }

    Rel(user, web, "Visits catch-up using [HTTPS]")
    Rel(web, spa, "Delivers to the user's web browser")
    Rel(user, spa, "Interacts with")
    Rel(spa, newsapi, "Fetches sources and articles from [HTTPS]")
    Rel(spa, logodev, "Fetches source logos from [HTTPS]")
    @enduml
    ```
    </details>

    **Note:** two containers, not one, `npm run build` only outputs static files, something still has to serve them over HTTP, that is `Nginx`'s job. The `Single Page Application` container is where every line of JavaScript in this guide ends up running, entirely inside the user's browser.

    ```
    git add .
    git commit -m "docs: add C4 container diagram."
    ```

17. **Model the SPA's components by bounded context (C4).** One level deeper, into a single container: the Single Page Application's major internal building blocks. This view groups them by DDD bounded context, the same news/shared split `class-diagram.puml` uses, and it is the only diagram where the two contexts appear together: every relation between news and shared is drawn here and nowhere else.

    <details>
    <summary>docs/c4/components-frontend.puml</summary>

    ```
    @startuml "Components-Bounded Contexts"
    !includeurl https://raw.githubusercontent.com/plantuml-stdlib/C4-PlantUML/master/C4_Component.puml

    title CatchUp - Component Diagram (Bounded Contexts)

    Container(web, "Web Application", "Nginx", "Serves the compiled Single Page Application to the user's browser")
    System_Ext(newsapi, "NewsAPI.org", "Provides news sources and their top headlines")
    System_Ext(logodev, "Logo.dev", "Resolves a source's logo from its website domain")

    Container_Boundary(spa, "Single Page Application") {
        Component(news, "News", "Vue", "Browses sources and reads their top headlines")
        Component(shared, "Shared", "Vue", "Url, DateTime, and StringValidator value objects, LogoDevApi, the HTTP error interceptor, and cross-cutting presentation: layout, language-switcher, footer-content")
    }

    Rel(web, shared, "Serves")
    Rel(shared, news, "Renders source-list, article-list, and unavailable-content from, and reads newsStore from")
    Rel(news, shared, "Uses Url, DateTime, and StringValidator from, resolves each source's logo with LogoDevApi from, and normalizes HTTP errors with errorInterceptor from")
    Rel(news, newsapi, "Fetches sources and articles from [HTTPS]")
    Rel(shared, logodev, "Fetches source logos from [HTTPS]")
    @enduml
    ```
    </details>

    **Note:** the arrows between contexts go both ways. `shared` renders `source-list`, `article-list`, and `unavailable-content` and reads `newsStore` (that is `layout`), and `news` uses `Url`, `DateTime`, and `StringValidator`, resolves each source's logo with `LogoDevApi`, and normalizes HTTP errors with `errorInterceptor`. The next two diagrams zoom into one context each and never draw a box that belongs to the other one.

    ```
    git add .
    git commit -m "docs: add C4 component diagram by bounded context."
    ```

18. **Model the news bounded context's components (C4).** One level deeper than the previous step, into the `news` box specifically: not "what does the SPA divide into" but "how is `news` itself divided". It is split by DDD layer. A C4 component is a set of files or classes, never a single class (one class belongs in the class diagram, the last step of this section). So Presentation has one box per Vue component, each one the `.vue` file that holds its script, template, and style, named the way you use it as a tag in a template (`source-list`, not a class name, because a Vue component is not a class), while Application is the single store `newsStore`, and Domain and Infrastructure group the classes that share one responsibility, named after the real folder that holds them (`news/domain/model`, `news/infrastructure`), with their classes listed in the description. Only news components are drawn here, plus NewsAPI.org. The arrows point toward the domain.

    <details>
    <summary>docs/c4/components-frontend-news.puml</summary>

    ```
    @startuml "Components-Layers"
    !includeurl https://raw.githubusercontent.com/plantuml-stdlib/C4-PlantUML/master/C4_Component.puml

    title CatchUp - News Component Diagram (DDD Layers)

    System_Ext(newsapi, "NewsAPI.org", "Provides news sources and their top headlines")

    Container_Boundary(news, "News Bounded Context") {
        Boundary(presentation, "Presentation") {
            Component(source_list, "source-list", "Vue Component", "source-list.vue: lists the sources in a drawer and relays which one was chosen")
            Component(source_item, "source-item", "Vue Component", "source-item.vue: one source, chosen with a click")
            Component(source_summary, "source-summary", "Vue Component", "source-summary.vue: one source in full, opened as a popover")
            Component(article_list, "article-list", "Vue Component", "article-list.vue: lists the articles of the current source")
            Component(article_item, "article-item", "Vue Component", "article-item.vue: one article, with read more, about the source, and share")
            Component(unavailable_content, "unavailable-content", "Vue Component", "unavailable-content.vue: the fallback shown when there are no articles, with the errors behind it")
        }
        Boundary(application, "Application") {
            Component(news_store, "newsStore", "Vue class with private state, entities in shallowRef", "newsStore, news.store.js: the sources, the articles cached by source, the errors, and the current source")
        }
        Boundary(domain, "Domain") {
            Component(news_domain_model, "news/domain/model", "Entities", "Source (source.entity.js), Article (article.entity.js): what makes a source and an article valid")
        }
        Boundary(infrastructure, "Infrastructure") {
            Component(news_infrastructure, "news/infrastructure", "HTTP, Assemblers, Resources", "NewsApi (news-api.js), SourceAssembler (source.assembler.js), ArticleAssembler (article.assembler.js), news-resources.js: fetches from the provider, the assemblers turn its resources into entities")
        }
    }

    Rel(source_list, source_item, "Renders one per source")
    Rel(article_list, article_item, "Renders one per article")
    Rel(article_item, source_summary, "Renders and toggles as a popover")
    Rel(source_item, news_domain_model, "Displays")
    Rel(source_summary, news_domain_model, "Displays")
    Rel(article_item, news_domain_model, "Displays")
    Rel(news_store, news_domain_model, "Holds")
    Rel(news_store, news_infrastructure, "Loads sources and articles through")
    Rel(news_infrastructure, news_domain_model, "Builds")
    Rel(news_infrastructure, newsapi, "Fetches sources and articles from [HTTPS]")
    @enduml
    ```
    </details>

    **Note:** no C4 diagram for what NewsAPI.org or Logo.dev look like on the inside, they are `System_Ext`, systems this project doesn't own and has no visibility into past their public API. C4 only models what is actually yours to draw.

    **Note:** `newsStore` reaches `NewsApi`, `SourceAssembler`, and `ArticleAssembler` (all in the `news/infrastructure` component) directly, it imports and instantiates the classes with no interface in between. The dependency points from Application to Infrastructure, the opposite of a strict layered design, where Application would declare a port and the API client would implement it. The guide keeps that simplicity, and the diagram shows the dependency as it is.

    ```
    git add .
    git commit -m "docs: add C4 news component diagram."
    ```

19. **Model the shared kernel's components (C4).** The same zoom level as the previous step, the other box from `components-frontend.puml`: how `shared` is divided internally, by DDD layer, with the same rule for what counts as a component. Domain and Infrastructure each group the classes that share one responsibility, named after their real folder. Only shared components are drawn here, plus Logo.dev.

    <details>
    <summary>docs/c4/components-frontend-shared.puml</summary>

    ```
    @startuml "Components-Shared"
    !includeurl https://raw.githubusercontent.com/plantuml-stdlib/C4-PlantUML/master/C4_Component.puml

    title CatchUp - Shared Component Diagram (DDD Layers)

    System_Ext(logodev, "Logo.dev", "Resolves a source's logo from its website domain")

    Container_Boundary(shared, "Shared Kernel") {
        Boundary(presentation, "Presentation") {
            Component(layout, "layout", "Vue Component", "layout.vue (hosted by the root app.vue): the frame every screen lives in")
            Component(language_switcher, "language-switcher", "Vue Component", "language-switcher.vue: toggles English and Spanish")
            Component(footer_content, "footer-content", "Vue Component", "footer-content.vue: the attribution notice")
        }
        Boundary(domain, "Domain") {
            Component(shared_domain_model, "shared/domain/model", "Value Objects", "Url (url.js), DateTime (date-time.js), StringValidator (string-validator.js): self-validating values and rules any context can use")
        }
        Boundary(infrastructure, "Infrastructure") {
            Component(shared_infrastructure, "shared/infrastructure", "HTTP, Logo URLs", "LogoDevApi (logo-dev-api.js), errorInterceptor (error.interceptor.js): builds a source's logo URL from its website domain, and normalizes HTTP errors")
        }
    }

    Rel(layout, language_switcher, "Renders")
    Rel(layout, footer_content, "Renders")
    Rel(shared_infrastructure, logodev, "Points logo image URLs at [HTTPS]")
    @enduml
    ```
    </details>

    **Note:** `Layout` is the one component of shared that depends on news: it imports `newsStore`, `SourceList`, `ArticleList`, and `UnavailableContent`, so a dependency goes from the kernel to a bounded context. That relation is drawn in `components-frontend.puml`, not here. A pure shared kernel would not know news exists, `Layout` sits in shared because it is the frame of the whole app, not because it is a reusable utility. `shared/domain/model` and `shared/infrastructure` depend on nothing in news, and `LogoDevApi` receives a `Url` without importing it.

    ```
    git add .
    git commit -m "docs: add C4 shared component diagram."
    ```

20. **Go one level deeper than C4: the class diagram.** C4 stops at Components on purpose, it never shows individual classes or their members. The actual classes, fields, and methods this guide builds are one level of detail past what C4 draws, in a plain (non-C4) PlantUML class diagram.

    <details>
    <summary>docs/class-diagram.puml</summary>

    ```plantuml
    @startuml
    ' Class diagram for CatchUp Application

    package "news.domain.model" {
      class Article << Entity >> {
        - author: string
        - title: string
        - description: string
        - url: Url
        - urlToImage: Url
        - publishedAt: DateTime
        - source: Source
        + getFormatedPublishedAt(): string
      }
      class Source << Entity >> {
        - id: string
        - name: string
        - description: string
        - url: Url
        - category: string
        - language: string
        - country: string
        - urlToLogo: string
      }
    }

    package "news.application" {
      class newsStore << Store >> {
        + sources: Source[]
        + articles: Article[]
        + errors: string[]
        + currentSource: Source
        + setCurrentSource(source): void
        + loadSources(): void
        + loadArticlesForCurrentSource(): void
      }
    }

    package "news.infrastructure" {
      class NewsApi << Adapter >> {
        + getSources(): Promise
        + getArticlesForSourceId(sourceId: string): Promise
      }
      class ArticleAssembler << Assembler >> {
        - #source: Source
        - #sourceAssembler: SourceAssembler
        + toEntityFromResource(resource: ArticleResource): Article
        + toEntitiesFromResponse(response: AxiosResponse): Article[]
      }
      class SourceAssembler << Assembler >> {
        - #logoApi: LogoDevApi
        + toEntitiesFromResponse(response: AxiosResponse): Source[]
        + toEntityFromResource(resource: SourceResource): Source
      }
      interface ArticleResource << Resource >> << (R,#FF7700) >> {
        + title: string
        + description: string
        + url: string
        + urlToImage: string
        + publishedAt: string
        + source: SourceResource
      }
      interface SourceResource << Resource >> << (R,#FF7700) >> {
        + id: string
        + name: string
        + description: string
        + url: string
        + category: string
        + language: string
        + country: string
      }
    }

    package "news.presentation.components" {
      class ArticleItem << Component >> {
        - article: Article
        - sourceSummary: SourceSummary
        + toggleSourceSummary(event): void
        + shareArticle(): void
        + articleShared(): void <<event>>
      }
      class ArticleList << Component >> {
        - articles: Article[]
      }
      class SourceItem << Component >> {
        - source: Source
        + emitSourceSelectedEvent(): void
        + sourceSelected(): void <<event>>
      }
      class SourceList << Component >> {
        - visible: Boolean
        - sources: Source[]
        + emitSourceSelectedEvent(source): void
        + onUpdateVisible(value): void
        + sourceSelected(): void <<event>>
        + updateVisible(): void <<event>>
      }
      class SourceSummary << Component >> {
        - source: Source
        + toggle(event): void
      }
      class UnavailableContent << Component >> {
        - errors: string[]
      }
    }

    package "shared.domain.model" {
      class DateTime << ValueObject >> {
        - #date: Date
        + isFuture(): boolean
        + format(locale, options): string
        + toDate(): Date
        + toISOString(): string
        + valueOf(): number
        {static} + now(): DateTime
      }
      class Url << ValueObject >> {
        - #url: string
        + isEmpty(): boolean
        + toString(): string
        + equals(other): boolean
        {static} + isValidUrl(url): boolean
      }
      class StringValidator << ValueObject >> {
        {static} + isNotEmptyString(value): boolean
      }
    }

    package "shared.infrastructure" {
      class LogoDevApi << Adapter >> {
        + getUrlToLogo(url): string
      }
      class errorInterceptor << HttpInterceptor >> {
        + onResponse(response): AxiosResponse
        + onError(error): Promise
      }
    }

    package "shared.presentation.components" {
      class FooterContent << Component >> {
        ' static content
      }
      class LanguageSwitcher << Component >> {
        ' uses useI18n
      }
      class Layout << Component >> {
        - drawerVisible: boolean
        - sources: Source[]
        - errors: string[]
        - articles: Article[]
        + toggleDrawer(): void
        + setSource(source): void
        + onMounted(): void
      }
    }

    class App << Component >>

    ' Relationships outside packages
    Article --> Source
    Article --> "shared.domain.model.Url"
    Article --> "shared.domain.model.DateTime"
    Source --> "shared.domain.model.Url"
    newsStore ..> "news.domain.model.Article" : uses
    newsStore ..> "news.domain.model.Source" : uses
    newsStore ..> "news.infrastructure.NewsApi" : uses
    newsStore ..> "news.infrastructure.ArticleAssembler" : uses
    newsStore ..> "news.infrastructure.SourceAssembler" : uses
    NewsApi ..> "shared.infrastructure.errorInterceptor" : uses
    ArticleAssembler ..> "news.domain.model.Article" : creates
    ArticleAssembler ..> SourceAssembler : uses
    ArticleAssembler ..> ArticleResource : uses
    SourceAssembler ..> "news.domain.model.Source" : creates
    SourceAssembler ..> "shared.infrastructure.LogoDevApi" : uses
    SourceAssembler ..> SourceResource : uses
    ArticleResource --> SourceResource
    ArticleList --> ArticleItem : uses
    ArticleItem --> Article : uses
    ArticleItem --> SourceSummary : uses
    SourceList --> SourceItem : uses
    SourceItem --> Source : uses
    SourceSummary --> Source : uses
    Layout --> FooterContent : uses
    Layout --> LanguageSwitcher : uses
    Layout --> "news.presentation.components.SourceList" : uses
    Layout --> "news.presentation.components.ArticleList" : uses
    Layout --> "news.presentation.components.UnavailableContent" : uses
    Layout --> newsStore : uses
    App --> Layout : uses

    @enduml
    ```
    </details>

    **Note:** `Article` and `Source` are entities, not value objects, even though most of their fields never change after construction, they have a real identity concept (`Source.id`, and `Article` is inherently tied to one specific `Source`), which is what tells an entity apart from a value object here.

    If it shows an error instead of a diagram, see [Appendix: If a PlantUML diagram doesn't render](#if-a-plantuml-diagram-doesnt-render).

    ```
    git add .
    git commit -m "docs: add class diagram."
    ```

21. **Connect to GitHub.**

    ```
    gh repo create <org>/catch-up --private --source=. --remote=origin --push --description "A news app to help you catch up on the latest headlines, illustrating Domain-Driven Design with Vue."
    ```

    No `gh`? See [Appendix: Creating the repo without the GitHub CLI](#creating-the-repo-without-the-github-cli).

22. **Install the Git Flow Helper plugin.** WebStorm → `Settings`/`Preferences` → `Plugins` → search **Git Flow Helper** → `Install` → restart if asked.

23. **Initialize Git Flow.** Git Flow Helper widget (bottom status bar) → `Init`. Accept the default branch prefixes (`feature/`, `release/`, `hotfix/`), main branch `main`, development branch `develop`.

    **Note:** you have already used Git Flow in earlier guides this course, so from here on this guide keeps every `Feature Start`/`Feature Publish`/`Feature Finish` step to one line, without repeating what each button does or which checkboxes to set. If you need the full walkthrough again (the widget's exact menu path, the `Integrate Immediately` / `Keep remote branch when finished` options), it is unchanged from those earlier guides.

---

## Browse News Sources (US001)

A visitor opens the app and sees a drawer listing every available news source, with the first one already active. This story builds the domain from the ground up: the `Source` entity first, then the `StringValidator` and `Url` it turned out to need, then the infrastructure that fetches real sources, then the components that display them.

1. **Start the feature `register-source-browsing`.**

2. **See the component tree this story builds.** This is the picture to keep in mind before writing code: every component the story builds, who owns what state, and how data flows between parent and child. `:prop` binds an input down, `@event` binds an output back up.

   ```
   +-----+
   | app |
   +-----+
       |
       +----------------------------------+
       | layout                           |
       | State (from newsStore):          |
       |   sources: ComputedRef<Source[]> |
       |   drawerVisible: Ref<boolean>    |
       +----------------------------------+
           |
           +-------------------------------------------+
           | source-list                               |
           | Input:  :visible (v-model), :sources      |
           | Output: @source-selected, @update:visible |
           +-------------------------------------------+
               |
               +--------------------------+
               | source-item              |
               | Input:  :source          |
               | Output: @source-selected |
               +--------------------------+
   ```

3. **Create the `Source` entity, fields and constructor.** Right-click `src` → `New` → `JavaScript File` → type `news/domain/model/source.entity` → Enter (WebStorm adds the `.js` and creates the folders). This is the aggregate the whole story is about, it validates `id` and `name`, the two fields nothing downstream can work without, and stores both trimmed, so `"  bbc-news "` and `"bbc-news"` end up as the same source.

   <details>
   <summary>src/news/domain/model/source.entity.js (so far)</summary>

   ```javascript
   export class Source {
       #id;
       #name;
       #description;
       #url;
       #category;
       #language;
       #country;
       #urlToLogo;

       constructor({id = "", name = "", description = "", url = "", category = "", language = "", country = "", urlToLogo = ""}) {
           if (!StringValidator.isNotEmptyString(id)) throw new Error('Source id must be a non-empty string');
           if (!StringValidator.isNotEmptyString(name)) throw new Error('Source name must be a non-empty string');

           this.#id = id.trim();
           this.#name = name.trim();
           this.#description = description;
           this.#url = url instanceof Url ? url : new Url(url);
           this.#category = category;
           this.#language = language;
           this.#country = country;
           this.#urlToLogo = urlToLogo;
       }
   }
   ```
   </details>

   **Note:** file names below carry a type suffix, `.entity.js`, this project's own convention for making the kind of domain object obvious from the file name alone, not a requirement of JavaScript or Vue.

   **Note:** real private fields, `#id`/`#name`/and so on, same as `Url` and `DateTime`. `Source` instances end up inside `NewsStore`, and a deep `reactive()` or `ref()` would wrap them in a `Proxy`, so a `#field` read would throw against that `Proxy`. `NewsStore` keeps them in a `shallowRef` instead of giving up `#`: Vue tracks the replacement of the list, never the entities inside it. ADR-0003 in `## Release` has the full reasoning.

   **Note:** `urlToLogo` is a constructor parameter, not a field you set after the fact. The temptation is `const source = new Source({...}); source.urlToLogo = theRealUrl;`, construct first, patch after, but `Source` has no setter for `urlToLogo`, so the assignment throws in strict mode (ES modules are always strict) and is silently ignored otherwise. Whoever creates a `Source` resolves the logo URL first, then passes everything into one constructor call. ADR-0004 in `## Release` covers why.

   **Note:** `Source` is immutable by design: private fields, only getters, no operation that changes them, and everything, `urlToLogo` included, is resolved before the constructor runs.

   **Note:** until the next two steps exist, the IDE underlines `StringValidator` and `Url` in the constructor as undefined. That is expected: writing the model is how you discover which support classes it needs, and the next two steps create them.

   **Note:** no commit here, `Source` still needs `StringValidator`, `Url`, and its read accessors.

4. **Create the `StringValidator` utility.** Right-click `src` → `New` → `JavaScript File` → type `shared/domain/model/string-validator` → Enter (WebStorm adds the `.js`). `Source`'s constructor calls it to validate `id` and `name`, so it comes next. A small, static-only class: no instance ever gets created, it exists purely to hold string-checking rules shared by every entity that validates one.

   <details>
   <summary>src/shared/domain/model/string-validator.js</summary>

   ```javascript
   export class StringValidator {
       static isString(value) {
           return typeof value === 'string' || value instanceof String;
       }

       static isNotEmptyString(value) {
           return this.isString(value) && value.trim().length > 0;
       }
   }
   ```
   </details>

   **Note:** back in `source.entity.js`, `StringValidator` is still underlined until it is imported. Put the cursor on it and press `Option+Enter` on macOS or `Alt+Enter` on Windows, then pick the option that adds the import; the underline disappears. The `Source` file is not complete until both imports are there.

   ```
   git add .
   git commit -m "feat(shared): add string validator utility."
   ```

5. **Create the `Url` value object.** Right-click `src` → `New` → `JavaScript File` → type `shared/domain/model/url` → Enter (WebStorm adds the `.js`). `Source` also stores a `Url` for its website, so this is the last support class it needs. A malformed URL never throws, it just becomes an empty `Url`, `""`. `toString()`/`valueOf()` expose the raw string, `isEmpty()` is the check most callers actually need, and `equals()` compares by value.

   <details>
   <summary>src/shared/domain/model/url.js</summary>

   ```javascript
   export class Url {
       #value;

       static isValidUrl(url) {
           if (typeof url !== 'string' && !(url instanceof String)) return false;
           if (URL.canParse) {
               return URL.canParse(url);
           }
           try {
               new URL(url);
               return true;
           } catch (_) {
               return false;
           }
       }

       constructor(value) {
           this.#value = Url.isValidUrl(value) ? value : '';
       }

       toString() {
           return this.#value;
       }

       isEmpty() {
           return this.#value === '';
       }

       valueOf() {
           return this.#value;
       }

       equals(other) {
           return other instanceof Url && this.#value === other.toString();
       }
   }
   ```
   </details>

   **Note:** `Url` uses a native `#value` private field, not `_value`, same as `Source` and `Article`. It never goes into Vue on its own: it only lives inside a `Source` or an `Article`, and those enter `newsStore` in a `shallowRef`, so Vue never wraps them in a `Proxy`. ADR-0003 in `## Release` has the full reasoning.

   **Note:** back in `source.entity.js`, `Url` is still underlined until it is imported. Put the cursor on it and press `Option+Enter` on macOS or `Alt+Enter` on Windows, then pick the option that adds the import; the underline disappears. The `Source` file is not complete until both imports are there.

   ```
   git add .
   git commit -m "feat(shared): add Url value object."
   ```

6. **Add the read accessors.** One getter per field, exposing exactly what the constructor validated and stored.

   <details>
   <summary>src/news/domain/model/source.entity.js (Full file)</summary>

   ```javascript
   import {StringValidator} from "../../../shared/domain/model/string-validator.js";
   import {Url} from "../../../shared/domain/model/url.js";

   export class Source {
       #id;
       #name;
       #description;
       #url;
       #category;
       #language;
       #country;
       #urlToLogo;

       constructor({id = "", name = "", description = "", url = "", category = "", language = "", country = "", urlToLogo = ""}) {
           if (!StringValidator.isNotEmptyString(id)) throw new Error('Source id must be a non-empty string');
           if (!StringValidator.isNotEmptyString(name)) throw new Error('Source name must be a non-empty string');

           this.#id = id.trim();
           this.#name = name.trim();
           this.#description = description;
           this.#url = url instanceof Url ? url : new Url(url);
           this.#category = category;
           this.#language = language;
           this.#country = country;
           this.#urlToLogo = urlToLogo;
       }

       get id() {
           return this.#id;
       }

       get name() {
           return this.#name;
       }

       get description() {
           return this.#description;
       }

       get url() {
           return this.#url;
       }

       get category() {
           return this.#category;
       }

       get language() {
           return this.#language;
       }

       get country() {
           return this.#country;
       }

       get urlToLogo() {
           return this.#urlToLogo;
       }
   }
   ```
   </details>

   ```
   git add .
   git commit -m "feat(news): add Source entity."
   ```

7. **Create the `LogoDevApi` helper.** Right-click `src` → `New` → `JavaScript File` → type `shared/infrastructure/logo-dev-api` → Enter. It builds a Logo.dev image URL from a source's website host, nothing more.

   **Tip:** open [`https://img.logo.dev/bbc.co.uk?token=pk_bufKzaXPQFeNkMz5gxZWAA`](https://img.logo.dev/bbc.co.uk?token=pk_bufKzaXPQFeNkMz5gxZWAA) in the browser first, this is exactly the URL `LogoDevApi` builds and hands to an `<img>` tag.

   <details>
   <summary>src/shared/infrastructure/logo-dev-api.js</summary>

   ```javascript
   const logoApiUrl = import.meta.env.VITE_LOGO_API_URL;
   const apiKey = import.meta.env.VITE_LOGO_PUBLISHABLE_API_KEY;

   export class LogoDevApi {
       getUrlToLogo = url => `${logoApiUrl}/${new URL(url.toString()).host}?token=${apiKey}`;
   }
   ```
   </details>

   **Note:** `getUrlToLogo` takes the `Url` it needs, not the whole `Source`. It only ever reads the website host, asking for less than the whole entity keeps this class from silently growing a dependency on fields it does not use.

   ```
   git add .
   git commit -m "feat(shared): add Logo.dev API helper."
   ```

8. **Create the news API response shapes.** Right-click `src` → `New` → `JavaScript File` → type `news/infrastructure/news-resources` → Enter. Plain JSDoc `@typedef`s describing what NewsAPI actually returns, no runtime code, just the types the assemblers and the store below reference. They are the one exception to "comments go last": the editor reads them for autocompletion and checking, so the code depends on them and they are written here, not in the final comments step.

   **Tip:** open [`https://newsapi.org/v2/sources?apiKey=0d5b87d6eed74a768b7f2f7a3ca1bafb`](https://newsapi.org/v2/sources?apiKey=0d5b87d6eed74a768b7f2f7a3ca1bafb) in the browser, this raw JSON is exactly what `SourceResource` and `SourcesResponse` model below.

   **Tip:** open [`https://newsapi.org/v2/top-headlines?sources=bbc-news&apiKey=0d5b87d6eed74a768b7f2f7a3ca1bafb`](https://newsapi.org/v2/top-headlines?sources=bbc-news&apiKey=0d5b87d6eed74a768b7f2f7a3ca1bafb) in the browser, this raw JSON is exactly what `ArticleResource` and `ArticlesResponse` model below.

   <details>
   <summary>src/news/infrastructure/news-resources.js</summary>

   ```javascript
   /**
    * @typedef {Object} SourceResource
    * @property {string} [id]
    * @property {string} [name]
    * @property {string} [description]
    * @property {string} [url]
    * @property {string} [category]
    * @property {string} [language]
    * @property {string} [country]
    */

   /**
    * @typedef {Object} SourcesResponse
    * @property {string} status
    * @property {SourceResource[]} sources
    */

   /**
    * @typedef {Object} ArticleResource
    * @property {string} [title]
    * @property {string} [description]
    * @property {string} [url]
    * @property {string} [urlToImage]
    * @property {string} [publishedAt]
    * @property {SourceResource} [source]
    */

   /**
    * @typedef {Object} ArticlesResponse
    * @property {string} status
    * @property {ArticleResource[]} articles
    */

   export {}
   ```
   </details>

   **Note:** `export {}` at the end is what makes this a module instead of a global script, JSDoc `@typedef`s alone do not require one, but an explicit empty export keeps `import "./news-resources.js"` meaningful elsewhere.

   ```
   git add .
   git commit -m "feat(news): add API response type definitions."
   ```

9. **Create the `errorInterceptor`.** Right-click `src` → `New` → `JavaScript File` → type `shared/infrastructure/error.interceptor` → Enter. A pair of Axios interceptor functions: one for a successful response, one that turns any kind of Axios error into a single, user-facing message string.

   <details>
   <summary>src/shared/infrastructure/error.interceptor.js</summary>

   ```javascript
   export const errorInterceptor = {
       onResponse: (response) => response,

       onError: (error) => {
           let message;

           if (error.response) {
               console.error("Data:", error.response.data);
               console.error("Status:", error.response.status);
               console.error("Headers:", error.response.headers);

               message = error.response.data["message"] || `Error ${error.response.status}: ${error.response.statusText}`;
           } else if (error.request) {
               console.error("Request:", error.request);
               message = "No response received from the server. Please check your internet connection.";
           } else {
               console.error("Error Message:", error.message);
               message = error.message;
           }

           return Promise.reject(message);
       }
   };
   ```
   </details>

   **Note:** three branches inside `onError`, in order: `error.response` means the server answered with an error status (a `404`, a `401`), `error.request` means the request went out but nothing came back (offline, a dropped connection), and the last branch means the request was never even sent (a bug in how it was built). Each one produces a message meant for a person, not a stack trace.

   ```
   git add .
   git commit -m "feat(shared): add Axios error interceptor."
   ```

10. **Create the `NewsApi` client.** Right-click `src` → `New` → `JavaScript File` → type `news/infrastructure/news-api` → Enter. One `axios` instance, configured once, shared by both of its methods.

    <details>
    <summary>src/news/infrastructure/news-api.js</summary>

    ```javascript
    import axios from "axios";
    import "./news-resources.js";
    import {errorInterceptor} from "../../shared/infrastructure/error.interceptor.js";

    const newsApi               = import.meta.env.VITE_NEWS_API_URL;
    const apiKey                = import.meta.env.VITE_NEWS_API_KEY;
    const sourcesEndpoint       = import.meta.env.VITE_SOURCES_ENDPOINT_PATH;
    const topHeadlinesEndpoint  = import.meta.env.VITE_TOP_HEADLINES_ENDPOINT_PATH;

    const http = axios.create({
        baseURL: newsApi,
        params: {
            apiKey: apiKey,
        },
    })

    http.interceptors.response.use(errorInterceptor.onResponse, errorInterceptor.onError);

    export class NewsApi {

        getSources = () => http.get(`${sourcesEndpoint}`);

        getArticlesForSourceId = sourceId => http.get(`${topHeadlinesEndpoint}`, {params: {sources: sourceId}});

    }
    ```
    </details>

    **Note:** `apiKey` travels as a query parameter on every request (`http.create({..., params: {apiKey}})`), because that is how NewsAPI's free tier expects it. `getArticlesForSourceId` is not used yet, US002 is the first to call it, it is written now because it lives on the same class and the same Axios instance as `getSources`.

    ```
    git add .
    git commit -m "feat(news): add NewsApi client."
    ```

11. **Create the `SourceAssembler`.** Right-click `src` → `New` → `JavaScript File` → type `news/infrastructure/source.assembler` → Enter. Turns a raw API resource into a `Source`, resolving its logo URL first, since `Source` only ever accepts an already-known `urlToLogo`.

    <details>
    <summary>src/news/infrastructure/source.assembler.js (so far)</summary>

    ```javascript
    import {Source} from "../domain/model/source.entity.js";
    import {Url} from "../../shared/domain/model/url.js";
    import {LogoDevApi} from "../../shared/infrastructure/logo-dev-api.js";
    import "./news-resources.js";

    export class SourceAssembler {
        #logoApi;

        constructor() {
            this.#logoApi = new LogoDevApi();
        }

        toEntitiesFromResponse(response) {
            if (response.data.status !== "ok") {
                console.error(`${response.data["status"]},  ${response.data["code"]}, ${response.data["message"]}`);
                return [];
            }
            const sourcesResponse = response.data;
            return sourcesResponse.sources.map((source) => {
                try {
                    return this.toEntityFromResource(source);
                } catch (error) {
                    console.error('Validation error for source:', error.message, source);
                    return null;
                }
            }).filter(source => source !== null);
        }

        toEntityFromResource(resource) {
            const url = resource.url instanceof Url ? resource.url : new Url(resource.url);
            const urlToLogo = !url.isEmpty() ? this.#logoApi.getUrlToLogo(url) : '';
            return new Source({...resource, url, urlToLogo});
        }
    }
    ```
    </details>

    **Note:** `toEntityFromResource` resolves `url` and `urlToLogo` as local variables *before* calling `new Source(...)`, then passes both in with the rest of the resource's fields. This is the "resolve everything first, construct once" shape ADR-0004 describes, `Source` is never patched after the fact.

    **Note:** one invalid source in the response does not sink the whole list, `toEntitiesFromResponse` catches the error per item and filters the failed ones out, logging why.

    ```
    git add .
    git commit -m "feat(news): add SourceAssembler."
    ```

12. **Create the `NewsStore`, sources only for now.** Right-click `src` → `New` → `JavaScript File` → type `news/application/news.store` → Enter. It is the application service every component below reads from and calls into. A class whose state is private (`#`), read through getters: domain entities go in `shallowRef`, so Vue never wraps them in a `Proxy`; plain UI state, like the error messages, goes in a normal `ref`.

    <details>
    <summary>src/news/application/news.store.js (so far)</summary>

    ```javascript
    import {computed, ref, shallowRef} from "vue";
    import {NewsApi} from "../infrastructure/news-api.js";
    import {SourceAssembler} from "../infrastructure/source.assembler.js";

    class NewsStore {
        #newsApi = new NewsApi();
        #sourceAssembler = new SourceAssembler();
        #sources = shallowRef([]);
        #currentSource = shallowRef(undefined);
        #errors = ref([]);
        #currentSourceId = computed(() => this.#currentSource.value?.id);

        get sources() {
            return this.#sources.value;
        }

        get errors() {
            return this.#errors.value;
        }

        get currentSource() {
            return this.#currentSource.value;
        }

        get currentSourceId() {
            return this.#currentSourceId.value;
        }

        setCurrentSource(source) {
            this.#currentSource.value = source;
        }

        loadSources() {
            if (this.#sources.value.length > 0) return;
            this.#errors.value = [];
            this.#newsApi.getSources().then(response => {
                const sources = this.#sourceAssembler.toEntitiesFromResponse(response);
                this.#sources.value = sources;
                if (sources.length > 0) this.setCurrentSource(sources[0]);
            }).catch(message => {
                this.#errors.value.push(message);
                this.#sources.value = [];
            });
        }
    }

    export const newsStore = new NewsStore();
    ```
    </details>

    **Note:** the state lives in `#sources`, `#currentSource` and `#errors`, private fields, so no component can assign `newsStore.sources = ...` from outside; components only read through the getters and change state by calling `setCurrentSource()` or `loadSources()`.

    **Note:** `newsStore` is exported as a single, shared instance, not created per component. Every component that imports it gets the exact same object, that is what makes selecting a source in one component show up in another. The instance is never wrapped in `reactive()`, the refs inside it already carry the reactivity, and the getters read `.value`, so any `computed()` that calls a getter keeps updating.

    **Note:** `loadSources()` returns early when the sources are already there, so calling it more than once (every time `Layout` mounts, for instance) only ever fetches once. `currentSourceId`, not the whole `Source`, is what the navigation list needs to know which row to mark active.

    **Note:** `setCurrentSource` does nothing with articles yet, US002 is the one that adds that. Right now, choosing a source only updates which one is marked active.

    ```
    git add .
    git commit -m "feat(news): add NewsStore, sources only."
    ```

13. **Create the `SourceItem` component's template.** Right-click `src` → `New` → `Vue Single-File Component`. A dropdown asks `Composition API` or `Options API`, pick `Composition API`. Type the full path starting from the bounded context, `news/presentation/components/source-item` → Enter. An avatar with the source's logo, and its name next to it; clicking anywhere in the row selects it.

    The row calls a handler when it is clicked, and shows the source's logo as an avatar:

    ```vue
    <div @click="emitSourceSelectedEvent" class="flex align-content-start flex-wrap cursor-pointer">
      <pv-avatar :aria-label="source.name" :image="source.urlToLogo" shape="circle"/>
    </div>
    ```

    <details>
    <summary>src/news/presentation/components/source-item.vue (template)</summary>

    ```vue
    <template>
      <div class="m-4">
        <div @click="emitSourceSelectedEvent" class="flex align-content-start flex-wrap cursor-pointer hover:bg-emphasis p-2 border-round transition-colors transition-duration-150">
        <span  class="flex align-items-center justify-content-center mr-2">
              <pv-avatar :aria-label="source.name"
                         :image="source.urlToLogo"
                         shape="circle"/>
        </span>
          <span  class="flex align-items-center justify-content-center font-medium">
              {{source.name}}
        </span>
        </div>
      </div>
    </template>
    ```
    </details>

    **Note:** `source` and `emitSourceSelectedEvent` do not exist anywhere yet, WebStorm shows both unresolved in the template. That is expected, script and template are separate concerns in a `<script setup>` component: nothing here compiles against the other the way a strictly-typed Angular template would. They get defined next, one at a time.

14. **Add the `source` prop.**

    The component receives the source it shows as a required prop:

    ```javascript
    const { source } = defineProps({ source: { type: Source, required: true } });
    ```

    <details>
    <summary>src/news/presentation/components/source-item.vue (so far)</summary>

    ```vue
    <script setup lang="js">
      import {Source} from "../../domain/model/source.entity.js";

      const { source } = defineProps({ source: { type: Source, required: true } });
    </script>

    <template>
      <div class="m-4">
        <div @click="emitSourceSelectedEvent" class="flex align-content-start flex-wrap cursor-pointer hover:bg-emphasis p-2 border-round transition-colors transition-duration-150">
        <span  class="flex align-items-center justify-content-center mr-2">
              <pv-avatar :aria-label="source.name"
                         :image="source.urlToLogo"
                         shape="circle"/>
        </span>
          <span  class="flex align-items-center justify-content-center font-medium">
              {{source.name}}
        </span>
        </div>
      </div>
    </template>

    <style scoped>

    </style>
    ```
    </details>

    **Note:** `{{source.name}}` and `:image="source.urlToLogo"` in the template now resolve, `source` exists. `emitSourceSelectedEvent` is still unresolved, that's the last piece.

15. **Add `emitSourceSelectedEvent()`.**

    Clicking the row emits the chosen source upward through the event `source-selected`:

    ```javascript
    const emit  = defineEmits(['source-selected']);

    const emitSourceSelectedEvent = () => {
      emit('source-selected', source);
    };
    ```

    <details>
    <summary>src/news/presentation/components/source-item.vue (Full file)</summary>

    ```vue
    <script setup lang="js">
      import {Source} from "../../domain/model/source.entity.js";

      const { source } = defineProps({ source: { type: Source, required: true } });
      const emit  = defineEmits(['source-selected']);

      const emitSourceSelectedEvent = () => {
        emit('source-selected', source);
      };
    </script>

    <template>
      <div class="m-4">
        <div @click="emitSourceSelectedEvent" class="flex align-content-start flex-wrap cursor-pointer hover:bg-emphasis p-2 border-round transition-colors transition-duration-150">
        <span  class="flex align-items-center justify-content-center mr-2">
              <pv-avatar :aria-label="source.name"
                         :image="source.urlToLogo"
                         shape="circle"/>
        </span>
          <span  class="flex align-items-center justify-content-center font-medium">
              {{source.name}}
        </span>
        </div>
      </div>
    </template>

    <style scoped>

    </style>
    ```
    </details>

    **Note:** everything in the template now resolves. No styles for this component, the classes on the `<div>`s are PrimeFlex utility classes, applied inline, there is nothing left for `<style scoped>` to add.

    ```
    git add .
    git commit -m "feat(news): add SourceItem component."
    ```

16. **Create the `SourceList` component's template.** A PrimeVue drawer, listing one `SourceItem` per source, each one wired to the events it will emit:

    ```vue
    <source-item v-for="source in sources"
                 :key="source.id"
                 :source="source"
                 @source-selected="emitSourceSelectedEvent(source)"/>
    ```

    <details>
    <summary>src/news/presentation/components/source-list.vue (template)</summary>

    ```vue
    <template>
      <pv-drawer :visible="visible" @update:visible="emitVisibilityUpdatedEvent">
        <source-item v-for="source in sources"
                     :key="source.id"
                     :source="source"
                     @source-selected="emitSourceSelectedEvent(source)"/>
      </pv-drawer>
    </template>
    ```
    </details>

17. **Add the `visible` and `sources` props.**

    The drawer receives whether it is open and the sources it lists:

    ```javascript
    const { visible, sources } = defineProps({ visible: Boolean, sources: Array });
    ```

    <details>
    <summary>src/news/presentation/components/source-list.vue (so far)</summary>

    ```vue
    <script setup lang="js">
      import {Source} from "../../domain/model/source.entity.js";
      import SourceItem from "./source-item.vue";

      const { visible, sources } = defineProps({ visible: Boolean, sources: Array });
    </script>

    <template>
      <pv-drawer :visible="visible" @update:visible="emitVisibilityUpdatedEvent">
        <source-item v-for="source in sources"
                     :key="source.id"
                     :source="source"
                     @source-selected="emitSourceSelectedEvent(source)"/>
      </pv-drawer>
    </template>

    <style scoped>

    </style>
    ```
    </details>

    **Note:** `sources: Array`, plain and simple, not `Array[Source]`. `Array[Source]` reads like "an array of `Source`", but it is not valid Vue prop syntax, `Source` there would be read as a property key on the `Array` constructor function, which does not exist, so it silently resolves to `undefined` and validates nothing.

18. **Add the two emitted events.**

    The list emits two events, one so the parent can open or close the drawer, and one that relays the source a `SourceItem` reported:

    ```javascript
    const emit  = defineEmits(['source-selected', 'update:visible']);

    const emitVisibilityUpdatedEvent = (value) => {
      emit('update:visible', value);
    };

    const emitSourceSelectedEvent = source => {
      emit('source-selected', source);
    };
    ```

    <details>
    <summary>src/news/presentation/components/source-list.vue (Full file)</summary>

    ```vue
    <script setup lang="js">
      import {Source} from "../../domain/model/source.entity.js";
      import SourceItem from "./source-item.vue";

      const { visible, sources } = defineProps({ visible: Boolean, sources: Array });
      const emit  = defineEmits(['source-selected', 'update:visible']);

      const emitVisibilityUpdatedEvent = (value) => {
        emit('update:visible', value);
      };

      const emitSourceSelectedEvent = source => {
        emit('source-selected', source);
      };
    </script>

    <template>
      <pv-drawer :visible="visible" @update:visible="emitVisibilityUpdatedEvent">
        <source-item v-for="source in sources"
                     :key="source.id"
                     :source="source"
                     @source-selected="emitSourceSelectedEvent(source)"/>
      </pv-drawer>
    </template>

    <style scoped>

    </style>
    ```
    </details>

    **Note:** `update:visible` is Vue's naming convention for `v-model:visible` support, whoever uses `<source-list v-model:visible="...">` gets two-way binding for free, Vue wires the `visible` prop and the `update:visible` event together.

    ```
    git add .
    git commit -m "feat(news): add SourceList component."
    ```

19. **Register PrimeVue in `main.js`.** Right-click `src` → the `main.js` file already exists from Project Setup, open it. This is the one place the whole app's global plugins and components get wired up.

    <details>
    <summary>src/main.js (so far)</summary>

    ```javascript
    import { createApp } from 'vue'
    import './style.css'
    import App from './app.vue'
    import PrimeVue from 'primevue/config';
    import Material from '@primeuix/themes/material';
    import 'primeicons/primeicons.css';
    import 'primeflex/primeflex.css';
    import {Avatar, Button, Card, Drawer, Menu, Menubar, Popover, SelectButton, Toolbar, Tooltip} from "primevue";

    const primeUiLicenseKey = import.meta.env.VITE_PRIME_UI_LICENSE_KEY;

    createApp(App)
        .use(PrimeVue, { ripple: true, theme: { preset: Material }, license: primeUiLicenseKey })
        .component('pv-button', Button)
        .component('pv-select-button', SelectButton)
        .component('pv-avatar', Avatar)
        .component('pv-drawer', Drawer)
        .component('pv-card', Card)
        .component('pv-toolbar', Toolbar)
        .component('pv-menu', Menu)
        .component('pv-menubar', Menubar)
        .component('pv-popover', Popover)
        .directive('tooltip', Tooltip)
        .mount('#app')
    ```
    </details>

    **Note:** every PrimeVue component this whole app uses gets registered here in one place, globally, with a `pv-` prefix, `pv-avatar` and `pv-drawer` used just now among them, even though later user stories are what introduce `pv-card`, `pv-popover`, and the rest. Registering them all up front means no component file below ever needs its own PrimeVue import.

    ```
    git add .
    git commit -m "feat: register PrimeVue globally."
    ```

20. **Wire `SourceList` into `Layout`, template only for now.** Right-click `src` → `New` → `Vue Single-File Component` → `Composition API` → type `shared/presentation/components/layout` → Enter. A menu bar with a button that opens the source drawer.

    A button toggles the drawer, and the drawer itself is the `SourceList`:

    ```vue
    <pv-button icon="pi pi-bars" label="CatchUp" text @click="toggleDrawer" class="mr-2"/>
    <source-list :sources="sources"
                 v-model:visible="drawerVisible"
                 @source-selected="setSource"/>
    ```

    <details>
    <summary>src/shared/presentation/components/layout.vue (template)</summary>

    ```vue
    <template>
      <div class="layout-container">
        <header class="sticky-header">
          <pv-menubar>
            <template #start>
              <pv-button icon="pi pi-bars" label="CatchUp"
                         text @click="toggleDrawer" class="mr-2"/>
              <source-list :sources="sources"
                           v-model:visible="drawerVisible"
                           @source-selected="setSource"/>
            </template>
          </pv-menubar>
        </header>
      </div>
    </template>

    <style scoped>
    .layout-container {
      display: flex;
      flex-direction: column;
      min-height: 100vh;
    }

    .sticky-header {
      position: sticky;
      top: 0;
      z-index: 1000;
    }
    </style>
    ```
    </details>

21. **Add the drawer state and the sources view.**

    The script holds the drawer state and a `computed()` view over the store's sources:

    ```javascript
    const drawerVisible = ref(false);

    const toggleDrawer = () => {
      drawerVisible.value = !drawerVisible.value;
    };

    const sources = computed(() => newsStore.sources);
    ```

    <details>
    <summary>src/shared/presentation/components/layout.vue (so far)</summary>

    ```vue
    <script lang="js" setup>
    import {newsStore} from "../../../news/application/news.store.js";
    import SourceList from "../../../news/presentation/components/source-list.vue";
    import {ref, computed} from "vue";

    const drawerVisible = ref(false);

    const toggleDrawer = () => {
      drawerVisible.value = !drawerVisible.value;
    };

    const sources = computed(() => newsStore.sources);
    </script>

    <template>
      <div class="layout-container">
        <header class="sticky-header">
          <pv-menubar>
            <template #start>
              <pv-button icon="pi pi-bars" label="CatchUp"
                         text @click="toggleDrawer" class="mr-2"/>
              <source-list :sources="sources"
                           v-model:visible="drawerVisible"
                           @source-selected="setSource"/>
            </template>
          </pv-menubar>
        </header>
      </div>
    </template>

    <style scoped>
    .layout-container {
      display: flex;
      flex-direction: column;
      min-height: 100vh;
    }

    .sticky-header {
      position: sticky;
      top: 0;
      z-index: 1000;
    }
    </style>
    ```
    </details>

    **Note:** `sources` reads `newsStore.sources` through a `computed()`, not directly. `Layout` never reaches into `newsStore` from the template, it always goes through a `computed()` view or a method, the same discipline every component below follows.

22. **Add `setSource()` and load the sources on mount.**

    Choosing a source selects it in the store, and `onMounted` asks the store to load the sources:

    ```javascript
    const setSource = source => {
      newsStore.setCurrentSource(source);
      toggleDrawer();
    };

    onMounted(() => {
      newsStore.loadSources();
    });
    ```

    <details>
    <summary>src/shared/presentation/components/layout.vue (Full file)</summary>

    ```vue
    <script lang="js" setup>
    import {newsStore} from "../../../news/application/news.store.js";
    import SourceList from "../../../news/presentation/components/source-list.vue";
    import {ref, computed, onMounted} from "vue";

    const drawerVisible = ref(false);

    const toggleDrawer = () => {
      drawerVisible.value = !drawerVisible.value;
    };

    const sources = computed(() => newsStore.sources);

    const setSource = source => {
      newsStore.setCurrentSource(source);
      toggleDrawer();
    };

    onMounted(() => {
      newsStore.loadSources();
    });
    </script>

    <template>
      <div class="layout-container">
        <header class="sticky-header">
          <pv-menubar>
            <template #start>
              <pv-button icon="pi pi-bars" label="CatchUp"
                         text @click="toggleDrawer" class="mr-2"/>
              <source-list :sources="sources"
                           v-model:visible="drawerVisible"
                           @source-selected="setSource"/>
            </template>
          </pv-menubar>
        </header>
      </div>
    </template>

    <style scoped>
    .layout-container {
      display: flex;
      flex-direction: column;
      min-height: 100vh;
    }

    .sticky-header {
      position: sticky;
      top: 0;
      z-index: 1000;
    }
    </style>
    ```
    </details>

    **Note:** choosing a source also closes the drawer (`toggleDrawer()` right after `setCurrentSource`), so the visible list is not left covering the page once a choice is made. `onMounted` is what actually starts the whole app, nothing loads until `Layout` exists on the page.

23. **Show `Layout` from `app.vue`.**

    <details>
    <summary>src/app.vue</summary>

    ```vue
    <script setup>
      import Layout from "./shared/presentation/components/layout.vue";

    </script>

    <template>
      <layout/>
    </template>

    <style scoped>

    </style>
    ```
    </details>

    ```
    git add .
    git commit -m "feat: add Layout component, wired into app.vue."
    ```

24. **Run it.**

    ```
    npm run dev
    ```

    Open the local URL Vite prints. A "CatchUp" button opens a drawer with a real list of news sources, fetched live from NewsAPI. Click one, the drawer closes. Nothing else on the page changes yet, that's US002. Stop the server with `Ctrl+C`.

25. **Publish and finish the feature.**

---

## View Articles (US002)

Choosing a source now only marks it active. This story makes it load and show real articles: the `Article` entity and the `DateTime` value object it needs, the assembler that resolves an article's source, and the two components that display the list.

1. **Start the feature `view-articles`.**

2. **See the component tree this story builds.** `ArticleItem` and `ArticleList` join the tree, and `Layout` now also exposes `articles`.

   ```
   +-----+
   | app |
   +-----+
       |
       +------------------------------------+
       | layout                             |
       | State (from newsStore):            |
       |   sources: ComputedRef<Source[]>   |
       |   drawerVisible: Ref<boolean>      |
       |   articles: ComputedRef<Article[]> |
       +------------------------------------+
           |
           +-------------------------------------------+
           | source-list                               |
           | Input:  :visible (v-model), :sources      |
           | Output: @source-selected, @update:visible |
           +-------------------------------------------+
               |
               +--------------------------+
               | source-item              |
               | Input:  :source          |
               | Output: @source-selected |
               +--------------------------+
           |
           +-------------------+
           | article-list      |
           | Input:  :articles |
           +-------------------+
               |
               +-------------------------+
               | article-item            |
               | Input:  :article        |
               | Output: @article-shared |
               +-------------------------+
   ```

3. **Create the `Article` entity, fields and constructor.** Right-click `src` → `New` → `JavaScript File` → type `news/domain/model/article.entity` → Enter. It validates the title, the source, and the url, stores the title trimmed, resolves a `DateTime` from whatever it was given, and falls back to a placeholder image when none was provided. The placeholder is a named constant, `NO_IMAGE_URL`, not a literal buried in the middle of the constructor.

   <details>
   <summary>src/news/domain/model/article.entity.js (so far)</summary>

   ```javascript
   import {Source} from "./source.entity.js";
   import {StringValidator} from "../../../shared/domain/model/string-validator.js";
      import {Url} from "../../../shared/domain/model/url.js";

   const NO_IMAGE_URL = 'https://placehold.co/600x400?text=No+Image';

   export class Article {
       #author;
       #title;
       #description;
       #url;
       #urlToImage;
       #source;
       #publishedAt;

       constructor({author = '', title = '', description = '', url = '', urlToImage = '', source = null, publishedAt = ''}) {
           if (!StringValidator.isNotEmptyString(title)) throw new Error('Article title must be a non-empty string');
           if (!(source instanceof Source)) throw new Error('Article must have a resolved Source entity');

           let dateTime;
           try {
               dateTime = publishedAt instanceof DateTime ? publishedAt : new DateTime(publishedAt);
           } catch (e) {
               throw new Error('Article publishedAt must be a valid date');
           }
           if (dateTime.isFuture()) throw new Error('Article publishedAt cannot be in the future');

           const resolvedUrl = url instanceof Url ? url : new Url(url);
           if (resolvedUrl.isEmpty()) throw new Error('Article url must be a valid, non-empty URL');

           this.#author = author;
           this.#title = title.trim();
           this.#description = description;
           this.#url = resolvedUrl;
           const resolvedImage = urlToImage instanceof Url ? urlToImage : new Url(urlToImage);
           this.#urlToImage = resolvedImage.isEmpty() ? new Url(NO_IMAGE_URL) : resolvedImage;
           this.#source = source;
           this.#publishedAt = dateTime;
       }
   }
   ```
   </details>

   **Note:** `Url` never throws, an invalid string falls back to an empty `Url` (ADR-0002), so "the article has a url" cannot be checked by whether `new Url(url)` throws. The guard checks the result instead: `resolvedUrl.isEmpty()` means the provider sent nothing usable, and the constructor throws. `ArticleAssembler` already builds every item inside a `try`/`catch` and drops the ones that fail, so an article without a url never reaches the list with a dead "Read more" button.

   **Note:** like `Source`, `Article` is immutable by design: private fields, only getters, and its `Source` and `DateTime` arrive already built.

   **Note:** until the next step exists, the IDE underlines `DateTime` in the constructor as undefined. That is expected: writing the model is how you discover which support classes it needs, and the next step creates it. `Url`, `Source`, and `StringValidator` already exist from US001, so their imports are already there.

   **Note:** no commit here, `Article` still needs `DateTime` and its read accessors.

4. **Create the `DateTime` value object.** `Article` needed this one to resolve its `publishedAt`. Right-click `src` → `New` → `JavaScript File` → type `shared/domain/model/date-time` → Enter. An invalid date throws immediately, unlike `Url`'s "fall back to empty" approach, a date-time is either real or it is a bug in the caller. It also answers `isFuture()`, formats itself, hands out a copy of its `Date`, and has a `now()` factory.

   <details>
   <summary>src/shared/domain/model/date-time.js</summary>

   ```javascript
   export class DateTime {
       #date;

       constructor(value) {
           const date = new Date(value);
           if (isNaN(date.getTime())) {
               throw new Error('Invalid date-time value');
           }
           this.#date = date;
       }

       isFuture() {
           return this.#date > new Date();
       }

       format(locale = 'en-US', options = {
           year: 'numeric',
           month: '2-digit',
           day: '2-digit',
           hour: '2-digit',
           minute: '2-digit'
       }) {
           return this.#date.toLocaleDateString(locale, options);
       }

       toDate() {
           return new Date(this.#date.getTime());
       }

       toISOString() {
           return this.#date.toISOString();
       }

       valueOf() {
           return this.#date.getTime();
       }

       static now() {
           return new DateTime(new Date());
       }
   }
   ```
   </details>

   **Note:** `#date`, native private, not `_date`. `DateTime` is never itself the direct value of a reactive property, only ever read through an already-reactive `Article`, the same reasoning as `Url` in US001.

   **Note:** `toDate()` returns `new Date(this.#date.getTime())`, a copy, never the internal `#date` itself. Handing out the real one would let a caller call a mutating method on it (`setFullYear()`, for instance) and silently corrupt a value object that is supposed to be immutable.

   **Note:** back in `article.entity.js`, `DateTime` is still underlined until it is imported. Put the cursor on it and press `Option+Enter` on macOS or `Alt+Enter` on Windows, then pick the option that adds the import; the underline disappears. The `Article` file is not complete until the import is there.

   ```
   git add .
   git commit -m "feat(shared): add DateTime value object."
   ```

5. **Add the read accessors.** One getter per field, plus `getFormatedPublishedAt()`, the derived view the presentation layer needs.

   <details>
   <summary>src/news/domain/model/article.entity.js (Full file)</summary>

   ```javascript
   import {Source} from "./source.entity.js";
   import {StringValidator} from "../../../shared/domain/model/string-validator.js";
   import {DateTime} from "../../../shared/domain/model/date-time.js";
   import {Url} from "../../../shared/domain/model/url.js";

   const NO_IMAGE_URL = 'https://placehold.co/600x400?text=No+Image';

   export class Article {
       #author;
       #title;
       #description;
       #url;
       #urlToImage;
       #source;
       #publishedAt;

       constructor({author = '', title = '', description = '', url = '', urlToImage = '', source = null, publishedAt = ''}) {
           if (!StringValidator.isNotEmptyString(title)) throw new Error('Article title must be a non-empty string');
           if (!(source instanceof Source)) throw new Error('Article must have a resolved Source entity');

           let dateTime;
           try {
               dateTime = publishedAt instanceof DateTime ? publishedAt : new DateTime(publishedAt);
           } catch (e) {
               throw new Error('Article publishedAt must be a valid date');
           }
           if (dateTime.isFuture()) throw new Error('Article publishedAt cannot be in the future');

           const resolvedUrl = url instanceof Url ? url : new Url(url);
           if (resolvedUrl.isEmpty()) throw new Error('Article url must be a valid, non-empty URL');

           this.#author = author;
           this.#title = title.trim();
           this.#description = description;
           this.#url = resolvedUrl;
           const resolvedImage = urlToImage instanceof Url ? urlToImage : new Url(urlToImage);
           this.#urlToImage = resolvedImage.isEmpty() ? new Url(NO_IMAGE_URL) : resolvedImage;
           this.#source = source;
           this.#publishedAt = dateTime;
       }

       get author() {
           return this.#author;
       }

       get title() {
           return this.#title;
       }

       get description() {
           return this.#description;
       }

       get url() {
           return this.#url;
       }

       get urlToImage() {
           return this.#urlToImage;
       }

       get source() {
           return this.#source;
       }

       get publishedAt() {
           return this.#publishedAt;
       }

       getFormatedPublishedAt() {
           return this.#publishedAt.format();
       }
   }
   ```
   </details>

   ```
   git add .
   git commit -m "feat(news): add Article entity."
   ```

6. **Create the `ArticleAssembler`.** Right-click `src` → `New` → `JavaScript File` → type `news/infrastructure/article.assembler` → Enter. Resolves the matching `Source` before constructing the `Article`, the same "resolve first, construct once" shape `SourceAssembler` already uses.

   <details>
   <summary>src/news/infrastructure/article.assembler.js (so far)</summary>

   ```javascript
   import {SourceAssembler} from "./source.assembler.js";
   import {Article} from "../domain/model/article.entity.js";
   import "./news-resources.js";

   export class ArticleAssembler {
       #source;
       #sourceAssembler;

       constructor(source = null) {
           this.#source = source;
           this.#sourceAssembler = new SourceAssembler();
       }

       toEntitiesFromResponse(response) {
           if (response.data.status !== "ok") {
               console.error(`${response.data["status"]},  ${response.data["code"]}, ${response.data["message"]}`);
               return [];
           }
           const articlesResponse = response.data;
           return articlesResponse["articles"].map((article) => {
               try {
                   return this.toEntityFromResource(article);
               } catch (error) {
                   console.error('Validation error for article:', error.message, article);
                   return null;
               }
           }).filter(article => article !== null);
       }

       toEntityFromResource(resource) {
           const resolvedSource = this.#source && (this.#source.id === resource.source?.id || this.#source.name === resource.source?.name)
               ? this.#source
               : this.#sourceAssembler.toEntityFromResource(resource.source || {id: 'unknown', name: 'Unknown Source'});
           return new Article({...resource, source: resolvedSource});
       }
   }
   ```
   </details>

   **Note:** the constructor takes an optional `source`, the one already active in `newsStore` when articles are being loaded *for* that source. `toEntityFromResource` reuses that exact instance when the resource's own embedded source matches it by `id` or `name`, instead of building a second, separate `Source` for the same news provider.

   ```
   git add .
   git commit -m "feat(news): add ArticleAssembler."
   ```

7. **Extend `NewsStore` to load articles for the current source.** Cache the articles by source id, expose the ones of the current source, and load them when the source changes. Choosing a source that was already visited shows its cached list, with no second request.

   The private article cache goes right after `#sources`:

   ```javascript
   #articles = shallowRef({});
   ```

   The derived list for the current source goes after `#currentSourceId`, with its getter, the getter for the whole cache, and the loading method:

   ```javascript
   #currentSourceArticles = computed(() => {
       const id = this.#currentSourceId.value;
       return id ? (this.#articles.value[id] ?? []) : [];
   });

   get articles() {
       return this.#articles.value;
   }

   get currentSourceArticles() {
       return this.#currentSourceArticles.value;
   }

   loadArticlesForCurrentSource() {
       const source = this.#currentSource.value;
       if (!source) return;
       if (this.#articles.value[source.id]) return;
       this.#newsApi.getArticlesForSourceId(source.id).then(response => {
           const articles = new ArticleAssembler(source).toEntitiesFromResponse(response);
           this.#articles.value = {...this.#articles.value, [source.id]: articles};
       }).catch(message => {
           this.#errors.value.push(message);
       });
   }
   ```

   `setCurrentSource` now ends by loading the articles of the source it just selected:

   ```javascript
   setCurrentSource(source) {
       this.#currentSource.value = source;
       this.loadArticlesForCurrentSource();
   }
   ```

   <details>
   <summary>src/news/application/news.store.js (Full file)</summary>

   ```javascript
   import {computed, ref, shallowRef} from "vue";
   import {NewsApi} from "../infrastructure/news-api.js";
   import {SourceAssembler} from "../infrastructure/source.assembler.js";
   import {ArticleAssembler} from "../infrastructure/article.assembler.js";

   class NewsStore {
       #newsApi = new NewsApi();
       #sourceAssembler = new SourceAssembler();
       #sources = shallowRef([]);
       #articles = shallowRef({});
       #currentSource = shallowRef(undefined);
       #errors = ref([]);
       #currentSourceId = computed(() => this.#currentSource.value?.id);

       #currentSourceArticles = computed(() => {
           const id = this.#currentSourceId.value;
           return id ? (this.#articles.value[id] ?? []) : [];
       });

       get sources() {
           return this.#sources.value;
       }

       get articles() {
           return this.#articles.value;
       }

       get errors() {
           return this.#errors.value;
       }

       get currentSource() {
           return this.#currentSource.value;
       }

       get currentSourceId() {
           return this.#currentSourceId.value;
       }

       get currentSourceArticles() {
           return this.#currentSourceArticles.value;
       }

       setCurrentSource(source) {
           this.#currentSource.value = source;
           this.loadArticlesForCurrentSource();
       }

       loadSources() {
           if (this.#sources.value.length > 0) return;
           this.#errors.value = [];
           this.#newsApi.getSources().then(response => {
               const sources = this.#sourceAssembler.toEntitiesFromResponse(response);
               this.#sources.value = sources;
               if (sources.length > 0) this.setCurrentSource(sources[0]);
           }).catch(message => {
               this.#errors.value.push(message);
               this.#sources.value = [];
           });
       }

       loadArticlesForCurrentSource() {
           const source = this.#currentSource.value;
           if (!source) return;
           if (this.#articles.value[source.id]) return;
           this.#newsApi.getArticlesForSourceId(source.id).then(response => {
               const articles = new ArticleAssembler(source).toEntitiesFromResponse(response);
               this.#articles.value = {...this.#articles.value, [source.id]: articles};
           }).catch(message => {
               this.#errors.value.push(message);
           });
       }
   }

   export const newsStore = new NewsStore();
   ```
   </details>

   **Note:** `setCurrentSource` calls `this.loadArticlesForCurrentSource()` as its last line, choosing a source and loading its articles are one action from the caller's side, never two separate steps to remember. `loadArticlesForCurrentSource` builds a *fresh* `ArticleAssembler(source)` every time, not a shared one, so the source it resolves articles against is always the one active right now.

   **Note:** `#articles` is replaced as a whole, `{...this.#articles.value, [source.id]: articles}`, never mutated in place, a `shallowRef` only notices a new value, and that is the same rule the entities follow.

   ```
   git add .
   git commit -m "feat(news): load articles for the current source."
   ```

8. **Create the `ArticleItem` component's template.** Right-click `src` → `New` → `Vue Single-File Component` → `Composition API` → type `news/presentation/components/article-item` → Enter.

   A card built from PrimeVue slots, `header`, `title`, `subtitle`, `content`, and `footer`. The image goes in the header, the source row with its avatar in the subtitle, and the read-more link with the share button in the footer:

   ```vue
   <pv-card class="m-2">
     <template #header> ... </template>
     <template #title> ... </template>
     <template #subtitle> ... </template>
     <template #content> ... </template>
     <template #footer> ... </template>
   </pv-card>
   ```

   <details>
   <summary>src/news/presentation/components/article-item.vue (template)</summary>

   ```vue
   <template>
     <pv-card class="m-2">
       <template #header>
         <img :alt="article.title" :src="article.urlToImage.toString()" class="image-fit"/>
       </template>
       <template #title>
         <p class="flex align-content-start flex-wrap">
           {{ article.title }}
         </p>
       </template>
       <template #subtitle>
         <div class="flex flex-column gap-2">
           <p class="flex align-content-start flex-wrap cursor-pointer" @click="toggleSourceSummary">
             <span class="flex align-items-center justify-content-center mr-2">
               <pv-avatar :aria-label="article.source.name"
                          :image="article.source.urlToLogo"
                          shape="circle"/>
             </span>
             <span class="flex align-items-center justify-content-center font-bold">
               {{ article.source.name }}
             </span>
           </p>
           <p v-if="article.author" class="flex align-content-start flex-wrap">
             <span class="text-sm">By {{ article.author }}</span>
           </p>
           <p class="flex align-content-start flex-wrap">
             <span class="text-sm">Published on {{ article.getFormatedPublishedAt() }}</span>
           </p>
         </div>
       </template>
       <template #content>
         <p class="flex align-content-start flex-wrap mt-4">
           {{ article.description }}
         </p>
       </template>
       <template #footer>
         <div class="flex justify-content-between align-items-center">
           <pv-button v-if="!article.url.isEmpty()" as="a" :href="article.url.toString()" target="_blank"
                      label="Read more" link class="p-0" />
           <pv-button
               v-if="!article.url.isEmpty()"
               label="Share"
               aria-label="Share article"
               text
               size="small"
               icon="pi pi-share-alt"
               @click="shareArticle"/>
         </div>
       </template>
     </pv-card>
   </template>

   <style scoped>
   .image-fit {
     width: 100%;
     height: 100%;
     object-fit: cover;
   }
   </style>
   ```
   </details>

   **Note:** `article`, `toggleSourceSummary`, and `shareArticle` do not exist yet, WebStorm shows them unresolved in the template. `"By "` and `"Published on "` are plain English text for now, not yet translated, US003 replaces both with `t(...)` calls once i18n exists. Building the template with real, readable copy first, then swapping it for translation keys, is easier to follow than starting from `{{ t('article.by') }}` with no English text to compare it against yet.

9. **Add the `article` prop.**

   The component receives the article it shows as a required prop:

   ```javascript
   const { article } = defineProps({article: {type: Article, required: true}});
   ```

   <details>
   <summary>src/news/presentation/components/article-item.vue (so far)</summary>

   ```vue
   <script lang="js" setup>
   import {Article} from "../../domain/model/article.entity.js";

   const { article } = defineProps({article: {type: Article, required: true}});
   </script>

   <template>
     <pv-card class="m-2">
       <template #header>
         <img :alt="article.title" :src="article.urlToImage.toString()" class="image-fit"/>
       </template>
       <template #title>
         <p class="flex align-content-start flex-wrap">
           {{ article.title }}
         </p>
       </template>
       <template #subtitle>
         <div class="flex flex-column gap-2">
           <p class="flex align-content-start flex-wrap cursor-pointer" @click="toggleSourceSummary">
             <span class="flex align-items-center justify-content-center mr-2">
               <pv-avatar :aria-label="article.source.name"
                          :image="article.source.urlToLogo"
                          shape="circle"/>
             </span>
             <span class="flex align-items-center justify-content-center font-bold">
               {{ article.source.name }}
             </span>
           </p>
           <p v-if="article.author" class="flex align-content-start flex-wrap">
             <span class="text-sm">By {{ article.author }}</span>
           </p>
           <p class="flex align-content-start flex-wrap">
             <span class="text-sm">Published on {{ article.getFormatedPublishedAt() }}</span>
           </p>
         </div>
       </template>
       <template #content>
         <p class="flex align-content-start flex-wrap mt-4">
           {{ article.description }}
         </p>
       </template>
       <template #footer>
         <div class="flex justify-content-between align-items-center">
           <pv-button v-if="!article.url.isEmpty()" as="a" :href="article.url.toString()" target="_blank"
                      label="Read more" link class="p-0" />
           <pv-button
               v-if="!article.url.isEmpty()"
               label="Share"
               aria-label="Share article"
               text
               size="small"
               icon="pi pi-share-alt"
               @click="shareArticle"/>
         </div>
       </template>
     </pv-card>
   </template>

   <style scoped>
   .image-fit {
     width: 100%;
     height: 100%;
     object-fit: cover;
   }
   </style>
   ```
   </details>

10. **Add the `SourceSummary` popover trigger.** `SourceSummary` itself does not exist yet, US004 builds it, this component only needs a `ref` to call `.toggle()` on it.

    A `ref` for the popover, and the handler the source row already calls when it is clicked:

    ```javascript
    const sourceSummary = ref();

    const toggleSourceSummary = event => {
      sourceSummary.value.toggle(event);
    };
    ```

    <details>
    <summary>src/news/presentation/components/article-item.vue (so far)</summary>

    ```vue
    <script lang="js" setup>
    import {Article} from "../../domain/model/article.entity.js";
    import {ref} from "vue";

    const { article } = defineProps({article: {type: Article, required: true}});

    const sourceSummary = ref();

    const toggleSourceSummary = event => {
      sourceSummary.value.toggle(event);
    };
    </script>

    <template>
      <pv-card class="m-2">
        <template #header>
          <img :alt="article.title" :src="article.urlToImage.toString()" class="image-fit"/>
        </template>
        <template #title>
          <p class="flex align-content-start flex-wrap">
            {{ article.title }}
          </p>
        </template>
        <template #subtitle>
          <div class="flex flex-column gap-2">
            <p class="flex align-content-start flex-wrap cursor-pointer" @click="toggleSourceSummary">
              <span class="flex align-items-center justify-content-center mr-2">
                <pv-avatar :aria-label="article.source.name"
                           :image="article.source.urlToLogo"
                           shape="circle"/>
              </span>
              <span class="flex align-items-center justify-content-center font-bold">
                {{ article.source.name }}
              </span>
            </p>
            <p v-if="article.author" class="flex align-content-start flex-wrap">
              <span class="text-sm">By {{ article.author }}</span>
            </p>
            <p class="flex align-content-start flex-wrap">
              <span class="text-sm">Published on {{ article.getFormatedPublishedAt() }}</span>
            </p>
          </div>
        </template>
        <template #content>
          <p class="flex align-content-start flex-wrap mt-4">
            {{ article.description }}
          </p>
        </template>
        <template #footer>
          <div class="flex justify-content-between align-items-center">
            <pv-button v-if="!article.url.isEmpty()" as="a" :href="article.url.toString()" target="_blank"
                       label="Read more" link class="p-0" />
            <pv-button
                v-if="!article.url.isEmpty()"
                label="Share"
                aria-label="Share article"
                text
                size="small"
                icon="pi pi-share-alt"
                @click="shareArticle"/>
          </div>
        </template>
      </pv-card>
    </template>

    <style scoped>
    .image-fit {
      width: 100%;
      height: 100%;
      object-fit: cover;
    }
    </style>
    ```
    </details>

    **Note:** no commit here, `<source-summary>` is not in the template yet either, that and this piece land together once `SourceSummary` exists, in US004.

11. **Add `shareArticle()`.** Web Share API first, clipboard as the fallback.

    First the event `article-shared`, right under the prop:

    ```javascript
    const emit = defineEmits(['article-shared']);
    ```

    Then `shareArticle()`, at the end of the script:

    ```javascript
    const shareArticle = async () => {
      const shareData = {title: article.title, url: article.url.toString()};
      if (navigator.share) {
        try {
          await navigator.share(shareData);
          console.log('Article shared successfully');
        } catch (err) {
          console.error('Error sharing the article:', err);
        }
      } else {
        try {
          await navigator.clipboard.writeText(shareData.url);
          emit('article-shared', shareData.url);
          console.log('Article URL copied to clipboard');
        } catch (err) {
          console.error('Failed to copy the article URL:', err);
        }
      }
    };
    ```

    <details>
    <summary>src/news/presentation/components/article-item.vue (Full file)</summary>

    ```vue
    <script lang="js" setup>
    import {Article} from "../../domain/model/article.entity.js";
    import {ref} from "vue";

    const { article } = defineProps({article: {type: Article, required: true}});

    const emit = defineEmits(['article-shared']);

    const sourceSummary = ref();

    const toggleSourceSummary = event => {
      sourceSummary.value.toggle(event);
    };

    const shareArticle = async () => {
      const shareData = {title: article.title, url: article.url.toString()};
      if (navigator.share) {
        try {
          await navigator.share(shareData);
          console.log('Article shared successfully');
        } catch (err) {
          console.error('Error sharing the article:', err);
        }
      } else {
        try {
          await navigator.clipboard.writeText(shareData.url);
          emit('article-shared', shareData.url);
          console.log('Article URL copied to clipboard');
        } catch (err) {
          console.error('Failed to copy the article URL:', err);
        }
      }
    };

    </script>

    <template>
      <pv-card class="m-2">
        <template #header>
          <img :alt="article.title" :src="article.urlToImage.toString()" class="image-fit"/>
        </template>
        <template #title>
          <p class="flex align-content-start flex-wrap">
            {{ article.title }}
          </p>
        </template>
        <template #subtitle>
          <div class="flex flex-column gap-2">
            <p class="flex align-content-start flex-wrap cursor-pointer" @click="toggleSourceSummary">
              <span class="flex align-items-center justify-content-center mr-2">
                <pv-avatar :aria-label="article.source.name"
                           :image="article.source.urlToLogo"
                           shape="circle"/>
              </span>
              <span class="flex align-items-center justify-content-center font-bold">
                {{ article.source.name }}
              </span>
            </p>
            <p v-if="article.author" class="flex align-content-start flex-wrap">
              <span class="text-sm">By {{ article.author }}</span>
            </p>
            <p class="flex align-content-start flex-wrap">
              <span class="text-sm">Published on {{ article.getFormatedPublishedAt() }}</span>
            </p>
          </div>
        </template>
        <template #content>
          <p class="flex align-content-start flex-wrap mt-4">
            {{ article.description }}
          </p>
        </template>
        <template #footer>
          <div class="flex justify-content-between align-items-center">
            <pv-button v-if="!article.url.isEmpty()" as="a" :href="article.url.toString()" target="_blank"
                       label="Read more" link class="p-0" />
            <pv-button
                v-if="!article.url.isEmpty()"
                label="Share"
                aria-label="Share article"
                text
                size="small"
                icon="pi pi-share-alt"
                @click="shareArticle"/>
          </div>
        </template>
      </pv-card>
    </template>

    <style scoped>
    .image-fit {
      width: 100%;
      height: 100%;
      object-fit: cover;
    }
    </style>
    ```
    </details>

    **Note:** `navigator.share` exists on most mobile browsers and a growing number of desktop ones; where it does not, `else` copies the URL to the clipboard instead and emits `article-shared` so whoever is listening knows it happened. Nothing listens to that event yet, `ArticleList` does not forward it and `Layout` does not handle it, this app has no toast/snackbar mechanism. Wiring one up is a good exercise once you finish this guide, not something this course project builds.

    ```
    git add .
    git commit -m "feat(news): add ArticleItem component."
    ```

12. **Create the `ArticleList` component's template.** Right-click `src` → `New` → `Vue Single-File Component` → `Composition API` → type `news/presentation/components/article-list` → Enter. One `ArticleItem` per article, keyed by the article's URL.

    Each article gets its own `ArticleItem`:

    ```vue
    <div v-for="article in articles" :key="article.url.toString()">
      <article-item :article="article"/>
    </div>
    ```

    <details>
    <summary>src/news/presentation/components/article-list.vue (template)</summary>

    ```vue
    <template>
      <div v-for="article in articles" :key="article.url.toString()">
        <article-item :article="article"/>
      </div>
    </template>
    ```
    </details>

13. **Add the `articles` prop.**

    The list receives the articles it shows as an array:

    ```javascript
    const { articles } = defineProps({ articles: { type: Array, required: true } });
    ```

    <details>
    <summary>src/news/presentation/components/article-list.vue (Full file)</summary>

    ```vue
    <script setup lang="js">
    import ArticleItem from "./article-item.vue";
    import {Article} from "../../domain/model/article.entity.js";

    const { articles } = defineProps({ articles: { type: Array, required: true } });

    </script>

    <template>
      <div v-for="article in articles" :key="article.url.toString()">
        <article-item :article="article"/>
      </div>
    </template>

    <style scoped>

    </style>
    ```
    </details>

    **Note:** `ArticleList` is complete after this one addition, there is only ever going to be this one prop.

    ```
    git add .
    git commit -m "feat(news): add ArticleList component."
    ```

14. **Wire `ArticleList` into `Layout`.**

    A `computed()` view over the store's articles for the current source:

    ```javascript
    const articles = computed(() => newsStore.currentSourceArticles);
    ```

    The list goes under the menu bar, in the main content area:

    ```vue
    <main class="content-padding">
      <article-list :articles="articles"/>
    </main>
    ```

    And the main area gets its padding:

    ```css
    .content-padding {
      padding: 1rem;
      flex: 1;
    }

    @media screen and (min-width: 768px) {
      .content-padding {
        padding: 2rem;
      }
    }
    ```

    <details>
    <summary>src/shared/presentation/components/layout.vue (Full file)</summary>

    ```vue
    <script lang="js" setup>

    import {newsStore} from "../../../news/application/news.store.js";
    import SourceList from "../../../news/presentation/components/source-list.vue";
    import ArticleList from "../../../news/presentation/components/article-list.vue";
    import {ref, computed, onMounted} from "vue";

    const drawerVisible = ref(false);

    const toggleDrawer = () => {
      drawerVisible.value = !drawerVisible.value;
    };

    const sources = computed(() => newsStore.sources);
    const articles = computed(() => newsStore.currentSourceArticles);

    const setSource = source => {
      newsStore.setCurrentSource(source);
      toggleDrawer();
    };

    onMounted(() => {
      newsStore.loadSources();
    });

    </script>

    <template>
      <div class="layout-container">
        <header class="sticky-header">
          <pv-menubar>
            <template #start>
              <pv-button icon="pi pi-bars" label="CatchUp"
                         text @click="toggleDrawer" class="mr-2"/>
              <source-list :sources="sources"
                           v-model:visible="drawerVisible"
                           @source-selected="setSource"/>
            </template>
          </pv-menubar>
        </header>
        <main class="content-padding">
          <article-list :articles="articles"/>
        </main>
      </div>
    </template>

    <style scoped>
    .layout-container {
      display: flex;
      flex-direction: column;
      min-height: 100vh;
    }

    .sticky-header {
      position: sticky;
      top: 0;
      z-index: 1000;
    }

    .content-padding {
      padding: 1rem;
      flex: 1;
    }

    @media screen and (min-width: 768px) {
      .content-padding {
        padding: 2rem;
      }
    }
    </style>
    ```
    </details>

    **Note:** `articles` reads `newsStore.currentSourceArticles`, the list cached for the source that is active right now. While the request for a newly chosen source is still in flight the getter returns an empty array, never `undefined`, so `v-for` always has an array to iterate.

    ```
    git add .
    git commit -m "feat: show ArticleList in Layout."
    ```

15. **Run it.**

    ```
    npm run dev
    ```

    Choosing a source now loads real articles for it: title, image (or the placeholder), author when one exists, and a formatted publish date. Click **Share** on an article, its URL lands on your clipboard (or your device's native share sheet opens, if it supports the Web Share API). Stop the server with `Ctrl+C`.

16. **Publish and finish the feature.**

---

## Engage with Ethical and Inclusive Features (US003)

Every string shown so far is hardcoded English. This story adds real internationalization (English and Spanish), a switcher to pick between them, a footer crediting the two external services this app depends on, and a fallback view for when a request fails.

1. **Start the feature `internationalize-the-application`.**

2. **See the component tree this story builds.** `LanguageSwitcher`, `UnavailableContent`, and `FooterContent` join the tree; `LanguageSwitcher` and `FooterContent` take no input (they read `useI18n()` directly), `UnavailableContent` takes `:errors` and replaces `ArticleList` through `v-else` when there are no articles. `Layout` now also exposes `errors`.

   ```
   +-----+
   | app |
   +-----+
       |
       +------------------------------------+
       | layout                             |
       | State (from newsStore):            |
       |   sources: ComputedRef<Source[]>   |
       |   drawerVisible: Ref<boolean>      |
       |   articles: ComputedRef<Article[]> |
       |   errors: ComputedRef<string[]>    |
       +------------------------------------+
           |
           +-------------------------------------------+
           | source-list                               |
           | Input:  :visible (v-model), :sources      |
           | Output: @source-selected, @update:visible |
           +-------------------------------------------+
               |
               +--------------------------+
               | source-item              |
               | Input:  :source          |
               | Output: @source-selected |
               +--------------------------+
           |
           +--------------------------------------------+
           | language-switcher                          |
           | (no Input/Output, uses useI18n() directly) |
           +--------------------------------------------+
           |
           +-------------------------------------+
           | article-list  (v-if articles.length) |
           | Input:  :articles                   |
           +-------------------------------------+
               |
               +-------------------------+
               | article-item            |
               | Input:  :article        |
               | Output: @article-shared |
               +-------------------------+
           |
           +------------------------------+
           | unavailable-content  (v-else) |
           | Input:  :errors              |
           +------------------------------+
           |
           +--------------------------------------------+
           | footer-content                             |
           | (no Input/Output, uses useI18n() directly) |
           +--------------------------------------------+
   ```

3. **Create the English dictionary.** Right-click `src` → `New` → `File` → type `locales/en.json` → Enter.

   <details>
   <summary>src/locales/en.json</summary>

   ```json
   {
     "read-more": "Read more",
     "unavailable-news": "News service is unavailable now.",
     "authoring-phrase": {
       "intro": "Made with",
       "use": "using",
       "author": "by {brand} Developer Team"
     },
     "article": {
       "share": "Share",
       "copy-to-clipboard": "Copy to clipboard",
       "by": "By",
       "published-on": "Published on"
     },
     "footer": {
       "powered-by": "Powered by",
       "and": "and"
     }
   }
   ```
   </details>

   **Note:** `"author": "by {brand} Developer Team"` has a `{brand}` placeholder, `vue-i18n` fills it in from an argument passed at the call site (`t('authoring-phrase.author', {brand: 'ACME'})`), the dictionary itself never hardcodes which brand.

4. **Create the Spanish dictionary, same keys.**

   <details>
   <summary>src/locales/es.json</summary>

   ```json
   {
     "read-more": "Ver más",
     "unavailable-news": "Servicio de noticias no disponible en este momento.",
     "authoring-phrase": {
       "intro": "Hecho con",
       "use": "utilizando",
       "author": "por el Equipo de Desarrollo de {brand}"
     },
     "article": {
       "share": "Compartir",
       "copy-to-clipboard": "Copiar al portapapeles",
       "by": "Por",
       "published-on": "Publicado el"
     },
     "footer": {
       "powered-by": "Contenido proporcionado por",
       "and": "y"
     }
   }
   ```
   </details>

   ```
   git add .
   git commit -m "feat(i18n): add English and Spanish dictionaries."
   ```

5. **Create the i18n instance.** Right-click `src` → `New` → `JavaScript File` → type `i18n` → Enter.

   <details>
   <summary>src/i18n.js</summary>

   ```javascript
   import en from "./locales/en.json";
   import es from "./locales/es.json";

   import {createI18n} from "vue-i18n";

   const i18n = createI18n({
       legacy: false,
       locale: "en",
       fallbackLocale: "en",
       messages: {en, es}
   });

   export default i18n;
   ```
   </details>

   **Note:** `legacy: false` opts into vue-i18n's Composition API mode, `useI18n()` inside `<script setup>`, instead of the older `this.$t(...)` Options API style. `fallbackLocale: "en"` means a key missing from `es.json` still renders, in English, instead of showing blank or the raw key name.

6. **Register i18n in `main.js`.**

   <details>
   <summary>src/main.js (Full file)</summary>

   ```javascript
   import { createApp } from 'vue'
   import './style.css'
   import App from './app.vue'
   import i18n from "./i18n.js";
   import PrimeVue from 'primevue/config';
   import Material from '@primeuix/themes/material';
   import 'primeicons/primeicons.css';
   import 'primeflex/primeflex.css';
   import {Avatar, Button, Card, Drawer, Menu, Menubar, Popover, SelectButton, Toolbar, Tooltip} from "primevue";

   const primeUiLicenseKey = import.meta.env.VITE_PRIME_UI_LICENSE_KEY;

   createApp(App)
       .use(i18n)
       .use(PrimeVue, { ripple: true, theme: { preset: Material }, license: primeUiLicenseKey })
       .component('pv-button', Button)
       .component('pv-select-button', SelectButton)
       .component('pv-avatar', Avatar)
       .component('pv-drawer', Drawer)
       .component('pv-card', Card)
       .component('pv-toolbar', Toolbar)
       .component('pv-menu', Menu)
       .component('pv-menubar', Menubar)
       .component('pv-popover', Popover)
       .directive('tooltip', Tooltip)
       .mount('#app')
   ```
   </details>

   ```
   git add .
   git commit -m "feat: register vue-i18n globally."
   ```

7. **Create the `LanguageSwitcher` component.** Right-click `src` → `New` → `Vue Single-File Component` → `Composition API` → type `shared/presentation/components/language-switcher` → Enter. `vue-i18n`'s own `useI18n()` already exposes everything this component needs, no props at all.

   <details>
   <summary>src/shared/presentation/components/language-switcher.vue</summary>

   ```vue
   <script setup lang="js">
   import { useI18n } from 'vue-i18n';

   const { locale, availableLocales } = useI18n();
   </script>

   <template>
     <pv-select-button v-model="locale" :options="availableLocales">
       <template #option="slotProps">
         <span>{{ slotProps.option.toUpperCase() }}</span>
       </template>
     </pv-select-button>
   </template>

   <style scoped>

   </style>
   ```
   </details>

   **Note:** `v-model="locale"` on the select button writes straight back into `useI18n()`'s own reactive `locale`, changing the whole app's language, immediately, everywhere `t(...)` is used, not just inside this component. `availableLocales` is derived automatically from the keys of the `messages` object passed to `createI18n`, `en` and `es`, nothing to configure twice.

   ```
   git add .
   git commit -m "feat(shared): add LanguageSwitcher component."
   ```

8. **Create the `FooterContent` component.** Right-click `src` → `New` → `Vue Single-File Component` → `Composition API` → type `shared/presentation/components/footer-content` → Enter. Attribution for the two external services this app depends on.

   <details>
   <summary>src/shared/presentation/components/footer-content.vue</summary>

   ```vue
   <script setup lang="js">
     import {useI18n} from "vue-i18n";

     const { t } = useI18n();
   </script>

   <template>
     <div class="grid mt-4 p-4 justify-content-center bg-primary text-primary-contrast text-center">
       <div class="col-12 flex flex-column align-items-center">
         <p>Copyright &copy; 2026. ACME Studios</p>
       </div>
       <div  class="col-12 flex flex-column align-items-center mt-3">
         <p>
           {{ t('authoring-phrase.intro') }} <i class="pi pi-heart text-red-500"/>
           {{ t('authoring-phrase.use') }} <a href="https://primevue.org/" target="_blank" class="text-primary-contrast font-bold">PrimeVue</a>
           {{ t('authoring-phrase.author', {brand: 'ACME'}) }}
         </p>
         <p>{{ t('footer.powered-by') }} <a href="https://www.newsapi.org" class="text-primary-contrast font-bold">NewsAPI.org</a> {{ t('footer.and') }} <a href="https://logo.dev" class="text-primary-contrast font-bold">Logo.dev Logo API</a> </p>
       </div>
     </div>
   </template>

   <style scoped>

   </style>
   ```
   </details>

   ```
   git add .
   git commit -m "feat(shared): add FooterContent component."
   ```

9. **Wire `LanguageSwitcher` and `FooterContent` into `Layout`.** `LanguageSwitcher` and `FooterContent` join the script's imports.

   The switcher goes at the end of the menu bar:

   ```vue
   <template #end>
     <language-switcher/>
   </template>
   ```

   And the footer goes below the main content:

   ```vue
   <footer>
     <footer-content/>
   </footer>
   ```

   <details>
   <summary>src/shared/presentation/components/layout.vue (Full file)</summary>

   ```vue
   <script lang="js" setup>

   import {newsStore} from "../../../news/application/news.store.js";
   import SourceList from "../../../news/presentation/components/source-list.vue";
   import LanguageSwitcher from "./language-switcher.vue";
   import ArticleList from "../../../news/presentation/components/article-list.vue";
   import FooterContent from "./footer-content.vue";
   import {ref, computed, onMounted} from "vue";

   const drawerVisible = ref(false);

   const toggleDrawer = () => {
     drawerVisible.value = !drawerVisible.value;
   };

   const sources = computed(() => newsStore.sources);
   const articles = computed(() => newsStore.currentSourceArticles);

   const setSource = source => {
     newsStore.setCurrentSource(source);
     toggleDrawer();
   };

   onMounted(() => {
     newsStore.loadSources();
   });

   </script>

   <template>
     <div class="layout-container">
       <header class="sticky-header">
         <pv-menubar>
           <template #start>
             <pv-button icon="pi pi-bars" label="CatchUp"
                        text @click="toggleDrawer" class="mr-2"/>
             <source-list :sources="sources"
                          v-model:visible="drawerVisible"
                          @source-selected="setSource"/>
           </template>
           <template #end>
             <language-switcher/>
           </template>
         </pv-menubar>
       </header>
       <main class="content-padding">
         <article-list :articles="articles"/>
       </main>
       <footer>
         <footer-content/>
       </footer>
     </div>
   </template>

   <style scoped>
   .layout-container {
     display: flex;
     flex-direction: column;
     min-height: 100vh;
   }

   .sticky-header {
     position: sticky;
     top: 0;
     z-index: 1000;
   }

   .content-padding {
     padding: 1rem;
     flex: 1;
   }

   @media screen and (min-width: 768px) {
     .content-padding {
       padding: 2rem;
     }
   }
   </style>
   ```
   </details>

   ```
   git add .
   git commit -m "feat: show LanguageSwitcher and FooterContent in Layout."
   ```

10. **Revisit `ArticleItem`, translate its text.** `"By "`, `"Published on "`, `"Read more"`, `"Share"`, and the tooltip were plain English, written before i18n existed.

    `useI18n()` gives the component its translation function:

    ```javascript
    const {t} = useI18n();
    ```

    The author and date labels read from the dictionary:

    ```vue
    <p v-if="article.author" class="flex align-content-start flex-wrap">
      <span class="text-sm">{{ t('article.by') }} {{ article.author }}</span>
    </p>
    <p class="flex align-content-start flex-wrap">
      <span class="text-sm">{{ t('article.published-on') }} {{ article.getFormatedPublishedAt() }}</span>
    </p>
    ```

    And so do the read-more link, the share button, and its tooltip:

    ```vue
    <pv-button v-if="!article.url.isEmpty()" as="a" :href="article.url.toString()" target="_blank"
               :label="t('read-more')" link class="p-0" />
    <pv-button
        v-if="!article.url.isEmpty()"
        v-tooltip="t('article.copy-to-clipboard')"
        :label="t('article.share')"
        aria-label="Share article"
        text
        size="small"
        icon="pi pi-share-alt"
        @click="shareArticle"/>
    ```

    <details>
    <summary>src/news/presentation/components/article-item.vue (Full file)</summary>

    ```vue
    <script lang="js" setup>
    import {useI18n} from "vue-i18n";
    import {Article} from "../../domain/model/article.entity.js";
    import {ref} from "vue";

    const {t} = useI18n();

    const { article } = defineProps({article: {type: Article, required: true}});

    const emit = defineEmits(['article-shared']);

    const sourceSummary = ref();

    const toggleSourceSummary = event => {
      sourceSummary.value.toggle(event);
    };

    const shareArticle = async () => {
      const shareData = {title: article.title, url: article.url.toString()};
      if (navigator.share) {
        try {
          await navigator.share(shareData);
          console.log('Article shared successfully');
        } catch (err) {
          console.error('Error sharing the article:', err);
        }
      } else {
        try {
          await navigator.clipboard.writeText(shareData.url);
          emit('article-shared', shareData.url);
          console.log('Article URL copied to clipboard');
        } catch (err) {
          console.error('Failed to copy the article URL:', err);
        }
      }
    };

    </script>

    <template>
      <pv-card class="m-2">
        <template #header>
          <img :alt="article.title" :src="article.urlToImage.toString()" class="image-fit"/>
        </template>
        <template #title>
          <p class="flex align-content-start flex-wrap">
            {{ article.title }}
          </p>
        </template>
        <template #subtitle>
          <div class="flex flex-column gap-2">
            <p class="flex align-content-start flex-wrap cursor-pointer" @click="toggleSourceSummary">
              <span class="flex align-items-center justify-content-center mr-2">
                <pv-avatar :aria-label="article.source.name"
                           :image="article.source.urlToLogo"
                           shape="circle"/>
              </span>
              <span class="flex align-items-center justify-content-center font-bold">
                {{ article.source.name }}
              </span>
            </p>
            <p v-if="article.author" class="flex align-content-start flex-wrap">
              <span class="text-sm">{{ t('article.by') }} {{ article.author }}</span>
            </p>
            <p class="flex align-content-start flex-wrap">
              <span class="text-sm">{{ t('article.published-on') }} {{ article.getFormatedPublishedAt() }}</span>
            </p>
          </div>
        </template>
        <template #content>
          <p class="flex align-content-start flex-wrap mt-4">
            {{ article.description }}
          </p>
        </template>
        <template #footer>
          <div class="flex justify-content-between align-items-center">
            <pv-button v-if="!article.url.isEmpty()" as="a" :href="article.url.toString()" target="_blank"
                       :label="t('read-more')" link class="p-0" />
            <pv-button
                v-if="!article.url.isEmpty()"
                v-tooltip="t('article.copy-to-clipboard')"
                :label="t('article.share')"
                aria-label="Share article"
                text
                size="small"
                icon="pi pi-share-alt"
                @click="shareArticle"/>
          </div>
        </template>
      </pv-card>
    </template>

    <style scoped>
    .image-fit {
      width: 100%;
      height: 100%;
      object-fit: cover;
    }
    </style>
    ```
    </details>

    **Note:** no `<source-summary>` in the template yet, and no import for it either, `SourceSummary` does not exist until US004. `sourceSummary` and `toggleSourceSummary` already exist, from US002, waiting for it.

    ```
    git add .
    git commit -m "feat(i18n): translate ArticleItem's text."
    ```

11. **Create the `UnavailableContent` component.** Right-click `src` → `New` → `Vue Single-File Component` → `Composition API` → type `news/presentation/components/unavailable-content` → Enter. Shown instead of the article list whenever loading sources or articles failed.

    <details>
    <summary>src/news/presentation/components/unavailable-content.vue</summary>

    ```vue
    <script setup lang="js">
      import {useI18n} from "vue-i18n";

      const { t } = useI18n();

      const { errors } = defineProps({ errors: { type: Array, default: () => [] } });
    </script>

    <template>
      <div class="flex flex-column align-items-center justify-content-center mt-8 text-muted-color">
        <i class="pi pi-exclamation-circle text-6xl mb-4" />
        <div><h4>{{ t('unavailable-news') }}</h4></div>
        <div v-for="error in errors" :key="error" class="mt-2">
          <h6>{{ error }}</h6>
        </div>
      </div>
    </template>

    <style scoped>

    </style>
    ```
    </details>

    ```
    git add .
    git commit -m "feat(news): add UnavailableContent component."
    ```

12. **Show `UnavailableContent` in `Layout` when there is nothing to display.**

    `UnavailableContent` joins the script's imports, and `errors` is a `computed()` view over the store's error messages:

    ```javascript
    const errors = computed(() => newsStore.errors);
    ```

    The list shows only when there are articles, and `UnavailableContent` takes its place otherwise:

    ```vue
    <article-list v-if="articles.length" :articles="articles"/>
    <unavailable-content v-else :errors="errors"/>
    ```

    <details>
    <summary>src/shared/presentation/components/layout.vue (Full file)</summary>

    ```vue
    <script lang="js" setup>

    import {newsStore} from "../../../news/application/news.store.js";
    import SourceList from "../../../news/presentation/components/source-list.vue";
    import LanguageSwitcher from "./language-switcher.vue";
    import ArticleList from "../../../news/presentation/components/article-list.vue";
    import UnavailableContent from "../../../news/presentation/components/unavailable-content.vue";
    import FooterContent from "./footer-content.vue";
    import {ref, computed, onMounted} from "vue";

    const drawerVisible = ref(false);

    const toggleDrawer = () => {
      drawerVisible.value = !drawerVisible.value;
    };

    const sources = computed(() => newsStore.sources);
    const errors = computed(() => newsStore.errors);
    const articles = computed(() => newsStore.currentSourceArticles);

    const setSource = source => {
      newsStore.setCurrentSource(source);
      toggleDrawer();
    };

    onMounted(() => {
      newsStore.loadSources();
    });

    </script>

    <template>
      <div class="layout-container">
        <header class="sticky-header">
          <pv-menubar>
            <template #start>
              <pv-button icon="pi pi-bars" label="CatchUp"
                         text @click="toggleDrawer" class="mr-2"/>
              <source-list :sources="sources"
                           v-model:visible="drawerVisible"
                           @source-selected="setSource"/>
            </template>
            <template #end>
              <language-switcher/>
            </template>
          </pv-menubar>
        </header>
        <main class="content-padding">
          <article-list v-if="articles.length" :articles="articles"/>
          <unavailable-content v-else :errors="errors"/>
        </main>
        <footer>
          <footer-content/>
        </footer>
      </div>
    </template>

    <style scoped>
    .layout-container {
      display: flex;
      flex-direction: column;
      min-height: 100vh;
    }

    .sticky-header {
      position: sticky;
      top: 0;
      z-index: 1000;
    }

    .content-padding {
      padding: 1rem;
      flex: 1;
    }

    @media screen and (min-width: 768px) {
      .content-padding {
        padding: 2rem;
      }
    }
    </style>
    ```
    </details>

    **Note:** `v-if="articles.length"` / `v-else`, never both branches rendered at once. An empty article list (no source chosen yet, or a request still in flight with zero results so far) falls into the same `v-else` branch as a real error, `UnavailableContent`'s own message covers both, `errors` is simply empty in the first case.

    ```
    git add .
    git commit -m "feat: show UnavailableContent when there are no articles."
    ```

13. **Run it.**

    ```
    npm run dev
    ```

    The switcher in the top bar changes every visible string, article text included, immediately. Disconnect from the network and reload: `UnavailableContent`'s message appears instead of a blank page. Stop the server with `Ctrl+C`.

14. **Publish and finish the feature.**

---

## Interact with Articles and Sources (US004)

Clicking an article's source name does nothing yet. This story adds `SourceSummary`, a popover with the source's full details, and wires it into the trigger `ArticleItem` has been holding onto since US002.

1. **Start the feature `add-source-summary-popover`.**

2. **See the component tree this story builds.** `SourceSummary` joins the tree, nested under `ArticleItem` in the template itself (a `pv-popover` toggled via an exposed `toggle()`, not a dynamically-opened dialog).

   ```
   +-----+
   | app |
   +-----+
       |
       +------------------------------------+
       | layout                             |
       | State (from newsStore):            |
       |   sources: ComputedRef<Source[]>   |
       |   drawerVisible: Ref<boolean>      |
       |   articles: ComputedRef<Article[]> |
       |   errors: ComputedRef<string[]>    |
       +------------------------------------+
           |
           +-------------------------------------------+
           | source-list                               |
           | Input:  :visible (v-model), :sources      |
           | Output: @source-selected, @update:visible |
           +-------------------------------------------+
               |
               +--------------------------+
               | source-item              |
               | Input:  :source          |
               | Output: @source-selected |
               +--------------------------+
           |
           +--------------------------------------------+
           | language-switcher                          |
           | (no Input/Output, uses useI18n() directly) |
           +--------------------------------------------+
           |
           +-------------------------------------+
           | article-list  (v-if articles.length) |
           | Input:  :articles                   |
           +-------------------------------------+
               |
               +-------------------------+
               | article-item            |
               | Input:  :article        |
               | Output: @article-shared |
               +-------------------------+
                   |
                   +-------------------+
                   | source-summary    |
                   | Input:  :source   |
                   | Exposes: toggle() |
                   +-------------------+
           |
           +------------------------------+
           | unavailable-content  (v-else) |
           | Input:  :errors              |
           +------------------------------+
           |
           +--------------------------------------------+
           | footer-content                             |
           | (no Input/Output, uses useI18n() directly) |
           +--------------------------------------------+
   ```

3. **Create the `SourceSummary` component's template.** Right-click `src` → `New` → `Vue Single-File Component` → `Composition API` → type `news/presentation/components/source-summary` → Enter. A popover: logo and name, description, category/language/country when present, and a link to the source's website.

   The popover shows the source's logo, name, and details, and is toggled from outside:

   ```vue
   <pv-popover ref="sourceSummary">
     ...
   </pv-popover>
   ```

   <details>
   <summary>src/news/presentation/components/source-summary.vue (template)</summary>

   ```vue
   <template>
     <pv-popover ref="sourceSummary">
       <div class="flex flex-column gap-3 w-25rem">
         <div class="flex align-items-center gap-2">
           <pv-avatar :image="source.urlToLogo" :aria-label="source.name" shape="circle" size="large" />
           <span class="font-bold text-xl">{{ source.name }}</span>
         </div>
         <div v-if="source.description" class="text-color-secondary">
           {{ source.description }}
         </div>
         <div class="flex flex-column gap-2">
           <div v-if="source.category" class="flex align-items-center gap-2">
             <i class="pi pi-tag text-primary"></i>
             <span>{{ source.category }}</span>
           </div>
           <div v-if="source.language" class="flex align-items-center gap-2">
             <i class="pi pi-globe text-primary"></i>
             <span>{{ source.language.toUpperCase() }}</span>
           </div>
           <div v-if="source.country" class="flex align-items-center gap-2">
             <i class="pi pi-map-marker text-primary"></i>
             <span>{{ source.country.toUpperCase() }}</span>
           </div>
         </div>
         <div v-if="!source.url.isEmpty()" class="flex justify-content-end">
           <pv-button
               as="a"
               :href="source.url.toString()"
               target="_blank"
               label="Read more"
               icon="pi pi-external-link"
               size="small"
               text />
         </div>
       </div>
     </pv-popover>
   </template>
   ```
   </details>

   **Note:** `"Read more"` is plain text here, on purpose. Unlike `ArticleItem` in US003, this component is created after i18n already exists, so it goes straight to `t('read-more')` in the next step, no need for a separate "translate it later" pass.

4. **Add the `source` prop and translate the read-more label.**

   The component receives the source it shows as a required prop, and gets the translation function:

   ```javascript
   const {t} = useI18n();

   const {source} = defineProps({
     source: {type: Source, required: true}
   });
   ```

   The link label reads from the dictionary instead of plain text:

   ```vue
   <pv-button
       as="a"
       :href="source.url.toString()"
       target="_blank"
       :label="t('read-more')"
       icon="pi pi-external-link"
       size="small"
       text />
   ```

   <details>
   <summary>src/news/presentation/components/source-summary.vue (so far)</summary>

   ```vue
   <script setup lang="js">
   import {Source} from "../../domain/model/source.entity.js";
   import {useI18n} from "vue-i18n";

   const {t} = useI18n();

   const {source} = defineProps({
     source: {type: Source, required: true}
   });
   </script>

   <template>
     <pv-popover ref="sourceSummary">
       <div class="flex flex-column gap-3 w-25rem">
         <div class="flex align-items-center gap-2">
           <pv-avatar :image="source.urlToLogo" :aria-label="source.name" shape="circle" size="large" />
           <span class="font-bold text-xl">{{ source.name }}</span>
         </div>
         <div v-if="source.description" class="text-color-secondary">
           {{ source.description }}
         </div>
         <div class="flex flex-column gap-2">
           <div v-if="source.category" class="flex align-items-center gap-2">
             <i class="pi pi-tag text-primary"></i>
             <span>{{ source.category }}</span>
           </div>
           <div v-if="source.language" class="flex align-items-center gap-2">
             <i class="pi pi-globe text-primary"></i>
             <span>{{ source.language.toUpperCase() }}</span>
           </div>
           <div v-if="source.country" class="flex align-items-center gap-2">
             <i class="pi pi-map-marker text-primary"></i>
             <span>{{ source.country.toUpperCase() }}</span>
           </div>
         </div>
         <div v-if="!source.url.isEmpty()" class="flex justify-content-end">
           <pv-button
               as="a"
               :href="source.url.toString()"
               target="_blank"
               :label="t('read-more')"
               icon="pi pi-external-link"
               size="small"
               text />
         </div>
       </div>
     </pv-popover>
   </template>

   <style scoped>
   </style>
   ```
   </details>

   **Note:** no commit here, `<pv-popover ref="sourceSummary">` has nothing to actually toggle it open, that's next.

5. **Add `toggle()` and expose it.** `ArticleItem`'s own `sourceSummary.value.toggle(event)`, from US002, calls exactly this method.

   A `ref` for the popover, the method that toggles it, and `defineExpose` so the parent can call it:

   ```javascript
   const sourceSummary = ref();

   const toggle = (event) => {
     sourceSummary.value.toggle(event);
   };

   defineExpose({toggle});
   ```

   <details>
   <summary>src/news/presentation/components/source-summary.vue (Full file)</summary>

   ```vue
   <script setup lang="js">
   import {Source} from "../../domain/model/source.entity.js";
   import {useI18n} from "vue-i18n";
   import {ref} from "vue";

   const {t} = useI18n();

   const {source} = defineProps({
     source: {type: Source, required: true}
   });

   const sourceSummary = ref();

   const toggle = (event) => {
     sourceSummary.value.toggle(event);
   };

   defineExpose({toggle});
   </script>

   <template>
     <pv-popover ref="sourceSummary">
       <div class="flex flex-column gap-3 w-25rem">
         <div class="flex align-items-center gap-2">
           <pv-avatar :image="source.urlToLogo" :aria-label="source.name" shape="circle" size="large" />
           <span class="font-bold text-xl">{{ source.name }}</span>
         </div>
         <div v-if="source.description" class="text-color-secondary">
           {{ source.description }}
         </div>
         <div class="flex flex-column gap-2">
           <div v-if="source.category" class="flex align-items-center gap-2">
             <i class="pi pi-tag text-primary"></i>
             <span>{{ source.category }}</span>
           </div>
           <div v-if="source.language" class="flex align-items-center gap-2">
             <i class="pi pi-globe text-primary"></i>
             <span>{{ source.language.toUpperCase() }}</span>
           </div>
           <div v-if="source.country" class="flex align-items-center gap-2">
             <i class="pi pi-map-marker text-primary"></i>
             <span>{{ source.country.toUpperCase() }}</span>
           </div>
         </div>
         <div v-if="!source.url.isEmpty()" class="flex justify-content-end">
           <pv-button
               as="a"
               :href="source.url.toString()"
               target="_blank"
               :label="t('read-more')"
               icon="pi pi-external-link"
               size="small"
               text />
         </div>
       </div>
     </pv-popover>
   </template>

   <style scoped>
   </style>
   ```
   </details>

   **Note:** `ref="sourceSummary"` inside this component's own template (on the `<pv-popover>`) and the `sourceSummary` this component exposes to *its* parent are two different bindings with the same name, one is the local handle this file uses to call PrimeVue's own `.toggle()` on the popover element, the other is what `defineExpose({toggle})` hands upward so `ArticleItem` can call *this* component's `toggle()` in turn.

   ```
   git add .
   git commit -m "feat(news): add SourceSummary component."
   ```

6. **Wire `SourceSummary` into `ArticleItem`.**

   `SourceSummary` joins the script's imports, and the popover goes inside the subtitle, next to the source row that toggles it:

   ```vue
   <source-summary ref="sourceSummary" :source="article.source" />
   ```

   <details>
   <summary>src/news/presentation/components/article-item.vue (Full file)</summary>

   ```vue
   <script lang="js" setup>
   import {useI18n} from "vue-i18n";
   import {Article} from "../../domain/model/article.entity.js";
   import SourceSummary from "./source-summary.vue";
   import {ref} from "vue";

   const {t} = useI18n();

   const { article } = defineProps({article: {type: Article, required: true}});

   const emit = defineEmits(['article-shared']);

   const sourceSummary = ref();

   const toggleSourceSummary = event => {
     sourceSummary.value.toggle(event);
   };

   const shareArticle = async () => {
     const shareData = {title: article.title, url: article.url.toString()};
     if (navigator.share) {
       try {
         await navigator.share(shareData);
         console.log('Article shared successfully');
       } catch (err) {
         console.error('Error sharing the article:', err);
       }
     } else {
       try {
         await navigator.clipboard.writeText(shareData.url);
         emit('article-shared', shareData.url);
         console.log('Article URL copied to clipboard');
       } catch (err) {
         console.error('Failed to copy the article URL:', err);
       }
     }
   };

   </script>

   <template>
     <pv-card class="m-2">
       <template #header>
         <img :alt="article.title" :src="article.urlToImage.toString()" class="image-fit"/>
       </template>
       <template #title>
         <p class="flex align-content-start flex-wrap">
           {{ article.title }}
         </p>
       </template>
       <template #subtitle>
         <div class="flex flex-column gap-2">
           <p class="flex align-content-start flex-wrap cursor-pointer" @click="toggleSourceSummary">
             <span class="flex align-items-center justify-content-center mr-2">
               <pv-avatar :aria-label="article.source.name"
                          :image="article.source.urlToLogo"
                          shape="circle"/>
             </span>
             <span class="flex align-items-center justify-content-center font-bold">
               {{ article.source.name }}
             </span>
           </p>
           <p v-if="article.author" class="flex align-content-start flex-wrap">
             <span class="text-sm">{{ t('article.by') }} {{ article.author }}</span>
           </p>
           <p class="flex align-content-start flex-wrap">
             <span class="text-sm">{{ t('article.published-on') }} {{ article.getFormatedPublishedAt() }}</span>
           </p>
         </div>
         <source-summary ref="sourceSummary" :source="article.source" />
       </template>
       <template #content>
         <p class="flex align-content-start flex-wrap mt-4">
           {{ article.description }}
         </p>
       </template>
       <template #footer>
         <div class="flex justify-content-between align-items-center">
           <pv-button v-if="!article.url.isEmpty()" as="a" :href="article.url.toString()" target="_blank"
                      :label="t('read-more')" link class="p-0" />
           <pv-button
               v-if="!article.url.isEmpty()"
               v-tooltip="t('article.copy-to-clipboard')"
               :label="t('article.share')"
               aria-label="Share article"
               text
               size="small"
               icon="pi pi-share-alt"
               @click="shareArticle"/>
         </div>
       </template>
     </pv-card>
   </template>

   <style scoped>
   .image-fit {
     width: 100%;
     height: 100%;
     object-fit: cover;
   }
   </style>
   ```
   </details>

   **Note:** `ref="sourceSummary"` on `<source-summary>` here is `ArticleItem`'s own handle onto the *whole* `SourceSummary` component instance, specifically the `{toggle}` object it exposed with `defineExpose` in the previous step. `sourceSummary.value.toggle(event)` in `toggleSourceSummary` (from US002) was always calling forward to this, waiting for `SourceSummary` to exist.

   ```
   git add .
   git commit -m "feat(news): show SourceSummary from ArticleItem."
   ```

7. **Run it.**

   ```
   npm run dev
   ```

   Click a source's name or avatar on any article card, a popover opens with its description, category, language, country, and a link to its website. Stop the server with `Ctrl+C`.

8. **Publish and finish the feature.**

---

## Prepare the First Release

**Still on `develop`.** All four user stories are merged. Every real public repo ships a `LICENSE.md`, a `README.md`, and a `CONTRIBUTING.md`, but none of them belonged earlier, back then there was nothing to describe yet.

1. **Run every scenario end to end.** `npm run dev`, then in the browser: confirm the drawer lists real sources with the first one active (US001); choosing a different source loads its articles, each with a title, author when present, and a formatted date (US002); switch the language and watch every string change, including the articles (US003); click a source's name and confirm the popover shows its details (US004). Then check every scenario in `docs/user-stories.md` against what the app actually does. No automated test drives these end to end, so this manual run is the acceptance check. Stop the server with `Ctrl+C`.

2. **Add doc comments to every class.** Every class and file built across the four user stories gets its JSDoc pass here, in one place, instead of interrupting the flow of each story to document a single file.

   <details>
   <summary>.env.development (Full file with doc comments)</summary>

   ```
   # Environment: Development
   # Description: This file contains the environment variables for the development environment.
   # Note: In real scenarios, this file is not committed to the repository.

   # VITE_NEWS_API_KEY is the API key for the News API.
   VITE_NEWS_API_KEY="0d5b87d6eed74a768b7f2f7a3ca1bafb"
   # VITE_NEWS_API_URL is the base URL for the News API.
   VITE_NEWS_API_URL="https://newsapi.org/v2"
   # VITE_LOGO_API_URL is the base URL for the Logo.dev API.
   VITE_LOGO_API_URL="https://img.logo.dev"
   # VITE_LOGO_PUBLISHABLE_API_KEY is the publishable API key for the Logo.dev API.
   VITE_LOGO_PUBLISHABLE_API_KEY="pk_bufKzaXPQFeNkMz5gxZWAA"
   # VITE_SOURCES_ENDPOINT_PATH is the path to the news sources endpoint.
   VITE_SOURCES_ENDPOINT_PATH="/sources"
   # VITE_TOP_HEADLINES_ENDPOINT_PATH is the path to the top headlines endpoint.
   VITE_TOP_HEADLINES_ENDPOINT_PATH="/top-headlines"
   # VITE_PRIME_UI_LICENSE_KEY is the license key for the Prime UI library.
   VITE_PRIME_UI_LICENSE_KEY="eyJpZCI6IjZlODA0NjNhLTJkMGMtNGI2ZC1iYmI1LTAwYjk3OWFkMGFmNCIsInByb2R1Y3QiOiJwcmltZXVpIiwidGllciI6ImNvbW11bml0eSIsInR5cGUiOiJkZXYiLCJpYXQiOjE3ODk1NTQ5MzAsImV4cCI6MTgyMTA5MDkzMH0.yvULBRGTn5hRzalLkmTf6BZaJYSwrK2LS6hLxTtO9fI0W2sgsCFpfcVHjfbqEQQe3i84X_KEZQv-WAQbRj9IAg"
   ```
   </details>

   <details>
   <summary>.env.production (Full file with doc comments)</summary>

   ```
   # Environment: Production
   # Description: This file contains the environment variables for the production environment.
   # Note: In real scenarios, this file is not committed to the repository.

   # VITE_NEWS_API_KEY is the API key for the News API.
   VITE_NEWS_API_KEY="0d5b87d6eed74a768b7f2f7a3ca1bafb"
   # VITE_NEWS_API_URL is the base URL for the News API.
   VITE_NEWS_API_URL="https://newsapi.org/v2"
   # VITE_LOGO_API_URL is the base URL for the Logo.dev API.
   VITE_LOGO_API_URL="https://img.logo.dev"
   # VITE_LOGO_PUBLISHABLE_API_KEY is the publishable API key for the Logo.dev API.
   VITE_LOGO_PUBLISHABLE_API_KEY="pk_bufKzaXPQFeNkMz5gxZWAA"
   # VITE_SOURCES_ENDPOINT_PATH is the path to the news sources endpoint.
   VITE_SOURCES_ENDPOINT_PATH="/sources"
   # VITE_TOP_HEADLINES_ENDPOINT_PATH is the path to the top headlines endpoint.
   VITE_TOP_HEADLINES_ENDPOINT_PATH="/top-headlines"
   # VITE_PRIME_UI_LICENSE_KEY is the license key for the Prime UI library.
   VITE_PRIME_UI_LICENSE_KEY="eyJpZCI6IjZlODA0NjNhLTJkMGMtNGI2ZC1iYmI1LTAwYjk3OWFkMGFmNCIsInByb2R1Y3QiOiJwcmltZXVpIiwidGllciI6ImNvbW11bml0eSIsInR5cGUiOiJkZXYiLCJpYXQiOjE3ODk1NTQ5MzAsImV4cCI6MTgyMTA5MDkzMH0.yvULBRGTn5hRzalLkmTf6BZaJYSwrK2LS6hLxTtO9fI0W2sgsCFpfcVHjfbqEQQe3i84X_KEZQv-WAQbRj9IAg"
   ```
   </details>

   <details>
   <summary>src/vite-env.d.ts (Full file with doc comments)</summary>

   ```typescript
   /**
    * Custom type definitions for the Vite environment variables.
    *
    * @remarks
    * This allows for better type checking and autocompletion when using the environment variables in the code.
    */

   /// <reference types="vite/client" />
   interface ImportMetaEnv {
     readonly VITE_NEWS_API_KEY: string;
     readonly VITE_NEWS_API_URL: string;
     readonly VITE_LOGO_API_URL: string;
     readonly VITE_LOGO_PUBLISHABLE_API_KEY: string;
     readonly VITE_SOURCES_ENDPOINT_PATH: string;
     readonly VITE_TOP_HEADLINES_ENDPOINT_PATH: string;
     readonly VITE_PRIME_UI_LICENSE_KEY: string;
   }

   interface ImportMeta {
     readonly env: ImportMetaEnv;
   }
   ```
   </details>

   <details>
   <summary>src/main.js (Full file with doc comments)</summary>

   ```javascript
   import { createApp } from 'vue'
   import './style.css'
   import App from './app.vue'
   import i18n from "./i18n.js";
   import PrimeVue from 'primevue/config';
   import Material from '@primeuix/themes/material';
   import 'primeicons/primeicons.css';
   import 'primeflex/primeflex.css';
   import {Avatar, Button, Card, Drawer, Menu, Menubar, Popover, SelectButton, Toolbar, Tooltip} from "primevue";

   const primeUiLicenseKey = import.meta.env.VITE_PRIME_UI_LICENSE_KEY;
   /**
    * Application composition root.
    *
    * @remarks
    * Specifies the main entry point of the Vue application, configuring global plugins, components, and mounting the app to the DOM.
    */

   createApp(App)
       .use(i18n)
       .use(PrimeVue, { ripple: true, theme: { preset: Material }, license: primeUiLicenseKey })
       .component('pv-button', Button)
       .component('pv-select-button', SelectButton)
       .component('pv-avatar', Avatar)
       .component('pv-drawer', Drawer)
       .component('pv-card', Card)
       .component('pv-toolbar', Toolbar)
       .component('pv-menu', Menu)
       .component('pv-menubar', Menubar)
       .component('pv-popover', Popover)
       .directive('tooltip', Tooltip)
       .mount('#app')
   ```
   </details>

   <details>
   <summary>src/app.vue (Full file with doc comments)</summary>

   ```vue
   <script setup>
     import Layout from "./shared/presentation/components/layout.vue";

     /**
      * Presentation shell component.
      *
      * @remarks
      * Hosts the application layout and keeps bootstrapping concerns out of
      * feature components.
      */
   </script>

   <template>
     <layout/>
   </template>

   <style scoped>

   </style>
   ```
   </details>

   <details>
   <summary>src/news/domain/model/source.entity.js (Full file with doc comments)</summary>

   ```javascript
   import {StringValidator} from "../../../shared/domain/model/string-validator.js";
   import {Url} from "../../../shared/domain/model/url.js";

   /**
    * Domain entity representing a news provider.
    *
    * @remarks
    * This model belongs to the domain layer and encapsulates the identity and
    * attributes of a news source. It remains independent of external API structures.
    * Every field is set once, in the constructor, there are no setters: `urlToLogo` is
    * resolved by the assembler before the entity is built, never assigned after.
    */
   export class Source {
       #id;
       #name;
       #description;
       #url;
       #category;
       #language;
       #country;
       #urlToLogo;

       /**
        * Creates a new Source entity instance.
        *
        * @param {Object} source - The source's identity, name, and optional details.
        * @param {string} [source.id] - Unique identifier for the source (e.g., 'bbc-news').
        * @param {string} [source.name] - Display name of the news source.
        * @param {string} [source.description] - A short description of the news source.
        * @param {string|Url} [source.url] - The website URL of the news source.
        * @param {string} [source.category] - The category the news source belongs to.
        * @param {string} [source.language] - The primary language of the source (ISO code).
        * @param {string} [source.country] - The country of origin (ISO code).
        * @param {string|Url} [source.urlToLogo] - The logo image URL, already resolved by the assembler.
        * @throws {Error} If id or name is empty.
        */
       constructor({id = "", name = "", description = "", url = "", category = "", language = "", country = "", urlToLogo = ""}) {
           if (!StringValidator.isNotEmptyString(id)) throw new Error('Source id must be a non-empty string');
           if (!StringValidator.isNotEmptyString(name)) throw new Error('Source name must be a non-empty string');

           this.#id = id.trim();
           this.#name = name.trim();
           this.#description = description;
           this.#url = url instanceof Url ? url : new Url(url);
           this.#category = category;
           this.#language = language;
           this.#country = country;
           this.#urlToLogo = urlToLogo;
       }

       /** @returns {string} */
       get id() {
           return this.#id;
       }

       /** @returns {string} */
       get name() {
           return this.#name;
       }

       /** @returns {string} */
       get description() {
           return this.#description;
       }

       /** @returns {Url} */
       get url() {
           return this.#url;
       }

       /** @returns {string} */
       get category() {
           return this.#category;
       }

       /** @returns {string} */
       get language() {
           return this.#language;
       }

       /** @returns {string} */
       get country() {
           return this.#country;
       }

       /** @returns {string} */
       get urlToLogo() {
           return this.#urlToLogo;
       }
   }
   ```
   </details>

   <details>
   <summary>src/shared/domain/model/string-validator.js (Full file with doc comments)</summary>

   ```javascript
   /**
    * Domain utility for string-based type validation.
    *
    * @remarks
    * Provides static methods to enforce string constraints across the domain.
    */
   export class StringValidator {
       /**
        * Checks if a value is a string primitive or a String object.
        *
        * @param {*} value - The value to evaluate.
        * @returns {boolean} True if the value is a string, false otherwise.
        */
       static isString(value) {
           return typeof value === 'string' || value instanceof String;
       }

       /**
        * Checks if a value is a string that contains at least one non-whitespace character.
        *
        * @param {*} value - The value to evaluate.
        * @returns {boolean} True if the value is a non-empty string, false otherwise.
        */
       static isNotEmptyString(value) {
           return this.isString(value) && value.trim().length > 0;
       }
   }
   ```
   </details>

   <details>
   <summary>src/shared/domain/model/url.js (Full file with doc comments)</summary>

   ```javascript
   /**
    * Value object representing a URL within the domain.
    *
    * @remarks
    * This value object ensures that URL values are well-formed according to
    * RFC standards and provides a consistent way to handle URLs. It is immutable.
    */
   export class Url {
       /** @type {string} */
       #value;

       /**
        * Validates if a string is a well-formed URL.
        *
        * @param {string} url - The URL string to validate.
        * @returns {boolean} True if the URL is valid, false otherwise.
        */
       static isValidUrl(url) {
           if (typeof url !== 'string' && !(url instanceof String)) return false;
           if (URL.canParse) {
               return URL.canParse(url);
           }
           try {
               new URL(url);
               return true;
           } catch (_) {
               return false;
           }
       }

       /**
        * Creates a new Url instance.
        *
        * @param {string} value - The URL string.
        */
       constructor(value) {
           this.#value = Url.isValidUrl(value) ? value : '';
       }

       /**
        * Returns the string representation of the URL.
        * @returns {string}
        */
       toString() {
           return this.#value;
       }

       /**
        * Checks if the URL is empty.
        * @returns {boolean}
        */
       isEmpty() {
           return this.#value === '';
       }

       /**
        * Returns the primitive value of the Url.
        * @returns {string}
        */
       valueOf() {
           return this.#value;
       }

       /**
        * Checks for equality with another Url instance.
        * @param {Url} other - The other Url to compare.
        * @returns {boolean}
        */
       equals(other) {
           return other instanceof Url && this.#value === other.toString();
       }
   }
   ```
   </details>

   <details>
   <summary>src/shared/infrastructure/logo-dev-api.js (Full file with doc comments)</summary>

   ```javascript
   const logoApiUrl = import.meta.env.VITE_LOGO_API_URL;
   const apiKey = import.meta.env.VITE_LOGO_PUBLISHABLE_API_KEY;

   /**
    * Infrastructure helper for building Logo.dev image URLs.
    *
    * @remarks
    * Encapsulates the logic for constructing URLs to retrieve source logos
    * from the Logo.dev external service.
    */
   export class LogoDevApi {
       /**
        * Constructs a logo URL based on a news source's website host.
        *
        * @param {import('../domain/model/url.js').Url} url - The website URL of the source.
        * @returns {string} The fully qualified URL to the source's logo image.
        */
       getUrlToLogo = url => `${logoApiUrl}/${new URL(url.toString()).host}?token=${apiKey}`;
   }
   ```
   </details>

   <details>
   <summary>src/news/infrastructure/news-resources.js (Full file with doc comments)</summary>

   ```javascript
   /**
    * Source data structure as returned by the NewsAPI.
    *
    * @typedef {Object} SourceResource
    * @property {string} [id] - The unique identifier for the source.
    * @property {string} [name] - The name of the source.
    * @property {string} [description] - A description of the source.
    * @property {string} [url] - The website URL of the source.
    * @property {string} [category] - The category of the source.
    * @property {string} [language] - The language the source is written in.
    * @property {string} [country] - The country the source originates from.
    */

   /**
    * API response structure for news sources.
    *
    * @typedef {Object} SourcesResponse
    * @property {string} status - The status of the response ('ok' or 'error').
    * @property {SourceResource[]} sources - The list of sources returned.
    */

   /**
    * Article data structure as returned by the NewsAPI.
    *
    * @typedef {Object} ArticleResource
    * @property {string} [title] - The title of the article.
    * @property {string} [description] - The description or summary of the article.
    * @property {string} [url] - The URL to the article.
    * @property {string} [urlToImage] - The URL to the article's image.
    * @property {string} [publishedAt] - The ISO 8601 timestamp of publication.
    * @property {SourceResource} [source] - The source of the article.
    */

   /**
    * API response structure for news articles.
    *
    * @typedef {Object} ArticlesResponse
    * @property {string} status - The status of the response ('ok' or 'error').
    * @property {ArticleResource[]} articles - The list of articles returned.
    */

   export {}
   ```
   </details>

   <details>
   <summary>src/shared/infrastructure/error.interceptor.js (Full file with doc comments)</summary>

   ```javascript
   /**
    * Axios interceptor for centralized error handling.
    *
    * @remarks
    * This object contains the success and error handlers for Axios response interceptors.
    * It simplifies error messages from the server and provides fallback messages for
    * network errors.
    */
   export const errorInterceptor = {
       /**
        * Handles successful responses.
        * @param {import('axios').AxiosResponse} response - The Axios response.
        * @returns {import('axios').AxiosResponse} The same response.
        */
       onResponse: (response) => response,

       /**
        * Handles error responses.
        * @param {import('axios').AxiosError} error - The Axios error.
        * @returns {Promise<never>} A rejected promise with a user-friendly error message.
        */
       onError: (error) => {
           let message;

           if (error.response) {
               // The request was made, and the server responded with a status code
               // that falls out of the range of 2xx
               console.error("Data:", error.response.data);
               console.error("Status:", error.response.status);
               console.error("Headers:", error.response.headers);

               message = error.response.data["message"] || `Error ${error.response.status}: ${error.response.statusText}`;
           } else if (error.request) {
               // The request was made but no response was received
               console.error("Request:", error.request);
               message = "No response received from the server. Please check your internet connection.";
           } else {
               // Something happened in setting up the request that triggered an Error
               console.error("Error Message:", error.message);
               message = error.message;
           }

           return Promise.reject(message);
       }
   };
   ```
   </details>

   <details>
   <summary>src/news/infrastructure/news-api.js (Full file with doc comments)</summary>

   ```javascript
   import axios from "axios";
   import "./news-resources.js";
   import {errorInterceptor} from "../../shared/infrastructure/error.interceptor.js";

   /**
    * Infrastructure adapter for NewsAPI HTTP endpoints.
    *
    * @remarks
    * This class isolates external transport concerns from the application and
    * domain layers.
    */
   const newsApi               = import.meta.env.VITE_NEWS_API_URL;
   const apiKey                = import.meta.env.VITE_NEWS_API_KEY;
   const sourcesEndpoint       = import.meta.env.VITE_SOURCES_ENDPOINT_PATH;
   const topHeadlinesEndpoint  = import.meta.env.VITE_TOP_HEADLINES_ENDPOINT_PATH;

   /**
    * Axios instance configured for NewsAPI requests.
    *
    * @remarks
    * This instance is configured with the base URL and API key for the NewsAPI.
    *
    * @type {axios.AxiosInstance}
    */
   const http = axios.create({
       baseURL: newsApi,
       params: {
           apiKey: apiKey,
       },
   })

   // Add a response interceptor
   http.interceptors.response.use(errorInterceptor.onResponse, errorInterceptor.onError);

   /**
    * Infrastructure adapter for interacting with the NewsAPI HTTP service.
    *
    * @remarks
    * This class isolates external transport concerns, providing a clean interface
    * for the application layer to fetch news data.
    */
   export class NewsApi {

       /**
        * Retrieves all available news sources from the provider.
        *
        * @returns {Promise<import('axios').AxiosResponse<SourcesResponse>>} A promise resolving to the Axios response containing sources.
        */
       getSources = () => http.get(`${sourcesEndpoint}`);

       /**
        * Retrieves top headlines for a specific news source.
        *
        * @param {string} sourceId - The unique identifier of the news source (e.g., 'cnn').
        * @returns {Promise<import('axios').AxiosResponse<ArticlesResponse>>} A promise resolving to the Axios response containing articles.
        */
       getArticlesForSourceId = sourceId => http.get(`${topHeadlinesEndpoint}`, {params: {sources: sourceId}});

   }
   ```
   </details>

   <details>
   <summary>src/news/infrastructure/source.assembler.js (Full file with doc comments)</summary>

   ```javascript
   import {Source} from "../domain/model/source.entity.js";
   import {Url} from "../../shared/domain/model/url.js";
   import {LogoDevApi} from "../../shared/infrastructure/logo-dev-api.js";
   import "./news-resources.js";

   /**
    * Infrastructure service that maps source data from API responses into Domain Entities.
    *
    * @remarks
    * Following DDD patterns, this assembler acts as a Data Mapper between the
    * infrastructure-specific source format and the Source domain entity.
    */
   export class SourceAssembler {
       #logoApi;

       /**
        * Initializes the SourceAssembler.
        */
       constructor() {
           this.#logoApi = new LogoDevApi();
       }

       /**
        * Maps a full Axios response containing source resources into an array of Source entities.
        *
        * @param {import('axios').AxiosResponse<SourcesResponse>} response - The HTTP response from the news provider.
        * @returns {Source[]} An array of Source domain entities. Returns an empty array if the status is not 'ok'.
        */
       toEntitiesFromResponse(response) {
           if (response.data.status !== "ok") {
               console.error(`${response.data["status"]},  ${response.data["code"]}, ${response.data["message"]}`);
               return [];
           }
           const sourcesResponse = response.data;
           return sourcesResponse.sources.map((source) => {
               try {
                   return this.toEntityFromResource(source);
               } catch (error) {
                   console.error('Validation error for source:', error.message, source);
                   return null;
               }
           }).filter(source => source !== null);
       }

       /**
        * Maps a single source resource into a Source domain entity, including logo URL resolution.
        *
        * @param {SourceResource} resource - The source data as received from the external API.
        * @returns {Source} The assembled Source domain entity.
        */
       toEntityFromResource(resource) {
           const url = resource.url instanceof Url ? resource.url : new Url(resource.url);
           const urlToLogo = !url.isEmpty() ? this.#logoApi.getUrlToLogo(url) : '';
           return new Source({...resource, url, urlToLogo});
       }
   }
   ```
   </details>

   <details>
   <summary>src/news/application/news.store.js (Full file with doc comments)</summary>

   ```javascript
   import {computed, ref, shallowRef} from "vue";
   import {NewsApi} from "../infrastructure/news-api.js";
   import {SourceAssembler} from "../infrastructure/source.assembler.js";
   import {ArticleAssembler} from "../infrastructure/article.assembler.js";

   /**
    * Application service that coordinates read models for the News bounded context.
    *
    * @remarks
    * The state is private. Domain entities are held in `shallowRef` so Vue never wraps
    * them in a Proxy, plain UI state (error messages) stays in a regular `ref`.
    */
   class NewsStore {
       #newsApi = new NewsApi();
       #sourceAssembler = new SourceAssembler();

       /**
        * All available sources, replaced as a whole when they load.
        *
        * @type {import('vue').ShallowRef<import('../domain/model/source.entity.js').Source[]>}
        */
       #sources = shallowRef([]);

       /**
        * Article lists cached by source id.
        *
        * @type {import('vue').ShallowRef<Record<string, import('../domain/model/article.entity.js').Article[]>>}
        */
       #articles = shallowRef({});

       /**
        * The currently selected source.
        *
        * @type {import('vue').ShallowRef<import('../domain/model/source.entity.js').Source | undefined>}
        */
       #currentSource = shallowRef(undefined);

       /**
        * Error messages encountered while loading data.
        *
        * @type {import('vue').Ref<string[]>}
        */
       #errors = ref([]);

       /**
        * Id of the currently selected source, used to mark it as active in the navigation list.
        */
       #currentSourceId = computed(() => this.#currentSource.value?.id);

       /**
        * Articles of the currently selected source.
        */
       #currentSourceArticles = computed(() => {
           const id = this.#currentSourceId.value;
           return id ? (this.#articles.value[id] ?? []) : [];
       });

       /**
        * @returns {import('../domain/model/source.entity.js').Source[]}
        */
       get sources() {
           return this.#sources.value;
       }

       /**
        * @returns {Record<string, import('../domain/model/article.entity.js').Article[]>}
        */
       get articles() {
           return this.#articles.value;
       }

       /**
        * @returns {string[]}
        */
       get errors() {
           return this.#errors.value;
       }

       /**
        * @returns {import('../domain/model/source.entity.js').Source | undefined}
        */
       get currentSource() {
           return this.#currentSource.value;
       }

       /**
        * @returns {string | undefined}
        */
       get currentSourceId() {
           return this.#currentSourceId.value;
       }

       /**
        * @returns {import('../domain/model/article.entity.js').Article[]}
        */
       get currentSourceArticles() {
           return this.#currentSourceArticles.value;
       }

       /**
        * Selects a source and triggers article loading for it.
        *
        * @param {import('../domain/model/source.entity.js').Source} source
        * @returns {void}
        */
       setCurrentSource(source) {
           this.#currentSource.value = source;
           this.loadArticlesForCurrentSource();
       }

       /**
        * Loads the source list from the provider and selects the first source.
        *
        * @returns {void}
        */
       loadSources() {
           if (this.#sources.value.length > 0) return;
           this.#errors.value = [];
           this.#newsApi.getSources().then(response => {
               const sources = this.#sourceAssembler.toEntitiesFromResponse(response);
               this.#sources.value = sources;
               if (sources.length > 0) this.setCurrentSource(sources[0]);
           }).catch(message => {
               this.#errors.value.push(message);
               this.#sources.value = [];
           });
       }

       /**
        * Loads articles for the selected source when they are not already cached.
        *
        * @returns {void}
        */
       loadArticlesForCurrentSource() {
           const source = this.#currentSource.value;
           if (!source) return;
           if (this.#articles.value[source.id]) return;
           this.#newsApi.getArticlesForSourceId(source.id).then(response => {
               const articles = new ArticleAssembler(source).toEntitiesFromResponse(response);
               this.#articles.value = {...this.#articles.value, [source.id]: articles};
           }).catch(message => {
               this.#errors.value.push(message);
           });
       }
   }

   /**
    * The single store instance shared by the presentation layer.
    */
   export const newsStore = new NewsStore();
   ```
   </details>

   <details>
   <summary>src/news/presentation/components/source-item.vue (Full file with doc comments)</summary>

   ```vue
   <script setup lang="js">
     import {Source} from "../../domain/model/source.entity.js";

     /**
      * Presentation component for a single news source item.
      *
      * @remarks
      * Displays source details and emits a selection event when clicked.
      */

     /**
      * Properties for the SourceItem component.
      *
      * @typedef {Object} SourceItemProps
      * @property {Source} source - The source entity to display.
      */

     /**
      * Emitted events for the SourceItem component.
      *
      * @typedef {Object} SourceItemEmits
      * @property {(event: 'source-selected', source: Source) => void} source-selected - Emitted when the source is clicked.
      */

     /** @type {SourceItemProps} */
     const { source } = defineProps({ source: { type: Source, required: true } });
     /** @type {SourceItemEmits['emit']} */
     const emit  = defineEmits(['source-selected']);

     /**
      * Emits a selected source to the parent component.
      *
      * @returns {void}
      */
     const emitSourceSelectedEvent = () => {
       emit('source-selected', source);
     };
   </script>

   <template>
     <div class="m-4">
       <div @click="emitSourceSelectedEvent" class="flex align-content-start flex-wrap cursor-pointer hover:bg-emphasis p-2 border-round transition-colors transition-duration-150">
       <span  class="flex align-items-center justify-content-center mr-2">
             <pv-avatar :aria-label="source.name"
                        :image="source.urlToLogo"
                        shape="circle"/>
       </span>
         <span  class="flex align-items-center justify-content-center font-medium">
             {{source.name}}
       </span>
       </div>
     </div>
   </template>

   <style scoped>

   </style>
   ```
   </details>

   <details>
   <summary>src/news/presentation/components/source-list.vue (Full file with doc comments)</summary>

   ```vue
   <script setup lang="js">
     import {Source} from "../../domain/model/source.entity.js";
     import SourceItem from "./source-item.vue";

     /**
      * Presentation component for displaying a list of selectable news sources.
      *
      * @remarks
      * Renders news sources within a navigation drawer and handles source selection.
      */

     /**
      * Properties for the SourceList component.
      *
      * @typedef {Object} SourceListProps
      * @property {boolean} visible - Controls the visibility of the source drawer.
      * @property {Source[]} sources - An array of news source entities to display.
      */

     /**
      * Emitted events for the SourceList component.
      *
      * @typedef {Object} SourceListEmits
      * @property {(event: 'source-selected', source: Source) => void} source-selected - Emitted when a source is selected from the list.
      * @property {(event: 'update:visible', visible: boolean) => void} update:visible - Emitted when the visibility of the drawer changes.
      */

     /** @type {SourceListProps} */
     const { visible, sources } = defineProps({ visible: Boolean, sources: Array });
     /** @type {SourceListEmits['emit']} */
     const emit  = defineEmits(['source-selected', 'update:visible']);

     /**
      * Emits the update:visible event for the container component.
      *
      * @param {boolean} value
      */
     const emitVisibilityUpdatedEvent = (value) => {
       emit('update:visible', value);
     };

     /**
      * Bubbles the selected source to the parent container.
      *
      * @param {Source} source
      * @returns {void}
      */
     const emitSourceSelectedEvent = source => {
       emit('source-selected', source);
     };
   </script>

   <template>
     <pv-drawer :visible="visible" @update:visible="emitVisibilityUpdatedEvent">
       <source-item v-for="source in sources"
                    :key="source.id"
                    :source="source"
                    @source-selected="emitSourceSelectedEvent(source)"/>
     </pv-drawer>
   </template>

   <style scoped>

   </style>
   ```
   </details>

   <details>
   <summary>src/shared/presentation/components/layout.vue (Full file with doc comments)</summary>

   ```vue
   <script lang="js" setup>

   import {newsStore} from "../../../news/application/news.store.js";
   import SourceList from "../../../news/presentation/components/source-list.vue";
   import LanguageSwitcher from "./language-switcher.vue";
   import ArticleList from "../../../news/presentation/components/article-list.vue";
   import UnavailableContent from "../../../news/presentation/components/unavailable-content.vue";
   import FooterContent from "./footer-content.vue";
   import {ref, computed, onMounted} from "vue";

   /**
    * Root presentation layout for the news application.
    *
    * @remarks
    * Coordinates the display of the menubar, news source drawer, and the main
    * content area (article list or error view). It bridges the UI with the
    * `newsStore` application service.
    */

   const drawerVisible = ref(false);

   /**
    * Toggles the source drawer visibility.
    *
    * @returns {void}
    */
   const toggleDrawer = () => {
     drawerVisible.value = !drawerVisible.value;
   };


   /** @type {import('vue').ComputedRef<import('../../../news/domain/model/source.entity.js').Source[]>} */
   const sources = computed(() => newsStore.sources);
   /** @type {import('vue').ComputedRef<Array<unknown>>} */
   const errors = computed(() => newsStore.errors);
   /** @type {import('vue').ComputedRef<import('../../../news/domain/model/article.entity.js').Article[]>} */
   const articles = computed(() => newsStore.currentSourceArticles);

   /**
    * Selects a source and refreshes article projections.
    *
    * @param {import('../../../news/domain/model/source.entity.js').Source} source
    * @returns {void}
    */
   const setSource = source => {
     newsStore.setCurrentSource(source);
     toggleDrawer();
   };

   onMounted(() => {
     newsStore.loadSources();
   });


   </script>

   <template>
     <div class="layout-container">
       <header class="sticky-header">
         <pv-menubar>
           <template #start>
             <pv-button icon="pi pi-bars" label="CatchUp"
                        text @click="toggleDrawer" class="mr-2"/>
             <source-list :sources="sources"
                          v-model:visible="drawerVisible"
                          @source-selected="setSource"/>
           </template>
           <template #end>
             <language-switcher/>
           </template>
         </pv-menubar>
       </header>
       <main class="content-padding">
         <article-list v-if="articles.length" :articles="articles"/>
         <unavailable-content v-else :errors="errors"/>
       </main>
       <footer>
         <footer-content/>
       </footer>
     </div>
   </template>

   <style scoped>
   .layout-container {
     display: flex;
     flex-direction: column;
     min-height: 100vh;
   }

   .sticky-header {
     position: sticky;
     top: 0;
     z-index: 1000;
   }

   .content-padding {
     padding: 1rem;
     flex: 1;
   }

   @media screen and (min-width: 768px) {
     .content-padding {
       padding: 2rem;
     }
   }
   </style>
   ```
   </details>

   <details>
   <summary>src/news/domain/model/article.entity.js (Full file with doc comments)</summary>

   ```javascript
   import {Source} from "./source.entity.js";
   import {StringValidator} from "../../../shared/domain/model/string-validator.js";
   import {DateTime} from "../../../shared/domain/model/date-time.js";
   import {Url} from "../../../shared/domain/model/url.js";

   /** Image shown when the provider sends none. */
   const NO_IMAGE_URL = 'https://placehold.co/600x400?text=No+Image';

   /**
    * Domain entity representing a news article.
    *
    * @remarks
    * This entity encapsulates the core attributes and behavior of a news article
    * within the domain. It ensures data integrity through validation in its
    * constructor, and every field is set once, in the constructor, with no setters.
    */
   export class Article {
       #author;
       #title;
       #description;
       #url;
       #urlToImage;
       #source;
       #publishedAt;

       /**
        * Creates a new Article instance.
        *
        * @param {Object} article - The article's content and the fully resolved source that published it.
        * @param {string} [article.author] - The article's byline, empty when unattributed.
        * @param {string} [article.title] - The title of the article.
        * @param {string} [article.description] - A brief summary of the article content.
        * @param {string|Url} [article.url] - The canonical URL of the article.
        * @param {string|Url} [article.urlToImage] - The URL to the main image of the article.
        * @param {Source} article.source - The fully resolved source that published the article.
        * @param {string|Date|DateTime} [article.publishedAt] - The publication timestamp.
        * @throws {Error} If title is empty, source is not a resolved Source entity, url is empty or invalid, or publishedAt is invalid/in the future.
        */
       constructor({author = '', title = '', description = '', url = '', urlToImage = '', source = null, publishedAt = ''}) {
           if (!StringValidator.isNotEmptyString(title)) throw new Error('Article title must be a non-empty string');
           if (!(source instanceof Source)) throw new Error('Article must have a resolved Source entity');

           let dateTime;
           try {
               dateTime = publishedAt instanceof DateTime ? publishedAt : new DateTime(publishedAt);
           } catch (e) {
               throw new Error('Article publishedAt must be a valid date');
           }
           if (dateTime.isFuture()) throw new Error('Article publishedAt cannot be in the future');

           const resolvedUrl = url instanceof Url ? url : new Url(url);
           if (resolvedUrl.isEmpty()) throw new Error('Article url must be a valid, non-empty URL');

           this.#author = author;
           this.#title = title.trim();
           this.#description = description;
           this.#url = resolvedUrl;
           const resolvedImage = urlToImage instanceof Url ? urlToImage : new Url(urlToImage);
           this.#urlToImage = resolvedImage.isEmpty() ? new Url(NO_IMAGE_URL) : resolvedImage;
           this.#source = source;
           this.#publishedAt = dateTime;
       }

       /** @returns {string} */
       get author() {
           return this.#author;
       }

       /** @returns {string} */
       get title() {
           return this.#title;
       }

       /** @returns {string} */
       get description() {
           return this.#description;
       }

       /** @returns {Url} */
       get url() {
           return this.#url;
       }

       /** @returns {Url} */
       get urlToImage() {
           return this.#urlToImage;
       }

       /** @returns {Source} */
       get source() {
           return this.#source;
       }

       /** @returns {DateTime} */
       get publishedAt() {
           return this.#publishedAt;
       }

       /**
        * Formats the publication date for display purposes.
        *
        * @returns {string} The formatted date string (e.g., MM/DD/YYYY, HH:MM AM/PM).
        */
       getFormatedPublishedAt() {
           return this.#publishedAt.format();
       }
   }
   ```
   </details>

   <details>
   <summary>src/shared/domain/model/date-time.js (Full file with doc comments)</summary>

   ```javascript
   /**
    * Value object representing a date and time within the domain.
    *
    * @remarks
    * This value object ensures that date-time values are valid and provides
    * consistent formatting and comparison logic. It is immutable.
    */
   export class DateTime {
       /** @type {Date} */
       #date;

       /**
        * Creates a new DateTime instance.
        *
        * @param {string|Date|number} value - The value to initialize the date with.
        * @throws {Error} If the provided value results in an invalid date.
        */
       constructor(value) {
           const date = new Date(value);
           if (isNaN(date.getTime())) {
               throw new Error('Invalid date-time value');
           }
           this.#date = date;
       }

       /**
        * Checks if this date-time is in the future relative to the current time.
        *
        * @returns {boolean} True if the date-time is in the future.
        */
       isFuture() {
           return this.#date > new Date();
       }

       /**
        * Formats the date-time for display.
        *
        * @param {string} [locale='en-US'] - The locale to use for formatting.
        * @param {Intl.DateTimeFormatOptions} [options] - Formatting options.
        * @returns {string} The formatted date-time string.
        */
       format(locale = 'en-US', options = {
           year: 'numeric',
           month: '2-digit',
           day: '2-digit',
           hour: '2-digit',
           minute: '2-digit'
       }) {
           return this.#date.toLocaleDateString(locale, options);
       }

       /**
        * Returns the underlying Date object.
        * @returns {Date}
        */
       toDate() {
           return new Date(this.#date.getTime());
       }

       /**
        * Returns the ISO string representation of the date-time.
        * @returns {string}
        */
       toISOString() {
           return this.#date.toISOString();
       }

       /**
        * Returns the primitive value of the DateTime (the timestamp).
        * @returns {number}
        */
       valueOf() {
           return this.#date.getTime();
       }

       /**
        * Static factory method to create a DateTime from the current time.
        * @returns {DateTime}
        */
       static now() {
           return new DateTime(new Date());
       }
   }
   ```
   </details>

   <details>
   <summary>src/news/infrastructure/article.assembler.js (Full file with doc comments)</summary>

   ```javascript
   import {SourceAssembler} from "./source.assembler.js";
   import {Article} from "../domain/model/article.entity.js";
   import "./news-resources.js";

   /**
    * Infrastructure service that maps article data from API responses into Domain Entities.
    *
    * @remarks
    * Following DDD patterns, this assembler acts as a Data Mapper between the
    * infrastructure-specific article format and the Article domain entity.
    */
   export class ArticleAssembler {
       #source;
       #sourceAssembler;

       /**
        * Initializes the ArticleAssembler.
        *
        * @param {import('../domain/model/source.entity.js').Source | null} [source=null] - An optional Source entity to associate with assembled articles.
        */
       constructor(source = null) {
           this.#source = source;
           this.#sourceAssembler = new SourceAssembler();
       }

       /**
        * Maps a full Axios response containing article resources into an array of Article entities.
        *
        * @param {import('axios').AxiosResponse<ArticlesResponse>} response - The HTTP response from the news provider.
        * @returns {Article[]} An array of Article domain entities. Returns an empty array if the status is not 'ok'.
        */
       toEntitiesFromResponse(response) {
           if (response.data.status !== "ok") {
               console.error(`${response.data["status"]},  ${response.data["code"]}, ${response.data["message"]}`);
               return [];
           }
           const articlesResponse = response.data;
           return articlesResponse["articles"].map((article) => {
               try {
                   return this.toEntityFromResource(article);
               } catch (error) {
                   console.error('Validation error for article:', error.message, article);
                   return null;
               }
           }).filter(article => article !== null);
       }

       /**
        * Maps a single article resource into an Article domain entity.
        *
        * @param {ArticleResource} resource - The article data as received from the external API.
        * @returns {Article} The assembled Article domain entity, with its source already resolved.
        */
       toEntityFromResource(resource) {
           const resolvedSource = this.#source && (this.#source.id === resource.source?.id || this.#source.name === resource.source?.name)
               ? this.#source
               : this.#sourceAssembler.toEntityFromResource(resource.source || {id: 'unknown', name: 'Unknown Source'});
           return new Article({...resource, source: resolvedSource});
       }
   }
   ```
   </details>

   <details>
   <summary>src/news/presentation/components/article-item.vue (Full file with doc comments)</summary>

   ```vue
   <script lang="js" setup>
   import {useI18n} from "vue-i18n";
   import {Article} from "../../domain/model/article.entity.js";
   import SourceSummary from "./source-summary.vue";
   import {ref} from "vue";

   /**
    * Presentation component for rendering a single article card.
    *
    * @remarks
    * This component is responsible for displaying article data and handling
    * UI-level interactions like sharing or copying the article URL.
    */

   /**
    * Properties for the ArticleItem component.
    *
    * @typedef {Object} ArticleItemProps
    * @property {Article} article - The article entity to display.
    */

   /**
    * Emitted events for the ArticleItem component.
    *
    * @typedef {Object} ArticleItemEmits
    * @property {(event: 'article-shared', articleUrl: string) => void} article-shared - Emitted when the article URL is copied to the clipboard.
    */

   const {t} = useI18n();

   /** @type {ArticleItemProps} */
   const { article } = defineProps({article: {type: Article, required: true}});


   /** @type {ArticleItemEmits['emit']} */
   const emit = defineEmits(['article-shared']);

   const sourceSummary = ref();

   /**
    * Toggles the source popover.
    *
    * @param {Event} event - The click event.
    */
   const toggleSourceSummary = event => {
     sourceSummary.value.toggle(event);
   };

   /**
    * Uses Web Share API when available; otherwise copies article URL.
    *
    * @returns {Promise<void>}
    */
   const shareArticle = async () => {
     const shareData = {title: article.title, url: article.url.toString()};
     if (navigator.share) {
       try {
         await navigator.share(shareData);
         console.log('Article shared successfully');
       } catch (err) {
         console.error('Error sharing the article:', err);
       }
     } else {
       try {
         await navigator.clipboard.writeText(shareData.url);
         emit('article-shared', shareData.url);
         console.log('Article URL copied to clipboard');
       } catch (err) {
         console.error('Failed to copy the article URL:', err);
       }
     }
   };

   </script>

   <template>
     <pv-card class="m-2">
       <template #header>
         <img :alt="article.title" :src="article.urlToImage.toString()" class="image-fit"/>
       </template>
       <template #title>
         <p class="flex align-content-start flex-wrap">
           {{ article.title }}
         </p>
       </template>
       <template #subtitle>
         <div class="flex flex-column gap-2">
           <p class="flex align-content-start flex-wrap cursor-pointer" @click="toggleSourceSummary">
             <span class="flex align-items-center justify-content-center mr-2">
               <pv-avatar :aria-label="article.source.name"
                          :image="article.source.urlToLogo"
                          shape="circle"/>
             </span>
             <span class="flex align-items-center justify-content-center font-bold">
               {{ article.source.name }}
             </span>
           </p>
           <p v-if="article.author" class="flex align-content-start flex-wrap">
             <span class="text-sm">{{ t('article.by') }} {{ article.author }}</span>
           </p>
           <p class="flex align-content-start flex-wrap">
             <span class="text-sm">{{ t('article.published-on') }} {{ article.getFormatedPublishedAt() }}</span>
           </p>
         </div>
         <source-summary ref="sourceSummary" :source="article.source" />
       </template>
       <template #content>
         <p class="flex align-content-start flex-wrap mt-4">
           {{ article.description }}
         </p>
       </template>
       <template #footer>
         <div class="flex justify-content-between align-items-center">
           <pv-button v-if="!article.url.isEmpty()" as="a" :href="article.url.toString()" target="_blank"
                      :label="t('read-more')" link class="p-0" />
           <pv-button
               v-if="!article.url.isEmpty()"
               v-tooltip="t('article.copy-to-clipboard')"
               :label="t('article.share')"
               aria-label="Share article"
               text
               size="small"
               icon="pi pi-share-alt"
               @click="shareArticle"/>
         </div>
       </template>
     </pv-card>
   </template>

   <style scoped>
   .image-fit {
     width: 100%;
     height: 100%;
     object-fit: cover;
   }
   </style>
   ```
   </details>

   <details>
   <summary>src/news/presentation/components/article-list.vue (Full file with doc comments)</summary>

   ```vue
   <script setup lang="js">
   import ArticleItem from "./article-item.vue";
   import {Article} from "../../domain/model/article.entity.js";

   /**
    * Presentation component for rendering a collection of article cards.
    *
    * @remarks
    * Iterates over an array of Article entities and renders an ArticleItem for each.
    */

   /**
    * Properties for the ArticleList component.
    *
    * @typedef {Object} ArticleListProps
    * @property {Article[]} articles - An array of Article entities to be displayed.
    */
   const { articles } = defineProps({ articles: { type: Array, required: true } });

   </script>

   <template>
     <div v-for="article in articles" :key="article.url.toString()">
       <article-item :article="article"/>
     </div>
   </template>

   <style scoped>

   </style>
   ```
   </details>

   <details>
   <summary>src/i18n.js (Full file with doc comments)</summary>

   ```javascript
   import en from "./locales/en.json";
   import es from "./locales/es.json";

   import {createI18n} from "vue-i18n";

   /**
    * Shared internationalization service used across presentation modules.
    */
   const i18n = createI18n({
       legacy: false,
       locale: "en",
       fallbackLocale: "en",
       messages: {en, es}
   });

   export default i18n;
   ```
   </details>

   <details>
   <summary>src/shared/presentation/components/language-switcher.vue (Full file with doc comments)</summary>

   ```vue
   <script setup lang="js">
   import { useI18n } from 'vue-i18n';

   /**
    * Presentation component for switching the application's locale.
    *
    * @remarks
    * Uses the vue-i18n instance to display and update the current language.
    */
   const { locale, availableLocales } = useI18n();
   </script>

   <template>
     <pv-select-button v-model="locale" :options="availableLocales">
       <template #option="slotProps">
         <span>{{ slotProps.option.toUpperCase() }}</span>
       </template>
     </pv-select-button>
   </template>

   <style scoped>

   </style>
   ```
   </details>

   <details>
   <summary>src/shared/presentation/components/footer-content.vue (Full file with doc comments)</summary>

   ```vue
   <script setup lang="js">
     import {useI18n} from "vue-i18n";

     /**
      * Presentation component for the application footer.
      *
      * @remarks
      * Displays attribution links, copyright information, and supports localization.
      */
     const { t } = useI18n();
   </script>

   <template>
     <div class="grid mt-4 p-4 justify-content-center bg-primary text-primary-contrast text-center">
       <div class="col-12 flex flex-column align-items-center">
         <p>Copyright &copy; 2026. ACME Studios</p>
       </div>
       <div  class="col-12 flex flex-column align-items-center mt-3">
         <p>
           {{ t('authoring-phrase.intro') }} <i class="pi pi-heart text-red-500"/>
           {{ t('authoring-phrase.use') }} <a href="https://primevue.org/" target="_blank" class="text-primary-contrast font-bold">PrimeVue</a>
           {{ t('authoring-phrase.author', {brand: 'ACME'}) }}
         </p>
         <p>{{ t('footer.powered-by') }} <a href="https://www.newsapi.org" class="text-primary-contrast font-bold">NewsAPI.org</a> {{ t('footer.and') }} <a href="https://logo.dev" class="text-primary-contrast font-bold">Logo.dev Logo API</a> </p>
       </div>
     </div>
   </template>

   <style scoped>

   </style>
   ```
   </details>

   <details>
   <summary>src/news/presentation/components/unavailable-content.vue (Full file with doc comments)</summary>

   ```vue
   <script setup lang="js">
     import {useI18n} from "vue-i18n";

     /**
      * Presentation component for displaying error messages or fallback content.
      *
      * @remarks
      * This component is rendered when no articles are available or an error occurs
      * during data fetching.
      */

     /**
      * Properties for the UnavailableContent component.
      *
      * @typedef {Object} UnavailableContentProps
      * @property {Array<string>} errors - A list of error message strings to display.
      */

     const { t } = useI18n();

     /** @type {UnavailableContentProps} */
     const { errors } = defineProps({ errors: { type: Array, default: () => [] } });
   </script>

   <template>
     <div class="flex flex-column align-items-center justify-content-center mt-8 text-muted-color">
       <i class="pi pi-exclamation-circle text-6xl mb-4" />
       <div><h4>{{ t('unavailable-news') }}</h4></div>
       <div v-for="error in errors" :key="error" class="mt-2">
         <h6>{{ error }}</h6>
       </div>
     </div>
   </template>

   <style scoped>

   </style>
   ```
   </details>

   <details>
   <summary>src/news/presentation/components/source-summary.vue (Full file with doc comments)</summary>

   ```vue
   <script setup lang="js">
   import {Source} from "../../domain/model/source.entity.js";
   import {useI18n} from "vue-i18n";
   import {ref} from "vue";

   /**
    * Presentation component for rendering news source details in a popover.
    *
    * @remarks
    * This component displays information about a news source, like its name, description, category, etc.
    * and provides a link to the source's website.
    */

   /**
    * Properties for the SourceSummary component.
    *
    * @typedef {Object} SourceSummaryProps
    * @property {Source} source - The source entity to display.
    */

   const {t} = useI18n();

   /** @type {SourceSummaryProps} */
   const {source} = defineProps({
     source: {type: Source, required: true}
   });

   /**
    * Reference to the popover component for toggling visibility.
    *
    */
   const sourceSummary = ref();

   /**
    * Toggles the popover visibility.
    *
    * @param {Event} event - The click event that triggered the popover.
    */
   const toggle = (event) => {
     sourceSummary.value.toggle(event);
   };

   /**
    * Exposes the toggle method to parent components.
    */
   defineExpose({toggle});
   </script>

   <template>
     <pv-popover ref="sourceSummary">
       <div class="flex flex-column gap-3 w-25rem">
         <div class="flex align-items-center gap-2">
           <pv-avatar :image="source.urlToLogo" :aria-label="source.name" shape="circle" size="large" />
           <span class="font-bold text-xl">{{ source.name }}</span>
         </div>
         <div v-if="source.description" class="text-color-secondary">
           {{ source.description }}
         </div>
         <div class="flex flex-column gap-2">
           <div v-if="source.category" class="flex align-items-center gap-2">
             <i class="pi pi-tag text-primary"></i>
             <span>{{ source.category }}</span>
           </div>
           <div v-if="source.language" class="flex align-items-center gap-2">
             <i class="pi pi-globe text-primary"></i>
             <span>{{ source.language.toUpperCase() }}</span>
           </div>
           <div v-if="source.country" class="flex align-items-center gap-2">
             <i class="pi pi-map-marker text-primary"></i>
             <span>{{ source.country.toUpperCase() }}</span>
           </div>
         </div>
         <div v-if="!source.url.isEmpty()" class="flex justify-content-end">
           <pv-button
               as="a"
               :href="source.url.toString()"
               target="_blank"
               :label="t('read-more')"
               icon="pi pi-external-link"
               size="small"
               text />
         </div>
       </div>
     </pv-popover>
   </template>

   <style scoped>
   </style>
   ```
   </details>

   ```
   git add .
   git commit -m "docs: add doc comments to every class."
   ```

3. **Add `LICENSE.md`.** Right-click the project root → `New` → `File` → type `LICENSE.md` → Enter.

   <details>
   <summary>LICENSE.md</summary>

   ```markdown
   # MIT License
   Copyright (c) 2026 Web Applications Development Team

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
   git commit -m "chore: add license."
   ```

4. **Add `README.md`.** Right-click the project root → `New` → `File` → type `README.md` → Enter.

   <details>
   <summary>README.md</summary>

   ````markdown
   # Catch Up

   Catch Up is a newsreader application that helps users browse news sources and read top headlines, built with a focus on Clean Architecture and Domain-Driven Design (DDD).

   ## Features

   - **Browse News Sources**: Select from a variety of news providers as your active feed.
   - **Top Headlines**: View the latest articles from the selected source.
   - **Article Summaries**: Read concise summaries and access original content.
   - **Source Interaction**: View detailed information about news sources through interactive summaries.
   - **Robust Content Handling**: Graceful handling of missing data and invalid URLs, with placeholder support.
   - **Multi-language Support**: Seamlessly switch between English (`en`) and Spanish (`es`).
   - **Responsive Design**: Optimized for different screen sizes using PrimeFlex.

   ## Technology Stack

   - **Framework**: Vue 3 (Composition API)
   - **Build Tool**: Vite
   - **UI Components**: PrimeVue + PrimeIcons
   - **CSS Utility**: PrimeFlex
   - **HTTP Client**: Axios
   - **Internationalization**: vue-i18n
   - **State Management**: Reactive Stores (based on Composition API)

   ## Prerequisites

   - Node.js (LTS recommended)
   - npm

   ## Quick Start

   1.  **Clone and Install**:
       ```bash
       npm install
       ```

   2.  **Environment Setup**: Create a `.env.local` file (see [Environment Variables](#environment-variables)).

   3.  **Run Development Server**:
       ```bash
       npm run dev
       ```

   Open the local URL printed by Vite (usually `http://localhost:5173`).

   ## Available Scripts

   - `npm run dev`: starts the development server.
   - `npm run build`: creates a production build in `dist/`.
   - `npm run preview`: serves the production build locally.

   ## Environment Variables

   This project reads API settings from Vite environment variables (`import.meta.env`).

   Vite uses different `.env` files based on the current mode:

   - `.env.development`: variables used during development (`npm run dev`).
   - `.env.production`: variables used for production builds (`npm run build`).
   - `.env.local`: can be used to override variables locally (should not be committed).

   ### Obtaining API and License Keys

   To run the application, you will need to obtain the following keys and add them to your `.env.local` file:

   1.  **NewsAPI API Key**:
       - Go to [NewsAPI.org Registration](https://newsapi.org/register).
       - Create an account and log in.
       - Copy your API Key from your dashboard.
       - Set it as `VITE_NEWS_API_KEY` in your `.env.local`.

   2.  **Logo.dev Publishable API Key**:
       - Visit [Logo.dev](https://logo.dev).
       - Sign up for an account to get your publishable key.
       - Set it as `VITE_LOGO_PUBLISHABLE_API_KEY` in your `.env.local`.

   3.  **Prime UI License Key**:
       - Visit [PrimeUI](https://primeui.dev).
       - Register for a Community or Commercial license.
       - Once obtained, set it as `VITE_PRIME_UI_LICENSE_KEY` in your `.env.local`.

   Create a local env file (for example `.env.local`) to provide your keys:

   ```bash
   VITE_NEWS_API_URL=https://newsapi.org/v2
   VITE_NEWS_API_KEY=your_news_api_key
   VITE_SOURCES_ENDPOINT_PATH=/sources
   VITE_TOP_HEADLINES_ENDPOINT_PATH=/top-headlines
   VITE_LOGO_API_URL=https://img.logo.dev
   VITE_LOGO_PUBLISHABLE_API_KEY=your_logo_dev_publishable_key
   VITE_PRIME_UI_LICENSE_KEY=your_prime_ui_license_key
   ```

   Notes:

   - Do not commit real API keys.
   - Use provider dashboards to rotate keys if they are exposed.

   ## Project Structure

   ```text
   src/
     news/
       application/      # store and use-case orchestration
       domain/model/     # entities (Article, Source)
       infrastructure/   # API clients and assemblers
       presentation/     # news-related UI components
     shared/
       domain/model/     # shared Value Objects (Url, DateTime, StringValidator)
       infrastructure/   # shared API helpers and interceptors
       presentation/     # shared layout/footer/language components
     locales/            # i18n dictionaries (en, es)
   docs/                 # architectural and requirement documentation
   ```

   ## Architecture

   The codebase follows **Domain-Driven Design (DDD)** principles and **Clean Architecture** to ensure maintainability and separation of concerns.

   - **Domain Layer**: Core business logic, Entities (`Article`, `Source`), and Value Objects (`Url`, `DateTime`).
   - **Application Layer**: Orchestrates domain logic and manages application state (`newsStore`).
   - **Infrastructure Layer**: Handles external communications, API clients (`NewsApi`), and data mapping (Assemblers).
   - **Presentation Layer**: Vue.js components and user interactions.

   For more details on architectural decisions, see [Architectural Decision Records (ADRs)](docs/adrs.md).

   ## Internationalization

   - **i18n setup**: `src/i18n.js`
   - **Dictionaries**: `src/locales/en.json`, `src/locales/es.json`

   ## Documentation & History

   This project maintains comprehensive documentation to bridge the gap between requirements and implementation:

   - **ADRs**: [Architectural Decision Records](docs/adrs.md)
   - **User Stories**: [Requirements and Traceability Matrix](docs/user-stories.md)
   - **C4 Model**: [docs/c4](docs/c4) (Context, Container, and two Component views).
   - **Design**: [Class Diagram (PlantUML)](docs/class-diagram.puml)
   - **Change Tracking**: [CHANGELOG.md](CHANGELOG.md)

   ## Attribution

   This app uses data and branding services from:

   - [NewsAPI.org](https://www.newsapi.org)
   - [Logo.dev](https://logo.dev)

   ## License

   MIT
   ````
   </details>

   ```
   git add .
   git commit -m "docs: update project-level documentation."
   ```

5. **Add `CONTRIBUTING.md`,** linked from the README.

   <details>
   <summary>CONTRIBUTING.md</summary>

   ````markdown
   # Contributing to CatchUp

   Thank you for your interest in contributing to **CatchUp**! This document outlines the standards and workflows we follow to maintain high code quality and architectural integrity.

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
   - **News Context**: The core business logic (`Article`, `Source` entities, `newsStore`, `NewsApi`, the assemblers, and every news-related component).
   - **Shared Kernel**: Cross-cutting value objects and utilities (`Url`, `DateTime`, `StringValidator`, the Logo.dev helper, the error interceptor).
   - **Layers**: a strict separation between **domain** (pure JavaScript, no Vue import), **application** (`newsStore`, orchestration), **infrastructure** (HTTP, mapping), and **presentation** (`.vue` components).

   ### Object-Oriented Programming (OOP)
   - **Encapsulation**: `Article` and `Source` use native `#` private fields, the same convention as `Url`, `DateTime`, and every other class in this project. `newsStore` keeps `sources`, `articles`, and `currentSource` in `shallowRef` and `errors` in a plain `ref`, so Vue never wraps an entity in a Proxy that would break a `#field` read (see `docs/adrs.md`, ADR-0003).
   - **Immutability**: every domain type is immutable by design: state lives in private fields, there are only getters, and everything is resolved before the constructor runs. (ADR-0004).
   - **Access**: always through getters; no raw setters anywhere in the domain layer.

   ### Vue 3.5 & Composition API
   - **`<script setup>`** for every component.
   - **PrimeVue** (`pv-` prefixed globally registered components) for every interactive UI element, no hand-rolled equivalents.
   - **Domain entities as props**: a component that needs an `Article` or a `Source` receives the entity itself, not its raw fields.

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

   **Scopes:** the file, class, or component touched, for example `news`, `shared`, `article`, `source`, `i18n`, `docs`.

   Example: `feat(news): add Article entity.`

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
   - File names: `lower-kebab-case`, with a role suffix for domain files (`.entity.js`).

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
   ```

---

## Release

**Still on `develop`.** Every feature plus the license, README, and contributing guide are merged. `main` shouldn't stay permanently behind `develop`: close the loop with a release.

1. **Start the release.** Git Flow Helper widget → `Release` → `Release Start` → **Version description** `v1.0.0` → `OK`. Creates and switches you to `release/v1.0.0`.

   **Note:** the release name carries a `v` prefix (`v1.0.0`), matching the git tag it becomes on finish. The `package.json` `"version"` stays plain (`1.0.0`), and so does the `CHANGELOG.md` heading (`## [1.0.0]`): npm and Keep a Changelog conventions don't use the prefix.

2. **Add the Architecture Decision Records** to a single `docs/adrs.md`. Right-click the `docs` folder → `New` → `File` → type `adrs.md` → Enter. Eight decisions, written in one sitting: the layered architecture, value objects for domain data, native `#` private fields with domain entities held in `shallowRef`, building an entity once and immutable by design, the Composition API plus PrimeVue choice, the assembler pattern, the centralized Axios error handling, and progressive `URL.canParse()` enhancement.

   <details>
   <summary>docs/adrs.md</summary>

   ````markdown
   # Architecture Decision Records

   # ADR-0001: Domain-Driven Design with Layered Clean Architecture

   **Status:** Accepted

   ## Context

   The application needs to be maintainable, scalable, and easy to understand. Business logic, application orchestration, and infrastructure details need a clear separation.

   ## Decision Drivers

   - Business rules (what makes a `Source`/`Article` valid) should live apart from the Vue components that present them.
   - Technical details (HTTP calls, response shapes) should never leak into the domain layer.

   ## Considered Options

   1. Domain-Driven Design with four layers: `domain`, `application`, `infrastructure`, `presentation` *(Chosen)*
   2. A single flat `src/components` and `src/services` split, no bounded-context boundary

   ## Decision

   The project is organized into a `news` bounded context and a `shared` kernel, each split into: **domain** (`Article`, `Source`, `Url`, `DateTime`, entities and value objects that represent the core business logic), **application** (`newsStore`, orchestrating the flow of data and use cases), **infrastructure** (`NewsApi`, `LogoDevApi`, the assemblers, technical details isolated from the domain), and **presentation** (the Vue components).

   ## Consequences

   **Positive:**
   - Strong separation of concerns, easier testing of business logic, and independence from external frameworks in the domain layer.

   **Negative:**
   - Increased initial complexity and boilerplate due to multiple layers and mapping (assemblers).

   ---

   # ADR-0002: Value Objects for Domain Data

   **Status:** Accepted

   ## Context

   Primitive strings were used for complex concepts like URLs and dates, leading to duplicated validation logic and the possibility of invalid states across the application.

   ## Decision Drivers

   - Validation for "is this a well-formed URL" or "is this a valid date" should live in exactly one place.
   - These concepts have no identity of their own; they are defined entirely by their value.

   ## Considered Options

   1. `Url` and `DateTime` value objects in the shared domain layer *(Chosen)*
   2. Raw strings, validated ad hoc wherever they are used

   ## Decision

   `Url` and `DateTime` are immutable by design (state in private fields, only getters), self-validating (a malformed value falls back to a safe default instead of leaving the object half-built), and behavior-rich (`isFuture()`, `format()`, `equals()`) instead of bare strings passed around and re-validated everywhere.

   ## Consequences

   **Positive:**
   - Centralized validation, improved type safety even in plain JavaScript, and more expressive domain code.

   **Negative:**
   - Requires wrapping and unwrapping values when interacting with APIs or templates (`new Url(...)`, `.toString()`).

   ---

   # ADR-0003: Native Private Fields (`#`) Everywhere, Domain Entities Held in `shallowRef`

   **Status:** Accepted

   ## Context

   Native `#field` gives real encapsulation, and it is the convention on every domain type in this course. Vue can wrap reactive objects in a `Proxy`, though. `reactive()` and `ref()` wrap in depth, so an entity stored in one of them becomes a `Proxy`, and a getter that reads `this.#field` through it throws `TypeError: Cannot read private member from an object whose class did not declare it`. JavaScript checks that the object used as `this` carries the private brand of the class that declared the field, and a `Proxy` is a different object, without that brand.

   A component that receives an entity as a prop cannot fix this either. If the parent already handed it over as a `Proxy`, the child keeps seeing the `Proxy`, so the entity has to enter Vue as a `shallowRef` from the start, not be repaired further down the tree.

   ## Decision Drivers

   - `#field` should not be traded away for a framework detail; `Article`/`Source` should follow the same rule as `Url` and `DateTime`.
   - `Article` and `Source` are immutable by design (ADR-0004): `newsStore` only ever needs to know when `sources`, `articles`, or `currentSource` are *replaced*, never when something inside an entity changes, because nothing does.
   - The rule has to be easy to teach and hold for every entity, however it is built.

   ## Considered Options

   1. `_field` convention on `Article`/`Source`, so a deep `reactive()` store never has to `Proxy`-wrap a private field (the original decision here)
   2. Native `#field` with the whole `newsStore` built on `shallowReactive()` (applied first, then dropped: it puts UI state like `errors` under shallow rules too, so `errors.push(...)` stops triggering updates)
   3. Native `#field`, domain entities in `shallowRef`, plain UI state in `ref`/`reactive` *(Chosen)*
   4. Native `#field` with `markRaw(entity)` at every place an entity is built (more invasive, easy to forget one)
   5. A plain ViewModel/DTO between the domain and Vue (very clean in a large application, but it adds a second model and mappings and drops domain behavior, for a course focused on entities, encapsulation, `#`, DDD, Vue, and stores)
   6. Plain public fields, no encapsulation at all

   ## Decision

   Vue can wrap reactive objects in a `Proxy`. Domain entities that use native private fields (`#`) must not be proxied. That is why we store them with `shallowRef` and update state by replacing references. Plain UI state (strings, message arrays, flags) stays in `ref`/`reactive`.

   - **UI state** (`ref`, `reactive`): deep reactivity, `Proxy`.
   - **Domain objects** (`shallowRef`): the real object, untouched.

   In `NewsStore`, `#sources`, `#articles`, and `#currentSource` are `shallowRef`, and `#errors` is a plain `ref`. The store is a class with private state: components read it through read-only getters (`newsStore.sources`, `newsStore.errors`, `newsStore.currentSourceArticles`) with no `.value`, and the exported instance is never wrapped in `reactive()`. Entities are updated by replacement, `this.#sources.value = [...]`, `this.#articles.value = {...this.#articles.value, [id]: list}`, `this.#currentSource.value = source`. Errors are just messages, so `this.#errors.value.push(message)` works as usual.

   ## Consequences

   **Positive:**
   - Real encapsulation: `someArticle.#title` is a syntax error outside the class, not just a convention nobody happens to break.
   - One privacy convention across the whole project, `#` everywhere, and one rule for Vue: entities in `shallowRef`, UI state in `ref`/`reactive`.
   - The rule is applied once, in the container, and never has to be repaired further down: it holds for every entity, however it is built.

   **Negative:**
   - An entity inside a `shallowRef` must be replaced, never mutated in place (`.push`, `.splice`, index assignment): Vue would not notice. Not a new constraint here, since entities are immutable and the store already replaces whole arrays.

   ---

   # ADR-0004: `Article` and `Source` Are Built Once and Immutable by Design

   **Status:** Accepted

   ## Context

   The original design set `source.urlToLogo` and `article.source` as plain property assignments performed by the assemblers *after* constructing the entity, since the logo URL and the resolved source are only known once an asynchronous lookup or a cross-reference finishes.

   ## Decision Drivers

   - An entity that can still be changed after construction is harder to reason about than one that is built once and never mutated again.
   - Every other domain type in this project (`Url`, `DateTime`) is already immutable; `Article` and `Source` were the exception.

   ## Considered Options

   1. Resolve `urlToLogo` and the cross-referenced `source` *before* constructing the entity, pass them in through the constructor, keep every field private with getters only *(Chosen)*
   2. Keep constructing a partial entity first, then assign the remaining fields from the assembler afterward

   ## Decision

   `SourceAssembler` resolves the logo URL from the raw `url` string before ever calling `new Source(...)`, passing `urlToLogo` in with the rest of the properties. `ArticleAssembler` resolves the matching `Source` before calling `new Article(...)`, passing it in as `source`. Both entities are immutable by design: state lives in `#` private fields, there are only getters, no operation changes it, and everything is resolved before the constructor runs.

   ## Consequences

   **Positive:**
   - `Article` and `Source` are true value-like entities once constructed: what you get back from the assembler is exactly what will exist for the rest of that object's life.

   **Negative:**
   - The assembler does slightly more work up front (resolving the logo URL, or the matching source) before it can call the constructor, instead of patching the entity afterward.
   - Nothing in the runtime stops outside code from adding loose properties to an entity: the guarantee rests on the design (no operation that modifies state) and on code review, not on a runtime check.

   ---

   # ADR-0005: Presentation Layer with Vue 3 Composition API and PrimeVue

   **Status:** Accepted

   ## Context

   The project needs a modern, reactive, component-based UI framework with a robust set of UI components to speed up development.

   ## Decision Drivers

   - Logic reuse and IDE support matter more than API familiarity for a course project.
   - A rich, accessible component library saves time over hand-rolling a drawer, a popover, and a card from scratch.

   ## Considered Options

   1. Vue 3 Composition API (`<script setup>`) with PrimeVue as the UI component library *(Chosen)*
   2. Vue 3 Options API
   3. A headless/unstyled component library, hand-styled

   ## Decision

   Every component uses `<script setup>`. PrimeVue supplies the drawer (`pv-drawer`), the card (`pv-card`), the avatar (`pv-avatar`), the popover (`pv-popover`), and the rest of the interactive UI.

   ## Consequences

   **Positive:**
   - High productivity, modern reactive patterns, and a rich set of pre-built, accessible UI components.

   **Negative:**
   - Dependency on the PrimeVue ecosystem and its own component API conventions.

   ---

   # ADR-0006: Data Mapping via Assemblers

   **Status:** Accepted

   ## Context

   Data returned from external APIs (NewsAPI, Logo.dev) does not always match the internal domain model. Infrastructure details should not leak into the domain or application layers.

   ## Decision Drivers

   - A change in the external API's response shape should only ever require a change in one place.
   - The domain entities should never need to know what an "API resource" looks like.

   ## Considered Options

   1. Data Mapper pattern via `ArticleAssembler`/`SourceAssembler` classes *(Chosen)*
   2. Construct domain entities directly from the raw HTTP response, wherever the response is received

   ## Decision

   `ArticleAssembler` and `SourceAssembler` are responsible for turning a raw API response into domain entities, resolving cross-references (a matching `Source` for an `Article`) and derived values (a `Source`'s logo URL) along the way.

   ## Consequences

   **Positive:**
   - The domain stays decoupled from the external API's schema. A change in NewsAPI's response shape only touches the assembler.

   **Negative:**
   - Requires an assembler class and a mapping method for every API resource.

   ---

   # ADR-0007: Centralized Technical Error Handling via an Axios Interceptor

   **Status:** Accepted

   ## Context

   Technical errors (401 Unauthorized, 404 Not Found, network failures) should be handled consistently across every API call, without duplicating the same `try`/`catch` in every method that calls `axios`.

   ## Decision Drivers

   - The application layer (`newsStore`) should receive one consistent kind of error, not a different shape depending on which layer of the HTTP stack failed.
   - Logging the technical detail (status, headers) should happen in one place.

   ## Considered Options

   1. A shared `errorInterceptor` registered on the Axios instance's response interceptor *(Chosen)*
   2. A `try`/`catch` block around every individual API call

   ## Decision

   `errorInterceptor` is registered once, on the `NewsApi`'s Axios instance. It logs the technical detail and rejects with a single, user-facing message string, which is what `newsStore` actually catches and stores in `errors`.

   ## Consequences

   **Positive:**
   - Consistent error handling, no repeated boilerplate in the API client, and centralized logging.

   **Negative:**
   - A single generic handler is not the right fit for a request that needs a very specific, custom error response, none of the current requests need that yet.

   ---

   # ADR-0008: Progressive Enhancement for URL Validation

   **Status:** Accepted

   ## Context

   Modern browsers support `URL.canParse()`, a cheap way to check if a string is a well-formed URL without constructing a full `URL` object. Older environments do not have it.

   ## Decision Drivers

   - The check should use the fast, built-in method when it exists, without breaking on a browser that lacks it.

   ## Considered Options

   1. `Url.isValidUrl` uses `URL.canParse()` when available, falling back to a `new URL()` inside a `try`/`catch` otherwise *(Chosen)*
   2. Always use `new URL()` inside a `try`/`catch`, ignore `URL.canParse()`

   ## Decision

   `Url.isValidUrl(value)` checks for `URL.canParse` first; if present, it calls it directly. Otherwise, it falls back to constructing a `new URL(value)` inside a `try`/`catch`, returning `true` only if construction succeeds.

   ## Consequences

   **Positive:**
   - Uses the modern, allocation-free API where available, while staying correct everywhere else.

   **Negative:**
   - Two code paths to keep in sync, though both express exactly the same rule.
   ````
   </details>

   ```
   git add .
   git commit -m "docs: add architecture decision records."
   ```

3. **Bump the version.** A release branch needs at least one commit of its own, or the merge into `develop` is a no-op. Open `package.json`, change `"version": "0.0.1"` to `"version": "1.0.0"`.

   ```
   git add .
   git commit -m "chore(release): bump version to 1.0.0."
   ```

4. **Add `CHANGELOG.md`.** Right-click the project root → `New` → `File` → type `CHANGELOG.md` → Enter.

   <details>
   <summary>CHANGELOG.md</summary>

   ```markdown
   # Changelog

   All notable changes to this project will be documented in this file.

   The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/),
   and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

   ## [1.0.0] - 2026-09-12

   ### Added
   - `news` bounded context (`Article` and `Source` entities) and a `shared` kernel (`Url` and `DateTime` value objects, `StringValidator`).
   - `newsStore`: an application service with private state that loads sources, selects the active one, and loads its articles.
   - `SourceList`/`SourceItem` components: browse available news sources, the first one selected by default (US001).
   - `ArticleList`/`ArticleItem` components: read articles for the active source, with author, publish date, and a placeholder image when one is missing (US002).
   - `LanguageSwitcher`/`FooterContent` components: switch between English and Spanish, and attribution for NewsAPI.org and Logo.dev (US003).
   - Sharing an article through the Web Share API, falling back to copying its URL; `SourceSummary` popover with the source's full details (US004).
   - `docs/user-stories.md` with a Requirement Traceability Matrix, `docs/c4`, `docs/class-diagram.puml`, `docs/adrs.md`.
   - `README.md`, MIT `LICENSE.md`, `CONTRIBUTING.md`.

   ### Design notes
   - `Article` and `Source` are built once, with every field resolved before construction, immutable by design; internal fields use native `#` private fields, same as every other class in this project, `newsStore` holds them in `shallowRef` precisely so Vue never wraps them (ADR-0003).
   - `Url` and `DateTime` are immutable value objects with native `#` private fields, since they are never themselves the direct value of a reactive property.
   ```
   </details>

   ```
   git add .
   git commit -m "docs: add changelog for 1.0.0."
   ```

   **Note:** don't push here. This commit rides to the remote with `Release Publish` in the next step, together with the version bump; it's also what gives `Release Finish` a real commit to merge into `develop`.

   **Note:** the `## [version] - date` line uses the date you finish the release, `YYYY-MM-DD`.

5. **Publish and finish the release.**
   - Git Flow Helper widget → `Release` → `Release Publish` (pushes `release/v1.0.0` with both commits).
   - Git Flow Helper widget → `Release` → `Release Finish`.

   `Release Finish` merges `release/v1.0.0` into `main` (tagging it `v1.0.0`), merges it into `develop`, pushes both, and deletes the release branch. `main` and `develop` are back in sync.

6. **Publish the GitHub Release.**
   - On GitHub: **Releases** → **Draft a new release**.
   - Tag: pick the existing `v1.0.0` (do not create a new one).
   - Release title: `Version 1.0.0` (the title spells it out; the tag keeps the `v` prefix).
   - Description: the release notes below.
   - Release label: leave the default, **None**, selected, don't pick **Pre-release**.
   - Click **Publish release**.

   <details>
   <summary>Release notes (1.0.0)</summary>

   ```markdown
   ## 🚀 Added

   - **US001:** browse available news sources, fetched live from NewsAPI, the first one active by default.
   - **US002:** read articles for the active source, with author, a formatted publish date, and a placeholder image when one is missing.
   - **US003:** switch between English and Spanish, everywhere, including article text; attribution for NewsAPI.org and Logo.dev in the footer; a fallback view when a request fails.
   - **US004:** share an article through the Web Share API (or copy its URL); a popover with a source's full details.
   - `Article` and `Source` entities: built once, every field resolved before construction, immutable by design, native `#` private fields; `newsStore` holds them in `shallowRef` so Vue never wraps them in a `Proxy`.
   - `Url` and `DateTime` value objects, immutable, native `#` private fields.
   - `README.md`, MIT `LICENSE.md`, `CONTRIBUTING.md`, `CHANGELOG.md`; `docs/user-stories.md` with a Requirement Traceability Matrix, `docs/c4`, `docs/class-diagram.puml`, `docs/adrs.md`.
   ```
   </details>

7. **Back on `develop`, move to the next development version.**
   - In `package.json`: `"version": "1.0.0"` → `"version": "1.0.1"`, so `develop` doesn't sit on an already-tagged version. The next change decides whether it becomes `1.0.1` (a fix) or `1.1.0` (a feature).
   - Then:

   ```
   git add .
   git commit -m "chore(dev): set development version to 1.0.1."
   git push
   ```

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
     gh repo clone <org>/catch-up
     ```

   - Open the `catch-up` folder in WebStorm afterward (`File` → `Open`). Or, from the JetBrains Welcome screen, `Clone Repository`, paste `https://github.com/<org>/catch-up.git`, pick a folder, `Clone`.

2. **Restore the dependencies.** A clone has no `node_modules/`:

   ```
   npm install
   ```

3. **Fill in your own keys.** The clone already has `.env.development` and `.env.production` with the demo values from Project Setup step 13. Replace the three demo values in each with your own NewsAPI, Logo.dev, and PrimeVue keys (Project Setup steps 10-12), they are not something `npm install` restores.

4. **Reinstall the tools that live outside the repo.** Plugins live in the IDE, not the repo: reinstall the Git Flow Helper plugin (Project Setup step 22) if this machine does not have it.

5. **Reinstate Git Flow.**
   - Check out `develop` before anything else: a fresh clone only has `main` as a local branch. Do it before `Init`, so Git Flow Helper registers against the existing `develop` instead of creating a new one.

     ```
     git checkout develop
     ```

   - Register your GitHub account in the IDE (Project Setup step 21): get the token with

     ```
     gh auth token
     ```

     then in `Settings` → `Version Control` → `GitHub`, remove any account already listed, then `+` → `Log In with Token...` → paste the token.
   - Run Git Flow `Init` from the widget and accept the defaults; the Git Flow settings live in the repo's local git config, which a clone does not copy.

6. **Get onto your feature branch,** only if you stopped partway through one:

   ```
   git checkout feature/<name>
   git pull
   ```

   **Note:** seeing only `main` locally right after a clone is normal. Every remote branch was downloaded; the checkouts above are what turn them into local branches.

### Signing in to GitHub with a token

The guide uses `gh auth login` (Project Setup step 21), which is the simplest way. If you can't install `gh`, GitHub also accepts a Personal Access Token.

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
   - Click **Generate token**, then copy it somewhere safe (a password manager) before navigating away. GitHub shows it **only once**. The IDE GitHub account (Project Setup step 21) needs it too.
2. Back in the terminal where the push is waiting:
   - **macOS:** type your GitHub username, then paste the token as the password (nothing shows as you paste, that's normal).
   - **Windows:** in the "Connect to GitHub" window, pick the `Token` tab and paste it there.
3. It is cached after this, so it won't ask again for the rest of the project.

**Note:** use `(classic)`, not "Fine-grained tokens": fine-grained tokens need the organization owner to approve them first, which can leave you waiting. With `repo` scope the token reaches every repo your account can, so reuse it across the other course projects.

### Creating the repo without the GitHub CLI

No `gh`? Do the whole thing through the GitHub website plus plain `git`.

1. Authenticate git first, since `gh auth login` isn't available: follow [Signing in to GitHub with a token](#signing-in-to-github-with-a-token).
2. On GitHub, inside your organization, create an empty **private** repo named `catch-up`, with no README, license, or `.gitignore` (this project already has all three).
3. On the repo's "Quick setup" page, copy the **HTTPS** clone URL, the one ending in `.git` (`https://github.com/<org>/catch-up.git`, `<org>` is your organization's name), not the address-bar URL.
4. From the project root, add the remote and push:

   ```
   git remote add origin https://github.com/<org>/catch-up.git
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

Then initialize from the project root as step 4 describes:

```
git init -b main
```

### Fixing file or folder permissions

On a shared lab machine, `npm install` or the project folder itself can end up owned by a different account. The symptoms:

- Files created outside the IDE (from the terminal) don't show up in WebStorm's **Project** tool window, or the IDE can't save over them.
- `npm install` fails with `EACCES`, or only works with `sudo` (which then makes the problem worse, because the files it writes are owned by `root`).

Fix the ownership of the whole project tree in one go, `$(id -gn)` resolves to the logged-in account's own primary group, one command works on the lab Macs and on your own Mac alike:

```
sudo chown -R "$(whoami):$(id -gn)" {CHANGE_WITH_YOUR_PATH}
```

For example, if the project is in `~/Documents/wa-projects/catch-up`:

```
sudo chown -R "$(whoami):$(id -gn)" ~/Documents/wa-projects/catch-up
```

If `npm` itself has been run with `sudo` before, its cache is root-owned too:

```
sudo chown -R "$(whoami):$(id -gn)" ~/.npm
```

**If `npm install` still fails with `EACCES` after that**, the cache isn't actually at `~/.npm`, check where it really lives (`npm config get cache`), delete whatever path that prints, and let npm rebuild it from scratch under the right owner:

```
sudo rm -rf ~/.npm
```

No `sudo` on the `npm install` that follows, letting the folder not exist is what makes npm recreate it correctly.

Then delete `node_modules` and reinstall **without** `sudo`:

```
rm -rf node_modules
npm install
```

On Windows this rarely happens; if a file is read-only, right-click it → `Properties` → uncheck `Read-only`. The lasting fix on any machine is to keep Node installed through a per-user version manager (`nvm`, `fnm`, or `volta`) so `npm install` never needs elevated rights. Don't keep adding `sudo`.

### If a PlantUML diagram doesn't render

The plantuml4idea plugin needs a local Java runtime and Graphviz to render some diagrams. If `docs/class-diagram.puml` shows an error instead of a diagram, install a JDK and Graphviz, then restart WebStorm.

On macOS, check Homebrew itself is installed first:

```
brew --version
```

No output, or `command not found: brew`? Install it:

```
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

The installer prints one or two `echo` commands near the end, under "Next steps", that add Homebrew to your `PATH`, they differ by chip (Apple Silicon vs Intel) and shell. Run exactly the ones it shows you, then close the terminal and open a new one. Confirm it worked:

```
brew --version
```

Now install Graphviz:

```
brew install graphviz
```

On Windows, download and run the installer from [graphviz.org](https://graphviz.org/download/).

The five diagrams under `docs/c4/` have an extra requirement: each starts with `!includeurl`, which downloads the C4-PlantUML macro definitions from GitHub the first time it renders. If one of them errors out specifically on the `!includeurl` line, check the machine has internet access and isn't behind a proxy or firewall blocking `raw.githubusercontent.com`.

### Free JetBrains license for students

WebStorm is free for students through the [JetBrains Student Pack](https://www.jetbrains.com/community/education/#students): apply with a school email address, or upload proof of enrollment if your school email isn't recognized. Approval usually takes a few minutes. The license covers the whole JetBrains suite and renews each year you're enrolled.

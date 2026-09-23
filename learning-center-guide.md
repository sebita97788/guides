# Learning Center Guide

## Table of Contents

- [Project Setup](#project-setup)
- [(US005) Switch the Application Language](#switch-the-application-language-us005)
- [(US006) Navigate the Application](#navigate-the-application-us006)
- [(US001) Manage Categories](#manage-categories-us001)
- [(US002) Manage Tutorials](#manage-tutorials-us002)
- [(US003) Register a New Account](#register-a-new-account-us003)
- [(US004) Sign In and Manage the Session](#sign-in-and-manage-the-session-us004)
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
   npm create vite@latest learning-center -- --template vue
   ```

   This is Vite's own scaffolding tool; `--template vue` skips the interactive framework/variant prompts (plain JavaScript, not TypeScript). It still asks one question:

   ```
   Install with npm and start now? … yes / no
   ```

   Answer **No**, `npm install` runs as its own explicit step next. Then:

   ```
   cd learning-center
   ```

   ```
   npm install
   ```

   Unlike some scaffolding tools, `npm create vite` does **not** initialize git for you, that stays an explicit step, in step 4 below.

   Then open it in the editor:

   ```
   webstorm .
   ```

   No `webstorm` command? Open WebStorm and use `File` → `Open` to pick the `learning-center` folder you just created.

   **Option B, from WebStorm.** `File` → `New Project`. In the left list under **Generators**, pick **Vite** (it scaffolds through the same `create-vite` template as Option A, framework-agnostic, so the wizard asks for the framework itself).
   - **Location:** `~/Documents/wa-projects/learning-center` (WebStorm creates the `wa-projects` folder too if it does not exist yet; the last path segment becomes the project name).
   - **Node runtime:** leave at its detected default.
   - The **Vite** dropdown: leave it at its default value, `npx create-vite`.
   - **Template:** pick `Vue` from the dropdown.
   - **Make sure "Use TypeScript template" is unchecked**, this project is plain JavaScript.
   - **Create**, then run `npm install` in the WebStorm terminal if the wizard did not do it for you. WebStorm opens the project automatically, nothing else to do here.

   **Note:** on a shared lab Mac, either option can fail with a permissions error, because a previous account owns files under your home folder or the new project folder. Take ownership, then re-run the failed command, `$(whoami)`/`$(id -gn)` resolve to whoever is actually logged in and their primary group, the same command works whether that's the shared lab account or your own:

   ```
   sudo chown -R "$(whoami):$(id -gn)" ~/Documents/wa-projects/learning-center
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
   - Add `"description"`, `"author"`, `"license"`, and `"keywords"` right after `"version"`:

     ```json
     "description": "ACME Learning Center is a web platform for learning and development. It illustrates Domain-Driven Design (DDD) principles and is built with Vue, Axios, Vite, Pinia and PrimeVue, in JavaScript.",
     "author": "Web Applications Development Team",
     "license": "MIT",
     "keywords": [
       "vue",
       "vite",
       "pinia",
       "primevue",
       "axios",
       "json-server",
       "domain-driven-design",
       "learning-center"
     ],
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

     **Note:** if you used `Refactor` → `Rename` in the first bullet, WebStorm already rewrote this import for you, the file already matches the block below, there is nothing to change. If you renamed the file outside the IDE instead (Finder, Explorer, a plain `mv`), the import still reads `./App.vue`, edit it by hand to match, or `npm run dev` fails to find the file.

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

   - Open `src/style.css` and replace its whole content. The scaffold's stylesheet centers `#app` in a fixed column with side borders and centered text, which would squeeze the layout this project builds; this one resets the box model and lets the page fill the window:

     <details>
     <summary>src/style.css</summary>

     ```css
     :root {
       font-family: system-ui, -apple-system, BlinkMacSystemFont, "Segoe UI", sans-serif;
       line-height: 1.5;
       font-weight: 400;
       color: #1f2937;
       background-color: #ffffff;
       font-synthesis: none;
       text-rendering: optimizeLegibility;
       -webkit-font-smoothing: antialiased;
       -moz-osx-font-smoothing: grayscale;
     }

     * {
       box-sizing: border-box;
     }

     html,
     body,
     #app {
       min-height: 100%;
       height: 100%;
     }

     body {
       margin: 0;
       min-width: 320px;
     }

     button,
     input,
     textarea,
     select {
       font: inherit;
     }

     a {
       color: inherit;
       text-decoration: none;
     }

     img,
     svg {
       display: block;
       max-width: 100%;
     }

     #app {
       width: 100%;
     }
     ```
     </details>

   - Open `index.html` and give the page its real title, the scaffold's is `learning-center`:

     <details>
     <summary>index.html (so far)</summary>

     ```html
     <!doctype html>
     <html lang="en">
       <head>
         <meta charset="UTF-8" />
         <link rel="icon" type="image/svg+xml" href="/favicon.svg" />
         <meta name="viewport" content="width=device-width, initial-scale=1.0" />
         <title>ACME Learning Center</title>
       </head>
       <body>
         <div id="app"></div>
         <script type="module" src="/src/main.js"></script>
       </body>
     </html>
     ```
     </details>

   - Add the ACME logo. Right-click `public` → `New` → `File` → type `acme-logo.svg` → Enter, then paste in the content below.

     **Tip:** instead of retyping the SVG markup, download the file itself from this guide's own repo, [assets/acme-logo.svg](assets/acme-logo.svg) (a different repo than your project), and drop it straight into your project's `public` folder.

     <details>
     <summary>public/acme-logo.svg</summary>

     ```xml
     <svg xmlns="http://www.w3.org/2000/svg" width="2500" height="2500" viewBox="0 0 192.756 192.756"><g fill-rule="evenodd" clip-rule="evenodd"><path fill="#fff" d="M0 0h192.756v192.756H0V0z"/><path d="M23.454 68.785l-20.62 55.488h15.768l2.729-7.883H33.46v7.883h15.464V68.785h-25.47zm9.703 31.535h-6.368l6.368-13.342v13.342zM94.71 68.178H70.149c-8.188 0-10.916 10.613-10.916 10.613l-5.761 33.051s-2.123 12.432 8.49 12.432h23.045l2.426-16.373H75.001s-5.458 1.213-4.548-4.852c.303-.91 2.426-13.039 2.426-13.039s.606-4.245 3.335-4.245h15.464l3.032-17.587zM101.988 68.482l-11.523 55.791h15.162l5.154-23.65 1.213 23.65h8.188l9.095-23.65-4.548 23.65h13.644l11.221-56.095h-19.406l-9.401 23.348V68.482h-18.799zM145.955 124.273l10.611-56.095h31.233l-2.426 16.072H169l-.91 5.457h15.767l-2.425 12.129-16.071-.303-.91 5.457h16.07l-3.335 17.283h-31.231zM185.07 123.365c-2.123 0-3.336-1.518-3.336-3.639 0-2.123 1.213-3.639 3.336-3.639s3.639 1.516 3.639 3.639c0 2.122-1.516 3.639-3.639 3.639zm0 1.213c2.729 0 4.852-2.426 4.852-4.852 0-2.729-2.123-4.852-4.852-4.852s-4.852 2.123-4.852 4.852c.001 2.426 2.124 4.852 4.852 4.852zm1.213-4.549c.91 0 1.516-.303 1.516-1.516s-.91-1.516-2.123-1.516h-2.426v5.154h.91v-2.123h.91l1.213 2.123h1.213l-1.213-2.122zm-2.123-.607v-1.516h1.213c.607 0 1.213 0 1.213.607 0 .605-.303.908-.91.908h-1.516v.001z" fill="#cd5241"/></g></svg>
     ```
     </details>

   - Point `index.html`'s icon at the logo instead of the scaffold's `favicon.svg`:

     ```html
     <link rel="icon" type="image/svg+xml" href="/acme-logo.svg" />
     ```

     <details>
     <summary>index.html</summary>

     ```html
     <!doctype html>
     <html lang="en">
       <head>
         <meta charset="UTF-8" />
         <link rel="icon" type="image/svg+xml" href="/acme-logo.svg" />
         <meta name="viewport" content="width=device-width, initial-scale=1.0" />
         <title>ACME Learning Center</title>
       </head>
       <body>
         <div id="app"></div>
         <script type="module" src="/src/main.js"></script>
       </body>
     </html>
     ```
     </details>

   - The scaffold's two icon files, `public/favicon.svg` and `public/icons.svg`, are no longer referenced by anything, delete both.

   ```
   git add .
   git commit -m "chore: replace the wizard's starter page with an empty shell, a plain stylesheet, a real title, and the ACME logo."
   ```

7. **Add PrimeVue.** This project's UI components (`pv-toolbar`, `pv-button`, `pv-data-table`, and the rest) come from it, not from hand-rolled markup.

   ```
   npm install primevue @primeuix/themes primeicons primeflex
   ```

   **Note:** `primevue` is the component library itself; `@primeuix/themes` is its theming engine (this project uses the `Material` preset); `primeicons` and `primeflex` are its icon font and CSS utility classes, both used throughout the templates below (`pi pi-bars`, `flex`, `gap-3`, and so on).

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

   ```
   git add .
   git commit -m "chore: add axios dependency."
   ```

10. **Add Pinia.** The state library the application stores are built on: one store per bounded context holds what the views show.

    ```
    npm install pinia
    ```

    ```
    git add .
    git commit -m "chore: add Pinia dependency."
    ```

11. **Add Vue Router.** It maps every address the user types or clicks to the view that must render, and it is where the application protects routes later.

    ```
    npm install vue-router
    ```

    ```
    git add .
    git commit -m "chore: add Vue Router dependency."
    ```

12. **Add json-server.** Learning Center reads and writes categories, tutorials, and user accounts through a REST API, and no backend exists yet to point at. During development a fake one stands in: `json-server` turns a JSON file into a working REST API. It is a development tool, so it goes into `devDependencies`.

    ```
    npm install --save-dev json-server@0.17.4
    ```

    **Note:** `npm` saves the version with a caret, `^0.17.4`, which keeps you on the `0.17` line of `json-server`. This guide's routes file follows the `0.17` format, newer major versions changed it.

    **Note:** six separate installs (PrimeVue, vue-i18n, axios, Pinia, Vue Router, json-server), not one combined command. Each one is its own concern, and if any single install fails (a flaky network on a lab machine, for instance), you know exactly which dependency to retry, not which one of several to suspect.

    ```
    git add .
    git commit -m "chore: add json-server dependency."
    ```

13. **Check the toolchain runs.** In the WebStorm terminal (`View` → `Tool Windows` → `Terminal`):

    ```
    npm run dev
    ```

    Open the local URL Vite prints (`http://localhost:5173/` by default): the page is blank, because `app.vue` is an empty shell, and neither the browser console nor the terminal shows an error. Stop the server with `Ctrl+C`.

    **Note:** no commit here, nothing changed, this step only verifies what the last steps already produced.

14. **Get a PrimeVue Community license key.** PrimeVue 22 and up needs a license key even for free use, the library paints a banner over the whole app without one.
    - Go to [primeui.dev/licenses/community](https://primeui.dev/licenses/community) and confirm you're eligible (the free Community license covers individuals, students, non-profits, and small organizations under specific revenue/headcount thresholds listed on that page).
    - Registration is self-service, based on your own confirmation of eligibility, no manual approval step. Copy the license key it issues you.

    **Note:** a Community key needs renewing once a year to reconfirm eligibility, with a 30-day grace period after it expires. Verification happens offline, the library never phones home to check it.

15. **Add the environment variables.** This project reads the address and the paths of the Learning Center API, and PrimeVue's own license check, from environment files. Right-click the project root → `New` → `File` → type `.env.development` → Enter. Its API address is the `json-server` fake API this setup finishes creating below.

    <details>
    <summary>.env.development</summary>

    ```
    VITE_LEARNING_PLATFORM_API_URL="http://localhost:3000/api/v1"
    VITE_CATEGORIES_ENDPOINT_PATH="/categories"
    VITE_TUTORIALS_ENDPOINT_PATH="/tutorials"
    VITE_SIGNUP_ENDPOINT_PATH="/authentication/sign-up"
    VITE_SIGNIN_ENDPOINT_PATH="/authentication/sign-in"
    VITE_USERS_ENDPOINT_PATH="/users"
    VITE_PRIME_UI_LICENSE_KEY="eyJpZCI6ImJhYTExYWZlLTdlM2MtNGY3Mi04YmQ1LTBiZTk2MDU5YTMzNiIsInByb2R1Y3QiOiJwcmltZXVpIiwidGllciI6ImNvbW11bml0eSIsInR5cGUiOiJkZXYiLCJpYXQiOjE3ODg4MzgwOTUsImV4cCI6MTgyMDM3NDA5NX0.O5bvWswetPPP514VSmc_wnJgtXhW3_iLiUKiYJDLC0SmpuxLLGvfHhxf9_qTJbyQcjpWHEEAmM__iIGl_HigAQ"
    ```
    </details>

    Do the same for `.env.production`. Its API address points at a public mock of the platform instead, the one a deployed build would talk to, and it needs the same license key.

    <details>
    <summary>.env.production</summary>

    ```
    VITE_LEARNING_PLATFORM_API_URL="https://lc2025201asi0730sandbox.free.beeceptor.com/api/v1"
    VITE_CATEGORIES_ENDPOINT_PATH="/categories"
    VITE_TUTORIALS_ENDPOINT_PATH="/tutorials"
    VITE_SIGNUP_ENDPOINT_PATH="/authentication/sign-up"
    VITE_SIGNIN_ENDPOINT_PATH="/authentication/sign-in"
    VITE_USERS_ENDPOINT_PATH="/users"
    VITE_PRIME_UI_LICENSE_KEY="eyJpZCI6ImJhYTExYWZlLTdlM2MtNGY3Mi04YmQ1LTBiZTk2MDU5YTMzNiIsInByb2R1Y3QiOiJwcmltZXVpIiwidGllciI6ImNvbW11bml0eSIsInR5cGUiOiJkZXYiLCJpYXQiOjE3ODg4MzgwOTUsImV4cCI6MTgyMDM3NDA5NX0.O5bvWswetPPP514VSmc_wnJgtXhW3_iLiUKiYJDLC0SmpuxLLGvfHhxf9_qTJbyQcjpWHEEAmM__iIGl_HigAQ"
    ```
    </details>

    The license key above is a disposable demo key, shown so you see the exact shape PrimeVue issues (a signed JWT), not something to keep using. Replace it with the key from your own account, from step 14.

    **Note:** `.env.development` and `.env.production` hold a working key here because this is a teaching project on a scaffold Vite already ignores real secrets from (`*.local` in `.gitignore` covers `.env.local`, the file meant for a key you do not want committed at all). A real production app would keep every key out of source control; treat these two files the same way you would treat any other credential, once you swap in your own key, do not paste a key you were not personally issued into a repository other people can see.

    ```
    git add .
    git commit -m "chore: add environment variable files."
    ```

16. **Declare the environment variable types.** Right-click `src` → `New` → `File` → type `vite-env.d.ts` → Enter. Vite exposes the values from `.env.development` and `.env.production` through `import.meta.env`, and this declaration file tells the editor which `VITE_*` variables exist and that each one is a `string`, so `import.meta.env.VITE_CATEGORIES_ENDPOINT_PATH` autocompletes and a typo gets flagged. It changes nothing at runtime.

    <details>
    <summary>src/vite-env.d.ts</summary>

    ```typescript
    /// <reference types="vite/client" />
    interface ImportMetaEnv {
      readonly VITE_LEARNING_PLATFORM_API_URL: string;
      readonly VITE_CATEGORIES_ENDPOINT_PATH: string;
      readonly VITE_TUTORIALS_ENDPOINT_PATH: string;
      readonly VITE_SIGNUP_ENDPOINT_PATH: string;
      readonly VITE_SIGNIN_ENDPOINT_PATH: string;
      readonly VITE_USERS_ENDPOINT_PATH: string;
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

17. **Create `docs/user-stories.md`.** Right-click the project root → `New` → `File` → type `docs/user-stories.md` → Enter.

    **Tip:** typing the `docs/` prefix creates that folder too.

    <details>
    <summary>docs/user-stories.md</summary>

    ```markdown
    # Learning Center Application User Stories

    ## Overview
    This document presents the functional requirement user stories for the ACME Learning Center application. The requirements are organized around core business domains: **Identity and Access Management (IAM)**, **Publishing Management**, and **Shared Capabilities**.

    Roles involved:
    - **Learning Manager**: Manages tutorial categories and taxonomic structures.
    - **Tutorial Author**: Manages tutorials, their summaries, and their category associations.
    - **Registered User**: Authenticated user accessing the system's publishing resources.
    - **Visitor / Guest**: Unauthenticated user accessing public information.

    ---

    ## Requirement Traceability Matrix (RTM)

    | User Story ID | Title                                     | Bounded Context | Related Implementation Elements                                                                                                                                                                |
    |---------------|-------------------------------------------|-----------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | **US001**     | Manage Categories for Tutorial Organization | Publishing    | `Category`, `CategoryAssembler`, `PublishingApi`, `usePublishingStore`, `category-list`, `category-form`                                                                                       |
    | **US002**     | Manage Tutorials and Classifications      | Publishing      | `Tutorial`, `TutorialAssembler`, `PublishingApi`, `usePublishingStore`, `tutorial-list`, `tutorial-form`                                                                                       |
    | **US003**     | Register New User Account                 | IAM             | `SignUpCommand`, `SignUpAssembler`, `SignUpResource`, `IamApi`, `useIamStore`, `sign-up-form`                                                                                                  |
    | **US004**     | Authenticate and Session Management       | IAM             | `SignInCommand`, `User`, `SignInAssembler`, `SignInResource`, `UserAssembler`, `IamApi`, `useIamStore`, `sign-in-form`, `authentication-section`, `authenticationGuard`, `iamInterceptor`      |
    | **US005**     | Switch Application Language               | Shared          | `language-switcher`, `i18n.js`, `src/locales/en.json`, `src/locales/es.json`                                                                                                                   |
    | **US006**     | Navigation and System Accessibility       | Shared          | `layout`, `footer-content`, `home`, `about`, `page-not-found`, `router.js`, `app.vue`                                                                                                          |
    | **US007**     | Graceful Error Handling and User Feedback | Shared          | `usePublishingStore` (error state), `useIamStore` (error state), `category-list`, `category-form`, `tutorial-list`, `tutorial-form` (error messages and required category) |

    ---

    ## US001: Category Management
    **Title:** Manage Categories for Tutorial Organization  
    **Context:** Publishing  
    **Description:**  
    _As a Learning Manager, I want to create, view, update, and delete tutorial categories so that tutorials are logically structured and easily discoverable._

    **Acceptance Criteria:**
    - **AC1.1 – View Categories:** Given existing tutorial categories in the system, when the Learning Manager views the categories list, then all available categories are displayed with their identifiers and names.
    - **AC1.2 - Create Category:** Given a valid category name, when the Learning Manager submits a new category, then the system saves the category and makes it available for tutorial categorization.
    - **AC1.3 - Edit Category:** Given an existing category, when the Learning Manager updates the category name with valid data, then the system updates the category information across the system.
    - **AC1.4 - Delete Category:** Given an existing category, when the Learning Manager confirms its deletion, then the system removes it from the catalog and ensures it is no longer listed.

    ---

    ## US002: Tutorial Management
    **Title:** Manage Tutorials and Classifications  
    **Context:** Publishing  
    **Description:**  
    _As a Tutorial Author, I want to create, view, update, and delete tutorials with their assigned categories so that learners can access up-to-date material._

    **Acceptance Criteria:**
    - **AC2.1 – View Tutorials:** Given existing tutorials, when the Tutorial Author views the tutorial catalog, then all tutorials are displayed showing their title, summary, and assigned category.
    - **AC2.2 - Create Tutorial:** Given valid tutorial information (title, summary, and selected existing category), when the Tutorial Author submits the new tutorial, then the system records the tutorial and includes it in the catalog.
    - **AC2.3 - Edit Tutorial:** Given an existing tutorial, when the Tutorial Author modifies the title, summary, or assigned category, then the system updates the tutorial details.
    - **AC2.4 – Delete Tutorial:** Given an existing tutorial, when the Tutorial Author confirms its removal, then the system deletes the tutorial and updates the catalog.

    ---

    ## US003: User Registration (Sign Up)
    **Title:** Register New User Account  
    **Context:** IAM (Identity and Access Management)  
    **Description:**  
    _As a new user, I want to sign up with a unique username and password so that I can access authenticated publishing services._

    **Acceptance Criteria:**
    - **AC3.1 – Registration Validation:** Given a registration attempt, when the username or password does not comply with required validation rules (e.g., mandatory fields, uniqueness), then the system informs the user of the invalid criteria and prevents registration.
    - **AC3.2 – Successful Registration:** Given valid and available registration credentials, when the user confirms registration, then the system creates the user account and allows them to proceed to authentication.

    ---

    ## US004: User Authentication (Sign In & Sign Out)
    **Title:** Authenticate and Session Management  
    **Context:** IAM (Identity and Access Management)  
    **Description:**  
    _As a registered user, I want to sign in to access protected features and sign out when my session is complete._

    **Acceptance Criteria:**
    - **AC4.1 – Sign In:** Given valid user credentials, when the user attempts to sign in, then the system authenticates the user and establishes an active session.
    - **AC4.2 - Authentication State:** Given an authenticated user with an active session, when interacting with the system, then the user's identity is recognized and authenticated actions are permitted.
    - **AC4.3 - Route Protection:** Given an anonymous user, when the user tries to open a protected section, then the system redirects the user to the sign-in screen.
    - **AC4.4 - Sign Out:** Given an active session, when the user requests to sign out, then the system terminates the active session and revokes access to protected actions until subsequent authentication.

    ---

    ## US005: Internationalization and Language Switching
    **Title:** Switch Application Language  
    **Context:** Shared  
    **Description:**  
    _As a user, I want to switch between supported languages (e.g., English and Spanish) dynamically so that the interface is displayed in my preferred language._

    **Acceptance Criteria:**
    - **AC5.1 – Language Selection:** Given a user selecting a supported language, when the selection is confirmed, then the system updates the active language for the user.
    - **AC5.2 – Content Localization:** Given a language change, when the system presents textual content, then all labels, headings, messages, and options are displayed in the chosen language.

    ---

    ## US006: Navigation and Application Shell
    **Title:** Navigation and System Accessibility  
    **Context:** Shared  
    **Description:**  
    _As a user, I want clear navigation across application sections so that I can seamlessly discover content and manage publishing assets._

    **Acceptance Criteria:**
    - **AC6.1 – Section Navigation:** Given the application sections (such as Home, About, Categories, Tutorials, Authentication), when the user chooses a section, then the system presents the corresponding functional area.
    - **AC6.2 – Unrecognized Resource Handling:** Given an attempt to access a non-existent section or resource, when the system cannot locate the requested item, then the system informs the user that the resource was not found and offers a way to return to the main landing area.
    - **AC6.3 – System Attribution:** Given any system view, when accessed, then organizational branding and copyright terms are available.

    ---

    ## US007: Error Handling and Validation Feedback
    **Title:** Graceful Error Handling and User Feedback  
    **Context:** Shared  
    **Description:**  
    _As a user, I want clear feedback when operations fail or inputs are invalid so that I can understand what happened and how to proceed._

    **Acceptance Criteria:**
    - **AC7.1 - Operation Failure Feedback:** Given a failed system operation or communication failure, when an error occurs, then the system displays a user-friendly error message indicating the failure without exposing internal system details.
    - **AC7.2 – Input Validation Feedback:** Given invalid or missing user input during an action, when input is evaluated, then the system provides specific, actionable feedback on the invalid fields.
    ```
    </details>

    ```
    git add .
    git commit -m "docs: add user stories."
    ```

18. **Look at the architecture, then model the system at the C4 Context level.**
    - Real projects rarely start from a blank slate: the course already sets DDD and this bounded-context split as part of the Definition of Done. What is ahead is learning to read a given architecture and implement it well.
    - Install the **plantuml4idea** plugin so every diagram in this guide renders: `File` → `Settings` → `Plugins` → `Marketplace` → search `plantuml4idea` → `Install`. Restart the IDE if prompted.
    - Right-click the `docs` folder → `New` → `Directory` → type `c4` → Enter.

    Three bounded contexts: **Publishing** (`publishing/domain/model` holds `Category` and `Tutorial`, `publishing/infrastructure` talks to the API, `publishing/application` holds `usePublishingStore`, `publishing/presentation/views` holds the four screens that manage categories and tutorials), **IAM** (identity and access management: users, sign-up, sign-in, and the guard and interceptor that protect the rest; a generic subdomain in DDD terms, needed but not what makes this business different, so this guide builds it last), and **Shared** (`shared/infrastructure` holds the base classes every API client reuses, `shared/presentation` holds `layout`, `language-switcher`, `footer-content`, and the `home`, `about`, and `page-not-found` views, none of which belong to a single context). This layered, bounded-context split is ADR-0001 in `## Release`.

    The steps below draw that architecture at increasing zoom, using the **C4 model** (Context, Container, Component, Code): each level answers a different question about the same system, and stays deliberately silent about anything one level deeper. This first one is the outermost, most zoomed-out view: one box for the whole system, the people who use it, and the other systems it talks to. Nothing about what is inside Learning Center shows up here at all.

    <details>
    <summary>docs/c4/context.puml</summary>

    ```
    @startuml "Context"
    !includeurl https://raw.githubusercontent.com/plantuml-stdlib/C4-PlantUML/master/C4_Context.puml

    title ACME Learning Center - Context Diagram

    Person(user, "User", "A learning manager, tutorial author, or registered user who manages categories and tutorials")
    System(learningcenter, "ACME Learning Center", "Lets a user register, sign in, and manage the categories and tutorials of the publishing catalog, in English or Spanish")
    System_Ext(learningapi, "Learning Center API", "Stores categories, tutorials, and user accounts; a json-server fake API in development")

    Rel(user, learningcenter, "Manages categories and tutorials using [HTTPS]")
    Rel(learningcenter, learningapi, "Reads and writes categories, tutorials, and sessions using [HTTP/JSON]")
    @enduml
    ```
    </details>

    **Note:** `!includeurl` fetches the C4 macro definitions (`Person`, `System`, `System_Ext`, `Rel`, ...) from a public GitHub URL at render time, this needs internet access, unlike `class-diagram.puml`'s plain PlantUML which needs none. If it shows an error instead of a diagram, see [Appendix: If a PlantUML diagram doesn't render](#if-a-plantuml-diagram-doesnt-render).

    ```
    git add .
    git commit -m "docs: add C4 context diagram."
    ```

19. **Model the system at the C4 Container level.** One level in: the separately runnable pieces inside Learning Center, each one something you could deploy and run on its own. Still nothing about what is inside any one of them.

    <details>
    <summary>docs/c4/containers.puml</summary>

    ```
    @startuml "Containers"
    !includeurl https://raw.githubusercontent.com/plantuml-stdlib/C4-PlantUML/master/C4_Container.puml

    title ACME Learning Center - Container Diagram

    Person(user, "User", "A learning manager, tutorial author, or registered user who manages categories and tutorials")
    System_Ext(learningapi, "Learning Center API", "Stores categories, tutorials, and user accounts; a json-server fake API in development")

    System_Boundary(learningcenter, "ACME Learning Center") {
        Container(spa, "Single Page Application", "Vue, PrimeVue", "Lets the user sign in, manage categories and tutorials, and switch languages, all in the browser")
    }

    Rel(user, spa, "Interacts with")
    Rel(spa, learningapi, "Reads and writes categories, tutorials, and sessions using [HTTP/JSON]")
    @enduml
    ```
    </details>

    **Note:** one container, the `Single Page Application`, is where every line of JavaScript in this guide ends up running, entirely inside the user's browser. This guide never deploys it, so the web server that would hand the compiled files to the browser is not drawn: C4 shows what exists, and here it does not yet.

    **Note:** the `Learning Center API` is a `System_Ext`, a system this project does not own. In development it is the `json-server` fake API this setup finishes creating below, later it would be a real backend.

    ```
    git add .
    git commit -m "docs: add C4 container diagram."
    ```

20. **Model the SPA's components by bounded context (C4).** One level deeper, into a single container: the Single Page Application's major internal building blocks. This view groups them by DDD bounded context, the same Publishing/IAM/Shared split `class-diagram.puml` uses, and it is the only diagram where the three contexts appear together: every relation between them is drawn here and nowhere else.

    <details>
    <summary>docs/c4/components-frontend.puml</summary>

    ```
    @startuml "Components-Bounded Contexts"
    !includeurl https://raw.githubusercontent.com/plantuml-stdlib/C4-PlantUML/master/C4_Component.puml

    title ACME Learning Center - Component Diagram (Bounded Contexts)

    System_Ext(learningapi, "Learning Center API", "Stores categories, tutorials, and user accounts; a json-server fake API in development")

    Container_Boundary(spa, "Single Page Application") {
        Component(publishing, "Publishing", "Vue", "Manages the categories and tutorials of the publishing catalog")
        Component(iam, "IAM", "Vue", "Registers users, signs them in and out, and protects routes; a generic subdomain built last")
        Component(shared, "Shared", "Vue", "BaseApi, BaseEndpoint, and cross-cutting presentation: layout, language-switcher, footer-content, home, about, page-not-found")
    }

    Rel(publishing, shared, "Extends BaseApi, and builds its endpoints from BaseEndpoint, from")
    Rel(iam, shared, "Extends BaseApi, and builds its endpoints from BaseEndpoint, from")
    Rel(shared, iam, "Renders authentication-section from, and adds the session token to requests with iamInterceptor from")
    Rel(iam, publishing, "Protects the routes of, with authenticationGuard")
    Rel(publishing, learningapi, "Reads and writes categories and tutorials using [HTTP/JSON]")
    Rel(iam, learningapi, "Signs users up and in using [HTTP/JSON]")
    @enduml
    ```
    </details>

    **Note:** the arrows do not all point the same way, and two of them are worth a second look. `Shared` renders `authentication-section` from `IAM` (that is `layout`) and adds the session token to requests with `iamInterceptor` (that is `BaseApi`), so the kernel depends on a bounded context, and `IAM` protects the routes of `Publishing`, so one context knows about the other. Both are real, and the diagram draws them as they are. The next three diagrams zoom into one context each and never draw a box that belongs to another one.

    ```
    git add .
    git commit -m "docs: add C4 component diagram by bounded context."
    ```

21. **Model the Publishing bounded context's components (C4).** One level deeper than the previous step, into the Publishing box specifically: not "what does the SPA divide into" but "how is Publishing itself divided". It is split by DDD layer. A C4 component is a set of files or classes, never a single class (one class belongs in the class diagram, the last step of this section). So Presentation has one box per Vue component, each one the `.vue` file that holds its script, template, and style, named the way you use it as a tag in a template (`category-list`, not a class name, because a Vue component is not a class), while Application is the single store `usePublishingStore`, and Domain and Infrastructure group the classes that share one responsibility, named after the real folder that holds them (`publishing/domain/model`, `publishing/infrastructure`), with their classes listed in the description. Only Publishing components are drawn here, plus the API. The arrows point toward the domain.

    <details>
    <summary>docs/c4/components-frontend-publishing.puml</summary>

    ```
    @startuml "Components-Publishing"
    !includeurl https://raw.githubusercontent.com/plantuml-stdlib/C4-PlantUML/master/C4_Component.puml

    title ACME Learning Center - Publishing Component Diagram (DDD Layers)

    System_Ext(learningapi, "Learning Center API", "Stores categories, tutorials, and user accounts; a json-server fake API in development")

    Container_Boundary(publishing, "Publishing Bounded Context") {
        Boundary(presentation, "Presentation") {
            Component(category_list, "category-list", "Vue Component", "category-list.vue: lists the categories in a sortable, paginated table")
            Component(category_form, "category-form", "Vue Component", "category-form.vue: creates or edits one category")
            Component(tutorial_list, "tutorial-list", "Vue Component", "tutorial-list.vue: lists the tutorials, each with its category name, in a sortable, paginated table")
            Component(tutorial_form, "tutorial-form", "Vue Component", "tutorial-form.vue: creates or edits one tutorial, with a required category")
        }
        Boundary(application, "Application") {
            Component(publishing_store, "usePublishingStore", "Pinia setup store, entities in shallowRef", "usePublishingStore, publishing.store.js: the categories, the tutorials, the loaded flags, and the errors")
        }
        Boundary(domain, "Domain") {
            Component(publishing_domain_model, "publishing/domain/model", "Entities", "Category (category.entity.js), Tutorial (tutorial.entity.js): what makes a category and a tutorial")
        }
        Boundary(infrastructure, "Infrastructure") {
            Component(publishing_infrastructure, "publishing/infrastructure", "HTTP, Assemblers", "PublishingApi (publishing-api.js), CategoryAssembler (category.assembler.js), TutorialAssembler (tutorial.assembler.js): fetches from the API, the assemblers turn its resources into entities and entities into resources")
        }
    }

    Rel(category_list, publishing_store, "Reads the categories from, and asks to delete one through")
    Rel(category_form, publishing_store, "Asks to add or update a category through")
    Rel(tutorial_list, publishing_store, "Reads the tutorials and the categories from, and asks to delete a tutorial through")
    Rel(tutorial_form, publishing_store, "Reads the categories from, and asks to add or update a tutorial through")
    Rel(category_list, publishing_domain_model, "Displays")
    Rel(tutorial_list, publishing_domain_model, "Displays")
    Rel(category_form, publishing_domain_model, "Builds")
    Rel(tutorial_form, publishing_domain_model, "Builds")
    Rel(publishing_store, publishing_domain_model, "Holds")
    Rel(publishing_store, publishing_infrastructure, "Loads and saves categories and tutorials through")
    Rel(publishing_infrastructure, publishing_domain_model, "Builds")
    Rel(publishing_infrastructure, learningapi, "Reads and writes categories and tutorials using [HTTP/JSON]")
    @enduml
    ```
    </details>

    **Note:** no C4 diagram for what the Learning Center API looks like on the inside, it is a `System_Ext`, a system this project does not own. C4 only models what is actually yours to draw.

    **Note:** `usePublishingStore` reaches `PublishingApi` and the two assemblers (all in the `publishing/infrastructure` component) directly, it imports and instantiates the classes with no interface in between. The dependency points from Application to Infrastructure, the opposite of a strict layered design, where Application would declare a port and the API client would implement it. The guide keeps that simplicity, and the diagram shows the dependency as it is.

    ```
    git add .
    git commit -m "docs: add C4 publishing component diagram."
    ```

22. **Model the IAM bounded context's components (C4).** The same zoom level and the same rules as the previous step, for the IAM box. The one difference in shape: Infrastructure here also holds the route guard and the HTTP interceptor, because they belong to how this context talks to the outside world.

    <details>
    <summary>docs/c4/components-frontend-iam.puml</summary>

    ```
    @startuml "Components-IAM"
    !includeurl https://raw.githubusercontent.com/plantuml-stdlib/C4-PlantUML/master/C4_Component.puml

    title ACME Learning Center - IAM Component Diagram (DDD Layers)

    System_Ext(learningapi, "Learning Center API", "Stores categories, tutorials, and user accounts; a json-server fake API in development")

    Container_Boundary(iam, "IAM Bounded Context") {
        Boundary(presentation, "Presentation") {
            Component(authentication_section, "authentication-section", "Vue Component", "authentication-section.vue: sign in and sign up buttons, or the user name and sign out")
            Component(sign_in_form, "sign-in-form", "Vue Component", "sign-in-form.vue: collects the credentials of a registered user")
            Component(sign_up_form, "sign-up-form", "Vue Component", "sign-up-form.vue: collects the credentials of a new user")
        }
        Boundary(application, "Application") {
            Component(iam_store, "useIamStore", "Pinia setup store, entities in shallowRef", "useIamStore, iam.store.js: whether a user is signed in, who they are, the session token, and the errors")
        }
        Boundary(domain, "Domain") {
            Component(iam_domain, "iam/domain", "Entity, Commands", "User (user.entity.js), SignInCommand (sign-in.command.js), SignUpCommand (sign-up.command.js): who a user is and the credentials to sign in or up")
        }
        Boundary(infrastructure, "Infrastructure") {
            Component(iam_infrastructure, "iam/infrastructure", "HTTP, Assemblers, Resources, Guard, Interceptor", "IamApi (iam-api.js), SignInAssembler (sign-in.assembler.js), SignUpAssembler (sign-up.assembler.js), UserAssembler (user.assembler.js), SignInResource (sign-in.resource.js), SignUpResource (sign-up.resource.js), authenticationGuard (authentication.guard.js), iamInterceptor (iam.interceptor.js): talks to the API, guards routes, and adds the session token to requests")
        }
    }

    Rel(authentication_section, iam_store, "Reads the session from, and asks to sign out through")
    Rel(sign_in_form, iam_store, "Asks to sign in through")
    Rel(sign_up_form, iam_store, "Asks to sign up through")
    Rel(sign_in_form, iam_domain, "Builds SignInCommand from")
    Rel(sign_up_form, iam_domain, "Builds SignUpCommand from")
    Rel(iam_store, iam_domain, "Holds")
    Rel(iam_store, iam_infrastructure, "Signs users in and up through")
    Rel(iam_infrastructure, iam_store, "Reads isSignedIn and currentToken from")
    Rel(iam_infrastructure, iam_domain, "Maps the commands of, and builds User from")
    Rel(iam_infrastructure, learningapi, "Signs users up and in using [HTTP/JSON]")
    @enduml
    ```
    </details>

    **Note:** the guard and the interceptor, both in `iam/infrastructure`, read `useIamStore`, so Infrastructure depends on Application here, again the opposite of a strict layered design. They have to: the session lives in the store, and they are what enforce it.

    ```
    git add .
    git commit -m "docs: add C4 IAM component diagram."
    ```

23. **Model the Shared kernel's components (C4).** The same zoom level as the previous two steps, the remaining box from `components-frontend.puml`: how Shared is divided internally, by DDD layer, with the same rule for what counts as a component. Infrastructure groups the two base classes every API client reuses, named after their folder. Only Shared components are drawn here.

    <details>
    <summary>docs/c4/components-frontend-shared.puml</summary>

    ```
    @startuml "Components-Shared"
    !includeurl https://raw.githubusercontent.com/plantuml-stdlib/C4-PlantUML/master/C4_Component.puml

    title ACME Learning Center - Shared Component Diagram (DDD Layers)

    Container_Boundary(shared, "Shared Kernel") {
        Boundary(presentation, "Presentation") {
            Component(layout, "layout", "Vue Component", "layout.vue (hosted by the root app.vue): the toolbar, navigation, and footer every screen lives in")
            Component(language_switcher, "language-switcher", "Vue Component", "language-switcher.vue: toggles English and Spanish")
            Component(footer_content, "footer-content", "Vue Component", "footer-content.vue: the copyright and attribution notice")
            Component(shared_views, "shared/presentation/views", "Vue Components", "home (home.vue), about (about.vue), page-not-found (page-not-found.vue): the routed pages that belong to no context")
        }
        Boundary(infrastructure, "Infrastructure") {
            Component(shared_infrastructure, "shared/infrastructure", "Base classes", "BaseApi (base-api.js), BaseEndpoint (base-endpoint.js): the configured HTTP client and the generic CRUD every API client reuses")
        }
    }

    Rel(layout, language_switcher, "Renders")
    Rel(layout, footer_content, "Renders")
    @enduml
    ```
    </details>

    **Note:** `layout` renders `authentication-section`, a component of `IAM`, so a dependency goes from the kernel to a bounded context, and `BaseApi` adds the session token with the IAM interceptor. Those relations are drawn in `components-frontend.puml`, not here. A pure shared kernel would not know IAM exists, `layout` sits in Shared because it is the frame of the whole app, not because it is a reusable utility.

    ```
    git add .
    git commit -m "docs: add C4 shared component diagram."
    ```

24. **Go one level deeper than C4: the class diagram.** C4 stops at Components on purpose, it never shows individual classes or their members. The actual classes, fields, and methods this guide builds are one level of detail past what C4 draws, in a plain (non-C4) PlantUML class diagram.

    <details>
    <summary>docs/class-diagram.puml</summary>

    ```plantuml
    @startuml
    ' Class diagram for the ACME Learning Center application

    package "shared.infrastructure" {
      class BaseApi << Base >> {
        - #http: AxiosInstance
        + http: AxiosInstance
      }
      class BaseEndpoint << Base >> {
        + http: AxiosInstance
        + endpointPath: string
        + getAll(): Promise
        + getById(id): Promise
        + create(resource): Promise
        + update(id, resource): Promise
        + delete(id): Promise
      }
    }

    package "publishing.domain.model" {
      class Category << Entity >> {
        - #id: number
        - #name: string
        + id: number
        + name: string
      }
      class Tutorial << Entity >> {
        - #id: number
        - #title: string
        - #summary: string
        - #categoryId: number
        - #category: Category
        + id: number
        + title: string
        + summary: string
        + categoryId: number
        + category: Category
      }
      Tutorial "0..1" o-- "1" Category
    }

    package "publishing.application" {
      class usePublishingStore << Store >> {
        + categories: Category[]
        + tutorials: Tutorial[]
        + errors: string[]
        + categoriesLoaded: boolean
        + tutorialsLoaded: boolean
        + categoriesCount: number
        + tutorialsCount: number
        + fetchCategories(): void
        + fetchTutorials(): void
        + getCategoryById(id): Category
        + addCategory(category: Category): void
        + updateCategory(category: Category): void
        + deleteCategory(category: Category): void
        + getTutorialById(id): Tutorial
        - withCategory(tutorial: Tutorial): Tutorial
        + addTutorial(tutorial: Tutorial): void
        + updateTutorial(tutorial: Tutorial): void
        + deleteTutorial(tutorial: Tutorial): void
      }
    }

    package "publishing.infrastructure" {
      class PublishingApi << Adapter >> {
        - #categoriesEndpoint: BaseEndpoint
        - #tutorialsEndpoint: BaseEndpoint
        + getCategories(): Promise
        + getCategoryById(id): Promise
        + createCategory(resource): Promise
        + updateCategory(resource): Promise
        + deleteCategory(id): Promise
        + getTutorials(): Promise
        + getTutorialById(id): Promise
        + createTutorial(resource): Promise
        + updateTutorial(resource): Promise
        + deleteTutorial(id): Promise
      }
      class CategoryAssembler << Assembler >> {
        + {static} toEntityFromResource(resource): Category
        + {static} toResourceFromEntity(entity: Category): CategoryResource
        + {static} toEntitiesFromResponse(response: AxiosResponse): Category[]
      }
      class TutorialAssembler << Assembler >> {
        + {static} toEntityFromResource(resource): Tutorial
        + {static} toResourceFromEntity(entity: Tutorial): TutorialResource
        + {static} toEntitiesFromResponse(response: AxiosResponse): Tutorial[]
      }
      interface CategoryResource << Resource >> << (R,#FF7700) >> {
        + id: number
        + name: string
      }
      interface TutorialResource << Resource >> << (R,#FF7700) >> {
        + id: number
        + title: string
        + summary: string
        + categoryId: number
      }
    }

    package "publishing.presentation.views" {
      class "category-list" << Component >> {
        + navigateToNew(): void
        + navigateToEdit(id): void
        + confirmDelete(category: Category): void
      }
      class "category-form" << Component >> {
        - form: name
        - isEdit: boolean
        + saveCategory(): void
        + navigateBack(): void
      }
      class "tutorial-list" << Component >> {
        + navigateToNew(): void
        + navigateToEdit(id): void
        + confirmDelete(tutorial: Tutorial): void
      }
      class "tutorial-form" << Component >> {
        - form: title, summary, categoryId
        - isEdit: boolean
        - categoryRequiredError: boolean
        + saveTutorial(): void
        + navigateBack(): void
      }
    }

    package "iam.domain" {
      class User << Entity >> {
        - #id: number
        - #username: string
        + id: number
        + username: string
      }
      class SignInCommand << Command >> {
        - #username: string
        - #password: string
        + username: string
        + password: string
      }
      class SignUpCommand << Command >> {
        - #username: string
        - #password: string
        + username: string
        + password: string
      }
    }

    package "iam.application" {
      class useIamStore << Store >> {
        + users: User[]
        + errors: string[]
        + usersLoaded: boolean
        + isSignedIn: boolean
        + currentUsername: string
        + currentUserId: number
        + currentToken: string
        + signIn(signInCommand: SignInCommand, router): void
        + signUp(signUpCommand: SignUpCommand, router): void
        + signOut(router): void
        + fetchUsers(): void
      }
    }

    package "iam.infrastructure" {
      class IamApi << Adapter >> {
        - #signInEndpoint: BaseEndpoint
        - #signUpEndpoint: BaseEndpoint
        - #usersEndpoint: BaseEndpoint
        + signIn(signInRequest): Promise
        + signUp(signUpRequest): Promise
        + getUsers(): Promise
      }
      class SignInAssembler << Assembler >> {
        + {static} toRequestFromCommand(command: SignInCommand): SignInRequest
        + {static} toResourceFromResponse(response: AxiosResponse): SignInResource
      }
      class SignUpAssembler << Assembler >> {
        + {static} toRequestFromCommand(command: SignUpCommand): SignUpRequest
        + {static} toResourceFromResponse(response: AxiosResponse): SignUpResource
      }
      class UserAssembler << Assembler >> {
        + {static} toEntityFromResource(resource): User
        + {static} toEntitiesFromResponse(response: AxiosResponse): User[]
      }
      class SignInResource << Resource >> {
        + id: number
        + username: string
        + token: string
      }
      class SignUpResource << Resource >> {
        + message: string
      }
      class authenticationGuard << Guard >> {
        + (to, from): boolean | route
      }
      class iamInterceptor << Interceptor >> {
        + (config): config
      }
    }

    package "iam.presentation" {
      class "authentication-section" << Component >> {
        + performSignIn(): void
        + performSignUp(): void
        + performSignOut(): void
      }
      class "sign-in-form" << Component >> {
        + performSignIn(): void
      }
      class "sign-up-form" << Component >> {
        + performSignUp(): void
      }
    }

    ' Shared kernel
    PublishingApi --|> BaseApi
    IamApi --|> BaseApi
    PublishingApi ..> BaseEndpoint : uses
    IamApi ..> BaseEndpoint : uses
    BaseApi ..> iamInterceptor : adds to requests

    ' Publishing
    usePublishingStore o-- Category
    usePublishingStore o-- Tutorial
    usePublishingStore ..> PublishingApi : uses
    usePublishingStore ..> CategoryAssembler : uses
    usePublishingStore ..> TutorialAssembler : uses
    CategoryAssembler ..> Category : builds
    TutorialAssembler ..> Tutorial : builds
    CategoryAssembler ..> CategoryResource : maps
    TutorialAssembler ..> TutorialResource : maps
    "category-list" ..> usePublishingStore
    "category-form" ..> usePublishingStore
    "tutorial-list" ..> usePublishingStore
    "tutorial-form" ..> usePublishingStore

    ' IAM
    useIamStore o-- User
    useIamStore ..> IamApi : uses
    useIamStore ..> SignInAssembler : uses
    useIamStore ..> SignUpAssembler : uses
    useIamStore ..> UserAssembler : uses
    SignInAssembler ..> SignInCommand : maps
    SignUpAssembler ..> SignUpCommand : maps
    SignInAssembler ..> SignInResource : builds
    SignUpAssembler ..> SignUpResource : builds
    UserAssembler ..> User : builds
    authenticationGuard ..> useIamStore : reads
    iamInterceptor ..> useIamStore : reads
    "authentication-section" ..> useIamStore
    "sign-in-form" ..> useIamStore
    "sign-up-form" ..> useIamStore
    "sign-in-form" ..> SignInCommand : builds
    "sign-up-form" ..> SignUpCommand : builds

    @enduml
    ```
    </details>

    **Note:** a member written `- #id` is a private field declared with a native `#`, the way every entity and command in this guide hides its state; each one is read through a getter and never assigned after construction. Vue components are not classes, so the diagram lists each one with the props it receives and the functions its script defines, as if it were one.

    If it shows an error instead of a diagram, see [Appendix: If a PlantUML diagram doesn't render](#if-a-plantuml-diagram-doesnt-render).

    ```
    git add .
    git commit -m "docs: add class diagram."
    ```

25. **Create the fake API data, `server/db.json`.** Right-click the project root → `New` → `File` → type `server/db.json` → Enter. This is the whole database `json-server` serves: two collections, `categories` and `tutorials`, each tutorial pointing at its category through `categoryId`.

    <details>
    <summary>server/db.json</summary>

    ```json
    {
      "categories": [
        { "id": 1, "name": "JavaScript" },
        { "id": 2, "name": "Vue" },
        { "id": 3, "name": "CSharp" },
        { "id": 4, "name": "ASP.NET" }
      ],
      "tutorials": [
        {
          "id": 1,
          "title": "Learn JavaScript",
          "summary": "A comprehensive guide to JavaScript.",
          "categoryId": 1
        },
        {
          "id": 2,
          "title": "Mastering Vue.js",
          "summary": "An in-depth look at Vue.js framework.",
          "categoryId": 2
        },
        {
          "id": 3,
          "title": "C# for Beginners",
          "summary": "Getting started with C# programming.",
          "categoryId": 3
        },
        {
          "id": 4,
          "title": "ASP.NET Core Tutorial",
          "summary": "Building web applications with ASP.NET Core.",
          "categoryId": 4
        }
      ]
    }
    ```
    </details>

    ```
    git add .
    git commit -m "chore: add fake API data."
    ```

26. **Create the fake API routes, `server/routes.json`.** Right-click the `server` folder → `New` → `File` → type `routes.json` → Enter. `json-server` serves `/categories` and `/tutorials` at the root; the application calls them under `/api/v1`, the way the real platform will expose them. This file rewrites every `/api/v1/...` request to the matching root path.

    <details>
    <summary>server/routes.json</summary>

    ```json
    {
      "/api/v1/*": "/$1"
    }
    ```
    </details>

    **Note:** the `*` matches the rest of the path, so `/api/v1/categories/1` is served as `/categories/1`. A pattern that captures only one path segment stops matching as soon as an `id` is in the URL, and every update or delete by `id` then fails with `404`.

    ```
    git add .
    git commit -m "chore: add fake API routes."
    ```

27. **Run the fake API.** In the terminal, at the project root:

    ```
    npx json-server --watch server/db.json --routes server/routes.json --port 3000
    ```

    **Tip:** open [`http://localhost:3000/api/v1/categories`](http://localhost:3000/api/v1/categories) in the browser, this raw JSON is exactly what the categories API client will receive in US001. Stop the server with `Ctrl+C`.

    **Note:** `--watch` also writes back to `server/db.json` every time the application creates, updates, or deletes something, so the file changes while you follow the guide. Restore it to its original content before a commit that includes it, with `git checkout server/db.json`.

    **Note:** no commit here, this step only verifies the two files above.

28. **Connect to GitHub.**

    ```
    gh repo create <org>/learning-center --private --source=. --remote=origin --push --description "ACME Learning Center, a web platform for learning and development, illustrating Domain-Driven Design with Vue."
    ```

    **Note:** run this from the repo root, the same folder `git init` ran in. No `gh`? See [Appendix: Creating the repo without the GitHub CLI](#creating-the-repo-without-the-github-cli), and [Appendix: Signing in to GitHub with a token](#signing-in-to-github-with-a-token) if `gh auth login` gives you trouble on a lab machine.

29. **Install the Git Flow Helper plugin.** WebStorm → `Settings`/`Preferences` → `Plugins` → search **Git Flow Helper** → `Install` → restart if asked.

30. **Initialize Git Flow.** Git Flow Helper widget (bottom status bar) → `Init`. Accept the default branch prefixes (`feature/`, `release/`, `hotfix/`), main branch `main`, development branch `develop`.

    **Note:** you have already used Git Flow in earlier guides this course, so from here on this guide keeps every `Feature Start`/`Feature Publish`/`Feature Finish` step to one line, without repeating what each button does or which checkboxes to set. If you need the full walkthrough again (the widget's exact menu path, the `Integrate Immediately` / `Keep remote branch when finished` options), it is unchanged from those earlier guides.

---

## Switch the Application Language (US005)

Learning Center speaks English and Spanish, and the user picks which one at any time, without reloading the page. `vue-i18n` was already installed in Project Setup; this story turns it on, adds the two dictionaries, and builds the component that changes the language: `language-switcher`. It comes first on purpose, so every screen built afterwards can be checked in both languages from the start.

1. **Start the feature `switch-application-language`.**

2. **See the component tree this story builds.** This is the picture to keep in mind before writing code. There is no layout yet, so for now `app` renders the switcher itself; the next story moves it into a shell of its own. The switcher has no props and no events: it talks to `useI18n()` directly. `:prop` binds an input down, `@event` binds an output back up.

   ```
   +-----+
   | app |
   +-----+
       |
       +--------------------------------------------+
       | language-switcher                          |
       | (no Input/Output, uses useI18n() directly) |
       +--------------------------------------------+
   ```

3. **Create the English dictionary.** Right-click `src` → `New` → `File` → type `locales/en.json` → Enter. Only the home page keys for now, the ones `app` shows to prove the language changes.

   <details>
   <summary>src/locales/en.json (so far)</summary>

   ```json
   {
     "home": {
       "title": "Welcome",
       "content": "Welcome to ACME Learning Center."
     }
   }
   ```
   </details>

   ```
   git add .
   git commit -m "feat(i18n): add English dictionary."
   ```

4. **Create the Spanish dictionary.** Right-click `locales` → `New` → `File` → type `es.json` → Enter. Same keys, same structure.

   <details>
   <summary>src/locales/es.json (so far)</summary>

   ```json
   {
     "home": {
       "title": "Inicio",
       "content": "Bienvenido a ACME Learning Center."
     }
   }
   ```
   </details>

   ```
   git add .
   git commit -m "feat(i18n): add Spanish dictionary."
   ```

5. **Create the i18n instance.** Right-click `src` → `New` → `JavaScript File` → type `i18n` → Enter (WebStorm adds the `.js`).

   <details>
   <summary>src/i18n.js</summary>

   ```javascript
   import {createI18n} from "vue-i18n";
   import en from "./locales/en.json";
   import es from "./locales/es.json";

   const i18n = createI18n({
       legacy: false,
       locale: 'en',
       fallbackLocale: 'en',
       messages: { en, es }
   });

   export default i18n;
   ```
   </details>

   **Note:** `legacy: false` opts into vue-i18n's Composition API mode, `useI18n()` inside `<script setup>`, instead of the older `this.$t(...)` Options API style. `fallbackLocale: 'en'` means a key missing from `es.json` still renders, in English, instead of showing blank or the raw key name.

   ```
   git add .
   git commit -m "feat(i18n): add the i18n instance."
   ```

6. **Register vue-i18n in `main.js`.** The instance joins the application with `.use(i18n)`, which is what makes `useI18n()` available in every component. The IDE offers to add the `import` for `i18n` as you type it, the full file below shows it.

   ```javascript
   createApp(App)
       .use(i18n)
   ```

   <details>
   <summary>src/main.js (so far)</summary>

   ```javascript
   import {createApp} from 'vue'
   import './style.css'
   import App from './app.vue'
   import i18n from "./i18n.js";

   createApp(App)
       .use(i18n)
       .mount('#app')
   ```
   </details>

   ```
   git add .
   git commit -m "feat: register vue-i18n globally."
   ```

7. **Register PrimeVue in `main.js`.** The plugin brings the theme (the `Material` preset), the ripple effect, and the license key from step 15; the two style sheets bring the icon font and the CSS utility classes.

   ```javascript
   const primeUiLicenseKey = import.meta.env.VITE_PRIME_UI_LICENSE_KEY;

   createApp(App)
       .use(PrimeVue, {theme: {preset: Material}, ripple: true, license: primeUiLicenseKey})
   ```

   <details>
   <summary>src/main.js (so far)</summary>

   ```javascript
   import {createApp} from 'vue'
   import './style.css'
   import App from './app.vue'
   import i18n from "./i18n.js";
   import PrimeVue from 'primevue/config';
   import Material from '@primeuix/themes/material';
   import 'primeflex/primeflex.css';
   import 'primeicons/primeicons.css';

   const primeUiLicenseKey = import.meta.env.VITE_PRIME_UI_LICENSE_KEY;

   createApp(App)
       .use(i18n)
       .use(PrimeVue, {theme: {preset: Material}, ripple: true, license: primeUiLicenseKey})
       .mount('#app')
   ```
   </details>

   ```
   git add .
   git commit -m "feat: register PrimeVue globally."
   ```

8. **Register the `SelectButton` component in `main.js`.** PrimeVue components are registered one by one with a `pv-` prefix, so a template can use `<pv-select-button>` without importing it.

   ```javascript
   createApp(App)
       .component('pv-select-button',  SelectButton)
   ```

   <details>
   <summary>src/main.js (so far)</summary>

   ```javascript
   import {createApp} from 'vue'
   import './style.css'
   import App from './app.vue'
   import i18n from "./i18n.js";
   import PrimeVue from 'primevue/config';
   import Material from '@primeuix/themes/material';
   import 'primeflex/primeflex.css';
   import 'primeicons/primeicons.css';
   import {
       SelectButton
   } from "primevue";

   const primeUiLicenseKey = import.meta.env.VITE_PRIME_UI_LICENSE_KEY;

   createApp(App)
       .use(i18n)
       .use(PrimeVue, {theme: {preset: Material}, ripple: true, license: primeUiLicenseKey})
       .component('pv-select-button',  SelectButton)
       .mount('#app')
   ```
   </details>

   ```
   git add .
   git commit -m "feat: register the PrimeVue select button."
   ```

9. **Create the `language-switcher` component.** Right-click `src` → `New` → `Vue Single-File Component` → `Composition API` → type `shared/presentation/components/language-switcher` → Enter. `vue-i18n`'s own `useI18n()` already exposes everything this component needs, no props at all.

   <details>
   <summary>src/shared/presentation/components/language-switcher.vue</summary>

   ```vue
   <script setup>
     import {useI18n} from "vue-i18n";
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
   git commit -m "feat(shared): add language-switcher component."
   ```

10. **Show the switcher and a translated text in `app`.** For now `app` renders the switcher plus the welcome text, so the change of language can be seen.

    ```vue
    <language-switcher/>
    <h1>{{ t('home.title') }}</h1>
    <p>{{ t('home.content') }}</p>
    ```

    <details>
    <summary>src/app.vue (so far)</summary>

    ```vue
    <script setup>
    import LanguageSwitcher from "./shared/presentation/components/language-switcher.vue";
    import {useI18n} from "vue-i18n";

    const {t} = useI18n();
    </script>

    <template>
      <language-switcher/>
      <h1>{{ t('home.title') }}</h1>
      <p>{{ t('home.content') }}</p>
    </template>
    ```
    </details>

    **Note:** `t('home.title')` looks the key up in the active language's dictionary and renders the text, and renders it again when the language changes. This template is temporary, the next story replaces it with the real application shell.

    ```
    git add .
    git commit -m "feat(app): show language-switcher in the app shell."
    ```

11. **Run it.**

    ```
    npm run dev
    ```

    Open the local URL Vite prints. The page shows the `EN` and `ES` toggles with `EN` highlighted, and the heading `Welcome`. Choose `ES`: the toggle moves and the text becomes `Inicio`. Choose `EN` again to go back. Stop the server with `Ctrl+C`.

12. **Publish and finish the feature.**

---

## Navigate the Application (US006)

A user moves around Learning Center from a toolbar that is always there, sees the organization's name and copyright on every screen, and lands on a helpful page when they type an address that does not exist. This story builds that application shell: the `layout` with its navigation, the `home` and `about` views, the `page-not-found` view, the footer, and the routes that tie them together. The catalog and the account screens plug into this shell in the stories that follow.

1. **Start the feature `navigate-the-application`.**

2. **See the component tree this story builds.** `layout` takes over from `app` as the frame of the whole application: it owns the toolbar, renders `language-switcher` and `footer-content`, and hosts the routed view in between. `home`, `about`, and `page-not-found` are not children in the template, the router puts the right one into `layout`'s `<router-view/>` for the current address. None of them has props or events.

   ```
   +-----+
   | app |
   +-----+
       |
       +-----------------------------+
       | layout                      |
       | State: drawer: Ref<boolean> |
       | (no Input/Output)           |
       +-----------------------------+
           |
           +--------------------------------------------+
           | language-switcher                          |
           | (no Input/Output, uses useI18n() directly) |
           +--------------------------------------------+
           |
           +-------------------------------------------+
           | <router-view/>  one routed view at a time |
           | home, about, or page-not-found            |
           | (no Input/Output)                         |
           +-------------------------------------------+
           |
           +--------------------------------------------+
           | footer-content                             |
           | (no Input/Output, uses useI18n() directly) |
           +--------------------------------------------+
   ```

3. **Add the navigation texts to the English dictionary.** Open `src/locales/en.json`. The navigation options, the two views, the not-found page, and the footer bring their texts, the `home` keys from the previous story stay where they are.

   <details>
   <summary>src/locales/en.json (so far)</summary>

   ```json
   {
     "option": {
       "home": "Home",
       "about": "About"
     },
     "authoring-phrase": {
       "intro": "Made with",
       "use": "using",
       "author": "by {brand} Developer Team"
     },
     "about": {
       "title": "About Us",
       "content": "ACME Learning Center is an Education Business Platform, part of ACME Corporation."
     },
     "home": {
       "title": "Welcome",
       "content": "Welcome to ACME Learning Center."
     },
     "page-not-found": {
       "title": "Page Not Found",
       "content": "The path {unavailable-route} is not available.",
       "go-home": "Go Home"
     }
   }
   ```
   </details>

   ```
   git add .
   git commit -m "feat(i18n): add navigation texts to the English dictionary."
   ```

4. **Add the navigation texts to the Spanish dictionary.** Open `src/locales/es.json`, same keys and the same structure.

   <details>
   <summary>src/locales/es.json (so far)</summary>

   ```json
   {
     "option": {
       "home": "Inicio",
       "about": "Acerca de"
     },
     "authoring-phrase": {
       "intro": "Hecho con",
       "use": "utilizando",
       "author": "por el Equipo de Desarrollo de {brand}"
     },
     "about": {
       "title": "Acerca de Nosotros",
       "content": "ACME Learning Center es una Plataforma educativa, parte de ACME Corporation."
     },
     "home": {
       "title": "Inicio",
       "content": "Bienvenido a ACME Learning Center."
     },
     "page-not-found": {
       "title": "Página no encontrada",
       "content": "La ruta {unavailable-route} no está disponible.",
       "go-home": "Ir al Inicio"
     }
   }
   ```
   </details>

   ```
   git add .
   git commit -m "feat(i18n): add navigation texts to the Spanish dictionary."
   ```

5. **Create the `home` view.** Right-click `src` → `New` → `Vue Single-File Component` → `Composition API` → type `shared/presentation/views/home` → Enter.

   <details>
   <summary>src/shared/presentation/views/home.vue</summary>

   ```vue
   <script setup>
   import { useI18n } from "vue-i18n";

   const { t } = useI18n();
   </script>

   <template>
     <section class="pt-6 p-4 md:p-5">
       <div class="flex flex-column gap-3">
         <h1 class="text-4xl font-bold text-color">{{ t('home.title') }}</h1>
         <p class="m-0 line-height-3 text-color-secondary">{{ t('home.content') }}</p>
       </div>
     </section>
   </template>
   ```
   </details>

   **Note:** the `section`, the flex column and the spacing are PrimeFlex utility classes (`pt-6`, `p-4`, `flex`, `flex-column`, `gap-3`), there is no CSS of its own here. The `md:p-5` prefix applies that padding only from the medium breakpoint up.

   ```
   git add .
   git commit -m "feat(shared): add home view."
   ```

6. **Create the `about` view.** Right-click `views` → `New` → `Vue Single-File Component` → `Composition API` → type `about` → Enter. It shows the ACME logo added back in Project Setup, with the same layout as `home`.

   <details>
   <summary>src/shared/presentation/views/about.vue</summary>

   ```vue
   <script setup>
   import {useI18n} from "vue-i18n";

   const { t } = useI18n();
   </script>

   <template>
     <section class="pt-6 p-4 md:p-5">
       <div class="flex flex-column gap-3">
         <h1 class="text-4xl font-bold text-color">{{ t('about.title') }}</h1>
         <img src="/acme-logo.svg" alt="ACME Logo" class="w-12rem h-auto border-round-md"/>
         <p class="m-0 line-height-3 text-color-secondary">{{ t('about.content') }}</p>
       </div>
     </section>
   </template>
   ```
   </details>

   ```
   git add .
   git commit -m "feat(shared): add about view."
   ```

7. **Create the `page-not-found` view.** Right-click `views` → `New` → `Vue Single-File Component` → `Composition API` → type `page-not-found` → Enter. It names the address the user typed, and offers a way back home.

   ```javascript
   const route = useRoute();
   const unavailableRoute = route.path;
   ```

   <details>
   <summary>src/shared/presentation/views/page-not-found.vue</summary>

   ```vue
   <script setup>
   import { useRoute } from "vue-router";
   import { useI18n } from "vue-i18n";

   const route = useRoute();
   const unavailableRoute = route.path;
   const { t } = useI18n();
   </script>

   <template>
     <section class="pt-6 p-4 md:p-5">
       <div class="flex flex-column gap-3">
         <h1 class="text-4xl font-bold text-color">{{ t('page-not-found.title') }}</h1>
         <p class="m-0 line-height-3 text-color-secondary">
           {{ t('page-not-found.content', { 'unavailable-route': unavailableRoute }) }}
         </p>
         <router-link to="/home" class="text-primary font-medium">
           {{ t('page-not-found.go-home') }}
         </router-link>
       </div>
     </section>
   </template>
   ```
   </details>

   **Note:** `useRoute()` returns the current route, and `route.path` is the address the router could not match. The dictionary's `{unavailable-route}` placeholder receives it as the named argument in `t(...)`.

   ```
   git add .
   git commit -m "feat(shared): add page-not-found view."
   ```

8. **Create the `footer-content` component.** Right-click `src` → `New` → `Vue Single-File Component` → `Composition API` → type `shared/presentation/components/footer-content` → Enter. The organization's copyright and the attribution for PrimeVue, translated with the `authoring-phrase` keys.

   <details>
   <summary>src/shared/presentation/components/footer-content.vue</summary>

   ```vue
   <script setup>
   import { useI18n } from "vue-i18n";

   const { t } = useI18n();
   </script>

   <template>
     <footer class="w-full border-none bg-primary mt-4 border-round-md shadow-1 p-3">
       <div class="flex flex-column align-items-center justify-content-center gap-2 text-center text-white">
         <p class="m-0 text-sm font-medium">Copyright &copy; 2026. ACME Studios</p>
         <p class="m-0 text-sm line-height-3">
           {{ t('authoring-phrase.intro') }}
           <i class="pi pi-heart text-pink-300" aria-hidden="true"/>
           {{ t('authoring-phrase.use') }}
           <a
             href="https://primevue.org/"
             target="_blank"
             rel="noopener noreferrer"
             class="text-white font-bold no-underline hover:text-blue-100"
           >
             PrimeVue
           </a>
           {{ t('authoring-phrase.author', { brand: 'ACME' }) }}
         </p>
       </div>
     </footer>
   </template>
   ```
   </details>

   **Note:** `t('authoring-phrase.author', { brand: 'ACME' })` fills the `{brand}` placeholder from an argument passed at the call site, the dictionary itself never hardcodes which brand.

   **Note:** the footer is a normal `footer` element that flows after the page content, it is not pinned to the bottom of the window, so on a short window it never covers a button of the forms built in later stories.

   ```
   git add .
   git commit -m "feat(shared): add footer-content component."
   ```

9. **Register the components the layout uses.** The toolbar, the buttons, and the drawer of the `layout` come from PrimeVue, registered in `main.js` like `SelectButton` was.

   ```javascript
   createApp(App)
       .component('pv-button',         Button)
       .component('pv-drawer',         Drawer)
       .component('pv-toolbar',        Toolbar)
   ```

   <details>
   <summary>src/main.js (so far)</summary>

   ```javascript
   import {createApp} from 'vue'
   import './style.css'
   import App from './app.vue'
   import i18n from "./i18n.js";
   import PrimeVue from 'primevue/config';
   import Material from '@primeuix/themes/material';
   import 'primeflex/primeflex.css';
   import 'primeicons/primeicons.css';
   import {
       Button,
       Drawer,
       SelectButton,
       Toolbar
   } from "primevue";

   const primeUiLicenseKey = import.meta.env.VITE_PRIME_UI_LICENSE_KEY;

   createApp(App)
       .use(i18n)
       .use(PrimeVue, {theme: {preset: Material}, ripple: true, license: primeUiLicenseKey})
       .component('pv-button',         Button)
       .component('pv-drawer',         Drawer)
       .component('pv-select-button',  SelectButton)
       .component('pv-toolbar',        Toolbar)
       .mount('#app')
   ```
   </details>

   ```
   git add .
   git commit -m "feat: register the PrimeVue toolbar, button and drawer."
   ```

10. **Create the `layout` component.** Right-click `src` → `New` → `Vue Single-File Component` → `Composition API` → type `shared/presentation/components/layout` → Enter. The frame of the whole application.

    The script defines the `items` of the navigation, each one a dictionary key and the address it goes to, and the `drawer` flag the hamburger button toggles:

    ```javascript
    const drawer = ref(false);

    const toggleDrawer = () => {
      drawer.value = !drawer.value;
    }

    const items = [
      {label: 'option.home', to: '/home'},
      {label: 'option.about', to: '/about'}
    ];
    ```

    The template puts a toolbar at the top, the routed view in the middle, and the footer at the bottom:

    ```vue
    <main class="mt-7">
      <router-view/>
    </main>
    <footer-content/>
    ```

    <details>
    <summary>src/shared/presentation/components/layout.vue (so far)</summary>

    ```vue
    <script setup>
    import LanguageSwitcher from "./language-switcher.vue";
    import {ref} from "vue";
    import {useI18n} from "vue-i18n";
    import FooterContent from "./footer-content.vue";

    const { t } = useI18n();

    const drawer = ref(false);

    const toggleDrawer = () => {
      drawer.value = !drawer.value;
    }

    const items = [
      {label: 'option.home', to: '/home'},
      {label: 'option.about', to: '/about'}
    ];
    </script>

    <template>
      <header class="absolute top-0 left-0 w-full">
        <pv-toolbar class="bg-primary">
          <template #start>
            <pv-button class="p-button-text" icon="pi pi-bars" @click="toggleDrawer"/>
            <h3>ACME Learning Center</h3>
          </template>
          <template #end>
            <div class="flex-column mr-3">
              <pv-button v-for="item in items" :key="item.label" as-child v-slot="slotProps">
                <router-link :to="item.to" :class="slotProps['class']">{{ t(item.label) }}</router-link>
              </pv-button>
            </div>
            <language-switcher/>
          </template>
        </pv-toolbar>
        <pv-drawer v-model:visible="drawer"/>
      </header>
      <main class="mt-7">
        <router-view/>
      </main>
      <footer-content/>
    </template>
    ```
    </details>

    **Note:** each `router-link` is rendered through `as-child` and `v-slot`, so the link takes the classes of a `pv-button` and looks like one. The hamburger button opens a drawer with nothing in it yet, it is here so the toolbar already has its final shape.

    ```
    git add .
    git commit -m "feat(shared): add layout component."
    ```

11. **Define the application routes.** Right-click `src` → `New` → `JavaScript File` → type `router` → Enter. Two named routes, a redirect from `/` to `/home`, and a last route that catches every other address and shows `page-not-found`.

    <details>
    <summary>src/router.js (so far)</summary>

    ```javascript
    import {createRouter, createWebHistory} from "vue-router";
    import Home from "./shared/presentation/views/home.vue";

    const about = () => import('./shared/presentation/views/about.vue');
    const pageNotFound = () => import('./shared/presentation/views/page-not-found.vue');
    const routes = [
        { path: '/home',            name: 'home',       component: Home,        meta: { title: 'Home' } },
        { path: '/about',           name: 'about',      component: about,       meta: { title: 'About' } },
        { path: '/',                redirect: '/home' },
        { path: '/:pathMatch(.*)*', name: 'not-found', component: pageNotFound, meta: { title: 'Page Not Found' } }
    ];

    const router = createRouter({
        history: createWebHistory(import.meta.env.BASE_URL),
        routes: routes
    });

    router.beforeEach((to) => {
        const baseTitle = 'ACME Learning Center';
        document.title = `${baseTitle} - ${to.meta['title']}`;
    });

    export default router;
    ```
    </details>

    **Note:** `about` and `pageNotFound` are lazy-loaded, their code is downloaded only the first time the user opens them, while `Home` is imported up front because it is the first screen. `beforeEach` runs before every navigation and writes the `meta.title` of the route into the browser tab.

    ```
    git add .
    git commit -m "feat: define the application routes."
    ```

12. **Register the router in `main.js`.**

    ```javascript
    createApp(App)
        .use(router)
    ```

    <details>
    <summary>src/main.js (so far)</summary>

    ```javascript
    import {createApp} from 'vue'
    import './style.css'
    import App from './app.vue'
    import i18n from "./i18n.js";
    import PrimeVue from 'primevue/config';
    import Material from '@primeuix/themes/material';
    import 'primeflex/primeflex.css';
    import 'primeicons/primeicons.css';
    import {
        Button,
        Drawer,
        SelectButton,
        Toolbar
    } from "primevue";
    import router from "./router.js";

    const primeUiLicenseKey = import.meta.env.VITE_PRIME_UI_LICENSE_KEY;

    createApp(App)
        .use(i18n)
        .use(PrimeVue, {theme: {preset: Material}, ripple: true, license: primeUiLicenseKey})
        .component('pv-button',         Button)
        .component('pv-drawer',         Drawer)
        .component('pv-select-button',  SelectButton)
        .component('pv-toolbar',        Toolbar)
        .use(router)
        .mount('#app')
    ```
    </details>

    ```
    git add .
    git commit -m "feat: register the router globally."
    ```

13. **Show `layout` in the `app` shell.** `app` stops rendering the switcher and the welcome text itself, `layout` renders the switcher and everything else.

    <details>
    <summary>src/app.vue</summary>

    ```vue
    <script setup>
    import Layout from "./shared/presentation/components/layout.vue";
    </script>

    <template>
      <layout/>
    </template>
    ```
    </details>

    ```
    git add .
    git commit -m "feat(app): show layout in the app shell."
    ```

14. **Run it.**

    ```
    npm run dev
    ```

    Open the local URL Vite prints. The address becomes `/home` on its own, the toolbar shows `ACME Learning Center`, the `Home` and `About` options, and the language toggles, and the footer sits at the bottom of the window.
    - Click `About`: the logo and the text appear, the address becomes `/about`, and the tab reads `ACME Learning Center - About`.
    - Choose `ES`: every text on screen changes, toolbar options and footer included.
    - Type `/anything` after the address in the address bar: the not-found page names the path you typed. Click `Go Home` to come back.

    Stop the server with `Ctrl+C`.

    **Note:** the toolbar has no `Categories` or `Tutorials` option yet, those sections do not exist. Each one joins the menu in its own story.

15. **Publish and finish the feature.**

---

## Manage Categories (US001)

A Learning Manager keeps the list of tutorial categories: sees them in a table, creates one, renames it, and deletes it. This story builds the Publishing bounded context from the ground up: the `Category` entity, the shared classes every REST endpoint of the application builds on, the infrastructure that talks to the fake API, the `usePublishingStore` the views read from, and the two views, `category-list` and `category-form`. Errors are handled along the way, US007 has no story of its own.

1. **Start the feature `manage-categories`.**

2. **See the component tree this story builds.** Two routed views join the shell from the previous story, `category-list` and `category-form`. Neither has props or events: they call `usePublishingStore()` directly, the same way `language-switcher` calls `useI18n()` directly, and the router decides which one shows in `<router-view/>`.

   ```
   +-----+
   | app |
   +-----+
       |
       +-----------------------------+
       | layout                      |
       | State: drawer: Ref<boolean> |
       | (no Input/Output)           |
       +-----------------------------+
           |
           +--------------------------------------------+
           | language-switcher                          |
           | (no Input/Output, uses useI18n() directly) |
           +--------------------------------------------+
           |
           +----------------------------------------------------------+
           | <router-view/>  one routed view at a time                |
           | home, about, page-not-found, category-list, category-form |
           | (no Input/Output, category-list and category-form use    |
           |  usePublishingStore() directly)                          |
           +----------------------------------------------------------+
           |
           +--------------------------------------------+
           | footer-content                             |
           | (no Input/Output, uses useI18n() directly) |
           +--------------------------------------------+
   ```

3. **Create the `Category` entity, fields and constructor.** Right-click `src` → `New` → `JavaScript File` → type `publishing/domain/model/category.entity` → Enter (WebStorm creates the `publishing/domain/model` folders with it). A category is an `id` and a `name`, both private fields (`#`), set once, in the constructor.

   <details>
   <summary>src/publishing/domain/model/category.entity.js (so far)</summary>

   ```javascript
   export class Category {
       #id;
       #name;

       constructor({id = null, name = ''}) {
           this.#id = id;
           this.#name = name;
       }
   }
   ```
   </details>

   **Note:** no commit here, `Category` still needs its read accessors, the next step.

4. **Add the read accessors.** One getter per field, nothing else: `Category` has no setter, there is no operation that changes an existing one, to rename a category the application builds a new one.

   <details>
   <summary>src/publishing/domain/model/category.entity.js (Full file)</summary>

   ```javascript
   export class Category {
       #id;
       #name;

       constructor({id = null, name = ''}) {
           this.#id = id;
           this.#name = name;
       }

       get id() {
           return this.#id;
       }

       get name() {
           return this.#name;
       }
   }
   ```
   </details>

   ```
   git add .
   git commit -m "feat(publishing): add Category entity."
   ```

5. **Create `BaseApi`.** Right-click `src` → `New` → `JavaScript File` → type `shared/infrastructure/base-api` → Enter. It lives in the shared kernel because every bounded context's API client extends it: it configures one Axios instance from the environment variable added in Project Setup, and exposes it through a getter, `#http` stays private.

   <details>
   <summary>src/shared/infrastructure/base-api.js (so far)</summary>

   ```javascript
   import axios from "axios";

   const platformApi = import.meta.env.VITE_LEARNING_PLATFORM_API_URL;

   export class BaseApi {
       #http;

       constructor() {
           this.#http = axios.create({
               baseURL: platformApi,
               headers: {
                   'Content-Type': 'application/json',
                   'Access-Control-Allow-Origin': '*'
               },
           });
       }

       get http() {
           return this.#http;
       }
   }
   ```
   </details>

   **Note:** no interceptor yet. Once sign-in exists, a later story adds one that attaches the session token to every outgoing request; that story edits this file again.

   ```
   git add .
   git commit -m "feat(shared): add BaseApi."
   ```

6. **Create `BaseEndpoint`.** Right-click `src` → `New` → `JavaScript File` → type `shared/infrastructure/base-endpoint` → Enter. The generic CRUD every REST resource of this application needs, built once against `BaseApi`'s Axios instance and one relative path.

   <details>
   <summary>src/shared/infrastructure/base-endpoint.js</summary>

   ```javascript
   export class BaseEndpoint {
       constructor(baseApi, endpointPath) {
           this.http = baseApi.http;
           this.endpointPath = endpointPath;
       }

       getAll() {
           return this.http.get(this.endpointPath);
       }

       getById(id) {
           return this.http.get(`${this.endpointPath}/${id}`);
       }

       create(resource) {
           return this.http.post(this.endpointPath, resource);
       }

       update(id, resource) {
           return this.http.put(`${this.endpointPath}/${id}`, resource);
       }

       delete(id) {
           return this.http.delete(`${this.endpointPath}/${id}`);
       }
   }
   ```
   </details>

   ```
   git add .
   git commit -m "feat(shared): add BaseEndpoint."
   ```

7. **Create the `CategoryAssembler`.** Right-click `src` → `New` → `JavaScript File` → type `publishing/infrastructure/category.assembler` → Enter. It maps between `Category` entities and the plain objects that travel over HTTP: a *resource*.

   ```javascript
   static toResourceFromEntity(entity) {
       return {id: entity.id, name: entity.name};
   }
   ```

   <details>
   <summary>src/publishing/infrastructure/category.assembler.js</summary>

   ```javascript
   import {Category} from "../domain/model/category.entity.js";

   export class CategoryAssembler {
       static toEntityFromResource(resource) {
           return new Category({...resource})
       }

       static toResourceFromEntity(entity) {
           return {id: entity.id, name: entity.name};
       }

       static toEntitiesFromResponse(response) {
           if (response.status !== 200) {
               console.error(`${response.status}, ${response.statusText}`);
               return [];
           }
           let resources = response.data instanceof Array ? response.data : response.data['categories'];

           return resources.map(resource => this.toEntityFromResource(resource));
       }
   }
   ```
   </details>

   **Note:** `Category`'s fields are private (`#id`/`#name`), so `JSON.stringify(category)` returns `{}`, Axios would post an empty body. A resource is a plain object built by hand from the entity's getters, that is what `toResourceFromEntity` is for, and it is what `createCategory`/`updateCategory` send below, never the entity itself.

   ```
   git add .
   git commit -m "feat(publishing): add CategoryAssembler."
   ```

8. **Create `PublishingApi`.** Right-click `src` → `New` → `JavaScript File` → type `publishing/infrastructure/publishing-api` → Enter. It extends `BaseApi` and composes one `BaseEndpoint` per resource, here just categories.

   <details>
   <summary>src/publishing/infrastructure/publishing-api.js (so far)</summary>

   ```javascript
   import {BaseApi} from "../../shared/infrastructure/base-api.js";
   import {BaseEndpoint} from "../../shared/infrastructure/base-endpoint.js";

   const categoriesEndpointPath = import.meta.env.VITE_CATEGORIES_ENDPOINT_PATH;

   export class PublishingApi extends BaseApi {
       #categoriesEndpoint;

       constructor() {
           super();
           this.#categoriesEndpoint = new BaseEndpoint(this, categoriesEndpointPath);
       }

       getCategories() {
           return this.#categoriesEndpoint.getAll();
       }

       getCategoryById(id) {
           return this.#categoriesEndpoint.getById(id);
       }

       createCategory(resource) {
           return this.#categoriesEndpoint.create(resource);
       }

       updateCategory(resource) {
           return this.#categoriesEndpoint.update(resource.id, resource);
       }

       deleteCategory(id) {
           return this.#categoriesEndpoint.delete(id);
       }
   }
   ```
   </details>

   ```
   git add .
   git commit -m "feat(publishing): add PublishingApi."
   ```

9. **Create `usePublishingStore`.** Right-click `src` → `New` → `JavaScript File` → type `publishing/application/publishing.store` → Enter. A Pinia setup store: a function that returns the reactive state and the actions every Publishing view reads and calls, so no view talks to `PublishingApi` directly.

   <details>
   <summary>src/publishing/application/publishing.store.js (so far)</summary>

   ```javascript
   import {defineStore} from "pinia";
   import {computed, ref, shallowRef} from "vue";
   import {PublishingApi} from "../infrastructure/publishing-api.js";
   import {CategoryAssembler} from "../infrastructure/category.assembler.js";
   import {Category} from "../domain/model/category.entity.js";

   const publishingApi = new PublishingApi();

   const usePublishingStore = defineStore('publishing', () => {
       const categories = shallowRef([]);
       const errors = ref([]);
       const categoriesLoaded = ref(false);
       const categoriesCount = computed(() => {
           return categoriesLoaded.value ? categories.value.length : 0;
       });

       function fetchCategories() {
           publishingApi.getCategories().then(response => {
               categories.value = CategoryAssembler.toEntitiesFromResponse(response);
               categoriesLoaded.value = true;
           }).catch(error => {
               errors.value.push(error.message);
           });
       }

       function getCategoryById(id) {
           const idNum = parseInt(id);
           return categories.value.find(category => category.id === idNum);
       }

       return {
           categories,
           errors,
           categoriesLoaded,
           categoriesCount,
           fetchCategories,
           getCategoryById
       }
   });

   export default usePublishingStore;
   ```
   </details>

   **Note:** `categories` is a `shallowRef`, not a `ref`. `Category` keeps its state in native `#` private fields, and Vue's `reactive()`/`ref()` wrap an object in a `Proxy` to track it; a `Proxy` is a different object from the one the class declared, so it does not carry the class's *private brand*, and reading a `#field` getter through it throws `TypeError: Cannot read private member`. `shallowRef` tracks only the replacement of the array itself, never wraps the `Category` instances inside it, so every action below replaces the whole array instead of mutating it in place. See ADR-0004 in `## Release` for the full reasoning.

   **Note:** `errors` holds plain strings, `error.message`, not `Error` objects, so the views below can join and render it directly.

   **Note:** this is where US007, graceful error handling, actually lives: every store action below catches its own failure and pushes a message here, instead of letting the promise reject silently; the views render `errors` in a line at the bottom of the page.

   ```
   git add .
   git commit -m "feat(publishing): add usePublishingStore."
   ```

10. **Register `DataTable`, `Column`, `ConfirmationService`, and `ConfirmDialog` in `main.js`.** `category-list` needs a sortable, paginated table (`pv-data-table`/`pv-column`) and a confirmation prompt before deleting a row (`useConfirm()`, backed by `ConfirmationService` and one `<pv-confirm-dialog/>` rendered once in `layout`).

    ```javascript
    createApp(App)
        .use(ConfirmationService)
        .component('pv-column',         Column)
        .component('pv-confirm-dialog', ConfirmDialog)
        .component('pv-data-table',     DataTable)
    ```

    <details>
    <summary>src/main.js (so far)</summary>

    ```javascript
    import {createApp} from 'vue'
    import './style.css'
    import App from './app.vue'
    import i18n from "./i18n.js";
    import PrimeVue from 'primevue/config';
    import Material from '@primeuix/themes/material';
    import 'primeflex/primeflex.css';
    import 'primeicons/primeicons.css';
    import {
        Button,
        Column,
        ConfirmationService,
        ConfirmDialog,
        DataTable,
        Drawer,
        SelectButton,
        Toolbar
    } from "primevue";
    import router from "./router.js";

    const primeUiLicenseKey = import.meta.env.VITE_PRIME_UI_LICENSE_KEY;

    createApp(App)
        .use(i18n)
        .use(PrimeVue, {theme: {preset: Material}, ripple: true, license: primeUiLicenseKey})
        .use(ConfirmationService)
        .component('pv-button',         Button)
        .component('pv-column',         Column)
        .component('pv-confirm-dialog', ConfirmDialog)
        .component('pv-data-table',     DataTable)
        .component('pv-drawer',         Drawer)
        .component('pv-select-button',  SelectButton)
        .component('pv-toolbar',        Toolbar)
        .use(router)
        .mount('#app')
    ```
    </details>

    **Note:** `pinia` is not registered yet, `usePublishingStore` cannot run without it. That is the next step, right before the first view that calls it.

    ```
    git add .
    git commit -m "feat: register the PrimeVue data table, column, confirmation service and confirm dialog."
    ```

11. **Register Pinia in `main.js`.**

    ```javascript
    createApp(App)
        .use(pinia)
    ```

    <details>
    <summary>src/main.js (so far)</summary>

    ```javascript
    import {createApp} from 'vue'
    import './style.css'
    import App from './app.vue'
    import i18n from "./i18n.js";
    import PrimeVue from 'primevue/config';
    import Material from '@primeuix/themes/material';
    import 'primeflex/primeflex.css';
    import 'primeicons/primeicons.css';
    import {
        Button,
        Column,
        ConfirmationService,
        ConfirmDialog,
        DataTable,
        Drawer,
        SelectButton,
        Toolbar
    } from "primevue";
    import router from "./router.js";
    import pinia from "./pinia.js";

    const primeUiLicenseKey = import.meta.env.VITE_PRIME_UI_LICENSE_KEY;

    createApp(App)
        .use(i18n)
        .use(PrimeVue, {theme: {preset: Material}, ripple: true, license: primeUiLicenseKey})
        .use(ConfirmationService)
        .component('pv-button',         Button)
        .component('pv-column',         Column)
        .component('pv-confirm-dialog', ConfirmDialog)
        .component('pv-data-table',     DataTable)
        .component('pv-drawer',         Drawer)
        .component('pv-select-button',  SelectButton)
        .component('pv-toolbar',        Toolbar)
        .use(router)
        .use(pinia)
        .mount('#app')
    ```
    </details>

    <details>
    <summary>src/pinia.js</summary>

    ```javascript
    import {createPinia} from "pinia";

    const pinia = createPinia();

    export default pinia;
    ```
    </details>

    ```
    git add .
    git commit -m "feat: register Pinia globally."
    ```

12. **Create the `category-list` view.** Right-click `src` → `New` → `Vue Single-File Component` → `Composition API` → type `publishing/presentation/views/category-list` → Enter. Read-only for now: a sortable, paginated table of every category.

    ```javascript
    const store = usePublishingStore();
    const {categories, errors, categoriesLoaded} = storeToRefs(store);
    const {fetchCategories} = store;

    onMounted(() => {
      if (!categoriesLoaded.value) fetchCategories();
    });
    ```

    <details>
    <summary>src/publishing/presentation/views/category-list.vue (so far)</summary>

    ```vue
    <script setup>
    import {useI18n} from "vue-i18n";
    import usePublishingStore from "../../application/publishing.store.js";
    import {onMounted} from "vue";
    import {storeToRefs} from "pinia";

    const {t} = useI18n();
    const store = usePublishingStore();
    const {categories, errors, categoriesLoaded} = storeToRefs(store);
    const {fetchCategories} = store;

    onMounted(() => {
      if (!categoriesLoaded.value) fetchCategories();
    });
    </script>

    <template>
      <div class="p-4">
        <h1>{{ t('categories.title') }}</h1>
        <pv-data-table
            :loading="!categoriesLoaded"
            :rows="5"
            :rows-per-page-options="[5, 10, 20]"
            :value="categories"
            paginator
            striped-rows
            table-style="min-width: 50rem">
          <pv-column :header="t('categories.id')" field="id" sortable/>
          <pv-column :header="t('categories.name')" field="name" sortable/>
        </pv-data-table>
        <div v-if="errors.length" class="text-red-500 mt-3">
          {{ t('errors.occurred') }}: {{ errors.join(', ') }}
        </div>
      </div>
    </template>

    <style scoped>

    </style>
    ```
    </details>

    **Note:** `storeToRefs(store)` keeps `categories`, `errors`, and `categoriesLoaded` reactive; destructuring them straight off `store` (`const {categories} = store`) copies out today's value once and stops updating, because a Pinia setup store is a plain object, not a reactive wrapper by itself. `fetchCategories`, a function, is fine to destructure directly, functions do not need to stay reactive.

    **Note:** `onMounted`'s guard, `if (!categoriesLoaded.value)`, means navigating back to this view after visiting another one does not refetch, the store already has the data.

    ```
    git add .
    git commit -m "feat(publishing): add category-list view."
    ```

13. **Create the Publishing routes.** Right-click `src` → `New` → `JavaScript File` → type `publishing/presentation/publishing-routes` → Enter.

    <details>
    <summary>src/publishing/presentation/publishing-routes.js (so far)</summary>

    ```javascript
    const categoryList = () => import('./views/category-list.vue');

    const publishingRoutes = [
        {   path: 'categories', name: 'publishing-categories', component: categoryList, meta: {title: 'Categories'}}
    ];

    export default publishingRoutes;
    ```
    </details>

    ```
    git add .
    git commit -m "feat(publishing): add Publishing routes."
    ```

14. **Wire the `/publishing` route into `router.js`.**

    ```javascript
    import publishingRoutes from "./publishing/presentation/publishing-routes.js";

    { path: '/publishing', name: 'publishing', children: publishingRoutes },
    ```

    <details>
    <summary>src/router.js</summary>

    ```javascript
    import {createRouter, createWebHistory} from "vue-router";
    import Home from "./shared/presentation/views/home.vue";
    import publishingRoutes from "./publishing/presentation/publishing-routes.js";

    const about = () => import('./shared/presentation/views/about.vue');
    const pageNotFound = () => import('./shared/presentation/views/page-not-found.vue');
    const routes = [
        { path: '/home',            name: 'home',       component: Home,        meta: { title: 'Home' } },
        { path: '/about',           name: 'about',      component: about,       meta: { title: 'About' } },
        { path: '/publishing',      name: 'publishing', children: publishingRoutes },
        { path: '/',                redirect: '/home' },
        { path: '/:pathMatch(.*)*', name: 'not-found', component: pageNotFound, meta: { title: 'Page Not Found' } }
    ];

    const router = createRouter({
        history: createWebHistory(import.meta.env.BASE_URL),
        routes: routes
    });

    router.beforeEach((to) => {
        const baseTitle = 'ACME Learning Center';
        document.title = `${baseTitle} - ${to.meta['title']}`;
    });

    export default router;
    ```
    </details>

    ```
    git add .
    git commit -m "feat: wire the publishing routes into the router."
    ```

15. **Add a Categories option to `layout`, and the confirmation dialog it will need.** `<pv-confirm-dialog/>` renders once, here, and answers every `useConfirm()` prompt raised anywhere in the app, `category-list`'s delete confirmation included, built two stories from now.

    ```vue
    <pv-confirm-dialog/>
    ```

    ```javascript
    const items = [
      {label: 'option.home', to: '/home'},
      {label: 'option.about', to: '/about'},
      {label: 'option.categories', to: '/publishing/categories'}
    ];
    ```

    <details>
    <summary>src/shared/presentation/components/layout.vue</summary>

    ```vue
    <script setup>
    import LanguageSwitcher from "./language-switcher.vue";
    import {ref} from "vue";
    import {useI18n} from "vue-i18n";
    import FooterContent from "./footer-content.vue";

    const { t } = useI18n();

    const drawer = ref(false);

    const toggleDrawer = () => {
      drawer.value = !drawer.value;
    }

    const items = [
      {label: 'option.home', to: '/home'},
      {label: 'option.about', to: '/about'},
      {label: 'option.categories', to: '/publishing/categories'}
    ];
    </script>

    <template>
      <pv-confirm-dialog/>
      <header class="absolute top-0 left-0 w-full">
        <pv-toolbar class="bg-primary">
          <template #start>
            <pv-button class="p-button-text" icon="pi pi-bars" @click="toggleDrawer"/>
            <h3>ACME Learning Center</h3>
          </template>
          <template #end>
            <div class="flex-column mr-3">
              <pv-button v-for="item in items" :key="item.label" as-child v-slot="slotProps">
                <router-link :to="item.to" :class="slotProps['class']">{{ t(item.label) }}</router-link>
              </pv-button>
            </div>
            <language-switcher/>
          </template>
        </pv-toolbar>
        <pv-drawer v-model:visible="drawer"/>
      </header>
      <main class="mt-7">
        <router-view/>
      </main>
      <footer-content/>
    </template>
    ```
    </details>

    ```
    git add .
    git commit -m "feat(shared): add categories option to the layout navigation."
    ```

16. **Add the category texts to the English dictionary.** Open `src/locales/en.json`.

    <details>
    <summary>src/locales/en.json (so far)</summary>

    ```json
    {
      "option": {
        "home": "Home",
        "about": "About",
        "categories": "Categories"
      },
      "authoring-phrase": {
        "intro": "Made with",
        "use": "using",
        "author": "by {brand} Developer Team"
      },
      "about": {
        "title": "About Us",
        "content": "ACME Learning Center is an Education Business Platform, part of ACME Corporation."
      },
      "home": {
        "title": "Welcome",
        "content": "Welcome to ACME Learning Center."
      },
      "page-not-found": {
        "title": "Page Not Found",
        "content": "The path {unavailable-route} is not available.",
        "go-home": "Go Home"
      },
      "categories": {
        "title": "Categories",
        "id": "ID",
        "name": "Name"
      },
      "errors": {
        "occurred": "Errors occurred"
      }
    }
    ```
    </details>

    ```
    git add .
    git commit -m "feat(i18n): add category texts to the English dictionary."
    ```

17. **Add the category texts to the Spanish dictionary.** Open `src/locales/es.json`, same keys and the same structure.

    <details>
    <summary>src/locales/es.json (so far)</summary>

    ```json
    {
      "option": {
        "home": "Inicio",
        "about": "Acerca de",
        "categories": "Categorías"
      },
      "authoring-phrase": {
        "intro": "Hecho con",
        "use": "utilizando",
        "author": "por el Equipo de Desarrollo de {brand}"
      },
      "about": {
        "title": "Acerca de Nosotros",
        "content": "ACME Learning Center es una Plataforma educativa, parte de ACME Corporation."
      },
      "home": {
        "title": "Inicio",
        "content": "Bienvenido a ACME Learning Center."
      },
      "page-not-found": {
        "title": "Página no encontrada",
        "content": "La ruta {unavailable-route} no está disponible.",
        "go-home": "Ir al Inicio"
      },
      "categories": {
        "title": "Categorías",
        "id": "ID",
        "name": "Nombre"
      },
      "errors": {
        "occurred": "Ocurrieron errores"
      }
    }
    ```
    </details>

    ```
    git add .
    git commit -m "feat(i18n): add category texts to the Spanish dictionary."
    ```

18. **Run it.** In one terminal:

    ```
    npx json-server --watch server/db.json --routes server/routes.json --port 3000
    ```

    In another:

    ```
    npm run dev
    ```

    Open the local URL Vite prints, click `Categories`. The table shows the four seed categories, sortable by `ID` and `Name`, paginated. Stop both with `Ctrl+C`.

19. **Add `addCategory` to `usePublishingStore`.**

    ```javascript
    function addCategory(category) {
        publishingApi.createCategory(CategoryAssembler.toResourceFromEntity(category)).then(response => {
            const newCategory = CategoryAssembler.toEntityFromResource(response.data);
            categories.value = [...categories.value, newCategory];
        }).catch(error => {
            errors.value.push(error.message);
        });
    }
    ```

    <details>
    <summary>src/publishing/application/publishing.store.js (so far)</summary>

    ```javascript
    import {defineStore} from "pinia";
    import {computed, ref, shallowRef} from "vue";
    import {PublishingApi} from "../infrastructure/publishing-api.js";
    import {CategoryAssembler} from "../infrastructure/category.assembler.js";
    import {Category} from "../domain/model/category.entity.js";

    const publishingApi = new PublishingApi();

    const usePublishingStore = defineStore('publishing', () => {
        const categories = shallowRef([]);
        const errors = ref([]);
        const categoriesLoaded = ref(false);
        const categoriesCount = computed(() => {
            return categoriesLoaded.value ? categories.value.length : 0;
        });

        function fetchCategories() {
            publishingApi.getCategories().then(response => {
                categories.value = CategoryAssembler.toEntitiesFromResponse(response);
                categoriesLoaded.value = true;
            }).catch(error => {
                errors.value.push(error.message);
            });
        }

        function getCategoryById(id) {
            const idNum = parseInt(id);
            return categories.value.find(category => category.id === idNum);
        }

        function addCategory(category) {
            publishingApi.createCategory(CategoryAssembler.toResourceFromEntity(category)).then(response => {
                const newCategory = CategoryAssembler.toEntityFromResource(response.data);
                categories.value = [...categories.value, newCategory];
            }).catch(error => {
                errors.value.push(error.message);
            });
        }

        return {
            categories,
            errors,
            categoriesLoaded,
            categoriesCount,
            fetchCategories,
            getCategoryById,
            addCategory
        }
    });

    export default usePublishingStore;
    ```
    </details>

    **Note:** `categories.value = [...categories.value, newCategory]` builds a new array with the created category appended and assigns it, it never pushes onto the existing one. A `shallowRef` only notices a new reference, `categories.value.push(newCategory)` would change the list on screen not at all.

    ```
    git add .
    git commit -m "feat(publishing): add create category method to usePublishingStore."
    ```

20. **Register `pv-input-text` in `main.js`.** `category-form`'s only field needs it.

    ```javascript
    createApp(App)
        .component('pv-input-text', InputText)
    ```

    <details>
    <summary>src/main.js (so far)</summary>

    ```javascript
    import {createApp} from 'vue'
    import './style.css'
    import App from './app.vue'
    import i18n from "./i18n.js";
    import PrimeVue from 'primevue/config';
    import Material from '@primeuix/themes/material';
    import 'primeflex/primeflex.css';
    import 'primeicons/primeicons.css';
    import {
        Button,
        Column,
        ConfirmationService,
        ConfirmDialog,
        DataTable,
        Drawer,
        InputText,
        SelectButton,
        Toolbar
    } from "primevue";
    import router from "./router.js";
    import pinia from "./pinia.js";

    const primeUiLicenseKey = import.meta.env.VITE_PRIME_UI_LICENSE_KEY;

    createApp(App)
        .use(i18n)
        .use(PrimeVue, {theme: {preset: Material}, ripple: true, license: primeUiLicenseKey})
        .use(ConfirmationService)
        .component('pv-button',         Button)
        .component('pv-column',         Column)
        .component('pv-confirm-dialog', ConfirmDialog)
        .component('pv-data-table',     DataTable)
        .component('pv-drawer',         Drawer)
        .component('pv-input-text',     InputText)
        .component('pv-select-button',  SelectButton)
        .component('pv-toolbar',        Toolbar)
        .use(router)
        .use(pinia)
        .mount('#app')
    ```
    </details>

    ```
    git add .
    git commit -m "feat: register the PrimeVue input text."
    ```

21. **Create the `category-form` view, create mode.** Right-click `src` → `New` → `Vue Single-File Component` → `Composition API` → type `publishing/presentation/views/category-form` → Enter. One field, one `Category` built from it, saved through the store.

    <details>
    <summary>src/publishing/presentation/views/category-form.vue (so far)</summary>

    ```vue
    <script setup>
    import {useI18n} from "vue-i18n";
    import {useRouter} from "vue-router";
    import usePublishingStore from "../../application/publishing.store.js";
    import {ref} from "vue";
    import {storeToRefs} from "pinia";
    import {Category} from "../../domain/model/category.entity.js";

    const {t} = useI18n();
    const router = useRouter();
    const store = usePublishingStore();
    const {errors} = storeToRefs(store);
    const {addCategory} = store;

    const form = ref({name: ''});

    const saveCategory = () => {
      const category = new Category({id: null, name: form.value.name});
      addCategory(category);
      navigateBack();
    };

    const navigateBack = () => {
      router.push({name: 'publishing-categories'});
    };
    </script>

    <template>
      <div class="p-4">
        <h1>{{ t('category.new-title') }}</h1>
        <form @submit.prevent="saveCategory">
          <div class="field mb-3">
            <label for="name">{{ t('category.name') }}</label>
            <pv-input-text id="name" v-model="form.name" class="w-full" required/>
          </div>
          <pv-button :label="t('category.save')" icon="pi pi-save" type="submit"/>
          <pv-button :label="t('category.cancel')" class="ml-2" severity="secondary" @click="navigateBack"/>
        </form>
        <div v-if="errors.length" class="text-red-500 mt-3">
          {{ t('errors.occurred') }}: {{ errors.join(', ') }}
        </div>
      </div>
    </template>

    <style scoped>

    </style>
    ```
    </details>

    ```
    git add .
    git commit -m "feat(publishing): add category-form view."
    ```

22. **Add the `categories/new` route.**

    ```javascript
    const categoryForm = () => import('./views/category-form.vue');

    {   path: 'categories/new', name: 'publishing-category-new', component: categoryForm, meta: {title: 'New Category'}}
    ```

    <details>
    <summary>src/publishing/presentation/publishing-routes.js (so far)</summary>

    ```javascript
    const categoryList = () => import('./views/category-list.vue');
    const categoryForm = () => import('./views/category-form.vue');

    const publishingRoutes = [
        {   path: 'categories',     name: 'publishing-categories',   component: categoryList, meta: {title: 'Categories'}},
        {   path: 'categories/new', name: 'publishing-category-new', component: categoryForm, meta: {title: 'New Category'}}
    ];

    export default publishingRoutes;
    ```
    </details>

    ```
    git add .
    git commit -m "feat(publishing): add route for creating a new category."
    ```

23. **Add a "New Category" button to `category-list`.**

    ```javascript
    import {useRouter} from "vue-router";

    const router = useRouter();

    const navigateToNew = () => {
      router.push({name: 'publishing-category-new'});
    };
    ```

    ```vue
    <pv-button :label="t('categories.new')" class="mb-3" icon="pi pi-plus" @click="navigateToNew"/>
    ```

    <details>
    <summary>src/publishing/presentation/views/category-list.vue (so far)</summary>

    ```vue
    <script setup>
    import {useI18n} from "vue-i18n";
    import {useRouter} from "vue-router";
    import usePublishingStore from "../../application/publishing.store.js";
    import {onMounted} from "vue";
    import {storeToRefs} from "pinia";

    const {t} = useI18n();
    const router = useRouter();
    const store = usePublishingStore();
    const {categories, errors, categoriesLoaded} = storeToRefs(store);
    const {fetchCategories} = store;

    onMounted(() => {
      if (!categoriesLoaded.value) fetchCategories();
    });

    const navigateToNew = () => {
      router.push({name: 'publishing-category-new'});
    };
    </script>

    <template>
      <div class="p-4">
        <h1>{{ t('categories.title') }}</h1>
        <pv-button :label="t('categories.new')" class="mb-3" icon="pi pi-plus" @click="navigateToNew"/>
        <pv-data-table
            :loading="!categoriesLoaded"
            :rows="5"
            :rows-per-page-options="[5, 10, 20]"
            :value="categories"
            paginator
            striped-rows
            table-style="min-width: 50rem">
          <pv-column :header="t('categories.id')" field="id" sortable/>
          <pv-column :header="t('categories.name')" field="name" sortable/>
        </pv-data-table>
        <div v-if="errors.length" class="text-red-500 mt-3">
          {{ t('errors.occurred') }}: {{ errors.join(', ') }}
        </div>
      </div>
    </template>

    <style scoped>

    </style>
    ```
    </details>

    ```
    git add .
    git commit -m "feat(publishing): add new category navigation button to category-list."
    ```

24. **Add the create-category texts to the English dictionary.**

    <details>
    <summary>src/locales/en.json (so far)</summary>

    ```json
    {
      "option": {
        "home": "Home",
        "about": "About",
        "categories": "Categories"
      },
      "authoring-phrase": {
        "intro": "Made with",
        "use": "using",
        "author": "by {brand} Developer Team"
      },
      "about": {
        "title": "About Us",
        "content": "ACME Learning Center is an Education Business Platform, part of ACME Corporation."
      },
      "home": {
        "title": "Welcome",
        "content": "Welcome to ACME Learning Center."
      },
      "page-not-found": {
        "title": "Page Not Found",
        "content": "The path {unavailable-route} is not available.",
        "go-home": "Go Home"
      },
      "categories": {
        "title": "Categories",
        "id": "ID",
        "name": "Name",
        "new": "New Category"
      },
      "category": {
        "new-title": "Create New Category",
        "name": "Name",
        "save": "Save",
        "cancel": "Cancel"
      },
      "errors": {
        "occurred": "Errors occurred"
      }
    }
    ```
    </details>

    ```
    git add .
    git commit -m "feat(i18n): add create category texts to the English dictionary."
    ```

25. **Add the create-category texts to the Spanish dictionary.**

    <details>
    <summary>src/locales/es.json (so far)</summary>

    ```json
    {
      "option": {
        "home": "Inicio",
        "about": "Acerca de",
        "categories": "Categorías"
      },
      "authoring-phrase": {
        "intro": "Hecho con",
        "use": "utilizando",
        "author": "por el Equipo de Desarrollo de {brand}"
      },
      "about": {
        "title": "Acerca de Nosotros",
        "content": "ACME Learning Center es una Plataforma educativa, parte de ACME Corporation."
      },
      "home": {
        "title": "Inicio",
        "content": "Bienvenido a ACME Learning Center."
      },
      "page-not-found": {
        "title": "Página no encontrada",
        "content": "La ruta {unavailable-route} no está disponible.",
        "go-home": "Ir al Inicio"
      },
      "categories": {
        "title": "Categorías",
        "id": "ID",
        "name": "Nombre",
        "new": "Nueva Categoría"
      },
      "category": {
        "new-title": "Crear Nueva Categoría",
        "name": "Nombre",
        "save": "Guardar",
        "cancel": "Cancelar"
      },
      "errors": {
        "occurred": "Ocurrieron errores"
      }
    }
    ```
    </details>

    ```
    git add .
    git commit -m "feat(i18n): add create category texts to the Spanish dictionary."
    ```

26. **Run it.** Start the fake API and `npm run dev` as in step 18. Click `Categories` → `New Category`, type a name, `Save`: the list shows it, with the id `json-server` assigned. Stop both with `Ctrl+C`.

27. **Add `updateCategory` to `usePublishingStore`.**

    ```javascript
    function updateCategory(category) {
        publishingApi.updateCategory(CategoryAssembler.toResourceFromEntity(category)).then(response => {
            const updatedCategory = CategoryAssembler.toEntityFromResource(response.data);
            categories.value = categories.value.map(c => c.id === updatedCategory.id ? updatedCategory : c);
        }).catch(error => {
            errors.value.push(error.message);
        });
    }
    ```

    <details>
    <summary>src/publishing/application/publishing.store.js (so far)</summary>

    ```javascript
    import {defineStore} from "pinia";
    import {computed, ref, shallowRef} from "vue";
    import {PublishingApi} from "../infrastructure/publishing-api.js";
    import {CategoryAssembler} from "../infrastructure/category.assembler.js";
    import {Category} from "../domain/model/category.entity.js";

    const publishingApi = new PublishingApi();

    const usePublishingStore = defineStore('publishing', () => {
        const categories = shallowRef([]);
        const errors = ref([]);
        const categoriesLoaded = ref(false);
        const categoriesCount = computed(() => {
            return categoriesLoaded.value ? categories.value.length : 0;
        });

        function fetchCategories() {
            publishingApi.getCategories().then(response => {
                categories.value = CategoryAssembler.toEntitiesFromResponse(response);
                categoriesLoaded.value = true;
            }).catch(error => {
                errors.value.push(error.message);
            });
        }

        function getCategoryById(id) {
            const idNum = parseInt(id);
            return categories.value.find(category => category.id === idNum);
        }

        function addCategory(category) {
            publishingApi.createCategory(CategoryAssembler.toResourceFromEntity(category)).then(response => {
                const newCategory = CategoryAssembler.toEntityFromResource(response.data);
                categories.value = [...categories.value, newCategory];
            }).catch(error => {
                errors.value.push(error.message);
            });
        }

        function updateCategory(category) {
            publishingApi.updateCategory(CategoryAssembler.toResourceFromEntity(category)).then(response => {
                const updatedCategory = CategoryAssembler.toEntityFromResource(response.data);
                categories.value = categories.value.map(c => c.id === updatedCategory.id ? updatedCategory : c);
            }).catch(error => {
                errors.value.push(error.message);
            });
        }

        return {
            categories,
            errors,
            categoriesLoaded,
            categoriesCount,
            fetchCategories,
            getCategoryById,
            addCategory,
            updateCategory
        }
    });

    export default usePublishingStore;
    ```
    </details>

    ```
    git add .
    git commit -m "feat(publishing): add update category method to usePublishingStore."
    ```

28. **Support edit mode in `category-form`.** A route parameter, `route.params.id`, switches the view between the two modes.

    ```javascript
    import {useRoute, useRouter} from "vue-router";
    import {computed, onMounted, ref} from "vue";

    const route = useRoute();
    const isEdit = computed(() => !!route.params.id);

    onMounted(() => {
      if (isEdit.value) {
        const category = getCategoryById(route.params.id);
        if (category) form.value.name = category.name; else router.push({name: 'publishing-categories'});
      }
    });

    function getCategoryById(id) {
      return store.getCategoryById(id);
    }
    ```

    <details>
    <summary>src/publishing/presentation/views/category-form.vue (Full file)</summary>

    ```vue
    <script setup>
    import {useI18n} from "vue-i18n";
    import {useRoute, useRouter} from "vue-router";
    import usePublishingStore from "../../application/publishing.store.js";
    import {computed, onMounted, ref} from "vue";
    import {storeToRefs} from "pinia";
    import {Category} from "../../domain/model/category.entity.js";

    const {t} = useI18n();
    const route = useRoute();
    const router = useRouter();
    const store = usePublishingStore();
    const {errors} = storeToRefs(store);
    const {addCategory, updateCategory} = store;

    const form = ref({name: ''});
    const isEdit = computed(() => !!route.params.id);

    onMounted(() => {
      if (isEdit.value) {
        const category = getCategoryById(route.params.id);
        if (category) form.value.name = category.name; else router.push({name: 'publishing-categories'});
      }
    });

    function getCategoryById(id) {
      return store.getCategoryById(id);
    }

    const saveCategory = () => {
      const category = new Category({
        id: isEdit.value ? Number(route.params.id) : null,
        name: form.value.name,
      });
      if (isEdit.value) updateCategory(category); else addCategory(category);
      navigateBack();
    };

    const navigateBack = () => {
      router.push({name: 'publishing-categories'});
    };
    </script>

    <template>
      <div class="p-4">
        <h1>{{ isEdit ? t('category.edit-title') : t('category.new-title') }}</h1>
        <form @submit.prevent="saveCategory">
          <div class="field mb-3">
            <label for="name">{{ t('category.name') }}</label>
            <pv-input-text id="name" v-model="form.name" class="w-full" required/>
          </div>
          <pv-button :label="t('category.save')" icon="pi pi-save" type="submit"/>
          <pv-button :label="t('category.cancel')" class="ml-2" severity="secondary" @click="navigateBack"/>
        </form>
        <div v-if="errors.length" class="text-red-500 mt-3">
          {{ t('errors.occurred') }}: {{ errors.join(', ') }}
        </div>
      </div>
    </template>

    <style scoped>

    </style>
    ```
    </details>

    **Note:** `route.params.id` is always a string, `vue-router` never parses it. `getCategoryById` in the store already runs it through `parseInt`, but the `Category` this view builds to *save* needs a real number, that is what `Number(route.params.id)` is for, `json-server` matches ids by value, not by string-vs-number.

    ```
    git add .
    git commit -m "refactor(publishing): support edit mode in category-form."
    ```

29. **Add the `categories/:id/edit` route.**

    ```javascript
    {   path: 'categories/:id/edit', name: 'publishing-category-edit', component: categoryForm, meta: {title: 'Edit Category'}}
    ```

    <details>
    <summary>src/publishing/presentation/publishing-routes.js (so far)</summary>

    ```javascript
    const categoryList = () => import('./views/category-list.vue');
    const categoryForm = () => import('./views/category-form.vue');

    const publishingRoutes = [
        {   path: 'categories',          name: 'publishing-categories',    component: categoryList, meta: {title: 'Categories'}},
        {   path: 'categories/new',      name: 'publishing-category-new',  component: categoryForm, meta: {title: 'New Category'}},
        {   path: 'categories/:id/edit', name: 'publishing-category-edit', component: categoryForm, meta: {title: 'Edit Category'}}
    ];

    export default publishingRoutes;
    ```
    </details>

    ```
    git add .
    git commit -m "feat(publishing): add route for editing a category."
    ```

30. **Add an edit action to `category-list`.**

    ```javascript
    const navigateToEdit = (id) => {
      router.push({name: 'publishing-category-edit', params: {id}});
    };
    ```

    ```vue
    <pv-column :header="t('categories.actions')">
      <template #body="slotProps">
        <pv-button icon="pi pi-pencil" rounded text @click="navigateToEdit(slotProps.data.id)"/>
      </template>
    </pv-column>
    ```

    <details>
    <summary>src/publishing/presentation/views/category-list.vue (so far)</summary>

    ```vue
    <script setup>
    import {useI18n} from "vue-i18n";
    import {useRouter} from "vue-router";
    import usePublishingStore from "../../application/publishing.store.js";
    import {onMounted} from "vue";
    import {storeToRefs} from "pinia";

    const {t} = useI18n();
    const router = useRouter();
    const store = usePublishingStore();
    const {categories, errors, categoriesLoaded} = storeToRefs(store);
    const {fetchCategories} = store;

    onMounted(() => {
      if (!categoriesLoaded.value) fetchCategories();
    });

    const navigateToNew = () => {
      router.push({name: 'publishing-category-new'});
    };

    const navigateToEdit = (id) => {
      router.push({name: 'publishing-category-edit', params: {id}});
    };
    </script>

    <template>
      <div class="p-4">
        <h1>{{ t('categories.title') }}</h1>
        <pv-button :label="t('categories.new')" class="mb-3" icon="pi pi-plus" @click="navigateToNew"/>
        <pv-data-table
            :loading="!categoriesLoaded"
            :rows="5"
            :rows-per-page-options="[5, 10, 20]"
            :value="categories"
            paginator
            striped-rows
            table-style="min-width: 50rem">
          <pv-column :header="t('categories.id')" field="id" sortable/>
          <pv-column :header="t('categories.name')" field="name" sortable/>
          <pv-column :header="t('categories.actions')">
            <template #body="slotProps">
              <pv-button icon="pi pi-pencil" rounded text @click="navigateToEdit(slotProps.data.id)"/>
            </template>
          </pv-column>
        </pv-data-table>
        <div v-if="errors.length" class="text-red-500 mt-3">
          {{ t('errors.occurred') }}: {{ errors.join(', ') }}
        </div>
      </div>
    </template>

    <style scoped>

    </style>
    ```
    </details>

    ```
    git add .
    git commit -m "feat(publishing): add edit action to category-list."
    ```

31. **Add `category.edit-title` and `categories.actions` to the English dictionary.**

    <details>
    <summary>src/locales/en.json (so far)</summary>

    ```json
    {
      "option": {
        "home": "Home",
        "about": "About",
        "categories": "Categories"
      },
      "authoring-phrase": {
        "intro": "Made with",
        "use": "using",
        "author": "by {brand} Developer Team"
      },
      "about": {
        "title": "About Us",
        "content": "ACME Learning Center is an Education Business Platform, part of ACME Corporation."
      },
      "home": {
        "title": "Welcome",
        "content": "Welcome to ACME Learning Center."
      },
      "page-not-found": {
        "title": "Page Not Found",
        "content": "The path {unavailable-route} is not available.",
        "go-home": "Go Home"
      },
      "categories": {
        "title": "Categories",
        "id": "ID",
        "name": "Name",
        "actions": "Actions",
        "new": "New Category"
      },
      "category": {
        "new-title": "Create New Category",
        "edit-title": "Edit Category",
        "name": "Name",
        "save": "Save",
        "cancel": "Cancel"
      },
      "errors": {
        "occurred": "Errors occurred"
      }
    }
    ```
    </details>

    ```
    git add .
    git commit -m "feat(i18n): add edit category texts to the English dictionary."
    ```

32. **Add the same keys to the Spanish dictionary.**

    <details>
    <summary>src/locales/es.json (so far)</summary>

    ```json
    {
      "option": {
        "home": "Inicio",
        "about": "Acerca de",
        "categories": "Categorías"
      },
      "authoring-phrase": {
        "intro": "Hecho con",
        "use": "utilizando",
        "author": "por el Equipo de Desarrollo de {brand}"
      },
      "about": {
        "title": "Acerca de Nosotros",
        "content": "ACME Learning Center es una Plataforma educativa, parte de ACME Corporation."
      },
      "home": {
        "title": "Inicio",
        "content": "Bienvenido a ACME Learning Center."
      },
      "page-not-found": {
        "title": "Página no encontrada",
        "content": "La ruta {unavailable-route} no está disponible.",
        "go-home": "Ir al Inicio"
      },
      "categories": {
        "title": "Categorías",
        "id": "ID",
        "name": "Nombre",
        "actions": "Acciones",
        "new": "Nueva Categoría"
      },
      "category": {
        "new-title": "Crear Nueva Categoría",
        "edit-title": "Editar Categoría",
        "name": "Nombre",
        "save": "Guardar",
        "cancel": "Cancelar"
      },
      "errors": {
        "occurred": "Ocurrieron errores"
      }
    }
    ```
    </details>

    ```
    git add .
    git commit -m "feat(i18n): add edit category texts to the Spanish dictionary."
    ```

33. **Run it.** Click the pencil icon on a row: the form opens pre-filled, `Save` updates the same row in place. Stop both servers with `Ctrl+C`.

34. **Add `deleteCategory` to `usePublishingStore`.**

    ```javascript
    function deleteCategory(category) {
        publishingApi.deleteCategory(category.id).then(() => {
            categories.value = categories.value.filter(c => c.id !== category.id);
        }).catch(error => {
            errors.value.push(error.message);
        });
    }
    ```

    <details>
    <summary>src/publishing/application/publishing.store.js (so far)</summary>

    ```javascript
    import {defineStore} from "pinia";
    import {computed, ref, shallowRef} from "vue";
    import {PublishingApi} from "../infrastructure/publishing-api.js";
    import {CategoryAssembler} from "../infrastructure/category.assembler.js";
    import {Category} from "../domain/model/category.entity.js";

    const publishingApi = new PublishingApi();

    const usePublishingStore = defineStore('publishing', () => {
        const categories = shallowRef([]);
        const errors = ref([]);
        const categoriesLoaded = ref(false);
        const categoriesCount = computed(() => {
            return categoriesLoaded.value ? categories.value.length : 0;
        });

        function fetchCategories() {
            publishingApi.getCategories().then(response => {
                categories.value = CategoryAssembler.toEntitiesFromResponse(response);
                categoriesLoaded.value = true;
            }).catch(error => {
                errors.value.push(error.message);
            });
        }

        function getCategoryById(id) {
            const idNum = parseInt(id);
            return categories.value.find(category => category.id === idNum);
        }

        function addCategory(category) {
            publishingApi.createCategory(CategoryAssembler.toResourceFromEntity(category)).then(response => {
                const newCategory = CategoryAssembler.toEntityFromResource(response.data);
                categories.value = [...categories.value, newCategory];
            }).catch(error => {
                errors.value.push(error.message);
            });
        }

        function updateCategory(category) {
            publishingApi.updateCategory(CategoryAssembler.toResourceFromEntity(category)).then(response => {
                const updatedCategory = CategoryAssembler.toEntityFromResource(response.data);
                categories.value = categories.value.map(c => c.id === updatedCategory.id ? updatedCategory : c);
            }).catch(error => {
                errors.value.push(error.message);
            });
        }

        function deleteCategory(category) {
            publishingApi.deleteCategory(category.id).then(() => {
                categories.value = categories.value.filter(c => c.id !== category.id);
            }).catch(error => {
                errors.value.push(error.message);
            });
        }

        return {
            categories,
            errors,
            categoriesLoaded,
            categoriesCount,
            fetchCategories,
            getCategoryById,
            addCategory,
            updateCategory,
            deleteCategory
        }
    });

    export default usePublishingStore;
    ```
    </details>

    ```
    git add .
    git commit -m "feat(publishing): add delete category method to usePublishingStore."
    ```

35. **Add a delete action to `category-list`, with a confirmation prompt.** `useConfirm()` raises the prompt, `<pv-confirm-dialog/>` in `layout` (step 15) is what actually renders it.

    ```javascript
    import {useConfirm} from "primevue";

    const confirm = useConfirm();
    const {deleteCategory} = store;

    const confirmDelete = (category) => {
      confirm.require({
        message: t('categories.confirm-delete', {name: category.name}),
        header: t('categories.delete-header'),
        icon: 'pi pi-exclamation-triangle',
        accept: () => {
          deleteCategory(category);
        },
      });
    };
    ```

    <details>
    <summary>src/publishing/presentation/views/category-list.vue (Full file)</summary>

    ```vue
    <script setup>
    import {useI18n} from "vue-i18n";
    import {useRouter} from "vue-router";
    import {useConfirm} from "primevue";
    import usePublishingStore from "../../application/publishing.store.js";
    import {onMounted} from "vue";
    import {storeToRefs} from "pinia";

    const {t} = useI18n();
    const router = useRouter();
    const confirm = useConfirm();
    const store = usePublishingStore();
    const {categories, errors, categoriesLoaded} = storeToRefs(store);
    const {fetchCategories, deleteCategory} = store;

    onMounted(() => {
      if (!categoriesLoaded.value) fetchCategories();
    });

    const navigateToNew = () => {
      router.push({name: 'publishing-category-new'});
    };

    const navigateToEdit = (id) => {
      router.push({name: 'publishing-category-edit', params: {id}});
    };

    const confirmDelete = (category) => {
      confirm.require({
        message: t('categories.confirm-delete', {name: category.name}),
        header: t('categories.delete-header'),
        icon: 'pi pi-exclamation-triangle',
        accept: () => {
          deleteCategory(category);
        },
      });
    };
    </script>

    <template>
      <div class="p-4">
        <h1>{{ t('categories.title') }}</h1>
        <pv-button :label="t('categories.new')" class="mb-3" icon="pi pi-plus" @click="navigateToNew"/>
        <pv-data-table
            :loading="!categoriesLoaded"
            :rows="5"
            :rows-per-page-options="[5, 10, 20]"
            :value="categories"
            paginator
            striped-rows
            table-style="min-width: 50rem">
          <pv-column :header="t('categories.id')" field="id" sortable/>
          <pv-column :header="t('categories.name')" field="name" sortable/>
          <pv-column :header="t('categories.actions')">
            <template #body="slotProps">
              <pv-button icon="pi pi-pencil" rounded text @click="navigateToEdit(slotProps.data.id)"/>
              <pv-button icon="pi pi-trash" rounded severity="danger" text @click="confirmDelete(slotProps.data)"/>
            </template>
          </pv-column>
        </pv-data-table>
        <div v-if="errors.length" class="text-red-500 mt-3">
          {{ t('errors.occurred') }}: {{ errors.join(', ') }}
        </div>
      </div>
    </template>

    <style scoped>

    </style>
    ```
    </details>

    ```
    git add .
    git commit -m "feat(publishing): add delete action to category-list."
    ```

36. **Add `categories.confirm-delete` and `categories.delete-header` to the English dictionary.**

    <details>
    <summary>src/locales/en.json</summary>

    ```json
    {
      "option": {
        "home": "Home",
        "about": "About",
        "categories": "Categories"
      },
      "authoring-phrase": {
        "intro": "Made with",
        "use": "using",
        "author": "by {brand} Developer Team"
      },
      "about": {
        "title": "About Us",
        "content": "ACME Learning Center is an Education Business Platform, part of ACME Corporation."
      },
      "home": {
        "title": "Welcome",
        "content": "Welcome to ACME Learning Center."
      },
      "page-not-found": {
        "title": "Page Not Found",
        "content": "The path {unavailable-route} is not available.",
        "go-home": "Go Home"
      },
      "categories": {
        "title": "Categories",
        "id": "ID",
        "name": "Name",
        "actions": "Actions",
        "new": "New Category",
        "confirm-delete": "Are you sure you want to delete {name}?",
        "delete-header": "Confirm Deletion"
      },
      "category": {
        "new-title": "Create New Category",
        "edit-title": "Edit Category",
        "name": "Name",
        "save": "Save",
        "cancel": "Cancel"
      },
      "errors": {
        "occurred": "Errors occurred"
      }
    }
    ```
    </details>

    ```
    git add .
    git commit -m "feat(i18n): add delete category texts to the English dictionary."
    ```

37. **Add the same keys to the Spanish dictionary.**

    <details>
    <summary>src/locales/es.json</summary>

    ```json
    {
      "option": {
        "home": "Inicio",
        "about": "Acerca de",
        "categories": "Categorías"
      },
      "authoring-phrase": {
        "intro": "Hecho con",
        "use": "utilizando",
        "author": "por el Equipo de Desarrollo de {brand}"
      },
      "about": {
        "title": "Acerca de Nosotros",
        "content": "ACME Learning Center es una Plataforma educativa, parte de ACME Corporation."
      },
      "home": {
        "title": "Inicio",
        "content": "Bienvenido a ACME Learning Center."
      },
      "page-not-found": {
        "title": "Página no encontrada",
        "content": "La ruta {unavailable-route} no está disponible.",
        "go-home": "Ir al Inicio"
      },
      "categories": {
        "title": "Categorías",
        "id": "ID",
        "name": "Nombre",
        "actions": "Acciones",
        "new": "Nueva Categoría",
        "confirm-delete": "¿Estás seguro de que quieres eliminar {name}?",
        "delete-header": "Confirmar Eliminación"
      },
      "category": {
        "new-title": "Crear Nueva Categoría",
        "edit-title": "Editar Categoría",
        "name": "Nombre",
        "save": "Guardar",
        "cancel": "Cancelar"
      },
      "errors": {
        "occurred": "Ocurrieron errores"
      }
    }
    ```
    </details>

    ```
    git add .
    git commit -m "feat(i18n): add delete category texts to the Spanish dictionary."
    ```

38. **Run it.** Click the trash icon: a dialog asks to confirm, naming the category. Accept: the row disappears. Cancel: nothing happens. Stop both servers with `Ctrl+C`.

    **Note:** `--watch` writes every create, update, and delete back into `server/db.json`, so the file now differs from what git tracked. Restore it before your next commit that touches it:

    ```
    git checkout server/db.json
    ```

39. **Publish and finish the feature.**

---

## Manage Tutorials (US002)

A Tutorial Author manages the tutorial catalog: sees every tutorial with its category, creates one, edits it, and deletes it. This story follows the exact shape of `Manage Categories`, `Tutorial` instead of `Category`, `tutorial-list`/`tutorial-form` instead of `category-list`/`category-form`, and one addition neither needed: a tutorial carries an object reference to its category, not just the foreign key, and the store has to keep that reference in sync whenever a category changes.

1. **Start the feature `manage-tutorials`.**

2. **See the component tree this story builds.** `tutorial-list` and `tutorial-form` join the routed views, and `layout` gets a `Tutorials` option next to `Categories`.

   ```
   +-----+
   | app |
   +-----+
       |
       +-----------------------------+
       | layout                      |
       | State: drawer: Ref<boolean> |
       | (no Input/Output)           |
       +-----------------------------+
           |
           +--------------------------------------------+
           | language-switcher                          |
           | (no Input/Output, uses useI18n() directly) |
           +--------------------------------------------+
           |
           +-----------------------------------------------------------+
           | <router-view/>  one routed view at a time                 |
           | home, about, page-not-found, category-list, category-form, |
           | tutorial-list, tutorial-form                              |
           | (no Input/Output, tutorial-list and tutorial-form use     |
           |  usePublishingStore() directly)                           |
           +-----------------------------------------------------------+
           |
           +--------------------------------------------+
           | footer-content                             |
           | (no Input/Output, uses useI18n() directly) |
           +--------------------------------------------+
   ```

3. **Create the `Tutorial` entity, fields and constructor.** Right-click `src` → `New` → `JavaScript File` → type `publishing/domain/model/tutorial.entity` → Enter. A tutorial holds `categoryId`, the foreign key `json-server` stores, and `category`, an optional `Category` reference the store fills in below.

   <details>
   <summary>src/publishing/domain/model/tutorial.entity.js (so far)</summary>

   ```javascript
   import {Category} from "./category.entity.js";

   export class Tutorial {
       #id;
       #title;
       #summary;
       #categoryId;
       #category;

       constructor({id = null, title = '', summary = '', categoryId = null, category = null}) {
           this.#id = id;
           this.#title = title;
           this.#summary = summary;
           this.#categoryId = categoryId;
           this.#category = category instanceof Category ? category : null;
       }
   }
   ```
   </details>

   **Note:** `category instanceof Category ? category : null` guards the field, a `Tutorial` built from a plain resource (no `category` key at all) ends up with `category: null`, never a stray object that only looks like a `Category`.

   **Note:** no commit here, `Tutorial` still needs its read accessors.

4. **Add the read accessors.**

   <details>
   <summary>src/publishing/domain/model/tutorial.entity.js (Full file)</summary>

   ```javascript
   import {Category} from "./category.entity.js";

   export class Tutorial {
       #id;
       #title;
       #summary;
       #categoryId;
       #category;

       constructor({id = null, title = '', summary = '', categoryId = null, category = null}) {
           this.#id = id;
           this.#title = title;
           this.#summary = summary;
           this.#categoryId = categoryId;
           this.#category = category instanceof Category ? category : null;
       }

       get id() {
           return this.#id;
       }

       get title() {
           return this.#title;
       }

       get summary() {
           return this.#summary;
       }

       get categoryId() {
           return this.#categoryId;
       }

       get category() {
           return this.#category;
       }
   }
   ```
   </details>

   ```
   git add .
   git commit -m "feat(publishing): add Tutorial entity."
   ```

5. **Create the `TutorialAssembler`.** Right-click `src` → `New` → `JavaScript File` → type `publishing/infrastructure/tutorial.assembler` → Enter. Same shape as `CategoryAssembler`.

   <details>
   <summary>src/publishing/infrastructure/tutorial.assembler.js</summary>

   ```javascript
   import {Tutorial} from "../domain/model/tutorial.entity.js";

   export class TutorialAssembler {
       static toEntityFromResource(resource) {
           return new Tutorial({...resource})
       }

       static toResourceFromEntity(entity) {
           return {id: entity.id, title: entity.title, summary: entity.summary, categoryId: entity.categoryId};
       }

       static toEntitiesFromResponse(response) {
           if (response.status !== 200) {
               console.error(`${response.status}, ${response.statusText}`);
               return [];
           }
           let resources = response.data instanceof Array ? response.data : response.data['tutorials'];

           return resources.map(resource => this.toEntityFromResource(resource));
       }
   }
   ```
   </details>

   **Note:** the resource carries `categoryId`, a plain number, not `category`. `TutorialAssembler` only ever sees the tutorial's own resource, it has no way to look a category up; that is the store's job, in the `withCategory` helper two steps from now.

   ```
   git add .
   git commit -m "feat(publishing): add TutorialAssembler."
   ```

6. **Add tutorials to `PublishingApi`.** One more `BaseEndpoint`, the same five methods, for the tutorials resource.

   ```javascript
   const tutorialsEndpointPath = import.meta.env.VITE_TUTORIALS_ENDPOINT_PATH;

   #tutorialsEndpoint;

   this.#tutorialsEndpoint = new BaseEndpoint(this, tutorialsEndpointPath);

   getTutorials() {
       return this.#tutorialsEndpoint.getAll();
   }

   getTutorialById(id) {
       return this.#tutorialsEndpoint.getById(id);
   }

   createTutorial(resource) {
       return this.#tutorialsEndpoint.create(resource);
   }

   updateTutorial(resource) {
       return this.#tutorialsEndpoint.update(resource.id, resource);
   }

   deleteTutorial(id) {
       return this.#tutorialsEndpoint.delete(id);
   }
   ```

   <details>
   <summary>src/publishing/infrastructure/publishing-api.js (Full file)</summary>

   ```javascript
   import {BaseApi} from "../../shared/infrastructure/base-api.js";
   import {BaseEndpoint} from "../../shared/infrastructure/base-endpoint.js";

   const categoriesEndpointPath = import.meta.env.VITE_CATEGORIES_ENDPOINT_PATH;
   const tutorialsEndpointPath = import.meta.env.VITE_TUTORIALS_ENDPOINT_PATH;

   export class PublishingApi extends BaseApi {
       #categoriesEndpoint;
       #tutorialsEndpoint;

       constructor() {
           super();
           this.#categoriesEndpoint = new BaseEndpoint(this, categoriesEndpointPath);
           this.#tutorialsEndpoint = new BaseEndpoint(this, tutorialsEndpointPath);
       }

       getCategories() {
           return this.#categoriesEndpoint.getAll();
       }

       getCategoryById(id) {
           return this.#categoriesEndpoint.getById(id);
       }

       createCategory(resource) {
           return this.#categoriesEndpoint.create(resource);
       }

       updateCategory(resource) {
           return this.#categoriesEndpoint.update(resource.id, resource);
       }

       deleteCategory(id) {
           return this.#categoriesEndpoint.delete(id);
       }

       getTutorials() {
           return this.#tutorialsEndpoint.getAll();
       }

       getTutorialById(id) {
           return this.#tutorialsEndpoint.getById(id);
       }

       createTutorial(resource) {
           return this.#tutorialsEndpoint.create(resource);
       }

       updateTutorial(resource) {
           return this.#tutorialsEndpoint.update(resource.id, resource);
       }

       deleteTutorial(id) {
           return this.#tutorialsEndpoint.delete(id);
       }
   }
   ```
   </details>

   ```
   git add .
   git commit -m "feat(publishing): add tutorials to PublishingApi."
   ```

7. **Add tutorials to `usePublishingStore`, with a `withCategory` helper.** `fetchTutorials` alone only gives every tutorial its `categoryId`, `withCategory` looks the matching `Category` up in the store (already loaded by `Manage Categories`) and rebuilds the tutorial with it attached.

   ```javascript
   import {Tutorial} from "../domain/model/tutorial.entity.js";
   import {TutorialAssembler} from "../infrastructure/tutorial.assembler.js";

   const tutorials = shallowRef([]);
   const tutorialsLoaded = ref(false);
   const tutorialsCount = computed(() => {
       return tutorialsLoaded.value ? tutorials.value.length : 0;
   });

   function withCategory(tutorial) {
       return new Tutorial({
           id: tutorial.id,
           title: tutorial.title,
           summary: tutorial.summary,
           categoryId: tutorial.categoryId,
           category: getCategoryById(tutorial.categoryId) ?? null
       });
   }

   function fetchTutorials() {
       publishingApi.getTutorials().then(response => {
           tutorials.value = TutorialAssembler.toEntitiesFromResponse(response).map(withCategory);
           tutorialsLoaded.value = true;
       }).catch(error => {
           errors.value.push(error.message);
       });
   }

   function getTutorialById(id) {
       const idNum = parseInt(id);
       return tutorials.value.find(tutorial => tutorial.id === idNum);
   }
   ```

   <details>
   <summary>src/publishing/application/publishing.store.js (so far)</summary>

   ```javascript
   import {defineStore} from "pinia";
   import {computed, ref, shallowRef} from "vue";
   import {PublishingApi} from "../infrastructure/publishing-api.js";
   import {CategoryAssembler} from "../infrastructure/category.assembler.js";
   import {TutorialAssembler} from "../infrastructure/tutorial.assembler.js";
   import {Category} from "../domain/model/category.entity.js";
   import {Tutorial} from "../domain/model/tutorial.entity.js";

   const publishingApi = new PublishingApi();

   const usePublishingStore = defineStore('publishing', () => {
       const categories = shallowRef([]);
       const tutorials = shallowRef([]);
       const errors = ref([]);
       const categoriesLoaded = ref(false);
       const tutorialsLoaded = ref(false);
       const categoriesCount = computed(() => {
           return categoriesLoaded.value ? categories.value.length : 0;
       });
       const tutorialsCount = computed(() => {
           return tutorialsLoaded.value ? tutorials.value.length : 0;
       });

       function withCategory(tutorial) {
           return new Tutorial({
               id: tutorial.id,
               title: tutorial.title,
               summary: tutorial.summary,
               categoryId: tutorial.categoryId,
               category: getCategoryById(tutorial.categoryId) ?? null
           });
       }

       function fetchCategories() {
           publishingApi.getCategories().then(response => {
               categories.value = CategoryAssembler.toEntitiesFromResponse(response);
               categoriesLoaded.value = true;
           }).catch(error => {
               errors.value.push(error.message);
           });
       }

       function fetchTutorials() {
           publishingApi.getTutorials().then(response => {
               tutorials.value = TutorialAssembler.toEntitiesFromResponse(response).map(withCategory);
               tutorialsLoaded.value = true;
           }).catch(error => {
               errors.value.push(error.message);
           });
       }

       function getCategoryById(id) {
           const idNum = parseInt(id);
           return categories.value.find(category => category.id === idNum);
       }

       function addCategory(category) {
           publishingApi.createCategory(CategoryAssembler.toResourceFromEntity(category)).then(response => {
               const newCategory = CategoryAssembler.toEntityFromResource(response.data);
               categories.value = [...categories.value, newCategory];
           }).catch(error => {
               errors.value.push(error.message);
           });
       }

       function updateCategory(category) {
           publishingApi.updateCategory(CategoryAssembler.toResourceFromEntity(category)).then(response => {
               const updatedCategory = CategoryAssembler.toEntityFromResource(response.data);
               categories.value = categories.value.map(c => c.id === updatedCategory.id ? updatedCategory : c);
           }).catch(error => {
               errors.value.push(error.message);
           });
       }

       function deleteCategory(category) {
           publishingApi.deleteCategory(category.id).then(() => {
               categories.value = categories.value.filter(c => c.id !== category.id);
           }).catch(error => {
               errors.value.push(error.message);
           });
       }

       function getTutorialById(id) {
           const idNum = parseInt(id);
           return tutorials.value.find(tutorial => tutorial.id === idNum);
       }

       return {
           categories,
           tutorials,
           errors,
           categoriesLoaded,
           tutorialsLoaded,
           categoriesCount,
           tutorialsCount,
           fetchCategories,
           fetchTutorials,
           getCategoryById,
           addCategory,
           updateCategory,
           deleteCategory,
           getTutorialById
       }
   });

   export default usePublishingStore;
   ```
   </details>

   **Note:** `withCategory` calls `getCategoryById`, defined further down the same `defineStore` setup function, that is fine, both are ordinary function declarations hoisted within the closure, the order they appear in the file does not matter to the call.

   ```
   git add .
   git commit -m "feat(publishing): add tutorials to usePublishingStore."
   ```

8. **Re-link tutorials whenever categories change.** `category-list` and `tutorial-list` fetch independently, whichever request finishes second must not leave the other's data stale: if tutorials load before categories, every `category` field is `null` until this fix; if a category is renamed or deleted after tutorials already loaded, its tutorials still show the old name, or none at all.

   ```javascript
   function fetchCategories() {
       publishingApi.getCategories().then(response => {
           categories.value = CategoryAssembler.toEntitiesFromResponse(response);
           categoriesLoaded.value = true;
           tutorials.value = tutorials.value.map(withCategory);
       }).catch(error => {
           errors.value.push(error.message);
       });
   }
   ```

   ```javascript
   function updateCategory(category) {
       publishingApi.updateCategory(CategoryAssembler.toResourceFromEntity(category)).then(response => {
           const updatedCategory = CategoryAssembler.toEntityFromResource(response.data);
           categories.value = categories.value.map(c => c.id === updatedCategory.id ? updatedCategory : c);
           tutorials.value = tutorials.value.map(withCategory);
       }).catch(error => {
           errors.value.push(error.message);
       });
   }
   ```

   ```javascript
   function deleteCategory(category) {
       publishingApi.deleteCategory(category.id).then(() => {
           categories.value = categories.value.filter(c => c.id !== category.id);
           tutorials.value = tutorials.value.map(withCategory);
       }).catch(error => {
           errors.value.push(error.message);
       });
   }
   ```

   <details>
   <summary>src/publishing/application/publishing.store.js (Full file)</summary>

   ```javascript
   import {defineStore} from "pinia";
   import {computed, ref, shallowRef} from "vue";
   import {PublishingApi} from "../infrastructure/publishing-api.js";
   import {CategoryAssembler} from "../infrastructure/category.assembler.js";
   import {TutorialAssembler} from "../infrastructure/tutorial.assembler.js";
   import {Category} from "../domain/model/category.entity.js";
   import {Tutorial} from "../domain/model/tutorial.entity.js";

   const publishingApi = new PublishingApi();

   const usePublishingStore = defineStore('publishing', () => {
       const categories = shallowRef([]);
       const tutorials = shallowRef([]);
       const errors = ref([]);
       const categoriesLoaded = ref(false);
       const tutorialsLoaded = ref(false);
       const categoriesCount = computed(() => {
           return categoriesLoaded.value ? categories.value.length : 0;
       });
       const tutorialsCount = computed(() => {
           return tutorialsLoaded.value ? tutorials.value.length : 0;
       });

       function withCategory(tutorial) {
           return new Tutorial({
               id: tutorial.id,
               title: tutorial.title,
               summary: tutorial.summary,
               categoryId: tutorial.categoryId,
               category: getCategoryById(tutorial.categoryId) ?? null
           });
       }

       function fetchCategories() {
           publishingApi.getCategories().then(response => {
               categories.value = CategoryAssembler.toEntitiesFromResponse(response);
               categoriesLoaded.value = true;
               tutorials.value = tutorials.value.map(withCategory);
           }).catch(error => {
               errors.value.push(error.message);
           });
       }

       function fetchTutorials() {
           publishingApi.getTutorials().then(response => {
               tutorials.value = TutorialAssembler.toEntitiesFromResponse(response).map(withCategory);
               tutorialsLoaded.value = true;
           }).catch(error => {
               errors.value.push(error.message);
           });
       }

       function getCategoryById(id) {
           const idNum = parseInt(id);
           return categories.value.find(category => category.id === idNum);
       }

       function addCategory(category) {
           publishingApi.createCategory(CategoryAssembler.toResourceFromEntity(category)).then(response => {
               const newCategory = CategoryAssembler.toEntityFromResource(response.data);
               categories.value = [...categories.value, newCategory];
           }).catch(error => {
               errors.value.push(error.message);
           });
       }

       function updateCategory(category) {
           publishingApi.updateCategory(CategoryAssembler.toResourceFromEntity(category)).then(response => {
               const updatedCategory = CategoryAssembler.toEntityFromResource(response.data);
               categories.value = categories.value.map(c => c.id === updatedCategory.id ? updatedCategory : c);
               tutorials.value = tutorials.value.map(withCategory);
           }).catch(error => {
               errors.value.push(error.message);
           });
       }

       function deleteCategory(category) {
           publishingApi.deleteCategory(category.id).then(() => {
               categories.value = categories.value.filter(c => c.id !== category.id);
               tutorials.value = tutorials.value.map(withCategory);
           }).catch(error => {
               errors.value.push(error.message);
           });
       }

       function getTutorialById(id) {
           const idNum = parseInt(id);
           return tutorials.value.find(tutorial => tutorial.id === idNum);
       }

       return {
           categories,
           tutorials,
           errors,
           categoriesLoaded,
           tutorialsLoaded,
           categoriesCount,
           tutorialsCount,
           fetchCategories,
           fetchTutorials,
           getCategoryById,
           addCategory,
           updateCategory,
           deleteCategory,
           getTutorialById
       }
   });

   export default usePublishingStore;
   ```
   </details>

   ```
   git add .
   git commit -m "fix(publishing): re-link tutorials whenever categories load or change."
   ```

9. **Create the `tutorial-list` view.** Right-click `src` → `New` → `Vue Single-File Component` → `Composition API` → type `publishing/presentation/views/tutorial-list` → Enter. Read-only for now, and it fetches both tutorials and categories, `withCategory` above needs categories loaded to attach them.

   ```javascript
   const {tutorials, tutorialsLoaded, categoriesLoaded, errors} = storeToRefs(store);
   const {fetchTutorials, fetchCategories} = store;

   onMounted(() => {
     if (!categoriesLoaded.value) fetchCategories();
     if (!tutorialsLoaded.value) fetchTutorials();
   });
   ```

   <details>
   <summary>src/publishing/presentation/views/tutorial-list.vue (so far)</summary>

   ```vue
   <script setup>
   import {useI18n} from "vue-i18n";
   import usePublishingStore from "../../application/publishing.store.js";
   import {onMounted} from "vue";
   import {storeToRefs} from "pinia";

   const {t} = useI18n();
   const store = usePublishingStore();
   const {tutorials, tutorialsLoaded, categoriesLoaded, errors} = storeToRefs(store);
   const {fetchTutorials, fetchCategories} = store;

   onMounted(() => {
     if (!categoriesLoaded.value) fetchCategories();
     if (!tutorialsLoaded.value) fetchTutorials();
   });
   </script>

   <template>
     <div class="p-4">
       <h1>{{ t('tutorials.title') }}</h1>
       <pv-data-table
           :value="tutorials"
           :loading="!tutorialsLoaded"
           striped-rows
           table-style="min-width: 50rem"
           paginator
           :rows="5"
           :rows-per-page-options="[5, 10, 20]"
       >
         <pv-column field="id" :header="t('tutorials.id')" sortable />
         <pv-column field="title" :header="t('tutorial.title')" sortable />
         <pv-column field="summary" :header="t('tutorials.summary')" />
         <pv-column :header="t('tutorial.category')">
           <template #body="slotProps">{{ slotProps.data.category?.name }}</template>
         </pv-column>
       </pv-data-table>
       <div v-if="errors.length" class="text-red-500 mt-3">
         {{ t('errors.occurred') }}: {{ errors.join(', ') }}
       </div>
     </div>
   </template>

   <style scoped>

   </style>
   ```
   </details>

   **Note:** every other column uses `field`, PrimeVue reads `slotProps.data[field]` (or a dotted path) straight off the row object. The category column cannot: `Tutorial.category` sits behind a getter on the prototype, not an own enumerable property, and PrimeVue's field resolver only follows a dotted path (`field="category.name"`) through own enumerable keys, so it silently returns `null` for an entity built from native `#` private fields. `<template #body>` calls the getter itself instead, `slotProps.data.category?.name`, so this column has no `field` and is not `sortable`. See ADR-0004 in `## Release`.

   ```
   git add .
   git commit -m "feat(publishing): add tutorial-list view."
   ```

10. **Add the `tutorials` route.**

    ```javascript
    const tutorialList = () => import('./views/tutorial-list.vue');

    {   path: 'tutorials', name: 'publishing-tutorials', component: tutorialList, meta: {title: 'Tutorials'}}
    ```

    <details>
    <summary>src/publishing/presentation/publishing-routes.js (so far)</summary>

    ```javascript
    const categoryList = () => import('./views/category-list.vue');
    const categoryForm = () => import('./views/category-form.vue');
    const tutorialList = () => import('./views/tutorial-list.vue');

    const publishingRoutes = [
        {   path: 'categories',          name: 'publishing-categories',    component: categoryList, meta: {title: 'Categories'}},
        {   path: 'categories/new',      name: 'publishing-category-new',  component: categoryForm, meta: {title: 'New Category'}},
        {   path: 'categories/:id/edit', name: 'publishing-category-edit', component: categoryForm, meta: {title: 'Edit Category'}},
        {   path: 'tutorials',           name: 'publishing-tutorials',     component: tutorialList, meta: {title: 'Tutorials'}}
    ];

    export default publishingRoutes;
    ```
    </details>

    ```
    git add .
    git commit -m "feat(publishing): add route for the tutorial list."
    ```

11. **Add a Tutorials option to `layout`.**

    ```javascript
    {label: 'option.tutorials', to: '/publishing/tutorials'}
    ```

    <details>
    <summary>src/shared/presentation/components/layout.vue</summary>

    ```vue
    <script setup>
    import LanguageSwitcher from "./language-switcher.vue";
    import {ref} from "vue";
    import {useI18n} from "vue-i18n";
    import FooterContent from "./footer-content.vue";

    const { t } = useI18n();

    const drawer = ref(false);

    const toggleDrawer = () => {
      drawer.value = !drawer.value;
    }

    const items = [
      {label: 'option.home', to: '/home'},
      {label: 'option.about', to: '/about'},
      {label: 'option.categories', to: '/publishing/categories'},
      {label: 'option.tutorials', to: '/publishing/tutorials'}
    ];
    </script>

    <template>
      <pv-confirm-dialog/>
      <header class="absolute top-0 left-0 w-full">
        <pv-toolbar class="bg-primary">
          <template #start>
            <pv-button class="p-button-text" icon="pi pi-bars" @click="toggleDrawer"/>
            <h3>ACME Learning Center</h3>
          </template>
          <template #end>
            <div class="flex-column mr-3">
              <pv-button v-for="item in items" :key="item.label" as-child v-slot="slotProps">
                <router-link :to="item.to" :class="slotProps['class']">{{ t(item.label) }}</router-link>
              </pv-button>
            </div>
            <language-switcher/>
          </template>
        </pv-toolbar>
        <pv-drawer v-model:visible="drawer"/>
      </header>
      <main class="mt-7">
        <router-view/>
      </main>
      <footer-content/>
    </template>
    ```
    </details>

    ```
    git add .
    git commit -m "feat(shared): add tutorials option to the layout navigation."
    ```

12. **Add the tutorial list texts to the English dictionary.**

    <details>
    <summary>src/locales/en.json (so far)</summary>

    ```json
    {
      "option": {
        "home": "Home",
        "about": "About",
        "categories": "Categories",
        "tutorials": "Tutorials"
      },
      "authoring-phrase": {
        "intro": "Made with",
        "use": "using",
        "author": "by {brand} Developer Team"
      },
      "about": {
        "title": "About Us",
        "content": "ACME Learning Center is an Education Business Platform, part of ACME Corporation."
      },
      "home": {
        "title": "Welcome",
        "content": "Welcome to ACME Learning Center."
      },
      "page-not-found": {
        "title": "Page Not Found",
        "content": "The path {unavailable-route} is not available.",
        "go-home": "Go Home"
      },
      "categories": {
        "title": "Categories",
        "id": "ID",
        "name": "Name",
        "actions": "Actions",
        "new": "New Category",
        "confirm-delete": "Are you sure you want to delete {name}?",
        "delete-header": "Confirm Deletion"
      },
      "category": {
        "new-title": "Create New Category",
        "edit-title": "Edit Category",
        "name": "Name",
        "save": "Save",
        "cancel": "Cancel"
      },
      "tutorials": {
        "title": "Tutorials",
        "id": "ID",
        "summary": "Summary"
      },
      "tutorial": {
        "title": "Title",
        "category": "Category"
      },
      "errors": {
        "occurred": "Errors occurred"
      }
    }
    ```
    </details>

    ```
    git add .
    git commit -m "feat(i18n): add tutorial list texts to the English dictionary."
    ```

13. **Add the same keys to the Spanish dictionary.**

    <details>
    <summary>src/locales/es.json (so far)</summary>

    ```json
    {
      "option": {
        "home": "Inicio",
        "about": "Acerca de",
        "categories": "Categorías",
        "tutorials": "Tutoriales"
      },
      "authoring-phrase": {
        "intro": "Hecho con",
        "use": "utilizando",
        "author": "por el Equipo de Desarrollo de {brand}"
      },
      "about": {
        "title": "Acerca de Nosotros",
        "content": "ACME Learning Center es una Plataforma educativa, parte de ACME Corporation."
      },
      "home": {
        "title": "Inicio",
        "content": "Bienvenido a ACME Learning Center."
      },
      "page-not-found": {
        "title": "Página no encontrada",
        "content": "La ruta {unavailable-route} no está disponible.",
        "go-home": "Ir al Inicio"
      },
      "categories": {
        "title": "Categorías",
        "id": "ID",
        "name": "Nombre",
        "actions": "Acciones",
        "new": "Nueva Categoría",
        "confirm-delete": "¿Estás seguro de que quieres eliminar {name}?",
        "delete-header": "Confirmar Eliminación"
      },
      "category": {
        "new-title": "Crear Nueva Categoría",
        "edit-title": "Editar Categoría",
        "name": "Nombre",
        "save": "Guardar",
        "cancel": "Cancelar"
      },
      "tutorials": {
        "title": "Tutoriales",
        "id": "ID",
        "summary": "Resumen"
      },
      "tutorial": {
        "title": "Título",
        "category": "Categoría"
      },
      "errors": {
        "occurred": "Ocurrieron errores"
      }
    }
    ```
    </details>

    ```
    git add .
    git commit -m "feat(i18n): add tutorial list texts to the Spanish dictionary."
    ```

14. **Run it.** Start the fake API and `npm run dev`, click `Tutorials`. All four seed tutorials show their real category name. Open `Categories` first, then `Tutorials`, and the other way around, either order the names are there, `withCategory` is called from both `fetchCategories` and `fetchTutorials`. Stop both with `Ctrl+C`.

15. **Add `addTutorial` to `usePublishingStore`.**

    ```javascript
    function addTutorial(tutorial) {
        publishingApi.createTutorial(TutorialAssembler.toResourceFromEntity(tutorial)).then(response => {
            const newTutorial = withCategory(TutorialAssembler.toEntityFromResource(response.data));
            tutorials.value = [...tutorials.value, newTutorial];
        }).catch(error => {
            errors.value.push(error.message);
        });
    }
    ```

    <details>
    <summary>src/publishing/application/publishing.store.js (so far)</summary>

    ```javascript
    import {defineStore} from "pinia";
    import {computed, ref, shallowRef} from "vue";
    import {PublishingApi} from "../infrastructure/publishing-api.js";
    import {CategoryAssembler} from "../infrastructure/category.assembler.js";
    import {TutorialAssembler} from "../infrastructure/tutorial.assembler.js";
    import {Category} from "../domain/model/category.entity.js";
    import {Tutorial} from "../domain/model/tutorial.entity.js";

    const publishingApi = new PublishingApi();

    const usePublishingStore = defineStore('publishing', () => {
        const categories = shallowRef([]);
        const tutorials = shallowRef([]);
        const errors = ref([]);
        const categoriesLoaded = ref(false);
        const tutorialsLoaded = ref(false);
        const categoriesCount = computed(() => {
            return categoriesLoaded.value ? categories.value.length : 0;
        });
        const tutorialsCount = computed(() => {
            return tutorialsLoaded.value ? tutorials.value.length : 0;
        });

        function withCategory(tutorial) {
            return new Tutorial({
                id: tutorial.id,
                title: tutorial.title,
                summary: tutorial.summary,
                categoryId: tutorial.categoryId,
                category: getCategoryById(tutorial.categoryId) ?? null
            });
        }

        function fetchCategories() {
            publishingApi.getCategories().then(response => {
                categories.value = CategoryAssembler.toEntitiesFromResponse(response);
                categoriesLoaded.value = true;
                tutorials.value = tutorials.value.map(withCategory);
            }).catch(error => {
                errors.value.push(error.message);
            });
        }

        function fetchTutorials() {
            publishingApi.getTutorials().then(response => {
                tutorials.value = TutorialAssembler.toEntitiesFromResponse(response).map(withCategory);
                tutorialsLoaded.value = true;
            }).catch(error => {
                errors.value.push(error.message);
            });
        }

        function getCategoryById(id) {
            const idNum = parseInt(id);
            return categories.value.find(category => category.id === idNum);
        }

        function addCategory(category) {
            publishingApi.createCategory(CategoryAssembler.toResourceFromEntity(category)).then(response => {
                const newCategory = CategoryAssembler.toEntityFromResource(response.data);
                categories.value = [...categories.value, newCategory];
            }).catch(error => {
                errors.value.push(error.message);
            });
        }

        function updateCategory(category) {
            publishingApi.updateCategory(CategoryAssembler.toResourceFromEntity(category)).then(response => {
                const updatedCategory = CategoryAssembler.toEntityFromResource(response.data);
                categories.value = categories.value.map(c => c.id === updatedCategory.id ? updatedCategory : c);
                tutorials.value = tutorials.value.map(withCategory);
            }).catch(error => {
                errors.value.push(error.message);
            });
        }

        function deleteCategory(category) {
            publishingApi.deleteCategory(category.id).then(() => {
                categories.value = categories.value.filter(c => c.id !== category.id);
                tutorials.value = tutorials.value.map(withCategory);
            }).catch(error => {
                errors.value.push(error.message);
            });
        }

        function getTutorialById(id) {
            const idNum = parseInt(id);
            return tutorials.value.find(tutorial => tutorial.id === idNum);
        }

        function addTutorial(tutorial) {
            publishingApi.createTutorial(TutorialAssembler.toResourceFromEntity(tutorial)).then(response => {
                const newTutorial = withCategory(TutorialAssembler.toEntityFromResource(response.data));
                tutorials.value = [...tutorials.value, newTutorial];
            }).catch(error => {
                errors.value.push(error.message);
            });
        }

        return {
            categories,
            tutorials,
            errors,
            categoriesLoaded,
            tutorialsLoaded,
            categoriesCount,
            tutorialsCount,
            fetchCategories,
            fetchTutorials,
            getCategoryById,
            addCategory,
            updateCategory,
            deleteCategory,
            getTutorialById,
            addTutorial
        }
    });

    export default usePublishingStore;
    ```
    </details>

    **Note:** `withCategory` wraps the created tutorial before it joins `tutorials.value`, so the new row shows its category name immediately, without waiting for a refetch.

    ```
    git add .
    git commit -m "feat(publishing): add create tutorial method to usePublishingStore."
    ```

16. **Register `pv-select` and `pv-textarea` in `main.js`.** `tutorial-form` needs a category dropdown and a multi-line summary field.

    ```javascript
    createApp(App)
        .component('pv-select',   Select)
        .component('pv-textarea', Textarea)
    ```

    <details>
    <summary>src/main.js (so far)</summary>

    ```javascript
    import {createApp} from 'vue'
    import './style.css'
    import App from './app.vue'
    import i18n from "./i18n.js";
    import PrimeVue from 'primevue/config';
    import Material from '@primeuix/themes/material';
    import 'primeflex/primeflex.css';
    import 'primeicons/primeicons.css';
    import {
        Button,
        Column,
        ConfirmationService,
        ConfirmDialog,
        DataTable,
        Drawer,
        InputText,
        Select,
        SelectButton,
        Textarea,
        Toolbar
    } from "primevue";
    import router from "./router.js";
    import pinia from "./pinia.js";

    const primeUiLicenseKey = import.meta.env.VITE_PRIME_UI_LICENSE_KEY;

    createApp(App)
        .use(i18n)
        .use(PrimeVue, {theme: {preset: Material}, ripple: true, license: primeUiLicenseKey})
        .use(ConfirmationService)
        .component('pv-button',         Button)
        .component('pv-column',         Column)
        .component('pv-confirm-dialog', ConfirmDialog)
        .component('pv-data-table',     DataTable)
        .component('pv-drawer',         Drawer)
        .component('pv-input-text',     InputText)
        .component('pv-select',         Select)
        .component('pv-select-button',  SelectButton)
        .component('pv-textarea',       Textarea)
        .component('pv-toolbar',        Toolbar)
        .use(router)
        .use(pinia)
        .mount('#app')
    ```
    </details>

    ```
    git add .
    git commit -m "feat: register the PrimeVue select and textarea."
    ```

17. **Create the `tutorial-form` view, create mode.** Right-click `src` → `New` → `Vue Single-File Component` → `Composition API` → type `publishing/presentation/views/tutorial-form` → Enter. `pv-select` is not a native `<select>`, so the browser's own required-field check does nothing on it, `categoryRequiredError` does that check by hand before saving.

   ```javascript
   const categoryRequiredError = ref(false);

   const saveTutorial = () => {
     if (!form.value.categoryId) {
       categoryRequiredError.value = true;
       return;
     }
     categoryRequiredError.value = false;
     ...
   };
   ```

   <details>
   <summary>src/publishing/presentation/views/tutorial-form.vue (so far)</summary>

   ```vue
   <script setup>
   import {useI18n} from "vue-i18n";
   import {useRouter} from "vue-router";
   import usePublishingStore from "../../application/publishing.store.js";
   import {onMounted, ref} from "vue";
   import {storeToRefs} from "pinia";
   import {Tutorial} from "../../domain/model/tutorial.entity.js";

   const {t} = useI18n();
   const router = useRouter();
   const store = usePublishingStore();
   const {errors, categories, categoriesLoaded} = storeToRefs(store);
   const {addTutorial, fetchCategories} = store;

   const form = ref({title: '', summary: '', categoryId: null});
   const categoryRequiredError = ref(false);

   onMounted(() => {
     if (!categoriesLoaded.value) fetchCategories();
   });

   const saveTutorial = () => {
     if (!form.value.categoryId) {
       categoryRequiredError.value = true;
       return;
     }
     categoryRequiredError.value = false;
     const tutorial = new Tutorial({
       id: null,
       title: form.value.title,
       summary: form.value.summary,
       categoryId: form.value.categoryId,
     });
     addTutorial(tutorial);
     navigateBack();
   };

   const navigateBack = () => {
     router.push({name: 'publishing-tutorials'});
   };
   </script>

   <template>
     <div class="p-4">
       <h1>{{ t('tutorial.new-title') }}</h1>
       <form @submit.prevent="saveTutorial">
         <div class="field mb-3">
           <label for="title">{{ t('tutorial.title') }}</label>
           <pv-input-text id="title" v-model="form.title" required class="w-full" />
         </div>
         <div class="field mb-3">
           <label for="summary">{{ t('tutorial.summary') }}</label>
           <pv-textarea id="summary" v-model="form.summary" rows="4" class="w-full" />
         </div>
         <div class="field mb-3">
           <label for="category">{{ t('tutorial.category') }}</label>
           <pv-select
               id="category"
               v-model="form.categoryId"
               :options="categories"
               optionLabel="name"
               optionValue="id"
               placeholder="Select a category"
               class="w-full"
           />
           <small v-if="categoryRequiredError" class="text-red-500">{{ t('tutorial.category-required') }}</small>
         </div>
         <pv-button type="submit" :label="t('tutorial.save')" icon="pi pi-save" />
         <pv-button :label="t('tutorial.cancel')" severity="secondary" class="ml-2" @click="navigateBack" />
       </form>
       <div v-if="errors.length" class="text-red-500 mt-3">
         {{ t('errors.occurred') }}: {{ errors.join(', ') }}
       </div>
     </div>
   </template>

   <style scoped>

   </style>
   ```
   </details>

   **Note:** `:options="categories"` binds straight to the `storeToRefs` ref, `pv-select` reads the category names from it through `optionLabel="name"`, so it needs `categories` already loaded, `onMounted` fetches them the same way `tutorial-list` does.

   ```
   git add .
   git commit -m "feat(publishing): add tutorial-form view."
   ```

18. **Add the `tutorials/new` route.**

    ```javascript
    const tutorialForm = () => import('./views/tutorial-form.vue');

    {   path: 'tutorials/new', name: 'publishing-tutorial-new', component: tutorialForm, meta: {title: 'New Tutorial'}}
    ```

    <details>
    <summary>src/publishing/presentation/publishing-routes.js (so far)</summary>

    ```javascript
    const categoryList = () => import('./views/category-list.vue');
    const categoryForm = () => import('./views/category-form.vue');
    const tutorialList = () => import('./views/tutorial-list.vue');
    const tutorialForm = () => import('./views/tutorial-form.vue');

    const publishingRoutes = [
        {   path: 'categories',          name: 'publishing-categories',    component: categoryList, meta: {title: 'Categories'}},
        {   path: 'categories/new',      name: 'publishing-category-new',  component: categoryForm, meta: {title: 'New Category'}},
        {   path: 'categories/:id/edit', name: 'publishing-category-edit', component: categoryForm, meta: {title: 'Edit Category'}},
        {   path: 'tutorials',           name: 'publishing-tutorials',     component: tutorialList, meta: {title: 'Tutorials'}},
        {   path: 'tutorials/new',       name: 'publishing-tutorial-new',  component: tutorialForm, meta: {title: 'New Tutorial'}}
    ];

    export default publishingRoutes;
    ```
    </details>

    ```
    git add .
    git commit -m "feat(publishing): add route for creating a new tutorial."
    ```

19. **Add a "New Tutorial" button to `tutorial-list`.**

    ```javascript
    import {useRouter} from "vue-router";

    const router = useRouter();

    const navigateToNew = () => {
      router.push({ name: 'publishing-tutorial-new' });
    };
    ```

    ```vue
    <pv-button :label="t('tutorials.new')" icon="pi pi-plus" class="mb-3" @click="navigateToNew" />
    ```

    <details>
    <summary>src/publishing/presentation/views/tutorial-list.vue (so far)</summary>

    ```vue
    <script setup>
    import {useI18n} from "vue-i18n";
    import {useRouter} from "vue-router";
    import usePublishingStore from "../../application/publishing.store.js";
    import {onMounted} from "vue";
    import {storeToRefs} from "pinia";

    const { t } = useI18n();
    const router = useRouter();
    const store = usePublishingStore();
    const {tutorials, tutorialsLoaded, categoriesLoaded, errors} = storeToRefs(store);
    const {fetchTutorials, fetchCategories} = store;

    onMounted(() => {
      if (!categoriesLoaded.value) fetchCategories();
      if (!tutorialsLoaded.value) fetchTutorials();
    });

    const navigateToNew = () => {
      router.push({ name: 'publishing-tutorial-new' });
    };
    </script>

    <template>
      <div class="p-4">
        <h1>{{ t('tutorials.title') }}</h1>
        <pv-button :label="t('tutorials.new')" icon="pi pi-plus" class="mb-3" @click="navigateToNew" />
        <pv-data-table
            :value="tutorials"
            :loading="!tutorialsLoaded"
            striped-rows
            table-style="min-width: 50rem"
            paginator
            :rows="5"
            :rows-per-page-options="[5, 10, 20]"
        >
          <pv-column field="id" :header="t('tutorials.id')" sortable />
          <pv-column field="title" :header="t('tutorial.title')" sortable />
          <pv-column field="summary" :header="t('tutorials.summary')" />
          <pv-column :header="t('tutorial.category')">
            <template #body="slotProps">{{ slotProps.data.category?.name }}</template>
          </pv-column>
        </pv-data-table>
        <div v-if="errors.length" class="text-red-500 mt-3">
          {{ t('errors.occurred') }}: {{ errors.join(', ') }}
        </div>
      </div>
    </template>

    <style scoped>

    </style>
    ```
    </details>

    ```
    git add .
    git commit -m "feat(publishing): add new tutorial navigation button to tutorial-list."
    ```

20. **Add the create-tutorial texts to the English dictionary.**

    <details>
    <summary>src/locales/en.json (so far)</summary>

    ```json
    {
      "option": {
        "home": "Home",
        "about": "About",
        "categories": "Categories",
        "tutorials": "Tutorials"
      },
      "authoring-phrase": {
        "intro": "Made with",
        "use": "using",
        "author": "by {brand} Developer Team"
      },
      "about": {
        "title": "About Us",
        "content": "ACME Learning Center is an Education Business Platform, part of ACME Corporation."
      },
      "home": {
        "title": "Welcome",
        "content": "Welcome to ACME Learning Center."
      },
      "page-not-found": {
        "title": "Page Not Found",
        "content": "The path {unavailable-route} is not available.",
        "go-home": "Go Home"
      },
      "categories": {
        "title": "Categories",
        "id": "ID",
        "name": "Name",
        "actions": "Actions",
        "new": "New Category",
        "confirm-delete": "Are you sure you want to delete {name}?",
        "delete-header": "Confirm Deletion"
      },
      "category": {
        "new-title": "Create New Category",
        "edit-title": "Edit Category",
        "name": "Name",
        "save": "Save",
        "cancel": "Cancel"
      },
      "tutorials": {
        "title": "Tutorials",
        "id": "ID",
        "summary": "Summary",
        "new": "New Tutorial"
      },
      "tutorial": {
        "new-title": "Create New Tutorial",
        "title": "Title",
        "summary": "Summary",
        "category": "Category",
        "category-required": "Please select a category.",
        "save": "Save",
        "cancel": "Cancel"
      },
      "errors": {
        "occurred": "Errors occurred"
      }
    }
    ```
    </details>

    ```
    git add .
    git commit -m "feat(i18n): add create tutorial texts to the English dictionary."
    ```

21. **Add the same keys to the Spanish dictionary.**

    <details>
    <summary>src/locales/es.json (so far)</summary>

    ```json
    {
      "option": {
        "home": "Inicio",
        "about": "Acerca de",
        "categories": "Categorías",
        "tutorials": "Tutoriales"
      },
      "authoring-phrase": {
        "intro": "Hecho con",
        "use": "utilizando",
        "author": "por el Equipo de Desarrollo de {brand}"
      },
      "about": {
        "title": "Acerca de Nosotros",
        "content": "ACME Learning Center es una Plataforma educativa, parte de ACME Corporation."
      },
      "home": {
        "title": "Inicio",
        "content": "Bienvenido a ACME Learning Center."
      },
      "page-not-found": {
        "title": "Página no encontrada",
        "content": "La ruta {unavailable-route} no está disponible.",
        "go-home": "Ir al Inicio"
      },
      "categories": {
        "title": "Categorías",
        "id": "ID",
        "name": "Nombre",
        "actions": "Acciones",
        "new": "Nueva Categoría",
        "confirm-delete": "¿Estás seguro de que quieres eliminar {name}?",
        "delete-header": "Confirmar Eliminación"
      },
      "category": {
        "new-title": "Crear Nueva Categoría",
        "edit-title": "Editar Categoría",
        "name": "Nombre",
        "save": "Guardar",
        "cancel": "Cancelar"
      },
      "tutorials": {
        "title": "Tutoriales",
        "id": "ID",
        "summary": "Resumen",
        "new": "Nuevo Tutorial"
      },
      "tutorial": {
        "new-title": "Crear Nuevo Tutorial",
        "title": "Título",
        "summary": "Resumen",
        "category": "Categoría",
        "category-required": "Por favor selecciona una categoría.",
        "save": "Guardar",
        "cancel": "Cancelar"
      },
      "errors": {
        "occurred": "Ocurrieron errores"
      }
    }
    ```
    </details>

    ```
    git add .
    git commit -m "feat(i18n): add create tutorial texts to the Spanish dictionary."
    ```

22. **Run it.** Click `Tutorials` → `New Tutorial`, leave the category empty, `Save`: the required-category message shows and nothing is sent. Choose a category, `Save`: the list shows the new tutorial with its category name. Stop both servers with `Ctrl+C`.

23. **Add `updateTutorial` to `usePublishingStore`.**

    ```javascript
    function updateTutorial(tutorial) {
        publishingApi.updateTutorial(TutorialAssembler.toResourceFromEntity(tutorial)).then(response => {
            const updatedTutorial = withCategory(TutorialAssembler.toEntityFromResource(response.data));
            tutorials.value = tutorials.value.map(t => t.id === updatedTutorial.id ? updatedTutorial : t);
        }).catch(error => {
            errors.value.push(error.message);
        });
    }
    ```

    <details>
    <summary>src/publishing/application/publishing.store.js (so far)</summary>

    ```javascript
    import {defineStore} from "pinia";
    import {computed, ref, shallowRef} from "vue";
    import {PublishingApi} from "../infrastructure/publishing-api.js";
    import {CategoryAssembler} from "../infrastructure/category.assembler.js";
    import {TutorialAssembler} from "../infrastructure/tutorial.assembler.js";
    import {Category} from "../domain/model/category.entity.js";
    import {Tutorial} from "../domain/model/tutorial.entity.js";

    const publishingApi = new PublishingApi();

    const usePublishingStore = defineStore('publishing', () => {
        const categories = shallowRef([]);
        const tutorials = shallowRef([]);
        const errors = ref([]);
        const categoriesLoaded = ref(false);
        const tutorialsLoaded = ref(false);
        const categoriesCount = computed(() => {
            return categoriesLoaded.value ? categories.value.length : 0;
        });
        const tutorialsCount = computed(() => {
            return tutorialsLoaded.value ? tutorials.value.length : 0;
        });

        function withCategory(tutorial) {
            return new Tutorial({
                id: tutorial.id,
                title: tutorial.title,
                summary: tutorial.summary,
                categoryId: tutorial.categoryId,
                category: getCategoryById(tutorial.categoryId) ?? null
            });
        }

        function fetchCategories() {
            publishingApi.getCategories().then(response => {
                categories.value = CategoryAssembler.toEntitiesFromResponse(response);
                categoriesLoaded.value = true;
                tutorials.value = tutorials.value.map(withCategory);
            }).catch(error => {
                errors.value.push(error.message);
            });
        }

        function fetchTutorials() {
            publishingApi.getTutorials().then(response => {
                tutorials.value = TutorialAssembler.toEntitiesFromResponse(response).map(withCategory);
                tutorialsLoaded.value = true;
            }).catch(error => {
                errors.value.push(error.message);
            });
        }

        function getCategoryById(id) {
            const idNum = parseInt(id);
            return categories.value.find(category => category.id === idNum);
        }

        function addCategory(category) {
            publishingApi.createCategory(CategoryAssembler.toResourceFromEntity(category)).then(response => {
                const newCategory = CategoryAssembler.toEntityFromResource(response.data);
                categories.value = [...categories.value, newCategory];
            }).catch(error => {
                errors.value.push(error.message);
            });
        }

        function updateCategory(category) {
            publishingApi.updateCategory(CategoryAssembler.toResourceFromEntity(category)).then(response => {
                const updatedCategory = CategoryAssembler.toEntityFromResource(response.data);
                categories.value = categories.value.map(c => c.id === updatedCategory.id ? updatedCategory : c);
                tutorials.value = tutorials.value.map(withCategory);
            }).catch(error => {
                errors.value.push(error.message);
            });
        }

        function deleteCategory(category) {
            publishingApi.deleteCategory(category.id).then(() => {
                categories.value = categories.value.filter(c => c.id !== category.id);
                tutorials.value = tutorials.value.map(withCategory);
            }).catch(error => {
                errors.value.push(error.message);
            });
        }

        function getTutorialById(id) {
            const idNum = parseInt(id);
            return tutorials.value.find(tutorial => tutorial.id === idNum);
        }

        function addTutorial(tutorial) {
            publishingApi.createTutorial(TutorialAssembler.toResourceFromEntity(tutorial)).then(response => {
                const newTutorial = withCategory(TutorialAssembler.toEntityFromResource(response.data));
                tutorials.value = [...tutorials.value, newTutorial];
            }).catch(error => {
                errors.value.push(error.message);
            });
        }

        function updateTutorial(tutorial) {
            publishingApi.updateTutorial(TutorialAssembler.toResourceFromEntity(tutorial)).then(response => {
                const updatedTutorial = withCategory(TutorialAssembler.toEntityFromResource(response.data));
                tutorials.value = tutorials.value.map(t => t.id === updatedTutorial.id ? updatedTutorial : t);
            }).catch(error => {
                errors.value.push(error.message);
            });
        }

        return {
            categories,
            tutorials,
            errors,
            categoriesLoaded,
            tutorialsLoaded,
            categoriesCount,
            tutorialsCount,
            fetchCategories,
            fetchTutorials,
            getCategoryById,
            addCategory,
            updateCategory,
            deleteCategory,
            getTutorialById,
            addTutorial,
            updateTutorial
        }
    });

    export default usePublishingStore;
    ```
    </details>

    ```
    git add .
    git commit -m "feat(publishing): add update tutorial method to usePublishingStore."
    ```

24. **Support edit mode in `tutorial-form`.**

    ```javascript
    import {useRoute, useRouter} from "vue-router";
    import {computed, onMounted, ref} from "vue";

    const route = useRoute();
    const isEdit = computed(() => !!route.params.id);

    onMounted(() => {
      if (!categoriesLoaded.value) fetchCategories();
      if (isEdit.value) {
        const tutorial = getTutorialById(route.params.id);
        if (tutorial) {
          form.value.title = tutorial.title;
          form.value.summary = tutorial.summary;
          form.value.categoryId = tutorial.categoryId;
        } else router.push({name: 'publishing-tutorials'});
      }
    });

    function getTutorialById(id) {
      return store.getTutorialById(id);
    }
    ```

    <details>
    <summary>src/publishing/presentation/views/tutorial-form.vue (Full file)</summary>

    ```vue
    <script setup>
    import {useI18n} from "vue-i18n";
    import {useRoute, useRouter} from "vue-router";
    import usePublishingStore from "../../application/publishing.store.js";
    import {computed, onMounted, ref} from "vue";
    import {storeToRefs} from "pinia";
    import {Tutorial} from "../../domain/model/tutorial.entity.js";

    const {t} = useI18n();
    const route = useRoute();
    const router = useRouter();
    const store = usePublishingStore();
    const {errors, categories, categoriesLoaded} = storeToRefs(store);
    const {addTutorial, updateTutorial, fetchCategories} = store;

    const form = ref({title: '', summary: '', categoryId: null});
    const isEdit = computed(() => !!route.params.id);
    const categoryRequiredError = ref(false);

    onMounted(() => {
      if (!categoriesLoaded.value) fetchCategories();
      if (isEdit.value) {
        const tutorial = getTutorialById(route.params.id);
        if (tutorial) {
          form.value.title = tutorial.title;
          form.value.summary = tutorial.summary;
          form.value.categoryId = tutorial.categoryId;
        } else router.push({name: 'publishing-tutorials'});
      }
    });

    function getTutorialById(id) {
      return store.getTutorialById(id);
    }

    const saveTutorial = () => {
      if (!form.value.categoryId) {
        categoryRequiredError.value = true;
        return;
      }
      categoryRequiredError.value = false;
      const tutorial = new Tutorial({
        id: isEdit.value ? Number(route.params.id) : null,
        title: form.value.title,
        summary: form.value.summary,
        categoryId: form.value.categoryId,
      });
      if (isEdit.value) updateTutorial(tutorial); else addTutorial(tutorial);
      navigateBack();
    };

    const navigateBack = () => {
      router.push({name: 'publishing-tutorials'});
    };
    </script>

    <template>
      <div class="p-4">
        <h1>{{ isEdit ? t('tutorial.edit-title') : t('tutorial.new-title') }}</h1>
        <form @submit.prevent="saveTutorial">
          <div class="field mb-3">
            <label for="title">{{ t('tutorial.title') }}</label>
            <pv-input-text id="title" v-model="form.title" required class="w-full" />
          </div>
          <div class="field mb-3">
            <label for="summary">{{ t('tutorial.summary') }}</label>
            <pv-textarea id="summary" v-model="form.summary" rows="4" class="w-full" />
          </div>
          <div class="field mb-3">
            <label for="category">{{ t('tutorial.category') }}</label>
            <pv-select
                id="category"
                v-model="form.categoryId"
                :options="categories"
                optionLabel="name"
                optionValue="id"
                placeholder="Select a category"
                class="w-full"
            />
            <small v-if="categoryRequiredError" class="text-red-500">{{ t('tutorial.category-required') }}</small>
          </div>
          <pv-button type="submit" :label="t('tutorial.save')" icon="pi pi-save" />
          <pv-button :label="t('tutorial.cancel')" severity="secondary" class="ml-2" @click="navigateBack" />
        </form>
        <div v-if="errors.length" class="text-red-500 mt-3">
          {{ t('errors.occurred') }}: {{ errors.join(', ') }}
        </div>
      </div>
    </template>

    <style scoped>

    </style>
    ```
    </details>

    ```
    git add .
    git commit -m "refactor(publishing): support edit mode in tutorial-form."
    ```

25. **Add the `tutorials/:id/edit` route.**

    ```javascript
    {   path: 'tutorials/:id/edit', name: 'publishing-tutorial-edit', component: tutorialForm, meta: {title: 'Edit Tutorial'}}
    ```

    <details>
    <summary>src/publishing/presentation/publishing-routes.js</summary>

    ```javascript
    const categoryList = () => import('./views/category-list.vue');
    const categoryForm = () => import('./views/category-form.vue');
    const tutorialList = () => import('./views/tutorial-list.vue');
    const tutorialForm = () => import('./views/tutorial-form.vue');

    const publishingRoutes = [
        {   path: 'categories',             name: 'publishing-categories',      component: categoryList, meta: {title: 'Categories'}},
        {   path: 'categories/new',         name: 'publishing-category-new',    component: categoryForm, meta: {title: 'New Category'}},
        {   path: 'categories/:id/edit',    name: 'publishing-category-edit',   component: categoryForm, meta: {title: 'Edit Category'}},
        {   path: 'tutorials',              name: 'publishing-tutorials',       component: tutorialList, meta: {title: 'Tutorials'}},
        {   path: 'tutorials/new',          name: 'publishing-tutorial-new',    component: tutorialForm, meta: {title: 'New Tutorial'}},
        {   path: 'tutorials/:id/edit',     name: 'publishing-tutorial-edit',   component: tutorialForm, meta: {title: 'Edit Tutorial'}}
    ];

    export default publishingRoutes;
    ```
    </details>

    ```
    git add .
    git commit -m "feat(publishing): add route for editing a tutorial."
    ```

26. **Add an edit action to `tutorial-list`.**

    ```javascript
    const navigateToEdit = (id) => {
      router.push({ name: 'publishing-tutorial-edit', params: { id } });
    };
    ```

    ```vue
    <pv-column :header="t('tutorials.actions')">
      <template #body="slotProps">
        <pv-button icon="pi pi-pencil" text rounded @click="navigateToEdit(slotProps.data.id)" />
      </template>
    </pv-column>
    ```

    <details>
    <summary>src/publishing/presentation/views/tutorial-list.vue (so far)</summary>

    ```vue
    <script setup>
    import {useI18n} from "vue-i18n";
    import {useRouter} from "vue-router";
    import usePublishingStore from "../../application/publishing.store.js";
    import {onMounted} from "vue";
    import {storeToRefs} from "pinia";

    const { t } = useI18n();
    const router = useRouter();
    const store = usePublishingStore();
    const {tutorials, tutorialsLoaded, categoriesLoaded, errors} = storeToRefs(store);
    const {fetchTutorials, fetchCategories} = store;

    onMounted(() => {
      if (!categoriesLoaded.value) fetchCategories();
      if (!tutorialsLoaded.value) fetchTutorials();
    });

    const navigateToNew = () => {
      router.push({ name: 'publishing-tutorial-new' });
    };

    const navigateToEdit = (id) => {
      router.push({ name: 'publishing-tutorial-edit', params: { id } });
    };
    </script>

    <template>
      <div class="p-4">
        <h1>{{ t('tutorials.title') }}</h1>
        <pv-button :label="t('tutorials.new')" icon="pi pi-plus" class="mb-3" @click="navigateToNew" />
        <pv-data-table
            :value="tutorials"
            :loading="!tutorialsLoaded"
            striped-rows
            table-style="min-width: 50rem"
            paginator
            :rows="5"
            :rows-per-page-options="[5, 10, 20]"
        >
          <pv-column field="id" :header="t('tutorials.id')" sortable />
          <pv-column field="title" :header="t('tutorial.title')" sortable />
          <pv-column field="summary" :header="t('tutorials.summary')" />
          <pv-column :header="t('tutorial.category')">
            <template #body="slotProps">{{ slotProps.data.category?.name }}</template>
          </pv-column>
          <pv-column :header="t('tutorials.actions')">
            <template #body="slotProps">
              <pv-button icon="pi pi-pencil" text rounded @click="navigateToEdit(slotProps.data.id)" />
            </template>
          </pv-column>
        </pv-data-table>
        <div v-if="errors.length" class="text-red-500 mt-3">
          {{ t('errors.occurred') }}: {{ errors.join(', ') }}
        </div>
      </div>
    </template>

    <style scoped>

    </style>
    ```
    </details>

    ```
    git add .
    git commit -m "feat(publishing): add edit action to tutorial-list."
    ```

27. **Add `tutorial.edit-title` and `tutorials.actions` to the English dictionary.**

    <details>
    <summary>src/locales/en.json (so far)</summary>

    ```json
    {
      "option": {
        "home": "Home",
        "about": "About",
        "categories": "Categories",
        "tutorials": "Tutorials"
      },
      "authoring-phrase": {
        "intro": "Made with",
        "use": "using",
        "author": "by {brand} Developer Team"
      },
      "about": {
        "title": "About Us",
        "content": "ACME Learning Center is an Education Business Platform, part of ACME Corporation."
      },
      "home": {
        "title": "Welcome",
        "content": "Welcome to ACME Learning Center."
      },
      "page-not-found": {
        "title": "Page Not Found",
        "content": "The path {unavailable-route} is not available.",
        "go-home": "Go Home"
      },
      "categories": {
        "title": "Categories",
        "id": "ID",
        "name": "Name",
        "actions": "Actions",
        "new": "New Category",
        "confirm-delete": "Are you sure you want to delete {name}?",
        "delete-header": "Confirm Deletion"
      },
      "category": {
        "new-title": "Create New Category",
        "edit-title": "Edit Category",
        "name": "Name",
        "save": "Save",
        "cancel": "Cancel"
      },
      "tutorials": {
        "title": "Tutorials",
        "id": "ID",
        "summary": "Summary",
        "actions": "Actions",
        "new": "New Tutorial"
      },
      "tutorial": {
        "new-title": "Create New Tutorial",
        "edit-title": "Edit Tutorial",
        "title": "Title",
        "summary": "Summary",
        "category": "Category",
        "category-required": "Please select a category.",
        "save": "Save",
        "cancel": "Cancel"
      },
      "errors": {
        "occurred": "Errors occurred"
      }
    }
    ```
    </details>

    ```
    git add .
    git commit -m "feat(i18n): add edit tutorial texts to the English dictionary."
    ```

28. **Add the same keys to the Spanish dictionary.**

    <details>
    <summary>src/locales/es.json (so far)</summary>

    ```json
    {
      "option": {
        "home": "Inicio",
        "about": "Acerca de",
        "categories": "Categorías",
        "tutorials": "Tutoriales"
      },
      "authoring-phrase": {
        "intro": "Hecho con",
        "use": "utilizando",
        "author": "por el Equipo de Desarrollo de {brand}"
      },
      "about": {
        "title": "Acerca de Nosotros",
        "content": "ACME Learning Center es una Plataforma educativa, parte de ACME Corporation."
      },
      "home": {
        "title": "Inicio",
        "content": "Bienvenido a ACME Learning Center."
      },
      "page-not-found": {
        "title": "Página no encontrada",
        "content": "La ruta {unavailable-route} no está disponible.",
        "go-home": "Ir al Inicio"
      },
      "categories": {
        "title": "Categorías",
        "id": "ID",
        "name": "Nombre",
        "actions": "Acciones",
        "new": "Nueva Categoría",
        "confirm-delete": "¿Estás seguro de que quieres eliminar {name}?",
        "delete-header": "Confirmar Eliminación"
      },
      "category": {
        "new-title": "Crear Nueva Categoría",
        "edit-title": "Editar Categoría",
        "name": "Nombre",
        "save": "Guardar",
        "cancel": "Cancelar"
      },
      "tutorials": {
        "title": "Tutoriales",
        "id": "ID",
        "summary": "Resumen",
        "actions": "Acciones",
        "new": "Nuevo Tutorial"
      },
      "tutorial": {
        "new-title": "Crear Nuevo Tutorial",
        "edit-title": "Editar Tutorial",
        "title": "Título",
        "summary": "Resumen",
        "category": "Categoría",
        "category-required": "Por favor selecciona una categoría.",
        "save": "Guardar",
        "cancel": "Cancelar"
      },
      "errors": {
        "occurred": "Ocurrieron errores"
      }
    }
    ```
    </details>

    ```
    git add .
    git commit -m "feat(i18n): add edit tutorial texts to the Spanish dictionary."
    ```

29. **Run it.** Click the pencil icon on a tutorial row: the form opens pre-filled, including the right category, `Save` updates it in place. Stop both servers with `Ctrl+C`.

30. **Add `deleteTutorial` to `usePublishingStore`.**

    ```javascript
    function deleteTutorial(tutorial) {
        publishingApi.deleteTutorial(tutorial.id).then(() => {
            tutorials.value = tutorials.value.filter(t => t.id !== tutorial.id);
        }).catch(error => {
            errors.value.push(error.message);
        });
    }
    ```

    <details>
    <summary>src/publishing/application/publishing.store.js (Full file)</summary>

    ```javascript
    import {defineStore} from "pinia";
    import {computed, ref, shallowRef} from "vue";
    import {PublishingApi} from "../infrastructure/publishing-api.js";
    import {CategoryAssembler} from "../infrastructure/category.assembler.js";
    import {TutorialAssembler} from "../infrastructure/tutorial.assembler.js";
    import {Category} from "../domain/model/category.entity.js";
    import {Tutorial} from "../domain/model/tutorial.entity.js";

    const publishingApi = new PublishingApi();

    const usePublishingStore = defineStore('publishing', () => {
        const categories = shallowRef([]);
        const tutorials = shallowRef([]);
        const errors = ref([]);
        const categoriesLoaded = ref(false);
        const tutorialsLoaded = ref(false);
        const categoriesCount = computed(() => {
            return categoriesLoaded.value ? categories.value.length : 0;
        });
        const tutorialsCount = computed(() => {
            return tutorialsLoaded.value ? tutorials.value.length : 0;
        });

        function withCategory(tutorial) {
            return new Tutorial({
                id: tutorial.id,
                title: tutorial.title,
                summary: tutorial.summary,
                categoryId: tutorial.categoryId,
                category: getCategoryById(tutorial.categoryId) ?? null
            });
        }

        function fetchCategories() {
            publishingApi.getCategories().then(response => {
                categories.value = CategoryAssembler.toEntitiesFromResponse(response);
                categoriesLoaded.value = true;
                tutorials.value = tutorials.value.map(withCategory);
            }).catch(error => {
                errors.value.push(error.message);
            });
        }

        function fetchTutorials() {
            publishingApi.getTutorials().then(response => {
                tutorials.value = TutorialAssembler.toEntitiesFromResponse(response).map(withCategory);
                tutorialsLoaded.value = true;
            }).catch(error => {
                errors.value.push(error.message);
            });
        }

        function getCategoryById(id) {
            const idNum = parseInt(id);
            return categories.value.find(category => category.id === idNum);
        }

        function addCategory(category) {
            publishingApi.createCategory(CategoryAssembler.toResourceFromEntity(category)).then(response => {
                const newCategory = CategoryAssembler.toEntityFromResource(response.data);
                categories.value = [...categories.value, newCategory];
            }).catch(error => {
                errors.value.push(error.message);
            });
        }

        function updateCategory(category) {
            publishingApi.updateCategory(CategoryAssembler.toResourceFromEntity(category)).then(response => {
                const updatedCategory = CategoryAssembler.toEntityFromResource(response.data);
                categories.value = categories.value.map(c => c.id === updatedCategory.id ? updatedCategory : c);
                tutorials.value = tutorials.value.map(withCategory);
            }).catch(error => {
                errors.value.push(error.message);
            });
        }

        function deleteCategory(category) {
            publishingApi.deleteCategory(category.id).then(() => {
                categories.value = categories.value.filter(c => c.id !== category.id);
                tutorials.value = tutorials.value.map(withCategory);
            }).catch(error => {
                errors.value.push(error.message);
            });
        }

        function getTutorialById(id) {
            const idNum = parseInt(id);
            return tutorials.value.find(tutorial => tutorial.id === idNum);
        }

        function addTutorial(tutorial) {
            publishingApi.createTutorial(TutorialAssembler.toResourceFromEntity(tutorial)).then(response => {
                const newTutorial = withCategory(TutorialAssembler.toEntityFromResource(response.data));
                tutorials.value = [...tutorials.value, newTutorial];
            }).catch(error => {
                errors.value.push(error.message);
            });
        }

        function updateTutorial(tutorial) {
            publishingApi.updateTutorial(TutorialAssembler.toResourceFromEntity(tutorial)).then(response => {
                const updatedTutorial = withCategory(TutorialAssembler.toEntityFromResource(response.data));
                tutorials.value = tutorials.value.map(t => t.id === updatedTutorial.id ? updatedTutorial : t);
            }).catch(error => {
                errors.value.push(error.message);
            });
        }

        function deleteTutorial(tutorial) {
            publishingApi.deleteTutorial(tutorial.id).then(() => {
                tutorials.value = tutorials.value.filter(t => t.id !== tutorial.id);
            }).catch(error => {
                errors.value.push(error.message);
            });
        }

        return {
            categories,
            tutorials,
            errors,
            categoriesLoaded,
            tutorialsLoaded,
            categoriesCount,
            tutorialsCount,
            fetchCategories,
            fetchTutorials,
            getCategoryById,
            addCategory,
            updateCategory,
            deleteCategory,
            addTutorial,
            updateTutorial,
            deleteTutorial,
            getTutorialById
        }
    });

    export default usePublishingStore;
    ```
    </details>

    ```
    git add .
    git commit -m "feat(publishing): add delete tutorial method to usePublishingStore."
    ```

31. **Add a delete action to `tutorial-list`, with a confirmation prompt.**

    ```javascript
    import {useConfirm} from "primevue";

    const confirm = useConfirm();
    const {deleteTutorial} = store;

    const confirmDelete = (tutorial) => {
      confirm.require({
        message: t('tutorials.confirm-delete', { title: tutorial.title }),
        header: t('tutorials.delete-header'),
        icon: 'pi pi-exclamation-triangle',
        accept: () => { deleteTutorial(tutorial); },
      });
    };
    ```

    <details>
    <summary>src/publishing/presentation/views/tutorial-list.vue (Full file)</summary>

    ```vue
    <script setup>
    import {useI18n} from "vue-i18n";
    import {useRouter} from "vue-router";
    import {useConfirm} from "primevue";
    import usePublishingStore from "../../application/publishing.store.js";
    import {onMounted} from "vue";
    import {storeToRefs} from "pinia";

    const { t } = useI18n();
    const router = useRouter();
    const confirm = useConfirm();
    const store = usePublishingStore();
    const {tutorials, tutorialsLoaded, categoriesLoaded, errors} = storeToRefs(store);
    const {fetchTutorials, fetchCategories, deleteTutorial} = store;

    onMounted(() => {
      if (!categoriesLoaded.value) fetchCategories();
      if (!tutorialsLoaded.value) fetchTutorials();
    });

    const navigateToNew = () => {
      router.push({ name: 'publishing-tutorial-new' });
    };

    const navigateToEdit = (id) => {
      router.push({ name: 'publishing-tutorial-edit', params: { id } });
    };

    const confirmDelete = (tutorial) => {
      confirm.require({
        message: t('tutorials.confirm-delete', { title: tutorial.title }),
        header: t('tutorials.delete-header'),
        icon: 'pi pi-exclamation-triangle',
        accept: () => { deleteTutorial(tutorial); },
      });
    };
    </script>

    <template>
      <div class="p-4">
        <h1>{{ t('tutorials.title') }}</h1>
        <pv-button :label="t('tutorials.new')" icon="pi pi-plus" class="mb-3" @click="navigateToNew" />
        <pv-data-table
            :value="tutorials"
            :loading="!tutorialsLoaded"
            striped-rows
            table-style="min-width: 50rem"
            paginator
            :rows="5"
            :rows-per-page-options="[5, 10, 20]"
        >
          <pv-column field="id" :header="t('tutorials.id')" sortable />
          <pv-column field="title" :header="t('tutorial.title')" sortable />
          <pv-column field="summary" :header="t('tutorials.summary')" />
          <pv-column :header="t('tutorial.category')">
            <template #body="slotProps">{{ slotProps.data.category?.name }}</template>
          </pv-column>
          <pv-column :header="t('tutorials.actions')">
            <template #body="slotProps">
              <pv-button icon="pi pi-pencil" text rounded @click="navigateToEdit(slotProps.data.id)" />
              <pv-button icon="pi pi-trash" text rounded severity="danger" @click="confirmDelete(slotProps.data)" />
            </template>
          </pv-column>
        </pv-data-table>
        <div v-if="errors.length" class="text-red-500 mt-3">
          {{ t('errors.occurred') }}: {{ errors.join(', ') }}
        </div>
      </div>
    </template>

    <style scoped>

    </style>
    ```
    </details>

    ```
    git add .
    git commit -m "feat(publishing): add delete action to tutorial-list."
    ```

32. **Add `tutorials.confirm-delete` and `tutorials.delete-header` to the English dictionary.**

    <details>
    <summary>src/locales/en.json</summary>

    ```json
    {
      "option": {
        "home": "Home",
        "about": "About",
        "categories": "Categories",
        "tutorials": "Tutorials"
      },
      "authoring-phrase": {
        "intro": "Made with",
        "use": "using",
        "author": "by {brand} Developer Team"
      },
      "about": {
        "title": "About Us",
        "content": "ACME Learning Center is an Education Business Platform, part of ACME Corporation."
      },
      "home": {
        "title": "Welcome",
        "content": "Welcome to ACME Learning Center."
      },
      "page-not-found": {
        "title": "Page Not Found",
        "content": "The path {unavailable-route} is not available.",
        "go-home": "Go Home"
      },
      "categories": {
        "title": "Categories",
        "id": "ID",
        "name": "Name",
        "actions": "Actions",
        "new": "New Category",
        "confirm-delete": "Are you sure you want to delete {name}?",
        "delete-header": "Confirm Deletion"
      },
      "category": {
        "new-title": "Create New Category",
        "edit-title": "Edit Category",
        "name": "Name",
        "save": "Save",
        "cancel": "Cancel"
      },
      "tutorials": {
        "title": "Tutorials",
        "id": "ID",
        "summary": "Summary",
        "actions": "Actions",
        "new": "New Tutorial",
        "confirm-delete": "Are you sure you want to delete {title}?",
        "delete-header": "Confirm Deletion"
      },
      "tutorial": {
        "new-title": "Create New Tutorial",
        "edit-title": "Edit Tutorial",
        "title": "Title",
        "summary": "Summary",
        "category": "Category",
        "category-required": "Please select a category.",
        "save": "Save",
        "cancel": "Cancel"
      },
      "errors": {
        "occurred": "Errors occurred"
      }
    }
    ```
    </details>

    ```
    git add .
    git commit -m "feat(i18n): add delete tutorial texts to the English dictionary."
    ```

33. **Add the same keys to the Spanish dictionary.** This is the final content of both dictionaries for this story.

    <details>
    <summary>src/locales/es.json</summary>

    ```json
    {
      "option": {
        "home": "Inicio",
        "about": "Acerca de",
        "categories": "Categorías",
        "tutorials": "Tutoriales"
      },
      "authoring-phrase": {
        "intro": "Hecho con",
        "use": "utilizando",
        "author": "por el Equipo de Desarrollo de {brand}"
      },
      "about": {
        "title": "Acerca de Nosotros",
        "content": "ACME Learning Center es una Plataforma educativa, parte de ACME Corporation."
      },
      "home": {
        "title": "Inicio",
        "content": "Bienvenido a ACME Learning Center."
      },
      "page-not-found": {
        "title": "Página no encontrada",
        "content": "La ruta {unavailable-route} no está disponible.",
        "go-home": "Ir al Inicio"
      },
      "categories": {
        "title": "Categorías",
        "id": "ID",
        "name": "Nombre",
        "actions": "Acciones",
        "new": "Nueva Categoría",
        "confirm-delete": "¿Estás seguro de que quieres eliminar {name}?",
        "delete-header": "Confirmar Eliminación"
      },
      "category": {
        "new-title": "Crear Nueva Categoría",
        "edit-title": "Editar Categoría",
        "name": "Nombre",
        "save": "Guardar",
        "cancel": "Cancelar"
      },
      "tutorials": {
        "title": "Tutoriales",
        "id": "ID",
        "summary": "Resumen",
        "actions": "Acciones",
        "new": "Nuevo Tutorial",
        "confirm-delete": "¿Estás seguro de que quieres eliminar {title}?",
        "delete-header": "Confirmar Eliminación"
      },
      "tutorial": {
        "new-title": "Crear Nuevo Tutorial",
        "edit-title": "Editar Tutorial",
        "title": "Título",
        "summary": "Resumen",
        "category": "Categoría",
        "category-required": "Por favor selecciona una categoría.",
        "save": "Guardar",
        "cancel": "Cancelar"
      },
      "errors": {
        "occurred": "Ocurrieron errores"
      }
    }
    ```
    </details>

    ```
    git add .
    git commit -m "feat(i18n): add delete tutorial texts to the Spanish dictionary."
    ```

34. **Run it.** Click the trash icon on a tutorial: the dialog names it, accept deletes it. Delete a category from `Categories` instead: its tutorials stay in the list, with an empty category cell, `withCategory` re-runs and finds nothing for that `categoryId` any more. Stop both servers with `Ctrl+C`.

    ```
    git checkout server/db.json
    ```

35. **Publish and finish the feature.**

---

## Register a New Account (US003)

A visitor creates an account with a username and a password. This story starts the IAM bounded context, a generic subdomain: it carries none of the Learning Center's own business rules, which is why it comes after every Publishing feature. It builds the `User` entity, the sign-up command, the infrastructure that posts the credentials, the first half of `useIamStore`, and the `sign-up-form` view.

**Note:** the fake API from Project Setup serves only `categories` and `tutorials`. It has no `/authentication/sign-up` or `/authentication/sign-in` endpoint, so a registration cannot succeed against it: the request goes out and `json-server` answers `404`. That is expected, nothing in this story or the next one is wrong when it happens. The `Run it` steps show what you can check without a backend: the form, its validation, the request in the browser's Network tab, and how the store reacts to the failure. A real backend, the Learning Center Platform the environment files point to, answers both endpoints.

1. **Start the feature `register-a-new-account`.**

2. **See the component tree this story builds.** One more routed view joins the shell, `sign-up-form`. Like the Publishing views it has no props and no events: it collects two values, hands them to `useIamStore`, and the router decides when `<router-view/>` shows it.

   ```
   +-----+
   | app |
   +-----+
       |
       +-----------------------------+
       | layout                      |
       | State: drawer: Ref<boolean> |
       | (no Input/Output)           |
       +-----------------------------+
           |
           +--------------------------------------------+
           | language-switcher                          |
           | (no Input/Output, uses useI18n() directly) |
           +--------------------------------------------+
           |
           +------------------------------------------------------------------+
           | <router-view/>  one routed view at a time                        |
           | home, about, page-not-found, category-list, category-form,      |
           | tutorial-list, tutorial-form, sign-up-form                       |
           | (no Input/Output, sign-up-form uses useIamStore() directly)      |
           +------------------------------------------------------------------+
           |
           +--------------------------------------------+
           | footer-content                             |
           | (no Input/Output, uses useI18n() directly) |
           +--------------------------------------------+
   ```

3. **Create the `User` entity, fields and constructor.** Right-click `src` → `New` → `JavaScript File` → type `iam/domain/user.entity` → Enter (WebStorm creates the `iam/domain` folder with it). A user is an `id` and a `username`, private fields (`#`) filled by the constructor, like `Category`. The password is not part of it: the entity is what the application knows about an account, and the application never keeps a password.

   <details>
   <summary>src/iam/domain/user.entity.js (so far)</summary>

   ```javascript
   export class User {
       #id;
       #username;

       constructor({id, username}) {
           this.#id = id;
           this.#username = username;
       }
   }
   ```
   </details>

   **Note:** no commit here, `User` still needs its read accessors.

4. **Add the read accessors.** One getter per field, no setters, the same rule as `Category` and `Tutorial`.

   <details>
   <summary>src/iam/domain/user.entity.js (Full file)</summary>

   ```javascript
   export class User {
       #id;
       #username;

       constructor({id, username}) {
           this.#id = id;
           this.#username = username;
       }

       get id() {
           return this.#id;
       }

       get username() {
           return this.#username;
       }
   }
   ```
   </details>

   ```
   git add .
   git commit -m "feat(iam): add User entity."
   ```

5. **Create the `SignUpCommand`, fields and constructor.** Right-click `src` → `New` → `JavaScript File` → type `iam/domain/sign-up.command` → Enter. A command is not an entity, it carries no identity, just the two values a sign-up needs.

   <details>
   <summary>src/iam/domain/sign-up.command.js (so far)</summary>

   ```javascript
   export class SignUpCommand {
       #username;
       #password;

       constructor({username, password}) {
           this.#username = username;
           this.#password = password;
       }
   }
   ```
   </details>

   **Note:** no commit here, `SignUpCommand` still needs its read accessors.

6. **Add the read accessors.**

   <details>
   <summary>src/iam/domain/sign-up.command.js (Full file)</summary>

   ```javascript
   export class SignUpCommand {
       #username;
       #password;

       constructor({username, password}) {
           this.#username = username;
           this.#password = password;
       }

       get username() {
           return this.#username;
       }

       get password() {
           return this.#password;
       }
   }
   ```
   </details>

   ```
   git add .
   git commit -m "feat(iam): add SignUpCommand."
   ```

7. **Create the `SignUpResource`.** Right-click `src` → `New` → `JavaScript File` → type `iam/infrastructure/sign-up.resource` → Enter. What the sign-up endpoint answers with: a confirmation message, nothing else, plain public fields, not an entity.

   <details>
   <summary>src/iam/infrastructure/sign-up.resource.js</summary>

   ```javascript
   export class SignUpResource {
       constructor({message}) {
           this.message = message;
       }
   }
   ```
   </details>

   ```
   git add .
   git commit -m "feat(iam): add SignUpResource."
   ```

8. **Create the `SignUpAssembler`.** Right-click `src` → `New` → `JavaScript File` → type `iam/infrastructure/sign-up.assembler` → Enter. `toRequestFromCommand` builds what goes out over HTTP; `toResourceFromResponse` builds what comes back, the same shape `CategoryAssembler` and `TutorialAssembler` follow.

   <details>
   <summary>src/iam/infrastructure/sign-up.assembler.js</summary>

   ```javascript
   import {SignUpResource} from "./sign-up.resource.js";

   export class SignUpAssembler {
       static toRequestFromCommand(command) {
           return {username: command.username, password: command.password};
       }

       static toResourceFromResponse(response) {
           if (response.status !== 200) {
               console.error(`${response.status}, ${response.statusText}`);
               return null;
           }
           return new SignUpResource(response.data);
       }
   }
   ```
   </details>

   ```
   git add .
   git commit -m "feat(iam): add SignUpAssembler."
   ```

9. **Create `IamApi`, sign-up only for now.** Right-click `src` → `New` → `JavaScript File` → type `iam/infrastructure/iam-api` → Enter. It extends `BaseApi` and composes one `BaseEndpoint`, the same pattern `PublishingApi` follows, sign-in and the user list join it in the next story.

   <details>
   <summary>src/iam/infrastructure/iam-api.js (so far)</summary>

   ```javascript
   import {BaseEndpoint} from "../../shared/infrastructure/base-endpoint.js";
   import {BaseApi} from "../../shared/infrastructure/base-api.js";
   const signUpEndpointPath = import.meta.env.VITE_SIGNUP_ENDPOINT_PATH;

   export class IamApi extends BaseApi {
       #signUpEndpoint;

       constructor() {
           super();
           this.#signUpEndpoint = new BaseEndpoint(this, signUpEndpointPath);
       }

       signUp(signUpRequest) {
           return this.#signUpEndpoint.create(signUpRequest);
       }
   }
   ```
   </details>

   ```
   git add .
   git commit -m "feat(iam): add IamApi with sign-up."
   ```

10. **Create `useIamStore`, sign-up only for now.** Right-click `src` → `New` → `JavaScript File` → type `iam/application/iam.store` → Enter. A Pinia setup store, the same shape as `usePublishingStore`: `errors` in a plain `ref`, the action reads `IamApi` through the assembler.

    <details>
    <summary>src/iam/application/iam.store.js (so far)</summary>

    ```javascript
    import {IamApi} from "../infrastructure/iam-api.js";
    import {defineStore} from "pinia";
    import {ref} from "vue";
    import {SignUpAssembler} from "../infrastructure/sign-up.assembler.js";

    const iamApi = new IamApi();

    const useIamStore = defineStore('iam', () => {
        const errors = ref([]);

        function signUp(signUpCommand, router) {
            iamApi.signUp(SignUpAssembler.toRequestFromCommand(signUpCommand))
                .then(response => {
                    const signUpResource = SignUpAssembler.toResourceFromResponse(response);
                    if (signUpResource) {
                        errors.value = [];
                        router.push({name: 'iam-sign-in'});
                    } else {
                        errors.value.push('Sign-up failed');
                        router.push({name: 'iam-sign-up'});
                    }
                })
                .catch(error => {
                    errors.value.push(error.message);
                    router.push({name: 'iam-sign-up'});
                });
        }

        return {
            errors,
            signUp
        };
    });

    export default useIamStore;
    ```
    </details>

    **Note:** `signUp` already routes to `iam-sign-in` on success and stays on `iam-sign-up` on failure, even though neither route exists yet, `sign-up-form` and its route join in a few steps, `sign-in-form` and its route in the next story. Against the fake API every attempt rejects with a `404`, so only the `.catch()` branch ever actually runs right now.

    ```
    git add .
    git commit -m "feat(iam): add useIamStore with sign-up."
    ```

11. **Register `FloatLabel` in `main.js`.** `sign-up-form` floats its labels above the inputs with `pv-float-label`, the last PrimeVue component this project registers.

    ```javascript
    import {
        Button,
        Column,
        ConfirmationService,
        ConfirmDialog,
        DataTable,
        Drawer,
        FloatLabel,
        InputText,
        Select,
        SelectButton,
        Textarea,
        Toolbar
    } from "primevue";
    ```

    ```javascript
    createApp(App)
        .component('pv-float-label',    FloatLabel)
    ```

    <details>
    <summary>src/main.js</summary>

    ```javascript
    import {createApp} from 'vue'
    import './style.css'
    import App from './app.vue'
    import i18n from "./i18n.js";
    import PrimeVue from 'primevue/config';
    import Material from '@primeuix/themes/material';
    import 'primeflex/primeflex.css';
    import 'primeicons/primeicons.css';
    import {
        Button,
        Column,
        ConfirmationService,
        ConfirmDialog,
        DataTable,
        Drawer,
        FloatLabel,
        InputText,
        Select,
        SelectButton,
        Textarea,
        Toolbar
    } from "primevue";
    import router from "./router.js";
    import pinia from "./pinia.js";

    const primeUiLicenseKey = import.meta.env.VITE_PRIME_UI_LICENSE_KEY;

    createApp(App)
        .use(i18n)
        .use(PrimeVue, {theme: {preset: Material}, ripple: true, license: primeUiLicenseKey})
        .use(ConfirmationService)
        .component('pv-button',         Button)
        .component('pv-column',         Column)
        .component('pv-confirm-dialog', ConfirmDialog)
        .component('pv-data-table',     DataTable)
        .component('pv-drawer',         Drawer)
        .component('pv-float-label',    FloatLabel)
        .component('pv-input-text',     InputText)
        .component('pv-select',         Select)
        .component('pv-select-button',  SelectButton)
        .component('pv-textarea',       Textarea)
        .component('pv-toolbar',        Toolbar)
        .use(router)
        .use(pinia)
        .mount('#app')
    ```
    </details>

    **Note:** `main.js` reaches its final shape here, nothing registered from this point on needs a new PrimeVue component.

    ```
    git add .
    git commit -m "feat: register the PrimeVue float label."
    ```

12. **Create the `sign-up-form` view.** Right-click `src` → `New` → `Vue Single-File Component` → `Composition API` → type `iam/presentation/views/sign-up-form` → Enter. A plain `reactive()` object for the two fields, `pv-float-label` around each input, a required-field message under each, and a submit that builds a `SignUpCommand` and calls the store.

    <details>
    <summary>src/iam/presentation/views/sign-up-form.vue</summary>

    ```vue
    <script setup>
    import useIamStore from "../../application/iam.store.js";
    import {reactive} from "vue";
    import {SignUpCommand} from "../../domain/sign-up.command.js";
    import {useRouter} from "vue-router";

    const router = useRouter();
    const store = useIamStore();
    const {signUp} = store;
    const form = reactive({
      username: '',
      password: ''
    })
    function performSignUp() {
      const signUpCommand = new SignUpCommand(form);
      signUp(signUpCommand, router);
    }
    </script>

    <template>
      <div>
        <h3>Sign Up</h3>
      </div>
      <p class="p-fluid mb-5">Please enter the required information to sign in.</p>
      <div>
        <form @submit.prevent="performSignUp">
          <div class="p-fluid">
            <div class="field mt-5">
              <pv-float-label>
                <label for="username">Username</label>
                <pv-input-text id="username" v-model="form.username" :class="{'p-invalid': !form.username}"/>
                <small v-if="!form.username" class="p-invalid">Username is required.</small>
              </pv-float-label>
            </div>
            <div class="p-field mt-5">
              <pv-float-label>
                <label for="password">Password</label>
                <pv-input-text id="password" v-model="form.password" :class="{'p-invalid': !form.password}" type="password"/>
                <small v-if="!form.password" class="p-invalid">Password is required.</small>
              </pv-float-label>
            </div>
            <div class="p-field mt-5">
              <pv-button type="submit">Sign Up</pv-button>
            </div>
          </div>
        </form>
      </div>
    </template>

    <style scoped>

    </style>
    ```
    </details>

    **Note:** every other view in this application reads its text through `t('key')`. This one, and `sign-in-form` and `authentication-section` in the next story, do not, their labels are hardcoded English. It is a real gap in this project, not something this guide papers over: `useIamStore`'s two failure messages (`'Sign-up failed'`, `'Sign-in failed'`) are plain strings for the same reason. A later pass could move all three into `src/locales/`, this guide builds what the project actually has.

    ```
    git add .
    git commit -m "feat(iam): add sign-up-form view."
    ```

13. **Add the sign-up route.** Right-click `src` → `New` → `JavaScript File` → type `iam/presentation/iam-routes` → Enter. Same shape as `publishing-routes.js`: lazy-loaded components, one array, exported by default.

    <details>
    <summary>src/iam/presentation/iam-routes.js (so far)</summary>

    ```javascript
    const signUpForm = () => import('./views/sign-up-form.vue')
    const iamRoutes = [
        { path: 'sign-up', name: 'iam-sign-up', component: signUpForm, meta: { title: 'Sign-Up' } }
    ];

    export default iamRoutes;
    ```
    </details>

    ```
    git add .
    git commit -m "feat(iam): add iam routes with sign-up."
    ```

14. **Wire `/iam` into `router.js`.** One more child route, next to `/publishing`. No guard yet, that is the next story.

    ```javascript
    import iamRoutes from "./iam/presentation/iam-routes.js";
    ```

    ```javascript
    { path: '/iam',             name: 'iam',        children: iamRoutes },
    ```

    <details>
    <summary>src/router.js</summary>

    ```javascript
    import {createRouter, createWebHistory} from "vue-router";
    import Home from "./shared/presentation/views/home.vue";
    import publishingRoutes from "./publishing/presentation/publishing-routes.js";
    import iamRoutes from "./iam/presentation/iam-routes.js";

    const about = () => import('./shared/presentation/views/about.vue');
    const pageNotFound = () => import('./shared/presentation/views/page-not-found.vue');
    const routes = [
        { path: '/home',            name: 'home',       component: Home,        meta: { title: 'Home' } },
        { path: '/about',           name: 'about',      component: about,       meta: { title: 'About' } },
        { path: '/publishing',      name: 'publishing', children: publishingRoutes },
        { path: '/iam',             name: 'iam',        children: iamRoutes },
        { path: '/',                redirect: '/home' },
        { path: '/:pathMatch(.*)*', name: 'not-found', component: pageNotFound, meta: { title: 'Page Not Found' } }
    ];

    const router = createRouter({
        history: createWebHistory(import.meta.env.BASE_URL),
        routes: routes
    });

    router.beforeEach((to) => {
        const baseTitle = 'ACME Learning Center';
        document.title = `${baseTitle} - ${to.meta['title']}`;
    });

    export default router;
    ```
    </details>

    ```
    git add .
    git commit -m "feat: wire iam routes into the router."
    ```

15. **Run it.** In one terminal:

    ```
    npx json-server --watch server/db.json --routes server/routes.json --port 3000
    ```

    In another:

    ```
    npm run dev
    ```

    Open the local URL Vite prints, then navigate to `/iam/sign-up` by hand in the address bar, there is no menu entry yet. Leave both fields empty and submit: both required messages show, nothing is sent. Fill both in and submit: open the browser's DevTools → Network tab first, then submit again, `POST /authentication/sign-up` shows a `404` response, exactly what the Note at the top of this story said would happen. Stop both servers with `Ctrl+C`.

    ```
    git checkout server/db.json
    ```

16. **Publish and finish the feature.**

---

## Sign In and Manage the Session (US004)

A registered user signs in, the toolbar recognizes them, and every protected screen stays out of reach until they do. This story finishes the IAM context: the sign-in command, the resources and assembler for the authentication response, `UserAssembler`, the session half of `useIamStore`, `sign-in-form`, `authenticationGuard`, `iamInterceptor`, and `authentication-section` in the toolbar.

1. **Start the feature `sign-in-and-manage-the-session`.**

2. **See the component tree this story builds.** `authentication-section` joins the toolbar, next to `language-switcher`, it has no props or events either, it reads and writes `useIamStore` directly. `sign-in-form` joins the routed views the same way `sign-up-form` did. From this story on, every route but `/iam/sign-in`, `/iam/sign-up`, `/about`, and the not-found page is protected.

   ```
   +-----+
   | app |
   +-----+
       |
       +-----------------------------+
       | layout                      |
       | State: drawer: Ref<boolean> |
       | (no Input/Output)           |
       +-----------------------------+
           |
           +----------------------------------------------------+
           | authentication-section                              |
           | (no Input/Output, reads/writes useIamStore()        |
           |  directly: isSignedIn, currentUsername, signOut()) |
           +----------------------------------------------------+
           |
           +--------------------------------------------+
           | language-switcher                          |
           | (no Input/Output, uses useI18n() directly) |
           +--------------------------------------------+
           |
           +----------------------------------------------------------------------+
           | <router-view/>  one routed view at a time                            |
           | home, about, page-not-found, category-list, category-form,          |
           | tutorial-list, tutorial-form, sign-up-form, sign-in-form              |
           | (no Input/Output, sign-up-form and sign-in-form use                  |
           |  useIamStore() directly; every route but /iam/*, /about, and         |
           |  page-not-found now goes through authenticationGuard)                |
           +----------------------------------------------------------------------+
           |
           +--------------------------------------------+
           | footer-content                             |
           | (no Input/Output, uses useI18n() directly) |
           +--------------------------------------------+
   ```

3. **Create the `SignInCommand`, fields and constructor.** Right-click `src` → `New` → `JavaScript File` → type `iam/domain/sign-in.command` → Enter. Same shape as `SignUpCommand`, a username and a password.

   <details>
   <summary>src/iam/domain/sign-in.command.js (so far)</summary>

   ```javascript
   export class SignInCommand {
       #username;
       #password;

       constructor({username, password}) {
           this.#username = username;
           this.#password = password;
       }
   }
   ```
   </details>

   **Note:** no commit here, `SignInCommand` still needs its read accessors.

4. **Add the read accessors.**

   <details>
   <summary>src/iam/domain/sign-in.command.js (Full file)</summary>

   ```javascript
   export class SignInCommand {
       #username;
       #password;

       constructor({username, password}) {
           this.#username = username;
           this.#password = password;
       }

       get username() {
           return this.#username;
       }

       get password() {
           return this.#password;
       }
   }
   ```
   </details>

   ```
   git add .
   git commit -m "feat(iam): add SignInCommand."
   ```

5. **Create the `SignInResource`.** Right-click `src` → `New` → `JavaScript File` → type `iam/infrastructure/sign-in.resource` → Enter. What a successful sign-in answers with: the account's `id` and `username`, plus the bearer `token` this session uses from now on.

   <details>
   <summary>src/iam/infrastructure/sign-in.resource.js</summary>

   ```javascript
   export class SignInResource {
       constructor({id, username, token}) {
           this.id = id;
           this.username = username;
           this.token = token;
       }
   }
   ```
   </details>

   ```
   git add .
   git commit -m "feat(iam): add SignInResource."
   ```

6. **Create the `SignInAssembler`.** Right-click `src` → `New` → `JavaScript File` → type `iam/infrastructure/sign-in.assembler` → Enter. Same shape as `SignUpAssembler`.

   <details>
   <summary>src/iam/infrastructure/sign-in.assembler.js</summary>

   ```javascript
   import {SignInResource} from "./sign-in.resource.js";

   export class SignInAssembler {
       static toRequestFromCommand(command) {
           return {username: command.username, password: command.password};
       }

       static toResourceFromResponse(response) {
           if (response.status !== 200) {
               console.error(`${response.status}, ${response.statusText}`);
               return null;
           }
           return new SignInResource(response.data);
       }
   }
   ```
   </details>

   ```
   git add .
   git commit -m "feat(iam): add SignInAssembler."
   ```

7. **Create the `UserAssembler`.** Right-click `src` → `New` → `JavaScript File` → type `iam/infrastructure/user.assembler` → Enter. It builds a `User` from a `SignInResource` (a sign-in response has `id` and `username` too), and from a plain user resource when listing every account.

   <details>
   <summary>src/iam/infrastructure/user.assembler.js</summary>

   ```javascript
   import {User} from "../domain/user.entity.js";

   export class UserAssembler {
       static toEntityFromResource(resource) {
           return new User({...resource});
       }

       static toEntitiesFromResponse(response) {
           if (response.status !== 200) {
               console.error(`${response.status}, ${response.statusText}`);
               return [];
           }
           let resources = response.data instanceof Array ? response.data : response.data['users'];

           return resources.map(resource => this.toEntityFromResource(resource));
       }
   }
   ```
   </details>

   ```
   git add .
   git commit -m "feat(iam): add UserAssembler."
   ```

8. **Extend `IamApi` with sign-in and the user list.** `#signInEndpoint` joins `#signUpEndpoint`, and a third endpoint, `#usersEndpoint`, reads a list of registered accounts. Nothing in this course's UI calls `getUsers()`, the real Learning Center Platform would use it for an account-management screen this guide does not build; it stays because it is part of the real `IamApi`.

   ```javascript
   const signInEndpointPath = import.meta.env.VITE_SIGNIN_ENDPOINT_PATH;
   const usersEndpointPath   = import.meta.env.VITE_USERS_ENDPOINT_PATH;
   ```

   ```javascript
   signIn(signInRequest) {
       return this.#signInEndpoint.create(signInRequest);
   }

   getUsers() {
       return this.#usersEndpoint.getAll();
   }
   ```

   <details>
   <summary>src/iam/infrastructure/iam-api.js (Full file)</summary>

   ```javascript
   import {BaseEndpoint} from "../../shared/infrastructure/base-endpoint.js";
   import {BaseApi} from "../../shared/infrastructure/base-api.js";
   const signInEndpointPath = import.meta.env.VITE_SIGNIN_ENDPOINT_PATH;
   const signUpEndpointPath = import.meta.env.VITE_SIGNUP_ENDPOINT_PATH;
   const usersEndpointPath   = import.meta.env.VITE_USERS_ENDPOINT_PATH;

   export class IamApi extends BaseApi {
       #signInEndpoint;
       #signUpEndpoint;
       #usersEndpoint;

       constructor() {
           super();
           this.#signInEndpoint = new BaseEndpoint(this, signInEndpointPath);
           this.#signUpEndpoint = new BaseEndpoint(this, signUpEndpointPath);
           this.#usersEndpoint = new BaseEndpoint(this, usersEndpointPath);
       }

       signIn(signInRequest) {
           return this.#signInEndpoint.create(signInRequest);
       }

       signUp(signUpRequest) {
           return this.#signUpEndpoint.create(signUpRequest);
       }

       getUsers() {
           return this.#usersEndpoint.getAll();
       }
   }
   ```
   </details>

   ```
   git add .
   git commit -m "feat(iam): extend IamApi with sign-in and the user list."
   ```

9. **Extend `useIamStore` with the session and the user list.** `isSignedIn`, `currentUsername`, and `currentUserId` are plain `ref`s; `currentToken` is `computed` from `localStorage`, so it disappears the moment `isSignedIn` goes false, without a second place to clear it. `users` is a `shallowRef`, the same reason `usePublishingStore` keeps `categories` and `tutorials` there: `User` holds its state in `#` private fields, and Vue must never wrap one in a Proxy (ADR-0004, written in `## Release` below).

   ```javascript
   import {computed, ref, shallowRef} from "vue";
   import {SignInAssembler} from "../infrastructure/sign-in.assembler.js";
   import {UserAssembler} from "../infrastructure/user.assembler.js";
   ```

   ```javascript
   function signIn(signInCommand, router) {
       iamApi.signIn(SignInAssembler.toRequestFromCommand(signInCommand))
           .then(response => {
               const signInResource = SignInAssembler.toResourceFromResponse(response);
               if (signInResource) {
                   const currentUser = UserAssembler.toEntityFromResource(signInResource);
                   currentUsername.value = currentUser.username;
                   currentUserId.value = currentUser.id;
                   localStorage.setItem('token', signInResource.token);
                   isSignedIn.value = true;
                   errors.value = [];
                   router.push({name: 'home'});
               } else {
                   isSignedIn.value = false;
                   errors.value.push('Sign-in failed');
                   router.push({name: 'iam-sign-in'});
               }
           })
           .catch(error => {
               isSignedIn.value = false;
               errors.value.push(error.message);
               router.push({name: 'iam-sign-in'});
           });
   }

   function signOut(router) {
       currentUsername.value = null;
       currentUserId.value = 0;
       localStorage.removeItem('token');
       isSignedIn.value = false;
       errors.value = [];
       router.push({name: 'iam-sign-in'});
   }

   function fetchUsers() {
       iamApi.getUsers().then(response => {
           users.value = UserAssembler.toEntitiesFromResponse(response);
           usersLoaded.value = true;
           errors.value = [];
       }).catch(error => {
           errors.value.push(error.message);
       });
   }
   ```

   <details>
   <summary>src/iam/application/iam.store.js (Full file)</summary>

   ```javascript
   import {IamApi} from "../infrastructure/iam-api.js";
   import {defineStore} from "pinia";
   import {computed, ref, shallowRef} from "vue";
   import {SignInAssembler} from "../infrastructure/sign-in.assembler.js";
   import {UserAssembler} from "../infrastructure/user.assembler.js";
   import {SignUpAssembler} from "../infrastructure/sign-up.assembler.js";

   const iamApi = new IamApi();

   const useIamStore = defineStore('iam', () => {
       const users = shallowRef([]);
       const errors = ref([]);
       const usersLoaded = ref(false);
       const isSignedIn = ref(false);
       const currentUsername = ref(null);
       const currentUserId = ref(0);
       const currentToken = computed(() => isSignedIn.value ? localStorage.getItem('token') : null);

       function signIn(signInCommand, router) {
           iamApi.signIn(SignInAssembler.toRequestFromCommand(signInCommand))
               .then(response => {
                   const signInResource = SignInAssembler.toResourceFromResponse(response);
                   if (signInResource) {
                       const currentUser = UserAssembler.toEntityFromResource(signInResource);
                       currentUsername.value = currentUser.username;
                       currentUserId.value = currentUser.id;
                       localStorage.setItem('token', signInResource.token);
                       isSignedIn.value = true;
                       errors.value = [];
                       router.push({name: 'home'});
                   } else {
                       isSignedIn.value = false;
                       errors.value.push('Sign-in failed');
                       router.push({name: 'iam-sign-in'});
                   }
               })
               .catch(error => {
                   isSignedIn.value = false;
                   errors.value.push(error.message);
                   router.push({name: 'iam-sign-in'});
               });
       }

       function signUp(signUpCommand, router) {
           iamApi.signUp(SignUpAssembler.toRequestFromCommand(signUpCommand))
               .then(response => {
                   const signUpResource = SignUpAssembler.toResourceFromResponse(response);
                   if (signUpResource) {
                       errors.value = [];
                       router.push({name: 'iam-sign-in'});
                   } else {
                       errors.value.push('Sign-up failed');
                       router.push({name: 'iam-sign-up'});
                   }
               })
               .catch(error => {
                   errors.value.push(error.message);
                   router.push({name: 'iam-sign-up'});
               });
       }

       function signOut(router) {
           currentUsername.value = null;
           currentUserId.value = 0;
           localStorage.removeItem('token');
           isSignedIn.value = false;
           errors.value = [];
           router.push({name: 'iam-sign-in'});
       }

       function fetchUsers() {
           iamApi.getUsers().then(response => {
               users.value = UserAssembler.toEntitiesFromResponse(response);
               usersLoaded.value = true;
               errors.value = [];
           }).catch(error => {
               errors.value.push(error.message);
           });
       }

       return {
           users,
           errors,
           usersLoaded,
           currentUsername,
           currentUserId,
           currentToken,
           isSignedIn,
           signIn,
           signUp,
           signOut,
           fetchUsers
       };
   });

   export default useIamStore;
   ```
   </details>

   ```
   git add .
   git commit -m "feat(iam): extend useIamStore with the session and the user list."
   ```

10. **Create the `sign-in-form` view.** Right-click `src` → `New` → `Vue Single-File Component` → `Composition API` → type `iam/presentation/views/sign-in-form` → Enter. The same shape as `sign-up-form`, one field fewer to explain: it builds a `SignInCommand` and calls `store.signIn`.

    <details>
    <summary>src/iam/presentation/views/sign-in-form.vue</summary>

    ```vue
    <script setup>
      import useIamStore from "../../application/iam.store.js";
      import {reactive} from "vue";
      import {SignInCommand} from "../../domain/sign-in.command.js";
      import {useRouter} from "vue-router";

      const router = useRouter();
      const store = useIamStore();
      const {signIn} = store;
      const form = reactive({
        username: '',
        password: ''
      })
      function performSignIn() {
        const signInCommand = new SignInCommand(form);
        signIn(signInCommand, router);
      }
    </script>

    <template>
      <div>
        <h3>Sign In</h3>
      </div>
      <p class="p-fluid mb-5">Please enter the required information to sign in.</p>
      <div>
        <form @submit.prevent="performSignIn">
          <div class="p-fluid">
            <div class="field mt-5">
              <pv-float-label>
                <label for="username">Username</label>
                <pv-input-text id="username" v-model="form.username" :class="{'p-invalid': !form.username}"/>
                <small v-if="!form.username" class="p-invalid">Username is required.</small>
              </pv-float-label>
            </div>
            <div class="p-field mt-5">
              <pv-float-label>
                <label for="password">Password</label>
                <pv-input-text id="password" v-model="form.password" :class="{'p-invalid': !form.password}" type="password"/>
                <small v-if="!form.password" class="p-invalid">Password is required.</small>
              </pv-float-label>
            </div>
            <div class="p-field mt-5">
              <pv-button type="submit">Sign In</pv-button>
            </div>
          </div>
        </form>
      </div>
    </template>

    <style scoped>

    </style>
    ```
    </details>

    ```
    git add .
    git commit -m "feat(iam): add sign-in-form view."
    ```

11. **Add the sign-in route.** It goes first in the array, the way a user reaches it first.

    <details>
    <summary>src/iam/presentation/iam-routes.js (Full file)</summary>

    ```javascript
    const signInForm = () => import('./views/sign-in-form.vue')
    const signUpForm = () => import('./views/sign-up-form.vue')
    const iamRoutes = [
        { path: 'sign-in', name: 'iam-sign-in', component: signInForm, meta: { title: 'Sign-In' } },
        { path: 'sign-up', name: 'iam-sign-up', component: signUpForm, meta: { title: 'Sign-Up' } }
    ];

    export default iamRoutes;
    ```
    </details>

    ```
    git add .
    git commit -m "feat(iam): add the sign-in route."
    ```

12. **Create `authenticationGuard`.** Right-click `src` → `New` → `JavaScript File` → type `iam/infrastructure/authentication.guard` → Enter. `to.path` against a short allow-list, plus the route named `not-found`: an unknown address stays reachable by anyone, so a mistyped URL shows the Page Not Found view instead of bouncing to sign-in.

    <details>
    <summary>src/iam/infrastructure/authentication.guard.js</summary>

    ```javascript
    import useIamStore from "../application/iam.store.js";

    export const authenticationGuard = (to, from) => {
        const store = useIamStore();
        const isAnonymous = !store.isSignedIn;
        const publicRoutes = ['/iam/sign-in', '/iam/sign-up', '/about'];
        const routeRequiresToBeAuthenticated = !publicRoutes.includes(to.path) && to.name !== 'not-found';
        if (isAnonymous && routeRequiresToBeAuthenticated) return {name: 'iam-sign-in'};
        return true;
    }
    ```
    </details>

    **Note:** `to.path` matching means the entries in `publicRoutes` are addresses, not route names, `/iam/sign-in` has to match exactly what `to.path` reports. `from` is not used by this guard, `router.js` passes it along regardless, in the next step.

    ```
    git add .
    git commit -m "feat(iam): add authenticationGuard."
    ```

13. **Wire the guard into `router.js`.** `beforeEach` now takes `from` too, sets the title as before, and returns whatever `authenticationGuard` decides.

    ```javascript
    import {authenticationGuard} from "./iam/infrastructure/authentication.guard.js";
    ```

    ```javascript
    router.beforeEach((to, from) => {
        const baseTitle = 'ACME Learning Center';
        document.title = `${baseTitle} - ${to.meta['title']}`;
        return authenticationGuard(to, from);
    });
    ```

    <details>
    <summary>src/router.js (Full file)</summary>

    ```javascript
    import {createRouter, createWebHistory} from "vue-router";
    import Home from "./shared/presentation/views/home.vue";
    import publishingRoutes from "./publishing/presentation/publishing-routes.js";
    import iamRoutes from "./iam/presentation/iam-routes.js";
    import {authenticationGuard} from "./iam/infrastructure/authentication.guard.js";

    const about = () => import('./shared/presentation/views/about.vue');
    const pageNotFound = () => import('./shared/presentation/views/page-not-found.vue');
    const routes = [
        { path: '/home',            name: 'home',       component: Home,        meta: { title: 'Home' } },
        { path: '/about',           name: 'about',      component: about,       meta: { title: 'About' } },
        { path: '/publishing',      name: 'publishing', children: publishingRoutes },
        { path: '/iam',             name: 'iam',        children: iamRoutes },
        { path: '/',                redirect: '/home' },
        { path: '/:pathMatch(.*)*', name: 'not-found', component: pageNotFound, meta: { title: 'Page Not Found' } }
    ];

    const router = createRouter({
        history: createWebHistory(import.meta.env.BASE_URL),
        routes: routes
    });

    router.beforeEach((to, from) => {
        const baseTitle = 'ACME Learning Center';
        document.title = `${baseTitle} - ${to.meta['title']}`;
        return authenticationGuard(to, from);
    });

    export default router;
    ```
    </details>

    ```
    git add .
    git commit -m "feat: protect the router with authenticationGuard."
    ```

14. **Create `iamInterceptor`.** Right-click `src` → `New` → `JavaScript File` → type `iam/infrastructure/iam.interceptor` → Enter. An Axios request interceptor: when a user is signed in, it stamps every outgoing request with the bearer token `useIamStore` keeps.

    <details>
    <summary>src/iam/infrastructure/iam.interceptor.js</summary>

    ```javascript
    import useIamStore from "../application/iam.store.js";

    export const iamInterceptor = (config) => {
        const store = useIamStore();
        if (store.isSignedIn) {
            config.headers.Authorization = `Bearer ${store.currentToken}`;
        }
        return config;
    }
    ```
    </details>

    ```
    git add .
    git commit -m "feat(iam): add iamInterceptor."
    ```

15. **Register the interceptor in `BaseApi`.** Every API client extends `BaseApi`, so registering the interceptor once here attaches it to `PublishingApi` and `IamApi` alike.

    ```javascript
    import {iamInterceptor} from "../../iam/infrastructure/iam.interceptor.js";
    ```

    ```javascript
    this.#http.interceptors.request.use(iamInterceptor);
    ```

    <details>
    <summary>src/shared/infrastructure/base-api.js (Full file)</summary>

    ```javascript
    import axios from "axios";

    import {iamInterceptor} from "../../iam/infrastructure/iam.interceptor.js";

    const platformApi = import.meta.env.VITE_LEARNING_PLATFORM_API_URL;

    export class BaseApi {
        #http;

        constructor() {
            this.#http = axios.create({
                baseURL: platformApi,
                headers: {
                    'Content-Type': 'application/json',
                    'Access-Control-Allow-Origin': '*'
                },
            });
            this.#http.interceptors.request.use(iamInterceptor);
        }

        get http() {
            return this.#http;
        }

    }
    ```
    </details>

    **Note:** `shared/infrastructure` now imports from `iam/infrastructure`, the Shared kernel depends on a bounded context. It is drawn that way, deliberately, in `components-frontend.puml` from Project Setup: `Shared` needs the interceptor to attach the session token, and there is no third place for it to live that would not just move the dependency.

    ```
    git add .
    git commit -m "feat(iam): register iamInterceptor in BaseApi."
    ```

16. **Create the `authentication-section` component.** Right-click `src` → `New` → `Vue Single-File Component` → `Composition API` → type `iam/presentation/components/authentication-section` → Enter. Signed out, `Sign In` and `Sign Up`, both navigate; signed in, a `Welcome, <username>` and `Sign Out`, which calls `store.signOut(router)`.

    <details>
    <summary>src/iam/presentation/components/authentication-section.vue</summary>

    ```vue
    <script setup>
    import useIamStore from "../../application/iam.store.js";
    import {useRouter} from "vue-router";
    import {computed} from "vue";

    const router = useRouter();
    const store = useIamStore();
    const {signOut} = store;

    let isSignedIn = computed(() => !!store.isSignedIn);
    let currentUsername = computed(() => store.currentUsername);

    function performSignIn() {
      router.push({name: 'iam-sign-in'});
    }

    function performSignUp() {
      router.push({name: 'iam-sign-up'});
    }

    function performSignOut() {
      signOut(router);
    }
    </script>

    <template>
      <div>
        <div v-if="isSignedIn">
          <span class="p-button-text bg-primary"> Welcome, {{ currentUsername }}</span>
          <pv-button class="bg-primary" text @click="performSignOut">Sign Out</pv-button>
        </div>
        <div v-else>
          <pv-button class="bg-primary" text @click="performSignIn">Sign In</pv-button>
          <pv-button class="bg-primary" text @click="performSignUp">Sign Up</pv-button>
        </div>
      </div>
    </template>

    <style scoped>

    </style>
    ```
    </details>

    ```
    git add .
    git commit -m "feat(iam): add authentication-section component."
    ```

17. **Wire `authentication-section` into `layout`.** It renders right before `language-switcher`, in the toolbar's `#end` slot.

    ```javascript
    import AuthenticationSection from "../../../iam/presentation/components/authentication-section.vue";
    ```

    ```vue
    <authentication-section/>
    <language-switcher/>
    ```

    <details>
    <summary>src/shared/presentation/components/layout.vue (Full file)</summary>

    ```vue
    <script setup>
    import LanguageSwitcher from "./language-switcher.vue";
    import {ref} from "vue";
    import {useI18n} from "vue-i18n";
    import FooterContent from "./footer-content.vue";
    import AuthenticationSection from "../../../iam/presentation/components/authentication-section.vue";

    const { t } = useI18n();

    const drawer = ref(false);

    const toggleDrawer = () => {
      drawer.value = !drawer.value;
    }

    const items = [
      {label: 'option.home', to: '/home'},
      {label: 'option.about', to: '/about'},
      {label: 'option.categories', to: '/publishing/categories'},
      {label: 'option.tutorials', to: '/publishing/tutorials'}
    ];
    </script>

    <template>
      <pv-confirm-dialog/>
      <header class="absolute top-0 left-0 w-full">
        <pv-toolbar class="bg-primary">
          <template #start>
            <pv-button class="p-button-text" icon="pi pi-bars" @click="toggleDrawer"/>
            <h3>ACME Learning Center</h3>
          </template>
          <template #end>
            <div class="flex-column mr-3">
              <pv-button v-for="item in items" :key="item.label" as-child v-slot="slotProps">
                <router-link :to="item.to" :class="slotProps['class']">{{ t(item.label) }}</router-link>
              </pv-button>
            </div>
            <authentication-section/>
            <language-switcher/>
          </template>
        </pv-toolbar>
        <pv-drawer v-model:visible="drawer"/>
      </header>
      <main class="mt-7">
        <router-view/>
      </main>
      <footer-content/>
    </template>
    ```
    </details>

    ```
    git add .
    git commit -m "feat(shared): wire authentication-section into layout."
    ```

18. **Run it.** Start the fake API and `npm run dev` as in the previous story.
    - Signed out, the toolbar now shows `Sign In` and `Sign Up`, `language-switcher` still on its right.
    - Click `Categories`, `Tutorials`, or type `/publishing/categories` by hand: every one of them bounces straight to `/iam/sign-in`, `authenticationGuard` at work.
    - Type an address that matches nothing, `/nothing-here`: the Page Not Found view shows, it is not blocked, `to.name !== 'not-found'` lets it through.
    - Open `/iam/sign-in`, submit any credentials: DevTools → Network shows `POST /authentication/sign-in` answering `404`, the same fake-API limit the last story's Note explained. Sign-in cannot succeed here, so `isSignedIn` never flips to `true` and the toolbar keeps showing `Sign In`/`Sign Up`.
    - Open Vue DevTools' Pinia panel (or `console.log` from the browser console) and inspect the `iam` store: `isSignedIn` is `false`, `currentToken` is `null`, confirming the session state the guard and the interceptor both read is exactly what it should be with nobody signed in.

    Stop both servers with `Ctrl+C`.

    ```
    git checkout server/db.json
    ```

19. **Publish and finish the feature.**

---

## Prepare the First Release

**Still on `develop`.** All six user stories are merged. Every real public repo ships a `LICENSE.md`, a `README.md`, and a `CONTRIBUTING.md`, but none of them belonged earlier, back then there was nothing to describe yet.

1. **Run every scenario end to end.** `npx json-server --watch server/db.json --routes server/routes.json --port 3000`, then `npm run dev`, then in the browser: switch language and watch every string change (US005); the toolbar, `Home`, `About`, and the Page Not Found view all work, in either language (US006); list, create, edit, and delete a category (US001); list, create, edit, and delete a tutorial, each with a required category, its name showing in the list (US002); the sign-up and sign-in forms validate, and their requests reach the fake API and fail with `404`, exactly as both stories' Notes described (US003, US004); an anonymous visit to `/publishing/categories` or `/publishing/tutorials` redirects to `/iam/sign-in`, and an unknown address still shows Page Not Found (US004). Then check every scenario in `docs/user-stories.md` against what the app actually does. No automated test drives these end to end, so this manual run is the acceptance check. Stop both servers with `Ctrl+C`, then:

   ```
   git checkout server/db.json
   ```

2. **Add doc comments to every class.** Every file built across the six user stories gets its JSDoc pass here, in one place, instead of interrupting the flow of each story to document a single file.

   <details>
   <summary>.env.development (Full file with doc comments)</summary>

   ```
   # Environment: Development
   # Description: Environment variables for development environment
   # Note: Do not commit sensitive information to version control
   # LEARNING_PLATFORM_API_URL version to be used when REST API is running on port 5222, otherwise use the one for port 3000
   # VITE_LEARNING_PLATFORM_API_URL="http://localhost:5222/api/v1"
   VITE_LEARNING_PLATFORM_API_URL="http://localhost:3000/api/v1"
   # VITE_CATEGORIES_ENDPOINT_PATH is the path to the categories endpoint.
   VITE_CATEGORIES_ENDPOINT_PATH="/categories"
   # VITE_TUTORIALS_ENDPOINT_PATH is the path to the tutorials endpoint.
   VITE_TUTORIALS_ENDPOINT_PATH="/tutorials"
   # VITE_SIGNUP_ENDPOINT_PATH is the path to the sign-up endpoint.
   VITE_SIGNUP_ENDPOINT_PATH="/authentication/sign-up"
   # VITE_SIGNIN_ENDPOINT_PATH is the path to the sign-in endpoint.
   VITE_SIGNIN_ENDPOINT_PATH="/authentication/sign-in"
   # VITE_USERS_ENDPOINT_PATH is the path to the users endpoint.
   VITE_USERS_ENDPOINT_PATH="/users"
   # VITE_PRIME_UI_LICENSE_KEY is the license key for the Prime UI library.
   VITE_PRIME_UI_LICENSE_KEY="eyJpZCI6ImJhYTExYWZlLTdlM2MtNGY3Mi04YmQ1LTBiZTk2MDU5YTMzNiIsInByb2R1Y3QiOiJwcmltZXVpIiwidGllciI6ImNvbW11bml0eSIsInR5cGUiOiJkZXYiLCJpYXQiOjE3ODg4MzgwOTUsImV4cCI6MTgyMDM3NDA5NX0.O5bvWswetPPP514VSmc_wnJgtXhW3_iLiUKiYJDLC0SmpuxLLGvfHhxf9_qTJbyQcjpWHEEAmM__iIGl_HigAQ"
   ```
   </details>

   <details>
   <summary>.env.production (Full file with doc comments)</summary>

   ```
   # Environment: Production
   # Description: Environment variables for development environment
   # Note: Do not commit sensitive information to version control
   VITE_LEARNING_PLATFORM_API_URL="https://lc2025201asi0730sandbox.free.beeceptor.com/api/v1"
   VITE_CATEGORIES_ENDPOINT_PATH="/categories"
   VITE_TUTORIALS_ENDPOINT_PATH="/tutorials"
   VITE_SIGNUP_ENDPOINT_PATH="/authentication/sign-up"
   VITE_SIGNIN_ENDPOINT_PATH="/authentication/sign-in"
   VITE_USERS_ENDPOINT_PATH="/users"
   # VITE_PRIME_UI_LICENSE_KEY is the license key for the Prime UI library.
   VITE_PRIME_UI_LICENSE_KEY="eyJpZCI6ImJhYTExYWZlLTdlM2MtNGY3Mi04YmQ1LTBiZTk2MDU5YTMzNiIsInByb2R1Y3QiOiJwcmltZXVpIiwidGllciI6ImNvbW11bml0eSIsInR5cGUiOiJkZXYiLCJpYXQiOjE3ODg4MzgwOTUsImV4cCI6MTgyMDM3NDA5NX0.O5bvWswetPPP514VSmc_wnJgtXhW3_iLiUKiYJDLC0SmpuxLLGvfHhxf9_qTJbyQcjpWHEEAmM__iIGl_HigAQ"
   ```
   </details>

   **Note:** `.env.production` carries the same disposable demo key as `.env.development`, and no comment above `VITE_LEARNING_PLATFORM_API_URL` here, it has only one value, no alternate port to choose between.

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
     readonly VITE_LEARNING_PLATFORM_API_URL: string;
     readonly VITE_CATEGORIES_ENDPOINT_PATH: string;
     readonly VITE_TUTORIALS_ENDPOINT_PATH: string;
     readonly VITE_SIGNUP_ENDPOINT_PATH: string;
     readonly VITE_SIGNIN_ENDPOINT_PATH: string;
     readonly VITE_USERS_ENDPOINT_PATH: string;
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
   import {createApp} from 'vue'
   import './style.css'
   import App from './app.vue'
   import i18n from "./i18n.js";
   import PrimeVue from 'primevue/config';
   import Material from '@primeuix/themes/material';
   import 'primeflex/primeflex.css';
   import 'primeicons/primeicons.css';
   import {
       Button,
       Column,
       ConfirmationService,
       ConfirmDialog,
       DataTable,
       Drawer,
       FloatLabel,
       InputText,
       Select,
       SelectButton,
       Textarea,
       Toolbar
   } from "primevue";
   import router from "./router.js";
   import pinia from "./pinia.js";

   const primeUiLicenseKey = import.meta.env.VITE_PRIME_UI_LICENSE_KEY;

   /**
    * Application composition root.
    *
    * @remarks
    * Configures the global plugins and PrimeVue components, then mounts the app.
    */
   createApp(App)
       .use(i18n)
       .use(PrimeVue, {theme: {preset: Material}, ripple: true, license: primeUiLicenseKey})
       .use(ConfirmationService)
       .component('pv-button',         Button)
       .component('pv-column',         Column)
       .component('pv-confirm-dialog', ConfirmDialog)
       .component('pv-data-table',     DataTable)
       .component('pv-drawer',         Drawer)
       .component('pv-float-label',    FloatLabel)
       .component('pv-input-text',     InputText)
       .component('pv-select',         Select)
       .component('pv-select-button',  SelectButton)
       .component('pv-textarea',       Textarea)
       .component('pv-toolbar',        Toolbar)
       .use(router)
       .use(pinia)
       .mount('#app')
   ```
   </details>

   <details>
   <summary>src/router.js (Full file with doc comments)</summary>

   ```javascript
   import {createRouter, createWebHistory} from "vue-router";
   import Home from "./shared/presentation/views/home.vue";
   import publishingRoutes from "./publishing/presentation/publishing-routes.js";
   import iamRoutes from "./iam/presentation/iam-routes.js";
   import {authenticationGuard} from "./iam/infrastructure/authentication.guard.js";

   // Define lazy-loaded components for routes
   const about = () => import('./shared/presentation/views/about.vue');
   const pageNotFound = () => import('./shared/presentation/views/page-not-found.vue');
   const routes = [
       { path: '/home',            name: 'home',       component: Home,        meta: { title: 'Home' } },
       { path: '/about',           name: 'about',      component: about,       meta: { title: 'About' } },
       { path: '/publishing',      name: 'publishing', children: publishingRoutes },
       { path: '/iam',             name: 'iam',        children: iamRoutes },
       { path: '/',                redirect: '/home' },
       { path: '/:pathMatch(.*)*', name: 'not-found', component: pageNotFound, meta: { title: 'Page Not Found' } }
   ];

   const router = createRouter({
       history: createWebHistory(import.meta.env.BASE_URL),
       routes: routes
   });

   /**
    * Global navigation guard that updates the document title and delegates to the IAM auth guard.
    *
    * @param {import('vue-router').RouteLocationNormalized} to - Target route.
    * @param {import('vue-router').RouteLocationNormalized} from - Previous route.
    * @returns {{name: string}|boolean} Returns true to allow navigation or an object to redirect.
    */
   router.beforeEach((to, from) => {
       const baseTitle = 'ACME Learning Center';
       document.title = `${baseTitle} - ${to.meta['title']}`;
       return authenticationGuard(to, from);
   });

   export default router;
   ```
   </details>

   <details>
   <summary>src/shared/infrastructure/base-api.js (Full file with doc comments)</summary>

   ```javascript
   import axios from "axios";

   import {iamInterceptor} from "../../iam/infrastructure/iam.interceptor.js";

   const platformApi = import.meta.env.VITE_LEARNING_PLATFORM_API_URL;

   /**
    * Shared infrastructure base class that configures the HTTP client.
    *
    * @class BaseApi
    */
   export class BaseApi {
       /**
        * @private
        * Axios HTTP client instance
        * @type {import('axios').AxiosInstance}
        */
       #http;

       /**
        * Initializes the Axios HTTP client with the base URL from environment variables
        */
       constructor() {
           this.#http = axios.create({
               baseURL: platformApi,
               headers: {
                   'Content-Type': 'application/json',
                   'Access-Control-Allow-Origin': '*'
               },
           });
           // Add interceptors for request/response if needed
           this.#http.interceptors.request.use(iamInterceptor);
       }

       /**
        * Returns the configured Axios HTTP client.
        * @returns {import('axios').AxiosInstance}
        */
       get http() {
           return this.#http;
       }

   }
   ```
   </details>

   <details>
   <summary>src/shared/infrastructure/base-endpoint.js (Full file with doc comments)</summary>

   ```javascript
   /**
    * Reusable endpoint client with CRUD operations over a resource collection.
    *
    * @class BaseEndpoint
    */
   export class BaseEndpoint {
       /**
        * @param {import('./base-api.js').BaseApi} baseApi - Configured API client owner.
        * @param {string} endpointPath - Relative resource path.
        */
       constructor(baseApi, endpointPath) {
           this.http = baseApi.http;
           this.endpointPath = endpointPath;
       }

       /** @returns {Promise<import('axios').AxiosResponse>} HTTP response with resource collection. */
       getAll() {
           return this.http.get(this.endpointPath);
       }

       /**
        * @param {string|number} id - Resource identifier.
        * @returns {Promise<import('axios').AxiosResponse>} HTTP response with one resource.
        */
       getById(id) {
           return this.http.get(`${this.endpointPath}/${id}`);
       }

       /**
        * @param {Object} resource - Resource payload to create.
        * @returns {Promise<import('axios').AxiosResponse>} HTTP response with created resource.
        */
       create(resource) {
           return this.http.post(this.endpointPath, resource);
       }

       /**
        * @param {string|number} id - Resource identifier.
        * @param {Object} resource - Resource payload to update.
        * @returns {Promise<import('axios').AxiosResponse>} HTTP response with updated resource.
        */
       update(id, resource) {
           return this.http.put(`${this.endpointPath}/${id}`, resource);
       }

       /**
        * @param {string|number} id - Resource identifier.
        * @returns {Promise<import('axios').AxiosResponse>} HTTP response for delete operation.
        */
       delete(id) {
           return this.http.delete(`${this.endpointPath}/${id}`);
       }
   }
   ```
   </details>

   <details>
   <summary>src/shared/presentation/components/layout.vue (Full file with doc comments)</summary>

   ```vue
   <script setup>
   import LanguageSwitcher from "./language-switcher.vue";
   import {ref} from "vue";
   import {useI18n} from "vue-i18n";
   import FooterContent from "./footer-content.vue";
   import AuthenticationSection from "../../../iam/presentation/components/authentication-section.vue";

   const { t } = useI18n();

   const drawer = ref(false);

   /**
    * Toggles the state of the drawer between open and closed.
    */
   const toggleDrawer = () => {
     drawer.value = !drawer.value;
   }

   const items = [
     {label: 'option.home', to: '/home'},
     {label: 'option.about', to: '/about'},
     {label: 'option.categories', to: '/publishing/categories'},
     {label: 'option.tutorials', to: '/publishing/tutorials'}
   ];
   </script>

   <template>
     <pv-confirm-dialog/>
     <header class="absolute top-0 left-0 w-full">
       <pv-toolbar class="bg-primary">
         <template #start>
           <pv-button class="p-button-text" icon="pi pi-bars" @click="toggleDrawer"/>
           <h3>ACME Learning Center</h3>
         </template>
         <template #end>
           <div class="flex-column mr-3">
             <pv-button v-for="item in items" :key="item.label" as-child v-slot="slotProps">
               <router-link :to="item.to" :class="slotProps['class']">{{ t(item.label) }}</router-link>
             </pv-button>
           </div>
           <authentication-section/>
           <language-switcher/>
         </template>
       </pv-toolbar>
       <pv-drawer v-model:visible="drawer"/>
     </header>
     <main class="mt-7">
       <router-view/>
     </main>
     <footer-content/>
   </template>
   ```
   </details>

   <details>
   <summary>src/publishing/domain/model/category.entity.js (Full file with doc comments)</summary>

   ```javascript
   /**
    * Category entity within the Publishing bounded context.
    *
    * @remarks
    * Every field is set once, in the constructor. To change a category, build a new one.
    *
    * @class Category
    */
   export class Category {
       #id;
       #name;

       /**
        * @param {Object} params - Entity attributes.
        * @param {?number} [params.id=null] - Category identifier.
        * @param {string} [params.name=''] - Human-readable category name.
        */
       constructor({id = null, name = ''}) {
           this.#id = id;
           this.#name = name;
       }

       /** @returns {?number} Category identifier. */
       get id() {
           return this.#id;
       }

       /** @returns {string} Human-readable category name. */
       get name() {
           return this.#name;
       }
   }
   ```
   </details>

   <details>
   <summary>src/publishing/domain/model/tutorial.entity.js (Full file with doc comments)</summary>

   ```javascript
   import {Category} from "./category.entity.js";

   /**
    * Tutorial entity within the Publishing bounded context.
    *
    * @remarks
    * Every field is set once, in the constructor. To change a tutorial, build a new one.
    *
    * @class Tutorial
    */
   export class Tutorial {
       #id;
       #title;
       #summary;
       #categoryId;
       #category;

       /**
        * @param {Object} params - Entity attributes.
        * @param {?number} [params.id=null] - Tutorial identifier.
        * @param {string} [params.title=''] - Tutorial title.
        * @param {string} [params.summary=''] - Tutorial abstract shown in listings.
        * @param {?number} [params.categoryId=null] - Foreign key of the related category.
        * @param {?Category} [params.category=null] - Optional category entity reference.
        */
       constructor({id = null, title = '', summary = '', categoryId = null, category = null}) {
           this.#id = id;
           this.#title = title;
           this.#summary = summary;
           this.#categoryId = categoryId;
           this.#category = category instanceof Category ? category : null;
       }

       /** @returns {?number} Tutorial identifier. */
       get id() {
           return this.#id;
       }

       /** @returns {string} Tutorial title. */
       get title() {
           return this.#title;
       }

       /** @returns {string} Tutorial abstract shown in listings. */
       get summary() {
           return this.#summary;
       }

       /** @returns {?number} Foreign key of the related category. */
       get categoryId() {
           return this.#categoryId;
       }

       /** @returns {?Category} Related category entity, if any. */
       get category() {
           return this.#category;
       }
   }
   ```
   </details>

   <details>
   <summary>src/publishing/infrastructure/category.assembler.js (Full file with doc comments)</summary>

   ```javascript
   import {Category} from "../domain/model/category.entity.js";

   /**
    * Maps publishing category resources into domain entities.
    *
    * @class CategoryAssembler
    */
   export class CategoryAssembler {
       /**
        * @param {Object} resource - Category resource payload.
        * @returns {Category} Category entity.
        */
       static toEntityFromResource(resource) {
           return new Category({...resource})
       }

       /**
        * @param {Category} entity - Category entity.
        * @returns {{id: ?number, name: string}} Category resource payload.
        */
       static toResourceFromEntity(entity) {
           return {id: entity.id, name: entity.name};
       }

       /**
        * Parses category resources from a response and maps them into entities.
        *
        * @param {import('axios').AxiosResponse<Array<Object>|Object>} response - HTTP response with category resources.
        * @returns {Category[]} Category entities.
        */
       static toEntitiesFromResponse(response) {
           if (response.status !== 200) {
               console.error(`${response.status}, ${response.statusText}`);
               return [];
           }
           let resources = response.data instanceof Array ? response.data : response.data['categories'];

           return resources.map(resource => this.toEntityFromResource(resource));
       }
   }
   ```
   </details>

   <details>
   <summary>src/publishing/infrastructure/tutorial.assembler.js (Full file with doc comments)</summary>

   ```javascript
   import {Tutorial} from "../domain/model/tutorial.entity.js";

   /**
    * Maps publishing tutorial resources into domain entities.
    *
    * @class TutorialAssembler
    */
   export class TutorialAssembler {
       /**
        * @param {Object} resource - Tutorial resource payload.
        * @returns {Tutorial} Tutorial entity.
        */
       static toEntityFromResource(resource) {
           return new Tutorial({...resource})
       }

       /**
        * @param {Tutorial} entity - Tutorial entity.
        * @returns {{id: ?number, title: string, summary: string, categoryId: ?number}} Tutorial resource payload.
        */
       static toResourceFromEntity(entity) {
           return {id: entity.id, title: entity.title, summary: entity.summary, categoryId: entity.categoryId};
       }

       /**
        * Parses tutorial resources from a response and maps them into entities.
        *
        * @param {import('axios').AxiosResponse<Array<Object>|Object>} response - HTTP response with tutorial resources.
        * @returns {Tutorial[]} Tutorial entities.
        */
       static toEntitiesFromResponse(response) {
           if (response.status !== 200) {
               console.error(`${response.status}, ${response.statusText}`);
               return [];
           }
           let resources = response.data instanceof Array ? response.data : response.data['tutorials'];

           return resources.map(resource => this.toEntityFromResource(resource));
       }
   }
   ```
   </details>

   <details>
   <summary>src/publishing/infrastructure/publishing-api.js (Full file with doc comments)</summary>

   ```javascript
   import {BaseApi} from "../../shared/infrastructure/base-api.js";
   import {BaseEndpoint} from "../../shared/infrastructure/base-endpoint.js";

   const categoriesEndpointPath    = import.meta.env.VITE_CATEGORIES_ENDPOINT_PATH;
   const tutorialsEndpointPath     = import.meta.env.VITE_TUTORIALS_ENDPOINT_PATH;

   /**
    * Infrastructure API client for Publishing bounded-context endpoints.
    *
    * @class PublishingApi
    * @extends BaseApi
    */
   export class PublishingApi extends BaseApi {
       /**
        * @type {BaseEndpoint}
        * @private
        */
       #categoriesEndpoint;
       /**
        * @type {BaseEndpoint}
        * @private
        */
       #tutorialsEndpoint;

       /** Creates endpoint clients for categories and tutorials. */
       constructor() {
           super();
           this.#categoriesEndpoint = new BaseEndpoint(this, categoriesEndpointPath);
           this.#tutorialsEndpoint = new BaseEndpoint(this, tutorialsEndpointPath);
       }

       /**
        * Fetches all categories.
        * @returns {Promise<import('axios').AxiosResponse>} Promise resolving to the categories' response.
        */
       getCategories() {
           return this.#categoriesEndpoint.getAll();
       }

       /**
        * Fetches a category by its ID.
        * @param {number|string} id - The ID of the category.
        * @returns {Promise<import('axios').AxiosResponse>} Promise resolving to the category response.
        */
       getCategoryById(id) {
           return this.#categoriesEndpoint.getById(id);
       }

       /**
        * Creates a category resource.
        * @param {Object} resource - Category resource payload.
        * @returns {Promise<import('axios').AxiosResponse>} Promise resolving to the created category response.
        */
       createCategory(resource) {
           return this.#categoriesEndpoint.create(resource);
       }

       /**
        * Updates a category resource.
        * @param {Object} resource - Category resource payload (must include id).
        * @returns {Promise<import('axios').AxiosResponse>} Promise resolving to the updated category response.
        */
       updateCategory(resource) {
           return this.#categoriesEndpoint.update(resource.id, resource);
       }

       /**
        * Deletes a category by its ID.
        * @param {number|string} id - The ID of the category to delete.
        * @returns {Promise<import('axios').AxiosResponse>} Promise resolving to the delete response.
        */
       deleteCategory(id) {
           return this.#categoriesEndpoint.delete(id);
       }

       /**
        * Fetches all tutorials.
        * @returns {Promise<import('axios').AxiosResponse>} Promise resolving to the tutorials' response.
        */
       getTutorials() {
           return this.#tutorialsEndpoint.getAll();
       }

       /**
        * Fetches a tutorial by its ID.
        * @param {number|string} id - The ID of the tutorial.
        * @returns {Promise<import('axios').AxiosResponse>} Promise resolving to the tutorial response.
        */
       getTutorialById(id) {
           return this.#tutorialsEndpoint.getById(id);
       }

       /**
        * Creates a tutorial resource.
        * @param {Object} resource - Tutorial resource payload.
        * @returns {Promise<import('axios').AxiosResponse>} Promise resolving to the created tutorial response.
        */
       createTutorial(resource) {
           return this.#tutorialsEndpoint.create(resource);
       }

       /**
        * Updates a tutorial resource.
        * @param {Object} resource - Tutorial resource payload (must include id).
        * @returns {Promise<import('axios').AxiosResponse>} Promise resolving to the updated tutorial response.
        */
       updateTutorial(resource) {
           return this.#tutorialsEndpoint.update(resource.id, resource);
       }

       /**
        * Deletes a tutorial by its ID.
        * @param {number|string} id - The ID of the tutorial to delete.
        * @returns {Promise<import('axios').AxiosResponse>} Promise resolving to the delete response.
        */
       deleteTutorial(id) {
           return this.#tutorialsEndpoint.delete(id);
       }
   }
   ```
   </details>

   <details>
   <summary>src/publishing/presentation/publishing-routes.js (Full file with doc comments)</summary>

   ```javascript
   // Lazy-loaded components
   const categoryList = () => import('./views/category-list.vue');
   const categoryForm = () => import('./views/category-form.vue');
   const tutorialList = () => import('./views/tutorial-list.vue');
   const tutorialForm = () => import('./views/tutorial-form.vue');

   const publishingRoutes = [
       {   path: 'categories',             name: 'publishing-categories',      component: categoryList, meta: {title: 'Categories'}},
       {   path: 'categories/new',         name: 'publishing-category-new',    component: categoryForm, meta: {title: 'New Category'}},
       {   path: 'categories/:id/edit',    name: 'publishing-category-edit',   component: categoryForm, meta: {title: 'Edit Category'}},
       {   path: 'tutorials',              name: 'publishing-tutorials',       component: tutorialList, meta: {title: 'Tutorials'}},
       {   path: 'tutorials/new',          name: 'publishing-tutorial-new',    component: tutorialForm, meta: {title: 'New Tutorial'}},
       {   path: 'tutorials/:id/edit',     name: 'publishing-tutorial-edit',   component: tutorialForm, meta: {title: 'Edit Tutorial'}}
   ];

   export default publishingRoutes;
   ```
   </details>

   <details>
   <summary>src/publishing/application/publishing.store.js (Full file with doc comments)</summary>

   ```javascript
   /**
    * Application service store for the Publishing bounded context.
    * It coordinates category and tutorial use cases and keeps UI-facing state.
    *
    * @module usePublishingStore
    */
   import {defineStore} from "pinia";
   import {computed, ref, shallowRef} from "vue";
   import {PublishingApi} from "../infrastructure/publishing-api.js";
   import {CategoryAssembler} from "../infrastructure/category.assembler.js";
   import {TutorialAssembler} from "../infrastructure/tutorial.assembler.js";
   import {Category} from "../domain/model/category.entity.js";
   import {Tutorial} from "../domain/model/tutorial.entity.js";

   const publishingApi = new PublishingApi();

   /**
    * Reactive store that exposes Publishing commands and queries.
    *
    * @remarks
    * Entities use native private fields, which a Vue Proxy cannot read, so the entity
    * lists live in `shallowRef` and are updated by replacing the whole array.
    *
    * @returns {Object} Store state and actions.
    */
   const usePublishingStore = defineStore('publishing', () => {
       /**
        * List of category entities.
        * @type {import('vue').ShallowRef<Category[]>}
        */
       const categories = shallowRef([]);
       /**
        * List of tutorial entities.
        * @type {import('vue').ShallowRef<Tutorial[]>}
        */
       const tutorials = shallowRef([]);
       /**
        * Messages of the errors encountered during API operations.
        * @type {import('vue').Ref<string[]>}
        */
       const errors = ref([]);
       /**
        * Whether categories have been loaded from the API.
        * @type {import('vue').Ref<boolean>}
        */
       const categoriesLoaded = ref(false);
       /**
        * Whether tutorials have been loaded from the API.
        * @type {import('vue').Ref<boolean>}
        */
       const tutorialsLoaded = ref(false);
       /**
        * Number of loaded categories.
        * @type {import('vue').ComputedRef<number>}
        */
       const categoriesCount = computed(() => {
           return categoriesLoaded.value ? categories.value.length : 0;
       });
       /**
        * Number of loaded tutorials.
        * @type {import('vue').ComputedRef<number>}
        */
       const tutorialsCount = computed(() => {
           return tutorialsLoaded.value ? tutorials.value.length : 0;
       });

       /**
        * Builds a copy of a tutorial that references its category entity.
        * @param {Tutorial} tutorial - Tutorial entity.
        * @returns {Tutorial} Tutorial entity with its category, or with none when it is not loaded yet.
        */
       function withCategory(tutorial) {
           return new Tutorial({
               id: tutorial.id,
               title: tutorial.title,
               summary: tutorial.summary,
               categoryId: tutorial.categoryId,
               category: getCategoryById(tutorial.categoryId) ?? null
           });
       }

       /**
        * Loads categories from infrastructure and updates the application state.
        * @returns {void}
        */
       function fetchCategories() {
           publishingApi.getCategories().then(response => {
               categories.value = CategoryAssembler.toEntitiesFromResponse(response);
               categoriesLoaded.value = true;
               tutorials.value = tutorials.value.map(withCategory);
           }).catch(error => {
               errors.value.push(error.message);
           });
       }

       /**
        * Loads tutorials from infrastructure and updates the application state.
        * @returns {void}
        */
       function fetchTutorials() {
           publishingApi.getTutorials().then(response => {
               tutorials.value = TutorialAssembler.toEntitiesFromResponse(response).map(withCategory);
               tutorialsLoaded.value = true;
           }).catch(error => {
               errors.value.push(error.message);
           });
       }

       /**
        * Finds a category entity by identifier.
        * @param {number|string} id - Category identifier.
        * @returns {Category|undefined} Matching category, if available.
        */
       function getCategoryById(id) {
           const idNum = parseInt(id);
           return categories.value.find(category => category.id === idNum);
       }

       /**
        * Creates a category through infrastructure and appends it to local state.
        * @param {Category} category - Category entity to persist.
        * @returns {void}
        */
       function addCategory(category) {
           publishingApi.createCategory(CategoryAssembler.toResourceFromEntity(category)).then(response => {
               const newCategory = CategoryAssembler.toEntityFromResource(response.data);
               categories.value = [...categories.value, newCategory];
           }).catch(error => {
               errors.value.push(error.message);
           });
       }

       /**
        * Updates an existing category and synchronizes local state.
        * @param {Category} category - Category entity with updated data.
        * @returns {void}
        */
       function updateCategory(category) {
           publishingApi.updateCategory(CategoryAssembler.toResourceFromEntity(category)).then(response => {
               const updatedCategory = CategoryAssembler.toEntityFromResource(response.data);
               categories.value = categories.value.map(c => c.id === updatedCategory.id ? updatedCategory : c);
               tutorials.value = tutorials.value.map(withCategory);
           }).catch(error => {
               errors.value.push(error.message);
           });
       }

       /**
        * Deletes a category and removes it from local state.
        * @param {Category} category - Category entity to remove.
        * @returns {void}
        */
       function deleteCategory(category) {
           publishingApi.deleteCategory(category.id).then(() => {
               categories.value = categories.value.filter(c => c.id !== category.id);
               tutorials.value = tutorials.value.map(withCategory);
           }).catch(error => {
               errors.value.push(error.message);
           });
       }

       /**
        * Finds a tutorial entity by identifier.
        * @param {number|string} id - Tutorial identifier.
        * @returns {Tutorial|undefined} Matching tutorial, if available.
        */
       function getTutorialById(id) {
           const idNum = parseInt(id);
           return tutorials.value.find(tutorial => tutorial.id === idNum);
       }

       /**
        * Creates a tutorial through infrastructure and appends it to local state.
        * @param {Tutorial} tutorial - Tutorial entity to persist.
        * @returns {void}
        */
       function addTutorial(tutorial) {
           publishingApi.createTutorial(TutorialAssembler.toResourceFromEntity(tutorial)).then(response => {
               const newTutorial = withCategory(TutorialAssembler.toEntityFromResource(response.data));
               tutorials.value = [...tutorials.value, newTutorial];
           }).catch(error => {
               errors.value.push(error.message);
           });
       }

       /**
        * Updates an existing tutorial and synchronizes local state.
        * @param {Tutorial} tutorial - Tutorial entity with updated data.
        * @returns {void}
        */
       function updateTutorial(tutorial) {
           publishingApi.updateTutorial(TutorialAssembler.toResourceFromEntity(tutorial)).then(response => {
               const updatedTutorial = withCategory(TutorialAssembler.toEntityFromResource(response.data));
               tutorials.value = tutorials.value.map(t => t.id === updatedTutorial.id ? updatedTutorial : t);
           }).catch(error => {
               errors.value.push(error.message);
           });
       }

       /**
        * Deletes a tutorial and removes it from local state.
        * @param {Tutorial} tutorial - Tutorial entity to remove.
        * @returns {void}
        */
       function deleteTutorial(tutorial) {
           publishingApi.deleteTutorial(tutorial.id).then(() => {
               tutorials.value = tutorials.value.filter(t => t.id !== tutorial.id);
           }).catch(error => {
               errors.value.push(error.message);
           });
       }

       return {
           categories,
           tutorials,
           errors,
           categoriesLoaded,
           tutorialsLoaded,
           categoriesCount,
           tutorialsCount,
           fetchCategories,
           fetchTutorials,
           getCategoryById,
           addCategory,
           updateCategory,
           deleteCategory,
           addTutorial,
           updateTutorial,
           deleteTutorial,
           getTutorialById
       }
   });

   export default usePublishingStore;
   ```
   </details>

   <details>
   <summary>src/publishing/presentation/views/category-list.vue (Full file with doc comments)</summary>

   ```vue
   <script setup>
   import {useI18n} from "vue-i18n";
   import {useRouter} from "vue-router";
   import {useConfirm} from "primevue";
   import usePublishingStore from "../../application/publishing.store.js";
   import {onMounted} from "vue";
   import {storeToRefs} from "pinia";

   const {t} = useI18n();
   const router = useRouter();
   const confirm = useConfirm();
   const store = usePublishingStore();
   const {categories, errors, categoriesLoaded} = storeToRefs(store);
   const {fetchCategories, deleteCategory} = store;

   onMounted(() => {
     if (!categoriesLoaded.value) fetchCategories();
   });

   /**
    * Navigate to the new category creation page.
    */
   const navigateToNew = () => {
     router.push({name: 'publishing-category-new'});
   };

   /**
    * Navigate to the category editing page.
    * @param {number} id - The ID of the category to edit.
    */
   const navigateToEdit = (id) => {
     router.push({name: 'publishing-category-edit', params: {id}});
   };

   /**
    * Confirm deletion of a category and execute deletion if confirmed.
    * @param {Object} category - The category object to delete.
    */
   const confirmDelete = (category) => {
     confirm.require({
       message: t('categories.confirm-delete', {name: category.name}),
       header: t('categories.delete-header'),
       icon: 'pi pi-exclamation-triangle',
       accept: () => {
         deleteCategory(category);
       },
     });
   };
   </script>

   <template>
     <div class="p-4">
       <h1>{{ t('categories.title') }}</h1>
       <pv-button :label="t('categories.new')" class="mb-3" icon="pi pi-plus" @click="navigateToNew"/>
       <pv-data-table
           :loading="!categoriesLoaded"
           :rows="5"
           :rows-per-page-options="[5, 10, 20]"
           :value="categories"
           paginator
           striped-rows
           table-style="min-width: 50rem">
         <pv-column :header="t('categories.id')" field="id" sortable/>
         <pv-column :header="t('categories.name')" field="name" sortable/>
         <pv-column :header="t('categories.actions')">
           <template #body="slotProps">
             <pv-button icon="pi pi-pencil" rounded text @click="navigateToEdit(slotProps.data.id)"/>
             <pv-button icon="pi pi-trash" rounded severity="danger" text @click="confirmDelete(slotProps.data)"/>
           </template>
         </pv-column>
       </pv-data-table>
       <div v-if="errors.length" class="text-red-500 mt-3">
         {{ t('errors.occurred') }}: {{ errors.join(', ') }}
       </div>
     </div>
   </template>

   <style scoped>

   </style>
   ```
   </details>

   <details>
   <summary>src/publishing/presentation/views/category-form.vue (Full file with doc comments)</summary>

   ```vue
   <script setup>
   import {useI18n} from "vue-i18n";
   import {useRoute, useRouter} from "vue-router";
   import usePublishingStore from "../../application/publishing.store.js";
   import {computed, onMounted, ref} from "vue";
   import {storeToRefs} from "pinia";
   import {Category} from "../../domain/model/category.entity.js";

   const {t} = useI18n();
   const route = useRoute();
   const router = useRouter();
   const store = usePublishingStore();
   const {errors} = storeToRefs(store);
   const {addCategory, updateCategory} = store;

   const form = ref({name: ''});
   const isEdit = computed(() => !!route.params.id);

   onMounted(() => {
     if (isEdit.value) {
       const category = getCategoryById(route.params.id);
       if (category) form.value.name = category.name; else router.push({name: 'publishing-categories'});
     }
   });

   /**
    * Retrieves a category by its ID.
    * @param {string} id - The ID of the category.
    * @returns {Category|null} - The category object if found, null otherwise.
    */
   function getCategoryById(id) {
     return store.getCategoryById(id);
   }

   /**
    * Saves the category, either by adding a new one or updating an existing one.
    */
   const saveCategory = () => {
     const category = new Category({
       id: isEdit.value ? Number(route.params.id) : null,
       name: form.value.name,
     });
     if (isEdit.value) updateCategory(category); else addCategory(category);
     navigateBack();
   };

   /**
    * Navigates back to the publishing categories list.
    */
   const navigateBack = () => {
     router.push({name: 'publishing-categories'});
   };
   </script>

   <template>
     <div class="p-4">
       <h1>{{ isEdit ? t('category.edit-title') : t('category.new-title') }}</h1>
       <form @submit.prevent="saveCategory">
         <div class="field mb-3">
           <label for="name">{{ t('category.name') }}</label>
           <pv-input-text id="name" v-model="form.name" class="w-full" required/>
         </div>
         <pv-button :label="t('category.save')" icon="pi pi-save" type="submit"/>
         <pv-button :label="t('category.cancel')" class="ml-2" severity="secondary" @click="navigateBack"/>
       </form>
       <div v-if="errors.length" class="text-red-500 mt-3">
         {{ t('errors.occurred') }}: {{ errors.join(', ') }}
       </div>
     </div>
   </template>

   <style scoped>

   </style>
   ```
   </details>

   <details>
   <summary>src/publishing/presentation/views/tutorial-list.vue (Full file with doc comments)</summary>

   ```vue
   <script setup>
   import {useI18n} from "vue-i18n";
   import {useRouter} from "vue-router";
   import {useConfirm} from "primevue";
   import usePublishingStore from "../../application/publishing.store.js";
   import {onMounted} from "vue";
   import {storeToRefs} from "pinia";

   const { t } = useI18n();
   const router = useRouter();
   const confirm = useConfirm();
   const store = usePublishingStore();
   const {tutorials, tutorialsLoaded, categoriesLoaded, errors} = storeToRefs(store);
   const {fetchTutorials, fetchCategories, deleteTutorial} = store;

   onMounted(() => {
     if (!categoriesLoaded.value) fetchCategories();
     if (!tutorialsLoaded.value) fetchTutorials();
   });

   /**
    * Navigate to the new tutorial creation page.
    */
   const navigateToNew = () => {
     router.push({ name: 'publishing-tutorial-new' });
   };

   /**
    * Navigate to the tutorial editing page.
    * @param {number} id - The ID of the tutorial to edit.
    */
   const navigateToEdit = (id) => {
     router.push({ name: 'publishing-tutorial-edit', params: { id } });
   };

   /**
    * Confirm and delete a tutorial.
    * @param {Object} tutorial - The tutorial to delete.
    */
   const confirmDelete = (tutorial) => {
     confirm.require({
       message: t('tutorials.confirm-delete', { title: tutorial.title }),
       header: t('tutorials.delete-header'),
       icon: 'pi pi-exclamation-triangle',
       accept: () => { deleteTutorial(tutorial); },
     });
   };
   </script>

   <template>
     <div class="p-4">
       <h1>{{ t('tutorials.title') }}</h1>
       <pv-button :label="t('tutorials.new')" icon="pi pi-plus" class="mb-3" @click="navigateToNew" />
       <pv-data-table
           :value="tutorials"
           :loading="!tutorialsLoaded"
           striped-rows
           table-style="min-width: 50rem"
           paginator
           :rows="5"
           :rows-per-page-options="[5, 10, 20]"
       >
         <pv-column field="id" :header="t('tutorials.id')" sortable />
         <pv-column field="title" :header="t('tutorial.title')" sortable />
         <pv-column field="summary" :header="t('tutorials.summary')" />
         <pv-column :header="t('tutorial.category')">
           <template #body="slotProps">{{ slotProps.data.category?.name }}</template>
         </pv-column>
         <pv-column :header="t('tutorials.actions')">
           <template #body="slotProps">
             <pv-button icon="pi pi-pencil" text rounded @click="navigateToEdit(slotProps.data.id)" />
             <pv-button icon="pi pi-trash" text rounded severity="danger" @click="confirmDelete(slotProps.data)" />
           </template>
         </pv-column>
       </pv-data-table>
       <div v-if="errors.length" class="text-red-500 mt-3">
         {{ t('errors.occurred') }}: {{ errors.join(', ') }}
       </div>
     </div>
   </template>

   <style scoped>

   </style>
   ```
   </details>

   <details>
   <summary>src/publishing/presentation/views/tutorial-form.vue (Full file with doc comments)</summary>

   ```vue
   <script setup>
   import {useI18n} from "vue-i18n";
   import {useRoute, useRouter} from "vue-router";
   import usePublishingStore from "../../application/publishing.store.js";
   import {computed, onMounted, ref} from "vue";
   import {storeToRefs} from "pinia";
   import {Tutorial} from "../../domain/model/tutorial.entity.js";

   const {t} = useI18n();
   const route = useRoute();
   const router = useRouter();
   const store = usePublishingStore();
   const {errors, categories, categoriesLoaded} = storeToRefs(store);
   const {addTutorial, updateTutorial, fetchCategories} = store;

   const form = ref({title: '', summary: '', categoryId: null});
   const isEdit = computed(() => !!route.params.id);
   const categoryRequiredError = ref(false);

   onMounted(() => {
     if (!categoriesLoaded.value) fetchCategories();
     if (isEdit.value) {
       const tutorial = getTutorialById(route.params.id);
       if (tutorial) {
         form.value.title = tutorial.title;
         form.value.summary = tutorial.summary;
         form.value.categoryId = tutorial.categoryId;
       } else router.push({name: 'publishing-tutorials'});
     }
   });

   /**
    * Retrieves a tutorial by its ID.
    * @param {string} id - The ID of the tutorial.
    * @returns {Object|null} - The tutorial object if found, null otherwise.
    */
   function getTutorialById(id) {
     return store.getTutorialById(id);
   }

   /**
    * Saves the tutorial.
    * If editing an existing tutorial, updates it; otherwise, adds a new tutorial.
    * A category is required: pv-select is not a native select, so the browser does not check it and it is checked here.
    */
   const saveTutorial = () => {
     if (!form.value.categoryId) {
       categoryRequiredError.value = true;
       return;
     }
     categoryRequiredError.value = false;
     const tutorial = new Tutorial({
       id: isEdit.value ? Number(route.params.id) : null,
       title: form.value.title,
       summary: form.value.summary,
       categoryId: form.value.categoryId,
     });
     if (isEdit.value) updateTutorial(tutorial); else addTutorial(tutorial);
     navigateBack();
   };

   /**
    * Navigates back to the tutorials list.
    */
   const navigateBack = () => {
     router.push({name: 'publishing-tutorials'});
   };
   </script>

   <template>
     <div class="p-4">
       <h1>{{ isEdit ? t('tutorial.edit-title') : t('tutorial.new-title') }}</h1>
       <form @submit.prevent="saveTutorial">
         <div class="field mb-3">
           <label for="title">{{ t('tutorial.title') }}</label>
           <pv-input-text id="title" v-model="form.title" required class="w-full" />
         </div>
         <div class="field mb-3">
           <label for="summary">{{ t('tutorial.summary') }}</label>
           <pv-textarea id="summary" v-model="form.summary" rows="4" class="w-full" />
         </div>
         <div class="field mb-3">
           <label for="category">{{ t('tutorial.category') }}</label>
           <pv-select
               id="category"
               v-model="form.categoryId"
               :options="categories"
               optionLabel="name"
               optionValue="id"
               placeholder="Select a category"
               class="w-full"
           />
           <small v-if="categoryRequiredError" class="text-red-500">{{ t('tutorial.category-required') }}</small>
         </div>
         <pv-button type="submit" :label="t('tutorial.save')" icon="pi pi-save" />
         <pv-button :label="t('tutorial.cancel')" severity="secondary" class="ml-2" @click="navigateBack" />
       </form>
       <div v-if="errors.length" class="text-red-500 mt-3">
         {{ t('errors.occurred') }}: {{ errors.join(', ') }}
       </div>
     </div>
   </template>

   <style scoped>

   </style>
   ```
   </details>

   <details>
   <summary>src/iam/domain/user.entity.js (Full file with doc comments)</summary>

   ```javascript
   /**
    * IAM user aggregate root representation used by the client domain model.
    *
    * @class User
    */
   export class User {
       #id;
       #username;

       /**
        * @param {Object} params - Entity attributes.
        * @param {string|number} params.id - Unique user identifier.
        * @param {string} params.username - Public username.
        */
       constructor({id, username}) {
           this.#id = id;
           this.#username = username;
       }

       /** @returns {string|number} Unique user identifier. */
       get id() {
           return this.#id;
       }

       /** @returns {string} Public username. */
       get username() {
           return this.#username;
       }
   }
   ```
   </details>

   <details>
   <summary>src/iam/domain/sign-in.command.js (Full file with doc comments)</summary>

   ```javascript
   /**
    * Command used by the IAM application layer to request authentication.
    *
    * @class SignInCommand
    */
   export class SignInCommand {
       #username;
       #password;

       /**
        * @param {Object} params - Command attributes.
        * @param {string} params.username - Username credential.
        * @param {string} params.password - Password credential.
        */
       constructor({username, password}) {
           this.#username = username;
           this.#password = password;
       }

       /** @returns {string} Username credential. */
       get username() {
           return this.#username;
       }

       /** @returns {string} Password credential. */
       get password() {
           return this.#password;
       }
   }
   ```
   </details>

   <details>
   <summary>src/iam/domain/sign-up.command.js (Full file with doc comments)</summary>

   ```javascript
   /**
    * Command used by the IAM application layer to register a new user.
    *
    * @class SignUpCommand
    */
   export class SignUpCommand {
       #username;
       #password;

       /**
        * @param {Object} params - Command attributes.
        * @param {string} params.username - Desired username.
        * @param {string} params.password - Desired password.
        */
       constructor({username, password}) {
           this.#username = username;
           this.#password = password;
       }

       /** @returns {string} Desired username. */
       get username() {
           return this.#username;
       }

       /** @returns {string} Desired password. */
       get password() {
           return this.#password;
       }
   }
   ```
   </details>

   <details>
   <summary>src/iam/infrastructure/sign-in.resource.js (Full file with doc comments)</summary>

   ```javascript
   /**
    * Infrastructure resource returned by the authentication endpoint.
    *
    * @class SignInResource
    */
   export class SignInResource {
       /**
        * @param {Object} params - Resource payload.
        * @param {string|number} params.id - Authenticated user identifier.
        * @param {string} params.username - Authenticated username.
        * @param {string} params.token - Bearer token.
        */
       constructor({id, username, token}) {
           this.id = id;
           this.username = username;
           this.token = token;
       }
   }
   ```
   </details>

   <details>
   <summary>src/iam/infrastructure/sign-up.resource.js (Full file with doc comments)</summary>

   ```javascript
   /**
    * Infrastructure resource returned after user registration.
    *
    * @class SignUpResource
    */
   export class SignUpResource {
       /**
        * @param {Object} params - Resource payload.
        * @param {string} params.message - Outcome message from the registration endpoint.
        */
       constructor({message}) {
           this.message = message;
       }
   }
   ```
   </details>

   <details>
   <summary>src/iam/infrastructure/sign-in.assembler.js (Full file with doc comments)</summary>

   ```javascript
   import {SignInResource} from "./sign-in.resource.js";

   /**
    * Maps authentication endpoint responses into IAM infrastructure resources.
    *
    * @class SignInAssembler
    */
   export class SignInAssembler {
       /**
        * @param {import('../domain/sign-in.command.js').SignInCommand} command - Sign-in command.
        * @returns {{username: string, password: string}} Sign-in request payload.
        */
       static toRequestFromCommand(command) {
           return {username: command.username, password: command.password};
       }

       /**
        * @param {import('axios').AxiosResponse<Object>} response - HTTP response from sign-in endpoint.
        * @returns {SignInResource|null} Parsed resource when the response is successful; otherwise null.
        */
       static toResourceFromResponse(response) {
           if (response.status !== 200) {
               console.error(`${response.status}, ${response.statusText}`);
               return null;
           }
           return new SignInResource(response.data);
       }
   }
   ```
   </details>

   <details>
   <summary>src/iam/infrastructure/sign-up.assembler.js (Full file with doc comments)</summary>

   ```javascript
   import {SignUpResource} from "./sign-up.resource.js";

   /**
    * Maps registration endpoint responses into IAM infrastructure resources.
    *
    * @class SignUpAssembler
    */
   export class SignUpAssembler {
       /**
        * @param {import('../domain/sign-up.command.js').SignUpCommand} command - Sign-up command.
        * @returns {{username: string, password: string}} Sign-up request payload.
        */
       static toRequestFromCommand(command) {
           return {username: command.username, password: command.password};
       }

       /**
        * @param {import('axios').AxiosResponse<Object>} response - HTTP response from sign-up endpoint.
        * @returns {SignUpResource|null} Parsed resource when the response is successful; otherwise null.
        */
       static toResourceFromResponse(response) {
           if (response.status !== 200) {
               console.error(`${response.status}, ${response.statusText}`);
               return null;
           }
           return new SignUpResource(response.data);
       }
   }
   ```
   </details>

   <details>
   <summary>src/iam/infrastructure/user.assembler.js (Full file with doc comments)</summary>

   ```javascript
   import {User} from "../domain/user.entity.js";

   /**
    * Maps IAM infrastructure resources into domain entities.
    *
    * @class UserAssembler
    */
   export class UserAssembler {
       /**
        * @param {Object} resource - User resource payload.
        * @returns {User} User entity.
        */
       static toEntityFromResource(resource) {
           return new User({...resource});
       }
       
       /**
        * @param {import('axios').AxiosResponse<Array<Object>|Object>} response - HTTP response containing user resources.
        * @returns {User[]} Collection of user entities.
        */
       static toEntitiesFromResponse(response) {
           if (response.status !== 200) {
               console.error(`${response.status}, ${response.statusText}`);
               return [];
           }
           let resources = response.data instanceof Array ? response.data : response.data['users'];

           return resources.map(resource => this.toEntityFromResource(resource));
       }
   }
   ```
   </details>

   <details>
   <summary>src/iam/infrastructure/iam-api.js (Full file with doc comments)</summary>

   ```javascript
   import {BaseEndpoint} from "../../shared/infrastructure/base-endpoint.js";
   import {BaseApi} from "../../shared/infrastructure/base-api.js";
   const signInEndpointPath = import.meta.env.VITE_SIGNIN_ENDPOINT_PATH;
   const signUpEndpointPath = import.meta.env.VITE_SIGNUP_ENDPOINT_PATH;
   const usersEndpointPath   = import.meta.env.VITE_USERS_ENDPOINT_PATH;

   /**
    * Infrastructure API client for IAM bounded-context endpoints.
    *
    * @class IamApi
    * @extends BaseApi
    */
   export class IamApi extends BaseApi {
       #signInEndpoint;
       #signUpEndpoint;
       #usersEndpoint;

       /** Creates endpoint clients for sign-in, sign-up, and user listing. */
       constructor() {
           super();
           this.#signInEndpoint = new BaseEndpoint(this, signInEndpointPath);
           this.#signUpEndpoint = new BaseEndpoint(this, signUpEndpointPath);
           this.#usersEndpoint = new BaseEndpoint(this, usersEndpointPath);
       }

       /**
        * Sends a sign-in command to the authentication endpoint.
        * @param {import('../domain/sign-in.command.js').SignInCommand} signInRequest - Sign-in command.
        * @returns {Promise<import('axios').AxiosResponse<Object>>} HTTP response with authentication payload.
        */
       signIn(signInRequest) {
           return this.#signInEndpoint.create(signInRequest);
       }

       /**
        * Sends a sign-up command to the registration endpoint.
        * @param {import('../domain/sign-up.command.js').SignUpCommand} signUpRequest - Sign-up command.
        * @returns {Promise<import('axios').AxiosResponse<Object>>} HTTP response with registration payload.
        */
       signUp(signUpRequest) {
           return this.#signUpEndpoint.create(signUpRequest);
       }

       /**
        * Retrieves users visible to the IAM context.
        * @returns {Promise<import('axios').AxiosResponse<Array<Object>|Object>>} HTTP response with user resources.
        */
       getUsers() {
           return this.#usersEndpoint.getAll();
       }
   }
   ```
   </details>

   <details>
   <summary>src/iam/application/iam.store.js (Full file with doc comments)</summary>

   ```javascript
   import {IamApi} from "../infrastructure/iam-api.js";
   import {defineStore} from "pinia";
   import {computed, ref, shallowRef} from "vue";
   import {SignInAssembler} from "../infrastructure/sign-in.assembler.js";
   import {UserAssembler} from "../infrastructure/user.assembler.js";
   import {SignUpAssembler} from "../infrastructure/sign-up.assembler.js";
   import {SignInCommand} from "../domain/sign-in.command.js";
   import {SignUpCommand} from "../domain/sign-up.command.js";

   const iamApi = new IamApi();

   /**
    * Application service store for the IAM bounded context.
    * It coordinates authentication commands and exposes UI-facing auth state.
    *
    * @remarks
    * Entities use native private fields, which a Vue Proxy cannot read, so the user
    * list lives in a `shallowRef` and is updated by replacing the whole array.
    *
    * @returns {Object} Store state and actions.
    */
   const useIamStore = defineStore('iam', () => {
       /** @type {import('vue').ShallowRef<User[]>} User entities. */
       const users = shallowRef([]);
       /** @type {import('vue').Ref<string[]>} Messages of the errors encountered during authentication. */
       const errors = ref([]);
       /** @type {import('vue').Ref<boolean>} Flag indicating if users have been loaded. */
       const usersLoaded = ref(false);
       /** @type {import('vue').Ref<boolean>} Flag indicating if a user is signed in. */
       const isSignedIn = ref(false);
       /** @type {import('vue').Ref<string|null>} Username of the signed-in user. */
       const currentUsername = ref(null);
       /** @type {import('vue').Ref<string|number>} Identifier of the signed-in user. */
       const currentUserId = ref(0);
       /** @type {import('vue').ComputedRef<string|null>} The current authentication token. */
       const currentToken = computed(() => isSignedIn.value ? localStorage.getItem('token') : null);

       /**
        * Executes the sign-in use case and updates authentication state.
        * @param {SignInCommand} signInCommand - Sign-in command.
        * @param {import('vue-router').Router} router - Router used to redirect on result.
        * @returns {void}
        */
       function signIn(signInCommand, router) {
           iamApi.signIn(SignInAssembler.toRequestFromCommand(signInCommand))
               .then(response => {
                   const signInResource = SignInAssembler.toResourceFromResponse(response);
                   if (signInResource) {
                       const currentUser = UserAssembler.toEntityFromResource(signInResource);
                       currentUsername.value = currentUser.username;
                       currentUserId.value = currentUser.id;
                       localStorage.setItem('token', signInResource.token);
                       isSignedIn.value = true;
                       errors.value = [];
                       router.push({name: 'home'});
                   } else {
                       isSignedIn.value = false;
                       errors.value.push('Sign-in failed');
                       router.push({name: 'iam-sign-in'});
                   }
               })
               .catch(error => {
                   isSignedIn.value = false;
                   errors.value.push(error.message);
                   router.push({name: 'iam-sign-in'});
               });
       }

       /**
        * Executes the sign-up use case and routes the user to the next screen.
        * @param {SignUpCommand} signUpCommand - Sign-up command.
        * @param {import('vue-router').Router} router - Router used to redirect on result.
        * @returns {void}
        */
       function signUp(signUpCommand, router) {
           iamApi.signUp(SignUpAssembler.toRequestFromCommand(signUpCommand))
               .then(response => {
                   const signUpResource = SignUpAssembler.toResourceFromResponse(response);
                   if (signUpResource) {
                       errors.value = [];
                       router.push({name: 'iam-sign-in'});
                   } else {
                       errors.value.push('Sign-up failed');
                       router.push({name: 'iam-sign-up'});
                   }
               })
               .catch(error => {
                   errors.value.push(error.message);
                   router.push({name: 'iam-sign-up'});
               });
       }

       /**
        * Clears the active IAM session and local auth artifacts.
        * @param {import('vue-router').Router} router - Router used to redirect after sign-out.
        * @returns {void}
        */
       function signOut(router) {
           currentUsername.value = null;
           currentUserId.value = 0;
           localStorage.removeItem('token');
           isSignedIn.value = false;
           errors.value = [];
           router.push({name: 'iam-sign-in'});
       }

       /**
        * Loads user entities from infrastructure.
        * @returns {void}
        */
       function fetchUsers() {
           iamApi.getUsers().then(response => {
               users.value = UserAssembler.toEntitiesFromResponse(response);
               usersLoaded.value = true;
               errors.value = [];
           }).catch(error => {
               errors.value.push(error.message);
           });
       }

       return {
           users,
           errors,
           usersLoaded,
           currentUsername,
           currentUserId,
           currentToken,
           isSignedIn,
           signIn,
           signUp,
           signOut,
           fetchUsers
       };
   });

   export default useIamStore;
   ```
   </details>

   <details>
   <summary>src/iam/infrastructure/authentication.guard.js (Full file with doc comments)</summary>

   ```javascript
   import useIamStore from "../application/iam.store.js";

   /**
    * Navigation guard that protects non-public routes for anonymous users.
    *
    * @param {import('vue-router').RouteLocationNormalized} to - Target route.
    * @param {import('vue-router').RouteLocationNormalized} from - Current route.
    * @returns {{name: string}|boolean} Returns true to allow navigation or an object to redirect.
    */
   export const authenticationGuard = (to, from) => {
       const store = useIamStore();
       const isAnonymous = !store.isSignedIn;
       const publicRoutes = ['/iam/sign-in', '/iam/sign-up', '/about'];
       const routeRequiresToBeAuthenticated = !publicRoutes.includes(to.path) && to.name !== 'not-found';
       if (isAnonymous && routeRequiresToBeAuthenticated) return {name: 'iam-sign-in'};
       return true;
   }
   ```
   </details>

   <details>
   <summary>src/iam/infrastructure/iam.interceptor.js (Full file with doc comments)</summary>

   ```javascript
   import useIamStore from "../application/iam.store.js";

   /**
    * Adds the IAM bearer token to outbound requests when a user is authenticated.
    *
    * @param {import('axios').InternalAxiosRequestConfig} config - Axios request configuration.
    * @returns {import('axios').InternalAxiosRequestConfig} Updated request configuration.
    */
   export const iamInterceptor = (config) => {
       const store = useIamStore();
       if (store.isSignedIn) {
           config.headers.Authorization = `Bearer ${store.currentToken}`;
       }
       return config;
   }
   ```
   </details>

   <details>
   <summary>src/iam/presentation/views/sign-in-form.vue (Full file with doc comments)</summary>

   ```vue
   <script setup>
     import useIamStore from "../../application/iam.store.js";
     import {reactive} from "vue";
     import {SignInCommand} from "../../domain/sign-in.command.js";
     import {useRouter} from "vue-router";

     const router = useRouter();
     const store = useIamStore();
     const {signIn} = store;
     const form = reactive({
       username: '',
       password: ''
     })
     /**
      * Performs the sign-in action by creating a SignInCommand
      * with the provided username and password, and then calling
      * the signIn method from the store. Navigation is handled
      * automatically upon successful sign-in.
      */
     function performSignIn() {
       const signInCommand = new SignInCommand(form);
       signIn(signInCommand, router);
     }
   </script>

   <template>
     <div>
       <h3>Sign In</h3>
     </div>
     <p class="p-fluid mb-5">Please enter the required information to sign in.</p>
     <div>
       <form @submit.prevent="performSignIn">
         <div class="p-fluid">
           <div class="field mt-5">
             <pv-float-label>
               <label for="username">Username</label>
               <pv-input-text id="username" v-model="form.username" :class="{'p-invalid': !form.username}"/>
               <small v-if="!form.username" class="p-invalid">Username is required.</small>
             </pv-float-label>
           </div>
           <div class="p-field mt-5">
             <pv-float-label>
               <label for="password">Password</label>
               <pv-input-text id="password" v-model="form.password" :class="{'p-invalid': !form.password}" type="password"/>
               <small v-if="!form.password" class="p-invalid">Password is required.</small>
             </pv-float-label>
           </div>
           <div class="p-field mt-5">
             <pv-button type="submit">Sign In</pv-button>
           </div>
         </div>
       </form>
     </div>
   </template>

   <style scoped>

   </style>
   ```
   </details>

   <details>
   <summary>src/iam/presentation/views/sign-up-form.vue (Full file with doc comments)</summary>

   ```vue
   <script setup>
   import useIamStore from "../../application/iam.store.js";
   import {reactive} from "vue";
   import {SignUpCommand} from "../../domain/sign-up.command.js";
   import {useRouter} from "vue-router";

   const router = useRouter();
   const store = useIamStore();
   const {signUp} = store;
   const form = reactive({
     username: '',
     password: ''
   })
   /**
    * Performs the sign-up action by creating a SignUpCommand
    * with the provided username and password and then calling
    * the signUp method from the store.
    */
   function performSignUp() {
     const signUpCommand = new SignUpCommand(form);
     signUp(signUpCommand, router);
   }
   </script>

   <template>
     <div>
       <h3>Sign Up</h3>
     </div>
     <p class="p-fluid mb-5">Please enter the required information to sign in.</p>
     <div>
       <form @submit.prevent="performSignUp">
         <div class="p-fluid">
           <div class="field mt-5">
             <pv-float-label>
               <label for="username">Username</label>
               <pv-input-text id="username" v-model="form.username" :class="{'p-invalid': !form.username}"/>
               <small v-if="!form.username" class="p-invalid">Username is required.</small>
             </pv-float-label>
           </div>
           <div class="p-field mt-5">
             <pv-float-label>
               <label for="password">Password</label>
               <pv-input-text id="password" v-model="form.password" :class="{'p-invalid': !form.password}" type="password"/>
               <small v-if="!form.password" class="p-invalid">Password is required.</small>
             </pv-float-label>
           </div>
           <div class="p-field mt-5">
             <pv-button type="submit">Sign Up</pv-button>
           </div>
         </div>
       </form>
     </div>
   </template>

   <style scoped>

   </style>
   ```
   </details>

   <details>
   <summary>src/iam/presentation/components/authentication-section.vue (Full file with doc comments)</summary>

   ```vue
   <script setup>
   import useIamStore from "../../application/iam.store.js";
   import {useRouter} from "vue-router";
   import {computed} from "vue";

   const router = useRouter();
   const store = useIamStore();
   const {signOut} = store;

   let isSignedIn = computed(() => !!store.isSignedIn);
   let currentUsername = computed(() => store.currentUsername);

   /**
    * Navigate to the sign-in page.
    * @function performSignIn
    */
   function performSignIn() {
     router.push({name: 'iam-sign-in'});
   }

   /**
    * Navigate to the sign-up page.
    * @function performSignUp
    */
   function performSignUp() {
     router.push({name: 'iam-sign-up'});
   }

   /**
    * Sign out the current user and navigate to the appropriate page.
    * @function performSignOut
    */
   function performSignOut() {
     signOut(router);
   }
   </script>

   <template>
     <div>
       <div v-if="isSignedIn">
         <span class="p-button-text bg-primary"> Welcome, {{ currentUsername }}</span>
         <pv-button class="bg-primary" text @click="performSignOut">Sign Out</pv-button>
       </div>
       <div v-else>
         <pv-button class="bg-primary" text @click="performSignIn">Sign In</pv-button>
         <pv-button class="bg-primary" text @click="performSignUp">Sign Up</pv-button>
       </div>
     </div>
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
   MIT License
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
   LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
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
   # ACME Learning Center

   [![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](LICENSE.md)

   A publishing-catalog application built with Vue and PrimeVue, connecting to a backend API with a Domain-Driven Design approach. A learning manager or tutorial author signs in, manages categories and tutorials, and switches between English and Spanish.

   In development the application talks to a local fake API served by `json-server` at `http://localhost:3000/api/v1`.

   ## Key Features

   - **Category and tutorial management**: create, list, edit, and delete, with every tutorial tied to a category.
   - **Pinia setup stores**: `usePublishingStore` and `useIamStore` keep the state; domain entities live in `shallowRef` and are replaced, never mutated.
   - **Domain-Driven Design**: `publishing`, `iam`, and `shared` bounded contexts, each in `domain`, `infrastructure`, `application`, and `presentation` layers.
   - **Authentication**: sign up, sign in, sign out, a router guard, and an Axios interceptor that adds the session token.
   - **Internationalization**: runtime English/Spanish translations through `vue-i18n`.
   - **PrimeVue**: components, the Material preset, and PrimeFlex utilities.
   - **Native `#` private fields** across the domain, with getters only.

   ## Architecture

   1. **Publishing Bounded Context**: the core business domain.
      - **Domain Layer**: the `Category` and `Tutorial` entities.
      - **Infrastructure Layer**: `PublishingApi`, `CategoryAssembler`, and `TutorialAssembler`.
      - **Application Layer**: `usePublishingStore`.
      - **Presentation Layer**: `category-list`, `category-form`, `tutorial-list`, `tutorial-form`.
   2. **IAM Bounded Context**: a generic subdomain, built last.
      - **Domain Layer**: the `User` entity, `SignInCommand`, and `SignUpCommand`.
      - **Infrastructure Layer**: `IamApi`, `SignInAssembler`, `SignUpAssembler`, `UserAssembler`, `SignInResource`, `SignUpResource`, `authenticationGuard`, and `iamInterceptor`.
      - **Application Layer**: `useIamStore`.
      - **Presentation Layer**: `sign-in-form`, `sign-up-form`, `authentication-section`.
   3. **Shared**: the HTTP base classes and cross-cutting presentation.
      - **Infrastructure Layer**: `BaseApi` and `BaseEndpoint`.
      - **Presentation Layer**: `layout`, `language-switcher`, `footer-content`, `home`, `about`, `page-not-found`.

   ## Project Structure

   ```text
   learning-center/
   ├── docs/
   │   ├── adrs.md                         # Architecture Decision Records
   │   ├── c4/                             # C4 model: context, container, and component diagrams
   │   ├── class-diagram.puml              # Class diagram of the three bounded contexts
   │   └── user-stories.md                 # User stories and Requirement Traceability Matrix
   ├── public/
   │   └── acme-logo.svg
   ├── server/
   │   ├── db.json                         # Fake API data: categories and tutorials
   │   └── routes.json                     # Rewrites /api/v1/* for json-server
   ├── src/
   │   ├── iam/                            # domain, infrastructure, application, presentation
   │   ├── locales/
   │   │   ├── en.json                     # English translations
   │   │   └── es.json                     # Spanish translations
   │   ├── publishing/                     # domain, infrastructure, application, presentation
   │   ├── shared/                         # infrastructure, presentation
   │   ├── app.vue                         # Root component
   │   ├── i18n.js
   │   ├── main.js                         # Composition root: plugins and PrimeVue components
   │   ├── pinia.js
   │   ├── router.js
   │   ├── style.css
   │   └── vite-env.d.ts                   # Types of the environment variables
   ├── .env.development
   ├── .env.production
   ├── CHANGELOG.md
   ├── CONTRIBUTING.md
   ├── index.html
   ├── LICENSE.md
   ├── package.json
   ├── README.md
   └── vite.config.js
   ```

   ## Technologies

   - **Framework**: Vue 3 (Composition API, `<script setup>`)
   - **Build tool**: Vite
   - **State**: Pinia
   - **Routing**: Vue Router
   - **UI**: PrimeVue with the Material preset, PrimeFlex, and PrimeIcons
   - **HTTP client**: Axios
   - **Internationalization**: `vue-i18n`
   - **Fake API**: `json-server`
   - **Diagrams**: PlantUML and C4-PlantUML

   ## Documentation

   - **User Stories & RTM**: [docs/user-stories.md](docs/user-stories.md)
   - **Architecture Decision Records**: [docs/adrs.md](docs/adrs.md)
   - **C4 Model**: [docs/c4](docs/c4) (Context, Container, and four Component views).
   - **Class Diagram**: [docs/class-diagram.puml](docs/class-diagram.puml). Open either with a PlantUML plugin or viewer to render them.
   - **Changelog**: [CHANGELOG.md](CHANGELOG.md)
   - **Contributing**: [CONTRIBUTING.md](CONTRIBUTING.md)

   ## Prerequisites

   - Node.js v24.20 or higher
   - npm v10 or higher

   ## Getting Started

   1. Clone the repository:
      ```bash
      git clone <repository-url>
      cd learning-center
      ```
   2. Install dependencies:
      ```bash
      npm install
      ```
   3. Start the fake API in one terminal, from the project root:
      ```bash
      npx json-server --watch server/db.json --routes server/routes.json --port 3000
      ```
   4. Start the development server in another terminal:
      ```bash
      npm run dev
      ```
   5. Open your browser at the address Vite prints, usually `http://localhost:5173/`.

   ## Available Scripts

   - `npm run dev`: starts the development server.
   - `npm run build`: creates a production build in `dist/`.
   - `npm run preview`: serves the production build locally.

   ## Environment Variables

   Vite reads `.env.development` for `npm run dev` and `.env.production` for `npm run build`; a `.env.local` file overrides both and should not be committed. The variables are declared in `src/vite-env.d.ts`:

   - `VITE_LEARNING_PLATFORM_API_URL`: base URL of the API.
   - `VITE_CATEGORIES_ENDPOINT_PATH`, `VITE_TUTORIALS_ENDPOINT_PATH`: paths of the publishing resources.
   - `VITE_SIGNUP_ENDPOINT_PATH`, `VITE_SIGNIN_ENDPOINT_PATH`, `VITE_USERS_ENDPOINT_PATH`: paths of the IAM resources.
   - `VITE_PRIME_UI_LICENSE_KEY`: the PrimeUI license key. Register for a Community license at [PrimeUI](https://primeui.dev) and set your own key in `.env.local`.

   ## Notes

   - Translation files are in `src/locales/`.
   - The fake API serves `categories` and `tutorials`. The sign-in and sign-up paths are declared in the environment files, but `server/db.json` does not serve them; they belong to the real Learning Center Platform backend.
   - The production environment file points to a sandbox API, which needs adjusting for a real deployment.

   ## License

   MIT, see [LICENSE.md](LICENSE.md).
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
   # Contributing to ACME Learning Center

   Thank you for your interest in contributing to the **ACME Learning Center** project! This document outlines the standards and workflows we follow to maintain high code quality and architectural integrity.

   ## Table of Contents
   - [Architectural Principles](#architectural-principles)
     - [Domain-Driven Design (DDD)](#domain-driven-design-ddd)
     - [Object-Oriented Programming (OOP)](#object-oriented-programming-oop)
     - [Vue 3 & Composition API](#vue-3--composition-api)
   - [Development Workflow](#development-workflow)
     - [Git Flow](#git-flow)
     - [Conventional Commits](#conventional-commits)
     - [Semantic Versioning](#semantic-versioning)
   - [Coding Standards](#coding-standards)
   - [Documentation](#documentation)

   ---

   ## Architectural Principles

   ### Domain-Driven Design (DDD)
   We strictly follow a layered architecture organized by Bounded Contexts under `src/`.
   - **Publishing Context**: The core business logic (`Category` and `Tutorial` entities, `PublishingApi`, `usePublishingStore`).
   - **IAM Context**: A generic subdomain (`User`, `SignInCommand`, `SignUpCommand`, `IamApi`, `useIamStore`, `authenticationGuard`, `iamInterceptor`), built after the publishing features.
   - **Shared**: The HTTP base classes (`BaseApi`, `BaseEndpoint`) and cross-cutting presentation (`layout`, `language-switcher`, `footer-content`).
   - **Layers**: Each context is split into layers.

   ```text
   src/<bounded-context>/
   ├── domain/           # Pure business logic: entities and commands. No Vue, no HTTP.
   ├── infrastructure/   # API clients, assemblers, resources, guards, interceptors.
   ├── application/      # Pinia setup stores.
   └── presentation/     # Vue components, views, and routes.
   ```

   Dependencies point toward the domain: the domain layer imports nothing from the other layers.

   ### Object-Oriented Programming (OOP)
   - **Encapsulation**: Native ECMAScript `#` private fields for the internal state of entities, commands, and API classes, exposed only through getters. Entities and commands are immutable: to change one, build a new one.
   - **Assembler Pattern**: Keep the resources the API sends and receives apart from the domain entities, converting between them with an assembler. An entity is never sent as it is, because its `#` fields are not serialized.
   - **Composition over inheritance**: A concrete API (`PublishingApi`, `IamApi`) composes `BaseEndpoint` instances; it extends only `BaseApi`.

   ### Vue 3 & Composition API
   - **`<script setup>`** for every component.
   - **Stores**: Pinia setup stores. Domain entities go in `shallowRef` and are replaced, never mutated; plain UI state (messages, flags) goes in `ref`. Vue must not wrap an entity in a Proxy, because a Proxy cannot read a `#` field (see `docs/adrs.md`, ADR-0004).
   - **State in components**: take state with `storeToRefs(store)` and actions straight from the store. Destructuring state from the store loses reactivity.
   - **PrimeVue**: use the `pv-` prefixed components registered in `src/main.js` for interactive UI elements, and PrimeFlex classes for layout.

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
   - `style`: Changes that do not affect the meaning of the code (white-space, formatting, etc.).
   - `refactor`: A code change that neither fixes a bug nor adds a feature.
   - `perf`: A code change that improves performance.
   - `test`: Adding missing tests or correcting existing tests.
   - `chore`: Changes to the build process or auxiliary tools and libraries.

   **Scopes:** the file, class, or component touched, for example `publishing`, `iam`, `shared`, `category`, `tutorial`, `docs`. Name a Vue component as its file, in kebab-case: `feat(publishing): add category-list component.`

   Example: `feat(publishing): add Category entity.`

   ### Semantic Versioning
   The project adheres to [Semantic Versioning (SemVer)](https://semver.org/): `MAJOR.MINOR.PATCH`.
   - **MAJOR**: Incompatible API changes.
   - **MINOR**: Add functionality in a backwards-compatible manner.
   - **PATCH**: Backwards-compatible bug fixes.

   ---

   ## Coding Standards
   - Follow the naming conventions defined in the project:
     - **Constants**: `UPPER_SNAKE_CASE` for module-level constants.
     - **Variables/Methods**: `lowerCamelCase`.
     - **Classes**: `UpperCamelCase`.
     - **Files and folders**: `kebab-case`, with a role suffix for domain and infrastructure files (`.entity.js`, `.assembler.js`, `.store.js`).
   - Use relative imports with the file extension; the project has no `@` alias.
   - Do not hardcode text in a template; add the key to `src/locales/en.json` and `src/locales/es.json` and read it with `t('key')`.
   - In a PrimeVue `pv-column`, use `field` only for a direct property of the entity (`id`, `title`); for a nested value such as the category name, use a `#body` template, because a dotted `field` does not read the entity getters.
   - Leave no `console.log` in the code.

   ---

   ## Documentation
   - **Architecture Decisions**: New significant architectural choices must be documented in `docs/adrs.md`.
   - **Traceability**: Ensure functional requirements are mapped in the Requirement Traceability Matrix (RTM) within `docs/user-stories.md`.
   - **Diagrams**: Keep `docs/class-diagram.puml` and `docs/c4` in step with the code.
   - **Changelog**: Update `CHANGELOG.md` for every release following the "Keep a Changelog" format.
   ````
   </details>

   **Note:** the coding standard above says never to hardcode text in a template. `sign-in-form`, `sign-up-form`, and `authentication-section` do exactly that, a real gap this guide flagged as it built them, not a rule this project actually holds to everywhere yet.

   ```
   git add .
   git commit -m "docs: add contributing guidelines."
   ```

---

## Release

**Still on `develop`.** Every feature plus the license, README, and contributing guide are merged. `main` shouldn't stay permanently behind `develop`: close the loop with a release.

1. **Start the release.** Git Flow Helper widget → `Release` → `Release Start` → **Version description** `v1.0.0` → `OK`. Creates and switches you to `release/v1.0.0`.

   **Note:** the release name carries a `v` prefix (`v1.0.0`), matching the git tag it becomes on finish. The `package.json` `"version"` stays plain (`1.0.0`), and so does the `CHANGELOG.md` heading (`## [1.0.0]`): npm and Keep a Changelog conventions don't use the prefix.

2. **Add the Architecture Decision Records** to a single `docs/adrs.md`. Right-click the `docs` folder → `New` → `File` → type `adrs.md` → Enter. Nine decisions, written in one sitting: the layered architecture, Pinia setup stores, the assembler pattern, native `#` private fields with entities held in `shallowRef`, runtime internationalization, PrimeVue with the Material preset, IAM as a generic subdomain built last, the json-server fake API, and relative imports with no path alias.

   <details>
   <summary>docs/adrs.md</summary>

   ````markdown
   # Architecture Decision Records

   # ADR-0001: Layered Architecture Organized by Bounded Contexts

   **Status:** Accepted

   ## Context

   The application manages a publishing catalog (categories and tutorials), lets users register and sign in, and renders everything with PrimeVue. Left together in the components, backend response shapes, session handling, and business rules leak into the views and can't be tested without the framework.

   ## Decision Drivers

   - Business rules should live in plain JavaScript, with no Vue import.
   - Backend response shapes should stay isolated behind an assembler, never passed straight into a component.
   - The structure should read the same as the other course projects: `domain`, `infrastructure`, `application`, and `presentation` layers inside a bounded context.
   - Identity and access management is not the reason this application exists; it should not shape the core domain.

   ## Considered Options

   1. Three bounded contexts (`publishing`, `iam`, `shared`), each split into layers *(Chosen)*
   2. Keep all logic in the components, no domain or infrastructure layer
   3. A single flat `components` and `services` split with no bounded-context boundary

   ## Decision

   Three bounded contexts under `src`:

   - **Publishing**: the core domain. `publishing/domain/model` holds `Category` and `Tutorial`; `publishing/infrastructure` holds `PublishingApi`, `CategoryAssembler`, and `TutorialAssembler`; `publishing/application` holds `usePublishingStore`; `publishing/presentation` holds `category-list`, `category-form`, `tutorial-list`, `tutorial-form`, and the publishing routes.
   - **IAM**: a generic subdomain. `iam/domain` holds `User`, `SignInCommand`, and `SignUpCommand`; `iam/infrastructure` holds `IamApi`, `SignInAssembler`, `SignUpAssembler`, `UserAssembler`, the sign-in and sign-up resources, `authenticationGuard`, and `iamInterceptor`; `iam/application` holds `useIamStore`; `iam/presentation` holds `sign-in-form`, `sign-up-form`, `authentication-section`, and the IAM routes.
   - **Shared**: `shared/infrastructure` holds `BaseApi` and `BaseEndpoint`; `shared/presentation` holds `layout`, `language-switcher`, `footer-content`, `home`, `about`, and `page-not-found`.

   Components are presenters or orchestrators; every backend response is translated into a domain entity by an assembler before a component ever sees it. Dependencies point toward the domain.

   ## Consequences

   **Positive:**
   - `Category` and `Tutorial` are unit-testable with no Vue at all.
   - A change to the backend's response shape is one edit, in one assembler.
   - IAM can be replaced or removed without touching the publishing domain.

   **Negative:**
   - More folders than a small application strictly needs. The layout pays off as soon as a second entity or a second context appears, and it matches the rest of the course.
   - The shared `layout` renders `authentication-section` from IAM, and the shared `BaseApi` adds `iamInterceptor` from IAM, so Shared knows about a context that depends on it. Both dependencies are drawn as they are in the C4 component diagrams.

   ---

   # ADR-0002: Pinia Setup Stores for Application State

   **Status:** Accepted

   ## Context

   Each bounded context needs one place that holds its state, loads it from the API, and tells the views when it changes. Vue's Composition API offers `ref`, `shallowRef`, and `computed`, and Pinia gives them a name, a single instance per application, and devtools support.

   ## Decision Drivers

   - The views should read state and call actions, and never talk to the API themselves.
   - A store should be plain to read for someone who already knows the Composition API.
   - The code should match the Composition-API-first style the course teaches.

   ## Considered Options

   1. One Pinia **setup store** per bounded context (`usePublishingStore`, `useIamStore`) *(Chosen)*
   2. Pinia option stores (`state`, `getters`, `actions`)
   3. Module-level `ref`s shared between components, with no store library
   4. State kept inside each component

   ## Decision

   A setup store is a function that declares `ref`, `shallowRef`, and `computed` values and plain functions, then returns the ones the views may use. `usePublishingStore` holds the `categories` and `tutorials` lists, an `errors` list of messages, the `categoriesLoaded` and `tutorialsLoaded` flags, two computed counts, and the actions that load, add, update, and delete. `useIamStore` holds the session (`isSignedIn`, `currentUsername`, `currentUserId`, `currentToken`) and the sign-in, sign-up, and sign-out actions. Views take state with `storeToRefs(store)` so it stays reactive and take actions straight from the store. Only a store calls an API class, and a store never imports a component.

   ## Consequences

   **Positive:**
   - One place per context decides what state exists and how it changes.
   - Views stay thin: they read refs, call actions, and render.
   - Pinia devtools show the state of each store.

   **Negative:**
   - Destructuring state from a store, `const {categories} = store`, copies the value and loses reactivity; `storeToRefs` is required for state, and plain destructuring is only correct for actions.
   - Everything a setup store returns is public; the convention, not the language, keeps a helper out of the return statement.

   ---

   # ADR-0003: Assembler Pattern and Generic REST Infrastructure

   **Status:** Accepted

   ## Context

   The backend's resources differ from the domain entities in shape and naming, and every REST endpoint repeats the same five operations: list, get, create, update, and delete. The entities also keep their state in native `#` private fields (ADR-0004), so `JSON.stringify(entity)` gives `{}`: an entity cannot be sent to the backend as it is.

   ## Decision Drivers

   - Domain entities must not depend on the wire format.
   - Adding an endpoint should mean declaring its assembler, not rewriting CRUD.
   - What travels over HTTP must be an explicit shape, never an entity serialized by accident.

   ## Considered Options

   1. An assembler per entity plus a generic base endpoint that owns CRUD *(Chosen)*
   2. Map JSON to entities inline in each store action
   3. Use the backend's resources directly as the domain model

   ## Decision

   Shared infrastructure defines the generic behavior:

   - `BaseApi`: creates the Axios client from `VITE_LEARNING_PLATFORM_API_URL` and adds the IAM interceptor; concrete APIs (`PublishingApi`, `IamApi`) extend it.
   - `BaseEndpoint`: generic `getAll()`, `getById()`, `create()`, `update()`, and `delete()` over one endpoint path.

   `PublishingApi` and `IamApi` compose `BaseEndpoint` instances, one per path taken from the environment variables. Each assembler is a class of static methods: `CategoryAssembler` and `TutorialAssembler` build entities from a resource or a response (`toEntityFromResource()`, `toEntitiesFromResponse()`) and build the resource from an entity (`toResourceFromEntity()`); `SignInAssembler` and `SignUpAssembler` build the request from a command (`toRequestFromCommand()`) and the resource from a response (`toResourceFromResponse()`); `UserAssembler` builds a `User`. A store always sends a resource, never an entity. Names say "resource" for what travels over HTTP and "entity" for what the domain holds.

   ## Consequences

   **Positive:**
   - Domain entities never change because the backend did; only an assembler does.
   - A new REST endpoint is a path in the environment file plus an assembler.
   - What goes over the wire is written down in one place.

   **Negative:**
   - An assembler class for every entity, even when the resource and the entity look alike today.
   - `BaseApi` imports `iamInterceptor` from IAM, so the shared kernel depends on a context that depends on it.

   ---

   # ADR-0004: Native Private Fields (`#`) with Entities Held in `shallowRef`

   **Status:** Accepted

   ## Context

   Domain entities and commands hold state that only their own methods should change, and a native `#field` gives real encapsulation at runtime. Vue can wrap reactive objects in a `Proxy`, though. `reactive()` and `ref()` wrap in depth, so an entity stored in one of them becomes a `Proxy`, and a getter that reads `this.#field` through it throws `TypeError: Cannot read private member from an object whose class did not declare it`. JavaScript checks that the object used as `this` carries the private brand of the class that declared the field, and a `Proxy` is a different object, without that brand.

   A component that receives an entity as a prop cannot fix this either: if the parent already handed it over as a `Proxy`, the child keeps seeing the `Proxy`. The entity has to enter Vue as a `shallowRef` from the start.

   ## Decision Drivers

   - `#field` should not be traded away for a framework detail; every domain class follows the same rule.
   - The entities are immutable by design: a store only needs to know when a list is *replaced*, never when something inside an entity changes, because nothing does.
   - The rule has to be easy to teach and hold for every entity, however it is built.

   ## Considered Options

   1. `_field` convention on the entities, so a deep `ref` never has to wrap a private field
   2. Native `#field` with `markRaw(entity)` at every place an entity is built (more invasive, easy to forget one)
   3. Native `#field`, entity lists in `shallowRef`, plain UI state in `ref` *(Chosen)*
   4. A plain ViewModel between the domain and Vue (clean in a large application, but a second model and its mappings, for a course centered on entities, encapsulation, and DDD)
   5. Plain public fields, no encapsulation at all

   ## Decision

   Vue can wrap reactive objects in a `Proxy`. Domain entities that use native private fields (`#`) must not be proxied. That is why we store them with `shallowRef` and update state by replacing references. Plain UI state (messages, flags) stays in `ref`.

   - **UI state** (`ref`): deep reactivity, `Proxy`.
   - **Domain objects** (`shallowRef`): the real object, untouched.

   In `usePublishingStore`, `categories` and `tutorials` are `shallowRef`, and `errors`, `categoriesLoaded`, and `tutorialsLoaded` are `ref`. Entities are updated by replacement: `categories.value = [...categories.value, created]` to add, `.map(...)` to update, `.filter(...)` to delete. `useIamStore` does the same with `users`. Errors are just messages (`string[]`), so `errors.value.push(message)` works as usual. A tutorial references its category, so the store builds a new `Tutorial` with its `Category` (`withCategory`) whenever tutorials or categories load, are added, updated, or deleted, whichever of the two lists arrives first. `Category`, `Tutorial`, `User`, `SignInCommand`, and `SignUpCommand` have only getters; to change an entity, a form builds a new one.

   ## Consequences

   **Positive:**
   - Real encapsulation: `category.#name` is a syntax error outside the class, not a convention nobody happens to break.
   - One privacy convention across the whole project, and one rule for Vue: entities in `shallowRef`, UI state in `ref`.
   - The rule is applied once, in the store, and never has to be repaired further down the tree.

   **Negative:**
   - An entity inside a `shallowRef` must be replaced, never mutated in place (`push`, `splice`, index assignment): Vue would not notice. Not a new constraint here, since entities are immutable and the stores already replace whole arrays.
   - An entity cannot be serialized by `JSON.stringify`; an assembler builds the resource (ADR-0003).
   - PrimeVue resolves a column `field` with `resolveFieldData`, which reads dotted paths (`category.name`) only from own enumerable keys. An entity keeps its fields in `#` fields and exposes getters on the prototype, so a dotted `field` renders empty (`field="id"` and `field="title"` still work). A column that needs a nested value uses a `#body` template (`slotProps.data.category?.name`) and is not sortable.

   ---

   # ADR-0005: Runtime Internationalization with vue-i18n

   **Status:** Accepted

   ## Context

   The application shows text in English or Spanish, chosen by the user at runtime, not decided once at build time.

   ## Decision Drivers

   - A user must be able to switch languages without a page reload.
   - Adding a new translated string should mean editing a JSON file.
   - The library should integrate with the Composition API.

   ## Considered Options

   1. `vue-i18n` in Composition API mode, with one JSON dictionary per language under `src/locales/` *(Chosen)*
   2. A separate build per locale
   3. Hardcoded strings with a manual `if` on a language field

   ## Decision

   `src/i18n.js` creates the instance with `legacy: false`, `locale: 'en'`, `fallbackLocale: 'en'`, and the messages of `src/locales/en.json` and `src/locales/es.json`. Components call `useI18n()` and translate with `t('key')`. `language-switcher` binds a `pv-select-button` to the `locale` ref, so choosing a language changes every translated text at once, with no reload.

   ## Consequences

   **Positive:**
   - A language switch is instant and needs no new build.
   - New translated text is a JSON key, addable without touching a component's logic.

   **Negative:**
   - Both dictionaries are part of the main bundle.
   - A missing key falls back to English, or shows the key, rather than failing at compile time; a typo in a key surfaces only by looking at the page.

   ---

   # ADR-0006: PrimeVue with the Material Preset and PrimeFlex

   **Status:** Accepted

   ## Context

   The application needs accessible, ready-made components (toolbar, data table, paginator, form fields, buttons, confirmation dialog) with a consistent look, without writing a design system.

   ## Decision Drivers

   - Components must be accessible by default.
   - The theme should be configured in one place.
   - Layout utilities should not require writing CSS for every screen.

   ## Considered Options

   1. PrimeVue 5 with the Material preset from `@primeuix/themes`, PrimeFlex for layout, and PrimeIcons *(Chosen)*
   2. Vuetify
   3. Hand-written components and CSS

   ## Decision

   `src/main.js` installs PrimeVue with the Material preset and `ripple: true`, passes the license key from `VITE_PRIME_UI_LICENSE_KEY`, and registers, with a `pv-` prefix, only the components the templates use: `pv-button`, `pv-column`, `pv-confirm-dialog`, `pv-data-table`, `pv-drawer`, `pv-float-label`, `pv-input-text`, `pv-select`, `pv-select-button`, `pv-textarea`, and `pv-toolbar`; `ConfirmationService` backs `useConfirm()`. One `pv-confirm-dialog` lives in `layout`, so a confirmation shows a single dialog. PrimeFlex classes (`flex`, `mt-7`, `bg-primary`) handle spacing and layout.

   ## Consequences

   **Positive:**
   - One place configures the look of every component, and accessibility (focus, contrast, keyboard) comes with them.
   - A screen needs a template and a few utility classes, not a stylesheet.

   **Negative:**
   - Someone new has to learn PrimeVue component and PrimeFlex class names.
   - The global registration is a list to keep in step with the templates: an unregistered `pv-` tag renders as an unknown element without failing the build.

   ---

   # ADR-0007: IAM as a Generic Subdomain, Built Last

   **Status:** Accepted

   ## Context

   Signing up, signing in, and protecting routes is necessary, but it is not what makes this application different; the catalog of categories and tutorials is. In DDD terms, IAM is a generic subdomain.

   ## Decision Drivers

   - The core domain should be built first and should not wait on authentication.
   - The core domain must not depend on how a user is authenticated.
   - The session should be simple to inspect and to clear while learning.

   ## Considered Options

   1. An `iam` bounded context built after the publishing features, with a route guard and an HTTP interceptor wired in at the end *(Chosen)*
   2. Authentication first, then the catalog
   3. No authentication

   ## Decision

   The `iam` context is built after every publishing feature. `useIamStore.signIn()` stores the token returned by the API in `localStorage` under `token` and sets the session refs; `signOut()` clears both. `authenticationGuard` sends anonymous users to `/iam/sign-in` for every route that is not public (`/iam/sign-in`, `/iam/sign-up`, `/about`) and for the route named `not-found`, so an anonymous user who types an unknown URL sees the Page Not Found view; the global `router.beforeEach` sets the page title and returns the guard's result. `iamInterceptor` adds `Authorization: Bearer <token>` to every outgoing request when a user is signed in. The publishing context has no import from `iam`.

   ## Consequences

   **Positive:**
   - The publishing features work and are demonstrable before any authentication exists.
   - IAM is replaceable: the publishing context never imports it.

   **Negative:**
   - A token in `localStorage` is readable by any script on the page; it is acceptable for a learning project, not for production.
   - Protecting routes takes edits to `router.js` and `base-api.js` after the fact, and the shared `layout` renders IAM's `authentication-section`.

   ---

   # ADR-0008: A json-server Fake API for Development

   **Status:** Accepted

   ## Context

   The publishing features need a REST backend to talk to, and the real Learning Center Platform backend is a separate project.

   ## Decision Drivers

   - The frontend should run with nothing but Node.js installed.
   - The URLs and resource shapes should match what the real backend will expose, so switching is one setting.
   - Seed data should live in one file a student can read and change.

   ## Considered Options

   1. `json-server` serving `server/db.json`, with `server/routes.json` rewriting `/api/v1/*` *(Chosen)*
   2. A hand-written mock inside the Vue application
   3. Wait for the real backend

   ## Decision

   `server/db.json` holds the `categories` and `tutorials` collections and `server/routes.json` maps `/api/v1/*` to `/$1`, so the application calls `http://localhost:3000/api/v1/categories` exactly as it will call the real backend. The base URL and every endpoint path live in `.env.development` and `.env.production` and are read with `import.meta.env`; `src/vite-env.d.ts` declares them. The development file carries the commented address of the Learning Center Platform. The `authentication` endpoint paths are declared in the environment files, but `db.json` does not serve them; they belong to the real backend.

   ## Consequences

   **Positive:**
   - Full create, read, update, and delete on categories and tutorials with one command.
   - Moving to the real backend is a base-URL edit.

   **Negative:**
   - Sign-up and sign-in have no fake implementation, so the IAM screens need the real backend to complete.
   - json-server keeps its data in the file, so a demo session changes `db.json` unless the file is restored.

   ---

   # ADR-0009: Relative Imports, No Path Alias

   **Status:** Accepted

   ## Context

   A project created with `create-vite` has no `@` alias for `src`, while a project created with `create-vue` does. An import such as `@/publishing/domain/model/category.entity.js` fails to resolve in the first kind of project, and configuring the alias twice (Vite and the editor) is one more thing to keep in step.

   ## Decision Drivers

   - Every import in the guide must work in a project created the way the guide says.
   - A student should not need an alias to run the code, and the editor should follow every import.

   ## Considered Options

   1. Relative imports everywhere (`../domain/model/category.entity.js`) *(Chosen)*
   2. A `@` alias declared in `vite.config.js` and in `jsconfig.json`

   ## Decision

   Every import uses a relative path with the file extension, and `vite.config.js` is the plain `create-vite` one, with only the Vue plugin.

   ## Consequences

   **Positive:**
   - The code runs in the project the guide creates, with no extra configuration, and the editor resolves every import.
   - The path of an import shows how far apart two files are, which is a hint about the layers between them.

   **Negative:**
   - Deep imports get long (`../../../shared/infrastructure/base-endpoint.js`), and moving a file means fixing its imports.
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

   ````markdown
   # Changelog

   All notable changes to this project will be documented in this file.

   The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/),
   and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

   ## [1.0.0] - 2026-09-20

   ### Added
   - `Publishing`, `IAM`, and `Shared` bounded contexts with a layered structure (`domain`, `infrastructure`, `application`, `presentation`).
   - `Category` and `Tutorial` entities, `CategoryAssembler` and `TutorialAssembler`, and `PublishingApi`, with `usePublishingStore` holding the state (US001, US002).
   - `category-list`, `category-form`, `tutorial-list`, and `tutorial-form`: list, create, edit, and delete categories and tutorials, with a required category on every tutorial.
   - `User`, `SignInCommand`, and `SignUpCommand`, `IamApi` with its assemblers and resources, and `useIamStore`; `sign-in-form`, `sign-up-form`, and `authentication-section` (US003, US004).
   - `authenticationGuard` to protect every route that is not public, and `iamInterceptor` to add the session token to every request.
   - `BaseApi` and `BaseEndpoint` in the Shared kernel.
   - `layout`, `language-switcher`, `footer-content`, `home`, `about`, and `page-not-found`: navigation, English/Spanish switching, attribution, and a page for unknown routes (US005, US006).
   - A fake REST API with `json-server` (`server/db.json`, `server/routes.json`).
   - `docs/user-stories.md` with a Requirement Traceability Matrix, `docs/c4`, `docs/class-diagram.puml`, `docs/adrs.md`.
   - `README.md`, MIT `LICENSE.md`, `CONTRIBUTING.md`.

   ### Fixed
   - Found while modernizing: entities and commands keep their state in native `#` private fields with getters only, and the stores keep entity lists in `shallowRef` and replace them instead of mutating them.
   - Assemblers build the resource that is sent to the API (`toResourceFromEntity()`, `toRequestFromCommand()`), because an entity with `#` fields is not serialized by `JSON.stringify`.
   - `category-form`, `tutorial-form`, `category-list`, and `tutorial-list` took state from the store by destructuring, which loses reactivity: the category select of a tutorial form opened directly was empty, and errors never showed. They now use `storeToRefs`.
   - Removed the duplicate `pv-confirm-dialog` from both lists, which showed two dialogs on every delete; the one in `layout` remains.
   - Editing a category or a tutorial sends its numeric id instead of the route parameter string.
   - `Tutorial.category` is populated: `usePublishingStore` builds each tutorial with its `Category` (`withCategory`) after every load, add, update, and delete of tutorials or categories, whichever list arrives first; `tutorial-list` fetches categories too and shows the category name, and the `tutorials.category-id` key is gone.
   - `authenticationGuard` no longer lists `/page-not-found` as public and lets the route named `not-found` through, so an anonymous user who types an unknown URL sees the Page Not Found view instead of sign-in.
   - The category column of `tutorial-list` renders `slotProps.data.category?.name` in a `#body` template and is not sortable: PrimeVue reads a dotted `field` (`category.name`) only from own enumerable keys, and entities expose getters (ADR-0004).
   - The Title column of `tutorial-list` used the page title key; it now uses `tutorial.title`.
   - Removed every `console.log` (one of them printed the sign-in credentials and another the session token), and the store errors are plain messages.
   - The router guard returns the redirect instead of calling `next`, and `authenticationGuard` follows it.
   - `main.js` registers only the PrimeVue components the templates use.
   - Removed the Firebase hosting files, unrelated to the course flow.
   - `server/routes.json` uses `{"/api/v1/*": "/$1"}` and `vite.config.js` is the plain `create-vite` one.

   ### Design notes
   - Vue components use the Composition API with `<script setup>`; state lives in Pinia setup stores.
   - Domain entities and commands are immutable by design: state in `#` private fields, only getters, everything resolved before the constructor runs. A store never mutates an entity, it replaces it.
   - Every import is relative and carries its file extension; there is no `@` alias.
   - Runtime English/Spanish translations through `vue-i18n`, bundled from `src/locales/`.
   ````
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

   - **US001:** list, create, edit, and delete tutorial categories, in a sortable, paginated table.
   - **US002:** list, create, edit, and delete tutorials, each with a required category, its name shown in the list.
   - **US003:** register a new account.
   - **US004:** sign in, sign out, and every protected route redirects an anonymous visitor to sign in first; an unknown address still shows Page Not Found.
   - **US005:** switch between English and Spanish, everywhere, at runtime.
   - **US006:** a toolbar with navigation, a footer with attribution, and a page for unknown routes.
   - `Category` and `Tutorial` entities, `User`, `SignInCommand`, and `SignUpCommand`: native `#` private fields, getters only, immutable by design; `usePublishingStore` and `useIamStore` hold their entity lists in `shallowRef` so Vue never wraps them in a `Proxy`.
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
     gh repo clone <org>/learning-center
     ```

   - Open the `learning-center` folder in WebStorm afterward (`File` → `Open`). Or, from the JetBrains Welcome screen, `Clone Repository`, paste `https://github.com/<org>/learning-center.git`, pick a folder, `Clone`.

2. **Restore the dependencies.** A clone has no `node_modules/`:

   ```
   npm install
   ```

3. **Fill in your own key.** The clone already has `.env.development` and `.env.production` with the demo value from Project Setup step 14-15. Replace the PrimeVue Community license key in each with your own (Project Setup step 14), it is not something `npm install` restores.

4. **Reinstall the tools that live outside the repo.** Plugins live in the IDE, not the repo: reinstall the Git Flow Helper plugin (Project Setup step 29) if this machine does not have it.

5. **Reinstate Git Flow.**
   - Check out `develop` before anything else: a fresh clone only has `main` as a local branch. Do it before `Init`, so Git Flow Helper registers against the existing `develop` instead of creating a new one.

     ```
     git checkout develop
     ```

   - Register your GitHub account in the IDE: get the token with

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

The guide uses `gh auth login` (Project Setup step 28), which is the simplest way. If you can't install `gh`, GitHub also accepts a Personal Access Token.

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
   - Click **Generate token**, then copy it somewhere safe (a password manager) before navigating away. GitHub shows it **only once**. The IDE GitHub account needs it too.
2. Back in the terminal where the push is waiting:
   - **macOS:** type your GitHub username, then paste the token as the password (nothing shows as you paste, that's normal).
   - **Windows:** in the "Connect to GitHub" window, pick the `Token` tab and paste it there.
3. It is cached after this, so it won't ask again for the rest of the project.

**Note:** use `(classic)`, not "Fine-grained tokens": fine-grained tokens need the organization owner to approve them first, which can leave you waiting. With `repo` scope the token reaches every repo your account can, so reuse it across the other course projects.

### Creating the repo without the GitHub CLI

No `gh`? Do the whole thing through the GitHub website plus plain `git`.

1. Authenticate git first, since `gh auth login` isn't available: follow [Signing in to GitHub with a token](#signing-in-to-github-with-a-token).
2. On GitHub, inside your organization, create an empty **private** repo named `learning-center`, with no README, license, or `.gitignore` (this project already has all three).
3. On the repo's "Quick setup" page, copy the **HTTPS** clone URL, the one ending in `.git` (`https://github.com/<org>/learning-center.git`, `<org>` is your organization's name), not the address-bar URL.
4. From the project root, add the remote and push:

   ```
   git remote add origin https://github.com/<org>/learning-center.git
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

Then initialize from the project root as Project Setup step 4 describes:

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

For example, if the project is in `~/Documents/wa-projects/learning-center`:

```
sudo chown -R "$(whoami):$(id -gn)" ~/Documents/wa-projects/learning-center
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

The six diagrams under `docs/c4/` have an extra requirement: each starts with `!includeurl`, which downloads the C4-PlantUML macro definitions from GitHub the first time it renders. If one of them errors out specifically on the `!includeurl` line, check the machine has internet access and isn't behind a proxy or firewall blocking `raw.githubusercontent.com`.

### Free JetBrains license for students

WebStorm is free for students through the [JetBrains Student Pack](https://www.jetbrains.com/community/education/#students): apply with a school email address, or upload proof of enrollment if your school email isn't recognized. Approval usually takes a few minutes. The license covers the whole JetBrains suite and renews each year you're enrolled.

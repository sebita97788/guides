# JavaScript Review Guide

## Table of Contents

- [Project Setup](#project-setup)
- [(US001) Registering a Supplier](#registering-a-supplier-us001)
- [(US002) Recording the Last Order Total for a Supplier](#recording-the-last-order-total-for-a-supplier-us002)
- [(US003) Creating a Purchase Order](#creating-a-purchase-order-us003)
- [(US004) Adding Items to a Purchase Order](#adding-items-to-a-purchase-order-us004)
- [(US005) Calculating the Total Price](#calculating-the-total-price-us005)
- [(US006) Cancelling a Purchase Order](#cancelling-a-purchase-order-us006)
- [(US007) Managing the Purchase Order Lifecycle](#managing-the-purchase-order-lifecycle-us007)
- [Prepare the First Release](#prepare-the-first-release)
- [Release](#release)
- [Document the Project](#document-the-project)
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

1. **Install Node.js (the latest LTS).** npm comes bundled with it.
   - macOS:

     ```
     brew install node@24
     ```

   - Windows: download the **LTS** installer from [nodejs.org/en/download](https://nodejs.org/en/download) (the page shows "LTS" and "Current" side by side, pick **LTS**) and run it.
   - Check both in a terminal:

     ```
     node --version
     ```

     ```
     npm --version
     ```

   **Note:** `24` is the LTS line as of this guide. [nodejs.org](https://nodejs.org) shows the current LTS major on its front page; if it's higher, use that number in the `brew` formula (`node@26`, and so on). The LTS majors are always even; an odd major (`v25`) is the "Current" build, not LTS.

2. **Create the WebStorm project.**
   - Project already open: `File` → `New Project...`. On the Welcome screen: click **New Project**.
   - Location: any local path you prefer, ending in `javascript-review`. There is no separate name field; the last folder in `Location` becomes the project name.
   - Language: leave it at the default, **JavaScript** (the selector right below `Location`).
   - Click **Create**.

   **Note:** if WebStorm asks you to choose a Node interpreter, point it at the Node.js you installed in step 1.

3. **Configure the project.** The JavaScript project wizard generated a `package.json` and an `index.js` at the project root. Open `package.json` and replace its contents with:

   <details>
   <summary>package.json</summary>

   ```json
   {
     "name": "javascript-review",
     "version": "0.1.0",
     "description": "A sample JavaScript project demonstrating Object-Oriented Programming (OOP) and Domain-Driven Design (DDD) concepts in a Supply Chain and Procurement context.",
     "main": "src/index.js",
     "type": "module",
     "scripts": {
       "start": "node src/index.js",
       "dev": "node --watch src/index.js",
       "lint": "eslint .",
       "format": "prettier --write .",
       "test": "npm run lint"
     },
     "keywords": [
       "javascript",
       "oop",
       "ddd",
       "procurement",
       "scm",
       "clean-code"
     ],
     "dependencies": {
       "uuid": "^14.0.2"
     },
     "devDependencies": {
       "@eslint/js": "^10.0.1",
       "eslint": "^10.9.1",
       "globals": "^17.11.0",
       "prettier": "^3.9.6"
     },
     "author": "Web Application Development Team",
     "license": "MIT",
     "private": true
   }
   ```
   </details>

   **Note:** starting at `0.1.0`, below `1.0.0`, signals early development: the code's structure and behavior can still change freely from one version to the next. `## Release` at the end of this guide bumps it to `1.0.0`, the first version meant to stay stable.

   **Note:** `"type": "module"` puts the whole project on ECMAScript modules, so every file uses `import` / `export` and every relative import carries an explicit `.js` extension. `uuid` is the only runtime dependency: it generates the time-ordered UUID v7 identifiers the value objects use. `eslint` and `prettier` are dev-only tools, wired to `npm run lint` (which `npm test` also runs) and `npm run format`.

4. **Add the linting and formatting configuration.** Three files at the project root. Right-click the project root → `New` → `File` for each.

   <details>
   <summary>eslint.config.js</summary>

   ```javascript
   import js from '@eslint/js';
   import globals from 'globals';

   export default [
     js.configs.recommended,
     {
       languageOptions: {
         ecmaVersion: 2022,
         sourceType: 'module',
         globals: {
           ...globals.node,
         },
       },
       rules: {
         'no-unused-vars': 'warn',
         'no-console': 'off',
       },
     },
   ];
   ```
   </details>

   <details>
   <summary>.prettierrc</summary>

   ```json
   {
     "semi": true,
     "singleQuote": true,
     "tabWidth": 2,
     "trailingComma": "es5",
     "printWidth": 100
   }
   ```
   </details>

   <details>
   <summary>.prettierignore</summary>

   ```
   node_modules
   package-lock.json
   docs
   ```
   </details>

   **Note:** the ESLint config uses the recommended ruleset (unused variables, unreachable code, duplicate keys, and so on); it does not enforce a brace or indentation style, Prettier owns formatting. `.prettierignore` keeps Prettier off `docs/` so this guide's own layout is never reflowed.

5. **Install the dependencies.** Open the WebStorm terminal (`View` → `Tool Windows` → `Terminal`) and run:

   ```
   npm install
   ```

   **Note:** `npm i` is the same command, just shorter.

   **Note:** this downloads `uuid`, `eslint`, `prettier`, and their dependencies into `node_modules` and writes a `package-lock.json`. There is no compiler or build step: `npm start` runs `src/index.js` straight through Node.

6. **Replace the wizard's `index.js` with an empty `src/index.js`.** The wizard created `index.js` at the project root, but this project keeps its entry point at `src/index.js` (which `package.json`'s `main` and `start` script already expect).
   - Delete the root `index.js`.
   - Right-click the project root → `New` → `JavaScript File` → type `src/index` → Enter. Typing the `src/` prefix creates that folder too. Leave the file blank.

   Through the rest of this guide you build a short demo in `src/index.js` and run it with `npm start` to check each feature by hand.

7. **Check the toolchain runs.** Still in the terminal:

   ```
   npm start
   ```

   An empty `src/index.js` prints nothing and exits cleanly. Then:

   ```
   npm test
   ```

   `npm test` runs ESLint over the project. With no source code yet it reports nothing. No error from either command means Node, npm, and the tooling are set up correctly.

8. **Create `docs/user-stories.md`.** Right-click the project root → `New` → `File` → type `docs/user-stories.md` → Enter.

   **Tip:** typing the `docs/` prefix creates that folder too.

   <details>
   <summary>docs/user-stories.md</summary>

   ```markdown
   # User Stories

   The following user stories describe the main features of the Supply Chain & Procurement console script.

   ## Requirement Traceability Matrix

   | User Story | Scenario                                     | Implementation                                                                                       |
   |------------|----------------------------------------------|-----------------------------------------------------------------------------------------------------|
   | US001      | Register a new supplier                       | `Supplier` constructor, `SupplierId` (scm)                                                          |
   | US001      | Reject a name outside 2 to 100 characters     | `Supplier` constructor (throws)                                                                    |
   | US001      | Reject an invalid contact email               | `Supplier` constructor, `Supplier.#isValidEmail` (throws)                                          |
   | US002      | Record the last order total for a supplier    | `Supplier.recordOrder()`                                                                           |
   | US002      | Reject a total that is not a `Money`          | `Supplier.recordOrder()` (throws)                                                                 |
   | US003      | Create a purchase order in the default state  | `PurchaseOrder` constructor, `PurchaseOrderId`, `SupplierId` (procurement), `PurchaseOrderState`   |
   | US003      | Reject an unsupported currency                | `Currency` constructor (throws)                                                                    |
   | US004      | Add an item to a draft purchase order         | `PurchaseOrder.addItem()`, `PurchaseOrderItem`, `ProductId`, `Money`                               |
   | US004      | Attempt to add an item to a submitted order   | `PurchaseOrder.addItem()` (throws)                                                                 |
   | US004      | Reject a unit price in another currency       | `PurchaseOrder.addItem()` (throws)                                                                 |
   | US004      | Reject a quantity outside 1 to 1000           | `PurchaseOrderItem` constructor (throws)                                                            |
   | US004      | Reject the 51st item                          | `PurchaseOrder.addItem()` (throws)                                                                 |
   | US005      | Calculate the total price with multiple items | `PurchaseOrder.calculateTotalPrice()`, `PurchaseOrderItem.calculateSubtotal()`, `Money.add()` / `Money.multiply()` |
   | US005      | Attempt to calculate the total of an empty order | `PurchaseOrder.calculateTotalPrice()` (throws)                                                  |
   | US006      | Cancel an order that is not completed         | `PurchaseOrder.cancel()`, `PurchaseOrderState.toCanceledFrom()`                                    |
   | US006      | Attempt to cancel a completed order           | `PurchaseOrderState.toCanceledFrom()` (throws)                                                     |
   | US007      | Walk the order through its full lifecycle     | `PurchaseOrder.submit()` / `approve()` / `ship()` / `complete()`, `PurchaseOrderState` transitions |
   | US007      | Attempt an out-of-sequence transition         | `PurchaseOrderState` transitions (throw)                                                           |

   ## US001: Registering a Supplier
   As a procurement manager, I want to register a supplier with a name and an optional contact email so that purchase orders can be raised against them.

   ### Acceptance Criteria
   - **Scenario: Register a new supplier**
       - **Given** a supplier's name and, optionally, a contact email,
       - **When** a procurement manager registers the supplier,
       - **Then** the supplier is created with a unique ID, that name, the contact email, and no last order total yet.
   - **Scenario: Reject a name outside 2 to 100 characters**
       - **Given** a name shorter than 2 characters or longer than 100,
       - **When** a procurement manager attempts to register the supplier,
       - **Then** an error is thrown and no supplier is created.
   - **Scenario: Reject an invalid contact email**
       - **Given** a contact email that is not a well-formed address,
       - **When** a procurement manager attempts to register the supplier,
       - **Then** an error is thrown and no supplier is created.

   ## US002: Recording the Last Order Total for a Supplier
   As a procurement manager, I want to record the total of a supplier's most recent order so that I can review their order history.

   ### Acceptance Criteria
   - **Scenario: Record the last order total**
       - **Given** a registered supplier and a purchase order total as a `Money` value,
       - **When** the total is recorded against the supplier,
       - **Then** the supplier's last order total reflects that value.
   - **Scenario: Reject a total that is not a `Money`**
       - **Given** a value that is not a `Money`,
       - **When** a procurement manager attempts to record it as the last order total,
       - **Then** an error is thrown and the supplier's last order total is unchanged.

   ## US003: Creating a Purchase Order
   As a procurement manager, I want to create a purchase order for a supplier so that I can begin ordering products.

   ### Acceptance Criteria
   - **Scenario: Create a purchase order in the default state**
       - **Given** a supplier that already exists and a supported currency,
       - **When** a procurement manager creates a purchase order with the supplier's ID and that currency,
       - **Then** the purchase order is created with a unique ID, the supplier reference, the currency, an order date (defaulting to now), no items, and a state of "Draft".
   - **Scenario: Reject an unsupported currency**
       - **Given** a currency code that is not one of USD, EUR, GBP, JPY,
       - **When** a procurement manager attempts to create the currency for the order,
       - **Then** an error is thrown and no purchase order is created.

   ## US004: Adding Items to a Purchase Order
   As a procurement manager, I want to add items to a purchase order so that I can specify the products and quantities needed.

   ### Acceptance Criteria
   - **Scenario: Add an item to a draft purchase order**
       - **Given** a purchase order in the "Draft" state,
       - **When** a procurement manager adds an item with a product ID, a quantity, and a unit price in the order's currency,
       - **Then** the item is added to the order and the total price can be calculated.
   - **Scenario: Attempt to add an item to a submitted order**
       - **Given** a purchase order that is no longer in the "Draft" state,
       - **When** a procurement manager attempts to add an item,
       - **Then** an error is thrown indicating that items can only be added while the order is "Draft".
   - **Scenario: Reject a unit price in another currency**
       - **Given** a draft purchase order in one currency,
       - **When** a procurement manager adds an item priced in a different currency,
       - **Then** an error is thrown indicating a currency mismatch.
   - **Scenario: Reject a quantity outside 1 to 1000**
       - **Given** a draft purchase order,
       - **When** a procurement manager adds an item with a quantity that is not a positive integer of at most 1000,
       - **Then** an error is thrown and the item is not added.
   - **Scenario: Reject the 51st item**
       - **Given** a draft purchase order that already contains 50 items,
       - **When** a procurement manager adds another item,
       - **Then** an error is thrown indicating that a purchase order cannot have more than 50 items.

   ## US005: Calculating the Total Price of a Purchase Order
   As a procurement manager, I want to calculate the total price of a purchase order so that I can review costs before submitting it.

   ### Acceptance Criteria
   - **Scenario: Calculate the total price with multiple items**
       - **Given** a purchase order with two items, one with a unit price of 45.99 and quantity 5, another with a unit price of 22.99 and quantity 10,
       - **When** a procurement manager calculates the total price,
       - **Then** the total is 459.85 as a `Money` in the order's currency.
   - **Scenario: Attempt to calculate the total of an empty order**
       - **Given** a purchase order with no items,
       - **When** a procurement manager calculates the total price,
       - **Then** an error is thrown indicating that an empty purchase order has no total.

   ## US006: Cancelling a Purchase Order
   As a procurement manager, I want to cancel a purchase order so that an order that is no longer needed can be voided.

   ### Acceptance Criteria
   - **Scenario: Cancel an order that is not completed**
       - **Given** a purchase order in any state except "Completed",
       - **When** a procurement manager cancels the order,
       - **Then** the order's state changes to "Canceled".
   - **Scenario: Attempt to cancel a completed order**
       - **Given** a purchase order in the "Completed" state,
       - **When** a procurement manager attempts to cancel the order,
       - **Then** an error is thrown indicating that a completed order cannot be canceled.

   ## US007: Managing the Purchase Order Lifecycle
   As a procurement manager, I want to transition a purchase order through its lifecycle so that I can track the order process.

   ### Acceptance Criteria
   - **Scenario: Walk the order through its full lifecycle**
       - **Given** a purchase order in the "Draft" state,
       - **When** a procurement manager submits, approves, ships, and completes the order in that order,
       - **Then** each transition updates the state, ending at "Completed".
   - **Scenario: Attempt an out-of-sequence transition**
       - **Given** a purchase order in a given state,
       - **When** a procurement manager attempts a transition whose predecessor state is not the current one (for example, approving a "Draft" order),
       - **Then** an error is thrown with a descriptive message and the state is unchanged.
   ```
   </details>

   **Note:** the Requirement Traceability Matrix already names classes that don't exist yet. That's expected: the matrix records the plan, the sections below build exactly what it points at.

9. **Look at the architecture before writing any code.**
   - Real projects rarely start from a blank slate: the course already sets DDD and this bounded-context split as part of the Definition of Done. What's ahead is learning to read a given architecture and implement it well.
   - Install the **plantuml4idea** plugin so the diagram renders: `File` → `Settings` → `Plugins` → `Marketplace` → search `plantuml4idea` → `Install`.
   - Right-click the `docs` folder → `New` → `File` → type `class-diagram.puml` → Enter. WebStorm shows a rendered preview beside the source.

   Three bounded contexts: `scm` (the supplier), `procurement` (the purchase order and its items), and a `shared` kernel (`Money`, `Currency`, `DateTime`, the `uuid` helper, `ValidationError`). Each context owns its identifiers, `procurement` has its own `SupplierId` so it never has to import SCM's. The `PurchaseOrder` aggregate root is the only way in and out of an order: its items and state change only through its methods.

   <details>
   <summary>docs/class-diagram.puml</summary>

   ```
   @startuml
   package "shared.domain.model" as shared {
       class "uuid" <<utility>> {
           + generateUuid(): string
           + validateUuid(value: string): boolean
       }

       class ValidationError extends Error {
       }

       class Currency {
           - #code: string
           + code: string
           + equals(other: Currency): boolean
           + toString(): string
       }

       class Money {
           - #amount: number
           - #currency: Currency
           + amount: number
           + currency: Currency
           + add(other: Money): Money
           + multiply(multiplier: number): Money
           + toString(): string
           + equals(other: Money): boolean
       }

       class DateTime {
           - #date: Date
           + date: Date
           + toISOString(): string
           + toString(): string
           + equals(other: DateTime): boolean
       }
   }

   package "scm.domain.model" as scm {
       class SupplierId {
           - #value: string
           + {static} generate(): SupplierId
           + value: string
           + equals(other: SupplierId): boolean
           + toString(): string
       }

       class Supplier {
           - #id: SupplierId
           - #name: string
           - #contactEmail: string | null
           - #lastOrderTotalPrice: Money | null
           + id: SupplierId
           + name: string
           + contactEmail: string | null
           + lastOrderTotalPrice: Money | null
           + changeName(newName: string): void
           + updateEmail(newEmail: string): void
           + recordOrder(orderTotal: Money): void
       }
   }

   package "procurement.domain.model" as procurement {
       class SupplierId {
           - #value: string
           + value: string
           + equals(other: SupplierId): boolean
           + toString(): string
       }

       class ProductId {
           - #value: string
           + {static} generate(): ProductId
           + value: string
           + equals(other: ProductId): boolean
           + toString(): string
       }

       class PurchaseOrderId {
           - #value: string
           + {static} generate(): PurchaseOrderId
           + value: string
           + equals(other: PurchaseOrderId): boolean
           + toString(): string
       }

       class PurchaseOrderState {
           - #value: string
           + value: string
           + isDraft(): boolean
           + toSubmittedFrom(currentState: PurchaseOrderState): PurchaseOrderState
           + toApprovedFrom(currentState: PurchaseOrderState): PurchaseOrderState
           + toShippedFrom(currentState: PurchaseOrderState): PurchaseOrderState
           + toCompletedFrom(currentState: PurchaseOrderState): PurchaseOrderState
           + toCanceledFrom(currentState: PurchaseOrderState): PurchaseOrderState
           + equals(other: PurchaseOrderState): boolean
       }

       class PurchaseOrderItem {
           - #orderId: PurchaseOrderId
           - #productId: ProductId
           - #quantity: number
           - #unitPrice: Money
           + orderId: PurchaseOrderId
           + productId: ProductId
           + quantity: number
           + unitPrice: Money
           + calculateSubtotal(): Money
           + equals(other: PurchaseOrderItem): boolean
       }

       class PurchaseOrder {
           - #id: PurchaseOrderId
           - #supplierId: SupplierId
           - #currency: Currency
           - #orderDate: DateTime
           - #items: PurchaseOrderItem[]
           - #state: PurchaseOrderState
           + id: PurchaseOrderId
           + supplierId: SupplierId
           + currency: Currency
           + orderDate: DateTime
           + items: ReadonlyArray<PurchaseOrderItem>
           + state: string
           + addItem(productId: ProductId, quantity: number, unitPrice: Money): void
           + calculateTotalPrice(): Money
           + submit(): void
           + approve(): void
           + ship(): void
           + complete(): void
           + cancel(): void
       }
   }

   Supplier "1" *-- "1" scm.SupplierId
   Supplier "1" -- "0..1" Money : lastOrderTotalPrice
   PurchaseOrder "1" *-- "1" PurchaseOrderId
   PurchaseOrder "1" *-- "1" procurement.SupplierId
   PurchaseOrder "1" *-- "1" PurchaseOrderState
   PurchaseOrder "1" *-- "0..*" PurchaseOrderItem
   PurchaseOrder "1" -- "1" Currency
   PurchaseOrder "1" -- "1" DateTime
   PurchaseOrderItem "1" -- "1" ProductId
   PurchaseOrderItem "1" -- "1" Money
   Money "1" -- "1" Currency
   scm.SupplierId ..> shared.uuid
   procurement.ProductId ..> shared.uuid
   procurement.PurchaseOrderId ..> shared.uuid

   note as N1
     There are two SupplierId classes, one per context.
     scm.SupplierId is the supplier's own identity, with a generate() factory.
     procurement.SupplierId is Procurement's own copy of the identifier it
     agrees on with SCM, with no factory: a purchase order is always raised
     against a supplier that already exists. Neither context imports the other's type.
   end note

   note as N2
     PurchaseOrderState values: Draft, Submitted, Approved, Shipped, Completed, Canceled.
     Draft -> Submitted -> Approved -> Shipped -> Completed; any state but Completed can be Canceled.
   end note

   @enduml
   ```
   </details>

   **Note:** there are two `SupplierId` classes on purpose, one per context. `scm.SupplierId` is the supplier's own identity; `procurement.SupplierId` is Procurement's own copy of the identifier it agrees on with SCM. Neither context imports the other's type.

   **Tip:** if it shows an error instead of a diagram, see [Appendix: If the class diagram doesn't render](#if-the-class-diagram-doesnt-render).

   **Note:** there is nothing to commit yet. The repository is set up a few steps from here, and this file is saved together with the rest of your work in step 11.

10. **Set up `.gitignore`.** The wizard generated one at the project root. Open it and replace its contents with the standard Node plus JetBrains template:

    <details>
    <summary>.gitignore</summary>

    ```
    # Dependencies
    node_modules/
    /jspm_packages/

    # Logs
    logs
    *.log
    npm-debug.log*
    yarn-debug.log*
    yarn-error.log*
    lerna-debug.log*
    .pnpm-debug.log*

    # Diagnostic reports (https://nodejs.org/api/report.html)
    report.[0-9]*.[0-9]*.[0-9]*.[0-9]*.json

    # Runtime data
    pids
    *.pid
    *.seed
    *.pid.lock

    # Directory for instrumented libs generated by jscoverage/JSCover
    lib-cov

    # Coverage directory used by tools like istanbul
    coverage
    *.lcov

    # nyc gitignore
    .nyc_output

    # Grunt intermediate storage (https://gruntjs.com/creating-plugins#creating-temporary-files)
    .grunt

    # Bower dependency directory (https://bower.io/)
    bower_components

    # node-waf configuration
    .lock-wscript

    # Compiled binary addons (https://nodejs.org/api/addons.html)
    build/Release

    # Dependency directories
    jspm_packages/

    # TypeScript v1 declaration files
    typings/

    # TypeScript cache
    *.tsbuildinfo

    # Optional npm cache directory
    .npm

    # Optional eslint cache
    .eslintcache

    # Optional stylelint cache
    .stylelintcache

    # Microbundle cache
    .rpt2_cache/
    .rts2_cache_cjs/
    .rts2_cache_es/
    .rts2_cache_umd/

    # Optional REPL history
    .node_repl_history

    # Output of 'npm pack'
    *.tgz

    # Yarn Integrity file
    .yarn-integrity

    # dotenv environment variables files
    .env
    .env.development.local
    .env.test.local
    .env.production.local
    .env.local

    # parcel-bundler cache (https://parceljs.org/)
    .cache
    .parcel-cache

    # Next.js build output
    .next
    out

    # Nuxt.js build / generate output
    .nuxt
    dist

    # Gatsby files
    .cache/
    # Comment in the public line if your project uses Gatsby and you want to ignore the built site
    # public

    # vue-cli build scripts
    dist/

    # Serverless directories
    .serverless/

    # FuseBox cache
    .fusebox/

    # DynamoDB Local files
    .dynamodb/

    # TernJS port file
    .tern-port

    # Stores VS Code state settings
    .vscode/*
    !.vscode/settings.json
    !.vscode/tasks.json
    !.vscode/launch.json
    !.vscode/extensions.json
    !.vscode/*.code-snippets

    # Local History for Visual Studio Code
    .history/

    # Built Visual Studio Code extensions
    *.vsix

    ### JetBrains ###
    # Covers IntelliJ, PyCharm, DataGrip, CLion, AppCode, Android Studio, WebStorm

    *.iws

    # User-specific stuff
    .idea/shelf/
    .idea/workspace.xml
    .idea/usage.statistics.xml
    .idea/dictionary.xml
    .idea/librarySourceMirror.xml

    # Sensitive or high-churn files
    .idea/dataSources/
    .idea/dataSources.ids
    .idea/dataSources.xml
    .idea/dataSources.local.xml
    .idea/sqlDataSources.xml
    .idea/dynamic.xml
    .idea/uiDesigner.xml
    .idea/dbnavigator.xml

    # Gradle
    .idea/gradle.xml
    .idea/libraries

    # Gradle and Maven with auto-import
    # Match the default behavior of IntelliJ IDEA
    .idea/artifacts
    .idea/compiler.xml
    .idea/modules.xml
    .idea/modules
    *.iml
    *.ipr

    # CMake
    cmake-build-*/

    # Mongo Explorer plugin
    .idea/mongoSettings.xml


    # IntelliJ
    out/

    # mpeltonen/sbt-idea plugin
    .idea_modules/

    # JIRA plugin
    atlassian-ide-plugin.xml

    # Cursive Clojure plugin
    .idea/replstate.xml

    # Crashlytics plugin (for Android Studio and IntelliJ)
    com_crashlytics_export_strings.xml
    crashlytics.properties
    crashlytics-build.properties
    fabric.properties

    # JSON modeler plugin
    .idea/jsonModel.xml

    # Zenika Test Recorder plugin
    .idea/recordings.xml

    # vscode-intellij-converter plugin
    .idea/vsc-extension-stats.json

    # If you want to ignore the whole .idea folder (common for simple JS projects):
    .idea/

    ### OS X ###
    .DS_Store
    .AppleDouble
    .LSOverride

    # Icon must end with two \r
    Icon

    # Thumbnails
    ._*

    # Files that might appear in the root of a volume
    .DocumentRevisions-V1
    .fseventsd
    .Spotlight-V100
    .TemporaryItems
    .Trashes
    .VolumeIcon.icns
    .com.apple.timemachine.donotpresent

    # Directories potentially created on remote AFP share
    .AppleDB
    .AppleDesktop
    Network Trash Folder
    Temporary Items
    .apdisk
    ```
    </details>

    **Note:** `.gitignore` has to exist before the first commit. `node_modules/` is large, regenerated by `npm install`, and never belongs in history; the `### JetBrains ###` section ignores `.idea/`, which WebStorm rewrites constantly.

11. **Initialize the local repository.**
    - Open WebStorm's **Terminal** tool window (bottom toolbar). It opens at the project root; confirm the prompt shows the `javascript-review` folder.
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
    - `git config` without `--global` scopes the identity to this repo, so it won't affect anyone else on a shared machine.
    - Ran it from a subfolder by mistake? See [Appendix: Removing a stray .git folder](#removing-a-stray-git-folder).

12. **Connect to GitHub.**

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
    - That account name is your `<org>` in the command below (angle brackets not typed): if the organization is `acme-labs`, the repo ends up at `github.com/acme-labs/javascript-review`.

    **Create the private repo and push.** One command. Replace `<org>` with your organization's name.
    - Check the terminal is at the project root (the folder with `package.json`) first: `--source=.` acts on the current folder.
    - Run:

      ```
      gh repo create <org>/javascript-review --private --source=. --remote=origin --push --description "JavaScript console application illustrating object-oriented and domain-driven design principles in the context of Supply Chain Management and Procurement."
      ```

    - It creates the private repo in your org, adds it as `origin`, pushes `main`, and sets the About text (GitHub's own repo-level summary, separate from `README.md`).

    **Note:** no GitHub CLI? Create the repo on the website and push by hand: see [Appendix: Creating the repo without the GitHub CLI](#creating-the-repo-without-the-github-cli).

13. **Install the Git Flow Helper plugin.**
    - `File` → `Settings` → `Plugins` → `Marketplace` tab.
    - Search `Git Flow Helper`, click `Install`.
    - Restart the IDE if prompted.

14. **Initialize Git Flow.** Git Flow Helper pushes through WebStorm's own GitHub connection, not the terminal's. Register **your** account there first, and make sure it's the only one.
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

## Registering a Supplier ([US001](./user-stories.md))

1. **Start the feature.** Git Flow Helper widget → `Feature` → `Feature Start` → **Feature description** `register-supplier` → `OK`. Creates and switches you to `feature/register-supplier`.

2. **Sketch the `Supplier` entity, fields only.** US001 is about registering a supplier, so start with `Supplier`. Right-click `src` → `New` → `JavaScript File` → type `scm/domain/model/supplier` → Enter. Write the class with its fields, nothing else.

   **Note:** every `Create the ...` step below is created the same way, always from a right-click on `src`. Type the path without the `.js` ending; WebStorm adds it and creates the folders along the way.

   <details>
   <summary>supplier.js (fields only)</summary>

   ```javascript
   export class Supplier {
     static #NAME_MIN_LENGTH = 2;
     static #NAME_MAX_LENGTH = 100;
     #id;
     #name;
     #contactEmail;
     #lastOrderTotalPrice;
   }
   ```
   </details>

   **Note:** four private instance fields, plus two named constants for the name-length rule so `changeName` reads by intent instead of magic numbers. `#lastOrderTotalPrice` is declared but not assigned here; the constructor sets it in step 8. The class does nothing until then. No commit until then, when `supplier.js` first works end to end.

3. **Create the `SupplierId` value object,** the identity a `Supplier` needs. Right-click `src` → `New` → `JavaScript File` → type `scm/domain/model/supplier-id`. Hold a `#value`, validate the UUID it's given, and freeze the instance. Add the `value` getter.

   <details>
   <summary>supplier-id.js (constructor, getter)</summary>

   ```javascript
   export class SupplierId {
     #value;

     constructor(value) {
       if (!validateUuid(value)) {
         throw new ValidationError(`Invalid SupplierId: ${value}. Must be a valid UUID`);
       }
       this.#value = value;
       Object.freeze(this);
     }

     get value() {
       return this.#value;
     }
   }
   ```
   </details>

   **Note:** `validateUuid` and `ValidationError` show red, the files they come from don't exist yet. The next two steps create `uuid.js` and `errors.js`, and WebStorm adds the two `import` lines once they do. `Object.freeze(this)` makes the value object immutable at runtime: once built, its `#value` can never change.

4. **Create the `uuid` utility,** what `SupplierId` imports for its UUID check. Every identifier in the model needs UUID helpers, so they go in one place. Right-click `src` → `New` → `JavaScript File` → type `shared/domain/model/uuid`. `generateUuid()` returns a time-ordered UUID v7, `validateUuid()` checks a string is a well-formed UUID.

   <details>
   <summary>uuid.js</summary>

   ```javascript
   import { v7 as uuidv7, validate as uuidValidate } from 'uuid';

   /**
    * Generates a new time-ordered UUID (version 7).
    * @returns {string} A UUID v7 string.
    */
   export function generateUuid() {
     return uuidv7();
   }

   /**
    * Validates that a string is a well-formed UUID.
    * @param {string} value - The string to validate.
    * @returns {boolean} True if valid, false otherwise.
    */
   export function validateUuid(value) {
     return uuidValidate(value);
   }
   ```
   </details>

5. **Create the `ValidationError` class,** the error every domain rule throws. Right-click `src` → `New` → `JavaScript File` → type `shared/domain/model/errors`. A named subclass of `Error`, so a caller can tell a domain rule violation apart from a runtime bug.

   <details>
   <summary>errors.js</summary>

   ```javascript
   /**
    * Custom error class for domain-specific validation failures.
    */
   export class ValidationError extends Error {
     /**
      * Creates a new ValidationError.
      * @param {string} message - The error message.
      */
     constructor(message) {
       super(message);
       this.name = 'ValidationError';
     }
   }
   ```
   </details>

   **Note:** `SupplierId` now resolves both imports. It still can't be constructed on its own with a real id (nothing generates one yet), which the next step fixes.

6. **Add `SupplierId.generate()`.** A static factory that mints a fresh `SupplierId` from a new UUID v7. Widen the `uuid` import to bring in `generateUuid` alongside `validateUuid`, then add the method.

   <details>
   <summary>supplier-id.js (addition: generate)</summary>

   ```javascript
   import { generateUuid, validateUuid } from '../../../shared/domain/model/uuid.js';

   static generate() {
     return new SupplierId(generateUuid());
   }
   ```
   </details>

7. **Add `SupplierId.equals()` and `toString()`.** A value object is compared by its value, not its reference: `equals()` returns true for another `SupplierId` with the same `#value`. `toString()` returns the raw id, so a `SupplierId` reads naturally inside a template literal. That completes `SupplierId`; the full file below includes its JSDoc.

   <details>
   <summary>supplier-id.js (addition: equals, toString)</summary>

   ```javascript
   equals(other) {
     return other instanceof SupplierId && this.#value === other.value;
   }

   toString() {
     return this.#value;
   }
   ```
   </details>

   <details>
   <summary>supplier-id.js</summary>

   ```javascript
   import { generateUuid, validateUuid } from '../../../shared/domain/model/uuid.js';
   import { ValidationError } from '../../../shared/domain/model/errors.js';

   /**
    * Value object representing a supplier's own identity in the Supply Chain Management context.
    * @remarks
    * Wrapping the identifier, instead of passing a raw string around, means a `SupplierId` can never be
    * confused with a `ProductId` or a `PurchaseOrderId` at the call site, even though all three are
    * UUIDs underneath. A new id is a time-ordered UUID v7.
    */
   export class SupplierId {
     #value;

     /**
      * Creates a new SupplierId.
      * @param {string} value - The UUID value.
      * @throws {ValidationError} If the value is not a valid UUID.
      */
     constructor(value) {
       if (!validateUuid(value)) {
         throw new ValidationError(`Invalid SupplierId: ${value}. Must be a valid UUID`);
       }
       this.#value = value;
       Object.freeze(this);
     }

     /**
      * Generates a new SupplierId with a random UUID v7.
      * @returns {SupplierId} A new SupplierId instance.
      */
     static generate() {
       return new SupplierId(generateUuid());
     }

     /**
      * Gets the UUID value.
      * @returns {string} The UUID.
      */
     get value() {
       return this.#value;
     }

     /**
      * Checks if this SupplierId equals another.
      * @param {SupplierId} other - The other SupplierId to compare.
      * @returns {boolean} True if equal, false otherwise.
      */
     equals(other) {
       return other instanceof SupplierId && this.#value === other.value;
     }

     toString() {
       return this.#value;
     }
   }
   ```
   </details>

8. **Add the `Supplier` constructor.** `SupplierId` exists, so `supplier.js` can be built now, starting from the constructor. It takes `{ id, name, contactEmail, lastOrderTotalPrice }`, checks the `id` is a `SupplierId` and stores it, then delegates the rest: `changeName(name)`, `updateEmail(contactEmail)` when one is given, `recordOrder(lastOrderTotalPrice)` when one is given. Those three methods come in the next steps, so construction and any later change run the same validation.

   <details>
   <summary>supplier.js (addition: constructor)</summary>

   ```javascript
   import { SupplierId } from './supplier-id.js';
   import { ValidationError } from '../../../shared/domain/model/errors.js';

   constructor({ id, name, contactEmail = null, lastOrderTotalPrice = null }) {
     if (!(id instanceof SupplierId)) {
       throw new ValidationError('Supplier ID must be a valid SupplierId object');
     }
     this.#id = id;
     this.changeName(name);
     if (contactEmail !== null) {
       this.updateEmail(contactEmail);
     } else {
       this.#contactEmail = null;
     }
     if (lastOrderTotalPrice !== null) {
       this.recordOrder(lastOrderTotalPrice);
     } else {
       this.#lastOrderTotalPrice = null;
     }
   }
   ```
   </details>

   **Note:** `changeName`, `updateEmail`, and `recordOrder` show red, the next steps add them (`recordOrder` in US002, once `Money` exists). Nothing runs until the demo in step 12, by which point `changeName` and `updateEmail` are in place; US001's demo passes no `lastOrderTotalPrice`, so the `recordOrder` branch never executes here.

9. **Add `Supplier.changeName()`,** the intention-revealing method the constructor delegates to for the name. It rejects a name that is not a string whose length is within the two constants from step 2.

   <details>
   <summary>supplier.js (addition: changeName)</summary>

   ```javascript
   changeName(newName) {
     if (
       typeof newName !== 'string' ||
       newName.length < Supplier.#NAME_MIN_LENGTH ||
       newName.length > Supplier.#NAME_MAX_LENGTH
     ) {
       throw new ValidationError(
         `Supplier name must be between ${Supplier.#NAME_MIN_LENGTH} and ${Supplier.#NAME_MAX_LENGTH} characters`
       );
     }
     this.#name = newName;
   }
   ```
   </details>

   **Note:** the bounds come from `Supplier.#NAME_MIN_LENGTH` / `Supplier.#NAME_MAX_LENGTH`, and the error message interpolates them, so the rule lives in one place. ADR-0004 in `## Document the Project` covers why `Supplier` changes only through methods like this, never a raw `set name`.

10. **Add `Supplier.updateEmail()` and its `#isValidEmail` helper.** `updateEmail()` sets the contact email, rejecting one that is not a well-formed address. `#isValidEmail` is a private method: a regex check no caller needs to see.

    <details>
    <summary>supplier.js (addition: updateEmail, #isValidEmail)</summary>

    ```javascript
    updateEmail(newEmail) {
      if (!this.#isValidEmail(newEmail)) {
        throw new ValidationError(`Invalid contact email: ${newEmail}`);
      }
      this.#contactEmail = newEmail;
    }

    #isValidEmail(email) {
      const emailRegex = /^[^\s@]+@[^\s@]+\.[^\s@]+$/;
      return emailRegex.test(email);
    }
    ```
    </details>

11. **Add the `Supplier` getters** for the four fields: `id`, `name`, `contactEmail`, `lastOrderTotalPrice`. Read access only; the fields change through `changeName`, `updateEmail`, and `recordOrder`.

    <details>
    <summary>supplier.js (addition: getters)</summary>

    ```javascript
    get id() {
      return this.#id;
    }

    get name() {
      return this.#name;
    }

    get contactEmail() {
      return this.#contactEmail;
    }

    get lastOrderTotalPrice() {
      return this.#lastOrderTotalPrice;
    }
    ```
    </details>

    `supplier.js` works end to end now. Check yours against the whole file so far:

    <details>
    <summary>supplier.js (so far)</summary>

    ```javascript
    import { SupplierId } from './supplier-id.js';
    import { ValidationError } from '../../../shared/domain/model/errors.js';

    export class Supplier {
      static #NAME_MIN_LENGTH = 2;
      static #NAME_MAX_LENGTH = 100;
      #id;
      #name;
      #contactEmail;
      #lastOrderTotalPrice;

      constructor({ id, name, contactEmail = null, lastOrderTotalPrice = null }) {
        if (!(id instanceof SupplierId)) {
          throw new ValidationError('Supplier ID must be a valid SupplierId object');
        }
        this.#id = id;
        this.changeName(name);
        if (contactEmail !== null) {
          this.updateEmail(contactEmail);
        } else {
          this.#contactEmail = null;
        }
        if (lastOrderTotalPrice !== null) {
          this.recordOrder(lastOrderTotalPrice);
        } else {
          this.#lastOrderTotalPrice = null;
        }
      }

      changeName(newName) {
        if (
          typeof newName !== 'string' ||
          newName.length < Supplier.#NAME_MIN_LENGTH ||
          newName.length > Supplier.#NAME_MAX_LENGTH
        ) {
          throw new ValidationError(
            `Supplier name must be between ${Supplier.#NAME_MIN_LENGTH} and ${Supplier.#NAME_MAX_LENGTH} characters`
          );
        }
        this.#name = newName;
      }

      updateEmail(newEmail) {
        if (!this.#isValidEmail(newEmail)) {
          throw new ValidationError(`Invalid contact email: ${newEmail}`);
        }
        this.#contactEmail = newEmail;
      }

      #isValidEmail(email) {
        const emailRegex = /^[^\s@]+@[^\s@]+\.[^\s@]+$/;
        return emailRegex.test(email);
      }

      get id() {
        return this.#id;
      }

      get name() {
        return this.#name;
      }

      get contactEmail() {
        return this.#contactEmail;
      }

      get lastOrderTotalPrice() {
        return this.#lastOrderTotalPrice;
      }
    }
    ```
    </details>

    **Note:** `this.recordOrder(...)` in the constructor shows red, US002 adds that method. It only runs when a `lastOrderTotalPrice` is passed, which US001's demo never does. `Supplier` keeps growing in US002; its full file, with JSDoc, is at the end of that section.

    ```
    git add .
    git commit -m "feat(scm): add supplier entity and its id."
    ```

12. **Register a supplier in `src/index.js`.** Create a `Supplier` with a name and a contact email, print its id, name, and email. The order context in US003 needs the supplier's raw id value, so the `Supplier` here uses SCM's own `SupplierId`.

    <details>
    <summary>index.js (so far)</summary>

    ```javascript
    import { Supplier } from './scm/domain/model/supplier.js';
    import { SupplierId as ScmSupplierId } from './scm/domain/model/supplier-id.js';

    // Register a supplier in the SCM context.
    const supplier = new Supplier({
      id: ScmSupplierId.generate(),
      name: 'Acme Corp',
      contactEmail: 'contact@acme.com',
    });
    console.log(`Registered supplier ${supplier.id} - ${supplier.name} <${supplier.contactEmail}>`);
    ```
    </details>

    **Note:** `${supplier.id}` prints the raw UUID because `SupplierId.toString()` returns it. `console.log(supplier)` on its own would print `Supplier {}`, the `#` private fields don't show, which is the encapsulation doing its job. Read the supplier through its getters instead.

    ```
    git add .
    git commit -m "feat(main): register a supplier."
    ```

13. **Publish and finish the feature.** Git Flow Helper widget → `Feature` → `Feature Publish`, then → `Feature Finish` (`Integrate Immediately`, `Keep remote branch when finished` unchecked). Merges into `develop` and pushes it too.

## Recording the Last Order Total for a Supplier ([US002](./user-stories.md))

1. **Start the feature.** Git Flow Helper widget → `Feature` → `Feature Start` → **Feature description** `record-last-order-total` → `OK`. Creates and switches you to `feature/record-last-order-total`.

2. **Create the `Currency` value object,** the first piece `Money` needs. Right-click `src` → `New` → `JavaScript File` → type `shared/domain/model/currency`. Hold a `#code`, validate it against a fixed list of supported codes, and freeze the instance. Add the `code` getter.

   <details>
   <summary>currency.js (constructor, getter)</summary>

   ```javascript
   import { ValidationError } from './errors.js';

   export class Currency {
     static #VALID_CODES = ['USD', 'EUR', 'GBP', 'JPY'];
     #code;

     constructor(code) {
       if (!Currency.#VALID_CODES.includes(code)) {
         throw new ValidationError(
           `Invalid currency code: ${code}. Must be one of ${Currency.#VALID_CODES.join(', ')}`
         );
       }
       this.#code = code;
       Object.freeze(this);
     }

     get code() {
       return this.#code;
     }
   }
   ```
   </details>

   **Note:** `static #VALID_CODES` is a private static field: the whitelist belongs to the class, not to any instance, and nothing outside `Currency` can read or change it.

3. **Add `Currency.equals()`.** True for another `Currency` with the same `#code`.

   <details>
   <summary>currency.js (addition: equals)</summary>

   ```javascript
   equals(other) {
     return other instanceof Currency && this.#code === other.code;
   }
   ```
   </details>

4. **Add `Currency.toString()`.** Returns the raw code. That completes `Currency`; the full file below includes its JSDoc.

   <details>
   <summary>currency.js (addition: toString)</summary>

   ```javascript
   toString() {
     return this.#code;
   }
   ```
   </details>

   <details>
   <summary>currency.js</summary>

   ```javascript
   import { ValidationError } from './errors.js';

   /**
    * Value object representing a currency with a code (e.g., USD, EUR).
    */
   export class Currency {
     /** @private */
     static #VALID_CODES = ['USD', 'EUR', 'GBP', 'JPY'];
     #code;

     /**
      * Creates a new Currency instance.
      * @param {string} code - The currency code (e.g., 'USD').
      * @throws {ValidationError} If the code is not valid.
      */
     constructor(code) {
       if (!Currency.#VALID_CODES.includes(code)) {
         throw new ValidationError(
           `Invalid currency code: ${code}. Must be one of ${Currency.#VALID_CODES.join(', ')}`
         );
       }
       this.#code = code;
       Object.freeze(this);
     }

     /**
      * Gets the currency code.
      * @returns {string} The currency code.
      */
     get code() {
       return this.#code;
     }

     /**
      * Checks if this currency equals another.
      * @param {Currency} other - The other currency to compare.
      * @returns {boolean} True if equal, false otherwise.
      */
     equals(other) {
       return other instanceof Currency && this.#code === other.code;
     }

     toString() {
       return this.#code;
     }
   }
   ```
   </details>

5. **Create the `Money` value object.** Right-click `src` → `New` → `JavaScript File` → type `shared/domain/model/money`. Hold a `#amount` and a `#currency`, reject a negative or non-finite amount and a currency that is not a `Currency`, round the amount to two decimals, and freeze the instance. Add the `amount` and `currency` getters.

   <details>
   <summary>money.js (constructor, getters)</summary>

   ```javascript
   import { ValidationError } from './errors.js';
   import { Currency } from './currency.js';

   export class Money {
     static #FRACTION_DIGITS = 2;
     #amount;
     #currency;

     constructor({ amount, currency }) {
       if (!Number.isFinite(amount) || amount < 0) {
         throw new ValidationError('Amount must be a non-negative number');
       }
       if (!(currency instanceof Currency)) {
         throw new ValidationError('Currency must be a valid Currency object');
       }
       this.#amount = Number(amount.toFixed(Money.#FRACTION_DIGITS));
       this.#currency = currency;
       Object.freeze(this);
     }

     get amount() {
       return this.#amount;
     }

     get currency() {
       return this.#currency;
     }
   }
   ```
   </details>

6. **Add `Money.add()`.** Returns a new `Money` for the sum, and throws if the two currencies differ. `Money` is immutable: `add` never changes `this`.

   <details>
   <summary>money.js (addition: add)</summary>

   ```javascript
   add(other) {
     if (!(other instanceof Money) || !this.#currency.equals(other.currency)) {
       throw new ValidationError('Cannot add Money with different currencies');
     }
     return new Money({
       amount: this.#amount + other.amount,
       currency: this.#currency,
     });
   }
   ```
   </details>

7. **Add `Money.multiply()`.** Returns a new `Money`, scaled by a non-negative factor.

   <details>
   <summary>money.js (addition: multiply)</summary>

   ```javascript
   multiply(multiplier) {
     if (!Number.isFinite(multiplier) || multiplier < 0) {
       throw new ValidationError('Multiplier must be a non-negative number');
     }
     return new Money({
       amount: this.#amount * multiplier,
       currency: this.#currency,
     });
   }
   ```
   </details>

8. **Add `Money.toString()`.** Prints the ISO code and the amount to two decimals, like `USD 100.00`.

   <details>
   <summary>money.js (addition: toString)</summary>

   ```javascript
   toString() {
     return `${this.#currency.code} ${this.#amount.toFixed(Money.#FRACTION_DIGITS)}`;
   }
   ```
   </details>

9. **Add `Money.equals()`.** True for another `Money` with the same amount and currency. That completes `Money`; the full file below includes its JSDoc.

   <details>
   <summary>money.js (addition: equals)</summary>

   ```javascript
   equals(other) {
     return (
       other instanceof Money &&
       this.#amount === other.amount &&
       this.#currency.equals(other.currency)
     );
   }
   ```
   </details>

   <details>
   <summary>money.js</summary>

   ```javascript
   import { ValidationError } from './errors.js';
   import { Currency } from './currency.js';

   /**
    * Value object representing an amount of money with a currency.
    */
   export class Money {
     /** @private */
     static #FRACTION_DIGITS = 2;
     #amount;
     #currency;
     /**
      * Creates a new Money instance.
      * @param {Object} params - The parameters.
      * @param {number} params.amount - The monetary amount.
      * @param {Currency} params.currency - The currency.
      * @throws {ValidationError} If amount is invalid or currency is not a Currency object.
      */
     constructor({ amount, currency }) {
       if (!Number.isFinite(amount) || amount < 0) {
         throw new ValidationError('Amount must be a non-negative number');
       }
       if (!(currency instanceof Currency)) {
         throw new ValidationError('Currency must be a valid Currency object');
       }
       this.#amount = Number(amount.toFixed(Money.#FRACTION_DIGITS));
       this.#currency = currency;
       Object.freeze(this);
     }

     /**
      * Gets the amount.
      * @returns {number} The amount.
      */
     get amount() {
       return this.#amount;
     }

     /**
      * Gets the currency.
      * @returns {Currency} The currency.
      */
     get currency() {
       return this.#currency;
     }

     /**
      * Adds another Money instance to this one.
      * @param {Money} other - The other Money to add.
      * @returns {Money} A new Money instance with the summed amount.
      * @throws {ValidationError} If currencies do not match.
      */
     add(other) {
       if (!(other instanceof Money) || !this.#currency.equals(other.currency)) {
         throw new ValidationError('Cannot add Money with different currencies');
       }
       return new Money({
         amount: this.#amount + other.amount,
         currency: this.#currency,
       });
     }

     /**
      * Multiplies this Money by a factor.
      * @param {number} multiplier - The multiplier.
      * @returns {Money} A new Money instance with the multiplied amount.
      * @throws {ValidationError} If multiplier is invalid.
      */
     multiply(multiplier) {
       if (!Number.isFinite(multiplier) || multiplier < 0) {
         throw new ValidationError('Multiplier must be a non-negative number');
       }
       return new Money({
         amount: this.#amount * multiplier,
         currency: this.#currency,
       });
     }

     /**
      * Returns a human-friendly string representation.
      * @returns {string} The formatted money (e.g., '$100.50').
      */
     toString() {
       return `${this.#currency.code} ${this.#amount.toFixed(Money.#FRACTION_DIGITS)}`;
     }

     /**
      * Checks if this Money equals another.
      * @param {Money} other - The other Money to compare.
      * @returns {boolean} True if equal, false otherwise.
      */
     equals(other) {
       return (
         other instanceof Money &&
         this.#amount === other.amount &&
         this.#currency.equals(other.currency)
       );
     }
   }
   ```
   </details>

10. **Add `Supplier.recordOrder()`.** Open `scm/domain/model/supplier.js`, import `Money`, and add the method the constructor's `lastOrderTotalPrice` branch has been waiting for. It rejects a total that is not a `Money` and sets `#lastOrderTotalPrice`. That completes `Supplier`; the full file below includes its JSDoc.

    <details>
    <summary>supplier.js (addition: recordOrder)</summary>

    ```javascript
    import { Money } from '../../../shared/domain/model/money.js';

    recordOrder(orderTotal) {
      if (!(orderTotal instanceof Money)) {
        throw new ValidationError('Order total must be a valid Money object');
      }
      this.#lastOrderTotalPrice = orderTotal;
    }
    ```
    </details>

    <details>
    <summary>supplier.js</summary>

    ```javascript
    import { SupplierId } from './supplier-id.js';
    import { ValidationError } from '../../../shared/domain/model/errors.js';
    import { Money } from '../../../shared/domain/model/money.js';

    /**
     * @class Supplier
     * Entity representing a supplier in the supply chain management context.
     * @property {SupplierId} id - The unique identifier for the supplier.
     * @property {string} name - The name of the supplier.
     * @property {string|null} contactEmail - The contact email of the supplier (optional).
     * @property {Money|null} lastOrderTotalPrice - The total price of the last order from this supplier (optional).
     */
    export class Supplier {
      /** @private */
      static #NAME_MIN_LENGTH = 2;
      /** @private */
      static #NAME_MAX_LENGTH = 100;
      #id;
      #name;
      #contactEmail;
      #lastOrderTotalPrice;
      /**
       * Creates a new Supplier.
       * @constructor
       * @param {Object} params - The parameters.
       * @param {SupplierId} params.id - The supplier ID.
       * @param {string} params.name - The supplier name.
       * @param {string|null} [params.contactEmail] - The contact email (optional).
       * @param {Money|null} [params.lastOrderTotalPrice] - The last order total price (optional).
       * @throws {ValidationError} If parameters are invalid.
       */
      constructor({ id, name, contactEmail = null, lastOrderTotalPrice = null }) {
        if (!(id instanceof SupplierId)) {
          throw new ValidationError('Supplier ID must be a valid SupplierId object');
        }
        this.#id = id;
        this.changeName(name);
        if (contactEmail !== null) {
          this.updateEmail(contactEmail);
        } else {
          this.#contactEmail = null;
        }
        if (lastOrderTotalPrice !== null) {
          this.recordOrder(lastOrderTotalPrice);
        } else {
          this.#lastOrderTotalPrice = null;
        }
      }

      /**
       * Changes the supplier name.
       * @param {string} newName - The new name.
       * @throws {ValidationError} If name is invalid.
       */
      changeName(newName) {
        if (
          typeof newName !== 'string' ||
          newName.length < Supplier.#NAME_MIN_LENGTH ||
          newName.length > Supplier.#NAME_MAX_LENGTH
        ) {
          throw new ValidationError(
            `Supplier name must be between ${Supplier.#NAME_MIN_LENGTH} and ${Supplier.#NAME_MAX_LENGTH} characters`
          );
        }
        this.#name = newName;
      }

      /**
       * Updates the contact email.
       * @param {string} newEmail - The new email address.
       * @throws {ValidationError} If email is invalid.
       */
      updateEmail(newEmail) {
        if (!this.#isValidEmail(newEmail)) {
          throw new ValidationError(`Invalid contact email: ${newEmail}`);
        }
        this.#contactEmail = newEmail;
      }

      /**
       * Records a new order for this supplier.
       * @param {Money} orderTotal - The total price of the order.
       * @throws {ValidationError} If orderTotal is not a Money object.
       */
      recordOrder(orderTotal) {
        if (!(orderTotal instanceof Money)) {
          throw new ValidationError('Order total must be a valid Money object');
        }
        this.#lastOrderTotalPrice = orderTotal;
      }

      /**
       * @private
       * Validates an email address.
       *
       * @param {string} email - The email to validate.
       * @returns {boolean} True if valid, false otherwise.
       */
      #isValidEmail(email) {
        const emailRegex = /^[^\s@]+@[^\s@]+\.[^\s@]+$/;
        return emailRegex.test(email);
      }

      /**
       * @public
       * Gets the supplier ID.
       *
       * @returns {SupplierId} The supplier ID.
       */
      get id() {
        return this.#id;
      }

      /**
       * Gets the supplier name.
       * @public
       * @returns {string} The name.
       */
      get name() {
        return this.#name;
      }

      /**
       * @public
       * Gets the contact email.
       * @returns {string|null} The contact email or null.
       */
      get contactEmail() {
        return this.#contactEmail;
      }

      /**
       * @public
       * Gets the last order total price.
       * @returns {Money|null} The last order total price or null.
       */
      get lastOrderTotalPrice() {
        return this.#lastOrderTotalPrice;
      }
    }
    ```
    </details>

    **Note:** ADR-0004 in `## Document the Project` covers why `Supplier` changes only through `changeName` / `updateEmail` / `recordOrder`, never a raw setter.

    **Note:** in the full file, `recordOrder` sits with `changeName` and `updateEmail`, before `#isValidEmail` and the getters, not at the end where you added it. Match the layout above; it keeps the three intention-revealing methods together.

    ```
    git add .
    git commit -m "feat(scm): add record order method to supplier."
    ```

11. **Nothing to wire in `src/index.js` yet.** Recording a total needs a real purchase order total, and `PurchaseOrder` arrives in US003. The `src/index.js` call lands in US005, right after the total is calculated.

12. **Publish and finish the feature.** Git Flow Helper widget → `Feature` → `Feature Publish`, then → `Feature Finish` (`Integrate Immediately`, `Keep remote branch when finished` unchecked). Merges into `develop` and pushes it too.

## Creating a Purchase Order ([US003](./user-stories.md))

1. **Start the feature.** Git Flow Helper widget → `Feature` → `Feature Start` → **Feature description** `create-purchase-order` → `OK`. Creates and switches you to `feature/create-purchase-order`.

2. **Sketch the `PurchaseOrder` aggregate, fields only.** Re-read US003's acceptance criteria: a supplier reference and a currency in; an order with its own id, an order date, no items, and a `Draft` state out. Right-click `src` → `New` → `JavaScript File` → type `procurement/domain/model/purchase-order` → Enter. Write the class with its fields, nothing else.

   <details>
   <summary>purchase-order.js (fields only)</summary>

   ```javascript
   export class PurchaseOrder {
     static #MAX_ITEMS = 50;
     #id;
     #supplierId;
     #currency;
     #orderDate;
     #items;
     #state;
   }
   ```
   </details>

   **Note:** `#MAX_ITEMS` is the cap US004 enforces, a named constant so the number lives in one place. `#items` is just an array, it needs no type. The class does nothing until the constructor lands in step 8. No commit until then.

3. **Create Procurement's own `SupplierId` value object,** the reference a `PurchaseOrder` holds. Right-click `src` → `New` → `JavaScript File` → type `procurement/domain/model/supplier-id`. The Procurement context needs to name the supplier an order belongs to, but it must not depend on SCM's `SupplierId` type. This is Procurement's own copy, carrying just the UUID string both contexts agree on. It has no `generate()` factory: a purchase order is always raised against a supplier that already exists, so the id is always supplied.

   <details>
   <summary>supplier-id.js (procurement)</summary>

   ```javascript
   import { validateUuid } from '../../../shared/domain/model/uuid.js';
   import { ValidationError } from '../../../shared/domain/model/errors.js';

   export class SupplierId {
     #value;

     constructor(value) {
       if (!validateUuid(value)) {
         throw new ValidationError(`Invalid SupplierId: ${value}. Must be a valid UUID`);
       }
       this.#value = value;
       Object.freeze(this);
     }

     get value() {
       return this.#value;
     }

     equals(other) {
       return other instanceof SupplierId && this.#value === other.value;
     }

     toString() {
       return this.#value;
     }
   }
   ```
   </details>

   **Note:** this is ADR-0003 in `## Document the Project`: each bounded context owns its identifiers. This file lives at `procurement/domain/model/supplier-id.js`, a different folder from the SCM one in US001. The full file, with JSDoc, is below.

   <details>
   <summary>supplier-id.js (procurement, full file)</summary>

   ```javascript
   import { validateUuid } from '../../../shared/domain/model/uuid.js';
   import { ValidationError } from '../../../shared/domain/model/errors.js';

   /**
    * Value object representing a reference to a supplier inside the Procurement context.
    * @remarks
    * Procurement needs to name the supplier a purchase order belongs to, but it must not depend on the
    * SCM context's own `SupplierId` type: each context owns its identifiers. This is Procurement's own
    * copy, carrying just the UUID string the two contexts agree on. It has no `generate()` factory, a
    * purchase order is always raised against a supplier that already exists, so the id is always supplied.
    */
   export class SupplierId {
     #value;

     /**
      * Creates a new SupplierId.
      * @param {string} value - The supplier's UUID, as agreed with the SCM context.
      * @throws {ValidationError} If the value is not a valid UUID.
      */
     constructor(value) {
       if (!validateUuid(value)) {
         throw new ValidationError(`Invalid SupplierId: ${value}. Must be a valid UUID`);
       }
       this.#value = value;
       Object.freeze(this);
     }

     /**
      * Gets the UUID value.
      * @returns {string} The UUID.
      */
     get value() {
       return this.#value;
     }

     /**
      * Checks if this SupplierId equals another.
      * @param {SupplierId} other - The other SupplierId to compare.
      * @returns {boolean} True if equal, false otherwise.
      */
     equals(other) {
       return other instanceof SupplierId && this.#value === other.value;
     }

     toString() {
       return this.#value;
     }
   }
   ```
   </details>

4. **Create the `PurchaseOrderId` value object,** the order's own identity. Right-click `src` → `New` → `JavaScript File` → type `procurement/domain/model/purchase-order-id`. Same shape as SCM's `SupplierId`: a validated UUID, a `static generate()`, the `value` getter, `equals()`, and `toString()`. That completes `PurchaseOrderId`; the full file below includes its JSDoc.

   <details>
   <summary>purchase-order-id.js</summary>

   ```javascript
   import { generateUuid, validateUuid } from '../../../shared/domain/model/uuid.js';
   import { ValidationError } from '../../../shared/domain/model/errors.js';

   /**
    * Value object representing a unique purchase order identifier in the Procurement context.
    * @remarks
    * Wrapping the identifier, instead of passing a raw string around, means a `PurchaseOrderId` can
    * never be confused with a `SupplierId` or a `ProductId` at the call site, even though all three are
    * UUIDs underneath. A new id is a time-ordered UUID v7.
    */
   export class PurchaseOrderId {
     #value;

     /**
      * Creates a new PurchaseOrderId.
      * @param {string} value - The UUID value.
      * @throws {ValidationError} If the value is not a valid UUID.
      */
     constructor(value) {
       if (!validateUuid(value)) {
         throw new ValidationError(`Invalid PurchaseOrderId: ${value}. Must be a valid UUID`);
       }
       this.#value = value;
       Object.freeze(this);
     }

     /**
      * Generates a new PurchaseOrderId with a random UUID v7.
      * @returns {PurchaseOrderId} A new PurchaseOrderId instance.
      */
     static generate() {
       return new PurchaseOrderId(generateUuid());
     }

     /**
      * Gets the UUID value.
      * @returns {string} The UUID.
      */
     get value() {
       return this.#value;
     }

     /**
      * Checks if this PurchaseOrderId equals another.
      * @param {PurchaseOrderId} other - The other PurchaseOrderId to compare.
      * @returns {boolean} True if equal, false otherwise.
      */
     equals(other) {
       return other instanceof PurchaseOrderId && this.#value === other.value;
     }

     toString() {
       return this.#value;
     }
   }
   ```
   </details>

5. **Create the `DateTime` value object,** for the order date. Right-click `src` → `New` → `JavaScript File` → type `shared/domain/model/date-time`. Wraps a `#date`. The constructor defaults to now, parses a `Date` or a string, throws if the result is invalid, and stores a **clone** so the value object stays immutable. Add the `date` getter, which also returns a copy.

   <details>
   <summary>date-time.js (constructor, getter)</summary>

   ```javascript
   import { ValidationError } from './errors.js';

   export class DateTime {
     #date;

     constructor(date = new Date()) {
       const parsedDate = date instanceof Date ? date : new Date(date);
       if (isNaN(parsedDate.getTime())) {
         throw new ValidationError(`Invalid date: ${date}`);
       }
       this.#date = new Date(parsedDate.getTime()); // Clone to keep the value object immutable
       Object.freeze(this);
     }

     get date() {
       return new Date(this.#date.getTime());
     }
   }
   ```
   </details>

   **Note:** a `Date` is mutable. If the constructor stored the argument directly, a caller holding that same `Date` could still call `setFullYear` on it and change the `DateTime` from the outside. Cloning on the way in and on the way out closes both doors.

6. **Add `DateTime.toISOString()`.** Returns the ISO 8601 string.

   <details>
   <summary>date-time.js (addition: toISOString)</summary>

   ```javascript
   toISOString() {
     return this.#date.toISOString();
   }
   ```
   </details>

7. **Add `DateTime.toString()`.** Returns a human-friendly `en-US` string, like `April 10, 2025 at 05:00 AM`.

   <details>
   <summary>date-time.js (addition: toString)</summary>

   ```javascript
   toString() {
     let options = {
       year: 'numeric',
       month: 'long',
       day: 'numeric',
       hour: '2-digit',
       minute: '2-digit',
       hour12: true,
     };
     return this.#date.toLocaleString('en-US', options);
   }
   ```
   </details>

8. **Add `DateTime.equals()`.** True for another `DateTime` at the same instant. That completes `DateTime`; the full file below includes its JSDoc.

   <details>
   <summary>date-time.js (addition: equals)</summary>

   ```javascript
   equals(other) {
     return other instanceof DateTime && this.#date.getTime() === other.date.getTime();
   }
   ```
   </details>

   <details>
   <summary>date-time.js</summary>

   ```javascript
   import { ValidationError } from './errors.js';

   /**
    * Value object representing a date and time with consistent handling.
    */
   export class DateTime {
     #date;

     /**
      * Creates a new DateTime instance.
      * @param {Date|string} [date=new Date()] - The date (defaults to now).
      * @throws {ValidationError} If the date is invalid.
      */
     constructor(date = new Date()) {
       const parsedDate = date instanceof Date ? date : new Date(date);
       if (isNaN(parsedDate.getTime())) {
         throw new ValidationError(`Invalid date: ${date}`);
       }
       this.#date = new Date(parsedDate.getTime()); // Clone to keep the value object immutable
       Object.freeze(this);
     }

     /**
      * Gets the underlying Date object.
      * @returns {Date} A defensive copy of the date.
      */
     get date() {
       return new Date(this.#date.getTime());
     }

     /**
      * Returns the date in ISO string format.
      * @returns {string} The ISO string (e.g., '2023-10-25T14:30:00.000Z').
      */
     toISOString() {
       return this.#date.toISOString();
     }

     /**
      * Returns a human-friendly string representation.
      * @returns {string} The formatted date (e.g., 'October 25, 2023, 2:30 PM').
      */
     toString() {
       let options = {
         year: 'numeric',
         month: 'long',
         day: 'numeric',
         hour: '2-digit',
         minute: '2-digit',
         hour12: true,
       };
       return this.#date.toLocaleString('en-US', options);
     }

     /**
      * Checks if this DateTime equals another.
      * @param {DateTime} other - The other DateTime to compare.
      * @returns {boolean} True if equal, false otherwise.
      */
     equals(other) {
       return other instanceof DateTime && this.#date.getTime() === other.date.getTime();
     }
   }
   ```
   </details>

9. **Create the `PurchaseOrderState` value object,** the last piece `PurchaseOrder`'s constructor needs. Right-click `src` → `New` → `JavaScript File` → type `procurement/domain/model/purchase-order-state`. Hold a `#value`, default it to `Draft`, and validate it against a private static map of the six states. Add the private `#validateState` check and the `value` getter, and freeze the instance.

   <details>
   <summary>purchase-order-state.js (constructor, #validateState, getter)</summary>

   ```javascript
   import { ValidationError } from '../../../shared/domain/model/errors.js';

   export class PurchaseOrderState {
     static #VALID_STATES = {
       DRAFT: 'Draft',
       SUBMITTED: 'Submitted',
       APPROVED: 'Approved',
       SHIPPED: 'Shipped',
       COMPLETED: 'Completed',
       CANCELED: 'Canceled',
     };
     #value;

     constructor(value = PurchaseOrderState.#VALID_STATES.DRAFT) {
       this.#validateState(value);
       this.#value = value;
       Object.freeze(this);
     }

     #validateState(state) {
       if (!Object.values(PurchaseOrderState.#VALID_STATES).includes(state)) {
         throw new ValidationError(
           `Invalid purchase order state: ${state}. Must be one of ${Object.values(PurchaseOrderState.#VALID_STATES).join(', ')}`
         );
       }
     }

     get value() {
       return this.#value;
     }
   }
   ```
   </details>

   **Note:** the state is a value object, not a bare string, so the legal transitions live with it. The transition methods (`toSubmittedFrom` and friends) arrive in US006 and US007, when there is a reason to move the state.

10. **Add `PurchaseOrderState.isDraft()`.** True when the state is `Draft`. `PurchaseOrder.addItem()` in US004 asks this instead of comparing strings.

    <details>
    <summary>purchase-order-state.js (addition: isDraft)</summary>

    ```javascript
    isDraft() {
      return this.#value === PurchaseOrderState.#VALID_STATES.DRAFT;
    }
    ```
    </details>

11. **Add `PurchaseOrderState.equals()`.** True for another `PurchaseOrderState` with the same `#value`.

    <details>
    <summary>purchase-order-state.js (addition: equals)</summary>

    ```javascript
    equals(other) {
      return other instanceof PurchaseOrderState && this.#value === other.value;
    }
    ```
    </details>

    Check yours against the whole file so far:

    <details>
    <summary>purchase-order-state.js (so far)</summary>

    ```javascript
    import { ValidationError } from '../../../shared/domain/model/errors.js';

    export class PurchaseOrderState {
      static #VALID_STATES = {
        DRAFT: 'Draft',
        SUBMITTED: 'Submitted',
        APPROVED: 'Approved',
        SHIPPED: 'Shipped',
        COMPLETED: 'Completed',
        CANCELED: 'Canceled',
      };
      #value;

      constructor(value = PurchaseOrderState.#VALID_STATES.DRAFT) {
        this.#validateState(value);
        this.#value = value;
        Object.freeze(this);
      }

      #validateState(state) {
        if (!Object.values(PurchaseOrderState.#VALID_STATES).includes(state)) {
          throw new ValidationError(
            `Invalid purchase order state: ${state}. Must be one of ${Object.values(PurchaseOrderState.#VALID_STATES).join(', ')}`
          );
        }
      }

      get value() {
        return this.#value;
      }

      isDraft() {
        return this.#value === PurchaseOrderState.#VALID_STATES.DRAFT;
      }

      equals(other) {
        return other instanceof PurchaseOrderState && this.#value === other.value;
      }
    }
    ```
    </details>

    **Note:** `PurchaseOrderState` keeps growing in US006 and US007. The transition methods land between `#validateState` and `isDraft` in the final file, so its full file, with JSDoc, is at the end of US007.

12. **Add the `PurchaseOrder` constructor.** Every field's type now exists, so `purchase-order.js` works. The constructor takes `{ supplierId, currency, orderDate }`, rejects a `supplierId` that is not Procurement's `SupplierId` and a `currency` that is not a `Currency`, mints a fresh `PurchaseOrderId`, wraps `orderDate` in a `DateTime` (defaulting to now), starts with an empty `#items`, and a fresh `PurchaseOrderState` (which is `Draft`).

    <details>
    <summary>purchase-order.js (addition: constructor)</summary>

    ```javascript
    import { SupplierId } from './supplier-id.js';
    import { PurchaseOrderId } from './purchase-order-id.js';
    import { DateTime } from '../../../shared/domain/model/date-time.js';
    import { ValidationError } from '../../../shared/domain/model/errors.js';
    import { PurchaseOrderState } from './purchase-order-state.js';
    import { Currency } from '../../../shared/domain/model/currency.js';

    constructor({ supplierId, currency, orderDate }) {
      if (!(supplierId instanceof SupplierId)) {
        throw new ValidationError('SupplierId must be a valid SupplierId object');
      }
      if (!(currency instanceof Currency)) {
        throw new ValidationError('Currency must be a valid Currency object');
      }
      this.#id = PurchaseOrderId.generate();
      this.#supplierId = supplierId;
      this.#currency = currency;
      this.#orderDate = orderDate instanceof DateTime ? orderDate : new DateTime();
      this.#items = [];
      this.#state = new PurchaseOrderState(); // Initial state: Draft
    }
    ```
    </details>

    **Note:** `SupplierId` here is `import { SupplierId } from './supplier-id.js'`, the Procurement copy from step 3, not SCM's.

13. **Add the `PurchaseOrder` getters** for the fields that exist so far: `id`, `supplierId`, `currency`, `orderDate`, `items`, and `state`. `items` returns a **frozen copy** (`Object.freeze([...this.#items])`), not the internal array, so a caller that does `order.items.push(...)` changes nothing. `state` returns the plain string `#state.value`, not the `PurchaseOrderState` object.

    <details>
    <summary>purchase-order.js (addition: getters)</summary>

    ```javascript
    get id() {
      return this.#id;
    }

    get supplierId() {
      return this.#supplierId;
    }

    get currency() {
      return this.#currency;
    }

    get orderDate() {
      return this.#orderDate;
    }

    get items() {
      return Object.freeze([...this.#items]);
    }

    get state() {
      return this.#state.value;
    }
    ```
    </details>

    **Note:** this is the first point `purchase-order.js` works. ADR-0005 covers why `items` returns a copy.

    Check yours against the whole file so far:

    <details>
    <summary>purchase-order.js (so far)</summary>

    ```javascript
    import { SupplierId } from './supplier-id.js';
    import { PurchaseOrderId } from './purchase-order-id.js';
    import { DateTime } from '../../../shared/domain/model/date-time.js';
    import { ValidationError } from '../../../shared/domain/model/errors.js';
    import { PurchaseOrderState } from './purchase-order-state.js';
    import { Currency } from '../../../shared/domain/model/currency.js';

    export class PurchaseOrder {
      static #MAX_ITEMS = 50;
      #id;
      #supplierId;
      #currency;
      #orderDate;
      #items;
      #state;

      constructor({ supplierId, currency, orderDate }) {
        if (!(supplierId instanceof SupplierId)) {
          throw new ValidationError('SupplierId must be a valid SupplierId object');
        }
        if (!(currency instanceof Currency)) {
          throw new ValidationError('Currency must be a valid Currency object');
        }
        this.#id = PurchaseOrderId.generate();
        this.#supplierId = supplierId;
        this.#currency = currency;
        this.#orderDate = orderDate instanceof DateTime ? orderDate : new DateTime();
        this.#items = [];
        this.#state = new PurchaseOrderState(); // Initial state: Draft
      }

      get id() {
        return this.#id;
      }

      get supplierId() {
        return this.#supplierId;
      }

      get currency() {
        return this.#currency;
      }

      get orderDate() {
        return this.#orderDate;
      }

      get items() {
        return Object.freeze([...this.#items]);
      }

      get state() {
        return this.#state.value;
      }
    }
    ```
    </details>

    **Note:** `PurchaseOrder` keeps growing through US004-US007 (`addItem`, `calculateTotalPrice`, and the lifecycle methods), each landing before the getters in the final file. Its full file, with JSDoc, is at the end of US007.

    ```
    git add .
    git commit -m "feat(procurement): add purchase order aggregate and its value objects."
    ```

14. **Create a purchase order in `src/index.js`.** The `supplier` is already there from US001. Add a `Currency`, a `DateTime`, and a `PurchaseOrder`; the order is in the Procurement context, so wrap the supplier's raw id in Procurement's own `SupplierId`. Print the order's id, supplier, order date, and state through its getters.

    <details>
    <summary>index.js (addition)</summary>

    ```javascript
    import { SupplierId as ProcurementSupplierId } from './procurement/domain/model/supplier-id.js';
    import { PurchaseOrder } from './procurement/domain/model/purchase-order.js';
    import { Currency } from './shared/domain/model/currency.js';
    import { DateTime } from './shared/domain/model/date-time.js';

    // Raise a purchase order in the Procurement context. The order references the supplier
    // through Procurement's own SupplierId, never SCM's type.
    const usd = new Currency('USD');
    const order = new PurchaseOrder({
      supplierId: new ProcurementSupplierId(supplier.id.value),
      currency: usd,
      orderDate: new DateTime(new Date('2025-04-10T10:00:00Z')),
    });
    console.log(
      `Purchase order ${order.id} - Supplier: ${supplier.name} (${supplier.id.value}), ` +
        `Ordered at: ${order.orderDate.toString()}, State: ${order.state}`
    );
    ```
    </details>

    ```
    git add .
    git commit -m "feat(main): create a purchase order."
    ```

15. **Publish and finish the feature.** Git Flow Helper widget → `Feature` → `Feature Publish`, then → `Feature Finish` (`Integrate Immediately`, `Keep remote branch when finished` unchecked). Merges into `develop` and pushes it too.

## Adding Items to a Purchase Order ([US004](./user-stories.md))

1. **Start the feature.** Git Flow Helper widget → `Feature` → `Feature Start` → **Feature description** `add-item-to-purchase-order` → `OK`. Creates and switches you to `feature/add-item-to-purchase-order`.

2. **Sketch the `PurchaseOrderItem` value object, fields only.** Re-read US004's acceptance criteria: an item carries a product, a quantity, and a unit price, and belongs to one order. Right-click `src` → `New` → `JavaScript File` → type `procurement/domain/model/purchase-order-item` → Enter. Write the class with its fields, nothing else.

   <details>
   <summary>purchase-order-item.js (fields only)</summary>

   ```javascript
   export class PurchaseOrderItem {
     static #MIN_QUANTITY = 1;
     static #MAX_QUANTITY = 1000;
     #orderId;
     #productId;
     #quantity;
     #unitPrice;
   }
   ```
   </details>

   **Note:** `PurchaseOrderId` and `Money` exist from US003 and US002. `ProductId` does not yet; the next step creates it. `#MIN_QUANTITY` / `#MAX_QUANTITY` are named constants so the quantity rule reads by intent instead of magic numbers. No commit until step 7, when `purchase-order-item.js` works.

3. **Create the `ProductId` value object,** the one type on `PurchaseOrderItem` that doesn't exist yet. Right-click `src` → `New` → `JavaScript File` → type `procurement/domain/model/product-id`. Same shape as `PurchaseOrderId`: a validated UUID, a `static generate()`, the `value` getter, `equals()`, and `toString()`. That completes `ProductId`; the full file below includes its JSDoc.

   <details>
   <summary>product-id.js</summary>

   ```javascript
   import { generateUuid, validateUuid } from '../../../shared/domain/model/uuid.js';
   import { ValidationError } from '../../../shared/domain/model/errors.js';

   /**
    * Value object representing a unique product identifier in the Procurement context.
    * @remarks
    * Wrapping the identifier, instead of passing a raw string around, means a `ProductId` can never be
    * confused with a `SupplierId` or a `PurchaseOrderId` at the call site, even though all three are
    * UUIDs underneath. A new id is a time-ordered UUID v7.
    */
   export class ProductId {
     #value;

     /**
      * Creates a new ProductId.
      * @param {string} value - The UUID value.
      * @throws {ValidationError} If the value is not a valid UUID.
      */
     constructor(value) {
       if (!validateUuid(value)) {
         throw new ValidationError(`Invalid ProductId: ${value}. Must be a valid UUID`);
       }
       this.#value = value;
       Object.freeze(this);
     }

     /**
      * Generates a new ProductId with a random UUID v7.
      * @returns {ProductId} A new ProductId instance.
      */
     static generate() {
       return new ProductId(generateUuid());
     }

     /**
      * Gets the UUID value.
      * @returns {string} The UUID.
      */
     get value() {
       return this.#value;
     }

     /**
      * Checks if this ProductId equals another.
      * @param {ProductId} other - The other ProductId to compare.
      * @returns {boolean} True if equal, false otherwise.
      */
     equals(other) {
       return other instanceof ProductId && this.#value === other.value;
     }

     toString() {
       return this.#value;
     }
   }
   ```
   </details>

4. **Add the `PurchaseOrderItem` constructor.** Every field's type exists now, so `purchase-order-item.js` works. The constructor takes `{ orderId, productId, quantity, unitPrice }`, rejects an `orderId` that is not a `PurchaseOrderId`, a `productId` that is not a `ProductId`, a `quantity` that is not a positive integer of at most 1000, and a `unitPrice` that is not a `Money`. Then it freezes the instance.

   <details>
   <summary>purchase-order-item.js (addition: constructor)</summary>

   ```javascript
   import { ProductId } from './product-id.js';
   import { PurchaseOrderId } from './purchase-order-id.js';
   import { Money } from '../../../shared/domain/model/money.js';
   import { ValidationError } from '../../../shared/domain/model/errors.js';

   constructor({ orderId, productId, quantity, unitPrice }) {
     if (!(orderId instanceof PurchaseOrderId)) {
       throw new ValidationError('Order ID must be a valid PurchaseOrderId object');
     }
     if (!(productId instanceof ProductId)) {
       throw new ValidationError('Product ID must be a valid ProductId object');
     }
     if (
       !Number.isInteger(quantity) ||
       quantity < PurchaseOrderItem.#MIN_QUANTITY ||
       quantity > PurchaseOrderItem.#MAX_QUANTITY
     ) {
       throw new ValidationError(
         `Quantity must be an integer between ${PurchaseOrderItem.#MIN_QUANTITY} and ${PurchaseOrderItem.#MAX_QUANTITY}`
       );
     }
     if (!(unitPrice instanceof Money)) {
       throw new ValidationError('Unit price must be a valid Money object');
     }
     this.#orderId = orderId;
     this.#productId = productId;
     this.#quantity = quantity;
     this.#unitPrice = unitPrice;
     Object.freeze(this);
   }
   ```
   </details>

5. **Add the getters,** one per field: `orderId`, `productId`, `quantity`, `unitPrice`.

   <details>
   <summary>purchase-order-item.js (addition: getters)</summary>

   ```javascript
   get orderId() {
     return this.#orderId;
   }

   get productId() {
     return this.#productId;
   }

   get quantity() {
     return this.#quantity;
   }

   get unitPrice() {
     return this.#unitPrice;
   }
   ```
   </details>

6. **Add `PurchaseOrderItem.calculateSubtotal()`.** Delegates to `Money.multiply()`, it does not reach into `Money`'s fields.

   <details>
   <summary>purchase-order-item.js (addition: calculateSubtotal)</summary>

   ```javascript
   calculateSubtotal() {
     return this.#unitPrice.multiply(this.#quantity);
   }
   ```
   </details>

7. **Add `PurchaseOrderItem.equals()`.** Two items are the same when they carry the same order, product, quantity, and unit price. That completes `PurchaseOrderItem`; the full file below includes its JSDoc.

   <details>
   <summary>purchase-order-item.js (addition: equals)</summary>

   ```javascript
   equals(other) {
     return (
       other instanceof PurchaseOrderItem &&
       this.#orderId.equals(other.orderId) &&
       this.#productId.equals(other.productId) &&
       this.#quantity === other.quantity &&
       this.#unitPrice.equals(other.unitPrice)
     );
   }
   ```
   </details>

   <details>
   <summary>purchase-order-item.js</summary>

   ```javascript
   import { ProductId } from './product-id.js';
   import { PurchaseOrderId } from './purchase-order-id.js';
   import { Money } from '../../../shared/domain/model/money.js';
   import { ValidationError } from '../../../shared/domain/model/errors.js';

   /**
    * Value object representing a single line item in a purchase order.
    * @remarks
    * Two items are the same when they carry the same order, product, quantity and unit price. The item
    * is immutable: to change a quantity, the order removes the line and adds a new one.
    */
   export class PurchaseOrderItem {
     /** @private */
     static #MIN_QUANTITY = 1;
     /** @private */
     static #MAX_QUANTITY = 1000;
     #orderId;
     #productId;
     #quantity;
     #unitPrice;

     /**
      * Creates a new PurchaseOrderItem.
      * @param {Object} params - The parameters.
      * @param {PurchaseOrderId} params.orderId - The purchase order this item belongs to.
      * @param {ProductId} params.productId - The product being ordered.
      * @param {number} params.quantity - The quantity ordered (an integer from 1 to 1000).
      * @param {Money} params.unitPrice - The unit price.
      * @throws {ValidationError} If any parameter is invalid.
      */
     constructor({ orderId, productId, quantity, unitPrice }) {
       if (!(orderId instanceof PurchaseOrderId)) {
         throw new ValidationError('Order ID must be a valid PurchaseOrderId object');
       }
       if (!(productId instanceof ProductId)) {
         throw new ValidationError('Product ID must be a valid ProductId object');
       }
       if (
         !Number.isInteger(quantity) ||
         quantity < PurchaseOrderItem.#MIN_QUANTITY ||
         quantity > PurchaseOrderItem.#MAX_QUANTITY
       ) {
         throw new ValidationError(
           `Quantity must be an integer between ${PurchaseOrderItem.#MIN_QUANTITY} and ${PurchaseOrderItem.#MAX_QUANTITY}`
         );
       }
       if (!(unitPrice instanceof Money)) {
         throw new ValidationError('Unit price must be a valid Money object');
       }
       this.#orderId = orderId;
       this.#productId = productId;
       this.#quantity = quantity;
       this.#unitPrice = unitPrice;
       Object.freeze(this);
     }

     /**
      * Gets the purchase order this item belongs to.
      * @returns {PurchaseOrderId} The order ID.
      */
     get orderId() {
       return this.#orderId;
     }

     /**
      * Gets the product being ordered.
      * @returns {ProductId} The product ID.
      */
     get productId() {
       return this.#productId;
     }

     /**
      * Gets the quantity ordered.
      * @returns {number} The quantity.
      */
     get quantity() {
       return this.#quantity;
     }

     /**
      * Gets the unit price.
      * @returns {Money} The unit price.
      */
     get unitPrice() {
       return this.#unitPrice;
     }

     /**
      * Calculates the subtotal for this line (quantity times unit price).
      * @returns {Money} The subtotal.
      */
     calculateSubtotal() {
       return this.#unitPrice.multiply(this.#quantity);
     }

     /**
      * Checks if this item equals another.
      * @param {PurchaseOrderItem} other - The other item to compare.
      * @returns {boolean} True if equal, false otherwise.
      */
     equals(other) {
       return (
         other instanceof PurchaseOrderItem &&
         this.#orderId.equals(other.orderId) &&
         this.#productId.equals(other.productId) &&
         this.#quantity === other.quantity &&
         this.#unitPrice.equals(other.unitPrice)
       );
     }
   }
   ```
   </details>

   **Note:** ADR-0007 in `## Document the Project` covers why `PurchaseOrderItem` is a value object with no id of its own, unlike the aggregate root.

   ```
   git add .
   git commit -m "feat(procurement): add purchase order item value object."
   ```

8. **Add `PurchaseOrder.addItem()`.** Open `procurement/domain/model/purchase-order.js`. It takes `{ productId, quantity, unitPrice }` where `unitPrice` is a `Money`. It rejects an order that is not `Draft`, a 51st item, a `unitPrice` that is not a `Money`, and a `unitPrice` in a currency other than the order's own. Then it builds a `PurchaseOrderItem` and pushes it to `#items`.

   <details>
   <summary>purchase-order.js (addition: addItem)</summary>

   ```javascript
   import { PurchaseOrderItem } from './purchase-order-item.js';
   import { Money } from '../../../shared/domain/model/money.js';

   addItem({ productId, quantity, unitPrice }) {
     if (!this.#state.isDraft()) {
       throw new ValidationError('Items can only be added to a PurchaseOrder in Draft state');
     }
     if (this.#items.length >= PurchaseOrder.#MAX_ITEMS) {
       throw new ValidationError(
         `PurchaseOrder cannot have more than ${PurchaseOrder.#MAX_ITEMS} items`
       );
     }
     if (!(unitPrice instanceof Money)) {
       throw new ValidationError('Unit price must be a valid Money object');
     }
     if (!unitPrice.currency.equals(this.#currency)) {
       throw new ValidationError(
         `Currency mismatch: expected ${this.#currency.code}, but got ${unitPrice.currency.code}`
       );
     }
     this.#items.push(new PurchaseOrderItem({ orderId: this.#id, productId, quantity, unitPrice }));
   }
   ```
   </details>

   **Note:** ADR-0006 covers why `addItem` takes a `Money` rather than a raw number. `productId` is passed straight through to `PurchaseOrderItem`, which validates it, so `PurchaseOrder` itself never imports `ProductId`.

   ```
   git add .
   git commit -m "feat(procurement): add add item method to purchase order."
   ```

9. **Add two items in `src/index.js`** and print `items.length`.

   <details>
   <summary>index.js (addition)</summary>

   ```javascript
   import { ProductId } from './procurement/domain/model/product-id.js';
   import { Money } from './shared/domain/model/money.js';

   order.addItem({
     productId: ProductId.generate(),
     quantity: 5,
     unitPrice: new Money({ amount: 45.99, currency: usd }),
   });
   order.addItem({
     productId: ProductId.generate(),
     quantity: 10,
     unitPrice: new Money({ amount: 22.99, currency: usd }),
   });
   console.log(`Items added: ${order.items.length}`);
   ```
   </details>

   ```
   git add .
   git commit -m "feat(main): add items to the purchase order."
   ```

10. **Publish and finish the feature.** Git Flow Helper widget → `Feature` → `Feature Publish`, then → `Feature Finish` (`Integrate Immediately`, `Keep remote branch when finished` unchecked). Merges into `develop` and pushes it too.

## Calculating the Total Price ([US005](./user-stories.md))

1. **Start the feature.** Git Flow Helper widget → `Feature` → `Feature Start` → **Feature description** `calculate-total-price` → `OK`. Creates and switches you to `feature/calculate-total-price`.

2. **Add `PurchaseOrder.calculateTotalPrice()`.** Throws if `#items` is empty; otherwise `#items.reduce`, summing `calculateSubtotal()` per item, starting from `new Money({ amount: 0, currency: this.#currency })`.

   <details>
   <summary>purchase-order.js (addition: calculateTotalPrice)</summary>

   ```javascript
   calculateTotalPrice() {
     if (this.#items.length === 0) {
       throw new ValidationError('Cannot calculate total price for an empty purchase order');
     }
     return this.#items.reduce(
       (sum, item) => sum.add(item.calculateSubtotal()),
       new Money({ amount: 0, currency: this.#currency })
     );
   }
   ```
   </details>

   ```
   git add .
   git commit -m "feat(procurement): add calculate total price method to purchase order."
   ```

3. **Record the total against the supplier in `src/index.js`.** This is the `src/index.js` half of US002. Calculate the order's total, record it on the supplier through `recordOrder()`, and print one line with the order id, the supplier, the formatted date, the state, the item count, and the total.

   <details>
   <summary>index.js (replaces the two console.log lines from US003 and US004)</summary>

   ```javascript
   const total = order.calculateTotalPrice();
   supplier.recordOrder(total);
   console.log(
     `Purchase order ${order.id} - Supplier: ${supplier.name} (${supplier.id.value}), ` +
       `Ordered at: ${order.orderDate.toString()}, State: ${order.state}, ` +
       `Items: ${order.items.length}, Total: ${total.toString()}`
   );
   ```
   </details>

   **Note:** replace the `console.log(\`Purchase order ...\`)` line from US003 and the `console.log(\`Items added: ...\`)` line from US004 with this single richer line. The final `src/index.js` is assembled in full at the end of US007.

   ```
   git add .
   git commit -m "feat(main): record the order total against the supplier."
   ```

4. **Publish and finish the feature.** Git Flow Helper widget → `Feature` → `Feature Publish`, then → `Feature Finish` (`Integrate Immediately`, `Keep remote branch when finished` unchecked). Merges into `develop` and pushes it too.

## Cancelling a Purchase Order ([US006](./user-stories.md))

1. **Start the feature.** Git Flow Helper widget → `Feature` → `Feature Start` → **Feature description** `cancel-purchase-order` → `OK`. Creates and switches you to `feature/cancel-purchase-order`.

2. **Add `PurchaseOrderState.toCanceledFrom()`.** Open `procurement/domain/model/purchase-order-state.js`. It throws only if the current state is `Completed`; otherwise it returns a fresh `Canceled` state. Any state but `Completed` can be canceled, a broader rule than the sequential transitions in US007.

   <details>
   <summary>purchase-order-state.js (addition: toCanceledFrom)</summary>

   ```javascript
   toCanceledFrom(currentState) {
     if (currentState.value === PurchaseOrderState.#VALID_STATES.COMPLETED) {
       throw new ValidationError('PurchaseOrder cannot be canceled once Completed');
     }
     return new PurchaseOrderState(PurchaseOrderState.#VALID_STATES.CANCELED);
   }
   ```
   </details>

   ```
   git add .
   git commit -m "feat(procurement): add to canceled from transition to purchase order state."
   ```

3. **Add `PurchaseOrder.cancel()`.** Delegates to `#state.toCanceledFrom(#state)`, reassigning `#state`. There is no state setter: `cancel()` is one of the six methods that can change `#state`.

   <details>
   <summary>purchase-order.js (addition: cancel)</summary>

   ```javascript
   cancel() {
     this.#state = this.#state.toCanceledFrom(this.#state);
   }
   ```
   </details>

   ```
   git add .
   git commit -m "feat(procurement): add cancel method to purchase order."
   ```

4. **Try cancelling a completed order in `src/index.js`,** wrapped in `try/catch`, and confirm it throws. The order in `src/index.js` reaches `Completed` at the end of US007, so this check goes near the bottom of the script; for now, add it after the total line and it will read correctly once US007 walks the order through its lifecycle.

   <details>
   <summary>index.js (addition)</summary>

   ```javascript
   try {
     order.cancel();
   } catch (error) {
     console.error(`Error: ${error.message}`); // PurchaseOrder cannot be canceled once Completed
   }
   ```
   </details>

   ```
   git add .
   git commit -m "feat(main): illustrate cancelling a completed purchase order."
   ```

5. **Publish and finish the feature.** Git Flow Helper widget → `Feature` → `Feature Publish`, then → `Feature Finish` (`Integrate Immediately`, `Keep remote branch when finished` unchecked). Merges into `develop` and pushes it too.

## Managing the Purchase Order Lifecycle ([US007](./user-stories.md))

1. **Start the feature.** Git Flow Helper widget → `Feature` → `Feature Start` → **Feature description** `manage-purchase-order-lifecycle` → `OK`. Creates and switches you to `feature/manage-purchase-order-lifecycle`.

2. **Add the four sequential transitions to `PurchaseOrderState`.** Open `procurement/domain/model/purchase-order-state.js`. `toSubmittedFrom`, `toApprovedFrom`, `toShippedFrom`, and `toCompletedFrom` each check that the current state is exactly the required predecessor (`Draft` → `Submitted` → `Approved` → `Shipped` → `Completed`), otherwise throw a descriptive `ValidationError`. They are the same shape, one per link in the chain. That completes `PurchaseOrderState`; the full file below includes its JSDoc, with the transitions in `Submitted` → `Canceled` order.

   <details>
   <summary>purchase-order-state.js (addition: sequential transitions)</summary>

   ```javascript
   toSubmittedFrom(currentState) {
     if (currentState.value !== PurchaseOrderState.#VALID_STATES.DRAFT) {
       throw new ValidationError('PurchaseOrder can only be submitted from Draft state');
     }
     return new PurchaseOrderState(PurchaseOrderState.#VALID_STATES.SUBMITTED);
   }

   toApprovedFrom(currentState) {
     if (currentState.value !== PurchaseOrderState.#VALID_STATES.SUBMITTED) {
       throw new ValidationError('PurchaseOrder can only be approved from Submitted state');
     }
     return new PurchaseOrderState(PurchaseOrderState.#VALID_STATES.APPROVED);
   }

   toShippedFrom(currentState) {
     if (currentState.value !== PurchaseOrderState.#VALID_STATES.APPROVED) {
       throw new ValidationError('PurchaseOrder can only be shipped from Approved state');
     }
     return new PurchaseOrderState(PurchaseOrderState.#VALID_STATES.SHIPPED);
   }

   toCompletedFrom(currentState) {
     if (currentState.value !== PurchaseOrderState.#VALID_STATES.SHIPPED) {
       throw new ValidationError('PurchaseOrder can only be completed from Shipped state');
     }
     return new PurchaseOrderState(PurchaseOrderState.#VALID_STATES.COMPLETED);
   }
   ```
   </details>

   <details>
   <summary>purchase-order-state.js</summary>

   ```javascript
   import { ValidationError } from '../../../shared/domain/model/errors.js';

   /**
    * Value object representing the state of a purchase order with valid transitions.
    * Manages the lifecycle states of a purchase order, ensuring transitions adhere to
    * procurement business rules such as sequential approval processes and restrictions
    * on modifying completed orders.
    */
   export class PurchaseOrderState {
     /** @private */
     static #VALID_STATES = {
       DRAFT: 'Draft',
       SUBMITTED: 'Submitted',
       APPROVED: 'Approved',
       SHIPPED: 'Shipped',
       COMPLETED: 'Completed',
       CANCELED: 'Canceled',
     };
     #value;

     /**
      * Creates a new PurchaseOrderState.
      * @param {string} [value=PurchaseOrderState.#VALID_STATES.DRAFT] - The state value, defaults to 'Draft'.
      * @throws {ValidationError} If the value is not a valid state.
      */
     constructor(value = PurchaseOrderState.#VALID_STATES.DRAFT) {
       this.#validateState(value);
       this.#value = value;
       Object.freeze(this);
     }

     /**
      * Validates if a state is valid.
      * @private
      * @param {string} state - The state to validate.
      * @throws {ValidationError} If the state is invalid.
      */
     #validateState(state) {
       if (!Object.values(PurchaseOrderState.#VALID_STATES).includes(state)) {
         throw new ValidationError(
           `Invalid purchase order state: ${state}. Must be one of ${Object.values(PurchaseOrderState.#VALID_STATES).join(', ')}`
         );
       }
     }

     /**
      * Transitions to Submitted state from the current state.
      *
      * **Business Rule**: A purchase order can only be submitted for approval once it has been fully drafted.
      * This ensures that all items and details are finalized before review by the procurement team.
      * Submission is restricted to the Draft state to prevent resubmission or modification after the
      * process has begun.
      *
      * @param {PurchaseOrderState} currentState - The current state.
      * @returns {PurchaseOrderState} New state instance set to 'Submitted'.
      * @throws {ValidationError} If current state is not 'Draft'.
      */
     toSubmittedFrom(currentState) {
       if (currentState.value !== PurchaseOrderState.#VALID_STATES.DRAFT) {
         throw new ValidationError('PurchaseOrder can only be submitted from Draft state');
       }
       return new PurchaseOrderState(PurchaseOrderState.#VALID_STATES.SUBMITTED);
     }

     /**
      * Transitions to Approved state from the current state.
      *
      * **Business Rule**: A purchase order must be approved by the procurement manager after submission.
      * Approval indicates that the order has been reviewed and authorized for fulfillment by the supplier.
      * This transition is only valid from the Submitted state to enforce a sequential review process.
      *
      * @param {PurchaseOrderState} currentState - The current state.
      * @returns {PurchaseOrderState} New state instance set to 'Approved'.
      * @throws {ValidationError} If current state is not 'Submitted'.
      */
     toApprovedFrom(currentState) {
       if (currentState.value !== PurchaseOrderState.#VALID_STATES.SUBMITTED) {
         throw new ValidationError('PurchaseOrder can only be approved from Submitted state');
       }
       return new PurchaseOrderState(PurchaseOrderState.#VALID_STATES.APPROVED);
     }

     /**
      * Transitions to Shipped state from the current state.
      *
      * **Business Rule**: A purchase order can only be marked as shipped after approval, indicating
      * that the supplier has dispatched the ordered items. This step ensures that shipment tracking
      * begins only after formal authorization, maintaining accountability in the supply chain.
      *
      * @param {PurchaseOrderState} currentState - The current state.
      * @returns {PurchaseOrderState} New state instance set to 'Shipped'.
      * @throws {ValidationError} If current state is not 'Approved'.
      */
     toShippedFrom(currentState) {
       if (currentState.value !== PurchaseOrderState.#VALID_STATES.APPROVED) {
         throw new ValidationError('PurchaseOrder can only be shipped from Approved state');
       }
       return new PurchaseOrderState(PurchaseOrderState.#VALID_STATES.SHIPPED);
     }

     /**
      * Transitions to Completed state from the current state.
      *
      * **Business Rule**: A purchase order is completed when all items have been received and verified
      * against the order after shipment. This finalizes the order process, locking it from further
      * modifications to ensure accurate financial and inventory records.
      *
      * @param {PurchaseOrderState} currentState - The current state.
      * @returns {PurchaseOrderState} New state instance set to 'Completed'.
      * @throws {ValidationError} If current state is not 'Shipped'.
      */
     toCompletedFrom(currentState) {
       if (currentState.value !== PurchaseOrderState.#VALID_STATES.SHIPPED) {
         throw new ValidationError('PurchaseOrder can only be completed from Shipped state');
       }
       return new PurchaseOrderState(PurchaseOrderState.#VALID_STATES.COMPLETED);
     }

     /**
      * Transitions to Canceled state from the current state.
      *
      * **Business Rule**: A purchase order can be canceled at any point before completion if it is no
      * longer necessary or if issues arise (e.g., supplier failure, budget cuts). Cancellation is blocked
      * once the order is Completed to prevent reversal of finalized transactions and maintain audit
      * integrity.
      *
      * @param {PurchaseOrderState} currentState - The current state.
      * @returns {PurchaseOrderState} New state instance set to 'Canceled'.
      * @throws {ValidationError} If current state is 'Completed'.
      */
     toCanceledFrom(currentState) {
       if (currentState.value === PurchaseOrderState.#VALID_STATES.COMPLETED) {
         throw new ValidationError('PurchaseOrder cannot be canceled once Completed');
       }
       return new PurchaseOrderState(PurchaseOrderState.#VALID_STATES.CANCELED);
     }

     /**
      * Checks if the current state is Draft.
      *
      * **Business Rule**: The Draft state indicates that the purchase order is still being prepared
      * and can be modified (e.g., items added) before submission. This method provides a way to
      * determine if modifications are allowed without relying on string literals.
      *
      * @returns {boolean} True if the state is 'Draft', false otherwise.
      */
     isDraft() {
       return this.#value === PurchaseOrderState.#VALID_STATES.DRAFT;
     }

     /**
      * Gets the state value.
      * @returns {string} The state (e.g., 'Draft', 'Submitted').
      */
     get value() {
       return this.#value;
     }

     /**
      * Checks if this state equals another.
      * @param {PurchaseOrderState} other - The other state to compare.
      * @returns {boolean} True if equal, false otherwise.
      */
     equals(other) {
       return other instanceof PurchaseOrderState && this.#value === other.value;
     }
   }
   ```
   </details>

   **Note:** the file's final member order is not the order you built them in: `isDraft`, `value`, and `equals` were written back in US003, but they sit after the transitions. Match the layout above; it keeps all five transitions together.

   ```
   git add .
   git commit -m "feat(procurement): add sequential lifecycle transitions to purchase order state."
   ```

3. **Add `submit`, `approve`, `ship`, and `complete` to `PurchaseOrder`.** Open `procurement/domain/model/purchase-order.js`. Each delegates to the matching `PurchaseOrderState` transition, reassigning `#state`, exactly like `cancel()` from US006. That completes `PurchaseOrder`; the full file below includes its JSDoc and the final member order.

   <details>
   <summary>purchase-order.js (addition: submit, approve, ship, complete)</summary>

   ```javascript
   submit() {
     this.#state = this.#state.toSubmittedFrom(this.#state);
   }

   approve() {
     this.#state = this.#state.toApprovedFrom(this.#state);
   }

   ship() {
     this.#state = this.#state.toShippedFrom(this.#state);
   }

   complete() {
     this.#state = this.#state.toCompletedFrom(this.#state);
   }
   ```
   </details>

   <details>
   <summary>purchase-order.js</summary>

   ```javascript
   import { SupplierId } from './supplier-id.js';
   import { PurchaseOrderId } from './purchase-order-id.js';
   import { PurchaseOrderItem } from './purchase-order-item.js';
   import { Money } from '../../../shared/domain/model/money.js';
   import { DateTime } from '../../../shared/domain/model/date-time.js';
   import { ValidationError } from '../../../shared/domain/model/errors.js';
   import { PurchaseOrderState } from './purchase-order-state.js';
   import { Currency } from '../../../shared/domain/model/currency.js';

   /**
    * Aggregate root representing a purchase order with a lifecycle state, in the Procurement context.
    */
   export class PurchaseOrder {
     /** @private */
     static #MAX_ITEMS = 50;
     #id;
     #supplierId;
     #currency;
     #orderDate;
     #items;
     #state;

     /**
      * Creates a new PurchaseOrder.
      * @param {Object} params - The parameters.
      * @param {SupplierId} params.supplierId - The supplier ID.
      * @param {Currency} params.currency - The currency for the order.
      * @param {DateTime} [params.orderDate] - The order date (defaults to now).
      * @throws {ValidationError} If supplierId or currency is invalid.
      */
     constructor({ supplierId, currency, orderDate }) {
       if (!(supplierId instanceof SupplierId)) {
         throw new ValidationError('SupplierId must be a valid SupplierId object');
       }
       if (!(currency instanceof Currency)) {
         throw new ValidationError('Currency must be a valid Currency object');
       }
       this.#id = PurchaseOrderId.generate();
       this.#supplierId = supplierId;
       this.#currency = currency;
       this.#orderDate = orderDate instanceof DateTime ? orderDate : new DateTime();
       this.#items = [];
       this.#state = new PurchaseOrderState(); // Initial state: Draft
     }

     /**
      * Adds an item to the purchase order if conditions are met.
      *
      * **Business Rules**:
      * - **Draft State Only**: Items can only be added while the purchase order is in the Draft state.
      *   This ensures that modifications are restricted to the preparation phase, preventing changes
      *   after submission for approval or fulfillment to maintain order integrity and auditability.
      * - **Maximum Items Limit**: A purchase order cannot exceed 50 items. This constraint prevents
      *   overly complex orders that could complicate supplier fulfillment, inventory management,
      *   or financial reconciliation, keeping orders manageable within operational capacity.
      * - **Unit Price in the Order's Currency**: the unit price travels as a `Money` value object. It
      *   must be priced in the order's own currency, so a mismatch is caught at the boundary instead
      *   of surfacing later inside a total.
      *
      * @param {Object} params - The item parameters.
      * @param {ProductId} params.productId - The product being ordered.
      * @param {number} params.quantity - The quantity.
      * @param {Money} params.unitPrice - The unit price, in the order's currency.
      * @throws {ValidationError} If the state is not Draft, the max items (50) are exceeded, or the unit price is not a Money in the order's currency.
      */
     addItem({ productId, quantity, unitPrice }) {
       if (!this.#state.isDraft()) {
         throw new ValidationError('Items can only be added to a PurchaseOrder in Draft state');
       }
       if (this.#items.length >= PurchaseOrder.#MAX_ITEMS) {
         throw new ValidationError(
           `PurchaseOrder cannot have more than ${PurchaseOrder.#MAX_ITEMS} items`
         );
       }
       if (!(unitPrice instanceof Money)) {
         throw new ValidationError('Unit price must be a valid Money object');
       }
       if (!unitPrice.currency.equals(this.#currency)) {
         throw new ValidationError(
           `Currency mismatch: expected ${this.#currency.code}, but got ${unitPrice.currency.code}`
         );
       }
       this.#items.push(new PurchaseOrderItem({ orderId: this.#id, productId, quantity, unitPrice }));
     }

     /**
      * Calculates the total price of all items.
      * @returns {Money} The total price.
      * @throws {ValidationError} If the order is empty.
      */
     calculateTotalPrice() {
       if (this.#items.length === 0) {
         throw new ValidationError('Cannot calculate total price for an empty purchase order');
       }
       return this.#items.reduce(
         (sum, item) => sum.add(item.calculateSubtotal()),
         new Money({ amount: 0, currency: this.#currency })
       );
     }

     /**
      * Transitions the purchase order to Submitted state.
      * @throws {ValidationError} If not in Draft state.
      */
     submit() {
       this.#state = this.#state.toSubmittedFrom(this.#state);
     }

     /**
      * Transitions the purchase order to Approved state.
      * @throws {ValidationError} If not in Submitted state.
      */
     approve() {
       this.#state = this.#state.toApprovedFrom(this.#state);
     }

     /**
      * Transitions the purchase order to Shipped state.
      * @throws {ValidationError} If not in Approved state.
      */
     ship() {
       this.#state = this.#state.toShippedFrom(this.#state);
     }

     /**
      * Transitions the purchase order to Completed state.
      * @throws {ValidationError} If not in Shipped state.
      */
     complete() {
       this.#state = this.#state.toCompletedFrom(this.#state);
     }

     /**
      * Transitions the purchase order to Canceled state.
      * @throws {ValidationError} If in Completed state.
      */
     cancel() {
       this.#state = this.#state.toCanceledFrom(this.#state);
     }

     /**
      * Gets the purchase order ID.
      * @returns {PurchaseOrderId} The order ID.
      */
     get id() {
       return this.#id;
     }

     /**
      * Gets the supplier ID.
      * @returns {SupplierId} The supplier ID.
      */
     get supplierId() {
       return this.#supplierId;
     }

     /**
      * Gets the currency.
      * @returns {Currency} The currency.
      */
     get currency() {
       return this.#currency;
     }

     /**
      * Gets the order date.
      * @returns {DateTime} The order date.
      */
     get orderDate() {
       return this.#orderDate;
     }

     /**
      * Gets a read-only snapshot of the order's items.
      * @remarks
      * Returns a frozen copy, not the internal array: mutating the returned array (push/pop/splice)
      * never reaches `#items`. `addItem()` is the only way to change what's in the order.
      * @returns {ReadonlyArray<PurchaseOrderItem>} The items.
      */
     get items() {
       return Object.freeze([...this.#items]);
     }

     /**
      * Gets the current state of the purchase order.
      * @returns {string} The state (e.g., 'Draft', 'Submitted', 'Approved', 'Shipped', 'Completed', 'Canceled').
      */
     get state() {
       return this.#state.value;
     }
   }
   ```
   </details>

   **Note:** the file's final member order is not the order you built them in: `cancel` (US006) sits with the other transitions, and the getters (US003) sit at the end. Match the layout above; it groups the lifecycle methods together.

   ```
   git add .
   git commit -m "feat(procurement): add lifecycle transition methods to purchase order."
   ```

4. **Finish `src/index.js`.** Walk the order through its full lifecycle, print the final state and the total recorded against the supplier, then show the two guard rails on a `Completed` order (can't add an item, can't cancel), each in a `try/catch` so the script runs to completion. This is the final, complete script:

   <details>
   <summary>index.js (final)</summary>

   ```javascript
   import { Supplier } from './scm/domain/model/supplier.js';
   import { SupplierId as ScmSupplierId } from './scm/domain/model/supplier-id.js';
   import { SupplierId as ProcurementSupplierId } from './procurement/domain/model/supplier-id.js';
   import { ProductId } from './procurement/domain/model/product-id.js';
   import { PurchaseOrder } from './procurement/domain/model/purchase-order.js';
   import { Currency } from './shared/domain/model/currency.js';
   import { Money } from './shared/domain/model/money.js';
   import { DateTime } from './shared/domain/model/date-time.js';

   // Register a supplier in the SCM context.
   const supplier = new Supplier({
     id: ScmSupplierId.generate(),
     name: 'Acme Corp',
     contactEmail: 'contact@acme.com',
   });
   console.log(`Registered supplier ${supplier.id} - ${supplier.name} <${supplier.contactEmail}>`);

   // Raise a purchase order in the Procurement context. The order references the supplier
   // through Procurement's own SupplierId, never SCM's type.
   const usd = new Currency('USD');
   const order = new PurchaseOrder({
     supplierId: new ProcurementSupplierId(supplier.id.value),
     currency: usd,
     orderDate: new DateTime(new Date('2025-04-10T10:00:00Z')),
   });
   order.addItem({
     productId: ProductId.generate(),
     quantity: 5,
     unitPrice: new Money({ amount: 45.99, currency: usd }),
   });
   order.addItem({
     productId: ProductId.generate(),
     quantity: 10,
     unitPrice: new Money({ amount: 22.99, currency: usd }),
   });

   const total = order.calculateTotalPrice();
   supplier.recordOrder(total);
   console.log(
     `Purchase order ${order.id} - Supplier: ${supplier.name} (${supplier.id.value}), ` +
       `Ordered at: ${order.orderDate.toString()}, State: ${order.state}, ` +
       `Items: ${order.items.length}, Total: ${total.toString()}`
   );

   // Walk the order through its lifecycle.
   order.submit();
   order.approve();
   order.ship();
   order.complete();
   console.log(
     `Order ${order.id} state: ${order.state}, last order recorded against ${supplier.name}: ${supplier.lastOrderTotalPrice.toString()}`
   );

   // The guard rails.
   try {
     order.addItem({
       productId: ProductId.generate(),
       quantity: 1,
       unitPrice: new Money({ amount: 10, currency: usd }),
     });
   } catch (error) {
     console.error(`Error: ${error.message}`); // Items can only be added to a PurchaseOrder in Draft state
   }

   try {
     order.cancel();
   } catch (error) {
     console.error(`Error: ${error.message}`); // PurchaseOrder cannot be canceled once Completed
   }
   ```
   </details>

   ```
   git add .
   git commit -m "feat(main): walk the purchase order through its full lifecycle."
   ```

5. **Publish and finish the feature.** Git Flow Helper widget → `Feature` → `Feature Publish`, then → `Feature Finish` (`Integrate Immediately`, `Keep remote branch when finished` unchecked). Merges into `develop` and pushes it too.

---

## Prepare the First Release

**All of this happens on `develop`:** `Feature Finish` leaves you there. These are the last steps before tagging `1.0.0`: two checks, then the files a public repo needs. Every real public repo ships a `LICENSE.md`, a `README.md`, and a `CONTRIBUTING.md`, but none of them belonged at Project Setup, back then there was nothing to describe.

1. **Run the script and the linter end to end.**

   ```
   npm start
   ```

   Confirm the output reads:

   ```
   Registered supplier <uuid> - Acme Corp <contact@acme.com>
   Purchase order <uuid> - Supplier: Acme Corp (<uuid>), Ordered at: April 10, 2025 at 05:00 AM, State: Draft, Items: 2, Total: USD 459.85
   Order <uuid> state: Completed, last order recorded against Acme Corp: USD 459.85
   Error: Items can only be added to a PurchaseOrder in Draft state
   Error: PurchaseOrder cannot be canceled once Completed
   ```

   Then:

   ```
   npm test
   ```

   `npm test` runs ESLint over the project; it should report no errors. There are no unit tests: this manual `npm start` run, checked against every scenario in `docs/user-stories.md`, is the acceptance check, and `npm test` guards code quality.

   **Tip:** `npm run format` runs Prettier over `src/` and the root files, so everything stays in one style.

2. **Add `LICENSE.md`.** Right-click the project root → `New` → `File` → type `LICENSE.md` → Enter. The README's badge links to it, so it goes in first.

   <details>
   <summary>LICENSE.md</summary>

   ```markdown
   # MIT License

   Copyright (c) 2026 Web Application Development Team

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
   # JavaScript Review (javascript-review)

   [![Node.js Version](https://img.shields.io/badge/node-%3E%3D20.0.0-brightgreen)](https://nodejs.org/)
   [![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE.md)

   ## Overview
   A JavaScript demonstration project to illustrate **Object-Oriented Programming (OOP)** and **Domain-Driven Design (DDD)** principles within a Supply Chain Management and Procurement context.

   The codebase serves as a reference for implementing domain models in bounded contexts using the latest JavaScript features.

   ## 🚀 Key Features
   - **Bounded Contexts:** Clear separation between `Procurement`, `SCM`, and `Shared` subdomains.
   - **Rich Domain Models:** Entities with internal business logic instead of anemic data structures.
   - **Deep Immutability:** Value Objects protected by `Object.freeze()` and defensive cloning.
   - **Standardized Identifiers:** UUID v7 based Value Objects for all entity IDs.
   - **State Machine:** Robust lifecycle management for Purchase Orders.
   - **Professional Tooling:** Integrated ESLint, Prettier, and automated linting.

   ## 🛠️ Principles in Action
   - **Encapsulation:** Uses private class fields (`#`) to protect internal state.
   - **Validation:** Enforces invariants at the constructor level; objects are always valid.
   - **Value Objects:** `Money`, `DateTime`, `Currency`, and IDs are treated as immutable values.
   - **Aggregate Roots:** `PurchaseOrder` manages the consistency of its `PurchaseOrderItem` collection.

   ## 📖 Documentation
   - [User Stories](docs/user-stories.md): requirements as User Stories with Given-When-Then acceptance criteria, plus a Requirement Traceability Matrix.
   - [Architecture Decision Records](docs/adrs.md): the DDD and design decisions behind the model.
   - [Class Diagram](docs/class-diagram.puml): visual representation of the domain model.
   - [Contributing Guide](CONTRIBUTING.md): development standards and workflow.
   - [Changelog](CHANGELOG.md): history of project evolutions.

   ## 🏁 Getting Started

   ### Prerequisites
   - **Node.js** (v20.x or higher recommended)
   - **npm** (v10.x or higher)

   ### Installation
   ```bash
   npm install
   ```

   ### Available Scripts
   - `npm start`: Runs the demonstration entry point (`src/index.js`).
   - `npm run dev`: Starts the application in watch mode.
   - `npm run lint`: Validates code quality using ESLint.
   - `npm run format`: Standardizes code style using Prettier.

   ## 🤝 Contributing
   Contributions are welcome! Please read our [CONTRIBUTING.md](CONTRIBUTING.md) for details on our code of conduct, DDD standards, and the process for submitting pull requests.

   ## 📝 License
   This project is licensed under the MIT License. See the [LICENSE.md](LICENSE.md) file for details.

   ---
   **Developed by the Web Application Development Team**
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

   ```markdown
   # Contributing to JavaScript Review

   Thank you for your interest in contributing to the JavaScript Review project! This document outlines the standards and workflows we follow to maintain a high-quality, domain-driven codebase.

   ## Core Principles

   We strictly adhere to the following architectural and programming paradigms:

   ### 1. Domain-Driven Design (DDD)
   - **Bounded Contexts:** Logic is organized into specific contexts (e.g., `procurement`, `scm`, `shared`).
   - **Entities & Aggregates:** Business logic belongs in rich domain models, not in service layers. Use private fields (`#`) for internal state.
   - **Value Objects:** Use immutable objects for attributes that have no identity (e.g., `Money`, `DateTime`, `SupplierId`). All Value Objects must implement `equals()` and `toString()`.
   - **Deep Immutability:** Value Objects must be frozen with `Object.freeze()` and use defensive cloning for mutable internal types like `Date`.

   ### 2. Object-Oriented Programming (OOP)
   - **Encapsulation:** Protect the internal state of objects. Provide access via getters or domain-specific methods that maintain invariants.
   - **Validation:** Objects must be valid upon creation. Use constructors to enforce business rules and throw errors if preconditions are not met.

   ### 3. Modern JavaScript
   - Use **ES Modules** (`"type": "module"` in `package.json`).
   - Leverage latest features like private class fields (`#`), optional chaining (`?.`), and nullish coalescing (`??`).
   - Use `uuid` version 7 for time-ordered unique identifiers.

   ## Development Workflow

   ### Git Flow & Branching
   We follow a simplified **Git Flow** strategy:
   - `main`: Production-ready code.
   - `develop`: Integration branch for features.
   - `feature/*`: New features or improvements.
   - `release/*`: Prepares for a new release.
   - `fix/*`: Bug fixes.
   - `hotfix/*`: Urgent production fixes.

   ### Conventional Commits
   Commit messages must follow the [Conventional Commits](https://www.conventionalcommits.org/) specification:
   - `feat`: A new feature.
   - `fix`: A bug fix.
   - `docs`: Documentation only changes.
   - `style`: Changes that do not affect the meaning of the code (white-space, formatting, etc).
   - `refactor`: A code change that neither fixes a bug nor adds a feature.
   - `perf`: A code change that improves performance.
   - `test`: Adding missing tests or correcting existing tests.
   - `build`: Changes that affect the build system or external dependencies (e.g., adding Prettier/ESLint configs).
   - `chore`: Other changes that don't modify src or test files.

   **Example:** `feat(procurement): add ability to cancel purchase orders`

   ### Semantic Versioning
   The project follows [SemVer](https://semver.org/).
   - **MAJOR** version for incompatible API changes.
   - **MINOR** version for functionality added in a backwards compatible manner.
   - **PATCH** version for backwards compatible bug fixes.

   ## Quality Standards

   ### Linting & Formatting
   Before submitting changes, ensure your code passes linting and is properly formatted:
   - `npm run lint`: Runs ESLint to check for code quality issues.
   - `npm run format`: Runs Prettier to ensure consistent code style.

   ### Documentation
   - **User Stories:** Update `docs/user-stories.md` using the Given-When-Then format for any new or changed behavior.
   - **Class Diagram:** Update `docs/class-diagram.puml` to reflect changes in the domain model.
   - **Changelog:** Add an entry to `CHANGELOG.md` under the `[Unreleased]` section.

   ## Getting Started
   1. Clone the repository.
   2. Install dependencies: `npm install`.
   3. Create a new branch: `git checkout -b feature/your-feature-name`.
   4. Make your changes following the principles above.
   5. Run linting and formatting.
   6. Commit your changes using Conventional Commits.
   7. Open a Pull Request.

   Thank you for contributing!
   ```
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

   ## [1.0.0] - 2026-09-08

   ### Added
   - SCM and Procurement bounded contexts with a `shared` kernel.
   - `Supplier` aggregate (SCM) covering US001-US002: register with a validated name and email, record its last order total through an intention-revealing method.
   - `PurchaseOrder` aggregate root (Procurement) covering US003-US007: create, add items, calculate the total, and walk the lifecycle (submit, approve, ship, complete, cancel).
   - `PurchaseOrderItem` value object, priced in the order's own currency.
   - `PurchaseOrderState` value object encapsulating the six states and every legal transition.
   - Value objects: `Money`, `Currency` (ISO 4217 whitelist), `DateTime` (immutable, defensively copied), `SupplierId` (SCM and Procurement each own a copy), `ProductId`, `PurchaseOrderId`.
   - Each bounded context owns its identifiers: Procurement has its own `SupplierId` rather than importing SCM's, so the two contexts stay decoupled.
   - `generateUuid()` / `validateUuid()` utility over the `uuid` package, producing time-ordered UUID v7 identifiers.
   - ESLint and Prettier configuration, wired to `npm run lint` (also `npm test`) and `npm run format`.
   - `docs/user-stories.md` (with a Requirement Traceability Matrix), `docs/class-diagram.puml`, `docs/adrs.md`.
   - `CONTRIBUTING.md` with OOP, DDD, Git Flow, and Conventional Commit guidelines.

   ### Design notes
   - ECMAScript private fields (`#`) throughout, and `Object.freeze` on every value object for true runtime immutability.
   - `PurchaseOrder.items` returns a frozen copy, so the internal array can't be mutated from outside; `addItem()` is the only way in.
   - Items can only be added while the order is `Draft`; state transitions are the only way to change `#state`.
   - `addItem()` takes a `Money` in the order's currency, rejecting a mismatch at the boundary.
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

   - **US001-US002 (SCM):** register a `Supplier` with a validated name and contact email, then change its name, update its email, or record its last order total, each through an intention-revealing method.
   - **US003-US007 (Procurement):** create a `PurchaseOrder`, add items while it is `Draft`, calculate the total, and walk it through its lifecycle (submit, approve, ship, complete) or cancel it from any state but `Completed`.
   - `PurchaseOrderItem` value object, priced in the order's own currency; `PurchaseOrderState` value object encapsulating the six states and every legal transition.
   - Shared value objects: `Money`, `Currency` (USD, EUR, GBP, JPY), `DateTime` (immutable, defensively copied), and the per-context identifiers `SupplierId`, `ProductId`, `PurchaseOrderId`, all built on time-ordered UUID v7.
   - Each bounded context owns its identifiers: Procurement carries its own `SupplierId` rather than importing SCM's.
   - ECMAScript private fields (`#`) and `Object.freeze` throughout; `PurchaseOrder.items` returns a frozen copy, so `addItem()` is the only way in.
   - ESLint and Prettier, wired to `npm run lint` (also `npm test`) and `npm run format`.
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

1. **Add the Architecture Decision Records** to a single `docs/adrs.md`. Right-click the `docs` folder → `New` → `File` → type `adrs.md` → Enter. Seven decisions, written in one sitting: real runtime encapsulation, UUID v7, per-context identifiers, intention-revealing methods, defensive copies, how `addItem` is priced, and the purchase order line as a value object.

   <details>
   <summary>docs/adrs.md</summary>

   ````markdown
   # Architecture Decision Records

   # ADR-0001: ECMAScript Private Fields and `Object.freeze` for Real Encapsulation

   **Status:** Accepted

   ## Context

   Every domain type in this project hides its internal state behind accessors, and every value object is meant to be immutable once built. A `_`-prefixed property is private only by convention: any caller can still read or overwrite it. A plain object can still have properties reassigned after construction. For a project whose whole point is teaching encapsulation and value semantics, "private" and "immutable" need to hold at runtime.

   ## Decision Drivers

   - Encapsulation should hold at runtime, not by naming convention.
   - The `PurchaseOrder` aggregate protects invariants (state transitions, item validation); a caller that can reach `#items` or `#state` directly defeats that.
   - A `Money` or a `Currency` that can be mutated after construction is not a value object.

   ## Considered Options

   1. ECMAScript private fields (`#name`) plus `Object.freeze(this)` in the constructor *(Chosen)*
   2. `_`-prefixed properties and a documented "do not touch" rule
   3. Closures over local variables

   ## Decision

   Every field on every class uses the `#` syntax. Every value object (`Money`, `Currency`, `DateTime`, `SupplierId`, `ProductId`, `PurchaseOrderId`, `PurchaseOrderItem`, `PurchaseOrderState`) calls `Object.freeze(this)` as the last line of its constructor. Getters expose read access; there are no field setters.

   ## Consequences

   **Positive:**
   - `order.#items` is a syntax error outside the class.
   - Reassigning `money.#amount` from outside is impossible; even a mistaken internal reassignment after `freeze` throws in strict mode (ES modules are strict).

   **Negative:**
   - `#` fields can't be enumerated or spread; serialization has to go through explicit accessors. Not a problem for this console app.

   ---

   # ADR-0002: Time-Ordered UUID v7 via the `uuid` Package

   **Status:** Accepted

   ## Context

   `SupplierId`, `ProductId`, and `PurchaseOrderId` each need a unique identifier. `crypto.randomUUID()` is built in but produces a v4 (fully random) UUID, and offers no way to validate that a string is a UUID.

   ## Decision Drivers

   - A time-ordered identifier sorts and indexes better than a fully random one.
   - A provided id (for example, a supplier id agreed with the SCM context) should be checked for shape, not trusted blindly.

   ## Considered Options

   1. `uuid` package: `v7()` for generation, `validate()` for checking *(Chosen)*
   2. Built-in `crypto.randomUUID()` (v4, no validation)

   ## Decision

   `src/shared/domain/model/uuid.js` wraps the `uuid` package: `generateUuid()` returns a v7, `validateUuid()` checks a string. Every id value object generates through `generateUuid()` and rejects a malformed provided id.

   ## Consequences

   **Positive:**
   - One dependency, one place that decides the UUID version.

   **Negative:**
   - A runtime dependency where there was none. Acceptable: it's a tiny, ubiquitous package.

   ---

   # ADR-0003: Each Bounded Context Owns Its Identifiers

   **Status:** Accepted

   ## Context

   A `PurchaseOrder` belongs to a supplier. The supplier's identity lives in the SCM context as `scm.SupplierId`. The Procurement context needs to name that supplier, but importing `scm.SupplierId` into `procurement` couples the two contexts: a change to SCM's identifier type would ripple into Procurement.

   ## Decision Drivers

   - Bounded contexts should be able to evolve independently.
   - The reference still needs to be a value object: a raw `string` reintroduces the primitive-obsession problem the identifiers solve.

   ## Considered Options

   1. `procurement` defines its own `SupplierId`, carrying just the UUID string both contexts agree on *(Chosen)*
   2. `procurement` imports `scm.SupplierId`
   3. `procurement` uses a raw `string`

   ## Decision

   `src/procurement/domain/model/supplier-id.js` is Procurement's own `SupplierId`. It has no `static generate()` factory (a purchase order is always raised against a supplier that already exists) and validates the UUID it is given. `PurchaseOrder.#supplierId` is this type. The two `SupplierId` classes never reference each other; the same reasoning moved `ProductId` under `procurement`.

   ## Consequences

   **Positive:**
   - `procurement` has no dependency on `scm`.
   - A `SupplierId` is still distinct from a `ProductId` at the call site.

   **Negative:**
   - Two `SupplierId` classes to keep conceptually aligned. The shared contract is just "a UUID string".

   ---

   # ADR-0004: Intention-Revealing Methods, Not Raw Setters

   **Status:** Accepted

   ## Context

   `Supplier` exposes its name, contact email, and last order total for change over its lifetime. An early version could have exposed plain setters (`set name`, `set contactEmail`, `set lastOrderTotalPrice`), so any caller could overwrite a field with any value, for any reason. The same temptation exists for `PurchaseOrder`'s state.

   ## Decision Drivers

   - The entity should be the one place that decides how its own state evolves, and every change should run the same validation.
   - A method name can say *why* a field changes; a setter cannot.

   ## Considered Options

   1. `changeName()`, `updateEmail()`, `recordOrder()` on `Supplier`, plus lifecycle methods on `PurchaseOrder` *(Chosen)*
   2. Public setters (`set name`, `set contactEmail`, `set lastOrderTotalPrice`, `set state`)

   ## Decision

   `Supplier` changes only through `changeName()`, `updateEmail()`, and `recordOrder()`, each validating its input; the constructor delegates to the same methods so construction and later change share one rule. `PurchaseOrder` has no state setter: `submit()`, `approve()`, `ship()`, `complete()`, and `cancel()` are the only ways to change `#state`, and each one asks `PurchaseOrderState` whether the transition is legal.

   ## Consequences

   **Positive:**
   - Every state change reads as a domain action at the call site.

   **Negative:**
   - Slightly more code than a setter. Worth it.

   ---

   # ADR-0005: Defensive Copy for Exposed Collections

   **Status:** Accepted

   ## Context

   `PurchaseOrder.items` exposes the order's line items. Returning the internal array lets a caller `push` or `splice` it directly, bypassing `addItem()`'s validation (Draft state, item limit, currency check).

   ## Decision Drivers

   - `addItem()` must be the only way to change what's in the order.
   - The guarantee should hold at runtime.

   ## Considered Options

   1. Return a frozen shallow copy: `Object.freeze([...this.#items])` *(Chosen)*
   2. Return the internal array and trust callers not to mutate it
   3. Freeze the internal array itself (then `addItem` can't push either)

   ## Decision

   `get items()` returns `Object.freeze([...this.#items])`. Mutating the returned array never reaches `#items`, and the freeze makes an attempted mutation fail loudly instead of silently no-op'ing on a copy.

   ## Consequences

   **Positive:**
   - The order's contents can only change through `addItem()`.

   **Negative:**
   - A new array per call. Negligible for orders of this size.

   ---

   # ADR-0006: `addItem` Takes a `Money`, Priced in the Order's Currency

   **Status:** Accepted

   ## Context

   A `PurchaseOrderItem`'s unit price is money. `addItem()` could take a raw `number` and build the `Money` itself, or take a `Money` the caller already built.

   ## Decision Drivers

   - The unit price is a monetary value; it should travel as one.
   - An order has a single currency; an item priced in another currency is a bug.

   ## Considered Options

   1. `addItem({ productId, quantity, unitPrice })` where `unitPrice` is a `Money`, rejecting a currency other than the order's *(Chosen)*
   2. `addItem({ productId, quantity, unitPrice })` where `unitPrice` is a `number`, building `new Money({ amount, currency: this.#currency })` internally

   ## Decision

   `addItem()` takes a `Money`. It checks `unitPrice instanceof Money` and `unitPrice.currency.equals(this.#currency)`, throwing a clear "Currency mismatch" error otherwise.

   ## Consequences

   **Positive:**
   - The unit price is a complete value object end to end.
   - A currency mismatch fails loudly at the boundary, not silently deep in a total.

   **Negative:**
   - The caller constructs the `Money`. That's the point: the price is theirs to state.

   ---

   # ADR-0007: `PurchaseOrderItem` Is a Value Object, Not an Entity

   **Status:** Accepted

   ## Context

   A purchase order line couples a product, a quantity, and a unit price. It could be modeled as a non-root entity with its own `PurchaseOrderItemId`, or as a value object with no identity of its own.

   ## Decision Drivers

   - Nothing in the domain needs to reference a single line from outside the order or track it across a change: to change a quantity, the order drops the line and adds a new one.
   - Two lines with the same product, quantity, and unit price are, for this model, the same line.
   - A synthetic id that is never looked up is ceremony without a payoff.

   ## Considered Options

   1. `PurchaseOrderItem` as a frozen value object, equal by its attributes *(Chosen)*
   2. `PurchaseOrderItem` as an entity with a `PurchaseOrderItemId`

   ## Decision

   `src/procurement/domain/model/purchase-order-item.js` is a value object: frozen in the constructor, with an `equals()` that compares `orderId`, `productId`, `quantity`, and `unitPrice`. It keeps `orderId` so a detached line still knows the order it was built for, but it has no identity beyond its values.

   ## Consequences

   **Positive:**
   - The item is immutable and compared by value, like every other value object in the model.
   - No id class to mint or carry for a concept that never needs one.

   **Negative:**
   - "Editing" a line means replacing it. That matches how a draft purchase order is actually revised.
   ````
   </details>

   ```
   git add .
   git commit -m "docs: add architecture decision records."
   git push
   ```

2. **Confirm the Requirement Traceability Matrix in `docs/user-stories.md`** maps every scenario to what implements it. It was added with the user stories; check each row still points at the right class now that all the code exists.

---

## Appendix

Reference notes for situations that come up now and then. Skip past this on a normal run and come back when you hit one of them.

### Continuing on another computer

- Install [WebStorm](https://www.jetbrains.com/webstorm/) and the current Node.js LTS.
- Sign in to `gh` again: `gh auth login` (see [Project Setup step 12](#project-setup)).
- Clone: `gh repo clone <org>/javascript-review`, or from WebStorm's Welcome screen: `Clone Repository`, paste `https://github.com/<org>/javascript-review.git`.
- Open the project, then run `npm install` in the terminal to restore `node_modules/`.
- Plugins live in the IDE, not the repo, so reinstall Git Flow Helper (Project Setup step 13) and plantuml4idea (Project Setup step 9) if this machine doesn't have them.
- Register your GitHub account in the IDE (Project Setup step 14): get the token with

  ```
  gh auth token
  ```

  and add it under `File` → `Settings` → `Version Control` → `GitHub`.

### Signing in to GitHub with a token

The guide uses `gh auth login` (Project Setup step 12), which is the simplest way. If you can't install `gh`, GitHub also accepts a Personal Access Token.

1. GitHub → `Settings` → `Developer settings` → `Personal access tokens` → `Generate new token (classic)`. Use **classic**, not "Fine-grained tokens": fine-grained tokens need an organization owner's approval before they work.
2. Fill in:
   - **Note:** `UPC` (a label to recognize it later).
   - **Expiration:** the default is fine.
   - **Scopes:** check only the top-level `repo` checkbox.
3. Click **Generate token**, then copy it somewhere safe (a password manager) before navigating away. GitHub shows it **only once**. The IDE GitHub account (Project Setup step 14) needs it too.

### Creating the repo without the GitHub CLI

No `gh`? Do the whole thing through the GitHub website plus plain `git`.

1. Authenticate git first, since `gh auth login` isn't available: follow [Signing in to GitHub with a token](#signing-in-to-github-with-a-token).
2. On GitHub, inside your organization, create an empty **private** repo named `javascript-review`, with no README, license, or `.gitignore` (this project already has all three).
3. On the repo's "Quick setup" page, copy the **HTTPS** clone URL, the one ending in `.git`.
4. From the project root, add the remote and push:

   ```
   git remote add origin https://github.com/<org>/javascript-review.git
   git push -u origin main
   ```

5. On GitHub, set the repo's **About** description manually (gear icon next to "About" on the repo page):

   ```
   JavaScript console application illustrating object-oriented and domain-driven design principles in the context of Supply Chain Management and Procurement.
   ```

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

### Removing a stray .git folder

Ran `git init` from inside a subfolder instead of the project root? A `.git` folder is now sitting in that subfolder. `.git` is hidden, so reveal it first:

- **macOS (Finder):** press `Cmd+Shift+.`
- **Windows (File Explorer):** turn on `View` → `Show` → `Hidden items`

Delete that `.git` folder, or from a terminal opened in the subfolder:

```
rm -rf .git                        # macOS / Linux / Git Bash
Remove-Item -Recurse -Force .git   # Windows PowerShell
```

Then re-run the initialization from the project root:

```
git init -b main
```

### Fixing file or folder permissions

On a shared macOS machine, a file or folder owned by another account causes one of two problems:

- **New files and folders don't appear** in WebStorm's `Project` tool window, even though they exist on disk.
- **`npm install` fails** with `permission denied` (an `EACCES` error), or only works with `sudo` and a password.

Both mean the path is not owned by your account. A project's `npm install` only writes to `node_modules/` and the npm cache (`~/.npm`), both of which you own, so it should **never** need `sudo`; when it does, something is owned by `root`, usually because `sudo npm install` was run once before.

**Don't keep adding `sudo`.** Give the path back to your account. On the lab machines the account is `alumnos` and the group is `staff`; put your project's path in place of the placeholder:

```
sudo chown -R alumnos:staff {CHANGE_WITH_YOUR_PATH}
```

For example:

```
sudo chown -R alumnos:staff ~/Documents/javascript-review
```

On your own Mac, use your account name (what `whoami` prints) instead of `alumnos`. If `npm install` was the failure, do the npm cache too:

```
sudo chown -R alumnos:staff ~/.npm
```

Then delete any half-written `node_modules/` and install again as yourself, no `sudo`:

```
rm -rf node_modules
npm install
```

**Windows:** this doesn't happen with `npm`. If a folder is marked read-only, right-click it → `Properties` → uncheck `Read-only`.

If you can't install Node yourself on the machine, ask for it through a version manager (`nvm`, `fnm`, or `volta`); those keep Node and npm inside your home folder, so nothing they do needs root.

### If the class diagram doesn't render

- Confirm the **plantuml4idea** plugin is installed and enabled (`File` → `Settings` → `Plugins` → `Installed`).
- The plugin needs a local Java runtime and Graphviz to render; if it reports either missing, install a JDK and Graphviz, then restart WebStorm.
- As a fallback, paste the diagram's content into the [PlantUML web server](https://www.plantuml.com/plantuml/uml/) to render it in a browser.

### Free JetBrains license for students

WebStorm is free for students through the [JetBrains Student Pack](https://www.jetbrains.com/community/education/#students): apply with a school email address, or upload proof of enrollment if your school email isn't recognized. Approval usually takes a few minutes.

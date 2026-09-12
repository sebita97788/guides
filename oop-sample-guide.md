# OOP Sample Guide

## Table of Contents

- [Project Setup](#project-setup)
- [(US001) Register a Supplier](#register-a-supplier-us001)
- [(US002) Create a Purchase Order](#create-a-purchase-order-us002)
- [(US003) Add Items to a Purchase Order](#add-items-to-a-purchase-order-us003)
- [(US004) Calculate Purchase Order Item Subtotal](#calculate-purchase-order-item-subtotal-us004)
- [(US005) Calculate Purchase Order Total](#calculate-purchase-order-total-us005)
- [Add a Console Presentation Layer](#add-a-console-presentation-layer)
- [Prepare the First Release](#prepare-the-first-release)
- [Release](#release)
- [(US006) Merge Duplicate Items in a Purchase Order](#merge-duplicate-items-in-a-purchase-order-us006)
- [Release](#release-1)
- [Testing (optional, explore on your own)](#testing-optional-explore-on-your-own)
- [Appendix](#appendix)
  - [Continuing on another computer](#continuing-on-another-computer)
  - [Signing in to GitHub with a token](#signing-in-to-github-with-a-token)
  - [Backing up unfinished work](#backing-up-unfinished-work)
  - [Feature Finish and pull requests](#feature-finish-and-pull-requests)
  - [Removing a stray .git folder](#removing-a-stray-git-folder)
  - [Creating the repo without the GitHub CLI](#creating-the-repo-without-the-github-cli)
  - [If the class diagram doesn't render](#if-the-class-diagram-doesnt-render)
  - [Free JetBrains license for students](#free-jetbrains-license-for-students)

## Project Setup

1. **Open Rider and create a new solution.**
   - Solution already open: `File` → `New Solution...`
   - On the Welcome screen (no solution open yet): click **New Solution**, or `File` → `New Solution...` if that screen shows a `File` menu

   In the wizard's sidebar, pick **Project Type: Console**, then fill in:
   - Solution name: `oop-sample`
   - Project name: `Acme.OOProgramming`
   - Solution directory: any local path you prefer, no need to match a specific folder
   - **Create Git repository**: leave it unchecked (`git init -b main` is done by hand in step 6)
   - Target framework: `net10.0`
   - Click **Create**

   **Note:** every IDE action in this guide comes with a menu path and a keyboard shortcut, and the shortcuts are the ones from Rider's **IntelliJ** keymap. Rider ships with a Visual Studio keymap by default, so switch it once: open `Settings` (`Rider → Settings` on macOS, `File → Settings` on Windows) → `Keymap` and pick **`IntelliJ`** from the dropdown at the top (on a Mac it may read `IntelliJ (macOS)`), not `Visual Studio`, `ReSharper`, `VS Code`, or another scheme. If a shortcut ever does something unexpected, use the menu path instead. On macOS, function-key shortcuts (`F6` and similar) may need `Fn` held down, or *Use F1, F2, etc. keys as standard function keys* turned on in System Settings → Keyboard.

2. **Set the project properties.** Solution Explorer defaults to **Solution** view, which doesn't show the `.csproj`. Switch it: the dropdown at the top of the Solution Explorer panel → **File System**. Open `Acme.OOProgramming.csproj`, confirm `<ImplicitUsings>enable</ImplicitUsings>` and `<Nullable>enable</Nullable>` are set (the wizard adds them), and add `<Version>0.1.0-preview</Version>` in the same `<PropertyGroup>`. Leave the dropdown on **File System** view; the rest of Project Setup stays there.

   **Note:** the dropdown at the top of Solution Explorer switches between two layouts:
   - **File System** view mirrors the folders on disk. Use it to add plain files: anything under `docs/`, `.gitignore`, `README.md`, `LICENSE.md`, `CHANGELOG.md`. Solution view's `Add` dialog won't take a folder path or a leading dot.
   - **Solution** view shows the logical project. Use it to add C# types (`Add` → `Class/Interface`) and projects.

   Each step says which view it needs.

   **Note:** `Nullable` is what makes `string?` mean something later (Feature 1's `Address.StateOrRegion`, the project's only optional field). `ImplicitUsings` is why the guide's code never needs an explicit `using System;`.

   **Note:** a `-preview` (or `-alpha` / `-beta` / `-rc`) suffix is NuGet's way of marking a version "prerelease", the closest .NET equivalent to Maven's `-SNAPSHOT`. Starting below `1.0.0` signals early development: the code's structure and behavior can still change freely from one version to the next. `1.0.0` is reserved for the first release meant to stay stable. `## Release` later walks through exactly that cycle.

3. **Create `docs/user-stories.md`.** Right-click the project root → `Add` → `File` → type `docs/user-stories.md` → Enter.

   **Tip:** typing the `docs/` prefix creates that folder too.

   <details>
   <summary>docs/user-stories.md</summary>

   ```markdown
   # User Stories

   **Author**: Web Applications Developer Team  
   **License**: See [LICENSE.md](../LICENSE.md) for details.

   ## US001: Register a Supplier
   As a procurement manager, I want to register a supplier with its identifier, name, and address so that I can reference it when creating purchase orders.

   ### Scenario: Successfully register a supplier
   - **Given** a supplier code "SUP001", name "Supplier Inc.", and address "Supplier St, 123, SupplierCity, SC, 12345, United States"
   - **When** the procurement manager registers the supplier
   - **Then** the supplier is created with the correct identifier, name, and address

   ### Scenario: Invalid supplier name
   - **Given** a valid supplier code and a valid address
   - **When** the procurement manager attempts to register a supplier with a missing or blank name
   - **Then** the system rejects the request with an error

   ### Scenario: Invalid supplier address
   - **Given** a valid supplier code and a valid name
   - **When** the procurement manager attempts to register a supplier with a missing address
   - **Then** the system rejects the request with an error

   ## US002: Create a Purchase Order
   As a procurement manager, I want to create a purchase order for a supplier so that I can order goods.

   ### Scenario: Successfully create a purchase order
   - **Given** a supplier with code "SUP001", name "Supplier Inc.", and address "Supplier St, 123, SupplierCity, SC, 12345, United States"
   - **When** the procurement manager creates a purchase order with order number "PO001" for supplier ID "SUP001" on March 29, 2025, in USD
   - **Then** the purchase order is created with the correct order number, supplier ID, date, and currency

   ### Scenario: Invalid order number
   - **Given** a supplier ID "SUP001", a valid date, and a valid currency
   - **When** the procurement manager attempts to create a purchase order with a missing order number
   - **Then** the system rejects the request with an error

   ### Scenario: Invalid supplier
   - **Given** an order number "PO001", a valid date, and a valid currency
   - **When** the procurement manager attempts to create a purchase order with a missing supplier ID
   - **Then** the system rejects the request with an error

   ### Scenario: Invalid currency
   - **Given** an order number "PO001", a valid supplier ID, and a valid date
   - **When** the procurement manager attempts to create a purchase order with a currency that isn't a valid 3-letter code
   - **Then** the system rejects the request with an error

   ## US003: Add Items to Purchase Order
   As a procurement manager, I want to add items to a purchase order so that I can specify what to order.

   ### Scenario: Successfully add an item
   - **Given** a purchase order "PO001" for supplier ID "SUP001" in USD
   - **When** the procurement manager adds an item with a newly generated product ID (Guid), quantity 10, and unit price amount 15.99
   - **Then** the purchase order internally creates and contains the item with the correct product ID, quantity, and unit price of $15.99 USD

   ### Scenario: Invalid product ID
   - **Given** a purchase order "PO001" for supplier ID "SUP001" in USD
   - **When** the procurement manager attempts to add an item with a missing product ID
   - **Then** the system rejects the request with an error

   ### Scenario: Invalid quantity
   - **Given** a purchase order "PO001" for supplier ID "SUP001" in USD
   - **When** the procurement manager attempts to add an item with a zero or negative quantity
   - **Then** the system rejects the request with an error

   ### Scenario: Invalid unit price
   - **Given** a purchase order "PO001" for supplier ID "SUP001" in USD
   - **When** the procurement manager attempts to add an item with a negative unit price amount
   - **Then** the system rejects the request with an error

   ## US004: Calculate Purchase Order Item Subtotal
   As a procurement manager, I want to calculate the subtotal of a purchase order item so that I can verify its cost.

   ### Scenario: Successfully calculate item subtotal
   - **Given** a purchase order "PO001" with an item having a product ID, quantity 10, and unit price amount 25.99 in USD
   - **When** the procurement manager requests the subtotal for the item
   - **Then** the subtotal is calculated as $259.90 USD

   ## US005: Calculate Purchase Order Total
   As a procurement manager, I want to calculate the total cost of a purchase order so that I know the overall expense.

   ### Scenario: Successfully calculate total
   - **Given** a purchase order "PO001" with an item having a product ID, quantity 10, and unit price amount 25.99 in USD
   - **When** the procurement manager requests the total
   - **Then** the total is calculated as $259.90 USD
   ```
   </details>

4. **Look at the architecture before writing any code.**

   First, install the **plantuml4idea** plugin so the diagram renders:
   - macOS: `Rider → Settings → Plugins → Marketplace → search "plantuml4idea"` → `Install`
   - Windows: `File → Settings → Plugins → Marketplace → search "plantuml4idea"` → `Install`

   Then create the file: right-click the project root → `Add` → `File` → type `docs/class-diagram.puml` → Enter. Rider shows a rendered preview beside the source. This diagram maps:
   - The two bounded contexts (`SupplyChain`, `Procurement`) and the shared kernel between them
   - Each context's aggregate roots, entities, and value objects
   - How a reference crosses from one context into another without either one depending on the other's internals directly (context mapping)

   <details>
   <summary>docs/class-diagram.puml</summary>

   ```
   @startuml classDiagram
   package "Acme.OOProgramming.Shared" {
   class "Money" <<Value Object>> {
   +Amount : decimal
   +Currency : Currency
   --
   +Add(other)
   +Multiply(factor)
   }

   class "Currency" <<Value Object>> {
   +Code : string
   }

   class "Address" <<Value Object>> {
   +Street : string
   +Number : string
   +City : string
   +StateOrRegion : string?
   +PostalCode : string
   +Country : string
   }
   }

   package "Acme.OOProgramming.SupplyChain" as supplyChain {
   class "SupplierId" <<Value Object>> {
   +Identifier : string
   }

   class "Supplier" <<Aggregate Root>> {
   +Id : SupplierId
   +Name : string
   +Address : Address
   }
   }
   package "Acme.OOProgramming.Procurement" as procurement {
   class "SupplierId" <<Value Object>> {
   +Identifier : string
   }

   class "PurchaseOrder" <<Aggregate Root>> {
   +OrderNumber : string
   +SupplierId : SupplierId
   +OrderDate : DateOnly
   +Currency : Currency
   +Items : IReadOnlyList<PurchaseOrderItem>
   --
   +AddItem(productId, quantity, unitPriceAmount)
   +CalculateTotal()
   }

   class "PurchaseOrderItem" <<Entity>> {
   +ProductId : ProductId
   +Quantity : int
   +UnitPrice : Money
   --
   +CalculateItemTotal()
   }

   class "ProductId" <<Value Object>> {
   +Id : Guid
   }
   }
   Supplier o--> "1" supplyChain.SupplierId
   Supplier o--> "1" Address
   PurchaseOrder o--> "1" procurement.SupplierId
   PurchaseOrder *--> "many" PurchaseOrderItem : manages
   PurchaseOrderItem o--> "1" Money
   PurchaseOrderItem o--> "1" ProductId
   PurchaseOrder o--> "1" Money
   Money o--> "1" Currency
   @enduml
   ```
   </details>

   **Note:** you're implementing a given architecture, not designing one. The client (here, this course) sets DDD and this bounded-context split as part of the **Definition of Done**, not something negotiated project by project. Reading a given architecture correctly and implementing it well is a skill just as real as designing one from scratch.

   **Tip:** if it shows an error instead of a diagram, see [Appendix: If the class diagram doesn't render](#if-the-class-diagram-doesnt-render).

   **Note:** `SupplyChain` and `Procurement` each have their own `SupplierId` on the diagram, deliberately. Each context owns the identity type of the aggregate it holds (`SupplierId` belongs to SupplyChain, home of `Supplier`); no other context references it directly, and Procurement defines its own. That's **Context Mapping** in practice, not an accident. More on why in `## Release` later.

   **Note:** everything on this diagram gets built feature by feature from here on, including parts (`Currency`, `PurchaseOrder.OrderDate` as a `DateOnly`) that only become code later in the guide.

5. **Create `.gitignore`.** Still in **File System** view: right-click the project root → `Add` → `File` → type `.gitignore` → Enter, then paste the block below. If Rider already left a `.gitignore` at the solution root, open that one and replace its contents instead. Solution view won't create a dotfile, which is one reason Project Setup stays in File System view.

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

6. **Enable Git and make the first commit.** Open Rider's **Terminal** tool window (bottom toolbar); it opens at the solution root (`oop-sample/`, the folder with `oop-sample.sln`) by default.

   **Note:** check the terminal prompt is at the solution root, not inside `Acme.OOProgramming/`, before you run anything below. `git init` acts on the current folder, so from a subfolder the repo lands in the wrong place.

   ```
   git init -b main
   git config user.name "Your Name"
   git config user.email "your.email@example.com"
   git add .
   git commit -m "chore: initial commit."
   ```

   **Note:** ran it from a subfolder by mistake? See [Appendix: Removing a stray .git folder](#removing-a-stray-git-folder).

   **Note:**
   - `git init` at the solution root tracks the whole solution, not just one project inside it. Every terminal block in this guide reuses this same session, so they all stay at the solution root.
   - `-b main` (short for `--initial-branch`) names the first branch `main`. It needs to be `main` here to match what **Git Flow Helper** (installed shortly) expects.
   - `git config` without `--global` scopes this to just this repo.

7. **Connect to GitHub.**

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
     It should print a version number. If you get "command not found", close and reopen the terminal (the installer only updates the PATH for new sessions).
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

     It then shows a one-time code (like `3155-2B43`) and waits at `Press Enter to open https://github.com/login/device in your browser...`. Copy the code, press Enter, then paste it into the page that opens and click **Authorize**.

     Answering `Yes` to the third prompt also configures git, so `git push` won't ask for a username or password later.

   **Create your own GitHub organization first.** Everything below pushes to a GitHub organization that is yours, never the course's.
   - If you don't have one, go to [github.com/organizations/plan](https://github.com/organizations/plan), pick the **Free** plan, and choose an account name for it.
   - That account name is your `<org>` in the commands below: if the organization is `acme-labs`, the repo ends up at `github.com/acme-labs/oop-sample`.

   **Create the private repo and push.** One command with the GitHub CLI. Replace `<org>` with your organization's name, no angle brackets (`<org>/oop-sample` becomes for example `acme-labs/oop-sample`).

   **Note:** before running it, check the terminal is at the solution root (`oop-sample/`, the folder with `oop-sample.sln`), not inside `Acme.OOProgramming/`. `--source=.` and `git init` act on the current folder, so from a subfolder the repo lands in the wrong place.

   ```
   gh repo create <org>/oop-sample --private --source=. --remote=origin --push --description "Console application demonstrating object-oriented programming (OOP) and domain-driven design (DDD) principles within the context of SupplyChain and Procurement domains."
   ```
   It creates the private repo in your org, adds it as `origin`, pushes `main`, and sets the About text.

   **Note:** `--source=.` uses the current folder; `--remote=origin --push` adds the remote and pushes `main`; `--description` fills the About text, GitHub's own repo-level summary shown on the repo page and in org/search listings, separate from `README.md`.

   **Note:** ran it from a subfolder by mistake? See [Appendix: Removing a stray .git folder](#removing-a-stray-git-folder).

   **Note:** no GitHub CLI? Create the repo on the website and push by hand: see [Appendix: Creating the repo without the GitHub CLI](#creating-the-repo-without-the-github-cli).

8. **Install the Git Flow Helper plugin.**
   - macOS: `Rider → Settings → Plugins → Marketplace → search "Git Flow Helper"`
   - Windows: `File → Settings → Plugins → Marketplace → search "Git Flow Helper"`

9. **Initialize Git Flow.**

   Git Flow Helper pushes through Rider's own GitHub connection, not the terminal's. Register **your** account there first, and make sure it's the only one.

   - Get your token, copy what it prints:
     ```
     gh auth token
     ```
     This is the same token your terminal git already uses.
   - Open `Settings` → `Version Control` → `GitHub` (macOS: `Rider → Settings`; Windows: `File → Settings`).
   - If any account is already listed (a shared machine may still have someone else's), select each one and click **`−`** to remove it. The list must be empty before you add yours.
   - Click **`+`** → **`Log In with Token...`** (not `Log In via GitHub...`, whose browser sign-in produces an OAuth token your organization blocks for third-party apps) → paste the `gh auth token` value → **`Add Account`**. Your account appears in the list; close `Settings`.
   - Click the Git Flow Helper widget in the status bar → `Init`.
   - The branch prefix fields (`Main`, `Develop`, `Feature`, `Release`, `Hotfix`) are pre-filled with sensible defaults; click `OK`.

   This creates a `develop` branch from `main` and pushes it to `origin` through the account you just added.

   **Note:** from here on, `main` is only touched through a Release or Hotfix, never worked on directly.

   **Tip:** the current branch name should show in the status bar (bottom-right, branch icon + name). If nothing shows, it's disabled by default: right-click an empty area of the status bar → check `Git Branch` in the widget list.

## Register a Supplier ([US001](./user-stories.md))

1. **Start the feature.** Git Flow Helper widget in the status bar → `Feature` → `Feature Start` → **Feature description** `register-supplier` → `OK`. Creates and switches you to `feature/register-supplier`.

   **Tip:** need to stop before the feature is done? See [Appendix: Backing up unfinished work](#backing-up-unfinished-work).

2. **Create the `Supplier` aggregate (properties only for now).** Switch the Solution Explorer dropdown to **Solution** view (Project Setup left it on **File System** view); it stays on **Solution** view through the user stories. Re-read US001's `Scenario: Successfully register a supplier`, then right-click the project root → `Add` → `Class/Interface` → type `SupplyChain/Domain/Model/Aggregates/Supplier` in the **Name** field → Enter.

   Write only the three properties:
   - `Id` (`SupplierId`), `Name` (`string`), `Address` (`Address`), all `get;` only: assignable in the constructor and never again, this project's equivalent of Java's `final`

   **Note:** the properties carry no validation. `Supplier` is an aggregate root, so its creation invariant is enforced in the constructor a few steps from now, not in the properties: the constructor is the aggregate's single entry point, and the only place a rule spanning more than one field could ever go. Its value objects (`SupplierId`, `Address`) still validate themselves. This split, value objects validate in their `init` accessor, aggregate roots in the constructor, is a deliberate decision, written up in `## Release` as ADR-0011.

   **Tip:** if Rider pops up an "Add File to Git" dialog, check `Don't ask again` and click `Cancel`. This guide stages through explicit `git add` / `git commit`.

   **Tip:** `SupplierId` and `Address` don't exist yet, so they show red with no `using`. That's expected: you build both in the next steps, then come back here. Rider adds the `using` once the type exists, or `Option+Enter` (macOS) / `Alt+Enter` (Windows) on the red name.

   <details>
   <summary>Supplier.cs (properties only)</summary>

   ```csharp
   namespace Acme.OOProgramming.SupplyChain.Domain.Model.Aggregates;

   public class Supplier
   {
       public SupplierId Id { get; }
       public string Name { get; }
       public Address Address { get; }
   }
   ```
   </details>

3. **Create `SupplierId`.** A `readonly record struct` wrapping a single `Identifier` string: its `init` accessor rejects null or blank (C# 13 `field` keyword) and its `get` returns `field ?? string.Empty`. Right-click the project root → `Add` → `Class/Interface` → `SupplyChain/Domain/Model/ValueObjects/SupplierId` → select `Record Struct` → Enter.

   **Tip:** you can also create it from the red underline in `Supplier.cs`: `Option+Enter` (macOS) / `Alt+Enter` (Windows) → create the type inline → `F6` → `Move To Folder` → `SupplyChain/Domain/Model/ValueObjects`.

   <details>
   <summary>SupplierId.cs (so far)</summary>

   ```csharp
   namespace Acme.OOProgramming.SupplyChain.Domain.Model.ValueObjects;

   public readonly record struct SupplierId
   {
       public string Identifier
       {
           get => field ?? string.Empty;
           init
           {
               ArgumentException.ThrowIfNullOrWhiteSpace(value);
               field = value;
           }
       }
   }
   ```
   </details>

   **Note:** `readonly record struct` = a small immutable value. `record` gives value-based equality (two `SupplierId`s with the same `Identifier` are equal); `struct` + `readonly` make it behave like a number (copied when passed, never `null`, no heap object). It's a `struct`, not a `class`, because a `SupplierId` is a value, not a thing with its own identity.

   **Note:** a `struct` can't be `null`, but it can be `default` (all-zero fields), and `default(SupplierId)` never runs your `init` validation. `get => field ?? string.Empty` makes a stray `default` read back a safe empty string instead of `null`; the next step blocks the parameterless constructor so `new SupplierId()` throws too.

4. **Add `SupplierId`'s constructors and `ToString()`.** The blocked parameterless constructor (so `new SupplierId()` throws instead of producing an unvalidated `default`), a constructor taking the raw string (`Supplier` and `Program.cs` call `new SupplierId("...")`), and `ToString()` returning the identifier. That completes `SupplierId`. The full file below includes its XML docs.

   <details>
   <summary>SupplierId.cs (addition: constructors and ToString)</summary>

   ```csharp
   public SupplierId() => throw new InvalidOperationException("SupplierId must be initialized with a non-empty identifier.");

   public SupplierId(string identifier) => Identifier = identifier;

   public override string ToString() => Identifier;
   ```
   </details>

   <details>
   <summary>SupplierId.cs</summary>

   ```csharp
   namespace Acme.OOProgramming.SupplyChain.Domain.Model.ValueObjects;

   /// <summary>
   /// Represents a supplier identifier value object in the SupplyChain bounded context.
   /// Other bounded contexts never reference this type directly: each one models its own
   /// reference to a supplier independently, even when it refers to the same real-world supplier.
   /// </summary>
   public readonly record struct SupplierId
   {
       /// <summary>
       /// The string identifier value.
       /// </summary>
       /// <exception cref="ArgumentException">Thrown when the value is null or white space.</exception>
       public string Identifier
       {
           get => field ?? string.Empty;
           init
           {
               ArgumentException.ThrowIfNullOrWhiteSpace(value);
               field = value;
           }
       }

       /// <summary>
       /// Prevents parameterless construction of <see cref="SupplierId"/>.
       /// </summary>
       /// <exception cref="InvalidOperationException">Always thrown because an identifier is required.</exception>
       public SupplierId() => throw new InvalidOperationException("SupplierId must be initialized with a non-empty identifier.");

       /// <summary>
       /// Creates a new instance of <see cref="SupplierId"/>.
       /// </summary>
       /// <param name="identifier">The unique identifier for the supplier.</param>
       /// <exception cref="ArgumentException">Thrown when the identifier is null or empty.</exception>
       public SupplierId(string identifier) => Identifier = identifier;

       /// <summary>
       /// Returns a string representation of the supplier identifier.
       /// </summary>
       /// <returns>A string representation of the supplier identifier.</returns>
       public override string ToString() => Identifier;
   }
   ```
   </details>

5. **Create `Address` (minimal).** Same shape, six properties. Right-click the project root → `Add` → `Class/Interface` → `Shared/Domain/Model/ValueObjects/Address` → select `Record Struct` → Enter.

   <details>
   <summary>Address.cs (minimal)</summary>

   ```csharp
   namespace Acme.OOProgramming.Shared.Domain.Model.ValueObjects;

   public readonly record struct Address
   {
       public string Street { get; init; }
       public string Number { get; init; }
       public string City { get; init; }
       public string? StateOrRegion { get; init; }
       public string PostalCode { get; init; }
       public string Country { get; init; }
   }
   ```
   </details>

6. **Add `Address`'s validation.** Replace the five required properties from step 5 (`Street`, `Number`, `City`, `PostalCode`, `Country`) with versions that validate in their `init` accessor (not null/blank, under a length limit) and expose a `get` returning `field ?? string.Empty`, and add the five length limits as named `const`s. `StateOrRegion` is left as it is.

   <details>
   <summary>Address.cs (the five required properties, with validation)</summary>

   ```csharp
   private const int MaxStreetLength = 100;
   private const int MaxNumberLength = 10;
   private const int MaxCityLength = 100;
   private const int MaxPostalCodeLength = 20;
   private const int MaxCountryLength = 100;

   public string Street
   {
       get => field ?? string.Empty;
       init
       {
           ArgumentException.ThrowIfNullOrWhiteSpace(value);
           if (value.Length > MaxStreetLength)
               throw new ArgumentException($"Street cannot exceed {MaxStreetLength} characters.", nameof(value));
           field = value;
       }
   }

   public string Number
   {
       get => field ?? string.Empty;
       init
       {
           ArgumentException.ThrowIfNullOrWhiteSpace(value);
           if (value.Length > MaxNumberLength)
               throw new ArgumentException($"Number cannot exceed {MaxNumberLength} characters.", nameof(value));
           field = value;
       }
   }

   public string City
   {
       get => field ?? string.Empty;
       init
       {
           ArgumentException.ThrowIfNullOrWhiteSpace(value);
           if (value.Length > MaxCityLength)
               throw new ArgumentException($"City cannot exceed {MaxCityLength} characters.", nameof(value));
           field = value;
       }
   }

   public string PostalCode
   {
       get => field ?? string.Empty;
       init
       {
           ArgumentException.ThrowIfNullOrWhiteSpace(value);
           if (value.Length > MaxPostalCodeLength)
               throw new ArgumentException($"Postal code cannot exceed {MaxPostalCodeLength} characters.", nameof(value));
           field = value;
       }
   }

   public string Country
   {
       get => field ?? string.Empty;
       init
       {
           ArgumentException.ThrowIfNullOrWhiteSpace(value);
           if (value.Length > MaxCountryLength)
               throw new ArgumentException($"Country cannot exceed {MaxCountryLength} characters.", nameof(value));
           field = value;
       }
   }
   ```
   </details>

   **Note:** these guards are written `if (cond) throw ...;` with no braces, on purpose for this track. The body is always a single `throw`, so any line mistakenly added after it is unreachable and the compiler flags it, unlike a brace-less `if` guarding an assignment. Checks the framework already covers use `ArgumentException.ThrowIf*` and need no `if` at all. Braces only come back where a check runs more than one statement, `PurchaseOrder.AddItem`'s merge branch, later.

   **Note:** `StateOrRegion` is the exception: it stays `public string? StateOrRegion { get; init; }`, since `null` is already valid for an optional field, no `default`-bypass gap to close.

   **Note:** the five length limits are named `const`s, not magic numbers repeated inline: the constant makes each limit's meaning obvious and keeps the guard and its error message from drifting apart.

7. **Add `Address`'s constructors and `ToString()`.** The blocked parameterless constructor, a six-parameter constructor, and a `ToString()` that skips `StateOrRegion` when it's blank. That completes `Address`. The full file below includes its XML docs.

   <details>
   <summary>Address.cs (addition: constructors and ToString)</summary>

   ```csharp
   public Address() => throw new InvalidOperationException("Address must be initialized with street, number, city, postal code, and country.");

   public Address(string street, string number, string city, string? stateOrRegion, string postalCode, string country)
   {
       Street = street;
       Number = number;
       City = city;
       StateOrRegion = stateOrRegion;
       PostalCode = postalCode;
       Country = country;
   }

   public override string ToString() => string.IsNullOrWhiteSpace(StateOrRegion)
       ? $"{Street}, {Number}, {City}, {PostalCode}, {Country}"
       : $"{Street}, {Number}, {City}, {StateOrRegion}, {PostalCode}, {Country}";
   ```
   </details>

   <details>
   <summary>Address.cs</summary>

   ```csharp
   namespace Acme.OOProgramming.Shared.Domain.Model.ValueObjects;

   /// <summary>
   /// Represents an international physical address value object.
   /// </summary>
   public readonly record struct Address
   {
       private const int MaxStreetLength = 100;
       private const int MaxNumberLength = 10;
       private const int MaxCityLength = 100;
       private const int MaxPostalCodeLength = 20;
       private const int MaxCountryLength = 100;

       /// <summary>
       /// The street address.
       /// </summary>
       /// <exception cref="ArgumentException">Thrown when the street is null, blank, or exceeds <see cref="MaxStreetLength"/> characters.</exception>
       public string Street
       {
           get => field ?? string.Empty;
           init
           {
               ArgumentException.ThrowIfNullOrWhiteSpace(value);
               if (value.Length > MaxStreetLength)
                   throw new ArgumentException($"Street cannot exceed {MaxStreetLength} characters.", nameof(value));
               field = value;
           }
       }

       /// <summary>
       /// The street address number.
       /// </summary>
       /// <exception cref="ArgumentException">Thrown when the number is null, blank, or exceeds <see cref="MaxNumberLength"/> characters.</exception>
       public string Number
       {
           get => field ?? string.Empty;
           init
           {
               ArgumentException.ThrowIfNullOrWhiteSpace(value);
               if (value.Length > MaxNumberLength)
                   throw new ArgumentException($"Number cannot exceed {MaxNumberLength} characters.", nameof(value));
               field = value;
           }
       }

       /// <summary>
       /// The city.
       /// </summary>
       /// <exception cref="ArgumentException">Thrown when the city is null, blank, or exceeds <see cref="MaxCityLength"/> characters.</exception>
       public string City
       {
           get => field ?? string.Empty;
           init
           {
               ArgumentException.ThrowIfNullOrWhiteSpace(value);
               if (value.Length > MaxCityLength)
                   throw new ArgumentException($"City cannot exceed {MaxCityLength} characters.", nameof(value));
               field = value;
           }
       }

       /// <summary>
       /// The state or region.
       /// </summary>
       public string? StateOrRegion { get; init; }

       /// <summary>
       /// The postal code.
       /// </summary>
       /// <exception cref="ArgumentException">Thrown when the postal code is null, blank, or exceeds <see cref="MaxPostalCodeLength"/> characters.</exception>
       public string PostalCode
       {
           get => field ?? string.Empty;
           init
           {
               ArgumentException.ThrowIfNullOrWhiteSpace(value);
               if (value.Length > MaxPostalCodeLength)
                   throw new ArgumentException($"Postal code cannot exceed {MaxPostalCodeLength} characters.", nameof(value));
               field = value;
           }
       }

       /// <summary>
       /// The country.
       /// </summary>
       /// <exception cref="ArgumentException">Thrown when the country is null, blank, or exceeds <see cref="MaxCountryLength"/> characters.</exception>
       public string Country
       {
           get => field ?? string.Empty;
           init
           {
               ArgumentException.ThrowIfNullOrWhiteSpace(value);
               if (value.Length > MaxCountryLength)
                   throw new ArgumentException($"Country cannot exceed {MaxCountryLength} characters.", nameof(value));
               field = value;
           }
       }

       /// <summary>
       /// Prevents parameterless construction of <see cref="Address"/>.
       /// </summary>
       /// <exception cref="InvalidOperationException">Always thrown because address components are required.</exception>
       public Address() => throw new InvalidOperationException("Address must be initialized with street, number, city, postal code, and country.");

       /// <summary>
       /// Creates a new instance of <see cref="Address"/>.
       /// </summary>
       /// <param name="street">The address street, which must not be null, blank, or exceed 100 characters.</param>
       /// <param name="number">The address number, which must not be null, blank, or exceed 10 characters.</param>
       /// <param name="city">The address city, which must not be null, blank, or exceed 100 characters.</param>
       /// <param name="stateOrRegion">The address state or region, which can be null.</param>
       /// <param name="postalCode">The address postal code, which must not be null, blank, or exceed 20 characters.</param>
       /// <param name="country">The address country, which must not be null, blank, or exceed 100 characters.</param>
       public Address(string street, string number, string city, string? stateOrRegion, string postalCode, string country)
       {
           Street = street;
           Number = number;
           City = city;
           StateOrRegion = stateOrRegion;
           PostalCode = postalCode;
           Country = country;
       }

       /// <summary>
       /// Returns a string representation of the address.
       /// </summary>
       /// <returns>A string representation of the address, which may include the state or region if present.</returns>
       public override string ToString() => string.IsNullOrWhiteSpace(StateOrRegion)
           ? $"{Street}, {Number}, {City}, {PostalCode}, {Country}"
           : $"{Street}, {Number}, {City}, {StateOrRegion}, {PostalCode}, {Country}";
   }
   ```
   </details>

8. **Add `Supplier`'s full constructor (happy path, no validation yet).** `Supplier(SupplierId id, string name, Address address)`: assigns the three properties directly. The value objects are in different namespaces than `Supplier`, so Rider underlines them red: cursor on each → `Option+Enter` (macOS) / `Alt+Enter` (Windows) → `using Acme.OOProgramming...;`.

   <details>
   <summary>Supplier.cs (addition: full constructor)</summary>

   ```csharp
   public Supplier(SupplierId id, string name, Address address)
   {
       Id = id;
       Name = name;
       Address = address;
   }
   ```
   </details>

   **Note:** same `using`-fixing mechanism every time a new file references a type from another namespace, for the rest of the guide.

9. **Add `Supplier`'s convenience constructor.** `Supplier(string identifier, string name, Address address)`: wraps a raw string into a `SupplierId` and delegates to the full one. That's what `Program.cs` uses shortly, a raw string is what a caller has on hand, not a `SupplierId` instance.

   **Tip:** `new Supplier(new SupplierId("SUP001"), "Supplier Inc.", address)` now satisfies `Scenario: Successfully register a supplier`, even though invalid input isn't rejected yet.

   <details>
   <summary>Supplier.cs (addition: convenience constructor)</summary>

   ```csharp
   public Supplier(string identifier, string name, Address address)
       : this(new SupplierId(identifier), name, address)
   {
   }
   ```
   </details>

10. **Add `Supplier`'s validation guards.** Re-read `Scenario: Invalid supplier name` and `Scenario: Invalid supplier address`. Replace the full constructor from step 8 with the version below: three guards above the assignments. `Name` is a bare `string`, so it gets `ThrowIfNullOrWhiteSpace`. `SupplierId` and `Address` are self-validating value objects, so the aggregate only adds `== default`: a `struct` is never `null`, but a caller can still pass `default(SupplierId)` / `default(Address)`, the struct's zero value, which never ran the type's own validation. The aggregate rejects that.

   **Tip:** try writing the guards yourself first. In an aggregate root they go in the constructor, the aggregate's single entry point, not in the properties.

   <details>
   <summary>Supplier.cs (constructor, with guards)</summary>

   ```csharp
   public Supplier(SupplierId id, string name, Address address)
   {
       if (id == default)
           throw new ArgumentException("Supplier ID is required.", nameof(id));
       ArgumentException.ThrowIfNullOrWhiteSpace(name);
       if (address == default)
           throw new ArgumentException("Supplier address is required.", nameof(address));

       Id = id;
       Name = name;
       Address = address;
   }
   ```
   </details>

   **Note:** the same shape recurs for every aggregate root in this guide: value objects carry their own rules, and the root's constructor adds a `== default` guard for each one it receives (`PurchaseOrder` does the same for its `SupplierId` and `Currency`). See [ADR-0005](docs/adrs.md#adr-0005-value-objects-as-readonly-record-struct).

11. **Add `Supplier`'s identity methods.** `Equals()`, `GetHashCode()`, `ToString()`.

   <details>
   <summary>Supplier.cs (addition: identity methods)</summary>

   ```csharp
   public override bool Equals(object? obj)
   {
       return obj is Supplier other && Id == other.Id;
   }

   public override int GetHashCode() => Id.GetHashCode();

   public override string ToString() => $"Supplier[Id={Id}, Name={Name}, Address={Address}]";
   ```
   </details>

   `Supplier` is complete, enforcing every scenario from US001's acceptance criteria. The full file:

   <details>
   <summary>Supplier.cs (no docs)</summary>

   ```csharp
   using Acme.OOProgramming.Shared.Domain.Model.ValueObjects;
   using Acme.OOProgramming.SupplyChain.Domain.Model.ValueObjects;

   namespace Acme.OOProgramming.SupplyChain.Domain.Model.Aggregates;

   public class Supplier
   {
       public SupplierId Id { get; }
       public string Name { get; }
       public Address Address { get; }

       public Supplier(SupplierId id, string name, Address address)
       {
           if (id == default)
               throw new ArgumentException("Supplier ID is required.", nameof(id));
           ArgumentException.ThrowIfNullOrWhiteSpace(name);
           if (address == default)
               throw new ArgumentException("Supplier address is required.", nameof(address));

           Id = id;
           Name = name;
           Address = address;
       }

       public Supplier(string identifier, string name, Address address)
           : this(new SupplierId(identifier), name, address)
       {
       }

       public override bool Equals(object? obj)
       {
           return obj is Supplier other && Id == other.Id;
       }

       public override int GetHashCode() => Id.GetHashCode();

       public override string ToString() => $"Supplier[Id={Id}, Name={Name}, Address={Address}]";
   }
   ```
   </details>

   The same file with its XML docs, the version you keep:

   <details>
   <summary>Supplier.cs</summary>

   ```csharp
   using Acme.OOProgramming.Shared.Domain.Model.ValueObjects;
   using Acme.OOProgramming.SupplyChain.Domain.Model.ValueObjects;

   namespace Acme.OOProgramming.SupplyChain.Domain.Model.Aggregates;

   /// <summary>
   /// Represents a supplier aggregate root in the Supply Chain bounded context.
   /// </summary>
   public class Supplier
   {
       /// <summary>
       /// The unique identifier for the supplier.
       /// </summary>
       public SupplierId Id { get; }

       /// <summary>
       /// The name of the supplier.
       /// </summary>
       public string Name { get; }

       /// <summary>
       /// The address of the supplier.
       /// </summary>
       public Address Address { get; }

       /// <summary>
       /// Creates a new instance of <see cref="Supplier"/>.
       /// </summary>
       /// <param name="id">The supplier identifier, which must not be the default value.</param>
       /// <param name="name">The supplier name, which must not be null or blank.</param>
       /// <param name="address">The supplier address, which must not be the default value.</param>
       /// <exception cref="ArgumentException">Thrown when the id or address is the default value, or the name is null or blank.</exception>
       public Supplier(SupplierId id, string name, Address address)
       {
           if (id == default)
               throw new ArgumentException("Supplier ID is required.", nameof(id));
           ArgumentException.ThrowIfNullOrWhiteSpace(name);
           if (address == default)
               throw new ArgumentException("Supplier address is required.", nameof(address));

           Id = id;
           Name = name;
           Address = address;
       }

       /// <summary>
       /// Creates a new instance of <see cref="Supplier"/> with a string identifier.
       /// </summary>
       /// <param name="identifier">The supplier identifier string.</param>
       /// <param name="name">The supplier name.</param>
       /// <param name="address">The supplier address.</param>
       public Supplier(string identifier, string name, Address address)
           : this(new SupplierId(identifier), name, address)
       {
       }

       /// <summary>
       /// Determines whether this <see cref="Supplier"/> is equal to another object, by identity.
       /// </summary>
       /// <param name="obj">The object to compare against.</param>
       /// <returns><see langword="true"/> if the other object is a <see cref="Supplier"/> with the same <see cref="Id"/>.</returns>
       public override bool Equals(object? obj)
       {
           return obj is Supplier other && Id == other.Id;
       }

       /// <summary>
       /// Returns a hash code based on the supplier's identity.
       /// </summary>
       /// <returns>A hash code derived from <see cref="Id"/>.</returns>
       public override int GetHashCode() => Id.GetHashCode();

       /// <summary>
       /// Returns a string representation of the supplier.
       /// </summary>
       /// <returns>A string representation of the supplier.</returns>
       public override string ToString() => $"Supplier[Id={Id}, Name={Name}, Address={Address}]";
   }
   ```
   </details>

   **Note:** `Equals()` / `GetHashCode()` compare only `Id`, not every property. An aggregate's identity is what makes two instances "the same", not their current state, unlike a `readonly record struct` (like `SupplierId` / `Address`), which gets value-based equality for free from every component. As a plain `class`, `Supplier` gets none of that automatically, so it's written by hand, comparing identity only.

   ```
   git add .
   git commit -m "feat(supplier): add supplier aggregate, supplier id and address value objects."
   ```

   **Note:** this is the first point where `Supplier` and both value objects are all real. That's the meaningful unit of work worth committing. If you didn't commit earlier, everything from step 2 lands here together.

12. **Register the supplier in `Program.cs`.** Delete the wizard's default `Console.WriteLine("Hello, World!");` first. Then create an `Address` and a `Supplier` from it, using SupplyChain's own `SupplierId`, and print the supplier's own `ToString()`.

   **Tip:** missing types (`Address`, `Supplier`, `SupplierId`) resolve with Rider's auto-import quick-fix (`Option+Enter` / `Alt+Enter`) as you type.

   <details>
   <summary>Program.cs (addition)</summary>

   ```csharp
   using Acme.OOProgramming.SupplyChain.Domain.Model.Aggregates;
   using Acme.OOProgramming.Shared.Domain.Model.ValueObjects;

   var supplierAddress = new Address("Supplier St", "123", "SupplierCity", null, "12345", "United States");
   var supplier = new Supplier(new SupplierId("SUP001"), "Supplier Inc.", supplierAddress);

   Console.WriteLine($"Registered Supplier {supplier.Id.Identifier}: {supplier}");
   ```
   </details>

   ```
   git add .
   git commit -m "feat(main): register the initial supplier."
   ```

13. **Publish and finish the feature.** Click the Git Flow Helper widget in the status bar → `Feature` → `Feature Publish`, then the widget again → `Feature` → `Feature Finish`. Merges `feature/register-supplier` into `develop` and pushes it too.

   **Note:** `Feature Publish` is the only push of `feature/register-supplier`: it pushes the branch to `origin` (setting it up there too, same as the very first push of `main` did back in Project Setup), no manual `git push -u` needed.

   **Tip:** in the `Feature Finish` dialog:
   - "What to do when finished": pick `Integrate Immediately` (not the merge-request options; this course merges features directly, no PR review step).
   - Uncheck `Keep remote branch when finished`. Leave the pre-filled commit message (`Merge branch 'feature/register-supplier' into develop`) as-is.

   **Note:** deleting the remote branch on finish keeps the repo clean, same as GitHub's own "Delete branch" prompt after merging a PR.

   **Note:** how this maps to a real pull-request workflow: see [Appendix: Feature Finish and pull requests](#feature-finish-and-pull-requests).

   From here on the later features show this step condensed as "**Publish and finish the feature.**", which always means exactly this sequence.

## Create a Purchase Order ([US002](./user-stories.md))

1. **Start the feature.** Feature description: `create-purchase-order` → `OK`. Creates and switches you to `feature/create-purchase-order`.

2. **Create the `PurchaseOrder` aggregate (properties only for now).** Re-read US002's `Scenario: Successfully create a purchase order` first. Right-click the project root → `Add` → `Class/Interface` → type `Procurement/Domain/Model/Aggregates/PurchaseOrder` → Enter (`Class` is selected by default).

   Write only the properties:
   - `OrderNumber` (`string`), `SupplierId` (`SupplierId`), `OrderDate` (`DateOnly`), `Currency` (`Currency`), all `get`-only

   **Note:**
   - All four are write-once: assigned in the constructor, never reassignable after.
   - `OrderNumber` grows a guard in the constructor a few steps from now; `SupplierId` and `Currency` validate themselves; `OrderDate` needs no guard, any date is valid.
   - `OrderDate` is a `DateOnly`, not a `DateTime`: a purchase order date is a calendar business date, nothing in this domain ever needs a time-of-day or time-zone component.

   **Tip:** `SupplierId` and `Currency` don't resolve yet, that's fine, and there's no `using` for them yet: nothing to import until they exist. You build Procurement's own `SupplierId` and the shared `Currency` in the next steps, then come back to `PurchaseOrder`.

   <details>
   <summary>PurchaseOrder.cs (properties only)</summary>

   ```csharp
   namespace Acme.OOProgramming.Procurement.Domain.Model.Aggregates;

   public class PurchaseOrder
   {
       public string OrderNumber { get; }
       public SupplierId SupplierId { get; }
       public DateOnly OrderDate { get; }
       public Currency Currency { get; }
   }
   ```
   </details>

3. **Create Procurement's own `SupplierId`.** Same shape as SupplyChain's from Feature 1: a `readonly record struct` wrapping a single `Identifier` string, validated in its `init` accessor, with a `get` returning `field ?? string.Empty`. Right-click the project root → `Add` → `Class/Interface` → type `Procurement/Domain/Model/ValueObjects/SupplierId`, **select `Record Struct`** → Enter.

   <details>
   <summary>SupplierId.cs (so far)</summary>

   ```csharp
   namespace Acme.OOProgramming.Procurement.Domain.Model.ValueObjects;

   public readonly record struct SupplierId
   {
       public string Identifier
       {
           get => field ?? string.Empty;
           init
           {
               ArgumentException.ThrowIfNullOrWhiteSpace(value);
               field = value;
           }
       }
   }
   ```
   </details>

   **Note:** this is the Context Mapping call from the Project Setup diagram showing up in code: **Procurement never imports SupplyChain's `SupplierId`**, it models its own reference to a supplier independently, even though both refer to the same real-world supplier.

4. **Add `SupplierId`'s constructors and `ToString()`.** The blocked parameterless constructor, a constructor taking the raw string (`PurchaseOrder` and `Program.cs` call `new SupplierId("...")`), and `ToString()` returning the identifier. That completes `SupplierId`. The full file below includes its XML docs.

   <details>
   <summary>SupplierId.cs (addition: constructors and ToString)</summary>

   ```csharp
   public SupplierId() => throw new InvalidOperationException("SupplierId must be initialized with a non-empty identifier.");

   public SupplierId(string identifier) => Identifier = identifier;

   public override string ToString() => Identifier;
   ```
   </details>

   <details>
   <summary>SupplierId.cs</summary>

   ```csharp
   namespace Acme.OOProgramming.Procurement.Domain.Model.ValueObjects;

   /// <summary>
   /// Represents a reference to a supplier, as understood by the Procurement bounded context.
   /// Deliberately decoupled from SupplyChain's own SupplierId: each bounded context models its own
   /// concepts independently, even when they refer to the same real-world supplier.
   /// </summary>
   public readonly record struct SupplierId
   {
       /// <summary>
       /// The string identifier value.
       /// </summary>
       /// <exception cref="ArgumentException">Thrown when the value is null or white space.</exception>
       public string Identifier
       {
           get => field ?? string.Empty;
           init
           {
               ArgumentException.ThrowIfNullOrWhiteSpace(value);
               field = value;
           }
       }

       /// <summary>
       /// Prevents parameterless construction of <see cref="SupplierId"/>.
       /// </summary>
       /// <exception cref="InvalidOperationException">Always thrown because an identifier is required.</exception>
       public SupplierId() => throw new InvalidOperationException("SupplierId must be initialized with a non-empty identifier.");

       /// <summary>
       /// Creates a new instance of <see cref="SupplierId"/>.
       /// </summary>
       /// <param name="identifier">The unique identifier for the supplier.</param>
       /// <exception cref="ArgumentException">Thrown when the identifier is null or empty.</exception>
       public SupplierId(string identifier) => Identifier = identifier;

       /// <summary>
       /// Returns a string representation of the supplier identifier.
       /// </summary>
       /// <returns>A string representation of the supplier identifier.</returns>
       public override string ToString() => Identifier;
   }
   ```
   </details>

5. **Create the `Currency` value object.** A `readonly record struct` in the shared kernel wrapping a single `Code` string: its `init` accessor rejects anything that isn't three ASCII letters and stores it upper-cased (C# 14 `field` keyword), its `get` returns `field ?? string.Empty`. The length limit is a named `const`, not a literal `3` repeated inline. Right-click the project root → `Add` → `Class/Interface` → type `Shared/Domain/Model/ValueObjects/Currency`, **select `Record Struct`** → Enter.

   <details>
   <summary>Currency.cs (so far)</summary>

   ```csharp
   namespace Acme.OOProgramming.Shared.Domain.Model.ValueObjects;

   public readonly record struct Currency
   {
       private const int CodeLength = 3;

       public string Code
       {
           get => field ?? string.Empty;
           init
           {
               ArgumentException.ThrowIfNullOrWhiteSpace(value);
               if (value.Length != CodeLength || !value.All(char.IsAsciiLetter))
                   throw new ArgumentException($"Currency must be a valid {CodeLength}-letter ISO code.", nameof(Code));
               field = value.ToUpperInvariant();
           }
       }
   }
   ```
   </details>

   **Note:** `Currency` is its own type, not a bare 3-letter `string`, for the same reason as `SupplierId` and `ProductId`: the validation rule lives in one place, and the compiler stops a caller from comparing a currency code against any other 3-character string (a product SKU, say). `PurchaseOrder` uses it now; `Money` uses it in Feature 3.

6. **Add `Currency`'s constructors and `ToString()`.** The blocked parameterless constructor, a constructor taking the raw code, and `ToString()` returning it. That completes `Currency`. The full file below includes its XML docs.

   <details>
   <summary>Currency.cs (addition: constructors and ToString)</summary>

   ```csharp
   public Currency() => throw new InvalidOperationException("Currency must be initialized with a valid 3-letter code.");

   public Currency(string code) => Code = code;

   public override string ToString() => Code;
   ```
   </details>

   <details>
   <summary>Currency.cs</summary>

   ```csharp
   namespace Acme.OOProgramming.Shared.Domain.Model.ValueObjects;

   /// <summary>
   /// Represents a currency value object: a validated ISO 4217 alphabetic code.
   /// </summary>
   public readonly record struct Currency
   {
       private const int CodeLength = 3;

       /// <summary>
       /// The currency code.
       /// </summary>
       /// <exception cref="ArgumentException">Thrown when the currency code is null, empty, or not a valid 3-letter ISO code.</exception>
       public string Code
       {
           get => field ?? string.Empty;
           init
           {
               ArgumentException.ThrowIfNullOrWhiteSpace(value);
               if (value.Length != CodeLength || !value.All(char.IsAsciiLetter))
                   throw new ArgumentException($"Currency must be a valid {CodeLength}-letter ISO code.", nameof(Code));
               field = value.ToUpperInvariant();
           }
       }

       /// <summary>
       /// Prevents parameterless construction of <see cref="Currency"/>.
       /// </summary>
       /// <exception cref="InvalidOperationException">Always thrown because a currency code is required.</exception>
       public Currency() => throw new InvalidOperationException("Currency must be initialized with a valid 3-letter code.");

       /// <summary>
       /// Creates a new instance of <see cref="Currency"/>.
       /// </summary>
       /// <param name="code">The currency code.</param>
       /// <exception cref="ArgumentException">Thrown when the currency code is null, empty, or not a valid 3-letter ISO code.</exception>
       public Currency(string code) => Code = code;

       /// <summary>
       /// Returns a string representation of the currency code.
       /// </summary>
       /// <returns>A string representation of the currency code.</returns>
       public override string ToString() => Code;
   }
   ```
   </details>

7. **Add `PurchaseOrder`'s constructor (happy path, no validation yet).** The canonical constructor `PurchaseOrder(string orderNumber, SupplierId supplierId, DateOnly orderDate, Currency currency)` assigns the four properties directly. Add two convenience overloads next to it, each delegating to the canonical one with `: this(...)`: a caller at the edge usually has a raw `"USD"` string and a `DateTime` on hand, not a `Currency` and a `DateOnly`. This assigns the `get`-only properties, so `PurchaseOrder` compiles from here on.

   **Tip:** `SupplierId` and `Currency` now resolve, but Rider still needs the `using`s: `Option+Enter` (macOS) / `Alt+Enter` (Windows) on each red name. For `SupplierId`, pick `Acme.OOProgramming.Procurement.Domain.Model.ValueObjects`, Procurement's own type, not SupplyChain's.

   <details>
   <summary>PurchaseOrder.cs (addition: constructors)</summary>

   ```csharp
   public PurchaseOrder(string orderNumber, SupplierId supplierId, DateOnly orderDate, Currency currency)
   {
       OrderNumber = orderNumber;
       SupplierId = supplierId;
       OrderDate = orderDate;
       Currency = currency;
   }

   public PurchaseOrder(string orderNumber, SupplierId supplierId, DateOnly orderDate, string currency)
       : this(orderNumber, supplierId, orderDate, new Currency(currency)) { }

   public PurchaseOrder(string orderNumber, SupplierId supplierId, DateTime orderDate, string currency)
       : this(orderNumber, supplierId, DateOnly.FromDateTime(orderDate), new Currency(currency)) { }
   ```
   </details>

   **Note:** the `DateTime` overload takes only the date component of whatever timestamp it's handed (`DateOnly.FromDateTime`), discarding the time-of-day. It's a convenience for the common "I have a `DateTime.UtcNow`, I want today's date" call, not a second way to model the order date.

8. **Add `PurchaseOrder`'s validation guards.** Replace the primary constructor from step 7 with the version below: three guards above the assignments. Re-read `Scenario: Invalid order number`, `Scenario: Invalid supplier`, and `Scenario: Invalid currency` first.

   **Tip:** try writing the three checks yourself first.

   <details>
   <summary>PurchaseOrder.cs (primary constructor, with guards)</summary>

   ```csharp
   public PurchaseOrder(string orderNumber, SupplierId supplierId, DateOnly orderDate, Currency currency)
   {
       ArgumentException.ThrowIfNullOrWhiteSpace(orderNumber);
       if (supplierId == default)
           throw new ArgumentException("Supplier ID is required.", nameof(supplierId));
       if (currency == default)
           throw new ArgumentException("Currency is required.", nameof(currency));

       OrderNumber = orderNumber;
       SupplierId = supplierId;
       OrderDate = orderDate;
       Currency = currency;
   }
   ```
   </details>

   **Note:** the guards live in the constructor, same as `Supplier`'s: an aggregate root enforces its creation invariant in its constructor, the single entry point and the only place a rule spanning more than one field could go (ADR-0011). `SupplierId` and `Currency` each also validate themselves, so `== default` is all the aggregate adds, a caller must pass a real one, not the struct's zero value.

9. **Add `PurchaseOrder`'s identity methods.** `Equals()` / `GetHashCode()` / `ToString()`, identity-based, comparing only `OrderNumber`.

   <details>
   <summary>PurchaseOrder.cs (addition: identity methods)</summary>

   ```csharp
   public override bool Equals(object? obj)
   {
       return obj is PurchaseOrder other && OrderNumber == other.OrderNumber;
   }

   public override int GetHashCode() => OrderNumber.GetHashCode();

   public override string ToString() => $"PurchaseOrder[OrderNumber={OrderNumber}, SupplierId={SupplierId}, OrderDate={OrderDate}, Currency={Currency}]";
   ```
   </details>

   `PurchaseOrder` is complete, enforcing every scenario from US002's acceptance criteria. The full file, no docs yet (`PurchaseOrder.cs` gets revised several more times as later features and refactors land, this isn't its last commit):

   <details>
   <summary>PurchaseOrder.cs (so far)</summary>

   ```csharp
   using Acme.OOProgramming.Procurement.Domain.Model.ValueObjects;
   using Acme.OOProgramming.Shared.Domain.Model.ValueObjects;

   namespace Acme.OOProgramming.Procurement.Domain.Model.Aggregates;

   public class PurchaseOrder
   {
       public string OrderNumber { get; }
       public SupplierId SupplierId { get; }
       public DateOnly OrderDate { get; }
       public Currency Currency { get; }

       public PurchaseOrder(string orderNumber, SupplierId supplierId, DateOnly orderDate, Currency currency)
       {
           ArgumentException.ThrowIfNullOrWhiteSpace(orderNumber);
           if (supplierId == default)
               throw new ArgumentException("Supplier ID is required.", nameof(supplierId));
           if (currency == default)
               throw new ArgumentException("Currency is required.", nameof(currency));

           OrderNumber = orderNumber;
           SupplierId = supplierId;
           OrderDate = orderDate;
           Currency = currency;
       }

       public PurchaseOrder(string orderNumber, SupplierId supplierId, DateOnly orderDate, string currency)
           : this(orderNumber, supplierId, orderDate, new Currency(currency)) { }

       public PurchaseOrder(string orderNumber, SupplierId supplierId, DateTime orderDate, string currency)
           : this(orderNumber, supplierId, DateOnly.FromDateTime(orderDate), new Currency(currency)) { }

       public override bool Equals(object? obj)
       {
           return obj is PurchaseOrder other && OrderNumber == other.OrderNumber;
       }

       public override int GetHashCode() => OrderNumber.GetHashCode();

       public override string ToString() => $"PurchaseOrder[OrderNumber={OrderNumber}, SupplierId={SupplierId}, OrderDate={OrderDate}, Currency={Currency}]";
   }
   ```
   </details>

   **Note:** `OrderNumber` is this aggregate's natural business key, not a generated surrogate ID, so equality compares it directly (same reasoning as `Supplier` comparing `Id`).

   **Note:** no items list yet: `PurchaseOrderItem` doesn't exist until Feature 3, where the items collection is added alongside it. `ToString()` gets extended there too, to include the item count.

   ```
   git add .
   git commit -m "feat(purchase-order): add purchase order aggregate, its own supplier id, and the currency value object."
   ```

   **Note:** this is the first point where `PurchaseOrder`, its `SupplierId`, and `Currency` are all real. Steps 2 to 9 land in this one commit together.

10. **Create the order in `Program.cs`.** Procurement's own `SupplierId` now shares a name with SupplyChain's, so the earlier `new SupplierId("SUP001")` call is ambiguous. Add an alias at the top of `Program.cs`, next to the other `using` directives:
   ```csharp
   using SupplyChainSupplierId = Acme.OOProgramming.SupplyChain.Domain.Model.ValueObjects.SupplierId;
   ```
   Then:
   - **Replace** `new SupplierId("SUP001")` with `new SupplyChainSupplierId("SUP001")`
   - Add a `PurchaseOrder` right after, translating the supplier's raw identifier into Procurement's own `SupplierId`
   - Use `DateTime.UtcNow`, not `DateTime.Now`, for the order date

   **Tip:** `PurchaseOrder` and the bare `SupplierId` are new to this file: resolve them with Rider's auto-import quick-fix as you type, or add the `using`s by hand.

   <details>
   <summary>Program.cs (so far)</summary>

   ```csharp
   using Acme.OOProgramming.Procurement.Domain.Model.Aggregates;
   using Acme.OOProgramming.Procurement.Domain.Model.ValueObjects;
   using Acme.OOProgramming.SupplyChain.Domain.Model.Aggregates;
   using Acme.OOProgramming.Shared.Domain.Model.ValueObjects;
   using SupplyChainSupplierId = Acme.OOProgramming.SupplyChain.Domain.Model.ValueObjects.SupplierId;

   var supplierAddress = new Address("Supplier St", "123", "SupplierCity", null, "12345", "United States");
   var supplier = new Supplier(new SupplyChainSupplierId("SUP001"), "Supplier Inc.", supplierAddress);

   Console.WriteLine($"Registered Supplier {supplier.Id.Identifier}: {supplier}");

   // Procurement never uses SupplyChain's SupplierId directly: translate its raw identifier here
   var purchaseOrder = new PurchaseOrder("PO001", new SupplierId(supplier.Id.Identifier), DateTime.UtcNow, "USD");

   Console.WriteLine($"Purchase Order {purchaseOrder.OrderNumber} created for Supplier ID {purchaseOrder.SupplierId.Identifier} in {purchaseOrder.Currency}");
   ```
   </details>

   **Note:** a real system timestamps things in UTC, never the server's local time zone, so timestamps stay consistent across servers and deployments.

   ```
   git add .
   git commit -m "feat(main): create a purchase order for the registered supplier."
   ```

11. **Publish and finish the feature.** Git Flow Helper widget → `Feature` → `Feature Publish`, then → `Feature Finish` (`Integrate Immediately`, `Keep remote branch when finished` unchecked). Merges into `develop` and pushes it too, no extra `git push` needed after.

## Add Items to a Purchase Order ([US003](./user-stories.md))

1. **Start the feature.** Feature description: `add-item-to-purchase-order` → `OK`. Creates and switches you to `feature/add-item-to-purchase-order`.

2. **Create the `ProductId` value object.** Right-click the project root → `Add` → `Class/Interface` → type `Procurement/Domain/Model/ValueObjects/ProductId`, **select `Record Struct`** → Enter. Write a `readonly record struct` wrapping a `Guid`, validated in its own `init` accessor (not `Guid.Empty`), plus a blocked parameterless constructor and a static factory `New()`.

   **Note:** the folder already exists from Feature 2; typing the full path again just reuses it.

   <details>
   <summary>ProductId.cs (no docs)</summary>

   ```csharp
   namespace Acme.OOProgramming.Procurement.Domain.Model.ValueObjects;

   public readonly record struct ProductId
   {
       public Guid Id
       {
           get;
           init
           {
               if (value == Guid.Empty)
                   throw new ArgumentException("Product ID cannot be an empty GUID.", nameof(value));
               field = value;
           }
       }

       public ProductId() => throw new InvalidOperationException("ProductId must be initialized with a non-empty GUID.");

       public ProductId(Guid id) => Id = id;

       public static ProductId New() => new(Guid.CreateVersion7());

       public override string ToString() => Id.ToString();
   }
   ```
   </details>

   The same file with its XML docs, the version you keep:

   <details>
   <summary>ProductId.cs</summary>

   ```csharp
   namespace Acme.OOProgramming.Procurement.Domain.Model.ValueObjects;

   /// <summary>
   /// Represents a product identifier value object in the Procurement bounded context.
   /// </summary>
   public readonly record struct ProductId
   {
       /// <summary>
       /// The unique identifier for the product.
       /// </summary>
       /// <exception cref="ArgumentException">Thrown when the identifier is an empty GUID.</exception>
       public Guid Id
       {
           get;
           init
           {
               if (value == Guid.Empty)
                   throw new ArgumentException("Product ID cannot be an empty GUID.", nameof(value));
               field = value;
           }
       }

       /// <summary>
       /// Prevents parameterless construction of <see cref="ProductId"/>.
       /// </summary>
       /// <exception cref="InvalidOperationException">Always thrown because a valid GUID is required.</exception>
       public ProductId() => throw new InvalidOperationException("ProductId must be initialized with a non-empty GUID.");

       /// <summary>
       /// Creates a new instance of <see cref="ProductId"/>.
       /// </summary>
       /// <param name="id">The product identifier, which must be a non-empty Guid object.</param>
       public ProductId(Guid id) => Id = id;

       /// <summary>
       /// Creates a new instance of <see cref="ProductId"/> using a time-ordered UUIDv7.
       /// </summary>
       /// <returns>A new <see cref="ProductId"/> instance containing a version 7 <see cref="Guid"/>.</returns>
       public static ProductId New() => new(Guid.CreateVersion7());

       /// <summary>
       /// Returns a string representation of the product identifier.
       /// </summary>
       /// <returns>A string representation of the product identifier.</returns>
       public override string ToString() => Id.ToString();
   }
   ```
   </details>

   ```
   git add .
   git commit -m "feat(product-id): add product id value object."
   ```

3. **Create the `Money` value object (minimal).** A `readonly record struct` wrapping `Amount` (`decimal`) and `Currency` (the value object from Feature 2), nothing else yet, same shape as every other value object here (ADR-0005). Right-click the project root → `Add` → `Class/Interface` → type `Shared/Domain/Model/ValueObjects/Money`, **select `Record Struct`** → Enter. Needed now for the first time, since an item's unit price is a `Money`.

   <details>
   <summary>Money.cs (minimal)</summary>

   ```csharp
   namespace Acme.OOProgramming.Shared.Domain.Model.ValueObjects;

   public readonly record struct Money
   {
       public decimal Amount { get; init; }
       public Currency Currency { get; init; }
   }
   ```
   </details>

   **Note:** `Money` depends only on `Currency`, which already exists, so unlike `PurchaseOrder` / `SupplierId` there's no unresolved type forcing you to split it across features. You still build it method by method, for practice.

4. **Add `Money`'s validation and constructors.** Replace `Amount` and `Currency` with `init` accessors that validate, same as every other value object (ADR-0011): `Amount` rejects a negative value, `Currency` rejects `default`. Then add the blocked parameterless constructor (`new Money()` must fail loudly, like `Currency` and `SupplierId`), the canonical `(decimal, Currency)` constructor (just assignments), and a convenience overload taking the currency as a raw `string`, since a caller usually has `"USD"` on hand, not a `Currency` instance.

   <details>
   <summary>Money.cs (Amount and Currency with validation, and the constructors)</summary>

   ```csharp
   public decimal Amount
   {
       get;
       init
       {
           ArgumentOutOfRangeException.ThrowIfNegative(value);
           field = value;
       }
   }

   public Currency Currency
   {
       get;
       init
       {
           if (value == default)
               throw new ArgumentException("Currency is required.", nameof(Currency));
           field = value;
       }
   }

   public Money() => throw new InvalidOperationException("Money must be initialized with an amount and currency.");

   public Money(decimal amount, Currency currency)
   {
       Amount = amount;
       Currency = currency;
   }

   public Money(decimal amount, string currencyCode) : this(amount, new Currency(currencyCode)) { }
   ```
   </details>

   **Note:** a `readonly record struct` always has an implicit parameterless constructor the language won't let you remove, so `default(Money)` (with `Amount == 0`, `Currency == default`) is constructible without ever running these `init` accessors. Blocking `new Money()` makes the explicit call fail loudly; the `default(Money)` gap is closed one level up, by a `== default` guard everywhere a `Money` crosses into an aggregate (ADR-0005).

5. **Add `Money`'s `ToString()`.** Prints the amount followed by the currency code.

   <details>
   <summary>Money.cs (addition: ToString)</summary>

   ```csharp
   public override string ToString() => $"{Amount} {Currency}";
   ```
   </details>

6. **Add `Money`'s arithmetic.** `Add()` (same currency only) and `Multiply()` (non-negative factor), plus `+` / `*` operator overloads that forward to them, so callers write `a + b` and `price * quantity` instead of `.Add(...)` / `.Multiply(...)`. Both methods also guard against a `default(Money)` (`Currency == default`), which a struct makes constructible: arithmetic on an uninitialized `Money` should fail loudly, not compute a wrong total. Neither is used yet, Features 3 and 4 pull them in, but they belong to `Money` itself, not to whichever feature needs them first.

   <details>
   <summary>Money.cs (addition: Add, Multiply, and operators)</summary>

   ```csharp
   public Money Add(Money other)
   {
       if (Currency == default || other.Currency == default)
           throw new InvalidOperationException("Cannot perform arithmetic on uninitialized Money instances.");

       if (Currency != other.Currency)
           throw new InvalidOperationException(
               $"Cannot add money with different currencies: '{Currency}' and '{other.Currency}'.");

       return new Money(Amount + other.Amount, Currency);
   }

   public Money Multiply(int factor) => Multiply((decimal)factor);

   public Money Multiply(decimal factor)
   {
       if (Currency == default)
           throw new InvalidOperationException("Cannot perform arithmetic on uninitialized Money instances.");

       ArgumentOutOfRangeException.ThrowIfNegative(factor);
       return new Money(Amount * factor, Currency);
   }

   public static Money operator +(Money left, Money right) => left.Add(right);

   public static Money operator *(Money money, decimal factor) => money.Multiply(factor);

   public static Money operator *(decimal factor, Money money) => money.Multiply(factor);

   public static Money operator *(Money money, int factor) => money.Multiply(factor);

   public static Money operator *(int factor, Money money) => money.Multiply(factor);
   ```
   </details>

   That completes `Money`. `Money.cs` doesn't change again, so the full file below carries its XML docs, the version you keep:

   <details>
   <summary>Money.cs</summary>

   ```csharp
   namespace Acme.OOProgramming.Shared.Domain.Model.ValueObjects;

   /// <summary>
   /// Represents a monetary value object.
   /// </summary>
   public readonly record struct Money
   {
       /// <summary>
       /// The underlying amount.
       /// </summary>
       /// <exception cref="ArgumentOutOfRangeException">Thrown when the amount is negative.</exception>
       public decimal Amount
       {
           get;
           init
           {
               ArgumentOutOfRangeException.ThrowIfNegative(value);
               field = value;
           }
       }

       /// <summary>
       /// The currency.
       /// </summary>
       /// <exception cref="ArgumentException">Thrown when the currency is not initialized.</exception>
       public Currency Currency
       {
           get;
           init
           {
               if (value == default)
                   throw new ArgumentException("Currency is required.", nameof(Currency));
               field = value;
           }
       }

       /// <summary>
       /// Prevents parameterless construction of <see cref="Money"/>.
       /// </summary>
       /// <exception cref="InvalidOperationException">Always thrown because an amount and currency are required.</exception>
       public Money() => throw new InvalidOperationException("Money must be initialized with an amount and currency.");

       /// <summary>
       /// Creates a new instance of <see cref="Money"/>.
       /// </summary>
       /// <param name="amount">The monetary amount.</param>
       /// <param name="currency">The currency.</param>
       /// <exception cref="ArgumentOutOfRangeException">Thrown when the amount is negative.</exception>
       /// <exception cref="ArgumentException">Thrown when the currency is not initialized.</exception>
       public Money(decimal amount, Currency currency)
       {
           Amount = amount;
           Currency = currency;
       }

       /// <summary>
       /// Creates a new instance of <see cref="Money"/> with the specified amount and currency code.
       /// </summary>
       /// <param name="amount">The monetary amount.</param>
       /// <param name="currencyCode">The currency code.</param>
       /// <exception cref="ArgumentOutOfRangeException">Thrown when the amount is negative.</exception>
       /// <exception cref="ArgumentException">Thrown when the currency code is not a valid 3-letter ISO code.</exception>
       public Money(decimal amount, string currencyCode) : this(amount, new Currency(currencyCode)) { }

       /// <summary>
       /// Returns a string representation of the monetary value.
       /// </summary>
       /// <returns>A string in the format "Amount Currency".</returns>
       public override string ToString() => $"{Amount} {Currency}";

       /// <summary>
       /// Adds two <see cref="Money"/> objects together.
       /// </summary>
       /// <param name="other">The other <see cref="Money"/> object to add.</param>
       /// <returns>A new <see cref="Money"/> object representing the sum of the two monetary values.</returns>
       /// <exception cref="InvalidOperationException">Thrown when the currencies do not match or an instance is uninitialized.</exception>
       public Money Add(Money other)
       {
           if (Currency == default || other.Currency == default)
               throw new InvalidOperationException("Cannot perform arithmetic on uninitialized Money instances.");

           if (Currency != other.Currency)
               throw new InvalidOperationException(
                   $"Cannot add money with different currencies: '{Currency}' and '{other.Currency}'.");

           return new Money(Amount + other.Amount, Currency);
       }

       /// <summary>
       /// Multiplies the monetary value by an integer factor.
       /// </summary>
       /// <param name="factor">The factor to multiply the monetary value by.</param>
       /// <returns>A new <see cref="Money"/> object representing the result of the multiplication.</returns>
       public Money Multiply(int factor) => Multiply((decimal)factor);

       /// <summary>
       /// Multiplies the monetary value by a decimal factor.
       /// </summary>
       /// <param name="factor">The factor to multiply the monetary value by.</param>
       /// <returns>A new <see cref="Money"/> object representing the result of the multiplication.</returns>
       /// <exception cref="ArgumentOutOfRangeException">Thrown when the factor is negative.</exception>
       /// <exception cref="InvalidOperationException">Thrown when the instance is uninitialized.</exception>
       public Money Multiply(decimal factor)
       {
           if (Currency == default)
               throw new InvalidOperationException("Cannot perform arithmetic on uninitialized Money instances.");

           ArgumentOutOfRangeException.ThrowIfNegative(factor);
           return new Money(Amount * factor, Currency);
       }

       /// <summary>
       /// Gets the result of adding two <see cref="Money"/> instances.
       /// </summary>
       public static Money operator +(Money left, Money right) => left.Add(right);

       /// <summary>
       /// Multiplies a <see cref="Money"/> value by a decimal factor.
       /// </summary>
       public static Money operator *(Money money, decimal factor) => money.Multiply(factor);

       /// <summary>
       /// Multiplies a <see cref="Money"/> value by a decimal factor.
       /// </summary>
       public static Money operator *(decimal factor, Money money) => money.Multiply(factor);

       /// <summary>
       /// Multiplies a <see cref="Money"/> value by an integer factor.
       /// </summary>
       public static Money operator *(Money money, int factor) => money.Multiply(factor);

       /// <summary>
       /// Multiplies a <see cref="Money"/> value by an integer factor.
       /// </summary>
       public static Money operator *(int factor, Money money) => money.Multiply(factor);
   }
   ```
   </details>

   **Note:** `Add()` rejects mismatched currencies (adding USD to EUR should never silently produce a USD-labeled result) and a `default(Money)` on either side. `Multiply()` guards the same way.

   ```
   git add .
   git commit -m "feat(money): add money value object."
   ```

7. **Create the `PurchaseOrderItem` entity (properties only for now).** Re-read US003's `Scenario: Invalid product ID` and `Scenario: Invalid quantity` first. Right-click the project root → `Add` → `Class/Interface` → type `Procurement/Domain/Model/Aggregates/PurchaseOrderItem` → Enter.

   Write only the properties:
   - `ProductId` (`ProductId`), `Quantity` (`int`), `UnitPrice` (`Money`), all `get`-only

   **Tip:** `ProductId` and `Money` resolve now, but Rider still needs the `using`s: `Option+Enter` (macOS) / `Alt+Enter` (Windows) on each red name → `using Acme.OOProgramming...;`.

   <details>
   <summary>PurchaseOrderItem.cs (properties only)</summary>

   ```csharp
   using Acme.OOProgramming.Procurement.Domain.Model.ValueObjects;
   using Acme.OOProgramming.Shared.Domain.Model.ValueObjects;

   namespace Acme.OOProgramming.Procurement.Domain.Model.Aggregates;

   public class PurchaseOrderItem
   {
       public ProductId ProductId { get; }
       public int Quantity { get; }
       public Money UnitPrice { get; }
   }
   ```
   </details>

   **Note:** this is an **entity**, not an aggregate root: it's managed by `PurchaseOrder`, never created or looked up on its own.

8. **Add `PurchaseOrderItem`'s constructor (happy path, no validation yet).** An `internal` constructor `(ProductId productId, int quantity, Money unitPrice)` that assigns the three properties directly.

   <details>
   <summary>PurchaseOrderItem.cs (addition: constructor)</summary>

   ```csharp
   internal PurchaseOrderItem(ProductId productId, int quantity, Money unitPrice)
   {
       ProductId = productId;
       Quantity = quantity;
       UnitPrice = unitPrice;
   }
   ```
   </details>

   **Note:** `internal` means only code in this assembly (in practice `PurchaseOrder`) can call it. A line item is created through its aggregate, never on its own.

9. **Add `PurchaseOrderItem`'s validation guards.** Replace the constructor from step 8 with the version below: three guards above the assignments, product ID not default, quantity above zero, unit price not default.

   **Tip:** try writing the guards yourself first.

   <details>
   <summary>PurchaseOrderItem.cs (constructor, with guards)</summary>

   ```csharp
   internal PurchaseOrderItem(ProductId productId, int quantity, Money unitPrice)
   {
       if (productId == default)
           throw new ArgumentException("Product ID is required.", nameof(productId));
       ArgumentOutOfRangeException.ThrowIfNegativeOrZero(quantity);
       if (unitPrice == default)
           throw new ArgumentException("Unit price is required.", nameof(unitPrice));

       ProductId = productId;
       Quantity = quantity;
       UnitPrice = unitPrice;
   }
   ```
   </details>

10. **Add `PurchaseOrderItem`'s identity methods.** Value-based `Equals()` / `GetHashCode()` / `ToString()`, on all three properties.

   <details>
   <summary>PurchaseOrderItem.cs (addition: identity methods)</summary>

   ```csharp
   public override bool Equals(object? obj)
   {
       return obj is PurchaseOrderItem other && ProductId == other.ProductId && Quantity == other.Quantity && UnitPrice == other.UnitPrice;
   }

   public override int GetHashCode() => HashCode.Combine(ProductId, Quantity, UnitPrice);

   public override string ToString() => $"PurchaseOrderItem[ProductId={ProductId}, Quantity={Quantity}, UnitPrice={UnitPrice}]";
   ```
   </details>

   That is every change Feature 3 makes to `PurchaseOrderItem`. It gains `CalculateItemTotal()` in US004 and a controlled quantity-mutation method in US006; the file so far, no docs yet:

   <details>
   <summary>PurchaseOrderItem.cs (so far)</summary>

   ```csharp
   using Acme.OOProgramming.Procurement.Domain.Model.ValueObjects;
   using Acme.OOProgramming.Shared.Domain.Model.ValueObjects;

   namespace Acme.OOProgramming.Procurement.Domain.Model.Aggregates;

   public class PurchaseOrderItem
   {
       public ProductId ProductId { get; }
       public int Quantity { get; }
       public Money UnitPrice { get; }

       internal PurchaseOrderItem(ProductId productId, int quantity, Money unitPrice)
       {
           if (productId == default)
               throw new ArgumentException("Product ID is required.", nameof(productId));
           ArgumentOutOfRangeException.ThrowIfNegativeOrZero(quantity);
           if (unitPrice == default)
               throw new ArgumentException("Unit price is required.", nameof(unitPrice));

           ProductId = productId;
           Quantity = quantity;
           UnitPrice = unitPrice;
       }

       public override bool Equals(object? obj)
       {
           return obj is PurchaseOrderItem other && ProductId == other.ProductId && Quantity == other.Quantity && UnitPrice == other.UnitPrice;
       }

       public override int GetHashCode() => HashCode.Combine(ProductId, Quantity, UnitPrice);

       public override string ToString() => $"PurchaseOrderItem[ProductId={ProductId}, Quantity={Quantity}, UnitPrice={UnitPrice}]";
   }
   ```
   </details>

   **Note:** `PurchaseOrder.Items` exposes items publicly a few steps from now, so a caller needs to compare or print one meaningfully. `PurchaseOrderItem` has no identity type of its own, unlike `Supplier` / `PurchaseOrder`, which compare by identity.

   ```
   git add .
   git commit -m "feat(purchase-order-item): add purchase order item entity."
   ```

11. **Add `PurchaseOrder`'s items collection and `AddItem()` (happy path, no validation yet).** Re-read `Scenario: Successfully add an item to a purchase order`: build a `Money` from the order's own `Currency`, construct the item, append it.

   <details>
   <summary>PurchaseOrder.cs (addition: items and AddItem)</summary>

   ```csharp
   private readonly List<PurchaseOrderItem> _items = new();

   public IReadOnlyList<PurchaseOrderItem> Items => _items.AsReadOnly();

   public void AddItem(ProductId productId, int quantity, decimal unitPriceAmount)
   {
       var unitPrice = new Money(unitPriceAmount, Currency);
       var item = new PurchaseOrderItem(productId, quantity, unitPrice);
       _items.Add(item);
   }
   ```
   </details>

   **Note:** `IReadOnlyList<PurchaseOrderItem>` is the .NET read-only view: callers can enumerate it but never add, remove, or replace an entry. `PurchaseOrderItem` didn't exist until this feature, so there was nothing to hold a list of until now.

12. **Add `AddItem()`'s validation guards.** Replace the method from step 11 with the version below: three guards, same shape as `PurchaseOrderItem`'s, but `unitPriceAmount` is a `decimal` this time. Re-read `Scenario: Invalid product ID`, `Scenario: Invalid quantity`, and `Scenario: Invalid unit price` from the aggregate's side.

   <details>
   <summary>PurchaseOrder.cs (AddItem, with guards)</summary>

   ```csharp
   public void AddItem(ProductId productId, int quantity, decimal unitPriceAmount)
   {
       if (productId == default)
           throw new ArgumentException("Product ID is required.", nameof(productId));
       ArgumentOutOfRangeException.ThrowIfNegativeOrZero(quantity);
       ArgumentOutOfRangeException.ThrowIfNegative(unitPriceAmount);

       var unitPrice = new Money(unitPriceAmount, Currency);
       var item = new PurchaseOrderItem(productId, quantity, unitPrice);
       _items.Add(item);
   }
   ```
   </details>

   **Note:** `AddItem()` re-validating what `PurchaseOrderItem`'s constructor already enforces is on purpose: an aggregate never trusts a caller to have validated correctly on its own. Validating `unitPriceAmount` as a `decimal` rejects a negative value before `Money` is even constructed.

13. **Update `PurchaseOrder.ToString()` to show the item count.** Replace the `ToString()` from Feature 2, don't add a second one, the class won't compile with two. The only change is `Items={_items.Count}`.

   <details>
   <summary>PurchaseOrder.cs (replaces the Feature 2 ToString())</summary>

   ```csharp
   public override string ToString() =>
       $"PurchaseOrder[OrderNumber={OrderNumber}, SupplierId={SupplierId}, OrderDate={OrderDate}, Items={_items.Count}, Currency={Currency}]";
   ```
   </details>

   That is every change Feature 3 makes to `PurchaseOrder`. It gains `CalculateTotal()` in US005, and `AddItem()` learns to merge duplicates and reject a conflicting price in US006; here is the file so far:

   <details>
   <summary>PurchaseOrder.cs (so far)</summary>

   ```csharp
   using Acme.OOProgramming.Procurement.Domain.Model.ValueObjects;
   using Acme.OOProgramming.Shared.Domain.Model.ValueObjects;

   namespace Acme.OOProgramming.Procurement.Domain.Model.Aggregates;

   public class PurchaseOrder
   {
       private readonly List<PurchaseOrderItem> _items = new();

       public string OrderNumber { get; }
       public SupplierId SupplierId { get; }
       public DateOnly OrderDate { get; }
       public Currency Currency { get; }

       public IReadOnlyList<PurchaseOrderItem> Items => _items.AsReadOnly();

       public PurchaseOrder(string orderNumber, SupplierId supplierId, DateOnly orderDate, Currency currency)
       {
           ArgumentException.ThrowIfNullOrWhiteSpace(orderNumber);
           if (supplierId == default)
               throw new ArgumentException("Supplier ID is required.", nameof(supplierId));
           if (currency == default)
               throw new ArgumentException("Currency is required.", nameof(currency));

           OrderNumber = orderNumber;
           SupplierId = supplierId;
           OrderDate = orderDate;
           Currency = currency;
       }

       public PurchaseOrder(string orderNumber, SupplierId supplierId, DateOnly orderDate, string currency)
           : this(orderNumber, supplierId, orderDate, new Currency(currency)) { }

       public PurchaseOrder(string orderNumber, SupplierId supplierId, DateTime orderDate, string currency)
           : this(orderNumber, supplierId, DateOnly.FromDateTime(orderDate), new Currency(currency)) { }

       public void AddItem(ProductId productId, int quantity, decimal unitPriceAmount)
       {
           if (productId == default)
               throw new ArgumentException("Product ID is required.", nameof(productId));
           ArgumentOutOfRangeException.ThrowIfNegativeOrZero(quantity);
           ArgumentOutOfRangeException.ThrowIfNegative(unitPriceAmount);

           var unitPrice = new Money(unitPriceAmount, Currency);
           var item = new PurchaseOrderItem(productId, quantity, unitPrice);
           _items.Add(item);
       }

       public override bool Equals(object? obj)
       {
           return obj is PurchaseOrder other && OrderNumber == other.OrderNumber;
       }

       public override int GetHashCode() => OrderNumber.GetHashCode();

       public override string ToString() =>
           $"PurchaseOrder[OrderNumber={OrderNumber}, SupplierId={SupplierId}, OrderDate={OrderDate}, Items={_items.Count}, Currency={Currency}]";
   }
   ```
   </details>

   ```
   git add .
   git commit -m "feat(purchase-order): add items collection and add item method."
   ```

14. **Add items in `Program.cs`.** Add two items to the order and print `Items.Count`.

   <details>
   <summary>Program.cs (addition)</summary>

   ```csharp
   purchaseOrder.AddItem(ProductId.New(), 10, 25.99m);
   purchaseOrder.AddItem(ProductId.New(), 20, 19.99m);
   Console.WriteLine($"Items added: {purchaseOrder.Items.Count}");
   ```
   </details>

   ```
   git add .
   git commit -m "feat(main): illustrate adding items to purchase order."
   ```

15. **Publish and finish the feature.** Git Flow Helper widget → `Feature` → `Feature Publish`, then → `Feature Finish` (`Integrate Immediately`, `Keep remote branch when finished` unchecked). Merges into `develop` and pushes it too, no extra `git push` needed after.

## Calculate Purchase Order Item Subtotal ([US004](./user-stories.md))

1. **Start the feature.** Feature description: `calculate-item-subtotal` → `OK`. Creates and switches you to `feature/calculate-item-subtotal`.

2. **Add `PurchaseOrderItem.CalculateItemTotal()`.** Re-read `Scenario: Successfully calculate item subtotal`. It's `UnitPrice * Quantity`. `Money` is a `readonly record struct` since Feature 3, so this reads as ordinary numeric arithmetic, no `.Multiply()` call needed.

   <details>
   <summary>PurchaseOrderItem.cs (addition)</summary>

   ```csharp
   public Money CalculateItemTotal() => UnitPrice * Quantity;
   ```
   </details>

   That is every change Feature 4 makes to `PurchaseOrderItem`. It changes once more in US006, where it gains a controlled way to mutate its own quantity; the file so far, still no docs:

   <details>
   <summary>PurchaseOrderItem.cs (so far)</summary>

   ```csharp
   using Acme.OOProgramming.Procurement.Domain.Model.ValueObjects;
   using Acme.OOProgramming.Shared.Domain.Model.ValueObjects;

   namespace Acme.OOProgramming.Procurement.Domain.Model.Aggregates;

   public class PurchaseOrderItem
   {
       public ProductId ProductId { get; }
       public int Quantity { get; }
       public Money UnitPrice { get; }

       internal PurchaseOrderItem(ProductId productId, int quantity, Money unitPrice)
       {
           if (productId == default)
               throw new ArgumentException("Product ID is required.", nameof(productId));
           ArgumentOutOfRangeException.ThrowIfNegativeOrZero(quantity);
           if (unitPrice == default)
               throw new ArgumentException("Unit price is required.", nameof(unitPrice));

           ProductId = productId;
           Quantity = quantity;
           UnitPrice = unitPrice;
       }

       public Money CalculateItemTotal() => UnitPrice * Quantity;

       public override bool Equals(object? obj)
       {
           return obj is PurchaseOrderItem other && ProductId == other.ProductId && Quantity == other.Quantity && UnitPrice == other.UnitPrice;
       }

       public override int GetHashCode() => HashCode.Combine(ProductId, Quantity, UnitPrice);

       public override string ToString() => $"PurchaseOrderItem[ProductId={ProductId}, Quantity={Quantity}, UnitPrice={UnitPrice}]";
   }
   ```
   </details>

   ```
   git add .
   git commit -m "feat(purchase-order-item): add calculate item total method."
   ```

3. **Print each subtotal in `Program.cs`.**

   <details>
   <summary>Program.cs (addition)</summary>

   ```csharp
   foreach (var item in purchaseOrder.Items)
   {
       Console.WriteLine($"Order Item Total: {item.CalculateItemTotal()}");
   }
   ```
   </details>

   ```
   git add .
   git commit -m "feat(main): illustrate order item subtotal calculation."
   ```

4. **Publish and finish the feature.** Git Flow Helper widget → `Feature` → `Feature Publish`, then → `Feature Finish` (`Integrate Immediately`, `Keep remote branch when finished` unchecked). Merges into `develop` and pushes it too, no extra `git push` needed after.

## Calculate Purchase Order Total ([US005](./user-stories.md))

1. **Start the feature.** Feature description: `calculate-order-total` → `OK`. Creates and switches you to `feature/calculate-order-total`.

2. **Add `PurchaseOrder.CalculateTotal()`.** Re-read `Scenario: Successfully calculate total`. It's a LINQ `Sum` over the items' subtotals, wrapped back into a `Money` with the order's currency.

   Nothing here is "new" functional logic: `CalculateItemTotal()` is the same OOP method already written in Feature 4. The functional style is just a different way of *composing* that existing method over a collection, instead of writing an explicit `foreach`.

   <details>
   <summary>PurchaseOrder.cs (addition: CalculateTotal)</summary>

   ```csharp
   public Money CalculateTotal()
   {
       var total = _items.Sum(item => item.CalculateItemTotal().Amount);
       return new Money(total, Currency);
   }
   ```
   </details>

   That is every change Features 4 and 5 make to `PurchaseOrder`. `AddItem()` learns to merge duplicate items and reject a conflicting price in US006; here is the file so far:

   <details>
   <summary>PurchaseOrder.cs (so far)</summary>

   ```csharp
   using Acme.OOProgramming.Procurement.Domain.Model.ValueObjects;
   using Acme.OOProgramming.Shared.Domain.Model.ValueObjects;

   namespace Acme.OOProgramming.Procurement.Domain.Model.Aggregates;

   public class PurchaseOrder
   {
       private readonly List<PurchaseOrderItem> _items = new();

       public string OrderNumber { get; }
       public SupplierId SupplierId { get; }
       public DateOnly OrderDate { get; }
       public Currency Currency { get; }

       public IReadOnlyList<PurchaseOrderItem> Items => _items.AsReadOnly();

       public PurchaseOrder(string orderNumber, SupplierId supplierId, DateOnly orderDate, Currency currency)
       {
           ArgumentException.ThrowIfNullOrWhiteSpace(orderNumber);
           if (supplierId == default)
               throw new ArgumentException("Supplier ID is required.", nameof(supplierId));
           if (currency == default)
               throw new ArgumentException("Currency is required.", nameof(currency));

           OrderNumber = orderNumber;
           SupplierId = supplierId;
           OrderDate = orderDate;
           Currency = currency;
       }

       public PurchaseOrder(string orderNumber, SupplierId supplierId, DateOnly orderDate, string currency)
           : this(orderNumber, supplierId, orderDate, new Currency(currency)) { }

       public PurchaseOrder(string orderNumber, SupplierId supplierId, DateTime orderDate, string currency)
           : this(orderNumber, supplierId, DateOnly.FromDateTime(orderDate), new Currency(currency)) { }

       public void AddItem(ProductId productId, int quantity, decimal unitPriceAmount)
       {
           if (productId == default)
               throw new ArgumentException("Product ID is required.", nameof(productId));
           ArgumentOutOfRangeException.ThrowIfNegativeOrZero(quantity);
           ArgumentOutOfRangeException.ThrowIfNegative(unitPriceAmount);

           var unitPrice = new Money(unitPriceAmount, Currency);
           var item = new PurchaseOrderItem(productId, quantity, unitPrice);
           _items.Add(item);
       }

       public Money CalculateTotal()
       {
           var total = _items.Sum(item => item.CalculateItemTotal().Amount);
           return new Money(total, Currency);
       }

       public override bool Equals(object? obj)
       {
           return obj is PurchaseOrder other && OrderNumber == other.OrderNumber;
       }

       public override int GetHashCode() => OrderNumber.GetHashCode();

       public override string ToString() =>
           $"PurchaseOrder[OrderNumber={OrderNumber}, SupplierId={SupplierId}, OrderDate={OrderDate}, Items={_items.Count}, Currency={Currency}]";
   }
   ```
   </details>

   **Note:** this is the first LINQ / functional-style code in the track, worth breaking down:
   - `_items.Sum(item => item.CalculateItemTotal().Amount)` calls the OOP method `CalculateItemTotal()` on every item (via the lambda `item => ...`), reads its `.Amount` (LINQ's `Sum` needs a plain `decimal`, not a `Money`), and adds all the `decimal`s together, no manual loop, no running-total variable.
   - The result is wrapped back into a `Money` using the order's own `Currency`.

   ```
   git add .
   git commit -m "feat(purchase-order): add calculate total method."
   ```

3. **Print the order total in `Program.cs`.**

   <details>
   <summary>Program.cs (addition)</summary>

   ```csharp
   Console.WriteLine($"Order Total: {purchaseOrder.CalculateTotal()}");
   ```
   </details>

   ```
   git add .
   git commit -m "feat(main): illustrate order total calculation."
   ```

4. **Publish and finish the feature.** Git Flow Helper widget → `Feature` → `Feature Publish`, then → `Feature Finish` (`Integrate Immediately`, `Keep remote branch when finished` unchecked). Merges into `develop` and pushes it too, no extra `git push` needed after.

## Add a Console Presentation Layer

**No user story behind this one:** it's the last piece of code before the first release. `Program.cs` has been interpolating domain objects straight into `Console.WriteLine` since Feature 2, repeating the same formatting expression at every call site. Before shipping, that formatting moves into its own presentation layer, kept out of the domain model, so `PurchaseOrder` and `Money` stay focused on the domain and never grow a display-specific `ToString()`.

1. **Start the feature.** Feature description: `add-presentation-layer` → `OK`. Creates and switches you to `feature/add-presentation-layer`.

2. **Add a `Presentation` layer for the shared kernel.** A C# 14 extension member in its own `Shared.Presentation` namespace, formatting a `Money` for the console. Right-click the project root → `Add` → `Class/Interface` → type `Shared/Presentation/ConsoleFormatting`, **select `Class`** → Enter.

   <details>
   <summary>ConsoleFormatting.cs (Shared/Presentation, no docs)</summary>

   ```csharp
   using Acme.OOProgramming.Shared.Domain.Model.ValueObjects;

   namespace Acme.OOProgramming.Shared.Presentation;

   internal static class ConsoleFormatting
   {
       extension(Money money)
       {
           public string Display => $"{money.Amount:N2} {money.Currency.Code}";
       }
   }
   ```
   </details>

   The same file with its XML docs, the version you keep:

   <details>
   <summary>ConsoleFormatting.cs (Shared/Presentation)</summary>

   ```csharp
   using Acme.OOProgramming.Shared.Domain.Model.ValueObjects;

   namespace Acme.OOProgramming.Shared.Presentation;

   /// <summary>
   /// Provides console formatting for shared kernel value objects.
   /// </summary>
   internal static class ConsoleFormatting
   {
       /// <summary>
       /// Formats a <see cref="Money"/> value object for display.
       /// </summary>
       /// <param name="money">The <see cref="Money"/> value object to format.</param>
       extension(Money money)
       {
           /// <summary>
           /// Returns a formatted string representation of the <see cref="Money"/> value object.
           /// </summary>
           public string Display => $"{money.Amount:N2} {money.Currency.Code}";
       }
   }
   ```
   </details>

   **Note:** an `extension(T target) { ... }` block adds members that read like real properties on `T` (`money.Display`) without touching `T` itself. `internal` keeps the formatter out of this project's public surface, it's a console-app-only concern. Neither `Money` nor `PurchaseOrder` gets a display `ToString()`: that would mix a presentation concern into the domain model, the coupling this codebase has stayed free of everywhere else.

3. **Add a `Presentation` layer for Procurement.** Same shape, this one formats a `PurchaseOrder`. Right-click the project root → `Add` → `Class/Interface` → type `Procurement/Presentation/ConsoleFormatting`, **select `Class`** → Enter.

   <details>
   <summary>ConsoleFormatting.cs (Procurement/Presentation, no docs)</summary>

   ```csharp
   using Acme.OOProgramming.Procurement.Domain.Model.Aggregates;

   namespace Acme.OOProgramming.Procurement.Presentation;

   internal static class ConsoleFormatting
   {
       extension(PurchaseOrder order)
       {
           public string Summary =>
               $"Purchase Order {order.OrderNumber} created for Supplier ID {order.SupplierId.Identifier} in {order.Currency} on {order.OrderDate}";
       }
   }
   ```
   </details>

   The same file with its XML docs, the version you keep:

   <details>
   <summary>ConsoleFormatting.cs (Procurement/Presentation)</summary>

   ```csharp
   using Acme.OOProgramming.Procurement.Domain.Model.Aggregates;

   namespace Acme.OOProgramming.Procurement.Presentation;

   /// <summary>
   /// Provides console formatting for purchase orders.
   /// </summary>
   internal static class ConsoleFormatting
   {
       /// <summary>
       /// Formats a <see cref="PurchaseOrder"/> for display.
       /// </summary>
       /// <param name="order">The <see cref="PurchaseOrder"/> to format.</param>
       extension(PurchaseOrder order)
       {
           /// <summary>
           /// Returns a formatted string summary of the <see cref="PurchaseOrder"/>.
           /// </summary>
           public string Summary =>
               $"Purchase Order {order.OrderNumber} created for Supplier ID {order.SupplierId.Identifier} in {order.Currency} on {order.OrderDate}";
       }
   }
   ```
   </details>

4. **Update `Program.cs` to use the extension members.** Replace the file with the version below: `purchaseOrder.Summary` and `item.UnitPrice.Display` read like real properties on the domain types, even though neither type was touched, only two `using` directives were added.

   <details>
   <summary>Program.cs (revised)</summary>

   ```csharp
   using Acme.OOProgramming.Procurement.Domain.Model.Aggregates;
   using Acme.OOProgramming.Procurement.Domain.Model.ValueObjects;
   using Acme.OOProgramming.Procurement.Presentation;
   using Acme.OOProgramming.SupplyChain.Domain.Model.Aggregates;
   using Acme.OOProgramming.Shared.Domain.Model.ValueObjects;
   using Acme.OOProgramming.Shared.Presentation;
   using SupplyChainSupplierId = Acme.OOProgramming.SupplyChain.Domain.Model.ValueObjects.SupplierId;

   var supplierAddress = new Address("Supplier St", "123", "SupplierCity", null, "12345", "United States");
   var supplier = new Supplier(new SupplyChainSupplierId("SUP001"), "Supplier Inc.", supplierAddress);

   Console.WriteLine($"Registered Supplier {supplier.Id.Identifier}: {supplier}");

   // Procurement never uses SupplyChain's SupplierId directly: translate its raw identifier here
   var purchaseOrder = new PurchaseOrder("PO001", new SupplierId(supplier.Id.Identifier), DateTime.UtcNow, "USD");
   purchaseOrder.AddItem(ProductId.New(), 10, 25.99m);
   purchaseOrder.AddItem(ProductId.New(), 20, 19.99m);

   Console.WriteLine(purchaseOrder.Summary);
   foreach (var item in purchaseOrder.Items)
   {
       Console.WriteLine($"Order Item: {item.ProductId} x {item.Quantity} at {item.UnitPrice.Display} = {item.CalculateItemTotal().Display}");
   }

   Console.WriteLine($"Order Total: {purchaseOrder.CalculateTotal().Display}");
   ```
   </details>

   Run it: `purchaseOrder.Summary` and `item.CalculateItemTotal().Display` read exactly like properties on `PurchaseOrder` / `Money`, without either type being touched.

   ```
   git add .
   git commit -m "feat(presentation): add console formatting via extension members."
   ```

5. **Publish and finish the feature.** Git Flow Helper widget → `Feature` → `Feature Publish`, then → `Feature Finish` (`Integrate Immediately`, `Keep remote branch when finished` unchecked). Merges into `develop` and pushes it too, no extra `git push` needed after.

## Prepare the First Release

**All of this happens on `develop`:** `Feature Finish` always leaves you there after merging, so no branch switch is needed. These are the last steps before tagging `1.0.0`: one end-to-end check, then the files a repo needs before anyone else looks at it.

1. **Re-run `Program.cs` end to end** and confirm all output prints in order. Compare it against the diagram from `## Project Setup`: `Address`, `Supplier`, `SupplierId`, `PurchaseOrder`, `PurchaseOrderItem`, `ProductId`, `Money`, and `Currency` are all real code now, exactly as sketched, and the console output goes through the `Presentation` layer.

2. **Add `LICENSE.md`.** In **File System** view, right-click the project root → `Add` → `File` → type `LICENSE.md` → Enter. The README's badge links to it, so it goes in first.

   <details>
   <summary>LICENSE.md</summary>

   ```markdown
   # License

   Copyright © 2026 ACME Studio. All rights reserved.

   Permission is hereby granted, free of charge, to any person obtaining a copy of this software and associated documentation files (the "Software"), to deal in the Software without restriction, including without limitation the rights to use, copy, modify, merge, publish, distribute, sublicense, and/or sell copies of the Software, and to permit persons to whom the Software is furnished to do so, subject to the following conditions:

   The above copyright notice and this permission notice shall be included in all copies or substantial portions of the Software.

   THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL ACME STUDIO OR THE WEB APPLICATIONS DEVELOPER TEAM BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE.

   **Author**: Web Applications Developer Team  
   **Contact**: For inquiries, please contact the Web Applications Developer Team at ACME Studio.
   ```
   </details>

3. **Add `README.md`.** Same way, right-click the project root → `Add` → `File` → type `README.md` → Enter. It describes what the project has right now: the five user stories, the domain model, the class diagram, how to build and run.

   <details>
   <summary>README.md</summary>

   ````markdown
   # OOP Sample (`oop-sample`)

   [![.NET](https://img.shields.io/badge/.NET-10-purple.svg)](https://dotnet.microsoft.com/)
   [![C#](https://img.shields.io/badge/C%23-14-blue.svg)](https://learn.microsoft.com/dotnet/csharp/)
   [![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](LICENSE.md)

   `oop-sample` is a sample C# console application demonstrating **Object-Oriented Programming (OOP)** and **Domain-Driven Design (DDD)** principles across two bounded contexts, SupplyChain and Procurement, and a shared kernel.

   **Author**: Web Applications Developer Team  
   **License**: See [LICENSE.md](LICENSE.md) for details.

   ---

   ## Technical Stack & Modern Features

   - **Runtime & Framework**: .NET 10.0 (C# 14.0)
   - **C# 14 & .NET 10 Features**:
     - **Extension Members (`extension(T)`)**: presentation formatting (`order.Summary`, `money.Display`) decoupled from the domain models.
     - **`field` Keyword**: property validation and null-safe fallback without an explicit private backing field.
     - **Struct Parameterless Constructor Safety**: every `readonly record struct` value object throws `InvalidOperationException` from `new X()`, and falls back safely on `default`.
     - **UUIDv7 Identifiers**: time-ordered identifiers via `Guid.CreateVersion7()` (`ProductId`).
     - **`DateOnly` Temporal Modeling**: a purchase order's date has no time-of-day or time zone.
     - **Modern Throw Helpers**: `ArgumentException.ThrowIfNullOrWhiteSpace` and friends, not hand-written null checks.

   ---

   ## Solution Structure

   ```text
   oop-sample/
   ├── Acme.OOProgramming/                     # Main domain & console application project
   │   ├── Procurement/                        # Procurement Bounded Context
   │   │   ├── Domain/Model/
   │   │   │   ├── Aggregates/                 # PurchaseOrder (AR), PurchaseOrderItem (Entity)
   │   │   │   └── ValueObjects/               # ProductId (UUIDv7), SupplierId
   │   │   └── Presentation/                   # ConsoleFormatting (C# 14 extension members)
   │   ├── SupplyChain/                        # Supply Chain Bounded Context
   │   │   └── Domain/Model/
   │   │       ├── Aggregates/                 # Supplier (AR)
   │   │       └── ValueObjects/               # SupplierId
   │   ├── Shared/                             # Shared Kernel
   │   │   ├── Domain/Model/ValueObjects/      # Currency (ISO 4217), Money, Address
   │   │   └── Presentation/                   # ConsoleFormatting (C# 14 extension members)
   │   └── Program.cs                          # Application entry point & demo scenarios
   ├── docs/                                   # Architecture & requirements documentation
   │   ├── class-diagram.puml                  # PlantUML domain model class diagram
   │   └── user-stories.md                     # User stories (US001-US005) & acceptance criteria
   ├── CHANGELOG.md                            # Project release notes & version history
   ├── LICENSE.md                              # Project license
   └── README.md                               # Project overview & guide
   ```

   ---

   ## Bounded Contexts & Domain Model

   ### 1. `Acme.OOProgramming.SupplyChain` (Supply Chain Management)
   - **`Supplier`** (*Aggregate Root*): a vendor with identity and location.
   - **`SupplierId`** (*Value Object*): strongly-typed identifier, owned by SupplyChain.

   ### 2. `Acme.OOProgramming.Procurement` (Procurement)
   - **`PurchaseOrder`** (*Aggregate Root*): purchase order invariants, currency consistency, and item lifecycle; `OrderDate` is a `DateOnly`, a calendar date with no time-of-day or time zone component.
   - **`PurchaseOrderItem`** (*Entity*): managed exclusively by `PurchaseOrder`, its constructor is `internal`.
   - **`ProductId`** (*Value Object*): time-ordered identifier generated with UUIDv7 (`Guid.CreateVersion7()`).
   - **`SupplierId`** (*Value Object*): Procurement's own copy of the concept, deliberately decoupled from SupplyChain's.
   - **`Presentation.ConsoleFormatting`** (`order.Summary`): console-only formatting kept out of the aggregate itself, via a C# 14 extension member.

   ### 3. `Acme.OOProgramming.Shared` (Shared Kernel)
   - **`Money`** (*Value Object*): `decimal` amount + a validated `Currency`, `readonly record struct`.
   - **`Currency`** (*Value Object*): validated 3-letter ISO code, `readonly record struct`.
   - **`Address`** (*Value Object*): international postal address, `readonly record struct`.
   - **`Presentation.ConsoleFormatting`** (`money.Display`): console-only formatting kept out of `Money` itself, via a C# 14 extension member.

   ---

   ## Key Domain Rules & Design Invariants

   - **Aggregate invariant encapsulation**: `PurchaseOrder` strictly controls the creation and lifecycle of `PurchaseOrderItem`.
   - **Single-currency rule**: every item in a `PurchaseOrder` is priced in the order's own currency.
   - **Currency-safe arithmetic**: `Money` rejects cross-currency operations and negative amounts.
   - **Cross-context references**: each bounded context owns its own copy of any identifier it references from another context, rather than sharing one type.
   - **Presentation decoupling**: display formatting (`order.Summary`, `money.Display`) lives in dedicated `*.Presentation` namespaces, never on the domain models themselves.

   ---

   ## Project Documentation

   | Document | Description |
   | :--- | :--- |
   | [**User Stories**](docs/user-stories.md) | User stories (US001-US005) and acceptance criteria. |
   | [**Class Diagram**](docs/class-diagram.puml) | PlantUML class diagram of bounded contexts, aggregates, entities, and value objects. |
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
   dotnet run --project Acme.OOProgramming
   ```
   ````
   </details>

   ```
   git add .
   git commit -m "docs: add license and readme."
   ```

## Release

**Still on `develop`, right where *Prepare the First Release* left off.** A release is a *batch* of finished features, not one per feature. The first five user stories plus the presentation layer together are one sprint's worth of work, exactly the kind of thing a real release bundles.

1. **Start the release.** Git Flow Helper widget → `Release` → `Release Start` → **Version description** `v1.0.0` → `OK`. Creates and switches you to `release/v1.0.0`.

   **Note:** the release name carries a `v` prefix (`v1.0.0`), matching the git tag it becomes on finish. The `.csproj` `<Version>` stays plain (`1.0.0`), and so does the `CHANGELOG.md` heading (`## [1.0.0]`): NuGet and Keep a Changelog conventions don't use the prefix.

   **Tip:** the `push local branch when finished` checkbox doesn't matter much either way here; step 4 below publishes the branch properly regardless.

2. **Bump the version in `Acme.OOProgramming.csproj`.** Switch the Solution Explorer dropdown to **File System** view (Solution view hides the `.csproj`, and this whole section edits it and adds plain files). Change `<Version>0.1.0-preview</Version>` to `<Version>1.0.0</Version>`.
   ```
   git add .
   git commit -m "chore(release): bump version to 1.0.0."
   ```

   **Note:** that one edit does two things. The `-preview` suffix comes off, since that's never what ships. And the version becomes `1.0.0`: the first release meant to stay stable, which is what reaching `1.0.0` signals.

3. **Add `CHANGELOG.md`.** Still in **File System** view, right-click the project root → `Add` → `File` → type `CHANGELOG.md` → Enter:

   <details>
   <summary>CHANGELOG.md</summary>

   ```markdown
   # Changelog

   All notable changes to this project will be documented in this file.

   The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/),
   and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

   ## [1.0.0] - 2026-08-27

   ### Added
   - US001: Register a Supplier: register a `Supplier` with an identifier, name, and address, in its own SupplyChain bounded context.
   - US002: Create a Purchase Order: create a `PurchaseOrder` for a `Supplier`, with order number, `DateOnly` order date, and currency validation.
   - US003: Add Items to a Purchase Order, US004: Calculate Purchase Order Item Subtotal, US005: Calculate Purchase Order Total: add `PurchaseOrderItem`s to a `PurchaseOrder`, with running total calculation.
   - Shared kernel value objects `Address`, `Money`, and `Currency`; identity value objects `SupplierId` and `ProductId`, modeled as C# records.
   - `Presentation` layer (`order.Summary`, `money.Display`) via C# 14 extension members, keeping console formatting out of the domain models.
   - Console demo in `Program.cs` showing domain validation and invariants.
   - Project `README.md` and MIT `LICENSE.md`.
   ```
   </details>

   **Note:** the `## [version] - date` line uses the date you finish the release, in `YYYY-MM-DD` format. The intro block (`Keep a Changelog` + `Semantic Versioning`) stays at the top; every later release adds its own section directly under it, newest first.

   ```
   git add .
   git commit -m "docs: add changelog for 1.0.0."
   ```

   **Note:** this commit matters beyond documentation, it's the reason `Release Finish` has something to merge into `develop`. A release branch with no commits of its own merges into `develop` as a no-op (no merge commit there) while still creating a real merge commit on `main`. That mismatch is what makes `develop` briefly show as "behind" `main` on GitHub. With a real commit here, both merges are real and `develop` / `main` land in sync on their own.

4. **Publish the release branch.** Git Flow Helper widget → `Release` → `Release Publish`. Pushes `release/v1.0.0` with both commits included. Do this every time after adding a commit to a release branch, right before finishing it, same as `Feature Publish`.

   **Note:** `Release Finish` also tries to push the release branch as part of its own sequence, but that push is broken in this plugin (it pushes the tag name instead of the branch). `Release Publish` is what actually gets it there.

5. **Finish the release.** Git Flow Helper widget → `Release` → `Release Finish`. No dialog, it runs immediately: merges `release/v1.0.0` into `main` (tags it `v1.0.0` there), merges it into `develop` too, pushes both, then deletes the release branch. A notification confirms: "Released finished and tag pushed successfully."

   **Note:** skipping step 4 can leave the local branch delete failing with a "branch not fully merged" warning, since git compares against a stale remote-tracking ref. Afterward, GitHub may show `develop` as slightly "ahead" / "behind" `main`: that's expected (each branch gets its own separate merge commit) and not something to fix; the file content already matches.

6. **Publish the GitHub Release.** On GitHub: **Releases** → **Draft a new release** → pick the existing tag `v1.0.0` (created by `Release Finish`, don't create a new one). Set the release title to `Version 1.0.0` (the title spells it out; the tag keeps the `v` prefix), description below, **Publish release**.

   <details>
   <summary>Release notes (1.0.0)</summary>

   ```markdown
   ## 🚀 Added

   - **US001: Register a Supplier**: register a `Supplier` with an identifier, name, and address, in its own SupplyChain bounded context.
   - **US002: Create a Purchase Order**: create a `PurchaseOrder` for a `Supplier`, with order number, date, and currency validation.
   - **US003: Add Items to a Purchase Order** and **US004/US005: Calculate Purchase Order Item/Total Subtotal**: add `PurchaseOrderItem`s to a `PurchaseOrder`, with running total calculation.
   - Value Objects `Address`, `Money`, `Currency`, `SupplierId`, `ProductId`, modeled as C# records.
   - `Presentation` layer (`order.Summary`, `money.Display`) via C# 14 extension members.
   - Project `README.md` and MIT license; `CHANGELOG.md` to track version history going forward.
   ```
   </details>

   **Note:** the branch selector on that screen only matters when creating a brand-new tag on the spot; since this tag already exists and points at a commit on `main`, it's ignored. The GitHub Release is a feature layered on top of the tag, separate from Git Flow itself, which only ever creates the tag.

   **Tip:** the same thing works from the command line: `gh release create v1.0.0 --title "Version 1.0.0" --notes-file CHANGELOG.md` (the [GitHub CLI](https://cli.github.com/), `gh`, authenticated once via `gh auth login`). `--notes-file` accepts any Markdown file; `CHANGELOG.md` works directly here since the tag already exists.

7. **Back on `develop`, pick the `-preview` suffix back up.** Git Flow Helper switches you to `develop` automatically after `Release Finish`. Still in **File System** view, in `Acme.OOProgramming.csproj`: `<Version>1.0.0</Version>` → `<Version>1.1.0-preview</Version>`.

   **Note:** skipping straight to `1.1.0-preview` (not `1.0.1-preview`) says out loud what's already planned: the sections below add a real new user story (US006) and the documentation work around it, not just a bugfix, and semantic versioning reserves the middle number for that.
   ```
   git add .
   git commit -m "chore(dev): set development version to 1.1.0-preview."
   git push
   ```


## Merge Duplicate Items in a Purchase Order ([US006](./user-stories.md))

**A real requirement change, arriving after `1.0.0` shipped.** Everything up to here (Features 1-5 plus the presentation layer) matches US001-US005. This one is different: a real procurement team using the shipped product reported back a genuine usability problem. `docs/user-stories.md` isn't a frozen, one-time deliverable, it grows exactly like this. It ships in `1.1.0`, alongside the documentation work below; `## Testing` further down writes its tests alongside every other user story.

1. **Start the feature.** Feature description: `merge-duplicate-items` → `OK`. Creates and switches you to `feature/merge-duplicate-items`.

2. **Document US006 first, before any code.** Add it to `docs/user-stories.md`, right after US005.

   <details>
   <summary>docs/user-stories.md (addition)</summary>

   ```markdown
   ## US006: Merge Duplicate Items in a Purchase Order
   As a procurement manager, I want adding a product that's already on the purchase order to combine into the existing line instead of creating a new one, so that my purchase order doesn't show confusing duplicate entries for the same product.

   ### Scenario: Merge quantities for a repeated product at the same price
   - **Given** a purchase order "PO001" with an item for product ID "X", quantity 10, unit price amount 15.99 in USD
   - **When** the procurement manager adds another item for the same product ID "X", quantity 5, unit price amount 15.99
   - **Then** the purchase order still has one item for product ID "X", now with quantity 15

   ### Scenario: Re-adding a product at a conflicting price is rejected
   - **Given** a purchase order "PO001" with an item for product ID "X", quantity 10, unit price amount 15.99 in USD
   - **When** the procurement manager adds another item for the same product ID "X", quantity 5, unit price amount 19.99
   - **Then** the purchase order rejects the call; the item for product ID "X" keeps its original quantity of 10 and unit price of 15.99 USD

   ### Scenario: Adding a different product still creates a new line
   - **Given** a purchase order "PO001" with an item for product ID "X"
   - **When** the procurement manager adds an item for a different product ID "Y"
   - **Then** the purchase order has two separate items
   ```
   </details>

   ```
   git add .
   git commit -m "docs(user-stories): add US006, merge duplicate items in a purchase order."
   ```

3. **Decide what happens when the price conflicts.** Re-read the three scenarios. If the same product is re-added at a *different* unit price (the supplier's price changed, or the caller made a typo), the merge can't pick one silently: keeping the original hides a real price change, overwriting hides a possible typo, and either way a caller who passed an explicit price gets no signal it was ignored. The choice here: **combine quantities only when the price matches; reject the call otherwise**, so the conflict surfaces instead of being resolved behind the caller's back.

   **Note:** an aggregate should fail loudly when an operation conflicts with state it already committed, not quietly choose a winner. That is the same reasoning behind every guard clause in the domain model.

4. **Give `PurchaseOrderItem` a controlled way to change its own quantity.** A merge increases an existing line's quantity. `PurchaseOrderItem` is an entity, not a value object, so it changes its own state through an intention-revealing method rather than `PurchaseOrder` rebuilding it from outside. Replace `Quantity` with a version that has a `private set`, and add an `internal void IncreaseQuantity(int)` method that writes through it.

   <details>
   <summary>PurchaseOrderItem.cs (Quantity with a private setter, and IncreaseQuantity)</summary>

   ```csharp
   public int Quantity
   {
       get;
       private set
       {
           ArgumentOutOfRangeException.ThrowIfNegativeOrZero(value);
           field = value;
       }
   }

   internal void IncreaseQuantity(int additionalQuantity)
   {
       ArgumentOutOfRangeException.ThrowIfNegativeOrZero(additionalQuantity);
       Quantity += additionalQuantity;
   }
   ```
   </details>

   **Note:** "tell, don't ask": the aggregate tells the item to increase its own quantity, it doesn't read the item's state and reconstruct it from outside. `internal` keeps the method callable only from inside this assembly, in practice `PurchaseOrder`.

   That completes `PurchaseOrderItem`. The full file, with its XML docs:

   <details>
   <summary>PurchaseOrderItem.cs</summary>

   ```csharp
   using Acme.OOProgramming.Procurement.Domain.Model.ValueObjects;
   using Acme.OOProgramming.Shared.Domain.Model.ValueObjects;

   namespace Acme.OOProgramming.Procurement.Domain.Model.Aggregates;

   /// <summary>
   /// Represents an item within a purchase order in the Procurement bounded context.
   /// This entity is managed by the <see cref="PurchaseOrder"/> aggregate.
   /// </summary>
   public class PurchaseOrderItem
   {
       /// <summary>
       /// Creates a new instance of <see cref="PurchaseOrderItem"/>.
       /// </summary>
       /// <param name="productId">The product identifier, which must be a non-empty <see cref="ProductId"/> value.</param>
       /// <param name="quantity">The quantity of the product, which must be greater than zero.</param>
       /// <param name="unitPrice">The unit price of the product, which must not be the default <see cref="Money"/> value.</param>
       internal PurchaseOrderItem(ProductId productId, int quantity, Money unitPrice)
       {
           if (productId == default)
               throw new ArgumentException("Product ID is required.", nameof(productId));
           ArgumentOutOfRangeException.ThrowIfNegativeOrZero(quantity);
           if (unitPrice == default)
               throw new ArgumentException("Unit price is required.", nameof(unitPrice));

           ProductId = productId;
           Quantity = quantity;
           UnitPrice = unitPrice;
       }

       /// <summary>
       /// The product identifier.
       /// </summary>
       public ProductId ProductId { get; }

       /// <summary>
       /// The quantity of the product.
       /// </summary>
       /// <exception cref="ArgumentOutOfRangeException">Thrown when the quantity is set to a value less than or equal to zero.</exception>
       public int Quantity
       {
           get;
           private set
           {
               ArgumentOutOfRangeException.ThrowIfNegativeOrZero(value);
               field = value;
           }
       }

       /// <summary>
       /// The unit price of the product.
       /// </summary>
       public Money UnitPrice { get; }

       /// <summary>
       /// Increases the quantity of this item by the specified amount, keeping the original unit price.
       /// </summary>
       /// <remarks>
       /// Called by <see cref="PurchaseOrder.AddItem"/> when the same product is added again at the same
       /// unit price: an intention-revealing method that mutates the entity's own state, instead of the
       /// aggregate reconstructing it from outside.
       /// </remarks>
       /// <param name="additionalQuantity">The additional quantity to add, which must be greater than zero.</param>
       /// <exception cref="ArgumentOutOfRangeException">Thrown when the additional quantity is less than or equal to zero.</exception>
       internal void IncreaseQuantity(int additionalQuantity)
       {
           ArgumentOutOfRangeException.ThrowIfNegativeOrZero(additionalQuantity);
           Quantity += additionalQuantity;
       }

       /// <summary>
       /// Calculates the total price of the item.
       /// </summary>
       /// <returns>The total price as a <see cref="Money"/> object.</returns>
       public Money CalculateItemTotal() => UnitPrice * Quantity;

       /// <summary>
       /// Determines whether this <see cref="PurchaseOrderItem"/> is equal to another object, by value.
       /// </summary>
       /// <param name="obj">The object to compare against.</param>
       /// <returns><see langword="true"/> if the other object is a <see cref="PurchaseOrderItem"/> with the same <see cref="ProductId"/>, <see cref="Quantity"/>, and <see cref="UnitPrice"/>.</returns>
       public override bool Equals(object? obj)
       {
           return obj is PurchaseOrderItem other && ProductId == other.ProductId && Quantity == other.Quantity && UnitPrice == other.UnitPrice;
       }

       /// <summary>
       /// Returns a hash code based on the item's product identifier, quantity, and unit price.
       /// </summary>
       /// <returns>A hash code derived from <see cref="ProductId"/>, <see cref="Quantity"/>, and <see cref="UnitPrice"/>.</returns>
       public override int GetHashCode() => HashCode.Combine(ProductId, Quantity, UnitPrice);

       /// <summary>
       /// Returns a string representation of the purchase order item.
       /// </summary>
       /// <returns>A string representation of the purchase order item.</returns>
       public override string ToString() => $"PurchaseOrderItem[ProductId={ProductId}, Quantity={Quantity}, UnitPrice={UnitPrice}]";
   }
   ```
   </details>

   ```
   git add .
   git commit -m "feat(purchase-order-item): let an item increase its own quantity."
   ```

5. **Implement merge-or-reject in `PurchaseOrder.AddItem()`.** Replace `AddItem()` from Feature 3 with the version below: look for an existing line with the same `ProductId`; if one exists and the new price differs, throw `InvalidOperationException`; if it matches, call `IncreaseQuantity()`; if no line exists, append a new one. The three validation guards at the top don't change.

   **Tip:** try writing the lookup logic yourself first.

   <details>
   <summary>PurchaseOrder.cs (AddItem, with the merge-or-reject branch)</summary>

   ```csharp
   public void AddItem(ProductId productId, int quantity, decimal unitPriceAmount)
   {
       if (productId == default)
           throw new ArgumentException("Product ID is required.", nameof(productId));
       ArgumentOutOfRangeException.ThrowIfNegativeOrZero(quantity);
       ArgumentOutOfRangeException.ThrowIfNegative(unitPriceAmount);

       var unitPrice = new Money(unitPriceAmount, Currency);
       var existing = _items.Find(item => item.ProductId == productId);
       if (existing is not null)
       {
           if (existing.UnitPrice != unitPrice)
               throw new InvalidOperationException(
                   $"Cannot add product {productId} at {unitPrice}; the order already has it at {existing.UnitPrice}.");
           existing.IncreaseQuantity(quantity);
           return;
       }

       _items.Add(new PurchaseOrderItem(productId, quantity, unitPrice));
   }
   ```
   </details>

   `PurchaseOrder.cs` doesn't change again after this. Two small tightenings land in the full file below:
   - `Items` caches its read-only wrapper in an `_itemsView` field (`_itemsView ??= _items.AsReadOnly()`) instead of allocating a fresh `ReadOnlyCollection` on every call; the list it wraps is the same one, so the cached view stays correct as items are added.
   - `CalculateTotal()` accumulates into a running `Money` with the `+` operator, instead of the Feature 5 `Sum` over unwrapped `decimal`s followed by a re-wrap.

   <details>
   <summary>PurchaseOrder.cs</summary>

   ```csharp
   using Acme.OOProgramming.Procurement.Domain.Model.ValueObjects;
   using Acme.OOProgramming.Shared.Domain.Model.ValueObjects;

   namespace Acme.OOProgramming.Procurement.Domain.Model.Aggregates;

   /// <summary>
   /// Represents a purchase order aggregate root in the Procurement bounded context.
   /// </summary>
   public class PurchaseOrder
   {
       private readonly List<PurchaseOrderItem> _items = [];
       private IReadOnlyList<PurchaseOrderItem>? _itemsView;

       /// <summary>
       /// Gets the unique purchase order number.
       /// </summary>
       public string OrderNumber { get; }

       /// <summary>
       /// Gets the identifier of the supplier associated with this purchase order.
       /// </summary>
       public SupplierId SupplierId { get; }

       /// <summary>
       /// Gets the order date.
       /// </summary>
       public DateOnly OrderDate { get; }

       /// <summary>
       /// Gets the currency enforced across all items in this purchase order.
       /// </summary>
       public Currency Currency { get; }

       /// <summary>
       /// Gets an immutable read-only view of the purchase order items.
       /// </summary>
       public IReadOnlyList<PurchaseOrderItem> Items => _itemsView ??= _items.AsReadOnly();

       /// <summary>
       /// Initializes a new instance of <see cref="PurchaseOrder"/>.
       /// </summary>
       /// <param name="orderNumber">The order number, which must be a non-null, non-empty string.</param>
       /// <param name="supplierId">The supplier identifier, which must be a non-null <see cref="SupplierId"/> object.</param>
       /// <param name="orderDate">The order date as a <see cref="DateOnly"/>.</param>
       /// <param name="currency">The currency enforced across every item in the order.</param>
       public PurchaseOrder(string orderNumber, SupplierId supplierId, DateOnly orderDate, Currency currency)
       {
           ArgumentException.ThrowIfNullOrWhiteSpace(orderNumber);
           if (supplierId == default)
               throw new ArgumentException("Supplier ID is required.", nameof(supplierId));
           if (currency == default)
               throw new ArgumentException("Currency is required.", nameof(currency));

           OrderNumber = orderNumber;
           SupplierId = supplierId;
           OrderDate = orderDate;
           Currency = currency;
       }

       /// <summary>
       /// Initializes a new instance of <see cref="PurchaseOrder"/> using a currency code.
       /// </summary>
       /// <param name="orderNumber">The order number, which must be a non-null, non-empty string.</param>
       /// <param name="supplierId">The supplier identifier, which must be a non-null <see cref="SupplierId"/> object.</param>
       /// <param name="orderDate">The order date, as a calendar date with no time-of-day or time zone component.</param>
       /// <param name="currency">The currency, which must be a non-null, non-empty string with a length of 3.</param>
       public PurchaseOrder(string orderNumber, SupplierId supplierId, DateOnly orderDate, string currency)
           : this(orderNumber, supplierId, orderDate, new Currency(currency)) { }

       /// <summary>
       /// Initializes a new instance of <see cref="PurchaseOrder"/> using a <see cref="DateTime"/> order date.
       /// </summary>
       /// <param name="orderNumber">The order number, which must be a non-null, non-empty string.</param>
       /// <param name="supplierId">The supplier identifier, which must be a non-null <see cref="SupplierId"/> object.</param>
       /// <param name="orderDate">The order date; only its date component is kept, the time-of-day is discarded.</param>
       /// <param name="currency">The currency, which must be a non-null, non-empty string with a length of 3.</param>
       public PurchaseOrder(string orderNumber, SupplierId supplierId, DateTime orderDate, string currency)
           : this(orderNumber, supplierId, DateOnly.FromDateTime(orderDate), new Currency(currency)) { }

       /// <summary>
       /// Adds an item to the purchase order. If an item for the same product already exists at the same
       /// unit price, its quantity is increased instead of creating a new line.
       /// </summary>
       /// <param name="productId">The product identifier, which must be a non-empty <see cref="ProductId"/> value.</param>
       /// <param name="quantity">The quantity of the product, which must be greater than zero.</param>
       /// <param name="unitPriceAmount">The unit price of the product, which must be a non-negative number.</param>
       /// <exception cref="ArgumentException">Thrown when the product ID is empty.</exception>
       /// <exception cref="ArgumentOutOfRangeException">Thrown when the quantity is less than or equal to zero, or the unit price is negative.</exception>
       /// <exception cref="InvalidOperationException">Thrown when the same product is re-added at a different unit price than its existing line.</exception>
       public void AddItem(ProductId productId, int quantity, decimal unitPriceAmount)
       {
           if (productId == default)
               throw new ArgumentException("Product ID is required.", nameof(productId));
           ArgumentOutOfRangeException.ThrowIfNegativeOrZero(quantity);
           ArgumentOutOfRangeException.ThrowIfNegative(unitPriceAmount);

           var unitPrice = new Money(unitPriceAmount, Currency);
           var existing = _items.Find(item => item.ProductId == productId);
           if (existing is not null)
           {
               if (existing.UnitPrice != unitPrice)
                   throw new InvalidOperationException(
                       $"Cannot add product {productId} at {unitPrice}; the order already has it at {existing.UnitPrice}.");
               existing.IncreaseQuantity(quantity);
               return;
           }

           _items.Add(new PurchaseOrderItem(productId, quantity, unitPrice));
       }

       /// <summary>
       /// Calculates the total price of the purchase order.
       /// </summary>
       /// <returns>The total price as a <see cref="Money"/> object.</returns>
       public Money CalculateTotal()
       {
           var total = new Money(0m, Currency);
           foreach (var item in _items) total += item.CalculateItemTotal();
           return total;
       }

       /// <summary>
       /// Determines whether this <see cref="PurchaseOrder"/> is equal to another object, by identity.
       /// </summary>
       /// <param name="obj">The object to compare against.</param>
       /// <returns><see langword="true"/> if the other object is a <see cref="PurchaseOrder"/> with the same <see cref="OrderNumber"/>.</returns>
       public override bool Equals(object? obj)
       {
           return obj is PurchaseOrder other && OrderNumber == other.OrderNumber;
       }

       /// <summary>
       /// Returns a hash code based on the purchase order's identity.
       /// </summary>
       /// <returns>A hash code derived from <see cref="OrderNumber"/>.</returns>
       public override int GetHashCode() => OrderNumber.GetHashCode();

       /// <summary>
       /// Returns a string representation of the purchase order.
       /// </summary>
       /// <returns>A string representation of the purchase order.</returns>
       public override string ToString() =>
           $"PurchaseOrder[OrderNumber={OrderNumber}, SupplierId={SupplierId}, OrderDate={OrderDate}, Items={_items.Count}, Currency={Currency}]";
   }
   ```
   </details>

   Add a block to `Program.cs` to watch both paths, right after the `Order Total` line:

   <details>
   <summary>Program.cs (addition)</summary>

   ```csharp
   var sharedProduct = ProductId.New();
   purchaseOrder.AddItem(sharedProduct, 10, 12.50m);
   purchaseOrder.AddItem(sharedProduct, 5, 12.50m);
   Console.WriteLine($"Merged line quantity: {purchaseOrder.Items.Single(i => i.ProductId == sharedProduct).Quantity}");

   try
   {
       purchaseOrder.AddItem(sharedProduct, 1, 9.99m);
   }
   catch (InvalidOperationException ex)
   {
       Console.WriteLine($"Rejected conflicting unit price: {ex.Message}");
   }
   ```
   </details>

   Confirm it compiles, then commit:
   ```
   dotnet build
   git add .
   git commit -m "feat(purchase-order): merge a duplicate product and reject a conflicting unit price."
   ```

6. **Publish and finish the feature.** Git Flow Helper widget → `Feature` → `Feature Publish`, then → `Feature Finish` (`Integrate Immediately`, `Keep remote branch when finished` unchecked). Merges into `develop` and pushes it too.

## Release

**Still on `develop`, no Git Flow feature needed until the version bump below:** writing down decisions already made across every feature so far, and shipping the record of them. It goes out in `1.1.0`, the same release as US006.

1. **Generate the XML documentation from the comments already in your code.** In **File System** view (Solution view hides the `.csproj`), add one property to `Acme.OOProgramming.csproj`, in the same `<PropertyGroup>` as `<Version>`:
   ```xml
   <GenerateDocumentationFile>true</GenerateDocumentationFile>
   ```
   Then:
   ```
   dotnet build
   ```
   Open `Acme.OOProgramming/bin/Debug/net10.0/Acme.OOProgramming.xml` in a text editor. Every `<summary>`/`<param>`/`<returns>`/`<exception>` comment written across all six user stories and the presentation layer turns into a real, structured XML file, exactly what IntelliSense reads to show tooltips, and what tools like DocFX turn into a browsable static site.

   **Note:** Expect a batch of `CS1591` warnings ("missing XML comment for publicly visible member") on properties/methods that never got their own explicit `<summary>`, only a class-level one: a real, honest gap this flag surfaces, not a sign anything is broken. A team with a strict docs policy would either add per-property comments or explicitly suppress `CS1591`; either is a legitimate call, just make it on purpose.

2. **Add these eleven Architecture Decision Records (ADRs) to a single `docs/adrs.md` file.** In **File System** view: right-click the `docs` folder → `Add` → `File` → `adrs.md`. They document every decision made so far, across all six user stories and the presentation layer, in one sitting rather than scattered one per feature:
   - why value objects are `record`s, including identity types like `ProductId`, instead of a raw `Guid`
   - why each bounded context owns its own `SupplierId` instead of sharing one
   - why aggregates compare by identity and hide their internal collections
   - why `Money.Amount` is a `decimal`, never `double`
   - why every value object is a `readonly record struct`, and how the `default(struct)` gap is closed
   - why `Currency` is a value object, not a bare 3-letter `string`
   - why `PurchaseOrderItem` changes its own quantity through a controlled mutation method
   - why re-adding a duplicate product merges only at a matching price and is rejected otherwise
   - why `OrderDate` is a `DateOnly`, not a `DateTime`
   - why console formatting moved out to its own `Presentation` layer
   - why value objects validate in their `init` accessor but aggregate roots validate in their constructor

   **Note:** the standard ADR format is **Status**, **Context**, **Decision Drivers**, **Considered Options**, **Decision**, **Consequences**. Look it up if it's unfamiliar.

   <details>
   <summary>docs/adrs.md</summary>

   ````markdown
   # Architecture Decision Records

   # ADR-0001: Value Objects as C# Records

   **Status:** Accepted
   **Note:** [ADR-0005](#adr-0005-value-objects-as-readonly-record-struct) settles every value object here on `readonly record struct` specifically, and covers the `default(struct)` risk that shape carries.

   ## Context

   Several concepts in this project have no identity of their own and are only ever compared by their current state: `Money`, `Currency`, `Address`, and the identity types `SupplierId` (one per bounded context) and `ProductId`. All of them need to be immutable and validated at construction time. Traditional C# classes require hand-writing `Equals()`, `GetHashCode()`, `ToString()`, and read-only properties for something the language itself has expressed directly since records were introduced (C# 9, refined with primary constructors in C# 12, and with the `field` keyword in C# 13).

   ## Decision Drivers

   - Immutability should be the default, not something achieved through discipline.
   - Value equality (two instances with the same state are equal) should come from the language, not be reimplemented by hand every time.
   - Validation belongs at construction time, in one place (each property's own `init` accessor), not scattered across every call site that creates one.
   - A raw `Guid`/`string` lets a caller accidentally pass one aggregate's identity where a different aggregate's is expected, and the compiler won't catch it.

   ## Considered Options

   1. C# records, each property validating itself in its own `init` accessor *(Chosen)*
   2. Plain classes with hand-written `Equals()`/`GetHashCode()`/`ToString()`
   3. Plain classes plus a third-party value-object base type (e.g. a NuGet package)

   ## Decision

   All value objects, including aggregate identity types, are `record`s. Each property validates itself the moment it's assigned, inside its own `init` accessor: a value object's rules are single-field checks in isolation, so the accessor is the right home for them (aggregate roots are different, see [ADR-0011](#adr-0011-where-domain-validation-lives)). Wrapping an aggregate's identity in its own record (`SupplierId`, `ProductId`) closes the primitive-obsession gap for free, without hand-writing `Equals()`/`GetHashCode()` for it. Whether a given record ends up a reference type or, per ADR-0005, a `readonly record struct` is a separate question this ADR doesn't settle on its own.

   ```csharp
   public readonly record struct ProductId
   {
       public Guid Id
       {
           get;
           init
           {
               if (value == Guid.Empty)
                   throw new ArgumentException("Product ID cannot be an empty GUID.", nameof(value));
               field = value;
           }
       }

       public ProductId() => throw new InvalidOperationException("ProductId must be initialized with a non-empty GUID.");

       public ProductId(Guid id) => Id = id;
   }
   ```

   ## Consequences

   **Positive:**
   - Zero boilerplate for `Equals()`/`GetHashCode()`/`ToString()`, generated correctly by the compiler.
   - Each `init` accessor gives its own property a single, unavoidable validation entry point, instead of a constructor body that has to remember every field.
   - Identity types like `SupplierId`/`ProductId` prevent primitive obsession without extra ceremony.

   **Negative / Trade-offs:**
   - Records can't extend another class (only implement interfaces); not a problem here, since none of these value objects need inheritance.
   - Adding a method to a record still means editing the type itself, records don't support extension methods.

   ---

   # ADR-0002: Each Bounded Context Owns Its Own Reference Types

   **Status:** Accepted

   ## Context

   Both the SupplyChain and Procurement bounded contexts need to refer to "a supplier". It would be tempting to define a single `SupplierId` type once, in the shared namespace, and have every context import it.

   ## Decision Drivers

   - Bounded contexts should be able to evolve independently, without a change in one forcing a change in another.
   - A concept's meaning can subtly differ across contexts: SupplyChain's `SupplierId` is the aggregate's own generated identity; Procurement's `SupplierId` is a reference it only ever receives, never creates.
   - Context Mapping: no bounded context should silently depend on another context's internal types.

   ## Considered Options

   1. Each bounded context defines and owns its own copy of any cross-context reference type *(Chosen)*
   2. A single shared `SupplierId` type in the shared kernel, imported by every context that needs it

   ## Decision

   SupplyChain's `SupplierId` lives in `Acme.OOProgramming.SupplyChain.Domain.Model.ValueObjects`; Procurement's `SupplierId` lives in `Acme.OOProgramming.Procurement.Domain.Model.ValueObjects`, a separate record with the same shape. The only place the two ever meet is the translation point in `Program.cs`, which explicitly converts SupplyChain's raw `Identifier` into a new instance of Procurement's own type:

   ```csharp
   var purchaseOrder = new PurchaseOrder("PO001", new SupplierId(supplier.Id.Identifier), DateTime.UtcNow, "USD");
   ```

   `Shared` is reserved for concepts genuinely owned by no single aggregate (`Money`, `Address`), not for cross-context references.

   ## Consequences

   **Positive:**
   - SupplyChain and Procurement can change their own `SupplierId` independently (e.g. if SupplyChain later needed extra fields on its identity, Procurement would be unaffected).
   - No hidden coupling: reading Procurement's code never requires understanding SupplyChain's `Supplier` aggregate internals.

   **Negative / Trade-offs:**
   - Two types with the same name and shape exist in the codebase; an explicit alias (`SupplyChainSupplierId`) is needed wherever both are in scope at once (see `Program.cs`).
   - Slightly more code than a single shared type would require.

   ---

   # ADR-0003: Aggregate Roots Enforce Identity-Based Equality and Full Encapsulation

   **Status:** Accepted
   **Note:** Partially superseded by [ADR-0007](#adr-0007-purchaseorderitem-grows-an-intention-revealing-mutation-method), which lets PurchaseOrderItem change its own quantity through a controlled, intention-revealing method.

   ## Context

   `Supplier` and `PurchaseOrder` are aggregate roots, not value objects: two instances with identical property values are still different real-world entities if their identity differs, and the same entity, mutated, should still be considered "the same" as it was before the mutation. As plain C# classes (not `record`s), they don't get `Equals()`/`GetHashCode()` for free the way value objects do.

   ## Decision Drivers

   - Entity equality must be based on identity, never on the current values of mutable state.
   - Internal collections (`PurchaseOrder`'s items) must never be exposed for external mutation.
   - State changes must only happen through intention-revealing methods, never a public setter.

   ## Considered Options

   1. Override `Equals()`/`GetHashCode()` on identity only; expose internal collections through `IReadOnlyList<T>`, never the backing `List<T>` *(Chosen)*
   2. Leave the default `object` reference-based equality (`==`) as-is, don't override anything
   3. Expose internal collections directly as `List<T>`, trusting callers not to mutate them

   ## Decision

   Where an aggregate root validates its creation preconditions, in the constructor rather than per-property, is settled by [ADR-0011](#adr-0011-where-domain-validation-lives). This ADR covers identity equality and collection encapsulation.

   `Supplier` and `PurchaseOrder` both override `Equals()`/`GetHashCode()` to compare only identity: `Supplier` by `Id` (its `SupplierId`), `PurchaseOrder` by `OrderNumber` (its natural business key, rather than a generated surrogate identity). `PurchaseOrder`'s backing `_items` field is `private`; the aggregate exposes it only through `Items => _items.AsReadOnly()`, an `IReadOnlyList<PurchaseOrderItem>` that supports enumeration but has no `Add`/`Remove`/indexer setter, so no caller can mutate the collection from outside. This is exposed from the same step the collection itself is introduced (Feature 3), since `US003`'s own acceptance criteria already require verifying that an added item is really there. `PurchaseOrderItem` goes a step further than being read-only from outside: its properties have no setters at all (`{ get; }` only), so once `PurchaseOrder.AddItem()` constructs one, nothing (including `PurchaseOrder` itself) can change it afterward; a new item is created for each addition instead. Since `PurchaseOrderItem` instances are reachable from outside the aggregate (via `Items`), it also gets `Equals()`/`GetHashCode()`/`ToString()`, comparing by value (`ProductId`, `Quantity`, `UnitPrice`): it has no identity type of its own, so value equality is what lets a caller meaningfully compare or print one.

   ```csharp
   public override bool Equals(object? obj)
   {
       return obj is PurchaseOrder other && OrderNumber == other.OrderNumber;
   }
   ```

   ## Consequences

   **Positive:**
   - Aggregates behave correctly in collections (`HashSet<T>`, or as a `Dictionary<TKey, TValue>` key) even after their mutable state changes.
   - No caller can ever put an aggregate into an invalid state by reaching past its public methods.

   **Negative / Trade-offs:**
   - `IReadOnlyList<T>.AsReadOnly()` is only a shallow, view-based guard: it blocks structural changes to the list itself, but doesn't prevent mutating an already-retrieved `PurchaseOrderItem`. At this point (Feature 3) `PurchaseOrderItem` has no setters at all, so the point is moot; US006 later gives it a `private` setter behind `IncreaseQuantity()` (see [ADR-0007](#adr-0007-purchaseorderitem-grows-an-intention-revealing-mutation-method)), still with no way for an outside caller to mutate a retrieved item.
   - `PurchaseOrder`'s identity is a plain `string` (`OrderNumber`), not a wrapped identity `record` like `SupplierId`/`ProductId` (see ADR-0001): it's a genuine business key supplied by the caller, not a generated surrogate, so wrapping it wouldn't add the same primitive-obsession protection ADR-0001 argues for elsewhere.

   ---

   # ADR-0004: Monetary Amounts as `decimal`, Never `double`

   **Status:** Accepted

   ## Context

   Money throughout the system needs to support fractional amounts and represent them exactly. Unlike Java, C# has a built-in `decimal` type designed specifically for financial and monetary calculations: base-10, fixed-point, no binary floating-point rounding error the way `double`/`float` have.

   ## Decision Drivers

   - Eliminate floating-point rounding error in monetary calculations.
   - Use the correct built-in type rather than a manual cents-as-integer scheme.

   ## Considered Options

   1. `Money.Amount` as `decimal` *(Chosen)*
   2. `double`/`float`
   3. Integer/long cents representation

   ## Decision

   `Money.Amount` is a `decimal` from the start, and `Money`'s guards reject a negative amount. Callers pass `decimal` literals with the `m` suffix (`25.99m`). The currency is a `Currency` value object, not a bare `string` (see [ADR-0006](#adr-0006-currency-as-a-dedicated-value-object)).

   ## Consequences

   **Positive:**
   - No floating-point rounding surprises anywhere money is calculated: `decimal` is exact for base-10 fractions like currency amounts, unlike `double`/`float`.

   **Negative / Trade-offs:**
   - Every caller must remember to use `decimal` literals (e.g. `25.99m`, with the `m` suffix), not `double`; nothing in the type system prevents passing a converted `double` value that already lost precision before it ever reached `Money`.

   ---

   # ADR-0005: Value Objects as readonly record struct

   **Status:** Accepted

   ## Context

   Every value object here (`Money`, `Currency`, `Address`, `SupplierId`, `ProductId`) has no identity of its own and is compared by state. C# offers two shapes for an immutable record: a `record` (reference type, heap-allocated) or a `readonly record struct` (value type, stack-allocated or inlined into its container). Both give generated structural equality for free. `Money` in particular is constructed constantly, once per line item, once per per-item subtotal `PurchaseOrderItem.CalculateItemTotal()` produces, once per running total, so allocation pressure is real.

   A `readonly record struct` comes with one catch: C# structs always have an implicit parameterless constructor the language does not allow removing. `default(Money)` (with `Amount == 0`, `Currency == default`) is legal anywhere a `Money` is expected, and it never runs the type's `init` accessors or any constructor. The same is true of `default(Address)`, `default(SupplierId)`, `default(ProductId)`.

   ## Decision Drivers

   - Reduce heap allocation for types constructed constantly.
   - Value semantics (copy, not reference) fit "an amount of money", "a supplier's address", and "an identifier" more naturally than reference semantics.
   - Preserve the "always valid" guarantee value objects are supposed to have, as far as the type system allows.
   - One shape for every value object, no arbitrary split where some are structs and others classes.

   ## Considered Options

   1. Every value object is a `readonly record struct` from the outset *(Chosen)*
   2. Only `Money` a struct (it is the allocation-heavy one), the rest `record` class
   3. Every value object a `record` class

   ## Decision

   Every value object without an identity of its own is a `readonly record struct` from the moment it is created. They are stack-allocated or inlined, and `==` is structural and free. The `default(T)` gap is closed the same way for all of them, two mitigations applied uniformly:

   - **Each blocks its parameterless constructor** (`public T() => throw ...`), so `new T()` fails loudly. Only `default(T)` still slips through, and that is a deliberate, visible choice at the call site, not an accident.
   - **Every aggregate boundary that receives one guards it with `== default`.** `Supplier`'s and `PurchaseOrder`'s constructors, `PurchaseOrder.AddItem()`, and `PurchaseOrderItem`'s own constructor all do this. `Money.Add()` / `Multiply()` additionally check `Currency == default`, since a `default(Money)` would otherwise compute a silently wrong total. Every one of those guards has a test.

   ```csharp
   public Supplier(SupplierId id, string name, Address address)
   {
       if (id == default)
           throw new ArgumentException("Supplier ID is required.", nameof(id));
       ArgumentException.ThrowIfNullOrWhiteSpace(name);
       if (address == default)
           throw new ArgumentException("Supplier address is required.", nameof(address));

       Id = id;
       Name = name;
       Address = address;
   }
   ```

   ## Consequences

   **Positive:**
   - No heap allocation for any value object: every `Money`/`Address`/`SupplierId`/`ProductId`/`Currency` instance is stack-allocated or inlined into its container.
   - Value semantics read naturally: `PurchaseOrderItem.CalculateItemTotal()` reads `UnitPrice * Quantity`, ordinary numeric syntax; identity and address comparisons use plain `==`.
   - One rule across the whole domain model, easy to teach and to extend: a new value object just follows the same pattern.

   **Negative / Trade-offs:**
   - `default(Money)`/`default(Address)`/`default(SupplierId)`/`default(ProductId)` are all constructible without validation; nothing in the type itself prevents this. The guarantee shifts to the aggregate boundary: every constructor and every `AddItem()`-style method that accepts one must remember to guard it, and forgetting one is a real, silent bug. Held through discipline plus a test for every guard, a materially weaker guarantee than "the type itself refuses to exist invalid".
   - None of these guards can be an `ArgumentNullException` check, since a non-nullable struct parameter can never be `null`, the compiler proves it. `== default` is the only check available, and a unit test asserting rejection of a `null` argument would not compile; the suite asserts rejection of the `default` value instead.
   - There is no way to express "this parameter cannot be the struct's own default" in the type system the way non-nullability expresses "cannot be null" for a reference type.

   ---

   # ADR-0006: Currency as a Dedicated Value Object

   **Status:** Accepted

   ## Context

   Both `Money` and `PurchaseOrder` carry a currency. A bare 3-letter `string` would put the same validation rule in two places (free to drift apart), and would let any string be compared to a currency code with no compiler help distinguishing "a currency code" from "any other 3-character string" (a product SKU, say). This is the same primitive-obsession gap ADR-0001 closes for `SupplierId` and `ProductId`.

   ## Decision Drivers

   - One validated definition of "what a currency code is", shared by every type that holds one.
   - Compiler help distinguishing a currency code from any other short string.
   - Consistency with the identity value objects: `SupplierId` and `ProductId` are already `readonly record struct`s (ADR-0005).

   ## Considered Options

   1. `Currency` as its own `readonly record struct`, used by both `Money` and `PurchaseOrder` *(Chosen)*
   2. A raw `string` with validation deduplicated into a static helper method
   3. `Currency` as a `record` (class)

   ## Decision

   `Currency` is a `readonly record struct` in the shared kernel, created in Feature 2 alongside `PurchaseOrder` and used by `Money` in Feature 3. Its `Code` property validates inside its own `init` accessor (C# 14 `field` keyword): not null or blank, exactly `CodeLength` (a named `const`, not a literal `3`) ASCII letters, stored upper-cased. Its parameterless constructor is blocked (`public Currency() => throw ...`), the same defensive pattern every value object here uses. `Money` and `PurchaseOrder` each keep a `string`-accepting constructor overload that wraps the code in `new Currency(...)`, so call sites that only have a raw `"USD"` on hand stay simple.

   ```csharp
   private const int CodeLength = 3;

   public string Code
   {
       get => field ?? string.Empty;
       init
       {
           ArgumentException.ThrowIfNullOrWhiteSpace(value);
           if (value.Length != CodeLength || !value.All(char.IsAsciiLetter))
               throw new ArgumentException($"Currency must be a valid {CodeLength}-letter ISO code.", nameof(Code));
           field = value.ToUpperInvariant();
       }
   }
   ```

   ## Consequences

   **Positive:**
   - One validated definition of "what a currency code is", instead of a rule copied into every type that holds a currency.
   - The `Code` property normalizes to uppercase and rejects non-letter characters, stricter than a length-only check.
   - `Money.Add()` / `Multiply()` compare `Currency` values directly, and their `Currency == default` guard (ADR-0005) also catches a caller passing an uninitialized `default(Money)`.

   **Negative / Trade-offs:**
   - Same `readonly record struct` caveat ADR-0005 documents: `default(Currency)` is constructible without validation (`new Currency()` throws, but `default(Currency)` does not); `Money`'s and `PurchaseOrder`'s own `init` / constructor guards reject a `default` currency one level up.
   - One more type to import wherever a currency code crosses an API boundary, though the convenience `string` constructors keep simple call sites unchanged.

   ---

   # ADR-0007: PurchaseOrderItem Grows an Intention-Revealing Mutation Method

   **Status:** Accepted (supersedes part of ADR-0003)

   ## Context

   ADR-0003 decided, back in Feature 3, that `PurchaseOrderItem` should have no setters at all. US006's merge feature revisits that: `PurchaseOrderItem` is explicitly documented, in its own class summary, as an entity managed by the `PurchaseOrder` aggregate, not a value object. Value objects earn immutability by having no identity of their own; entities are defined by the opposite, a life cycle and mutable state, tracked by something other than their current values. Keeping `PurchaseOrderItem` fully immutable would force `PurchaseOrder.AddItem()` to reconstruct the entity from outside on every merge (reading `existing.Quantity`/`existing.UnitPrice`, then building a brand-new `PurchaseOrderItem` to replace it in the list), which asks the aggregate to know how to rebuild one of its own entities instead of asking the entity to change itself, the opposite of "tell, don't ask."

   ## Decision Drivers

   - `PurchaseOrderItem` is documented as an entity, not a value object; DDD does not require entities to be immutable, only that state changes go through intention-revealing methods.
   - "Tell, don't ask": the aggregate should tell an item to increase its own quantity, not read its state and rebuild it.
   - Whatever changes here still needs to keep the guard ADR-0003's Decision Drivers actually require: "state changes must only happen through intention-revealing methods, never a public setter."

   ## Considered Options

   1. Add an `internal void IncreaseQuantity(int)` method, backed by a `private set` on `Quantity` *(Chosen)*
   2. Keep `PurchaseOrderItem` fully immutable, and have `PurchaseOrder.AddItem()` rebuild a replacement instance on every merge
   3. Make `Quantity`'s setter `public`, let any caller change it directly

   ## Decision

   `Quantity` keeps a `get`, but its `set` is now `private`, validated the same way the constructor already validates it (`ArgumentOutOfRangeException.ThrowIfNegativeOrZero`, via the C# 14 `field` keyword). A new `internal void IncreaseQuantity(int additionalQuantity)` method is the only thing that can invoke that setter from outside the property itself; `PurchaseOrder.AddItem()` calls `existing.IncreaseQuantity(quantity)` on a price match instead of rebuilding the item. This satisfies ADR-0003's actual Decision Driver (no *public* setter, every change goes through a named method) more directly than a fully-immutable `PurchaseOrderItem` would.

   ```csharp
   public int Quantity
   {
       get;
       private set
       {
           ArgumentOutOfRangeException.ThrowIfNegativeOrZero(value);
           field = value;
       }
   }

   internal void IncreaseQuantity(int additionalQuantity)
   {
       ArgumentOutOfRangeException.ThrowIfNegativeOrZero(additionalQuantity);
       Quantity += additionalQuantity;
   }
   ```

   ## Consequences

   **Positive:**
   - `PurchaseOrder.AddItem()` no longer needs to know how to reconstruct a valid `PurchaseOrderItem`; it delegates the state change to the entity itself.
   - One fewer object allocated per merge (no replacement instance, no list-index rewrite).
   - Matches how entities are meant to behave in DDD more directly than an all-immutable value-object-style implementation does for something that is not a value object.

   **Negative / Trade-offs:**
   - `PurchaseOrderItem` still overrides `Equals()`/`GetHashCode()` by value (`ProductId`, `Quantity`, `UnitPrice`, unchanged from ADR-0003). A mutable entity with a value-based hash code is a known hazard if an instance is ever placed in a `HashSet<T>`/used as a `Dictionary` key and then mutated afterward, its hash code would change and corrupt the collection. Today `_items` is a plain `List<PurchaseOrderItem>`, never a hash-based collection, so the hazard is latent, not active; a future revision that needs `PurchaseOrderItem` in a hash-based collection would need to revisit this.
   - `IncreaseQuantity()` is `internal`, reachable from anything in the same assembly, not only from `PurchaseOrder`; nothing currently calls it from elsewhere, but the compiler doesn't enforce that only the owning aggregate can call it, the way a `private` nested-class relationship would.

   ---

   # ADR-0008: AddItem Merges a Duplicate Product, Rejecting a Conflicting Unit Price

   **Status:** Accepted

   ## Context

   US006 arrived after 1.0.0 shipped: a real procurement team using the shipped product reported that adding the same product twice to a purchase order created two separate, confusing line items instead of one combined quantity. `AddItem()`'s Feature 3 behavior always appended a new `PurchaseOrderItem`, this was never wrong, just incomplete: US003's acceptance criteria only ever described adding a single item, never a repeat.

   Once merging is on the table, a second question follows: if the product is re-added at a *different* unit price than the existing line (the supplier's price changed since the order was started, or the caller made a typo), what should happen? Silently keeping the original price hides a real price change; silently overwriting it hides a possible typo; and either way, a caller who passed an explicit price gets no signal it was ignored. Silently resolving a conflict the caller didn't know they created is exactly what DDD invariant protection argues against: an aggregate should fail loudly when an operation conflicts with state it already committed.

   ## Decision Drivers

   - Real procurement feedback: duplicate lines for the same product on one order are a genuine usability problem, not a hypothetical one.
   - A purchase order line's price, once committed, represents a real agreement; silently overwriting *or* silently ignoring a conflicting price both hide a potential real-world discrepancy from whoever reads the result.
   - Failing fast on an ambiguous instruction is safer for financial data than resolving the ambiguity silently, in either direction.
   - Whatever gets decided has to be traceable to an explicit acceptance criterion in `docs/user-stories.md`, not inferred from the code.

   ## Considered Options

   1. Merge quantities when the re-added price matches the existing line; throw `InvalidOperationException` when it conflicts *(Chosen)*
   2. Merge quantities, always keep the existing line's original price, silently discard the newly provided one
   3. Merge quantities, always overwrite with the newly provided price
   4. Keep appending a separate line item per `AddItem()` call, regardless of repeats (the Feature 3 behavior)

   ## Decision

   `AddItem()` looks for an existing `PurchaseOrderItem` with the same `ProductId` before appending anything. If one exists and the newly computed `Money` matches its `UnitPrice`, the quantities merge via `existing.IncreaseQuantity(quantity)` (see [ADR-0007](#adr-0007-purchaseorderitem-grows-an-intention-revealing-mutation-method)). If the price differs, `AddItem()` throws `InvalidOperationException` naming both the conflicting price and the existing one, and the order's state is left unchanged (the exception is thrown before any mutation). If no matching item exists, behavior is unchanged: a new line is appended using the price provided.

   ```csharp
   var existing = _items.Find(item => item.ProductId == productId);
   if (existing is not null)
   {
       if (existing.UnitPrice != unitPrice)
           throw new InvalidOperationException(
               $"Cannot add product {productId} at {unitPrice}; the order already has it at {existing.UnitPrice}.");
       existing.IncreaseQuantity(quantity);
       return;
   }
   ```

   ## Consequences

   **Positive:**
   - Matches the real usability complaint US006 was written for: no more duplicate lines for the same product.
   - No silent data loss: a caller who passes a conflicting price for an existing line finds out immediately, instead of the system quietly keeping a value different from what was requested.
   - The order's state is never left half-changed: the price check runs before `IncreaseQuantity()`, so a rejected call is a no-op.

   **Negative / Trade-offs:**
   - A legitimate supplier price change mid-order now requires the caller to handle the exception explicitly (choose a different `ProductId`, or wait for a future explicit `UpdateItemPrice()` method); there is no built-in way to intentionally change an existing line's price.
   - `AddItem()` does two related but distinct things (append, or merge-or-reject) behind one method name; a reader has to follow the branch to see that a repeat `ProductId` is handled specially. The XML doc comment calls this out.

   ---

   # ADR-0009: DateOnly for Purchase Order Dates

   **Status:** Accepted

   ## Context

   A purchase order date is a calendar business date, not an instantaneous timestamp: nothing in this domain ever needs the time-of-day or time-zone component a `DateTime` carries. Modeling it as a `DateTime` would mean every construction carries a meaningless `00:00:00` (or worse, an arbitrary time-of-day from `DateTime.UtcNow`), and two orders created on the same calendar date at different times would compare as having different dates.

   ## Decision Drivers

   - Represent domain intent precisely: an order date is a date, not a timestamp.
   - Eliminate a whole category of bugs this domain never needed to worry about: time-zone conversion, Daylight Saving Time edge cases, and two dates differing only by time-of-day.
   - Keep the common "I have a `DateTime.UtcNow`, I want today's date" call site simple.

   ## Considered Options

   1. Model `OrderDate` as a `DateOnly`, with a `DateTime`-accepting convenience constructor that converts internally *(Chosen)*
   2. Model `OrderDate` as a `DateOnly`, with no `DateTime` overload, forcing every caller to convert explicitly
   3. Model `OrderDate` as a `DateTime`

   ## Decision

   `PurchaseOrder.OrderDate` is a `DateOnly` from Feature 2, and the canonical constructor takes a `DateOnly orderDate`. A convenience constructor also accepts a `DateTime`, converting it via `DateOnly.FromDateTime(orderDate)` before delegating, so a caller holding a `DateTime.UtcNow` (like `Program.cs`) stays simple; only the discarded time-of-day component differs, and it was never meaningful.

   ```csharp
   public PurchaseOrder(string orderNumber, SupplierId supplierId, DateTime orderDate, string currency)
       : this(orderNumber, supplierId, DateOnly.FromDateTime(orderDate), new Currency(currency)) { }
   ```

   ## Consequences

   **Positive:**
   - `OrderDate` says exactly what it means: a calendar date, nothing more.
   - Two `PurchaseOrder`s created on the same calendar date but at different times of day correctly compare as having the same `OrderDate`.
   - No time-zone conversion code is needed anywhere: `DateOnly` sidesteps the whole category of bugs by construction.

   **Negative / Trade-offs:**
   - Three constructor overloads (`DateOnly`+`Currency`, `DateOnly`+`string`, `DateTime`+`string`) add a small amount of surface area; a reader has to notice `DateOnly`+`Currency` is the canonical one and the rest are conveniences.
   - Any future code that genuinely needs a time-of-day for something purchase-order-related (an audit timestamp, for instance) would need its own separate property, not `OrderDate`.

   ---

   # ADR-0010: Presentation Formatting via C# 14 Extension Members

   **Status:** Accepted

   ## Context

   `Program.cs` has always interpolated domain objects directly into `Console.WriteLine` calls (`$"Purchase Order {order.OrderNumber} ... in {order.Currency}"`, `$"{money.Amount:N2} {money.Currency.Code}"` fragments duplicated wherever a `Money` is printed). Neither `PurchaseOrder` nor `Money` has ever had a `ToString()` written specifically for end-user display, and adding one would mix a presentation concern into the domain model, the same kind of coupling the domain layer has otherwise stayed free of throughout this guide.

   ## Decision Drivers

   - Keep domain models free of presentation concerns: `PurchaseOrder`/`Money` should describe the domain, not how it looks on a console.
   - Avoid duplicating the same formatting expression at every call site.
   - Prefer a solution discoverable at the call site (`order.Summary`, `money.Display`), not a separate static helper class the caller has to know to look for.

   ## Considered Options

   1. C# 14 extension members (`extension(T target) { public string Prop => ...; }`) in dedicated `*.Presentation` namespaces *(Chosen)*
   2. Traditional static extension methods (`public static string Summary(this PurchaseOrder order) => ...`)
   3. Add a `ToString()` override (or a `Display()` method) directly on `PurchaseOrder`/`Money`

   ## Decision

   Two `internal static class ConsoleFormatting` types, one per bounded context that needs one (`Procurement.Presentation`, `Shared.Presentation`), each declaring an `extension(T target) { ... }` block with a read-only property: `PurchaseOrder.Summary` and `Money.Display`. `internal` keeps these presentation helpers from leaking outside this project's own console entry point; `Program.cs` imports both `*.Presentation` namespaces and calls `purchaseOrder.Summary`/`item.CalculateItemTotal().Display` directly, reading exactly like a property on the domain type itself, even though neither type was touched.

   ```csharp
   extension(Money money)
   {
       public string Display => $"{money.Amount:N2} {money.Currency.Code}";
   }
   ```

   ## Consequences

   **Positive:**
   - `PurchaseOrder`/`Money` stay exactly as focused on domain behavior as every other value object and aggregate in this codebase.
   - Call sites read naturally (`order.Summary`), same discoverability as a real property, without actually being one.
   - Formatting logic lives in exactly one place per concept, instead of being copy-pasted into every `Console.WriteLine`.

   **Negative / Trade-offs:**
   - Extension members are a C# 14 language feature; a reader unfamiliar with it might mistake `order.Summary` for a real property on `PurchaseOrder` until they notice the `using Acme.OOProgramming.Procurement.Presentation;` import it actually requires.
   - The presentation namespace must be imported wherever its extension members are used, one more `using` to remember, easy to miss without an IDE's auto-import quick-fix.

   ---

   # ADR-0011: Where Domain Validation Lives

   **Status:** Accepted

   ## Context

   Every type in the domain model rejects invalid input somewhere. C# offers two natural places: inside a property's own `init` / `set` accessor (checked on every assignment path, the moment the value lands), or in the constructor body (checked once, when the object is assembled). Early on the codebase was inconsistent: value objects and `Supplier`'s `Name` / `Address` validated in accessors, `PurchaseOrder` validated in its constructor, and `Supplier`'s own `Id` had no guard at all. That split was inherited, never decided.

   ## Decision Drivers

   - An object should be impossible to observe in an invalid state.
   - A rule that compares two or more fields can only run where all of them are visible at once.
   - Validating field by field can leave an object half-mutated: the earlier fields already changed, then a later one throws.
   - One rule a student can state in a sentence, applied the same way to every type of the same kind.
   - `with` expressions and object initializers reach a property's accessor without going through the constructor.

   ## Considered Options

   1. Value objects validate per-property in their `init` accessor; aggregate roots and entities validate in the constructor and in the methods that change state *(Chosen)*
   2. Everything, value objects and aggregates alike, validates per-property in `init` accessors
   3. Everything validates in the constructor; properties carry no validation

   ## Decision

   - **Value objects** (`Currency`, `Money`, `Address`, `SupplierId`, `ProductId`) validate **in each property's own `init` accessor** (ADR-0001). Every rule is a single-field check in isolation (`Amount` non-negative, a three-letter code, a non-empty string), so an accessor is enough, and the rule sits next to the property and its `<exception>` doc.
   - **Aggregate roots** (`Supplier`, `PurchaseOrder`) validate **in the constructor**, before assigning anything; properties stay `{ get; }`. The constructor is the aggregate's single entry point and the only place a rule spanning more than one field could go, and guarding there keeps a rejected construction atomic, nothing is assigned until every guard passes. Each value-object parameter still gets its `== default` guard here (ADR-0005), on top of the value object's own validation.
   - **Entities** (`PurchaseOrderItem`) validate in their constructor and in the methods that mutate them (`IncreaseQuantity`, the `private set` on `Quantity`), for the same reasons as an aggregate root.

   This matches the mainstream .NET DDD guidance: Microsoft's own domain-model-validation guidance puts entity validation "in domain entity constructors or in methods that can update the entity", and shows how field-by-field validation can leave an object invalid partway through.

   ```csharp
   // value object: in the property's init accessor
   public string Code
   {
       get => field ?? string.Empty;
       init
       {
           ArgumentException.ThrowIfNullOrWhiteSpace(value);
           if (value.Length != CodeLength || !value.All(char.IsAsciiLetter))
               throw new ArgumentException($"Currency must be a valid {CodeLength}-letter ISO code.", nameof(Code));
           field = value.ToUpperInvariant();
       }
   }

   // aggregate root: in the constructor
   public Supplier(SupplierId id, string name, Address address)
   {
       if (id == default)
           throw new ArgumentException("Supplier ID is required.", nameof(id));
       ArgumentException.ThrowIfNullOrWhiteSpace(name);
       if (address == default)
           throw new ArgumentException("Supplier address is required.", nameof(address));

       Id = id;
       Name = name;
       Address = address;
   }
   ```

   ## Consequences

   **Positive:**
   - One rule, stated in a sentence: a value validates where it lands (its property), an aggregate validates where it is assembled (its constructor).
   - An aggregate that later grows a cross-field invariant already has the right place for it, no restructuring.
   - A rejected aggregate construction assigns nothing until every guard has passed.

   **Negative / Trade-offs:**
   - A value object and an aggregate root are validated in different places, so a reader has to know which kind of type they are looking at.
   - An aggregate root's `<exception>` documentation lives on the constructor, away from the individual properties.
   - A value object's `init`-accessor guard can still be reached by a `with` expression or object initializer; acceptable here because each such guard is complete on its own, there is no multi-field rule for `with` to violate.
   ````
   </details>

   ```
   git add .
   git commit -m "docs(adr): document value object, context mapping, encapsulation, money, struct, currency, entity mutation, merge, date, and presentation decisions."
   git push
   ```

3. **Add a Requirements Traceability Matrix to the top of `docs/user-stories.md`**, right after the title, before the individual user stories: one row per story, mapping it to the bounded context, the aggregate/entity it lives on, and the method that implements it. No Test Suite column yet, there's no test suite yet, `## Testing` below adds one to this same table if you get to it.

   <details>
   <summary>docs/user-stories.md (addition, insert before "## US001")</summary>

   ```markdown
   ## Requirements Traceability Matrix

   | Story ID | User Story Title | Bounded Context | Aggregate / Entity | Primary Implementation |
   |:---|:---|:---|:---|:---|
   | **US001** | Register a Supplier | SupplyChain Context | [`Supplier`](../Acme.OOProgramming/SupplyChain/Domain/Model/Aggregates/Supplier.cs) | `Supplier(id, name, address)` |
   | **US002** | Create a Purchase Order | Procurement Context | [`PurchaseOrder`](../Acme.OOProgramming/Procurement/Domain/Model/Aggregates/PurchaseOrder.cs) | `PurchaseOrder(orderNumber, supplierId, orderDate, currency)` |
   | **US003** | Add Items to a Purchase Order | Procurement Context | [`PurchaseOrder`](../Acme.OOProgramming/Procurement/Domain/Model/Aggregates/PurchaseOrder.cs), [`PurchaseOrderItem`](../Acme.OOProgramming/Procurement/Domain/Model/Aggregates/PurchaseOrderItem.cs) | `AddItem(productId, quantity, unitPriceAmount)` |
   | **US004** | Calculate Purchase Order Item Subtotal | Procurement Context | [`PurchaseOrderItem`](../Acme.OOProgramming/Procurement/Domain/Model/Aggregates/PurchaseOrderItem.cs) | `CalculateItemTotal()` |
   | **US005** | Calculate Purchase Order Total | Procurement Context | [`PurchaseOrder`](../Acme.OOProgramming/Procurement/Domain/Model/Aggregates/PurchaseOrder.cs) | `CalculateTotal()` |
   | **US006** | Merge Duplicate Items in a Purchase Order | Procurement Context | [`PurchaseOrder`](../Acme.OOProgramming/Procurement/Domain/Model/Aggregates/PurchaseOrder.cs), [`PurchaseOrderItem`](../Acme.OOProgramming/Procurement/Domain/Model/Aggregates/PurchaseOrderItem.cs) | `AddItem(productId, quantity, unitPriceAmount)` (merge-or-reject branch) |

   ---
   ```
   </details>

   ```
   git add .
   git commit -m "docs(user-stories): add requirements traceability matrix."
   git push
   ```

4. **Update `README.md`** now that the ADRs exist. Replace the file from `## Prepare the First Release` with the version below: the domain model is unchanged since `1.0.0`, this adds `see ADR-NNNN` links throughout, the two new US006 rules, and a `docs/adrs.md` row in `## Project Documentation`.

   <details>
   <summary>README.md</summary>

   ````markdown
   # OOP Sample (`oop-sample`)

   [![.NET](https://img.shields.io/badge/.NET-10-purple.svg)](https://dotnet.microsoft.com/)
   [![C#](https://img.shields.io/badge/C%23-14-blue.svg)](https://learn.microsoft.com/dotnet/csharp/)
   [![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](LICENSE.md)

   `oop-sample` is a sample C# console application demonstrating **Object-Oriented Programming (OOP)** and **Domain-Driven Design (DDD)** principles across two bounded contexts, SupplyChain and Procurement, and a shared kernel.

   **Author**: Web Applications Developer Team  
   **License**: See [LICENSE.md](LICENSE.md) for details.

   ---

   ## Technical Stack & Modern Features

   - **Runtime & Framework**: .NET 10.0 (C# 14.0)
   - **C# 14 & .NET 10 Features**:
     - **Extension Members (`extension(T)`)**: presentation formatting (`order.Summary`, `money.Display`) decoupled from the domain models.
     - **`field` Keyword**: property validation and null-safe fallback without an explicit private backing field.
     - **Struct Parameterless Constructor Safety**: every `readonly record struct` value object throws `InvalidOperationException` from `new X()`, and falls back safely on `default`.
     - **UUIDv7 Identifiers**: time-ordered identifiers via `Guid.CreateVersion7()` (`ProductId`).
     - **`DateOnly` Temporal Modeling**: a purchase order's date has no time-of-day or time zone.
     - **Modern Throw Helpers**: `ArgumentException.ThrowIfNullOrWhiteSpace` and friends, not hand-written null checks.

   ---

   ## Solution Structure

   ```text
   oop-sample/
   ├── Acme.OOProgramming/                     # Main domain & console application project
   │   ├── Procurement/                        # Procurement Bounded Context
   │   │   ├── Domain/Model/
   │   │   │   ├── Aggregates/                 # PurchaseOrder (AR), PurchaseOrderItem (Entity)
   │   │   │   └── ValueObjects/               # ProductId (UUIDv7), SupplierId
   │   │   └── Presentation/                   # ConsoleFormatting (C# 14 extension members)
   │   ├── SupplyChain/                        # Supply Chain Bounded Context
   │   │   └── Domain/Model/
   │   │       ├── Aggregates/                 # Supplier (AR)
   │   │       └── ValueObjects/               # SupplierId
   │   ├── Shared/                             # Shared Kernel
   │   │   ├── Domain/Model/ValueObjects/      # Currency (ISO 4217), Money, Address
   │   │   └── Presentation/                   # ConsoleFormatting (C# 14 extension members)
   │   └── Program.cs                          # Application entry point & demo scenarios
   ├── docs/                                   # Architecture & requirements documentation
   │   ├── adrs.md                             # Architecture Decision Records (ADR-0001 through ADR-0011)
   │   ├── class-diagram.puml                  # PlantUML domain model class diagram
   │   └── user-stories.md                     # User stories (US001-US006) & Requirements Traceability Matrix
   ├── CHANGELOG.md                            # Project release notes & version history
   ├── LICENSE.md                              # Project license
   └── README.md                               # Project overview & guide
   ```

   ---

   ## Bounded Contexts & Domain Model

   ### 1. `Acme.OOProgramming.SupplyChain` (Supply Chain Management)
   - **`Supplier`** (*Aggregate Root*): a vendor with identity and location.
   - **`SupplierId`** (*Value Object*): strongly-typed identifier, owned by SupplyChain.

   ### 2. `Acme.OOProgramming.Procurement` (Procurement)
   - **`PurchaseOrder`** (*Aggregate Root*): purchase order invariants, currency consistency, and item lifecycle; `OrderDate` is a `DateOnly`, a calendar date with no time-of-day or time zone component (see [ADR-0009](docs/adrs.md#adr-0009-dateonly-for-purchase-order-dates)).
   - **`PurchaseOrderItem`** (*Entity*): managed exclusively by `PurchaseOrder`, its constructor is `internal`.
   - **`ProductId`** (*Value Object*): time-ordered identifier generated with UUIDv7 (`Guid.CreateVersion7()`).
   - **`SupplierId`** (*Value Object*): Procurement's own copy of the concept, deliberately decoupled from SupplyChain's (see [ADR-0002](docs/adrs.md#adr-0002-each-bounded-context-owns-its-own-reference-types)).
   - **`Presentation.ConsoleFormatting`** (`order.Summary`): console-only formatting kept out of the aggregate itself, via a C# 14 extension member (see [ADR-0010](docs/adrs.md#adr-0010-presentation-formatting-via-c-14-extension-members)).

   ### 3. `Acme.OOProgramming.Shared` (Shared Kernel)
   - **`Money`** (*Value Object*): `decimal` amount + validated `Currency`, `readonly record struct` for value semantics and zero heap allocation.
   - **`Currency`** (*Value Object*): validated 3-letter ISO code, `readonly record struct` (see [ADR-0006](docs/adrs.md#adr-0006-currency-as-a-dedicated-value-object)).
   - **`Address`** (*Value Object*): international postal address, `readonly record struct`.
   - **`Presentation.ConsoleFormatting`** (`money.Display`): console-only formatting kept out of `Money` itself, via a C# 14 extension member (see [ADR-0010](docs/adrs.md#adr-0010-presentation-formatting-via-c-14-extension-members)).

   ---

   ## Key Domain Rules & Design Invariants

   - **Aggregate invariant encapsulation**: `PurchaseOrder` strictly controls the creation and lifecycle of `PurchaseOrderItem`.
   - **Single-currency rule**: every item in a `PurchaseOrder` is priced in the order's own currency.
   - **Currency-safe arithmetic**: `Money` rejects cross-currency operations and negative amounts; its `+`/`*` operators call the same validated methods underneath.
   - **Duplicate line item handling**: `PurchaseOrder.AddItem` merges quantities when an existing `ProductId` is re-added at the same unit price; re-adding it at a different price throws instead of silently picking one (see [ADR-0008](docs/adrs.md#adr-0008-additem-merges-a-duplicate-product-rejecting-a-conflicting-unit-price)).
   - **Uniform value-type adoption**: `Money`, `Currency`, `Address`, `SupplierId`, and `ProductId` are all `readonly record struct`s, each `default`-guarded at every aggregate boundary that consumes one (see [ADR-0005](docs/adrs.md#adr-0005-value-objects-as-readonly-record-struct) and [ADR-0006](docs/adrs.md#adr-0006-currency-as-a-dedicated-value-object)).
   - **Cross-context references**: each bounded context owns its own copy of any identifier it references from another context, rather than sharing one type.
   - **Presentation decoupling**: display formatting (`order.Summary`, `money.Display`) lives in dedicated `*.Presentation` namespaces, never on the domain models themselves (see [ADR-0010](docs/adrs.md#adr-0010-presentation-formatting-via-c-14-extension-members)).

   ---

   ## Project Documentation

   | Document | Description |
   | :--- | :--- |
   | [**Architecture Decision Records (ADRs)**](docs/adrs.md) | Eleven architectural decisions (ADR-0001 through ADR-0011). |
   | [**User Stories & RTM**](docs/user-stories.md) | User stories (US001-US006) and Requirements Traceability Matrix. |
   | [**Class Diagram**](docs/class-diagram.puml) | PlantUML class diagram of bounded contexts, aggregates, entities, and value objects. |
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
   dotnet run --project Acme.OOProgramming
   ```
   ````
   </details>

   ```
   git add .
   git commit -m "docs(readme): document the currency, presentation, and date-only work."
   git push
   ```

5. **Ship `v1.1.0`,** the same way as the `## Release` section above. `develop` is now ahead of `main` again, carrying everything built since `v1.0.0`: US006, all eleven ADRs, the requirements traceability matrix, and the `README.md` update.
   - `Release Start` → `v1.1.0` (branch `release/v1.1.0`)
   - drop the `-preview` suffix in `Acme.OOProgramming.csproj` (`1.1.0-preview` → `1.1.0`), commit `chore(release): bump version to 1.1.0.`
   - add a `## [1.1.0] - <date>` section to `CHANGELOG.md`, directly under the intro block and above `## [1.0.0]`, and commit it too
   - `Release Publish`, then `Release Finish`

   <details>
   <summary>CHANGELOG.md (addition)</summary>

   ```markdown
   ## [1.1.0] - 2026-08-29

   ### Added
   - US006: Merge Duplicate Items in a Purchase Order: re-adding a product on the order merges quantities at a matching price, and is rejected at a conflicting one.
   - `PurchaseOrderItem.IncreaseQuantity()`, a controlled mutation method behind a `private` setter.
   - Architecture Decision Records (ADR-0001 through ADR-0011) in `docs/adrs.md`.
   - Requirements traceability matrix in `docs/user-stories.md`.

   ### Changed
   - `README.md` updated with `see ADR-NNNN` links throughout and a `docs/adrs.md` entry.
   ```
   </details>

   Publish the GitHub Release the same way as `v1.0.0`: **Releases** → **Draft a new release** → pick the tag `v1.1.0`, title `Version 1.1.0`, description below, **Publish release** (or from the command line: `gh release create v1.1.0 --title "Version 1.1.0" --notes-file <path to a file with the notes below>`).

   <details>
   <summary>Release notes (1.1.0)</summary>

   ```markdown
   ## 🚀 Added

   - **US006: Merge Duplicate Items in a Purchase Order**: adding a product already on the order merges into the existing line instead of creating a duplicate, as long as the unit price matches; re-adding it at a different price is rejected instead of silently picking one (see ADR-0008).
   - `PurchaseOrderItem.IncreaseQuantity()`: the entity changes its own quantity through an intention-revealing method behind a `private` setter, instead of `PurchaseOrder` rebuilding it from outside (see ADR-0007).

   ## 🔧 Changed

   - Architecture Decision Records consolidated into a single `docs/adrs.md`, covering value objects, bounded-context ownership, aggregate encapsulation, `Money`'s representation, the `readonly record struct` adoption, the `Currency` value object, the `PurchaseOrderItem` mutation method, the merge-or-reject decision, the `DateOnly` order date, the presentation layer, and where domain validation lives (ADR-0001 through ADR-0011).
   - `README.md` gained `see ADR-NNNN` links throughout and a `docs/adrs.md` entry.

   ## 📝 Documentation

   - Added a Requirements Traceability Matrix to `docs/user-stories.md`, mapping each user story to its bounded context, aggregate, and implementation.
   ```
   </details>

   **Note:** a release branch still needs at least one commit of its own (the changelog entry), or the merge into `develop` is a no-op, same reasoning as the `## Release` section.

6. **Back on `develop`, pick the `-preview` suffix back up.** In `Acme.OOProgramming.csproj`: `<Version>1.1.0</Version>` → `<Version>1.1.1-preview</Version>`, commit `chore(dev): set development version to 1.1.1-preview.`, push.

   **Note:** `## Testing` below is optional, self-study only, so `develop` shouldn't sit on an already-tagged version while there's still unreleased optional work.

## Testing (optional, explore on your own)

Everything up to here (Features 1-5, the presentation layer, US006, and the documentation work above) already shipped as real releases, `1.0.0` and `1.1.0`. This section doesn't gate any of that: it's optional. Testing well is a real skill, but it isn't this course's objective, and nothing later in this guide depends on finishing it.

The idea: add a test project with xUnit and FluentAssertions, paste in the starter test suite (one file per class already built, covering every user story's acceptance criteria, US001 through US006), run it, then use it as a jumping-off point:
- add a scenario it doesn't cover yet
- break a validation rule on purpose and confirm the test catches it
- look up `[Theory]` / `[InlineData]` in the xUnit docs and try something this suite doesn't use

1. **Add the test project.** Switch the Solution Explorer dropdown back to **Solution** view (this section adds a project and C# classes). Right-click the solution root → `Add` → `New Project...` → `xUnit Test Project` → name it `Acme.OOProgramming.Tests`, same solution directory. Then:
   - add a project reference from `Acme.OOProgramming.Tests` to `Acme.OOProgramming` (right-click `Acme.OOProgramming.Tests` → `Add` → `Reference...` → check `Acme.OOProgramming`)
   - add the `FluentAssertions` NuGet package (`Add` → `NuGet Reference...` → search `FluentAssertions` → install)
   - delete the template's `UnitTest1.cs`

   **Note:** `FluentAssertions` 8+ requires a paid license for commercial use above a revenue threshold; it stays free for individual developers, students, non-profits, and open source, which covers this course. Check the current terms before reusing this pattern in a paid company project.

2. **Make the internal constructor visible to the tests** without making it `public`. Add one file:

   <details>
   <summary>AssemblyInfo.cs (in Acme.OOProgramming, not Acme.OOProgramming.Tests)</summary>

   ```csharp
   using System.Runtime.CompilerServices;

   [assembly: InternalsVisibleTo("Acme.OOProgramming.Tests")]
   ```
   </details>

   **Note:** `PurchaseOrderItem`'s constructor is `internal`: only code inside the `Acme.OOProgramming` assembly can call it directly, and a test project is a separate assembly. Making it `public` instead would let any caller construct one, defeating the whole point of Feature 3's encapsulation decision.

   ```
   git add .
   git commit -m "chore(tests): allow test assembly to access internal members."
   ```

3. **Create each test file below under `Acme.OOProgramming.Tests`,** mirroring the production namespace it tests. Right-click the matching folder (create it the same way as any other folder if it doesn't exist yet) → `Add` → `Class` → paste the whole file over the generated skeleton.

   <details>
   <summary>AddressTests.cs (Shared/Domain/Model/ValueObjects)</summary>

   ```csharp
   using Acme.OOProgramming.Shared.Domain.Model.ValueObjects;
   using FluentAssertions;

   namespace Acme.OOProgramming.Tests.Shared.Domain.Model.ValueObjects;

   public class AddressTests
   {
       [Fact]
       public void ParameterlessConstructor_ThrowsInvalidOperationException()
       {
           var act = () => new Address();

           act.Should().Throw<InvalidOperationException>();
       }

       [Fact]
       public void Constructor_WithValidComponents_InitializesSuccessfully()
       {
           var address = new Address("Main St", "100", "Springfield", "IL", "62701", "USA");

           address.Street.Should().Be("Main St");
           address.Number.Should().Be("100");
           address.City.Should().Be("Springfield");
           address.StateOrRegion.Should().Be("IL");
           address.PostalCode.Should().Be("62701");
           address.Country.Should().Be("USA");
       }

       [Fact]
       public void Constructor_WithNullStateOrRegion_InitializesSuccessfully()
       {
           var address = new Address("Main St", "100", "Springfield", null, "62701", "USA");

           address.StateOrRegion.Should().BeNull();
           address.ToString().Should().Be("Main St, 100, Springfield, 62701, USA");
       }

       [Theory]
       [InlineData(null)]
       [InlineData("")]
       [InlineData("   ")]
       public void Constructor_WithNullOrWhitespaceStreet_ThrowsArgumentException(string? street)
       {
           var act = () => new Address(street!, "100", "City", null, "12345", "Country");

           act.Should().Throw<ArgumentException>();
       }

       [Fact]
       public void Constructor_WithStreetExceeding100Chars_ThrowsArgumentException()
       {
           var longStreet = new string('a', 101);

           var act = () => new Address(longStreet, "100", "City", null, "12345", "Country");

           act.Should().Throw<ArgumentException>();
       }

       [Theory]
       [InlineData(null)]
       [InlineData("")]
       [InlineData("   ")]
       public void Constructor_WithNullOrWhitespaceNumber_ThrowsArgumentException(string? number)
       {
           var act = () => new Address("Street", number!, "City", null, "12345", "Country");

           act.Should().Throw<ArgumentException>();
       }

       [Fact]
       public void Constructor_WithNumberExceeding10Chars_ThrowsArgumentException()
       {
           var longNumber = new string('1', 11);

           var act = () => new Address("Street", longNumber, "City", null, "12345", "Country");

           act.Should().Throw<ArgumentException>();
       }

       [Theory]
       [InlineData(null)]
       [InlineData("")]
       [InlineData("   ")]
       public void Constructor_WithNullOrWhitespaceCity_ThrowsArgumentException(string? city)
       {
           var act = () => new Address("Street", "100", city!, null, "12345", "Country");

           act.Should().Throw<ArgumentException>();
       }

       [Fact]
       public void Constructor_WithCityExceeding100Chars_ThrowsArgumentException()
       {
           var longCity = new string('a', 101);

           var act = () => new Address("Street", "100", longCity, null, "12345", "Country");

           act.Should().Throw<ArgumentException>();
       }

       [Theory]
       [InlineData(null)]
       [InlineData("")]
       [InlineData("   ")]
       public void Constructor_WithNullOrWhitespacePostalCode_ThrowsArgumentException(string? postalCode)
       {
           var act = () => new Address("Street", "100", "City", null, postalCode!, "Country");

           act.Should().Throw<ArgumentException>();
       }

       [Fact]
       public void Constructor_WithPostalCodeExceeding20Chars_ThrowsArgumentException()
       {
           var longPostalCode = new string('1', 21);

           var act = () => new Address("Street", "100", "City", null, longPostalCode, "Country");

           act.Should().Throw<ArgumentException>();
       }

       [Theory]
       [InlineData(null)]
       [InlineData("")]
       [InlineData("   ")]
       public void Constructor_WithNullOrWhitespaceCountry_ThrowsArgumentException(string? country)
       {
           var act = () => new Address("Street", "100", "City", null, "12345", country!);

           act.Should().Throw<ArgumentException>();
       }

       [Fact]
       public void Constructor_WithCountryExceeding100Chars_ThrowsArgumentException()
       {
           var longCountry = new string('a', 101);

           var act = () => new Address("Street", "100", "City", null, "12345", longCountry);

           act.Should().Throw<ArgumentException>();
       }

       [Fact]
       public void DefaultStruct_PropertiesReturnEmptyStrings()
       {
           Address defaultAddress = default;

           defaultAddress.Street.Should().BeEmpty();
           defaultAddress.Number.Should().BeEmpty();
           defaultAddress.City.Should().BeEmpty();
           defaultAddress.PostalCode.Should().BeEmpty();
           defaultAddress.Country.Should().BeEmpty();
       }

       [Fact]
       public void ToString_WithStateOrRegion_FormatsCorrectly()
       {
           var address = new Address("Main St", "100", "Springfield", "IL", "62701", "USA");

           address.ToString().Should().Be("Main St, 100, Springfield, IL, 62701, USA");
       }

       [Fact]
       public void Equality_SameValues_AreEqual()
       {
           var first = new Address("Main St", "100", "Springfield", "IL", "62701", "USA");
           var second = new Address("Main St", "100", "Springfield", "IL", "62701", "USA");
           var third = new Address("Other St", "100", "Springfield", "IL", "62701", "USA");

           first.Should().Be(second);
           (first == second).Should().BeTrue();
           first.Should().NotBe(third);
           (first != third).Should().BeTrue();
       }
   }
   ```
   </details>

   <details>
   <summary>MoneyTests.cs (Shared/Domain/Model/ValueObjects)</summary>

   ```csharp
   using Acme.OOProgramming.Shared.Domain.Model.ValueObjects;
   using FluentAssertions;

   namespace Acme.OOProgramming.Tests.Shared.Domain.Model.ValueObjects;

   public class MoneyTests
   {
       [Fact]
       public void ParameterlessConstructor_ThrowsInvalidOperationException()
       {
           var act = () => new Money();

           act.Should().Throw<InvalidOperationException>();
       }

       [Fact]
       public void Constructor_WithValidAmountAndCurrency_InitializesSuccessfully()
       {
           var currency = new Currency("USD");
           var money = new Money(100.50m, currency);

           money.Amount.Should().Be(100.50m);
           money.Currency.Should().Be(currency);
           money.ToString().Should().Be("100.50 USD");
       }

       [Fact]
       public void Constructor_WithStringCurrencyCode_InitializesSuccessfully()
       {
           var money = new Money(50.00m, "EUR");

           money.Amount.Should().Be(50.00m);
           money.Currency.Code.Should().Be("EUR");
       }

       [Theory]
       [InlineData(null)]
       [InlineData("")]
       [InlineData("US")]
       [InlineData("USDD")]
       public void Constructor_WithInvalidCurrencyCode_ThrowsArgumentException(string? invalidCurrency)
       {
           var act = () => new Money(10.00m, invalidCurrency!);

           act.Should().Throw<ArgumentException>();
       }

       [Fact]
       public void Constructor_WithNegativeAmount_ThrowsArgumentOutOfRangeException()
       {
           var currency = new Currency("USD");

           var act = () => new Money(-1.00m, currency);

           act.Should().Throw<ArgumentOutOfRangeException>();
       }

       [Fact]
       public void Constructor_WithDefaultCurrency_ThrowsArgumentException()
       {
           var act = () => new Money(10.00m, default(Currency));

           act.Should().Throw<ArgumentException>();
       }

       [Fact]
       public void Add_WithSameCurrency_ReturnsCombinedSum()
       {
           var first = new Money(10.50m, "USD");
           var second = new Money(20.25m, "USD");

           var sumMethod = first.Add(second);
           var sumOp = first + second;

           sumMethod.Amount.Should().Be(30.75m);
           sumMethod.Currency.Code.Should().Be("USD");
           sumMethod.Should().Be(sumOp);
       }

       [Fact]
       public void Add_WithDifferentCurrency_ThrowsInvalidOperationException()
       {
           var usdMoney = new Money(10.00m, "USD");
           var eurMoney = new Money(10.00m, "EUR");

           var act = () => usdMoney.Add(eurMoney);
           var opAct = () => usdMoney + eurMoney;

           act.Should().Throw<InvalidOperationException>();
           opAct.Should().Throw<InvalidOperationException>();
       }

       [Fact]
       public void Add_WithDefaultMoney_ThrowsInvalidOperationException()
       {
           var money = new Money(10.00m, "USD");
           Money defaultMoney = default;

           var act1 = () => money.Add(defaultMoney);
           var act2 = () => defaultMoney.Add(money);
           var act3 = () => money + defaultMoney;
           var act4 = () => defaultMoney + money;

           act1.Should().Throw<InvalidOperationException>();
           act2.Should().Throw<InvalidOperationException>();
           act3.Should().Throw<InvalidOperationException>();
           act4.Should().Throw<InvalidOperationException>();
       }

       [Fact]
       public void Multiply_WithValidFactor_ReturnsMultipliedAmount()
       {
           var money = new Money(29.99m, "USD");

           var resultMethod = money.Multiply(2);
           var resultOp1 = money * 2m;
           var resultOp2 = 2m * money;

           resultMethod.Amount.Should().Be(59.98m);
           resultMethod.Currency.Code.Should().Be("USD");
           resultMethod.Should().Be(resultOp1);
           resultMethod.Should().Be(resultOp2);
       }

       [Fact]
       public void Multiply_WithZeroFactor_ReturnsZeroAmount()
       {
           var money = new Money(29.99m, "USD");

           var result = money.Multiply(0);

           result.Amount.Should().Be(0m);
       }

       [Fact]
       public void Multiply_WithNegativeFactor_ThrowsArgumentOutOfRangeException()
       {
           var money = new Money(25.00m, "USD");

           var act = () => money.Multiply(-2m);
           var opAct = () => money * -2m;

           act.Should().Throw<ArgumentOutOfRangeException>();
           opAct.Should().Throw<ArgumentOutOfRangeException>();
       }

       [Fact]
       public void Multiply_WithDefaultMoney_ThrowsInvalidOperationException()
       {
           Money defaultMoney = default;

           var act1 = () => defaultMoney.Multiply(2m);
           var act2 = () => defaultMoney * 2m;
           var act3 = () => 2m * defaultMoney;

           act1.Should().Throw<InvalidOperationException>();
           act2.Should().Throw<InvalidOperationException>();
           act3.Should().Throw<InvalidOperationException>();
       }

       [Fact]
       public void Equality_SameAmountAndCurrency_AreEqual()
       {
           var first = new Money(15.99m, "USD");
           var second = new Money(15.99m, "USD");
           var third = new Money(20.00m, "USD");
           var fourth = new Money(15.99m, "EUR");

           first.Should().Be(second);
           (first == second).Should().BeTrue();
           first.Should().NotBe(third);
           first.Should().NotBe(fourth);
       }
   }
   ```
   </details>

   <details>
   <summary>CurrencyTests.cs (Shared/Domain/Model/ValueObjects)</summary>

   ```csharp
   using Acme.OOProgramming.Shared.Domain.Model.ValueObjects;
   using FluentAssertions;

   namespace Acme.OOProgramming.Tests.Shared.Domain.Model.ValueObjects;

   public class CurrencyTests
   {
       [Theory]
       [InlineData("USD")]
       [InlineData("EUR")]
       [InlineData("PEN")]
       [InlineData("GBP")]
       public void Constructor_WithValidCode_InitializesSuccessfully(string code)
       {
           var currency = new Currency(code);

           currency.Code.Should().Be(code);
           currency.ToString().Should().Be(code);
       }

       [Fact]
       public void Constructor_WithLowercaseCode_UppercasesCode()
       {
           var currency = new Currency("usd");

           currency.Code.Should().Be("USD");
       }

       [Theory]
       [InlineData(null)]
       [InlineData("")]
       [InlineData("   ")]
       public void Constructor_WithNullOrWhitespaceCode_ThrowsArgumentException(string? code)
       {
           var act = () => new Currency(code!);

           act.Should().Throw<ArgumentException>();
       }

       [Theory]
       [InlineData("US")]
       [InlineData("USDD")]
       [InlineData("U")]
       public void Constructor_WithInvalidLength_ThrowsArgumentException(string code)
       {
           var act = () => new Currency(code);

           act.Should().Throw<ArgumentException>();
       }

       [Theory]
       [InlineData("U1D")]
       [InlineData("US$")]
       [InlineData("123")]
       public void Constructor_WithNonAlphabeticCharacters_ThrowsArgumentException(string code)
       {
           var act = () => new Currency(code);

           act.Should().Throw<ArgumentException>();
       }

       [Fact]
       public void ParameterlessConstructor_ThrowsInvalidOperationException()
       {
           var act = () => new Currency();

           act.Should().Throw<InvalidOperationException>();
       }

       [Fact]
       public void DefaultStruct_CodeReturnsEmptyString()
       {
           Currency defaultCurrency = default;

           defaultCurrency.Code.Should().Be(string.Empty);
       }

       [Fact]
       public void Equality_SameCodes_AreEqual()
       {
           var first = new Currency("USD");
           var second = new Currency("USD");
           var third = new Currency("EUR");

           first.Should().Be(second);
           (first == second).Should().BeTrue();
           first.Should().NotBe(third);
           (first != third).Should().BeTrue();
       }
   }
   ```
   </details>

   <details>
   <summary>SupplierIdTests.cs (SupplyChain/Domain/Model/ValueObjects)</summary>

   ```csharp
   using Acme.OOProgramming.SupplyChain.Domain.Model.ValueObjects;
   using FluentAssertions;

   namespace Acme.OOProgramming.Tests.SupplyChain.Domain.Model.ValueObjects;

   public class SupplierIdTests
   {
       [Fact]
       public void ParameterlessConstructor_ThrowsInvalidOperationException()
       {
           var act = () => new SupplierId();

           act.Should().Throw<InvalidOperationException>();
       }

       [Theory]
       [InlineData("SUP001")]
       [InlineData("VEND-123")]
       [InlineData("ACME-CORP")]
       public void Constructor_WithValidIdentifier_InitializesSuccessfully(string identifier)
       {
           var supplierId = new SupplierId(identifier);

           supplierId.Identifier.Should().Be(identifier);
           supplierId.ToString().Should().Be(identifier);
       }

       [Theory]
       [InlineData(null)]
       [InlineData("")]
       [InlineData("   ")]
       public void Constructor_WithNullOrWhitespaceIdentifier_ThrowsArgumentException(string? identifier)
       {
           var act = () => new SupplierId(identifier!);

           act.Should().Throw<ArgumentException>();
       }

       [Fact]
       public void DefaultStruct_IdentifierReturnsEmptyString()
       {
           SupplierId defaultId = default;

           defaultId.Identifier.Should().BeEmpty();
       }

       [Fact]
       public void Equality_SameIdentifier_AreEqual()
       {
           var id1 = new SupplierId("SUP001");
           var id2 = new SupplierId("SUP001");
           var id3 = new SupplierId("SUP002");

           id1.Should().Be(id2);
           (id1 == id2).Should().BeTrue();
           id1.Should().NotBe(id3);
           (id1 != id3).Should().BeTrue();
       }
   }
   ```
   </details>

   <details>
   <summary>SupplierTests.cs (SupplyChain/Domain/Model/Aggregates)</summary>

   ```csharp
   using Acme.OOProgramming.SupplyChain.Domain.Model.Aggregates;
   using Acme.OOProgramming.SupplyChain.Domain.Model.ValueObjects;
   using Acme.OOProgramming.Shared.Domain.Model.ValueObjects;
   using FluentAssertions;

   namespace Acme.OOProgramming.Tests.SupplyChain.Domain.Model.Aggregates;

   public class SupplierTests
   {
       private readonly Address _address = new("Supplier St", "123", "SupplierCity", "SC", "12345", "United States");

       [Fact]
       public void Constructor_WithSupplierId_InitializesSuccessfully()
       {
           var id = new SupplierId("SUP001");

           var supplier = new Supplier(id, "Supplier Inc.", _address);

           supplier.Id.Should().Be(id);
           supplier.Name.Should().Be("Supplier Inc.");
           supplier.Address.Should().Be(_address);
       }

       [Fact]
       public void Constructor_WithStringIdentifier_InitializesSuccessfully()
       {
           var supplier = new Supplier("SUP001", "Supplier Inc.", _address);

           supplier.Id.Should().Be(new SupplierId("SUP001"));
           supplier.Name.Should().Be("Supplier Inc.");
           supplier.Address.Should().Be(_address);
       }

       [Theory]
       [InlineData(null)]
       [InlineData("")]
       [InlineData("   ")]
       public void Constructor_WithNullOrWhitespaceName_ThrowsArgumentException(string? invalidName)
       {
           var act = () => new Supplier(new SupplierId("SUP001"), invalidName!, _address);

           act.Should().Throw<ArgumentException>();
       }

       [Fact]
       public void Constructor_WithDefaultSupplierId_ThrowsArgumentException()
       {
           var act = () => new Supplier(default(SupplierId), "Supplier Inc.", _address);

           act.Should().Throw<ArgumentException>();
       }

       [Fact]
       public void Constructor_WithDefaultAddress_ThrowsArgumentException()
       {
           var act = () => new Supplier(new SupplierId("SUP001"), "Supplier Inc.", default);

           act.Should().Throw<ArgumentException>();
       }

       [Fact]
       public void Equals_WithDifferentSupplierIdSameFields_ReturnsFalse()
       {
           var first = new Supplier(new SupplierId("SUP001"), "Supplier Inc.", _address);
           var second = new Supplier(new SupplierId("SUP002"), "Supplier Inc.", _address);

           first.Should().NotBe(second);
       }

       [Fact]
       public void Equals_WithSameSupplierIdDifferentName_ReturnsTrue()
       {
           var id = new SupplierId("SUP001");
           var first = new Supplier(id, "Supplier Inc.", _address);
           var second = new Supplier(id, "A Different Name Inc.", _address);

           first.Should().Be(second);
       }
   }
   ```
   </details>

   <details>
   <summary>SupplierIdTests.cs (Procurement/Domain/Model/ValueObjects)</summary>

   ```csharp
   using Acme.OOProgramming.Procurement.Domain.Model.ValueObjects;
   using FluentAssertions;

   namespace Acme.OOProgramming.Tests.Procurement.Domain.Model.ValueObjects;

   public class SupplierIdTests
   {
       [Fact]
       public void ParameterlessConstructor_ThrowsInvalidOperationException()
       {
           var act = () => new SupplierId();

           act.Should().Throw<InvalidOperationException>();
       }

       [Theory]
       [InlineData("SUP001")]
       [InlineData("VEND-123")]
       [InlineData("ACME-CORP")]
       public void Constructor_WithValidIdentifier_InitializesSuccessfully(string identifier)
       {
           var supplierId = new SupplierId(identifier);

           supplierId.Identifier.Should().Be(identifier);
           supplierId.ToString().Should().Be(identifier);
       }

       [Theory]
       [InlineData(null)]
       [InlineData("")]
       [InlineData("   ")]
       public void Constructor_WithNullOrWhitespaceIdentifier_ThrowsArgumentException(string? identifier)
       {
           var act = () => new SupplierId(identifier!);

           act.Should().Throw<ArgumentException>();
       }

       [Fact]
       public void DefaultStruct_IdentifierReturnsEmptyString()
       {
           SupplierId defaultId = default;

           defaultId.Identifier.Should().BeEmpty();
       }

       [Fact]
       public void Equality_SameIdentifier_AreEqual()
       {
           var id1 = new SupplierId("SUP001");
           var id2 = new SupplierId("SUP001");
           var id3 = new SupplierId("SUP002");

           id1.Should().Be(id2);
           (id1 == id2).Should().BeTrue();
           id1.Should().NotBe(id3);
           (id1 != id3).Should().BeTrue();
       }
   }
   ```
   </details>

   <details>
   <summary>ProductIdTests.cs (Procurement/Domain/Model/ValueObjects)</summary>

   ```csharp
   using Acme.OOProgramming.Procurement.Domain.Model.ValueObjects;
   using FluentAssertions;

   namespace Acme.OOProgramming.Tests.Procurement.Domain.Model.ValueObjects;

   public class ProductIdTests
   {
       [Fact]
       public void ParameterlessConstructor_ThrowsInvalidOperationException()
       {
           var act = () => new ProductId();

           act.Should().Throw<InvalidOperationException>();
       }

       [Fact]
       public void Constructor_WithValidGuid_InitializesSuccessfully()
       {
           var guid = Guid.NewGuid();

           var productId = new ProductId(guid);

           productId.Id.Should().Be(guid);
           productId.ToString().Should().Be(guid.ToString());
       }

       [Fact]
       public void Constructor_WithEmptyGuid_ThrowsArgumentException()
       {
           var act = () => new ProductId(Guid.Empty);

           act.Should().Throw<ArgumentException>();
       }

       [Fact]
       public void New_GeneratesVersion7Guid()
       {
           var productId = ProductId.New();

           productId.Id.Should().NotBe(Guid.Empty);
           productId.Id.Version.Should().Be(7);
       }

       [Fact]
       public void Equality_SameGuid_AreEqual()
       {
           var guid = Guid.NewGuid();
           var p1 = new ProductId(guid);
           var p2 = new ProductId(guid);
           var p3 = ProductId.New();

           p1.Should().Be(p2);
           (p1 == p2).Should().BeTrue();
           p1.Should().NotBe(p3);
           (p1 != p3).Should().BeTrue();
       }
   }
   ```
   </details>

   <details>
   <summary>PurchaseOrderItemTests.cs (Procurement/Domain/Model/Aggregates)</summary>

   ```csharp
   using Acme.OOProgramming.Procurement.Domain.Model.Aggregates;
   using Acme.OOProgramming.Procurement.Domain.Model.ValueObjects;
   using Acme.OOProgramming.Shared.Domain.Model.ValueObjects;
   using FluentAssertions;

   namespace Acme.OOProgramming.Tests.Procurement.Domain.Model.Aggregates;

   public class PurchaseOrderItemTests
   {
       private readonly Money _unitPrice = new(25.99m, "USD");

       [Fact]
       public void Constructor_WithValidArguments_InitializesSuccessfully()
       {
           var productId = ProductId.New();

           var item = new PurchaseOrderItem(productId, 10, _unitPrice);

           item.ProductId.Should().Be(productId);
           item.Quantity.Should().Be(10);
           item.UnitPrice.Should().Be(_unitPrice);
       }

       [Fact]
       public void Constructor_WithDefaultProductId_ThrowsArgumentException()
       {
           var act = () => new PurchaseOrderItem(default, 10, _unitPrice);

           act.Should().Throw<ArgumentException>();
       }

       [Fact]
       public void Constructor_WithDefaultUnitPrice_ThrowsArgumentException()
       {
           var act = () => new PurchaseOrderItem(ProductId.New(), 10, default);

           act.Should().Throw<ArgumentException>();
       }

       [Theory]
       [InlineData(0)]
       [InlineData(-1)]
       [InlineData(-10)]
       public void Constructor_WithZeroOrNegativeQuantity_ThrowsArgumentOutOfRangeException(int quantity)
       {
           var act = () => new PurchaseOrderItem(ProductId.New(), quantity, _unitPrice);

           act.Should().Throw<ArgumentOutOfRangeException>();
       }

       [Fact]
       public void IncreaseQuantity_WithPositiveAmount_IncreasesQuantity()
       {
           var item = new PurchaseOrderItem(ProductId.New(), 2, _unitPrice);

           item.IncreaseQuantity(5);

           item.Quantity.Should().Be(7);
       }

       [Theory]
       [InlineData(0)]
       [InlineData(-1)]
       public void IncreaseQuantity_WithZeroOrNegativeAmount_ThrowsArgumentOutOfRangeException(int addition)
       {
           var item = new PurchaseOrderItem(ProductId.New(), 2, _unitPrice);

           var act = () => item.IncreaseQuantity(addition);

           act.Should().Throw<ArgumentOutOfRangeException>();
       }

       [Fact]
       public void CalculateItemTotal_ReturnsQuantityMultipliedByUnitPrice()
       {
           var item = new PurchaseOrderItem(ProductId.New(), 10, _unitPrice);

           var total = item.CalculateItemTotal();

           total.Amount.Should().Be(259.90m);
           total.Currency.Code.Should().Be("USD");
       }

       [Fact]
       public void Equals_WithSameProductQuantityAndUnitPrice_ReturnsTrue()
       {
           var productId = ProductId.New();

           var first = new PurchaseOrderItem(productId, 10, _unitPrice);
           var second = new PurchaseOrderItem(productId, 10, _unitPrice);

           first.Should().Be(second);
           first.GetHashCode().Should().Be(second.GetHashCode());
       }

       [Fact]
       public void Equals_WithDifferentQuantity_ReturnsFalse()
       {
           var productId = ProductId.New();

           var first = new PurchaseOrderItem(productId, 10, _unitPrice);
           var second = new PurchaseOrderItem(productId, 5, _unitPrice);

           first.Should().NotBe(second);
       }
   }
   ```
   </details>

   <details>
   <summary>PurchaseOrderTests.cs (Procurement/Domain/Model/Aggregates)</summary>

   ```csharp
   using Acme.OOProgramming.Procurement.Domain.Model.Aggregates;
   using Acme.OOProgramming.Procurement.Domain.Model.ValueObjects;
   using Acme.OOProgramming.Shared.Domain.Model.ValueObjects;
   using FluentAssertions;

   namespace Acme.OOProgramming.Tests.Procurement.Domain.Model.Aggregates;

   public class PurchaseOrderTests
   {
       private readonly SupplierId _supplierId = new("SUP001");
       private readonly Currency _usd = new("USD");
       private readonly DateOnly _orderDate = new(2025, 3, 29);

       [Fact]
       public void Constructor_WithValidArguments_InitializesSuccessfully()
       {
           var order = new PurchaseOrder("PO001", _supplierId, _orderDate, _usd);

           order.OrderNumber.Should().Be("PO001");
           order.SupplierId.Should().Be(_supplierId);
           order.OrderDate.Should().Be(_orderDate);
           order.Currency.Should().Be(_usd);
           order.Items.Should().BeEmpty();
       }

       [Fact]
       public void Constructor_WithStringCurrency_InitializesSuccessfully()
       {
           var order = new PurchaseOrder("PO002", _supplierId, _orderDate, "EUR");

           order.OrderNumber.Should().Be("PO002");
           order.Currency.Code.Should().Be("EUR");
       }

       [Fact]
       public void Constructor_WithDateTime_InitializesSuccessfully()
       {
           var dateTime = new DateTime(2025, 3, 29, 14, 30, 0);

           var order = new PurchaseOrder("PO003", _supplierId, dateTime, "USD");

           order.OrderDate.Should().Be(new DateOnly(2025, 3, 29));
       }

       [Theory]
       [InlineData(null)]
       [InlineData("")]
       [InlineData("   ")]
       public void Constructor_WithNullOrWhitespaceOrderNumber_ThrowsArgumentException(string? orderNumber)
       {
           var act = () => new PurchaseOrder(orderNumber!, _supplierId, _orderDate, _usd);

           act.Should().Throw<ArgumentException>();
       }

       [Fact]
       public void Constructor_WithDefaultSupplierId_ThrowsArgumentException()
       {
           var act = () => new PurchaseOrder("PO001", default, _orderDate, _usd);

           act.Should().Throw<ArgumentException>();
       }

       [Theory]
       [InlineData(null)]
       [InlineData("")]
       [InlineData("US")]
       public void Constructor_WithInvalidCurrency_ThrowsArgumentException(string? invalidCurrency)
       {
           var act = () => new PurchaseOrder("PO001", _supplierId, _orderDate, invalidCurrency!);

           act.Should().Throw<ArgumentException>();
       }

       [Fact]
       public void Constructor_WithDefaultCurrency_ThrowsArgumentException()
       {
           var act = () => new PurchaseOrder("PO001", _supplierId, _orderDate, default(Currency));

           act.Should().Throw<ArgumentException>();
       }

       [Fact]
       public void Items_ReturnsCachedReadOnlyView()
       {
           var order = new PurchaseOrder("PO001", _supplierId, _orderDate, _usd);

           var view1 = order.Items;
           var view2 = order.Items;

           view1.Should().BeSameAs(view2);
       }

       [Fact]
       public void AddItem_WithValidArguments_AddsItemToOrder()
       {
           var order = new PurchaseOrder("PO001", _supplierId, _orderDate, _usd);
           var productId = ProductId.New();

           order.AddItem(productId, 10, 15.99m);

           order.Items.Should().HaveCount(1);
           order.Items[0].ProductId.Should().Be(productId);
           order.Items[0].Quantity.Should().Be(10);
           order.Items[0].UnitPrice.Amount.Should().Be(15.99m);
           order.Items[0].UnitPrice.Currency.Code.Should().Be("USD");
       }

       [Fact]
       public void AddItem_ReturnsReadOnlyItemsThatCannotBeModifiedDirectly()
       {
           var order = new PurchaseOrder("PO001", _supplierId, _orderDate, _usd);
           order.AddItem(ProductId.New(), 1, 10.00m);

           order.Items.Should().BeAssignableTo<IReadOnlyList<PurchaseOrderItem>>();
           var mutableView = (IList<PurchaseOrderItem>)order.Items;
           var act = () => mutableView.Add(new PurchaseOrderItem(ProductId.New(), 1, new Money(1.00m, "USD")));

           act.Should().Throw<NotSupportedException>();
       }

       [Fact]
       public void AddItem_WithDefaultProductId_ThrowsArgumentException()
       {
           var order = new PurchaseOrder("PO001", _supplierId, _orderDate, _usd);

           var act = () => order.AddItem(default, 5, 19.99m);

           act.Should().Throw<ArgumentException>();
       }

       [Theory]
       [InlineData(0)]
       [InlineData(-1)]
       public void AddItem_WithZeroOrNegativeQuantity_ThrowsArgumentOutOfRangeException(int quantity)
       {
           var order = new PurchaseOrder("PO001", _supplierId, _orderDate, _usd);

           var act = () => order.AddItem(ProductId.New(), quantity, 15.99m);

           act.Should().Throw<ArgumentOutOfRangeException>();
       }

       [Fact]
       public void AddItem_WithNegativeUnitPrice_ThrowsArgumentOutOfRangeException()
       {
           var order = new PurchaseOrder("PO001", _supplierId, _orderDate, _usd);

           var act = () => order.AddItem(ProductId.New(), 1, -1.00m);

           act.Should().Throw<ArgumentOutOfRangeException>();
       }

       [Fact]
       public void AddItem_WithDuplicateProductAndMatchingPrice_MergesQuantity()
       {
           var order = new PurchaseOrder("PO001", _supplierId, _orderDate, _usd);
           var productId = ProductId.New();
           order.AddItem(productId, 10, 15.99m);

           order.AddItem(productId, 5, 15.99m);

           order.Items.Should().HaveCount(1);
           order.Items[0].Quantity.Should().Be(15);
       }

       [Fact]
       public void AddItem_WithDuplicateProductAndConflictingPrice_ThrowsInvalidOperationException()
       {
           var order = new PurchaseOrder("PO001", _supplierId, _orderDate, _usd);
           var productId = ProductId.New();
           order.AddItem(productId, 10, 15.99m);

           var act = () => order.AddItem(productId, 5, 19.99m);

           act.Should().Throw<InvalidOperationException>();
           order.Items[0].UnitPrice.Amount.Should().Be(15.99m);
           order.Items[0].Quantity.Should().Be(10);
       }

       [Fact]
       public void AddItem_WithDifferentProduct_CreatesSeparateLine()
       {
           var order = new PurchaseOrder("PO001", _supplierId, _orderDate, _usd);
           order.AddItem(ProductId.New(), 10, 15.99m);

           order.AddItem(ProductId.New(), 5, 19.99m);

           order.Items.Should().HaveCount(2);
       }

       [Fact]
       public void CalculateTotal_WithEmptyOrder_ReturnsZeroMoneyInOrderCurrency()
       {
           var order = new PurchaseOrder("PO001", _supplierId, _orderDate, _usd);

           var total = order.CalculateTotal();

           total.Amount.Should().Be(0m);
           total.Currency.Should().Be(_usd);
       }

       [Fact]
       public void CalculateTotal_WithMultipleItems_CalculatesAccurateTotal()
       {
           var order = new PurchaseOrder("PO001", _supplierId, _orderDate, _usd);
           order.AddItem(ProductId.New(), 10, 25.99m);
           order.AddItem(ProductId.New(), 1, 10.00m);

           var total = order.CalculateTotal();

           total.Amount.Should().Be(269.90m);
           total.Currency.Should().Be(_usd);
       }

       [Fact]
       public void Equals_WithDifferentOrderNumberSameSupplier_ReturnsFalse()
       {
           var first = new PurchaseOrder("PO001", _supplierId, _orderDate, _usd);
           var second = new PurchaseOrder("PO002", _supplierId, _orderDate, _usd);

           first.Should().NotBe(second);
       }

       [Fact]
       public void Equals_WithSameOrderNumber_ReturnsTrue()
       {
           var first = new PurchaseOrder("PO001", _supplierId, _orderDate, _usd);
           var second = new PurchaseOrder("PO001", new SupplierId("SUP002"), _orderDate, "EUR");

           first.Should().Be(second);
       }
   }
   ```
   </details>

   <details>
   <summary>ConsoleFormattingTests.cs (Shared/Presentation)</summary>

   ```csharp
   using Acme.OOProgramming.Shared.Domain.Model.ValueObjects;
   using Acme.OOProgramming.Shared.Presentation;
   using FluentAssertions;

   namespace Acme.OOProgramming.Tests.Shared.Presentation;

   public class ConsoleFormattingTests
   {
       [Fact]
       public void Display_ReturnsFormattedAmountAndCurrency()
       {
           var money = new Money(1234.50m, "USD");

           money.Display.Should().Be("1,234.50 USD");
       }
   }
   ```
   </details>

   <details>
   <summary>ConsoleFormattingTests.cs (Procurement/Presentation)</summary>

   ```csharp
   using Acme.OOProgramming.Procurement.Domain.Model.Aggregates;
   using Acme.OOProgramming.Procurement.Domain.Model.ValueObjects;
   using Acme.OOProgramming.Procurement.Presentation;
   using Acme.OOProgramming.Shared.Domain.Model.ValueObjects;
   using FluentAssertions;

   namespace Acme.OOProgramming.Tests.Procurement.Presentation;

   public class ConsoleFormattingTests
   {
       [Fact]
       public void Summary_ReturnsFormattedPurchaseOrderSummary()
       {
           var supplierId = new SupplierId("SUP001");
           var currency = new Currency("USD");
           var orderDate = new DateOnly(2026, 8, 19);
           var order = new PurchaseOrder("PO001", supplierId, orderDate, currency);

           order.Summary.Should().Be($"Purchase Order PO001 created for Supplier ID SUP001 in USD on {orderDate}");
       }
   }
   ```
   </details>

4. **Run it.**
   ```
   dotnet test
   ```

5. **Commit it, straight on `develop`.**
   ```
   git add .
   git commit -m "test(domain): add unit test suite for us001 through us005 and the presentation layer."
   git push
   ```

   **Note:** no user story behind this, so no Git Flow feature branch, same as the class diagram in Project Setup.

6. **Add the `Test Suite` column to the Requirements Traceability Matrix.** Go back to the matrix in `## Release` above and add it now that real tests exist: one cell per row, linking to the test class (or specific method) that verifies that user story.

   <details>
   <summary>docs/user-stories.md (addition, add a Test Suite column to the matrix)</summary>

   ```markdown
   | Story ID | User Story Title | Bounded Context | Aggregate / Entity | Primary Implementation | Test Suite |
   |:---|:---|:---|:---|:---|:---|
   | **US001** | Register a Supplier | SupplyChain Context | [`Supplier`](../Acme.OOProgramming/SupplyChain/Domain/Model/Aggregates/Supplier.cs) | `Supplier(id, name, address)` | [`SupplierTests.Constructor_WithSupplierId_InitializesSuccessfully`](../Acme.OOProgramming.Tests/SupplyChain/Domain/Model/Aggregates/SupplierTests.cs) |
   | **US002** | Create a Purchase Order | Procurement Context | [`PurchaseOrder`](../Acme.OOProgramming/Procurement/Domain/Model/Aggregates/PurchaseOrder.cs) | `PurchaseOrder(orderNumber, supplierId, orderDate, currency)` | [`PurchaseOrderTests.Constructor_WithValidArguments_InitializesSuccessfully`](../Acme.OOProgramming.Tests/Procurement/Domain/Model/Aggregates/PurchaseOrderTests.cs) |
   | **US003** | Add Items to a Purchase Order | Procurement Context | [`PurchaseOrder`](../Acme.OOProgramming/Procurement/Domain/Model/Aggregates/PurchaseOrder.cs), [`PurchaseOrderItem`](../Acme.OOProgramming/Procurement/Domain/Model/Aggregates/PurchaseOrderItem.cs) | `AddItem(productId, quantity, unitPriceAmount)` | [`PurchaseOrderTests.AddItem_WithValidArguments_AddsItemToOrder`](../Acme.OOProgramming.Tests/Procurement/Domain/Model/Aggregates/PurchaseOrderTests.cs) |
   | **US004** | Calculate Purchase Order Item Subtotal | Procurement Context | [`PurchaseOrderItem`](../Acme.OOProgramming/Procurement/Domain/Model/Aggregates/PurchaseOrderItem.cs) | `CalculateItemTotal()` | [`PurchaseOrderItemTests.CalculateItemTotal_ReturnsQuantityMultipliedByUnitPrice`](../Acme.OOProgramming.Tests/Procurement/Domain/Model/Aggregates/PurchaseOrderItemTests.cs) |
   | **US005** | Calculate Purchase Order Total | Procurement Context | [`PurchaseOrder`](../Acme.OOProgramming/Procurement/Domain/Model/Aggregates/PurchaseOrder.cs) | `CalculateTotal()` | [`PurchaseOrderTests.CalculateTotal_WithMultipleItems_CalculatesAccurateTotal`](../Acme.OOProgramming.Tests/Procurement/Domain/Model/Aggregates/PurchaseOrderTests.cs) |
   | **US006** | Merge Duplicate Items in a Purchase Order | Procurement Context | [`PurchaseOrder`](../Acme.OOProgramming/Procurement/Domain/Model/Aggregates/PurchaseOrder.cs), [`PurchaseOrderItem`](../Acme.OOProgramming/Procurement/Domain/Model/Aggregates/PurchaseOrderItem.cs) | `AddItem(productId, quantity, unitPriceAmount)` (merge-or-reject branch) | [`PurchaseOrderTests`](../Acme.OOProgramming.Tests/Procurement/Domain/Model/Aggregates/PurchaseOrderTests.cs): `AddItem_WithDuplicateProductAndMatchingPrice_MergesQuantity`, `AddItem_WithDuplicateProductAndConflictingPrice_ThrowsInvalidOperationException`, `AddItem_WithDifferentProduct_CreatesSeparateLine` |
   ```
   </details>

   ```
   git add .
   git commit -m "docs(user-stories): add test suite column to the traceability matrix."
   git push
   ```

7. **Update `README.md`** now that the test suite exists. Replace the file from `## Release` with the version below: this adds the `Tests` badge, the `Testing Framework` bullet, the `Acme.OOProgramming.Tests` entry in `## Solution Structure`, and a `## Run the Automated Test Suite` step under `## Getting Started`.

   <details>
   <summary>README.md</summary>

   ````markdown
   # OOP Sample (`oop-sample`)

   [![.NET](https://img.shields.io/badge/.NET-10-purple.svg)](https://dotnet.microsoft.com/)
   [![C#](https://img.shields.io/badge/C%23-14-blue.svg)](https://learn.microsoft.com/dotnet/csharp/)
   [![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](LICENSE.md)
   [![Tests](https://img.shields.io/badge/Tests-passing-brightgreen.svg)](Acme.OOProgramming.Tests)

   `oop-sample` is a sample C# console application demonstrating **Object-Oriented Programming (OOP)** and **Domain-Driven Design (DDD)** principles across two bounded contexts, SupplyChain and Procurement, and a shared kernel.

   **Author**: Web Applications Developer Team  
   **License**: See [LICENSE.md](LICENSE.md) for details.

   ---

   ## Technical Stack & Modern Features

   - **Runtime & Framework**: .NET 10.0 (C# 14.0)
   - **Testing Framework**: xUnit with `Microsoft.NET.Test.Sdk`
   - **C# 14 & .NET 10 Features**:
     - **Extension Members (`extension(T)`)**: presentation formatting (`order.Summary`, `money.Display`) decoupled from the domain models.
     - **`field` Keyword**: property validation and null-safe fallback without an explicit private backing field.
     - **Struct Parameterless Constructor Safety**: every `readonly record struct` value object throws `InvalidOperationException` from `new X()`, and falls back safely on `default`.
     - **UUIDv7 Identifiers**: time-ordered identifiers via `Guid.CreateVersion7()` (`ProductId`).
     - **`DateOnly` Temporal Modeling**: a purchase order's date has no time-of-day or time zone.
     - **Modern Throw Helpers**: `ArgumentException.ThrowIfNullOrWhiteSpace` and friends, not hand-written null checks.

   ---

   ## Solution Structure

   ```text
   oop-sample/
   ├── Acme.OOProgramming/                     # Main domain & console application project
   │   ├── Procurement/                        # Procurement Bounded Context
   │   │   ├── Domain/Model/
   │   │   │   ├── Aggregates/                 # PurchaseOrder (AR), PurchaseOrderItem (Entity)
   │   │   │   └── ValueObjects/               # ProductId (UUIDv7), SupplierId
   │   │   └── Presentation/                   # ConsoleFormatting (C# 14 extension members)
   │   ├── SupplyChain/                        # Supply Chain Bounded Context
   │   │   └── Domain/Model/
   │   │       ├── Aggregates/                 # Supplier (AR)
   │   │       └── ValueObjects/               # SupplierId
   │   ├── Shared/                             # Shared Kernel
   │   │   ├── Domain/Model/ValueObjects/      # Currency (ISO 4217), Money, Address
   │   │   └── Presentation/                   # ConsoleFormatting (C# 14 extension members)
   │   └── Program.cs                          # Application entry point & demo scenarios
   ├── Acme.OOProgramming.Tests/                # Automated xUnit test suite (131 tests)
   │   ├── Procurement/                        # PurchaseOrder, PurchaseOrderItem, ProductId tests
   │   ├── SupplyChain/                        # Supplier, SupplierId tests
   │   └── Shared/                             # Currency, Money, Address, ConsoleFormatting tests
   ├── docs/                                   # Architecture & requirements documentation
   │   ├── adrs.md                             # Architecture Decision Records (ADR-0001 through ADR-0011)
   │   ├── class-diagram.puml                  # PlantUML domain model class diagram
   │   └── user-stories.md                     # User stories (US001-US006) & Requirements Traceability Matrix
   ├── CHANGELOG.md                            # Project release notes & version history
   ├── LICENSE.md                              # Project license
   └── README.md                               # Project overview & guide
   ```

   ---

   ## Bounded Contexts & Domain Model

   ### 1. `Acme.OOProgramming.SupplyChain` (Supply Chain Management)
   - **`Supplier`** (*Aggregate Root*): a vendor with identity and location.
   - **`SupplierId`** (*Value Object*): strongly-typed identifier, owned by SupplyChain.

   ### 2. `Acme.OOProgramming.Procurement` (Procurement)
   - **`PurchaseOrder`** (*Aggregate Root*): purchase order invariants, currency consistency, and item lifecycle; `OrderDate` is a `DateOnly`, a calendar date with no time-of-day or time zone component (see [ADR-0009](docs/adrs.md#adr-0009-dateonly-for-purchase-order-dates)).
   - **`PurchaseOrderItem`** (*Entity*): managed exclusively by `PurchaseOrder`, its constructor is `internal`.
   - **`ProductId`** (*Value Object*): time-ordered identifier generated with UUIDv7 (`Guid.CreateVersion7()`).
   - **`SupplierId`** (*Value Object*): Procurement's own copy of the concept, deliberately decoupled from SupplyChain's (see [ADR-0002](docs/adrs.md#adr-0002-each-bounded-context-owns-its-own-reference-types)).
   - **`Presentation.ConsoleFormatting`** (`order.Summary`): console-only formatting kept out of the aggregate itself, via a C# 14 extension member (see [ADR-0010](docs/adrs.md#adr-0010-presentation-formatting-via-c-14-extension-members)).

   ### 3. `Acme.OOProgramming.Shared` (Shared Kernel)
   - **`Money`** (*Value Object*): `decimal` amount + validated `Currency`, `readonly record struct` for value semantics and zero heap allocation.
   - **`Currency`** (*Value Object*): validated 3-letter ISO code, `readonly record struct` (see [ADR-0006](docs/adrs.md#adr-0006-currency-as-a-dedicated-value-object)).
   - **`Address`** (*Value Object*): international postal address, `readonly record struct`.
   - **`Presentation.ConsoleFormatting`** (`money.Display`): console-only formatting kept out of `Money` itself, via a C# 14 extension member (see [ADR-0010](docs/adrs.md#adr-0010-presentation-formatting-via-c-14-extension-members)).

   ---

   ## Key Domain Rules & Design Invariants

   - **Aggregate invariant encapsulation**: `PurchaseOrder` strictly controls the creation and lifecycle of `PurchaseOrderItem`.
   - **Single-currency rule**: every item in a `PurchaseOrder` is priced in the order's own currency.
   - **Currency-safe arithmetic**: `Money` rejects cross-currency operations and negative amounts; its `+`/`*` operators call the same validated methods underneath.
   - **Duplicate line item handling**: `PurchaseOrder.AddItem` merges quantities when an existing `ProductId` is re-added at the same unit price; re-adding it at a different price throws instead of silently picking one (see [ADR-0008](docs/adrs.md#adr-0008-additem-merges-a-duplicate-product-rejecting-a-conflicting-unit-price)).
   - **Uniform value-type adoption**: `Money`, `Currency`, `Address`, `SupplierId`, and `ProductId` are all `readonly record struct`s, each `default`-guarded at every aggregate boundary that consumes one (see [ADR-0005](docs/adrs.md#adr-0005-value-objects-as-readonly-record-struct) and [ADR-0006](docs/adrs.md#adr-0006-currency-as-a-dedicated-value-object)).
   - **Cross-context references**: each bounded context owns its own copy of any identifier it references from another context, rather than sharing one type.
   - **Presentation decoupling**: display formatting (`order.Summary`, `money.Display`) lives in dedicated `*.Presentation` namespaces, never on the domain models themselves (see [ADR-0010](docs/adrs.md#adr-0010-presentation-formatting-via-c-14-extension-members)).

   ---

   ## Project Documentation

   | Document | Description |
   | :--- | :--- |
   | [**Architecture Decision Records (ADRs)**](docs/adrs.md) | Eleven architectural decisions (ADR-0001 through ADR-0011). |
   | [**User Stories & RTM**](docs/user-stories.md) | User stories (US001-US006) and Requirements Traceability Matrix, with a test-suite column. |
   | [**Class Diagram**](docs/class-diagram.puml) | PlantUML class diagram of bounded contexts, aggregates, entities, and value objects. |
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
   dotnet run --project Acme.OOProgramming
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

8. **Ship `v1.1.1`,** one more time through the release cycle. `develop` is ahead of `main` again, and there's no more work planned after this.
   - `Release Start` → `v1.1.1` (branch `release/v1.1.1`)
   - drop the `-preview` suffix in `Acme.OOProgramming.csproj` (`1.1.1-preview` → `1.1.1`), commit `chore(release): bump version to 1.1.1.`
   - add a `## [1.1.1] - <date>` section to `CHANGELOG.md`, directly under the intro block and above `## [1.1.0]`, and commit it too
   - `Release Publish`, then `Release Finish`

   <details>
   <summary>CHANGELOG.md (addition)</summary>

   ```markdown
   ## [1.1.1] - 2026-08-30

   ### Added
   - Automated unit test suite (xUnit) covering domain aggregates, value objects, and presentation extensions across the `Shared`, `SupplyChain`, and `Procurement` bounded contexts.
   - Test suite column in the requirements traceability matrix (`docs/user-stories.md`).
   ```
   </details>

   Publish the GitHub Release the same way as every release so far: **Releases** → **Draft a new release** → pick the tag `v1.1.1`, title `Version 1.1.1`, description below, **Publish release**.

   <details>
   <summary>Release notes (1.1.1)</summary>

   ```markdown
   ## 🚀 Added

   - Unit test suite (xUnit + FluentAssertions) covering US001 through US006 and the `Presentation` layer, one test class per domain class.

   ## ✅ Verified

   - All tests pass against the full domain model (`dotnet test`).
   ```
   </details>

   **Note:** `1.1.1` is another patch bump, still no new capability, just tests and documentation.

9. **From here, it's on you.** Add a test for a scenario not covered yet, break a validation rule on purpose and confirm the test catches it, or look up something in the xUnit or FluentAssertions docs this suite doesn't use yet.

## Appendix

Reference notes for situations that come up now and then. Skip past this on a normal run and come back when you hit one of them.

### Continuing on another computer

Once you've pushed your work it's on GitHub, so you can carry on from any machine.

1. **Sign in, then clone.**
   - **Sign in** once, so both git and the IDE can reach the private repo:
     ```
     gh auth login
     ```
     This also registers `gh` as git's credential helper for `github.com`, so neither the terminal nor Rider asks again.
   - **Clone in the terminal:** `cd` into the folder where you keep your projects, then (`<org>` is your organization's name, no angle brackets, same as when you first pushed):
     ```
     gh repo clone <org>/oop-sample
     ```
     Open the `oop-sample` folder in Rider afterward (`File` → `Open`); it picks up the solution from the `.sln` on its own.
   - **Or clone from the IDE:** on the JetBrains Welcome screen (close any open solution first), click `Clone Repository`, paste `https://github.com/<org>/oop-sample.git`, pick a target folder, and click `Clone`. The solution opens when the clone finishes.

2. **Reinstate Git Flow.**
   - Install `plantuml4idea` (Project Setup step 4) and Git Flow Helper (Project Setup step 8) if this machine doesn't already have them. Plugins live in the IDE, not the repo.
   - Check out `develop` before anything else. A fresh clone only has `main` as a local branch; this turns `develop` into a real local branch tracking `origin/develop`. Do it before `Init`, so Git Flow Helper registers against the existing `develop` instead of creating a new one off `main`. Either way:
     - **Terminal:**
       ```
       git checkout develop
       ```
     - **From the branch widget:** click the widget in the status bar (bottom-right), find `origin/develop` under **Remote Branches**, and pick `Checkout`.
   - Register your GitHub account in the IDE, same as Project Setup step 9: get your token with
     ```
     gh auth token
     ```
     then in `Settings` → `Version Control` → `GitHub`, remove any account already listed (`−`), then `+` → `Log In with Token...` → paste the token → `Add Account`.
   - Run Git Flow `Init` from the widget (Project Setup step 9). The Git Flow settings live in the repo's local git config, which a clone doesn't copy; `Init` re-registers the branch names on this machine. Accept the defaults. If a login popup appears during the push, click `Log In with Token` and paste the same token.

3. **Get onto your feature branch**, only if you stopped partway through a feature. Checking out `develop` above didn't bring the feature branch; get it now:
   - **Terminal:**
     ```
     git checkout feature/<name>
     git pull
     ```
   - **From the branch widget:** click the widget in the status bar (bottom-right), find `origin/feature/<name>` under **Remote Branches**, and pick `Checkout` from the actions that appear.

   Either way, checking out a branch with no local copy creates it from `origin/feature/<name>` and starts tracking it.

   **Note:** seeing only `main` locally right after a clone is normal, not a sync problem. Every remote branch was still downloaded; the `develop` and feature checkouts are what turn them into local branches.

### Signing in to GitHub with a token

The guide uses `gh auth login` (step 7), which is the simplest way. If you can't install `gh`, GitHub also accepts a Personal Access Token.

**On a shared machine, clear any cached credential first** so a plain `git push` doesn't run as whoever signed in last:
```
git credential reject
```
Then type these two lines, Enter after each, and Enter once more on the empty line to finish:
```
protocol=https
host=github.com
```

1. When `git push` asks for credentials, generate one: GitHub → `Settings` → `Developer settings` → `Personal access tokens` → `Generate new token (classic)`.
   - Note (label): `UPC`
   - Expiration: leave the default (`30 days`)
   - Scopes: check only the top-level `repo` checkbox (covers everything underneath); leave the rest unchecked
   - Click **Generate token**, then copy it somewhere safe (a password manager) before navigating away. GitHub shows it **only once**.
2. Back in the terminal, where the push is waiting:
   - macOS: type your GitHub username, then paste the token as the password (no characters show as you paste, that's normal)
   - Windows: in the "Connect to GitHub" window, pick the `Token` tab and paste it there

   It's cached after this, so it won't ask again for the rest of the project.

Use `(classic)`, not "Fine-grained tokens": fine-grained tokens need the organization's owner to approve them first, which can leave you waiting. With `repo` scope the token reaches every repo your account can, so you can reuse it across the other projects too.

### Backing up unfinished work

Commits stay on your machine until you push them. If you have to stop before a feature is finished, commit your progress and run `Feature Publish` (or `git push`, if the branch is already published). Later, check out the branch on any machine and `git pull` to continue. A feature doesn't have to be one commit.

### Feature Finish and pull requests

`Feature Finish` with `Integrate Immediately` merges your branch straight into `develop`. A real team does this through a pull request instead: open one from your feature branch into `develop` (`Feature Publish` already put the branch on GitHub), wait for the build and a reviewer, then merge it. The git steps are the same; `Integrate Immediately` just skips the review, which you can't do on your own anyway.

### Removing a stray .git folder

If `git init` ran from a subfolder instead of the solution root, a `.git` folder is now sitting in that subfolder. Delete it, then re-run `git init` from the solution root.

`.git` is a hidden folder, so reveal it first:

- **macOS (Finder):** press `Cmd+Shift+.`
- **Windows (File Explorer):** turn on `View` → `Show` → `Hidden items`

Then delete the `.git` folder like any other folder.

Or, from a terminal opened in that subfolder, run the line for your shell:

```
rm -rf .git                        # macOS / Linux / Git Bash
Remove-Item -Recurse -Force .git   # Windows PowerShell
rmdir /s /q .git                   # Windows Command Prompt
```

### Creating the repo without the GitHub CLI

No `gh`? Do the whole thing through the GitHub website plus plain `git`.

1. Authenticate git first, since `gh auth login` isn't available: follow [Signing in to GitHub with a token](#signing-in-to-github-with-a-token).
2. On GitHub, create an empty **private** repo named `oop-sample` in your organization, with no README, license, or `.gitignore` (this repo already has all three).
3. On the repo's "Quick setup" page, copy the **HTTPS** clone URL, the one that ends in `.git` (e.g. `https://github.com/<org>/oop-sample.git`, where `<org>` is your organization's name), not the address-bar URL.
4. From the solution root, add the remote and push:
   ```
   git remote add origin https://github.com/<org>/oop-sample.git
   git push -u origin main
   ```
   `-u` (short for `--set-upstream`) links your local `main` to `origin`'s `main`, so later `git push` / `git pull` need no arguments.
5. On the repo page, click the gear next to **About** and paste the same description text the `gh repo create` command in Project Setup uses.

### If the class diagram doesn't render

The `plantuml4idea` plugin needs Graphviz for some diagrams. If `docs/class-diagram.puml` shows an error instead of a rendered diagram, install Graphviz and restart the IDE.

On macOS:

```
brew install graphviz
```

On Windows, download and run the installer from [graphviz.org](https://graphviz.org/download/).

### Free JetBrains license for students

Rider, like every other JetBrains IDE, is free while you're a student. Apply with your university email at [jetbrains.com/shop/eform/v2/students](https://www.jetbrains.com/shop/eform/v2/students). The license covers the whole JetBrains suite and renews each year you're enrolled.

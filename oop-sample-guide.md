# OOP Sample Guide

## Table of Contents

- [Project Setup](#project-setup)
- [(US001) Register a Supplier](#register-a-supplier-us001)
- [(US002) Create a Purchase Order](#create-a-purchase-order-us002)
- [(US003) Add Items to a Purchase Order](#add-items-to-a-purchase-order-us003)
- [(US004) Calculate Purchase Order Item Subtotal](#calculate-purchase-order-item-subtotal-us004)
- [(US005) Calculate Purchase Order Total](#calculate-purchase-order-total-us005)
- [Wrap-Up](#wrap-up)
- [Release](#release)
- [(US006) Merge Duplicate Items in a Purchase Order](#merge-duplicate-items-in-a-purchase-order-us006)
- [Document the Project](#document-the-project)
- [Testing (optional, explore on your own)](#testing-optional-explore-on-your-own)
- [Appendix](#appendix)

## Project Setup

1. **Open Rider and create the new solution.**
   - Solution already open: `File` → `New Solution...`
   - On the Welcome screen (no solution open yet): click **New Solution**, or `File` → `New Solution...` if that screen shows a `File` menu

   In the wizard's sidebar, pick **Project Type: Console**, then fill in:
   - Solution name: `oop-sample`
   - Project name: `Acme.OOProgramming`
   - Solution directory: any local path you prefer, no need to match a specific folder
   - **Create Git repository**: leave it unchecked (`git init -b main` is done by hand in step 6)
   - Target framework: `net10.0`
   - Click **Create**
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

   **Note:** you're implementing a given architecture, not designing one. The client (here, this course) sets DDD and this bounded-context split as part of the **Definition of Done**, not something negotiated project by project. Reading a given architecture correctly and implementing it well is a skill just as real as designing one from scratch.

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

   **Note:** if the diagram doesn't render, PlantUML needs Graphviz installed separately. macOS: `brew install graphviz`, then restart the IDE. Windows: install it from [graphviz.org](https://graphviz.org/download/) and restart.

   **Note:** `SupplyChain` and `Procurement` each have their own `SupplierId` on the diagram, deliberately. Each context owns the identity type of the aggregate it holds (`SupplierId` belongs to SupplyChain, home of `Supplier`); no other context references it directly, and Procurement defines its own. That's **Context Mapping** in practice, not an accident. More on why in `## Document the Project` later.

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
6. **Enable Git and make the first commit.** Open Rider's **Terminal** tool window (bottom toolbar); it opens at the solution root (`oop-sample/`) by default. Run:
   ```
   git init -b main
   git config user.name "Your Name"
   git config user.email "your.email@example.com"
   git add .
   git commit -m "chore: initial commit."
   ```

   **Note:** `git init` runs at the solution root so the whole solution ends up tracked, not just one project inside it. Every terminal block in this guide reuses this same session, so they all stay at the solution root.

   **Note:**
   - `-b main` (short for `--initial-branch`) names the first branch `main`. It needs to be `main` here to match what **Git Flow Helper** (installed shortly) expects.
   - `git config` without `--global` scopes this to just this repo.
7. **Connect to GitHub.**

   **Sign in first.** Install the GitHub CLI once:
   - macOS: `brew install gh`
   - Windows: `winget install --id GitHub.cli`, or the installer from [cli.github.com](https://cli.github.com/) if you don't have `winget`

   Then `gh auth login` → `GitHub.com` → `HTTPS` → **Login with a web browser** (accept the defaults on the other prompts). Paste the one-time code into the page it opens and authorize. This sets up git too, so your pushes won't ask for anything.

   **Create the private repo and push.** Two ways, pick one. Either way, the repo's About description is:
   `Console application demonstrating object-oriented programming (OOP) and domain-driven design (DDD) principles within the context of SupplyChain and Procurement domains.`

   - **With the GitHub CLI,** one command from the solution root:
     ```
     gh repo create <org>/oop-sample --private --source=. --remote=origin --push --description "Console application demonstrating object-oriented programming (OOP) and domain-driven design (DDD) principles within the context of SupplyChain and Procurement domains."
     ```
     It creates the repo in the org, adds it as `origin`, pushes `main`, and sets the About text.
   - **The traditional way:**
     - On GitHub, create an empty **private** repo named `oop-sample` in the org (no README/license/`.gitignore`, this repo has all of that).
     - Copy its **Clone → HTTPS** URL from the "Quick setup" page (ends in `.git`, e.g. `https://github.com/<org>/oop-sample.git`), **not** the address bar URL.
     - Add the remote and push:
       ```
       git remote add origin https://github.com/<org>/oop-sample.git
       git push -u origin main
       ```
     - On the repo page, click the gear icon next to **About** and paste the description from above.

   **Note:** `--source=.` uses the current folder; `--remote=origin --push` adds the remote and pushes `main`; `--description` fills the About text.

   **Note:** on a shared machine, run `gh auth logout` first in case someone else is still signed in.

   **Note:** if you can't install `gh`, GitHub also takes a Personal Access Token: see *Appendix: Signing in to GitHub with a token*.

   **Note:** the About text is GitHub's own repo-level summary (repo page + org/search listings), separate from `README.md`.

   **Note:** `-u` (short for `--set-upstream`) links your local `main` to `origin`'s `main`, so later `git push`/`git pull` need no arguments.
8. **Install the Git Flow Helper plugin.**
   - macOS: `Rider → Settings → Plugins → Marketplace → search "Git Flow Helper"`
   - Windows: `File → Settings → Plugins → Marketplace → search "Git Flow Helper"`
9. **Initialize Git Flow.**
   - Run `gh auth token` in the terminal and copy what it prints. Git Flow Helper pushes through Rider's own GitHub connection, not the one `gh` set up for the terminal, so Rider needs its own account and you'll paste this token in a moment.
   - Click the Git Flow Helper widget in the status bar → `Init`.
   - The branch prefix fields (`Main`, `Develop`, `Feature`, `Release`, `Hotfix`) are pre-filled with sensible defaults; click `OK`.
   - Rider then shows a **Login with GitHub** popup, since pushing the new `develop` branch is its first push. Click **`Log In with Token`**, paste the token, and confirm.

   This creates a `develop` branch from `main` and pushes it to `origin`.

   **Note:** use `Log In with Token`, not `Log In via GitHub...`. The browser (OAuth) sign-in gives a token your organization blocks for third-party apps, and the push then fails with "Repository not found". The `gh auth token` value is the same one your terminal git already uses, so it works.

   **Note:** if the popup doesn't appear, or you dismissed it, add the account by hand: `Settings` → `Version Control` → `GitHub` → `+` → **`Log In with Token...`** → paste the `gh auth token` value. Then run `Init` again from the widget.

   **Note:** from here on, `main` is only touched through a Release or Hotfix, never worked on directly.

   **Tip:** the current branch name should show in the status bar (bottom-right, branch icon + name). If nothing shows, it's disabled by default: right-click an empty area of the status bar → check `Git Branch` in the widget list.

## Register a Supplier ([US001](./user-stories.md))

1. **Start the feature.** Git Flow Helper widget in the status bar → `Feature` → `Feature Start` → **Feature description** `register-supplier` → `OK`. Creates and switches you to `feature/register-supplier`.

   **Tip:** need to stop before the feature is done? See *Appendix: Backing up unfinished work*.
2. **Create the `Supplier` aggregate (properties only for now).** Switch the Solution Explorer dropdown to **Solution** view now (Project Setup left it on **File System** view). From here through the user stories you're adding C# types, so it stays on **Solution** view except where a step says otherwise. Re-read **US001**'s `Scenario: Successfully register a supplier` first (a valid code, name, and address in, a registered supplier out). Right-click the project root in the Solution Explorer → `Add` → `Class/Interface` → type the full path and name in the **Name** field, `SupplyChain/Domain/Model/Aggregates/Supplier` → Enter.

   **Tip:** the first time you add a file, Rider may pop up an "Add File to Git" dialog. Check `Don't ask again` and click `Cancel`, so it won't ask again. This guide stages and commits through explicit `git add`/`git commit`, not Rider's add-on-create prompt.

   Write only the properties:
   - `Id` (`SupplierId`), `get`-only
   - `Name` / `Address` (`Address`), auto-implemented `get; init;`, no validation yet

   **Note:**
   - All three are write-once: assigned in the constructor, never reassignable after. `init` doesn't loosen that, it just permits that initial assignment.
   - `Id` stays a bare `get;` because `SupplierId` validates itself. `Name` and `Address` use `init` because each grows a validation body in that accessor in steps 5 and 6, and a guard needs an accessor to live in.

   **Tip:** `SupplierId` and `Address` don't resolve yet, that's fine. The IDE tells you what's missing; you stub both in the next step and flesh them out in step 8.

   **Tip:** first time a C# property shows up in this track:
   - `public SupplierId Id { get; }` has a getter, no setter, so it's only assignable inside the constructor, this project's equivalent of Java's `final`.
   - `get; init;` is the auto-implemented shorthand: `init` (not `set`) allows assignment only in the constructor or an object initializer, never after.

   <details>
   <summary>Supplier.cs (attributes only)</summary>

   ```csharp
   using Acme.OOProgramming.Shared.Domain.Model.ValueObjects;
   using Acme.OOProgramming.SupplyChain.Domain.Model.ValueObjects;

   namespace Acme.OOProgramming.SupplyChain.Domain.Model.Aggregates;

   public class Supplier
   {
       public SupplierId Id { get; }
       public string Name { get; init; }
       public Address Address { get; init; }
   }
   ```
   </details>

3. **Create `SupplierId` and `Address` as stubs.** `Supplier` references both. Create them now with just enough shape to compile; you'll harden them in step 8, once `Supplier` itself is done. Two ways to create each file, both used throughout this course:
   - **From the unresolved reference** (good when building the domain up one type at a time): cursor on `SupplierId` in `Supplier.cs` → `Option+Enter` (macOS) / `Alt+Enter` (Windows) → the type-creation quick-fix. Rider generates it inline; make it a `readonly record struct`. Move it to its own file: cursor on the new type → `Fn+F6` (macOS) / `F6` (Windows) → pick **`Move To Folder`** from the popup → set the target folder to `SupplyChain/Domain/Model/ValueObjects` → confirm. Same for `Address`, target folder `Shared/Domain/Model/ValueObjects`.
   - **From the Solution Explorer** (what you'll mostly do once the repo is large and you're pasting complete code from a guide): right-click the project root → `Add` → `Class/Interface` → type `SupplyChain/Domain/Model/ValueObjects/SupplierId`, **select `Record Struct`** → Enter. Same for `Shared/Domain/Model/ValueObjects/Address`.

   A positional `readonly record struct` for each, no validation yet:

   <details>
   <summary>SupplierId.cs (stub)</summary>

   ```csharp
   namespace Acme.OOProgramming.SupplyChain.Domain.Model.ValueObjects;

   public readonly record struct SupplierId(string Identifier);
   ```
   </details>

   <details>
   <summary>Address.cs (stub)</summary>

   ```csharp
   namespace Acme.OOProgramming.Shared.Domain.Model.ValueObjects;

   public readonly record struct Address(
       string Street, string Number, string City, string? StateOrRegion, string PostalCode, string Country);
   ```
   </details>

   **Note:** read `readonly record struct` as "a small, immutable value".
   - `record` gives it value-based equality: two `SupplierId`s with the same `Identifier` are equal.
   - `struct` + `readonly` make it behave like a number: copied when you pass it, never `null`, no separate object on the heap.
   - It's a `struct` and not a `class` because a `SupplierId` is a value, not a thing with its own identity. Making it a `class` would cost a heap allocation per instance and buy nothing back.

4. **Add `Supplier`'s constructors (happy path, no validation yet).** Both stubs exist now, in different namespaces than `Supplier`, so Rider underlines them red: cursor on each → `Option+Enter` (macOS) / `Alt+Enter` (Windows) → `using Acme.OOProgramming...;`. Then write:
   - **The full constructor** `Supplier(SupplierId id, string name, Address address)`: assigns the three properties directly.
   - **A thin convenience constructor** `Supplier(string identifier, string name, Address address)`: wraps a raw string into a `SupplierId` and delegates to the full one.

   **Note:** same `using`-fixing mechanism every time a new file references a type from another namespace, for the rest of the guide.

   **Note:** the convenience constructor is what `Program.cs` uses shortly: a raw string is what a caller has on hand, not a `SupplierId` instance already.

   **Tip:** this is the first point everything compiles. `new Supplier(new SupplierId("SUP001"), "Supplier Inc.", address)` already satisfies `Scenario: Successfully register a supplier`, even though invalid input isn't rejected yet.

   <details>
   <summary>Supplier.cs (happy path, no validation yet)</summary>

   ```csharp
   public SupplierId Id { get; }
   public string Name { get; init; }
   public Address Address { get; init; }

   public Supplier(SupplierId id, string name, Address address)
   {
       Id = id;
       Name = name;
       Address = address;
   }

   public Supplier(string identifier, string name, Address address)
       : this(new SupplierId(identifier), name, address)
   {
   }
   ```
   </details>

5. **Add the name guard.** Re-read `Scenario: Invalid supplier name`: an empty name in, an exception with a clear message out. Replace the auto-implemented `Name` property with one that validates in its `init` accessor.

   **Tip:** try writing it yourself first. Which accessor does it belong in? Not the constructor, this project's validation lives right next to the property it protects.

   <details>
   <summary>Supplier.cs (addition: name guard)</summary>

   ```csharp
   public string Name
   {
       get;
       init
       {
           ArgumentException.ThrowIfNullOrWhiteSpace(value);
           field = value;
       }
   }
   ```
   </details>

6. **Add the address guard.** Re-read `Scenario: Invalid supplier address`: a missing address in (a `default(Address)`; once `Address` is fleshed out in step 8 it also guarantees its own fields are never blank), an exception out. This one checks `== default` instead of blank, since `Address` isn't a `string`. Replace the auto-implemented `Address` property the same way.

   **Tip:** try it yourself first, same shape as the name guard.

   <details>
   <summary>Supplier.cs (addition: address guard)</summary>

   ```csharp
   public Address Address
   {
       get;
       init
       {
           if (value == default)
               throw new ArgumentException("Supplier address must be provided.", nameof(value));
           field = value;
       }
   }
   ```
   </details>

7. **Add `Supplier`'s identity methods.** `Equals()`, `GetHashCode()`, `ToString()`.

   **Note:** `Equals()` / `GetHashCode()` compare only `Id`, not every property. An aggregate's identity is what makes two instances "the same", not their current state, unlike a `readonly record struct` (like `SupplierId` / `Address`), which gets value-based equality for free from every component. As a plain `class`, `Supplier` gets none of that automatically, so it's written by hand, comparing identity only.

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

   `Supplier` is complete, enforcing every scenario from US001's acceptance criteria against the value objects it will receive. The full file:

   <details>
   <summary>Supplier.cs (no docs)</summary>

   ```csharp
   using Acme.OOProgramming.Shared.Domain.Model.ValueObjects;
   using Acme.OOProgramming.SupplyChain.Domain.Model.ValueObjects;

   namespace Acme.OOProgramming.SupplyChain.Domain.Model.Aggregates;

   public class Supplier
   {
       public SupplierId Id { get; }

       public string Name
       {
           get;
           init
           {
               ArgumentException.ThrowIfNullOrWhiteSpace(value);
               field = value;
           }
       }

       public Address Address
       {
           get;
           init
           {
               if (value == default)
                   throw new ArgumentException("Supplier address must be provided.", nameof(value));
               field = value;
           }
       }

       public Supplier(SupplierId id, string name, Address address)
       {
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

   That's the version you type by hand. The committed file also carries full XML docs:

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
       /// <exception cref="ArgumentException">Thrown when the name is null or blank.</exception>
       public string Name
       {
           get;
           init
           {
               ArgumentException.ThrowIfNullOrWhiteSpace(value);
               field = value;
           }
       }

       /// <summary>
       /// The address of the supplier.
       /// </summary>
       /// <exception cref="ArgumentException">Thrown when the address is not initialized.</exception>
       public Address Address
       {
           get;
           init
           {
               if (value == default)
                   throw new ArgumentException("Supplier address must be provided.", nameof(value));
               field = value;
           }
       }

       /// <summary>
       /// Creates a new instance of <see cref="Supplier"/>.
       /// </summary>
       /// <param name="id">The supplier identifier.</param>
       /// <param name="name">The supplier name.</param>
       /// <param name="address">The supplier address.</param>
       public Supplier(SupplierId id, string name, Address address)
       {
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

8. **Flesh out `SupplierId` and `Address`.** `Supplier` is done; now make the two stubs real. Replace each positional record with its full form: every required property validating in its own `init` accessor, a blocked parameterless constructor, `ToString()`.

   **Note:** the one catch with a `struct`: it can't be `null`, but it can be `default` (all-zero fields). Every struct has a parameterless constructor you can't remove, so `default(SupplierId)` is always legal and never runs your validation. The code handles that:
   - `Identifier` validates in a full `init` body (C# 13 `field` keyword), and `get` returns `field ?? string.Empty`, so a stray `default(SupplierId)` reads back a safe empty string, not `null`.
   - the blocked `SupplierId()` only stops `new SupplierId()`; the `?? string.Empty` fallback is what covers `default(SupplierId)`.

   <details>
   <summary>SupplierId.cs (no docs)</summary>

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

       public SupplierId() => throw new InvalidOperationException("SupplierId must be initialized with a non-empty identifier.");

       public SupplierId(string identifier) => Identifier = identifier;

       public override string ToString() => Identifier;
   }
   ```
   </details>

   That's the version you type by hand. The committed file also carries full XML docs:

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

   `Address` follows the same pattern, with more properties: `Street` / `Number` / `City` / `StateOrRegion` (nullable) / `PostalCode` / `Country`. Each required property validates itself in its own `init` accessor, plus a blocked parameterless constructor and `ToString()`.

   **Note:** `StateOrRegion` is the one exception: a plain auto-implemented nullable property (`public string? StateOrRegion { get; init; }`), no full accessor body needed, since `null` is already a valid, safe value for an optional field, there's no `default`-bypass gap to close there the way there is for the other five.

   **Note:** the five length limits (`100`, `10`, `100`, `20`, `100`) are named `const`s, not magic numbers repeated inline: each constant makes the limit's meaning obvious at the declaration site and keeps the guard and its own error message from silently drifting apart if the limit ever changes.

   <details>
   <summary>Address.cs (no docs)</summary>

   ```csharp
   namespace Acme.OOProgramming.Shared.Domain.Model.ValueObjects;

   public readonly record struct Address
   {
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

       public string? StateOrRegion { get; init; }

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
   }
   ```
   </details>

   That's the version you type by hand. The committed file also carries full XML docs:

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

   ```
   git add .
   git commit -m "feat(supplier): add supplier aggregate, supplier id and address value objects."
   ```

   **Note:** this is the first point where `Supplier` and its value objects are all real, `Supplier` enforcing every invariant from US001 and the value objects validating themselves. That's the meaningful unit of work worth committing. If you didn't commit earlier, everything from step 2 lands here together.
9. **Register the supplier in `Program.cs`.** Delete the wizard's default `Console.WriteLine("Hello, World!");` first. Then create an `Address` and a `Supplier` from it, using SupplyChain's own `SupplierId`, and print the supplier's own `ToString()`.

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
10. **Publish and finish the feature.** Click the Git Flow Helper widget in the status bar → `Feature` → `Feature Publish`, then the widget again → `Feature` → `Feature Finish`. Merges `feature/register-supplier` into `develop` and pushes it too.

   **Note:** `Feature Publish` is the only push of `feature/register-supplier`: it pushes the branch to `origin` (setting it up there too, same as the very first push of `main` did back in Project Setup), no manual `git push -u` needed.

   **Tip:** in the `Feature Finish` dialog:
   - "What to do when finished": pick `Integrate Immediately` (not the merge-request options; this course merges features directly, no PR review step).
   - Uncheck `Keep remote branch when finished`. Leave the pre-filled commit message (`Merge branch 'feature/register-supplier' into develop`) as-is.

   **Note:** deleting the remote branch on finish keeps the repo clean, same as GitHub's own "Delete branch" prompt after merging a PR.

   **Note:** how this maps to a real pull-request workflow: see *Appendix: Feature Finish and pull requests*.

   From here on the later features show this step condensed as "**Publish and finish the feature.**", which always means exactly this sequence.

## Create a Purchase Order ([US002](./user-stories.md))

1. **Start the feature.** Feature description: `create-purchase-order` → `OK`. Creates and switches you to `feature/create-purchase-order`.
2. **Create the `PurchaseOrder` aggregate.** Right-click the project root → `Add` → `Class/Interface` → type `Procurement/Domain/Model/Aggregates/PurchaseOrder` → Enter (`Class` is selected by default). Re-read US002's `Scenario: Invalid order number`, `Scenario: Invalid supplier`, and `Scenario: Invalid currency` first, then write:
   - `OrderNumber` / `SupplierId` / `OrderDate` / `Currency` properties
   - a constructor that validates each one
   - `Equals()` / `GetHashCode()` / `ToString()`

   **Tip:** try writing the three checks yourself first. `SupplierId` doesn't resolve yet, that's fine; the IDE tells you what's missing.

   **Note:** identity-based equality, comparing only `OrderNumber`, this aggregate's natural business key rather than a generated surrogate ID (same reasoning as `Supplier`).

   **Note:** no items list yet: `PurchaseOrderItem` doesn't exist until Feature 3, where the items collection is added alongside it. `ToString()` gets extended then too, to include the item count.

   <details>
   <summary>PurchaseOrder.cs (validation)</summary>

   ```csharp
   public PurchaseOrder(string orderNumber, SupplierId supplierId, DateTime orderDate, string currency)
   {
       ArgumentException.ThrowIfNullOrWhiteSpace(orderNumber);
       if (supplierId == default)
           throw new ArgumentException("Supplier ID is required.", nameof(supplierId));
       if (string.IsNullOrWhiteSpace(currency) || currency.Length != 3)
           throw new ArgumentException("Currency must be a valid 3-letter code.", nameof(currency));

       OrderNumber = orderNumber;
       SupplierId = supplierId;
       OrderDate = orderDate;
       Currency = currency;
   }
   ```
   </details>

   Then the complete file, no docs yet (`PurchaseOrder.cs` gets revised several more times as later features and refactors land, this isn't its last commit):

   <details>
   <summary>PurchaseOrder.cs (so far)</summary>

   ```csharp
   using Acme.OOProgramming.Procurement.Domain.Model.ValueObjects;

   namespace Acme.OOProgramming.Procurement.Domain.Model.Aggregates;

   public class PurchaseOrder
   {
       public string OrderNumber { get; }
       public SupplierId SupplierId { get; }
       public DateTime OrderDate { get; }
       public string Currency { get; }

       public PurchaseOrder(string orderNumber, SupplierId supplierId, DateTime orderDate, string currency)
       {
           ArgumentException.ThrowIfNullOrWhiteSpace(orderNumber);
           if (supplierId == default)
               throw new ArgumentException("Supplier ID is required.", nameof(supplierId));
           if (string.IsNullOrWhiteSpace(currency) || currency.Length != 3)
               throw new ArgumentException("Currency must be a valid 3-letter code.", nameof(currency));

           OrderNumber = orderNumber;
           SupplierId = supplierId;
           OrderDate = orderDate;
           Currency = currency;
       }

       public override bool Equals(object? obj)
       {
           return obj is PurchaseOrder other && OrderNumber == other.OrderNumber;
       }

       public override int GetHashCode() => OrderNumber.GetHashCode();

       public override string ToString() => $"PurchaseOrder[OrderNumber={OrderNumber}, SupplierId={SupplierId}, OrderDate={OrderDate}, Currency={Currency}]";
   }
   ```
   </details>

3. **Create Procurement's own `SupplierId` value object.** Right-click the project root → `Add` → `Class/Interface` → type `Procurement/Domain/Model/ValueObjects/SupplierId`, **select `Record Struct`** → Enter. Same shape as SupplyChain's, but a completely separate type.

   **Note:** this is the Context Mapping call from the Project Setup diagram showing up in code: **Procurement never imports SupplyChain's `SupplierId`**, it models its own reference to a supplier independently, even though both refer to the same real-world supplier.

   <details>
   <summary>SupplierId.cs (no docs)</summary>

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

       public SupplierId() => throw new InvalidOperationException("SupplierId must be initialized with a non-empty identifier.");

       public SupplierId(string identifier) => Identifier = identifier;

       public override string ToString() => Identifier;
   }
   ```
   </details>

   That's the version you type by hand. The committed file also carries full XML docs:

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

   Then back in `PurchaseOrder.cs`, add the missing `using` so `SupplierId` resolves to Procurement's own type, not SupplyChain's:
   ```csharp
   using Acme.OOProgramming.Procurement.Domain.Model.ValueObjects;
   ```

   ```
   git add .
   git commit -m "feat(purchase-order): add purchase order aggregate and its own supplier id."
   ```
4. **Create the order in `Program.cs`.** Procurement's own `SupplierId` now shares a name with SupplyChain's, so the earlier `new SupplierId("SUP001")` call is ambiguous. Add an alias at the top of `Program.cs`, next to the other `using` directives:
   ```csharp
   using SupplyChainSupplierId = Acme.OOProgramming.SupplyChain.Domain.Model.ValueObjects.SupplierId;
   ```
   Then:
   - **Replace** `new SupplierId("SUP001")` with `new SupplyChainSupplierId("SUP001")`
   - Add a `PurchaseOrder` right after, translating the supplier's raw identifier into Procurement's own `SupplierId`
   - Use `DateTime.UtcNow`, not `DateTime.Now`, for the order date

   **Tip:** `PurchaseOrder` and the bare `SupplierId` are new to this file: resolve them with Rider's auto-import quick-fix as you type, or add the `using`s by hand.

   **Note:** a real system timestamps things in UTC, never the server's local time zone, so timestamps stay consistent across servers and deployments.

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

   ```
   git add .
   git commit -m "feat(main): create a purchase order for the registered supplier."
   ```
5. **Publish and finish the feature.** Git Flow Helper widget → `Feature` → `Feature Publish`, then → `Feature Finish` (`Integrate Immediately`, `Keep remote branch when finished` unchecked). Merges into `develop` and pushes it too, no extra `git push` needed after.

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

   That's the version you type by hand. The committed file also carries full XML docs:

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
3. **Create the `Money` value object, complete.** Right-click the project root → `Add` → `Class/Interface` → type `Shared/Domain/Model/ValueObjects/Money` → Enter. Needed now for the first time, since an item's unit price is a `Money`. Write:
   - `record` with `Amount` / `Currency`
   - constructor validating `Currency` is a 3-letter code and `Amount` is not negative
   - `ToString()`, `Add()`, `Multiply()`

   **Note:** `Money` doesn't depend on anything else in the project, so unlike `PurchaseOrder` / `SupplierId` there's no unresolved type forcing you to split it across features.

   **Note:** `Add()` / `Multiply()` aren't used yet (Features 3 and 4), but they belong to `Money` itself, not to whichever feature needs them first. `Add()` rejects a null argument and mismatched currencies: adding USD to EUR should never silently produce a USD-labeled result with the wrong amount.

   <details>
   <summary>Money.cs (so far)</summary>

   ```csharp
   namespace Acme.OOProgramming.Shared.Domain.Model.ValueObjects;

   public record Money
   {
       public decimal Amount { get; init; }
       public string Currency { get; init; }

       public Money(decimal amount, string currency)
       {
           ArgumentOutOfRangeException.ThrowIfNegative(amount);
           ArgumentException.ThrowIfNullOrWhiteSpace(currency);
           if (currency.Length != 3)
           {
               throw new ArgumentException("Currency must be a valid 3-letter ISO code.", nameof(currency));
           }
           Amount = amount;
           Currency = currency;
       }

       public override string ToString() => $"{Amount} {Currency}";

       public Money Add(Money? other)
       {
           ArgumentNullException.ThrowIfNull(other);
           if (Currency != other.Currency)
           {
               throw new ArgumentException("Cannot add different currencies.", nameof(other));
           }
           return new Money(Amount + other.Amount, Currency);
       }

       public Money Multiply(int factor) => Multiply((decimal)factor);

       public Money Multiply(decimal factor)
       {
           ArgumentOutOfRangeException.ThrowIfNegative(factor);
           return new Money(Amount * factor, Currency);
       }
   }
   ```
   </details>

   No XML docs yet: `Money` gets refactored to a `readonly record struct` in a couple of steps, and again once `Currency` becomes its own value object, this still isn't its last commit.

   ```
   git add .
   git commit -m "feat(money): add money value object."
   ```
4. **Create the `PurchaseOrderItem` entity.** Right-click the project root → `Add` → `Class/Interface` → type `Procurement/Domain/Model/Aggregates/PurchaseOrderItem` → Enter. Re-read US003's `Scenario: Invalid product ID` and `Scenario: Invalid quantity` first. Write:
   - an **`internal`** constructor `(ProductId productId, int quantity, Money unitPrice)` with guards (product ID not default, `quantity > 0`, unit price not default)
   - `ProductId` / `Quantity` / `UnitPrice` properties
   - `Equals()` / `GetHashCode()` / `ToString()`, value-based on all three

   **Tip:** try writing the two guards yourself first.

   **Note:** this is an **entity**, not an aggregate root: it's managed by `PurchaseOrder`, never created or looked up on its own, so the constructor is `internal`.

   **Note:** since `PurchaseOrder.Items` exposes it publicly (next step), it needs value equality so a caller can meaningfully compare or print one, unlike `Supplier` / `PurchaseOrder`, which compare by identity. `PurchaseOrderItem` has no identity type of its own.

   <details>
   <summary>PurchaseOrderItem.cs (validation)</summary>

   ```csharp
   if (productId == default)
   {
       throw new ArgumentException("Product ID is required.", nameof(productId));
   }
   ArgumentOutOfRangeException.ThrowIfNegativeOrZero(quantity);
   if (unitPrice == default)
   {
       throw new ArgumentException("Unit price is required.", nameof(unitPrice));
   }
   ```
   </details>

   Then the complete file, no docs yet (`PurchaseOrderItem.cs` gets revised twice more later, this isn't its last commit):

   <details>
   <summary>PurchaseOrderItem.cs (so far)</summary>

   ```csharp
   using Acme.OOProgramming.Procurement.Domain.Model.ValueObjects;
   using Acme.OOProgramming.Shared.Domain.Model.ValueObjects;

   namespace Acme.OOProgramming.Procurement.Domain.Model.Aggregates;

   public class PurchaseOrderItem
   {
       internal PurchaseOrderItem(ProductId productId, int quantity, Money unitPrice)
       {
           if (productId == default)
           {
               throw new ArgumentException("Product ID is required.", nameof(productId));
           }
           ArgumentOutOfRangeException.ThrowIfNegativeOrZero(quantity);
           if (unitPrice == default)
           {
               throw new ArgumentException("Unit price is required.", nameof(unitPrice));
           }

           ProductId = productId;
           Quantity = quantity;
           UnitPrice = unitPrice;
       }

       public ProductId ProductId { get; }
       public int Quantity { get; }
       public Money UnitPrice { get; }

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
   git commit -m "feat(purchase-order-item): add purchase order item entity."
   ```
5. **Add `PurchaseOrder.AddItem()`, the items collection, and the extended `ToString()`.** Re-read `Scenario: Invalid product ID`, `Scenario: Invalid quantity`, and `Scenario: Invalid unit price` from the aggregate's side. `AddItem(ProductId, int quantity, decimal unitPriceAmount)`:
   - three guards, same shape as `PurchaseOrderItem`'s, but `unitPriceAmount` is a `decimal` this time
   - build a `Money` using the order's own `Currency`, construct the item, append it
   - add the items collection itself, exposed as `IReadOnlyList<PurchaseOrderItem>`

   **Tip:** try writing the three guards yourself first.

   **Note:** `AddItem()` re-validating what `PurchaseOrderItem`'s constructor already enforces is on purpose: an aggregate never trusts a caller to have validated correctly on its own. Validating `unitPriceAmount` as a `decimal` rejects a negative value before `Money` is even constructed.

   **Note:** `IReadOnlyList<PurchaseOrderItem>` is the .NET read-only view: callers can enumerate it but never add/remove/replace an entry. `PurchaseOrderItem` didn't exist until this feature, so there was nothing to hold a list of until now.

   **Note:** `ToString()` also changes here. **Replace** the one written in Feature 2 with the version below, don't paste this addition below it, or the class ends up with two `ToString()` methods and won't compile. The only difference is `Items={_items.Count}` added to the interpolated string.

   <details>
   <summary>PurchaseOrder.cs (addition: AddItem guards)</summary>

   ```csharp
   if (productId == default)
       throw new ArgumentException("Product ID is required.", nameof(productId));
   ArgumentOutOfRangeException.ThrowIfNegativeOrZero(quantity);
   ArgumentOutOfRangeException.ThrowIfNegative(unitPriceAmount);
   ```
   </details>

   Then the full addition:

   <details>
   <summary>PurchaseOrder.cs (addition, replaces the Feature 2 ToString())</summary>

   ```csharp
   private readonly List<PurchaseOrderItem> _items = new();

   public IReadOnlyList<PurchaseOrderItem> Items => _items.AsReadOnly();

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

   public override string ToString() =>
       $"PurchaseOrder[OrderNumber={OrderNumber}, SupplierId={SupplierId}, OrderDate={OrderDate}, Items={_items.Count}, Currency={Currency}]";
   ```
   </details>

   ```
   git add .
   git commit -m "feat(purchase-order): add items collection and add item method."
   ```
6. **Add items in `Program.cs`.** Add two items to the order and print `Items.Count`.

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
7. **Convert `Money` to a `readonly record struct`.** Swap `Money.cs` for the version below (it also adds operator overloads for `+` and `*`).

   **Note:** it was just written as a plain `record`, the right first draft, no reason to reach for value-type semantics before a real caller exists. Now one does: `PurchaseOrder.AddItem()`. Value objects with no identity of their own are natural `struct` candidates: no heap allocation, copied instead of referenced, still get structural equality for free from `record`. Same reasoning as `Address` / `SupplierId` (Features 1-2) and `ProductId` (earlier this feature).

   <details>
   <summary>Money.cs (readonly record struct, so far)</summary>

   ```csharp
   namespace Acme.OOProgramming.Shared.Domain.Model.ValueObjects;

   public readonly record struct Money
   {
       public decimal Amount { get; init; }
       public string Currency { get; init; }

       public Money(decimal amount, string currency)
       {
           ArgumentOutOfRangeException.ThrowIfNegative(amount);
           ArgumentException.ThrowIfNullOrWhiteSpace(currency);
           if (currency.Length != 3)
           {
               throw new ArgumentException("Currency must be a valid 3-letter ISO code.", nameof(currency));
           }
           Amount = amount;
           Currency = currency;
       }

       public override string ToString() => $"{Amount} {Currency}";

       public Money Add(Money? other)
       {
           ArgumentNullException.ThrowIfNull(other);
           if (Currency != other.Value.Currency)
           {
               throw new ArgumentException("Cannot add different currencies.", nameof(other));
           }
           return new Money(Amount + other.Value.Amount, Currency);
       }

       public Money Multiply(int factor) => Multiply((decimal)factor);

       public Money Multiply(decimal factor)
       {
           ArgumentOutOfRangeException.ThrowIfNegative(factor);
           return new Money(Amount * factor, Currency);
       }

       public static Money operator +(Money left, Money right) => left.Add(right);
       public static Money operator *(Money money, decimal factor) => money.Multiply(factor);
       public static Money operator *(decimal factor, Money money) => money.Multiply(factor);
       public static Money operator *(Money money, int factor) => money.Multiply(factor);
       public static Money operator *(int factor, Money money) => money.Multiply(factor);
   }
   ```
   </details>

   Still no XML docs: `Currency` becomes its own value object next, and `Money.Currency`'s type changes because of it, so this isn't `Money`'s last commit.

   ```
   git add .
   git commit -m "refactor(money): convert money to a readonly record struct."
   ```

   **Note:** `Add()`'s body now reads `other.Value.Currency` / `other.Value.Amount` instead of `other.Currency` / `other.Amount`. `Money?` on a struct means `Nullable<Money>`, not "maybe a reference" the way it did on a `record` class, so its members aren't reachable without unwrapping through `.Value` first, even after the null-check.

   **Note:** two things in this version beyond the `struct` keyword:
   - The currency check now leads with `ArgumentException.ThrowIfNullOrWhiteSpace(currency)`, the guard-clause style used in every constructor here.
   - Five `operator` overloads (`+`, `*` in every operand order). They work the same on a `record` class; it's just a natural moment to add them. With them, arithmetic on `Money` reads like ordinary numeric code, `.Add()` / `.Multiply()` still there underneath.

   **Note:** `PurchaseOrderItem`'s constructor needs no change here: its `unitPrice` guard already reads `if (unitPrice == default)` from Feature 3, not an `ArgumentNullException.ThrowIfNull`, so it already means the right thing on both sides of this conversion.

   **Note:** this is a real trade-off, not a free win. A stray `default(Money)` (`Amount == 0`, `Currency == default`) is now silently constructible, where a stray `null` on the old `record` class version would have failed loudly. The guarantee from here on comes from disciplined `== default` guards at every point one of these structs crosses into an aggregate, plus a test for each. Keep this in mind for `## Testing`: every constructor that used to guard against `null` now guards against `default`.
8. **Implement the `Currency` value object.** The design diagram already shows a dedicated `Currency`; build it now. Right-click the project root → `Add` → `Class/Interface` → type `Shared/Domain/Model/ValueObjects/Currency`, **select `Record Struct`** → Enter.

   **Note:** `Money.Currency` and `PurchaseOrder.Currency` currently run the same 3-letter validation independently, two copies of a rule that could drift apart, and nothing stops a caller from comparing a currency code against any other 3-character string by mistake. That's the primitive-obsession gap `Currency` closes. Same reasoning as `SupplierId` and `ProductId`.

   **Note:** `Currency` becomes a `readonly record struct`, small enough and exercised immediately enough (every `Money` / `PurchaseOrder` comparison touches it) to be as low-risk a struct candidate as `Money` itself.

   <details>
   <summary>Currency.cs (no docs)</summary>

   ```csharp
   namespace Acme.OOProgramming.Shared.Domain.Model.ValueObjects;

   public readonly record struct Currency
   {
       public string Code
       {
           get => field ?? string.Empty;
           init
           {
               ArgumentException.ThrowIfNullOrWhiteSpace(value);
               if (value.Length != 3 || !value.All(char.IsAsciiLetter))
                   throw new ArgumentException("Currency must be a valid 3-letter ISO code.", nameof(Code));
               field = value.ToUpperInvariant();
           }
       }

       public Currency() => throw new InvalidOperationException("Currency must be initialized with a valid 3-letter code.");

       public Currency(string code) => Code = code;

       public override string ToString() => Code;
   }
   ```
   </details>

   That's the version you type by hand. The committed file also carries full XML docs:

   <details>
   <summary>Currency.cs</summary>

   ```csharp
   namespace Acme.OOProgramming.Shared.Domain.Model.ValueObjects;

   /// <summary>
   /// Represents a currency value object.
   /// </summary>
   public readonly record struct Currency
   {
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
               if (value.Length != 3 || !value.All(char.IsAsciiLetter))
                   throw new ArgumentException("Currency must be a valid 3-letter ISO code.", nameof(Code));
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

   **Note:** `Currency`'s own `Code` property validates inside its `init` accessor, using the C# 14 `field` keyword, instead of the constructor body; its parameterless constructor is blocked the same way `Money`'s now is, `new Currency()` throws, closing part of the same `default`-bypass gap `Money` has.

   Swap `Money.Currency` and `PurchaseOrder.Currency` from `string` to `Currency`, keeping a `string`-accepting overload on both constructors so every existing call site (`Program.cs`, the tests) keeps compiling unchanged:

   <details>
   <summary>Money.cs (Currency property and constructors)</summary>

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
   ```
   </details>

   `Amount`'s guard clause moves the same way, from the constructor body into its own `init` accessor. `Money.cs` doesn't change again after this: full file, with the XML docs that ship in the real repo:

   **Note:** `Add()` / `Multiply()` get a real upgrade here, not just a type-signature change:
   - The old `Money? other` parameter only caught a genuine `null`. A struct passed as `Money?` is never `null` unless the caller writes it explicitly, so `other.Add(default)` slipped past the null-check and failed later with a misleading "different currencies" message.
   - Both methods now check `Currency == default` on every operand directly, no `Nullable<Money>` wrapping, and report the real issue with `InvalidOperationException`.

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
       /// <param name="left">The first monetary operand.</param>
       /// <param name="right">The second monetary operand.</param>
       /// <returns>The sum of the two monetary values.</returns>
       public static Money operator +(Money left, Money right) => left.Add(right);

       /// <summary>
       /// Multiplies a <see cref="Money"/> value by a decimal factor.
       /// </summary>
       /// <param name="money">The monetary value.</param>
       /// <param name="factor">The multiplier factor.</param>
       /// <returns>The multiplied monetary value.</returns>
       public static Money operator *(Money money, decimal factor) => money.Multiply(factor);

       /// <summary>
       /// Multiplies a <see cref="Money"/> value by a decimal factor.
       /// </summary>
       /// <param name="factor">The multiplier factor.</param>
       /// <param name="money">The monetary value.</param>
       /// <returns>The multiplied monetary value.</returns>
       public static Money operator *(decimal factor, Money money) => money.Multiply(factor);

       /// <summary>
       /// Multiplies a <see cref="Money"/> value by an integer factor.
       /// </summary>
       /// <param name="money">The monetary value.</param>
       /// <param name="factor">The multiplier factor.</param>
       /// <returns>The multiplied monetary value.</returns>
       public static Money operator *(Money money, int factor) => money.Multiply(factor);

       /// <summary>
       /// Multiplies a <see cref="Money"/> value by an integer factor.
       /// </summary>
       /// <param name="factor">The multiplier factor.</param>
       /// <param name="money">The monetary value.</param>
       /// <returns>The multiplied monetary value.</returns>
       public static Money operator *(int factor, Money money) => money.Multiply(factor);
   }
   ```
   </details>

   ```
   git add .
   git commit -m "refactor(shared): extract currency into its own value object."
   ```
9. **Publish and finish the feature.** Git Flow Helper widget → `Feature` → `Feature Publish`, then → `Feature Finish` (`Integrate Immediately`, `Keep remote branch when finished` unchecked). Merges into `develop` and pushes it too, no extra `git push` needed after.

## Calculate Purchase Order Item Subtotal ([US004](./user-stories.md))

1. **Start the feature.** Feature description: `calculate-item-subtotal` → `OK`. Creates and switches you to `feature/calculate-item-subtotal`.
2. **Add `PurchaseOrderItem.CalculateItemTotal()`.** Re-read `Scenario: Successfully calculate item subtotal`. It's `UnitPrice * Quantity`. `Money` is a `readonly record struct` since Feature 3, so this reads as ordinary numeric arithmetic, no `.Multiply()` call needed.

   <details>
   <summary>PurchaseOrderItem.cs (addition)</summary>

   ```csharp
   public Money CalculateItemTotal() => UnitPrice * Quantity;
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

   **Note:** this is the first LINQ / functional-style code in the track, worth breaking down:
   - `_items.Sum(item => item.CalculateItemTotal().Amount)` calls the OOP method `CalculateItemTotal()` on every item (via the lambda `item => ...`), reads its `.Amount` (LINQ's `Sum` needs a plain `decimal`, not a `Money`), and adds all the `decimal`s together, no manual loop, no running-total variable.
   - The result is wrapped back into a `Money` using the order's own `Currency`.

   Nothing here is "new" functional logic: `CalculateItemTotal()` is the same OOP method already written in Feature 4. The functional style is just a different way of *composing* that existing method over a collection, instead of writing an explicit `foreach`.

   <details>
   <summary>PurchaseOrder.cs (addition)</summary>

   ```csharp
   public Money CalculateTotal()
   {
       var total = _items.Sum(item => item.CalculateItemTotal().Amount);
       return new Money(total, Currency);
   }
   ```
   </details>

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

## Wrap-Up

**All of this happens on `develop`:** `Feature Finish` always leaves you there after merging, so no branch switch is needed to start.

1. **Re-run `Program.cs` end to end** and confirm all output prints in order. Compare it against the diagram from `## Project Setup`: `Address`, `Supplier`, `SupplierId`, `PurchaseOrder`, `PurchaseOrderItem`, `ProductId`, and `Money` are all real code now, exactly as sketched. `Currency` and the `DateOnly` order date are still ahead.

## Release

**Still on `develop`, right where Wrap-Up left off.** A release is a *batch* of finished features, not one per feature. These 5 user stories together are one sprint's worth of work, exactly the kind of thing a real release bundles.

1. **Start the release.** Git Flow Helper widget → `Release` → `Release Start` → **Version description** `1.0.0` → `OK`. Creates and switches you to `release/1.0.0`.

   **Tip:** the `push local branch when finished` checkbox doesn't matter much either way here; step 4 below publishes the branch properly regardless.
2. **Bump the version in `Acme.OOProgramming.csproj`.** Switch the Solution Explorer dropdown to **File System** view (Solution view hides the `.csproj`, and this whole section edits it and adds plain files). Change `<Version>0.1.0-preview</Version>` to `<Version>1.0.0</Version>`.
   ```
   git add .
   git commit -m "chore(release): bump version to 1.0.0."
   ```

   **Note:** that one edit does two things. The `-preview` suffix comes off, since that's never what ships. And the version jumps straight to `1.0.0`, not `0.1.1` / `0.2.0`: this is the first release meant to be stable, exactly what reaching `1.0.0` signals.
3. **Add `CHANGELOG.md`.** Still in **File System** view, right-click the project root → `Add` → `File` → type `CHANGELOG.md` → Enter:

   <details>
   <summary>CHANGELOG.md</summary>

   ```markdown
   # Changelog

   ## 1.0.0

   - US001: Register a Supplier
   - US002: Create a Purchase Order
   - US003: Add Items to a Purchase Order
   - US004: Calculate Purchase Order Item Subtotal
   - US005: Calculate Purchase Order Total
   ```
   </details>

   ```
   git add .
   git commit -m "docs: add changelog for 1.0.0."
   ```

   **Note:** this commit matters beyond documentation, it's the reason `Release Finish` has something to merge into `develop`. A release branch with no commits of its own merges into `develop` as a no-op (no merge commit there) while still creating a real merge commit on `main`. That mismatch is what makes `develop` briefly show as "behind" `main` on GitHub. With a real commit here, both merges are real and `develop` / `main` land in sync on their own.
4. **Publish the release branch.** Git Flow Helper widget → `Release` → `Release Publish`. Pushes `release/1.0.0` with both commits included. Do this every time after adding a commit to a release branch, right before finishing it, same as `Feature Publish`.

   **Note:** `Release Finish` also tries to push the release branch as part of its own sequence, but that push is broken in this plugin (it pushes the tag name instead of the branch). `Release Publish` is what actually gets it there.
5. **Finish the release.** Git Flow Helper widget → `Release` → `Release Finish`. No dialog, it runs immediately: merges `release/1.0.0` into `main` (tags it `1.0.0` there), merges it into `develop` too, pushes both, then deletes the release branch. A notification confirms: "Released finished and tag pushed successfully."

   **Note:** skipping step 4 can leave the local branch delete failing with a "branch not fully merged" warning, since git compares against a stale remote-tracking ref. Afterward, GitHub may show `develop` as slightly "ahead" / "behind" `main`: that's expected (each branch gets its own separate merge commit) and not something to fix; the file content already matches.
6. **Publish the GitHub Release.** On GitHub: **Releases** → **Draft a new release** → pick the existing tag `1.0.0` (created by `Release Finish`, don't create a new one). Title `1.0.0`, description below, **Publish release**.

   **Note:** the branch selector on that screen only matters when creating a brand-new tag on the spot; since this tag already exists and points at a commit on `main`, it's ignored. The GitHub Release is a feature layered on top of the tag, separate from Git Flow itself, which only ever creates the tag.

   <details>
   <summary>Release notes (1.0.0)</summary>

   ```markdown
   ## 🚀 Added

   - **US001: Register a Supplier**: register a `Supplier` with an identifier, name, and address, in its own SupplyChain bounded context.
   - **US002: Create a Purchase Order**: create a `PurchaseOrder` for a `Supplier`, with order number, date, and currency validation.
   - **US003: Add Items to a Purchase Order** and **US004/US005: Calculate Purchase Order Item/Total Subtotal**: add `PurchaseOrderItem`s to a `PurchaseOrder`, with running total calculation.
   - Value Objects `Address`, `Money`, `SupplierId`, `ProductId`, modeled as C# records.
   - `CHANGELOG.md` to track version history going forward.
   ```
   </details>

   **Tip:** the same thing works from the command line: `gh release create 1.0.0 --title "1.0.0" --notes-file CHANGELOG.md` (the [GitHub CLI](https://cli.github.com/), `gh`, authenticated once via `gh auth login`). `--notes-file` accepts any Markdown file; `CHANGELOG.md` works directly here since the tag already exists.
7. **Back on `develop`, pick the `-preview` suffix back up.** Git Flow Helper switches you to `develop` automatically after `Release Finish`. Still in **File System** view, in `Acme.OOProgramming.csproj`: `<Version>1.0.0</Version>` → `<Version>1.1.0-preview</Version>`.

   **Note:** skipping straight to `1.1.0-preview` (not `1.0.1-preview`) says out loud what's already planned: the sections below add real new capabilities (a value-type refactor, a new user story, and a full test suite), not just a bugfix, and semantic versioning reserves the middle number for that.
   ```
   git add .
   git commit -m "chore(dev): set development version to 1.1.0-preview."
   git push
   ```


## Merge Duplicate Items in a Purchase Order ([US006](./user-stories.md))

**A real requirement change, arriving after `1.0.0` shipped.** Everything up to here (Features 1-5) matches US001-US005 exactly. This one is different: a realistic case of a real procurement team using the shipped product and reporting back a genuine usability problem. `docs/user-stories.md` isn't a frozen, one-time deliverable, it grows exactly like this. This is not optional exploration, it ships in the same `1.1.0` release as the documentation work below and the test suite; `## Testing` further down writes the tests for it alongside every other user story.

1. **Start the feature.** Feature description: `merge-duplicate-items` → `OK`. Creates and switches you to `feature/merge-duplicate-items`.
2. **Document US006 first, before any code.** Add it to `docs/user-stories.md`, right after US005.

   <details>
   <summary>docs/user-stories.md (addition)</summary>

   ```markdown
   ## US006: Merge Duplicate Items in a Purchase Order
   As a procurement manager, I want adding a product that's already on the purchase order to combine into the existing line instead of creating a new one, so that my purchase order doesn't show confusing duplicate entries for the same product.

   ### Scenario: Successfully merge quantities for a repeated product
   - **Given** a purchase order "PO001" with an item for product ID "X", quantity 10, unit price amount 15.99 in USD
   - **When** the procurement manager adds another item for the same product ID "X", quantity 5, unit price amount 15.99
   - **Then** the purchase order still has one item for product ID "X", now with quantity 15

   ### Scenario: Merging keeps the original unit price
   - **Given** a purchase order "PO001" with an item for product ID "X", quantity 10, unit price amount 15.99 in USD
   - **When** the procurement manager adds another item for the same product ID "X", quantity 5, unit price amount 19.99
   - **Then** the item for product ID "X" keeps its original unit price of 15.99 USD, unaffected by the newly provided amount

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
3. **Decide which price wins on a re-add.** This is a genuinely ambiguous design decision, not just a bug fix: if the same product is re-added at a *different* price, which price wins? The choice here: **keep the line's original unit price**, discard the newly provided one.

   **Note:** a purchase order line represents a price agreed at a specific point in time; silently overwriting it on every re-add could misrepresent what was actually negotiated. Just implement it for now; the reasoning against the alternative (overwrite with the new price) is worth talking through.
4. **Implement the merge in `PurchaseOrder.AddItem()`.** Search `_items` for a matching `ProductId`: if found, replace it with a new item combining the quantity and keeping the original `UnitPrice`; otherwise append a new line same as before. The three validation guards at the top don't change, this is purely about what happens after them.

   **Tip:** try writing the lookup-and-merge logic yourself first.

   <details>
   <summary>PurchaseOrder.cs (addition, replaces AddItem())</summary>

   ```csharp
   public void AddItem(ProductId productId, int quantity, decimal unitPriceAmount)
   {
       if (productId == default)
           throw new ArgumentException("Product ID is required.", nameof(productId));
       ArgumentOutOfRangeException.ThrowIfNegativeOrZero(quantity);
       ArgumentOutOfRangeException.ThrowIfNegative(unitPriceAmount);

       var existingIndex = _items.FindIndex(item => item.ProductId == productId);
       if (existingIndex >= 0)
       {
           var existing = _items[existingIndex];
           _items[existingIndex] = new PurchaseOrderItem(productId, existing.Quantity + quantity, existing.UnitPrice);
           return;
       }

       var unitPrice = new Money(unitPriceAmount, Currency);
       var item = new PurchaseOrderItem(productId, quantity, unitPrice);
       _items.Add(item);
   }
   ```
   </details>

   ```
   git add .
   git commit -m "feat(purchase-order): merge quantities when adding an existing product."
   ```
5. **Confirm it still compiles.** Run `dotnet build`.

   **Note:** the three scenarios above don't get their own tests yet, they're written together with the rest of the suite in `## Testing` below.
6. **Publish and finish the feature.** Git Flow Helper widget → `Feature` → `Feature Publish`, then → `Feature Finish` (`Integrate Immediately`, `Keep remote branch when finished` unchecked). Merges into `develop` and pushes it too.

## Document the Project

**Still on `develop`, no Git Flow feature needed:** writing down decisions already made, including the one just made for US006 above and the `Money`/`Currency` refactor back in Feature 3. Same release, same reasoning: it ships in `1.1.0` too.

1. **Generate the XML documentation that ships in every file's final version.** In **File System** view (Solution view hides the `.csproj`), add one property to `Acme.OOProgramming.csproj`, in the same `<PropertyGroup>` as `<Version>`:
   ```xml
   <GenerateDocumentationFile>true</GenerateDocumentationFile>
   ```
   Run `dotnet build`, then open `Acme.OOProgramming/bin/Debug/net10.0/Acme.OOProgramming.xml` in a text editor. Every `<summary>`/`<param>`/`<returns>`/`<exception>` comment written across all 5 features turns into a real, structured XML file, exactly what IntelliSense reads to show tooltips, and what tools like DocFX turn into a browsable static site.

   **Note:** Expect a batch of `CS1591` warnings ("missing XML comment for publicly visible member") on properties/methods that never got their own explicit `<summary>`, only a class-level one: a real, honest gap this flag surfaces, not a sign anything is broken. A team with a strict docs policy would either add per-property comments or explicitly suppress `CS1591`; either is a legitimate call, just make it on purpose.
2. **Close the last gap with the diagram: `PurchaseOrder.OrderDate` becomes a `DateOnly`.** Keep a `DateTime`-accepting constructor overload so `Program.cs` and the existing test suite keep compiling unchanged, only converting internally. Swap `PurchaseOrder.cs` for the version below: only the type of `OrderDate`/the constructor's parameter changed, and a new `DateTime`-accepting constructor was added.

   **Note:** It's been a `DateTime` since Feature 2, a placeholder for the `DateOnly` the design already called for: an order date is a calendar business date, not an instantaneous timestamp, nothing in this domain ever needed the time-of-day or time-zone component `DateTime` carries.

   **Note:** `AddItem()`/`CalculateTotal()`/`Equals()`/`GetHashCode()`/`ToString()` are unchanged from Feature 3/US006. Still no docs on this block either: `AddItem()` gets revised once more, later in this same section, so this still isn't `PurchaseOrder.cs`'s last commit.

   <details>
   <summary>PurchaseOrder.cs (so far)</summary>

   ```csharp
   using Acme.OOProgramming.Procurement.Domain.Model.ValueObjects;
   using Acme.OOProgramming.Shared.Domain.Model.ValueObjects;

   namespace Acme.OOProgramming.Procurement.Domain.Model.Aggregates;

   public class PurchaseOrder
   {
       private readonly List<PurchaseOrderItem> _items = new();
       private IReadOnlyList<PurchaseOrderItem>? _itemsView;

       public string OrderNumber { get; }
       public SupplierId SupplierId { get; }
       public DateOnly OrderDate { get; }
       public Currency Currency { get; }

       public IReadOnlyList<PurchaseOrderItem> Items => _itemsView ??= _items.AsReadOnly();

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

   **Note:** The starter test suite's `Constructor_WithValidArguments_InitializesSuccessfully` (in `## Testing` below) switches its own `_orderDate` field from `new DateTime(2025, 3, 29)` to `new DateOnly(2025, 3, 29)` to match; every other test still passes a `DateTime.UtcNow` straight through the new compatibility constructor, unaffected.
   ```
   git add .
   git commit -m "refactor(purchase-order): represent the order date as a DateOnly."
   ```
3. **Add a `Presentation` layer: console formatting kept out of the domain model.** C# 14 extension members, in their own `*.Presentation` namespaces, one per bounded context that needs one.

   **Note:** `Program.cs` has been interpolating `PurchaseOrder`/`Money` directly into `Console.WriteLine` calls since Feature 2, duplicating the same formatting expression at every call site. Neither type gets a display-specific `ToString()`, that would mix a presentation concern into the domain model, the same coupling this codebase has stayed free of everywhere else.

   Back in **Solution** view, right-click the project root → `Add` → `Class/Interface` → type `Shared/Presentation/ConsoleFormatting`, **select `Class`** → Enter:

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

   That's the version you type by hand. The committed file also carries full XML docs:

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

   Same way, `Procurement/Presentation/ConsoleFormatting`:

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

   That's the version you type by hand. The committed file also carries full XML docs:

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

   `internal` keeps both out of this project's public surface, they're a console-app-only concern. Update `Program.cs` to use them instead of ad hoc interpolation:

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
   var salesOfDay = new Money(0, "USD");

   // Procurement never uses SupplyChain's SupplierId directly: translate its raw identifier here
   var purchaseOrder = new PurchaseOrder("PO001", new SupplierId(supplier.Id.Identifier), DateOnly.FromDateTime(DateTime.UtcNow), "USD");
   var sharedProduct = ProductId.New();
   purchaseOrder.AddItem(sharedProduct, 10, 25.99m);
   purchaseOrder.AddItem(sharedProduct, 5, 25.99m);
   purchaseOrder.AddItem(ProductId.New(), 20, 19.99m);

   Console.WriteLine(purchaseOrder.Summary);
   foreach (var item in purchaseOrder.Items)
   {
       Console.Write($"Order Item: {item.ProductId} x {item.Quantity} at Unit Price of {item.UnitPrice.Display} ");
       Console.WriteLine($"Results in Order Item Total: {item.CalculateItemTotal().Display}");
   }

   Console.WriteLine($"Order Total: {purchaseOrder.CalculateTotal().Display}");

   try
   {
       purchaseOrder.AddItem(sharedProduct, 1, 9.99m);
   }
   catch (InvalidOperationException ex)
   {
       Console.WriteLine($"Rejected conflicting unit price: {ex.Message}");
   }

   Console.WriteLine($"Sales for the day: {salesOfDay.Add(purchaseOrder.CalculateTotal()).Display}");

   Console.WriteLine($"Supplier: {supplier.Name} is located at {supplier.Address}");
   ```
   </details>

   Run it: `order.Summary` and `item.CalculateItemTotal().Display` read exactly like real properties on `PurchaseOrder`/`Money`, even though neither type was touched, only two new `using` directives were added.

   **Note:** this step also folds in a few small additions to `Program.cs` (all behavior already covered by tests, just not visible when the program runs):
   - add `sharedProduct` twice to show the US006 merge in action (`10` then `5`, watch `Quantity` come out as `15`)
   - a `try`/`catch` around a third add at a conflicting price, to see `AddItem()`'s rejection actually fire
   - a running `salesOfDay` total accumulated with `Money`'s own `+` operator across orders
   - a final line printing the `Supplier`'s own `ToString()`
   ```
   git add .
   git commit -m "feat(presentation): add console formatting via extension members."
   ```
4. **Revisit the US006 decision one more time.** `AddItem()` currently discards a conflicting new price in silence, keeping the existing line's price. The revised decision: reject the call instead. Update the scenario in `docs/user-stories.md`:

   **Note:** On review, that is a real API surprise for anyone calling it: a caller who explicitly passes a different price gets no signal that it was ignored.

   <details>
   <summary>docs/user-stories.md (US006, replaces "Merging keeps the original unit price")</summary>

   ```markdown
   ### Scenario: Merging at a different price is rejected
   - **Given** a purchase order "PO001" with an item for product ID "X", quantity 10, unit price amount 15.99 in USD
   - **When** the procurement manager adds another item for the same product ID "X", quantity 5, unit price amount 19.99
   - **Then** the purchase order rejects the call, the item for product ID "X" keeps its original quantity of 10 and unit price of 15.99 USD
   ```
   </details>

   `PurchaseOrderItem` also changes: it is an entity, not a value object (the class summary already says so), so instead of `PurchaseOrder.AddItem()` rebuilding a new instance on every merge, `PurchaseOrderItem` grows its own intention-revealing method to change its own state:

   <details>
   <summary>PurchaseOrderItem.cs (Quantity setter and new method)</summary>

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

   `PurchaseOrderItem.cs` doesn't change again after this: full file, with the XML docs that ship in the real repo:

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
           {
               throw new ArgumentException("Product ID is required.", nameof(productId));
           }
           ArgumentOutOfRangeException.ThrowIfNegativeOrZero(quantity);
           if (unitPrice == default)
           {
               throw new ArgumentException("Unit price is required.", nameof(unitPrice));
           }

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

   And `PurchaseOrder.AddItem()` calls it instead of rebuilding the list entry, throwing when the price conflicts:

   <details>
   <summary>PurchaseOrder.cs (AddItem, revised)</summary>

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

   `PurchaseOrder.cs` doesn't change again after this: full file, with the XML docs that ship in the real repo.

   **Note:** One more small tightening lands silently in this step: `CalculateTotal()` drops the Feature 5 `_items.Sum(item => item.CalculateItemTotal().Amount)` round trip (compute every subtotal, unwrap each to a bare `decimal`, sum those, then re-wrap the sum in a new `Money`) for a plain `foreach` that accumulates directly into a running `Money` total via the `+` operator introduced back when `Money` became a struct. Same result, one less unwrap-then-rewrap step.

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

   Run `dotnet build` to confirm everything still compiles; `## Testing` below rewrites the one test that asserted the old silent-preserve behavior.
   ```
   git add .
   git commit -m "refactor(purchase-order): reject a duplicate product at a conflicting unit price."
   ```
5. **Add these eleven Architecture Decision Records (ADRs) to a single `docs/adrs.md` file** (in **File System** view: right-click the `docs` folder → `Add` → `File` → `adrs.md`), documenting every decision made so far, from Features 1-5 (including the `Money`/`Currency` refactor in Feature 3) through the four just-made refinements above, in one sitting rather than scattered one per feature: why value objects are `record`s (including identity types like `ProductId`, instead of a raw `Guid`), why each bounded context owns its own `SupplierId` instead of sharing one, why aggregates compare by identity and hide their internal collections, why `Money` is `decimal` + a validated currency code, never `double`, why `Money` alone, of the original value objects, became a `readonly record struct`, why merging a duplicate line originally kept its original price, why `Currency` was extracted into its own value object, why `PurchaseOrderItem` grew a controlled mutation method, why a conflicting price is now rejected instead of silently discarded, why `OrderDate` became a `DateOnly`, and why console formatting moved out to its own `Presentation` layer.

   **Note:** the standard ADR format is **Status**, **Context**, **Decision Drivers**, **Considered Options**, **Decision**, **Consequences**. Look it up if it's unfamiliar.

   <details>
   <summary>docs/adrs.md</summary>

   ````markdown
   # Architecture Decision Records

   # ADR-0001: Value Objects as C# Records

   **Status:** Accepted
   **Note:** Refined by [ADR-0005](#adr-0005-uniform-readonly-record-struct-adoption-for-value-objects), which settles every value object here on `readonly record struct` specifically, once `Money`'s own migration (Feature 3) showed the risk that shape carries can be closed with discipline instead of avoided by staying a class.

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

   All value objects, including aggregate identity types, are `record`s. Each property validates itself the moment it's assigned, inside its own `init` accessor, rather than the constructor body checking every field up front before assigning any of them. Wrapping an aggregate's identity in its own record (`SupplierId`, `ProductId`) closes the primitive-obsession gap for free, without hand-writing `Equals()`/`GetHashCode()` for it. Whether a given record ends up a reference type or, per ADR-0005, a `readonly record struct` is a separate question this ADR doesn't settle on its own.

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
   **Note:** Partially superseded by [ADR-0008](#adr-0008-purchaseorderitem-grows-an-intention-revealing-mutation-method), which allows PurchaseOrderItem to grow a controlled, intention-revealing mutation method.

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
   - `IReadOnlyList<T>.AsReadOnly()` is only a shallow, view-based guard: it blocks structural changes to the list itself, but doesn't prevent mutating an already-retrieved `PurchaseOrderItem` if that type ever grew a setter of its own (it doesn't, and shouldn't; the guard is defense in depth, not the only reason `PurchaseOrderItem` stays immutable).
   - `PurchaseOrder`'s identity is a plain `string` (`OrderNumber`), not a wrapped identity `record` like `SupplierId`/`ProductId` (see ADR-0001): it's a genuine business key supplied by the caller, not a generated surrogate, so wrapping it wouldn't add the same primitive-obsession protection ADR-0001 argues for elsewhere.

   ---

   # ADR-0004: Monetary Amounts as decimal + Currency Code

   **Status:** Accepted

   ## Context

   Money throughout the system needs to support fractional amounts, must never silently mix currencies, and must represent decimals exactly. Unlike Java, C# has a built-in `decimal` type designed specifically for financial and monetary calculations: base-10, fixed-point, no binary floating-point rounding error the way `double`/`float` have. The type itself isn't the whole problem, though: nothing about `decimal` alone stops two different currencies from being added together.

   ## Decision Drivers

   - Eliminate floating-point rounding error in monetary calculations (solved by choosing `decimal`, not `double`/`float`, in the first place).
   - Prevent operations that mix two different currencies (not solved by the type alone, needs explicit validation).
   - Keep the currency representation simple: a validated 3-letter ISO code (`string`), not a heavier `CultureInfo`/`RegionInfo` dependency this project doesn't otherwise need.

   ## Considered Options

   1. Immutable `Money` record backed by `decimal` + a validated 3-letter currency code *(Chosen)*
   2. `double`/`float` plus a separate currency code string
   3. Integer/long cents representation

   ## Decision

   `Money(decimal amount, string currency)` validates a non-null, non-blank, exactly-3-character currency code; `decimal` is used for `Amount` from the start, since C# already provides it as the correct built-in type for this, no reason to reach for `double`. `Add()` rejects a null argument and mismatched currencies; `Multiply()` scales the amount by an integer factor, always staying in the same currency.

   ```csharp
   public Money Add(Money? other)
   {
       ArgumentNullException.ThrowIfNull(other);
       if (Currency != other.Value.Currency)
       {
           throw new ArgumentException("Cannot add different currencies.", nameof(other));
       }
       return new Money(Amount + other.Value.Amount, Currency);
   }
   ```

   ## Consequences

   **Positive:**
   - No floating-point rounding surprises anywhere money is calculated: `decimal` is exact for base-10 fractions like currency amounts, unlike `double`/`float`.
   - Cross-currency bugs (adding USD to EUR) are caught immediately, not silently wrong.

   **Negative / Trade-offs:**
   - The currency code is a plain, validated `string`, not a dedicated `CultureInfo`-backed currency type; correct for this project's scope (a validated ISO code is all four user stories need), but wouldn't scale to formatting/locale-aware display without a real currency type layered on top later.
   - Every caller must remember to use `decimal` literals (e.g. `25.99m`, with the `m` suffix), not `double`; nothing in the type system prevents passing a converted `double` value that already lost precision before it ever reached `Money`.

   ---

   # ADR-0005: Uniform readonly record struct Adoption for Value Objects

   **Status:** Accepted
   **Note:** Started as a `Money`-only decision (see the original reasoning below, kept for the mechanics it teaches); extended to every value object once the risk it originally hedged against turned out to be closable by discipline, not avoidable only by staying a class.

   ## Context

   `Money` is a small, two-field value object (`decimal Amount`, `Currency Currency`) used constantly in arithmetic (`Add()`, `Multiply()`) throughout the Procurement bounded context. As a `record` (reference type), every `Money` instance, including every per-item subtotal `PurchaseOrderItem.CalculateItemTotal()` produces, is heap-allocated. C# offers `readonly record struct` as a value-type alternative: same generated structural equality, but stack-allocated (or inlined into the containing type) instead of heap-allocated.

   Not every value object in this codebase looked like an equally good candidate at first. C# structs (including `readonly record struct`) always have an implicit parameterless constructor the language does not allow removing: `default(Money)` is legal anywhere a `Money` is expected, and it never runs the type's own validating constructor. A value with `Amount == 0` and `Currency == default` can exist without ever passing through `Money(decimal, Currency)`. The open question this ADR originally left unresolved: is that risk specific to `Money`, or does it apply just as much to `Address`/`SupplierId`/`ProductId`?

   ## Decision Drivers

   - Reduce heap allocations for types constructed constantly: once per item on every `PurchaseOrder`, every time a total is calculated; once per `Supplier`/`PurchaseOrder` for their identity and address.
   - Value semantics (copy, not reference) fit "an amount of money", "a supplier's address", and "an identifier" more naturally than reference semantics.
   - Preserve the "always valid" guarantee value objects are supposed to have, as much as the type system allows: a struct default silently substituting for a validated instance would be a real regression, unless something else closes that gap.
   - Consistency: treating every value object the same way, once the risk is understood and closed, avoids an arbitrary split where some are structs and others stay classes for no principled reason.

   ## Considered Options

   1. Convert `Money` to `readonly record struct` first, then extend the same treatment to `Address`/`SupplierId`/`ProductId` once the risk is understood *(Chosen)*
   2. Convert `Money` only, leave `Address`/`SupplierId`/`ProductId` as `record` (class) permanently
   3. Leave every value object as `record` (class)

   ## Decision

   Every value object without an identity type of its own (`Money`, `Currency`, `Address`, `SupplierId`, `ProductId`) is a `readonly record struct`. `Money` went first, in Feature 3, and two properties specific to it made it the lowest-risk starting point:

   - **It's exercised immediately after construction.** Both `Add()` and `Multiply()` return their result through `new Money(...)`, which re-runs the full constructor validation, including the currency check, every single time. A stray `default(Money)` (`Currency == default`) fails loudly the moment it's used in either operation, including inside `CalculateItemTotal()` (`UnitPrice * Quantity` calls `Multiply()` internally), not silently downstream in `CalculateTotal()`.
   - **It's small.** Two fields, both cheap to copy. The .NET struct design guidance is to avoid structs larger than roughly 16 bytes, since copying a large struct on every assignment or parameter pass can cost more than the heap allocation it was meant to avoid.

   `Address`/`SupplierId`/`ProductId` don't share that first property on their own: nothing about constructing an `Address` re-validates it the way `Money.Add()` does. What closes the gap instead is the same pattern already used for `Currency`/`Money` themselves: every place one of these values gets handed to an aggregate, that aggregate re-validates it explicitly with `== default`, and every one of those guards has a test asserting it. `Supplier`'s constructor checks `Address == default`; `PurchaseOrder`'s constructor checks `SupplierId == default`; `PurchaseOrder.AddItem()` and `PurchaseOrderItem`'s own constructor both check `ProductId == default`. None of these types is exercised by a self-checking operation the way `Money.Add()` is, so the guarantee comes from discipline at every consumption point plus the tests that hold that discipline in place, not from the type itself:

   ```csharp
   public Address Address
   {
       get;
       init
       {
           if (value == default)
               throw new ArgumentException("Supplier address must be provided.", nameof(value));
           field = value;
       }
   }
   ```

   ## Consequences

   **Positive:**
   - No heap allocation for any of these value objects: every `Money`/`Address`/`SupplierId`/`ProductId` instance is stack-allocated (or inlined into its containing type) instead of a separate heap object.
   - Value semantics read naturally throughout: `PurchaseOrderItem.CalculateItemTotal()` reads `UnitPrice * Quantity`, ordinary numeric syntax, no `.Multiply()` method call needed; identity and address comparisons use plain `==`.
   - One consistent rule across the whole domain model instead of a split between "the struct" and "the classes", easier to teach and easier to extend correctly later: a new value object just follows the same pattern.

   **Negative / Trade-offs:**
   - `default(Money)`/`default(Address)`/`default(SupplierId)`/`default(ProductId)` are all constructible without validation; nothing in any of these types itself prevents this. The guarantee shifts entirely to the aggregate boundary: every constructor and every `AddItem()`-style method that accepts one of these values must remember to guard it explicitly, and forgetting one is a real, silent bug the type system won't catch. This project holds that guarantee through discipline plus a test for every guard, not through the type system alone, a materially weaker guarantee than "the type itself refuses to exist invalid" and a real trade-off, not a free win.
   - None of `PurchaseOrderItem`'s constructor guards can be an `ArgumentNullException` check any more, since a non-nullable struct parameter can never be `null` in the first place, the compiler statically proves it. `== default` is the only check the type system leaves available. This is a stronger guarantee than a runtime null-check would have been, but it also means a unit test asserting rejection of a `null` argument would never compile; the test suite asserts rejection of the `default` value instead.
   - Every place one of these five types crosses into an aggregate needs its own `== default` guard, by hand, at every call site; there is no way to express "this parameter cannot be the struct's own default" in the type system itself the way non-nullability expresses "this parameter cannot be null" for a reference type.

   ---

   # ADR-0006: Merging Duplicate Product Lines Preserves the Original Unit Price

   **Status:** Accepted
   **Note:** Superseded by [ADR-0009](#adr-0009-additem-rejects-a-duplicate-product-at-a-conflicting-unit-price), which rejects a duplicate product at a conflicting unit price instead of silently preserving the original price.

   ## Context

   US006 arrived after 1.0.0 shipped, well before 1.1.0 does, right after Features 1-5 were built: procurement managers reported that adding the same product twice to a purchase order created two separate, confusing line items instead of one combined quantity. `AddItem()`'s original behavior always appended a new `PurchaseOrderItem`, this was never wrong, just incomplete, US003's original acceptance criteria only ever described adding a single item, it never said anything about what should happen on a repeat.

   A second, related question surfaces once merging is on the table: if the product is re-added at a different unit price than the existing line (the supplier's price changed since the order was started, or the caller simply made a typo), which price should the merged line carry?

   ## Decision Drivers

   - Real procurement feedback: duplicate lines for the same product on one order are a genuine usability problem, not a hypothetical one.
   - A purchase order line represents a price agreed at a specific point in time; silently overwriting it on every re-add could misrepresent what was actually negotiated.
   - Whatever gets decided has to be traceable to an explicit acceptance criterion in `docs/user-stories.md`, not inferred from reading the code, the exact gap this feature exists to close.

   ## Considered Options

   1. Merge quantities into the existing line, keep the line's original unit price *(Chosen)*
   2. Merge quantities into the existing line, overwrite with the newly provided unit price
   3. Keep creating a separate line item per `AddItem()` call, regardless of repeats (the original Feature 2-3 behavior)

   ## Decision

   `AddItem()` now looks for an existing `PurchaseOrderItem` with the same `ProductId` before appending anything. If found, it's replaced with a new `PurchaseOrderItem` (immutable, so a new instance is required either way) carrying the combined quantity and the **original** unit price; the `unitPriceAmount` argument passed to that particular call is still validated (must be non-negative), but otherwise discarded, it does not override the existing line's price. If no matching item exists, behavior is unchanged from Features 1-2: a new line item is appended using the price provided.

   ```csharp
   var existingIndex = _items.FindIndex(item => item.ProductId == productId);
   if (existingIndex >= 0)
   {
       var existing = _items[existingIndex];
       _items[existingIndex] = new PurchaseOrderItem(productId, existing.Quantity + quantity, existing.UnitPrice);
       return;
   }
   ```

   ## Consequences

   **Positive:**
   - Matches the real usability complaint US006 was written for: no more duplicate lines for the same product.
   - The price a line was first agreed at can never be silently changed by a later, unrelated `AddItem()` call, protecting against accidentally recording the wrong price for goods already committed to at the original price.

   **Negative / Trade-offs:**
   - If a supplier genuinely changes their price mid-order and the procurement manager re-adds the product expecting the new price to apply, it silently doesn't, nothing in `AddItem()` surfaces this. A future revision might need an explicit `UpdateItemPrice()` method for that case, rather than overloading `AddItem()` to do it implicitly.
   - `unitPriceAmount` is validated but discarded on merge; a caller could reasonably expect it to always apply. This is a real API surprise, called out prominently in the XML doc comment on `AddItem()` for exactly that reason.

   ---

   # ADR-0007: Currency as a Dedicated Value Object

   **Status:** Accepted

   ## Context

   ADR-0004 chose a plain, validated 3-letter `string` for `Money.Currency`, explicitly flagging that choice wouldn't scale to a "real currency type layered on top later" without giving up the project's original simplicity. Nothing about that trade-off has changed on its own, but a shared kernel is exactly the place such a type belongs once the currency comparison logic (`Currency != other.Currency`) is duplicated wherever `Money` methods run, and once `PurchaseOrder.Currency` also became a bare `string` doing the exact same 3-letter validation independently. A raw string also lets any string be silently compared to `Currency` with no compiler help distinguishing "a currency code" from "any other 3-character string" (e.g. a product SKU).

   ## Decision Drivers

   - Eliminate the duplicated 3-letter validation logic that existed separately in both `Money` and `PurchaseOrder`.
   - Close the same primitive-obsession gap ADR-0001 already closed for `SupplierId`/`ProductId`, now for currency codes too.
   - Keep `Money`'s existing `readonly record struct` shape (ADR-0005) and its "always valid" story: swapping in a validated value object should not reopen that gap.

   ## Considered Options

   1. `Currency` as its own `readonly record struct`, used by both `Money` and `PurchaseOrder` *(Chosen)*
   2. Keep a raw `string`, deduplicate validation into a static helper method
   3. `Currency` as a `record` (class)

   ## Decision

   `Currency` becomes a `readonly record struct` (matching `Money`, `Address`, `SupplierId`, and `ProductId`, all `readonly record struct`s per ADR-0005): it's small (one field), and like `Money`, gets exercised immediately by comparisons and `ToString()` calls that would surface a stray `default` quickly. Its `Code` property validates inside its own `init` accessor (using the C# 14 `field` keyword) instead of the constructor body, and its own parameterless constructor is blocked (`public Currency() => throw ...`), the same defensive pattern `Money` already uses. `Money.Currency` and `PurchaseOrder.Currency` both become `Currency` instead of `string`; both types keep a `string`-accepting constructor overload (`Money(decimal, string)`, `PurchaseOrder(..., string)`) that just wraps the string in a `new Currency(...)`, so every existing call site in `Program.cs` and the tests keeps compiling unchanged.

   ```csharp
   public string Code
   {
       get => field ?? string.Empty;
       init
       {
           ArgumentException.ThrowIfNullOrWhiteSpace(value);
           if (value.Length != 3 || !value.All(char.IsAsciiLetter))
               throw new ArgumentException("Currency must be a valid 3-letter ISO code.", nameof(Code));
           field = value.ToUpperInvariant();
       }
   }
   ```

   With `Currency` now carrying a checkable `default` state of its own, `Money.Add()`/`Multiply()` are refined at the same time to check `Currency == default` directly on non-nullable `Money` operands, replacing the `Money? other` parameter ADR-0005 introduced:

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
   ```

   ## Consequences

   **Positive:**
   - One validated definition of "what a currency code is", instead of two copies that could silently drift apart.
   - `Money` also gets the same "block `new Money()`" guard `Currency` now has, directly answering part of ADR-0005's own documented negative trade-off ("nothing in `Money` itself prevents [a stray `default`]").
   - The `Code` property now also normalizes to uppercase and rejects non-letter characters, stricter than the length-only check `Money`/`PurchaseOrder` each used to run separately.
   - `Money.Add()`/`Multiply()` drop the `Money? other` parameter and its `.Value` unwrapping, ADR-0005's other documented readability cost: now that `Currency` carries a checkable `default` state, both methods check `Currency == default` directly on non-nullable `Money` operands instead, and, unlike the old null-check, this also catches a caller passing an uninitialized `default(Money)`, something `ArgumentNullException.ThrowIfNull(other)` never actually did on a struct parameter.

   **Negative / Trade-offs:**
   - Same `readonly record struct` caveat ADR-0005 already documents for `Money`: `default(Currency)` is still constructible without validation, `new Currency()` throws but `default(Currency)`/uninitialized fields do not; `Money`'s own `Currency` property `init` accessor guards against a `default` value being assigned, closing that gap one level up.
   - One more type to import wherever a currency code crosses an API boundary; call sites that only ever dealt with a `string` before now see a `Currency` in `Money`/`PurchaseOrder`'s public surface, even though the convenience `string` constructors keep simple call sites unchanged.

   ---

   # ADR-0008: PurchaseOrderItem Grows an Intention-Revealing Mutation Method

   **Status:** Accepted (supersedes part of ADR-0003)

   ## Context

   ADR-0003 originally decided `PurchaseOrderItem` should have no setters at all, reasoning that if it "ever grew a setter of its own... it doesn't, and shouldn't." Revisiting that stance: `PurchaseOrderItem` is explicitly documented, in its own class summary, as an entity managed by the `PurchaseOrder` aggregate, not a value object. Value objects earn immutability by having no identity of their own; entities are defined by the opposite, a life cycle and mutable state, tracked by something other than their current values. Under the original "no setters" rule, `PurchaseOrder.AddItem()` had to reconstruct the entity from outside on every merge (reading `existing.Quantity`/`existing.UnitPrice`, then building a brand-new `PurchaseOrderItem` to replace it in the list), which asks the aggregate to know how to rebuild one of its own entities instead of asking the entity to change itself, the opposite of "tell, don't ask."

   ## Decision Drivers

   - `PurchaseOrderItem` is documented as an entity, not a value object; DDD does not require entities to be immutable, only that state changes go through intention-revealing methods.
   - "Tell, don't ask": the aggregate should tell an item to increase its own quantity, not read its state and rebuild it.
   - Whatever changes here still needs to keep the guard ADR-0003's Decision Drivers actually require: "state changes must only happen through intention-revealing methods, never a public setter."

   ## Considered Options

   1. Add an `internal void IncreaseQuantity(int)` method, backed by a `private set` on `Quantity` *(Chosen)*
   2. Keep `PurchaseOrder.AddItem()` rebuilding a new `PurchaseOrderItem` on every merge, as before
   3. Make `Quantity`'s setter `public`, let any caller change it directly

   ## Decision

   `Quantity` keeps a `get`, but its `set` is now `private`, validated the same way the constructor already validates it (`ArgumentOutOfRangeException.ThrowIfNegativeOrZero`, via the C# 14 `field` keyword). A new `internal void IncreaseQuantity(int additionalQuantity)` method is the only thing that can invoke that setter from outside the property itself; `PurchaseOrder.AddItem()` calls `existing.IncreaseQuantity(quantity)` on a price match instead of rebuilding the item. This satisfies ADR-0003's actual Decision Driver (no *public* setter, every change goes through a named method) more directly than the original "no setters at all" implementation did.

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

   # ADR-0009: AddItem Rejects a Duplicate Product at a Conflicting Unit Price

   **Status:** Accepted (supersedes ADR-0006)

   ## Context

   ADR-0006 decided that re-adding an existing product at a different unit price should silently keep the original line's price, discarding the new amount. That ADR's own Consequences section already flagged the cost candidly: "`unitPriceAmount` is validated but discarded on merge; a caller could reasonably expect it to always apply. This is a real API surprise." Silently discarding a value the caller explicitly passed is exactly the kind of behavior DDD invariant protection argues against: an aggregate should fail loudly when an operation would conflict with an existing, already-committed piece of state, not quietly pick a winner on the caller's behalf.

   ## Decision Drivers

   - A purchase order line's price, once committed, represents a real agreement; silently overwriting or silently ignoring a conflicting price both hide a potential real-world discrepancy (a genuine supplier price change, or a caller's typo) from whoever is looking at the result.
   - ADR-0006 already named the concrete alternative in its own Negative/Trade-offs section (an explicit way to signal a price conflict), rather than requiring this ADR to invent one from nothing.
   - Failing fast on an ambiguous instruction is safer for financial data than resolving the ambiguity silently, in either direction.

   ## Considered Options

   1. Throw `InvalidOperationException` when the existing line's price differs from the newly provided one; merge quantities only when the price matches *(Chosen)*
   2. Keep ADR-0006's behavior: merge quantities, always keep the original price, discard the new one silently
   3. Merge quantities, always overwrite with the newly provided price

   ## Decision

   `AddItem()` still looks for an existing `PurchaseOrderItem` with the same `ProductId`. If one exists and the newly computed `Money` matches its `UnitPrice`, the quantities merge via `IncreaseQuantity()` (see ADR-0008). If the price differs, `AddItem()` throws `InvalidOperationException` naming both the conflicting price and the existing one, and the order's state is left unchanged (the exception is thrown before any mutation happens). If no matching item exists, behavior is unchanged: a new line is appended.

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
   - No more silent data loss: a caller who passes a conflicting price for an existing line finds out immediately, instead of the system quietly keeping a different value than what was requested.
   - Directly resolves the "real API surprise" ADR-0006 already flagged as a known cost of its own decision, without needing a separate `UpdateItemPrice()` method (the future revision ADR-0006 anticipated needing).

   **Negative / Trade-offs:**
   - This is a real, breaking behavior change from ADR-0006: any caller relying on "the second price is silently ignored" now gets an exception instead. `PurchaseOrderTests`'s merge test was rewritten to `AddItem_WithDuplicateProductAndConflictingPrice_ThrowsInvalidOperationException`, asserting the new behavior.
   - A legitimate price change from the supplier mid-order now requires the caller to handle the exception explicitly (e.g. by choosing a different `ProductId`, or by a future explicit `UpdateItemPrice()` method); there is still no built-in way to intentionally update an existing line's price.

   ---

   # ADR-0010: DateOnly for Purchase Order Dates

   **Status:** Accepted

   ## Context

   `PurchaseOrder.OrderDate` has been a `DateTime` since Feature 2. A purchase order date is a calendar business date, not an instantaneous timestamp: nothing in this domain ever needs the time-of-day or time-zone component `DateTime` carries, and every constructor so far has accepted whatever time-of-day the caller happened to pass (`DateTime.UtcNow`, an arbitrary `new DateTime(2025, 3, 29)`), including a meaningless `00:00:00` component whenever a caller only cared about the date.

   ## Decision Drivers

   - Represent domain intent precisely: an order date is a date, not a timestamp.
   - Eliminate a whole category of bugs this domain never needed to worry about: time-zone conversion, Daylight Saving Time edge cases, and two `DateTime` values differing only by time-of-day comparing as different order dates.
   - Keep every existing call site compiling: `Program.cs` and the test suite both construct `PurchaseOrder` with a `DateTime` today.

   ## Considered Options

   1. Convert `OrderDate` to `DateOnly`, keep a `DateTime`-accepting constructor overload that converts internally *(Chosen)*
   2. Convert `OrderDate` to `DateOnly`, remove the `DateTime` overload, force every call site to convert explicitly
   3. Leave `OrderDate` as `DateTime`

   ## Decision

   `PurchaseOrder`'s main constructor now takes a `DateOnly orderDate`. A second convenience constructor still accepts a `DateTime`, converting it via `DateOnly.FromDateTime(orderDate)` before delegating to the main one, so every existing caller (`Program.cs`'s `DateTime.UtcNow`, the test suite's `new DateTime(...)`) keeps compiling unchanged; only the discarded time-of-day component changes behavior, and it was never meaningful to begin with.

   ```csharp
   public PurchaseOrder(string orderNumber, SupplierId supplierId, DateTime orderDate, string currency)
       : this(orderNumber, supplierId, DateOnly.FromDateTime(orderDate), new Currency(currency)) { }
   ```

   ## Consequences

   **Positive:**
   - `OrderDate` now says exactly what it means: a calendar date, nothing more.
   - Two `PurchaseOrder`s created on the same calendar date but at different times of day now correctly compare as having the same `OrderDate`; that was a bug under `DateTime`, not a feature.
   - No time-zone conversion code was ever needed to fix this, `DateOnly` sidesteps the whole category of bugs by construction.

   **Negative / Trade-offs:**
   - A third constructor overload adds a small amount of surface area; a reader has to notice `DateOnly` is now the primary representation and `DateTime` is only a compatibility path.
   - Any future code that genuinely needs a time-of-day for something purchase-order-related (an audit timestamp, for instance) would need its own separate property, not `OrderDate`.

   ---

   # ADR-0011: Presentation Formatting via C# 14 Extension Members

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
   ````
   </details>

   ```
   git add .
   git commit -m "docs(adr): document value object, context mapping, encapsulation, money, struct, currency, entity mutation, merge, date, and presentation decisions."
   git push
   ```
6. **Add a Requirements Traceability Matrix to the top of `docs/user-stories.md`**, right after the title, before the individual user stories: one row per story, mapping it to the bounded context, the aggregate/entity it lives on, and the method that implements it. No Test Suite column yet, there's no test suite yet, `## Testing` below adds one to this same table if you get to it.

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
   | **US006** | Merge Duplicate Items in a Purchase Order | Procurement Context | [`PurchaseOrder`](../Acme.OOProgramming/Procurement/Domain/Model/Aggregates/PurchaseOrder.cs), [`PurchaseOrderItem`](../Acme.OOProgramming/Procurement/Domain/Model/Aggregates/PurchaseOrderItem.cs) | `AddItem(productId, quantity, unitPriceAmount)` (merge branch) |

   ---
   ```
   </details>

   ```
   git add .
   git commit -m "docs(user-stories): add requirements traceability matrix."
   git push
   ```
7. **Add `LICENSE.md` and `README.md`.** In **File System** view, right-click the project root → `Add` → `File` → type `LICENSE.md` → Enter (the README's badge links to it, so it needs to exist first).

   **Note:** a real public repo ships both, but neither belonged at Project Setup: back then there was no code, no test suite, no ADRs, nothing to describe yet.

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

   Same way, right-click the project root → `Add` → `File` → type `README.md` → Enter:

   <details>
   <summary>README.md</summary>

   ````markdown
   # OOP Sample

   [![.NET](https://img.shields.io/badge/.NET-10-purple.svg)](https://dotnet.microsoft.com/)
   [![C#](https://img.shields.io/badge/C%23-14-blue.svg)](https://learn.microsoft.com/dotnet/csharp/)
   [![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](LICENSE.md)
   [![Tests](https://img.shields.io/badge/Tests-passing-brightgreen.svg)](Acme.OOProgramming.Tests)

   ## Overview

   This project is a sample C# console application illustrating Object-Oriented Programming (OOP) and Domain-Driven Design (DDD) principles in a supply chain domain. It features two bounded contexts: SupplyChain (for supplier management) and Procurement (for purchase order management).

   ### Bounded Contexts & Domain Model

   **`Acme.OOProgramming.SupplyChain`** (Supply Chain Management)
   - `Supplier` (Aggregate Root): a vendor with identity and location.
   - `SupplierId` (Value Object): strongly-typed identifier, owned by SupplyChain.

   **`Acme.OOProgramming.Procurement`** (Procurement)
   - `PurchaseOrder` (Aggregate Root): purchase order invariants, currency consistency, and item lifecycle; `OrderDate` is a `DateOnly`, a calendar date with no time-of-day or time zone component (see [ADR-0010](docs/adrs.md#adr-0010-dateonly-for-purchase-order-dates)).
   - `PurchaseOrderItem` (Entity): managed exclusively by `PurchaseOrder`, its constructor is `internal`.
   - `ProductId` (Value Object): time-ordered identifier generated with UUIDv7 (`Guid.CreateVersion7()`).
   - `SupplierId` (Value Object): Procurement's own copy of the concept, deliberately decoupled from SupplyChain's (see [ADR-0002](docs/adrs.md#adr-0002-each-bounded-context-owns-its-own-reference-types)).
   - `Presentation.ConsoleFormatting` (`order.Summary`): console-only formatting kept out of the aggregate itself, via a C# 14 extension member (see [ADR-0011](docs/adrs.md#adr-0011-presentation-formatting-via-c-14-extension-members)).

   **`Acme.OOProgramming.Shared`** (Shared Kernel)
   - `Money` (Value Object): `decimal` amount + validated `Currency`, `readonly record struct` for value semantics and zero heap allocation.
   - `Currency` (Value Object): validated 3-letter ISO code, `readonly record struct` (see [ADR-0007](docs/adrs.md#adr-0007-currency-as-a-dedicated-value-object)).
   - `Address` (Value Object): international postal address, `readonly record struct`.
   - `Presentation.ConsoleFormatting` (`money.Display`): console-only formatting kept out of `Money` itself, via a C# 14 extension member (see [ADR-0011](docs/adrs.md#adr-0011-presentation-formatting-via-c-14-extension-members)).

   ### Key Domain Rules
   - **Aggregate invariant encapsulation**: `PurchaseOrder` strictly controls the creation and lifecycle of `PurchaseOrderItem`.
   - **Single-currency rule**: every item in a `PurchaseOrder` is priced in the order's own currency.
   - **Currency-safe arithmetic**: `Money` rejects cross-currency operations and negative amounts; its `+`/`*` operators call the same validated methods underneath.
   - **Duplicate line item handling**: `PurchaseOrder.AddItem` merges quantities when an existing `ProductId` is re-added at the same unit price; re-adding it at a different price throws instead of silently picking one (see [ADR-0009](docs/adrs.md#adr-0009-additem-rejects-a-duplicate-product-at-a-conflicting-unit-price)).
   - **Uniform value-type adoption**: `Money`, `Currency`, `Address`, `SupplierId`, and `ProductId` are all `readonly record struct`s, each `default`-guarded at every aggregate boundary that consumes one (see [ADR-0005](docs/adrs.md#adr-0005-uniform-readonly-record-struct-adoption-for-value-objects) and [ADR-0007](docs/adrs.md#adr-0007-currency-as-a-dedicated-value-object)).
   - **Cross-context references**: each bounded context owns its own copy of any identifier it references from another context, rather than sharing one type.
   - **Presentation decoupling**: display formatting (`order.Summary`, `money.Display`) lives in dedicated `*.Presentation` namespaces, never on the domain models themselves (see [ADR-0011](docs/adrs.md#adr-0011-presentation-formatting-via-c-14-extension-members)).

   ## Class Diagram
   See [`docs/class-diagram.puml`](docs/class-diagram.puml). Open it with a PlantUML plugin/viewer to render it.

   ## Prerequisites
   - .NET 10 SDK

   ## Build and Run
   ```bash
   dotnet build
   dotnet run --project Acme.OOProgramming
   dotnet test
   ```

   ## Docs
   - [`docs/user-stories.md`](docs/user-stories.md): acceptance criteria.
   - [`docs/adrs.md`](docs/adrs.md): architecture decision records.
   - [`CHANGELOG.md`](CHANGELOG.md): version history.

   ## License
   MIT, see [`LICENSE.md`](LICENSE.md).
   ````
   </details>

   ```
   git add .
   git commit -m "docs: add license and readme."
   git push
   ```
8. **Ship `1.1.0`,** the same way as the `## Release` section above. `develop` is now ahead of `main` again, carrying everything built since `1.0.0`: US006 (including its later revision), the `Money` / `Currency` refactor, `OrderDate` as a `DateOnly`, the new `Presentation` layer, all eleven ADRs, the traceability matrix, and the license/README.
   - `Release Start` → `1.1.0`
   - drop the `-preview` suffix in `Acme.OOProgramming.csproj` (`1.1.0-preview` → `1.1.0`), commit `chore(release): bump version to 1.1.0.`
   - add a `## 1.1.0` entry to `CHANGELOG.md`, above the existing `## 1.0.0` one, and commit it too
   - `Release Publish`, then `Release Finish`

   **Note:** a release branch still needs at least one commit of its own (the changelog entry), or the merge into `develop` is a no-op, same reasoning as the `## Release` section.

   <details>
   <summary>CHANGELOG.md (addition)</summary>

   ```markdown
   ## 1.1.0

   - US006: Merge Duplicate Items in a Purchase Order
   - Convert `Money` to a `readonly record struct` with `+`/`*` operators
   - Extract `Currency` into its own `readonly record struct` value object
   - Represent `PurchaseOrder.OrderDate` as a `DateOnly`
   - Add a `Presentation` layer via C# 14 extension members (`order.Summary`, `money.Display`)
   - Modernize guard clauses across value objects and aggregates
   - Add Architecture Decision Records (ADR-0001 through ADR-0011) to a single `docs/adrs.md`
   - Add requirements traceability matrix to `docs/user-stories.md`
   - Add `LICENSE.md` and `README.md`
   ```
   </details>

   Publish the GitHub Release the same way as `1.0.0`: **Releases** → **Draft a new release** → pick `1.1.0`, title `1.1.0`, description below, **Publish release** (or from the command line: `gh release create 1.1.0 --title "1.1.0" --notes-file <path to a file with the notes below>`).

   <details>
   <summary>Release notes (1.1.0)</summary>

   ```markdown
   ## 🚀 Added

   - **US006: Merge Duplicate Items in a Purchase Order**: adding a product already on the order now merges into the existing line instead of creating a duplicate, as long as the unit price matches; re-adding it at a different price is rejected instead of silently picking one (see ADR-0009, which supersedes the original ADR-0006 decision).
   - `Currency` value object, extracted out of `Money`/`PurchaseOrder`'s duplicated 3-letter validation logic (see ADR-0007).
   - `Presentation` layer (`order.Summary`, `money.Display`), via C# 14 extension members, keeping console formatting out of the domain models (see ADR-0011).

   ## 🔧 Changed

   - `Money` is now a `readonly record struct` (see ADR-0005): stack-allocated, with `+`/`*` operator overloads.
   - `PurchaseOrderItem.Quantity` gained a controlled, intention-revealing way to change: a private setter behind a new `IncreaseQuantity()` method, instead of `PurchaseOrder` rebuilding the entity from outside on every merge (see ADR-0008).
   - `PurchaseOrder.OrderDate` is now a `DateOnly` instead of a `DateTime`; a `DateTime`-accepting constructor overload is kept for compatibility (see ADR-0010).
   - Guard clauses across `Address`, `Money`, `Currency`, `SupplierId`, and `PurchaseOrder` modernized to .NET's argument-validation throw helpers.
   - Architecture Decision Records consolidated into a single `docs/adrs.md`, covering value objects, bounded-context ownership, aggregate encapsulation, `Money`'s representation, the struct conversion, the `Currency` extraction, the `PurchaseOrderItem` mutation method, the merge decision and its later revision, the `DateOnly` conversion, and the presentation layer (ADR-0001 through ADR-0011).

   ## 📝 Documentation

   - Added a Requirements Traceability Matrix to `docs/user-stories.md`, mapping each user story to its bounded context, aggregate, and implementation.
   - Added `LICENSE.md` and `README.md`.
   ```
   </details>
9. **Back on `develop`, pick the `-preview` suffix back up.** In `Acme.OOProgramming.csproj`: `<Version>1.1.0</Version>` → `<Version>1.1.1-preview</Version>`, commit `chore(dev): set development version to 1.1.1-preview.`, push.

   **Note:** `## Testing` below is optional, self-study only, so `develop` shouldn't sit on an already-tagged version while there's still unreleased optional work.

## Testing (optional, explore on your own)

Everything up to here (Features 1-5, US006, and the architecture/modernization work above) already shipped as real releases, `1.0.0` and `1.1.0`. This section doesn't gate any of that: it's optional. Testing well is a real skill, but it isn't this course's objective, and nothing later in this guide depends on finishing it.

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

   **Note:** `PurchaseOrderItem`'s constructor is `internal`: only code inside the `Acme.OOProgramming` assembly can call it directly, and a test project is a separate assembly. Making it `public` instead would let any caller construct one, defeating the whole point of Feature 3's encapsulation decision.

   <details>
   <summary>AssemblyInfo.cs (in Acme.OOProgramming, not Acme.OOProgramming.Tests)</summary>

   ```csharp
   using System.Runtime.CompilerServices;

   [assembly: InternalsVisibleTo("Acme.OOProgramming.Tests")]
   ```
   </details>

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
6. **Add the `Test Suite` column to the Requirements Traceability Matrix.** Go back to the matrix in `## Document the Project` above and add it now that real tests exist: one cell per row, linking to the test class (or specific method) that verifies that user story.

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
   | **US006** | Merge Duplicate Items in a Purchase Order | Procurement Context | [`PurchaseOrder`](../Acme.OOProgramming/Procurement/Domain/Model/Aggregates/PurchaseOrder.cs), [`PurchaseOrderItem`](../Acme.OOProgramming/Procurement/Domain/Model/Aggregates/PurchaseOrderItem.cs) | `AddItem(productId, quantity, unitPriceAmount)` (merge branch) | [`PurchaseOrderTests`](../Acme.OOProgramming.Tests/Procurement/Domain/Model/Aggregates/PurchaseOrderTests.cs): `AddItem_WithDuplicateProductAndMatchingPrice_MergesQuantity`, `AddItem_WithDuplicateProductAndConflictingPrice_ThrowsInvalidOperationException`, `AddItem_WithDifferentProduct_CreatesSeparateLine` |
   ```
   </details>

   ```
   git add .
   git commit -m "docs(user-stories): add test suite column to the traceability matrix."
   git push
   ```
7. **Ship `1.1.1`,** one more time through the release cycle. `develop` is ahead of `main` again, and there's no more work planned after this.
   - `Release Start` → `1.1.1`
   - drop the `-preview` suffix in `Acme.OOProgramming.csproj` (`1.1.1-preview` → `1.1.1`), commit `chore(release): bump version to 1.1.1.`
   - add a `## 1.1.1` entry to `CHANGELOG.md` above the existing `## 1.1.0` one, and commit it too
   - `Release Publish`, then `Release Finish`

   **Note:** `1.1.1` is another patch bump, still no new capability, just tests and documentation.

   <details>
   <summary>CHANGELOG.md (addition)</summary>

   ```markdown
   ## 1.1.1

   - Add unit test suite covering US001 through US006 and the presentation layer
   - Complete the requirements traceability matrix with test coverage
   ```
   </details>

   Publish the GitHub Release the same way as every release so far: **Releases** → **Draft a new release** → pick `1.1.1`, title `1.1.1`, description below, **Publish release**.

   <details>
   <summary>Release notes (1.1.1)</summary>

   ```markdown
   ## 🚀 Added

   - Unit test suite (xUnit + FluentAssertions) covering US001 through US006 and the `Presentation` layer, one test class per domain class.

   ## ✅ Verified

   - All tests pass against the full domain model (`dotnet test`).
   ```
   </details>
8. **From here, it's on you.** Add a test for a scenario not covered yet, break a validation rule on purpose and confirm the test catches it, or look up something in the xUnit or FluentAssertions docs this suite doesn't use yet.

## Appendix

Reference notes for situations that come up now and then. Skip past this on a normal run and come back when you hit one of them.

### Continuing on another computer

Once you've pushed your work it's on GitHub, so you can carry on from any machine.

1. Sign in and clone:
   ```
   gh auth login
   gh repo clone <org>/oop-sample
   cd oop-sample
   ```
2. If you were partway through a feature, switch to its branch and pull the latest:
   ```
   git checkout feature/<name>
   git pull
   ```
3. Open the folder in Rider (`File` → `Open`, pick `oop-sample`). It reads the solution from the `.sln` on its own.
4. Reinstall the plugins. They belong to the IDE, not the repo, so a fresh machine won't have them: the `plantuml4idea` plugin (Project Setup step 4) and Git Flow Helper (Project Setup step 8).
5. Run Git Flow `Init` again from the widget (Project Setup step 9). The Git Flow settings live in the repo's local git config, which a clone doesn't copy. `Init` sees that `main` and `develop` already exist, so it doesn't recreate anything; it just registers the branch names on this machine. Accept the defaults. When Rider prompts to log in, run `gh auth token`, choose `Log In with Token`, and paste; if no prompt appears, add the account first from `Settings` → `Version Control` → `GitHub` → `+` → `Log In with Token...`.

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

### If the class diagram doesn't render

The `plantuml4idea` plugin needs Graphviz for some diagrams. If `docs/class-diagram.puml` shows an error instead of a rendered diagram:
- macOS: `brew install graphviz`, then restart the IDE
- Windows: install it from [graphviz.org](https://graphviz.org/download/) and restart

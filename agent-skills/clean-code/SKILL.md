---
name: clean-code
description: Apply clean-code principles when writing, reviewing, or refactoring code to improve readability, maintainability, testability, and extensibility. Use for code-smell reviews, naming and function design, duplication removal, class responsibilities, and behavior-preserving cleanup across projects and languages.
---

# Clean Code

## Purpose and mindset

Treat clean code as a mindset, not merely a checklist. Write from the client's and caller's perspective, making intentions clear to the people who read and maintain the code. Machines execute code; humans maintain it.

The source expresses this through two principles:

- “Clean Code is not a checklist, it's a mindset.” 
- “Any fool can write code that a computer can understand. Good programmers write code that humans can understand.” — Martin Fowler

Favor code that is easy to understand, maintain, debug, and extend. Recognize code smells even when code executes correctly: unclear code invites confusion and bugs.

## Applying this skill across projects

1. Read the relevant code, callers, project conventions, and existing tests before proposing changes.
2. Apply the guidelines below to the requested work, using the project's language, framework, formatter, and testing tools. TypeScript conventions below illustrate the principles; use equivalent idioms in other languages.
3. Preserve existing behavior during refactoring. Use characterization tests to capture unclear behavior before changing its structure, and distinguish intentional behavior changes from cleanup.
4. Make focused improvements to the code being touched, following the Boy Scout Rule. Keep changes within the requested scope.
5. Run relevant checks and tests, and explain meaningful changes and any unverified behavior.


Treat reference snippets as illustrations of intent. Verify syntax, identifiers, calculations, and behavior before adapting them: some snippets contain mistakes or alter behavior. For example, characterization tests must describe existing behavior; adding rejection of non-positive rectangle dimensions is a separate behavior change if the original implementation simply multiplies its inputs.

## Names that explain intent

- Use descriptive, meaningful variable and function names that communicate their purpose without extra explanation.
- Avoid unclear or generic names such as `temp`, `i`, `j`,`foo`, `bar`, and `data` when a more specific name expresses the role.
- Make function names accurately describe their action. Use present tense rather than past tense.
- Prefer clarity and descriptiveness over brevity, including for simple tasks. For example, prefer `calculateSquare(inputNumber)` over `sqr(n)` and `calculateTotalPrice(price)` over `cost(x)`.
- Use explaining variables and explaining functions to reveal what values and operations represent. Clear names reduce confusion and make later changes easier.

## Meaningful constants and configuration

- Replace unexplained hard-coded numbers with named constants that express their meaning, such as `PI`, a discount threshold, or a tax rate.
- Replace magic strings, such as repeated status values, with named constants or enums.
- Avoid hard-coded configurable numbers, strings, file paths, and connection details that reduce flexibility.
- Centralize repeated values and data in constants, configuration files, or dedicated modules.
- Store sensitive or environment-specific configuration, such as database connection details, in environment variables. Use the project's configuration mechanism; `dotenv` is the source's TypeScript example.

## Comments, casing, and formatting

- Refactor code to explain itself rather than relying on extensive comments describing what it does.
- Keep comments when necessary to explain why something is done. Remove redundant comments after making names and structure clear.
- Keep letter case conventions consistent throughout the codebase. The source's TypeScript conventions are `camelCase` for variables and functions, `PascalCase` for classes, and `UPPER_SNAKE_CASE` for constants.
- Format code consistently so readers can identify patterns, follow logic, and debug, maintain, and update it easily.

## Expressions and control flow

### Split God Lines

- Treat a very long, complex line combining multiple operations as a God Line code smell.
- Split it into small, meaningful steps with well-named intermediate variables and extracted functions.
- Separate concepts such as base price, discount, and shipping so each can be understood and debugged independently.

### Flatten nested conditionals

- Treat deeply nested conditionals as a readability and maintenance smell: they hide the main purpose behind an arrow-shaped structure.
- Handle failure conditions at the start of the function using guard clauses and early returns, then express the main path without unnecessary nesting.
- Preserve the original condition semantics and ordering while flattening branches.

## Function design

### Accurate names

Check that a function's name communicates what it actually does. Rename misleading functions using present-tense action names.

### Short parameter lists

- Treat more than two parameters as a long-signature code smell, as specified by the source. Inspect whether the function does too much or lacks a suitable abstraction.
- When more than two related values are needed, encapsulate them in a class and pass an instance to the function. Use the corresponding structured parameter idiom where appropriate in the project's language.
- Keep the properties and behavior together when they belong to one concept, such as a `Product` or `User`, instead of requiring callers to manage long, error-prone argument lists.

### One task per function

- A function should do one task. Long functions that mix responsibilities are difficult to understand, test, and maintain.
- Extract distinct tasks into smaller functions, each with one responsibility, and call them from the original coordinating function.
- Make extracted logic reusable wherever it is needed.
- For example, separate input validation, total calculation, average calculation, and output; separate order validation, inventory checks, charging, shipping, and confirmation.

## Class organization and responsibility

### Group methods by visibility

Keep private methods together and public methods together rather than mixing them. Make it easy to find a method by its visibility.

### Remove feature envy

- Recognize feature envy when a method frequently accesses or manipulates another class's data or behavior to do its work.
- Such behavior weakens encapsulation, increases coupling, and violates tell-don't-ask.
- Extract and move the method to the class that owns the relevant data or functionality.
- Alternatively, introduce a class encapsulating the data and functionality used together.

### Tell, don't ask

- Bundle data with the functions operating on it. Tell an object what to do rather than retrieving its internal data and operating on it elsewhere.
- Make objects responsible for their own data and behavior.
- For example, let `Product.cost()` calculate price times quantity, and let `Orders` aggregate product costs instead of fetching product internals to calculate each cost itself.

## Duplication and change locality

### DRY: Don't Repeat Yourself

- Reduce repetition through abstraction rather than reimplementing the same logic in multiple places.
- Repeated code increases codebase size without adding functionality, harms readability, makes errors harder to locate, and forces changes in multiple places.
- Fixes applied to only some copies lead to inconsistent behavior and bugs.
- Centralize shared logic in reusable functions, methods, components, or modules so one change updates all callers uniformly.
- Isolate logic to make it easier to test, maintain, and debug. Fix a shared error in one place.
- For example, extract a shared tax calculation or per-product discounted cost instead of duplicating arithmetic for each caller or cart entry.

### Internal duplication

- Look for repeated logic, code, and data within a codebase, including repetition hidden inside larger structures or recurring operations.
- Extract repeated patterns into helper functions, methods, or utilities. For repeated loop operations, put shared logic in a function and call it from the loop.
- Centralize hard-coded values and repeated data in constants and configuration modules.
- Use design patterns such as Singleton, Factory, or Strategy when they address repetitive instantiation or logic.
- Divide code into small, reusable modules or components.
- Use automated tests to help expose repeated behavior and verify that centralizing logic does not break functionality elsewhere.
- Aim for maintainability, readability, consistency, fewer bugs, and modular reuse through DRY.

### Avoid shotgun surgery

- Recognize shotgun surgery when one logical change requires edits across multiple classes or locations. It often overlaps with duplicated code and causes changes in one place to break another.
- Distinguish it from divergent change, where many different changes affect a single class.
- Encapsulate shared rules in one place to reduce scattered edits, effort, and inconsistent updates.
- For example, extract a membership-inactive balance check shared by borrowing, reserving, and paying late fees into a single predicate, while preserving operation-specific notifications.

## Extensible design

- Make it easy to add features without editing existing core code other than configuration, while keeping the system simple, readable, testable, and maintainable.
- Identify hard-coded type-dependent rules that force changes to a central class whenever a type or rule changes. These increase complexity and duplication and reduce readability as features grow.
- Separate varying behavior into focused strategies behind a common interface. Use a factory or configuration to choose the strategy.
- In the source's discount example, keep product management in `ShoppingCart`, discount calculations in individual strategies, and strategy selection in a factory.
- Add a product type by adding a strategy and updating selection configuration or the factory, leaving the cart's core logic unchanged.
- Change a type's discount rule in its own strategy rather than in the shopping cart.

## Pure functions and explicit state

- Recognize impure functions: they modify external state, interact with external systems, or rely on external state that affects their behavior.
- Such functions can have hidden dependencies, unpredictable behavior, and difficult isolation and testing.
- Prefer pure functions for calculations and transformations: the same inputs always produce the same output, without relying on or modifying external state and without side effects.
- Refactor hidden state into explicit inputs and return the new value instead of modifying globals or caller-owned data.
- For example, use `add(newValue, total)` instead of mutating a global total, and `increment(counter)` instead of mutating a global counter.
- Return a new sequence, such as `numbers.slice(1)`, instead of removing an item from an externally owned array.
- Use purity to improve testability, predictability, and composition of more complex behavior.

## Compile-time safety

- Use the compiler and type system to catch errors before runtime where the language supports it.
- Declare meaningful input and output types so invalid arguments are rejected, such as passing a string to a numeric maximum function.
- Use generics to preserve relationships between types and move suitable checks from runtime to compile time.
- For example, `Container<string>` should accept strings and reject numbers when updating its stored value.

## Testing support for clean code

### Characterization tests

- Write automated tests documenting what existing code currently does, especially when its implementation is hard to understand.
- Capture behavior before refactoring. The primary goal is to preserve and explain current behavior, not introduce a new one.
- Run the tests after changes to detect regressions and newly introduced errors.

### Fixtures

- Use predefined, structured, reusable, prepared datasets for consistent unit, integration, and end-to-end tests.
- Make results easy to compare across runs and avoid recreating the same data for each test.
- Reuse fixture files across tests and centralize data instead of duplicating setup.
- Keep test files focused on testing logic. Shared fixtures save time and reduce typos and mismatched data.
- Include relevant valid and invalid cases, such as valid and invalid registration data.

## Boy Scout Rule

Leave the codebase cleaner than you found it whenever you touch a piece of code, even through small improvements. Look for duplicated code, lengthy methods, long parameter lists, hard-coded values, and excessive comments. Apply the relevant techniques above to improve readability and maintainability within the work's scope.

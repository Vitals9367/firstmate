---
name: naming-scan-fix
description: Scan a codebase for unclear names of variables, constants, parameters, functions, methods, classes, types, and files, and rename them to descriptive Clean Code names that say exactly what they hold or do. Use when the user asks for a naming scan, naming review, naming cleanup, or to fix or improve names in a codebase, directory, or file. Supports scan-only (report) and fix (rename) modes.
---

# Naming scan and fix

Find unclear names and replace them with descriptive names.
The name must tell the reader exactly what the thing holds or does, without reading its body.

## Inputs

- **Scope:** a path, a glob, the files changed on the current branch, or the whole repository.
  If the user gives no scope, use the whole repository, without vendored, generated, and build output directories.
- **Mode:** `scan` writes a report and changes nothing.
  `fix` renames.
  If the user does not say, use `fix`.

## Naming rules

1. **Descriptive.** A name says exactly what the value holds or what the code does: `userEmailAddresses`, not `list`, and `calculateMonthlyInvoiceTotal`, not `calc`.
2. **Functions and methods start with a verb** that says the exact action: `fetchActiveSubscriptions`, `parseInvoiceCsv`, `sendPasswordResetEmail`.
   Do not use vague verbs (`handle`, `process`, `do`, `manage`, `run`) unless the name also says what is processed and how.
3. **Booleans read as a true or false question:** `isExpired`, `hasUnpaidInvoices`, `canEditDocument`, `shouldRetryRequest`.
4. **Classes and types are nouns** that name one clear responsibility: `InvoicePdfRenderer`, not `InvoiceManager` or `Helper`.
5. **Files are named for their content.** A file that holds one main class or function uses that name in the project's file-name style.
   Do not use `utils`, `helpers`, `misc`, `common`, or `stuff` files when a more exact name is possible.
6. **No unclear abbreviations:** `customerAddress`, not `custAddr`.
   Keep only abbreviations that are more known than the full word (`id`, `url`, `html`, `api`, `csv`).
7. **No generic names:** `data`, `info`, `item`, `obj`, `temp`, `tmp`, `val`, `value`, `result`, `res`, `ret`, `thing`, `stuff`, `foo`, `x`, `arr`, `list`, `str`, `num`, `flag`.
   Single letters are permitted only for short loop indexes and for math formulas where the domain uses that letter.
8. **Collections are plural** and say what they hold: `pendingOrders`.
   Maps say key and value: `ordersByCustomerId`.
9. **Units are in the name** when the type does not carry them: `timeoutSeconds`, `fileSizeBytes`, `priceCents`.
10. **No misleading names.** A name must not say something the code does not do: `getUser` that also creates a user is wrong.
11. **One function, one thing.** If an exact name for a function needs "and", "or", or "then", the function does more than one thing.
    Do not hide this with a vague name.
    Report it as a split candidate.
12. **Keep the language and project conventions** for case style (camelCase, snake_case, PascalCase, kebab-case files) and for framework-required names (Laravel conventions, React hooks `useX`, test framework names, magic methods, ORM conventions).

## Do not rename without approval

Report these as "needs approval", and do not change them, unless the user said they may change:

- Public or exported API that code outside the scope uses: published package exports, HTTP routes, CLI flags.
- Names that are stored or sent outside the code: database tables and columns, migrations, serialized or JSON keys, API request and response fields, environment variables, config keys, queue and event names, cache keys.
- Names that a framework or tool finds by convention or reflection, when the rename would break that lookup.
- Generated code, vendored code, and lockfiles.

## Procedure

1. **Learn the conventions.** Read the project's `AGENTS.md`, `CLAUDE.md`, `CONTRIBUTING.md`, linter config, and some typical files.
   Note the case style and framework conventions for each language.
2. **Scan.** Go through the scope file by file.
   For each unclear name, record: file and line, kind (variable, function, class, file, and so on), current name, proposed name, the rule it breaks, and a risk class (`safe`, `needs approval`, `split candidate`).
3. **Report.** Show the findings as a table grouped by file.
   In `scan` mode, stop here.
4. **Rename.** In `fix` mode, rename only `safe` findings:
   - Use a semantic rename tool when one is available (language server rename, IDE refactor, `ast-grep`, `rope`, `ts-morph`).
     Otherwise, find every reference with a word-boundary search and update each one.
   - Update all references: imports, exports, string references in templates and routes inside the scope, docs, and type declarations.
   - Rename a file with `git mv` and update every import path.
   - Do one rename at a time.
     Do not change behavior, formatting, or logic.
5. **Verify.** Run the project's type check, build, linter, and existing test suite.
   If one fails because of a rename, fix the reference or revert that rename.
6. **Deliver.** Keep each PR at about 500 changed lines.
   Split a large rename into a stack of PRs, for example one per module or directory.

## Output rules

- Do not add comments to the code.
- Do not add tests.
- Write the report, commit messages, and PR descriptions in short, simple sentences (ASD-STE100 style): active voice, one meaning per word.
- In the final summary, list: renames done, renames that need approval, split candidates, and the verification results.

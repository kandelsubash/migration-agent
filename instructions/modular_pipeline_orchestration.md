# Modular Migration Pipeline Orchestration Guide

> Master workflow and orchestration rules coordinating Agent 1, Agent 2, Agent 3, and Agent 4.
> **Key Architecture Decision:** Backing `.java` files are eliminated. All operations identified in `.xhtml` files are mapped directly to exact API endpoints, HTTP methods, request payloads, and response payloads provided by the user in Agent 3.

---

## 1. Pipeline Execution Flow

```
+-----------------------------------------------------------------------------------+
| STEP 1: INITIAL DISCOVERY (Run Once)                                             |
| Agent 1 analyzes legacy navigation header -> outputs module_header_mapping.json   |
| (Maps Module -> Submodule -> Route -> .xhtml view files. No .java required)       |
+-----------------------------------------------------------------------------------+
                                         |
                                         v
+-----------------------------------------------------------------------------------+
| STEP 2: MODULE-BY-MODULE LOOP                                                     |
|                                                                                   |
| 2A. Scope Selection (Agent 2):                                                    |
|     - Prompts user for Module & Submodule                                         |
|     - Queries module_header_mapping.json                                          |
|     - Emits scope handoff payload (Module, Submodule, Route, .xhtml views)        |
|                                                                                   |
| 2B. Behavioral & API Extraction (Agent 3):                                        |
|     - Prompts user for .xhtml file path(s) & read permissions                     |
|     - Identifies all operations (tables, forms, buttons, AJAX triggers)           |
|     - Asks user for exact API URL, HTTP Method, Request & Response Payloads       |
|     - Appends/merges into submodule_behavior_specs.json (Never overwrites)        |
|                                                                                   |
| 2C. Code Generation & Parity Validation (Agent 4):                                |
|     - Reads submodule_behavior_specs.json                                         |
|     - Locates last completion flag; processes next unmigrated submodule           |
|     - Synthesizes TypeScript interfaces from request & response payloads          |
|     - Generates Angular services calling exact user-provided API endpoints        |
|     - Generates Angular 22 standalone components, OnPush, Signals, Jasmine specs  |
|     - Uses @shared/components/data-table & dynamic-form with dynamic CSS          |
|     - Preserves legacy routes & visual styling                                    |
|     - Appends [MIGRATION_COMPLETED: <Module> -> <Submodule>] checkpoint flag      |
+-----------------------------------------------------------------------------------+
                                         |
                                         v
+-----------------------------------------------------------------------------------+
| STEP 3: NEXT ITERATION                                                            |
| Repeat Step 2 for next submodule or module until application migration is complete.|
+-----------------------------------------------------------------------------------+
```

---

## 2. Checkpoint & Flag Resumption Protocol

Agent 4 relies on state flags to maintain idempotency across modular migration runs:

### Flag Syntax
```markdown
<!-- [MIGRATION_COMPLETED: <ModuleName> -> <SubmoduleName> | TIMESTAMP: <ISO_8601>] -->
```

### Resumption Decision Matrix
1. **No flag present in file:** Start code generation from the very first submodule in `submodule_behavior_specs.json`.
2. **Flags present:** Scan the file from top to bottom. Find the last completed submodule. Resume code generation with the next submodule that lacks a completion flag.
3. **All submodules flagged:** Report migration fully up to date for all parsed modules.

---

## 3. Data Integrity & Non-Destructive Merging
- Agent 3 must **never** truncate or overwrite `submodule_behavior_specs.json`.
- When a new submodule is processed, it must be nested under its existing parent `moduleName` or appended as a new key if the module is encountered for the first time.
- All extracted legacy CSS classes, route outcomes, validator constraints, and user-provided API contracts must be preserved verbatim.

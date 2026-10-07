# Running the Modular Migration Agents with Claude 3.5 Sonnet (API-First Pipeline)

This guide explains how to execute the 4-agent legacy migration pipeline using **Claude 3.5 Sonnet (Paid)** to migrate legacy JSF/RichFaces applications to **Angular 22.x** and **PrimeNG 22.x** with minimal token consumption.

---

## Key Architecture Improvement: Zero Java Dependency
> **Why we don't need `.java` files:**
> In the modern architecture, frontend and backend communicate via REST APIs. Trying to parse legacy Java beans leads to hallucinated logic and excessive token waste. Instead, the pipeline parses only the UI view (`.xhtml`). For every operation found in the `.xhtml` file (table load, form submit, button click, row action, dynamic dropdown), **Agent 3 interactively asks you for the exact API URL, HTTP Method, Request Payload, and Response Payload**. Agent 4 then generates type-safe TypeScript interfaces and Angular services directly matching your real API!

---

## Workspace & File Structure

All agent definitions, skills, and orchestrations are located in:
```
C:\Users\dell\Desktop\Legacy-to-modern\agent-updated\
├── agents/
│   ├── agent_1_header_mapper.md      # Header & navigation route extractor (views only)
│   ├── agent_2_feature_extractor.md  # Scope selector & handoff coordinator
│   ├── agent_3_behavior_parser.md    # .xhtml parser & interactive API contract collector
│   └── agent_4_coder_migrator.md     # Angular 22 & PrimeNG 22 coder + Jasmine specs
├── skills/
│   ├── richfaces_to_primeng_matrix.md   # Tag, AJAX, and attribute mapping matrix
│   ├── shared_components_contract.md    # @shared data-table and dynamic-form API
│   └── security_and_coding_rules.md     # 14 security rules & Signals/OnPush standards
├── instructions/
│   └── modular_pipeline_orchestration.md # Master workflow and flag resumption logic
└── CLAUDE_SONNET_EXECUTION_GUIDE.md     # This execution guide
```

---

## 3 Core Non-Negotiable Rules

1. **Route & Navigation Parity:** Every legacy route, URL path, outcome, or menu structure must be preserved in Angular feature routes (`<module>.routes.ts`) and `app.routes.ts`.
2. **Zero Feature Exclusion (100% Parity):** Every legacy button, dialog, form field, validator, table column, action, and AJAX behavior must be migrated. **Never omit any feature** unless you explicitly instruct the agent to exclude it.
3. **Behavioral & Visual Parity:** Migrated screens must match legacy layout alignments, field spacing, color palettes, dialog triggers, and async operations.

---

## Recommended Claude Setup (Projects / Chat)

To achieve **minimal token consumption** with Claude 3.5 Sonnet:
- If using **Claude Projects**, add the 3 skill files in `agent-updated/skills/` as project knowledge files once. They will be cached via prompt caching.
- If using standard **Claude Chat**, pass only the specific agent markdown file and relevant skill file when invoking that agent.

---

## Step-by-Step Execution Workflow

### STEP 1: Run Agent 1 (Header Analysis — Run Once Per Project)

Agent 1 analyzes the legacy header/menu structure and produces `module_header_mapping.json`.

1. **Start a new Claude session** and paste the content of:
   `C:\Users\dell\Desktop\Legacy-to-modern\agent-updated\agents\agent_1_header_mapper.md`
2. **Claude will ask for the header file path.**
   - Example reply:
     ```
     C:\Users\dell\Desktop\LegacyApp\src\main\webapp\templates\header.xhtml
     ```
   *(Attach or paste the file contents).*
3. **Claude will parse the navigation tree and ask for the save location.**
   - Example reply:
     ```
     C:\Users\dell\Desktop\Legacy-to-modern\agent-updated\module_header_mapping.json
     ```
4. **Result:** Agent 1 generates and saves `module_header_mapping.json` mapping all Modules, Submodules, canonical Routes, and linked `.xhtml` view files.

---

### STEP 2: Run Agent 2 (Feature Scope Extraction)

Agent 2 selects the submodule to migrate and creates the handoff envelope for Agent 3.

1. **In Claude**, paste the content of:
   `C:\Users\dell\Desktop\Legacy-to-modern\agent-updated\agents\agent_2_feature_extractor.md`
2. **Claude will prompt:**
   `"1. Which Module name do you want to migrate? 2. Which Submodule of this module should be converted?"`
3. **Your input:**
   ```
   1. Module: FLMS
   2. Submodule: A
   ```
4. **Attach or provide path to:** `module_header_mapping.json` (from Step 1).
5. **Result:** Agent 2 outputs the handoff payload:
   ```json
   {
     "moduleName": "FLMS",
     "submoduleName": "A",
     "route": "/flms/a",
     "views": ["a.xhtml"],
     "innerModules": []
   }
   ```

---

### STEP 3: Run Agent 3 (Exhaustive 6-Dimensional Replica Blueprint & API Collection)

Agent 3 inspects the targeted `.xhtml` file, builds an **exact 6-dimensional UI & behavioral blueprint** (DOM hierarchy, form controls, table specifications, dialogs, visual CSS styles, and event triggers), and asks you for the exact API details.

1. **In Claude**, paste the content of:
   - `C:\Users\dell\Desktop\Legacy-to-modern\agent-updated\agents\agent_3_behavior_parser.md`
   - Include `skills/richfaces_to_primeng_matrix.md` for reference.
2. **Feed Agent 2's handoff payload to Claude.**
3. **Claude will request the `.xhtml` file path and read permission:**
   `"Please provide the absolute file path(s) to access these .xhtml file(s), and grant read permission."`
4. **Your input:**
   ```
   Granted read permission. File:
   - C:\Users\dell\Desktop\LegacyApp\src\main\webapp\views\flms\a.xhtml
   ```
   *(Attach or paste `a.xhtml`).*
5. **Claude parses `a.xhtml`, constructs the 6-dimensional UI blueprint, and prompts you for API details for each operation found:**
   ```text
   Extracted Replica Blueprint for a.xhtml:
   - Layout: Two-column card layout, containerClass: 'flms-page-container'
   - Form: 'applicantForm' with fields [applicantName, applicationDate]
   - Table: 'applicantTable' with columns [id, applicantName, applicationDate, status]
   - Dialog: 'editModal' for editing applicant details
   - Custom CSS: captured legacy styles, alignments, and widths

   Please provide the API URL, HTTP Method, Request Payload (if any), and Response Payload for each operation:
   1. Initial table data load (rich:dataTable value="#{applicantBean.applicants}")
   2. Form submission (a4j:commandButton action="#{applicantBean.saveApplicant}")
   3. Row delete button (a4j:commandButton action="#{applicantBean.delete(row.id)}")
   ```
6. **Your input (provide exact API specs):**
   ```text
   For 1 (Table Load):
   - URL: /api/v1/flms/applicants
   - Method: GET
   - Request Payload: null
   - Response Payload:
   {
     "success": true,
     "data": [
       { "id": 1, "applicantName": "Alice", "applicationDate": "2026-05-10", "status": "APPROVED" }
     ],
     "message": "Success"
   }

   For 2 (Form Submit):
   - URL: /api/v1/flms/applicants
   - Method: POST
   - Request Payload: { "applicantName": "Alice", "applicationDate": "2026-05-10" }
   - Response Payload:
   {
     "success": true,
     "data": { "id": 2, "applicantName": "Alice", "applicationDate": "2026-05-10", "status": "PENDING" },
     "message": "Created"
   }

   For 3 (Delete):
   - URL: /api/v1/flms/applicants/{id}
   - Method: DELETE
   - Request Payload: null
   - Response Payload: { "success": true, "data": null, "message": "Deleted" }
   ```
7. **Claude asks for output location:**
   - Your reply:
     ```
     C:\Users\dell\Desktop\Legacy-to-modern\agent-updated\submodule_behavior_specs.json
     ```
8. **Result:** Agent 3 saves the complete `replicaBlueprint` along with your exact API contracts non-destructively into `submodule_behavior_specs.json`.

---

### STEP 4: Run Agent 4 (Exact Replica Code Generation & Checkpoint Flag)

Agent 4 consumes the `replicaBlueprint` and synthesizes an **exact visual and behavioral replica** of the legacy screen in modern Angular 22 & PrimeNG 22.

1. **In Claude**, paste the content of:
   - `C:\Users\dell\Desktop\Legacy-to-modern\agent-updated\agents\agent_4_coder_migrator.md`
   - Attach references:
     - `skills/shared_components_contract.md`
     - `skills/richfaces_to_primeng_matrix.md`
     - `skills/security_and_coding_rules.md`
2. **Provide the behavior specs file:**
   - Attach or supply `submodule_behavior_specs.json`.
3. **Claude checks the checkpoint flag:**
   - If no flag exists, starts from the first submodule.
   - If flags like `<!-- [MIGRATION_COMPLETED: ...] -->` exist, starts immediately after the last flagged submodule.
4. **Claude generates the exact replica files:**
   - `models/<entity>.model.ts` (Strict TypeScript interfaces generated from your request & response payloads!)
   - `services/<entity>.service.ts` & `.spec.ts` (Injects `ApiService`, calls your exact URLs)
   - `<submodule>.component.ts` (Standalone, OnPush, Signals state, dialog triggers, confirmation dialogs)
   - `<submodule>.component.html` (Exact DOM structure utilizing `@shared/components/data-table` and `@shared/components/dynamic-form` with dynamic CSS properties)
   - `<submodule>.component.scss` (Includes all custom styles and classes to guarantee pixel-perfect legacy visual match)
   - `<submodule>.component.spec.ts` (Full Jasmine unit tests asserting DOM rendering, signal changes, and user actions)
   - `<module>.routes.ts` (Preserving the exact legacy route)
5. **Claude appends the checkpoint flag to `submodule_behavior_specs.json`:**
   ```markdown
   <!-- [MIGRATION_COMPLETED: FLMS -> A | TIMESTAMP: 2026-10-07T00:30:00Z] -->
   ```

---

## Resuming Iterations (Submodule B, C, etc.)

When migrating subsequent submodules (e.g. `FLMS -> B`):
1. **Run Agent 2:** Select `Module: FLMS`, `Submodule: B`.
2. **Run Agent 3:** Provide path to `b.xhtml` and provide API details for operations in `b.xhtml`. Agent 3 appends `B` under `FLMS` in `submodule_behavior_specs.json` without modifying `A`.
3. **Run Agent 4:** Feed `submodule_behavior_specs.json`. Agent 4 sees that `A` is marked completed, automatically skips `A`, generates code for `B`, and appends `[MIGRATION_COMPLETED: FLMS -> B]`.

---

## Token Optimization Summary

- **Eliminating Java files saves 60-80% of context tokens** previously wasted on parsing legacy enterprise Java controllers and DAOs.
- **Prompt Caching:** Place the 3 skill files in Claude Project Knowledge so they are cached automatically.
- **Precise API contracts** eliminate guesswork and repeated re-generation cycles.

# Agent 2: Feature Extraction Agent

## Role
Scope resolution and pipeline coordinator. Bridges user module selection with Agent 1's mapping data and hands off target `.xhtml` views to Agent 3.

---

## Non-Negotiable Rules
1. **Interactive Scoping:** Prompt the user to select the specific Module and Submodule.
2. **Lookup Integrity:** Read `module_header_mapping.json` (created by Agent 1) to accurately extract all associated `.xhtml` view files without omission.
3. **No .java Files:** Do not search for or request `.java` classes. Backend operations will be defined via user-provided API contracts in Agent 3.
4. **Route Preservation:** Ensure the legacy route path is carried forward in the handoff.
5. **Handoff Contract:** Emit the exact JSON handoff envelope expected by Agent 3.

---

## Step-by-Step Execution Protocol

### Step 1: User Input — Scope Selection
Prompt the user:
```
"1. Which Module name do you want to migrate? (e.g. FLMS)
 2. Which Submodule of this module should be converted? (e.g. A)"
```
*Wait for user response.*

### Step 2: Query Mapping File
1. Load `module_header_mapping.json` from the output location defined in Agent 1.
2. Search for the requested `moduleName` and `submoduleName`.
3. If not found, list available modules and submodules from the JSON file and prompt the user to re-enter.
4. Retrieve:
   - `moduleName`
   - `submoduleName`
   - `route`
   - All `views` (`.xhtml` files)
   - Any nested `innerModules`

### Step 3: Present Extraction & Handoff
Display the discovered views to the user:
```markdown
### Target Migration Scope
- **Module:** {moduleName}
- **Submodule:** {submoduleName}
- **Canonical Route:** {route}
- **Associated Views (.xhtml):**
  - `a.xhtml`
```

Pass the standardized handoff JSON payload to Agent 3:
```json
{
  "moduleName": "FLMS",
  "submoduleName": "A",
  "route": "/flms/a",
  "views": ["a.xhtml"],
  "innerModules": []
}
```
Prompt the user to proceed to Agent 3.

# Shared Components Contract & Usage Guide

> Single source of truth for `@shared/components/data-table` and `@shared/components/dynamic-form`.
> **Rule:** Any tabular presentation or form in migrated code MUST utilize these components rather than duplicating PrimeNG table/form templates.

---

## 1. AppDataTableComponent (`@shared/components/data-table`)

### Component Selector
`<app-data-table>`

### TypeScript Interface & Inputs
```typescript
import { AppDataTableComponent, TableColumn } from '@shared/components/data-table/data-table.component';

export interface TableColumn {
  field: string;               // Key in data object
  header: string;              // Column header title
  sortable?: boolean;          // Default false
  width?: string;              // Optional CSS width (e.g. '150px', '20%')
  columnStyleClass?: string;   // Optional column-specific CSS class
}
```

| Input Property | Type | Default | Description |
| :--- | :--- | :--- | :--- |
| `columns` | `TableColumn[]` | **Required** | Column definitions array. |
| `data` | `T[]` | `[]` | Row items array. |
| `loading` | `boolean` | `false` | Spinner display state. |
| `paginator` | `boolean` | `true` | Enables pagination bar. |
| `rows` | `number` | `10` | Rows per page. |
| `emptyMessage` | `string` | `'No records found.'` | Message when data is empty. |
| `containerClass` | `string` | `''` | CSS class for outer container (margin, layout). |
| `styleClass` | `string` | `'p-datatable-striped'` | PrimeNG table style class for visual parity. |
| `tableStyle` | `Record<string, string>` | `{ 'min-width': '50rem' }` | Style object passed to `<p-table>`. |

### Example Usage in Feature Component
```html
<app-data-table
  [columns]="columns"
  [data]="items()"
  [loading]="loading()"
  [paginator]="true"
  [rows]="15"
  containerClass="flms-table-wrapper"
  styleClass="p-datatable-sm p-datatable-gridlines"
  [tableStyle]="{ 'min-width': '60rem', 'width': '100%' }"
/>
```

---

## 2. AppDynamicFormComponent (`@shared/components/dynamic-form`)

### Component Selector
`<app-dynamic-form>`

### TypeScript Interface & Inputs
```typescript
import { AppDynamicFormComponent, FormFieldConfig } from '@shared/components/dynamic-form/dynamic-form.component';

export interface FormFieldConfig {
  name: string;                         // Form control key
  label: string;                        // Visible field label
  type: 'text' | 'number' | 'email';    // Input control type
  placeholder?: string;                 // Input placeholder
  required?: boolean;                   // Displays red asterisk
  errorMessage?: string;                // Validation error text
  fieldClass?: string;                  // Field-specific CSS class for grid sizing
}
```

| Input Property | Type | Default | Description |
| :--- | :--- | :--- | :--- |
| `form` | `FormGroup` | **Required** | Reactive form instance with validators. |
| `fields` | `FormFieldConfig[]` | **Required** | Field configuration schema array. |
| `submitLabel` | `string` | `'Submit'` | Action button label. |
| `loading` | `boolean` | `false` | Action button loading state. |
| `layout` | `'vertical' \| 'horizontal' \| 'grid'` | `'vertical'` | Structural form layout. |
| `containerClass` | `string` | `''` | Outer wrapper CSS class. |
| `styleClass` | `string` | `''` | Form container CSS class for visual parity. |

### Output Events
| Output Event | Type | Description |
| :--- | :--- | :--- |
| `formSubmit` | `EventEmitter<void>` | Emitted when submit button clicked and form is valid. |

### Example Usage in Feature Component
```html
<app-dynamic-form
  [form]="featureForm"
  [fields]="formFields"
  submitLabel="Save Application"
  [loading]="saving()"
  layout="grid"
  containerClass="flms-form-panel"
  styleClass="legacy-aligned-form"
  (formSubmit)="onSave()"
/>
```

---

## 3. Visual Parity Rule for Shared Components
- Pass legacy CSS class names or equivalent utility classes via `styleClass`, `tableStyle`, `containerClass`, `columnStyleClass`, and `fieldClass`.
- Retain identical table column widths, grid columns, field spacing, and color schemes observed in legacy XHTML.

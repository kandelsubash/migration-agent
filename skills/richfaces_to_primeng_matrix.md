# RichFaces & JSF to Angular 22 + PrimeNG 22 Conversion Matrix

> Token-optimized lookup matrix for Claude 3.5 Sonnet. Maps legacy JSF/RichFaces syntax directly to Angular 22 (Standalone + Signals) and PrimeNG 22.

## 1. Component Tag Mappings

| Legacy Tag | Angular 22 + PrimeNG 22 Equivalent | Conversion Rules & Notes |
| :--- | :--- | :--- |
| `rich:dataTable` | `<app-data-table>` wrapping `<p-table>` | Use `@shared/components/data-table`. Pass `columns`, `data`, `paginator`, `rows`. Pass `styleClass`, `tableStyle`, `containerClass`. |
| `rich:extendedDataTable` | `<app-data-table>` wrapping `<p-table>` | Enable column resize/reorder via table inputs and PrimeNG directives. |
| `rich:column` | `TableColumn` config object | Map `header`, `field`, `sortable`, `width`, `columnStyleClass`. |
| `h:form` | `<app-dynamic-form>` or Reactive `[formGroup]` | Use `@shared/components/dynamic-form` for standard forms. Use `FormGroup` with `Validators` (SEC-04). |
| `h:inputText` | `<input pInputText>` / `FormFieldConfig` | Type `text`. Bind via `formControlName`. |
| `h:inputTextarea` | `<textarea pInputTextarea>` / `FormFieldConfig` | Type `textarea`. Map `rows`, `cols`. |
| `h:selectOneMenu` | `<p-select>` (PrimeNG 22) / `<p-dropdown>` | Map `options`, `optionLabel`, `optionValue`. |
| `h:selectManyCheckbox` | `<p-checkbox>` group | Array-bound reactive form control. |
| `h:selectBooleanCheckbox` | `<p-checkbox>` [binary]="true" | Boolean reactive form control. |
| `h:selectOneRadio` | `<p-radioButton>` group | Bind each radio option to shared `formControlName`. |
| `rich:calendar` | `<p-datepicker>` (PrimeNG 22) | Map `dateFormat`, `showTime`, `showIcon`. |
| `rich:popupPanel` | `<p-dialog>` | Modal `[modal]="true"`, closable, draggable. State managed via `signal<boolean>()`. |
| `rich:tabPanel` / `rich:tab` | `<p-tabs>` / `<p-tab>` (PrimeNG 22) | Modern tabs structure. Active tab state in Signal. |
| `rich:accordion` | `<p-accordion>` / `<p-accordionPanel>` | Multi-panel or single collapsible sections. |
| `rich:fileUpload` | `app-file-upload` / `<p-fileupload>` | Map `accept`, `maxFileSize`, `multiple`. |
| `rich:tree` / `rich:treeNode` | `<p-tree>` / `<p-treetable>` | Hierarchical tree nodes via `TreeNode<T>[]`. |
| `rich:pickList` | `<p-picklist>` | Dual-list transfer control with source/target arrays. |
| `rich:orderingList` | `<p-orderlist>` | Reorderable list component. |
| `rich:autocomplete` | `<p-autocomplete>` | Search suggestions bound to async search signal/method. |
| `rich:editor` | `<p-editor>` | Rich text editor. Output sanitized via SafeHtmlPipe (SEC-01). |
| `rich:progressBar` | `<p-progressbar>` | Progress percentage binding. |
| `rich:tooltip` | `[pTooltip]="text"` | Attribute directive on target element. |
| `rich:messages` / `h:messages` | `<p-toast>` + `<p-message>` | Injected `MessageService` / `NotificationService`. |
| `h:commandButton` | `<p-button>` with `(click)` | Synchronous button action. |
| `a4j:commandButton` | `<p-button>` with `[loading]` + `(click)` | Async AJAX action calling service method returning Observable. |
| `h:commandLink` / `a4j:commandLink` | `<p-button [link]="true">` or `<a>` with `(click)` | Text link with click handler. |
| `h:link` / `h:outputLink` | `<a [routerLink]="...">` | Angular client-side router link. |
| `h:outputText` | `{{ value() }}` (Interpolation) | Signal or template expression. |
| `h:panelGrid` | CSS Grid (`display: grid`) | Map `columns="N"` to CSS `grid-template-columns: repeat(N, 1fr)`. |
| `h:panelGroup` | `<div>` or `<ng-container>` | Structural wrapper. |

---

## 2. Dynamic Behavior & AJAX Conversions

| Legacy Pattern | Angular 22 / PrimeNG 22 Pattern | Example |
| :--- | :--- | :--- |
| `rendered="#{expr}"` | Modern `@if (condition())` | `@if (user.isActive()) { ... }` |
| `<ui:repeat>` / `c:forEach` | Modern `@for (item of items(); track item.id)` | Mandatory `track` expression. |
| `reRender="id1, id2"` / `render="id1"` | Signal state update triggers fine-grained OnPush DOM update | Updating `item.set(newItem)` refreshes bound templates automatically. |
| `actionListener="#{bean.handler}"` | Component method call via `(click)="handler()"` | `(click)="onSave()"` |
| `oncomplete="javascript:..."` | Observable `.subscribe({ next: () => ... })` completion block | Executes logic after API response succeeds. |
| `f:ajax event="change"` | Form control valueChanges or `(onChange)` | `control.valueChanges.subscribe(...)` |
| `f:validateLength min="X" max="Y"` | Angular `Validators.minLength(X)`, `Validators.maxLength(Y)` | Attached to reactive `FormControl`. |
| `f:convertDateTime pattern="dd/MM/yyyy"` | Angular `DatePipe` (`\| date:'dd/MM/yyyy'`) | Template interpolation pipe. |
| `#{msg['key']}` | i18n JSON translation file key | Key added to `assets/i18n/en.json`. |

---

## 3. Scope & State Mapping

- **`@RequestScoped` / `@ViewScoped` Bean:** Migrates to local component properties and **Signals** (`signal<T>()`, `computed()`).
- **`@SessionScoped` / `@ApplicationScoped` Bean:** Migrates to `@Injectable({ providedIn: 'root' })` singleton Angular service.
- **EL Expressions `#{bean.prop}`:** Converted to typed signal read `this.prop()` or form control value `this.form.get('prop')?.value`.

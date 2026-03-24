# DIP-897: Codebase Context — Template Deletion

## Files to Modify

### 1. API Service Layer
**`src/app/shared/services/api.service.ts`** (line ~751)
- Existing template methods at lines 728-751: GET list, POST create, PATCH update
- URL pattern: `${environment.apiUrl}/v1/${skolaIzo}/sablona-pozvanky/${id}/`
- Need to add: `deleteSablonaPozvanky(skolaIzo: string, sablonaPozvankyId: string)` — HTTP DELETE

### 2. Feature Service
**`src/app/skola/services/sablony-pozvanek.service.ts`** (112 lines)
- Injects: `ZodParseService`, `ApiService`, `SelectedIzoService`, `NotificationService`, `Router`
- Pattern for methods: get IZO → try API call → show success/error notification
- No Zod parsing needed for DELETE (no response body expected)
- Need to add: `deleteSablonaPozvanky(id: string): Promise<boolean>`

### 3. List Component
**`src/app/skola/components/sablony-pozvanek-list/sablony-pozvanek-list.component.ts`** (35 lines)
- Standalone component, imports: CommonModule, ButtonModule, TableModule, RouterModule, MessageModule
- Constructor injects: `SablonyPozvanekService`
- Loads templates in `ngOnInit()`, stores in `sablonyPozvanek` WritableSignal
- Need to add: `ConfirmationService` via `inject()`, `smazat(sablona)` method

**`src/app/skola/components/sablony-pozvanek-list/sablony-pozvanek-list.component.html`** (86 lines)
- Table body template at line 59: shows `sablona.nazev` + "Upravit" button per row
- Button cell at line 64: `<td class="flex justify-content-end">`
- Need to add: "Smazat" button next to "Upravit"

### 4. Detail Component
**`src/app/skola/components/sablony-pozvanek-detail/sablony-pozvanek-detail.component.ts`** (451 lines)
- Standalone component, constructor injects: SablonyPozvanekService, Router, Clipboard, MessageService, UserInfoService, ChangeDetectorRef
- Has `mode` signal: `'new' | 'update' | null`
- Has `id` input (string | null)
- Has `canDeactivate()` guard that shows browser confirm when `hasDraft()` is true — must clear draft state before navigating after delete
- Need to add: `ConfirmationService` via constructor (consistent with existing 6 constructor params), `smazat()` method (only visible in 'update' mode)

**`src/app/skola/components/sablony-pozvanek-detail/sablony-pozvanek-detail.component.html`** (119 lines)
- Footer buttons at lines 104-118: "Uložit" + "Zpět"
- Need to add: "Smazat" button (only in update mode), positioned after "Zpět"

## Reference Patterns

### Confirmation Dialog (primary reference)
**`src/app/skola/components/prihlaska-sdilene-prilohy/prihlaska-sdilene-prilohy.component.ts`** (line 255-300)
```typescript
this.confirmationService.confirm({
  message: `Opravdu chcete trvale smazat přílohu „${soubor.filename}" z kategorie „${typLabel}"? Tato akce je nevratná.`,
  header: 'Smazat přílohu?',
  acceptLabel: 'Ano, smazat',
  rejectLabel: 'Ne',
  acceptButtonStyleClass: 'p-button-danger',
  rejectButtonStyleClass: 'p-button-secondary p-button-outlined',
  accept: async () => { /* API call */ },
});
```
Uses `inject(ConfirmationService)` at line 66.

### Global confirm dialog
**`src/app/app.component.html`** line 4: `<p-confirmDialog [style]="{ width: '50vw' }"></p-confirmDialog>` — already present.

## Data Flow

```
User clicks "Smazat" → ConfirmationService.confirm() → accept callback →
SablonyPozvanekService.deleteSablonaPozvanky(id) →
  SelectedIzoService.currentIzo() → ApiService.deleteSablonaPozvanky(izo, id) →
  HTTP DELETE /v1/{skolaIzo}/sablona-pozvanky/{id}/ →
  Success toast + refresh list (or navigate back from detail)
```

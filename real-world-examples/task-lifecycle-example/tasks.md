# DIP-897: Task Breakdown

**Classification**: Simple Extension — follows established patterns, low risk. Single phase.

## Phase 1: Complete Implementation

All changes are small and interdependent. Delivering as a single functional slice.

### Tasks

1. **Add `deleteSablonaPozvanky` to `api.service.ts`**
   - HTTP DELETE method following existing pattern (lines 728-751)
   - URL: `${environment.apiUrl}/v1/${skolaIzo}/sablona-pozvanky/${sablonaPozvankyId}/`

2. **Add `deleteSablonaPozvanky` to `sablony-pozvanek.service.ts`**
   - Follow existing method pattern (get IZO → try API call → notify)
   - Returns `Promise<boolean>` — true on success, false on error
   - Success toast: "Šablona pozvánky byla smazána"

3. **Add delete to list component**
   - Import `ConfirmationService` from `primeng/api` (NO `ConfirmDialogModule` — global `<p-confirmDialog>` in `app.component.html` handles rendering)
   - Inject `ConfirmationService` via `inject()` (coding rules prefer `inject()`)
   - Add `smazat(sablona: SablonaPozvanky)` method with confirmation dialog
   - Add "Smazat" button in template next to "Upravit"
   - Refresh list after successful delete

4. **Add delete to detail component**
   - Import `ConfirmationService` from `primeng/api` (NO `ConfirmDialogModule`)
   - Inject via constructor (detail component already has 6 constructor-injected dependencies — stay consistent locally)
   - Add `smazat()` method (only callable in update mode)
   - In `accept` callback: clear draft state before navigating to prevent `canDeactivate` double-dialog
   - Add "Smazat" button in footer (visible only when `mode() === 'update'`), visually separated from Uložit/Zpět with `ml-auto`
   - Navigate to list after successful delete

### Success Criteria

- Can delete a template from the list view with confirmation
- Can delete a template from the detail/edit view with confirmation
- Success toast appears after deletion
- List refreshes / navigates back after deletion
- "Smazat" button not visible when creating a new template
- API errors are shown as error toasts

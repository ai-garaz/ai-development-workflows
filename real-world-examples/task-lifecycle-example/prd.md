# DIP-897: Delete Invitation Templates — PRD

## Overview

Schools cannot delete invitation templates ("šablony pozvánek") they no longer need. Over two years, some schools accumulated 15+ templates, including ones marked "(neplatná)". The BE DELETE endpoint already exists (soft delete). This task adds the FE delete capability in both list and detail views.

Time-sensitive: useful only while schools are still preparing invitations (before early-mid March 2026). Priority C — improvement, not a bug fix.

## Scope

**In scope:**
- DELETE HTTP call in API service
- Delete method in feature service with error handling + success notification
- Delete button with confirmation dialog in list view (per row)
- Delete button with confirmation dialog in detail/edit view (update mode only) — rationale: schools will typically open a template to read it before deciding to delete, so the button should be available in-place without forcing navigation back to the list

**Out of scope:**
- BE changes (endpoint already complete and deployed)
- "In use" validation (BE doesn't enforce it — templates can be deleted regardless)
- Bulk delete

## User Stories

**Primary flow (list):**
1. School admin navigates to Nastavení → Šablony pozvánek
2. Sees list of templates with "Upravit" and "Smazat" buttons per row
3. Clicks "Smazat" on a template
4. Confirmation dialog: "Opravdu chcete smazat šablonu „{název}"?"
5. Confirms → template disappears from list, success toast shown

**Primary flow (detail):**
1. School admin opens a template for editing
2. Sees "Smazat" button next to "Zpět" in the footer
3. Clicks "Smazat"
4. Same confirmation dialog
5. Confirms → navigated back to list, success toast shown

**Edge cases:**
- **New template mode**: "Smazat" button is NOT shown (no id to delete)
- **API error (403/404)**: Error toast shown via `propagateBackendErrors()`, template stays in list
- **Unsaved changes in detail + delete**: The detail component has a `canDeactivate` guard that shows a browser confirm dialog when navigating away with unsaved changes. After a successful delete, reset draft state before navigating, so the guard does not trigger a second confirmation dialog.

## Functional Requirements

1. `api.service.ts`: `deleteSablonaPozvanky(skolaIzo, sablonaPozvankyId)` — `HTTP DELETE /v1/{skolaIzo}/sablona-pozvanky/{sablonaPozvankyId}/`
2. `sablony-pozvanek.service.ts`: `deleteSablonaPozvanky(id)` — resolves IZO, calls API, shows success toast "Šablona pozvánky byla smazána", returns `true`/`false`
3. List component: "Smazat" button per row with `pi-trash` icon, `p-button-danger p-button-outlined p-button-sm` styling. Calls `ConfirmationService.confirm()` on click.
4. List component: on successful delete, refreshes the template list
5. Detail component: "Smazat" button in footer (only in `update` mode), `p-button-danger p-button-outlined` styling. Visually separated from "Uložit"/"Zpět" group — use `ml-auto` or a flex spacer to push it to the right end of the button row. Same confirmation pattern.
6. Detail component: on successful delete, clear draft state to prevent `canDeactivate` guard from triggering, then navigate to list
7. Confirmation dialog: header "Smazat šablonu?", message "Opravdu chcete smazat šablonu „{název}"?", accept "Ano, smazat", reject "Ne"

## Codebase References

- **Pattern to follow**: `src/app/skola/components/prihlaska-sdilene-prilohy/prihlaska-sdilene-prilohy.component.ts` (lines 255-300) — ConfirmationService with async accept
- **API pattern**: `src/app/shared/services/api.service.ts` lines 728-751 — existing template methods
- **Service pattern**: `src/app/skola/services/sablony-pozvanek.service.ts` — existing CRUD methods
- **Global dialog**: `src/app/app.component.html` line 4 — `<p-confirmDialog>` already present

## API Integration

- **Endpoint**: `DELETE /v1/{skolaIzo}/sablona-pozvanky/{sablonaPozvankyId}/`
- **Payload**: None (path params only)
- **Success**: 200 (no body needed)
- **Errors**: 403 (wrong IZO, not allowed in mode), 404 (not found) — handled by `propagateBackendErrors()`

## Risks

None significant. Soft delete is reversible at DB level. No shared references to break.

# DIP-897: Active Context

## Status: PR Created

## Current State
- Implementation complete, build passes
- Branch: `feature/DIP-897-template-deletion`

## What Was Completed
- API service: `deleteSablonaPozvanky()` HTTP DELETE method
- Feature service: `deleteSablonaPozvanky()` with IZO resolution, firstValueFrom, error handling, success toast
- List component: "Smazat" button per row with confirmation dialog, list refresh after delete
- Detail component: "Smazat" button in footer (update mode only), confirmation dialog, draft state cleared before navigate

## Key Decisions
- `inject()` for list component, constructor injection for detail component (consistency with existing code)
- No `ConfirmDialogModule` import needed — global `<p-confirmDialog>` handles rendering
- `ml-auto` to visually separate delete button from save/back group in detail view
- Draft state cleared before navigation to prevent `canDeactivate` double-dialog

## What Remains
- PR review and merge
- Post-merge cleanup (switch to main, delete feature branch)
- Issue tracker status update

# User feedback rules

## Instructions

- The `UserFeedbackService` (`frontend/src/app/services/user-feedback.service.ts`) is used to communicate various states and messages to the user
- Access the service using Angular's `inject()` function for dependency injection (per Angular 20 best practices)

**Example usage:**
```typescript
import { inject } from '@angular/core';

export class MyComponent {
  private readonly userFeedbackService = inject(UserFeedbackService);
  // ... use the service
}
```

### Loading States

Two types of loaders are available:

- **Blocking Loaders**: Use when you need to prevent user interaction until a task completes
  ```typescript
  userFeedbackService.addGlobalLoader(true); // Start blocking loader
  userFeedbackService.removeGlobalLoader(true); // Remove blocking loader
  ```
- **Non-Blocking Loaders**: Use for background tasks where user can continue interacting
  ```typescript
  userFeedbackService.addGlobalLoader(); // Start non-blocking loader
  userFeedbackService.removeGlobalLoader(); // Remove non-blocking loader
  ```

### Success Messages

Use only when the success isn't immediately obvious to the user

```typescript
userFeedbackService.showSuccess({
  bodyMessage: "Your changes have been saved",
  topSummary: "Save Complete", // optional title
  life: 3000, // optional duration in ms (default: 5000)
  sticky: false, // optional: keep until user dismisses
});
```

### Information Messages

Use for general updates or context that doesn't require user action

```typescript
userFeedbackService.showInfo({
  message: "New features available",
  details: "Check out our latest updates in the changelog", // optional
});
```

### Warning Messages

Use for non-critical alerts that users should be aware of

```typescript
userFeedbackService.showWarning({
  message: "Unsaved changes",
  details: "Your changes will be lost if you leave", // optional
  timeout: 8000, // optional duration in ms (default: 5000)
});
```

### Error Messages

Three types of error handling:

1. **User-Correctable Errors**: Use `showError` for issues the user can fix

```typescript
userFeedbackService.showError({
  message: "Invalid Input",
  details: "Please enter a valid email address", // optional
});
```

2. **Backend Errors**: Use `propagateBeError` for displaying formatted error messages from the backend

```typescript
userFeedbackService.propagateBeError(
  error, // The error object from the API call (typically HttpErrorResponse)
  "Failed to update scenario" // optional fallback message if error can't be parsed
);
```

3. **Unexpected Errors**: Use `somethingWentWrong` ONLY for unexpected technical issues that are not coming from backend responses

```typescript
userFeedbackService.somethingWentWrong(
  "Failed to process local data", // debug message (logged to console)
  error // optional error details (logged to console)
);
```

### Error Handling Decision Guide

- Use `propagateBeError` when:
  - Handling errors from API responses
  - The error contains structured information from the backend
  - You want to display the specific error message from the backend to the user

- Use `somethingWentWrong` when:
  - An unexpected error occurs in the frontend code
  - You encounter null/undefined values when they're expected to exist
  - A logic error occurs that isn't related to API communication
  - Local data processing fails

### Best Practices

- Use blocking loaders for critical operations (e.g., scenario creation, conversation submissions, data saving)
- Use non-blocking loaders for background tasks (e.g., data prefetching, auto-save, real-time updates)
- Skip success messages when the result is visually obvious (e.g., form appearing after click, data showing in UI)
- Include specific, actionable messages in user-correctable errors
- Keep messages concise and clear
- Include details parameter when additional context would help users understand or resolve the situation
- For real-time features (audio recording, WebSocket connections), provide continuous feedback through loading states rather than blocking the UI

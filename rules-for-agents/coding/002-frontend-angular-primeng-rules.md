# Frontend Rules (Angular + PrimeNG)

Follow these rules whenever analyzing, writing, debugging, planning, or refactoring frontend code.

## Angular 20 Breaking Changes & New Patterns

### DEPRECATED: Old Control Flow Syntax

<critical>
- **NEVER use** `*ngIf`, `*ngFor`, or `[ngSwitch]` - these are **deprecated in Angular 20** and will be removed in Angular 22
- **ALWAYS use** the new control flow syntax: `@if`, `@for`, `@switch`
- The new syntax is faster, reduces bundle size, and fixes memory leaks in `*ngFor`
- You do NOT need to import CommonModule for control flow blocks
</critical>

### DEPRECATED: Old Injection Pattern

<critical>
- **NEVER use** `InjectFlags` API - removed in Angular 20
- **ALWAYS use** the options-based approach with `inject()`
</critical>

### Component Architecture

- Use **standalone components** by default (no NgModules)
- **Do NOT set `standalone: true`** in component decorators - it's the default in Angular v20+
- Use `ChangeDetectionStrategy.OnPush` for all components
- Leverage Angular's **signals system** - all signal primitives are now stable (signal, effect, linkedSignal, signal-based queries, inputs)
- **Use `input()` and `output()` functions** instead of `@Input()` and `@Output()` decorators for component inputs/outputs
- Use **signal-based inputs** and **signal-based queries** (viewChild, contentChild, etc.)
- **Do NOT use `@HostBinding` and `@HostListener` decorators** - put host bindings inside the `host` object of the `@Component` or `@Directive` decorator instead
- Minimize RxJS complexity - prefer signals, or convert promises/observables to signals using stable conversion utilities
- Use the `inject()` function for dependency injection within components and services
- Use strongly-typed inputs and outputs with TypeScript

### File Organization

- **For larger components**, use **separate files** for HTML, TS, and SCSS:
  - `component-name.component.ts`
  - `component-name.component.html`
  - `component-name.component.scss`
- **For small, simple components**, prefer inline templates to reduce file count
- When using external templates/styles, use paths **relative to the component TS file**
- Component-specific styles are kept in their respective `.scss` files
- Create subcomponents for well-separable, self-contained functionality
- Shared, reusable components go in `frontend/src/app/shared/`
- Feature-specific components within their feature folders

### State Management

- **Prefer signals** for component state and reactive data
- Use **`computed()`** for derived state that depends on other signals (read-only computed values)
- Use **`linkedSignal()`** for derived state that needs to be writable
- Use **signal-based inputs** with `input()` function for component inputs
- Use `effect()` for side effects when signals change (now stable)
- **Do NOT use `mutate` on signals** - use `update()` or `set()` instead for proper change detection
- Convert API responses to signals using `toSignal()` (now stable)
- Keep state transformations pure and predictable
- Use RxJS observables only when:
  - Working with WebSocket streams
  - Handling complex event sequences
  - Real-time audio/conversation streams require observable patterns
- Use `toObservable()` and `toSignal()` to bridge between signals and observables (now stable)

### Services

- Design services around a single responsibility
- Use the `providedIn: 'root'` option for singleton services
- Use the `inject()` function instead of constructor injection
- Use signals in services for shared state when appropriate
- Return typed responses from API calls

### Forms

- **Prefer Reactive forms** over Template-driven forms
- Use strongly-typed forms with TypeScript
- Handle form validation declaratively
- Refer to PrimeNG 20 documentation for updated form component demos

## PrimeNG 20 Guidelines

### Component Usage

- Use **PrimeNG 20+** components for UI elements
- Leverage the new Material 3 design system via PrimeUIX
- ALWAYS consider if what we're creating can be done using PrimeNG components
- Minimize customization unless necessary or explicitly requested
- Import only the specific PrimeNG modules needed (tree-shaking)
- For form components, refer to the updated template-driven and reactive form demos in PrimeNG 20

### Images

- **Use `NgOptimizedImage`** for all static images
- Note: `NgOptimizedImage` does not work for inline base64 images
- Leverage built-in image optimization features (lazy loading, srcset, etc.)

### Styling Approach & Tailwind CSS Integration

<critical>
- **PrimeFlex is deprecated** - PrimeTek no longer maintains PrimeFlex and officially recommends Tailwind CSS
- Use the **`tailwindcss-primeui`** plugin for first-class integration between PrimeNG and Tailwind CSS
- The plugin provides PrimeNG theme tokens as Tailwind utilities (e.g., `bg-primary`, `text-surface-500`, `text-muted-color`)
</critical>

**Styling priority:**
1. **First choice**: Use PrimeNG's built-in component capabilities
2. **Second choice**: Use **Tailwind CSS utility classes** for layouts, spacing, colors, and responsive design
3. **Third choice**: Use PrimeNG's semantic color utilities from the `tailwindcss-primeui` plugin (e.g., `bg-primary`, `border-surface`)
4. **Last resort**: Custom SCSS for styling that cannot be achieved through PrimeNG and Tailwind

**Key Tailwind utilities from the plugin:**
- Color palette: `primary-[50-950]`, `surface-[0-950]`, `bg-emphasis`, `bg-highlight`
- Text colors: `text-color`, `text-muted-color`, `text-color-emphasis`
- Borders: `border-surface`, `rounded-border`
- All Tailwind variants work: `dark:sm:hover:bg-primary`

### PrimeNG Best Practices

- ALWAYS check if modifications to default PrimeNG components/layouts are necessary
- NEVER write obsolete classes/SCSS code to achieve what PrimeNG does by default
- Use Tailwind's responsive breakpoints instead of custom media queries
- Leverage Tailwind's utility-first approach for rapid UI development

## UI & Design Guidelines

### Styling Rules

<critical>
- NEVER use `@import` in SCSS files - use `@use` instead
- Always place `@use` statements at the beginning of the file
- Use **Tailwind CSS** utility classes instead of writing custom CSS whenever possible
- **Do NOT use `ngClass`** - use `class` bindings instead (e.g., `[class.active]="isActive"`)
- **Do NOT use `ngStyle`** - use `style` bindings instead (e.g., `[style.color]="textColor"`)
</critical>

- Use **BEM naming convention** for custom classes (Block__Element--Modifier) - but only when Tailwind utilities are insufficient
- Prefer Tailwind utility classes over custom SCSS whenever possible
- Keep component-specific styles scoped to their `.scss` files
- Use design tokens from PrimeNG's theming system (available as Tailwind utilities via `tailwindcss-primeui`)

### Design System Principles

- Maintain consistent spacing using Tailwind spacing utilities (e.g., `p-4`, `m-2`, `gap-6`)
- Use PrimeNG's color palette via Tailwind utilities (e.g., `bg-primary`, `text-surface-500`)
- Follow Material 3 design principles (PrimeNG 20's foundation)
- Ensure responsive design using Tailwind's responsive utilities (e.g., `sm:`, `md:`, `lg:`)

## TypeScript & Template Features

- Angular 20 requires **TypeScript 5.8+**
- Use strict type checking
- Prefer type inference when the type is obvious
- Avoid the `any` type; use `unknown` when type is uncertain

### Templates

<critical>
- Keep templates simple and avoid complex logic
- Use native control flow (`@if`, `@for`, `@switch`) instead of `*ngIf`, `*ngFor`, `*ngSwitch`
- Use the async pipe to handle observables
- **Do NOT assume globals** like `new Date()` are available in templates
- **Do NOT write arrow functions** in templates (they are not supported)
- **Do NOT write Regular expressions** in templates (they are not supported)
</critical>

- New template features available in Angular 20:
  - Template string literals
  - Exponentiation operator
  - `in` keyword
  - `void` operator

## Accessibility

- **MUST pass all AXE checks** - use automated accessibility testing
- **MUST follow all WCAG AA minimums**, including:
  - Focus management (visible focus indicators, logical tab order)
  - Color contrast ratios (4.5:1 for normal text, 3:1 for large text)
  - Proper ARIA attributes and roles
  - Keyboard navigation support

- Use semantic HTML elements
- Provide proper ARIA labels for interactive elements
- Ensure keyboard navigation works properly with logical tab order
- PrimeNG components have built-in accessibility - don't override without reason
- Handle focus management explicitly for modals, dialogs, and dynamic content

## Performance

- Use OnPush change detection strategy
- Consider **zoneless change detection** (developer preview in Angular 20) for improved performance
- Lazy load routes and heavy components
- Use Angular's built-in optimization features
- Minimize bundle size by importing only needed PrimeNG modules
- Consider virtual scrolling for long lists (PrimeNG provides this)
- New control flow syntax (`@if`, `@for`, `@switch`) is faster and produces smaller bundles than old directives

## Audio/Voice Features

- Handle audio streams using Web APIs (Web Audio API, MediaRecorder)
- Implement proper cleanup for audio resources
- Use appropriate buffers for real-time audio processing
- Provide visual feedback for audio states (recording, processing, playing)
- Handle microphone permissions gracefully

## Real-time Communication

- Use signals for WebSocket state management
- Handle connection states explicitly (connecting, connected, disconnected, error)
- Implement reconnection logic for WebSocket failures
- Provide user feedback for connection status
- Clean up WebSocket connections on component destroy

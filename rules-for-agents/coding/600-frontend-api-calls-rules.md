# API calls rules

## Instructions

- Use the `ApiService` for all API calls (located in `frontend/src/app/services/` or `frontend/src/app/shared/services/`)
- Unless a stream is expected, use `firstValueFrom` to convert observables to promises directly in the `ApiService`
- Generally, API calls in the `ApiService` should return response data, not the full response object, unless BE pagination is being implemented
- There might be exceptions that require an observable or the full response object and in such cases this should be indicated in the name of the method (e.g., `getUsersObservable`)
- Error handling is done in each business logic service (e.g., `AuthService`, `ScenariosService`, `ConversationsService`)
- Use Angular's `inject()` function for dependency injection in services (per Angular 20 best practices)

<example>

  ```typescript
    refreshToken(refreshToken: string): Promise<RefreshResponseData> {
      const headers = new HttpHeaders().set(SKIP_INTERCEPTOR_HEADER, '');
      const response = this.http
        .post(`${this.apiUrl}/auth/refresh`, { refreshToken }, { headers })
        .pipe(this.zodParseService.mapObservableWithData(ZRefreshResponse));

      return firstValueFrom(response);
    }
  ```
</example>

## Data Flow Patterns

### API Response Handling

Our application follows a structured pattern for handling API data:

1. **Schema Definition** (Zod schema files, typically `*.zod.ts`):
- Define Zod schemas for both request and response data
- Export TypeScript types derived from these schemas
- Use `createResponseSchema()` and `createResponseSchemaWithPagination()` from `schemas/core.zod.ts` for consistent API response wrappers
- Schemas can be colocated with services or organized in shared locations

<example>

  ```typescript
  // Example from scenario.zod.ts
  export const ZScenario = z.object({
    id: z.string(),
    title: z.string(),
    description: z.string(),
    // ... other fields
  });
  export const ZScenarioListResponse = createResponseSchemaWithPagination(z.array(ZScenario));
  export const ZScenarioDetailResponse = createResponseSchema(ZScenario);

  // Export TypeScript types
  export type Scenario = z.infer<typeof ZScenario>;
  export type ScenarioListResponse = z.infer<typeof ZScenarioListResponse>;
  export type ScenarioDetailResponse = z.infer<typeof ZScenarioDetailResponse>;
  ```
</example>

2. **API Service Layer** (`ApiService`):
- Handles all direct communication with the backend
- Uses Zod schemas to validate and parse responses
- Returns promises with typed data
- Converts HTTP responses to data using `zodParseService.mapObservable` or `mapObservableWithData`

<example>

  ```typescript
  // Example API method with pagination
  getScenarios(
    userId: string,
    pageNumber: number = 1,
    pageSize: number = 100
  ): Promise<ScenarioListResponse> {
    const response = this.http
      .get(
        `${this.apiUrl}/users/${userId}/scenarios?page[number]=${pageNumber}&page[size]=${pageSize}`
      )
      .pipe(this.zodParseService.mapObservable(ZScenarioListResponse));

    return firstValueFrom(response);
  }
  ```
</example>

3. **Business Logic Services** (feature or shared services):
- Utilize API calls from `ApiService`
- Maintain state with signals for data that will be consumed by components
- Handle error propagation using `UserFeedbackService` or similar notification service

<example>

  ```typescript
  // Example usage in business service
  try {
    const scenarios = await this.apiService.getScenarios(
      currentUser.id,
      page,
      limit
    );
    this.scenariosSignal.set(scenarios);
  } catch (error) {
    this.userFeedbackService.propagateBeError(error, 'Failed to load scenarios');
  }
  ```
</example>

4. **Component Layer**:
- Use readonly signals from services
- No direct API calls
- Handle loading states via service

<example>

  ```typescript
  // Using inject() function (Angular 20 best practice)
  @Component({
    selector: 'app-scenario-selector',
    standalone: true,
    // ...
  })
  export class ScenarioSelectorComponent {
    private readonly scenariosService = inject(ScenariosService);

    readonly scenarios = this.scenariosService.scenariosList;
    readonly selectedScenario = this.scenariosService.selectedScenario;
  }
  ```
</example>

### Alternative Pattern: Component-Managed State

In specific cases where different components need to display different states of the same data simultaneously (e.g., different filtering/sorting in multiple views), we may use a stateless approach:

<example>

  ```typescript
  // Business service provides a stateless method
  async fetchScenarios(
    page: number,
    limit: number,
    filterParams: Record<string, string>
  ): Promise<ScenarioListResponse | null> {
    try {
      return await this.apiService.getScenarios(
        currentUser.id,
        page,
        limit,
        filterParams
      );
    } catch (error) {
      this.userFeedbackService.propagateBeError(error, 'Failed to load scenarios');
      return null;
    }
  }

  // Component manages its own state (using inject() function)
  @Component({
    selector: 'app-scenario-list',
    standalone: true,
    // ...
  })
  export class ScenarioListComponent {
    private readonly scenariosService = inject(ScenariosService);

    private readonly scenariosResponseSignal = signal<ScenarioListResponse | null>(null);
    protected readonly loading = signal(false);

    protected readonly scenarios = computed(() => this.scenariosResponseSignal()?.data || null);

    private async loadData() {
      try {
        this.loading.set(true);
        const scenarios = await this.scenariosService.fetchScenarios(page, limit, filters);
        this.scenariosResponseSignal.set(scenarios);
      } finally {
        this.loading.set(false);
      }
    }
  }
  ```
</example>

This pattern should be used selectively when:
- Multiple components need different views of the same data simultaneously
- Components already manage their own filter/sort/pagination state
- Service-managed state would lead to interference between components

### List vs Single Item Pattern

#### List Pattern

- Response includes pagination metadata
- Business service maintains both raw response (for metadata) and computed list
- Components can access both list data and pagination info
- Loading states should preserve previous data until new data arrives

#### Single Item Pattern

- Response wrapped in standard response envelope
- Business service typically only needs to maintain the item itself
- Loading states may clear previous data depending on UX requirements

### Error Handling

- API service focuses on data validation and parsing with zod
- Business services handle errors and present them to user via UserFeedbackService
- Components remain clean of error handling logic

### Loading States

- Business services manage loading states
- Use blocking loaders for critical operations
- Use non-blocking loaders for background updates
- Preserve existing data during list updates


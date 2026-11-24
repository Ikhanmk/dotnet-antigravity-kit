# Angular Rules (Angular 19+)

Guidelines for writing clean, modern, and reactive Angular code integrated with .NET backends.

## 1. Component Architecture & Modern Syntax
- **Always Standalone**: Every component, directive, and pipe must be `standalone: true`. Never introduce `NgModule` unless wrapping a legacy 3rd-party library.
- **Signals over RxJS for Local State**: Use `signal()` and `computed()` for component-local state. Reserve RxJS `Observable` for async streams, event debouncing, or `HttpClient`.
- **Signal Inputs & Outputs**:
  - DO use `input<T>()`, `input.required<T>()`, and `output<T>()`.
  - DON'T use `@Input()` or `@Output()` decorators.
- **Built-in Control Flow**:
  - DO use `@if`, `@else if`, `@else`, `@switch`, `@case`, and `@for`.
  - ALWAYS provide tracking in `@for`: `@for (item of items(); track item.id)`.
  - DON'T use structural directives like `*ngIf`, `*ngFor`, or `*ngSwitch`.
- **Dependency Injection**: Use `inject(Service)` instead of constructor injection.

## 2. Full-Stack .NET API Integration
- **Typed HTTP Services**: Encapsulate API calls in dedicated feature services using `HttpClient`.
- **Match Backend DTOs**: TypeScript models must match .NET record DTOs exactly (camelCase on JSON wire).
- **RFC 9457 ProblemDetails Handling**: Catch HTTP errors through a functional interceptor (`HttpInterceptorFn`) that parses backend `ProblemDetails`.
- **Date Handling**: Treat backend UTC dates as ISO strings; format in templates using Angular's `date` pipe or `Intl.DateTimeFormat`.

## 3. Performance & Clean Code
- **Change Detection**: Components must default to `changeDetection: ChangeDetectionStrategy.OnPush`.
- **Lazy Loading**: Route declarations must use `loadComponent: () => import('./path').then(m => m.Component)` or `loadChildren`.
- **No Direct DOM Manipulation**: Never use `document.querySelector` or raw DOM APIs; use `ElementRef` with `Renderer2` or signal view queries (`viewChild`).

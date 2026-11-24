---
name: angular
description: >
  Guidance for modern standalone Angular applications (Angular 19+), Signals,
  signal inputs/outputs, modern control flow (@if, @for), OnPush change detection,
  functional HTTP interceptors, and integration with ASP.NET Core / .NET 10 APIs.
  Load when creating Angular components, state services, routing, or full-stack integrations.
---

# Modern Angular Skill (Angular 19+)

## 1. Core Principles
1. **Standalone & Modular**: No `NgModule`. Components, directives, and pipes declare dependencies in their own `imports` array.
2. **Signal-First Reactivity**: State flows through Signals (`signal`, `computed`). UI reacts predictably with `ChangeDetectionStrategy.OnPush`.
3. **Seamless .NET Contract**: TypeScript interfaces strictly mirror .NET DTOs. Responses from ASP.NET Core Minimal APIs map directly to typed HTTP observables or signal resources.
4. **Resilient HTTP Communication**: Centralized error interceptor extracting backend `ProblemDetails` errors.

## 2. Patterns

### Modern Standalone Component with Signals & Control Flow
```typescript
import { Component, ChangeDetectionStrategy, inject, signal, input, output } from '@angular/core';
import { CustomerService } from './customer.service';
import { CustomerDto } from './customer.models';

@Component({
  selector: 'app-customer-card',
  standalone: true,
  imports: [],
  changeDetection: ChangeDetectionStrategy.OnPush,
  template: `
    <div class="customer-card">
      <h3>{{ customer().name }}</h3>
      <p>Email: {{ customer().email }}</p>
      
      @if (customer().isActive) {
        <span class="badge active">Active</span>
      } @else {
        <span class="badge inactive">Inactive</span>
      }

      <button (click)="onSelect()">View Details</button>
    </div>
  `
})
export class CustomerCardComponent {
  customer = input.required<CustomerDto>();
  selected = output<string>();

  onSelect() {
    this.selected.emit(this.customer().id);
  }
}
```

### Typed HTTP Service Connecting to .NET 10 Backend
```typescript
import { Injectable, inject } from '@angular/core';
import { HttpClient } from '@angular/common/http';
import { Observable } from 'rxjs';
import { CustomerDto, CreateCustomerRequest } from './customer.models';

@Injectable({
  providedIn: 'root'
})
export class CustomerService {
  private readonly http = inject(HttpClient);
  private readonly baseUrl = '/api/customers';

  getAll(): Observable<CustomerDto[]> {
    return this.http.get<CustomerDto[]>(this.baseUrl);
  }

  getById(id: string): Observable<CustomerDto> {
    return this.http.get<CustomerDto>(`${this.baseUrl}/${id}`);
  }

  create(request: CreateCustomerRequest): Observable<CustomerDto> {
    return this.http.post<CustomerDto>(this.baseUrl, request);
  }
}
```

### Functional HTTP Interceptor for .NET ProblemDetails
```typescript
import { HttpInterceptorFn, HttpErrorResponse } from '@angular/common/http';
import { catchError, throwError } from 'rxjs';

export interface ProblemDetails {
  title?: string;
  detail?: string;
  status?: number;
  errors?: Record<string, string[]>;
}

export const problemDetailsInterceptor: HttpInterceptorFn = (req, next) => {
  return next(req).pipe(
    catchError((error: HttpErrorResponse) => {
      if (error.error && typeof error.error === 'object') {
        const problem = error.error as ProblemDetails;
        console.error(`Backend Problem [${problem.status}]: ${problem.title ?? error.message}`, problem.detail);
      }
      return throwError(() => error);
    })
  );
};
```

## 3. Anti-Patterns
```typescript
// BAD: Legacy NgModule, @Input decorators, *ngIf structural directives
@Component({
  selector: 'app-legacy',
  template: '<div *ngIf="isLoaded">{{ data }}</div>'
})
export class LegacyComponent {
  @Input() data: string = '';
}

// GOOD: Standalone, input() signal, @if native control flow
@Component({
  selector: 'app-modern',
  standalone: true,
  changeDetection: ChangeDetectionStrategy.OnPush,
  template: '@if (isLoaded()) { <div>{{ data() }}</div> }'
})
export class ModernComponent {
  data = input<string>('');
  isLoaded = signal(true);
}
```

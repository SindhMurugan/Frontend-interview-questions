# Angular Advanced Concepts – Interview Notes

---

## 1. Injectables: Levels
In Angular, services (classes decorated with `@Injectable`) can be provided at different levels. This controls the **lifetime** and **scope** of the service.

- **root**: Singleton for the entire app.
- **platform**: Shared across multiple Angular apps running in the same page.
- **any**: New instance per lazy-loaded module.
- **module / component providers array**: Scoped only to that injector tree.

```ts
@Injectable({ providedIn: 'root' })
export class UserService {}
```

👉 In interviews, emphasize **scoping** and **memory optimization**. Example: heavy services can be scoped to feature modules instead of `root`.

---

## 2. Async Pipe – Drawbacks
`async` pipe simplifies handling Observables/Promises in templates.

```html
<p>{{ user$ | async }}</p>
```

**Drawbacks:**
- Creates multiple subscriptions if used at multiple places in the same template.
- Limited control over subscription (e.g., no retry, catchError inside template).
- Debugging is harder.

👉 Interview Tip: Suggest combining with `shareReplay` or using `*ngIf="obs$ | async as data"` to avoid multiple subscriptions.

---

## 3. Parent Mutation with OnPush
When using `ChangeDetectionStrategy.OnPush`, Angular only checks for **reference changes**.

```ts
@Component({
  selector: 'child',
  template: `{{ user.first_name }} {{ user.last_name }}`,
  changeDetection: ChangeDetectionStrategy.OnPush
})
export class ChildComponent {
  @Input() user!: { first_name: string; last_name: string };
}
```

If parent passes reference and you **mutate** (`delete user.last_name`), change detection may **not run**, because the reference did not change.

👉 Safe practice: Always create new objects:
```ts
this.user = { ...this.user, last_name: undefined };
```

---

## 4. Tokens in Angular (Auth Token)
Tokens are used with DI to inject values/services.

```ts
export const AUTH_TOKEN = new InjectionToken<string>('AuthToken');

@NgModule({
  providers: [{ provide: AUTH_TOKEN, useValue: 'xyz-123' }]
})
export class AppModule {}

constructor(@Inject(AUTH_TOKEN) private token: string) {}
```

👉 Interview Tip: Mention use case for environment configs, feature flags, and injecting non-class values.

---

## 5. HTTP Interceptor – Clone Method
- `HttpRequest` in Angular is **immutable**.
- To modify headers/body, you must **clone**.

```ts
intercept(req: HttpRequest<any>, next: HttpHandler) {
  const cloned = req.clone({ setHeaders: { Authorization: `Bearer ${token}` } });
  return next.handle(cloned);
}
```

**Why clone()?**
- Prevents mutating the original request (ensures immutability).
- Each step in interceptor chain gets a fresh request.

**Other ways to set headers:**
- Directly via `HttpClient.get(url, { headers })`.
- Global `HttpBackend` customization.

👉 Explain **immutability + pipeline safety** in interview.

---

## 6. Zone.js, OnPush & Manual Change Detection
Angular uses **Zone.js** to patch async APIs (setTimeout, promises).
This lets Angular know when to trigger CD.

- `OnPush`: Skips CD unless input changes, event emitted, or `markForCheck` called.
- Manual CD:
```ts
constructor(private cd: ChangeDetectorRef) {}

ngOnInit() {
  setTimeout(() => {
    this.cd.detectChanges();
  });
}
```

👉 Interview: highlight **performance gain** with OnPush + manual CD.

---

## 7. ngZone – Run Task Outside Angular & Re-Enter
- `runOutsideAngular`: Avoids triggering CD unnecessarily (good for performance-heavy loops).
- `run`: Brings execution back inside Angular to update UI.

```ts
constructor(private ngZone: NgZone) {}

ngOnInit() {
  this.ngZone.runOutsideAngular(() => {
    setTimeout(() => {
      this.ngZone.run(() => console.log('Back in Angular'));
    });
  });
}
```

---

## 8. Detecting Object Changes & Monitoring Unchanged
Tracking changes in large objects:

- Use `trackBy` in `*ngFor`:
```html
<li *ngFor="let item of items; trackBy: trackByFn">{{ item.name }}</li>
```
```ts
trackByFn(index: number, item: any) { return item.id; }
```

- Deep change detection: Use immutable updates (`...spread`) + OnPush.
- External libraries: `immer`, `ngrx/entity`.

👉 Interview: Mention **object diffing is expensive**, Angular prefers reference checks.

---

## 9. JS Comparison Operators Under the Hood
- `==` → Abstract Equality (performs coercion).
- `===` → Strict Equality (no coercion).

```js
1 == '1'   // true (coercion)
1 === '1'  // false (different types)
```

👉 Trick Example:
```js
[1,2] == '1,2' // true → Array gets toString() → '1,2'
```

---

## 10. Angular Material: What & Why
Angular Material = UI Component library following Material Design.
- Provides prebuilt, accessible, responsive components.
- Saves development time.
- Works well with Angular CDK.

```ts
import { MatButtonModule } from '@angular/material/button';
```

👉 In interview: Mention **accessibility + responsiveness**.

---

## 11. forwardRef & Use Case
Used when a class reference is not yet defined.

```ts
@Injectable()
class AService {
  constructor(@Inject(forwardRef(() => BService)) private b: BService) {}
}
```

👉 Useful for **circular dependencies**.

---

## 12. ViewContainerRef & Use Case
Represents a container for inserting views dynamically.

```ts
constructor(private vcr: ViewContainerRef, private resolver: ComponentFactoryResolver) {}

create() {
  const compFactory = this.resolver.resolveComponentFactory(MyComponent);
  this.vcr.createComponent(compFactory);
}
```

👉 Use case: Dynamic component loading (dialogs, dashboards, plugins).

---

## 13. APP_INITIALIZER & Use Case
Runs code before app bootstrap.

```ts
export function initFactory(config: ConfigService) {
  return () => config.load();
}

providers: [
  { provide: APP_INITIALIZER, useFactory: initFactory, deps: [ConfigService], multi: true }
]
```

👉 Use case: Load configs, auth state, translations before app loads.

---

## 14. Ivy Engine (EV)
Ivy = Angular’s new rendering engine (since v9).

- Smaller bundles (tree-shaking).
- Incremental compilation.
- Better debugging.
- Dynamic component loading simplified.

👉 Interview: Ivy replaced View Engine → more efficient.

---

## 15. Signals & Computed Signals, Effects, Deferrable Views
- **Signals**: Reactive primitives that store state.
```ts
const counter = signal(0);
counter.set(counter() + 1);
```

- **Computed**: Derived state.
```ts
const double = computed(() => counter() * 2);
```

- **Effect**: Side effects when signals change.
```ts
effect(() => console.log(counter()));
```

- **Deferrable Views** (`@defer`): Lazy load parts of template when condition met.
```html
@defer (when user$ | async) {
  <UserProfile />
}
```

👉 Modern Angular feature (v16+). Improves performance + reactive UX.

---

## 16. Object Changes & Monitoring Unchanged (Large List)
Scenario: object of length 100 → after API call, 20 added.

**Options:**
- Use immutability: replace `items` with a new array.
- Use `trackBy` to efficiently re-render only new ones.

```html
<li *ngFor="let item of items; trackBy: trackById">{{ item.name }}</li>
```
```ts
trackById(index: number, item: Item) { return item.id; }
```

- To monitor changes: keep a diff utility.
```ts
const added = newItems.filter(x => !oldItems.some(o => o.id === x.id));
const unchanged = oldItems.filter(x => newItems.some(o => o.id === x.id));
```

👉 Interview: Stress **trackBy + immutability** for efficiency.

---


# Angular Advanced Concepts – Interview Notes

## Table of Contents
- [Injectables: Levels](#injectables-levels)
- [Async Pipe – Drawbacks](#async-pipe--drawbacks)
- [Parent Mutation with OnPush](#parent-mutation-with-onpush)
- [Tokens in Angular](#tokens-in-angular)
- [HTTP Interceptor & Clone Method](#http-interceptor--clone-method)
- [Zone.js, OnPush & Manual Change Detection, ngZone](#zonejs-onpush--manual-change-detection-ngzone)
- [ngZone: Run Outside Angular & Re-Enter](#ngzone-run-outside-angular--re-enter)
- [Detecting Object Changes & Monitoring Unchanged](#detecting-object-changes--monitoring-unchanged)
- [Changes Outside Zone](#changes-outside-zone)
- [JS Comparison Operators Under the Hood](#js-comparison-operators-under-the-hood)
- [Angular Material: What & Why](#angular-material-what--why)
- [[1,2] == "1,2" Explanation](#12--12-explanation)
- [forwardRef & Use Case](#forwardref--use-case)
- [ViewContainerRef & Use Case](#viewcontainerref--use-case)
- [APP_INITIALIZER & Use Case](#app_initializer--use-case)
- [Ivy Engine](#ivy-engine)
- [Signals, Computed Signals & Deferrable Views](#signals-computed-signals--deferrable-views)
- [Tracking Changed & Unchanged Objects](#tracking-changed--unchanged-objects)

---

## Injectables: Levels
Angular services can be provided at different levels:

- **root** → Singleton across the app.
- **platform** → Shared across multiple Angular apps running on the same page.
- **any** → New instance in each lazy-loaded module.
- **component providers** → Scoped to component and its children.

```ts
@Injectable({ providedIn: 'root' })
export class UserService {}
```

---

## Async Pipe – Drawbacks
- Auto-subscribes and unsubscribes to observables.
- **Drawbacks**:
  - Multiple async pipes in template → multiple subscriptions.
  - Limited to template usage (cannot reuse transformed values in TS).

```html
<!-- Creates 2 subscriptions -->
<div>{{ user$ | async }}</div>
<div>{{ user$ | async }}</div>
```

---

## Parent Mutation with OnPush
- With `ChangeDetectionStrategy.OnPush`, Angular checks only when **input reference changes**.
- Passing object reference → mutating property affects parent too.

```ts
@Component({
  selector: 'child',
  changeDetection: ChangeDetectionStrategy.OnPush,
  template: `{{ user.first_name }} {{ user.last_name }}`
})
export class ChildComponent {
  @Input() user!: { first_name: string; last_name: string };
}

// Parent passes reference
<child [user]="user"></child>

// If child deletes user.last_name → reflected in parent (same reference).
```

---

## Tokens in Angular
Tokens allow **dependency injection of values** instead of classes.

```ts
export const API_URL = new InjectionToken<string>('API_URL');

@NgModule({
  providers: [
    { provide: API_URL, useValue: 'https://api.example.com' }
  ]
})
export class AppModule {}

constructor(@Inject(API_URL) private apiUrl: string) {}
```

---

## HTTP Interceptor & Clone Method
- Interceptors sit between app & backend.
- `HttpRequest` is **immutable** → use `clone()`.
- Other ways to set headers: directly in `HttpClient`, use custom `HttpBackend`.

```ts
@Injectable()
export class AuthInterceptor implements HttpInterceptor {
  intercept(req: HttpRequest<any>, next: HttpHandler): Observable<HttpEvent<any>> {
    const cloned = req.clone({
      setHeaders: { Authorization: `Bearer token` }
    });
    return next.handle(cloned);
  }
}
```

---

## Zone.js, OnPush & Manual Change Detection, ngZone
- **Zone.js** patches async APIs (setTimeout, promises) → triggers CD.
- **OnPush** → skips CD unless:
  - Input reference changes
  - Event fired inside component
  - `markForCheck()`/`detectChanges()` called
- **ngZone** can be used to skip or re-enter Angular CD.

---

## ngZone: Run Outside Angular & Re-Enter
```ts
constructor(private ngZone: NgZone) {}

runTask() {
  this.ngZone.runOutsideAngular(() => {
    setTimeout(() => {
      // Heavy work outside CD
      this.ngZone.run(() => {
        // Re-enter CD
        this.value = 'done';
      });
    }, 1000);
  });
}
```

---

## Detecting Object Changes & Monitoring Unchanged
Angular does **shallow reference checks**.
- To detect deep changes: use `trackBy`, immutability, or `KeyValueDiffers`.

```ts
constructor(private differs: KeyValueDiffers) {}
ngOnInit() {
  this.differ = this.differs.find(this.obj).create();
}
ngDoCheck() {
  const changes = this.differ.diff(this.obj);
  if (changes) console.log('changed');
}
```

---

## Changes Outside Zone
- Use `runOutsideAngular` for performance heavy tasks.
- To apply updates → `ngZone.run()`.

---

## JS Comparison Operators Under the Hood
- `==` → type coercion.
- `===` → strict equality (no coercion).

```js
1 == "1"   // true → coercion
1 === "1"  // false
```

---

## Angular Material: What & Why
- UI Component library by Angular team.
- Provides accessible, responsive, themable UI.
- Saves dev time.

---

## [1,2] == "1,2" Explanation
- `[1,2].toString()` → `'1,2'`.
- JS converts array to string before comparison.

```js
[1,2] == "1,2" // true
```

---

## forwardRef & Use Case
- Helps resolve circular dependency issues in DI.

```ts
@Injectable()
class AService {
  constructor(@Inject(forwardRef(() => BService)) private b: BService) {}
}

@Injectable()
class BService {
  constructor(private a: AService) {}
}
```

---

## ViewContainerRef & Use Case
- Allows dynamic component creation.

```ts
constructor(private vcr: ViewContainerRef) {}

load(comp: Type<any>) {
  this.vcr.createComponent(comp);
}
```

---

## APP_INITIALIZER & Use Case
- Runs functions before app starts.

```ts
export function initApp(config: ConfigService) {
  return () => config.load();
}

@NgModule({
  providers: [
    { provide: APP_INITIALIZER, useFactory: initApp, deps: [ConfigService], multi: true }
  ]
})
```

---

## Ivy Engine
- Angular's new rendering engine.
- Benefits:
  - Smaller bundles
  - Faster builds
  - Better tree-shaking
  - More debugging APIs

---

## Signals, Computed Signals & Deferrable Views
- **Signals**: Reactive primitives for state.
- **Computed**: Derives values from signals.
- **Effect**: Runs side-effects when signals change.
- **@defer**: Lazy-load part of template.

```ts
count = signal(0);
double = computed(() => this.count() * 2);

constructor() {
  effect(() => console.log(this.double()));
}
```

```html
@defer (on viewport) {
  <heavy-component />
}
```

---

## Tracking Changed & Unchanged Objects
Scenario: 100 objects → fetch adds 20 more.
- Use `trackBy` in `*ngFor` for unchanged detection.

```html
<div *ngFor="let item of items; trackBy: trackById">{{ item.name }}</div>
```

```ts
trackById(index: number, item: any) {
  return item.id;
}
```

- Alternative: `KeyValueDiffers`, immutability helpers, or libraries like `immer`.

---


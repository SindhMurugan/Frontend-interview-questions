# Angular  – Interview Notes



## why we need framework we can build out application using js right?
JavaScript is the core language of the web, and yes, we can build applications with plain JavaScript.
But when applications grow bigger, plain JS becomes difficult to manage because we have to manually handle DOM updates, state management, and routing.

That’s where Angular comes in. Angular is a framework built on top of JavaScript/TypeScript that provides a structured way to build scalable and maintainable applications.

For example:

- Angular uses a component-based architecture, so the code is modular and reusable.

- It provides data binding, which automatically syncs data between the UI and logic, so I don’t have to manually manipulate the DOM.

- Features like dependency injection, routing, form handling, and RxJS integration are built-in, which reduces boilerplate code.

- It also supports TypeScript, which helps catch errors early and makes the application more reliable.




---

## Upgrade Angular
- Check Current and Target Versions – I first check the Angular Update Guide (from Angular’s official site) to understand breaking changes and migration steps between my current version and the target version.

- Update Angular CLI & Core – I use Angular CLI commands like ng update @angular/cli @angular/core to upgrade step by step. If I’m multiple versions behind, I upgrade sequentially instead of skipping directly.

- Update Dependencies – I run ng update without arguments to see which libraries (RxJS, Angular Material, etc.) need updates. I also handle TypeScript and Node version compatibility.

- Run & Fix Lint/Tests – After each step, I run linting and unit tests to ensure the upgrade hasn’t broken existing functionality.

- Handle Breaking Changes – If any deprecated APIs or RxJS operators are used, I refactor them as per the migration guide.

- Verify Build & Deployment – Finally, I make sure the app builds successfully and test it in different environments (dev/stage) before releasing.

---

## ng serve vs ng-build
- ng serve is mainly for development.
  It compiles the application in memory and runs a development server.It supports live reload, so whenever I make changes, the app recompiles automatically. Importantly, ng serve does **not generate build files in the dist/ folder`. It just runs the app locally for testing and debugging.
- ng build is used for production or deployment.
  It generates an optimized version of the app in the dist/ folder.



---

## *ngFor vs @for()
*ngFor is directive-based and needs extra code for empty states and tracking.
@for is the new block syntax — it’s cleaner, supports @empty natively, has built-in track by, and is faster due to compiler optimizations.



---

## Why were Signals introduced?

“Angular traditionally relied on zone.js and change detection to track updates, which can become inefficient for complex applications since the entire component tree might re-render even if only a small part of the state changes.

Signals were introduced to provide a reactive, fine-grained state management mechanism without needing external libraries like NgRx or RxJS for simple cases. They make change detection more predictable, efficient, and developer-friendly.



---

## type
- A reactive variable whose value you can read and update.
```tsc
import { signal } from '@angular/core';

count = signal(0);
increment() {
  this.count.update(v => v + 1);
}

```

- Computed Signal (computed)
 Derived from one or more signals. It automatically recalculates when dependencies change.

```tsc
import { computed } from '@angular/core';

doubleCount = computed(() => this.count() * 2);


```
-Effect (effect)

Runs a side effect whenever the dependent signals change (like subscribe in RxJS).
```tsc
import { effect } from '@angular/core';

constructor() {
  effect(() => {
    console.log('Count changed:', this.count());
  });
}

```

---


- Alternative: `KeyValueDiffers`, immutability helpers, or libraries like `immer`.

---
## Improved Version of Your Answer

To measure and improve the performance of an Angular application, I usually start with the Console tab, where I check for any error or warning messages. Fixing those ensures the app runs without runtime issues.

Next, I use the Network tab to monitor how many HTTP requests are being made, the response times of APIs, and the size of script bundles. If I see large bundles or slow requests, I work on optimizations like lazy loading or caching.

Then I move to the Performance tab, where I record snapshots while interacting with the app. I check for long JavaScript tasks, dropped frames, and whether the heap memory is cleared properly when I navigate away from a page. If memory isn’t released, it indicates a potential memory leak, which I fix by cleaning up subscriptions and destroying components correctly.

Using Lighthouse, I analyze core web vitals such as First Contentful Paint (FCP), Largest Contentful Paint (LCP), Cumulative Layout Shift (CLS), and Time to Interactive (TTI). These metrics tell me how fast the first element loads, when the main content is visible, and how quickly the app responds to user actions.

By combining Console, Network, Performance, and Lighthouse, I get a complete picture of both runtime errors and user experience, and I apply optimizations wherever needed.


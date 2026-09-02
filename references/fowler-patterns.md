# Martin Fowler Refactoring Patterns & Code Smells

A practical reference catalog based on Martin Fowler's *Refactoring: Improving the Design of Existing Code (2nd ed.)*, adapted for modern TypeScript / React frontend development.

---

## 1. Code Smells & Corresponding Patterns

| Code Smell | Symptoms | Key Refactoring Patterns |
|---|---|---|
| **Mysterious Name** | Variables, functions, or components with names that don't reveal intent (`data`, `temp`, `handle1`) | Change Function / Variable Declaration |
| **Duplicated Code** | Similar or identical logic / JSX in two or more places | Extract Function, Extract Custom Hook |
| **Long Function / Mega Component** | Function or component too long to read in one screen; handles multiple concerns | Extract Function, Extract Sub-component, Extract Hook |
| **Long Parameter List** | Too many function arguments or component props | Introduce Parameter Object, Preserve Whole Object |
| **Global Data / Overloaded Global State** | Mutable module-level variables or unscoped global state that is hard to trace | Encapsulate Variable, Reduce State Scope |
| **Divergent Change** | One module changes for many unrelated reasons (render + API + calculation) | Split Phase, Extract Module, Extract Hook |
| **Shotgun Surgery** | One logical change forces edits in many files | Move Function / Field, Combine Functions into Module |
| **Feature Envy** | A function references another module's data more than its own | Move Function, Extract then Move |
| **Data Clumps** | A group of variables that always travel together (`startDate`, `endDate`, `keyword`) | Introduce Parameter Object, Create Data Class / Type |
| **Primitive Obsession** | Domain concepts expressed as raw strings / numbers / booleans, scattering validation logic | Replace Primitive with Object, Introduce Branded Type |
| **Repeated Switches** | The same `switch` / `if-else` type-dispatch duplicated in multiple places | Replace Conditional with Polymorphism / Lookup Table |
| **Temporary Field** | State or props that are only populated in certain situations | Extract Component / State, Introduce Null Object |
| **Message Chains** | Deep traversal (`a.b.c.d()`) or excessive prop drilling | Hide Delegate, introduce Context / slot pattern |
| **Middle Man** | A wrapper that does nothing but delegate to something else | Remove Middle Man |
| **Refused Bequest** | Subtype ignores most of what a shared interface provides | Replace Subclass with Delegation, Prefer Composition |

---

## 2. Refactoring Pattern Catalog (TypeScript / React guide)

### A. Foundational Refactorings

#### Extract Function
- **Concept**: Identify a code fragment, understand its purpose, extract it into a named function.
- **TS/React**:
  - Inline formatting / computation in JSX → standalone pure utility function.
  - Business logic and state orchestration → `useXxx` custom hook.
  - Oversized component → focused presentational child component.

#### Extract / Inline Variable
- **Concept**: Assign a complex expression to a well-named constant to make intent explicit. Inline trivial temporaries in the reverse direction.
- **TS/React**:
  - `if (order.status === 'PAID' && !order.isCancelled && user.role === 'ADMIN')` → `const canCancelOrder = ...`

#### Change Function Declaration
- **Concept**: Rename a function or reshape its parameter list / return type so intent is unambiguous.
- **TS/React**:
  - `process()` → `calculateDiscountedPrice()`.
  - Positional argument list → structured `Params` interface.

#### Encapsulate Variable
- **Concept**: Wrap direct data access behind a function or hook to centralise mutation points.
- **TS/React**:
  - `localStorage.getItem('token')` scattered everywhere → `useAuthToken()` hook.

#### Introduce Parameter Object
- **Concept**: Bundle a recurring group of arguments into a single object / interface.
- **TS/React**:
  - `(startDate, endDate, keyword, page)` → `(params: SearchFilterParams)`

---

### B. Simplifying Conditional Logic

#### Decompose Conditional
- **Concept**: Extract complex condition checks and their branches into named functions.
- **TS/React**: Split tangled `if-else` render logic into clearly-named helpers or child components.

#### Replace Nested Conditional with Guard Clauses
- **Concept**: Handle special / error cases first with early returns so the happy path is never nested.
- **TS/React**:
  ```tsx
  if (isLoading) return <Spinner />;
  if (error)     return <ErrorMessage error={error} />;
  if (!data)     return null;
  return <MainContent data={data} />;
  ```

#### Replace Conditional with Polymorphism / Lookup Table
- **Concept**: Replace repeated `switch` / `if-else` dispatch with a map or strategy object.
- **TS/React**:
  ```ts
  const STATUS_CONFIG: Record<OrderStatus, { label: string; color: Color }> = {
    PENDING:    { label: 'Pending',    color: 'gray'  },
    PROCESSING: { label: 'Processing', color: 'blue'  },
    COMPLETED:  { label: 'Completed',  color: 'green' },
    CANCELLED:  { label: 'Cancelled',  color: 'red'   },
  };
  ```

---

### C. Moving Features

#### Move Function / Statements
- **Concept**: Move a function to the module it is most closely associated with.
- **TS/React**: Data-transformation utilities defined inside a component file → `utils/` or a domain mapper module if used elsewhere.

#### Replace Inline Code with Function Call
- **Concept**: Replace hand-rolled logic that already exists as a library utility.
- **TS/React**: Custom date formatting → `dayjs(date).format(...)`, custom currency display → `formatCurrency(value)`.

---

### D. Organising Data

#### Replace Derived Variable with Query
- **Concept**: Values that can be fully computed from existing state should not be stored separately.
- **TS/React**: Remove the `useEffect + setState` anti-pattern for derived values; compute inline or with `useMemo`.

#### Split Phase
- **Concept**: When a function does two distinct jobs, separate them with an intermediate data structure.
- **TS/React**: Raw API response (phase 1: parse / normalise DTO) → UI ViewModel (phase 2: shape for display).

---

### E. Indirection Layer Strategies

> *"All problems in computer science can be solved by another level of indirection."* — David Wheeler

Many Fowler refactorings introduce a deliberate indirection layer to reduce coupling and localise change. Key frontend strategies:

#### API Adapter / Gateway Layer
- **Problem**: Server DTO shapes leak directly into UI components; a backend schema change breaks many screens.
- **Solution**: Insert `API Response → DTO Parser / Adapter → UI ViewModel`. UI depends only on the ViewModel.

#### Custom Hook as Business Logic Layer
- **Problem**: A React component mixes render, state, API calls, event handling, and business calculation.
- **Solution**: Extract a `useXxx` hook as an intermediary. Component becomes a pure presentational shell.

#### Lookup Table / Strategy Layer
- **Problem**: Status/type-based branches for styles, labels, and behaviour are scattered across the component.
- **Solution**: Centralise rules in a `Record<Status, Config>` map. Adding a new status requires updating only the map (OCP).

#### Hide Delegate / Facade
- **Problem**: Component traverses deep chains into stores or libraries (`a.b.c.d()`).
- **Solution**: Introduce a thin facade function or context wrapper that exposes only the needed interface, hiding internal complexity.

---

## 3. Pre-Proposal Checklist

Before proposing a refactoring, verify:

1. **Behavior Preservation** — only internal structure changes; observable behavior is identical.
2. **Zero Side-Effect / Backward Compatibility** — existing callers compile and run without modification; public interfaces (signatures, props, return types) are unchanged.
3. **Atomicity** — refactoring steps are small enough to apply (and revert) independently.
4. **Cohesion & Readability** — each module / function has one clear responsibility after the change.
5. **Type Safety** — TypeScript's type system catches regressions at compile time, not runtime.
6. **Project Conventions** — the refactored code matches the existing style (naming, formatting, patterns).

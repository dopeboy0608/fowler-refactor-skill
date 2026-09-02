# fowler-refactor-skill

An agent skill that applies **Martin Fowler's refactoring catalog** to your codebase.

It conducts a focused `grill-me`-style interview to understand what you want to improve, maps the relevant smells to Fowler's patterns, proposes a Before/After plan, and applies changes only after your approval — **guaranteeing zero breaking changes** to existing callers.

---

## Install

```bash
npx skills@latest add dopeboy0608/fowler-refactor-skill
```

Works with **Claude Code**, **OpenCode**, and any agent that supports the [skills.sh](https://skills.sh) format.

---

## Usage

Invoke explicitly in your agent:

```
/fowler-refactor-skill
```

The skill will **not** auto-trigger on casual requests like "clean this up" or "refactor this". Explicit invocation is required.

---

## What it does

1. **Interview** (`grill-me` style) — one focused question at a time with a recommended answer, until target / smell / constraints are clear.
2. **Pattern mapping** — selects the most appropriate pattern(s) from the Fowler catalog (Extract Function, Decompose Conditional, Split Phase, Replace Conditional with Lookup Table, and more).
3. **Proposal report** — presents a structured Before/After design with compatibility guarantees before touching any code.
4. **Feedback loop** — you can request changes to the plan; code is applied only on explicit approval.
5. **Safe application** — type-check / lint verification + formatter applied to modified files.

### Core patterns covered

- **Foundational**: Extract Function, Introduce Parameter Object, Encapsulate Variable, Change Function Declaration
- **Conditionals**: Decompose Conditional, Guard Clauses, Replace Conditional with Lookup Table / Polymorphism
- **Moving features**: Move Function, Split Phase, Replace Inline Code with Function Call
- **Data organisation**: Replace Derived Variable with Query
- **Indirection layers**: API Adapter / Gateway, Custom Hook as Business Layer, Strategy/Lookup Table, Hide Delegate / Facade

---

## Compatibility guarantee

Every refactoring proposed by this skill preserves:

- Public function signatures, component props, and return types
- All existing call sites (zero changes required in callers)
- Observable runtime behaviour

---

## References & Credits

- Martin Fowler, *Refactoring: Improving the Design of Existing Code (2nd Edition)*, Addison-Wesley Professional, 2018.
- Pattern catalog: [refactoring.com](https://refactoring.com/catalog/)
- Interview interaction pattern inspired by Matt Pocock's [`/grill-me`](https://github.com/mattpocock/skills) skill (MIT License).

> This project is an independent community tool and is not officially affiliated with or endorsed by Martin Fowler or Pearson Education.

---

## License

[MIT](./LICENSE) © YongKyu Kim

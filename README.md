# Select Generic Type Inference

Reproduction for [primefaces/primevue#8482](https://github.com/primefaces/primevue/issues/8482).

## The bug

`Select` (and 18 other PrimeVue components) declares all slot parameters as `any`. When you pass a typed `User[]` array to `:options`, the `#option` slot's `option` parameter is still `any` — TypeScript cannot catch property typos or type errors in templates.

## Quick start

```bash
bun install
bun run type-check    # 0 errors — "option.naem" typo goes undetected
```

To apply the fix:

```bash
bun run patch          # Apply generic type inference patch
bun run type-check     # TS2339: Property 'naem' does not exist on type 'User'
bun run unpatch        # Revert patch
```

## What this demonstrates

`src/App.vue` has a `<Select>` with typed `User[]` options and an intentional typo — `option.naem` instead of `option.name`.

**Without patch:** `vue-tsc` reports 0 errors. The typo is invisible to the type checker because `option` is `any`.

**With patch:** `vue-tsc` catches the typo: `Property 'naem' does not exist on type 'User'`. The generic constructor lets TypeScript infer `T = User` from `:options="users"` and flow it through to slot parameters.

## Root cause

PrimeVue's `DefineComponent` uses a no-arg constructor (`new ()`). When `vue-tsc` generates `new Select({ options: users })`, TypeScript has no constructor parameter to infer `T` from — it falls back to the default `any`.

The fix replaces this with a generic constructor: `new <T>(props: SelectProps<T>)`. Now TypeScript infers `T = User` from the actual prop value.

Toggle with `bun run patch` / `bun run unpatch` (uses bun's `patchedDependencies`).

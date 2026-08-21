---
name: lean4
description: Build, explain, debug, and review Lean 4 programs and proofs, including Mathlib formalizations, tactics, dependent types, Lake and Elan projects, and VS Code setup. Use for .lean files, Lean error messages, theorem proving, or Lean-specific learning; do not use for Lean 3 or unrelated proof systems.
---

# Lean 4

Use the project's pinned toolchain and the bundled references to produce Lean code that elaborates, compiles, and preserves the intended theorem or program semantics.

## Establish the project context

Before changing code:

1. Inspect `lean-toolchain`, `lakefile.toml` or `lakefile.lean`, `lake-manifest.json`, the target file's imports, and nearby declarations.
2. Treat the pinned Lean and Mathlib versions as authoritative. Do not upgrade a toolchain, dependency, or import set unless the request requires it.
3. Follow the repository's namespace, declaration, tactic, formatting, and import conventions.
4. Determine whether the task concerns core Lean, Mathlib, an educational dialect with custom tactics, or project-specific definitions.

## Work against Lean's feedback

Validate names, types, and proof states instead of relying on memory.

- In a Lake project, check a file with `lake env lean path/to/File.lean` and use `lake build` when the change may affect other modules or generated targets.
- Outside a Lake project, use the `lean` executable selected by the active Elan toolchain.
- Use temporary `#check`, `#print`, `#reduce`, `#eval`, or small `example` declarations when they clarify elaboration. Remove diagnostics that do not belong in the final source.
- For theorem discovery, use the local environment first: editor completion and hover information, `#check`, `#find` where available, project source search, and suggestion tactics such as `exact?`, `apply?`, `rw?`, `simp?`, `aesop?`, or `library_search` when imported.
- Read the actual goal state after each meaningful tactic step. Account for implicit arguments, coercions, type-class synthesis, reducibility, namespaces, and active simp lemmas.
- Re-run the narrowest relevant check after every substantive edit, then run the broader project check warranted by the change.

If the expected toolchain is unavailable, report the exact missing command or version and continue with static reasoning only when useful. Do not claim compilation success without a successful check.

## Preserve proof and program integrity

- Do not finish with `sorry`, `admit`, `by?`, an undeclared axiom, or a deliberately weakened statement unless the user explicitly requested a scaffold or assumption.
- Do not use `unsafe`, `partial`, `noncomputable`, `classical`, broad simp attributes, or extra axioms merely to bypass an obligation. Use them only when they match the intended semantics, and explain a non-obvious choice.
- Prefer the smallest proof or implementation that remains readable in the surrounding code. Avoid brittle dependence on incidental simp sets or generated declaration names.
- Keep imports as narrow as the project convention permits. Confirm that a proof still works with its actual imports, not only in a larger scratch environment.
- For recursive definitions, verify termination and distinguish structural recursion, well-founded recursion, partial definitions, and executable code deliberately.
- For mathematical formalization, preserve the exact domain, side conditions, universes, and algebraic structures. Never replace a hard goal with a materially weaker proposition.

## Use the bundled knowledge selectively

The references are a snapshot extracted on 2026-08-21. They are large, so search a likely file for a heading, declaration, tactic, or error name and read only the surrounding section. For example:

```bash
rg -n '^#{1,6} .*Simplifier|synthInstanceFailed' references/language-*.md
```

Use the project toolchain when its behavior differs from an example in the snapshot.

### Reference routing

- Read [references/language-core.md](references/language-core.md) for elaboration, interaction modes, the type system, definitions, namespaces, modules, attributes, type classes, coercions, and axioms.
- Read [references/language-terms-and-tactics.md](references/language-terms-and-tactics.md) for runtime behavior, term syntax, tactic proofs, `simp`, `grind`, and `mvcgen`.
- Read [references/language-monads-and-propositions.md](references/language-monads-and-propositions.md) for functors, monads, `do` notation, and core logical propositions.
- Read [references/language-basic-types-numbers.md](references/language-basic-types-numbers.md) for natural numbers, integers, finite numbers, fixed-width integers, bitvectors, and floating-point numbers.
- Read [references/language-basic-types-text-and-containers.md](references/language-basic-types-text-and-containers.md) for characters, strings, unit, empty, booleans, optional values, tuples, sums, lists, and arrays.
- Read [references/language-basic-types-collections.md](references/language-basic-types-collections.md) for byte arrays, ranges, maps, sets, subtypes, and lazy computations.
- Read [references/language-io-and-iterators.md](references/language-io-and-iterators.md) for IO, files, processes, tasks, threads, mutable references, and iterators.
- Read [references/language-metaprogramming-and-tooling.md](references/language-metaprogramming-and-tooling.md) for notation, macros, syntax extensions, elaborators, custom output, Lake, Elan, validation, release information, and named compiler errors.
- Read [references/functional-programming.md](references/functional-programming.md) for introductory programming, project creation, structures, datatypes, polymorphism, monads and transformers, dependent programming, termination, performance, and executable examples.
- Read [references/theorem-proving.md](references/theorem-proving.md) for dependent type theory, propositions, quantifiers, equality, tactic proofs, inductive types, recursion, structures, type classes, `conv`, axioms, and computation.
- Read [references/mathematics-in-lean.md](references/mathematics-in-lean.md) for Mathlib proofs involving logic, sets, functions, number theory, combinatorics, algebraic structures, groups, rings, linear algebra, topology, calculus, integration, or measure theory.
- Read [references/logic-and-proof.md](references/logic-and-proof.md) for natural deduction, classical reasoning, first-order logic, semantics, sets, relations, functions, induction, combinatorics, infinity, and axiomatic foundations.
- Read [references/mechanics-of-proof.md](references/mechanics-of-proof.md) for beginner-oriented mathematical proof technique, calculational proofs, structured reasoning, induction, number theory, functions, sets, relations, and its explicitly marked custom teaching tactics. Do not assume those custom tactics exist in an ordinary Mathlib project.
- Read [references/logical-verification.md](references/logical-verification.md) for forward and backward proof patterns, inductive predicates, effectful programming, metaprogramming, operational and denotational semantics, Hoare logic, logical foundations, and course exercises.
- Read [references/editor-and-tooling.md](references/editor-and-tooling.md) for installation, Unicode input, VS Code commands, infoview behavior, project and toolchain management, theorem search, setup diagnostics, semantic tokens, LaTeX highlighting, and common setup questions.
- Read [references/learn-portal.md](references/learn-portal.md) when choosing an official learning path or citation format.
- Read [references/catalog.md](references/catalog.md) only when a high-level inventory of the original combined corpus is useful.
- Read [references/source-map.md](references/source-map.md) for provenance, conversion notes, caveats, attribution, and licensing.

## Match the response to the task

For implementation or repair:

1. Make the smallest coherent change.
2. State the command used to validate it and the observed result.
3. Call out any remaining version, import, or environment dependency.

For explanation or teaching:

1. Start from the current goal, hypotheses, and inferred types.
2. Explain why each proof step changes the state as shown.
3. Distinguish kernel-checked facts from tactic automation and from executable evaluation.
4. Prefer a compact working example compatible with the user's imports and level.

For review:

1. Prioritize semantic errors, unproved obligations, unsound assumptions, version mismatches, fragile automation, and avoidable performance problems.
2. Verify suspected failures with the pinned toolchain where possible.
3. Give exact source locations and concrete corrections.

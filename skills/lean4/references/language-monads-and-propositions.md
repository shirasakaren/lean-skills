## Functors, Monads and do -Notation {#manual-functors-monads-and-do--notation}

> 📄 Source: https://lean-lang.org/doc/reference/latest/Functors___-Monads-and--do--Notation/

## 18. Functors, Monads and `do`-Notation

The type classes `[Functor]](#manual-Functor___mk)`, `[Applicative]](#manual-Applicative___mk)`, and `[Monad]](#manual-Monad___mk)` provide fundamental tools for functional programming.An introduction to programming with these abstractions is available in [*Functional Programming in Lean*](https://lean-lang.org/functional_programming_in_lean/functor-applicative-monad.html).
While they are inspired by the concepts of functors and monads in category theory, the versions used for programming are more limited.
The type classes in Lean's standard library represent the concepts as used for programming, rather than the general mathematical definition.

Instances of `[Functor]](#manual-Functor___mk)` allow an operation to be applied consistently throughout some polymorphic context.
Examples include transforming each element of a list by applying a function and creating new `[IO](https://lean-lang.org/doc/reference/latest/IO/Logical-Model/#IO)` actions by arranging for a pure function to be applied to the result of an existing `[IO](https://lean-lang.org/doc/reference/latest/IO/Logical-Model/#IO)` action.
Instances of `[Monad]](#manual-Monad___mk)` allow side effects with data dependencies to be encoded; examples include using a tuple to simulate mutable state, a sum type to simulate exceptions, and representing actual side effects with `[IO](https://lean-lang.org/doc/reference/latest/IO/Logical-Model/#IO)`.
`[Applicative]](#manual-Applicative___mk)` functors occupy a middle ground: like monads, they allow functions computed with effects to be applied to arguments that are computed with effects, but they do not allow sequential data dependencies where the output of an effect forms an input into another effectful operation.

The additional type classes `[Pure]](#manual-Pure___mk)`, `[Bind]](#manual-Bind___mk)`, `[SeqLeft]](#manual-SeqLeft___mk)`, `[SeqRight]](#manual-SeqRight___mk)`, and `[Seq]](#manual-Seq___mk)` capture individual operations from `[Applicative]](#manual-Applicative___mk)` and `[Monad]](#manual-Monad___mk)`, allowing them to be overloaded and used with types that are not necessarily `[Applicative]](#manual-Applicative___mk)` functors or `[Monad]](#manual-Monad___mk)`s.
The `[Alternative]](#manual-Alternative___mk)` type class describes applicative functors that additionally have some notion of failure and recovery.

type class

```lean
[Functor.{u, v}]](#manual-Functor___mk) (f : Type u → Type v) : Type (max (u + 1) v)



[Functor.{u, v}]](#manual-Functor___mk) (f : Type u → Type v) :
  Type (max (u + 1) v)
```

A functor in the sense used in functional programming, which means a function `f : Type u → Type v`
has a way of mapping a function over its contents. This `map` operator is written `<$>`, and
overloaded via `[Functor]](#manual-Functor___mk)` instances.

This `map` function should respect identity and function composition. In other words, for all terms
`v : f α`, it should be the case that:

- `id <$> v = v`
- For all functions `h : β → γ` and `g : α → β`, `(h ∘ g) <$> v = h <$> g <$> v`

While all `[Functor]](#manual-Functor___mk)` instances should live up to these requirements, they are not required to *prove*
that they do. Proofs may be required or provided via the `[LawfulFunctor](https://lean-lang.org/doc/reference/latest/Functors___-Monads-and--do--Notation/Laws/#LawfulFunctor___mk)` class.

Assuming that instances are lawful, this definition corresponds to the category-theoretic notion of
[functor](https://en.wikipedia.org/wiki/Functor) in the special case where the category is the
category of types and functions between them.

Instance Constructor

```lean
[Functor.mk]](#manual-Functor___mk).{u, v}
```

Methods

```lean
map : {α β : Type u} → (α → β) → f α → f β
```

Applies a function inside a functor. This is used to overload the `<$>` operator.

When mapping a constant function, use `[Functor.mapConst]](#manual-Functor___mk)` instead, because it may be more
efficient.

Conventions for notations in identifiers:

- The recommended spelling of `<$>` in identifiers is `map`.

```lean
mapConst : {α β : Type u} → α → f β → f α
```

Mapping a constant function.

Given `a : α` and `v : f β`, `mapConst a v` is equivalent to `(fun _ => a) <$> v`. For some
functors, this can be implemented more efficiently; for all other functors, the default
implementation may be used.

type class

```lean
[Pure.{u, v}]](#manual-Pure___mk) (f : Type u → Type v) : Type (max (u + 1) v)



[Pure.{u, v}]](#manual-Pure___mk) (f : Type u → Type v) :
  Type (max (u + 1) v)
```

The `[pure]](#manual-Pure___mk)` function is overloaded via `[Pure]](#manual-Pure___mk)` instances.

`[Pure]](#manual-Pure___mk)` is typically accessed via `[Monad]](#manual-Monad___mk)` or `[Applicative]](#manual-Applicative___mk)` instances.

Instance Constructor

```lean
[Pure.mk]](#manual-Pure___mk).{u, v}
```

Methods

```lean
pure : {α : Type u} → α → f α
```

Given `a : α`, then `pure a : f α` represents an action that does nothing and returns `a`.

Examples:

- `(pure "hello" : Option String) = some "hello"`
- `(pure "hello" : Except (Array String) String) = Except.ok "hello"`
- `(pure "hello" : StateM Nat String).run 105 = ("hello", 105)`

type class

```lean
[Seq.{u, v}]](#manual-Seq___mk) (f : Type u → Type v) : Type (max (u + 1) v)



[Seq.{u, v}]](#manual-Seq___mk) (f : Type u → Type v) :
  Type (max (u + 1) v)
```

The `<*>` operator is overloaded using the function `[Seq.seq]](#manual-Seq___mk)`.

While `<$>` from the class `[Functor]](#manual-Functor___mk)` allows an ordinary function to be mapped over its contents,
`<*>` allows a function that's “inside” the functor to be applied. When thinking about `f` as
possible side effects, this captures evaluation order: `seq` arranges for the effects that produce
the function to occur prior to those that produce the argument value.

For most applications, `[Applicative]](#manual-Applicative___mk)` or `[Monad]](#manual-Monad___mk)` should be used rather than `[Seq]](#manual-Seq___mk)` itself.

Instance Constructor

```lean
[Seq.mk]](#manual-Seq___mk).{u, v}
```

Methods

```lean
seq : {α β : Type u} → f (α → β) → ([Unit](https://lean-lang.org/doc/reference/latest/Basic-Types/The-Unit-Type/#Unit) → f α) → f β
```

The implementation of the `<*>` operator.

In a monad, `mf <*> mx` is the same as `[do](https://lean-lang.org/doc/reference/latest/Functors___-Monads-and--do--Notation/Syntax/#Lean___Parser___Term___do) let f ← mf; x ← mx; pure (f x)`: it evaluates the
function first, then the argument, and applies one to the other.

To avoid surprising evaluation semantics, `mx` is taken "lazily", using a `[Unit](https://lean-lang.org/doc/reference/latest/Basic-Types/The-Unit-Type/#Unit) → f α` function.

Conventions for notations in identifiers:

- The recommended spelling of `<*>` in identifiers is `seq`.

type class

```lean
[SeqLeft.{u, v}]](#manual-SeqLeft___mk) (f : Type u → Type v) : Type (max (u + 1) v)



[SeqLeft.{u, v}]](#manual-SeqLeft___mk) (f : Type u → Type v) :
  Type (max (u + 1) v)
```

The `<*` operator is overloaded using `seqLeft`.

When thinking about `f` as potential side effects, `<*` evaluates first the left and then the right
argument for their side effects, discarding the value of the right argument and returning the value
of the left argument.

For most applications, `[Applicative]](#manual-Applicative___mk)` or `[Monad]](#manual-Monad___mk)` should be used rather than `[SeqLeft]](#manual-SeqLeft___mk)` itself.

Instance Constructor

```lean
[SeqLeft.mk]](#manual-SeqLeft___mk).{u, v}
```

Methods

```lean
seqLeft : {α β : Type u} → f α → ([Unit](https://lean-lang.org/doc/reference/latest/Basic-Types/The-Unit-Type/#Unit) → f β) → f α
```

Sequences the effects of two terms, discarding the value of the second. This function is usually
invoked via the `<*` operator.

Given `x : f α` and `y : f β`, `x <* y` runs `x`, then runs `y`, and finally returns the result of
`x`.

The evaluation of the second argument is delayed by wrapping it in a function, enabling
“short-circuiting” behavior from `f`.

Conventions for notations in identifiers:

- The recommended spelling of `<*` in identifiers is `seqLeft`.

type class

```lean
[SeqRight.{u, v}]](#manual-SeqRight___mk) (f : Type u → Type v) : Type (max (u + 1) v)



[SeqRight.{u, v}]](#manual-SeqRight___mk) (f : Type u → Type v) :
  Type (max (u + 1) v)
```

The `*>` operator is overloaded using `seqRight`.

When thinking about `f` as potential side effects, `*>` evaluates first the left and then the right
argument for their side effects, discarding the value of the left argument and returning the value
of the right argument.

For most applications, `[Applicative]](#manual-Applicative___mk)` or `[Monad]](#manual-Monad___mk)` should be used rather than `[SeqRight]](#manual-SeqRight___mk)` itself.

Instance Constructor

```lean
[SeqRight.mk]](#manual-SeqRight___mk).{u, v}
```

Methods

```lean
seqRight : {α β : Type u} → f α → ([Unit](https://lean-lang.org/doc/reference/latest/Basic-Types/The-Unit-Type/#Unit) → f β) → f β
```

Sequences the effects of two terms, discarding the value of the first. This function is usually
invoked via the `*>` operator.

Given `x : f α` and `y : f β`, `x *> y` runs `x`, then runs `y`, and finally returns the result of
`y`.

The evaluation of the second argument is delayed by wrapping it in a function, enabling
“short-circuiting” behavior from `f`.

Conventions for notations in identifiers:

- The recommended spelling of `*>` in identifiers is `seqRight`.

type class

```lean
[Applicative.{u, v}]](#manual-Applicative___mk) (f : Type u → Type v) : Type (max (u + 1) v)



[Applicative.{u, v}]](#manual-Applicative___mk) (f : Type u → Type v) :
  Type (max (u + 1) v)
```

An [applicative functor](https://lean-lang.org/doc/reference/4.34.0-rc1/find/?domain=Verso.Genre.Manual.section&name=monads-and-do) is more powerful than a `[Functor]](#manual-Functor___mk)`, but
less powerful than a `[Monad]](#manual-Monad___mk)`.

Applicative functors capture sequencing of effects with the `<*>` operator, overloaded as `seq`, but
not data-dependent effects. The results of earlier computations cannot be used to control later
effects.

Applicative functors should satisfy four laws. Instances of `[Applicative]](#manual-Applicative___mk)` are not required to prove
that they satisfy these laws, which are part of the `[LawfulApplicative](https://lean-lang.org/doc/reference/latest/Functors___-Monads-and--do--Notation/Laws/#LawfulApplicative___mk)` class.

Instance Constructor

```lean
[Applicative.mk]](#manual-Applicative___mk).{u, v}
```

Extends

- `[Functor]](#manual-Functor___mk) f`
- `[Pure]](#manual-Pure___mk) f`
- `[Seq]](#manual-Seq___mk) f`
- `[SeqLeft]](#manual-SeqLeft___mk) f`
- `[SeqRight]](#manual-SeqRight___mk) f`

Methods

```lean
map : {α β : Type u} → (α → β) → f α → f β
```

Inherited from

1. `[Functor]](#manual-Functor___mk) f`
2. `[Pure]](#manual-Pure___mk) f`
3. `[Seq]](#manual-Seq___mk) f`
4. `[SeqLeft]](#manual-SeqLeft___mk) f`
5. `[SeqRight]](#manual-SeqRight___mk) f`

```lean
mapConst : {α β : Type u} → α → f β → f α
```

Inherited from

1. `[Functor]](#manual-Functor___mk) f`
2. `[Pure]](#manual-Pure___mk) f`
3. `[Seq]](#manual-Seq___mk) f`
4. `[SeqLeft]](#manual-SeqLeft___mk) f`
5. `[SeqRight]](#manual-SeqRight___mk) f`

```lean
pure : {α : Type u} → α → f α
```

Inherited from

1. `[Functor]](#manual-Functor___mk) f`
2. `[Pure]](#manual-Pure___mk) f`
3. `[Seq]](#manual-Seq___mk) f`
4. `[SeqLeft]](#manual-SeqLeft___mk) f`
5. `[SeqRight]](#manual-SeqRight___mk) f`

```lean
seq : {α β : Type u} → f (α → β) → ([Unit](https://lean-lang.org/doc/reference/latest/Basic-Types/The-Unit-Type/#Unit) → f α) → f β
```

Inherited from

1. `[Functor]](#manual-Functor___mk) f`
2. `[Pure]](#manual-Pure___mk) f`
3. `[Seq]](#manual-Seq___mk) f`
4. `[SeqLeft]](#manual-SeqLeft___mk) f`
5. `[SeqRight]](#manual-SeqRight___mk) f`

```lean
seqLeft : {α β : Type u} → f α → ([Unit](https://lean-lang.org/doc/reference/latest/Basic-Types/The-Unit-Type/#Unit) → f β) → f α
```

Inherited from

1. `[Functor]](#manual-Functor___mk) f`
2. `[Pure]](#manual-Pure___mk) f`
3. `[Seq]](#manual-Seq___mk) f`
4. `[SeqLeft]](#manual-SeqLeft___mk) f`
5. `[SeqRight]](#manual-SeqRight___mk) f`

```lean
seqRight : {α β : Type u} → f α → ([Unit](https://lean-lang.org/doc/reference/latest/Basic-Types/The-Unit-Type/#Unit) → f β) → f β
```

Inherited from

1. `[Functor]](#manual-Functor___mk) f`
2. `[Pure]](#manual-Pure___mk) f`
3. `[Seq]](#manual-Seq___mk) f`
4. `[SeqLeft]](#manual-SeqLeft___mk) f`
5. `[SeqRight]](#manual-SeqRight___mk) f`

**Example: Lists with Lengths as Applicative Functors**

The structure `[LenList]](#manual-LenList-_LPAR_in-Lists-with-Lengths-as-Applicative-Functors_RPAR_)` pairs a list with a proof that it has the desired length.
As a consequence, its `zipWith` operator doesn't require a fallback in case the lengths of its inputs differ.

```lean
structure LenList (length : [Nat](https://lean-lang.org/doc/reference/latest/Basic-Types/Natural-Numbers/#Nat___zero)) (α : Type u) where
list : [List](https://lean-lang.org/doc/reference/latest/Basic-Types/Linked-Lists/#List___nil) α
lengthOk : list.[length](https://lean-lang.org/doc/reference/latest/Basic-Types/Linked-Lists/#List___length) = length
def LenList.head (xs : [LenList]](#manual-LenList-_LPAR_in-Lists-with-Lengths-as-Applicative-Functors_RPAR_) (n + 1) α) : α :=
xs.[list]](#manual-LenList___list-_LPAR_in-Lists-with-Lengths-as-Applicative-Functors_RPAR_).[head](https://lean-lang.org/doc/reference/latest/Basic-Types/Linked-Lists/#List___head) <| byα:Type uβ:Type un:[Nat](https://lean-lang.org/doc/reference/latest/Basic-Types/Natural-Numbers/#Nat___zero)xs:[LenList]](#manual-LenList-_LPAR_in-Lists-with-Lengths-as-Applicative-Functors_RPAR_) [(]](#manual-HAdd___mk)n [+]](#manual-HAdd___mk) 1[)]](#manual-HAdd___mk) α⊢ xs.[list]](#manual-LenList___list-_LPAR_in-Lists-with-Lengths-as-Applicative-Functors_RPAR_) ≠ [[](https://lean-lang.org/doc/reference/latest/Basic-Types/Linked-Lists/#List___nil)[]](https://lean-lang.org/doc/reference/latest/Basic-Types/Linked-Lists/#List___nil)
[intro]](#manual-intro) hα:Type uβ:Type un:[Nat](https://lean-lang.org/doc/reference/latest/Basic-Types/Natural-Numbers/#Nat___zero)xs:[LenList]](#manual-LenList-_LPAR_in-Lists-with-Lengths-as-Applicative-Functors_RPAR_) [(]](#manual-HAdd___mk)n [+]](#manual-HAdd___mk) 1[)]](#manual-HAdd___mk) αh:xs.[list]](#manual-LenList___list-_LPAR_in-Lists-with-Lengths-as-Applicative-Functors_RPAR_) [=](https://lean-lang.org/doc/reference/latest/Basic-Propositions/Propositional-Equality/#Eq___refl) [[](https://lean-lang.org/doc/reference/latest/Basic-Types/Linked-Lists/#List___nil)[]](https://lean-lang.org/doc/reference/latest/Basic-Types/Linked-Lists/#List___nil)⊢ [False](https://lean-lang.org/doc/reference/latest/Basic-Propositions/Truth/#False)
[cases]](#manual-cases) xsmkα:Type uβ:Type un:[Nat](https://lean-lang.org/doc/reference/latest/Basic-Types/Natural-Numbers/#Nat___zero)list✝:[List](https://lean-lang.org/doc/reference/latest/Basic-Types/Linked-Lists/#List___nil) αlengthOk✝:list✝.[length](https://lean-lang.org/doc/reference/latest/Basic-Types/Linked-Lists/#List___length) [=](https://lean-lang.org/doc/reference/latest/Basic-Propositions/Propositional-Equality/#Eq___refl) n [+]](#manual-HAdd___mk) 1h:{ [list]](#manual-LenList___list-_LPAR_in-Lists-with-Lengths-as-Applicative-Functors_RPAR_) := list✝, [lengthOk]](#manual-LenList___lengthOk-_LPAR_in-Lists-with-Lengths-as-Applicative-Functors_RPAR_) := lengthOk✝ }.[list]](#manual-LenList___list-_LPAR_in-Lists-with-Lengths-as-Applicative-Functors_RPAR_) [=](https://lean-lang.org/doc/reference/latest/Basic-Propositions/Propositional-Equality/#Eq___refl) [[](https://lean-lang.org/doc/reference/latest/Basic-Types/Linked-Lists/#List___nil)[]](https://lean-lang.org/doc/reference/latest/Basic-Types/Linked-Lists/#List___nil)⊢ [False](https://lean-lang.org/doc/reference/latest/Basic-Propositions/Truth/#False)
[simp_all]](#manual-simp_all)mkα:Type uβ:Type un:[Nat](https://lean-lang.org/doc/reference/latest/Basic-Types/Natural-Numbers/#Nat___zero)list✝:[List](https://lean-lang.org/doc/reference/latest/Basic-Types/Linked-Lists/#List___nil) αlengthOk✝:list✝.[length](https://lean-lang.org/doc/reference/latest/Basic-Types/Linked-Lists/#List___length) [=](https://lean-lang.org/doc/reference/latest/Basic-Propositions/Propositional-Equality/#Eq___refl) n [+]](#manual-HAdd___mk) 1h:list✝ [=](https://lean-lang.org/doc/reference/latest/Basic-Propositions/Propositional-Equality/#Eq___refl) [[](https://lean-lang.org/doc/reference/latest/Basic-Types/Linked-Lists/#List___nil)[]](https://lean-lang.org/doc/reference/latest/Basic-Types/Linked-Lists/#List___nil)⊢ [False](https://lean-lang.org/doc/reference/latest/Basic-Propositions/Truth/#False)
[subst_eqs]](#manual-subst_eqs)All goals completed! 🐙
def LenList.tail (xs : [LenList]](#manual-LenList-_LPAR_in-Lists-with-Lengths-as-Applicative-Functors_RPAR_) (n + 1) α) : [LenList]](#manual-LenList-_LPAR_in-Lists-with-Lengths-as-Applicative-Functors_RPAR_) n α :=
[match]](#manual-Lean___Parser___Term___match) xs [with]](#manual-Lean___Parser___Term___match)
| ⟨_ :: xs', _⟩ => ⟨xs', byα:Type uβ:Type un:[Nat](https://lean-lang.org/doc/reference/latest/Basic-Types/Natural-Numbers/#Nat___zero)xs:[LenList]](#manual-LenList-_LPAR_in-Lists-with-Lengths-as-Applicative-Functors_RPAR_) [(]](#manual-HAdd___mk)n [+]](#manual-HAdd___mk) 1[)]](#manual-HAdd___mk) αhead✝:αxs':[List](https://lean-lang.org/doc/reference/latest/Basic-Types/Linked-Lists/#List___nil) αlengthOk✝:[(](https://lean-lang.org/doc/reference/latest/Basic-Types/Linked-Lists/#List___nil)head✝ [::](https://lean-lang.org/doc/reference/latest/Basic-Types/Linked-Lists/#List___nil) xs'[)](https://lean-lang.org/doc/reference/latest/Basic-Types/Linked-Lists/#List___nil).[length](https://lean-lang.org/doc/reference/latest/Basic-Types/Linked-Lists/#List___length) [=](https://lean-lang.org/doc/reference/latest/Basic-Propositions/Propositional-Equality/#Eq___refl) n [+]](#manual-HAdd___mk) 1⊢ xs'.[length](https://lean-lang.org/doc/reference/latest/Basic-Types/Linked-Lists/#List___length) [=](https://lean-lang.org/doc/reference/latest/Basic-Propositions/Propositional-Equality/#Eq___refl) n [simp_all]](#manual-simp_all)All goals completed! 🐙⟩
def LenList.map (f : α → β) (xs : [LenList]](#manual-LenList-_LPAR_in-Lists-with-Lengths-as-Applicative-Functors_RPAR_) n α) : [LenList]](#manual-LenList-_LPAR_in-Lists-with-Lengths-as-Applicative-Functors_RPAR_) n β where
[list]](#manual-LenList___list-_LPAR_in-Lists-with-Lengths-as-Applicative-Functors_RPAR_) := xs.[list]](#manual-LenList___list-_LPAR_in-Lists-with-Lengths-as-Applicative-Functors_RPAR_).[map](https://lean-lang.org/doc/reference/latest/Basic-Types/Linked-Lists/#List___map) f
[lengthOk]](#manual-LenList___lengthOk-_LPAR_in-Lists-with-Lengths-as-Applicative-Functors_RPAR_) := byα:Type uβ:Type un:[Nat](https://lean-lang.org/doc/reference/latest/Basic-Types/Natural-Numbers/#Nat___zero)f:α → βxs:[LenList]](#manual-LenList-_LPAR_in-Lists-with-Lengths-as-Applicative-Functors_RPAR_) n α⊢ ([List.map](https://lean-lang.org/doc/reference/latest/Basic-Types/Linked-Lists/#List___map) f xs.[list]](#manual-LenList___list-_LPAR_in-Lists-with-Lengths-as-Applicative-Functors_RPAR_)).[length](https://lean-lang.org/doc/reference/latest/Basic-Types/Linked-Lists/#List___length) [=](https://lean-lang.org/doc/reference/latest/Basic-Propositions/Propositional-Equality/#Eq___refl) n
[cases]](#manual-cases) xsmkα:Type uβ:Type un:[Nat](https://lean-lang.org/doc/reference/latest/Basic-Types/Natural-Numbers/#Nat___zero)f:α → βlist✝:[List](https://lean-lang.org/doc/reference/latest/Basic-Types/Linked-Lists/#List___nil) αlengthOk✝:list✝.[length](https://lean-lang.org/doc/reference/latest/Basic-Types/Linked-Lists/#List___length) [=](https://lean-lang.org/doc/reference/latest/Basic-Propositions/Propositional-Equality/#Eq___refl) n⊢ ([List.map](https://lean-lang.org/doc/reference/latest/Basic-Types/Linked-Lists/#List___map) f { [list]](#manual-LenList___list-_LPAR_in-Lists-with-Lengths-as-Applicative-Functors_RPAR_) := list✝, [lengthOk]](#manual-LenList___lengthOk-_LPAR_in-Lists-with-Lengths-as-Applicative-Functors_RPAR_) := lengthOk✝ }.[list]](#manual-LenList___list-_LPAR_in-Lists-with-Lengths-as-Applicative-Functors_RPAR_)).[length](https://lean-lang.org/doc/reference/latest/Basic-Types/Linked-Lists/#List___length) [=](https://lean-lang.org/doc/reference/latest/Basic-Propositions/Propositional-Equality/#Eq___refl) n
[simp]](#manual-simp) [List.length_map, *]All goals completed! 🐙
def LenList.zipWith (f : α → β → γ)
(xs : [LenList]](#manual-LenList-_LPAR_in-Lists-with-Lengths-as-Applicative-Functors_RPAR_) n α) (ys : [LenList]](#manual-LenList-_LPAR_in-Lists-with-Lengths-as-Applicative-Functors_RPAR_) n β) :
[LenList]](#manual-LenList-_LPAR_in-Lists-with-Lengths-as-Applicative-Functors_RPAR_) n γ where
[list]](#manual-LenList___list-_LPAR_in-Lists-with-Lengths-as-Applicative-Functors_RPAR_) := xs.[list]](#manual-LenList___list-_LPAR_in-Lists-with-Lengths-as-Applicative-Functors_RPAR_).[zipWith](https://lean-lang.org/doc/reference/latest/Basic-Types/Linked-Lists/#List___zipWith) f ys.[list]](#manual-LenList___list-_LPAR_in-Lists-with-Lengths-as-Applicative-Functors_RPAR_)
[lengthOk]](#manual-LenList___lengthOk-_LPAR_in-Lists-with-Lengths-as-Applicative-Functors_RPAR_) := byα:Type uβ:Type uγ:Type ?u.15n:[Nat](https://lean-lang.org/doc/reference/latest/Basic-Types/Natural-Numbers/#Nat___zero)f:α → β → γxs:[LenList]](#manual-LenList-_LPAR_in-Lists-with-Lengths-as-Applicative-Functors_RPAR_) n αys:[LenList]](#manual-LenList-_LPAR_in-Lists-with-Lengths-as-Applicative-Functors_RPAR_) n β⊢ ([List.zipWith](https://lean-lang.org/doc/reference/latest/Basic-Types/Linked-Lists/#List___zipWith) f xs.[list]](#manual-LenList___list-_LPAR_in-Lists-with-Lengths-as-Applicative-Functors_RPAR_) ys.[list]](#manual-LenList___list-_LPAR_in-Lists-with-Lengths-as-Applicative-Functors_RPAR_)).[length](https://lean-lang.org/doc/reference/latest/Basic-Types/Linked-Lists/#List___length) [=](https://lean-lang.org/doc/reference/latest/Basic-Propositions/Propositional-Equality/#Eq___refl) n
[cases]](#manual-cases) xsmkα:Type uβ:Type uγ:Type ?u.15n:[Nat](https://lean-lang.org/doc/reference/latest/Basic-Types/Natural-Numbers/#Nat___zero)f:α → β → γys:[LenList]](#manual-LenList-_LPAR_in-Lists-with-Lengths-as-Applicative-Functors_RPAR_) n βlist✝:[List](https://lean-lang.org/doc/reference/latest/Basic-Types/Linked-Lists/#List___nil) αlengthOk✝:list✝.[length](https://lean-lang.org/doc/reference/latest/Basic-Types/Linked-Lists/#List___length) [=](https://lean-lang.org/doc/reference/latest/Basic-Propositions/Propositional-Equality/#Eq___refl) n⊢ ([List.zipWith](https://lean-lang.org/doc/reference/latest/Basic-Types/Linked-Lists/#List___zipWith) f { [list]](#manual-LenList___list-_LPAR_in-Lists-with-Lengths-as-Applicative-Functors_RPAR_) := list✝, [lengthOk]](#manual-LenList___lengthOk-_LPAR_in-Lists-with-Lengths-as-Applicative-Functors_RPAR_) := lengthOk✝ }.[list]](#manual-LenList___list-_LPAR_in-Lists-with-Lengths-as-Applicative-Functors_RPAR_) ys.[list]](#manual-LenList___list-_LPAR_in-Lists-with-Lengths-as-Applicative-Functors_RPAR_)).[length](https://lean-lang.org/doc/reference/latest/Basic-Types/Linked-Lists/#List___length) [=](https://lean-lang.org/doc/reference/latest/Basic-Propositions/Propositional-Equality/#Eq___refl) n; [cases]](#manual-cases) ysmk.mkα:Type uβ:Type uγ:Type ?u.15n:[Nat](https://lean-lang.org/doc/reference/latest/Basic-Types/Natural-Numbers/#Nat___zero)f:α → β → γlist✝¹:[List](https://lean-lang.org/doc/reference/latest/Basic-Types/Linked-Lists/#List___nil) αlengthOk✝¹:list✝.[length](https://lean-lang.org/doc/reference/latest/Basic-Types/Linked-Lists/#List___length) [=](https://lean-lang.org/doc/reference/latest/Basic-Propositions/Propositional-Equality/#Eq___refl) nlist✝:[List](https://lean-lang.org/doc/reference/latest/Basic-Types/Linked-Lists/#List___nil) βlengthOk✝:list✝.[length](https://lean-lang.org/doc/reference/latest/Basic-Types/Linked-Lists/#List___length) [=](https://lean-lang.org/doc/reference/latest/Basic-Propositions/Propositional-Equality/#Eq___refl) n⊢ ([List.zipWith](https://lean-lang.org/doc/reference/latest/Basic-Types/Linked-Lists/#List___zipWith) f { [list]](#manual-LenList___list-_LPAR_in-Lists-with-Lengths-as-Applicative-Functors_RPAR_) := list✝¹, [lengthOk]](#manual-LenList___lengthOk-_LPAR_in-Lists-with-Lengths-as-Applicative-Functors_RPAR_) := lengthOk✝¹ }.[list]](#manual-LenList___list-_LPAR_in-Lists-with-Lengths-as-Applicative-Functors_RPAR_) { [list]](#manual-LenList___list-_LPAR_in-Lists-with-Lengths-as-Applicative-Functors_RPAR_) := list✝, [lengthOk]](#manual-LenList___lengthOk-_LPAR_in-Lists-with-Lengths-as-Applicative-Functors_RPAR_) := lengthOk✝ }.[list]](#manual-LenList___list-_LPAR_in-Lists-with-Lengths-as-Applicative-Functors_RPAR_)).[length](https://lean-lang.org/doc/reference/latest/Basic-Types/Linked-Lists/#List___length) [=](https://lean-lang.org/doc/reference/latest/Basic-Propositions/Propositional-Equality/#Eq___refl)
n
[simp]](#manual-simp) [List.length_zipWith, *]All goals completed! 🐙
```

The well-behaved `[Applicative]](#manual-Applicative___mk)` instance applies functions to arguments element-wise.
Because `[Applicative]](#manual-Applicative___mk)` extends `[Functor]](#manual-Functor___mk)`, a separate `[Functor]](#manual-Functor___mk)` instance is not necessary, and `[map]](#manual-Functor___mk)` can be defined as part of the `[Applicative]](#manual-Applicative___mk)` instance.

```lean
instance : [Applicative]](#manual-Applicative___mk) ([LenList]](#manual-LenList-_LPAR_in-Lists-with-Lengths-as-Applicative-Functors_RPAR_) n) where
[map]](#manual-Functor___mk) := [LenList.map]](#manual-LenList___map-_LPAR_in-Lists-with-Lengths-as-Applicative-Functors_RPAR_)
[pure]](#manual-Pure___mk) x := {
[list]](#manual-LenList___list-_LPAR_in-Lists-with-Lengths-as-Applicative-Functors_RPAR_) := [List.replicate](https://lean-lang.org/doc/reference/latest/Basic-Types/Linked-Lists/#List___replicate) n x
[lengthOk]](#manual-LenList___lengthOk-_LPAR_in-Lists-with-Lengths-as-Applicative-Functors_RPAR_) := List.length_replicate
}
[seq]](#manual-Seq___mk) {α β} fs xs := fs.[zipWith]](#manual-LenList___zipWith-_LPAR_in-Lists-with-Lengths-as-Applicative-Functors_RPAR_) (· ·) (xs ())
```

The well-behaved `[Monad]](#manual-Monad___mk)` instance takes the diagonal of the results of applying the function:

```lean
@[simp]
theorem LenList.list_length_eq (xs : [LenList]](#manual-LenList-_LPAR_in-Lists-with-Lengths-as-Applicative-Functors_RPAR_) n α) :
xs.[list]](#manual-LenList___list-_LPAR_in-Lists-with-Lengths-as-Applicative-Functors_RPAR_).[length](https://lean-lang.org/doc/reference/latest/Basic-Types/Linked-Lists/#List___length) = n := byα:Type un:[Nat](https://lean-lang.org/doc/reference/latest/Basic-Types/Natural-Numbers/#Nat___zero)xs:[LenList]](#manual-LenList-_LPAR_in-Lists-with-Lengths-as-Applicative-Functors_RPAR_) n α⊢ xs.[list]](#manual-LenList___list-_LPAR_in-Lists-with-Lengths-as-Applicative-Functors_RPAR_).[length](https://lean-lang.org/doc/reference/latest/Basic-Types/Linked-Lists/#List___length) [=](https://lean-lang.org/doc/reference/latest/Basic-Propositions/Propositional-Equality/#Eq___refl) n
[cases]](#manual-cases) xsmkα:Type un:[Nat](https://lean-lang.org/doc/reference/latest/Basic-Types/Natural-Numbers/#Nat___zero)list✝:[List](https://lean-lang.org/doc/reference/latest/Basic-Types/Linked-Lists/#List___nil) αlengthOk✝:list✝.[length](https://lean-lang.org/doc/reference/latest/Basic-Types/Linked-Lists/#List___length) [=](https://lean-lang.org/doc/reference/latest/Basic-Propositions/Propositional-Equality/#Eq___refl) n⊢ { [list]](#manual-LenList___list-_LPAR_in-Lists-with-Lengths-as-Applicative-Functors_RPAR_) := list✝, [lengthOk]](#manual-LenList___lengthOk-_LPAR_in-Lists-with-Lengths-as-Applicative-Functors_RPAR_) := lengthOk✝ }.[list]](#manual-LenList___list-_LPAR_in-Lists-with-Lengths-as-Applicative-Functors_RPAR_).[length](https://lean-lang.org/doc/reference/latest/Basic-Types/Linked-Lists/#List___length) [=](https://lean-lang.org/doc/reference/latest/Basic-Propositions/Propositional-Equality/#Eq___refl) n
[simp]](#manual-simp) [*]All goals completed! 🐙
def LenList.diagonal (square : [LenList]](#manual-LenList-_LPAR_in-Lists-with-Lengths-as-Applicative-Functors_RPAR_) n ([LenList]](#manual-LenList-_LPAR_in-Lists-with-Lengths-as-Applicative-Functors_RPAR_) n α)) : [LenList]](#manual-LenList-_LPAR_in-Lists-with-Lengths-as-Applicative-Functors_RPAR_) n α :=
[match]](#manual-Lean___Parser___Term___match) n [with]](#manual-Lean___Parser___Term___match)
| 0 => ⟨[], [rfl](https://lean-lang.org/doc/reference/latest/Basic-Propositions/Propositional-Equality/#rfl-next)⟩
| n' + 1 => {
[list]](#manual-LenList___list-_LPAR_in-Lists-with-Lengths-as-Applicative-Functors_RPAR_) :=
square.[head]](#manual-LenList___head-_LPAR_in-Lists-with-Lengths-as-Applicative-Functors_RPAR_).[head]](#manual-LenList___head-_LPAR_in-Lists-with-Lengths-as-Applicative-Functors_RPAR_) :: (square.[tail]](#manual-LenList___tail-_LPAR_in-Lists-with-Lengths-as-Applicative-Functors_RPAR_).[map]](#manual-LenList___map-_LPAR_in-Lists-with-Lengths-as-Applicative-Functors_RPAR_) (·.[tail]](#manual-LenList___tail-_LPAR_in-Lists-with-Lengths-as-Applicative-Functors_RPAR_))).[diagonal]](#manual-LenList___diagonal-_LPAR_in-Lists-with-Lengths-as-Applicative-Functors_RPAR_).[list]](#manual-LenList___list-_LPAR_in-Lists-with-Lengths-as-Applicative-Functors_RPAR_)
[lengthOk]](#manual-LenList___lengthOk-_LPAR_in-Lists-with-Lengths-as-Applicative-Functors_RPAR_) := byα:Type uβ:Type un:[Nat](https://lean-lang.org/doc/reference/latest/Basic-Types/Natural-Numbers/#Nat___zero)n':[Nat](https://lean-lang.org/doc/reference/latest/Basic-Types/Natural-Numbers/#Nat___zero)square:[LenList]](#manual-LenList-_LPAR_in-Lists-with-Lengths-as-Applicative-Functors_RPAR_) [(]](#manual-HAdd___mk)n' [+]](#manual-HAdd___mk) 1[)]](#manual-HAdd___mk) ([LenList]](#manual-LenList-_LPAR_in-Lists-with-Lengths-as-Applicative-Functors_RPAR_) [(]](#manual-HAdd___mk)n' [+]](#manual-HAdd___mk) 1[)]](#manual-HAdd___mk) α)⊢ [(](https://lean-lang.org/doc/reference/latest/Basic-Types/Linked-Lists/#List___nil)square.[head]](#manual-LenList___head-_LPAR_in-Lists-with-Lengths-as-Applicative-Functors_RPAR_).[head]](#manual-LenList___head-_LPAR_in-Lists-with-Lengths-as-Applicative-Functors_RPAR_) [::](https://lean-lang.org/doc/reference/latest/Basic-Types/Linked-Lists/#List___nil) ([diagonal]](#manual-LenList___diagonal-_LPAR_in-Lists-with-Lengths-as-Applicative-Functors_RPAR_) ([map]](#manual-LenList___map-_LPAR_in-Lists-with-Lengths-as-Applicative-Functors_RPAR_) (fun x => x.[tail]](#manual-LenList___tail-_LPAR_in-Lists-with-Lengths-as-Applicative-Functors_RPAR_)) square.[tail]](#manual-LenList___tail-_LPAR_in-Lists-with-Lengths-as-Applicative-Functors_RPAR_))).[list]](#manual-LenList___list-_LPAR_in-Lists-with-Lengths-as-Applicative-Functors_RPAR_)[)](https://lean-lang.org/doc/reference/latest/Basic-Types/Linked-Lists/#List___nil).[length](https://lean-lang.org/doc/reference/latest/Basic-Types/Linked-Lists/#List___length) [=](https://lean-lang.org/doc/reference/latest/Basic-Propositions/Propositional-Equality/#Eq___refl) n' [+]](#manual-HAdd___mk) 1 [simp]](#manual-simp)All goals completed! 🐙
}
```

type class

```lean
[Alternative.{u, v}]](#manual-Alternative___mk) (f : Type u → Type v) : Type (max (u + 1) v)



[Alternative.{u, v}]](#manual-Alternative___mk) (f : Type u → Type v) :
  Type (max (u + 1) v)
```

An `[Alternative]](#manual-Alternative___mk)` functor is an `[Applicative]](#manual-Applicative___mk)` functor that can "fail" or be "empty"
and a binary operation `<|>` that “collects values” or finds the “left-most success”.

Important instances include

- `[Option](https://lean-lang.org/doc/reference/latest/Basic-Types/Optional-Values/#Option___none)`, where `failure := none` and `<|>` returns the left-most `[some](https://lean-lang.org/doc/reference/latest/Basic-Types/Optional-Values/#Option___none)`.
- Parser combinators typically provide an `[Applicative]](#manual-Applicative___mk)` instance for error-handling and
  backtracking.

Error recovery and state can interact subtly. For example, the implementation of `[Alternative]](#manual-Alternative___mk)` for `[OptionT](https://lean-lang.org/doc/reference/latest/Functors___-Monads-and--do--Notation/Varieties-of-Monads/#OptionT) ([StateT](https://lean-lang.org/doc/reference/latest/Functors___-Monads-and--do--Notation/Varieties-of-Monads/#StateT) σ [Id](https://lean-lang.org/doc/reference/latest/Functors___-Monads-and--do--Notation/Varieties-of-Monads/#Id))` keeps modifications made to the state while recovering from failure, while `[StateT](https://lean-lang.org/doc/reference/latest/Functors___-Monads-and--do--Notation/Varieties-of-Monads/#StateT) σ ([OptionT](https://lean-lang.org/doc/reference/latest/Functors___-Monads-and--do--Notation/Varieties-of-Monads/#OptionT) [Id](https://lean-lang.org/doc/reference/latest/Functors___-Monads-and--do--Notation/Varieties-of-Monads/#Id))` discards them.

Instance Constructor

```lean
[Alternative.mk]](#manual-Alternative___mk).{u, v}
```

Extends

- `[Applicative]](#manual-Applicative___mk) f`

Methods

```lean
map : {α β : Type u} → (α → β) → f α → f β
```

Inherited from

1. `[Applicative]](#manual-Applicative___mk) f`

```lean
mapConst : {α β : Type u} → α → f β → f α
```

Inherited from

1. `[Applicative]](#manual-Applicative___mk) f`

```lean
pure : {α : Type u} → α → f α
```

Inherited from

1. `[Applicative]](#manual-Applicative___mk) f`

```lean
seq : {α β : Type u} → f (α → β) → ([Unit](https://lean-lang.org/doc/reference/latest/Basic-Types/The-Unit-Type/#Unit) → f α) → f β
```

Inherited from

1. `[Applicative]](#manual-Applicative___mk) f`

```lean
seqLeft : {α β : Type u} → f α → ([Unit](https://lean-lang.org/doc/reference/latest/Basic-Types/The-Unit-Type/#Unit) → f β) → f α
```

Inherited from

1. `[Applicative]](#manual-Applicative___mk) f`

```lean
seqRight : {α β : Type u} → f α → ([Unit](https://lean-lang.org/doc/reference/latest/Basic-Types/The-Unit-Type/#Unit) → f β) → f β
```

Inherited from

1. `[Applicative]](#manual-Applicative___mk) f`

```lean
failure : {α : Type u} → f α
```

Produces an empty collection or recoverable failure. The `<|>` operator collects values or recovers
from failures. See `[Alternative]](#manual-Alternative___mk)` for more details.

```lean
orElse : {α : Type u} → f α → ([Unit](https://lean-lang.org/doc/reference/latest/Basic-Types/The-Unit-Type/#Unit) → f α) → f α
```

Depending on the `[Alternative]](#manual-Alternative___mk)` instance, collects values or recovers from `failure`s by
returning the leftmost success. Can be written using the `<|>` operator syntax.

type class

```lean
[Bind.{u, v}]](#manual-Bind___mk) (m : Type u → Type v) : Type (max (u + 1) v)



[Bind.{u, v}]](#manual-Bind___mk) (m : Type u → Type v) :
  Type (max (u + 1) v)
```

The `>>=` operator is overloaded via instances of `[bind]](#manual-Bind___mk)`.

`[Bind]](#manual-Bind___mk)` is typically used via `[Monad]](#manual-Monad___mk)`, which extends it.

Instance Constructor

```lean
[Bind.mk]](#manual-Bind___mk).{u, v}
```

Methods

```lean
bind : {α β : Type u} → m α → (α → m β) → m β
```

Sequences two computations, allowing the second to depend on the value computed by the first.

If `x : m α` and `f : α → m β`, then `x >>= f : m β` represents the result of executing `x` to get
a value of type `α` and then passing it to `f`.

Conventions for notations in identifiers:

- The recommended spelling of `>>=` in identifiers is `[bind]](#manual-Bind___mk)`.

type class

```lean
[Monad.{u, v}]](#manual-Monad___mk) (m : Type u → Type v) : Type (max (u + 1) v)



[Monad.{u, v}]](#manual-Monad___mk) (m : Type u → Type v) :
  Type (max (u + 1) v)
```

[Monads](https://en.wikipedia.org/wiki/Monad_(functional_programming)) are an abstraction of
sequential control flow and side effects used in functional programming. Monads allow both
sequencing of effects and data-dependent effects: the values that result from an early step may
influence the effects carried out in a later step.

The `[Monad]](#manual-Monad___mk)` API may be used directly. However, it is most commonly accessed through
[`do`-notation](https://lean-lang.org/doc/reference/4.34.0-rc1/find/?domain=Verso.Genre.Manual.section&name=do-notation).

Most `[Monad]](#manual-Monad___mk)` instances provide implementations of `[pure]](#manual-Pure___mk)` and `[bind]](#manual-Bind___mk)`, and use default implementations
for the other methods inherited from `[Applicative]](#manual-Applicative___mk)`. Monads should satisfy certain laws, but
instances are not required to prove this. An instance of `[LawfulMonad](https://lean-lang.org/doc/reference/latest/Functors___-Monads-and--do--Notation/Laws/#LawfulMonad___mk)` expresses that a given
monad's operations are lawful.

Instance Constructor

```lean
[Monad.mk]](#manual-Monad___mk).{u, v}
```

Extends

- `[Applicative]](#manual-Applicative___mk) m`
- `[Bind]](#manual-Bind___mk) m`

Methods

```lean
map : {α β : Type u} → (α → β) → m α → m β
```

Inherited from

1. `[Applicative]](#manual-Applicative___mk) m`
2. `[Bind]](#manual-Bind___mk) m`

```lean
mapConst : {α β : Type u} → α → m β → m α
```

Inherited from

1. `[Applicative]](#manual-Applicative___mk) m`
2. `[Bind]](#manual-Bind___mk) m`

```lean
pure : {α : Type u} → α → m α
```

Inherited from

1. `[Applicative]](#manual-Applicative___mk) m`
2. `[Bind]](#manual-Bind___mk) m`

```lean
seq : {α β : Type u} → m (α → β) → ([Unit](https://lean-lang.org/doc/reference/latest/Basic-Types/The-Unit-Type/#Unit) → m α) → m β
```

Inherited from

1. `[Applicative]](#manual-Applicative___mk) m`
2. `[Bind]](#manual-Bind___mk) m`

```lean
seqLeft : {α β : Type u} → m α → ([Unit](https://lean-lang.org/doc/reference/latest/Basic-Types/The-Unit-Type/#Unit) → m β) → m α
```

Inherited from

1. `[Applicative]](#manual-Applicative___mk) m`
2. `[Bind]](#manual-Bind___mk) m`

```lean
seqRight : {α β : Type u} → m α → ([Unit](https://lean-lang.org/doc/reference/latest/Basic-Types/The-Unit-Type/#Unit) → m β) → m β
```

Inherited from

1. `[Applicative]](#manual-Applicative___mk) m`
2. `[Bind]](#manual-Bind___mk) m`

```lean
bind : {α β : Type u} → m α → (α → m β) → m β
```

Inherited from

1. `[Applicative]](#manual-Applicative___mk) m`
2. `[Bind]](#manual-Bind___mk) m`

1. [18.1. Laws](https://lean-lang.org/doc/reference/latest/Functors___-Monads-and--do--Notation/Laws/#monad-laws)
2. [18.2. Lifting Monads](https://lean-lang.org/doc/reference/latest/Functors___-Monads-and--do--Notation/Lifting-Monads/#lifting-monads)
3. [18.3. Syntax](https://lean-lang.org/doc/reference/latest/Functors___-Monads-and--do--Notation/Syntax/#The-Lean-Language-Reference--Functors___-Monads-and--do--Notation--Syntax)
4. [18.4. API Reference](https://lean-lang.org/doc/reference/latest/Functors___-Monads-and--do--Notation/API-Reference/#The-Lean-Language-Reference--Functors___-Monads-and--do--Notation--API-Reference)
5. [18.5. Varieties of Monads](https://lean-lang.org/doc/reference/latest/Functors___-Monads-and--do--Notation/Varieties-of-Monads/#monad-varieties)

---



## Functors, Monads and do -Notation — 18.1. Laws {#manual-functors-monads-and-do--notation-181-laws}

> 📄 Source: https://lean-lang.org/doc/reference/latest/Functors___-Monads-and--do--Notation/Laws/

Having `[map]](#manual-Functor___mk)`, `[pure]](#manual-Pure___mk)`, `[seq]](#manual-Seq___mk)`, and `[bind]](#manual-Bind___mk)` operators with the appropriate types is not really sufficient to have a functor, applicative functor, or monad.
These operators must additionally satisfy certain axioms, which are often called the *laws* of the type class.

For a functor, the `[map]](#manual-Functor___mk)` operation must preserve identity and function composition. In other words, given a purported `[Functor]](#manual-Functor___mk)` `f`, for all `x`​`:`​`f α`:

- `id <$> x = x`, and
- for all function `g` and `h`, `(h ∘ g) <$> x = h <$> g <$> x`.

Instances that violate these assumptions can be very surprising!
Additionally, because `[Functor]](#manual-Functor___mk)` includes `[mapConst]](#manual-Functor___mk)` to enable instances to provide a more efficient implementation, a lawful functor's `[mapConst]](#manual-Functor___mk)` should be equivalent to its default implementation.

The Lean standard library does not require proofs of these properties in every instance of `[Functor]](#manual-Functor___mk)`.
Nonetheless, if an instance violates them, then it should be considered a bug.
When proofs of these properties are necessary, an instance implicit parameter of type `[LawfulFunctor]](#manual-LawfulFunctor___mk) f` can be used.
The `[LawfulFunctor]](#manual-LawfulFunctor___mk)` class includes the necessary proofs.

type class

```lean
[LawfulFunctor.{u, v}]](#manual-LawfulFunctor___mk) (f : Type u → Type v) [[Functor]](#manual-Functor___mk) f] : Prop



[LawfulFunctor.{u, v}]](#manual-LawfulFunctor___mk) (f : Type u → Type v)
  [[Functor]](#manual-Functor___mk) f] : Prop
```

A functor satisfies the functor laws.

The `[Functor]](#manual-Functor___mk)` class contains the operations of a functor, but does not require that instances
prove they satisfy the laws of a functor. A `[LawfulFunctor]](#manual-LawfulFunctor___mk)` instance includes proofs that the laws
are satisfied. Because `[Functor]](#manual-Functor___mk)` instances may provide optimized implementations of `mapConst`,
`[LawfulFunctor]](#manual-LawfulFunctor___mk)` instances must also prove that the optimized implementation is equivalent to the
standard implementation.

Instance Constructor

```lean
[LawfulFunctor.mk]](#manual-LawfulFunctor___mk).{u, v}
```

Methods

```lean
map_const : ∀ {α β : Type u}, [Functor.mapConst]](#manual-Functor___mk) [=](https://lean-lang.org/doc/reference/latest/Basic-Propositions/Propositional-Equality/#Eq___refl) [Functor.map]](#manual-Functor___mk) [∘]](#manual-Function___comp) [Function.const]](#manual-Function___const) β
```

The `mapConst` implementation is equivalent to the default implementation.

```lean
id_map : ∀ {α : Type u} (x : f α), id [<$>]](#manual-Functor___mk) x [=](https://lean-lang.org/doc/reference/latest/Basic-Propositions/Propositional-Equality/#Eq___refl) x
```

The `map` implementation preserves identity.

```lean
comp_map : ∀ {α β γ : Type u} (g : α → β) (h : β → γ) (x : f α), [(]](#manual-Function___comp)h [∘]](#manual-Function___comp) g[)]](#manual-Function___comp) [<$>]](#manual-Functor___mk) x [=](https://lean-lang.org/doc/reference/latest/Basic-Propositions/Propositional-Equality/#Eq___refl) h [<$>]](#manual-Functor___mk) g [<$>]](#manual-Functor___mk) x
```

The `map` implementation preserves function composition.

In addition to proving that the potentially-optimized `[SeqLeft.seqLeft]](#manual-SeqLeft___mk)` and `[SeqRight.seqRight]](#manual-SeqRight___mk)` operations are equivalent to their default implementations, Applicative functors `[f](https://lean-lang.org/doc/reference/latest/releases/v4.27.0/#f)` must satisfy four laws.

type class

```lean
[LawfulApplicative.{u, v}]](#manual-LawfulApplicative___mk) (f : Type u → Type v) [[Applicative]](#manual-Applicative___mk) f] : Prop



[LawfulApplicative.{u, v}]](#manual-LawfulApplicative___mk)
  (f : Type u → Type v) [[Applicative]](#manual-Applicative___mk) f] :
  Prop
```

An applicative functor satisfies the laws of an applicative functor.

The `[Applicative]](#manual-Applicative___mk)` class contains the operations of an applicative functor, but does not require that
instances prove they satisfy the laws of an applicative functor. A `[LawfulApplicative]](#manual-LawfulApplicative___mk)` instance
includes proofs that the laws are satisfied.

Because `[Applicative]](#manual-Applicative___mk)` instances may provide optimized implementations of `seqLeft` and `seqRight`,
`[LawfulApplicative]](#manual-LawfulApplicative___mk)` instances must also prove that the optimized implementation is equivalent to the
standard implementation.

Instance Constructor

```lean
[LawfulApplicative.mk]](#manual-LawfulApplicative___mk).{u, v}
```

Extends

- `[LawfulFunctor]](#manual-LawfulFunctor___mk) f`

Methods

```lean
map_const : ∀ {α β : Type u}, [Functor.mapConst]](#manual-Functor___mk) [=](https://lean-lang.org/doc/reference/latest/Basic-Propositions/Propositional-Equality/#Eq___refl) [Functor.map]](#manual-Functor___mk) [∘]](#manual-Function___comp) [Function.const]](#manual-Function___const) β
```

Inherited from

1. `[LawfulFunctor]](#manual-LawfulFunctor___mk) f`

```lean
id_map : ∀ {α : Type u} (x : f α), id [<$>]](#manual-Functor___mk) x [=](https://lean-lang.org/doc/reference/latest/Basic-Propositions/Propositional-Equality/#Eq___refl) x
```

Inherited from

1. `[LawfulFunctor]](#manual-LawfulFunctor___mk) f`

```lean
comp_map : ∀ {α β γ : Type u} (g : α → β) (h : β → γ) (x : f α), [(]](#manual-Function___comp)h [∘]](#manual-Function___comp) g[)]](#manual-Function___comp) [<$>]](#manual-Functor___mk) x [=](https://lean-lang.org/doc/reference/latest/Basic-Propositions/Propositional-Equality/#Eq___refl) h [<$>]](#manual-Functor___mk) g [<$>]](#manual-Functor___mk) x
```

Inherited from

1. `[LawfulFunctor]](#manual-LawfulFunctor___mk) f`

```lean
seqLeft_eq : ∀ {α β : Type u} (x : f α) (y : f β), x <* y [=](https://lean-lang.org/doc/reference/latest/Basic-Propositions/Propositional-Equality/#Eq___refl) [Function.const]](#manual-Function___const) β [<$>]](#manual-Functor___mk) x <*> y
```

`seqLeft` is equivalent to the default implementation.

```lean
seqRight_eq : ∀ {α β : Type u} (x : f α) (y : f β), x *> y [=](https://lean-lang.org/doc/reference/latest/Basic-Propositions/Propositional-Equality/#Eq___refl) [Function.const]](#manual-Function___const) α id [<$>]](#manual-Functor___mk) x <*> y
```

`seqRight` is equivalent to the default implementation.

```lean
pure_seq : ∀ {α β : Type u} (g : α → β) (x : f α), [pure]](#manual-Pure___mk) g <*> x [=](https://lean-lang.org/doc/reference/latest/Basic-Propositions/Propositional-Equality/#Eq___refl) g [<$>]](#manual-Functor___mk) x
```

`[pure]](#manual-Pure___mk)` before `seq` is equivalent to `[Functor.map]](#manual-Functor___mk)`.

This means that `[pure]](#manual-Pure___mk)` really is pure when occurring immediately prior to `seq`.

```lean
map_pure : ∀ {α β : Type u} (g : α → β) (x : α), g [<$>]](#manual-Functor___mk) [pure]](#manual-Pure___mk) x [=](https://lean-lang.org/doc/reference/latest/Basic-Propositions/Propositional-Equality/#Eq___refl) [pure]](#manual-Pure___mk) (g x)
```

Mapping a function over the result of `[pure]](#manual-Pure___mk)` is equivalent to applying the function under `[pure]](#manual-Pure___mk)`.

This means that `[pure]](#manual-Pure___mk)` really is pure with respect to `[Functor.map]](#manual-Functor___mk)`.

```lean
seq_pure : ∀ {α β : Type u} (g : f (α → β)) (x : α), g <*> [pure]](#manual-Pure___mk) x [=](https://lean-lang.org/doc/reference/latest/Basic-Propositions/Propositional-Equality/#Eq___refl) (fun h => h x) [<$>]](#manual-Functor___mk) g
```

`[pure]](#manual-Pure___mk)` after `seq` is equivalent to `[Functor.map]](#manual-Functor___mk)`.

This means that `[pure]](#manual-Pure___mk)` really is pure when occurring just after `seq`.

```lean
seq_assoc : ∀ {α β γ : Type u} (x : f α) (g : f (α → β)) (h : f (β → γ)), h <*> (g <*> x) [=](https://lean-lang.org/doc/reference/latest/Basic-Propositions/Propositional-Equality/#Eq___refl) [Function.comp]](#manual-Function___comp) [<$>]](#manual-Functor___mk) h <*> g <*> x
```

`seq` is associative.

Changing the nesting of `seq` calls while maintaining the order of computations results in an
equivalent computation. This means that `seq` is not doing any more than sequencing.

The monad laws specify that `[pure]](#manual-Pure___mk)` followed by `[bind]](#manual-Bind___mk)` should be equivalent to function application (that is, `[pure]](#manual-Pure___mk)` has no effects), that `[bind]](#manual-Bind___mk)` followed by `[pure]](#manual-Pure___mk)` around a function application is equivalent to `[map]](#manual-Functor___mk)`, and that `[bind]](#manual-Bind___mk)` is associative.

type class

```lean
[LawfulMonad.{u, v}]](#manual-LawfulMonad___mk) (m : Type u → Type v) [[Monad]](#manual-Monad___mk) m] : Prop



[LawfulMonad.{u, v}]](#manual-LawfulMonad___mk) (m : Type u → Type v)
  [[Monad]](#manual-Monad___mk) m] : Prop
```

Lawful monads are those that satisfy a certain behavioral specification. While all instances of
`[Monad]](#manual-Monad___mk)` should satisfy these laws, not all implementations are required to prove this.

`[LawfulMonad.mk']](#manual-LawfulMonad___mk___)` is an alternative constructor that contains useful defaults for many fields.

Instance Constructor

```lean
[LawfulMonad.mk]](#manual-LawfulMonad___mk).{u, v}
```

Extends

- `[LawfulApplicative]](#manual-LawfulApplicative___mk) m`

Methods

```lean
map_const : ∀ {α β : Type u}, [Functor.mapConst]](#manual-Functor___mk) [=](https://lean-lang.org/doc/reference/latest/Basic-Propositions/Propositional-Equality/#Eq___refl) [Functor.map]](#manual-Functor___mk) [∘]](#manual-Function___comp) [Function.const]](#manual-Function___const) β
```

Inherited from

1. `[LawfulApplicative]](#manual-LawfulApplicative___mk) m`

```lean
id_map : ∀ {α : Type u} (x : m α), id [<$>]](#manual-Functor___mk) x [=](https://lean-lang.org/doc/reference/latest/Basic-Propositions/Propositional-Equality/#Eq___refl) x
```

Inherited from

1. `[LawfulApplicative]](#manual-LawfulApplicative___mk) m`

```lean
comp_map : ∀ {α β γ : Type u} (g : α → β) (h : β → γ) (x : m α), [(]](#manual-Function___comp)h [∘]](#manual-Function___comp) g[)]](#manual-Function___comp) [<$>]](#manual-Functor___mk) x [=](https://lean-lang.org/doc/reference/latest/Basic-Propositions/Propositional-Equality/#Eq___refl) h [<$>]](#manual-Functor___mk) g [<$>]](#manual-Functor___mk) x
```

Inherited from

1. `[LawfulApplicative]](#manual-LawfulApplicative___mk) m`

```lean
seqLeft_eq : ∀ {α β : Type u} (x : m α) (y : m β), x <* y [=](https://lean-lang.org/doc/reference/latest/Basic-Propositions/Propositional-Equality/#Eq___refl) [Function.const]](#manual-Function___const) β [<$>]](#manual-Functor___mk) x <*> y
```

Inherited from

1. `[LawfulApplicative]](#manual-LawfulApplicative___mk) m`

```lean
seqRight_eq : ∀ {α β : Type u} (x : m α) (y : m β), x *> y [=](https://lean-lang.org/doc/reference/latest/Basic-Propositions/Propositional-Equality/#Eq___refl) [Function.const]](#manual-Function___const) α id [<$>]](#manual-Functor___mk) x <*> y
```

Inherited from

1. `[LawfulApplicative]](#manual-LawfulApplicative___mk) m`

```lean
pure_seq : ∀ {α β : Type u} (g : α → β) (x : m α), [pure]](#manual-Pure___mk) g <*> x [=](https://lean-lang.org/doc/reference/latest/Basic-Propositions/Propositional-Equality/#Eq___refl) g [<$>]](#manual-Functor___mk) x
```

Inherited from

1. `[LawfulApplicative]](#manual-LawfulApplicative___mk) m`

```lean
map_pure : ∀ {α β : Type u} (g : α → β) (x : α), g [<$>]](#manual-Functor___mk) [pure]](#manual-Pure___mk) x [=](https://lean-lang.org/doc/reference/latest/Basic-Propositions/Propositional-Equality/#Eq___refl) [pure]](#manual-Pure___mk) (g x)
```

Inherited from

1. `[LawfulApplicative]](#manual-LawfulApplicative___mk) m`

```lean
seq_pure : ∀ {α β : Type u} (g : m (α → β)) (x : α), g <*> [pure]](#manual-Pure___mk) x [=](https://lean-lang.org/doc/reference/latest/Basic-Propositions/Propositional-Equality/#Eq___refl) (fun h => h x) [<$>]](#manual-Functor___mk) g
```

Inherited from

1. `[LawfulApplicative]](#manual-LawfulApplicative___mk) m`

```lean
seq_assoc : ∀ {α β γ : Type u} (x : m α) (g : m (α → β)) (h : m (β → γ)), h <*> (g <*> x) [=](https://lean-lang.org/doc/reference/latest/Basic-Propositions/Propositional-Equality/#Eq___refl) [Function.comp]](#manual-Function___comp) [<$>]](#manual-Functor___mk) h <*> g <*> x
```

Inherited from

1. `[LawfulApplicative]](#manual-LawfulApplicative___mk) m`

```lean
bind_pure_comp : ∀ {α β : Type u} (f : α → β) (x : m α),
  (do
      let a ← x
      [pure]](#manual-Pure___mk) (f a)) [=](https://lean-lang.org/doc/reference/latest/Basic-Propositions/Propositional-Equality/#Eq___refl)
    f [<$>]](#manual-Functor___mk) x
```

A `[bind]](#manual-Bind___mk)` followed by `[pure]](#manual-Pure___mk)` composed with a function is equivalent to a functorial map.

This means that `[pure]](#manual-Pure___mk)` really is pure after a `[bind]](#manual-Bind___mk)` and has no effects.

```lean
bind_map : ∀ {α β : Type u} (f : m (α → β)) (x : m α),
  (do
      let x_1 ← f
      x_1 [<$>]](#manual-Functor___mk) x) [=](https://lean-lang.org/doc/reference/latest/Basic-Propositions/Propositional-Equality/#Eq___refl)
    f <*> x
```

A `[bind]](#manual-Bind___mk)` followed by a functorial map is equivalent to `[Applicative]](#manual-Applicative___mk)` sequencing.

This means that the effect sequencing from `[Monad]](#manual-Monad___mk)` and `[Applicative]](#manual-Applicative___mk)` are the same.

```lean
pure_bind : ∀ {α β : Type u} (x : α) (f : α → m β), [pure]](#manual-Pure___mk) x [>>=]](#manual-Bind___mk) f [=](https://lean-lang.org/doc/reference/latest/Basic-Propositions/Propositional-Equality/#Eq___refl) f x
```

`[pure]](#manual-Pure___mk)` followed by `[bind]](#manual-Bind___mk)` is equivalent to function application.

This means that `[pure]](#manual-Pure___mk)` really is pure before a `[bind]](#manual-Bind___mk)` and has no effects.

```lean
bind_assoc : ∀ {α β γ : Type u} (x : m α) (f : α → m β) (g : β → m γ), x [>>=]](#manual-Bind___mk) f [>>=]](#manual-Bind___mk) g [=](https://lean-lang.org/doc/reference/latest/Basic-Propositions/Propositional-Equality/#Eq___refl) x [>>=]](#manual-Bind___mk) fun x => f x [>>=]](#manual-Bind___mk) g
```

`[bind]](#manual-Bind___mk)` is associative.

Changing the nesting of `[bind]](#manual-Bind___mk)` calls while maintaining the order of computations results in an
equivalent computation. This means that `[bind]](#manual-Bind___mk)` is not doing more than data-dependent sequencing.

theorem

```lean
[LawfulMonad.mk'.{u, v}]](#manual-LawfulMonad___mk___) (m : Type u → Type v) [[Monad]](#manual-Monad___mk) m]
  (id_map : ∀ {α : Type u} (x : m α), id [<$>]](#manual-Functor___mk) x [=](https://lean-lang.org/doc/reference/latest/Basic-Propositions/Propositional-Equality/#Eq___refl) x)
  (pure_bind :
    ∀ {α β : Type u} (x : α) (f : α → m β), [pure]](#manual-Pure___mk) x [>>=]](#manual-Bind___mk) f [=](https://lean-lang.org/doc/reference/latest/Basic-Propositions/Propositional-Equality/#Eq___refl) f x)
  (bind_assoc :
    ∀ {α β γ : Type u} (x : m α) (f : α → m β) (g : β → m γ),
      x [>>=]](#manual-Bind___mk) f [>>=]](#manual-Bind___mk) g [=](https://lean-lang.org/doc/reference/latest/Basic-Propositions/Propositional-Equality/#Eq___refl) x [>>=]](#manual-Bind___mk) fun x => f x [>>=]](#manual-Bind___mk) g)
  (map_const :
    ∀ {α β : Type u} (x : α) (y : m β),
      [Functor.mapConst]](#manual-Functor___mk) x y [=](https://lean-lang.org/doc/reference/latest/Basic-Propositions/Propositional-Equality/#Eq___refl) [Function.const]](#manual-Function___const) β x [<$>]](#manual-Functor___mk) y := by
    intros; rfl)
  (seqLeft_eq :
    ∀ {α β : Type u} (x : m α) (y : m β),
      x <* y [=](https://lean-lang.org/doc/reference/latest/Basic-Propositions/Propositional-Equality/#Eq___refl) do
        let a ← x
        let _ ← y
        [pure]](#manual-Pure___mk) a := by
    intros; rfl)
  (seqRight_eq :
    ∀ {α β : Type u} (x : m α) (y : m β),
      x *> y [=](https://lean-lang.org/doc/reference/latest/Basic-Propositions/Propositional-Equality/#Eq___refl) do
        let _ ← x
        y := by
    intros; rfl)
  (bind_pure_comp :
    ∀ {α β : Type u} (f : α → β) (x : m α),
      (do
          let y ← x
          [pure]](#manual-Pure___mk) (f y)) [=](https://lean-lang.org/doc/reference/latest/Basic-Propositions/Propositional-Equality/#Eq___refl)
        f [<$>]](#manual-Functor___mk) x := by
    intros; rfl)
  (bind_map :
    ∀ {α β : Type u} (f : m (α → β)) (x : m α),
      (do
          let x_1 ← f
          x_1 [<$>]](#manual-Functor___mk) x) [=](https://lean-lang.org/doc/reference/latest/Basic-Propositions/Propositional-Equality/#Eq___refl)
        f <*> x := by
    intros; rfl) :
  [LawfulMonad]](#manual-LawfulMonad___mk) m



[LawfulMonad.mk'.{u, v}]](#manual-LawfulMonad___mk___)
  (m : Type u → Type v) [[Monad]](#manual-Monad___mk) m]
  (id_map :
    ∀ {α : Type u} (x : m α),
      id [<$>]](#manual-Functor___mk) x [=](https://lean-lang.org/doc/reference/latest/Basic-Propositions/Propositional-Equality/#Eq___refl) x)
  (pure_bind :
    ∀ {α β : Type u} (x : α)
      (f : α → m β), [pure]](#manual-Pure___mk) x [>>=]](#manual-Bind___mk) f [=](https://lean-lang.org/doc/reference/latest/Basic-Propositions/Propositional-Equality/#Eq___refl) f x)
  (bind_assoc :
    ∀ {α β γ : Type u} (x : m α)
      (f : α → m β) (g : β → m γ),
      x [>>=]](#manual-Bind___mk) f [>>=]](#manual-Bind___mk) g [=](https://lean-lang.org/doc/reference/latest/Basic-Propositions/Propositional-Equality/#Eq___refl)
        x [>>=]](#manual-Bind___mk) fun x => f x [>>=]](#manual-Bind___mk) g)
  (map_const :
    ∀ {α β : Type u} (x : α) (y : m β),
      [Functor.mapConst]](#manual-Functor___mk) x y [=](https://lean-lang.org/doc/reference/latest/Basic-Propositions/Propositional-Equality/#Eq___refl)
        [Function.const]](#manual-Function___const) β x [<$>]](#manual-Functor___mk) y := by
    intros; rfl)
  (seqLeft_eq :
    ∀ {α β : Type u} (x : m α) (y : m β),
      x <* y [=](https://lean-lang.org/doc/reference/latest/Basic-Propositions/Propositional-Equality/#Eq___refl) do
        let a ← x
        let _ ← y
        [pure]](#manual-Pure___mk) a := by
    intros; rfl)
  (seqRight_eq :
    ∀ {α β : Type u} (x : m α) (y : m β),
      x *> y [=](https://lean-lang.org/doc/reference/latest/Basic-Propositions/Propositional-Equality/#Eq___refl) do
        let _ ← x
        y := by
    intros; rfl)
  (bind_pure_comp :
    ∀ {α β : Type u} (f : α → β)
      (x : m α),
      (do
          let y ← x
          [pure]](#manual-Pure___mk) (f y)) [=](https://lean-lang.org/doc/reference/latest/Basic-Propositions/Propositional-Equality/#Eq___refl)
        f [<$>]](#manual-Functor___mk) x := by
    intros; rfl)
  (bind_map :
    ∀ {α β : Type u} (f : m (α → β))
      (x : m α),
      (do
          let x_1 ← f
          x_1 [<$>]](#manual-Functor___mk) x) [=](https://lean-lang.org/doc/reference/latest/Basic-Propositions/Propositional-Equality/#Eq___refl)
        f <*> x := by
    intros; rfl) :
  [LawfulMonad]](#manual-LawfulMonad___mk) m
```

An alternative constructor for `[LawfulMonad]](#manual-LawfulMonad___mk)` which has more
defaultable fields in the common case.

---



## Functors, Monads and do -Notation — 18.2. Lifting Monads {#manual-functors-monads-and-do--notation-182-lifting-monads}

> 📄 Source: https://lean-lang.org/doc/reference/latest/Functors___-Monads-and--do--Notation/Lifting-Monads/

When one monad is at least as capable as another, then actions from the latter monad can be used in a context that expects actions from the former.
This is called *lifting* the action from one monad to another.
Lean automatically inserts lifts when they are available; lifts are defined in the `[MonadLift]](#manual-MonadLift___mk)` type class.
Automatic monad lifting is attempted before the general [coercion]](#manual---tech-term-coercion) mechanism.

type class

```lean
[MonadLift.{u, v, w}]](#manual-MonadLift___mk) (m : [semiOutParam]](#manual-semiOutParam) (Type u → Type v))
  (n : Type u → Type w) : Type (max (max (u + 1) v) w)



[MonadLift.{u, v, w}]](#manual-MonadLift___mk)
  (m : [semiOutParam]](#manual-semiOutParam) (Type u → Type v))
  (n : Type u → Type w) :
  Type (max (max (u + 1) v) w)
```

Computations in the monad `m` can be run in the monad `n`. These translations are inserted
automatically by the compiler.

Usually, `n` consists of some number of monad transformers applied to `m`, but this is not
mandatory.

New instances should use this class, `[MonadLift]](#manual-MonadLift___mk)`. Clients that require one monad to be liftable into
another should instead request `[MonadLiftT]](#manual-MonadLiftT___mk)`, which is the reflexive, transitive closure of
`[MonadLift]](#manual-MonadLift___mk)`.

Instance Constructor

```lean
[MonadLift.mk]](#manual-MonadLift___mk).{u, v, w}
```

Methods

```lean
monadLift : {α : Type u} → m α → n α
```

Translates an action from monad `m` into monad `n`.

[Lifting]](#manual---tech-term-lifting) between monads is reflexive and transitive:

- Any monad can run its own actions.
- Lifts from `m` to `m'` and from `m'` to `n` can be composed to yield a lift from `m` to `n`.
  The utility type class `[MonadLiftT]](#manual-MonadLiftT___mk)` constructs lifts via the reflexive and transitive closure of `[MonadLift]](#manual-MonadLift___mk)` instances.
  Users should not define new instances of `[MonadLiftT]](#manual-MonadLiftT___mk)`, but it is useful as an instance implicit parameter to a polymorphic function that needs to run actions from multiple monads in some user-provided monad.

type class

```lean
[MonadLiftT.{u, v, w}]](#manual-MonadLiftT___mk) (m : Type u → Type v) (n : Type u → Type w) :
  Type (max (max (u + 1) v) w)



[MonadLiftT.{u, v, w}]](#manual-MonadLiftT___mk) (m : Type u → Type v)
  (n : Type u → Type w) :
  Type (max (max (u + 1) v) w)
```

Computations in the monad `m` can be run in the monad `n`. These translations are inserted
automatically by the compiler.

Usually, `n` consists of some number of monad transformers applied to `m`, but this is not
mandatory.

This is the reflexive, transitive closure of `[MonadLift]](#manual-MonadLift___mk)`. Clients that require one monad to be
liftable into another should request an instance of `[MonadLiftT]](#manual-MonadLiftT___mk)`. New instances should instead be
defined for `[MonadLift]](#manual-MonadLift___mk)` itself.

Instance Constructor

```lean
[MonadLiftT.mk]](#manual-MonadLiftT___mk).{u, v, w}
```

Methods

```lean
monadLift : {α : Type u} → m α → n α
```

Translates an action from monad `m` into monad `n`.

**Example: Monad Lifts in Function Signatures**

The function `[IO.withStdin](https://lean-lang.org/doc/reference/latest/IO/Files___-File-Handles___-and-Streams/#IO___withStdin)` has the following signature:

```lean
[IO.withStdin](https://lean-lang.org/doc/reference/latest/IO/Files___-File-Handles___-and-Streams/#IO___withStdin).{u} {m : Type → Type u} {α : Type}
[[Monad]](#manual-Monad___mk) m] [[MonadFinally](https://lean-lang.org/doc/reference/latest/Functors___-Monads-and--do--Notation/Varieties-of-Monads/#MonadFinally___mk) m] [[MonadLiftT]](#manual-MonadLiftT___mk) [BaseIO](https://lean-lang.org/doc/reference/latest/IO/Logical-Model/#BaseIO) m]
(h : [IO.FS.Stream](https://lean-lang.org/doc/reference/latest/IO/Files___-File-Handles___-and-Streams/#IO___FS___Stream___mk)) (x : m α) :
m α
```

Because it doesn't require its parameter to precisely be in `[IO](https://lean-lang.org/doc/reference/latest/IO/Logical-Model/#IO)`, it can be used in many monads, and the body does not need to restrict itself to `[IO](https://lean-lang.org/doc/reference/latest/IO/Logical-Model/#IO)`.
The instance implicit parameter `[MonadLiftT]](#manual-MonadLiftT___mk) [BaseIO](https://lean-lang.org/doc/reference/latest/IO/Logical-Model/#BaseIO) m` allows the reflexive transitive closure of `[MonadLift]](#manual-MonadLift___mk)` to be used to assemble the lift.

When a term of type `n β` is expected, but the provided term has type `m α`, and the two types are not definitionally equal, Lean attempts to insert lifts and coercions before reporting an error.
There are the following possibilities:

1. If `m` and `n` can be unified to the same monad, then `α` and `β` are not the same.
   In this case, no monad lifts are necessary, but the value in the monad must be [coerced]](#manual---tech-term-coercion).
   If the appropriate coercion is found, then a call to `Lean.Internal.coeM` is inserted, which has the following signature:

   ```lean
   Lean.Internal.coeM.{u, v} {m : Type u → Type v} {α β : Type u}
   [(a : α) → [CoeT]](#manual-CoeT___mk) α a β] [[Monad]](#manual-Monad___mk) m]
   (x : m α) :
   m β
   ```
2. If `α` and `β` can be unified, then the monads differ.
   In this case, a monad lift is necessary to transform an expression with type `m α` to `n α`.
   If `m` can be lifted to `n` (that is, there is an instance of `[MonadLiftT]](#manual-MonadLiftT___mk) m n`) then a call to `liftM`, which is an alias for `[MonadLiftT.monadLift]](#manual-MonadLiftT___mk)`, is inserted.

   ```lean
   liftM.{u, v, w}
   {m : Type u → Type v} {n : Type u → Type w}
   [self : [MonadLiftT]](#manual-MonadLiftT___mk) m n] {α : Type u} :
   m α → n α
   ```
3. If neither `m` and `n` nor `α` and `β` can be unified, but `m` can be lifted into `n` and `α` can be [coerced]](#manual---tech-term-coercion) to `β`, then a lift and a coercion can be combined.
   This is done by inserting a call to `Lean.Internal.liftCoeM`:

   ```lean
   Lean.Internal.liftCoeM.{u, v, w}
   {m : Type u → Type v} {n : Type u → Type w}
   {α β : Type u}
   [[MonadLiftT]](#manual-MonadLiftT___mk) m n] [(a : α) → [CoeT]](#manual-CoeT___mk) α a β] [[Monad]](#manual-Monad___mk) n]
   (x : m α) :
   n β
   ```

As their names suggest, `Lean.Internal.coeM` and `Lean.Internal.liftCoeM` are implementation details, not part of the public API.
In the resulting terms, occurrences of `Lean.Internal.coeM`, `Lean.Internal.liftCoeM`, and coercions are unfolded.

**Example: Lifting IO Monads**

There is an instance of `[MonadLift]](#manual-MonadLift___mk) [BaseIO](https://lean-lang.org/doc/reference/latest/IO/Logical-Model/#BaseIO) [IO](https://lean-lang.org/doc/reference/latest/IO/Logical-Model/#IO)`, so any `BaseIO` action can be run in `IO` as well:

```lean
def fromBaseIO (act : [BaseIO](https://lean-lang.org/doc/reference/latest/IO/Logical-Model/#BaseIO) α) : [IO](https://lean-lang.org/doc/reference/latest/IO/Logical-Model/#IO) α := act
```

Behind the scenes, `liftM` is inserted:

```lean
[#check]](#manual-Lean___Parser___Command___check) fun {α} (act : [BaseIO](https://lean-lang.org/doc/reference/latest/IO/Logical-Model/#BaseIO) α) => (act : [IO](https://lean-lang.org/doc/reference/latest/IO/Logical-Model/#IO) α)
```

```lean
fun {α} act => liftM act : {α : Type} → [BaseIO](https://lean-lang.org/doc/reference/latest/IO/Logical-Model/#BaseIO) α → [EIO](https://lean-lang.org/doc/reference/latest/IO/Logical-Model/#EIO) [IO.Error](https://lean-lang.org/doc/reference/latest/IO/Logical-Model/#IO___Error___alreadyExists) α
```

**Example: Lifting Transformed Monads**

There are also instances of `[MonadLift]](#manual-MonadLift___mk)` for most of the standard library's [monad transformers](https://lean-lang.org/doc/reference/latest/Functors___-Monads-and--do--Notation/Varieties-of-Monads/#--tech-term-monad-transformer), so base monad actions can be used in transformed monads without additional work.
For example, state monad actions can be lifted across reader and exception transformers, allowing compatible monads to be intermixed freely:

```lean
def incrBy (n : [Nat](https://lean-lang.org/doc/reference/latest/Basic-Types/Natural-Numbers/#Nat___zero)) : [StateM](https://lean-lang.org/doc/reference/latest/Functors___-Monads-and--do--Notation/Varieties-of-Monads/#StateM) [Nat](https://lean-lang.org/doc/reference/latest/Basic-Types/Natural-Numbers/#Nat___zero) [Unit](https://lean-lang.org/doc/reference/latest/Basic-Types/The-Unit-Type/#Unit) := [modify](https://lean-lang.org/doc/reference/latest/Functors___-Monads-and--do--Notation/Varieties-of-Monads/#modify) (· + n)
def incrOrFail : [ReaderT](https://lean-lang.org/doc/reference/latest/Functors___-Monads-and--do--Notation/Varieties-of-Monads/#ReaderT) [Nat](https://lean-lang.org/doc/reference/latest/Basic-Types/Natural-Numbers/#Nat___zero) ([ExceptT](https://lean-lang.org/doc/reference/latest/Functors___-Monads-and--do--Notation/Varieties-of-Monads/#ExceptT) [String](https://lean-lang.org/doc/reference/latest/Basic-Types/Strings/#String___ofByteArray) ([StateM](https://lean-lang.org/doc/reference/latest/Functors___-Monads-and--do--Notation/Varieties-of-Monads/#StateM) [Nat](https://lean-lang.org/doc/reference/latest/Basic-Types/Natural-Numbers/#Nat___zero))) [Unit](https://lean-lang.org/doc/reference/latest/Basic-Types/The-Unit-Type/#Unit) := [do](https://lean-lang.org/doc/reference/latest/Functors___-Monads-and--do--Notation/Syntax/#Lean___Parser___Term___do)
if (← [read](https://lean-lang.org/doc/reference/latest/Functors___-Monads-and--do--Notation/Varieties-of-Monads/#MonadReader___mk)) > 5 then [throw](https://lean-lang.org/doc/reference/latest/Functors___-Monads-and--do--Notation/Varieties-of-Monads/#MonadExcept___mk) "Too much!"
incrBy (← [read](https://lean-lang.org/doc/reference/latest/Functors___-Monads-and--do--Notation/Varieties-of-Monads/#MonadReader___mk))
```

Disabling lifting causes an error:

```lean
set_option [autoLift]](#manual-autoLift) false
def incrBy (n : [Nat](https://lean-lang.org/doc/reference/latest/Basic-Types/Natural-Numbers/#Nat___zero)) : [StateM](https://lean-lang.org/doc/reference/latest/Functors___-Monads-and--do--Notation/Varieties-of-Monads/#StateM) [Nat](https://lean-lang.org/doc/reference/latest/Basic-Types/Natural-Numbers/#Nat___zero) [Unit](https://lean-lang.org/doc/reference/latest/Basic-Types/The-Unit-Type/#Unit) := [modify](https://lean-lang.org/doc/reference/latest/Functors___-Monads-and--do--Notation/Varieties-of-Monads/#modify) (. + n)
def incrOrFail : [ReaderT](https://lean-lang.org/doc/reference/latest/Functors___-Monads-and--do--Notation/Varieties-of-Monads/#ReaderT) [Nat](https://lean-lang.org/doc/reference/latest/Basic-Types/Natural-Numbers/#Nat___zero) ([ExceptT](https://lean-lang.org/doc/reference/latest/Functors___-Monads-and--do--Notation/Varieties-of-Monads/#ExceptT) [String](https://lean-lang.org/doc/reference/latest/Basic-Types/Strings/#String___ofByteArray) ([StateM](https://lean-lang.org/doc/reference/latest/Functors___-Monads-and--do--Notation/Varieties-of-Monads/#StateM) [Nat](https://lean-lang.org/doc/reference/latest/Basic-Types/Natural-Numbers/#Nat___zero))) [Unit](https://lean-lang.org/doc/reference/latest/Basic-Types/The-Unit-Type/#Unit) := [do](https://lean-lang.org/doc/reference/latest/Functors___-Monads-and--do--Notation/Syntax/#Lean___Parser___Term___do)
if (← [read](https://lean-lang.org/doc/reference/latest/Functors___-Monads-and--do--Notation/Varieties-of-Monads/#MonadReader___mk)) > 5 then [throw](https://lean-lang.org/doc/reference/latest/Functors___-Monads-and--do--Notation/Varieties-of-Monads/#MonadExcept___mk) "Too much!"
incrBy (← [read](https://lean-lang.org/doc/reference/latest/Functors___-Monads-and--do--Notation/Varieties-of-Monads/#MonadReader___mk))
```

```lean
Type mismatch
  incrBy __do_lift✝
has type
  [StateM](https://lean-lang.org/doc/reference/latest/Functors___-Monads-and--do--Notation/Varieties-of-Monads/#StateM) [Nat](https://lean-lang.org/doc/reference/latest/Basic-Types/Natural-Numbers/#Nat___zero) [Unit](https://lean-lang.org/doc/reference/latest/Basic-Types/The-Unit-Type/#Unit)
but is expected to have type
  [ReaderT](https://lean-lang.org/doc/reference/latest/Functors___-Monads-and--do--Notation/Varieties-of-Monads/#ReaderT) [Nat](https://lean-lang.org/doc/reference/latest/Basic-Types/Natural-Numbers/#Nat___zero) ([ExceptT](https://lean-lang.org/doc/reference/latest/Functors___-Monads-and--do--Notation/Varieties-of-Monads/#ExceptT) [String](https://lean-lang.org/doc/reference/latest/Basic-Types/Strings/#String___ofByteArray) ([StateM](https://lean-lang.org/doc/reference/latest/Functors___-Monads-and--do--Notation/Varieties-of-Monads/#StateM) [Nat](https://lean-lang.org/doc/reference/latest/Basic-Types/Natural-Numbers/#Nat___zero))) [Unit](https://lean-lang.org/doc/reference/latest/Basic-Types/The-Unit-Type/#Unit)
```

Automatic lifting can be disabled by setting `[autoLift]](#manual-autoLift)` to `[false](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)`.

option

```lean
autoLift
```

Default value: `[true](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)`

Insert monadic lifts (i.e., `liftM` and coercions) when needed.

### 18.2.1. Reversing Lifts {#manual-The-Lean-Language-Reference--Functors___-Monads-and--do--Notation--Lifting-Monads--Reversing-Lifts}

Monad lifting is not always sufficient to combine monads.
Many operations provided by monads are higher order, taking an action *in the same monad* as a parameter.
Even if these operations are lifted to some more powerful monad, their arguments are still restricted to the original monad.

There are two type classes that support this kind of “reverse lifting”: `[MonadFunctor]](#manual-MonadFunctor___mk)` and `[MonadControl]](#manual-MonadControl___mk)`.
An instance of `[MonadFunctor]](#manual-MonadFunctor___mk) m n` explains how to interpret a fully-polymorphic function in `m` into `n`.
This polymorphic function must work for *all* types `α`: it has type `{α : Type u} → m α → n α`.
Such a function can be thought of as one that may have effects, but can't do so based on specific values that are provided.
An instance of `[MonadControl]](#manual-MonadControl___mk) m n` explains how to interpret an arbitrary action from `m` into `n`, while at the same time providing a “reverse interpreter” that allows the `m` action to run `n` actions.

#### 18.2.1.1. Monad Functors {#manual-The-Lean-Language-Reference--Functors___-Monads-and--do--Notation--Lifting-Monads--Reversing-Lifts--Monad-Functors}

type class

```lean
[MonadFunctor.{u, v, w}]](#manual-MonadFunctor___mk) (m : [semiOutParam]](#manual-semiOutParam) (Type u → Type v))
  (n : Type u → Type w) : Type (max (max (u + 1) v) w)



[MonadFunctor.{u, v, w}]](#manual-MonadFunctor___mk)
  (m : [semiOutParam]](#manual-semiOutParam) (Type u → Type v))
  (n : Type u → Type w) :
  Type (max (max (u + 1) v) w)
```

A way to interpret a fully-polymorphic function in `m` into `n`. Such a function can be thought of
as one that may change the effects in `m`, but can't do so based on specific values that are
provided.

Clients of `[MonadFunctor]](#manual-MonadFunctor___mk)` should typically use `[MonadFunctorT]](#manual-MonadFunctorT___mk)`, which is the reflexive, transitive
closure of `[MonadFunctor]](#manual-MonadFunctor___mk)`. New instances should be defined for `[MonadFunctor]](#manual-MonadFunctor___mk).`

Instance Constructor

```lean
[MonadFunctor.mk]](#manual-MonadFunctor___mk).{u, v, w}
```

Methods

```lean
monadMap : {α : Type u} → ({β : Type u} → m β → m β) → n α → n α
```

Lifts a fully-polymorphic transformation of `m` into `n`.

type class

```lean
[MonadFunctorT.{u, v, w}]](#manual-MonadFunctorT___mk) (m : Type u → Type v) (n : Type u → Type w) :
  Type (max (max (u + 1) v) w)



[MonadFunctorT.{u, v, w}]](#manual-MonadFunctorT___mk)
  (m : Type u → Type v)
  (n : Type u → Type w) :
  Type (max (max (u + 1) v) w)
```

A way to interpret a fully-polymorphic function in `m` into `n`. Such a function can be thought of
as one that may change the effects in `m`, but can't do so based on specific values that are
provided.

This is the reflexive, transitive closure of `[MonadFunctor]](#manual-MonadFunctor___mk)`. It automatically chains together
`[MonadFunctor]](#manual-MonadFunctor___mk)` instances as needed. Clients of `[MonadFunctor]](#manual-MonadFunctor___mk)` should typically use `[MonadFunctorT]](#manual-MonadFunctorT___mk)`,
but new instances should be defined for `[MonadFunctor]](#manual-MonadFunctor___mk)`.

Instance Constructor

```lean
[MonadFunctorT.mk]](#manual-MonadFunctorT___mk).{u, v, w}
```

Methods

```lean
monadMap : {α : Type u} → ({β : Type u} → m β → m β) → n α → n α
```

Lifts a fully-polymorphic transformation of `m` into `n`.

#### 18.2.1.2. Reversible Lifting with `MonadControl` {#manual-The-Lean-Language-Reference--Functors___-Monads-and--do--Notation--Lifting-Monads--Reversing-Lifts--Reversible-Lifting-with--MonadControl}

type class

```lean
[MonadControl.{u, v, w}]](#manual-MonadControl___mk) (m : [semiOutParam]](#manual-semiOutParam) (Type u → Type v))
  (n : Type u → Type w) : Type (max (max (u + 1) v) w)



[MonadControl.{u, v, w}]](#manual-MonadControl___mk)
  (m : [semiOutParam]](#manual-semiOutParam) (Type u → Type v))
  (n : Type u → Type w) :
  Type (max (max (u + 1) v) w)
```

A way to lift a computation from one monad to another while providing the lifted computation with a
means of interpreting computations from the outer monad. This provides a means of lifting
higher-order operations automatically.

Clients should typically use `[control]](#manual-control)` or `[controlAt]](#manual-controlAt)`, which request an instance of `[MonadControlT]](#manual-MonadControlT___mk)`:
the reflexive, transitive closure of `[MonadControl]](#manual-MonadControl___mk)`. New instances should be defined for
`[MonadControl]](#manual-MonadControl___mk)` itself.

Instance Constructor

```lean
[MonadControl.mk]](#manual-MonadControl___mk).{u, v, w}
```

Methods

```lean
stM : Type u → Type u
```

A type that can be used to reconstruct both a returned value and any state used by the outer
monad.

```lean
liftWith : {α : Type u} → (({β : Type u} → n β → m ([MonadControl.stM]](#manual-MonadControl___mk) m n β)) → m α) → n α
```

Lifts an action from the inner monad `m` to the outer monad `n`. The inner monad has access to a
reverse lifting operator that can run an `n` action, returning a value and state together.

```lean
restoreM : {α : Type u} → m ([MonadControl.stM]](#manual-MonadControl___mk) m n α) → n α
```

Lifts a monadic action that returns a state and a value in the inner monad to an action in the
outer monad. The extra state information is used to restore the results of effects from the
reverse lift passed to `[liftWith]](#manual-MonadControlT___mk)`'s parameter.

type class

```lean
[MonadControlT.{u, v, w}]](#manual-MonadControlT___mk) (m : Type u → Type v) (n : Type u → Type w) :
  Type (max (max (u + 1) v) w)



[MonadControlT.{u, v, w}]](#manual-MonadControlT___mk)
  (m : Type u → Type v)
  (n : Type u → Type w) :
  Type (max (max (u + 1) v) w)
```

A way to lift a computation from one monad to another while providing the lifted computation with a
means of interpreting computations from the outer monad. This provides a means of lifting
higher-order operations automatically.

Clients should typically use `[control]](#manual-control)` or `[controlAt]](#manual-controlAt)`, which request an instance of `[MonadControlT]](#manual-MonadControlT___mk)`:
the reflexive, transitive closure of `[MonadControl]](#manual-MonadControl___mk)`. New instances should be defined for
`[MonadControl]](#manual-MonadControl___mk)` itself.

Instance Constructor

```lean
[MonadControlT.mk]](#manual-MonadControlT___mk).{u, v, w}
```

Methods

```lean
stM : Type u → Type u
```

A type that can be used to reconstruct both a returned value and any state used by the outer
monad.

```lean
liftWith : {α : Type u} → (({β : Type u} → n β → m ([stM]](#manual-MonadControlT___mk) m n β)) → m α) → n α
```

Lifts an action from the inner monad `m` to the outer monad `n`. The inner monad has access to a
reverse lifting operator that can run an `n` action, returning a value and state together.

```lean
restoreM : {α : Type u} → [stM]](#manual-MonadControlT___mk) m n α → n α
```

Lifts a monadic action that returns a state and a value in the inner monad to an action in the
outer monad. The extra state information is used to restore the results of effects from the
reverse lift passed to `[liftWith]](#manual-MonadControlT___mk)`'s parameter.

def

```lean
[control.{u, v, w}]](#manual-control) {m : Type u → Type v} {n : Type u → Type w}
  [[MonadControlT]](#manual-MonadControlT___mk) m n] [[Bind]](#manual-Bind___mk) n] {α : Type u}
  (f : ({β : Type u} → n β → m ([stM]](#manual-MonadControlT___mk) m n β)) → m ([stM]](#manual-MonadControlT___mk) m n α)) : n α



[control.{u, v, w}]](#manual-control) {m : Type u → Type v}
  {n : Type u → Type w}
  [[MonadControlT]](#manual-MonadControlT___mk) m n] [[Bind]](#manual-Bind___mk) n]
  {α : Type u}
  (f :
    ({β : Type u} → n β → m ([stM]](#manual-MonadControlT___mk) m n β)) →
      m ([stM]](#manual-MonadControlT___mk) m n α)) :
  n α
```

Lifts an operation from an inner monad to an outer monad, providing it with a reverse lifting
operator that allows outer monad computations to be run in the inner monad. The lifted operation is
required to return extra information that is required in order to reconstruct the reverse lift's
effects in the outer monad; this extra information is determined by `[stM]](#manual-MonadControlT___mk)`.

This function takes the inner monad as an implicit parameter. Use `[controlAt]](#manual-controlAt)` to specify it
explicitly.

def

```lean
[controlAt.{u, v, w}]](#manual-controlAt) (m : Type u → Type v) {n : Type u → Type w}
  [[MonadControlT]](#manual-MonadControlT___mk) m n] [[Bind]](#manual-Bind___mk) n] {α : Type u}
  (f : ({β : Type u} → n β → m ([stM]](#manual-MonadControlT___mk) m n β)) → m ([stM]](#manual-MonadControlT___mk) m n α)) : n α



[controlAt.{u, v, w}]](#manual-controlAt) (m : Type u → Type v)
  {n : Type u → Type w}
  [[MonadControlT]](#manual-MonadControlT___mk) m n] [[Bind]](#manual-Bind___mk) n]
  {α : Type u}
  (f :
    ({β : Type u} → n β → m ([stM]](#manual-MonadControlT___mk) m n β)) →
      m ([stM]](#manual-MonadControlT___mk) m n α)) :
  n α
```

Lifts an operation from an inner monad to an outer monad, providing it with a reverse lifting
operator that allows outer monad computations to be run in the inner monad. The lifted operation is
required to return extra information that is required in order to reconstruct the reverse lift's
effects in the outer monad; this extra information is determined by `[stM]](#manual-MonadControlT___mk)`.

This function takes the inner monad as an explicit parameter. Use `[control]](#manual-control)` to infer the monad.

**Example: Exceptions and Lifting**

One example is `[Except.tryCatch](https://lean-lang.org/doc/reference/latest/Functors___-Monads-and--do--Notation/Varieties-of-Monads/#Except___tryCatch)`:

```lean
[Except.tryCatch](https://lean-lang.org/doc/reference/latest/Functors___-Monads-and--do--Notation/Varieties-of-Monads/#Except___tryCatch).{u, v} {ε : Type u} {α : Type v}
(ma : [Except](https://lean-lang.org/doc/reference/latest/Functors___-Monads-and--do--Notation/Varieties-of-Monads/#Except___error) ε α) (handle : ε → [Except](https://lean-lang.org/doc/reference/latest/Functors___-Monads-and--do--Notation/Varieties-of-Monads/#Except___error) ε α) :
[Except](https://lean-lang.org/doc/reference/latest/Functors___-Monads-and--do--Notation/Varieties-of-Monads/#Except___error) ε α
```

Both of its parameters are in `[Except](https://lean-lang.org/doc/reference/latest/Functors___-Monads-and--do--Notation/Varieties-of-Monads/#Except___error) ε`.
`[MonadLift]](#manual-MonadLift___mk)` can lift the entire application of the handler.
The function `[getBytes]](#manual-getBytes-_LPAR_in-Exceptions-and-Lifting_RPAR_)`, which extracts the single bytes from an array of `[Nat](https://lean-lang.org/doc/reference/latest/Basic-Types/Natural-Numbers/#Nat___zero)`s using state and exceptions, is written without [`do`](https://lean-lang.org/doc/reference/latest/Functors___-Monads-and--do--Notation/Syntax/#Lean___Parser___Term___do)-notation or automatic lifting in order to make its structure explicit.

```lean
set_option [autoLift]](#manual-autoLift) false
def getByte (n : [Nat](https://lean-lang.org/doc/reference/latest/Basic-Types/Natural-Numbers/#Nat___zero)) : [Except](https://lean-lang.org/doc/reference/latest/Functors___-Monads-and--do--Notation/Varieties-of-Monads/#Except___error) [String](https://lean-lang.org/doc/reference/latest/Basic-Types/Strings/#String___ofByteArray) [UInt8](https://lean-lang.org/doc/reference/latest/Basic-Types/Fixed-Precision-Integers/#UInt8___ofBitVec) :=
[if]](#manual-termIfThenElse) n < 256 [then]](#manual-termIfThenElse)
[pure]](#manual-Pure___mk) n.[toUInt8](https://lean-lang.org/doc/reference/latest/Basic-Types/Natural-Numbers/#Nat___toUInt8)
[else]](#manual-termIfThenElse) [throw](https://lean-lang.org/doc/reference/latest/Functors___-Monads-and--do--Notation/Varieties-of-Monads/#MonadExcept___mk) s!"Out of range: {n}"
def getBytes (input : [Array](https://lean-lang.org/doc/reference/latest/Basic-Types/Arrays/#Array___mk) [Nat](https://lean-lang.org/doc/reference/latest/Basic-Types/Natural-Numbers/#Nat___zero)) :
[StateT](https://lean-lang.org/doc/reference/latest/Functors___-Monads-and--do--Notation/Varieties-of-Monads/#StateT) ([Array](https://lean-lang.org/doc/reference/latest/Basic-Types/Arrays/#Array___mk) [UInt8](https://lean-lang.org/doc/reference/latest/Basic-Types/Fixed-Precision-Integers/#UInt8___ofBitVec)) ([Except](https://lean-lang.org/doc/reference/latest/Functors___-Monads-and--do--Notation/Varieties-of-Monads/#Except___error) [String](https://lean-lang.org/doc/reference/latest/Basic-Types/Strings/#String___ofByteArray)) [Unit](https://lean-lang.org/doc/reference/latest/Basic-Types/The-Unit-Type/#Unit) := [do](https://lean-lang.org/doc/reference/latest/Functors___-Monads-and--do--Notation/Syntax/#Lean___Parser___Term___do)
input.[forM](https://lean-lang.org/doc/reference/latest/Basic-Types/Arrays/#Array___forM) fun i =>
liftM ([Except.tryCatch](https://lean-lang.org/doc/reference/latest/Functors___-Monads-and--do--Notation/Varieties-of-Monads/#Except___tryCatch) ([some](https://lean-lang.org/doc/reference/latest/Basic-Types/Optional-Values/#Option___none) <$> [getByte]](#manual-getByte-_LPAR_in-Exceptions-and-Lifting_RPAR_) i) fun _ => [pure]](#manual-Pure___mk) [none](https://lean-lang.org/doc/reference/latest/Basic-Types/Optional-Values/#Option___none)) >>=
fun
| [some](https://lean-lang.org/doc/reference/latest/Basic-Types/Optional-Values/#Option___none) b => [modify](https://lean-lang.org/doc/reference/latest/Functors___-Monads-and--do--Notation/Varieties-of-Monads/#modify) (·.[push](https://lean-lang.org/doc/reference/latest/Basic-Types/Arrays/#Array___push) b)
| [none](https://lean-lang.org/doc/reference/latest/Basic-Types/Optional-Values/#Option___none) => [pure]](#manual-Pure___mk) ()
```

```lean
[#eval]](#manual-Lean___Parser___Command___eval) [getBytes]](#manual-getBytes-_LPAR_in-Exceptions-and-Lifting_RPAR_) #[1, 58, 255, 300, 2, 1000000] |>.[run](https://lean-lang.org/doc/reference/latest/Functors___-Monads-and--do--Notation/Varieties-of-Monads/#StateT___run) #[] |>.[map](https://lean-lang.org/doc/reference/latest/Functors___-Monads-and--do--Notation/Varieties-of-Monads/#Except___map) (·.[2](https://lean-lang.org/doc/reference/latest/Basic-Types/Tuples/#Prod___mk))
```

```lean
Except.ok #[1, 58, 255, 2]
```

`[getBytes]](#manual-getBytes-_LPAR_in-Exceptions-and-Lifting_RPAR_)` uses an `Option` returned from the lifted action to signal the desired state updates.
This quickly becomes unwieldy if there is more than one way to react to the inner action, such as saving handled exceptions.
Ideally, state updates would be performed within the `[tryCatch](https://lean-lang.org/doc/reference/latest/Functors___-Monads-and--do--Notation/Varieties-of-Monads/#MonadExcept___mk)` call directly.

Attempting to save bytes and handled exceptions does not work, however, because the arguments to `[Except.tryCatch](https://lean-lang.org/doc/reference/latest/Functors___-Monads-and--do--Notation/Varieties-of-Monads/#Except___tryCatch)` have type `[Except](https://lean-lang.org/doc/reference/latest/Functors___-Monads-and--do--Notation/Varieties-of-Monads/#Except___error) [String](https://lean-lang.org/doc/reference/latest/Basic-Types/Strings/#String___ofByteArray) [Unit](https://lean-lang.org/doc/reference/latest/Basic-Types/The-Unit-Type/#Unit)`:

```lean
def getBytes' (input : [Array](https://lean-lang.org/doc/reference/latest/Basic-Types/Arrays/#Array___mk) [Nat](https://lean-lang.org/doc/reference/latest/Basic-Types/Natural-Numbers/#Nat___zero)) :
[StateT](https://lean-lang.org/doc/reference/latest/Functors___-Monads-and--do--Notation/Varieties-of-Monads/#StateT) ([Array](https://lean-lang.org/doc/reference/latest/Basic-Types/Arrays/#Array___mk) [String](https://lean-lang.org/doc/reference/latest/Basic-Types/Strings/#String___ofByteArray))
([StateT](https://lean-lang.org/doc/reference/latest/Functors___-Monads-and--do--Notation/Varieties-of-Monads/#StateT) ([Array](https://lean-lang.org/doc/reference/latest/Basic-Types/Arrays/#Array___mk) [UInt8](https://lean-lang.org/doc/reference/latest/Basic-Types/Fixed-Precision-Integers/#UInt8___ofBitVec))
([Except](https://lean-lang.org/doc/reference/latest/Functors___-Monads-and--do--Notation/Varieties-of-Monads/#Except___error) [String](https://lean-lang.org/doc/reference/latest/Basic-Types/Strings/#String___ofByteArray))) [Unit](https://lean-lang.org/doc/reference/latest/Basic-Types/The-Unit-Type/#Unit) := [do](https://lean-lang.org/doc/reference/latest/Functors___-Monads-and--do--Notation/Syntax/#Lean___Parser___Term___do)
input.[forM](https://lean-lang.org/doc/reference/latest/Basic-Types/Arrays/#Array___forM) fun i =>
liftM
([Except.tryCatch](https://lean-lang.org/doc/reference/latest/Functors___-Monads-and--do--Notation/Varieties-of-Monads/#Except___tryCatch)
([getByte]](#manual-getByte-_LPAR_in-Exceptions-and-Lifting_RPAR_) i >>= fun b =>
[modifyThe](https://lean-lang.org/doc/reference/latest/Functors___-Monads-and--do--Notation/Varieties-of-Monads/#modifyThe) ([Array](https://lean-lang.org/doc/reference/latest/Basic-Types/Arrays/#Array___mk) [UInt8](https://lean-lang.org/doc/reference/latest/Basic-Types/Fixed-Precision-Integers/#UInt8___ofBitVec)) (·.[push](https://lean-lang.org/doc/reference/latest/Basic-Types/Arrays/#Array___push) b))
fun e =>
[modifyThe](https://lean-lang.org/doc/reference/latest/Functors___-Monads-and--do--Notation/Varieties-of-Monads/#modifyThe) ([Array](https://lean-lang.org/doc/reference/latest/Basic-Types/Arrays/#Array___mk) [String](https://lean-lang.org/doc/reference/latest/Basic-Types/Strings/#String___ofByteArray)) (·.[push](https://lean-lang.org/doc/reference/latest/Basic-Types/Arrays/#Array___push) e))
```

```lean
failed to synthesize instance of type class
  [MonadStateOf](https://lean-lang.org/doc/reference/latest/Functors___-Monads-and--do--Notation/Varieties-of-Monads/#MonadStateOf___mk) ([Array](https://lean-lang.org/doc/reference/latest/Basic-Types/Arrays/#Array___mk) [String](https://lean-lang.org/doc/reference/latest/Basic-Types/Strings/#String___ofByteArray)) ([Except](https://lean-lang.org/doc/reference/latest/Functors___-Monads-and--do--Notation/Varieties-of-Monads/#Except___error) [String](https://lean-lang.org/doc/reference/latest/Basic-Types/Strings/#String___ofByteArray))

Hint: Type class instance resolution failures can be inspected with the `set_option trace.Meta.synthInstance true` command.
```

Because `[StateT](https://lean-lang.org/doc/reference/latest/Functors___-Monads-and--do--Notation/Varieties-of-Monads/#StateT)` has a `[MonadControl]](#manual-MonadControl___mk)` instance, `[control]](#manual-control)` can be used instead of `liftM`.
It provides the inner action with an interpreter for the outer monad.
In the case of `[StateT](https://lean-lang.org/doc/reference/latest/Functors___-Monads-and--do--Notation/Varieties-of-Monads/#StateT)`, this interpreter expects that the inner monad returns a tuple that includes the updated state, and takes care of providing the initial state and extracting the updated state from the tuple.

```lean
def getBytes' (input : [Array](https://lean-lang.org/doc/reference/latest/Basic-Types/Arrays/#Array___mk) [Nat](https://lean-lang.org/doc/reference/latest/Basic-Types/Natural-Numbers/#Nat___zero)) :
[StateT](https://lean-lang.org/doc/reference/latest/Functors___-Monads-and--do--Notation/Varieties-of-Monads/#StateT) ([Array](https://lean-lang.org/doc/reference/latest/Basic-Types/Arrays/#Array___mk) [String](https://lean-lang.org/doc/reference/latest/Basic-Types/Strings/#String___ofByteArray))
([StateT](https://lean-lang.org/doc/reference/latest/Functors___-Monads-and--do--Notation/Varieties-of-Monads/#StateT) ([Array](https://lean-lang.org/doc/reference/latest/Basic-Types/Arrays/#Array___mk) [UInt8](https://lean-lang.org/doc/reference/latest/Basic-Types/Fixed-Precision-Integers/#UInt8___ofBitVec))
([Except](https://lean-lang.org/doc/reference/latest/Functors___-Monads-and--do--Notation/Varieties-of-Monads/#Except___error) [String](https://lean-lang.org/doc/reference/latest/Basic-Types/Strings/#String___ofByteArray))) [Unit](https://lean-lang.org/doc/reference/latest/Basic-Types/The-Unit-Type/#Unit) := [do](https://lean-lang.org/doc/reference/latest/Functors___-Monads-and--do--Notation/Syntax/#Lean___Parser___Term___do)
input.[forM](https://lean-lang.org/doc/reference/latest/Basic-Types/Arrays/#Array___forM) fun i =>
[control]](#manual-control) fun run =>
([Except.tryCatch](https://lean-lang.org/doc/reference/latest/Functors___-Monads-and--do--Notation/Varieties-of-Monads/#Except___tryCatch)
([getByte]](#manual-getByte-_LPAR_in-Exceptions-and-Lifting_RPAR_) i >>= fun b =>
run ([modifyThe](https://lean-lang.org/doc/reference/latest/Functors___-Monads-and--do--Notation/Varieties-of-Monads/#modifyThe) ([Array](https://lean-lang.org/doc/reference/latest/Basic-Types/Arrays/#Array___mk) [UInt8](https://lean-lang.org/doc/reference/latest/Basic-Types/Fixed-Precision-Integers/#UInt8___ofBitVec)) (·.[push](https://lean-lang.org/doc/reference/latest/Basic-Types/Arrays/#Array___push) b))))
fun e =>
run ([modifyThe](https://lean-lang.org/doc/reference/latest/Functors___-Monads-and--do--Notation/Varieties-of-Monads/#modifyThe) ([Array](https://lean-lang.org/doc/reference/latest/Basic-Types/Arrays/#Array___mk) [String](https://lean-lang.org/doc/reference/latest/Basic-Types/Strings/#String___ofByteArray)) (·.[push](https://lean-lang.org/doc/reference/latest/Basic-Types/Arrays/#Array___push) e))
```

```lean
[#eval]](#manual-Lean___Parser___Command___eval)
getBytes' #[1, 58, 255, 300, 2, 1000000]
|>.[run](https://lean-lang.org/doc/reference/latest/Functors___-Monads-and--do--Notation/Varieties-of-Monads/#StateT___run) #[] |>.[run](https://lean-lang.org/doc/reference/latest/Functors___-Monads-and--do--Notation/Varieties-of-Monads/#StateT___run) #[]
|>.[map](https://lean-lang.org/doc/reference/latest/Functors___-Monads-and--do--Notation/Varieties-of-Monads/#Except___map) (fun (((), bytes), errs) => (bytes, errs))
```

```lean
Except.ok (#["Out of range: 300", "Out of range: 1000000"], #[1, 58, 255, 2])
```

---



## Functors, Monads and do -Notation — 18.3. Syntax {#manual-functors-monads-and-do--notation-183-syntax}

> 📄 Source: https://lean-lang.org/doc/reference/latest/Functors___-Monads-and--do--Notation/Syntax/

Lean supports programming with functors, applicative functors, and monads via special syntax:

- Infix operators are provided for the most common operations.
- An embedded language called [[`do`]](#manual-Lean___Parser___Term___do)-notation](https://lean-lang.org/doc/reference/latest/Functors___-Monads-and--do--Notation/Syntax/#--tech-term-do-notation) allows the use of imperative syntax when writing programs in a monad.

### 18.3.1. Infix Operators {#manual-The-Lean-Language-Reference--Functors___-Monads-and--do--Notation--Syntax--Infix-Operators}

Infix operators are primarily useful in smaller expressions, or when there is no `[Monad]](#manual-Monad___mk)` instance.

#### 18.3.1.1. Functors {#manual-The-Lean-Language-Reference--Functors___-Monads-and--do--Notation--Syntax--Infix-Operators--Functors}

There are two infix operators for `[Functor.map]](#manual-Functor___mk)`.

syntaxFunctor Operators

`g <$> x` is short for `[Functor.map]](#manual-Functor___mk) g x`.

```lean
term ::= ...
    | term <$> term
```

`x <&> g` is short for `[Functor.map]](#manual-Functor___mk) g x`.

```lean
term ::= ...
    | term <&> term
```

#### 18.3.1.2. Applicative Functors {#manual-The-Lean-Language-Reference--Functors___-Monads-and--do--Notation--Syntax--Infix-Operators--Applicative-Functors}

syntaxApplicative Operators

`g <*> x` is short for `[Seq.seq]](#manual-Seq___mk) g (fun () => x)`.
The function is inserted to delay evaluation because control might not reach the argument.

```lean
term ::= ...
    | term <*> term
```

`e1 *> e2` is short for `[SeqRight.seqRight]](#manual-SeqRight___mk) e1 (fun () => e2)`.

```lean
term ::= ...
    | term *> term
```

`e1 <* e2` is short for `[SeqLeft.seqLeft]](#manual-SeqLeft___mk) e1 (fun () => e2)`.

```lean
term ::= ...
    | term <* term
```

Many applicative functors also support failure and recovery via the `[Alternative]](#manual-Alternative___mk)` type class.
This class also has an infix operator.

syntaxAlternative Operators

`e <|> e'` is short for `OrElse.orElse e (fun () => e')`.
The function is inserted to delay evaluation because control might not reach the argument.

```lean
term ::= ...
    | term <|> term
```

```lean
structure User where
name : [String](https://lean-lang.org/doc/reference/latest/Basic-Types/Strings/#String___ofByteArray)
favoriteNat : [Nat](https://lean-lang.org/doc/reference/latest/Basic-Types/Natural-Numbers/#Nat___zero)
def main : [IO](https://lean-lang.org/doc/reference/latest/IO/Logical-Model/#IO) [Unit](https://lean-lang.org/doc/reference/latest/Basic-Types/The-Unit-Type/#Unit) := [pure]](#manual-Pure___mk) ()
```

**Example: Infix Functor and Applicative Operators**

A common functional programming idiom is to use a pure function in some context with effects by applying it via `[Functor.map]](#manual-Functor___mk)` and `[Seq.seq]](#manual-Seq___mk)`.
The function is applied to its sequence of arguments using `<$>`, and the arguments are separated by `<*>`.

In this example, the constructor `User.mk` is applied via this idiom in the body of `[main]](#manual-main-_LPAR_in-Infix--Functor--and--Applicative--Operators_RPAR_)`.

```lean
def getName : [IO](https://lean-lang.org/doc/reference/latest/IO/Logical-Model/#IO) [String](https://lean-lang.org/doc/reference/latest/Basic-Types/Strings/#String___ofByteArray) := [do]](#manual-Lean___Parser___Term___do)
[IO.println](https://lean-lang.org/doc/reference/latest/IO/Console-Output/#IO___println) "What is your name?"
return (← (← [IO.getStdin](https://lean-lang.org/doc/reference/latest/IO/Files___-File-Handles___-and-Streams/#IO___getStdin)).[getLine](https://lean-lang.org/doc/reference/latest/IO/Files___-File-Handles___-and-Streams/#IO___FS___Stream___mk)).[trimAsciiEnd](https://lean-lang.org/doc/reference/latest/Basic-Types/Strings/#String___trimAsciiEnd).[copy](https://lean-lang.org/doc/reference/latest/Basic-Types/Strings/#String___Slice___copy)
partial def getFavoriteNat : [IO](https://lean-lang.org/doc/reference/latest/IO/Logical-Model/#IO) [Nat](https://lean-lang.org/doc/reference/latest/Basic-Types/Natural-Numbers/#Nat___zero) := [do]](#manual-Lean___Parser___Term___do)
[IO.println](https://lean-lang.org/doc/reference/latest/IO/Console-Output/#IO___println) "What is your favorite natural number?"
let line ← (← [IO.getStdin](https://lean-lang.org/doc/reference/latest/IO/Files___-File-Handles___-and-Streams/#IO___getStdin)).[getLine](https://lean-lang.org/doc/reference/latest/IO/Files___-File-Handles___-and-Streams/#IO___FS___Stream___mk)
if let [some](https://lean-lang.org/doc/reference/latest/Basic-Types/Optional-Values/#Option___none) n := line.[trimAscii](https://lean-lang.org/doc/reference/latest/Basic-Types/Strings/#String___trimAscii).[copy](https://lean-lang.org/doc/reference/latest/Basic-Types/Strings/#String___Slice___copy).[toNat?](https://lean-lang.org/doc/reference/latest/Basic-Types/Strings/#String___toNat___) then
return n
else
[IO.println](https://lean-lang.org/doc/reference/latest/IO/Console-Output/#IO___println) "Let's try again."
[getFavoriteNat]](#manual-getFavoriteNat-_LPAR_in-Infix--Functor--and--Applicative--Operators_RPAR_)
structure User where
name : [String](https://lean-lang.org/doc/reference/latest/Basic-Types/Strings/#String___ofByteArray)
favoriteNat : [Nat](https://lean-lang.org/doc/reference/latest/Basic-Types/Natural-Numbers/#Nat___zero)
[deriving]](#manual-Lean___Parser___Command___optDeriving-next) [Repr]](#manual-Repr___mk)
def main : [IO](https://lean-lang.org/doc/reference/latest/IO/Logical-Model/#IO) [Unit](https://lean-lang.org/doc/reference/latest/Basic-Types/The-Unit-Type/#Unit) := [do]](#manual-Lean___Parser___Term___do)
let user ← User.mk <$> [getName]](#manual-getName-_LPAR_in-Infix--Functor--and--Applicative--Operators_RPAR_) <*> [getFavoriteNat]](#manual-getFavoriteNat-_LPAR_in-Infix--Functor--and--Applicative--Operators_RPAR_)
[IO.println](https://lean-lang.org/doc/reference/latest/IO/Console-Output/#IO___println) ([repr]](#manual-repr-next) user)
```

When run with this input:

`stdin``A. Lean User``None``42`

it produces this output:

`stdout``What is your name?``What is your favorite natural number?``Let's try again.``What is your favorite natural number?``{ name := "A. Lean User", favoriteNat := 42 }`

#### 18.3.1.3. Monads {#manual-The-Lean-Language-Reference--Functors___-Monads-and--do--Notation--Syntax--Infix-Operators--Monads}

Monads are primarily used via [[`do`]](#manual-Lean___Parser___Term___do)-notation](https://lean-lang.org/doc/reference/latest/Functors___-Monads-and--do--Notation/Syntax/#--tech-term-do-notation).
However, it can sometimes be convenient to describe monadic computations via operators.

syntaxMonad Operators

`act >>= f` is syntax for `[Bind.bind]](#manual-Bind___mk) act f`.

```lean
term ::= ...
    | term >>= term
```

Similarly, the reversed operator `f =<< act` is syntax for `[Bind.bind]](#manual-Bind___mk) act f`.

```lean
term ::= ...
    | term =<< term
```

The Kleisli composition operators `[Bind.kleisliRight](https://lean-lang.org/doc/reference/latest/Functors___-Monads-and--do--Notation/API-Reference/#Bind___kleisliRight)` and `[Bind.kleisliLeft](https://lean-lang.org/doc/reference/latest/Functors___-Monads-and--do--Notation/API-Reference/#Bind___kleisliLeft)` also have infix operators.

```lean
term ::= ...
    | term >=> term
```

```lean
term ::= ...
    | term <=< term
```

### 18.3.2. `do`-Notation {#manual-do-notation}

Monads are primarily used via [`do`]](#manual-Lean___Parser___Term___do)-notation, which is an embedded language for programming in an imperative style.
It provides familiar syntax for sequencing effectful operations, early return, local mutable variables, loops, and exception handling.
All of these features are translated to the operations of the `[Monad]](#manual-Monad___mk)` type class, with a few of them requiring addition instances of classes such as `[ForIn]](#manual-ForIn___mk)` that specify iteration over containers.
For more details about the design of [`do`]](#manual-Lean___Parser___Term___do)-notation, please consult Ullrich and de Moura (2022)Sebastian Ullrich and Leonardo de Moura, 2022. [“`do` Unchained: Embracing Local Imperativity in a Purely Functional Language”](https://dl.acm.org/doi/10.1145/3547640). In *Proceedings of the ACM on Programming Languages: ICFP 2022.*.

A [`do`]](#manual-Lean___Parser___Term___do) term consists of the keyword [`do`]](#manual-Lean___Parser___Term___do) followed by a sequence of *[`do`]](#manual-Lean___Parser___Term___do) elements*.

syntax`do`-Notation

```lean
term ::= ...
    | do doSeqItem*
```

The elements in a [`do`]](#manual-Lean___Parser___Term___do) may be separated by semicolons; otherwise, each should be on its own line and they should have equal indentation.

#### 18.3.2.1. Sequential Computations {#manual-The-Lean-Language-Reference--Functors___-Monads-and--do--Notation--Syntax--do--Notation--Sequential-Computations}

One form of [[`do`]](#manual-Lean___Parser___Term___do)-element](https://lean-lang.org/doc/reference/latest/Functors___-Monads-and--do--Notation/Syntax/#--tech-term-do-elements) is a term.

syntaxTerms in `do`-Notation

```lean
doSeqItem ::= ...
    | term
```

A term followed by a sequence of elements is translated to a use of `[bind]](#manual-Bind___mk)`; in particular, `[do]](#manual-Lean___Parser___Term___do) e1; es` is translated to `e1 >>= fun () => [do]](#manual-Lean___Parser___Term___do) es`.

| [`do`]](#manual-Lean___Parser___Term___do) Element | Desugaring |
| --- | --- |
| ```lean [do]](#manual-Lean___Parser___Term___do) e1 es ``` | ```lean e1 >>= fun () => [do]](#manual-Lean___Parser___Term___do) es ``` |

The result of the term's computation may also be named, allowing it to be used in subsequent steps.
This is done using `let`.

syntaxData Dependence in `do`-Notation

There are two forms of monadic `let`-binding in a [`do`]](#manual-Lean___Parser___Term___do) block.
The first binds an identifier to the result, with an optional type annotation:

```lean
doSeqItem ::= ...
    | let ident(:term)? ← term
```

The second binds a pattern to the result.
The fallback clause, beginning with `|`, specifies the behavior when the pattern does not match the result.

```lean
doSeqItem ::= ...
    | let term ← term
        (| doSeqIndent)?
```

This syntax is also translated to a use of `[bind]](#manual-Bind___mk)`.
`[do]](#manual-Lean___Parser___Term___do) let x ← e1; es` is translated to `e1 >>= fun x => [do]](#manual-Lean___Parser___Term___do) es`, and fallback clauses are translated to default pattern matches.
`let` may also be used with the standard definition syntax `:=` instead of `←`.
This indicates a pure, rather than monadic, definition:

syntaxLocal Definitions in `do`-Notation

```lean
doSeqItem ::= ...
    | let (ident | [hole]](#manual-Lean___Parser___Term___hole)) := term
```

`[do]](#manual-Lean___Parser___Term___do) let x := e; es` is translated to `let x := e; [do]](#manual-Lean___Parser___Term___do) es`.

| [`do`]](#manual-Lean___Parser___Term___do) Element | Desugaring |
| --- | --- |
| ```lean [do]](#manual-Lean___Parser___Term___do) let x ← e1 es ``` | ```lean e1 >>= fun x => [do]](#manual-Lean___Parser___Term___do) es ``` |
| ```lean [do]](#manual-Lean___Parser___Term___do) let some x ← e1? | fallback es ``` | ```lean e1? >>= fun | [some](https://lean-lang.org/doc/reference/latest/Basic-Types/Optional-Values/#Option___none) x => [do]](#manual-Lean___Parser___Term___do) es | _ => fallback ``` |
| ```lean [do]](#manual-Lean___Parser___Term___do) let x := e es ``` | ```lean let x := e [do]](#manual-Lean___Parser___Term___do) es ``` |

Within a [`do`]](#manual-Lean___Parser___Term___do) block, `←` may be used as a prefix operator.
The expression to which it is applied is replaced with a fresh variable, which is bound using `[bind]](#manual-Bind___mk)` just before the current step.
This allows monadic effects to be used in positions that otherwise might expect a pure value, while still maintaining the distinction between *describing* an effectful computation and actually *executing* its effects.
Multiple occurrences of `←` are processed from left to right, inside to outside.

| Example [`do`]](#manual-Lean___Parser___Term___do) Element | Desugaring |
| --- | --- |
| ```lean [do]](#manual-Lean___Parser___Term___do) f (← e1) (← e2) es ``` | ```lean [do]](#manual-Lean___Parser___Term___do) let x ← e1 let y ← e2 f x y es ``` |
| ```lean [do]](#manual-Lean___Parser___Term___do) let x := g (← h (← e1)) es ``` | ```lean [do]](#manual-Lean___Parser___Term___do) let y ← e1 let z ← h y let x := g z es ``` |

Example Nested Action Desugarings

In addition to convenient support for sequential computations with data dependencies, [`do`]](#manual-Lean___Parser___Term___do)-notation also supports the local addition of a variety of effects, including early return, local mutable state, and loops with early termination.
These effects are implemented via transformations of the entire [`do`]](#manual-Lean___Parser___Term___do) block in a manner akin to [monad transformers](https://lean-lang.org/doc/reference/latest/Functors___-Monads-and--do--Notation/Varieties-of-Monads/#--tech-term-monad-transformer), rather than via a local desugaring.

#### 18.3.2.2. Early Return {#manual-early-return}

Early return terminates a computation immediately with a given value.
The value is returned from the closest containing [`do`]](#manual-Lean___Parser___Term___do) block; however, this may not be the closest `do` keyword.
The rules for determining the extent of a [`do`]](#manual-Lean___Parser___Term___do) block are described [in their own section]](#manual-closest-do-block).

syntaxEarly Return

```lean
doSeqItem ::= ...
    | return term
```

```lean
doSeqItem ::= ...
    | return
```

Not all monads include early return.
Thus, when a [`do`]](#manual-Lean___Parser___Term___do) block contains `return`, the code needs to be rewritten to simulate the effect.
A program that uses early return to compute a value of type `α` in a monad `m` can be thought of as a program in the monad `[ExceptT](https://lean-lang.org/doc/reference/latest/Functors___-Monads-and--do--Notation/Varieties-of-Monads/#ExceptT) α m α`: early-returned values take the exception pathway, while ordinary returns do not.
Then, an outer handler can return the value from either code paths.
Internally, the [`do`]](#manual-Lean___Parser___Term___do) elaborator performs a translation very much like this one.

On its own, `return` is short for `return`​` `​`()`.

#### 18.3.2.3. Local Mutable State {#manual-let-mut}

Local mutable state is mutable state that cannot escape the [`do`]](#manual-Lean___Parser___Term___do) block in which it is defined.
The `let mut` binder introduces a locally-mutable binding.

syntaxLocal Mutability

Mutable bindings may be initialized either with pure computations or with monadic computations:

```lean
doSeqItem ::= ...
    | let mut (ident | [hole]](#manual-Lean___Parser___Term___hole)) := term
```

```lean
doSeqItem ::= ...
    | let mut ident ← doElem
```

Similarly, they can be mutated either with pure values or the results of monad computations:

```lean
doElem ::= ...
    | ident(: term)?  := term
```

```lean
doElem ::= ...
    | term(: term)? := term
```

```lean
doElem ::= ...
    | ident(: term)? ← term
```

```lean
doElem ::= ...
    | term ← term
        (| doSeqIndent)?
```

These locally-mutable bindings are less powerful than a [state monad](https://lean-lang.org/doc/reference/latest/Functors___-Monads-and--do--Notation/Varieties-of-Monads/#--tech-term-State-monads) because they are not mutable outside their lexical scope; this also makes them easier to reason about.
When [`do`]](#manual-Lean___Parser___Term___do) blocks contain mutable bindings, the [`do`]](#manual-Lean___Parser___Term___do) elaborator transforms the expression similarly to the way that `[StateT](https://lean-lang.org/doc/reference/latest/Functors___-Monads-and--do--Notation/Varieties-of-Monads/#StateT)` would, constructing a new monad and initializing it with the correct values.

#### 18.3.2.4. Control Structures {#manual-do-control-structures}

There are [`do`]](#manual-Lean___Parser___Term___do) elements that correspond to most of Lean's term-level control structures.
When they occur as a step in a [`do`]](#manual-Lean___Parser___Term___do) block, they are interpreted as [`do`]](#manual-Lean___Parser___Term___do) elements rather than terms.
Each branch of the control structures is a sequence of [`do`]](#manual-Lean___Parser___Term___do) elements, rather than a term, and some of them are more syntactically flexible than their corresponding terms.

syntaxConditionals

In a [`do`]](#manual-Lean___Parser___Term___do) block, `if` statements may omit their `else` branch.
Omitting an `else` branch is equivalent to using `[pure]](#manual-Pure___mk)``()` as the contents of the branch.

```lean
doSeqItem ::= ...
    | if ((ident | [hole]](#manual-Lean___Parser___Term___hole)) :)? term then
        doSeqItem*
      (else
        doSeqItem*)?
```

Syntactically, the `then` branch cannot be omitted.
For these cases, `unless` only executes its body when the condition is false.
The [`do`]](#manual-Lean___Parser___Term___do) in `unless` is part of its syntax and does not induce a nested [`do`]](#manual-Lean___Parser___Term___do) block.

syntaxReverse Conditionals

```lean
doSeqItem ::= ...
    | unless term do
        doSeqItem*
```

When `match` is used in a [`do`]](#manual-Lean___Parser___Term___do) block, each branch is considered to be part of the same block.
Otherwise, it is equivalent to the [`match`]](#manual-Lean___Parser___Term___match) term.

syntaxPattern Matching

```lean
doSeqItem ::= ...
    | match (((ident | [hole]](#manual-Lean___Parser___Term___hole)) :)? term),* with
        (| term,* => doSeqItem*)*
```

#### 18.3.2.5. Iteration {#manual-monad-iteration-syntax}

Within a [`do`]](#manual-Lean___Parser___Term___do) block, `for`​`…`​`in` loops allow iteration over a data structure.
The body of the loop is part of the containing [`do`]](#manual-Lean___Parser___Term___do) block, so local effects such as early return and mutable variables may be used.

syntaxIteration over Collections

```lean
doSeqItem ::= ...
    | for ((ident :)? term in term),* do
        doSeqItem*
```

A `for`​`…`​`in` loop requires at least one clause that specifies the iteration to be performed, which consists of an optional membership proof name followed by a colon (`:`), a pattern to bind, the keyword `in`, and a collection term.
The pattern, which may just be an [identifier](https://lean-lang.org/doc/reference/latest/Notations-and-Macros/Defining-New-Syntax/#--tech-term-Identifiers), must match any element of the collection; patterns in this position cannot be used as implicit filters.
Further clauses may be provided by separating them with commas.
Each collection is iterated over at the same time, and iteration stops when any of the collections runs out of elements.

**Example: Iteration Over Multiple Collections**

When iterating over multiple collections, iteration stops when any of the collections runs out of elements.

```lean
[#eval]](#manual-Lean___Parser___Command___eval) [Id.run](https://lean-lang.org/doc/reference/latest/Functors___-Monads-and--do--Notation/Varieties-of-Monads/#Id___run) [do]](#manual-Lean___Parser___Term___do)
let mut v := #[]
for x in [0:43], y in ['a', 'b'] do
v := v.[push](https://lean-lang.org/doc/reference/latest/Basic-Types/Arrays/#Array___push) (x, y)
return v
```

```lean
[#[](https://lean-lang.org/doc/reference/latest/Basic-Types/Linked-Lists/#List___toArray)[(](https://lean-lang.org/doc/reference/latest/Basic-Types/Tuples/#Prod___mk)0[,](https://lean-lang.org/doc/reference/latest/Basic-Types/Tuples/#Prod___mk) 'a'[)](https://lean-lang.org/doc/reference/latest/Basic-Types/Tuples/#Prod___mk)[,](https://lean-lang.org/doc/reference/latest/Basic-Types/Linked-Lists/#List___nil) [(](https://lean-lang.org/doc/reference/latest/Basic-Types/Tuples/#Prod___mk)1[,](https://lean-lang.org/doc/reference/latest/Basic-Types/Tuples/#Prod___mk) 'b'[)](https://lean-lang.org/doc/reference/latest/Basic-Types/Tuples/#Prod___mk)[]](https://lean-lang.org/doc/reference/latest/Basic-Types/Linked-Lists/#List___toArray)
```

**Example: Iteration over Array Indices with for**

When iterating over the valid indices for an array with `for`, naming the membership proof allows the tactic that searches for proofs that array indices are in bounds to succeed.

```lean
def satisfyingIndices
(p : α → Prop) [[DecidablePred]](#manual-DecidablePred) p]
(xs : [Array](https://lean-lang.org/doc/reference/latest/Basic-Types/Arrays/#Array___mk) α) : [Array](https://lean-lang.org/doc/reference/latest/Basic-Types/Arrays/#Array___mk) [Nat](https://lean-lang.org/doc/reference/latest/Basic-Types/Natural-Numbers/#Nat___zero) := [Id.run](https://lean-lang.org/doc/reference/latest/Functors___-Monads-and--do--Notation/Varieties-of-Monads/#Id___run) [do]](#manual-Lean___Parser___Term___do)
let mut out := #[]
for h : i in [0:xs.[size](https://lean-lang.org/doc/reference/latest/Basic-Types/Arrays/#Array___size)] do
if p xs[i] then out := out.[push](https://lean-lang.org/doc/reference/latest/Basic-Types/Arrays/#Array___push) i
return out
```

Omitting the hypothesis name causes the array lookup to fail, because no proof is available in the context that the iteration variable is within the specified range.

Iteration with `for`-loops is translated into uses of `ForIn.forIn`, which is an analogue of `ForM.forM` with added support for local mutations and early termination.
`[ForIn.forIn]](#manual-ForIn___mk)` receives an initial value for the local mutable state and a monadic action as parameters, along with the collection being iterated over.
The monadic action passed to `[ForIn.forIn]](#manual-ForIn___mk)` takes a current state as a parameter and, after carrying out actions in the monad `m`, returns either `[ForInStep.yield]](#manual-ForInStep___done)` to indicate that iteration should continue with an updated set of local mutable values, or `[ForInStep.done]](#manual-ForInStep___done)` to indicate that `break` or `return` was executed.
When iteration is complete, `[ForIn.forIn]](#manual-ForIn___mk)` returns the final values of the local mutable values.

The specific desugaring of a loop depends on how state and early termination are used in its body.
Here are some examples:

| [`do`]](#manual-Lean___Parser___Term___do) Element | Desugaring |
| --- | --- |
| ```lean [do]](#manual-Lean___Parser___Term___do) let mut b := … for x in xs do b ← f x b es ``` | ```lean [do]](#manual-Lean___Parser___Term___do) let b := … let b ← [ForIn.forIn]](#manual-ForIn___mk) xs b fun x b => [do]](#manual-Lean___Parser___Term___do) let b ← f x b return [ForInStep.yield]](#manual-ForInStep___done) b es ``` |
| ```lean [do]](#manual-Lean___Parser___Term___do) let mut b := … for x in xs do b ← f x b break es ``` | ```lean [do]](#manual-Lean___Parser___Term___do) let b := … let b ← [ForIn.forIn]](#manual-ForIn___mk) xs b fun x b => [do]](#manual-Lean___Parser___Term___do) let b ← f x b return [ForInStep.done]](#manual-ForInStep___done) b es ``` |
| ```lean [do]](#manual-Lean___Parser___Term___do) let mut b := … for h : x in xs do b ← f' x h b es ``` | ```lean [do]](#manual-Lean___Parser___Term___do) let b := … let b ← [ForIn'.forIn']](#manual-ForIn______mk) xs b fun x h b => [do]](#manual-Lean___Parser___Term___do) let b ← f' x h b return [ForInStep.yield]](#manual-ForInStep___done) b es ``` |
| ```lean [do]](#manual-Lean___Parser___Term___do) let mut b := … for h : x in xs do b ← f' x h b break es ``` | ```lean [do]](#manual-Lean___Parser___Term___do) let b := … let b ← [ForIn'.forIn']](#manual-ForIn______mk) xs b fun x h b => [do]](#manual-Lean___Parser___Term___do) let b ← f' x h b return [ForInStep.done]](#manual-ForInStep___done) b es ``` |

The body of a `while` loop is repeated while the condition remains true.
It is possible to write infinite loops using them in functions that are not marked `partial`.
This is because the `partial` modifier only applies to non-termination or infinite regress induced by the function being defined, and not by those that it calls.
The translation of `while` loops relies on a separate helper.

syntaxConditional Loops

```lean
doSeqItem ::= ...
    | while term do
        doSeqItem*
```

```lean
doSeqItem ::= ...
    | while (ident | [hole]](#manual-Lean___Parser___Term___hole)) : term do
        doSeqItem*
```

The body of a `repeat`-`until` loop is always executed at least once.
After each iteration, the condition is checked, and the loop is repeated when the condition is **false**.
When the condition becomes true, iteration stops.

syntaxPost-Tested Loops

```lean
doSeqItem ::= ...
    | repeat
        doSeqItem*
      until term
```

The body of a `repeat` loop is repeated until a `break` statement is executed.
Just like `while` loops, these loops can be used in functions that are not marked `partial`.

syntaxUnconditional Loops

```lean
doSeqItem ::= ...
    | repeat
        doSeqItem*
```

The `continue` statement skips the rest of the body of the closest enclosing `repeat`, `while`, or `for` loop, moving on to the next iteration.
The `break` statement terminates the closest enclosing `repeat`, `while`, or `for` loop, stopping iteration.

syntaxLoop Control Statements

```lean
doSeqItem ::= ...
    | continue
```

```lean
doSeqItem ::= ...
    | break
```

In addition to `break`, loops can always be terminated by effects in the current monad.
Throwing an exception from a loop terminates the loop.

**Example: Terminating Loops in the Option Monad**

The `failure` method from the `[Alternative]](#manual-Alternative___mk)` class can be used to terminate an otherwise-infinite loop in the `[Option](https://lean-lang.org/doc/reference/latest/Basic-Types/Optional-Values/#Option___none)` monad.

```lean
[#eval]](#manual-Lean___Parser___Command___eval) show [Option](https://lean-lang.org/doc/reference/latest/Basic-Types/Optional-Values/#Option___none) [Nat](https://lean-lang.org/doc/reference/latest/Basic-Types/Natural-Numbers/#Nat___zero) from [do]](#manual-Lean___Parser___Term___do)
let mut i := 0
repeat
if i > 1000 then failure
else i := 2 * (i + 1)
return i
```

```lean
[none](https://lean-lang.org/doc/reference/latest/Basic-Types/Optional-Values/#Option___none)
```

#### 18.3.2.6. Identifying `do` Blocks {#manual-closest-do-block}

Many features of [`do`]](#manual-Lean___Parser___Term___do)-notation have an effect on the current [`do`]](#manual-Lean___Parser___Term___do) block.
In particular, early return aborts the current block, causing it to evaluate to the returned value, and mutable bindings can only be mutated in the block in which they are defined.
Understanding these features requires a precise definition of what it means to be in the “same” block.

Empirically, this can be checked using the Lean language server.
When the cursor is on a `return` statement, the corresponding [`do`]](#manual-Lean___Parser___Term___do) keyword is highlighted.
Attempting to mutate a mutable binding outside of the same [`do`]](#manual-Lean___Parser___Term___do) block results in an error message.

![Highlighting do from return](https://lean-lang.org/doc/reference/latest/Functors___-Monads-and--do--Notation/Syntax/static/screenshots/do-return-hl-1.png)

![Highlighting do from return with errors](https://lean-lang.org/doc/reference/latest/Functors___-Monads-and--do--Notation/Syntax/static/screenshots/do-return-hl-2.png)

Highlighting [`do`]](#manual-Lean___Parser___Term___do)

The rules are as follows:

- Each element immediately nested under the [`do`]](#manual-Lean___Parser___Term___do) keyword that begins a block belongs to that block.
- Each element immediately nested under the [`do`]](#manual-Lean___Parser___Term___do) keyword that is an element in a containing [`do`]](#manual-Lean___Parser___Term___do) block belongs to the outer block.
- Elements in the branches of an `if`, `match`, or `unless` element belong to the same [`do`]](#manual-Lean___Parser___Term___do) block as the control structure that contains them. The `do` keyword that is part of the syntax of `unless` does not introduce a new [`do`]](#manual-Lean___Parser___Term___do) block.
- Elements in the body of `repeat`, `while`, and `for` belong to the same [`do`]](#manual-Lean___Parser___Term___do) block as the loop that contains them. The `do` keyword that is part of the syntax of `while` and `for` does not introduce a new [`do`]](#manual-Lean___Parser___Term___do) block.

**Example: Nested do and Branches**

The following example outputs `6` rather than `7`:

```lean
def test : [StateM](https://lean-lang.org/doc/reference/latest/Functors___-Monads-and--do--Notation/Varieties-of-Monads/#StateM) [Nat](https://lean-lang.org/doc/reference/latest/Basic-Types/Natural-Numbers/#Nat___zero) [Unit](https://lean-lang.org/doc/reference/latest/Basic-Types/The-Unit-Type/#Unit) := [do]](#manual-Lean___Parser___Term___do)
[set](https://lean-lang.org/doc/reference/latest/Functors___-Monads-and--do--Notation/Varieties-of-Monads/#MonadStateOf___mk) 5
if [true](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false) then
[set](https://lean-lang.org/doc/reference/latest/Functors___-Monads-and--do--Notation/Varieties-of-Monads/#MonadStateOf___mk) 6
do return
[set](https://lean-lang.org/doc/reference/latest/Functors___-Monads-and--do--Notation/Varieties-of-Monads/#MonadStateOf___mk) 7
return
[#eval]](#manual-Lean___Parser___Command___eval) [test]](#manual-test-_LPAR_in-Nested--do--and-Branches_RPAR_).[run](https://lean-lang.org/doc/reference/latest/Functors___-Monads-and--do--Notation/Varieties-of-Monads/#StateT___run) 0
```

```lean
[(](https://lean-lang.org/doc/reference/latest/Basic-Types/Tuples/#Prod___mk)[(](https://lean-lang.org/doc/reference/latest/Basic-Types/The-Unit-Type/#Unit___unit)[)](https://lean-lang.org/doc/reference/latest/Basic-Types/The-Unit-Type/#Unit___unit)[,](https://lean-lang.org/doc/reference/latest/Basic-Types/Tuples/#Prod___mk) 6[)](https://lean-lang.org/doc/reference/latest/Basic-Types/Tuples/#Prod___mk)
```

This is because the `return` statement under the `if` belongs to the same [`do`]](#manual-Lean___Parser___Term___do) as its immediate parent, which itself belongs to the same [`do`]](#manual-Lean___Parser___Term___do) as the `if`.
If [`do`]](#manual-Lean___Parser___Term___do) blocks that occurred as elements in other [`do`]](#manual-Lean___Parser___Term___do) blocks instead created new blocks, then the example would output `7`.

#### 18.3.2.7. Type Classes for Iteration {#manual-The-Lean-Language-Reference--Functors___-Monads-and--do--Notation--Syntax--do--Notation--Type-Classes-for-Iteration}

To be used with `for` loops without membership proofs, collections must implement the `[ForIn]](#manual-ForIn___mk)` type class.
Implementing `[ForIn']](#manual-ForIn______mk)` additionally allows the use of `for` loops with membership proofs.

type class

```lean
[ForIn.{u, v, u₁, u₂}]](#manual-ForIn___mk) (m : Type u₁ → Type u₂) (ρ : Type u)
  (α : [outParam]](#manual-outParam) (Type v)) : Type (max (max (max u (u₁ + 1)) u₂) v)



[ForIn.{u, v, u₁, u₂}]](#manual-ForIn___mk)
  (m : Type u₁ → Type u₂) (ρ : Type u)
  (α : [outParam]](#manual-outParam) (Type v)) :
  Type (max (max (max u (u₁ + 1)) u₂) v)
```

Monadic iteration in `do`-blocks, using the `for x in xs` notation.

The parameter `m` is the monad of the `do`-block in which iteration is performed, `ρ` is the type of
the collection being iterated over, and `α` is the type of elements.

Instance Constructor

```lean
[ForIn.mk]](#manual-ForIn___mk).{u, v, u₁, u₂}
```

Methods

```lean
forIn : {β : Type u₁} → ρ → β → (α → β → m ([ForInStep]](#manual-ForInStep___done) β)) → m β
```

Monadically iterates over the contents of a collection `xs`, with a local state `b` and the
possibility of early termination.

Because a `do` block supports local mutable bindings along with `return`, and `break`, the monadic
action passed to `[ForIn.forIn]](#manual-ForIn___mk)` takes a starting state in addition to the current element of the
collection and returns an updated state together with an indication of whether iteration should
continue or terminate. If the action returns `[ForInStep.done]](#manual-ForInStep___done)`, then `[ForIn.forIn]](#manual-ForIn___mk)` should stop
iteration and return the updated state. If the action returns `[ForInStep.yield]](#manual-ForInStep___done)`, then
`[ForIn.forIn]](#manual-ForIn___mk)` should continue iterating if there are further elements, passing the updated state
to the action.

More information about the translation of `for` loops into `[ForIn.forIn]](#manual-ForIn___mk)` is available in [the Lean
reference manual](https://lean-lang.org/doc/reference/4.34.0-rc1/find/?domain=Verso.Genre.Manual.section&name=monad-iteration-syntax).

type class

```lean
[ForIn'.{u, v, u₁, u₂}]](#manual-ForIn______mk) (m : Type u₁ → Type u₂) (ρ : Type u)
  (α : [outParam]](#manual-outParam) (Type v)) (d : [outParam]](#manual-outParam) (Membership α ρ)) :
  Type (max (max (max u (u₁ + 1)) u₂) v)



[ForIn'.{u, v, u₁, u₂}]](#manual-ForIn______mk)
  (m : Type u₁ → Type u₂) (ρ : Type u)
  (α : [outParam]](#manual-outParam) (Type v))
  (d : [outParam]](#manual-outParam) (Membership α ρ)) :
  Type (max (max (max u (u₁ + 1)) u₂) v)
```

Monadic iteration in `do`-blocks with a membership proof, using the `for h : x in xs` notation.

The parameter `m` is the monad of the `do`-block in which iteration is performed, `ρ` is the type of
the collection being iterated over, `α` is the type of elements, and `d` is the specific membership
predicate to provide.

Instance Constructor

```lean
[ForIn'.mk]](#manual-ForIn______mk).{u, v, u₁, u₂}
```

Methods

```lean
forIn' : {β : Type u₁} → (x : ρ) → β → ((a : α) → a ∈ x → β → m ([ForInStep]](#manual-ForInStep___done) β)) → m β
```

Monadically iterates over the contents of a collection `xs`, with a local state `b` and the
possibility of early termination. At each iteration, the body of the loop is provided with a proof
that the current element is in the collection.

Because a `do` block supports local mutable bindings along with `return`, and `break`, the monadic
action passed to `[ForIn'.forIn']](#manual-ForIn______mk)` takes a starting state in addition to the current element of the
collection with its membership proof. The action returns an updated state together with an
indication of whether iteration should continue or terminate. If the action returns
`[ForInStep.done]](#manual-ForInStep___done)`, then `[ForIn'.forIn']](#manual-ForIn______mk)` should stop iteration and return the updated state. If the
action returns `[ForInStep.yield]](#manual-ForInStep___done)`, then `[ForIn'.forIn']](#manual-ForIn______mk)` should continue iterating if there are
further elements, passing the updated state to the action.

More information about the translation of `for` loops into `[ForIn'.forIn']](#manual-ForIn______mk)` is available in [the
Lean reference manual](https://lean-lang.org/doc/reference/4.34.0-rc1/find/?domain=Verso.Genre.Manual.section&name=monad-iteration-syntax).

inductive type

```lean
[ForInStep.{u}]](#manual-ForInStep___done) (α : Type u) : Type u



[ForInStep.{u}]](#manual-ForInStep___done) (α : Type u) : Type u
```

An indication of whether a loop's body terminated early that's used to compile the `for x in xs`
notation.

A collection's `[ForIn]](#manual-ForIn___mk)` or `[ForIn']](#manual-ForIn______mk)` instance describes how to iterate over its elements. The monadic
action that represents the body of the loop returns a `[ForInStep]](#manual-ForInStep___done) α`, where `α` is the local state
used to implement features such as `let mut`.

Constructors

```lean
[ForInStep.done.{u}]](#manual-ForInStep___done) {α : Type u} : α → [ForInStep]](#manual-ForInStep___done) α
```

The loop should terminate early.

`[ForInStep.done]](#manual-ForInStep___done)` is produced by uses of `break` or `return` in the loop body.

```lean
[ForInStep.yield.{u}]](#manual-ForInStep___done) {α : Type u} : α → [ForInStep]](#manual-ForInStep___done) α
```

The loop should continue with the next iteration, using the returned state.

`[ForInStep.yield]](#manual-ForInStep___done)` is produced by `continue` and by reaching the bottom of the loop body.

def

```lean
[ForInStep.value.{u_1}]](#manual-ForInStep___value) {α : Type u_1} (x : [ForInStep]](#manual-ForInStep___done) α) : α



[ForInStep.value.{u_1}]](#manual-ForInStep___value) {α : Type u_1}
  (x : [ForInStep]](#manual-ForInStep___done) α) : α
```

Extracts the value from a `[ForInStep]](#manual-ForInStep___done)`, ignoring whether it is `[ForInStep.done]](#manual-ForInStep___done)` or `[ForInStep.yield]](#manual-ForInStep___done)`.

type class

```lean
[ForM.{u, v, w₁, w₂}]](#manual-ForM___mk) (m : Type u → Type v) (γ : Type w₁)
  (α : [outParam]](#manual-outParam) (Type w₂)) : Type (max (max v w₁) w₂)



[ForM.{u, v, w₁, w₂}]](#manual-ForM___mk) (m : Type u → Type v)
  (γ : Type w₁) (α : [outParam]](#manual-outParam) (Type w₂)) :
  Type (max (max v w₁) w₂)
```

Overloaded monadic iteration over some container type.

An instance of `[ForM]](#manual-ForM___mk) m γ α` describes how to iterate a monadic operator over a container of type `γ`
with elements of type `α` in the monad `m`. The element type should be uniquely determined by the
monad and the container.

Use `[ForM.forIn]](#manual-ForM___forIn)` to construct a `[ForIn]](#manual-ForIn___mk)` instance from a `[ForM]](#manual-ForM___mk)` instance, thus enabling the use of
the `for` operator in `do`-notation.

Instance Constructor

```lean
[ForM.mk]](#manual-ForM___mk).{u, v, w₁, w₂}
```

Methods

```lean
forM : γ → (α → m [PUnit](https://lean-lang.org/doc/reference/latest/Basic-Types/The-Unit-Type/#PUnit___unit)) → m [PUnit](https://lean-lang.org/doc/reference/latest/Basic-Types/The-Unit-Type/#PUnit___unit)
```

Runs the monadic action `f` on each element of the collection `coll`.

def

```lean
[ForM.forIn.{u_1, u_2, u_3, u_4}]](#manual-ForM___forIn) {m : Type u_1 → Type u_2} {β : Type u_1}
  {ρ : Type u_3} {α : Type u_4} [[Monad]](#manual-Monad___mk) m]
  [[ForM]](#manual-ForM___mk) ([StateT](https://lean-lang.org/doc/reference/latest/Functors___-Monads-and--do--Notation/Varieties-of-Monads/#StateT) β ([ExceptT](https://lean-lang.org/doc/reference/latest/Functors___-Monads-and--do--Notation/Varieties-of-Monads/#ExceptT) β m)) ρ α] (x : ρ) (b : β)
  (f : α → β → m ([ForInStep]](#manual-ForInStep___done) β)) : m β



[ForM.forIn.{u_1, u_2, u_3, u_4}]](#manual-ForM___forIn)
  {m : Type u_1 → Type u_2} {β : Type u_1}
  {ρ : Type u_3} {α : Type u_4} [[Monad]](#manual-Monad___mk) m]
  [[ForM]](#manual-ForM___mk) ([StateT](https://lean-lang.org/doc/reference/latest/Functors___-Monads-and--do--Notation/Varieties-of-Monads/#StateT) β ([ExceptT](https://lean-lang.org/doc/reference/latest/Functors___-Monads-and--do--Notation/Varieties-of-Monads/#ExceptT) β m)) ρ α]
  (x : ρ) (b : β)
  (f : α → β → m ([ForInStep]](#manual-ForInStep___done) β)) : m β
```

Creates a suitable implementation of `[ForIn.forIn]](#manual-ForIn___mk)` from a `[ForM]](#manual-ForM___mk)` instance.

---



## Functors, Monads and do -Notation — 18.4. API Reference {#manual-functors-monads-and-do--notation-184-api-reference}

> 📄 Source: https://lean-lang.org/doc/reference/latest/Functors___-Monads-and--do--Notation/API-Reference/

In addition to the general functions described here, there are some functions that are conventionally defined as part of the API of in the namespace of each collection type:

- `mapM` maps a monadic function.
- `forM` maps a monadic function, throwing away the result.
- `filterM` filters using a monadic predicate, returning the values that satisfy it.

**Example: Monadic Collection Operations**

`[Array.filterM](https://lean-lang.org/doc/reference/latest/Basic-Types/Arrays/#Array___filterM)` can be used to write a filter that depends on a side effect.

```lean
def values := #[1, 2, 3, 5, 8]
def main : [IO](https://lean-lang.org/doc/reference/latest/IO/Logical-Model/#IO) [Unit](https://lean-lang.org/doc/reference/latest/Basic-Types/The-Unit-Type/#Unit) := [do]](#manual-Lean___Parser___Term___do)
let filtered ← [values]](#manual-values-_LPAR_in-Monadic-Collection-Operations_RPAR_).[filterM](https://lean-lang.org/doc/reference/latest/Basic-Types/Arrays/#Array___filterM) fun v => [do]](#manual-Lean___Parser___Term___do)
repeat
[IO.println](https://lean-lang.org/doc/reference/latest/IO/Console-Output/#IO___println) s!"Keep {v}? [y/n]"
let answer := (← (← [IO.getStdin](https://lean-lang.org/doc/reference/latest/IO/Files___-File-Handles___-and-Streams/#IO___getStdin)).[getLine](https://lean-lang.org/doc/reference/latest/IO/Files___-File-Handles___-and-Streams/#IO___FS___Stream___mk)).[trimAscii](https://lean-lang.org/doc/reference/latest/Basic-Types/Strings/#String___trimAscii).[copy](https://lean-lang.org/doc/reference/latest/Basic-Types/Strings/#String___Slice___copy)
if answer == "y" then return [true](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)
if answer == "n" then return [false](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)
return [false](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)
[IO.println](https://lean-lang.org/doc/reference/latest/IO/Console-Output/#IO___println) "These values were kept:"
for v in filtered do
[IO.println](https://lean-lang.org/doc/reference/latest/IO/Console-Output/#IO___println) s!" * {v}"
```

`stdin``y``n``oops``y``n``y`

`stdout``Keep 1? [y/n]``Keep 2? [y/n]``Keep 3? [y/n]``Keep 3? [y/n]``Keep 5? [y/n]``Keep 8? [y/n]``These values were kept:`` * 1`` * 3`` * 8`

### 18.4.1. Discarding Results {#manual-The-Lean-Language-Reference--Functors___-Monads-and--do--Notation--API-Reference--Discarding-Results}

The `[discard]](#manual-Functor___discard)` function is especially useful when using an action that returns a value only for its side effects.

def

```lean
[Functor.discard.{u, v}]](#manual-Functor___discard) {f : Type u → Type v} {α : Type u} [[Functor]](#manual-Functor___mk) f]
  (x : f α) : f [PUnit](https://lean-lang.org/doc/reference/latest/Basic-Types/The-Unit-Type/#PUnit___unit)



[Functor.discard.{u, v}]](#manual-Functor___discard)
  {f : Type u → Type v} {α : Type u}
  [[Functor]](#manual-Functor___mk) f] (x : f α) : f [PUnit](https://lean-lang.org/doc/reference/latest/Basic-Types/The-Unit-Type/#PUnit___unit)
```

Discards the value in a functor, retaining the functor's structure.

Discarding values is especially useful when using `[Applicative]](#manual-Applicative___mk)` functors or `[Monad]](#manual-Monad___mk)`s to implement
effects, and some operation should be carried out only for its effects. In `do`-notation, statements
whose values are discarded must return `[Unit](https://lean-lang.org/doc/reference/latest/Basic-Types/The-Unit-Type/#Unit)`, and `[discard]](#manual-Functor___discard)` can be used to explicitly discard their
values.

### 18.4.2. Control Flow {#manual-The-Lean-Language-Reference--Functors___-Monads-and--do--Notation--API-Reference--Control-Flow}

def

```lean
[guard.{v}]](#manual-guard) {f : Type → Type v} [[Alternative]](#manual-Alternative___mk) f] (p : Prop) [[Decidable]](#manual-Decidable___isFalse) p] :
  f [Unit](https://lean-lang.org/doc/reference/latest/Basic-Types/The-Unit-Type/#Unit)



[guard.{v}]](#manual-guard) {f : Type → Type v}
  [[Alternative]](#manual-Alternative___mk) f] (p : Prop)
  [[Decidable]](#manual-Decidable___isFalse) p] : f [Unit](https://lean-lang.org/doc/reference/latest/Basic-Types/The-Unit-Type/#Unit)
```

If the proposition `p` is true, does nothing, else fails (using `failure`).

def

```lean
[optional.{u, v}]](#manual-optional) {f : Type u → Type v} [[Alternative]](#manual-Alternative___mk) f] {α : Type u}
  (x : f α) : f ([Option](https://lean-lang.org/doc/reference/latest/Basic-Types/Optional-Values/#Option___none) α)



[optional.{u, v}]](#manual-optional) {f : Type u → Type v}
  [[Alternative]](#manual-Alternative___mk) f] {α : Type u} (x : f α) :
  f ([Option](https://lean-lang.org/doc/reference/latest/Basic-Types/Optional-Values/#Option___none) α)
```

Returns `[some](https://lean-lang.org/doc/reference/latest/Basic-Types/Optional-Values/#Option___none) x` if `f` succeeds with value `x`, else returns `[none](https://lean-lang.org/doc/reference/latest/Basic-Types/Optional-Values/#Option___none)`.

### 18.4.3. Lifting Boolean Operations {#manual-The-Lean-Language-Reference--Functors___-Monads-and--do--Notation--API-Reference--Lifting-Boolean-Operations}

def

```lean
[andM.{u, v}]](#manual-andM) {m : Type u → Type v} {β : Type u} [[Monad]](#manual-Monad___mk) m] [ToBool β]
  (x y : m β) : m β



[andM.{u, v}]](#manual-andM) {m : Type u → Type v}
  {β : Type u} [[Monad]](#manual-Monad___mk) m] [ToBool β]
  (x y : m β) : m β
```

Converts the result of the monadic action `x` to a `[Bool](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)`. If it is `[true](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)`, returns `y`; otherwise,
returns the original result of `x`.

This is a monadic counterpart to the short-circuiting `&&` operator, usually accessed via the `<&&>`
operator.

Conventions for notations in identifiers:

- The recommended spelling of `<&&>` in identifiers is `[andM]](#manual-andM)`.

def

```lean
[orM.{u, v}]](#manual-orM) {m : Type u → Type v} {β : Type u} [[Monad]](#manual-Monad___mk) m] [ToBool β]
  (x y : m β) : m β



[orM.{u, v}]](#manual-orM) {m : Type u → Type v}
  {β : Type u} [[Monad]](#manual-Monad___mk) m] [ToBool β]
  (x y : m β) : m β
```

Converts the result of the monadic action `x` to a `[Bool](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)`. If it is `[true](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)`, returns it and ignores
`y`; otherwise, runs `y` and returns its result.

This is a monadic counterpart to the short-circuiting `||` operator, usually accessed via the `<||>`
operator.

Conventions for notations in identifiers:

- The recommended spelling of `<||>` in identifiers is `[orM]](#manual-orM)`.

def

```lean
[notM.{v}]](#manual-notM) {m : Type → Type v} [[Functor]](#manual-Functor___mk) m] (x : m [Bool](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)) : m [Bool](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)



[notM.{v}]](#manual-notM) {m : Type → Type v} [[Functor]](#manual-Functor___mk) m]
  (x : m [Bool](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)) : m [Bool](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)
```

Runs a monadic action and returns the negation of its result.

### 18.4.4. Kleisli Composition {#manual-The-Lean-Language-Reference--Functors___-Monads-and--do--Notation--API-Reference--Kleisli-Composition}

*Kleisli composition* is the composition of monadic functions, analogous to `[Function.comp]](#manual-Function___comp)` for ordinary functions.

def

```lean
[Bind.kleisliRight.{u, u_1, u_2}]](#manual-Bind___kleisliRight) {α : Type u} {m : Type u_1 → Type u_2}
  {β γ : Type u_1} [[Bind]](#manual-Bind___mk) m] (f₁ : α → m β) (f₂ : β → m γ) (a : α) : m γ



[Bind.kleisliRight.{u, u_1, u_2}]](#manual-Bind___kleisliRight)
  {α : Type u} {m : Type u_1 → Type u_2}
  {β γ : Type u_1} [[Bind]](#manual-Bind___mk) m] (f₁ : α → m β)
  (f₂ : β → m γ) (a : α) : m γ
```

Left-to-right composition of Kleisli arrows.

Conventions for notations in identifiers:

- The recommended spelling of `>=>` in identifiers is `kleisliRight`.

def

```lean
[Bind.kleisliLeft.{u, u_1, u_2}]](#manual-Bind___kleisliLeft) {α : Type u} {m : Type u_1 → Type u_2}
  {β γ : Type u_1} [[Bind]](#manual-Bind___mk) m] (f₂ : β → m γ) (f₁ : α → m β) (a : α) : m γ



[Bind.kleisliLeft.{u, u_1, u_2}]](#manual-Bind___kleisliLeft)
  {α : Type u} {m : Type u_1 → Type u_2}
  {β γ : Type u_1} [[Bind]](#manual-Bind___mk) m] (f₂ : β → m γ)
  (f₁ : α → m β) (a : α) : m γ
```

Right-to-left composition of Kleisli arrows.

Conventions for notations in identifiers:

- The recommended spelling of `<=<` in identifiers is `kleisliLeft`.

### 18.4.5. Re-Ordered Operations {#manual-The-Lean-Language-Reference--Functors___-Monads-and--do--Notation--API-Reference--Re-Ordered-Operations}

Sometimes, it can be convenient to partially apply a function to its second argument.
These functions reverse the order of arguments, making it this easier.

def

```lean
[Functor.mapRev.{u, v}]](#manual-Functor___mapRev) {f : Type u → Type v} [[Functor]](#manual-Functor___mk) f] {α β : Type u} :
  f α → (α → β) → f β



[Functor.mapRev.{u, v}]](#manual-Functor___mapRev)
  {f : Type u → Type v} [[Functor]](#manual-Functor___mk) f]
  {α β : Type u} : f α → (α → β) → f β
```

Maps a function over a functor, with parameters swapped so that the function comes last.

This function is `[Functor.map]](#manual-Functor___mk)` with the parameters reversed, typically used via the `<&>` operator.

Conventions for notations in identifiers:

- The recommended spelling of `<&>` in identifiers is `mapRev`.

def

```lean
[Bind.bindLeft.{u, u_1}]](#manual-Bind___bindLeft) {α : Type u} {m : Type u → Type u_1} {β : Type u}
  [[Bind]](#manual-Bind___mk) m] (f : α → m β) (ma : m α) : m β



[Bind.bindLeft.{u, u_1}]](#manual-Bind___bindLeft) {α : Type u}
  {m : Type u → Type u_1} {β : Type u}
  [[Bind]](#manual-Bind___mk) m] (f : α → m β) (ma : m α) : m β
```

Same as `[Bind.bind]](#manual-Bind___mk)` but with arguments swapped.

Conventions for notations in identifiers:

- The recommended spelling of `=<<` in identifiers is `bindLeft`.

---



## Functors, Monads and do -Notation — 18.5. Varieties of Monads {#manual-functors-monads-and-do--notation-185-varieties-of-monads}

> 📄 Source: https://lean-lang.org/doc/reference/latest/Functors___-Monads-and--do--Notation/Varieties-of-Monads/

The `[IO](https://lean-lang.org/doc/reference/latest/IO/Logical-Model/#IO)` monad has many, many effects, and is used for writing programs that need to interact with the world.
It is described in [its own section](https://lean-lang.org/doc/reference/latest/IO/#io).
Programs that use `[IO](https://lean-lang.org/doc/reference/latest/IO/Logical-Model/#IO)` are essentially black boxes: they are typically not particularly amenable to verification.

Many algorithms are easiest to express with a much smaller set of effects.
These effects can often be simulated; for example, mutable state can be simulated by passing around a tuple that contains both the program's value and the state.
These simulated effects are easier to reason formally about, because they are defined using ordinary code rather than new language primitives.

The standard library provides abstractions for working with commonly-used effects.
Many frequently-used effects fall into a small number of categories:

State monads have mutable state
:   Computations that have access to some data that may be modified by other parts of the computation use *mutable state*.
    State can be implemented in a variety of ways, described in the section on [state monads]](#manual-state-monads) and captured in the `[MonadState]](#manual-MonadState___mk)` type class.

Reader monads are parameterized computations
:   Computations that can read the value of some parameter provided by a context exist in most programming languages, but many languages that feature state and exceptions as first-class features do not have built-in facilities for defining new parameterized computations.
    Typically, these computations are provided with a parameter value when invoked, and sometimes they can locally override it.
    Parameter values have *dynamic extent*: the value provided most recently in the call stack is the one that is used.
    They can be simulated by passing a value unchanged through a sequence of function calls; however, this technique can make code harder to read and introduces a risk that the values may be passed incorrectly to further calls by mistake.
    They can also be simulated using mutable state with a careful discipline surrounding the modification of the state.
    Monads that maintain a parameter, potentially allowing it to be overridden in a section of the call stack, are called *reader monads*.
    Reader monads are captured in the `[MonadReader]](#manual-MonadReader___mk)` type class.
    Additionally, reader monads that allow the parameter value to be locally overridden are captured in the `[MonadWithReader]](#manual-MonadWithReader___mk)` type class.

Exception monads have exceptions
:   Computations that may terminate early with an exceptional value use *exceptions*.
    They are typically modeled with a sum type that has a constructor for ordinary termination and a constructor for early termination with errors.
    Exception monads are described in the section on [exception monads]](#manual-exception-monads), and captured in the `[MonadExcept]](#manual-MonadExcept___mk)` type class.

### 18.5.1. Monad Type Classes {#manual-The-Lean-Language-Reference--Functors___-Monads-and--do--Notation--Varieties-of-Monads--Monad-Type-Classes}

Using type classes like `[MonadState]](#manual-MonadState___mk)` and `[MonadExcept]](#manual-MonadExcept___mk)` allow client code to be polymorphic with respect to monads.
Together with automatic lifting, this allows programs to be reusable in many different monads and makes them more robust to refactoring.

It's important to be aware that effects in a monad may not interact in only one way.
For example, a monad with state and exceptions may or may not roll back state changes when an exception is thrown.
If this matters for the correctness of a function, then it should use a more specific signature.

**Example: Effect Ordering**

The function `[sumNonFives]](#manual-sumNonFives-_LPAR_in-Effect-Ordering_RPAR_)` adds the contents of a list using a state monad, terminating early if it encounters a `5`.

```lean
def sumNonFives {m}
[[Monad]](#manual-Monad___mk) m] [[MonadState]](#manual-MonadState___mk) [Nat](https://lean-lang.org/doc/reference/latest/Basic-Types/Natural-Numbers/#Nat___zero) m] [[MonadExcept]](#manual-MonadExcept___mk) [String](https://lean-lang.org/doc/reference/latest/Basic-Types/Strings/#String___ofByteArray) m]
(xs : [List](https://lean-lang.org/doc/reference/latest/Basic-Types/Linked-Lists/#List___nil) [Nat](https://lean-lang.org/doc/reference/latest/Basic-Types/Natural-Numbers/#Nat___zero)) :
m [Unit](https://lean-lang.org/doc/reference/latest/Basic-Types/The-Unit-Type/#Unit) := [do]](#manual-Lean___Parser___Term___do)
for x in xs do
if x == 5 then
[throw]](#manual-MonadExcept___mk) "Five was encountered"
else
[modify]](#manual-modify) (· + x)
```

Running it in one monad returns the state at the time that `5` was encountered:

```lean
[#eval]](#manual-Lean___Parser___Command___eval)
[sumNonFives]](#manual-sumNonFives-_LPAR_in-Effect-Ordering_RPAR_) (m := [ExceptT]](#manual-ExceptT) [String](https://lean-lang.org/doc/reference/latest/Basic-Types/Strings/#String___ofByteArray) ([StateM]](#manual-StateM) [Nat](https://lean-lang.org/doc/reference/latest/Basic-Types/Natural-Numbers/#Nat___zero)))
[1, 2, 3, 4, 5, 6] |>.[run]](#manual-ExceptT___run) |>.[run]](#manual-StateT___run) 0
```

```lean
(Except.error "Five was encountered", 10)
```

In another, the state is discarded:

```lean
[#eval]](#manual-Lean___Parser___Command___eval)
[sumNonFives]](#manual-sumNonFives-_LPAR_in-Effect-Ordering_RPAR_) (m := [StateT]](#manual-StateT) [Nat](https://lean-lang.org/doc/reference/latest/Basic-Types/Natural-Numbers/#Nat___zero) ([Except]](#manual-Except___error) [String](https://lean-lang.org/doc/reference/latest/Basic-Types/Strings/#String___ofByteArray)))
[1, 2, 3, 4, 5, 6] |>.[run]](#manual-StateT___run) 0
```

```lean
Except.error "Five was encountered"
```

In the second case, an exception handler would roll back the state to its value at the start of the `try`.
The following function is thus incorrect:

```lean
/-- Computes the sum of the non-5 prefix of a list. -/
def sumUntilFive {m}
[[Monad]](#manual-Monad___mk) m] [[MonadState]](#manual-MonadState___mk) [Nat](https://lean-lang.org/doc/reference/latest/Basic-Types/Natural-Numbers/#Nat___zero) m] [[MonadExcept]](#manual-MonadExcept___mk) [String](https://lean-lang.org/doc/reference/latest/Basic-Types/Strings/#String___ofByteArray) m]
(xs : [List](https://lean-lang.org/doc/reference/latest/Basic-Types/Linked-Lists/#List___nil) [Nat](https://lean-lang.org/doc/reference/latest/Basic-Types/Natural-Numbers/#Nat___zero)) :
m [Nat](https://lean-lang.org/doc/reference/latest/Basic-Types/Natural-Numbers/#Nat___zero) := [do]](#manual-Lean___Parser___Term___do)
[MonadState.set]](#manual-MonadState___mk) 0
try
[sumNonFives]](#manual-sumNonFives-_LPAR_in-Effect-Ordering_RPAR_) xs
catch _ =>
[pure]](#manual-Pure___mk) ()
get
```

In one monad, the answer is correct:

```lean
[#eval]](#manual-Lean___Parser___Command___eval)
[sumUntilFive]](#manual-sumUntilFive-_LPAR_in-Effect-Ordering_RPAR_) (m := [ExceptT]](#manual-ExceptT) [String](https://lean-lang.org/doc/reference/latest/Basic-Types/Strings/#String___ofByteArray) ([StateM]](#manual-StateM) [Nat](https://lean-lang.org/doc/reference/latest/Basic-Types/Natural-Numbers/#Nat___zero)))
[1, 2, 3, 4, 5, 6] |>.[run]](#manual-ExceptT___run) |>.[run']](#manual-StateT___run___) 0
```

```lean
Except.ok 10
```

In the other, it is not:

```lean
[#eval]](#manual-Lean___Parser___Command___eval)
[sumUntilFive]](#manual-sumUntilFive-_LPAR_in-Effect-Ordering_RPAR_) (m := [StateT]](#manual-StateT) [Nat](https://lean-lang.org/doc/reference/latest/Basic-Types/Natural-Numbers/#Nat___zero) ([Except]](#manual-Except___error) [String](https://lean-lang.org/doc/reference/latest/Basic-Types/Strings/#String___ofByteArray)))
[1, 2, 3, 4, 5, 6] |>.[run']](#manual-StateT___run___) 0
```

```lean
Except.ok 0
```

A single monad may support multiple version of the same effect.
For example, there might be a mutable `[Nat](https://lean-lang.org/doc/reference/latest/Basic-Types/Natural-Numbers/#Nat___zero)` and a mutable `[String](https://lean-lang.org/doc/reference/latest/Basic-Types/Strings/#String___ofByteArray)` or two separate reader parameters.
As long as they have different types, it should be convenient to access both.
In typical use, some monadic operations that are overloaded in type classes have type information available for [instance synthesis]](#manual---tech-term-synthesizes), while others do not.
For example, the argument passed to `[set]](#manual-MonadState___mk)` determines the type of the state to be used, while `get` takes no such argument.
The type information present in applications of `[set]](#manual-MonadState___mk)` can be used to pick the correct instance when multiple states are available, which suggests that the type of the mutable state should be an input parameter or [semi-output parameter]](#manual---tech-term-Semi-output-parameters) so that it can be used to select instances.
The lack of type information present in uses of `get`, on the other hand, suggests that the type of the mutable state should be an [output parameter]](#manual---tech-term-output-parameter) in `[MonadState]](#manual-MonadState___mk)`, so type class synthesis determines the state's type from the monad itself.

This dichotomy is solved by having two versions of many of the effect type classes.
The version with a semi-output parameter has the suffix `-Of`, and its operations take types explicitly as needed.
Examples include `[MonadStateOf]](#manual-MonadStateOf___mk)`, `[MonadReaderOf]](#manual-MonadReaderOf___mk)`, and `[MonadExceptOf]](#manual-MonadExceptOf___mk)`.
The operations with explicit type parameters have names ending in `-The`, such as `[getThe]](#manual-getThe)`, `[readThe]](#manual-readThe)`, and `[tryCatchThe]](#manual-tryCatchThe)`.
The name of the version with an output parameter is undecorated.
The standard library exports a mix of operations from the `-Of` and undecorated versions of each type class, based on what has good inference behavior in typical use cases.

| Operation | From Class | Notes |
| --- | --- | --- |
| `get` | `[MonadState]](#manual-MonadState___mk)` | Output parameter improves type inference |
| `[set]](#manual-MonadStateOf___mk)` | `[MonadStateOf]](#manual-MonadStateOf___mk)` | Semi-output parameter uses type information from `[set]](#manual-MonadStateOf___mk)`'s argument |
| `[modify]](#manual-modify)` | `[MonadState]](#manual-MonadState___mk)` | Output parameter is needed to allow functions without annotations |
| `modifyGet` | `[MonadState]](#manual-MonadState___mk)` | Output parameter is needed to allow functions without annotations |
| `[read]](#manual-MonadReader___mk)` | `[MonadReader]](#manual-MonadReader___mk)` | Output parameter is needed due to lack of type information from arguments |
| `[readThe]](#manual-readThe)` | `[MonadReaderOf]](#manual-MonadReaderOf___mk)` | Semi-output parameter uses the provided type to guide synthesis |
| `[withReader]](#manual-MonadWithReader___mk)` | `[MonadWithReader]](#manual-MonadWithReader___mk)` | Output parameter avoids the need for type annotations on the function |
| `[withTheReader]](#manual-withTheReader)` | `[MonadWithReaderOf]](#manual-MonadWithReaderOf___mk)` | Semi-output parameter uses provided type to guide synthesis |
| `[throw]](#manual-MonadExcept___mk)` | `[MonadExcept]](#manual-MonadExcept___mk)` | Output parameter enables the use of constructor dot notation for the exception |
| `[throwThe]](#manual-throwThe)` | `[MonadExceptOf]](#manual-MonadExceptOf___mk)` | Semi-output parameter uses provided type to guide synthesis |
| `[tryCatch]](#manual-MonadExcept___mk)` | `[MonadExcept]](#manual-MonadExcept___mk)` | Output parameter enables the use of constructor dot notation for the exception |
| `[tryCatchThe]](#manual-tryCatchThe)` | `[MonadExceptOf]](#manual-MonadExceptOf___mk)` | Semi-output parameter uses provided type to guide synthesis |

**Example: State Types**

The state monad `[M]](#manual-M-_LPAR_in-State-Types_RPAR_)` has two separate states: a `[Nat](https://lean-lang.org/doc/reference/latest/Basic-Types/Natural-Numbers/#Nat___zero)` and a `[String](https://lean-lang.org/doc/reference/latest/Basic-Types/Strings/#String___ofByteArray)`.

```lean
abbrev M := [StateT]](#manual-StateT) [Nat](https://lean-lang.org/doc/reference/latest/Basic-Types/Natural-Numbers/#Nat___zero) ([StateM]](#manual-StateM) [String](https://lean-lang.org/doc/reference/latest/Basic-Types/Strings/#String___ofByteArray))
```

Because `get` is an alias for `MonadState.get`, the state type is an output parameter.
This means that Lean selects a state type automatically, in this case the one from the outermost monad transformer:

```lean
[#check]](#manual-Lean___Parser___Command___check) (get : [M]](#manual-M-_LPAR_in-State-Types_RPAR_) _)
```

```lean
get : [M]](#manual-M-_LPAR_in-State-Types_RPAR_) [Nat](https://lean-lang.org/doc/reference/latest/Basic-Types/Natural-Numbers/#Nat___zero)
```

Only the outermost may be used, because the type of the state is an output parameter.

```lean
[#check]](#manual-Lean___Parser___Command___check) (get : [M]](#manual-M-_LPAR_in-State-Types_RPAR_) [String](https://lean-lang.org/doc/reference/latest/Basic-Types/Strings/#String___ofByteArray))
```

```lean
failed to synthesize instance of type class
  [MonadState]](#manual-MonadState___mk) [String](https://lean-lang.org/doc/reference/latest/Basic-Types/Strings/#String___ofByteArray) [M]](#manual-M-_LPAR_in-State-Types_RPAR_)

Hint: Type class instance resolution failures can be inspected with the `set_option trace.Meta.synthInstance true` command.
```

Providing the state type explicitly using `[getThe]](#manual-getThe)` from `[MonadStateOf]](#manual-MonadStateOf___mk)` allows both states to be read.

```lean
[#check]](#manual-Lean___Parser___Command___check) (([getThe]](#manual-getThe) [String](https://lean-lang.org/doc/reference/latest/Basic-Types/Strings/#String___ofByteArray), [getThe]](#manual-getThe) [Nat](https://lean-lang.org/doc/reference/latest/Basic-Types/Natural-Numbers/#Nat___zero)) : [M]](#manual-M-_LPAR_in-State-Types_RPAR_) [String](https://lean-lang.org/doc/reference/latest/Basic-Types/Strings/#String___ofByteArray) × [M]](#manual-M-_LPAR_in-State-Types_RPAR_) [Nat](https://lean-lang.org/doc/reference/latest/Basic-Types/Natural-Numbers/#Nat___zero))
```

```lean
[(](https://lean-lang.org/doc/reference/latest/Basic-Types/Tuples/#Prod___mk)[getThe]](#manual-getThe) [String](https://lean-lang.org/doc/reference/latest/Basic-Types/Strings/#String___ofByteArray)[,](https://lean-lang.org/doc/reference/latest/Basic-Types/Tuples/#Prod___mk) [getThe]](#manual-getThe) [Nat](https://lean-lang.org/doc/reference/latest/Basic-Types/Natural-Numbers/#Nat___zero)[)](https://lean-lang.org/doc/reference/latest/Basic-Types/Tuples/#Prod___mk) : [M]](#manual-M-_LPAR_in-State-Types_RPAR_) [String](https://lean-lang.org/doc/reference/latest/Basic-Types/Strings/#String___ofByteArray) [×](https://lean-lang.org/doc/reference/latest/Basic-Types/Tuples/#Prod___mk) [M]](#manual-M-_LPAR_in-State-Types_RPAR_) [Nat](https://lean-lang.org/doc/reference/latest/Basic-Types/Natural-Numbers/#Nat___zero)
```

Setting a state works for either type, because the state type is a [semi-output parameter]](#manual---tech-term-Semi-output-parameters) on `[MonadStateOf]](#manual-MonadStateOf___mk)`.

```lean
[#check]](#manual-Lean___Parser___Command___check) ([set]](#manual-MonadStateOf___mk) 4 : [M]](#manual-M-_LPAR_in-State-Types_RPAR_) [Unit](https://lean-lang.org/doc/reference/latest/Basic-Types/The-Unit-Type/#Unit))
```

```lean
[set]](#manual-MonadStateOf___mk) 4 : [M]](#manual-M-_LPAR_in-State-Types_RPAR_) [PUnit](https://lean-lang.org/doc/reference/latest/Basic-Types/The-Unit-Type/#PUnit___unit)
```

```lean
[#check]](#manual-Lean___Parser___Command___check) ([set]](#manual-MonadStateOf___mk) "Four" : [M]](#manual-M-_LPAR_in-State-Types_RPAR_) [Unit](https://lean-lang.org/doc/reference/latest/Basic-Types/The-Unit-Type/#Unit))
```

```lean
[set]](#manual-MonadStateOf___mk) "Four" : [M]](#manual-M-_LPAR_in-State-Types_RPAR_) [PUnit](https://lean-lang.org/doc/reference/latest/Basic-Types/The-Unit-Type/#PUnit___unit)
```

### 18.5.2. Monad Transformers {#manual-monad-transformers}

A *monad transformer* is a function that, when provided with a monad, gives back a new monad.
Typically, this new monad has all the effects of the original monad along with some additional ones.

A monad transformer consists of the following:

- A function `T` that constructs the new monad's type from an existing monad
- A `run` function that adapts a `T m α` into some variant of `m`, often requiring additional parameters and returning a more specific type under `m`
- An instance of `[[Monad]](#manual-Monad___mk) m] → [Monad]](#manual-Monad___mk) (T m)` that allows the transformed monad to be used as a monad
- An instance of `[MonadLift]](#manual-MonadLift___mk)` that allows the original monad's code to be used in the transformed monad
- If possible, an instance of `[MonadControl]](#manual-MonadControl___mk) m (T m)` that allows actions from the transformed monad to be used in the original monad

Typically, a monad transformer also provides instances of one or more type classes that describe the effects that it introduces.
The transformer's `[Monad]](#manual-Monad___mk)` and `[MonadLift]](#manual-MonadLift___mk)` instances make it practical to write code in the transformed monad, while the type class instances allow the transformed monad to be used with polymorphic functions.

**Example: The Identity Monad Transformer**

The identity monad transformer neither adds nor removes capabilities to the transformed monad.
Its definition is the identity function, suitably specialized:

```lean
def IdT (m : Type u → Type v) : Type u → Type v := m
```

Similarly, the `[run]](#manual-IdT___run-_LPAR_in-The-Identity-Monad-Transformer-_RPAR_)` function requires no additional arguments and just returns an `m α`:

```lean
def IdT.run (act : [IdT]](#manual-IdT-_LPAR_in-The-Identity-Monad-Transformer-_RPAR_) m α) : m α := act
```

The monad instance relies on the monad instance for the transformed monad, selecting it via [type ascriptions]](#manual---tech-term-Type-ascriptions):

```lean
instance [[Monad]](#manual-Monad___mk) m] : [Monad]](#manual-Monad___mk) ([IdT]](#manual-IdT-_LPAR_in-The-Identity-Monad-Transformer-_RPAR_) m) where
[pure]](#manual-Pure___mk) x := ([pure]](#manual-Pure___mk) x : m _)
[bind]](#manual-Bind___mk) x f := (x >>= f : m _)
```

Because `[IdT]](#manual-IdT-_LPAR_in-The-Identity-Monad-Transformer-_RPAR_) m` is definitionally equal to `m`, the `[MonadLift]](#manual-MonadLift___mk) m ([IdT]](#manual-IdT-_LPAR_in-The-Identity-Monad-Transformer-_RPAR_) m)` instance doesn't need to modify the action being lifted:

```lean
instance : [MonadLift]](#manual-MonadLift___mk) m ([IdT]](#manual-IdT-_LPAR_in-The-Identity-Monad-Transformer-_RPAR_) m) where
[monadLift]](#manual-MonadLift___mk) x := x
```

The `[MonadControl]](#manual-MonadControl___mk)` instance is similarly simple.

```lean
instance [[Monad]](#manual-Monad___mk) m] : [MonadControl]](#manual-MonadControl___mk) m ([IdT]](#manual-IdT-_LPAR_in-The-Identity-Monad-Transformer-_RPAR_) m) where
[stM]](#manual-MonadControl___mk) α := α
[liftWith]](#manual-MonadControl___mk) f := f (fun x => [Id.run]](#manual-Id___run) <| [pure]](#manual-Pure___mk) x)
[restoreM]](#manual-MonadControl___mk) v := v
```

The Lean standard library provides transformer versions of many different monads, including `[ReaderT]](#manual-ReaderT)`, `[ExceptT]](#manual-ExceptT)`, and `[StateT]](#manual-StateT)`, along with variants using other representations such as `[StateCpsT]](#manual-StateCpsT)`, `[StateRefT]](#manual-StateRefT___)`, and `[ExceptCpsT]](#manual-ExceptCpsT)`.
Additionally, the `[EStateM]](#manual-EStateM)` monad is equivalent to combining `[ExceptT]](#manual-ExceptT)` and `[StateT]](#manual-StateT)`, but it can use a more specialized representation to improve performance.

### 18.5.3. Identity {#manual-The-Lean-Language-Reference--Functors___-Monads-and--do--Notation--Varieties-of-Monads--Identity}

The identity monad `[Id]](#manual-Id)` has no effects whatsoever.
Both `[Id]](#manual-Id)` and the corresponding implementation of `[pure]](#manual-Pure___mk)` are the identity function, and `[bind]](#manual-Bind___mk)` is reversed function application.
The identity monad has two primary use cases:

1. It can be the type of a [`do`]](#manual-Lean___Parser___Term___do) block that implements a pure function with local effects.
2. It can be placed at the bottom of a stack of monad transformers.

def

```lean
[Id.{u}]](#manual-Id) (type : Type u) : Type u



[Id.{u}]](#manual-Id) (type : Type u) : Type u
```

The identity function on types, used primarily for its `[Monad]](#manual-Monad___mk)` instance.

The identity monad is useful together with monad transformers to construct monads for particular
purposes. Additionally, it can be used with `do`-notation in order to use control structures such as
local mutability, `for`-loops, and early returns in code that does not otherwise use monads.

Examples:

```lean
def containsFive (xs : [List](https://lean-lang.org/doc/reference/latest/Basic-Types/Linked-Lists/#List___nil) [Nat](https://lean-lang.org/doc/reference/latest/Basic-Types/Natural-Numbers/#Nat___zero)) : [Bool](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false) := [Id.run]](#manual-Id___run) [do]](#manual-Lean___Parser___Term___do)
for x in xs do
if x == 5 then return [true](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)
return [false](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)
```

```
#eval containsFive [1, 3, 5, 7]
```

```lean
[true](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)
```

def

```lean
[Id.run.{u_1}]](#manual-Id___run) {α : Type u_1} (x : [Id]](#manual-Id) α) : α



[Id.run.{u_1}]](#manual-Id___run) {α : Type u_1} (x : [Id]](#manual-Id) α) : α
```

Runs a computation in the identity monad.

This function is the identity function. Because its parameter has type `[Id]](#manual-Id) α`, it causes
`do`-notation in its arguments to use the `[Monad]](#manual-Monad___mk) [Id]](#manual-Id)` instance.

**Example: Local Effects with the Identity Monad**

This code block implements a countdown procedure by using simulated local mutability in the identity monad.

```lean
[#eval]](#manual-Lean___Parser___Command___eval) [Id.run]](#manual-Id___run) [do]](#manual-Lean___Parser___Term___do)
let mut xs := []
for x in [0:10] do
xs := x :: xs
[pure]](#manual-Pure___mk) xs
```

```lean
[[](https://lean-lang.org/doc/reference/latest/Basic-Types/Linked-Lists/#List___nil)9[,](https://lean-lang.org/doc/reference/latest/Basic-Types/Linked-Lists/#List___nil) 8[,](https://lean-lang.org/doc/reference/latest/Basic-Types/Linked-Lists/#List___nil) 7[,](https://lean-lang.org/doc/reference/latest/Basic-Types/Linked-Lists/#List___nil) 6[,](https://lean-lang.org/doc/reference/latest/Basic-Types/Linked-Lists/#List___nil) 5[,](https://lean-lang.org/doc/reference/latest/Basic-Types/Linked-Lists/#List___nil) 4[,](https://lean-lang.org/doc/reference/latest/Basic-Types/Linked-Lists/#List___nil) 3[,](https://lean-lang.org/doc/reference/latest/Basic-Types/Linked-Lists/#List___nil) 2[,](https://lean-lang.org/doc/reference/latest/Basic-Types/Linked-Lists/#List___nil) 1[,](https://lean-lang.org/doc/reference/latest/Basic-Types/Linked-Lists/#List___nil) 0[]](https://lean-lang.org/doc/reference/latest/Basic-Types/Linked-Lists/#List___nil)
```

### 18.5.4. State {#manual-state-monads}

[State monads]](#manual---tech-term-State-monads) provide access to a mutable value.
The underlying implementation may use a tuple to simulate mutability, or it may use something like `[ST.Ref](https://lean-lang.org/doc/reference/latest/IO/Mutable-References/#ST___Ref___mk)` to ensure mutation.
Even those implementations that use a tuple may in fact use mutation at run-time due to Lean's use of mutation when there are unique references to values, but this requires a programming style that prefers `[modify]](#manual-modify)` and `modifyGet` over `get` and `[set]](#manual-MonadStateOf___mk)`.

#### 18.5.4.1. General State API {#manual-The-Lean-Language-Reference--Functors___-Monads-and--do--Notation--Varieties-of-Monads--State--General-State-API}

type class

```lean
[MonadState.{u, v}]](#manual-MonadState___mk) (σ : [outParam]](#manual-outParam) (Type u)) (m : Type u → Type v) :
  Type (max (u + 1) v)



[MonadState.{u, v}]](#manual-MonadState___mk) (σ : [outParam]](#manual-outParam) (Type u))
  (m : Type u → Type v) :
  Type (max (u + 1) v)
```

State monads provide a value of a given type (the *state*) that can be retrieved or replaced.
Instances may implement these operations by passing state values around, by using a mutable
reference cell (e.g. `[ST.Ref](https://lean-lang.org/doc/reference/latest/IO/Mutable-References/#ST___Ref___mk) σ`), or in other ways.

In this class, `σ` is an `[outParam]](#manual-outParam)`, which means that it is inferred from `m`. `[MonadStateOf]](#manual-MonadStateOf___mk) σ`
provides the same operations, but allows `σ` to influence instance synthesis.

The mutable state of a state monad is visible between multiple `do`-blocks or functions, unlike
[local mutable state](https://lean-lang.org/doc/reference/4.34.0-rc1/find/?domain=Verso.Genre.Manual.section&name=do-notation-let-mut) in `do`-notation.

Instance Constructor

```lean
[MonadState.mk]](#manual-MonadState___mk).{u, v}
```

Methods

```lean
get : m σ
```

Retrieves the current value of the monad's mutable state.

```lean
set : σ → m [PUnit](https://lean-lang.org/doc/reference/latest/Basic-Types/The-Unit-Type/#PUnit___unit)
```

Replaces the current value of the mutable state with a new one.

```lean
modifyGet : {α : Type u} → (σ → α [×](https://lean-lang.org/doc/reference/latest/Basic-Types/Tuples/#Prod___mk) σ) → m α
```

Applies a function to the current state that both computes a new state and a value. The new state
replaces the current state, and the value is returned.

It is equivalent to `[do]](#manual-Lean___Parser___Term___do) let (a, s) := f (← get); set s; pure a`. However, using `modifyGet` may
lead to higher performance because it doesn't add a new reference to the state value. Additional
references can inhibit in-place updates of data.

def

```lean
MonadState.get.{u, v} {σ : [outParam]](#manual-outParam) (Type u)} {m : Type u → Type v}
  [self : [MonadState]](#manual-MonadState___mk) σ m] : m σ



MonadState.get.{u, v}
  {σ : [outParam]](#manual-outParam) (Type u)}
  {m : Type u → Type v}
  [self : [MonadState]](#manual-MonadState___mk) σ m] : m σ
```

Retrieves the current value of the monad's mutable state.

def

```lean
[modify.{u, v}]](#manual-modify) {σ : Type u} {m : Type u → Type v} [[MonadState]](#manual-MonadState___mk) σ m]
  (f : σ → σ) : m [PUnit](https://lean-lang.org/doc/reference/latest/Basic-Types/The-Unit-Type/#PUnit___unit)



[modify.{u, v}]](#manual-modify) {σ : Type u}
  {m : Type u → Type v} [[MonadState]](#manual-MonadState___mk) σ m]
  (f : σ → σ) : m [PUnit](https://lean-lang.org/doc/reference/latest/Basic-Types/The-Unit-Type/#PUnit___unit)
```

Mutates the current state, replacing its value with the result of applying `f` to it.

Use `[modifyThe]](#manual-modifyThe)` to explicitly select a state type to modify.

It is equivalent to `do set (f (← get))`. However, using `[modify]](#manual-modify)` may lead to higher performance
because it doesn't add a new reference to the state value. Additional references can inhibit
in-place updates of data.

def

```lean
MonadState.modifyGet.{u, v} {σ : [outParam]](#manual-outParam) (Type u)}
  {m : Type u → Type v} [self : [MonadState]](#manual-MonadState___mk) σ m] {α : Type u} :
  (σ → α [×](https://lean-lang.org/doc/reference/latest/Basic-Types/Tuples/#Prod___mk) σ) → m α



MonadState.modifyGet.{u, v}
  {σ : [outParam]](#manual-outParam) (Type u)}
  {m : Type u → Type v}
  [self : [MonadState]](#manual-MonadState___mk) σ m] {α : Type u} :
  (σ → α [×](https://lean-lang.org/doc/reference/latest/Basic-Types/Tuples/#Prod___mk) σ) → m α
```

Applies a function to the current state that both computes a new state and a value. The new state
replaces the current state, and the value is returned.

It is equivalent to `[do]](#manual-Lean___Parser___Term___do) let (a, s) := f (← get); set s; pure a`. However, using `modifyGet` may
lead to higher performance because it doesn't add a new reference to the state value. Additional
references can inhibit in-place updates of data.

def

```lean
[getModify.{u, v}]](#manual-getModify) {σ : Type u} {m : Type u → Type v} [[MonadState]](#manual-MonadState___mk) σ m]
  (f : σ → σ) : m σ



[getModify.{u, v}]](#manual-getModify) {σ : Type u}
  {m : Type u → Type v} [[MonadState]](#manual-MonadState___mk) σ m]
  (f : σ → σ) : m σ
```

Replaces the state with the result of applying `f` to it. Returns the old value of the state.

It is equivalent to `get <* modify f` but may be more efficient.

type class

```lean
[MonadStateOf.{u, v}]](#manual-MonadStateOf___mk) (σ : [semiOutParam]](#manual-semiOutParam) (Type u)) (m : Type u → Type v) :
  Type (max (u + 1) v)



[MonadStateOf.{u, v}]](#manual-MonadStateOf___mk)
  (σ : [semiOutParam]](#manual-semiOutParam) (Type u))
  (m : Type u → Type v) :
  Type (max (u + 1) v)
```

State monads provide a value of a given type (the *state*) that can be retrieved or replaced.
Instances may implement these operations by passing state values around, by using a mutable
reference cell (e.g. `[ST.Ref](https://lean-lang.org/doc/reference/latest/IO/Mutable-References/#ST___Ref___mk) σ`), or in other ways.

In this class, `σ` is a `[semiOutParam]](#manual-semiOutParam)`, which means that it can influence the choice of instance.
`[MonadState]](#manual-MonadState___mk) σ` provides the same operations, but requires that `σ` be inferable from `m`.

The mutable state of a state monad is visible between multiple `do`-blocks or functions, unlike
[local mutable state](https://lean-lang.org/doc/reference/4.34.0-rc1/find/?domain=Verso.Genre.Manual.section&name=do-notation-let-mut) in `do`-notation.

Instance Constructor

```lean
[MonadStateOf.mk]](#manual-MonadStateOf___mk).{u, v}
```

Methods

```lean
get : m σ
```

Retrieves the current value of the monad's mutable state.

```lean
set : σ → m [PUnit](https://lean-lang.org/doc/reference/latest/Basic-Types/The-Unit-Type/#PUnit___unit)
```

Replaces the current value of the mutable state with a new one.

```lean
modifyGet : {α : Type u} → (σ → α [×](https://lean-lang.org/doc/reference/latest/Basic-Types/Tuples/#Prod___mk) σ) → m α
```

Applies a function to the current state that both computes a new state and a value. The new state
replaces the current state, and the value is returned.

It is equivalent to `[do]](#manual-Lean___Parser___Term___do) let (a, s) := f (← get); set s; pure a`. However, using `modifyGet` may
lead to higher performance because it doesn't add a new reference to the state value. Additional
references can inhibit in-place updates of data.

def

```lean
[getThe.{u, v}]](#manual-getThe) (σ : Type u) {m : Type u → Type v} [[MonadStateOf]](#manual-MonadStateOf___mk) σ m] :
  m σ



[getThe.{u, v}]](#manual-getThe) (σ : Type u)
  {m : Type u → Type v}
  [[MonadStateOf]](#manual-MonadStateOf___mk) σ m] : m σ
```

Gets the current state that has the explicitly-provided type `σ`. When the current monad has
multiple state types available, this function selects one of them.

def

```lean
[modifyThe.{u, v}]](#manual-modifyThe) (σ : Type u) {m : Type u → Type v} [[MonadStateOf]](#manual-MonadStateOf___mk) σ m]
  (f : σ → σ) : m [PUnit](https://lean-lang.org/doc/reference/latest/Basic-Types/The-Unit-Type/#PUnit___unit)



[modifyThe.{u, v}]](#manual-modifyThe) (σ : Type u)
  {m : Type u → Type v} [[MonadStateOf]](#manual-MonadStateOf___mk) σ m]
  (f : σ → σ) : m [PUnit](https://lean-lang.org/doc/reference/latest/Basic-Types/The-Unit-Type/#PUnit___unit)
```

Mutates the current state that has the explicitly-provided type `σ`, replacing its value with the
result of applying `f` to it. When the current monad has multiple state types available, this
function selects one of them.

It is equivalent to `do set (f (← get))`. However, using `[modify]](#manual-modify)` may lead to higher performance
because it doesn't add a new reference to the state value. Additional references can inhibit
in-place updates of data.

def

```lean
[modifyGetThe.{u, v}]](#manual-modifyGetThe) {α : Type u} (σ : Type u) {m : Type u → Type v}
  [[MonadStateOf]](#manual-MonadStateOf___mk) σ m] (f : σ → α [×](https://lean-lang.org/doc/reference/latest/Basic-Types/Tuples/#Prod___mk) σ) : m α



[modifyGetThe.{u, v}]](#manual-modifyGetThe) {α : Type u}
  (σ : Type u) {m : Type u → Type v}
  [[MonadStateOf]](#manual-MonadStateOf___mk) σ m] (f : σ → α [×](https://lean-lang.org/doc/reference/latest/Basic-Types/Tuples/#Prod___mk) σ) : m α
```

Applies a function to the current state that has the explicitly-provided type `σ`. The function both
computes a new state and a value. The new state replaces the current state, and the value is
returned.

It is equivalent to `do let (a, s) := f (← getThe σ); set s; pure a`. However, using `[modifyGetThe]](#manual-modifyGetThe)`
may lead to higher performance because it doesn't add a new reference to the state value. Additional
references can inhibit in-place updates of data.

#### 18.5.4.2. Tuple-Based State Monads {#manual-The-Lean-Language-Reference--Functors___-Monads-and--do--Notation--Varieties-of-Monads--State--Tuple-Based-State-Monads}

The tuple-based state monads represent a computation with states that have type `σ` yielding values of type `α` as functions that take a starting state and yield a value paired with a final state, e.g. `σ → α × σ`.
The `[Monad]](#manual-Monad___mk)` operations thread the state correctly through the computation.

def

```lean
[StateM.{u}]](#manual-StateM) (σ α : Type u) : Type u



[StateM.{u}]](#manual-StateM) (σ α : Type u) : Type u
```

A tuple-based state monad.

Actions in `[StateM]](#manual-StateM) σ` are functions that take an initial state and return a value paired with a
final state.

def

```lean
[StateT.{u, v}]](#manual-StateT) (σ : Type u) (m : Type u → Type v) (α : Type u) :
  Type (max u v)



[StateT.{u, v}]](#manual-StateT) (σ : Type u)
  (m : Type u → Type v) (α : Type u) :
  Type (max u v)
```

Adds a mutable state of type `σ` to a monad.

Actions in the resulting monad are functions that take an initial state and return, in `m`, a tuple
of a value and a state.

def

```lean
[StateT.run.{u, v}]](#manual-StateT___run) {σ : Type u} {m : Type u → Type v} {α : Type u}
  (x : [StateT]](#manual-StateT) σ m α) (s : σ) : m [(](https://lean-lang.org/doc/reference/latest/Basic-Types/Tuples/#Prod___mk)α [×](https://lean-lang.org/doc/reference/latest/Basic-Types/Tuples/#Prod___mk) σ[)](https://lean-lang.org/doc/reference/latest/Basic-Types/Tuples/#Prod___mk)



[StateT.run.{u, v}]](#manual-StateT___run) {σ : Type u}
  {m : Type u → Type v} {α : Type u}
  (x : [StateT]](#manual-StateT) σ m α) (s : σ) : m [(](https://lean-lang.org/doc/reference/latest/Basic-Types/Tuples/#Prod___mk)α [×](https://lean-lang.org/doc/reference/latest/Basic-Types/Tuples/#Prod___mk) σ[)](https://lean-lang.org/doc/reference/latest/Basic-Types/Tuples/#Prod___mk)
```

Executes an action from a monad with added state in the underlying monad `m`. Given an initial
state, it returns a value paired with the final state.

def

```lean
[StateT.get.{u, v}]](#manual-StateT___get) {σ : Type u} {m : Type u → Type v} [[Monad]](#manual-Monad___mk) m] :
  [StateT]](#manual-StateT) σ m σ



[StateT.get.{u, v}]](#manual-StateT___get) {σ : Type u}
  {m : Type u → Type v} [[Monad]](#manual-Monad___mk) m] :
  [StateT]](#manual-StateT) σ m σ
```

Retrieves the current value of the monad's mutable state.

This increments the reference count of the state, which may inhibit in-place updates.

def

```lean
[StateT.set.{u, v}]](#manual-StateT___set) {σ : Type u} {m : Type u → Type v} [[Monad]](#manual-Monad___mk) m] :
  σ → [StateT]](#manual-StateT) σ m [PUnit](https://lean-lang.org/doc/reference/latest/Basic-Types/The-Unit-Type/#PUnit___unit)



[StateT.set.{u, v}]](#manual-StateT___set) {σ : Type u}
  {m : Type u → Type v} [[Monad]](#manual-Monad___mk) m] :
  σ → [StateT]](#manual-StateT) σ m [PUnit](https://lean-lang.org/doc/reference/latest/Basic-Types/The-Unit-Type/#PUnit___unit)
```

Replaces the mutable state with a new value.

def

```lean
[StateT.orElse.{u, v}]](#manual-StateT___orElse) {σ : Type u} {m : Type u → Type v} [[Alternative]](#manual-Alternative___mk) m]
  {α : Type u} (x₁ : [StateT]](#manual-StateT) σ m α) (x₂ : [Unit](https://lean-lang.org/doc/reference/latest/Basic-Types/The-Unit-Type/#Unit) → [StateT]](#manual-StateT) σ m α) :
  [StateT]](#manual-StateT) σ m α



[StateT.orElse.{u, v}]](#manual-StateT___orElse) {σ : Type u}
  {m : Type u → Type v} [[Alternative]](#manual-Alternative___mk) m]
  {α : Type u} (x₁ : [StateT]](#manual-StateT) σ m α)
  (x₂ : [Unit](https://lean-lang.org/doc/reference/latest/Basic-Types/The-Unit-Type/#Unit) → [StateT]](#manual-StateT) σ m α) :
  [StateT]](#manual-StateT) σ m α
```

Recovers from errors. The state is rolled back on error recovery. Typically used via the `<|>`
operator.

def

```lean
[StateT.failure.{u, v}]](#manual-StateT___failure) {σ : Type u} {m : Type u → Type v} [[Alternative]](#manual-Alternative___mk) m]
  {α : Type u} : [StateT]](#manual-StateT) σ m α



[StateT.failure.{u, v}]](#manual-StateT___failure) {σ : Type u}
  {m : Type u → Type v} [[Alternative]](#manual-Alternative___mk) m]
  {α : Type u} : [StateT]](#manual-StateT) σ m α
```

Fails with a recoverable error. The state is rolled back on error recovery.

def

```lean
[StateT.run'.{u, v}]](#manual-StateT___run___) {σ : Type u} {m : Type u → Type v} [[Functor]](#manual-Functor___mk) m]
  {α : Type u} (x : [StateT]](#manual-StateT) σ m α) (s : σ) : m α



[StateT.run'.{u, v}]](#manual-StateT___run___) {σ : Type u}
  {m : Type u → Type v} [[Functor]](#manual-Functor___mk) m]
  {α : Type u} (x : [StateT]](#manual-StateT) σ m α)
  (s : σ) : m α
```

Executes an action from a monad with added state in the underlying monad `m`. Given an initial
state, it returns a value, discarding the final state.

def

```lean
[StateT.bind.{u, v}]](#manual-StateT___bind) {σ : Type u} {m : Type u → Type v} [[Monad]](#manual-Monad___mk) m]
  {α β : Type u} (x : [StateT]](#manual-StateT) σ m α) (f : α → [StateT]](#manual-StateT) σ m β) :
  [StateT]](#manual-StateT) σ m β



[StateT.bind.{u, v}]](#manual-StateT___bind) {σ : Type u}
  {m : Type u → Type v} [[Monad]](#manual-Monad___mk) m]
  {α β : Type u} (x : [StateT]](#manual-StateT) σ m α)
  (f : α → [StateT]](#manual-StateT) σ m β) : [StateT]](#manual-StateT) σ m β
```

Sequences two actions. Typically used via the `>>=` operator.

def

```lean
[StateT.modifyGet.{u, v}]](#manual-StateT___modifyGet) {σ : Type u} {m : Type u → Type v} [[Monad]](#manual-Monad___mk) m]
  {α : Type u} (f : σ → α [×](https://lean-lang.org/doc/reference/latest/Basic-Types/Tuples/#Prod___mk) σ) : [StateT]](#manual-StateT) σ m α



[StateT.modifyGet.{u, v}]](#manual-StateT___modifyGet) {σ : Type u}
  {m : Type u → Type v} [[Monad]](#manual-Monad___mk) m]
  {α : Type u} (f : σ → α [×](https://lean-lang.org/doc/reference/latest/Basic-Types/Tuples/#Prod___mk) σ) :
  [StateT]](#manual-StateT) σ m α
```

Applies a function to the current state that both computes a new state and a value. The new state
replaces the current state, and the value is returned.

It is equivalent to `do let (a, s) := f (← StateT.get); StateT.set s; pure a`. However, using
`[StateT.modifyGet]](#manual-StateT___modifyGet)` may lead to better performance because it doesn't add a new reference to the
state value, and additional references can inhibit in-place updates of data.

def

```lean
[StateT.lift.{u, v}]](#manual-StateT___lift) {σ : Type u} {m : Type u → Type v} [[Monad]](#manual-Monad___mk) m]
  {α : Type u} (t : m α) : [StateT]](#manual-StateT) σ m α



[StateT.lift.{u, v}]](#manual-StateT___lift) {σ : Type u}
  {m : Type u → Type v} [[Monad]](#manual-Monad___mk) m]
  {α : Type u} (t : m α) : [StateT]](#manual-StateT) σ m α
```

Runs an action from the underlying monad in the monad with state. The state is not modified.

This function is typically implicitly accessed via a `[MonadLiftT]](#manual-MonadLiftT___mk)` instance as part of [automatic
lifting](https://lean-lang.org/doc/reference/4.34.0-rc1/find/?domain=Verso.Genre.Manual.section&name=monad-lifting).

def

```lean
[StateT.map.{u, v}]](#manual-StateT___map) {σ : Type u} {m : Type u → Type v} [[Monad]](#manual-Monad___mk) m]
  {α β : Type u} (f : α → β) (x : [StateT]](#manual-StateT) σ m α) : [StateT]](#manual-StateT) σ m β



[StateT.map.{u, v}]](#manual-StateT___map) {σ : Type u}
  {m : Type u → Type v} [[Monad]](#manual-Monad___mk) m]
  {α β : Type u} (f : α → β)
  (x : [StateT]](#manual-StateT) σ m α) : [StateT]](#manual-StateT) σ m β
```

Modifies the value returned by a computation. Typically used via the `<$>` operator.

def

```lean
[StateT.pure.{u, v}]](#manual-StateT___pure) {σ : Type u} {m : Type u → Type v} [[Monad]](#manual-Monad___mk) m]
  {α : Type u} (a : α) : [StateT]](#manual-StateT) σ m α



[StateT.pure.{u, v}]](#manual-StateT___pure) {σ : Type u}
  {m : Type u → Type v} [[Monad]](#manual-Monad___mk) m]
  {α : Type u} (a : α) : [StateT]](#manual-StateT) σ m α
```

Returns the given value without modifying the state. Typically used via `[Pure.pure]](#manual-Pure___mk)`.

#### 18.5.4.3. State Monads in Continuation Passing Style {#manual-The-Lean-Language-Reference--Functors___-Monads-and--do--Notation--Varieties-of-Monads--State--State-Monads-in-Continuation-Passing-Style}

Continuation-passing-style state monads represent stateful computations as functions that, for any type whatsoever, take an initial state and a continuation (modeled as a function) that accepts a value and an updated state.
An example of such a type is `(δ : Type u) → σ → (α → σ → δ) → δ`, though `[StateCpsT]](#manual-StateCpsT)` is a transformer that can be applied to any monad.
State monads in continuation passing style have different performance characteristics than tuple-based state monads; for some applications, it may be worth benchmarking them.

def

```lean
[StateCpsT.{u, v}]](#manual-StateCpsT) (σ : Type u) (m : Type u → Type v) (α : Type u) :
  Type (max (u + 1) v)



[StateCpsT.{u, v}]](#manual-StateCpsT) (σ : Type u)
  (m : Type u → Type v) (α : Type u) :
  Type (max (u + 1) v)
```

An alternative implementation of a state monad transformer that internally uses continuation passing
style instead of tuples.

def

```lean
[StateCpsT.lift.{u, v}]](#manual-StateCpsT___lift) {α σ : Type u} {m : Type u → Type v} [[Monad]](#manual-Monad___mk) m]
  (x : m α) : [StateCpsT]](#manual-StateCpsT) σ m α



[StateCpsT.lift.{u, v}]](#manual-StateCpsT___lift) {α σ : Type u}
  {m : Type u → Type v} [[Monad]](#manual-Monad___mk) m]
  (x : m α) : [StateCpsT]](#manual-StateCpsT) σ m α
```

Runs an action from the underlying monad in the monad with state. The state is not modified.

This function is typically implicitly accessed via a `[MonadLiftT]](#manual-MonadLiftT___mk)` instance as part of [automatic
lifting](https://lean-lang.org/doc/reference/4.34.0-rc1/find/?domain=Verso.Genre.Manual.section&name=monad-lifting).

def

```lean
[StateCpsT.runK.{u, v}]](#manual-StateCpsT___runK) {α σ : Type u} {m : Type u → Type v} {β : Type u}
  (x : [StateCpsT]](#manual-StateCpsT) σ m α) (s : σ) (k : α → σ → m β) : m β



[StateCpsT.runK.{u, v}]](#manual-StateCpsT___runK) {α σ : Type u}
  {m : Type u → Type v} {β : Type u}
  (x : [StateCpsT]](#manual-StateCpsT) σ m α) (s : σ)
  (k : α → σ → m β) : m β
```

Runs a stateful computation that's represented using continuation passing style by providing it with
an initial state and a continuation.

def

```lean
[StateCpsT.run'.{u, v}]](#manual-StateCpsT___run___) {α σ : Type u} {m : Type u → Type v} [[Monad]](#manual-Monad___mk) m]
  (x : [StateCpsT]](#manual-StateCpsT) σ m α) (s : σ) : m α



[StateCpsT.run'.{u, v}]](#manual-StateCpsT___run___) {α σ : Type u}
  {m : Type u → Type v} [[Monad]](#manual-Monad___mk) m]
  (x : [StateCpsT]](#manual-StateCpsT) σ m α) (s : σ) : m α
```

Executes an action from a monad with added state in the underlying monad `m`. Given an initial
state, it returns a value, discarding the final state.

def

```lean
[StateCpsT.run.{u, v}]](#manual-StateCpsT___run) {α σ : Type u} {m : Type u → Type v} [[Monad]](#manual-Monad___mk) m]
  (x : [StateCpsT]](#manual-StateCpsT) σ m α) (s : σ) : m [(](https://lean-lang.org/doc/reference/latest/Basic-Types/Tuples/#Prod___mk)α [×](https://lean-lang.org/doc/reference/latest/Basic-Types/Tuples/#Prod___mk) σ[)](https://lean-lang.org/doc/reference/latest/Basic-Types/Tuples/#Prod___mk)



[StateCpsT.run.{u, v}]](#manual-StateCpsT___run) {α σ : Type u}
  {m : Type u → Type v} [[Monad]](#manual-Monad___mk) m]
  (x : [StateCpsT]](#manual-StateCpsT) σ m α) (s : σ) :
  m [(](https://lean-lang.org/doc/reference/latest/Basic-Types/Tuples/#Prod___mk)α [×](https://lean-lang.org/doc/reference/latest/Basic-Types/Tuples/#Prod___mk) σ[)](https://lean-lang.org/doc/reference/latest/Basic-Types/Tuples/#Prod___mk)
```

Executes an action from a monad with added state in the underlying monad `m`. Given an initial
state, it returns a value paired with the final state.

While the state is internally represented in continuation passing style, the resulting value is the
same as for a non-CPS state monad.

#### 18.5.4.4. State Monads from Mutable References {#manual-The-Lean-Language-Reference--Functors___-Monads-and--do--Notation--Varieties-of-Monads--State--State-Monads-from-Mutable-References}

The monad `[StateRefT]](#manual-Lean___Parser___Term___stateRefT) σ m` is a specialized state monad transformer that can be used when `m` is a monad to which `[ST](https://lean-lang.org/doc/reference/latest/IO/Mutable-References/#ST)` computations can be lifted.
It implements the operations of `[MonadState]](#manual-MonadState___mk)` using an `[ST.Ref](https://lean-lang.org/doc/reference/latest/IO/Mutable-References/#ST___Ref___mk)`, rather than pure functions.
This ensures that mutation is actually used at run time.

`[ST](https://lean-lang.org/doc/reference/latest/IO/Mutable-References/#ST)` and `[EST](https://lean-lang.org/doc/reference/latest/IO/Mutable-References/#EST)` require a phantom type parameter that's used together with `[runST](https://lean-lang.org/doc/reference/latest/IO/Mutable-References/#runST)`'s polymorphic function argument to encapsulate mutability.
Rather than require this as a parameter to the transformer, an auxiliary type class `[STWorld]](#manual-STWorld___mk)` is used to propagate it directly from `m`.

The transformer itself is defined as a [syntax extension](https://lean-lang.org/doc/reference/latest/Notations-and-Macros/Defining-New-Syntax/#syntax-ext) and an [elaborator](https://lean-lang.org/doc/reference/latest/Notations-and-Macros/Elaborators/#elaborators), rather than an ordinary function.
This is because `[STWorld]](#manual-STWorld___mk)` has no methods: it exists only to propagate information from the inner monad to the transformed monad.
Nonetheless, its instances are terms; keeping them around could lead to unnecessarily large types.

type class

```lean
[STWorld]](#manual-STWorld___mk) (σ : [outParam]](#manual-outParam) Type) (m : Type → Type) : Type



[STWorld]](#manual-STWorld___mk) (σ : [outParam]](#manual-outParam) Type)
  (m : Type → Type) : Type
```

An auxiliary class used to infer the “state” of `[EST](https://lean-lang.org/doc/reference/latest/IO/Mutable-References/#EST)` and `[ST](https://lean-lang.org/doc/reference/latest/IO/Mutable-References/#ST)` monads.

Instance Constructor

```lean
[STWorld.mk]](#manual-STWorld___mk)
```

syntax`StateRefT`

The syntax for `[StateRefT]](#manual-Lean___Parser___Term___stateRefT) σ m` accepts two arguments:

```lean
term ::= ...
    | StateRefT term (macroDollarArg
       | term)
```

Its elaborator synthesizes an instance of `[STWorld]](#manual-STWorld___mk) ω m` to ensure that `m` supports mutable references.
Having discovered the value of `ω`, it then produces the term `[StateRefT']](#manual-StateRefT___) ω σ m`, discarding the synthesized instance.

def

```lean
[StateRefT']](#manual-StateRefT___) (ω σ : Type) (m : Type → Type) (α : Type) : Type



[StateRefT']](#manual-StateRefT___) (ω σ : Type) (m : Type → Type)
  (α : Type) : Type
```

A state monad that uses an actual mutable reference cell (i.e. an `[ST.Ref](https://lean-lang.org/doc/reference/latest/IO/Mutable-References/#ST___Ref___mk) ω σ`).

The macro `[StateRefT]](#manual-Lean___Parser___Term___stateRefT) σ m α` infers `ω` from `m`. It should normally be used instead.

def

```lean
[StateRefT'.get]](#manual-StateRefT______get) {ω σ : Type} {m : Type → Type} [[MonadLiftT]](#manual-MonadLiftT___mk) ([ST](https://lean-lang.org/doc/reference/latest/IO/Mutable-References/#ST) ω) m] :
  [StateRefT']](#manual-StateRefT___) ω σ m σ



[StateRefT'.get]](#manual-StateRefT______get) {ω σ : Type}
  {m : Type → Type}
  [[MonadLiftT]](#manual-MonadLiftT___mk) ([ST](https://lean-lang.org/doc/reference/latest/IO/Mutable-References/#ST) ω) m] :
  [StateRefT']](#manual-StateRefT___) ω σ m σ
```

Retrieves the current value of the monad's mutable state.

This increments the reference count of the state, which may inhibit in-place updates.

def

```lean
[StateRefT'.set]](#manual-StateRefT______set) {ω σ : Type} {m : Type → Type} [[MonadLiftT]](#manual-MonadLiftT___mk) ([ST](https://lean-lang.org/doc/reference/latest/IO/Mutable-References/#ST) ω) m]
  (s : σ) : [StateRefT']](#manual-StateRefT___) ω σ m [PUnit](https://lean-lang.org/doc/reference/latest/Basic-Types/The-Unit-Type/#PUnit___unit)



[StateRefT'.set]](#manual-StateRefT______set) {ω σ : Type}
  {m : Type → Type} [[MonadLiftT]](#manual-MonadLiftT___mk) ([ST](https://lean-lang.org/doc/reference/latest/IO/Mutable-References/#ST) ω) m]
  (s : σ) : [StateRefT']](#manual-StateRefT___) ω σ m [PUnit](https://lean-lang.org/doc/reference/latest/Basic-Types/The-Unit-Type/#PUnit___unit)
```

Replaces the mutable state with a new value.

def

```lean
[StateRefT'.modifyGet]](#manual-StateRefT______modifyGet) {ω σ : Type} {m : Type → Type} {α : Type}
  [[MonadLiftT]](#manual-MonadLiftT___mk) ([ST](https://lean-lang.org/doc/reference/latest/IO/Mutable-References/#ST) ω) m] (f : σ → α [×](https://lean-lang.org/doc/reference/latest/Basic-Types/Tuples/#Prod___mk) σ) : [StateRefT']](#manual-StateRefT___) ω σ m α



[StateRefT'.modifyGet]](#manual-StateRefT______modifyGet) {ω σ : Type}
  {m : Type → Type} {α : Type}
  [[MonadLiftT]](#manual-MonadLiftT___mk) ([ST](https://lean-lang.org/doc/reference/latest/IO/Mutable-References/#ST) ω) m] (f : σ → α [×](https://lean-lang.org/doc/reference/latest/Basic-Types/Tuples/#Prod___mk) σ) :
  [StateRefT']](#manual-StateRefT___) ω σ m α
```

Applies a function to the current state that both computes a new state and a value. The new state
replaces the current state, and the value is returned.

It is equivalent to a `get` followed by a `[set]](#manual-MonadStateOf___mk)`. However, using `modifyGet` may lead to higher
performance because it doesn't add a new reference to the state value. Additional references can
inhibit in-place updates of data.

def

```lean
[StateRefT'.run]](#manual-StateRefT______run) {ω σ : Type} {m : Type → Type} [[Monad]](#manual-Monad___mk) m]
  [[MonadLiftT]](#manual-MonadLiftT___mk) ([ST](https://lean-lang.org/doc/reference/latest/IO/Mutable-References/#ST) ω) m] {α : Type} (x : [StateRefT']](#manual-StateRefT___) ω σ m α) (s : σ) :
  m [(](https://lean-lang.org/doc/reference/latest/Basic-Types/Tuples/#Prod___mk)α [×](https://lean-lang.org/doc/reference/latest/Basic-Types/Tuples/#Prod___mk) σ[)](https://lean-lang.org/doc/reference/latest/Basic-Types/Tuples/#Prod___mk)



[StateRefT'.run]](#manual-StateRefT______run) {ω σ : Type}
  {m : Type → Type} [[Monad]](#manual-Monad___mk) m]
  [[MonadLiftT]](#manual-MonadLiftT___mk) ([ST](https://lean-lang.org/doc/reference/latest/IO/Mutable-References/#ST) ω) m] {α : Type}
  (x : [StateRefT']](#manual-StateRefT___) ω σ m α) (s : σ) :
  m [(](https://lean-lang.org/doc/reference/latest/Basic-Types/Tuples/#Prod___mk)α [×](https://lean-lang.org/doc/reference/latest/Basic-Types/Tuples/#Prod___mk) σ[)](https://lean-lang.org/doc/reference/latest/Basic-Types/Tuples/#Prod___mk)
```

Executes an action from a monad with added state in the underlying monad `m`. Given an initial
state, it returns a value paired with the final state.

The monad `m` must support `[ST](https://lean-lang.org/doc/reference/latest/IO/Mutable-References/#ST)` effects in order to create and mutate reference cells.

def

```lean
[StateRefT'.run']](#manual-StateRefT______run___) {ω σ : Type} {m : Type → Type} [[Monad]](#manual-Monad___mk) m]
  [[MonadLiftT]](#manual-MonadLiftT___mk) ([ST](https://lean-lang.org/doc/reference/latest/IO/Mutable-References/#ST) ω) m] {α : Type} (x : [StateRefT']](#manual-StateRefT___) ω σ m α) (s : σ) :
  m α



[StateRefT'.run']](#manual-StateRefT______run___) {ω σ : Type}
  {m : Type → Type} [[Monad]](#manual-Monad___mk) m]
  [[MonadLiftT]](#manual-MonadLiftT___mk) ([ST](https://lean-lang.org/doc/reference/latest/IO/Mutable-References/#ST) ω) m] {α : Type}
  (x : [StateRefT']](#manual-StateRefT___) ω σ m α) (s : σ) : m α
```

Executes an action from a monad with added state in the underlying monad `m`. Given an initial
state, it returns a value, discarding the final state.

The monad `m` must support `[ST](https://lean-lang.org/doc/reference/latest/IO/Mutable-References/#ST)` effects in order to create and mutate reference cells.

def

```lean
[StateRefT'.lift]](#manual-StateRefT______lift) {ω σ : Type} {m : Type → Type} {α : Type} (x : m α) :
  [StateRefT']](#manual-StateRefT___) ω σ m α



[StateRefT'.lift]](#manual-StateRefT______lift) {ω σ : Type}
  {m : Type → Type} {α : Type} (x : m α) :
  [StateRefT']](#manual-StateRefT___) ω σ m α
```

Runs an action from the underlying monad in the monad with state. The state is not modified.

This function is typically implicitly accessed via a `[MonadLiftT]](#manual-MonadLiftT___mk)` instance as part of [automatic
lifting](https://lean-lang.org/doc/reference/4.34.0-rc1/find/?domain=Verso.Genre.Manual.section&name=monad-lifting).

### 18.5.5. Reader {#manual-reader-monad}

type class

```lean
[MonadReader.{u, v}]](#manual-MonadReader___mk) (ρ : [outParam]](#manual-outParam) (Type u)) (m : Type u → Type v) :
  Type v



[MonadReader.{u, v}]](#manual-MonadReader___mk) (ρ : [outParam]](#manual-outParam) (Type u))
  (m : Type u → Type v) : Type v
```

Reader monads provide the ability to implicitly thread a value through a computation. The value can
be read, but not written. A `[MonadWithReader]](#manual-MonadWithReader___mk) ρ` instance additionally allows the value to be locally
overridden for a sub-computation.

In this class, `ρ` is an `[outParam]](#manual-outParam)`, which means that it is inferred from `m`. `[MonadReaderOf]](#manual-MonadReaderOf___mk) ρ`
provides the same operations, but allows `ρ` to influence instance synthesis.

Instance Constructor

```lean
[MonadReader.mk]](#manual-MonadReader___mk).{u, v}
```

Methods

```lean
read : m ρ
```

Retrieves the local value.

Use `[readThe]](#manual-readThe)` to explicitly specify a type when more than one value is available.

type class

```lean
[MonadReaderOf.{u, v}]](#manual-MonadReaderOf___mk) (ρ : [semiOutParam]](#manual-semiOutParam) (Type u)) (m : Type u → Type v) :
  Type v



[MonadReaderOf.{u, v}]](#manual-MonadReaderOf___mk)
  (ρ : [semiOutParam]](#manual-semiOutParam) (Type u))
  (m : Type u → Type v) : Type v
```

Reader monads provide the ability to implicitly thread a value through a computation. The value can
be read, but not written. A `[MonadWithReader]](#manual-MonadWithReader___mk) ρ` instance additionally allows the value to be locally
overridden for a sub-computation.

In this class, `ρ` is a `[semiOutParam]](#manual-semiOutParam)`, which means that it can influence the choice of instance.
`[MonadReader]](#manual-MonadReader___mk) ρ` provides the same operations, but requires that `ρ` be inferable from `m`.

Instance Constructor

```lean
[MonadReaderOf.mk]](#manual-MonadReaderOf___mk).{u, v}
```

Methods

```lean
read : m ρ
```

Retrieves the local value.

def

```lean
[readThe.{u, v}]](#manual-readThe) (ρ : Type u) {m : Type u → Type v} [[MonadReaderOf]](#manual-MonadReaderOf___mk) ρ m] :
  m ρ



[readThe.{u, v}]](#manual-readThe) (ρ : Type u)
  {m : Type u → Type v}
  [[MonadReaderOf]](#manual-MonadReaderOf___mk) ρ m] : m ρ
```

Retrieves the local value whose type is `ρ`. This is useful when a monad supports reading more than
one type of value.

Use `[read]](#manual-MonadReader___mk)` for a version that expects the type `ρ` to be inferred from `m`.

type class

```lean
[MonadWithReader.{u, v}]](#manual-MonadWithReader___mk) (ρ : [outParam]](#manual-outParam) (Type u)) (m : Type u → Type v) :
  Type (max (u + 1) v)



[MonadWithReader.{u, v}]](#manual-MonadWithReader___mk)
  (ρ : [outParam]](#manual-outParam) (Type u))
  (m : Type u → Type v) :
  Type (max (u + 1) v)
```

A reader monad that additionally allows the value to be locally overridden.

In this class, `ρ` is an `[outParam]](#manual-outParam)`, which means that it is inferred from `m`. `[MonadWithReaderOf]](#manual-MonadWithReaderOf___mk) ρ`
provides the same operations, but allows `ρ` to influence instance synthesis.

Instance Constructor

```lean
[MonadWithReader.mk]](#manual-MonadWithReader___mk).{u, v}
```

Methods

```lean
withReader : {α : Type u} → (ρ → ρ) → m α → m α
```

Locally modifies the reader monad's value while running an action.

During the inner action `x`, reading the value returns `f` applied to the original value. After
control returns from `x`, the reader monad's value is restored.

type class

```lean
[MonadWithReaderOf.{u, v}]](#manual-MonadWithReaderOf___mk) (ρ : [semiOutParam]](#manual-semiOutParam) (Type u))
  (m : Type u → Type v) : Type (max (u + 1) v)



[MonadWithReaderOf.{u, v}]](#manual-MonadWithReaderOf___mk)
  (ρ : [semiOutParam]](#manual-semiOutParam) (Type u))
  (m : Type u → Type v) :
  Type (max (u + 1) v)
```

A reader monad that additionally allows the value to be locally overridden.

In this class, `ρ` is a `[semiOutParam]](#manual-semiOutParam)`, which means that it can influence the choice of instance.
`[MonadWithReader]](#manual-MonadWithReader___mk) ρ` provides the same operations, but requires that `ρ` be inferable from `m`.

Instance Constructor

```lean
[MonadWithReaderOf.mk]](#manual-MonadWithReaderOf___mk).{u, v}
```

Methods

```lean
withReader : {α : Type u} → (ρ → ρ) → m α → m α
```

Locally modifies the reader monad's value while running an action.

During the inner action `x`, reading the value returns `f` applied to the original value. After
control returns from `x`, the reader monad's value is restored.

def

```lean
[withTheReader.{u, v}]](#manual-withTheReader) (ρ : Type u) {m : Type u → Type v}
  [[MonadWithReaderOf]](#manual-MonadWithReaderOf___mk) ρ m] {α : Type u} (f : ρ → ρ) (x : m α) : m α



[withTheReader.{u, v}]](#manual-withTheReader) (ρ : Type u)
  {m : Type u → Type v}
  [[MonadWithReaderOf]](#manual-MonadWithReaderOf___mk) ρ m] {α : Type u}
  (f : ρ → ρ) (x : m α) : m α
```

Locally modifies the reader monad's value while running an action, with the reader monad's local
value type specified explicitly. This is useful when a monad supports reading more than one type of
value.

During the inner action `x`, reading the value returns `f` applied to the original value. After
control returns from `x`, the reader monad's value is restored.

Use `[withReader]](#manual-MonadWithReader___mk)` for a version that expects the local value's type to be inferred from `m`.

def

```lean
[ReaderT.{u, v}]](#manual-ReaderT) (ρ : Type u) (m : Type u → Type v) (α : Type u) :
  Type (max u v)



[ReaderT.{u, v}]](#manual-ReaderT) (ρ : Type u)
  (m : Type u → Type v) (α : Type u) :
  Type (max u v)
```

Adds the ability to access a read-only value of type `ρ` to a monad. The value can be locally
overridden by `[withReader]](#manual-MonadWithReader___mk)`, but it cannot be mutated.

Actions in the resulting monad are functions that take the local value as a parameter, returning
ordinary actions in `m`.

def

```lean
[ReaderM.{u}]](#manual-ReaderM) (ρ α : Type u) : Type u



[ReaderM.{u}]](#manual-ReaderM) (ρ α : Type u) : Type u
```

A monad with access to a read-only value of type `ρ`. The value can be locally overridden by
`[withReader]](#manual-MonadWithReader___mk)`, but it cannot be mutated.

def

```lean
[ReaderT.run.{u, v}]](#manual-ReaderT___run) {ρ : Type u} {m : Type u → Type v} {α : Type u}
  (x : [ReaderT]](#manual-ReaderT) ρ m α) (r : ρ) : m α



[ReaderT.run.{u, v}]](#manual-ReaderT___run) {ρ : Type u}
  {m : Type u → Type v} {α : Type u}
  (x : [ReaderT]](#manual-ReaderT) ρ m α) (r : ρ) : m α
```

Executes an action from a monad with a read-only value in the underlying monad `m`.

def

```lean
[ReaderT.read.{u, v}]](#manual-ReaderT___read) {ρ : Type u} {m : Type u → Type v} [[Monad]](#manual-Monad___mk) m] :
  [ReaderT]](#manual-ReaderT) ρ m ρ



[ReaderT.read.{u, v}]](#manual-ReaderT___read) {ρ : Type u}
  {m : Type u → Type v} [[Monad]](#manual-Monad___mk) m] :
  [ReaderT]](#manual-ReaderT) ρ m ρ
```

Retrieves the reader monad's local value. Typically accessed via `[read]](#manual-MonadReader___mk)`, or via `[readThe]](#manual-readThe)` when more
than one local value is available.

def

```lean
[ReaderT.adapt.{u, v}]](#manual-ReaderT___adapt) {ρ : Type u} {m : Type u → Type v} {ρ' α : Type u}
  (f : ρ' → ρ) : [ReaderT]](#manual-ReaderT) ρ m α → [ReaderT]](#manual-ReaderT) ρ' m α



[ReaderT.adapt.{u, v}]](#manual-ReaderT___adapt) {ρ : Type u}
  {m : Type u → Type v} {ρ' α : Type u}
  (f : ρ' → ρ) :
  [ReaderT]](#manual-ReaderT) ρ m α → [ReaderT]](#manual-ReaderT) ρ' m α
```

Modifies a reader monad's local value with `f`. The resulting computation applies `f` to the
incoming local value and passes the result to the inner computation.

def

```lean
[ReaderT.pure.{u, v}]](#manual-ReaderT___pure) {ρ : Type u} {m : Type u → Type v} [[Monad]](#manual-Monad___mk) m]
  {α : Type u} (a : α) : [ReaderT]](#manual-ReaderT) ρ m α



[ReaderT.pure.{u, v}]](#manual-ReaderT___pure) {ρ : Type u}
  {m : Type u → Type v} [[Monad]](#manual-Monad___mk) m]
  {α : Type u} (a : α) : [ReaderT]](#manual-ReaderT) ρ m α
```

Returns the provided value `a`, ignoring the reader monad's local value. Typically used via
`[Pure.pure]](#manual-Pure___mk)`.

def

```lean
[ReaderT.bind.{u, v}]](#manual-ReaderT___bind) {ρ : Type u} {m : Type u → Type v} [[Monad]](#manual-Monad___mk) m]
  {α β : Type u} (x : [ReaderT]](#manual-ReaderT) ρ m α) (f : α → [ReaderT]](#manual-ReaderT) ρ m β) :
  [ReaderT]](#manual-ReaderT) ρ m β



[ReaderT.bind.{u, v}]](#manual-ReaderT___bind) {ρ : Type u}
  {m : Type u → Type v} [[Monad]](#manual-Monad___mk) m]
  {α β : Type u} (x : [ReaderT]](#manual-ReaderT) ρ m α)
  (f : α → [ReaderT]](#manual-ReaderT) ρ m β) : [ReaderT]](#manual-ReaderT) ρ m β
```

Sequences two reader monad computations. Both are provided with the local value, and the second is
passed the value of the first. Typically used via the `>>=` operator.

def

```lean
[ReaderT.orElse.{u_1, u_2}]](#manual-ReaderT___orElse) {m : Type u_1 → Type u_2} {ρ α : Type u_1}
  [[Alternative]](#manual-Alternative___mk) m] (x₁ : [ReaderT]](#manual-ReaderT) ρ m α) (x₂ : [Unit](https://lean-lang.org/doc/reference/latest/Basic-Types/The-Unit-Type/#Unit) → [ReaderT]](#manual-ReaderT) ρ m α) :
  [ReaderT]](#manual-ReaderT) ρ m α



[ReaderT.orElse.{u_1, u_2}]](#manual-ReaderT___orElse)
  {m : Type u_1 → Type u_2}
  {ρ α : Type u_1} [[Alternative]](#manual-Alternative___mk) m]
  (x₁ : [ReaderT]](#manual-ReaderT) ρ m α)
  (x₂ : [Unit](https://lean-lang.org/doc/reference/latest/Basic-Types/The-Unit-Type/#Unit) → [ReaderT]](#manual-ReaderT) ρ m α) :
  [ReaderT]](#manual-ReaderT) ρ m α
```

Recovers from errors. The same local value is provided to both branches. Typically used via the
`<|>` operator.

def

```lean
[ReaderT.failure.{u_1, u_2}]](#manual-ReaderT___failure) {m : Type u_1 → Type u_2} {ρ α : Type u_1}
  [[Alternative]](#manual-Alternative___mk) m] : [ReaderT]](#manual-ReaderT) ρ m α



[ReaderT.failure.{u_1, u_2}]](#manual-ReaderT___failure)
  {m : Type u_1 → Type u_2}
  {ρ α : Type u_1} [[Alternative]](#manual-Alternative___mk) m] :
  [ReaderT]](#manual-ReaderT) ρ m α
```

Fails with a recoverable error.

### 18.5.6. Option {#manual-option-monad}

Ordinarily, `[Option](https://lean-lang.org/doc/reference/latest/Basic-Types/Optional-Values/#Option___none)` is thought of as data, similarly to a nullable type.
It can also be considered as a monad, and thus a way of performing computations.
The `[Option](https://lean-lang.org/doc/reference/latest/Basic-Types/Optional-Values/#Option___none)` monad and its transformer `[OptionT]](#manual-OptionT)` can be understood as describing computations that may terminate early, discarding the results.
Callers can check for early termination and invoke a fallback if desired using `OrElse.orElse` or by treating it as a `[MonadExcept]](#manual-MonadExcept___mk) [Unit](https://lean-lang.org/doc/reference/latest/Basic-Types/The-Unit-Type/#Unit)`.

def

```lean
[OptionT.{u, v}]](#manual-OptionT) (m : Type u → Type v) (α : Type u) : Type v



[OptionT.{u, v}]](#manual-OptionT) (m : Type u → Type v)
  (α : Type u) : Type v
```

Adds the ability to fail to a monad. Unlike ordinary exceptions, there is no way to signal why a
failure occurred.

def

```lean
[OptionT.run.{u, v}]](#manual-OptionT___run) {m : Type u → Type v} {α : Type u}
  (x : [OptionT]](#manual-OptionT) m α) : m ([Option](https://lean-lang.org/doc/reference/latest/Basic-Types/Optional-Values/#Option___none) α)



[OptionT.run.{u, v}]](#manual-OptionT___run) {m : Type u → Type v}
  {α : Type u} (x : [OptionT]](#manual-OptionT) m α) :
  m ([Option](https://lean-lang.org/doc/reference/latest/Basic-Types/Optional-Values/#Option___none) α)
```

Executes an action that might fail in the underlying monad `m`, returning `[none](https://lean-lang.org/doc/reference/latest/Basic-Types/Optional-Values/#Option___none)` in case of failure.

def

```lean
[OptionT.lift.{u, v}]](#manual-OptionT___lift) {m : Type u → Type v} [[Monad]](#manual-Monad___mk) m] {α : Type u}
  (x : m α) : [OptionT]](#manual-OptionT) m α



[OptionT.lift.{u, v}]](#manual-OptionT___lift) {m : Type u → Type v}
  [[Monad]](#manual-Monad___mk) m] {α : Type u} (x : m α) :
  [OptionT]](#manual-OptionT) m α
```

Converts a computation from the underlying monad into one that could fail, even though it does not.

This function is typically implicitly accessed via a `[MonadLiftT]](#manual-MonadLiftT___mk)` instance as part of [automatic
lifting](https://lean-lang.org/doc/reference/4.34.0-rc1/find/?domain=Verso.Genre.Manual.section&name=monad-lifting).

def

```lean
[OptionT.mk.{u, v}]](#manual-OptionT___mk) {m : Type u → Type v} {α : Type u}
  (x : m ([Option](https://lean-lang.org/doc/reference/latest/Basic-Types/Optional-Values/#Option___none) α)) : [OptionT]](#manual-OptionT) m α



[OptionT.mk.{u, v}]](#manual-OptionT___mk) {m : Type u → Type v}
  {α : Type u} (x : m ([Option](https://lean-lang.org/doc/reference/latest/Basic-Types/Optional-Values/#Option___none) α)) :
  [OptionT]](#manual-OptionT) m α
```

Converts an action that returns an `[Option](https://lean-lang.org/doc/reference/latest/Basic-Types/Optional-Values/#Option___none)` into one that might fail, with `[none](https://lean-lang.org/doc/reference/latest/Basic-Types/Optional-Values/#Option___none)` indicating
failure.

def

```lean
[OptionT.pure.{u, v}]](#manual-OptionT___pure) {m : Type u → Type v} [[Monad]](#manual-Monad___mk) m] {α : Type u}
  (a : α) : [OptionT]](#manual-OptionT) m α



[OptionT.pure.{u, v}]](#manual-OptionT___pure) {m : Type u → Type v}
  [[Monad]](#manual-Monad___mk) m] {α : Type u} (a : α) :
  [OptionT]](#manual-OptionT) m α
```

Succeeds with the provided value.

def

```lean
[OptionT.bind.{u, v}]](#manual-OptionT___bind) {m : Type u → Type v} [[Monad]](#manual-Monad___mk) m] {α β : Type u}
  (x : [OptionT]](#manual-OptionT) m α) (f : α → [OptionT]](#manual-OptionT) m β) : [OptionT]](#manual-OptionT) m β



[OptionT.bind.{u, v}]](#manual-OptionT___bind) {m : Type u → Type v}
  [[Monad]](#manual-Monad___mk) m] {α β : Type u}
  (x : [OptionT]](#manual-OptionT) m α)
  (f : α → [OptionT]](#manual-OptionT) m β) : [OptionT]](#manual-OptionT) m β
```

Sequences two potentially-failing actions. The second action is run only if the first succeeds.

def

```lean
[OptionT.fail.{u, v}]](#manual-OptionT___fail) {m : Type u → Type v} [[Monad]](#manual-Monad___mk) m] {α : Type u} :
  [OptionT]](#manual-OptionT) m α



[OptionT.fail.{u, v}]](#manual-OptionT___fail) {m : Type u → Type v}
  [[Monad]](#manual-Monad___mk) m] {α : Type u} : [OptionT]](#manual-OptionT) m α
```

A recoverable failure.

def

```lean
[OptionT.orElse.{u, v}]](#manual-OptionT___orElse) {m : Type u → Type v} [[Monad]](#manual-Monad___mk) m] {α : Type u}
  (x : [OptionT]](#manual-OptionT) m α) (y : [Unit](https://lean-lang.org/doc/reference/latest/Basic-Types/The-Unit-Type/#Unit) → [OptionT]](#manual-OptionT) m α) : [OptionT]](#manual-OptionT) m α



[OptionT.orElse.{u, v}]](#manual-OptionT___orElse)
  {m : Type u → Type v} [[Monad]](#manual-Monad___mk) m]
  {α : Type u} (x : [OptionT]](#manual-OptionT) m α)
  (y : [Unit](https://lean-lang.org/doc/reference/latest/Basic-Types/The-Unit-Type/#Unit) → [OptionT]](#manual-OptionT) m α) : [OptionT]](#manual-OptionT) m α
```

Recovers from failures. Typically used via the `<|>` operator.

def

```lean
[OptionT.tryCatch.{u, v, u_1}]](#manual-OptionT___tryCatch) {m : Type u → Type v} [[Monad]](#manual-Monad___mk) m]
  {α : Type u} (x : [OptionT]](#manual-OptionT) m α) (handle : [PUnit](https://lean-lang.org/doc/reference/latest/Basic-Types/The-Unit-Type/#PUnit___unit) → [OptionT]](#manual-OptionT) m α) :
  [OptionT]](#manual-OptionT) m α



[OptionT.tryCatch.{u, v, u_1}]](#manual-OptionT___tryCatch)
  {m : Type u → Type v} [[Monad]](#manual-Monad___mk) m]
  {α : Type u} (x : [OptionT]](#manual-OptionT) m α)
  (handle : [PUnit](https://lean-lang.org/doc/reference/latest/Basic-Types/The-Unit-Type/#PUnit___unit) → [OptionT]](#manual-OptionT) m α) :
  [OptionT]](#manual-OptionT) m α
```

Handles failures by treating them as exceptions of type `[Unit](https://lean-lang.org/doc/reference/latest/Basic-Types/The-Unit-Type/#Unit)`.

### 18.5.7. Exceptions {#manual-exception-monads}

Exception monads describe computations that terminate early (fail).
Failing computations provide their caller with an *exception* value that describes *why* they failed.
In other words, computations either return a value or an exception.
The inductive type `[Except]](#manual-Except___error)` captures this pattern, and is itself a monad.

#### 18.5.7.1. Exceptions {#manual-The-Lean-Language-Reference--Functors___-Monads-and--do--Notation--Varieties-of-Monads--Exceptions--Exceptions}

inductive type

```lean
[Except.{u, v}]](#manual-Except___error) (ε : Type u) (α : Type v) : Type (max u v)



[Except.{u, v}]](#manual-Except___error) (ε : Type u) (α : Type v) :
  Type (max u v)
```

`[Except]](#manual-Except___error) ε α` is a type which represents either an error of type `ε` or a successful result with a
value of type `α`.

`Except ε : Type u → Type v` is a `[Monad]](#manual-Monad___mk)` that represents computations that may throw exceptions:
the `[pure]](#manual-Pure___mk)` operation is `[Except.ok]](#manual-Except___error)` and the `[bind]](#manual-Bind___mk)` operation returns the first encountered
`[Except.error]](#manual-Except___error)`.

Constructors

```lean
[Except.error.{u, v}]](#manual-Except___error) {ε : Type u} {α : Type v} :
  ε → [Except]](#manual-Except___error) ε α
```

A failure value of type `ε`

```lean
[Except.ok.{u, v}]](#manual-Except___error) {ε : Type u} {α : Type v} : α → [Except]](#manual-Except___error) ε α
```

A success value of type `α`

def

```lean
[Except.pure.{u, u_1}]](#manual-Except___pure) {ε : Type u} {α : Type u_1} (a : α) : [Except]](#manual-Except___error) ε α



[Except.pure.{u, u_1}]](#manual-Except___pure) {ε : Type u}
  {α : Type u_1} (a : α) : [Except]](#manual-Except___error) ε α
```

A successful computation in the `[Except]](#manual-Except___error) ε` monad: `a` is returned, and no exception is thrown.

def

```lean
[Except.bind.{u, u_1, u_2}]](#manual-Except___bind) {ε : Type u} {α : Type u_1} {β : Type u_2}
  (ma : [Except]](#manual-Except___error) ε α) (f : α → [Except]](#manual-Except___error) ε β) : [Except]](#manual-Except___error) ε β



[Except.bind.{u, u_1, u_2}]](#manual-Except___bind) {ε : Type u}
  {α : Type u_1} {β : Type u_2}
  (ma : [Except]](#manual-Except___error) ε α) (f : α → [Except]](#manual-Except___error) ε β) :
  [Except]](#manual-Except___error) ε β
```

Sequences two operations that may throw exceptions, allowing the second to depend on the value
returned by the first.

If the first operation throws an exception, then it is the result of the computation. If the first
succeeds but the second throws an exception, then that exception is the result. If both succeed,
then the result is the result of the second computation.

This is the implementation of the `>>=` operator for `[Except]](#manual-Except___error) ε`.

def

```lean
[Except.map.{u, u_1, u_2}]](#manual-Except___map) {ε : Type u} {α : Type u_1} {β : Type u_2}
  (f : α → β) : [Except]](#manual-Except___error) ε α → [Except]](#manual-Except___error) ε β



[Except.map.{u, u_1, u_2}]](#manual-Except___map) {ε : Type u}
  {α : Type u_1} {β : Type u_2}
  (f : α → β) : [Except]](#manual-Except___error) ε α → [Except]](#manual-Except___error) ε β
```

Transforms a successful result with a function, doing nothing when an exception is thrown.

Examples:

- `([pure]](#manual-Pure___mk) 2 : [Except]](#manual-Except___error) [String](https://lean-lang.org/doc/reference/latest/Basic-Types/Strings/#String___ofByteArray) [Nat](https://lean-lang.org/doc/reference/latest/Basic-Types/Natural-Numbers/#Nat___zero)).[map]](#manual-Except___map) toString = [pure]](#manual-Pure___mk) "2"`
- `([throw]](#manual-MonadExcept___mk) "Error" : [Except]](#manual-Except___error) [String](https://lean-lang.org/doc/reference/latest/Basic-Types/Strings/#String___ofByteArray) [Nat](https://lean-lang.org/doc/reference/latest/Basic-Types/Natural-Numbers/#Nat___zero)).[map]](#manual-Except___map) toString = [throw]](#manual-MonadExcept___mk) "Error"`

def

```lean
[Except.mapError.{u, u_1, u_2}]](#manual-Except___mapError) {ε : Type u} {ε' : Type u_1}
  {α : Type u_2} (f : ε → ε') : [Except]](#manual-Except___error) ε α → [Except]](#manual-Except___error) ε' α



[Except.mapError.{u, u_1, u_2}]](#manual-Except___mapError) {ε : Type u}
  {ε' : Type u_1} {α : Type u_2}
  (f : ε → ε') : [Except]](#manual-Except___error) ε α → [Except]](#manual-Except___error) ε' α
```

Transforms exceptions with a function, doing nothing on successful results.

Examples:

- `([pure]](#manual-Pure___mk) 2 : [Except]](#manual-Except___error) [String](https://lean-lang.org/doc/reference/latest/Basic-Types/Strings/#String___ofByteArray) [Nat](https://lean-lang.org/doc/reference/latest/Basic-Types/Natural-Numbers/#Nat___zero)).[mapError]](#manual-Except___mapError) (·.[length](https://lean-lang.org/doc/reference/latest/Basic-Types/Strings/#String___length)) = [pure]](#manual-Pure___mk) 2`
- `([throw]](#manual-MonadExcept___mk) "Error" : [Except]](#manual-Except___error) [String](https://lean-lang.org/doc/reference/latest/Basic-Types/Strings/#String___ofByteArray) [Nat](https://lean-lang.org/doc/reference/latest/Basic-Types/Natural-Numbers/#Nat___zero)).[mapError]](#manual-Except___mapError) (·.[length](https://lean-lang.org/doc/reference/latest/Basic-Types/Strings/#String___length)) = [throw]](#manual-MonadExcept___mk) 5`

def

```lean
[Except.tryCatch.{u, u_1}]](#manual-Except___tryCatch) {ε : Type u} {α : Type u_1} (ma : [Except]](#manual-Except___error) ε α)
  (handle : ε → [Except]](#manual-Except___error) ε α) : [Except]](#manual-Except___error) ε α



[Except.tryCatch.{u, u_1}]](#manual-Except___tryCatch) {ε : Type u}
  {α : Type u_1} (ma : [Except]](#manual-Except___error) ε α)
  (handle : ε → [Except]](#manual-Except___error) ε α) : [Except]](#manual-Except___error) ε α
```

Handles exceptions thrown in the `[Except]](#manual-Except___error) ε` monad.

If `ma` is successful, its result is returned. If it throws an exception, then `handle` is invoked
on the exception's value.

Examples:

- `([pure]](#manual-Pure___mk) 2 : [Except]](#manual-Except___error) [String](https://lean-lang.org/doc/reference/latest/Basic-Types/Strings/#String___ofByteArray) [Nat](https://lean-lang.org/doc/reference/latest/Basic-Types/Natural-Numbers/#Nat___zero)).[tryCatch]](#manual-Except___tryCatch) ([pure]](#manual-Pure___mk) ·.[length](https://lean-lang.org/doc/reference/latest/Basic-Types/Strings/#String___length)) = [pure]](#manual-Pure___mk) 2`
- `([throw]](#manual-MonadExcept___mk) "Error" : [Except]](#manual-Except___error) [String](https://lean-lang.org/doc/reference/latest/Basic-Types/Strings/#String___ofByteArray) [Nat](https://lean-lang.org/doc/reference/latest/Basic-Types/Natural-Numbers/#Nat___zero)).[tryCatch]](#manual-Except___tryCatch) ([pure]](#manual-Pure___mk) ·.[length](https://lean-lang.org/doc/reference/latest/Basic-Types/Strings/#String___length)) = [pure]](#manual-Pure___mk) 5`
- `([throw]](#manual-MonadExcept___mk) "Error" : [Except]](#manual-Except___error) [String](https://lean-lang.org/doc/reference/latest/Basic-Types/Strings/#String___ofByteArray) [Nat](https://lean-lang.org/doc/reference/latest/Basic-Types/Natural-Numbers/#Nat___zero)).[tryCatch]](#manual-Except___tryCatch) (fun x => [throw]](#manual-MonadExcept___mk) ("E: " ++ x)) = [throw]](#manual-MonadExcept___mk) "E: Error"`

def

```lean
[Except.orElseLazy.{u, u_1}]](#manual-Except___orElseLazy) {ε : Type u} {α : Type u_1} (x : [Except]](#manual-Except___error) ε α)
  (y : [Unit](https://lean-lang.org/doc/reference/latest/Basic-Types/The-Unit-Type/#Unit) → [Except]](#manual-Except___error) ε α) : [Except]](#manual-Except___error) ε α



[Except.orElseLazy.{u, u_1}]](#manual-Except___orElseLazy) {ε : Type u}
  {α : Type u_1} (x : [Except]](#manual-Except___error) ε α)
  (y : [Unit](https://lean-lang.org/doc/reference/latest/Basic-Types/The-Unit-Type/#Unit) → [Except]](#manual-Except___error) ε α) : [Except]](#manual-Except___error) ε α
```

Recovers from exceptions thrown in the `[Except]](#manual-Except___error) ε` monad. Typically used via the `<|>` operator.

`[Except.tryCatch]](#manual-Except___tryCatch)` is a related operator that allows the recovery procedure to depend on *which*
exception was thrown.

def

```lean
[Except.isOk.{u, u_1}]](#manual-Except___isOk) {ε : Type u} {α : Type u_1} : [Except]](#manual-Except___error) ε α → [Bool](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)



[Except.isOk.{u, u_1}]](#manual-Except___isOk) {ε : Type u}
  {α : Type u_1} : [Except]](#manual-Except___error) ε α → [Bool](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)
```

Returns `[true](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)` if the value is `[Except.ok]](#manual-Except___error)`, `[false](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)` otherwise.

def

```lean
[Except.toOption.{u, u_1}]](#manual-Except___toOption) {ε : Type u} {α : Type u_1} :
  [Except]](#manual-Except___error) ε α → [Option](https://lean-lang.org/doc/reference/latest/Basic-Types/Optional-Values/#Option___none) α



[Except.toOption.{u, u_1}]](#manual-Except___toOption) {ε : Type u}
  {α : Type u_1} : [Except]](#manual-Except___error) ε α → [Option](https://lean-lang.org/doc/reference/latest/Basic-Types/Optional-Values/#Option___none) α
```

Returns `[none](https://lean-lang.org/doc/reference/latest/Basic-Types/Optional-Values/#Option___none)` if an exception was thrown, or `[some](https://lean-lang.org/doc/reference/latest/Basic-Types/Optional-Values/#Option___none)` around the value on success.

Examples:

- `([pure]](#manual-Pure___mk) 10 : [Except]](#manual-Except___error) [String](https://lean-lang.org/doc/reference/latest/Basic-Types/Strings/#String___ofByteArray) [Nat](https://lean-lang.org/doc/reference/latest/Basic-Types/Natural-Numbers/#Nat___zero)).[toOption]](#manual-Except___toOption) = [some](https://lean-lang.org/doc/reference/latest/Basic-Types/Optional-Values/#Option___none) 10`
- `([throw]](#manual-MonadExcept___mk) "Failure" : [Except]](#manual-Except___error) [String](https://lean-lang.org/doc/reference/latest/Basic-Types/Strings/#String___ofByteArray) [Nat](https://lean-lang.org/doc/reference/latest/Basic-Types/Natural-Numbers/#Nat___zero)).[toOption]](#manual-Except___toOption) = [none](https://lean-lang.org/doc/reference/latest/Basic-Types/Optional-Values/#Option___none)`

def

```lean
[Except.toBool.{u, u_1}]](#manual-Except___toBool) {ε : Type u} {α : Type u_1} : [Except]](#manual-Except___error) ε α → [Bool](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)



[Except.toBool.{u, u_1}]](#manual-Except___toBool) {ε : Type u}
  {α : Type u_1} : [Except]](#manual-Except___error) ε α → [Bool](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)
```

Returns `[true](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)` if the value is `[Except.ok]](#manual-Except___error)`, `[false](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)` otherwise.

#### 18.5.7.2. Type Class {#manual-The-Lean-Language-Reference--Functors___-Monads-and--do--Notation--Varieties-of-Monads--Exceptions--Type-Class}

type class

```lean
[MonadExcept.{u, v, w}]](#manual-MonadExcept___mk) (ε : [outParam]](#manual-outParam) (Type u)) (m : Type v → Type w) :
  Type (max (max u (v + 1)) w)



[MonadExcept.{u, v, w}]](#manual-MonadExcept___mk)
  (ε : [outParam]](#manual-outParam) (Type u))
  (m : Type v → Type w) :
  Type (max (max u (v + 1)) w)
```

Exception monads provide the ability to throw errors and handle errors.

In this class, `ε` is an `[outParam]](#manual-outParam)`, which means that it is inferred from `m`. `[MonadExceptOf]](#manual-MonadExceptOf___mk) ε`
provides the same operations, but allows `ε` to influence instance synthesis.

`[MonadExcept.tryCatch]](#manual-MonadExcept___mk)` is used to desugar `try ... catch ...` steps inside `do`-blocks when the
handlers do not have exception type annotations.

Instance Constructor

```lean
[MonadExcept.mk]](#manual-MonadExcept___mk).{u, v, w}
```

Methods

```lean
throw : {α : Type v} → ε → m α
```

Throws an exception of type `ε` to the nearest enclosing handler.

```lean
tryCatch : {α : Type v} → m α → (ε → m α) → m α
```

Catches errors thrown in `body`, passing them to `handler`. Errors in `handler` are not caught.

def

```lean
[MonadExcept.ofExcept.{u_1, u_2, u_3}]](#manual-MonadExcept___ofExcept) {m : Type u_1 → Type u_2}
  {ε : Type u_3} {α : Type u_1} [[Monad]](#manual-Monad___mk) m] [[MonadExcept]](#manual-MonadExcept___mk) ε m] :
  [Except]](#manual-Except___error) ε α → m α



[MonadExcept.ofExcept.{u_1, u_2, u_3}]](#manual-MonadExcept___ofExcept)
  {m : Type u_1 → Type u_2} {ε : Type u_3}
  {α : Type u_1} [[Monad]](#manual-Monad___mk) m]
  [[MonadExcept]](#manual-MonadExcept___mk) ε m] : [Except]](#manual-Except___error) ε α → m α
```

Re-interprets an `[Except]](#manual-Except___error) ε` action in an exception monad `m`, succeeding if it succeeds and throwing
an exception if it throws an exception.

def

```lean
[MonadExcept.orElse.{u, v, w}]](#manual-MonadExcept___orElse) {ε : Type u} {m : Type v → Type w}
  [[MonadExcept]](#manual-MonadExcept___mk) ε m] {α : Type v} (t₁ : m α) (t₂ : [Unit](https://lean-lang.org/doc/reference/latest/Basic-Types/The-Unit-Type/#Unit) → m α) : m α



[MonadExcept.orElse.{u, v, w}]](#manual-MonadExcept___orElse) {ε : Type u}
  {m : Type v → Type w} [[MonadExcept]](#manual-MonadExcept___mk) ε m]
  {α : Type v} (t₁ : m α)
  (t₂ : [Unit](https://lean-lang.org/doc/reference/latest/Basic-Types/The-Unit-Type/#Unit) → m α) : m α
```

Unconditional error recovery that ignores which exception was thrown. Usually used via the `<|>`
operator.

If both computations throw exceptions, then the result is the second exception.

def

```lean
[MonadExcept.orelse'.{u, v, w}]](#manual-MonadExcept___orelse___) {ε : Type u} {m : Type v → Type w}
  [[MonadExcept]](#manual-MonadExcept___mk) ε m] {α : Type v} (t₁ t₂ : m α)
  (useFirstEx : [Bool](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false) := [true](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)) : m α



[MonadExcept.orelse'.{u, v, w}]](#manual-MonadExcept___orelse___) {ε : Type u}
  {m : Type v → Type w} [[MonadExcept]](#manual-MonadExcept___mk) ε m]
  {α : Type v} (t₁ t₂ : m α)
  (useFirstEx : [Bool](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false) := [true](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)) : m α
```

An alternative unconditional error recovery operator that allows callers to specify which exception
to throw in cases where both operations throw exceptions.

By default, the first is thrown, because the `<|>` operator throws the second.

type class

```lean
[MonadExceptOf.{u, v, w}]](#manual-MonadExceptOf___mk) (ε : [semiOutParam]](#manual-semiOutParam) (Type u))
  (m : Type v → Type w) : Type (max (max u (v + 1)) w)



[MonadExceptOf.{u, v, w}]](#manual-MonadExceptOf___mk)
  (ε : [semiOutParam]](#manual-semiOutParam) (Type u))
  (m : Type v → Type w) :
  Type (max (max u (v + 1)) w)
```

Exception monads provide the ability to throw errors and handle errors.

In this class, `ε` is a `[semiOutParam]](#manual-semiOutParam)`, which means that it can influence the choice of instance.
`[MonadExcept]](#manual-MonadExcept___mk) ε` provides the same operations, but requires that `ε` be inferable from `m`.

`[tryCatchThe]](#manual-tryCatchThe)`, which takes an explicit exception type, is used to desugar `try ... catch ...` steps
inside `do`-blocks when the handlers have type annotations.

Instance Constructor

```lean
[MonadExceptOf.mk]](#manual-MonadExceptOf___mk).{u, v, w}
```

Methods

```lean
throw : {α : Type v} → ε → m α
```

Throws an exception of type `ε` to the nearest enclosing `catch`.

```lean
tryCatch : {α : Type v} → m α → (ε → m α) → m α
```

Catches errors thrown in `body`, passing them to `handler`. Errors in `handler` are not caught.

def

```lean
[throwThe.{u, v, w}]](#manual-throwThe) (ε : Type u) {m : Type v → Type w}
  [[MonadExceptOf]](#manual-MonadExceptOf___mk) ε m] {α : Type v} (e : ε) : m α



[throwThe.{u, v, w}]](#manual-throwThe) (ε : Type u)
  {m : Type v → Type w}
  [[MonadExceptOf]](#manual-MonadExceptOf___mk) ε m] {α : Type v}
  (e : ε) : m α
```

Throws an exception, with the exception type specified explicitly. This is useful when a monad
supports throwing more than one type of exception.

Use `[throw]](#manual-MonadExcept___mk)` for a version that expects the exception type to be inferred from `m`.

def

```lean
[tryCatchThe.{u, v, w}]](#manual-tryCatchThe) (ε : Type u) {m : Type v → Type w}
  [[MonadExceptOf]](#manual-MonadExceptOf___mk) ε m] {α : Type v} (x : m α) (handle : ε → m α) : m α



[tryCatchThe.{u, v, w}]](#manual-tryCatchThe) (ε : Type u)
  {m : Type v → Type w}
  [[MonadExceptOf]](#manual-MonadExceptOf___mk) ε m] {α : Type v}
  (x : m α) (handle : ε → m α) : m α
```

Catches errors, recovering using `handle`. The exception type is specified explicitly. This is useful when a monad
supports throwing or handling more than one type of exception.

Use `[tryCatch]](#manual-MonadExcept___mk)`, for a version that expects the exception type to be inferred from `m`.

#### 18.5.7.3. “Finally” Computations {#manual-The-Lean-Language-Reference--Functors___-Monads-and--do--Notation--Varieties-of-Monads--Exceptions--___Finally___-Computations}

type class

```lean
[MonadFinally.{u, v}]](#manual-MonadFinally___mk) (m : Type u → Type v) : Type (max (u + 1) v)



[MonadFinally.{u, v}]](#manual-MonadFinally___mk)
  (m : Type u → Type v) :
  Type (max (u + 1) v)
```

Monads that provide the ability to ensure an action happens, regardless of exceptions or other
failures.

`[MonadFinally.tryFinally']](#manual-MonadFinally___mk)` is used to desugar `try ... finally ...` syntax.

Instance Constructor

```lean
[MonadFinally.mk]](#manual-MonadFinally___mk).{u, v}
```

Methods

```lean
tryFinally' : {α β : Type u} → m α → ([Option](https://lean-lang.org/doc/reference/latest/Basic-Types/Optional-Values/#Option___none) α → m β) → m [(](https://lean-lang.org/doc/reference/latest/Basic-Types/Tuples/#Prod___mk)α [×](https://lean-lang.org/doc/reference/latest/Basic-Types/Tuples/#Prod___mk) β[)](https://lean-lang.org/doc/reference/latest/Basic-Types/Tuples/#Prod___mk)
```

Runs an action, ensuring that some other action always happens afterward.

More specifically, `tryFinally' x f` runs `x` and then the “finally” computation `f`. If `x`
succeeds with some value `a : α`, `f (some a)` is returned. If `x` fails for `m`'s definition of
failure, `f [none](https://lean-lang.org/doc/reference/latest/Basic-Types/Optional-Values/#Option___none)` is returned.

`[tryFinally']](#manual-MonadFinally___mk)` can be thought of as performing the same role as a `finally` block in an imperative
programming language.

#### 18.5.7.4. Transformer {#manual-The-Lean-Language-Reference--Functors___-Monads-and--do--Notation--Varieties-of-Monads--Exceptions--Transformer}

def

```lean
[ExceptT.{u, v}]](#manual-ExceptT) (ε : Type u) (m : Type u → Type v) (α : Type u) : Type v



[ExceptT.{u, v}]](#manual-ExceptT) (ε : Type u)
  (m : Type u → Type v) (α : Type u) :
  Type v
```

Adds exceptions of type `ε` to a monad `m`.

def

```lean
[ExceptT.lift.{u, v}]](#manual-ExceptT___lift) {ε : Type u} {m : Type u → Type v} [[Monad]](#manual-Monad___mk) m]
  {α : Type u} (t : m α) : [ExceptT]](#manual-ExceptT) ε m α



[ExceptT.lift.{u, v}]](#manual-ExceptT___lift) {ε : Type u}
  {m : Type u → Type v} [[Monad]](#manual-Monad___mk) m]
  {α : Type u} (t : m α) : [ExceptT]](#manual-ExceptT) ε m α
```

Runs a computation from an underlying monad in the transformed monad with exceptions.

def

```lean
[ExceptT.run.{u, v}]](#manual-ExceptT___run) {ε : Type u} {m : Type u → Type v} {α : Type u}
  (x : [ExceptT]](#manual-ExceptT) ε m α) : m ([Except]](#manual-Except___error) ε α)



[ExceptT.run.{u, v}]](#manual-ExceptT___run) {ε : Type u}
  {m : Type u → Type v} {α : Type u}
  (x : [ExceptT]](#manual-ExceptT) ε m α) : m ([Except]](#manual-Except___error) ε α)
```

Use a monadic action that may throw an exception as an action that may return an exception's value.

This is the inverse of `[ExceptT.mk]](#manual-ExceptT___mk)`.

def

```lean
[ExceptT.pure.{u, v}]](#manual-ExceptT___pure) {ε : Type u} {m : Type u → Type v} [[Monad]](#manual-Monad___mk) m]
  {α : Type u} (a : α) : [ExceptT]](#manual-ExceptT) ε m α



[ExceptT.pure.{u, v}]](#manual-ExceptT___pure) {ε : Type u}
  {m : Type u → Type v} [[Monad]](#manual-Monad___mk) m]
  {α : Type u} (a : α) : [ExceptT]](#manual-ExceptT) ε m α
```

Returns the value `a` without throwing exceptions or having any other effect.

def

```lean
[ExceptT.bind.{u, v}]](#manual-ExceptT___bind) {ε : Type u} {m : Type u → Type v} [[Monad]](#manual-Monad___mk) m]
  {α β : Type u} (ma : [ExceptT]](#manual-ExceptT) ε m α) (f : α → [ExceptT]](#manual-ExceptT) ε m β) :
  [ExceptT]](#manual-ExceptT) ε m β



[ExceptT.bind.{u, v}]](#manual-ExceptT___bind) {ε : Type u}
  {m : Type u → Type v} [[Monad]](#manual-Monad___mk) m]
  {α β : Type u} (ma : [ExceptT]](#manual-ExceptT) ε m α)
  (f : α → [ExceptT]](#manual-ExceptT) ε m β) : [ExceptT]](#manual-ExceptT) ε m β
```

Sequences two actions that may throw exceptions. Typically used via `do`-notation or the `>>=`
operator.

def

```lean
[ExceptT.bindCont.{u, v}]](#manual-ExceptT___bindCont) {ε : Type u} {m : Type u → Type v} [[Monad]](#manual-Monad___mk) m]
  {α β : Type u} (f : α → [ExceptT]](#manual-ExceptT) ε m β) : [Except]](#manual-Except___error) ε α → m ([Except]](#manual-Except___error) ε β)



[ExceptT.bindCont.{u, v}]](#manual-ExceptT___bindCont) {ε : Type u}
  {m : Type u → Type v} [[Monad]](#manual-Monad___mk) m]
  {α β : Type u} (f : α → [ExceptT]](#manual-ExceptT) ε m β) :
  [Except]](#manual-Except___error) ε α → m ([Except]](#manual-Except___error) ε β)
```

Handles exceptions thrown by an action that can have no effects *other* than throwing exceptions.

def

```lean
[ExceptT.tryCatch.{u, v}]](#manual-ExceptT___tryCatch) {ε : Type u} {m : Type u → Type v} [[Monad]](#manual-Monad___mk) m]
  {α : Type u} (ma : [ExceptT]](#manual-ExceptT) ε m α) (handle : ε → [ExceptT]](#manual-ExceptT) ε m α) :
  [ExceptT]](#manual-ExceptT) ε m α



[ExceptT.tryCatch.{u, v}]](#manual-ExceptT___tryCatch) {ε : Type u}
  {m : Type u → Type v} [[Monad]](#manual-Monad___mk) m]
  {α : Type u} (ma : [ExceptT]](#manual-ExceptT) ε m α)
  (handle : ε → [ExceptT]](#manual-ExceptT) ε m α) :
  [ExceptT]](#manual-ExceptT) ε m α
```

Handles exceptions produced in the `[ExceptT]](#manual-ExceptT) ε` transformer.

def

```lean
[ExceptT.mk.{u, v}]](#manual-ExceptT___mk) {ε : Type u} {m : Type u → Type v} {α : Type u}
  (x : m ([Except]](#manual-Except___error) ε α)) : [ExceptT]](#manual-ExceptT) ε m α



[ExceptT.mk.{u, v}]](#manual-ExceptT___mk) {ε : Type u}
  {m : Type u → Type v} {α : Type u}
  (x : m ([Except]](#manual-Except___error) ε α)) : [ExceptT]](#manual-ExceptT) ε m α
```

Use a monadic action that may return an exception's value as an action in the transformed monad that
may throw the corresponding exception.

This is the inverse of `[ExceptT.run]](#manual-ExceptT___run)`.

def

```lean
[ExceptT.map.{u, v}]](#manual-ExceptT___map) {ε : Type u} {m : Type u → Type v} [[Monad]](#manual-Monad___mk) m]
  {α β : Type u} (f : α → β) (x : [ExceptT]](#manual-ExceptT) ε m α) : [ExceptT]](#manual-ExceptT) ε m β



[ExceptT.map.{u, v}]](#manual-ExceptT___map) {ε : Type u}
  {m : Type u → Type v} [[Monad]](#manual-Monad___mk) m]
  {α β : Type u} (f : α → β)
  (x : [ExceptT]](#manual-ExceptT) ε m α) : [ExceptT]](#manual-ExceptT) ε m β
```

Transforms a successful computation's value using `f`. Typically used via the `<$>` operator.

def

```lean
[ExceptT.adapt.{u, v}]](#manual-ExceptT___adapt) {ε : Type u} {m : Type u → Type v} [[Monad]](#manual-Monad___mk) m]
  {ε' α : Type u} (f : ε → ε') : [ExceptT]](#manual-ExceptT) ε m α → [ExceptT]](#manual-ExceptT) ε' m α



[ExceptT.adapt.{u, v}]](#manual-ExceptT___adapt) {ε : Type u}
  {m : Type u → Type v} [[Monad]](#manual-Monad___mk) m]
  {ε' α : Type u} (f : ε → ε') :
  [ExceptT]](#manual-ExceptT) ε m α → [ExceptT]](#manual-ExceptT) ε' m α
```

Transforms exceptions using the function `f`.

This is the `[ExceptT]](#manual-ExceptT)` version of `[Except.mapError]](#manual-Except___mapError)`.

#### 18.5.7.5. Exception Monads in Continuation Passing Style {#manual-The-Lean-Language-Reference--Functors___-Monads-and--do--Notation--Varieties-of-Monads--Exceptions--Exception-Monads-in-Continuation-Passing-Style}

Continuation-passing-style exception monads represent potentially-failing computations as functions that take success and failure continuations, both of which return the same type, returning that type.
They must work for *any* return type.
An example of such a type is `(β : Type u) → (α → β) → (ε → β) → β`.
`[ExceptCpsT]](#manual-ExceptCpsT)` is a transformer that can be applied to any monad, so `[ExceptCpsT]](#manual-ExceptCpsT) ε m α` is actually defined as `(β : Type u) → (α → m β) → (ε → m β) → m β`.
Exception monads in continuation passing style have different performance characteristics than `[Except]](#manual-Except___error)`-based state monads; for some applications, it may be worth benchmarking them.

def

```lean
[ExceptCpsT.{u, v}]](#manual-ExceptCpsT) (ε : Type u) (m : Type u → Type v) (α : Type u) :
  Type (max (u + 1) v)



[ExceptCpsT.{u, v}]](#manual-ExceptCpsT) (ε : Type u)
  (m : Type u → Type v) (α : Type u) :
  Type (max (u + 1) v)
```

Adds exceptions of type `ε` to a monad `m`.

Instead of using `[Except]](#manual-Except___error) ε` to model exceptions, this implementation uses continuation passing
style. This has different performance characteristics from `[ExceptT]](#manual-ExceptT) ε`.

def

```lean
[ExceptCpsT.runCatch.{u_1, u_2}]](#manual-ExceptCpsT___runCatch) {m : Type u_1 → Type u_2} {α : Type u_1}
  [[Monad]](#manual-Monad___mk) m] (x : [ExceptCpsT]](#manual-ExceptCpsT) α m α) : m α



[ExceptCpsT.runCatch.{u_1, u_2}]](#manual-ExceptCpsT___runCatch)
  {m : Type u_1 → Type u_2} {α : Type u_1}
  [[Monad]](#manual-Monad___mk) m] (x : [ExceptCpsT]](#manual-ExceptCpsT) α m α) : m α
```

Returns the value of a computation, forgetting whether it was an exception or a success.

This corresponds to early return.

def

```lean
[ExceptCpsT.runK.{u, u_1}]](#manual-ExceptCpsT___runK) {m : Type u → Type u_1} {β ε α : Type u}
  (x : [ExceptCpsT]](#manual-ExceptCpsT) ε m α) (ok : α → m β) (error : ε → m β) : m β



[ExceptCpsT.runK.{u, u_1}]](#manual-ExceptCpsT___runK)
  {m : Type u → Type u_1} {β ε α : Type u}
  (x : [ExceptCpsT]](#manual-ExceptCpsT) ε m α) (ok : α → m β)
  (error : ε → m β) : m β
```

Use a monadic action that may throw an exception by providing explicit success and failure
continuations.

def

```lean
[ExceptCpsT.run.{u, u_1}]](#manual-ExceptCpsT___run) {m : Type u → Type u_1} {ε α : Type u} [[Monad]](#manual-Monad___mk) m]
  (x : [ExceptCpsT]](#manual-ExceptCpsT) ε m α) : m ([Except]](#manual-Except___error) ε α)



[ExceptCpsT.run.{u, u_1}]](#manual-ExceptCpsT___run)
  {m : Type u → Type u_1} {ε α : Type u}
  [[Monad]](#manual-Monad___mk) m] (x : [ExceptCpsT]](#manual-ExceptCpsT) ε m α) :
  m ([Except]](#manual-Except___error) ε α)
```

Use a monadic action that may throw an exception as an action that may return an exception's value.

def

```lean
[ExceptCpsT.lift.{u_1, u_2}]](#manual-ExceptCpsT___lift) {m : Type u_1 → Type u_2} {α ε : Type u_1}
  [[Monad]](#manual-Monad___mk) m] (x : m α) : [ExceptCpsT]](#manual-ExceptCpsT) ε m α



[ExceptCpsT.lift.{u_1, u_2}]](#manual-ExceptCpsT___lift)
  {m : Type u_1 → Type u_2}
  {α ε : Type u_1} [[Monad]](#manual-Monad___mk) m] (x : m α) :
  [ExceptCpsT]](#manual-ExceptCpsT) ε m α
```

Run an action from the transformed monad in the exception monad.

### 18.5.8. Combined Error and State Monads {#manual-The-Lean-Language-Reference--Functors___-Monads-and--do--Notation--Varieties-of-Monads--Combined-Error-and-State-Monads}

The `[EStateM]](#manual-EStateM)` monad has both exceptions and mutable state.
`[EStateM]](#manual-EStateM) ε σ α` is logically equivalent to `[ExceptT]](#manual-ExceptT) ε ([StateM]](#manual-StateM) σ) α`.
While `[ExceptT]](#manual-ExceptT) ε ([StateM]](#manual-StateM) σ)` evaluates to the type `σ → [Except]](#manual-Except___error) ε α × σ`, the type `[EStateM]](#manual-EStateM) ε σ α` evaluates to `σ → [EStateM.Result]](#manual-EStateM___Result___ok) ε σ α`.
`[EStateM.Result]](#manual-EStateM___Result___ok)` is an inductive type that's very similar to `[Except]](#manual-Except___error)`, except both constructors have an additional field for the state.
In compiled code, this representation removes one level of indirection from each monadic bind.

def

```lean
[EStateM.{u}]](#manual-EStateM) (ε σ α : Type u) : Type u



[EStateM.{u}]](#manual-EStateM) (ε σ α : Type u) : Type u
```

A combined state and exception monad in which exceptions do not automatically roll back the state.

Instances of `[EStateM.Backtrackable]](#manual-EStateM___Backtrackable___mk)` provide a way to roll back some part of the state if needed.

`[EStateM]](#manual-EStateM) ε σ` is equivalent to `[ExceptT]](#manual-ExceptT) ε ([StateM]](#manual-StateM) σ)`, but it is more efficient.

inductive type

```lean
[EStateM.Result.{u}]](#manual-EStateM___Result___ok) (ε σ α : Type u) : Type u



[EStateM.Result.{u}]](#manual-EStateM___Result___ok) (ε σ α : Type u) :
  Type u
```

The value returned from a combined state and exception monad in which exceptions do not
automatically roll back the state.

`Result ε σ α` is equivalent to `[Except]](#manual-Except___error) ε α × σ`, but using a single combined inductive type yields
a more efficient data representation.

Constructors

```lean
[EStateM.Result.ok.{u}]](#manual-EStateM___Result___ok) {ε σ α : Type u} :
  α → σ → [EStateM.Result]](#manual-EStateM___Result___ok) ε σ α
```

A success value of type `α` and a new state `σ`.

```lean
[EStateM.Result.error.{u}]](#manual-EStateM___Result___ok) {ε σ α : Type u} :
  ε → σ → [EStateM.Result]](#manual-EStateM___Result___ok) ε σ α
```

An exception of type `ε` and a new state `σ`.

def

```lean
[EStateM.run.{u}]](#manual-EStateM___run) {ε σ α : Type u} (x : [EStateM]](#manual-EStateM) ε σ α) (s : σ) :
  [EStateM.Result]](#manual-EStateM___Result___ok) ε σ α



[EStateM.run.{u}]](#manual-EStateM___run) {ε σ α : Type u}
  (x : [EStateM]](#manual-EStateM) ε σ α) (s : σ) :
  [EStateM.Result]](#manual-EStateM___Result___ok) ε σ α
```

Executes an `[EStateM]](#manual-EStateM)` action with the initial state `s`. The returned value includes the final state
and indicates whether an exception was thrown or a value was returned.

def

```lean
[EStateM.run'.{u}]](#manual-EStateM___run___) {ε σ α : Type u} (x : [EStateM]](#manual-EStateM) ε σ α) (s : σ) : [Option](https://lean-lang.org/doc/reference/latest/Basic-Types/Optional-Values/#Option___none) α



[EStateM.run'.{u}]](#manual-EStateM___run___) {ε σ α : Type u}
  (x : [EStateM]](#manual-EStateM) ε σ α) (s : σ) : [Option](https://lean-lang.org/doc/reference/latest/Basic-Types/Optional-Values/#Option___none) α
```

Executes an `[EStateM]](#manual-EStateM)` with the initial state `s` for the returned value `α`, discarding the final
state. Returns `[none](https://lean-lang.org/doc/reference/latest/Basic-Types/Optional-Values/#Option___none)` if an unhandled exception was thrown.

def

```lean
[EStateM.adaptExcept.{u}]](#manual-EStateM___adaptExcept) {ε σ α ε' : Type u} (f : ε → ε')
  (x : [EStateM]](#manual-EStateM) ε σ α) : [EStateM]](#manual-EStateM) ε' σ α



[EStateM.adaptExcept.{u}]](#manual-EStateM___adaptExcept)
  {ε σ α ε' : Type u} (f : ε → ε')
  (x : [EStateM]](#manual-EStateM) ε σ α) : [EStateM]](#manual-EStateM) ε' σ α
```

Transforms exceptions with a function, doing nothing on successful results.

def

```lean
[EStateM.fromStateM]](#manual-EStateM___fromStateM) {ε σ α : Type} (x : [StateM]](#manual-StateM) σ α) : [EStateM]](#manual-EStateM) ε σ α



[EStateM.fromStateM]](#manual-EStateM___fromStateM) {ε σ α : Type}
  (x : [StateM]](#manual-StateM) σ α) : [EStateM]](#manual-EStateM) ε σ α
```

Converts a state monad action into a state monad action with exceptions.

The resulting action does not throw an exception.

#### 18.5.8.1. State Rollback {#manual-The-Lean-Language-Reference--Functors___-Monads-and--do--Notation--Varieties-of-Monads--Combined-Error-and-State-Monads--State-Rollback}

Composing `[StateT]](#manual-StateT)` and `[ExceptT]](#manual-ExceptT)` in different orders causes exceptions to interact differently with state.
In one ordering, state changes are rolled back when exceptions are caught; in the other, they persist.
The latter option matches the semantics of most imperative programming languages, but the former is very useful for search-based problems.
Often, some but not all state should be rolled back; this can be achieved by “sandwiching” `[ExceptT]](#manual-ExceptT)` between two separate uses of `[StateT]](#manual-StateT)`.

To avoid yet another layer of indirection via the use of `[StateT]](#manual-StateT) σ ([EStateM]](#manual-EStateM) ε σ') α`, `[EStateM]](#manual-EStateM)` offers the `[EStateM.Backtrackable]](#manual-EStateM___Backtrackable___mk)` [type class]](#manual---tech-term-type-class).
This class specifies some part of the state that can be saved and restored.
`[EStateM]](#manual-EStateM)` then arranges for the saving and restoring to take place around error handling.

type class

```lean
[EStateM.Backtrackable.{u}]](#manual-EStateM___Backtrackable___mk) (δ : [outParam]](#manual-outParam) (Type u)) (σ : Type u) : Type u



[EStateM.Backtrackable.{u}]](#manual-EStateM___Backtrackable___mk)
  (δ : [outParam]](#manual-outParam) (Type u)) (σ : Type u) :
  Type u
```

Exception handlers in `[EStateM]](#manual-EStateM)` save some part of the state, determined by `δ`, and restore it if an
exception is caught. By default, `δ` is `[Unit](https://lean-lang.org/doc/reference/latest/Basic-Types/The-Unit-Type/#Unit)`, and no information is saved.

Instance Constructor

```lean
[EStateM.Backtrackable.mk]](#manual-EStateM___Backtrackable___mk).{u}
```

Methods

```lean
save : σ → δ
```

Extracts the information in the state that should be rolled back if an exception is handled.

```lean
restore : σ → δ → σ
```

Updates the current state with the saved information that should be rolled back. This updated
state becomes the current state when an exception is handled.

There is a universally-applicable instance of `[Backtrackable]](#manual-EStateM___Backtrackable___mk)` that neither saves nor restores anything.
Because instance synthesis chooses the most recent instance first, the universal instance is used only if no other instance has been defined.

def

```lean
[EStateM.nonBacktrackable.{u}]](#manual-EStateM___nonBacktrackable) {σ : Type u} :
  [EStateM.Backtrackable]](#manual-EStateM___Backtrackable___mk) [PUnit](https://lean-lang.org/doc/reference/latest/Basic-Types/The-Unit-Type/#PUnit___unit) σ



[EStateM.nonBacktrackable.{u}]](#manual-EStateM___nonBacktrackable)
  {σ : Type u} :
  [EStateM.Backtrackable]](#manual-EStateM___Backtrackable___mk) [PUnit](https://lean-lang.org/doc/reference/latest/Basic-Types/The-Unit-Type/#PUnit___unit) σ
```

A fallback `Backtrackable` instance that saves no information from a state. This allows every type
to be used as a state in `[EStateM]](#manual-EStateM)`, with no rollback.

Because this is the first declared instance of `Backtrackable _ σ`, it will be picked only if there
are no other `Backtrackable _ σ` instances registered.

#### 18.5.8.2. Implementations {#manual-The-Lean-Language-Reference--Functors___-Monads-and--do--Notation--Varieties-of-Monads--Combined-Error-and-State-Monads--Implementations}

These functions are typically not called directly, but rather are accessed through their corresponding type classes.

def

```lean
[EStateM.map.{u}]](#manual-EStateM___map) {ε σ α β : Type u} (f : α → β) (x : [EStateM]](#manual-EStateM) ε σ α) :
  [EStateM]](#manual-EStateM) ε σ β



[EStateM.map.{u}]](#manual-EStateM___map) {ε σ α β : Type u}
  (f : α → β) (x : [EStateM]](#manual-EStateM) ε σ α) :
  [EStateM]](#manual-EStateM) ε σ β
```

Transforms the value returned from an `[EStateM]](#manual-EStateM) ε σ` action using a function.

def

```lean
[EStateM.pure.{u}]](#manual-EStateM___pure) {ε σ α : Type u} (a : α) : [EStateM]](#manual-EStateM) ε σ α



[EStateM.pure.{u}]](#manual-EStateM___pure) {ε σ α : Type u}
  (a : α) : [EStateM]](#manual-EStateM) ε σ α
```

Returns a value without modifying the state or throwing an exception.

def

```lean
[EStateM.bind.{u}]](#manual-EStateM___bind) {ε σ α β : Type u} (x : [EStateM]](#manual-EStateM) ε σ α)
  (f : α → [EStateM]](#manual-EStateM) ε σ β) : [EStateM]](#manual-EStateM) ε σ β



[EStateM.bind.{u}]](#manual-EStateM___bind) {ε σ α β : Type u}
  (x : [EStateM]](#manual-EStateM) ε σ α)
  (f : α → [EStateM]](#manual-EStateM) ε σ β) : [EStateM]](#manual-EStateM) ε σ β
```

Sequences two `[EStateM]](#manual-EStateM) ε σ` actions, passing the returned value from the first into the second.

def

```lean
[EStateM.orElse.{u}]](#manual-EStateM___orElse) {ε σ α δ : Type u} [[EStateM.Backtrackable]](#manual-EStateM___Backtrackable___mk) δ σ]
  (x₁ : [EStateM]](#manual-EStateM) ε σ α) (x₂ : [Unit](https://lean-lang.org/doc/reference/latest/Basic-Types/The-Unit-Type/#Unit) → [EStateM]](#manual-EStateM) ε σ α) : [EStateM]](#manual-EStateM) ε σ α



[EStateM.orElse.{u}]](#manual-EStateM___orElse) {ε σ α δ : Type u}
  [[EStateM.Backtrackable]](#manual-EStateM___Backtrackable___mk) δ σ]
  (x₁ : [EStateM]](#manual-EStateM) ε σ α)
  (x₂ : [Unit](https://lean-lang.org/doc/reference/latest/Basic-Types/The-Unit-Type/#Unit) → [EStateM]](#manual-EStateM) ε σ α) :
  [EStateM]](#manual-EStateM) ε σ α
```

Failure handling that does not depend on specific exception values.

The `Backtrackable δ σ` instance is used to save a snapshot of part of the state prior to running
`x₁`. If an exception is caught, the state is updated with the saved snapshot, rolling back part of
the state. If no instance of `Backtrackable` is provided, a fallback instance in which `δ` is `[Unit](https://lean-lang.org/doc/reference/latest/Basic-Types/The-Unit-Type/#Unit)`
is used, and no information is rolled back.

def

```lean
[EStateM.orElse'.{u}]](#manual-EStateM___orElse___) {ε σ α δ : Type u} [[EStateM.Backtrackable]](#manual-EStateM___Backtrackable___mk) δ σ]
  (x₁ x₂ : [EStateM]](#manual-EStateM) ε σ α) (useFirstEx : [Bool](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false) := [true](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)) : [EStateM]](#manual-EStateM) ε σ α



[EStateM.orElse'.{u}]](#manual-EStateM___orElse___) {ε σ α δ : Type u}
  [[EStateM.Backtrackable]](#manual-EStateM___Backtrackable___mk) δ σ]
  (x₁ x₂ : [EStateM]](#manual-EStateM) ε σ α)
  (useFirstEx : [Bool](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false) := [true](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)) :
  [EStateM]](#manual-EStateM) ε σ α
```

Alternative orElse operator that allows callers to select which exception should be used when both
operations fail. The default is to use the first exception since the standard `orElse` uses the
second.

def

```lean
[EStateM.seqRight.{u}]](#manual-EStateM___seqRight) {ε σ α β : Type u} (x : [EStateM]](#manual-EStateM) ε σ α)
  (y : [Unit](https://lean-lang.org/doc/reference/latest/Basic-Types/The-Unit-Type/#Unit) → [EStateM]](#manual-EStateM) ε σ β) : [EStateM]](#manual-EStateM) ε σ β



[EStateM.seqRight.{u}]](#manual-EStateM___seqRight) {ε σ α β : Type u}
  (x : [EStateM]](#manual-EStateM) ε σ α)
  (y : [Unit](https://lean-lang.org/doc/reference/latest/Basic-Types/The-Unit-Type/#Unit) → [EStateM]](#manual-EStateM) ε σ β) :
  [EStateM]](#manual-EStateM) ε σ β
```

Sequences two `[EStateM]](#manual-EStateM) ε σ` actions, running `x` before `y`. The first action's return value is
ignored.

def

```lean
[EStateM.tryCatch.{u}]](#manual-EStateM___tryCatch) {ε σ δ : Type u} [[EStateM.Backtrackable]](#manual-EStateM___Backtrackable___mk) δ σ]
  {α : Type u} (x : [EStateM]](#manual-EStateM) ε σ α) (handle : ε → [EStateM]](#manual-EStateM) ε σ α) :
  [EStateM]](#manual-EStateM) ε σ α



[EStateM.tryCatch.{u}]](#manual-EStateM___tryCatch) {ε σ δ : Type u}
  [[EStateM.Backtrackable]](#manual-EStateM___Backtrackable___mk) δ σ] {α : Type u}
  (x : [EStateM]](#manual-EStateM) ε σ α)
  (handle : ε → [EStateM]](#manual-EStateM) ε σ α) :
  [EStateM]](#manual-EStateM) ε σ α
```

Handles exceptions thrown in the combined error and state monad.

The `Backtrackable δ σ` instance is used to save a snapshot of part of the state prior to running
`x`. If an exception is caught, the state is updated with the saved snapshot, rolling back part of
the state. If no instance of `Backtrackable` is provided, a fallback instance in which `δ` is `[Unit](https://lean-lang.org/doc/reference/latest/Basic-Types/The-Unit-Type/#Unit)`
is used, and no information is rolled back.

def

```lean
[EStateM.throw.{u}]](#manual-EStateM___throw) {ε σ α : Type u} (e : ε) : [EStateM]](#manual-EStateM) ε σ α



[EStateM.throw.{u}]](#manual-EStateM___throw) {ε σ α : Type u}
  (e : ε) : [EStateM]](#manual-EStateM) ε σ α
```

Throws an exception of type `ε` to the nearest enclosing handler.

def

```lean
[EStateM.get.{u}]](#manual-EStateM___get) {ε σ : Type u} : [EStateM]](#manual-EStateM) ε σ σ



[EStateM.get.{u}]](#manual-EStateM___get) {ε σ : Type u} :
  [EStateM]](#manual-EStateM) ε σ σ
```

Retrieves the current value of the monad's mutable state.

def

```lean
[EStateM.set.{u}]](#manual-EStateM___set) {ε σ : Type u} (s : σ) : [EStateM]](#manual-EStateM) ε σ [PUnit](https://lean-lang.org/doc/reference/latest/Basic-Types/The-Unit-Type/#PUnit___unit)



[EStateM.set.{u}]](#manual-EStateM___set) {ε σ : Type u} (s : σ) :
  [EStateM]](#manual-EStateM) ε σ [PUnit](https://lean-lang.org/doc/reference/latest/Basic-Types/The-Unit-Type/#PUnit___unit)
```

Replaces the current value of the mutable state with a new one.

def

```lean
[EStateM.modifyGet.{u}]](#manual-EStateM___modifyGet) {ε σ α : Type u} (f : σ → α [×](https://lean-lang.org/doc/reference/latest/Basic-Types/Tuples/#Prod___mk) σ) : [EStateM]](#manual-EStateM) ε σ α



[EStateM.modifyGet.{u}]](#manual-EStateM___modifyGet) {ε σ α : Type u}
  (f : σ → α [×](https://lean-lang.org/doc/reference/latest/Basic-Types/Tuples/#Prod___mk) σ) : [EStateM]](#manual-EStateM) ε σ α
```

Applies a function to the current state that both computes a new state and a value. The new state
replaces the current state, and the value is returned.

It is equivalent to `do let (a, s) := f (← get); set s; pure a`. However, using `modifyGet` may
lead to higher performance because it doesn't add a new reference to the state value. Additional
references can inhibit in-place updates of data.

---



## Basic Propositions {#manual-basic-propositions}

> 📄 Source: https://lean-lang.org/doc/reference/latest/Basic-Propositions/

With the exception of implication and universal quantification, logical connectives and quantifiers are implemented as [inductive types]](#manual---tech-term-Inductive-types) in the `Prop` universe.
In some sense, the connectives described in this chapter are not special—they could be implemented by any user.
However, these basic connectives are used pervasively in the standard library and built-in proof automation tools.

1. [19.1. Truth](https://lean-lang.org/doc/reference/latest/Basic-Propositions/Truth/#true-false)
2. [19.2. Logical Connectives](https://lean-lang.org/doc/reference/latest/Basic-Propositions/Logical-Connectives/#The-Lean-Language-Reference--Basic-Propositions--Logical-Connectives)
3. [19.3. Quantifiers](https://lean-lang.org/doc/reference/latest/Basic-Propositions/Quantifiers/#The-Lean-Language-Reference--Basic-Propositions--Quantifiers)
4. [19.4. Propositional Equality](https://lean-lang.org/doc/reference/latest/Basic-Propositions/Propositional-Equality/#propositional-equality)

---



## Basic Propositions — 19.1. Truth {#manual-basic-propositions-191-truth}

> 📄 Source: https://lean-lang.org/doc/reference/latest/Basic-Propositions/Truth/

Fundamentally, there are only two propositions in Lean: `[True]](#manual-True___intro)` and `[False]](#manual-False)`.
The axiom of propositional extensionality (`[propext]](#manual-propext)`) allows propositions to be considered equal when they are logically equivalent, and every true proposition is logically equivalent to `[True]](#manual-True___intro)`.
Similarly, every false proposition is logically equivalent to `[False]](#manual-False)`.

`[True]](#manual-True___intro)` is an inductively defined proposition with a single constructor that takes no parameters.
It is always possible to prove `[True]](#manual-True___intro)`.
`[False]](#manual-False)`, on the other hand, is an inductively defined proposition with no constructors.
Proving it requires finding an inconsistency in the current context.

Both `[True]](#manual-True___intro)` and `[False]](#manual-False)` are [subsingletons]](#manual-subsingleton-elimination); this means that they can be used to compute inhabitants of non-propositional types.
For `[True]](#manual-True___intro)`, this amounts to ignoring the proof, which is not informative.
For `[False]](#manual-False)`, this amounts to a demonstration that the current code is unreachable and does not need to be completed.

inductive proposition

```lean
[True]](#manual-True___intro) : Prop



[True]](#manual-True___intro) : Prop
```

`[True]](#manual-True___intro)` is a proposition and has only an introduction rule, `[True.intro]](#manual-True___intro) : [True]](#manual-True___intro)`.
In other words, `[True]](#manual-True___intro)` is simply true, and has a canonical proof, `[True.intro]](#manual-True___intro)`
For more information: [Propositional Logic](https://lean-lang.org/theorem_proving_in_lean4/propositions_and_proofs.html#propositional-logic)

Constructors

```lean
[True.intro]](#manual-True___intro) : [True]](#manual-True___intro)
```

`[True]](#manual-True___intro)` is true, and `[True.intro]](#manual-True___intro)` (or more commonly, `trivial`)
is the proof.

inductive proposition

```lean
[False]](#manual-False) : Prop



[False]](#manual-False) : Prop
```

`[False]](#manual-False)` is the empty proposition. Thus, it has no introduction rules.
It represents a contradiction. `[False]](#manual-False)` elimination rule, `False.rec`,
expresses the fact that anything follows from a contradiction.
This rule is sometimes called ex falso (short for ex falso sequitur quodlibet),
or the principle of explosion.
For more information: [Propositional Logic](https://lean-lang.org/theorem_proving_in_lean4/propositions_and_proofs.html#propositional-logic)

Constructors

def

```lean
[False.elim.{u}]](#manual-False___elim) {C : Sort u} (h : [False]](#manual-False)) : C



[False.elim.{u}]](#manual-False___elim) {C : Sort u} (h : [False]](#manual-False)) :
  C
```

`[False.elim]](#manual-False___elim) : [False]](#manual-False) → C` says that from `[False]](#manual-False)`, any desired proposition
`C` holds. Also known as ex falso quodlibet (EFQ) or the principle of explosion.

The target type is actually `C : Sort u` which means it works for both
propositions and types. When executed, this acts like an "unreachable"
instruction: it is **undefined behavior** to run, but it will probably print
"unreachable code". (You would need to construct a proof of false to run it
anyway, which you can only do using `[sorry]](#manual-sorry)` or unsound axioms.)

**Example: Dead Code and Subsingleton Elimination**

The fourth branch in the definition of `[f]](#manual-f-_LPAR_in-Dead-Code-and-Subsingleton-Elimination_RPAR_)` is unreachable, so no concrete `[String](https://lean-lang.org/doc/reference/latest/Basic-Types/Strings/#String___ofByteArray)` value needs to be provided:

```lean
def f (n : [Nat](https://lean-lang.org/doc/reference/latest/Basic-Types/Natural-Numbers/#Nat___zero)) : [String](https://lean-lang.org/doc/reference/latest/Basic-Types/Strings/#String___ofByteArray) :=
[if]](#manual-termDepIfThenElse) h1 : n < 11 [then]](#manual-termDepIfThenElse)
"Small"
[else]](#manual-termDepIfThenElse) [if]](#manual-termDepIfThenElse) h2 : n > 13 [then]](#manual-termDepIfThenElse)
"Large"
[else]](#manual-termDepIfThenElse) [if]](#manual-termDepIfThenElse) h3 : n % 2 = 1 [then]](#manual-termDepIfThenElse)
"Odd"
[else]](#manual-termDepIfThenElse) [if]](#manual-termDepIfThenElse) h4 : n ≠ 12 [then]](#manual-termDepIfThenElse)
[False.elim]](#manual-False___elim) (byn:[Nat](https://lean-lang.org/doc/reference/latest/Basic-Types/Natural-Numbers/#Nat___zero)h1:[¬](https://lean-lang.org/doc/reference/latest/Basic-Propositions/Logical-Connectives/#Not)n [<]](#manual-LT___mk) 11h2:[¬](https://lean-lang.org/doc/reference/latest/Basic-Propositions/Logical-Connectives/#Not)n > 13h3:[¬](https://lean-lang.org/doc/reference/latest/Basic-Propositions/Logical-Connectives/#Not)n [%]](#manual-HMod___mk) 2 [=](https://lean-lang.org/doc/reference/latest/Basic-Propositions/Propositional-Equality/#Eq___refl) 1h4:n ≠ 12⊢ [False]](#manual-False) [omega]](#manual-omega)All goals completed! 🐙)
[else]](#manual-termDepIfThenElse) "Twelve"
```

In this example, `[False.elim]](#manual-False___elim)` indicates to Lean that the current local context is logically inconsistent: proving `[False]](#manual-False)` suffices to abandon the branch.

Similarly, the definition of `[g]](#manual-g-_LPAR_in-Dead-Code-and-Subsingleton-Elimination_RPAR_)` appears to have the potential to be non-terminating.
However, the recursive call occurs on an unreachable path through the program.
The proof automation used for producing termination proofs can detect that the local assumptions are inconsistent.

```lean
def g (n : [Nat](https://lean-lang.org/doc/reference/latest/Basic-Types/Natural-Numbers/#Nat___zero)) : [String](https://lean-lang.org/doc/reference/latest/Basic-Types/Strings/#String___ofByteArray) :=
[if]](#manual-termIfThenElse) n < 11 [then]](#manual-termIfThenElse)
"Small"
[else]](#manual-termIfThenElse) [if]](#manual-termIfThenElse) n > 13 [then]](#manual-termIfThenElse)
"Large"
[else]](#manual-termIfThenElse) [if]](#manual-termIfThenElse) n % 2 = 1 [then]](#manual-termIfThenElse)
"Odd"
[else]](#manual-termIfThenElse) [if]](#manual-termIfThenElse) n ≠ 12 [then]](#manual-termIfThenElse)
[g]](#manual-g-_LPAR_in-Dead-Code-and-Subsingleton-Elimination_RPAR_) (n + 1)
[else]](#manual-termIfThenElse) "Twelve"
termination_by n
```

---



## Basic Propositions — 19.2. Logical Connectives {#manual-basic-propositions-192-logical-connectives}

> 📄 Source: https://lean-lang.org/doc/reference/latest/Basic-Propositions/Logical-Connectives/

Conjunction is implemented as the inductively defined proposition `[And]](#manual-And___intro)`.
The constructor `[And.intro]](#manual-And___intro)` represents the introduction rule for conjunction: to prove a conjunction, it suffices to prove both conjuncts.
Similarly, `[And.elim]](#manual-And___elim)` represents the elimination rule: given a proof of a conjunction and a proof of some other statement that assumes both conjuncts, the other statement can be proven.
Because `[And]](#manual-And___intro)` is a [subsingleton]](#manual---tech-term-subsingleton), `[And.elim]](#manual-And___elim)` can also be used as part of computing data.
However, it should not be confused with `[PProd](https://lean-lang.org/doc/reference/latest/Basic-Types/Tuples/#PProd___mk)`: using non-computable reasoning principles such as the Axiom of Choice to define data (including `[Prod](https://lean-lang.org/doc/reference/latest/Basic-Types/Tuples/#Prod___mk)`) causes Lean to be unable to compile and run the resulting program, while using them in a proof of a proposition causes no such issue.

In a [tactic]](#manual-tactics) proof, conjunctions can be proved using `[And.intro]](#manual-And___intro)` explicitly via `[apply]](#manual-apply)`, but `[constructor]](#manual-constructor)` is more common.
When multiple conjunctions are nested in a proof goal, `[and_intros]](#manual-and_intros)` can be used to apply `[And.intro]](#manual-And___intro)` in each relevant location.
Assumptions of conjunctions in the context can be simplified using `[cases]](#manual-cases)`, pattern matching with `[let]](#manual-let)` or `[match]](#manual-match)`, or `[rcases]](#manual-rcases)`.

structure

```lean
[And]](#manual-And___intro) (a b : Prop) : Prop



[And]](#manual-And___intro) (a b : Prop) : Prop
```

`[And]](#manual-And___intro) a b`, or `a ∧ b`, is the conjunction of propositions. It can be
constructed and destructed like a pair: if `ha : a` and `hb : b` then
`⟨ha, hb⟩ : a ∧ b`, and if `h : a ∧ b` then `h.[left]](#manual-And___intro) : a` and `h.[right]](#manual-And___intro) : b`.

Conventions for notations in identifiers:

- The recommended spelling of `∧` in identifiers is `[and](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___and)`.

Constructor

```lean
[And.intro]](#manual-And___intro)
```

`[And.intro]](#manual-And___intro) : a → b → a ∧ b` is the constructor for the And operation.

Fields

```lean
left : a
```

Extract the left conjunct from a conjunction. `h : a ∧ b` then
`h.[left]](#manual-And___intro)`, also notated as `h.[1]](#manual-And___intro)`, is a proof of `a`.

```lean
right : b
```

Extract the right conjunct from a conjunction. `h : a ∧ b` then
`h.[right]](#manual-And___intro)`, also notated as `h.[2]](#manual-And___intro)`, is a proof of `b`.

def

```lean
[And.elim.{u_1}]](#manual-And___elim) {a b : Prop} {α : Sort u_1} (f : a → b → α) (h : a [∧]](#manual-And___intro) b) :
  α



[And.elim.{u_1}]](#manual-And___elim) {a b : Prop} {α : Sort u_1}
  (f : a → b → α) (h : a [∧]](#manual-And___intro) b) : α
```

Non-dependent eliminator for `[And]](#manual-And___intro)`.

Disjunction implemented as the inductively defined proposition `[Or]](#manual-Or___inl)`.
It has two constructors, one for each introduction rule: a proof of either disjunct is sufficient to prove the disjunction.
While the definition of `[Or]](#manual-Or___inl)` is similar to that of `[Sum](https://lean-lang.org/doc/reference/latest/Basic-Types/Sum-Types/#Sum___inl)`, it is quite different in practice.
Because `[Sum](https://lean-lang.org/doc/reference/latest/Basic-Types/Sum-Types/#Sum___inl)` is a type, it is possible to check *which* constructor was used to create any given value.
`[Or]](#manual-Or___inl)`, on the other hand, forms propositions: terms that prove a disjunction cannot be interrogated to check which disjunct was true.
In other words, because `[Or]](#manual-Or___inl)` is not a [subsingleton]](#manual---tech-term-subsingleton), its proofs cannot be used as part of a computation.

In a [tactic]](#manual-tactics) proof, disjunctions can be proved using either constructor (`[Or.inl]](#manual-Or___inl)` or `[Or.inr]](#manual-Or___inl)`) explicitly via `[apply]](#manual-apply)`.
The `[left]](#manual-left)` and `[right]](#manual-right)` tactics select the left and right disjuncts.
Assumptions of disjunctions in the context can be simplified using `[cases]](#manual-cases)`, pattern matching with `[match]](#manual-match)`, or `[rcases]](#manual-rcases)`.

inductive predicate

```lean
[Or]](#manual-Or___inl) (a b : Prop) : Prop



[Or]](#manual-Or___inl) (a b : Prop) : Prop
```

`[Or]](#manual-Or___inl) a b`, or `a ∨ b`, is the disjunction of propositions. There are two
constructors for `[Or]](#manual-Or___inl)`, called `[Or.inl]](#manual-Or___inl) : a → a ∨ b` and `[Or.inr]](#manual-Or___inl) : b → a ∨ b`,
and you can use `[match]](#manual-match)` or `[cases]](#manual-cases)` to destruct an `[Or]](#manual-Or___inl)` assumption into the
two cases.

Conventions for notations in identifiers:

- The recommended spelling of `∨` in identifiers is `[or](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___or)`.

Constructors

```lean
[Or.inl]](#manual-Or___inl) {a b : Prop} (h : a) : a [∨]](#manual-Or___inl) b
```

`[Or.inl]](#manual-Or___inl)` is "left injection" into an `[Or]](#manual-Or___inl)`. If `h : a` then `[Or.inl]](#manual-Or___inl) h : a ∨ b`.

```lean
[Or.inr]](#manual-Or___inl) {a b : Prop} (h : b) : a [∨]](#manual-Or___inl) b
```

`[Or.inr]](#manual-Or___inl)` is "right injection" into an `[Or]](#manual-Or___inl)`. If `h : b` then `[Or.inr]](#manual-Or___inl) h : a ∨ b`.

When either disjunct is [decidable]](#manual---tech-term-decidable), it becomes possible to use `[Or]](#manual-Or___inl)` to compute data.
This is because the decision procedure's result provides a suitable branch condition.

def

```lean
[Or.by_cases.{u}]](#manual-Or___by_cases) {p q : Prop} [[Decidable]](#manual-Decidable___isFalse) p] {α : Sort u} (h : p [∨]](#manual-Or___inl) q)
  (h₁ : p → α) (h₂ : q → α) : α



[Or.by_cases.{u}]](#manual-Or___by_cases) {p q : Prop} [[Decidable]](#manual-Decidable___isFalse) p]
  {α : Sort u} (h : p [∨]](#manual-Or___inl) q) (h₁ : p → α)
  (h₂ : q → α) : α
```

Construct a non-Prop by cases on an `[Or]](#manual-Or___inl)`, when the left conjunct is decidable.

def

```lean
[Or.by_cases'.{u}]](#manual-Or___by_cases___) {q p : Prop} [[Decidable]](#manual-Decidable___isFalse) q] {α : Sort u} (h : p [∨]](#manual-Or___inl) q)
  (h₁ : p → α) (h₂ : q → α) : α



[Or.by_cases'.{u}]](#manual-Or___by_cases___) {q p : Prop}
  [[Decidable]](#manual-Decidable___isFalse) q] {α : Sort u} (h : p [∨]](#manual-Or___inl) q)
  (h₁ : p → α) (h₂ : q → α) : α
```

Construct a non-Prop by cases on an `[Or]](#manual-Or___inl)`, when the right conjunct is decidable.

Rather than encoding negation as an inductive type, `¬P` is defined to mean `P → [False]](#manual-False)`.
In other words, to prove a negation, it suffices to assume the negated statement and derive a contradiction.
This also means that `[False]](#manual-False)` can be derived immediately from a proof of a proposition and its negation, and then used to prove any proposition or inhabit any type.

def

```lean
[Not]](#manual-Not) (a : Prop) : Prop



[Not]](#manual-Not) (a : Prop) : Prop
```

`[Not]](#manual-Not) p`, or `¬p`, is the negation of `p`. It is defined to be `p → [False]](#manual-False)`,
so if your goal is `¬p` you can use `[intro]](#manual-intro) h` to turn the goal into
`h : p ⊢ False`, and if you have `hn : ¬p` and `h : p` then `hn h : [False]](#manual-False)`
and `(hn h).[elim]](#manual-False___elim)` will prove anything.
For more information: [Propositional Logic](https://lean-lang.org/theorem_proving_in_lean4/propositions_and_proofs.html#propositional-logic)

Conventions for notations in identifiers:

- The recommended spelling of `¬` in identifiers is `[not](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___not)`.

def

```lean
[absurd.{v}]](#manual-absurd) {a : Prop} {b : Sort v} (h₁ : a) (h₂ : [¬]](#manual-Not)a) : b



[absurd.{v}]](#manual-absurd) {a : Prop} {b : Sort v}
  (h₁ : a) (h₂ : [¬]](#manual-Not)a) : b
```

Anything follows from two contradictory hypotheses. Example:

```lean
example (hp : p) (hnp : ¬p) : q := [absurd]](#manual-absurd) hp hnp
```

For more information: [Propositional Logic](https://lean-lang.org/theorem_proving_in_lean4/propositions_and_proofs.html#propositional-logic)

def

```lean
[Not.elim.{u_1}]](#manual-Not___elim) {a : Prop} {α : Sort u_1} (H1 : [¬]](#manual-Not)a) (H2 : a) : α



[Not.elim.{u_1}]](#manual-Not___elim) {a : Prop} {α : Sort u_1}
  (H1 : [¬]](#manual-Not)a) (H2 : a) : α
```

*Ex falso* for negation: from `¬a` and `a` anything follows. This is the same as `[absurd]](#manual-absurd)` with
the arguments flipped, but it is in the `[Not]](#manual-Not)` namespace so that projection notation can be used.

Implication is represented using [function types]](#manual-function-types) in the [universe]](#manual---tech-term-universes) of [propositions]](#manual---tech-term-Propositions).
To prove `A → B`, it is enough to prove `B` after assuming `A`.
This corresponds to the typing rule for `fun`.
Similarly, the typing rule for function application corresponds to *modus ponens*: given a proof of `A → B` and a proof of `A`, `B` can be proved.

**Example: Truth-Functional Implication**

The representation of implication as functions in the universe of propositions is equivalent to the traditional definition in which `A → B` is defined as `(¬A) ∨ B`.
This can be proved using [propositional extensionality]](#manual---tech-term-Extensionality) and the law of the excluded middle:

```lean
theorem truth_functional_imp {A B : Prop} :
((¬ A) ∨ B) = (A → B) := byA:PropB:Prop⊢ [(]](#manual-Or___inl)[¬]](#manual-Not)A [∨]](#manual-Or___inl) B[)]](#manual-Or___inl) [=](https://lean-lang.org/doc/reference/latest/Basic-Propositions/Propositional-Equality/#Eq___refl) (A → B)
[apply]](#manual-apply) [propext]](#manual-propext)A:PropB:Prop⊢ [¬]](#manual-Not)A [∨]](#manual-Or___inl) B [↔]](#manual-Iff___intro) A → B
[constructor]](#manual-constructor)mpA:PropB:Prop⊢ [¬]](#manual-Not)A [∨]](#manual-Or___inl) B → A → BmprA:PropB:Prop⊢ (A → B) → [¬]](#manual-Not)A [∨]](#manual-Or___inl) B
.mpA:PropB:Prop⊢ [¬]](#manual-Not)A [∨]](#manual-Or___inl) B → A → B [rintro]](#manual-rintro) (h | h) amp.inlA:PropB:Proph:[¬]](#manual-Not)Aa:A⊢ Bmp.inrA:PropB:Proph:Ba:A⊢ B <;>mp.inlA:PropB:Proph:[¬]](#manual-Not)Aa:A⊢ Bmp.inrA:PropB:Proph:Ba:A⊢ B [trivial]](#manual-trivial)All goals completed! 🐙
.mprA:PropB:Prop⊢ (A → B) → [¬]](#manual-Not)A [∨]](#manual-Or___inl) B [intro]](#manual-intro) hmprA:PropB:Proph:A → B⊢ [¬]](#manual-Not)A [∨]](#manual-Or___inl) B
[by_cases]](#manual-by_cases) AposA:PropB:Proph:A → Bh✝:A⊢ [¬]](#manual-Not)A [∨]](#manual-Or___inl) BnegA:PropB:Proph:A → Bh✝:[¬]](#manual-Not)A⊢ [¬]](#manual-Not)A [∨]](#manual-Or___inl) B
.posA:PropB:Proph:A → Bh✝:A⊢ [¬]](#manual-Not)A [∨]](#manual-Or___inl) B [apply]](#manual-apply) [Or.inr]](#manual-Or___inl)posA:PropB:Proph:A → Bh✝:A⊢ B; [solve_by_elim]](#manual-solve_by_elim)All goals completed! 🐙
.negA:PropB:Proph:A → Bh✝:[¬]](#manual-Not)A⊢ [¬]](#manual-Not)A [∨]](#manual-Or___inl) B [apply]](#manual-apply) [Or.inl]](#manual-Or___inl)negA:PropB:Proph:A → Bh✝:[¬]](#manual-Not)A⊢ [¬]](#manual-Not)A; [trivial]](#manual-trivial)All goals completed! 🐙
```

Logical equivalence, or “if and only if”, is represented using a structure that is equivalent to the conjunction of both directions of the implication.

structure

```lean
[Iff]](#manual-Iff___intro) (a b : Prop) : Prop



[Iff]](#manual-Iff___intro) (a b : Prop) : Prop
```

If and only if, or logical bi-implication. `a ↔ b` means that `a` implies `b` and vice versa.
By `[propext]](#manual-propext)`, this implies that `a` and `b` are equal and hence any expression involving `a`
is equivalent to the corresponding expression with `b` instead.

Conventions for notations in identifiers:

- The recommended spelling of `↔` in identifiers is `iff`.
- The recommended spelling of `<->` in identifiers is `iff` (prefer `↔` over `<->`).

Constructor

```lean
[Iff.intro]](#manual-Iff___intro)
```

If `a → b` and `b → a` then `a` and `b` are equivalent.

Fields

```lean
mp : a → b
```

Modus ponens for if and only if. If `a ↔ b` and `a`, then `b`.

```lean
mpr : b → a
```

Modus ponens for if and only if, reversed. If `a ↔ b` and `b`, then `a`.

def

```lean
[Iff.elim.{u_1}]](#manual-Iff___elim) {a b : Prop} {α : Sort u_1} (f : (a → b) → (b → a) → α)
  (h : a [↔]](#manual-Iff___intro) b) : α



[Iff.elim.{u_1}]](#manual-Iff___elim) {a b : Prop} {α : Sort u_1}
  (f : (a → b) → (b → a) → α)
  (h : a [↔]](#manual-Iff___intro) b) : α
```

Non-dependent eliminator for `[Iff]](#manual-Iff___intro)`.

syntaxPropositional Connectives

The logical connectives other than implication are typically referred to using dedicated syntax, rather than via their defined names:

```lean
term ::= ...
    | term ∧ term
```

```lean
term ::= ...
    | term ∨ term
```

```lean
term ::= ...
    | ¬ term
```

```lean
term ::= ...
    | term ↔ term
```

---



## Basic Propositions — 19.3. Quantifiers {#manual-basic-propositions-193-quantifiers}

> 📄 Source: https://lean-lang.org/doc/reference/latest/Basic-Propositions/Quantifiers/

Just as implication is implemented as ordinary function types in `Prop`, universal quantification is implemented as dependent function types in `Prop`.
Because `Prop` is [impredicative]](#manual---tech-term-impredicative), any function type in which the [codomain]](#manual---tech-term-codomain) is a `Prop` is itself a `Prop`, even if the [domain]](#manual---tech-term-domain) is a `Type`.
The typing rules for dependent functions precisely match the introduction and elimination rules for universal quantification: if a predicate holds for any arbitrarily chosen element of a type, then it holds universally.
If a predicate holds universally, then it can be instantiated to a proof for any individual.

syntaxUniversal Quantification

```lean
term ::= ...
    | ∀ ident ident* (: term)?, term
```

```lean
term ::= ...
    | forall ident ident* (: term)?, term
```

```lean
term ::= ...
    | ∀ (ident | [hole]](#manual-Lean___Parser___Term___hole) | bracketedBinder) (ident | [hole]](#manual-Lean___Parser___Term___hole) | bracketedBinder)*, term
```

```lean
term ::= ...
    | forall (ident | [hole]](#manual-Lean___Parser___Term___hole) | bracketedBinder) (ident | [hole]](#manual-Lean___Parser___Term___hole) | bracketedBinder)*, term
```

Universal quantifiers bind one or more variables, which are then in scope in the final term.
The identifiers may also be `_`.
With parenthesized type annotations, multiple bound variables may have different types, while the unparenthesized variant requires that all have the same type.

Even though universal quantifiers are represented by functions, their proofs should not be thought of as computations.
Because of proof irrelevance and the elimination restriction for propositions, there's no way to actually compute data using these proofs.
As a result, they are free to use reasoning principles that are not readily computed, such as the classical Axiom of Choice.

Existential quantification is implemented as a structure that is similar to `[Subtype](https://lean-lang.org/doc/reference/latest/Basic-Types/Subtypes/#Subtype___mk)` and `[Sigma](https://lean-lang.org/doc/reference/latest/Basic-Types/Tuples/#Sigma___mk)`: it contains a *witness*, which is a value that satisfies the predicate, along with a proof that the witness does in fact satisfy the predicate.
In other words, it is a form of dependent pair type.
Unlike both `[Subtype](https://lean-lang.org/doc/reference/latest/Basic-Types/Subtypes/#Subtype___mk)` and `[Sigma](https://lean-lang.org/doc/reference/latest/Basic-Types/Tuples/#Sigma___mk)`, it is a [proposition]](#manual---tech-term-Propositions); this means that programs cannot in general use a proof of an existential statement to obtain a value that satisfies the predicate.

When writing a proof, the `[exists]](#manual-exists)` tactic allows one (or more) witness(es) to be specified for a (potentially nested) existential statement.
The `[constructor]](#manual-constructor)` tactic, on the other hand, creates a [metavariable]](#manual---tech-term-metavariables) for the witness; providing a proof of the predicate may solve the metavariable as well.
The components of an existential assumption can be made available individually by pattern matching with `[let]](#manual-let)` or `[match]](#manual-match)`, as well as by using `[cases]](#manual-cases)` or `[rcases]](#manual-rcases)`.

**Example: Proving Existential Statements**

When proving that there exists some natural number that is the sum of four and five, the `[exists]](#manual-exists)` tactic expects the sum to be provided, constructing the equality proof using `[trivial]](#manual-trivial)`:

```lean
theorem ex_four_plus_five : ∃ n, 4 + 5 = n := by⊢ [∃]](#manual-Exists___intro) n[,]](#manual-Exists___intro) 4 [+]](#manual-HAdd___mk) 5 [=](https://lean-lang.org/doc/reference/latest/Basic-Propositions/Propositional-Equality/#Eq___refl) n
[exists]](#manual-exists) 9All goals completed! 🐙
```

The `[constructor]](#manual-constructor)` tactic, on the other hand, expects a proof.
The `[rfl]](#manual-rfl)` tactic causes the sum to be determined as a side effect of checking definitional equality.

```lean
theorem ex_four_plus_five' : ∃ n, 4 + 5 = n := by⊢ [∃]](#manual-Exists___intro) n[,]](#manual-Exists___intro) 4 [+]](#manual-HAdd___mk) 5 [=](https://lean-lang.org/doc/reference/latest/Basic-Propositions/Propositional-Equality/#Eq___refl) n
[constructor]](#manual-constructor)h⊢ 4 [+]](#manual-HAdd___mk) 5 [=](https://lean-lang.org/doc/reference/latest/Basic-Propositions/Propositional-Equality/#Eq___refl) ?ww⊢ [Nat](https://lean-lang.org/doc/reference/latest/Basic-Types/Natural-Numbers/#Nat___zero)
[rfl]](#manual-rfl)All goals completed! 🐙
```

inductive predicate

```lean
[Exists.{u}]](#manual-Exists___intro) {α : Sort u} (p : α → Prop) : Prop



[Exists.{u}]](#manual-Exists___intro) {α : Sort u} (p : α → Prop) :
  Prop
```

Existential quantification. If `p : α → Prop` is a predicate, then `∃ x : α, p x`
asserts that there is some `x` of type `α` such that `p x` holds.
To create an existential proof, use the `[exists]](#manual-exists)` tactic,
or the anonymous constructor notation `⟨x, h⟩`.
To unpack an existential, use `[cases]](#manual-cases) h` where `h` is a proof of `∃ x : α, p x`,
or `[let]](#manual-let) ⟨x, hx⟩ := h`.

Because Lean has proof irrelevance, any two proofs of an existential are
definitionally equal. One consequence of this is that it is impossible to recover the
witness of an existential from the mere fact of its existence.
For example, the following does not compile:

```
example (h : ∃ x : Nat, x = x) : Nat :=
  let ⟨x, _⟩ := h  -- fail, because the goal is `Nat : Type`
  x
```

The error message `recursor 'Exists.casesOn' can only eliminate into Prop` means
that this only works when the current goal is another proposition:

```lean
example (h : ∃ x : [Nat](https://lean-lang.org/doc/reference/latest/Basic-Types/Natural-Numbers/#Nat___zero), x = x) : [True]](#manual-True___intro) :=
let ⟨x, _⟩ := h -- ok, because the goal is `True : Prop`
trivial
```

Constructors

```lean
[Exists.intro.{u}]](#manual-Exists___intro) {α : Sort u} {p : α → Prop} (w : α)
  (h : p w) : [Exists]](#manual-Exists___intro) p
```

Existential introduction. If `a : α` and `h : p a`,
then `⟨a, h⟩` is a proof that `∃ x : α, p x`.

syntaxExistential Quantification

```lean
term ::= ...
    | ∃ ident ident* (: term)?, term
```

```lean
term ::= ...
    | exists ident ident* (: term)?, term
```

```lean
term ::= ...
    | ∃ bracketedExplicitBinders bracketedExplicitBinders*, term
```

```lean
term ::= ...
    | exists bracketedExplicitBinders bracketedExplicitBinders*, term
```

Existential quantifiers bind one or more variables, which are then in scope in the final term.
The identifiers may also be `_`.
With parenthesized type annotations, multiple bound variables may have different types, while the unparenthesized variant requires that all have the same type.
If more than one variable is bound, then the result is multiple instances of `[Exists]](#manual-Exists___intro)`, nested to the right.

def

```lean
[Exists.choose.{u_1}]](#manual-Exists___choose) {α : Sort u_1} {p : α → Prop} (P : [∃]](#manual-Exists___intro) a[,]](#manual-Exists___intro) p a) : α



[Exists.choose.{u_1}]](#manual-Exists___choose) {α : Sort u_1}
  {p : α → Prop} (P : [∃]](#manual-Exists___intro) a[,]](#manual-Exists___intro) p a) : α
```

Extract an element from an existential statement, using `Classical.choose`.

---



## Basic Propositions — 19.4. Propositional Equality {#manual-basic-propositions-194-propositional-equality}

> 📄 Source: https://lean-lang.org/doc/reference/latest/Basic-Propositions/Propositional-Equality/

*Propositional equality* is the operator that allows the equality of two terms to be stated as a proposition.
[Definitional equality]](#manual---tech-term-definitional-equality) is checked automatically where necessary.
As a result, its expressive power is limited in order to keep the algorithm that checks it fast and understandable.
Propositional equality, on the other hand, must be explicitly proved and explicitly used—Lean checks the validity of the proofs, rather than determining whether the statement is true.
In exchange, it is much more expressive: many terms are propositionally equal without being definitionally equal.

Propositional equality is defined as an inductive type.
Its sole constructor `[Eq.refl]](#manual-Eq___refl)` requires that both of the equated values be the same; this is implicitly an appeal to [definitional equality]](#manual---tech-term-definitional-equality).
Propositional equality can also be thought of as the least reflexive relation modulo definitional equality.
In addition to `[Eq.refl]](#manual-Eq___refl)`, equality proofs are generated by the `[propext]](#manual-propext)` and `[Quot.sound]](#manual-Quot___sound)` axioms.

inductive predicate

```lean
[Eq.{u_1}]](#manual-Eq___refl) {α : Sort u_1} : α → α → Prop



[Eq.{u_1}]](#manual-Eq___refl) {α : Sort u_1} : α → α → Prop
```

The equality relation. It has one introduction rule, `[Eq.refl]](#manual-Eq___refl)`.
We use `a = b` as notation for `[Eq]](#manual-Eq___refl) a b`.
A fundamental property of equality is that it is an equivalence relation.

```lean
[variable]](#manual-Lean___Parser___Command___variable) (α : Type) (a b c d : α)
[variable]](#manual-Lean___Parser___Command___variable) (hab : a = b) (hcb : c = b) (hcd : c = d)
example : a = d :=
[Eq.trans]](#manual-Eq___trans) ([Eq.trans]](#manual-Eq___trans) hab ([Eq.symm]](#manual-Eq___symm) hcb)) hcd
```

Equality is much more than an equivalence relation, however. It has the important property that every assertion
respects the equivalence, in the sense that we can substitute equal expressions without changing the truth value.
That is, given `h1 : a = b` and `h2 : p a`, we can construct a proof for `p b` using substitution: `[Eq.subst]](#manual-Eq___subst) h1 h2`.
Example:

```lean
example (α : Type) (a b : α) (p : α → Prop)
(h1 : a = b) (h2 : p a) : p b :=
[Eq.subst]](#manual-Eq___subst) h1 h2
example (α : Type) (a b : α) (p : α → Prop)
(h1 : a = b) (h2 : p a) : p b :=
h1 ▸ h2
```

The triangle in the second presentation is a macro built on top of `[Eq.subst]](#manual-Eq___subst)` and `[Eq.symm]](#manual-Eq___symm)`, and you can enter it by typing `\t`.
For more information: [Equality](https://lean-lang.org/theorem_proving_in_lean4/quantifiers_and_equality.html#equality)

Conventions for notations in identifiers:

- The recommended spelling of `=` in identifiers is `eq`.

Constructors

```lean
[Eq.refl.{u_1}]](#manual-Eq___refl) {α : Sort u_1} (a : α) : a [=]](#manual-Eq___refl) a
```

`[Eq.refl]](#manual-Eq___refl) a : a = a` is reflexivity, the unique constructor of the
equality type. See also `[rfl]](#manual-rfl-next)`, which is usually used instead.

syntaxPropositional Equality

```lean
term ::= ...
    | term = term
```

Propositional equality is typically denoted by the infix `=` operator.

def

```lean
[rfl.{u}]](#manual-rfl-next) {α : Sort u} {a : α} : a [=]](#manual-Eq___refl) a



[rfl.{u}]](#manual-rfl-next) {α : Sort u} {a : α} : a [=]](#manual-Eq___refl) a
```

`[rfl]](#manual-rfl-next) : a = a` is the unique constructor of the equality type. This is the
same as `[Eq.refl]](#manual-Eq___refl)` except that it takes `a` implicitly instead of explicitly.

This is a more powerful theorem than it may appear at first, because although
the statement of the theorem is `a = a`, Lean will allow anything that is
definitionally equal to that type. So, for instance, `2 + 2 = 4` is proven in
Lean by `[rfl]](#manual-rfl-next)`, because both sides are the same up to definitional equality.

theorem

```lean
[Eq.symm.{u}]](#manual-Eq___symm) {α : Sort u} {a b : α} (h : a [=]](#manual-Eq___refl) b) : b [=]](#manual-Eq___refl) a



[Eq.symm.{u}]](#manual-Eq___symm) {α : Sort u} {a b : α}
  (h : a [=]](#manual-Eq___refl) b) : b [=]](#manual-Eq___refl) a
```

Equality is symmetric: if `a = b` then `b = a`.

Because this is in the `[Eq]](#manual-Eq___refl)` namespace, if you have a variable `h : a = b`,
`h.[symm]](#manual-Eq___symm)` can be used as shorthand for `[Eq.symm]](#manual-Eq___symm) h` as a proof of `b = a`.

For more information: [Equality](https://lean-lang.org/theorem_proving_in_lean4/quantifiers_and_equality.html#equality)

theorem

```lean
[Eq.trans.{u}]](#manual-Eq___trans) {α : Sort u} {a b c : α} (h₁ : a [=]](#manual-Eq___refl) b) (h₂ : b [=]](#manual-Eq___refl) c) : a [=]](#manual-Eq___refl) c



[Eq.trans.{u}]](#manual-Eq___trans) {α : Sort u} {a b c : α}
  (h₁ : a [=]](#manual-Eq___refl) b) (h₂ : b [=]](#manual-Eq___refl) c) : a [=]](#manual-Eq___refl) c
```

Equality is transitive: if `a = b` and `b = c` then `a = c`.

Because this is in the `[Eq]](#manual-Eq___refl)` namespace, if you have variables or expressions
`h₁ : a = b` and `h₂ : b = c`, you can use `h₁.[trans]](#manual-Eq___trans) h₂ : a = c` as shorthand
for `[Eq.trans]](#manual-Eq___trans) h₁ h₂`.

For more information: [Equality](https://lean-lang.org/theorem_proving_in_lean4/quantifiers_and_equality.html#equality)

theorem

```lean
[Eq.subst.{u}]](#manual-Eq___subst) {α : Sort u} {motive : α → Prop} {a b : α} (h₁ : a [=]](#manual-Eq___refl) b)
  (h₂ : motive a) : motive b



[Eq.subst.{u}]](#manual-Eq___subst) {α : Sort u}
  {motive : α → Prop} {a b : α}
  (h₁ : a [=]](#manual-Eq___refl) b) (h₂ : motive a) : motive b
```

The substitution principle for equality. If `a = b` and `P a` holds,
then `P b` also holds. We conventionally use the name `motive` for `P` here,
so that you can specify it explicitly using e.g.
`[Eq.subst]](#manual-Eq___subst) (motive := fun x => x < 5)` if it is not otherwise inferred correctly.

This theorem is the underlying mechanism behind the `[rw]](#manual-rw)` tactic, which is
essentially a fancy algorithm for finding good `motive` arguments to usefully
apply this theorem to replace occurrences of `a` with `b` in the goal or
hypotheses.

For more information: [Equality](https://lean-lang.org/theorem_proving_in_lean4/quantifiers_and_equality.html#equality)

def

```lean
[cast.{u}]](#manual-cast) {α β : Sort u} (h : α [=]](#manual-Eq___refl) β) (a : α) : β



[cast.{u}]](#manual-cast) {α β : Sort u} (h : α [=]](#manual-Eq___refl) β)
  (a : α) : β
```

Cast across a type equality. If `h : α = β` is an equality of types, and
`a : α`, then `a : β` will usually not typecheck directly, but this function
will allow you to work around this and embed `a` in type `β` as `[cast]](#manual-cast) h a : β`.

It is best to avoid this function if you can, because it is more complicated
to reason about terms containing casts, but if the types don't match up
definitionally sometimes there isn't anything better you can do.

For more information: [Equality](https://lean-lang.org/theorem_proving_in_lean4/quantifiers_and_equality.html#equality)

theorem

```lean
[congr.{u, v}]](#manual-congr-next) {α : Sort u} {β : Sort v} {f₁ f₂ : α → β} {a₁ a₂ : α}
  (h₁ : f₁ [=]](#manual-Eq___refl) f₂) (h₂ : a₁ [=]](#manual-Eq___refl) a₂) : f₁ a₁ [=]](#manual-Eq___refl) f₂ a₂



[congr.{u, v}]](#manual-congr-next) {α : Sort u} {β : Sort v}
  {f₁ f₂ : α → β} {a₁ a₂ : α}
  (h₁ : f₁ [=]](#manual-Eq___refl) f₂) (h₂ : a₁ [=]](#manual-Eq___refl) a₂) :
  f₁ a₁ [=]](#manual-Eq___refl) f₂ a₂
```

Congruence in both function and argument. If `f₁ = f₂` and `a₁ = a₂` then
`f₁ a₁ = f₂ a₂`. This only works for nondependent functions; the theorem
statement is more complex in the dependent case.

For more information: [Equality](https://lean-lang.org/theorem_proving_in_lean4/quantifiers_and_equality.html#equality)

theorem

```lean
[congrFun.{u, v}]](#manual-congrFun) {α : Sort u} {β : α → Sort v} {f g : (x : α) → β x}
  (h : f [=]](#manual-Eq___refl) g) (a : α) : f a [=]](#manual-Eq___refl) g a



[congrFun.{u, v}]](#manual-congrFun) {α : Sort u}
  {β : α → Sort v} {f g : (x : α) → β x}
  (h : f [=]](#manual-Eq___refl) g) (a : α) : f a [=]](#manual-Eq___refl) g a
```

Congruence in the function part of an application: If `f = g` then `f a = g a`.

theorem

```lean
[congrArg.{u, v}]](#manual-congrArg) {α : Sort u} {β : Sort v} {a₁ a₂ : α} (f : α → β)
  (h : a₁ [=]](#manual-Eq___refl) a₂) : f a₁ [=]](#manual-Eq___refl) f a₂



[congrArg.{u, v}]](#manual-congrArg) {α : Sort u} {β : Sort v}
  {a₁ a₂ : α} (f : α → β) (h : a₁ [=]](#manual-Eq___refl) a₂) :
  f a₁ [=]](#manual-Eq___refl) f a₂
```

Congruence in the function argument: if `a₁ = a₂` then `f a₁ = f a₂` for
any (nondependent) function `f`. This is more powerful than it might look at first, because
you can also use a lambda expression for `f` to prove that
`<something containing a₁> = <something containing a₂>`. This function is used
internally by tactics like `[congr]](#manual-congr-next)` and `[simp]](#manual-simp)` to apply equalities inside
subterms.

For more information: [Equality](https://lean-lang.org/theorem_proving_in_lean4/quantifiers_and_equality.html#equality)

def

```lean
[Eq.mp.{u}]](#manual-Eq___mp) {α β : Sort u} (h : α [=]](#manual-Eq___refl) β) (a : α) : β



[Eq.mp.{u}]](#manual-Eq___mp) {α β : Sort u} (h : α [=]](#manual-Eq___refl) β)
  (a : α) : β
```

If `h : α = β` is a proof of type equality, then `h.[mp]](#manual-Eq___mp) : α → β` is the induced
"cast" operation, mapping elements of `α` to elements of `β`.

You can prove theorems about the resulting element by induction on `h`, since
`[rfl]](#manual-rfl-next).[mp]](#manual-Eq___mp)` is definitionally the identity function.

def

```lean
[Eq.mpr.{u}]](#manual-Eq___mpr) {α β : Sort u} (h : α [=]](#manual-Eq___refl) β) (b : β) : α



[Eq.mpr.{u}]](#manual-Eq___mpr) {α β : Sort u} (h : α [=]](#manual-Eq___refl) β)
  (b : β) : α
```

If `h : α = β` is a proof of type equality, then `h.[mpr]](#manual-Eq___mpr) : β → α` is the induced
"cast" operation in the reverse direction, mapping elements of `β` to elements of `α`.

You can prove theorems about the resulting element by induction on `h`, since
`[rfl]](#manual-rfl-next).[mpr]](#manual-Eq___mpr)` is definitionally the identity function.

syntaxCasting

```lean
term ::= ...
    | term ▸ term
```

When a term's type includes one side of an equality as a sub-term, it can be rewritten using the `▸` operator.
If both sides of the equality occur in the term's type, then the left side is rewritten to the right.

### 19.4.1. Uniqueness of Equality Proofs {#manual-UIP}

Because of definitional proof irrelevance, propositional equality proofs are *unique*: two mathematical objects cannot be equal in different ways.

```lean
theorem Eq.unique {α : Sort u}
(x y : α)
(p1 p2 : x = y) :
p1 = p2 := byα:Sort ux:αy:αp1:x [=]](#manual-Eq___refl) yp2:x [=]](#manual-Eq___refl) y⊢ p1 [=]](#manual-Eq___refl) p2
[rfl]](#manual-rfl)All goals completed! 🐙
```

Streicher's axiom K (Streicher, 1993)Thomas Streicher, 1993. *[Investigations into Intensional Type Theory](https://www2.mathematik.tu-darmstadt.de/~streicher/HabilStreicher.pdf)*. Habilitation, Ludwig-Maximilians-Universität München is also a consequence of definitional proof irrelevance, as is its computation rule.
Axiom K is a principle that's logically equivalent to `[Eq.unique]](#manual-Eq___unique)`, implemented as an alternative [recursor]](#manual---tech-term-recursor) for propositional equality.

```lean
def K {α : Sort u}
{motive : {x : α} → x = x → Sort v}
(d : {x : α} → motive ([Eq.refl]](#manual-Eq___refl) x))
(x : α) (z : x = x) :
motive z :=
d
example {α : Sort u} {a : α}
{motive : {x : α} → x = x → Sort u}
{d : {x : α} → motive ([Eq.refl]](#manual-Eq___refl) x)} :
[K]](#manual-K) (motive := motive) d a [rfl]](#manual-rfl-next) = d := byα:Sort ua:αmotive:{x : α} → x [=]](#manual-Eq___refl) x → Sort ud:{x : α} → motive ⋯⊢ [K]](#manual-K) (fun {x} => d) a ⋯ [=]](#manual-Eq___refl) d
[rfl]](#manual-rfl)All goals completed! 🐙
```

### 19.4.2. Heterogeneous Equality {#manual-HEq}

*Heterogeneous equality* is a version of [propositional equality]](#manual---tech-term-Propositional-equality) that does not require that the two equated terms have the same type.
However, *proving* that the terms are equal using its version of `[rfl]](#manual-rfl-next)` requires that both the types and the terms are definitionally equal.
In other words, it allows more statements to be formulated.

Heterogeneous equality is typically less convenient in practice than ordinary propositional equality.
The greater flexibility afforded by not requiring both sides of the equality to have the same type means that it has fewer useful properties.
It is often encountered as a result of dependent pattern matching: the `[split]](#manual-split)` tactic and functional induction add heterogeneous equality assumptions to the context when the ordinary equality assumptions that are needed to accurate reflect the corresponding control flow would not be type correct.
In these cases, the built-in automation has no choice but to use heterogeneous equality.

inductive predicate

```lean
[HEq.{u}]](#manual-HEq___refl) {α : Sort u} : α → {β : Sort u} → β → Prop



[HEq.{u}]](#manual-HEq___refl) {α : Sort u} :
  α → {β : Sort u} → β → Prop
```

Heterogeneous equality. `a ≍ b` asserts that `a` and `b` have the same
type, and casting `a` across the equality yields `b`, and vice versa.

You should avoid using this type if you can. Heterogeneous equality does not
have all the same properties as `[Eq]](#manual-Eq___refl)`, because the assumption that the types of
`a` and `b` are equal is often too weak to prove theorems of interest. One
public important non-theorem is the analogue of `[congr]](#manual-congr-next)`: If `f ≍ g` and `x ≍ y`
and `f x` and `g y` are well typed it does not follow that `f x ≍ g y`.
(This does follow if you have `f = g` instead.) However if `a` and `b` have
the same type then `a = b` and `a ≍ b` are equivalent.

Conventions for notations in identifiers:

- The recommended spelling of `≍` in identifiers is `heq`.

Constructors

```lean
[HEq.refl.{u}]](#manual-HEq___refl) {α : Sort u} (a : α) : a [≍]](#manual-HEq___refl) a
```

Reflexivity of heterogeneous equality.

syntaxHeterogeneous Equality

```lean
term ::= ...
    | term ≍ term
```

Heterogeneous equality `[HEq]](#manual-HEq___refl) x y` can be written `x ≍ y`.

def

```lean
[HEq.rfl.{u}]](#manual-HEq___rfl) {α : Sort u} {a : α} : a [≍]](#manual-HEq___refl) a



[HEq.rfl.{u}]](#manual-HEq___rfl) {α : Sort u} {a : α} : a [≍]](#manual-HEq___refl) a
```

A version of `[HEq.refl]](#manual-HEq___refl)` with an implicit argument.

**Example: Heterogeneous Equality**

The type `Vector α n` is a wrapper around an `[Array](https://lean-lang.org/doc/reference/latest/Basic-Types/Arrays/#Array___mk) α` that includes a proof that the array has size `n`.
Appending `Vector`s is associative, but this fact cannot be straightforwardly stated using ordinary propositional equality:

```lean
[variable]](#manual-Lean___Parser___Command___variable)
{xs : Vector α l₁} {ys : Vector α l₂} {zs : Vector α l₃}
set_option linter.unusedVariables false
```

```lean
theorem Vector.append_associative :
xs ++ (ys ++ zs) = (xs ++ ys) ++ zs := by⊢ sorry [sorry]](#manual-sorry)All goals completed! 🐙
```

The problem is that the associativity of addition of natural numbers holds propositionally, but not definitionally:

```lean
Type mismatch
  xs [++]](#manual-HAppend___mk) ys [++]](#manual-HAppend___mk) zs
has type
  Vector α [(]](#manual-HAdd___mk)l₁ [+]](#manual-HAdd___mk) l₂ [+]](#manual-HAdd___mk) l₃[)]](#manual-HAdd___mk)
but is expected to have type
  Vector α [(]](#manual-HAdd___mk)l₁ [+]](#manual-HAdd___mk) [(]](#manual-HAdd___mk)l₂ [+]](#manual-HAdd___mk) l₃[)]](#manual-HAdd___mk)[)]](#manual-HAdd___mk)
```

One solution to this problem is to use the associativity of natural number addition in the statement:

```lean
theorem Vector.append_associative' :
xs ++ (ys ++ zs) =
Nat.add_assoc _ _ _ ▸ ((xs ++ ys) ++ zs) := byα:Type ul₁:[Nat](https://lean-lang.org/doc/reference/latest/Basic-Types/Natural-Numbers/#Nat___zero)l₂:[Nat](https://lean-lang.org/doc/reference/latest/Basic-Types/Natural-Numbers/#Nat___zero)l₃:[Nat](https://lean-lang.org/doc/reference/latest/Basic-Types/Natural-Numbers/#Nat___zero)xs:Vector α l₁ys:Vector α l₂zs:Vector α l₃⊢ xs [++]](#manual-HAppend___mk) [(]](#manual-HAppend___mk)ys [++]](#manual-HAppend___mk) zs[)]](#manual-HAppend___mk) [=]](#manual-Eq___refl) ⋯ ▸ [(]](#manual-HAppend___mk)xs [++]](#manual-HAppend___mk) ys [++]](#manual-HAppend___mk) zs[)]](#manual-HAppend___mk)
[sorry]](#manual-sorry)All goals completed! 🐙
```

However, such proof statements can be difficult to work with in certain circumstances.

Another is to use heterogeneous equality:

```lean
theorem Vector.append_associative :
[HEq]](#manual-HEq___refl) (xs ++ (ys ++ zs)) ((xs ++ ys) ++ zs) := byα:Type ul₁:[Nat](https://lean-lang.org/doc/reference/latest/Basic-Types/Natural-Numbers/#Nat___zero)l₂:[Nat](https://lean-lang.org/doc/reference/latest/Basic-Types/Natural-Numbers/#Nat___zero)l₃:[Nat](https://lean-lang.org/doc/reference/latest/Basic-Types/Natural-Numbers/#Nat___zero)xs:Vector α l₁ys:Vector α l₂zs:Vector α l₃⊢ xs [++]](#manual-HAppend___mk) [(]](#manual-HAppend___mk)ys [++]](#manual-HAppend___mk) zs[)]](#manual-HAppend___mk) [≍]](#manual-HEq___refl) xs [++]](#manual-HAppend___mk) ys [++]](#manual-HAppend___mk) zs [sorry]](#manual-sorry)All goals completed! 🐙
```

In this case, [the simplifier]](#manual-the-simplifier) can rewrite both sides of the equation without having to preserve their types.
However, proving the theorem does require eventually proving that the lengths nonetheless match.

```lean
theorem Vector.append_associative :
[HEq]](#manual-HEq___refl) (xs ++ (ys ++ zs)) ((xs ++ ys) ++ zs) := byα:Type ul₁:[Nat](https://lean-lang.org/doc/reference/latest/Basic-Types/Natural-Numbers/#Nat___zero)l₂:[Nat](https://lean-lang.org/doc/reference/latest/Basic-Types/Natural-Numbers/#Nat___zero)l₃:[Nat](https://lean-lang.org/doc/reference/latest/Basic-Types/Natural-Numbers/#Nat___zero)xs:Vector α l₁ys:Vector α l₂zs:Vector α l₃⊢ xs [++]](#manual-HAppend___mk) [(]](#manual-HAppend___mk)ys [++]](#manual-HAppend___mk) zs[)]](#manual-HAppend___mk) [≍]](#manual-HEq___refl) xs [++]](#manual-HAppend___mk) ys [++]](#manual-HAppend___mk) zs
[cases]](#manual-cases) xsmkα:Type ul₁:[Nat](https://lean-lang.org/doc/reference/latest/Basic-Types/Natural-Numbers/#Nat___zero)l₂:[Nat](https://lean-lang.org/doc/reference/latest/Basic-Types/Natural-Numbers/#Nat___zero)l₃:[Nat](https://lean-lang.org/doc/reference/latest/Basic-Types/Natural-Numbers/#Nat___zero)ys:Vector α l₂zs:Vector α l₃toArray✝:[Array](https://lean-lang.org/doc/reference/latest/Basic-Types/Arrays/#Array___mk) αsize_toArray✝:toArray✝.[size](https://lean-lang.org/doc/reference/latest/Basic-Types/Arrays/#Array___size) [=]](#manual-Eq___refl) l₁⊢ mk toArray✝ size_toArray✝ [++]](#manual-HAppend___mk) [(]](#manual-HAppend___mk)ys [++]](#manual-HAppend___mk) zs[)]](#manual-HAppend___mk) [≍]](#manual-HEq___refl) mk toArray✝ size_toArray✝ [++]](#manual-HAppend___mk) ys [++]](#manual-HAppend___mk) zs; [cases]](#manual-cases) ysmk.mkα:Type ul₁:[Nat](https://lean-lang.org/doc/reference/latest/Basic-Types/Natural-Numbers/#Nat___zero)l₂:[Nat](https://lean-lang.org/doc/reference/latest/Basic-Types/Natural-Numbers/#Nat___zero)l₃:[Nat](https://lean-lang.org/doc/reference/latest/Basic-Types/Natural-Numbers/#Nat___zero)zs:Vector α l₃toArray✝¹:[Array](https://lean-lang.org/doc/reference/latest/Basic-Types/Arrays/#Array___mk) αsize_toArray✝¹:toArray✝.[size](https://lean-lang.org/doc/reference/latest/Basic-Types/Arrays/#Array___size) [=]](#manual-Eq___refl) l₁toArray✝:[Array](https://lean-lang.org/doc/reference/latest/Basic-Types/Arrays/#Array___mk) αsize_toArray✝:toArray✝.[size](https://lean-lang.org/doc/reference/latest/Basic-Types/Arrays/#Array___size) [=]](#manual-Eq___refl) l₂⊢ mk toArray✝¹ size_toArray✝¹ [++]](#manual-HAppend___mk) [(]](#manual-HAppend___mk)mk toArray✝ size_toArray✝ [++]](#manual-HAppend___mk) zs[)]](#manual-HAppend___mk) [≍]](#manual-HEq___refl)
mk toArray✝¹ size_toArray✝¹ [++]](#manual-HAppend___mk) mk toArray✝ size_toArray✝ [++]](#manual-HAppend___mk) zs; [cases]](#manual-cases) zsmk.mk.mkα:Type ul₁:[Nat](https://lean-lang.org/doc/reference/latest/Basic-Types/Natural-Numbers/#Nat___zero)l₂:[Nat](https://lean-lang.org/doc/reference/latest/Basic-Types/Natural-Numbers/#Nat___zero)l₃:[Nat](https://lean-lang.org/doc/reference/latest/Basic-Types/Natural-Numbers/#Nat___zero)toArray✝²:[Array](https://lean-lang.org/doc/reference/latest/Basic-Types/Arrays/#Array___mk) αsize_toArray✝²:toArray✝.[size](https://lean-lang.org/doc/reference/latest/Basic-Types/Arrays/#Array___size) [=]](#manual-Eq___refl) l₁toArray✝¹:[Array](https://lean-lang.org/doc/reference/latest/Basic-Types/Arrays/#Array___mk) αsize_toArray✝¹:toArray✝.[size](https://lean-lang.org/doc/reference/latest/Basic-Types/Arrays/#Array___size) [=]](#manual-Eq___refl) l₂toArray✝:[Array](https://lean-lang.org/doc/reference/latest/Basic-Types/Arrays/#Array___mk) αsize_toArray✝:toArray✝.[size](https://lean-lang.org/doc/reference/latest/Basic-Types/Arrays/#Array___size) [=]](#manual-Eq___refl) l₃⊢ mk toArray✝² size_toArray✝² [++]](#manual-HAppend___mk) [(]](#manual-HAppend___mk)mk toArray✝¹ size_toArray✝¹ [++]](#manual-HAppend___mk) mk toArray✝ size_toArray✝[)]](#manual-HAppend___mk) [≍]](#manual-HEq___refl)
mk toArray✝² size_toArray✝² [++]](#manual-HAppend___mk) mk toArray✝¹ size_toArray✝¹ [++]](#manual-HAppend___mk) mk toArray✝ size_toArray✝
[simp]](#manual-simp)mk.mk.mkα:Type ul₁:[Nat](https://lean-lang.org/doc/reference/latest/Basic-Types/Natural-Numbers/#Nat___zero)l₂:[Nat](https://lean-lang.org/doc/reference/latest/Basic-Types/Natural-Numbers/#Nat___zero)l₃:[Nat](https://lean-lang.org/doc/reference/latest/Basic-Types/Natural-Numbers/#Nat___zero)toArray✝²:[Array](https://lean-lang.org/doc/reference/latest/Basic-Types/Arrays/#Array___mk) αsize_toArray✝²:toArray✝.[size](https://lean-lang.org/doc/reference/latest/Basic-Types/Arrays/#Array___size) [=]](#manual-Eq___refl) l₁toArray✝¹:[Array](https://lean-lang.org/doc/reference/latest/Basic-Types/Arrays/#Array___mk) αsize_toArray✝¹:toArray✝.[size](https://lean-lang.org/doc/reference/latest/Basic-Types/Arrays/#Array___size) [=]](#manual-Eq___refl) l₂toArray✝:[Array](https://lean-lang.org/doc/reference/latest/Basic-Types/Arrays/#Array___mk) αsize_toArray✝:toArray✝.[size](https://lean-lang.org/doc/reference/latest/Basic-Types/Arrays/#Array___size) [=]](#manual-Eq___refl) l₃⊢ mk [(]](#manual-HAppend___mk)toArray✝² [++]](#manual-HAppend___mk) [(]](#manual-HAppend___mk)toArray✝¹ [++]](#manual-HAppend___mk) toArray✝[)]](#manual-HAppend___mk)[)]](#manual-HAppend___mk) ⋯ [≍]](#manual-HEq___refl) mk [(]](#manual-HAppend___mk)toArray✝² [++]](#manual-HAppend___mk) [(]](#manual-HAppend___mk)toArray✝¹ [++]](#manual-HAppend___mk) toArray✝[)]](#manual-HAppend___mk)[)]](#manual-HAppend___mk) ⋯
[congr]](#manual-congr) 1mk.mk.mk.e_2α:Type ul₁:[Nat](https://lean-lang.org/doc/reference/latest/Basic-Types/Natural-Numbers/#Nat___zero)l₂:[Nat](https://lean-lang.org/doc/reference/latest/Basic-Types/Natural-Numbers/#Nat___zero)l₃:[Nat](https://lean-lang.org/doc/reference/latest/Basic-Types/Natural-Numbers/#Nat___zero)toArray✝²:[Array](https://lean-lang.org/doc/reference/latest/Basic-Types/Arrays/#Array___mk) αsize_toArray✝²:toArray✝.[size](https://lean-lang.org/doc/reference/latest/Basic-Types/Arrays/#Array___size) [=]](#manual-Eq___refl) l₁toArray✝¹:[Array](https://lean-lang.org/doc/reference/latest/Basic-Types/Arrays/#Array___mk) αsize_toArray✝¹:toArray✝.[size](https://lean-lang.org/doc/reference/latest/Basic-Types/Arrays/#Array___size) [=]](#manual-Eq___refl) l₂toArray✝:[Array](https://lean-lang.org/doc/reference/latest/Basic-Types/Arrays/#Array___mk) αsize_toArray✝:toArray✝.[size](https://lean-lang.org/doc/reference/latest/Basic-Types/Arrays/#Array___size) [=]](#manual-Eq___refl) l₃⊢ l₁ [+]](#manual-HAdd___mk) [(]](#manual-HAdd___mk)l₂ [+]](#manual-HAdd___mk) l₃[)]](#manual-HAdd___mk) [=]](#manual-Eq___refl) l₁ [+]](#manual-HAdd___mk) l₂ [+]](#manual-HAdd___mk) l₃mk.mk.mk.e_4α:Type ul₁:[Nat](https://lean-lang.org/doc/reference/latest/Basic-Types/Natural-Numbers/#Nat___zero)l₂:[Nat](https://lean-lang.org/doc/reference/latest/Basic-Types/Natural-Numbers/#Nat___zero)l₃:[Nat](https://lean-lang.org/doc/reference/latest/Basic-Types/Natural-Numbers/#Nat___zero)toArray✝²:[Array](https://lean-lang.org/doc/reference/latest/Basic-Types/Arrays/#Array___mk) αsize_toArray✝²:toArray✝.[size](https://lean-lang.org/doc/reference/latest/Basic-Types/Arrays/#Array___size) [=]](#manual-Eq___refl) l₁toArray✝¹:[Array](https://lean-lang.org/doc/reference/latest/Basic-Types/Arrays/#Array___mk) αsize_toArray✝¹:toArray✝.[size](https://lean-lang.org/doc/reference/latest/Basic-Types/Arrays/#Array___size) [=]](#manual-Eq___refl) l₂toArray✝:[Array](https://lean-lang.org/doc/reference/latest/Basic-Types/Arrays/#Array___mk) αsize_toArray✝:toArray✝.[size](https://lean-lang.org/doc/reference/latest/Basic-Types/Arrays/#Array___size) [=]](#manual-Eq___refl) l₃⊢ ⋯ [≍]](#manual-HEq___refl) ⋯
.mk.mk.mk.e_2α:Type ul₁:[Nat](https://lean-lang.org/doc/reference/latest/Basic-Types/Natural-Numbers/#Nat___zero)l₂:[Nat](https://lean-lang.org/doc/reference/latest/Basic-Types/Natural-Numbers/#Nat___zero)l₃:[Nat](https://lean-lang.org/doc/reference/latest/Basic-Types/Natural-Numbers/#Nat___zero)toArray✝²:[Array](https://lean-lang.org/doc/reference/latest/Basic-Types/Arrays/#Array___mk) αsize_toArray✝²:toArray✝.[size](https://lean-lang.org/doc/reference/latest/Basic-Types/Arrays/#Array___size) [=]](#manual-Eq___refl) l₁toArray✝¹:[Array](https://lean-lang.org/doc/reference/latest/Basic-Types/Arrays/#Array___mk) αsize_toArray✝¹:toArray✝.[size](https://lean-lang.org/doc/reference/latest/Basic-Types/Arrays/#Array___size) [=]](#manual-Eq___refl) l₂toArray✝:[Array](https://lean-lang.org/doc/reference/latest/Basic-Types/Arrays/#Array___mk) αsize_toArray✝:toArray✝.[size](https://lean-lang.org/doc/reference/latest/Basic-Types/Arrays/#Array___size) [=]](#manual-Eq___refl) l₃⊢ l₁ [+]](#manual-HAdd___mk) [(]](#manual-HAdd___mk)l₂ [+]](#manual-HAdd___mk) l₃[)]](#manual-HAdd___mk) [=]](#manual-Eq___refl) l₁ [+]](#manual-HAdd___mk) l₂ [+]](#manual-HAdd___mk) l₃ [omega]](#manual-omega)All goals completed! 🐙
.mk.mk.mk.e_4α:Type ul₁:[Nat](https://lean-lang.org/doc/reference/latest/Basic-Types/Natural-Numbers/#Nat___zero)l₂:[Nat](https://lean-lang.org/doc/reference/latest/Basic-Types/Natural-Numbers/#Nat___zero)l₃:[Nat](https://lean-lang.org/doc/reference/latest/Basic-Types/Natural-Numbers/#Nat___zero)toArray✝²:[Array](https://lean-lang.org/doc/reference/latest/Basic-Types/Arrays/#Array___mk) αsize_toArray✝²:toArray✝.[size](https://lean-lang.org/doc/reference/latest/Basic-Types/Arrays/#Array___size) [=]](#manual-Eq___refl) l₁toArray✝¹:[Array](https://lean-lang.org/doc/reference/latest/Basic-Types/Arrays/#Array___mk) αsize_toArray✝¹:toArray✝.[size](https://lean-lang.org/doc/reference/latest/Basic-Types/Arrays/#Array___size) [=]](#manual-Eq___refl) l₂toArray✝:[Array](https://lean-lang.org/doc/reference/latest/Basic-Types/Arrays/#Array___mk) αsize_toArray✝:toArray✝.[size](https://lean-lang.org/doc/reference/latest/Basic-Types/Arrays/#Array___size) [=]](#manual-Eq___refl) l₃⊢ ⋯ [≍]](#manual-HEq___refl) ⋯ [apply]](#manual-apply) [heq_of_eqRec_eq]](#manual-heq_of_eqRec_eq)mk.mk.mk.e_4.h₂α:Type ul₁:[Nat](https://lean-lang.org/doc/reference/latest/Basic-Types/Natural-Numbers/#Nat___zero)l₂:[Nat](https://lean-lang.org/doc/reference/latest/Basic-Types/Natural-Numbers/#Nat___zero)l₃:[Nat](https://lean-lang.org/doc/reference/latest/Basic-Types/Natural-Numbers/#Nat___zero)toArray✝²:[Array](https://lean-lang.org/doc/reference/latest/Basic-Types/Arrays/#Array___mk) αsize_toArray✝²:toArray✝.[size](https://lean-lang.org/doc/reference/latest/Basic-Types/Arrays/#Array___size) [=]](#manual-Eq___refl) l₁toArray✝¹:[Array](https://lean-lang.org/doc/reference/latest/Basic-Types/Arrays/#Array___mk) αsize_toArray✝¹:toArray✝.[size](https://lean-lang.org/doc/reference/latest/Basic-Types/Arrays/#Array___size) [=]](#manual-Eq___refl) l₂toArray✝:[Array](https://lean-lang.org/doc/reference/latest/Basic-Types/Arrays/#Array___mk) αsize_toArray✝:toArray✝.[size](https://lean-lang.org/doc/reference/latest/Basic-Types/Arrays/#Array___size) [=]](#manual-Eq___refl) l₃⊢ ⋯ [=]](#manual-Eq___refl) ⋯mk.mk.mk.e_4.h₁α:Type ul₁:[Nat](https://lean-lang.org/doc/reference/latest/Basic-Types/Natural-Numbers/#Nat___zero)l₂:[Nat](https://lean-lang.org/doc/reference/latest/Basic-Types/Natural-Numbers/#Nat___zero)l₃:[Nat](https://lean-lang.org/doc/reference/latest/Basic-Types/Natural-Numbers/#Nat___zero)toArray✝²:[Array](https://lean-lang.org/doc/reference/latest/Basic-Types/Arrays/#Array___mk) αsize_toArray✝²:toArray✝.[size](https://lean-lang.org/doc/reference/latest/Basic-Types/Arrays/#Array___size) [=]](#manual-Eq___refl) l₁toArray✝¹:[Array](https://lean-lang.org/doc/reference/latest/Basic-Types/Arrays/#Array___mk) αsize_toArray✝¹:toArray✝.[size](https://lean-lang.org/doc/reference/latest/Basic-Types/Arrays/#Array___size) [=]](#manual-Eq___refl) l₂toArray✝:[Array](https://lean-lang.org/doc/reference/latest/Basic-Types/Arrays/#Array___mk) αsize_toArray✝:toArray✝.[size](https://lean-lang.org/doc/reference/latest/Basic-Types/Arrays/#Array___size) [=]](#manual-Eq___refl) l₃⊢ [(]](#manual-Eq___refl)[(]](#manual-HAppend___mk)toArray✝² [++]](#manual-HAppend___mk) [(]](#manual-HAppend___mk)toArray✝¹ [++]](#manual-HAppend___mk) toArray✝[)]](#manual-HAppend___mk)[)]](#manual-HAppend___mk).[size](https://lean-lang.org/doc/reference/latest/Basic-Types/Arrays/#Array___size) [=]](#manual-Eq___refl) l₁ [+]](#manual-HAdd___mk) [(]](#manual-HAdd___mk)l₂ [+]](#manual-HAdd___mk) l₃[)]](#manual-HAdd___mk)[)]](#manual-Eq___refl) [=]](#manual-Eq___refl)
[(]](#manual-Eq___refl)[(]](#manual-HAppend___mk)toArray✝² [++]](#manual-HAppend___mk) [(]](#manual-HAppend___mk)toArray✝¹ [++]](#manual-HAppend___mk) toArray✝[)]](#manual-HAppend___mk)[)]](#manual-HAppend___mk).[size](https://lean-lang.org/doc/reference/latest/Basic-Types/Arrays/#Array___size) [=]](#manual-Eq___refl) l₁ [+]](#manual-HAdd___mk) l₂ [+]](#manual-HAdd___mk) l₃[)]](#manual-Eq___refl)
.mk.mk.mk.e_4.h₂α:Type ul₁:[Nat](https://lean-lang.org/doc/reference/latest/Basic-Types/Natural-Numbers/#Nat___zero)l₂:[Nat](https://lean-lang.org/doc/reference/latest/Basic-Types/Natural-Numbers/#Nat___zero)l₃:[Nat](https://lean-lang.org/doc/reference/latest/Basic-Types/Natural-Numbers/#Nat___zero)toArray✝²:[Array](https://lean-lang.org/doc/reference/latest/Basic-Types/Arrays/#Array___mk) αsize_toArray✝²:toArray✝.[size](https://lean-lang.org/doc/reference/latest/Basic-Types/Arrays/#Array___size) [=]](#manual-Eq___refl) l₁toArray✝¹:[Array](https://lean-lang.org/doc/reference/latest/Basic-Types/Arrays/#Array___mk) αsize_toArray✝¹:toArray✝.[size](https://lean-lang.org/doc/reference/latest/Basic-Types/Arrays/#Array___size) [=]](#manual-Eq___refl) l₂toArray✝:[Array](https://lean-lang.org/doc/reference/latest/Basic-Types/Arrays/#Array___mk) αsize_toArray✝:toArray✝.[size](https://lean-lang.org/doc/reference/latest/Basic-Types/Arrays/#Array___size) [=]](#manual-Eq___refl) l₃⊢ ⋯ [=]](#manual-Eq___refl) ⋯ [rfl]](#manual-rfl)All goals completed! 🐙
.mk.mk.mk.e_4.h₁α:Type ul₁:[Nat](https://lean-lang.org/doc/reference/latest/Basic-Types/Natural-Numbers/#Nat___zero)l₂:[Nat](https://lean-lang.org/doc/reference/latest/Basic-Types/Natural-Numbers/#Nat___zero)l₃:[Nat](https://lean-lang.org/doc/reference/latest/Basic-Types/Natural-Numbers/#Nat___zero)toArray✝²:[Array](https://lean-lang.org/doc/reference/latest/Basic-Types/Arrays/#Array___mk) αsize_toArray✝²:toArray✝.[size](https://lean-lang.org/doc/reference/latest/Basic-Types/Arrays/#Array___size) [=]](#manual-Eq___refl) l₁toArray✝¹:[Array](https://lean-lang.org/doc/reference/latest/Basic-Types/Arrays/#Array___mk) αsize_toArray✝¹:toArray✝.[size](https://lean-lang.org/doc/reference/latest/Basic-Types/Arrays/#Array___size) [=]](#manual-Eq___refl) l₂toArray✝:[Array](https://lean-lang.org/doc/reference/latest/Basic-Types/Arrays/#Array___mk) αsize_toArray✝:toArray✝.[size](https://lean-lang.org/doc/reference/latest/Basic-Types/Arrays/#Array___size) [=]](#manual-Eq___refl) l₃⊢ [(]](#manual-Eq___refl)[(]](#manual-HAppend___mk)toArray✝² [++]](#manual-HAppend___mk) [(]](#manual-HAppend___mk)toArray✝¹ [++]](#manual-HAppend___mk) toArray✝[)]](#manual-HAppend___mk)[)]](#manual-HAppend___mk).[size](https://lean-lang.org/doc/reference/latest/Basic-Types/Arrays/#Array___size) [=]](#manual-Eq___refl) l₁ [+]](#manual-HAdd___mk) [(]](#manual-HAdd___mk)l₂ [+]](#manual-HAdd___mk) l₃[)]](#manual-HAdd___mk)[)]](#manual-Eq___refl) [=]](#manual-Eq___refl)
[(]](#manual-Eq___refl)[(]](#manual-HAppend___mk)toArray✝² [++]](#manual-HAppend___mk) [(]](#manual-HAppend___mk)toArray✝¹ [++]](#manual-HAppend___mk) toArray✝[)]](#manual-HAppend___mk)[)]](#manual-HAppend___mk).[size](https://lean-lang.org/doc/reference/latest/Basic-Types/Arrays/#Array___size) [=]](#manual-Eq___refl) l₁ [+]](#manual-HAdd___mk) l₂ [+]](#manual-HAdd___mk) l₃[)]](#manual-Eq___refl) [apply]](#manual-apply) [propext]](#manual-propext)mk.mk.mk.e_4.h₁α:Type ul₁:[Nat](https://lean-lang.org/doc/reference/latest/Basic-Types/Natural-Numbers/#Nat___zero)l₂:[Nat](https://lean-lang.org/doc/reference/latest/Basic-Types/Natural-Numbers/#Nat___zero)l₃:[Nat](https://lean-lang.org/doc/reference/latest/Basic-Types/Natural-Numbers/#Nat___zero)toArray✝²:[Array](https://lean-lang.org/doc/reference/latest/Basic-Types/Arrays/#Array___mk) αsize_toArray✝²:toArray✝.[size](https://lean-lang.org/doc/reference/latest/Basic-Types/Arrays/#Array___size) [=]](#manual-Eq___refl) l₁toArray✝¹:[Array](https://lean-lang.org/doc/reference/latest/Basic-Types/Arrays/#Array___mk) αsize_toArray✝¹:toArray✝.[size](https://lean-lang.org/doc/reference/latest/Basic-Types/Arrays/#Array___size) [=]](#manual-Eq___refl) l₂toArray✝:[Array](https://lean-lang.org/doc/reference/latest/Basic-Types/Arrays/#Array___mk) αsize_toArray✝:toArray✝.[size](https://lean-lang.org/doc/reference/latest/Basic-Types/Arrays/#Array___size) [=]](#manual-Eq___refl) l₃⊢ [(]](#manual-HAppend___mk)toArray✝² [++]](#manual-HAppend___mk) [(]](#manual-HAppend___mk)toArray✝¹ [++]](#manual-HAppend___mk) toArray✝[)]](#manual-HAppend___mk)[)]](#manual-HAppend___mk).[size](https://lean-lang.org/doc/reference/latest/Basic-Types/Arrays/#Array___size) [=]](#manual-Eq___refl) l₁ [+]](#manual-HAdd___mk) [(]](#manual-HAdd___mk)l₂ [+]](#manual-HAdd___mk) l₃[)]](#manual-HAdd___mk) [↔]](#manual-Iff___intro)
[(]](#manual-HAppend___mk)toArray✝² [++]](#manual-HAppend___mk) [(]](#manual-HAppend___mk)toArray✝¹ [++]](#manual-HAppend___mk) toArray✝[)]](#manual-HAppend___mk)[)]](#manual-HAppend___mk).[size](https://lean-lang.org/doc/reference/latest/Basic-Types/Arrays/#Array___size) [=]](#manual-Eq___refl) l₁ [+]](#manual-HAdd___mk) l₂ [+]](#manual-HAdd___mk) l₃
[constructor]](#manual-constructor)mk.mk.mk.e_4.h₁.mpα:Type ul₁:[Nat](https://lean-lang.org/doc/reference/latest/Basic-Types/Natural-Numbers/#Nat___zero)l₂:[Nat](https://lean-lang.org/doc/reference/latest/Basic-Types/Natural-Numbers/#Nat___zero)l₃:[Nat](https://lean-lang.org/doc/reference/latest/Basic-Types/Natural-Numbers/#Nat___zero)toArray✝²:[Array](https://lean-lang.org/doc/reference/latest/Basic-Types/Arrays/#Array___mk) αsize_toArray✝²:toArray✝.[size](https://lean-lang.org/doc/reference/latest/Basic-Types/Arrays/#Array___size) [=]](#manual-Eq___refl) l₁toArray✝¹:[Array](https://lean-lang.org/doc/reference/latest/Basic-Types/Arrays/#Array___mk) αsize_toArray✝¹:toArray✝.[size](https://lean-lang.org/doc/reference/latest/Basic-Types/Arrays/#Array___size) [=]](#manual-Eq___refl) l₂toArray✝:[Array](https://lean-lang.org/doc/reference/latest/Basic-Types/Arrays/#Array___mk) αsize_toArray✝:toArray✝.[size](https://lean-lang.org/doc/reference/latest/Basic-Types/Arrays/#Array___size) [=]](#manual-Eq___refl) l₃⊢ [(]](#manual-HAppend___mk)toArray✝² [++]](#manual-HAppend___mk) [(]](#manual-HAppend___mk)toArray✝¹ [++]](#manual-HAppend___mk) toArray✝[)]](#manual-HAppend___mk)[)]](#manual-HAppend___mk).[size](https://lean-lang.org/doc/reference/latest/Basic-Types/Arrays/#Array___size) [=]](#manual-Eq___refl) l₁ [+]](#manual-HAdd___mk) [(]](#manual-HAdd___mk)l₂ [+]](#manual-HAdd___mk) l₃[)]](#manual-HAdd___mk) →
[(]](#manual-HAppend___mk)toArray✝² [++]](#manual-HAppend___mk) [(]](#manual-HAppend___mk)toArray✝¹ [++]](#manual-HAppend___mk) toArray✝[)]](#manual-HAppend___mk)[)]](#manual-HAppend___mk).[size](https://lean-lang.org/doc/reference/latest/Basic-Types/Arrays/#Array___size) [=]](#manual-Eq___refl) l₁ [+]](#manual-HAdd___mk) l₂ [+]](#manual-HAdd___mk) l₃mk.mk.mk.e_4.h₁.mprα:Type ul₁:[Nat](https://lean-lang.org/doc/reference/latest/Basic-Types/Natural-Numbers/#Nat___zero)l₂:[Nat](https://lean-lang.org/doc/reference/latest/Basic-Types/Natural-Numbers/#Nat___zero)l₃:[Nat](https://lean-lang.org/doc/reference/latest/Basic-Types/Natural-Numbers/#Nat___zero)toArray✝²:[Array](https://lean-lang.org/doc/reference/latest/Basic-Types/Arrays/#Array___mk) αsize_toArray✝²:toArray✝.[size](https://lean-lang.org/doc/reference/latest/Basic-Types/Arrays/#Array___size) [=]](#manual-Eq___refl) l₁toArray✝¹:[Array](https://lean-lang.org/doc/reference/latest/Basic-Types/Arrays/#Array___mk) αsize_toArray✝¹:toArray✝.[size](https://lean-lang.org/doc/reference/latest/Basic-Types/Arrays/#Array___size) [=]](#manual-Eq___refl) l₂toArray✝:[Array](https://lean-lang.org/doc/reference/latest/Basic-Types/Arrays/#Array___mk) αsize_toArray✝:toArray✝.[size](https://lean-lang.org/doc/reference/latest/Basic-Types/Arrays/#Array___size) [=]](#manual-Eq___refl) l₃⊢ [(]](#manual-HAppend___mk)toArray✝² [++]](#manual-HAppend___mk) [(]](#manual-HAppend___mk)toArray✝¹ [++]](#manual-HAppend___mk) toArray✝[)]](#manual-HAppend___mk)[)]](#manual-HAppend___mk).[size](https://lean-lang.org/doc/reference/latest/Basic-Types/Arrays/#Array___size) [=]](#manual-Eq___refl) l₁ [+]](#manual-HAdd___mk) l₂ [+]](#manual-HAdd___mk) l₃ →
[(]](#manual-HAppend___mk)toArray✝² [++]](#manual-HAppend___mk) [(]](#manual-HAppend___mk)toArray✝¹ [++]](#manual-HAppend___mk) toArray✝[)]](#manual-HAppend___mk)[)]](#manual-HAppend___mk).[size](https://lean-lang.org/doc/reference/latest/Basic-Types/Arrays/#Array___size) [=]](#manual-Eq___refl) l₁ [+]](#manual-HAdd___mk) [(]](#manual-HAdd___mk)l₂ [+]](#manual-HAdd___mk) l₃[)]](#manual-HAdd___mk) <;>mk.mk.mk.e_4.h₁.mpα:Type ul₁:[Nat](https://lean-lang.org/doc/reference/latest/Basic-Types/Natural-Numbers/#Nat___zero)l₂:[Nat](https://lean-lang.org/doc/reference/latest/Basic-Types/Natural-Numbers/#Nat___zero)l₃:[Nat](https://lean-lang.org/doc/reference/latest/Basic-Types/Natural-Numbers/#Nat___zero)toArray✝²:[Array](https://lean-lang.org/doc/reference/latest/Basic-Types/Arrays/#Array___mk) αsize_toArray✝²:toArray✝.[size](https://lean-lang.org/doc/reference/latest/Basic-Types/Arrays/#Array___size) [=]](#manual-Eq___refl) l₁toArray✝¹:[Array](https://lean-lang.org/doc/reference/latest/Basic-Types/Arrays/#Array___mk) αsize_toArray✝¹:toArray✝.[size](https://lean-lang.org/doc/reference/latest/Basic-Types/Arrays/#Array___size) [=]](#manual-Eq___refl) l₂toArray✝:[Array](https://lean-lang.org/doc/reference/latest/Basic-Types/Arrays/#Array___mk) αsize_toArray✝:toArray✝.[size](https://lean-lang.org/doc/reference/latest/Basic-Types/Arrays/#Array___size) [=]](#manual-Eq___refl) l₃⊢ [(]](#manual-HAppend___mk)toArray✝² [++]](#manual-HAppend___mk) [(]](#manual-HAppend___mk)toArray✝¹ [++]](#manual-HAppend___mk) toArray✝[)]](#manual-HAppend___mk)[)]](#manual-HAppend___mk).[size](https://lean-lang.org/doc/reference/latest/Basic-Types/Arrays/#Array___size) [=]](#manual-Eq___refl) l₁ [+]](#manual-HAdd___mk) [(]](#manual-HAdd___mk)l₂ [+]](#manual-HAdd___mk) l₃[)]](#manual-HAdd___mk) →
[(]](#manual-HAppend___mk)toArray✝² [++]](#manual-HAppend___mk) [(]](#manual-HAppend___mk)toArray✝¹ [++]](#manual-HAppend___mk) toArray✝[)]](#manual-HAppend___mk)[)]](#manual-HAppend___mk).[size](https://lean-lang.org/doc/reference/latest/Basic-Types/Arrays/#Array___size) [=]](#manual-Eq___refl) l₁ [+]](#manual-HAdd___mk) l₂ [+]](#manual-HAdd___mk) l₃mk.mk.mk.e_4.h₁.mprα:Type ul₁:[Nat](https://lean-lang.org/doc/reference/latest/Basic-Types/Natural-Numbers/#Nat___zero)l₂:[Nat](https://lean-lang.org/doc/reference/latest/Basic-Types/Natural-Numbers/#Nat___zero)l₃:[Nat](https://lean-lang.org/doc/reference/latest/Basic-Types/Natural-Numbers/#Nat___zero)toArray✝²:[Array](https://lean-lang.org/doc/reference/latest/Basic-Types/Arrays/#Array___mk) αsize_toArray✝²:toArray✝.[size](https://lean-lang.org/doc/reference/latest/Basic-Types/Arrays/#Array___size) [=]](#manual-Eq___refl) l₁toArray✝¹:[Array](https://lean-lang.org/doc/reference/latest/Basic-Types/Arrays/#Array___mk) αsize_toArray✝¹:toArray✝.[size](https://lean-lang.org/doc/reference/latest/Basic-Types/Arrays/#Array___size) [=]](#manual-Eq___refl) l₂toArray✝:[Array](https://lean-lang.org/doc/reference/latest/Basic-Types/Arrays/#Array___mk) αsize_toArray✝:toArray✝.[size](https://lean-lang.org/doc/reference/latest/Basic-Types/Arrays/#Array___size) [=]](#manual-Eq___refl) l₃⊢ [(]](#manual-HAppend___mk)toArray✝² [++]](#manual-HAppend___mk) [(]](#manual-HAppend___mk)toArray✝¹ [++]](#manual-HAppend___mk) toArray✝[)]](#manual-HAppend___mk)[)]](#manual-HAppend___mk).[size](https://lean-lang.org/doc/reference/latest/Basic-Types/Arrays/#Array___size) [=]](#manual-Eq___refl) l₁ [+]](#manual-HAdd___mk) l₂ [+]](#manual-HAdd___mk) l₃ →
[(]](#manual-HAppend___mk)toArray✝² [++]](#manual-HAppend___mk) [(]](#manual-HAppend___mk)toArray✝¹ [++]](#manual-HAppend___mk) toArray✝[)]](#manual-HAppend___mk)[)]](#manual-HAppend___mk).[size](https://lean-lang.org/doc/reference/latest/Basic-Types/Arrays/#Array___size) [=]](#manual-Eq___refl) l₁ [+]](#manual-HAdd___mk) [(]](#manual-HAdd___mk)l₂ [+]](#manual-HAdd___mk) l₃[)]](#manual-HAdd___mk) [intro]](#manual-intro) hmk.mk.mk.e_4.h₁.mprα:Type ul₁:[Nat](https://lean-lang.org/doc/reference/latest/Basic-Types/Natural-Numbers/#Nat___zero)l₂:[Nat](https://lean-lang.org/doc/reference/latest/Basic-Types/Natural-Numbers/#Nat___zero)l₃:[Nat](https://lean-lang.org/doc/reference/latest/Basic-Types/Natural-Numbers/#Nat___zero)toArray✝²:[Array](https://lean-lang.org/doc/reference/latest/Basic-Types/Arrays/#Array___mk) αsize_toArray✝²:toArray✝.[size](https://lean-lang.org/doc/reference/latest/Basic-Types/Arrays/#Array___size) [=]](#manual-Eq___refl) l₁toArray✝¹:[Array](https://lean-lang.org/doc/reference/latest/Basic-Types/Arrays/#Array___mk) αsize_toArray✝¹:toArray✝.[size](https://lean-lang.org/doc/reference/latest/Basic-Types/Arrays/#Array___size) [=]](#manual-Eq___refl) l₂toArray✝:[Array](https://lean-lang.org/doc/reference/latest/Basic-Types/Arrays/#Array___mk) αsize_toArray✝:toArray✝.[size](https://lean-lang.org/doc/reference/latest/Basic-Types/Arrays/#Array___size) [=]](#manual-Eq___refl) l₃h:[(]](#manual-HAppend___mk)toArray✝² [++]](#manual-HAppend___mk) [(]](#manual-HAppend___mk)toArray✝¹ [++]](#manual-HAppend___mk) toArray✝[)]](#manual-HAppend___mk)[)]](#manual-HAppend___mk).[size](https://lean-lang.org/doc/reference/latest/Basic-Types/Arrays/#Array___size) [=]](#manual-Eq___refl) l₁ [+]](#manual-HAdd___mk) l₂ [+]](#manual-HAdd___mk) l₃⊢ [(]](#manual-HAppend___mk)toArray✝² [++]](#manual-HAppend___mk) [(]](#manual-HAppend___mk)toArray✝¹ [++]](#manual-HAppend___mk) toArray✝[)]](#manual-HAppend___mk)[)]](#manual-HAppend___mk).[size](https://lean-lang.org/doc/reference/latest/Basic-Types/Arrays/#Array___size) [=]](#manual-Eq___refl) l₁ [+]](#manual-HAdd___mk) [(]](#manual-HAdd___mk)l₂ [+]](#manual-HAdd___mk) l₃[)]](#manual-HAdd___mk) <;>mk.mk.mk.e_4.h₁.mpα:Type ul₁:[Nat](https://lean-lang.org/doc/reference/latest/Basic-Types/Natural-Numbers/#Nat___zero)l₂:[Nat](https://lean-lang.org/doc/reference/latest/Basic-Types/Natural-Numbers/#Nat___zero)l₃:[Nat](https://lean-lang.org/doc/reference/latest/Basic-Types/Natural-Numbers/#Nat___zero)toArray✝²:[Array](https://lean-lang.org/doc/reference/latest/Basic-Types/Arrays/#Array___mk) αsize_toArray✝²:toArray✝.[size](https://lean-lang.org/doc/reference/latest/Basic-Types/Arrays/#Array___size) [=]](#manual-Eq___refl) l₁toArray✝¹:[Array](https://lean-lang.org/doc/reference/latest/Basic-Types/Arrays/#Array___mk) αsize_toArray✝¹:toArray✝.[size](https://lean-lang.org/doc/reference/latest/Basic-Types/Arrays/#Array___size) [=]](#manual-Eq___refl) l₂toArray✝:[Array](https://lean-lang.org/doc/reference/latest/Basic-Types/Arrays/#Array___mk) αsize_toArray✝:toArray✝.[size](https://lean-lang.org/doc/reference/latest/Basic-Types/Arrays/#Array___size) [=]](#manual-Eq___refl) l₃h:[(]](#manual-HAppend___mk)toArray✝² [++]](#manual-HAppend___mk) [(]](#manual-HAppend___mk)toArray✝¹ [++]](#manual-HAppend___mk) toArray✝[)]](#manual-HAppend___mk)[)]](#manual-HAppend___mk).[size](https://lean-lang.org/doc/reference/latest/Basic-Types/Arrays/#Array___size) [=]](#manual-Eq___refl) l₁ [+]](#manual-HAdd___mk) [(]](#manual-HAdd___mk)l₂ [+]](#manual-HAdd___mk) l₃[)]](#manual-HAdd___mk)⊢ [(]](#manual-HAppend___mk)toArray✝² [++]](#manual-HAppend___mk) [(]](#manual-HAppend___mk)toArray✝¹ [++]](#manual-HAppend___mk) toArray✝[)]](#manual-HAppend___mk)[)]](#manual-HAppend___mk).[size](https://lean-lang.org/doc/reference/latest/Basic-Types/Arrays/#Array___size) [=]](#manual-Eq___refl) l₁ [+]](#manual-HAdd___mk) l₂ [+]](#manual-HAdd___mk) l₃mk.mk.mk.e_4.h₁.mprα:Type ul₁:[Nat](https://lean-lang.org/doc/reference/latest/Basic-Types/Natural-Numbers/#Nat___zero)l₂:[Nat](https://lean-lang.org/doc/reference/latest/Basic-Types/Natural-Numbers/#Nat___zero)l₃:[Nat](https://lean-lang.org/doc/reference/latest/Basic-Types/Natural-Numbers/#Nat___zero)toArray✝²:[Array](https://lean-lang.org/doc/reference/latest/Basic-Types/Arrays/#Array___mk) αsize_toArray✝²:toArray✝.[size](https://lean-lang.org/doc/reference/latest/Basic-Types/Arrays/#Array___size) [=]](#manual-Eq___refl) l₁toArray✝¹:[Array](https://lean-lang.org/doc/reference/latest/Basic-Types/Arrays/#Array___mk) αsize_toArray✝¹:toArray✝.[size](https://lean-lang.org/doc/reference/latest/Basic-Types/Arrays/#Array___size) [=]](#manual-Eq___refl) l₂toArray✝:[Array](https://lean-lang.org/doc/reference/latest/Basic-Types/Arrays/#Array___mk) αsize_toArray✝:toArray✝.[size](https://lean-lang.org/doc/reference/latest/Basic-Types/Arrays/#Array___size) [=]](#manual-Eq___refl) l₃h:[(]](#manual-HAppend___mk)toArray✝² [++]](#manual-HAppend___mk) [(]](#manual-HAppend___mk)toArray✝¹ [++]](#manual-HAppend___mk) toArray✝[)]](#manual-HAppend___mk)[)]](#manual-HAppend___mk).[size](https://lean-lang.org/doc/reference/latest/Basic-Types/Arrays/#Array___size) [=]](#manual-Eq___refl) l₁ [+]](#manual-HAdd___mk) l₂ [+]](#manual-HAdd___mk) l₃⊢ [(]](#manual-HAppend___mk)toArray✝² [++]](#manual-HAppend___mk) [(]](#manual-HAppend___mk)toArray✝¹ [++]](#manual-HAppend___mk) toArray✝[)]](#manual-HAppend___mk)[)]](#manual-HAppend___mk).[size](https://lean-lang.org/doc/reference/latest/Basic-Types/Arrays/#Array___size) [=]](#manual-Eq___refl) l₁ [+]](#manual-HAdd___mk) [(]](#manual-HAdd___mk)l₂ [+]](#manual-HAdd___mk) l₃[)]](#manual-HAdd___mk) [simp_all]](#manual-simp_all) +[arith]](#manual-Lean___Meta___Simp___Config___mk)All goals completed! 🐙
```

def

```lean
[HEq.elim.{u, v}]](#manual-HEq___elim) {α : Sort u} {a : α} {p : α → Sort v} {b : α}
  (h₁ : a [≍]](#manual-HEq___refl) b) (h₂ : p a) : p b



[HEq.elim.{u, v}]](#manual-HEq___elim) {α : Sort u} {a : α}
  {p : α → Sort v} {b : α} (h₁ : a [≍]](#manual-HEq___refl) b)
  (h₂ : p a) : p b
```

`[HEq.ndrec]](#manual-HEq___ndrec)` variant

def

```lean
[HEq.ndrec.{u1, u2}]](#manual-HEq___ndrec) {α : Sort u2} {a : α}
  {motive : {β : Sort u2} → β → Sort u1} (m : motive a) {β : Sort u2}
  {b : β} (h : a [≍]](#manual-HEq___refl) b) : motive b



[HEq.ndrec.{u1, u2}]](#manual-HEq___ndrec) {α : Sort u2} {a : α}
  {motive : {β : Sort u2} → β → Sort u1}
  (m : motive a) {β : Sort u2} {b : β}
  (h : a [≍]](#manual-HEq___refl) b) : motive b
```

Non-dependent recursor for `[HEq]](#manual-HEq___refl)`

def

```lean
[HEq.ndrecOn.{u1, u2}]](#manual-HEq___ndrecOn) {α : Sort u2} {a : α}
  {motive : {β : Sort u2} → β → Sort u1} {β : Sort u2} {b : β}
  (h : a [≍]](#manual-HEq___refl) b) (m : motive a) : motive b



[HEq.ndrecOn.{u1, u2}]](#manual-HEq___ndrecOn) {α : Sort u2} {a : α}
  {motive : {β : Sort u2} → β → Sort u1}
  {β : Sort u2} {b : β} (h : a [≍]](#manual-HEq___refl) b)
  (m : motive a) : motive b
```

`[HEq.ndrec]](#manual-HEq___ndrec)` variant

theorem

```lean
[HEq.subst.{u}]](#manual-HEq___subst) {α β : Sort u} {a : α} {b : β}
  {p : (T : Sort u) → T → Prop} (h₁ : a [≍]](#manual-HEq___refl) b) (h₂ : p α a) : p β b



[HEq.subst.{u}]](#manual-HEq___subst) {α β : Sort u} {a : α}
  {b : β} {p : (T : Sort u) → T → Prop}
  (h₁ : a [≍]](#manual-HEq___refl) b) (h₂ : p α a) : p β b
```

Substitution with heterogeneous equality.

theorem

```lean
[eq_of_heq.{u}]](#manual-eq_of_heq) {α : Sort u} {a a' : α} (h : a [≍]](#manual-HEq___refl) a') : a [=]](#manual-Eq___refl) a'



[eq_of_heq.{u}]](#manual-eq_of_heq) {α : Sort u} {a a' : α}
  (h : a [≍]](#manual-HEq___refl) a') : a [=]](#manual-Eq___refl) a'
```

If two heterogeneously equal terms have the same type, then they are propositionally equal.

theorem

```lean
[heq_of_eq.{u_1}]](#manual-heq_of_eq) {α✝ : Sort u_1} {a a' : α✝} (h : a [=]](#manual-Eq___refl) a') : a [≍]](#manual-HEq___refl) a'



[heq_of_eq.{u_1}]](#manual-heq_of_eq) {α✝ : Sort u_1}
  {a a' : α✝} (h : a [=]](#manual-Eq___refl) a') : a [≍]](#manual-HEq___refl) a'
```

Propositionally equal terms are also heterogeneously equal.

theorem

```lean
[heq_of_eqRec_eq.{u}]](#manual-heq_of_eqRec_eq) {α β : Sort u} {a : α} {b : β} (h₁ : α [=]](#manual-Eq___refl) β)
  (h₂ : h₁ ▸ a [=]](#manual-Eq___refl) b) : a [≍]](#manual-HEq___refl) b



[heq_of_eqRec_eq.{u}]](#manual-heq_of_eqRec_eq) {α β : Sort u} {a : α}
  {b : β} (h₁ : α [=]](#manual-Eq___refl) β) (h₂ : h₁ ▸ a [=]](#manual-Eq___refl) b) :
  a [≍]](#manual-HEq___refl) b
```

If casting a term with `Eq.rec` to another type makes it equal to some other term, then the two
terms are heterogeneously equal.

theorem

```lean
[eqRec_heq.{u, v}]](#manual-eqRec_heq) {α : Sort u} {φ : α → Sort v} {a a' : α} (h : a [=]](#manual-Eq___refl) a')
  (p : φ a) : Eq.recOn h p [≍]](#manual-HEq___refl) p



[eqRec_heq.{u, v}]](#manual-eqRec_heq) {α : Sort u}
  {φ : α → Sort v} {a a' : α} (h : a [=]](#manual-Eq___refl) a')
  (p : φ a) : Eq.recOn h p [≍]](#manual-HEq___refl) p
```

Rewriting inside `φ` using `Eq.recOn` yields a term that's heterogeneously equal to the original
term.

theorem

```lean
[cast_heq.{u}]](#manual-cast_heq) {α β : Sort u} (h : α [=]](#manual-Eq___refl) β) (a : α) : [cast]](#manual-cast) h a [≍]](#manual-HEq___refl) a



[cast_heq.{u}]](#manual-cast_heq) {α β : Sort u} (h : α [=]](#manual-Eq___refl) β)
  (a : α) : [cast]](#manual-cast) h a [≍]](#manual-HEq___refl) a
```

The result of casting a term with `[cast]](#manual-cast)` is heterogeneously equal to the original term.

theorem

```lean
[heq_of_heq_of_eq.{u}]](#manual-heq_of_heq_of_eq) {α β : Sort u} {a : α} {b b' : β} (h₁ : a [≍]](#manual-HEq___refl) b)
  (h₂ : b [=]](#manual-Eq___refl) b') : a [≍]](#manual-HEq___refl) b'



[heq_of_heq_of_eq.{u}]](#manual-heq_of_heq_of_eq) {α β : Sort u}
  {a : α} {b b' : β} (h₁ : a [≍]](#manual-HEq___refl) b)
  (h₂ : b [=]](#manual-Eq___refl) b') : a [≍]](#manual-HEq___refl) b'
```

Heterogeneous equality precomposes with propositional equality.

theorem

```lean
[type_eq_of_heq.{u}]](#manual-type_eq_of_heq) {α β : Sort u} {a : α} {b : β} (h : a [≍]](#manual-HEq___refl) b) : α [=]](#manual-Eq___refl) β



[type_eq_of_heq.{u}]](#manual-type_eq_of_heq) {α β : Sort u} {a : α}
  {b : β} (h : a [≍]](#manual-HEq___refl) b) : α [=]](#manual-Eq___refl) β
```

If two terms are heterogeneously equal then their types are propositionally equal.

---




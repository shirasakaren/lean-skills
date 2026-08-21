# Part VIII — The Hitchhiker's Guide to Logical Verification {#part-8}



## README / About {#hhg-readme-about}

> 📄 Source: https://raw.githubusercontent.com/lean-forward/logical_verification_2025/main/README.md

## Logical Verification 2025

Files associated with the Hitchhiker's Guide to Logical Verification (2025
edition).


### Installation

To edit the Lean files, open the `lean` folder as a Lean 4 project [as described
here](https://leanprover-community.github.io/install/project.html#working-on-an-existing-project).

---



## Types and Terms (Demo) {#hhg-types-and-terms-demo}

> 📄 Source: https://raw.githubusercontent.com/lean-forward/logical_verification_2025/main/lean/LoVe/LoVe01_TypesAndTerms_Demo.lean

```lean
import LoVe.LoVelib
```


## LoVe Preface

### Proof Assistants

Proof assistants (also called interactive theorem provers)

* check and help develop formal proofs;
* can be used to prove big theorems, not only logic puzzles;
* can be tedious to use;
* are highly addictive (think video games).

A selection of proof assistants, classified by logical foundations:

* set theory: Isabelle/ZF, Metamath, Mizar;
* simple type theory: HOL4, HOL Light, Isabelle/HOL, PVS;
* **dependent type theory**: Agda, **Lean**, Matita, Rocq.


### Success Stories

Mathematics:

* the four-color theorem;
* the Kepler conjecture;
* the definition of perfectoid spaces.

Computer science:

* hardware;
* operating systems;
* programming language theory;
* compilers;
* security.


### Lean

Lean is a proof assistant developed primarily by Leonardo de Moura (Amazon Web
Services) since 2012.

Its mathematical library, `mathlib`, is developed by a large community of
contributors.

We use the community version of Lean 4. We use its basic libraries, `mathlib4`,
and `LoVelib`, among others. Lean is a research project.

Strengths:

* highly expressive logic based on a dependent type theory called the
  **calculus of inductive constructions**;
* extended with classical axioms and quotient types;
* metaprogramming framework;
* modern user interface;
* documentation;
* open source;
* endless source of puns (Lean Forward, Lean Together, Boolean, …).


### Our Goal

We want you to

* master fundamental theory and techniques in interactive theorem proving;
* get familiarized with some application areas;
* develop some practical skills you can apply on a larger project (as a hobby,
  for an MSc or PhD, or in industry);
* feel ready to move to another proof assistant and apply what you have learned;
* understand the domain well enough to start reading scientific papers.

This course is neither a pure logical foundations course nor a Lean tutorial.
Lean is our means, not an end of itself.


## LoVe Demo 1: Types and Terms

We start our journey by studying the basics of Lean, starting with terms
(expressions) and their types.


```lean
set_option autoImplicit false
set_option tactic.hygienic false

namespace LoVe
```


### A View of Lean

In a first approximation:

    Lean = functional programming + logic

In today's lecture, we cover the syntax of types and terms, which are similar to
those of the simply typed λ-calculus or typed functional programming languages
(ML, OCaml, Haskell).


### Types

Types `σ`, `τ`, `υ`:

* type variables `α`;
* basic types `T`;
* complex types `T σ1 … σN`.

Some type constructors `T` are written infix, e.g., `→` (function type).

The function arrow is right-associative:
`σ₁ → σ₂ → σ₃ → τ` = `σ₁ → (σ₂ → (σ₃ → τ))`.

Polymorphic types are also possible. In Lean, the type variables must be bound
using `∀`, e.g., `∀α, α → α`.


### Terms

Terms `t`, `u`:

* constants `c`;
* variables `x`;
* applications `t u`;
* anonymous functions `fun x ↦ t` (also called λ-expressions).

__Currying__: functions can be

* fully applied (e.g., `f x y z` if `f` can take at most 3 arguments);
* partially applied (e.g., `f x y`, `f x`);
* left unapplied (e.g., `f`).

Application is left-associative: `f x y z` = `((f x) y) z`.

`#check` reports the type of its argument.


```lean
#check ℕ
#check ℤ

#check Empty
#check Unit
#check Bool

#check ℕ → ℤ
#check ℤ → ℕ
#check Bool → ℕ → ℤ
#check (Bool → ℕ) → ℤ
#check ℕ → (Bool → ℕ) → ℤ

#check fun x : ℕ ↦ x
#check fun f : ℕ → ℕ ↦ fun g : ℕ → ℕ ↦ fun h : ℕ → ℕ ↦
  fun x : ℕ ↦ h (g (f x))
#check fun (f g h : ℕ → ℕ) (x : ℕ) ↦ h (g (f x))
```


`opaque` defines an arbitrary constant of the specified type.


```lean
opaque a : ℤ
opaque b : ℤ
opaque f : ℤ → ℤ
opaque g : ℤ → ℤ → ℤ

#check fun x : ℤ ↦ g (f (g a x)) (g x b)
#check fun x ↦ g (f (g a x)) (g x b)

#check fun x ↦ x
```


### Type Checking and Type Inference

Type checking and type inference are decidable problems (although this property is
quickly lost if features such as overloading or subtyping are added).

Type judgment: `C ⊢ t : σ`, meaning `t` has type `σ` in local context `C`.

Typing rules:

    —————————— Cst   if c is globally declared with type σ
    C ⊢ c : σ

    —————————— Var   if x : σ is the rightmost occurrence of x in C
    C ⊢ x : σ

    C ⊢ t : σ → τ    C ⊢ u : σ
    ——————————————————————————— App
    C ⊢ t u : τ

    C, x : σ ⊢ t : τ
    ——————————————————————————— Fun
    C ⊢ (fun x : σ ↦ t) : σ → τ

If the same variable `x` occurs multiple times in the context C, the rightmost
occurrence shadows the other ones.


### Type Inhabitation

Given a type `σ`, the __type inhabitation__ problem consists of finding a term
of that type. Type inhabitation is undecidable.

Recursive procedure:

1. If `σ` is of the form `τ → υ`, a candidate inhabitant is an anonymous
   function of the form `fun x ↦ _`.

2. Alternatively, you can use any constant or variable `x : τ₁ → ⋯ → τN → σ` to
   build the term `x _ … _`.


```lean
opaque α : Type
opaque β : Type
opaque γ : Type

def someFunOfType : (α → β → γ) → ((β → α) → β) → α → γ :=
  fun f g a ↦ f a (g (fun b ↦ a))

end LoVe
```

---



## Types and Terms (ExerciseSheet) {#hhg-types-and-terms-exercisesheet}

> 📄 Source: https://raw.githubusercontent.com/lean-forward/logical_verification_2025/main/lean/LoVe/LoVe01_TypesAndTerms_ExerciseSheet.lean

```lean
import LoVe.LoVe01_TypesAndTerms_Demo
```


## LoVe Exercise 1: Types and Terms

Replace the placeholders (e.g., `:= sorry`) with your solutions.


```lean
set_option autoImplicit false
set_option tactic.hygienic false

namespace LoVe
```


### Question 1: Terms

Complete the following definitions, by replacing the `sorry` markers by terms
of the expected type.

Hint: A procedure for doing so systematically is described in Section 1.4 of
the Hitchhiker's Guide. As explained there, you can use `_` as a placeholder
while constructing a term. By hovering over `_`, you will see the current
logical context.


```lean
def I : α → α :=
  fun a ↦ a

def K : α → β → α :=
  fun a b ↦ a

def C : (α → β → γ) → β → α → γ :=
  sorry

def projFst : α → α → α :=
  sorry
```


Give a different answer than for `projFst`.


```lean
def projSnd : α → α → α :=
  sorry

def someNonsense : (α → β → γ) → α → (α → γ) → β → γ :=
  sorry
```


### Question 2: Typing Derivation

Show the typing derivation for your definition of `C` above, on paper or using
ASCII or Unicode art. Start with an empty context. You might find the
characters `–` (to draw horizontal bars) and `⊢` useful.


```lean
-- write your solution in a comment here or on paper

end LoVe
```

---



## Types and Terms (HomeworkSheet) {#hhg-types-and-terms-homeworksheet}

> 📄 Source: https://raw.githubusercontent.com/lean-forward/logical_verification_2025/main/lean/LoVe/LoVe01_TypesAndTerms_HomeworkSheet.lean

```lean
import LoVe.LoVelib
```


## LoVe Homework 1 (10 points): Types and Terms

Replace the placeholders (e.g., `:= sorry`) with your solutions.


```lean
set_option autoImplicit false
set_option tactic.hygienic false

namespace LoVe
```


### Question 1 (6 points): Terms

We start by declaring four new opaque types.


```lean
opaque α : Type
opaque β : Type
opaque γ : Type
opaque δ : Type
```


1.1 (4 points). Complete the following definitions, by providing terms with
the expected type.

Please use reasonable names for the bound variables, e.g., `a : α`, `b : β`,
`c : γ`.

Hint: A procedure for doing so systematically is described in Section 1.4 of the
Hitchhiker's Guide. As explained there, you can use `_` as a placeholder while
constructing a term. By hovering over `_`, you will see the current logical
context.


```lean
def B : (α → β) → (γ → α) → γ → β :=
  sorry

def S : (α → β → γ) → (α → β) → α → γ :=
  sorry

def moreNonsense : ((α → β) → γ → δ) → γ → β → δ :=
  sorry

def evenMoreNonsense : (α → β) → (α → γ) → α → β → γ :=
  sorry
```


1.2 (2 points). Complete the following definition.

This one looks more difficult, but it should be fairly straightforward if you
follow the procedure described in the Hitchhiker's Guide.

Note: Peirce is pronounced like the English word "purse".


```lean
def weakPeirce : ((((α → β) → α) → α) → β) → β :=
  sorry
```


### Question 2 (4 points): Typing Derivation

Show the typing derivation for your definition of `B` above, using ASCII or
Unicode art. Start with an empty context. You might find the characters `–` (to
draw horizontal bars) and `⊢` useful.

Feel free to introduce abbreviations to avoid repeating large contexts `C`.


```lean
-- write your solution here

end LoVe
```

---



## Programs and Theorems (Demo) {#hhg-programs-and-theorems-demo}

> 📄 Source: https://raw.githubusercontent.com/lean-forward/logical_verification_2025/main/lean/LoVe/LoVe02_ProgramsAndTheorems_Demo.lean

```lean
import LoVe.LoVelib
```


## LoVe Demo 2: Programs and Theorems

We continue our study of the basics of Lean, focusing on programs and theorems,
without carrying out any proofs yet. We review how to define new types and
functions and how to state their intended properties as theorems.


```lean
set_option autoImplicit false
set_option tactic.hygienic false

namespace LoVe
```


### Type Definitions

An __inductive type__ (also called __inductive datatype__,
__algebraic datatype__, or just __datatype__) is a type that consists all the
values that can be built using a finite number of applications of its
__constructors__, and only those.


#### Natural Numbers


```lean
namespace MyNat
```


Definition of type `Nat` (= `ℕ`) of natural numbers, using unary notation:


```lean
inductive Nat : Type where
  | zero : Nat
  | succ : Nat → Nat

#check Nat
#check Nat.zero
#check Nat.succ
```


`#print` outputs the definition of its argument.


```lean
#print Nat

end MyNat
```


Outside namespace `MyNat`, `Nat` refers to the type defined in the Lean core
library unless it is qualified by the `MyNat` namespace.


```lean
#print Nat
#print MyNat.Nat
```


#### Arithmetic Expressions


```lean
inductive AExp : Type where
  | num : ℤ → AExp
  | var : String → AExp
  | add : AExp → AExp → AExp
  | sub : AExp → AExp → AExp
  | mul : AExp → AExp → AExp
  | div : AExp → AExp → AExp
```


#### Lists


```lean
namespace MyList

inductive List (α : Type) where
  | nil  : List α
  | cons : α → List α → List α

#check List
#check List.nil
#check List.cons
#print List

end MyList

#print List
#print MyList.List
```


### Function Definitions

The syntax for defining a function operating on an inductive type is very
compact: We define a single function and use __pattern matching__ to extract the
arguments to the constructors.


```lean
def fib : ℕ → ℕ
  | 0     => 0
  | 1     => 1
  | n + 2 => fib (n + 1) + fib n
```


When there are multiple arguments, separate the patterns by `,`:


```lean
def add : ℕ → ℕ → ℕ
  | m, Nat.zero   => m
  | m, Nat.succ n => Nat.succ (add m n)
```


`#eval` and `#reduce` evaluate and output the value of a term.


```lean
#eval add 2 7
#reduce add 2 7

def mul : ℕ → ℕ → ℕ
  | _, Nat.zero   => Nat.zero
  | m, Nat.succ n => add m (mul m n)

#eval mul 2 7

#print mul

def power : ℕ → ℕ → ℕ
  | _, Nat.zero   => 1
  | m, Nat.succ n => mul m (power m n)

#eval power 2 5
```


`add`, `mul`, and `power` are artificial examples. These operations are
already available in Lean as `+`, `*`, and `^`.

If it is not necessary to pattern-match on an argument, it can be moved to
the left of the `:` and made a named argument:


```lean
def powerParam (m : ℕ) : ℕ → ℕ
  | Nat.zero   => 1
  | Nat.succ n => mul m (powerParam m n)

#eval powerParam 2 5

def iter (α : Type) (z : α) (f : α → α) : ℕ → α
  | Nat.zero   => z
  | Nat.succ n => f (iter α z f n)

#check iter

def powerIter (m n : ℕ) : ℕ :=
  iter ℕ 1 (mul m) n

#eval powerIter 2 5

def append (α : Type) : List α → List α → List α
  | List.nil,       ys => ys
  | List.cons x xs, ys => List.cons x (append α xs ys)
```


Because `append` must work for any type of list, the type of its elements is
provided as an argument. As a result, the type must be provided in every call
(or use `_` if Lean can infer the type).


```lean
#check append
#eval append ℕ [3, 1] [4, 1, 5]
#eval append _ [3, 1] [4, 1, 5]
```


If the type argument is enclosed in `{ }` rather than `( )`, it is implicit
and need not be provided in every call (provided Lean can infer it).


```lean
def appendImplicit {α : Type} : List α → List α → List α
  | List.nil,       ys => ys
  | List.cons x xs, ys => List.cons x (appendImplicit xs ys)

#eval appendImplicit [3, 1] [4, 1, 5]
```


Prefixing a definition name with `@` gives the corresponding definition in
which all implicit arguments have been made explicit. This is useful in
situations where Lean cannot work out how to instantiate the implicit
arguments.


```lean
#check @appendImplicit
#eval @appendImplicit ℕ [3, 1] [4, 1, 5]
#eval @appendImplicit _ [3, 1] [4, 1, 5]
```


Aliases:

   `[]`          := `List.nil`
   `x :: xs`     := `List.cons x xs`
   `[x₁, …, xN]` := `x₁ :: … :: xN :: []`


```lean
def appendPretty {α : Type} : List α → List α → List α
  | [],      ys => ys
  | x :: xs, ys => x :: appendPretty xs ys

def reverse {α : Type} : List α → List α
  | []      => []
  | x :: xs => reverse xs ++ [x]

def eval (env : String → ℤ) : AExp → ℤ
  | AExp.num i     => i
  | AExp.var x     => env x
  | AExp.add e₁ e₂ => eval env e₁ + eval env e₂
  | AExp.sub e₁ e₂ => eval env e₁ - eval env e₂
  | AExp.mul e₁ e₂ => eval env e₁ * eval env e₂
  | AExp.div e₁ e₂ => eval env e₁ / eval env e₂

#eval eval (fun x ↦ 7) (AExp.div (AExp.var "y") (AExp.num 0))
```


Lean only accepts the function definitions for which it can prove
termination. In particular, it accepts __structurally recursive__ functions,
which peel off exactly one constructor at a time.


### Theorem Statements

Notice the similarity with `def` commands. `theorem` is like `def` except that
the result is a proposition rather than data or a function.


```lean
namespace SorryTheorems

theorem add_comm (m n : ℕ) :
    add m n = add n m :=
  sorry

theorem add_assoc (l m n : ℕ) :
    add (add l m) n = add l (add m n) :=
  sorry

theorem mul_comm (m n : ℕ) :
    mul m n = mul n m :=
  sorry

theorem mul_assoc (l m n : ℕ) :
    mul (mul l m) n = mul l (mul m n) :=
  sorry

theorem mul_add (l m n : ℕ) :
    mul l (add m n) = add (mul l m) (mul l n) :=
  sorry

theorem reverse_reverse {α : Type} (xs : List α) :
    reverse (reverse xs) = xs :=
  sorry
```


Axioms are like theorems but without proofs. Opaque declarations are like
definitions but without bodies.


```lean
opaque a : ℤ
opaque b : ℤ

axiom a_less_b :
    a < b

end SorryTheorems

end LoVe
```

---



## Programs and Theorems (ExerciseSheet) {#hhg-programs-and-theorems-exercisesheet}

> 📄 Source: https://raw.githubusercontent.com/lean-forward/logical_verification_2025/main/lean/LoVe/LoVe02_ProgramsAndTheorems_ExerciseSheet.lean

```lean
import LoVe.LoVe02_ProgramsAndTheorems_Demo
```


## LoVe Exercise 2: Programs and Theorems

Replace the placeholders (e.g., `:= sorry`) with your solutions.


```lean
set_option autoImplicit false
set_option tactic.hygienic false

namespace LoVe
```


### Question 1: Predecessor Function

1.1. Define the function `pred` of type `ℕ → ℕ` that returns the predecessor of
its argument, or 0 if the argument is 0. For example:

    `pred 7 = 6`
    `pred 0 = 0`


```lean
def pred : ℕ → ℕ :=
  sorry
```


1.2. Check that your function works as expected.


```lean
#eval pred 0    -- expected: 0
#eval pred 1    -- expected: 0
#eval pred 2    -- expected: 1
#eval pred 3    -- expected: 2
#eval pred 10   -- expected: 9
#eval pred 99   -- expected: 98
```


### Question 2: Arithmetic Expressions

Consider the type `AExp` from the lecture and the function `eval` that
computes the value of an expression. You will find the definitions in the file
`LoVe02_ProgramsAndTheorems_Demo.lean`. One way to find them quickly is to

1. hold the Control (on Linux and Windows) or Command (on macOS) key pressed;
2. move the cursor to the identifier `AExp` or `eval`;
3. click the identifier.


```lean
#check AExp
#check eval
```


2.1. Test that `eval` behaves as expected. Make sure to exercise each
constructor at least once. You can use the following environment in your tests.
What happens if you divide by zero?

Note that `#eval` (Lean's evaluation command) and `eval` (our evaluation
function on `AExp`) are unrelated.


```lean
def someEnv : String → ℤ
  | "x" => 3
  | "y" => 17
  | _   => 201

#eval eval someEnv (AExp.var "x")   -- expected: 3
-- invoke `#eval` here
```


2.2. The following function simplifies arithmetic expressions involving
addition. It simplifies `0 + e` and `e + 0` to `e`. Complete the definition so
that it also simplifies expressions involving the other three binary
operators.


```lean
def simplify : AExp → AExp
  | AExp.add (AExp.num 0) e₂ => simplify e₂
  | AExp.add e₁ (AExp.num 0) => simplify e₁
  -- insert the missing cases here
  -- catch-all cases below
  | AExp.num i               => AExp.num i
  | AExp.var x               => AExp.var x
  | AExp.add e₁ e₂           => AExp.add (simplify e₁) (simplify e₂)
  | AExp.sub e₁ e₂           => AExp.sub (simplify e₁) (simplify e₂)
  | AExp.mul e₁ e₂           => AExp.mul (simplify e₁) (simplify e₂)
  | AExp.div e₁ e₂           => AExp.div (simplify e₁) (simplify e₂)
```


2.3. Is the `simplify` function correct? In fact, what would it mean for it
to be correct or not? Intuitively, for `simplify` to be correct, it must
return an arithmetic expression that yields the same numeric value when
evaluated as the original expression.

Given an environment `env` and an expression `e`, state (without proving it)
the property that the value of `e` after simplification is the same as the
value of `e` before.


```lean
theorem simplify_correct (env : String → ℤ) (e : AExp) :
  True :=   -- replace `True` by your theorem statement
  sorry   -- leave `sorry` alone
```


### Question 3 (**optional**): Map

3.1 (**optional**). Define a generic `map` function that applies a function to
every element in a list.


```lean
def map {α : Type} {β : Type} (f : α → β) : List α → List β :=
  sorry

#eval map (fun n ↦ n + 10) [1, 2, 3]   -- expected: [11, 12, 13]
```


3.2 (**optional**). State (without proving them) the so-called functorial
properties of `map` as theorems. Schematically:

     map (fun x ↦ x) xs = xs
     map (fun x ↦ g (f x)) xs = map g (map f xs)

Try to give meaningful names to your theorems. Also, make sure to state the
second property as generally as possible, for arbitrary types.


```lean
-- enter your theorem statements here

end LoVe
```

---



## Programs and Theorems (HomeworkSheet) {#hhg-programs-and-theorems-homeworksheet}

> 📄 Source: https://raw.githubusercontent.com/lean-forward/logical_verification_2025/main/lean/LoVe/LoVe02_ProgramsAndTheorems_HomeworkSheet.lean

```lean
import LoVe.LoVe02_ProgramsAndTheorems_Demo
```


## LoVe Homework 2 (10 points): Programs and Theorems

Replace the placeholders (e.g., `:= sorry`) with your solutions.


```lean
set_option autoImplicit false
set_option tactic.hygienic false

namespace LoVe
```


### Question 1 (4 points): Snoc

1.1 (3 points). Define the function `snoc` that appends a single element to the
end of a list. Your function should be defined by recursion and not using `++`
(`List.append`).


```lean
def snoc {α : Type} : List α → α → List α :=
  sorry
```


1.2 (1 point). Convince yourself that your definition of `snoc` works by
testing it on a few examples.


```lean
#eval snoc [1] 2
-- invoke `#eval` or `#reduce` here
```


### Question 2 (6 points): Sum

2.1 (3 points). Define a `sum` function that computes the sum of all the numbers
in a list.


```lean
def sum : List ℕ → ℕ :=
  sorry

#eval sum [1, 12, 3]   -- expected: 16
```


2.2 (3 points). State (without proving them) the following properties of
`sum` as theorems. Schematically:

     sum (snoc ms n) = n + sum ms
     sum (ms ++ ns) = sum ms + sum ns
     sum (reverse ns) = sum ns

Try to give meaningful names to your theorems. Use `sorry` as the proof.


```lean
-- enter your theorem statements here

end LoVe
```

---



## Backward Proofs (Demo) {#hhg-backward-proofs-demo}

> 📄 Source: https://raw.githubusercontent.com/lean-forward/logical_verification_2025/main/lean/LoVe/LoVe03_BackwardProofs_Demo.lean

```lean
import LoVe.LoVe02_ProgramsAndTheorems_Demo
```


## LoVe Demo 3: Backward Proofs

A __tactic__ operates on a proof goal and either proves it or creates new
subgoals. Tactics are a __backward__ proof mechanism: They start from the goal
and work towards the available hypotheses and theorems.


```lean
set_option autoImplicit false
set_option tactic.hygienic false

namespace LoVe

namespace BackwardProofs
```


### Tactic Mode

Syntax of tactical proofs:

    by
      _tactic₁_
      …
      _tacticN_

The keyword `by` indicates to Lean the proof is tactical.


```lean
theorem fst_of_two_props :
    ∀a b : Prop, a → b → a :=
  by
    intro a b
    intro ha hb
    apply ha
```


Note that `a → b → a` is parsed as `a → (b → a)`.

Propositions in Lean are terms of type `Prop`. `Prop` is a type, just like `Nat`
and `List Bool`. In fact there is a close correspondence between propositions
and types, which will be explained in lecture 4.


### Basic Tactics

`intro` moves `∀`-quantified variables, or the assumptions of implications `→`,
from the goal's conclusion (after `⊢`) into the goal's hypotheses (before `⊢`).

`apply` matches the goal's conclusion with the conclusion of the specified
theorem and adds the theorem's hypotheses as new goals.


```lean
theorem fst_of_two_props_params (a b : Prop) (ha : a) (hb : b) :
    a :=
  by apply ha

theorem prop_comp (a b c : Prop) (hab : a → b) (hbc : b → c) :
    a → c :=
  by
    intro ha
    apply hbc
    apply hab
    apply ha
```


The above proof step by step:

* Assume we have a proof of `a`.
* The goal is `c`, which we can show if we prove `b` (from `hbc`).
* The goal is `b`, which we can show if we prove `a` (from `hab`).
* We already know `a` (from `ha`).

Next, `exact` matches the goal's conclusion with the specified theorem, closing
the goal. We can often use `apply` in such situations, but `exact` communicates
our intentions better.


```lean
theorem fst_of_two_props_exact (a b : Prop) (ha : a) (hb : b) :
    a :=
  by exact ha
```


`assumption` finds a hypothesis from the local context that matches the
goal's conclusion and applies it to prove the goal.


```lean
theorem fst_of_two_props_assumption (a b : Prop)
      (ha : a) (hb : b) :
    a :=
  by assumption
```


`rfl` proves `l = r`, where the two sides are syntactically equal up to
computation. Computation means unfolding of definitions, β-reduction
(application of `fun` to an argument), `let`, and more.


```lean
theorem α_example {α β : Type} (f : α → β) :
    (fun x ↦ f x) = (fun y ↦ f y) :=
  by rfl

theorem β_example {α β : Type} (f : α → β) (a : α) :
    (fun x ↦ f x) a = f a :=
  by rfl

def double (n : ℕ) : ℕ :=
  n + n

theorem δ_example :
    double 5 = 5 + 5 :=
  by rfl
```


`let` introduces a definition that is locally scoped. Below, `n := 2` is only
in scope in the expression `n + n`.


```lean
theorem ζ_example :
    (let n : ℕ := 2
     n + n) = 4 :=
  by rfl

theorem η_example {α β : Type} (f : α → β) :
    (fun x ↦ f x) = f :=
  by rfl
```


`(a, b)` is the pair whose first component is `a` and whose second component
is `b`. `Prod.fst` is a so-called projection that extracts the first component
of a pair.


```lean
theorem ι_example {α β : Type} (a : α) (b : β) :
    Prod.fst (a, b) = a :=
  by rfl
```


### Reasoning about Logical Connectives and Quantifiers

Introduction rules:


```lean
#check True.intro
#check And.intro
#check Or.inl
#check Or.inr
#check Iff.intro
#check Exists.intro
```


Elimination rules:


```lean
#check False.elim
#check And.left
#check And.right
#check Or.elim
#check Iff.mp
#check Iff.mpr
#check Exists.elim
```


Definition of `¬` and related theorems:


```lean
#print Not
#check Classical.em
#check Classical.byContradiction
```


There are no explicit rules for `Not` (`¬`) since `¬ p` is defined as
`p → False`.


```lean
theorem And_swap (a b : Prop) :
    a ∧ b → b ∧ a :=
  by
    intro hab
    apply And.intro
    apply And.right
    exact hab
    apply And.left
    exact hab
```


The above proof step by step:

* Assume we know `a ∧ b`.
* The goal is `b ∧ a`.
* Show `b`, which we can if we can show a conjunction with `b` on the right.
* We can, we already have `a ∧ b`.
* Show `a`, which we can if we can show a conjunction with `a` on the left.
* We can, we already have `a ∧ b`.

The `{ … }` combinator focuses on a specific subgoal. The tactic inside must
fully prove it. In the proof below, `{ … }` is used for each of the two subgoals
to give more structure to the proof.


```lean
theorem And_swap_braces :
    ∀a b : Prop, a ∧ b → b ∧ a :=
  by
    intro a b hab
    apply And.intro
    { exact And.right hab }
    { exact And.left hab }
```


Notice above how we pass the hypothesis `hab` directly to the theorems
`And.right` and `And.left`, instead of waiting for the theorems' assumptions to
appear as new subgoals. This is a small forward step in an otherwise backward
proof.


```lean
opaque f : ℕ → ℕ

theorem f5_if (h : ∀n : ℕ, f n = n) :
    f 5 = 5 :=
  by exact h 5

theorem Or_swap (a b : Prop) :
    a ∨ b → b ∨ a :=
  by
    intro hab
    apply Or.elim hab
    { intro ha
      exact Or.inr ha }
    { intro hb
      exact Or.inl hb }

theorem modus_ponens (a b : Prop) :
    (a → b) → a → b :=
  by
    intro hab ha
    apply hab
    exact ha

theorem Not_Not_intro (a : Prop) :
    a → ¬¬ a :=
  by
    intro ha hna
    apply hna
    exact ha

theorem Exists_double_iden :
    ∃n : ℕ, double n = n :=
  by
    apply Exists.intro 0
    rfl
```


### Reasoning about Equality


```lean
#check Eq.refl
#check Eq.symm
#check Eq.trans
#check Eq.subst
```


The above rules can be used directly:


```lean
theorem Eq_trans_symm {α : Type} (a b c : α)
      (hab : a = b) (hcb : c = b) :
    a = c :=
  by
    apply Eq.trans
    { exact hab }
    { apply Eq.symm
      exact hcb }
```


`rw` applies a single equation as a left-to-right rewrite rule, once. To
apply an equation right-to-left, prefix its name with `←`.


```lean
theorem Eq_trans_symm_rw {α : Type} (a b c : α)
      (hab : a = b) (hcb : c = b) :
    a = c :=
  by
    rw [hab]
    rw [hcb]
```


`rw` can expand a definition. Below, `¬¬ a` becomes `¬ a → False`, and `¬ a`
becomes `a → False`.


```lean
theorem a_proof_of_negation (a : Prop) :
    a → ¬¬ a :=
  by
    rw [Not]
    rw [Not]
    intro ha
    intro hna
    apply hna
    exact ha
```


`simp` applies a standard set of rewrite rules (the __simp set__)
exhaustively. The set can be extended using the `@[simp]` attribute. Theorems
can be temporarily added to the simp set with the syntax
`simp [_theorem₁_, …, _theoremN_]`.


```lean
theorem cong_two_args_1p1 {α : Type} (a b c d : α)
      (g : α → α → ℕ → α) (hab : a = b) (hcd : c = d) :
    g a c (1 + 1) = g b d 2 :=
  by simp [hab, hcd]
```


`ac_rfl` is similar to `rfl`, but it can reason up to associativity and
commutativity of `+`, `*`, and other binary operators.


```lean
theorem abc_Eq_cba (a b c : ℕ) :
    a + b + c = c + b + a :=
  by ac_rfl
```


### Proofs by Mathematical Induction

`induction` performs induction on the specified variable. It gives rise to one
named subgoal per constructor.


```lean
theorem add_zero (n : ℕ) :
    add 0 n = n :=
  by
    induction n with
    | zero       => rfl
    | succ n' ih => simp [add, ih]

theorem add_succ (m n : ℕ) :
    add (Nat.succ m) n = Nat.succ (add m n) :=
  by
    induction n with
    | zero       => rfl
    | succ n' ih => simp [add, ih]

theorem add_comm (m n : ℕ) :
    add m n = add n m :=
  by
    induction n with
    | zero       => simp [add, add_zero]
    | succ n' ih => simp [add, add_succ, ih]

theorem add_assoc (l m n : ℕ) :
    add (add l m) n = add l (add m n) :=
  by
    induction n with
    | zero       => rfl
    | succ n' ih => simp [add, ih]
```


`ac_rfl` is extensible. We can register `add` as a commutative and
associative operator using the type class instance mechanism (explained in
lecture 5). This is useful for the `ac_rfl` invocation below.


```lean
instance Associative_add : Std.Associative add :=
  { assoc := add_assoc }

instance Commutative_add : Std.Commutative add :=
  { comm := add_comm }

theorem mul_add (l m n : ℕ) :
    mul l (add m n) = add (mul l m) (mul l n) :=
  by
    induction n with
    | zero       => rfl
    | succ n' ih =>
      simp [add, mul, ih]
      ac_rfl
```


### Cleanup Tactics

`clear` removes unused variables or hypotheses.

`rename` changes the name of a variable or hypothesis.


```lean
theorem cleanup_example (a b c : Prop) (ha : a) (hb : b)
      (hab : a → b) (hbc : b → c) :
    c :=
  by
    clear ha hab a
    apply hbc
    clear hbc c
    rename b => h
    exact h

end BackwardProofs

end LoVe
```

---



## Backward Proofs (ExerciseSheet) {#hhg-backward-proofs-exercisesheet}

> 📄 Source: https://raw.githubusercontent.com/lean-forward/logical_verification_2025/main/lean/LoVe/LoVe03_BackwardProofs_ExerciseSheet.lean

```lean
import LoVe.LoVe03_BackwardProofs_Demo
```


## LoVe Exercise 3: Backward Proofs

Replace the placeholders (e.g., `:= sorry`) with your solutions.


```lean
set_option autoImplicit false
set_option tactic.hygienic false

namespace LoVe

namespace BackwardProofs
```


### Question 1: Connectives and Quantifiers

1.1. Carry out the following proofs using basic tactics such as `intro`,
`apply`, and `exact`.

Hint: Some strategies for carrying out such proofs are described at the end of
Section 3.3 in the Hitchhiker's Guide.


```lean
theorem I (a : Prop) :
    a → a :=
  sorry

theorem K (a b : Prop) :
    a → b → b :=
  sorry

theorem C (a b c : Prop) :
    (a → b → c) → b → a → c :=
  sorry

theorem proj_fst (a : Prop) :
    a → a → a :=
  sorry
```


Please give a different answer than for `proj_fst`:


```lean
theorem proj_snd (a : Prop) :
    a → a → a :=
  sorry

theorem some_nonsense (a b c : Prop) :
    (a → b → c) → a → (a → c) → b → c :=
  sorry
```


1.2. Prove the contraposition rule using basic tactics.


```lean
theorem contrapositive (a b : Prop) :
    (a → b) → ¬ b → ¬ a :=
  sorry
```


1.3. Prove the distributivity of `∀` over `∧` using basic tactics.

Hint: This exercise is tricky, especially the right-to-left direction. Some
forward reasoning, like in the proof of `and_swap_braces` in the lecture, might
be necessary.


```lean
theorem forall_and {α : Type} (p q : α → Prop) :
    (∀x, p x ∧ q x) ↔ (∀x, p x) ∧ (∀x, q x) :=
  sorry
```


### Question 2: Natural Numbers

2.1. Prove the following recursive equations on the first argument of the
`mul` operator defined in lecture 1.


```lean
#check mul

theorem mul_zero (n : ℕ) :
    mul 0 n = 0 :=
  sorry

#check add_succ
theorem mul_succ (m n : ℕ) :
    mul (Nat.succ m) n = add (mul m n) n :=
  sorry
```


2.2. Prove commutativity and associativity of multiplication using the
`induction` tactic. Choose the induction variable carefully.


```lean
theorem mul_comm (m n : ℕ) :
    mul m n = mul n m :=
  sorry

theorem mul_assoc (l m n : ℕ) :
    mul (mul l m) n = mul l (mul m n) :=
  sorry
```


2.3. Prove the symmetric variant of `mul_add` using `rw`. To apply
commutativity at a specific position, instantiate the rule by passing some
arguments (e.g., `mul_comm _ l`).


```lean
theorem add_mul (l m n : ℕ) :
    mul (add l m) n = add (mul n l) (mul n m) :=
  sorry
```


### Question 3 (**optional**): Intuitionistic Logic

Intuitionistic logic is extended to classical logic by assuming a classical
axiom. There are several possibilities for the choice of axiom. In this
question, we are concerned with the logical equivalence of three different
axioms:


```lean
def ExcludedMiddle : Prop :=
  ∀a : Prop, a ∨ ¬ a

def Peirce : Prop :=
  ∀a b : Prop, ((a → b) → a) → a

def DoubleNegation : Prop :=
  ∀a : Prop, (¬¬ a) → a
```


For the proofs below, avoid using theorems from Lean's `Classical` namespace.

3.1 (**optional**). Prove the following implication using tactics.

Hint: You will need `Or.elim` and `False.elim`. You can use
`rw [ExcludedMiddle]` to unfold the definition of `ExcludedMiddle`,
and similarly for `Peirce`.


```lean
theorem Peirce_of_EM :
    ExcludedMiddle → Peirce :=
  sorry
```


3.2 (**optional**). Prove the following implication using tactics.


```lean
theorem DN_of_Peirce :
    Peirce → DoubleNegation :=
  sorry
```


We leave the remaining implication for the homework:


```lean
namespace SorryTheorems

theorem EM_of_DN :
    DoubleNegation → ExcludedMiddle :=
  sorry

end SorryTheorems

end BackwardProofs

end LoVe
```

---



## Backward Proofs (HomeworkSheet) {#hhg-backward-proofs-homeworksheet}

> 📄 Source: https://raw.githubusercontent.com/lean-forward/logical_verification_2025/main/lean/LoVe/LoVe03_BackwardProofs_HomeworkSheet.lean

```lean
import LoVe.LoVe03_BackwardProofs_ExerciseSheet
```


## LoVe Homework 3 (10 points): Backward Proofs

Replace the placeholders (e.g., `:= sorry`) with your solutions.


```lean
set_option autoImplicit false
set_option tactic.hygienic false

namespace LoVe

namespace BackwardProofs
```


### Question 1 (5 points): Connectives and Quantifiers

1.1 (4 points). Complete the following proofs using basic tactics such as
`intro`, `apply`, and `exact`.

Hint: Some strategies for carrying out such proofs are described at the end of
Section 3.3 in the Hitchhiker's Guide.


```lean
theorem B (a b c : Prop) :
    (a → b) → (c → a) → c → b :=
  sorry

theorem S (a b c : Prop) :
    (a → b → c) → (a → b) → a → c :=
  sorry

theorem more_nonsense (a b c d : Prop) :
    ((a → b) → c → d) → c → b → d :=
  sorry

theorem even_more_nonsense (a b c : Prop) :
    (a → b) → (a → c) → a → b → c :=
  sorry
```


1.2 (1 point). Prove the following theorem using basic tactics.


```lean
theorem weak_peirce (a b : Prop) :
    ((((a → b) → a) → a) → b) → b :=
  sorry
```


### Question 2 (5 points): Logical Connectives

2.1 (1 point). Prove the following property about double negation using basic
tactics.

Hints:

* Keep in mind that `¬ a` is defined as `a → False`. You can start by invoking
  `simp [Not]` if this helps you.

* You will need to apply the elimination rule for `False` at a key point in the
  proof.


```lean
theorem herman (a : Prop) :
    ¬¬ (¬¬ a → a) :=
  sorry
```


2.2 (2 points). Prove the missing link in our chain of classical axiom
implications.

Hints:

* One way to find the definitions of `DoubleNegation` and `ExcludedMiddle`
  quickly is to

  1. hold the Control (on Linux and Windows) or Command (on macOS) key pressed;
  2. move the cursor to the identifier `DoubleNegation` or `ExcludedMiddle`;
  3. click the identifier.

* You can use `rw DoubleNegation` to unfold the definition of
  `DoubleNegation`, and similarly for the other definitions.

* You will need to apply the double negation hypothesis for `a ∨ ¬ a`. You will
  also need the left and right introduction rules for `∨` at some point.


```lean
#check DoubleNegation
#check ExcludedMiddle

theorem EM_of_DN :
    DoubleNegation → ExcludedMiddle :=
  sorry
```


2.3 (2 points). We have proved three of the six possible implications
between `ExcludedMiddle`, `Peirce`, and `DoubleNegation`. State and prove the
three missing implications, exploiting the three theorems we already have.


```lean
#check Peirce_of_EM
#check DN_of_Peirce
#check EM_of_DN

-- enter your solution here

end BackwardProofs

end LoVe
```

---



## Forward Proofs (Demo) {#hhg-forward-proofs-demo}

> 📄 Source: https://raw.githubusercontent.com/lean-forward/logical_verification_2025/main/lean/LoVe/LoVe04_ForwardProofs_Demo.lean

```lean
import LoVe.LoVe02_ProgramsAndTheorems_Demo
```


## LoVe Demo 4: Forward Proofs

When developing a proof, often it makes sense to work __forward__: to start with
what we already know and proceed step by step towards our goal. Lean's
structured proofs and raw proof terms are two styles that support forward
reasoning.


```lean
set_option autoImplicit false
set_option tactic.hygienic false

namespace LoVe

namespace ForwardProofs
```


### Structured Constructs

Structured proofs are syntactic sugar sprinkled on top of Lean's
__proof terms__.

The simplest kind of structured proof is the name of a theorem, possibly with
arguments.


```lean
theorem add_comm (m n : ℕ) :
    add m n = add n m :=
  sorry

theorem add_comm_zero_left (n : ℕ) :
    add 0 n = add n 0 :=
  add_comm 0 n
```


The equivalent backward proof:


```lean
theorem add_comm_zero_left_by_exact (n : ℕ) :
    add 0 n = add n 0 :=
  by exact add_comm 0 n
```


`fix` and `assume` move `∀`-quantified variables and assumptions from the
goal into the local context. They can be seen as structured versions of the
`intro` tactic.

`show` repeats the goal to prove. It is useful as documentation or to rephrase
the goal (up to computation).


```lean
theorem fst_of_two_props :
    ∀a b : Prop, a → b → a :=
  fix a b : Prop
  assume ha : a
  assume hb : b
  show a from
    ha

theorem fst_of_two_props_show (a b : Prop) (ha : a) (hb : b) :
    a :=
  show a from
    ha

theorem fst_of_two_props_no_show (a b : Prop) (ha : a) (hb : b) :
    a :=
  ha
```


`have` proves an intermediate theorem, which can refer to the local
context.


```lean
theorem prop_comp (a b c : Prop) (hab : a → b) (hbc : b → c) :
    a → c :=
  assume ha : a
  have hb : b :=
    hab ha
  have hc : c :=
    hbc hb
  show c from
    hc

theorem prop_comp_inline (a b c : Prop) (hab : a → b)
    (hbc : b → c) :
  a → c :=
  assume ha : a
  show c from
    hbc (hab ha)
```


### Forward Reasoning about Connectives and Quantifiers


```lean
theorem And_swap (a b : Prop) :
    a ∧ b → b ∧ a :=
  assume hab : a ∧ b
  have ha : a :=
    And.left hab
  have hb : b :=
    And.right hab
  show b ∧ a from
    And.intro hb ha

theorem Or_swap (a b : Prop) :
    a ∨ b → b ∨ a :=
  assume hab : a ∨ b
  show b ∨ a from
    Or.elim hab
      (assume ha : a
       show b ∨ a from
         Or.inr ha)
      (assume hb : b
       show b ∨ a from
         Or.inl hb)

def double (n : ℕ) : ℕ :=
  n + n

theorem Nat_exists_double_iden :
    ∃n : ℕ, double n = n :=
  Exists.intro 0
    (show double 0 = 0 from
     by rfl)

theorem Nat_exists_double_iden_no_show :
    ∃n : ℕ, double n = n :=
  Exists.intro 0 (by rfl)

theorem modus_ponens (a b : Prop) :
    (a → b) → a → b :=
  assume hab : a → b
  assume ha : a
  show b from
    hab ha

theorem not_not_intro (a : Prop) :
    a → ¬¬ a :=
  assume ha : a
  assume hna : ¬ a
  show False from
    hna ha
```


Just as you can apply forward reasoning inside a backward proof, you can
apply backward reasoning inside a forward proof (indicated with `by`):


```lean
theorem Forall.one_point {α : Type} (t : α) (P : α → Prop) :
    (∀x, x = t → P x) ↔ P t :=
  Iff.intro
    (assume hall : ∀x, x = t → P x
     show P t from
       by
         apply hall t
         rfl)
    (assume hp : P t
     fix x : α
     assume heq : x = t
     show P x from
       by
         rw [heq]
         exact hp)

theorem beast_666 (beast : ℕ) :
    (∀n, n = 666 → beast ≥ n) ↔ beast ≥ 666 :=
  Forall.one_point _ _

#print beast_666

theorem Exists.one_point {α : Type} (t : α) (P : α → Prop) :
    (∃x : α, x = t ∧ P x) ↔ P t :=
  Iff.intro
    (assume hex : ∃x, x = t ∧ P x
     show P t from
       Exists.elim hex
         (fix x : α
          assume hand : x = t ∧ P x
          have hxt : x = t :=
            And.left hand
          have hpx : P x :=
            And.right hand
          show P t from
            by
              rw [←hxt]
              exact hpx))
    (assume hp : P t
     show ∃x : α, x = t ∧ P x from
       Exists.intro t
         (have tt : t = t :=
            by rfl
          show t = t ∧ P t from
            And.intro tt hp))
```


### Calculational Proofs

In informal mathematics, we often use transitive chains of equalities,
inequalities, or equivalences (e.g., `a ≥ b ≥ c`). In Lean, such calculational
proofs are supported by `calc`.

Syntax:

    calc
      _term₀_ _op₁_ _term₁_ :=
        _proof₁_
      _ _op₂_ _term₂_ :=
        _proof₂_
     ⋮
      _ _opN_ _termN_ :=
        _proofN_


```lean
theorem two_mul_example (m n : ℕ) :
    2 * m + n = m + n + m :=
  calc
    2 * m + n = m + m + n :=
      by rw [Nat.two_mul]
    _ = m + n + m :=
      by ac_rfl
```


`calc` saves some repetition, some `have` labels, and some transitive
reasoning:


```lean
theorem two_mul_example_have (m n : ℕ) :
    2 * m + n = m + n + m :=
  have hmul : 2 * m + n = m + m + n :=
    by rw [Nat.two_mul]
  have hcomm : m + m + n = m + n + m :=
    by ac_rfl
  show _ from
    Eq.trans hmul hcomm
```


### Forward Reasoning with Tactics

The `have`, `let`, and `calc` structured proof commands are also available as a
tactic. Even in tactic mode, it can be useful to state intermediate results and
definitions in a forward fashion.


```lean
theorem prop_comp_tactical (a b c : Prop) (hab : a → b)
    (hbc : b → c) :
    a → c :=
  by
    intro ha
    have hb : b :=
      hab ha
    let c' := c
    have hc : c' :=
      hbc hb
    exact hc
```


### Dependent Types

Dependent types are the defining feature of the dependent type theory family of
logics.

Consider a function `pick` that take a number `n : ℕ` and that returns a number
between 0 and `n`. Conceptually, `pick` has a dependent type, namely

    `(n : ℕ) → {i : ℕ // i ≤ n}`

We can think of this type as a `ℕ`-indexed family, where each member's type may
depend on the index:

    `pick n : {i : ℕ // i ≤ n}`

But a type may also depend on another type, e.g., `List` (or `fun α ↦ List α`)
and `fun α ↦ α → α`.

A term may depend on a type, e.g., `fun α ↦ fun (x : α) ↦ x` (a polymorphic
identity function).

Of course, a term may also depend on a term.

Unless otherwise specified, a __dependent type__ means a type depending on a
term. This is what we mean when we say that simple type theory does not support
dependent types.

In summary, there are four cases for `fun x ↦ t` in the calculus of inductive
constructions (cf. Barendregt's `λ`-cube):

Body (`t`) |              | Argument (`x`) | Description
---------- | ------------ | -------------- | ----------------------------------
A term     | depending on | a term         | Simply typed anonymous function
A type     | depending on | a term         | Dependent type (strictly speaking)
A term     | depending on | a type         | Polymorphic term
A type     | depending on | a type         | Type constructor

Revised typing rules:

    C ⊢ t : (x : σ) → τ[x]    C ⊢ u : σ
    ———————————————————————————————————— App'
    C ⊢ t u : τ[u]

    C, x : σ ⊢ t : τ[x]
    ———————————————————————————————————— Fun'
    C ⊢ (fun x : σ ↦ t) : (x : σ) → τ[x]

These two rules degenerate to `App` and `Fun` if `x` does not occur in `τ[x]`

Example of `App'`:

    ⊢ pick : (n : ℕ) → {i : ℕ // i ≤ n}    ⊢ 5 : ℕ
    ——————————————————————————————————————————————— App'
    ⊢ pick 5 : {i : ℕ // i ≤ 5}

Example of `Fun'`:

    α : Type, x : α ⊢ x : α
    —————————————————————————————————— Fun or Fun'
    α : Type ⊢ (fun x : α ↦ x) : α → α
    ————————————————————————————————————————————————————— Fun'
    ⊢ (fun α : Type ↦ fun x : α ↦ x) : (α : Type) → α → α

Remarkably, universal quantification is simply an alias for a dependent type:

    `∀x : σ, τ` := `(x : σ) → τ`

This will become clearer below.


### The PAT Principle

`→` is used both as the implication symbol and as the type constructor of
functions. The two pairs of concepts not only look the same, they are the same,
by the PAT principle:

* PAT = propositions as types;
* PAT = proofs as terms.

Types:

* `σ → τ` is the type of total functions from `σ` to `τ`.
* `(x : σ) → τ[x]` is the dependent function type from `x : σ` to `τ[x]`.

Propositions:

* `P → Q` can be read as "`P` implies `Q`", or as the type of functions mapping
  proofs of `P` to proofs of `Q`.
* `∀x : σ, Q[x]` can be read as "for all `x`, `Q[x]`", or as the type of
  functions of type `(x : σ) → Q[x]`, mapping values `x` of type `σ` to proofs
  of `Q[x]`.

Terms:

* A constant is a term.
* A variable is a term.
* `t u` is the application of function `t` to value `u`.
* `fun x ↦ t[x]` is a function mapping `x` to `t[x]`.

Proofs:

* A theorem or hypothesis name is a proof.
* `H t`, which instantiates the leading parameter or quantifier of proof `H`'
  statement with term `t`, is a proof.
* `H G`, which discharges the leading assumption of `H`'s statement with
  proof `G`, is a proof.
* `fun h : P ↦ H[h]` is a proof of `P → Q`, assuming `H[h]` is a proof of `Q`
  for `h : P`.
* `fun x : σ ↦ H[x]` is a proof of `∀x : σ, Q[x]`, assuming `H[x]` is a proof
  of `Q[x]` for `x : σ`.


```lean
theorem And_swap_raw (a b : Prop) :
    a ∧ b → b ∧ a :=
  fun hab : a ∧ b ↦ And.intro (And.right hab) (And.left hab)

theorem And_swap_tactical (a b : Prop) :
    a ∧ b → b ∧ a :=
  by
    intro hab
    apply And.intro
    apply And.right
    exact hab
    apply And.left
    exact hab
```


Tactical proofs are reduced to proof terms.


```lean
#print And_swap
#print And_swap_raw
#print And_swap_tactical

end ForwardProofs
```


### Induction by Pattern Matching and Recursion

By the PAT principle, a proof by induction is the same as a recursively
specified proof term. Thus, as alternative to the `induction` tactic, induction
can also be done by pattern matching and recursion:

* the induction hypothesis is then available under the name of the theorem we
  are proving;

* well-foundedness of the argument is often proved automatically.


```lean
#check reverse

theorem reverse_append {α : Type} :
    ∀xs ys : List α,
      reverse (xs ++ ys) = reverse ys ++ reverse xs
  | [],      ys => by simp [reverse]
  | x :: xs, ys => by simp [reverse, reverse_append xs]

theorem reverse_append_tactical {α : Type} (xs ys : List α) :
    reverse (xs ++ ys) = reverse ys ++ reverse xs :=
  by
    induction xs with
    | nil           => simp [reverse]
    | cons x xs' ih => simp [reverse, ih]

theorem reverse_reverse {α : Type} :
    ∀xs : List α, reverse (reverse xs) = xs
  | []      => by rfl
  | x :: xs =>
    by simp [reverse, reverse_append, reverse_reverse xs]

end LoVe
```

---



## Forward Proofs (ExerciseSheet) {#hhg-forward-proofs-exercisesheet}

> 📄 Source: https://raw.githubusercontent.com/lean-forward/logical_verification_2025/main/lean/LoVe/LoVe04_ForwardProofs_ExerciseSheet.lean

```lean
import LoVe.LoVelib
```


## LoVe Exercise 4: Forward Proofs


```lean
set_option autoImplicit false
set_option tactic.hygienic false

namespace LoVe
```


### Question 1: Connectives and Quantifiers

1.1. Supply structured proofs of the following theorems.


```lean
theorem I (a : Prop) :
    a → a :=
  sorry

theorem K (a b : Prop) :
    a → b → b :=
  sorry

theorem C (a b c : Prop) :
    (a → b → c) → b → a → c :=
  sorry

theorem proj_fst (a : Prop) :
    a → a → a :=
  sorry
```


Please give a different answer than for `proj_fst`.


```lean
theorem proj_snd (a : Prop) :
    a → a → a :=
  sorry

theorem some_nonsense (a b c : Prop) :
    (a → b → c) → a → (a → c) → b → c :=
  sorry
```


1.2. Supply a structured proof of the contraposition rule.


```lean
theorem contrapositive (a b : Prop) :
    (a → b) → ¬ b → ¬ a :=
  sorry
```


1.3. Supply a structured proof of the distributivity of `∀` over `∧`.


```lean
theorem forall_and {α : Type} (p q : α → Prop) :
    (∀x, p x ∧ q x) ↔ (∀x, p x) ∧ (∀x, q x) :=
  sorry
```


1.4 (**optional**). Supply a structured proof of the following property,
which can be used to pull a `∀` quantifier past an `∃` quantifier.


```lean
theorem forall_exists_of_exists_forall {α : Type} (p : α → α → Prop) :
    (∃x, ∀y, p x y) → (∀y, ∃x, p x y) :=
  sorry
```


### Question 2: Chain of Equalities

2.1. Write the following proof using `calc`.

      (a + b) * (a + b)
    = a * (a + b) + b * (a + b)
    = a * a + a * b + b * a + b * b
    = a * a + a * b + a * b + b * b
    = a * a + 2 * a * b + b * b

Hint: This is a difficult question. You might need the tactics `simp` and
`ac_rfl` and some of the theorems `mul_add`, `add_mul`, `add_comm`, `add_assoc`,
`mul_comm`, `mul_assoc`, , and `Nat.two_mul`.


```lean
theorem binomial_square (a b : ℕ) :
    (a + b) * (a + b) = a * a + 2 * a * b + b * b :=
  sorry
```


2.2 (**optional**). Prove the same argument again, this time as a structured
proof, with `have` steps corresponding to the `calc` equations. Try to reuse as
much of the above proof idea as possible, proceeding mechanically.


```lean
theorem binomial_square₂ (a b : ℕ) :
    (a + b) * (a + b) = a * a + 2 * a * b + b * b :=
  sorry
```


### Question 3 (**optional**): One-Point Rules

3.1 (**optional**). Prove that the following wrong formulation of the one-point
rule for `∀` is inconsistent, using a structured proof.


```lean
axiom All.one_point_wrong {α : Type} (t : α) (P : α → Prop) :
    (∀x : α, x = t ∧ P x) ↔ P t

theorem All.proof_of_False :
    False :=
  sorry
```


3.2 (**optional**). Prove that the following wrong formulation of the
one-point rule for `∃` is inconsistent, using a structured proof.


```lean
axiom Exists.one_point_wrong {α : Type} (t : α) (P : α → Prop) :
    (∃x : α, x = t → P x) ↔ P t

theorem Exists.proof_of_False :
    False :=
  sorry

end LoVe
```

---



## Forward Proofs (HomeworkSheet) {#hhg-forward-proofs-homeworksheet}

> 📄 Source: https://raw.githubusercontent.com/lean-forward/logical_verification_2025/main/lean/LoVe/LoVe04_ForwardProofs_HomeworkSheet.lean

```lean
import LoVe.LoVe03_BackwardProofs_ExerciseSheet
```


## LoVe Homework 4 (10 points): Forward Proofs

Replace the placeholders (e.g., `:= sorry`) with your solutions.


```lean
set_option autoImplicit false
set_option tactic.hygienic false

namespace LoVe
```


### Question 1 (4 points): Logic Puzzles

Consider the following tactical proof:


```lean
theorem about_Impl :
    ∀a b : Prop, ¬ a ∨ b → a → b :=
  by
    intros a b hor ha
    apply Or.elim hor
    { intro hna
      apply False.elim
      apply hna
      exact ha }
    { intro hb
      exact hb }
```


1.1 (2 points). Prove the same theorem again, this time by providing a proof
term.

Hint: There is an easy way.


```lean
theorem about_Impl_term :
    ∀a b : Prop, ¬ a ∨ b → a → b :=
  sorry
```


1.2 (2 points). Prove the same theorem again, this time by providing a
structured proof, with `fix`, `assume`, and `show`.


```lean
theorem about_Impl_struct :
    ∀a b : Prop, ¬ a ∨ b → a → b :=
  sorry
```


### Question 2 (6 points): Connectives and Quantifiers

2.1 (3 points). Supply a structured proof of the commutativity of `∨` under a
`∀` quantifier, using no other theorems than the introduction and elimination
rules for `∀`, `∨`, and `↔`.


```lean
theorem Or_comm_under_All {α : Type} (p q : α → Prop) :
    (∀x, p x ∨ q x) ↔ (∀x, q x ∨ p x) :=
  sorry
```


2.2 (3 points). We have proved or stated three of the six possible
implications between `ExcludedMiddle`, `Peirce`, and `DoubleNegation` in the
exercise of lecture 3. Prove the three missing implications using structured
proofs, exploiting the three theorems we already have.


```lean
namespace BackwardProofs

#check Peirce_of_EM
#check DN_of_Peirce
#check SorryTheorems.EM_of_DN

theorem Peirce_of_DN :
    DoubleNegation → Peirce :=
  sorry

theorem EM_of_Peirce :
    Peirce → ExcludedMiddle :=
  sorry

theorem dn_of_em :
    ExcludedMiddle → DoubleNegation :=
  sorry

end BackwardProofs

end LoVe
```

---



## Functional Programming (Demo) {#hhg-functional-programming-demo}

> 📄 Source: https://raw.githubusercontent.com/lean-forward/logical_verification_2025/main/lean/LoVe/LoVe05_FunctionalProgramming_Demo.lean

```lean
import LoVe.LoVelib
```


## LoVe Demo 5: Functional Programming

We take a closer look at the basics of typed functional programming: inductive
types, proofs by induction, recursive functions, pattern matching, structures
(records), and type classes.


```lean
set_option autoImplicit false
set_option tactic.hygienic false

namespace LoVe
```


### Inductive Types

Recall the definition of type `Nat`:


```lean
#print Nat
```


Mottos:

* **No junk**: The type contains no values beyond those expressible using the
  constructors.

* **No confusion**: Values built in a different ways are different.

For `Nat`:

* "No junk" means that there are no special values, say, `–1` or `ε`, that
  cannot be expressed using a finite combination of `Nat.zero` and `Nat.succ`.

* "No confusion" is what ensures that `Nat.zero` ≠ `Nat.succ n`.

In addition, values of inductive types are always finite.
`Nat.succ (Nat.succ …)` is not a value.


### Structural Induction

__Structural induction__ is a generalization of mathematical induction to
inductive types. To prove a property `P[n]` for all natural numbers `n`, it
suffices to prove the base case

    `P[0]`

and the induction step

    `∀k, P[k] → P[k + 1]`

For lists, the base case is

    `P[[]]`

and the induction step is

    `∀y ys, P[ys] → P[y :: ys]`

In general, there is one subgoal per constructor, and induction hypotheses are
available for all constructor arguments of the type we are doing the induction
on.


```lean
theorem Nat.succ_neq_self (n : ℕ) :
    Nat.succ n ≠ n :=
  by
    induction n with
    | zero       => simp
    | succ n' ih => simp [ih]
```


### Structural Recursion

__Structural recursion__ is a form of recursion that allows us to peel off
one constructor from the value on which we recurse. Such functions are
guaranteed to call themselves only finitely many times before the recursion
stops. This is a prerequisite for establishing that the function terminates.


```lean
def fact : ℕ → ℕ
  | 0     => 1
  | n + 1 => (n + 1) * fact n

def factThreeCases : ℕ → ℕ
  | 0     => 1
  | 1     => 1
  | n + 1 => (n + 1) * factThreeCases n
```


For structurally recursive functions, Lean can automatically prove
termination. For more general recursive schemes, the termination check may fail.
Sometimes it does so for a good reason, as in the following example:

-- fails
def illegal : ℕ → ℕ
  | n => illegal n + 1


```lean
opaque immoral : ℕ → ℕ

axiom immoral_eq (n : ℕ) :
    immoral n = immoral n + 1

theorem proof_of_False :
    False :=
  have hi : immoral 0 = immoral 0 + 1 :=
    immoral_eq 0
  have him :
    immoral 0 - immoral 0 = immoral 0 + 1 - immoral 0 :=
    by rw [←hi]
  have h0eq1 : 0 = 1 :=
    by simp at him
  show False from
    by simp at h0eq1
```


### Pattern Matching Expressions

    `match` _term₁_, …, _termM_ `with`
    | _pattern₁₁_, …, _pattern₁M_ => _result₁_
        ⋮
    | _patternN₁_, …, _patternNM_ => _resultN_

`match` allows nonrecursive pattern matching within terms.


```lean
def bcount {α : Type} (p : α → Bool) : List α → ℕ
  | []      => 0
  | x :: xs =>
    match p x with
    | true  => bcount p xs + 1
    | false => bcount p xs

def min (a b : ℕ) : ℕ :=
  if a ≤ b then a else b
```


### Structures

Lean provides a convenient syntax for defining records, or structures. These are
essentially nonrecursive, single-constructor inductive types.


```lean
structure RGB where
  red   : ℕ
  green : ℕ
  blue  : ℕ

#check RGB.mk
#check RGB.red
#check RGB.green
#check RGB.blue

namespace RGB_as_inductive
```


The RGB structure definition is equivalent to the following set of
definitions:


```lean
inductive RGB : Type where
  | mk : ℕ → ℕ → ℕ → RGB

def RGB.red : RGB → ℕ
  | RGB.mk r _ _ => r

def RGB.green : RGB → ℕ
  | RGB.mk _ g _ => g

def RGB.blue : RGB → ℕ
  | RGB.mk _ _ b => b

end RGB_as_inductive
```


A new structure can be created by extending an existing structure:


```lean
structure RGBA extends RGB where
  alpha : ℕ
```


An `RGBA` is a `RGB` with the extra field `alpha : ℕ`.


```lean
#print RGBA

def pureRed : RGB :=
  RGB.mk 0xff 0x00 0x00

def pureGreen : RGB :=
  { red   := 0x00
    green := 0xff
    blue  := 0x00 }

def semitransparentGreen : RGBA :=
  { pureGreen with
    alpha := 0x7f }

#print pureRed
#print pureGreen
#print semitransparentGreen

def shuffle (c : RGB) : RGB :=
  { red   := RGB.green c
    green := RGB.blue c
    blue  := RGB.red c }
```


Alternative definition using pattern matching:


```lean
def shufflePattern : RGB → RGB
  | RGB.mk r g b => RGB.mk g b r

theorem shuffle_shuffle_shuffle (c : RGB) :
    shuffle (shuffle (shuffle c)) = c :=
  by rfl
```


### Type Classes

A __type class__ is a structure type combining abstract constants and their
properties. A type can be declared an instance of a type class by providing
concrete definitions for the constants and proving that the properties hold.
Based on the type, Lean retrieves the relevant instance.


```lean
#print Inhabited

instance Nat.Inhabited : Inhabited ℕ :=
  { default := 0 }

instance List.Inhabited {α : Type} : Inhabited (List α) :=
  { default := [] }

#eval (Inhabited.default : ℕ)
#eval (Inhabited.default : List Int)

def head {α : Type} [Inhabited α] : List α → α
  | []     => Inhabited.default
  | x :: _ => x

theorem head_head {α : Type} [Inhabited α] (xs : List α) :
    head [head xs] = head xs :=
  by rfl

#eval head ([] : List ℕ)

#check List.head

instance Fun.Inhabited {α β : Type} [Inhabited β] :
  Inhabited (α → β) :=
  { default := fun a : α ↦ Inhabited.default }

instance Prod.Inhabited {α β : Type}
    [Inhabited α] [Inhabited β] :
  Inhabited (α × β) :=
  { default := (Inhabited.default, Inhabited.default) }
```


We encountered these type classes in lecture 3:


```lean
#print Std.Associative
#print Std.Commutative
```


### Lists

`List` is an inductive polymorphic type constructed from `List.nil` and
`List.cons`:


```lean
#print List
```


`cases` performs a case distinction on the specified term. This gives rise
to as many subgoals as there are constructors in the definition of the term's
type. The tactic behaves the same as `induction` except that it does not
produce induction hypotheses. Here is a contrived example:


```lean
theorem head_head_cases {α : Type} [Inhabited α]
      (xs : List α) :
    head [head xs] = head xs :=
  by
    cases xs with
    | nil        => rfl
    | cons x xs' => rfl
```


`match` is the structured equivalent:


```lean
theorem head_head_match {α : Type} [Inhabited α]
      (xs : List α) :
    head [head xs] = head xs :=
  match xs with
  | List.nil        => by rfl
  | List.cons x xs' => by rfl
```


`cases` can also be used on a hypothesis of the form `l = r`. It matches `r`
against `l` and replaces all occurrences of the variables occurring in `r` with
the corresponding terms in `l` everywhere in the goal.


```lean
theorem injection_example {α : Type} (x y : α) (xs ys : List α)
      (h : x :: xs = y :: ys) :
    x = y ∧ xs = ys :=
  by
    cases h
    simp
```


If `r` fails to match `l`, no subgoals emerge; the proof is complete.


```lean
theorem distinctness_example {α : Type} (y : α) (ys : List α)
      (h : [] = y :: ys) :
    False :=
  by cases h

def map {α β : Type} (f : α → β) : List α → List β
  | []      => []
  | x :: xs => f x :: map f xs

def mapArgs {α β : Type} : (α → β) → List α → List β
  | _, []      => []
  | f, x :: xs => f x :: mapArgs f xs

#check List.map

theorem map_ident {α : Type} (xs : List α) :
    map (fun x ↦ x) xs = xs :=
  by
    induction xs with
    | nil           => rfl
    | cons x xs' ih => simp [map, ih]

theorem map_comp {α β γ : Type} (f : α → β) (g : β → γ)
      (xs : List α) :
    map g (map f xs) = map (fun x ↦ g (f x)) xs :=
  by
    induction xs with
    | nil           => rfl
    | cons x xs' ih => simp [map, ih]

theorem map_append {α β : Type} (f : α → β)
      (xs ys : List α) :
    map f (xs ++ ys) = map f xs ++ map f ys :=
  by
    induction xs with
    | nil           => rfl
    | cons x xs' ih => simp [map, ih]

def tail {α : Type} : List α → List α
  | []      => []
  | _ :: xs => xs

def headOpt {α : Type} : List α → Option α
  | []     => Option.none
  | x :: _ => Option.some x

def headPre {α : Type} : (xs : List α) → xs ≠ [] → α
  | [],     hxs => by simp at *
  | x :: _, hxs => x

#eval headOpt [3, 1, 4]
#eval headPre [3, 1, 4] (by simp)

def zip {α β : Type} : List α → List β → List (α × β)
  | x :: xs, y :: ys => (x, y) :: zip xs ys
  | [],      _       => []
  | _ :: _,  []      => []

#check List.zip

def length {α : Type} : List α → ℕ
  | []      => 0
  | x :: xs => length xs + 1

#check List.length
```


`cases` can also be used to perform a case distinction on a proposition, in
conjunction with `Classical.em`. Two cases emerge: one in which the proposition
is true and one in which it is false.


```lean
#check Classical.em

theorem min_add_add (l m n : ℕ) :
    min (m + l) (n + l) = min m n + l :=
  by
    cases Classical.em (m ≤ n) with
    | inl h => simp [min, h]
    | inr h => simp [min, h]

theorem min_add_add_match (l m n : ℕ) :
    min (m + l) (n + l) = min m n + l :=
  match Classical.em (m ≤ n) with
  | Or.inl h => by simp [min, h]
  | Or.inr h => by simp [min, h]

theorem min_add_add_if (l m n : ℕ) :
    min (m + l) (n + l) = min m n + l :=
  if h : m ≤ n then
    by simp [min, h]
  else
    by simp [min, h]

theorem length_zip {α β : Type} (xs : List α) (ys : List β) :
    length (zip xs ys) = min (length xs) (length ys) :=
  by
    induction xs generalizing ys with
    | nil           => simp [min, length]
    | cons x xs' ih =>
      cases ys with
      | nil        => rfl
      | cons y ys' => simp [zip, length, ih ys', min_add_add]

theorem map_zip {α α' β β' : Type} (f : α → α')
      (g : β → β') :
    ∀xs ys,
      map (fun ab : α × β ↦
          (f (Prod.fst ab), g (Prod.snd ab)))
        (zip xs ys) =
      zip (map f xs) (map g ys)
  | x :: xs, y :: ys => by simp [zip, map, map_zip f g xs ys]
  | [],      _       => by rfl
  | _ :: _,  []      => by rfl
```


### Binary Trees

Inductive types with constructors taking several recursive arguments define
tree-like objects. __Binary trees__ have nodes with at most two children.


```lean
#print Tree
```


The type `AExp` of arithmetic expressions was also an example of a tree data
structure.

The nodes of a tree, whether inner nodes or leaf nodes, often carry labels or
other annotations.

Inductive trees contain no infinite branches, not even cycles. This is less
expressive than pointer- or reference-based data structures (in imperative
languages) but easier to reason about.

Recursive definitions (and proofs by induction) work roughly as for lists, but
we may need to recurse (or invoke the induction hypothesis) on several child
nodes.


```lean
def mirror {α : Type} : Tree α → Tree α
  | Tree.nil        => Tree.nil
  | Tree.node a l r => Tree.node a (mirror r) (mirror l)

theorem mirror_mirror {α : Type} (t : Tree α) :
    mirror (mirror t) = t :=
  by
    induction t with
    | nil                  => rfl
    | node a l r ih_l ih_r => simp [mirror, ih_l, ih_r]

theorem mirror_mirror_calc {α : Type} :
    ∀t : Tree α, mirror (mirror t) = t
  | Tree.nil        => by rfl
  | Tree.node a l r =>
    calc
      mirror (mirror (Tree.node a l r))
      = mirror (Tree.node a (mirror r) (mirror l)) :=
        by rfl
      _ = Tree.node a (mirror (mirror l))
        (mirror (mirror r)) :=
        by rfl
      _ = Tree.node a l (mirror (mirror r)) :=
        by rw [mirror_mirror_calc l]
      _ = Tree.node a l r :=
        by rw [mirror_mirror_calc r]

theorem mirror_Eq_nil_Iff {α : Type} :
    ∀t : Tree α, mirror t = Tree.nil ↔ t = Tree.nil
  | Tree.nil        => by simp [mirror]
  | Tree.node _ _ _ => by simp [mirror]
```


### Dependent Inductive Types (**optional**)


```lean
inductive Vec (α : Type) : ℕ → Type where
  | nil                                : Vec α 0
  | cons (a : α) {n : ℕ} (v : Vec α n) : Vec α (n + 1)

#check Vec.nil
#check Vec.cons

def listOfVec {α : Type} : ∀{n : ℕ}, Vec α n → List α
  | _, Vec.nil      => []
  | _, Vec.cons a v => a :: listOfVec v

def vecOfList {α : Type} :
    ∀xs : List α, Vec α (List.length xs)
  | []      => Vec.nil
  | x :: xs => Vec.cons x (vecOfList xs)

theorem length_listOfVec {α : Type} :
    ∀(n : ℕ) (v : Vec α n), List.length (listOfVec v) = n
  | _, Vec.nil      => by rfl
  | _, Vec.cons a v =>
    by simp [listOfVec, length_listOfVec _ v]

end LoVe
```

---



## Functional Programming (ExerciseSheet) {#hhg-functional-programming-exercisesheet}

> 📄 Source: https://raw.githubusercontent.com/lean-forward/logical_verification_2025/main/lean/LoVe/LoVe05_FunctionalProgramming_ExerciseSheet.lean

```lean
import LoVe.LoVe04_ForwardProofs_Demo
```


## LoVe Exercise 5: Functional Programming

Replace the placeholders (e.g., `:= sorry`) with your solutions.


```lean
set_option autoImplicit false
set_option tactic.hygienic false

namespace LoVe
```


### Question 1: Reverse of a List

We define an accumulator-based variant of `reverse`. The first argument, `as`,
serves as the accumulator. This definition is __tail-recursive__, meaning that
compilers and interpreters can easily optimize the recursion away, resulting in
more efficient code.


```lean
def reverseAccu {α : Type} : List α → List α → List α
  | as, []      => as
  | as, x :: xs => reverseAccu (x :: as) xs
```


1.1. Our intention is that `reverseAccu [] xs` should be equal to
`reverse xs`. But if we start an induction, we quickly see that the induction
hypothesis is not strong enough. Start by proving the following generalization
(using the `induction` tactic or pattern matching):


```lean
theorem reverseAccu_Eq_reverse_append {α : Type} :
    ∀as xs : List α, reverseAccu as xs = reverse xs ++ as :=
  sorry
```


1.2. Derive the desired equation.


```lean
theorem reverseAccu_eq_reverse {α : Type} (xs : List α) :
    reverseAccu [] xs = reverse xs :=
  sorry
```


1.3. Prove the following property.

Hint: A one-line inductionless proof is possible.


```lean
theorem reverseAccu_reverseAccu {α : Type} (xs : List α) :
    reverseAccu [] (reverseAccu [] xs) = xs :=
  sorry
```


1.4. Prove the following theorem by structural induction, as a "paper"
proof. This is a good exercise to develop a deeper understanding of how
structural induction works (and is good practice for the final exam).

    theorem reverseAccu_Eq_reverse_append {α : Type} :
      ∀as xs : list α, reverseAccu as xs = reverse xs ++ as

Guidelines for paper proofs:

We expect detailed, rigorous, mathematical proofs. You are welcome to use
standard mathematical notation or Lean structured commands (e.g., `assume`,
`have`, `show`, `calc`). You can also use tactical proofs (e.g., `intro`,
`apply`), but then please indicate some of the intermediate goals, so that we
can follow the chain of reasoning.

Major proof steps, including applications of induction and invocation of the
induction hypothesis, must be stated explicitly. For each case of a proof by
induction, you must list the induction hypotheses assumed (if any) and the goal
to be proved. Minor proof steps corresponding to `rfl` or `simp` need not be
justified if you think they are obvious (to humans), but you should say which
key theorems they depend on. You should be explicit whenever you use a function
definition.


```lean
-- enter your paper proof here
```


### Question 2: Drop and Take

The `drop` function removes the first `n` elements from the front of a list.


```lean
def drop {α : Type} : ℕ → List α → List α
  | 0,     xs      => xs
  | _ + 1, []      => []
  | m + 1, _ :: xs => drop m xs
```


2.1. Define the `take` function, which returns a list consisting of the first
`n` elements at the front of a list.

To avoid unpleasant surprises in the proofs, we recommend that you follow the
same recursion pattern as for `drop` above.


```lean
def take {α : Type} : ℕ → List α → List α :=
  sorry

#eval take 0 [3, 7, 11]   -- expected: []
#eval take 1 [3, 7, 11]   -- expected: [3]
#eval take 2 [3, 7, 11]   -- expected: [3, 7]
#eval take 3 [3, 7, 11]   -- expected: [3, 7, 11]
#eval take 4 [3, 7, 11]   -- expected: [3, 7, 11]

#eval take 2 ["a", "b", "c"]   -- expected: ["a", "b"]
```


2.2. Prove the following theorems, using `induction` or pattern matching.
Notice that they are registered as simplification rules thanks to the `@[simp]`
attribute.


```lean
@[simp] theorem drop_nil {α : Type} :
    ∀n : ℕ, drop n ([] : List α) = [] :=
  sorry

@[simp] theorem take_nil {α : Type} :
    ∀n : ℕ, take n ([] : List α) = [] :=
  sorry
```


2.3. Follow the recursion pattern of `drop` and `take` to prove the
following theorems. In other words, for each theorem, there should be three
cases, and the third case will need to invoke the induction hypothesis.

Hint: Note that there are three variables in the `drop_drop` theorem (but only
two arguments to `drop`). For the third case, `← add_assoc` might be useful.


```lean
theorem drop_drop {α : Type} :
    ∀(m n : ℕ) (xs : List α), drop n (drop m xs) = drop (n + m) xs
  | 0,     n, xs      => by rfl
  -- supply the two missing cases here

theorem take_take {α : Type} :
    ∀(m : ℕ) (xs : List α), take m (take m xs) = take m xs :=
  sorry

theorem take_drop {α : Type} :
    ∀(n : ℕ) (xs : List α), take n xs ++ drop n xs = xs :=
  sorry
```


### Question 3: A Type of Terms

3.1. Define an inductive type corresponding to the terms of the untyped
λ-calculus, as given by the following grammar:

    Term  ::=  `var` String        -- variable (e.g., `x`)
            |  `lam` String Term   -- λ-expression (e.g., `λx. t`)
            |  `app` Term Term     -- application (e.g., `t u`)


```lean
-- enter your definition here
```


3.2 (**optional**). Register a textual representation of the type `Term` as
an instance of the `Repr` type class. Make sure to supply enough parentheses to
guarantee that the output is unambiguous.


```lean
def Term.repr : Term → String
-- enter your answer here

instance Term.Repr : Repr Term :=
  { reprPrec := fun t prec ↦ Term.repr t }
```


3.3 (**optional**). Test your textual representation. The following command
should print something like `(λx. ((y x) x))`.


```lean
#eval (Term.lam "x" (Term.app (Term.app (Term.var "y") (Term.var "x"))
    (Term.var "x")))

end LoVe
```

---



## Functional Programming (HomeworkSheet) {#hhg-functional-programming-homeworksheet}

> 📄 Source: https://raw.githubusercontent.com/lean-forward/logical_verification_2025/main/lean/LoVe/LoVe05_FunctionalProgramming_HomeworkSheet.lean

```lean
import LoVe.LoVe05_FunctionalProgramming_Demo
```


## LoVe Homework 5 (10 points + 2 bonus points): Functional Programming

Replace the placeholders (e.g., `:= sorry`) with your solutions.


```lean
set_option autoImplicit false
set_option tactic.hygienic false

namespace LoVe
```


### Question 1 (6 points): Huffman Trees

Consider the following type of weighted binary trees:


```lean
inductive HTree (α : Type)
  | leaf  : ℕ → α → HTree α
  | inner : ℕ → HTree α → HTree α → HTree α
```


Each constructor corresponds to a kind of node. An `HTree.leaf` node stores a
numeric weight and a label of some type `α`, and an `HTree.inner` node stores a
numeric weight, a left subtree, and a right subtree.

1.1 (1 point). Define a polymorphic Lean function called `weight` that takes a
tree over some type variable `α` and that returns the weight component of the
root node of the tree:


```lean
def weight {α : Type} : HTree α → ℕ :=
  sorry
```


1.2 (1 point). Define a polymorphic Lean function called `unite` that takes
two trees `l, r : HTree α` and that returns a new tree such that (1) its left
child is `l`; (2) its right child is `r`; and (3) its weight is the sum of the
weights of `l` and `r`.


```lean
def unite {α : Type} : HTree α → HTree α → HTree α :=
  sorry
```


1.3 (2 points). Consider the following `insort` function, which inserts a
tree `u` in a list of trees that is sorted by increasing weight and which
preserves the sorting. (If the input list is not sorted, the result is not
necessarily sorted.)


```lean
def insort {α : Type} (u : HTree α) : List (HTree α) → List (HTree α)
  | []      => [u]
  | t :: ts => if weight u ≤ weight t then u :: t :: ts else t :: insort u ts
```


Prove that `insort`ing a tree into a list cannot yield the empty list:


```lean
theorem insort_Neq_nil {α : Type} (t : HTree α) :
    ∀ts : List (HTree α), insort t ts ≠ [] :=
  sorry
```


1.4 (2 points). Prove the same property as above again, this time as a
"paper" proof. Follow the guidelines given in question 1.4 of the exercise.


```lean
-- enter your paper proof here
```


### Question 2 (4 points + 2 bonus points): Gauss's Summation Formula

`sumUpToOfFun f n = f 0 + f 1 + ⋯ + f n`:


```lean
def sumUpToOfFun (f : ℕ → ℕ) : ℕ → ℕ
  | 0     => f 0
  | m + 1 => sumUpToOfFun f m + f (m + 1)
```


2.1 (2 points). Prove the following theorem, discovered by Carl Friedrich
Gauss as a pupil.

Hints:

* The `mul_add` and `add_mul` theorems might be useful to reason about
  multiplication.

* The `linarith` tactic introduced in lecture 6 might be useful to reason about
  addition.


```lean
#check mul_add
#check add_mul

theorem sumUpToOfFun_eq :
    ∀m : ℕ, 2 * sumUpToOfFun (fun i ↦ i) m = m * (m + 1) :=
  sorry
```


2.2 (2 points). Prove the following property of `sumUpToOfFun`.


```lean
theorem sumUpToOfFun_mul (f g : ℕ → ℕ) :
    ∀n : ℕ, sumUpToOfFun (fun i ↦ f i + g i) n =
      sumUpToOfFun f n + sumUpToOfFun g n :=
  sorry
```


2.3 (2 bonus points). Prove `sumUpToOfFun_mul` again as a "paper" proof.
Follow the guidelines given in question 1.4 of the exercise.


```lean
-- enter your paper proof here

end LoVe
```

---



## Inductive Predicates (Demo) {#hhg-inductive-predicates-demo}

> 📄 Source: https://raw.githubusercontent.com/lean-forward/logical_verification_2025/main/lean/LoVe/LoVe06_InductivePredicates_Demo.lean

```lean
import LoVe.LoVe04_ForwardProofs_Demo
import LoVe.LoVe05_FunctionalProgramming_Demo
```


## LoVe Demo 6: Inductive Predicates

__Inductive predicates__, or inductively defined propositions, are a convenient
way to specify functions of type `⋯ → Prop`. They are reminiscent of formal
systems and of the Horn clauses of Prolog, the logic programming language par
excellence.

A possible view of Lean:

    Lean = functional programming + logic programming + more logic


```lean
set_option autoImplicit false
set_option tactic.hygienic false

namespace LoVe
```


### Introductory Examples

#### Even Numbers

Mathematicians often define sets as the smallest that meets some criteria. For
example:

    The set `E` of even natural numbers is defined as the smallest set closed
    under the following rules: (1) `0 ∈ E` and (2) for every `k ∈ ℕ`, if
    `k ∈ E`, then `k + 2 ∈ E`.

In Lean, we can define the corresponding "is even" predicate as follows:


```lean
inductive Even : ℕ → Prop where
  | zero    : Even 0
  | add_two : ∀k : ℕ, Even k → Even (k + 2)
```


This should look familiar. We have used the same syntax, except with `Type`
instead of `Prop`, for inductive types.

The above command introduces a new unary predicate `Even` as well as two
constructors, `Even.zero` and `Even.add_two`, which can be used to build proof
terms. Thanks to the "no junk" guarantee of inductive definitions, `Even.zero`
and `Even.add_two` are the only two ways to construct proofs of `Even`.

By the PAT principle, `Even` can be seen as an inductive type, the values being
the proof terms.


```lean
theorem Even_4 :
    Even 4 :=
  have Even_0 : Even 0 :=
    Even.zero
  have Even_2 : Even 2 :=
    Even.add_two _ Even_0
  show Even 4 from
    Even.add_two _ Even_2
```


Why cannot we simply define `Even` recursively? Indeed, why not?


```lean
def evenRec : ℕ → Bool
  | 0     => true
  | 1     => false
  | k + 2 => evenRec k
```


There are advantages and disadvantages to both styles.

The recursive version requires us to specify a false case (1), and it requires
us to worry about termination. On the other hand, because it is computational,
it works well with `rfl`, `simp`, `#reduce`, and `#eval`.

The inductive version is often considered more abstract and elegant. Each rule
is stated independently of the others.

Yet another way to define `Even` is as a nonrecursive definition:


```lean
def evenNonrec (k : ℕ) : Prop :=
  k % 2 = 0
```


Mathematicians would probably find this the most satisfactory definition.
But the inductive version is a convenient, intuitive example that is typical of
many realistic inductive definitions.


#### Tennis Games

Transition systems consists of transition rules, which together specify a
binary predicate connecting a "before" and an "after" state. As a simple
specimen of a transition system, we consider the possible transitions, in a game
of tennis, starting from 0–0.


```lean
inductive Score : Type where
  | vs       : ℕ → ℕ → Score
  | advServ  : Score
  | advRecv  : Score
  | gameServ : Score
  | gameRecv : Score

infixr:50 " – " => Score.vs

inductive Step : Score → Score → Prop where
  | serv_0_15     : ∀n, Step (0–n) (15–n)
  | serv_15_30    : ∀n, Step (15–n) (30–n)
  | serv_30_40    : ∀n, Step (30–n) (40–n)
  | serv_40_game  : ∀n, n < 40 → Step (40–n) Score.gameServ
  | serv_40_adv   : Step (40–40) Score.advServ
  | serv_adv_40   : Step Score.advServ (40–40)
  | serv_adv_game : Step Score.advServ Score.gameServ
  | recv_0_15     : ∀n, Step (n–0) (n–15)
  | recv_15_30    : ∀n, Step (n–15) (n–30)
  | recv_30_40    : ∀n, Step (n–30) (n–40)
  | recv_40_game  : ∀n, n < 40 → Step (n–40) Score.gameRecv
  | recv_40_adv   : Step (40–40) Score.advRecv
  | recv_adv_40   : Step Score.advRecv (40–40)
  | recv_adv_game : Step Score.advRecv Score.gameRecv

infixr:45 " ↝ " => Step
```


Note that while `Score.vs` allows arbitrary numbers as arguments, the
formulation of the constructors for `Step` ensures only valid tennis scores can
be reached from `0–0`.

We can ask, and formally answer, questions such as: Can the score ever return to
`0–0`?


```lean
theorem no_Step_to_0_0 (s : Score) :
    ¬ s ↝ 0–0 :=
  by
    intro h
    cases h
```


#### Reflexive Transitive Closure

Our last introductory example is the reflexive transitive closure of a
relation `R`, modeled as a binary predicate `Star R`.


```lean
inductive Star {α : Type} (R : α → α → Prop) : α → α → Prop
where
  | base (a b : α)    : R a b → Star R a b
  | refl (a : α)      : Star R a a
  | trans (a b c : α) : Star R a b → Star R b c → Star R a c
```


The first rule embeds `R` into `Star R`. The second rule achieves the
reflexive closure. The third rule achieves the transitive closure.

The definition is truly elegant. If you doubt this, try implementing `Star` as a
recursive function:


```lean
def starRec {α : Type} (R : α → α → Bool) :
  α → α → Bool :=
  sorry
```


#### A Nonexample

Not all inductive definitions are legal.

-- fails
inductive Illegal : Prop where
  | intro : ¬ Illegal → Illegal

### Logical Symbols

The truth values `False` and `True`, the connectives `∧`, `∨` and `↔`, the
`∃` quantifier, and the equality predicate `=` are all defined as inductive
propositions or predicates. In contrast, `∀` and `→` are built into the logic.

Syntactic sugar:

    `∃x : α, P` := `Exists (λx : α, P)`
    `x = y`     := `Eq x y`


```lean
namespace logical_symbols

inductive And (a b : Prop) : Prop where
  | intro : a → b → And a b

inductive Or (a b : Prop) : Prop where
  | inl : a → Or a b
  | inr : b → Or a b

inductive Iff (a b : Prop) : Prop where
  | intro : (a → b) → (b → a) → Iff a b

inductive Exists {α : Type} (P : α → Prop) : Prop where
  | intro : ∀a : α, P a → Exists P

inductive True : Prop where
  | intro : True

inductive False : Prop where

inductive Eq {α : Type} : α → α → Prop where
  | refl : ∀a : α, Eq a a

end logical_symbols

#print And
#print Or
#print Iff
#print Exists
#print True
#print False
#print Eq
```


### Rule Induction

Just as we can perform induction on a term, we can perform induction on a proof
term.

This is called __rule induction__, because the induction is on the introduction
rules (i.e., the constructors of the proof term). Thanks to the PAT principle,
this works as expected.


```lean
theorem mod_two_Eq_zero_of_Even (n : ℕ) (h : Even n) :
    n % 2 = 0 :=
  by
    induction h with
    | zero            => rfl
    | add_two k hk ih => simp [ih]

theorem Not_Even_two_mul_add_one (m n : ℕ)
      (hm : m = 2 * n + 1) :
    ¬ Even m :=
  by
    intro h
    induction h generalizing n with
    | zero            => linarith
    | add_two k hk ih =>
      apply ih (n - 1)
      cases n with
      | zero    => simp [Nat.ctor_eq_zero] at *
      | succ n' =>
        simp [Nat.succ_eq_add_one] at *
        linarith
```


`linarith` proves goals involving linear arithmetic equalities or
inequalities. "Linear" means it works only with `+` and `-`, not `*` and `/`
(but multiplication by a constant is supported).


```lean
theorem linarith_example (i : Int) (hi : i > 5) :
    2 * i + 3 > 11 :=
  by linarith

theorem Star_Star_Iff_Star {α : Type} (R : α → α → Prop)
      (a b : α) :
    Star (Star R) a b ↔ Star R a b :=
  by
    apply Iff.intro
    { intro h
      induction h with
      | base a b hab                  => exact hab
      | refl a                        => apply Star.refl
      | trans a b c hab hbc ihab ihbc =>
        apply Star.trans a b
        { exact ihab }
        { exact ihbc } }
    { intro h
      apply Star.base
      exact h }

@[simp] theorem Star_Star_Eq_Star {α : Type}
      (R : α → α → Prop) :
    Star (Star R) = Star R :=
  by
    apply funext
    intro a
    apply funext
    intro b
    apply propext
    apply Star_Star_Iff_Star

#check funext
#check propext
```


### Elimination

Given an inductive predicate `P`, its introduction rules typically have the form
`∀…, ⋯ → P …` and can be used to prove goals of the form `⊢ P …`.

Elimination works the other way around: It extracts information from a theorem
or hypothesis of the form `P …`. Elimination takes various forms: pattern
matching, the `cases` and `induction` tactics, and custom elimination rules
(e.g., `And.left`).

* `cases` works like `induction` but without induction hypothesis.

* `match` is available as well.

Now we can finally understand how `cases h` where `h : l = r` and how
`cases Classical.em h` work.


```lean
#print Eq

theorem cases_Eq_example {α : Type} (l r : α) (h : l = r)
      (P : α → α → Prop) :
    P l r :=
  by
    cases h
    sorry

#check Classical.em
#print Or

theorem cases_Classical_em_example {α : Type} (a : α)
      (P Q : α → Prop) :
    Q a :=
  by
    have hor : P a ∨ ¬ P a :=
      Classical.em (P a)
    cases hor with
    | inl hl => sorry
    | inr hr => sorry
```


Often it is convenient to rewrite concrete terms of the form `P (c …)`,
where `c` is typically a constructor. We can state and prove an
__inversion rule__ to support such eliminative reasoning.

Typical inversion rule:

    `∀x y, P (c x y) → (∃…, ⋯ ∧ ⋯) ∨ ⋯ ∨ (∃…, ⋯ ∧ ⋯)`

It can be useful to combine introduction and elimination into a single theorem,
which can be used for rewriting both the hypotheses and conclusions of goals:

    `∀x y, P (c x y) ↔ (∃…, ⋯ ∧ ⋯) ∨ ⋯ ∨ (∃…, ⋯ ∧ ⋯)`


```lean
theorem Even_Iff (n : ℕ) :
    Even n ↔ n = 0 ∨ (∃m : ℕ, n = m + 2 ∧ Even m) :=
  by
    apply Iff.intro
    { intro hn
      cases hn with
      | zero         => simp
      | add_two k hk =>
        apply Or.inr
        apply Exists.intro k
        simp [hk] }
    { intro hor
      cases hor with
      | inl heq => simp [heq, Even.zero]
      | inr hex =>
        cases hex with
        | intro k hand =>
          cases hand with
          | intro heq hk =>
            simp [heq, Even.add_two _ hk] }

theorem Even_Iff_struct (n : ℕ) :
    Even n ↔ n = 0 ∨ (∃m : ℕ, n = m + 2 ∧ Even m) :=
  Iff.intro
    (assume hn : Even n
     match n, hn with
     | _, Even.zero         =>
       show 0 = 0 ∨ _ from
         by simp
     | _, Even.add_two k hk =>
       show _ ∨ (∃m, k + 2 = m + 2 ∧ Even m) from
         Or.inr (Exists.intro k (by simp [*])))
    (assume hor : n = 0 ∨ (∃m, n = m + 2 ∧ Even m)
     match hor with
     | Or.inl heq =>
       show Even n from
         by simp [heq, Even.zero]
     | Or.inr hex =>
       match hex with
       | Exists.intro m hand =>
         match hand with
         | And.intro heq hm =>
           show Even n from
             by simp [heq, Even.add_two _ hm])
```


### Further Examples

#### Sorted Lists


```lean
inductive Sorted : List ℕ → Prop where
  | nil : Sorted []
  | single (x : ℕ) : Sorted [x]
  | two_or_more (x y : ℕ) {zs : List ℕ} (hle : x ≤ y)
      (hsorted : Sorted (y :: zs)) :
    Sorted (x :: y :: zs)

theorem Sorted_nil :
    Sorted [] :=
  Sorted.nil

theorem Sorted_2 :
    Sorted [2] :=
  Sorted.single _

theorem Sorted_3_5 :
    Sorted [3, 5] :=
  by
    apply Sorted.two_or_more
    { simp }
    { exact Sorted.single _ }

theorem Sorted_3_5_raw :
    Sorted [3, 5] :=
  Sorted.two_or_more _ _ (by simp) (Sorted.single _)

theorem sorted_7_9_9_11 :
    Sorted [7, 9, 9, 11] :=
  Sorted.two_or_more _ _ (by simp)
    (Sorted.two_or_more _ _ (by simp)
       (Sorted.two_or_more _ _ (by simp)
          (Sorted.single _)))

theorem Not_Sorted_17_13 :
    ¬ Sorted [17, 13] :=
  by
    intro h
    cases h with
    | two_or_more _ _ hlet hsorted => simp at hlet
```


#### Palindromes


```lean
inductive Palindrome {α : Type} : List α → Prop where
  | nil : Palindrome []
  | single (x : α) : Palindrome [x]
  | sandwich (x : α) (xs : List α) (hxs : Palindrome xs) :
    Palindrome ([x] ++ xs ++ [x])
```


-- fails
def palindromeRec {α : Type} : List α → Bool
  | []                 => true
  | [_]                => true
  | ([x] ++ xs ++ [x]) => palindromeRec xs
  | _                  => false


```lean
theorem Palindrome_aa {α : Type} (a : α) :
    Palindrome [a, a] :=
  Palindrome.sandwich a _ Palindrome.nil

theorem Palindrome_aba {α : Type} (a b : α) :
    Palindrome [a, b, a] :=
  Palindrome.sandwich a _ (Palindrome.single b)

theorem Palindrome_reverse {α : Type} (xs : List α)
      (hxs : Palindrome xs) :
    Palindrome (reverse xs) :=
  by
    induction hxs with
    | nil                  => exact Palindrome.nil
    | single x             => exact Palindrome.single x
    | sandwich x xs hxs ih =>
      { simp [reverse, reverse_append]
        exact Palindrome.sandwich _ _ ih }
```


#### Full Binary Trees


```lean
#check Tree

inductive IsFull {α : Type} : Tree α → Prop where
  | nil : IsFull Tree.nil
  | node (a : α) (l r : Tree α)
      (hl : IsFull l) (hr : IsFull r)
      (hiff : l = Tree.nil ↔ r = Tree.nil) :
    IsFull (Tree.node a l r)

theorem IsFull_singleton {α : Type} (a : α) :
    IsFull (Tree.node a Tree.nil Tree.nil) :=
  by
    apply IsFull.node
    { exact IsFull.nil }
    { exact IsFull.nil }
    { rfl }

theorem IsFull_mirror {α : Type} (t : Tree α)
      (ht : IsFull t) :
    IsFull (mirror t) :=
  by
    induction ht with
    | nil                             => exact IsFull.nil
    | node a l r hl hr hiff ih_l ih_r =>
      { rw [mirror]
        apply IsFull.node
        { exact ih_r }
        { exact ih_l }
        { simp [mirror_Eq_nil_Iff, *] } }

theorem IsFull_mirror_struct_induct {α : Type} (t : Tree α) :
    IsFull t → IsFull (mirror t) :=
  by
    induction t with
    | nil                  =>
      { intro ht
        exact ht }
    | node a l r ih_l ih_r =>
      { intro ht
        cases ht with
        | node _ _ _ hl hr hiff =>
          { rw [mirror]
            apply IsFull.node
            { exact ih_r hr }
            { apply ih_l hl }
            { simp [mirror_Eq_nil_Iff, *] } } }
```


#### First-Order Terms


```lean
inductive Term (α β : Type) : Type where
  | var : β → Term α β
  | fn  : α → List (Term α β) → Term α β

inductive WellFormed {α β : Type} (arity : α → ℕ) :
  Term α β → Prop where
  | var (x : β) : WellFormed arity (Term.var x)
  | fn (f : α) (ts : List (Term α β))
      (hargs : ∀t ∈ ts, WellFormed arity t)
      (hlen : length ts = arity f) :
    WellFormed arity (Term.fn f ts)

inductive VariableFree {α β : Type} : Term α β → Prop where
  | fn (f : α) (ts : List (Term α β))
      (hargs : ∀t ∈ ts, VariableFree t) :
    VariableFree (Term.fn f ts)

end LoVe
```

---



## Inductive Predicates (ExerciseSheet) {#hhg-inductive-predicates-exercisesheet}

> 📄 Source: https://raw.githubusercontent.com/lean-forward/logical_verification_2025/main/lean/LoVe/LoVe06_InductivePredicates_ExerciseSheet.lean

```lean
import LoVe.LoVe06_InductivePredicates_Demo
```


## LoVe Exercise 6: Inductive Predicates

Replace the placeholders (e.g., `:= sorry`) with your solutions.


```lean
set_option autoImplicit false
set_option tactic.hygienic false

namespace LoVe
```


### Question 1: Even and Odd

The `Even` predicate is `True` for even numbers and `False` for odd numbers.


```lean
#check Even
```


We define `Odd` as the negation of `Even`:


```lean
def Odd (n : ℕ) : Prop :=
  ¬ Even n
```


1.1. Prove that 1 is odd and register this fact as a simp rule.

Hint: `cases` or `induction` is useful to reason about hypotheses of the form
`Even …`.


```lean
@[simp] theorem Odd_1 :
    Odd 1 :=
  sorry
```


1.2. Prove that 3 and 5 are odd.


```lean
-- enter your answer here
```


1.3. Complete the following proof by structural induction.


```lean
theorem Even_two_times :
    ∀m : ℕ, Even (2 * m)
  | 0     => Even.zero
  | m + 1 =>
    sorry
```


### Question 2: Tennis Games

Recall the inductive type of tennis scores from the demo:


```lean
#check Score
```


2.1. Define an inductive predicate that returns `True` if the server is
ahead of the receiver and that returns `False` otherwise.


```lean
inductive ServAhead : Score → Prop
  -- enter the missing cases here
```


2.2. Validate your predicate definition by proving the following theorems.


```lean
theorem ServAhead_vs {m n : ℕ} (hgt : m > n) :
    ServAhead (Score.vs m n) :=
  sorry

theorem ServAhead_advServ :
    ServAhead Score.advServ :=
  sorry

theorem not_ServAhead_advRecv :
    ¬ ServAhead Score.advRecv :=
  sorry

theorem ServAhead_gameServ :
    ServAhead Score.gameServ :=
  sorry

theorem not_ServAhead_gameRecv :
    ¬ ServAhead Score.gameRecv :=
  sorry
```


2.3. Compare the above theorem statements with your definition. What do you
observe?


```lean
-- enter your answer here
```


### Question 3: Binary Trees

3.1. Prove the converse of `IsFull_mirror`. You may exploit already proved
theorems (e.g., `IsFull_mirror`, `mirror_mirror`).


```lean
#check IsFull_mirror
#check mirror_mirror

theorem mirror_IsFull {α : Type} :
    ∀t : Tree α, IsFull (mirror t) → IsFull t :=
  sorry
```


3.2. Define a `map` function on binary trees, similar to `List.map`.


```lean
def Tree.map {α β : Type} (f : α → β) : Tree α → Tree β :=
  sorry
```


3.3. Prove the following theorem by case distinction.


```lean
theorem Tree.map_eq_empty_iff {α β : Type} (f : α → β) :
    ∀t : Tree α, Tree.map f t = Tree.nil ↔ t = Tree.nil :=
  sorry
```


3.4 (**optional**). Prove the following theorem by rule induction.


```lean
theorem map_mirror {α β : Type} (f : α → β) :
    ∀t : Tree α, IsFull t → IsFull (Tree.map f t) :=
  sorry

end LoVe
```

---



## Inductive Predicates (HomeworkSheet) {#hhg-inductive-predicates-homeworksheet}

> 📄 Source: https://raw.githubusercontent.com/lean-forward/logical_verification_2025/main/lean/LoVe/LoVe06_InductivePredicates_HomeworkSheet.lean

```lean
import LoVe.LoVelib
```


## LoVe Homework 6 (10 points): Inductive Predicates

Replace the placeholders (e.g., `:= sorry`) with your solutions.


```lean
set_option autoImplicit false
set_option tactic.hygienic false

namespace LoVe
```


### Question 1 (4 points): A Type of Terms

Recall the type of terms from question 3 of exercise 5:


```lean
inductive Term : Type
  | var : String → Term
  | lam : String → Term → Term
  | app : Term → Term → Term
```


1.1 (2 points). Define an inductive predicate `IsLam` that returns `True` if
its argument is of the form `Term.lam …` and that returns `False` otherwise.


```lean
-- enter your definition here
```


1.2 (2 points). Validate your answer to question 1.1 by proving the following
theorems:


```lean
theorem IsLam_lam (s : String) (t : Term) :
    IsLam (Term.lam s t) :=
  sorry

theorem not_IsLamVar (s : String) :
    ¬ IsLam (Term.var s) :=
  sorry

theorem not_IsLam_app (t u : Term) :
    ¬ IsLam (Term.app t u) :=
  sorry
```


### Question 2 (6 points): Transitive Closure

In mathematics, the transitive closure `R⁺` of a binary relation `R` over a
set `A` can be defined as the smallest solution satisfying the following rules:

    (base) for all `a, b ∈ A`, if `a R b`, then `a R⁺ b`;
    (step) for all `a, b, c ∈ A`, if `a R b` and `b R⁺ c`, then `a R⁺ c`.

In Lean, we can define this notion as follows, by identifying the set `A` with
the type `α`:


```lean
inductive TCV1 {α : Type} (R : α → α → Prop) : α → α → Prop
  | base (a b : α)   : R a b → TCV1 R a b
  | step (a b c : α) : R a b → TCV1 R b c → TCV1 R a c
```


2.1 (2 points). Rule `(step)` makes it convenient to extend transitive chains
by adding links to the left. Another way to define the transitive closure `R⁺`
would use replace `(step)` with the following right-leaning rule:

    (pets) for all `a, b, c ∈ A`, if `a R⁺ b` and `b R c`, then `a R⁺ c`.

Define a predicate `TCV2` that embodies this alternative definition.


```lean
-- enter your definition here
```


2.2 (2 points). Yet another definition of the transitive closure `R⁺` would
use the following symmetric rule instead of `(step)` or `(pets)`:

    (trans) for all `a, b, c ∈ A`, if `a R⁺ b` and `b R⁺ c`, then `a R⁺ c`.

Define a predicate `TCV3` that embodies this alternative definition.


```lean
-- enter your definition here
```


2.3 (1 point). Prove that `(step)` also holds as a theorem about `TCV3`.


```lean
theorem TCV3_step {α : Type} (R : α → α → Prop) (a b c : α) (rab : R a b)
      (tbc : TCV3 R b c) :
    TCV3 R a c :=
  sorry
```


2.4 (1 point). Prove the following theorem by rule induction:


```lean
theorem TCV1_pets {α : Type} (R : α → α → Prop) (c : α) :
    ∀a b, TCV1 R a b → R b c → TCV1 R a c :=
  sorry

end LoVe
```

---



## Effectful Programming (Demo) {#hhg-effectful-programming-demo}

> 📄 Source: https://raw.githubusercontent.com/lean-forward/logical_verification_2025/main/lean/LoVe/LoVe07_EffectfulProgramming_Demo.lean

```lean
import LoVe.LoVelib
```


## LoVe Demo 7: Effectful Programming

Monads are an important functional programming abstraction. They generalize
computation with side effects, offering effectful programming in a pure
functional programming language. Haskell has shown that they can be used very
successfully to write imperative programs. For us, they are interesting in their
own right and for two more reasons:

* They provide a nice example of axiomatic reasoning.

* They are needed for programming Lean itself (metaprogramming, lecture 8).


```lean
set_option autoImplicit false
set_option tactic.hygienic false

namespace LoVe
```


### Introductory Example

Consider the following programming task:

    Implement a function `sum257 ns` that sums up the second, fifth, and
    seventh items of a list `ns` of natural numbers. Use `Option ℕ` for the
    result so that if the list has fewer than seven elements, you can return
    `Option.none`.

A straightforward solution follows:


```lean
def nth {α : Type} : List α → Nat → Option α
  | [],      _     => Option.none
  | x :: _,  0     => Option.some x
  | _ :: xs, n + 1 => nth xs n

def sum257 (ns : List ℕ) : Option ℕ :=
  match nth ns 1 with
  | Option.none    => Option.none
  | Option.some n₂ =>
    match nth ns 4 with
    | Option.none    => Option.none
    | Option.some n₅ =>
      match nth ns 6 with
      | Option.none    => Option.none
      | Option.some n₇ => Option.some (n₂ + n₅ + n₇)
```


The code is ugly, because of all the pattern matching on options.

We can put all the ugliness in one function, which we call `connect`:


```lean
def connect {α : Type} {β : Type} :
  Option α → (α → Option β) → Option β
  | Option.none,   _ => Option.none
  | Option.some a, f => f a

def sum257Connect (ns : List ℕ) : Option ℕ :=
  connect (nth ns 1)
    (fun n₂ ↦ connect (nth ns 4)
       (fun n₅ ↦ connect (nth ns 6)
          (fun n₇ ↦ Option.some (n₂ + n₅ + n₇))))
```


Instead of defining `connect` ourselves, we can use Lean's predefined
general `bind` operation. We can also use `pure` instead of `Option.some`:


```lean
#check bind

def sum257Bind (ns : List ℕ) : Option ℕ :=
  bind (nth ns 1)
    (fun n₂ ↦ bind (nth ns 4)
       (fun n₅ ↦ bind (nth ns 6)
          (fun n₇ ↦ pure (n₂ + n₅ + n₇))))
```


By using `bind` and `pure`, `sum257Bind` makes no reference to the
constructors `Option.none` and `Option.some`.

Syntactic sugar:

    `ma >>= f` := `bind ma f`


```lean
def sum257Op (ns : List ℕ) : Option ℕ :=
  nth ns 1 >>=
    fun n₂ ↦ nth ns 4 >>=
      fun n₅ ↦ nth ns 6 >>=
        fun n₇ ↦ pure (n₂ + n₅ + n₇)
```


Syntactic sugar:

   do
     let a ← ma
     t
   :=
   ma >>= (fun a ↦ t)

   do
     ma
     t
   :=
   ma >>= (fun _ ↦ t)


```lean
def sum257Dos (ns : List ℕ) : Option ℕ :=
  do
    let n₂ ← nth ns 1
    do
      let n₅ ← nth ns 4
      do
        let n₇ ← nth ns 6
        pure (n₂ + n₅ + n₇)
```


The `do`s can be combined:


```lean
def sum257Do (ns : List ℕ) : Option ℕ :=
  do
    let n₂ ← nth ns 1
    let n₅ ← nth ns 4
    let n₇ ← nth ns 6
    pure (n₂ + n₅ + n₇)
```


Although the notation has an imperative flavor, the function is a pure
functional program.


### Two Operations and Three Laws

The `Option` type constructor is an example of a monad.

In general, a __monad__ is a type constructor `m` that depends on some type
parameter `α` (i.e., `m α`) equipped with two distinguished operations:

    `pure {α : Type} : α → m α`
    `bind {α β : Type} : m α → (α → m β) → m β`

For `Option`:

    `pure` := `Option.some`
    `bind` := `connect`

Intuitively, we can think of a monad as a "box":

* `pure` puts the data into the box.

* `bind` allows us to access the data in the box and modify it (possibly even
  changing its type, since the result is an `m β` monad, not a `m α` monad).

There is no general way to extract the data from the monad, i.e., to obtain an
`α` from an `m α`.

To summarize, `pure a` provides no side effect and simply provides a box
containing the the value `a`, whereas `bind ma f` (also written `ma >>= f`)
executes `ma`, then executes `f` with the boxed result `a` of `ma`.

The option monad is only one instance among many.

Type                 | Effect
-------------------- | -------------------------------------------------------
`id`                 | no effects
`Option`             | simple exceptions
`fun α ↦ σ → α × σ`  | threading through a state of type `σ`
`Set`                | nondeterministic computation returning `α` values
`fun α ↦ t → α`      | reading elements of type `t` (e.g., a configuration)
`fun α ↦ ℕ × α`      | adjoining running time (e.g., to model time complexity)
`fun α ↦ String × α` | adjoining text output (e.g., for logging)
`IO`                 | interaction with the operating system
`TacticM`            | interaction with the proof assistant

All of the above are unary type constructors `m : Type → Type`.

Some effects can be combined (e.g., `Option (t → α)`).

Some effects are not executable (e.g., `Set α`). They are nonetheless useful for
modeling programs abstractly in the logic.

Specific monads may provide a way to extract the boxed value stored in the monad
without `bind`'s requirement of putting it back in a monad.

Monads have several benefits, including:

* They provide the convenient and highly readable `do` notation.

* They support generic operations, such as
  `mmap {α β : Type} : (α → m β) → List α → m (List β)`, which work uniformly
  across all monads.

The `bind` and `pure` operations are normally required to obey three laws. Pure
data as the first program can be simplified away:

    do
      let a' ← pure a,
      f a'
  =
    f a

Pure data as the second program can be simplified away:

    do
      let a ← ma
      pure a
  =
    ma

Nested programs `ma`, `f`, `g` can be flattened using this associativity rule:

    do
      let b ←
        do
          let a ← ma
          f a
      g b
  =
    do
      let a ← ma
      let b ← f a
      g b


### A Type Class of Monads

Monads are a mathematical structure, so we use class to add them as a type
class. We can think of a type class as a structure that is parameterized by a
type, or here, by a type constructor `m : Type → Type`.


```lean
class LawfulMonad (m : Type → Type)
  extends Pure m, Bind m where
  pure_bind {α β : Type} (a : α) (f : α → m β) :
    (pure a >>= f) = f a
  bind_pure {α : Type} (ma : m α) :
    (ma >>= pure) = ma
  bind_assoc {α β γ : Type} (f : α → m β) (g : β → m γ)
      (ma : m α) :
    ((ma >>= f) >>= g) = (ma >>= (fun a ↦ f a >>= g))
```


Step by step:

* We are creating a structure parameterized by a unary type constructor `m`.

* The structure inherits the fields, and any syntactic sugar, from structures
  called `Bind` and `Pure`, which provide the `bind` and `pure` operations on
  `m` and some syntactic sugar.

* The definition adds three fields to those already provided by `Bind` and
  `Pure`, to store the proofs of the laws.

To instantiate this definition with a concrete monad, we must supply the type
constructor `m` (e.g., `Option`), `bind` and `pure` operators, and proofs of the
laws.


### No Effects

Our first monad is the trivial monad `m := id` (i.e., `m := (fun α ↦ α)`).


```lean
def id.pure {α : Type} : α → id α
  | a => a

def id.bind {α β : Type} : id α → (α → id β) → id β
  | a, f => f a

instance id.LawfulMonad : LawfulMonad id :=
  { pure       := id.pure
    bind       := id.bind
    pure_bind  :=
      by
        intro α β a f
        rfl
    bind_pure  :=
      by
        intro α ma
        rfl
    bind_assoc :=
      by
        intro α β γ f g ma
        rfl }
```


### Basic Exceptions

As we saw above, the option type provides a basic exception mechanism.


```lean
def Option.pure {α : Type} : α → Option α :=
  Option.some

def Option.bind {α β : Type} :
  Option α → (α → Option β) → Option β
  | Option.none,   _ => Option.none
  | Option.some a, f => f a

instance Option.LawfulMonad : LawfulMonad Option :=
  { pure       := Option.pure
    bind       := Option.bind
    pure_bind  :=
      by
        intro α β a f
        rfl
    bind_pure  :=
      by
        intro α ma
        cases ma with
        | none   => rfl
        | some _ => rfl
    bind_assoc :=
      by
        intro α β γ f g ma
        cases ma with
        | none   => rfl
        | some _ => rfl }

def Option.throw {α : Type} : Option α :=
  Option.none

def Option.catch {α : Type} : Option α → Option α → Option α
  | Option.none,   ma' => ma'
  | Option.some a, _   => Option.some a
```


### Mutable State

The state monad provides an abstraction corresponding to a mutable state. Some
compilers recognize the state monad to produce efficient imperative code.


```lean
def Action (σ α : Type) : Type :=
  σ → α × σ

def Action.read {σ : Type} : Action σ σ
  | s => (s, s)

def Action.write {σ : Type} (s : σ) : Action σ Unit
  | _ => ((), s)

def Action.pure {σ α : Type} (a : α) : Action σ α
  | s => (a, s)

def Action.bind {σ : Type} {α β : Type} (ma : Action σ α)
      (f : α → Action σ β) :
    Action σ β
  | s =>
    match ma s with
    | (a, s') => f a s'
```


`Action.pure` is like a `return` statement; it does not change the state.

`Action.bind` is like the sequential composition of two statements with
respect to a state.


```lean
instance Action.LawfulMonad {σ : Type} :
  LawfulMonad (Action σ) :=
  { pure       := Action.pure
    bind       := Action.bind
    pure_bind  :=
      by
        intro α β a f
        rfl
    bind_pure  :=
      by
        intro α ma
        rfl
    bind_assoc :=
      by
        intro α β γ f g ma
        rfl }

def increasingly : List ℕ → Action ℕ (List ℕ)
  | []        => pure []
  | (n :: ns) =>
    do
      let prev ← Action.read
      if n < prev then
        increasingly ns
      else
        do
          Action.write n
          let ns' ← increasingly ns
          pure (n :: ns')

#eval increasingly [1, 2, 3, 2] 0
#eval increasingly [1, 2, 3, 2, 4, 5, 2] 0
```


### Nondeterminism

The set monad stores an arbitrary, possibly infinite number of `α` values.


```lean
#check Set

def Set.pure {α : Type} : α → Set α
  | a => {a}

def Set.bind {α β : Type} : Set α → (α → Set β) → Set β
  | A, f => {b | ∃a, a ∈ A ∧ b ∈ f a}

instance Set.LawfulMonad : LawfulMonad Set :=
  { pure       := Set.pure
    bind       := Set.bind
    pure_bind  :=
      by
        intro α β a f
        simp [Pure.pure, Bind.bind, Set.pure, Set.bind]
    bind_pure  :=
      by
        intro α ma
        simp [Pure.pure, Bind.bind, Set.pure, Set.bind]
    bind_assoc :=
      by
        intro α β γ f g ma
        simp [Pure.pure, Bind.bind, Set.pure, Set.bind]
        apply Set.ext
        aesop }
```


`aesop` is a general-purpose proof search tactic. Among others, it performs
elimination of the logical symbols `∧`, `∨`, `↔`, and `∃` in hypotheses and
introduction of `∧`, `↔`, and `∃` in the target, and it regularly invokes the
simplifier. It can succeed at proving a goal, fail, or succeed partially,
leaving some unfinished subgoals to the user.


### A Generic Algorithm: Iteration over a List

We consider a generic effectful program `mmap` that iterates over a list and
applies a function `f` to each element.


```lean
def nthsFine {α : Type} (xss : List (List α)) (n : ℕ) :
  List (Option α) :=
  List.map (fun xs ↦ nth xs n) xss

#eval nthsFine [[11, 12, 13, 14], [21, 22, 23]] 2
#eval nthsFine [[11, 12, 13, 14], [21, 22, 23]] 3

def mmap {m : Type → Type} [LawfulMonad m] {α β : Type}
    (f : α → m β) :
  List α → m (List β)
  | []      => pure []
  | a :: as =>
    do
      let b ← f a
      let bs ← mmap f as
      pure (b :: bs)

def nthsCoarse {α : Type} (xss : List (List α)) (n : ℕ) :
  Option (List α) :=
  mmap (fun xs ↦ nth xs n) xss

#eval nthsCoarse [[11, 12, 13, 14], [21, 22, 23]] 2
#eval nthsCoarse [[11, 12, 13, 14], [21, 22, 23]] 3

theorem mmap_append {m : Type → Type} [LawfulMonad m]
      {α β : Type} (f : α → m β) :
    ∀as as' : List α, mmap f (as ++ as') =
      do
        let bs ← mmap f as
        let bs' ← mmap f as'
        pure (bs ++ bs')
  | [],      _   =>
    by simp [mmap, LawfulMonad.bind_pure, LawfulMonad.pure_bind]
  | a :: as, as' =>
    by simp [mmap, mmap_append _ as as', LawfulMonad.pure_bind,
      LawfulMonad.bind_assoc]

end LoVe
```

---



## Effectful Programming (ExerciseSheet) {#hhg-effectful-programming-exercisesheet}

> 📄 Source: https://raw.githubusercontent.com/lean-forward/logical_verification_2025/main/lean/LoVe/LoVe07_EffectfulProgramming_ExerciseSheet.lean

```lean
import LoVe.LoVe07_EffectfulProgramming_Demo
```


## LoVe Exercise 7: Effectful Programming

Replace the placeholders (e.g., `:= sorry`) with your solutions.


```lean
set_option autoImplicit false
set_option tactic.hygienic false

namespace LoVe
```


### Question 1: A State Monad with Failure

We introduce a richer notion of lawful monad that provides an `orelse`
operator satisfying some laws, given below. `emp` denotes failure. `orelse x y`
tries `x` first, falling back on `y` on failure.


```lean
class LawfulMonadWithOrelse (m : Type → Type)
  extends LawfulMonad m where
  emp {α : Type} : m α
  orelse {α : Type} : m α → m α → m α
  emp_orelse {α : Type} (a : m α) :
    orelse emp a = a
  orelse_emp {α : Type} (a : m α) :
    orelse a emp = a
  orelse_assoc {α : Type} (a b c : m α) :
    orelse (orelse a b) c = orelse a (orelse b c)
  emp_bind {α β : Type} (f : α → m β) :
    (emp >>= f) = emp
  bind_emp {α β : Type} (f : m α) :
    (f >>= (fun a ↦ (emp : m β))) = emp
```


1.1. We set up the `Option` type constructor to be a
`LawfulMonad_with_orelse`. Complete the proofs.

Hint: Use `simp [Bind.bind]` to unfold the definition of the bind operator and
`simp [Option.orelse]` to unfold the definition of the `orelse` operator.


```lean
def Option.orelse {α : Type} : Option α → Option α → Option α
  | Option.none,   ma' => ma'
  | Option.some a, _   => Option.some a

instance Option.LawfulMonadWithOrelse :
  LawfulMonadWithOrelse Option :=
  { Option.LawfulMonad with
    emp          := Option.none
    orelse       := Option.orelse
    emp_orelse   :=
      sorry
    orelse_emp   :=
      by
        intro α ma
        simp [Option.orelse]
        cases ma
        { rfl }
        { rfl }
    orelse_assoc :=
      sorry
    emp_bind     :=
      by
        intro α β f
        simp [Bind.bind]
        rfl
    bind_emp     :=
      sorry
  }

@[simp] theorem Option.some_bind {α β : Type} (a : α) (g : α → Option β) :
    (Option.some a >>= g) = g a :=
  sorry
```


1.2. Now we are ready to define `FAction σ`: a monad with an internal state
of type `σ` that can fail (unlike `Action σ`).

We start with defining `FAction σ α`, where `σ` is the type of the internal
state, and `α` is the type of the value stored in the monad. We use `Option` to
model failure. This means we can also use the monad operations of `Option` when
defining the monad operations on `FAction`.

Hints:

* Remember that `FAction σ α` is an alias for a function type, so you can use
  pattern matching and `fun s ↦ …` to define values of type `FAction σ α`.

* `FAction` is very similar to `Action` from the lecture's demo. You can look
  there for inspiration.


```lean
def FAction (σ : Type) (α : Type) : Type :=
  sorry
```


1.3. Define the `get` and `set` function for `FAction`, where `get` returns
the state passed along the state monad and `set s` changes the state to `s`.


```lean
def get {σ : Type} : FAction σ σ :=
  sorry

def set {σ : Type} (s : σ) : FAction σ Unit :=
  sorry
```


We set up the `>>=` syntax on `FAction`:


```lean
def FAction.bind {σ α β : Type} (f : FAction σ α) (g : α → FAction σ β) :
  FAction σ β
  | s => f s >>= (fun (a, s) ↦ g a s)

instance FAction.Bind {σ : Type} : Bind (FAction σ) :=
  { bind := FAction.bind }

theorem FAction.bind_apply {σ α β : Type} (f : FAction σ α)
      (g : α → FAction σ β) (s : σ) :
    (f >>= g) s = (f s >>= (fun as ↦ g (Prod.fst as) (Prod.snd as))) :=
  by rfl
```


1.4. Define the operator `pure` for `FAction`, in such a way that it will
satisfy the three laws.


```lean
def FAction.pure {σ α : Type} (a : α) : FAction σ α :=
  sorry
```


We set up the syntax for `pure` on `FAction`:


```lean
instance FAction.Pure {σ : Type} : Pure (FAction σ) :=
  { pure := FAction.pure }

theorem FAction.pure_apply {σ α : Type} (a : α) (s : σ) :
    (pure a : FAction σ α) s = Option.some (a, s) :=
  by rfl
```


1.5. Register `FAction` as a monad.

Hints:

* The `funext` theorem is useful when you need to prove equality between two
  functions.

* The theorem `FAction.pure_apply` or `FAction.bind_apply` might prove useful.


```lean
instance FAction.LawfulMonad {σ : Type} : LawfulMonad (FAction σ) :=
  { FAction.Bind, FAction.Pure with
    pure_bind :=
      by
      sorry
    bind_pure :=
      by
        intro α ma
        apply funext
        intro s
        have bind_pure_helper :
          (do
             let x ← ma s
             pure (Prod.fst x) (Prod.snd x)) =
          ma s :=
          by apply LawfulMonad.bind_pure
        aesop
    bind_assoc :=
      sorry
  }
```


### Question 2 (**optional**): Kleisli Operator

The Kleisli operator `>=>` (not to be confused with `>>=`) is useful for
pipelining effectful functions. Note that `fun a ↦ f a >>= g` is to be parsed as
`fun a ↦ (f a >>= g)`, not as `(fun a ↦ f a) >>= g`.


```lean
def kleisli {m : Type → Type} [LawfulMonad m] {α β γ : Type} (f : α → m β)
    (g : β → m γ) : α → m γ :=
  fun a ↦ f a >>= g

infixr:90 (priority := high) " >=> " => kleisli
```


2.1 (**optional**). Prove that `pure` is a left and right unit for the
Kleisli operator.


```lean
theorem pure_kleisli {m : Type → Type} [LawfulMonad m] {α β : Type}
      (f : α → m β) :
    (pure >=> f) = f :=
  sorry

theorem kleisli_pure {m : Type → Type} [LawfulMonad m] {α β : Type}
      (f : α → m β) :
    (f >=> pure) = f :=
  sorry
```


2.2 (**optional**). Prove that the Kleisli operator is associative.


```lean
theorem kleisli_assoc {m : Type → Type} [LawfulMonad m] {α β γ δ : Type}
      (f : α → m β) (g : β → m γ) (h : γ → m δ) :
    ((f >=> g) >=> h) = (f >=> (g >=> h)) :=
  sorry

end LoVe
```

---



## Effectful Programming (HomeworkSheet) {#hhg-effectful-programming-homeworksheet}

> 📄 Source: https://raw.githubusercontent.com/lean-forward/logical_verification_2025/main/lean/LoVe/LoVe07_EffectfulProgramming_HomeworkSheet.lean

```lean
import LoVe.LoVe07_EffectfulProgramming_Demo
```


## LoVe Homework 7 (10 points + 1 bonus point): Monads

Replace the placeholders (e.g., `:= sorry`) with your solutions.


```lean
set_option autoImplicit false
set_option tactic.hygienic false

namespace LoVe
```


### Question 1 (5 points): `map` for Monads

We will define a `map` function for monads and derive its so-called functorial
properties from the three laws.

1.1 (2 points). Define `map` on `m`. This function should not be confused
with `mmap` from the lecture's demo.

Hint: The challenge is to find a way to create a value of type `m β`. Follow the
types. Inventory all the arguments and operations available (e.g., `pure`,
`>>=`) with their types and see if you can plug them together like Lego
bricks.


```lean
def map {m : Type → Type} [LawfulMonad m] {α β : Type} (f : α → β) (ma : m α) :
    m β :=
  sorry
```


1.2 (1 point). Prove the identity law for `map`.

Hint: You will need `LawfulMonad.bind_pure`.


```lean
theorem map_id {m : Type → Type} [LawfulMonad m] {α : Type} (ma : m α) :
    map id ma = ma :=
  sorry
```


1.3 (2 points). Prove the composition law for `map`.


```lean
theorem map_map {m : Type → Type} [LawfulMonad m] {α β γ : Type}
      (f : α → β) (g : β → γ) (ma : m α) :
    map g (map f ma) = map (fun x ↦ g (f x)) ma :=
  sorry
```


### Question 2 (5 points + 1 bonus point): Monadic Structure on Lists

`List` can be seen as a monad, similar to `Option` but with several possible
outcomes. It is also similar to `Set`, but the results are ordered and finite.
The code below sets `List` up as a monad.


```lean
namespace List

def bind {α β : Type} : List α → (α → List β) → List β
  | [],      f => []
  | a :: as, f => f a ++ bind as f

def pure {α : Type} (a : α) : List α :=
  [a]
```


2.1 (1 point). Prove the following property of `bind` under the append
operation.


```lean
theorem bind_append {α β : Type} (f : α → List β) :
    ∀as as' : List α, bind (as ++ as') f = bind as f ++ bind as' f :=
  sorry
```


2.2 (3 points). Prove the three laws for `List`.


```lean
theorem pure_bind {α β : Type} (a : α) (f : α → List β) :
    bind (pure a) f = f a :=
  sorry

theorem bind_pure {α : Type} :
    ∀as : List α, bind as pure = as :=
  sorry

theorem bind_assoc {α β γ : Type} (f : α → List β) (g : β → List γ) :
    ∀as : List α, bind (bind as f) g = bind as (fun a ↦ bind (f a) g) :=
  sorry
```


2.3 (1 point). Prove the following list-specific law.


```lean
theorem bind_pure_comp_eq_map {α β : Type} {f : α → β} :
    ∀as : List α, bind as (fun a ↦ pure (f a)) = List.map f as :=
  sorry
```


2.4 (1 bonus point). Register `List` as a lawful monad:


```lean
instance LawfulMonad : LawfulMonad List :=
  sorry

end List

end LoVe
```

---



## Metaprogramming (Demo) {#hhg-metaprogramming-demo}

> 📄 Source: https://raw.githubusercontent.com/lean-forward/logical_verification_2025/main/lean/LoVe/LoVe08_Metaprogramming_Demo.lean

```lean
import LoVe.LoVe06_InductivePredicates_Demo
```


## LoVe Demo 8: Metaprogramming

Users can extend Lean with custom tactics and tools. This kind of
programming—programming the prover—is called metaprogramming.

Lean's metaprogramming framework uses mostly the same notions and syntax as
Lean's input language itself. Abstract syntax trees __reflect__ internal data
structures, e.g., for expressions (terms). The prover's internals are exposed
through Lean interfaces, which we can use for

* accessing the current context and goal;
* unifying expressions;
* querying and modifying the environment;
* setting attributes.

Most of Lean itself is implemented in Lean.

Example applications:

* proof goal transformations;
* heuristic proof search;
* decision procedures;
* definition generators;
* advisor tools;
* exporters;
* ad hoc automation.

Advantages of Lean's metaprogramming framework:

* Users do not need to learn another programming language to write
  metaprograms; they can work with the same constructs and notation used to
  define ordinary objects in the prover's library.

* Everything in that library is available for metaprogramming purposes.

* Metaprograms can be written and debugged in the same interactive environment,
  encouraging a style where formal libraries and supporting automation are
  developed at the same time.


```lean
set_option autoImplicit false
set_option tactic.hygienic false

open Lean
open Lean.Meta
open Lean.Elab.Tactic
open Lean.TSyntax

namespace LoVe
```


### Tactic Combinators

When programming our own tactics, we often need to repeat some actions on
several goals, or to recover if a tactic fails. Tactic combinators help in such
cases.

`repeat'` applies its argument repeatedly on all (sub…sub)goals until it cannot
be applied any further.


```lean
theorem repeat'_example :
    Even 4 ∧ Even 7 ∧ Even 3 ∧ Even 0 :=
  by
    repeat' apply And.intro
    repeat' apply Even.add_two
    repeat' sorry
```


The "first" combinator `first | ⋯ | ⋯ | ⋯` tries its first argument. If that
fails, it applies its second argument. If that fails, it applies its third
argument. And so on.


```lean
theorem repeat'_first_example :
    Even 4 ∧ Even 7 ∧ Even 3 ∧ Even 0 :=
  by
    repeat' apply And.intro
    repeat'
      first
      | apply Even.add_two
      | apply Even.zero
    repeat' sorry
```


`all_goals` applies its argument exactly once to each goal. It succeeds only
if the argument succeeds on **all** goals.

theorem all_goals_example :
    Even 4 ∧ Even 7 ∧ Even 3 ∧ Even 0 :=
  by
    repeat' apply And.intro
    all_goals apply Even.add_two   -- fails
    repeat' sorry

`try` transforms its argument into a tactic that never fails.


```lean
theorem all_goals_try_example :
    Even 4 ∧ Even 7 ∧ Even 3 ∧ Even 0 :=
  by
    repeat' apply And.intro
    all_goals try apply Even.add_two
    repeat sorry
```


`any_goals` applies its argument exactly once to each goal. It succeeds
if the argument succeeds on **any** goal.


```lean
theorem any_goals_example :
    Even 4 ∧ Even 7 ∧ Even 3 ∧ Even 0 :=
  by
    repeat' apply And.intro
    any_goals apply Even.add_two
    repeat' sorry
```


`solve | ⋯ | ⋯ | ⋯` is like `first` except that it succeeds only if one of
the arguments fully proves the current goal.


```lean
theorem any_goals_solve_repeat_first_example :
    Even 4 ∧ Even 7 ∧ Even 3 ∧ Even 0 :=
  by
    repeat' apply And.intro
    any_goals
      solve
      | repeat'
          first
          | apply Even.add_two
          | apply Even.zero
    repeat' sorry
```


The combinator `repeat'` can easily lead to infinite looping:

-- loops
theorem repeat'_Not_example :
    ¬ Even 1 :=
  by repeat' apply Not.intro

### Macros

We start with the actual metaprogramming, by coding a custom tactic as a
macro. The tactic embodies the behavior we hardcoded in the `solve` example
above:


```lean
macro "intro_and_even" : tactic =>
  `(tactic|
      (repeat' apply And.intro
       any_goals
         solve
         | repeat'
             first
             | apply Even.add_two
             | apply Even.zero))
```


Let us apply our custom tactic:


```lean
theorem intro_and_even_example :
    Even 4 ∧ Even 7 ∧ Even 3 ∧ Even 0 :=
  by
    intro_and_even
    repeat' sorry
```


### The Metaprogramming Monads

`MetaM` is the low-level metaprogramming monad. `TacticM` extends `MetaM` with
goal management.

* `MetaM` is a state monad providing access to the global context (including all
  definitions and inductive types), notations, and attributes (e.g., the list of
  `@[simp]` theorems), among others. `TacticM` additionally provides access to
  the list of goals.

* `MetaM` and `TacticM` behave like an option monad. The metaprogram `failure`
  leaves the monad in an error state.

* `MetaM` and `TacticM` support tracing, so we can use `logInfo` to display
  messages.

* Like other monads, `MetaM` and `TacticM` support imperative constructs such as
  `for`–`in`, `continue`, and `return`.


```lean
def traceGoals : TacticM Unit :=
  do
    logInfo m!"Lean version {Lean.versionString}"
    logInfo "All goals:"
    let goals ← getUnsolvedGoals
    logInfo m!"{goals}"
    match goals with
    | []     => return
    | _ :: _ =>
      logInfo "First goal's target:"
      let target ← getMainTarget
      logInfo m!"{target}"

elab "trace_goals" : tactic =>
  traceGoals

theorem Even_18_and_Even_20 (α : Type) (a : α) :
    Even 18 ∧ Even 20 :=
  by
    apply And.intro
    trace_goals
    intro_and_even
```


### First Example: An Assumption Tactic

We define a `hypothesis` tactic that behaves essentially the same as the
predefined `assumption` tactic.


```lean
def hypothesis : TacticM Unit :=
  withMainContext
    (do
       let target ← getMainTarget
       let lctx ← getLCtx
       for ldecl in lctx do
         if ! LocalDecl.isImplementationDetail ldecl then
           let eq ← isDefEq (LocalDecl.type ldecl) target
           if eq then
             let goal ← getMainGoal
             MVarId.assign goal (LocalDecl.toExpr ldecl)
             return
       failure)

elab "hypothesis" : tactic =>
  hypothesis

theorem hypothesis_example {α : Type} {p : α → Prop} {a : α}
      (hpa : p a) :
    p a :=
  by hypothesis
```


### Expressions

The metaprogramming framework revolves around the type `Expr` of expressions or
terms.


```lean
#print Expr
```


#### Names

We can create literal names with backticks:

* Names with a single backtick, `n, are not checked for existence.

* Names with two backticks, ``n, are resolved and checked.


```lean
#check `x
#eval `x
#eval `Even          -- wrong
#eval `LoVe.Even     -- suboptimal
#eval ``Even
```


#eval ``EvenThough   -- fails

#### Constants


```lean
#check Expr.const

#eval ppExpr (Expr.const ``Nat.add [])
#eval ppExpr (Expr.const ``Nat [])
```


#### Sorts (lecture 12)


```lean
#check Expr.sort

#eval ppExpr (Expr.sort Level.zero)
#eval ppExpr (Expr.sort (Level.succ Level.zero))
```


#### Free Variables


```lean
#check Expr.fvar

#check FVarId.mk `n
#eval ppExpr (Expr.fvar (FVarId.mk `n))
```


#### Metavariables


```lean
#check Expr.mvar
```


#### Applications


```lean
#check Expr.app

#eval ppExpr (Expr.app (Expr.const ``Nat.succ [])
  (Expr.const ``Nat.zero []))
```


#### Anonymous Functions and Bound Variables


```lean
#check Expr.bvar
#check Expr.lam

#eval ppExpr (Expr.bvar 0)

#eval ppExpr (Expr.lam `x (Expr.const ``Nat []) (Expr.bvar 0)
  BinderInfo.default)

#eval ppExpr (Expr.lam `x (Expr.const ``Nat [])
  (Expr.lam `y (Expr.const ``Nat []) (Expr.bvar 1)
     BinderInfo.default)
  BinderInfo.default)
```


#### Dependent Function Types


```lean
#check Expr.forallE

#eval ppExpr (Expr.forallE `n (Expr.const ``Nat [])
  (Expr.app (Expr.const ``Even []) (Expr.bvar 0))
  BinderInfo.default)

#eval ppExpr (Expr.forallE `dummy (Expr.const `Nat [])
  (Expr.const `Bool []) BinderInfo.default)
```


#### Other Constructors


```lean
#check Expr.letE
#check Expr.lit
#check Expr.mdata
#check Expr.proj
```


### Second Example: A Conjunction-Destructing Tactic

We define a `destruct_and` tactic that automates the elimination of `∧` in
premises, automating proofs such as these:


```lean
theorem abc_a (a b c : Prop) (h : a ∧ b ∧ c) :
    a :=
  And.left h

theorem abc_b (a b c : Prop) (h : a ∧ b ∧ c) :
    b :=
  And.left (And.right h)

theorem abc_bc (a b c : Prop) (h : a ∧ b ∧ c) :
    b ∧ c :=
  And.right h

theorem abc_c (a b c : Prop) (h : a ∧ b ∧ c) :
    c :=
  And.right (And.right h)
```


Our tactic relies on a helper function, which takes as argument the
hypothesis `h` to use as an expression:


```lean
partial def destructAndExpr (hP : Expr) : TacticM Bool :=
  withMainContext
    (do
       let target ← getMainTarget
       let P ← inferType hP
       let eq ← isDefEq P target
       if eq then
         let goal ← getMainGoal
         MVarId.assign goal hP
         return true
       else
         match Expr.and? P with
         | Option.none        => return false
         | Option.some (Q, R) =>
           let hQ ← mkAppM ``And.left #[hP]
           let success ← destructAndExpr hQ
           if success then
             return true
           else
             let hR ← mkAppM ``And.right #[hP]
             destructAndExpr hR)

#check Expr.and?

def destructAnd (name : Name) : TacticM Unit :=
  withMainContext
    (do
       let h ← getFVarFromUserName name
       let success ← destructAndExpr h
       if ! success then
         failure)

elab "destruct_and" h:ident : tactic =>
  destructAnd (getId h)
```


Let us check that our tactic works:


```lean
theorem abc_a_again (a b c : Prop) (h : a ∧ b ∧ c) :
    a :=
  by destruct_and h

theorem abc_b_again (a b c : Prop) (h : a ∧ b ∧ c) :
    b :=
  by destruct_and h

theorem abc_bc_again (a b c : Prop) (h : a ∧ b ∧ c) :
    b ∧ c :=
  by destruct_and h

theorem abc_c_again (a b c : Prop) (h : a ∧ b ∧ c) :
    c :=
  by destruct_and h
```


theorem abc_ac (a b c : Prop) (h : a ∧ b ∧ c) :
    a ∧ c :=
  by destruct_and h   -- fails

### Third Example: A Direct Proof Finder

Finally, we implement a `prove_direct` tool that traverses all theorems in the
database and checks whether one of them can be used to prove the current
goal.


```lean
def isTheorem : ConstantInfo → Bool
  | ConstantInfo.axiomInfo _ => true
  | ConstantInfo.thmInfo _   => true
  | _                        => false

def applyConstant (name : Name) : TacticM Unit :=
  do
    let cst ← mkConstWithFreshMVarLevels name
    liftMetaTactic (fun goal ↦ MVarId.apply goal cst)

def andThenOnSubgoals (tac₁ tac₂ : TacticM Unit) :
  TacticM Unit :=
  do
    let origGoals ← getGoals
    let mainGoal ← getMainGoal
    setGoals [mainGoal]
    tac₁
    let subgoals₁ ← getUnsolvedGoals
    let mut newGoals := []
    for subgoal in subgoals₁ do
      let assigned ← MVarId.isAssigned subgoal
      if ! assigned then
        setGoals [subgoal]
        tac₂
        let subgoals₂ ← getUnsolvedGoals
        newGoals := newGoals ++ subgoals₂
    setGoals (newGoals ++ List.tail origGoals)

def proveUsingTheorem (name : Name) : TacticM Unit :=
  andThenOnSubgoals (applyConstant name) hypothesis

def proveDirect : TacticM Unit :=
  do
    let origGoals ← getUnsolvedGoals
    let goal ← getMainGoal
    setGoals [goal]
    let env ← getEnv
    for (name, info)
        in SMap.toList (Environment.constants env) do
      if isTheorem info && ! ConstantInfo.isUnsafe info then
        try
          proveUsingTheorem name
          logInfo m!"Proved directly by {name}"
          setGoals (List.tail origGoals)
          return
        catch _ =>
          continue
    failure

elab "prove_direct" : tactic =>
  proveDirect
```


Let us check that our tactic works:


```lean
theorem Nat.symm (x y : ℕ) (h : x = y) :
    y = x :=
  by prove_direct

theorem Nat.symm_manual (x y : ℕ) (h : x = y) :
    y = x :=
  by
    apply symm
    hypothesis

theorem Nat.trans (x y z : ℕ) (hxy : x = y) (hyz : y = z) :
    x = z :=
  by prove_direct

theorem List.reverse_twice (xs : List ℕ) :
    List.reverse (List.reverse xs) = xs :=
  by prove_direct
```


Lean has `apply?`:


```lean
theorem List.reverse_twice_apply? (xs : List ℕ) :
    List.reverse (List.reverse xs) = xs :=
  by apply?

end LoVe
```

---



## Metaprogramming (ExerciseSheet) {#hhg-metaprogramming-exercisesheet}

> 📄 Source: https://raw.githubusercontent.com/lean-forward/logical_verification_2025/main/lean/LoVe/LoVe08_Metaprogramming_ExerciseSheet.lean

```lean
import LoVe.LoVe08_Metaprogramming_Demo
```


## LoVe Exercise 8: Metaprogramming

Replace the placeholders (e.g., `:= sorry`) with your solutions.


```lean
set_option autoImplicit false
set_option tactic.hygienic false
set_option linter.unusedTactic false

open Lean
open Lean.Meta
open Lean.Elab.Tactic
open Lean.TSyntax

namespace LoVe
```


### Question 1: `destruct_and` on Steroids

Recall from the lecture that `destruct_and` fails on easy goals such as


```lean
theorem abc_ac (a b c : Prop) (h : a ∧ b ∧ c) :
    a ∧ c :=
  sorry
```


We will now address this by developing a new tactic called `destro_and`,
which applies both **des**truction and in**tro**duction rules for conjunction.
It will also go automatically through the hypotheses instead of taking an
argument. We will develop it in three steps.

1.1. Develop a tactic `intro_and` that replaces all goals of the form
`a ∧ b` with two new goals `a` and `b` systematically, until all top-level
conjunctions are gone. Define your tactic as a macro.


```lean
#check repeat'

-- enter your definition here

theorem abcd_bd (a b c d : Prop) (h : a ∧ (b ∧ c) ∧ d) :
    b ∧ d :=
  by
    intro_and
    /- The proof state should be as follows:

        case left
        a b c d: Prop
        h : a ∧ (b ∧ c) ∧ d
        ⊢ b

        case right
        a b c d : Prop
        h : a ∧ (b ∧ c) ∧ d
        ⊢ d -/
    repeat' sorry

theorem abcd_bacb (a b c d : Prop) (h : a ∧ (b ∧ c) ∧ d) :
    b ∧ (a ∧ (c ∧ b)) :=
  by
    intro_and
    /- The proof state should be as follows:

        case left
        a b c d : Prop
        h : a ∧ (b ∧ c) ∧ d
        ⊢ b

        case right.left
        a b c d : Prop
        h : a ∧ (b ∧ c) ∧ d
        ⊢ a

        case right.right.left
        a b c d : Prop
        h : a ∧ (b ∧ c) ∧ d
        ⊢ c

        case right.right.right
        a b c d : Prop
        h : a ∧ (b ∧ c) ∧ d
        ⊢ b -/
    repeat' sorry
```


1.2. Develop a tactic `cases_and` that replaces hypotheses of the form
`h : a ∧ b` by two new hypotheses `h_left : a` and `h_right : b` systematically,
until all top-level conjunctions are gone.

Here is some pseudocode that you can follow:

1. Wrap the entire `do` block in a call to `withMainContext` to ensure you work
   with the right context.

2. Retrieve the list of hypotheses from the context. This is provided by
   `getLCtx`.

3. Find the first hypothesis (= term) with a type (= proposition) of the form
   `_ ∧ _`. To iterate, you can use the `for … in … do` syntax. To obtain the
   type of a term, you can use `inferType`. To check if a type `ty` has the form
   `_ ∧ _`, you can use `Expr.isAppOfArity ty ``And 2` (with two backticks before
   `And`).

4. Perform a case split on the first found hypothesis. This can be achieved
   using the metaprogram `cases` provided in `LoVelib`, which is similar to the
   `cases` tactic but is a metaprogram. To extract the free variable associated
   with a hypothesis, use `LocalDecl.fvarId`.

5. Repeat (via a recursive call).

6. Return.

Hint: When iterating over the declarations in the local context, make sure to
skip any declaration that is an implementation detail.


```lean
partial def casesAnd : TacticM Unit :=
  sorry

elab "cases_and" : tactic =>
  casesAnd

theorem abcd_bd_again (a b c d : Prop) :
    a ∧ (b ∧ c) ∧ d → b ∧ d :=
  by
    intro h
    cases_and
    /- The proof state should be as follows:

        case intro.intro.intro
        a b c d : Prop
        left : a
        right : d
        left_1 : b
        right_1 : c
        ⊢ b ∧ d -/
    sorry
```


1.3. Implement a `destro_and` tactic that first invokes `cases_and`, then
`intro_and`, before it tries to prove all the subgoals that can be discharged
directly by `assumption`.


```lean
macro "destro_and" : tactic =>
  sorry

theorem abcd_bd_over_again (a b c d : Prop) (h : a ∧ (b ∧ c) ∧ d) :
    b ∧ d :=
  by destro_and

theorem abcd_bacb_again (a b c d : Prop) (h : a ∧ (b ∧ c) ∧ d) :
    b ∧ (a ∧ (c ∧ b)) :=
  by destro_and

theorem abd_bacb_again (a b c d : Prop) (h : a ∧ b ∧ d) :
    b ∧ (a ∧ (c ∧ b)) :=
  by
    destro_and
    /- The proof state should be roughly as follows:

        case intro.intro.right.right.left
        a b c d : Prop
        left : a
        left_1 : b
        right : d
        ⊢ c -/
    sorry   -- unprovable
```


1.4. Provide some more examples for `destro_and` to convince yourself that
it works as expected also on more complicated examples.


```lean
-- enter your examples here
```


### Question 2: A Theorem Finder

We will implement a function that allows us to find theorems by constants
appearing in their statements. So given a list of constant names, the function
will list all theorems in which all these constants appear.

2.1. Write a function that checks whether an expression contains a specific
constant.

Hints:

* You can pattern-match on `e` and proceed recursively.

* The "not" connective on `Bool` is called `not`, the "or" connective is called
  `||`, the "and" connective is called `&&`, and equality is called `==`.


```lean
def constInExpr (name : Name) (e : Expr) : Bool :=
  sorry
```


2.2. Write a function that checks whether an expression contains **all**
constants in a list.

Hint: You can either proceed recursively or use `List.and` and `List.map`.


```lean
def constsInExpr (names : List Name) (e : Expr) : Bool :=
  sorry
```


2.3. Develop a tactic that uses `constsInExpr` to print the name of all
theorems that contain all constants `names` in their statement.

This code should be similar to that of `proveDirect` in the demo file. With
`ConstantInfo.type`, you can extract the proposition associated with a theorem.


```lean
def findConsts (names : List Name) : TacticM Unit :=
  sorry

elab "find_consts" "(" names:ident+ ")" : tactic =>
  findConsts (Array.toList (Array.map getId names))
```


Test the solution.


```lean
theorem List.a_property_of_reverse {α : Type} (xs : List α) (a : α) :
    List.concat xs a = List.reverse (a :: List.reverse xs) :=
  by
    find_consts (List.reverse)
    find_consts (List.reverse List.concat)
    sorry

end LoVe
```

---



## Metaprogramming (HomeworkSheet) {#hhg-metaprogramming-homeworksheet}

> 📄 Source: https://raw.githubusercontent.com/lean-forward/logical_verification_2025/main/lean/LoVe/LoVe08_Metaprogramming_HomeworkSheet.lean

```lean
import LoVe.LoVelib
```


## LoVe Homework 8 (10 points + 2 bonus points): Metaprogramming

Replace the placeholders (e.g., `:= sorry`) with your solutions.


```lean
set_option autoImplicit false
set_option tactic.hygienic false

open Lean
open Lean.Meta
open Lean.Elab.Tactic
open Lean.TSyntax

namespace LoVe
```


### Question 1 (10 points): A `safe` Tactic

You will develop a tactic that applies all safe introduction and elimination
rules for the connectives and quantifiers exhaustively. A rule is said to be
__safe__ if, given a provable goal, it always gives rise to provable subgoals.
In addition, we will require that safe rules do not introduce metavariables
(since these can easily be instantiated accidentally with the wrong terms).

You will proceed in three steps.

1.1 (4 points). Develop a `safe_intros` tactic that repeatedly applies the
introduction rules for `True`, `∧`, and `↔` and that invokes `intro _` for
`→`/`∀`. The tactic generalizes `intro_and` from the exercise.


```lean
macro "safe_intros" : tactic =>
  sorry

theorem abcd (a b c d : Prop) :
    a → ¬ b ∧ (c ↔ d) :=
  by
    safe_intros
    /- The proof state should be roughly as follows:

        case left
        a b c d : Prop
        a_1 : a
        a_2 : b
        ⊢ False

        case right.mp
        a b c d : Prop
        a_1 : a
        a_2 : c
        ⊢ d

        case right.mpr
        a b c d : Prop
        a_1 : a
        a_2 : d
        ⊢ c -/
    repeat' sorry
```


1.2 (4 points). Develop a `safe_cases` tactic that performs case
distinctions on `False`, `∧` (`And`), and `∃` (`Exists`). The tactic generalizes
`cases_and` from the exercise.

Hints:

* The last argument of `Expr.isAppOfArity` is the number of arguments expected
  by the logical symbol. For example, the arity of `∧` is 2.

* The "or" connective on `Bool` is called `||`.

Hint: When iterating over the declarations in the local context, make sure to
skip any declaration that is an implementation detail.


```lean
#check @False
#check @And
#check @Exists

partial def safeCases : TacticM Unit :=
  sorry

elab "safe_cases" : tactic =>
  safeCases

theorem abcdef (a b c d e f : Prop) (P : ℕ → Prop)
      (hneg: ¬ a) (hand : a ∧ b ∧ c) (hor : c ∨ d) (himp : b → e) (hiff : e ↔ f)
      (hex : ∃x, P x) :
    False :=
  by
    safe_cases
  /- The proof state should be roughly as follows:

      case intro.intro.intro
      a b c d e f : Prop
      P : ℕ → Prop
      hneg : ¬a
      hor : c ∨ d
      himp : b → e
      hiff : e ↔ f
      left : a
      w : ℕ
      h : P w
      left_1 : b
      right : c
      ⊢ False -/
    sorry
```


1.3 (2 points). Implement a `safe` tactic that first invokes `safe_intros`
on all goals, then `safe_cases` on all emerging subgoals, before it tries
`assumption` on all emerging subsubgoals.


```lean
macro "safe" : tactic =>
  sorry

theorem abcdef_abcd (a b c d e f : Prop) (P : ℕ → Prop)
      (hneg: ¬ a) (hand : a ∧ b ∧ c) (hor : c ∨ d) (himp : b → e) (hiff : e ↔ f)
      (hex : ∃x, P x) :
    a → ¬ b ∧ (c ↔ d) :=
  by
    safe
    /- The proof state should be roughly as follows:

        case left.intro.intro.intro
        a b c d e f : Prop
        P : ℕ → Prop
        hneg : ¬a
        hor : c ∨ d
        himp : b → e
        hiff : e ↔ f
        a_1 : a
        a_2 : b
        left : a
        w : ℕ
        h : P w
        left_1 : b
        right : c
        ⊢ False

        case right.mp.intro.intro.intro
        a b c d e f : Prop
        P : ℕ → Prop
        hneg : ¬a
        hor : c ∨ d
        himp : b → e
        hiff : e ↔ f
        a_1 : a
        a_2 : c
        left : a
        w : ℕ
        h : P w
        left_1 : b
        right : c
        ⊢ d -/
    repeat' sorry
```


### Question 2 (2 bonus points): An `aesop`-Like Tactic

2.1 (1 bonus point). Develop a simple `aesop`-like tactic.

This tactic should apply all safe introduction and elimination rules. In
addition, it should try potentially unsafe rules (such as `Or.inl` and
`False.elim`) but backtrack at some point (or try several possibilities in
parallel). Iterative deepening may be a valid approach, or best-first search, or
breadth-first search. The tactic should also try to apply assumptions whose
conclusion matches the goal, but backtrack if necessary.

Hint: The `MonadBacktrack` monad class might be useful.

2.2 (1 bonus point). Test your tactic on some benchmarks.

You can try your tactic on logic puzzles of the kinds we proved in exercise and
homework 3. Please include these below.


```lean
end LoVe
```

---



## Operational Semantics (Demo) {#hhg-operational-semantics-demo}

> 📄 Source: https://raw.githubusercontent.com/lean-forward/logical_verification_2025/main/lean/LoVe/LoVe09_OperationalSemantics_Demo.lean

```lean
import LoVe.LoVelib
```


## LoVe Demo 9: Operational Semantics

In this and the next two lectures, we will see how to use Lean to specify the
syntax and semantics of programming languages and to reason about the
semantics.


```lean
set_option autoImplicit false
set_option tactic.hygienic false

namespace LoVe
```


### First Things First: Formalization Projects

Instead of two of the homework sheets, you can do a verification project, worth
20 points. If you choose to do so, please send your lecturer a message by email
by the end of the week. For a fully successful project, we expect about 200 (or
more) lines of Lean, including definitions and proofs.

Some ideas for projects follow.

Computer science:

* extended WHILE language with static arrays or other features;
* functional data structures (e.g., balanced trees);
* functional algorithms (e.g., bubble sort, merge sort, Tarjan's algorithm);
* compiler from expressions or imperative programs to, e.g., stack machine;
* type systems (e.g., Benjamin Pierce's __Types and Programming Languages__);
* security properties (e.g., Volpano–Smith-style noninterference analysis);
* theory of first-order terms, including matching, term rewriting;
* automata theory;
* normalization of context-free grammars or regular expressions;
* process algebras and bisimilarity;
* soundness and possibly completeness of proof systems (e.g., Genzen's sequent
  calculus, natural deduction, tableaux);
* separation logic;
* verified program using Hoare logic.

Mathematics:

* graphs;
* combinatorics;
* number theory.

Metaprogramming:

* custom tactic;
* custom diagnosis tool.

Past evaluation:

Q: How did you find the project?

A: Enjoyable.

A: Fun and hard.

A: Good, I think the format was excellent in a way that it gave people the
   chance to do challenging exercises and hand them in incomplete.

A: I really really liked it. I think it's a great way of learning—find
   something you like, dig in it a little, get stuck, ask for help. I wish I
   could do more of that!

A: It was great to have some time to try to work out some stuff you find
   interesting yourself.

A: lots of fun actually!!!

A: Very helpful. It gave the opportunity to spend some more time on a
   particular aspect of the course.


### Formal Semantics

A formal semantics helps specify and reason about the programming language
itself, and about individual programs.

It can form the basis of verified compilers, interpreters, verifiers, static
analyzers, type checkers, etc. Without formal proofs, these tools are
**almost always wrong**.

In this area, proof assistants are widely used. Every year, about 10-20% of POPL
papers are partially or totally formalized. Reasons for this success:

* Little machinery (background libraries, tactics) is needed to get started,
  beyond inductive types and predicates and recursive functions.

* The proofs tend to have lots of cases, which is a good match for computers.

* Proof assistants keep track of what needs to be changed when we extend the
  programming language with more features.

Case in point: WebAssembly. To quote Conrad Watt (with some abbreviations):

    We have produced a full Isabelle mechanisation of the core execution
    semantics and type system of the WebAssembly language. To complete this
    proof, **several deficiencies** in the official WebAssembly specification,
    uncovered by our proof and modelling work, needed to be corrected. In some
    cases, these meant that the type system was **originally unsound**.

    We have maintained a constructive dialogue with the working group,
    verifying new features as they are added. In particular, the mechanism by
    which a WebAssembly implementation interfaces with its host environment was
    not formally specified in the working group's original paper. Extending our
    mechanisation to model this feature revealed a deficiency in the WebAssembly
    specification that **sabotaged the soundness** of the type system.


### A Minimalistic Imperative Language

A state `s` is a function from variable names to values (`String → ℕ`).

__WHILE__ is a minimalistic imperative language with the following grammar:

    S  ::=  skip                 -- no-op
         |  x := a               -- assignment
         |  S; S                 -- sequential composition
         |  if B then S else S   -- conditional statement
         |  while B do S         -- while loop

where `S` stands for a statement (also called command or program), `x` for a
variable, `a` for an arithmetic expression, and `B` for a Boolean expression.


```lean
#print State

inductive Stmt : Type where
  | skip       : Stmt
  | assign     : String → (State → ℕ) → Stmt
  | seq        : Stmt → Stmt → Stmt
  | ifThenElse : (State → Prop) → Stmt → Stmt → Stmt
  | whileDo    : (State → Prop) → Stmt → Stmt

infixr:90 "; " => Stmt.seq
```


In our grammar, we deliberately leave the syntax of arithmetic and Boolean
expressions unspecified. In Lean, we have the choice:

* We could use a type such as `AExp` from lecture 2 and similarly for Boolean
  expressions.

* We could decide that an arithmetic expression is simply a function from
  states to natural numbers (`State → ℕ`) and a Boolean expression is a
  predicate (`State → Prop` or `State → Bool`).

This corresponds to the difference between deep and shallow embeddings:

* A __deep embedding__ of some syntax (expression, formula, program, etc.)
  consists of an abstract syntax tree specified in the proof assistant
  (e.g., `AExp`) with a semantics (e.g., `eval`).

* In contrast, a __shallow embedding__ simply reuses the corresponding
  mechanisms from the logic (e.g., terms, functions, predicate types).

A deep embedding allows us to reason about the syntax (and its semantics). A
shallow embedding is more lightweight, because we can use it directly, without
having to define a semantics.

We will use a deep embedding of programs (which we find interesting), and
shallow embeddings of assignments and Boolean expressions (which we find
boring).

The program

  while x > y do
    skip;
    x := x - 1

is modeled as


```lean
def sillyLoop : Stmt :=
  Stmt.whileDo (fun s ↦ s "x" > s "y")
    (Stmt.skip;
     Stmt.assign "x" (fun s ↦ s "x" - 1))
```


### Big-Step Semantics

An __operational semantics__ corresponds to an idealized interpreter (specified
in a Prolog-like language). Two main variants:

* big-step semantics;

* small-step semantics.

In a __big-step semantics__ (also called __natural semantics__), judgments have
the form `(S, s) ⟹ t`:

    Starting in a state `s`, executing `S` terminates in the state `t`.

Example:

    `(x := x + y; y := 0, [x ↦ 3, y ↦ 5]) ⟹ [x ↦ 8, y ↦ 0]`

Derivation rules:

    ——————————————— Skip
    (skip, s) ⟹ s

    ——————————————————————————— Assign
    (x := a, s) ⟹ s[x ↦ s(a)]

    (S, s) ⟹ t   (T, t) ⟹ u
    ——————————————————————————— Seq
    (S; T, s) ⟹ u

    (S, s) ⟹ t
    ————————————————————————————— If-True   if s(B) is true
    (if B then S else T, s) ⟹ t

    (T, s) ⟹ t
    ————————————————————————————— If-False   if s(B) is false
    (if B then S else T, s) ⟹ t

    (S, s) ⟹ t   (while B do S, t) ⟹ u
    —————————————————————————————————————— While-True   if s(B) is true
    (while B do S, s) ⟹ u

    ————————————————————————— While-False   if s(B) is false
    (while B do S, s) ⟹ s

Above, `s(e)` denotes the value of expression `e` in state `s` and `s[x ↦ s(e)]`
denotes the state that is identical to `s` except that the variable `x` is bound
to the value `s(e)`.

In Lean, the judgment corresponds to an inductive predicate, and the derivation
rules correspond to the predicate's introduction rules. Using an inductive
predicate as opposed to a recursive function allows us to cope with
nontermination (e.g., a diverging `while`) and nondeterminism (e.g.,
multithreading).


```lean
inductive BigStep : Stmt × State → State → Prop where
  | skip (s) :
    BigStep (Stmt.skip, s) s
  | assign (x a s) :
    BigStep (Stmt.assign x a, s) (s[x ↦ a s])
  | seq (S T s t u) (hS : BigStep (S, s) t)
      (hT : BigStep (T, t) u) :
    BigStep (S; T, s) u
  | if_true (B S T s t) (hcond : B s)
      (hbody : BigStep (S, s) t) :
    BigStep (Stmt.ifThenElse B S T, s) t
  | if_false (B S T s t) (hcond : ¬ B s)
      (hbody : BigStep (T, s) t) :
    BigStep (Stmt.ifThenElse B S T, s) t
  | while_true (B S s t u) (hcond : B s)
      (hbody : BigStep (S, s) t)
      (hrest : BigStep (Stmt.whileDo B S, t) u) :
    BigStep (Stmt.whileDo B S, s) u
  | while_false (B S s) (hcond : ¬ B s) :
    BigStep (Stmt.whileDo B S, s) s

infix:110 " ⟹ " => BigStep

theorem sillyLoop_from_1_BigStep :
    (sillyLoop, (fun _ ↦ 0)["x" ↦ 1]) ⟹ (fun _ ↦ 0) :=
  by
    rw [sillyLoop]
    apply BigStep.while_true
    { simp }
    { apply BigStep.seq
      { apply BigStep.skip }
      { apply BigStep.assign } }
    { simp
      apply BigStep.while_false
      simp }
```


### Properties of the Big-Step Semantics

Equipped with a big-step semantics, we can

* prove properties of the programming language, such as **equivalence proofs**
  between programs and **determinism**;

* reason about **concrete programs**, proving theorems relating final states `t`
  with initial states `s`.


```lean
theorem BigStep_deterministic {Ss l r} (hl : Ss ⟹ l)
      (hr : Ss ⟹ r) :
    l = r :=
  by
    induction hl generalizing r with
    | skip s =>
      cases hr with
      | skip => rfl
    | assign x a s =>
      cases hr with
      | assign => rfl
    | seq S T s l₀ l hS hT ihS ihT =>
      cases hr with
      | seq _ _ _ r₀ _ hS' hT' =>
        cases ihS hS' with
        | refl =>
          cases ihT hT' with
          | refl => rfl
    | if_true B S T s l hB hS ih =>
      cases hr with
      | if_true _ _ _ _ _ hB' hS'  => apply ih hS'
      | if_false _ _ _ _ _ hB' hS' => aesop
    | if_false B S T s l hB hT ih =>
      cases hr with
      | if_true _ _ _ _ _ hB' hS'  => aesop
      | if_false _ _ _ _ _ hB' hS' => apply ih hS'
    | while_true B S s l₀ l hB hS hw ihS ihw =>
      cases hr with
      | while_true _ _ _ r₀ hB' hB' hS' hw' =>
        cases ihS hS' with
        | refl =>
          cases ihw hw' with
          | refl => rfl
      | while_false _ _ _ hB' => aesop
    | while_false B S s hB =>
      cases hr with
      | while_true _ _ _ s' _ hB' hS hw => aesop
      | while_false _ _ _ hB'           => rfl
```


theorem BigStep_terminates {S s} :
    ∃t, (S, s) ⟹ t :=
  sorry   -- unprovable

We can define inversion rules about the big-step semantics:


```lean
@[simp] theorem BigStep_skip_Iff {s t} :
    (Stmt.skip, s) ⟹ t ↔ t = s :=
  by
    apply Iff.intro
    { intro h
      cases h with
      | skip => rfl }
    { intro h
      rw [h]
      apply BigStep.skip }

@[simp] theorem BigStep_assign_Iff {x a s t} :
    (Stmt.assign x a, s) ⟹ t ↔ t = s[x ↦ a s] :=
  by
    apply Iff.intro
    { intro h
      cases h with
      | assign => rfl }
    { intro h
      rw [h]
      apply BigStep.assign }

@[simp] theorem BigStep_seq_Iff {S T s u} :
    (S; T, s) ⟹ u ↔ (∃t, (S, s) ⟹ t ∧ (T, t) ⟹ u) :=
  by
    apply Iff.intro
    { intro h
      cases h with
      | seq =>
        apply Exists.intro
        apply And.intro <;>
          assumption }
    { intro h
      cases h with
      | intro s' h' =>
        cases h' with
        | intro hS hT =>
          apply BigStep.seq <;>
            assumption }

@[simp] theorem BigStep_if_Iff {B S T s t} :
    (Stmt.ifThenElse B S T, s) ⟹ t ↔
    (B s ∧ (S, s) ⟹ t) ∨ (¬ B s ∧ (T, s) ⟹ t) :=
  by
    apply Iff.intro
    { intro h
      cases h with
      | if_true _ _ _ _ _ hB hS =>
        apply Or.intro_left
        aesop
      | if_false _ _ _ _ _ hB hT =>
        apply Or.intro_right
        aesop }
    { intro h
      cases h with
      | inl h =>
        cases h with
        | intro hB hS =>
          apply BigStep.if_true <;>
            assumption
      | inr h =>
        cases h with
        | intro hB hT =>
          apply BigStep.if_false <;>
            assumption }

theorem BigStep_while_Iff {B S s u} :
    (Stmt.whileDo B S, s) ⟹ u ↔
    (B s ∧ ∃t, (S, s) ⟹ t ∧ (Stmt.whileDo B S, t) ⟹ u)
    ∨ (¬ B s ∧ u = s) :=
  by
    apply Iff.intro
    { intro h
      cases h with
      | while_true _ _ _ t _ hB hS hw => aesop
      | while_false _ _ _ hB => aesop }
    { intro h
      cases h with
      | inl hex =>
        cases hex with
        | intro t h =>
          cases h with
          | intro hB h =>
            cases h with
            | intro hS hwhile =>
              apply BigStep.while_true <;>
                assumption
      | inr h =>
        cases h with
        | intro hB hus =>
          rw [hus]
          apply BigStep.while_false
          assumption}

@[simp] theorem BigStep_while_true_Iff {B S s u}
      (hcond : B s) :
    (Stmt.whileDo B S, s) ⟹ u ↔
    (∃t, (S, s) ⟹ t ∧ (Stmt.whileDo B S, t) ⟹ u) :=
  by
    rw [BigStep_while_Iff]
    simp [hcond]

@[simp] theorem BigStep_while_false_Iff {B S s t}
      (hcond : ¬ B s) :
    (Stmt.whileDo B S, s) ⟹ t ↔ t = s :=
  by
    rw [BigStep_while_Iff]
    simp [hcond]
```


### Small-Step Semantics

A big-step semantics

* does not let us reason about intermediate states;

* does not let us express nontermination or interleaving (for multithreading).

__Small-step semantics__ (also called __structural operational semantics__)
solve the above issues.

A judgment has the form `(S, s) ⇒ (T, t)`:

    Starting in a state `s`, executing one step of `S` leaves us in the
    state `t`, with the program `T` remaining to be executed.

An execution is a finite or infinite chain `(S₀, s₀) ⇒ (S₁, s₁) ⇒ …`.

A pair `(S, s)` is called a __configuration__. It is __final__ if no transition
of the form `(S, s) ⇒ _` is possible.

Example:

      `(x := x + y; y := 0, [x ↦ 3, y ↦ 5])`
    `⇒ (skip; y := 0,       [x ↦ 8, y ↦ 5])`
    `⇒ (y := 0,             [x ↦ 8, y ↦ 5])`
    `⇒ (skip,               [x ↦ 8, y ↦ 0])`

Derivation rules:

    ————————————————————————————————— Assign
    (x := a, s) ⇒ (skip, s[x ↦ s(a)])

    (S, s) ⇒ (S', s')
    ———-——————————————————— Seq-Step
    (S; T, s) ⇒ (S'; T, s')

    ————————————————————— Seq-Skip
    (skip; S, s) ⇒ (S, s)

    ———————————————————————————————— If-True   if s(B) is true
    (if B then S else T, s) ⇒ (S, s)

    ———————————————————————————————— If-False   if s(B) is false
    (if B then S else T, s) ⇒ (T, s)

    ——————————————————————————————————————————————————————————————— While
    (while B do S, s) ⇒ (if B then (S; while B do S) else skip, s)

There is no rule for `skip` (why?).


```lean
inductive SmallStep : Stmt × State → Stmt × State → Prop where
  | assign (x a s) :
    SmallStep (Stmt.assign x a, s) (Stmt.skip, s[x ↦ a s])
  | seq_step (S S' T s s') (hS : SmallStep (S, s) (S', s')) :
    SmallStep (S; T, s) (S'; T, s')
  | seq_skip (T s) :
    SmallStep (Stmt.skip; T, s) (T, s)
  | if_true (B S T s) (hcond : B s) :
    SmallStep (Stmt.ifThenElse B S T, s) (S, s)
  | if_false (B S T s) (hcond : ¬ B s) :
    SmallStep (Stmt.ifThenElse B S T, s) (T, s)
  | whileDo (B S s) :
    SmallStep (Stmt.whileDo B S, s)
      (Stmt.ifThenElse B (S; Stmt.whileDo B S) Stmt.skip, s)

infixr:100 " ⇒ " => SmallStep
infixr:100 " ⇒* " => RTC SmallStep

theorem sillyLoop_from_1_SmallStep :
    (sillyLoop, (fun _ ↦ 0)["x" ↦ 1]) ⇒*
    (Stmt.skip, (fun _ ↦ 0)) :=
  by
    rw [sillyLoop]
    apply RTC.head
    { apply SmallStep.whileDo }
    { apply RTC.head
      { apply SmallStep.if_true
        aesop }
      { apply RTC.head
        { apply SmallStep.seq_step
          apply SmallStep.seq_skip }
        { apply RTC.head
          { apply SmallStep.seq_step
            apply SmallStep.assign }
          { apply RTC.head
            { apply SmallStep.seq_skip }
            { apply RTC.head
              { apply SmallStep.whileDo }
              { apply RTC.head
                { apply SmallStep.if_false
                  simp }
                { simp
                  apply RTC.refl } } } } } } }
```


Equipped with a small-step semantics, we can **define** a big-step
semantics:

    `(S, s) ⟹ t` if and only if `(S, s) ⇒* (skip, t)`

where `r*` denotes the reflexive transitive closure of a relation `r`.

Alternatively, if we have already defined a big-step semantics, we can **prove**
the above equivalence theorem to validate our definitions.

The main disadvantage of small-step semantics is that we now have two relations,
`⇒` and `⇒*`, and reasoning tends to be more complicated.


### Properties of the Small-Step Semantics

We can prove that a configuration `(S, s)` is final if and only if `S = skip`.
This ensures that we have not forgotten a derivation rule.


```lean
theorem SmallStep_final (S s) :
    (¬ ∃T t, (S, s) ⇒ (T, t)) ↔ S = Stmt.skip :=
  by
    induction S with
    | skip =>
      simp
      intros T t hstep
      cases hstep
    | assign x a =>
      simp
      apply Exists.intro Stmt.skip
      apply Exists.intro (s[x ↦ a s])
      apply SmallStep.assign
    | seq S T ihS ihT =>
      simp
      cases Classical.em (S = Stmt.skip) with
      | inl h =>
        rw [h]
        apply Exists.intro T
        apply Exists.intro s
        apply SmallStep.seq_skip
      | inr h =>
        simp [h] at ihS
        cases ihS with
        | intro S' hS₀ =>
          cases hS₀ with
          | intro s' hS =>
            apply Exists.intro (S'; T)
            apply Exists.intro s'
            apply SmallStep.seq_step
            assumption
    | ifThenElse B S T ihS ihT =>
      simp
      cases Classical.em (B s) with
      | inl h =>
        apply Exists.intro S
        apply Exists.intro s
        apply SmallStep.if_true
        assumption
      | inr h =>
        apply Exists.intro T
        apply Exists.intro s
        apply SmallStep.if_false
        assumption
    | whileDo B S ih =>
      simp
      apply Exists.intro
        (Stmt.ifThenElse B (S; Stmt.whileDo B S)
           Stmt.skip)
      apply Exists.intro s
      apply SmallStep.whileDo

theorem SmallStep_deterministic {Ss Ll Rr}
      (hl : Ss ⇒ Ll) (hr : Ss ⇒ Rr) :
    Ll = Rr :=
  by
    induction hl generalizing Rr with
    | assign x a s =>
      cases hr with
      | assign _ _ _ => rfl
    | seq_step S S₁ T s s₁ hS₁ ih =>
      cases hr with
      | seq_step S S₂ _ _ s₂ hS₂ =>
        have hSs₁₂ :=
          ih hS₂
        aesop
      | seq_skip => cases hS₁
    | seq_skip T s =>
      cases hr with
      | seq_step _ S _ _ s' hskip => cases hskip
      | seq_skip                  => rfl
    | if_true B S T s hB =>
      cases hr with
      | if_true  => rfl
      | if_false => aesop
    | if_false B S T s hB =>
      cases hr with
      | if_true  => aesop
      | if_false => rfl
    | whileDo B S s =>
      cases hr with
      | whileDo => rfl
```


We can define inversion rules also about the small-step semantics. Here are
three examples:


```lean
theorem SmallStep_skip {S s t} :
    ¬ ((Stmt.skip, s) ⇒ (S, t)) :=
  by
    intro h
    cases h

@[simp] theorem SmallStep_seq_Iff {S T s Ut} :
    (S; T, s) ⇒ Ut ↔
    (∃S' t, (S, s) ⇒ (S', t) ∧ Ut = (S'; T, t))
    ∨ (S = Stmt.skip ∧ Ut = (T, s)) :=
  by
    apply Iff.intro
    { intro hST
      cases hST with
      | seq_step _ S' _ _ s' hS =>
        apply Or.intro_left
        apply Exists.intro S'
        apply Exists.intro s'
        aesop
      | seq_skip =>
        apply Or.intro_right
        aesop }
    {
      intro hor
      cases hor with
      | inl hex =>
        cases hex with
        | intro S' hex' =>
          cases hex' with
          | intro s' hand =>
            cases hand with
            | intro hS hUt =>
              rw [hUt]
              apply SmallStep.seq_step
              assumption
      | inr hand =>
        cases hand with
        | intro hS hUt =>
          rw [hS, hUt]
          apply SmallStep.seq_skip }

@[simp] theorem SmallStep_if_Iff {B S T s Us} :
    (Stmt.ifThenElse B S T, s) ⇒ Us ↔
    (B s ∧ Us = (S, s)) ∨ (¬ B s ∧ Us = (T, s)) :=
  by
    apply Iff.intro
    { intro h
      cases h with
      | if_true _ _ _ _ hB  => aesop
      | if_false _ _ _ _ hB => aesop }
    { intro hor
      cases hor with
      | inl hand =>
        cases hand with
        | intro hB hUs =>
          rw [hUs]
          apply SmallStep.if_true
          assumption
      | inr hand =>
        cases hand with
        | intro hB hUs =>
          rw [hUs]
          apply SmallStep.if_false
          assumption }
```


#### Equivalence of the Big-Step and the Small-Step Semantics (**optional**)

A more important result is the connection between the big-step and the
small-step semantics:

    `(S, s) ⟹ t ↔ (S, s) ⇒* (Stmt.skip, t)`

Its proof, given below, is beyond the scope of this course.


```lean
theorem RTC_SmallStep_seq {S T s u}
      (h : (S, s) ⇒* (Stmt.skip, u)) :
    (S; T, s) ⇒* (Stmt.skip; T, u) :=
  by
    apply RTC.lift (fun Ss ↦ (Prod.fst Ss; T, Prod.snd Ss)) _ h
    intro Ss Ss' hrtc
    cases Ss with
    | mk S s =>
      cases Ss' with
      | mk S' s' =>
        apply SmallStep.seq_step
        assumption

theorem RTC_SmallStep_of_BigStep {Ss t} (hS : Ss ⟹ t) :
    Ss ⇒* (Stmt.skip, t) :=
  by
    induction hS with
    | skip => exact RTC.refl
    | assign =>
      apply RTC.single
      apply SmallStep.assign
    | seq S T s t u hS hT ihS ihT =>
      apply RTC.trans
      { exact RTC_SmallStep_seq ihS }
      { apply RTC.head
        apply SmallStep.seq_skip
        assumption }
    | if_true B S T s t hB hst ih =>
      apply RTC.head
      { apply SmallStep.if_true
        assumption }
      { assumption }
    | if_false B S T s t hB hst ih =>
      apply RTC.head
      { apply SmallStep.if_false
        assumption }
      { assumption }
    | while_true B S s t u hB hS hw ihS ihw =>
      apply RTC.head
      { apply SmallStep.whileDo }
      { apply RTC.head
        { apply SmallStep.if_true
          assumption }
        { apply RTC.trans
          { exact RTC_SmallStep_seq ihS }
          { apply RTC.head
            apply SmallStep.seq_skip
            assumption } } }
    | while_false B S s hB =>
      apply RTC.tail
      apply RTC.single
      apply SmallStep.whileDo
      apply SmallStep.if_false
      assumption

theorem BigStep_of_SmallStep_of_BigStep {Ss₀ Ss₁ s₂}
      (h₁ : Ss₀ ⇒ Ss₁) :
    Ss₁ ⟹ s₂ → Ss₀ ⟹ s₂ :=
  by
    induction h₁ generalizing s₂ with
    | assign x a s               => simp
    | seq_step S S' T s s' hS ih => aesop
    | seq_skip T s               => simp
    | if_true B S T s hB         => aesop
    | if_false B S T s hB        => aesop
    | whileDo B S s              => aesop

theorem BigStep_of_RTC_SmallStep {Ss t} :
    Ss ⇒* (Stmt.skip, t) → Ss ⟹ t :=
  by
    intro hS
    induction hS using RTC.head_induction_on with
    | refl =>
      apply BigStep.skip
    | head Ss Ss' hST hsmallT ih =>
      cases Ss' with
      | mk S' s' =>
        apply BigStep_of_SmallStep_of_BigStep hST
        apply ih

theorem BigStep_Iff_RTC_SmallStep {Ss t} :
    Ss ⟹ t ↔ Ss ⇒* (Stmt.skip, t) :=
  Iff.intro RTC_SmallStep_of_BigStep BigStep_of_RTC_SmallStep

end LoVe
```

---



## Operational Semantics (ExerciseSheet) {#hhg-operational-semantics-exercisesheet}

> 📄 Source: https://raw.githubusercontent.com/lean-forward/logical_verification_2025/main/lean/LoVe/LoVe09_OperationalSemantics_ExerciseSheet.lean

```lean
import LoVe.LoVe09_OperationalSemantics_Demo
```


## LoVe Exercise 9: Operational Semantics

Replace the placeholders (e.g., `:= sorry`) with your solutions.


```lean
set_option autoImplicit false
set_option tactic.hygienic false

namespace LoVe
```


### Question 1: Guarded Command Language

In 1976, E. W. Dijkstra introduced the guarded command language (GCL), a
minimalistic imperative language with built-in nondeterminism. A grammar for one
of its variants is given below:

    S  ::=  x := e       -- assignment
         |  assert B     -- assertion
         |  S ; S        -- sequential composition
         |  S | ⋯ | S    -- nondeterministic choice
         |  loop S       -- nondeterministic iteration

Assignment and sequential composition are as in the WHILE language. The other
statements have the following semantics:

* `assert B` aborts if `B` evaluates to false; otherwise, the command is a
  no-op.

* `S | ⋯ | S` chooses any of the branches and executes it, ignoring the other
  branches.

* `loop S` executes `S` any number of times.

In Lean, GCL is captured by the following inductive type:


```lean
namespace GCL

inductive Stmt : Type
  | assign : String → (State → ℕ) → Stmt
  | assert : (State → Prop) → Stmt
  | seq    : Stmt → Stmt → Stmt
  | choice : List Stmt → Stmt
  | loop   : Stmt → Stmt

infixr:90 "; " => Stmt.seq
```


1.1. Complete the following big-step semantics, based on the informal
specification of GCL above.


```lean
inductive BigStep : (Stmt × State) → State → Prop
  -- enter the missing `assign` rule here
  | assert (B s) (hB : B s) :
    BigStep (Stmt.assert B, s) s
  -- enter the missing `seq` rule here
  -- below, `Ss[i]'hless` returns element `i` of `Ss`, which exists thanks to
  -- condition `hless`
  | choice (Ss s t i) (hless : i < List.length Ss)
      (hbody : BigStep (Ss[i]'hless, s) t) :
    BigStep (Stmt.choice Ss, s) t
  -- enter the missing `loop` rules here

infixl:110 " ⟹ " => BigStep
```


1.2. Prove the following inversion rules, as we did in the lecture for the
WHILE language.


```lean
@[simp] theorem BigStep_assign_iff {x a s t} :
    (Stmt.assign x a, s) ⟹ t ↔ t = s[x ↦ a s] :=
  sorry

@[simp] theorem BigStep_assert {B s t} :
    (Stmt.assert B, s) ⟹ t ↔ t = s ∧ B s :=
  sorry

@[simp] theorem BigStep_seq_iff {S₁ S₂ s t} :
    (Stmt.seq S₁ S₂, s) ⟹ t ↔ (∃u, (S₁, s) ⟹ u ∧ (S₂, u) ⟹ t) :=
  sorry

theorem BigStep_loop {S s u} :
    (Stmt.loop S, s) ⟹ u ↔
    (s = u ∨ (∃t, (S, s) ⟹ t ∧ (Stmt.loop S, t) ⟹ u)) :=
  sorry
```


This one is more difficult:


```lean
@[simp] theorem BigStep_choice {Ss s t} :
    (Stmt.choice Ss, s) ⟹ t ↔
    (∃(i : ℕ) (hless : i < List.length Ss), (Ss[i]'hless, s) ⟹ t) :=
  sorry

end GCL
```


1.3. Complete the translation below of a deterministic program to a GCL
program, by filling in the `sorry` placeholders below.


```lean
def gcl_of : Stmt → GCL.Stmt
  | Stmt.skip =>
    GCL.Stmt.assert (fun _ ↦ True)
  | Stmt.assign x a =>
    sorry
  | S; T =>
    sorry
  | Stmt.ifThenElse B S T  =>
    sorry
  | Stmt.whileDo B S =>
    sorry
```


1.4. In the definition of `gcl_of` above, `skip` is translated to
`assert (fun _ ↦ True)`. Looking at the big-step semantics of both constructs,
we can convince ourselves that it makes sense. Can you think of other correct
ways to define the `skip` case?


```lean
-- enter your answer here
```


### Question 2: Program Equivalence

For this question, we introduce the notion of program equivalence: `S₁ ~ S₂`.


```lean
def BigStepEquiv (S₁ S₂ : Stmt) : Prop :=
  ∀s t, (S₁, s) ⟹ t ↔ (S₂, s) ⟹ t

infix:50 (priority := high) " ~ " => BigStepEquiv
```


Program equivalence is an equivalence relation, i.e., it is reflexive,
symmetric, and transitive.


```lean
theorem BigStepEquiv.refl {S} :
    S ~ S :=
  fix s t : State
  show (S, s) ⟹ t ↔ (S, s) ⟹ t from
    by rfl

theorem BigStepEquiv.symm {S₁ S₂} :
    S₁ ~ S₂ → S₂ ~ S₁ :=
  assume h : S₁ ~ S₂
  fix s t : State
  show (S₂, s) ⟹ t ↔ (S₁, s) ⟹ t from
    Iff.symm (h s t)

theorem BigStepEquiv.trans {S₁ S₂ S₃} (h₁₂ : S₁ ~ S₂) (h₂₃ : S₂ ~ S₃) :
    S₁ ~ S₃ :=
  fix s t : State
  show (S₁, s) ⟹ t ↔ (S₃, s) ⟹ t from
    Iff.trans (h₁₂ s t) (h₂₃ s t)
```


2.1. Prove the following program equivalences.


```lean
theorem BigStepEquiv.skip_assign_id {x} :
    Stmt.assign x (fun s ↦ s x) ~ Stmt.skip :=
  sorry

theorem BigStepEquiv.seq_skip_left {S} :
    Stmt.skip; S ~ S :=
  sorry

theorem BigStepEquiv.seq_skip_right {S} :
    S; Stmt.skip ~ S :=
  sorry

theorem BigStepEquiv.if_seq_while_skip {B S} :
    Stmt.ifThenElse B (S; Stmt.whileDo B S) Stmt.skip ~ Stmt.whileDo B S :=
  sorry
```


2.2 (**optional**). Program equivalence can be used to replace subprograms
by other subprograms with the same semantics. Prove the following so-called
congruence rules that facilitate such replacement:


```lean
theorem BigStepEquiv.seq_congr {S₁ S₂ T₁ T₂} (hS : S₁ ~ S₂)
      (hT : T₁ ~ T₂) :
    S₁; T₁ ~ S₂; T₂ :=
  sorry

theorem BigStepEquiv.if_congr {B S₁ S₂ T₁ T₂} (hS : S₁ ~ S₂) (hT : T₁ ~ T₂) :
    Stmt.ifThenElse B S₁ T₁ ~ Stmt.ifThenElse B S₂ T₂ :=
  sorry

end LoVe
```

---



## Operational Semantics (HomeworkSheet) {#hhg-operational-semantics-homeworksheet}

> 📄 Source: https://raw.githubusercontent.com/lean-forward/logical_verification_2025/main/lean/LoVe/LoVe09_OperationalSemantics_HomeworkSheet.lean

```lean
import LoVe.LoVe02_ProgramsAndTheorems_Demo
```


## LoVe Homework 9 (10 points + 1 bonus point): Operational Semantics

Replace the placeholders (e.g., `:= sorry`) with your solutions.


```lean
set_option autoImplicit false
set_option tactic.hygienic false

namespace LoVe
```


### Question 1 (5 points): Arithmetic Expressions

Recall the type of arithmetic expressions from lecture 1 and its evaluation
function:


```lean
#check AExp
#check eval
```


Let us introduce the following abbreviation for an environment that maps
variable names to values:


```lean
def Envir : Type :=
  String → ℤ
```


1.1 (2 points). Complete the following Lean definition of a big-step-style
semantics for arithmetic expressions. The predicate `BigStep` (`⟹`) relates
an arithmetic expression, an environment, and the value to which the expression
evaluates in the given environment:


```lean
inductive BigStep : AExp × Envir → ℤ → Prop
  | num (i env) : BigStep (AExp.num i, env) i

infix:60 " ⟹ " => BigStep
```


1.2 (1 point). Prove the following theorem to validate your definition
above.

Hint: It may help to first prove
`(AExp.add (AExp.num 2) (AExp.num 2), env) ⟹ 2 + 2`.


```lean
theorem BigStep_add_two_two (env : Envir) :
    (AExp.add (AExp.num 2) (AExp.num 2), env) ⟹ 4 :=
  sorry
```


1.3 (2 points). Prove that the big-step semantics is sound with respect to
the `eval` function:


```lean
theorem BigStep_sound (aenv : AExp × Envir) (i : ℤ) (hstep : aenv ⟹ i) :
    eval (Prod.snd aenv) (Prod.fst aenv) = i :=
  sorry
```


### Question 2 (5 points + 1 bonus point): Semantics of Regular Expressions

Regular expressions are a very popular tool for software development. Often,
when textual input needs to be analyzed it is matched against a regular
expression. In this question, we define the syntax of regular expressions and
what it means for a regular expression to match a string.

We define `Regex` to represent the following grammar:

    R  ::=  ∅       -- `nothing`: matches nothing
         |  ε       -- `empty`: matches the empty string
         |  a       -- `atom`: matches the atom `a`
         |  R ⬝ R    -- `concat`: matches the concatenation of two regexes
         |  R + R   -- `alt`: matches either of two regexes
         |  R*      -- `star`: matches arbitrary many repetitions of a Regex

Notice the rough correspondence with a WHILE language:

    `empty`  ~ `skip`
    `atom`   ~ assignment
    `concat` ~ sequential composition
    `alt`    ~ conditional statement
    `star`   ~ while loop


```lean
inductive Regex (α : Type) : Type
  | nothing : Regex α
  | empty   : Regex α
  | atom    : α → Regex α
  | concat  : Regex α → Regex α → Regex α
  | alt     : Regex α → Regex α → Regex α
  | star    : Regex α → Regex α
```


The `Matches r s` predicate indicates that the regular expression `r` matches
the string `s` (where the string is a sequence of atoms).


```lean
inductive Matches {α : Type} : Regex α → List α → Prop
| empty :
  Matches Regex.empty []
| atom (a : α) :
  Matches (Regex.atom a) [a]
| concat (r₁ r₂ : Regex α) (s₁ s₂ : List α) (h₁ : Matches r₁ s₁)
    (h₂ : Matches r₂ s₂) :
  Matches (Regex.concat r₁ r₂) (s₁ ++ s₂)
| alt_left (r₁ r₂ : Regex α) (s : List α) (h : Matches r₁ s) :
  Matches (Regex.alt r₁ r₂) s
| alt_right (r₁ r₂ : Regex α) (s : List α) (h : Matches r₂ s) :
  Matches (Regex.alt r₁ r₂) s
| star_base (r : Regex α) :
  Matches (Regex.star r) []
| star_step (r : Regex α) (s s' : List α) (h₁ : Matches r s)
    (h₂ : Matches (Regex.star r) s') :
  Matches (Regex.star r) (s ++ s')
```


The introduction rules correspond to the following cases:

* match the empty string
* match one atom (e.g., character)
* match two concatenated regexes
* match the left option
* match the right option
* match the empty string (the base case of `R*`)
* match `R` followed again by `R*` (the induction step of `R*`)

2.1 (1 point). Explain why there is no rule for `nothing`.


```lean
-- enter your answer here
```


2.2 (4 points). Prove the following inversion rules.


```lean
@[simp] theorem Matches_atom {α : Type} {s : List α} {a : α} :
    Matches (Regex.atom a) s ↔ s = [a] :=
  sorry

@[simp] theorem Matches_nothing {α : Type} {s : List α} :
    ¬ Matches Regex.nothing s :=
  sorry

@[simp] theorem Matches_empty {α : Type} {s : List α} :
    Matches Regex.empty s ↔ s = [] :=
  sorry

@[simp] theorem Matches_concat {α : Type} {s : List α} {r₁ r₂ : Regex α} :
    Matches (Regex.concat r₁ r₂) s
    ↔ (∃s₁ s₂, Matches r₁ s₁ ∧ Matches r₂ s₂ ∧ s = s₁ ++ s₂) :=
  sorry

@[simp] theorem Matches_alt {α : Type} {s : List α} {r₁ r₂ : Regex α} :
    Matches (Regex.alt r₁ r₂) s ↔ (Matches r₁ s ∨ Matches r₂ s) :=
  sorry
```


2.3 (1 bonus point). Prove the following inversion rule.


```lean
theorem Matches_star {α : Type} {s : List α} {r : Regex α} :
    Matches (Regex.star r) s ↔
    s = []
    ∨ (∃s₁ s₂, Matches r s₁ ∧ Matches (Regex.star r) s₂ ∧ s = s₁ ++ s₂) :=
  sorry

end LoVe
```

---



## Hoare Logic (Demo) {#hhg-hoare-logic-demo}

> 📄 Source: https://raw.githubusercontent.com/lean-forward/logical_verification_2025/main/lean/LoVe/LoVe10_HoareLogic_Demo.lean

```lean
import LoVe.LoVe08_Metaprogramming_Demo
import LoVe.LoVe09_OperationalSemantics_Demo
```


## LoVe Demo 10: Hoare Logic

We review a second way to specify the semantics of a programming language: Hoare
logic. If operational semantics corresponds to an idealized interpreter,
__Hoare logic__ (also called __axiomatic semantics__) corresponds to a verifier.
Hoare logic is particularly convenient to reason about concrete programs.


```lean
set_option autoImplicit false
set_option tactic.hygienic false

open Lean
open Lean.Meta
open Lean.Elab.Tactic

namespace LoVe
```


### Hoare Triples

The basic judgments of Hoare logic are often called __Hoare triples__. They have
the form

    `{P} S {Q}`

where `S` is a statement, and `P` and `Q` (called __precondition__ and
__postcondition__) are logical formulas over the state variables.

Intended meaning:

    If `P` holds before `S` is executed and the execution terminates normally,
    `Q` holds at termination.

This is a __partial correctness__ statement: The program is correct if it
terminates normally (i.e., no run-time error, no infinite loop or divergence).

All of these Hoare triples are valid (with respect to the intended meaning):

    `{True} b := 4 {b = 4}`
    `{a = 2} b := 2 * a {a = 2 ∧ b = 4}`
    `{b ≥ 5} b := b + 1 {b ≥ 6}`
    `{False} skip {b = 100}`
    `{True} while i ≠ 100 do i := i + 1 {i = 100}`


### Hoare Rules

The following is a complete set of rules for reasoning about WHILE programs:

    ———————————— Skip
    {P} skip {P}

    ——————————————————— Assign
    {Q[a/x]} x := a {Q}

    {P} S {R}   {R} S' {Q}
    —————————————————————— Seq
    {P} S; S' {Q}

    {P ∧ B} S {Q}   {P ∧ ¬B} S' {Q}
    ——————————————————————————————— If
    {P} if B then S else S' {Q}

    {P ∧ B} S {P}
    ————————————————————————— While
    {P} while B do S {P ∧ ¬B}

    P' → P   {P} S {Q}   Q → Q'
    ——————————————————————————— Conseq
    {P'} S {Q'}

`Q[a/x]` denotes `Q` with `x` replaced by `a`.

In the `While` rule, `P` is called an __invariant__.

Except for `Conseq`, the rules are syntax-driven: by looking at a program, we
see immediately which rule to apply.

Example derivations:

    —————————————————————— Assign   —————————————————————— Assign
    {a = 2} b := a {b = 2}          {b = 2} c := b {c = 2}
    —————————————————————————————————————————————————————— Seq
    {a = 2} b := a; c := b {c = 2}


                     —————————————————————— Assign
    x > 10 → x > 5   {x > 5} y := x {y > 5}   y > 5 → y > 0
    ——————————————————————————————————————————————————————— Conseq
    {x > 10} y := x {y > 0}

Various __derived rules__ can be proved to be correct in terms of the standard
rules. For example, we can derive bidirectional rules for `skip`, `:=`, and
`while`:

    P → Q
    ———————————— Skip'
    {P} skip {Q}

    P → Q[a/x]
    —————————————— Assign'
    {P} x := a {Q}

    {P ∧ B} S {P}   P ∧ ¬B → Q
    —————————————————————————— While'
    {P} while B do S {Q}


### A Semantic Approach to Hoare Logic

We can, and will, define Hoare triples **semantically** in Lean.

We will use predicates on states (`State → Prop`) to represent pre- and
postconditions, following the shallow embedding style.


```lean
def PartialHoare (P : State → Prop) (S : Stmt)
    (Q : State → Prop) : Prop :=
  ∀s t, P s → (S, s) ⟹ t → Q t

macro "{*" P:term " *} " "(" S:term ")" " {* " Q:term " *}" : term =>
  `(PartialHoare $P $S $Q)

namespace PartialHoare

theorem skip_intro {P} :
    {* P *} (Stmt.skip) {* P *} :=
  by
    intro s t hs hst
    cases hst
    assumption

theorem assign_intro (P) {x a} :
    {* fun s ↦ P (s[x ↦ a s]) *} (Stmt.assign x a) {* P *} :=
  by
    intro s t P' hst
    cases hst with
    | assign => assumption

theorem seq_intro {P Q R S T} (hS : {* P *} (S) {* Q *})
      (hT : {* Q *} (T) {* R *}) :
    {* P *} (S; T) {* R *} :=
  by
    intro s t hs hst
    cases hst with
    | seq _ _ _ u d hS' hT' =>
      apply hT
      { apply hS
        { exact hs }
        { assumption } }
      { assumption }

theorem if_intro {B P Q S T}
      (hS : {* fun s ↦ P s ∧ B s *} (S) {* Q *})
      (hT : {* fun s ↦ P s ∧ ¬ B s *} (T) {* Q *}) :
    {* P *} (Stmt.ifThenElse B S T) {* Q *} :=
  by
    intro s t hs hst
    cases hst with
    | if_true _ _ _ _ _ hB hS' =>
      apply hS
      exact And.intro hs hB
      assumption
    | if_false _ _ _ _ _ hB hT' =>
      apply hT
      exact And.intro hs hB
      assumption

theorem while_intro (P) {B S}
      (h : {* fun s ↦ P s ∧ B s *} (S) {* P *}) :
    {* P *} (Stmt.whileDo B S) {* fun s ↦ P s ∧ ¬ B s *} :=
  by
    intro s t hs hst
    generalize ws_eq : (Stmt.whileDo B S, s) = Ss
    rw [ws_eq] at hst
    induction hst generalizing s with
    | skip s'                       => cases ws_eq
    | assign x a s'                 => cases ws_eq
    | seq S T s' t' u hS hT ih      => cases ws_eq
    | if_true B S T s' t' hB hS ih  => cases ws_eq
    | if_false B S T s' t' hB hT ih => cases ws_eq
    | while_true B' S' s' t' u hB' hS hw ih_hS ih_hw =>
      cases ws_eq
      apply ih_hw
      { apply h
        { apply And.intro <;>
            assumption }
        { exact hS } }
      { rfl }
    | while_false B' S' s' hB'      =>
      cases ws_eq
      aesop

theorem consequence {P P' Q Q' S}
      (h : {* P *} (S) {* Q *}) (hp : ∀s, P' s → P s)
      (hq : ∀s, Q s → Q' s) :
    {* P' *} (S) {* Q' *} :=
  fix s t : State
  assume hs : P' s
  assume hst : (S, s) ⟹ t
  show Q' t from
    hq _ (h s t (hp s hs) hst)

theorem consequence_left (P') {P Q S}
      (h : {* P *} (S) {* Q *}) (hp : ∀s, P' s → P s) :
    {* P' *} (S) {* Q *} :=
  consequence h hp (by aesop)

theorem consequence_right (Q) {Q' P S}
      (h : {* P *} (S) {* Q *}) (hq : ∀s, Q s → Q' s) :
    {* P *} (S) {* Q' *} :=
  consequence h (by aesop) hq

theorem skip_intro' {P Q} (h : ∀s, P s → Q s) :
    {* P *} (Stmt.skip) {* Q *} :=
  consequence skip_intro h (by aesop)

theorem assign_intro' {P Q x a}
      (h : ∀s, P s → Q (s[x ↦ a s])):
    {* P *} (Stmt.assign x a) {* Q *} :=
  consequence (assign_intro Q) h (by aesop)

theorem seq_intro' {P Q R S T} (hT : {* Q *} (T) {* R *})
      (hS : {* P *} (S) {* Q *}) :
    {* P *} (S; T) {* R *} :=
  seq_intro hS hT

theorem while_intro' {B P Q S} (I)
      (hS : {* fun s ↦ I s ∧ B s *} (S) {* I *})
      (hP : ∀s, P s → I s)
      (hQ : ∀s, ¬ B s → I s → Q s) :
    {* P *} (Stmt.whileDo B S) {* Q *} :=
  consequence (while_intro I hS) hP (by aesop)

theorem assign_intro_forward (P) {x a} :
    {* P *}
    (Stmt.assign x a)
    {* fun s ↦ ∃n₀, P (s[x ↦ n₀])
       ∧ s x = a (s[x ↦ n₀]) *} :=
  by
    apply assign_intro'
    intro s hP
    apply Exists.intro (s x)
    simp [*]

theorem assign_intro_backward (Q) {x a} :
    {* fun s ↦ ∃n', Q (s[x ↦ n']) ∧ n' = a s *}
    (Stmt.assign x a)
    {* Q *} :=
  by
    apply assign_intro'
    intro s hP
    cases hP with
    | intro n' hQ => aesop

end PartialHoare
```


### First Program: Exchanging Two Variables


```lean
def SWAP : Stmt :=
  Stmt.assign "t" (fun s ↦ s "a");
  Stmt.assign "a" (fun s ↦ s "b");
  Stmt.assign "b" (fun s ↦ s "t")

theorem SWAP_correct (a₀ b₀ : ℕ) :
    {* fun s ↦ s "a" = a₀ ∧ s "b" = b₀ *}
    (SWAP)
    {* fun s ↦ s "a" = b₀ ∧ s "b" = a₀ *} :=
  by
    apply PartialHoare.seq_intro'
    apply PartialHoare.seq_intro'
    apply PartialHoare.assign_intro
    apply PartialHoare.assign_intro
    apply PartialHoare.assign_intro'
    aesop
```


### Second Program: Adding Two Numbers


```lean
def ADD : Stmt :=
  Stmt.whileDo (fun s ↦ s "n" ≠ 0)
    (Stmt.assign "n" (fun s ↦ s "n" - 1);
     Stmt.assign "m" (fun s ↦ s "m" + 1))

theorem ADD_correct (n₀ m₀ : ℕ) :
    {* fun s ↦ s "n" = n₀ ∧ s "m" = m₀ *}
    (ADD)
    {* fun s ↦ s "n" = 0 ∧ s "m" = n₀ + m₀ *} :=
  PartialHoare.while_intro' (fun s ↦ s "n" + s "m" = n₀ + m₀)
    (by
      apply PartialHoare.seq_intro'
      { apply PartialHoare.assign_intro }
      { apply PartialHoare.assign_intro'
        aesop })
    (by aesop)
    (by aesop)
```


How did we come up with this invariant? The invariant must

1. be true before we enter the loop;

2. remain true after each iteration of the loop if it was true before the
   iteration;

3. be strong enough to imply the desired loop postcondition.

The invariant `True` meets 1 and 2 but usually not 3. Similarly, `False` meets
2 and 3 but usually not 1. Suitable invariants are often of the form

__work done__ + __work remaining__ = __desired result__

where `+` is some suitable operator. When we enter the loop, __work done__ will
often be `0`. And when we exit the loop, __work remaining__ should be `0`.

For the `ADD` loop:

* __work done__ is `m`;
* __work remaining__ is `n`;
* __desired result__ is `n₀ + m₀`.


### A Verification Condition Generator

__Verification condition generators__ (VCGs) are programs that apply Hoare rules
automatically, producing __verification conditions__ that must be proved by the
user. The user must usually also provide strong enough loop invariants, as an
annotation in their programs.

We can use Lean's metaprogramming framework to define a simple VCG.

Hundreds of program verification tools are based on these principles.

VCGs typically work backwards from the postcondition, using backward rules
(rules stated to have an arbitrary `Q` as their postcondition). This works well
because `Assign` is backward.


```lean
def Stmt.invWhileDo (I B : State → Prop) (S : Stmt) : Stmt :=
  Stmt.whileDo B S

namespace PartialHoare

theorem invWhile_intro {B I Q S}
      (hS : {* fun s ↦ I s ∧ B s *} (S) {* I *})
      (hQ : ∀s, ¬ B s → I s → Q s) :
    {* I *} (Stmt.invWhileDo I B S) {* Q *} :=
  while_intro' I hS (by aesop) hQ

theorem invWhile_intro' {B I P Q S}
      (hS : {* fun s ↦ I s ∧ B s *} (S) {* I *})
      (hP : ∀s, P s → I s) (hQ : ∀s, ¬ B s → I s → Q s) :
    {* P *} (Stmt.invWhileDo I B S) {* Q *} :=
  while_intro' I hS hP hQ

end PartialHoare

def matchPartialHoare : Expr → Option (Expr × Expr × Expr)
  | (Expr.app (Expr.app (Expr.app
       (Expr.const ``PartialHoare _) P) S) Q) =>
    Option.some (P, S, Q)
  | _ =>
    Option.none

partial def vcg : TacticM Unit :=
  do
    let goals ← getUnsolvedGoals
    if goals.length != 0 then
      let target ← getMainTarget
      match matchPartialHoare target with
      | Option.none           => return
      | Option.some (P, S, Q) =>
        if Expr.isAppOfArity S ``Stmt.skip 0 then
          if Expr.isMVar P then
            applyConstant ``PartialHoare.skip_intro
          else
            applyConstant ``PartialHoare.skip_intro'
        else if Expr.isAppOfArity S ``Stmt.assign 2 then
          if Expr.isMVar P then
            applyConstant ``PartialHoare.assign_intro
          else
            applyConstant ``PartialHoare.assign_intro'
        else if Expr.isAppOfArity S ``Stmt.seq 2 then
          andThenOnSubgoals
            (applyConstant ``PartialHoare.seq_intro') vcg
        else if Expr.isAppOfArity S ``Stmt.ifThenElse 3 then
          andThenOnSubgoals
            (applyConstant ``PartialHoare.if_intro) vcg
        else if Expr.isAppOfArity S ``Stmt.invWhileDo 3 then
          if Expr.isMVar P then
            andThenOnSubgoals
              (applyConstant ``PartialHoare.invWhile_intro) vcg
          else
            andThenOnSubgoals
              (applyConstant ``PartialHoare.invWhile_intro')
              vcg
        else
          failure

elab "vcg" : tactic =>
  vcg
```


### Second Program Revisited: Adding Two Numbers


```lean
theorem ADD_correct_vcg (n₀ m₀ : ℕ) :
    {* fun s ↦ s "n" = n₀ ∧ s "m" = m₀ *}
    (ADD)
    {* fun s ↦ s "n" = 0 ∧ s "m" = n₀ + m₀ *} :=
  show {* fun s ↦ s "n" = n₀ ∧ s "m" = m₀ *}
     (Stmt.invWhileDo (fun s ↦ s "n" + s "m" = n₀ + m₀)
        (fun s ↦ s "n" ≠ 0)
        (Stmt.assign "n" (fun s ↦ s "n" - 1);
         Stmt.assign "m" (fun s ↦ s "m" + 1)))
     {* fun s ↦ s "n" = 0 ∧ s "m" = n₀ + m₀ *} from
  by
    vcg <;>
      aesop
```


### Hoare Triples for Total Correctness

__Total correctness__ asserts that the program not only is partially correct but
also that it always terminates normally. Hoare triples for total correctness
have the form

    [P] S [Q]

Intended meaning:

    If `P` holds before `S` is executed, the execution terminates normally and
    `Q` holds in the final state.

For deterministic programs, an equivalent formulation is as follows:

    If `P` holds before `S` is executed, there exists a state in which execution
    terminates normally and `Q` holds in that state.

Example:

    `[i ≤ 10] while i ≠ 10 do i := i + 1 [i = 10]`

In our WHILE language, this only affects while loops, which must now be
annotated by a __variant__ `V` (a natural number that decreases with each
iteration):

    [I ∧ B ∧ V = v₀] S [I ∧ V < v₀]
    ——————————————————————————————— While-Var
    [I] while B do S [I ∧ ¬B]

What is a suitable variant for the example above?


```lean
end LoVe
```

---



## Hoare Logic (ExerciseSheet) {#hhg-hoare-logic-exercisesheet}

> 📄 Source: https://raw.githubusercontent.com/lean-forward/logical_verification_2025/main/lean/LoVe/LoVe10_HoareLogic_ExerciseSheet.lean

```lean
import LoVe.LoVe10_HoareLogic_Demo
```


## LoVe Exercise 10: Hoare Logic

Replace the placeholders (e.g., `:= sorry`) with your solutions.


```lean
set_option autoImplicit false
set_option tactic.hygienic false

namespace LoVe
```


### Question 1: Program Verification

1.1. The following WHILE program takes two numbers `a` and `b` and increments
`b` until it reaches `a`:


```lean
def COUNT_UP : Stmt :=
  Stmt.whileDo (fun s ↦ s "b" ≠ s "a")
    (Stmt.assign "b" (fun s ↦ s "b" + 1))
```


Prove the following Hoare triple. The main difficulty is to figure out which
invariant to use for the while loop. The invariant should capture both the work
that has been done already (the intermediate result) and the work that remains
to be done. Use a `show` command to annotate the program with a loop invariant.

Hint: If a variable `x` does not change in a program, it might be useful to
record this in the invariant, by adding a conjunct `s "x" = x₀`.


```lean
theorem COUNT_UP_correct (a₀ : ℕ) :
    {* fun s ↦ s "a" = a₀ *} (COUNT_UP) {* fun s ↦ s "a" = a₀ ∧ s "b" = a₀ *} :=
  sorry
```


1.2. What happens if the program is run with `b > a`? How is this captured
by the Hoare triple?


```lean
-- enter your solution here
```


1.3. The following WHILE program is intended to compute the Gaussian sum up
to `n`, leaving the result in `r`.


```lean
def GAUSS (N : ℕ) : Stmt :=
  Stmt.assign "r" (fun s ↦ 0);
  Stmt.assign "n" (fun s ↦ 0);
  Stmt.whileDo (fun s ↦ s "n" ≠ N)
    (Stmt.assign "n" (fun s ↦ s "n" + 1);
     Stmt.assign "r" (fun s ↦ s "r" + s "n"))
```


Here is a functional implementation of the same function:


```lean
def sumUpTo : ℕ → ℕ
  | 0     => 0
  | n + 1 => n + 1 + sumUpTo n
```


Invoke `vcg` on `GAUSS` using a suitable loop invariant and prove the
emerging verification conditions.


```lean
theorem GAUSS_correct (N : ℕ) :
    {* fun s ↦ True *} (GAUSS N) {* fun s ↦ s "r" = sumUpTo N *} :=
  sorry
```


1.4 (**optional**). The following program `MUL` is intended to compute the
product of `n` and `m`, leaving the result in `r`. Invoke `vcg` on `MUL` using a
suitable loop invariant and prove the emerging verification conditions.


```lean
def MUL : Stmt :=
  Stmt.assign "r" (fun s ↦ 0);
  Stmt.whileDo (fun s ↦ s "n" ≠ 0)
    (Stmt.assign "r" (fun s ↦ s "r" + s "m");
     Stmt.assign "n" (fun s ↦ s "n" - 1))

theorem MUL_correct (n₀ m₀ : ℕ) :
    {* fun s ↦ s "n" = n₀ ∧ s "m" = m₀ *} (MUL) {* fun s ↦ s "r" = n₀ * m₀ *} :=
  sorry
```


### Question 2: Hoare Triples for Total Correctness

The following definition captures Hoare triples for total correctness for
deterministic languages:


```lean
def TotalHoare (P : State → Prop) (S : Stmt) (Q : State → Prop) : Prop :=
  ∀s, P s → ∃t, (S, s) ⟹ t ∧ Q t

macro "[*" P:term " *] " "(" S:term ")" " [* " Q:term " *]" : term =>
  `(TotalHoare $P $S $Q)

namespace TotalHoare
```


2.1. Prove the consequence rule.


```lean
theorem consequence {P P' Q Q' S}
      (hS : [* P *] (S) [* Q *]) (hP : ∀s, P' s → P s) (hQ : ∀s, Q s → Q' s) :
    [* P' *] (S) [* Q' *] :=
  sorry
```


2.2. Prove the rule for `skip`.


```lean
theorem skip_intro {P} :
    [* P *] (Stmt.skip) [* P *] :=
  sorry
```


2.3. Prove the rule for `assign`.


```lean
theorem assign_intro {P x a} :
    [* fun s ↦ P (s[x ↦ a s]) *] (Stmt.assign x a) [* P *] :=
  sorry
```


2.4. Prove the rule for `seq`.


```lean
theorem seq_intro {P Q R S T} (hS : [* P *] (S) [* Q *])
      (hT : [* Q *] (T) [* R *]) :
    [* P *] (S; T) [* R *] :=
  sorry
```


2.5. Complete the proof of the rule for `if`–`then`–`else`.

Hint: The proof requires a case distinction on the truth value of `B s`.


```lean
theorem if_intro {B P Q S T}
      (hS : [* fun s ↦ P s ∧ B s *] (S) [* Q *])
      (hT : [* fun s ↦ P s ∧ ¬ B s *] (T) [* Q *]) :
    [* P *] (Stmt.ifThenElse B S T) [* Q *] :=
  sorry
```


2.6 (**optional**). Try to prove the rule for `while`.

The rule is parameterized by a loop invariant `I` and by a variant `V` that
decreases with each iteration of the loop body.

Before we prove the desired theorem, we introduce an auxiliary theorem. Its
proof requires induction by pattern matching and recursion. When using
`var_while_intro_aux` as induction hypothesis we recommend to do it directly
after proving that the argument is less than `v₀`:

    have ih : ∃u, (Stmt.whileDo B S, t) ⟹ u ∧ I u ∧ ¬ B u :=
      have _ : V t < v₀ :=
        …
      var_while_intro_aux I V h_inv (V t) …

Similarly to `if`--`then`--`else`, the proof requires a case distinction on the
truth value of `B s`.


```lean
theorem var_while_intro_aux {B} (I : State → Prop) (V : State → ℕ) {S}
    (h_inv : ∀v₀,
       [* fun s ↦ I s ∧ B s ∧ V s = v₀ *] (S) [* fun s ↦ I s ∧ V s < v₀ *]) :
    ∀v₀ s, V s = v₀ → I s → ∃t, (Stmt.whileDo B S, s) ⟹ t ∧ I t ∧ ¬ B t
  | v₀, s, V_eq, hs =>
    sorry

theorem var_while_intro {B} (I : State → Prop) (V : State → ℕ) {S}
    (hinv : ∀v₀,
       [* fun s ↦ I s ∧ B s ∧ V s = v₀ *] (S) [* fun s ↦ I s ∧ V s < v₀ *]) :
    [* I *] (Stmt.whileDo B S) [* fun s ↦ I s ∧ ¬ B s *] :=
  sorry

end TotalHoare

end LoVe
```

---



## Hoare Logic (HomeworkSheet) {#hhg-hoare-logic-homeworksheet}

> 📄 Source: https://raw.githubusercontent.com/lean-forward/logical_verification_2025/main/lean/LoVe/LoVe10_HoareLogic_HomeworkSheet.lean

```lean
import LoVe.LoVe09_OperationalSemantics_ExerciseSheet
import LoVe.LoVe10_HoareLogic_Demo
```


## LoVe Homework 10 (10 points + 1 bonus point): Hoare Logic

Replace the placeholders (e.g., `:= sorry`) with your solutions.


```lean
set_option autoImplicit false
set_option tactic.hygienic false

namespace LoVe
```


### Question 1 (5 points): Factorial

The following WHILE program is intended to compute the factorial of `n₀`, leaving
the result in `r`.


```lean
def FACT : Stmt :=
  Stmt.assign "i" (fun s ↦ 0);
  Stmt.assign "r" (fun s ↦ 1);
  Stmt.whileDo (fun s ↦ s "i" ≠ s "n")
    (Stmt.assign "i" (fun s ↦ s "i" + 1);
     Stmt.assign "r" (fun s ↦ s "r" * s "i"))
```


Recall the definition of the `fact` function:


```lean
#print fact
```


Let us register its recursive equations as simplification rules to
strengthen the simplifier and `aesop`, using some new Lean syntax:


```lean
attribute [simp] fact
```


Prove the correctness of `FACT` using `vcg`.

Hint: Remember to strengthen the loop invariant with `s "n" = n₀` to
capture the fact that the variable `n` does not change.


```lean
theorem FACT_correct (n₀ : ℕ) :
    {* fun s ↦ s "n" = n₀ *} (FACT) {* fun s ↦ s "r" = fact n₀ *} :=
  sorry
```


### Question 2 (5 points + 1 bonus point):
### Hoare Logic for the Guarded Command Language

Recall the definition of GCL from exercise 9:


```lean
namespace GCL

#check Stmt
#check BigStep
```


The definition of Hoare triples for partial correctness is unsurprising:


```lean
def PartialHoare (P : State → Prop) (S : Stmt) (Q : State → Prop) : Prop :=
  ∀s t, P s → (S, s) ⟹ t → Q t

macro (priority := high) "{*" P:term " *} " "(" S:term ")" " {* " Q:term " *}" :
  term =>
  `(PartialHoare $P $S $Q)

namespace PartialHoare
```


2.1 (5 points). Prove the following Hoare rules:


```lean
theorem consequence {P P' Q Q' S} (h : {* P *} (S) {* Q *})
      (hp : ∀s, P' s → P s) (hq : ∀s, Q s → Q' s) :
    {* P' *} (S) {* Q' *} :=
  sorry

theorem assign_intro {P x a} :
    {* fun s ↦ P (s[x ↦ a s]) *} (Stmt.assign x a) {* P *} :=
  sorry

theorem assert_intro {P Q : State → Prop} :
    {* fun s ↦ Q s → P s *} (Stmt.assert Q) {* P *} :=
  sorry

theorem seq_intro {P Q R S T}
      (hS : {* P *} (S) {* Q *}) (hT : {* Q *} (T) {* R *}) :
    {* P *} (Stmt.seq S T) {* R *} :=
  sorry

theorem choice_intro {P Q Ss}
      (h : ∀i (hi : i < List.length Ss), {* P *} (Ss[i]'hi) {* Q *}) :
    {* P *} (Stmt.choice Ss) {* Q *} :=
  sorry
```


2.2 (1 bonus point). Prove the rule for `loop`. Notice the similarity with
the rule for `while` in the WHILE language.


```lean
theorem loop_intro {P S} (h : {* P *} (S) {* P *}) :
    {* P *} (Stmt.loop S) {* P *} :=
  sorry

end PartialHoare

end GCL

end LoVe
```

---



## Denotational Semantics (Demo) {#hhg-denotational-semantics-demo}

> 📄 Source: https://raw.githubusercontent.com/lean-forward/logical_verification_2025/main/lean/LoVe/LoVe11_DenotationalSemantics_Demo.lean

```lean
import LoVe.LoVe09_OperationalSemantics_Demo
```


## LoVe Demo 11: Denotational Semantics

We review a third way to specify the semantics of a programming language:
denotational semantics. Denotational semantics attempts to directly specify the
meaning of programs.

If operational semantics is an idealized interpreter and Hoare logic is an
idealized verifier, then denotational semantics is an idealized compiler.


```lean
set_option autoImplicit false
set_option tactic.hygienic false

namespace LoVe
```


### Compositionality

A __denotational semantics__ defines the meaning of each program as a
mathematical object:

    `⟦ ⟧ : syntax → semantics`

A key property of denotational semantics is __compositionality__: The meaning of
a compound statement should be defined in terms of the meaning of its
components. This disqualifies

    `⟦S⟧ = {st | (S, Prod.fst st) ⟹ Prod.snd st}`

(i.e.

    `⟦S⟧ = {(s, t) | (S, s) ⟹ t}`)

because operational semantics is not compositional.

In short, we want

    `⟦S; T⟧               = … ⟦S⟧ … ⟦T⟧ …`
    `⟦if B then S else T⟧ = … ⟦S⟧ … ⟦T⟧ …`
    `⟦while B do S⟧       = … ⟦S⟧ …`

An evaluation function on arithmetic expressions

    `eval : AExp → ((String → ℤ) → ℤ)`

is a denotational semantics. We want the same for imperative programs.


### A Relational Denotational Semantics

We can represent the semantics of an imperative program as a function from
initial state to final state or more generally as a relation between initial
state and final state: `Set (State × State)`.

For `skip`, `:=`, `;`, and `if then else`, the denotational semantics is
easy:


```lean
namespace SorryDefs

def denote : Stmt → Set (State × State)
  | Stmt.skip             => Id
  | Stmt.assign x a       =>
    {st | Prod.snd st = (Prod.fst st)[x ↦ a (Prod.fst st)]}
  | Stmt.seq S T          => denote S ◯ denote T
  | Stmt.ifThenElse B S T =>
    (denote S ⇃ B) ∪ (denote T ⇃ (fun s ↦ ¬ B s))
  | Stmt.whileDo B S      => sorry

end SorryDefs
```


We write `⟦S⟧` for `denote S`. For `while`, we would like to write

    `((denote S ◯ denote (Stmt.whileDo B S)) ⇃ B)`
    `∪ (Id ⇃ (fun s ↦ ¬ B s))`

but this is ill-founded due to the recursive call on `Stmt.whileDo B S`.

What we are looking for is an `X` such that

    `X = ((denote S ◯ X) ⇃ B) ∪ (Id ⇃ (fun s ↦ ¬ B s))`

In other words, we are looking for a fixpoint.

Most of this lecture is concerned with building a least fixpoint operator
`lfp` that will allow us to define the `while` case as well:

    `lfp (fun X ↦ ((denote S ◯ X) ⇃ B) ∪ (Id ⇃ (fun s ↦ ¬ B s)))`


### Fixpoints

A __fixpoint__ (or fixed point) of `f` is a solution for `X` in the equation

    `X = f X`

In general, fixpoints may not exist at all (e.g., `f := Nat.succ`), or there may
be several fixpoints (e.g., `f := id`). But under some conditions on `f`, a
unique __least fixpoint__ and a unique __greatest fixpoint__ are guaranteed to
exist.

Consider this __fixpoint equation__:

    `X = (fun (P : ℕ → Prop) (n : ℕ) ↦ n = 0 ∨ ∃m : ℕ, n = m + 2 ∧ P m) X`
      `= (fun n : ℕ ↦ n = 0 ∨ ∃m : ℕ, n = m + 2 ∧ X m)`

where `X : ℕ → Prop` and
`f := (fun (P : ℕ → Prop) (n : ℕ) ↦ n = 0 ∨ ∃m : ℕ, n = m + 2 ∧ P m)`.

The above example admits only one fixpoint. The fixpoint equation uniquely
specifies `X` as the predicate specifying even numbers.

In general, the least and greatest fixpoint may be different:

    `X = X`

Here, the least fixpoint is `fun _ ↦ False` and the greatest fixpoint is
`fun _ ↦ True`. Conventionally, `False < True`, and thus
`(fun _ ↦ False) < (fun _ ↦ True)`. Similarly, `∅ < @Set.univ ℕ`.

For the semantics of programming languages:

* `X` will have type `Set (State × State)` (which is isomorphic to
  `State → State → Prop`), representing relations between states;

* `f` will correspond to either taking one extra iteration of the loop (if the
  condition `B` is true) or the identity (if `B` is false).

The least fixpoint corresponds to finite executions of a program, which is all
we care about.

**Key observation**:

    Inductive predicates correspond to least fixpoints, but they are built into
    Lean's logic (the calculus of inductive constructions).


### Monotone Functions

Let `α` and `β` be types with partial order `≤`. A function `f : α → β` is
__monotone__ if

    `a₁ ≤ a₂ → f a₁ ≤ f a₂`   for all `a₁`, `a₂`

Many operations on sets (e.g., `∪`), relations (e.g., `◯`), and functions
(e.g., `fun x ↦ x`, `fun _ ↦ k`, `∘`) are monotone or preserve monotonicity.

All monotone functions `f : Set α → Set α` admit least and greatest fixpoints.

**Example of a nonmonotone function**:

    `f A = (if A = ∅ then Set.univ else ∅)`

Assuming `α` is inhabited, we have `∅ ⊆ Set.univ`, but
`f ∅ = Set.univ ⊈ ∅ = f Set.univ`.


```lean
def Monotone {α β : Type} [PartialOrder α] [PartialOrder β]
  (f : α → β) : Prop :=
  ∀a₁ a₂, a₁ ≤ a₂ → f a₁ ≤ f a₂

theorem Monotone_id {α : Type} [PartialOrder α] :
    Monotone (fun a : α ↦ a) :=
  by
    intro a₁ a₂ ha
    exact ha

theorem Monotone_const {α β : Type} [PartialOrder α]
    [PartialOrder β] (b : β) :
    Monotone (fun _ : α ↦ b) :=
  by
    intro a₁ a₂ ha
    exact le_refl b

theorem Monotone_union {α β : Type} [PartialOrder α]
      (f g : α → Set β) (hf : Monotone f) (hg : Monotone g) :
    Monotone (fun a ↦ f a ∪ g a) :=
  by
    intro a₁ a₂ ha b hb
    cases hb with
    | inl h => exact Or.inl (hf a₁ a₂ ha h)
    | inr h => exact Or.inr (hg a₁ a₂ ha h)
```


We will prove the following two theorems in the exercise.


```lean
namespace SorryTheorems

theorem Monotone_comp {α β : Type} [PartialOrder α]
      (f g : α → Set (β × β)) (hf : Monotone f)
      (hg : Monotone g) :
    Monotone (fun a ↦ f a ◯ g a) :=
  sorry

theorem Monotone_restrict {α β : Type} [PartialOrder α]
      (f : α → Set (β × β)) (P : β → Prop) (hf : Monotone f) :
    Monotone (fun a ↦ f a ⇃ P) :=
  sorry

end SorryTheorems
```


### Complete Lattices

To define the least fixpoint on sets, we need `⊆` and `⋂`: ⋂ {A | f A ⊆ A}.
Complete lattices capture this concept abstractly. A __complete lattice__ is
an ordered type `α` for which each set of type `Set α` has an infimum.

More precisely, A complete lattice consists of

* a partial order `≤ : α → α → Prop` (i.e., a reflexive, antisymmetric, and
  transitive, and binary predicate);

* an operator `Inf : Set α → α`, called __infimum__.

Moreover, `Inf A` must satisfy these two properties:

* `Inf A` is a lower bound of `A`: `Inf A ≤ b` for all `b ∈ A`;

* `Inf A` is a greatest lower bound: `b ≤ Inf A` for all `b` such that
  `∀a, a ∈ A → b ≤ a`.

**Warning:** `Inf A` is not necessarily an element of `A`.

Examples:

* `Set α` is an instance w.r.t. `⊆` and `⋂` for all `α`;
* `Prop` is an instance w.r.t. `→` and `∀` (`Inf A := ∀a ∈ A, a`);
* `ENat := ℕ ∪ {∞}`;
* `EReal := ℝ ∪ {- ∞, ∞}`;
* `β → α` if `α` is a complete lattice;
* `α × β` if `α`, `β` are complete lattices.

Finite example (with apologies for the ASCII art):

                Z            Inf {}           = ?
              /   \          Inf {Z}          = ?
             A     B         Inf {A, B}       = ?
              \   /          Inf {Z, A}       = ?
                Y            Inf {Z, A, B, Y} = ?

Nonexamples:

* `ℕ`, `ℤ`, `ℚ`, `ℝ`: no infimum for `∅`.
* `ERat := ℚ ∪ {- ∞, ∞}`: `Inf {q | 2 < q * q} = sqrt 2` is not in `ERat`.


```lean
class CompleteLattice (α : Type)
  extends PartialOrder α : Type where
  Inf    : Set α → α
  Inf_le : ∀A b, b ∈ A → Inf A ≤ b
  le_Inf : ∀A b, (∀a, a ∈ A → b ≤ a) → b ≤ Inf A
```


For sets:


```lean
instance Set.CompleteLattice {α : Type} :
  CompleteLattice (Set α) :=
  { @Set.PartialOrder α with
    Inf         := fun X ↦ {a | ∀A, A ∈ X → a ∈ A}
    Inf_le      := by aesop
    le_Inf      := by aesop }
```


### Least Fixpoint


```lean
def lfp {α : Type} [CompleteLattice α] (f : α → α) : α :=
  CompleteLattice.Inf {a | f a ≤ a}

theorem lfp_le {α : Type} [CompleteLattice α] (f : α → α)
      (a : α) (h : f a ≤ a) :
    lfp f ≤ a :=
  CompleteLattice.Inf_le _ _ h

theorem le_lfp {α : Type} [CompleteLattice α] (f : α → α)
      (a : α) (h : ∀a', f a' ≤ a' → a ≤ a') :
    a ≤ lfp f :=
  CompleteLattice.le_Inf _ _ h
```


**Knaster-Tarski theorem:** For any monotone function `f`:

* `lfp f` is a fixpoint: `lfp f = f (lfp f)` (theorem `lfp_eq`);
* `lfp f` is smaller than any other fixpoint: `X = f X → lfp f ≤ X`.


```lean
theorem lfp_eq {α : Type} [CompleteLattice α] (f : α → α)
      (hf : Monotone f) :
    lfp f = f (lfp f) :=
  by
    have h : f (lfp f) ≤ lfp f :=
      by
        apply le_lfp
        intro a' ha'
        apply le_trans
        { apply hf
          apply lfp_le
          assumption }
        { assumption }
    apply le_antisymm
    { apply lfp_le
      apply hf
      assumption }
    { assumption }
```


### A Relational Denotational Semantics, Continued


```lean
def denote : Stmt → Set (State × State)
  | Stmt.skip             => Id
  | Stmt.assign x a       =>
    {st | Prod.snd st = (Prod.fst st)[x ↦ a (Prod.fst st)]}
  | Stmt.seq S T          => denote S ◯ denote T
  | Stmt.ifThenElse B S T =>
    (denote S ⇃ B) ∪ (denote T ⇃ (fun s ↦ ¬ B s))
  | Stmt.whileDo B S      =>
    lfp (fun X ↦ ((denote S ◯ X) ⇃ B)
      ∪ (Id ⇃ (fun s ↦ ¬ B s)))

notation (priority := high) "⟦" S "⟧" => denote S

theorem Monotone_while_lfp_arg (S B) :
    Monotone (fun X ↦ ⟦S⟧ ◯ X ⇃ B ∪ Id ⇃ (fun s ↦ ¬ B s)) :=
  by
    apply Monotone_union
    { apply SorryTheorems.Monotone_restrict
      apply SorryTheorems.Monotone_comp
      { exact Monotone_const _ }
      { exact Monotone_id } }
    { apply SorryTheorems.Monotone_restrict
      exact Monotone_const _ }
```


### Application to Program Equivalence

Based on the denotational semantics, we introduce the notion of program
equivalence: `S₁ ~ S₂`. (Compare with exercise 9.)


```lean
def DenoteEquiv (S₁ S₂ : Stmt) : Prop :=
  ⟦S₁⟧ = ⟦S₂⟧

infix:50 (priority := high) " ~ " => DenoteEquiv
```


It is obvious from the definition that `~` is an equivalence relation.

Program equivalence can be used to replace subprograms by other subprograms with
the same semantics. This is achieved by the following congruence rules:


```lean
theorem DenoteEquiv.seq_congr {S₁ S₂ T₁ T₂ : Stmt}
      (hS : S₁ ~ S₂) (hT : T₁ ~ T₂) :
    S₁; T₁ ~ S₂; T₂ :=
  by
    simp [DenoteEquiv, denote] at *
    simp [*]

theorem DenoteEquiv.if_congr {B} {S₁ S₂ T₁ T₂ : Stmt}
      (hS : S₁ ~ S₂) (hT : T₁ ~ T₂) :
    Stmt.ifThenElse B S₁ T₁ ~ Stmt.ifThenElse B S₂ T₂ :=
  by
    simp [DenoteEquiv, denote] at *
    simp [*]

theorem DenoteEquiv.while_congr {B} {S₁ S₂ : Stmt}
      (hS : S₁ ~ S₂) :
    Stmt.whileDo B S₁ ~ Stmt.whileDo B S₂ :=
  by
    simp [DenoteEquiv, denote] at *
    simp [*]
```


Compare the simplicity of these proofs with the corresponding proofs for a
big-step semantics (exercise 8).

Let us prove some program equivalences.


```lean
theorem DenoteEquiv.skip_assign_id {x} :
    Stmt.assign x (fun s ↦ s x) ~ Stmt.skip :=
  by simp [DenoteEquiv, denote, Id]

theorem DenoteEquiv.seq_skip_left {S} :
    Stmt.skip; S ~ S :=
  by simp [DenoteEquiv, denote, Id, comp]

theorem DenoteEquiv.seq_skip_right {S} :
    S; Stmt.skip ~ S :=
  by simp [DenoteEquiv, denote, Id, comp]

theorem DenoteEquiv.if_seq_while {B S} :
    Stmt.ifThenElse B (S; Stmt.whileDo B S) Stmt.skip
    ~ Stmt.whileDo B S :=
  by
    simp [DenoteEquiv, denote]
    apply Eq.symm
    apply lfp_eq
    apply Monotone_while_lfp_arg
```


### Equivalence of the Denotational and the Big-Step Semantics
### (**optional**)


```lean
theorem denote_of_BigStep (Ss : Stmt × State) (t : State)
      (h : Ss ⟹ t) :
    (Prod.snd Ss, t) ∈ ⟦Prod.fst Ss⟧ :=
  by
    induction h with
    | skip s => simp [denote]
    | assign x a s => simp [denote]
    | seq S T s t u hS hT ihS ihT =>
      simp [denote]
      aesop
    | if_true B S T s t hB hS ih =>
      simp at *
      simp [denote, *]
    | if_false B S T s t hB hT ih =>
      simp at *
      simp [denote, *]
    | while_true B S s t u hB hS hw ihS ihw =>
      rw [Eq.symm DenoteEquiv.if_seq_while]
      simp at *
      simp [denote, *]
      aesop
    | while_false B S s hB =>
      rw [Eq.symm DenoteEquiv.if_seq_while]
      simp at *
      simp [denote, *]

theorem BigStep_of_denote :
    ∀(S : Stmt) (s t : State), (s, t) ∈ ⟦S⟧ → (S, s) ⟹ t
  | Stmt.skip,             s, t => by simp [denote]
  | Stmt.assign x a,       s, t => by simp [denote]
  | Stmt.seq S T,          s, t =>
    by
      intro hst
      simp [denote] at hst
      cases hst with
      | intro u hu =>
        cases hu with
        | intro hsu hut =>
          apply BigStep.seq
          { exact BigStep_of_denote _ _ _ hsu }
          { exact BigStep_of_denote _ _ _ hut }
  | Stmt.ifThenElse B S T, s, t =>
    by
      intro hst
      simp [denote] at hst
      cases hst with
      | inl htrue =>
        cases htrue with
        | intro hst hB =>
          apply BigStep.if_true
          { exact hB }
          { exact BigStep_of_denote _ _ _ hst }
      | inr hfalse =>
        cases hfalse with
        | intro hst hB =>
          apply BigStep.if_false
          { exact hB }
          { exact BigStep_of_denote _ _ _ hst }
  | Stmt.whileDo B S,      s, t =>
    by
      have hw : ⟦Stmt.whileDo B S⟧
        ≤ {st | (Stmt.whileDo B S, Prod.fst st) ⟹
             Prod.snd st} :=
        by
          apply lfp_le _ _ _
          intro uv huv
          cases uv with
          | mk u v =>
            simp at huv
            cases huv with
            | inl hand =>
              cases hand with
              | intro hst hB =>
                cases hst with
                | intro w hw =>
                  cases hw with
                  | intro huw hw =>
                    apply BigStep.while_true
                    { exact hB }
                    { exact BigStep_of_denote _ _ _ huw }
                    { exact hw }
            | inr hand =>
              cases hand with
              | intro hvu hB =>
                cases hvu
                apply BigStep.while_false
                exact hB
      apply hw

theorem denote_Iff_BigStep (S : Stmt) (s t : State) :
    (s, t) ∈ ⟦S⟧ ↔ (S, s) ⟹ t :=
  Iff.intro (BigStep_of_denote S s t) (denote_of_BigStep (S, s) t)
```


### A Simpler Approach Based on an Inductive Predicate (**optional**)


```lean
inductive Awhile (B : State → Prop)
    (r : Set (State × State)) :
  State → State → Prop
  | true {s t u} (hcond : B s) (hbody : (s, t) ∈ r)
      (hrest : Awhile B r t u) :
    Awhile B r s u
  | false {s} (hcond : ¬ B s) :
    Awhile B r s s

def denoteAwhile : Stmt → Set (State × State)
  | Stmt.skip             => Id
  | Stmt.assign x a       =>
    {st | Prod.snd st = (Prod.fst st)[x ↦ a (Prod.fst st)]}
  | Stmt.seq S T          => denoteAwhile S ◯ denoteAwhile T
  | Stmt.ifThenElse B S T =>
    (denoteAwhile S ⇃ B)
    ∪ (denoteAwhile T ⇃ (fun s ↦ ¬ B s))
  | Stmt.whileDo B S      =>
    {st | Awhile B (denoteAwhile S) (Prod.fst st)
       (Prod.snd st)}

end LoVe
```

---



## Denotational Semantics (ExerciseSheet) {#hhg-denotational-semantics-exercisesheet}

> 📄 Source: https://raw.githubusercontent.com/lean-forward/logical_verification_2025/main/lean/LoVe/LoVe11_DenotationalSemantics_ExerciseSheet.lean

```lean
import LoVe.LoVe11_DenotationalSemantics_Demo
```


## LoVe Exercise 11: Denotational Semantics

Replace the placeholders (e.g., `:= sorry`) with your solutions.


```lean
set_option autoImplicit false
set_option tactic.hygienic false

namespace LoVe
```


### Question 1: Monotonicity

1.1. Prove the following theorem from the lecture.


```lean
theorem Monotone_restrict {α β : Type} [PartialOrder α] (f : α → Set (β × β))
      (p : β → Prop) (hf : Monotone f) :
    Monotone (fun a ↦ f a ⇃ p) :=
  sorry
```


1.2. Prove its cousin.


```lean
theorem Monotone_comp {α β : Type} [PartialOrder α] (f g : α → Set (β × β))
      (hf : Monotone f) (hg : Monotone g) :
    Monotone (fun a ↦ f a ◯ g a) :=
  sorry
```


### Question 2: Regular Expressions

__Regular expressions__, or __regexes__, are a highly popular tool for software
development, to analyze textual inputs. Regexes are generated by the following
grammar:

    R  ::=  ∅
         |  ε
         |  a
         |  R ⬝ R
         |  R + R
         |  R*

Informally, the semantics of regular expressions is as follows:

* `∅` accepts nothing;
* `ε` accepts the empty string;
* `a` accepts the atom `a`;
* `R ⬝ R` accepts the concatenation of two regexes;
* `R + R` accepts either of two regexes;
* `R*` accepts arbitrary many repetitions of a regex.

Notice the rough correspondence with a WHILE language:

    `∅` ~ diverging statement (e.g., `while true do skip`)
    `ε` ~ `skip`
    `a` ~ `:=`
    `⬝` ~ `;`
    `+` ~ `if then else`
    `*` ~ `while` loop


```lean
inductive Regex (α : Type) : Type
  | nothing : Regex α
  | empty   : Regex α
  | atom    : α → Regex α
  | concat  : Regex α → Regex α → Regex α
  | alt     : Regex α → Regex α → Regex α
  | star    : Regex α → Regex α
```


In this exercise, we explore an alternative semantics of regular
expressions. Namely, we can imagine that the atoms represent binary relations,
instead of letters or symbols. Concatenation corresponds to composition of
relations, and alternation is union. Mathematically, regexes and binary
relations are both instances of Kleene algebras.

2.1. Complete the following translation of regular expressions to relations.

Hint: Exploit the correspondence with the WHILE language.


```lean
def rel_of_Regex {α : Type} : Regex (Set (α × α)) → Set (α × α)
  | Regex.nothing      => ∅
  | Regex.empty        => Id
  -- enter the missing cases here
```


2.2. Prove the following recursive equation about your definition.


```lean
theorem rel_of_Regex_Star {α : Type} (r : Regex (Set (α × α))) :
    rel_of_Regex (Regex.star r) =
    rel_of_Regex (Regex.alt (Regex.concat r (Regex.star r)) Regex.empty) :=
  sorry

end LoVe
```

---



## Denotational Semantics (HomeworkSheet) {#hhg-denotational-semantics-homeworksheet}

> 📄 Source: https://raw.githubusercontent.com/lean-forward/logical_verification_2025/main/lean/LoVe/LoVe11_DenotationalSemantics_HomeworkSheet.lean

```lean
import LoVe.LoVe11_DenotationalSemantics_Demo
```


## LoVe Homework 11 (10 points + 2 bonus points): Denotational Semantics

Replace the placeholders (e.g., `:= sorry`) with your solutions.


```lean
set_option autoImplicit false
set_option tactic.hygienic false

namespace LoVe
```


The following command enables noncomputable decidability on every `Prop`.
The `0` argument ensures this is used only when necessary; otherwise, it would
make some computable definitions noncomputable for Lean. Depending on how you
solve question 2.2, this command might help you.


```lean
attribute [instance 0] Classical.propDecidable
```


Denotational semantics are well suited to functional programming. In this
exercise, we will study some representations of functional programs in Lean and
their denotational semantics.

The `Nondet` type represents functional programs that can perform
nondeterministic computations: A program can choose between many different
computation paths / return values. Returning no results at all is represented
by `fail`, and nondeterministic choice between two options, identified by the
`Bool` values `true` and `false`, is represented by `choice`.


```lean
inductive Nondet (α : Type) : Type
  | just   : α → Nondet α
  | fail   : Nondet α
  | choice : (Bool → Nondet α) → Nondet α

namespace Nondet
```


### Question 1 (5 points + 1 bonus point): The `Nondet` Monad

The `Nondet` inductive type forms a monad. The `pure` operator is `Nondet.just`.
`bind` is as follows:


```lean
def bind {α β : Type} : Nondet α → (α → Nondet β) → Nondet β
  | just a,   f => f a
  | fail,     f => fail
  | choice k, f => choice (fun b ↦ bind (k b) f)

instance : Pure Nondet :=
  { pure := just }

instance : Bind Nondet :=
  { bind := bind }
```


1.1 (5 points). Prove the three monad laws for `Nondet`.

Hints:

* To unfold the definition of `pure` and `>>=`, invoke
  `simp [Bind.bind, Pure.pure]`.

* To reduce `f = g` to `∀x, f x = g x`, use the theorem `funext`.


```lean
theorem pure_bind {α β : Type} (a : α) (f : α → Nondet β) :
    pure a >>= f = f a :=
 sorry

theorem bind_pure {α : Type} :
    ∀na : Nondet α, na >>= pure = na :=
  sorry

theorem bind_assoc {α β γ : Type} :
    ∀(na : Nondet α) (f : α → Nondet β) (g : β → Nondet γ),
      ((na >>= f) >>= g) = (na >>= (fun a ↦ f a >>= g)) :=
  sorry
```


The function `portmanteau` computes a portmanteau of two lists: A
portmanteau of `xs` and `ys` has `xs` as a prefix and `ys` as a suffix, and they
overlap. We use `startsWith xs ys` to test that `ys` has `xs` as a prefix.


```lean
def startsWith : List ℕ → List ℕ → Bool
  | x :: xs, []      => false
  | [],      ys      => true
  | x :: xs, y :: ys => x = y && startsWith xs ys

#eval startsWith [1, 2] [1, 2, 3]
#eval startsWith [1, 2, 3] [1, 2]

def portmanteau : List ℕ → List ℕ → List (List ℕ)
| [],      ys => []
| x :: xs, ys =>
  List.map (List.cons x) (portmanteau xs ys) ++
  (if startsWith (x :: xs) ys then [ys] else [])
```


Here are some examples of portmanteaux:


```lean
#eval portmanteau [0, 1, 2, 3] [2, 3, 4]
#eval portmanteau [0, 1] [2, 3, 4]
#eval portmanteau [0, 1, 2, 1, 2] [1, 2, 1, 2, 3, 4]
```


1.2 (1 bonus point). Translate the `portmanteau` program from the `List`
monad to the `Nondet` monad.


```lean
def nondetPortmanteau : List ℕ → List ℕ → Nondet (List ℕ) :=
  sorry
```


### Question 2 (5 points + 1 bonus point): Nondeterminism, Denotationally

2.1 (2 points). Give a denotational semantics for `Nondet`, mapping it into a
`List` of all results. `pure` returns one result, `fail` returns zero, and
`choice` combines the results of either option.


```lean
def listSem {α : Type} : Nondet α → List α :=
  sorry
```


Check that the following lines give the same output as for `portmanteau` (if
you have answered question 1.2):


```lean
#reduce listSem (nondetPortmanteau [0, 1, 2, 3] [2, 3, 4])
#reduce listSem (nondetPortmanteau [0, 1] [2, 3, 4])
#reduce listSem (nondetPortmanteau [0, 1, 2, 1, 2] [1, 2, 1, 2, 3, 4])
```


2.2 (3 points). Often, we are not interested in getting all outcomes, just
the first successful one. Give a semantics for `Nondet` that produces the first
successful result, if any. Your solution should *not* use `listSem`.


```lean
noncomputable def optionSem {α : Type} : Nondet α → Option α :=
  sorry
```


2.3 (1 bonus point). Prove the theorem `List_Option_compat` below, showing
that the two semantics you defined are compatible.

`List.head?` returns the head of a list wrapped in an `Option.some`, or
`Option.none` for an empty list. It corresponds to the function we called
`headOpt` in lecture 5.


```lean
theorem List_Option_compat {α : Type} :
    ∀na : Nondet α, optionSem na = List.head? (listSem na) :=
  sorry

end Nondet

end LoVe
```

---



## Logical Foundations of Mathematics (Demo) {#hhg-logical-foundations-of-mathematics-demo}

> 📄 Source: https://raw.githubusercontent.com/lean-forward/logical_verification_2025/main/lean/LoVe/LoVe12_LogicalFoundationsOfMathematics_Demo.lean

```lean
import LoVe.LoVe06_InductivePredicates_Demo
```


## LoVe Demo 12: Logical Foundations of Mathematics

We dive deeper into the logical foundations of Lean. Most of the features
described here are especially relevant for defining mathematical objects and
proving theorems about them.


```lean
set_option autoImplicit false
set_option tactic.hygienic false

namespace LoVe
```


### Universes

Not only terms have a type, but also types have a type. For example,

    `@And.intro : ∀a b, a → b → a ∧ b`

and

    `∀a b, a → b → a ∧ b : Prop`

Now, what is the type of `Prop`? `Prop` has the same type as virtually all other
types we have constructed so far:

    `Prop : Type`

What is the type of `Type`? The typing `Type : Type` would lead to a
contradiction, called **Girard's paradox**, resembling Russel's paradox.
Instead:

    `Type   : Type 1`
    `Type 1 : Type 2`
    `Type 2 : Type 3`
    ⋮

Aliases:

    `Type`   := `Type 0`
    `Prop`   := `Sort 0`
    `Type u` := `Sort (u + 1)`

The types of types (`Sort u`, `Type u`, and `Prop`) are called __universes__.
The `u` in `Sort u` is a __universe level__.

The hierarchy is captured by the following typing judgment:

    ————————————————————————— Sort
    C ⊢ Sort u : Sort (u + 1)


```lean
#check @And.intro
#check ∀a b : Prop, a → b → a ∧ b
#check Prop
#check ℕ
#check Type
#check Type 1
#check Type 2

universe u v

#check Type u

#check Sort 0
#check Sort 1
#check Sort 2
#check Sort u

#check Type _
```


### The Peculiarities of Prop

`Prop` is different from the other universes in many respects.


#### Impredicativity

The function type `σ → τ` is put into the larger one of the universes that
`σ` and `τ` live in:

    C ⊢ σ : Type u    C ⊢ τ : Type v
    ————————————————————————————————— SimpleArrow-Type
    C ⊢ σ → τ : Type (max u v)

For dependent types, this generalizes to

    C ⊢ σ : Type u    C, x : σ ⊢ τ[x] : Type v
    ——————————————————————————————————————————— Arrow-Type
    C ⊢ (x : σ) → τ[x] : Type (max u v)

This behavior of the universes `Type v` is called __predicativity__.

To force expressions such as `∀a : Prop, a → a` to be of type `Prop` anyway, we
need a special typing rule for `Prop`:

    C ⊢ σ : Sort u    x : σ ⊢ τ[x] : Prop
    —————————————————————————————————————— Arrow-Prop
    C ⊢ (∀x : σ, τ[x]) : Prop

This behavior of `Prop` is called __impredicativity__.

The rules `Arrow-Type` and `Arrow-Prop` can be generalized into a single rule:

    C ⊢ σ : Sort u    C, x : σ ⊢ τ[x] : Sort v
    ——————————————————————————————————————————— Arrow
    C ⊢ (x : σ) → τ[x] : Sort (imax u v)

where

    `imax u 0       = 0`
    `imax u (v + 1) = max u (v + 1)`


```lean
#check fun (α : Type u) (β : Type v) ↦ α → β
#check ∀a : Prop, a → a
```


#### Proof Irrelevance

A second difference between `Prop` and `Type u` is __proof irrelevance__:

    `∀(a : Prop) (h₁ h₂ : a), h₁ = h₂`

This makes reasoning about dependent types easier.

When viewing a proposition as a type and a proof as an element of that type,
proof irrelevance means that a proposition is either an empty type or has
exactly one inhabitant.

Proof irrelevance can be proved by `rfl`.

An unfortunate consequence of proof irrelevance is that it prevents us from
performing rule induction by pattern matching and recursion.


```lean
#check proof_irrel

theorem proof_irrel {a : Prop} (h₁ h₂ : a) :
    h₁ = h₂ :=
  by rfl
```


#### No Large Elimination

A further difference between `Prop` and `Type u` is that `Prop` does not allow
__large elimination__, meaning that it impossible to extract data from a proof
of a proposition.

This is necessary to allow proof irrelevance.

-- fails
def unsquare (i : ℤ) (hsq : ∃j, i = j * j) : ℤ :=
  match hsq with
  | Exists.intro j _ => j

If the above were accepted, we could derive `False` as follows.

Let

    `hsq₁` := `Exists.intro 3 (by linarith)`
    `hsq₂` := `Exists.intro (-3) (by linarith)`

be two proofs of `∃j, (9 : ℤ) = j * j`. Then

    `unsquare 9 hsq₁ = 3`
    `unsquare 9 hsq₂ = -3`

However, by proof irrelevance, `hsq₁ = hsq₂`. Hence

    `unsquare 9 hsq₂ = 3`

Thus

    `3 = -3`

a contradiction.

As a compromise, Lean allows __small elimination__. It is called small
elimination because it eliminates only into `Prop`, whereas large elimination can
eliminate into an arbitrary large universe `Sort u`. This means we can use
`match` to analyze the structure of a proof, extract existential witnesses, and
so on, as long as the `match` expression is itself a proof. We have seen several
examples of this in lecture 5.

As a further compromise, Lean allows large elimination for
__syntactic subsingletons__: types in `Prop` for which it can be established
syntactically that they have cardinality 0 or 1. These are propositions such as
`False` and `a ∧ b` that can be proved in at most one way.


### The Axiom of Choice

Lean's logic includes the axiom of choice, which makes it possible to obtain an
arbitrary element from any nonempty type.

Consider Lean's `Nonempty` inductive predicate:


```lean
#print Nonempty
```


The predicate states that `α` has at least one element.

To prove `Nonempty α`, we must provide an `α` value to `Nonempty.intro`:


```lean
theorem Nat.Nonempty :
    Nonempty ℕ :=
  Nonempty.intro 0
```


Since `Nonempty` lives in `Prop`, large elimination is not available, and
thus we cannot extract the element that was used from a proof of `Nonempty α`.

The axiom of choice allows us to obtain some element of type `α` if we can show
`Nonempty α`:


```lean
#check Classical.choice
```


It will just give us an arbitrary element of `α`; we have no way of knowing
whether this is the element that was used to prove `Nonempty α`.

The constant `Classical.choice` is noncomputable, which is why some logicians
prefer to work without this axiom.

#eval Classical.choice Nat.Nonempty     -- fails


```lean
#reduce Classical.choice Nat.Nonempty
```


The axiom of choice is only an axiom in Lean's core library, giving users
the freedom to work with or without it.

Definitions using it must be marked as `noncomputable`:


```lean
noncomputable def arbitraryNat : ℕ :=
  Classical.choice Nat.Nonempty
```


The following tools rely on choice.


#### Law of Excluded Middle


```lean
#check Classical.em
```


#### Hilbert Choice


```lean
#check Classical.choose
#check Classical.choose_spec
```


#### Set-Theoretic Axiom of Choice


```lean
#print Classical.axiomOfChoice
```


### Subtypes

Subtyping is a mechanism to create new types from existing ones.

Given a predicate on the elements of the base type, the __subtype__ contains
only those elements of the base type that fulfill this property. More precisely,
the subtype contains element–proof pairs that combine an element of the base
type and a proof that it fulfills the property.

Subtyping is useful for those types that cannot be defined as an inductive
type. For example, any attempt at defining the type of finite sets along the
following lines is doomed to fail:


```lean
-- wrong
inductive Finset (α : Type) : Type
  | empty  : Finset α
  | insert : α → Finset α → Finset α
```


Why does this not model finite sets?

Given a base type and a property, the subtype has the syntax

    `{` _variable_ `:` _base-type_ `//` _property-applied-to-variable_ `}`

Alias:

    `{x : τ // P[x]}` := `@Subtype τ (fun x ↦ P[x])`

Examples:

    `{i : ℕ // i ≤ n}`            := `@Subtype ℕ (fun i ↦ i ≤ n)`
    `{A : Set α // Set.Finite A}` := `@Subtype (Set α) Set.Finite`


#### First Example: Full Binary Trees


```lean
#check Tree
#check IsFull
#check mirror
#check IsFull_mirror
#check mirror_mirror

def FullTree (α : Type) : Type :=
  {t : Tree α // IsFull t}

#print Subtype
#check Subtype.mk
```


To define elements of `FullTree`, we must provide a `Tree` and a proof that
it is full:


```lean
def nilFullTree : FullTree ℕ :=
  Subtype.mk Tree.nil IsFull.nil

def fullTree6 : FullTree ℕ :=
  Subtype.mk (Tree.node 6 Tree.nil Tree.nil)
    (by
       apply IsFull.node
       apply IsFull.nil
       apply IsFull.nil
       rfl)

#reduce Subtype.val fullTree6
#check Subtype.property fullTree6
```


We can lift existing operations on `Tree` to `FullTree`:


```lean
def FullTree.mirror {α : Type} (t : FullTree α) :
  FullTree α :=
  Subtype.mk (LoVe.mirror (Subtype.val t))
    (by
       apply IsFull_mirror
       apply Subtype.property t)

#reduce Subtype.val (FullTree.mirror fullTree6)
```


And of course we can prove theorems about the lifted operations:


```lean
theorem FullTree.mirror_mirror {α : Type}
      (t : FullTree α) :
    (FullTree.mirror (FullTree.mirror t)) = t :=
  by
    apply Subtype.eq
    simp [FullTree.mirror, LoVe.mirror_mirror]

#check Subtype.eq
```


#### Second Example: Vectors


```lean
def Vector (α : Type) (n : ℕ) : Type :=
  {xs : List α // List.length xs = n}

def vector123 : Vector ℤ 3 :=
  Subtype.mk [1, 2, 3] (by rfl)

def Vector.neg {n : ℕ} (v : Vector ℤ n) : Vector ℤ n :=
  Subtype.mk (List.map Int.neg (Subtype.val v))
    (by
       rw [List.length_map]
       exact Subtype.property v)

theorem Vector.neg_neg (n : ℕ) (v : Vector ℤ n) :
    Vector.neg (Vector.neg v) = v :=
  by
    apply Subtype.eq
    simp [Vector.neg]
```


### Quotient Types

Quotients are a powerful construction in mathematics used to construct `ℤ`, `ℚ`,
`ℝ`, and many other types.

Like subtyping, quotienting constructs a new type from an existing type. Unlike
a subtype, a quotient type contains all of the elements of the base type, but
some elements that were different in the base type are considered equal in the
quotient type. In mathematical terms, the quotient type is isomorphic to a
partition of the base type.

To define a quotient type, we need to provide a type that it is derived from and
an equivalence relation on the type that determines which elements are
considered equal.


```lean
#check Quotient
#print Setoid

#check Quotient.mk
#check Quotient.sound
#check Quotient.exact

#check Quotient.lift
#check Quotient.lift₂
#check @Quotient.inductionOn
```


### First Example: Integers

Let us build the integers `ℤ` as a quotient over pairs of natural numbers
`ℕ × ℕ`.

A pair `(p, n)` of natural numbers represents the integer `p - n`. Nonnegative
integers `p` can be represented by `(p, 0)`. Negative integers `-n` can be
represented by `(0, n)`. However, many representations of the same integer are
possible; e.g., `(7, 0)`, `(8, 1)`, `(9, 2)`, and `(10, 3)` all represent the
integer `7`.

Which equivalence relation can we use?

We want two pairs `(p₁, n₁)` and `(p₂, n₂)` to be equal if `p₁ - n₁ = p₂ - n₂`.
However, this does not work because subtraction on `ℕ` is ill-behaved (e.g.,
`0 - 1 = 0`). Instead, we use `p₁ + n₂ = p₂ + n₁`.


```lean
instance Int.Setoid : Setoid (ℕ × ℕ) :=
  { r :=
      fun pn₁ pn₂ : ℕ × ℕ ↦
        Prod.fst pn₁ + Prod.snd pn₂ =
        Prod.fst pn₂ + Prod.snd pn₁
    iseqv :=
      { refl :=
          by
            intro pn
            rfl
        symm :=
          by
            intro pn₁ pn₂ h
            rw [h]
        trans :=
          by
            intro pn₁ pn₂ pn₃ h₁₂ h₂₃
            linarith } }

theorem Int.Setoid_Iff (pn₁ pn₂ : ℕ × ℕ) :
    pn₁ ≈ pn₂ ↔
    Prod.fst pn₁ + Prod.snd pn₂ =
    Prod.fst pn₂ + Prod.snd pn₁ :=
  by rfl

def Int : Type :=
  Quotient Int.Setoid

def Int.zero : Int :=
  ⟦(0, 0)⟧

theorem Int.zero_Eq (m : ℕ) :
    Int.zero = ⟦(m, m)⟧ :=
  by
    rw [Int.zero]
    apply Quotient.sound
    rw [Int.Setoid_Iff]
    simp

def Int.add : Int → Int → Int :=
  Quotient.lift₂
    (fun pn₁ pn₂ : ℕ × ℕ ↦
       ⟦(Prod.fst pn₁ + Prod.fst pn₂,
         Prod.snd pn₁ + Prod.snd pn₂)⟧)
    (by
       intro pn₁ pn₂ pn₁' pn₂' h₁ h₂
       apply Quotient.sound
       rw [Int.Setoid_Iff] at *
       linarith)

theorem Int.add_Eq (p₁ n₁ p₂ n₂ : ℕ) :
    Int.add ⟦(p₁, n₁)⟧ ⟦(p₂, n₂)⟧ =
    ⟦(p₁ + p₂, n₁ + n₂)⟧ :=
  by rfl

theorem Int.add_zero (i : Int) :
    Int.add Int.zero i = i :=
  by
    induction i using Quotient.inductionOn with
    | h pn =>
      cases pn with
      | mk p n => simp [Int.zero, Int.add]
```


This definitional syntax would be nice:

-- fails
def Int.add : Int → Int → Int
  | ⟦(p₁, n₁)⟧, ⟦(p₂, n₂)⟧ => ⟦(p₁ + p₂, n₁ + n₂)⟧

But it would be dangerous:

-- fails
def Int.fst : Int → ℕ
  | ⟦(p, n)⟧ => p

Using `Int.fst`, we could derive `False`. First, we have

    `Int.fst ⟦(0, 0)⟧ = 0`
    `Int.fst ⟦(1, 1)⟧ = 1`

But since `⟦(0, 0)⟧ = ⟦(1, 1)⟧`, we get

    `0 = 1`

#### Second Example: Unordered Pairs

__Unordered pairs__ are pairs for which no distinction is made between the first
and second components. They are usually written `{a, b}`.

We will introduce the type `UPair` of unordered pairs as the quotient of pairs
`(a, b)` with respect to the relation "contains the same elements as".


```lean
instance UPair.Setoid (α : Type) : Setoid (α × α) :=
{ r :=
    fun ab₁ ab₂ : α × α ↦
      ({Prod.fst ab₁, Prod.snd ab₁} : Set α) =
      ({Prod.fst ab₂, Prod.snd ab₂} : Set α)
  iseqv :=
    { refl  := by simp
      symm  := by aesop
      trans := by aesop } }

theorem UPair.Setoid_Iff {α : Type} (ab₁ ab₂ : α × α) :
    ab₁ ≈ ab₂ ↔
    ({Prod.fst ab₁, Prod.snd ab₁} : Set α) =
    ({Prod.fst ab₂, Prod.snd ab₂} : Set α) :=
  by rfl

def UPair (α : Type) : Type :=
  Quotient (UPair.Setoid α)

#check UPair.Setoid
```


It is easy to prove that our pairs are really unordered:


```lean
theorem UPair.mk_symm {α : Type} (a b : α) :
    (⟦(a, b)⟧ : UPair α) = ⟦(b, a)⟧ :=
  by
    apply Quotient.sound
    rw [UPair.Setoid_Iff]
    aesop
```


Another representation of unordered pairs is as sets of cardinality 1 or 2.
The following operation converts `UPair` to that representation:


```lean
def Set_of_UPair {α : Type} : UPair α → Set α :=
  Quotient.lift (fun ab : α × α ↦ {Prod.fst ab, Prod.snd ab})
    (by
       intro ab₁ ab₂ h
       rw [UPair.Setoid_Iff] at *
       exact h)
```


#### Alternative Definitions via Normalization and Subtyping

Each element of a quotient type corresponds to an `≈`-equivalence class.
If there exists a systematic way to obtain a **canonical representative** for
each class, we can use a subtype instead of a quotient, keeping only the
canonical representatives.

Consider the quotient type `Int` above. We could say that a pair `(p, n)` is
__canonical__ if `p` or `n` is `0`.


```lean
namespace Alternative

inductive Int.IsCanonical : ℕ × ℕ → Prop
  | nonpos {n : ℕ} : Int.IsCanonical (0, n)
  | nonneg {p : ℕ} : Int.IsCanonical (p, 0)

def Int : Type :=
  {pn : ℕ × ℕ // Int.IsCanonical pn}
```


**Normalizing** pairs of natural numbers is easy:


```lean
def Int.normalize : ℕ × ℕ → ℕ × ℕ
  | (p, n) => if p ≥ n then (p - n, 0) else (0, n - p)

theorem Int.IsCanonical_normalize (pn : ℕ × ℕ) :
    Int.IsCanonical (Int.normalize pn) :=
  by
    cases pn with
    | mk p n =>
      simp [Int.normalize]
      cases Classical.em (p ≥ n) with
      | inl hpn =>
        simp [*]
        exact Int.IsCanonical.nonneg
      | inr hpn =>
        simp [*]
        exact Int.IsCanonical.nonpos
```


For unordered pairs, there is no obvious normal form, except to always put
the smaller element first (or last). This requires a linear order `≤` on `α`.


```lean
def UPair.IsCanonical {α : Type} [LinearOrder α] :
  α × α → Prop
  | (a, b) => a ≤ b

def UPair (α : Type) [LinearOrder α] : Type :=
  {ab : α × α // UPair.IsCanonical ab}

end Alternative

end LoVe
```

---



## Logical Foundations of Mathematics (ExerciseSheet) {#hhg-logical-foundations-of-mathematics-exercisesheet}

> 📄 Source: https://raw.githubusercontent.com/lean-forward/logical_verification_2025/main/lean/LoVe/LoVe12_LogicalFoundationsOfMathematics_ExerciseSheet.lean

```lean
import LoVe.LoVe12_LogicalFoundationsOfMathematics_Demo
```


## LoVe Exercise 12: Logical Foundations of Mathematics

Replace the placeholders (e.g., `:= sorry`) with your solutions.


```lean
set_option autoImplicit false
set_option tactic.hygienic false

namespace LoVe
```


### Question 1: Vectors as Subtypes

Recall the definition of vectors from the demo:


```lean
#check Vector
```


The following function adds two lists of integers elementwise. If one
function is longer than the other, the tail of the longer list is ignored.


```lean
def List.add : List ℤ → List ℤ → List ℤ
  | [],      []      => []
  | x :: xs, y :: ys => (x + y) :: List.add xs ys
  | [],      y :: ys => []
  | x :: xs, []      => []
```


1.1. Show that if the lists have the same length, the resulting list also
has that length.


```lean
theorem List.length_add :
    ∀xs ys, List.length xs = List.length ys →
      List.length (List.add xs ys) = List.length xs
  | [], [] =>
    sorry
  | x :: xs, y :: ys =>
    sorry
  | [], y :: ys =>
    sorry
  | x :: xs, [] =>
    sorry
```


1.2. Define componentwise addition on vectors using `List.add` and
`List.length_add`.


```lean
def Vector.add {n : ℕ} : Vector ℤ n → Vector ℤ n → Vector ℤ n :=
  sorry
```


1.3. Show that `List.add` and `Vector.add` are commutative.


```lean
theorem List.add.comm :
    ∀xs ys, List.add xs ys = List.add ys xs :=
  sorry

theorem Vector.add.comm {n : ℕ} (u v : Vector ℤ n) :
    Vector.add u v = Vector.add v u :=
  sorry
```


### Question 2: Integers as Quotients

Recall the construction of integers from the lecture, not to be confused with
Lean's predefined type `Int` (= `ℤ`):


```lean
#check Int.Setoid
#check Int.Setoid_Iff
#check Int
```


2.1. Define negation on these integers. Observe that if `(p, n)` represents
an integer, then `(n, p)` represents its negation.


```lean
def Int.neg : Int → Int :=
  sorry
```


2.2. Prove the following theorems about negation.


```lean
theorem Int.neg_eq (p n : ℕ) :
    Int.neg ⟦(p, n)⟧ = ⟦(n, p)⟧ :=
  sorry

theorem int.neg_neg (a : Int) :
    Int.neg (Int.neg a) = a :=
  sorry

end LoVe
```

---



## Logical Foundations of Mathematics (HomeworkSheet) {#hhg-logical-foundations-of-mathematics-homeworksheet}

> 📄 Source: https://raw.githubusercontent.com/lean-forward/logical_verification_2025/main/lean/LoVe/LoVe12_LogicalFoundationsOfMathematics_HomeworkSheet.lean

```lean
import LoVe.LoVe06_InductivePredicates_Demo
```


## LoVe Homework 12 (10 points + 2 bonus points):
## Logical Foundations of Mathematics

Replace the placeholders (e.g., `:= sorry`) with your solutions.


```lean
set_option autoImplicit false
set_option tactic.hygienic false

namespace LoVe
```


### Question 1 (8 points): Even Numbers as a Subtype

Usually, the most convenient way to represent even natural numbers is to use the
larger type `ℕ`, which also includes the odd natural numbers. If we want to
quantify only over even numbers `n`, we can add an assumption `Even n` to our
theorem statement.

An alternative is to encode evenness in the type, using a subtype. We will
explore this approach.

1.1 (1 point). Define the type `Eveℕ` of even natural numbers, using the `Even`
predicate introduced in the lecture 5 demo.


```lean
#print Even

def Eveℕ : Type :=
  sorry
```


1.2 (1 point). Prove the following theorem about the `Even` predicate. You will
need it to answer question 1.3.

Hint: The theorems `add_assoc` and `add_comm` might be useful.


```lean
theorem Even.add {m n : ℕ} (hm : Even m) (hn : Even n) :
    Even (m + n) :=
  sorry
```


1.3 (2 points). Define zero and addition of even numbers by filling in the
`sorry` placeholders.


```lean
def Eveℕ.zero : Eveℕ :=
  sorry

def Eveℕ.add (m n : Eveℕ) : Eveℕ :=
  sorry
```


1.4 (4 points). Prove that addition of even numbers is commutative and
associative, and has 0 as an identity element.


```lean
theorem Eveℕ.add_comm (m n : Eveℕ) :
    Eveℕ.add m n = Eveℕ.add n m :=
  sorry

theorem Eveℕ.add_assoc (l m n : Eveℕ) :
    Eveℕ.add (Eveℕ.add l m) n = Eveℕ.add l (Eveℕ.add m n) :=
  sorry

theorem Eveℕ.add_iden_left (n : Eveℕ) :
    Eveℕ.add Eveℕ.zero n = n :=
  sorry

theorem Eveℕ.add_iden_right (n : Eveℕ) :
    Eveℕ.add n Eveℕ.zero = n :=
  sorry
```


### Question 2 (2 points + 2 bonus points): Hilbert Choice

2.1 (2 bonus points). Prove the following theorem.

Hints:

* A good way to start is to make a case distinction on whether `∃n, f n < x`
  is true or false.

* The theorem `le_of_not_gt` might be useful.


```lean
theorem exists_minimal_arg_helper (f : ℕ → ℕ) :
    ∀x m, f m = x → ∃n, ∀i, f n ≤ f i
  | x, m, eq =>
    by
      sorry
```


Now this interesting theorem falls off:


```lean
theorem exists_minimal_arg (f : ℕ → ℕ) :
    ∃n : ℕ, ∀i : ℕ, f n ≤ f i :=
  exists_minimal_arg_helper f _ 0 (by rfl)
```


2.2 (1 point). Use what you learned about Hilbert choice in the lecture to
define the following function, which returns the (or an) index of the minimal
element in `f`'s image.


```lean
noncomputable def minimal_arg (f : ℕ → ℕ) : ℕ :=
  sorry
```


2.3 (1 point). Prove the following characteristic theorem about your
definition.


```lean
theorem minimal_arg_spec (f : ℕ → ℕ) :
    ∀i : ℕ, f (minimal_arg f) ≤ f i :=
  sorry

end LoVe
```

---



## Basic Mathematical Structures (Demo) {#hhg-basic-mathematical-structures-demo}

> 📄 Source: https://raw.githubusercontent.com/lean-forward/logical_verification_2025/main/lean/LoVe/LoVe13_BasicMathematicalStructures_Demo.lean

```lean
import LoVe.LoVe06_InductivePredicates_Demo
```


## LoVe Demo 13: Basic Mathematical Structures

We introduce definitions and proofs about basic mathematical structures such as
groups, fields, and linear orders.


```lean
set_option autoImplicit false
set_option tactic.hygienic false

namespace LoVe
```


### Type Classes over a Single Binary Operator

Mathematically, a __group__ is a set `G` with a binary operator `⬝ : G × G → G`
with the following properties, called __group axioms__:

* associativity: for all `a, b, c ∈ G`, we have `(a ⬝ b) ⬝ c = a ⬝ (b ⬝ c)`;
* identity element: there exists an element `e ∈ G` such that for all `a ∈ G`,
  we have `e ⬝ a = a` and `a ⬝ e = a`;
* inverse element: for each `a ∈ G`, there exists an inverse element
  `b ∈ G` such that `b ⬝ a = e` and `a ⬝ b = e`.

Examples of groups are
* `ℤ` with `+`;
* `ℝ` with `+`;
* `ℝ \ {0}` with `*`.

In Lean, a type class for groups can be defined as follows:


```lean
namespace MonolithicGroup

class Group (α : Type) where
  mul          : α → α → α
  one          : α
  inv          : α → α
  mul_assoc    : ∀a b c, mul (mul a b) c = mul a (mul b c)
  one_mul      : ∀a, mul one a = a
  mul_left_inv : ∀a, mul (inv a) a = one

end MonolithicGroup
```


In Lean, however, group is part of a larger hierarchy of algebraic
structures:

Type class             | Properties                               | Examples
---------------------- | -----------------------------------------|-------------------
`Semigroup`            | associativity of `*`                     | `ℝ`, `ℚ`, `ℤ`, `ℕ`
`Monoid`               | `Semigroup` with unit `1`                | `ℝ`, `ℚ`, `ℤ`, `ℕ`
`LeftCancelSemigroup`  | `Semigroup` with `c * a = c * b → a = b` |
`RightCancelSemigroup` | `Semigroup` with `a * c = b * c → a = b` |
`Group`                | `Monoid` with inverse `⁻¹`               |

Most of these structures have commutative versions: `CommSemigroup`,
`CommMonoid`, `CommGroup`.

The __multiplicative__ structures (over `*`, `1`, `⁻¹`) are copied to produce
__additive__ versions (over `+`, `0`, `-`):

Type class                | Properties                                  | Examples
------------------------- | --------------------------------------------|-------------------
`AddSemigroup`            | associativity of `+`                        | `ℝ`, `ℚ`, `ℤ`, `ℕ`
`AddMonoid`               | `AddSemigroup` with unit `0`                | `ℝ`, `ℚ`, `ℤ`, `ℕ`
`AddLeftCancelSemigroup`  | `AddSemigroup` with `c + a = c + b → a = b` | `ℝ`, `ℚ`, `ℤ`, `ℕ`
`AddRightCancelSemigroup` | `AddSemigroup` with `a + c = b + c → a = b` | `ℝ`, `ℚ`, `ℤ`, `ℕ`
`AddGroup`                | `AddMonoid` with inverse `-`                | `ℝ`, `ℚ`, `ℤ`


```lean
#print Group
#print AddGroup
```


Let us define our own type, of integers modulo 2, and register it as an
additive group.


```lean
inductive Int2 : Type
  | zero
  | one

def Int2.add : Int2 → Int2 → Int2
  | Int2.zero, a         => a
  | Int2.one,  Int2.zero => Int2.one
  | Int2.one,  Int2.one  => Int2.zero

instance Int2.AddGroup : AddGroup Int2 :=
  { add            := Int2.add
    zero           := Int2.zero
    neg            := fun a ↦ a
    add_assoc      :=
      by
        intro a b c
        cases a <;>
          cases b <;>
          cases c <;>
          rfl
    zero_add       :=
      by
        intro a
        cases a <;>
          rfl
    add_zero       :=
      by
        intro a
        cases a <;>
          rfl
    neg_add_cancel :=
      by
        intro a
        cases a <;>
          rfl
    nsmul         :=
      @nsmulRec Int2 (Zero.mk Int2.zero) (Add.mk Int2.add)
    zsmul         :=
      @zsmulRec Int2 (Zero.mk Int2.zero) (Add.mk Int2.add)
        (Neg.mk (fun a ↦ a))
        (@nsmulRec Int2 (Zero.mk Int2.zero) (Add.mk Int2.add)) }
```


`nsmul` and `znmul` are redundant. They are needed for technical reasons.


```lean
#reduce Int2.one + 0 - 0 - Int2.one
```


Another example: Lists are an `AddMonoid`:


```lean
instance List.AddMonoid {α : Type} : AddMonoid (List α) :=
  { zero      := []
    add       := fun xs ys ↦ xs ++ ys
    add_assoc := List.append_assoc
    zero_add  := List.nil_append
    add_zero  := List.append_nil
    nsmul     :=
      @nsmulRec (List α) (Zero.mk [])
        (Add.mk (fun xs ys ↦ xs ++ ys))}
```


### Type Classes with Two Binary Operators

Mathematically, a __field__ is a set `F` such that

* `F` forms a commutative group under an operator `+`, called addition, with
  identity element `0`.
* `F \ {0}` forms a commutative group under an operator `*`, called
  multiplication.
* Multiplication distributes over addition—i.e.,
  `a * (b + c) = a * b + a * c` for all `a, b, c ∈ F`.

In Lean, fields are also part of a larger hierarchy:

Type class      |  Properties                                         | Examples
----------------|-----------------------------------------------------|-------------------
`Semiring`      | `Monoid` and `AddCommMonoid` with distributivity    | `ℝ`, `ℚ`, `ℤ`, `ℕ`
`CommSemiring`  | `Semiring` with commutativity of `*`                | `ℝ`, `ℚ`, `ℤ`, `ℕ`
`Ring`          | `Monoid` and `AddCommGroup` with distributivity     | `ℝ`, `ℚ`, `ℤ`
`CommRing`      | `Ring` with commutativity of `*`                    | `ℝ`, `ℚ`, `ℤ`
`DivisionRing`  | `Ring` with multiplicative inverse `⁻¹`             | `ℝ`, `ℚ`
`Field`         | `DivisionRing` with commutativity of `*`            | `ℝ`, `ℚ`


```lean
#print Field
```


We continue with our example:


```lean
def Int2.mul : Int2 → Int2 → Int2
  | Int2.one,  a => a
  | Int2.zero, _ => Int2.zero

theorem Int2.mul_assoc (a b c : Int2) :
     Int2.mul (Int2.mul a b) c = Int2.mul a (Int2.mul b c) :=
  by
    cases a <;>
      cases b <;>
      cases c <;>
      rfl

instance Int2.Field : Field Int2 :=
  { Int2.AddGroup with
    one            := Int2.one
    mul            := Int2.mul
    inv            := fun a ↦ a
    add_comm       :=
      by
        intro a b
        cases a <;>
          cases b <;>
          rfl
    exists_pair_ne :=
      by
        apply Exists.intro Int2.zero
        apply Exists.intro Int2.one
        simp
    zero_mul       :=
      by
        intro a
        rfl
    mul_zero       :=
      by
        intro a
        cases a <;>
          rfl
    one_mul        :=
      by
        intro a
        rfl
    mul_one        :=
      by
        intro a
        cases a <;>
          rfl
    mul_inv_cancel :=
      by
        intro a h
        cases a
        { apply False.elim
          apply h
          rfl }
        { rfl }
    inv_zero       := by rfl
    mul_assoc      := Int2.mul_assoc
    mul_comm       :=
      by
        intro a b
        cases a <;>
          cases b <;>
          rfl
    left_distrib   :=
      by
        intro a b c
        cases a <;>
          cases b <;>
          rfl
    right_distrib  :=
      by
        intro a b c
        cases a <;>
          cases b <;>
          cases c <;>
          rfl
    nnqsmul        := _
    nnqsmul_def    :=
      by
        intro a b
        rfl
    qsmul          := _
    qsmul_def :=
      by
        intro a b
        rfl
    nnratCast_def  :=
      by
        intro q
        rfl }

#reduce (1 : Int2) * 0 / (0 - 1)

#reduce (3 : Int2)

theorem ring_example (a b : Int2) :
    (a + b) ^ 3 = a ^ 3 + 3 * a ^ 2 * b + 3 * a * b ^ 2 + b ^ 3
    :=
  by ring
```


`ring` proves equalities over commutative rings and semirings by normalizing
expressions.


### Coercions

When combining numbers form `ℕ`, `ℤ`, `ℚ`, and `ℝ`, we might want to cast from
one type to another. Lean has a mechanism to automatically introduce coercions,
represented by `Coe.coe` (syntactic sugar: `↑`). `Coe.coe` can be set up to
provide implicit coercions between arbitrary types.

Many coercions are already in place, including the following:

* `Coe.coe : ℕ → α` casts `ℕ` to another semiring `α`;
* `Coe.coe : ℤ → α` casts `ℤ` to another ring `α`;
* `Coe.coe : ℚ → α` casts `ℚ` to another division ring `α`.

For example, this works, even though negation `- n` is not defined on natural
numbers:


```lean
theorem neg_mul_neg_Nat (n : ℕ) (z : ℤ) :
    (- z) * (- n) = z * n :=
  by simp
```


Notice how Lean introduced a `↑` coercion:


```lean
#check neg_mul_neg_Nat
```


Type annotations can document our intentions:


```lean
theorem neg_Nat_mul_neg (n : ℕ) (z : ℤ) :
    (- n : ℤ) * (- z) = n * z :=
  by simp

#print neg_Nat_mul_neg
```


In proofs involving coercions, the tactic `norm_cast` can be convenient.


```lean
theorem Eq_coe_int_imp_Eq_Nat (m n : ℕ)
      (h : (m : ℤ) = (n : ℤ)) :
    m = n :=
  by norm_cast at h

theorem Nat_coe_Int_add_eq_add_Nat_coe_Int (m n : ℕ) :
    (m : ℤ) + (n : ℤ) = ((m + n : ℕ) : ℤ) :=
  by norm_cast
```


`norm_cast` moves coercions towards the inside of expressions, as a form of
simplification. Like `simp`, it will often produce a subgoal.

`norm_cast` relies on theorems such as these:


```lean
#check Nat.cast_add
#check Int.cast_add
#check Rat.cast_add
```


#### Lists, Multisets, and Finite Sets

For finite collections of elements different structures are available:

* lists: order and duplicates matter;
* multisets: only duplicates matter;
* finsets: neither order nor duplicates matter.


```lean
theorem List_duplicates_example :
    [2, 3, 3, 4] ≠ [2, 3, 4] :=
  by decide

theorem List_order_example :
    [4, 3, 2] ≠ [2, 3, 4] :=
  by decide

theorem Multiset_duplicates_example :
    ({2, 3, 3, 4} : Multiset ℕ) ≠ {2, 3, 4} :=
  by decide

theorem Multiset_order_example :
    ({2, 3, 4} : Multiset ℕ) = {4, 3, 2} :=
  by decide

theorem Finset_duplicates_example :
    ({2, 3, 3, 4} : Finset ℕ) = {2, 3, 4} :=
  by decide

theorem Finset_order_example :
    ({2, 3, 4} : Finset ℕ) = {4, 3, 2} :=
  by decide
```


`decide` is a tactic that can be used on true decidable goals (e.g., a true
executable expression).


```lean
def List.elems : Tree ℕ → List ℕ
  | Tree.nil        => []
  | Tree.node a l r => a :: List.elems l ++ List.elems r

def Multiset.elems : Tree ℕ → Multiset ℕ
  | Tree.nil        => ∅
  | Tree.node a l r =>
    {a} ∪ Multiset.elems l ∪ Multiset.elems r

def Finset.elems : Tree ℕ → Finset ℕ
  | Tree.nil        => ∅
  | Tree.node a l r => {a} ∪ Finset.elems l ∪ Finset.elems r

#eval List.sum [2, 3, 4]
#eval Multiset.sum ({2, 3, 4} : Multiset ℕ)

#eval List.prod [2, 3, 4]
#eval Multiset.prod ({2, 3, 4} : Multiset ℕ)
```


### Order Type Classes

Many of the structures introduced above can be ordered. For example, the
well-known order on the natural numbers can be defined as follows:


```lean
inductive Nat.le : ℕ → ℕ → Prop
  | refl : ∀a : ℕ, Nat.le a a
  | step : ∀a b : ℕ, Nat.le a b → Nat.le a (b + 1)

#print Preorder
```


This is an example of a linear order. A __linear order__ (or
__total order__) is a binary relation `≤` such that for all `a`, `b`, `c`, the
following properties hold:

* reflexivity: `a ≤ a`;
* transitivity: if `a ≤ b` and `b ≤ c`, then `a ≤ c`;
* antisymmetry: if `a ≤ b` and `b ≤ a`, then `a = b`;
* totality: `a ≤ b` or `b ≤ a`.

If a relation has the first three properties, it is a __partial order__. An
example is `⊆` on sets, finite sets, or multisets. If a relation has the first
two properties, it is a __preorder__. An example is comparing lists by their
length.

In Lean, there are type classes for these different kinds of orders:
`LinearOrdeer`, `PartialOrder`, and `Preorder`.


```lean
#print Preorder
#print PartialOrder
#print LinearOrder
```


We can declare the preorder on lists that compares lists by their length as
follows:


```lean
instance List.length.Preorder {α : Type} : Preorder (List α) :=
  { le :=
      fun xs ys ↦ List.length xs ≤ List.length ys
    lt :=
      fun xs ys ↦ List.length xs < List.length ys
    le_refl :=
      by
        intro xs
        apply Nat.le_refl
    le_trans :=
      by
        intro xs ys zs
        exact Nat.le_trans
    lt_iff_le_not_le :=
      by
        intro a b
        exact Nat.lt_iff_le_not_le }
```


This instance introduces the infix syntax `≤` and the relations `≥`, `<`,
and `>`:


```lean
theorem list.length.Preorder_example :
    [1] > [] :=
  by decide
```


Complete lattices (lecture 11) are formalized as another type class,
`CompleteLattice`, which inherits from `PartialOrder`.

Type classes combining orders and algebraic structures are also available:

    `OrderedCancelCommMonoid`
    `OrderedCommGroup`
    `OrderedSemiring`
    `LinearOrderedSemiring`
    `LinearOrderedCommRing`
    `LinearOrderedField`

All these mathematical structures relate `≤` and `<` with `0`, `1`, `+`, and `*`
by monotonicity rules (e.g., `a ≤ b → c ≤ d → a + c ≤ b + d`) and cancellation
rules (e.g., `c + a ≤ c + b → a ≤ b`).


```lean
end LoVe
```

---



## Basic Mathematical Structures (ExerciseSheet) {#hhg-basic-mathematical-structures-exercisesheet}

> 📄 Source: https://raw.githubusercontent.com/lean-forward/logical_verification_2025/main/lean/LoVe/LoVe13_BasicMathematicalStructures_ExerciseSheet.lean

```lean
import LoVe.LoVe13_BasicMathematicalStructures_Demo
```


## LoVe Exercise 13: Basic Mathematical Structures

Replace the placeholders (e.g., `:= sorry`) with your solutions.


```lean
set_option autoImplicit false
set_option tactic.hygienic false

namespace LoVe
```


### Question 1: Type Classes

Recall the inductive type `Tree` we introduced in lecture 5:


```lean
#check Tree
```


The following function takes two trees and attaches copies of the second
tree to each leaf of the first tree.


```lean
def Tree.graft {α : Type} : Tree α → Tree α → Tree α
  | Tree.nil,        u => u
  | Tree.node a l r, u =>
    Tree.node a (Tree.graft l u) (Tree.graft r u)

#reduce Tree.graft (Tree.node 1 Tree.nil Tree.nil)
  (Tree.node 2 Tree.nil Tree.nil)
```


1.1. Prove the following two theorems by structural induction on `t`.


```lean
theorem Tree.graft_assoc {α : Type} (t u v : Tree α) :
    Tree.graft (Tree.graft t u) v = Tree.graft t (Tree.graft u v) :=
  sorry

theorem Tree.graft_nil {α : Type} (t : Tree α) :
    Tree.graft t Tree.nil = t :=
  sorry
```


1.2. Declare `Tree` an instance of `AddMonoid` using `graft` as the
addition operator.


```lean
#print AddMonoid

instance Tree.AddMonoid {α : Type} : AddMonoid (Tree α) :=
  { add       := Tree.graft
    add_assoc :=
      sorry
    zero      := Tree.nil
    add_zero  :=
      sorry
    zero_add  :=
      sorry
    nsmul     := @nsmulRec (Tree α) (Zero.mk Tree.nil) (Add.mk Tree.graft)
  }
```


1.3 (**optional**). Explain why `Tree` with `graft` as addition cannot be
declared an instance of `AddGroup`.


```lean
#print AddGroup

-- enter your explanation here
```


1.4 (**optional**). Prove the following theorem illustrating why `Tree`
with `graft` as addition does not constitute an `AddGroup`.


```lean
theorem Tree.add_left_neg_counterexample :
    ∃x : Tree ℕ, ∀y : Tree ℕ, Tree.graft y x ≠ Tree.nil :=
  sorry
```


### Question 2: Multisets and Finsets

Recall the following definitions from the lecture:


```lean
#check Finset.elems
#check List.elems
```


2.1. Prove that the finite set of nodes does not change when mirroring a
tree.


```lean
theorem Finset.elems_mirror (t : Tree ℕ) :
    Finset.elems (mirror t) = Finset.elems t :=
  sorry
```


2.2. Show that this does not hold for the list of nodes by providing a
tree `t` for which `List.elems t ≠ List.elems (mirror t)`.

If you define a suitable counterexample, the proof below will succeed.


```lean
def rottenTree : Tree ℕ :=
  sorry

#eval List.elems rottenTree
#eval List.elems (mirror rottenTree)

theorem List.elems_mirror_counterexample :
    ∃t : Tree ℕ, List.elems t ≠ List.elems (mirror t) :=
  by
    apply Exists.intro rottenTree
    simp [List.elems]

end LoVe
```

---



## Rational and Real Numbers (Demo) {#hhg-rational-and-real-numbers-demo}

> 📄 Source: https://raw.githubusercontent.com/lean-forward/logical_verification_2025/main/lean/LoVe/LoVe14_RationalAndRealNumbers_Demo.lean

```lean
import LoVe.LoVelib
```


## LoVe Demo 14: Rational and Real Numbers

We review the construction of `ℚ` and `ℝ` as quotient types.

Our procedure to construct types with specific properties:

1. Create a new type that can represent all elements, but not necessarily in a
   unique manner.

2. Quotient this representation, equating elements that should be equal.

3. Define operators on the quotient type by lifting functions from the base
   type and prove that they are compatible with the quotient relation.

We used this approach in lecture 12 to construct `ℤ`. It can be used for `ℚ` and
`ℝ` as well.


```lean
set_option autoImplicit false
set_option tactic.hygienic false

namespace LoVe
```


### Rational Numbers

**Step 1:** A rational number is a number that can be expressed as a fraction
`n / d` of integers `n` and `d ≠ 0`:


```lean
structure Fraction where
  num            : ℤ
  denom          : ℤ
  denom_Neq_zero : denom ≠ 0
```


The number `n` is called the numerator, and the number `d` is called the
denominator.

The representation of a rational number as a fraction is not unique—e.g.,
`1 / 2 = 2 / 4 = -1 / -2`.

**Step 2:** Two fractions `n₁ / d₁` and `n₂ / d₂` represent the same rational
number if the ratio between numerator and denominator are the same—i.e.,
`n₁ * d₂ = n₂ * d₁`. This will be our equivalence relation `≈` on fractions.


```lean
namespace Fraction

instance Setoid : Setoid Fraction :=
  { r :=
      fun a b : Fraction ↦ num a * denom b = num b * denom a
    iseqv :=
      { refl  := by aesop
        symm  := by aesop
        trans :=
          by
            intro a b c heq_ab heq_bc
            apply Int.eq_of_mul_eq_mul_right (denom_Neq_zero b)
            calc
              num a * denom c * denom b
              = num a * denom b * denom c :=
                by ac_rfl
              _ = num b * denom a * denom c :=
                by rw [heq_ab]
              _ = num b * denom c * denom a :=
                by ac_rfl
              _ = num c * denom b * denom a :=
                by rw [heq_bc]
              _ = num c * denom a * denom b :=
                by ac_rfl
            } }

theorem Setoid_Iff (a b : Fraction) :
    a ≈ b ↔ num a * denom b = num b * denom a :=
  by rfl
```


**Step 3:** Define `0 := 0 / 1`, `1 := 1 / 1`, addition, multiplication, etc.

    `n₁ / d₁ + n₂ / d₂`     := `(n₁ * d₂ + n₂ * d₁) / (d₁ * d₂)`
    `(n₁ / d₁) * (n₂ / d₂)` := `(n₁ * n₂) / (d₁ * d₂)`

Then show that they are compatible with `≈`.


```lean
def of_int (i : ℤ) : Fraction :=
  { num            := i
    denom          := 1
    denom_Neq_zero := by simp }

instance Zero : Zero Fraction :=
  { zero := of_int 0 }

instance One : One Fraction :=
  { one := of_int 1 }

instance Add : Add Fraction :=
  { add := fun a b : Fraction ↦
      { num            := num a * denom b + num b * denom a
        denom          := denom a * denom b
        denom_Neq_zero := by simp [denom_Neq_zero] } }

@[simp] theorem add_num (a b : Fraction) :
    num (a + b) = num a * denom b + num b * denom a :=
  by rfl

@[simp] theorem add_denom (a b : Fraction) :
    denom (a + b) = denom a * denom b :=
  by rfl

theorem Setoid_add {a a' b b' : Fraction} (ha : a ≈ a')
      (hb : b ≈ b') :
    a + b ≈ a' + b' :=
  by
    simp [Setoid_Iff, add_denom, add_num] at *
    calc
      (num a * denom b + num b * denom a)
          * (denom a' * denom b')
      = num a * denom a' * denom b * denom b'
        + num b * denom b' * denom a * denom a' :=
        by
          simp [add_mul, mul_add]
          ac_rfl
      _ = num a' * denom a * denom b * denom b'
            + num b' * denom b * denom a * denom a' :=
        by simp [*]
      _ = (num a' * denom b' + num b' * denom a')
            * (denom a * denom b) :=
        by
          simp [add_mul, mul_add]
          ac_rfl

instance Neg : Neg Fraction :=
  { neg := fun a : Fraction ↦
      { a with
        num := - num a } }

@[simp] theorem neg_num (a : Fraction) :
    num (- a) = - num a :=
  by rfl

@[simp] theorem neg_denom (a : Fraction) :
    denom (- a) = denom a :=
  by rfl

theorem Setoid_neg {a a' : Fraction} (hab : a ≈ a') :
    - a ≈ - a' :=
  by
    simp [Setoid_Iff] at *
    exact hab

instance Mul : Mul Fraction :=
  { mul := fun a b : Fraction ↦
      { num            := num a * num b
        denom          := denom a * denom b
        denom_Neq_zero :=
          by simp [Int.mul_eq_zero, denom_Neq_zero] } }

@[simp] theorem mul_num (a b : Fraction) :
    num (a * b) = num a * num b :=
  by rfl

@[simp] theorem mul_denom (a b : Fraction) :
    denom (a * b) = denom a * denom b :=
  by rfl

theorem Setoid_mul {a a' b b' : Fraction} (ha : a ≈ a')
      (hb : b ≈ b') :
    a * b ≈ a' * b' :=
  by
    simp [Setoid_Iff] at *
    calc
      num a * num b * (denom a' * denom b')
      = (num a * denom a') * (num b * denom b') :=
        by ac_rfl
      _ = (num a' * denom a) * (num b' * denom b) :=
        by simp [*]
      _ = num a' * num b' * (denom a * denom b) :=
        by ac_rfl

instance Inv : Inv Fraction :=
  { inv := fun a : Fraction ↦
      if ha : num a = 0 then
        0
      else
        { num            := denom a
          denom          := num a
          denom_Neq_zero := ha } }

theorem inv_def (a : Fraction) (ha : num a ≠ 0) :
    a⁻¹ =
    { num            := denom a
      denom          := num a
      denom_Neq_zero := ha } :=
  dif_neg ha

theorem inv_zero (a : Fraction) (ha : num a = 0) :
    a⁻¹ = 0 :=
  dif_pos ha

@[simp] theorem inv_num (a : Fraction) (ha : num a ≠ 0) :
    num (a⁻¹) = denom a :=
  by rw [inv_def a ha]

@[simp] theorem inv_denom (a : Fraction) (ha : num a ≠ 0) :
    denom (a⁻¹) = num a :=
  by rw [inv_def a ha]

theorem Setoid_inv {a a' : Fraction} (ha : a ≈ a') :
    a⁻¹ ≈ a'⁻¹ :=
  by
    cases Classical.em (num a = 0) with
    | inl ha0 =>
      cases Classical.em (num a' = 0) with
      | inl ha'0 =>
        simp [ha0, ha'0, inv_zero]
      | inr ha'0 =>
        simp [ha0, ha'0, Setoid_Iff, denom_Neq_zero] at ha
    | inr ha0 =>
      cases Classical.em (num a' = 0) with
      | inl ha'0 =>
        simp [ha0, ha'0, Setoid_Iff, denom_Neq_zero] at ha
      | inr ha'0 =>
        simp [Setoid_Iff, ha0, ha'0] at *
        linarith

end Fraction

def Rat : Type :=
  Quotient Fraction.Setoid

namespace Rat

def mk : Fraction → Rat :=
  Quotient.mk Fraction.Setoid

instance Zero : Zero Rat :=
  { zero := mk 0 }

instance One : One Rat :=
  { one := mk 1 }

instance Add : Add Rat :=
  { add := Quotient.lift₂ (fun a b : Fraction ↦ mk (a + b))
      (by
         intro a b a' b' ha hb
         apply Quotient.sound
         exact Fraction.Setoid_add ha hb) }

instance Neg : Neg Rat :=
  { neg := Quotient.lift (fun a : Fraction ↦ mk (- a))
      (by
         intro a a' ha
         apply Quotient.sound
         exact Fraction.Setoid_neg ha) }

instance Mul : Mul Rat :=
  { mul := Quotient.lift₂ (fun a b : Fraction ↦ mk (a * b))
      (by
         intro a b a' b' ha hb
         apply Quotient.sound
         exact Fraction.Setoid_mul ha hb) }

instance Inv : Inv Rat :=
  { inv := Quotient.lift (fun a : Fraction ↦ mk (a⁻¹))
      (by
         intro a a' ha
         apply Quotient.sound
         exact Fraction.Setoid_inv ha) }

end Rat
```


#### Alternative Definition of `ℚ`

Define `ℚ` as a subtype of `fraction`, with the requirement that the denominator
is positive and that the numerator and the denominator have no common divisors
except `1` and `-1`:


```lean
namespace Alternative

def Rat.IsCanonical (a : Fraction) : Prop :=
  Fraction.denom a > 0
  ∧ Nat.Coprime (Int.natAbs (Fraction.num a))
      (Int.natAbs (Fraction.denom a))

def Rat : Type :=
  {a : Fraction // Rat.IsCanonical a}

end Alternative
```


This is more or less the `mathlib` definition.

Advantages:

* no quotient required;
* more efficient computation;
* more properties are syntactic equalities up to computation.

Disadvantage:

* more complicated function definitions.


#### Real Numbers

Some sequences of rational numbers seem to converge because the numbers in the
sequence get closer and closer to each other, and yet do not converge to a
rational number.

Example:

    `a₀ = 1`
    `a₁ = 1.4`
    `a₂ = 1.41`
    `a₃ = 1.414`
    `a₄ = 1.4142`
    `a₅ = 1.41421`
    `a₆ = 1.414213`
    `a₇ = 1.4142135`
       ⋮

This sequence seems to converge because each `a_n` is at most `10^-n` away from
any of the following numbers. But the limit is `√2`, which is not a rational
number.

The rational numbers are incomplete, and the reals are their  __completion__.

To construct the reals, we need to fill in the gaps that are revealed by these
sequences that seem to converge, but do not.

Mathematically, a sequence `a₀, a₁, …` of rational numbers is __Cauchy__ if for
any `ε > 0`, there exists an `N ∈ ℕ` such that for all `m ≥ N`, we have
`|a_N - a_m| < ε`.

In other words, no matter how small we choose `ε`, we can always find a point in
the sequence from which all following numbers deviate less than by `ε`.


```lean
def IsCauchySeq (f : ℕ → ℚ) : Prop :=
  ∀ε > 0, ∃N, ∀m ≥ N, abs (f N - f m) < ε
```


Not every sequence is a Cauchy sequence:


```lean
theorem id_Not_CauchySeq :
    ¬ IsCauchySeq (fun n : ℕ ↦ (n : ℚ)) :=
  by
    rw [IsCauchySeq]
    intro h
    cases h 1 zero_lt_one with
    | intro i hi =>
      have hi_succi :=
        hi (i + 1) (by simp)
      simp [←sub_sub] at hi_succi
```


We define a type of Cauchy sequences as a subtype:


```lean
def CauchySeq : Type :=
  {f : ℕ → ℚ // IsCauchySeq f}

def seqOf (f : CauchySeq) : ℕ → ℚ :=
  Subtype.val f
```


Cauchy sequences represent real numbers:

* `a_n = 1 / n` represents the real number `0`;
* `1, 1.4, 1.41, …` represents the real number `√2`;
* `a_n = 0` also represents the real number `0`.

Since different Cauchy sequences can represent the same real number, we need to
take the quotient. Formally, two sequences represent the same real number when
their difference converges to zero:


```lean
namespace CauchySeq

instance Setoid : Setoid CauchySeq :=
{ r :=
    fun f g : CauchySeq ↦
      ∀ε > 0, ∃N, ∀m ≥ N, abs (seqOf f m - seqOf g m) < ε
  iseqv :=
    { refl :=
        by
          intro f ε hε
          apply Exists.intro 0
          aesop
      symm :=
        by
          intro f g hfg ε hε
          cases hfg ε hε with
          | intro N hN =>
            apply Exists.intro N
            intro m hm
            rw [abs_sub_comm]
            apply hN m hm
      trans :=
        by
          intro f g h hfg hgh ε hε
          cases hfg (ε / 2) (by linarith) with
          | intro N₁ hN₁ =>
            cases hgh (ε / 2) (by linarith) with
            | intro N₂ hN₂ =>
              apply Exists.intro (max N₁ N₂)
              intro m hm
              calc
                abs (seqOf f m - seqOf h m)
                ≤ abs (seqOf f m - seqOf g m)
                  + abs (seqOf g m - seqOf h m) :=
                  by apply abs_sub_le
              _ < ε / 2 + ε / 2 :=
                add_lt_add (hN₁ m (le_of_max_le_left hm))
                  (hN₂ m (le_of_max_le_right hm))
              _ = ε :=
                by simp } }

theorem Setoid_iff (f g : CauchySeq) :
    f ≈ g ↔
    ∀ε > 0, ∃N, ∀m ≥ N, abs (seqOf f m - seqOf g m) < ε :=
  by rfl
```


We can define constants such as `0` and `1` as a constant sequence. Any
constant sequence is a Cauchy sequence:


```lean
def const (q : ℚ) : CauchySeq :=
  Subtype.mk (fun _ : ℕ ↦ q)
    (by
       rw [IsCauchySeq]
       intro ε hε
       aesop)
```


Defining addition of real numbers requires a little more effort. We define
addition on Cauchy sequences as pairwise addition:


```lean
instance Add : Add CauchySeq :=
  { add := fun f g : CauchySeq ↦
      Subtype.mk (fun n : ℕ ↦ seqOf f n + seqOf g n) sorry }
```


Above, we omit the proof that the addition of two Cauchy sequences is again
a Cauchy sequence.

Next, we need to show that this addition is compatible with `≈`:


```lean
theorem Setoid_add {f f' g g' : CauchySeq} (hf : f ≈ f')
      (hg : g ≈ g') :
    f + g ≈ f' + g' :=
  by
    intro ε₀ hε₀
    simp [Setoid_iff]
    cases hf (ε₀ / 2) (by linarith) with
    | intro Nf hNf =>
      cases hg (ε₀ / 2) (by linarith) with
      | intro Ng hNg =>
        apply Exists.intro (max Nf Ng)
        intro m hm
        calc
          abs (seqOf (f + g) m - seqOf (f' + g') m)
          = abs ((seqOf f m + seqOf g m)
            - (seqOf f' m + seqOf g' m)) :=
            by rfl
          _ = abs ((seqOf f m - seqOf f' m)
              + (seqOf g m - seqOf g' m)) :=
            by
              have arg_eq :
                seqOf f m + seqOf g m
                  - (seqOf f' m + seqOf g' m) =
                seqOf f m - seqOf f' m
                  + (seqOf g m - seqOf g' m) :=
                by linarith
              rw [arg_eq]
          _ ≤ abs (seqOf f m - seqOf f' m)
              + abs (seqOf g m - seqOf g' m) :=
            by apply abs_add
          _ < ε₀ / 2 + ε₀ / 2 :=
            add_lt_add (hNf m (le_of_max_le_left hm))
              (hNg m (le_of_max_le_right hm))
          _ = ε₀ :=
            by simp

end CauchySeq
```


The real numbers are the quotient:


```lean
def Real : Type :=
  Quotient CauchySeq.Setoid

namespace Real

instance Zero : Zero Real :=
  { zero := ⟦CauchySeq.const 0⟧ }

instance One : One Real :=
  { one := ⟦CauchySeq.const 1⟧ }

instance Add : Add Real :=
{ add := Quotient.lift₂ (fun a b : CauchySeq ↦ ⟦a + b⟧)
    (by
       intro a b a' b' ha hb
       apply Quotient.sound
       exact CauchySeq.Setoid_add ha hb) }

end Real
```


#### Alternative Definitions of `ℝ`

* Dedekind cuts: `r : ℝ` is represented essentially as `{x : ℚ | x < r}`.

* Binary sequences `ℕ → Bool` can represent the interval `[0, 1]`. This can be
  used to build `ℝ`.


```lean
end LoVe
```

---



## Rational and Real Numbers (ExerciseSheet) {#hhg-rational-and-real-numbers-exercisesheet}

> 📄 Source: https://raw.githubusercontent.com/lean-forward/logical_verification_2025/main/lean/LoVe/LoVe14_RationalAndRealNumbers_ExerciseSheet.lean

```lean
import LoVe.LoVe06_InductivePredicates_Demo
import LoVe.LoVe14_RationalAndRealNumbers_Demo
```


## LoVe Exercise 14: Rational and Real Numbers

Replace the placeholders (e.g., `:= sorry`) with your solutions.


```lean
set_option autoImplicit false
set_option tactic.hygienic false

namespace LoVe
```


### Question 1: Rationals

1.1. Prove the following theorem.

Hints:

* Start with case distinctions on `a` and `b`.

* When the goal starts getting complicated, use `simp at *` to clean it up.


```lean
theorem Fraction.ext (a b : Fraction) (hnum : Fraction.num a = Fraction.num b)
      (hdenom : Fraction.denom a = Fraction.denom b) :
    a = b :=
  sorry
```


1.2. Extending the `Fraction.Mul` instance from the lecture, declare
`Fraction` as an instance of `Semigroup`.

Hint: Use the theorem `Fraction.ext` above, and possibly `Fraction.mul_num` and
`Fraction.mul_denom`.


```lean
#check Fraction.ext
#check Fraction.mul_num
#check Fraction.mul_denom

instance Fraction.Semigroup : Semigroup Fraction :=
  { Fraction.Mul with
    mul_assoc :=
      sorry
  }
```


1.3. Extending the `Rat.Mul` instance from the lecture, declare `Rat` as an
instance of `Semigroup`.


```lean
instance Rat.Semigroup : Semigroup Rat :=
  { Rat.Mul with
    mul_assoc :=
      sorry
  }

end LoVe
```

---



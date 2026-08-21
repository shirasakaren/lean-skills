## Basic Types {#manual-basic-types}

> 📄 Source: https://lean-lang.org/doc/reference/latest/Basic-Types/

Lean includes a number of built-in types that are specially supported by the compiler.
Some, such as `[Nat](https://lean-lang.org/doc/reference/latest/Basic-Types/Natural-Numbers/#Nat___zero)`, additionally have special support in the kernel.
Other types don't have special compiler support *per se*, but rely in important ways on the internal representation of types for performance reasons.

1. [20.1. Natural Numbers](https://lean-lang.org/doc/reference/latest/Basic-Types/Natural-Numbers/#Nat)
2. [20.2. Integers](https://lean-lang.org/doc/reference/latest/Basic-Types/Integers/#Int)
3. [20.3. Finite Natural Numbers](https://lean-lang.org/doc/reference/latest/Basic-Types/Finite-Natural-Numbers/#Fin)
4. [20.4. Fixed-Precision Integers](https://lean-lang.org/doc/reference/latest/Basic-Types/Fixed-Precision-Integers/#fixed-ints)
5. [20.5. Bitvectors](https://lean-lang.org/doc/reference/latest/Basic-Types/Bitvectors/#BitVec)
6. [20.6. Floating-Point Numbers](https://lean-lang.org/doc/reference/latest/Basic-Types/Floating-Point-Numbers/#Float)
7. [20.7. Characters](https://lean-lang.org/doc/reference/latest/Basic-Types/Characters/#Char)
8. [20.8. Strings](https://lean-lang.org/doc/reference/latest/Basic-Types/Strings/#String)
9. [20.9. The Unit Type](https://lean-lang.org/doc/reference/latest/Basic-Types/The-Unit-Type/#The-Lean-Language-Reference--Basic-Types--The-Unit-Type)
10. [20.10. The Empty Type](https://lean-lang.org/doc/reference/latest/Basic-Types/The-Empty-Type/#empty)
11. [20.11. Booleans](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#The-Lean-Language-Reference--Basic-Types--Booleans)
12. [20.12. Optional Values](https://lean-lang.org/doc/reference/latest/Basic-Types/Optional-Values/#option)
13. [20.13. Tuples](https://lean-lang.org/doc/reference/latest/Basic-Types/Tuples/#tuples)
14. [20.14. Sum Types](https://lean-lang.org/doc/reference/latest/Basic-Types/Sum-Types/#sum-types)
15. [20.15. Linked Lists](https://lean-lang.org/doc/reference/latest/Basic-Types/Linked-Lists/#List)
16. [20.16. Arrays](https://lean-lang.org/doc/reference/latest/Basic-Types/Arrays/#Array)
17. [20.17. Byte Arrays](https://lean-lang.org/doc/reference/latest/Basic-Types/Byte-Arrays/#ByteArray)
18. [20.18. Ranges](https://lean-lang.org/doc/reference/latest/Basic-Types/Ranges/#ranges)
19. [20.19. Maps and Sets](https://lean-lang.org/doc/reference/latest/Basic-Types/Maps-and-Sets/#maps)
20. [20.20. Subtypes](https://lean-lang.org/doc/reference/latest/Basic-Types/Subtypes/#Subtype)
21. [20.21. Lazy Computations](https://lean-lang.org/doc/reference/latest/Basic-Types/Lazy-Computations/#Thunk)

---



## Basic Types — 20.1. Natural Numbers {#manual-basic-types-201-natural-numbers}

> 📄 Source: https://lean-lang.org/doc/reference/latest/Basic-Types/Natural-Numbers/

The natural numbers are nonnegative integers.
Logically, they are the numbers 0, 1, 2, 3, …, generated from the constructors `[Nat.zero]](#manual-Nat___zero)` and `[Nat.succ]](#manual-Nat___zero)`.
Lean imposes no upper bound on the representation of natural numbers other than physical constraints imposed by the available memory of the computer.

Because the natural numbers are fundamental to both mathematical reasoning and programming, they are specially supported by Lean's implementation. The logical model of the natural numbers is as an [inductive type]](#manual---tech-term-Inductive-types), and arithmetic operations are specified using this model. In Lean's kernel, the interpreter, and compiled code, closed natural numbers are represented as efficient arbitrary-precision integers. Sufficiently small numbers are values that don't require indirection through a pointer. Arithmetic operations are implemented by primitives that take advantage of the efficient representations.

### 20.1.1. Logical Model {#manual-nat-model}

inductive type

```lean
[Nat]](#manual-Nat___zero) : Type



[Nat]](#manual-Nat___zero) : Type
```

The natural numbers, starting at zero.

This type is special-cased by both the kernel and the compiler, and overridden with an efficient
implementation. Both use a fast arbitrary-precision arithmetic library (usually
[GMP](https://gmplib.org/)); at runtime, `[Nat]](#manual-Nat___zero)` values that are sufficiently small are unboxed.

Constructors

```lean
[Nat.zero]](#manual-Nat___zero) : [Nat]](#manual-Nat___zero)
```

Zero, the smallest natural number.

Using `[Nat.zero]](#manual-Nat___zero)` explicitly should usually be avoided in favor of the literal `0`, which is the
[simp normal form](https://lean-lang.org/doc/reference/4.34.0-rc1/find/?domain=Verso.Genre.Manual.section&name=simp-normal-forms).

```lean
[Nat.succ]](#manual-Nat___zero) (n : [Nat]](#manual-Nat___zero)) : [Nat]](#manual-Nat___zero)
```

The successor of a natural number `n`.

Using `[Nat.succ]](#manual-Nat___zero) n` should usually be avoided in favor of `n + 1`, which is the [simp normal
form](https://lean-lang.org/doc/reference/4.34.0-rc1/find/?domain=Verso.Genre.Manual.section&name=simp-normal-forms).

**Example: Proofs by Induction**

The natural numbers are an [inductive type]](#manual---tech-term-Inductive-types), so the `[induction]](#manual-induction)` tactic can be used to prove universally-quantified statements.
A proof by induction requires a base case and an induction step.
The base case is a proof that the statement is true for `0`.
The induction step is a proof that the truth of the statement for some arbitrary number `i` implies its truth for `i + 1`.

This proof uses the lemma `Nat.succ_lt_succ` in its induction step.

```lean
example (n : [Nat]](#manual-Nat___zero)) : n < n + 1 := byi:[Nat]](#manual-Nat___zero)n:[Nat]](#manual-Nat___zero)⊢ n [<]](#manual-LT___mk) n [+]](#manual-HAdd___mk) 1
[induction]](#manual-induction) n with
| [zero]](#manual-Nat___zero) =>zeroi:[Nat]](#manual-Nat___zero)⊢ 0 [<]](#manual-LT___mk) 0 [+]](#manual-HAdd___mk) 1
[show]](#manual-show) 0 < 1zeroi:[Nat]](#manual-Nat___zero)⊢ 0 [<]](#manual-LT___mk) 1
[decide]](#manual-decide)All goals completed! 🐙
| [succ]](#manual-Nat___zero) i ih =>succi✝:[Nat]](#manual-Nat___zero)i:[Nat]](#manual-Nat___zero)ih:i [<]](#manual-LT___mk) i [+]](#manual-HAdd___mk) 1⊢ i [+]](#manual-HAdd___mk) 1 [<]](#manual-LT___mk) i [+]](#manual-HAdd___mk) 1 [+]](#manual-HAdd___mk) 1 -- ih : i < i + 1
[show]](#manual-show) i + 1 < i + 1 + 1succi✝:[Nat]](#manual-Nat___zero)i:[Nat]](#manual-Nat___zero)ih:i [<]](#manual-LT___mk) i [+]](#manual-HAdd___mk) 1⊢ i [+]](#manual-HAdd___mk) 1 [<]](#manual-LT___mk) i [+]](#manual-HAdd___mk) 1 [+]](#manual-HAdd___mk) 1
[exact]](#manual-exact) Nat.succ_lt_succ ihAll goals completed! 🐙
```

#### 20.1.1.1. Peano Axioms {#manual-peano-axioms}

The Peano axioms are a consequence of this definition.
The induction principle generated for `[Nat]](#manual-Nat___zero)` is the one demanded by the axiom of induction:

```lean
Nat.rec.{u} {motive : [Nat]](#manual-Nat___zero) → Sort u}
(zero : motive [zero]](#manual-Nat___zero))
(succ : (n : [Nat]](#manual-Nat___zero)) → motive n → motive n.[succ]](#manual-Nat___zero))
(t : [Nat]](#manual-Nat___zero)) :
motive t
```

This induction principle also implements primitive recursion.
The injectivity of `[Nat.succ]](#manual-Nat___zero)` and the disjointness of `[Nat.succ]](#manual-Nat___zero)` and `Nat.zero` are consequences of the induction principle, using a construction typically called “no confusion”:

```lean
def NoConfusion : [Nat]](#manual-Nat___zero) → [Nat]](#manual-Nat___zero) → Prop
| 0, 0 => [True]](#manual-True___intro)
| 0, _ + 1 | _ + 1, 0 => [False]](#manual-False)
| n + 1, k + 1 => n = k
theorem noConfusionDiagonal (n : [Nat]](#manual-Nat___zero)) :
[NoConfusion]](#manual-NoConfusion) n n :=
Nat.rec [True.intro]](#manual-True___intro) (fun _ _ => [rfl]](#manual-rfl-next)) n
theorem noConfusion (n k : [Nat]](#manual-Nat___zero)) (eq : n = k) :
[NoConfusion]](#manual-NoConfusion) n k :=
eq ▸ [noConfusionDiagonal]](#manual-noConfusionDiagonal) n
theorem succ_injective : n + 1 = k + 1 → n = k :=
[noConfusion]](#manual-noConfusion) (n + 1) (k + 1)
theorem succ_not_zero : ¬n + 1 = 0 :=
[noConfusion]](#manual-noConfusion) (n + 1) 0
```

### 20.1.2. Run-Time Representation {#manual-nat-runtime}

The representation suggested by the declaration of `Nat` would be horrendously inefficient, as it's essentially a linked list.
The length of the list would be the number.
With this representation, addition would take time linear in the size of one of the addends, and numbers would take at least as many machine words as their magnitude in memory.
Thus, natural numbers have special support in both the kernel and the compiler that avoids this overhead.

In the kernel, there are special `Nat` literal values that use a widely-trusted, efficient arbitrary-precision integer library (usually [GMP](https://gmplib.org/)).
Basic functions such as addition are overridden by primitives that use this representation.
Because they are part of the kernel, if these primitives did not correspond to their definitions as Lean functions, it could undermine soundness.

In compiled code, sufficiently-small natural numbers are represented without pointer indirections: the lowest-order bit in an object pointer is used to indicate that the value is not, in fact, a pointer, and the remaining bits are used to store the number.
31 bits are available on 32-bits architectures for pointer-free `[Nat]](#manual-Nat___zero)`s, while 63 bits are available on 64-bit architectures.
In other words, natural numbers smaller than `2^{31} = 2,147,483,648` or `2^{63} = 9,223,372,036,854,775,808` do not require allocations.
If an natural number is too large for this representation, it is instead allocated as an ordinary Lean object that consists of an object header and an arbitrary-precision integer value.

#### 20.1.2.1. Performance Notes {#manual-nat-performance}

Using Lean's built-in arithmetic operators, rather than redefining them, is essential.
The logical model of `[Nat]](#manual-Nat___zero)` is essentially a linked list, so addition would take time linear in the size of one argument.
Still worse, multiplication takes quadratic time in this model.
While defining arithmetic from scratch can be a useful learning exercise, these redefined operations will not be nearly as fast.

### 20.1.3. Syntax {#manual-nat-syntax}

Natural number literals are overridden using the `[OfNat]](#manual-OfNat___mk)` type class, which is described in the [section on literal syntax]](#manual-nat-literals).

### 20.1.4. API Reference {#manual-nat-api}

#### 20.1.4.1. Arithmetic {#manual-nat-api-arithmetic}

def

```lean
[Nat.pred]](#manual-Nat___pred) : [Nat]](#manual-Nat___zero) → [Nat]](#manual-Nat___zero)



[Nat.pred]](#manual-Nat___pred) : [Nat]](#manual-Nat___zero) → [Nat]](#manual-Nat___zero)
```

The predecessor of a natural number is one less than it. The predecessor of `0` is defined to be
`0`.

This definition is overridden in the compiler with an efficient implementation. This definition is
the logical model.

def

```lean
[Nat.add]](#manual-Nat___add) : [Nat]](#manual-Nat___zero) → [Nat]](#manual-Nat___zero) → [Nat]](#manual-Nat___zero)



[Nat.add]](#manual-Nat___add) : [Nat]](#manual-Nat___zero) → [Nat]](#manual-Nat___zero) → [Nat]](#manual-Nat___zero)
```

Addition of natural numbers, typically used via the `+` operator.

This function is overridden in both the kernel and the compiler to efficiently evaluate using the
arbitrary-precision arithmetic library. The definition provided here is the logical model.

def

```lean
[Nat.sub]](#manual-Nat___sub) : [Nat]](#manual-Nat___zero) → [Nat]](#manual-Nat___zero) → [Nat]](#manual-Nat___zero)



[Nat.sub]](#manual-Nat___sub) : [Nat]](#manual-Nat___zero) → [Nat]](#manual-Nat___zero) → [Nat]](#manual-Nat___zero)
```

Subtraction of natural numbers, truncated at `0`. Usually used via the `-` operator.

If a result would be less than zero, then the result is zero.

This definition is overridden in both the kernel and the compiler to efficiently evaluate using the
arbitrary-precision arithmetic library. The definition provided here is the logical model.

Examples:

- `5 - 3 = 2`
- `8 - 2 = 6`
- `8 - 8 = 0`
- `8 - 20 = 0`

def

```lean
[Nat.mul]](#manual-Nat___mul) : [Nat]](#manual-Nat___zero) → [Nat]](#manual-Nat___zero) → [Nat]](#manual-Nat___zero)



[Nat.mul]](#manual-Nat___mul) : [Nat]](#manual-Nat___zero) → [Nat]](#manual-Nat___zero) → [Nat]](#manual-Nat___zero)
```

Multiplication of natural numbers, usually accessed via the `*` operator.

This function is overridden in both the kernel and the compiler to efficiently evaluate using the
arbitrary-precision arithmetic library. The definition provided here is the logical model.

def

```lean
[Nat.div]](#manual-Nat___div) (x y : [Nat]](#manual-Nat___zero)) : [Nat]](#manual-Nat___zero)



[Nat.div]](#manual-Nat___div) (x y : [Nat]](#manual-Nat___zero)) : [Nat]](#manual-Nat___zero)
```

Division of natural numbers, discarding the remainder. Division by `0` returns `0`. Usually accessed
via the `/` operator.

This operation is sometimes called “floor division.”

This function is overridden at runtime with an efficient implementation. This definition is
the logical model.

Examples:

- `21 / 3 = 7`
- `21 / 5 = 4`
- `0 / 22 = 0`
- `5 / 0 = 0`

def

```lean
[Nat.mod]](#manual-Nat___mod) : [Nat]](#manual-Nat___zero) → [Nat]](#manual-Nat___zero) → [Nat]](#manual-Nat___zero)



[Nat.mod]](#manual-Nat___mod) : [Nat]](#manual-Nat___zero) → [Nat]](#manual-Nat___zero) → [Nat]](#manual-Nat___zero)
```

The modulo operator, which computes the remainder when dividing one natural number by another.
Usually accessed via the `%` operator. When the divisor is `0`, the result is the dividend rather
than an error.

`[Nat.mod]](#manual-Nat___mod)` is a wrapper around `[Nat.modCore]](#manual-Nat___modCore)` that special-cases two situations, giving better
definitional reductions:

- `[Nat.mod]](#manual-Nat___mod) 0 m` should reduce to `0`, for all terms `m : [Nat]](#manual-Nat___zero)`.
- `[Nat.mod]](#manual-Nat___mod) n (m + n + 1)` should reduce to `n` for concrete `[Nat]](#manual-Nat___zero)` literals `n`.

These reductions help `[Fin](https://lean-lang.org/doc/reference/latest/Basic-Types/Finite-Natural-Numbers/#Fin___mk) n` literals work well, because the `[OfNat]](#manual-OfNat___mk)` instance for `[Fin](https://lean-lang.org/doc/reference/latest/Basic-Types/Finite-Natural-Numbers/#Fin___mk)` uses
`[Nat.mod]](#manual-Nat___mod)`. In particular, `(0 : [Fin](https://lean-lang.org/doc/reference/latest/Basic-Types/Finite-Natural-Numbers/#Fin___mk) (n + 1)).[val](https://lean-lang.org/doc/reference/latest/Basic-Types/Finite-Natural-Numbers/#Fin___mk)` should reduce definitionally to `0`. `[Nat.modCore]](#manual-Nat___modCore)`
can handle all numbers, but its definitional reductions are not as convenient.

This function is overridden at runtime with an efficient implementation. This definition is the
logical model.

Examples:

- `7 % 2 = 1`
- `9 % 3 = 0`
- `5 % 7 = 5`
- `5 % 0 = 5`
- `show ∀ (n : [Nat]](#manual-Nat___zero)), 0 % n = 0 from fun _ => [rfl]](#manual-rfl-next)`
- `show ∀ (m : [Nat]](#manual-Nat___zero)), 5 % (m + 6) = 5 from fun _ => [rfl]](#manual-rfl-next)`

def

```lean
[Nat.modCore]](#manual-Nat___modCore) (x y : [Nat]](#manual-Nat___zero)) : [Nat]](#manual-Nat___zero)



[Nat.modCore]](#manual-Nat___modCore) (x y : [Nat]](#manual-Nat___zero)) : [Nat]](#manual-Nat___zero)
```

The modulo operator, which computes the remainder when dividing one natural number by another.
Usually accessed via the `%` operator. When the divisor is `0`, the result is the dividend rather
than an error.

This is the core implementation of `[Nat.mod]](#manual-Nat___mod)`. It computes the correct result for any two closed
natural numbers, but it does not have some convenient [definitional
reductions](https://lean-lang.org/doc/reference/4.34.0-rc1/find/?domain=Verso.Genre.Manual.section&name=type-system) when the `[Nat]](#manual-Nat___zero)`s contain free variables. The wrapper
`[Nat.mod]](#manual-Nat___mod)` handles those cases specially and then calls `[Nat.modCore]](#manual-Nat___modCore)`.

This function is overridden at runtime with an efficient implementation. This definition is the
logical model.

def

```lean
[Nat.pow]](#manual-Nat___pow) (m : [Nat]](#manual-Nat___zero)) : [Nat]](#manual-Nat___zero) → [Nat]](#manual-Nat___zero)



[Nat.pow]](#manual-Nat___pow) (m : [Nat]](#manual-Nat___zero)) : [Nat]](#manual-Nat___zero) → [Nat]](#manual-Nat___zero)
```

The power operation on natural numbers, usually accessed via the `^` operator.

This function is overridden in both the kernel and the compiler to efficiently evaluate using the
arbitrary-precision arithmetic library. The definition provided here is the logical model.

def

```lean
[Nat.log2]](#manual-Nat___log2) (n : [Nat]](#manual-Nat___zero)) : [Nat]](#manual-Nat___zero)



[Nat.log2]](#manual-Nat___log2) (n : [Nat]](#manual-Nat___zero)) : [Nat]](#manual-Nat___zero)
```

Base-two logarithm of natural numbers. Returns `⌊max 0 (log₂ n)⌋`.

This function is overridden at runtime with an efficient implementation. This definition is
the logical model.

Examples:

- `[Nat.log2]](#manual-Nat___log2) 0 = 0`
- `[Nat.log2]](#manual-Nat___log2) 1 = 0`
- `[Nat.log2]](#manual-Nat___log2) 2 = 1`
- `[Nat.log2]](#manual-Nat___log2) 4 = 2`
- `[Nat.log2]](#manual-Nat___log2) 7 = 2`
- `[Nat.log2]](#manual-Nat___log2) 8 = 3`

##### 20.1.4.1.1. Bitwise Operations {#manual-nat-api-bitwise}

def

```lean
[Nat.shiftLeft]](#manual-Nat___shiftLeft) : [Nat]](#manual-Nat___zero) → [Nat]](#manual-Nat___zero) → [Nat]](#manual-Nat___zero)



[Nat.shiftLeft]](#manual-Nat___shiftLeft) : [Nat]](#manual-Nat___zero) → [Nat]](#manual-Nat___zero) → [Nat]](#manual-Nat___zero)
```

Shifts the binary representation of a value left by the specified number of bits. Usually accessed
via the `<<<` operator.

Examples:

- `1 <<< 2 = 4`
- `1 <<< 3 = 8`
- `0 <<< 3 = 0`
- `0xf1 <<< 4 = 0xf10`

def

```lean
[Nat.shiftRight]](#manual-Nat___shiftRight) : [Nat]](#manual-Nat___zero) → [Nat]](#manual-Nat___zero) → [Nat]](#manual-Nat___zero)



[Nat.shiftRight]](#manual-Nat___shiftRight) : [Nat]](#manual-Nat___zero) → [Nat]](#manual-Nat___zero) → [Nat]](#manual-Nat___zero)
```

Shifts the binary representation of a value right by the specified number of bits. Usually accessed
via the `>>>` operator.

Examples:

- `4 >>> 2 = 1`
- `8 >>> 2 = 2`
- `8 >>> 3 = 1`
- `0 >>> 3 = 0`
- `0xf13a >>> 8 = 0xf1`

def

```lean
[Nat.xor]](#manual-Nat___xor) : [Nat]](#manual-Nat___zero) → [Nat]](#manual-Nat___zero) → [Nat]](#manual-Nat___zero)



[Nat.xor]](#manual-Nat___xor) : [Nat]](#manual-Nat___zero) → [Nat]](#manual-Nat___zero) → [Nat]](#manual-Nat___zero)
```

Bitwise exclusive or. Usually accessed via the `^^^` operator.

Each bit of the resulting value is set if the corresponding bit is set in exactly one of the inputs.

def

```lean
[Nat.lor]](#manual-Nat___lor) : [Nat]](#manual-Nat___zero) → [Nat]](#manual-Nat___zero) → [Nat]](#manual-Nat___zero)



[Nat.lor]](#manual-Nat___lor) : [Nat]](#manual-Nat___zero) → [Nat]](#manual-Nat___zero) → [Nat]](#manual-Nat___zero)
```

Bitwise or. Usually accessed via the `|||` operator.

Each bit of the resulting value is set if the corresponding bit is set in at least one of the inputs.

def

```lean
[Nat.land]](#manual-Nat___land) : [Nat]](#manual-Nat___zero) → [Nat]](#manual-Nat___zero) → [Nat]](#manual-Nat___zero)



[Nat.land]](#manual-Nat___land) : [Nat]](#manual-Nat___zero) → [Nat]](#manual-Nat___zero) → [Nat]](#manual-Nat___zero)
```

Bitwise and. Usually accessed via the `&&&` operator.

Each bit of the resulting value is set if the corresponding bit is set in both of the inputs.

def

```lean
[Nat.bitwise]](#manual-Nat___bitwise) (f : [Bool](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false) → [Bool](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false) → [Bool](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)) (n m : [Nat]](#manual-Nat___zero)) : [Nat]](#manual-Nat___zero)



[Nat.bitwise]](#manual-Nat___bitwise) (f : [Bool](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false) → [Bool](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false) → [Bool](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false))
  (n m : [Nat]](#manual-Nat___zero)) : [Nat]](#manual-Nat___zero)
```

A helper for implementing bitwise operators on `[Nat]](#manual-Nat___zero)`.

Each bit of the resulting `[Nat]](#manual-Nat___zero)` is the result of applying `f` to the corresponding bits of the input
`[Nat]](#manual-Nat___zero)`s, up to the position of the highest set bit in either input.

def

```lean
[Nat.testBit]](#manual-Nat___testBit) (m n : [Nat]](#manual-Nat___zero)) : [Bool](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)



[Nat.testBit]](#manual-Nat___testBit) (m n : [Nat]](#manual-Nat___zero)) : [Bool](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)
```

Returns `[true](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)` if the `(n+1)`th least significant bit is `1`, or `[false](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)` if it is `0`.

#### 20.1.4.2. Minimum and Maximum {#manual-nat-api-minmax}

def

```lean
[Nat.min]](#manual-Nat___min) (n m : [Nat]](#manual-Nat___zero)) : [Nat]](#manual-Nat___zero)



[Nat.min]](#manual-Nat___min) (n m : [Nat]](#manual-Nat___zero)) : [Nat]](#manual-Nat___zero)
```

Returns the lesser of two natural numbers. Usually accessed via `[Min.min]](#manual-Min___mk)`.

Returns `n` if `n ≤ m`, or `m` if `m ≤ n`.

Examples:

- `[min]](#manual-Min___mk) 0 5 = 0`
- `[min]](#manual-Min___mk) 4 5 = 4`
- `[min]](#manual-Min___mk) 4 3 = 3`
- `[min]](#manual-Min___mk) 8 8 = 8`

def

```lean
[Nat.max]](#manual-Nat___max) (n m : [Nat]](#manual-Nat___zero)) : [Nat]](#manual-Nat___zero)



[Nat.max]](#manual-Nat___max) (n m : [Nat]](#manual-Nat___zero)) : [Nat]](#manual-Nat___zero)
```

Returns the greater of two natural numbers. Usually accessed via `[Max.max]](#manual-Max___mk)`.

Returns `m` if `n ≤ m`, or `n` if `m ≤ n`.

Examples:

- `[max]](#manual-Max___mk) 0 5 = 5`
- `[max]](#manual-Max___mk) 4 5 = 5`
- `[max]](#manual-Max___mk) 4 3 = 4`
- `[max]](#manual-Max___mk) 8 8 = 8`

#### 20.1.4.3. GCD and LCM {#manual-nat-api-gcd-lcm}

def

```lean
[Nat.gcd]](#manual-Nat___gcd) (m n : [Nat]](#manual-Nat___zero)) : [Nat]](#manual-Nat___zero)



[Nat.gcd]](#manual-Nat___gcd) (m n : [Nat]](#manual-Nat___zero)) : [Nat]](#manual-Nat___zero)
```

Computes the greatest common divisor of two natural numbers. The GCD of two natural numbers is the
largest natural number that evenly divides both.

In particular, the GCD of a number and `0` is the number itself.

This reference implementation via the Euclidean algorithm is overridden in both the kernel and the
compiler to efficiently evaluate using arbitrary-precision arithmetic. The definition provided here
is the logical model.

Examples:

- `[Nat.gcd]](#manual-Nat___gcd) 10 15 = 5`
- `[Nat.gcd]](#manual-Nat___gcd) 0 5 = 5`
- `[Nat.gcd]](#manual-Nat___gcd) 7 0 = 7`

def

```lean
[Nat.lcm]](#manual-Nat___lcm) (m n : [Nat]](#manual-Nat___zero)) : [Nat]](#manual-Nat___zero)



[Nat.lcm]](#manual-Nat___lcm) (m n : [Nat]](#manual-Nat___zero)) : [Nat]](#manual-Nat___zero)
```

The least common multiple of `m` and `n` is the smallest natural number that's evenly divisible by
both `m` and `n`. Returns `0` if either `m` or `n` is `0`.

Examples:

- `[Nat.lcm]](#manual-Nat___lcm) 9 6 = 18`
- `[Nat.lcm]](#manual-Nat___lcm) 9 3 = 9`
- `[Nat.lcm]](#manual-Nat___lcm) 0 3 = 0`
- `[Nat.lcm]](#manual-Nat___lcm) 3 0 = 0`

#### 20.1.4.4. Powers of Two {#manual-nat-api-pow2}

def

```lean
[Nat.isPowerOfTwo]](#manual-Nat___isPowerOfTwo) (n : [Nat]](#manual-Nat___zero)) : Prop



[Nat.isPowerOfTwo]](#manual-Nat___isPowerOfTwo) (n : [Nat]](#manual-Nat___zero)) : Prop
```

A natural number `n` is a power of two if there exists some `k : [Nat]](#manual-Nat___zero)` such that `n = 2 ^ k`.

def

```lean
[Nat.nextPowerOfTwo]](#manual-Nat___nextPowerOfTwo) (n : [Nat]](#manual-Nat___zero)) : [Nat]](#manual-Nat___zero)



[Nat.nextPowerOfTwo]](#manual-Nat___nextPowerOfTwo) (n : [Nat]](#manual-Nat___zero)) : [Nat]](#manual-Nat___zero)
```

Returns the least power of two that's greater than or equal to `n`.

Examples:

- `[Nat.nextPowerOfTwo]](#manual-Nat___nextPowerOfTwo) 0 = 1`
- `[Nat.nextPowerOfTwo]](#manual-Nat___nextPowerOfTwo) 1 = 1`
- `[Nat.nextPowerOfTwo]](#manual-Nat___nextPowerOfTwo) 2 = 2`
- `[Nat.nextPowerOfTwo]](#manual-Nat___nextPowerOfTwo) 3 = 4`
- `[Nat.nextPowerOfTwo]](#manual-Nat___nextPowerOfTwo) 5 = 8`

#### 20.1.4.5. Comparisons {#manual-nat-api-comparison}

##### 20.1.4.5.1. Boolean Comparisons {#manual-nat-api-comparison-bool}

def

```lean
[Nat.beq]](#manual-Nat___beq) : [Nat]](#manual-Nat___zero) → [Nat]](#manual-Nat___zero) → [Bool](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)



[Nat.beq]](#manual-Nat___beq) : [Nat]](#manual-Nat___zero) → [Nat]](#manual-Nat___zero) → [Bool](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)
```

Boolean equality of natural numbers, usually accessed via the `==` operator.

This function is overridden in both the kernel and the compiler to efficiently evaluate using the
arbitrary-precision arithmetic library. The definition provided here is the logical model.

def

```lean
[Nat.ble]](#manual-Nat___ble) : [Nat]](#manual-Nat___zero) → [Nat]](#manual-Nat___zero) → [Bool](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)



[Nat.ble]](#manual-Nat___ble) : [Nat]](#manual-Nat___zero) → [Nat]](#manual-Nat___zero) → [Bool](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)
```

The Boolean less-than-or-equal-to comparison on natural numbers.

This function is overridden in both the kernel and the compiler to efficiently evaluate using the
arbitrary-precision arithmetic library. The definition provided here is the logical model.

Examples:

- `[Nat.ble]](#manual-Nat___ble) 2 5 = [true](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)`
- `[Nat.ble]](#manual-Nat___ble) 5 2 = [false](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)`
- `[Nat.ble]](#manual-Nat___ble) 5 5 = [true](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)`

def

```lean
[Nat.blt]](#manual-Nat___blt) (a b : [Nat]](#manual-Nat___zero)) : [Bool](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)



[Nat.blt]](#manual-Nat___blt) (a b : [Nat]](#manual-Nat___zero)) : [Bool](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)
```

The Boolean less-than comparison on natural numbers.

This function is overridden in both the kernel and the compiler to efficiently evaluate using the
arbitrary-precision arithmetic library. The definition provided here is the logical model.

Examples:

- `[Nat.blt]](#manual-Nat___blt) 2 5 = [true](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)`
- `[Nat.blt]](#manual-Nat___blt) 5 2 = [false](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)`
- `[Nat.blt]](#manual-Nat___blt) 5 5 = [false](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)`

##### 20.1.4.5.2. Decidable Equality {#manual-nat-api-deceq}

def

```lean
[Nat.decEq]](#manual-Nat___decEq) (n m : [Nat]](#manual-Nat___zero)) : [Decidable]](#manual-Decidable___isFalse) [(]](#manual-Eq___refl)n [=]](#manual-Eq___refl) m[)]](#manual-Eq___refl)



[Nat.decEq]](#manual-Nat___decEq) (n m : [Nat]](#manual-Nat___zero)) : [Decidable]](#manual-Decidable___isFalse) [(]](#manual-Eq___refl)n [=]](#manual-Eq___refl) m[)]](#manual-Eq___refl)
```

A decision procedure for equality of natural numbers, usually accessed via the `[DecidableEq]](#manual-DecidableEq) [Nat]](#manual-Nat___zero)`
instance.

This function is overridden in both the kernel and the compiler to efficiently evaluate using the
arbitrary-precision arithmetic library. The definition provided here is the logical model.

Examples:

- `[Nat.decEq]](#manual-Nat___decEq) 5 5 = [isTrue]](#manual-Decidable___isFalse) [rfl]](#manual-rfl-next)`
- `([if]](#manual-termIfThenElse) 3 = 4 [then]](#manual-termIfThenElse) "yes" [else]](#manual-termIfThenElse) "no") = "no"`
- `show 12 = 12 by [decide]](#manual-decide)`

def

```lean
[Nat.decLe]](#manual-Nat___decLe) (n m : [Nat]](#manual-Nat___zero)) : [Decidable]](#manual-Decidable___isFalse) [(]](#manual-LE___mk)n [≤]](#manual-LE___mk) m[)]](#manual-LE___mk)



[Nat.decLe]](#manual-Nat___decLe) (n m : [Nat]](#manual-Nat___zero)) : [Decidable]](#manual-Decidable___isFalse) [(]](#manual-LE___mk)n [≤]](#manual-LE___mk) m[)]](#manual-LE___mk)
```

A decision procedure for non-strict inequality of natural numbers, usually accessed via the
`[DecidableLE]](#manual-DecidableLE) [Nat]](#manual-Nat___zero)` instance.

Examples:

- `([if]](#manual-termIfThenElse) 3 ≤ 4 [then]](#manual-termIfThenElse) "yes" [else]](#manual-termIfThenElse) "no") = "yes"`
- `([if]](#manual-termIfThenElse) 6 ≤ 4 [then]](#manual-termIfThenElse) "yes" [else]](#manual-termIfThenElse) "no") = "no"`
- `show 12 ≤ 12 by [decide]](#manual-decide)`
- `show 5 ≤ 12 by [decide]](#manual-decide)`

def

```lean
[Nat.decLt]](#manual-Nat___decLt) (n m : [Nat]](#manual-Nat___zero)) : [Decidable]](#manual-Decidable___isFalse) [(]](#manual-LT___mk)n [<]](#manual-LT___mk) m[)]](#manual-LT___mk)



[Nat.decLt]](#manual-Nat___decLt) (n m : [Nat]](#manual-Nat___zero)) : [Decidable]](#manual-Decidable___isFalse) [(]](#manual-LT___mk)n [<]](#manual-LT___mk) m[)]](#manual-LT___mk)
```

A decision procedure for strict inequality of natural numbers, usually accessed via the
`[DecidableLT]](#manual-DecidableLT) [Nat]](#manual-Nat___zero)` instance.

Examples:

- `([if]](#manual-termIfThenElse) 3 < 4 [then]](#manual-termIfThenElse) "yes" [else]](#manual-termIfThenElse) "no") = "yes"`
- `([if]](#manual-termIfThenElse) 4 < 4 [then]](#manual-termIfThenElse) "yes" [else]](#manual-termIfThenElse) "no") = "no"`
- `([if]](#manual-termIfThenElse) 6 < 4 [then]](#manual-termIfThenElse) "yes" [else]](#manual-termIfThenElse) "no") = "no"`
- `show 5 < 12 by [decide]](#manual-decide)`

##### 20.1.4.5.3. Predicates {#manual-nat-api-predicates}

inductive predicate

```lean
[Nat.le]](#manual-Nat___le___refl) (n : [Nat]](#manual-Nat___zero)) : [Nat]](#manual-Nat___zero) → Prop



[Nat.le]](#manual-Nat___le___refl) (n : [Nat]](#manual-Nat___zero)) : [Nat]](#manual-Nat___zero) → Prop
```

Non-strict, or weak, inequality of natural numbers, usually accessed via the `≤` operator.

Constructors

```lean
[Nat.le.refl]](#manual-Nat___le___refl) {n : [Nat]](#manual-Nat___zero)} : n.[le]](#manual-Nat___le___refl) n
```

Non-strict inequality is reflexive: `n ≤ n`

```lean
[Nat.le.step]](#manual-Nat___le___refl) {n m : [Nat]](#manual-Nat___zero)} : n.[le]](#manual-Nat___le___refl) m → n.[le]](#manual-Nat___le___refl) m.[succ]](#manual-Nat___zero)
```

If `n ≤ m`, then `n ≤ m + 1`.

def

```lean
[Nat.lt]](#manual-Nat___lt) (n m : [Nat]](#manual-Nat___zero)) : Prop



[Nat.lt]](#manual-Nat___lt) (n m : [Nat]](#manual-Nat___zero)) : Prop
```

Strict inequality of natural numbers, usually accessed via the `<` operator.

It is defined as `n < m = n + 1 ≤ m`.

#### 20.1.4.6. Iteration {#manual-nat-api-iteration}

Many iteration operators come in two versions: a structurally recursive version and a tail-recursive version.
The structurally recursive version is typically easier to use in contexts where definitional equality is important, as it will compute when only some prefix of a natural number is known.

def

```lean
[Nat.repeat.{u}]](#manual-Nat___repeat) {α : Type u} (f : α → α) (n : [Nat]](#manual-Nat___zero)) (a : α) : α



[Nat.repeat.{u}]](#manual-Nat___repeat) {α : Type u} (f : α → α)
  (n : [Nat]](#manual-Nat___zero)) (a : α) : α
```

Applies a function to a starting value the specified number of times.

In other words, `f` is iterated `n` times on `a`.

Examples:

- `Nat.repeat f 3 a = f <| f <| f <| a`
- `[Nat.repeat]](#manual-Nat___repeat) (· ++ "!") 4 "Hello" = "Hello!!!!"`

def

```lean
[Nat.repeatTR.{u}]](#manual-Nat___repeatTR) {α : Type u} (f : α → α) (n : [Nat]](#manual-Nat___zero)) (a : α) : α



[Nat.repeatTR.{u}]](#manual-Nat___repeatTR) {α : Type u} (f : α → α)
  (n : [Nat]](#manual-Nat___zero)) (a : α) : α
```

Applies a function to a starting value the specified number of times.

In other words, `f` is iterated `n` times on `a`.

This is a tail-recursive version of `[Nat.repeat]](#manual-Nat___repeat)` that's used at runtime.

Examples:

- `Nat.repeatTR f 3 a = f <| f <| f <| a`
- `[Nat.repeatTR]](#manual-Nat___repeatTR) (· ++ "!") 4 "Hello" = "Hello!!!!"`

def

```lean
[Nat.fold.{u}]](#manual-Nat___fold) {α : Type u} (n : [Nat]](#manual-Nat___zero)) (f : (i : [Nat]](#manual-Nat___zero)) → i [<]](#manual-LT___mk) n → α → α)
  (init : α) : α



[Nat.fold.{u}]](#manual-Nat___fold) {α : Type u} (n : [Nat]](#manual-Nat___zero))
  (f : (i : [Nat]](#manual-Nat___zero)) → i [<]](#manual-LT___mk) n → α → α)
  (init : α) : α
```

Iterates the application of a function `f` to a starting value `init`, `n` times. At each step, `f`
is applied to the current value and to the next natural number less than `n`, in increasing order.

Examples:

- `[Nat.fold]](#manual-Nat___fold) 3 f init = (init |> f 0 (by [simp]](#manual-simp)) |> f 1 (by [simp]](#manual-simp)) |> f 2 (by [simp]](#manual-simp)))`
- `[Nat.fold]](#manual-Nat___fold) 4 (fun i _ xs => xs.[push](https://lean-lang.org/doc/reference/latest/Basic-Types/Arrays/#Array___push) i) #[] = #[0, 1, 2, 3]`
- `[Nat.fold]](#manual-Nat___fold) 0 (fun i _ xs => xs.[push](https://lean-lang.org/doc/reference/latest/Basic-Types/Arrays/#Array___push) i) #[] = #[]`

def

```lean
[Nat.foldTR.{u}]](#manual-Nat___foldTR) {α : Type u} (n : [Nat]](#manual-Nat___zero)) (f : (i : [Nat]](#manual-Nat___zero)) → i [<]](#manual-LT___mk) n → α → α)
  (init : α) : α



[Nat.foldTR.{u}]](#manual-Nat___foldTR) {α : Type u} (n : [Nat]](#manual-Nat___zero))
  (f : (i : [Nat]](#manual-Nat___zero)) → i [<]](#manual-LT___mk) n → α → α)
  (init : α) : α
```

Iterates the application of a function `f` to a starting value `init`, `n` times. At each step, `f`
is applied to the current value and to the next natural number less than `n`, in increasing order.

This is a tail-recursive version of `[Nat.fold]](#manual-Nat___fold)` that's used at runtime.

Examples:

- `[Nat.foldTR]](#manual-Nat___foldTR) 3 f init = (init |> f 0 (by [simp]](#manual-simp)) |> f 1 (by [simp]](#manual-simp)) |> f 2 (by [simp]](#manual-simp)))`
- `[Nat.foldTR]](#manual-Nat___foldTR) 4 (fun i _ xs => xs.[push](https://lean-lang.org/doc/reference/latest/Basic-Types/Arrays/#Array___push) i) #[] = #[0, 1, 2, 3]`
- `[Nat.foldTR]](#manual-Nat___foldTR) 0 (fun i _ xs => xs.[push](https://lean-lang.org/doc/reference/latest/Basic-Types/Arrays/#Array___push) i) #[] = #[]`

def

```lean
[Nat.foldM.{u, v}]](#manual-Nat___foldM) {α : Type u} {m : Type u → Type v} [[Monad]](#manual-Monad___mk) m] (n : [Nat]](#manual-Nat___zero))
  (f : (i : [Nat]](#manual-Nat___zero)) → i [<]](#manual-LT___mk) n → α → m α) (init : α) : m α



[Nat.foldM.{u, v}]](#manual-Nat___foldM) {α : Type u}
  {m : Type u → Type v} [[Monad]](#manual-Monad___mk) m]
  (n : [Nat]](#manual-Nat___zero))
  (f : (i : [Nat]](#manual-Nat___zero)) → i [<]](#manual-LT___mk) n → α → m α)
  (init : α) : m α
```

Iterates the application of a monadic function `f` to a starting value `init`, `n` times. At each
step, `f` is applied to the current value and to the next natural number less than `n`, in
increasing order.

def

```lean
[Nat.foldRev.{u}]](#manual-Nat___foldRev) {α : Type u} (n : [Nat]](#manual-Nat___zero)) (f : (i : [Nat]](#manual-Nat___zero)) → i [<]](#manual-LT___mk) n → α → α)
  (init : α) : α



[Nat.foldRev.{u}]](#manual-Nat___foldRev) {α : Type u} (n : [Nat]](#manual-Nat___zero))
  (f : (i : [Nat]](#manual-Nat___zero)) → i [<]](#manual-LT___mk) n → α → α)
  (init : α) : α
```

Iterates the application of a function `f` to a starting value `init`, `n` times. At each step, `f`
is applied to the current value and to the next natural number less than `n`, in decreasing order.

Examples:

- `[Nat.foldRev]](#manual-Nat___foldRev) 3 f init = (f 0 (by [simp]](#manual-simp)) <| f 1 (by [simp]](#manual-simp)) <| f 2 (by [simp]](#manual-simp)) init)`
- `[Nat.foldRev]](#manual-Nat___foldRev) 4 (fun i _ xs => xs.[push](https://lean-lang.org/doc/reference/latest/Basic-Types/Arrays/#Array___push) i) #[] = #[3, 2, 1, 0]`
- `[Nat.foldRev]](#manual-Nat___foldRev) 0 (fun i _ xs => xs.[push](https://lean-lang.org/doc/reference/latest/Basic-Types/Arrays/#Array___push) i) #[] = #[]`

def

```lean
[Nat.foldRevM.{u, v}]](#manual-Nat___foldRevM) {α : Type u} {m : Type u → Type v} [[Monad]](#manual-Monad___mk) m]
  (n : [Nat]](#manual-Nat___zero)) (f : (i : [Nat]](#manual-Nat___zero)) → i [<]](#manual-LT___mk) n → α → m α) (init : α) : m α



[Nat.foldRevM.{u, v}]](#manual-Nat___foldRevM) {α : Type u}
  {m : Type u → Type v} [[Monad]](#manual-Monad___mk) m]
  (n : [Nat]](#manual-Nat___zero))
  (f : (i : [Nat]](#manual-Nat___zero)) → i [<]](#manual-LT___mk) n → α → m α)
  (init : α) : m α
```

Iterates the application of a monadic function `f` to a starting value `init`, `n` times. At each
step, `f` is applied to the current value and to the next natural number less than `n`, in
decreasing order.

def

```lean
[Nat.forM.{u_1}]](#manual-Nat___forM) {m : Type → Type u_1} [[Monad]](#manual-Monad___mk) m] (n : [Nat]](#manual-Nat___zero))
  (f : (i : [Nat]](#manual-Nat___zero)) → i [<]](#manual-LT___mk) n → m [Unit](https://lean-lang.org/doc/reference/latest/Basic-Types/The-Unit-Type/#Unit)) : m [Unit](https://lean-lang.org/doc/reference/latest/Basic-Types/The-Unit-Type/#Unit)



[Nat.forM.{u_1}]](#manual-Nat___forM) {m : Type → Type u_1}
  [[Monad]](#manual-Monad___mk) m] (n : [Nat]](#manual-Nat___zero))
  (f : (i : [Nat]](#manual-Nat___zero)) → i [<]](#manual-LT___mk) n → m [Unit](https://lean-lang.org/doc/reference/latest/Basic-Types/The-Unit-Type/#Unit)) :
  m [Unit](https://lean-lang.org/doc/reference/latest/Basic-Types/The-Unit-Type/#Unit)
```

Executes a monadic action on all the numbers less than some bound, in increasing order.

Example:

```lean
[#eval]](#manual-Lean___Parser___Command___eval) [Nat.forM]](#manual-Nat___forM) 5 fun i _ => [IO.println](https://lean-lang.org/doc/reference/latest/IO/Console-Output/#IO___println) i
```

```lean
0
1
2
3
4
```

def

```lean
[Nat.forRevM.{u_1}]](#manual-Nat___forRevM) {m : Type → Type u_1} [[Monad]](#manual-Monad___mk) m] (n : [Nat]](#manual-Nat___zero))
  (f : (i : [Nat]](#manual-Nat___zero)) → i [<]](#manual-LT___mk) n → m [Unit](https://lean-lang.org/doc/reference/latest/Basic-Types/The-Unit-Type/#Unit)) : m [Unit](https://lean-lang.org/doc/reference/latest/Basic-Types/The-Unit-Type/#Unit)



[Nat.forRevM.{u_1}]](#manual-Nat___forRevM) {m : Type → Type u_1}
  [[Monad]](#manual-Monad___mk) m] (n : [Nat]](#manual-Nat___zero))
  (f : (i : [Nat]](#manual-Nat___zero)) → i [<]](#manual-LT___mk) n → m [Unit](https://lean-lang.org/doc/reference/latest/Basic-Types/The-Unit-Type/#Unit)) :
  m [Unit](https://lean-lang.org/doc/reference/latest/Basic-Types/The-Unit-Type/#Unit)
```

Executes a monadic action on all the numbers less than some bound, in decreasing order.

Example:

```lean
[#eval]](#manual-Lean___Parser___Command___eval) [Nat.forRevM]](#manual-Nat___forRevM) 5 fun i _ => [IO.println](https://lean-lang.org/doc/reference/latest/IO/Console-Output/#IO___println) i
```

```lean
4
3
2
1
0
```

def

```lean
[Nat.all]](#manual-Nat___all) (n : [Nat]](#manual-Nat___zero)) (f : (i : [Nat]](#manual-Nat___zero)) → i [<]](#manual-LT___mk) n → [Bool](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)) : [Bool](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)



[Nat.all]](#manual-Nat___all) (n : [Nat]](#manual-Nat___zero))
  (f : (i : [Nat]](#manual-Nat___zero)) → i [<]](#manual-LT___mk) n → [Bool](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)) : [Bool](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)
```

Checks whether `f` returns `[true](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)` for every number strictly less than a bound.

Examples:

- `[Nat.all]](#manual-Nat___all) 4 (fun i _ => i < 5) = [true](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)`
- `[Nat.all]](#manual-Nat___all) 7 (fun i _ => i < 5) = [false](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)`
- `[Nat.all]](#manual-Nat___all) 7 (fun i _ => i % 2 = 0) = [false](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)`
- `[Nat.all]](#manual-Nat___all) 1 (fun i _ => i % 2 = 0) = [true](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)`

def

```lean
[Nat.allTR]](#manual-Nat___allTR) (n : [Nat]](#manual-Nat___zero)) (f : (i : [Nat]](#manual-Nat___zero)) → i [<]](#manual-LT___mk) n → [Bool](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)) : [Bool](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)



[Nat.allTR]](#manual-Nat___allTR) (n : [Nat]](#manual-Nat___zero))
  (f : (i : [Nat]](#manual-Nat___zero)) → i [<]](#manual-LT___mk) n → [Bool](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)) : [Bool](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)
```

Checks whether `f` returns `[true](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)` for every number strictly less than a bound.

This is a tail-recursive equivalent of `[Nat.all]](#manual-Nat___all)` that's used at runtime.

Examples:

- `[Nat.allTR]](#manual-Nat___allTR) 4 (fun i _ => i < 5) = [true](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)`
- `[Nat.allTR]](#manual-Nat___allTR) 7 (fun i _ => i < 5) = [false](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)`
- `[Nat.allTR]](#manual-Nat___allTR) 7 (fun i _ => i % 2 = 0) = [false](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)`
- `[Nat.allTR]](#manual-Nat___allTR) 1 (fun i _ => i % 2 = 0) = [true](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)`

def

```lean
[Nat.any]](#manual-Nat___any) (n : [Nat]](#manual-Nat___zero)) (f : (i : [Nat]](#manual-Nat___zero)) → i [<]](#manual-LT___mk) n → [Bool](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)) : [Bool](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)



[Nat.any]](#manual-Nat___any) (n : [Nat]](#manual-Nat___zero))
  (f : (i : [Nat]](#manual-Nat___zero)) → i [<]](#manual-LT___mk) n → [Bool](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)) : [Bool](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)
```

Checks whether there is some number less that the given bound for which `f` returns `[true](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)`.

Examples:

- `[Nat.any]](#manual-Nat___any) 4 (fun i _ => i < 5) = [true](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)`
- `[Nat.any]](#manual-Nat___any) 7 (fun i _ => i < 5) = [true](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)`
- `[Nat.any]](#manual-Nat___any) 7 (fun i _ => i % 2 = 0) = [true](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)`
- `[Nat.any]](#manual-Nat___any) 1 (fun i _ => i % 2 = 1) = [false](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)`

def

```lean
[Nat.anyTR]](#manual-Nat___anyTR) (n : [Nat]](#manual-Nat___zero)) (f : (i : [Nat]](#manual-Nat___zero)) → i [<]](#manual-LT___mk) n → [Bool](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)) : [Bool](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)



[Nat.anyTR]](#manual-Nat___anyTR) (n : [Nat]](#manual-Nat___zero))
  (f : (i : [Nat]](#manual-Nat___zero)) → i [<]](#manual-LT___mk) n → [Bool](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)) : [Bool](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)
```

Checks whether there is some number less that the given bound for which `f` returns `[true](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)`.

This is a tail-recursive equivalent of `[Nat.any]](#manual-Nat___any)` that's used at runtime.

Examples:

- `[Nat.anyTR]](#manual-Nat___anyTR) 4 (fun i _ => i < 5) = [true](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)`
- `[Nat.anyTR]](#manual-Nat___anyTR) 7 (fun i _ => i < 5) = [true](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)`
- `[Nat.anyTR]](#manual-Nat___anyTR) 7 (fun i _ => i % 2 = 0) = [true](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)`
- `[Nat.anyTR]](#manual-Nat___anyTR) 1 (fun i _ => i % 2 = 1) = [false](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)`

def

```lean
[Nat.allM.{u_1}]](#manual-Nat___allM) {m : Type → Type u_1} [[Monad]](#manual-Monad___mk) m] (n : [Nat]](#manual-Nat___zero))
  (p : (i : [Nat]](#manual-Nat___zero)) → i [<]](#manual-LT___mk) n → m [Bool](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)) : m [Bool](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)



[Nat.allM.{u_1}]](#manual-Nat___allM) {m : Type → Type u_1}
  [[Monad]](#manual-Monad___mk) m] (n : [Nat]](#manual-Nat___zero))
  (p : (i : [Nat]](#manual-Nat___zero)) → i [<]](#manual-LT___mk) n → m [Bool](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)) :
  m [Bool](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)
```

Checks whether the monadic predicate `p` returns `[true](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)` for all numbers less that the given bound.
Numbers are checked in increasing order until `p` returns false, after which no further are checked.

def

```lean
[Nat.anyM.{u_1}]](#manual-Nat___anyM) {m : Type → Type u_1} [[Monad]](#manual-Monad___mk) m] (n : [Nat]](#manual-Nat___zero))
  (p : (i : [Nat]](#manual-Nat___zero)) → i [<]](#manual-LT___mk) n → m [Bool](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)) : m [Bool](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)



[Nat.anyM.{u_1}]](#manual-Nat___anyM) {m : Type → Type u_1}
  [[Monad]](#manual-Monad___mk) m] (n : [Nat]](#manual-Nat___zero))
  (p : (i : [Nat]](#manual-Nat___zero)) → i [<]](#manual-LT___mk) n → m [Bool](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)) :
  m [Bool](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)
```

Checks whether there is some number less that the given bound for which the monadic predicate `p`
returns `[true](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)`. Numbers are checked in increasing order until `p` returns true, after which
no further are checked.

#### 20.1.4.7. Conversion {#manual-nat-api-conversion}

def

```lean
[Nat.toUInt8]](#manual-Nat___toUInt8) (n : [Nat]](#manual-Nat___zero)) : [UInt8](https://lean-lang.org/doc/reference/latest/Basic-Types/Fixed-Precision-Integers/#UInt8___ofBitVec)



[Nat.toUInt8]](#manual-Nat___toUInt8) (n : [Nat]](#manual-Nat___zero)) : [UInt8](https://lean-lang.org/doc/reference/latest/Basic-Types/Fixed-Precision-Integers/#UInt8___ofBitVec)
```

Converts a natural number to an 8-bit unsigned integer, wrapping on overflow.

This function is overridden at runtime with an efficient implementation.

Examples:

- `[Nat.toUInt8]](#manual-Nat___toUInt8) 5 = 5`
- `[Nat.toUInt8]](#manual-Nat___toUInt8) 255 = 255`
- `[Nat.toUInt8]](#manual-Nat___toUInt8) 256 = 0`
- `[Nat.toUInt8]](#manual-Nat___toUInt8) 259 = 3`
- `[Nat.toUInt8]](#manual-Nat___toUInt8) 32770 = 2`

def

```lean
[Nat.toUInt16]](#manual-Nat___toUInt16) (n : [Nat]](#manual-Nat___zero)) : [UInt16](https://lean-lang.org/doc/reference/latest/Basic-Types/Fixed-Precision-Integers/#UInt16___ofBitVec)



[Nat.toUInt16]](#manual-Nat___toUInt16) (n : [Nat]](#manual-Nat___zero)) : [UInt16](https://lean-lang.org/doc/reference/latest/Basic-Types/Fixed-Precision-Integers/#UInt16___ofBitVec)
```

Converts a natural number to a 16-bit unsigned integer, wrapping on overflow.

This function is overridden at runtime with an efficient implementation.

Examples:

- `[Nat.toUInt16]](#manual-Nat___toUInt16) 5 = 5`
- `[Nat.toUInt16]](#manual-Nat___toUInt16) 255 = 255`
- `[Nat.toUInt16]](#manual-Nat___toUInt16) 32770 = 32770`
- `[Nat.toUInt16]](#manual-Nat___toUInt16) 65537 = 1`

def

```lean
[Nat.toUInt32]](#manual-Nat___toUInt32) (n : [Nat]](#manual-Nat___zero)) : [UInt32](https://lean-lang.org/doc/reference/latest/Basic-Types/Fixed-Precision-Integers/#UInt32___ofBitVec)



[Nat.toUInt32]](#manual-Nat___toUInt32) (n : [Nat]](#manual-Nat___zero)) : [UInt32](https://lean-lang.org/doc/reference/latest/Basic-Types/Fixed-Precision-Integers/#UInt32___ofBitVec)
```

Converts a natural number to a 32-bit unsigned integer, wrapping on overflow.

This function is overridden at runtime with an efficient implementation.

Examples:

- `[Nat.toUInt32]](#manual-Nat___toUInt32) 5 = 5`
- `[Nat.toUInt32]](#manual-Nat___toUInt32) 65_539 = 65_539`
- `[Nat.toUInt32]](#manual-Nat___toUInt32) 4_294_967_299 = 3`

def

```lean
[Nat.toUInt64]](#manual-Nat___toUInt64) (n : [Nat]](#manual-Nat___zero)) : [UInt64](https://lean-lang.org/doc/reference/latest/Basic-Types/Fixed-Precision-Integers/#UInt64___ofBitVec)



[Nat.toUInt64]](#manual-Nat___toUInt64) (n : [Nat]](#manual-Nat___zero)) : [UInt64](https://lean-lang.org/doc/reference/latest/Basic-Types/Fixed-Precision-Integers/#UInt64___ofBitVec)
```

Converts a natural number to a 64-bit unsigned integer, wrapping on overflow.

This function is overridden at runtime with an efficient implementation.

Examples:

- `[Nat.toUInt64]](#manual-Nat___toUInt64) 5 = 5`
- `[Nat.toUInt64]](#manual-Nat___toUInt64) 65539 = 65539`
- `[Nat.toUInt64]](#manual-Nat___toUInt64) 4_294_967_299 = 4_294_967_299`
- `[Nat.toUInt64]](#manual-Nat___toUInt64) 18_446_744_073_709_551_620 = 4`

def

```lean
[Nat.toUSize]](#manual-Nat___toUSize) (n : [Nat]](#manual-Nat___zero)) : [USize](https://lean-lang.org/doc/reference/latest/Basic-Types/Fixed-Precision-Integers/#USize___ofBitVec)



[Nat.toUSize]](#manual-Nat___toUSize) (n : [Nat]](#manual-Nat___zero)) : [USize](https://lean-lang.org/doc/reference/latest/Basic-Types/Fixed-Precision-Integers/#USize___ofBitVec)
```

Converts an arbitrary-precision natural number to an unsigned word-sized integer, wrapping around on
overflow.

This function is overridden at runtime with an efficient implementation.

def

```lean
[Nat.toInt8]](#manual-Nat___toInt8) (n : [Nat]](#manual-Nat___zero)) : [Int8](https://lean-lang.org/doc/reference/latest/Basic-Types/Fixed-Precision-Integers/#Int8___ofUInt8)



[Nat.toInt8]](#manual-Nat___toInt8) (n : [Nat]](#manual-Nat___zero)) : [Int8](https://lean-lang.org/doc/reference/latest/Basic-Types/Fixed-Precision-Integers/#Int8___ofUInt8)
```

Converts a natural number to an 8-bit signed integer, wrapping around to negative numbers on
overflow.

Examples:

- `[Nat.toInt8]](#manual-Nat___toInt8) 53 = 53`
- `[Nat.toInt8]](#manual-Nat___toInt8) 127 = 127`
- `[Nat.toInt8]](#manual-Nat___toInt8) 128 = -128`
- `[Nat.toInt8]](#manual-Nat___toInt8) 255 = -1`

def

```lean
[Nat.toInt16]](#manual-Nat___toInt16) (n : [Nat]](#manual-Nat___zero)) : [Int16](https://lean-lang.org/doc/reference/latest/Basic-Types/Fixed-Precision-Integers/#Int16___ofUInt16)



[Nat.toInt16]](#manual-Nat___toInt16) (n : [Nat]](#manual-Nat___zero)) : [Int16](https://lean-lang.org/doc/reference/latest/Basic-Types/Fixed-Precision-Integers/#Int16___ofUInt16)
```

Converts a natural number to a 16-bit signed integer, wrapping around to negative numbers on
overflow.

Examples:

- `[Nat.toInt16]](#manual-Nat___toInt16) 127 = 127`
- `[Nat.toInt16]](#manual-Nat___toInt16) 32767 = 32767`
- `[Nat.toInt16]](#manual-Nat___toInt16) 32768 = -32768`
- `[Nat.toInt16]](#manual-Nat___toInt16) 32770 = -32766`

def

```lean
[Nat.toInt32]](#manual-Nat___toInt32) (n : [Nat]](#manual-Nat___zero)) : [Int32](https://lean-lang.org/doc/reference/latest/Basic-Types/Fixed-Precision-Integers/#Int32___ofUInt32)



[Nat.toInt32]](#manual-Nat___toInt32) (n : [Nat]](#manual-Nat___zero)) : [Int32](https://lean-lang.org/doc/reference/latest/Basic-Types/Fixed-Precision-Integers/#Int32___ofUInt32)
```

Converts a natural number to a 32-bit signed integer, wrapping around to negative numbers on
overflow.

Examples:

- `[Nat.toInt32]](#manual-Nat___toInt32) 127 = 127`
- `[Nat.toInt32]](#manual-Nat___toInt32) 32770 = 32770`
- `[Nat.toInt32]](#manual-Nat___toInt32) 2_147_483_647 = 2_147_483_647`
- `[Nat.toInt32]](#manual-Nat___toInt32) 2_147_483_648 = -2_147_483_648`

def

```lean
[Nat.toInt64]](#manual-Nat___toInt64) (n : [Nat]](#manual-Nat___zero)) : [Int64](https://lean-lang.org/doc/reference/latest/Basic-Types/Fixed-Precision-Integers/#Int64___ofUInt64)



[Nat.toInt64]](#manual-Nat___toInt64) (n : [Nat]](#manual-Nat___zero)) : [Int64](https://lean-lang.org/doc/reference/latest/Basic-Types/Fixed-Precision-Integers/#Int64___ofUInt64)
```

Converts a natural number to a 64-bit signed integer, wrapping around to negative numbers on
overflow.

Examples:

- `[Nat.toInt64]](#manual-Nat___toInt64) 127 = 127`
- `[Nat.toInt64]](#manual-Nat___toInt64) 2_147_483_648 = 2_147_483_648`
- `[Nat.toInt64]](#manual-Nat___toInt64) 9_223_372_036_854_775_807 = 9_223_372_036_854_775_807`
- `[Nat.toInt64]](#manual-Nat___toInt64) 9_223_372_036_854_775_808 = -9_223_372_036_854_775_808`
- `[Nat.toInt64]](#manual-Nat___toInt64) 18_446_744_073_709_551_618 = 0`

def

```lean
[Nat.toISize]](#manual-Nat___toISize) (n : [Nat]](#manual-Nat___zero)) : [ISize](https://lean-lang.org/doc/reference/latest/Basic-Types/Fixed-Precision-Integers/#ISize___ofUSize)



[Nat.toISize]](#manual-Nat___toISize) (n : [Nat]](#manual-Nat___zero)) : [ISize](https://lean-lang.org/doc/reference/latest/Basic-Types/Fixed-Precision-Integers/#ISize___ofUSize)
```

Converts an arbitrary-precision natural number to a word-sized signed integer, wrapping around on
overflow.

This function is overridden at runtime with an efficient implementation.

def

```lean
[Nat.toFloat]](#manual-Nat___toFloat) (n : [Nat]](#manual-Nat___zero)) : [Float](https://lean-lang.org/doc/reference/latest/Basic-Types/Floating-Point-Numbers/#Float___ofModel)



[Nat.toFloat]](#manual-Nat___toFloat) (n : [Nat]](#manual-Nat___zero)) : [Float](https://lean-lang.org/doc/reference/latest/Basic-Types/Floating-Point-Numbers/#Float___ofModel)
```

Converts a natural number into the closest-possible 64-bit floating-point number, or an infinite
floating-point value if the range of `[Float](https://lean-lang.org/doc/reference/latest/Basic-Types/Floating-Point-Numbers/#Float___ofModel)` is exceeded.

def

```lean
[Nat.toFloat32]](#manual-Nat___toFloat32) (n : [Nat]](#manual-Nat___zero)) : [Float32](https://lean-lang.org/doc/reference/latest/Basic-Types/Floating-Point-Numbers/#Float32___ofModel)



[Nat.toFloat32]](#manual-Nat___toFloat32) (n : [Nat]](#manual-Nat___zero)) : [Float32](https://lean-lang.org/doc/reference/latest/Basic-Types/Floating-Point-Numbers/#Float32___ofModel)
```

Converts a natural number into the closest-possible 32-bit floating-point number, or an infinite
floating-point value if the range of `[Float32](https://lean-lang.org/doc/reference/latest/Basic-Types/Floating-Point-Numbers/#Float32___ofModel)` is exceeded.

def

```lean
[Nat.isValidChar]](#manual-Nat___isValidChar) (n : [Nat]](#manual-Nat___zero)) : Prop



[Nat.isValidChar]](#manual-Nat___isValidChar) (n : [Nat]](#manual-Nat___zero)) : Prop
```

A `[Nat]](#manual-Nat___zero)` denotes a valid Unicode code point if it is less than `0x110000` and it is also not a
surrogate code point (the range `0xd800` to `0xdfff` inclusive).

def

```lean
[Nat.repr]](#manual-Nat___repr) (n : [Nat]](#manual-Nat___zero)) : [String](https://lean-lang.org/doc/reference/latest/Basic-Types/Strings/#String___ofByteArray)



[Nat.repr]](#manual-Nat___repr) (n : [Nat]](#manual-Nat___zero)) : [String](https://lean-lang.org/doc/reference/latest/Basic-Types/Strings/#String___ofByteArray)
```

Converts a natural number to its decimal string representation.

def

```lean
[Nat.toDigits]](#manual-Nat___toDigits) (base n : [Nat]](#manual-Nat___zero)) : [List](https://lean-lang.org/doc/reference/latest/Basic-Types/Linked-Lists/#List___nil) [Char](https://lean-lang.org/doc/reference/latest/Basic-Types/Characters/#Char___mk)



[Nat.toDigits]](#manual-Nat___toDigits) (base n : [Nat]](#manual-Nat___zero)) : [List](https://lean-lang.org/doc/reference/latest/Basic-Types/Linked-Lists/#List___nil) [Char](https://lean-lang.org/doc/reference/latest/Basic-Types/Characters/#Char___mk)
```

Returns the decimal representation of a natural number as a list of digit characters in the given
base. If the base is greater than `16` then `'*'` is returned for digits greater than `0xf`.

Examples:

- `[Nat.toDigits]](#manual-Nat___toDigits) 10 0xff = ['2', '5', '5']`
- `[Nat.toDigits]](#manual-Nat___toDigits) 8 0xc = ['1', '4']`
- `[Nat.toDigits]](#manual-Nat___toDigits) 16 0xcafe = ['c', 'a', 'f', 'e']`
- `[Nat.toDigits]](#manual-Nat___toDigits) 80 200 = ['2', '*']`

def

```lean
[Nat.digitChar]](#manual-Nat___digitChar) (n : [Nat]](#manual-Nat___zero)) : [Char](https://lean-lang.org/doc/reference/latest/Basic-Types/Characters/#Char___mk)



[Nat.digitChar]](#manual-Nat___digitChar) (n : [Nat]](#manual-Nat___zero)) : [Char](https://lean-lang.org/doc/reference/latest/Basic-Types/Characters/#Char___mk)
```

Returns a single digit representation of `n`, which is assumed to be in a base less than or equal to
`16`. Returns `'*'` if `n > 15`.

Examples:

- `[Nat.digitChar]](#manual-Nat___digitChar) 5 = '5'`
- `[Nat.digitChar]](#manual-Nat___digitChar) 12 = 'c'`
- `[Nat.digitChar]](#manual-Nat___digitChar) 15 = 'f'`
- `[Nat.digitChar]](#manual-Nat___digitChar) 16 = '*'`
- `[Nat.digitChar]](#manual-Nat___digitChar) 85 = '*'`

def

```lean
[Nat.toSubscriptString]](#manual-Nat___toSubscriptString) (n : [Nat]](#manual-Nat___zero)) : [String](https://lean-lang.org/doc/reference/latest/Basic-Types/Strings/#String___ofByteArray)



[Nat.toSubscriptString]](#manual-Nat___toSubscriptString) (n : [Nat]](#manual-Nat___zero)) : [String](https://lean-lang.org/doc/reference/latest/Basic-Types/Strings/#String___ofByteArray)
```

Converts a natural number to a string that contains the its decimal representation as Unicode
subscript digit characters.

Examples:

- `[Nat.toSubscriptString]](#manual-Nat___toSubscriptString) 0 = "₀"`
- `[Nat.toSubscriptString]](#manual-Nat___toSubscriptString) 35 = "₃₅"`

def

```lean
[Nat.toSuperscriptString]](#manual-Nat___toSuperscriptString) (n : [Nat]](#manual-Nat___zero)) : [String](https://lean-lang.org/doc/reference/latest/Basic-Types/Strings/#String___ofByteArray)



[Nat.toSuperscriptString]](#manual-Nat___toSuperscriptString) (n : [Nat]](#manual-Nat___zero)) : [String](https://lean-lang.org/doc/reference/latest/Basic-Types/Strings/#String___ofByteArray)
```

Converts a natural number to a string that contains the its decimal representation as Unicode
superscript digit characters.

Examples:

- `[Nat.toSuperscriptString]](#manual-Nat___toSuperscriptString) 0 = "⁰"`
- `[Nat.toSuperscriptString]](#manual-Nat___toSuperscriptString) 35 = "³⁵"`

def

```lean
[Nat.toSuperDigits]](#manual-Nat___toSuperDigits) (n : [Nat]](#manual-Nat___zero)) : [List](https://lean-lang.org/doc/reference/latest/Basic-Types/Linked-Lists/#List___nil) [Char](https://lean-lang.org/doc/reference/latest/Basic-Types/Characters/#Char___mk)



[Nat.toSuperDigits]](#manual-Nat___toSuperDigits) (n : [Nat]](#manual-Nat___zero)) : [List](https://lean-lang.org/doc/reference/latest/Basic-Types/Linked-Lists/#List___nil) [Char](https://lean-lang.org/doc/reference/latest/Basic-Types/Characters/#Char___mk)
```

Converts a natural number to the list of Unicode superscript digit characters that corresponds to
its decimal representation.

Examples:

- `[Nat.toSuperDigits]](#manual-Nat___toSuperDigits) 0 = ['⁰']`
- `[Nat.toSuperDigits]](#manual-Nat___toSuperDigits) 35 = ['³', '⁵']`

def

```lean
[Nat.toSubDigits]](#manual-Nat___toSubDigits) (n : [Nat]](#manual-Nat___zero)) : [List](https://lean-lang.org/doc/reference/latest/Basic-Types/Linked-Lists/#List___nil) [Char](https://lean-lang.org/doc/reference/latest/Basic-Types/Characters/#Char___mk)



[Nat.toSubDigits]](#manual-Nat___toSubDigits) (n : [Nat]](#manual-Nat___zero)) : [List](https://lean-lang.org/doc/reference/latest/Basic-Types/Linked-Lists/#List___nil) [Char](https://lean-lang.org/doc/reference/latest/Basic-Types/Characters/#Char___mk)
```

Converts a natural number to the list of Unicode subscript digit characters that corresponds to
its decimal representation.

Examples:

- `[Nat.toSubDigits]](#manual-Nat___toSubDigits) 0 = ['₀']`
- `[Nat.toSubDigits]](#manual-Nat___toSubDigits) 35 = ['₃', '₅']`

def

```lean
[Nat.subDigitChar]](#manual-Nat___subDigitChar) (n : [Nat]](#manual-Nat___zero)) : [Char](https://lean-lang.org/doc/reference/latest/Basic-Types/Characters/#Char___mk)



[Nat.subDigitChar]](#manual-Nat___subDigitChar) (n : [Nat]](#manual-Nat___zero)) : [Char](https://lean-lang.org/doc/reference/latest/Basic-Types/Characters/#Char___mk)
```

Converts a natural number less than `10` to the corresponding Unicode subscript digit character.
Returns `'*'` for other numbers.

Examples:

- `[Nat.subDigitChar]](#manual-Nat___subDigitChar) 3 = '₃'`
- `[Nat.subDigitChar]](#manual-Nat___subDigitChar) 7 = '₇'`
- `[Nat.subDigitChar]](#manual-Nat___subDigitChar) 10 = '*'`

def

```lean
[Nat.superDigitChar]](#manual-Nat___superDigitChar) (n : [Nat]](#manual-Nat___zero)) : [Char](https://lean-lang.org/doc/reference/latest/Basic-Types/Characters/#Char___mk)



[Nat.superDigitChar]](#manual-Nat___superDigitChar) (n : [Nat]](#manual-Nat___zero)) : [Char](https://lean-lang.org/doc/reference/latest/Basic-Types/Characters/#Char___mk)
```

Converts a natural number less than `10` to the corresponding Unicode superscript digit character.
Returns `'*'` for other numbers.

Examples:

- `[Nat.superDigitChar]](#manual-Nat___superDigitChar) 3 = '³'`
- `[Nat.superDigitChar]](#manual-Nat___superDigitChar) 7 = '⁷'`
- `[Nat.superDigitChar]](#manual-Nat___superDigitChar) 10 = '*'`

#### 20.1.4.8. Elimination {#manual-nat-api-elim}

The recursion principle that is automatically generated for `[Nat]](#manual-Nat___zero)` results in proof goals that are phrased in terms of `[Nat.zero]](#manual-Nat___zero)` and `[Nat.succ]](#manual-Nat___zero)`.
This is not particularly user-friendly, so an alternative logically-equivalent recursion principle is provided that results in goals that are phrased in terms of `0` and `n + 1`.
[Custom eliminators]](#manual---tech-term-Custom-eliminators) for the `[induction]](#manual-induction)` and `[cases]](#manual-cases)` tactics can be supplied using the `induction_eliminator` and `cases_eliminator` attributes.

def

```lean
[Nat.recAux.{u}]](#manual-Nat___recAux) {motive : [Nat]](#manual-Nat___zero) → Sort u} (zero : motive 0)
  (succ : (n : [Nat]](#manual-Nat___zero)) → motive n → motive [(]](#manual-HAdd___mk)n [+]](#manual-HAdd___mk) 1[)]](#manual-HAdd___mk)) (t : [Nat]](#manual-Nat___zero)) : motive t



[Nat.recAux.{u}]](#manual-Nat___recAux) {motive : [Nat]](#manual-Nat___zero) → Sort u}
  (zero : motive 0)
  (succ :
    (n : [Nat]](#manual-Nat___zero)) → motive n → motive [(]](#manual-HAdd___mk)n [+]](#manual-HAdd___mk) 1[)]](#manual-HAdd___mk))
  (t : [Nat]](#manual-Nat___zero)) : motive t
```

A recursor for `[Nat]](#manual-Nat___zero)` that uses the notations `0` for `[Nat.zero]](#manual-Nat___zero)` and `n + 1` for `[Nat.succ]](#manual-Nat___zero)`.

It is otherwise identical to the default recursor `Nat.rec`. It is used by the `[induction]](#manual-induction)` tactic
by default for `[Nat]](#manual-Nat___zero)`.

def

```lean
[Nat.casesAuxOn.{u}]](#manual-Nat___casesAuxOn) {motive : [Nat]](#manual-Nat___zero) → Sort u} (t : [Nat]](#manual-Nat___zero)) (zero : motive 0)
  (succ : (n : [Nat]](#manual-Nat___zero)) → motive [(]](#manual-HAdd___mk)n [+]](#manual-HAdd___mk) 1[)]](#manual-HAdd___mk)) : motive t



[Nat.casesAuxOn.{u}]](#manual-Nat___casesAuxOn) {motive : [Nat]](#manual-Nat___zero) → Sort u}
  (t : [Nat]](#manual-Nat___zero)) (zero : motive 0)
  (succ : (n : [Nat]](#manual-Nat___zero)) → motive [(]](#manual-HAdd___mk)n [+]](#manual-HAdd___mk) 1[)]](#manual-HAdd___mk)) :
  motive t
```

A case analysis principle for `[Nat]](#manual-Nat___zero)` that uses the notations `0` for `[Nat.zero]](#manual-Nat___zero)` and `n + 1` for
`[Nat.succ]](#manual-Nat___zero)`.

It is otherwise identical to the default recursor `Nat.casesOn`. It is used as the default `[Nat]](#manual-Nat___zero)`
case analysis principle for `[Nat]](#manual-Nat___zero)` by the `[cases]](#manual-cases)` tactic.

##### 20.1.4.8.1. Alternative Induction Principles {#manual-nat-api-induction}

def

```lean
[Nat.strongRecOn.{u}]](#manual-Nat___strongRecOn) {motive : [Nat]](#manual-Nat___zero) → Sort u} (n : [Nat]](#manual-Nat___zero))
  (ind : (n : [Nat]](#manual-Nat___zero)) → ((m : [Nat]](#manual-Nat___zero)) → m [<]](#manual-LT___mk) n → motive m) → motive n) :
  motive n



[Nat.strongRecOn.{u}]](#manual-Nat___strongRecOn)
  {motive : [Nat]](#manual-Nat___zero) → Sort u} (n : [Nat]](#manual-Nat___zero))
  (ind :
    (n : [Nat]](#manual-Nat___zero)) →
      ((m : [Nat]](#manual-Nat___zero)) → m [<]](#manual-LT___mk) n → motive m) →
        motive n) :
  motive n
```

Strong induction on the natural numbers.

The induction hypothesis is that all numbers less than a given number satisfy the motive, which
should be demonstrated for the given number.

def

```lean
[Nat.caseStrongRecOn.{u}]](#manual-Nat___caseStrongRecOn) {motive : [Nat]](#manual-Nat___zero) → Sort u} (a : [Nat]](#manual-Nat___zero))
  (zero : motive 0)
  (ind : (n : [Nat]](#manual-Nat___zero)) → ((m : [Nat]](#manual-Nat___zero)) → m [≤]](#manual-LE___mk) n → motive m) → motive n.[succ]](#manual-Nat___zero)) :
  motive a



[Nat.caseStrongRecOn.{u}]](#manual-Nat___caseStrongRecOn)
  {motive : [Nat]](#manual-Nat___zero) → Sort u} (a : [Nat]](#manual-Nat___zero))
  (zero : motive 0)
  (ind :
    (n : [Nat]](#manual-Nat___zero)) →
      ((m : [Nat]](#manual-Nat___zero)) → m [≤]](#manual-LE___mk) n → motive m) →
        motive n.[succ]](#manual-Nat___zero)) :
  motive a
```

Case analysis based on strong induction for the natural numbers.

def

```lean
[Nat.div.inductionOn.{u}]](#manual-Nat___div___inductionOn) {motive : [Nat]](#manual-Nat___zero) → [Nat]](#manual-Nat___zero) → Sort u} (x y : [Nat]](#manual-Nat___zero))
  (ind : (x y : [Nat]](#manual-Nat___zero)) → 0 [<]](#manual-LT___mk) y [∧]](#manual-And___intro) y [≤]](#manual-LE___mk) x → motive [(]](#manual-HSub___mk)x [-]](#manual-HSub___mk) y[)]](#manual-HSub___mk) y → motive x y)
  (base : (x y : [Nat]](#manual-Nat___zero)) → [¬]](#manual-Not)[(]](#manual-And___intro)0 [<]](#manual-LT___mk) y [∧]](#manual-And___intro) y [≤]](#manual-LE___mk) x[)]](#manual-And___intro) → motive x y) : motive x y



[Nat.div.inductionOn.{u}]](#manual-Nat___div___inductionOn)
  {motive : [Nat]](#manual-Nat___zero) → [Nat]](#manual-Nat___zero) → Sort u}
  (x y : [Nat]](#manual-Nat___zero))
  (ind :
    (x y : [Nat]](#manual-Nat___zero)) →
      0 [<]](#manual-LT___mk) y [∧]](#manual-And___intro) y [≤]](#manual-LE___mk) x →
        motive [(]](#manual-HSub___mk)x [-]](#manual-HSub___mk) y[)]](#manual-HSub___mk) y → motive x y)
  (base :
    (x y : [Nat]](#manual-Nat___zero)) →
      [¬]](#manual-Not)[(]](#manual-And___intro)0 [<]](#manual-LT___mk) y [∧]](#manual-And___intro) y [≤]](#manual-LE___mk) x[)]](#manual-And___intro) → motive x y) :
  motive x y
```

An induction principle customized for reasoning about the recursion pattern of natural number
division by iterated subtraction.

def

```lean
[Nat.div2Induction.{u}]](#manual-Nat___div2Induction) {motive : [Nat]](#manual-Nat___zero) → Sort u} (n : [Nat]](#manual-Nat___zero))
  (ind : (n : [Nat]](#manual-Nat___zero)) → (n > 0 → motive [(]](#manual-HDiv___mk)n [/]](#manual-HDiv___mk) 2[)]](#manual-HDiv___mk)) → motive n) : motive n



[Nat.div2Induction.{u}]](#manual-Nat___div2Induction)
  {motive : [Nat]](#manual-Nat___zero) → Sort u} (n : [Nat]](#manual-Nat___zero))
  (ind :
    (n : [Nat]](#manual-Nat___zero)) →
      (n > 0 → motive [(]](#manual-HDiv___mk)n [/]](#manual-HDiv___mk) 2[)]](#manual-HDiv___mk)) →
        motive n) :
  motive n
```

An induction principle for the natural numbers with two cases:

- `n = 0`, and the motive is satisfied for `0`
- `n > 0`, and the motive should be satisfied for `n` on the assumption that it is satisfied for
  `n / 2`.

def

```lean
[Nat.mod.inductionOn.{u}]](#manual-Nat___mod___inductionOn) {motive : [Nat]](#manual-Nat___zero) → [Nat]](#manual-Nat___zero) → Sort u} (x y : [Nat]](#manual-Nat___zero))
  (ind : (x y : [Nat]](#manual-Nat___zero)) → 0 [<]](#manual-LT___mk) y [∧]](#manual-And___intro) y [≤]](#manual-LE___mk) x → motive [(]](#manual-HSub___mk)x [-]](#manual-HSub___mk) y[)]](#manual-HSub___mk) y → motive x y)
  (base : (x y : [Nat]](#manual-Nat___zero)) → [¬]](#manual-Not)[(]](#manual-And___intro)0 [<]](#manual-LT___mk) y [∧]](#manual-And___intro) y [≤]](#manual-LE___mk) x[)]](#manual-And___intro) → motive x y) : motive x y



[Nat.mod.inductionOn.{u}]](#manual-Nat___mod___inductionOn)
  {motive : [Nat]](#manual-Nat___zero) → [Nat]](#manual-Nat___zero) → Sort u}
  (x y : [Nat]](#manual-Nat___zero))
  (ind :
    (x y : [Nat]](#manual-Nat___zero)) →
      0 [<]](#manual-LT___mk) y [∧]](#manual-And___intro) y [≤]](#manual-LE___mk) x →
        motive [(]](#manual-HSub___mk)x [-]](#manual-HSub___mk) y[)]](#manual-HSub___mk) y → motive x y)
  (base :
    (x y : [Nat]](#manual-Nat___zero)) →
      [¬]](#manual-Not)[(]](#manual-And___intro)0 [<]](#manual-LT___mk) y [∧]](#manual-And___intro) y [≤]](#manual-LE___mk) x[)]](#manual-And___intro) → motive x y) :
  motive x y
```

An induction principle customized for reasoning about the recursion pattern of `[Nat.mod]](#manual-Nat___mod)`.

---



## Basic Types — 20.2. Integers {#manual-basic-types-202-integers}

> 📄 Source: https://lean-lang.org/doc/reference/latest/Basic-Types/Integers/

The integers are whole numbers, both positive and negative.
Integers are arbitrary-precision, limited only by the capability of the hardware on which Lean is running; for fixed-width integers that are used in programming and computer science, please see the [section on fixed-precision integers](https://lean-lang.org/doc/reference/latest/Basic-Types/Fixed-Precision-Integers/#fixed-ints).

Integers are specially supported by Lean's implementation.
The logical model of the integers is based on the natural numbers: each integer is modeled as either a natural number or the negative successor of a natural number.
Operations on the integers are specified using this model, which is used in the kernel and in interpreted code.
In these contexts, integer code inherits the performance benefits of the natural numbers' special support.
In compiled code, integers are represented as efficient arbitrary-precision integers, and sufficiently small numbers are stored as values that don't require indirection through a pointer.
Arithmetic operations are implemented by primitives that take advantage of the efficient representations.

### 20.2.1. Logical Model {#manual-int-model}

Integers are represented either as a natural number or as the negation of the successor of a natural number.

inductive type

```lean
[Int]](#manual-Int___ofNat) : Type



[Int]](#manual-Int___ofNat) : Type
```

The integers.

This type is special-cased by the compiler and overridden with an efficient implementation. The
runtime has a special representation for `[Int]](#manual-Int___ofNat)` that stores “small” signed numbers directly, while
larger numbers use a fast arbitrary-precision arithmetic library (usually
[GMP](https://gmplib.org/)). A “small number” is an integer that can be encoded with one fewer bits
than the platform's pointer size (i.e. 63 bits on 64-bit architectures and 31 bits on 32-bit
architectures).

Constructors

```lean
[Int.ofNat]](#manual-Int___ofNat) : [Nat]](#manual-Nat___zero) → [Int]](#manual-Int___ofNat)
```

A natural number is an integer.

This constructor covers the non-negative integers (from `0` to `∞`).

```lean
[Int.negSucc]](#manual-Int___ofNat) : [Nat]](#manual-Nat___zero) → [Int]](#manual-Int___ofNat)
```

The negation of the successor of a natural number is an integer.

This constructor covers the negative integers (from `-1` to `-∞`).

This representation of the integers has a number of useful properties.
It is relatively simple to use and to understand.
Unlike a pair of a sign and a `[Nat]](#manual-Nat___zero)`, there is a unique representation for `0`, which simplifies reasoning about equality.
Integers can also be represented as a pair of natural numbers in which one is subtracted from the other, but this requires a [quotient type]](#manual-quotients) to be well-behaved, and quotient types can be laborious to work with due to the need to prove that functions respect the equivalence relation.

### 20.2.2. Run-Time Representation {#manual-int-runtime}

Like [natural numbers]](#manual-nat-runtime), sufficiently-small integers are represented without pointers: the lowest-order bit in an object pointer is used to indicate that the value is not, in fact, a pointer.
If an integer is too large to fit in the remaining bits, it is instead allocated as an ordinary Lean object that consists of an object header and an arbitrary-precision integer.

### 20.2.3. Syntax {#manual-int-syntax}

The `[OfNat]](#manual-OfNat___mk) [Int]](#manual-Int___ofNat)` instance allows numerals to be used as literals, both in expression and in pattern contexts.
`([OfNat.ofNat]](#manual-OfNat___mk) n : [Int]](#manual-Int___ofNat))` reduces to the constructor application `[Int.ofNat]](#manual-Int___ofNat) n`.
The `[Neg]](#manual-Neg___mk) [Int]](#manual-Int___ofNat)` instance allows negation to be used as well.

On top of these instances, there is special syntax for the constructor `[Int.negSucc]](#manual-Int___ofNat)` that is available when the `Int` namespace is opened.
The notation `-[ n +1]` is suggestive of `-(n + 1)`, which is the meaning of `[Int.negSucc]](#manual-Int___ofNat) n`.

syntaxNegative Successor

`-[ n +1]` is notation for `[Int.negSucc]](#manual-Int___ofNat) n`.

```lean
term ::= ...
    | -[ term +1]
```

### 20.2.4. API Reference {#manual-The-Lean-Language-Reference--Basic-Types--Integers--API-Reference}

#### 20.2.4.1. Properties {#manual-The-Lean-Language-Reference--Basic-Types--Integers--API-Reference--Properties}

def

```lean
[Int.sign]](#manual-Int___sign) : [Int]](#manual-Int___ofNat) → [Int]](#manual-Int___ofNat)



[Int.sign]](#manual-Int___sign) : [Int]](#manual-Int___ofNat) → [Int]](#manual-Int___ofNat)
```

Returns the “sign” of the integer as another integer:

- `1` for positive numbers,
- `-1` for negative numbers, and
- `0` for `0`.

Examples:

- `[Int.sign]](#manual-Int___sign) 34 = 1`
- `[Int.sign]](#manual-Int___sign) 2 = 1`
- `[Int.sign]](#manual-Int___sign) 0 = 0`
- `[Int.sign]](#manual-Int___sign) -1 = -1`
- `[Int.sign]](#manual-Int___sign) -362 = -1`

#### 20.2.4.2. Conversions {#manual-The-Lean-Language-Reference--Basic-Types--Integers--API-Reference--Conversions}

def

```lean
[Int.natAbs]](#manual-Int___natAbs) (m : [Int]](#manual-Int___ofNat)) : [Nat]](#manual-Nat___zero)



[Int.natAbs]](#manual-Int___natAbs) (m : [Int]](#manual-Int___ofNat)) : [Nat]](#manual-Nat___zero)
```

The absolute value of an integer is its distance from `0`.

This function is overridden by the compiler with an efficient implementation. This definition is
the logical model.

Examples:

- `(7 : [Int]](#manual-Int___ofNat)).[natAbs]](#manual-Int___natAbs) = 7`
- `(0 : [Int]](#manual-Int___ofNat)).[natAbs]](#manual-Int___natAbs) = 0`
- `(-11 : [Int]](#manual-Int___ofNat)).[natAbs]](#manual-Int___natAbs) = 11`

def

```lean
[Int.toNat]](#manual-Int___toNat) : [Int]](#manual-Int___ofNat) → [Nat]](#manual-Nat___zero)



[Int.toNat]](#manual-Int___toNat) : [Int]](#manual-Int___ofNat) → [Nat]](#manual-Nat___zero)
```

Converts an integer into a natural number. Negative numbers are converted to `0`.

Examples:

- `(7 : [Int]](#manual-Int___ofNat)).[toNat]](#manual-Int___toNat) = 7`
- `(0 : [Int]](#manual-Int___ofNat)).[toNat]](#manual-Int___toNat) = 0`
- `(-7 : [Int]](#manual-Int___ofNat)).[toNat]](#manual-Int___toNat) = 0`

def

```lean
[Int.toNat?]](#manual-Int___toNat___) : [Int]](#manual-Int___ofNat) → [Option](https://lean-lang.org/doc/reference/latest/Basic-Types/Optional-Values/#Option___none) [Nat]](#manual-Nat___zero)



[Int.toNat?]](#manual-Int___toNat___) : [Int]](#manual-Int___ofNat) → [Option](https://lean-lang.org/doc/reference/latest/Basic-Types/Optional-Values/#Option___none) [Nat]](#manual-Nat___zero)
```

Converts an integer into a natural number. Returns `[none](https://lean-lang.org/doc/reference/latest/Basic-Types/Optional-Values/#Option___none)` for negative numbers.

Examples:

- `(7 : [Int]](#manual-Int___ofNat)).[toNat?]](#manual-Int___toNat___) = [some](https://lean-lang.org/doc/reference/latest/Basic-Types/Optional-Values/#Option___none) 7`
- `(0 : [Int]](#manual-Int___ofNat)).[toNat?]](#manual-Int___toNat___) = [some](https://lean-lang.org/doc/reference/latest/Basic-Types/Optional-Values/#Option___none) 0`
- `(-7 : [Int]](#manual-Int___ofNat)).[toNat?]](#manual-Int___toNat___) = [none](https://lean-lang.org/doc/reference/latest/Basic-Types/Optional-Values/#Option___none)`

def

```lean
[Int.toISize]](#manual-Int___toISize) (i : [Int]](#manual-Int___ofNat)) : [ISize](https://lean-lang.org/doc/reference/latest/Basic-Types/Fixed-Precision-Integers/#ISize___ofUSize)



[Int.toISize]](#manual-Int___toISize) (i : [Int]](#manual-Int___ofNat)) : [ISize](https://lean-lang.org/doc/reference/latest/Basic-Types/Fixed-Precision-Integers/#ISize___ofUSize)
```

Converts an arbitrary-precision integer to a word-sized signed integer, wrapping around on over- or
underflow.

This function is overridden at runtime with an efficient implementation.

def

```lean
[Int.toInt8]](#manual-Int___toInt8) (i : [Int]](#manual-Int___ofNat)) : [Int8](https://lean-lang.org/doc/reference/latest/Basic-Types/Fixed-Precision-Integers/#Int8___ofUInt8)



[Int.toInt8]](#manual-Int___toInt8) (i : [Int]](#manual-Int___ofNat)) : [Int8](https://lean-lang.org/doc/reference/latest/Basic-Types/Fixed-Precision-Integers/#Int8___ofUInt8)
```

Converts an arbitrary-precision integer to an 8-bit integer, wrapping on overflow or underflow.

Examples:

- `[Int.toInt8]](#manual-Int___toInt8) 48 = 48`
- `[Int.toInt8]](#manual-Int___toInt8) (-115) = -115`
- `[Int.toInt8]](#manual-Int___toInt8) (-129) = 127`
- `[Int.toInt8]](#manual-Int___toInt8) (128) = -128`

def

```lean
[Int.toInt16]](#manual-Int___toInt16) (i : [Int]](#manual-Int___ofNat)) : [Int16](https://lean-lang.org/doc/reference/latest/Basic-Types/Fixed-Precision-Integers/#Int16___ofUInt16)



[Int.toInt16]](#manual-Int___toInt16) (i : [Int]](#manual-Int___ofNat)) : [Int16](https://lean-lang.org/doc/reference/latest/Basic-Types/Fixed-Precision-Integers/#Int16___ofUInt16)
```

Converts an arbitrary-precision integer to a 16-bit integer, wrapping on overflow or underflow.

Examples:

- `[Int.toInt16]](#manual-Int___toInt16) 48 = 48`
- `[Int.toInt16]](#manual-Int___toInt16) (-129) = -129`
- `[Int.toInt16]](#manual-Int___toInt16) (128) = 128`
- `[Int.toInt16]](#manual-Int___toInt16) 70000 = 4464`
- `[Int.toInt16]](#manual-Int___toInt16) (-40000) = 25536`

def

```lean
[Int.toInt32]](#manual-Int___toInt32) (i : [Int]](#manual-Int___ofNat)) : [Int32](https://lean-lang.org/doc/reference/latest/Basic-Types/Fixed-Precision-Integers/#Int32___ofUInt32)



[Int.toInt32]](#manual-Int___toInt32) (i : [Int]](#manual-Int___ofNat)) : [Int32](https://lean-lang.org/doc/reference/latest/Basic-Types/Fixed-Precision-Integers/#Int32___ofUInt32)
```

Converts an arbitrary-precision integer to a 32-bit integer, wrapping on overflow or underflow.

Examples:

- `[Int.toInt32]](#manual-Int___toInt32) 48 = 48`
- `[Int.toInt32]](#manual-Int___toInt32) (-129) = -129`
- `[Int.toInt32]](#manual-Int___toInt32) 70000 = 70000`
- `[Int.toInt32]](#manual-Int___toInt32) (-40000) = -40000`
- `[Int.toInt32]](#manual-Int___toInt32) 2147483648 = -2147483648`
- `[Int.toInt32]](#manual-Int___toInt32) (-2147483649) = 2147483647`

def

```lean
[Int.toInt64]](#manual-Int___toInt64) (i : [Int]](#manual-Int___ofNat)) : [Int64](https://lean-lang.org/doc/reference/latest/Basic-Types/Fixed-Precision-Integers/#Int64___ofUInt64)



[Int.toInt64]](#manual-Int___toInt64) (i : [Int]](#manual-Int___ofNat)) : [Int64](https://lean-lang.org/doc/reference/latest/Basic-Types/Fixed-Precision-Integers/#Int64___ofUInt64)
```

Converts an arbitrary-precision integer to a 64-bit integer, wrapping on overflow or underflow.

This function is overridden at runtime with an efficient implementation.

Examples:

- `[Int.toInt64]](#manual-Int___toInt64) 48 = 48`
- `[Int.toInt64]](#manual-Int___toInt64) (-40_000) = -40_000`
- `[Int.toInt64]](#manual-Int___toInt64) 2_147_483_648 = 2_147_483_648`
- `[Int.toInt64]](#manual-Int___toInt64) (-2_147_483_649) = -2_147_483_649`
- `[Int.toInt64]](#manual-Int___toInt64) 9_223_372_036_854_775_808 = -9_223_372_036_854_775_808`
- `[Int.toInt64]](#manual-Int___toInt64) (-9_223_372_036_854_775_809) = 9_223_372_036_854_775_807`

def

```lean
[Int.repr]](#manual-Int___repr) : [Int]](#manual-Int___ofNat) → [String](https://lean-lang.org/doc/reference/latest/Basic-Types/Strings/#String___ofByteArray)



[Int.repr]](#manual-Int___repr) : [Int]](#manual-Int___ofNat) → [String](https://lean-lang.org/doc/reference/latest/Basic-Types/Strings/#String___ofByteArray)
```

Returns the decimal string representation of an integer.

#### 20.2.4.3. Arithmetic {#manual-The-Lean-Language-Reference--Basic-Types--Integers--API-Reference--Arithmetic}

Typically, arithmetic operations on integers are accessed using Lean's overloaded arithmetic notation.
In particular, the instances of `[Add]](#manual-Add___mk) [Int]](#manual-Int___ofNat)`, `[Neg]](#manual-Neg___mk) [Int]](#manual-Int___ofNat)`, `[Sub]](#manual-Sub___mk) [Int]](#manual-Int___ofNat)`, and `[Mul]](#manual-Mul___mk) [Int]](#manual-Int___ofNat)` allow ordinary infix operators to be used.
[Division]](#manual-int-div) is somewhat more intricate, because there are multiple sensible notions of division on integers.

def

```lean
[Int.add]](#manual-Int___add) (m n : [Int]](#manual-Int___ofNat)) : [Int]](#manual-Int___ofNat)



[Int.add]](#manual-Int___add) (m n : [Int]](#manual-Int___ofNat)) : [Int]](#manual-Int___ofNat)
```

Addition of integers, usually accessed via the `+` operator.

This function is overridden by the compiler with an efficient implementation. This definition is
the logical model.

Examples:

- `(7 : [Int]](#manual-Int___ofNat)) + (6 : [Int]](#manual-Int___ofNat)) = 13`
- `(6 : [Int]](#manual-Int___ofNat)) + (-6 : [Int]](#manual-Int___ofNat)) = 0`

def

```lean
[Int.sub]](#manual-Int___sub) (m n : [Int]](#manual-Int___ofNat)) : [Int]](#manual-Int___ofNat)



[Int.sub]](#manual-Int___sub) (m n : [Int]](#manual-Int___ofNat)) : [Int]](#manual-Int___ofNat)
```

Subtraction of integers, usually accessed via the `-` operator.

This function is overridden by the compiler with an efficient implementation. This definition is
the logical model.

Examples:

- `(63 : [Int]](#manual-Int___ofNat)) - (6 : [Int]](#manual-Int___ofNat)) = 57`
- `(7 : [Int]](#manual-Int___ofNat)) - (0 : [Int]](#manual-Int___ofNat)) = 7`
- `(0 : [Int]](#manual-Int___ofNat)) - (7 : [Int]](#manual-Int___ofNat)) = -7`

def

```lean
[Int.subNatNat]](#manual-Int___subNatNat) (m n : [Nat]](#manual-Nat___zero)) : [Int]](#manual-Int___ofNat)



[Int.subNatNat]](#manual-Int___subNatNat) (m n : [Nat]](#manual-Nat___zero)) : [Int]](#manual-Int___ofNat)
```

Non-truncating subtraction of two natural numbers.

Examples:

- `[Int.subNatNat]](#manual-Int___subNatNat) 5 2 = 3`
- `[Int.subNatNat]](#manual-Int___subNatNat) 2 5 = -3`
- `[Int.subNatNat]](#manual-Int___subNatNat) 0 13 = -13`

def

```lean
[Int.neg]](#manual-Int___neg) (n : [Int]](#manual-Int___ofNat)) : [Int]](#manual-Int___ofNat)



[Int.neg]](#manual-Int___neg) (n : [Int]](#manual-Int___ofNat)) : [Int]](#manual-Int___ofNat)
```

Negation of integers, usually accessed via the `-` prefix operator.

This function is overridden by the compiler with an efficient implementation. This definition is
the logical model.

Examples:

- `-(6 : [Int]](#manual-Int___ofNat)) = -6`
- `-(-6 : [Int]](#manual-Int___ofNat)) = 6`
- `(12 : [Int]](#manual-Int___ofNat)).[neg]](#manual-Int___neg) = -12`

def

```lean
[Int.negOfNat]](#manual-Int___negOfNat) : [Nat]](#manual-Nat___zero) → [Int]](#manual-Int___ofNat)



[Int.negOfNat]](#manual-Int___negOfNat) : [Nat]](#manual-Nat___zero) → [Int]](#manual-Int___ofNat)
```

Negation of natural numbers.

Examples:

- `[Int.negOfNat]](#manual-Int___negOfNat) 6 = -6`
- `[Int.negOfNat]](#manual-Int___negOfNat) 0 = 0`

def

```lean
[Int.mul]](#manual-Int___mul) (m n : [Int]](#manual-Int___ofNat)) : [Int]](#manual-Int___ofNat)



[Int.mul]](#manual-Int___mul) (m n : [Int]](#manual-Int___ofNat)) : [Int]](#manual-Int___ofNat)
```

Multiplication of integers, usually accessed via the `*` operator.

This function is overridden by the compiler with an efficient implementation. This definition is
the logical model.

Examples:

- `(63 : [Int]](#manual-Int___ofNat)) * (6 : [Int]](#manual-Int___ofNat)) = 378`
- `(6 : [Int]](#manual-Int___ofNat)) * (-6 : [Int]](#manual-Int___ofNat)) = -36`
- `(7 : [Int]](#manual-Int___ofNat)) * (0 : [Int]](#manual-Int___ofNat)) = 0`

def

```lean
[Int.pow]](#manual-Int___pow) : [Int]](#manual-Int___ofNat) → [Nat]](#manual-Nat___zero) → [Int]](#manual-Int___ofNat)



[Int.pow]](#manual-Int___pow) : [Int]](#manual-Int___ofNat) → [Nat]](#manual-Nat___zero) → [Int]](#manual-Int___ofNat)
```

Power of an integer to a natural number, usually accessed via the `^` operator.

Examples:

- `(2 : [Int]](#manual-Int___ofNat)) ^ 4 = 16`
- `(10 : [Int]](#manual-Int___ofNat)) ^ 0 = 1`
- `(0 : [Int]](#manual-Int___ofNat)) ^ 10 = 0`
- `(-7 : [Int]](#manual-Int___ofNat)) ^ 3 = -343`

def

```lean
[Int.gcd]](#manual-Int___gcd) (m n : [Int]](#manual-Int___ofNat)) : [Nat]](#manual-Nat___zero)



[Int.gcd]](#manual-Int___gcd) (m n : [Int]](#manual-Int___ofNat)) : [Nat]](#manual-Nat___zero)
```

Computes the greatest common divisor of two integers as a natural number. The GCD of two integers is
the largest natural number that evenly divides both. However, the GCD of a number and `0` is the
number's absolute value.

This implementation uses `[Nat.gcd]](#manual-Nat___gcd)`, which is overridden in both the kernel and the compiler to
efficiently evaluate using arbitrary-precision arithmetic.

Examples:

- `[Int.gcd]](#manual-Int___gcd) 10 15 = 5`
- `[Int.gcd]](#manual-Int___gcd) 10 (-15) = 5`
- `[Int.gcd]](#manual-Int___gcd) (-6) (-9) = 3`
- `[Int.gcd]](#manual-Int___gcd) 0 5 = 5`
- `[Int.gcd]](#manual-Int___gcd) (-7) 0 = 7`

def

```lean
[Int.lcm]](#manual-Int___lcm) (m n : [Int]](#manual-Int___ofNat)) : [Nat]](#manual-Nat___zero)



[Int.lcm]](#manual-Int___lcm) (m n : [Int]](#manual-Int___ofNat)) : [Nat]](#manual-Nat___zero)
```

Computes the least common multiple of two integers as a natural number. The LCM of two integers is
the smallest natural number that's evenly divisible by the absolute values of both.

Examples:

- `[Int.lcm]](#manual-Int___lcm) 9 6 = 18`
- `[Int.lcm]](#manual-Int___lcm) 9 (-6) = 18`
- `[Int.lcm]](#manual-Int___lcm) 9 3 = 9`
- `[Int.lcm]](#manual-Int___lcm) 9 (-3) = 9`
- `[Int.lcm]](#manual-Int___lcm) 0 3 = 0`
- `[Int.lcm]](#manual-Int___lcm) (-3) 0 = 0`

##### 20.2.4.3.1. Division {#manual-int-div}

The `[Div]](#manual-Div___mk) [Int]](#manual-Int___ofNat)` and `[Mod]](#manual-Mod___mk) [Int]](#manual-Int___ofNat)` instances implement Euclidean division, described in the reference for `[Int.ediv]](#manual-Int___ediv)`.
This is not, however, the only sensible convention for rounding and remainders in division.
Four pairs of division and modulus functions are available, implementing various conventions.

**Example: Division by 0**

In all integer division conventions, division by `0` is defined to be `0`:

```lean
[#eval]](#manual-Lean___Parser___Command___eval) [Int.ediv]](#manual-Int___ediv) 5 0
[#eval]](#manual-Lean___Parser___Command___eval) [Int.ediv]](#manual-Int___ediv) 0 0
[#eval]](#manual-Lean___Parser___Command___eval) [Int.ediv]](#manual-Int___ediv) (-5) 0
[#eval]](#manual-Lean___Parser___Command___eval) [Int.bdiv]](#manual-Int___bdiv) 5 0
[#eval]](#manual-Lean___Parser___Command___eval) [Int.bdiv]](#manual-Int___bdiv) 0 0
[#eval]](#manual-Lean___Parser___Command___eval) [Int.bdiv]](#manual-Int___bdiv) (-5) 0
[#eval]](#manual-Lean___Parser___Command___eval) [Int.fdiv]](#manual-Int___fdiv) 5 0
[#eval]](#manual-Lean___Parser___Command___eval) [Int.fdiv]](#manual-Int___fdiv) 0 0
[#eval]](#manual-Lean___Parser___Command___eval) [Int.fdiv]](#manual-Int___fdiv) (-5) 0
[#eval]](#manual-Lean___Parser___Command___eval) [Int.tdiv]](#manual-Int___tdiv) 5 0
[#eval]](#manual-Lean___Parser___Command___eval) [Int.tdiv]](#manual-Int___tdiv) 0 0
[#eval]](#manual-Lean___Parser___Command___eval) [Int.tdiv]](#manual-Int___tdiv) (-5) 0
```

All evaluate to 0.

```lean
0
```

def

```lean
[Int.ediv]](#manual-Int___ediv) : [Int]](#manual-Int___ofNat) → [Int]](#manual-Int___ofNat) → [Int]](#manual-Int___ofNat)



[Int.ediv]](#manual-Int___ediv) : [Int]](#manual-Int___ofNat) → [Int]](#manual-Int___ofNat) → [Int]](#manual-Int___ofNat)
```

Integer division that uses the E-rounding convention. Usually accessed via the `/` operator.
Division by zero is defined to be zero, rather than an error.

In the E-rounding convention (Euclidean division), `[Int.emod]](#manual-Int___emod) x y` satisfies `0 ≤ Int.emod x y < Int.natAbs y`
for `y ≠ 0` and `[Int.ediv]](#manual-Int___ediv)` is the unique function satisfying `[Int.emod]](#manual-Int___emod) x y + ([Int.ediv]](#manual-Int___ediv) x y) * y = x`
for `y ≠ 0`.

This means that `[Int.ediv]](#manual-Int___ediv) x y` is `⌊x / y⌋` when `y > 0` and `⌈x / y⌉` when `y < 0`.

This function is overridden by the compiler with an efficient implementation. This definition is
the logical model.

Examples:

- `(7 : [Int]](#manual-Int___ofNat)) / (0 : [Int]](#manual-Int___ofNat)) = 0`
- `(0 : [Int]](#manual-Int___ofNat)) / (7 : [Int]](#manual-Int___ofNat)) = 0`
- `(12 : [Int]](#manual-Int___ofNat)) / (6 : [Int]](#manual-Int___ofNat)) = 2`
- `(12 : [Int]](#manual-Int___ofNat)) / (-6 : [Int]](#manual-Int___ofNat)) = -2`
- `(-12 : [Int]](#manual-Int___ofNat)) / (6 : [Int]](#manual-Int___ofNat)) = -2`
- `(-12 : [Int]](#manual-Int___ofNat)) / (-6 : [Int]](#manual-Int___ofNat)) = 2`
- `(12 : [Int]](#manual-Int___ofNat)) / (7 : [Int]](#manual-Int___ofNat)) = 1`
- `(12 : [Int]](#manual-Int___ofNat)) / (-7 : [Int]](#manual-Int___ofNat)) = -1`
- `(-12 : [Int]](#manual-Int___ofNat)) / (7 : [Int]](#manual-Int___ofNat)) = -2`
- `(-12 : [Int]](#manual-Int___ofNat)) / (-7 : [Int]](#manual-Int___ofNat)) = 2`

def

```lean
[Int.emod]](#manual-Int___emod) : [Int]](#manual-Int___ofNat) → [Int]](#manual-Int___ofNat) → [Int]](#manual-Int___ofNat)



[Int.emod]](#manual-Int___emod) : [Int]](#manual-Int___ofNat) → [Int]](#manual-Int___ofNat) → [Int]](#manual-Int___ofNat)
```

Integer modulus that uses the E-rounding convention. Usually accessed via the `%` operator.

In the E-rounding convention (Euclidean division), `[Int.emod]](#manual-Int___emod) x y` satisfies `0 ≤ Int.emod x y < Int.natAbs y`
for `y ≠ 0` and `[Int.ediv]](#manual-Int___ediv)` is the unique function satisfying `[Int.emod]](#manual-Int___emod) x y + ([Int.ediv]](#manual-Int___ediv) x y) * y = x`
for `y ≠ 0`.

This function is overridden by the compiler with an efficient implementation. This definition is
the logical model.

Examples:

- `(7 : [Int]](#manual-Int___ofNat)) % (0 : [Int]](#manual-Int___ofNat)) = 7`
- `(0 : [Int]](#manual-Int___ofNat)) % (7 : [Int]](#manual-Int___ofNat)) = 0`
- `(12 : [Int]](#manual-Int___ofNat)) % (6 : [Int]](#manual-Int___ofNat)) = 0`
- `(12 : [Int]](#manual-Int___ofNat)) % (-6 : [Int]](#manual-Int___ofNat)) = 0`
- `(-12 : [Int]](#manual-Int___ofNat)) % (6 : [Int]](#manual-Int___ofNat)) = 0`
- `(-12 : [Int]](#manual-Int___ofNat)) % (-6 : [Int]](#manual-Int___ofNat)) = 0`
- `(12 : [Int]](#manual-Int___ofNat)) % (7 : [Int]](#manual-Int___ofNat)) = 5`
- `(12 : [Int]](#manual-Int___ofNat)) % (-7 : [Int]](#manual-Int___ofNat)) = 5`
- `(-12 : [Int]](#manual-Int___ofNat)) % (7 : [Int]](#manual-Int___ofNat)) = 2`
- `(-12 : [Int]](#manual-Int___ofNat)) % (-7 : [Int]](#manual-Int___ofNat)) = 2`

def

```lean
[Int.tdiv]](#manual-Int___tdiv) : [Int]](#manual-Int___ofNat) → [Int]](#manual-Int___ofNat) → [Int]](#manual-Int___ofNat)



[Int.tdiv]](#manual-Int___tdiv) : [Int]](#manual-Int___ofNat) → [Int]](#manual-Int___ofNat) → [Int]](#manual-Int___ofNat)
```

Integer division using the T-rounding convention.

In [the T-rounding convention](https://dl.acm.org/doi/pdf/10.1145/128861.128862) (division with truncation), all rounding is towards zero.
Division by 0 is defined to be 0. In this convention, `[Int.tmod]](#manual-Int___tmod) a b + b * ([Int.tdiv]](#manual-Int___tdiv) a b) = a`.

This function is overridden by the compiler with an efficient implementation. This definition is the
logical model.

Examples:

- `(7 : [Int]](#manual-Int___ofNat)).[tdiv]](#manual-Int___tdiv) (0 : [Int]](#manual-Int___ofNat)) = 0`
- `(0 : [Int]](#manual-Int___ofNat)).[tdiv]](#manual-Int___tdiv) (7 : [Int]](#manual-Int___ofNat)) = 0`
- `(12 : [Int]](#manual-Int___ofNat)).[tdiv]](#manual-Int___tdiv) (6 : [Int]](#manual-Int___ofNat)) = 2`
- `(12 : [Int]](#manual-Int___ofNat)).[tdiv]](#manual-Int___tdiv) (-6 : [Int]](#manual-Int___ofNat)) = -2`
- `(-12 : [Int]](#manual-Int___ofNat)).[tdiv]](#manual-Int___tdiv) (6 : [Int]](#manual-Int___ofNat)) = -2`
- `(-12 : [Int]](#manual-Int___ofNat)).[tdiv]](#manual-Int___tdiv) (-6 : [Int]](#manual-Int___ofNat)) = 2`
- `(12 : [Int]](#manual-Int___ofNat)).[tdiv]](#manual-Int___tdiv) (7 : [Int]](#manual-Int___ofNat)) = 1`
- `(12 : [Int]](#manual-Int___ofNat)).[tdiv]](#manual-Int___tdiv) (-7 : [Int]](#manual-Int___ofNat)) = -1`
- `(-12 : [Int]](#manual-Int___ofNat)).[tdiv]](#manual-Int___tdiv) (7 : [Int]](#manual-Int___ofNat)) = -1`
- `(-12 : [Int]](#manual-Int___ofNat)).[tdiv]](#manual-Int___tdiv) (-7 : [Int]](#manual-Int___ofNat)) = 1`

def

```lean
[Int.tmod]](#manual-Int___tmod) : [Int]](#manual-Int___ofNat) → [Int]](#manual-Int___ofNat) → [Int]](#manual-Int___ofNat)



[Int.tmod]](#manual-Int___tmod) : [Int]](#manual-Int___ofNat) → [Int]](#manual-Int___ofNat) → [Int]](#manual-Int___ofNat)
```

Integer modulo using the T-rounding convention.

In [the T-rounding convention](https://dl.acm.org/doi/pdf/10.1145/128861.128862) (division with truncation), all rounding is towards zero.
Division by 0 is defined to be 0 and `[Int.tmod]](#manual-Int___tmod) a 0 = a`.

In this convention, `[Int.tmod]](#manual-Int___tmod) a b + b * ([Int.tdiv]](#manual-Int___tdiv) a b) = a`. Additionally,
`[Int.natAbs]](#manual-Int___natAbs) ([Int.tmod]](#manual-Int___tmod) a b) = [Int.natAbs]](#manual-Int___natAbs) a % [Int.natAbs]](#manual-Int___natAbs) b`, and when `b` does not divide `a`,
`[Int.tmod]](#manual-Int___tmod) a b` has the same sign as `a`.

This function is overridden by the compiler with an efficient implementation. This definition is the
logical model.

Examples:

- `(7 : [Int]](#manual-Int___ofNat)).[tmod]](#manual-Int___tmod) (0 : [Int]](#manual-Int___ofNat)) = 7`
- `(0 : [Int]](#manual-Int___ofNat)).[tmod]](#manual-Int___tmod) (7 : [Int]](#manual-Int___ofNat)) = 0`
- `(12 : [Int]](#manual-Int___ofNat)).[tmod]](#manual-Int___tmod) (6 : [Int]](#manual-Int___ofNat)) = 0`
- `(12 : [Int]](#manual-Int___ofNat)).[tmod]](#manual-Int___tmod) (-6 : [Int]](#manual-Int___ofNat)) = 0`
- `(-12 : [Int]](#manual-Int___ofNat)).[tmod]](#manual-Int___tmod) (6 : [Int]](#manual-Int___ofNat)) = 0`
- `(-12 : [Int]](#manual-Int___ofNat)).[tmod]](#manual-Int___tmod) (-6 : [Int]](#manual-Int___ofNat)) = 0`
- `(12 : [Int]](#manual-Int___ofNat)).[tmod]](#manual-Int___tmod) (7 : [Int]](#manual-Int___ofNat)) = 5`
- `(12 : [Int]](#manual-Int___ofNat)).[tmod]](#manual-Int___tmod) (-7 : [Int]](#manual-Int___ofNat)) = 5`
- `(-12 : [Int]](#manual-Int___ofNat)).[tmod]](#manual-Int___tmod) (7 : [Int]](#manual-Int___ofNat)) = -5`
- `(-12 : [Int]](#manual-Int___ofNat)).[tmod]](#manual-Int___tmod) (-7 : [Int]](#manual-Int___ofNat)) = -5`

def

```lean
[Int.bdiv]](#manual-Int___bdiv) (x : [Int]](#manual-Int___ofNat)) (m : [Nat]](#manual-Nat___zero)) : [Int]](#manual-Int___ofNat)



[Int.bdiv]](#manual-Int___bdiv) (x : [Int]](#manual-Int___ofNat)) (m : [Nat]](#manual-Nat___zero)) : [Int]](#manual-Int___ofNat)
```

Balanced division.

This returns the unique integer so that `b * ([Int.bdiv]](#manual-Int___bdiv) a b) + [Int.bmod]](#manual-Int___bmod) a b = a`.

Examples:

- `(7 : [Int]](#manual-Int___ofNat)).[bdiv]](#manual-Int___bdiv) 0 = 0`
- `(0 : [Int]](#manual-Int___ofNat)).[bdiv]](#manual-Int___bdiv) 7 = 0`
- `(12 : [Int]](#manual-Int___ofNat)).[bdiv]](#manual-Int___bdiv) 6 = 2`
- `(12 : [Int]](#manual-Int___ofNat)).[bdiv]](#manual-Int___bdiv) 7 = 2`
- `(12 : [Int]](#manual-Int___ofNat)).[bdiv]](#manual-Int___bdiv) 8 = 2`
- `(12 : [Int]](#manual-Int___ofNat)).[bdiv]](#manual-Int___bdiv) 9 = 1`
- `(-12 : [Int]](#manual-Int___ofNat)).[bdiv]](#manual-Int___bdiv) 6 = -2`
- `(-12 : [Int]](#manual-Int___ofNat)).[bdiv]](#manual-Int___bdiv) 7 = -2`
- `(-12 : [Int]](#manual-Int___ofNat)).[bdiv]](#manual-Int___bdiv) 8 = -1`
- `(-12 : [Int]](#manual-Int___ofNat)).[bdiv]](#manual-Int___bdiv) 9 = -1`

def

```lean
[Int.bmod]](#manual-Int___bmod) (x : [Int]](#manual-Int___ofNat)) (m : [Nat]](#manual-Nat___zero)) : [Int]](#manual-Int___ofNat)



[Int.bmod]](#manual-Int___bmod) (x : [Int]](#manual-Int___ofNat)) (m : [Nat]](#manual-Nat___zero)) : [Int]](#manual-Int___ofNat)
```

Balanced modulus.

This version of integer modulus uses the balanced rounding convention, which guarantees that
`-m / 2 ≤ Int.bmod x m < m/2` for `m ≠ 0` and `[Int.bmod]](#manual-Int___bmod) x m` is congruent to `x` modulo `m`.

If `m = 0`, then `[Int.bmod]](#manual-Int___bmod) x m = x`.

Examples:

- `(7 : [Int]](#manual-Int___ofNat)).[bmod]](#manual-Int___bmod) 0 = 7`
- `(0 : [Int]](#manual-Int___ofNat)).[bmod]](#manual-Int___bmod) 7 = 0`
- `(12 : [Int]](#manual-Int___ofNat)).[bmod]](#manual-Int___bmod) 6 = 0`
- `(12 : [Int]](#manual-Int___ofNat)).[bmod]](#manual-Int___bmod) 7 = -2`
- `(12 : [Int]](#manual-Int___ofNat)).[bmod]](#manual-Int___bmod) 8 = -4`
- `(12 : [Int]](#manual-Int___ofNat)).[bmod]](#manual-Int___bmod) 9 = 3`
- `(-12 : [Int]](#manual-Int___ofNat)).[bmod]](#manual-Int___bmod) 6 = 0`
- `(-12 : [Int]](#manual-Int___ofNat)).[bmod]](#manual-Int___bmod) 7 = 2`
- `(-12 : [Int]](#manual-Int___ofNat)).[bmod]](#manual-Int___bmod) 8 = -4`
- `(-12 : [Int]](#manual-Int___ofNat)).[bmod]](#manual-Int___bmod) 9 = -3`

def

```lean
[Int.fdiv]](#manual-Int___fdiv) : [Int]](#manual-Int___ofNat) → [Int]](#manual-Int___ofNat) → [Int]](#manual-Int___ofNat)



[Int.fdiv]](#manual-Int___fdiv) : [Int]](#manual-Int___ofNat) → [Int]](#manual-Int___ofNat) → [Int]](#manual-Int___ofNat)
```

Integer division using the F-rounding convention.

In the F-rounding convention (flooring division), `[Int.fdiv]](#manual-Int___fdiv) x y` satisfies `Int.fdiv x y = ⌊x / y⌋`
and `[Int.fmod]](#manual-Int___fmod)` is the unique function satisfying `[Int.fmod]](#manual-Int___fmod) x y + ([Int.fdiv]](#manual-Int___fdiv) x y) * y = x`.

Examples:

- `(7 : [Int]](#manual-Int___ofNat)).[fdiv]](#manual-Int___fdiv) (0 : [Int]](#manual-Int___ofNat)) = 0`
- `(0 : [Int]](#manual-Int___ofNat)).[fdiv]](#manual-Int___fdiv) (7 : [Int]](#manual-Int___ofNat)) = 0`
- `(12 : [Int]](#manual-Int___ofNat)).[fdiv]](#manual-Int___fdiv) (6 : [Int]](#manual-Int___ofNat)) = 2`
- `(12 : [Int]](#manual-Int___ofNat)).[fdiv]](#manual-Int___fdiv) (-6 : [Int]](#manual-Int___ofNat)) = -2`
- `(-12 : [Int]](#manual-Int___ofNat)).[fdiv]](#manual-Int___fdiv) (6 : [Int]](#manual-Int___ofNat)) = -2`
- `(-12 : [Int]](#manual-Int___ofNat)).[fdiv]](#manual-Int___fdiv) (-6 : [Int]](#manual-Int___ofNat)) = 2`
- `(12 : [Int]](#manual-Int___ofNat)).[fdiv]](#manual-Int___fdiv) (7 : [Int]](#manual-Int___ofNat)) = 1`
- `(12 : [Int]](#manual-Int___ofNat)).[fdiv]](#manual-Int___fdiv) (-7 : [Int]](#manual-Int___ofNat)) = -2`
- `(-12 : [Int]](#manual-Int___ofNat)).[fdiv]](#manual-Int___fdiv) (7 : [Int]](#manual-Int___ofNat)) = -2`
- `(-12 : [Int]](#manual-Int___ofNat)).[fdiv]](#manual-Int___fdiv) (-7 : [Int]](#manual-Int___ofNat)) = 1`

def

```lean
[Int.fmod]](#manual-Int___fmod) : [Int]](#manual-Int___ofNat) → [Int]](#manual-Int___ofNat) → [Int]](#manual-Int___ofNat)



[Int.fmod]](#manual-Int___fmod) : [Int]](#manual-Int___ofNat) → [Int]](#manual-Int___ofNat) → [Int]](#manual-Int___ofNat)
```

Integer modulus using the F-rounding convention.

In the F-rounding convention (flooring division), `[Int.fdiv]](#manual-Int___fdiv) x y` satisfies `Int.fdiv x y = ⌊x / y⌋`
and `[Int.fmod]](#manual-Int___fmod)` is the unique function satisfying `[Int.fmod]](#manual-Int___fmod) x y + ([Int.fdiv]](#manual-Int___fdiv) x y) * y = x`.

Examples:

- `(7 : [Int]](#manual-Int___ofNat)).[fmod]](#manual-Int___fmod) (0 : [Int]](#manual-Int___ofNat)) = 7`
- `(0 : [Int]](#manual-Int___ofNat)).[fmod]](#manual-Int___fmod) (7 : [Int]](#manual-Int___ofNat)) = 0`
- `(12 : [Int]](#manual-Int___ofNat)).[fmod]](#manual-Int___fmod) (6 : [Int]](#manual-Int___ofNat)) = 0`
- `(12 : [Int]](#manual-Int___ofNat)).[fmod]](#manual-Int___fmod) (-6 : [Int]](#manual-Int___ofNat)) = 0`
- `(-12 : [Int]](#manual-Int___ofNat)).[fmod]](#manual-Int___fmod) (6 : [Int]](#manual-Int___ofNat)) = 0`
- `(-12 : [Int]](#manual-Int___ofNat)).[fmod]](#manual-Int___fmod) (-6 : [Int]](#manual-Int___ofNat)) = 0`
- `(12 : [Int]](#manual-Int___ofNat)).[fmod]](#manual-Int___fmod) (7 : [Int]](#manual-Int___ofNat)) = 5`
- `(12 : [Int]](#manual-Int___ofNat)).[fmod]](#manual-Int___fmod) (-7 : [Int]](#manual-Int___ofNat)) = -2`
- `(-12 : [Int]](#manual-Int___ofNat)).[fmod]](#manual-Int___fmod) (7 : [Int]](#manual-Int___ofNat)) = 2`
- `(-12 : [Int]](#manual-Int___ofNat)).[fmod]](#manual-Int___fmod) (-7 : [Int]](#manual-Int___ofNat)) = -5`

#### 20.2.4.4. Bitwise Operators {#manual-The-Lean-Language-Reference--Basic-Types--Integers--API-Reference--Bitwise-Operators}

Bitwise operators on `[Int]](#manual-Int___ofNat)` can be understood as bitwise operators on an infinite stream of bits that are the twos-complement representation of integers.

def

```lean
[Int.not]](#manual-Int___not) : [Int]](#manual-Int___ofNat) → [Int]](#manual-Int___ofNat)



[Int.not]](#manual-Int___not) : [Int]](#manual-Int___ofNat) → [Int]](#manual-Int___ofNat)
```

Bitwise not, usually accessed via the `~~~` prefix operator.

Interprets the integer as an infinite sequence of bits in two's complement and complements each bit.

Examples:

- `~~~(0 : [Int]](#manual-Int___ofNat)) = -1`
- `~~~(1 : [Int]](#manual-Int___ofNat)) = -2`
- `~~~(-1 : [Int]](#manual-Int___ofNat)) = 0`

def

```lean
[Int.shiftRight]](#manual-Int___shiftRight) : [Int]](#manual-Int___ofNat) → [Nat]](#manual-Nat___zero) → [Int]](#manual-Int___ofNat)



[Int.shiftRight]](#manual-Int___shiftRight) : [Int]](#manual-Int___ofNat) → [Nat]](#manual-Nat___zero) → [Int]](#manual-Int___ofNat)
```

Bitwise right shift, usually accessed via the `>>>` operator.

Interprets the integer as an infinite sequence of bits in two's complement and shifts the value to
the right.

Examples:

- `( 0b0111 : [Int]](#manual-Int___ofNat)) >>> 1 = 0b0011`
- `( 0b1000 : [Int]](#manual-Int___ofNat)) >>> 1 = 0b0100`
- `(-0b1000 : [Int]](#manual-Int___ofNat)) >>> 1 = -0b0100`
- `(-0b0111 : [Int]](#manual-Int___ofNat)) >>> 1 = -0b0100`

#### 20.2.4.5. Comparisons {#manual-The-Lean-Language-Reference--Basic-Types--Integers--API-Reference--Comparisons}

Equality and inequality tests on `[Int]](#manual-Int___ofNat)` are typically performed using the decidability of its equality and ordering relations or using the `[BEq]](#manual-BEq___mk) [Int]](#manual-Int___ofNat)` and `[Ord]](#manual-Ord___mk) [Int]](#manual-Int___ofNat)` instances.

def

```lean
[Int.le]](#manual-Int___le) (a b : [Int]](#manual-Int___ofNat)) : Prop



[Int.le]](#manual-Int___le) (a b : [Int]](#manual-Int___ofNat)) : Prop
```

Non-strict inequality of integers, usually accessed via the `≤` operator.

`a ≤ b` is defined as `b - a ≥ 0`, using `Int.NonNeg`.

def

```lean
[Int.lt]](#manual-Int___lt) (a b : [Int]](#manual-Int___ofNat)) : Prop



[Int.lt]](#manual-Int___lt) (a b : [Int]](#manual-Int___ofNat)) : Prop
```

Strict inequality of integers, usually accessed via the `<` operator.

`a < b` when `a + 1 ≤ b`.

def

```lean
[Int.decEq]](#manual-Int___decEq) (a b : [Int]](#manual-Int___ofNat)) : [Decidable]](#manual-Decidable___isFalse) [(]](#manual-Eq___refl)a [=]](#manual-Eq___refl) b[)]](#manual-Eq___refl)



[Int.decEq]](#manual-Int___decEq) (a b : [Int]](#manual-Int___ofNat)) : [Decidable]](#manual-Decidable___isFalse) [(]](#manual-Eq___refl)a [=]](#manual-Eq___refl) b[)]](#manual-Eq___refl)
```

Decides whether two integers are equal. Usually accessed via the `[DecidableEq]](#manual-DecidableEq) [Int]](#manual-Int___ofNat)` instance.

This function is overridden by the compiler with an efficient implementation. This definition is the
logical model.

Examples:

- `show (7 : [Int]](#manual-Int___ofNat)) = (3 : [Int]](#manual-Int___ofNat)) + (4 : [Int]](#manual-Int___ofNat)) by [decide]](#manual-decide)`
- `[if]](#manual-termIfThenElse) (6 : [Int]](#manual-Int___ofNat)) = (3 : [Int]](#manual-Int___ofNat)) * (2 : [Int]](#manual-Int___ofNat)) [then]](#manual-termIfThenElse) "yes" [else]](#manual-termIfThenElse) "no" = "yes"`
- `(¬ (6 : [Int]](#manual-Int___ofNat)) = (3 : [Int]](#manual-Int___ofNat))) = [true](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)`

---



## Basic Types — 20.3. Finite Natural Numbers {#manual-basic-types-203-finite-natural-numbers}

> 📄 Source: https://lean-lang.org/doc/reference/latest/Basic-Types/Finite-Natural-Numbers/

For any [natural number]](#manual---tech-term-natural-numbers) `n`, the `[Fin]](#manual-Fin___mk) n` is a type that contains all the natural numbers that are strictly less than `n`.
In other words, `[Fin]](#manual-Fin___mk) n` has exactly `n` elements.
It can be used to represent the valid indices into a list or array, or it can serve as a canonical `n`-element type.

structure

```lean
[Fin]](#manual-Fin___mk) (n : [Nat]](#manual-Nat___zero)) : Type



[Fin]](#manual-Fin___mk) (n : [Nat]](#manual-Nat___zero)) : Type
```

Natural numbers less than some upper bound.

In particular, a `[Fin]](#manual-Fin___mk) n` is a natural number `i` with the constraint that `i < n`. It is the
canonical type with `n` elements.

Constructor

```lean
[Fin.mk]](#manual-Fin___mk)
```

Creates a `[Fin]](#manual-Fin___mk) n` from `i : [Nat]](#manual-Nat___zero)` and a proof that `i < n`.

Fields

```lean
val : [Nat]](#manual-Nat___zero)
```

The number that is strictly less than `n`.

`[Fin.val]](#manual-Fin___mk)` is a coercion, so any `[Fin]](#manual-Fin___mk) n` can be used in a position where a `[Nat]](#manual-Nat___zero)` is expected.

```lean
isLt : ↑self [<]](#manual-LT___mk) n
```

The number `val` is strictly less than the bound `n`.

`[Fin]](#manual-Fin___mk)` is closely related to `[UInt8](https://lean-lang.org/doc/reference/latest/Basic-Types/Fixed-Precision-Integers/#UInt8___ofBitVec)`, `[UInt16](https://lean-lang.org/doc/reference/latest/Basic-Types/Fixed-Precision-Integers/#UInt16___ofBitVec)`, `[UInt32](https://lean-lang.org/doc/reference/latest/Basic-Types/Fixed-Precision-Integers/#UInt32___ofBitVec)`, `[UInt64](https://lean-lang.org/doc/reference/latest/Basic-Types/Fixed-Precision-Integers/#UInt64___ofBitVec)`, and `[USize](https://lean-lang.org/doc/reference/latest/Basic-Types/Fixed-Precision-Integers/#USize___ofBitVec)`, which also represent finite non-negative integral types.
However, these types are backed by bitvectors rather than by natural numbers, and they have fixed bounds.
`[Fin]](#manual-Fin___mk)` is comparatively more flexible, but also less convenient for low-level reasoning.
In particular, using bitvectors rather than proofs that a number is less than some power of two avoids needing to take care to avoid evaluating the concrete bound.

### 20.3.1. Run-Time Characteristics {#manual-The-Lean-Language-Reference--Basic-Types--Finite-Natural-Numbers--Run-Time-Characteristics}

Because `[Fin]](#manual-Fin___mk) n` is a structure in which only a single field is not a proof, it is a [trivial wrapper]](#manual-inductive-types-trivial-wrappers).
This means that it is represented identically to the underlying natural number in compiled code.

### 20.3.2. Coercions and Literals {#manual-The-Lean-Language-Reference--Basic-Types--Finite-Natural-Numbers--Coercions-and-Literals}

There is a [coercion]](#manual---tech-term-coercion) from `[Fin]](#manual-Fin___mk) n` to `[Nat]](#manual-Nat___zero)` that discards the proof that the number is less than the bound.
In particular, this coercion is precisely the projection `[Fin.val]](#manual-Fin___mk)`.
One consequence of this is that uses of `[Fin.val]](#manual-Fin___mk)` are displayed as coercions rather than explicit projections in proof states.

**Example: Coercing from Fin to Nat**

A `[Fin]](#manual-Fin___mk) n` can be used where a `[Nat]](#manual-Nat___zero)` is expected:

```lean
[#eval]](#manual-Lean___Parser___Command___eval) let one : [Fin]](#manual-Fin___mk) 3 := ⟨1, byn:[Nat]](#manual-Nat___zero)⊢ 1 [<]](#manual-LT___mk) 3 [omega]](#manual-omega)All goals completed! 🐙⟩; (one : [Nat]](#manual-Nat___zero))
```

```lean
1
```

Uses of `[Fin.val]](#manual-Fin___mk)` show up as coercions in proof states:

n:[Nat]](#manual-Nat___zero)i:[Fin]](#manual-Fin___mk) n⊢ ↑i [<]](#manual-LT___mk) n

Natural number literals may be used for `[Fin]](#manual-Fin___mk)` types, implemented as usual via an `[OfNat]](#manual-OfNat___mk)` instance.
The `[OfNat]](#manual-OfNat___mk)` instance for `[Fin]](#manual-Fin___mk) n` requires that the upper bound `n` is not zero, but does not check that the literal is less than `n`.
If the literal is larger than the type can represent, the remainder when dividing it by `n` is used.

**Example: Numeric Literals for Fin**

If `n > 0`, then natural number literals can be used for `[Fin]](#manual-Fin___mk) n`:

```lean
example : [Fin]](#manual-Fin___mk) 5 := 3
example : [Fin]](#manual-Fin___mk) 20 := 19
```

When the literal is greater than or equal to `n`, the remainder when dividing by `n` is used:

```lean
[#eval]](#manual-Lean___Parser___Command___eval) (5 : [Fin]](#manual-Fin___mk) 3)
```

```lean
2
```

```lean
[#eval]](#manual-Lean___Parser___Command___eval) ([0, 1, 2, 3, 4, 5, 6] : [List](https://lean-lang.org/doc/reference/latest/Basic-Types/Linked-Lists/#List___nil) ([Fin]](#manual-Fin___mk) 3))
```

```lean
[[](https://lean-lang.org/doc/reference/latest/Basic-Types/Linked-Lists/#List___nil)0[,](https://lean-lang.org/doc/reference/latest/Basic-Types/Linked-Lists/#List___nil) 1[,](https://lean-lang.org/doc/reference/latest/Basic-Types/Linked-Lists/#List___nil) 2[,](https://lean-lang.org/doc/reference/latest/Basic-Types/Linked-Lists/#List___nil) 0[,](https://lean-lang.org/doc/reference/latest/Basic-Types/Linked-Lists/#List___nil) 1[,](https://lean-lang.org/doc/reference/latest/Basic-Types/Linked-Lists/#List___nil) 2[,](https://lean-lang.org/doc/reference/latest/Basic-Types/Linked-Lists/#List___nil) 0[]](https://lean-lang.org/doc/reference/latest/Basic-Types/Linked-Lists/#List___nil)
```

If Lean can't synthesize an instance of `[NeZero]](#manual-NeZero___mk) n`, then there is no `[OfNat]](#manual-OfNat___mk) ([Fin]](#manual-Fin___mk) n)` instance:

```lean
example : [Fin]](#manual-Fin___mk) 0 := 0
```

```lean
failed to synthesize instance of type class
  [OfNat]](#manual-OfNat___mk) ([Fin]](#manual-Fin___mk) 0) 0
numerals are polymorphic in Lean, but the numeral `0` cannot be used in a context where the expected type is
  [Fin]](#manual-Fin___mk) 0
due to the absence of the instance above

Hint: Type class instance resolution failures can be inspected with the `set_option trace.Meta.synthInstance true` command.
```

```lean
example (k : [Nat]](#manual-Nat___zero)) : [Fin]](#manual-Fin___mk) k := 0
```

```lean
failed to synthesize instance of type class
  [OfNat]](#manual-OfNat___mk) ([Fin]](#manual-Fin___mk) k) 0
numerals are polymorphic in Lean, but the numeral `0` cannot be used in a context where the expected type is
  [Fin]](#manual-Fin___mk) k
due to the absence of the instance above

Hint: Type class instance resolution failures can be inspected with the `set_option trace.Meta.synthInstance true` command.
```

### 20.3.3. API Reference {#manual-The-Lean-Language-Reference--Basic-Types--Finite-Natural-Numbers--API-Reference}

#### 20.3.3.1. Construction {#manual-The-Lean-Language-Reference--Basic-Types--Finite-Natural-Numbers--API-Reference--Construction}

def

```lean
[Fin.last]](#manual-Fin___last) (n : [Nat]](#manual-Nat___zero)) : [Fin]](#manual-Fin___mk) [(]](#manual-HAdd___mk)n [+]](#manual-HAdd___mk) 1[)]](#manual-HAdd___mk)



[Fin.last]](#manual-Fin___last) (n : [Nat]](#manual-Nat___zero)) : [Fin]](#manual-Fin___mk) [(]](#manual-HAdd___mk)n [+]](#manual-HAdd___mk) 1[)]](#manual-HAdd___mk)
```

The greatest value of `[Fin]](#manual-Fin___mk) (n+1)`, namely `n`.

Examples:

- `[Fin.last]](#manual-Fin___last) 4 = (4 : [Fin]](#manual-Fin___mk) 5)`
- `([Fin.last]](#manual-Fin___last) 0).[val]](#manual-Fin___mk) = (0 : [Nat]](#manual-Nat___zero))`

def

```lean
[Fin.succ]](#manual-Fin___succ) {n : [Nat]](#manual-Nat___zero)} : [Fin]](#manual-Fin___mk) n → [Fin]](#manual-Fin___mk) [(]](#manual-HAdd___mk)n [+]](#manual-HAdd___mk) 1[)]](#manual-HAdd___mk)



[Fin.succ]](#manual-Fin___succ) {n : [Nat]](#manual-Nat___zero)} : [Fin]](#manual-Fin___mk) n → [Fin]](#manual-Fin___mk) [(]](#manual-HAdd___mk)n [+]](#manual-HAdd___mk) 1[)]](#manual-HAdd___mk)
```

The successor, with an increased bound.

This differs from adding `1`, which instead wraps around.

Examples:

- `(2 : [Fin]](#manual-Fin___mk) 3).[succ]](#manual-Fin___succ) = (3 : [Fin]](#manual-Fin___mk) 4)`
- `(2 : [Fin]](#manual-Fin___mk) 3) + 1 = (0 : [Fin]](#manual-Fin___mk) 3)`

def

```lean
[Fin.pred]](#manual-Fin___pred) {n : [Nat]](#manual-Nat___zero)} (i : [Fin]](#manual-Fin___mk) [(]](#manual-HAdd___mk)n [+]](#manual-HAdd___mk) 1[)]](#manual-HAdd___mk)) (h : i ≠ 0) : [Fin]](#manual-Fin___mk) n



[Fin.pred]](#manual-Fin___pred) {n : [Nat]](#manual-Nat___zero)} (i : [Fin]](#manual-Fin___mk) [(]](#manual-HAdd___mk)n [+]](#manual-HAdd___mk) 1[)]](#manual-HAdd___mk))
  (h : i ≠ 0) : [Fin]](#manual-Fin___mk) n
```

The predecessor of a non-zero element of `[Fin]](#manual-Fin___mk) (n+1)`, with the bound decreased.

Examples:

- `(4 : [Fin]](#manual-Fin___mk) 8).[pred]](#manual-Fin___pred) (by [decide]](#manual-decide)) = (3 : [Fin]](#manual-Fin___mk) 7)`
- `(1 : [Fin]](#manual-Fin___mk) 2).[pred]](#manual-Fin___pred) (by [decide]](#manual-decide)) = (0 : [Fin]](#manual-Fin___mk) 1)`

#### 20.3.3.2. Arithmetic {#manual-The-Lean-Language-Reference--Basic-Types--Finite-Natural-Numbers--API-Reference--Arithmetic}

Typically, arithmetic operations on `[Fin]](#manual-Fin___mk)` should be accessed using Lean's overloaded arithmetic notation, particularly via the instances `[Add]](#manual-Add___mk) ([Fin]](#manual-Fin___mk) n)`, `[Sub]](#manual-Sub___mk) ([Fin]](#manual-Fin___mk) n)`, `[Mul]](#manual-Mul___mk) ([Fin]](#manual-Fin___mk) n)`, `[Div]](#manual-Div___mk) ([Fin]](#manual-Fin___mk) n)`, and `[Mod]](#manual-Mod___mk) ([Fin]](#manual-Fin___mk) n)`.
Heterogeneous operators such as `[Fin.natAdd]](#manual-Fin___natAdd)` do not have corresponding heterogeneous instances (e.g. `[HAdd]](#manual-HAdd___mk)`) to avoid confusing type inference behavior.

def

```lean
[Fin.add]](#manual-Fin___add) {n : [Nat]](#manual-Nat___zero)} : [Fin]](#manual-Fin___mk) n → [Fin]](#manual-Fin___mk) n → [Fin]](#manual-Fin___mk) n



[Fin.add]](#manual-Fin___add) {n : [Nat]](#manual-Nat___zero)} : [Fin]](#manual-Fin___mk) n → [Fin]](#manual-Fin___mk) n → [Fin]](#manual-Fin___mk) n
```

Addition modulo `n`, usually invoked via the `+` operator.

Examples:

- `(2 : [Fin]](#manual-Fin___mk) 8) + (2 : [Fin]](#manual-Fin___mk) 8) = (4 : [Fin]](#manual-Fin___mk) 8)`
- `(2 : [Fin]](#manual-Fin___mk) 3) + (2 : [Fin]](#manual-Fin___mk) 3) = (1 : [Fin]](#manual-Fin___mk) 3)`

def

```lean
[Fin.natAdd]](#manual-Fin___natAdd) {m : [Nat]](#manual-Nat___zero)} (n : [Nat]](#manual-Nat___zero)) (i : [Fin]](#manual-Fin___mk) m) : [Fin]](#manual-Fin___mk) [(]](#manual-HAdd___mk)n [+]](#manual-HAdd___mk) m[)]](#manual-HAdd___mk)



[Fin.natAdd]](#manual-Fin___natAdd) {m : [Nat]](#manual-Nat___zero)} (n : [Nat]](#manual-Nat___zero))
  (i : [Fin]](#manual-Fin___mk) m) : [Fin]](#manual-Fin___mk) [(]](#manual-HAdd___mk)n [+]](#manual-HAdd___mk) m[)]](#manual-HAdd___mk)
```

Adds a natural number to a `[Fin]](#manual-Fin___mk)`, increasing the bound.

This is a generalization of `[Fin.succ]](#manual-Fin___succ)`.

`[Fin.addNat]](#manual-Fin___addNat)` is a version of this function that takes its `[Nat]](#manual-Nat___zero)` parameter second.

Examples:

- `[Fin.natAdd]](#manual-Fin___natAdd) 3 (5 : [Fin]](#manual-Fin___mk) 8) = (8 : [Fin]](#manual-Fin___mk) 11)`
- `[Fin.natAdd]](#manual-Fin___natAdd) 1 (0 : [Fin]](#manual-Fin___mk) 8) = (1 : [Fin]](#manual-Fin___mk) 9)`
- `[Fin.natAdd]](#manual-Fin___natAdd) 1 (2 : [Fin]](#manual-Fin___mk) 8) = (3 : [Fin]](#manual-Fin___mk) 9)`

def

```lean
[Fin.addNat]](#manual-Fin___addNat) {n : [Nat]](#manual-Nat___zero)} (i : [Fin]](#manual-Fin___mk) n) (m : [Nat]](#manual-Nat___zero)) : [Fin]](#manual-Fin___mk) [(]](#manual-HAdd___mk)n [+]](#manual-HAdd___mk) m[)]](#manual-HAdd___mk)



[Fin.addNat]](#manual-Fin___addNat) {n : [Nat]](#manual-Nat___zero)} (i : [Fin]](#manual-Fin___mk) n)
  (m : [Nat]](#manual-Nat___zero)) : [Fin]](#manual-Fin___mk) [(]](#manual-HAdd___mk)n [+]](#manual-HAdd___mk) m[)]](#manual-HAdd___mk)
```

Adds a natural number to a `[Fin]](#manual-Fin___mk)`, increasing the bound.

This is a generalization of `[Fin.succ]](#manual-Fin___succ)`.

`[Fin.natAdd]](#manual-Fin___natAdd)` is a version of this function that takes its `[Nat]](#manual-Nat___zero)` parameter first.

Examples:

- `[Fin.addNat]](#manual-Fin___addNat) (5 : [Fin]](#manual-Fin___mk) 8) 3 = (8 : [Fin]](#manual-Fin___mk) 11)`
- `[Fin.addNat]](#manual-Fin___addNat) (0 : [Fin]](#manual-Fin___mk) 8) 1 = (1 : [Fin]](#manual-Fin___mk) 9)`
- `[Fin.addNat]](#manual-Fin___addNat) (1 : [Fin]](#manual-Fin___mk) 8) 2 = (3 : [Fin]](#manual-Fin___mk) 10)`

def

```lean
[Fin.mul]](#manual-Fin___mul) {n : [Nat]](#manual-Nat___zero)} : [Fin]](#manual-Fin___mk) n → [Fin]](#manual-Fin___mk) n → [Fin]](#manual-Fin___mk) n



[Fin.mul]](#manual-Fin___mul) {n : [Nat]](#manual-Nat___zero)} : [Fin]](#manual-Fin___mk) n → [Fin]](#manual-Fin___mk) n → [Fin]](#manual-Fin___mk) n
```

Multiplication modulo `n`, usually invoked via the `*` operator.

Examples:

- `(2 : [Fin]](#manual-Fin___mk) 10) * (2 : [Fin]](#manual-Fin___mk) 10) = (4 : [Fin]](#manual-Fin___mk) 10)`
- `(2 : [Fin]](#manual-Fin___mk) 10) * (7 : [Fin]](#manual-Fin___mk) 10) = (4 : [Fin]](#manual-Fin___mk) 10)`
- `(3 : [Fin]](#manual-Fin___mk) 10) * (7 : [Fin]](#manual-Fin___mk) 10) = (1 : [Fin]](#manual-Fin___mk) 10)`

def

```lean
[Fin.sub]](#manual-Fin___sub) {n : [Nat]](#manual-Nat___zero)} : [Fin]](#manual-Fin___mk) n → [Fin]](#manual-Fin___mk) n → [Fin]](#manual-Fin___mk) n



[Fin.sub]](#manual-Fin___sub) {n : [Nat]](#manual-Nat___zero)} : [Fin]](#manual-Fin___mk) n → [Fin]](#manual-Fin___mk) n → [Fin]](#manual-Fin___mk) n
```

Subtraction modulo `n`, usually invoked via the `-` operator.

Examples:

- `(5 : [Fin]](#manual-Fin___mk) 11) - (3 : [Fin]](#manual-Fin___mk) 11) = (2 : [Fin]](#manual-Fin___mk) 11)`
- `(3 : [Fin]](#manual-Fin___mk) 11) - (5 : [Fin]](#manual-Fin___mk) 11) = (9 : [Fin]](#manual-Fin___mk) 11)`

def

```lean
[Fin.subNat]](#manual-Fin___subNat) {n : [Nat]](#manual-Nat___zero)} (m : [Nat]](#manual-Nat___zero)) (i : [Fin]](#manual-Fin___mk) [(]](#manual-HAdd___mk)n [+]](#manual-HAdd___mk) m[)]](#manual-HAdd___mk)) (h : m [≤]](#manual-LE___mk) ↑i) : [Fin]](#manual-Fin___mk) n



[Fin.subNat]](#manual-Fin___subNat) {n : [Nat]](#manual-Nat___zero)} (m : [Nat]](#manual-Nat___zero))
  (i : [Fin]](#manual-Fin___mk) [(]](#manual-HAdd___mk)n [+]](#manual-HAdd___mk) m[)]](#manual-HAdd___mk)) (h : m [≤]](#manual-LE___mk) ↑i) : [Fin]](#manual-Fin___mk) n
```

Subtraction of a natural number from a `[Fin]](#manual-Fin___mk)`, with the bound narrowed.

This is a generalization of `[Fin.pred]](#manual-Fin___pred)`. It is guaranteed to not underflow or wrap around.

Examples:

- `(5 : [Fin]](#manual-Fin___mk) 9).[subNat]](#manual-Fin___subNat) 2 (by [decide]](#manual-decide)) = (3 : [Fin]](#manual-Fin___mk) 7)`
- `(5 : [Fin]](#manual-Fin___mk) 9).[subNat]](#manual-Fin___subNat) 0 (by [decide]](#manual-decide)) = (5 : [Fin]](#manual-Fin___mk) 9)`
- `(3 : [Fin]](#manual-Fin___mk) 9).[subNat]](#manual-Fin___subNat) 3 (by [decide]](#manual-decide)) = (0 : [Fin]](#manual-Fin___mk) 6)`

def

```lean
[Fin.div]](#manual-Fin___div) {n : [Nat]](#manual-Nat___zero)} : [Fin]](#manual-Fin___mk) n → [Fin]](#manual-Fin___mk) n → [Fin]](#manual-Fin___mk) n



[Fin.div]](#manual-Fin___div) {n : [Nat]](#manual-Nat___zero)} : [Fin]](#manual-Fin___mk) n → [Fin]](#manual-Fin___mk) n → [Fin]](#manual-Fin___mk) n
```

Division of bounded numbers, usually invoked via the `/` operator.

The resulting value is that computed by the `/` operator on `[Nat]](#manual-Nat___zero)`. In particular, the result of
division by `0` is `0`.

Examples:

- `(5 : [Fin]](#manual-Fin___mk) 10) / (2 : [Fin]](#manual-Fin___mk) 10) = (2 : [Fin]](#manual-Fin___mk) 10)`
- `(5 : [Fin]](#manual-Fin___mk) 10) / (0 : [Fin]](#manual-Fin___mk) 10) = (0 : [Fin]](#manual-Fin___mk) 10)`
- `(5 : [Fin]](#manual-Fin___mk) 10) / (7 : [Fin]](#manual-Fin___mk) 10) = (0 : [Fin]](#manual-Fin___mk) 10)`

def

```lean
[Fin.mod]](#manual-Fin___mod) {n : [Nat]](#manual-Nat___zero)} : [Fin]](#manual-Fin___mk) n → [Fin]](#manual-Fin___mk) n → [Fin]](#manual-Fin___mk) n



[Fin.mod]](#manual-Fin___mod) {n : [Nat]](#manual-Nat___zero)} : [Fin]](#manual-Fin___mk) n → [Fin]](#manual-Fin___mk) n → [Fin]](#manual-Fin___mk) n
```

Modulus of bounded numbers, usually invoked via the `%` operator.

The resulting value is that computed by the `%` operator on `[Nat]](#manual-Nat___zero)`.

def

```lean
[Fin.modn]](#manual-Fin___modn) {n : [Nat]](#manual-Nat___zero)} : [Fin]](#manual-Fin___mk) n → [Nat]](#manual-Nat___zero) → [Fin]](#manual-Fin___mk) n



[Fin.modn]](#manual-Fin___modn) {n : [Nat]](#manual-Nat___zero)} : [Fin]](#manual-Fin___mk) n → [Nat]](#manual-Nat___zero) → [Fin]](#manual-Fin___mk) n
```

Modulus of bounded numbers with respect to a `[Nat]](#manual-Nat___zero)`.

The resulting value is that computed by the `%` operator on `[Nat]](#manual-Nat___zero)`.

def

```lean
[Fin.log2]](#manual-Fin___log2) {m : [Nat]](#manual-Nat___zero)} (n : [Fin]](#manual-Fin___mk) m) : [Fin]](#manual-Fin___mk) m



[Fin.log2]](#manual-Fin___log2) {m : [Nat]](#manual-Nat___zero)} (n : [Fin]](#manual-Fin___mk) m) : [Fin]](#manual-Fin___mk) m
```

Logarithm base 2 for bounded numbers.

The resulting value is the same as that computed by `[Nat.log2]](#manual-Nat___log2)`. In particular, the result for `0` is
`0`.

Examples:

- `(8 : [Fin]](#manual-Fin___mk) 10).[log2]](#manual-Fin___log2) = (3 : [Fin]](#manual-Fin___mk) 10)`
- `(7 : [Fin]](#manual-Fin___mk) 10).[log2]](#manual-Fin___log2) = (2 : [Fin]](#manual-Fin___mk) 10)`
- `(4 : [Fin]](#manual-Fin___mk) 10).[log2]](#manual-Fin___log2) = (2 : [Fin]](#manual-Fin___mk) 10)`
- `(3 : [Fin]](#manual-Fin___mk) 10).[log2]](#manual-Fin___log2) = (1 : [Fin]](#manual-Fin___mk) 10)`
- `(1 : [Fin]](#manual-Fin___mk) 10).[log2]](#manual-Fin___log2) = (0 : [Fin]](#manual-Fin___mk) 10)`
- `(0 : [Fin]](#manual-Fin___mk) 10).[log2]](#manual-Fin___log2) = (0 : [Fin]](#manual-Fin___mk) 10)`

#### 20.3.3.3. Bitwise Operations {#manual-The-Lean-Language-Reference--Basic-Types--Finite-Natural-Numbers--API-Reference--Bitwise-Operations}

def

```lean
[Fin.shiftLeft]](#manual-Fin___shiftLeft) {n : [Nat]](#manual-Nat___zero)} : [Fin]](#manual-Fin___mk) n → [Fin]](#manual-Fin___mk) n → [Fin]](#manual-Fin___mk) n



[Fin.shiftLeft]](#manual-Fin___shiftLeft) {n : [Nat]](#manual-Nat___zero)} :
  [Fin]](#manual-Fin___mk) n → [Fin]](#manual-Fin___mk) n → [Fin]](#manual-Fin___mk) n
```

Bitwise left shift of bounded numbers, with wraparound on overflow.

Examples:

- `(1 : [Fin]](#manual-Fin___mk) 10) <<< (1 : [Fin]](#manual-Fin___mk) 10) = (2 : [Fin]](#manual-Fin___mk) 10)`
- `(1 : [Fin]](#manual-Fin___mk) 10) <<< (3 : [Fin]](#manual-Fin___mk) 10) = (8 : [Fin]](#manual-Fin___mk) 10)`
- `(1 : [Fin]](#manual-Fin___mk) 10) <<< (4 : [Fin]](#manual-Fin___mk) 10) = (6 : [Fin]](#manual-Fin___mk) 10)`

def

```lean
[Fin.shiftRight]](#manual-Fin___shiftRight) {n : [Nat]](#manual-Nat___zero)} : [Fin]](#manual-Fin___mk) n → [Fin]](#manual-Fin___mk) n → [Fin]](#manual-Fin___mk) n



[Fin.shiftRight]](#manual-Fin___shiftRight) {n : [Nat]](#manual-Nat___zero)} :
  [Fin]](#manual-Fin___mk) n → [Fin]](#manual-Fin___mk) n → [Fin]](#manual-Fin___mk) n
```

Bitwise right shift of bounded numbers.

This operator corresponds to logical rather than arithmetic bit shifting. The new bits are always
`0`.

Examples:

- `(15 : [Fin]](#manual-Fin___mk) 16) >>> (1 : [Fin]](#manual-Fin___mk) 16) = (7 : [Fin]](#manual-Fin___mk) 16)`
- `(15 : [Fin]](#manual-Fin___mk) 16) >>> (2 : [Fin]](#manual-Fin___mk) 16) = (3 : [Fin]](#manual-Fin___mk) 16)`
- `(15 : [Fin]](#manual-Fin___mk) 17) >>> (2 : [Fin]](#manual-Fin___mk) 17) = (3 : [Fin]](#manual-Fin___mk) 17)`

def

```lean
[Fin.land]](#manual-Fin___land) {n : [Nat]](#manual-Nat___zero)} : [Fin]](#manual-Fin___mk) n → [Fin]](#manual-Fin___mk) n → [Fin]](#manual-Fin___mk) n



[Fin.land]](#manual-Fin___land) {n : [Nat]](#manual-Nat___zero)} : [Fin]](#manual-Fin___mk) n → [Fin]](#manual-Fin___mk) n → [Fin]](#manual-Fin___mk) n
```

Bitwise and.

def

```lean
[Fin.lor]](#manual-Fin___lor) {n : [Nat]](#manual-Nat___zero)} : [Fin]](#manual-Fin___mk) n → [Fin]](#manual-Fin___mk) n → [Fin]](#manual-Fin___mk) n



[Fin.lor]](#manual-Fin___lor) {n : [Nat]](#manual-Nat___zero)} : [Fin]](#manual-Fin___mk) n → [Fin]](#manual-Fin___mk) n → [Fin]](#manual-Fin___mk) n
```

Bitwise or.

def

```lean
[Fin.xor]](#manual-Fin___xor) {n : [Nat]](#manual-Nat___zero)} : [Fin]](#manual-Fin___mk) n → [Fin]](#manual-Fin___mk) n → [Fin]](#manual-Fin___mk) n



[Fin.xor]](#manual-Fin___xor) {n : [Nat]](#manual-Nat___zero)} : [Fin]](#manual-Fin___mk) n → [Fin]](#manual-Fin___mk) n → [Fin]](#manual-Fin___mk) n
```

Bitwise xor (“exclusive or”).

#### 20.3.3.4. Conversions {#manual-The-Lean-Language-Reference--Basic-Types--Finite-Natural-Numbers--API-Reference--Conversions}

def

```lean
[Fin.toNat]](#manual-Fin___toNat) {n : [Nat]](#manual-Nat___zero)} (i : [Fin]](#manual-Fin___mk) n) : [Nat]](#manual-Nat___zero)



[Fin.toNat]](#manual-Fin___toNat) {n : [Nat]](#manual-Nat___zero)} (i : [Fin]](#manual-Fin___mk) n) : [Nat]](#manual-Nat___zero)
```

Extracts the underlying `[Nat]](#manual-Nat___zero)` value.

This function is a synonym for `[Fin.val]](#manual-Fin___mk)`, which is the simp normal form. `[Fin.val]](#manual-Fin___mk)` is also a
coercion, so values of type `[Fin]](#manual-Fin___mk) n` are automatically converted to `[Nat]](#manual-Nat___zero)`s as needed.

def

```lean
[Fin.ofNat]](#manual-Fin___ofNat) (n : [Nat]](#manual-Nat___zero)) [[NeZero]](#manual-NeZero___mk) n] (a : [Nat]](#manual-Nat___zero)) : [Fin]](#manual-Fin___mk) n



[Fin.ofNat]](#manual-Fin___ofNat) (n : [Nat]](#manual-Nat___zero)) [[NeZero]](#manual-NeZero___mk) n] (a : [Nat]](#manual-Nat___zero)) :
  [Fin]](#manual-Fin___mk) n
```

Returns `a` modulo `n` as a `[Fin]](#manual-Fin___mk) n`.

The assumption `[NeZero]](#manual-NeZero___mk) n` ensures that `[Fin]](#manual-Fin___mk) n` is nonempty.

def

```lean
[Fin.cast]](#manual-Fin___cast) {n m : [Nat]](#manual-Nat___zero)} (eq : n [=]](#manual-Eq___refl) m) (i : [Fin]](#manual-Fin___mk) n) : [Fin]](#manual-Fin___mk) m



[Fin.cast]](#manual-Fin___cast) {n m : [Nat]](#manual-Nat___zero)} (eq : n [=]](#manual-Eq___refl) m)
  (i : [Fin]](#manual-Fin___mk) n) : [Fin]](#manual-Fin___mk) m
```

Uses a proof that two bounds are equal to allow a value bounded by one to be used with the other.

In other words, when `eq : n = m`, `[Fin.cast]](#manual-Fin___cast) eq i` converts `i : [Fin]](#manual-Fin___mk) n` into a `[Fin]](#manual-Fin___mk) m`.

def

```lean
[Fin.castLT]](#manual-Fin___castLT) {n m : [Nat]](#manual-Nat___zero)} (i : [Fin]](#manual-Fin___mk) m) (h : ↑i [<]](#manual-LT___mk) n) : [Fin]](#manual-Fin___mk) n



[Fin.castLT]](#manual-Fin___castLT) {n m : [Nat]](#manual-Nat___zero)} (i : [Fin]](#manual-Fin___mk) m)
  (h : ↑i [<]](#manual-LT___mk) n) : [Fin]](#manual-Fin___mk) n
```

Replaces the bound with another that is suitable for the value.

The proof embedded in `i` can be used to cast to a larger bound even if the concrete value is not
known.

Examples:

```lean
example : [Fin]](#manual-Fin___mk) 12 := (7 : [Fin]](#manual-Fin___mk) 10).[castLT]](#manual-Fin___castLT) (by⊢ 7 [<]](#manual-LT___mk) 12 [decide]](#manual-decide)All goals completed! 🐙 : 7 < 12)
```

```lean
example (i : [Fin]](#manual-Fin___mk) 10) : [Fin]](#manual-Fin___mk) 12 :=
i.[castLT]](#manual-Fin___castLT) <| byi:[Fin]](#manual-Fin___mk) 10⊢ ↑i [<]](#manual-LT___mk) 12
[cases]](#manual-cases) imkval✝:[Nat]](#manual-Nat___zero)isLt✝:val✝ [<]](#manual-LT___mk) 10⊢ ↑[⟨]](#manual-Fin___mk)val✝[,]](#manual-Fin___mk) isLt✝[⟩]](#manual-Fin___mk) [<]](#manual-LT___mk) 12; [simp]](#manual-simp)mkval✝:[Nat]](#manual-Nat___zero)isLt✝:val✝ [<]](#manual-LT___mk) 10⊢ val✝ [<]](#manual-LT___mk) 12; [omega]](#manual-omega)All goals completed! 🐙
```

def

```lean
[Fin.castLE]](#manual-Fin___castLE) {n m : [Nat]](#manual-Nat___zero)} (h : n [≤]](#manual-LE___mk) m) (i : [Fin]](#manual-Fin___mk) n) : [Fin]](#manual-Fin___mk) m



[Fin.castLE]](#manual-Fin___castLE) {n m : [Nat]](#manual-Nat___zero)} (h : n [≤]](#manual-LE___mk) m)
  (i : [Fin]](#manual-Fin___mk) n) : [Fin]](#manual-Fin___mk) m
```

Coarsens a bound to one at least as large.

See also `[Fin.castAdd]](#manual-Fin___castAdd)` for a version that represents the larger bound with addition rather than an
explicit inequality proof.

def

```lean
[Fin.castAdd]](#manual-Fin___castAdd) {n : [Nat]](#manual-Nat___zero)} (m : [Nat]](#manual-Nat___zero)) : [Fin]](#manual-Fin___mk) n → [Fin]](#manual-Fin___mk) [(]](#manual-HAdd___mk)n [+]](#manual-HAdd___mk) m[)]](#manual-HAdd___mk)



[Fin.castAdd]](#manual-Fin___castAdd) {n : [Nat]](#manual-Nat___zero)} (m : [Nat]](#manual-Nat___zero)) :
  [Fin]](#manual-Fin___mk) n → [Fin]](#manual-Fin___mk) [(]](#manual-HAdd___mk)n [+]](#manual-HAdd___mk) m[)]](#manual-HAdd___mk)
```

Coarsens a bound to one at least as large.

See also `[Fin.natAdd]](#manual-Fin___natAdd)` and `[Fin.addNat]](#manual-Fin___addNat)` for addition functions that increase the bound, and
`[Fin.castLE]](#manual-Fin___castLE)` for a version that uses an explicit inequality proof.

def

```lean
[Fin.castSucc]](#manual-Fin___castSucc) {n : [Nat]](#manual-Nat___zero)} : [Fin]](#manual-Fin___mk) n → [Fin]](#manual-Fin___mk) [(]](#manual-HAdd___mk)n [+]](#manual-HAdd___mk) 1[)]](#manual-HAdd___mk)



[Fin.castSucc]](#manual-Fin___castSucc) {n : [Nat]](#manual-Nat___zero)} :
  [Fin]](#manual-Fin___mk) n → [Fin]](#manual-Fin___mk) [(]](#manual-HAdd___mk)n [+]](#manual-HAdd___mk) 1[)]](#manual-HAdd___mk)
```

Coarsens a bound by one.

def

```lean
[Fin.rev]](#manual-Fin___rev) {n : [Nat]](#manual-Nat___zero)} (i : [Fin]](#manual-Fin___mk) n) : [Fin]](#manual-Fin___mk) n



[Fin.rev]](#manual-Fin___rev) {n : [Nat]](#manual-Nat___zero)} (i : [Fin]](#manual-Fin___mk) n) : [Fin]](#manual-Fin___mk) n
```

Replaces a value with its difference from the largest value in the type.

Considering the values of `[Fin]](#manual-Fin___mk) n` as a sequence `0`, `1`, …, `n-2`, `n-1`, `[Fin.rev]](#manual-Fin___rev)` finds the
corresponding element of the reversed sequence. In other words, it maps `0` to `n-1`, `1` to `n-2`,
..., and `n-1` to `0`.

Examples:

- `(5 : [Fin]](#manual-Fin___mk) 6).[rev]](#manual-Fin___rev) = (0 : [Fin]](#manual-Fin___mk) 6)`
- `(0 : [Fin]](#manual-Fin___mk) 6).[rev]](#manual-Fin___rev) = (5 : [Fin]](#manual-Fin___mk) 6)`
- `(2 : [Fin]](#manual-Fin___mk) 5).[rev]](#manual-Fin___rev) = (2 : [Fin]](#manual-Fin___mk) 5)`

def

```lean
[Fin.elim0.{u}]](#manual-Fin___elim0) {α : Sort u} : [Fin]](#manual-Fin___mk) 0 → α



[Fin.elim0.{u}]](#manual-Fin___elim0) {α : Sort u} : [Fin]](#manual-Fin___mk) 0 → α
```

The type `[Fin]](#manual-Fin___mk) 0` is uninhabited, so it can be used to derive any result whatsoever.

This is similar to `[Empty.elim](https://lean-lang.org/doc/reference/latest/Basic-Types/The-Empty-Type/#Empty___elim)`. It can be thought of as a compiler-checked assertion that a code
path is unreachable, or a logical contradiction from which `[False]](#manual-False)` and thus anything else could be
derived.

#### 20.3.3.5. Iteration {#manual-The-Lean-Language-Reference--Basic-Types--Finite-Natural-Numbers--API-Reference--Iteration}

def

```lean
[Fin.foldr.{u_1}]](#manual-Fin___foldr) {α : Sort u_1} (n : [Nat]](#manual-Nat___zero)) (f : [Fin]](#manual-Fin___mk) n → α → α)
  (init : α) : α



[Fin.foldr.{u_1}]](#manual-Fin___foldr) {α : Sort u_1} (n : [Nat]](#manual-Nat___zero))
  (f : [Fin]](#manual-Fin___mk) n → α → α) (init : α) : α
```

Combine all the values that can be represented by `[Fin]](#manual-Fin___mk) n` with an initial value, starting at `n - 1`
and nesting to the right.

Example:

- `[Fin.foldr]](#manual-Fin___foldr) 3 (·.[val]](#manual-Fin___mk) + ·) (0 : [Nat]](#manual-Nat___zero)) = (0 : [Fin]](#manual-Fin___mk) 3).[val]](#manual-Fin___mk) + ((1 : [Fin]](#manual-Fin___mk) 3).[val]](#manual-Fin___mk) + ((2 : [Fin]](#manual-Fin___mk) 3).[val]](#manual-Fin___mk) + 0))`

def

```lean
[Fin.foldrM.{u_1, u_2}]](#manual-Fin___foldrM) {m : Type u_1 → Type u_2} {α : Type u_1} [[Monad]](#manual-Monad___mk) m]
  (n : [Nat]](#manual-Nat___zero)) (f : [Fin]](#manual-Fin___mk) n → α → m α) (init : α) : m α



[Fin.foldrM.{u_1, u_2}]](#manual-Fin___foldrM)
  {m : Type u_1 → Type u_2} {α : Type u_1}
  [[Monad]](#manual-Monad___mk) m] (n : [Nat]](#manual-Nat___zero))
  (f : [Fin]](#manual-Fin___mk) n → α → m α) (init : α) : m α
```

Folds a monadic function over `[Fin]](#manual-Fin___mk) n` from right to left, starting with `n-1`.

It is the sequence of steps:

```
Fin.foldrM n f xₙ = do
  let xₙ₋₁ ← f (n-1) xₙ
  let xₙ₋₂ ← f (n-2) xₙ₋₁
  ...
  let x₀ ← f 0 x₁
  pure x₀
```

def

```lean
[Fin.foldl.{u_1}]](#manual-Fin___foldl) {α : Sort u_1} (n : [Nat]](#manual-Nat___zero)) (f : α → [Fin]](#manual-Fin___mk) n → α)
  (init : α) : α



[Fin.foldl.{u_1}]](#manual-Fin___foldl) {α : Sort u_1} (n : [Nat]](#manual-Nat___zero))
  (f : α → [Fin]](#manual-Fin___mk) n → α) (init : α) : α
```

Combine all the values that can be represented by `[Fin]](#manual-Fin___mk) n` with an initial value, starting at `0` and
nesting to the left.

Example:

- `[Fin.foldl]](#manual-Fin___foldl) 3 (· + ·.[val]](#manual-Fin___mk)) (0 : [Nat]](#manual-Nat___zero)) = ((0 + (0 : [Fin]](#manual-Fin___mk) 3).[val]](#manual-Fin___mk)) + (1 : [Fin]](#manual-Fin___mk) 3).[val]](#manual-Fin___mk)) + (2 : [Fin]](#manual-Fin___mk) 3).[val]](#manual-Fin___mk)`

def

```lean
[Fin.foldlM.{u_1, u_2}]](#manual-Fin___foldlM) {m : Type u_1 → Type u_2} {α : Type u_1} [[Monad]](#manual-Monad___mk) m]
  (n : [Nat]](#manual-Nat___zero)) (f : α → [Fin]](#manual-Fin___mk) n → m α) (init : α) : m α



[Fin.foldlM.{u_1, u_2}]](#manual-Fin___foldlM)
  {m : Type u_1 → Type u_2} {α : Type u_1}
  [[Monad]](#manual-Monad___mk) m] (n : [Nat]](#manual-Nat___zero))
  (f : α → [Fin]](#manual-Fin___mk) n → m α) (init : α) : m α
```

Folds a monadic function over all the values in `[Fin]](#manual-Fin___mk) n` from left to right, starting with `0`.

It is the sequence of steps:

```
Fin.foldlM n f x₀ = do
  let x₁ ← f x₀ 0
  let x₂ ← f x₁ 1
  ...
  let xₙ ← f xₙ₋₁ (n-1)
  pure xₙ
```

def

```lean
[Fin.hIterate.{u_1}]](#manual-Fin___hIterate) (P : [Nat]](#manual-Nat___zero) → Sort u_1) {n : [Nat]](#manual-Nat___zero)} (init : P 0)
  (f : (i : [Fin]](#manual-Fin___mk) n) → P ↑i → P [(]](#manual-HAdd___mk)↑i [+]](#manual-HAdd___mk) 1[)]](#manual-HAdd___mk)) : P n



[Fin.hIterate.{u_1}]](#manual-Fin___hIterate) (P : [Nat]](#manual-Nat___zero) → Sort u_1)
  {n : [Nat]](#manual-Nat___zero)} (init : P 0)
  (f : (i : [Fin]](#manual-Fin___mk) n) → P ↑i → P [(]](#manual-HAdd___mk)↑i [+]](#manual-HAdd___mk) 1[)]](#manual-HAdd___mk)) :
  P n
```

Applies an index-dependent function to all the values less than the given bound `n`, starting at
`0` with an accumulator.

Concretely, `[Fin.hIterate]](#manual-Fin___hIterate) P init f` is equal to

```
  init |> f 0 |> f 1 |> ... |> f (n-1)
```

Theorems about `[Fin.hIterate]](#manual-Fin___hIterate)` can be proven using the general theorem `Fin.hIterate_elim` or other more
specialized theorems.

`[Fin.hIterateFrom]](#manual-Fin___hIterateFrom)` is a variant that takes a custom starting value instead of `0`.

def

```lean
[Fin.hIterateFrom.{u_1}]](#manual-Fin___hIterateFrom) (P : [Nat]](#manual-Nat___zero) → Sort u_1) {n : [Nat]](#manual-Nat___zero)}
  (f : (i : [Fin]](#manual-Fin___mk) n) → P ↑i → P [(]](#manual-HAdd___mk)↑i [+]](#manual-HAdd___mk) 1[)]](#manual-HAdd___mk)) (i : [Nat]](#manual-Nat___zero)) (ubnd : i [≤]](#manual-LE___mk) n)
  (a : P i) : P n



[Fin.hIterateFrom.{u_1}]](#manual-Fin___hIterateFrom)
  (P : [Nat]](#manual-Nat___zero) → Sort u_1) {n : [Nat]](#manual-Nat___zero)}
  (f : (i : [Fin]](#manual-Fin___mk) n) → P ↑i → P [(]](#manual-HAdd___mk)↑i [+]](#manual-HAdd___mk) 1[)]](#manual-HAdd___mk))
  (i : [Nat]](#manual-Nat___zero)) (ubnd : i [≤]](#manual-LE___mk) n) (a : P i) : P n
```

Applies an index-dependent function `f` to all of the values in `[i:n]`, starting at `i` with an
initial accumulator `a`.

Concretely, `[Fin.hIterateFrom]](#manual-Fin___hIterateFrom) P f i a` is equal to

```
  a |> f i |> f (i + 1) |> ... |> f (n - 1)
```

Theorems about `[Fin.hIterateFrom]](#manual-Fin___hIterateFrom)` can be proven using the general theorem `[Fin]](#manual-Fin___mk).hIterateFrom_elim` or
other more specialized theorems.

`[Fin.hIterate]](#manual-Fin___hIterate)` is a variant that always starts at `0`.

#### 20.3.3.6. Reasoning {#manual-The-Lean-Language-Reference--Basic-Types--Finite-Natural-Numbers--API-Reference--Reasoning}

def

```lean
[Fin.induction.{u_1}]](#manual-Fin___induction) {n : [Nat]](#manual-Nat___zero)} {motive : [Fin]](#manual-Fin___mk) [(]](#manual-HAdd___mk)n [+]](#manual-HAdd___mk) 1[)]](#manual-HAdd___mk) → Sort u_1}
  (zero : motive 0)
  (succ : (i : [Fin]](#manual-Fin___mk) n) → motive i.[castSucc]](#manual-Fin___castSucc) → motive i.[succ]](#manual-Fin___succ))
  (i : [Fin]](#manual-Fin___mk) [(]](#manual-HAdd___mk)n [+]](#manual-HAdd___mk) 1[)]](#manual-HAdd___mk)) : motive i



[Fin.induction.{u_1}]](#manual-Fin___induction) {n : [Nat]](#manual-Nat___zero)}
  {motive : [Fin]](#manual-Fin___mk) [(]](#manual-HAdd___mk)n [+]](#manual-HAdd___mk) 1[)]](#manual-HAdd___mk) → Sort u_1}
  (zero : motive 0)
  (succ :
    (i : [Fin]](#manual-Fin___mk) n) →
      motive i.[castSucc]](#manual-Fin___castSucc) → motive i.[succ]](#manual-Fin___succ))
  (i : [Fin]](#manual-Fin___mk) [(]](#manual-HAdd___mk)n [+]](#manual-HAdd___mk) 1[)]](#manual-HAdd___mk)) : motive i
```

Proves a statement by induction on the underlying `[Nat]](#manual-Nat___zero)` value in a `[Fin]](#manual-Fin___mk) (n + 1)`.

For the induction:

- `zero` is the base case, demonstrating `motive 0`.
- `succ` is the inductive step, assuming the motive for `i : Fin n` (lifted to `[Fin]](#manual-Fin___mk) (n + 1)` with
  `[Fin.castSucc]](#manual-Fin___castSucc)`) and demonstrating it for `i.[succ]](#manual-Fin___succ)`.

`[Fin.inductionOn]](#manual-Fin___inductionOn)` is a version of this induction principle that takes the `[Fin]](#manual-Fin___mk)` as its first
parameter, `[Fin.cases]](#manual-Fin___cases)` is the corresponding case analysis operator, and `[Fin.reverseInduction]](#manual-Fin___reverseInduction)` is a
version that starts at the greatest value instead of `0`.

def

```lean
[Fin.inductionOn.{u_1}]](#manual-Fin___inductionOn) {n : [Nat]](#manual-Nat___zero)} (i : [Fin]](#manual-Fin___mk) [(]](#manual-HAdd___mk)n [+]](#manual-HAdd___mk) 1[)]](#manual-HAdd___mk))
  {motive : [Fin]](#manual-Fin___mk) [(]](#manual-HAdd___mk)n [+]](#manual-HAdd___mk) 1[)]](#manual-HAdd___mk) → Sort u_1} (zero : motive 0)
  (succ : (i : [Fin]](#manual-Fin___mk) n) → motive i.[castSucc]](#manual-Fin___castSucc) → motive i.[succ]](#manual-Fin___succ)) : motive i



[Fin.inductionOn.{u_1}]](#manual-Fin___inductionOn) {n : [Nat]](#manual-Nat___zero)}
  (i : [Fin]](#manual-Fin___mk) [(]](#manual-HAdd___mk)n [+]](#manual-HAdd___mk) 1[)]](#manual-HAdd___mk))
  {motive : [Fin]](#manual-Fin___mk) [(]](#manual-HAdd___mk)n [+]](#manual-HAdd___mk) 1[)]](#manual-HAdd___mk) → Sort u_1}
  (zero : motive 0)
  (succ :
    (i : [Fin]](#manual-Fin___mk) n) →
      motive i.[castSucc]](#manual-Fin___castSucc) → motive i.[succ]](#manual-Fin___succ)) :
  motive i
```

Proves a statement by induction on the underlying `[Nat]](#manual-Nat___zero)` value in a `[Fin]](#manual-Fin___mk) (n + 1)`.

For the induction:

- `zero` is the base case, demonstrating `motive 0`.
- `succ` is the inductive step, assuming the motive for `i : Fin n` (lifted to `[Fin]](#manual-Fin___mk) (n + 1)` with
  `[Fin.castSucc]](#manual-Fin___castSucc)`) and demonstrating it for `i.[succ]](#manual-Fin___succ)`.

`[Fin.induction]](#manual-Fin___induction)` is a version of this induction principle that takes the `[Fin]](#manual-Fin___mk)` as its last
parameter.

def

```lean
[Fin.reverseInduction.{u_1}]](#manual-Fin___reverseInduction) {n : [Nat]](#manual-Nat___zero)} {motive : [Fin]](#manual-Fin___mk) [(]](#manual-HAdd___mk)n [+]](#manual-HAdd___mk) 1[)]](#manual-HAdd___mk) → Sort u_1}
  (last : motive ([Fin.last]](#manual-Fin___last) n))
  (cast : (i : [Fin]](#manual-Fin___mk) n) → motive i.[succ]](#manual-Fin___succ) → motive i.[castSucc]](#manual-Fin___castSucc))
  (i : [Fin]](#manual-Fin___mk) [(]](#manual-HAdd___mk)n [+]](#manual-HAdd___mk) 1[)]](#manual-HAdd___mk)) : motive i



[Fin.reverseInduction.{u_1}]](#manual-Fin___reverseInduction) {n : [Nat]](#manual-Nat___zero)}
  {motive : [Fin]](#manual-Fin___mk) [(]](#manual-HAdd___mk)n [+]](#manual-HAdd___mk) 1[)]](#manual-HAdd___mk) → Sort u_1}
  (last : motive ([Fin.last]](#manual-Fin___last) n))
  (cast :
    (i : [Fin]](#manual-Fin___mk) n) →
      motive i.[succ]](#manual-Fin___succ) → motive i.[castSucc]](#manual-Fin___castSucc))
  (i : [Fin]](#manual-Fin___mk) [(]](#manual-HAdd___mk)n [+]](#manual-HAdd___mk) 1[)]](#manual-HAdd___mk)) : motive i
```

Proves a statement by reverse induction on the underlying `[Nat]](#manual-Nat___zero)` value in a `[Fin]](#manual-Fin___mk) (n + 1)`.

For the induction:

- `last` is the base case, demonstrating `motive ([Fin.last]](#manual-Fin___last) n)`.
- `[cast]](#manual-cast)` is the inductive step, assuming the motive for `(j : [Fin]](#manual-Fin___mk) n).[succ]](#manual-Fin___succ)` and demonstrating it for
  the predecessor `j.[castSucc]](#manual-Fin___castSucc)`.

`[Fin.induction]](#manual-Fin___induction)` is the non-reverse induction principle.

def

```lean
[Fin.cases.{u_1}]](#manual-Fin___cases) {n : [Nat]](#manual-Nat___zero)} {motive : [Fin]](#manual-Fin___mk) [(]](#manual-HAdd___mk)n [+]](#manual-HAdd___mk) 1[)]](#manual-HAdd___mk) → Sort u_1}
  (zero : motive 0) (succ : (i : [Fin]](#manual-Fin___mk) n) → motive i.[succ]](#manual-Fin___succ))
  (i : [Fin]](#manual-Fin___mk) [(]](#manual-HAdd___mk)n [+]](#manual-HAdd___mk) 1[)]](#manual-HAdd___mk)) : motive i



[Fin.cases.{u_1}]](#manual-Fin___cases) {n : [Nat]](#manual-Nat___zero)}
  {motive : [Fin]](#manual-Fin___mk) [(]](#manual-HAdd___mk)n [+]](#manual-HAdd___mk) 1[)]](#manual-HAdd___mk) → Sort u_1}
  (zero : motive 0)
  (succ : (i : [Fin]](#manual-Fin___mk) n) → motive i.[succ]](#manual-Fin___succ))
  (i : [Fin]](#manual-Fin___mk) [(]](#manual-HAdd___mk)n [+]](#manual-HAdd___mk) 1[)]](#manual-HAdd___mk)) : motive i
```

Proves a statement by cases on the underlying `[Nat]](#manual-Nat___zero)` value in a `[Fin]](#manual-Fin___mk) (n + 1)`.

The two cases are:

- `zero`, used when the value is of the form `(0 : [Fin]](#manual-Fin___mk) (n + 1))`
- `succ`, used when the value is of the form `(j : [Fin]](#manual-Fin___mk) n).[succ]](#manual-Fin___succ)`

The corresponding induction principle is `[Fin.induction]](#manual-Fin___induction)`.

def

```lean
[Fin.lastCases.{u_1}]](#manual-Fin___lastCases) {n : [Nat]](#manual-Nat___zero)} {motive : [Fin]](#manual-Fin___mk) [(]](#manual-HAdd___mk)n [+]](#manual-HAdd___mk) 1[)]](#manual-HAdd___mk) → Sort u_1}
  (last : motive ([Fin.last]](#manual-Fin___last) n)) (cast : (i : [Fin]](#manual-Fin___mk) n) → motive i.[castSucc]](#manual-Fin___castSucc))
  (i : [Fin]](#manual-Fin___mk) [(]](#manual-HAdd___mk)n [+]](#manual-HAdd___mk) 1[)]](#manual-HAdd___mk)) : motive i



[Fin.lastCases.{u_1}]](#manual-Fin___lastCases) {n : [Nat]](#manual-Nat___zero)}
  {motive : [Fin]](#manual-Fin___mk) [(]](#manual-HAdd___mk)n [+]](#manual-HAdd___mk) 1[)]](#manual-HAdd___mk) → Sort u_1}
  (last : motive ([Fin.last]](#manual-Fin___last) n))
  (cast : (i : [Fin]](#manual-Fin___mk) n) → motive i.[castSucc]](#manual-Fin___castSucc))
  (i : [Fin]](#manual-Fin___mk) [(]](#manual-HAdd___mk)n [+]](#manual-HAdd___mk) 1[)]](#manual-HAdd___mk)) : motive i
```

Proves a statement by cases on the underlying `[Nat]](#manual-Nat___zero)` value in a `[Fin]](#manual-Fin___mk) (n + 1)`, checking whether the
value is the greatest representable or a predecessor of some other.

The two cases are:

- `last`, used when the value is `[Fin.last]](#manual-Fin___last) n`
- `[cast]](#manual-cast)`, used when the value is of the form `(j : [Fin]](#manual-Fin___mk) n).[succ]](#manual-Fin___succ)`

The corresponding induction principle is `[Fin.reverseInduction]](#manual-Fin___reverseInduction)`.

def

```lean
[Fin.addCases.{u}]](#manual-Fin___addCases) {m n : [Nat]](#manual-Nat___zero)} {motive : [Fin]](#manual-Fin___mk) [(]](#manual-HAdd___mk)m [+]](#manual-HAdd___mk) n[)]](#manual-HAdd___mk) → Sort u}
  (left : (i : [Fin]](#manual-Fin___mk) m) → motive ([Fin.castAdd]](#manual-Fin___castAdd) n i))
  (right : (i : [Fin]](#manual-Fin___mk) n) → motive ([Fin.natAdd]](#manual-Fin___natAdd) m i)) (i : [Fin]](#manual-Fin___mk) [(]](#manual-HAdd___mk)m [+]](#manual-HAdd___mk) n[)]](#manual-HAdd___mk)) :
  motive i



[Fin.addCases.{u}]](#manual-Fin___addCases) {m n : [Nat]](#manual-Nat___zero)}
  {motive : [Fin]](#manual-Fin___mk) [(]](#manual-HAdd___mk)m [+]](#manual-HAdd___mk) n[)]](#manual-HAdd___mk) → Sort u}
  (left :
    (i : [Fin]](#manual-Fin___mk) m) →
      motive ([Fin.castAdd]](#manual-Fin___castAdd) n i))
  (right :
    (i : [Fin]](#manual-Fin___mk) n) → motive ([Fin.natAdd]](#manual-Fin___natAdd) m i))
  (i : [Fin]](#manual-Fin___mk) [(]](#manual-HAdd___mk)m [+]](#manual-HAdd___mk) n[)]](#manual-HAdd___mk)) : motive i
```

A case analysis operator for `i : [Fin]](#manual-Fin___mk) (m + n)` that separately handles the cases where `i < m` and
where `m ≤ i < m + n`.

The first case, where `i < m`, is handled by `left`. In this case, `i` can be represented as
`[Fin.castAdd]](#manual-Fin___castAdd) n (j : [Fin]](#manual-Fin___mk) m)`.

The second case, where `m ≤ i < m + n`, is handled by `right`. In this case, `i` can be represented
as `[Fin.natAdd]](#manual-Fin___natAdd) m (j : [Fin]](#manual-Fin___mk) n)`.

def

```lean
[Fin.succRec.{u_1}]](#manual-Fin___succRec) {motive : (n : [Nat]](#manual-Nat___zero)) → [Fin]](#manual-Fin___mk) n → Sort u_1}
  (zero : (n : [Nat]](#manual-Nat___zero)) → motive n.[succ]](#manual-Nat___zero) 0)
  (succ : (n : [Nat]](#manual-Nat___zero)) → (i : [Fin]](#manual-Fin___mk) n) → motive n i → motive n.[succ]](#manual-Nat___zero) i.[succ]](#manual-Fin___succ))
  {n : [Nat]](#manual-Nat___zero)} (i : [Fin]](#manual-Fin___mk) n) : motive n i



[Fin.succRec.{u_1}]](#manual-Fin___succRec)
  {motive : (n : [Nat]](#manual-Nat___zero)) → [Fin]](#manual-Fin___mk) n → Sort u_1}
  (zero : (n : [Nat]](#manual-Nat___zero)) → motive n.[succ]](#manual-Nat___zero) 0)
  (succ :
    (n : [Nat]](#manual-Nat___zero)) →
      (i : [Fin]](#manual-Fin___mk) n) →
        motive n i → motive n.[succ]](#manual-Nat___zero) i.[succ]](#manual-Fin___succ))
  {n : [Nat]](#manual-Nat___zero)} (i : [Fin]](#manual-Fin___mk) n) : motive n i
```

An induction principle for `[Fin]](#manual-Fin___mk)` that considers a given `i : [Fin]](#manual-Fin___mk) n` as given by a sequence of `i`
applications of `[Fin.succ]](#manual-Fin___succ)`.

The cases in the induction are:

- `zero` demonstrates the motive for `(0 : [Fin]](#manual-Fin___mk) (n + 1))` for all bounds `n`
- `succ` demonstrates the motive for `[Fin.succ]](#manual-Fin___succ)` applied to an arbitrary `[Fin]](#manual-Fin___mk)` for an arbitrary
  bound `n`

Unlike `[Fin.induction]](#manual-Fin___induction)`, the motive quantifies over the bound, and the bound varies at each inductive
step. `[Fin.succRecOn]](#manual-Fin___succRecOn)` is a version of this induction principle that takes the `[Fin]](#manual-Fin___mk)` argument first.

def

```lean
[Fin.succRecOn.{u_1}]](#manual-Fin___succRecOn) {n : [Nat]](#manual-Nat___zero)} (i : [Fin]](#manual-Fin___mk) n)
  {motive : (n : [Nat]](#manual-Nat___zero)) → [Fin]](#manual-Fin___mk) n → Sort u_1}
  (zero : (n : [Nat]](#manual-Nat___zero)) → motive [(]](#manual-HAdd___mk)n [+]](#manual-HAdd___mk) 1[)]](#manual-HAdd___mk) 0)
  (succ : (n : [Nat]](#manual-Nat___zero)) → (i : [Fin]](#manual-Fin___mk) n) → motive n i → motive n.[succ]](#manual-Nat___zero) i.[succ]](#manual-Fin___succ)) :
  motive n i



[Fin.succRecOn.{u_1}]](#manual-Fin___succRecOn) {n : [Nat]](#manual-Nat___zero)} (i : [Fin]](#manual-Fin___mk) n)
  {motive : (n : [Nat]](#manual-Nat___zero)) → [Fin]](#manual-Fin___mk) n → Sort u_1}
  (zero : (n : [Nat]](#manual-Nat___zero)) → motive [(]](#manual-HAdd___mk)n [+]](#manual-HAdd___mk) 1[)]](#manual-HAdd___mk) 0)
  (succ :
    (n : [Nat]](#manual-Nat___zero)) →
      (i : [Fin]](#manual-Fin___mk) n) →
        motive n i →
          motive n.[succ]](#manual-Nat___zero) i.[succ]](#manual-Fin___succ)) :
  motive n i
```

An induction principle for `[Fin]](#manual-Fin___mk)` that considers a given `i : [Fin]](#manual-Fin___mk) n` as given by a sequence of `i`
applications of `[Fin.succ]](#manual-Fin___succ)`.

The cases in the induction are:

- `zero` demonstrates the motive for `(0 : [Fin]](#manual-Fin___mk) (n + 1))` for all bounds `n`
- `succ` demonstrates the motive for `[Fin.succ]](#manual-Fin___succ)` applied to an arbitrary `[Fin]](#manual-Fin___mk)` for an arbitrary
  bound `n`

Unlike `[Fin.induction]](#manual-Fin___induction)`, the motive quantifies over the bound, and the bound varies at each inductive
step. `[Fin.succRec]](#manual-Fin___succRec)` is a version of this induction principle that takes the `[Fin]](#manual-Fin___mk)` argument last.

---



## Basic Types — 20.4. Fixed-Precision Integers {#manual-basic-types-204-fixed-precision-integers}

> 📄 Source: https://lean-lang.org/doc/reference/latest/Basic-Types/Fixed-Precision-Integers/

Lean's standard library includes the usual assortment of fixed-width integer types.
From the perspective of formalization and proofs, these types are wrappers around bitvectors of the appropriate size; the wrappers ensure that the correct implementations of e.g. arithmetic operations are applied.
In compiled code, they are represented efficiently: the compiler has special support for them, as it does for other fundamental types.

### 20.4.1. Logical Model {#manual-The-Lean-Language-Reference--Basic-Types--Fixed-Precision-Integers--Logical-Model}

Fixed-width integers may be unsigned or signed.
Furthermore, they are available in five sizes: 8, 16, 32, and 64 bits, along with the current architecture's word size.
In their logical models, the unsigned integers are structures that wrap a `[BitVec](https://lean-lang.org/doc/reference/latest/Basic-Types/Bitvectors/#BitVec___ofFin)` of the appropriate width.
Signed integers wrap the corresponding unsigned integers, and use a twos-complement representation.

#### 20.4.1.1. Unsigned {#manual-The-Lean-Language-Reference--Basic-Types--Fixed-Precision-Integers--Logical-Model--Unsigned}

structure

```lean
[USize]](#manual-USize___ofBitVec) : Type



[USize]](#manual-USize___ofBitVec) : Type
```

Unsigned integers that are the size of a word on the platform's architecture.

On a 32-bit architecture, `[USize]](#manual-USize___ofBitVec)` is equivalent to `[UInt32]](#manual-UInt32___ofBitVec)`. On a 64-bit machine, it is equivalent
to `[UInt64]](#manual-UInt64___ofBitVec)`.

Constructor

```lean
[USize.ofBitVec]](#manual-USize___ofBitVec)
```

Creates a `[USize]](#manual-USize___ofBitVec)` from a `[BitVec](https://lean-lang.org/doc/reference/latest/Basic-Types/Bitvectors/#BitVec___ofFin) [System.Platform.numBits](https://lean-lang.org/doc/reference/latest/IO/System-and-Platform-Information/#System___Platform___numBits)`. This function is overridden with a
native implementation.

Fields

```lean
toBitVec : [BitVec](https://lean-lang.org/doc/reference/latest/Basic-Types/Bitvectors/#BitVec___ofFin) [System.Platform.numBits](https://lean-lang.org/doc/reference/latest/IO/System-and-Platform-Information/#System___Platform___numBits)
```

Unpacks a `[USize]](#manual-USize___ofBitVec)` into a `[BitVec](https://lean-lang.org/doc/reference/latest/Basic-Types/Bitvectors/#BitVec___ofFin) [System.Platform.numBits](https://lean-lang.org/doc/reference/latest/IO/System-and-Platform-Information/#System___Platform___numBits)`. This function is overridden with a native
implementation.

structure

```lean
[UInt8]](#manual-UInt8___ofBitVec) : Type



[UInt8]](#manual-UInt8___ofBitVec) : Type
```

Unsigned 8-bit integers.

This type has special support in the compiler so it can be represented by an unboxed 8-bit value
rather than wrapping a `[BitVec](https://lean-lang.org/doc/reference/latest/Basic-Types/Bitvectors/#BitVec___ofFin) 8`.

Constructor

```lean
[UInt8.ofBitVec]](#manual-UInt8___ofBitVec)
```

Creates a `[UInt8]](#manual-UInt8___ofBitVec)` from a `[BitVec](https://lean-lang.org/doc/reference/latest/Basic-Types/Bitvectors/#BitVec___ofFin) 8`. This function is overridden with a native implementation.

Fields

```lean
toBitVec : [BitVec](https://lean-lang.org/doc/reference/latest/Basic-Types/Bitvectors/#BitVec___ofFin) 8
```

Unpacks a `[UInt8]](#manual-UInt8___ofBitVec)` into a `[BitVec](https://lean-lang.org/doc/reference/latest/Basic-Types/Bitvectors/#BitVec___ofFin) 8`. This function is overridden with a native implementation.

structure

```lean
[UInt16]](#manual-UInt16___ofBitVec) : Type



[UInt16]](#manual-UInt16___ofBitVec) : Type
```

Unsigned 16-bit integers.

This type has special support in the compiler so it can be represented by an unboxed 16-bit value
rather than wrapping a `[BitVec](https://lean-lang.org/doc/reference/latest/Basic-Types/Bitvectors/#BitVec___ofFin) 16`.

Constructor

```lean
[UInt16.ofBitVec]](#manual-UInt16___ofBitVec)
```

Creates a `[UInt16]](#manual-UInt16___ofBitVec)` from a `[BitVec](https://lean-lang.org/doc/reference/latest/Basic-Types/Bitvectors/#BitVec___ofFin) 16`. This function is overridden with a native implementation.

Fields

```lean
toBitVec : [BitVec](https://lean-lang.org/doc/reference/latest/Basic-Types/Bitvectors/#BitVec___ofFin) 16
```

Unpacks a `[UInt16]](#manual-UInt16___ofBitVec)` into a `[BitVec](https://lean-lang.org/doc/reference/latest/Basic-Types/Bitvectors/#BitVec___ofFin) 16`. This function is overridden with a native implementation.

structure

```lean
[UInt32]](#manual-UInt32___ofBitVec) : Type



[UInt32]](#manual-UInt32___ofBitVec) : Type
```

Unsigned 32-bit integers.

This type has special support in the compiler so it can be represented by an unboxed 32-bit value
rather than wrapping a `[BitVec](https://lean-lang.org/doc/reference/latest/Basic-Types/Bitvectors/#BitVec___ofFin) 32`.

Constructor

```lean
[UInt32.ofBitVec]](#manual-UInt32___ofBitVec)
```

Creates a `[UInt32]](#manual-UInt32___ofBitVec)` from a `[BitVec](https://lean-lang.org/doc/reference/latest/Basic-Types/Bitvectors/#BitVec___ofFin) 32`. This function is overridden with a native implementation.

Fields

```lean
toBitVec : [BitVec](https://lean-lang.org/doc/reference/latest/Basic-Types/Bitvectors/#BitVec___ofFin) 32
```

Unpacks a `[UInt32]](#manual-UInt32___ofBitVec)` into a `[BitVec](https://lean-lang.org/doc/reference/latest/Basic-Types/Bitvectors/#BitVec___ofFin) 32`. This function is overridden with a native implementation.

structure

```lean
[UInt64]](#manual-UInt64___ofBitVec) : Type



[UInt64]](#manual-UInt64___ofBitVec) : Type
```

Unsigned 64-bit integers.

This type has special support in the compiler so it can be represented by an unboxed 64-bit value
rather than wrapping a `[BitVec](https://lean-lang.org/doc/reference/latest/Basic-Types/Bitvectors/#BitVec___ofFin) 64`.

Constructor

```lean
[UInt64.ofBitVec]](#manual-UInt64___ofBitVec)
```

Creates a `[UInt64]](#manual-UInt64___ofBitVec)` from a `[BitVec](https://lean-lang.org/doc/reference/latest/Basic-Types/Bitvectors/#BitVec___ofFin) 64`. This function is overridden with a native implementation.

Fields

```lean
toBitVec : [BitVec](https://lean-lang.org/doc/reference/latest/Basic-Types/Bitvectors/#BitVec___ofFin) 64
```

Unpacks a `[UInt64]](#manual-UInt64___ofBitVec)` into a `[BitVec](https://lean-lang.org/doc/reference/latest/Basic-Types/Bitvectors/#BitVec___ofFin) 64`. This function is overridden with a native implementation.

#### 20.4.1.2. Signed {#manual-The-Lean-Language-Reference--Basic-Types--Fixed-Precision-Integers--Logical-Model--Signed}

structure

```lean
[ISize]](#manual-ISize___ofUSize) : Type



[ISize]](#manual-ISize___ofUSize) : Type
```

Signed integers that are the size of a word on the platform's architecture.

On a 32-bit architecture, `[ISize]](#manual-ISize___ofUSize)` is equivalent to `[Int32]](#manual-Int32___ofUInt32)`. On a 64-bit machine, it is equivalent to
`[Int64]](#manual-Int64___ofUInt64)`. This type has special support in the compiler so it can be represented by an unboxed value.

Constructor

```lean
[ISize.ofUSize]](#manual-ISize___ofUSize)
```

Fields

```lean
toUSize : [USize]](#manual-USize___ofBitVec)
```

Converts a word-sized signed integer into the word-sized unsigned integer that is its two's
complement encoding.

structure

```lean
[Int8]](#manual-Int8___ofUInt8) : Type



[Int8]](#manual-Int8___ofUInt8) : Type
```

Signed 8-bit integers.

This type has special support in the compiler so it can be represented by an unboxed 8-bit value.

Constructor

```lean
[Int8.ofUInt8]](#manual-Int8___ofUInt8)
```

Fields

```lean
toUInt8 : [UInt8]](#manual-UInt8___ofBitVec)
```

Converts an 8-bit signed integer into the 8-bit unsigned integer that is its two's complement
encoding.

structure

```lean
[Int16]](#manual-Int16___ofUInt16) : Type



[Int16]](#manual-Int16___ofUInt16) : Type
```

Signed 16-bit integers.

This type has special support in the compiler so it can be represented by an unboxed 16-bit value.

Constructor

```lean
[Int16.ofUInt16]](#manual-Int16___ofUInt16)
```

Fields

```lean
toUInt16 : [UInt16]](#manual-UInt16___ofBitVec)
```

Converts an 16-bit signed integer into the 16-bit unsigned integer that is its two's complement
encoding.

structure

```lean
[Int32]](#manual-Int32___ofUInt32) : Type



[Int32]](#manual-Int32___ofUInt32) : Type
```

Signed 32-bit integers.

This type has special support in the compiler so it can be represented by an unboxed 32-bit value.

Constructor

```lean
[Int32.ofUInt32]](#manual-Int32___ofUInt32)
```

Fields

```lean
toUInt32 : [UInt32]](#manual-UInt32___ofBitVec)
```

Converts an 32-bit signed integer into the 32-bit unsigned integer that is its two's complement
encoding.

structure

```lean
[Int64]](#manual-Int64___ofUInt64) : Type



[Int64]](#manual-Int64___ofUInt64) : Type
```

Signed 64-bit integers.

This type has special support in the compiler so it can be represented by an unboxed 64-bit value.

Constructor

```lean
[Int64.ofUInt64]](#manual-Int64___ofUInt64)
```

Fields

```lean
toUInt64 : [UInt64]](#manual-UInt64___ofBitVec)
```

Converts an 64-bit signed integer into the 64-bit unsigned integer that is its two's complement
encoding.

### 20.4.2. Run-Time Representation {#manual-fixed-int-runtime}

In compiled code in contexts that require [boxed]](#manual---tech-term-Boxed) representations, fixed-width integer types that fit in one less bit than the platform's pointer size are always represented without additional allocations or indirections.
This always includes `[Int8]](#manual-Int8___ofUInt8)`, `[UInt8]](#manual-UInt8___ofBitVec)`, `[Int16]](#manual-Int16___ofUInt16)`, and `[UInt16]](#manual-UInt16___ofBitVec)`.
On 64-bit architectures, `[Int32]](#manual-Int32___ofUInt32)` and `[UInt32]](#manual-UInt32___ofBitVec)` are also represented without pointers.
On 32-bit architectures, `[Int32]](#manual-Int32___ofUInt32)` and `[UInt32]](#manual-UInt32___ofBitVec)` require a pointer to an object on the heap.
`[ISize]](#manual-ISize___ofUSize)`, `[USize]](#manual-USize___ofBitVec)`, `[Int64]](#manual-Int64___ofUInt64)` and `[UInt64]](#manual-UInt64___ofBitVec)` may require pointers on all architectures.

Even though some fixed-with integer types require boxing in general, the compiler is able to represent them without boxing or pointer indirections in code paths that use only a specific fixed-width type rather than being polymorphic, potentially after a specialization pass.
This applies in most practical situations where these types are used: their values are represented using the corresponding unsigned fixed-width C type when a constructor parameter, function parameter, function return value, or intermediate result is known to be a fixed-width integer type.
The Lean run-time system includes primitives for storing fixed-width integers in constructors of [inductive types]](#manual---tech-term-Inductive-types), and the primitive operations are defined on the corresponding C types, so boxing tends to happen at the “edges” of integer calculations rather than for each intermediate result.
In contexts where other types might occur, such as the contents of polymorphic containers like `[Array](https://lean-lang.org/doc/reference/latest/Basic-Types/Arrays/#Array___mk)`, these types are boxed, even if an array is statically known to contain only a single fixed-width integer type.The monomorphic array type `[ByteArray](https://lean-lang.org/doc/reference/latest/Basic-Types/Byte-Arrays/#ByteArray___mk)` avoids boxing for arrays of `[UInt8]](#manual-UInt8___ofBitVec)`.
Lean does not specialize the representation of inductive types or arrays.
Inspecting a function's type in Lean is not sufficient to determine how fixed-width integer values will be represented, because boxed values are not eagerly unboxed—a function that projects an `[Int64]](#manual-Int64___ofUInt64)` from an array returns a boxed integer value.

### 20.4.3. Syntax {#manual-The-Lean-Language-Reference--Basic-Types--Fixed-Precision-Integers--Syntax}

All the fixed-width integer types have `[OfNat]](#manual-OfNat___mk)` instances, which allow numerals to be used as literals, both in expression and in pattern contexts.
The signed types additionally have `[Neg]](#manual-Neg___mk)` instances, allowing negation to be applied.

**Example: Fixed-Width Literals**

Lean allows both decimal and hexadecimal literals to be used for types with `[OfNat]](#manual-OfNat___mk)` instances.
In this example, literal notation is used to define masks.

```lean
structure Permissions where
readable : [Bool](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)
writable : [Bool](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)
executable : [Bool](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)
def Permissions.encode (p : [Permissions]](#manual-Permissions-_LPAR_in-Fixed-Width-Literals_RPAR_)) : [UInt8]](#manual-UInt8___ofBitVec) :=
let r := [if]](#manual-termIfThenElse) p.[readable]](#manual-Permissions___readable-_LPAR_in-Fixed-Width-Literals_RPAR_) [then]](#manual-termIfThenElse) 0x01 [else]](#manual-termIfThenElse) 0
let w := [if]](#manual-termIfThenElse) p.[writable]](#manual-Permissions___writable-_LPAR_in-Fixed-Width-Literals_RPAR_) [then]](#manual-termIfThenElse) 0x02 [else]](#manual-termIfThenElse) 0
let x := [if]](#manual-termIfThenElse) p.[executable]](#manual-Permissions___executable-_LPAR_in-Fixed-Width-Literals_RPAR_) [then]](#manual-termIfThenElse) 0x04 [else]](#manual-termIfThenElse) 0
r ||| w ||| x
def Permissions.decode (i : [UInt8]](#manual-UInt8___ofBitVec)) : [Permissions]](#manual-Permissions-_LPAR_in-Fixed-Width-Literals_RPAR_) :=
⟨i &&& 0x01 ≠ 0, i &&& 0x02 ≠ 0, i &&& 0x04 ≠ 0⟩
```

Literals that overflow their types' precision are interpreted modulus the precision.
Signed types, are interpreted according to the underlying twos-complement representation.

**Example: Overflowing Fixed-Width Literals**

The following statements are all true:

```lean
example : (255 : [UInt8]](#manual-UInt8___ofBitVec)) = 255 := by⊢ 255 [=]](#manual-Eq___refl) 255 [rfl]](#manual-rfl)All goals completed! 🐙
example : (256 : [UInt8]](#manual-UInt8___ofBitVec)) = 0 := by⊢ 256 [=]](#manual-Eq___refl) 0 [rfl]](#manual-rfl)All goals completed! 🐙
example : (257 : [UInt8]](#manual-UInt8___ofBitVec)) = 1 := by⊢ 257 [=]](#manual-Eq___refl) 1 [rfl]](#manual-rfl)All goals completed! 🐙
example : (0x7f : [Int8]](#manual-Int8___ofUInt8)) = 127 := by⊢ 127 [=]](#manual-Eq___refl) 127 [rfl]](#manual-rfl)All goals completed! 🐙
example : (0x8f : [Int8]](#manual-Int8___ofUInt8)) = -113 := by⊢ 143 [=]](#manual-Eq___refl) -113 [rfl]](#manual-rfl)All goals completed! 🐙
example : (0xff : [Int8]](#manual-Int8___ofUInt8)) = -1 := by⊢ 255 [=]](#manual-Eq___refl) -1 [rfl]](#manual-rfl)All goals completed! 🐙
```

### 20.4.4. API Reference {#manual-The-Lean-Language-Reference--Basic-Types--Fixed-Precision-Integers--API-Reference}

#### 20.4.4.1. Sizes {#manual-The-Lean-Language-Reference--Basic-Types--Fixed-Precision-Integers--API-Reference--Sizes}

Each fixed-width integer has a *size*, which is the number of distinct values that can be represented by the type.
This is not equivalent to C's `sizeof` operator, which instead determines how many bytes the type occupies.

def

```lean
[USize.size]](#manual-USize___size) : [Nat]](#manual-Nat___zero)



[USize.size]](#manual-USize___size) : [Nat]](#manual-Nat___zero)
```

The number of distinct values representable by `[USize]](#manual-USize___ofBitVec)`, that is, `2^[System.Platform.numBits](https://lean-lang.org/doc/reference/latest/IO/System-and-Platform-Information/#System___Platform___numBits)`.

def

```lean
[ISize.size]](#manual-ISize___size) : [Nat]](#manual-Nat___zero)



[ISize.size]](#manual-ISize___size) : [Nat]](#manual-Nat___zero)
```

The number of distinct values representable by `[ISize]](#manual-ISize___ofUSize)`, that is, `2^[System.Platform.numBits](https://lean-lang.org/doc/reference/latest/IO/System-and-Platform-Information/#System___Platform___numBits)`.

def

```lean
[UInt8.size]](#manual-UInt8___size) : [Nat]](#manual-Nat___zero)



[UInt8.size]](#manual-UInt8___size) : [Nat]](#manual-Nat___zero)
```

The number of distinct values representable by `[UInt8]](#manual-UInt8___ofBitVec)`, that is, `2^8 = 256`.

def

```lean
[Int8.size]](#manual-Int8___size) : [Nat]](#manual-Nat___zero)



[Int8.size]](#manual-Int8___size) : [Nat]](#manual-Nat___zero)
```

The number of distinct values representable by `[Int8]](#manual-Int8___ofUInt8)`, that is, `2^8 = 256`.

def

```lean
[UInt16.size]](#manual-UInt16___size) : [Nat]](#manual-Nat___zero)



[UInt16.size]](#manual-UInt16___size) : [Nat]](#manual-Nat___zero)
```

The number of distinct values representable by `[UInt16]](#manual-UInt16___ofBitVec)`, that is, `2^16 = 65536`.

def

```lean
[Int16.size]](#manual-Int16___size) : [Nat]](#manual-Nat___zero)



[Int16.size]](#manual-Int16___size) : [Nat]](#manual-Nat___zero)
```

The number of distinct values representable by `[Int16]](#manual-Int16___ofUInt16)`, that is, `2^16 = 65536`.

def

```lean
[UInt32.size]](#manual-UInt32___size) : [Nat]](#manual-Nat___zero)



[UInt32.size]](#manual-UInt32___size) : [Nat]](#manual-Nat___zero)
```

The number of distinct values representable by `[UInt32]](#manual-UInt32___ofBitVec)`, that is, `2^32 = 4294967296`.

def

```lean
[Int32.size]](#manual-Int32___size) : [Nat]](#manual-Nat___zero)



[Int32.size]](#manual-Int32___size) : [Nat]](#manual-Nat___zero)
```

The number of distinct values representable by `[Int32]](#manual-Int32___ofUInt32)`, that is, `2^32 = 4294967296`.

def

```lean
[UInt64.size]](#manual-UInt64___size) : [Nat]](#manual-Nat___zero)



[UInt64.size]](#manual-UInt64___size) : [Nat]](#manual-Nat___zero)
```

The number of distinct values representable by `[UInt64]](#manual-UInt64___ofBitVec)`, that is, `2^64 = 18446744073709551616`.

def

```lean
[Int64.size]](#manual-Int64___size) : [Nat]](#manual-Nat___zero)



[Int64.size]](#manual-Int64___size) : [Nat]](#manual-Nat___zero)
```

The number of distinct values representable by `[Int64]](#manual-Int64___ofUInt64)`, that is, `2^64 = 18446744073709551616`.

#### 20.4.4.2. Ranges {#manual-The-Lean-Language-Reference--Basic-Types--Fixed-Precision-Integers--API-Reference--Ranges}

def

```lean
[ISize.minValue]](#manual-ISize___minValue) : [ISize]](#manual-ISize___ofUSize)



[ISize.minValue]](#manual-ISize___minValue) : [ISize]](#manual-ISize___ofUSize)
```

The smallest number that `[ISize]](#manual-ISize___ofUSize)` can represent: `-2^([System.Platform.numBits](https://lean-lang.org/doc/reference/latest/IO/System-and-Platform-Information/#System___Platform___numBits) - 1)`.

def

```lean
[ISize.maxValue]](#manual-ISize___maxValue) : [ISize]](#manual-ISize___ofUSize)



[ISize.maxValue]](#manual-ISize___maxValue) : [ISize]](#manual-ISize___ofUSize)
```

The largest number that `[ISize]](#manual-ISize___ofUSize)` can represent: `2^([System.Platform.numBits](https://lean-lang.org/doc/reference/latest/IO/System-and-Platform-Information/#System___Platform___numBits) - 1) - 1`.

def

```lean
[Int8.minValue]](#manual-Int8___minValue) : [Int8]](#manual-Int8___ofUInt8)



[Int8.minValue]](#manual-Int8___minValue) : [Int8]](#manual-Int8___ofUInt8)
```

The smallest number that `[Int8]](#manual-Int8___ofUInt8)` can represent: `-2^7 = -128`.

def

```lean
[Int8.maxValue]](#manual-Int8___maxValue) : [Int8]](#manual-Int8___ofUInt8)



[Int8.maxValue]](#manual-Int8___maxValue) : [Int8]](#manual-Int8___ofUInt8)
```

The largest number that `[Int8]](#manual-Int8___ofUInt8)` can represent: `2^7 - 1 = 127`.

def

```lean
[Int16.minValue]](#manual-Int16___minValue) : [Int16]](#manual-Int16___ofUInt16)



[Int16.minValue]](#manual-Int16___minValue) : [Int16]](#manual-Int16___ofUInt16)
```

The smallest number that `[Int16]](#manual-Int16___ofUInt16)` can represent: `-2^15 = -32768`.

def

```lean
[Int16.maxValue]](#manual-Int16___maxValue) : [Int16]](#manual-Int16___ofUInt16)



[Int16.maxValue]](#manual-Int16___maxValue) : [Int16]](#manual-Int16___ofUInt16)
```

The largest number that `[Int16]](#manual-Int16___ofUInt16)` can represent: `2^15 - 1 = 32767`.

def

```lean
[Int32.minValue]](#manual-Int32___minValue) : [Int32]](#manual-Int32___ofUInt32)



[Int32.minValue]](#manual-Int32___minValue) : [Int32]](#manual-Int32___ofUInt32)
```

The smallest number that `[Int32]](#manual-Int32___ofUInt32)` can represent: `-2^31 = -2147483648`.

def

```lean
[Int32.maxValue]](#manual-Int32___maxValue) : [Int32]](#manual-Int32___ofUInt32)



[Int32.maxValue]](#manual-Int32___maxValue) : [Int32]](#manual-Int32___ofUInt32)
```

The largest number that `[Int32]](#manual-Int32___ofUInt32)` can represent: `2^31 - 1 = 2147483647`.

def

```lean
[Int64.minValue]](#manual-Int64___minValue) : [Int64]](#manual-Int64___ofUInt64)



[Int64.minValue]](#manual-Int64___minValue) : [Int64]](#manual-Int64___ofUInt64)
```

The smallest number that `[Int64]](#manual-Int64___ofUInt64)` can represent: `-2^63 = -9223372036854775808`.

def

```lean
[Int64.maxValue]](#manual-Int64___maxValue) : [Int64]](#manual-Int64___ofUInt64)



[Int64.maxValue]](#manual-Int64___maxValue) : [Int64]](#manual-Int64___ofUInt64)
```

The largest number that `[Int64]](#manual-Int64___ofUInt64)` can represent: `2^63 - 1 = 9223372036854775807`.

#### 20.4.4.3. Conversions {#manual-The-Lean-Language-Reference--Basic-Types--Fixed-Precision-Integers--API-Reference--Conversions}

##### 20.4.4.3.1. To and From `Int` {#manual-The-Lean-Language-Reference--Basic-Types--Fixed-Precision-Integers--API-Reference--Conversions--To-and-From--Int}

def

```lean
[ISize.toInt]](#manual-ISize___toInt) (i : [ISize]](#manual-ISize___ofUSize)) : [Int]](#manual-Int___ofNat)



[ISize.toInt]](#manual-ISize___toInt) (i : [ISize]](#manual-ISize___ofUSize)) : [Int]](#manual-Int___ofNat)
```

Converts a word-sized signed integer to an arbitrary-precision integer that denotes the same number.

This function is overridden at runtime with an efficient implementation.

def

```lean
[Int8.toInt]](#manual-Int8___toInt) (i : [Int8]](#manual-Int8___ofUInt8)) : [Int]](#manual-Int___ofNat)



[Int8.toInt]](#manual-Int8___toInt) (i : [Int8]](#manual-Int8___ofUInt8)) : [Int]](#manual-Int___ofNat)
```

Converts an 8-bit signed integer to an arbitrary-precision integer that denotes the same number.

This function is overridden at runtime with an efficient implementation.

def

```lean
[Int16.toInt]](#manual-Int16___toInt) (i : [Int16]](#manual-Int16___ofUInt16)) : [Int]](#manual-Int___ofNat)



[Int16.toInt]](#manual-Int16___toInt) (i : [Int16]](#manual-Int16___ofUInt16)) : [Int]](#manual-Int___ofNat)
```

Converts a 16-bit signed integer to an arbitrary-precision integer that denotes the same number.

This function is overridden at runtime with an efficient implementation.

def

```lean
[Int32.toInt]](#manual-Int32___toInt) (i : [Int32]](#manual-Int32___ofUInt32)) : [Int]](#manual-Int___ofNat)



[Int32.toInt]](#manual-Int32___toInt) (i : [Int32]](#manual-Int32___ofUInt32)) : [Int]](#manual-Int___ofNat)
```

Converts a 32-bit signed integer to an arbitrary-precision integer that denotes the same number.

This function is overridden at runtime with an efficient implementation.

def

```lean
[Int64.toInt]](#manual-Int64___toInt) (i : [Int64]](#manual-Int64___ofUInt64)) : [Int]](#manual-Int___ofNat)



[Int64.toInt]](#manual-Int64___toInt) (i : [Int64]](#manual-Int64___ofUInt64)) : [Int]](#manual-Int___ofNat)
```

Converts a 64-bit signed integer to an arbitrary-precision integer that denotes the same number.

This function is overridden at runtime with an efficient implementation.

def

```lean
[ISize.ofInt]](#manual-ISize___ofInt) (i : [Int]](#manual-Int___ofNat)) : [ISize]](#manual-ISize___ofUSize)



[ISize.ofInt]](#manual-ISize___ofInt) (i : [Int]](#manual-Int___ofNat)) : [ISize]](#manual-ISize___ofUSize)
```

Converts an arbitrary-precision integer to a word-sized signed integer, wrapping around on over- or
underflow.

This function is overridden at runtime with an efficient implementation.

def

```lean
[Int8.ofInt]](#manual-Int8___ofInt) (i : [Int]](#manual-Int___ofNat)) : [Int8]](#manual-Int8___ofUInt8)



[Int8.ofInt]](#manual-Int8___ofInt) (i : [Int]](#manual-Int___ofNat)) : [Int8]](#manual-Int8___ofUInt8)
```

Converts an arbitrary-precision integer to an 8-bit integer, wrapping on overflow or underflow.

This function is overridden at runtime with an efficient implementation.

Examples:

- `[Int8.ofInt]](#manual-Int8___ofInt) 48 = 48`
- `[Int8.ofInt]](#manual-Int8___ofInt) (-115) = -115`
- `[Int8.ofInt]](#manual-Int8___ofInt) (-129) = 127`
- `[Int8.ofInt]](#manual-Int8___ofInt) (128) = -128`

def

```lean
[Int16.ofInt]](#manual-Int16___ofInt) (i : [Int]](#manual-Int___ofNat)) : [Int16]](#manual-Int16___ofUInt16)



[Int16.ofInt]](#manual-Int16___ofInt) (i : [Int]](#manual-Int___ofNat)) : [Int16]](#manual-Int16___ofUInt16)
```

Converts an arbitrary-precision integer to a 16-bit signed integer, wrapping on overflow or underflow.

This function is overridden at runtime with an efficient implementation.

Examples:

- `[Int16.ofInt]](#manual-Int16___ofInt) 48 = 48`
- `[Int16.ofInt]](#manual-Int16___ofInt) (-129) = -129`
- `[Int16.ofInt]](#manual-Int16___ofInt) (128) = 128`
- `[Int16.ofInt]](#manual-Int16___ofInt) 70000 = 4464`
- `[Int16.ofInt]](#manual-Int16___ofInt) (-40000) = 25536`

def

```lean
[Int32.ofInt]](#manual-Int32___ofInt) (i : [Int]](#manual-Int___ofNat)) : [Int32]](#manual-Int32___ofUInt32)



[Int32.ofInt]](#manual-Int32___ofInt) (i : [Int]](#manual-Int___ofNat)) : [Int32]](#manual-Int32___ofUInt32)
```

Converts an arbitrary-precision integer to a 32-bit integer, wrapping on overflow or underflow.

This function is overridden at runtime with an efficient implementation.

Examples:

- `[Int32.ofInt]](#manual-Int32___ofInt) 48 = 48`
- `[Int32.ofInt]](#manual-Int32___ofInt) (-129) = -129`
- `[Int32.ofInt]](#manual-Int32___ofInt) 70000 = 70000`
- `[Int32.ofInt]](#manual-Int32___ofInt) (-40000) = -40000`
- `[Int32.ofInt]](#manual-Int32___ofInt) 2147483648 = -2147483648`
- `[Int32.ofInt]](#manual-Int32___ofInt) (-2147483649) = 2147483647`

def

```lean
[Int64.ofInt]](#manual-Int64___ofInt) (i : [Int]](#manual-Int___ofNat)) : [Int64]](#manual-Int64___ofUInt64)



[Int64.ofInt]](#manual-Int64___ofInt) (i : [Int]](#manual-Int___ofNat)) : [Int64]](#manual-Int64___ofUInt64)
```

Converts an arbitrary-precision integer to a 64-bit integer, wrapping on overflow or underflow.

This function is overridden at runtime with an efficient implementation.

Examples:

- `[Int64.ofInt]](#manual-Int64___ofInt) 48 = 48`
- `[Int64.ofInt]](#manual-Int64___ofInt) (-40_000) = -40_000`
- `[Int64.ofInt]](#manual-Int64___ofInt) 2_147_483_648 = 2_147_483_648`
- `[Int64.ofInt]](#manual-Int64___ofInt) (-2_147_483_649) = -2_147_483_649`
- `[Int64.ofInt]](#manual-Int64___ofInt) 9_223_372_036_854_775_808 = -9_223_372_036_854_775_808`
- `[Int64.ofInt]](#manual-Int64___ofInt) (-9_223_372_036_854_775_809) = 9_223_372_036_854_775_807`

def

```lean
[ISize.ofIntClamp]](#manual-ISize___ofIntClamp) (i : [Int]](#manual-Int___ofNat)) : [ISize]](#manual-ISize___ofUSize)



[ISize.ofIntClamp]](#manual-ISize___ofIntClamp) (i : [Int]](#manual-Int___ofNat)) : [ISize]](#manual-ISize___ofUSize)
```

Constructs an `[ISize]](#manual-ISize___ofUSize)` from an `[Int]](#manual-Int___ofNat)`, clamping if the value is too small or too large.

def

```lean
[Int8.ofIntClamp]](#manual-Int8___ofIntClamp) (i : [Int]](#manual-Int___ofNat)) : [Int8]](#manual-Int8___ofUInt8)



[Int8.ofIntClamp]](#manual-Int8___ofIntClamp) (i : [Int]](#manual-Int___ofNat)) : [Int8]](#manual-Int8___ofUInt8)
```

Constructs an `[Int8]](#manual-Int8___ofUInt8)` from an `[Int]](#manual-Int___ofNat)`, clamping if the value is too small or too large.

def

```lean
[Int16.ofIntClamp]](#manual-Int16___ofIntClamp) (i : [Int]](#manual-Int___ofNat)) : [Int16]](#manual-Int16___ofUInt16)



[Int16.ofIntClamp]](#manual-Int16___ofIntClamp) (i : [Int]](#manual-Int___ofNat)) : [Int16]](#manual-Int16___ofUInt16)
```

Constructs an `[Int16]](#manual-Int16___ofUInt16)` from an `[Int]](#manual-Int___ofNat)`, clamping if the value is too small or too large.

def

```lean
[Int32.ofIntClamp]](#manual-Int32___ofIntClamp) (i : [Int]](#manual-Int___ofNat)) : [Int32]](#manual-Int32___ofUInt32)



[Int32.ofIntClamp]](#manual-Int32___ofIntClamp) (i : [Int]](#manual-Int___ofNat)) : [Int32]](#manual-Int32___ofUInt32)
```

Constructs an `[Int32]](#manual-Int32___ofUInt32)` from an `[Int]](#manual-Int___ofNat)`, clamping if the value is too small or too large.

def

```lean
[Int64.ofIntClamp]](#manual-Int64___ofIntClamp) (i : [Int]](#manual-Int___ofNat)) : [Int64]](#manual-Int64___ofUInt64)



[Int64.ofIntClamp]](#manual-Int64___ofIntClamp) (i : [Int]](#manual-Int___ofNat)) : [Int64]](#manual-Int64___ofUInt64)
```

Constructs an `[Int64]](#manual-Int64___ofUInt64)` from an `[Int]](#manual-Int___ofNat)`, clamping if the value is too small or too large.

def

```lean
[ISize.ofIntLE]](#manual-ISize___ofIntLE) (i : [Int]](#manual-Int___ofNat)) (_hl : [ISize.minValue]](#manual-ISize___minValue).[toInt]](#manual-ISize___toInt) [≤]](#manual-LE___mk) i)
  (_hr : i [≤]](#manual-LE___mk) [ISize.maxValue]](#manual-ISize___maxValue).[toInt]](#manual-ISize___toInt)) : [ISize]](#manual-ISize___ofUSize)



[ISize.ofIntLE]](#manual-ISize___ofIntLE) (i : [Int]](#manual-Int___ofNat))
  (_hl : [ISize.minValue]](#manual-ISize___minValue).[toInt]](#manual-ISize___toInt) [≤]](#manual-LE___mk) i)
  (_hr : i [≤]](#manual-LE___mk) [ISize.maxValue]](#manual-ISize___maxValue).[toInt]](#manual-ISize___toInt)) : [ISize]](#manual-ISize___ofUSize)
```

Constructs an `[ISize]](#manual-ISize___ofUSize)` from an `[Int]](#manual-Int___ofNat)` that is known to be in bounds.

def

```lean
[Int8.ofIntLE]](#manual-Int8___ofIntLE) (i : [Int]](#manual-Int___ofNat)) (_hl : [Int8.minValue]](#manual-Int8___minValue).[toInt]](#manual-Int8___toInt) [≤]](#manual-LE___mk) i)
  (_hr : i [≤]](#manual-LE___mk) [Int8.maxValue]](#manual-Int8___maxValue).[toInt]](#manual-Int8___toInt)) : [Int8]](#manual-Int8___ofUInt8)



[Int8.ofIntLE]](#manual-Int8___ofIntLE) (i : [Int]](#manual-Int___ofNat))
  (_hl : [Int8.minValue]](#manual-Int8___minValue).[toInt]](#manual-Int8___toInt) [≤]](#manual-LE___mk) i)
  (_hr : i [≤]](#manual-LE___mk) [Int8.maxValue]](#manual-Int8___maxValue).[toInt]](#manual-Int8___toInt)) : [Int8]](#manual-Int8___ofUInt8)
```

Constructs an `[Int8]](#manual-Int8___ofUInt8)` from an `[Int]](#manual-Int___ofNat)` that is known to be in bounds.

def

```lean
[Int16.ofIntLE]](#manual-Int16___ofIntLE) (i : [Int]](#manual-Int___ofNat)) (_hl : [Int16.minValue]](#manual-Int16___minValue).[toInt]](#manual-Int16___toInt) [≤]](#manual-LE___mk) i)
  (_hr : i [≤]](#manual-LE___mk) [Int16.maxValue]](#manual-Int16___maxValue).[toInt]](#manual-Int16___toInt)) : [Int16]](#manual-Int16___ofUInt16)



[Int16.ofIntLE]](#manual-Int16___ofIntLE) (i : [Int]](#manual-Int___ofNat))
  (_hl : [Int16.minValue]](#manual-Int16___minValue).[toInt]](#manual-Int16___toInt) [≤]](#manual-LE___mk) i)
  (_hr : i [≤]](#manual-LE___mk) [Int16.maxValue]](#manual-Int16___maxValue).[toInt]](#manual-Int16___toInt)) : [Int16]](#manual-Int16___ofUInt16)
```

Constructs an `[Int16]](#manual-Int16___ofUInt16)` from an `[Int]](#manual-Int___ofNat)` that is known to be in bounds.

def

```lean
[Int32.ofIntLE]](#manual-Int32___ofIntLE) (i : [Int]](#manual-Int___ofNat)) (_hl : [Int32.minValue]](#manual-Int32___minValue).[toInt]](#manual-Int32___toInt) [≤]](#manual-LE___mk) i)
  (_hr : i [≤]](#manual-LE___mk) [Int32.maxValue]](#manual-Int32___maxValue).[toInt]](#manual-Int32___toInt)) : [Int32]](#manual-Int32___ofUInt32)



[Int32.ofIntLE]](#manual-Int32___ofIntLE) (i : [Int]](#manual-Int___ofNat))
  (_hl : [Int32.minValue]](#manual-Int32___minValue).[toInt]](#manual-Int32___toInt) [≤]](#manual-LE___mk) i)
  (_hr : i [≤]](#manual-LE___mk) [Int32.maxValue]](#manual-Int32___maxValue).[toInt]](#manual-Int32___toInt)) : [Int32]](#manual-Int32___ofUInt32)
```

Constructs an `[Int32]](#manual-Int32___ofUInt32)` from an `[Int]](#manual-Int___ofNat)` that is known to be in bounds.

def

```lean
[Int64.ofIntLE]](#manual-Int64___ofIntLE) (i : [Int]](#manual-Int___ofNat)) (_hl : [Int64.minValue]](#manual-Int64___minValue).[toInt]](#manual-Int64___toInt) [≤]](#manual-LE___mk) i)
  (_hr : i [≤]](#manual-LE___mk) [Int64.maxValue]](#manual-Int64___maxValue).[toInt]](#manual-Int64___toInt)) : [Int64]](#manual-Int64___ofUInt64)



[Int64.ofIntLE]](#manual-Int64___ofIntLE) (i : [Int]](#manual-Int___ofNat))
  (_hl : [Int64.minValue]](#manual-Int64___minValue).[toInt]](#manual-Int64___toInt) [≤]](#manual-LE___mk) i)
  (_hr : i [≤]](#manual-LE___mk) [Int64.maxValue]](#manual-Int64___maxValue).[toInt]](#manual-Int64___toInt)) : [Int64]](#manual-Int64___ofUInt64)
```

Constructs an `[Int64]](#manual-Int64___ofUInt64)` from an `[Int]](#manual-Int___ofNat)` that is known to be in bounds.

##### 20.4.4.3.2. To and From `Nat` {#manual-The-Lean-Language-Reference--Basic-Types--Fixed-Precision-Integers--API-Reference--Conversions--To-and-From--Nat}

def

```lean
[USize.ofNat]](#manual-USize___ofNat) (n : [Nat]](#manual-Nat___zero)) : [USize]](#manual-USize___ofBitVec)



[USize.ofNat]](#manual-USize___ofNat) (n : [Nat]](#manual-Nat___zero)) : [USize]](#manual-USize___ofBitVec)
```

Converts an arbitrary-precision natural number to an unsigned word-sized integer, wrapping around on
overflow.

This function is overridden at runtime with an efficient implementation.

def

```lean
[ISize.ofNat]](#manual-ISize___ofNat) (n : [Nat]](#manual-Nat___zero)) : [ISize]](#manual-ISize___ofUSize)



[ISize.ofNat]](#manual-ISize___ofNat) (n : [Nat]](#manual-Nat___zero)) : [ISize]](#manual-ISize___ofUSize)
```

Converts an arbitrary-precision natural number to a word-sized signed integer, wrapping around on
overflow.

This function is overridden at runtime with an efficient implementation.

def

```lean
[UInt8.ofNat]](#manual-UInt8___ofNat) (n : [Nat]](#manual-Nat___zero)) : [UInt8]](#manual-UInt8___ofBitVec)



[UInt8.ofNat]](#manual-UInt8___ofNat) (n : [Nat]](#manual-Nat___zero)) : [UInt8]](#manual-UInt8___ofBitVec)
```

Converts a natural number to an 8-bit unsigned integer, wrapping on overflow.

This function is overridden at runtime with an efficient implementation.

Examples:

- `[UInt8.ofNat]](#manual-UInt8___ofNat) 5 = 5`
- `[UInt8.ofNat]](#manual-UInt8___ofNat) 255 = 255`
- `[UInt8.ofNat]](#manual-UInt8___ofNat) 256 = 0`
- `[UInt8.ofNat]](#manual-UInt8___ofNat) 259 = 3`
- `[UInt8.ofNat]](#manual-UInt8___ofNat) 32770 = 2`

def

```lean
[Int8.ofNat]](#manual-Int8___ofNat) (n : [Nat]](#manual-Nat___zero)) : [Int8]](#manual-Int8___ofUInt8)



[Int8.ofNat]](#manual-Int8___ofNat) (n : [Nat]](#manual-Nat___zero)) : [Int8]](#manual-Int8___ofUInt8)
```

Converts a natural number to an 8-bit signed integer, wrapping around on overflow.

This function is overridden at runtime with an efficient implementation.

Examples:

- `[Int8.ofNat]](#manual-Int8___ofNat) 53 = 53`
- `[Int8.ofNat]](#manual-Int8___ofNat) 127 = 127`
- `[Int8.ofNat]](#manual-Int8___ofNat) 128 = -128`
- `[Int8.ofNat]](#manual-Int8___ofNat) 255 = -1`

def

```lean
[UInt16.ofNat]](#manual-UInt16___ofNat) (n : [Nat]](#manual-Nat___zero)) : [UInt16]](#manual-UInt16___ofBitVec)



[UInt16.ofNat]](#manual-UInt16___ofNat) (n : [Nat]](#manual-Nat___zero)) : [UInt16]](#manual-UInt16___ofBitVec)
```

Converts a natural number to a 16-bit unsigned integer, wrapping on overflow.

This function is overridden at runtime with an efficient implementation.

Examples:

- `[UInt16.ofNat]](#manual-UInt16___ofNat) 5 = 5`
- `[UInt16.ofNat]](#manual-UInt16___ofNat) 255 = 255`
- `[UInt16.ofNat]](#manual-UInt16___ofNat) 32770 = 32770`
- `[UInt16.ofNat]](#manual-UInt16___ofNat) 65537 = 1`

def

```lean
[Int16.ofNat]](#manual-Int16___ofNat) (n : [Nat]](#manual-Nat___zero)) : [Int16]](#manual-Int16___ofUInt16)



[Int16.ofNat]](#manual-Int16___ofNat) (n : [Nat]](#manual-Nat___zero)) : [Int16]](#manual-Int16___ofUInt16)
```

Converts a natural number to a 16-bit signed integer, wrapping around on overflow.

This function is overridden at runtime with an efficient implementation.

Examples:

- `[Int16.ofNat]](#manual-Int16___ofNat) 127 = 127`
- `[Int16.ofNat]](#manual-Int16___ofNat) 32767 = 32767`
- `[Int16.ofNat]](#manual-Int16___ofNat) 32768 = -32768`
- `[Int16.ofNat]](#manual-Int16___ofNat) 32770 = -32766`

def

```lean
[UInt32.ofNat]](#manual-UInt32___ofNat) (n : [Nat]](#manual-Nat___zero)) : [UInt32]](#manual-UInt32___ofBitVec)



[UInt32.ofNat]](#manual-UInt32___ofNat) (n : [Nat]](#manual-Nat___zero)) : [UInt32]](#manual-UInt32___ofBitVec)
```

Converts a natural number to a 32-bit unsigned integer, wrapping on overflow.

This function is overridden at runtime with an efficient implementation.

Examples:

- `[UInt32.ofNat]](#manual-UInt32___ofNat) 5 = 5`
- `[UInt32.ofNat]](#manual-UInt32___ofNat) 65539 = 65539`
- `[UInt32.ofNat]](#manual-UInt32___ofNat) 4_294_967_299 = 3`

def

```lean
[Int32.ofNat]](#manual-Int32___ofNat) (n : [Nat]](#manual-Nat___zero)) : [Int32]](#manual-Int32___ofUInt32)



[Int32.ofNat]](#manual-Int32___ofNat) (n : [Nat]](#manual-Nat___zero)) : [Int32]](#manual-Int32___ofUInt32)
```

Converts a natural number to a 32-bit signed integer, wrapping around on overflow.

This function is overridden at runtime with an efficient implementation.

Examples:

- `[Int32.ofNat]](#manual-Int32___ofNat) 127 = 127`
- `[Int32.ofNat]](#manual-Int32___ofNat) 32770 = 32770`
- `[Int32.ofNat]](#manual-Int32___ofNat) 2_147_483_647 = 2_147_483_647`
- `[Int32.ofNat]](#manual-Int32___ofNat) 2_147_483_648 = -2_147_483_648`

def

```lean
[UInt64.ofNat]](#manual-UInt64___ofNat) (n : [Nat]](#manual-Nat___zero)) : [UInt64]](#manual-UInt64___ofBitVec)



[UInt64.ofNat]](#manual-UInt64___ofNat) (n : [Nat]](#manual-Nat___zero)) : [UInt64]](#manual-UInt64___ofBitVec)
```

Converts a natural number to a 64-bit unsigned integer, wrapping on overflow.

This function is overridden at runtime with an efficient implementation.

Examples:

- `[UInt64.ofNat]](#manual-UInt64___ofNat) 5 = 5`
- `[UInt64.ofNat]](#manual-UInt64___ofNat) 65539 = 65539`
- `[UInt64.ofNat]](#manual-UInt64___ofNat) 4_294_967_299 = 4_294_967_299`
- `[UInt64.ofNat]](#manual-UInt64___ofNat) 18_446_744_073_709_551_620 = 4`

def

```lean
[Int64.ofNat]](#manual-Int64___ofNat) (n : [Nat]](#manual-Nat___zero)) : [Int64]](#manual-Int64___ofUInt64)



[Int64.ofNat]](#manual-Int64___ofNat) (n : [Nat]](#manual-Nat___zero)) : [Int64]](#manual-Int64___ofUInt64)
```

Converts a natural number to a 64-bit signed integer, wrapping around to negative numbers on
overflow.

This function is overridden at runtime with an efficient implementation.

Examples:

- `[Int64.ofNat]](#manual-Int64___ofNat) 127 = 127`
- `[Int64.ofNat]](#manual-Int64___ofNat) 2_147_483_648 = 2_147_483_648`
- `[Int64.ofNat]](#manual-Int64___ofNat) 9_223_372_036_854_775_807 = 9_223_372_036_854_775_807`
- `[Int64.ofNat]](#manual-Int64___ofNat) 9_223_372_036_854_775_808 = -9_223_372_036_854_775_808`
- `[Int64.ofNat]](#manual-Int64___ofNat) 18_446_744_073_709_551_618 = 0`

def

```lean
[USize.ofNat32]](#manual-USize___ofNat32) (n : [Nat]](#manual-Nat___zero)) (h : n [<]](#manual-LT___mk) 4294967296) : [USize]](#manual-USize___ofBitVec)



[USize.ofNat32]](#manual-USize___ofNat32) (n : [Nat]](#manual-Nat___zero))
  (h : n [<]](#manual-LT___mk) 4294967296) : [USize]](#manual-USize___ofBitVec)
```

Converts a natural number to a `[USize]](#manual-USize___ofBitVec)`. Overflow is impossible on any supported platform because
`[USize.size]](#manual-USize___size)` is either `2^32` or `2^64`.

This function is overridden at runtime with an efficient implementation.

def

```lean
[USize.ofNatLT]](#manual-USize___ofNatLT) (n : [Nat]](#manual-Nat___zero)) (h : n [<]](#manual-LT___mk) [USize.size]](#manual-USize___size)) : [USize]](#manual-USize___ofBitVec)



[USize.ofNatLT]](#manual-USize___ofNatLT) (n : [Nat]](#manual-Nat___zero))
  (h : n [<]](#manual-LT___mk) [USize.size]](#manual-USize___size)) : [USize]](#manual-USize___ofBitVec)
```

Converts a natural number to a `[USize]](#manual-USize___ofBitVec)`. Requires a proof that the number is small enough to be
representable without overflow.

This function is overridden at runtime with an efficient implementation.

def

```lean
[UInt8.ofNatLT]](#manual-UInt8___ofNatLT) (n : [Nat]](#manual-Nat___zero)) (h : n [<]](#manual-LT___mk) [UInt8.size]](#manual-UInt8___size)) : [UInt8]](#manual-UInt8___ofBitVec)



[UInt8.ofNatLT]](#manual-UInt8___ofNatLT) (n : [Nat]](#manual-Nat___zero))
  (h : n [<]](#manual-LT___mk) [UInt8.size]](#manual-UInt8___size)) : [UInt8]](#manual-UInt8___ofBitVec)
```

Converts a natural number to an 8-bit unsigned integer. Requires a proof that the number is small
enough to be representable without overflow; it must be smaller than `2^8`.

This function is overridden at runtime with an efficient implementation.

def

```lean
[UInt16.ofNatLT]](#manual-UInt16___ofNatLT) (n : [Nat]](#manual-Nat___zero)) (h : n [<]](#manual-LT___mk) [UInt16.size]](#manual-UInt16___size)) : [UInt16]](#manual-UInt16___ofBitVec)



[UInt16.ofNatLT]](#manual-UInt16___ofNatLT) (n : [Nat]](#manual-Nat___zero))
  (h : n [<]](#manual-LT___mk) [UInt16.size]](#manual-UInt16___size)) : [UInt16]](#manual-UInt16___ofBitVec)
```

Converts a natural number to a 16-bit unsigned integer. Requires a proof that the number is small
enough to be representable without overflow; it must be smaller than `2^16`.

This function is overridden at runtime with an efficient implementation.

def

```lean
[UInt32.ofNatLT]](#manual-UInt32___ofNatLT) (n : [Nat]](#manual-Nat___zero)) (h : n [<]](#manual-LT___mk) [UInt32.size]](#manual-UInt32___size)) : [UInt32]](#manual-UInt32___ofBitVec)



[UInt32.ofNatLT]](#manual-UInt32___ofNatLT) (n : [Nat]](#manual-Nat___zero))
  (h : n [<]](#manual-LT___mk) [UInt32.size]](#manual-UInt32___size)) : [UInt32]](#manual-UInt32___ofBitVec)
```

Converts a natural number to a 32-bit unsigned integer. Requires a proof that the number is small
enough to be representable without overflow; it must be smaller than `2^32`.

This function is overridden at runtime with an efficient implementation.

def

```lean
[UInt64.ofNatLT]](#manual-UInt64___ofNatLT) (n : [Nat]](#manual-Nat___zero)) (h : n [<]](#manual-LT___mk) [UInt64.size]](#manual-UInt64___size)) : [UInt64]](#manual-UInt64___ofBitVec)



[UInt64.ofNatLT]](#manual-UInt64___ofNatLT) (n : [Nat]](#manual-Nat___zero))
  (h : n [<]](#manual-LT___mk) [UInt64.size]](#manual-UInt64___size)) : [UInt64]](#manual-UInt64___ofBitVec)
```

Converts a natural number to a 64-bit unsigned integer. Requires a proof that the number is small
enough to be representable without overflow; it must be smaller than `2^64`.

This function is overridden at runtime with an efficient implementation.

def

```lean
[USize.ofNatClamp]](#manual-USize___ofNatClamp) (n : [Nat]](#manual-Nat___zero)) : [USize]](#manual-USize___ofBitVec)



[USize.ofNatClamp]](#manual-USize___ofNatClamp) (n : [Nat]](#manual-Nat___zero)) : [USize]](#manual-USize___ofBitVec)
```

Converts a natural number to `[USize]](#manual-USize___ofBitVec)`, returning the largest representable value if the number is too
large.

Returns `[USize.size]](#manual-USize___size) - 1`, which is `2^64 - 1` or `2^32 - 1` depending on the platform, for natural
numbers greater than or equal to `[USize.size]](#manual-USize___size)`.

def

```lean
[UInt8.ofNatClamp]](#manual-UInt8___ofNatClamp) (n : [Nat]](#manual-Nat___zero)) : [UInt8]](#manual-UInt8___ofBitVec)



[UInt8.ofNatClamp]](#manual-UInt8___ofNatClamp) (n : [Nat]](#manual-Nat___zero)) : [UInt8]](#manual-UInt8___ofBitVec)
```

Converts a natural number to an 8-bit unsigned integer, returning the largest representable value if
the number is too large.

Returns `2^8 - 1` for natural numbers greater than or equal to `2^8`.

def

```lean
[UInt16.ofNatClamp]](#manual-UInt16___ofNatClamp) (n : [Nat]](#manual-Nat___zero)) : [UInt16]](#manual-UInt16___ofBitVec)



[UInt16.ofNatClamp]](#manual-UInt16___ofNatClamp) (n : [Nat]](#manual-Nat___zero)) : [UInt16]](#manual-UInt16___ofBitVec)
```

Converts a natural number to a 16-bit unsigned integer, returning the largest representable value if
the number is too large.

Returns `2^16 - 1` for natural numbers greater than or equal to `2^16`.

def

```lean
[UInt32.ofNatClamp]](#manual-UInt32___ofNatClamp) (n : [Nat]](#manual-Nat___zero)) : [UInt32]](#manual-UInt32___ofBitVec)



[UInt32.ofNatClamp]](#manual-UInt32___ofNatClamp) (n : [Nat]](#manual-Nat___zero)) : [UInt32]](#manual-UInt32___ofBitVec)
```

Converts a natural number to a 32-bit unsigned integer, returning the largest representable value if
the number is too large.

Returns `2^32 - 1` for natural numbers greater than or equal to `2^32`.

def

```lean
[UInt64.ofNatClamp]](#manual-UInt64___ofNatClamp) (n : [Nat]](#manual-Nat___zero)) : [UInt64]](#manual-UInt64___ofBitVec)



[UInt64.ofNatClamp]](#manual-UInt64___ofNatClamp) (n : [Nat]](#manual-Nat___zero)) : [UInt64]](#manual-UInt64___ofBitVec)
```

Converts a natural number to a 64-bit unsigned integer, returning the largest representable value if
the number is too large.

Returns `2^64 - 1` for natural numbers greater than or equal to `2^64`.

def

```lean
[USize.toNat]](#manual-USize___toNat) (n : [USize]](#manual-USize___ofBitVec)) : [Nat]](#manual-Nat___zero)



[USize.toNat]](#manual-USize___toNat) (n : [USize]](#manual-USize___ofBitVec)) : [Nat]](#manual-Nat___zero)
```

Converts a word-sized unsigned integer to an arbitrary-precision natural number.

This function is overridden at runtime with an efficient implementation.

def

```lean
[ISize.toNatClampNeg]](#manual-ISize___toNatClampNeg) (i : [ISize]](#manual-ISize___ofUSize)) : [Nat]](#manual-Nat___zero)



[ISize.toNatClampNeg]](#manual-ISize___toNatClampNeg) (i : [ISize]](#manual-ISize___ofUSize)) : [Nat]](#manual-Nat___zero)
```

Converts a word-sized signed integer to a natural number, mapping all negative numbers to `0`.

Use `[ISize.toBitVec]](#manual-ISize___toBitVec)` to obtain the two's complement representation.

def

```lean
[UInt8.toNat]](#manual-UInt8___toNat) (n : [UInt8]](#manual-UInt8___ofBitVec)) : [Nat]](#manual-Nat___zero)



[UInt8.toNat]](#manual-UInt8___toNat) (n : [UInt8]](#manual-UInt8___ofBitVec)) : [Nat]](#manual-Nat___zero)
```

Converts an 8-bit unsigned integer to an arbitrary-precision natural number.

This function is overridden at runtime with an efficient implementation.

def

```lean
[Int8.toNatClampNeg]](#manual-Int8___toNatClampNeg) (i : [Int8]](#manual-Int8___ofUInt8)) : [Nat]](#manual-Nat___zero)



[Int8.toNatClampNeg]](#manual-Int8___toNatClampNeg) (i : [Int8]](#manual-Int8___ofUInt8)) : [Nat]](#manual-Nat___zero)
```

Converts an 8-bit signed integer to a natural number, mapping all negative numbers to `0`.

Use `[Int8.toBitVec]](#manual-Int8___toBitVec)` to obtain the two's complement representation.

def

```lean
[UInt16.toNat]](#manual-UInt16___toNat) (n : [UInt16]](#manual-UInt16___ofBitVec)) : [Nat]](#manual-Nat___zero)



[UInt16.toNat]](#manual-UInt16___toNat) (n : [UInt16]](#manual-UInt16___ofBitVec)) : [Nat]](#manual-Nat___zero)
```

Converts a 16-bit unsigned integer to an arbitrary-precision natural number.

This function is overridden at runtime with an efficient implementation.

def

```lean
[Int16.toNatClampNeg]](#manual-Int16___toNatClampNeg) (i : [Int16]](#manual-Int16___ofUInt16)) : [Nat]](#manual-Nat___zero)



[Int16.toNatClampNeg]](#manual-Int16___toNatClampNeg) (i : [Int16]](#manual-Int16___ofUInt16)) : [Nat]](#manual-Nat___zero)
```

Converts a 16-bit signed integer to a natural number, mapping all negative numbers to `0`.

Use `[Int16.toBitVec]](#manual-Int16___toBitVec)` to obtain the two's complement representation.

def

```lean
[UInt32.toNat]](#manual-UInt32___toNat) (n : [UInt32]](#manual-UInt32___ofBitVec)) : [Nat]](#manual-Nat___zero)



[UInt32.toNat]](#manual-UInt32___toNat) (n : [UInt32]](#manual-UInt32___ofBitVec)) : [Nat]](#manual-Nat___zero)
```

Converts a 32-bit unsigned integer to an arbitrary-precision natural number.

This function is overridden at runtime with an efficient implementation.

def

```lean
[Int32.toNatClampNeg]](#manual-Int32___toNatClampNeg) (i : [Int32]](#manual-Int32___ofUInt32)) : [Nat]](#manual-Nat___zero)



[Int32.toNatClampNeg]](#manual-Int32___toNatClampNeg) (i : [Int32]](#manual-Int32___ofUInt32)) : [Nat]](#manual-Nat___zero)
```

Converts a 32-bit signed integer to a natural number, mapping all negative numbers to `0`.

Use `[Int32.toBitVec]](#manual-Int32___toBitVec)` to obtain the two's complement representation.

def

```lean
[UInt64.toNat]](#manual-UInt64___toNat) (n : [UInt64]](#manual-UInt64___ofBitVec)) : [Nat]](#manual-Nat___zero)



[UInt64.toNat]](#manual-UInt64___toNat) (n : [UInt64]](#manual-UInt64___ofBitVec)) : [Nat]](#manual-Nat___zero)
```

Converts a 64-bit unsigned integer to an arbitrary-precision natural number.

This function is overridden at runtime with an efficient implementation.

def

```lean
[Int64.toNatClampNeg]](#manual-Int64___toNatClampNeg) (i : [Int64]](#manual-Int64___ofUInt64)) : [Nat]](#manual-Nat___zero)



[Int64.toNatClampNeg]](#manual-Int64___toNatClampNeg) (i : [Int64]](#manual-Int64___ofUInt64)) : [Nat]](#manual-Nat___zero)
```

Converts a 64-bit signed integer to a natural number, mapping all negative numbers to `0`.

Use `[Int64.toBitVec]](#manual-Int64___toBitVec)` to obtain the two's complement representation.

##### 20.4.4.3.3. To Other Fixed-Width Integers {#manual-The-Lean-Language-Reference--Basic-Types--Fixed-Precision-Integers--API-Reference--Conversions--To-Other-Fixed-Width-Integers}

def

```lean
[USize.toUInt8]](#manual-USize___toUInt8) (a : [USize]](#manual-USize___ofBitVec)) : [UInt8]](#manual-UInt8___ofBitVec)



[USize.toUInt8]](#manual-USize___toUInt8) (a : [USize]](#manual-USize___ofBitVec)) : [UInt8]](#manual-UInt8___ofBitVec)
```

Converts word-sized unsigned integers to 8-bit unsigned integers. Wraps around on overflow.

This function is overridden at runtime with an efficient implementation.

def

```lean
[USize.toUInt16]](#manual-USize___toUInt16) (a : [USize]](#manual-USize___ofBitVec)) : [UInt16]](#manual-UInt16___ofBitVec)



[USize.toUInt16]](#manual-USize___toUInt16) (a : [USize]](#manual-USize___ofBitVec)) : [UInt16]](#manual-UInt16___ofBitVec)
```

Converts word-sized unsigned integers to 16-bit unsigned integers. Wraps around on overflow.

This function is overridden at runtime with an efficient implementation.

def

```lean
[USize.toUInt32]](#manual-USize___toUInt32) (a : [USize]](#manual-USize___ofBitVec)) : [UInt32]](#manual-UInt32___ofBitVec)



[USize.toUInt32]](#manual-USize___toUInt32) (a : [USize]](#manual-USize___ofBitVec)) : [UInt32]](#manual-UInt32___ofBitVec)
```

Converts word-sized unsigned integers to 32-bit unsigned integers. Wraps around on overflow, which
might occur on 64-bit architectures.

This function is overridden at runtime with an efficient implementation.

def

```lean
[USize.toUInt64]](#manual-USize___toUInt64) (a : [USize]](#manual-USize___ofBitVec)) : [UInt64]](#manual-UInt64___ofBitVec)



[USize.toUInt64]](#manual-USize___toUInt64) (a : [USize]](#manual-USize___ofBitVec)) : [UInt64]](#manual-UInt64___ofBitVec)
```

Converts word-sized unsigned integers to 32-bit unsigned integers. This cannot overflow because
`[USize.size]](#manual-USize___size)` is either `2^32` or `2^64`.

This function is overridden at runtime with an efficient implementation.

def

```lean
[USize.toISize]](#manual-USize___toISize) (i : [USize]](#manual-USize___ofBitVec)) : [ISize]](#manual-ISize___ofUSize)



[USize.toISize]](#manual-USize___toISize) (i : [USize]](#manual-USize___ofBitVec)) : [ISize]](#manual-ISize___ofUSize)
```

Obtains the `[ISize]](#manual-ISize___ofUSize)` that is 2's complement equivalent to the `[USize]](#manual-USize___ofBitVec)`.

def

```lean
[UInt8.toInt8]](#manual-UInt8___toInt8) (i : [UInt8]](#manual-UInt8___ofBitVec)) : [Int8]](#manual-Int8___ofUInt8)



[UInt8.toInt8]](#manual-UInt8___toInt8) (i : [UInt8]](#manual-UInt8___ofBitVec)) : [Int8]](#manual-Int8___ofUInt8)
```

Obtains the `[Int8]](#manual-Int8___ofUInt8)` that is 2's complement equivalent to the `[UInt8]](#manual-UInt8___ofBitVec)`.

def

```lean
[UInt8.toUInt16]](#manual-UInt8___toUInt16) (a : [UInt8]](#manual-UInt8___ofBitVec)) : [UInt16]](#manual-UInt16___ofBitVec)



[UInt8.toUInt16]](#manual-UInt8___toUInt16) (a : [UInt8]](#manual-UInt8___ofBitVec)) : [UInt16]](#manual-UInt16___ofBitVec)
```

Converts 8-bit unsigned integers to 16-bit unsigned integers.

This function is overridden at runtime with an efficient implementation.

def

```lean
[UInt8.toUInt32]](#manual-UInt8___toUInt32) (a : [UInt8]](#manual-UInt8___ofBitVec)) : [UInt32]](#manual-UInt32___ofBitVec)



[UInt8.toUInt32]](#manual-UInt8___toUInt32) (a : [UInt8]](#manual-UInt8___ofBitVec)) : [UInt32]](#manual-UInt32___ofBitVec)
```

Converts 8-bit unsigned integers to 32-bit unsigned integers.

This function is overridden at runtime with an efficient implementation.

def

```lean
[UInt8.toUInt64]](#manual-UInt8___toUInt64) (a : [UInt8]](#manual-UInt8___ofBitVec)) : [UInt64]](#manual-UInt64___ofBitVec)



[UInt8.toUInt64]](#manual-UInt8___toUInt64) (a : [UInt8]](#manual-UInt8___ofBitVec)) : [UInt64]](#manual-UInt64___ofBitVec)
```

Converts 8-bit unsigned integers to 64-bit unsigned integers.

This function is overridden at runtime with an efficient implementation.

def

```lean
[UInt8.toUSize]](#manual-UInt8___toUSize) (a : [UInt8]](#manual-UInt8___ofBitVec)) : [USize]](#manual-USize___ofBitVec)



[UInt8.toUSize]](#manual-UInt8___toUSize) (a : [UInt8]](#manual-UInt8___ofBitVec)) : [USize]](#manual-USize___ofBitVec)
```

Converts 8-bit unsigned integers to word-sized unsigned integers.

This function is overridden at runtime with an efficient implementation.

def

```lean
[UInt16.toUInt8]](#manual-UInt16___toUInt8) (a : [UInt16]](#manual-UInt16___ofBitVec)) : [UInt8]](#manual-UInt8___ofBitVec)



[UInt16.toUInt8]](#manual-UInt16___toUInt8) (a : [UInt16]](#manual-UInt16___ofBitVec)) : [UInt8]](#manual-UInt8___ofBitVec)
```

Converts 16-bit unsigned integers to 8-bit unsigned integers. Wraps around on overflow.

This function is overridden at runtime with an efficient implementation.

def

```lean
[UInt16.toInt16]](#manual-UInt16___toInt16) (i : [UInt16]](#manual-UInt16___ofBitVec)) : [Int16]](#manual-Int16___ofUInt16)



[UInt16.toInt16]](#manual-UInt16___toInt16) (i : [UInt16]](#manual-UInt16___ofBitVec)) : [Int16]](#manual-Int16___ofUInt16)
```

Obtains the `[Int16]](#manual-Int16___ofUInt16)` that is 2's complement equivalent to the `[UInt16]](#manual-UInt16___ofBitVec)`.

def

```lean
[UInt16.toUInt32]](#manual-UInt16___toUInt32) (a : [UInt16]](#manual-UInt16___ofBitVec)) : [UInt32]](#manual-UInt32___ofBitVec)



[UInt16.toUInt32]](#manual-UInt16___toUInt32) (a : [UInt16]](#manual-UInt16___ofBitVec)) : [UInt32]](#manual-UInt32___ofBitVec)
```

Converts 16-bit unsigned integers to 32-bit unsigned integers.

This function is overridden at runtime with an efficient implementation.

def

```lean
[UInt16.toUInt64]](#manual-UInt16___toUInt64) (a : [UInt16]](#manual-UInt16___ofBitVec)) : [UInt64]](#manual-UInt64___ofBitVec)



[UInt16.toUInt64]](#manual-UInt16___toUInt64) (a : [UInt16]](#manual-UInt16___ofBitVec)) : [UInt64]](#manual-UInt64___ofBitVec)
```

Converts 16-bit unsigned integers to 64-bit unsigned integers. Wraps around on overflow.

This function is overridden at runtime with an efficient implementation.

def

```lean
[UInt16.toUSize]](#manual-UInt16___toUSize) (a : [UInt16]](#manual-UInt16___ofBitVec)) : [USize]](#manual-USize___ofBitVec)



[UInt16.toUSize]](#manual-UInt16___toUSize) (a : [UInt16]](#manual-UInt16___ofBitVec)) : [USize]](#manual-USize___ofBitVec)
```

Converts 16-bit unsigned integers to word-sized unsigned integers.

This function is overridden at runtime with an efficient implementation.

def

```lean
[UInt32.toUInt8]](#manual-UInt32___toUInt8) (a : [UInt32]](#manual-UInt32___ofBitVec)) : [UInt8]](#manual-UInt8___ofBitVec)



[UInt32.toUInt8]](#manual-UInt32___toUInt8) (a : [UInt32]](#manual-UInt32___ofBitVec)) : [UInt8]](#manual-UInt8___ofBitVec)
```

Converts a 32-bit unsigned integer to an 8-bit unsigned integer, wrapping on overflow.

This function is overridden at runtime with an efficient implementation.

def

```lean
[UInt32.toUInt16]](#manual-UInt32___toUInt16) (a : [UInt32]](#manual-UInt32___ofBitVec)) : [UInt16]](#manual-UInt16___ofBitVec)



[UInt32.toUInt16]](#manual-UInt32___toUInt16) (a : [UInt32]](#manual-UInt32___ofBitVec)) : [UInt16]](#manual-UInt16___ofBitVec)
```

Converts 32-bit unsigned integers to 16-bit unsigned integers. Wraps around on overflow.

This function is overridden at runtime with an efficient implementation.

def

```lean
[UInt32.toInt32]](#manual-UInt32___toInt32) (i : [UInt32]](#manual-UInt32___ofBitVec)) : [Int32]](#manual-Int32___ofUInt32)



[UInt32.toInt32]](#manual-UInt32___toInt32) (i : [UInt32]](#manual-UInt32___ofBitVec)) : [Int32]](#manual-Int32___ofUInt32)
```

Obtains the `[Int32]](#manual-Int32___ofUInt32)` that is 2's complement equivalent to the `[UInt32]](#manual-UInt32___ofBitVec)`.

def

```lean
[UInt32.toUInt64]](#manual-UInt32___toUInt64) (a : [UInt32]](#manual-UInt32___ofBitVec)) : [UInt64]](#manual-UInt64___ofBitVec)



[UInt32.toUInt64]](#manual-UInt32___toUInt64) (a : [UInt32]](#manual-UInt32___ofBitVec)) : [UInt64]](#manual-UInt64___ofBitVec)
```

Converts 32-bit unsigned integers to 64-bit unsigned integers. Wraps around on overflow.

This function is overridden at runtime with an efficient implementation.

def

```lean
[UInt32.toUSize]](#manual-UInt32___toUSize) (a : [UInt32]](#manual-UInt32___ofBitVec)) : [USize]](#manual-USize___ofBitVec)



[UInt32.toUSize]](#manual-UInt32___toUSize) (a : [UInt32]](#manual-UInt32___ofBitVec)) : [USize]](#manual-USize___ofBitVec)
```

Converts 32-bit unsigned integers to word-sized unsigned integers.

This function is overridden at runtime with an efficient implementation.

def

```lean
[UInt64.toUInt8]](#manual-UInt64___toUInt8) (a : [UInt64]](#manual-UInt64___ofBitVec)) : [UInt8]](#manual-UInt8___ofBitVec)



[UInt64.toUInt8]](#manual-UInt64___toUInt8) (a : [UInt64]](#manual-UInt64___ofBitVec)) : [UInt8]](#manual-UInt8___ofBitVec)
```

Converts 64-bit unsigned integers to 8-bit unsigned integers. Wraps around on overflow.

This function is overridden at runtime with an efficient implementation.

def

```lean
[UInt64.toUInt16]](#manual-UInt64___toUInt16) (a : [UInt64]](#manual-UInt64___ofBitVec)) : [UInt16]](#manual-UInt16___ofBitVec)



[UInt64.toUInt16]](#manual-UInt64___toUInt16) (a : [UInt64]](#manual-UInt64___ofBitVec)) : [UInt16]](#manual-UInt16___ofBitVec)
```

Converts 64-bit unsigned integers to 16-bit unsigned integers. Wraps around on overflow.

This function is overridden at runtime with an efficient implementation.

def

```lean
[UInt64.toUInt32]](#manual-UInt64___toUInt32) (a : [UInt64]](#manual-UInt64___ofBitVec)) : [UInt32]](#manual-UInt32___ofBitVec)



[UInt64.toUInt32]](#manual-UInt64___toUInt32) (a : [UInt64]](#manual-UInt64___ofBitVec)) : [UInt32]](#manual-UInt32___ofBitVec)
```

Converts 64-bit unsigned integers to 32-bit unsigned integers. Wraps around on overflow.

This function is overridden at runtime with an efficient implementation.

def

```lean
[UInt64.toInt64]](#manual-UInt64___toInt64) (i : [UInt64]](#manual-UInt64___ofBitVec)) : [Int64]](#manual-Int64___ofUInt64)



[UInt64.toInt64]](#manual-UInt64___toInt64) (i : [UInt64]](#manual-UInt64___ofBitVec)) : [Int64]](#manual-Int64___ofUInt64)
```

Obtains the `[Int64]](#manual-Int64___ofUInt64)` that is 2's complement equivalent to the `[UInt64]](#manual-UInt64___ofBitVec)`.

def

```lean
[UInt64.toUSize]](#manual-UInt64___toUSize) (a : [UInt64]](#manual-UInt64___ofBitVec)) : [USize]](#manual-USize___ofBitVec)



[UInt64.toUSize]](#manual-UInt64___toUSize) (a : [UInt64]](#manual-UInt64___ofBitVec)) : [USize]](#manual-USize___ofBitVec)
```

Converts 64-bit unsigned integers to word-sized unsigned integers. On 32-bit machines, this may
overflow, which results in the value wrapping around (that is, it is reduced modulo `[USize.size]](#manual-USize___size)`).

This function is overridden at runtime with an efficient implementation.

def

```lean
[ISize.toInt8]](#manual-ISize___toInt8) (a : [ISize]](#manual-ISize___ofUSize)) : [Int8]](#manual-Int8___ofUInt8)



[ISize.toInt8]](#manual-ISize___toInt8) (a : [ISize]](#manual-ISize___ofUSize)) : [Int8]](#manual-Int8___ofUInt8)
```

Converts a word-sized signed integer to an 8-bit signed integer by truncating its bitvector representation.

This function is overridden at runtime with an efficient implementation.

def

```lean
[ISize.toInt16]](#manual-ISize___toInt16) (a : [ISize]](#manual-ISize___ofUSize)) : [Int16]](#manual-Int16___ofUInt16)



[ISize.toInt16]](#manual-ISize___toInt16) (a : [ISize]](#manual-ISize___ofUSize)) : [Int16]](#manual-Int16___ofUInt16)
```

Converts a word-sized integer to a 16-bit integer by truncating its bitvector representation.

This function is overridden at runtime with an efficient implementation.

def

```lean
[ISize.toInt32]](#manual-ISize___toInt32) (a : [ISize]](#manual-ISize___ofUSize)) : [Int32]](#manual-Int32___ofUInt32)



[ISize.toInt32]](#manual-ISize___toInt32) (a : [ISize]](#manual-ISize___ofUSize)) : [Int32]](#manual-Int32___ofUInt32)
```

Converts a word-sized signed integer to a 32-bit signed integer.

On 32-bit platforms, this conversion is lossless. On 64-bit platforms, the integer's bitvector
representation is truncated to 32 bits. This function is overridden at runtime with an efficient
implementation.

This function is overridden at runtime with an efficient implementation.

def

```lean
[ISize.toInt64]](#manual-ISize___toInt64) (a : [ISize]](#manual-ISize___ofUSize)) : [Int64]](#manual-Int64___ofUInt64)



[ISize.toInt64]](#manual-ISize___toInt64) (a : [ISize]](#manual-ISize___ofUSize)) : [Int64]](#manual-Int64___ofUInt64)
```

Converts word-sized signed integers to 64-bit signed integers that denote the same number. This
conversion is lossless, because `[ISize]](#manual-ISize___ofUSize)` is either `[Int32]](#manual-Int32___ofUInt32)` or `[Int64]](#manual-Int64___ofUInt64)`.

This function is overridden at runtime with an efficient implementation.

def

```lean
[Int8.toInt16]](#manual-Int8___toInt16) (a : [Int8]](#manual-Int8___ofUInt8)) : [Int16]](#manual-Int16___ofUInt16)



[Int8.toInt16]](#manual-Int8___toInt16) (a : [Int8]](#manual-Int8___ofUInt8)) : [Int16]](#manual-Int16___ofUInt16)
```

Converts 8-bit signed integers to 16-bit signed integers that denote the same number.

This function is overridden at runtime with an efficient implementation.

def

```lean
[Int8.toInt32]](#manual-Int8___toInt32) (a : [Int8]](#manual-Int8___ofUInt8)) : [Int32]](#manual-Int32___ofUInt32)



[Int8.toInt32]](#manual-Int8___toInt32) (a : [Int8]](#manual-Int8___ofUInt8)) : [Int32]](#manual-Int32___ofUInt32)
```

Converts 8-bit signed integers to 32-bit signed integers that denote the same number.

This function is overridden at runtime with an efficient implementation.

def

```lean
[Int8.toInt64]](#manual-Int8___toInt64) (a : [Int8]](#manual-Int8___ofUInt8)) : [Int64]](#manual-Int64___ofUInt64)



[Int8.toInt64]](#manual-Int8___toInt64) (a : [Int8]](#manual-Int8___ofUInt8)) : [Int64]](#manual-Int64___ofUInt64)
```

Converts 8-bit signed integers to 64-bit signed integers that denote the same number.

This function is overridden at runtime with an efficient implementation.

def

```lean
[Int8.toISize]](#manual-Int8___toISize) (a : [Int8]](#manual-Int8___ofUInt8)) : [ISize]](#manual-ISize___ofUSize)



[Int8.toISize]](#manual-Int8___toISize) (a : [Int8]](#manual-Int8___ofUInt8)) : [ISize]](#manual-ISize___ofUSize)
```

Converts 8-bit signed integers to word-sized signed integers that denote the same number. This
conversion is lossless, because `[ISize]](#manual-ISize___ofUSize)` is either `[Int32]](#manual-Int32___ofUInt32)` or `[Int64]](#manual-Int64___ofUInt64)`.

This function is overridden at runtime with an efficient implementation.

def

```lean
[Int16.toInt8]](#manual-Int16___toInt8) (a : [Int16]](#manual-Int16___ofUInt16)) : [Int8]](#manual-Int8___ofUInt8)



[Int16.toInt8]](#manual-Int16___toInt8) (a : [Int16]](#manual-Int16___ofUInt16)) : [Int8]](#manual-Int8___ofUInt8)
```

Converts 16-bit signed integers to 8-bit signed integers by truncating their bitvector
representation.

This function is overridden at runtime with an efficient implementation.

def

```lean
[Int16.toInt32]](#manual-Int16___toInt32) (a : [Int16]](#manual-Int16___ofUInt16)) : [Int32]](#manual-Int32___ofUInt32)



[Int16.toInt32]](#manual-Int16___toInt32) (a : [Int16]](#manual-Int16___ofUInt16)) : [Int32]](#manual-Int32___ofUInt32)
```

Converts 8-bit signed integers to 32-bit signed integers that denote the same number.

This function is overridden at runtime with an efficient implementation.

def

```lean
[Int16.toInt64]](#manual-Int16___toInt64) (a : [Int16]](#manual-Int16___ofUInt16)) : [Int64]](#manual-Int64___ofUInt64)



[Int16.toInt64]](#manual-Int16___toInt64) (a : [Int16]](#manual-Int16___ofUInt16)) : [Int64]](#manual-Int64___ofUInt64)
```

Converts 16-bit signed integers to 64-bit signed integers that denote the same number.

This function is overridden at runtime with an efficient implementation.

def

```lean
[Int16.toISize]](#manual-Int16___toISize) (a : [Int16]](#manual-Int16___ofUInt16)) : [ISize]](#manual-ISize___ofUSize)



[Int16.toISize]](#manual-Int16___toISize) (a : [Int16]](#manual-Int16___ofUInt16)) : [ISize]](#manual-ISize___ofUSize)
```

Converts 16-bit signed integers to word-sized signed integers that denote the same number. This conversion is lossless, because
`[ISize]](#manual-ISize___ofUSize)` is either `[Int32]](#manual-Int32___ofUInt32)` or `[Int64]](#manual-Int64___ofUInt64)`.

This function is overridden at runtime with an efficient implementation.

def

```lean
[Int32.toInt8]](#manual-Int32___toInt8) (a : [Int32]](#manual-Int32___ofUInt32)) : [Int8]](#manual-Int8___ofUInt8)



[Int32.toInt8]](#manual-Int32___toInt8) (a : [Int32]](#manual-Int32___ofUInt32)) : [Int8]](#manual-Int8___ofUInt8)
```

Converts a 32-bit signed integer to an 8-bit signed integer by truncating its bitvector
representation.

This function is overridden at runtime with an efficient implementation.

def

```lean
[Int32.toInt16]](#manual-Int32___toInt16) (a : [Int32]](#manual-Int32___ofUInt32)) : [Int16]](#manual-Int16___ofUInt16)



[Int32.toInt16]](#manual-Int32___toInt16) (a : [Int32]](#manual-Int32___ofUInt32)) : [Int16]](#manual-Int16___ofUInt16)
```

Converts a 32-bit signed integer to an 16-bit signed integer by truncating its bitvector
representation.

This function is overridden at runtime with an efficient implementation.

def

```lean
[Int32.toInt64]](#manual-Int32___toInt64) (a : [Int32]](#manual-Int32___ofUInt32)) : [Int64]](#manual-Int64___ofUInt64)



[Int32.toInt64]](#manual-Int32___toInt64) (a : [Int32]](#manual-Int32___ofUInt32)) : [Int64]](#manual-Int64___ofUInt64)
```

Converts 32-bit signed integers to 64-bit signed integers that denote the same number.

This function is overridden at runtime with an efficient implementation.

def

```lean
[Int32.toISize]](#manual-Int32___toISize) (a : [Int32]](#manual-Int32___ofUInt32)) : [ISize]](#manual-ISize___ofUSize)



[Int32.toISize]](#manual-Int32___toISize) (a : [Int32]](#manual-Int32___ofUInt32)) : [ISize]](#manual-ISize___ofUSize)
```

Converts 32-bit signed integers to word-sized signed integers that denote the same number. This
conversion is lossless, because `[ISize]](#manual-ISize___ofUSize)` is either `[Int32]](#manual-Int32___ofUInt32)` or `[Int64]](#manual-Int64___ofUInt64)`.

This function is overridden at runtime with an efficient implementation.

def

```lean
[Int64.toInt8]](#manual-Int64___toInt8) (a : [Int64]](#manual-Int64___ofUInt64)) : [Int8]](#manual-Int8___ofUInt8)



[Int64.toInt8]](#manual-Int64___toInt8) (a : [Int64]](#manual-Int64___ofUInt64)) : [Int8]](#manual-Int8___ofUInt8)
```

Converts a 64-bit signed integer to an 8-bit signed integer by truncating its bitvector
representation.

This function is overridden at runtime with an efficient implementation.

def

```lean
[Int64.toInt16]](#manual-Int64___toInt16) (a : [Int64]](#manual-Int64___ofUInt64)) : [Int16]](#manual-Int16___ofUInt16)



[Int64.toInt16]](#manual-Int64___toInt16) (a : [Int64]](#manual-Int64___ofUInt64)) : [Int16]](#manual-Int16___ofUInt16)
```

Converts a 64-bit signed integer to a 16-bit signed integer by truncating its bitvector
representation.

This function is overridden at runtime with an efficient implementation.

def

```lean
[Int64.toInt32]](#manual-Int64___toInt32) (a : [Int64]](#manual-Int64___ofUInt64)) : [Int32]](#manual-Int32___ofUInt32)



[Int64.toInt32]](#manual-Int64___toInt32) (a : [Int64]](#manual-Int64___ofUInt64)) : [Int32]](#manual-Int32___ofUInt32)
```

Converts a 64-bit signed integer to a 32-bit signed integer by truncating its bitvector
representation.

This function is overridden at runtime with an efficient implementation.

def

```lean
[Int64.toISize]](#manual-Int64___toISize) (a : [Int64]](#manual-Int64___ofUInt64)) : [ISize]](#manual-ISize___ofUSize)



[Int64.toISize]](#manual-Int64___toISize) (a : [Int64]](#manual-Int64___ofUInt64)) : [ISize]](#manual-ISize___ofUSize)
```

Converts 64-bit signed integers to word-sized signed integers, truncating the bitvector
representation on 32-bit platforms. This conversion is lossless on 64-bit platforms.

This function is overridden at runtime with an efficient implementation.

##### 20.4.4.3.4. To Floating-Point Numbers {#manual-The-Lean-Language-Reference--Basic-Types--Fixed-Precision-Integers--API-Reference--Conversions--To-Floating-Point-Numbers}

def

```lean
[ISize.toFloat]](#manual-ISize___toFloat) (n : [ISize]](#manual-ISize___ofUSize)) : [Float](https://lean-lang.org/doc/reference/latest/Basic-Types/Floating-Point-Numbers/#Float___ofModel)



[ISize.toFloat]](#manual-ISize___toFloat) (n : [ISize]](#manual-ISize___ofUSize)) : [Float](https://lean-lang.org/doc/reference/latest/Basic-Types/Floating-Point-Numbers/#Float___ofModel)
```

Obtains a `[Float](https://lean-lang.org/doc/reference/latest/Basic-Types/Floating-Point-Numbers/#Float___ofModel)` whose value is near the given `[ISize]](#manual-ISize___ofUSize)`.

It will be exactly the value of the given `[ISize]](#manual-ISize___ofUSize)` if such a `[Float](https://lean-lang.org/doc/reference/latest/Basic-Types/Floating-Point-Numbers/#Float___ofModel)` exists. If no such `[Float](https://lean-lang.org/doc/reference/latest/Basic-Types/Floating-Point-Numbers/#Float___ofModel)`
exists, the returned value will either be the smallest `[Float](https://lean-lang.org/doc/reference/latest/Basic-Types/Floating-Point-Numbers/#Float___ofModel)` that is larger than the given value,
or the largest `[Float](https://lean-lang.org/doc/reference/latest/Basic-Types/Floating-Point-Numbers/#Float___ofModel)` that is smaller than the given value.

This function has a logical model in terms of `[Float.Model](https://lean-lang.org/doc/reference/latest/Basic-Types/Floating-Point-Numbers/#Float___Model___mk)`, but is overridden at runtime with an
efficient implementation.

def

```lean
[ISize.toFloat32]](#manual-ISize___toFloat32) (n : [ISize]](#manual-ISize___ofUSize)) : [Float32](https://lean-lang.org/doc/reference/latest/Basic-Types/Floating-Point-Numbers/#Float32___ofModel)



[ISize.toFloat32]](#manual-ISize___toFloat32) (n : [ISize]](#manual-ISize___ofUSize)) : [Float32](https://lean-lang.org/doc/reference/latest/Basic-Types/Floating-Point-Numbers/#Float32___ofModel)
```

Obtains a `[Float32](https://lean-lang.org/doc/reference/latest/Basic-Types/Floating-Point-Numbers/#Float32___ofModel)` whose value is near the given `[ISize]](#manual-ISize___ofUSize)`.

It will be exactly the value of the given `[ISize]](#manual-ISize___ofUSize)` if such a `[Float32](https://lean-lang.org/doc/reference/latest/Basic-Types/Floating-Point-Numbers/#Float32___ofModel)` exists. If no such `[Float32](https://lean-lang.org/doc/reference/latest/Basic-Types/Floating-Point-Numbers/#Float32___ofModel)`
exists, the returned value will either be the smallest `[Float32](https://lean-lang.org/doc/reference/latest/Basic-Types/Floating-Point-Numbers/#Float32___ofModel)` that is larger than the given
value, or the largest `[Float32](https://lean-lang.org/doc/reference/latest/Basic-Types/Floating-Point-Numbers/#Float32___ofModel)` that is smaller than the given value.

This function has a logical model in terms of `[Float32.Model](https://lean-lang.org/doc/reference/latest/Basic-Types/Floating-Point-Numbers/#Float32___Model___mk)`, but is overridden at runtime with an
efficient implementation.

def

```lean
[Int8.toFloat]](#manual-Int8___toFloat) (n : [Int8]](#manual-Int8___ofUInt8)) : [Float](https://lean-lang.org/doc/reference/latest/Basic-Types/Floating-Point-Numbers/#Float___ofModel)



[Int8.toFloat]](#manual-Int8___toFloat) (n : [Int8]](#manual-Int8___ofUInt8)) : [Float](https://lean-lang.org/doc/reference/latest/Basic-Types/Floating-Point-Numbers/#Float___ofModel)
```

Obtains the `[Float](https://lean-lang.org/doc/reference/latest/Basic-Types/Floating-Point-Numbers/#Float___ofModel)` whose value is the same as the given `[Int8]](#manual-Int8___ofUInt8)`.

def

```lean
[Int8.toFloat32]](#manual-Int8___toFloat32) (n : [Int8]](#manual-Int8___ofUInt8)) : [Float32](https://lean-lang.org/doc/reference/latest/Basic-Types/Floating-Point-Numbers/#Float32___ofModel)



[Int8.toFloat32]](#manual-Int8___toFloat32) (n : [Int8]](#manual-Int8___ofUInt8)) : [Float32](https://lean-lang.org/doc/reference/latest/Basic-Types/Floating-Point-Numbers/#Float32___ofModel)
```

Obtains the `[Float32](https://lean-lang.org/doc/reference/latest/Basic-Types/Floating-Point-Numbers/#Float32___ofModel)` whose value is the same as the given `[Int8]](#manual-Int8___ofUInt8)`.

def

```lean
[Int16.toFloat]](#manual-Int16___toFloat) (n : [Int16]](#manual-Int16___ofUInt16)) : [Float](https://lean-lang.org/doc/reference/latest/Basic-Types/Floating-Point-Numbers/#Float___ofModel)



[Int16.toFloat]](#manual-Int16___toFloat) (n : [Int16]](#manual-Int16___ofUInt16)) : [Float](https://lean-lang.org/doc/reference/latest/Basic-Types/Floating-Point-Numbers/#Float___ofModel)
```

Obtains the `[Float](https://lean-lang.org/doc/reference/latest/Basic-Types/Floating-Point-Numbers/#Float___ofModel)` whose value is the same as the given `[Int16]](#manual-Int16___ofUInt16)`.

def

```lean
[Int16.toFloat32]](#manual-Int16___toFloat32) (n : [Int16]](#manual-Int16___ofUInt16)) : [Float32](https://lean-lang.org/doc/reference/latest/Basic-Types/Floating-Point-Numbers/#Float32___ofModel)



[Int16.toFloat32]](#manual-Int16___toFloat32) (n : [Int16]](#manual-Int16___ofUInt16)) : [Float32](https://lean-lang.org/doc/reference/latest/Basic-Types/Floating-Point-Numbers/#Float32___ofModel)
```

Obtains the `[Float32](https://lean-lang.org/doc/reference/latest/Basic-Types/Floating-Point-Numbers/#Float32___ofModel)` whose value is the same as the given `[Int16]](#manual-Int16___ofUInt16)`.

def

```lean
[Int32.toFloat]](#manual-Int32___toFloat) (n : [Int32]](#manual-Int32___ofUInt32)) : [Float](https://lean-lang.org/doc/reference/latest/Basic-Types/Floating-Point-Numbers/#Float___ofModel)



[Int32.toFloat]](#manual-Int32___toFloat) (n : [Int32]](#manual-Int32___ofUInt32)) : [Float](https://lean-lang.org/doc/reference/latest/Basic-Types/Floating-Point-Numbers/#Float___ofModel)
```

Obtains the `[Float](https://lean-lang.org/doc/reference/latest/Basic-Types/Floating-Point-Numbers/#Float___ofModel)` whose value is the same as the given `[Int32]](#manual-Int32___ofUInt32)`.

def

```lean
[Int32.toFloat32]](#manual-Int32___toFloat32) (n : [Int32]](#manual-Int32___ofUInt32)) : [Float32](https://lean-lang.org/doc/reference/latest/Basic-Types/Floating-Point-Numbers/#Float32___ofModel)



[Int32.toFloat32]](#manual-Int32___toFloat32) (n : [Int32]](#manual-Int32___ofUInt32)) : [Float32](https://lean-lang.org/doc/reference/latest/Basic-Types/Floating-Point-Numbers/#Float32___ofModel)
```

Obtains a `[Float32](https://lean-lang.org/doc/reference/latest/Basic-Types/Floating-Point-Numbers/#Float32___ofModel)` whose value is near the given `[Int32]](#manual-Int32___ofUInt32)`.

It will be exactly the value of the given `[Int32]](#manual-Int32___ofUInt32)` if such a `[Float32](https://lean-lang.org/doc/reference/latest/Basic-Types/Floating-Point-Numbers/#Float32___ofModel)` exists. If no such `[Float32](https://lean-lang.org/doc/reference/latest/Basic-Types/Floating-Point-Numbers/#Float32___ofModel)`
exists, the returned value will either be the smallest `[Float32](https://lean-lang.org/doc/reference/latest/Basic-Types/Floating-Point-Numbers/#Float32___ofModel)` that is larger than the given
value, or the largest `[Float32](https://lean-lang.org/doc/reference/latest/Basic-Types/Floating-Point-Numbers/#Float32___ofModel)` that is smaller than the given value.

This function has a logical model in terms of `[Float32.Model](https://lean-lang.org/doc/reference/latest/Basic-Types/Floating-Point-Numbers/#Float32___Model___mk)`, but is overridden at runtime with an
efficient implementation.

def

```lean
[Int64.toFloat]](#manual-Int64___toFloat) (n : [Int64]](#manual-Int64___ofUInt64)) : [Float](https://lean-lang.org/doc/reference/latest/Basic-Types/Floating-Point-Numbers/#Float___ofModel)



[Int64.toFloat]](#manual-Int64___toFloat) (n : [Int64]](#manual-Int64___ofUInt64)) : [Float](https://lean-lang.org/doc/reference/latest/Basic-Types/Floating-Point-Numbers/#Float___ofModel)
```

Obtains a `[Float](https://lean-lang.org/doc/reference/latest/Basic-Types/Floating-Point-Numbers/#Float___ofModel)` whose value is near the given `[Int64]](#manual-Int64___ofUInt64)`.

It will be exactly the value of the given `[Int64]](#manual-Int64___ofUInt64)` if such a `[Float](https://lean-lang.org/doc/reference/latest/Basic-Types/Floating-Point-Numbers/#Float___ofModel)` exists. If no such `[Float](https://lean-lang.org/doc/reference/latest/Basic-Types/Floating-Point-Numbers/#Float___ofModel)`
exists, the returned value will either be the smallest `[Float](https://lean-lang.org/doc/reference/latest/Basic-Types/Floating-Point-Numbers/#Float___ofModel)` that is larger than the given value,
or the largest `[Float](https://lean-lang.org/doc/reference/latest/Basic-Types/Floating-Point-Numbers/#Float___ofModel)` that is smaller than the given value.

This function has a logical model in terms of `[Float.Model](https://lean-lang.org/doc/reference/latest/Basic-Types/Floating-Point-Numbers/#Float___Model___mk)`, but is overridden at runtime with an
efficient implementation.

def

```lean
[Int64.toFloat32]](#manual-Int64___toFloat32) (n : [Int64]](#manual-Int64___ofUInt64)) : [Float32](https://lean-lang.org/doc/reference/latest/Basic-Types/Floating-Point-Numbers/#Float32___ofModel)



[Int64.toFloat32]](#manual-Int64___toFloat32) (n : [Int64]](#manual-Int64___ofUInt64)) : [Float32](https://lean-lang.org/doc/reference/latest/Basic-Types/Floating-Point-Numbers/#Float32___ofModel)
```

Obtains a `[Float32](https://lean-lang.org/doc/reference/latest/Basic-Types/Floating-Point-Numbers/#Float32___ofModel)` whose value is near the given `[Int64]](#manual-Int64___ofUInt64)`.

It will be exactly the value of the given `[Int64]](#manual-Int64___ofUInt64)` if such a `[Float32](https://lean-lang.org/doc/reference/latest/Basic-Types/Floating-Point-Numbers/#Float32___ofModel)` exists. If no such `[Float32](https://lean-lang.org/doc/reference/latest/Basic-Types/Floating-Point-Numbers/#Float32___ofModel)`
exists, the returned value will either be the smallest `[Float32](https://lean-lang.org/doc/reference/latest/Basic-Types/Floating-Point-Numbers/#Float32___ofModel)` that is larger than the given
value, or the largest `[Float32](https://lean-lang.org/doc/reference/latest/Basic-Types/Floating-Point-Numbers/#Float32___ofModel)` that is smaller than the given value.

This function has a logical model in terms of `[Float32.Model](https://lean-lang.org/doc/reference/latest/Basic-Types/Floating-Point-Numbers/#Float32___Model___mk)`, but is overridden at runtime with an
efficient implementation.

def

```lean
[USize.toFloat]](#manual-USize___toFloat) (n : [USize]](#manual-USize___ofBitVec)) : [Float](https://lean-lang.org/doc/reference/latest/Basic-Types/Floating-Point-Numbers/#Float___ofModel)



[USize.toFloat]](#manual-USize___toFloat) (n : [USize]](#manual-USize___ofBitVec)) : [Float](https://lean-lang.org/doc/reference/latest/Basic-Types/Floating-Point-Numbers/#Float___ofModel)
```

Obtains a `[Float](https://lean-lang.org/doc/reference/latest/Basic-Types/Floating-Point-Numbers/#Float___ofModel)` whose value is near the given `[USize]](#manual-USize___ofBitVec)`.

It will be exactly the value of the given `[USize]](#manual-USize___ofBitVec)` if such a `[Float](https://lean-lang.org/doc/reference/latest/Basic-Types/Floating-Point-Numbers/#Float___ofModel)` exists. If no such `[Float](https://lean-lang.org/doc/reference/latest/Basic-Types/Floating-Point-Numbers/#Float___ofModel)`
exists, the returned value will either be the smallest `[Float](https://lean-lang.org/doc/reference/latest/Basic-Types/Floating-Point-Numbers/#Float___ofModel)` that is larger than the given value,
or the largest `[Float](https://lean-lang.org/doc/reference/latest/Basic-Types/Floating-Point-Numbers/#Float___ofModel)` that is smaller than the given value.

This function has a logical model in terms of `[Float.Model](https://lean-lang.org/doc/reference/latest/Basic-Types/Floating-Point-Numbers/#Float___Model___mk)`, but is overridden at runtime with an
efficient implementation.

def

```lean
[USize.toFloat32]](#manual-USize___toFloat32) (n : [USize]](#manual-USize___ofBitVec)) : [Float32](https://lean-lang.org/doc/reference/latest/Basic-Types/Floating-Point-Numbers/#Float32___ofModel)



[USize.toFloat32]](#manual-USize___toFloat32) (n : [USize]](#manual-USize___ofBitVec)) : [Float32](https://lean-lang.org/doc/reference/latest/Basic-Types/Floating-Point-Numbers/#Float32___ofModel)
```

Obtains a `[Float32](https://lean-lang.org/doc/reference/latest/Basic-Types/Floating-Point-Numbers/#Float32___ofModel)` whose value is near the given `[USize]](#manual-USize___ofBitVec)`.

It will be exactly the value of the given `[USize]](#manual-USize___ofBitVec)` if such a `[Float32](https://lean-lang.org/doc/reference/latest/Basic-Types/Floating-Point-Numbers/#Float32___ofModel)` exists. If no such `[Float32](https://lean-lang.org/doc/reference/latest/Basic-Types/Floating-Point-Numbers/#Float32___ofModel)`
exists, the returned value will either be the smallest `[Float32](https://lean-lang.org/doc/reference/latest/Basic-Types/Floating-Point-Numbers/#Float32___ofModel)` that is larger than the given
value, or the largest `[Float32](https://lean-lang.org/doc/reference/latest/Basic-Types/Floating-Point-Numbers/#Float32___ofModel)` that is smaller than the given value.

This function has a logical model in terms of `[Float32.Model](https://lean-lang.org/doc/reference/latest/Basic-Types/Floating-Point-Numbers/#Float32___Model___mk)`, but is overridden at runtime with an
efficient implementation.

def

```lean
[UInt8.toFloat]](#manual-UInt8___toFloat) (n : [UInt8]](#manual-UInt8___ofBitVec)) : [Float](https://lean-lang.org/doc/reference/latest/Basic-Types/Floating-Point-Numbers/#Float___ofModel)



[UInt8.toFloat]](#manual-UInt8___toFloat) (n : [UInt8]](#manual-UInt8___ofBitVec)) : [Float](https://lean-lang.org/doc/reference/latest/Basic-Types/Floating-Point-Numbers/#Float___ofModel)
```

Obtains the `[Float](https://lean-lang.org/doc/reference/latest/Basic-Types/Floating-Point-Numbers/#Float___ofModel)` whose value is the same as the given `[UInt8]](#manual-UInt8___ofBitVec)`.

def

```lean
[UInt8.toFloat32]](#manual-UInt8___toFloat32) (n : [UInt8]](#manual-UInt8___ofBitVec)) : [Float32](https://lean-lang.org/doc/reference/latest/Basic-Types/Floating-Point-Numbers/#Float32___ofModel)



[UInt8.toFloat32]](#manual-UInt8___toFloat32) (n : [UInt8]](#manual-UInt8___ofBitVec)) : [Float32](https://lean-lang.org/doc/reference/latest/Basic-Types/Floating-Point-Numbers/#Float32___ofModel)
```

Obtains the `[Float32](https://lean-lang.org/doc/reference/latest/Basic-Types/Floating-Point-Numbers/#Float32___ofModel)` whose value is the same as the given `[UInt8]](#manual-UInt8___ofBitVec)`.

def

```lean
[UInt16.toFloat]](#manual-UInt16___toFloat) (n : [UInt16]](#manual-UInt16___ofBitVec)) : [Float](https://lean-lang.org/doc/reference/latest/Basic-Types/Floating-Point-Numbers/#Float___ofModel)



[UInt16.toFloat]](#manual-UInt16___toFloat) (n : [UInt16]](#manual-UInt16___ofBitVec)) : [Float](https://lean-lang.org/doc/reference/latest/Basic-Types/Floating-Point-Numbers/#Float___ofModel)
```

Obtains the `[Float](https://lean-lang.org/doc/reference/latest/Basic-Types/Floating-Point-Numbers/#Float___ofModel)` whose value is the same as the given `[UInt16]](#manual-UInt16___ofBitVec)`.

def

```lean
[UInt16.toFloat32]](#manual-UInt16___toFloat32) (n : [UInt16]](#manual-UInt16___ofBitVec)) : [Float32](https://lean-lang.org/doc/reference/latest/Basic-Types/Floating-Point-Numbers/#Float32___ofModel)



[UInt16.toFloat32]](#manual-UInt16___toFloat32) (n : [UInt16]](#manual-UInt16___ofBitVec)) : [Float32](https://lean-lang.org/doc/reference/latest/Basic-Types/Floating-Point-Numbers/#Float32___ofModel)
```

Obtains the `[Float32](https://lean-lang.org/doc/reference/latest/Basic-Types/Floating-Point-Numbers/#Float32___ofModel)` whose value is the same as the given `[UInt16]](#manual-UInt16___ofBitVec)`.

def

```lean
[UInt32.toFloat]](#manual-UInt32___toFloat) (n : [UInt32]](#manual-UInt32___ofBitVec)) : [Float](https://lean-lang.org/doc/reference/latest/Basic-Types/Floating-Point-Numbers/#Float___ofModel)



[UInt32.toFloat]](#manual-UInt32___toFloat) (n : [UInt32]](#manual-UInt32___ofBitVec)) : [Float](https://lean-lang.org/doc/reference/latest/Basic-Types/Floating-Point-Numbers/#Float___ofModel)
```

Obtains the `[Float](https://lean-lang.org/doc/reference/latest/Basic-Types/Floating-Point-Numbers/#Float___ofModel)` whose value is the same as the given `[UInt32]](#manual-UInt32___ofBitVec)`.

def

```lean
[UInt32.toFloat32]](#manual-UInt32___toFloat32) (n : [UInt32]](#manual-UInt32___ofBitVec)) : [Float32](https://lean-lang.org/doc/reference/latest/Basic-Types/Floating-Point-Numbers/#Float32___ofModel)



[UInt32.toFloat32]](#manual-UInt32___toFloat32) (n : [UInt32]](#manual-UInt32___ofBitVec)) : [Float32](https://lean-lang.org/doc/reference/latest/Basic-Types/Floating-Point-Numbers/#Float32___ofModel)
```

Obtains a `[Float32](https://lean-lang.org/doc/reference/latest/Basic-Types/Floating-Point-Numbers/#Float32___ofModel)` whose value is near the given `[UInt32]](#manual-UInt32___ofBitVec)`.

It will be exactly the value of the given `[UInt32]](#manual-UInt32___ofBitVec)` if such a `[Float32](https://lean-lang.org/doc/reference/latest/Basic-Types/Floating-Point-Numbers/#Float32___ofModel)` exists. If no such `[Float32](https://lean-lang.org/doc/reference/latest/Basic-Types/Floating-Point-Numbers/#Float32___ofModel)`
exists, the returned value will either be the smallest `[Float32](https://lean-lang.org/doc/reference/latest/Basic-Types/Floating-Point-Numbers/#Float32___ofModel)` that is larger than the given
value, or the largest `[Float32](https://lean-lang.org/doc/reference/latest/Basic-Types/Floating-Point-Numbers/#Float32___ofModel)` that is smaller than the given value.

This function has a logical model in terms of `[Float32.Model](https://lean-lang.org/doc/reference/latest/Basic-Types/Floating-Point-Numbers/#Float32___Model___mk)`, but is overridden at runtime with an
efficient implementation.

def

```lean
[UInt64.toFloat]](#manual-UInt64___toFloat) (n : [UInt64]](#manual-UInt64___ofBitVec)) : [Float](https://lean-lang.org/doc/reference/latest/Basic-Types/Floating-Point-Numbers/#Float___ofModel)



[UInt64.toFloat]](#manual-UInt64___toFloat) (n : [UInt64]](#manual-UInt64___ofBitVec)) : [Float](https://lean-lang.org/doc/reference/latest/Basic-Types/Floating-Point-Numbers/#Float___ofModel)
```

Obtains a `[Float](https://lean-lang.org/doc/reference/latest/Basic-Types/Floating-Point-Numbers/#Float___ofModel)` whose value is near the given `[UInt64]](#manual-UInt64___ofBitVec)`.

It will be exactly the value of the given `[UInt64]](#manual-UInt64___ofBitVec)` if such a `[Float](https://lean-lang.org/doc/reference/latest/Basic-Types/Floating-Point-Numbers/#Float___ofModel)` exists. If no such `[Float](https://lean-lang.org/doc/reference/latest/Basic-Types/Floating-Point-Numbers/#Float___ofModel)`
exists, the returned value will either be the smallest `[Float](https://lean-lang.org/doc/reference/latest/Basic-Types/Floating-Point-Numbers/#Float___ofModel)` that is larger than the given value,
or the largest `[Float](https://lean-lang.org/doc/reference/latest/Basic-Types/Floating-Point-Numbers/#Float___ofModel)` that is smaller than the given value.

This function has a logical model in terms of `[Float.Model](https://lean-lang.org/doc/reference/latest/Basic-Types/Floating-Point-Numbers/#Float___Model___mk)`, but is overridden at runtime with an
efficient implementation.

def

```lean
[UInt64.toFloat32]](#manual-UInt64___toFloat32) (n : [UInt64]](#manual-UInt64___ofBitVec)) : [Float32](https://lean-lang.org/doc/reference/latest/Basic-Types/Floating-Point-Numbers/#Float32___ofModel)



[UInt64.toFloat32]](#manual-UInt64___toFloat32) (n : [UInt64]](#manual-UInt64___ofBitVec)) : [Float32](https://lean-lang.org/doc/reference/latest/Basic-Types/Floating-Point-Numbers/#Float32___ofModel)
```

Obtains a `[Float32](https://lean-lang.org/doc/reference/latest/Basic-Types/Floating-Point-Numbers/#Float32___ofModel)` whose value is near the given `[UInt64]](#manual-UInt64___ofBitVec)`.

It will be exactly the value of the given `[UInt64]](#manual-UInt64___ofBitVec)` if such a `[Float32](https://lean-lang.org/doc/reference/latest/Basic-Types/Floating-Point-Numbers/#Float32___ofModel)` exists. If no such `[Float32](https://lean-lang.org/doc/reference/latest/Basic-Types/Floating-Point-Numbers/#Float32___ofModel)`
exists, the returned value will either be the smallest `[Float32](https://lean-lang.org/doc/reference/latest/Basic-Types/Floating-Point-Numbers/#Float32___ofModel)` that is larger than the given
value, or the largest `[Float32](https://lean-lang.org/doc/reference/latest/Basic-Types/Floating-Point-Numbers/#Float32___ofModel)` that is smaller than the given value.

This function has a logical model in terms of `[Float32.Model](https://lean-lang.org/doc/reference/latest/Basic-Types/Floating-Point-Numbers/#Float32___Model___mk)`, but is overridden at runtime with an
efficient implementation.

##### 20.4.4.3.5. To and From Bitvectors {#manual-The-Lean-Language-Reference--Basic-Types--Fixed-Precision-Integers--API-Reference--Conversions--To-and-From-Bitvectors}

def

```lean
[ISize.toBitVec]](#manual-ISize___toBitVec) (x : [ISize]](#manual-ISize___ofUSize)) : [BitVec](https://lean-lang.org/doc/reference/latest/Basic-Types/Bitvectors/#BitVec___ofFin) [System.Platform.numBits](https://lean-lang.org/doc/reference/latest/IO/System-and-Platform-Information/#System___Platform___numBits)



[ISize.toBitVec]](#manual-ISize___toBitVec) (x : [ISize]](#manual-ISize___ofUSize)) :
  [BitVec](https://lean-lang.org/doc/reference/latest/Basic-Types/Bitvectors/#BitVec___ofFin) [System.Platform.numBits](https://lean-lang.org/doc/reference/latest/IO/System-and-Platform-Information/#System___Platform___numBits)
```

Obtain the `[BitVec](https://lean-lang.org/doc/reference/latest/Basic-Types/Bitvectors/#BitVec___ofFin)` that contains the 2's complement representation of the `[ISize]](#manual-ISize___ofUSize)`.

def

```lean
[ISize.ofBitVec]](#manual-ISize___ofBitVec) (b : [BitVec](https://lean-lang.org/doc/reference/latest/Basic-Types/Bitvectors/#BitVec___ofFin) [System.Platform.numBits](https://lean-lang.org/doc/reference/latest/IO/System-and-Platform-Information/#System___Platform___numBits)) : [ISize]](#manual-ISize___ofUSize)



[ISize.ofBitVec]](#manual-ISize___ofBitVec)
  (b : [BitVec](https://lean-lang.org/doc/reference/latest/Basic-Types/Bitvectors/#BitVec___ofFin) [System.Platform.numBits](https://lean-lang.org/doc/reference/latest/IO/System-and-Platform-Information/#System___Platform___numBits)) :
  [ISize]](#manual-ISize___ofUSize)
```

Obtains the `[ISize]](#manual-ISize___ofUSize)` whose 2's complement representation is the given `[BitVec](https://lean-lang.org/doc/reference/latest/Basic-Types/Bitvectors/#BitVec___ofFin)`.

def

```lean
[Int8.toBitVec]](#manual-Int8___toBitVec) (x : [Int8]](#manual-Int8___ofUInt8)) : [BitVec](https://lean-lang.org/doc/reference/latest/Basic-Types/Bitvectors/#BitVec___ofFin) 8



[Int8.toBitVec]](#manual-Int8___toBitVec) (x : [Int8]](#manual-Int8___ofUInt8)) : [BitVec](https://lean-lang.org/doc/reference/latest/Basic-Types/Bitvectors/#BitVec___ofFin) 8
```

Obtain the `[BitVec](https://lean-lang.org/doc/reference/latest/Basic-Types/Bitvectors/#BitVec___ofFin)` that contains the 2's complement representation of the `[Int8]](#manual-Int8___ofUInt8)`.

def

```lean
[Int8.ofBitVec]](#manual-Int8___ofBitVec) (b : [BitVec](https://lean-lang.org/doc/reference/latest/Basic-Types/Bitvectors/#BitVec___ofFin) 8) : [Int8]](#manual-Int8___ofUInt8)



[Int8.ofBitVec]](#manual-Int8___ofBitVec) (b : [BitVec](https://lean-lang.org/doc/reference/latest/Basic-Types/Bitvectors/#BitVec___ofFin) 8) : [Int8]](#manual-Int8___ofUInt8)
```

Obtains the `[Int8]](#manual-Int8___ofUInt8)` whose 2's complement representation is the given `[BitVec](https://lean-lang.org/doc/reference/latest/Basic-Types/Bitvectors/#BitVec___ofFin) 8`.

def

```lean
[Int16.toBitVec]](#manual-Int16___toBitVec) (x : [Int16]](#manual-Int16___ofUInt16)) : [BitVec](https://lean-lang.org/doc/reference/latest/Basic-Types/Bitvectors/#BitVec___ofFin) 16



[Int16.toBitVec]](#manual-Int16___toBitVec) (x : [Int16]](#manual-Int16___ofUInt16)) : [BitVec](https://lean-lang.org/doc/reference/latest/Basic-Types/Bitvectors/#BitVec___ofFin) 16
```

Obtain the `[BitVec](https://lean-lang.org/doc/reference/latest/Basic-Types/Bitvectors/#BitVec___ofFin)` that contains the 2's complement representation of the `[Int16]](#manual-Int16___ofUInt16)`.

def

```lean
[Int16.ofBitVec]](#manual-Int16___ofBitVec) (b : [BitVec](https://lean-lang.org/doc/reference/latest/Basic-Types/Bitvectors/#BitVec___ofFin) 16) : [Int16]](#manual-Int16___ofUInt16)



[Int16.ofBitVec]](#manual-Int16___ofBitVec) (b : [BitVec](https://lean-lang.org/doc/reference/latest/Basic-Types/Bitvectors/#BitVec___ofFin) 16) : [Int16]](#manual-Int16___ofUInt16)
```

Obtains the `[Int16]](#manual-Int16___ofUInt16)` whose 2's complement representation is the given `[BitVec](https://lean-lang.org/doc/reference/latest/Basic-Types/Bitvectors/#BitVec___ofFin) 16`.

def

```lean
[Int32.toBitVec]](#manual-Int32___toBitVec) (x : [Int32]](#manual-Int32___ofUInt32)) : [BitVec](https://lean-lang.org/doc/reference/latest/Basic-Types/Bitvectors/#BitVec___ofFin) 32



[Int32.toBitVec]](#manual-Int32___toBitVec) (x : [Int32]](#manual-Int32___ofUInt32)) : [BitVec](https://lean-lang.org/doc/reference/latest/Basic-Types/Bitvectors/#BitVec___ofFin) 32
```

Obtain the `[BitVec](https://lean-lang.org/doc/reference/latest/Basic-Types/Bitvectors/#BitVec___ofFin)` that contains the 2's complement representation of the `[Int32]](#manual-Int32___ofUInt32)`.

def

```lean
[Int32.ofBitVec]](#manual-Int32___ofBitVec) (b : [BitVec](https://lean-lang.org/doc/reference/latest/Basic-Types/Bitvectors/#BitVec___ofFin) 32) : [Int32]](#manual-Int32___ofUInt32)



[Int32.ofBitVec]](#manual-Int32___ofBitVec) (b : [BitVec](https://lean-lang.org/doc/reference/latest/Basic-Types/Bitvectors/#BitVec___ofFin) 32) : [Int32]](#manual-Int32___ofUInt32)
```

Obtains the `[Int32]](#manual-Int32___ofUInt32)` whose 2's complement representation is the given `[BitVec](https://lean-lang.org/doc/reference/latest/Basic-Types/Bitvectors/#BitVec___ofFin) 32`.

def

```lean
[Int64.toBitVec]](#manual-Int64___toBitVec) (x : [Int64]](#manual-Int64___ofUInt64)) : [BitVec](https://lean-lang.org/doc/reference/latest/Basic-Types/Bitvectors/#BitVec___ofFin) 64



[Int64.toBitVec]](#manual-Int64___toBitVec) (x : [Int64]](#manual-Int64___ofUInt64)) : [BitVec](https://lean-lang.org/doc/reference/latest/Basic-Types/Bitvectors/#BitVec___ofFin) 64
```

Obtain the `[BitVec](https://lean-lang.org/doc/reference/latest/Basic-Types/Bitvectors/#BitVec___ofFin)` that contains the 2's complement representation of the `[Int64]](#manual-Int64___ofUInt64)`.

def

```lean
[Int64.ofBitVec]](#manual-Int64___ofBitVec) (b : [BitVec](https://lean-lang.org/doc/reference/latest/Basic-Types/Bitvectors/#BitVec___ofFin) 64) : [Int64]](#manual-Int64___ofUInt64)



[Int64.ofBitVec]](#manual-Int64___ofBitVec) (b : [BitVec](https://lean-lang.org/doc/reference/latest/Basic-Types/Bitvectors/#BitVec___ofFin) 64) : [Int64]](#manual-Int64___ofUInt64)
```

Obtains the `[Int64]](#manual-Int64___ofUInt64)` whose 2's complement representation is the given `[BitVec](https://lean-lang.org/doc/reference/latest/Basic-Types/Bitvectors/#BitVec___ofFin) 64`.

##### 20.4.4.3.6. To and From Finite Numbers {#manual-The-Lean-Language-Reference--Basic-Types--Fixed-Precision-Integers--API-Reference--Conversions--To-and-From-Finite-Numbers}

def

```lean
[USize.toFin]](#manual-USize___toFin) (x : [USize]](#manual-USize___ofBitVec)) : [Fin]](#manual-Fin___mk) [USize.size]](#manual-USize___size)



[USize.toFin]](#manual-USize___toFin) (x : [USize]](#manual-USize___ofBitVec)) : [Fin]](#manual-Fin___mk) [USize.size]](#manual-USize___size)
```

Converts a `[USize]](#manual-USize___ofBitVec)` into the corresponding `[Fin]](#manual-Fin___mk) [USize.size]](#manual-USize___size)`.

def

```lean
[UInt8.toFin]](#manual-UInt8___toFin) (x : [UInt8]](#manual-UInt8___ofBitVec)) : [Fin]](#manual-Fin___mk) [UInt8.size]](#manual-UInt8___size)



[UInt8.toFin]](#manual-UInt8___toFin) (x : [UInt8]](#manual-UInt8___ofBitVec)) : [Fin]](#manual-Fin___mk) [UInt8.size]](#manual-UInt8___size)
```

Converts a `[UInt8]](#manual-UInt8___ofBitVec)` into the corresponding `[Fin]](#manual-Fin___mk) [UInt8.size]](#manual-UInt8___size)`.

def

```lean
[UInt16.toFin]](#manual-UInt16___toFin) (x : [UInt16]](#manual-UInt16___ofBitVec)) : [Fin]](#manual-Fin___mk) [UInt16.size]](#manual-UInt16___size)



[UInt16.toFin]](#manual-UInt16___toFin) (x : [UInt16]](#manual-UInt16___ofBitVec)) :
  [Fin]](#manual-Fin___mk) [UInt16.size]](#manual-UInt16___size)
```

Converts a `[UInt16]](#manual-UInt16___ofBitVec)` into the corresponding `[Fin]](#manual-Fin___mk) [UInt16.size]](#manual-UInt16___size)`.

def

```lean
[UInt32.toFin]](#manual-UInt32___toFin) (x : [UInt32]](#manual-UInt32___ofBitVec)) : [Fin]](#manual-Fin___mk) [UInt32.size]](#manual-UInt32___size)



[UInt32.toFin]](#manual-UInt32___toFin) (x : [UInt32]](#manual-UInt32___ofBitVec)) :
  [Fin]](#manual-Fin___mk) [UInt32.size]](#manual-UInt32___size)
```

Converts a `[UInt32]](#manual-UInt32___ofBitVec)` into the corresponding `[Fin]](#manual-Fin___mk) [UInt32.size]](#manual-UInt32___size)`.

def

```lean
[UInt64.toFin]](#manual-UInt64___toFin) (x : [UInt64]](#manual-UInt64___ofBitVec)) : [Fin]](#manual-Fin___mk) [UInt64.size]](#manual-UInt64___size)



[UInt64.toFin]](#manual-UInt64___toFin) (x : [UInt64]](#manual-UInt64___ofBitVec)) :
  [Fin]](#manual-Fin___mk) [UInt64.size]](#manual-UInt64___size)
```

Converts a `[UInt64]](#manual-UInt64___ofBitVec)` into the corresponding `[Fin]](#manual-Fin___mk) [UInt64.size]](#manual-UInt64___size)`.

def

```lean
[USize.ofFin]](#manual-USize___ofFin) (a : [Fin]](#manual-Fin___mk) [USize.size]](#manual-USize___size)) : [USize]](#manual-USize___ofBitVec)



[USize.ofFin]](#manual-USize___ofFin) (a : [Fin]](#manual-Fin___mk) [USize.size]](#manual-USize___size)) : [USize]](#manual-USize___ofBitVec)
```

Converts a `[Fin]](#manual-Fin___mk) [USize.size]](#manual-USize___size)` into the corresponding `[USize]](#manual-USize___ofBitVec)`.

def

```lean
[UInt8.ofFin]](#manual-UInt8___ofFin) (a : [Fin]](#manual-Fin___mk) [UInt8.size]](#manual-UInt8___size)) : [UInt8]](#manual-UInt8___ofBitVec)



[UInt8.ofFin]](#manual-UInt8___ofFin) (a : [Fin]](#manual-Fin___mk) [UInt8.size]](#manual-UInt8___size)) : [UInt8]](#manual-UInt8___ofBitVec)
```

Converts a `[Fin]](#manual-Fin___mk) [UInt8.size]](#manual-UInt8___size)` into the corresponding `[UInt8]](#manual-UInt8___ofBitVec)`.

def

```lean
[UInt16.ofFin]](#manual-UInt16___ofFin) (a : [Fin]](#manual-Fin___mk) [UInt16.size]](#manual-UInt16___size)) : [UInt16]](#manual-UInt16___ofBitVec)



[UInt16.ofFin]](#manual-UInt16___ofFin) (a : [Fin]](#manual-Fin___mk) [UInt16.size]](#manual-UInt16___size)) :
  [UInt16]](#manual-UInt16___ofBitVec)
```

Converts a `[Fin]](#manual-Fin___mk) [UInt16.size]](#manual-UInt16___size)` into the corresponding `[UInt16]](#manual-UInt16___ofBitVec)`.

def

```lean
[UInt32.ofFin]](#manual-UInt32___ofFin) (a : [Fin]](#manual-Fin___mk) [UInt32.size]](#manual-UInt32___size)) : [UInt32]](#manual-UInt32___ofBitVec)



[UInt32.ofFin]](#manual-UInt32___ofFin) (a : [Fin]](#manual-Fin___mk) [UInt32.size]](#manual-UInt32___size)) :
  [UInt32]](#manual-UInt32___ofBitVec)
```

Converts a `[Fin]](#manual-Fin___mk) [UInt32.size]](#manual-UInt32___size)` into the corresponding `[UInt32]](#manual-UInt32___ofBitVec)`.

def

```lean
[UInt64.ofFin]](#manual-UInt64___ofFin) (a : [Fin]](#manual-Fin___mk) [UInt64.size]](#manual-UInt64___size)) : [UInt64]](#manual-UInt64___ofBitVec)



[UInt64.ofFin]](#manual-UInt64___ofFin) (a : [Fin]](#manual-Fin___mk) [UInt64.size]](#manual-UInt64___size)) :
  [UInt64]](#manual-UInt64___ofBitVec)
```

Converts a `[Fin]](#manual-Fin___mk) [UInt64.size]](#manual-UInt64___size)` into the corresponding `[UInt64]](#manual-UInt64___ofBitVec)`.

def

```lean
[USize.repr]](#manual-USize___repr) (n : [USize]](#manual-USize___ofBitVec)) : [String](https://lean-lang.org/doc/reference/latest/Basic-Types/Strings/#String___ofByteArray)



[USize.repr]](#manual-USize___repr) (n : [USize]](#manual-USize___ofBitVec)) : [String](https://lean-lang.org/doc/reference/latest/Basic-Types/Strings/#String___ofByteArray)
```

Converts a word-sized unsigned integer into a decimal string.

This function is overridden at runtime with an efficient implementation.

Examples:

- `[USize.repr]](#manual-USize___repr) 0 = "0"`
- `[USize.repr]](#manual-USize___repr) 28 = "28"`
- `[USize.repr]](#manual-USize___repr) 307 = "307"`

##### 20.4.4.3.7. To Characters {#manual-The-Lean-Language-Reference--Basic-Types--Fixed-Precision-Integers--API-Reference--Conversions--To-Characters}

The `[Char](https://lean-lang.org/doc/reference/latest/Basic-Types/Characters/#Char___mk)` type is a wrapper around `[UInt32]](#manual-UInt32___ofBitVec)` that requires a proof that the wrapped integer represents a Unicode code point.
This predicate is part of the `[UInt32]](#manual-UInt32___ofBitVec)` API.

def

```lean
[UInt32.isValidChar]](#manual-UInt32___isValidChar) (n : [UInt32]](#manual-UInt32___ofBitVec)) : Prop



[UInt32.isValidChar]](#manual-UInt32___isValidChar) (n : [UInt32]](#manual-UInt32___ofBitVec)) : Prop
```

A `[UInt32]](#manual-UInt32___ofBitVec)` denotes a valid Unicode code point if it is less than `0x110000` and it is also not a
surrogate code point (the range `0xd800` to `0xdfff` inclusive).

#### 20.4.4.4. Comparisons {#manual-fixed-int-comparisons}

The operators in this section are rarely invoked by name.
Typically, comparisons operations on fixed-width integers should use the decidability of the corresponding relations, which consist of the equality type `[Eq]](#manual-Eq___refl)` and those implemented in instances of `[LE]](#manual-LE___mk)` and `[LT]](#manual-LT___mk)`.

def

```lean
[USize.le]](#manual-USize___le) (a b : [USize]](#manual-USize___ofBitVec)) : Prop



[USize.le]](#manual-USize___le) (a b : [USize]](#manual-USize___ofBitVec)) : Prop
```

Non-strict inequality of word-sized unsigned integers, defined as inequality of the corresponding
natural numbers. Usually accessed via the `≤` operator.

def

```lean
[ISize.le]](#manual-ISize___le) (a b : [ISize]](#manual-ISize___ofUSize)) : Prop



[ISize.le]](#manual-ISize___le) (a b : [ISize]](#manual-ISize___ofUSize)) : Prop
```

Non-strict inequality of word-sized signed integers, defined as inequality of the corresponding
integers. Usually accessed via the `≤` operator.

def

```lean
[UInt8.le]](#manual-UInt8___le) (a b : [UInt8]](#manual-UInt8___ofBitVec)) : Prop



[UInt8.le]](#manual-UInt8___le) (a b : [UInt8]](#manual-UInt8___ofBitVec)) : Prop
```

Non-strict inequality of 8-bit unsigned integers, defined as inequality of the corresponding
natural numbers. Usually accessed via the `≤` operator.

def

```lean
[Int8.le]](#manual-Int8___le) (a b : [Int8]](#manual-Int8___ofUInt8)) : Prop



[Int8.le]](#manual-Int8___le) (a b : [Int8]](#manual-Int8___ofUInt8)) : Prop
```

Non-strict inequality of 8-bit signed integers, defined as inequality of the corresponding integers.
Usually accessed via the `≤` operator.

def

```lean
[UInt16.le]](#manual-UInt16___le) (a b : [UInt16]](#manual-UInt16___ofBitVec)) : Prop



[UInt16.le]](#manual-UInt16___le) (a b : [UInt16]](#manual-UInt16___ofBitVec)) : Prop
```

Non-strict inequality of 16-bit unsigned integers, defined as inequality of the corresponding
natural numbers. Usually accessed via the `≤` operator.

def

```lean
[Int16.le]](#manual-Int16___le) (a b : [Int16]](#manual-Int16___ofUInt16)) : Prop



[Int16.le]](#manual-Int16___le) (a b : [Int16]](#manual-Int16___ofUInt16)) : Prop
```

Non-strict inequality of 16-bit signed integers, defined as inequality of the corresponding
integers. Usually accessed via the `≤` operator.

def

```lean
[UInt32.le]](#manual-UInt32___le) (a b : [UInt32]](#manual-UInt32___ofBitVec)) : Prop



[UInt32.le]](#manual-UInt32___le) (a b : [UInt32]](#manual-UInt32___ofBitVec)) : Prop
```

Non-strict inequality of 32-bit unsigned integers, defined as inequality of the corresponding
natural numbers. Usually accessed via the `≤` operator.

def

```lean
[Int32.le]](#manual-Int32___le) (a b : [Int32]](#manual-Int32___ofUInt32)) : Prop



[Int32.le]](#manual-Int32___le) (a b : [Int32]](#manual-Int32___ofUInt32)) : Prop
```

Non-strict inequality of 32-bit signed integers, defined as inequality of the corresponding integers.
Usually accessed via the `≤` operator.

def

```lean
[UInt64.le]](#manual-UInt64___le) (a b : [UInt64]](#manual-UInt64___ofBitVec)) : Prop



[UInt64.le]](#manual-UInt64___le) (a b : [UInt64]](#manual-UInt64___ofBitVec)) : Prop
```

Non-strict inequality of 64-bit unsigned integers, defined as inequality of the corresponding
natural numbers. Usually accessed via the `≤` operator.

def

```lean
[Int64.le]](#manual-Int64___le) (a b : [Int64]](#manual-Int64___ofUInt64)) : Prop



[Int64.le]](#manual-Int64___le) (a b : [Int64]](#manual-Int64___ofUInt64)) : Prop
```

Non-strict inequality of 64-bit signed integers, defined as inequality of the corresponding integers.
Usually accessed via the `≤` operator.

def

```lean
[USize.lt]](#manual-USize___lt) (a b : [USize]](#manual-USize___ofBitVec)) : Prop



[USize.lt]](#manual-USize___lt) (a b : [USize]](#manual-USize___ofBitVec)) : Prop
```

Strict inequality of word-sized unsigned integers, defined as inequality of the corresponding
natural numbers. Usually accessed via the `<` operator.

def

```lean
[ISize.lt]](#manual-ISize___lt) (a b : [ISize]](#manual-ISize___ofUSize)) : Prop



[ISize.lt]](#manual-ISize___lt) (a b : [ISize]](#manual-ISize___ofUSize)) : Prop
```

Strict inequality of word-sized signed integers, defined as inequality of the corresponding
integers. Usually accessed via the `<` operator.

def

```lean
[UInt8.lt]](#manual-UInt8___lt) (a b : [UInt8]](#manual-UInt8___ofBitVec)) : Prop



[UInt8.lt]](#manual-UInt8___lt) (a b : [UInt8]](#manual-UInt8___ofBitVec)) : Prop
```

Strict inequality of 8-bit unsigned integers, defined as inequality of the corresponding
natural numbers. Usually accessed via the `<` operator.

def

```lean
[Int8.lt]](#manual-Int8___lt) (a b : [Int8]](#manual-Int8___ofUInt8)) : Prop



[Int8.lt]](#manual-Int8___lt) (a b : [Int8]](#manual-Int8___ofUInt8)) : Prop
```

Strict inequality of 8-bit signed integers, defined as inequality of the corresponding integers.
Usually accessed via the `<` operator.

def

```lean
[UInt16.lt]](#manual-UInt16___lt) (a b : [UInt16]](#manual-UInt16___ofBitVec)) : Prop



[UInt16.lt]](#manual-UInt16___lt) (a b : [UInt16]](#manual-UInt16___ofBitVec)) : Prop
```

Strict inequality of 16-bit unsigned integers, defined as inequality of the corresponding
natural numbers. Usually accessed via the `<` operator.

def

```lean
[Int16.lt]](#manual-Int16___lt) (a b : [Int16]](#manual-Int16___ofUInt16)) : Prop



[Int16.lt]](#manual-Int16___lt) (a b : [Int16]](#manual-Int16___ofUInt16)) : Prop
```

Strict inequality of 16-bit signed integers, defined as inequality of the corresponding integers.
Usually accessed via the `<` operator.

def

```lean
[UInt32.lt]](#manual-UInt32___lt) (a b : [UInt32]](#manual-UInt32___ofBitVec)) : Prop



[UInt32.lt]](#manual-UInt32___lt) (a b : [UInt32]](#manual-UInt32___ofBitVec)) : Prop
```

Strict inequality of 32-bit unsigned integers, defined as inequality of the corresponding
natural numbers. Usually accessed via the `<` operator.

def

```lean
[Int32.lt]](#manual-Int32___lt) (a b : [Int32]](#manual-Int32___ofUInt32)) : Prop



[Int32.lt]](#manual-Int32___lt) (a b : [Int32]](#manual-Int32___ofUInt32)) : Prop
```

Strict inequality of 32-bit signed integers, defined as inequality of the corresponding integers.
Usually accessed via the `<` operator.

def

```lean
[UInt64.lt]](#manual-UInt64___lt) (a b : [UInt64]](#manual-UInt64___ofBitVec)) : Prop



[UInt64.lt]](#manual-UInt64___lt) (a b : [UInt64]](#manual-UInt64___ofBitVec)) : Prop
```

Strict inequality of 64-bit unsigned integers, defined as inequality of the corresponding
natural numbers. Usually accessed via the `<` operator.

def

```lean
[Int64.lt]](#manual-Int64___lt) (a b : [Int64]](#manual-Int64___ofUInt64)) : Prop



[Int64.lt]](#manual-Int64___lt) (a b : [Int64]](#manual-Int64___ofUInt64)) : Prop
```

Strict inequality of 64-bit signed integers, defined as inequality of the corresponding integers.
Usually accessed via the `<` operator.

def

```lean
[USize.decEq]](#manual-USize___decEq) (a b : [USize]](#manual-USize___ofBitVec)) : [Decidable]](#manual-Decidable___isFalse) [(]](#manual-Eq___refl)a [=]](#manual-Eq___refl) b[)]](#manual-Eq___refl)



[USize.decEq]](#manual-USize___decEq) (a b : [USize]](#manual-USize___ofBitVec)) :
  [Decidable]](#manual-Decidable___isFalse) [(]](#manual-Eq___refl)a [=]](#manual-Eq___refl) b[)]](#manual-Eq___refl)
```

Decides whether two word-sized unsigned integers are equal. Usually accessed via the
`[DecidableEq]](#manual-DecidableEq) [USize]](#manual-USize___ofBitVec)` instance.

This function is overridden at runtime with an efficient implementation.

Examples:

- `[USize.decEq]](#manual-USize___decEq) 123 123 = [.isTrue]](#manual-Decidable___isFalse) [rfl]](#manual-rfl-next)`
- `([if]](#manual-termIfThenElse) (6 : [USize]](#manual-USize___ofBitVec)) = 7 [then]](#manual-termIfThenElse) "yes" [else]](#manual-termIfThenElse) "no") = "no"`
- `show (7 : [USize]](#manual-USize___ofBitVec)) = 7 by [decide]](#manual-decide)`

def

```lean
[ISize.decEq]](#manual-ISize___decEq) (a b : [ISize]](#manual-ISize___ofUSize)) : [Decidable]](#manual-Decidable___isFalse) [(]](#manual-Eq___refl)a [=]](#manual-Eq___refl) b[)]](#manual-Eq___refl)



[ISize.decEq]](#manual-ISize___decEq) (a b : [ISize]](#manual-ISize___ofUSize)) :
  [Decidable]](#manual-Decidable___isFalse) [(]](#manual-Eq___refl)a [=]](#manual-Eq___refl) b[)]](#manual-Eq___refl)
```

Decides whether two word-sized signed integers are equal. Usually accessed via the
`[DecidableEq]](#manual-DecidableEq) [ISize]](#manual-ISize___ofUSize)` instance.

This function is overridden at runtime with an efficient implementation.

Examples:

- `[ISize.decEq]](#manual-ISize___decEq) 123 123 = [.isTrue]](#manual-Decidable___isFalse) [rfl]](#manual-rfl-next)`
- `([if]](#manual-termIfThenElse) ((-7) : [ISize]](#manual-ISize___ofUSize)) = 7 [then]](#manual-termIfThenElse) "yes" [else]](#manual-termIfThenElse) "no") = "no"`
- `show (7 : [ISize]](#manual-ISize___ofUSize)) = 7 by [decide]](#manual-decide)`

def

```lean
[UInt8.decEq]](#manual-UInt8___decEq) (a b : [UInt8]](#manual-UInt8___ofBitVec)) : [Decidable]](#manual-Decidable___isFalse) [(]](#manual-Eq___refl)a [=]](#manual-Eq___refl) b[)]](#manual-Eq___refl)



[UInt8.decEq]](#manual-UInt8___decEq) (a b : [UInt8]](#manual-UInt8___ofBitVec)) :
  [Decidable]](#manual-Decidable___isFalse) [(]](#manual-Eq___refl)a [=]](#manual-Eq___refl) b[)]](#manual-Eq___refl)
```

Decides whether two 8-bit unsigned integers are equal. Usually accessed via the `[DecidableEq]](#manual-DecidableEq) [UInt8]](#manual-UInt8___ofBitVec)`
instance.

This function is overridden at runtime with an efficient implementation.

Examples:

- `[UInt8.decEq]](#manual-UInt8___decEq) 123 123 = [.isTrue]](#manual-Decidable___isFalse) [rfl]](#manual-rfl-next)`
- `([if]](#manual-termIfThenElse) (6 : [UInt8]](#manual-UInt8___ofBitVec)) = 7 [then]](#manual-termIfThenElse) "yes" [else]](#manual-termIfThenElse) "no") = "no"`
- `show (7 : [UInt8]](#manual-UInt8___ofBitVec)) = 7 by [decide]](#manual-decide)`

def

```lean
[Int8.decEq]](#manual-Int8___decEq) (a b : [Int8]](#manual-Int8___ofUInt8)) : [Decidable]](#manual-Decidable___isFalse) [(]](#manual-Eq___refl)a [=]](#manual-Eq___refl) b[)]](#manual-Eq___refl)



[Int8.decEq]](#manual-Int8___decEq) (a b : [Int8]](#manual-Int8___ofUInt8)) :
  [Decidable]](#manual-Decidable___isFalse) [(]](#manual-Eq___refl)a [=]](#manual-Eq___refl) b[)]](#manual-Eq___refl)
```

Decides whether two 8-bit signed integers are equal. Usually accessed via the `[DecidableEq]](#manual-DecidableEq) [Int8]](#manual-Int8___ofUInt8)`
instance.

This function is overridden at runtime with an efficient implementation.

Examples:

- `[Int8.decEq]](#manual-Int8___decEq) 123 123 = [.isTrue]](#manual-Decidable___isFalse) [rfl]](#manual-rfl-next)`
- `([if]](#manual-termIfThenElse) ((-7) : [Int8]](#manual-Int8___ofUInt8)) = 7 [then]](#manual-termIfThenElse) "yes" [else]](#manual-termIfThenElse) "no") = "no"`
- `show (7 : [Int8]](#manual-Int8___ofUInt8)) = 7 by [decide]](#manual-decide)`

def

```lean
[UInt16.decEq]](#manual-UInt16___decEq) (a b : [UInt16]](#manual-UInt16___ofBitVec)) : [Decidable]](#manual-Decidable___isFalse) [(]](#manual-Eq___refl)a [=]](#manual-Eq___refl) b[)]](#manual-Eq___refl)



[UInt16.decEq]](#manual-UInt16___decEq) (a b : [UInt16]](#manual-UInt16___ofBitVec)) :
  [Decidable]](#manual-Decidable___isFalse) [(]](#manual-Eq___refl)a [=]](#manual-Eq___refl) b[)]](#manual-Eq___refl)
```

Decides whether two 16-bit unsigned integers are equal. Usually accessed via the
`[DecidableEq]](#manual-DecidableEq) [UInt16]](#manual-UInt16___ofBitVec)` instance.

This function is overridden at runtime with an efficient implementation.

Examples:

- `[UInt16.decEq]](#manual-UInt16___decEq) 123 123 = [.isTrue]](#manual-Decidable___isFalse) [rfl]](#manual-rfl-next)`
- `([if]](#manual-termIfThenElse) (6 : [UInt16]](#manual-UInt16___ofBitVec)) = 7 [then]](#manual-termIfThenElse) "yes" [else]](#manual-termIfThenElse) "no") = "no"`
- `show (7 : [UInt16]](#manual-UInt16___ofBitVec)) = 7 by [decide]](#manual-decide)`

def

```lean
[Int16.decEq]](#manual-Int16___decEq) (a b : [Int16]](#manual-Int16___ofUInt16)) : [Decidable]](#manual-Decidable___isFalse) [(]](#manual-Eq___refl)a [=]](#manual-Eq___refl) b[)]](#manual-Eq___refl)



[Int16.decEq]](#manual-Int16___decEq) (a b : [Int16]](#manual-Int16___ofUInt16)) :
  [Decidable]](#manual-Decidable___isFalse) [(]](#manual-Eq___refl)a [=]](#manual-Eq___refl) b[)]](#manual-Eq___refl)
```

Decides whether two 16-bit signed integers are equal. Usually accessed via the `[DecidableEq]](#manual-DecidableEq) [Int16]](#manual-Int16___ofUInt16)`
instance.

This function is overridden at runtime with an efficient implementation.

Examples:

- `[Int16.decEq]](#manual-Int16___decEq) 123 123 = [.isTrue]](#manual-Decidable___isFalse) [rfl]](#manual-rfl-next)`
- `([if]](#manual-termIfThenElse) ((-7) : [Int16]](#manual-Int16___ofUInt16)) = 7 [then]](#manual-termIfThenElse) "yes" [else]](#manual-termIfThenElse) "no") = "no"`
- `show (7 : [Int16]](#manual-Int16___ofUInt16)) = 7 by [decide]](#manual-decide)`

def

```lean
[UInt32.decEq]](#manual-UInt32___decEq) (a b : [UInt32]](#manual-UInt32___ofBitVec)) : [Decidable]](#manual-Decidable___isFalse) [(]](#manual-Eq___refl)a [=]](#manual-Eq___refl) b[)]](#manual-Eq___refl)



[UInt32.decEq]](#manual-UInt32___decEq) (a b : [UInt32]](#manual-UInt32___ofBitVec)) :
  [Decidable]](#manual-Decidable___isFalse) [(]](#manual-Eq___refl)a [=]](#manual-Eq___refl) b[)]](#manual-Eq___refl)
```

Decides whether two 32-bit unsigned integers are equal. Usually accessed via the
`[DecidableEq]](#manual-DecidableEq) [UInt32]](#manual-UInt32___ofBitVec)` instance.

This function is overridden at runtime with an efficient implementation.

Examples:

- `[UInt32.decEq]](#manual-UInt32___decEq) 123 123 = [.isTrue]](#manual-Decidable___isFalse) [rfl]](#manual-rfl-next)`
- `([if]](#manual-termIfThenElse) (6 : [UInt32]](#manual-UInt32___ofBitVec)) = 7 [then]](#manual-termIfThenElse) "yes" [else]](#manual-termIfThenElse) "no") = "no"`
- `show (7 : [UInt32]](#manual-UInt32___ofBitVec)) = 7 by [decide]](#manual-decide)`

def

```lean
[Int32.decEq]](#manual-Int32___decEq) (a b : [Int32]](#manual-Int32___ofUInt32)) : [Decidable]](#manual-Decidable___isFalse) [(]](#manual-Eq___refl)a [=]](#manual-Eq___refl) b[)]](#manual-Eq___refl)



[Int32.decEq]](#manual-Int32___decEq) (a b : [Int32]](#manual-Int32___ofUInt32)) :
  [Decidable]](#manual-Decidable___isFalse) [(]](#manual-Eq___refl)a [=]](#manual-Eq___refl) b[)]](#manual-Eq___refl)
```

Decides whether two 32-bit signed integers are equal. Usually accessed via the `[DecidableEq]](#manual-DecidableEq) [Int32]](#manual-Int32___ofUInt32)`
instance.

This function is overridden at runtime with an efficient implementation.

Examples:

- `[Int32.decEq]](#manual-Int32___decEq) 123 123 = [.isTrue]](#manual-Decidable___isFalse) [rfl]](#manual-rfl-next)`
- `([if]](#manual-termIfThenElse) ((-7) : [Int32]](#manual-Int32___ofUInt32)) = 7 [then]](#manual-termIfThenElse) "yes" [else]](#manual-termIfThenElse) "no") = "no"`
- `show (7 : [Int32]](#manual-Int32___ofUInt32)) = 7 by [decide]](#manual-decide)`

def

```lean
[UInt64.decEq]](#manual-UInt64___decEq) (a b : [UInt64]](#manual-UInt64___ofBitVec)) : [Decidable]](#manual-Decidable___isFalse) [(]](#manual-Eq___refl)a [=]](#manual-Eq___refl) b[)]](#manual-Eq___refl)



[UInt64.decEq]](#manual-UInt64___decEq) (a b : [UInt64]](#manual-UInt64___ofBitVec)) :
  [Decidable]](#manual-Decidable___isFalse) [(]](#manual-Eq___refl)a [=]](#manual-Eq___refl) b[)]](#manual-Eq___refl)
```

Decides whether two 64-bit unsigned integers are equal. Usually accessed via the
`[DecidableEq]](#manual-DecidableEq) [UInt64]](#manual-UInt64___ofBitVec)` instance.

This function is overridden at runtime with an efficient implementation.

Examples:

- `[UInt64.decEq]](#manual-UInt64___decEq) 123 123 = [.isTrue]](#manual-Decidable___isFalse) [rfl]](#manual-rfl-next)`
- `([if]](#manual-termIfThenElse) (6 : [UInt64]](#manual-UInt64___ofBitVec)) = 7 [then]](#manual-termIfThenElse) "yes" [else]](#manual-termIfThenElse) "no") = "no"`
- `show (7 : [UInt64]](#manual-UInt64___ofBitVec)) = 7 by [decide]](#manual-decide)`

def

```lean
[Int64.decEq]](#manual-Int64___decEq) (a b : [Int64]](#manual-Int64___ofUInt64)) : [Decidable]](#manual-Decidable___isFalse) [(]](#manual-Eq___refl)a [=]](#manual-Eq___refl) b[)]](#manual-Eq___refl)



[Int64.decEq]](#manual-Int64___decEq) (a b : [Int64]](#manual-Int64___ofUInt64)) :
  [Decidable]](#manual-Decidable___isFalse) [(]](#manual-Eq___refl)a [=]](#manual-Eq___refl) b[)]](#manual-Eq___refl)
```

Decides whether two 64-bit signed integers are equal. Usually accessed via the `[DecidableEq]](#manual-DecidableEq) [Int64]](#manual-Int64___ofUInt64)`
instance.

This function is overridden at runtime with an efficient implementation.

Examples:

- `[Int64.decEq]](#manual-Int64___decEq) 123 123 = [.isTrue]](#manual-Decidable___isFalse) [rfl]](#manual-rfl-next)`
- `([if]](#manual-termIfThenElse) ((-7) : [Int64]](#manual-Int64___ofUInt64)) = 7 [then]](#manual-termIfThenElse) "yes" [else]](#manual-termIfThenElse) "no") = "no"`
- `show (7 : [Int64]](#manual-Int64___ofUInt64)) = 7 by [decide]](#manual-decide)`

def

```lean
[USize.decLe]](#manual-USize___decLe) (a b : [USize]](#manual-USize___ofBitVec)) : [Decidable]](#manual-Decidable___isFalse) [(]](#manual-LE___mk)a [≤]](#manual-LE___mk) b[)]](#manual-LE___mk)



[USize.decLe]](#manual-USize___decLe) (a b : [USize]](#manual-USize___ofBitVec)) :
  [Decidable]](#manual-Decidable___isFalse) [(]](#manual-LE___mk)a [≤]](#manual-LE___mk) b[)]](#manual-LE___mk)
```

Decides whether one word-sized unsigned integer is less than or equal to another. Usually accessed
via the `[DecidableLE]](#manual-DecidableLE) [USize]](#manual-USize___ofBitVec)` instance.

This function is overridden at runtime with an efficient implementation.

Examples:

- `([if]](#manual-termIfThenElse) (15 : [USize]](#manual-USize___ofBitVec)) ≤ 15 [then]](#manual-termIfThenElse) "yes" [else]](#manual-termIfThenElse) "no") = "yes"`
- `([if]](#manual-termIfThenElse) (15 : [USize]](#manual-USize___ofBitVec)) ≤ 5 [then]](#manual-termIfThenElse) "yes" [else]](#manual-termIfThenElse) "no") = "no"`
- `([if]](#manual-termIfThenElse) (5 : [USize]](#manual-USize___ofBitVec)) ≤ 15 [then]](#manual-termIfThenElse) "yes" [else]](#manual-termIfThenElse) "no") = "yes"`
- `show (7 : [USize]](#manual-USize___ofBitVec)) ≤ 7 by [decide]](#manual-decide)`

def

```lean
[ISize.decLe]](#manual-ISize___decLe) (a b : [ISize]](#manual-ISize___ofUSize)) : [Decidable]](#manual-Decidable___isFalse) [(]](#manual-LE___mk)a [≤]](#manual-LE___mk) b[)]](#manual-LE___mk)



[ISize.decLe]](#manual-ISize___decLe) (a b : [ISize]](#manual-ISize___ofUSize)) :
  [Decidable]](#manual-Decidable___isFalse) [(]](#manual-LE___mk)a [≤]](#manual-LE___mk) b[)]](#manual-LE___mk)
```

Decides whether one word-sized signed integer is less than or equal to another. Usually accessed via
the `[DecidableLE]](#manual-DecidableLE) [ISize]](#manual-ISize___ofUSize)` instance.

This function is overridden at runtime with an efficient implementation.

Examples:

- `([if]](#manual-termIfThenElse) ((-7) : [ISize]](#manual-ISize___ofUSize)) ≤ 7 [then]](#manual-termIfThenElse) "yes" [else]](#manual-termIfThenElse) "no") = "yes"`
- `([if]](#manual-termIfThenElse) (15 : [ISize]](#manual-ISize___ofUSize)) ≤ 15 [then]](#manual-termIfThenElse) "yes" [else]](#manual-termIfThenElse) "no") = "yes"`
- `([if]](#manual-termIfThenElse) (15 : [ISize]](#manual-ISize___ofUSize)) ≤ 5 [then]](#manual-termIfThenElse) "yes" [else]](#manual-termIfThenElse) "no") = "no"`
- `show (7 : [ISize]](#manual-ISize___ofUSize)) ≤ 7 by [decide]](#manual-decide)`

def

```lean
[UInt8.decLe]](#manual-UInt8___decLe) (a b : [UInt8]](#manual-UInt8___ofBitVec)) : [Decidable]](#manual-Decidable___isFalse) [(]](#manual-LE___mk)a [≤]](#manual-LE___mk) b[)]](#manual-LE___mk)



[UInt8.decLe]](#manual-UInt8___decLe) (a b : [UInt8]](#manual-UInt8___ofBitVec)) :
  [Decidable]](#manual-Decidable___isFalse) [(]](#manual-LE___mk)a [≤]](#manual-LE___mk) b[)]](#manual-LE___mk)
```

Decides whether one 8-bit unsigned integer is less than or equal to another. Usually accessed via the
`[DecidableLE]](#manual-DecidableLE) [UInt8]](#manual-UInt8___ofBitVec)` instance.

This function is overridden at runtime with an efficient implementation.

Examples:

- `([if]](#manual-termIfThenElse) (15 : [UInt8]](#manual-UInt8___ofBitVec)) ≤ 15 [then]](#manual-termIfThenElse) "yes" [else]](#manual-termIfThenElse) "no") = "yes"`
- `([if]](#manual-termIfThenElse) (15 : [UInt8]](#manual-UInt8___ofBitVec)) ≤ 5 [then]](#manual-termIfThenElse) "yes" [else]](#manual-termIfThenElse) "no") = "no"`
- `([if]](#manual-termIfThenElse) (5 : [UInt8]](#manual-UInt8___ofBitVec)) ≤ 15 [then]](#manual-termIfThenElse) "yes" [else]](#manual-termIfThenElse) "no") = "yes"`
- `show (7 : [UInt8]](#manual-UInt8___ofBitVec)) ≤ 7 by [decide]](#manual-decide)`

def

```lean
[Int8.decLe]](#manual-Int8___decLe) (a b : [Int8]](#manual-Int8___ofUInt8)) : [Decidable]](#manual-Decidable___isFalse) [(]](#manual-LE___mk)a [≤]](#manual-LE___mk) b[)]](#manual-LE___mk)



[Int8.decLe]](#manual-Int8___decLe) (a b : [Int8]](#manual-Int8___ofUInt8)) :
  [Decidable]](#manual-Decidable___isFalse) [(]](#manual-LE___mk)a [≤]](#manual-LE___mk) b[)]](#manual-LE___mk)
```

Decides whether one 8-bit signed integer is less than or equal to another. Usually accessed via the
`[DecidableLE]](#manual-DecidableLE) [Int8]](#manual-Int8___ofUInt8)` instance.

This function is overridden at runtime with an efficient implementation.

Examples:

- `([if]](#manual-termIfThenElse) ((-7) : [Int8]](#manual-Int8___ofUInt8)) ≤ 7 [then]](#manual-termIfThenElse) "yes" [else]](#manual-termIfThenElse) "no") = "yes"`
- `([if]](#manual-termIfThenElse) (15 : [Int8]](#manual-Int8___ofUInt8)) ≤ 15 [then]](#manual-termIfThenElse) "yes" [else]](#manual-termIfThenElse) "no") = "yes"`
- `([if]](#manual-termIfThenElse) (15 : [Int8]](#manual-Int8___ofUInt8)) ≤ 5 [then]](#manual-termIfThenElse) "yes" [else]](#manual-termIfThenElse) "no") = "no"`
- `show (7 : [Int8]](#manual-Int8___ofUInt8)) ≤ 7 by [decide]](#manual-decide)`

def

```lean
[UInt16.decLe]](#manual-UInt16___decLe) (a b : [UInt16]](#manual-UInt16___ofBitVec)) : [Decidable]](#manual-Decidable___isFalse) [(]](#manual-LE___mk)a [≤]](#manual-LE___mk) b[)]](#manual-LE___mk)



[UInt16.decLe]](#manual-UInt16___decLe) (a b : [UInt16]](#manual-UInt16___ofBitVec)) :
  [Decidable]](#manual-Decidable___isFalse) [(]](#manual-LE___mk)a [≤]](#manual-LE___mk) b[)]](#manual-LE___mk)
```

Decides whether one 16-bit unsigned integer is less than or equal to another. Usually accessed via the
`[DecidableLE]](#manual-DecidableLE) [UInt16]](#manual-UInt16___ofBitVec)` instance.

This function is overridden at runtime with an efficient implementation.

Examples:

- `([if]](#manual-termIfThenElse) (15 : [UInt16]](#manual-UInt16___ofBitVec)) ≤ 15 [then]](#manual-termIfThenElse) "yes" [else]](#manual-termIfThenElse) "no") = "yes"`
- `([if]](#manual-termIfThenElse) (15 : [UInt16]](#manual-UInt16___ofBitVec)) ≤ 5 [then]](#manual-termIfThenElse) "yes" [else]](#manual-termIfThenElse) "no") = "no"`
- `([if]](#manual-termIfThenElse) (5 : [UInt16]](#manual-UInt16___ofBitVec)) ≤ 15 [then]](#manual-termIfThenElse) "yes" [else]](#manual-termIfThenElse) "no") = "yes"`
- `show (7 : [UInt16]](#manual-UInt16___ofBitVec)) ≤ 7 by [decide]](#manual-decide)`

def

```lean
[Int16.decLe]](#manual-Int16___decLe) (a b : [Int16]](#manual-Int16___ofUInt16)) : [Decidable]](#manual-Decidable___isFalse) [(]](#manual-LE___mk)a [≤]](#manual-LE___mk) b[)]](#manual-LE___mk)



[Int16.decLe]](#manual-Int16___decLe) (a b : [Int16]](#manual-Int16___ofUInt16)) :
  [Decidable]](#manual-Decidable___isFalse) [(]](#manual-LE___mk)a [≤]](#manual-LE___mk) b[)]](#manual-LE___mk)
```

Decides whether one 16-bit signed integer is less than or equal to another. Usually accessed via the
`[DecidableLE]](#manual-DecidableLE) [Int16]](#manual-Int16___ofUInt16)` instance.

This function is overridden at runtime with an efficient implementation.

Examples:

- `([if]](#manual-termIfThenElse) ((-7) : [Int16]](#manual-Int16___ofUInt16)) ≤ 7 [then]](#manual-termIfThenElse) "yes" [else]](#manual-termIfThenElse) "no") = "yes"`
- `([if]](#manual-termIfThenElse) (15 : [Int16]](#manual-Int16___ofUInt16)) ≤ 15 [then]](#manual-termIfThenElse) "yes" [else]](#manual-termIfThenElse) "no") = "yes"`
- `([if]](#manual-termIfThenElse) (15 : [Int16]](#manual-Int16___ofUInt16)) ≤ 5 [then]](#manual-termIfThenElse) "yes" [else]](#manual-termIfThenElse) "no") = "no"`
- `show (7 : [Int16]](#manual-Int16___ofUInt16)) ≤ 7 by [decide]](#manual-decide)`

def

```lean
[UInt32.decLe]](#manual-UInt32___decLe) (a b : [UInt32]](#manual-UInt32___ofBitVec)) : [Decidable]](#manual-Decidable___isFalse) [(]](#manual-LE___mk)a [≤]](#manual-LE___mk) b[)]](#manual-LE___mk)



[UInt32.decLe]](#manual-UInt32___decLe) (a b : [UInt32]](#manual-UInt32___ofBitVec)) :
  [Decidable]](#manual-Decidable___isFalse) [(]](#manual-LE___mk)a [≤]](#manual-LE___mk) b[)]](#manual-LE___mk)
```

Decides whether one 32-bit signed integer is less than or equal to another. Usually accessed via the
`[DecidableLE]](#manual-DecidableLE) [UInt32]](#manual-UInt32___ofBitVec)` instance.

This function is overridden at runtime with an efficient implementation.

Examples:

- `([if]](#manual-termIfThenElse) (15 : [UInt32]](#manual-UInt32___ofBitVec)) ≤ 15 [then]](#manual-termIfThenElse) "yes" [else]](#manual-termIfThenElse) "no") = "yes"`
- `([if]](#manual-termIfThenElse) (15 : [UInt32]](#manual-UInt32___ofBitVec)) ≤ 5 [then]](#manual-termIfThenElse) "yes" [else]](#manual-termIfThenElse) "no") = "no"`
- `([if]](#manual-termIfThenElse) (5 : [UInt32]](#manual-UInt32___ofBitVec)) ≤ 15 [then]](#manual-termIfThenElse) "yes" [else]](#manual-termIfThenElse) "no") = "yes"`
- `show (7 : [UInt32]](#manual-UInt32___ofBitVec)) ≤ 7 by [decide]](#manual-decide)`

def

```lean
[Int32.decLe]](#manual-Int32___decLe) (a b : [Int32]](#manual-Int32___ofUInt32)) : [Decidable]](#manual-Decidable___isFalse) [(]](#manual-LE___mk)a [≤]](#manual-LE___mk) b[)]](#manual-LE___mk)



[Int32.decLe]](#manual-Int32___decLe) (a b : [Int32]](#manual-Int32___ofUInt32)) :
  [Decidable]](#manual-Decidable___isFalse) [(]](#manual-LE___mk)a [≤]](#manual-LE___mk) b[)]](#manual-LE___mk)
```

Decides whether one 32-bit signed integer is less than or equal to another. Usually accessed via the
`[DecidableLE]](#manual-DecidableLE) [Int32]](#manual-Int32___ofUInt32)` instance.

This function is overridden at runtime with an efficient implementation.

Examples:

- `([if]](#manual-termIfThenElse) ((-7) : [Int32]](#manual-Int32___ofUInt32)) ≤ 7 [then]](#manual-termIfThenElse) "yes" [else]](#manual-termIfThenElse) "no") = "yes"`
- `([if]](#manual-termIfThenElse) (15 : [Int32]](#manual-Int32___ofUInt32)) ≤ 15 [then]](#manual-termIfThenElse) "yes" [else]](#manual-termIfThenElse) "no") = "yes"`
- `([if]](#manual-termIfThenElse) (15 : [Int32]](#manual-Int32___ofUInt32)) ≤ 5 [then]](#manual-termIfThenElse) "yes" [else]](#manual-termIfThenElse) "no") = "no"`
- `show (7 : [Int32]](#manual-Int32___ofUInt32)) ≤ 7 by [decide]](#manual-decide)`

def

```lean
[UInt64.decLe]](#manual-UInt64___decLe) (a b : [UInt64]](#manual-UInt64___ofBitVec)) : [Decidable]](#manual-Decidable___isFalse) [(]](#manual-LE___mk)a [≤]](#manual-LE___mk) b[)]](#manual-LE___mk)



[UInt64.decLe]](#manual-UInt64___decLe) (a b : [UInt64]](#manual-UInt64___ofBitVec)) :
  [Decidable]](#manual-Decidable___isFalse) [(]](#manual-LE___mk)a [≤]](#manual-LE___mk) b[)]](#manual-LE___mk)
```

Decides whether one 64-bit unsigned integer is less than or equal to another. Usually accessed via the
`[DecidableLE]](#manual-DecidableLE) [UInt64]](#manual-UInt64___ofBitVec)` instance.

This function is overridden at runtime with an efficient implementation.

Examples:

- `([if]](#manual-termIfThenElse) (15 : [UInt64]](#manual-UInt64___ofBitVec)) ≤ 15 [then]](#manual-termIfThenElse) "yes" [else]](#manual-termIfThenElse) "no") = "yes"`
- `([if]](#manual-termIfThenElse) (15 : [UInt64]](#manual-UInt64___ofBitVec)) ≤ 5 [then]](#manual-termIfThenElse) "yes" [else]](#manual-termIfThenElse) "no") = "no"`
- `([if]](#manual-termIfThenElse) (5 : [UInt64]](#manual-UInt64___ofBitVec)) ≤ 15 [then]](#manual-termIfThenElse) "yes" [else]](#manual-termIfThenElse) "no") = "yes"`
- `show (7 : [UInt64]](#manual-UInt64___ofBitVec)) ≤ 7 by [decide]](#manual-decide)`

def

```lean
[Int64.decLe]](#manual-Int64___decLe) (a b : [Int64]](#manual-Int64___ofUInt64)) : [Decidable]](#manual-Decidable___isFalse) [(]](#manual-LE___mk)a [≤]](#manual-LE___mk) b[)]](#manual-LE___mk)



[Int64.decLe]](#manual-Int64___decLe) (a b : [Int64]](#manual-Int64___ofUInt64)) :
  [Decidable]](#manual-Decidable___isFalse) [(]](#manual-LE___mk)a [≤]](#manual-LE___mk) b[)]](#manual-LE___mk)
```

Decides whether one 8-bit signed integer is less than or equal to another. Usually accessed via the
`[DecidableLE]](#manual-DecidableLE) [Int64]](#manual-Int64___ofUInt64)` instance.

This function is overridden at runtime with an efficient implementation.

Examples:

- `([if]](#manual-termIfThenElse) ((-7) : [Int64]](#manual-Int64___ofUInt64)) ≤ 7 [then]](#manual-termIfThenElse) "yes" [else]](#manual-termIfThenElse) "no") = "yes"`
- `([if]](#manual-termIfThenElse) (15 : [Int64]](#manual-Int64___ofUInt64)) ≤ 15 [then]](#manual-termIfThenElse) "yes" [else]](#manual-termIfThenElse) "no") = "yes"`
- `([if]](#manual-termIfThenElse) (15 : [Int64]](#manual-Int64___ofUInt64)) ≤ 5 [then]](#manual-termIfThenElse) "yes" [else]](#manual-termIfThenElse) "no") = "no"`
- `show (7 : [Int64]](#manual-Int64___ofUInt64)) ≤ 7 by [decide]](#manual-decide)`

def

```lean
[USize.decLt]](#manual-USize___decLt) (a b : [USize]](#manual-USize___ofBitVec)) : [Decidable]](#manual-Decidable___isFalse) [(]](#manual-LT___mk)a [<]](#manual-LT___mk) b[)]](#manual-LT___mk)



[USize.decLt]](#manual-USize___decLt) (a b : [USize]](#manual-USize___ofBitVec)) :
  [Decidable]](#manual-Decidable___isFalse) [(]](#manual-LT___mk)a [<]](#manual-LT___mk) b[)]](#manual-LT___mk)
```

Decides whether one word-sized unsigned integer is strictly less than another. Usually accessed via
the `[DecidableLT]](#manual-DecidableLT) [USize]](#manual-USize___ofBitVec)` instance.

This function is overridden at runtime with an efficient implementation.

Examples:

- `([if]](#manual-termIfThenElse) (6 : [USize]](#manual-USize___ofBitVec)) < 7 [then]](#manual-termIfThenElse) "yes" [else]](#manual-termIfThenElse) "no") = "yes"`
- `([if]](#manual-termIfThenElse) (5 : [USize]](#manual-USize___ofBitVec)) < 5 [then]](#manual-termIfThenElse) "yes" [else]](#manual-termIfThenElse) "no") = "no"`
- `show ¬((7 : [USize]](#manual-USize___ofBitVec)) < 7) by [decide]](#manual-decide)`

def

```lean
[ISize.decLt]](#manual-ISize___decLt) (a b : [ISize]](#manual-ISize___ofUSize)) : [Decidable]](#manual-Decidable___isFalse) [(]](#manual-LT___mk)a [<]](#manual-LT___mk) b[)]](#manual-LT___mk)



[ISize.decLt]](#manual-ISize___decLt) (a b : [ISize]](#manual-ISize___ofUSize)) :
  [Decidable]](#manual-Decidable___isFalse) [(]](#manual-LT___mk)a [<]](#manual-LT___mk) b[)]](#manual-LT___mk)
```

Decides whether one word-sized signed integer is strictly less than another. Usually accessed via the
`[DecidableLT]](#manual-DecidableLT) [ISize]](#manual-ISize___ofUSize)` instance.

This function is overridden at runtime with an efficient implementation.

Examples:

- `([if]](#manual-termIfThenElse) ((-7) : [ISize]](#manual-ISize___ofUSize)) < 7 [then]](#manual-termIfThenElse) "yes" [else]](#manual-termIfThenElse) "no") = "yes"`
- `([if]](#manual-termIfThenElse) (5 : [ISize]](#manual-ISize___ofUSize)) < 5 [then]](#manual-termIfThenElse) "yes" [else]](#manual-termIfThenElse) "no") = "no"`
- `show ¬((7 : [ISize]](#manual-ISize___ofUSize)) < 7) by [decide]](#manual-decide)`

def

```lean
[UInt8.decLt]](#manual-UInt8___decLt) (a b : [UInt8]](#manual-UInt8___ofBitVec)) : [Decidable]](#manual-Decidable___isFalse) [(]](#manual-LT___mk)a [<]](#manual-LT___mk) b[)]](#manual-LT___mk)



[UInt8.decLt]](#manual-UInt8___decLt) (a b : [UInt8]](#manual-UInt8___ofBitVec)) :
  [Decidable]](#manual-Decidable___isFalse) [(]](#manual-LT___mk)a [<]](#manual-LT___mk) b[)]](#manual-LT___mk)
```

Decides whether one 8-bit unsigned integer is strictly less than another. Usually accessed via the
`[DecidableLT]](#manual-DecidableLT) [UInt8]](#manual-UInt8___ofBitVec)` instance.

This function is overridden at runtime with an efficient implementation.

Examples:

- `([if]](#manual-termIfThenElse) (6 : [UInt8]](#manual-UInt8___ofBitVec)) < 7 [then]](#manual-termIfThenElse) "yes" [else]](#manual-termIfThenElse) "no") = "yes"`
- `([if]](#manual-termIfThenElse) (5 : [UInt8]](#manual-UInt8___ofBitVec)) < 5 [then]](#manual-termIfThenElse) "yes" [else]](#manual-termIfThenElse) "no") = "no"`
- `show ¬((7 : [UInt8]](#manual-UInt8___ofBitVec)) < 7) by [decide]](#manual-decide)`

def

```lean
[Int8.decLt]](#manual-Int8___decLt) (a b : [Int8]](#manual-Int8___ofUInt8)) : [Decidable]](#manual-Decidable___isFalse) [(]](#manual-LT___mk)a [<]](#manual-LT___mk) b[)]](#manual-LT___mk)



[Int8.decLt]](#manual-Int8___decLt) (a b : [Int8]](#manual-Int8___ofUInt8)) :
  [Decidable]](#manual-Decidable___isFalse) [(]](#manual-LT___mk)a [<]](#manual-LT___mk) b[)]](#manual-LT___mk)
```

Decides whether one 8-bit signed integer is strictly less than another. Usually accessed via the
`[DecidableLT]](#manual-DecidableLT) [Int8]](#manual-Int8___ofUInt8)` instance.

This function is overridden at runtime with an efficient implementation.

Examples:

- `([if]](#manual-termIfThenElse) ((-7) : [Int8]](#manual-Int8___ofUInt8)) < 7 [then]](#manual-termIfThenElse) "yes" [else]](#manual-termIfThenElse) "no") = "yes"`
- `([if]](#manual-termIfThenElse) (5 : [Int8]](#manual-Int8___ofUInt8)) < 5 [then]](#manual-termIfThenElse) "yes" [else]](#manual-termIfThenElse) "no") = "no"`
- `show ¬((7 : [Int8]](#manual-Int8___ofUInt8)) < 7) by [decide]](#manual-decide)`

def

```lean
[UInt16.decLt]](#manual-UInt16___decLt) (a b : [UInt16]](#manual-UInt16___ofBitVec)) : [Decidable]](#manual-Decidable___isFalse) [(]](#manual-LT___mk)a [<]](#manual-LT___mk) b[)]](#manual-LT___mk)



[UInt16.decLt]](#manual-UInt16___decLt) (a b : [UInt16]](#manual-UInt16___ofBitVec)) :
  [Decidable]](#manual-Decidable___isFalse) [(]](#manual-LT___mk)a [<]](#manual-LT___mk) b[)]](#manual-LT___mk)
```

Decides whether one 16-bit unsigned integer is strictly less than another. Usually accessed via the
`[DecidableLT]](#manual-DecidableLT) [UInt16]](#manual-UInt16___ofBitVec)` instance.

This function is overridden at runtime with an efficient implementation.

Examples:

- `([if]](#manual-termIfThenElse) (6 : [UInt16]](#manual-UInt16___ofBitVec)) < 7 [then]](#manual-termIfThenElse) "yes" [else]](#manual-termIfThenElse) "no") = "yes"`
- `([if]](#manual-termIfThenElse) (5 : [UInt16]](#manual-UInt16___ofBitVec)) < 5 [then]](#manual-termIfThenElse) "yes" [else]](#manual-termIfThenElse) "no") = "no"`
- `show ¬((7 : [UInt16]](#manual-UInt16___ofBitVec)) < 7) by [decide]](#manual-decide)`

def

```lean
[Int16.decLt]](#manual-Int16___decLt) (a b : [Int16]](#manual-Int16___ofUInt16)) : [Decidable]](#manual-Decidable___isFalse) [(]](#manual-LT___mk)a [<]](#manual-LT___mk) b[)]](#manual-LT___mk)



[Int16.decLt]](#manual-Int16___decLt) (a b : [Int16]](#manual-Int16___ofUInt16)) :
  [Decidable]](#manual-Decidable___isFalse) [(]](#manual-LT___mk)a [<]](#manual-LT___mk) b[)]](#manual-LT___mk)
```

Decides whether one 16-bit signed integer is strictly less than another. Usually accessed via the
`[DecidableLT]](#manual-DecidableLT) [Int16]](#manual-Int16___ofUInt16)` instance.

This function is overridden at runtime with an efficient implementation.

Examples:

- `([if]](#manual-termIfThenElse) ((-7) : [Int16]](#manual-Int16___ofUInt16)) < 7 [then]](#manual-termIfThenElse) "yes" [else]](#manual-termIfThenElse) "no") = "yes"`
- `([if]](#manual-termIfThenElse) (5 : [Int16]](#manual-Int16___ofUInt16)) < 5 [then]](#manual-termIfThenElse) "yes" [else]](#manual-termIfThenElse) "no") = "no"`
- `show ¬((7 : [Int16]](#manual-Int16___ofUInt16)) < 7) by [decide]](#manual-decide)`

def

```lean
[UInt32.decLt]](#manual-UInt32___decLt) (a b : [UInt32]](#manual-UInt32___ofBitVec)) : [Decidable]](#manual-Decidable___isFalse) [(]](#manual-LT___mk)a [<]](#manual-LT___mk) b[)]](#manual-LT___mk)



[UInt32.decLt]](#manual-UInt32___decLt) (a b : [UInt32]](#manual-UInt32___ofBitVec)) :
  [Decidable]](#manual-Decidable___isFalse) [(]](#manual-LT___mk)a [<]](#manual-LT___mk) b[)]](#manual-LT___mk)
```

Decides whether one 8-bit unsigned integer is strictly less than another. Usually accessed via the
`[DecidableLT]](#manual-DecidableLT) [UInt32]](#manual-UInt32___ofBitVec)` instance.

This function is overridden at runtime with an efficient implementation.

Examples:

- `([if]](#manual-termIfThenElse) (6 : [UInt32]](#manual-UInt32___ofBitVec)) < 7 [then]](#manual-termIfThenElse) "yes" [else]](#manual-termIfThenElse) "no") = "yes"`
- `([if]](#manual-termIfThenElse) (5 : [UInt32]](#manual-UInt32___ofBitVec)) < 5 [then]](#manual-termIfThenElse) "yes" [else]](#manual-termIfThenElse) "no") = "no"`
- `show ¬((7 : [UInt32]](#manual-UInt32___ofBitVec)) < 7) by [decide]](#manual-decide)`

def

```lean
[Int32.decLt]](#manual-Int32___decLt) (a b : [Int32]](#manual-Int32___ofUInt32)) : [Decidable]](#manual-Decidable___isFalse) [(]](#manual-LT___mk)a [<]](#manual-LT___mk) b[)]](#manual-LT___mk)



[Int32.decLt]](#manual-Int32___decLt) (a b : [Int32]](#manual-Int32___ofUInt32)) :
  [Decidable]](#manual-Decidable___isFalse) [(]](#manual-LT___mk)a [<]](#manual-LT___mk) b[)]](#manual-LT___mk)
```

Decides whether one 32-bit signed integer is strictly less than another. Usually accessed via the
`[DecidableLT]](#manual-DecidableLT) [Int32]](#manual-Int32___ofUInt32)` instance.

This function is overridden at runtime with an efficient implementation.

Examples:

- `([if]](#manual-termIfThenElse) ((-7) : [Int32]](#manual-Int32___ofUInt32)) < 7 [then]](#manual-termIfThenElse) "yes" [else]](#manual-termIfThenElse) "no") = "yes"`
- `([if]](#manual-termIfThenElse) (5 : [Int32]](#manual-Int32___ofUInt32)) < 5 [then]](#manual-termIfThenElse) "yes" [else]](#manual-termIfThenElse) "no") = "no"`
- `show ¬((7 : [Int32]](#manual-Int32___ofUInt32)) < 7) by [decide]](#manual-decide)`

def

```lean
[UInt64.decLt]](#manual-UInt64___decLt) (a b : [UInt64]](#manual-UInt64___ofBitVec)) : [Decidable]](#manual-Decidable___isFalse) [(]](#manual-LT___mk)a [<]](#manual-LT___mk) b[)]](#manual-LT___mk)



[UInt64.decLt]](#manual-UInt64___decLt) (a b : [UInt64]](#manual-UInt64___ofBitVec)) :
  [Decidable]](#manual-Decidable___isFalse) [(]](#manual-LT___mk)a [<]](#manual-LT___mk) b[)]](#manual-LT___mk)
```

Decides whether one 64-bit unsigned integer is strictly less than another. Usually accessed via the
`[DecidableLT]](#manual-DecidableLT) [UInt64]](#manual-UInt64___ofBitVec)` instance.

This function is overridden at runtime with an efficient implementation.

Examples:

- `([if]](#manual-termIfThenElse) (6 : [UInt64]](#manual-UInt64___ofBitVec)) < 7 [then]](#manual-termIfThenElse) "yes" [else]](#manual-termIfThenElse) "no") = "yes"`
- `([if]](#manual-termIfThenElse) (5 : [UInt64]](#manual-UInt64___ofBitVec)) < 5 [then]](#manual-termIfThenElse) "yes" [else]](#manual-termIfThenElse) "no") = "no"`
- `show ¬((7 : [UInt64]](#manual-UInt64___ofBitVec)) < 7) by [decide]](#manual-decide)`

def

```lean
[Int64.decLt]](#manual-Int64___decLt) (a b : [Int64]](#manual-Int64___ofUInt64)) : [Decidable]](#manual-Decidable___isFalse) [(]](#manual-LT___mk)a [<]](#manual-LT___mk) b[)]](#manual-LT___mk)



[Int64.decLt]](#manual-Int64___decLt) (a b : [Int64]](#manual-Int64___ofUInt64)) :
  [Decidable]](#manual-Decidable___isFalse) [(]](#manual-LT___mk)a [<]](#manual-LT___mk) b[)]](#manual-LT___mk)
```

Decides whether one 8-bit signed integer is strictly less than another. Usually accessed via the
`[DecidableLT]](#manual-DecidableLT) [Int64]](#manual-Int64___ofUInt64)` instance.

This function is overridden at runtime with an efficient implementation.

Examples:

- `([if]](#manual-termIfThenElse) ((-7) : [Int64]](#manual-Int64___ofUInt64)) < 7 [then]](#manual-termIfThenElse) "yes" [else]](#manual-termIfThenElse) "no") = "yes"`
- `([if]](#manual-termIfThenElse) (5 : [Int64]](#manual-Int64___ofUInt64)) < 5 [then]](#manual-termIfThenElse) "yes" [else]](#manual-termIfThenElse) "no") = "no"`
- `show ¬((7 : [Int64]](#manual-Int64___ofUInt64)) < 7) by [decide]](#manual-decide)`

#### 20.4.4.5. Arithmetic {#manual-fixed-int-arithmetic}

Typically, arithmetic operations on fixed-width integers should be accessed using Lean's overloaded arithmetic notation, particularly their instances of `[Add]](#manual-Add___mk)`, `[Sub]](#manual-Sub___mk)`, `[Mul]](#manual-Mul___mk)`, `[Div]](#manual-Div___mk)`, and `[Mod]](#manual-Mod___mk)`, as well as `[Neg]](#manual-Neg___mk)` for signed types.

def

```lean
[ISize.neg]](#manual-ISize___neg) (i : [ISize]](#manual-ISize___ofUSize)) : [ISize]](#manual-ISize___ofUSize)



[ISize.neg]](#manual-ISize___neg) (i : [ISize]](#manual-ISize___ofUSize)) : [ISize]](#manual-ISize___ofUSize)
```

Negates word-sized signed integers. Usually accessed via the `-` prefix operator.

This function is overridden at runtime with an efficient implementation.

def

```lean
[Int8.neg]](#manual-Int8___neg) (i : [Int8]](#manual-Int8___ofUInt8)) : [Int8]](#manual-Int8___ofUInt8)



[Int8.neg]](#manual-Int8___neg) (i : [Int8]](#manual-Int8___ofUInt8)) : [Int8]](#manual-Int8___ofUInt8)
```

Negates 8-bit signed integers. Usually accessed via the `-` prefix operator.

This function is overridden at runtime with an efficient implementation.

def

```lean
[Int16.neg]](#manual-Int16___neg) (i : [Int16]](#manual-Int16___ofUInt16)) : [Int16]](#manual-Int16___ofUInt16)



[Int16.neg]](#manual-Int16___neg) (i : [Int16]](#manual-Int16___ofUInt16)) : [Int16]](#manual-Int16___ofUInt16)
```

Negates 16-bit signed integers. Usually accessed via the `-` prefix operator.

This function is overridden at runtime with an efficient implementation.

def

```lean
[Int32.neg]](#manual-Int32___neg) (i : [Int32]](#manual-Int32___ofUInt32)) : [Int32]](#manual-Int32___ofUInt32)



[Int32.neg]](#manual-Int32___neg) (i : [Int32]](#manual-Int32___ofUInt32)) : [Int32]](#manual-Int32___ofUInt32)
```

Negates 32-bit signed integers. Usually accessed via the `-` prefix operator.

This function is overridden at runtime with an efficient implementation.

def

```lean
[Int64.neg]](#manual-Int64___neg) (i : [Int64]](#manual-Int64___ofUInt64)) : [Int64]](#manual-Int64___ofUInt64)



[Int64.neg]](#manual-Int64___neg) (i : [Int64]](#manual-Int64___ofUInt64)) : [Int64]](#manual-Int64___ofUInt64)
```

Negates 64-bit signed integers. Usually accessed via the `-` prefix operator.

This function is overridden at runtime with an efficient implementation.

def

```lean
[USize.neg]](#manual-USize___neg) (a : [USize]](#manual-USize___ofBitVec)) : [USize]](#manual-USize___ofBitVec)



[USize.neg]](#manual-USize___neg) (a : [USize]](#manual-USize___ofBitVec)) : [USize]](#manual-USize___ofBitVec)
```

Negation of word-sized unsigned integers, computed modulo `[USize.size]](#manual-USize___size)`.

This function is overridden at runtime with an efficient implementation.

def

```lean
[UInt8.neg]](#manual-UInt8___neg) (a : [UInt8]](#manual-UInt8___ofBitVec)) : [UInt8]](#manual-UInt8___ofBitVec)



[UInt8.neg]](#manual-UInt8___neg) (a : [UInt8]](#manual-UInt8___ofBitVec)) : [UInt8]](#manual-UInt8___ofBitVec)
```

Negation of 8-bit unsigned integers, computed modulo `[UInt8.size]](#manual-UInt8___size)`.

`[UInt8.neg]](#manual-UInt8___neg) a` is equivalent to `255 - a + 1`.

This function is overridden at runtime with an efficient implementation.

def

```lean
[UInt16.neg]](#manual-UInt16___neg) (a : [UInt16]](#manual-UInt16___ofBitVec)) : [UInt16]](#manual-UInt16___ofBitVec)



[UInt16.neg]](#manual-UInt16___neg) (a : [UInt16]](#manual-UInt16___ofBitVec)) : [UInt16]](#manual-UInt16___ofBitVec)
```

Negation of 16-bit unsigned integers, computed modulo `[UInt16.size]](#manual-UInt16___size)`.

`[UInt16.neg]](#manual-UInt16___neg) a` is equivalent to `65_535 - a + 1`.

This function is overridden at runtime with an efficient implementation.

def

```lean
[UInt32.neg]](#manual-UInt32___neg) (a : [UInt32]](#manual-UInt32___ofBitVec)) : [UInt32]](#manual-UInt32___ofBitVec)



[UInt32.neg]](#manual-UInt32___neg) (a : [UInt32]](#manual-UInt32___ofBitVec)) : [UInt32]](#manual-UInt32___ofBitVec)
```

Negation of 32-bit unsigned integers, computed modulo `[UInt32.size]](#manual-UInt32___size)`.

`[UInt32.neg]](#manual-UInt32___neg) a` is equivalent to `429_4967_295 - a + 1`.

This function is overridden at runtime with an efficient implementation.

def

```lean
[UInt64.neg]](#manual-UInt64___neg) (a : [UInt64]](#manual-UInt64___ofBitVec)) : [UInt64]](#manual-UInt64___ofBitVec)



[UInt64.neg]](#manual-UInt64___neg) (a : [UInt64]](#manual-UInt64___ofBitVec)) : [UInt64]](#manual-UInt64___ofBitVec)
```

Negation of 64-bit unsigned integers, computed modulo `[UInt64.size]](#manual-UInt64___size)`.

`[UInt64.neg]](#manual-UInt64___neg) a` is equivalent to `18_446_744_073_709_551_615 - a + 1`.

This function is overridden at runtime with an efficient implementation.

def

```lean
[USize.add]](#manual-USize___add) (a b : [USize]](#manual-USize___ofBitVec)) : [USize]](#manual-USize___ofBitVec)



[USize.add]](#manual-USize___add) (a b : [USize]](#manual-USize___ofBitVec)) : [USize]](#manual-USize___ofBitVec)
```

Adds two word-sized unsigned integers, wrapping around on overflow. Usually accessed via the `+`
operator.

This function is overridden at runtime with an efficient implementation.

def

```lean
[ISize.add]](#manual-ISize___add) (a b : [ISize]](#manual-ISize___ofUSize)) : [ISize]](#manual-ISize___ofUSize)



[ISize.add]](#manual-ISize___add) (a b : [ISize]](#manual-ISize___ofUSize)) : [ISize]](#manual-ISize___ofUSize)
```

Adds two word-sized signed integers, wrapping around on over- or underflow. Usually accessed via
the `+` operator.

This function is overridden at runtime with an efficient implementation.

def

```lean
[UInt8.add]](#manual-UInt8___add) (a b : [UInt8]](#manual-UInt8___ofBitVec)) : [UInt8]](#manual-UInt8___ofBitVec)



[UInt8.add]](#manual-UInt8___add) (a b : [UInt8]](#manual-UInt8___ofBitVec)) : [UInt8]](#manual-UInt8___ofBitVec)
```

Adds two 8-bit unsigned integers, wrapping around on overflow. Usually accessed via the `+`
operator.

This function is overridden at runtime with an efficient implementation.

def

```lean
[Int8.add]](#manual-Int8___add) (a b : [Int8]](#manual-Int8___ofUInt8)) : [Int8]](#manual-Int8___ofUInt8)



[Int8.add]](#manual-Int8___add) (a b : [Int8]](#manual-Int8___ofUInt8)) : [Int8]](#manual-Int8___ofUInt8)
```

Adds two 8-bit signed integers, wrapping around on over- or underflow. Usually accessed via the `+`
operator.

This function is overridden at runtime with an efficient implementation.

def

```lean
[UInt16.add]](#manual-UInt16___add) (a b : [UInt16]](#manual-UInt16___ofBitVec)) : [UInt16]](#manual-UInt16___ofBitVec)



[UInt16.add]](#manual-UInt16___add) (a b : [UInt16]](#manual-UInt16___ofBitVec)) : [UInt16]](#manual-UInt16___ofBitVec)
```

Adds two 16-bit unsigned integers, wrapping around on overflow. Usually accessed via the `+`
operator.

This function is overridden at runtime with an efficient implementation.

def

```lean
[Int16.add]](#manual-Int16___add) (a b : [Int16]](#manual-Int16___ofUInt16)) : [Int16]](#manual-Int16___ofUInt16)



[Int16.add]](#manual-Int16___add) (a b : [Int16]](#manual-Int16___ofUInt16)) : [Int16]](#manual-Int16___ofUInt16)
```

Adds two 16-bit signed integers, wrapping around on over- or underflow. Usually accessed via the `+`
operator.

This function is overridden at runtime with an efficient implementation.

def

```lean
[UInt32.add]](#manual-UInt32___add) (a b : [UInt32]](#manual-UInt32___ofBitVec)) : [UInt32]](#manual-UInt32___ofBitVec)



[UInt32.add]](#manual-UInt32___add) (a b : [UInt32]](#manual-UInt32___ofBitVec)) : [UInt32]](#manual-UInt32___ofBitVec)
```

Adds two 32-bit unsigned integers, wrapping around on overflow. Usually accessed via the `+`
operator.

This function is overridden at runtime with an efficient implementation.

def

```lean
[Int32.add]](#manual-Int32___add) (a b : [Int32]](#manual-Int32___ofUInt32)) : [Int32]](#manual-Int32___ofUInt32)



[Int32.add]](#manual-Int32___add) (a b : [Int32]](#manual-Int32___ofUInt32)) : [Int32]](#manual-Int32___ofUInt32)
```

Adds two 32-bit signed integers, wrapping around on over- or underflow. Usually accessed via the
`+` operator.

This function is overridden at runtime with an efficient implementation.

def

```lean
[UInt64.add]](#manual-UInt64___add) (a b : [UInt64]](#manual-UInt64___ofBitVec)) : [UInt64]](#manual-UInt64___ofBitVec)



[UInt64.add]](#manual-UInt64___add) (a b : [UInt64]](#manual-UInt64___ofBitVec)) : [UInt64]](#manual-UInt64___ofBitVec)
```

Adds two 64-bit unsigned integers, wrapping around on overflow. Usually accessed via the `+`
operator.

This function is overridden at runtime with an efficient implementation.

def

```lean
[Int64.add]](#manual-Int64___add) (a b : [Int64]](#manual-Int64___ofUInt64)) : [Int64]](#manual-Int64___ofUInt64)



[Int64.add]](#manual-Int64___add) (a b : [Int64]](#manual-Int64___ofUInt64)) : [Int64]](#manual-Int64___ofUInt64)
```

Adds two 64-bit signed integers, wrapping around on over- or underflow. Usually accessed via the
`+` operator.

This function is overridden at runtime with an efficient implementation.

def

```lean
[USize.sub]](#manual-USize___sub) (a b : [USize]](#manual-USize___ofBitVec)) : [USize]](#manual-USize___ofBitVec)



[USize.sub]](#manual-USize___sub) (a b : [USize]](#manual-USize___ofBitVec)) : [USize]](#manual-USize___ofBitVec)
```

Subtracts one word-sized-bit unsigned integer from another, wrapping around on underflow. Usually
accessed via the `-` operator.

This function is overridden at runtime with an efficient implementation.

def

```lean
[ISize.sub]](#manual-ISize___sub) (a b : [ISize]](#manual-ISize___ofUSize)) : [ISize]](#manual-ISize___ofUSize)



[ISize.sub]](#manual-ISize___sub) (a b : [ISize]](#manual-ISize___ofUSize)) : [ISize]](#manual-ISize___ofUSize)
```

Subtracts one word-sized signed integer from another, wrapping around on over- or underflow. Usually
accessed via the `-` operator.

This function is overridden at runtime with an efficient implementation.

def

```lean
[UInt8.sub]](#manual-UInt8___sub) (a b : [UInt8]](#manual-UInt8___ofBitVec)) : [UInt8]](#manual-UInt8___ofBitVec)



[UInt8.sub]](#manual-UInt8___sub) (a b : [UInt8]](#manual-UInt8___ofBitVec)) : [UInt8]](#manual-UInt8___ofBitVec)
```

Subtracts one 8-bit unsigned integer from another, wrapping around on underflow. Usually accessed
via the `-` operator.

This function is overridden at runtime with an efficient implementation.

def

```lean
[Int8.sub]](#manual-Int8___sub) (a b : [Int8]](#manual-Int8___ofUInt8)) : [Int8]](#manual-Int8___ofUInt8)



[Int8.sub]](#manual-Int8___sub) (a b : [Int8]](#manual-Int8___ofUInt8)) : [Int8]](#manual-Int8___ofUInt8)
```

Subtracts one 8-bit signed integer from another, wrapping around on over- or underflow. Usually
accessed via the `-` operator.

This function is overridden at runtime with an efficient implementation.

def

```lean
[UInt16.sub]](#manual-UInt16___sub) (a b : [UInt16]](#manual-UInt16___ofBitVec)) : [UInt16]](#manual-UInt16___ofBitVec)



[UInt16.sub]](#manual-UInt16___sub) (a b : [UInt16]](#manual-UInt16___ofBitVec)) : [UInt16]](#manual-UInt16___ofBitVec)
```

Subtracts one 16-bit unsigned integer from another, wrapping around on underflow. Usually accessed
via the `-` operator.

This function is overridden at runtime with an efficient implementation.

def

```lean
[Int16.sub]](#manual-Int16___sub) (a b : [Int16]](#manual-Int16___ofUInt16)) : [Int16]](#manual-Int16___ofUInt16)



[Int16.sub]](#manual-Int16___sub) (a b : [Int16]](#manual-Int16___ofUInt16)) : [Int16]](#manual-Int16___ofUInt16)
```

Subtracts one 16-bit signed integer from another, wrapping around on over- or underflow. Usually
accessed via the `-` operator.

This function is overridden at runtime with an efficient implementation.

def

```lean
[UInt32.sub]](#manual-UInt32___sub) (a b : [UInt32]](#manual-UInt32___ofBitVec)) : [UInt32]](#manual-UInt32___ofBitVec)



[UInt32.sub]](#manual-UInt32___sub) (a b : [UInt32]](#manual-UInt32___ofBitVec)) : [UInt32]](#manual-UInt32___ofBitVec)
```

Subtracts one 32-bit unsigned integer from another, wrapping around on underflow. Usually accessed
via the `-` operator.

This function is overridden at runtime with an efficient implementation.

def

```lean
[Int32.sub]](#manual-Int32___sub) (a b : [Int32]](#manual-Int32___ofUInt32)) : [Int32]](#manual-Int32___ofUInt32)



[Int32.sub]](#manual-Int32___sub) (a b : [Int32]](#manual-Int32___ofUInt32)) : [Int32]](#manual-Int32___ofUInt32)
```

Subtracts one 32-bit signed integer from another, wrapping around on over- or underflow. Usually
accessed via the `-` operator.

This function is overridden at runtime with an efficient implementation.

def

```lean
[UInt64.sub]](#manual-UInt64___sub) (a b : [UInt64]](#manual-UInt64___ofBitVec)) : [UInt64]](#manual-UInt64___ofBitVec)



[UInt64.sub]](#manual-UInt64___sub) (a b : [UInt64]](#manual-UInt64___ofBitVec)) : [UInt64]](#manual-UInt64___ofBitVec)
```

Subtracts one 64-bit unsigned integer from another, wrapping around on underflow. Usually accessed
via the `-` operator.

This function is overridden at runtime with an efficient implementation.

def

```lean
[Int64.sub]](#manual-Int64___sub) (a b : [Int64]](#manual-Int64___ofUInt64)) : [Int64]](#manual-Int64___ofUInt64)



[Int64.sub]](#manual-Int64___sub) (a b : [Int64]](#manual-Int64___ofUInt64)) : [Int64]](#manual-Int64___ofUInt64)
```

Subtracts one 64-bit signed integer from another, wrapping around on over- or underflow. Usually
accessed via the `-` operator.

This function is overridden at runtime with an efficient implementation.

def

```lean
[USize.mul]](#manual-USize___mul) (a b : [USize]](#manual-USize___ofBitVec)) : [USize]](#manual-USize___ofBitVec)



[USize.mul]](#manual-USize___mul) (a b : [USize]](#manual-USize___ofBitVec)) : [USize]](#manual-USize___ofBitVec)
```

Multiplies two word-sized unsigned integers, wrapping around on overflow. Usually accessed via the
`*` operator.

This function is overridden at runtime with an efficient implementation.

def

```lean
[ISize.mul]](#manual-ISize___mul) (a b : [ISize]](#manual-ISize___ofUSize)) : [ISize]](#manual-ISize___ofUSize)



[ISize.mul]](#manual-ISize___mul) (a b : [ISize]](#manual-ISize___ofUSize)) : [ISize]](#manual-ISize___ofUSize)
```

Multiplies two word-sized signed integers, wrapping around on over- or underflow. Usually accessed
via the `*` operator.

This function is overridden at runtime with an efficient implementation.

def

```lean
[UInt8.mul]](#manual-UInt8___mul) (a b : [UInt8]](#manual-UInt8___ofBitVec)) : [UInt8]](#manual-UInt8___ofBitVec)



[UInt8.mul]](#manual-UInt8___mul) (a b : [UInt8]](#manual-UInt8___ofBitVec)) : [UInt8]](#manual-UInt8___ofBitVec)
```

Multiplies two 8-bit unsigned integers, wrapping around on overflow. Usually accessed via the `*`
operator.

This function is overridden at runtime with an efficient implementation.

def

```lean
[Int8.mul]](#manual-Int8___mul) (a b : [Int8]](#manual-Int8___ofUInt8)) : [Int8]](#manual-Int8___ofUInt8)



[Int8.mul]](#manual-Int8___mul) (a b : [Int8]](#manual-Int8___ofUInt8)) : [Int8]](#manual-Int8___ofUInt8)
```

Multiplies two 8-bit signed integers, wrapping around on over- or underflow. Usually accessed via
the `*` operator.

This function is overridden at runtime with an efficient implementation.

def

```lean
[UInt16.mul]](#manual-UInt16___mul) (a b : [UInt16]](#manual-UInt16___ofBitVec)) : [UInt16]](#manual-UInt16___ofBitVec)



[UInt16.mul]](#manual-UInt16___mul) (a b : [UInt16]](#manual-UInt16___ofBitVec)) : [UInt16]](#manual-UInt16___ofBitVec)
```

Multiplies two 16-bit unsigned integers, wrapping around on overflow. Usually accessed via the `*`
operator.

This function is overridden at runtime with an efficient implementation.

def

```lean
[Int16.mul]](#manual-Int16___mul) (a b : [Int16]](#manual-Int16___ofUInt16)) : [Int16]](#manual-Int16___ofUInt16)



[Int16.mul]](#manual-Int16___mul) (a b : [Int16]](#manual-Int16___ofUInt16)) : [Int16]](#manual-Int16___ofUInt16)
```

Multiplies two 16-bit signed integers, wrapping around on over- or underflow. Usually accessed via
the `*` operator.

This function is overridden at runtime with an efficient implementation.

def

```lean
[UInt32.mul]](#manual-UInt32___mul) (a b : [UInt32]](#manual-UInt32___ofBitVec)) : [UInt32]](#manual-UInt32___ofBitVec)



[UInt32.mul]](#manual-UInt32___mul) (a b : [UInt32]](#manual-UInt32___ofBitVec)) : [UInt32]](#manual-UInt32___ofBitVec)
```

Multiplies two 32-bit unsigned integers, wrapping around on overflow. Usually accessed via the `*`
operator.

This function is overridden at runtime with an efficient implementation.

def

```lean
[Int32.mul]](#manual-Int32___mul) (a b : [Int32]](#manual-Int32___ofUInt32)) : [Int32]](#manual-Int32___ofUInt32)



[Int32.mul]](#manual-Int32___mul) (a b : [Int32]](#manual-Int32___ofUInt32)) : [Int32]](#manual-Int32___ofUInt32)
```

Multiplies two 32-bit signed integers, wrapping around on over- or underflow. Usually accessed via
the `*` operator.

This function is overridden at runtime with an efficient implementation.

def

```lean
[UInt64.mul]](#manual-UInt64___mul) (a b : [UInt64]](#manual-UInt64___ofBitVec)) : [UInt64]](#manual-UInt64___ofBitVec)



[UInt64.mul]](#manual-UInt64___mul) (a b : [UInt64]](#manual-UInt64___ofBitVec)) : [UInt64]](#manual-UInt64___ofBitVec)
```

Multiplies two 64-bit unsigned integers, wrapping around on overflow. Usually accessed via the `*`
operator.

This function is overridden at runtime with an efficient implementation.

def

```lean
[Int64.mul]](#manual-Int64___mul) (a b : [Int64]](#manual-Int64___ofUInt64)) : [Int64]](#manual-Int64___ofUInt64)



[Int64.mul]](#manual-Int64___mul) (a b : [Int64]](#manual-Int64___ofUInt64)) : [Int64]](#manual-Int64___ofUInt64)
```

Multiplies two 64-bit signed integers, wrapping around on over- or underflow. Usually accessed via
the `*` operator.

This function is overridden at runtime with an efficient implementation.

def

```lean
[USize.div]](#manual-USize___div) (a b : [USize]](#manual-USize___ofBitVec)) : [USize]](#manual-USize___ofBitVec)



[USize.div]](#manual-USize___div) (a b : [USize]](#manual-USize___ofBitVec)) : [USize]](#manual-USize___ofBitVec)
```

Unsigned division for word-sized unsigned integers, discarding the remainder. Usually accessed
via the `/` operator.

This operation is sometimes called “floor division.” Division by zero is defined to be zero.

This function is overridden at runtime with an efficient implementation.

def

```lean
[ISize.div]](#manual-ISize___div) (a b : [ISize]](#manual-ISize___ofUSize)) : [ISize]](#manual-ISize___ofUSize)



[ISize.div]](#manual-ISize___div) (a b : [ISize]](#manual-ISize___ofUSize)) : [ISize]](#manual-ISize___ofUSize)
```

Truncating division for word-sized signed integers, rounding towards zero. Usually accessed via the
`/` operator.

Division by zero is defined to be zero.

This function is overridden at runtime with an efficient implementation.

Examples:

- `[ISize.div]](#manual-ISize___div) 10 3 = 3`
- `[ISize.div]](#manual-ISize___div) 10 (-3) = (-3)`
- `[ISize.div]](#manual-ISize___div) (-10) (-3) = 3`
- `[ISize.div]](#manual-ISize___div) (-10) 3 = (-3)`
- `[ISize.div]](#manual-ISize___div) 10 0 = 0`

def

```lean
[UInt8.div]](#manual-UInt8___div) (a b : [UInt8]](#manual-UInt8___ofBitVec)) : [UInt8]](#manual-UInt8___ofBitVec)



[UInt8.div]](#manual-UInt8___div) (a b : [UInt8]](#manual-UInt8___ofBitVec)) : [UInt8]](#manual-UInt8___ofBitVec)
```

Unsigned division for 8-bit unsigned integers, discarding the remainder. Usually accessed
via the `/` operator.

This operation is sometimes called “floor division.” Division by zero is defined to be zero.

This function is overridden at runtime with an efficient implementation.

def

```lean
[Int8.div]](#manual-Int8___div) (a b : [Int8]](#manual-Int8___ofUInt8)) : [Int8]](#manual-Int8___ofUInt8)



[Int8.div]](#manual-Int8___div) (a b : [Int8]](#manual-Int8___ofUInt8)) : [Int8]](#manual-Int8___ofUInt8)
```

Truncating division for 8-bit signed integers, rounding towards zero. Usually accessed via the `/`
operator.

Division by zero is defined to be zero.

This function is overridden at runtime with an efficient implementation.

Examples:

- `[Int8.div]](#manual-Int8___div) 10 3 = 3`
- `[Int8.div]](#manual-Int8___div) 10 (-3) = (-3)`
- `[Int8.div]](#manual-Int8___div) (-10) (-3) = 3`
- `[Int8.div]](#manual-Int8___div) (-10) 3 = (-3)`
- `[Int8.div]](#manual-Int8___div) 10 0 = 0`

def

```lean
[UInt16.div]](#manual-UInt16___div) (a b : [UInt16]](#manual-UInt16___ofBitVec)) : [UInt16]](#manual-UInt16___ofBitVec)



[UInt16.div]](#manual-UInt16___div) (a b : [UInt16]](#manual-UInt16___ofBitVec)) : [UInt16]](#manual-UInt16___ofBitVec)
```

Unsigned division for 16-bit unsigned integers, discarding the remainder. Usually accessed
via the `/` operator.

This operation is sometimes called “floor division.” Division by zero is defined to be zero.

This function is overridden at runtime with an efficient implementation.

def

```lean
[Int16.div]](#manual-Int16___div) (a b : [Int16]](#manual-Int16___ofUInt16)) : [Int16]](#manual-Int16___ofUInt16)



[Int16.div]](#manual-Int16___div) (a b : [Int16]](#manual-Int16___ofUInt16)) : [Int16]](#manual-Int16___ofUInt16)
```

Truncating division for 16-bit signed integers, rounding towards zero. Usually accessed via the `/`
operator.

Division by zero is defined to be zero.

This function is overridden at runtime with an efficient implementation.

Examples:

- `[Int16.div]](#manual-Int16___div) 10 3 = 3`
- `[Int16.div]](#manual-Int16___div) 10 (-3) = (-3)`
- `[Int16.div]](#manual-Int16___div) (-10) (-3) = 3`
- `[Int16.div]](#manual-Int16___div) (-10) 3 = (-3)`
- `[Int16.div]](#manual-Int16___div) 10 0 = 0`

def

```lean
[UInt32.div]](#manual-UInt32___div) (a b : [UInt32]](#manual-UInt32___ofBitVec)) : [UInt32]](#manual-UInt32___ofBitVec)



[UInt32.div]](#manual-UInt32___div) (a b : [UInt32]](#manual-UInt32___ofBitVec)) : [UInt32]](#manual-UInt32___ofBitVec)
```

Unsigned division for 32-bit unsigned integers, discarding the remainder. Usually accessed
via the `/` operator.

This operation is sometimes called “floor division.” Division by zero is defined to be zero.

This function is overridden at runtime with an efficient implementation.

def

```lean
[Int32.div]](#manual-Int32___div) (a b : [Int32]](#manual-Int32___ofUInt32)) : [Int32]](#manual-Int32___ofUInt32)



[Int32.div]](#manual-Int32___div) (a b : [Int32]](#manual-Int32___ofUInt32)) : [Int32]](#manual-Int32___ofUInt32)
```

Truncating division for 32-bit signed integers, rounding towards zero. Usually accessed via the `/`
operator.

Division by zero is defined to be zero.

This function is overridden at runtime with an efficient implementation.

Examples:

- `[Int32.div]](#manual-Int32___div) 10 3 = 3`
- `[Int32.div]](#manual-Int32___div) 10 (-3) = (-3)`
- `[Int32.div]](#manual-Int32___div) (-10) (-3) = 3`
- `[Int32.div]](#manual-Int32___div) (-10) 3 = (-3)`
- `[Int32.div]](#manual-Int32___div) 10 0 = 0`

def

```lean
[UInt64.div]](#manual-UInt64___div) (a b : [UInt64]](#manual-UInt64___ofBitVec)) : [UInt64]](#manual-UInt64___ofBitVec)



[UInt64.div]](#manual-UInt64___div) (a b : [UInt64]](#manual-UInt64___ofBitVec)) : [UInt64]](#manual-UInt64___ofBitVec)
```

Unsigned division for 64-bit unsigned integers, discarding the remainder. Usually accessed
via the `/` operator.

This operation is sometimes called “floor division.” Division by zero is defined to be zero.

This function is overridden at runtime with an efficient implementation.

def

```lean
[Int64.div]](#manual-Int64___div) (a b : [Int64]](#manual-Int64___ofUInt64)) : [Int64]](#manual-Int64___ofUInt64)



[Int64.div]](#manual-Int64___div) (a b : [Int64]](#manual-Int64___ofUInt64)) : [Int64]](#manual-Int64___ofUInt64)
```

Truncating division for 64-bit signed integers, rounding towards zero. Usually accessed via the `/`
operator.

Division by zero is defined to be zero.

This function is overridden at runtime with an efficient implementation.

Examples:

- `[Int64.div]](#manual-Int64___div) 10 3 = 3`
- `[Int64.div]](#manual-Int64___div) 10 (-3) = (-3)`
- `[Int64.div]](#manual-Int64___div) (-10) (-3) = 3`
- `[Int64.div]](#manual-Int64___div) (-10) 3 = (-3)`
- `[Int64.div]](#manual-Int64___div) 10 0 = 0`

def

```lean
[USize.mod]](#manual-USize___mod) (a b : [USize]](#manual-USize___ofBitVec)) : [USize]](#manual-USize___ofBitVec)



[USize.mod]](#manual-USize___mod) (a b : [USize]](#manual-USize___ofBitVec)) : [USize]](#manual-USize___ofBitVec)
```

The modulo operator for word-sized unsigned integers, which computes the remainder when dividing one
integer by another. Usually accessed via the `%` operator.

When the divisor is `0`, the result is the dividend rather than an error.

This function is overridden at runtime with an efficient implementation.

Examples:

- `[USize.mod]](#manual-USize___mod) 5 2 = 1`
- `[USize.mod]](#manual-USize___mod) 4 2 = 0`
- `[USize.mod]](#manual-USize___mod) 4 0 = 4`

def

```lean
[ISize.mod]](#manual-ISize___mod) (a b : [ISize]](#manual-ISize___ofUSize)) : [ISize]](#manual-ISize___ofUSize)



[ISize.mod]](#manual-ISize___mod) (a b : [ISize]](#manual-ISize___ofUSize)) : [ISize]](#manual-ISize___ofUSize)
```

The modulo operator for word-sized signed integers, which computes the remainder when dividing one
integer by another with the T-rounding convention used by `[ISize.div]](#manual-ISize___div)`. Usually accessed via the `%`
operator.

When the divisor is `0`, the result is the dividend rather than an error.

This function is overridden at runtime with an efficient implementation.

Examples:

- `[ISize.mod]](#manual-ISize___mod) 5 2 = 1`
- `[ISize.mod]](#manual-ISize___mod) 5 (-2) = 1`
- `[ISize.mod]](#manual-ISize___mod) (-5) 2 = (-1)`
- `[ISize.mod]](#manual-ISize___mod) (-5) (-2) = (-1)`
- `[ISize.mod]](#manual-ISize___mod) 4 2 = 0`
- `[ISize.mod]](#manual-ISize___mod) 4 (-2) = 0`
- `[ISize.mod]](#manual-ISize___mod) 4 0 = 4`
- `[ISize.mod]](#manual-ISize___mod) (-4) 0 = (-4)`

def

```lean
[UInt8.mod]](#manual-UInt8___mod) (a b : [UInt8]](#manual-UInt8___ofBitVec)) : [UInt8]](#manual-UInt8___ofBitVec)



[UInt8.mod]](#manual-UInt8___mod) (a b : [UInt8]](#manual-UInt8___ofBitVec)) : [UInt8]](#manual-UInt8___ofBitVec)
```

The modulo operator for 8-bit unsigned integers, which computes the remainder when dividing one
integer by another. Usually accessed via the `%` operator.

When the divisor is `0`, the result is the dividend rather than an error.

This function is overridden at runtime with an efficient implementation.

Examples:

- `[UInt8.mod]](#manual-UInt8___mod) 5 2 = 1`
- `[UInt8.mod]](#manual-UInt8___mod) 4 2 = 0`
- `[UInt8.mod]](#manual-UInt8___mod) 4 0 = 4`

def

```lean
[Int8.mod]](#manual-Int8___mod) (a b : [Int8]](#manual-Int8___ofUInt8)) : [Int8]](#manual-Int8___ofUInt8)



[Int8.mod]](#manual-Int8___mod) (a b : [Int8]](#manual-Int8___ofUInt8)) : [Int8]](#manual-Int8___ofUInt8)
```

The modulo operator for 8-bit signed integers, which computes the remainder when dividing one
integer by another with the T-rounding convention used by `[Int8.div]](#manual-Int8___div)`. Usually accessed via the `%`
operator.

When the divisor is `0`, the result is the dividend rather than an error.

This function is overridden at runtime with an efficient implementation.

Examples:

- `[Int8.mod]](#manual-Int8___mod) 5 2 = 1`
- `[Int8.mod]](#manual-Int8___mod) 5 (-2) = 1`
- `[Int8.mod]](#manual-Int8___mod) (-5) 2 = (-1)`
- `[Int8.mod]](#manual-Int8___mod) (-5) (-2) = (-1)`
- `[Int8.mod]](#manual-Int8___mod) 4 2 = 0`
- `[Int8.mod]](#manual-Int8___mod) 4 (-2) = 0`
- `[Int8.mod]](#manual-Int8___mod) 4 0 = 4`
- `[Int8.mod]](#manual-Int8___mod) (-4) 0 = (-4)`

def

```lean
[UInt16.mod]](#manual-UInt16___mod) (a b : [UInt16]](#manual-UInt16___ofBitVec)) : [UInt16]](#manual-UInt16___ofBitVec)



[UInt16.mod]](#manual-UInt16___mod) (a b : [UInt16]](#manual-UInt16___ofBitVec)) : [UInt16]](#manual-UInt16___ofBitVec)
```

The modulo operator for 16-bit unsigned integers, which computes the remainder when dividing one
integer by another. Usually accessed via the `%` operator.

When the divisor is `0`, the result is the dividend rather than an error.

This function is overridden at runtime with an efficient implementation.

Examples:

- `[UInt16.mod]](#manual-UInt16___mod) 5 2 = 1`
- `[UInt16.mod]](#manual-UInt16___mod) 4 2 = 0`
- `[UInt16.mod]](#manual-UInt16___mod) 4 0 = 4`

def

```lean
[Int16.mod]](#manual-Int16___mod) (a b : [Int16]](#manual-Int16___ofUInt16)) : [Int16]](#manual-Int16___ofUInt16)



[Int16.mod]](#manual-Int16___mod) (a b : [Int16]](#manual-Int16___ofUInt16)) : [Int16]](#manual-Int16___ofUInt16)
```

The modulo operator for 16-bit signed integers, which computes the remainder when dividing one
integer by another with the T-rounding convention used by `[Int16.div]](#manual-Int16___div)`. Usually accessed via the `%`
operator.

When the divisor is `0`, the result is the dividend rather than an error.

This function is overridden at runtime with an efficient implementation.

Examples:

- `[Int16.mod]](#manual-Int16___mod) 5 2 = 1`
- `[Int16.mod]](#manual-Int16___mod) 5 (-2) = 1`
- `[Int16.mod]](#manual-Int16___mod) (-5) 2 = (-1)`
- `[Int16.mod]](#manual-Int16___mod) (-5) (-2) = (-1)`
- `[Int16.mod]](#manual-Int16___mod) 4 2 = 0`
- `[Int16.mod]](#manual-Int16___mod) 4 (-2) = 0`
- `[Int16.mod]](#manual-Int16___mod) 4 0 = 4`
- `[Int16.mod]](#manual-Int16___mod) (-4) 0 = (-4)`

def

```lean
[UInt32.mod]](#manual-UInt32___mod) (a b : [UInt32]](#manual-UInt32___ofBitVec)) : [UInt32]](#manual-UInt32___ofBitVec)



[UInt32.mod]](#manual-UInt32___mod) (a b : [UInt32]](#manual-UInt32___ofBitVec)) : [UInt32]](#manual-UInt32___ofBitVec)
```

The modulo operator for 32-bit unsigned integers, which computes the remainder when dividing one
integer by another. Usually accessed via the `%` operator.

When the divisor is `0`, the result is the dividend rather than an error.

This function is overridden at runtime with an efficient implementation.

Examples:

- `[UInt32.mod]](#manual-UInt32___mod) 5 2 = 1`
- `[UInt32.mod]](#manual-UInt32___mod) 4 2 = 0`
- `[UInt32.mod]](#manual-UInt32___mod) 4 0 = 4`

def

```lean
[Int32.mod]](#manual-Int32___mod) (a b : [Int32]](#manual-Int32___ofUInt32)) : [Int32]](#manual-Int32___ofUInt32)



[Int32.mod]](#manual-Int32___mod) (a b : [Int32]](#manual-Int32___ofUInt32)) : [Int32]](#manual-Int32___ofUInt32)
```

The modulo operator for 32-bit signed integers, which computes the remainder when dividing one
integer by another with the T-rounding convention used by `[Int32.div]](#manual-Int32___div)`. Usually accessed via the `%`
operator.

When the divisor is `0`, the result is the dividend rather than an error.

This function is overridden at runtime with an efficient implementation.

Examples:

- `[Int32.mod]](#manual-Int32___mod) 5 2 = 1`
- `[Int32.mod]](#manual-Int32___mod) 5 (-2) = 1`
- `[Int32.mod]](#manual-Int32___mod) (-5) 2 = (-1)`
- `[Int32.mod]](#manual-Int32___mod) (-5) (-2) = (-1)`
- `[Int32.mod]](#manual-Int32___mod) 4 2 = 0`
- `[Int32.mod]](#manual-Int32___mod) 4 (-2) = 0`
- `[Int32.mod]](#manual-Int32___mod) 4 0 = 4`
- `[Int32.mod]](#manual-Int32___mod) (-4) 0 = (-4)`

def

```lean
[UInt64.mod]](#manual-UInt64___mod) (a b : [UInt64]](#manual-UInt64___ofBitVec)) : [UInt64]](#manual-UInt64___ofBitVec)



[UInt64.mod]](#manual-UInt64___mod) (a b : [UInt64]](#manual-UInt64___ofBitVec)) : [UInt64]](#manual-UInt64___ofBitVec)
```

The modulo operator for 64-bit unsigned integers, which computes the remainder when dividing one
integer by another. Usually accessed via the `%` operator.

When the divisor is `0`, the result is the dividend rather than an error.

This function is overridden at runtime with an efficient implementation.

Examples:

- `[UInt64.mod]](#manual-UInt64___mod) 5 2 = 1`
- `[UInt64.mod]](#manual-UInt64___mod) 4 2 = 0`
- `[UInt64.mod]](#manual-UInt64___mod) 4 0 = 4`

def

```lean
[Int64.mod]](#manual-Int64___mod) (a b : [Int64]](#manual-Int64___ofUInt64)) : [Int64]](#manual-Int64___ofUInt64)



[Int64.mod]](#manual-Int64___mod) (a b : [Int64]](#manual-Int64___ofUInt64)) : [Int64]](#manual-Int64___ofUInt64)
```

The modulo operator for 64-bit signed integers, which computes the remainder when dividing one
integer by another with the T-rounding convention used by `[Int64.div]](#manual-Int64___div)`. Usually accessed via the `%`
operator.

When the divisor is `0`, the result is the dividend rather than an error.

This function is overridden at runtime with an efficient implementation.

Examples:

- `[Int64.mod]](#manual-Int64___mod) 5 2 = 1`
- `[Int64.mod]](#manual-Int64___mod) 5 (-2) = 1`
- `[Int64.mod]](#manual-Int64___mod) (-5) 2 = (-1)`
- `[Int64.mod]](#manual-Int64___mod) (-5) (-2) = (-1)`
- `[Int64.mod]](#manual-Int64___mod) 4 2 = 0`
- `[Int64.mod]](#manual-Int64___mod) 4 (-2) = 0`
- `[Int64.mod]](#manual-Int64___mod) 4 0 = 4`
- `[Int64.mod]](#manual-Int64___mod) (-4) 0 = (-4)`

def

```lean
[USize.log2]](#manual-USize___log2) (a : [USize]](#manual-USize___ofBitVec)) : [USize]](#manual-USize___ofBitVec)



[USize.log2]](#manual-USize___log2) (a : [USize]](#manual-USize___ofBitVec)) : [USize]](#manual-USize___ofBitVec)
```

Base-two logarithm of word-sized unsigned integers. Returns `⌊max 0 (log₂ a)⌋`.

This function is overridden at runtime with an efficient implementation. This definition is
the logical model.

Examples:

- `[USize.log2]](#manual-USize___log2) 0 = 0`
- `[USize.log2]](#manual-USize___log2) 1 = 0`
- `[USize.log2]](#manual-USize___log2) 2 = 1`
- `[USize.log2]](#manual-USize___log2) 4 = 2`
- `[USize.log2]](#manual-USize___log2) 7 = 2`
- `[USize.log2]](#manual-USize___log2) 8 = 3`

def

```lean
[UInt8.log2]](#manual-UInt8___log2) (a : [UInt8]](#manual-UInt8___ofBitVec)) : [UInt8]](#manual-UInt8___ofBitVec)



[UInt8.log2]](#manual-UInt8___log2) (a : [UInt8]](#manual-UInt8___ofBitVec)) : [UInt8]](#manual-UInt8___ofBitVec)
```

Base-two logarithm of 8-bit unsigned integers. Returns `⌊max 0 (log₂ a)⌋`.

This function is overridden at runtime with an efficient implementation. This definition is
the logical model.

Examples:

- `[UInt8.log2]](#manual-UInt8___log2) 0 = 0`
- `[UInt8.log2]](#manual-UInt8___log2) 1 = 0`
- `[UInt8.log2]](#manual-UInt8___log2) 2 = 1`
- `[UInt8.log2]](#manual-UInt8___log2) 4 = 2`
- `[UInt8.log2]](#manual-UInt8___log2) 7 = 2`
- `[UInt8.log2]](#manual-UInt8___log2) 8 = 3`

def

```lean
[UInt16.log2]](#manual-UInt16___log2) (a : [UInt16]](#manual-UInt16___ofBitVec)) : [UInt16]](#manual-UInt16___ofBitVec)



[UInt16.log2]](#manual-UInt16___log2) (a : [UInt16]](#manual-UInt16___ofBitVec)) : [UInt16]](#manual-UInt16___ofBitVec)
```

Base-two logarithm of 16-bit unsigned integers. Returns `⌊max 0 (log₂ a)⌋`.

This function is overridden at runtime with an efficient implementation. This definition is
the logical model.

Examples:

- `[UInt16.log2]](#manual-UInt16___log2) 0 = 0`
- `[UInt16.log2]](#manual-UInt16___log2) 1 = 0`
- `[UInt16.log2]](#manual-UInt16___log2) 2 = 1`
- `[UInt16.log2]](#manual-UInt16___log2) 4 = 2`
- `[UInt16.log2]](#manual-UInt16___log2) 7 = 2`
- `[UInt16.log2]](#manual-UInt16___log2) 8 = 3`

def

```lean
[UInt32.log2]](#manual-UInt32___log2) (a : [UInt32]](#manual-UInt32___ofBitVec)) : [UInt32]](#manual-UInt32___ofBitVec)



[UInt32.log2]](#manual-UInt32___log2) (a : [UInt32]](#manual-UInt32___ofBitVec)) : [UInt32]](#manual-UInt32___ofBitVec)
```

Base-two logarithm of 32-bit unsigned integers. Returns `⌊max 0 (log₂ a)⌋`.

This function is overridden at runtime with an efficient implementation. This definition is
the logical model.

Examples:

- `[UInt32.log2]](#manual-UInt32___log2) 0 = 0`
- `[UInt32.log2]](#manual-UInt32___log2) 1 = 0`
- `[UInt32.log2]](#manual-UInt32___log2) 2 = 1`
- `[UInt32.log2]](#manual-UInt32___log2) 4 = 2`
- `[UInt32.log2]](#manual-UInt32___log2) 7 = 2`
- `[UInt32.log2]](#manual-UInt32___log2) 8 = 3`

def

```lean
[UInt64.log2]](#manual-UInt64___log2) (a : [UInt64]](#manual-UInt64___ofBitVec)) : [UInt64]](#manual-UInt64___ofBitVec)



[UInt64.log2]](#manual-UInt64___log2) (a : [UInt64]](#manual-UInt64___ofBitVec)) : [UInt64]](#manual-UInt64___ofBitVec)
```

Base-two logarithm of 64-bit unsigned integers. Returns `⌊max 0 (log₂ a)⌋`.

This function is overridden at runtime with an efficient implementation. This definition is
the logical model.

Examples:

- `[UInt64.log2]](#manual-UInt64___log2) 0 = 0`
- `[UInt64.log2]](#manual-UInt64___log2) 1 = 0`
- `[UInt64.log2]](#manual-UInt64___log2) 2 = 1`
- `[UInt64.log2]](#manual-UInt64___log2) 4 = 2`
- `[UInt64.log2]](#manual-UInt64___log2) 7 = 2`
- `[UInt64.log2]](#manual-UInt64___log2) 8 = 3`

def

```lean
[ISize.abs]](#manual-ISize___abs) (a : [ISize]](#manual-ISize___ofUSize)) : [ISize]](#manual-ISize___ofUSize)



[ISize.abs]](#manual-ISize___abs) (a : [ISize]](#manual-ISize___ofUSize)) : [ISize]](#manual-ISize___ofUSize)
```

Computes the absolute value of a word-sized signed integer.

This function is equivalent to `[if]](#manual-termIfThenElse) a < 0 [then]](#manual-termIfThenElse) -a [else]](#manual-termIfThenElse) a`, so in particular `[ISize.minValue]](#manual-ISize___minValue)` will be
mapped to `[ISize.minValue]](#manual-ISize___minValue)`.

This function is overridden at runtime with an efficient implementation.

def

```lean
[Int8.abs]](#manual-Int8___abs) (a : [Int8]](#manual-Int8___ofUInt8)) : [Int8]](#manual-Int8___ofUInt8)



[Int8.abs]](#manual-Int8___abs) (a : [Int8]](#manual-Int8___ofUInt8)) : [Int8]](#manual-Int8___ofUInt8)
```

Computes the absolute value of an 8-bit signed integer.

This function is equivalent to `[if]](#manual-termIfThenElse) a < 0 [then]](#manual-termIfThenElse) -a [else]](#manual-termIfThenElse) a`, so in particular `[Int8.minValue]](#manual-Int8___minValue)` will be
mapped to `[Int8.minValue]](#manual-Int8___minValue)`.

This function is overridden at runtime with an efficient implementation.

def

```lean
[Int16.abs]](#manual-Int16___abs) (a : [Int16]](#manual-Int16___ofUInt16)) : [Int16]](#manual-Int16___ofUInt16)



[Int16.abs]](#manual-Int16___abs) (a : [Int16]](#manual-Int16___ofUInt16)) : [Int16]](#manual-Int16___ofUInt16)
```

Computes the absolute value of a 16-bit signed integer.

This function is equivalent to `[if]](#manual-termIfThenElse) a < 0 [then]](#manual-termIfThenElse) -a [else]](#manual-termIfThenElse) a`, so in particular `[Int16.minValue]](#manual-Int16___minValue)` will be
mapped to `[Int16.minValue]](#manual-Int16___minValue)`.

This function is overridden at runtime with an efficient implementation.

def

```lean
[Int32.abs]](#manual-Int32___abs) (a : [Int32]](#manual-Int32___ofUInt32)) : [Int32]](#manual-Int32___ofUInt32)



[Int32.abs]](#manual-Int32___abs) (a : [Int32]](#manual-Int32___ofUInt32)) : [Int32]](#manual-Int32___ofUInt32)
```

Computes the absolute value of a 32-bit signed integer.

This function is equivalent to `[if]](#manual-termIfThenElse) a < 0 [then]](#manual-termIfThenElse) -a [else]](#manual-termIfThenElse) a`, so in particular `[Int32.minValue]](#manual-Int32___minValue)` will be
mapped to `[Int32.minValue]](#manual-Int32___minValue)`.

This function is overridden at runtime with an efficient implementation.

def

```lean
[Int64.abs]](#manual-Int64___abs) (a : [Int64]](#manual-Int64___ofUInt64)) : [Int64]](#manual-Int64___ofUInt64)



[Int64.abs]](#manual-Int64___abs) (a : [Int64]](#manual-Int64___ofUInt64)) : [Int64]](#manual-Int64___ofUInt64)
```

Computes the absolute value of a 64-bit signed integer.

This function is equivalent to `[if]](#manual-termIfThenElse) a < 0 [then]](#manual-termIfThenElse) -a [else]](#manual-termIfThenElse) a`, so in particular `[Int64.minValue]](#manual-Int64___minValue)` will be
mapped to `[Int64.minValue]](#manual-Int64___minValue)`.

This function is overridden at runtime with an efficient implementation.

#### 20.4.4.6. Bitwise Operations {#manual-The-Lean-Language-Reference--Basic-Types--Fixed-Precision-Integers--API-Reference--Bitwise-Operations}

Typically, bitwise operations on fixed-width integers should be accessed using Lean's overloaded operators, particularly their instances of `[ShiftLeft]](#manual-ShiftLeft___mk)`, `[ShiftRight]](#manual-ShiftRight___mk)`, `[AndOp]](#manual-AndOp___mk)`, `[OrOp]](#manual-OrOp___mk)`, and `[XorOp]](#manual-XorOp___mk)`.

def

```lean
[USize.land]](#manual-USize___land) (a b : [USize]](#manual-USize___ofBitVec)) : [USize]](#manual-USize___ofBitVec)



[USize.land]](#manual-USize___land) (a b : [USize]](#manual-USize___ofBitVec)) : [USize]](#manual-USize___ofBitVec)
```

Bitwise and for word-sized unsigned integers. Usually accessed via the `&&&` operator.

Each bit of the resulting integer is set if the corresponding bits of both input integers are set.

This function is overridden at runtime with an efficient implementation.

def

```lean
[ISize.land]](#manual-ISize___land) (a b : [ISize]](#manual-ISize___ofUSize)) : [ISize]](#manual-ISize___ofUSize)



[ISize.land]](#manual-ISize___land) (a b : [ISize]](#manual-ISize___ofUSize)) : [ISize]](#manual-ISize___ofUSize)
```

Bitwise and for word-sized signed integers. Usually accessed via the `&&&` operator.

Each bit of the resulting integer is set if the corresponding bits of both input integers are set,
according to the two's complement representation.

This function is overridden at runtime with an efficient implementation.

def

```lean
[UInt8.land]](#manual-UInt8___land) (a b : [UInt8]](#manual-UInt8___ofBitVec)) : [UInt8]](#manual-UInt8___ofBitVec)



[UInt8.land]](#manual-UInt8___land) (a b : [UInt8]](#manual-UInt8___ofBitVec)) : [UInt8]](#manual-UInt8___ofBitVec)
```

Bitwise and for 8-bit unsigned integers. Usually accessed via the `&&&` operator.

Each bit of the resulting integer is set if the corresponding bits of both input integers are set.

This function is overridden at runtime with an efficient implementation.

def

```lean
[Int8.land]](#manual-Int8___land) (a b : [Int8]](#manual-Int8___ofUInt8)) : [Int8]](#manual-Int8___ofUInt8)



[Int8.land]](#manual-Int8___land) (a b : [Int8]](#manual-Int8___ofUInt8)) : [Int8]](#manual-Int8___ofUInt8)
```

Bitwise and for 8-bit signed integers. Usually accessed via the `&&&` operator.

Each bit of the resulting integer is set if the corresponding bits of both input integers are set,
according to the two's complement representation.

This function is overridden at runtime with an efficient implementation.

def

```lean
[UInt16.land]](#manual-UInt16___land) (a b : [UInt16]](#manual-UInt16___ofBitVec)) : [UInt16]](#manual-UInt16___ofBitVec)



[UInt16.land]](#manual-UInt16___land) (a b : [UInt16]](#manual-UInt16___ofBitVec)) : [UInt16]](#manual-UInt16___ofBitVec)
```

Bitwise and for 16-bit unsigned integers. Usually accessed via the `&&&` operator.

Each bit of the resulting integer is set if the corresponding bits of both input integers are set.

This function is overridden at runtime with an efficient implementation.

def

```lean
[Int16.land]](#manual-Int16___land) (a b : [Int16]](#manual-Int16___ofUInt16)) : [Int16]](#manual-Int16___ofUInt16)



[Int16.land]](#manual-Int16___land) (a b : [Int16]](#manual-Int16___ofUInt16)) : [Int16]](#manual-Int16___ofUInt16)
```

Bitwise and for 16-bit signed integers. Usually accessed via the `&&&` operator.

Each bit of the resulting integer is set if the corresponding bits of both input integers are set,
according to the two's complement representation.

This function is overridden at runtime with an efficient implementation.

def

```lean
[UInt32.land]](#manual-UInt32___land) (a b : [UInt32]](#manual-UInt32___ofBitVec)) : [UInt32]](#manual-UInt32___ofBitVec)



[UInt32.land]](#manual-UInt32___land) (a b : [UInt32]](#manual-UInt32___ofBitVec)) : [UInt32]](#manual-UInt32___ofBitVec)
```

Bitwise and for 32-bit unsigned integers. Usually accessed via the `&&&` operator.

Each bit of the resulting integer is set if the corresponding bits of both input integers are set.

This function is overridden at runtime with an efficient implementation.

def

```lean
[Int32.land]](#manual-Int32___land) (a b : [Int32]](#manual-Int32___ofUInt32)) : [Int32]](#manual-Int32___ofUInt32)



[Int32.land]](#manual-Int32___land) (a b : [Int32]](#manual-Int32___ofUInt32)) : [Int32]](#manual-Int32___ofUInt32)
```

Bitwise and for 32-bit signed integers. Usually accessed via the `&&&` operator.

Each bit of the resulting integer is set if the corresponding bits of both input integers are set,
according to the two's complement representation.

This function is overridden at runtime with an efficient implementation.

def

```lean
[UInt64.land]](#manual-UInt64___land) (a b : [UInt64]](#manual-UInt64___ofBitVec)) : [UInt64]](#manual-UInt64___ofBitVec)



[UInt64.land]](#manual-UInt64___land) (a b : [UInt64]](#manual-UInt64___ofBitVec)) : [UInt64]](#manual-UInt64___ofBitVec)
```

Bitwise and for 64-bit unsigned integers. Usually accessed via the `&&&` operator.

Each bit of the resulting integer is set if the corresponding bits of both input integers are set.

This function is overridden at runtime with an efficient implementation.

def

```lean
[Int64.land]](#manual-Int64___land) (a b : [Int64]](#manual-Int64___ofUInt64)) : [Int64]](#manual-Int64___ofUInt64)



[Int64.land]](#manual-Int64___land) (a b : [Int64]](#manual-Int64___ofUInt64)) : [Int64]](#manual-Int64___ofUInt64)
```

Bitwise and for 64-bit signed integers. Usually accessed via the `&&&` operator.

Each bit of the resulting integer is set if the corresponding bits of both input integers are set,
according to the two's complement representation.

This function is overridden at runtime with an efficient implementation.

def

```lean
[USize.lor]](#manual-USize___lor) (a b : [USize]](#manual-USize___ofBitVec)) : [USize]](#manual-USize___ofBitVec)



[USize.lor]](#manual-USize___lor) (a b : [USize]](#manual-USize___ofBitVec)) : [USize]](#manual-USize___ofBitVec)
```

Bitwise or for word-sized unsigned integers. Usually accessed via the `|||` operator.

Each bit of the resulting integer is set if at least one of the corresponding bits of both input
integers are set.

This function is overridden at runtime with an efficient implementation.

def

```lean
[ISize.lor]](#manual-ISize___lor) (a b : [ISize]](#manual-ISize___ofUSize)) : [ISize]](#manual-ISize___ofUSize)



[ISize.lor]](#manual-ISize___lor) (a b : [ISize]](#manual-ISize___ofUSize)) : [ISize]](#manual-ISize___ofUSize)
```

Bitwise or for word-sized signed integers. Usually accessed via the `|||` operator.

Each bit of the resulting integer is set if at least one of the corresponding bits of the input
integers is set, according to the two's complement representation.

This function is overridden at runtime with an efficient implementation.

def

```lean
[UInt8.lor]](#manual-UInt8___lor) (a b : [UInt8]](#manual-UInt8___ofBitVec)) : [UInt8]](#manual-UInt8___ofBitVec)



[UInt8.lor]](#manual-UInt8___lor) (a b : [UInt8]](#manual-UInt8___ofBitVec)) : [UInt8]](#manual-UInt8___ofBitVec)
```

Bitwise or for 8-bit unsigned integers. Usually accessed via the `|||` operator.

Each bit of the resulting integer is set if at least one of the corresponding bits of both input
integers are set.

This function is overridden at runtime with an efficient implementation.

def

```lean
[Int8.lor]](#manual-Int8___lor) (a b : [Int8]](#manual-Int8___ofUInt8)) : [Int8]](#manual-Int8___ofUInt8)



[Int8.lor]](#manual-Int8___lor) (a b : [Int8]](#manual-Int8___ofUInt8)) : [Int8]](#manual-Int8___ofUInt8)
```

Bitwise or for 8-bit signed integers. Usually accessed via the `|||` operator.

Each bit of the resulting integer is set if at least one of the corresponding bits of the input
integers is set, according to the two's complement representation.

This function is overridden at runtime with an efficient implementation.

def

```lean
[UInt16.lor]](#manual-UInt16___lor) (a b : [UInt16]](#manual-UInt16___ofBitVec)) : [UInt16]](#manual-UInt16___ofBitVec)



[UInt16.lor]](#manual-UInt16___lor) (a b : [UInt16]](#manual-UInt16___ofBitVec)) : [UInt16]](#manual-UInt16___ofBitVec)
```

Bitwise or for 16-bit unsigned integers. Usually accessed via the `|||` operator.

Each bit of the resulting integer is set if at least one of the corresponding bits of both input
integers are set.

This function is overridden at runtime with an efficient implementation.

def

```lean
[Int16.lor]](#manual-Int16___lor) (a b : [Int16]](#manual-Int16___ofUInt16)) : [Int16]](#manual-Int16___ofUInt16)



[Int16.lor]](#manual-Int16___lor) (a b : [Int16]](#manual-Int16___ofUInt16)) : [Int16]](#manual-Int16___ofUInt16)
```

Bitwise or for 16-bit signed integers. Usually accessed via the `|||` operator.

Each bit of the resulting integer is set if at least one of the corresponding bits of the input
integers is set, according to the two's complement representation.

This function is overridden at runtime with an efficient implementation.

def

```lean
[UInt32.lor]](#manual-UInt32___lor) (a b : [UInt32]](#manual-UInt32___ofBitVec)) : [UInt32]](#manual-UInt32___ofBitVec)



[UInt32.lor]](#manual-UInt32___lor) (a b : [UInt32]](#manual-UInt32___ofBitVec)) : [UInt32]](#manual-UInt32___ofBitVec)
```

Bitwise or for 32-bit unsigned integers. Usually accessed via the `|||` operator.

Each bit of the resulting integer is set if at least one of the corresponding bits of both input
integers are set.

This function is overridden at runtime with an efficient implementation.

def

```lean
[Int32.lor]](#manual-Int32___lor) (a b : [Int32]](#manual-Int32___ofUInt32)) : [Int32]](#manual-Int32___ofUInt32)



[Int32.lor]](#manual-Int32___lor) (a b : [Int32]](#manual-Int32___ofUInt32)) : [Int32]](#manual-Int32___ofUInt32)
```

Bitwise or for 32-bit signed integers. Usually accessed via the `|||` operator.

Each bit of the resulting integer is set if at least one of the corresponding bits of the input
integers is set, according to the two's complement representation.

This function is overridden at runtime with an efficient implementation.

def

```lean
[UInt64.lor]](#manual-UInt64___lor) (a b : [UInt64]](#manual-UInt64___ofBitVec)) : [UInt64]](#manual-UInt64___ofBitVec)



[UInt64.lor]](#manual-UInt64___lor) (a b : [UInt64]](#manual-UInt64___ofBitVec)) : [UInt64]](#manual-UInt64___ofBitVec)
```

Bitwise or for 64-bit unsigned integers. Usually accessed via the `|||` operator.

Each bit of the resulting integer is set if at least one of the corresponding bits of both input
integers are set.

This function is overridden at runtime with an efficient implementation.

def

```lean
[Int64.lor]](#manual-Int64___lor) (a b : [Int64]](#manual-Int64___ofUInt64)) : [Int64]](#manual-Int64___ofUInt64)



[Int64.lor]](#manual-Int64___lor) (a b : [Int64]](#manual-Int64___ofUInt64)) : [Int64]](#manual-Int64___ofUInt64)
```

Bitwise or for 64-bit signed integers. Usually accessed via the `|||` operator.

Each bit of the resulting integer is set if at least one of the corresponding bits of the input
integers is set, according to the two's complement representation.

This function is overridden at runtime with an efficient implementation.

def

```lean
[USize.xor]](#manual-USize___xor) (a b : [USize]](#manual-USize___ofBitVec)) : [USize]](#manual-USize___ofBitVec)



[USize.xor]](#manual-USize___xor) (a b : [USize]](#manual-USize___ofBitVec)) : [USize]](#manual-USize___ofBitVec)
```

Bitwise exclusive or for word-sized unsigned integers. Usually accessed via the `^^^` operator.

Each bit of the resulting integer is set if exactly one of the corresponding bits of both input
integers are set.

This function is overridden at runtime with an efficient implementation.

def

```lean
[ISize.xor]](#manual-ISize___xor) (a b : [ISize]](#manual-ISize___ofUSize)) : [ISize]](#manual-ISize___ofUSize)



[ISize.xor]](#manual-ISize___xor) (a b : [ISize]](#manual-ISize___ofUSize)) : [ISize]](#manual-ISize___ofUSize)
```

Bitwise exclusive or for word-sized signed integers. Usually accessed via the `^^^` operator.

Each bit of the resulting integer is set if exactly one of the corresponding bits of the input
integers is set, according to the two's complement representation.

This function is overridden at runtime with an efficient implementation.

def

```lean
[UInt8.xor]](#manual-UInt8___xor) (a b : [UInt8]](#manual-UInt8___ofBitVec)) : [UInt8]](#manual-UInt8___ofBitVec)



[UInt8.xor]](#manual-UInt8___xor) (a b : [UInt8]](#manual-UInt8___ofBitVec)) : [UInt8]](#manual-UInt8___ofBitVec)
```

Bitwise exclusive or for 8-bit unsigned integers. Usually accessed via the `^^^` operator.

Each bit of the resulting integer is set if exactly one of the corresponding bits of both input
integers are set.

This function is overridden at runtime with an efficient implementation.

def

```lean
[Int8.xor]](#manual-Int8___xor) (a b : [Int8]](#manual-Int8___ofUInt8)) : [Int8]](#manual-Int8___ofUInt8)



[Int8.xor]](#manual-Int8___xor) (a b : [Int8]](#manual-Int8___ofUInt8)) : [Int8]](#manual-Int8___ofUInt8)
```

Bitwise exclusive or for 8-bit signed integers. Usually accessed via the `^^^` operator.

Each bit of the resulting integer is set if exactly one of the corresponding bits of the input
integers is set, according to the two's complement representation.

This function is overridden at runtime with an efficient implementation.

def

```lean
[UInt16.xor]](#manual-UInt16___xor) (a b : [UInt16]](#manual-UInt16___ofBitVec)) : [UInt16]](#manual-UInt16___ofBitVec)



[UInt16.xor]](#manual-UInt16___xor) (a b : [UInt16]](#manual-UInt16___ofBitVec)) : [UInt16]](#manual-UInt16___ofBitVec)
```

Bitwise exclusive or for 16-bit unsigned integers. Usually accessed via the `^^^` operator.

Each bit of the resulting integer is set if exactly one of the corresponding bits of both input
integers are set.

This function is overridden at runtime with an efficient implementation.

def

```lean
[Int16.xor]](#manual-Int16___xor) (a b : [Int16]](#manual-Int16___ofUInt16)) : [Int16]](#manual-Int16___ofUInt16)



[Int16.xor]](#manual-Int16___xor) (a b : [Int16]](#manual-Int16___ofUInt16)) : [Int16]](#manual-Int16___ofUInt16)
```

Bitwise exclusive or for 16-bit signed integers. Usually accessed via the `^^^` operator.

Each bit of the resulting integer is set if exactly one of the corresponding bits of the input
integers is set, according to the two's complement representation.

This function is overridden at runtime with an efficient implementation.

def

```lean
[UInt32.xor]](#manual-UInt32___xor) (a b : [UInt32]](#manual-UInt32___ofBitVec)) : [UInt32]](#manual-UInt32___ofBitVec)



[UInt32.xor]](#manual-UInt32___xor) (a b : [UInt32]](#manual-UInt32___ofBitVec)) : [UInt32]](#manual-UInt32___ofBitVec)
```

Bitwise exclusive or for 32-bit unsigned integers. Usually accessed via the `^^^` operator.

Each bit of the resulting integer is set if exactly one of the corresponding bits of both input
integers are set.

This function is overridden at runtime with an efficient implementation.

def

```lean
[Int32.xor]](#manual-Int32___xor) (a b : [Int32]](#manual-Int32___ofUInt32)) : [Int32]](#manual-Int32___ofUInt32)



[Int32.xor]](#manual-Int32___xor) (a b : [Int32]](#manual-Int32___ofUInt32)) : [Int32]](#manual-Int32___ofUInt32)
```

Bitwise exclusive or for 32-bit signed integers. Usually accessed via the `^^^` operator.

Each bit of the resulting integer is set if exactly one of the corresponding bits of the input
integers is set, according to the two's complement representation.

This function is overridden at runtime with an efficient implementation.

def

```lean
[UInt64.xor]](#manual-UInt64___xor) (a b : [UInt64]](#manual-UInt64___ofBitVec)) : [UInt64]](#manual-UInt64___ofBitVec)



[UInt64.xor]](#manual-UInt64___xor) (a b : [UInt64]](#manual-UInt64___ofBitVec)) : [UInt64]](#manual-UInt64___ofBitVec)
```

Bitwise exclusive or for 64-bit unsigned integers. Usually accessed via the `^^^` operator.

Each bit of the resulting integer is set if exactly one of the corresponding bits of both input
integers are set.

This function is overridden at runtime with an efficient implementation.

def

```lean
[Int64.xor]](#manual-Int64___xor) (a b : [Int64]](#manual-Int64___ofUInt64)) : [Int64]](#manual-Int64___ofUInt64)



[Int64.xor]](#manual-Int64___xor) (a b : [Int64]](#manual-Int64___ofUInt64)) : [Int64]](#manual-Int64___ofUInt64)
```

Bitwise exclusive or for 64-bit signed integers. Usually accessed via the `^^^` operator.

Each bit of the resulting integer is set if exactly one of the corresponding bits of the input
integers is set, according to the two's complement representation.

This function is overridden at runtime with an efficient implementation.

def

```lean
[USize.complement]](#manual-USize___complement) (a : [USize]](#manual-USize___ofBitVec)) : [USize]](#manual-USize___ofBitVec)



[USize.complement]](#manual-USize___complement) (a : [USize]](#manual-USize___ofBitVec)) : [USize]](#manual-USize___ofBitVec)
```

Bitwise complement, also known as bitwise negation, for word-sized unsigned integers. Usually
accessed via the `~~~` prefix operator.

Each bit of the resulting integer is the opposite of the corresponding bit of the input integer.

This function is overridden at runtime with an efficient implementation.

def

```lean
[ISize.complement]](#manual-ISize___complement) (a : [ISize]](#manual-ISize___ofUSize)) : [ISize]](#manual-ISize___ofUSize)



[ISize.complement]](#manual-ISize___complement) (a : [ISize]](#manual-ISize___ofUSize)) : [ISize]](#manual-ISize___ofUSize)
```

Bitwise complement, also known as bitwise negation, for word-sized signed integers. Usually accessed
via the `~~~` prefix operator.

Each bit of the resulting integer is the opposite of the corresponding bit of the input integer.
Integers use the two's complement representation, so `[ISize.complement]](#manual-ISize___complement) a = -(a + 1)`.

This function is overridden at runtime with an efficient implementation.

def

```lean
[UInt8.complement]](#manual-UInt8___complement) (a : [UInt8]](#manual-UInt8___ofBitVec)) : [UInt8]](#manual-UInt8___ofBitVec)



[UInt8.complement]](#manual-UInt8___complement) (a : [UInt8]](#manual-UInt8___ofBitVec)) : [UInt8]](#manual-UInt8___ofBitVec)
```

Bitwise complement, also known as bitwise negation, for 8-bit unsigned integers. Usually accessed
via the `~~~` prefix operator.

Each bit of the resulting integer is the opposite of the corresponding bit of the input integer.

This function is overridden at runtime with an efficient implementation.

def

```lean
[Int8.complement]](#manual-Int8___complement) (a : [Int8]](#manual-Int8___ofUInt8)) : [Int8]](#manual-Int8___ofUInt8)



[Int8.complement]](#manual-Int8___complement) (a : [Int8]](#manual-Int8___ofUInt8)) : [Int8]](#manual-Int8___ofUInt8)
```

Bitwise complement, also known as bitwise negation, for 8-bit signed integers. Usually accessed via
the `~~~` prefix operator.

Each bit of the resulting integer is the opposite of the corresponding bit of the input integer.
Integers use the two's complement representation, so `[Int8.complement]](#manual-Int8___complement) a = -(a + 1)`.

This function is overridden at runtime with an efficient implementation.

def

```lean
[UInt16.complement]](#manual-UInt16___complement) (a : [UInt16]](#manual-UInt16___ofBitVec)) : [UInt16]](#manual-UInt16___ofBitVec)



[UInt16.complement]](#manual-UInt16___complement) (a : [UInt16]](#manual-UInt16___ofBitVec)) : [UInt16]](#manual-UInt16___ofBitVec)
```

Bitwise complement, also known as bitwise negation, for 16-bit unsigned integers. Usually accessed
via the `~~~` prefix operator.

Each bit of the resulting integer is the opposite of the corresponding bit of the input integer.

This function is overridden at runtime with an efficient implementation.

def

```lean
[Int16.complement]](#manual-Int16___complement) (a : [Int16]](#manual-Int16___ofUInt16)) : [Int16]](#manual-Int16___ofUInt16)



[Int16.complement]](#manual-Int16___complement) (a : [Int16]](#manual-Int16___ofUInt16)) : [Int16]](#manual-Int16___ofUInt16)
```

Bitwise complement, also known as bitwise negation, for 16-bit signed integers. Usually accessed via
the `~~~` prefix operator.

Each bit of the resulting integer is the opposite of the corresponding bit of the input integer.
Integers use the two's complement representation, so `[Int16.complement]](#manual-Int16___complement) a = -(a + 1)`.

This function is overridden at runtime with an efficient implementation.

def

```lean
[UInt32.complement]](#manual-UInt32___complement) (a : [UInt32]](#manual-UInt32___ofBitVec)) : [UInt32]](#manual-UInt32___ofBitVec)



[UInt32.complement]](#manual-UInt32___complement) (a : [UInt32]](#manual-UInt32___ofBitVec)) : [UInt32]](#manual-UInt32___ofBitVec)
```

Bitwise complement, also known as bitwise negation, for 32-bit unsigned integers. Usually accessed
via the `~~~` prefix operator.

Each bit of the resulting integer is the opposite of the corresponding bit of the input integer.

This function is overridden at runtime with an efficient implementation.

def

```lean
[Int32.complement]](#manual-Int32___complement) (a : [Int32]](#manual-Int32___ofUInt32)) : [Int32]](#manual-Int32___ofUInt32)



[Int32.complement]](#manual-Int32___complement) (a : [Int32]](#manual-Int32___ofUInt32)) : [Int32]](#manual-Int32___ofUInt32)
```

Bitwise complement, also known as bitwise negation, for 32-bit signed integers. Usually accessed via
the `~~~` prefix operator.

Each bit of the resulting integer is the opposite of the corresponding bit of the input integer.
Integers use the two's complement representation, so `[Int32.complement]](#manual-Int32___complement) a = -(a + 1)`.

This function is overridden at runtime with an efficient implementation.

def

```lean
[UInt64.complement]](#manual-UInt64___complement) (a : [UInt64]](#manual-UInt64___ofBitVec)) : [UInt64]](#manual-UInt64___ofBitVec)



[UInt64.complement]](#manual-UInt64___complement) (a : [UInt64]](#manual-UInt64___ofBitVec)) : [UInt64]](#manual-UInt64___ofBitVec)
```

Bitwise complement, also known as bitwise negation, for 64-bit unsigned integers. Usually accessed
via the `~~~` prefix operator.

Each bit of the resulting integer is the opposite of the corresponding bit of the input integer.

This function is overridden at runtime with an efficient implementation.

def

```lean
[Int64.complement]](#manual-Int64___complement) (a : [Int64]](#manual-Int64___ofUInt64)) : [Int64]](#manual-Int64___ofUInt64)



[Int64.complement]](#manual-Int64___complement) (a : [Int64]](#manual-Int64___ofUInt64)) : [Int64]](#manual-Int64___ofUInt64)
```

Bitwise complement, also known as bitwise negation, for 64-bit signed integers. Usually accessed via
the `~~~` prefix operator.

Each bit of the resulting integer is the opposite of the corresponding bit of the input integer.
Integers use the two's complement representation, so `[Int64.complement]](#manual-Int64___complement) a = -(a + 1)`.

This function is overridden at runtime with an efficient implementation.

def

```lean
[USize.shiftLeft]](#manual-USize___shiftLeft) (a b : [USize]](#manual-USize___ofBitVec)) : [USize]](#manual-USize___ofBitVec)



[USize.shiftLeft]](#manual-USize___shiftLeft) (a b : [USize]](#manual-USize___ofBitVec)) : [USize]](#manual-USize___ofBitVec)
```

Bitwise left shift for word-sized unsigned integers. Usually accessed via the `<<<` operator.

This function is overridden at runtime with an efficient implementation.

def

```lean
[ISize.shiftLeft]](#manual-ISize___shiftLeft) (a b : [ISize]](#manual-ISize___ofUSize)) : [ISize]](#manual-ISize___ofUSize)



[ISize.shiftLeft]](#manual-ISize___shiftLeft) (a b : [ISize]](#manual-ISize___ofUSize)) : [ISize]](#manual-ISize___ofUSize)
```

Bitwise left shift for word-sized signed integers. Usually accessed via the `<<<` operator.

Signed integers are interpreted as bitvectors according to the two's complement representation.

This function is overridden at runtime with an efficient implementation.

def

```lean
[UInt8.shiftLeft]](#manual-UInt8___shiftLeft) (a b : [UInt8]](#manual-UInt8___ofBitVec)) : [UInt8]](#manual-UInt8___ofBitVec)



[UInt8.shiftLeft]](#manual-UInt8___shiftLeft) (a b : [UInt8]](#manual-UInt8___ofBitVec)) : [UInt8]](#manual-UInt8___ofBitVec)
```

Bitwise left shift for 8-bit unsigned integers. Usually accessed via the `<<<` operator.

This function is overridden at runtime with an efficient implementation.

def

```lean
[Int8.shiftLeft]](#manual-Int8___shiftLeft) (a b : [Int8]](#manual-Int8___ofUInt8)) : [Int8]](#manual-Int8___ofUInt8)



[Int8.shiftLeft]](#manual-Int8___shiftLeft) (a b : [Int8]](#manual-Int8___ofUInt8)) : [Int8]](#manual-Int8___ofUInt8)
```

Bitwise left shift for 8-bit signed integers. Usually accessed via the `<<<` operator.

Signed integers are interpreted as bitvectors according to the two's complement representation.

This function is overridden at runtime with an efficient implementation.

def

```lean
[UInt16.shiftLeft]](#manual-UInt16___shiftLeft) (a b : [UInt16]](#manual-UInt16___ofBitVec)) : [UInt16]](#manual-UInt16___ofBitVec)



[UInt16.shiftLeft]](#manual-UInt16___shiftLeft) (a b : [UInt16]](#manual-UInt16___ofBitVec)) : [UInt16]](#manual-UInt16___ofBitVec)
```

Bitwise left shift for 16-bit unsigned integers. Usually accessed via the `<<<` operator.

This function is overridden at runtime with an efficient implementation.

def

```lean
[Int16.shiftLeft]](#manual-Int16___shiftLeft) (a b : [Int16]](#manual-Int16___ofUInt16)) : [Int16]](#manual-Int16___ofUInt16)



[Int16.shiftLeft]](#manual-Int16___shiftLeft) (a b : [Int16]](#manual-Int16___ofUInt16)) : [Int16]](#manual-Int16___ofUInt16)
```

Bitwise left shift for 16-bit signed integers. Usually accessed via the `<<<` operator.

Signed integers are interpreted as bitvectors according to the two's complement representation.

This function is overridden at runtime with an efficient implementation.

def

```lean
[UInt32.shiftLeft]](#manual-UInt32___shiftLeft) (a b : [UInt32]](#manual-UInt32___ofBitVec)) : [UInt32]](#manual-UInt32___ofBitVec)



[UInt32.shiftLeft]](#manual-UInt32___shiftLeft) (a b : [UInt32]](#manual-UInt32___ofBitVec)) : [UInt32]](#manual-UInt32___ofBitVec)
```

Bitwise left shift for 32-bit unsigned integers. Usually accessed via the `<<<` operator.

This function is overridden at runtime with an efficient implementation.

def

```lean
[Int32.shiftLeft]](#manual-Int32___shiftLeft) (a b : [Int32]](#manual-Int32___ofUInt32)) : [Int32]](#manual-Int32___ofUInt32)



[Int32.shiftLeft]](#manual-Int32___shiftLeft) (a b : [Int32]](#manual-Int32___ofUInt32)) : [Int32]](#manual-Int32___ofUInt32)
```

Bitwise left shift for 32-bit signed integers. Usually accessed via the `<<<` operator.

Signed integers are interpreted as bitvectors according to the two's complement representation.

This function is overridden at runtime with an efficient implementation.

def

```lean
[UInt64.shiftLeft]](#manual-UInt64___shiftLeft) (a b : [UInt64]](#manual-UInt64___ofBitVec)) : [UInt64]](#manual-UInt64___ofBitVec)



[UInt64.shiftLeft]](#manual-UInt64___shiftLeft) (a b : [UInt64]](#manual-UInt64___ofBitVec)) : [UInt64]](#manual-UInt64___ofBitVec)
```

Bitwise left shift for 64-bit unsigned integers. Usually accessed via the `<<<` operator.

This function is overridden at runtime with an efficient implementation.

def

```lean
[Int64.shiftLeft]](#manual-Int64___shiftLeft) (a b : [Int64]](#manual-Int64___ofUInt64)) : [Int64]](#manual-Int64___ofUInt64)



[Int64.shiftLeft]](#manual-Int64___shiftLeft) (a b : [Int64]](#manual-Int64___ofUInt64)) : [Int64]](#manual-Int64___ofUInt64)
```

Bitwise left shift for 64-bit signed integers. Usually accessed via the `<<<` operator.

Signed integers are interpreted as bitvectors according to the two's complement representation.

This function is overridden at runtime with an efficient implementation.

def

```lean
[USize.shiftRight]](#manual-USize___shiftRight) (a b : [USize]](#manual-USize___ofBitVec)) : [USize]](#manual-USize___ofBitVec)



[USize.shiftRight]](#manual-USize___shiftRight) (a b : [USize]](#manual-USize___ofBitVec)) : [USize]](#manual-USize___ofBitVec)
```

Bitwise right shift for word-sized unsigned integers. Usually accessed via the `>>>` operator.

This function is overridden at runtime with an efficient implementation.

def

```lean
[ISize.shiftRight]](#manual-ISize___shiftRight) (a b : [ISize]](#manual-ISize___ofUSize)) : [ISize]](#manual-ISize___ofUSize)



[ISize.shiftRight]](#manual-ISize___shiftRight) (a b : [ISize]](#manual-ISize___ofUSize)) : [ISize]](#manual-ISize___ofUSize)
```

Arithmetic right shift for word-sized signed integers. Usually accessed via the `<<<` operator.

The high bits are filled with the value of
the most significant bit.

This function is overridden at runtime with an efficient implementation.

def

```lean
[UInt8.shiftRight]](#manual-UInt8___shiftRight) (a b : [UInt8]](#manual-UInt8___ofBitVec)) : [UInt8]](#manual-UInt8___ofBitVec)



[UInt8.shiftRight]](#manual-UInt8___shiftRight) (a b : [UInt8]](#manual-UInt8___ofBitVec)) : [UInt8]](#manual-UInt8___ofBitVec)
```

Bitwise right shift for 8-bit unsigned integers. Usually accessed via the `>>>` operator.

This function is overridden at runtime with an efficient implementation.

def

```lean
[Int8.shiftRight]](#manual-Int8___shiftRight) (a b : [Int8]](#manual-Int8___ofUInt8)) : [Int8]](#manual-Int8___ofUInt8)



[Int8.shiftRight]](#manual-Int8___shiftRight) (a b : [Int8]](#manual-Int8___ofUInt8)) : [Int8]](#manual-Int8___ofUInt8)
```

Arithmetic right shift for 8-bit signed integers. Usually accessed via the `<<<` operator.

The high bits are filled with the value of the most significant bit.

This function is overridden at runtime with an efficient implementation.

def

```lean
[UInt16.shiftRight]](#manual-UInt16___shiftRight) (a b : [UInt16]](#manual-UInt16___ofBitVec)) : [UInt16]](#manual-UInt16___ofBitVec)



[UInt16.shiftRight]](#manual-UInt16___shiftRight) (a b : [UInt16]](#manual-UInt16___ofBitVec)) : [UInt16]](#manual-UInt16___ofBitVec)
```

Bitwise right shift for 16-bit unsigned integers. Usually accessed via the `>>>` operator.

This function is overridden at runtime with an efficient implementation.

def

```lean
[Int16.shiftRight]](#manual-Int16___shiftRight) (a b : [Int16]](#manual-Int16___ofUInt16)) : [Int16]](#manual-Int16___ofUInt16)



[Int16.shiftRight]](#manual-Int16___shiftRight) (a b : [Int16]](#manual-Int16___ofUInt16)) : [Int16]](#manual-Int16___ofUInt16)
```

Arithmetic right shift for 16-bit signed integers. Usually accessed via the `<<<` operator.

The high bits are filled with the value of the most significant bit.

This function is overridden at runtime with an efficient implementation.

def

```lean
[UInt32.shiftRight]](#manual-UInt32___shiftRight) (a b : [UInt32]](#manual-UInt32___ofBitVec)) : [UInt32]](#manual-UInt32___ofBitVec)



[UInt32.shiftRight]](#manual-UInt32___shiftRight) (a b : [UInt32]](#manual-UInt32___ofBitVec)) : [UInt32]](#manual-UInt32___ofBitVec)
```

Bitwise right shift for 32-bit unsigned integers. Usually accessed via the `>>>` operator.

This function is overridden at runtime with an efficient implementation.

def

```lean
[Int32.shiftRight]](#manual-Int32___shiftRight) (a b : [Int32]](#manual-Int32___ofUInt32)) : [Int32]](#manual-Int32___ofUInt32)



[Int32.shiftRight]](#manual-Int32___shiftRight) (a b : [Int32]](#manual-Int32___ofUInt32)) : [Int32]](#manual-Int32___ofUInt32)
```

Arithmetic right shift for 32-bit signed integers. Usually accessed via the `<<<` operator.

The high bits are filled with the value of the most significant bit.

This function is overridden at runtime with an efficient implementation.

def

```lean
[UInt64.shiftRight]](#manual-UInt64___shiftRight) (a b : [UInt64]](#manual-UInt64___ofBitVec)) : [UInt64]](#manual-UInt64___ofBitVec)



[UInt64.shiftRight]](#manual-UInt64___shiftRight) (a b : [UInt64]](#manual-UInt64___ofBitVec)) : [UInt64]](#manual-UInt64___ofBitVec)
```

Bitwise right shift for 64-bit unsigned integers. Usually accessed via the `>>>` operator.

This function is overridden at runtime with an efficient implementation.

def

```lean
[Int64.shiftRight]](#manual-Int64___shiftRight) (a b : [Int64]](#manual-Int64___ofUInt64)) : [Int64]](#manual-Int64___ofUInt64)



[Int64.shiftRight]](#manual-Int64___shiftRight) (a b : [Int64]](#manual-Int64___ofUInt64)) : [Int64]](#manual-Int64___ofUInt64)
```

Arithmetic right shift for 64-bit signed integers. Usually accessed via the `<<<` operator.

The high bits are filled with the value of the most significant bit.

This function is overridden at runtime with an efficient implementation.

---



## Basic Types — 20.5. Bitvectors {#manual-basic-types-205-bitvectors}

> 📄 Source: https://lean-lang.org/doc/reference/latest/Basic-Types/Bitvectors/

Bitvectors are fixed-width sequences of binary digits.
They are frequently used in software verification, because they closely model efficient data structures and operations that are similar to hardware.
A bitvector can be understood from two perspectives: as a sequence of bits, or as a number encoded by a sequence of bits.
When a bitvector represents a number, it can do so as either a signed or an unsigned number.
Signed numbers are represented in two's complement form.

### 20.5.1. Logical Model {#manual-The-Lean-Language-Reference--Basic-Types--Bitvectors--Logical-Model}

Bitvectors are represented as a wrapper around a `[Fin]](#manual-Fin___mk)` with a suitable bound.
Because `[Fin]](#manual-Fin___mk)` itself is a wrapper around a `[Nat]](#manual-Nat___zero)`, bitvectors are able to use the kernel's special support for efficient computation with natural numbers.

structure

```lean
[BitVec]](#manual-BitVec___ofFin) (w : [Nat]](#manual-Nat___zero)) : Type



[BitVec]](#manual-BitVec___ofFin) (w : [Nat]](#manual-Nat___zero)) : Type
```

A bitvector of the specified width.

This is represented as the underlying `[Nat]](#manual-Nat___zero)` number in both the runtime
and the kernel, inheriting all the special support for `[Nat]](#manual-Nat___zero)`.

Constructor

```lean
[BitVec.ofFin]](#manual-BitVec___ofFin)
```

Construct a `[BitVec]](#manual-BitVec___ofFin) w` from a number less than `2^w`.
O(1), because we use `[Fin]](#manual-Fin___mk)` as the internal representation of a bitvector.

Fields

```lean
toFin : [Fin]](#manual-Fin___mk) [(]](#manual-HPow___mk)2 [^]](#manual-HPow___mk) w[)]](#manual-HPow___mk)
```

Interpret a bitvector as a number less than `2^w`.
O(1), because we use `[Fin]](#manual-Fin___mk)` as the internal representation of a bitvector.

### 20.5.2. Runtime Representation {#manual-The-Lean-Language-Reference--Basic-Types--Bitvectors--Runtime-Representation}

Bitvectors are represented as a `[Fin]](#manual-Fin___mk)` with the corresponding range.
Because `[BitVec]](#manual-BitVec___ofFin)` is a [trivial wrapper]](#manual-inductive-types-trivial-wrappers) around `[Fin]](#manual-Fin___mk)` and `[Fin]](#manual-Fin___mk)` is a trivial wrapper around `[Nat]](#manual-Nat___zero)`, bitvectors use the same runtime representation as `[Nat]](#manual-Nat___zero)` in compiled code.

### 20.5.3. Syntax {#manual-The-Lean-Language-Reference--Basic-Types--Bitvectors--Syntax}

There is an `[OfNat]](#manual-OfNat___mk) ([BitVec]](#manual-BitVec___ofFin) w) n` instance for all widths `w` and natural numbers `n`.
Natural number literals, including those that use hexadecimal or binary notation, may be used to represent bitvectors in contexts where the expected type is known.
When the expected type is not known, a dedicated syntax allows the width of the bitvector to be specified along with its value.

**Example: Numeric Literals for Bitvectors**

The following literals are all equivalent:

```lean
example : [BitVec]](#manual-BitVec___ofFin) 8 := 0xff
example : [BitVec]](#manual-BitVec___ofFin) 8 := 255
example : [BitVec]](#manual-BitVec___ofFin) 8 := 0b1111_1111
```

syntaxFixed-Width Bitvector Literals

```lean
term ::= ...
    | num#term
```

This notation pairs a numeric literal with a term that denotes its width.
Spaces are forbidden around the `#`.
Literals that overflow the width of the bitvector are truncated.

**Example: Fixed-Width Bitvector Literals**

Bitvectors may be represented by natural number literals, so `(5 : [BitVec]](#manual-BitVec___ofFin) 8)` is a valid bitvector.
Additionally, a width may be specified directly in the literal:

```lean
5#8
```

Spaces are not allowed on either side of the `#`:

```lean
```lean
5 #8
```
```

```lean
<example>:1:2-1:3: expected end of input
```

```lean
```lean
5# 8
```
```

```lean
<example>:1:3-1:4: expected no space before
```

A numeric literal is required to the left of the `#`:

```lean
```lean
(3 + 2)#8
```
```

```lean
<example>:1:7-1:8: expected end of input
```

However, a term is allowed to the right of the `#`:

```lean
5#(4 + 4)
```

If the literal is too large to fit in the specified number of bits, then it is truncated:

```lean
[#eval]](#manual-Lean___Parser___Command___eval) 7#2
```

```lean
[3]](#manual-BitVec___ofNat)[#]](#manual-BitVec___ofNat)2
```

syntaxBounded Bitvector Literals

```lean
term ::= ...
    | num#'term
```

This notation is available only when the `BitVec` namespace has been opened.
Rather than an explicit width, it expects a proof that the literal value is representable by a bitvector of the corresponding width.

**Example: Bounded Bitvector Literals**

The bounded bitvector literal notation ensures that literals do not overflow the specified number of bits.
The notation is only available when the `BitVec` namespace has been opened.

```lean
[open]](#manual-Lean___Parser___Command___open) BitVec
```

Literals that are in bounds require a proof to that effect:

```lean
example : [BitVec]](#manual-BitVec___ofFin) 8 := 1#'(by⊢ 1 [<]](#manual-LT___mk) 2 [^]](#manual-HPow___mk) 8 [decide]](#manual-decide)All goals completed! 🐙)
```

Literals that are not in bounds are not allowed:

```lean
example : [BitVec]](#manual-BitVec___ofFin) 8 := 256#'(by⊢ 256 [<]](#manual-LT___mk) 2 [^]](#manual-HPow___mk) 8 [decide]](#manual-decide)⊢ 256 [<]](#manual-LT___mk) 2 [^]](#manual-HPow___mk) 8)
```

```lean
Tactic `decide` proved that the proposition
  256 [<]](#manual-LT___mk) 2 [^]](#manual-HPow___mk) 8
is false
```

### 20.5.4. Automation {#manual-BitVec-automation}

In addition to the full suite of automation and tools provided by Lean for every type, the `[bv_decide]](#manual-bv_decide)` tactic can solve many bitvector-related problems.
This tactic invokes an external automated theorem prover (`cadical`) and reconstructs the proof that it provides in Lean's own logic.
The resulting proofs rely only on the axiom `Lean.ofReduceBool`; the external prover is not part of the trusted code base.

**Example: Popcount**

The function `[popcount]](#manual-popcount-_LPAR_in-Popcount_RPAR_)` returns the number of set bits in a bitvector.
It can be implemented as a 32-iteration loop that tests each bit, incrementing a counter if the bit is set:

```lean
def popcount_spec (x : [BitVec]](#manual-BitVec___ofFin) 32) : [BitVec]](#manual-BitVec___ofFin) 32 :=
(32 : [Nat]](#manual-Nat___zero)).[fold]](#manual-Nat___fold) (init := 0) fun i _ pop =>
pop + ((x >>> i) &&& 1)
```

An alternative implementation of `[popcount]](#manual-popcount-_LPAR_in-Popcount_RPAR_)` is described in *Hacker's Delight, Second Edition*, by Henry S. Warren,
Jr. in Figure 5-2 on p. 82.
It uses low-level bitwise operations to compute the same value with far fewer operations:

```lean
def popcount (x : [BitVec]](#manual-BitVec___ofFin) 32) : [BitVec]](#manual-BitVec___ofFin) 32 :=
let x := x - ((x >>> 1) &&& 0x55555555)
let x := (x &&& 0x33333333) + ((x >>> 2) &&& 0x33333333)
let x := (x + (x >>> 4)) &&& 0x0F0F0F0F
let x := x + (x >>> 8)
let x := x + (x >>> 16)
let x := x &&& 0x0000003F
x
```

These two implementations can be proven equivalent using `[bv_decide]](#manual-bv_decide)`:

```lean
theorem popcount_correct : [popcount]](#manual-popcount-_LPAR_in-Popcount_RPAR_) = [popcount_spec]](#manual-popcount_spec-_LPAR_in-Popcount_RPAR_) := by⊢ [popcount]](#manual-popcount-_LPAR_in-Popcount_RPAR_) [=]](#manual-Eq___refl) [popcount_spec]](#manual-popcount_spec-_LPAR_in-Popcount_RPAR_)
[funext]](#manual-funext-next) xx:[BitVec]](#manual-BitVec___ofFin) 32⊢ [popcount]](#manual-popcount-_LPAR_in-Popcount_RPAR_) x [=]](#manual-Eq___refl) [popcount_spec]](#manual-popcount_spec-_LPAR_in-Popcount_RPAR_) x
[simp]](#manual-simp) [[popcount]](#manual-popcount-_LPAR_in-Popcount_RPAR_), [popcount_spec]](#manual-popcount_spec-_LPAR_in-Popcount_RPAR_)]x:[BitVec]](#manual-BitVec___ofFin) 32⊢ [(]](#manual-HAnd___mk)[(]](#manual-HAnd___mk)x [-]](#manual-HSub___mk) [(]](#manual-HAnd___mk)x [>>>]](#manual-HShiftRight___mk) 1 [&&&]](#manual-HAnd___mk) [1431655765]](#manual-BitVec___ofNat)[#]](#manual-BitVec___ofNat)32[)]](#manual-HAnd___mk) [&&&]](#manual-HAnd___mk) [858993459]](#manual-BitVec___ofNat)[#]](#manual-BitVec___ofNat)32[)]](#manual-HAnd___mk) [+]](#manual-HAdd___mk) [(]](#manual-HAnd___mk)[(]](#manual-HSub___mk)x [-]](#manual-HSub___mk) [(]](#manual-HAnd___mk)x [>>>]](#manual-HShiftRight___mk) 1 [&&&]](#manual-HAnd___mk) [1431655765]](#manual-BitVec___ofNat)[#]](#manual-BitVec___ofNat)32[)]](#manual-HAnd___mk)[)]](#manual-HSub___mk) [>>>]](#manual-HShiftRight___mk) 2 [&&&]](#manual-HAnd___mk) [858993459]](#manual-BitVec___ofNat)[#]](#manual-BitVec___ofNat)32[)]](#manual-HAnd___mk) [+]](#manual-HAdd___mk)
[(]](#manual-HAdd___mk)[(]](#manual-HAnd___mk)x [-]](#manual-HSub___mk) [(]](#manual-HAnd___mk)x [>>>]](#manual-HShiftRight___mk) 1 [&&&]](#manual-HAnd___mk) [1431655765]](#manual-BitVec___ofNat)[#]](#manual-BitVec___ofNat)32[)]](#manual-HAnd___mk) [&&&]](#manual-HAnd___mk) [858993459]](#manual-BitVec___ofNat)[#]](#manual-BitVec___ofNat)32[)]](#manual-HAnd___mk) [+]](#manual-HAdd___mk)
[(]](#manual-HAnd___mk)[(]](#manual-HSub___mk)x [-]](#manual-HSub___mk) [(]](#manual-HAnd___mk)x [>>>]](#manual-HShiftRight___mk) 1 [&&&]](#manual-HAnd___mk) [1431655765]](#manual-BitVec___ofNat)[#]](#manual-BitVec___ofNat)32[)]](#manual-HAnd___mk)[)]](#manual-HSub___mk) [>>>]](#manual-HShiftRight___mk) 2 [&&&]](#manual-HAnd___mk) [858993459]](#manual-BitVec___ofNat)[#]](#manual-BitVec___ofNat)32[)]](#manual-HAnd___mk)[)]](#manual-HAdd___mk) [>>>]](#manual-HShiftRight___mk)
4 [&&&]](#manual-HAnd___mk)
[252645135]](#manual-BitVec___ofNat)[#]](#manual-BitVec___ofNat)32[)]](#manual-HAnd___mk) [+]](#manual-HAdd___mk)
[(]](#manual-HAnd___mk)[(]](#manual-HAnd___mk)x [-]](#manual-HSub___mk) [(]](#manual-HAnd___mk)x [>>>]](#manual-HShiftRight___mk) 1 [&&&]](#manual-HAnd___mk) [1431655765]](#manual-BitVec___ofNat)[#]](#manual-BitVec___ofNat)32[)]](#manual-HAnd___mk) [&&&]](#manual-HAnd___mk) [858993459]](#manual-BitVec___ofNat)[#]](#manual-BitVec___ofNat)32[)]](#manual-HAnd___mk) [+]](#manual-HAdd___mk)
[(]](#manual-HAnd___mk)[(]](#manual-HSub___mk)x [-]](#manual-HSub___mk) [(]](#manual-HAnd___mk)x [>>>]](#manual-HShiftRight___mk) 1 [&&&]](#manual-HAnd___mk) [1431655765]](#manual-BitVec___ofNat)[#]](#manual-BitVec___ofNat)32[)]](#manual-HAnd___mk)[)]](#manual-HSub___mk) [>>>]](#manual-HShiftRight___mk) 2 [&&&]](#manual-HAnd___mk) [858993459]](#manual-BitVec___ofNat)[#]](#manual-BitVec___ofNat)32[)]](#manual-HAnd___mk) [+]](#manual-HAdd___mk)
[(]](#manual-HAdd___mk)[(]](#manual-HAnd___mk)x [-]](#manual-HSub___mk) [(]](#manual-HAnd___mk)x [>>>]](#manual-HShiftRight___mk) 1 [&&&]](#manual-HAnd___mk) [1431655765]](#manual-BitVec___ofNat)[#]](#manual-BitVec___ofNat)32[)]](#manual-HAnd___mk) [&&&]](#manual-HAnd___mk) [858993459]](#manual-BitVec___ofNat)[#]](#manual-BitVec___ofNat)32[)]](#manual-HAnd___mk) [+]](#manual-HAdd___mk)
[(]](#manual-HAnd___mk)[(]](#manual-HSub___mk)x [-]](#manual-HSub___mk) [(]](#manual-HAnd___mk)x [>>>]](#manual-HShiftRight___mk) 1 [&&&]](#manual-HAnd___mk) [1431655765]](#manual-BitVec___ofNat)[#]](#manual-BitVec___ofNat)32[)]](#manual-HAnd___mk)[)]](#manual-HSub___mk) [>>>]](#manual-HShiftRight___mk) 2 [&&&]](#manual-HAnd___mk) [858993459]](#manual-BitVec___ofNat)[#]](#manual-BitVec___ofNat)32[)]](#manual-HAnd___mk)[)]](#manual-HAdd___mk) [>>>]](#manual-HShiftRight___mk)
4 [&&&]](#manual-HAnd___mk)
[252645135]](#manual-BitVec___ofNat)[#]](#manual-BitVec___ofNat)32[)]](#manual-HAnd___mk) [>>>]](#manual-HShiftRight___mk)
8 [+]](#manual-HAdd___mk)
[(]](#manual-HAdd___mk)[(]](#manual-HAnd___mk)[(]](#manual-HAnd___mk)x [-]](#manual-HSub___mk) [(]](#manual-HAnd___mk)x [>>>]](#manual-HShiftRight___mk) 1 [&&&]](#manual-HAnd___mk) [1431655765]](#manual-BitVec___ofNat)[#]](#manual-BitVec___ofNat)32[)]](#manual-HAnd___mk) [&&&]](#manual-HAnd___mk) [858993459]](#manual-BitVec___ofNat)[#]](#manual-BitVec___ofNat)32[)]](#manual-HAnd___mk) [+]](#manual-HAdd___mk)
[(]](#manual-HAnd___mk)[(]](#manual-HSub___mk)x [-]](#manual-HSub___mk) [(]](#manual-HAnd___mk)x [>>>]](#manual-HShiftRight___mk) 1 [&&&]](#manual-HAnd___mk) [1431655765]](#manual-BitVec___ofNat)[#]](#manual-BitVec___ofNat)32[)]](#manual-HAnd___mk)[)]](#manual-HSub___mk) [>>>]](#manual-HShiftRight___mk) 2 [&&&]](#manual-HAnd___mk) [858993459]](#manual-BitVec___ofNat)[#]](#manual-BitVec___ofNat)32[)]](#manual-HAnd___mk) [+]](#manual-HAdd___mk)
[(]](#manual-HAdd___mk)[(]](#manual-HAnd___mk)x [-]](#manual-HSub___mk) [(]](#manual-HAnd___mk)x [>>>]](#manual-HShiftRight___mk) 1 [&&&]](#manual-HAnd___mk) [1431655765]](#manual-BitVec___ofNat)[#]](#manual-BitVec___ofNat)32[)]](#manual-HAnd___mk) [&&&]](#manual-HAnd___mk) [858993459]](#manual-BitVec___ofNat)[#]](#manual-BitVec___ofNat)32[)]](#manual-HAnd___mk) [+]](#manual-HAdd___mk)
[(]](#manual-HAnd___mk)[(]](#manual-HSub___mk)x [-]](#manual-HSub___mk) [(]](#manual-HAnd___mk)x [>>>]](#manual-HShiftRight___mk) 1 [&&&]](#manual-HAnd___mk) [1431655765]](#manual-BitVec___ofNat)[#]](#manual-BitVec___ofNat)32[)]](#manual-HAnd___mk)[)]](#manual-HSub___mk) [>>>]](#manual-HShiftRight___mk) 2 [&&&]](#manual-HAnd___mk) [858993459]](#manual-BitVec___ofNat)[#]](#manual-BitVec___ofNat)32[)]](#manual-HAnd___mk)[)]](#manual-HAdd___mk) [>>>]](#manual-HShiftRight___mk)
4 [&&&]](#manual-HAnd___mk)
[252645135]](#manual-BitVec___ofNat)[#]](#manual-BitVec___ofNat)32[)]](#manual-HAnd___mk) [+]](#manual-HAdd___mk)
[(]](#manual-HAnd___mk)[(]](#manual-HAnd___mk)x [-]](#manual-HSub___mk) [(]](#manual-HAnd___mk)x [>>>]](#manual-HShiftRight___mk) 1 [&&&]](#manual-HAnd___mk) [1431655765]](#manual-BitVec___ofNat)[#]](#manual-BitVec___ofNat)32[)]](#manual-HAnd___mk) [&&&]](#manual-HAnd___mk) [858993459]](#manual-BitVec___ofNat)[#]](#manual-BitVec___ofNat)32[)]](#manual-HAnd___mk) [+]](#manual-HAdd___mk)
[(]](#manual-HAnd___mk)[(]](#manual-HSub___mk)x [-]](#manual-HSub___mk) [(]](#manual-HAnd___mk)x [>>>]](#manual-HShiftRight___mk) 1 [&&&]](#manual-HAnd___mk) [1431655765]](#manual-BitVec___ofNat)[#]](#manual-BitVec___ofNat)32[)]](#manual-HAnd___mk)[)]](#manual-HSub___mk) [>>>]](#manual-HShiftRight___mk) 2 [&&&]](#manual-HAnd___mk) [858993459]](#manual-BitVec___ofNat)[#]](#manual-BitVec___ofNat)32[)]](#manual-HAnd___mk) [+]](#manual-HAdd___mk)
[(]](#manual-HAdd___mk)[(]](#manual-HAnd___mk)x [-]](#manual-HSub___mk) [(]](#manual-HAnd___mk)x [>>>]](#manual-HShiftRight___mk) 1 [&&&]](#manual-HAnd___mk) [1431655765]](#manual-BitVec___ofNat)[#]](#manual-BitVec___ofNat)32[)]](#manual-HAnd___mk) [&&&]](#manual-HAnd___mk) [858993459]](#manual-BitVec___ofNat)[#]](#manual-BitVec___ofNat)32[)]](#manual-HAnd___mk) [+]](#manual-HAdd___mk)
[(]](#manual-HAnd___mk)[(]](#manual-HSub___mk)x [-]](#manual-HSub___mk) [(]](#manual-HAnd___mk)x [>>>]](#manual-HShiftRight___mk) 1 [&&&]](#manual-HAnd___mk) [1431655765]](#manual-BitVec___ofNat)[#]](#manual-BitVec___ofNat)32[)]](#manual-HAnd___mk)[)]](#manual-HSub___mk) [>>>]](#manual-HShiftRight___mk) 2 [&&&]](#manual-HAnd___mk) [858993459]](#manual-BitVec___ofNat)[#]](#manual-BitVec___ofNat)32[)]](#manual-HAnd___mk)[)]](#manual-HAdd___mk) [>>>]](#manual-HShiftRight___mk)
4 [&&&]](#manual-HAnd___mk)
[252645135]](#manual-BitVec___ofNat)[#]](#manual-BitVec___ofNat)32[)]](#manual-HAnd___mk) [>>>]](#manual-HShiftRight___mk)
8[)]](#manual-HAdd___mk) [>>>]](#manual-HShiftRight___mk)
16 [&&&]](#manual-HAnd___mk)
[63]](#manual-BitVec___ofNat)[#]](#manual-BitVec___ofNat)32 [=]](#manual-Eq___refl)
[(]](#manual-HAnd___mk)x [&&&]](#manual-HAnd___mk) [1]](#manual-BitVec___ofNat)[#]](#manual-BitVec___ofNat)32[)]](#manual-HAnd___mk) [+]](#manual-HAdd___mk) [(]](#manual-HAnd___mk)x [>>>]](#manual-HShiftRight___mk) 1 [&&&]](#manual-HAnd___mk) [1]](#manual-BitVec___ofNat)[#]](#manual-BitVec___ofNat)32[)]](#manual-HAnd___mk) [+]](#manual-HAdd___mk) [(]](#manual-HAnd___mk)x [>>>]](#manual-HShiftRight___mk) 2 [&&&]](#manual-HAnd___mk) [1]](#manual-BitVec___ofNat)[#]](#manual-BitVec___ofNat)32[)]](#manual-HAnd___mk) [+]](#manual-HAdd___mk) [(]](#manual-HAnd___mk)x [>>>]](#manual-HShiftRight___mk) 3 [&&&]](#manual-HAnd___mk) [1]](#manual-BitVec___ofNat)[#]](#manual-BitVec___ofNat)32[)]](#manual-HAnd___mk) [+]](#manual-HAdd___mk) [(]](#manual-HAnd___mk)x [>>>]](#manual-HShiftRight___mk) 4 [&&&]](#manual-HAnd___mk) [1]](#manual-BitVec___ofNat)[#]](#manual-BitVec___ofNat)32[)]](#manual-HAnd___mk) [+]](#manual-HAdd___mk)
[(]](#manual-HAnd___mk)x [>>>]](#manual-HShiftRight___mk) 5 [&&&]](#manual-HAnd___mk) [1]](#manual-BitVec___ofNat)[#]](#manual-BitVec___ofNat)32[)]](#manual-HAnd___mk) [+]](#manual-HAdd___mk)
[(]](#manual-HAnd___mk)x [>>>]](#manual-HShiftRight___mk) 6 [&&&]](#manual-HAnd___mk) [1]](#manual-BitVec___ofNat)[#]](#manual-BitVec___ofNat)32[)]](#manual-HAnd___mk) [+]](#manual-HAdd___mk)
[(]](#manual-HAnd___mk)x [>>>]](#manual-HShiftRight___mk) 7 [&&&]](#manual-HAnd___mk) [1]](#manual-BitVec___ofNat)[#]](#manual-BitVec___ofNat)32[)]](#manual-HAnd___mk) [+]](#manual-HAdd___mk)
[(]](#manual-HAnd___mk)x [>>>]](#manual-HShiftRight___mk) 8 [&&&]](#manual-HAnd___mk) [1]](#manual-BitVec___ofNat)[#]](#manual-BitVec___ofNat)32[)]](#manual-HAnd___mk) [+]](#manual-HAdd___mk)
[(]](#manual-HAnd___mk)x [>>>]](#manual-HShiftRight___mk) 9 [&&&]](#manual-HAnd___mk) [1]](#manual-BitVec___ofNat)[#]](#manual-BitVec___ofNat)32[)]](#manual-HAnd___mk) [+]](#manual-HAdd___mk)
[(]](#manual-HAnd___mk)x [>>>]](#manual-HShiftRight___mk) 10 [&&&]](#manual-HAnd___mk) [1]](#manual-BitVec___ofNat)[#]](#manual-BitVec___ofNat)32[)]](#manual-HAnd___mk) [+]](#manual-HAdd___mk)
[(]](#manual-HAnd___mk)x [>>>]](#manual-HShiftRight___mk) 11 [&&&]](#manual-HAnd___mk) [1]](#manual-BitVec___ofNat)[#]](#manual-BitVec___ofNat)32[)]](#manual-HAnd___mk) [+]](#manual-HAdd___mk)
[(]](#manual-HAnd___mk)x [>>>]](#manual-HShiftRight___mk) 12 [&&&]](#manual-HAnd___mk) [1]](#manual-BitVec___ofNat)[#]](#manual-BitVec___ofNat)32[)]](#manual-HAnd___mk) [+]](#manual-HAdd___mk)
[(]](#manual-HAnd___mk)x [>>>]](#manual-HShiftRight___mk) 13 [&&&]](#manual-HAnd___mk) [1]](#manual-BitVec___ofNat)[#]](#manual-BitVec___ofNat)32[)]](#manual-HAnd___mk) [+]](#manual-HAdd___mk)
[(]](#manual-HAnd___mk)x [>>>]](#manual-HShiftRight___mk) 14 [&&&]](#manual-HAnd___mk) [1]](#manual-BitVec___ofNat)[#]](#manual-BitVec___ofNat)32[)]](#manual-HAnd___mk) [+]](#manual-HAdd___mk)
[(]](#manual-HAnd___mk)x [>>>]](#manual-HShiftRight___mk) 15 [&&&]](#manual-HAnd___mk) [1]](#manual-BitVec___ofNat)[#]](#manual-BitVec___ofNat)32[)]](#manual-HAnd___mk) [+]](#manual-HAdd___mk)
[(]](#manual-HAnd___mk)x [>>>]](#manual-HShiftRight___mk) 16 [&&&]](#manual-HAnd___mk) [1]](#manual-BitVec___ofNat)[#]](#manual-BitVec___ofNat)32[)]](#manual-HAnd___mk) [+]](#manual-HAdd___mk)
[(]](#manual-HAnd___mk)x [>>>]](#manual-HShiftRight___mk) 17 [&&&]](#manual-HAnd___mk) [1]](#manual-BitVec___ofNat)[#]](#manual-BitVec___ofNat)32[)]](#manual-HAnd___mk) [+]](#manual-HAdd___mk)
[(]](#manual-HAnd___mk)x [>>>]](#manual-HShiftRight___mk) 18 [&&&]](#manual-HAnd___mk) [1]](#manual-BitVec___ofNat)[#]](#manual-BitVec___ofNat)32[)]](#manual-HAnd___mk) [+]](#manual-HAdd___mk)
[(]](#manual-HAnd___mk)x [>>>]](#manual-HShiftRight___mk) 19 [&&&]](#manual-HAnd___mk) [1]](#manual-BitVec___ofNat)[#]](#manual-BitVec___ofNat)32[)]](#manual-HAnd___mk) [+]](#manual-HAdd___mk)
[(]](#manual-HAnd___mk)x [>>>]](#manual-HShiftRight___mk) 20 [&&&]](#manual-HAnd___mk) [1]](#manual-BitVec___ofNat)[#]](#manual-BitVec___ofNat)32[)]](#manual-HAnd___mk) [+]](#manual-HAdd___mk)
[(]](#manual-HAnd___mk)x [>>>]](#manual-HShiftRight___mk) 21 [&&&]](#manual-HAnd___mk) [1]](#manual-BitVec___ofNat)[#]](#manual-BitVec___ofNat)32[)]](#manual-HAnd___mk) [+]](#manual-HAdd___mk)
[(]](#manual-HAnd___mk)x [>>>]](#manual-HShiftRight___mk) 22 [&&&]](#manual-HAnd___mk) [1]](#manual-BitVec___ofNat)[#]](#manual-BitVec___ofNat)32[)]](#manual-HAnd___mk) [+]](#manual-HAdd___mk)
[(]](#manual-HAnd___mk)x [>>>]](#manual-HShiftRight___mk) 23 [&&&]](#manual-HAnd___mk) [1]](#manual-BitVec___ofNat)[#]](#manual-BitVec___ofNat)32[)]](#manual-HAnd___mk) [+]](#manual-HAdd___mk)
[(]](#manual-HAnd___mk)x [>>>]](#manual-HShiftRight___mk) 24 [&&&]](#manual-HAnd___mk) [1]](#manual-BitVec___ofNat)[#]](#manual-BitVec___ofNat)32[)]](#manual-HAnd___mk) [+]](#manual-HAdd___mk)
[(]](#manual-HAnd___mk)x [>>>]](#manual-HShiftRight___mk) 25 [&&&]](#manual-HAnd___mk) [1]](#manual-BitVec___ofNat)[#]](#manual-BitVec___ofNat)32[)]](#manual-HAnd___mk) [+]](#manual-HAdd___mk)
[(]](#manual-HAnd___mk)x [>>>]](#manual-HShiftRight___mk) 26 [&&&]](#manual-HAnd___mk) [1]](#manual-BitVec___ofNat)[#]](#manual-BitVec___ofNat)32[)]](#manual-HAnd___mk) [+]](#manual-HAdd___mk)
[(]](#manual-HAnd___mk)x [>>>]](#manual-HShiftRight___mk) 27 [&&&]](#manual-HAnd___mk) [1]](#manual-BitVec___ofNat)[#]](#manual-BitVec___ofNat)32[)]](#manual-HAnd___mk) [+]](#manual-HAdd___mk)
[(]](#manual-HAnd___mk)x [>>>]](#manual-HShiftRight___mk) 28 [&&&]](#manual-HAnd___mk) [1]](#manual-BitVec___ofNat)[#]](#manual-BitVec___ofNat)32[)]](#manual-HAnd___mk) [+]](#manual-HAdd___mk)
[(]](#manual-HAnd___mk)x [>>>]](#manual-HShiftRight___mk) 29 [&&&]](#manual-HAnd___mk) [1]](#manual-BitVec___ofNat)[#]](#manual-BitVec___ofNat)32[)]](#manual-HAnd___mk) [+]](#manual-HAdd___mk)
[(]](#manual-HAnd___mk)x [>>>]](#manual-HShiftRight___mk) 30 [&&&]](#manual-HAnd___mk) [1]](#manual-BitVec___ofNat)[#]](#manual-BitVec___ofNat)32[)]](#manual-HAnd___mk) [+]](#manual-HAdd___mk)
[(]](#manual-HAnd___mk)x [>>>]](#manual-HShiftRight___mk) 31 [&&&]](#manual-HAnd___mk) [1]](#manual-BitVec___ofNat)[#]](#manual-BitVec___ofNat)32[)]](#manual-HAnd___mk)
[bv_decide]](#manual-bv_decide)All goals completed! 🐙
```

### 20.5.5. API Reference {#manual-BitVec-api}

#### 20.5.5.1. Bounds {#manual-The-Lean-Language-Reference--Basic-Types--Bitvectors--API-Reference--Bounds}

def

```lean
[BitVec.intMax]](#manual-BitVec___intMax) (w : [Nat]](#manual-Nat___zero)) : [BitVec]](#manual-BitVec___ofFin) w



[BitVec.intMax]](#manual-BitVec___intMax) (w : [Nat]](#manual-Nat___zero)) : [BitVec]](#manual-BitVec___ofFin) w
```

The bitvector of width `w` that has the largest value when interpreted as an integer.

def

```lean
[BitVec.intMin]](#manual-BitVec___intMin) (w : [Nat]](#manual-Nat___zero)) : [BitVec]](#manual-BitVec___ofFin) w



[BitVec.intMin]](#manual-BitVec___intMin) (w : [Nat]](#manual-Nat___zero)) : [BitVec]](#manual-BitVec___ofFin) w
```

The bitvector of width `w` that has the smallest value when interpreted as an integer.

#### 20.5.5.2. Construction {#manual-The-Lean-Language-Reference--Basic-Types--Bitvectors--API-Reference--Construction}

def

```lean
[BitVec.fill]](#manual-BitVec___fill) (w : [Nat]](#manual-Nat___zero)) (b : [Bool](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)) : [BitVec]](#manual-BitVec___ofFin) w



[BitVec.fill]](#manual-BitVec___fill) (w : [Nat]](#manual-Nat___zero)) (b : [Bool](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)) :
  [BitVec]](#manual-BitVec___ofFin) w
```

Fills a bitvector with `w` copies of the bit `b`.

def

```lean
[BitVec.zero]](#manual-BitVec___zero) (n : [Nat]](#manual-Nat___zero)) : [BitVec]](#manual-BitVec___ofFin) n



[BitVec.zero]](#manual-BitVec___zero) (n : [Nat]](#manual-Nat___zero)) : [BitVec]](#manual-BitVec___ofFin) n
```

Returns a bitvector of size `n` where all bits are `0`.

def

```lean
[BitVec.allOnes]](#manual-BitVec___allOnes) (n : [Nat]](#manual-Nat___zero)) : [BitVec]](#manual-BitVec___ofFin) n



[BitVec.allOnes]](#manual-BitVec___allOnes) (n : [Nat]](#manual-Nat___zero)) : [BitVec]](#manual-BitVec___ofFin) n
```

Returns a bitvector of size `n` where all bits are `1`.

def

```lean
[BitVec.twoPow]](#manual-BitVec___twoPow) (w i : [Nat]](#manual-Nat___zero)) : [BitVec]](#manual-BitVec___ofFin) w



[BitVec.twoPow]](#manual-BitVec___twoPow) (w i : [Nat]](#manual-Nat___zero)) : [BitVec]](#manual-BitVec___ofFin) w
```

`twoPow w i` is the bitvector `2^i` if `i < w`, and `0` otherwise. In other words, it is 2 to the
power `i`.

From the bitwise point of view, it has the `i`th bit as `1` and all other bits as `0`.

#### 20.5.5.3. Conversion {#manual-The-Lean-Language-Reference--Basic-Types--Bitvectors--API-Reference--Conversion}

def

```lean
[BitVec.toHex]](#manual-BitVec___toHex) {n : [Nat]](#manual-Nat___zero)} (x : [BitVec]](#manual-BitVec___ofFin) n) : [String](https://lean-lang.org/doc/reference/latest/Basic-Types/Strings/#String___ofByteArray)



[BitVec.toHex]](#manual-BitVec___toHex) {n : [Nat]](#manual-Nat___zero)} (x : [BitVec]](#manual-BitVec___ofFin) n) :
  [String](https://lean-lang.org/doc/reference/latest/Basic-Types/Strings/#String___ofByteArray)
```

Converts a bitvector into a fixed-width hexadecimal number with enough digits to represent it.

If `n` is `0`, then one digit is returned. Otherwise, `⌊(n + 3) / 4⌋` digits are returned.

def

```lean
[BitVec.toInt]](#manual-BitVec___toInt) {n : [Nat]](#manual-Nat___zero)} (x : [BitVec]](#manual-BitVec___ofFin) n) : [Int]](#manual-Int___ofNat)



[BitVec.toInt]](#manual-BitVec___toInt) {n : [Nat]](#manual-Nat___zero)} (x : [BitVec]](#manual-BitVec___ofFin) n) :
  [Int]](#manual-Int___ofNat)
```

Interprets the bitvector as an integer stored in two's complement form.

def

```lean
[BitVec.toNat]](#manual-BitVec___toNat) {w : [Nat]](#manual-Nat___zero)} (x : [BitVec]](#manual-BitVec___ofFin) w) : [Nat]](#manual-Nat___zero)



[BitVec.toNat]](#manual-BitVec___toNat) {w : [Nat]](#manual-Nat___zero)} (x : [BitVec]](#manual-BitVec___ofFin) w) :
  [Nat]](#manual-Nat___zero)
```

Return the underlying `[Nat]](#manual-Nat___zero)` that represents a bitvector.

This is O(1) because `[BitVec]](#manual-BitVec___ofFin)` is a (zero-cost) wrapper around a `[Nat]](#manual-Nat___zero)`.

def

```lean
[BitVec.ofBool]](#manual-BitVec___ofBool) (b : [Bool](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)) : [BitVec]](#manual-BitVec___ofFin) 1



[BitVec.ofBool]](#manual-BitVec___ofBool) (b : [Bool](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)) : [BitVec]](#manual-BitVec___ofFin) 1
```

Turns a `[Bool](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)` into a bitvector of length `1`.

def

```lean
[BitVec.ofBoolListBE]](#manual-BitVec___ofBoolListBE) (bs : [List](https://lean-lang.org/doc/reference/latest/Basic-Types/Linked-Lists/#List___nil) [Bool](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)) : [BitVec]](#manual-BitVec___ofFin) bs.[length](https://lean-lang.org/doc/reference/latest/Basic-Types/Linked-Lists/#List___length)



[BitVec.ofBoolListBE]](#manual-BitVec___ofBoolListBE) (bs : [List](https://lean-lang.org/doc/reference/latest/Basic-Types/Linked-Lists/#List___nil) [Bool](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)) :
  [BitVec]](#manual-BitVec___ofFin) bs.[length](https://lean-lang.org/doc/reference/latest/Basic-Types/Linked-Lists/#List___length)
```

Converts a list of `[Bool](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)`s into a big-endian `[BitVec]](#manual-BitVec___ofFin)`.

def

```lean
[BitVec.ofBoolListLE]](#manual-BitVec___ofBoolListLE) (bs : [List](https://lean-lang.org/doc/reference/latest/Basic-Types/Linked-Lists/#List___nil) [Bool](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)) : [BitVec]](#manual-BitVec___ofFin) bs.[length](https://lean-lang.org/doc/reference/latest/Basic-Types/Linked-Lists/#List___length)



[BitVec.ofBoolListLE]](#manual-BitVec___ofBoolListLE) (bs : [List](https://lean-lang.org/doc/reference/latest/Basic-Types/Linked-Lists/#List___nil) [Bool](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)) :
  [BitVec]](#manual-BitVec___ofFin) bs.[length](https://lean-lang.org/doc/reference/latest/Basic-Types/Linked-Lists/#List___length)
```

Converts a list of `[Bool](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)`s into a little-endian `[BitVec]](#manual-BitVec___ofFin)`.

def

```lean
[BitVec.ofInt]](#manual-BitVec___ofInt) (n : [Nat]](#manual-Nat___zero)) (i : [Int]](#manual-Int___ofNat)) : [BitVec]](#manual-BitVec___ofFin) n



[BitVec.ofInt]](#manual-BitVec___ofInt) (n : [Nat]](#manual-Nat___zero)) (i : [Int]](#manual-Int___ofNat)) :
  [BitVec]](#manual-BitVec___ofFin) n
```

Converts an integer to its two's complement representation as a bitvector of the given width `n`,
over- and underflowing as needed.

The underlying `[Nat]](#manual-Nat___zero)` is `(2^n + (i mod 2^n)) mod 2^n`. Converting the bitvector back to an `[Int]](#manual-Int___ofNat)`
with `[BitVec.toInt]](#manual-BitVec___toInt)` results in the value `i.[bmod]](#manual-Int___bmod) (2^n)`.

def

```lean
[BitVec.ofNat]](#manual-BitVec___ofNat) (n i : [Nat]](#manual-Nat___zero)) : [BitVec]](#manual-BitVec___ofFin) n



[BitVec.ofNat]](#manual-BitVec___ofNat) (n i : [Nat]](#manual-Nat___zero)) : [BitVec]](#manual-BitVec___ofFin) n
```

The bitvector with value `i mod 2^n`.

Conventions for notations in identifiers:

- The recommended spelling of `0#n` in identifiers is `zero` (not `ofNat_zero`).
- The recommended spelling of `1#n` in identifiers is `one` (not `ofNat_one`).

def

```lean
[BitVec.ofNatLT]](#manual-BitVec___ofNatLT) {w : [Nat]](#manual-Nat___zero)} (i : [Nat]](#manual-Nat___zero)) (p : i [<]](#manual-LT___mk) 2 [^]](#manual-HPow___mk) w) : [BitVec]](#manual-BitVec___ofFin) w



[BitVec.ofNatLT]](#manual-BitVec___ofNatLT) {w : [Nat]](#manual-Nat___zero)} (i : [Nat]](#manual-Nat___zero))
  (p : i [<]](#manual-LT___mk) 2 [^]](#manual-HPow___mk) w) : [BitVec]](#manual-BitVec___ofFin) w
```

The `[BitVec]](#manual-BitVec___ofFin)` with value `i`, given a proof that `i < 2^w`.

def

```lean
[BitVec.cast]](#manual-BitVec___cast) {n m : [Nat]](#manual-Nat___zero)} (eq : n [=]](#manual-Eq___refl) m) (x : [BitVec]](#manual-BitVec___ofFin) n) : [BitVec]](#manual-BitVec___ofFin) m



[BitVec.cast]](#manual-BitVec___cast) {n m : [Nat]](#manual-Nat___zero)} (eq : n [=]](#manual-Eq___refl) m)
  (x : [BitVec]](#manual-BitVec___ofFin) n) : [BitVec]](#manual-BitVec___ofFin) m
```

If two natural numbers `n` and `m` are equal, then a bitvector of width `n` is also a bitvector of
width `m`.

Using `x.[cast]](#manual-BitVec___cast) eq` should be preferred over `eq ▸ x` because there are special-purpose `[simp]](#manual-simp)` lemmas
that can more consistently simplify `[BitVec.cast]](#manual-BitVec___cast)` away.

#### 20.5.5.4. Comparisons {#manual-The-Lean-Language-Reference--Basic-Types--Bitvectors--API-Reference--Comparisons}

def

```lean
[BitVec.ule]](#manual-BitVec___ule) {n : [Nat]](#manual-Nat___zero)} (x y : [BitVec]](#manual-BitVec___ofFin) n) : [Bool](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)



[BitVec.ule]](#manual-BitVec___ule) {n : [Nat]](#manual-Nat___zero)} (x y : [BitVec]](#manual-BitVec___ofFin) n) :
  [Bool](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)
```

Unsigned less-than-or-equal-to for bitvectors.

SMT-LIB name: `bvule`.

def

```lean
[BitVec.sle]](#manual-BitVec___sle) {n : [Nat]](#manual-Nat___zero)} (x y : [BitVec]](#manual-BitVec___ofFin) n) : [Bool](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)



[BitVec.sle]](#manual-BitVec___sle) {n : [Nat]](#manual-Nat___zero)} (x y : [BitVec]](#manual-BitVec___ofFin) n) :
  [Bool](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)
```

Signed less-than-or-equal-to for bitvectors.

SMT-LIB name: `bvsle`.

def

```lean
[BitVec.ult]](#manual-BitVec___ult) {n : [Nat]](#manual-Nat___zero)} (x y : [BitVec]](#manual-BitVec___ofFin) n) : [Bool](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)



[BitVec.ult]](#manual-BitVec___ult) {n : [Nat]](#manual-Nat___zero)} (x y : [BitVec]](#manual-BitVec___ofFin) n) :
  [Bool](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)
```

Unsigned less-than for bitvectors.

SMT-LIB name: `bvult`.

def

```lean
[BitVec.slt]](#manual-BitVec___slt) {n : [Nat]](#manual-Nat___zero)} (x y : [BitVec]](#manual-BitVec___ofFin) n) : [Bool](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)



[BitVec.slt]](#manual-BitVec___slt) {n : [Nat]](#manual-Nat___zero)} (x y : [BitVec]](#manual-BitVec___ofFin) n) :
  [Bool](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)
```

Signed less-than for bitvectors.

SMT-LIB name: `bvslt`.

Examples:

- `[BitVec.slt]](#manual-BitVec___slt) 6#4 7 = [true](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)`
- `[BitVec.slt]](#manual-BitVec___slt) 7#4 8 = [false](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)`

def

```lean
[BitVec.decEq]](#manual-BitVec___decEq) {w : [Nat]](#manual-Nat___zero)} (x y : [BitVec]](#manual-BitVec___ofFin) w) : [Decidable]](#manual-Decidable___isFalse) [(]](#manual-Eq___refl)x [=]](#manual-Eq___refl) y[)]](#manual-Eq___refl)



[BitVec.decEq]](#manual-BitVec___decEq) {w : [Nat]](#manual-Nat___zero)} (x y : [BitVec]](#manual-BitVec___ofFin) w) :
  [Decidable]](#manual-Decidable___isFalse) [(]](#manual-Eq___refl)x [=]](#manual-Eq___refl) y[)]](#manual-Eq___refl)
```

Bitvectors have decidable equality.

This should be used via the instance `[DecidableEq]](#manual-DecidableEq) ([BitVec]](#manual-BitVec___ofFin) w)`.

#### 20.5.5.5. Hashing {#manual-The-Lean-Language-Reference--Basic-Types--Bitvectors--API-Reference--Hashing}

def

```lean
[BitVec.hash]](#manual-BitVec___hash) {n : [Nat]](#manual-Nat___zero)} (bv : [BitVec]](#manual-BitVec___ofFin) n) : [UInt64]](#manual-UInt64___ofBitVec)



[BitVec.hash]](#manual-BitVec___hash) {n : [Nat]](#manual-Nat___zero)} (bv : [BitVec]](#manual-BitVec___ofFin) n) :
  [UInt64]](#manual-UInt64___ofBitVec)
```

Computes a hash of a bitvector, combining 64-bit words using `[mixHash]](#manual-mixHash)`.

#### 20.5.5.6. Sequence Operations {#manual-The-Lean-Language-Reference--Basic-Types--Bitvectors--API-Reference--Sequence-Operations}

These operations treat bitvectors as sequences of bits, rather than as encodings of numbers.

def

```lean
[BitVec.nil]](#manual-BitVec___nil) : [BitVec]](#manual-BitVec___ofFin) 0



[BitVec.nil]](#manual-BitVec___nil) : [BitVec]](#manual-BitVec___ofFin) 0
```

The empty bitvector.

def

```lean
[BitVec.cons]](#manual-BitVec___cons) {n : [Nat]](#manual-Nat___zero)} (msb : [Bool](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)) (lsbs : [BitVec]](#manual-BitVec___ofFin) n) : [BitVec]](#manual-BitVec___ofFin) [(]](#manual-HAdd___mk)n [+]](#manual-HAdd___mk) 1[)]](#manual-HAdd___mk)



[BitVec.cons]](#manual-BitVec___cons) {n : [Nat]](#manual-Nat___zero)} (msb : [Bool](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false))
  (lsbs : [BitVec]](#manual-BitVec___ofFin) n) : [BitVec]](#manual-BitVec___ofFin) [(]](#manual-HAdd___mk)n [+]](#manual-HAdd___mk) 1[)]](#manual-HAdd___mk)
```

Prepends a single bit to the front of a bitvector, using big-endian order (see `append`).

The new bit is the most significant bit.

def

```lean
[BitVec.concat]](#manual-BitVec___concat) {n : [Nat]](#manual-Nat___zero)} (msbs : [BitVec]](#manual-BitVec___ofFin) n) (lsb : [Bool](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)) : [BitVec]](#manual-BitVec___ofFin) [(]](#manual-HAdd___mk)n [+]](#manual-HAdd___mk) 1[)]](#manual-HAdd___mk)



[BitVec.concat]](#manual-BitVec___concat) {n : [Nat]](#manual-Nat___zero)} (msbs : [BitVec]](#manual-BitVec___ofFin) n)
  (lsb : [Bool](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)) : [BitVec]](#manual-BitVec___ofFin) [(]](#manual-HAdd___mk)n [+]](#manual-HAdd___mk) 1[)]](#manual-HAdd___mk)
```

Append a single bit to the end of a bitvector, using big endian order (see `append`).
That is, the new bit is the least significant bit.

def

```lean
[BitVec.shiftConcat]](#manual-BitVec___shiftConcat) {n : [Nat]](#manual-Nat___zero)} (x : [BitVec]](#manual-BitVec___ofFin) n) (b : [Bool](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)) : [BitVec]](#manual-BitVec___ofFin) n



[BitVec.shiftConcat]](#manual-BitVec___shiftConcat) {n : [Nat]](#manual-Nat___zero)}
  (x : [BitVec]](#manual-BitVec___ofFin) n) (b : [Bool](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)) : [BitVec]](#manual-BitVec___ofFin) n
```

Shifts all bits of `x` to the left by `1` and sets the least significant bit to `b`.

This is a non-dependent version of `[BitVec.concat]](#manual-BitVec___concat)` that does not change the total bitwidth.

def

```lean
[BitVec.truncate]](#manual-BitVec___truncate) {w : [Nat]](#manual-Nat___zero)} (v : [Nat]](#manual-Nat___zero)) (x : [BitVec]](#manual-BitVec___ofFin) w) : [BitVec]](#manual-BitVec___ofFin) v



[BitVec.truncate]](#manual-BitVec___truncate) {w : [Nat]](#manual-Nat___zero)} (v : [Nat]](#manual-Nat___zero))
  (x : [BitVec]](#manual-BitVec___ofFin) w) : [BitVec]](#manual-BitVec___ofFin) v
```

Transforms a bitvector of length `w` into a bitvector of length `v`, padding with `0` as needed.

The specific behavior depends on the relationship between the starting width `w` and the final width
`v`:

- If `v > w`, it is zero-extended; the high bits are padded with zeroes until the bitvector has `v`
  bits.
- If `v = w`, the bitvector is returned unchanged.
- If `v < w`, the high bits are truncated.

`[BitVec.setWidth]](#manual-BitVec___setWidth)`, `[BitVec.zeroExtend]](#manual-BitVec___zeroExtend)`, and `[BitVec.truncate]](#manual-BitVec___truncate)` are aliases for this operation.

SMT-LIB name: `zero_extend`.

def

```lean
[BitVec.setWidth]](#manual-BitVec___setWidth) {w : [Nat]](#manual-Nat___zero)} (v : [Nat]](#manual-Nat___zero)) (x : [BitVec]](#manual-BitVec___ofFin) w) : [BitVec]](#manual-BitVec___ofFin) v



[BitVec.setWidth]](#manual-BitVec___setWidth) {w : [Nat]](#manual-Nat___zero)} (v : [Nat]](#manual-Nat___zero))
  (x : [BitVec]](#manual-BitVec___ofFin) w) : [BitVec]](#manual-BitVec___ofFin) v
```

Transforms a bitvector of length `w` into a bitvector of length `v`, padding with `0` as needed.

The specific behavior depends on the relationship between the starting width `w` and the final width
`v`:

- If `v > w`, it is zero-extended; the high bits are padded with zeroes until the bitvector has `v`
  bits.
- If `v = w`, the bitvector is returned unchanged.
- If `v < w`, the high bits are truncated.

`[BitVec.setWidth]](#manual-BitVec___setWidth)`, `[BitVec.zeroExtend]](#manual-BitVec___zeroExtend)`, and `[BitVec.truncate]](#manual-BitVec___truncate)` are aliases for this operation.

SMT-LIB name: `zero_extend`.

def

```lean
[BitVec.setWidth']](#manual-BitVec___setWidth___) {n w : [Nat]](#manual-Nat___zero)} (le : n [≤]](#manual-LE___mk) w) (x : [BitVec]](#manual-BitVec___ofFin) n) : [BitVec]](#manual-BitVec___ofFin) w



[BitVec.setWidth']](#manual-BitVec___setWidth___) {n w : [Nat]](#manual-Nat___zero)} (le : n [≤]](#manual-LE___mk) w)
  (x : [BitVec]](#manual-BitVec___ofFin) n) : [BitVec]](#manual-BitVec___ofFin) w
```

Increases the width of a bitvector to one that is at least as large by zero-extending it.

This is a constant-time operation because the underlying `[Nat]](#manual-Nat___zero)` is unmodified; because the new width
is at least as large as the old one, no overflow is possible.

def

```lean
[BitVec.append]](#manual-BitVec___append) {n m : [Nat]](#manual-Nat___zero)} (msbs : [BitVec]](#manual-BitVec___ofFin) n) (lsbs : [BitVec]](#manual-BitVec___ofFin) m) :
  [BitVec]](#manual-BitVec___ofFin) [(]](#manual-HAdd___mk)n [+]](#manual-HAdd___mk) m[)]](#manual-HAdd___mk)



[BitVec.append]](#manual-BitVec___append) {n m : [Nat]](#manual-Nat___zero)}
  (msbs : [BitVec]](#manual-BitVec___ofFin) n) (lsbs : [BitVec]](#manual-BitVec___ofFin) m) :
  [BitVec]](#manual-BitVec___ofFin) [(]](#manual-HAdd___mk)n [+]](#manual-HAdd___mk) m[)]](#manual-HAdd___mk)
```

Concatenates two bitvectors using the “big-endian” convention that the more significant
input is on the left. Usually accessed via the `++` operator.

SMT-LIB name: `concat`.

Example:

- `0xAB#8 ++ 0xCD#8 = 0xABCD#16`.

def

```lean
[BitVec.replicate]](#manual-BitVec___replicate) {w : [Nat]](#manual-Nat___zero)} (i : [Nat]](#manual-Nat___zero)) : [BitVec]](#manual-BitVec___ofFin) w → [BitVec]](#manual-BitVec___ofFin) [(]](#manual-HMul___mk)w [*]](#manual-HMul___mk) i[)]](#manual-HMul___mk)



[BitVec.replicate]](#manual-BitVec___replicate) {w : [Nat]](#manual-Nat___zero)} (i : [Nat]](#manual-Nat___zero)) :
  [BitVec]](#manual-BitVec___ofFin) w → [BitVec]](#manual-BitVec___ofFin) [(]](#manual-HMul___mk)w [*]](#manual-HMul___mk) i[)]](#manual-HMul___mk)
```

Concatenates `i` copies of `x` into a new vector of length `w * i`.

def

```lean
[BitVec.reverse]](#manual-BitVec___reverse) {w : [Nat]](#manual-Nat___zero)} : [BitVec]](#manual-BitVec___ofFin) w → [BitVec]](#manual-BitVec___ofFin) w



[BitVec.reverse]](#manual-BitVec___reverse) {w : [Nat]](#manual-Nat___zero)} :
  [BitVec]](#manual-BitVec___ofFin) w → [BitVec]](#manual-BitVec___ofFin) w
```

Reverses the bits in a bitvector.

def

```lean
[BitVec.rotateLeft]](#manual-BitVec___rotateLeft) {w : [Nat]](#manual-Nat___zero)} (x : [BitVec]](#manual-BitVec___ofFin) w) (n : [Nat]](#manual-Nat___zero)) : [BitVec]](#manual-BitVec___ofFin) w



[BitVec.rotateLeft]](#manual-BitVec___rotateLeft) {w : [Nat]](#manual-Nat___zero)} (x : [BitVec]](#manual-BitVec___ofFin) w)
  (n : [Nat]](#manual-Nat___zero)) : [BitVec]](#manual-BitVec___ofFin) w
```

Rotates the bits in a bitvector to the left.

All the bits of `x` are shifted to higher positions, with the top `n` bits wrapping around to fill
the vacated low bits.

SMT-LIB name: `[rotate_left]](#manual-rotate_left)`, except this operator uses a `[Nat]](#manual-Nat___zero)` shift amount.

Example:

- `(0b0011#4).[rotateLeft]](#manual-BitVec___rotateLeft) 3 = 0b1001`

def

```lean
[BitVec.rotateRight]](#manual-BitVec___rotateRight) {w : [Nat]](#manual-Nat___zero)} (x : [BitVec]](#manual-BitVec___ofFin) w) (n : [Nat]](#manual-Nat___zero)) : [BitVec]](#manual-BitVec___ofFin) w



[BitVec.rotateRight]](#manual-BitVec___rotateRight) {w : [Nat]](#manual-Nat___zero)}
  (x : [BitVec]](#manual-BitVec___ofFin) w) (n : [Nat]](#manual-Nat___zero)) : [BitVec]](#manual-BitVec___ofFin) w
```

Rotates the bits in a bitvector to the right.

All the bits of `x` are shifted to lower positions, with the bottom `n` bits wrapping around to fill
the vacated high bits.

SMT-LIB name: `[rotate_right]](#manual-rotate_right)`, except this operator uses a `[Nat]](#manual-Nat___zero)` shift amount.

Example:

- `rotateRight 0b01001#5 1 = 0b10100`

##### 20.5.5.6.1. Bit Extraction {#manual-The-Lean-Language-Reference--Basic-Types--Bitvectors--API-Reference--Sequence-Operations--Bit-Extraction}

def

```lean
[BitVec.msb]](#manual-BitVec___msb) {n : [Nat]](#manual-Nat___zero)} (x : [BitVec]](#manual-BitVec___ofFin) n) : [Bool](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)



[BitVec.msb]](#manual-BitVec___msb) {n : [Nat]](#manual-Nat___zero)} (x : [BitVec]](#manual-BitVec___ofFin) n) : [Bool](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)
```

Returns the most significant bit in a bitvector.

def

```lean
[BitVec.getMsbD]](#manual-BitVec___getMsbD) {w : [Nat]](#manual-Nat___zero)} (x : [BitVec]](#manual-BitVec___ofFin) w) (i : [Nat]](#manual-Nat___zero)) : [Bool](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)



[BitVec.getMsbD]](#manual-BitVec___getMsbD) {w : [Nat]](#manual-Nat___zero)} (x : [BitVec]](#manual-BitVec___ofFin) w)
  (i : [Nat]](#manual-Nat___zero)) : [Bool](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)
```

Returns the `i`th most significant bit, or `[false](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)` if `i ≥ w`.

def

```lean
[BitVec.getMsb]](#manual-BitVec___getMsb) {w : [Nat]](#manual-Nat___zero)} (x : [BitVec]](#manual-BitVec___ofFin) w) (i : [Fin]](#manual-Fin___mk) w) : [Bool](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)



[BitVec.getMsb]](#manual-BitVec___getMsb) {w : [Nat]](#manual-Nat___zero)} (x : [BitVec]](#manual-BitVec___ofFin) w)
  (i : [Fin]](#manual-Fin___mk) w) : [Bool](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)
```

Returns the `i`th most significant bit.

def

```lean
[BitVec.getMsb?]](#manual-BitVec___getMsb___) {w : [Nat]](#manual-Nat___zero)} (x : [BitVec]](#manual-BitVec___ofFin) w) (i : [Nat]](#manual-Nat___zero)) : [Option](https://lean-lang.org/doc/reference/latest/Basic-Types/Optional-Values/#Option___none) [Bool](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)



[BitVec.getMsb?]](#manual-BitVec___getMsb___) {w : [Nat]](#manual-Nat___zero)} (x : [BitVec]](#manual-BitVec___ofFin) w)
  (i : [Nat]](#manual-Nat___zero)) : [Option](https://lean-lang.org/doc/reference/latest/Basic-Types/Optional-Values/#Option___none) [Bool](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)
```

Returns the `i`th most significant bit or `[none](https://lean-lang.org/doc/reference/latest/Basic-Types/Optional-Values/#Option___none)` if `i ≥ w`.

def

```lean
[BitVec.getLsbD]](#manual-BitVec___getLsbD) {w : [Nat]](#manual-Nat___zero)} (x : [BitVec]](#manual-BitVec___ofFin) w) (i : [Nat]](#manual-Nat___zero)) : [Bool](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)



[BitVec.getLsbD]](#manual-BitVec___getLsbD) {w : [Nat]](#manual-Nat___zero)} (x : [BitVec]](#manual-BitVec___ofFin) w)
  (i : [Nat]](#manual-Nat___zero)) : [Bool](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)
```

Returns the `i`th least significant bit or `[false](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)` if `i ≥ w`.

def

```lean
[BitVec.getLsb]](#manual-BitVec___getLsb) {w : [Nat]](#manual-Nat___zero)} (x : [BitVec]](#manual-BitVec___ofFin) w) (i : [Fin]](#manual-Fin___mk) w) : [Bool](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)



[BitVec.getLsb]](#manual-BitVec___getLsb) {w : [Nat]](#manual-Nat___zero)} (x : [BitVec]](#manual-BitVec___ofFin) w)
  (i : [Fin]](#manual-Fin___mk) w) : [Bool](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)
```

Returns the `i`th least significant bit.

def

```lean
[BitVec.getLsb?]](#manual-BitVec___getLsb___) {w : [Nat]](#manual-Nat___zero)} (x : [BitVec]](#manual-BitVec___ofFin) w) (i : [Nat]](#manual-Nat___zero)) : [Option](https://lean-lang.org/doc/reference/latest/Basic-Types/Optional-Values/#Option___none) [Bool](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)



[BitVec.getLsb?]](#manual-BitVec___getLsb___) {w : [Nat]](#manual-Nat___zero)} (x : [BitVec]](#manual-BitVec___ofFin) w)
  (i : [Nat]](#manual-Nat___zero)) : [Option](https://lean-lang.org/doc/reference/latest/Basic-Types/Optional-Values/#Option___none) [Bool](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)
```

Returns the `i`th least significant bit, or `[none](https://lean-lang.org/doc/reference/latest/Basic-Types/Optional-Values/#Option___none)` if `i ≥ w`.

def

```lean
[BitVec.extractLsb]](#manual-BitVec___extractLsb) {n : [Nat]](#manual-Nat___zero)} (hi lo : [Nat]](#manual-Nat___zero)) (x : [BitVec]](#manual-BitVec___ofFin) n) :
  [BitVec]](#manual-BitVec___ofFin) [(]](#manual-HAdd___mk)hi [-]](#manual-HSub___mk) lo [+]](#manual-HAdd___mk) 1[)]](#manual-HAdd___mk)



[BitVec.extractLsb]](#manual-BitVec___extractLsb) {n : [Nat]](#manual-Nat___zero)} (hi lo : [Nat]](#manual-Nat___zero))
  (x : [BitVec]](#manual-BitVec___ofFin) n) : [BitVec]](#manual-BitVec___ofFin) [(]](#manual-HAdd___mk)hi [-]](#manual-HSub___mk) lo [+]](#manual-HAdd___mk) 1[)]](#manual-HAdd___mk)
```

Extracts the bits from `hi` down to `lo` (both inclusive) from a bitvector, which is implicitly
zero-extended if necessary.

The resulting bitvector has size `hi - lo + 1`.

SMT-LIB name: `extract`.

def

```lean
[BitVec.extractLsb']](#manual-BitVec___extractLsb___) {n : [Nat]](#manual-Nat___zero)} (start len : [Nat]](#manual-Nat___zero)) (x : [BitVec]](#manual-BitVec___ofFin) n) :
  [BitVec]](#manual-BitVec___ofFin) len



[BitVec.extractLsb']](#manual-BitVec___extractLsb___) {n : [Nat]](#manual-Nat___zero)}
  (start len : [Nat]](#manual-Nat___zero)) (x : [BitVec]](#manual-BitVec___ofFin) n) :
  [BitVec]](#manual-BitVec___ofFin) len
```

Extracts the bits `start` to `start + len - 1` from a bitvector of size `n` to yield a
new bitvector of size `len`. If `start + len > n`, then the bitvector is zero-extended.

#### 20.5.5.7. Bitwise Operators {#manual-The-Lean-Language-Reference--Basic-Types--Bitvectors--API-Reference--Bitwise-Operators}

These operators modify the individual bits of one or more bitvectors.

def

```lean
[BitVec.and]](#manual-BitVec___and) {n : [Nat]](#manual-Nat___zero)} (x y : [BitVec]](#manual-BitVec___ofFin) n) : [BitVec]](#manual-BitVec___ofFin) n



[BitVec.and]](#manual-BitVec___and) {n : [Nat]](#manual-Nat___zero)} (x y : [BitVec]](#manual-BitVec___ofFin) n) :
  [BitVec]](#manual-BitVec___ofFin) n
```

Bitwise and for bitvectors. Usually accessed via the `&&&` operator.

SMT-LIB name: `bvand`.

Example:

- `0b1010#4 &&& 0b0110#4 = 0b0010#4`

def

```lean
[BitVec.or]](#manual-BitVec___or) {n : [Nat]](#manual-Nat___zero)} (x y : [BitVec]](#manual-BitVec___ofFin) n) : [BitVec]](#manual-BitVec___ofFin) n



[BitVec.or]](#manual-BitVec___or) {n : [Nat]](#manual-Nat___zero)} (x y : [BitVec]](#manual-BitVec___ofFin) n) :
  [BitVec]](#manual-BitVec___ofFin) n
```

Bitwise or for bitvectors. Usually accessed via the `|||` operator.

SMT-LIB name: `bvor`.

Example:

- `0b1010#4 ||| 0b0110#4 = 0b1110#4`

def

```lean
[BitVec.not]](#manual-BitVec___not) {n : [Nat]](#manual-Nat___zero)} (x : [BitVec]](#manual-BitVec___ofFin) n) : [BitVec]](#manual-BitVec___ofFin) n



[BitVec.not]](#manual-BitVec___not) {n : [Nat]](#manual-Nat___zero)} (x : [BitVec]](#manual-BitVec___ofFin) n) :
  [BitVec]](#manual-BitVec___ofFin) n
```

Bitwise complement for bitvectors. Usually accessed via the `~~~` prefix operator.

SMT-LIB name: `bvnot`.

Example:

- `~~~(0b0101#4) == 0b1010`

def

```lean
[BitVec.xor]](#manual-BitVec___xor) {n : [Nat]](#manual-Nat___zero)} (x y : [BitVec]](#manual-BitVec___ofFin) n) : [BitVec]](#manual-BitVec___ofFin) n



[BitVec.xor]](#manual-BitVec___xor) {n : [Nat]](#manual-Nat___zero)} (x y : [BitVec]](#manual-BitVec___ofFin) n) :
  [BitVec]](#manual-BitVec___ofFin) n
```

Bitwise xor for bitvectors. Usually accessed via the `^^^` operator.

SMT-LIB name: `bvxor`.

Example:

- `0b1010#4 ^^^ 0b0110#4 = 0b1100#4`

def

```lean
[BitVec.zeroExtend]](#manual-BitVec___zeroExtend) {w : [Nat]](#manual-Nat___zero)} (v : [Nat]](#manual-Nat___zero)) (x : [BitVec]](#manual-BitVec___ofFin) w) : [BitVec]](#manual-BitVec___ofFin) v



[BitVec.zeroExtend]](#manual-BitVec___zeroExtend) {w : [Nat]](#manual-Nat___zero)} (v : [Nat]](#manual-Nat___zero))
  (x : [BitVec]](#manual-BitVec___ofFin) w) : [BitVec]](#manual-BitVec___ofFin) v
```

Transforms a bitvector of length `w` into a bitvector of length `v`, padding with `0` as needed.

The specific behavior depends on the relationship between the starting width `w` and the final width
`v`:

- If `v > w`, it is zero-extended; the high bits are padded with zeroes until the bitvector has `v`
  bits.
- If `v = w`, the bitvector is returned unchanged.
- If `v < w`, the high bits are truncated.

`[BitVec.setWidth]](#manual-BitVec___setWidth)`, `[BitVec.zeroExtend]](#manual-BitVec___zeroExtend)`, and `[BitVec.truncate]](#manual-BitVec___truncate)` are aliases for this operation.

SMT-LIB name: `zero_extend`.

def

```lean
[BitVec.signExtend]](#manual-BitVec___signExtend) {w : [Nat]](#manual-Nat___zero)} (v : [Nat]](#manual-Nat___zero)) (x : [BitVec]](#manual-BitVec___ofFin) w) : [BitVec]](#manual-BitVec___ofFin) v



[BitVec.signExtend]](#manual-BitVec___signExtend) {w : [Nat]](#manual-Nat___zero)} (v : [Nat]](#manual-Nat___zero))
  (x : [BitVec]](#manual-BitVec___ofFin) w) : [BitVec]](#manual-BitVec___ofFin) v
```

Transforms a bitvector of length `w` into a bitvector of length `v`, padding as needed with the most
significant bit's value.

If `x` is an empty bitvector, then the sign is treated as zero.

SMT-LIB name: `sign_extend`.

def

```lean
[BitVec.ushiftRight]](#manual-BitVec___ushiftRight) {n : [Nat]](#manual-Nat___zero)} (x : [BitVec]](#manual-BitVec___ofFin) n) (s : [Nat]](#manual-Nat___zero)) : [BitVec]](#manual-BitVec___ofFin) n



[BitVec.ushiftRight]](#manual-BitVec___ushiftRight) {n : [Nat]](#manual-Nat___zero)}
  (x : [BitVec]](#manual-BitVec___ofFin) n) (s : [Nat]](#manual-Nat___zero)) : [BitVec]](#manual-BitVec___ofFin) n
```

Shifts a bitvector to the right. This is a logical right shift - the high bits are filled with
zeros.

As a numeric operation, this is equivalent to `x / 2^s`, rounding down.

SMT-LIB name: `bvlshr` except this operator uses a `[Nat]](#manual-Nat___zero)` shift value.

def

```lean
[BitVec.sshiftRight]](#manual-BitVec___sshiftRight) {n : [Nat]](#manual-Nat___zero)} (x : [BitVec]](#manual-BitVec___ofFin) n) (s : [Nat]](#manual-Nat___zero)) : [BitVec]](#manual-BitVec___ofFin) n



[BitVec.sshiftRight]](#manual-BitVec___sshiftRight) {n : [Nat]](#manual-Nat___zero)}
  (x : [BitVec]](#manual-BitVec___ofFin) n) (s : [Nat]](#manual-Nat___zero)) : [BitVec]](#manual-BitVec___ofFin) n
```

Shifts a bitvector to the right. This is an arithmetic right shift - the high bits are filled with
most significant bit's value.

As a numeric operation, this is equivalent to `x.[toInt]](#manual-BitVec___toInt) >>> s`.

SMT-LIB name: `bvashr` except this operator uses a `[Nat]](#manual-Nat___zero)` shift value.

def

```lean
[BitVec.sshiftRight']](#manual-BitVec___sshiftRight___) {n m : [Nat]](#manual-Nat___zero)} (a : [BitVec]](#manual-BitVec___ofFin) n) (s : [BitVec]](#manual-BitVec___ofFin) m) : [BitVec]](#manual-BitVec___ofFin) n



[BitVec.sshiftRight']](#manual-BitVec___sshiftRight___) {n m : [Nat]](#manual-Nat___zero)}
  (a : [BitVec]](#manual-BitVec___ofFin) n) (s : [BitVec]](#manual-BitVec___ofFin) m) : [BitVec]](#manual-BitVec___ofFin) n
```

Shifts a bitvector to the right. This is an arithmetic right shift - the high bits are filled with
most significant bit's value.

As a numeric operation, this is equivalent to `a.[toInt]](#manual-BitVec___toInt) >>> s.[toNat]](#manual-BitVec___toNat)`.

SMT-LIB name: `bvashr`.

def

```lean
[BitVec.shiftLeft]](#manual-BitVec___shiftLeft) {n : [Nat]](#manual-Nat___zero)} (x : [BitVec]](#manual-BitVec___ofFin) n) (s : [Nat]](#manual-Nat___zero)) : [BitVec]](#manual-BitVec___ofFin) n



[BitVec.shiftLeft]](#manual-BitVec___shiftLeft) {n : [Nat]](#manual-Nat___zero)} (x : [BitVec]](#manual-BitVec___ofFin) n)
  (s : [Nat]](#manual-Nat___zero)) : [BitVec]](#manual-BitVec___ofFin) n
```

Shifts a bitvector to the left. The low bits are filled with zeros. As a numeric operation, this is
equivalent to `x * 2^s`, modulo `2^n`.

SMT-LIB name: `bvshl` except this operator uses a `[Nat]](#manual-Nat___zero)` shift value.

def

```lean
[BitVec.shiftLeftZeroExtend]](#manual-BitVec___shiftLeftZeroExtend) {w : [Nat]](#manual-Nat___zero)} (msbs : [BitVec]](#manual-BitVec___ofFin) w) (m : [Nat]](#manual-Nat___zero)) :
  [BitVec]](#manual-BitVec___ofFin) [(]](#manual-HAdd___mk)w [+]](#manual-HAdd___mk) m[)]](#manual-HAdd___mk)



[BitVec.shiftLeftZeroExtend]](#manual-BitVec___shiftLeftZeroExtend) {w : [Nat]](#manual-Nat___zero)}
  (msbs : [BitVec]](#manual-BitVec___ofFin) w) (m : [Nat]](#manual-Nat___zero)) :
  [BitVec]](#manual-BitVec___ofFin) [(]](#manual-HAdd___mk)w [+]](#manual-HAdd___mk) m[)]](#manual-HAdd___mk)
```

Returns `zeroExtend (w+n) x <<< n` without needing to compute `x % 2^(2+n)`.

#### 20.5.5.8. Arithmetic {#manual-The-Lean-Language-Reference--Basic-Types--Bitvectors--API-Reference--Arithmetic}

These operators treat bitvectors as numbers.
Some operations are signed, while others are unsigned.
Because bitvectors are understood as two's complement numbers, addition, subtraction and multiplication coincide for the signed and unsigned interpretations.

def

```lean
[BitVec.add]](#manual-BitVec___add) {n : [Nat]](#manual-Nat___zero)} (x y : [BitVec]](#manual-BitVec___ofFin) n) : [BitVec]](#manual-BitVec___ofFin) n



[BitVec.add]](#manual-BitVec___add) {n : [Nat]](#manual-Nat___zero)} (x y : [BitVec]](#manual-BitVec___ofFin) n) :
  [BitVec]](#manual-BitVec___ofFin) n
```

Adds two bitvectors. This can be interpreted as either signed or unsigned addition modulo `2^n`.
Usually accessed via the `+` operator.

SMT-LIB name: `bvadd`.

def

```lean
[BitVec.sub]](#manual-BitVec___sub) {n : [Nat]](#manual-Nat___zero)} (x y : [BitVec]](#manual-BitVec___ofFin) n) : [BitVec]](#manual-BitVec___ofFin) n



[BitVec.sub]](#manual-BitVec___sub) {n : [Nat]](#manual-Nat___zero)} (x y : [BitVec]](#manual-BitVec___ofFin) n) :
  [BitVec]](#manual-BitVec___ofFin) n
```

Subtracts one bitvector from another. This can be interpreted as either signed or unsigned subtraction
modulo `2^n`. Usually accessed via the `-` operator.

def

```lean
[BitVec.mul]](#manual-BitVec___mul) {n : [Nat]](#manual-Nat___zero)} (x y : [BitVec]](#manual-BitVec___ofFin) n) : [BitVec]](#manual-BitVec___ofFin) n



[BitVec.mul]](#manual-BitVec___mul) {n : [Nat]](#manual-Nat___zero)} (x y : [BitVec]](#manual-BitVec___ofFin) n) :
  [BitVec]](#manual-BitVec___ofFin) n
```

Multiplies two bitvectors. This can be interpreted as either signed or unsigned multiplication
modulo `2^n`. Usually accessed via the `*` operator.

SMT-LIB name: `bvmul`.

##### 20.5.5.8.1. Unsigned Operations {#manual-The-Lean-Language-Reference--Basic-Types--Bitvectors--API-Reference--Arithmetic--Unsigned-Operations}

def

```lean
[BitVec.udiv]](#manual-BitVec___udiv) {n : [Nat]](#manual-Nat___zero)} (x y : [BitVec]](#manual-BitVec___ofFin) n) : [BitVec]](#manual-BitVec___ofFin) n



[BitVec.udiv]](#manual-BitVec___udiv) {n : [Nat]](#manual-Nat___zero)} (x y : [BitVec]](#manual-BitVec___ofFin) n) :
  [BitVec]](#manual-BitVec___ofFin) n
```

Unsigned division of bitvectors using the Lean convention where division by zero returns zero.
Usually accessed via the `/` operator.

def

```lean
[BitVec.smtUDiv]](#manual-BitVec___smtUDiv) {n : [Nat]](#manual-Nat___zero)} (x y : [BitVec]](#manual-BitVec___ofFin) n) : [BitVec]](#manual-BitVec___ofFin) n



[BitVec.smtUDiv]](#manual-BitVec___smtUDiv) {n : [Nat]](#manual-Nat___zero)}
  (x y : [BitVec]](#manual-BitVec___ofFin) n) : [BitVec]](#manual-BitVec___ofFin) n
```

Unsigned division of bitvectors using the
[SMT-LIB convention](http://smtlib.cs.uiowa.edu/theories-FixedSizeBitVectors.shtml),
where division by zero returns `BitVector.allOnes n`.

SMT-LIB name: `bvudiv`.

def

```lean
[BitVec.umod]](#manual-BitVec___umod) {n : [Nat]](#manual-Nat___zero)} (x y : [BitVec]](#manual-BitVec___ofFin) n) : [BitVec]](#manual-BitVec___ofFin) n



[BitVec.umod]](#manual-BitVec___umod) {n : [Nat]](#manual-Nat___zero)} (x y : [BitVec]](#manual-BitVec___ofFin) n) :
  [BitVec]](#manual-BitVec___ofFin) n
```

Unsigned modulo for bitvectors. Usually accessed via the `%` operator.

SMT-LIB name: `bvurem`.

def

```lean
[BitVec.uaddOverflow]](#manual-BitVec___uaddOverflow) {w : [Nat]](#manual-Nat___zero)} (x y : [BitVec]](#manual-BitVec___ofFin) w) : [Bool](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)



[BitVec.uaddOverflow]](#manual-BitVec___uaddOverflow) {w : [Nat]](#manual-Nat___zero)}
  (x y : [BitVec]](#manual-BitVec___ofFin) w) : [Bool](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)
```

Checks whether addition of `x` and `y` results in *unsigned* overflow.

SMT-LIB name: `bvuaddo`.

def

```lean
[BitVec.usubOverflow]](#manual-BitVec___usubOverflow) {w : [Nat]](#manual-Nat___zero)} (x y : [BitVec]](#manual-BitVec___ofFin) w) : [Bool](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)



[BitVec.usubOverflow]](#manual-BitVec___usubOverflow) {w : [Nat]](#manual-Nat___zero)}
  (x y : [BitVec]](#manual-BitVec___ofFin) w) : [Bool](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)
```

Checks whether subtraction of `x` and `y` results in *unsigned* overflow.

SMT-Lib name: `bvusubo`.

##### 20.5.5.8.2. Signed Operations {#manual-The-Lean-Language-Reference--Basic-Types--Bitvectors--API-Reference--Arithmetic--Signed-Operations}

def

```lean
[BitVec.abs]](#manual-BitVec___abs) {n : [Nat]](#manual-Nat___zero)} (x : [BitVec]](#manual-BitVec___ofFin) n) : [BitVec]](#manual-BitVec___ofFin) n



[BitVec.abs]](#manual-BitVec___abs) {n : [Nat]](#manual-Nat___zero)} (x : [BitVec]](#manual-BitVec___ofFin) n) :
  [BitVec]](#manual-BitVec___ofFin) n
```

Returns the absolute value of a signed bitvector.

def

```lean
[BitVec.neg]](#manual-BitVec___neg) {n : [Nat]](#manual-Nat___zero)} (x : [BitVec]](#manual-BitVec___ofFin) n) : [BitVec]](#manual-BitVec___ofFin) n



[BitVec.neg]](#manual-BitVec___neg) {n : [Nat]](#manual-Nat___zero)} (x : [BitVec]](#manual-BitVec___ofFin) n) :
  [BitVec]](#manual-BitVec___ofFin) n
```

Negation of bitvectors. This can be interpreted as either signed or unsigned negation modulo `2^n`.
Usually accessed via the `-` prefix operator.

SMT-LIB name: `bvneg`.

def

```lean
[BitVec.sdiv]](#manual-BitVec___sdiv) {n : [Nat]](#manual-Nat___zero)} (x y : [BitVec]](#manual-BitVec___ofFin) n) : [BitVec]](#manual-BitVec___ofFin) n



[BitVec.sdiv]](#manual-BitVec___sdiv) {n : [Nat]](#manual-Nat___zero)} (x y : [BitVec]](#manual-BitVec___ofFin) n) :
  [BitVec]](#manual-BitVec___ofFin) n
```

Signed T-division (using the truncating rounding convention) for bitvectors. This function obeys the
Lean convention that division by zero returns zero.

Examples:

- `(7#4).[sdiv]](#manual-BitVec___sdiv) 2 = 3#4`
- `(-8#4).[sdiv]](#manual-BitVec___sdiv) 2 = -4#4`
- `(5#4).[sdiv]](#manual-BitVec___sdiv) -2 = -2#4`
- `(-7#4).[sdiv]](#manual-BitVec___sdiv) (-2) = 3#4`

def

```lean
[BitVec.smtSDiv]](#manual-BitVec___smtSDiv) {n : [Nat]](#manual-Nat___zero)} (x y : [BitVec]](#manual-BitVec___ofFin) n) : [BitVec]](#manual-BitVec___ofFin) n



[BitVec.smtSDiv]](#manual-BitVec___smtSDiv) {n : [Nat]](#manual-Nat___zero)}
  (x y : [BitVec]](#manual-BitVec___ofFin) n) : [BitVec]](#manual-BitVec___ofFin) n
```

Signed division for bitvectors using the SMT-LIB using the
[SMT-LIB convention](http://smtlib.cs.uiowa.edu/theories-FixedSizeBitVectors.shtml),
where division by zero returns `BitVector.allOnes n`.

Specifically, `x.[smtSDiv]](#manual-BitVec___smtSDiv) 0 = [if]](#manual-termIfThenElse) x >= 0 [then]](#manual-termIfThenElse) -1 [else]](#manual-termIfThenElse) 1`

SMT-LIB name: `bvsdiv`.

def

```lean
[BitVec.smod]](#manual-BitVec___smod) {m : [Nat]](#manual-Nat___zero)} (x y : [BitVec]](#manual-BitVec___ofFin) m) : [BitVec]](#manual-BitVec___ofFin) m



[BitVec.smod]](#manual-BitVec___smod) {m : [Nat]](#manual-Nat___zero)} (x y : [BitVec]](#manual-BitVec___ofFin) m) :
  [BitVec]](#manual-BitVec___ofFin) m
```

Remainder for signed division rounded to negative infinity.

SMT-LIB name: `bvsmod`.

def

```lean
[BitVec.srem]](#manual-BitVec___srem) {n : [Nat]](#manual-Nat___zero)} (x y : [BitVec]](#manual-BitVec___ofFin) n) : [BitVec]](#manual-BitVec___ofFin) n



[BitVec.srem]](#manual-BitVec___srem) {n : [Nat]](#manual-Nat___zero)} (x y : [BitVec]](#manual-BitVec___ofFin) n) :
  [BitVec]](#manual-BitVec___ofFin) n
```

Remainder for signed division rounding to zero.

SMT-LIB name: `bvsrem`.

def

```lean
[BitVec.saddOverflow]](#manual-BitVec___saddOverflow) {w : [Nat]](#manual-Nat___zero)} (x y : [BitVec]](#manual-BitVec___ofFin) w) : [Bool](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)



[BitVec.saddOverflow]](#manual-BitVec___saddOverflow) {w : [Nat]](#manual-Nat___zero)}
  (x y : [BitVec]](#manual-BitVec___ofFin) w) : [Bool](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)
```

Checks whether addition of `x` and `y` results in *signed* overflow, treating `x` and `y` as 2's
complement signed bitvectors.

SMT-LIB name: `bvsaddo`.

def

```lean
[BitVec.ssubOverflow]](#manual-BitVec___ssubOverflow) {w : [Nat]](#manual-Nat___zero)} (x y : [BitVec]](#manual-BitVec___ofFin) w) : [Bool](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)



[BitVec.ssubOverflow]](#manual-BitVec___ssubOverflow) {w : [Nat]](#manual-Nat___zero)}
  (x y : [BitVec]](#manual-BitVec___ofFin) w) : [Bool](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)
```

Checks whether the subtraction of `x` and `y` results in *signed* overflow, treating `x` and `y` as
2's complement signed bitvectors.

SMT-Lib name: `bvssubo`.

#### 20.5.5.9. Iteration {#manual-The-Lean-Language-Reference--Basic-Types--Bitvectors--API-Reference--Iteration}

def

```lean
[BitVec.iunfoldr.{u_1}]](#manual-BitVec___iunfoldr) {w : [Nat]](#manual-Nat___zero)} {α : Type u_1}
  (f : [Fin]](#manual-Fin___mk) w → α → α [×](https://lean-lang.org/doc/reference/latest/Basic-Types/Tuples/#Prod___mk) [Bool](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)) (s : α) : α [×](https://lean-lang.org/doc/reference/latest/Basic-Types/Tuples/#Prod___mk) [BitVec]](#manual-BitVec___ofFin) w



[BitVec.iunfoldr.{u_1}]](#manual-BitVec___iunfoldr) {w : [Nat]](#manual-Nat___zero)}
  {α : Type u_1}
  (f : [Fin]](#manual-Fin___mk) w → α → α [×](https://lean-lang.org/doc/reference/latest/Basic-Types/Tuples/#Prod___mk) [Bool](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)) (s : α) :
  α [×](https://lean-lang.org/doc/reference/latest/Basic-Types/Tuples/#Prod___mk) [BitVec]](#manual-BitVec___ofFin) w
```

Constructs a bitvector by iteratively computing a state for each bit using the function `f`,
starting with the initial state `s`. At each step, the prior state and the current bit index are
passed to `f`, and it produces a bit along with the next state value. These bits are assembled into
the final bitvector.

It produces a sequence of state values `[s_0, s_1 .. s_w]` and a bitvector `v` where `f i s_i = (s_{i+1}, b_i)` and `b_i` is bit `i`th least-significant bit in `v` (e.g., `getLsb v i = b_i`).

The theorem `iunfoldr_replace` allows uses of `[BitVec.iunfoldr]](#manual-BitVec___iunfoldr)` to be replaced with declarative
specifications that are easier to reason about.

theorem

```lean
[BitVec.iunfoldr_replace.{u_1}]](#manual-BitVec___iunfoldr_replace) {w : [Nat]](#manual-Nat___zero)} {α : Type u_1}
  {f : [Fin]](#manual-Fin___mk) w → α → α [×](https://lean-lang.org/doc/reference/latest/Basic-Types/Tuples/#Prod___mk) [Bool](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)} (state : [Nat]](#manual-Nat___zero) → α) (value : [BitVec]](#manual-BitVec___ofFin) w)
  (a : α) (init : state 0 [=]](#manual-Eq___refl) a)
  (step : ∀ (i : [Fin]](#manual-Fin___mk) w), f i (state ↑i) [=]](#manual-Eq___refl) [(](https://lean-lang.org/doc/reference/latest/Basic-Types/Tuples/#Prod___mk)state [(]](#manual-HAdd___mk)↑i [+]](#manual-HAdd___mk) 1[)]](#manual-HAdd___mk)[,](https://lean-lang.org/doc/reference/latest/Basic-Types/Tuples/#Prod___mk) value[[]](#manual-GetElem___mk)↑i[]](https://lean-lang.org/doc/reference/latest/Type-Classes/Basic-Classes/#GetElem___mk)[)](https://lean-lang.org/doc/reference/latest/Basic-Types/Tuples/#Prod___mk)) :
  [BitVec.iunfoldr]](#manual-BitVec___iunfoldr) f a [=]](#manual-Eq___refl) [(](https://lean-lang.org/doc/reference/latest/Basic-Types/Tuples/#Prod___mk)state w[,](https://lean-lang.org/doc/reference/latest/Basic-Types/Tuples/#Prod___mk) value[)](https://lean-lang.org/doc/reference/latest/Basic-Types/Tuples/#Prod___mk)



[BitVec.iunfoldr_replace.{u_1}]](#manual-BitVec___iunfoldr_replace) {w : [Nat]](#manual-Nat___zero)}
  {α : Type u_1}
  {f : [Fin]](#manual-Fin___mk) w → α → α [×](https://lean-lang.org/doc/reference/latest/Basic-Types/Tuples/#Prod___mk) [Bool](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)}
  (state : [Nat]](#manual-Nat___zero) → α) (value : [BitVec]](#manual-BitVec___ofFin) w)
  (a : α) (init : state 0 [=]](#manual-Eq___refl) a)
  (step :
    ∀ (i : [Fin]](#manual-Fin___mk) w),
      f i (state ↑i) [=]](#manual-Eq___refl)
        [(](https://lean-lang.org/doc/reference/latest/Basic-Types/Tuples/#Prod___mk)state [(]](#manual-HAdd___mk)↑i [+]](#manual-HAdd___mk) 1[)]](#manual-HAdd___mk)[,](https://lean-lang.org/doc/reference/latest/Basic-Types/Tuples/#Prod___mk) value[[]](#manual-GetElem___mk)↑i[]](https://lean-lang.org/doc/reference/latest/Type-Classes/Basic-Classes/#GetElem___mk)[)](https://lean-lang.org/doc/reference/latest/Basic-Types/Tuples/#Prod___mk)) :
  [BitVec.iunfoldr]](#manual-BitVec___iunfoldr) f a [=]](#manual-Eq___refl) [(](https://lean-lang.org/doc/reference/latest/Basic-Types/Tuples/#Prod___mk)state w[,](https://lean-lang.org/doc/reference/latest/Basic-Types/Tuples/#Prod___mk) value[)](https://lean-lang.org/doc/reference/latest/Basic-Types/Tuples/#Prod___mk)
```

Given a function `state` that provides the correct state for every potential iteration count and a
function that computes these states from the correct initial state, the result of applying
`[BitVec.iunfoldr]](#manual-BitVec___iunfoldr) f` to the initial state is the state corresponding to the bitvector's width paired
with the bitvector that consists of each computed bit.

This theorem can be used to prove properties of functions that are defined using `[BitVec.iunfoldr]](#manual-BitVec___iunfoldr)`.

#### 20.5.5.10. Proof Automation {#manual-The-Lean-Language-Reference--Basic-Types--Bitvectors--API-Reference--Proof-Automation}

##### 20.5.5.10.1. Bit Blasting {#manual-The-Lean-Language-Reference--Basic-Types--Bitvectors--API-Reference--Proof-Automation--Bit-Blasting}

The standard library contains a number of helper implementations that are useful to implement bit blasting, which is the technique used by `[bv_decide]](#manual-bv_decide)` to encode propositions as Boolean satisfiability problems for external solvers.

def

```lean
[BitVec.adc]](#manual-BitVec___adc) {w : [Nat]](#manual-Nat___zero)} (x y : [BitVec]](#manual-BitVec___ofFin) w) : [Bool](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false) → [Bool](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false) [×](https://lean-lang.org/doc/reference/latest/Basic-Types/Tuples/#Prod___mk) [BitVec]](#manual-BitVec___ofFin) w



[BitVec.adc]](#manual-BitVec___adc) {w : [Nat]](#manual-Nat___zero)} (x y : [BitVec]](#manual-BitVec___ofFin) w) :
  [Bool](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false) → [Bool](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false) [×](https://lean-lang.org/doc/reference/latest/Basic-Types/Tuples/#Prod___mk) [BitVec]](#manual-BitVec___ofFin) w
```

Bitwise addition implemented via a ripple carry adder.

def

```lean
[BitVec.adcb]](#manual-BitVec___adcb) (x y c : [Bool](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)) : [Bool](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false) [×](https://lean-lang.org/doc/reference/latest/Basic-Types/Tuples/#Prod___mk) [Bool](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)



[BitVec.adcb]](#manual-BitVec___adcb) (x y c : [Bool](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)) : [Bool](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false) [×](https://lean-lang.org/doc/reference/latest/Basic-Types/Tuples/#Prod___mk) [Bool](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)
```

Carry function for bitwise addition.

def

```lean
[BitVec.carry]](#manual-BitVec___carry) {w : [Nat]](#manual-Nat___zero)} (i : [Nat]](#manual-Nat___zero)) (x y : [BitVec]](#manual-BitVec___ofFin) w) (c : [Bool](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)) : [Bool](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)



[BitVec.carry]](#manual-BitVec___carry) {w : [Nat]](#manual-Nat___zero)} (i : [Nat]](#manual-Nat___zero))
  (x y : [BitVec]](#manual-BitVec___ofFin) w) (c : [Bool](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)) : [Bool](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)
```

carry i x y c returns true if the `i` carry bit is true when computing `x + y + c`.

def

```lean
[BitVec.mulRec]](#manual-BitVec___mulRec) {w : [Nat]](#manual-Nat___zero)} (x y : [BitVec]](#manual-BitVec___ofFin) w) (s : [Nat]](#manual-Nat___zero)) : [BitVec]](#manual-BitVec___ofFin) w



[BitVec.mulRec]](#manual-BitVec___mulRec) {w : [Nat]](#manual-Nat___zero)} (x y : [BitVec]](#manual-BitVec___ofFin) w)
  (s : [Nat]](#manual-Nat___zero)) : [BitVec]](#manual-BitVec___ofFin) w
```

A recurrence that describes multiplication as repeated addition.

This function is useful for bit blasting multiplication.

def

```lean
[BitVec.divRec]](#manual-BitVec___divRec) {w : [Nat]](#manual-Nat___zero)} (m : [Nat]](#manual-Nat___zero)) (args : BitVec.DivModArgs w)
  (qr : BitVec.DivModState w) : BitVec.DivModState w



[BitVec.divRec]](#manual-BitVec___divRec) {w : [Nat]](#manual-Nat___zero)} (m : [Nat]](#manual-Nat___zero))
  (args : BitVec.DivModArgs w)
  (qr : BitVec.DivModState w) :
  BitVec.DivModState w
```

A recursive definition of division for bit blasting, in terms of a shift-subtraction circuit.

def

```lean
[BitVec.divSubtractShift]](#manual-BitVec___divSubtractShift) {w : [Nat]](#manual-Nat___zero)} (args : BitVec.DivModArgs w)
  (qr : BitVec.DivModState w) : BitVec.DivModState w



[BitVec.divSubtractShift]](#manual-BitVec___divSubtractShift) {w : [Nat]](#manual-Nat___zero)}
  (args : BitVec.DivModArgs w)
  (qr : BitVec.DivModState w) :
  BitVec.DivModState w
```

One round of the division algorithm. It tries to perform a subtract shift.

This should only be called when `r.msb = [false](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)`, so it will not overflow.

def

```lean
[BitVec.shiftLeftRec]](#manual-BitVec___shiftLeftRec) {w₁ w₂ : [Nat]](#manual-Nat___zero)} (x : [BitVec]](#manual-BitVec___ofFin) w₁) (y : [BitVec]](#manual-BitVec___ofFin) w₂)
  (n : [Nat]](#manual-Nat___zero)) : [BitVec]](#manual-BitVec___ofFin) w₁



[BitVec.shiftLeftRec]](#manual-BitVec___shiftLeftRec) {w₁ w₂ : [Nat]](#manual-Nat___zero)}
  (x : [BitVec]](#manual-BitVec___ofFin) w₁) (y : [BitVec]](#manual-BitVec___ofFin) w₂)
  (n : [Nat]](#manual-Nat___zero)) : [BitVec]](#manual-BitVec___ofFin) w₁
```

Shifts `x` to the left by the first `n` bits of `y`.

The theorem `BitVec.shiftLeft_eq_shiftLeftRec` proves the equivalence of `(x <<< y)` and
`[BitVec.shiftLeftRec]](#manual-BitVec___shiftLeftRec) x y`.

Together with equations `BitVec.shiftLeftRec_zero` and `BitVec.shiftLeftRec_succ`, this allows
`[BitVec.shiftLeft]](#manual-BitVec___shiftLeft)` to be unfolded into a circuit for bit blasting.

def

```lean
[BitVec.sshiftRightRec]](#manual-BitVec___sshiftRightRec) {w₁ w₂ : [Nat]](#manual-Nat___zero)} (x : [BitVec]](#manual-BitVec___ofFin) w₁) (y : [BitVec]](#manual-BitVec___ofFin) w₂)
  (n : [Nat]](#manual-Nat___zero)) : [BitVec]](#manual-BitVec___ofFin) w₁



[BitVec.sshiftRightRec]](#manual-BitVec___sshiftRightRec) {w₁ w₂ : [Nat]](#manual-Nat___zero)}
  (x : [BitVec]](#manual-BitVec___ofFin) w₁) (y : [BitVec]](#manual-BitVec___ofFin) w₂)
  (n : [Nat]](#manual-Nat___zero)) : [BitVec]](#manual-BitVec___ofFin) w₁
```

Shifts `x` arithmetically (signed) to the right by the first `n` bits of `y`.

The theorem `BitVec.sshiftRight_eq_sshiftRightRec` proves the equivalence of `(x.[sshiftRight]](#manual-BitVec___sshiftRight) y)` and
`[BitVec.sshiftRightRec]](#manual-BitVec___sshiftRightRec) x y`. Together with equations `[BitVec]](#manual-BitVec___ofFin).sshiftRightRec_zero`, and
`[BitVec]](#manual-BitVec___ofFin).sshiftRightRec_succ`, this allows `[BitVec.sshiftRight]](#manual-BitVec___sshiftRight)` to be unfolded into a circuit for
bit blasting.

def

```lean
[BitVec.ushiftRightRec]](#manual-BitVec___ushiftRightRec) {w₁ w₂ : [Nat]](#manual-Nat___zero)} (x : [BitVec]](#manual-BitVec___ofFin) w₁) (y : [BitVec]](#manual-BitVec___ofFin) w₂)
  (n : [Nat]](#manual-Nat___zero)) : [BitVec]](#manual-BitVec___ofFin) w₁



[BitVec.ushiftRightRec]](#manual-BitVec___ushiftRightRec) {w₁ w₂ : [Nat]](#manual-Nat___zero)}
  (x : [BitVec]](#manual-BitVec___ofFin) w₁) (y : [BitVec]](#manual-BitVec___ofFin) w₂)
  (n : [Nat]](#manual-Nat___zero)) : [BitVec]](#manual-BitVec___ofFin) w₁
```

Shifts `x` logically to the right by the first `n` bits of `y`.

The theorem `BitVec.shiftRight_eq_ushiftRightRec` proves the equivalence
of `(x >>> y)` and `[BitVec.ushiftRightRec]](#manual-BitVec___ushiftRightRec)`.

Together with equations `BitVec.ushiftRightRec_zero` and `BitVec.ushiftRightRec_succ`,
this allows `[BitVec.ushiftRight]](#manual-BitVec___ushiftRight)` to be unfolded into a circuit for bit blasting.

---



## Basic Types — 20.6. Floating-Point Numbers {#manual-basic-types-206-floating-point-numbers}

> 📄 Source: https://lean-lang.org/doc/reference/latest/Basic-Types/Floating-Point-Numbers/

Floating-point numbers are a an approximation of the real numbers that are efficiently implemented in computer hardware.
Computations that use floating-point numbers are very efficient; however, the nature of the way that they approximate the real numbers is complex, with many corner cases.
The IEEE 754 standard, which defines the floating-point format that is used on modern computers, allows hardware designers and programming language implementations to make certain choices, and real systems differ in these small details.
Any given combination of hardware, operating system, C compiler, library versions, and even compilation flags can result in different behavior.
For example, there are many distinct bit representations of `NaN`, the indicator that a result is undefined, and some platforms differ with respect to *which* `NaN` is returned from adding two `NaN`s.

To enable reasoning about floating-point numbers, Lean exposes a logical model of `[Float]](#manual-Float___ofModel)` that is used in proofs.
In particular, `[Float]](#manual-Float___ofModel)` and `[Float32]](#manual-Float32___ofModel)` are implemented as wrappers around the logical model.
In compiled code, this logical model is replaced by efficient native code.
Differences between platforms are resolved by choosing specific representations (for example, all `NaN` values are replaced by a single canonical `NaN` when any operation requests a bit representation) and by modeling only the subset of floating-point operations that are implemented identically on all supported platforms.
Other operations, such as trigonometric functions, are represented as opaque functions in Lean's logic.

The logical model is extensively empirically tested against the floating-point operations on all supported platforms.
As long as FFI code does not modify the floating-point environment, Lean's runtime floating-point primitives match the model's specification.

structure

```lean
[Float]](#manual-Float___ofModel) : Type



[Float]](#manual-Float___ofModel) : Type
```

64-bit floating-point numbers.

`[Float]](#manual-Float___ofModel)` corresponds to the IEEE 754 *binary64* format (`double` in C or `f64` in Rust).
Floating-point numbers are a finite representation of a subset of the real numbers, extended with
extra “sentinel” values that represent undefined and infinite results as well as separate positive
and negative zeroes. Arithmetic on floating-point numbers approximates the corresponding operations
on the real numbers by rounding the results to numbers that are representable, propagating error and
infinite values.

Floating-point numbers include [subnormal numbers](https://en.wikipedia.org/wiki/Subnormal_number).
Their special values are:

- `NaN`, which denotes a class of “not a number” values that result from operations such as
  dividing zero by zero, and
- `Inf` and `-Inf`, which represent positive and infinities that result from dividing non-zero
  values by zero.

Like other low-level types, `[Float]](#manual-Float___ofModel)` is special-cased by the Lean compiler to correspond to the C
`double` type. From the point of view of Lean's logic, `[Float]](#manual-Float___ofModel)` is equivalent to `[Float.Model]](#manual-Float___Model___mk)` (via
the functions `[Float.toModel]](#manual-Float___ofModel)` and `[Float.ofModel]](#manual-Float___ofModel)`), which is itself a subtype of `[UInt64]](#manual-UInt64___ofBitVec)`. Some of
the operations on `[Float]](#manual-Float___ofModel)` are defined in terms of their `[Float.Model]](#manual-Float___Model___mk)` counterparts, while others
are opaque to Lean's kernel.

Constructor

```lean
[Float.ofModel]](#manual-Float___ofModel)
```

Constructs a `[Float]](#manual-Float___ofModel)` from a `[Float.Model]](#manual-Float___Model___mk)`.

Fields

```lean
toModel : [Float.Model]](#manual-Float___Model___mk)
```

Converts a `[Float]](#manual-Float___ofModel)` into a `[Float.Model]](#manual-Float___Model___mk)`.

structure

```lean
[Float32]](#manual-Float32___ofModel) : Type



[Float32]](#manual-Float32___ofModel) : Type
```

32-bit floating-point numbers.

`[Float32]](#manual-Float32___ofModel)` corresponds to the IEEE 754 *binary32* format (`float` in C or `f32` in Rust).
Floating-point numbers are a finite representation of a subset of the real numbers, extended with
extra “sentinel” values that represent undefined and infinite results as well as separate positive
and negative zeroes. Arithmetic on floating-point numbers approximates the corresponding operations
on the real numbers by rounding the results to numbers that are representable, propagating error and
infinite values.

Floating-point numbers include [subnormal numbers](https://en.wikipedia.org/wiki/Subnormal_number).
Their special values are:

- `NaN`, which denotes a class of “not a number” values that result from operations such as
  dividing zero by zero, and
- `Inf` and `-Inf`, which represent positive and infinities that result from dividing non-zero
  values by zero.

Like other low-level types, `[Float32]](#manual-Float32___ofModel)` is special-cased by the Lean compiler to correspond to the C
`float` type. From the point of view of Lean's logic, `[Float32]](#manual-Float32___ofModel)` is equivalent to `[Float32.Model]](#manual-Float32___Model___mk)`
(via the functions `[Float32.toModel]](#manual-Float32___ofModel)` and `[Float32.ofModel]](#manual-Float32___ofModel)`), which is itself a subtype of `[UInt32]](#manual-UInt32___ofBitVec)`.
Some of the operations on `[Float32]](#manual-Float32___ofModel)` are defined in terms of their `[Float32.Model]](#manual-Float32___Model___mk)` counterparts,
while others are opaque to Lean's kernel.

Constructor

```lean
[Float32.ofModel]](#manual-Float32___ofModel)
```

Constructs a `[Float32]](#manual-Float32___ofModel)` from a `[Float32.Model]](#manual-Float32___Model___mk)`.

Fields

```lean
toModel : [Float32.Model]](#manual-Float32___Model___mk)
```

Converts a `[Float32]](#manual-Float32___ofModel)` into a `[Float32.Model]](#manual-Float32___Model___mk)`.

### 20.6.1. Logical Model {#manual-The-Lean-Language-Reference--Basic-Types--Floating-Point-Numbers--Logical-Model}

Lean provides two floating-point types: `[Float]](#manual-Float___ofModel)` represents 64-bit floating-point values, while `[Float32]](#manual-Float32___ofModel)` represents 32-bit floating-point values.
The precision of `[Float]](#manual-Float___ofModel)` does not vary based on the platform that Lean is running on.

#### 20.6.1.1. Model Details {#manual-The-Lean-Language-Reference--Basic-Types--Floating-Point-Numbers--Logical-Model--Model-Details}

The logical models of `[Float]](#manual-Float___ofModel)` and `[Float32]](#manual-Float32___ofModel)` consist of unsigned integers with validity predicates.
Each defined operation first interprets the integer into a `[Float.Model.UnpackedFloat]](#manual-Float___Model___UnpackedFloat___infinity)`, which is a higher-level model that is not specific to a bit width.
Then, the defined operation is implemented in terms of `[UnpackedFloat]](#manual-Float___Model___UnpackedFloat___infinity)`, and the result is re-packed.
These definitions constitute a *logical specification* designed for reasoning.
Although they can be executed, they will run significantly slower than native code.
Not all operations are defined; some are instead opaque functions whose behavior cannot be reasoned about in Lean's logic.

This model is not intended to serve as the basis for a more extensive floating-point library.
It exists only to support the reasoning tools available in Lean and is not suitable for larger-scale development.
Do not use this model as the basis of a more extensive floating-point library.
Instead, implement a suitable model, prove the equivalence of the its operations to this model, and then transfer lemmas using the equivalence.

structure

```lean
[Float.Model]](#manual-Float___Model___mk) : Type



[Float.Model]](#manual-Float___Model___mk) : Type
```

The logical model for the `[Float]](#manual-Float___ofModel)` type.

This is defined as the type of `[UInt64]](#manual-UInt64___ofBitVec)` with the additional restriction that bit patterns encoding
a `NaN` must be exactly a chosen canonical `NaN`.

Most functions on `[Float.Model]](#manual-Float___Model___mk)` work by unpacking the `[Float.Model]](#manual-Float___Model___mk)` into the inductive type
`UnpackedFloat`, performing the operation there, and then repacking the result into a `[Float.Model]](#manual-Float___Model___mk)`.

It is not a goal of this development to serve as the basis for a general-purpose floating-point
library or to have any direct lemmas written about it at all. Rather, users interested in a library
about floating-point numbers should develop such a library completely separately, and users
interested in proving properties of their programs involving `[Float]](#manual-Float___ofModel)` should prove that the
operations defined here are equivalent to the operations defined in the separate library and then
transfer lemmas from the library to the `[Float]](#manual-Float___ofModel)` and `[Float32]](#manual-Float32___ofModel)` types.

Constructor

```lean
[Float.Model.mk]](#manual-Float___Model___mk)
```

Fields

```lean
toBits : [UInt64]](#manual-UInt64___ofBitVec)
```

The underlying bit pattern of the `[Float.Model]](#manual-Float___Model___mk)`.

```lean
valid : Float.Model.Format.binary64.Valid self.[toBits]](#manual-Float___Model___mk).[toBitVec]](#manual-UInt64___ofBitVec)
```

The underlying bit pattern is valid according to the IEEE `binary64` format.

structure

```lean
[Float32.Model]](#manual-Float32___Model___mk) : Type



[Float32.Model]](#manual-Float32___Model___mk) : Type
```

The logical model for the `[Float32]](#manual-Float32___ofModel)` type.

This is defined as the type of `[UInt32]](#manual-UInt32___ofBitVec)` with the additional restriction that bit patterns encoding
a `NaN` must be exactly a chosen canonical `NaN`.

Most functions on `[Float32.Model]](#manual-Float32___Model___mk)` work by unpacking the `[Float32.Model]](#manual-Float32___Model___mk)` into the inductive type
`UnpackedFloat`, performing the operation there, and then repacking the result into a
`[Float32.Model]](#manual-Float32___Model___mk)`.

It is not a goal of this development to serve as the basis for a general-purpose floating-point
library or to have any direct lemmas written about it at all. Rather, users interested in a library
about floating-point numbers should develop such a library completely separately, and users
interested in proving properties of their programs involving `[Float32]](#manual-Float32___ofModel)` should prove that the
operations defined here are equivalent to the operations defined in the separate library and then
transfer lemmas from the library to the `[Float]](#manual-Float___ofModel)` and `[Float32]](#manual-Float32___ofModel)` types.

Constructor

```lean
[Float32.Model.mk]](#manual-Float32___Model___mk)
```

Fields

```lean
toBits : [UInt32]](#manual-UInt32___ofBitVec)
```

The underlying bit pattern of the `[Float32.Model]](#manual-Float32___Model___mk)`.

```lean
valid : Float.Model.Format.binary32.Valid self.[toBits]](#manual-Float32___Model___mk).[toBitVec]](#manual-UInt32___ofBitVec)
```

The underlying bit pattern is valid according to the IEEE `binary32` format.

def

```lean
[Float.Model.pack]](#manual-Float___Model___pack) (f : [Float.Model.UnpackedFloat]](#manual-Float___Model___UnpackedFloat___infinity)) : [Float.Model]](#manual-Float___Model___mk)



[Float.Model.pack]](#manual-Float___Model___pack)
  (f : [Float.Model.UnpackedFloat]](#manual-Float___Model___UnpackedFloat___infinity)) :
  [Float.Model]](#manual-Float___Model___mk)
```

Pack an `UnpackedFloat` into the corresponding `[Float.Model]](#manual-Float___Model___mk)`.
This operation only gives a meaningful result if the float is
already correctly rounded for the `Format.binary64` format.

def

```lean
[Float32.Model.pack]](#manual-Float32___Model___pack) (f : [Float.Model.UnpackedFloat]](#manual-Float___Model___UnpackedFloat___infinity)) : [Float32.Model]](#manual-Float32___Model___mk)



[Float32.Model.pack]](#manual-Float32___Model___pack)
  (f : [Float.Model.UnpackedFloat]](#manual-Float___Model___UnpackedFloat___infinity)) :
  [Float32.Model]](#manual-Float32___Model___mk)
```

Pack an `UnpackedFloat` into the corresponding `[Float32.Model]](#manual-Float32___Model___mk)`.
This operation only gives a meaningful result if the float is
already correctly rounded for the `Format.binary32` format.

def

```lean
[Float.Model.unpack]](#manual-Float___Model___unpack) (f : [Float.Model]](#manual-Float___Model___mk)) : [Float.Model.UnpackedFloat]](#manual-Float___Model___UnpackedFloat___infinity)



[Float.Model.unpack]](#manual-Float___Model___unpack) (f : [Float.Model]](#manual-Float___Model___mk)) :
  [Float.Model.UnpackedFloat]](#manual-Float___Model___UnpackedFloat___infinity)
```

Unpack a `[Float.Model]](#manual-Float___Model___mk)` into the corresponding `UnpackedFloat`.

def

```lean
[Float32.Model.unpack]](#manual-Float32___Model___unpack) (f : [Float32.Model]](#manual-Float32___Model___mk)) : [Float.Model.UnpackedFloat]](#manual-Float___Model___UnpackedFloat___infinity)



[Float32.Model.unpack]](#manual-Float32___Model___unpack) (f : [Float32.Model]](#manual-Float32___Model___mk)) :
  [Float.Model.UnpackedFloat]](#manual-Float___Model___UnpackedFloat___infinity)
```

Unpack a `[Float32.Model]](#manual-Float32___Model___mk)` into the corresponding `UnpackedFloat`.

inductive type

```lean
[Float.Model.UnpackedFloat]](#manual-Float___Model___UnpackedFloat___infinity) : Type



[Float.Model.UnpackedFloat]](#manual-Float___Model___UnpackedFloat___infinity) : Type
```

An inductive type representing a floating-point number with constructors for signed infinity,
not-a-number without payload, signed zero, and finite floats with a sign, positive natural
mantissa and integral exponent.

Finite floats do not have a unique representation in this format: multiplying the
mantissa by two and decreasing the exponent by one yields a finite float that represents the
same rational number.

For a given `Format`, we say that an unpacked float is in canonical form if the exponent
is equal to the `targetExponent` according to that format. Some operations on `UnpackedFloat`,
such as `[compare]](#manual-Ord___mk)`, assume that the input(s) are all in canonical form for the same format.

Note that an unpacked float in canonical form for a given format may not actually be
representable in that format as the exponent is too large to fit. In this case, the `pack`
function will overflow the float to infinity.

This type exists solely for the purpose of supporting `[Float.Model]](#manual-Float___Model___mk)` and `[Float32.Model]](#manual-Float32___Model___mk)`. It is not
a goal of this development to serve as the basis for a general-purpose floating-point library or
to have any direct lemmas written about it at all. Rather, users interested in a library about
floating-point numbers should develop such a library completely separately, and users interested in
proving properties of their programs involving `[Float]](#manual-Float___ofModel)` should prove that the operations defined
here are equivalent to the operations defined in the separate library and then transfer lemmas from
the library to the `[Float]](#manual-Float___ofModel)` and `[Float32]](#manual-Float32___ofModel)` types.

Constructors

```lean
[Float.Model.UnpackedFloat.infinity]](#manual-Float___Model___UnpackedFloat___infinity)
  (sign : Float.Model.UnpackedFloat.Sign) :
  [Float.Model.UnpackedFloat]](#manual-Float___Model___UnpackedFloat___infinity)
```

Signed infinity.

```lean
[Float.Model.UnpackedFloat.notANumber]](#manual-Float___Model___UnpackedFloat___infinity) :
  [Float.Model.UnpackedFloat]](#manual-Float___Model___UnpackedFloat___infinity)
```

Not a number. There is no payload attached to a NaN in this format.

```lean
[Float.Model.UnpackedFloat.zero]](#manual-Float___Model___UnpackedFloat___infinity)
  (sign : Float.Model.UnpackedFloat.Sign) :
  [Float.Model.UnpackedFloat]](#manual-Float___Model___UnpackedFloat___infinity)
```

Signed zero.

```lean
[Float.Model.UnpackedFloat.finite]](#manual-Float___Model___UnpackedFloat___infinity)
  (sign : Float.Model.UnpackedFloat.Sign) (mantissa : [Nat]](#manual-Nat___zero))
  (exponent : [Int]](#manual-Int___ofNat)) (mantissa_pos : 0 [<]](#manual-LT___mk) mantissa) :
  [Float.Model.UnpackedFloat]](#manual-Float___Model___UnpackedFloat___infinity)
```

Finite floats consisting of a sign bit, a positive natural mantissa and an exponent.

#### 20.6.1.2. Model Operations {#manual-The-Lean-Language-Reference--Basic-Types--Floating-Point-Numbers--Logical-Model--Model-Operations}

The following operations are specified for floating-point values.
Other operators are represented by opaque functions and do not reduce in the kernel.

def

```lean
[Float.Model.UnpackedFloat.add]](#manual-Float___Model___UnpackedFloat___add) (spec : Float.Model.Format) :
  [Float.Model.UnpackedFloat]](#manual-Float___Model___UnpackedFloat___infinity) →
    [Float.Model.UnpackedFloat]](#manual-Float___Model___UnpackedFloat___infinity) → [Float.Model.UnpackedFloat]](#manual-Float___Model___UnpackedFloat___infinity)



[Float.Model.UnpackedFloat.add]](#manual-Float___Model___UnpackedFloat___add)
  (spec : Float.Model.Format) :
  [Float.Model.UnpackedFloat]](#manual-Float___Model___UnpackedFloat___infinity) →
    [Float.Model.UnpackedFloat]](#manual-Float___Model___UnpackedFloat___infinity) →
      [Float.Model.UnpackedFloat]](#manual-Float___Model___UnpackedFloat___infinity)
```

Computes the sum of two floating point numbers and rounds the result according to
the given specification.

def

```lean
[Float.Model.UnpackedFloat.sub]](#manual-Float___Model___UnpackedFloat___sub) (spec : Float.Model.Format) :
  [Float.Model.UnpackedFloat]](#manual-Float___Model___UnpackedFloat___infinity) →
    [Float.Model.UnpackedFloat]](#manual-Float___Model___UnpackedFloat___infinity) → [Float.Model.UnpackedFloat]](#manual-Float___Model___UnpackedFloat___infinity)



[Float.Model.UnpackedFloat.sub]](#manual-Float___Model___UnpackedFloat___sub)
  (spec : Float.Model.Format) :
  [Float.Model.UnpackedFloat]](#manual-Float___Model___UnpackedFloat___infinity) →
    [Float.Model.UnpackedFloat]](#manual-Float___Model___UnpackedFloat___infinity) →
      [Float.Model.UnpackedFloat]](#manual-Float___Model___UnpackedFloat___infinity)
```

Computes the difference of two floating point numbers and rounds the result according to
the given specification.

def

```lean
[Float.Model.UnpackedFloat.mul]](#manual-Float___Model___UnpackedFloat___mul) (spec : Float.Model.Format) :
  [Float.Model.UnpackedFloat]](#manual-Float___Model___UnpackedFloat___infinity) →
    [Float.Model.UnpackedFloat]](#manual-Float___Model___UnpackedFloat___infinity) → [Float.Model.UnpackedFloat]](#manual-Float___Model___UnpackedFloat___infinity)



[Float.Model.UnpackedFloat.mul]](#manual-Float___Model___UnpackedFloat___mul)
  (spec : Float.Model.Format) :
  [Float.Model.UnpackedFloat]](#manual-Float___Model___UnpackedFloat___infinity) →
    [Float.Model.UnpackedFloat]](#manual-Float___Model___UnpackedFloat___infinity) →
      [Float.Model.UnpackedFloat]](#manual-Float___Model___UnpackedFloat___infinity)
```

Computes the product of two floating-point numbers and rounds the result according to
the given specification.

def

```lean
[Float.Model.UnpackedFloat.div]](#manual-Float___Model___UnpackedFloat___div) (spec : Float.Model.Format) :
  [Float.Model.UnpackedFloat]](#manual-Float___Model___UnpackedFloat___infinity) →
    [Float.Model.UnpackedFloat]](#manual-Float___Model___UnpackedFloat___infinity) → [Float.Model.UnpackedFloat]](#manual-Float___Model___UnpackedFloat___infinity)



[Float.Model.UnpackedFloat.div]](#manual-Float___Model___UnpackedFloat___div)
  (spec : Float.Model.Format) :
  [Float.Model.UnpackedFloat]](#manual-Float___Model___UnpackedFloat___infinity) →
    [Float.Model.UnpackedFloat]](#manual-Float___Model___UnpackedFloat___infinity) →
      [Float.Model.UnpackedFloat]](#manual-Float___Model___UnpackedFloat___infinity)
```

Computes the quotient of two floating point numbers and rounds the result according to
the given specification.

def

```lean
[Float.Model.UnpackedFloat.sqrt]](#manual-Float___Model___UnpackedFloat___sqrt) (spec : Float.Model.Format) :
  [Float.Model.UnpackedFloat]](#manual-Float___Model___UnpackedFloat___infinity) → [Float.Model.UnpackedFloat]](#manual-Float___Model___UnpackedFloat___infinity)



[Float.Model.UnpackedFloat.sqrt]](#manual-Float___Model___UnpackedFloat___sqrt)
  (spec : Float.Model.Format) :
  [Float.Model.UnpackedFloat]](#manual-Float___Model___UnpackedFloat___infinity) →
    [Float.Model.UnpackedFloat]](#manual-Float___Model___UnpackedFloat___infinity)
```

Computes the square root of a floating-point number and rounds the result according to the given
specification.

def

```lean
[Float.Model.UnpackedFloat.neg]](#manual-Float___Model___UnpackedFloat___neg) :
  [Float.Model.UnpackedFloat]](#manual-Float___Model___UnpackedFloat___infinity) → [Float.Model.UnpackedFloat]](#manual-Float___Model___UnpackedFloat___infinity)



[Float.Model.UnpackedFloat.neg]](#manual-Float___Model___UnpackedFloat___neg) :
  [Float.Model.UnpackedFloat]](#manual-Float___Model___UnpackedFloat___infinity) →
    [Float.Model.UnpackedFloat]](#manual-Float___Model___UnpackedFloat___infinity)
```

Negates the given float.

def

```lean
[Float.Model.UnpackedFloat.abs]](#manual-Float___Model___UnpackedFloat___abs) :
  [Float.Model.UnpackedFloat]](#manual-Float___Model___UnpackedFloat___infinity) → [Float.Model.UnpackedFloat]](#manual-Float___Model___UnpackedFloat___infinity)



[Float.Model.UnpackedFloat.abs]](#manual-Float___Model___UnpackedFloat___abs) :
  [Float.Model.UnpackedFloat]](#manual-Float___Model___UnpackedFloat___infinity) →
    [Float.Model.UnpackedFloat]](#manual-Float___Model___UnpackedFloat___infinity)
```

Returns the given float with positive sign.

def

```lean
[Float.Model.UnpackedFloat.isNaN]](#manual-Float___Model___UnpackedFloat___isNaN) : [Float.Model.UnpackedFloat]](#manual-Float___Model___UnpackedFloat___infinity) → [Bool](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)



[Float.Model.UnpackedFloat.isNaN]](#manual-Float___Model___UnpackedFloat___isNaN) :
  [Float.Model.UnpackedFloat]](#manual-Float___Model___UnpackedFloat___infinity) → [Bool](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)
```

Returns `[true](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)` if the float is `NaN`.

def

```lean
[Float.Model.UnpackedFloat.isInf]](#manual-Float___Model___UnpackedFloat___isInf) : [Float.Model.UnpackedFloat]](#manual-Float___Model___UnpackedFloat___infinity) → [Bool](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)



[Float.Model.UnpackedFloat.isInf]](#manual-Float___Model___UnpackedFloat___isInf) :
  [Float.Model.UnpackedFloat]](#manual-Float___Model___UnpackedFloat___infinity) → [Bool](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)
```

Returns `[true](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)` if the float is positive or negative infinity.

def

```lean
[Float.Model.UnpackedFloat.isFinite]](#manual-Float___Model___UnpackedFloat___isFinite) : [Float.Model.UnpackedFloat]](#manual-Float___Model___UnpackedFloat___infinity) → [Bool](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)



[Float.Model.UnpackedFloat.isFinite]](#manual-Float___Model___UnpackedFloat___isFinite) :
  [Float.Model.UnpackedFloat]](#manual-Float___Model___UnpackedFloat___infinity) → [Bool](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)
```

Returns `[true](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)` if the float represents a real number, i.e., it is neither infinite nor `NaN`.

def

```lean
[Float.Model.UnpackedFloat.compare]](#manual-Float___Model___UnpackedFloat___compare) :
  [Float.Model.UnpackedFloat]](#manual-Float___Model___UnpackedFloat___infinity) →
    [Float.Model.UnpackedFloat]](#manual-Float___Model___UnpackedFloat___infinity) → [Option](https://lean-lang.org/doc/reference/latest/Basic-Types/Optional-Values/#Option___none) [Ordering]](#manual-Ordering___lt)



[Float.Model.UnpackedFloat.compare]](#manual-Float___Model___UnpackedFloat___compare) :
  [Float.Model.UnpackedFloat]](#manual-Float___Model___UnpackedFloat___infinity) →
    [Float.Model.UnpackedFloat]](#manual-Float___Model___UnpackedFloat___infinity) →
      [Option](https://lean-lang.org/doc/reference/latest/Basic-Types/Optional-Values/#Option___none) [Ordering]](#manual-Ordering___lt)
```

Computes the ordering between the two floats as specificed by IEEE. Returns an
`[Option](https://lean-lang.org/doc/reference/latest/Basic-Types/Optional-Values/#Option___none) [Ordering]](#manual-Ordering___lt)` to account for the fact that `NaN` is incomparable with everything.
Also, positive and negative zero are equal.

Important: this operation only works correctly if the two inputs are in
canonical form for a common format (see the docstring for `UnpackedFloat` for details.)

def

```lean
[Float.Model.UnpackedFloat.beq]](#manual-Float___Model___UnpackedFloat___beq) (a b : [Float.Model.UnpackedFloat]](#manual-Float___Model___UnpackedFloat___infinity)) : [Bool](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)



[Float.Model.UnpackedFloat.beq]](#manual-Float___Model___UnpackedFloat___beq)
  (a b : [Float.Model.UnpackedFloat]](#manual-Float___Model___UnpackedFloat___infinity)) : [Bool](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)
```

Determines whether `a` is equal to `b` according to IEEE rules.

This is not a reflexive relation.

def

```lean
[Float.Model.UnpackedFloat.lt]](#manual-Float___Model___UnpackedFloat___lt) (a b : [Float.Model.UnpackedFloat]](#manual-Float___Model___UnpackedFloat___infinity)) : [Bool](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)



[Float.Model.UnpackedFloat.lt]](#manual-Float___Model___UnpackedFloat___lt)
  (a b : [Float.Model.UnpackedFloat]](#manual-Float___Model___UnpackedFloat___infinity)) : [Bool](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)
```

Determines whether `a` is less than `b` according to IEEE rules.

This is not a total ordering.

def

```lean
[Float.Model.UnpackedFloat.le]](#manual-Float___Model___UnpackedFloat___le) (a b : [Float.Model.UnpackedFloat]](#manual-Float___Model___UnpackedFloat___infinity)) : [Bool](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)



[Float.Model.UnpackedFloat.le]](#manual-Float___Model___UnpackedFloat___le)
  (a b : [Float.Model.UnpackedFloat]](#manual-Float___Model___UnpackedFloat___infinity)) : [Bool](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)
```

Determines whether `a` is less than or equal to `b` according to IEEE rules.

This is not a total ordering, and `≤` is not reflexive.

def

```lean
[Float.Model.UnpackedFloat.ofNat]](#manual-Float___Model___UnpackedFloat___ofNat) (spec : Float.Model.Format) (n : [Nat]](#manual-Nat___zero)) :
  [Float.Model.UnpackedFloat]](#manual-Float___Model___UnpackedFloat___infinity)



[Float.Model.UnpackedFloat.ofNat]](#manual-Float___Model___UnpackedFloat___ofNat)
  (spec : Float.Model.Format) (n : [Nat]](#manual-Nat___zero)) :
  [Float.Model.UnpackedFloat]](#manual-Float___Model___UnpackedFloat___infinity)
```

Converts a `[Nat]](#manual-Nat___zero)` to an `UnpackedFloat`, returning positive zero on zero.

def

```lean
[Float.Model.UnpackedFloat.ofInt]](#manual-Float___Model___UnpackedFloat___ofInt) (spec : Float.Model.Format) (n : [Int]](#manual-Int___ofNat)) :
  [Float.Model.UnpackedFloat]](#manual-Float___Model___UnpackedFloat___infinity)



[Float.Model.UnpackedFloat.ofInt]](#manual-Float___Model___UnpackedFloat___ofInt)
  (spec : Float.Model.Format) (n : [Int]](#manual-Int___ofNat)) :
  [Float.Model.UnpackedFloat]](#manual-Float___Model___UnpackedFloat___infinity)
```

Converts an `[Int]](#manual-Int___ofNat)` to an `UnpackedFloat`, returning positive zero on zero.

def

```lean
[Float.Model.UnpackedFloat.ofScientific]](#manual-Float___Model___UnpackedFloat___ofScientific) (spec : Float.Model.Format)
  (m : [Nat]](#manual-Nat___zero)) (e : [Int]](#manual-Int___ofNat)) : [Float.Model.UnpackedFloat]](#manual-Float___Model___UnpackedFloat___infinity)



[Float.Model.UnpackedFloat.ofScientific]](#manual-Float___Model___UnpackedFloat___ofScientific)
  (spec : Float.Model.Format) (m : [Nat]](#manual-Nat___zero))
  (e : [Int]](#manual-Int___ofNat)) : [Float.Model.UnpackedFloat]](#manual-Float___Model___UnpackedFloat___infinity)
```

Computes `m * 10 ^ e`.

def

```lean
[Float.Model.UnpackedFloat.toInt8]](#manual-Float___Model___UnpackedFloat___toInt8) (f : [Float.Model.UnpackedFloat]](#manual-Float___Model___UnpackedFloat___infinity)) : [Int8]](#manual-Int8___ofUInt8)



[Float.Model.UnpackedFloat.toInt8]](#manual-Float___Model___UnpackedFloat___toInt8)
  (f : [Float.Model.UnpackedFloat]](#manual-Float___Model___UnpackedFloat___infinity)) : [Int8]](#manual-Int8___ofUInt8)
```

Converts an `UnpackedFloat` to an `[Int8]](#manual-Int8___ofUInt8)`, truncating after the decimal point, sending `NaN` to
`0` and clamping out-of-range values and infinities.

def

```lean
[Float.Model.UnpackedFloat.ofInt8]](#manual-Float___Model___UnpackedFloat___ofInt8) (spec : Float.Model.Format)
  (n : [Int8]](#manual-Int8___ofUInt8)) : [Float.Model.UnpackedFloat]](#manual-Float___Model___UnpackedFloat___infinity)



[Float.Model.UnpackedFloat.ofInt8]](#manual-Float___Model___UnpackedFloat___ofInt8)
  (spec : Float.Model.Format) (n : [Int8]](#manual-Int8___ofUInt8)) :
  [Float.Model.UnpackedFloat]](#manual-Float___Model___UnpackedFloat___infinity)
```

Converts an `[Int8]](#manual-Int8___ofUInt8)` to an `UnpackedFloat`, returning positive zero on zero.

def

```lean
[Float.Model.UnpackedFloat.toInt16]](#manual-Float___Model___UnpackedFloat___toInt16) (f : [Float.Model.UnpackedFloat]](#manual-Float___Model___UnpackedFloat___infinity)) :
  [Int16]](#manual-Int16___ofUInt16)



[Float.Model.UnpackedFloat.toInt16]](#manual-Float___Model___UnpackedFloat___toInt16)
  (f : [Float.Model.UnpackedFloat]](#manual-Float___Model___UnpackedFloat___infinity)) : [Int16]](#manual-Int16___ofUInt16)
```

Converts an `UnpackedFloat` to an `[Int16]](#manual-Int16___ofUInt16)`, truncating after the decimal point, sending `NaN` to
`0` and clamping out-of-range values and infinities.

def

```lean
[Float.Model.UnpackedFloat.ofInt16]](#manual-Float___Model___UnpackedFloat___ofInt16) (spec : Float.Model.Format)
  (n : [Int16]](#manual-Int16___ofUInt16)) : [Float.Model.UnpackedFloat]](#manual-Float___Model___UnpackedFloat___infinity)



[Float.Model.UnpackedFloat.ofInt16]](#manual-Float___Model___UnpackedFloat___ofInt16)
  (spec : Float.Model.Format)
  (n : [Int16]](#manual-Int16___ofUInt16)) : [Float.Model.UnpackedFloat]](#manual-Float___Model___UnpackedFloat___infinity)
```

Converts an `[Int16]](#manual-Int16___ofUInt16)` to an `UnpackedFloat`, returning positive zero on zero.

def

```lean
[Float.Model.UnpackedFloat.toInt32]](#manual-Float___Model___UnpackedFloat___toInt32) (f : [Float.Model.UnpackedFloat]](#manual-Float___Model___UnpackedFloat___infinity)) :
  [Int32]](#manual-Int32___ofUInt32)



[Float.Model.UnpackedFloat.toInt32]](#manual-Float___Model___UnpackedFloat___toInt32)
  (f : [Float.Model.UnpackedFloat]](#manual-Float___Model___UnpackedFloat___infinity)) : [Int32]](#manual-Int32___ofUInt32)
```

Converts an `UnpackedFloat` to an `[Int32]](#manual-Int32___ofUInt32)`, truncating after the decimal point, sending `NaN` to
`0` and clamping out-of-range values and infinities.

def

```lean
[Float.Model.UnpackedFloat.ofInt32]](#manual-Float___Model___UnpackedFloat___ofInt32) (spec : Float.Model.Format)
  (n : [Int32]](#manual-Int32___ofUInt32)) : [Float.Model.UnpackedFloat]](#manual-Float___Model___UnpackedFloat___infinity)



[Float.Model.UnpackedFloat.ofInt32]](#manual-Float___Model___UnpackedFloat___ofInt32)
  (spec : Float.Model.Format)
  (n : [Int32]](#manual-Int32___ofUInt32)) : [Float.Model.UnpackedFloat]](#manual-Float___Model___UnpackedFloat___infinity)
```

Converts an `[Int32]](#manual-Int32___ofUInt32)` to an `UnpackedFloat`, returning positive zero on zero.

def

```lean
[Float.Model.UnpackedFloat.toInt64]](#manual-Float___Model___UnpackedFloat___toInt64) (f : [Float.Model.UnpackedFloat]](#manual-Float___Model___UnpackedFloat___infinity)) :
  [Int64]](#manual-Int64___ofUInt64)



[Float.Model.UnpackedFloat.toInt64]](#manual-Float___Model___UnpackedFloat___toInt64)
  (f : [Float.Model.UnpackedFloat]](#manual-Float___Model___UnpackedFloat___infinity)) : [Int64]](#manual-Int64___ofUInt64)
```

Converts an `UnpackedFloat` to an `[Int64]](#manual-Int64___ofUInt64)`, truncating after the decimal point, sending `NaN` to
`0` and clamping out-of-range values and infinities.

def

```lean
[Float.Model.UnpackedFloat.ofInt64]](#manual-Float___Model___UnpackedFloat___ofInt64) (spec : Float.Model.Format)
  (n : [Int64]](#manual-Int64___ofUInt64)) : [Float.Model.UnpackedFloat]](#manual-Float___Model___UnpackedFloat___infinity)



[Float.Model.UnpackedFloat.ofInt64]](#manual-Float___Model___UnpackedFloat___ofInt64)
  (spec : Float.Model.Format)
  (n : [Int64]](#manual-Int64___ofUInt64)) : [Float.Model.UnpackedFloat]](#manual-Float___Model___UnpackedFloat___infinity)
```

Converts an `[Int64]](#manual-Int64___ofUInt64)` to an `UnpackedFloat`, returning positive zero on zero.

def

```lean
[Float.Model.UnpackedFloat.toISize]](#manual-Float___Model___UnpackedFloat___toISize) (f : [Float.Model.UnpackedFloat]](#manual-Float___Model___UnpackedFloat___infinity)) :
  [ISize]](#manual-ISize___ofUSize)



[Float.Model.UnpackedFloat.toISize]](#manual-Float___Model___UnpackedFloat___toISize)
  (f : [Float.Model.UnpackedFloat]](#manual-Float___Model___UnpackedFloat___infinity)) : [ISize]](#manual-ISize___ofUSize)
```

Converts an `UnpackedFloat` to an `[ISize]](#manual-ISize___ofUSize)`, truncating after the decimal point, sending `NaN` to
`0` and clamping out-of-range values and infinities.

def

```lean
[Float.Model.UnpackedFloat.ofISize]](#manual-Float___Model___UnpackedFloat___ofISize) (spec : Float.Model.Format)
  (n : [ISize]](#manual-ISize___ofUSize)) : [Float.Model.UnpackedFloat]](#manual-Float___Model___UnpackedFloat___infinity)



[Float.Model.UnpackedFloat.ofISize]](#manual-Float___Model___UnpackedFloat___ofISize)
  (spec : Float.Model.Format)
  (n : [ISize]](#manual-ISize___ofUSize)) : [Float.Model.UnpackedFloat]](#manual-Float___Model___UnpackedFloat___infinity)
```

Converts an `[ISize]](#manual-ISize___ofUSize)` to an `UnpackedFloat`, returning positive zero on zero.

def

```lean
[Float.Model.UnpackedFloat.toUInt8]](#manual-Float___Model___UnpackedFloat___toUInt8) (f : [Float.Model.UnpackedFloat]](#manual-Float___Model___UnpackedFloat___infinity)) :
  [UInt8]](#manual-UInt8___ofBitVec)



[Float.Model.UnpackedFloat.toUInt8]](#manual-Float___Model___UnpackedFloat___toUInt8)
  (f : [Float.Model.UnpackedFloat]](#manual-Float___Model___UnpackedFloat___infinity)) : [UInt8]](#manual-UInt8___ofBitVec)
```

Converts an `UnpackedFloat` to a `[UInt8]](#manual-UInt8___ofBitVec)`, truncating after the decimal point, sending `NaN` to
`0` and clamping out-of-range values and infinities.

def

```lean
[Float.Model.UnpackedFloat.ofUInt8]](#manual-Float___Model___UnpackedFloat___ofUInt8) (spec : Float.Model.Format)
  (n : [UInt8]](#manual-UInt8___ofBitVec)) : [Float.Model.UnpackedFloat]](#manual-Float___Model___UnpackedFloat___infinity)



[Float.Model.UnpackedFloat.ofUInt8]](#manual-Float___Model___UnpackedFloat___ofUInt8)
  (spec : Float.Model.Format)
  (n : [UInt8]](#manual-UInt8___ofBitVec)) : [Float.Model.UnpackedFloat]](#manual-Float___Model___UnpackedFloat___infinity)
```

Converts a `[UInt8]](#manual-UInt8___ofBitVec)` to an `UnpackedFloat`, returning positive zero on zero.

def

```lean
[Float.Model.UnpackedFloat.toUInt16]](#manual-Float___Model___UnpackedFloat___toUInt16) (f : [Float.Model.UnpackedFloat]](#manual-Float___Model___UnpackedFloat___infinity)) :
  [UInt16]](#manual-UInt16___ofBitVec)



[Float.Model.UnpackedFloat.toUInt16]](#manual-Float___Model___UnpackedFloat___toUInt16)
  (f : [Float.Model.UnpackedFloat]](#manual-Float___Model___UnpackedFloat___infinity)) : [UInt16]](#manual-UInt16___ofBitVec)
```

Converts an `UnpackedFloat` to a `[UInt16]](#manual-UInt16___ofBitVec)`, truncating after the decimal point, sending `NaN` to
`0` and clamping out-of-range values and infinities.

def

```lean
[Float.Model.UnpackedFloat.ofUInt16]](#manual-Float___Model___UnpackedFloat___ofUInt16) (spec : Float.Model.Format)
  (n : [UInt16]](#manual-UInt16___ofBitVec)) : [Float.Model.UnpackedFloat]](#manual-Float___Model___UnpackedFloat___infinity)



[Float.Model.UnpackedFloat.ofUInt16]](#manual-Float___Model___UnpackedFloat___ofUInt16)
  (spec : Float.Model.Format)
  (n : [UInt16]](#manual-UInt16___ofBitVec)) : [Float.Model.UnpackedFloat]](#manual-Float___Model___UnpackedFloat___infinity)
```

Converts a `[UInt16]](#manual-UInt16___ofBitVec)` to an `UnpackedFloat`, returning positive zero on zero.

def

```lean
[Float.Model.UnpackedFloat.toUInt32]](#manual-Float___Model___UnpackedFloat___toUInt32) (f : [Float.Model.UnpackedFloat]](#manual-Float___Model___UnpackedFloat___infinity)) :
  [UInt32]](#manual-UInt32___ofBitVec)



[Float.Model.UnpackedFloat.toUInt32]](#manual-Float___Model___UnpackedFloat___toUInt32)
  (f : [Float.Model.UnpackedFloat]](#manual-Float___Model___UnpackedFloat___infinity)) : [UInt32]](#manual-UInt32___ofBitVec)
```

Converts an `UnpackedFloat` to a `[UInt32]](#manual-UInt32___ofBitVec)`, truncating after the decimal point, sending `NaN` to
`0` and clamping out-of-range values and infinities.

def

```lean
[Float.Model.UnpackedFloat.ofUInt32]](#manual-Float___Model___UnpackedFloat___ofUInt32) (spec : Float.Model.Format)
  (n : [UInt32]](#manual-UInt32___ofBitVec)) : [Float.Model.UnpackedFloat]](#manual-Float___Model___UnpackedFloat___infinity)



[Float.Model.UnpackedFloat.ofUInt32]](#manual-Float___Model___UnpackedFloat___ofUInt32)
  (spec : Float.Model.Format)
  (n : [UInt32]](#manual-UInt32___ofBitVec)) : [Float.Model.UnpackedFloat]](#manual-Float___Model___UnpackedFloat___infinity)
```

Converts a `[UInt32]](#manual-UInt32___ofBitVec)` to an `UnpackedFloat`, returning positive zero on zero.

def

```lean
[Float.Model.UnpackedFloat.toUInt64]](#manual-Float___Model___UnpackedFloat___toUInt64) (f : [Float.Model.UnpackedFloat]](#manual-Float___Model___UnpackedFloat___infinity)) :
  [UInt64]](#manual-UInt64___ofBitVec)



[Float.Model.UnpackedFloat.toUInt64]](#manual-Float___Model___UnpackedFloat___toUInt64)
  (f : [Float.Model.UnpackedFloat]](#manual-Float___Model___UnpackedFloat___infinity)) : [UInt64]](#manual-UInt64___ofBitVec)
```

Converts an `UnpackedFloat` to a `[UInt64]](#manual-UInt64___ofBitVec)`, truncating after the decimal point, sending `NaN` to
`0` and clamping out-of-range values and infinities.

def

```lean
[Float.Model.UnpackedFloat.ofUInt64]](#manual-Float___Model___UnpackedFloat___ofUInt64) (spec : Float.Model.Format)
  (n : [UInt64]](#manual-UInt64___ofBitVec)) : [Float.Model.UnpackedFloat]](#manual-Float___Model___UnpackedFloat___infinity)



[Float.Model.UnpackedFloat.ofUInt64]](#manual-Float___Model___UnpackedFloat___ofUInt64)
  (spec : Float.Model.Format)
  (n : [UInt64]](#manual-UInt64___ofBitVec)) : [Float.Model.UnpackedFloat]](#manual-Float___Model___UnpackedFloat___infinity)
```

Converts a `[UInt64]](#manual-UInt64___ofBitVec)` to an `UnpackedFloat`, returning positive zero on zero.

def

```lean
[Float.Model.UnpackedFloat.toUSize]](#manual-Float___Model___UnpackedFloat___toUSize) (f : [Float.Model.UnpackedFloat]](#manual-Float___Model___UnpackedFloat___infinity)) :
  [USize]](#manual-USize___ofBitVec)



[Float.Model.UnpackedFloat.toUSize]](#manual-Float___Model___UnpackedFloat___toUSize)
  (f : [Float.Model.UnpackedFloat]](#manual-Float___Model___UnpackedFloat___infinity)) : [USize]](#manual-USize___ofBitVec)
```

Converts an `UnpackedFloat` to a `[USize]](#manual-USize___ofBitVec)`, truncating after the decimal point, sending `NaN` to
`0` and clamping out-of-range values and infinities.

def

```lean
[Float.Model.UnpackedFloat.ofUSize]](#manual-Float___Model___UnpackedFloat___ofUSize) (spec : Float.Model.Format)
  (n : [USize]](#manual-USize___ofBitVec)) : [Float.Model.UnpackedFloat]](#manual-Float___Model___UnpackedFloat___infinity)



[Float.Model.UnpackedFloat.ofUSize]](#manual-Float___Model___UnpackedFloat___ofUSize)
  (spec : Float.Model.Format)
  (n : [USize]](#manual-USize___ofBitVec)) : [Float.Model.UnpackedFloat]](#manual-Float___Model___UnpackedFloat___infinity)
```

Converts a `[USize]](#manual-USize___ofBitVec)` to an `UnpackedFloat`, returning positive zero on zero.

**Example: Kernel Reasoning**

The Lean kernel can compare expressions of type `[Float]](#manual-Float___ofModel)` for syntactic equality, so `0.0` is definitionally equal to itself.

```lean
example : (0.0 : [Float]](#manual-Float___ofModel)) = (0.0 : [Float]](#manual-Float___ofModel)) := by⊢ 0.0 [=]](#manual-Eq___refl) 0.0 [rfl]](#manual-rfl)All goals completed! 🐙
```

Additionally, terms that require reduction to become syntactically equal can be checked by the kernel when they use only operations that are modeled in Lean's logic:

```lean
example : (0.0 : [Float]](#manual-Float___ofModel)) = (0.0 + 0.0 : [Float]](#manual-Float___ofModel)) := by⊢ 0.0 [=]](#manual-Eq___refl) 0.0 [+]](#manual-HAdd___mk) 0.0 [rfl]](#manual-rfl)All goals completed! 🐙
```

The kernel cannot reduce terms that use operations that are not directly modeled, such as trigonometric functions:

```lean
example : (0.0 : [Float]](#manual-Float___ofModel)).[sin]](#manual-Float___sin) = (0.0 : [Float]](#manual-Float___ofModel)) := by⊢ [Float.sin]](#manual-Float___sin) 0.0 [=]](#manual-Eq___refl) 0.0 [rfl]](#manual-rfl)⊢ [Float.sin]](#manual-Float___sin) 0.0 [=]](#manual-Eq___refl) 0.0
```

```lean
Tactic `rfl` failed: The left-hand side
  [Float.sin]](#manual-Float___sin) 0.0
is not definitionally equal to the right-hand side
  0.0

⊢ [Float.sin]](#manual-Float___sin) 0.0 [=]](#manual-Eq___refl) 0.0
```

However, the `[native_decide]](#manual-native_decide)` tactic can invoke the underlying platform's floating-point primitives that are used by Lean for run-time programs:

```lean
theorem Float.sin_zero_eq_zero :
((0.0 : [Float]](#manual-Float___ofModel)).[sin]](#manual-Float___sin) == (0.0 : [Float]](#manual-Float___ofModel))) = [true](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false) := by⊢ [(]](#manual-BEq___mk)[sin]](#manual-Float___sin) 0.0 [==]](#manual-BEq___mk) 0.0[)]](#manual-BEq___mk) [=]](#manual-Eq___refl) [true](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)
[native_decide]](#manual-native_decide)All goals completed! 🐙
```

This tactic executes a decision procedure as compiled native code.
This requires trusting the Lean compiler, interpreter and the low-level implementations of built-in operators in addition to the kernel.
To make this dependency precisely clear, the tactic creates the axiom `Float.sin_zero_eq_zero._native.native_decide.ax_1`:

```lean
[#print]](#manual-Lean___Parser___Command___printAxioms) [axioms]](#manual-Lean___Parser___Command___printAxioms) [Float.sin_zero_eq_zero]](#manual-Float___sin_zero_eq_zero-_LPAR_in-Kernel-Reasoning_RPAR_)
```

```lean
'Float.sin_zero_eq_zero' depends on axioms: [[propext]](#manual-propext),
 Classical.choice,
 [Quot.sound]](#manual-Quot___sound),
 Float.sin_zero_eq_zero._native.native_decide.ax_1]
```

**Example: Floating-Point Equality Is Not Reflexive**

Floating-point operations may produce `NaN` values that indicate an undefined result.
These values are not comparable with each other; in particular, all comparisons involving `NaN` will return `false`, including equality.

```lean
[#eval]](#manual-Lean___Parser___Command___eval) ((0.0 : [Float]](#manual-Float___ofModel)) / 0.0) == ((0.0 : [Float]](#manual-Float___ofModel)) / 0.0)
```

**Example: Floating-Point Equality Is Not a Congruence**

Applying a function to two equal floating-point numbers may not result in equal numbers.
In particular, positive and negative zero are distinct values that are equated by floating-point equality, but division by positive or negative zero yields positive or negative infinite values.

```lean
def neg0 : [Float]](#manual-Float___ofModel) := -0.0
def pos0 : [Float]](#manual-Float___ofModel) := 0.0
[#eval]](#manual-Lean___Parser___Command___eval) ([neg0]](#manual-neg0-_LPAR_in-Floating-Point-Equality-Is-Not-a-Congruence_RPAR_) == [pos0]](#manual-pos0-_LPAR_in-Floating-Point-Equality-Is-Not-a-Congruence_RPAR_), 1.0 / [neg0]](#manual-neg0-_LPAR_in-Floating-Point-Equality-Is-Not-a-Congruence_RPAR_) == 1.0 / [pos0]](#manual-pos0-_LPAR_in-Floating-Point-Equality-Is-Not-a-Congruence_RPAR_))
```

```lean
[(](https://lean-lang.org/doc/reference/latest/Basic-Types/Tuples/#Prod___mk)[true](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)[,](https://lean-lang.org/doc/reference/latest/Basic-Types/Tuples/#Prod___mk) [false](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)[)](https://lean-lang.org/doc/reference/latest/Basic-Types/Tuples/#Prod___mk)
```

### 20.6.2. Syntax {#manual-The-Lean-Language-Reference--Basic-Types--Floating-Point-Numbers--Syntax}

Lean does not have dedicated floating-point literals.
Instead, floating-point literals are resolved via the appropriate instances of the `[OfScientific]](#manual-OfScientific___mk)` and `[Neg]](#manual-Neg___mk)` type classes.

**Example: Floating-Point Literals**

The term

```lean
(-2.523 : [Float]](#manual-Float___ofModel))
```

is syntactic sugar for

```lean
([Neg.neg]](#manual-Neg___mk) ([OfScientific.ofScientific]](#manual-OfScientific___mk) 22523 [true](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false) 4) : [Float]](#manual-Float___ofModel))
```

and the term

```lean
(413.52 : [Float32]](#manual-Float32___ofModel))
```

is syntactic sugar for

```lean
([OfScientific.ofScientific]](#manual-OfScientific___mk) 41352 [true](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false) 2 : [Float32]](#manual-Float32___ofModel))
```

### 20.6.3. API Reference {#manual-Float-api}

#### 20.6.3.1. Properties {#manual-The-Lean-Language-Reference--Basic-Types--Floating-Point-Numbers--API-Reference--Properties}

Floating-point numbers fall into one of three categories:

- Finite numbers are ordinary floating-point values.
- Infinities, which may be positive or negative, result from division by zero.
- `NaN`s, which are not numbers, result from other undefined operations, such as the square root of a negative number.

def

```lean
[Float.isInf]](#manual-Float___isInf) : [Float]](#manual-Float___ofModel) → [Bool](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)



[Float.isInf]](#manual-Float___isInf) : [Float]](#manual-Float___ofModel) → [Bool](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)
```

Checks whether a floating-point number is a positive or negative infinite number, but not a finite
number or `NaN`.

This function has a logical model in terms of `[Float.Model]](#manual-Float___Model___mk)`. It is compiled to the C operator `isinf`.

def

```lean
[Float32.isInf]](#manual-Float32___isInf) : [Float32]](#manual-Float32___ofModel) → [Bool](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)



[Float32.isInf]](#manual-Float32___isInf) : [Float32]](#manual-Float32___ofModel) → [Bool](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)
```

Checks whether a floating-point number is a positive or negative infinite number, but not a finite
number or `NaN`.

This function has a logical model in terms of `[Float32.Model]](#manual-Float32___Model___mk)`. It is compiled to the C operator `isinf`.

def

```lean
[Float.isNaN]](#manual-Float___isNaN) : [Float]](#manual-Float___ofModel) → [Bool](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)



[Float.isNaN]](#manual-Float___isNaN) : [Float]](#manual-Float___ofModel) → [Bool](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)
```

Checks whether a floating point number is a `NaN` (“not a number”) value.

`NaN` values result from operations that might otherwise be errors, such as dividing zero by zero.

This function returns `[true](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)` if and only if the input is propositionally equal to `Float.nan`.

This function has a logical model in terms of `[Float.Model]](#manual-Float___Model___mk)`. It is compiled to the C operator `isnan`.

def

```lean
[Float32.isNaN]](#manual-Float32___isNaN) : [Float32]](#manual-Float32___ofModel) → [Bool](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)



[Float32.isNaN]](#manual-Float32___isNaN) : [Float32]](#manual-Float32___ofModel) → [Bool](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)
```

Checks whether a floating point number is a `NaN` ("not a number") value.

`NaN` values result from operations that might otherwise be errors, such as dividing zero by zero.

This function returns `[true](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)` if and only if the input is propositionally equal to `Float32.nan`.

This function has a logical model in terms of `[Float32.Model]](#manual-Float32___Model___mk)`. It is compiled to the C operator `isnan`.

def

```lean
[Float.isFinite]](#manual-Float___isFinite) : [Float]](#manual-Float___ofModel) → [Bool](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)



[Float.isFinite]](#manual-Float___isFinite) : [Float]](#manual-Float___ofModel) → [Bool](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)
```

Checks whether a floating-point number is finite, that is, whether it is normal, subnormal, or zero,
but not infinite or `NaN`.

This function has a logical model in terms of `[Float.Model]](#manual-Float___Model___mk)`. It is compiled to the C operator `isfinite`.

def

```lean
[Float32.isFinite]](#manual-Float32___isFinite) : [Float32]](#manual-Float32___ofModel) → [Bool](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)



[Float32.isFinite]](#manual-Float32___isFinite) : [Float32]](#manual-Float32___ofModel) → [Bool](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)
```

Checks whether a floating-point number is finite, that is, whether it is normal, subnormal, or zero,
but not infinite or `NaN`.

This function has a logical model in terms of `[Float32.Model]](#manual-Float32___Model___mk)`. It is compiled to the C operator `isfinite`.

#### 20.6.3.2. Conversions {#manual-The-Lean-Language-Reference--Basic-Types--Floating-Point-Numbers--API-Reference--Conversions}

def

```lean
[Float.toBits]](#manual-Float___toBits) : [Float]](#manual-Float___ofModel) → [UInt64]](#manual-UInt64___ofBitVec)



[Float.toBits]](#manual-Float___toBits) : [Float]](#manual-Float___ofModel) → [UInt64]](#manual-UInt64___ofBitVec)
```

Bit-for-bit conversion to `[UInt64]](#manual-UInt64___ofBitVec)`. Interprets a `[Float]](#manual-Float___ofModel)` as a `[UInt64]](#manual-UInt64___ofBitVec)`, ignoring the numeric value
and treating the `[Float]](#manual-Float___ofModel)`'s bit pattern as a `[UInt64]](#manual-UInt64___ofBitVec)`.

`[Float]](#manual-Float___ofModel)`s and `[UInt64]](#manual-UInt64___ofBitVec)`s have the same endianness on all supported platforms. IEEE 754 very precisely
specifies the bit layout of floats.

This function is distinct from `[Float.toUInt64]](#manual-Float___toUInt64)`, which attempts to preserve the numeric value rather
than reinterpreting the bit pattern.

def

```lean
[Float32.toBits]](#manual-Float32___toBits) : [Float32]](#manual-Float32___ofModel) → [UInt32]](#manual-UInt32___ofBitVec)



[Float32.toBits]](#manual-Float32___toBits) : [Float32]](#manual-Float32___ofModel) → [UInt32]](#manual-UInt32___ofBitVec)
```

Bit-for-bit conversion to `[UInt32]](#manual-UInt32___ofBitVec)`. Interprets a `[Float32]](#manual-Float32___ofModel)` as a `[UInt32]](#manual-UInt32___ofBitVec)`, ignoring the numeric value
and treating the `[Float32]](#manual-Float32___ofModel)`'s bit pattern as a `[UInt32]](#manual-UInt32___ofBitVec)`.

`[Float32]](#manual-Float32___ofModel)`s and `[UInt32]](#manual-UInt32___ofBitVec)`s have the same endianness on all supported platforms. IEEE 754 very
precisely specifies the bit layout of floats.

This function is distinct from `[Float.toUInt32]](#manual-Float___toUInt32)`, which attempts to preserve the numeric value rather
than reinterpreting the bit pattern.

def

```lean
[Float.ofBits]](#manual-Float___ofBits) : [UInt64]](#manual-UInt64___ofBitVec) → [Float]](#manual-Float___ofModel)



[Float.ofBits]](#manual-Float___ofBits) : [UInt64]](#manual-UInt64___ofBitVec) → [Float]](#manual-Float___ofModel)
```

Bit-for-bit conversion from `[UInt64]](#manual-UInt64___ofBitVec)`. Interprets a `[UInt64]](#manual-UInt64___ofBitVec)` as a `[Float]](#manual-Float___ofModel)`, ignoring the numeric value
and treating the `[UInt64]](#manual-UInt64___ofBitVec)`'s bit pattern as a `[Float]](#manual-Float___ofModel)`.

`[Float]](#manual-Float___ofModel)`s and `[UInt64]](#manual-UInt64___ofBitVec)`s have the same endianness on all supported platforms. IEEE 754 very precisely
specifies the bit layout of floats.

This function has a logical model in terms of `[Float.Model]](#manual-Float___Model___mk)`.

def

```lean
[Float32.ofBits]](#manual-Float32___ofBits) : [UInt32]](#manual-UInt32___ofBitVec) → [Float32]](#manual-Float32___ofModel)



[Float32.ofBits]](#manual-Float32___ofBits) : [UInt32]](#manual-UInt32___ofBitVec) → [Float32]](#manual-Float32___ofModel)
```

Bit-for-bit conversion from `[UInt32]](#manual-UInt32___ofBitVec)`. Interprets a `[UInt32]](#manual-UInt32___ofBitVec)` as a `[Float32]](#manual-Float32___ofModel)`, ignoring the numeric
value and treating the `[UInt32]](#manual-UInt32___ofBitVec)`'s bit pattern as a `[Float32]](#manual-Float32___ofModel)`.

`[Float32]](#manual-Float32___ofModel)`s and `[UInt32]](#manual-UInt32___ofBitVec)`s have the same endianness on all supported platforms. IEEE 754 very
precisely specifies the bit layout of floats.

This function has a logical model in terms of `[Float32.Model]](#manual-Float32___Model___mk)`.

opaque

```lean
[Float.toFloat32]](#manual-Float___toFloat32) : [Float]](#manual-Float___ofModel) → [Float32]](#manual-Float32___ofModel)



[Float.toFloat32]](#manual-Float___toFloat32) : [Float]](#manual-Float___ofModel) → [Float32]](#manual-Float32___ofModel)
```

Converts a 64-bit floating-point number to a 32-bit floating-point number.
This may lose precision.

This function does not reduce in the kernel.

opaque

```lean
[Float32.toFloat]](#manual-Float32___toFloat) : [Float32]](#manual-Float32___ofModel) → [Float]](#manual-Float___ofModel)



[Float32.toFloat]](#manual-Float32___toFloat) : [Float32]](#manual-Float32___ofModel) → [Float]](#manual-Float___ofModel)
```

Converts a 32-bit floating-point number to a 64-bit floating-point number.

This function does not reduce in the kernel.

opaque

```lean
[Float.toString]](#manual-Float___toString) : [Float]](#manual-Float___ofModel) → [String](https://lean-lang.org/doc/reference/latest/Basic-Types/Strings/#String___ofByteArray)



[Float.toString]](#manual-Float___toString) : [Float]](#manual-Float___ofModel) → [String](https://lean-lang.org/doc/reference/latest/Basic-Types/Strings/#String___ofByteArray)
```

Converts a floating-point number to a string.

This function does not reduce in the kernel.

opaque

```lean
[Float32.toString]](#manual-Float32___toString) : [Float32]](#manual-Float32___ofModel) → [String](https://lean-lang.org/doc/reference/latest/Basic-Types/Strings/#String___ofByteArray)



[Float32.toString]](#manual-Float32___toString) : [Float32]](#manual-Float32___ofModel) → [String](https://lean-lang.org/doc/reference/latest/Basic-Types/Strings/#String___ofByteArray)
```

Converts a floating-point number to a string.

This function does not reduce in the kernel.

def

```lean
[Float.toUInt8]](#manual-Float___toUInt8) : [Float]](#manual-Float___ofModel) → [UInt8]](#manual-UInt8___ofBitVec)



[Float.toUInt8]](#manual-Float___toUInt8) : [Float]](#manual-Float___ofModel) → [UInt8]](#manual-UInt8___ofBitVec)
```

Converts a floating-point number to an 8-bit unsigned integer.

If the given `[Float]](#manual-Float___ofModel)` is non-negative, truncates the value to a positive integer, rounding down and
clamping to the range of `[UInt8]](#manual-UInt8___ofBitVec)`. Returns `0` if the `[Float]](#manual-Float___ofModel)` is negative or `NaN`, and returns the
largest `[UInt8]](#manual-UInt8___ofBitVec)` value (i.e. `[UInt8.size]](#manual-UInt8___size) - 1`) if the float is larger than it.

This function has a logical model in terms of `[Float.Model]](#manual-Float___Model___mk)`.

def

```lean
[Float.toInt8]](#manual-Float___toInt8) : [Float]](#manual-Float___ofModel) → [Int8]](#manual-Int8___ofUInt8)



[Float.toInt8]](#manual-Float___toInt8) : [Float]](#manual-Float___ofModel) → [Int8]](#manual-Int8___ofUInt8)
```

Truncates a floating-point number to the nearest 8-bit signed integer, rounding towards zero.

If the `[Float]](#manual-Float___ofModel)` is larger than the maximum value for `[Int8]](#manual-Int8___ofUInt8)` (including `Inf`), returns the maximum value of
`[Int8]](#manual-Int8___ofUInt8)` (i.e. `[Int8.maxValue]](#manual-Int8___maxValue)`). If it is smaller than the minimum value for `[Int8]](#manual-Int8___ofUInt8)` (including `-Inf`),
returns the minimum value of `[Int8]](#manual-Int8___ofUInt8)` (i.e. `[Int8.minValue]](#manual-Int8___minValue)`). If it is `NaN`, returns `0`.

This function has a logical model in terms of `[Float.Model]](#manual-Float___Model___mk)`.

def

```lean
[Float32.toUInt8]](#manual-Float32___toUInt8) : [Float32]](#manual-Float32___ofModel) → [UInt8]](#manual-UInt8___ofBitVec)



[Float32.toUInt8]](#manual-Float32___toUInt8) : [Float32]](#manual-Float32___ofModel) → [UInt8]](#manual-UInt8___ofBitVec)
```

Converts a floating-point number to an 8-bit unsigned integer.

If the given `[Float32]](#manual-Float32___ofModel)` is non-negative, truncates the value to a positive integer, rounding down and
clamping to the range of `[UInt8]](#manual-UInt8___ofBitVec)`. Returns `0` if the `[Float32]](#manual-Float32___ofModel)` is negative or `NaN`, and returns the
largest `[UInt8]](#manual-UInt8___ofBitVec)` value (i.e. `[UInt8.size]](#manual-UInt8___size) - 1`) if the float is larger than it.

This function has a logical model in terms of `[Float32.Model]](#manual-Float32___Model___mk)`.

def

```lean
[Float32.toInt8]](#manual-Float32___toInt8) : [Float32]](#manual-Float32___ofModel) → [Int8]](#manual-Int8___ofUInt8)



[Float32.toInt8]](#manual-Float32___toInt8) : [Float32]](#manual-Float32___ofModel) → [Int8]](#manual-Int8___ofUInt8)
```

Truncates a floating-point number to the nearest 8-bit signed integer, rounding towards zero.

If the `[Float]](#manual-Float___ofModel)` is larger than the maximum value for `[Int8]](#manual-Int8___ofUInt8)` (including `Inf`), returns the maximum value of
`[Int8]](#manual-Int8___ofUInt8)` (i.e. `[Int8.maxValue]](#manual-Int8___maxValue)`). If it is smaller than the minimum value for `[Int8]](#manual-Int8___ofUInt8)` (including `-Inf`),
returns the minimum value of `[Int8]](#manual-Int8___ofUInt8)` (i.e. `[Int8.minValue]](#manual-Int8___minValue)`). If it is `NaN`, returns `0`.

This function has a logical model in terms of `[Float32.Model]](#manual-Float32___Model___mk)`.

def

```lean
[Float.toUInt16]](#manual-Float___toUInt16) : [Float]](#manual-Float___ofModel) → [UInt16]](#manual-UInt16___ofBitVec)



[Float.toUInt16]](#manual-Float___toUInt16) : [Float]](#manual-Float___ofModel) → [UInt16]](#manual-UInt16___ofBitVec)
```

Converts a floating-point number to a 16-bit unsigned integer.

If the given `[Float]](#manual-Float___ofModel)` is non-negative, truncates the value to a positive integer, rounding down and
clamping to the range of `[UInt16]](#manual-UInt16___ofBitVec)`. Returns `0` if the `[Float]](#manual-Float___ofModel)` is negative or `NaN`, and returns the
largest `[UInt16]](#manual-UInt16___ofBitVec)` value (i.e. `[UInt16.size]](#manual-UInt16___size) - 1`) if the float is larger than it.

This function has a logical model in terms of `[Float.Model]](#manual-Float___Model___mk)`.

def

```lean
[Float.toInt16]](#manual-Float___toInt16) : [Float]](#manual-Float___ofModel) → [Int16]](#manual-Int16___ofUInt16)



[Float.toInt16]](#manual-Float___toInt16) : [Float]](#manual-Float___ofModel) → [Int16]](#manual-Int16___ofUInt16)
```

Truncates a floating-point number to the nearest 16-bit signed integer, rounding towards zero.

If the `[Float]](#manual-Float___ofModel)` is larger than the maximum value for `[Int16]](#manual-Int16___ofUInt16)` (including `Inf`), returns the maximum
value of `[Int16]](#manual-Int16___ofUInt16)` (i.e. `[Int16.maxValue]](#manual-Int16___maxValue)`). If it is smaller than the minimum value for `[Int16]](#manual-Int16___ofUInt16)`
(including `-Inf`), returns the minimum value of `[Int16]](#manual-Int16___ofUInt16)` (i.e. `[Int16.minValue]](#manual-Int16___minValue)`). If it is `NaN`,
returns `0`.

This function has a logical model in terms of `[Float.Model]](#manual-Float___Model___mk)`.

def

```lean
[Float32.toUInt16]](#manual-Float32___toUInt16) : [Float32]](#manual-Float32___ofModel) → [UInt16]](#manual-UInt16___ofBitVec)



[Float32.toUInt16]](#manual-Float32___toUInt16) : [Float32]](#manual-Float32___ofModel) → [UInt16]](#manual-UInt16___ofBitVec)
```

Converts a floating-point number to a 16-bit unsigned integer.

If the given `[Float32]](#manual-Float32___ofModel)` is non-negative, truncates the value to a positive integer, rounding down and
clamping to the range of `[UInt16]](#manual-UInt16___ofBitVec)`. Returns `0` if the `[Float32]](#manual-Float32___ofModel)` is negative or `NaN`, and returns
the largest `[UInt16]](#manual-UInt16___ofBitVec)` value (i.e. `[UInt16.size]](#manual-UInt16___size) - 1`) if the float is larger than it.

This function has a logical model in terms of `[Float32.Model]](#manual-Float32___Model___mk)`.

def

```lean
[Float32.toInt16]](#manual-Float32___toInt16) : [Float32]](#manual-Float32___ofModel) → [Int16]](#manual-Int16___ofUInt16)



[Float32.toInt16]](#manual-Float32___toInt16) : [Float32]](#manual-Float32___ofModel) → [Int16]](#manual-Int16___ofUInt16)
```

Truncates a floating-point number to the nearest 16-bit signed integer, rounding towards zero.

If the `[Float]](#manual-Float___ofModel)` is larger than the maximum value for `[Int16]](#manual-Int16___ofUInt16)` (including `Inf`), returns the maximum
value of `[Int16]](#manual-Int16___ofUInt16)` (i.e. `[Int16.maxValue]](#manual-Int16___maxValue)`). If it is smaller than the minimum value for `[Int16]](#manual-Int16___ofUInt16)`
(including `-Inf`), returns the minimum value of `[Int16]](#manual-Int16___ofUInt16)` (i.e. `[Int16.minValue]](#manual-Int16___minValue)`). If it is `NaN`,
returns `0`.

This function has a logical model in terms of `[Float32.Model]](#manual-Float32___Model___mk)`.

def

```lean
[Float.toUInt32]](#manual-Float___toUInt32) : [Float]](#manual-Float___ofModel) → [UInt32]](#manual-UInt32___ofBitVec)



[Float.toUInt32]](#manual-Float___toUInt32) : [Float]](#manual-Float___ofModel) → [UInt32]](#manual-UInt32___ofBitVec)
```

Converts a floating-point number to a 32-bit unsigned integer.

If the given `[Float]](#manual-Float___ofModel)` is non-negative, truncates the value to a positive integer, rounding down and
clamping to the range of `[UInt32]](#manual-UInt32___ofBitVec)`. Returns `0` if the `[Float]](#manual-Float___ofModel)` is negative or `NaN`, and returns the
largest `[UInt32]](#manual-UInt32___ofBitVec)` value (i.e. `[UInt32.size]](#manual-UInt32___size) - 1`) if the float is larger than it.

This function has a logical model in terms of `[Float.Model]](#manual-Float___Model___mk)`.

def

```lean
[Float32.toUInt32]](#manual-Float32___toUInt32) : [Float32]](#manual-Float32___ofModel) → [UInt32]](#manual-UInt32___ofBitVec)



[Float32.toUInt32]](#manual-Float32___toUInt32) : [Float32]](#manual-Float32___ofModel) → [UInt32]](#manual-UInt32___ofBitVec)
```

Converts a floating-point number to a 32-bit unsigned integer.

If the given `[Float32]](#manual-Float32___ofModel)` is non-negative, truncates the value to a positive integer, rounding down and
clamping to the range of `[UInt32]](#manual-UInt32___ofBitVec)`. Returns `0` if the `[Float32]](#manual-Float32___ofModel)` is negative or `NaN`, and returns
the largest `[UInt32]](#manual-UInt32___ofBitVec)` value (i.e. `[UInt32.size]](#manual-UInt32___size) - 1`) if the float is larger than it.

This function has a logical model in terms of `[Float32.Model]](#manual-Float32___Model___mk)`.

def

```lean
[Float.toInt32]](#manual-Float___toInt32) : [Float]](#manual-Float___ofModel) → [Int32]](#manual-Int32___ofUInt32)



[Float.toInt32]](#manual-Float___toInt32) : [Float]](#manual-Float___ofModel) → [Int32]](#manual-Int32___ofUInt32)
```

Truncates a floating-point number to the nearest 32-bit signed integer, rounding towards zero.

If the `[Float]](#manual-Float___ofModel)` is larger than the maximum value for `[Int32]](#manual-Int32___ofUInt32)` (including `Inf`), returns the maximum
value of `[Int32]](#manual-Int32___ofUInt32)` (i.e. `[Int32.maxValue]](#manual-Int32___maxValue)`). If it is smaller than the minimum value for `[Int32]](#manual-Int32___ofUInt32)`
(including `-Inf`), returns the minimum value of `[Int32]](#manual-Int32___ofUInt32)` (i.e. `[Int32.minValue]](#manual-Int32___minValue)`). If it is `NaN`,
returns `0`.

This function has a logical model in terms of `[Float.Model]](#manual-Float___Model___mk)`.

def

```lean
[Float32.toInt32]](#manual-Float32___toInt32) : [Float32]](#manual-Float32___ofModel) → [Int32]](#manual-Int32___ofUInt32)



[Float32.toInt32]](#manual-Float32___toInt32) : [Float32]](#manual-Float32___ofModel) → [Int32]](#manual-Int32___ofUInt32)
```

Truncates a floating-point number to the nearest 32-bit signed integer, rounding towards zero.

If the `[Float]](#manual-Float___ofModel)` is larger than the maximum value for `[Int32]](#manual-Int32___ofUInt32)` (including `Inf`), returns the maximum
value of `[Int32]](#manual-Int32___ofUInt32)` (i.e. `[Int32.maxValue]](#manual-Int32___maxValue)`). If it is smaller than the minimum value for `[Int32]](#manual-Int32___ofUInt32)`
(including `-Inf`), returns the minimum value of `[Int32]](#manual-Int32___ofUInt32)` (i.e. `[Int32.minValue]](#manual-Int32___minValue)`). If it is `NaN`,
returns `0`.

This function has a logical model in terms of `[Float32.Model]](#manual-Float32___Model___mk)`.

def

```lean
[Float.toUInt64]](#manual-Float___toUInt64) : [Float]](#manual-Float___ofModel) → [UInt64]](#manual-UInt64___ofBitVec)



[Float.toUInt64]](#manual-Float___toUInt64) : [Float]](#manual-Float___ofModel) → [UInt64]](#manual-UInt64___ofBitVec)
```

Converts a floating-point number to a 64-bit unsigned integer.

If the given `[Float]](#manual-Float___ofModel)` is non-negative, truncates the value to a positive integer, rounding down and
clamping to the range of `[UInt64]](#manual-UInt64___ofBitVec)`. Returns `0` if the `[Float]](#manual-Float___ofModel)` is negative or `NaN`, and returns the
largest `[UInt64]](#manual-UInt64___ofBitVec)` value (i.e. `[UInt64.size]](#manual-UInt64___size) - 1`) if the float is larger than it.

This function has a logical model in terms of `[Float.Model]](#manual-Float___Model___mk)`.

def

```lean
[Float.toInt64]](#manual-Float___toInt64) : [Float]](#manual-Float___ofModel) → [Int64]](#manual-Int64___ofUInt64)



[Float.toInt64]](#manual-Float___toInt64) : [Float]](#manual-Float___ofModel) → [Int64]](#manual-Int64___ofUInt64)
```

Truncates a floating-point number to the nearest 64-bit signed integer, rounding towards zero.

If the `[Float]](#manual-Float___ofModel)` is larger than the maximum value for `[Int64]](#manual-Int64___ofUInt64)` (including `Inf`), returns the maximum
value of `[Int64]](#manual-Int64___ofUInt64)` (i.e. `[Int64.maxValue]](#manual-Int64___maxValue)`). If it is smaller than the minimum value for `[Int64]](#manual-Int64___ofUInt64)`
(including `-Inf`), returns the minimum value of `[Int64]](#manual-Int64___ofUInt64)` (i.e. `[Int64.minValue]](#manual-Int64___minValue)`). If it is `NaN`,
returns `0`.

This function has a logical model in terms of `[Float.Model]](#manual-Float___Model___mk)`.

def

```lean
[Float32.toUInt64]](#manual-Float32___toUInt64) : [Float32]](#manual-Float32___ofModel) → [UInt64]](#manual-UInt64___ofBitVec)



[Float32.toUInt64]](#manual-Float32___toUInt64) : [Float32]](#manual-Float32___ofModel) → [UInt64]](#manual-UInt64___ofBitVec)
```

Converts a floating-point number to a 64-bit unsigned integer.

If the given `[Float32]](#manual-Float32___ofModel)` is non-negative, truncates the value to a positive integer, rounding down and
clamping to the range of `[UInt64]](#manual-UInt64___ofBitVec)`. Returns `0` if the `[Float32]](#manual-Float32___ofModel)` is negative or `NaN`, and returns
the largest `[UInt64]](#manual-UInt64___ofBitVec)` value (i.e. `[UInt64.size]](#manual-UInt64___size) - 1`) if the float is larger than it.

This function has a logical model in terms of `[Float32.Model]](#manual-Float32___Model___mk)`.

def

```lean
[Float32.toInt64]](#manual-Float32___toInt64) : [Float32]](#manual-Float32___ofModel) → [Int64]](#manual-Int64___ofUInt64)



[Float32.toInt64]](#manual-Float32___toInt64) : [Float32]](#manual-Float32___ofModel) → [Int64]](#manual-Int64___ofUInt64)
```

Truncates a floating-point number to the nearest 64-bit signed integer, rounding towards zero.

If the `[Float]](#manual-Float___ofModel)` is larger than the maximum value for `[Int64]](#manual-Int64___ofUInt64)` (including `Inf`), returns the maximum
value of `[Int64]](#manual-Int64___ofUInt64)` (i.e. `[Int64.maxValue]](#manual-Int64___maxValue)`). If it is smaller than the minimum value for `[Int64]](#manual-Int64___ofUInt64)`
(including `-Inf`), returns the minimum value of `[Int64]](#manual-Int64___ofUInt64)` (i.e. `[Int64.minValue]](#manual-Int64___minValue)`). If it is `NaN`,
returns `0`.

This function has a logical model in terms of `[Float32.Model]](#manual-Float32___Model___mk)`.

def

```lean
[Float.toUSize]](#manual-Float___toUSize) : [Float]](#manual-Float___ofModel) → [USize]](#manual-USize___ofBitVec)



[Float.toUSize]](#manual-Float___toUSize) : [Float]](#manual-Float___ofModel) → [USize]](#manual-USize___ofBitVec)
```

Converts a floating-point number to a word-sized unsigned integer.

If the given `[Float]](#manual-Float___ofModel)` is non-negative, truncates the value to a positive integer, rounding down and
clamping to the range of `[USize]](#manual-USize___ofBitVec)`. Returns `0` if the `[Float]](#manual-Float___ofModel)` is negative or `NaN`, and returns the
largest `[USize]](#manual-USize___ofBitVec)` value (i.e. `[USize.size]](#manual-USize___size) - 1`) if the float is larger than it.

This function has a logical model in terms of `[Float.Model]](#manual-Float___Model___mk)`.

def

```lean
[Float32.toUSize]](#manual-Float32___toUSize) : [Float32]](#manual-Float32___ofModel) → [USize]](#manual-USize___ofBitVec)



[Float32.toUSize]](#manual-Float32___toUSize) : [Float32]](#manual-Float32___ofModel) → [USize]](#manual-USize___ofBitVec)
```

Converts a floating-point number to a word-sized unsigned integer.

If the given `[Float32]](#manual-Float32___ofModel)` is non-negative, truncates the value to a positive integer, rounding down and
clamping to the range of `[USize]](#manual-USize___ofBitVec)`. Returns `0` if the `[Float32]](#manual-Float32___ofModel)` is negative or `NaN`, and returns the
largest `[USize]](#manual-USize___ofBitVec)` value (i.e. `[USize.size]](#manual-USize___size) - 1`) if the float is larger than it.

This function has a logical model in terms of `[Float32.Model]](#manual-Float32___Model___mk)`.

def

```lean
[Float.toISize]](#manual-Float___toISize) : [Float]](#manual-Float___ofModel) → [ISize]](#manual-ISize___ofUSize)



[Float.toISize]](#manual-Float___toISize) : [Float]](#manual-Float___ofModel) → [ISize]](#manual-ISize___ofUSize)
```

Truncates a floating-point number to the nearest word-sized signed integer, rounding towards zero.

If the `[Float]](#manual-Float___ofModel)` is larger than the maximum value for `[ISize]](#manual-ISize___ofUSize)` (including `Inf`), returns the maximum
value of `[ISize]](#manual-ISize___ofUSize)` (i.e. `[ISize.maxValue]](#manual-ISize___maxValue)`). If it is smaller than the minimum value for `[ISize]](#manual-ISize___ofUSize)`
(including `-Inf`), returns the minimum value of `[ISize]](#manual-ISize___ofUSize)` (i.e. `[ISize.minValue]](#manual-ISize___minValue)`). If it is `NaN`,
returns `0`.

This function has a logical model in terms of `[Float.Model]](#manual-Float___Model___mk)`.

def

```lean
[Float32.toISize]](#manual-Float32___toISize) : [Float32]](#manual-Float32___ofModel) → [ISize]](#manual-ISize___ofUSize)



[Float32.toISize]](#manual-Float32___toISize) : [Float32]](#manual-Float32___ofModel) → [ISize]](#manual-ISize___ofUSize)
```

Truncates a floating-point number to the nearest word-sized signed integer, rounding towards zero.

If the `[Float]](#manual-Float___ofModel)` is larger than the maximum value for `[ISize]](#manual-ISize___ofUSize)` (including `Inf`), returns the maximum
value of `[ISize]](#manual-ISize___ofUSize)` (i.e. `[ISize.maxValue]](#manual-ISize___maxValue)`). If it is smaller than the minimum value for `[ISize]](#manual-ISize___ofUSize)`
(including `-Inf`), returns the minimum value of `[ISize]](#manual-ISize___ofUSize)` (i.e. `[ISize.minValue]](#manual-ISize___minValue)`). If it is `NaN`,
returns `0`.

This function has a logical model in terms of `[Float32.Model]](#manual-Float32___Model___mk)`.

def

```lean
[Float.ofInt]](#manual-Float___ofInt) : [Int]](#manual-Int___ofNat) → [Float]](#manual-Float___ofModel)



[Float.ofInt]](#manual-Float___ofInt) : [Int]](#manual-Int___ofNat) → [Float]](#manual-Float___ofModel)
```

Converts an integer into the closest-possible 64-bit floating-point number, or positive or negative
infinite floating-point value if the range of `[Float]](#manual-Float___ofModel)` is exceeded.

def

```lean
[Float32.ofInt]](#manual-Float32___ofInt) : [Int]](#manual-Int___ofNat) → [Float32]](#manual-Float32___ofModel)



[Float32.ofInt]](#manual-Float32___ofInt) : [Int]](#manual-Int___ofNat) → [Float32]](#manual-Float32___ofModel)
```

Converts an integer into the closest-possible 32-bit floating-point number, or positive or negative
infinite floating-point value if the range of `[Float32]](#manual-Float32___ofModel)` is exceeded.

def

```lean
[Float.ofNat]](#manual-Float___ofNat) (n : [Nat]](#manual-Nat___zero)) : [Float]](#manual-Float___ofModel)



[Float.ofNat]](#manual-Float___ofNat) (n : [Nat]](#manual-Nat___zero)) : [Float]](#manual-Float___ofModel)
```

Converts a natural number into the closest-possible 64-bit floating-point number, or an infinite
floating-point value if the range of `[Float]](#manual-Float___ofModel)` is exceeded.

def

```lean
[Float32.ofNat]](#manual-Float32___ofNat) (n : [Nat]](#manual-Nat___zero)) : [Float32]](#manual-Float32___ofModel)



[Float32.ofNat]](#manual-Float32___ofNat) (n : [Nat]](#manual-Nat___zero)) : [Float32]](#manual-Float32___ofModel)
```

Converts a natural number into the closest-possible 32-bit floating-point number, or an infinite
floating-point value if the range of `[Float32]](#manual-Float32___ofModel)` is exceeded.

opaque

```lean
[Float.frExp]](#manual-Float___frExp) : [Float]](#manual-Float___ofModel) → [Float]](#manual-Float___ofModel) [×](https://lean-lang.org/doc/reference/latest/Basic-Types/Tuples/#Prod___mk) [Int]](#manual-Int___ofNat)



[Float.frExp]](#manual-Float___frExp) : [Float]](#manual-Float___ofModel) → [Float]](#manual-Float___ofModel) [×](https://lean-lang.org/doc/reference/latest/Basic-Types/Tuples/#Prod___mk) [Int]](#manual-Int___ofNat)
```

Splits the given float `x` into a significand/exponent pair `(s, i)` such that `x = s * 2^i` where
`s ∈ (-1;-0.5] ∪ [0.5; 1)`. Returns an undefined value if `x` is not finite.

This function does not reduce in the kernel. It is implemented in compiled code by the C function
`frexp`.

opaque

```lean
[Float32.frExp]](#manual-Float32___frExp) : [Float32]](#manual-Float32___ofModel) → [Float32]](#manual-Float32___ofModel) [×](https://lean-lang.org/doc/reference/latest/Basic-Types/Tuples/#Prod___mk) [Int]](#manual-Int___ofNat)



[Float32.frExp]](#manual-Float32___frExp) : [Float32]](#manual-Float32___ofModel) → [Float32]](#manual-Float32___ofModel) [×](https://lean-lang.org/doc/reference/latest/Basic-Types/Tuples/#Prod___mk) [Int]](#manual-Int___ofNat)
```

Splits the given float `x` into a significand/exponent pair `(s, i)` such that `x = s * 2^i` where
`s ∈ (-1;-0.5] ∪ [0.5; 1)`. Returns an undefined value if `x` is not finite.

This function does not reduce in the kernel. It is implemented in compiled code by the C function
`frexp`.

#### 20.6.3.3. Comparisons {#manual-The-Lean-Language-Reference--Basic-Types--Floating-Point-Numbers--API-Reference--Comparisons}

def

```lean
[Float.beq]](#manual-Float___beq) (a b : [Float]](#manual-Float___ofModel)) : [Bool](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)



[Float.beq]](#manual-Float___beq) (a b : [Float]](#manual-Float___ofModel)) : [Bool](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)
```

Checks whether two floating-point numbers are equal according to IEEE 754.

Floating-point equality does not correspond with propositional equality. In particular, it is not
reflexive since `NaN != NaN`, and it is not a congruence because `0.0 == -0.0`, but
`1.0 / 0.0 != 1.0 / -0.0`.

This function does not reduce in the kernel. It is compiled to the C equality operator.

def

```lean
[Float32.beq]](#manual-Float32___beq) (a b : [Float32]](#manual-Float32___ofModel)) : [Bool](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)



[Float32.beq]](#manual-Float32___beq) (a b : [Float32]](#manual-Float32___ofModel)) : [Bool](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)
```

Checks whether two floating-point numbers are equal according to IEEE 754.

Floating-point equality does not correspond with propositional equality. In particular, it is not
reflexive since `NaN != NaN`, and it is not a congruence because `0.0 == -0.0`, but
`1.0 / 0.0 != 1.0 / -0.0`.

This function does not reduce in the kernel. It is compiled to the C equality operator.

##### 20.6.3.3.1. Inequalities {#manual-The-Lean-Language-Reference--Basic-Types--Floating-Point-Numbers--API-Reference--Comparisons--Inequalities}

The decision procedures for inequalities are opaque constants in the logic.
They can only be used via the `Lean.ofReduceBool` axiom, e.g. via the `[native_decide]](#manual-native_decide)` tactic.

def

```lean
[Float.le]](#manual-Float___le) : [Float]](#manual-Float___ofModel) → [Float]](#manual-Float___ofModel) → [Bool](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)



[Float.le]](#manual-Float___le) : [Float]](#manual-Float___ofModel) → [Float]](#manual-Float___ofModel) → [Bool](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)
```

Non-strict inequality of floating-point numbers. Typically used via the `≤` operator.

def

```lean
[Float32.le]](#manual-Float32___le) : [Float32]](#manual-Float32___ofModel) → [Float32]](#manual-Float32___ofModel) → [Bool](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)



[Float32.le]](#manual-Float32___le) : [Float32]](#manual-Float32___ofModel) → [Float32]](#manual-Float32___ofModel) → [Bool](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)
```

Non-strict inequality of floating-point numbers. Typically used via the `≤` operator.

def

```lean
[Float.lt]](#manual-Float___lt) : [Float]](#manual-Float___ofModel) → [Float]](#manual-Float___ofModel) → [Bool](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)



[Float.lt]](#manual-Float___lt) : [Float]](#manual-Float___ofModel) → [Float]](#manual-Float___ofModel) → [Bool](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)
```

Strict inequality of floating-point numbers. Typically used via the `<` operator.

def

```lean
[Float32.lt]](#manual-Float32___lt) : [Float32]](#manual-Float32___ofModel) → [Float32]](#manual-Float32___ofModel) → [Bool](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)



[Float32.lt]](#manual-Float32___lt) : [Float32]](#manual-Float32___ofModel) → [Float32]](#manual-Float32___ofModel) → [Bool](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)
```

Strict inequality of floating-point numbers. Typically used via the `<` operator.

def

```lean
[Float.decLe]](#manual-Float___decLe) (a b : [Float]](#manual-Float___ofModel)) : [Decidable]](#manual-Decidable___isFalse) [(]](#manual-LE___mk)a [≤]](#manual-LE___mk) b[)]](#manual-LE___mk)



[Float.decLe]](#manual-Float___decLe) (a b : [Float]](#manual-Float___ofModel)) :
  [Decidable]](#manual-Decidable___isFalse) [(]](#manual-LE___mk)a [≤]](#manual-LE___mk) b[)]](#manual-LE___mk)
```

Compares two floating point numbers for non-strict inequality.

This function does not reduce in the kernel. It is compiled to the C inequality operator.

def

```lean
[Float32.decLe]](#manual-Float32___decLe) (a b : [Float32]](#manual-Float32___ofModel)) : [Decidable]](#manual-Decidable___isFalse) [(]](#manual-LE___mk)a [≤]](#manual-LE___mk) b[)]](#manual-LE___mk)



[Float32.decLe]](#manual-Float32___decLe) (a b : [Float32]](#manual-Float32___ofModel)) :
  [Decidable]](#manual-Decidable___isFalse) [(]](#manual-LE___mk)a [≤]](#manual-LE___mk) b[)]](#manual-LE___mk)
```

Compares two floating point numbers for non-strict inequality.

This function does not reduce in the kernel. It is compiled to the C inequality operator.

def

```lean
[Float.decLt]](#manual-Float___decLt) (a b : [Float]](#manual-Float___ofModel)) : [Decidable]](#manual-Decidable___isFalse) [(]](#manual-LT___mk)a [<]](#manual-LT___mk) b[)]](#manual-LT___mk)



[Float.decLt]](#manual-Float___decLt) (a b : [Float]](#manual-Float___ofModel)) :
  [Decidable]](#manual-Decidable___isFalse) [(]](#manual-LT___mk)a [<]](#manual-LT___mk) b[)]](#manual-LT___mk)
```

Compares two floating point numbers for strict inequality.

This function does not reduce in the kernel. It is compiled to the C inequality operator.

def

```lean
[Float32.decLt]](#manual-Float32___decLt) (a b : [Float32]](#manual-Float32___ofModel)) : [Decidable]](#manual-Decidable___isFalse) [(]](#manual-LT___mk)a [<]](#manual-LT___mk) b[)]](#manual-LT___mk)



[Float32.decLt]](#manual-Float32___decLt) (a b : [Float32]](#manual-Float32___ofModel)) :
  [Decidable]](#manual-Decidable___isFalse) [(]](#manual-LT___mk)a [<]](#manual-LT___mk) b[)]](#manual-LT___mk)
```

Compares two floating point numbers for strict inequality.

This function does not reduce in the kernel. It is compiled to the C inequality operator.

#### 20.6.3.4. Arithmetic {#manual-The-Lean-Language-Reference--Basic-Types--Floating-Point-Numbers--API-Reference--Arithmetic}

Arithmetic operations on floating-point values are typically invoked via the `[Add]](#manual-Add___mk) [Float]](#manual-Float___ofModel)`, `[Sub]](#manual-Sub___mk) [Float]](#manual-Float___ofModel)`, `[Mul]](#manual-Mul___mk) [Float]](#manual-Float___ofModel)`, `[Div]](#manual-Div___mk) [Float]](#manual-Float___ofModel)`, and `[HomogeneousPow]](#manual-HomogeneousPow___mk) [Float]](#manual-Float___ofModel)` instances, along with the corresponding `[Float32]](#manual-Float32___ofModel)` instances.

def

```lean
[Float.add]](#manual-Float___add) : [Float]](#manual-Float___ofModel) → [Float]](#manual-Float___ofModel) → [Float]](#manual-Float___ofModel)



[Float.add]](#manual-Float___add) : [Float]](#manual-Float___ofModel) → [Float]](#manual-Float___ofModel) → [Float]](#manual-Float___ofModel)
```

Adds two 64-bit floating-point numbers according to IEEE 754. Typically used via the `+` operator.

This function has a logical model in terms of `[Float.Model]](#manual-Float___Model___mk)`. It is compiled to the C addition operator.

def

```lean
[Float32.add]](#manual-Float32___add) : [Float32]](#manual-Float32___ofModel) → [Float32]](#manual-Float32___ofModel) → [Float32]](#manual-Float32___ofModel)



[Float32.add]](#manual-Float32___add) : [Float32]](#manual-Float32___ofModel) → [Float32]](#manual-Float32___ofModel) → [Float32]](#manual-Float32___ofModel)
```

Adds two 32-bit floating-point numbers according to IEEE 754. Typically used via the `+` operator.

This function has a logical model in terms of `[Float32.Model]](#manual-Float32___Model___mk)`. It is compiled to the C addition operator.

def

```lean
[Float.sub]](#manual-Float___sub) : [Float]](#manual-Float___ofModel) → [Float]](#manual-Float___ofModel) → [Float]](#manual-Float___ofModel)



[Float.sub]](#manual-Float___sub) : [Float]](#manual-Float___ofModel) → [Float]](#manual-Float___ofModel) → [Float]](#manual-Float___ofModel)
```

Subtracts 64-bit floating-point numbers according to IEEE 754. Typically used via the `-` operator.

This function has a logical model in terms of `[Float.Model]](#manual-Float___Model___mk)`. It is compiled to the C subtraction operator.

def

```lean
[Float32.sub]](#manual-Float32___sub) : [Float32]](#manual-Float32___ofModel) → [Float32]](#manual-Float32___ofModel) → [Float32]](#manual-Float32___ofModel)



[Float32.sub]](#manual-Float32___sub) : [Float32]](#manual-Float32___ofModel) → [Float32]](#manual-Float32___ofModel) → [Float32]](#manual-Float32___ofModel)
```

Subtracts 32-bit floating-point numbers according to IEEE 754. Typically used via the `-` operator.

This function has a logical model in terms of `[Float32.Model]](#manual-Float32___Model___mk)`. It is compiled to the C subtraction operator.

def

```lean
[Float.mul]](#manual-Float___mul) : [Float]](#manual-Float___ofModel) → [Float]](#manual-Float___ofModel) → [Float]](#manual-Float___ofModel)



[Float.mul]](#manual-Float___mul) : [Float]](#manual-Float___ofModel) → [Float]](#manual-Float___ofModel) → [Float]](#manual-Float___ofModel)
```

Multiplies 64-bit floating-point numbers according to IEEE 754. Typically used via the `*` operator.

This function has a logical model in terms of `[Float.Model]](#manual-Float___Model___mk)`. It is compiled to the C multiplication operator.

def

```lean
[Float32.mul]](#manual-Float32___mul) : [Float32]](#manual-Float32___ofModel) → [Float32]](#manual-Float32___ofModel) → [Float32]](#manual-Float32___ofModel)



[Float32.mul]](#manual-Float32___mul) : [Float32]](#manual-Float32___ofModel) → [Float32]](#manual-Float32___ofModel) → [Float32]](#manual-Float32___ofModel)
```

Multiplies 32-bit floating-point numbers according to IEEE 754. Typically used via the `*` operator.

This function has a logical model in terms of `[Float32.Model]](#manual-Float32___Model___mk)`. It is compiled to the C multiplication operator.

def

```lean
[Float.div]](#manual-Float___div) : [Float]](#manual-Float___ofModel) → [Float]](#manual-Float___ofModel) → [Float]](#manual-Float___ofModel)



[Float.div]](#manual-Float___div) : [Float]](#manual-Float___ofModel) → [Float]](#manual-Float___ofModel) → [Float]](#manual-Float___ofModel)
```

Divides 64-bit floating-point numbers according to IEEE 754. Typically used via the `/` operator.

In Lean, division by zero typically yields zero. For `[Float]](#manual-Float___ofModel)`, it instead yields either `Inf`,
`-Inf`, or `NaN`.

This function has a logical model in terms of `[Float.Model]](#manual-Float___Model___mk)`. It is compiled to the C division operator.

def

```lean
[Float32.div]](#manual-Float32___div) : [Float32]](#manual-Float32___ofModel) → [Float32]](#manual-Float32___ofModel) → [Float32]](#manual-Float32___ofModel)



[Float32.div]](#manual-Float32___div) : [Float32]](#manual-Float32___ofModel) → [Float32]](#manual-Float32___ofModel) → [Float32]](#manual-Float32___ofModel)
```

Divides 32-bit floating-point numbers according to IEEE 754. Typically used via the `/` operator.

In Lean, division by zero typically yields zero. For `[Float32]](#manual-Float32___ofModel)`, it instead yields either `Inf`,
`-Inf`, or `NaN`.

This function has a logical model in terms of `[Float32.Model]](#manual-Float32___Model___mk)`. It is compiled to the C division operator.

opaque

```lean
[Float.pow]](#manual-Float___pow) : [Float]](#manual-Float___ofModel) → [Float]](#manual-Float___ofModel) → [Float]](#manual-Float___ofModel)



[Float.pow]](#manual-Float___pow) : [Float]](#manual-Float___ofModel) → [Float]](#manual-Float___ofModel) → [Float]](#manual-Float___ofModel)
```

Raises one floating-point number to the power of another. Typically used via the `^` operator.

This function does not reduce in the kernel. It is implemented in compiled code by the C function
`pow`.

opaque

```lean
[Float32.pow]](#manual-Float32___pow) : [Float32]](#manual-Float32___ofModel) → [Float32]](#manual-Float32___ofModel) → [Float32]](#manual-Float32___ofModel)



[Float32.pow]](#manual-Float32___pow) : [Float32]](#manual-Float32___ofModel) → [Float32]](#manual-Float32___ofModel) → [Float32]](#manual-Float32___ofModel)
```

Raises one floating-point number to the power of another. Typically used via the `^` operator.

This function does not reduce in the kernel. It is implemented in compiled code by the C function
`powf`.

opaque

```lean
[Float.exp]](#manual-Float___exp) (x : [Float]](#manual-Float___ofModel)) : [Float]](#manual-Float___ofModel)



[Float.exp]](#manual-Float___exp) (x : [Float]](#manual-Float___ofModel)) : [Float]](#manual-Float___ofModel)
```

Computes the exponential `e^x` of a floating-point number.

This function does not reduce in the kernel. It is implemented in compiled code by the C function
`exp`.

opaque

```lean
[Float32.exp]](#manual-Float32___exp) : [Float32]](#manual-Float32___ofModel) → [Float32]](#manual-Float32___ofModel)



[Float32.exp]](#manual-Float32___exp) : [Float32]](#manual-Float32___ofModel) → [Float32]](#manual-Float32___ofModel)
```

Computes the exponential `e^x` of a floating-point number.

This function does not reduce in the kernel. It is implemented in compiled code by the C function
`expf`.

opaque

```lean
[Float.exp2]](#manual-Float___exp2) (x : [Float]](#manual-Float___ofModel)) : [Float]](#manual-Float___ofModel)



[Float.exp2]](#manual-Float___exp2) (x : [Float]](#manual-Float___ofModel)) : [Float]](#manual-Float___ofModel)
```

Computes the base-2 exponential `2^x` of a floating-point number.

This function does not reduce in the kernel. It is implemented in compiled code by the C function
`exp2`.

opaque

```lean
[Float32.exp2]](#manual-Float32___exp2) : [Float32]](#manual-Float32___ofModel) → [Float32]](#manual-Float32___ofModel)



[Float32.exp2]](#manual-Float32___exp2) : [Float32]](#manual-Float32___ofModel) → [Float32]](#manual-Float32___ofModel)
```

Computes the base-2 exponential `2^x` of a floating-point number.

This function does not reduce in the kernel. It is implemented in compiled code by the C function
`exp2f`.

##### 20.6.3.4.1. Roots {#manual-The-Lean-Language-Reference--Basic-Types--Floating-Point-Numbers--API-Reference--Arithmetic--Roots}

Computing the square root of a negative number yields `NaN`.

def

```lean
[Float.sqrt]](#manual-Float___sqrt) : [Float]](#manual-Float___ofModel) → [Float]](#manual-Float___ofModel)



[Float.sqrt]](#manual-Float___sqrt) : [Float]](#manual-Float___ofModel) → [Float]](#manual-Float___ofModel)
```

Computes the square root of a floating-point number.

This function has a logical model in terms of `[Float.Model]](#manual-Float___Model___mk)`. It is implemented in compiled code by the
C function `sqrt`.

def

```lean
[Float32.sqrt]](#manual-Float32___sqrt) : [Float32]](#manual-Float32___ofModel) → [Float32]](#manual-Float32___ofModel)



[Float32.sqrt]](#manual-Float32___sqrt) : [Float32]](#manual-Float32___ofModel) → [Float32]](#manual-Float32___ofModel)
```

Computes the square root of a floating-point number.

This function has a logical model in terms of `[Float32.Model]](#manual-Float32___Model___mk)`. It is implemented in compiled code by
the C function `sqrtf`.

opaque

```lean
[Float.cbrt]](#manual-Float___cbrt) : [Float]](#manual-Float___ofModel) → [Float]](#manual-Float___ofModel)



[Float.cbrt]](#manual-Float___cbrt) : [Float]](#manual-Float___ofModel) → [Float]](#manual-Float___ofModel)
```

Computes the cube root of a floating-point number.

This function does not reduce in the kernel. It is implemented in compiled code by the C function
`cbrt`.

opaque

```lean
[Float32.cbrt]](#manual-Float32___cbrt) : [Float32]](#manual-Float32___ofModel) → [Float32]](#manual-Float32___ofModel)



[Float32.cbrt]](#manual-Float32___cbrt) : [Float32]](#manual-Float32___ofModel) → [Float32]](#manual-Float32___ofModel)
```

Computes the cube root of a floating-point number.

This function does not reduce in the kernel. It is implemented in compiled code by the C function
`cbrtf`.

#### 20.6.3.5. Logarithms {#manual-The-Lean-Language-Reference--Basic-Types--Floating-Point-Numbers--API-Reference--Logarithms}

opaque

```lean
[Float.log]](#manual-Float___log) (x : [Float]](#manual-Float___ofModel)) : [Float]](#manual-Float___ofModel)



[Float.log]](#manual-Float___log) (x : [Float]](#manual-Float___ofModel)) : [Float]](#manual-Float___ofModel)
```

Computes the natural logarithm `ln x` of a floating-point number.

This function does not reduce in the kernel. It is implemented in compiled code by the C function
`log`.

opaque

```lean
[Float32.log]](#manual-Float32___log) : [Float32]](#manual-Float32___ofModel) → [Float32]](#manual-Float32___ofModel)



[Float32.log]](#manual-Float32___log) : [Float32]](#manual-Float32___ofModel) → [Float32]](#manual-Float32___ofModel)
```

Computes the natural logarithm `ln x` of a floating-point number.

This function does not reduce in the kernel. It is implemented in compiled code by the C function
`logf`.

opaque

```lean
[Float.log10]](#manual-Float___log10) : [Float]](#manual-Float___ofModel) → [Float]](#manual-Float___ofModel)



[Float.log10]](#manual-Float___log10) : [Float]](#manual-Float___ofModel) → [Float]](#manual-Float___ofModel)
```

Computes the base-10 logarithm of a floating-point number.

This function does not reduce in the kernel. It is implemented in compiled code by the C function
`log10`.

opaque

```lean
[Float32.log10]](#manual-Float32___log10) : [Float32]](#manual-Float32___ofModel) → [Float32]](#manual-Float32___ofModel)



[Float32.log10]](#manual-Float32___log10) : [Float32]](#manual-Float32___ofModel) → [Float32]](#manual-Float32___ofModel)
```

Computes the base-10 logarithm of a floating-point number.

This function does not reduce in the kernel. It is implemented in compiled code by the C function
`log10f`.

opaque

```lean
[Float.log2]](#manual-Float___log2) : [Float]](#manual-Float___ofModel) → [Float]](#manual-Float___ofModel)



[Float.log2]](#manual-Float___log2) : [Float]](#manual-Float___ofModel) → [Float]](#manual-Float___ofModel)
```

Computes the base-2 logarithm of a floating-point number.

This function does not reduce in the kernel. It is implemented in compiled code by the C function
`log2`.

opaque

```lean
[Float32.log2]](#manual-Float32___log2) : [Float32]](#manual-Float32___ofModel) → [Float32]](#manual-Float32___ofModel)



[Float32.log2]](#manual-Float32___log2) : [Float32]](#manual-Float32___ofModel) → [Float32]](#manual-Float32___ofModel)
```

Computes the base-2 logarithm of a floating-point number.

This function does not reduce in the kernel. It is implemented in compiled code by the C function
`log2f`.

#### 20.6.3.6. Scaling {#manual-The-Lean-Language-Reference--Basic-Types--Floating-Point-Numbers--API-Reference--Scaling}

opaque

```lean
[Float.scaleB]](#manual-Float___scaleB) (x : [Float]](#manual-Float___ofModel)) (i : [Int]](#manual-Int___ofNat)) : [Float]](#manual-Float___ofModel)



[Float.scaleB]](#manual-Float___scaleB) (x : [Float]](#manual-Float___ofModel)) (i : [Int]](#manual-Int___ofNat)) : [Float]](#manual-Float___ofModel)
```

Efficiently computes `x * 2^i`.

This function does not reduce in the kernel.

opaque

```lean
[Float32.scaleB]](#manual-Float32___scaleB) (x : [Float32]](#manual-Float32___ofModel)) (i : [Int]](#manual-Int___ofNat)) : [Float32]](#manual-Float32___ofModel)



[Float32.scaleB]](#manual-Float32___scaleB) (x : [Float32]](#manual-Float32___ofModel)) (i : [Int]](#manual-Int___ofNat)) :
  [Float32]](#manual-Float32___ofModel)
```

Efficiently computes `x * 2^i`.

This function does not reduce in the kernel.

#### 20.6.3.7. Rounding {#manual-The-Lean-Language-Reference--Basic-Types--Floating-Point-Numbers--API-Reference--Rounding}

opaque

```lean
[Float.round]](#manual-Float___round) : [Float]](#manual-Float___ofModel) → [Float]](#manual-Float___ofModel)



[Float.round]](#manual-Float___round) : [Float]](#manual-Float___ofModel) → [Float]](#manual-Float___ofModel)
```

Rounds to the nearest integer, rounding away from zero at half-way points.

This function does not reduce in the kernel. It is implemented in compiled code by the C function
`round`.

opaque

```lean
[Float32.round]](#manual-Float32___round) : [Float32]](#manual-Float32___ofModel) → [Float32]](#manual-Float32___ofModel)



[Float32.round]](#manual-Float32___round) : [Float32]](#manual-Float32___ofModel) → [Float32]](#manual-Float32___ofModel)
```

Rounds to the nearest integer, rounding away from zero at half-way points.

This function does not reduce in the kernel. It is implemented in compiled code by the C function
`roundf`.

opaque

```lean
[Float.floor]](#manual-Float___floor) : [Float]](#manual-Float___ofModel) → [Float]](#manual-Float___ofModel)



[Float.floor]](#manual-Float___floor) : [Float]](#manual-Float___ofModel) → [Float]](#manual-Float___ofModel)
```

Computes the floor of a floating-point number, which is the largest integer that's no larger
than the given number.

This function does not reduce in the kernel. It is implemented in compiled code by the C function
`floor`.

Examples:

- `[Float.floor]](#manual-Float___floor) 1.5 = 1`
- `[Float.floor]](#manual-Float___floor) (-1.5) = (-2)`

opaque

```lean
[Float32.floor]](#manual-Float32___floor) : [Float32]](#manual-Float32___ofModel) → [Float32]](#manual-Float32___ofModel)



[Float32.floor]](#manual-Float32___floor) : [Float32]](#manual-Float32___ofModel) → [Float32]](#manual-Float32___ofModel)
```

Computes the floor of a floating-point number, which is the largest integer that's no larger
than the given number.

This function does not reduce in the kernel. It is implemented in compiled code by the C function
`floorf`.

Examples:

- `[Float32.floor]](#manual-Float32___floor) 1.5 = 1`
- `[Float32.floor]](#manual-Float32___floor) (-1.5) = (-2)`

opaque

```lean
[Float.ceil]](#manual-Float___ceil) : [Float]](#manual-Float___ofModel) → [Float]](#manual-Float___ofModel)



[Float.ceil]](#manual-Float___ceil) : [Float]](#manual-Float___ofModel) → [Float]](#manual-Float___ofModel)
```

Computes the ceiling of a floating-point number, which is the smallest integer that's no smaller
than the given number.

This function does not reduce in the kernel. It is implemented in compiled code by the C function
`ceil`.

Examples:

- `[Float.ceil]](#manual-Float___ceil) 1.5 = 2`
- `[Float.ceil]](#manual-Float___ceil) (-1.5) = (-1)`

opaque

```lean
[Float32.ceil]](#manual-Float32___ceil) : [Float32]](#manual-Float32___ofModel) → [Float32]](#manual-Float32___ofModel)



[Float32.ceil]](#manual-Float32___ceil) : [Float32]](#manual-Float32___ofModel) → [Float32]](#manual-Float32___ofModel)
```

Computes the ceiling of a floating-point number, which is the smallest integer that's no smaller
than the given number.

This function does not reduce in the kernel. It is implemented in compiled code by the C function
`ceilf`.

Examples:

- `[Float32.ceil]](#manual-Float32___ceil) 1.5 = 2`
- `[Float32.ceil]](#manual-Float32___ceil) (-1.5) = (-1)`

#### 20.6.3.8. Trigonometry {#manual-The-Lean-Language-Reference--Basic-Types--Floating-Point-Numbers--API-Reference--Trigonometry}

##### 20.6.3.8.1. Sine {#manual-The-Lean-Language-Reference--Basic-Types--Floating-Point-Numbers--API-Reference--Trigonometry--Sine}

opaque

```lean
[Float.sin]](#manual-Float___sin) : [Float]](#manual-Float___ofModel) → [Float]](#manual-Float___ofModel)



[Float.sin]](#manual-Float___sin) : [Float]](#manual-Float___ofModel) → [Float]](#manual-Float___ofModel)
```

Computes the sine of a floating-point number in radians.

This function does not reduce in the kernel. It is implemented in compiled code by the C function
`sin`.

opaque

```lean
[Float32.sin]](#manual-Float32___sin) : [Float32]](#manual-Float32___ofModel) → [Float32]](#manual-Float32___ofModel)



[Float32.sin]](#manual-Float32___sin) : [Float32]](#manual-Float32___ofModel) → [Float32]](#manual-Float32___ofModel)
```

Computes the sine of a floating-point number in radians.

This function does not reduce in the kernel. It is implemented in compiled code by the C function
`sinf`.

opaque

```lean
[Float.sinh]](#manual-Float___sinh) : [Float]](#manual-Float___ofModel) → [Float]](#manual-Float___ofModel)



[Float.sinh]](#manual-Float___sinh) : [Float]](#manual-Float___ofModel) → [Float]](#manual-Float___ofModel)
```

Computes the hyperbolic sine of a floating-point number.

This function does not reduce in the kernel. It is implemented in compiled code by the C function
`sinh`.

opaque

```lean
[Float32.sinh]](#manual-Float32___sinh) : [Float32]](#manual-Float32___ofModel) → [Float32]](#manual-Float32___ofModel)



[Float32.sinh]](#manual-Float32___sinh) : [Float32]](#manual-Float32___ofModel) → [Float32]](#manual-Float32___ofModel)
```

Computes the hyperbolic sine of a floating-point number.

This function does not reduce in the kernel. It is implemented in compiled code by the C function
`sinhf`.

opaque

```lean
[Float.asin]](#manual-Float___asin) : [Float]](#manual-Float___ofModel) → [Float]](#manual-Float___ofModel)



[Float.asin]](#manual-Float___asin) : [Float]](#manual-Float___ofModel) → [Float]](#manual-Float___ofModel)
```

Computes the arc sine (inverse sine) of a floating-point number in radians.

This function does not reduce in the kernel. It is implemented in compiled code by the C function
`asin`.

opaque

```lean
[Float32.asin]](#manual-Float32___asin) : [Float32]](#manual-Float32___ofModel) → [Float32]](#manual-Float32___ofModel)



[Float32.asin]](#manual-Float32___asin) : [Float32]](#manual-Float32___ofModel) → [Float32]](#manual-Float32___ofModel)
```

Computes the arc sine (inverse sine) of a floating-point number in radians.

This function does not reduce in the kernel. It is implemented in compiled code by the C function
`asinf`.

opaque

```lean
[Float.asinh]](#manual-Float___asinh) : [Float]](#manual-Float___ofModel) → [Float]](#manual-Float___ofModel)



[Float.asinh]](#manual-Float___asinh) : [Float]](#manual-Float___ofModel) → [Float]](#manual-Float___ofModel)
```

Computes the hyperbolic arc sine (inverse sine) of a floating-point number.

This function does not reduce in the kernel. It is implemented in compiled code by the C function
`asinh`.

opaque

```lean
[Float32.asinh]](#manual-Float32___asinh) : [Float32]](#manual-Float32___ofModel) → [Float32]](#manual-Float32___ofModel)



[Float32.asinh]](#manual-Float32___asinh) : [Float32]](#manual-Float32___ofModel) → [Float32]](#manual-Float32___ofModel)
```

Computes the hyperbolic arc sine (inverse sine) of a floating-point number.

This function does not reduce in the kernel. It is implemented in compiled code by the C function
`asinhf`.

##### 20.6.3.8.2. Cosine {#manual-The-Lean-Language-Reference--Basic-Types--Floating-Point-Numbers--API-Reference--Trigonometry--Cosine}

opaque

```lean
[Float.cos]](#manual-Float___cos) : [Float]](#manual-Float___ofModel) → [Float]](#manual-Float___ofModel)



[Float.cos]](#manual-Float___cos) : [Float]](#manual-Float___ofModel) → [Float]](#manual-Float___ofModel)
```

Computes the cosine of a floating-point number in radians.

This function does not reduce in the kernel. It is implemented in compiled code by the C function
`cos`.

opaque

```lean
[Float32.cos]](#manual-Float32___cos) : [Float32]](#manual-Float32___ofModel) → [Float32]](#manual-Float32___ofModel)



[Float32.cos]](#manual-Float32___cos) : [Float32]](#manual-Float32___ofModel) → [Float32]](#manual-Float32___ofModel)
```

Computes the cosine of a floating-point number in radians.

This function does not reduce in the kernel. It is implemented in compiled code by the C function
`cosf`.

opaque

```lean
[Float.cosh]](#manual-Float___cosh) : [Float]](#manual-Float___ofModel) → [Float]](#manual-Float___ofModel)



[Float.cosh]](#manual-Float___cosh) : [Float]](#manual-Float___ofModel) → [Float]](#manual-Float___ofModel)
```

Computes the hyperbolic cosine of a floating-point number.

This function does not reduce in the kernel. It is implemented in compiled code by the C function
`cosh`.

opaque

```lean
[Float32.cosh]](#manual-Float32___cosh) : [Float32]](#manual-Float32___ofModel) → [Float32]](#manual-Float32___ofModel)



[Float32.cosh]](#manual-Float32___cosh) : [Float32]](#manual-Float32___ofModel) → [Float32]](#manual-Float32___ofModel)
```

Computes the hyperbolic cosine of a floating-point number.

This function does not reduce in the kernel. It is implemented in compiled code by the C function
`coshf`.

opaque

```lean
[Float.acos]](#manual-Float___acos) : [Float]](#manual-Float___ofModel) → [Float]](#manual-Float___ofModel)



[Float.acos]](#manual-Float___acos) : [Float]](#manual-Float___ofModel) → [Float]](#manual-Float___ofModel)
```

Computes the arc cosine (inverse cosine) of a floating-point number in radians.

This function does not reduce in the kernel. It is implemented in compiled code by the C function
`acos`.

opaque

```lean
[Float32.acos]](#manual-Float32___acos) : [Float32]](#manual-Float32___ofModel) → [Float32]](#manual-Float32___ofModel)



[Float32.acos]](#manual-Float32___acos) : [Float32]](#manual-Float32___ofModel) → [Float32]](#manual-Float32___ofModel)
```

Computes the arc cosine (inverse cosine) of a floating-point number in radians.

This function does not reduce in the kernel. It is implemented in compiled code by the C function
`acosf`.

opaque

```lean
[Float.acosh]](#manual-Float___acosh) : [Float]](#manual-Float___ofModel) → [Float]](#manual-Float___ofModel)



[Float.acosh]](#manual-Float___acosh) : [Float]](#manual-Float___ofModel) → [Float]](#manual-Float___ofModel)
```

Computes the hyperbolic arc cosine (inverse cosine) of a floating-point number.

This function does not reduce in the kernel. It is implemented in compiled code by the C function
`acosh`.

opaque

```lean
[Float32.acosh]](#manual-Float32___acosh) : [Float32]](#manual-Float32___ofModel) → [Float32]](#manual-Float32___ofModel)



[Float32.acosh]](#manual-Float32___acosh) : [Float32]](#manual-Float32___ofModel) → [Float32]](#manual-Float32___ofModel)
```

Computes the hyperbolic arc cosine (inverse cosine) of a floating-point number.

This function does not reduce in the kernel. It is implemented in compiled code by the C function
`acoshf`.

##### 20.6.3.8.3. Tangent {#manual-The-Lean-Language-Reference--Basic-Types--Floating-Point-Numbers--API-Reference--Trigonometry--Tangent}

opaque

```lean
[Float.tan]](#manual-Float___tan) : [Float]](#manual-Float___ofModel) → [Float]](#manual-Float___ofModel)



[Float.tan]](#manual-Float___tan) : [Float]](#manual-Float___ofModel) → [Float]](#manual-Float___ofModel)
```

Computes the tangent of a floating-point number in radians.

This function does not reduce in the kernel. It is implemented in compiled code by the C function
`tan`.

opaque

```lean
[Float32.tan]](#manual-Float32___tan) : [Float32]](#manual-Float32___ofModel) → [Float32]](#manual-Float32___ofModel)



[Float32.tan]](#manual-Float32___tan) : [Float32]](#manual-Float32___ofModel) → [Float32]](#manual-Float32___ofModel)
```

Computes the tangent of a floating-point number in radians.

This function does not reduce in the kernel. It is implemented in compiled code by the C function
`tanf`.

opaque

```lean
[Float.tanh]](#manual-Float___tanh) : [Float]](#manual-Float___ofModel) → [Float]](#manual-Float___ofModel)



[Float.tanh]](#manual-Float___tanh) : [Float]](#manual-Float___ofModel) → [Float]](#manual-Float___ofModel)
```

Computes the hyperbolic tangent of a floating-point number.

This function does not reduce in the kernel. It is implemented in compiled code by the C function
`tanh`.

opaque

```lean
[Float32.tanh]](#manual-Float32___tanh) : [Float32]](#manual-Float32___ofModel) → [Float32]](#manual-Float32___ofModel)



[Float32.tanh]](#manual-Float32___tanh) : [Float32]](#manual-Float32___ofModel) → [Float32]](#manual-Float32___ofModel)
```

Computes the hyperbolic tangent of a floating-point number.

This function does not reduce in the kernel. It is implemented in compiled code by the C function
`tanhf`.

opaque

```lean
[Float.atan]](#manual-Float___atan) : [Float]](#manual-Float___ofModel) → [Float]](#manual-Float___ofModel)



[Float.atan]](#manual-Float___atan) : [Float]](#manual-Float___ofModel) → [Float]](#manual-Float___ofModel)
```

Computes the arc tangent (inverse tangent) of a floating-point number in radians.

This function does not reduce in the kernel. It is implemented in compiled code by the C function
`atan`.

opaque

```lean
[Float32.atan]](#manual-Float32___atan) : [Float32]](#manual-Float32___ofModel) → [Float32]](#manual-Float32___ofModel)



[Float32.atan]](#manual-Float32___atan) : [Float32]](#manual-Float32___ofModel) → [Float32]](#manual-Float32___ofModel)
```

Computes the arc tangent (inverse tangent) of a floating-point number in radians.

This function does not reduce in the kernel. It is implemented in compiled code by the C function
`atanf`.

opaque

```lean
[Float.atanh]](#manual-Float___atanh) : [Float]](#manual-Float___ofModel) → [Float]](#manual-Float___ofModel)



[Float.atanh]](#manual-Float___atanh) : [Float]](#manual-Float___ofModel) → [Float]](#manual-Float___ofModel)
```

Computes the hyperbolic arc tangent (inverse tangent) of a floating-point number.

This function does not reduce in the kernel. It is implemented in compiled code by the C function
`atanh`.

opaque

```lean
[Float32.atanh]](#manual-Float32___atanh) : [Float32]](#manual-Float32___ofModel) → [Float32]](#manual-Float32___ofModel)



[Float32.atanh]](#manual-Float32___atanh) : [Float32]](#manual-Float32___ofModel) → [Float32]](#manual-Float32___ofModel)
```

Computes the hyperbolic arc tangent (inverse tangent) of a floating-point number.

This function does not reduce in the kernel. It is implemented in compiled code by the C function
`atanhf`.

opaque

```lean
[Float.atan2]](#manual-Float___atan2) (y x : [Float]](#manual-Float___ofModel)) : [Float]](#manual-Float___ofModel)



[Float.atan2]](#manual-Float___atan2) (y x : [Float]](#manual-Float___ofModel)) : [Float]](#manual-Float___ofModel)
```

Computes the arc tangent (inverse tangent) of `y / x` in radians, in the range `-π`–`π`. The signs
of the arguments determine the quadrant of the result.

This function does not reduce in the kernel. It is implemented in compiled code by the C function
`atan2`.

opaque

```lean
[Float32.atan2]](#manual-Float32___atan2) : [Float32]](#manual-Float32___ofModel) → [Float32]](#manual-Float32___ofModel) → [Float32]](#manual-Float32___ofModel)



[Float32.atan2]](#manual-Float32___atan2) :
  [Float32]](#manual-Float32___ofModel) → [Float32]](#manual-Float32___ofModel) → [Float32]](#manual-Float32___ofModel)
```

Computes the arc tangent (inverse tangent) of `y / x` in radians, in the range `-π`–`π`. The signs
of the arguments determine the quadrant of the result.

This function does not reduce in the kernel. It is implemented in compiled code by the C function
`atan2f`.

#### 20.6.3.9. Negation and Absolute Value {#manual-The-Lean-Language-Reference--Basic-Types--Floating-Point-Numbers--API-Reference--Negation-and-Absolute-Value}

def

```lean
[Float.abs]](#manual-Float___abs) : [Float]](#manual-Float___ofModel) → [Float]](#manual-Float___ofModel)



[Float.abs]](#manual-Float___abs) : [Float]](#manual-Float___ofModel) → [Float]](#manual-Float___ofModel)
```

Computes the absolute value of a floating-point number.

This function has a logical model in terms of `[Float.Model]](#manual-Float___Model___mk)`. It is implemented in compiled code by the
C function `fabs`.

def

```lean
[Float32.abs]](#manual-Float32___abs) : [Float32]](#manual-Float32___ofModel) → [Float32]](#manual-Float32___ofModel)



[Float32.abs]](#manual-Float32___abs) : [Float32]](#manual-Float32___ofModel) → [Float32]](#manual-Float32___ofModel)
```

Computes the absolute value of a floating-point number.

This function has a logical model in terms of `[Float32.Model]](#manual-Float32___Model___mk)`. It is implemented in compiled code by
the C function `fabsf`.

def

```lean
[Float.neg]](#manual-Float___neg) : [Float]](#manual-Float___ofModel) → [Float]](#manual-Float___ofModel)



[Float.neg]](#manual-Float___neg) : [Float]](#manual-Float___ofModel) → [Float]](#manual-Float___ofModel)
```

Negates 64-bit floating-point numbers according to IEEE 754. Typically used via the `-` prefix
operator.

This function has a logical model in terms of `[Float.Model]](#manual-Float___Model___mk)`. It is compiled to the C negation operator.

def

```lean
[Float32.neg]](#manual-Float32___neg) : [Float32]](#manual-Float32___ofModel) → [Float32]](#manual-Float32___ofModel)



[Float32.neg]](#manual-Float32___neg) : [Float32]](#manual-Float32___ofModel) → [Float32]](#manual-Float32___ofModel)
```

Negates 32-bit floating-point numbers according to IEEE 754. Typically used via the `-` prefix
operator.

This function has a logical model in terms of `[Float32.Model]](#manual-Float32___Model___mk)`. It is compiled to the C negation operator.

---




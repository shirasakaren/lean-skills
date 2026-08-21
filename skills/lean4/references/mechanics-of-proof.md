# Part VII — The Mechanics of Proof {#part-7}



## 0. Introduction {#m2001-0-introduction}

> 📄 Source: https://hrmacbeth.github.io/math2001/00_Introduction.html

## Preface

### About this book

This is a book dealing with how to write careful, rigorous mathematical proofs.
The book is paired with code in the computer formalization language
[Lean](https://leanprover.github.io/about/).

The book’s focus is on technique, not on theory-building: rather few theorems are proved and then
referred back to later. Instead, the core of the book is in examples. Over two hundred problems
appear with solutions as examples in the text, and several hundred more problems appear without
solution as exercises for the reader. Each problem and each solution is presented both in standard
mathematical prose and in Lean (the book should be read with a computer open to the corresponding
file of Lean code),
and since these two “languages” are almost equally foreign for
students who are new to proofs, most solutions are annotated with informal commentary.

### Why Lean?

People have been expressing mathematical arguments in words for thousands of years, and mathematical
language is by now very standardized, with many conventions to let mathematicians communicate with
each other efficiently and unambiguously. The primary goal of this book is to teach you to read and
write standard mathematical English prose.

Computer systems called *interactive theorem provers* (also known as *proof assistants* or systems
for *formalization*) offer an alternative way to express a mathematical argument.
[Lean](https://leanprover.github.io/), an open-source project developed at Microsoft Research and
elsewhere since 2013, is one such system, but they have existed since
[the early days of computing](https://en.wikipedia.org/wiki/Automath).

Interactive theorem-proving systems such as Lean have become steadily easier to use over the
years, but they are not yet actually *easy* to use: they can be fussy and unforgiving, just like any
other computer programming language. So why does this book propose that you use such
a system in your first encounter with proofs?

First: Like it says on the tin, writing proofs in an interactive theorem prover is interactive. At
each point in your proof, you can see a visual representation of the *proof state*: what you know
(your *hypotheses*) and what you are currently working towards (your *goal(s)*).
If you are new to writing mathematical proofs, you may be surprised at how hard you find it to
distinguish hypotheses from goals on paper after a few steps alternating between forward and
backward reasoning, or how easily you lose track of one case in a case division.
Lean’s live-updating visual representation of the proof state frees you from needing to keep this
in your working memory.

Second: Computer formalization systems, fussy unforgiving programming languages that they are, throw
syntax errors when you make a mistake. Feedback is instant and you can keep iterating until you
have something that works. Writing a solution in Lean ensures that it is completely correct:
no substitutions of an inequality under a minus sign, no divisions by zero, no terms dropped in
the algebra. This is especially useful in doing proof-based mathematics: make a small error
halfway through a calculus problem and the rest of the solution probably won’t change much, but
make a small error halfway through a proof and the rest of the solution may be useless.

Finally: You interact with Lean using *tactics* which each perform a single step of a certain mode
of reasoning. The tactics which are taught in this textbook (some custom-written for this book)
each constitute a single permissible “atom” of reasoning in the mental world of the book.
This makes objective something which in a prose-only textbook can be subjective: what constitutes a
fully-detailed proof. What’s more, the “atoms” I offer you are designed to nudge you toward
certain styles of mathematical argument structure 1 which are conventional in standard
mathematical prose but which students are often slow to adopt.

This is a book about mathematics which uses Lean as a tool. It is designed so that the learning
curve is flatter for the Lean than it is for the mathematics 2 – partly by a careful
choice of exercises, and partly by writing in a
Lean “dialect” of my own, with a limited Lean vocabulary 3 which is just flexible enough for the
mathematical needs of the book. (A summary of the major differences can be
found in the appendix
[Transitioning to mainstream Lean](https://hrmacbeth.github.io/math2001/Mainstream_Lean.html#transitioning-to-regular-lean).) My hope is that most of your intellectual effort in solving
the problems will be devoted to the mathematics, not to Lean’s implementation details or
quirks of syntax.

### Contents and prerequisites

You are ready to read this book if (1) you know high school algebra inside out and (2) you have
experience learning to carry out complicated mathematical algorithms. I have in mind, as a typical
reader, a first- or second-year university student who has just taken Calculus II. But no calculus
is actually used in this book.

The main novelty of this text is the “bilingual” presentation, juxtaposing prose mathematics with
Lean code. But the design choices enforced by this presentation have shaped the text in other ways.

[Chapter 1](https://hrmacbeth.github.io/math2001/01_Proofs_by_Calculation.html#proofs-by-calculation) contains an unusually careful treatment
of “calculational proofs”. These proofs are a natural starting point for the book because they
translate easily to Lean, but they are also worthy of attention as a topic which students at this
level often struggle with. 4

[Chapter 2](https://hrmacbeth.github.io/math2001/02_Proofs_with_Structure.html#proofs-with-structure) and [Chapter 4](https://hrmacbeth.github.io/math2001/04_Proofs_with_Structure_II.html#proofs-with-structure-ii)
constitute a slow march through the rules of
[natural deduction](https://en.wikipedia.org/wiki/Natural_deduction), solving problems about
\(\mathbb{N}\), \(\mathbb{Z}\), \(\mathbb{Q}\) and \(\mathbb{R}\) which feature
increasingly many of the logical connectives and quantifiers. The requirement to translate
everything to Lean keeps the book strictly honest through these chapters. The typical intro-to-proof
textbook does not have this guardrail, and will commonly make minor abuses here – for example,
presenting a good example of a proof by cases which implicitly also uses a not-yet-covered proof
technique such as filling a witness to an existential.

Logic is not taught explicitly until [Chapter 5](https://hrmacbeth.github.io/math2001/05_Logic.html#logic), by which stage
readers will already be comfortable with the various logical connectives/quantifiers and with
translating mathematical statements back and forth between words and symbols. This permits the
logic chapter to be relatively brief, with a focus on the concept of logical equivalence (presented
primarily using natural deduction, to link with [Chapters 2](https://hrmacbeth.github.io/math2001/02_Proofs_with_Structure.html#proofs-with-structure) and
[4](https://hrmacbeth.github.io/math2001/04_Proofs_with_Structure_II.html#proofs-with-structure-ii), rather than using truth tables). 5

The other chapters of the book are more recognizable as the usual subject matter of a first
course on proofs. Sufficient familiarity with Lean has been established, and the mathematical presentation
is no longer constrained.

[Chapter 3](https://hrmacbeth.github.io/math2001/03_Parity_and_Divisibility.html#parity-and-divisibility) covers the basic notions of elementary number theory.
This chapter uses only a limited toolbox of
reasoning techniques, to permit it to be placed as a break between
[Chapter 2](https://hrmacbeth.github.io/math2001/02_Proofs_with_Structure.html#proofs-with-structure) and [Chapter 4](https://hrmacbeth.github.io/math2001/04_Proofs_with_Structure_II.html#proofs-with-structure-ii).
Number-theoretic definitions and theorems continue to appear as examples in subsequent
chapters, and the presentation of the subject concludes in [Chapter 7](https://hrmacbeth.github.io/math2001/07_Number_Theory.html#number-theory) with
the big results of Greek mathematics: infinitude of primes, Euclid’s lemma, and the irrationality of
the square root of two.

[Chapter 6](https://hrmacbeth.github.io/math2001/06_Induction.html#induction) covers induction. The treatment is fairly comprehensive, including
induction and recursion relative to various nontrivial well-founded relations on \(\mathbb{Z}\),
\(\mathbb{N}\times \mathbb{N}\) and \(\mathbb{Z}\times \mathbb{Z}\).

Finally, [Chapters 8](https://hrmacbeth.github.io/math2001/08_Functions.html#functions), [9](https://hrmacbeth.github.io/math2001/09_Sets.html#sets) and [10](https://hrmacbeth.github.io/math2001/10_Relations.html#relations) cover
functions, sets, and relations, in that order – we take the type-theoretic point of view that
functions are the primitive notion and sets and relations are defined as functions into
\(\left[\operatorname{true}/\operatorname{false}\right]\).

### Note for instructors

This book is based on lecture notes from a course I taught in Spring 2023 at Fordham University.
There were 20 students in the course, mostly first- and second-year students, with a median
background of Calculus II. Many but not all had also taken a first course in computer
programming.

The course had 75-minute classes, twice a week for 13 weeks, and in this time covered about 80% of
the material in this book. A typical class structure might look like this:

- 25 minutes traditional blackboard lecture;
- 5 minutes screenshare lecture doing the same problems in Lean;
- 20 minutes working in Lean in pairs, instructor circulating;
- 25 minutes traditional blackboard lecture, perhaps more theoretical than the first.

The homework assignments for the course are available on request.
They are relatively short (5-7 problems per week), but students were required to submit almost all
problems both in writing and in Lean. Most students required support in office hours or by email
to complete the homework assignments.

The course also featured oral examinations at the 5- and 10-week marks. These were 20-minute
one-on-one interviews assessing Lean fluency: students solved previously-unseen Lean exercises
(different exercises for each student), explaining their reasoning aloud. The grade breakdown for
the course was 25% homework, 20% oral examinations, and 55% traditional written examinations
(a midterm and a final, both completely Lean-free).

Clearly, the combination of in-class work with instructor circulating, homework support in office
hours and by email, and oral examinations adds up to a significant amount of time spent interacting
with individual (or small groups of) students. The student:staff ratio 20:1 was sustainable. I
suspect that to go beyond this ratio, one would need very strong students or an experienced and
enthusiastic TA.

The students ran Lean in a cloud development environment, to avoid needing to install Lean on their
own computers. I used [Gitpod](https://www.gitpod.io/) for this (an alternative is
[GitHub Codespaces](https://github.com/features/codespaces)) – see the README of the book’s
[code repository](https://github.com/hrmacbeth/math2001) for brief instructions on how to start
using Gitpod. The students’ Lean homework was automatically graded using a
[Gradescope](https://www.gradescope.com/) auto-grader (an alternative is
[GitHub Classroom](https://classroom.github.com/)). The Lean community’s
[teaching advice webpage](https://leanprover-community.github.io/teaching/) provides instructions
and troubleshooting for setting up this kind of course infrastructure.

### Acknowledgements

My heartfelt thanks to

- Microsoft Research, for a [grant](https://www.microsoft.com/en-us/research/academic-program/microsoft-research-lean-award-program/)
  which supported the writing of the book;
- My department at Fordham, for letting me teach the experimental course which this book grew from;
- The intrepid students in that course, Math 2001 L01 Spring 2023, for their enthusiasm and
  resourcefulness;
- Matthew Hertz, for setting up the Sphinx infrastructure for the book and typesetting the first
  chapters;
- The [mathlib community](https://leanprover-community.github.io/), and particularly Mario
  Carneiro, Gabriel Ebner, Scott Morrison, Thomas Murrills and David Renshaw, for their work on the
  Lean 3 to Lean 4 port in fall 2022 and winter 2023 prioritizing the parts of the library I needed
  for the course;
- Mario Carneiro (also) for marathon hacking sessions which produced the most interesting custom
  tactics used in the book;
- Jeremy Avigad, Rob Lewis and Patrick Massot, for sharing technical infrastructure for Lean-based
  courses and for many conversations about the dream of teaching mathematics with Lean.

Footnotes

1
:   For example, the uses of calculation blocks for most algebraic reasoning, and a preference
    for forward over backward reasoning.

2
:   If you are looking for the reverse,
    [Mathematics in Lean](https://leanprover-community.github.io/mathematics_in_lean/) is
    the canonical introduction to mathematical Lean. But note that
    that book expects more mathematical experience than this one does: writing idiomatic Lean code, even
    to prove elementary statements, requires some mathematical maturity.

3
:   The algebraic-reasoning tactic vocabulary of `ring`, `rw`,
    `numbers` (AKA `norm_num`), `rel` (custom-written for this book but now in mathlib proper),
    `extra` (custom-written) and `cancel` (custom-written) suffices for pretty much all algebraic
    reasoning over the integers, including nonlinear inequalities. The training in this vernacular
    presented over the course of
    [Sections 1.2](https://hrmacbeth.github.io/math2001/01_Proofs_by_Calculation.html#proving-equalities-in-lean) - [2.1](https://hrmacbeth.github.io/math2001/02_Proofs_with_Structure.html#tactic-mode) pays off later,
    by avoiding the need to invoke the endless lemmas such as
    `mul_le_mul_of_nonneg_left`, `pow_pos` or `le_of_pow_le_pow` by name. Other custom
    automation lightly streamlines work with induction principles, well-foundedness justifications,
    product types, and sets.
    In total, fewer than fifty lemmas are invoked by name in the book.

4
:   It’s in fact quite possible to get through the *equality*-heavy
    reasoning of many intro-to-proof courses
    without really mastering this mode of reasoning, but it’s nearly impossible to reason about
    *inequalities* without mastering calculational proofs, and students who don’t pick up the skill in
    an intro-to-proof course will find it come back to haunt them when they reach real analysis.

5
:   The expert reader may enjoy the problems in [Section 5.2](https://hrmacbeth.github.io/math2001/05_Logic.html#lem), which introduces
    classical reasoning; they are new (to my knowledge), and more elementary than the usual textbook
    examples.

---



## 1. Proofs by Calculation {#m2001-1-proofs-by-calculation}

> 📄 Source: https://hrmacbeth.github.io/math2001/01_Proofs_by_Calculation.html

This book begins in the familiar world of numbers: \(\mathbb{N}\), the natural
numbers (which in this book include 0);
\(\mathbb{Z}\), the integers; \(\mathbb{Q}\), the rational numbers;
and \(\mathbb{R}\), the real numbers. We solve problems which feel pretty close
to high school algebra – deducing equalities/inequalities from other
equalities/inequalities – using a technique which is not usually taught in high
school algebra: building a single chain of expressions connecting the left-hand
side with the right.

### 1.1. Proving equalities

#### 1.1.1. Example

We start with proofs of equalities. Here is a typical example of the technique mentioned.

> **Problem:**
>
> Let \(a\) and \(b\) be rational numbers and suppose that
> \(a - b = 4\) and \(ab=1\). Show that \((a+b)^2=20\).

> **Solution:**
>
> \[\begin{split}(a+b)^2
> &=(a-b)^2+4ab\\
> &=4^2+4\cdot 1\\
> &=20.\end{split}\]

We call the above proof a *proof by calculation*. The goal was to show that \((a+b)^2=20\),
and we established this by writing down a chain of equalities,
which starts with the expression \((a+b)^2\) (top left) and ends with
\(20\) (bottom right). The proof, implicitly, has three steps:

1.
\(\underline{\text{Proof that }(a+b)^2=(a-b)^2+4ab}\): this is a purely algebraic
rearrangement – after expanding out and simplifying, both sides are
the same quantity, \(a^2+2ab+b^2\).

2.
\(\underline{\text{Proof that }(a-b)^2+4ab=4^2+4\cdot 1}\): this is a pure substitution
step, using the known facts that \(a-b=4\) and \(ab=1\).

3.
\(\underline{\text{Proof that }4^2+4\cdot 1=20}\): this is another purely algebraic step.

This is the most common style of presenting an equality proof in advanced
mathematics textbooks and in research mathematics. There is a trade-off: it usually takes
more work for you, the writer, of the proof, to organize a proof in this
style, but the resulting proof is short and easy to check, which is courteous
to your readers.

In Section 1.3 we will discuss the question of how to come up with a proof
in this style. For now, let’s focus on how to understand them.

#### 1.1.2. Example

> **Problem:**
>
> Let \(r\) and \(s\) be real numbers, and suppose that
> \(r + 2 s = -1\) and \(s = 3\).
> Prove that \(r = -7\).

> **Solution:**
>
> \[\begin{split}r
> &= (r + 2s) - 2s \\
> &= -1 - 2s\\
> &= -1 - 2 \cdot 3 \\
> &= - 7.\end{split}\]

This proof implicitly has four steps,
which successively transform the left-hand side, \(r\), to the right-hand side,
\(-7\):

1.
\(\underline{\text{Proof that }r=(r+2s)-2s}\): a purely algebraic rearrangement.

2.
\(\underline{\text{Proof that }(r+2s)-2s=-1 - 2s}\): this is a pure substitution step,
using the known fact that \(r + 2 s = -1\).

2.
\(\underline{\text{Proof that }-1-2s=-1 - 2 \cdot 3}\): this is a pure substitution step,
using the known fact that \(s = 3\).

4.
\(\underline{\text{Proof that }-1 - 2 \cdot 3=-7}\): this is another purely algebraic step.

You might wonder what flexibility there is in presenting a proof in this style.
It is common to put each expression of the proof on its own line, so that the
first expression isn’t all alone on the left. This is certainly acceptable,
and is useful if the expressions involved are very long, so the extra space
is needed.

> **Solution:**
>
> \[\begin{split}&r \\
> &= (r + 2s) - 2s \\
> &= -1 - 2s\\
> &= -1 - 2 \cdot 3 \\
> &= - 7.\end{split}\]

Sometimes students are tempted to omit the equals signs, or to put the
equals signs at the right. This is very unconventional; don’t do this!

[

![_images/cross_1.1_1.png](https://hrmacbeth.github.io/math2001/_images/cross_1.1_1.png)

](https://hrmacbeth.github.io/math2001/_images/cross_1.1_1.png)
[

![_images/cross_1.1_2.png](https://hrmacbeth.github.io/math2001/_images/cross_1.1_2.png)

](https://hrmacbeth.github.io/math2001/_images/cross_1.1_2.png)

Finally, notice that the calculations end with a period.
The whole calculation is considered to be a single sentence, and the period ends it.

#### 1.1.3. Example

The next example again follows a pattern of algebra, substitution, algebra.
Check each step in your head.

> **Problem:**
>
> Let \(a\), \(b\), \(m\) and \(n\) be integers,
> and suppose that \(b^2=2a^2\) and \(am+bn=1\). Show that
> \((2an+bm)^2=2\).

> **Solution:**
>
> \[\begin{split}(2an + bm) ^ 2
> &= 2(am + bn) ^ 2 + (m ^ 2 - 2n ^ 2) (b ^ 2 - 2 a ^ 2) \\
> &= 2 \cdot 1 ^ 2 + (m ^ 2 - 2n ^ 2) (2a ^ 2 - 2a ^ 2) \\
> & = 2.\end{split}\]

In this case, the algebra required in the first step,

\[(2an + bm) ^ 2= 2(am + bn) ^ 2 + (m ^ 2 - 2n ^ 2) (b ^ 2 - 2 a ^ 2),\]

is very extensive. (This fact is known as
[Brahmagupta’s identity](https://en.wikipedia.org/wiki/Brahmagupta%27s_identity), named for
the Indian mathematician who discovered it in c. 628 CE.) You might
optionally choose to help your reader by providing several intermediate steps,
each one of which can be checked by a simpler algebraic calculation.

> **Solution:**
>
> \[\begin{split}(2an + bm) ^ 2
> &=4a^2n^2+4anbm+b^2m^2\\
> &=2(a^2m^2+2anbm+b^2n^2)+(4a^2n^2+b^2m^2-2a^2m^2-2b^2n^2)\\
> &= 2(am + bn) ^ 2 + (m ^ 2 - 2n ^ 2) (b ^ 2 - 2 a ^ 2) \\
> &= 2 \cdot 1 ^ 2 + (m ^ 2 - 2n ^ 2) (2a ^ 2 - 2a ^ 2) \\
> & = 2.\end{split}\]

#### 1.1.4. Example

Here is one more example. Again, check each step in your head. Notice that
you might be tempted to start on this problem by “solving for” \(a\) and
\(f\), with \(a=bc/d\) and \(f = de/c\). But this actually would
make the solution more complicated, by introducing unnecessary case splits
depending on whether or not \(d=0\) and \(c=0\). The proof by direct
calculation avoids these case splits.

> **Problem:**
>
> Let \(a\), \(b\), \(c\), \(d\), \(e\) and \(f\) be
> integers, and suppose that
> \(ad = bc\) and \(cf=de\). Show that \(d(af - be) = 0\).

> **Solution:**
>
> \[\begin{split}d(af - be)
> &= (ad) f - dbe \\
> &= (bc) f - dbe \\
> &= b (cf) - dbe \\
> &= b (d e) - dbe \\
> &= 0.\end{split}\]

### 1.2. Proving equalities in Lean

In this book, we will learn to write every proof in two ways: in words, as humans have
done for thousands of years, and in a computer system called a *proof assistant*, an approach which
was [first experimented with in the 1960s](https://en.wikipedia.org/wiki/Automath) and is still
quite unusual. We will be using the proof assistant
[Lean 4](https://leanprover.github.io/), developed since 2014 at Microsoft Research and elsewhere
by a team led by Leonardo de Moura, and its standard mathematical library
[Mathlib](https://github.com/leanprover-community/mathlib4).

In this and all following sections of the book, there is an associated Lean file, which you
should have open to experiment with at the same time as you are reading. Head over now to the
GitHub repository, [https://github.com/hrmacbeth/math2001](https://github.com/hrmacbeth/math2001), to get instructions for downloading this
code to your own computer or opening it in the cloud on Gitpod. The Gitpod option is recommended
for beginners – just make an account and you will be ready to start work, no Lean installation
necessary.

Lean code is designed to be written in an *interactive development environment* (IDE) so that you
get live feedback as you work. In this book I assume that you are running the IDE *Visual Studio
Code* – this will open automatically when you start Gitpod.

To get started, navigate in Visual Studio Code to the file which corresponds to this section,
`Math2001/01_Proofs_by_Calculation/02_Proving_Equalities_in_Lean`. When you open this file, you may
see a second panel pop up called the “Lean Infoview”. You can ignore this for now, or even close
it. We will start using the Lean Infoview in [Chapter 2](https://hrmacbeth.github.io/math2001/02_Proofs_with_Structure.html#proofs-with-structure).

#### 1.2.1. Example

The first two important lines in the file look like this:

```lean
example {a b : ℚ} (h1 : a - b = 4) (h2 : a * b = 1) : (a + b) ^ 2 = 20 :=
```

This is a Lean representation of Example 1.1.1:

> **Problem:**
>
> Let \(a\) and \(b\) be rational numbers and suppose that
> \(a - b = 4\) and \(ab=1\). Show that \((a+b)^2=20\).

The code `{a b : ℚ}` sets up two variables `a` and `b` whose type is `ℚ`, which is the
standard mathematical notation for the rational numbers (mnemonic: “quotients”).

The code `(h1 : a - b = 4) (h2 : a * b = 1)` sets up two *hypotheses*, for the two facts given
to us in the problem statement, \(a - b = 4\) and \(ab=1\). Hypotheses in Lean have names,
here `h1` and `h2`, so that we can refer back to them later. Notice that multiplication must
be written explicitly, using the symbol `*`, whereas on paper multiplication is inferred by
writing two variables next to each other like \(ab\).

After the colon, `:`, comes the *goal*, the statement we have been asked to prove:
`(a + b) ^ 2 = 20`, i.e. \((a+b)^2=20\). The symbol `^` is used in Lean for raising to a
power.

Lean’s key feature is that it checks your proofs as you write them, giving you instant feedback
about whether they are correct. The solution to this problem, as we wrote it in the previous
section, was a single calculation:

\[\begin{split}(a+b)^2
&=(a-b)^2+4ab\\
&=4^2+4\cdot 1\\
&=20.\end{split}\]

This can be expressed in Lean as a “calculation block” using the keyword `calc`. The steps of
the calculation are typed out, similarly to how they were written on paper. At the end of each
line comes a justification for why the deduction in that line is valid. We discussed in the
previous section why each of the three deductions was valid:

1.
\(\underline{\text{Proof that }(a+b)^2=(a-b)^2+4ab}\): algebraic rearrangement

2.
\(\underline{\text{Proof that }(a-b)^2+4ab=4^2+4\cdot 1}\): substitution, using the known facts that \(a-b=4\) and \(ab=1\).

3.
\(\underline{\text{Proof that }4^2+4\cdot 1=20}\): algebraic rearrangement

In Lean, an algebraic rearrangement is indicated by the *tactic* `ring`, and a substitution by the
tactic `rw` (stands for “rewrite”). When making a substitution, you must indicate by name the
hypotheses which you are substituting.

```lean
example {a b : ℚ} (h1 : a - b = 4) (h2 : a * b = 1) : (a + b) ^ 2 = 20 :=
  calc
    (a + b) ^ 2 = (a - b) ^ 2 + 4 * (a * b) := by ring
    _ = 4 ^ 2 + 4 * 1 := by rw [h1, h2]
    _ = 20 := by ring
```

#### 1.2.2. Example

Here is a Lean representation of Example 1.1.2, and its proof,
which on paper looked like this:

\[\begin{split}r
&= (r + 2s) - 2s \\
&= -1 - 2s\\
&= -1 - 2 \cdot 3 \\
&= - 7.\end{split}\]

In each of the four places marked with Lean’s standard placeholder `sorry`, 1 fill in the appropriate Lean
justification (either `ring` or `rw` with some hypotheses).

```lean
example {r s : ℝ} (h1 : s = 3) (h2 : r + 2 * s = -1) : r = -7 :=
  calc
    r = r + 2 * s - 2 * s := by sorry
    _ = -1 - 2 * s := by sorry
    _ = -1 - 2 * 3 := by sorry
    _ = -7 := by sorry
```

While filling in the justifications here, you probably discovered what happens in Lean when you
make a mistake: a red underline appears somewhere. For example, all of the following will cause
a red underline to appear somewhere. Try them!

- misspellings, like `rin` instead of `ring`
- punctuation errors, like `rw [h2` instead of `rw [h2]`
- erroneous tactic for the justification, like putting `ring` where the justification should have been a `rw`
- erroneous information provided to the tactic for the justification, like putting `rw [h2]` when in fact what’s being substituted is `h1`
- erroneous mathematics in the calculation, like `1 - 2 * s` instead of `-1 - 2 * s`

If there are no red underlines anywhere then your proof is correct. Sometimes the red underlines
are very small, so look closely. You can double check by consulting the horizontal blue status line
at the bottom of the screen in VS Code. Next to the symbol “⊗” is a number indicating how many
mistakes are in the file, and if there are no mistakes, there will also be a check mark.

#### 1.2.3. Example

Here is a Lean representation of Example 1.1.3, and its proof,
which on paper looked like this:

\[\begin{split}(2an + bm) ^ 2
&= 2(am + bn) ^ 2 + (m ^ 2 - 2n ^ 2) (b ^ 2 - 2 a ^ 2) \\
&= 2 \cdot 1 ^ 2 + (m ^ 2 - 2n ^ 2) (2a ^ 2 - 2a ^ 2) \\
& = 2.\end{split}\]

As before, in each of the three places marked with the placeholder `sorry`, fill in the
appropriate Lean justification.

```lean
example {a b m n : ℤ} (h1 : a * m + b * n = 1) (h2 : b ^ 2 = 2 * a ^ 2) :
    (2 * a * n + b * m) ^ 2 = 2 :=
  calc
    (2 * a * n + b * m) ^ 2
      = 2 * (a * m + b * n) ^ 2 + (m ^ 2 - 2 * n ^ 2) * (b ^ 2 - 2 * a ^ 2) := by sorry
    _ = 2 * 1 ^ 2 + (m ^ 2 - 2 * n ^ 2) * (2 * a ^ 2 - 2 * a ^ 2) := by sorry
    _ = 2 := by sorry
```

#### 1.2.4. Example

Finally, here is a Lean representation of Example 1.1.4. On
paper its proof looked like this:

\[\begin{split}d(af - be)
&= (ad) f - dbe \\
&= (bc) f - dbe \\
&= b (cf) - dbe \\
&= b (d e) - dbe \\
&= 0.\end{split}\]

Type out the whole proof in Lean and fill out the justification of each step. Be warned that Lean
is very sensitive about order of operations. For example, `(x * y) * z`, `x * (y * z)`, and
`(y * x) * z` all mean different things in Lean. 2 So look closely at each step in the paper proof
and make sure, when you are rewriting, that the parentheses exactly surround the small part of the
expression in which you want to make the substitution.

```lean
example {a b c d e f : ℤ} (h1 : a * d = b * c) (h2 : c * f = d * e) :
    d * (a * f - b * e) = 0 :=
  sorry
```

#### 1.2.5. Exercises

The next section, Section 1.3, contains many examples of calculational proofs. Without yet reading the
mathematics of the section closely, type up in Lean some of the examples from that section,
following the paper proofs given. The Lean file for the next section is
`Math2001/01_Proofs_by_Calculation/03_Tips_and_Tricks`.

Footnotes

1
:   You are apologizing to Lean for not having provided a proof of this assertion yet!

2
:   If there are no parentheses, like `x * y * z`, then Lean interprets the expression as
    being parenthesized greedily towards the front: `(x * y) * z`.

### 1.3. Tips and tricks

#### 1.3.1. Example

In this section we will cover some tips and tricks for actually coming up with
a proof by calculation.

> **Problem:**
>
> Let \(a\) and \(b\) be integers and suppose that \(a = 2b + 5\)
> and \(b = 3\). Show that \(a = 11\).

Since the goal in this problem is to show that \(a=11\), we already know that
the solution will look something like this:

\[\begin{split}a&=\ldots\\
&=\ldots\\
&=11.\end{split}\]

Casting around for ideas, we see that one of the hypotheses, \(a = 2b + 5\),
has \(a\) on the left-hand side. So we put that as the first step of the
calculation, and the rest of the steps write themselves.

> **Solution:**
>
> \[\begin{split}a &= 2b + 5 \\
> &= 2 \cdot 3 + 5 \\
> &= 11.\end{split}\]

```lean
example {a b : ℤ} (h1 : a = 2 * b + 5) (h2 : b = 3) : a = 11 :=
  sorry
```

#### 1.3.2. Example

More commonly, none of the hypotheses have a left- or right-hand side which
exactly appears in the goal. Here is an example.

> **Problem:**
>
> Let \(x\) be an integer and suppose that \(x+4=2\). Show that
> \(x=-2\).

Since the goal in this problem is to show that \(x=-2\), we already know that
the solution will look something like this:

\[\begin{split}x&=\ldots\\
&=\ldots\\
&=-2.\end{split}\]

Our only hypothesis has an \(x+4\) on the left-hand side, so we create an
\(x+4\) in our goal by adding and subtracting \(4\) from \(x\),
like this: \(x=(x+4)-4\). Then the rest of the proof works out easily.

> **Solution:**
>
> \[\begin{split}x&=(x+4)-4\\
> &=2-4\\
> &=-2.\end{split}\]

```lean
example {x : ℤ} (h1 : x + 4 = 2) : x = -2 :=
  sorry
```

#### 1.3.3. Example

Sometimes we need to perform this process, of “creating” one side of a
hypothesis inside the goal, more than once.

> **Problem:**
>
> Let \(a\) and \(b\) be real numbers and suppose that \(a-5b=4\)
> and \(b+2=3\). Show that \(a=9\).

In the first step of the solution we “create” an \(a-5b\); later in the third
step we “create” a \(b+2\).

> **Solution:**
>
> \[\begin{split}a
> &= (a - 5b) + 5b\\
> &= 4 + 5b \\
> &= -6 + 5(b + 2) \\
> &= -6 + 5 \cdot 3 \\
> &= 9.\end{split}\]

```lean
example {a b : ℝ} (h1 : a - 5 * b = 4) (h2 : b + 2 = 3) : a = 9 :=
  sorry
```

#### 1.3.4. Example

We might need to use both addition/subtraction and multiplication/division to
“create” one side of the hypothesis.

> **Problem:**
>
> Let \(w\) be a rational number and suppose that \(3w+1=4\).
> Show that \(w=1\).

> **Solution:**
>
> \[\begin{split}w&=\frac{3w+1}{3}-\frac{1}{3}\\
> &=\frac{4}{3}-\frac{1}{3}\\
> &=1.\end{split}\]

```lean
example {w : ℚ} (h1 : 3 * w + 1 = 4) : w = 1 :=
  sorry
```

#### 1.3.5. Example

This technique also works for the classic, Algebra I-style equations.
Consider the following problem:

> **Problem:**
>
> Let \(x\) be an integer and suppose that \(2x + 3 = x\).
> Show that \(x=-3\).

You might have been taught to solve this kind of equation by subtracting and
rearranging, something like this:

\[\begin{split}2x+3&=x\\
2x+3-x&=x-x\\
x + 3&= 0\\
x &=-3.\end{split}\]

But the solution can also be presented as a proof by calculation, by
“creating” a \(2x+3\).

> **Solution:**
>
> \[\begin{split}x&=(2x+3)-x-3\\
> &=x-x-3\\
> &=-3.\end{split}\]

```lean
example {x : ℤ} (h1 : 2 * x + 3 = x) : x = -3 :=
  sorry
```

#### 1.3.6. Example

Likewise, you have probably seen before systems of simultaneous equations like
the following:

> **Problem:**
>
> Let \(x\) and \(y\) be integers and suppose that \(2x-y=4\)
> and \(y-x+1=2\). Prove that \(x=5\).

You might have been taught to solve systems like these by adding or subtracting
equations to eliminate the variable not of interest, then solving the remaining
one-variable equation.

\[ \begin{align}\begin{aligned}2x-y&=4
&
\hspace{1cm}&(1)\\y-x+1&=2
&
\hspace{1cm}&(2)\\(2x-y)+(y-x+1)&=4+2
&
\hspace{1cm}&\text{by adding (1) and (2)}\\x +1&=6
&
\hspace{1cm}&\\x&=5
&
\hspace{1cm}&\end{aligned}\end{align} \]

But this argument can also be presented as a proof by calculation, which has the
advantage that there is no magic “add (1) and (2)” line requiring an annotation
for the reader to follow.

> **Solution:**
>
> \[\begin{split}x &=(2x-y)+(y-x+1)-1\\
> &=4+2-1\\
> &= 5.\end{split}\]

```lean
example {x y : ℤ} (h1 : 2 * x - y = 4) (h2 : y - x + 1 = 2) : x = 5 :=
  sorry
```

#### 1.3.7. Example

Here is another example of a system of simultaneous equations solved by forming
a clever combination of the hypotheses.

> **Problem:**
>
> Let \(u\) and \(v\) be rational numbers, and suppose that
> \(u+2v=4\) and \(u-2v=6\). Show that \(u=5\).

> **Solution:**
>
> \[\begin{split}u &= \frac{(u+2v)+(u-2v)}{2}\\
> &=\frac{4+6}{2}\\
> &=5.\end{split}\]

```lean
example {u v : ℚ} (h1 : u + 2 * v = 4) (h2 : u - 2 * v = 6) : u = 5 :=
  sorry
```

#### 1.3.8. Example

And another – in this case we have to combine the tricks of the last two examples, taking varying
multiples of the hypotheses to cancel the \(y\) and then dividing through by the multiple of
\(x\) that is left.

> **Problem:**
>
> Let \(x\) and \(y\) be real numbers, and suppose that
> \(x + y = 4\) and \(5x-3y=4\). Show that \(x=2\).

> **Solution:**
>
> \[\begin{split}x &= \frac{3(x+y)+(5x-3y)}{8}\\
> &=\frac{3\cdot 4+4}{8}\\
> &=2.\end{split}\]

```lean
example {x y : ℝ} (h1 : x + y = 4) (h2 : 5 * x - 3 * y = 4) : x = 2 :=
  sorry
```

#### 1.3.9. Example

Finally, let’s do a few examples involving equations of degree greater than one.

> **Problem:**
>
> Let \(a\) and \(b\) be rational numbers and suppose that
> \(a-3=2b\). Show that \(a ^ 2 - a + 3 = 4 b ^ 2 + 10 b + 9\).

> **Solution:**
>
> \[\begin{split}a ^ 2 - a + 3 &=\left[(a-3)^2+6a-9\right]-a+3\\
> &=(a-3)^2+5a-6\\
> &=(a-3)^2+5[(a-3)+3]-6\\
> &=(a-3)^2+5(a-3)+9\\
> &=(2b)^2+5(2b)+9\\
> &=4b^2+10b+9.\end{split}\]

The proof above has a few more steps than necessary, to explain how you might come up
with this proof: first you deal with the \(a^2\) term by introducing an
\((a-3)^2\) and adding/subtracting off the extra terms; then simplify; then
deal with the \(a\) term; then simplify; then substitute and simplify again.
It could be shortened to a proof in which successive purely-algebra steps are
combined, leaving something as short as possible: one step consisting of a
big algebraic calculation, one substitution step, then one more algebra step.

> **Solution:**
>
> \[\begin{split}a ^ 2 - a + 3 &=(a-3)^2+5(a-3)+9\\
> &=(2b)^2+5(2b)+9\\
> &=4b^2+10b+9.\end{split}\]

```lean
example {a b : ℚ} (h1 : a - 3 = 2 * b) : a ^ 2 - a + 3 = 4 * b ^ 2 + 10 * b + 9 :=
  sorry
```

#### 1.3.10. Example

Here’s another example with terms of degree greater than one.

> **Problem:**
>
> Let \(z\) be a real number and suppose that \(z^2-2=0\).
> Show that \(z ^ 4 - z ^ 3 - z ^ 2 + 2 z + 1=3\).

> **Solution:**
>
> \[\begin{split}z ^ 4 - z ^ 3 - z ^ 2 + 2 z + 1
> &=(z ^ 2 - z + 1) (z ^ 2 - 2)+3\\
> &=(z^2-z+1)\cdot 0+3\\
> &=3.\end{split}\]

That seems almost too slick, right? In my scratch work, I came up with this solution
using the method of [polynomial long division](https://en.wikipedia.org/wiki/Polynomial_long_division),
which you may have been taught before:
when \(z ^ 4 - z ^ 3 - z ^ 2 + 2 z + 1\) is divided by \(z^2-2\) it gives a quotient of
\(z ^ 2 - z + 1\) and remainder of \(3\).
But in the proof, it doesn’t matter what method was used to discover the fact that

\[z ^ 4 - z ^ 3 - z ^ 2 + 2 z + 1=(z ^ 2 - z + 1) (z ^ 2 - 2)+3.\]

This is a purely algebraic identity which can be easily checked by expanding
and simplifying, so you can state the result without writing out the method.

```lean
example {z : ℝ} (h1 : z ^ 2 - 2 = 0) : z ^ 4 - z ^ 3 - z ^ 2 + 2 * z + 1 = 3 :=
  sorry
```

#### 1.3.11. Exercises

Give proofs by calculation for each of the following problems.

1. Let \(x\) and \(y\) be real numbers and suppose that \(x = 3\)
   and \(y = 4x - 3\). Show that \(y = 9\).

   ```lean
   example {x y : ℝ} (h1 : x = 3) (h2 : y = 4 * x - 3) : y = 9 :=
     sorry
   ```
2. Let \(a\) and \(b\) be integers and suppose that \(a-b=0\).
   Show that \(a=b\).

   ```lean
   example {a b : ℤ} (h : a - b = 0) : a = b :=
     sorry
   ```
3. Let \(x\) and \(y\) be integers and suppose that \(x-3y=5\)
   and \(y=3\). Show that \(x=14\).

   ```lean
   example {x y : ℤ} (h1 : x - 3 * y = 5) (h2 : y = 3) : x = 14 :=
     sorry
   ```
4. Let \(p\) and \(q\) be rational numbers and suppose that \(p-2q=1\)
   and \(q=-1\). Show that \(p=-1\).

   ```lean
   example {p q : ℚ} (h1 : p - 2 * q = 1) (h2 : q = -1) : p = -1 :=
     sorry
   ```
5. Let \(x\) and \(y\) be rational numbers and suppose that \(y+1=3\)
   and \(x+2y=3\). Show that \(x=-1\).

   ```lean
   example {x y : ℚ} (h1 : y + 1 = 3) (h2 : x + 2 * y = 3) : x = -1 :=
     sorry
   ```
6. Let \(p\) and \(q\) be integers and suppose that \(p+4q=1\) and
   \(q-1=2\). Show that \(p=-11\).

   ```lean
   example {p q : ℤ} (h1 : p + 4 * q = 1) (h2 : q - 1 = 2) : p = -11 :=
     sorry
   ```
7. Let \(a\), \(b\) and \(c\) be real numbers and suppose that
   \(a+2b+3c=7\), \(b+2c=3\) and \(c=1\). Show that \(a=2\).

   ```lean
   example {a b c : ℝ} (h1 : a + 2 * b + 3 * c = 7) (h2 : b + 2 * c = 3)
       (h3 : c = 1) : a = 2 :=
     sorry
   ```
8. Let \(u\) and \(v\) be rational numbers and suppose that
   \(4u+v=3\) and \(v=2\). Show that \(u=1/4\).

   ```lean
   example {u v : ℚ} (h1 : 4 * u + v = 3) (h2 : v = 2) : u = 1 / 4 :=
     sorry
   ```
9. Let \(c\) be a rational number and suppose that \(4 c + 1 = 3 c - 2\).
   Show that \(c = -3\).

   ```lean
   example {c : ℚ} (h1 : 4 * c + 1 = 3 * c - 2) : c = -3 :=
     sorry
   ```
10. Let \(p\) be a real number and suppose that \(5 p - 3 = 3 p + 1\).
    Show that \(p = 2\).

    ```lean
    example {p : ℝ} (h1 : 5 * p - 3 = 3 * p + 1) : p = 2 :=
      sorry
    ```
11. Let \(x\) and \(y\) be integers and suppose that \(2x+y=4\) and
    \(x+y=1\). Show that \(x=3\).

    ```lean
    example {x y : ℤ} (h1 : 2 * x + y = 4) (h2 : x + y = 1) : x = 3 :=
      sorry
    ```
12. Let \(a\) and \(b\) be real numbers and suppose that
    \(a + 2 b = 4\) and \(a - b = 1\). Show that \(a = 2\).

    ```lean
    example {a b : ℝ} (h1 : a + 2 * b = 4) (h2 : a - b = 1) : a = 2 :=
      sorry
    ```
13. Let \(u\) and \(v\) be real numbers and suppose that \(u+1=v\).
    Show that \(u^2+3u+1=v^2+v-1\).

    ```lean
    example {u v : ℝ} (h1 : u + 1 = v) : u ^ 2 + 3 * u + 1 = v ^ 2 + v - 1 :=
      sorry
    ```
14. Let \(t\) be a rational number and suppose that \(t^2-4=0\).
    Show that \(t^4 + 3t^3 - 3t^2 - 2 t - 2 = 10t+2\).

    ```lean
    example {t : ℚ} (ht : t ^ 2 - 4 = 0) :
        t ^ 4 + 3 * t ^ 3 - 3 * t ^ 2 - 2 * t - 2 = 10 * t + 2 :=
      sorry
    ```
15. \(\!\!\!\!{^\*}\) Let \(x\) and \(y\) be real numbers and suppose
    that \(x + 3 = 5\) and \(2x - yx = 0\). Show that \(y = 2\).

    ```lean
    example {x y : ℝ} (h1 : x + 3 = 5) (h2 : 2 * x - y * x = 0) : y = 2 :=
      sorry
    ```
16. \(\!\!\!\!{^\*}\) Let \(p\), \(q\) and \(r\) be rational numbers and
    suppose that \(p + q + r = 0\) and \(pq + pr + qr = 2\). Show that
    \(p ^ 2 + q ^ 2 + r ^ 2 = -4\).

    ```lean
    example {p q r : ℚ} (h1 : p + q + r = 0) (h2 : p * q + p * r + q * r = 2) :
        p ^ 2 + q ^ 2 + r ^ 2 = -4 :=
      sorry
    ```

### 1.4. Proving inequalities

#### 1.4.1. Example

Proofs by calculation are also well-suited to proving inequalities: that is, facts featuring an
\(<\), \(\le\), \(>\) or \(\ge\). Consider the following worked example:

> **Problem:**
>
> Let \(x\) and \(y\) be integers, and suppose that
> \(x + 3 \le 2\) and \(y + 2x\geq 3\). Show that \(y>3\).

> **Solution:**
>
> \[\begin{split}y&=(y+2x)-2x\\
> &\geq 3 - 2x\\
> &=9 - 2(x+3)\\
> &\geq 9 - 2 \cdot 2\\
> &> 3.\end{split}\]

The goal was to show that \(y>3\),
and we established this by writing down a chain of inequalities,
which starts with the expression \(y\) (top left) and ends with
\(3\) (bottom right). The proof, implicitly, has five steps:

1.
\(\underline{\text{Proof that }y=(y+2x)-2x}\): algebraic rearrangement.

2.
\(\underline{\text{Proof that }(y+2x)-2x\geq 3-2x}\): this uses the given fact that
\(y + 2x\geq 3\) together with the general rule that an inequality is preserved under
subtraction (\(A\geq B\) implies \(A-C\geq B-C\)).

3.
\(\underline{\text{Proof that }3-2x=9-2(x+3)}\): algebraic rearrangement

4.
\(\underline{\text{Proof that }9-2(x+3)\geq 9-2\cdot 2}\): this uses the given fact that
\(x + 3 \le 2\) together with the general rules that an inequality is preserved under
multiplication by a positive number (if \(C\geq 0\) then \(A\geq B\) implies
\(CA\geq CB\)) and that an inequality is reversed when it is subtracted (\(A\geq B\) implies
\(C-B\geq C-A\)).

5.
\(\underline{\text{Proof that }9-2\cdot 2>3}\): this is a numeric fact which can be proved by
direct calculation and comparison.

Think carefully about steps 2 and 4. They look very similar to the steps which, in
proofs-by-calculation of equalities, we have called “substitution steps” (in Lean, `rw`). For
example,

- step 2 looks like we are “substituting” the inequality \(y + 2x\geq 3\) into the expression
  \((y+2x)-2x\) to get \((y+2x)-2x\geq 3-2x\);
- step 4 looks like we are “substituting” the inequality \(x + 3 \le 2\) into the expression
  \(9-2(x+3)\) to get \(9-2(x+3)\geq 9-2\cdot 2\).

But they are not just straight substitutions; instead they are
using all the rules mentioned about the preservation or reversal of inequalities under
subtraction, multiplication, etc. There will be some situations when there is no relevant rule.
For example, from \(x \le y\) you can in general conclude neither that
\(\sin x \le \sin y\) nor that \(\sin x \ge \sin y\).

Also look closely at the *relation* indicated in each step. The first step features an \(=\),
the second a \(\geq\), the third a \(=\), the fourth a \(\geq\), and the last a
\(>\). In each case this reflects the particular reasoning used to establish that step. The
final result is a \(>\), since \(>\) takes precedence, so to speak, over \(\geq\) and
\(=\) (and furthermore \(\geq\) takes precedence over \(=\)). That is, if you know
that \(A\geq B\) and \(B>C\), then they combine transitively to a \(>\) relation:
\(A>C\).

Let’s now solve the same problem in Lean.

- Algebraic rearrangement steps are justified with `ring`, as before.
- “Substitution”-like steps are justified with a tactic `rel` indicating the
  inequality being “substituted” – but note that if you try to use this in a situation when there
  is no rule about preservation/reversal of inequalities under the relevant operations,
  then it will fail.
- “Numeric facts” are justified with the tactic `numbers`. (This tactic can also justify
  “numeric facts” about equalities, like \(4^2+4\cdot 1=20\), for which we have previously
  used the `ring` tactic.)

```lean
example {x y : ℤ} (hx : x + 3 ≤ 2) (hy : y + 2 * x ≥ 3) : y > 3 :=
  calc
    y = y + 2 * x - 2 * x := by ring
    _ ≥ 3 - 2 * x := by rel [hy]
    _ = 9 - 2 * (x + 3) := by ring
    _ ≥ 9 - 2 * 2 := by rel [hx]
    _ > 3 := by numbers
```

#### 1.4.2. Example

Here’s another worked example of a proof by calculation of an inequality.

> **Problem:**
>
> Let \(r\) and \(s\) be rational numbers, and suppose that
> \(s+3\geq r\) and \(s+r \leq 3\). Show that \(r\leq 3\).

> **Solution:**
>
> \[\begin{split}r&=\frac{(s+r)+r-s}{2}\\
> &\leq \frac{3+(s+3)-s}{2}\\
> &=3.\end{split}\]

The goal was to show that \(r\leq 3\), and the proof, implicitly, has three steps:

1.
\(\underline{\text{Proof that }r=\frac{(s+r)+r-s}{2}}\): algebraic rearrangement.

2.
\(\underline{\text{Proof that }\frac{(s+r)+r-s}{2}\leq \frac{3+(s+3)-s}{2}}\): we are
“substituting” the given facts \(s+3\geq r\) and \(s+r \leq 3\), using rules about the
preservation of inequalities under addition, and under division by positive numbers (and also,
implicitly, the fact that 2 is positive).

3.
\(\underline{\text{Proof that }\frac{3+(s+3)-s}{2}=3}\): algebraic rearrangement.

This time the first step features the relation \(=\), the second a \(\leq\), and the third
a \(=\), and the “net result” is the relation \(\leq\).

Exercise: Fill in the sorries in the following Lean solution to this problem.

```lean
example {r s : ℚ} (h1 : s + 3 ≥ r) (h2 : s + r ≤ 3) : r ≤ 3 :=
  calc
    r = (s + r + r - s) / 2 := by sorry
    _ ≤ (3 + (s + 3) - s) / 2 := by sorry
    _ = 3 := by sorry
```

#### 1.4.3. Example

One more similar problem:

> **Problem:**
>
> Let \(x\) and \(y\) be real numbers and suppose that
> \(y\leq x+5\) and \(x\leq -2\). Show that \(x+y<2\).

> **Solution:**
>
> \[\begin{split}x + y &\leq x + (x + 5)\\
> &= 2x+5 \\
> &\leq 2\cdot -2 +5\\
> &< 2.\end{split}\]

Exercise: Express the solution to this problem in Lean.

```lean
example {x y : ℝ} (h1 : y ≤ x + 5) (h2 : x ≤ -2) : x + y < 2 :=
  sorry
```

#### 1.4.4. Example

In the following problem, note the use of shorthands such as \(0<A\leq 1\)
and \(x,y\leq B\) to express several related inequalities concisely.
You can look at the Lean statement which follows to see what these shorthands
mean more explicitly.

> **Problem:**
>
> Let \(u, v, x, y, A\) and \(B\) be real numbers.
> Suppose we know that \(0<A\leq 1, B\geq 1, x,y\leq B, 0\leq u<A\)
> and \(0\leq v<A\). Show that \(uy+vx+uv<3AB\).

> **Solution:**
>
> \[\begin{split}uy+vx+uv&\leq uB+vB+uv\\
> &\leq AB+AB+Av\\
> &\leq AB+AB+1\cdot v\\
> &\leq AB+AB+Bv\\
> &<AB+AB+BA \vphantom{>}\\
> &=3AB.\end{split}\]

In this calculation, the rules for preservation of inequalities under multiplication are used
repeatedly. For example, in the first step, \(uy+vx+uv\leq uB+vB+uv\), the given facts that
\(x\leq B\) and \(y\leq B\) are used, together with the rule that inequalities \(≤\)
are preserved under multiplication with a nonnegative constant (here that constant is \(u\)).
In the second step, \(uB+vB+uv\leq AB+AB+Av\), the given facts
\(u< A\) and \(v< A\) are multiplied by nonnegative constants \(B\) and \(v\).

Importantly, it is generally acceptable to omit a formal proof of the nonnegativity of the
constants which you are multiplying by, if that proof is “obvious”. For example, in this case the
nonnegativity of \(B\) comes from the given hypothesis that \(B\geq 1\). The Lean tactic
`rel` is also set up to infer these “obvious” proofs of positivity and nonnegativity.

Exercise: Fill in the sorries in the following Lean solution to this problem. You will need to
determine which of the nine hypotheses is being used at each step.

```lean
example {u v x y A B : ℝ} (h1 : 0 < A) (h2 : A ≤ 1) (h3 : 1 ≤ B) (h4 : x ≤ B)
    (h5 : y ≤ B) (h6 : 0 ≤ u) (h7 : 0 ≤ v) (h8 : u < A) (h9 : v < A) :
    u * y + v * x + u * v < 3 * A * B :=
  calc
    u * y + v * x + u * v
      ≤ u * B + v * B + u * v := by sorry
    _ ≤ A * B + A * B + A * v := by sorry
    _ ≤ A * B + A * B + 1 * v := by sorry
    _ ≤ A * B + A * B + B * v := by sorry
    _ < A * B + A * B + B * A := by sorry
    _ = 3 * A * B := by sorry
```

#### 1.4.5. Example

Here is an example with a small subtlety.

> **Problem:**
>
> Show that if \(t\) is a real number and \(t\geq 10\) then
> \(t^2-3t+17\geq 5\).

On seeing the hypothesis \(t\geq 10\), you might be tempted to “substitute” it directly into
the expression \(t^2-3t+17\).

[

![_images/cross_1.4.png](https://hrmacbeth.github.io/math2001/_images/cross_1.4.png)

](https://hrmacbeth.github.io/math2001/_images/cross_1.4.png)

This isn’t a valid solution! From \(t\geq 10\) we can deduce \(t^2\geq 10^2\) (there is a
general rule that squaring preserves an inequality of nonnegative numbers), and that
\(3t\geq 3\cdot 10\) (by multiplying the inequality by a nonnegative constant). But the second
inequality would be reversed under negation, \(-3t\leq -3\cdot 10\), so we are not able to make
a determination about the relative sizes of \(t^2-3t+17\) and \(10^2-3\cdot 10+17\).

Here is a valid solution to this problem. Instead of dropping down immediately from \(t^2\) to
\(10^2\), we go down “halfway”, to \(10t\). This can then counteract the \(-3t\) term,
combining with it to give \(7t\), which has a positive rather than negative coefficient,
allowing for a valid further substitution of the given fact \(t\geq 10\).

> **Solution:**
>
> \[\begin{split}t^2-3t+17&=t\cdot t-3t+17\\
> &\geq 10t-3t+17\\
> &=7t+17\\
> &\geq 7\cdot 10+17\\
> &\geq 5.\end{split}\]

Exercise: Fill in the sorries in the following Lean solution to this problem. Also try writing out
the incorrect solution in Lean, and check that Lean complains.

```lean
example {t : ℚ} (ht : t ≥ 10) : t ^ 2 - 3 * t - 17 ≥ 5 :=
  calc
    t ^ 2 - 3 * t - 17
      = t * t - 3 * t - 17 := by sorry
    _ ≥ 10 * t - 3 * t - 17 := by sorry
    _ = 7 * t - 17 := by sorry
    _ ≥ 7 * 10 - 17 := by sorry
    _ ≥ 5 := by sorry
```

#### 1.4.6. Example

Here’s another problem where it would be easy to go wrong.

> **Problem:**
>
> Let \(n\geq 5\) be an integer. Show that \(n ^ 2 > 2n + 11\).

Note that “let \(n\geq 5\) be an integer” (as used in the problem above)
is a common shorthand for “let \(n\) be an integer and suppose \(n\geq 5\).”

Here’s an incorrect “solution” to this problem by “substitution”:

> \[\begin{split}n^2&\geq 5^2\\
> &> 2 \cdot 5+11\\
> &\leq 2n+11.\end{split}\]

What’s wrong? Each individual deduction is valid:

- \(n^2\geq 5^2\)
- \(5^2> 2 \cdot 5+11\)
- \(2 \cdot 5+11\leq 2n+11\)

but the sequence of signs \(\geq\), \(>\), \(\leq\) cannot be combined transitively.
(If \(A>B\) and \(B\leq C\), we can draw no conclusion about the relative sizes of
\(A\) and \(C\).)

Here is a correct solution to the problem. Again the trick is to be more delicate at the first
step, dropping down initially from \(n^2\) only to \(5n\), rather than to \(5^2\).

> **Solution:**
>
> \[\begin{split}n^2&=n\cdot n\\
> &\geq 5n\\
> &=2n+3n\\
> &\geq 2n + 3 \cdot 5\\
> &= 2n + 11 + 4\\
> &>2n+11.\end{split}\]

Exercise: Express the solution to this problem in Lean. Also try writing out
the incorrect solution in Lean, and check that Lean complains.

```lean
example {n : ℤ} (hn : n ≥ 5) : n ^ 2 > 2 * n + 11 :=
  sorry
```

In fact, while writing up the correct solution, you probably had difficulty justifying the last
step. Come back to that step after you have studied the next example.

#### 1.4.7. Example

The next example features a new trick.

> **Problem:**
>
> Let \(m\) and \(n\) be integers, and suppose that \(m ^ 2 + n \le 2\).
> Show that \(n \le 2\).

We solve this problem by seeing that squares are positive, so \(n\) must be smaller than
\(m ^ 2 + n\). (Pedantically, “smaller than or equal to”.) So since we have an upper bound, 2,
for \(m ^ 2 + n\), this upper bound must also apply to the smaller number, \(n\).

> **Solution:**
>
> \[\begin{split}n&\le m ^ 2 + n\\
> &\le 2.\end{split}\]

Lean knows that squares are positive and can deal with this kind of argument silently.

```lean
example {m n : ℤ} (h : m ^ 2 + n ≤ 2) : n ≤ 2 :=
  calc
    n ≤ m ^ 2 + n := by extra
    _ ≤ 2 := by rel [h]
```

#### 1.4.8. Example

Exploiting that squares are nonnegative is a very common method for proving inequalities. Here’s
another example, where it requires some cleverness to come up with just the right square to add:
\((x-y)^2\).

> **Problem:**
>
> Let \(x\) and \(y\) be real numbers, and suppose that \(x ^ 2 + y ^ 2 \le 1\).
> Show that \((x + y) ^ 2 < 3\).

> **Solution:**
>
> \[\begin{split}(x + y) ^ 2 &\le (x + y) ^ 2 + (x - y) ^ 2\\
> &= 2 (x ^ 2 + y ^ 2)\\
> &\le 2 \cdot 1\\
> &< 3.\end{split}\]

Exercise: Fill in the sorries in the following Lean solution to this problem.

```lean
example {x y : ℝ} (h : x ^ 2 + y ^ 2 ≤ 1) : (x + y) ^ 2 < 3 :=
  calc
    (x + y) ^ 2 ≤ (x + y) ^ 2 + (x - y) ^ 2 := by sorry
    _ = 2 * (x ^ 2 + y ^ 2) := by sorry
    _ ≤ 2 * 1 := by sorry
    _ < 3 := by sorry
```

#### 1.4.9. Example

And the same trick again ….

> **Problem:**
>
> Let \(a\) and \(b\) be nonnegative rational numbers, and suppose that
> \(a+b\leq 8\). Show that \(3ab+a \leq 7b+72\).

> **Solution:**
>
> \[\begin{split}3ab+a&\leq 2b^2+a^2+(3ab+a)\\
> &=2(a+b)b+(a+b)a+a\\
> &\leq 2\cdot 8b+8a+a\\
> &=7b+9(a+b)\\
> &\leq 7b+9\cdot 8\\
> &=7b+72.\end{split}\]

Exercise: Fill in the sorries in the following Lean solution to this problem.

```lean
example {a b : ℚ} (h1 : a ≥ 0) (h2 : b ≥ 0) (h3 : a + b ≤ 8) :
    3 * a * b + a ≤ 7 * b + 72 :=
  calc
    3 * a * b + a
      ≤ 2 * b ^ 2 + a ^ 2 + (3 * a * b + a) := by sorry
    _ = 2 * ((a + b) * b) + (a + b) * a + a := by sorry
    _ ≤ 2 * (8 * b) + 8 * a + a := by sorry
    _ = 7 * b + 9 * (a + b) := by sorry
    _ ≤ 7 * b + 9 * 8 := by sorry
    _ = 7 * b + 72 := by sorry
```

#### 1.4.10. Example

Finally, here’s a particularly wild example 3 of the technique, invoking the nonnegativity of
three separate squares: \((a ^ 2 (b ^ 2 - c ^ 2)) ^ 2\), \((b ^ 4 - c ^ 4) ^ 2\), and
\((a ^ 2 b c - b ^ 2 c ^ 2) ^ 2\).

> **Problem:**
>
> Let \(a\), \(b\) and \(c\) be real numbers. Show that
> \(a ^ 2 (a ^ 6 + 8 b ^ 3 c ^ 3) ≤ (a ^ 4 + b ^ 4 + c ^ 4) ^ 2\).

> **Solution:**
>
> \[\begin{split}&a ^ 2 (a ^ 6 + 8 b ^ 3 c ^ 3)\\
> &\le 2 (a ^ 2 (b ^ 2 - c ^ 2)) ^ 2 + (b ^ 4 - c ^ 4) ^ 2 +
> 4(a ^ 2 b c - b ^ 2 c ^ 2) ^ 2 + a ^ 2 (a ^ 6 + 8 b ^ 3 c ^ 3) \\
> &=(a ^ 4 + b ^ 4 + c ^ 4) ^ 2.\end{split}\]

```lean
example {a b c : ℝ} :
    a ^ 2 * (a ^ 6 + 8 * b ^ 3 * c ^ 3) ≤ (a ^ 4 + b ^ 4 + c ^ 4) ^ 2 :=
  calc
    a ^ 2 * (a ^ 6 + 8 * b ^ 3 * c ^ 3)
      ≤ 2 * (a ^ 2 * (b ^ 2 - c ^ 2)) ^ 2 + (b ^ 4 - c ^ 4) ^ 2
          + 4 * (a ^ 2 * b * c - b ^ 2 * c ^ 2) ^ 2
          + a ^ 2 * (a ^ 6 + 8 * b ^ 3 * c ^ 3) := by extra
    _ = (a ^ 4 + b ^ 4 + c ^ 4) ^ 2 := by ring
```

#### 1.4.11. Exercises

1. Let \(x\) and \(y\) be integers and suppose that
   \(x+3 \geq 2y\) and \(1 \le y\). Show that \(x \geq -1\).

   ```lean
   example {x y : ℤ} (h1 : x + 3 ≥ 2 * y) (h2 : 1 ≤ y) : x ≥ -1 :=
     sorry
   ```
2. Let \(a\) and \(b\) be rational numbers and suppose that
   \(3 \leq a\) and \(a+2b\geq 4\). Show that \(a+b\geq 3\).

   ```lean
   example {a b : ℚ} (h1 : 3 ≤ a) (h2 : a + 2 * b ≥ 4) : a + b ≥ 3 :=
     sorry
   ```
3. Let \(x\) be an integer, with \(x\geq 9\). Show that
   \(x ^ 3 - 8x ^ 2 + 2x \geq 3\).

   ```lean
   example {x : ℤ} (hx : x ≥ 9) : x ^ 3 - 8 * x ^ 2 + 2 * x ≥ 3 :=
     sorry
   ```
4. Let \(n\geq 10\) be an integer. Show that
   \(n ^ 4 - 2 n ^ 2 > 3 n ^ 3\).

   ```lean
   example {n : ℤ} (hn : n ≥ 10) : n ^ 4 - 2 * n ^ 2 > 3 * n ^ 3 :=
     sorry
   ```
5. Let \(n\geq 5\) be an integer. Show that
   \(n ^ 2 - 2 n + 3 > 14\).

   ```lean
   example {n : ℤ} (h1 : n ≥ 5) : n ^ 2 - 2 * n + 3 > 14 :=
     sorry
   ```
6. Let \(x\) be a rational number. Show that
   \(x ^ 2 - 2 x \ge -1\).

   ```lean
   example {x : ℚ} : x ^ 2 - 2 * x ≥ -1 :=
     sorry
   ```
7. Let \(a\) and \(b\) be real numbers. Show that \(a ^ 2 + b ^ 2 \ge 2ab\).

   ```lean
   example (a b : ℝ) : a ^ 2 + b ^ 2 ≥ 2 * a * b :=
     sorry
   ```

Footnotes

3
:   Adapted from [IMO 2001](https://www.imo-official.org/year_info.aspx?year=2001), problem 2.

### 1.5. A shortcut

A few of the problems we’ve solved so far would have been easy to solve by eye. Like
Example 1.3.2, for example:

> **Problem:**
>
> Let \(x\) be an integer and suppose that \(x+4=2\). Show that
> \(x=-2\).

We solved it with the calculation

\[\begin{split}x&=(x+4)-4\\
&=2-4\\
&=-2,\end{split}\]

but honestly this seems kind of overkill.

For the purposes of this book, let’s draw the line as follows: if a fact can be deduced from
another fact simply by adding/subtracting terms from both sides (no multiplication/division/etc),
then there is no need to write out a full proof by calculation.

For use in Lean, I’ve provided a tactic `addarith` which carries out simple deductions like this.
Here’s how to use it in the example above:

```lean
example {x : ℤ} (h1 : x + 4 = 2) : x = -2 := by addarith [h1]
```

And here are a few more deductions which just involve adding/subtracting terms, and for which
therefore we will not require an explicit proof by calculation:

- If \(a-2b=1\), then \(a=2b+1\).

  ```lean
  example {a b : ℤ} (ha : a - 2 * b = 1) : a = 2 * b + 1 := by addarith [ha]
  ```
- If \(x=2\) and \(y ^ 2 = -7\), then \(x+y^2=-5\).

  ```lean
  example {x y : ℚ} (hx : x = 2) (hy : y ^ 2 = -7) : x + y ^ 2 = -5 :=
    calc
      x + y ^ 2 = x - 7 := by addarith [hy]
      _ = -5 := by addarith [hx]
  ```

It is also fine to do this for inequalities, if all that’s involved in the inequality deduction is
adding/subtracting terms. For example,

- if \(t=4-st\), then \(t+st>0\).

  ```lean
  example {s t : ℝ} (h : t = 4 - s * t) : t + s * t > 0 := by addarith [h]
  ```
- if \(m \le 8 - n\), then \(10>m+n\).

  ```lean
  example {m n : ℝ} (h1 : m ≤ 8 - n) : 10 > m + n := by addarith [h1]
  ```

But in Example 1.3.4 there was a deduction which required a division, not just
addition and subtraction: if \(3w+1=4\), then \(w=1\).

\[\begin{split}w&=\frac{3w+1}{3}-\frac{1}{3}\\
&=\frac{4}{3}-\frac{1}{3}\\
&=1.\end{split}\]

We’ll still require that this kind of proof be written out in full.

Check that `addarith` can’t verify this deduction.

```lean
example {w : ℚ} (h1 : 3 * w + 1 = 4) : w = 1 := sorry
```

---



## 2. Proofs with Structure {#m2001-2-proofs-with-structure}

> 📄 Source: https://hrmacbeth.github.io/math2001/02_Proofs_with_Structure.html

The proofs by calculation of [Chapter 1]](#m2001-proofs-by-calculation) were all,
from a certain point of view, one-step proofs. In this chapter we gradually
introduce the ingredients for multi-step proofs. These include establishing
“intermediate” facts which get referred back to later, invoking named lemmas
previously proved by yourself or other people, and taking apart complicated
mathematical statements which have been built up from simpler ones using the
logical symbols \(\lor\), \(\land\) and \(\exists\).

This chapter also introduces the key interactivity feature of the Lean language:
the live-updating *infoview* keeping track of your current hypotheses and goals.

The work of this chapter continues (after a break) in
[Chapter 4](https://hrmacbeth.github.io/math2001/04_Proofs_with_Structure_II.html#proofs-with-structure-ii).

### 2.1. Intermediate steps

#### 2.1.1. Example

Every proof we’ve seen so far has been a single calculation. More typically, though, a proof will
have a more complex structure, with facts established at an early stage which are not used immediately,
but instead brought in later.

For example, here’s the algebra problem from [Example 1.3.3]](#m2001-id22).

> **Problem:**
>
> Let \(a\) and \(b\) be real numbers and suppose that \(a-5b=4\)
> and \(b+2=3\). Show that \(a=9\).

We previously solved it by a single long calculation,

\[\begin{split}a
&= \ldots\\
&= \ldots\\
&= 9.\end{split}\]

But another way to express the solution – maybe more natural, and closer to how you might have
learned to solve it in high school – is to first solve for \(b\), and then substitute that
in to help solve for \(a\).

> **Solution:**
>
> Since \(b+2=3\), we have \(b=1\). Therefore
>
> \[\begin{split}a
> &= (a - 5b) + 5b\\
> &= 4 + 5 \cdot 1 \\
> &= 9.\end{split}\]

This is the first time we have seen a proof with words. The sentence

> Since \(b+2=3\), we have \(b=1\).

is a use of the reasoning discussed in [Section 1.5]](#m2001-shortcut): The fact \(b=1\) is
deduced from the hypothesis \(b+2=3\) by mentally subtracting 2 from both sides, and this is
considered sufficiently obvious that we don’t explain the reasoning apart from indicating which of
the hypotheses is being invoked.

We have now added one more fact, \(b=1\), to our list of known facts in this problem, and
we carry out a calculational proof in which this fact is used (it is substituted, together with
\(a-5b=4\), at the step \((a - 5b) + 5b = 4 + 5 \cdot 1\)). The word “Therefore”
(synonyms: “Thus”, “So”) introduces this calculational proof; its meaning is that the fact just
proved will be used in the reasoning which follows.

Here is how this argument looks in Lean. We state \(b=1\) with the keyword `have hb`. This is
immediately followed by the reasoning to justify this fact: adding/subtracting something from the
hypothesis \(b+2=3\), which in this problem is named `h2`. Then we give a calculational
proof as usual, in which the fact \(b=1\) (under the name `hb`) gets used at some point.

```lean
example {a b : ℝ} (h1 : a - 5 * b = 4) (h2 : b + 2 = 3) : a = 9 := by
  have hb : b = 1 := by addarith [h2]
  calc
    a = a - 5 * b + 5 * b := by ring
    _ = 4 + 5 * 1 := by rw [h1, hb]
    _ = 9 := by ring
```

It’s not so hard to understand this proof step by step by reading the Lean code. But Lean actually
provides a powerful tool to help us understand multi-step proofs: the *Lean Infoview* window, which
we will now use for the first time. Let’s walk through what the infoview can tell us as we work
through this problem.

1. Put your cursor at the start of the line

   ```lean
   have hb : b = 1
   ```

   and look at the Lean Infoview window. We see this:

   ```
   a b : ℝ
   h1 : a - 5 * b = 4
   h2 : b + 2 = 3
   ⊢ a = 9
   ```

   This is simply a vertically-displayed version of the problem we started with:

   ```lean
   example {a b : ℝ} (h1 : a - 5 * b = 4) (h2 : b + 2 = 3) :
     a = 9 :=
   ```

   It lists all the variables and hypotheses given to us in the problem, and, next to the symbol
   `⊢`, it displays our *goal*: to show `a = 9`.
2. Next, move your cursor to the start of the lines

   ```lean
   calc
     a = (a - 5 * b) + 5 * b := by ring
   ```

   The newly-proved fact `hb`, that `b = 1`, has been added to the list of hypotheses in the
   infoview.

   ```
   a b : ℝ
   h1 : a - 5 * b = 4
   h2 : b + 2 = 3
   hb : b = 1
   ⊢ a = 9
   ```
3. Finally, move your cursor to the start of the line below the last line of code,

   ```lean
   _ = 9 := by ring
   ```

   The infoview now displays no tasks for us. Instead it displays the message

   ```
   No goals
   ```

   which serves as a visual confirmation that the `calc` block solved the goal, thus
   completing the problem.

#### 2.1.2. Example

Here’s another example of reasoning in which we establish an intermediate statement before the main
proof.

> **Problem:**
>
> Let \(m\) and \(n\) be integers, and suppose that \(m+3\le 2n-1\) and \(n\le 5\).
> Show that \(m\le 6\).

> **Solution:**
>
> We have that
>
> \[\begin{split}m+3&\le 2n-1\\
> &\le 2\cdot 5-1\\
> &= 9,\end{split}\]
>
> so \(m \le 6\).

Here, the intermediate step is the fact \(m+3\le 9\). This can be read off by looking at the
calculational proof following the text

> We have that

The top left expression in the calculation is \(m+3\), the bottom right expression is
\(9\), and the sequence of relations \(\le\), \(\le\), \(=\) at the steps in
between together establish the relation \(\le\) between \(m+3\) and \(9\).

We discussed in Example 2.1.1 the meaning of “therefore”/”thus”/”so”. Here
the text

> so \(m \le 6\).

tells us that the fact just proved (\(m+3\le 9\)) implies that \(m \le 6\), by a proof
which is too straightforward to require further details; in this case it’s another example of the
add/subtract-from-both-sides reasoning discussed in [Section 1.5]](#m2001-shortcut).

Here is the same proof in Lean. A calculation block which is being used to establish an
intermediate step is introduced and named by `have`:

```lean
example {m n : ℤ} (h1 : m + 3 ≤ 2 * n - 1) (h2 : n ≤ 5) : m ≤ 6 := by
  have h3 :=
  calc
    m + 3 ≤ 2 * n - 1 := by rel [h1]
    _ ≤ 2 * 5 - 1 := by rel [h2]
    _ = 9 := by numbers
  addarith [h3]
```

Notice that, right at the beginning of the final line

```lean
addarith [h3]
```

the goal state (as displayed in the infoview) is

```
m n : ℤ
h1 : m + 3 ≤ 2 * n - 1
h2 : n ≤ 5
h3 : m + 3 ≤ 9
⊢ m ≤ 6
```

The fact \(m + 3 ≤ 9\), which was established by the calc block and given the name `h3`, is
now available as an additional fact to use in future steps, and indeed it is used in the next step
(`addarith [h3]`).

#### 2.1.3. Example

Let’s redo another example, this time [Example 1.4.2]](#m2001-id35). The problem was:

> **Problem:**
>
> Let \(r\) and \(s\) be rational numbers, and suppose that
> \(s+3\geq r\) and \(s+r \leq 3\). Show that \(r\leq 3\).

We did it before by a single, clever, calculation, but the following solution, though longer, might
be easier to come up with.

> **Solution:**
>
> From \(s + 3 \geq r\) we have \(r \leq 3 + s\), and
> from \(s + r \leq 3\) we have \(r \leq 3 - s\).
> Therefore
>
> \[\begin{split}r&=\frac{r+r}{2}\\
> &\leq \frac{(3+s)+(3-s)}{2}\\
> &=3.\end{split}\]

There are two intermediate steps in this problem: proving that \(r \leq 3 + s\) and that
\(r \leq 3 - s\).

Exercise: Here is a Lean setup for this problem, with the stated intermediate intermediate steps
(not yet justified), and the outline of the stated calculational proof as the final step. Fill in
all the sorries. Also, try to predict what the infoview will display at each position in the proof,
and then compare your prediction with reality.

```lean
example {r s : ℚ} (h1 : s + 3 ≥ r) (h2 : s + r ≤ 3) : r ≤ 3 := by
  have h3 : r ≤ 3 + s := by sorry -- justify with one tactic
  have h4 : r ≤ 3 - s := by sorry -- justify with one tactic
  calc
    r = (r + r) / 2 := by sorry -- justify with one tactic
    _ ≤ (3 - s + (3 + s)) / 2 := by sorry -- justify with one tactic
    _ = 3 := by sorry -- justify with one tactic
```

#### 2.1.4. Example

The next problem features a new form of reasoning.

> **Problem:**
>
> Let \(t\) be a real number, and suppose that \(t^2=3t\) and \(t \geq 1\). Show that
> in fact \(t\geq 2\).

> **Solution:**
>
> We have that
>
> \[\begin{split}t\cdot t&=t^2\\
> &=3t,\end{split}\]
>
> so \(t=3\). Thus \(t\geq 2\).

The first step of this proof is a calculation establishing the intermediate statement
\(t\cdot t=3t\). Then, with the phrase

> so \(t=3\)

we establish another intermediate statement, \(t=3\), by cancelling \(t\) from the left and
right sides of \(t\cdot t=3t\). Finally, we deduce the goal, \(t=3\).

In Lean, cancellation reasoning is done with the `cancel` tactic. In the proof below, you will
see that before the line `cancel t at h3` the goal state contains the hypothesis

```
h3 : t * t = 3 * t
```

and after that line it has been modified to

```
h3 : t = 3
```

Here is the full proof in Lean.

```lean
example {t : ℝ} (h1 : t ^ 2 = 3 * t) (h2 : t ≥ 1) : t ≥ 2 := by
  have h3 :=
  calc t * t = t ^ 2 := by ring
    _ = 3 * t := by rw [h1]
  cancel t at h3
  addarith [h3]
```

There is a mathematical subtlety here. You can only cancel a common factor from both
sides of an equation if that common factor is known to be nonzero. In this problem, Lean can deduce
that the common factor, `t`, is nonzero. Why?

#### 2.1.5. Example

Here’s another problem in which we establish an intermediate fact and then simplify it.

> **Problem:**
>
> Let \(a\) and \(b\) be real numbers, and suppose that \(a ^ 2 = b ^ 2 + 1\) and
> that \(a\) is nonnegative. Show that \(a \geq 1\).

> **Solution:**
>
> We have that
>
> \[\begin{split}a ^ 2&= b^2 +1\\
> &\geq 1\\
> &=1^2,\end{split}\]
>
> so \(a\geq 1\).

The deduction from \(a ^ 2 \geq 1 ^ 2\) that \(a \geq 1\) is again something that the
`cancel` tactic can do. Notice that Lean is silently checking the condition for this to be
valid (that \(a\geq 0\)). Check that if you delete the hypothesis `h2 : a ≥ 0` then the
cancellation step fails in Lean.

```lean
example {a b : ℝ} (h1 : a ^ 2 = b ^ 2 + 1) (h2 : a ≥ 0) : a ≥ 1 := by
  have h3 :=
  calc
    a ^ 2 = b ^ 2 + 1 := by rw [h1]
    _ ≥ 1 := by extra
    _ = 1 ^ 2 := by ring
  cancel 2 at h3
```

#### 2.1.6. Example

We conclude the section with some exercises translating prose proofs into Lean proofs. The
difficult part with these problems is to pick out, from the text, what the intermediate statements
are.

First, we redo one more example, this time [Example 1.4.1]](#m2001-id33). The problem was:

> **Problem:**
>
> Let \(x\) and \(y\) be integers, and suppose that
> \(x + 3 \le 2\) and \(y + 2x\geq 3\). Show that \(y>3\).

Here is a solution which uses an intermediate step.

> **Solution:**
>
> Since \(x + 3 \le 2\), we have \(x \leq -1\). So
>
> \[\begin{split}y&\geq 3-2x\\
> &\geq 3-2\cdot -1\\
> &>3.\end{split}\]

Exercise: Identify the intermediate step, and express this solution in Lean.

```lean
example {x y : ℤ} (hx : x + 3 ≤ 2) (hy : y + 2 * x ≥ 3) : y > 3 := by
  sorry
```

#### 2.1.7. Example

This next one is mathematically a little harder.

> **Problem:**
>
> Let \(a\) and \(b\) be real numbers and suppose that \(-b \le a \le b\).
> Show that \(a ^ 2 \le b ^ 2\).

> **Solution:**
>
> By the first part of the hypothesis, \(0 \le b + a\), and by the
> second part of the hypothesis, \(0 \le b - a\).
>
> Therefore \((b + a)(b - a)\) is nonnegative, so
>
> \[\begin{split}a ^ 2 &\le a ^ 2 + (b+a)(b-a)\\
> &= b ^ 2.\end{split}\]

Exercise: Identify the two intermediate steps, and express this solution in Lean. (Note that Lean
is a little more powerful here than the human reader: you do not need to give any Lean translation
of the step

> Therefore \((b + a)(b - a)\) is nonnegative,

instead Lean will figure this out by itself at the point where this is needed.)

```lean
example (a b : ℝ) (h1 : -b ≤ a) (h2 : a ≤ b) : a ^ 2 ≤ b ^ 2 := by
  sorry
```

#### 2.1.8. Example

> **Problem:**
>
> Let \(a\) and \(b\) be real numbers and suppose that \(a \le b\).
> Show that \(a ^ 3 \le b ^ 3\).

Notice that we are not assuming that \(a\) and \(b\) be positive, so our easier tricks for
the behaviour of inequalities under multiplication/powers do not apply.

> **Solution:**
>
> Since \(a \le b\), we have \(0 \le b - a\).
>
> Therefore \(\frac{(b - a)\left[(b - a)^2+3(b+a)^2\right]}{4}\) is nonnegative, so
>
> \[\begin{split}a ^ 3 &\le a ^ 3 + \frac{(b - a)\left[(b - a)^2+3(b+a)^2\right]}{4}\\
> &= b ^ 3.\end{split}\]

Exercise: Identify the intermediate step, and express this solution in Lean.

```lean
example (a b : ℝ) (h : a ≤ b) : a ^ 3 ≤ b ^ 3 := by
  sorry
```

#### 2.1.9. Exercises

1. Let \(x\) be a rational number whose square is 4, and which is greater than 1. Show that
   \(x=2\).

   Suggested steps: Prove that \(x(x+2)=2(x+2)\), then cancel to deduce that \(x=2\).

   ```lean
   example {x : ℚ} (h1 : x ^ 2 = 4) (h2 : 1 < x) : x = 2 := by
     sorry
   ```
2. Let \(n\) be an integer satisfying \(n^2+4=4n\). Prove that \(n=2\).

   Suggested steps: Prove that \((n-2)^2=0\), cancel the square to deduce that \(n-2=0\),
   then finish off.

   ```lean
   example {n : ℤ} (hn : n ^ 2 + 4 = 4 * n) : n = 2 := by
     sorry
   ```
3. Let \(x\) and \(y\) be rational numbers, and suppose that \(xy=1\) and
   \(x \ge 1\). Show that \(y \le 1\).

   Suggested steps: Prove that \(0<xy\), cancel \(x\) to deduce that \(0<y\), then
   give a calculation to prove the goal.

   ```lean
   example (x y : ℚ) (h : x * y = 1) (h2 : x ≥ 1) : y ≤ 1 := by
     sorry
   ```

### 2.2. Invoking lemmas

#### 2.2.1. Example

Here’s a style of problem we haven’t seen before, in which the goal is a *disequality*,
\(x\ne 1\), rather than an equality (\(=\)) or inequality (\(\le\), \(<\),
\(\ge\), \(>\)).

> **Problem:**
>
> Let \(x\) be a rational number, and suppose that \(3x=2\). Show that \(x\ne 1\).

> **Solution:**
>
> It suffices to prove that \(x< 1\). Indeed,
>
> \[\begin{split}x &= (3x)/3 \\
> &=2/3 \\
> &< 1.\end{split}\]

What happened in this problem? We used a piece of “general knowledge”, that if one number is
strictly smaller than another then they can’t be the same. Or at least, this feels like general
knowledge – just common sense! – but really it is a *lemma*: a fact that has been proved already
1
by us or someone else, and which we are allowed to invoke in our proof.

When working in Lean, if we want to invoke a lemma in this way, we need to call it out by name.
Someone earlier, in the big Lean mathematical library, has proved this fact and named it
`ne_of_lt`: 2

```lean
lemma ne_of_lt {a b : ℚ} (h : a < b) : a ≠ b :=
```

We can invoke this lemma in our problem using the Lean `apply` tactic. At the start of our work
on the problem,

```lean
example {x : ℚ} (hx : 3 * x = 2) : x ≠ 1 := by
```

our goal state looks like this:

```
x : ℚ
hx : 3 * x = 2
⊢ x ≠ 1
```

When we `apply` the lemma, with our cursor at the end of this line,

```lean
example {x : ℚ} (hx : 3 * x = 2) : x ≠ 1 := by
  apply ne_of_lt
```

the goal state has changed to this:

```
x : ℚ
hx : 3 * x = 2
⊢ x < 1
```

So `apply ne_of_lt` changes (only) the goal: it transforms the goal `x ≠ 1` into the goal
`x < 1`, which we can then solve by our usual methods for inequalities.

```lean
example {x : ℚ} (hx : 3 * x = 2) : x ≠ 1 := by
  apply ne_of_lt
  calc
    x = 3 * x / 3 := by ring
    _ = 2 / 3 := by rw [hx]
    _ < 1 := by numbers
```

Comparing the text proof and the Lean proof, you will notice that the phrasing in invoking the
lemma is rather different. In text, I said,

> It suffices to prove that \(x< 1\). Indeed, ….

That is, I effectively stated what the new goal would be, and left the reader to figure out what
piece of general knowledge I used to make this change to the goal. In Lean, I don’t need to state
what the new goal is, because my reader can find this out for herself by inspecting the goal state.
But I do need to explicitly mention the name of the lemma,

```lean
apply ne_of_lt
```

because “it’s general knowledge!” is not a precise enough justification for Lean to find it.

#### 2.2.2. Example

Here’s a similar problem, in which I will prove a disequality by showing the left-hand side is
*larger*, rather than (as in the previous problem) *smaller*.

> **Problem:**
>
> Let \(y\) be a real number. Show that \(y ^ 2 + 1\ne 0\).

> **Solution:**
>
> It suffices to prove that \(0 < y ^ 2 + 1\), which is clear since squares are nonnegative.

Exercise: Express the solution to this problem in Lean.

```lean
example {y : ℝ} : y ^ 2 + 1 ≠ 0 := by
  sorry
```

Use the lemma `ne_of_gt`:

```lean
lemma ne_of_gt {a b : ℝ} (h : a > b) : a ≠ b :=
```

#### 2.2.3. Example

> **Problem:**
>
> Let \(a\) and \(b\) be real numbers, and suppose that \(a^2+b^2=0\). Show that
> \(a ^ 2 = 0\).

> **Solution:**
>
> It suffices to prove that both \(a ^ 2 \le 0\) and \(a ^ 2 ≥ 0\). The second is clear
> since squares are nonnegative. For the first,
>
> \[\begin{split}a ^ 2 &\le a ^ 2 + b ^ 2\\
> &=0.\end{split}\]

In Lean use this lemma, the “antisymmetry of the \(\le\) relation”:

```lean
lemma le_antisymm {a b : α} (h1 : a ≤ b) (h2 : b ≤ a) : a = b :=
```

Here is the full proof in Lean.

```lean
example {a b : ℝ} (h1 : a ^ 2 + b ^ 2 = 0) : a ^ 2 = 0 := by
  apply le_antisymm
  calc
    a ^ 2 ≤ a ^ 2 + b ^ 2 := by extra
    _ = 0 := h1
  extra
```

Notice that after applying the lemma, the infoview shows *two* goals!

```
2 goals

a b : ℝ
h1 : a ^ 2 + b ^ 2 = 0
⊢ a ^ 2 ≤ 0

a b : ℝ
h1 : a ^ 2 + b ^ 2 = 0
⊢ 0 ≤ a ^ 2
```

This mathematically is not surprising, since the lemma had two preconditions, both of which we
need to justify.
Anyway, having multiple goals is perfectly possible in Lean – there can be many goals at any one
time, and the mechanism is simply that any code you write acts on the first goal on the list (until
it’s resolved, then work starts on the second goal, etc).

#### 2.2.4. Exercises

1. Let \(m\) be an integer for which \(m + 1=5\). Show that \(3m\ne 6\).

   You may wish to use the fact that two numbers are not equal if the first is greater than the
   second (you’ll need the lemma `ne_of_gt`, as in Example 2.2.2).

   ```lean
   example {m : ℤ} (hm : m + 1 = 5) : 3 * m ≠ 6 := by
     sorry
   ```
2. Let \(s\) be a rational number for which \(3s ≤ -6\) and
   \(2s \ge -4\). Show that \(s=-2\).

   You will probably use the lemma `le_antisymm`, stating if \(x\le y\) and
   \(x\ge y\) then \(x = y\).

   ```lean
   example {s : ℚ} (h1 : 3 * s ≤ -6) (h2 : 2 * s ≥ -4) : s = -2 := by
     sorry
   ```

Footnotes

1
:   In this case, as a consequence of the definition of the relation \(<\) on the rational
    numbers.

2
:   Here `ne` stands for “not equal”, `lt` stands for “less than”, and the `of` means that
    we are deducing a `ne` statement from a `lt` statement. The standard Lean mathematical
    library has many such naming conventions, but you don’t need to follow them; you can call your
    own lemmas `foo` and `banana` if you like.

### 2.3. “Or” and proof by cases

#### 2.3.1. Example

In mathematics, the word “or” (denoted \(\lor\) as a logical symbol) can connect two
statements. For example, here is a problem in which the hypothesis is an “or”-statement.

> **Problem:**
>
> Let \(x\) and \(y\) be real numbers and suppose that either \(x=1\) or
> \(y=-1\). Show that \(xy+x=y + 1\).

The hypothesis

> \(x=1\) or \(y=-1\)

tells us that at least one of the alternatives \(x=1\), \(y=-1\) must occur (and possibly
both, but this needs no special consideration). So a solution to this problem simply involves
considering these two alternatives in turn. This is called a *proof by cases*.

> **Solution:**
>
> If \(x=1\), then
>
> \[\begin{split}xy+x&=1\cdot y+1\\
> &= y+1,\end{split}\]
>
> and if \(y=-1\) then
>
> \[\begin{split}xy+x&=x\cdot -1+x\\
> &=-1+1\\
> &=y+1.\end{split}\]

In Lean, an “or”-statement is represented using the logical symbol `∨`. At the start of
this problem, the infoview displays one task, with `h` as the “or”-statement hypothesis, and the
goal as proving that \(xy+x=y + 1\).

```
x y : ℝ
h : x = 1 ∨ y = -1
⊢ x * y + x = y + 1
```

To consider in turn the two cases represented by an “or”-statement, we use the tactic `obtain`.
After applying this tactic, the infoview now displays two simpler tasks. The goal in each task is
still to prove that \(xy+x=y + 1\), but the hypothesis has changed: to the left alternative,
`x = 1`, in the first task, and to the right alternative, `y = -1`, in the second task.

```
x y : ℝ
hx : x = 1
⊢ x * y + x = y + 1

x y : ℝ
hy : y = -1
⊢ x * y + x = y + 1
```

We can then solve these simpler tasks one by one, giving a calculational proof of each.

```lean
example {x y : ℝ} (h : x = 1 ∨ y = -1) : x * y + x = y + 1 := by
  obtain hx | hy := h
  calc
    x * y + x = 1 * y + 1 := by rw [hx]
    _ = y + 1 := by ring
  calc
    x * y + x = x * -1 + x := by rw [hy]
    _ = -1 + 1 := by ring
    _ = y + 1 := by rw [hy]
```

#### 2.3.2. Example

More commonly, you will give a proof by cases for a problem in which there is no hypothesis
directly structured as an “or”-statement. Instead, you will create such a hypothesis yourself, by
setting up and proving an intermediate statement which is an “or”-statement.

> **Problem:**
>
> Let \(n\) be any natural number. Show that \(n ^ 2 \ne 2\).

In this problem, we will case-split on the “or”-statement

> \(n \le 1\) or \(2 \le n\).

On paper this can be stated without a proof, although really it comes as the application of a lemma
about the natural numbers: in general, either \(n\) is less than or equal to one natural
number or it’s greater than or equal to the next one. The solution to the problem is easy after
this case-split.

> **Solution:**
>
> We consider separately the cases \(n \le 1\) and \(2 \le n\).
>
> **Case 1** (\(n \le 1\)): It suffices to prove that \(n ^ 2 < 2\). Indeed,
>
> \[\begin{split}n ^ 2 & \le 1 ^ 2\\
> &<2.\end{split}\]
>
> **Case 2** (\(2 \le n\)): It suffices to prove that \(n ^ 2 > 2\). Indeed,
>
> \[\begin{split}2 &< 2 ^ 2\\
> & \le n ^ 2.\end{split}\]

In Lean, we set up this “or”-statement explicitly as an intermediate fact, the application of a
lemma,

```lean
lemma le_or_succ_le (a b : ℕ) : a ≤ b ∨ b + 1 ≤ a :=
```

We have not previously invoked lemmas in this way. The syntax uses `have`,

```lean
have hn := le_or_succ_le n 1
```

and after this line of code, the infoview displays the or-statement we want:

```
hn : n ≤ 1 ∨ 2 ≤ n
```

After establishing the intermediate fact, we case-split on that fact, and are left with two tasks
corresponding to the two cases:

```
n : ℕ
hn : n ≤ 1
⊢ n ^ 2 ≠ 2

n : ℕ
hn : 2 ≤ n
⊢ n ^ 2 ≠ 2
```

Exercise: The first case has been written up in Lean. Fill in the details of the second case.

```lean
example {n : ℕ} : n ^ 2 ≠ 2 := by
  have hn := le_or_succ_le n 1
  obtain hn | hn := hn
  apply ne_of_lt
  calc
    n ^ 2 ≤ 1 ^ 2 := by rel [hn]
    _ < 2 := by numbers
  sorry
```

#### 2.3.3. Example

So far we have discussed how to deal with an “or”-statement appearing in a hypothesis. Let us turn
to how to deal with an “or”-statement appearing in a goal. This is quite easy: you have to prove
one or the other of the alternatives in the “or”-statement, so just announce which one you expect
to work, and then prove it.

> **Problem:**
>
> Let \(x\) be a real number for which \(2x+1=5\). Show that either \(x=1\) or
> \(x=2\).

> **Solution:**
>
> We will show \(x=2\). Indeed,
>
> \[\begin{split}x &=\frac{(2x+1)-1}{2}\\
> &=\frac{5-1}{2}\\
> &=2.\end{split}\]

In Lean, use the tactic `right` to announce you will be proving the right alternative of the goal
(or `left` for the left one). This changes the goal displayed in the infoview: from

```
⊢ x = 1 ∨ x = 2
```

before the tactic application to

```
⊢ x = 2
```

afterwards.

```lean
example {x : ℝ} (hx : 2 * x + 1 = 5) : x = 1 ∨ x = 2 := by
  right
  calc
    x = (2 * x + 1 - 1) / 2 := by ring
    _ = (5 - 1) / 2 := by rw [hx]
    _ = 2 := by numbers
```

#### 2.3.4. Example

Let’s do an example which features both these styles of logical reasoning.
We solve a quadratic equation; this is a classic situation where the result has an “or” in it.
We will get to use the following lemma: if the
product of two numbers is zero, then one of them is zero.

> **Problem:**
>
> Let \(x\) be a real number for which \(x^2-3x+2=0\). Show that either \(x=1\) or
> \(x=2\).

> **Solution:**
>
> We have that
>
> \[\begin{split}(x-1)(x-2) &= x ^ 2 - 3x+2\\
> &=0.\end{split}\]
>
> So either \(x-1=0\) or \(x-2=0\).
>
> If \(x-1=0\), then \(x=1\).
>
> If \(x-2=0\), then \(x=2\).

In this solution, the case division argument is written a little more casually than in previous
examples. The phrases “If \(x-1=0\)” and “If \(x-2=0\)” silently introduce the two cases,
and it is left to the reader to observe that \(x=1\) is the left alternative of
“\(x=1\) or \(x=2\)”, which finishes the problem (similarly for the second case).

I have done the first part of the Lean argument: writing up the calculation which establishes that
\((x-1)(x-2)=0\), then calling on the lemma `eq_zero_or_eq_zero_of_mul_eq_zero` to turn this
into an “or” hypothesis. The goal state now looks like this, with both an “or” hypothesis and an
“or” goal.

```lean
x : ℝ
hx : x ^ 2 - 3 * x + 2 = 0
h1 : (x - 1) * (x - 2) = 0
h2 : x - 1 = 0 ∨ x - 2 = 0
⊢ x = 1 ∨ x = 2
```

Exercise: Fill in the rest of the Lean version of the argument.

```lean
example {x : ℝ} (hx : x ^ 2 - 3 * x + 2 = 0) : x = 1 ∨ x = 2 := by
  have h1 :=
    calc
    (x - 1) * (x - 2) = x ^ 2 - 3 * x + 2 := by ring
    _ = 0 := by rw [hx]
  have h2 := eq_zero_or_eq_zero_of_mul_eq_zero h1
  sorry
```

#### 2.3.5. Example

In Example 2.3.2 we showed that no natural number squares to 2. It is also
true that no integer squares to 2, but since order laws are more complicated when negative numbers
are involved, the proof is more complicated, requiring cases within cases.

> **Problem:**
>
> Let \(n\) be any integer. Show that \(n ^ 2 \ne 2\).

> **Solution:**
>
> We consider separately the cases \(n \le 0\) and \(1 \le n\).
>
> **Case 1** (\(n \le 0\)): In this case we have \(0 \le -n\).
> We consider separately the cases \(-n \le 1\) and \(2 \le -n\).
>
> **Case 1(i)** (\(-n \le 1\)): It suffices to prove that \(n ^ 2 < 2\). Indeed,
>
> \[\begin{split}n ^ 2 &= (-n) ^ 2\\
> & \le 1 ^ 2\\
> &<2.\end{split}\]
>
> **Case 1(ii)** (\(2 \le -n\)): It suffices to prove that \(n ^ 2 > 2\). Indeed,
>
> \[\begin{split}2 &< 2 ^ 2\\
> &\le (-n) ^ 2\\
> & = n ^ 2.\end{split}\]
>
> **Case 2** (\(1 \le n\)): We consider separately the cases \(n \le 1\) and \(2 \le n\).
>
> **Case 2(i)** (\(n \le 1\)): It suffices to prove that \(n ^ 2 < 2\). Indeed,
>
> \[\begin{split}n ^ 2 & \le 1 ^ 2\\
> &<2.\end{split}\]
>
> **Case 2(ii)** (\(2 \le n\)): It suffices to prove that \(n ^ 2 > 2\). Indeed,
>
> \[\begin{split}2 &< 2 ^ 2\\
> & \le n ^ 2.\end{split}\]

When a proof becomes this complicated, you may find it helpful to mark the start of each new
sub-proof with the symbol `·`, as follows.

```lean
example {n : ℤ} : n ^ 2 ≠ 2 := by
  have hn0 := le_or_succ_le n 0
  obtain hn0 | hn0 := hn0
  · have : 0 ≤ -n := by addarith [hn0]
    have hn := le_or_succ_le (-n) 1
    obtain hn | hn := hn
    · apply ne_of_lt
      calc
        n ^ 2 = (-n) ^ 2 := by ring
        _ ≤ 1 ^ 2 := by rel [hn]
        _ < 2 := by numbers
    · apply ne_of_gt
      calc
        (2:ℤ) < 2 ^ 2 := by numbers
        _ ≤ (-n) ^ 2 := by rel [hn]
        _ = n ^ 2 := by ring
  · have hn := le_or_succ_le n 1
    obtain hn | hn := hn
    · apply ne_of_lt
      calc
        n ^ 2 ≤ 1 ^ 2 := by rel [hn]
        _ < 2 := by numbers
    · apply ne_of_gt
      calc
        (2:ℤ) < 2 ^ 2 := by numbers
        _ ≤ n ^ 2 := by rel [hn]
```

We record this theorem for future use under the name `sq_ne_two`.

#### 2.3.6. Exercises

1. Let \(x\) be a rational number and suppose that \(x=4\) or \(x=-4\). Show that
   \(x^2+1=17\).

   ```lean
   example {x : ℚ} (h : x = 4 ∨ x = -4) : x ^ 2 + 1 = 17 := by
     sorry
   ```
2. Let \(x\) be a real number and suppose that either \(x=1\) or
   \(x=2\). Show that \(x^2-3x+2=0\).

   ```lean
   example {x : ℝ} (h : x = 1 ∨ x = 2) : x ^ 2 - 3 * x + 2 = 0 := by
     sorry
   ```
3. Let \(t\) be a rational number and suppose that \(t=-2\) or \(t=3\). Show that
   \(t^2-t-6=0\).

   ```lean
   example {t : ℚ} (h : t = -2 ∨ t = 3) : t ^ 2 - t - 6 = 0 := by
     sorry
   ```
4. Let \(x\) and \(y\) be real numbers and suppose that \(x=2\) or \(y=-2\). Show
   that \(x^2+2x=2y+4\).

   ```lean
   example {x y : ℝ} (h : x = 2 ∨ y = -2) : x * y + 2 * x = 2 * y + 4 := by
     sorry
   ```
5. Let \(s\) and \(t\) be rational numbers for which \(s = 3 - t\).
   Show that either \(s + t = 3\) or \(s + t = 5\).

   ```lean
   example {s t : ℚ} (h : s = 3 - t) : s + t = 3 ∨ s + t = 5 := by
     sorry
   ```
6. Let \(a\) and \(b\) be rational numbers for which \(a + 2b < 0\).
   Show that either \(b < a / 2\) or \(b < -a/2\).

   ```lean
   example {a b : ℚ} (h : a + 2 * b < 0) : b < a / 2 ∨ b < - a / 2 := by
     sorry
   ```
7. Let \(x\) and \(y\) be real numbers for which \(y = 2x+1\).
   Show that either \(x<y/2\) or \(x>y/2\).

   ```lean
   example {x y : ℝ} (h : y = 2 * x + 1) : x < y / 2 ∨ x > y / 2 := by
     sorry
   ```
8. Let \(x\) be a real number for which \(x^2+2x-3=0\). Show that either \(x=-3\) or
   \(x=1\).

   You will probably use the same lemma as in Example 2.3.4.

   ```lean
   example {x : ℝ} (hx : x ^ 2 + 2 * x - 3 = 0) : x = -3 ∨ x = 1 := by
     sorry
   ```
9. Let \(a\) and \(b\) be real numbers for which \(a^2+2b^2=3ab\). Show that either
   \(a=b\) or \(a=2b\).

   You will probably use the same lemma as in Example 2.3.4.

   ```lean
   example {a b : ℝ} (hab : a ^ 2 + 2 * b ^ 2 = 3 * a * b) : a = b ∨ a = 2 * b := by
     sorry
   ```
10. Let \(t\) be a real number for which \(t^3=t^2\). Show that either
    \(t=1\) or \(t=0\).

    You will probably use the same lemma as in Example 2.3.4, as well as
    the `cancel` tactic.

    ```lean
    example {t : ℝ} (ht : t ^ 3 = t ^ 2) : t = 1 ∨ t = 0 := by
      sorry
    ```
11. Let \(n\) be any natural number. Show that \(n ^ 2 \ne 7\).

    You will probably use the same lemma as in Example 2.3.2.

    ```lean
    example {n : ℕ} : n ^ 2 ≠ 7 := by
      sorry
    ```
12. Let \(x\) be any integer. Show that \(2x \ne 3\).

    You will probably use the same lemma as in Example 2.3.2.

    ```lean
    example {x : ℤ} : 2 * x ≠ 3 := by
      sorry
    ```
13. Let \(t\) be any integer. Show that \(5t \ne 18\).

    You will probably use the same lemma as in Example 2.3.2.

    ```lean
    example {t : ℤ} : 5 * t ≠ 18 := by
      sorry
    ```
14. Let \(m\) be any natural number. Show that \(m ^ 2 +4m\ne 46\).

    You will probably use the same lemma as in Example 2.3.2.

    ```lean
    example {m : ℕ} : m ^ 2 + 4 * m ≠ 46 := by
      sorry
    ```

### 2.4. “And”

#### 2.4.1. Example

In mathematics, the word “and” (denoted \(\land\) as a logical symbol), like “or”, can connect
two statements. For example, here is a problem in which the hypothesis is an “and”-statement.

> **Problem:**
>
> Let \(x\) and \(y\) be integers and suppose that \(2x-y=4\)
> and \(y-x+1=2\). Prove that \(x=5\).

In fact, we studied this problem before, in [Example 1.3.6]](#m2001-simultaneous). Then, we
considered the problem as having two separate hypotheses:

- \(2x-y=4\)
- \(y-x+1=2\)

but now for the sake of argument we’re considering it as having a single hypothesis:

- \(2x-y=4\) and \(y-x+1=2\).

The distinction is pretty pedantic, and invisible in the text. In Lean, it’s more visible: we
might find ourselves in a situation with a hypothesis which explicitly features the `∧` symbol,
like

```
x y : ℤ
h : 2 * x - y = 4 ∧ y - x + 1 = 2
⊢ x = 5
```

In such a situation, the tactic `obtain` will split up an “and” hypothesis into its constituent
parts,

```
x y : ℤ
h1 : 2 * x - y = 4
h2 : y - x + 1 = 2
⊢ x = 5
```

and then the problem can be solved accessing each of these parts separately, as needed. In this
case we have effectively brought the problem back to the setup of
[Example 1.3.6]](#m2001-simultaneous).

```lean
example {x y : ℤ} (h : 2 * x - y = 4 ∧ y - x + 1 = 2) : x = 5 := by
  obtain ⟨h1, h2⟩ := h
  calc
    x = 2 * x - y + (y - x + 1) - 1 := by ring
    _ = 4 + 2 - 1 := by rw [h1, h2]
    _ = 5 := by ring
```

#### 2.4.2. Example

“And” hypotheses turn up relatively rarely in the wild. One situation in which one might occur is
when a single hypothesis has two natural consequences, which are paired together in a lemma. For
example, if \(x^2 \le y^2\) for some positive number \(y\), then one can draw the conclusion
\(-y \le x \le y\), which is shorthand (recall [Example 1.4.4]](#m2001-shorthand)) for
“\(-y\leq x\) and \(x\le y\).”

> **Problem:**
>
> Let \(p\) be a rational number for which \(p^2\le 8\). Show that \(p\ge -5\).

> **Solution:**
>
> We have that
>
> \[\begin{split}p^2&\le 9\\
> &= 3^2,\end{split}\]
>
> and 3 is positive, so \(-3\le p\le 3\). Thus
>
> \[\begin{split}p&\ge -3\\
> &\ge -5.\end{split}\]

In Lean, we use the lemma `abs_le_of_sq_le_sq'` for this argument. This lemma was added to Lean’s
library by a Fordham student, Ben Davidson. Note the `∧` in the conclusion of the lemma statement.

```lean
theorem abs_le_of_sq_le_sq' {x y : ℚ} (h1 : x ^ 2 ≤ y ^ 2) (h2 : 0 ≤ y) :
    -y ≤ x ∧ x ≤ y :=
```

Exercise: I have written up in Lean the part of the proof which gets us to the intermediate fact
`hp' : -3 ≤ p ∧ p ≤ 3`. Deal with this “and” hypothesis and then finish the proof in Lean.

```lean
example {p : ℚ} (hp : p ^ 2 ≤ 8) : p ≥ -5 := by
  have hp' : -3 ≤ p ∧ p ≤ 3
  · apply abs_le_of_sq_le_sq'
    calc
      p ^ 2 ≤ 9 := by addarith [hp]
      _ = 3 ^ 2 := by numbers
    numbers
  sorry
```

Note a new piece of Lean syntax in the above proof: a line like

```lean
have hp' : -3 ≤ p ∧ p ≤ 3
```

without a justification will cause Lean to ask you for that justification – that is, a new goal
will appear, the goal of proving that statement.

```
2 goals

p : ℚ
hp : p ^ 2 ≤ 8
⊢ -3 ≤ p ∧ p ≤ 3

p : ℚ
hp : p ^ 2 ≤ 8
hp' : -3 ≤ p ∧ p ≤ 3
⊢ p ≥ -5
```

After you complete the proof (as I did here),
you are back to where you were before except that the fact `hp'` is now a fully-justified
intermediate fact, available for use.

#### 2.4.3. Example

It also sometimes happens that the goal of a problem features an “and” statement. For example, you
might be given a system of simultaneous equations, and asked to determine the values of all the
variables appearing. Here’s a system of simultaneous equations we saw before in
[Example 1.3.3]](#m2001-id22), but the problem statement has been tweaked to ask us to find the
values of both \(a\) and \(b\).

> **Problem:**
>
> Let \(a\) and \(b\) be real numbers and suppose that \(a-5b=4\)
> and \(b+2=3\). Show that \(a=9\) and \(b=1\).

One way to solve such a problem is to establish the two facts asked for completely independently:

> **Solution:**
>
> We have,
>
> \[\begin{split}a
> &= 4 + 5b \\
> &= -6 + 5(b + 2) \\
> &= -6 + 5 \cdot 3 \\
> &= 9,\end{split}\]
>
> and since \(b+2=3\), we have \(b=1\).

In Lean, we write this proof using the `constructor` tactic, which takes a problem in which the
goal is an “and” statement,

```lean
a b : ℝ
h1 : a - 5 * b = 4
h2 : b + 2 = 3
⊢ a = 9 ∧ b = 1
```

and reduces it two simpler tasks, one for each part of the “and”.

```lean
a b : ℝ
h1 : a - 5 * b = 4
h2 : b + 2 = 3
⊢ a = 9

a b : ℝ
h1 : a - 5 * b = 4
h2 : b + 2 = 3
⊢ b = 1
```

We then write up Lean proofs for these two tasks, one after the other.

```lean
example {a b : ℝ} (h1 : a - 5 * b = 4) (h2 : b + 2 = 3) : a = 9 ∧ b = 1 := by
  constructor
  · calc
      a = 4 + 5 * b := by addarith [h1]
      _ = -6 + 5 * (b + 2) := by ring
      _ = -6 + 5 * 3 := by rw [h2]
      _ = 9 := by ring
  · addarith [h2]
```

Alternatively, there might be an intermediate fact which you wish to note and then use in both
parts of the proof. For example, you might want to first solve for \(b\), and then use that to
shorten the work of solving for \(a\).

> **Solution:**
>
> Since \(b+2=3\), we have \(b=1\). Therefore
>
> \[\begin{split}a
> &= 4 + 5b \\
> &= 4 + 5 \cdot 3 \\
> &= 9.\end{split}\]

It is typically left to the reader to check that both parts of the desired goal, \(a=9\) and
\(b=1\), are established somewhere along the way.

Here is how this proof looks in Lean. The important observation is that if there is something you
want to use in both parts of the proof (here, the fact \(b=1\)), then this must be established
before the tactic `constructor` is used. When the tactic `constructor` is used, all facts
established so far become available for both of the two tasks created:

```
a b : ℝ
h1 : a - 5 * b = 4
h2 : b + 2 = 3
hb : b = 1
⊢ a = 9

a b : ℝ
h1 : a - 5 * b = 4
h2 : b + 2 = 3
hb : b = 1
⊢ b = 1
```

```lean
example {a b : ℝ} (h1 : a - 5 * b = 4) (h2 : b + 2 = 3) : a = 9 ∧ b = 1 := by
  have hb : b = 1 := by addarith [h2]
  constructor
  · calc
      a = 4 + 5 * b := by addarith [h1]
      _ = 4 + 5 * 1 := by rw [hb]
      _ = 9 := by ring
  · apply hb
```

#### 2.4.4. Example

One more example with an “and” in the goal.

> **Problem:**
>
> Let \(a\) and \(b\) be real numbers and suppose that \(a^2+b^2=0\). Show that
> \(a=0\) and \(b=0\).

> **Solution:**
>
> We first show that \(a^2=0\). \((\star)\) Indeed,
>
> \[\begin{split}a ^ 2 &\le a ^ 2 + b ^ 2\\
> &=0.\end{split}\]
>
> and also since squares are nonnegative \(a^2\geq 0\).
>
> By \((\star)\), \(a=0\). Also by \((\star)\),
>
> \[\begin{split}b ^ 2 &= a ^ 2 + b ^ 2\\
> &=0,\end{split}\]
>
> so \(b=0\).

Part of this problem might feel familiar. The proof of the intermediate goal, \(a^2=0\), is
a repeat of Example 2.2.3.

Here is the problem statement in Lean, together with a proof of the intermediate goal \(a^2=0\)
copied from Example 2.2.3. Fill in the rest of the proof. The tactic
`cancel` will be helpful to deduce that quantities are zero when their squares are known to be
zero.

```lean
example {a b : ℝ} (h1 : a ^ 2 + b ^ 2 = 0) : a = 0 ∧ b = 0 := by
  have h2 : a ^ 2 = 0
  · apply le_antisymm
    calc
      a ^ 2 ≤ a ^ 2 + b ^ 2 := by extra
      _ = 0 := by rw [h1]
    extra
  sorry
```

#### 2.4.5. Exercises

1. Let \(a\) and \(b\) be rational numbers and suppose that
   \(a \le 1\) and \(a + b \le 3\). Show that \(2a+b \le 4\).

   ```lean
   example {a b : ℚ} (H : a ≤ 1 ∧ a + b ≤ 3) : 2 * a + b ≤ 4 := by
     sorry
   ```
2. Let \(r\) and \(s\) be real numbers and suppose that
   \(r + s \le 1\) and \(r - s \le 5\). Show that \(2r \le 6\).

   ```lean
   example {r s : ℝ} (H : r + s ≤ 1 ∧ r - s ≤ 5) : 2 * r ≤ 6 := by
     sorry
   ```
3. Let \(m\) and \(n\) be integers and suppose that
   \(n \le 8\) and \(m + 5 \le n\). Show that \(m \le 3\).

   ```lean
   example {m n : ℤ} (H : n ≤ 8 ∧ m + 5 ≤ n) : m ≤ 3 := by
     sorry
   ```
4. Let \(p\) be an integer and suppose that \(p + 2 \ge 9\). Show that \(p^2\geq 49\)
   and \(7 \le p\).

   ```lean
   example {p : ℤ} (hp : p + 2 ≥ 9) : p ^ 2 ≥ 49 ∧ 7 ≤ p := by
     sorry
   ```
5. Let \(a\) be a rational number and suppose that \(a - 1 \ge 5\). Show that
   \(a \ge 6\) and \(3a \ge 10\).

   ```lean
   example {a : ℚ} (h : a - 1 ≥ 5) : a ≥ 6 ∧ 3 * a ≥ 10 := by
     sorry
   ```
6. Let \(x\) and \(y\) be rational numbers and suppose that \(x + y = 5\) and
   \(x + 2y = 7\). Show that \(x=3\) and \(y=2\).

   ```lean
   example {x y : ℚ} (h : x + y = 5 ∧ x + 2 * y = 7) : x = 3 ∧ y = 2 := by
     sorry
   ```
7. Let \(a\) and \(b\) be real numbers and suppose that \(ab=a=b\). Show that either
   \(a=b=0\) or \(a=b=1\).

   You will probably need the lemma `eq_zero_or_eq_zero_of_mul_eq_zero` proved in
   Example 2.3.4.

   ```lean
   example {a b : ℝ} (h1 : a * b = a) (h2 : a * b = b) :
       a = 0 ∧ b = 0 ∨ a = 1 ∧ b = 1 := by
     sorry
   ```

### 2.5. Existence proofs

#### 2.5.1. Example

This section covers the *existential quantifier*, the logical concept expressed in English as

> There exists … such that ….

For example, here is a problem in which a hypothesis features an existential quantifier.

> **Problem:**
>
> Let \(a\) be a rational number, and suppose that there exists a rational number \(b\),
> such that \(a=b^2+1\). Show that \(a>0\).

The hypothesis “there exists a rational number \(b\), such that \(a=b^2+1\)” can be broken
down immediately to actually get our hands on the *witness* for the existential: a rational number
\(b\) satisfying \(a=b^2+1\) (there might be more than one, but this will just choose one
witness). Then we can do a regular calculational proof which involves the witness \(b\).

> **Solution:**
>
> Let \(b\) be a rational number such that \(a=b^2+1\). We have,
>
> \[\begin{split}a &=b^2+1\\
> &>0.\end{split}\]

The logical symbol for an existential is \(\exists\). In Lean, the tactic `obtain` is used to
break up an existential hypothesis into a witness and a hypothesis about that witness. The syntax
is the same as the syntax for breaking apart an “and” (see Section 2.4).

```lean
example {a : ℚ} (h : ∃ b : ℚ, a = b ^ 2 + 1) : a > 0 := by
  obtain ⟨b, hb⟩ := h
  calc
    a = b ^ 2 + 1 := hb
    _ > 0 := by extra
```

In this problem, the goal view is initially

```lean
a : ℚ
h : ∃ b, a = b ^ 2 + 1
⊢ a > 0
```

After the use of the `obtain` tactic, the existential is broken apart, so the witness appears
separately on the variable list and can be accessed in the succeeding proof:

```lean
a b : ℚ
hb : a = b ^ 2 + 1
⊢ a > 0
```

#### 2.5.2. Example

Here’s another problem with an existential hypothesis, “there exists a real number \(a\),
such that \(at<0\)”. As before we break it apart and then follow earlier methods.

> **Problem:**
>
> Let \(t\) be a real number, and suppose that there exists a real number \(a\),
> such that \(at<0\). Show that \(t\ne 0\).

> **Solution:**
>
> Let \(x\) be a real number such that \(xt<0\). We consider separately the cases
> \(x\le 0\) and \(0<x\).
>
> **Case 1** (\(x \le 0\)): We have \(0<(-x)t\) and \(0 \le -x\), so we have that
> \(0 < t\), and so \(t\ne 0\).
>
> **Case 2** (\(0<x\)): We have that
>
> \[\begin{split}0&<-xt\\
> &=x(-t)\end{split}\]
>
> and \(0 \le x\), so \(0 < -t\), so \(t<0\), so \(t\ne 0\),

Here is a partial solution in Lean (using `obtain` for the first step to break apart the
existential, and then again later to perform the case division). Case 2 is missing; do that
yourself.

```lean
example {t : ℝ} (h : ∃ a : ℝ, a * t < 0) : t ≠ 0 := by
  obtain ⟨x, hxt⟩ := h
  have H := le_or_gt x 0
  obtain hx | hx := H
  · have hxt' : 0 < (-x) * t := by addarith [hxt]
    have hx' : 0 ≤ -x := by addarith [hx]
    cancel -x at hxt'
    apply ne_of_gt
    apply hxt'
  · sorry
```

#### 2.5.3. Example

To prove a problem in which the goal has an existential, you need to provide a witness yourself, and
then verify that your proposed witness works.

> **Problem:**
>
> Show that there exists an integer \(n\), such that \(12n=84\).

> **Solution:**
>
> The integer \(7\) has this property. Indeed, \(12 \cdot 7=84\).

In Lean the tactic `use` is used to state what you have chosen as the witness.

```lean
example : ∃ n : ℤ, 12 * n = 84 := by
  use 7
  numbers
```

In this problem, the goal was initially

```
⊢ ∃ n : ℤ, 12 * n = 84
```

but after the tactic `use 7` the goal has changed to checking that the proposed witness, 7,
works.

```
⊢ 12 * 7 = 84
```

This is what `numbers` checks.

#### 2.5.4. Example

Often, it requires some creativity to come up with a witness for an existential goal. The rest of
this section consists of practice coming up with witnesses.

> **Problem:**
>
> Let \(x\) be a real number. Show that there exists a real number \(y\), such that
> \(y>x\).

> **Solution:**
>
> The real number \(x + 1\) has this property. Indeed, \(x+1>x\).

```lean
example (x : ℝ) : ∃ y : ℝ, y > x := by
  use x + 1
  extra
```

#### 2.5.5. Example

> **Problem:**
>
> Show that there exist integers \(m\) and \(n\), such that
> \(m^2-n^2=11\).

> **Solution:**
>
> We can take \(m=6\), \(n=5\). Indeed,
>
> \[\begin{split}6^2-5^2&=36-25\\
> &=11.\end{split}\]

```lean
example : ∃ m n : ℤ, m ^ 2 - n ^ 2 = 11 := by
  sorry
```

#### 2.5.6. Example

Sometimes you may wish to leave out the sentence “We can take …” explicitly stating what the
witness is. In this case you should be extra pedantic about verifying the desired properties
exactly in the form they are stated.

> **Problem:**
>
> Let \(a\) be an integer. Show that there exist integers \(m\) and \(n\), such that
> \(m^2-n^2=2a+1\).

> **Solution:**
>
> \[(a+1)^2-a^2=2a+1.\]

In Lean, you still have to use `use` to state the witnesses.

```lean
example (a : ℤ) : ∃ m n : ℤ, m ^ 2 - n ^ 2 = 2 * a + 1 := by
  sorry
```

#### 2.5.7. Example

> **Problem:**
>
> Let \(p\) and \(q\) be real numbers, and suppose \(p<q\). Show that there exists a
> real number \(x\), such that \(p<x<q\).

> **Solution:**
>
> We will show that \(\frac{p+q}{2}\) has this property. Indeed,
>
> \[\begin{split}p&=\frac{p+p}{2}\\
> &<\frac{p+q}{2},\end{split}\]
>
> and
>
> \[\begin{split}\frac{p+q}{2} &<\frac{q+q}{2}\\
> &=q.\end{split}\]

```lean
example {p q : ℝ} (h : p < q) : ∃ x, p < x ∧ x < q := by
  sorry
```

#### 2.5.8. Example

> I remember once going to see him [Ramanujan] when he was lying ill at Putney. I had ridden in taxi cab number
> 1729 and remarked that the number seemed to me rather a dull one, and that I hoped it was not an
> unfavorable omen. “No,” he replied, “it is a very interesting number; it is the smallest number
> expressible as the sum of two cubes in two different ways.”
>
> —G. H. Hardy, *Ramanujan: Twelve lectures on subjects suggested by his life and work*

> **Problem:**
>
> Show that there exist natural numbers \(a\), \(b\), \(c\) and \(d\), such that
> \(a^3+b^3=1729=c^3+d^3\), but \(a\ne c\) and \(a\ne d\). 3

> **Solution:**
>
> \(1^3+12^3=1729=9^3+10^3\), but \(1\ne 9\) and \(1\ne 10\).

```lean
example : ∃ a b c d : ℕ,
    a ^ 3 + b ^ 3 = 1729 ∧ c ^ 3 + d ^ 3 = 1729 ∧ a ≠ c ∧ a ≠ d := by
  use 1, 12, 9, 10
  constructor
  numbers
  constructor
  numbers
  constructor
  numbers
  numbers
```

#### 2.5.9. Exercises

1. Show that there exists a rational number \(t\), such that \(t^2=1.69\).

   ```lean
   example : ∃ t : ℚ, t ^ 2 = 1.69 := by
     sorry
   ```
2. Show that there exist integers \(m\) and \(n\), such that \(m^2+n^2=85\).

   ```lean
   example : ∃ m n : ℤ, m ^ 2 + n ^ 2 = 85 := by
     sorry
   ```
3. Show that there exists a real number \(x\), such that \(x<0\) and \(x^2<1\).

   ```lean
   example : ∃ x : ℝ, x < 0 ∧ x ^ 2 < 1 := by
     sorry
   ```
4. Show that there exist natural numbers \(a\) and \(b\), such that \(2 ^ a = 5b+1\).

   ```lean
   example : ∃ a b : ℕ, 2 ^ a = 5 * b + 1 := by
     sorry
   ```
5. Let \(x\) be a rational number. Show that there exists a rational number \(y\), such
   that \(y^2>x\).

   ```lean
   example (x : ℚ) : ∃ y : ℚ, y ^ 2 > x := by
     sorry
   ```
6. Let \(t\) be a real number, and suppose that there exists a real number \(a\),
   such that \(at+1<a+t\). Show that \(t\ne 1\).

   As in Example 2.5.2, I used the lemma `le_or_gt`, which says
   that if \(s\) and \(t\) are real numbers then
   either \(s \le t\) or \(t < s\); it can be a useful case division.

   ```lean
   example {t : ℝ} (h : ∃ a : ℝ, a * t + 1 < a + t) : t ≠ 1 := by
     sorry
   ```
7. Let \(m\) be an integer, and suppose that there exists an integer \(a\),
   such that \(2a=m\). Show that \(m\ne 5\).

   ```lean
   example {m : ℤ} (h : ∃ a, 2 * a = m) : m ≠ 5 := by
     sorry
   ```
8. Let \(n\) be an integer.
   Show that there exists an integer \(a\), such that \(2a^3 ≥ na+7\).

   ```lean
   example {n : ℤ} : ∃ a, 2 * a ^ 3 ≥ n * a + 7 := by
     sorry
   ```
9. Let \(a\), \(b\) and \(c\) be real numbers, and suppose that
   \(a\le b+c\), \(b\le a+c\) and \(c\le a+b\). Show that there exist nonnegative
   real numbers \(x\), \(y\) and \(z\) such that \(a=y+z\), \(b=x+z\) and
   \(c=x+y\).

   ```lean
   example {a b c : ℝ} (ha : a ≤ b + c) (hb : b ≤ a + c) (hc : c ≤ a + b) :
       ∃ x y z, x ≥ 0 ∧ y ≥ 0 ∧ z ≥ 0 ∧ a = y + z ∧ b = x + z ∧ c = x + y := by
     sorry
   ```

Footnotes

3
:   Example adapted from Hammack,
    [Book of Proof](https://www.people.vcu.edu/~rhammack/BookOfProof/), Section 7.3.

---



## 3. Parity and Divisibility {#m2001-3-parity-and-divisibility}

> 📄 Source: https://hrmacbeth.github.io/math2001/03_Parity_and_Divisibility.html

The problems in this chapter concern some elementary properties of natural numbers
and integers: *parity* (whether a number is *even* or *odd*), *divisibility*, and
*congruence modulo* \(n\).

There are no new logical symbols like \(\lor\) or \(\exists\) in this
chapter, and no major additions (such as the use of intermediate steps or lemmas)
to our reasoning toolkit. Thus this chapter functions as a breather between the
hard work of [Chapter 2]](#m2001-proofs-with-structure) and
[Chapter 4](https://hrmacbeth.github.io/math2001/04_Proofs_with_Structure_II.html#proofs-with-structure-ii), a chance to consolidate what you
have learned.

### 3.1. Definitions; parity

#### 3.1.1. Example

When a mathematical term is introduced for the first time, it is given a definition.

> **Definition:**
>
> An integer \(a\) is *odd*, if there exists an integer \(k\), such that \(a=2k+1\).

Here’s how we make a definition in Lean.

```lean
def Odd (a : ℤ) : Prop := ∃ k, a = 2 * k + 1
```

On paper, you need to memorize every definition; this is crucial to be able to work with them.
In Lean, when you see a term whose definition you’d like to double-check, you can right-click
(Windows) / two-finger-click (Mac) to bring up an option “Go to Definition”. This will send you
to the place in the Lean code where the definition was introduced. (The keyboard shortcut
`Ctrl`-`-` will bring you back from that definition to where you were working before.)

> **Problem:**
>
> Show that \(7\) is odd.

> **Solution:**
>
> \(7=2\cdot 3+1\), so \(7\) is odd.

Here’s how this problem looks in Lean.

```lean
example : Odd (7:ℤ) := by
  sorry
```

The goal state at the start is:

```
⊢ Odd 7
```

You can check a definition within a proof using the `dsimp` (“definitional-simplify”) tactic.
Here, you can type `dsimp [Odd]` within the proof to have the definition of “odd” unfolded for
you:

```lean
example : Odd (7:ℤ) := by
  dsimp [Odd]
```

After doing this, the goal is displayed using the definition of “odd” rather than with the word
“odd”:

```
⊢ ∃ (k : ℤ), 7 = 2 * k + 1
```

This is optional, though; the proof will still work if you delete the `dsimp` line.

Here is the full Lean translation of the solution.

```lean
example : Odd (7 : ℤ) := by
  dsimp [Odd]
  use 3
  numbers
```

If you don’t quite follow the proof – either in its text version or its Lean version – then
reread some of the examples in [Section 2.5]](#m2001-exists), such as
[Example 2.5.3]](#m2001-simple-existential).

#### 3.1.2. Example

Here’s a similar example.

> **Problem:**
>
> Show that \(-3\) is odd.

```lean
example : Odd (-3 : ℤ) := by
  sorry
```

You should show that \(-3=2k+1\) for some integer \(k\). But what is the \(k\) that
works?

#### 3.1.3. Example

> **Problem:**
>
> Prove that if \(n\) is an odd integer, then \(3n+2\) is odd.

> **Solution:**
>
> Let n be odd. Then there exists an integer \(k\) such that \(n = 2k+1\). Thus
>
> \[\begin{split}3 n + 2 &= 3 (2 k + 1) + 2 \\
> & = 2 (3 k + 2) + 1,\end{split}\]
>
> so \(3n+2\) is odd.

In the Lean version of the problem, the goal state initially looks like this.

```
n : ℤ
hn : Odd n
⊢ Odd (3 * n + 2)
```

If you want to unfold the definition `Odd` in the goal, you can use `dsimp [Odd]`, as
discussed in Example 3.1.1. If you want to unfold the definition `Odd` everywhere
(goals and hypotheses), you can use `dsimp [Odd] at *`. After that the goal state looks like
this:

```
n : ℤ
hn : ∃ (k : ℤ), n = 2 * k + 1
⊢ ∃ (k : ℤ), 3 * n + 2 = 2 * k + 1
```

This is our first encounter with something which might be confusing. The variable inside an
existential is a “throwaway” variable, which exists only for the duration of the sentence. So the
same variable might occur within the goal state repeatedly, without meaning the same thing. Here
the `k` in the hypothesis `hn` is different from the `k` in the goal; it’s just the default
name when the definition “odd” is unfolded.

```lean
example {n : ℤ} (hn : Odd n) : Odd (3 * n + 2) := by
  dsimp [Odd] at *
  obtain ⟨k, hk⟩ := hn
  use 3 * k + 2
  calc
    3 * n + 2 = 3 * (2 * k + 1) + 2 := by rw [hk]
    _ = 2 * (3 * k + 2) + 1 := by ring
```

Again, you can check that the `dsimp` line is not actually needed in the solution.

#### 3.1.4. Example

> **Problem:**
>
> Let \(n\) be an integer. Prove that if \(n\) is odd, then \(7n-4\) is odd.

```lean
example {n : ℤ} (hn : Odd n) : Odd (7 * n - 4) := by
  sorry
```

#### 3.1.5. Example

> **Problem:**
>
> Prove that if the integers \(x\) and \(y\) are odd, then \(x+y+1\) is odd.

> **Solution:**
>
> Since \(x\) and \(y\) are odd, there exists an integer \(a\) such that
> \(x=2a+1\), and there exists an integer \(b\) such that \(y=2b+1\). Then
>
> \[\begin{split}x + y + 1 &= (2 a + 1) + (2 b + 1) + 1 \\
> &= 2 (a + b + 1) + 1,\end{split}\]
>
> so \(x+y+1\) is odd.

```lean
example {x y : ℤ} (hx : Odd x) (hy : Odd y) : Odd (x + y + 1) := by
  obtain ⟨a, ha⟩ := hx
  obtain ⟨b, hb⟩ := hy
  use a + b + 1
  calc
    x + y + 1 = 2 * a + 1 + (2 * b + 1) + 1 := by rw [ha, hb]
    _ = 2 * (a + b + 1) + 1 := by ring
```

#### 3.1.6. Example

> **Problem:**
>
> Prove that if the integers \(x\) and \(y\) are odd, then \(xy+2y\) is odd.

> **Solution:**
>
> Since \(x\) and \(y\) are odd, there exists an integer \(a\) such that
> \(x=2a+1\), and there exists an integer \(b\) such that \(y=2b+1\). Then
>
> \[\begin{split}x y + 2 y &= (2 a + 1) (2 b + 1) + 2 (2 b + 1) \\
> &= 2 (2 a b + 3 b + a + 1) + 1,\end{split}\]
>
> so \(xy+2y\) is odd.

```lean
example {x y : ℤ} (hx : Odd x) (hy : Odd y) : Odd (x * y + 2 * y) := by
  sorry
```

#### 3.1.7. Example

You can probably guess the definition of “even”.

> **Definition:**
>
> An integer \(a\) is *even*, if there exists an integer \(k\), such that \(a=2k\).

```lean
def Even (a : ℤ) : Prop := ∃ k, a = 2 * k
```

> **Problem:**
>
> Let \(m\) be an integer. Prove that if \(m\) is odd, then \(3m-5\) is even.

> **Solution:**
>
> Since m is odd, there exists an integer \(t\) such that \(m = 2t+1\). Thus
>
> \[\begin{split}3 m-5 &= 3 (2 t + 1) - 5 \\
> & = 2 (3 t - 1),\end{split}\]
>
> so \(3m-5\) is even.

```lean
example {m : ℤ} (hm : Odd m) : Even (3 * m - 5) := by
  sorry
```

#### 3.1.8. Example

> **Problem:**
>
> Let \(n\) be an even integer. Prove that \(n ^ 2 + 2 n - 5\) is odd.

```lean
example {n : ℤ} (hn : Even n) : Odd (n ^ 2 + 2 * n - 5) := by
  sorry
```

#### 3.1.9. Example

In fact every integer is either even or odd; we will discuss how to prove this later, in
[Example 4.2.9](https://hrmacbeth.github.io/math2001/04_Proofs_with_Structure_II.html#even-or-odd-proof).

In Lean this fact can be invoked as the lemma `Int.even_or_odd`:

```lean
lemma Int.even_or_odd (n : ℤ) : Even n ∨ Odd n :=
```

> **Problem:**
>
> Let \(n\) be an integer. Prove that \(n ^ 2 + n + 4\) is even.

> **Solution:**
>
> We consider two cases, depending on whether \(n\) is even or odd.
>
> If \(n\) is even, then there exists an integer \(x\) such that \(n=2x\). Then
>
> \[\begin{split}n ^ 2 + n + 4 &= (2 x) ^ 2 + 2 x + 4\\
> &= 2 (2 x ^ 2 + x + 2),\end{split}\]
>
> so \(n ^ 2 + n + 4\) is even.
>
> If \(n\) is odd, then there exists an integer \(x\) such that \(n=2x+1\). Then
>
> \[\begin{split}n ^ 2 + n + 4 &= (2 x + 1) ^ 2 + (2 x + 1) + 4 \\
> &= 2 (2 x ^ 2 + 3 x + 3),\end{split}\]
>
> so \(n ^ 2 + n + 4\) is again even.

```lean
example (n : ℤ) : Even (n ^ 2 + n + 4) := by
  obtain hn | hn := Int.even_or_odd n
  · obtain ⟨x, hx⟩ := hn
    use 2 * x ^ 2 + x + 2
    calc
      n ^ 2 + n + 4 = (2 * x) ^ 2 + 2 * x + 4 := by rw [hx]
      _ = 2 * (2 * x ^ 2 + x + 2) := by ring
  · obtain ⟨x, hx⟩ := hn
    use 2 * x ^ 2 + 3 * x + 3
    calc
      n ^ 2 + n + 4 = (2 * x + 1) ^ 2 + (2 * x + 1) + 4 := by rw [hx]
      _ = 2 * (2 * x ^ 2 + 3 * x + 3) := by ring
```

#### 3.1.10. Exercises

1. Prove that -9 is odd.

   ```lean
   example : Odd (-9 : ℤ) := by
     sorry
   ```
2. Prove that 26 is even.

   ```lean
   example : Even (26 : ℤ) := by
     sorry
   ```
3. Let \(m\) be an odd integer and \(n\) be an even integer.
   Prove that \(n + m\) is odd.

   ```lean
   example {m n : ℤ} (hm : Odd m) (hn : Even n) : Odd (n + m) := by
     sorry
   ```
4. Let \(p\) be an odd integer and let \(q\) be an even integer. Show that
   \(p - q - 4\) is odd.

   ```lean
   example {p q : ℤ} (hp : Odd p) (hq : Even q) : Odd (p - q - 4) := by
     sorry
   ```
5. Let \(a\) be an even integer and let \(b\) be an odd integer. Show that
   \(3a + b - 3\) is even.

   ```lean
   example {a b : ℤ} (ha : Even a) (hb : Odd b) : Even (3 * a + b - 3) := by
     sorry
   ```
6. Prove that if the integers \(r\) and \(s\) are odd, then \(3r-5s\) is even.

   ```lean
   example {r s : ℤ} (hr : Odd r) (hs : Odd s) : Even (3 * r - 5 * s) := by
     sorry
   ```
7. Let \(x\) be an integer. Show that if \(x\) is odd then so is \(x^3\).

   ```lean
   example {x : ℤ} (hx : Odd x) : Odd (x ^ 3) := by
     sorry
   ```
8. Let \(n\) be an odd integer. Show that \(n^2-3n+2\) is even.

   ```lean
   example {n : ℤ} (hn : Odd n) : Even (n ^ 2 - 3 * n + 2) := by
     sorry
   ```
9. Let \(a\) be an integer and suppose that \(a\) is odd. Show that \(a^2+2a-4\) is
   odd.

   ```lean
   example {a : ℤ} (ha : Odd a) : Odd (a ^ 2 + 2 * a - 4) := by
     sorry
   ```
10. Let \(p\) be an odd integer. Show that \(p^2+3p-5\) is odd.

    ```lean
    example {p : ℤ} (hp : Odd p) : Odd (p ^ 2 + 3 * p - 5) := by
      sorry
    ```
11. Let \(x\) and \(y\) be odd integers. Show that \(xy\) is odd.

    ```lean
    example {x y : ℤ} (hx : Odd x) (hy : Odd y) : Odd (x * y) := by
      sorry
    ```
12. Let \(n\) be an integer. Show that \(3n^2+3n- 1\) is odd.

    ```lean
    example (n : ℤ) : Odd (3 * n ^ 2 + 3 * n - 1) := by
      sorry
    ```
13. Let \(n\) be an integer. Show that there exists an integer \(m\geq n\) which is odd.

    ```lean
    example (n : ℤ) : ∃ m ≥ n, Odd m := by
      sorry
    ```
14. Let \(a\), \(b\) and \(c\) be integers. Show that at least one of \(a-b\),
    \(a+c\) or \(b-c\) is even. 1

    ```lean
    example (a b c : ℤ) : Even (a - b) ∨ Even (a + c) ∨ Even (b - c) := by
      sorry
    ```

Footnotes

1
:   Exercise taken from Hammack,
    [Book of Proof](https://www.people.vcu.edu/~rhammack/BookOfProof/), Chapter 9.

### 3.2. Divisibility

#### 3.2.1. Example

Another mathematical definition you may have seen before is the definition of divisibility.

> **Definition:**
>
> A natural number \(b\) *is divisible by* another natural number \(a\), if there exists
> a natural number \(c\), such that \(b=ac\).

For example,

> **Problem:**
>
> Show that the natural number 88 is divisible by 11.

> **Solution:**
>
> \(88 = 11 \cdot 8\).

Divisibility is a very important concept, and there are several different names for it. All the
following mean the same thing:

- \(b\) *is divisible by* \(a\)
- \(b\) *is a multiple of* \(a\)
- \(a\) *is a divisor of* \(b\)
- \(a\) *is a factor of* \(b\)
- \(a\) *divides* \(b\)

We will most frequently use the last of these, “\(a\) *divides* \(b\),” since it’s the most
compact. There is also a standard notation, \(a \mid b\), which we will also use frequently.

In Lean, the definition of divisibility is already in the library,
together with many theorems about it. We will typically work with this definition in Lean using
the notation, `∣`, which you can type in Lean as `\|` or `\mid`. (In general, hover over a
symbol in VSCode with your mouse to find out how to type it.) Warning: this is different from the
visually similar symbol `|` which appears on your keyboard and which we use in `obtain`
statements.

As in Section 3.1 with `dsimp [Odd]`, it is possible to unfold the definition of
divisibility in the middle of a Lean proof to remind yourself of what it means in the current
context. You can write either `dsimp [Dvd.dvd]` (this being the text name of the Lean notation
\(\mid\) for divisibility) or `dsimp [(· ∣ ·)]` – unfortunately `dsimp [∣]` without the
dots and parentheses does not work. As in the Section 3.1 examples, this unfolding
is optional – the proof still works without it.

```lean
example : (11 : ℕ) ∣ 88 := by
  dsimp [(· ∣ ·)]
  use 8
  numbers
```

#### 3.2.2. Example

Another feature of this definition is that, although we stated it above for natural numbers, we
will also often want to consider divisibility of integers. Here is the corresponding definition for
integers:

> **Definition:**
>
> An integer \(b\) *is divisible by* another integer \(a\), if there exists an integer
> \(c\), such that \(b=ac\).

We also use all the same variant terminology and the same notation \(a \mid b\) for integer
divisibility.

> **Problem:**
>
> Show that the integer 6 is divisible by -2.

> **Solution:**
>
> \(6 = -2 \cdot -3\).

```lean
example : (-2 : ℤ) ∣ 6 := by
  sorry
```

#### 3.2.3. Example

> **Problem:**
>
> Let \(a\) and \(b\) be integers and suppose that \(a \mid b\). Show that
> \(a \mid b^2+2b\).

> **Solution:**
>
> Since \(a \mid b\), there exists an integer \(k\) such that \(b=ak\). Then
>
> \[\begin{split}b ^ 2 + 2 b &= (a k) ^ 2 + 2 (a k) \\
> & = a (k (a k + 2)).\end{split}\]
>
> So \(a \mid b^2+2b\).

```lean
example {a b : ℤ} (hab : a ∣ b) : a ∣ b ^ 2 + 2 * b := by
  obtain ⟨k, hk⟩ := hab
  use k * (a * k + 2)
  calc
    b ^ 2 + 2 * b = (a * k) ^ 2 + 2 * (a * k) := by rw [hk]
    _ = a * (k * (a * k + 2)) := by ring
```

#### 3.2.4. Example

> **Problem:**
>
> Let \(a\), \(b\) and \(c\) be natural numbers and suppose that \(a \mid b\) and
> \(b ^2\mid c\). Show that \(a^2 \mid c\).

> **Solution:**
>
> Since \(a \mid b\), there exists a natural number \(x\) such that \(b=ax\).
> Since \(b^2 \mid c\), there exists a natural number \(y\) such that \(c=b^2y\). Then
>
> \[\begin{split}c &= b ^ 2 y \\
> & = (a x) ^ 2 y\\
> & = a ^ 2 (x ^ 2 y).\end{split}\]
>
> So \(a ^2 \mid c\).

Translate this solution into Lean.
As usual, if you wish, you can use the command `dsimp [(· ∣ ·)] at *` to unfold \(\mid\) to
its definition everywhere in the goal state, though this will have no effect on the correctness of
the proof.

```lean
example {a b c : ℕ} (hab : a ∣ b) (hbc : b ^ 2 ∣ c) : a ^ 2 ∣ c := by
  sorry
```

#### 3.2.5. Example

> **Problem:**
>
> Let \(x\), \(y\) and \(z\) be natural numbers and suppose that \(xy \mid z\).
> Show that \(x \mid z\).

> **Solution:**
>
> Since \(xy \mid z\), there exists a natural number \(t\) such that \(z=(xy)t\). Then
>
> \[\begin{split}z &= (xy)t \\
> & = x(yt).\end{split}\]
>
> So \(x \mid z\).

```lean
example {x y z : ℕ} (h : x * y ∣ z) : x ∣ z := by
  sorry
```

#### 3.2.6. Example

You might wonder how to show that a number is *not* divisible by another number. A convenient test
here is a theorem which we will prove later in the book, in
[Example 4.5.8](https://hrmacbeth.github.io/math2001/04_Proofs_with_Structure_II.html#not-divisible-proof): if an integer \(a\) is strictly between two
consecutive multiples of an integer \(b\), then it is not a multiple of
\(b\). More formally, if there exists an integer \(q\) such that
\(bq<a<b(q + 1)\), then \(a\) is not a multiple of \(b\). Here is an
example applying this test:

> **Problem:**
>
> Show that 12 is not divisible by 5.

> **Solution:**
>
> \(5 \cdot 2 < 12 < 5 \cdot (2 + 1)\).

In Lean, this test is available as the lemma `Int.not_dvd_of_exists_lt_and_lt`:

```lean
lemma Int.not_dvd_of_exists_lt_and_lt (a b : ℤ)
  (h : ∃ q, b * q < a ∧ a < b * (q + 1)) :
  ¬b ∣ a :=
```

And here is the same solution written up in Lean.

```lean
example : ¬(5 : ℤ) ∣ 12 := by
  apply Int.not_dvd_of_exists_lt_and_lt
  use 2
  constructor
  · numbers -- show `5 * 2 < 12`
  · numbers -- show `12 < 5 * (2 + 1)`
```

#### 3.2.7. Example

> **Problem:**
>
> Let \(a\) and \(b\) be natural numbers, with \(b\) positive, and suppose that
> \(a\) divides \(b\). Show that \(a \le b\).

> **Solution:**
>
> Since \(a \mid b\), there exists a natural number \(k\) such that \(b=ak\).
>
> We first note that
>
> \[0 < b =ak\]
>
> so \(0<k\). Thus in fact \(1 \le k\).
>
> Now, we have
>
> \[\begin{split}a &= a \cdot 1 \\
> &≤ a k \\
> &= b.\end{split}\]

```lean
example {a b : ℕ} (hb : 0 < b) (hab : a ∣ b) : a ≤ b := by
  obtain ⟨k, hk⟩ := hab
  have H1 :=
    calc
      0 < b := hb
      _ = a * k := hk
  cancel a at H1
  have H : 1 ≤ k := H1
  calc
    a = a * 1 := by ring
    _ ≤ a * k := by rel [H]
    _ = b := by rw [hk]
```

This lemma is available in the main Lean library under the name `Nat.le_of_dvd`.

#### 3.2.8. Example

> **Problem:**
>
> Let \(a\) and \(b\) be natural numbers, with \(b\) positive, and suppose that
> \(a\) divides \(b\). Show that \(a\) is positive.

> **Solution:**
>
> Since \(a \mid b\), there exists a natural number \(k\) such that \(b=ak\).
>
> We have
>
> \[0 < b = a k,\]
>
> so \(0<a\).

```lean
example {a b : ℕ} (hab : a ∣ b) (hb : 0 < b) : 0 < a := by
  sorry
```

This lemma is also available in the main Lean library, under the name `Nat.pos_of_dvd_of_pos`.

#### 3.2.9. Exercises

1. Show that 0 is divisible by every integer \(t\).

   ```lean
   example (t : ℤ) : t ∣ 0 := by
     sorry
   ```
2. Show that -10 is not divisible by 3.

   ```lean
   example : ¬(3 : ℤ) ∣ -10 := by
     sorry
   ```
3. Let \(x\) and \(y\) be integers and suppose that \(x \mid y\). Show that
   \(x \mid 3y-4y^2\).

   ```lean
   example {x y : ℤ} (h : x ∣ y) : x ∣ 3 * y - 4 * y ^ 2 := by
     sorry
   ```
4. Let \(m\) and \(n\) be integers and suppose that \(m \mid n\). Show that
   \(m \mid 2n^3+n\).

   ```lean
   example {m n : ℤ} (h : m ∣ n) : m ∣ 2 * n ^ 3 + n := by
     sorry
   ```
5. Let \(a\) and \(b\) be integers and suppose that \(a \mid b\). Show that
   \(a \mid 2b^3-b^2+3b\).

   ```lean
   example {a b : ℤ} (hab : a ∣ b) : a ∣ 2 * b ^ 3 - b ^ 2 + 3 * b := by
     sorry
   ```
6. Let \(k\), \(l\) and \(m\) be integers and suppose that \(k\) divides \(l\)
   and \(l^3\) divides \(m\). Show that \(k^3\) divides \(m\).

   ```lean
   example {k l m : ℤ} (h1 : k ∣ l) (h2 : l ^ 3 ∣ m) : k ^ 3 ∣ m := by
     sorry
   ```
7. Let \(p\), \(q\) and \(r\) be integers and suppose that \(p^3\) divides \(q\)
   and \(q^2\) divides \(r\). Show that \(p^6\) divides \(r\).

   ```lean
   example {p q r : ℤ} (hpq : p ^ 3 ∣ q) (hqr : q ^ 2 ∣ r) : p ^ 6 ∣ r := by
     sorry
   ```
8. Show that there exists a natural number \(n>0\), such that \(9\) is a factor of
   \(2^n-1\).

   ```lean
   example : ∃ n : ℕ, 0 < n ∧ 9 ∣ 2 ^ n - 1 := by
     sorry
   ```
9. Show that there exists integers \(a\) and \(b\), with \(0<b<a\), such that
   \(a-b \mid a+b\).

   ```lean
   example : ∃ a b : ℤ, 0 < b ∧ b < a ∧ a - b ∣ a + b := by
     sorry
   ```

### 3.3. Modular arithmetic: theory

> **Definition:**
>
> The integers \(a\) and \(b\) are *congruent modulo* \(n\), if \(n\mid (a - b)\).

We use the notation \(a\equiv b \mod n\) to denote that \(a\) and \(b\) are congruent
modulo \(n\).

```lean
def Int.ModEq (n a b : ℤ) : Prop := n ∣ a - b

notation:50 a " ≡ " b " [ZMOD " n "]" => Int.ModEq n a b
```

#### 3.3.1. Example

> **Problem:**
>
> Show that \(11\equiv 3 \mod 4\).

> **Solution:**
>
> \(11-3=4\cdot 2\), so \(4\mid(11-3)\).

```lean
example : 11 ≡ 3 [ZMOD 4] := by
  use 2
  numbers
```

#### 3.3.2. Example

> **Problem:**
>
> Show that \(-5\equiv 1 \mod 3\).

> **Solution:**
>
> \(-5-1=3\cdot -2\), so \(3\mid(-5-1)\).

```lean
example : -5 ≡ 1 [ZMOD 3] := by
  sorry
```

#### 3.3.3. Example

> **Lemma (addition rule for modular arithmetic):**
>
> Let \(a\), \(b\), \(c\), \(d\) and \(n\) be integers, and suppose that
> \(a\equiv b \mod n\) and \(c\equiv d \mod n\). Then \(a+c\equiv b+d \mod n\).

> **Proof:**
>
> Since \(a\equiv b \mod n\), there exists an integer \(x\) such that \(a-b=nx\).
> Since \(c\equiv d \mod n\), there exists an integer \(y\) such that \(c-d=ny\).
> Then
>
> \[\begin{split}a + c - (b + d) &= (a - b) + (c - d) \\
> &= n x + n y \\
> &= n (x + y),\end{split}\]
>
> so \(a+c\equiv b+d \mod n\).

```lean
theorem Int.ModEq.add {n a b c d : ℤ} (h1 : a ≡ b [ZMOD n]) (h2 : c ≡ d [ZMOD n]) :
    a + c ≡ b + d [ZMOD n] := by
  dsimp [Int.ModEq] at *
  obtain ⟨x, hx⟩ := h1
  obtain ⟨y, hy⟩ := h2
  use x + y
  calc
    a + c - (b + d) = a - b + (c - d) := by ring
    _ = n * x + n * y := by rw [hx, hy]
    _ = n * (x + y) := by ring
```

#### 3.3.4. Exercise

> **Lemma (subtraction rule for modular arithmetic):**
>
> Let \(a\), \(b\), \(c\), \(d\) and \(n\) be integers, and suppose that
> \(a\equiv b \mod n\) and \(c\equiv d \mod n\). Then \(a-c\equiv b-d \mod n\).

```lean
theorem Int.ModEq.sub {n a b c d : ℤ} (h1 : a ≡ b [ZMOD n]) (h2 : c ≡ d [ZMOD n]) :
    a - c ≡ b - d [ZMOD n] := by
  sorry
```

#### 3.3.5. Exercise

> **Lemma (negation rule for modular arithmetic):**
>
> Let \(a\), \(b\) and \(n\) be integers, and suppose that
> \(a\equiv b \mod n\). Then \(-a\equiv -b \mod n\).

```lean
theorem Int.ModEq.neg {n a b : ℤ} (h1 : a ≡ b [ZMOD n]) : -a ≡ -b [ZMOD n] := by
  sorry
```

#### 3.3.6. Example

> **Lemma (multiplication rule for modular arithmetic):**
>
> Let \(a\), \(b\), \(c\), \(d\) and \(n\) be integers, and suppose that
> \(a\equiv b \mod n\) and \(c\equiv d \mod n\). Then \(ac\equiv bd \mod n\).

> **Proof:**
>
> Since \(a\equiv b \mod n\), there exists an integer \(x\) such that \(a-b=nx\).
> Since \(c\equiv d \mod n\), there exists an integer \(y\) such that \(c-d=ny\).
> Then
>
> \[\begin{split}a c - b d &= (a - b) c + b (c - d) \\
> &= (n x) c + b (n y) \\
> & = n (x c + b y),\end{split}\]
>
> so \(ac\equiv bd \mod n\).

```lean
theorem Int.ModEq.mul {n a b c d : ℤ} (h1 : a ≡ b [ZMOD n]) (h2 : c ≡ d [ZMOD n]) :
    a * c ≡ b * d [ZMOD n] := by
  obtain ⟨x, hx⟩ := h1
  obtain ⟨y, hy⟩ := h2
  use x * c + b * y
  calc
    a * c - b * d = (a - b) * c + b * (c - d) := by ring
    _ = n * x * c + b * (n * y) := by rw [hx, hy]
    _ = n * (x * c + b * y) := by ring
```

#### 3.3.7. Example

Warning: There is no “division rule” for modular arithmetic!

> **Problem:**
>
> It is possible to have integers \(a\), \(b\), \(c\), \(d\) and \(n\) with
> \(a\equiv b \mod n\) and \(c\equiv d \mod n\), but \(\frac{a}{c}\not\equiv \frac{b}{d} \mod n\).

> **Solution:**
>
> We can take \(a=10\), \(b=18\), \(c=2\), \(d=6\). Indeed,
>
> - \(10-18=4\cdot -2\), so \(10\equiv 18 \mod 4\);
> - \(2-6=4\cdot -1\), so \(2\equiv 6 \mod 4\);
> - \(\frac{10}{2}-\frac{18}{6}=2\) lies between the consecutive multiples \(4 \cdot 0\) and
>   \(4 \cdot (0 + 1)\) of 4, so \(\frac{10}{2}\not\equiv \frac{18}{6} \mod 4\).

Notice that here we’re using the test for non-divisibility from
Example 3.2.6.

#### 3.3.8. Example

> **Lemma (squaring rule for modular arithmetic):**
>
> Let \(a\), \(b\) and \(n\) be integers, and suppose that
> \(a\equiv b \mod n\). Then \(a^2\equiv b^2 \mod n\).

> **Proof:**
>
> Since \(a\equiv b \mod n\), there exists an integer \(x\) such that \(a-b=nx\).
> Then
>
> \[\begin{split}a ^ 2 - b ^ 2 &= (a - b) (a + b) \\
> &= (n x) (a + b) \\
> &= n (x (a + b)).\end{split}\]

```lean
theorem Int.ModEq.pow_two (h : a ≡ b [ZMOD n]) : a ^ 2 ≡ b ^ 2 [ZMOD n] := by
  obtain ⟨x, hx⟩ := h
  use x * (a + b)
  calc
    a ^ 2 - b ^ 2 = (a - b) * (a + b) := by ring
    _ = n * x * (a + b) := by rw [hx]
    _ = n * (x * (a + b)) := by ring
```

#### 3.3.9. Exercise

> **Lemma (cubing rule for modular arithmetic):**
>
> Let \(a\), \(b\) and \(n\) be integers, and suppose that
> \(a\equiv b \mod n\). Then \(a^3\equiv b^3 \mod n\).

```lean
theorem Int.ModEq.pow_three (h : a ≡ b [ZMOD n]) : a ^ 3 ≡ b ^ 3 [ZMOD n] := by
  sorry
```

In fact the same is true for any power, although we don’t yet have the tools to prove it. We’ll
come back to this later in the book, in [Example 6.1.3](https://hrmacbeth.github.io/math2001/06_Induction.html#modeq-pow-proof).

> **Lemma (power rule for modular arithmetic):**
>
> Let \(k\) be a natural number and let \(a\), \(b\) and \(n\) be integers, and
> suppose that \(a\equiv b \mod n\). Then \(a^k\equiv b^k \mod n\).

```lean
theorem Int.ModEq.pow (k : ℕ) (h : a ≡ b [ZMOD n]) : a ^ k ≡ b ^ k [ZMOD n] :=
  sorry -- we'll prove this later in the book
```

#### 3.3.10. Example

> **Lemma (reflexivity rule for modular arithmetic):**
>
> Let \(a\) and \(n\) be integers. Then \(a\equiv a \mod n\).

> **Proof:**
>
> \(a-a=n\cdot 0\), so \(n\mid a - a\).

```lean
theorem Int.ModEq.refl (a : ℤ) : a ≡ a [ZMOD n] := by
  use 0
  ring
```

#### 3.3.11. Example

Whew! That was a lot of lemmas. But they pay off, as you will now see. Suppose we now encounter
some very specific modular arithmetic problem of the same general type as we’ve seen before: the
congruence modulo \(n\) of two expressions which differ in a kind of spot-the-difference way,
like a “rewriting” by one congruence. For example,

> **Problem:**
>
> Let \(a\) and \(b\) be integers, and suppose that \(a\equiv 2 \mod 4\).
> Show that \(a b ^ 2 + a ^ 2 b + 3 a \equiv 2 b ^ 2 + 2 ^ 2 \cdot b + 3 \cdot 2 \mod 4\).

We could solve this by working directly from the definition, which is rather painful:

```lean
example {a b : ℤ} (ha : a ≡ 2 [ZMOD 4]) :
    a * b ^ 2 + a ^ 2 * b + 3 * a ≡ 2 * b ^ 2 + 2 ^ 2 * b + 3 * 2 [ZMOD 4] := by
  obtain ⟨x, hx⟩ := ha
  use x * (b ^ 2 + a * b + 2 * b + 3)
  calc
    a * b ^ 2 + a ^ 2 * b + 3 * a - (2 * b ^ 2 + 2 ^ 2 * b + 3 * 2) =
        (a - 2) * (b ^ 2 + a * b + 2 * b + 3) :=
      by ring
    _ = 4 * x * (b ^ 2 + a * b + 2 * b + 3) := by rw [hx]
    _ = 4 * (x * (b ^ 2 + a * b + 2 * b + 3)) := by ring
```

Or, better, we can solve this by applying the right combination of the lemmas we already proved,
in the right order. This requires much less thinking:

```lean
example {a b : ℤ} (ha : a ≡ 2 [ZMOD 4]) :
    a * b ^ 2 + a ^ 2 * b + 3 * a ≡ 2 * b ^ 2 + 2 ^ 2 * b + 3 * 2 [ZMOD 4] := by
  apply Int.ModEq.add
  apply Int.ModEq.add
  apply Int.ModEq.mul
  apply ha
  apply Int.ModEq.refl
  apply Int.ModEq.mul
  apply Int.ModEq.pow
  apply ha
  apply Int.ModEq.refl
  apply Int.ModEq.mul
  apply Int.ModEq.refl
  apply ha
```

#### 3.3.12. Exercises

1. Show that \(34\equiv 104 \mod 5\).

   ```lean
   example : 34 ≡ 104 [ZMOD 5] := by
     sorry
   ```
2. (symmetry rule for modular arithmetic) Let \(a\), \(b\) and \(n\) be integers, and
   suppose that \(a\equiv b \mod n\). Show that \(b\equiv a \mod n\).

   ```lean
   theorem Int.ModEq.symm (h : a ≡ b [ZMOD n]) : b ≡ a [ZMOD n] := by
     sorry
   ```
3. (transitivity rule for modular arithmetic) Let \(a\), \(b\), \(c\) and \(n\) be
   integers, and suppose that \(a\equiv b \mod n\) and \(b\equiv c \mod n\). Show that
   \(a\equiv c \mod n\).

   ```lean
   theorem Int.ModEq.trans (h1 : a ≡ b [ZMOD n]) (h2 : b ≡ c [ZMOD n]) :
       a ≡ c [ZMOD n] := by
     sorry
   ```
4. Let \(a\), \(c\) and \(n\) be integers. Show that \(a+nc\equiv a \mod n\).

   ```lean
   example : a + n * c ≡ a [ZMOD n] := by
     sorry
   ```
5. Give an alternative solution (i.e., with different numbers) to
   Example 3.3.7.
6. Let \(a\) and \(b\) be integers, and suppose that \(a \equiv b \mod 5\). Show that
   \(2a+3 \equiv 2b+3 \mod 5\).

   Give two solutions, following the style of the two solutions in
   Example 3.3.11.

   ```lean
   example {a b : ℤ} (h : a ≡ b [ZMOD 5]) : 2 * a + 3 ≡ 2 * b + 3 [ZMOD 5] := by
     sorry
   ```
7. Let \(m\) and \(n\) be integers, and suppose that \(m \equiv n \mod 4\). Show that
   \(3m-1 \equiv 3n-1 \mod 4\).

   Give two solutions, following the style of the two solutions in
   Example 3.3.11.

   ```lean
   example {m n : ℤ} (h : m ≡ n [ZMOD 4]) : 3 * m - 1 ≡ 3 * n - 1 [ZMOD 4] := by
     sorry
   ```
8. Let \(k\) be an integer, and suppose that \(k\equiv 3 \mod 5\). Show that
   \(4 k + k ^ 3 + 3\equiv 4 \cdot 3 + 3 ^ 3 + 3 \mod 5\).

   Give two solutions, following the style of the two solutions in
   Example 3.3.11.

   ```lean
   example {k : ℤ} (hb : k ≡ 3 [ZMOD 5]) :
       4 * k + k ^ 3 + 3 ≡ 4 * 3 + 3 ^ 3 + 3 [ZMOD 5] := by
     sorry
   ```

### 3.4. Modular arithmetic: calculations

#### 3.4.1. Example

Recall the problem from Example 3.3.11.

> **Problem:**
>
> Let \(a\) and \(b\) be integers, and suppose that \(a\equiv 2 \mod 4\).
> Show that \(a b ^ 2 + a ^ 2 b + 3 a \equiv 2 b ^ 2 + 2 ^ 2 \cdot b + 3 \cdot 2 \mod 4\).

After solving this and the many similar problems in Section 3.3, you
probably feel like you can check the correctness of any statement of this style by sight. And
that’s great! When we have built up enough theory that a particular class of statement can be
checked by sight, we will stop requiring detailed proofs. So for now on we’ll take such statements
for granted.

This is also usually the point at which it’s easy to write a Lean tactic which checks the
correctness of a class of statement. I’ve done exactly this, updating tactic `rel` to cover this
kind of statement. We won’t discuss tactic-writing in this book, but effectively, the tactic
now throws the lemmas `Int.ModEq.add`, `Int.ModEq.neg`, `Int.ModEq.sub`, `Int.ModEq.mul`,
`Int.ModEq.pow`, `Int.ModEq.refl` and the provided hypotheses at the statement until (a) the
goal is solved or (b) none of these lemmas apply any more. And that’s exactly what we’re doing in
our heads when we check the paper statement of the problem.

```lean
example {a b : ℤ} (ha : a ≡ 2 [ZMOD 4]) :
    a * b ^ 2 + a ^ 2 * b + 3 * a ≡ 2 * b ^ 2 + 2 ^ 2 * b + 3 * 2 [ZMOD 4] := by
  rel [ha]
```

#### 3.4.2. Example

From now on, we will solve more interesting modular arithmetic problems, dealing with steps like
Example 3.3.11 in a single line.

> **Problem:**
>
> Let \(a\) and \(b\) be integers, with \(a \equiv 4\mod 5\) and
> \(b \equiv 3\mod 5\). Show that \(ab+b^3+3 \equiv 2\mod 5\).

> **Solution:**
>
> \[\begin{split}a b + b ^ 3 + 3 &\equiv 4 b + b ^ 3 + 3 \mod 5\\
> &\equiv 4 \cdot 3 + 3 ^ 3 + 3 \mod 5\\
> &=2+5 \cdot 8\\
> &\equiv 2\mod 5.\end{split}\]

```lean
example {a b : ℤ} (ha : a ≡ 4 [ZMOD 5]) (hb : b ≡ 3 [ZMOD 5]) :
    a * b + b ^ 3 + 3 ≡ 2 [ZMOD 5] :=
  calc
    a * b + b ^ 3 + 3 ≡ 4 * b + b ^ 3 + 3 [ZMOD 5] := by rel [ha]
    _ ≡ 4 * 3 + 3 ^ 3 + 3 [ZMOD 5] := by rel [hb]
    _ = 2 + 5 * 8 := by numbers
    _ ≡ 2 [ZMOD 5] := by extra
```

#### 3.4.3. Example

> **Problem:**
>
> Show that there exists an integer \(a\), such that \(6a \equiv 4\mod 11\).

> **Solution:**
>
> The integer 8 has this property. Indeed,
>
> \[\begin{split}6 \cdot 8 &= 4 + 4 \cdot 11\\
> &\equiv 4\mod 11.\end{split}\]

```lean
example : ∃ a : ℤ, 6 * a ≡ 4 [ZMOD 11] := by
  use 8
  calc
    (6:ℤ) * 8 = 4 + 4 * 11 := by numbers
    _ ≡ 4 [ZMOD 11] := by extra
```

#### 3.4.4. Example

> **Problem:**
>
> Let \(x\) be an integer. Show that \(x ^ 3 \equiv x\mod 3\).

> **Solution:**
>
> We consider cases according to the residue of \(x\) modulo 3.
>
> **Case 1** (\(x\equiv 0\mod 3\)):
>
> \[\begin{split}x^3 &\equiv 0^3\mod 3\\
> & = 0\\
> &\equiv x\mod 3.\end{split}\]
>
> **Case 2** (\(x\equiv 1\mod 3\)):
>
> \[\begin{split}x^3 &\equiv 1^3\mod 3\\
> & = 1\\
> &\equiv x\mod 3.\end{split}\]
>
> **Case 3** (\(x\equiv 2\mod 3\)):
>
> \[\begin{split}x^3 &\equiv 2^3\mod 3\\
> & = 2 + 3 \cdot 2\\
> &\equiv 2\mod 3\\
> &\equiv x\mod 3.\end{split}\]

```lean
example {x : ℤ} : x ^ 3 ≡ x [ZMOD 3] := by
  mod_cases hx : x % 3
  calc
    x ^ 3 ≡ 0 ^ 3 [ZMOD 3] := by rel [hx]
    _ = 0 := by numbers
    _ ≡ x [ZMOD 3] := by rel [hx]
  calc
    x ^ 3 ≡ 1 ^ 3 [ZMOD 3] := by rel [hx]
    _ = 1 := by numbers
    _ ≡ x [ZMOD 3] := by rel [hx]
  calc
    x ^ 3 ≡ 2 ^ 3 [ZMOD 3] := by rel [hx]
    _ = 2 + 3 * 2 := by numbers
    _ ≡ 2 [ZMOD 3] := by extra
    _ ≡ x [ZMOD 3] := by rel [hx]
```

#### 3.4.5. Exercises

1. Let \(n\) be an integer for which \(n\equiv 1\mod 3\).
   Show that \(n^3+7n\equiv 2\mod 3\).

   ```lean
   example {n : ℤ} (hn : n ≡ 1 [ZMOD 3]) : n ^ 3 + 7 * n ≡ 2 [ZMOD 3] :=
     sorry
   ```
2. Let \(a\) be an integer for which \(a\equiv 3\mod 4\).
   Show that \(a^3+4a^2+2\equiv 1\mod 4\).

   ```lean
   example {a : ℤ} (ha : a ≡ 3 [ZMOD 4]) :
       a ^ 3 + 4 * a ^ 2 + 2 ≡ 1 [ZMOD 4] :=
     sorry
   ```
3. Let \(a\) and \(b\) be integers. Show that \((a + b)^3\equiv a^3+b^3\mod 3\).

   ```lean
   example (a b : ℤ) : (a + b) ^ 3 ≡ a ^ 3 + b ^ 3 [ZMOD 3] :=
     sorry
   ```
4. Show that there exists an integer \(a\), such that \(4a\equiv 1\mod 7\).

   ```lean
   example : ∃ a : ℤ, 4 * a ≡ 1 [ZMOD 7] := by
     sorry
   ```
5. Show that there exists an integer \(k\), such that \(5k\equiv 6\mod 8\).

   ```lean
   example : ∃ k : ℤ, 5 * k ≡ 6 [ZMOD 8] := by
     sorry
   ```
6. Let \(n\) be an integer. Show that \(5n^2+3n+7\equiv 1\mod 2\).

   ```lean
   example (n : ℤ) : 5 * n ^ 2 + 3 * n + 7 ≡ 1 [ZMOD 2] := by
     sorry
   ```
7. Let \(x\) be an integer. Show that \(x^5\equiv x\mod 5\).

   ```lean
   example {x : ℤ} : x ^ 5 ≡ x [ZMOD 5] := by
     sorry
   ```

### 3.5. Bézout’s identity

#### 3.5.1. Example

> **Problem:**
>
> Let \(n\) be an integer and suppose that \(5n\) is a multiple of \(8\). Show that
> \(n\) is also a multiple of \(8\).

> **Solution:**
>
> Since \(8\mid 5n\), there exists an integer \(a\) such that \(5n=8a\). Then
>
> \[\begin{split}n &= -3 (5 n) + 16 n \\
> &= -3 (8 a) + 16 n \\
> & = 8 (-3 a + 2 n),\end{split}\]
>
> so \(8\mid n\).

```lean
example {n : ℤ} (hn : 8 ∣ 5 * n) : 8 ∣ n := by
  obtain ⟨a, ha⟩ := hn
  use -3 * a + 2 * n
  calc
    n = -3 * (5 * n) + 16 * n := by ring
    _ = -3 * (8 * a) + 16 * n := by rw [ha]
    _ = 8 * (-3 * a + 2 * n) := by ring
```

Such a problem will typically have many possible solutions. Here is another solution.

> **Solution:**
>
> Since \(8\mid 5n\), there exists an integer \(a\) such that \(5n=8a\). Then
>
> \[\begin{split}n &= 5 (5 n) - 24 n \\
> &= 5 (8 a) -24 n \\
> & = 8 (5 a - 3 n),\end{split}\]
>
> so \(8\mid n\).

Try typing the variant solution up in Lean.

```lean
example {n : ℤ} (hn : 8 ∣ 5 * n) : 8 ∣ n := by
  sorry
```

#### 3.5.2. Example

> **Problem:**
>
> Show that if for some integer \(n\) we have that \(5\) divides \(3n\), then \(5\)
> also divides \(n\).

> **Solution:**
>
> Since \(5\mid 3n\), there exists an integer \(x\) such that \(3n=5x\). Then
>
> \[\begin{split}n &= 2 (3 n) - 5 n \\
> &= 2 (5 x) - 5 n \\
> & = 5 (2x - n),\end{split}\]
>
> so \(5\mid n\).

```lean
example {n : ℤ} (h1 : 5 ∣ 3 * n) : 5 ∣ n := by
  sorry
```

#### 3.5.3. Example

> **Problem:**
>
> Let \(m\) be an integer which is divisible by 8 and by 5. Show that it is also divisible by
> 40.

> **Solution:**
>
> Since \(8\mid m\), there exists an integer \(a\) such that \(m=8a\).
> Since \(5\mid m\), there exists an integer \(b\) such that \(m=5b\). Then
>
> \[\begin{split}m &= -15 m + 16 m\\
> &= -15 (8 a) + 16 m \\
> &= -15 (8 a) + 16 (5 b) \\
> & = 40 (-3 a + 2 b),\end{split}\]
>
> so \(40\mid m\).

```lean
example {m : ℤ} (h1 : 8 ∣ m) (h2 : 5 ∣ m) : 40 ∣ m := by
  obtain ⟨a, ha⟩ := h1
  obtain ⟨b, hb⟩ := h2
  use -3 * a + 2 * b
  calc
    m = -15 * m + 16 * m := by ring
    _ = -15 * (8 * a) + 16 * m := by rw [ha]
    _ = -15 * (8 * a) + 16 * (5 * b) := by rw [hb]
    _ = 40 * (-3 * a + 2 * b) := by ring
```

#### 3.5.4. Exercises

1. Show that if 6 divides \(11n\), then it divides \(n\).

   ```lean
   example {n : ℤ} (hn : 6 ∣ 11 * n) : 6 ∣ n := by
     sorry
   ```
2. Let \(a\) be an integer and suppose that \(5a\) is a multiple of \(7\). Show that
   \(a\) is also a multiple of \(7\).

   ```lean
   example {a : ℤ} (ha : 7 ∣ 5 * a) : 7 ∣ a := by
     sorry
   ```
3. Suppose that 7 and 9 are both factors of some integer \(n\). Show that 63 is also a factor of
   \(n\).

   ```lean
   example {n : ℤ} (h1 : 7 ∣ n) (h2 : 9 ∣ n) : 63 ∣ n := by
     sorry
   ```
4. Let \(n\) be an integer which is divisible by 5 and by 13. Show that it is also divisible by
   65.

   ```lean
   example {n : ℤ} (h1 : 5 ∣ n) (h2 : 13 ∣ n) : 65 ∣ n := by
     sorry
   ```

---



## 4. Proofs with Structure II {#m2001-4-proofs-with-structure-ii}

> 📄 Source: https://hrmacbeth.github.io/math2001/04_Proofs_with_Structure_II.html

## 4. Proofs with structure, II

In the course of [Chapter 2]](#m2001-proofs-with-structure), we studied the
logical symbols \(\lor\), \(\land\) and \(\exists\), which allow
complicated mathematical statements to be built up from simpler ones. For each
such symbol, we learned its “grammar”: the rule for using it when it appears in a
hypothesis and the rule for using it when it appears in the goal. This grammar is
called [natural deduction](https://en.wikipedia.org/wiki/Natural_deduction).

This chapter finishes the work started in [Chapter 2]](#m2001-proofs-with-structure).
We learn the grammar of the remaining logical symbols: \(\forall\),
\(\to\), and \(\lnot\). We also learn the grammar of two other logical
symbols, \(\leftrightarrow\) and \(\exists!\), which are less fundamental
because they can be defined in terms of the other ones.

### 4.1. “For all” and implication

#### 4.1.1. Example

> **Problem:**
>
> Let \(a\) be a real number and suppose that for all real numbers \(x\),
> it is true that \(a\le x^2-2x\). Show that \(a\le -1\).

[

![_images/04_logic_01_parabola.png](https://hrmacbeth.github.io/math2001/_images/04_logic_01_parabola.png)

](https://hrmacbeth.github.io/math2001/_images/04_logic_01_parabola.png)


Fig. 4.1 The parabola \(y= x^2-2x\).

Specifying that a formula or predicate is meant to be true for all values of a variable \(x\),
like we did with \(a\le x^2-2x\) in the above problem, is called *universally quantifying* over
the variable \(x\). It is expressed symbolically as `∀`.

To use a hypothesis with a universal quantifier, you may want to “specialize” its use to
one particular variable. For example, in the solution below, we use the special case of the
hypothesis in which \(x\) is set to 1.

> **Solution:**
>
> \[\begin{split}a &\le 1 ^ 2 - 2 \cdot 1 \\
> &= -1.\end{split}\]

In Lean, perform this specialization using the `apply` tactic.

```lean
example {a : ℝ} (h : ∀ x, a ≤ x ^ 2 - 2 * x) : a ≤ -1 :=
  calc
    a ≤ 1 ^ 2 - 2 * 1 := by apply h
    _ = -1 := by numbers
```

#### 4.1.2. Example

> **Problem:**
>
> Let \(n\) be a natural number which is a factor of every natural number \(m\). Show that
> \(n=1\).

> **Solution:**
>
> Since \(n\) is a factor of every natural number, it is a factor of \(1\). Also notice
> that \(1\) is positive. So we can invoke the size bounds on factors discussed in
> [Example 3.2.7]](#m2001-dvd-bd-1) and [Example 3.2.8]](#m2001-dvd-bd-2): \(n\le 1\) and
> \(1 \le n\). Therefore \(n=1\).

For the Lean proof, we recall that the bound from [Example 3.2.7]](#m2001-dvd-bd-1) is in Lean as
`Nat.le_of_dvd`, and the bound from [Example 3.2.8]](#m2001-dvd-bd-2) is in Lean as
`Nat.pos_of_dvd_of_pos`.

```lean
example {n : ℕ} (hn : ∀ m, n ∣ m) : n = 1 := by
  have h1 : n ∣ 1 := by apply hn
  have h2 : 0 < 1 := by numbers
  apply le_antisymm
  · apply Nat.le_of_dvd h2 h1
  · apply Nat.pos_of_dvd_of_pos h1 h2
```

#### 4.1.3. Example

> **Problem:**
>
> Let \(a\) and \(b\) be real numbers and suppose that every real number \(x\) is
> either at least \(a\) or at most \(b\). Show that \(a \le b\).

> **Solution:**
>
> Consider the real number \(\frac {a+b}{2}\). It is either at least \(a\) or at most
> \(b\).
>
> **Case 1:** \(\frac {a+b}{2} \geq a\). Then
>
> \[\begin{split}b &= 2 \left(\frac{a + b} {2}\right) - a\\
> &\geq 2 \cdot a - a \\
> & = a.\end{split}\]
>
> **Case 2:** \(\frac {a+b}{2} \leq b\). Then
>
> \[\begin{split}a &= 2 \left(\frac{a + b} {2}\right) - b\\
> &\leq 2 \cdot b - b \\
> & = b.\end{split}\]

```lean
example {a b : ℝ} (h : ∀ x, x ≥ a ∨ x ≤ b) : a ≤ b := by
  sorry
```

#### 4.1.4. Example

> **Problem:**
>
> Let \(a\) be real number whose square is at most 2, and which is greater than or equal to
> any real number whose square is at most 2. 1 Let \(b\) be another real number with the same
> two properties. Prove that \(a=b\).

Consider the hypothesis in this problem that

> \(a\) is greater than or equal to any real number whose square is at most 2.

Implicitly, there is a universal quantification (“any real number”), and also an *implication*,
so a more pedantic version of this hypothesis would be

> for all real numbers \(y\), if \(y^2\le 2\) then \(y\le a\).

We can specialize such a hypothesis to any particular choice of \(y\) for which the
*antecedent* \(y^2\le 2\) is true.

> **Solution:**
>
> Since \(a^2\le 2\), and \(b\) is greater than or equal to any real number whose square is
> at most 2, \(a \le b\).
>
> Since \(b^2\le 2\), and \(a\) is greater than or equal to any real number whose square is
> at most 2, \(b \le a\).
>
> Therefore \(a=b\).

In Lean, an implication is expressed using the symbol `→`. The tactic `apply` works for
hypotheses featuring implications. In the proof below, the goal state before `apply hb2` is

```
a b: ℝ
ha1 : a ^ 2 ≤ 2
hb1 : b ^ 2 ≤ 2
ha2 : ∀ (y : ℝ), y ^ 2 ≤ 2 → y ≤ a
hb2 : ∀ (y : ℝ), y ^ 2 ≤ 2 → y ≤ b
⊢ a ≤ b
```

and the goal state after it is

```
a b : ℝ
ha1 : a ^ 2 ≤ 2
hb1 : b ^ 2 ≤ 2
ha2 : ∀ (y : ℝ), y ^ 2 ≤ 2 → y ≤ a
hb2 : ∀ (y : ℝ), y ^ 2 ≤ 2 → y ≤ b
⊢ a ^ 2 ≤ 2
```

The hypothesis `∀ (y : ℝ), y ^ 2 ≤ 2 → y ≤ b` has been applied to the goal `a ≤ b`, leaving
the hopefully-easier goal `a ^ 2 ≤ 2`: proving the antecedent of the implication.

Fill in the second part of the proof.

```lean
example {a b : ℝ} (ha1 : a ^ 2 ≤ 2) (hb1 : b ^ 2 ≤ 2) (ha2 : ∀ y, y ^ 2 ≤ 2 → y ≤ a)
    (hb2 : ∀ y, y ^ 2 ≤ 2 → y ≤ b) :
    a = b := by
  apply le_antisymm
  · apply hb2
    apply ha1
  · sorry
```

#### 4.1.5. Example

> **Problem:**
>
> Show that there exists a real number \(b\) such that for every real number \(x\),
> it is true that \(b \le x^2-2x\).

Notice that in this problem a universally quantified statement appears in the goal:
“for every real number \(x\), ….”

> **Solution:**
>
> We show that -1 has this property. Indeed, let \(x\) be a real number; then
>
> \[\begin{split}-1 &\le -1 + (x-1)^2 \\
> &=x^2-2x.\end{split}\]

We solve the problem by formally introducing a particular, arbitrary real number \(x\) (“let
\(x\) be a real number”) and proving the desired statement for that \(x\). In Lean this
argument is performed by the tactic `intro`. Before the use of this tactic, the goal state is

```
⊢ ∀ (x : ℝ), -1 ≤ x ^ 2 - 2 * x
```

After the use of this tactic, the goal state is

```
x : ℝ
⊢ -1 ≤ x ^ 2 - 2 * x
```

```lean
example : ∃ b : ℝ, ∀ x : ℝ, b ≤ x ^ 2 - 2 * x := by
  use -1
  intro x
  calc
    -1 ≤ -1 + (x - 1) ^ 2 := by extra
    _ = x ^ 2 - 2 * x := by ring
```

#### 4.1.6. Example

> **Problem:**
>
> Show that there exists a real number \(c\) such that for all real numbers \(x\) and
> \(y\), if \(x^2+y^2\le 4\), then \(x+y\geq c\).

Here the goal contains universal quantification over \(x\) and \(y\), as well as an
implication: “if \(x^2+y^2\le 4\), then ….” In solving the problem, we formally introduce
the variables \(x\) and \(y\) and also the hypothesis \(x^2+y^2\le 4\) which they are
assumed to satisfy:

> **Solution:**
>
> We show that -3 has this property. Indeed, let \(x\) and \(y\) be real numbers and
> suppose that \(x^2+y^2\le 4\). Then
>
> \[\begin{split}(x + y) ^ 2 &\le (x + y) ^ 2 + (x - y) ^ 2 \\
> &= 2 (x ^ 2 + y ^ 2) \\
> &\le 2 \cdot 4 \\
> &\le 3 ^ 2.\end{split}\]
>
> So \(x + y \geq -3\) (and also \(x + y \leq 3\)).

In Lean, the introduction of both the variables \(x\) and \(y\) and the hypothesis
\(x^2+y^2\le 4\) is done using the `intro` tactic. To deduce from
\((x + y) ^ 2 \le 3 ^ 2\) that \(-3 ≤ x + y\) and \(x + y ≤ 3\), you will need to use
the lemma

```lean
lemma abs_le_of_sq_le_sq' (h : x ^ 2 ≤ y ^ 2) (hy : 0 ≤ y) : -y ≤ x ∧ x ≤ y :=
```

which we previously saw in [Example 2.4.2]](#m2001-abs-le-of-sq-le-sq).

```lean
example : ∃ c : ℝ, ∀ x y, x ^ 2 + y ^ 2 ≤ 4 → x + y ≥ c := by
  sorry
```

#### 4.1.7. Example

> **Definition:**
>
> A property is true *for all sufficiently large integers* \(n\), if there exists an integer
> \(N\), such that that property is true for all integers \(n\geq N\).

Likewise for rational numbers, real numbers, ….

> **Problem:**
>
> Show that for all sufficiently large integers \(n\), it is true that
> \(n ^ 3 ≥ 4n ^ 2 + 7\).

In the solution that follows, “For all \(n\geq 5\)” is a shorter way of expressing, “Let
\(n\) be an integer and suppose that \(n\geq 5\)”.

> **Solution:**
>
> For all \(n\geq 5\),
>
> \[\begin{split}n ^ 3 &= n \cdot n ^ 2 \\
> &\geq 5 n ^ 2 \\
> & = 4 n ^ 2 + n ^ 2\\
> & \geq 4 n ^ 2 + 5 ^ 2 \\
> & = 4 n ^ 2 + 7 + 18 \\
> & ≥ 4 n ^ 2 + 7.\end{split}\]

I have provided the notation `forall_sufficiently_large` to express this and similar problems in
Lean.

```lean
example : forall_sufficiently_large n : ℤ, n ^ 3 ≥ 4 * n ^ 2 + 7 := by
  dsimp
  use 5
  intro n hn
  calc
    n ^ 3 = n * n ^ 2 := by ring
    _ ≥ 5 * n ^ 2 := by rel [hn]
    _ = 4 * n ^ 2 + n ^ 2 := by ring
    _ ≥ 4 * n ^ 2 + 5 ^ 2 := by rel [hn]
    _ = 4 * n ^ 2 + 7 + 18 := by ring
    _ ≥ 4 * n ^ 2 + 7 := by extra
```

#### 4.1.8. Example

> **Definition:**
>
> A natural number \(p\) is *prime*, if it is at least \(2\), and the only factors
> of \(p\) are \(1\) and \(p\).

```lean
def Prime (p : ℕ) : Prop :=
  2 ≤ p ∧ ∀ m : ℕ, m ∣ p → m = 1 ∨ m = p
```

> **Problem:**
>
> Show that 2 is prime.

> **Solution:**
>
> Clearly \(2 \le 2\). Let \(m\) be a factor of \(2\). Since \(2\) is positive,
> by the size bounds on factors discussed in [Example 3.2.7]](#m2001-dvd-bd-1) and
> [Example 3.2.8]](#m2001-dvd-bd-2), we have that \(m \le 2\) and \(1 \le m\). The only
> natural numbers \(m\) satisfying \(m \le 2\) and \(1 \le m\) are \(1\) and
> \(2\), so as required \(m=1\) or \(m=2\).

One new technique used in this solution is to observe from numeric bounds on a natural number (like
\(m \le 2\) and \(1 \le m\) here) that there are finitely many possibilities (here,
\(m=1\) or \(m=2\).) In Lean, we use the tactic `interval_cases` for this kind of
argument. It also works for integers, but it doesn’t work for rational numbers or real numbers –
why?

```lean
example : Prime 2 := by
  constructor
  · numbers -- show `2 ≤ 2`
  intro m hmp
  have hp : 0 < 2 := by numbers
  have hmp_le : m ≤ 2 := Nat.le_of_dvd hp hmp
  have h1m : 1 ≤ m := Nat.pos_of_dvd_of_pos hmp hp
  interval_cases m
  · left
    numbers -- show `1 = 1`
  · right
    numbers -- show `2 = 2`
```

This lemma is available for future use in Lean under the name `prime_two`.

#### 4.1.9. Example

You might be wondering how to prove that a natural number \(p\) is *not* prime. The idea is to
show that it can be expressed as a nontrivial product.

> **Problem:**
>
> Show that 6 is not prime.

> **Solution:**
>
> \(6=2\cdot 3\), so \(2 \mid 6\). But \(2 \ne 1\) and \(2 \ne 6\).

We will prove this test carefully in Example 4.5.7. For now, feel free to
use it. In Lean the lemma name is `not_prime`.

```lean
example : ¬ Prime 6 := by
  apply not_prime 2 3
  · numbers -- show `2 ≠ 1`
  · numbers -- show `2 ≠ 6`
  · numbers -- show `6 = 2 * 3`
```

#### 4.1.10. Exercises

1. Let \(a\) be a rational number and suppose that for all rational numbers \(b\),
   \(a\ge -3+4b-b^2\). Show that \(a\ge 1\).

   ```lean
   example {a : ℚ} (h : ∀ b : ℚ, a ≥ -3 + 4 * b - b ^ 2) : a ≥ 1 :=
     sorry
   ```
2. Let \(n\) be an integer and suppose that every integer \(m\) between 1 and 5 is a
   factor of \(n\). Show that 15 is a factor of \(n\). (You may need to review
   [Section 3.5]](#m2001-bezout).)

   ```lean
   example {n : ℤ} (hn : ∀ m, 1 ≤ m → m ≤ 5 → m ∣ n) : 15 ∣ n := by
     sorry
   ```
3. Show that there exists a natural number \(n\) such that every natural number \(m\) is at
   least \(n\).

   ```lean
   example : ∃ n : ℕ, ∀ m : ℕ, n ≤ m := by
     sorry
   ```
4. Show that there exists a real number \(a\), such that for all real numbers \(b\), there
   exists a real number \(c\), such that \(a + b < c\).

   ```lean
   example : ∃ a : ℝ, ∀ b : ℝ, ∃ c : ℝ, a + b < c := by
     sorry
   ```
5. Show that for all sufficiently large real numbers \(x\),
   \(x ^ 3 + 3 x ≥ 7 x ^ 2 + 12\).

   ```lean
   example : forall_sufficiently_large x : ℝ, x ^ 3 + 3 * x ≥ 7 * x ^ 2 + 12 := by
     sorry
   ```
6. Show that 45 is not prime.

   You may use the Lean lemma `not_prime`, as in Example 4.1.9.

   ```lean
   example : ¬(Prime 45) := by
     sorry
   ```

Footnotes

1
:   That is, \(a\) is *maximal in the set of real numbers whose square is at most 2.*

### 4.2. “If and only if”

#### 4.2.1. Example

> **Problem:**
>
> Let \(a\) be a rational number. Show that \(3a+1\le 7\) if and only if \(a\le 2\).

The phrase “if and only if” means exactly what it sounds like. In this problem we have to show
(1) if \(3a+1\le 7\) then \(a\le 2\) and (2) if \(a\le 2\) then \(3a+1\le 7\).

> **Solution:**
>
> First, suppose that \(3a+1\le 7\). Then
>
> \[\begin{split}a&=\frac{(3a+1)-1}{3}\\
> &\le \frac{7-1}{3}\\
> &=2.\end{split}\]
>
> Conversely, suppose that \(a\le 2\). Then
>
> \[\begin{split}3a+1&\le 3\cdot 2+1\\
> &=7.\end{split}\]

In handwritten work, it is quite common to annotate the two directions with the symbols
\(\Rightarrow\) and \(\Leftarrow\) respectively:

> **Solution:**
>
> \(\Rightarrow\) Suppose that \(3a+1\le 7\). Then …
>
> \(\Leftarrow\) Suppose that \(a\le 2\). Then …

This is recommended for homework, tests, writing on the blackboard, etc. In more formal writing
(such as this book) we omit such symbols and instead use words like “First” and
“Conversely” to signal the different parts of the proof.

In Lean, an “if and only if” is stated with the bi-implication symbol `↔`. Since, under the hood,
an “if and only if” is an “and” statement, we use the same tactic as for “and” goals:
`constructor`.

```lean
example {a : ℚ} : 3 * a + 1 ≤ 7 ↔ a ≤ 2 := by
  constructor
  · intro h
    calc a = ((3 * a + 1) - 1) / 3 := by ring
      _ ≤ (7 - 1) / 3 := by rel [h]
      _ = 2 := by numbers
  · intro h
    calc 3 * a + 1 ≤ 3 * 2 + 1 := by rel [h]
      _ = 7 := by numbers
```

#### 4.2.2. Example

Let’s modify [Example 3.5.1]](#m2001-bezout-prob1) to be an if-and-only-if problem. Now there are two
things to prove, one of which we did before.

> **Problem:**
>
> Let \(n\) be an integer. Show that \(5n\) is a multiple of \(8\) if and only if
> \(n\) is.

> **Solution:**
>
> Suppose that \(8\mid 5n\). Then there exists an integer \(a\) such that \(5n=8a\).
> So
>
> \[\begin{split}n &= -3 (5 n) + 16 n \\
> &= -3 (8 a) + 16 n \\
> & = 8 (-3 a + 2 n),\end{split}\]
>
> so \(8\mid n\).
>
> Conversely, suppose that \(8\mid n\). Then there exists an integer \(a\) such that
> \(n=8a\). So
>
> \[\begin{split}5n &= 5(8a) \\
> &= 8(5a),\end{split}\]
>
> so \(8\mid 5n\).

```lean
example {n : ℤ} : 8 ∣ 5 * n ↔ 8 ∣ n := by
  constructor
  · intro hn
    obtain ⟨a, ha⟩ := hn
    use -3 * a + 2 * n
    calc
      n = -3 * (5 * n) + 16 * n := by ring
      _ = -3 * (8 * a) + 16 * n := by rw [ha]
      _ = 8 * (-3 * a + 2 * n) := by ring
  · intro hn
    obtain ⟨a, ha⟩ := hn
    use 5 * a
    calc 5 * n = 5 * (8 * a) := by rw [ha]
      _ = 8 * (5 * a) := by ring
```

#### 4.2.3. Example

> **Problem:**
>
> Show that an integer \(n\) is odd, if and only if it is congruent to 1 modulo 2.

> **Solution:**
>
> First, suppose that \(n\) is odd. Then there exists an integer \(k\) such that
> \(n=2k+1\). Therefore \(n-1=2k\), so \(n-1\) is divisible by 2, so
> \(n\equiv 1\mod 2\).
>
> Conversely, suppose that \(n\equiv 1\mod 2\). Then \(2\mid n -1\), so there exists an
> integer \(k\) such \(n-1=2k\). Thus \(n=2k+1\) and so \(n\) is odd.

We name this example `Int.odd_iff_modEq` for future use.

```lean
theorem odd_iff_modEq (n : ℤ) : Odd n ↔ n ≡ 1 [ZMOD 2] := by
  constructor
  · intro h
    obtain ⟨k, hk⟩ := h
    dsimp [Int.ModEq]
    dsimp [(· ∣ ·)]
    use k
    addarith [hk]
  · sorry
```

#### 4.2.4. Example

Now do the same to characterise evenness.

> **Problem:**
>
> Show that an integer \(n\) is even, if and only if it is congruent to 0 modulo 2.

```lean
theorem even_iff_modEq (n : ℤ) : Even n ↔ n ≡ 0 [ZMOD 2] := by
  constructor
  · intro h
    obtain ⟨k, hk⟩ := h
    dsimp [Int.ModEq]
    dsimp [(· ∣ ·)]
    use k
    addarith [hk]
  · sorry
```

#### 4.2.5. Example

The high school concept of “solving” equations represents an “if and only if” problem: to solve an
equation, you state a list of numbers and prove that they satisfy the equation and no other numbers
do.

> **Problem:**
>
> Let \(x\) be a real number. Show that \(x ^ 2 + x - 6 = 0\), if and only if \(x = -3\)
> or \(x = 2\).

> **Solution:**
>
> First, suppose that \(x ^ 2 + x - 6 = 0\). Then
>
> \[\begin{split}(x+3)(x-2)&=x ^ 2 + x - 6\\
> &=0,\end{split}\]
>
> so either \(x+3=0\) or \(x-2=0\). If the former, \(x=-3\); if the latter,
> \(x=2\).
>
> Conversely, if \(x=-3\) then
>
> \[\begin{split}x ^ 2 + x - 6&=(-3)^2+(-3)-6\\
> &=0,\end{split}\]
>
> and if \(x=2\) then
>
> \[\begin{split}x ^ 2 + x - 6&=2^2+2-6\\
> &=0.\end{split}\]

```lean
example {x : ℝ} : x ^ 2 + x - 6 = 0 ↔ x = -3 ∨ x = 2 := by
  sorry
```

#### 4.2.6. Example

> **Problem:**
>
> Let \(a\) be an integer. Show that \(a^2-5a+5 \le -1\), if and only if \(a\) is 2
> or 3.

> **Solution:**
>
> First, suppose that \(a^2-5a+5 \le -1\). Then
>
> \[\begin{split}(2 a - 5) ^ 2&= 4 (a ^ 2 - 5 a + 5) + 5 \\
> &\le 4 \cdot -1 + 5 \\
> &= 1^2,\end{split}\]
>
> so \(-1\le 2a-5\le 1\). Therefore \(2 \cdot 2 \le 2a\), so \(2 \le a\), and similarly
> \(2a ≤ 2 \cdot 3\), so \(a \le 3\). Since \(2\le a\le 3\), \(a\) is 2 or 3.
>
> Conversely, if \(a=2\) then
>
> \[\begin{split}a^2-5a+5 &= 2^2-5\cdot 2+5 \\
> &\le -1,\end{split}\]
>
> and if \(a=3\) then
>
> \[\begin{split}a^2-5a+5 &= 3^2-5\cdot 3+5 \\
> &\le -1.\end{split}\]

```lean
example {a : ℤ} : a ^ 2 - 5 * a + 5 ≤ -1 ↔ a = 2 ∨ a = 3 := by
  sorry
```

#### 4.2.7. Example

Some library lemmas have the form of an “if and only if”. This is convenient because they take
the place of two ordinary lemmas, one for each direction.

> **Problem:**
>
> Let \(n\) be an integer and suppose \(n ^ 2 - 10 n + 24 = 0\). Show that \(n\) is
> even.

> **Solution:**
>
> We have
>
> \[\begin{split}(n-4)(n-6)&= n^2-10n+24\\
> &= 0,\end{split}\]
>
> so either \(n-4=0\) or \(n-6=0\). If the former, then \(n=2\cdot 2\) so \(n\) is
> even; if the latter, then \(n=2\cdot 3\) so \(n\) is even.

In this problem we need to transform the fact \((n-4)(n-6)=0\) into the fact that
“\(n-4=0\) or \(n-6=0\).” Previously (like in [Example 2.3.4]](#m2001-solve-quadratic)), we
would have done this in Lean by inserting this fact directly into the lemma

```lean
theorem eq_zero_or_eq_zero_of_mul_eq_zero {a b : ℤ} (h : a * b = 0) : a = 0 ∨ b = 0 :=
```

resulting in a proof setup like this:

```lean
example {n : ℤ} (hn : n ^ 2 - 10 * n + 24 = 0) : Even n := by
  have hn1 :=
    calc (n - 4) * (n - 6) = n ^ 2 - 10 * n + 24 := by ring
      _ = 0 := hn
  have hn2 := eq_zero_or_eq_zero_of_mul_eq_zero hn1
  sorry
```

(Exercise: finish off the proof.)

But there is also a library lemma in `↔` form, which combines `eq_zero_or_eq_zero_of_mul_eq_zero`
with the *converse* (other direction) of that statement:

```lean
theorem mul_eq_zero {a b : ℤ} : a * b = 0 ↔ a = 0 ∨ b = 0 :=
```

We can use an `↔` lemma in Lean with the `rw` tactic; it converts a hypothesis (or the goal) in
the form of the left-hand side of the `↔` to one with the form of the right-hand side.

```lean
example {n : ℤ} (hn : n ^ 2 - 10 * n + 24 = 0) : Even n := by
  have hn1 :=
    calc (n - 4) * (n - 6) = n ^ 2 - 10 * n + 24 := by ring
      _ = 0 := hn
  rw [mul_eq_zero] at hn1 -- `hn1 : n - 4 = 0 ∨ n - 6 = 0`
  sorry
```

#### 4.2.8. Example

Above, in Example 4.2.3, we proved that an integer is odd if and only if it
is congruent to 1 modulo 2, recording this under the name `Int.odd_iff_modEq`. This is now also
a convenient “if and only if” library lemma which we can use to solve problems about parity using
modulo arithmetic. As an example, let’s re-do the problem from
[Example 3.1.5]](#m2001-typical-parity).

> **Problem:**
>
> Prove that if the integers \(x\) and \(y\) are odd, then \(x+y+1\) is odd.

> **Solution:**
>
> We will prove that if \(x\equiv 1 \mod 2\) and \(y\equiv 1 \mod 2\) then
> \(x+y+1\equiv 1 \mod 2\). Indeed,
>
> \[\begin{split}x + y + 1 &\equiv 1 + 1 + 1 \mod 2\\
> &= 2 \cdot 1 + 1\\
> &\equiv 1\mod 2.\end{split}\]

```lean
example {x y : ℤ} (hx : Odd x) (hy : Odd y) : Odd (x + y + 1) := by
  rw [Int.odd_iff_modEq] at *
  calc x + y + 1 ≡ 1 + 1 + 1 [ZMOD 2] := by rel [hx, hy]
    _ = 2 * 1 + 1 := by ring
    _ ≡ 1 [ZMOD 2] := by extra
```

#### 4.2.9. Example

Another way we can use the characterization of parity in terms of modular arithmetic is to
prove the theorem from [Example 3.1.9]](#m2001-even-or-odd) whose proof we skipped.

> **Theorem:**
>
> Every integer is either even or odd.

> **Proof:**
>
> Let \(n\) be an integer. We do a case division according to the residue of \(n\)
> modulo 2.
>
> If \(n\equiv 0\mod 2\), then \(n\) is even, and we are done.
>
> If \(n\equiv 0\mod 2\), then \(n\) is odd, and we are done.

Write this in Lean using the lemmas `Int.odd_iff_modEq` and `Int.even_iff_modEq` from
Example 4.2.3 and Example 4.2.4, together with the
tactic `mod_cases`. I have written the beginning.

```lean
example (n : ℤ) : Even n ∨ Odd n := by
  mod_cases hn : n % 2
  · left
    rw [Int.even_iff_modEq]
    apply hn
  · sorry
```

#### 4.2.10. Exercises

1. Let \(x\) be a real number. Show that \(2x-1=11\) if and only if \(x=6\).

   ```lean
   example {x : ℝ} : 2 * x - 1 = 11 ↔ x = 6 := by
     sorry
   ```
2. Let \(n\) be an integer. Show that 63 is a factor of \(n\) if and only if both 7 and 9
   are.

   ```lean
   example {n : ℤ} : 63 ∣ n ↔ 7 ∣ n ∧ 9 ∣ n := by
     sorry
   ```
3. Let \(a\) and \(n\) be integers. Show that \(a\) is a multiple of \(n\) if and
   only if \(a \equiv 0 \mod n\).

   ```lean
   theorem dvd_iff_modEq {a n : ℤ} : n ∣ a ↔ a ≡ 0 [ZMOD n] := by
     sorry
   ```
4. Let \(a\) and \(b\) be integers and suppose that \(a \mid b\). Show that
   \(a \mid 2b^3-b^2+3b\).

   Note that this appeared already as an exercise in [Section 3.2]](#m2001-divisibility). But now,
   using the lemma `Int.dvd_iff_modEq` proved in the previous exercise, this kind of problem is
   much easier.

   ```lean
   example {a b : ℤ} (hab : a ∣ b) : a ∣ 2 * b ^ 3 - b ^ 2 + 3 * b := by
     sorry
   ```
5. Let \(k\) be a natural number. Show that \(k^2 \le 6\), if and only if \(k\) is 0, 1
   or 2.

   ```lean
   example {k : ℕ} : k ^ 2 ≤ 6 ↔ k = 0 ∨ k = 1 ∨ k = 2 := by
     sorry
   ```

### 4.3. “There exists a unique”

#### 4.3.1. Example

> **Problem:**
>
> Show that there exists a unique real number \(a\), such that \(3a+1=7\).

> **Solution:**
>
> We will show that 2 is the unique real number with this property.
>
> First, we show that 2 has this property. Indeed, \(3\cdot 2+1=7\).
>
> Now, let \(y\) be a real number for which \(3y+1=7\). Then
>
> \[\begin{split}y &= \frac{(3 y + 1) - 1} {3}\\
> &= \frac{7 - 1}{ 3}\\
> &= 2.\end{split}\]

```lean
example : ∃! a : ℝ, 3 * a + 1 = 7 := by
  use 2
  dsimp
  constructor
  · numbers
  intro y hy
  calc
    y = (3 * y + 1 - 1) / 3 := by ring
    _ = (7 - 1) / 3 := by rw [hy]
    _ = 2 := by numbers
```

#### 4.3.2. Example

> **Problem:**
>
> Show that there exists a unique rational number \(x\), such that for every rational number
> \(a\) between 1 and 3, \((a-x)^2\le 1\).

> **Solution:**
>
> We will show that 2 is the unique rational number with this property.
>
> Firstly, if \(a\) is a rational number between 1 and 3, then \(-1 \le a-2 \le 1\), so by
> [Example 2.1.7]](#m2001-prove-sq-le-sq),
>
> \[\begin{split}(a-2)^2 &\le 1 ^ 2\\
> &=1.\end{split}\]
>
> Now, let \(y\) be a rational number for which, for every rational number
> \(a\) between 1 and 3, \((a-y)^2\le 1\).
>
> Since 1 is between 1 and 3, \((1-y)^2\le 1\), and since 3 is between 1 and 3,
> \((3-y)^2\le 1\).
>
> So
>
> \[\begin{split}(y - 2) ^ 2 &= \frac{(1 - y) ^ 2 + (3 - y) ^ 2 - 2}{ 2}\\
> &≤ \frac{1 + 1 - 2}{2} \\
> & = 0.\end{split}\]
>
> Also \((y - 2) ^ 2\geq 0\), since squares are positive. Thus
> \((y - 2) ^ 2= 0\), so \(y - 2= 0\), and so \(y = 2\).

In Lean, the result of [Example 2.1.7]](#m2001-prove-sq-le-sq) is available as the lemma `sq_le_sq'`.

```lean
example : ∃! x : ℚ, ∀ a, a ≥ 1 → a ≤ 3 → (a - x) ^ 2 ≤ 1 := by
  sorry
```

#### 4.3.3. Example

> **Problem:**
>
> Let \(x\) be a rational number, and suppose that there exists a unique rational number
> \(a\) such that \(a^2=x\). Show that \(x=0\).

More colloquially: the only rational number with a unique square root is 0.

> **Solution:**
>
> We first show that \(-a=a\). Indeed,
>
> \[\begin{split}(-a)^2&=a^2\\
> &=x,\end{split}\]
>
> and since \(a\) is the unique rational number such that \(a^2=x\), this means that
> \(-a=a\).
>
> It follows that
>
> \[\begin{split}a &= \frac{a - (-a)}{ 2}\\
> &=\frac{a-a}{2}\\
> & = 0.\end{split}\]
>
> So \(x=0\) also:
>
> \[\begin{split}x &= a ^ 2 \\
> &= 0 ^ 2\\
> & = 0.\end{split}\]

```lean
example {x : ℚ} (hx : ∃! a : ℚ, a ^ 2 = x) : x = 0 := by
  obtain ⟨a, ha1, ha2⟩ := hx
  have h1 : -a = a
  · apply ha2
    calc
      (-a) ^ 2 = a ^ 2 := by ring
      _ = x := ha1
  have h2 :=
    calc
      a = (a - -a) / 2 := by ring
      _ = (a - a) / 2 := by rw [h1]
      _ = 0 := by ring
  calc
    x = a ^ 2 := by rw [ha1]
    _ = 0 ^ 2 := by rw [h2]
    _ = 0 := by ring
```

#### 4.3.4. Example

The following is an important theorem about the integers, which we will prove later in the book, in
the exercises to [Section 6.6](https://hrmacbeth.github.io/math2001/06_Induction.html#constructive-division-algorithm).

> **Theorem (the Division Algorithm):**
>
> Let \(a\) and \(b\) be integers, with \(b\) positive. Then there exists a unique
> integer \(r\) between 0 (inclusive) and \(b\) (exclusive), such that
> \(a\equiv r\mod b\).

This lemma is what allows us to do case divisions according to congruence class modulo \(b\)
(the Lean tactic `mod_cases`). But it’s actually a bit more powerful, since the “uniqueness” part
of the statement provides extra information.

In the Lean library it is available in the following form:

```lean
lemma Int.existsUnique_modEq_lt (a b : ℤ) (h : 0 < b) :
  ∃! r : ℤ, 0 ≤ r ∧ r < b ∧ a ≡ r [ZMOD b] :=
```

To understand this theorem better, let’s prove just one of the infinitely many cases of this
theorem.

> **Problem:**
>
> Show that there exists a unique integer \(r\), such that \(0\le r < 5\) and
> \(14\equiv r\mod 5\).

> **Solution:**
>
> We will show that the unique integer with this property is 4.
>
> First, we show that 4 has this property. It is true that \(0\le 4 < 5\), and since
> \(14 - 4 = 5 \cdot 2\) it is true that \(14\equiv r\mod 5\).
>
> Now, let \(r\) be an integer for which \(0\le r < 5\) and \(14\equiv r\mod 5\). Then
> there exists an integer \(q\) such that \(14-r=5q\).
>
> We have,
>
> \[\begin{split}5& \cdot 1 < 14 - r \\
> & = 5q,\end{split}\]
>
> so since \(5\) is positive, \(1<q\). Similarly, we have
>
> \[\begin{split}5 q &= 14 - r \\
> & < 5 \cdot 3\end{split}\]
>
> and so since \(5\) is positive, \(q<3\).
>
> Therefore \(q\) must be 2, the only integer
> strictly between 1 and 3. So \(r=14-5\cdot 2=4\).

```lean
example : ∃! r : ℤ, 0 ≤ r ∧ r < 5 ∧ 14 ≡ r [ZMOD 5] := by
  use 4
  dsimp
  constructor
  · constructor
    · numbers
    constructor
    · numbers
    use 2
    numbers
  intro r hr
  obtain ⟨hr1, hr2, q, hr3⟩ := hr
  have :=
    calc
      5 * 1 < 14 - r := by addarith [hr2]
      _ = 5 * q := by rw [hr3]
  cancel 5 at this
  have :=
    calc
      5 * q = 14 - r := by rw [hr3]
      _ < 5 * 3 := by addarith [hr1]
  cancel 5 at this
  interval_cases q
  addarith [hr3]
```

#### 4.3.5. Exercises

1. Show that there exists a unique rational number \(x\), such that \(4x-3=9\).

   ```lean
   example : ∃! x : ℚ, 4 * x - 3 = 9 := by
     sorry
   ```
2. Show that there exists a unique natural number \(n\), such that for all natural numbers
   \(a\), we have \(n\le a\).

   ```lean
   example : ∃! n : ℕ, ∀ a, n ≤ a := by
     sorry
   ```
3. Show that there exists a unique integer \(r\), such that \(0\le r < 3\) and
   \(11\equiv r\mod 3\).

   ```lean
   example : ∃! r : ℤ, 0 ≤ r ∧ r < 3 ∧ 11 ≡ r [ZMOD 3] := by
     sorry
   ```

### 4.4. Contradictory hypotheses

#### 4.4.1. Example

Sometimes, we encounter a situation with two contradictory hypotheses. At this point, there is no
need to prove anything more. Two contradictory hypotheses mean that the situation we had
hypothesized can’t actually happen.

It is quite common to encounter this when giving a proof by cases. You might reduce the problem to
some list of cases, prove your goal in some of those cases, and prove that the other cases are
impossible.

Here is an example of this kind of reasoning. I have written the solution very pedantically, to
make the point clearer.

> **Lemma:**
>
> Let \(x\) and \(y\) be real numbers, and suppose that \(0<xy\) and \(0 \le x\).
> Show that \(0<y\).

We have used this fact many times before – it is one of the facts which underlie the `cancel`
tactic.

> **Proof:**
>
> We consider two cases, depending on whether or not \(y\) is positive.
>
> **Case 1** (\(y \le 0\)): Since \(0 \le x\), we have that
>
> \[\begin{split}0 &= x \cdot 0\\
> &\geq xy,\end{split}\]
>
> and so it is false that \(0<xy\). This contradicts the hypothesis that \(0< xy\), so this
> case can’t happen.
>
> **Case 2** (\(0 < y\)): This is what we needed to prove, so we are done.

In Lean, the tactic `contradiction` concludes a (part of a) proof by pointing out two
contradictory hypotheses. In the Lean translation of this example, notice that right before it’s
used, the goal state is

```
y x : ℝ
h : 0 < x * y
hx : 0 ≤ x
hneg : y ≤ 0
this : ¬0 < x * y
⊢ 0 < y
```

which contains the contradictory hypotheses `h : 0 < x * y` and `this : ¬0 < x * y`. (Remember
that `¬` is the logical symbol for “not”. If you don’t name a hypothesis, Lean labels it
`this`.)

```lean
example {y : ℝ} (x : ℝ) (h : 0 < x * y) (hx : 0 ≤ x) : 0 < y := by
  obtain hneg | hpos : y ≤ 0 ∨ 0 < y := le_or_lt y 0
  · -- the case `y ≤ 0`
    have : ¬0 < x * y
    · apply not_lt_of_ge
      calc
        0 = x * 0 := by ring
        _ ≥ x * y := by rel [hneg]
    contradiction
  · -- the case `0 < y`
    apply hpos
```

#### 4.4.2. Example

One very common way to get a contradiction is by proving that the hypotheses imply some “obviously
false” numeric fact, whose falseness can be checked with `numbers`.

> **Problem:**
>
> Let \(t\) be an integer which is less than 3, and suppose that \(t - 1 = 6\). Show that
> \(t=13\).

> **Solution:**
>
> We have,
>
> \[\begin{split}7 &= t\\
> &<3.\end{split}\]
>
> But clearly it’s false that \(7 <3\), contradiction. So any conclusion (including
> \(t=13\)) is true.

You can write this proof up in Lean directly using the `contradiction` tactic, as in the previous
examples:

```lean
example {t : ℤ} (h2 : t < 3) (h : t - 1 = 6) : t = 13 := by
  have H :=
  calc
    7 = t := by addarith [h]
    _ < 3 := h2
  have : ¬(7 : ℤ) < 3 := by numbers
  contradiction
```

But the pattern is also sufficiently common that there is a shorthand in Lean. If `H` is a
hypothesis whose negation can be proved by `numbers`, then writing `numbers at H` will
close the goal.

```lean
example {t : ℤ} (h2 : t < 3) (h : t - 1 = 6) : t = 13 := by
  have H :=
  calc
    7 = t := by addarith [h]
    _ < 3 := h2
  numbers at H -- this is a contradiction!
```

#### 4.4.3. Example

> **Problem:**
>
> Show that if \(n^2+n+1\equiv 1\mod 3\) then \(n\equiv 0\mod 3\) or
> \(n\equiv 2\mod 3\).

> **Solution:**
>
> We consider cases according to the residue of \(n\) modulo 3. If \(n\equiv 0\mod 3\) or
> \(n\equiv 2\mod 3\), then we are done. Otherwise, \(n\equiv 1\mod 3\), so
>
> \[\begin{split}0 &\equiv 0 + 3 \cdot 1 \mod 3 \\
> & = 1 ^ 2 + 1 + 1\\
> &\equiv n ^ 2 + n + 1 \mod 3\\
> &\equiv 1 \mod 3,\end{split}\]
>
> contradiction.

Notice that in the above proof we did some extra work to get \(0\equiv 1\mod 3\) for the
contradiction, rather than \(3\equiv 1\mod 3\), which could have been obtained more easily:

\[\begin{split}3 & = 1 ^ 2 + 1 + 1\\
&\equiv \ldots\end{split}\]

For the purposes of this book, we will treat as “obviously true/false” only congruences
\(i\equiv j\mod n\) for which \(0 \le i<n\) and \(0 \le j<n\). We will ask that
congruences involving larger numbers be explicitly reduced modulo \(n\), as we did here in
reducing \(3=1 ^ 2 + 1 + 1\) to \(0 + 3 \cdot 1\) modulo 3 and thus to \(0\).

The mathematical justification for this is the uniqueness lemma `Int.existsUnique_modEq_lt`,
discussed in Example 4.3.4.

```lean
example (n : ℤ) (hn : n ^ 2 + n + 1 ≡ 1 [ZMOD 3]) :
    n ≡ 0 [ZMOD 3] ∨ n ≡ 2 [ZMOD 3] := by
  mod_cases h : n % 3
  · -- case 1: `n ≡ 0 [ZMOD 3]`
    left
    apply h
  · -- case 2: `n ≡ 1 [ZMOD 3]`
    have H :=
      calc 0 ≡ 0 + 3 * 1 [ZMOD 3] := by extra
      _ = 1 ^ 2 + 1 + 1 := by numbers
      _ ≡ n ^ 2 + n + 1 [ZMOD 3] := by rel [h]
      _ ≡ 1 [ZMOD 3] := hn
    numbers at H -- contradiction!
  · -- case 3: `n ≡ 2 [ZMOD 3]`
    right
    apply h
```

#### 4.4.4. Example

We defined prime numbers in Example 4.1.8, and proved that 2 was prime. Let’s now
prove a slight reformulation of the definition, which will be convenient in showing that other
numbers are prime.

> **Lemma:**
>
> Let \(p\) be a natural number greater than or equal to 2. Suppose that for all natural
> numbers \(m\) for which \(1<m<p\), it is not true that \(m\) is a factor of \(p\).
> Show that \(p\) is prime.

> **Proof:**
>
> Since we are given that \(2 \le p\), what’s left is to prove the second part of the definition
> “prime”: let \(m\) be a factor of \(p\) (\(\star\)); we must show that \(m=1\) or
> \(m=p\).
>
> Since \(m\) is a factor of \(p\), we have that \(1 \le m\). So either \(m=1\) or
> \(1<m\); we consider cases accordingly.
>
> **Case 1** (\(m=1\)): This immediately gives our goal that \(m=1\) or \(m=p\).
>
> **Case 2** (\(1<m\)): Since \(m\) is a factor of \(p\), we have that
> \(m \le p\). So either \(m=p\) or \(m<p\); we consider cases accordingly.
>
> **Case 2(i)** (\(m=p\)): This immediately gives our goal that \(m=1\) or \(m=p\).
>
> **Case 2(ii)** (\(m<p\)): We have now established that \(1<m<p\), and by one of the
> facts given in the problem statement, this means that \(m\) is not a factor of \(p\).
> This contradicts the earlier statement (\(\star\)).

I’ve filled in about half of this proof in Lean; fill in the rest, including the final
contradiction. If you need to look up the Lean names for the size bounds on factors, they were
proved in [Example 3.2.7]](#m2001-dvd-bd-1) and [Example 3.2.8]](#m2001-dvd-bd-2).

```lean
example {p : ℕ} (hp : 2 ≤ p) (H : ∀ m : ℕ, 1 < m → m < p → ¬m ∣ p) : Prime p := by
  constructor
  · apply hp -- show that `2 ≤ p`
  intro m hmp
  have hp' : 0 < p := by extra
  have h1m : 1 ≤ m := Nat.pos_of_dvd_of_pos hmp hp'
  obtain hm | hm_left : 1 = m ∨ 1 < m := eq_or_lt_of_le h1m
  · -- the case `m = 1`
    left
    addarith [hm]
  -- the case `1 < m`
  sorry
```

We record this for future use under the Lean name `prime_test`.

Here’s an example of how this prime-testing lemma is used.

> **Problem:**
>
> Show that 5 is prime.

> **Solution:**
>
> Clearly \(2 ≤ 5\). Let \(m\) be a natural number with \(1<m<5\). We must show that
> 5 is not a multiple of \(m\). There are three cases to check:
>
> **Case 1** (\(m=2\)): Since 5 lies between the consecutive multiples
> \(2\cdot 2\) and \(2 \cdot 3\) of 2, it is not a multiple of 2.
>
> **Case 2** (\(m=3\)): Since 5 lies between the consecutive multiples
> \(3\cdot 1\) and \(3 \cdot 2\) of 3, it is not a multiple of 3.
>
> **Case 3** (\(m=4\)): Since 5 lies between the consecutive multiples
> \(4\cdot 1\) and \(4 \cdot 2\) of 4, it is not a multiple of 4.

```lean
example : Prime 5 := by
  apply prime_test
  · numbers
  intro m hm_left hm_right
  apply Nat.not_dvd_of_exists_lt_and_lt
  interval_cases m
  · use 2
    constructor <;> numbers
  · use 1
    constructor <;> numbers
  · use 1
    constructor <;> numbers
```

Here `constructor <;> numbers` is a shorthand for

```lean
constructor
· numbers
· numbers
```

More generally, `<;>` connects two tactics, performing the second one on every goal created by the
first one.

#### 4.4.5. Example

Here’s a harder example, with many cases.

> **Problem:**
>
> Let \(a\), \(b\) and \(c\) be positive natural numbers satisfying \(a^2+b^2=c^2\).
> Show that \(3 \le a\).

Three numbers satisfying this equation are called a *Pythagorean triple*, since by Pythagoras’
theorem this means that they form the three sides of a right-angled triangle. The natural numbers
3, 4, 5 satisfy this equation: \(3^2+4^2=5^2\). There are other solutions, like 5, 12, 13, but
we are showing in this problem that 3, 4, 5 is the smallest solution.

> **Solution:**
>
> Either \(a \le 2\) or \(3 \le a\). If \(3 \le a\) then we are done. We will derive
> a contradiction if \(a \le 2\).
>
> Either \(b \le 1\) or \(2 \le b\). We will consider these two cases separately.
>
> **Case 1** (\(b \le 1\)):
>
> We have that
>
> \[\begin{split}c ^ 2 & = a ^ 2 + b ^ 2 \\
> &\le 2^2+1^2\\
> &<3^2.\end{split}\]
>
> This implies that \(c<3\). We now have upper bounds \(a \le 2\),
> \(b \le 1\), \(c < 3\), so \(a\) is 1 or 2, \(b\) is 1, and \(c\) is 1 or 2.
> We can analyze all these cases and check they don’t work:
>
> \[\begin{split}1^2+1^2&\ne 1^2,\\
> 2^2+1^2&\ne 1^2,\\
> 1^2+1^2&\ne 2^2,\\
> 2^2+1^2&\ne 2^2.\end{split}\]
>
> **Case 2** (\(2 \le b\)):
>
> We have that
>
> \[\begin{split}b ^ 2 &< a ^ 2 + b ^ 2 \\
> & = c ^ 2,\end{split}\]
>
> so \(b<c\), so \(b+1\le c\). But also
>
> \[\begin{split}c ^ 2 &= a ^ 2 + b ^ 2 \\
> &≤ 2 ^ 2 + b ^ 2 \\
> & = b ^ 2 + 2 \cdot 2 \\
> & ≤ b ^ 2 + 2 b \\
> & < b ^ 2 + 2b + 1\\
> & = (b + 1) ^ 2,\end{split}\]
>
> so \(c<b+1\), so it is false that \(b+1\le c\). These two facts contradict each other.

Write this proof up in Lean. It will be long; my version is 27 lines.

```lean
example {a b c : ℕ} (ha : 0 < a) (hb : 0 < b) (hc : 0 < c)
    (h_pyth : a ^ 2 + b ^ 2 = c ^ 2) : 3 ≤ a := by
  sorry
```

#### 4.4.6. Exercises

1. Let \(x\) and \(y\) be real numbers, with \(x\) nonnegative, and let
   \(n\) be a positive natural number. Prove that if \(y^n \le x^n\) then
   \(y\le x\).

   We have used this fact before; it’s another of the lemmas which underlie the `cancel` tactic.

   ```lean
   example {x y : ℝ} (n : ℕ) (hx : 0 ≤ x) (hn : 0 < n) (h : y ^ n ≤ x ^ n) :
       y ≤ x := by
     sorry
   ```
2. Show that if \(n^2\equiv 4\mod 5\) then \(n\equiv 2\mod 5\) or \(n\equiv 3\mod 5\).

   ```lean
   example (n : ℤ) (hn : n ^ 2 ≡ 4 [ZMOD 5]) : n ≡ 2 [ZMOD 5] ∨ n ≡ 3 [ZMOD 5] := by
     sorry
   ```
3. Show that 7 is prime.

   ```lean
   example : Prime 7 := by
     sorry
   ```
4. Give a different proof of a problem from the exercises to [Section 2.1]](#m2001-tactic-mode): Let
   \(x\)
   be a rational number whose square is 4, and which is greater than 1. Show that \(x=2\).

   Instead of using the `cancel` tactic, give a direct proof using the ideas of this section:
   break into two cases, and
   then rule out one of them. (You may find it convenient to deduce a numeric contradiction.)

   ```lean
   example {x : ℚ} (h1 : x ^ 2 = 4) (h2 : 1 < x) : x = 2 := by
     have h3 :=
       calc
         (x + 2) * (x - 2) = x ^ 2 + 2 * x - 2 * x - 4 := by ring
         _ = 0 := by addarith [h1]
     rw [mul_eq_zero] at h3
     sorry
   ```
5. Show that a prime number is either 2 or odd.

   ```lean
   example (p : ℕ) (h : Prime p) : p = 2 ∨ Odd p := by
     sorry
   ```

### 4.5. Proof by contradiction

#### 4.5.1. Example

> **Problem:**
>
> Show that it is not true that for all real numbers \(x\), we have \(x^2\geq x\).

> **Solution:**
>
> Suppose that for all real numbers \(x\), we have \(x^2\geq x\). Then in particular
> \(0.5^2\geq 0.5\), but this is false, contradiction.

```lean
example : ¬ (∀ x : ℝ, x ^ 2 ≥ x) := by
  intro h
  have : 0.5 ^ 2 ≥ 0.5 := h 0.5
  numbers at this
```

#### 4.5.2. Example

> **Problem:**
>
> Show that 13 is not a multiple of 3.

In the past we have established non-divisibility facts using the theorem
`Nat.not_dvd_of_exists_lt_and_lt`. But now we finally have the tools to do this from first
principles.

> **Solution:**
>
> Suppose that 13 were a multiple of 3. Then there would exist a natural number \(k\) such that
> \(13=3k\).
>
> Case 1, \(k \le 4\): Then
>
> \[\begin{split}13 &= 3k \\
> &\le 3\cdot 4,\end{split}\]
>
> contradiction.
>
> Case 2, \(k \ge 5\): Then
>
> \[\begin{split}13 &= 3k \\
> &\ge 3\cdot 5,\end{split}\]
>
> contradiction.

```lean
example : ¬ 3 ∣ 13 := by
  intro H
  obtain ⟨k, hk⟩ := H
  obtain h4 | h5 := le_or_succ_le k 4
  · have h :=
    calc 13 = 3 * k := hk
      _ ≤ 3 * 4 := by rel [h4]
    numbers at h
  · sorry
```

#### 4.5.3. Example

> **Problem:**
>
> Let \(x\) and \(y\) be real numbers and suppose that \(x+y=0\). Show that it is not
> possible for both \(x\) and \(y\) to be positive.

> **Solution:**
>
> Suppose that \(x\) and \(y\) were both positive. Then
>
> \[\begin{split}0 &= x+y \\
> &> 0,\end{split}\]
>
> contradiction.

```lean
example {x y : ℝ} (h : x + y = 0) : ¬(x > 0 ∧ y > 0) := by
  intro h
  obtain ⟨hx, hy⟩ := h
  have H :=
  calc 0 = x + y := by rw [h]
    _ > 0 := by extra
  numbers at H
```

#### 4.5.4. Example

> **Problem:**
>
> Show that there does not exist a natural number \(n\), such that \(n^2=2\).

(Compare this with [Example 2.3.2]](#m2001-sq-ne-two).)

> **Solution:**
>
> Suppose that some integer \(n\) satisfied \(n^2=2\).
>
> Case 1, \(n \le 1\): Then
>
> \[\begin{split}2 &= n^2 \\
> &\le 1^2,\end{split}\]
>
> contradiction.
>
> Case 2, \(n \ge 2\): Then
>
> \[\begin{split}2 &= n^2 \\
> &\ge 2^2,\end{split}\]
>
> contradiction.

```lean
example : ¬ (∃ n : ℕ, n ^ 2 = 2) := by
  sorry
```

#### 4.5.5. Example

> **Lemma:**
>
> Show that an integer \(n\) is even if and only if it is not odd.

> **Proof:**
>
> First, let \(n\) be even, and suppose that it were also odd. Then \(n\equiv 0\mod 2\)
> but also \(n\equiv 1\mod 2\). So
>
> \[\begin{split}0 &\equiv n \mod 2 \\
> &\equiv 1\mod 2,\end{split}\]
>
> contradiction.
>
> Now, suppose that \(n\) is not odd. Since \(n\) has to be either even or odd, it is even.

We record this for future use in Lean problems under the name `Int.even_iff_not_odd`.

```lean
example (n : ℤ) : Int.Even n ↔ ¬ Int.Odd n := by
  constructor
  · intro h1 h2
    rw [Int.even_iff_modEq] at h1
    rw [Int.odd_iff_modEq] at h2
    have h :=
    calc 0 ≡ n [ZMOD 2] := by rel [h1]
      _ ≡ 1 [ZMOD 2] := by rel [h2]
    numbers at h -- contradiction!
  · intro h
    obtain h1 | h2 := Int.even_or_odd n
    · apply h1
    · contradiction
```

Now repeat the process to characterise “not-even.”

> **Lemma:**
>
> Show that an integer \(n\) is odd if and only if it is not even.

```lean
example (n : ℤ) : Int.Odd n ↔ ¬ Int.Even n := by
  sorry
```

#### 4.5.6. Example

> **Problem:**
>
> Let \(n\) be an integer. Show that \(n^2\not\equiv 2 \mod 3\).

> **Solution:**
>
> Suppose that \(n^2\equiv 2 \mod 3\).
> We consider cases according to the residue of \(n\) modulo 3.
>
> If \(n\equiv 0 \mod 3\), then
>
> \[\begin{split}0 &= 0^2 \\
> &\equiv n^2 \mod 3 \\
> &\equiv 2 \mod 3,\end{split}\]
>
> contradiction.
>
> If \(n\equiv 1 \mod 3\), then
>
> \[\begin{split}1 &= 1^2 \\
> &\equiv n^2 \mod 3 \\
> &\equiv 2 \mod 3,\end{split}\]
>
> contradiction.
>
> Finally, if \(n\equiv 2 \mod 3\), then
>
> \[\begin{split}1 &\equiv 1 + 3 \cdot 1\mod 3\\
> &=2^2 \\
> &\equiv n^2 \mod 3 \\
> &\equiv 2 \mod 3,\end{split}\]
>
> contradiction.

```lean
example (n : ℤ) : ¬(n ^ 2 ≡ 2 [ZMOD 3]) := by
  intro h
  mod_cases hn : n % 3
  · have h :=
    calc (0:ℤ) = 0 ^ 2 := by numbers
      _ ≡ n ^ 2 [ZMOD 3] := by rel [hn]
      _ ≡ 2 [ZMOD 3] := by rel [h]
    numbers at h -- contradiction!
  · sorry
  · sorry
```

#### 4.5.7. Example

We can now pay a couple of debts. First, there is this theorem, first mentioned in
Example 4.1.9:

> **Theorem:**
>
> Let \(p\), \(k\) and \(l\) be natural numbers, with \(k\ne 1\), \(k\ne p\), and
> \(p=kl\). Then \(p\) is not prime.

> **Proof:**
>
> \(k\) is a factor of \(p\). If \(p\) were prime, then by definition for any factor
> \(x\) of \(p\) either
> \(x=1\) or \(x=p\), so in particular \(k=1\) or \(k=p\). But either of these
> contradicts a hypothesis.

```lean
example {p : ℕ} (k l : ℕ) (hk1 : k ≠ 1) (hkp : k ≠ p) (hkl : p = k * l) :
    ¬(Prime p) := by
  have hk : k ∣ p
  · use l
    apply hkl
  intro h
  obtain ⟨h2, hfact⟩ := h
  have : k = 1 ∨ k = p := hfact k hk
  obtain hk1' | hkp' := this
  · contradiction
  · contradiction
```

#### 4.5.8. Example

Secondly, there is this theorem, first mentioned in [Example 3.2.6]](#m2001-not-divisible):

> **Theorem:**
>
> Let \(a\) and \(b\) be integers. If there exists an
> integer \(q\) such that \(bq<a<b(q + 1)\), then \(a\) is not a multiple
> of \(b\).

This is the lemma we have invoked in Lean as `Int.not_dvd_of_exists_lt_and_lt`.

> **Proof:**
>
> Suppose for the sake of contradiction that \(a\) is a multiple of \(b\). Then there exists
> an integer \(k\) such that \(a=bk\). Also, let \(q\) be an integer such that
> \(b q<a<b(q + 1)\).
>
> We first note that
>
> \[\begin{split}0 &= a - a\\
> &< b(q+1)-bq\\
> &=b.\end{split}\]
>
> Now let us reason from the two known inequalities separately. We first observe that
>
> \[\begin{split}bk &=a \\
> & < b(q+1),\end{split}\]
>
> and so (since \(b>0\)) we have \(k < q+1\).
>
> We next observe that
>
> \[\begin{split}bq &< a \\
> & =bk,\end{split}\]
>
> and so (since \(b>0\)) we have \(q < k\), thus \(q+1 \le k\).
>
> These two facts contradict each other, so \(a\) must not be a multiple of \(b\) after all.

```lean
example (a b : ℤ) (h : ∃ q, b * q < a ∧ a < b * (q + 1)) : ¬b ∣ a := by
  intro H
  obtain ⟨k, hk⟩ := H
  obtain ⟨q, hq₁, hq₂⟩ := h
  have hb :=
  calc 0 = a - a := by ring
    _ < b * (q + 1) - b * q := by rel [hq₁, hq₂]
    _ = b := by ring
  have h1 :=
  calc b * k = a := by rw [hk]
    _ < b * (q + 1) := hq₂
  cancel b at h1
  sorry
```

#### 4.5.9. Example

We also establish a test for primality which will be more efficient than the test from
Example 4.4.4.

> **Theorem:**
>
> Let \(p\) be a natural number which is at least 2. Let \(T\) be another natural number,
> whose square is greater than \(p\), and suppose that every natural number \(m\)
> for which \(1<m<T\) is not a factor of \(p\). Then \(p\) is prime.

(Notice that in the Example 4.4.4 test we had to check that every number up to
\(p\) was not a factor of \(p\); with this test we only need to check the numbers up to
approximately the square root of \(p\).)

> **Proof:**
>
> By the prime test from Example 4.4.4, it suffices to show that every natural
> number \(m\) with \(1<m<p\) is not a factor of \(p\). Let \(m\) be such a natural
> number. If \(m < T\) then by hypothesis \(m\) is not a factor of \(p\).
>
> So suppose that \(T \le m\), and that \(m\) is a factor of \(p\). Then there exists a
> natural number \(l\) such that \(p= ml\). The natural number \(l\) is a factor of
> \(p\), too.
>
> We claim that \(1<l\). It will suffice to show that \(m \cdot 1 < ml\), and indeed,
>
> \[\begin{split}m\cdot 1 &=m \\
> & < p\\
> &=ml.\end{split}\]
>
> We also claim that \(l<T\). It will suffice to show that \(Tl < T \cdot T\), and indeed,
>
> \[\begin{split}Tl & \le ml \\
> & =p\\
> &< T^2\\
> &=T\cdot T.\end{split}\]
>
> Since we have established that \(1<l<T\), by hypothesis \(l\) is not a factor of
> \(p\), contradiction. So \(m\) must not be a factor of \(p\) after all.

```lean
example {p : ℕ} (hp : 2 ≤ p)  (T : ℕ) (hTp : p < T ^ 2)
    (H : ∀ (m : ℕ), 1 < m → m < T → ¬ (m ∣ p)) :
    Prime p := by
  apply prime_test hp
  intro m hm1 hmp
  obtain hmT | hmT := lt_or_le m T
  · apply H m hm1 hmT
  intro h_div
  obtain ⟨l, hl⟩ := h_div
  have : l ∣ p
  · sorry
  have hl1 :=
    calc m * 1 = m := by ring
      _ < p := hmp
      _ = m * l := hl
  cancel m at hl1
  have hl2 : l < T
  · sorry
  have : ¬ l ∣ p := H l hl1 hl2
  contradiction
```

We record this for future use under the Lean name `better_prime_test`.

Here’s an example of how this prime-testing lemma is used. I have left some of the later cases
for you to check.

> **Problem:**
>
> Show that 79 is prime.

> **Solution:**
>
> Clearly \(2 ≤ 79\). Also notice that \(79<9^2\). Let \(m\) be a natural number with
> \(1<m<9\). We will show that 79 is not a multiple of \(m\). There are seven cases to
> check:
>
> **Case 1** (\(m=2\)): Since 79 lies between the consecutive multiples
> \(2\cdot 39\) and \(2 \cdot 40\) of 2, it is not a multiple of 2.
>
> **Case 2** (\(m=3\)): Since 79 lies between the consecutive multiples
> \(3\cdot 26\) and \(3 \cdot 27\) of 3, it is not a multiple of 3.
>
> **Case 3** (\(m=4\)): Since 79 lies between the consecutive multiples
> \(4\cdot 19\) and \(4 \cdot 20\) of 4, it is not a multiple of 4.
>
> (etc. for 5, 6, 7 and 8)

```lean
example : Prime 79 := by
  apply better_prime_test (T := 9)
  · numbers
  · numbers
  intro m hm1 hm2
  apply Nat.not_dvd_of_exists_lt_and_lt
  interval_cases m
  · use 39
    constructor <;> numbers
  · use 26
    constructor <;> numbers
  · use 19
    constructor <;> numbers
  · sorry
  · sorry
  · sorry
  · sorry
```

#### 4.5.10. Exercises

1. Show that there does not exist a real number \(t\), such that \(t \le 4\) and
   \(t\geq 5\).

   ```lean
   example : ¬ (∃ t : ℝ, t ≤ 4 ∧ t ≥ 5) := by
     sorry
   ```
2. Show that there does not exist a real number \(a\), such that \(a^2 \le 8\) and
   \(a^3\geq 30\).

   ```lean
   example : ¬ (∃ a : ℝ, a ^ 2 ≤ 8 ∧ a ^ 3 ≥ 30) := by
     sorry
   ```
3. Show that 7 is not even.

   ```lean
   example : ¬ Int.Even 7 := by
     sorry
   ```
4. Let \(n\) be an integer satisfying \(n+3=7\). Show that \(n\) cannot be both
   even and a solution to \(n^2=10\).

   ```lean
   example {n : ℤ} (hn : n + 3 = 7) : ¬ (Int.Even n ∧ n ^ 2 = 10) := by
     sorry
   ```
5. Let \(x\) be a real number satisfying \(x^2<9\). Show that \(x\) cannot be either
   less than or equal to -3, or greater than or equal to 3.

   ```lean
   example {x : ℝ} (hx : x ^ 2 < 9) : ¬ (x ≤ -3 ∨ x ≥ 3) := by
     sorry
   ```
6. Show that there does not exist a natural number \(N\), such that every natural number
   greater than \(N\) is even.

   ```lean
   example : ¬ (∃ N : ℕ, ∀ k > N, Nat.Even k) := by
     sorry
   ```
7. Let \(n\) be an integer. Show that \(n^2\not\equiv 2 \mod 4\).

   ```lean
   example (n : ℤ) : ¬(n ^ 2 ≡ 2 [ZMOD 4]) := by
     sorry
   ```
8. Show that 1 is not prime.

   We record this lemma for future use under the name `not_prime_one`.

   ```lean
   example : ¬ Prime 1 := by
     sorry
   ```
9. Show that 97 is prime.

   ```lean
   example : Prime 97 := by
     sorry
   ```

---



## 5. Logic {#m2001-5-logic}

> 📄 Source: https://hrmacbeth.github.io/math2001/05_Logic.html

In the course of [Chapter 2]](#m2001-proofs-with-structure) and
[Chapter 4]](#m2001-proofs-with-structure-ii) we learned the “grammar” of the
various logical symbols, like \(\land\), \(\forall\) and \(\to\).
In those chapters, logical reasoning took place in fairly concrete mathematical
situations: problems about equalities and inequalities in the natural
numbers, the rational numbers, and so on.

In this chapter, we take a more abstract point of view, studying the process of
logical reasoning in its own right. The central concept is the concept of *logical
equivalence*: transformations to the logical structure of a statement which are
always valid, because the “before” and “after” can be deduced from each other
using only abstract logical reasoning, not anything specific to the mathematical
situation at hand.

The most important logical equivalences are those covered in the final section of
the chapter, Section 5.3. These are logical
equivalences which move a negation symbol (\(\lnot\)) to a deeper position in a
logical statement. Taken together, these transformations give us a way to delay and
minimize our encounters with \(\lnot\), the most awkward logical symbol.

### 5.1. Logical equivalence

#### 5.1.1. Example

If you abstract away the numbers, definitions, equations and inequalities, you are left with pure
logic problems. And the pure logic tactics like `obtain`, `apply`, `constructor`, and so on
can still be used.

```lean
example {P Q : Prop} (h1 : P ∨ Q) (h2 : ¬ Q) : P := by
  obtain hP | hQ := h1
  · apply hP
  · contradiction
```

It’s hardly worth trying to write proofs like this in words. The \(P\) and \(Q\) are
abstract propositions (`Prop`), and this is just a game of manipulation.

```lean
example (P Q : Prop) : P → (P ∨ ¬ Q) := by
  intro hP
  left
  apply hP
```

#### 5.1.2. Example

We can think of propositional logic statements in the following way. Imagine that each variable,
like \(P\), can be either “true” or “false”. There are fixed rules for how truth and
falsehood combine under logical operations. For example, \(P \land Q\) is true if \(P\)
and \(Q\) are both true, and otherwise it is false. We can record this information in a table
called a *truth table*:

| P | Q | (P ∧ Q) |
| --- | --- | --- |
| true | true | true |
| false | true | false |
| true | false | false |
| false | false | false |

  

Likewise here is the rule for \(\lnot P\): it is the opposite of \(P\).

| P | ¬P |
| --- | --- |
| true | false |
| false | true |

  

Using the rules for the basic operations, we can work out the truth table for a more complex
statement step by step from the operations it’s composed of. For example, here’s how to find
the truth table for \(\lnot(P \land \lnot Q)\): first compute the table for \(\lnot Q\), then
for \(P \land \lnot Q\), then finally for \(\lnot(P \land \lnot Q)\).

| P | Q | ¬Q | (P ∧ ¬Q) | ¬(P ∧ ¬Q) |
| --- | --- | --- | --- | --- |
| true | true | false | false | true |
| false | true | false | false | true |
| true | false | true | true | false |
| false | false | true | false | true |

  

You should practise doing this by hand, but the Lean command `#truth_table` will also do it
automatically.

```lean
#truth_table ¬(P ∧ ¬ Q)
```

That’s how I generated these pictures! The `#truth_table` command was written by Joseph Rotella
with contributions from Ryan Edmonds; both are students at Brown University.

#### 5.1.3. Exercise

Here are the rules for the remaining basic logical operations:

| P | Q | (P ∨ Q) |
| --- | --- | --- |
| true | true | true |
| false | true | true |
| true | false | true |
| false | false | false |

  

| P | Q | (P → Q) |
| --- | --- | --- |
| true | true | true |
| false | true | true |
| true | false | false |
| false | false | true |

  

| P | Q | (P ↔ Q) |
| --- | --- | --- |
| true | true | true |
| false | true | false |
| true | false | false |
| false | false | true |

  
> **Problem:**
>
> Work out the truth table for \(P \leftrightarrow (\lnot P \lor Q)\).

Then check it in Lean.

#### 5.1.4. Example

Two propositional logic formulas are said to be *logically equivalent*, if the “if and only if”
between them can be proved in Lean. For example,

> **Problem:**
>
> Show that \(P \lor P\) is logically equivalent to \(P\).

```lean
example (P : Prop) : (P ∨ P) ↔ P := by
  constructor
  · intro h
    obtain h1 | h2 := h
    · apply h1
    · apply h2
  · intro h
    left
    apply h
```

An important caveat here is that there is still one logic tactic which hasn’t been introduced yet
(see Section 5.2). So there are some pairs of propositional logic
formulas which are logically equivalent in a way that we can’t yet demonstrate.

#### 5.1.5. Example

> **Problem:**
>
> Show that \(P \land (Q \lor R)\) is logically equivalent to
> \((P \land Q) \lor (P \land R)\).

This is a long one. I’ve done the first direction and left the second for you.

```lean
example (P Q R : Prop) : (P ∧ (Q ∨ R)) ↔ ((P ∧ Q) ∨ (P ∧ R)) := by
  constructor
  · intro h
    obtain ⟨h1, h2 | h2⟩ := h
    · left
      constructor
      · apply h1
      · apply h2
    · right
      constructor
      · apply h1
      · apply h2
  · sorry
```

We will not prove it in this book, but two statements in propositional logic are logically
equivalent if and only if they have the same truth table. For example, compare the output of these
two Lean commands:

```lean
#truth_table P ∧ (Q ∨ R)
#truth_table (P ∧ Q) ∨ (P ∧ R)
```

#### 5.1.6. Example

We can also perform this kind of abstract logic game when quantifiers are involved.

```lean
example {P Q : α → Prop} (h1 : ∀ x : α, P x) (h2 : ∀ x : α, Q x) :
    ∀ x : α, P x ∧ Q x := by
  intro x
  constructor
  · apply h1
  · apply h2
```

Here the \(P\) and \(Q\) are *predicates*, abstractions of a statement involving a variable
(here called \(x\)). Statements about quantified predicates are sometimes referred to as
*first-order logic*.

Here’s another example of abstract logical reasoning involving quantifiers.

```lean
example {P : α → β → Prop} (h : ∃ x : α, ∀ y : β, P x y) :
    ∀ y : β, ∃ x : α, P x y := by
  obtain ⟨x, hx⟩ := h
  intro y
  use x
  apply hx
```

The concept of logical equivalence also still makes sense in this setting.

> **Problem:**
>
> Show that \(\lnot\exists x, P(x)\) is logically equivalent to \(\forall x, \lnot P(x)\).

```lean
example (P : α → Prop) : ¬ (∃ x, P x) ↔ ∀ x, ¬ P x := by
  constructor
  · intro h a ha
    have : ∃ x, P x
    · use a
      apply ha
    contradiction
  · intro h h'
    obtain ⟨x, hx⟩ := h'
    have : ¬ P x := h x
    contradiction
```

#### 5.1.7. Exercises

1. Prove the following propositional logic statement:

   ```lean
   example {P Q : Prop} (h : P ∧ Q) : P ∨ Q := by
     sorry
   ```
2. Prove the following propositional logic statement:

   ```lean
   example {P Q R : Prop} (h1 : P → Q) (h2 : P → R) (h3 : P) : Q ∧ R := by
     sorry
   ```
3. Prove the following propositional logic statement:

   ```lean
   example (P : Prop) : ¬(P ∧ ¬ P) := by
     sorry
   ```
4. Prove the following propositional logic statement:

   ```lean
   example {P Q : Prop} (h1 : P ↔ ¬ Q) (h2 : Q) : ¬ P := by
     sorry
   ```
5. Prove the following propositional logic statement:

   ```lean
   example {P Q : Prop} (h1 : P ∨ Q) (h2 : Q → P) : P := by
     sorry
   ```
6. Prove the following propositional logic statement:

   ```lean
   example {P Q R : Prop} (h : P ↔ Q) : (P ∧ R) ↔ (Q ∧ R) := by
     sorry
   ```
7. Prove that \(P \land P\) is logically equivalent to \(P\).

   ```lean
   example (P : Prop) : (P ∧ P) ↔ P := by
     sorry
   ```
8. Prove that \(P \lor Q\) is logically equivalent to \(Q \lor P\).

   ```lean
   example (P Q : Prop) : (P ∨ Q) ↔ (Q ∨ P) := by
     sorry
   ```
9. Prove that \(\lnot(P \lor Q)\) is logically equivalent to \(\lnot P \land \lnot Q\).

   This theorem is in the library under the name `not_or`. It is one of [“De Morgan’s laws”](https://en.wikipedia.org/wiki/De_Morgan%27s_laws).

   ```lean
   example (P Q : Prop) : ¬(P ∨ Q) ↔ (¬P ∧ ¬Q) := by
     sorry
   ```
10. Prove the following first-order logic statement:

    ```lean
    example {P Q : α → Prop} (h1 : ∀ x, P x → Q x) (h2 : ∀ x, P x) : ∀ x, Q x := by
      sorry
    ```
11. Prove the following first-order logic statement:

    ```lean
    example {P Q : α → Prop} (h : ∀ x, P x ↔ Q x) : (∃ x, P x) ↔ (∃ x, Q x) := by
      sorry
    ```
12. Show that \(\exists x \ y, P(x, y)\) is logically equivalent to
    \(\exists y \ x, P(x, y)\).

    ```lean
    example (P : α → β → Prop) : (∃ x y, P x y) ↔ ∃ y x, P x y := by
      sorry
    ```
13. Show that \(\forall x \ y, P(x, y)\) is logically equivalent to
    \(\forall y \ x, P(x, y)\).

    ```lean
    example (P : α → β → Prop) : (∀ x y, P x y) ↔ ∀ y x, P x y := by
      sorry
    ```
14. Show that \((\exists x, P(x)) \land Q\) is logically equivalent to
    \(\exists x, (P(x) \land Q)\).

    ```lean
    example (P : α → Prop) (Q : Prop) : ((∃ x, P x) ∧ Q) ↔ ∃ x, (P x ∧ Q) := by
      sorry
    ```

### 5.2. The law of the excluded middle

A tradition which [goes back](https://en.wikipedia.org/wiki/Perfect_number) to the
[ancient Greeks](https://en.wikipedia.org/wiki/Amicable_numbers)
is to give a slightly silly name to a class of numbers, in order to provide shorter theorem
statements while studying it. In that vein, I present, just for this section … the superpowered
numbers!

> **Definition:**
>
> A natural number \(k\) is *superpowered*, if for every natural number \(n\), the number
> \(k^{k^n} + 1\) is prime.

```lean
def Superpowered (k : ℕ) : Prop := ∀ n : ℕ, Prime (k ^ k ^ n + 1)
```

#### 5.2.1. Example

Is 0 superpowered? \(0^{0^0}+1=1\), \(0^{0^1}+1=2\), \(0^{0^2}+1=2\),
\(0^{0^3}+1=2\). We can also do these calculations in Lean:

```lean
#eval 0 ^ 0 ^ 0 + 1 -- 1
#eval 0 ^ 0 ^ 1 + 1 -- 2
#eval 0 ^ 0 ^ 2 + 1 -- 2
```

The first is not prime, the others are, but taking them together, the “for all” in the definition is
false. Formally:

> **Lemma:**
>
> 0 is not superpowered.

> **Proof:**
>
> Suppose 0 were superpowered. Then in particular \(0^{0^0}+1=1\) would be prime, which is a
> contradiction, since 1 is not prime.

To write this in Lean, we use the lemma `not_prime_one`, from an exercise to
[Section 4.5]](#m2001-contradiction).

```lean
theorem not_superpowered_zero : ¬ Superpowered 0 := by
  intro h
  have one_prime : Prime (0 ^ 0 ^ 0 + 1) := h 0
  conv at one_prime => numbers -- simplifies that statement to `Prime 1`
  have : ¬ Prime 1 := not_prime_one
  contradiction
```

Don’t worry too much about the unfamiliar tactic `conv` in the above proof – we will not
encounter it outside of this section. Just compare the goal state before and after the use of the
tactic and check that you agree intuitively with the transformation which occurred.

#### 5.2.2. Example

Is 1 superpowered?

```lean
#eval 1 ^ 1 ^ 0 + 1 -- 2
#eval 1 ^ 1 ^ 1 + 1 -- 2
#eval 1 ^ 1 ^ 2 + 1 -- 2
```

> **Lemma:**
>
> 1 is superpowered.

> **Proof:**
>
> Let \(n\) be a natural number. Then \(1^{1^n}+1=1^1+1=2\), which is prime.

To write this in Lean, we use the lemma `prime_two`, from [Example 4.1.8]](#m2001-prime-def).

```lean
theorem superpowered_one : Superpowered 1 := by
  intro n
  conv => ring -- simplifies goal from `Prime (1 ^ 1 ^ n + 1)` to `Prime 2`
  apply prime_two
```

#### 5.2.3. Example

Is 2 superpowered?

```lean
#eval 2 ^ 2 ^ 0 + 1 -- 3
#eval 2 ^ 2 ^ 1 + 1 -- 5
#eval 2 ^ 2 ^ 2 + 1 -- 17
#eval 2 ^ 2 ^ 3 + 1 -- 257
#eval 2 ^ 2 ^ 4 + 1 -- 65537
```

All these numbers are prime, as it happens. But checking 257 is prime using our usual lemma
`better_prime_test` will be a good 30 lines of calculations in Lean, and as for 65537, I
certainly don’t have the patience. The next one will be even worse. Let’s leave the question of
2 open for now.

#### 5.2.4. Example

Is 3 superpowered?

```lean
#eval 3 ^ 3 ^ 0 + 1 -- 4
#eval 3 ^ 3 ^ 1 + 1 -- 28
#eval 3 ^ 3 ^ 2 + 1 -- 19684
```

Nope! It fails even at the first step.

> **Lemma:**
>
> 3 is not superpowered.

> **Proof:**
>
> Suppose 3 were superpowered. Then in particular \(3^{3^0}+1=4\) would be prime, which is a
> contradiction, since \(4=2\cdot 2\).

Remember to use the lemma `not_prime` in Lean to prove that a number is not prime by producing a
factor for it.

```lean
theorem not_superpowered_three : ¬ Superpowered 3 := by
  intro h
  dsimp [Superpowered] at h
  have four_prime : Prime (3 ^ 3 ^ 0 + 1) := h 0
  conv at four_prime => numbers -- simplifies that statement to `Prime 4`
  have four_not_prime : ¬ Prime 4
  · apply not_prime 2 2
    · numbers -- show `2 ≠ 1`
    · numbers -- show `2 ≠ 4`
    · numbers -- show `4 = 2 * 2`
  contradiction
```

#### 5.2.5. Example

All this was warmup. Here is the question I really want to study.

> **Problem:**
>
> Prove that there exists a natural number \(k\), such that \(k\) is superpowered and
> \(k+1\) is not.

> **Solution:**
>
> We consider two cases, depending on whether or not 2 is superpowered.
>
> If 2 is superpowered, then \(k=2\) has the desired property, since 2 is superpowered and 3
> is not superpowered.
>
> If not, then \(k=1\) has the desired property, since 1 is superpowered and 2 is not
> superpowered. 1

The point about this proof is that it works even though we don’t know whether 2 is superpowered or
not. Either way, we have a way of solving the problem.

The fact that any statement (such as “2 is superpowered”) must be either true or false is an axiom
of mathematics, called the *law of the excluded middle*. Therefore, this is always a valid case
division in a proof, although it is relatively rare to need to do it.

In Lean, you can perform a case division on the truth or falsehood of a statement by using the
tactic `by_cases`. In the proof that follows, using this tactic takes us from a goal state of

```
⊢ ∃ k, Superpowered k ∧ ¬ Superpowered (k + 1)
```

to a goal state with two goals, one under the assumption `Superpowered 2` and one under the
assumption `¬ Superpowered 2`.

```
h2 : Superpowered 2
⊢ ∃ k, Superpowered k ∧ ¬ Superpowered (k + 1)

h2 : ¬ Superpowered 2
⊢ ∃ k, Superpowered k ∧ ¬ Superpowered (k + 1)
```

Here is the full proof in Lean.

```lean
example : ∃ k : ℕ, Superpowered k ∧ ¬ Superpowered (k + 1) := by
  by_cases h2 : Superpowered 2
  · use 2
    constructor
    · apply h2
    · apply not_superpowered_three
  · use 1
    constructor
    · apply superpowered_one
    · apply h2
```

#### 5.2.6. Example

As noted above, it’s relatively rare to need to use the law of the excluded middle in a proof. But
here is one more example where it’s needed, this time from propositional logic: “two wrongs make a
right”.

```lean
example {P : Prop} (hP : ¬¬P) : P := by
  by_cases hP : P
  · apply hP
  · contradiction
```

#### 5.2.7. Exercises

1. Let us call a real number \(x\) *tribalanced*, if for every natural number \(n\), the
   inequality \(\left(1+\frac{x}{n}\right)^n<3\) holds. Show that there exists a real number
   \(x\), such that \(x\) is tribalanced and \(x+1\) is not.

   ```lean
   def Tribalanced (x : ℝ) : Prop := ∀ n : ℕ, (1 + x / n) ^ n < 3

   example : ∃ x : ℝ, Tribalanced x ∧ ¬ Tribalanced (x + 1) := by
     sorry
   ```
2. Prove that \(\lnot P \to \lnot Q\) is logically equivalent to \(Q \to P\). You will
   need to use the law of the excluded middle.

   This logical equivalence is known as the *principle of contraposition*. You might like to
   compare their truth tables as a sanity check.

   ```lean
   example (P Q : Prop) : (¬P → ¬Q) ↔ (Q → P) := by
     sorry
   ```
3. In case you’re still wondering: 2 is not superpowered. The question was raised in 1650 by the
   mathematician Pierre de Fermat, who observed, as we did, that 3, 5, 17, 257 and 65537 are all
   prime. It was settled in 1732, when Leonhard Euler showed that the next number in the sequence,
   \(2^{2^5}+1=4294967297\), is equal to \(641 \times 6700417\) and therefore not prime.

   Use Euler’s discovery to give a proof without case division to the problem in
   Example 5.2.5.

   ```lean
   example : ∃ k : ℕ, Superpowered k ∧ ¬ Superpowered (k + 1) := by
     sorry
   ```

Footnotes

1
:   Experts will notice that this proof adapts the idea from a more famous
    problem: show that there exists an irrational power of an irrational number which is rational.

### 5.3. Normal form for negations

#### 5.3.1. Example

An important family of logical equivalences allows us to “push” negations inwards in a logical
statement. For example, we proved a rule for negating \(\exists\) in
Example 5.1.6 (that \(\lnot\exists x, P(x)\) is
logically equivalent to \(\forall x, \lnot P(x)\)), and a rule for negating \(\lor\) in the
exercises to Section 5.1 (that \(\lnot(P \lor Q)\) is
logically equivalent to \(\lnot P \land \lnot Q\)).

Let’s do one more of this form, a rule for negating \(\land\). This one requires the use of
the law of the excluded middle. I have done the first half and left the second half for you.

> **Problem:**
>
> Show that \(\lnot(P \land Q)\) is logically equivalent to \(\lnot P \lor \lnot Q\).

```lean
example (P Q : Prop) : ¬ (P ∧ Q) ↔ (¬ P ∨ ¬ Q) := by
  constructor
  · intro h
    by_cases hP : P
    · right
      intro hQ
      have hPQ : P ∧ Q
      · constructor
        · apply hP
        · apply hQ
      contradiction
    · left
      apply hP
  · sorry
```

Here is the full set of rules, together with their Lean lemma names. The remaining proofs have been
left for the exercises to this section.

Table 5.1 Logical equivalences for negations







| Operation | Negation-outward | Negation-inward | Lean name | Proof |
| --- | --- | --- | --- | --- |
| \(\lnot\) | \(\lnot(\lnot P)\) | \(P\) | `not_not` | Exercises 5.3.6 |
| \(\lor\) | \(\lnot(P \lor Q)\) | \(\lnot P \land \lnot Q\) | `not_or` | Exercises 5.1.7 |
| \(\land\) | \(\lnot(P \land Q)\) | \(\lnot P \lor \lnot Q\) | `not_and_or` | Example 5.3.1 |
| \(\to\) | \(\lnot(P \to Q)\) | \(P \land \lnot Q\) | `not_imp` | Exercises 5.3.6 |
| \(\exists\) | \(\lnot(\exists x, P(x))\) | \(\forall x, \lnot P(x)\) | `not_exists` | Example 5.1.6 |
| \(\forall\) | \(\lnot(\forall x, P(x))\) | \(\exists x, \lnot P(x)\) | `not_forall` | Exercises 5.3.6 |

#### 5.3.2. Example

By applying these rules in sequence, any mathematical statement can be brought to a form with
“negations on the inside”. This is generally the most convenient form for proofs (compare the
relative awkwardness of the contradiction proofs in [Section 4.4]](#m2001-contradiction-hyp) and
[Section 4.5]](#m2001-contradiction) with the proofs in earlier sections).

Here is an example of this process.

> **Problem:**
>
> Show that \(\lnot(\forall m :\mathbb{Z}, m\ne 2 \to \exists n:\mathbb{Z},n^2 = m)\) is
> logically equivalent to
> \(\exists m :\mathbb{Z}, m\ne 2\land \forall n :\mathbb{Z},n^2 ≠ m\).

We can prove this in Lean with a calculation using the `rel` tactic, rewriting at each step by
one of the rules in Table 5.1.

```lean
example :
    ¬(∀ m : ℤ, m ≠ 2 → ∃ n : ℤ, n ^ 2 = m) ↔ ∃ m : ℤ, m ≠ 2 ∧ ∀ n : ℤ, n ^ 2 ≠ m :=
  calc ¬(∀ m : ℤ, m ≠ 2 → ∃ n : ℤ, n ^ 2 = m)
      ↔ ∃ m : ℤ, ¬(m ≠ 2 → ∃ n : ℤ, n ^ 2 = m) := by rel [not_forall]
    _ ↔ ∃ m : ℤ, m ≠ 2 ∧ ¬(∃ n : ℤ, n ^ 2 = m) := by rel [not_imp]
    _ ↔ ∃ m : ℤ, m ≠ 2 ∧ ∀ n : ℤ, n ^ 2 ≠ m := by rel [not_exists]
```

#### 5.3.3. Example

Try it yourself!

> **Problem:**
>
> Show that \(\lnot(\forall n :\mathbb{Z}, \exists m : \mathbb{Z}, n^2 < m < (n+1)^2)\) is
> logically equivalent to
> \(\exists n :\mathbb{Z}, \forall m : \mathbb{Z}, n^2 \geq m \lor m \geq (n+1)^2\).

In this problem, in addition to the rules from Table 5.1, you’ll need
to use the lemma `not_lt` to convert the negation of a \(<\) to a \(\geq\).

Also notice that \(n^2 < m < (n+1)^2\) is shorthand for \(n^2 < m \land m < (n+1)^2\).
We have encountered this point before, in [Example 1.4.4]](#m2001-shorthand).

```lean
example : ¬(∀ n : ℤ, ∃ m : ℤ, n ^ 2 < m ∧ m < (n + 1) ^ 2)
    ↔ ∃ n : ℤ, ∀ m : ℤ, n ^ 2 ≥ m ∨ m ≥ (n + 1) ^ 2 :=
  sorry
```

#### 5.3.4. Example

This process is clearly very formulaic. You should learn to do it in your head.
And as usual, when a proof process is formulaic, there is a Lean tactic to do it for us. The tactic
is called `push_neg`. Here it is, with output, on the last two examples:

```lean
#push_neg ¬(∀ m : ℤ, m ≠ 2 → ∃ n : ℤ, n ^ 2 = m)
  -- ∃ m : ℤ, m ≠ 2 ∧ ∀ (n : ℤ), n ^ 2 ≠ m

#push_neg ¬(∀ n : ℤ, ∃ m : ℤ, n ^ 2 < m ∧ m < (n + 1) ^ 2)
  -- ∃ n : ℤ, ∀ m : ℤ, m ≤ n ^ 2 ∨ (n + 1) ^ 2 ≤ m
```

Work out the following negations in your head, then check your work using the Lean output.

```lean
#push_neg ¬(∃ m n : ℤ, ∀ t : ℝ, m < t ∧ t < n)
#push_neg ¬(∀ a : ℕ, ∃ x y : ℕ, x * y ∣ a → x ∣ a ∧ y ∣ a)
#push_neg ¬(∀ m : ℤ, m ≠ 2 → ∃ n : ℤ, n ^ 2 = m)
```

There are more exercises of this style at the end of the section.

#### 5.3.5. Example

Let’s show how the process of pushing negations inwards is useful in regular proofs. We return to
the problem from [Example 4.5.4]](#m2001-sq-ne-two).

> **Problem:**
>
> Show that there does not exist a natural number \(n\), such that \(n^2=2\).

At the time, we observed that the solution to this problem seemed very similar to that of
[Example 2.3.2]](#m2001-sq-ne-two).

> **Problem:**
>
> Let \(n\) be any natural number. Show that \(n ^ 2 \ne 2\).

Now we can understand why: the statements of the two problems are
logically equivalent! The mathematical ideas in the two solutions are the same, but
[Example 2.3.2]](#m2001-sq-ne-two)’s solution is conceptually simpler because it doesn’t involve a
contradiction. We can give a more comprehensible solution to
[Example 4.5.4]](#m2001-sq-ne-two) by re-stating it in the form [Example 2.3.2]](#m2001-sq-ne-two)
and then writing out [Example 2.3.2]](#m2001-sq-ne-two)’s solution.

> **Solution:**
>
> It suffices to prove that for any natural number \(n\), we have \(n ^ 2 \ne 2\).
>
> We consider separately the cases \(n \le 1\) and \(2 \le n\).
>
> **Case 1** (\(n \le 1\)): It suffices to prove that \(n ^ 2 < 2\). Indeed,
>
> \[\begin{split}n ^ 2 & \le 1 ^ 2\\
> &<2.\end{split}\]
>
> **Case 2** (\(2 \le n\)): It suffices to prove that \(n ^ 2 > 2\). Indeed,
>
> \[\begin{split}2 &< 2 ^ 2\\
> & \le n ^ 2.\end{split}\]

Here’s how this looks in Lean. I left a bit for you.

```lean
example : ¬ (∃ n : ℕ, n ^ 2 = 2) := by
  push_neg
  intro n
  have hn := le_or_succ_le n 1
  obtain hn | hn := hn
  · apply ne_of_lt
    calc
      n ^ 2 ≤ 1 ^ 2 := by rel [hn]
      _ < 2 := by numbers
  · sorry
```

#### 5.3.6. Exercises

1. Show that \(\lnot(\lnot P)\) is logically equivalent to \(P\).

   The point of this exercise is that you are proving the lemma `not_not`, from
   Table 5.1. So don’t use that lemma or the tactic `push_neg`
   which depends on it; instead prove this from scratch. You will need to use the law of the
   excluded middle.

   ```lean
   example (P : Prop) : ¬ (¬ P) ↔ P := by
     sorry
   ```
2. Prove that \(\lnot(P \to Q)\) is logically equivalent to \(P \land \lnot Q\).

   The point of this exercise is that you are proving the lemma `not_imp`, from
   Table 5.1. So don’t use that lemma or the tactic `push_neg`
   which depends on it; instead prove this from scratch. You will need to use the law of the
   excluded middle.

   ```lean
   example (P Q : Prop) : ¬ (P → Q) ↔ (P ∧ ¬ Q) := by
     sorry
   ```
3. Show that \(\lnot\forall x, P(x)\) is logically equivalent to \(\exists x, \lnot P(x)\).

   The point of this exercise is that you are proving the lemma `not_forall`, from
   Table 5.1. So don’t use that lemma or the tactic `push_neg`
   which depends on it; instead prove this from scratch. You will need to use the law of the
   excluded middle.

   ```lean
   example (P : α → Prop) : ¬ (∀ x, P x) ↔ ∃ x, ¬ P x := by
     sorry
   ```
4. Show step by step using the rules in Table 5.1 that
   \(\lnot(\forall a b :\mathbb{Z}, ab=1 \to a = 1 \lor b = 1)\) is logically equivalent to
   \(\exists a b :\mathbb{Z}, ab = 1\land a \ne 1 \land b \ne 1\).

   ```lean
   example : (¬ ∀ a b : ℤ, a * b = 1 → a = 1 ∨ b = 1)
       ↔ ∃ a b : ℤ, a * b = 1 ∧ a ≠ 1 ∧ b ≠ 1 :=
     sorry
   ```
5. Show step by step using the rules in Table 5.1 that
   \(\lnot(\exists x:\mathbb{R},\forall y:\mathbb{R}, y \le x)\)
   is logically equivalent to
   \(\forall x:\mathbb{R},\exists y:\mathbb{R}, y > x\).

   ```lean
   example : (¬ ∃ x : ℝ, ∀ y : ℝ, y ≤ x) ↔ (∀ x : ℝ, ∃ y : ℝ, y > x) :=
     sorry
   ```
6. Show step by step using the rules in Table 5.1 that
   \(\lnot(\exists m:\mathbb{Z},\forall n:\mathbb{Z},m=n+5)\)
   is logically equivalent to
   \(\forall m:\mathbb{Z},\exists n:\mathbb{Z},m\ne n+5\).

   ```lean
   example : ¬ (∃ m : ℤ, ∀ n : ℤ, m = n + 5) ↔ ∀ m : ℤ, ∃ n : ℤ, m ≠ n + 5 :=
     sorry
   ```
7. Work out the following negations in your head, then check your work using the Lean output.

   ```lean
   #push_neg ¬(∀ n : ℕ, n > 0 → ∃ k l : ℕ, k < n ∧ l < n ∧ k ≠ l)
   #push_neg ¬(∀ m : ℤ, m ≠ 2 → ∃ n : ℤ, n ^ 2 = m)
   #push_neg ¬(∃ x : ℝ, ∀ y : ℝ, ∃ m : ℤ, x < y * m ∧ y * m < m)
   #push_neg ¬(∃ x : ℝ, ∀ q : ℝ, q > x → ∃ m : ℕ, q ^ m > x)
   ```
8. Show that it is not true that for all real numbers \(x\), we have \(x^2\geq x\).

   (We solved this already in [Example 4.5.1]](#m2001-contradiction-ex1), but this time, give a proof
   which begins with `push_neg`.)

   ```lean
   example : ¬ (∀ x : ℝ, x ^ 2 ≥ x) := by
     push_neg
     sorry
   ```
9. Show that there does not exist a real number \(t\), such that \(t \le 4\) and
   \(t\geq 5\).

   (We solved this already in the exercises to [Section 4.5]](#m2001-contradiction), but this time,
   give a proof which begins with `push_neg`.)

   ```lean
   example : ¬ (∃ t : ℝ, t ≤ 4 ∧ t ≥ 5) := by
     push_neg
     sorry
   ```
10. Show that 7 is not even.

    (We solved this already in the exercises to [Section 4.5]](#m2001-contradiction), but this time,
    give a proof which begins with `push_neg`.)

    ```lean
    example : ¬ Int.Even 7 := by
      dsimp [Int.Even]
      push_neg
      sorry
    ```
11. Let \(p\) and \(k\) be natural numbers, with \(k\ne 1\), \(k\ne p\), and
    \(k\mid p\). Show that \(p\) is not prime.

    (We solved this already in [Example 4.5.7]](#m2001-not-prime-proof), but this time, give a proof
    which begins with `push_neg`.)

    ```lean
    example {p : ℕ} (k : ℕ) (hk1 : k ≠ 1) (hkp : k ≠ p) (hk : k ∣ p) : ¬ Prime p := by
      dsimp [Prime]
      push_neg
      sorry
    ```
12. Show that it is not true that there exists an integer \(a\),
    such that for all integers \(n\), \(2a^3 ≥ na+7\).

    Suggested structure: Start your proof by negation-normalizing.

    You might find it interesting to compare this fact with Exercise 8 in
    [Section 2.5]](#m2001-exists). How is it possible that this statement is false and that one is
    true?

    ```lean
    example : ¬ ∃ a : ℤ, ∀ n : ℤ, 2 * a ^ 3 ≥ n * a + 7 := by
      sorry
    ```
13. Let \(p \geq 2\) be a natural number which is not prime. Show that there exists a natural
    number \(2 \le m < p\) which is a factor of \(p\).

    We record this lemma for future use under the name `exists_factor_of_not_prime`.

    Suggested structure: Set up an intermediate goal that it is not true that any natural number
    \(2 \le m < p\) is
    not a factor of \(p\), and prove it by contradiction using the lemma `prime_test` from
    [Example 4.4.4]](#m2001-prime-test). Then negation-normalize that result.

    ```lean
    example {p : ℕ} (hp : ¬ Prime p) (hp2 : 2 ≤ p) : ∃ m, 2 ≤ m ∧ m < p ∧ m ∣ p := by
      have H : ¬ (∀ (m : ℕ), 2 ≤ m → m < p → ¬m ∣ p)
      · intro H
        sorry
      sorry
    ```

---



## 6. Induction {#m2001-6-induction}

> 📄 Source: https://hrmacbeth.github.io/math2001/06_Induction.html

This chapter introduces *induction*, a proof method which applies to the natural numbers
and to other discrete types such as integers or pairs of natural numbers.
We also introduce *recursion*, a method for defining sequences (and, more generally,
functions from discrete types); induction is the canonical method for proving results about
recursively-defined objects.

In Section 6.1 - Section 6.3,
we use only the most traditional form of induction, proving a result for a natural number by
relating it to the result for the previous natural number, and small variants on this form
of induction. In Section 6.4 -
Section 6.7, we introduce *strong induction*, and, even more
generally, *well-founded induction*. These induction principles are more flexible.

### 6.1. Introduction

#### 6.1.1. Example

> **Problem:**
>
> Let \(n\) be a natural number. Show that \(2 ^n\ge n+1\).

> **Solution:**
>
> We prove this by induction on \(n\). The base case, \(2^0\geq 0+1\), is clear.
>
> Suppose now that for some natural number \(k\), it is true that \(2 ^k\ge k+1\). Then
>
> \[\begin{split}2^{k+1} &= 2 \cdot 2^k \\
> &\ge 2(k+1)\\
> &=(k+1+1) + k\\
> &\geq k+1+1.\end{split}\]

In Lean, the tactic `simple_induction` will set up an induction proof. Here, before the line
`simple_induction n with k IH`, the goal state displays a single goal,

```
n : ℕ
⊢ 2 ^ n ≥ n + 1
```

and after the tactic is used the goal state displays two goals, one for the base case and one for
the inductive step.

```
⊢ 2 ^ 0 ≥ 0 + 1

k : ℕ
IH : 2 ^ k ≥ k + 1
⊢ 2 ^ (k + 1) ≥ k + 1 + 1
```

Here is the full proof in Lean.

```lean
example (n : ℕ) : 2 ^ n ≥ n + 1 := by
  simple_induction n with k IH
  · -- base case
    numbers
  · -- inductive step
    calc 2 ^ (k + 1) = 2 * 2 ^ k := by ring
      _ ≥ 2 * (k + 1) := by rel [IH]
      _ = (k + 1 + 1) + k := by ring
      _ ≥ k + 1 + 1 := by extra
```

#### 6.1.2. Example

> **Theorem:**
>
> Let \(n\) be a natural number. Then \(n\) is either even or odd.

(Compare [Example 3.1.9]](#m2001-even-or-odd), [Example 4.2.9]](#m2001-even-or-odd-proof).)

> **Proof:**
>
> We prove this by induction on \(n\).
>
> The base case is to show that 0 is either even or odd. We will show that it is even. Indeed,
> \(0=2\cdot 0\).
>
> Suppose now that for some natural number \(k\), it is true that \(k\) is either even or
> odd.
>
> **Case 1** (\(k\) is even): Then there exists an integer \(x\) such that \(k=2x\), and
> so \(k+1 = 2x+1\), so \(k+1\) is odd.
>
> **Case 2** (\(k\) is odd): Then there exists an integer \(x\) such that \(k=2x+1\),
> and
>
> \[\begin{split}k+1 &= (2x+1)+1 \\
> &= 2(x+1),\end{split}\]
>
> so \(k+1\) is even.

Here is the outline of this argument in Lean. Fill in the sorries.

```lean
example (n : ℕ) : Even n ∨ Odd n := by
  simple_induction n with k IH
  · -- base case
    sorry
  · -- inductive step
    obtain ⟨x, hx⟩ | ⟨x, hx⟩ := IH
    · sorry
    · sorry
```

#### 6.1.3. Example

> **Theorem:**
>
> Let \(a, b, d\) be integers, and suppose that \(a\equiv b \mod d\). Let \(n\) be a
> natural number. Then \(a^n\equiv b ^ n \mod d\).

This is the power rule for modular arithmetic, the lemma `Int.modEq.pow`, which we stated without
proof in [Example 3.3.9]](#m2001-modeq-pow). It is one of the lemmas which are bundled together to
form the tactic `rel`’s capability for modular arithmetic.

> **Proof:**
>
> We prove this by induction on \(n\).
>
> We first note that \(a^0-b^0 = d\cdot 0\), so \(d\mid a^0-b^0\), so
> \(a^0\equiv b ^ 0 \mod d\). This is the base case.
>
> Now, let \(k\) be a natural number, and suppose that \(a^k\equiv b ^ k \mod d\). Then
> there exists an integer \(x\) such that \(a^k- b ^ k =dx\). Also, by hypothesis,
> \(a\equiv b \mod d\), so there exists an integer \(y\) such that \(a-b=dy\). We then
> have
>
> \[\begin{split}a^{k+1}-b^{k+1} &= a(a^k-b^k)+b^k(a-b) \\
> &= a(dx) +b^k(dy)\\
> &=d(ax+b^ky),\end{split}\]
>
> so \(d\mid a^{k+1}-b^{k+1}\), so \(a^{k+1}\equiv b ^ {k+1} \mod d\).

Write out this proof in Lean.

```lean
example {a b d : ℤ} (h : a ≡ b [ZMOD d]) (n : ℕ) : a ^ n ≡ b ^ n [ZMOD d] := by
  sorry
```

#### 6.1.4. Example

> **Problem:**
>
> Let \(n\) be a natural number. Show that \(4^n\) is congruent to either \(1\) or
> \(4\) modulo 15.

> **Solution:**
>
> We prove this by induction on \(n\). First, \(4^0=1\), so \(4^0\equiv 1\mod 15\).
>
> Now, let \(k\) be a natural number, and suppose that we know that \(4^k\) is congruent to
> either \(1\) or \(4\) modulo 15.
>
> **Case 1** (\(4^k\equiv 1\mod 15\)): Then
>
> \[\begin{split}4^{k+1}&=4\cdot 4^{k}\\
> &\equiv 4 \cdot 1\mod 15\\
> &=4.\end{split}\]
>
> **Case 2** (\(4^k\equiv 4\mod 15\)): Then
>
> \[\begin{split}4^{k+1}&=4\cdot 4^{k}\\
> &\equiv 4 \cdot 4\mod 15\\
> &=15\cdot 1+1\\
> &\equiv 1\mod 15.\end{split}\]

```lean
example (n : ℕ) : 4 ^ n ≡ 1 [ZMOD 15] ∨ 4 ^ n ≡ 4 [ZMOD 15] := by
  simple_induction n with k IH
  · -- base case
    left
    numbers
  · -- inductive step
    obtain hk | hk := IH
    · right
      calc (4:ℤ) ^ (k + 1) = 4 * 4 ^ k := by ring
        _ ≡ 4 * 1 [ZMOD 15] := by rel [hk]
        _ = 4 := by numbers
    · left
      calc (4:ℤ) ^ (k + 1) = 4 * 4 ^ k := by ring
        _ ≡ 4 * 4 [ZMOD 15] := by rel [hk]
        _ = 15 * 1 + 1 := by numbers
        _ ≡ 1 [ZMOD 15] := by extra
```

#### 6.1.5. Example

We can also use induction to prove results about all natural numbers greater than a given number.
We just start the induction at that number.

> **Problem:**
>
> Let \(n\) be a natural number greater than or equal to 2. Show that \(3 ^n\ge 2^n+5\).

> **Solution:**
>
> We prove this by induction on \(n\), starting at 2. The base case, \(3^2\geq 2^2+5\), is
> clear.
>
> Suppose now that for some natural number \(k\), it is true that \(3 ^k\ge 2^k+5\). Then
>
> \[\begin{split}3^{k+1} &= 2 \cdot 3^k + 3^k\\
> &\ge 2(2^k+5)+3^k\\
> &=2^{k+1}+5+(5+3^k)\\
> &\geq 2^{k+1}+5.\end{split}\]

In Lean, the tactic `induction_from_starting_point` will set up an induction proof starting at a
given point.

```lean
example {n : ℕ} (hn : 2 ≤ n) : (3:ℤ) ^ n ≥ 2 ^ n + 5 := by
  induction_from_starting_point n, hn with k hk IH
  · -- base case
    numbers
  · -- inductive step
    calc (3:ℤ) ^ (k + 1) = 2 * 3 ^ k + 3 ^ k := by ring
      _ ≥ 2 * (2 ^ k + 5) + 3 ^ k := by rel [IH]
      _ = 2 ^ (k + 1) + 5 + (5 + 3 ^ k) := by ring
      _ ≥ 2 ^ (k + 1) + 5 := by extra
```

#### 6.1.6. Example

Induction from a nonzero starting point is a particularly useful technique for “sufficiently large”
problems.

> **Problem:**
>
> Show that for all sufficiently large natural numbers \(n\), \(2^n\geq n^2\).

> **Solution:**
>
> We will show this for all natural numbers \(n\geq 4\).
>
> We prove this by induction on \(n\), starting at 4. The base case, \(2^4\geq 4^2\), is
> clear.
>
> Suppose now that for some natural number \(k\geq 4\), it is true that \(2 ^k\ge k^2\).
> Then
>
> \[\begin{split}2^{k+1}&=2\cdot 2^k\\
> &\geq 2k^2\\
> &=k^2+k\cdot k\\
> &\geq k^2+4k\\
> &=k^2+2k+2k\\
> &\geq k^2+2k+2\cdot 4\\
> &=(k+1)^2+7\\
> &\geq (k+1)^2.\end{split}\]

Here is the outline of this argument in Lean. Fill in the sorries.

```lean
example : forall_sufficiently_large n : ℕ, 2 ^ n ≥ n ^ 2 := by
  dsimp
  use 4
  intro n hn
  induction_from_starting_point n, hn with k hk IH
  · -- base case
    sorry
  · -- inductive step
    sorry
```

#### 6.1.7. Exercises

1. Let \(n\) be a natural number. Show that \(3 ^n\ge n^2+n+1\).

   ```lean
   example (n : ℕ) : 3 ^ n ≥ n ^ 2 + n + 1 := by
     sorry
   ```
2. Let \(a\geq -1\) be a real number, and let \(n\) be a natural number. Show that
   \((1+a)^n\ge 1+na\).

   This fact is known as *Bernoulli’s inequality*.

   ```lean
   example {a : ℝ} (ha : -1 ≤ a) (n : ℕ) : (1 + a) ^ n ≥ 1 + n * a := by
     sorry
   ```
3. Let \(n\) be a natural number. Show that \(5^n\) is congruent to either \(1\) or
   \(5\) modulo 8.

   ```lean
   example (n : ℕ) : 5 ^ n ≡ 1 [ZMOD 8] ∨ 5 ^ n ≡ 5 [ZMOD 8] := by
     sorry
   ```
4. Let \(n\) be a natural number. Show that \(6^n\) is congruent to either \(1\) or
   \(6\) modulo 7.

   ```lean
   example (n : ℕ) : 6 ^ n ≡ 1 [ZMOD 7] ∨ 6 ^ n ≡ 6 [ZMOD 7] := by
     sorry
   ```
5. Let \(n\) be a natural number. Show that \(4^n\) is congruent to \(1\), \(2\)
   or \(4\) modulo 7.

   ```lean
   example (n : ℕ) :
       4 ^ n ≡ 1 [ZMOD 7] ∨ 4 ^ n ≡ 2 [ZMOD 7] ∨ 4 ^ n ≡ 4 [ZMOD 7] := by
     sorry
   ```
6. Show that for all natural numbers \(n\) sufficiently large, \(3^n\geq 2^n+100\).

   ```lean
   example : forall_sufficiently_large n : ℕ, (3:ℤ) ^ n ≥ 2 ^ n + 100 := by
     dsimp
     sorry
   ```
7. Show that for all natural numbers \(n\) sufficiently large, \(2^n\geq n^2+4\).

   ```lean
   example : forall_sufficiently_large n : ℕ, 2 ^ n ≥ n ^ 2 + 4 := by
     dsimp
     sorry
   ```
8. Show that for all natural numbers \(n\) sufficiently large, \(2^n\geq n^3\).

   ```lean
   example : forall_sufficiently_large n : ℕ, 2 ^ n ≥ n ^ 3 := by
     dsimp
     sorry
   ```
9. Let \(a\) be an odd natural number. Show by induction that for all natural numbers
   \(n\), the natural number \(a^n\) is odd.

   Also deduce that for all natural numbers \(a\) and \(n\), if \(a^n\) is even then
   \(a\) is even. (This part is not an induction problem.)

   ```lean
   theorem Odd.pow {a : ℕ} (ha : Odd a) (n : ℕ) : Odd (a ^ n) := by
     sorry

   theorem Nat.even_of_pow_even {a n : ℕ} (ha : Even (a ^ n)) : Even a := by
     sorry
   ```

### 6.2. Recurrence relations

#### 6.2.1. Example

A *sequence* of numbers is an indexed list which goes on forever. Some sequences are defined by
closed formulas. For example, \(a\_n=2^n\) defines a sequence, the powers of two, with

\[\begin{split}a\_0&=1\\
a\_1&=2\\
a\_2&=4\\
a\_3&=8\\
a\_4&=16\\
a\_5&=32\\
\ldots\end{split}\]

In Lean, we would define this sequence as

```lean
def a (n : ℕ) : ℕ := 2 ^ n
```

and Lean will also calculate any term of the sequence we wish:

```lean
#eval a 20 -- infoview displays `1048576`
```

However, many important sequences do not have an easy closed formula. A more flexible way to define
sequences is with a *recursive definition*. For example, we can define a sequence \((b\_n)\)
recursively by,

> \[\begin{split}b\_0&=3 \\
> \text{for }n:\mathbb{N},\quad b\_{n+1} &= b\_n{}^2-2.\end{split}\]

The first few terms of this sequence are

\[\begin{split}b\_0&=3\\
b\_1&=b\_0{}^2-2\\
&=3^2-2\\
&=7\\
b\_2&=b\_1{}^2-2\\
&=7^2-2\\
&=47\\
b\_3&=b\_2{}^2-2\\
&=47^2-2\\
&=2207\\
\ldots\end{split}\]

In Lean, we would define this sequence as

```lean
def b : ℕ → ℤ
  | 0 => 3
  | n + 1 => b n ^ 2 - 2
```

and Lean will also calculate any term of the sequence we wish (up to the limits of its computational
power!):

```lean
#eval b 7 -- infoview displays `316837008400094222150776738483768236006420971486980607`
```

When a sequence is defined recursively, it is convenient to reason about it by induction.

> **Problem:**
>
> Show that for all \(n\), the integer \(b\_n\) is odd.

> **Solution:**
>
> We will prove this by induction on \(n\).
>
> First, note that
>
> \[\begin{split}b\_0 &= 3\\
> &=2\cdot 1+1,\\\end{split}\]
>
> so \(b\_0\) is odd.
>
> Now, let \(k\) be a natural number and suppose that \(b\_k\) is odd. Then there exists an
> integer \(x\) such that \(b\_k=2x+1\). We then have,
>
> \[\begin{split}b\_{k+1} &= b\_k{}^2-2\\
> &=(2x+1)^2-2\\
> &=2(2x^2+2x-1)+1,\end{split}\]
>
> so \(b\_{k+1}\) is also odd.

Here is that solution in Lean; note the use of `rw [b]` to unfold either piece of the recursive
definition of \(b\), as needed.

```lean
example (n : ℕ) : Odd (b n) := by
  simple_induction n with k hk
  · -- base case
    use 1
    calc b 0 = 3 := by rw [b]
      _ = 2 * 1 + 1 := by numbers
  · -- inductive step
    obtain ⟨x, hx⟩ := hk
    use 2 * x ^ 2 + 2 * x - 1
    calc b (k + 1) = b k ^ 2 - 2 := by rw [b]
      _ = (2 * x + 1) ^ 2 - 2 := by rw [hx]
      _ = 2 * (2 * x ^ 2 + 2 * x - 1) + 1 := by ring
```

You might like to try giving another proof using the modular-arithmetic characterization of parity;
this will work both in text and in Lean.

#### 6.2.2. Example

Here is another recursively defined sequence:

> \[\begin{split}x\_0&=5 \\
> \text{for }n:\mathbb{N},\quad x\_{n+1} &= 2x\_n-1.\end{split}\]

In Lean the definition looks like this:

```lean
def x : ℕ → ℤ
  | 0 => 5
  | n + 1 => 2 * x n - 1
```

Work out the first few terms of this sequence (or get Lean to do it for you!). Here is a property
we can prove about the sequence \((x\_n)\):

> **Problem:**
>
> Show that for all natural numbers \(n\), \(x\_n\equiv 1\mod 4\).

> **Solution:**
>
> We prove this by induction on \(n\).
>
> For the base case, observe that
>
> \[\begin{split}x\_0&=5\\
> &=4\cdot 1+1\\
> &\equiv 1 \mod 4.\end{split}\]
>
> For the inductive step, suppose that \(x\_k\equiv 1\mod 4\) for some natural number \(k\).
> Then
>
> \[\begin{split}x\_{k+1}&=2x\_k-1\\
> &\equiv 2 \cdot 1-1\mod 4\\
> &=1\end{split}\]
>
> also.

Write out the two parts of the proof of this statement in Lean.

```lean
example (n : ℕ) : x n ≡ 1 [ZMOD 4] := by
  simple_induction n with k IH
  · -- base case
    sorry
  · -- inductive step
    sorry
```

#### 6.2.3. Example

Sometimes, a sequence defined recursively can *also* be given a closed form expression. This is the
case for the sequence \((x\_n)\) from the previous problem.

> **Problem:**
>
> Show that for all natural numbers \(n\), \(x\_n=2^{n+2}+1\).

> **Solution:**
>
> We prove this by induction on \(n\).
>
> For the base case, note that as desired,
>
> \[\begin{split}x\_0&= 5\\
> &=2^{0+2}+1.\end{split}\]
>
> For the inductive step, suppose that \(x\_k=2^{k+2}+1\) for some natural number \(k\).
> Then
>
> \[\begin{split}x\_{k+1}&=2x\_k-1\\
> &=2(2^{k+2}+1)-1\\
> &=2^{(k+1)+2}+1.\end{split}\]

```lean
example (n : ℕ) : x n = 2 ^ (n + 2) + 1 := by
  simple_induction n with k IH
  · -- base case
    calc x 0 = 5 := by rw [x]
      _ = 2 ^ (0 + 2) + 1 := by numbers
  · -- inductive step
    calc x (k + 1) = 2 * x k - 1 := by rw [x]
      _ = 2 * (2 ^ (k + 2) + 1) - 1 := by rw [IH]
      _ = 2 ^ ((k + 1) + 2) + 1 := by ring
```

#### 6.2.4. Example

Here is one more recursively defined sequence:

> \[\begin{split}A\_0&=0 \\
> \text{for }n:\mathbb{N},\quad A\_{n+1} &= A\_n + (n + 1).\end{split}\]

```lean
def A : ℕ → ℚ
  | 0 => 0
  | n + 1 => A n + (n + 1)
```

Let’s work out the first terms of this sequence:

> \[\begin{split}A\_0&=0 \\
> A\_1&=A\_0+1 \\
> &=1\\
> A\_2&=A\_1+2 \\
> &=1+2\\
> &=3\\
> A\_3&=A\_2+3 \\
> &=3+3\\
> &=6\\
> A\_4&=A\_3+4 \\
> &=6+4\\
> &=10\\
> \ldots\end{split}\]

Note the pattern: first we added 1, then 2, then 3, then 4. So in fact

> \[\begin{split}A\_1&=1 \\
> A\_2&=1+2 \\
> A\_3&=1+2+3 \\
> A\_4&=1+2+3+4\\
> \ldots\end{split}\]

The term \(A\_n\) of the sequence represents the sum of the numbers from 1 to \(n\).

> **Problem:**
>
> Show that for all natural numbers \(n\),
>
> \[A\_n=\frac{n(n+1)}{2}.\]

> **Solution:**
>
> We prove this by induction on \(n\). First note that
>
> \[\begin{split}A\_0&=0\\
> &=\frac{0(0+1)}{2}.\end{split}\]
>
> This establishes the base case. Now, let \(k\) be a natural number and suppose that
> \(A\_k=\frac{k(k+1)}{2}\). We then have
>
> \[\begin{split}A\_{k+1}&=A\_k+(k+1)\\
> &=\frac{k(k+1)}{2}+(k+1)\\
> &=\frac{k(k+1)+2(k+1)}{2}\\
> &=\frac{(k+1)\cdot[(k+1)+1]}{2}.\end{split}\]

Here it is in Lean, written with one fewer step because Lean is better at algebra than humans are.

```lean
example (n : ℕ) : A n = n * (n + 1) / 2 := by
  simple_induction n with k IH
  · -- base case
    calc A 0 = 0 := by rw [A]
      _ = 0 * (0 + 1) / 2 := by numbers
  · -- inductive step
    calc
      A (k + 1) = A k + (k + 1) := by rw [A]
      _ = k * (k + 1) / 2 + (k + 1) := by rw [IH]
      _ = (k + 1) * (k + 1 + 1) / 2 := by ring
```

#### 6.2.5. Example

We built the previous sequence by adding 1, then 2, then 3, etc. What if we do the same thing but
for multiplication? This gives the so-called *factorial* function, with “\(n\) factorial”
denoted \(n!\).

> \[\begin{split}0!&=1 \\
> \text{for }n:\mathbb{N},\quad(n+1)! &= (n + 1) ⬝ n!\end{split}\]

```lean
def factorial : ℕ → ℕ
  | 0 => 1
  | n + 1 => (n + 1) * factorial n

notation:10000 n "!" => factorial n
```

So

> \[\begin{split}1!&=1 \\
> 2!&=2\cdot 1 \\
> 3!&=3\cdot 2\cdot 1 \\
> 4!&=4\cdot 3\cdot 2\cdot 1.\\
> \ldots\end{split}\]

Concretely,

> \[\begin{split}0!&=1 \\
> 1!&=1\cdot A\_0 \\
> &=1\cdot 1 \\
> &=1\\
> 2!&=2\cdot A\_1 \\
> &=2\cdot 1 \\
> &=2\\
> 3!&=3\cdot A\_2 \\
> &=3\cdot 2 \\
> &=6\\
> 4!&=4\cdot A\_3 \\
> &=4\cdot 6 \\
> &=24\\
> \ldots\end{split}\]

> **Problem:**
>
> Let \(n\) be a natural number. Show that every natural number \(d\) with
> \(1\le d\le n\) is a factor of \(n!\).

> **Solution:**
>
> We prove this by induction on \(n\). For the base case, \(n=0\), the statement is
> vacuous, since there is no natural number \(d\) with \(1\le d\le 0\).
>
> Let \(k\) be a natural number and suppose that every natural number \(d\) with
> \(1\le d\le k\) is a factor of \(k!\). Now let \(d\) be a natural number with
> \(1\le d\le k+1\). We must show that \(d\) is a factor of \((k+1)!\).
>
> **Case 1** (\(d=k+1\)): We have that
>
> \[\begin{split}(k+1)!&=(k+1)\cdot k!\\
> &=d\cdot k!,\end{split}\]
>
> so \(d\) is a factor of \((k+1)!\).
>
> **Case 1** (\(d<k+1\)): Then \(d\le k\), so by the inductive hypothesis \(d\) is a
> factor of \(k!\). Therefore there exists a natural number \(x\) such that \(k!=dx\).
> We then have
>
> \[\begin{split}(k+1)!&=(k+1)\cdot k!\\
> &=(k+1)\cdot dx\\
> &=d\cdot (k+1)x,\end{split}\]
>
> so \(d\) is a factor of \((k+1)!\).

Here is the same proof in Lean. We record it for future use under the name `dvd_factorial`.

```lean
example (n : ℕ) : ∀ d, 1 ≤ d → d ≤ n → d ∣ n ! := by
  simple_induction n with k IH
  · -- base case
    intro k hk1 hk
    interval_cases k
  · -- inductive step
    intro d hk1 hk
    obtain hk | hk : d = k + 1 ∨ d < k + 1 := eq_or_lt_of_le hk
    · -- case 1: `d = k + 1`
      sorry
    · -- case 2: `d < k + 1`
      sorry
```

#### 6.2.6. Example

> **Problem:**
>
> Show that for all natural numbers \(n\), we have \((n+1)!\ge 2^n\).

> **Solution:**
>
> We prove this by induction on \(n\).
>
> For the base case,
>
> \[\begin{split}(0+1)!&=(0+1)\cdot 0!\\
> &= (0+1)\cdot 1\\
> &\ge 2 ^ 0.\end{split}\]
>
> For the inductive step, suppose that for some natural number \(k\), \((k+1)!\ge 2^k\).
> Then
>
> \[\begin{split}(k+1+1)!&=(k+1+1)\cdot (k+1)!\\
> &\ge(k+1+1)\cdot 2^k\\
> &= k\cdot 2^k+2\cdot 2^k\\
> &\ge 2\cdot 2^k\\
> &=2 ^ {k+1}.\end{split}\]

```lean
example (n : ℕ) : (n + 1)! ≥ 2 ^ n := by
  sorry
```

#### 6.2.7. Exercises

1. Consider the sequence \((c\_n)\) defined recursively by,

   \[\begin{split}c\_0&=7 \\
   \text{for }n:\mathbb{N},\quad c\_{n+1} &= 3c\_n-10.\end{split}\]

   Show that for all natural numbers \(n\), the integer \(c\_n\) is odd.

   ```lean
   def c : ℕ → ℤ
     | 0 => 7
     | n + 1 => 3 * c n - 10

   example (n : ℕ) : Odd (c n) := by
     sorry
   ```
2. Let the sequence \((c\_n)\) be defined as in the previous problem. Show that for all
   \(n\), \(c\_n=2\cdot 3^n+5\).

   ```lean
   example (n : ℕ) : c n = 2 * 3 ^ n + 5 := by
     sorry
   ```
3. Consider the sequence \((y\_n)\) defined recursively by,

   \[\begin{split}y\_0&=2 \\
   \text{for }n:\mathbb{N},\quad y\_{n+1} &= y\_n{}^2.\end{split}\]

   Show that for all natural numbers \(n\), \(y\_n=2^{2^n}\).

   ```lean
   def y : ℕ → ℕ
     | 0 => 2
     | n + 1 => (y n) ^ 2

   example (n : ℕ) : y n = 2 ^ (2 ^ n) := by
     sorry
   ```
4. Consider the sequence \((B\_n)\) defined recursively by,

   \[\begin{split}B\_0&=0 \\
   \text{for }n:\mathbb{N},\quad B\_{n+1} &= B\_n+(n+1)^2.\end{split}\]

   Thus \(B\_n\) represents the sum \(1^2+2^2+3^2+\cdots+n^2\).
   Show that for all natural numbers \(n\),

   \[B\_n=\frac{n(n+1)(2n+1)}{6}.\]

   ```lean
   def B : ℕ → ℚ
     | 0 => 0
     | n + 1 => B n + (n + 1 : ℚ) ^ 2

   example (n : ℕ) : B n = n * (n + 1) * (2 * n + 1) / 6 := by
     sorry
   ```
5. Consider the sequence \((S\_n)\) defined recursively by,

   \[\begin{split}S\_0&=1 \\
   \text{for }n:\mathbb{N},\quad S\_{n+1} &= S\_n+\frac{1}{2^{n+1}}.\end{split}\]

   Thus \(S\_n\) represents the sum \(1+\frac{1}{2}+\frac{1}{4}+\cdots+\frac{1}{2^n}\).
   Show that for all natural numbers \(n\),

   \[S\_n=2-\frac{1}{2^n}.\]

   ```lean
   def S : ℕ → ℚ
     | 0 => 1
     | n + 1 => S n + 1 / 2 ^ (n + 1)

   example (n : ℕ) : S n = 2 - 1 / 2 ^ n := by
     sorry
   ```
6. Show that \(n!\) is strictly positive, for all natural numbers \(n\).

   We record this for future use under the name `factorial_pos`.

   ```lean
   example (n : ℕ) : 0 < n ! := by
     sorry
   ```
7. Show that \(n!\) is even for all \(n\geq 2\). Use induction from the starting point 2
   (see Example 6.1.5).

   ```lean
   example {n : ℕ} (hn : 2 ≤ n) : Nat.Even (n !) := by
     sorry
   ```
8. Show that for all natural numbers \(n\), \((n+1)!\le (n+1)^n\).

   (Compare with Example 6.2.6.)

   ```lean
   example (n : ℕ) : (n + 1) ! ≤ (n + 1) ^ n := by
     sorry
   ```

### 6.3. Two-step induction

#### 6.3.1. Example

In the last section we studied recursively defined sequences in which each term is constructed from
the previous term. But it is also possible to define a sequence recursively with reliance on several
previous terms.

For example, here is a sequence defined recursively in terms of the two previous terms.

\[\begin{split}a\_0&=2\\
a\_1&=1\\
\text{for }n:\mathbb{N},\quad a\_{n+2}&=a\_{n+1}+2a\_n.\end{split}\]

Notice that since the recurrence relation depends on two previous terms, we need to give two
concrete values of the sequence (\(a\_0=2\) and \(a\_1=1\)) to start with.

The first few terms of this sequence are

\[\begin{split}a\_0&=2\\
a\_1&=1\\
a\_2&=a\_1+2a\_0\\
&=1+2\cdot 2\\
&=5\\
a\_3&=a\_2+2a\_1\\
&=5+2\cdot 1\\
&=7\\
a\_4&=a\_3+2a\_2\\
&=7+2\cdot 5\\
&=17\end{split}\]

In Lean, we would define this sequence as

```lean
def a : ℕ → ℤ
  | 0 => 2
  | 1 => 1
  | n + 2 => a (n + 1) + 2 * a n
```

and, as in the previous section, Lean will calculate for us any term of the sequence:

```lean
#eval a 5 -- infoview displays `31`
```

Compute several more terms of the sequence, either on paper or with Lean’s help. You will start to
see a pattern: every term of the sequence differs by one from a power of two. We can prove this
pattern by induction.

> **Problem:**
>
> Prove that for all natural numbers \(n\), \(a\_n=2^n+(-1)^n\).

In the proof below, notice that there are *two* base cases and *two* inductive hypotheses. Think
about why.

> **Solution:**
>
> We prove this by induction on \(n\).
>
> We have that
>
> \[\begin{split}a\_0&=2\\
> &=2^0+(-1)^0\end{split}\]
>
> and
>
> \[\begin{split}a\_1&=1\\
> &=2^1+(-1)^1.\end{split}\]
>
> Now let \(k\) be a natural number and suppose that \(a\_k=2^k+(-1)^k\) and
> \(a\_{k+1}=2^{k+1}+(-1)^{k+1}\). Then
>
> \[\begin{split}a\_{k+2}&=a\_{k+1}+2a\_k\\
> &=(2^{k+1}+(-1)^{k+1})+2(2^k+(-1)^k)\\
> &=(2\cdot 2^{k}-(-1)^{k})+(2\cdot 2^k+2\cdot (-1)^k)\\
> &=2^2\cdot 2^{k}+(-1)^2\cdot (-1)^{k}\\
> &=2^{k+2}+(-1)^{k+2}.\end{split}\]

The first two steps (using the recurrence relation and the inductive hypotheses) of the main
calculation are fairly fixed, but you might have more or fewer lines of endgame depending on your
facility with exponent rules and mental arithmetic. Lean can do it all in one line!

We use the Lean tactic `two_step_induction` for this kind of induction with two base cases and two
inductive hypotheses.

```lean
example (n : ℕ) : a n = 2 ^ n + (-1) ^ n := by
  two_step_induction n with k IH1 IH2
  . calc a 0 = 2 := by rw [a]
      _ = 2 ^ 0 + (-1) ^ 0 := by numbers
  . calc a 1 = 1 := by rw [a]
      _ = 2 ^ 1 + (-1) ^ 1 := by numbers
  calc
    a (k + 2)
      = a (k + 1) + 2 * a k := by rw [a]
    _ = (2 ^ (k + 1) + (-1) ^ (k + 1)) + 2 * (2 ^ k + (-1) ^ k) := by rw [IH1, IH2]
    _ = (2 : ℤ) ^ (k + 2) + (-1) ^ (k + 2) := by ring
```

#### 6.3.2. Example

> **Problem:**
>
> Prove that for all natural numbers \(m\geq 1\), \(a\_m\) is congruent to either 1 or 5
> modulo 6.

> **Solution:**
>
> We will prove a more precise result than stated, namely that for all natural numbers
> \(n\geq 1\), either
>
> - \(a\_n\equiv 1\mod 6\) and \(a\_{n+1}\equiv 5\mod 6\), or
> - \(a\_n\equiv 5\mod 6\) and \(a\_{n+1}\equiv 1\mod 6\).
>
> We prove this by induction on \(n\).
>
> For the base case, \(n=1\), notice that \(a\_1=1\) and \(a\_2=5\), so
> \(a\_1\equiv 1\mod 6\) and \(a\_2\equiv 5\mod 6\).
>
> For the inductive step, let \(k\) be a natural number and suppose that either
>
> - \(a\_k\equiv 1\mod 6\) and \(a\_{k+1}\equiv 5\mod 6\), or
> - \(a\_k\equiv 5\mod 6\) and \(a\_{k+1}\equiv 1\mod 6\).
>
> **Case 1** (\(a\_k\equiv 1\mod 6\) and \(a\_{k+1}\equiv 5\mod 6\)): Then
>
> \[\begin{split}a\_{k+2}&=a\_{k+1}+2a\_k\\
> &\equiv 5+2\cdot 1\mod 6\\
> &=6\cdot 1+1\\
> &\equiv 1\mod 6.\end{split}\]
>
> **Case 2** (\(a\_k\equiv 5\mod 6\) and \(a\_{k+1}\equiv 1\mod 6\)): Then
>
> \[\begin{split}a\_{k+2}&=a\_{k+1}+2a\_k\\
> &\equiv 1+2\cdot 5\mod 6\\
> &=6\cdot 1+5\\
> &\equiv 5\mod 6.\end{split}\]

It may not be clear to you exactly why these calculations are what’s needed to solve the problem.
There is also quite a bit of low-level logical manipulation happening, without being called out
directly in the text. The Lean proof below may make some of these logical manipulations more
apparent to you.

```lean
example {m : ℕ} (hm : 1 ≤ m) : a m ≡ 1 [ZMOD 6] ∨ a m ≡ 5 [ZMOD 6] := by
  have H : ∀ n : ℕ, 1 ≤ n →
      (a n ≡ 1 [ZMOD 6] ∧ a (n + 1) ≡ 5 [ZMOD 6])
    ∨ (a n ≡ 5 [ZMOD 6] ∧ a (n + 1) ≡ 1 [ZMOD 6])
  · intro n hn
    induction_from_starting_point n, hn with k hk IH
    · left
      constructor
      calc a 1 = 1 := by rw [a]
        _ ≡ 1 [ZMOD 6] := by extra
      calc a (1 + 1) = 1 + 2 * 2 := by rw [a, a, a]
        _ = 5 := by numbers
        _ ≡ 5 [ZMOD 6] := by extra
    · obtain ⟨IH1, IH2⟩ | ⟨IH1, IH2⟩ := IH
      · right
        constructor
        · apply IH2
        calc a (k + 1 + 1) = a (k + 1) + 2 * a k := by rw [a]
          _ ≡ 5 + 2 * 1 [ZMOD 6] := by rel [IH1, IH2]
          _ = 6 * 1 + 1 := by numbers
          _ ≡ 1 [ZMOD 6] := by extra
      · left
        constructor
        · apply IH2
        calc a (k + 1 + 1) = a (k + 1) + 2 * a k := by rw [a]
          _ ≡ 1 + 2 * 5 [ZMOD 6] := by rel [IH1, IH2]
          _ = 6 * 1 + 5 := by numbers
          _ ≡ 5 [ZMOD 6] := by extra
  obtain ⟨H1, H2⟩ | ⟨H1, H2⟩ := H m hm
  · left
    apply H1
  · right
    apply H1
```

#### 6.3.3. Example

The most famous example of a sequence defined recursively in terms of its two previous values is the
*Fibonacci sequence*: each term is the sum of the two previous.

\[\begin{split}F\_0&=1\\
F\_1&=1\\
\text{for }n:\mathbb{N},\quad F\_{n+2}&=F\_{n+1}+F\_n.\end{split}\]

```lean
def F : ℕ → ℤ
  | 0 => 1
  | 1 => 1
  | n + 2 => F (n + 1) + F n
```

Work out the first 10 terms, on paper or with Lean’s help.

> **Problem:**
>
> Show that the Fibonacci sequence \((F\_n)\) satisfies: for all natural numbers \(n\),
> \(F\_n \le 2^n\).

> **Solution:**
>
> We prove this by induction on \(n\).
>
> For \(n=0\), we have that
>
> \[\begin{split}F\_0&=1\\
> &\le 2^0.\end{split}\]
>
> For \(n=1\), we have that
>
> \[\begin{split}F\_1&=1\\
> &\le 2^1.\end{split}\]
>
> Let \(k\) be a natural number and suppose that \(F\_k\le 2^k\) and
> \(F\_{k+1}\le 2^{k+1}\). Then
>
> \[\begin{split}F\_{k+2}&=F\_{k+1}+F\_k\\
> &\le 2^{k+1}+2^k\\
> &\le 2^{k+1}+2^k+2^k\\
> &= 2^{k+1}+2^{k+1}\\
> &= 2^{k+2}.\end{split}\]

```lean
example (n : ℕ) : F n ≤ 2 ^ n := by
  two_step_induction n with k IH1 IH2
  · calc F 0 = 1 := by rw [F]
      _ ≤ 2 ^ 0 := by numbers
  · calc F 1 = 1 := by rw [F]
      _ ≤ 2 ^ 1 := by numbers
  · calc F (k + 2) = F (k + 1) + F k := by rw [F]
      _ ≤ 2 ^ (k + 1) + 2 ^ k := by rel [IH1, IH2]
      _ ≤ 2 ^ (k + 1) + 2 ^ k + 2 ^ k := by extra
      _ = 2 ^ (k + 2) := by ring
```

#### 6.3.4. Example

> **Problem:**
>
> Show 1 that the Fibonacci sequence \((F\_n)\) satisfies: for all natural numbers \(n\),
>
> \[F\_{n+1}^2-F\_{n+1}F\_n-F\_n^2=-(-1)^n.\]

> **Solution:**
>
> We prove this by induction on \(n\). First,
>
> \[\begin{split}F\_1^2-F\_1F\_0-F\_0^2&=1^2-1\cdot 1-1^2\\
> &=-(-1)^0.\end{split}\]
>
> Now, let \(k\) be a natural number and suppose that
> \(F\_{k+1}^2-F\_{k+1}F\_k-F\_k^2=-(-1)^k\). Then
>
> \[\begin{split}F\_{k+2}^2-F\_{k+2}F\_{k+1}-F\_{k+1}^2
> &=(F\_{k+1}+F\_k)^2-(F\_{k+1}+F\_k)F\_{k+1}-F\_{k+1}^2\\
> &=-(F\_{k+1}^2-F\_{k+1}F\_k-F\_k^2)\\
> &=-(-(-1)^k)\\
> &=-(-1)^{k+1}.\end{split}\]

```lean
example (n : ℕ) : F (n + 1) ^ 2 - F (n + 1) * F n - F n ^ 2 = - (-1) ^ n := by
  simple_induction n with k IH
  · calc F 1 ^ 2 - F 1 * F 0 - F 0 ^ 2 = 1 ^ 2 - 1 * 1 - 1 ^ 2 := by rw [F, F]
      _ = - (-1) ^ 0 := by numbers
  · calc F (k + 2) ^ 2 - F (k + 2) * F (k + 1) - F (k + 1) ^ 2
        = (F (k + 1) + F k) ^ 2 - (F (k + 1) + F k) * F (k + 1)
            - F (k + 1) ^ 2 := by rw [F]
      _ = - (F (k + 1) ^ 2 - F (k + 1) * F k - F k ^ 2) := by ring
      _ = - -(-1) ^ k := by rw [IH]
      _ = -(-1) ^ (k + 1) := by ring
```

#### 6.3.5. Example

We have so far seen simple induction, induction from a specified starting point, and two-step
induction. It may not surprise you to learn that it is also valid to perform two-step induction from
a specified starting point.

Consider the sequence \((d\_n)\) defined recursively by,

> \[\begin{split}d\_0&=3\\
> d\_1&=1\\
> \text{for }n:\mathbb{N},\quad d\_{n+2}&=3d\_{n+1}+5d\_n.\end{split}\]

```lean
def d : ℕ → ℤ
  | 0 => 3
  | 1 => 1
  | k + 2 => 3 * d (k + 1) + 5 * d k
```

> **Problem:**
>
> Show that for all sufficiently large natural numbers \(n\), \(d\_n \ge 4^n\).

To start this problem, you might experiment by calculating the first few terms, by hand or in Lean.

```lean
#eval d 2 -- infoview displays `18`
#eval d 3 -- infoview displays `59`
#eval d 4 -- infoview displays `267`
#eval d 5 -- infoview displays `1096`
#eval d 6 -- infoview displays `4623`
#eval d 7 -- infoview displays `19349`
```

Likewise, you can calculate the first few powers of 4, by hand or in Lean.

```lean
#eval 4 ^ 2 -- infoview displays `16`
#eval 4 ^ 3 -- infoview displays `64`
#eval 4 ^ 4 -- infoview displays `256`
#eval 4 ^ 5 -- infoview displays `1024`
#eval 4 ^ 6 -- infoview displays `4096`
#eval 4 ^ 7 -- infoview displays `16384`
```

Based on this limited sample, it looks like \(d\_n\) overtakes \(4^n\) at \(n=4\). So
let’s try an induction starting at 4.

> **Solution:**
>
> We will show this for all natural numbers \(n\geq 4\).
>
> For \(n=4\), we have that
>
> \[\begin{split}d\_4&=267\\
> &\ge 4^4.\end{split}\]
>
> For \(n=5\), we have that
>
> \[\begin{split}d\_5&=1096\\
> &\ge 4^5.\end{split}\]
>
> Let \(k\) be a natural number and suppose that \(d\_k\ge 4^k\) and
> \(d\_{k+1}\ge 4^{k+1}\). Then we have that
>
> \[\begin{split}d\_{k+2}&=3d\_{k+1}+5d\_k\\
> &\ge 3 \cdot 4^{k+1}+5 \cdot 4^k\\
> &= 16 \cdot 4^k + 4 ^ k\\
> &\geq 16 \cdot 4^k\\
> &= 4^{k+2}.\end{split}\]

In Lean, we can use the tactic `two_step_induction_from_starting_point` for this style of
argument.

```lean
example : forall_sufficiently_large n : ℕ, d n ≥ 4 ^ n := by
  dsimp
  use 4
  intro n hn
  two_step_induction_from_starting_point n, hn with k hk IH1 IH2
  · calc d 4 = 267 := by rfl
      _ ≥ 4 ^ 4 := by numbers
  · calc d 5 = 1096 := by rfl
      _ ≥ 4 ^ 5 := by numbers
  calc d (k + 2) = 3 * d (k + 1) + 5 * d k := by rw [d]
    _ ≥ 3 * 4 ^ (k + 1) + 5 * 4 ^ k := by rel [IH1, IH2]
    _ = 16 * 4 ^ k + 4 ^ k := by ring
    _ ≥ 16 * 4 ^ k := by extra
    _ = 4 ^ (k + 2) := by ring
```

#### 6.3.6. Exercises

1. Consider the sequence \((b\_n)\) defined recursively by,

   \[\begin{split}b\_0&=0\\
   b\_1&=1\\
   \text{for }n:\mathbb{N},\quad b\_{n+2}&=5b\_{n+1}-6b\_n.\end{split}\]

   Prove that for all natural numbers \(n\), \(b\_n=3^n - 2 ^ n\).

   ```lean
   def b : ℕ → ℤ
     | 0 => 0
     | 1 => 1
     | n + 2 => 5 * b (n + 1) - 6 * b n

   example (n : ℕ) : b n = 3 ^ n - 2 ^ n := by
     sorry
   ```
2. Consider the sequence \((c\_n)\) defined recursively by,

   \[\begin{split}c\_0&=3\\
   c\_1&=2\\
   \text{for }n:\mathbb{N},\quad c\_{n+2}&=4c\_n.\end{split}\]

   Prove that for all natural numbers \(n\), \(c\_n=2\cdot 2^n+(-2)^n\).

   ```lean
   def c : ℕ → ℤ
     | 0 => 3
     | 1 => 2
     | n + 2 => 4 * c n

   example (n : ℕ) : c n = 2 * 2 ^ n + (-2) ^ n := by
     sorry
   ```
3. Consider the sequence \((t\_n)\) defined recursively by,

   \[\begin{split}t\_0&=5\\
   t\_1&=7\\
   \text{for }n:\mathbb{N},\quad t\_{n+2}&=2t\_{n+1}-t\_n.\end{split}\]

   Prove that for all natural numbers \(n\), \(t\_n=2n+5\).

   ```lean
   def t : ℕ → ℤ
     | 0 => 5
     | 1 => 7
     | n + 2 => 2 * t (n + 1) - t n

   example (n : ℕ) : t n = 2 * n + 5 := by
     sorry
   ```
4. Consider the sequence \((q\_n)\) defined recursively by,

   \[\begin{split}q\_0&=1\\
   q\_1&=2\\
   \text{for }n:\mathbb{N},\quad q\_{n+2}&=2q\_{n+1}-q\_n+6n + 6.\end{split}\]

   Prove that for all natural numbers \(n\), \(q\_n=n^3+1\).

   ```lean
   def q : ℕ → ℤ
     | 0 => 1
     | 1 => 2
     | n + 2 => 2 * q (n + 1) - q n + 6 * n + 6

   example (n : ℕ) : q n = (n:ℤ) ^ 3 + 1 := by
     sorry
   ```
5. Consider the sequence \((s\_n)\) defined recursively by,

   \[\begin{split}s\_0&=2\\
   s\_1&=3\\
   \text{for }n:\mathbb{N},\quad s\_{n+2}&=2s\_{n+1}+3s\_n.\end{split}\]

   Prove that for all natural numbers \(m\), \(s\_m\) is congruent to either 2 or 3
   modulo 5.

   ```lean
   def s : ℕ → ℤ
     | 0 => 2
     | 1 => 3
     | n + 2 => 2 * s (n + 1) + 3 * s n

   example (m : ℕ) : s m ≡ 2 [ZMOD 5] ∨ s m ≡ 3 [ZMOD 5] := by
     sorry
   ```
6. Consider the sequence \((p\_n)\) defined recursively by,

   \[\begin{split}p\_0&=2\\
   p\_1&=3\\
   \text{for }n:\mathbb{N},\quad p\_{n+2}&=6p\_{n+1}-p\_n.\end{split}\]

   Prove that for all natural numbers \(m\geq 1\), \(p\_m\) is congruent to either 2 or 3
   modulo 7.

   ```lean
   def p : ℕ → ℤ
     | 0 => 2
     | 1 => 3
     | n + 2 => 6 * p (n + 1) - p n

   example (m : ℕ) : p m ≡ 2 [ZMOD 7] ∨ p m ≡ 3 [ZMOD 7] := by
     sorry
   ```
7. Consider the sequence \((r\_n)\) defined recursively by,

   \[\begin{split}r\_0&=2\\
   r\_1&=0\\
   \text{for }n:\mathbb{N},\quad r\_{n+2}&=2r\_{n+1}+r\_n.\end{split}\]

   Prove that for all sufficiently large natural numbers \(n\), it is true that
   \(r\_n\geq 2^n\).

   ```lean
   def r : ℕ → ℤ
     | 0 => 2
     | 1 => 0
     | n + 2 => 2 * r (n + 1) + r n

   example : forall_sufficiently_large n : ℕ, r n ≥ 2 ^ n := by
     sorry
   ```
8. Show that the Fibonacci sequence \((F\_n)\) satisfies: for all sufficiently large natural
   numbers \(n\), \(0.4 \cdot 1.6^n < F\_n < 0.5 \cdot 1.7^n\).

   ```lean
   example : forall_sufficiently_large n : ℕ,
       (0.4:ℚ) * 1.6 ^ n < F n ∧ F n < (0.5:ℚ) * 1.7 ^ n := by
     sorry
   ```

Footnotes

1
:   Example adapted from Hammack,
    [Book of Proof](https://www.people.vcu.edu/~rhammack/BookOfProof/), Section 10.5.

### 6.4. Strong induction

#### 6.4.1. Example

We have been encountering increasingly complicated induction principles, starting from “simple
induction” in Example 6.1.1 and eventually reaching rather niche
induction principles like “two-step induction from a specified starting point” in
Example 6.3.5. Rather than develop more and more exotic induction
principles as our problems become yet more complicated, let’s explain a more general method: *strong
induction*. This method lets us prove a proposition for the natural numbers one by one, relying at
each step not just on the immediately previous step, but on *any* previous step.

Let’s re-do Example 6.3.3 using strong induction. The differences
will be more apparent in Lean, but we start with a text proof, where the difference amounts to a
change of emphasis.

> **Problem:**
>
> Show that for all natural numbers \(n\), \(F\_n \le 2^n\).

> **Solution:**
>
> We prove this by strong induction on \(n\). Let \(n\) be a natural number and suppose that
> for all natural numbers \(m < n\), \(F\_m \le 2^m\). \((\star)\)
>
> We consider cases according to whether \(n\) is 0, 1, or \(k + 2\) for some
> natural number \(k\).
>
> For \(n=0\), we have that
>
> \[\begin{split}F\_0&=1\\
> &\le 2^0.\end{split}\]
>
> For \(n=1\), we have that
>
> \[\begin{split}F\_1&=1\\
> &\le 2^1.\end{split}\]
>
> For \(n=k+2\), we have that \(k<k+2\) and \(k+1<k+2\), so by the inductive hypothesis
> \((\star)\), \(F\_k\le 2^k\) and \(F\_{k+1}\le 2^{k+1}\). Thus
>
> \[\begin{split}F\_{k+2}&=F\_{k+1}+F\_k\\
> &\le 2^{k+1}+2^k\\
> &\le 2^{k+1}+2^k+2^k\\
> &= 2^{k+1}+2^{k+1}\\
> &= 2^{k+2}.\end{split}\]

In Lean, strong induction can be used in a proof almost silently. We set up a theorem
stating the result we want to prove by strong induction (here the statement

> for all natural numbers \(n\), \(F\_n \le 2^n\)

which I have named `F_bound` in Lean). Then within the proof of that theorem we can refer to the
theorem itself! Lean will attempt to check for us that we are using the theorem only for input
values smaller than the value currently being studied.

You might find this suspicious, or in danger of becoming circular. Check for yourself that if you
try to invoke the lemma `F_bound` at the value \(n\) itself, or at a larger value like
\(n + 17\), then Lean gives an error.

```lean
theorem F_bound (n : ℕ) : F n ≤ 2 ^ n := by
  match n with
  | 0 =>
      calc F 0 = 1 := by rw [F]
        _ ≤ 2 ^ 0 := by numbers
  | 1 =>
      calc F 1 = 1 := by rw [F]
        _ ≤ 2 ^ 1 := by numbers
  | k + 2  =>
      have IH1 := F_bound k -- first inductive hypothesis
      have IH2 := F_bound (k + 1) -- second inductive hypothesis
      calc F (k + 2) = F (k + 1) + F k := by rw [F]
        _ ≤ 2 ^ (k + 1) + 2 ^ k := by rel [IH1, IH2]
        _ ≤ 2 ^ (k + 1) + 2 ^ k + 2 ^ k := by extra
        _ = 2 ^ (k + 2) := by ring
```

#### 6.4.2. Example

> **Theorem:**
>
> Let \(n \geq 2\) be a natural number. Then there exists a prime number \(p\) which is a
> factor of \(n\).

> **Proof:**
>
> We prove this by strong induction on \(n\). Let \(n\) be a natural number and suppose that
> for all natural numbers \(2 \le m < n\), there exists a prime number \(p\) which is a
> factor of \(m\). \((\star)\)
>
> If \(n\) is prime, then \(n\) itself is a prime factor of \(n\), and we are done.
>
> If \(n\) is not prime, then since \(n \geq 2\), there exists a natural number
> \(2 \le m < n\) which is a factor of \(n\). (This was proved in one of the exercises in
> [Section 5.3]](#m2001-negation-normalize).) By the inductive hypothesis \((\star)\), there
> exists a prime number \(p\) which is a factor of \(m\).
>
> Since \(m\mid n\), there exists a natural number \(x\) such that \(n = mx\). Since
> \(p \mid m\), there exists a natural number \(y\) such that \(m = py\). Then
>
> \[\begin{split}n &=mx\\
> &=(py)x\\
> &=p(xy),\end{split}\]
>
> so \(p\) is a factor of \(n\) too.

Here is the same proof in Lean. The lemma from the exercise in
[Section 5.3]](#m2001-negation-normalize) has the Lean name `exists_factor_of_not_prime`.

Notice that this is again a strong induction proof: we are invoking the theorem (which we name
`exists_prime_factor`), instantiated for \(m\), within the proof of the theorem instantiated
for \(n\). At that point Lean has a hypothesis available saying that \(m<n\), so this is
valid.

```lean
theorem exists_prime_factor {n : ℕ} (hn2 : 2 ≤ n) : ∃ p : ℕ, Prime p ∧ p ∣ n := by
  by_cases hn : Prime n
  . -- case 1: `n` is prime
    use n
    constructor
    · apply hn
    · use 1
      ring
  . -- case 2: `n` is not prime
    obtain ⟨m, hmn, _, ⟨x, hx⟩⟩ := exists_factor_of_not_prime hn hn2
    have IH : ∃ p, Prime p ∧ p ∣ m := exists_prime_factor hmn -- inductive hypothesis
    obtain ⟨p, hp, y, hy⟩ := IH
    use p
    constructor
    · apply hp
    · use x * y
      calc n = m * x := hx
        _ = (p * y) * x := by rw [hy]
        _ = p * (x * y) := by ring
```

#### 6.4.3. Exercises

1. Show that for all natural numbers \(n>0\), there exist natural numbers \(a\) and
   \(x\), with \(x\) odd, such that \(n=2^ax\).

   Suggested approach: start with a case split on the parity of \(n\), using the lemma
   `even_or_odd`.

   ```lean
   theorem extract_pow_two (n : ℕ) (hn : 0 < n) : ∃ a x, Odd x ∧ n = 2 ^ a * x := by
     sorry
   ```

### 6.5. Pascal’s triangle

#### 6.5.1. Definition

Consider the family of natural numbers \((P\_{a,b})\) defined recursively by,

> \[\begin{split}\text{for }a:\mathbb{N},\quad P\_{a,0}&=1 \\
> \text{for }b:\mathbb{N},\quad P\_{0,b+1}&=1 \\
> \text{for }a,b:\mathbb{N},\quad P\_{a+1,b+1} &= P\_{a+1,b}+P\_{a,b+1}.\end{split}\]

This definition is *well-founded* because each step of the definition depends only on previous terms
\(P\_{a,b}\) for which the expression \(a+b\) is strictly smaller.

Here is how this looks in Lean, with the well-foundedness explanation expressed using the syntax
`termination_by`.

```lean
def pascal : ℕ → ℕ → ℕ
  | a, 0 => 1
  | 0, b + 1 => 1
  | a + 1, b + 1 => pascal (a + 1) b + pascal a (b + 1)
termination_by _ a b => a + b
```

As usual, Lean can work out any value of the function we ask for. For example,

```lean
#eval pascal 2 4 -- infoview displays `15`
```

Here are all the values for \(a\) and \(b\) from 0 to 5.

Table 6.1 Values of the recursively defined function `pascal`









|  | 0 | 1 | 2 | 3 | 4 | 5 |
| --- | --- | --- | --- | --- | --- | --- |
| 0 | 1 | 1 | 1 | 1 | 1 | 1 |
| 1 | 1 | 2 | 3 | 4 | 5 | 6 |
| 2 | 1 | 3 | 6 | 10 | 15 | 21 |
| 3 | 1 | 4 | 10 | 20 | 35 | 56 |
| 4 | 1 | 5 | 15 | 35 | 70 | 126 |
| 5 | 1 | 6 | 21 | 56 | 126 | 252 |

Check your understanding of the definition by re-calculating a few of these values from scratch.

The conventional visualization of the function `pascal` is in a rotated version of the above
table, as a triangle.

![_images/pascal.gif](https://hrmacbeth.github.io/math2001/_images/pascal.gif)



Fig. 6.1 Pascal’s triangle (image credit:
[Wikimedia Commons](https://commons.wikimedia.org/wiki/File:PascalTriangleAnimated2.gif))

#### 6.5.2. Example

> **Theorem:**
>
> For all natural numbers \(a\) and \(b\), \(P\_{a,b} \le (a+b)!\).

> **Proof:**
>
> We prove this by strong induction relative to the expression \(a+b\).
>
> By an exercise in Section 6.2, factorials are always \(\geq 1\), so
> for all \(a\),
>
> \[\begin{split}P\_{a,0}&=1\\
> &\le (a+0)!,\end{split}\]
>
> and for all \(b\),
>
> \[\begin{split}P\_{0,b+1}&=1\\
> &\le (0+(b+1))!.\end{split}\]
>
> Now let \(a\) and \(b\) be natural numbers and suppose that for all \(x\) and
> \(y\) with \(x+y<(a+1)+(b+1)\), it is true that \(P\_{x,y} \le (x+y)!\). Then in
> particular
>
> \[\begin{split}P\_{a+1,b} &\le ((a+1)+b)!\\
> P\_{a,b+1} &\le (a+(b+1))!.\end{split}\]
>
> So
>
> \[\begin{split}P\_{a + 1,b + 1} &= P\_{a + 1, b} + P\_{a, b + 1}\\
> & ≤ (a + 1 + b) ! + (a + (b + 1)) !\\
> & ≤ (a + b) (a + b + 1) ! + (a + 1 + b) ! + (a + (b + 1)) ! \\
> & = ((a + b + 1) + 1) (a + b + 1)! \\
> & = ((a + b + 1) + 1)! \\
> & = (a + 1 + (b + 1))!.\end{split}\]

```lean
theorem pascal_le (a b : ℕ) : pascal a b ≤ (a + b)! := by
  match a, b with
  | a, 0 =>
      calc pascal a 0 = 1 := by rw [pascal]
        _ ≤ (a + 0)! := by apply factorial_pos
  | 0, b + 1 =>
      calc pascal 0 (b + 1) = 1 := by rw [pascal]
        _ ≤ (0 + (b + 1))! := by apply factorial_pos
  | a + 1, b + 1 =>
      have IH1 := pascal_le (a + 1) b -- inductive hypothesis
      have IH2 := pascal_le a (b + 1) -- inductive hypothesis
      calc pascal (a + 1) (b + 1) = pascal (a + 1) b + pascal a (b + 1) := by rw [pascal]
        _ ≤ (a + 1 + b) ! + (a + (b + 1)) ! := by rel [IH1, IH2]
        _ ≤ (a + b) * (a + b + 1) ! + (a + 1 + b) ! + (a + (b + 1)) !  := by extra
        _ = ((a + b + 1) + 1) * (a + b + 1)! := by ring
        _ = ((a + b + 1) + 1)! := by rw [factorial, factorial, factorial]
        _ = (a + 1 + (b + 1))! := by ring
termination_by _ a b => a + b
```

#### 6.5.3. Example

With a more delicate calculation, we can improve the bound in Example 6.5.2 to
an exact formula.

> **Theorem:**
>
> For all natural numbers \(a\) and \(b\), \(P\_{a,b} \ a! \ b!= (a+b)!\).

> **Proof:**
>
> We prove this by strong induction relative to the expression \(a+b\).
>
> For all \(a\),
>
> \[\begin{split}P\_{a,0}&=1\\
> &= (a+0)!,\end{split}\]
>
> and for all \(b\),
>
> \[\begin{split}P\_{0,b+1}&=1\\
> &= (0+(b+1))!.\end{split}\]
>
> Now let \(a\) and \(b\) be natural numbers and suppose that for all \(x\) and
> \(y\) with \(x+y<(a+1)+(b+1)\), it is true that \(P\_{x,y} \ x! \ y! = (x+y)!\). Then
> in particular
>
> \[\begin{split}P\_{a+1,b} \ (a+1)! \ b! &= ((a+1)+b)!\\
> P\_{a,b+1} \ a! \ (b+1)! &= (a+(b+1))!.\end{split}\]
>
> So
>
> \[\begin{split}P\_{a + 1, b + 1} \ (a + 1)! \ (b + 1)!
> & = (P\_{a + 1, b} + P\_{a, b + 1}) \ (a + 1)! \ (b + 1)! \\
> & = P\_{a + 1, b} \ (a + 1)! \ (b + 1)!
> + P\_{ a, b + 1} \ (a + 1)! \ (b + 1)! \\
> & = P\_{a + 1, b} \ (a + 1)! \ ((b + 1) b !)
> + P\_{a, b + 1} ((a + 1) a !) \ (b + 1)! \\
> & = (b + 1) (P\_{a + 1, b} \ (a + 1)! \ b !)
> + (a + 1) (P\_{a, b + 1} \ a ! \ (b + 1)!) \\
> & = (b + 1) ((a + 1) + b) !
> + (a + 1) (a + (b + 1)) ! \\
> & = ((1 + a + b) + 1) (1 + a + b) ! \\
> & = ((1 + a + b) + 1) ! \\
> & = ((a + 1) + (b + 1)) !.\end{split}\]

```lean
theorem pascal_eq (a b : ℕ) : pascal a b * a ! * b ! = (a + b)! := by
  match a, b with
  | a, 0 =>
    calc pascal _ 0 * a ! * 0! = 1 * a ! * 0! := by rw [pascal]
      _ = 1 * a ! * 1 := by rw [factorial]
      _ = (a + 0)! := by ring
  | 0, b + 1 =>
    calc pascal 0 (b + 1) * 0 ! * (b + 1)! = 1 * 0 ! * (b + 1)! := by rw [pascal]
      _ = 1 * 1 * (b + 1)! := by rw [factorial, factorial]
      _ = (0 + (b + 1))! := by ring
  | a + 1, b + 1 =>
    have IH1 := pascal_eq (a + 1) b -- inductive hypothesis
    have IH2 := pascal_eq a (b + 1) -- inductive hypothesis
    calc
      pascal (a + 1) (b + 1) * (a + 1)! * (b + 1)!
        = (pascal (a + 1) b + pascal a (b + 1)) * (a + 1)! * (b + 1)! := by rw [pascal]
      _ = pascal (a + 1) b * (a + 1)! * (b + 1)!
          + pascal a (b + 1) * (a + 1)! * (b + 1)! := by ring
      _ = pascal (a + 1) b * (a + 1)! * ((b + 1) * b !)
          + pascal a (b + 1) * ((a + 1) * a !) * (b + 1)! := by rw [factorial, factorial]
      _ = (b + 1) * (pascal (a + 1) b * (a + 1)! * b !)
          + (a + 1) * (pascal a (b + 1) * a ! * (b + 1)!) := by ring
      _ = (b + 1) * ((a + 1) + b) !
          + (a + 1) * (a + (b + 1)) ! := by rw [IH1, IH2]
      _ = ((1 + a + b) + 1) * (1 + a + b) ! := by ring
      _ = ((1 + a + b) + 1) ! := by rw [factorial]
      _ = ((a + 1) + (b + 1)) ! := by ring
termination_by _ a b => a + b
```

> **Corollary:**
>
> For all natural numbers \(a\) and \(b\),
>
> \[P\_{a,b}=\frac{(a+b)!}{a! \ b!}.\]

This fact is a trivial rearrangement of the theorem above, but the Lean proof is more complicated,
due to issues with division, which in this book we largely avoid. Don’t sweat the details here,
or the two unfamiliar tactics `field_simp` and `norm_cast`.

```lean
example (a b : ℕ) : (pascal a b : ℚ) = (a + b)! / (a ! * b !) := by
  have ha := factorial_pos a
  have hb := factorial_pos b
  field_simp [ha, hb]
  norm_cast
  calc pascal a b * (a ! * b !) = pascal a b * a ! * b ! := by ring
    _ = (a + b)! := by apply pascal_eq
```

#### 6.5.4. Exercises

1. Prove that for all natural numbers \(a\) and \(b\), \(P\_{a,b} =P\_{b,a}\).

   ```lean
   theorem pascal_symm (m n : ℕ) : pascal m n = pascal n m := by
     match m, n with
     | 0, 0 => sorry
     | a + 1, 0 => sorry
     | 0, b + 1 => sorry
     | a + 1, b + 1 => sorry
   termination_by _ a b => a + b
   ```
2. Prove using simple induction that for all natural numbers \(a\), \(P\_{a,1} =a+1\).

   ```lean
   example (a : ℕ) : pascal a 1 = a + 1 := by
     sorry
   ```

### 6.6. The Division Algorithm

#### 6.6.1. Definition

Consider the functions \(\operatorname{mod}\) and \(\operatorname{div}\) defined recursively
on the integers by,

\[\begin{split}\operatorname{mod}(n, d)&=
\begin{cases}
\operatorname{mod}(n + d, d),&nd<0\\
\operatorname{mod}(n - d, d),&0< d(n-d)\\
0,&n=d\\
n,&0\le nd\le d^2\text{ and }n\ne d
\end{cases}\\
\operatorname{div}(n, d)&=
\begin{cases}
\operatorname{div}(n + d, d)-1,&nd<0\\
\operatorname{div}(n - d, d)+1,&0< d(n-d)\\
1,&n=d\\
0,&0\le nd\le d^2\text{ and }n\ne d
\end{cases}\end{split}\]

The idea will be that \(\operatorname{div}\) computes the quotient of \(n\) by \(d\)
(in the elementary-school sense where we produce an integer rather than continuing into decimal
places), and \(\operatorname{mod}\) computes the remainder of \(n\) on division by
\(d\). For example,

\[ \begin{align}\begin{aligned}&
&
&\qquad
&
&\operatorname{mod}(11,4)
&
&\qquad
&
&\operatorname{div}(11,4)\\0&<4(11-4)
&
&\qquad
&
& =\operatorname{mod}(7,4)
&
&\qquad
&
& =\operatorname{div}(7,4)+1\\0&<4(7-4)
&
&\qquad
&
&=\operatorname{mod}(3,4)
&
&\qquad
&
&=(\operatorname{div}(3,4)+1)+1\\0\le 4\cdot 3\le 4^2,4&\ne 3
&
&\qquad
&
&=3
&
&\qquad
&
&=(0+1)+1\\&
&
&
&
&
&
&
&
&=2.\end{aligned}\end{align} \]

These definitions are *well-founded* because each step of the definition depends only on previous
terms \(\operatorname{mod}(n, d)\), \(\operatorname{div}(n, d)\) for which the expression
\(2n - d\) is strictly smaller in absolute value. (This is not very obvious, although Lean can
prove it automatically. As a sanity check,

\[\begin{split}2 \cdot 11 - 4 = 18\\
2 \cdot 7 - 4 = 10\\
2 \cdot 3 - 4 = 2\end{split}\]

which decreases strictly.) Here is how this looks in Lean, with the well-foundedness explanation
expressed using the syntax `termination_by`, as in Definition 6.5.1.

```lean
def fmod (n d : ℤ) : ℤ :=
  if n * d < 0 then
    fmod (n + d) d
  else if h2 : 0 < d * (n - d) then
    fmod (n - d) d
  else if h3 : n = d then
    0
  else
    n
termination_by _ n d => 2 * n - d

def fdiv (n d : ℤ) : ℤ :=
  if n * d < 0 then
    fdiv (n + d) d - 1
  else if 0 < d * (n - d) then
    fdiv (n - d) d + 1
  else if h3 : n = d then
    1
  else
    0
termination_by _ n d => 2 * n - d
```

Let’s check they do what they’re supposed to:

```lean
#eval fmod 11 4 -- infoview displays `3`
#eval fdiv 11 4 -- infoview displays `2`
```

Compute a few examples yourself (checking your work with Lean) and see if you believe that
\(\operatorname{div}\) and \(\operatorname{mod}\) are producing “quotient” and “remainder”.
Now let’s make that rigorous.

#### 6.6.2. Example

> **Theorem:**
>
> For any integers \(n\) and \(d\), we have that
> \(\operatorname{mod}(n, d) + d \cdot \operatorname{div}(n, d) = n\).

> **Proof:**
>
> We prove this by strong induction relative to the expression \(2n - d\). Suppose that for all
> integers \(m\) and \(c\) with \(|2m - c|<|2n-d|\), it is true that
> \(\operatorname{mod}(n, d) + d \cdot \operatorname{div}(n, d) = n\).
>
> **Case 1** (\(nd<0\)): Then by the inductive hypothesis
>
> \[\operatorname{mod}(n+d, d) + d \cdot \operatorname{div}(n+d, d) = n+d,\]
>
> so
>
> \[\begin{split}\operatorname{mod}(n, d) + d \cdot \operatorname{div}(n, d)
> &=\operatorname{mod}(n+d, d) + d \cdot (\operatorname{div}(n+d, d) -1)\\
> &=(\operatorname{mod}(n+d, d) + d \cdot \operatorname{div}(n+d, d))-d\\
> &=(n+d)-d\\
> &=n.\end{split}\]
>
> **Case 2** (\(0<d(n-d)\)): Then by the inductive hypothesis
>
> \[\operatorname{mod}(n-d, d) + d \cdot \operatorname{div}(n-d, d) = n-d,\]
>
> so
>
> \[\begin{split}\operatorname{mod}(n, d) + d \cdot \operatorname{div}(n, d)
> &=\operatorname{mod}(n-d, d) + d \cdot (\operatorname{div}(n-d, d) +1)\\
> &=(\operatorname{mod}(n-d, d) + d \cdot \operatorname{div}(n-d, d))+d\\
> &=(n-d)+d\\
> &=n.\end{split}\]
>
> **Case 3** (\(n=d\)): Then
>
> \[\begin{split}\operatorname{mod}(n, d) + d \cdot \operatorname{div}(n, d)
> &=0 + d\cdot 1\\
> &=d\\
> &=n.\end{split}\]
>
> **Case 4**: In this case,
>
> \[\begin{split}\operatorname{mod}(n, d) + d \cdot \operatorname{div}(n, d)
> &=n + d\cdot 0\\
> &=n.\end{split}\]

```lean
theorem fmod_add_fdiv (n d : ℤ) : fmod n d + d * fdiv n d = n := by
  rw [fdiv, fmod]
  split_ifs with h1 h2 h3 <;> push_neg at *
  · -- case `n * d < 0`
    have IH := fmod_add_fdiv (n + d) d -- inductive hypothesis
    calc fmod (n + d) d + d * (fdiv (n + d) d - 1)
        = (fmod (n + d) d + d * fdiv (n + d) d) - d := by ring
      _ = (n + d) - d := by rw [IH]
      _ = n := by ring
  · -- case `0 < d * (n - d)`
    have IH := fmod_add_fdiv (n - d) d -- inductive hypothesis
    calc fmod (n - d) d + d * (fdiv (n - d) d + 1)
        = (fmod (n - d) d + d * fdiv (n - d) d) + d := by ring
        _ = n := by addarith [IH]
  · -- case `n = d`
    calc 0 + d * 1 = d := by ring
      _ = n := by rw [h3]
  · -- last case
    ring
termination_by _ n d => 2 * n - d
```

#### 6.6.3. Example

> **Theorem:**
>
> For any integers \(n\) and \(d\), with \(d\) positive, we have that
> \(\operatorname{mod}(n, d)\) is nonnegative.

> **Proof:**
>
> We prove this by strong induction relative to the expression \(2n - d\). Suppose that for all
> integers \(m\) and \(c\) with \(c\) positive and \(|2m - c|<|2n-d|\), it is true
> that \(\operatorname{mod}(m, c)\) is nonnegative.
>
> **Case 1** (\(nd<0\)): Then by the inductive hypothesis
> \(\operatorname{mod}(n, d)=\operatorname{mod}(n + d, d)\geq 0\).
>
> **Case 2** (\(0<d(n-d)\)): Then by the inductive hypothesis
> \(\operatorname{mod}(n, d)=\operatorname{mod}(n - d, d)\geq 0\).
>
> **Case 3** (\(n=d\)): Then \(\operatorname{mod}(n, d)= 0\), so
> \(\operatorname{mod}(n, d)\geq 0\).
>
> **Case 4** (\(0\le nd\le d^2\) and \(n\ne d\)): Then since
> \(0\le nd\) and by hypothesis \(0<d\), we have that
> \(\operatorname{mod}(n, d)=n\geq 0\).

```lean
theorem fmod_nonneg_of_pos (n : ℤ) {d : ℤ} (hd : 0 < d) : 0 ≤ fmod n d := by
  rw [fmod]
  split_ifs with h1 h2 h3 <;> push_neg at *
  · -- case `n * d < 0`
    have IH := fmod_nonneg_of_pos (n + d) hd -- inductive hypothesis
    apply IH
  · -- case `0 < d * (n - d)`
    have IH := fmod_nonneg_of_pos (n - d) hd -- inductive hypothesis
    apply IH
  · -- case `n = d`
    extra
  · -- last case
    cancel d at h1
termination_by _ n d hd => 2 * n - d
```

#### 6.6.4. Example

> **Theorem:**
>
> For any integers \(n\) and \(d\), with \(d\) positive, we have that
> \(\operatorname{mod}(n, d)<d\).

> **Proof:**
>
> We prove this by strong induction relative to the expression \(2n - d\). Suppose that for all
> integers \(m\) and \(c\) with \(c\) positive and \(|2m - c|<|2n-d|\), it is true
> that \(\operatorname{mod}(m, c)<c\).
>
> **Case 1** (\(nd<0\)): Then by the inductive hypothesis
> \(\operatorname{mod}(n, d)=\operatorname{mod}(n + d, d)<d\).
>
> **Case 2** (\(0<d(n-d)\)): Then by the inductive hypothesis
> \(\operatorname{mod}(n, d)=\operatorname{mod}(n - d, d)<d\).
>
> **Case 3** (\(n=d\)): Then \(\operatorname{mod}(n, d)= 0<d\) by hypothesis.
>
> **Case 4** (\(0\le nd\le d^2\) and \(n\ne d\)): We have that \(n-d\le 0\), since
> \(d(n-d)\le 0\) and by hypothesis \(0<d\). Therefore \(n\le d\). Also, by hypothesis,
> \(n\ne d\). Combining these, we have that \(n< d\).

```lean
theorem fmod_lt_of_pos (n : ℤ) {d : ℤ} (hd : 0 < d) : fmod n d < d := by
  rw [fmod]
  split_ifs with h1 h2 h3 <;> push_neg at *
  · -- case `n * d < 0`
    have IH := fmod_lt_of_pos (n + d) hd -- inductive hypothesis
    apply IH
  · -- case `0 < d * (n - d)`
    have IH := fmod_lt_of_pos (n - d) hd -- inductive hypothesis
    apply IH
  · -- case `n = d`
    apply hd
  · -- last case
    have h4 :=
    calc 0 ≤ - d * (n - d) := by addarith [h2]
      _ = d * (d - n) := by ring
    cancel d at h4
    apply lt_of_le_of_ne
    · addarith [h4]
    · apply h3
termination_by _ n d hd => 2 * n - d
```

#### 6.6.5. Example

Putting this all together, we can prove the following theorem. This theorem justifies the tactic
`mod_cases`, which we have been using since [Example 3.4.4]](#m2001-first-mod-cases): we can list out
just finitely many possibilities for an integer \(a\), considered modulo a positive integer
\(b\).

> **Theorem:**
>
> Let \(a\) and \(b\) be integers, with \(b\) positive. There exists an
> integer \(r\), with \(0 \le r < b\), such that \(a \equiv r\mod b\).

> **Proof:**
>
> We will show that the integer \(\operatorname{mod}(a,b)\) has this property. Indeed, by
> Example 6.6.3 and Example 6.6.4,
> \(0 \le \operatorname{mod}(a,b) < b\), and by Example 6.6.2,
>
> \[\operatorname{mod}(a, b) + b \cdot \operatorname{div}(a, b) = a,\]
>
> so
>
> \[a-\operatorname{mod}(a, b)=b \cdot \operatorname{div}(a, b),\]
>
> so
>
> \[a \equiv \operatorname{mod}(a, b)\mod b.\]

```lean
example (a b : ℤ) (h : 0 < b) : ∃ r : ℤ, 0 ≤ r ∧ r < b ∧ a ≡ r [ZMOD b] := by
  use fmod a b
  constructor
  · apply fmod_nonneg_of_pos a h
  constructor
  · apply fmod_lt_of_pos a h
  · use fdiv a b
    have Hab : fmod a b + b * fdiv a b = a := fmod_add_fdiv a b
    addarith [Hab]
```

#### 6.6.6. Exercises

1. Prove the analogue of Example 6.6.4 for \(d\) negative:
   For any integers \(n\) and \(d\), with \(d\) negative, we have that
   \(d<\operatorname{mod}(n, d)\).

   ```lean
   theorem lt_fmod_of_neg (n : ℤ) {d : ℤ} (hd : d < 0) : d < fmod n d := by
     sorry
   ```
2. Consider the function \(T\) defined recursively on the integers by,

   \[\begin{split}T(n)=
   \begin{cases}
   T(1-n)+2n-1,&0< n\\
   T(-n),&n< 0\\
   0&n=0.
   \end{cases}\end{split}\]

   This recursive definition is well-founded, since its self-references are strictly decreasing in
   \(|3n-1|\).

   Prove that for all integers \(n\), \(T(n)=n^2\).

   ```lean
   def T (n : ℤ) : ℤ :=
     if 0 < n then
       T (1 - n) + 2 * n - 1
     else if 0 < - n then
       T (-n)
     else
       0
   termination_by T n => 3 * n - 1

   theorem T_eq (n : ℤ) : T n = n ^ 2 := by
     sorry
   ```
3. Let \(a\) and \(b\) be integers, with \(b\) positive. Prove that there exists a
   unique integer \(r\) in the range \(0 \le r < b\), such that \(a\equiv r\mod b\).

   This theorem is an upgrade to Example 6.6.5, stating uniqueness
   as well as existence. We stated it without proof in [Example 4.3.4]](#m2001-division-algorithm)
   (with the Lean name `Int.existsUnique_modEq_lt`) and have been implicitly using it whenever we
   deduce a contradiction involving “obvious non-congruences”, such as in
   [Example 4.4.3]](#m2001-mod-contradictory).

   Suggested approach: Write the following as a stand-alone theorem `uniqueness` and prove it,
   somewhat along the lines of the special case proved in
   [Example 4.3.4]](#m2001-division-algorithm):

   > Let \(a\) and \(b\) be integers, with \(b\) positive. Let \(r\) and \(s\)
   > be integers, both in the range \(0 \le r < b\), \(0 \le s < b\) and both congruent to
   > \(a\) modulo \(b\). Show that they are equal.

   Then put all the pieces together, combining that with the argument from
   Example 6.6.5.

   ```lean
   theorem uniqueness (a b : ℤ) (h : 0 < b) {r s : ℤ}
       (hr : 0 ≤ r ∧ r < b ∧ a ≡ r [ZMOD b])
       (hs : 0 ≤ s ∧ s < b ∧ a ≡ s [ZMOD b]) : r = s := by
     sorry

   example (a b : ℤ) (h : 0 < b) : ∃! r : ℤ, 0 ≤ r ∧ r < b ∧ a ≡ r [ZMOD b] := by
     sorry
   ```

### 6.7. The Euclidean algorithm

#### 6.7.1. Definition

> **Definition:**
>
> The function \(\operatorname{gcd}\) of two integers is defined recursively as follows:
>
> > \[\begin{split}\operatorname{gcd}(a,b)=
> > \begin{cases}
> > \operatorname{gcd}(b,\operatorname{mod}(a,b)) & 0< b\\
> > \operatorname{gcd}(b,\operatorname{mod}(a,-b)) & b< 0\\
> > a & b=0\text{ and }0\le a\\
> > -a & b=0\text{ and }a<0.
> > \end{cases}\end{split}\]

Let’s practice evaluating the \(\operatorname{gcd}\) function. We calculate
\(\operatorname{gcd}(-21,15)\):

\[ \begin{align}\begin{aligned}\operatorname{mod}(-21,15)&=9
&
&\qquad
&
\operatorname{gcd}(-21,15)&=\operatorname{gcd}(15,9)\\\operatorname{mod}(15,9)&=6
&
&\qquad
&
&=\operatorname{gcd}(9,6)\\\operatorname{mod}(9,6)&=3
&
&\qquad
&
&=\operatorname{gcd}(6,3)\\\operatorname{mod}(6,3)&=0
&
&\qquad
&
&=\operatorname{gcd}(3,0)\\&
&
&\qquad
&
&=3.\end{aligned}\end{align} \]

(Remember that \(\operatorname{mod}(a,b)\) is the “remainder” function, defined in
Definition 6.6.1.)

As in Definition 6.5.1 and
Definition 6.6.1, we
need to justify that this recursive definition is *well-founded*, that is that the process always
terminates. And as in those sections, we do this by providing an expression which becomes strictly
smaller in absolute value as the process goes on. Here that expression is \(b\), the second of
the two numbers (note that in the example we have \(b\) being successively 15, 9, 6, 3, 0, which
indeed is decreasing.)

This leads to the following attempt at a Lean definition for \(\operatorname{gcd}\), with the
clause `termination_by _ a b => b` indicating that \(b\) is the size expression for the
well-foundedness.

```lean
def gcd (a b : ℤ) : ℤ :=
  if 0 < b then
    gcd b (fmod a b)
  else if b < 0 then
    gcd b (fmod a (-b))
  else if 0 ≤ a then
    a
  else
    -a
termination_by _ a b => b
```

But unlike in Section 6.5 and
Section 6.6, the definition is not yet complete: the fact
that \(b\) is decreasing along the recursion is “non-obvious enough” that it requires an
explicit proof.

> **Proposition:**
>
> The recursive definition \(\operatorname{gcd}\) is well-founded.

> **Proof:**
>
> There are two things to check:
>
> 1. \(\underline{\text{If }0<b\text{ then }-b<\operatorname{mod}(a,b)<b}\):
>    By Example 6.6.3 and Example 6.6.4,
>    \(0\le \operatorname{mod}(a,b)<b\), which establishes the upper bound immediately and the
>    lower bound since
>
>    > \[\begin{split}-b&<0\\
>    > &\le \operatorname{mod}(a,b).\end{split}\]
> 2. \(\underline{\text{If }b<0\text{ then }b<\operatorname{mod}(a,-b)<-b}\):
>    We have that \(0<-b\),
>    so by Example 6.6.3 and Example 6.6.4,
>    \(0\le \operatorname{mod}(a,-b)<-b\), which establishes the upper bound immediately and
>    the lower bound since
>
>    > \[\begin{split}b&<0\\
>    > &\le \operatorname{mod}(a,-b).\end{split}\]

In Lean, we state and prove these facts separately, tagging them with the attribute
`@[decreasing]`, which lets the subsequent definition `gcd` call on them for the
well-foundedness. Check that if they are omitted then the definition gives an error.

```lean
@[decreasing] theorem lower_bound_fmod1 (a b : ℤ) (h1 : 0 < b) : -b < fmod a b := by
  have H : 0 ≤ fmod a b
  · apply fmod_nonneg_of_pos
    apply h1
  calc -b < 0 := by addarith [h1]
    _ ≤ _ := H

@[decreasing] theorem lower_bound_fmod2 (a b : ℤ) (h1 : b < 0) : b < fmod a (-b) := by
  have H : 0 ≤ fmod a (-b)
  · apply fmod_nonneg_of_pos
    addarith [h1]
  have h2 : 0 < -b := by addarith [h1]
  calc b < 0 := h1
    _ ≤ fmod a (-b) := H

@[decreasing] theorem upper_bound_fmod2 (a b : ℤ) (h1 : b < 0) : fmod a (-b) < -b := by
  apply fmod_lt_of_pos
  addarith [h1]

@[decreasing] theorem upper_bound_fmod1 (a b : ℤ) (h1 : 0 < b) : fmod a b < b := by
  apply fmod_lt_of_pos
  apply h1
```

After this the Lean definition `gcd` goes through successfully. Sanity check: does the Lean
definition agree with our calculation above for \(\operatorname{gcd}(-21,15)\)?

```lean
#eval gcd (-21) 15 -- infoview displays `3`
```

#### 6.7.2. Example

Every fact about \(\operatorname{gcd}\) will be proved by strong induction, using the same
well-foundedness justification.

> **Proposition:**
>
> For all integers \(a\) and \(b\), the integer \(\operatorname{gcd}(a,b)\) is
> nonnegative.

> **Proof:**
>
> We prove this by strong induction on \(b\). Suppose that for all
> integers \(x\) and \(y\) with \(|y|<|b|\), it is true that
> \(0 \le \operatorname{gcd}(x, y)\).
>
> **Case 1** (\(0<b\)): Then \(\operatorname{gcd}(a,b)\) is equal to
> \(\operatorname{gcd}(b,\operatorname{mod}(a,b))\) which by the inductive hypothesis is
> nonnegative.
>
> **Case 2** (\(b<0\)): Then \(\operatorname{gcd}(a,b)\) is equal to
> \(\operatorname{gcd}(b,\operatorname{mod}(a,-b))\) which by the inductive hypothesis is
> nonnegative.
>
> **Case 3** (\(b=0\), \(0\le a\)): Then we have that
> \(\operatorname{gcd}(a,b)=a\geq 0\).
>
> **Case 4** (\(b=0\), \(a<0\)): Then we have that \(\operatorname{gcd}(a,b)=-a\geq 0\).

```lean
theorem gcd_nonneg (a b : ℤ) : 0 ≤ gcd a b := by
  rw [gcd]
  split_ifs with h1 h2 ha <;> push_neg at *
  · -- case `0 < b`
    have IH := gcd_nonneg b (fmod a b) -- inductive hypothesis
    apply IH
  · -- case `b < 0`
    have IH := gcd_nonneg b (fmod a (-b)) -- inductive hypothesis
    apply IH
  · -- case `b = 0`, `0 ≤ a`
    apply ha
  · -- case `b = 0`, `a < 0`
    addarith [ha]
termination_by _ a b => b
```

#### 6.7.3. Example

> **Proposition:**
>
> For all integers \(a\) and \(b\), the integer \(\operatorname{gcd}(a,b)\) is a factor
> of both \(a\) and \(b\).

That is, \(\operatorname{gcd}(a,b)\) is a *common divisor* of \(a\) and \(b\). We will
prove later (see the exercises) that in fact it is the *greatest common divisor* of \(a\) and
\(b\), hence the acronym GCD.

> **Proof:**
>
> We prove this by strong induction on \(b\). Suppose that for all
> integers \(x\) and \(y\) with \(|y|<|b|\), it is true that
> \(0 \le \operatorname{gcd}(x, y)\).
>
> **Case 1** (\(0<b\)): Let \(q=\operatorname{div}(a,b)\) and let
> \(r=\operatorname{mod}(a,b)\), so that \(a=r+bq\) (by Example 6.6.2).
>
> Then by the recursive definition \(\operatorname{gcd}(a,b)\) is equal to
> \(\operatorname{gcd}(b,r)\), and by the inductive hypothesis this
> divides both \(b\) and \(r\). We need to show it divides \(b\)
> (which is immediate) and \(a\), which we now turn to.
>
> Since \(\operatorname{gcd}(a,b)\mid b\), there exists an integer \(k\) such that
> \(b = \operatorname{gcd}(a,b)k\), and since \(\operatorname{gcd}(a,b)\mid r\),
> there exists an integer \(l\) such that \(r = \operatorname{gcd}(a,b)l\). We now have
>
> \[\begin{split}a&=r+bq\\
> &=\operatorname{gcd}(a,b)l+(\operatorname{gcd}(a,b)k)q\\
> &=\operatorname{gcd}(a,b)\cdot (l+kq),\end{split}\]
>
> so \(\operatorname{gcd}(a,b)\mid a\).
>
> **Case 2** (\(b<0\)): Let \(q=\operatorname{div}(a,-b)\) and let
> \(r=\operatorname{mod}(a,-b)\), so that \(a=r+(-b)q\) (by
> Example 6.6.2).
>
> Then by the recursive definition \(\operatorname{gcd}(a,b)\) is equal to
> \(\operatorname{gcd}(b,r)\), and by the inductive hypothesis this
> divides both \(b\) and \(r\). We need to show it divides \(b\)
> (which is immediate) and \(a\), which we now turn to.
>
> Since \(\operatorname{gcd}(a,b)\mid b\), there exists an integer \(k\) such that
> \(b = \operatorname{gcd}(a,b)k\), and since \(\operatorname{gcd}(a,b)\mid r\),
> there exists an integer \(l\) such that \(r = \operatorname{gcd}(a,b)l\). We now have
>
> \[\begin{split}a&=r+(-b)q\\
> &=\operatorname{gcd}(a,b)l+(-\operatorname{gcd}(a,b)k)q\\
> &=\operatorname{gcd}(a,b)\cdot (l-kq),\end{split}\]
>
> so \(\operatorname{gcd}(a,b)\mid a\).
>
> **Case 3** (\(b=0\), \(0\le a\)): Then we have that
> \(\operatorname{gcd}(a,b)=a\), which is a factor of \(a\) since \(a\cdot 1=a\) and
> \(b\) since
>
> \[\begin{split}b &= 0\\
> &=0\cdot a.\end{split}\]
>
> **Case 4** (\(b=0\), \(a<0\)): Then we have that \(\operatorname{gcd}(a,b)=-a\), which
> is a factor of \(a\) since \(-a\cdot -1=a\) and \(b\) since
>
> \[\begin{split}b &= 0\\
> &=0\cdot -a.\end{split}\]

It would be possible to set up this proof in Lean with the structure of an “and” goal, like this:
(The `_` are placeholders just to show the basic structure.)

```lean
theorem gcd_dvd (a b : ℤ) : gcd a b ∣ b ∧ gcd a b ∣ a := by
  rw [gcd]
  split_ifs with h1 h2 <;> push_neg at *
  · -- case `0 < b`
    have IH : _ ∧ _ := gcd_dvd b (fmod a b) -- inductive hypothesis
    obtain ⟨IH_right, IH_left⟩ := IH
    constructor
    · -- prove that `gcd a b ∣ b`
      sorry
    · -- prove that `gcd a b ∣ a`
      sorry
  · -- case `b < 0`
    have IH : _ ∧ _ := gcd_dvd b (fmod a (-b)) -- inductive hypothesis
    obtain ⟨IH_right, IH_left⟩ := IH
    constructor
    · -- prove that `gcd a b ∣ b`
      sorry
    · -- prove that `gcd a b ∣ a`
      sorry
  · -- case `b = 0`, `0 ≤ a`
    constructor
    · -- prove that `gcd a b ∣ b`
      sorry
    · -- prove that `gcd a b ∣ a`
      sorry
  · -- case `b = 0`, `a < 0`
    constructor
    · -- prove that `gcd a b ∣ b`
      sorry
    · -- prove that `gcd a b ∣ a`
      sorry
termination_by gcd_dvd a b => b
```

But the constant switching between the \(\operatorname{gcd}(a,b)\mid b\) task and the
\(\operatorname{gcd}(a,b)\mid a\) task is a little hard to keep track of. A more elegant setup
features two separate lemmas, one to show \(\operatorname{gcd}(a,b)\mid b\) and one to show
\(\operatorname{gcd}(a,b)\mid a\), with the following structure:

```lean
theorem gcd_dvd_right (a b : ℤ) : gcd a b ∣ b := by
  rw [gcd]
  split_ifs with h1 h2 <;> push_neg at *
  · -- case `0 < b`
    have IH := gcd_dvd_left b (fmod a b) -- inductive hypothesis
  · -- case `b < 0`
    have IH := gcd_dvd_left b (fmod a (-b)) -- inductive hypothesis
  · -- case `b = 0`, `0 ≤ a`
    sorry
  · -- case `b = 0`, `a < 0`
    sorry

theorem gcd_dvd_left (a b : ℤ) : gcd a b ∣ a := by
  rw [gcd]
  split_ifs with h1 h2 <;> push_neg at *
  · -- case `0 < b`
    have IH1 := gcd_dvd_left b (fmod a b) -- inductive hypothesis
    have IH2 := gcd_dvd_right b (fmod a b) -- inductive hypothesis
    sorry
  · -- case `b < 0`
    have IH1 := gcd_dvd_left b (fmod a (-b)) -- inductive hypothesis
    have IH2 := gcd_dvd_right b (fmod a (-b)) -- inductive hypothesis
    sorry
  · -- case `b = 0`, `0 ≤ a`
    sorry
  · -- case `b = 0`, `a < 0`
    sorry
```

But now the strong induction structure is complicated: the proof of `gcd_dvd_right` depends on
`gcd_dvd_left` for smaller values of `a`, `b`, and the proof of `gcd_dvd_left` depends on
`gcd_dvd_right` for smaller values of `a`, `b`. This is called a *mutual induction* and it
has a special syntax in Lean: the two theorems are enclosed in a `mutual` block, with a joint
termination explanation at the end, like this:

```lean
mutual

theorem gcd_dvd_right (a b : ℤ) : gcd a b ∣ b := by
  ...

theorem gcd_dvd_left (a b : ℤ) : gcd a b ∣ a := by
  ...

end
termination_by gcd_dvd_right a b => b ; gcd_dvd_left a b => b
```

Here is the full proof in Lean.

```lean
mutual
theorem gcd_dvd_right (a b : ℤ) : gcd a b ∣ b := by
  rw [gcd]
  split_ifs with h1 h2 <;> push_neg at *
  · -- case `0 < b`
    apply gcd_dvd_left b (fmod a b) -- inductive hypothesis
  · -- case `b < 0`
    apply gcd_dvd_left b (fmod a (-b)) -- inductive hypothesis
  · -- case `b = 0`, `0 ≤ a`
    have hb : b = 0 := le_antisymm h1 h2
    use 0
    calc b = 0 := hb
      _ = a * 0 := by ring
  · -- case `b = 0`, `a < 0`
    have hb : b = 0 := le_antisymm h1 h2
    use 0
    calc b = 0 := hb
      _ = -a * 0 := by ring

theorem gcd_dvd_left (a b : ℤ) : gcd a b ∣ a := by
  rw [gcd]
  split_ifs with h1 h2 <;> push_neg at *
  · -- case `0 < b`
    have IH1 := gcd_dvd_left b (fmod a b) -- inductive hypothesis
    have IH2 := gcd_dvd_right b (fmod a b) -- inductive hypothesis
    obtain ⟨k, hk⟩ := IH1
    obtain ⟨l, hl⟩ := IH2
    have H : fmod a b + b * fdiv a b = a := fmod_add_fdiv a b
    set q := fdiv a b
    set r := fmod a b
    use l + k * q
    calc a = r + b * q := by rw [H]
      _ = gcd b r * l + (gcd b r * k) * q := by rw [← hk, ← hl]
      _ = gcd b r * (l + k * q) := by ring
  · -- case `b < 0`
    have IH1 := gcd_dvd_left b (fmod a (-b)) -- inductive hypothesis
    have IH2 := gcd_dvd_right b (fmod a (-b)) -- inductive hypothesis
    obtain ⟨k, hk⟩ := IH1
    obtain ⟨l, hl⟩ := IH2
    have H := fmod_add_fdiv a (-b)
    set q := fdiv a (-b)
    set r := fmod a (-b)
    use l - k * q
    calc a = r + (-b) * q := by rw [H]
      _ = gcd b r * l + (- (gcd b r * k)) * q := by rw [← hk, ← hl]
      _ = gcd b r * (l - k * q) := by ring
  · -- case `b = 0`, `0 ≤ a`
    use 1
    ring
  · -- case `b = 0`, `a < 0`
    use -1
    ring

end
termination_by gcd_dvd_right a b => b ; gcd_dvd_left a b => b
```

There is a new tactic in the above proof: `set`, which introduce a short name for a long
expression (typically one which occurs frequently and you are tired of typing out in full). Notice
how the goal state changes before and after its use.

#### 6.7.4. Definition

The process described in Definition 6.7.1 is generally called the
*Euclidean algorithm*. A process called the *extended Euclidean algorithm* is used to compute two
other functions, which we will call \(L(a,b)\) and \(R(a,b)\), at the same time as
\(\operatorname{gcd}(a,b)\).

> **Definition:**
>
> The functions \(L\) and \(R\) of two integers are defined mutually recursively as follows:
>
> > \[\begin{split}L(a,b)&=
> > \begin{cases}
> > R(b,\operatorname{mod}(a,b)) & 0< b\\
> > R(b,\operatorname{mod}(a,-b)) & b<0\\
> > 1 & b=0\text{ and }0\le a\\
> > -1 & b=0\text{ and }a <0.
> > \end{cases} \\
> > R(a,b)&=
> > \begin{cases}
> > L(b,\operatorname{mod}(a,b))-\operatorname{div}(a,b)R(b,\operatorname{mod}(a,b)) & 0< b\\
> > L(b,\operatorname{mod}(a,-b))+\operatorname{div}(a,-b)R(b,\operatorname{mod}(a,-b)) & b<0\\
> > 0 & b=0.
> > \end{cases} \\\end{split}\]

Let’s practice evaluating the \(\operatorname{gcd}(a,b)\), \(L(a,b)\) and \(R(a,b)\)
functions jointly, on the same example (\(a=-21\), \(b=15\)) as
in Definition 6.7.1.

Since we will need both \(\operatorname{div}\) and \(\operatorname{mod}\) down the
recursion, it is convenient to calculate them jointly in advance:

\[\begin{split}-21&=9+15\cdot -2\\
15&=6+9\cdot 1\\
9&=3+6\cdot 1\\
6&=0+3\cdot 2.\end{split}\]

(This table is a shorthand record that \(\operatorname{div}(-21,15)=-2\) and
\(\operatorname{mod}(-21,15)=9\), and so on.) We conclude immediately that

\[\begin{split}\operatorname{gcd}(-21,15)&=\operatorname{gcd}(15,9)\\
&=\operatorname{gcd}(9,6)\\
&=\operatorname{gcd}(6,3)\\
&=\operatorname{gcd}(3,0)\\
&=3,\end{split}\]

as before. To calculate \(L(a,b)\) and \(R(a,b)\), it is convenient to work backwards:

\[ \begin{align}\begin{aligned}L(3,0)&=1
&
\qquad R(3,0)&=0\\L(6,3)&=R(3,0)
&
\qquad R(6,3)&=L(3,0)-\operatorname{div}(6,3)R(3,0)\\&=0
&
\qquad&=1-2\cdot 0\\&
&
\qquad&=1\\L(9,6)&=R(6,3)
&
\qquad R(9,6)&=L(6,3)-\operatorname{div}(9,6)R(6,3)\\&=1
&
\qquad&=0-1\cdot 1\\&
&
\qquad&=-1\\L(15,9)&=R(9,6)
&
\qquad R(15,9)&=L(9,6)-\operatorname{div}(15,9)R(9,6)\\&=-1
&
\qquad&=1-1\cdot -1\\&
&
\qquad&=2\\L(-21,15)&=R(15,9)
&
\qquad R(-21,15)&=L(15,9)-\operatorname{div}(-21,15)R(15,9)\\&=2
&
\qquad&=-1-(-2)\cdot 2\\&
&
\qquad&=3\end{aligned}\end{align} \]

In Lean this mutual-recursion definition looks similar to the mutual-induction proof in
Example 6.7.3; like that example it is enclosed in a block marked
`mutual`.

```lean
mutual

def L (a b : ℤ) : ℤ :=
  if 0 < b then
    R b (fmod a b)
  else if b < 0 then
    R b (fmod a (-b))
  else if 0 ≤ a then
    1
  else
    -1

def R (a b : ℤ) : ℤ :=
  if 0 < b then
    L b (fmod a b) - (fdiv a b) * R b (fmod a b)
  else if b < 0 then
    L b (fmod a (-b)) + (fdiv a (-b)) * R b (fmod a (-b))
  else
    0

end
termination_by L a b => b ; R a b => b
```

Sanity check: does the Lean definition agree with our computation by hand of \(L(-21,15)\) and
\(R(-21,15)\)?

```lean
#eval L (-21) 15 -- infoview displays `2`
#eval R (-21) 15 -- infoview displays `3`
```

#### 6.7.5. Example

The reason for making the definitions \(L(a,b)\) and \(R(a,b)\) is that they satisfy the
following identity.

> **Theorem:**
>
> For all integers \(a\) and \(b\),
>
> \[L(a,b)a+R(a,b)b=\operatorname{gcd}(a,b).\]

> **Proof:**
>
> We prove this by strong induction on \(b\). Suppose that for all
> integers \(x\) and \(y\) with \(|y|<|b|\), it is true that
> \(0 \le \operatorname{gcd}(x, y)\).
>
> **Case 1** (\(0<b\)): Let \(q=\operatorname{div}(a,b)\) and let
> \(r=\operatorname{mod}(a,b)\), so that \(a=r+bq\) (by Example 6.6.2).
>
> Then by the recurrence definitions,
>
> \[\begin{split}\operatorname{gcd}(a,b)&=\operatorname{gcd}(b,r)\\
> L(a,b)&=R(b,r)\\
> R(a,b)&=L(b,r)-qR(b,r)\end{split}\]
>
> and by the inductive hypothesis \(L(b,r)b+R(b,r)r=\operatorname{gcd}(b,r)\). So
>
> \[\begin{split}L(a,b)a+R(a,b)b
> &=R(b,r)a+\left(L(b,r)-qR(b,r)\right)b\\
> &=R(b,r)(r+bq)+\left(L(b,r)-qR(b,r)\right)b\\
> &=R(b,r)r+L(b,r)b\\
> &=\operatorname{gcd}(b,r)\\
> &=\operatorname{gcd}(a,b).\end{split}\]
>
> **Case 2** (\(b<0\)): Let \(q=\operatorname{div}(a,-b)\) and let
> \(r=\operatorname{mod}(a,-b)\), so that \(a=r+(-b)q\) (by
> Example 6.6.2).
>
> Then by the recurrence definitions,
>
> \[\begin{split}\operatorname{gcd}(a,b)&=\operatorname{gcd}(b,r)\\
> L(a,b)&=R(b,r)\\
> R(a,b)&=L(b,r)+qR(b,r)\end{split}\]
>
> and by the inductive hypothesis \(L(b,r)b+R(b,r)r=\operatorname{gcd}(b,r)\). So
>
> \[\begin{split}L(a,b)a+R(a,b)b
> &=R(b,r)a+\left(L(b,r)+qR(b,r)\right)b\\
> &=R(b,r)(r+(-b)q)+\left(L(b,r)+qR(b,r)\right)b\\
> &=R(b,r)r+L(b,r)b\\
> &=\operatorname{gcd}(b,r)\\
> &=\operatorname{gcd}(a,b).\end{split}\]
>
> **Case 3** (\(b=0\), \(0\le a\)): Then by the recurrence definitions,
> \(\operatorname{gcd}(a,b)=a\), \(L(a,b)=1\) and \(R(a,b)=0\), so
>
> \[\begin{split}L(a,b)a+R(a,b)b
> &=1\cdot a+0\cdot b\\
> &=a\\
> &=\operatorname{gcd}(a,b).\end{split}\]
>
> **Case 4** (\(b=0\), \(a<0\)): Then by the recurrence definitions,
> \(\operatorname{gcd}(a,b)=-a\), \(L(a,b)=-1\) and \(R(a,b)=0\), so
>
> \[\begin{split}L(a,b)a+R(a,b)b
> &=-1\cdot -a+0\cdot b\\
> &=a\\
> &=\operatorname{gcd}(a,b).\end{split}\]

Here is the same proof in Lean.

```lean
theorem L_mul_add_R_mul (a b : ℤ) : L a b * a + R a b * b = gcd a b := by
  rw [R, L, gcd]
  split_ifs with h1 h2 <;> push_neg at *
  · -- case `0 < b`
    have IH := L_mul_add_R_mul b (fmod a b) -- inductive hypothesis
    have H : fmod a b + b * fdiv a b = a := fmod_add_fdiv a b
    set q := fdiv a b
    set r := fmod a b
    calc R b r * a + (L b r - q * R b r) * b
        = R b r * (r + b * q) + (L b r - q * R b r) * b:= by rw [H]
      _ = L b r * b + R b r * r := by ring
      _ = gcd b r := IH
  · -- case `b < 0`
    have IH := L_mul_add_R_mul b (fmod a (-b)) -- inductive hypothesis
    have H : fmod a (-b) + (-b) * fdiv a (-b) = a := fmod_add_fdiv a (-b)
    set q := fdiv a (-b)
    set r := fmod a (-b)
    calc  R b r * a + (L b r + q * R b r) * b
        =  R b r * (r + -b * q) + (L b r + q * R b r) * b := by rw [H]
      _ = L b r * b + R b r * r := by ring
      _ = gcd b r := IH
  · -- case `b = 0`, `0 ≤ a`
    ring
  · -- case `b = 0`, `a < 0`
    ring
termination_by L_mul_add_R_mul a b => b
```

#### 6.7.6. Example

We proved in Example 6.7.5 that for any integers \(a\) and
\(b\), the integers \(L(a,b)\) and \(R(a,b)\) satisfy

\[L(a,b)a+R(a,b)b=\operatorname{gcd}(a,b).\]

For example, \(L(7,5)=-2\) and \(R(7,5)=3\) and \(\operatorname{gcd}(7,5)=1\)

```lean
#eval L 7 5 -- infoview displays `-2`
#eval R 7 5 -- infoview displays `3`
#eval gcd 7 5 -- infoview displays `1`
```

and \((-2) \cdot 7 + 3 \cdot 5 = 1\).

But it is interesting to note that these are generally not the only pair of integers with that
property. For example,

\[\begin{split}3 \cdot 7 + (-4) \cdot 5 &= 1\\
(-7) \cdot 7 + 10 \cdot 5 &= 1\\
8 \cdot 7 + (-11) \cdot 5 &= 1\\
\ldots\end{split}\]

In applications, it is common to need only the property, not the particular construction via
\(L(a,b)\) and \(R(a,b)\). So we record this separately. This fact is known as
*Bézout’s identity*.

> **Corollary (Bézout’s identity):**
>
> Let \(a\) and \(b\) be integers. Then there exist integers \(x\) and \(y\), such
> that \(xa+yb=\operatorname{gcd}(a,b)\).

> **Proof:**
>
> By Example 6.7.5, the integers \(L(a,b)\) and \(R(a,b)\)
> have this property.

```lean
theorem bezout (a b : ℤ) : ∃ x y : ℤ, x * a + y * b = gcd a b := by
  use L a b, R a b
  apply L_mul_add_R_mul
```

#### 6.7.7. Exercises

1. Show that \(\operatorname{gcd}(a,b)\) is not just a *common divisor* of \(a\) and
   \(b\) (see Example 6.7.3), but their *greatest common divisor*: if
   \(d\) is an integer which divides both \(a\) and \(b\), then it divides
   \(\operatorname{gcd}(a,b)\).

   This problem does not need an induction; it is a direct corollary of Bézout’s identity
   (Example 6.7.6).

   ```lean
   theorem gcd_maximal {d a b : ℤ} (ha : d ∣ a) (hb : d ∣ b) : d ∣ gcd a b := by
     sorry
   ```

---



## 7. Number Theory {#m2001-7-number-theory}

> 📄 Source: https://hrmacbeth.github.io/math2001/07_Number_Theory.html

This chapter is different in style from the other chapters of the book. The facts here are
famous theorems, and their proofs require one-off, clever ideas. These particular ideas will not
turn up again on homework or exams. Think of this chapter instead as a capstone: we explore what
kinds of mathematical statements are in reach with the reasoning tools and theory we have developed
so far in the book.

### 7.1. Infinitely many primes

> **Theorem:**
>
> There are infinitely many prime numbers.

This is a an [extremely old](https://en.wikipedia.org/wiki/Euclid%27s_theorem) theorem, with the
first known proof written in Euclid’s *Elements* in about 300 BC.

> **Proof:**
>
> We show that for a given natural number \(N\), there exists a prime number \(p \geq N\).
>
> Consider \(N!\), the factorial of \(N\). By one of the exercises to
> [Section 6.2]](#m2001-recurrences), \(0<N!\), so \(2 \le N! + 1\). Therefore, by
> [Example 6.4.2]](#m2001-exists-prime-factor), there exists a prime number \(p\) which is a
> factor of \(N! + 1\).
>
> Let \(k\) be a natural number such that \(N! + 1=pk\). This number \(k\) cannot be
> zero, since if it were, we would have
>
> \[\begin{split}0&< N! + 1\\
> &=p \cdot 0\\
> &=0,\end{split}\]
>
> contradiction. Thus \(k>0\), so \(k\) is of the form \(l+1\) for some natural number
> \(l\), and \(N! + 1=p(l+1)\).
>
> We now show that \(p\) is not a factor of \(N!\), by showing that \(N!\) lies between
> the consecutive multiples \(pl\) and \(p(l+1)\) of \(p\). (This is the test from
> [Example 4.5.8]](#m2001-not-divisible-proof).) Indeed,
>
> \[\begin{split}pl+p&=p(l+1)\\
> &=N!+1\\
> &< N!+p,\end{split}\]
>
> so \(pl< N!\), and
>
> \[\begin{split}N!&< N!+1\\
> &=p(l+1).\end{split}\]
>
> If \(p\le N\), then by [Example 6.2.5]](#m2001-factorial), \(p\) would be a factor of
> \(N!\), which contradicts what we just proved. Therefore \(p > N\). This gives the
> required prime number greater than or equal to \(N\).

In Lean, the exercise to [Section 6.2]](#m2001-recurrences) has the name `factorial_pos`,
[Example 6.4.2]](#m2001-exists-prime-factor) has the name `exists_prime_factor`,
[Example 4.5.8]](#m2001-not-divisible-proof) has the name `Nat.not_dvd_of_exists_lt_and_lt`, and
[Example 6.2.5]](#m2001-factorial) has the name `dvd_factorial`.

```lean
example (N : ℕ) : ∃ p ≥ N, Prime p := by
  have hN0 : 0 < N ! := by apply factorial_pos
  have hN2 : 2 ≤ N ! + 1 := by addarith [hN0]
  -- `N! + 1` has a prime factor, `p`
  obtain ⟨p, hp, hpN⟩ : ∃ p : ℕ, Prime p ∧ p ∣ N ! + 1 := exists_prime_factor hN2
  have hp2 : 2 ≤ p
  · obtain ⟨hp', hp''⟩ := hp
    apply hp'
  obtain ⟨k, hk⟩ := hpN
  match k with
  | 0 => -- if `k` is zero, contradiction
    have k_contra :=
    calc 0 < N ! + 1 := by extra
      _ = p * 0 := hk
      _ = 0 := by ring
    numbers at k_contra
  | l + 1 => -- so `k = l + 1` for some `l`
    -- the key fact: `p` is not a factor of `N!`
    have key : ¬ p ∣ (N !)
    · apply Nat.not_dvd_of_exists_lt_and_lt (N !)
      use l
      constructor
      · have :=
        calc p * l + p = p * (l + 1) := by ring
          _ = N ! + 1 := by rw [hk]
          _ < N ! + p := by addarith [hp2]
        addarith [this]
      · calc N ! < N ! + 1 := by extra
          _ = p * (l + 1) := by rw [hk]
    -- so `p` is a prime number greater than or equal to `N`, as we sought
    use p
    constructor
    · obtain h_le | h_gt : p ≤ N ∨ N < p := le_or_lt p N
      · have : p ∣ (N !)
        · apply dvd_factorial
          · extra
          · addarith [h_le]
        contradiction
      · addarith [h_gt]
    · apply hp
```

### 7.2. Gauss’ and Euclid’s lemmas

> **Theorem (Gauss’ lemma):**
>
> Let \(a\), \(b\) and \(d\) be integers. Suppose that \(ab\) is a multiple of
> \(d\) and \(\operatorname{gcd}(a,d)=1\). Then \(b\) is a multiple of \(d\).

This lemma is the “final form” of the argument we have carried out in special cases in
[Example 3.5.1]](#m2001-bezout-prob1), [Example 3.5.2]](#m2001-bezout-prob2), etc. Like in those
special cases, the trick is to find a “Bézout identity” relating \(a\) and \(d\): multiples
of \(a\) and \(d\) which differ by 1. In the special cases we could find such multiples
explicitly. In the general situation the existence of such multiples is guaranteed by
[Example 6.7.6]](#m2001-bezout-gcd).

> **Proof:**
>
> By [Example 6.7.6]](#m2001-bezout-gcd) (Bézout’s identity) there exist integers \(x\) and
> \(y\) such that \(xa + yd = \operatorname{gcd}(a, d)\). Since \(ab\) is a multiple of
> \(d\), there exists an integer \(z\) such that \(ab=dz\). We then have that
>
> \[\begin{split}b &= b \cdot 1\\
> &= b \cdot \operatorname{gcd}(a, d)\\
> &= b(xa + yd)\\
> &= x(ab) + byd\\
> &= x(dz) + byd\\
> &= d(xz + by),\end{split}\]
>
> so \(b\) is a multiple of \(d\).

```lean
theorem gauss_lemma {d a b : ℤ} (h1 : d ∣ a * b) (h2 : gcd a d = 1) : d ∣ b := by
  obtain ⟨x, y, h⟩ := bezout a d
  obtain ⟨z, hz⟩ := h1
  use x * z + b * y
  calc b = b * 1 := by ring
    _ = b * gcd a d := by rw [h2]
    _ = b * (x * a + y * d) := by rw [h]
    _ = x * (a * b) + b * y * d := by ring
    _ = x * (d * z) + b * y * d := by rw [hz]
    _ = d * (x * z + b * y) := by ring
```

> **Theorem (Euclid’s lemma):**
>
> Let \(a\), \(b\) and \(p\) be natural numbers, with \(p\) prime. Suppose that
> \(ab\) is a multiple of \(p\). Then either \(a\) or \(b\) is a multiple of
> \(p\).

This lemma [also dates back](https://en.wikipedia.org/wiki/Euclid%27s_lemma) to Euclid’s
*Elements*.

> **Proof:**
>
> By [Example 6.7.2]](#m2001-gcd-nonneg), \(\operatorname{gcd}(a,p)\geq 0\), so
> \(\operatorname{gcd}(a,p)\) (a priori an integer) can be considered as a natural number. Let
> us call that natural number \(d\). Then
>
> - by [Example 6.7.3]](#m2001-common-divisor), \(d\mid a\) and \(d\mid p\);
> - (\(\star\)) by Gauss’ lemma, if \(p\mid ab\) and \(d=1\) then \(p \mid b\).
>
> A priori these divisibility statements are all statements about
> \(a\), \(b\), \(p\) and \(d\) considered as integers, but these are equivalent to
> the corresponding divisibility statements about natural numbers.
>
> We now begin the proof proper. Since \(p\) is prime and \(d\mid p\), either \(d=1\)
> or \(d=p\).
>
> **Case 1** (\(d=1\)): Then by (\(\star\)), \(p \mid b\).
>
> **Case 2** (\(d=p\)): Then since \(d\mid a\), \(p\mid a\).

To write this proof in Lean, we need some tricks to handle the interaction between integers and
natural numbers. These will not be used again and it’s fine not to read them very closely.

- When invoking lemmas about integers (`gcd_dvd_left`, `gcd_dvd_right`, `gauss_lemma`,
  `gcd_nonneg`) on natural number inputs, we *cast* the natural number inputs to integers, like
  `(a:ℤ)` – this is not always necessary but it avoids ambiguity. (The casts then display in the
  infoview with arrows, as `↑a`, etc.)
- Once we have the hypothesis `0 ≤ gcd (a:ℤ) (p:ℤ)`, we can use the Lean tactic `lift` to
  introduce a natural number `d` whose cast to the integers is `gcd (a:ℤ) (p:ℤ)`.
- Finally, we can run the tactic `norm_cast` on all our hypotheses, which converts statements
  about natural-numbers-cast-to-integers into the corresponding statements about natural numbers, if
  this is mathematically valid. For example, `↑d ∣ ↑a` is converted to `d ∣ a`.

```lean
theorem euclid_lemma {a b p : ℕ} (hp : Prime p) (H : p ∣ a * b) : p ∣ a ∨ p ∣ b := by
  -- write down everything we know about `gcd (a:ℤ) (p:ℤ)`
  have hap1 : gcd (a:ℤ) (p:ℤ) ∣ (a:ℤ) := gcd_dvd_left (a:ℤ) (p:ℤ)
  have hap2 : gcd (a:ℤ) (p:ℤ) ∣ (p:ℤ) := gcd_dvd_right (a:ℤ) (p:ℤ)
  have h_gauss : (p:ℤ) ∣ (a:ℤ) * (b:ℤ) → gcd (a:ℤ) (p:ℤ) = 1 → (p:ℤ) ∣ (b:ℤ) :=
    gauss_lemma
  have hgcd : 0 ≤ gcd (a:ℤ) (p:ℤ) := gcd_nonneg (a:ℤ) (p:ℤ)
  -- convert to `ℕ` facts
  lift gcd a p to ℕ using hgcd with d hd
  norm_cast at hap1 hap2 h_gauss
  -- actually prove the theorem
  dsimp [Prime] at hp
  obtain ⟨hp1, hp2⟩ := hp
  obtain hgcd_1 | hgcd_p : d = 1 ∨ d = p := hp2 d hap2
  · right
    apply h_gauss H hgcd_1
  · left
    rw [← hgcd_p]
    apply hap1
```

> **Corollary:**
>
> Let \(a\), \(p\) and \(k\) be natural numbers, with \(p\) prime and
> \(k\geq 1\). If \(a^k\) is a multiple of \(p\), then \(a\) is a multiple of
> \(p\).

> **Proof:**
>
> We prove this by induction on \(k\), starting at 1.
>
> **Base case:** If \(a^1\) is a multiple of \(p\), then since \(a^1=a\), we conclude
> that \(a\) is a multiple of \(p\).
>
> **Inductive step:** Let \(t\) be a natural number and suppose that if \(a^t\) is a
> multiple of \(p\) then \(a\) is. (\(\star\))
>
> Now suppose that \(a^{t+1}\) is a multiple of \(p\). Since \(a^{t+1}=a\cdot a^t\),
> this means that \(a \cdot a^t\) is a multiple of \(p\).
>
> Therefore, by Euclid’s lemma, either \(a\) is a multiple of \(p\), in which case we are
> done, or \(a^t\) is a multiple of \(p\), in which case by the inductive hypothesis
> (\(\star\)) we are done.

```lean
theorem euclid_lemma_pow (a k p : ℕ) (hp : Prime p) (hk : 1 ≤ k) (H : p ∣ a ^ k) :
    p ∣ a := by
  induction_from_starting_point k, hk with t ht IH
  · have ha : a ^ 1 = a := by ring
    rw [ha] at H
    apply H
  have ha : a ^ (t + 1) = a * a ^ t := by ring
  rw [ha] at H
  have key : p ∣ a ∨ p ∣ a ^ t := euclid_lemma hp H
  obtain h1 | h2 := key
  · apply h1
  · apply IH
    apply h2
```

### 7.3. The square root of two

> **Theorem:**
>
> There do not exist natural numbers \(a\) and \(b\) with \(b\ne 0\), for which
> \(a^2=2b^2\).

This is another theorem which dates back to the ancient Greeks, in this case to the
[followers of Pythagoras](https://en.wikipedia.org/wiki/Square_root_of_2), c. 450 BC.

This theorem constitutes most of the work of proving that the square root of two is *irrational*,
that is, not a rational number (\(\mathbb{Q}\)). We can’t draw that conclusion here since we
haven’t yet defined rational numbers precisely, but we will circle back to it later in the book.

> **Proof:**
>
> We will show a logically equivalent fact: that for all natural numbers \(a\) and \(b\)
> with \(b\ne 0\), it holds that \(a^2\ne 2b^2\). We prove this by strong induction on
> \(b\).
>
> Let \(a\) and \(b\) be natural numbers and suppose that for all natural numbers
> \(r\), \(s\) with \(s<b\) and \(s\ne 0\), it holds that \(r^2\ne 2s^2\).
>
> Suppose that \(a^2= 2b^2\). Then \(a^2\) is even, so by one of the exercises to
> [Section 6.1]](#m2001-induction-intro), \(a\) is even. Let \(k\) be a natural number
> such that \(a=2k\). Then
>
> \[\begin{split}2 b ^ 2 &= a ^ 2\\
> &= (2 k) ^ 2 \\
> & = 2 (2 k ^ 2),\end{split}\]
>
> so \(b^2=2k^2\). Then
>
> \[\begin{split}0 &< b^2\\
> &=2k^2\\
> &=k(2k),\end{split}\]
>
> so \(k>0\), so \(k\ne 0\).
>
> We therefore invoke the inductive hypothesis with \(r=b\), \(s=k\). (Note that
>
> \[\begin{split}k^2&< k^2+k^2\\
> &=2k^2\\
> &=b^2,\end{split}\]
>
> so \(k<b\), so the induction is well-founded.) The inductive hypothesis gives that
> \(b^2\ne 2k^2\), contradiction. So it cannot be that \(a^2\ne 2b^2\).

In Lean we break this argument into three pieces. The lemma `irrat_aux_wf` is the
justification for the well-foundedness of the strong induction. Like in
[Example 6.7.1]](#m2001-euclidean-algorithm-def), we tag the well-foundedness lemma with
`@[decreasing]` to make it available later for the strong induction.

```lean
@[decreasing] theorem irrat_aux_wf (b k : ℕ) (hb : k ≠ 0) (hab : b ^ 2 = 2 * k ^ 2) :
    k < b := by
  have h :=
  calc k ^ 2 < k ^ 2 + k ^ 2 := by extra
    _ = 2 * k ^ 2 := by ring
    _ = b ^ 2 := by rw [hab]
  cancel 2 at h
```

The lemma `irrat_aux` is the claim which is proved by strong induction. It contains the central
argument of the proof. In Lean, the exercise to [Section 6.1]](#m2001-induction-intro) which is used
in this step has the name `Nat.even_of_pow_even`.

```lean
theorem irrat_aux (a b : ℕ) (hb : b ≠ 0) : a ^ 2 ≠ 2 * b ^ 2 := by
  intro hab
  have H : Nat.Even a
  · apply Nat.even_of_pow_even (n := 2)
    use b ^ 2
    apply hab
  obtain ⟨k, hk⟩ := H
  have hbk :=
    calc 2 * b ^ 2 = a ^ 2 := by rw [hab]
      _ = (2 * k) ^ 2 := by rw [hk]
      _ = 2 * (2 * k ^ 2) := by ring
  cancel 2 at hbk
  have hk' :=
    calc 0 < b ^ 2 := by extra
      _ = 2 * k ^ 2 := by rw [hbk]
      _ = k * (2 * k) := by ring
  cancel 2 * k at hk'
  have hk'' : k ≠ 0 := ne_of_gt hk'
  have IH := irrat_aux b k -- inductive hypothesis
  have : b ^ 2 ≠ 2 * k ^ 2 := IH hk''
  contradiction
termination_by _ => b
```

Finally, the main theorem is actually logically equivalent to `irrat_aux`, and its proof consists
of establishing this logical equivalence.

```lean
example : ¬ ∃ a b : ℕ, b ≠ 0 ∧ a ^ 2 = 2 * b ^ 2 := by
  intro h
  obtain ⟨a, b, hb, hab⟩ := h
  have := irrat_aux a b hb
  contradiction
```

That was fun … let’s do it again! Here is a different proof of the same theorem. Or, *nearly*
the same theorem – this proof works better for integers. (It would be fiddly but not fundamentally
difficult to convert the integer version to the natural number version or vice versa, using the
techniques from Section 7.2.)

> **Theorem:**
>
> There do not exist integers \(a\) and \(b\) with \(b\ne 0\), for which
> \(a^2=2b^2\).

> **Proof:**
>
> Suppose that \(a\) and \(b\) were integers with \(b\ne 0\) and \(a^2=2b^2\).
>
> Denote by \(d\) the greatest common divisor of \(a\) and \(b\).
> By [Example 6.7.3]](#m2001-common-divisor), \(d\mid a\) and
> \(d\mid b\). Let \(k\) and \(l\) be integers such that
> \(a=dk\) and \(b=dl\).
>
> Also by [Example 6.7.6]](#m2001-bezout-gcd) (Bézout’s identity) there exist integers \(x\) and
> \(y\) such that \(xa+yb=d\).
>
> The key calculation is the following (\(\dagger\)).:
>
> \[\begin{split}(2k y + lx) ^ 2 \cdot d^2
> &= (2(dk) y + (d l) x) ^ 2 \\
> &= (2 a y + b x) ^ 2 \\
> &= 2 (x a + y b) ^ 2 + (x ^ 2 - 2 y ^ 2) (b ^ 2 - 2 a ^ 2) \\
> &= 2 d ^ 2 + (x ^ 2 - 2 y ^ 2) (b ^ 2 - b ^ 2) \\
> &= 2 \cdot d^2 \\\end{split}\]
>
> We have that \(d\ne 0\), since if not,
>
> \[\begin{split}b &=dl\\
> &=0\cdot l\\
> &=0,\end{split}\]
>
> contradiction. Hence by (\(\dagger\)),
>
> \[(2k y + lx) ^ 2 = 2.\]
>
> But by [Example 2.3.5]](#m2001-int-sq-ne-two), no integer squares to 2, so this is impossible.

Note the use of Brahmagupta’s identity ([Example 1.1.3]](#m2001-id6)) above in the middle of the key
calculation.

```lean
example : ¬ ∃ a b : ℤ, b ≠ 0 ∧ b ^ 2 = 2 * a ^ 2 := by
  intro h
  obtain ⟨a, b, hb, hab⟩ := h
  have Ha : gcd a b ∣ a := gcd_dvd_left a b
  have Hb : gcd a b ∣ b := gcd_dvd_right a b
  obtain ⟨k, hk⟩ := Ha
  obtain ⟨l, hl⟩ := Hb
  obtain ⟨x, y, h⟩ := bezout a b
  set d := gcd a b
  have key :=
  calc (2 * k * y + l * x) ^ 2 * d ^ 2
      = (2 * (d * k) * y + (d * l) * x) ^ 2 := by ring
    _ = (2 * a * y + b * x) ^ 2 := by rw [hk, hl]
    _ = 2 * (x * a + y * b) ^ 2 + (x ^ 2 - 2 * y ^ 2) * (b ^ 2 - 2 * a ^ 2) := by ring
    _ = 2 * d ^ 2 + (x ^ 2 - 2 * y ^ 2) * (b ^ 2 - b ^ 2) := by rw [h, hab]
    _ = 2 * d ^ 2 := by ring
  have hd : d ≠ 0
  · intro hd
    have :=
    calc b = d * l := hl
      _ = 0 * l := by rw [hd]
      _ = 0 := by ring
    contradiction
  cancel d ^ 2 at key
  have := sq_ne_two (2 * k * y + l * x)
  contradiction
```

---



## 8. Functions {#m2001-8-functions}

> 📄 Source: https://hrmacbeth.github.io/math2001/08_Functions.html

So far in the book, we have studied properties of numbers (whether a number is odd,
positive, prime; whether one number is divisible by another) and operations on
numbers (addition, greatest common divisor).

In this chapter we go up a level of abstraction, and study properties of and
operations on functions. These new properties include: whether a function is
*injective*, *surjective*, *bijective*; whether one function is *inverse* to
another; and the operation of *composition*.

We also expand our horizon beyond the numeric types (\(\mathbb{N}\),
\(\mathbb{Z}\), \(\mathbb{Q}\), \(\mathbb{R}\)) which have formed the
setting of the early part of the book. We now start to work with a broader range
of types, including function types, finite inductive types, and product types.

### 8.1. Injectivity and surjectivity

#### 8.1.1. Example

We have studied particular functions before. For example, the Fibonacci sequence from
[Example 6.3.3]](#m2001-fibonacci) is a function from \(\mathbb{N}\) to \(\mathbb{Z}\): it
takes in a natural number, for example 5, and gives out an integer, in this case 8 (the index-5
term of the sequence).

```lean
def F : ℕ → ℤ
  | 0 => 1
  | 1 => 1
  | n + 2 => F (n + 1) + F n

#eval F 5 -- infoview displays `8`
```

The *domain* of a function is the type where it takes its input values, and the *codomain* of a
function is the type where it takes its output values. For example, the domain of the Fibonacci
sequence is \(\mathbb{N}\) and its codomain is \(\mathbb{Z}\). The type of functions with
domain \(\mathbb{N}\) and codomain \(\mathbb{Z}\) is denoted
\(\mathbb{N}\to \mathbb{Z}\). Lean will confirm for us that the Fibonacci sequence `F` has
this type:

```lean
#check @F -- infoview displays `F : ℕ → ℤ`
```

Another way to define a function is by a closed formula. For example,
“let \(q:\mathbb{R}\to \mathbb{R}\) be the function defined by, \(q(x)=x+3\).” In Lean we
can write this as

```lean
def q (x : ℝ) : ℝ := x + 3
```

This function has domain \(\mathbb{R}\) and codomain \(\mathbb{R}\) – I even said when I
was defining it that I was making \(q\) to have type \(\mathbb{R}\to \mathbb{R}\). Let’s
check that this is indeed the type of the Lean object:

```lean
#check @q -- infoview displays `q : ℝ → ℝ`
```

A third way to define a function, if we expect to use it just once and don’t want to waste a name on
it, is by using the notation \(\mapsto\): we can refer to “the function \(x \mapsto x ^ 2\)
from \(\mathbb{R}\) to \(\mathbb{R}\)”. Here is the same notation in Lean:

```lean
#check fun (x : ℝ) ↦ x ^ 2 -- infoview displays `fun x ↦ x ^ 2 : ℝ → ℝ`
```

#### 8.1.2. Definition

Now that we have the notation \(X\to Y\) for “the type of functions from \(X\) to
\(Y\)”, we can introduce properties of functions, in the same way that we have previously
introduced properties (like “odd” and “prime”) of numbers. Here is our first one.

> **Definition:**
>
> A function \(f : X \to Y\) is *injective*, if for all \(x\_1\) and \(x\_2\) of type
> \(X\), if \(f(x\_1)=f(x\_2)\), then \(x\_1=x\_2\).

```lean
def Injective (f : X → Y) : Prop := ∀ {x1 x2 : X}, f x1 = f x2 → x1 = x2
```

#### 8.1.3. Example

> **Problem:**
>
> Show that the function \(q:\mathbb{R}\to\mathbb{R}\) from
> Example 8.1.1 is injective.

> **Solution:**
>
> Let \(x\_1\) and \(x\_2\) be real numbers and suppose that \(q(x\_1)=q(x\_2)\). Then
> \(x\_1+1=x\_2+1\), so \(x\_1=x\_2\).

Here is this solution in Lean. Note that after unfolding the definition “injective” using the
command `dsimp [Injective]`, the goal state displays

```
⊢ ∀ ⦃x1 x2 : ℝ⦄, q x1 = q x2 → x1 = x2
```

This is the “injective” definition specialized to the problem at hand.

```lean
example : Injective q := by
  dsimp [Injective]
  intro x1 x2 h
  dsimp [q] at h
  addarith [h]
```

#### 8.1.4. Example

> **Problem:**
>
> Show that the function \(x \mapsto x ^ 2\) from \(\mathbb{R}\) to \(\mathbb{R}\)
> is not injective.

> **Solution:**
>
> We must show that there exist real numbers \(x\_1\) and \(x\_2\), such that
> \(x\_1{}^2=x\_2{}^2\) and \(x\_1\ne x\_2\). Indeed, -1 and 1 have these properties.

The first sentence of that proof was a negation-normalization: I unfolded the “injective” definition
and restated its negation in a logically equivalent but more convenient form. Recall that in Lean
we do this using the tactic `push_neg`. Here is the goal state after the tactic `push_neg` has
been used:

```
⊢ ∃ x1 x2, x1 ^ 2 = x2 ^ 2 ∧ x1 ≠ x2
```

And here is the full Lean proof.

```lean
example : ¬ Injective (fun x : ℝ ↦ x ^ 2) := by
  dsimp [Injective]
  push_neg
  use -1, 1
  constructor
  · numbers
  · numbers
```

#### 8.1.5. Definition

> **Definition:**
>
> A function \(f : X \to Y\) is *surjective*, if for all \(y\) of type \(Y\), there
> exists \(x\) of type \(X\), such that \(f(x)=y\).

```lean
def Surjective (f : X → Y) : Prop := ∀ y : Y, ∃ x : X, f x = y
```

#### 8.1.6. Example

> **Problem:**
>
> Consider the function \(s:\mathbb{Q}\to\mathbb{Q}\) defined by, \(s(a)=3a+2\). Show that
> \(s\) is surjective.

> **Solution:**
>
> Let \(y\) be a rational number. Then
>
> \[\begin{split}s\left(\frac{y-2}{3}\right)&=3\left(\frac{y-2}{3}\right)+2\\
> &=y.\end{split}\]

Here is the solution in Lean. The goal state after unfolding the definition of “surjective” is

```
⊢ ∀ (y : ℚ), ∃ x, s x = y
```

which confirms what we need to prove and why what we wrote in the text proof above is sufficient.

```lean
def s (a : ℚ) : ℚ := 3 * a + 2

example : Surjective s := by
  dsimp [Surjective]
  intro y
  use (y - 2) / 3
  calc s ((y - 2) / 3) = 3 * ((y - 2) / 3) + 2 := by rw [s]
    _ = y := by ring
```

#### 8.1.7. Example

> **Problem:**
>
> Show that the function \(x \mapsto x ^ 2\) from \(\mathbb{R}\) to \(\mathbb{R}\)
> is not surjective.

> **Solution:**
>
> We will show that there exists a real number \(y\), such that for all real numbers \(x\),
> \(x^2\ne y\).
>
> Indeed, let us show that -1 has this property. Let \(x\) be a real number. Then
>
> \[\begin{split}-1&<0\\
> &\le x^2,\end{split}\]
>
> so \(x^2\ne -1\).

As in Example 8.1.4, the first sentence constitutes a
negation-normalization of the definition of “surjective” in this context. Effectively we are stating
what would be the goal state in the Lean proof after `push_neg`.

```
⊢ ∃ y, ∀ (x : ℝ), x ^ 2 ≠ y
```

And here is the full Lean proof.

```lean
example : ¬ Surjective (fun x : ℝ ↦ x ^ 2) := by
  dsimp [Surjective]
  push_neg
  use -1
  intro x
  apply ne_of_gt
  calc -1 < 0 := by numbers
    _ ≤ x ^ 2 := by extra
```

#### 8.1.8. Example

We have so far seen numeric types, like the integers \(\mathbb{Z}\) and the real numbers
\(\mathbb{R}\), and function types, like \(\mathbb{Z}\to \mathbb{R}\) (the functions from
\(\mathbb{Z}\) to \(\mathbb{R}\)).

Another way to make a type is as a finite set of options. Finite types are useful for conceptual
examples, because everything is explicit and checkable.

```lean
inductive Musketeer
  | athos
  | porthos
  | aramis
  deriving DecidableEq
```

Here is how to define a function whose domain is a given finite inductive type.

```lean
def f : Musketeer → Musketeer
  | athos => aramis
  | porthos => aramis
  | aramis => athos
```

> **Problem:**
>
> Show that the function \(f\) is not injective.

![_images/musketeer1a.png](https://hrmacbeth.github.io/math2001/_images/musketeer1a.png)

```lean
example : ¬ Injective f := by
  dsimp [Injective]
  push_neg
  use athos, porthos
  dsimp [f] -- optional
  exhaust
```

The tactic `exhaust` is new here. At the place where it is used, the goal state is

```
aramis = aramis ∧ athos ≠ porthos
```

that is,

```
True ∧ ¬ False
```

which is logically equivalent to `True`. The tactic `exhaust` can do this kind of propositional
logic reasoning, up to arbitrary complexity.

In particular, `exhaust` can prove any (true) variable-free statement in an inductive type, and
that’s how we’ll use it in this chapter. We start making more serious use of `exhaust` in
[Chapter 9](https://hrmacbeth.github.io/math2001/09_Sets.html#sets).

#### 8.1.9. Example

> **Problem:**
>
> Show that the function \(f\) defined in Example 8.1.8 is not surjective.

![_images/musketeer1b.png](https://hrmacbeth.github.io/math2001/_images/musketeer1b.png)

We can case-check on a variable `a` in a finite inductive type using the tactic `cases`.

```lean
example : ¬ Surjective f := by
  dsimp [Surjective]
  push_neg
  use porthos
  intro a
  cases a
  · exhaust
  · exhaust
  · exhaust
```

Such proofs can become repetitive, and you may wish to use a trick we have seen before (for example
[Example 4.4.4]](#m2001-prime-test), [Example 4.5.9]](#m2001-better-prime-test),
[Example 6.6.2]](#m2001-mod-add-div), [Example 6.7.2]](#m2001-gcd-nonneg)): when a tactic such as
`cases` generates many goals which can all be proved by the same tactic, you can write
`<;>` to apply the tactic to all those goals.

```lean
-- better (more automated) version of the previous proof
example : ¬ Surjective f := by
  dsimp [Surjective]
  push_neg
  use porthos
  intro a
  cases a <;> exhaust
```

You might also like to check that the `cases a <;> exhaust` line is necessary; `exhaust` can’t close
the goal on its own. As discussed in Example 8.1.8, `exhaust` can prove any
(true) *variable-free* statement in an inductive type, but before the `cases a <;> exhaust` line the
goal state is

```
a : Musketeer
⊢ f a ≠ porthos
```

which contains a variable, `a`.

#### 8.1.10. Example

Let \(g\) be the following function from the Musketeer type to itself:

```lean
def g : Musketeer → Musketeer
  | athos => porthos
  | porthos => aramis
  | aramis => athos
```

![_images/musketeer2.png](https://hrmacbeth.github.io/math2001/_images/musketeer2.png)

> **Problem:**
>
> Show that the function \(g\) is injective.

There are a lot of cases in this proof – \(3 \times 3 = 9\), to be precise. Fortunately
`exhaust` can prove all of them!

```lean
example : Injective g := by
  dsimp [Injective]
  intro x1 x2 hx
  cases x1 <;> cases x2 <;> exhaust
```

#### 8.1.11. Example

> **Problem:**
>
> Show that the function \(g\) defined in Example 8.1.10 is surjective.

```lean
example : Surjective g := by
  dsimp [Surjective]
  intro y
  cases y
  · use aramis
    exhaust
  · use athos
    exhaust
  · use porthos
    exhaust
```

#### 8.1.12. Example

We finish with a relatively hard example. The proof here is efficient and self-contained but not
particularly well-motivated. For an alternative (perhaps more intuitive) approach, combine the last
exercise in this section (the one about *strictly monotone* functions) with the idea from
[Example 2.1.8]](#m2001-cube-inequality).

> **Problem:**
>
> Show that the function \(x \mapsto x ^ 3\) from \(\mathbb{R}\) to \(\mathbb{R}\) is
> injective.

> **Solution:**
>
> Let \(x\_1\) and \(x\_2\) be real numbers and suppose that \(x\_1{}^3=x\_2{}^3\). Then
>
> \[\begin{split}(x\_1-x\_2)(x\_1{}^2+x\_1x\_2+x\_2{}^2)&=x\_1{}^3-x\_2{}^3\\
> &=x\_1{}^3-x\_1{}^3\\
> &=0,\end{split}\]
>
> so either \(x\_1-x\_2=0\), in which case we are done, or \(x\_1{}^2+x\_1x\_2+x\_2{}^2=0\), which
> we henceforth assume.
>
> We now consider a further case split according to whether \(x\_1=0\).
>
> **Case 1** (\(x\_1=0\)): Then
>
> \[\begin{split}x\_2{}^3&=x\_1{}^3\\
> &=0^3\\&=0,\end{split}\]
>
> so \(x\_2=0\) also. Thus \(x\_1=0=x\_2\) as required.
>
> **Case 2** (\(x\_1\ne 0\)): Then
>
> \[\begin{split}0&< x\_1{}^2+\left((x\_1+x\_2)^2+x\_2{}^2\right)\\
> &=2(x\_1{}^2+x\_1x\_2+x\_2{}^2)\\
> &=2\cdot 0\\
> &=0,\end{split}\]
>
> contradiction.

```lean
example : Injective (fun (x:ℝ) ↦ x ^ 3) := by
  intro x1 x2 hx
  dsimp at hx
  have H : (x1 - x2) * (x1 ^ 2 + x1 * x2 + x2 ^ 2) = 0
  · calc (x1 - x2) * (x1 ^ 2 + x1 * x2 + x2 ^ 2) = x1 ^ 3 - x2 ^ 3 := by ring
      _ = x1 ^ 3 - x1 ^ 3 := by rw [hx]
      _ = 0 := by ring
  rw [mul_eq_zero] at H
  obtain H1 | H2 := H
  · -- case 1: x1 - x2 = 0
    addarith [H1]
  · -- case 2: x1 ^2 + x1 * x2 + x2 ^ 2  = 0
    by_cases hx1 : x1 = 0
    · -- case 2a: x1 = 0
      have hx2 :=
      calc x2 ^ 3 = x1 ^ 3 := by rw [hx]
        _ = 0 ^ 3 := by rw [hx1]
        _ = 0 := by numbers
      cancel 3 at hx2
      calc x1 = 0 := by rw [hx1]
        _ = x2 := by rw [hx2]
    · -- case 2b: x1 ≠ 0
      have :=
      calc 0 < x1 ^ 2 + ((x1 + x2) ^ 2 + x2 ^ 2) := by extra
          _ = 2 * (x1 ^ 2 + x1 * x2 + x2 ^ 2) := by ring
          _ = 2 * 0 := by rw [H2]
          _ = 0 := by ring
      numbers at this -- contradiction!
```

#### 8.1.13. Exercises

1. Prove or disprove that the function \(x \mapsto x-12\) from \(\mathbb{Q}\) to
   \(\mathbb{Q}\) is injective.

   (If you think it’s true, prove it, by solving the first version below. If you think it’s false,
   solve the second version.)

   ```lean
   example : Injective (fun (x : ℚ) ↦ x - 12) := by
     sorry

   example : ¬ Injective (fun (x : ℚ) ↦ x - 12) := by
     sorry
   ```
2. Prove or disprove that the function \(x \mapsto 3\) from \(\mathbb{R}\) to
   \(\mathbb{R}\) is injective.

   (If you think it’s true, prove it, by solving the first version below. If you think it’s false,
   solve the second version.)

   ```lean
   example : Injective (fun (x : ℝ) ↦ 3) := by
     sorry

   example : ¬ Injective (fun (x : ℝ) ↦ 3) := by
     sorry
   ```
3. Prove or disprove that the function \(x \mapsto 3x-1\) from \(\mathbb{ℚ}\) to
   \(\mathbb{ℚ}\) is injective.

   (If you think it’s true, prove it, by solving the first version below. If you think it’s false,
   solve the second version.)

   ```lean
   example : Injective (fun (x : ℚ) ↦ 3 * x - 1) := by
     sorry

   example : ¬ Injective (fun (x : ℚ) ↦ 3 * x - 1) := by
     sorry
   ```
4. Prove or disprove that the function \(x \mapsto 3x-1\) from \(\mathbb{Z}\) to
   \(\mathbb{Z}\) is injective.

   (If you think it’s true, prove it, by solving the first version below. If you think it’s false,
   solve the second version.)

   ```lean
   example : Injective (fun (x : ℤ) ↦ 3 * x - 1) := by
     sorry

   example : ¬ Injective (fun (x : ℤ) ↦ 3 * x - 1) := by
     sorry
   ```
5. Prove or disprove that the function \(x \mapsto 2x\) from \(\mathbb{R}\) to
   \(\mathbb{R}\) is surjective.

   (If you think it’s true, prove it, by solving the first version below. If you think it’s false,
   solve the second version.)

   ```lean
   example : Surjective (fun (x : ℝ) ↦ 2 * x) := by
     sorry

   example : ¬ Surjective (fun (x : ℝ) ↦ 2 * x) := by
     sorry
   ```
6. Prove or disprove that the function \(x \mapsto 2x\) from \(\mathbb{Z}\) to
   \(\mathbb{Z}\) is surjective.

   (If you think it’s true, prove it, by solving the first version below. If you think it’s false,
   solve the second version.)

   ```lean
   example : Surjective (fun (x : ℤ) ↦ 2 * x) := by
     sorry

   example : ¬ Surjective (fun (x : ℤ) ↦ 2 * x) := by
     sorry
   ```
7. Prove or disprove that the function \(n \mapsto n^2\) from \(\mathbb{N}\) to
   \(\mathbb{N}\) is surjective.

   (If you think it’s true, prove it, by solving the first version below. If you think it’s false,
   solve the second version.)

   ```lean
   example : Surjective (fun (n : ℕ) ↦ n ^ 2) := by
     sorry

   example : ¬ Surjective (fun (n : ℕ) ↦ n ^ 2) := by
     sorry
   ```
8. Consider the following finite inductive type White, and the following function \(h\) from
   the Musketeer type (see Example 8.1.8) to the White type. Prove or disprove
   that the function \(h\) is injective.

   (If you think it’s true, prove it, by solving the first version below. If you think it’s false,
   solve the second version.)

   ```lean
   inductive White
     | meg
     | jack
     deriving DecidableEq

   open White

   def h : Musketeer → White
     | athos => jack
     | porthos => meg
     | aramis => jack

   example : Injective h := by
     sorry

   example : ¬ Injective h := by
     sorry
   ```
9. Prove or disprove that the function \(h\) from the previous example is surjective.

   (If you think it’s true, prove it, by solving the first version below. If you think it’s false,
   solve the second version.)

   ```lean
   example : Surjective h := by
     sorry

   example : ¬ Surjective h := by
     sorry
   ```
10. Consider the following function \(l\) from the White type (see the previous two problems)
    to the Musketeer type (see Example 8.1.8). Prove or
    disprove that the function \(l\) is injective.

    (If you think it’s true, prove it, by solving the first version below. If you think it’s false,
    solve the second version.)

    ```lean
    def l : White → Musketeer
      | meg => aramis
      | jack => porthos

    example : Injective l := by
      sorry

    example : ¬ Injective l := by
      sorry
    ```
11. Prove or disprove that the function \(l\) from the previous example is surjective.

    (If you think it’s true, prove it, by solving the first version below. If you think it’s false,
    solve the second version.)

    ```lean
    example : Surjective l := by
      sorry

    example : ¬ Surjective l := by
      sorry
    ```
12. Let \(f : X \to Y\) be a function. Show that \(f\) is injective if and only if for all
    \(x\_1\) and \(x\_2\) of type \(X\), if \(x\_1\ne x\_2\) then
    \(f(x\_1)\ne f(x\_2)\).

    You will need to use a tactic (such as `push_neg` or `by_cases`) which is capable of
    handling the subtler negations.

    ```lean
    example (f : X → Y) : Injective f ↔ ∀ x1 x2 : X, x1 ≠ x2 → f x1 ≠ f x2 := by
      sorry
    ```
13. Prove or disprove that for all functions \(f:\mathbb{Q}\to \mathbb{Q}\), if \(f\) is
    injective, then the function \(x \mapsto f(x)+1\) from \(\mathbb{Q}\) to
    \(\mathbb{Q}\) is also injective.

    ```lean
    example : ∀ (f : ℚ → ℚ), Injective f → Injective (fun x ↦ f x + 1) := by
      sorry

    example : ¬ ∀ (f : ℚ → ℚ), Injective f → Injective (fun x ↦ f x + 1) := by
      sorry
    ```
14. Prove or disprove that for all functions \(f:\mathbb{Q}\to \mathbb{Q}\), if \(f\) is
    injective, then the function \(x \mapsto f(x)+x\) from \(\mathbb{Q}\) to
    \(\mathbb{Q}\) is also injective.

    ```lean
    example : ∀ (f : ℚ → ℚ), Injective f → Injective (fun x ↦ f x + x) := by
      sorry

    example : ¬ ∀ (f : ℚ → ℚ), Injective f → Injective (fun x ↦ f x + x) := by
      sorry
    ```
15. Prove or disprove that for all functions \(f:\mathbb{Z}\to \mathbb{Z}\), if \(f\) is
    surjective, then the function \(x \mapsto 2f(x)\) from \(\mathbb{Z}\) to
    \(\mathbb{Z}\) is also surjective.

    ```lean
    example : ∀ (f : ℤ → ℤ), Surjective f → Surjective (fun x ↦ 2 * f x) := by
      sorry

    example : ¬ ∀ (f : ℤ → ℤ), Surjective f → Surjective (fun x ↦ 2 * f x) := by
      sorry
    ```
16. Prove or disprove that for all real numbers \(c\), the function \(x\mapsto cx\) from
    \(\mathbb{R}\) to \(\mathbb{R}\) is surjective.

    ```lean
    example : ∀ c : ℝ, Surjective (fun x ↦ c * x) := by
      sorry

    example : ¬ ∀ c : ℝ, Surjective (fun x ↦ c * x) := by
      sorry
    ```
17. Let \(f:\mathbb{Q}\to\mathbb{Q}\) be a function which is *strictly monotone*; that is, for
    all real numbers \(x\) and \(y\) with \(x<y\), it is true that \(f(x)<f(y)\).
    Prove that \(f\) is injective.

    You may wish to use the lemma `lt_trichotomy`

    ```lean
    lemma lt_trichotomy (x y : ℚ) : x < y ∨ x = y ∨ x < y :=
    ```

    which gives a case division on the relative sizes of two real numbers.

    ```lean
    example {f : ℚ → ℚ} (hf : ∀ x y, x < y → f x < f y) : Injective f := by
      sorry
    ```
18. Let \(f:X\to\mathbb{N}\) be a function, let \(x\_0\) be of type \(X\) with
    \(f(x\_0)=0\), and let \(i:X\to X\) be a function such that, for all \(x\),
    \(f(i(x))=f(x)+1\). Show that \(f\) is surjective. I recommend induction.

    We record this theorem for future use under the name `surjective_of_intertwining`.

    ```lean
    example {f : X → ℕ} {x0 : X} (h0 : f x0 = 0) {i : X → X}
        (hi : ∀ x, f (i x) = f x + 1) : Surjective f := by
      sorry
    ```

### 8.2. Bijectivity

#### 8.2.1. Definition

> **Definition:**
>
> A function is *bijective*, if it is both injective and surjective.

```lean
def Bijective (f : X → Y) : Prop := Injective f ∧ Surjective f
```

#### 8.2.2. Example

> **Problem:**
>
> Let \(p:\mathbb{R}\to\mathbb{R}\) be the function defined by, \(p(x)=2x-5\).
> Show that \(p\) is bijective.

> **Solution:**
>
> We must show that \(p\) is injective and surjective.
>
> For the injectivity, let \(x\_1\) and \(x\_2\) be real numbers and suppose that
> \(p(x\_1)=p(x\_2)\). This means that \(2x\_1-5=2x\_2-5\). So
>
> \[\begin{split}x\_1&= \frac{(2x\_1-5)+5}{2}\\
> &= \frac{(2x\_2-5)+5}{2}\\
> &=x\_2.\end{split}\]
>
> For the surjectivity, let \(y\) be a real number. Then
>
> \[\begin{split}p \left(\frac{y+5}{2}\right)&=2\left(\frac{y+5}{2}\right)-5\\
> &=y.\end{split}\]

```lean
def p (x : ℝ) : ℝ := 2 * x - 5

example : Bijective p := by
  dsimp [Bijective]
  constructor
  · dsimp [Injective]
    intro x1 x2 hx
    dsimp [p] at hx
    calc x1 = ((2 * x1 - 5) + 5) / 2 := by ring
      _ = ((2 * x2 - 5) + 5) / 2 := by rw [hx]
      _ = x2 := by ring
  · dsimp [Surjective]
    intro y
    use (y + 5) / 2
    calc p ((y + 5) / 2) = 2 * ((y + 5) / 2) - 5 := by rfl
      _ = y := by ring
```

#### 8.2.3. Example

> **Problem:**
>
> Let \(a:\mathbb{R}\to\mathbb{R}\) be the function defined by, \(a(t)=t^3-t\).
> Show that \(a\) is not bijective.

> **Solution:**
>
> We will show that \(a\) is not injective. Indeed, note that \(0\ne 1\) but
>
> \[\begin{split}a(0)&=0^3-0\\
> &=1^3-1\\
> &=a(1).\end{split}\]

```lean
def a (t : ℝ) : ℝ := t ^ 3 - t

example : ¬ Bijective a := by
  dsimp [Bijective]
  push_neg
  left
  dsimp [Injective]
  push_neg
  use 0, 1
  constructor
  · calc a 0 = 0 ^ 3 - 0 := by rfl
      _ = 1 ^ 3 - 1 := by numbers
      _ = a 1 := by rfl
  · numbers
```

#### 8.2.4. Example

Consider the following finite inductive types Celestial and Subatomic:

```lean
inductive Celestial
  | sun
  | moon
  deriving DecidableEq

inductive Subatomic
  | proton
  | neutron
  | electron
  deriving DecidableEq
```

Consider the following function \(f\) from the Celestial type to the Subatomic type.

```lean
def f : Celestial → Subatomic
  | sun => proton
  | moon => electron
```

> **Problem:**
>
> Prove that the function \(f\) is not bijective.

![_images/celestial_subatomic.png](https://hrmacbeth.github.io/math2001/_images/celestial_subatomic.png)

```lean
example : ¬ Bijective f := by
  dsimp [Bijective]
  push_neg
  right
  dsimp [Surjective]
  push_neg
  use neutron
  intro x
  cases x <;> exhaust
```

#### 8.2.5. Example

> **Theorem:**
>
> A function \(f:X\to Y\) is bijective, if and only if for all \(y\) of type \(Y\),
> there exists a unique \(x\) of type \(X\), such that \(f(x)=y\).

> **Proof:**
>
> First, suppose that \(f\) is bijective. Let \(y\) be of type \(Y\). Since \(f\)
> is surjective, there exists \(x\) of type \(X\), such that \(f(x)=y\). We will show
> that this \(x\) is unique. Indeed, for any other \(x'\) with \(f(x')=y\), we have
> that
>
> \[\begin{split}f(x')&=y\\
> &=f(x),\end{split}\]
>
> and so by the injectivity of \(f\), \(x'=x\).
>
> Conversely, suppose that for all \(y\) of type \(Y\), there exists a unique \(x\) of
> type \(X\), such that \(f(x)=y\). (\(\star\)) We must show that \(f\) is
> injective and surjective.
>
> For the injectivity, let \(x\_1\) and \(x\_2\) be of type \(X\), and suppose that
> \(f(x\_1)=f(x\_2)\). By (\(\star\)) applied with \(y=f(x\_1)\), there exists a unique
> \(x\) of type \(X\) for which \(f(x)=f(x\_1)\). So, by the uniqueness, \(x\_1=x\),
> and also, since \(f(x\_2)=f(x\_1)=f(x)\), \(x\_2=x\). Combining these, \(x\_1=x\_2\).
>
> For the surjectivity, let \(y\) be of type \(Y\). By (\(\star\)), there exists (a
> unique) \(x\) of type \(X\) for which \(f(x)=y\).

```lean
example {f : X → Y} : Bijective f ↔ ∀ y, ∃! x, f x = y := by
  constructor
  · -- if `f` is bijective then `∀ y, ∃! x, f x = y`
    intro h y
    obtain ⟨h_inj, h_surj⟩ := h
    obtain ⟨x, hx⟩ := h_surj y
    use x
    dsimp
    constructor
    · apply hx
    · intro x' hx'
      apply h_inj
      calc f x' = y := by rw [hx']
        _ = f x := by rw [hx]
  · -- if `∀ y, ∃! x, f x = y` then `f` is bijective
    intro h
    constructor
    · -- `f` is injective
      intro x1 x2 hx1x2
      obtain ⟨x, hx, hx'⟩ := h (f x1)
      have hxx1 : x1 = x
      · apply hx'
        rfl
      have hxx2 : x2 = x
      · apply hx'
        rw [hx1x2]
      calc x1 = x := by rw [hxx1]
        _ = x2 := by rw [hxx2]
    · -- `f` is surjective
      intro y
      obtain ⟨x, hx, hx'⟩ := h y
      use x
      apply hx
```

#### 8.2.6. Example

> **Problem:**
>
> Show that for all functions \(f\) from the Celestial type
> (Example 8.2.4) to itself, if \(f\) is injective then it is
> bijective.

We prove this by an exhaustive analysis of all functions \(f\) from the Celestial type to
itself. There are four such functions. I dealt with the first two cases below; fill in the second
two cases yourself.

```lean
example : ∀ f : Celestial → Celestial, Injective f → Bijective f := by
  intro f hf
  constructor
  · -- `f` is injective by assumption
    apply hf
  -- show that `f` is surjective
  match h_sun : f sun, h_moon : f moon with
  | sun, sun =>
    have : sun = moon
    · apply hf
      rw [h_sun, h_moon]
    contradiction
  | sun, moon =>
    intro y
    cases y
    · use sun
      apply h_sun
    · use moon
      apply h_moon
  | moon, sun => sorry
  | moon, moon => sorry
```

#### 8.2.7. Example

> **Problem:**
>
> Show that it is not true that for all functions \(f:\mathbb{N}\to \mathbb{N}\), if \(f\)
> is injective then it is bijective.

> **Solution:**
>
> We must show that there exists a function \(f:\mathbb{N}\to \mathbb{N}\), which is injective
> but not bijective.
>
> Indeed, consider the function \(f(n)=n+1\). This function is injective, since for all
> natural numbers \(n\_1\) and \(n\_2\), if \(n\_1+1=n\_2+1\) then \(n\_1=n\_2\).
>
> However, this function is not surjective, and therefore not bijective. To see this, observe that
> for all natural numbers \(n\), we have \(f(n)=n+1>0\) and therefore \(f(n)\ne 0\).

```lean
example : ¬ ∀ f : ℕ → ℕ, Injective f → Bijective f := by
  push_neg
  use fun n ↦ n + 1
  constructor
  · -- the function is injective
    intro n1 n2 hn
    addarith [hn]
  · -- the function is not bijective
    dsimp [Bijective]
    push_neg
    right
    -- specifically, it's not surjective
    dsimp [Surjective]
    push_neg
    use 0
    intro n
    apply ne_of_gt
    extra
```

#### 8.2.8. Exercises

1. Prove or disprove that the function \(x \mapsto 4-3x\) from \(\mathbb{R}\) to
   \(\mathbb{R}\) is bijective.

   (If you think it’s true, prove it, by solving the first version below. If you think it’s false,
   solve the second version.)

   ```lean
   example : Bijective (fun (x : ℝ) ↦ 4 - 3 * x) := by
     sorry

   example : ¬ Bijective (fun (x : ℝ) ↦ 4 - 3 * x) := by
     sorry
   ```
2. Prove or disprove that the function \(x \mapsto x^2+2x\) from \(\mathbb{R}\) to
   \(\mathbb{R}\) is bijective.

   (If you think it’s true, prove it, by solving the first version below. If you think it’s false,
   solve the second version.)

   ```lean
   example : Bijective (fun (x : ℝ) ↦ x ^ 2 + 2 * x) := by
     sorry

   example : ¬ Bijective (fun (x : ℝ) ↦ x ^ 2 + 2 * x) := by
     sorry
   ```
3. Consider the following finite inductive type Element, and the following function \(e\) from
   the Element type to itself. Prove or disprove that the function \(e\) is bijective.

   (If you think it’s true, prove it, by solving the first version below. If you think it’s false,
   solve the second version.)

   ```lean
   inductive Element
     | fire
     | water
     | earth
     | air
     deriving DecidableEq

   open Element

   def e : Element → Element
     | fire => earth
     | water => air
     | earth => fire
     | air => water

   example : Bijective e := by
     sorry

   example : ¬ Bijective e := by
     sorry
   ```
4. Show that for all functions \(f\) from the Subatomic type
   (Example 8.2.4) to itself, if \(f\) is injective then it is
   bijective.

   This is like Example 8.2.6, but with more cases to check.

   ```lean
   example : ∀ f : Subatomic → Subatomic, Injective f → Bijective f := by
     sorry
   ```
5. Consider the finite inductive type Element from a previous exercise. Show that for all
   functions \(f\) from the Element type to itself, if \(f\) is injective then it is
   bijective.

   This is like Example 8.2.6 and the previous exercise, but with even more
   (frankly, way too many ….) cases to check.

   ```lean
   example : ∀ f : Element → Element, Injective f → Bijective f := by
     sorry
   ```

### 8.3. Composition of functions

#### 8.3.1. Definition

> **Definition:**
>
> The *composition* of the function \(g : Y \to Z\) with the function \(f : X \to Y\) is the
> function from \(X\) to \(Z\) which sends \(x\) to \(g(f(x))\).

```lean
def comp (f : X → Y) (g : Y → Z) (x : X) : Z := g (f x)
```

The composition of \(g : Y \to Z\) with \(f : X \to Y\) is denoted \(g \circ f\) (in
Lean, `g ∘ f`).

#### 8.3.2. Example

> **Problem:**
>
> Let \(f:\mathbb{R}\to\mathbb{R}\) be the function defined by, \(f(a)=a+3\).
> Let \(g:\mathbb{R}\to\mathbb{R}\) be the function defined by, \(g(b)=2b\).
> Let \(h:\mathbb{R}\to\mathbb{R}\) be the function defined by, \(h(c)=2c+6\).
>
> Show that \(g \circ f = h\).

> **Solution:**
>
> Let \(x\) be a real number. Then
>
> \[\begin{split}(g \circ f) (x)&=g(f(x))\\
> &=2(x+3)\\
> &=2x+6\\
> &=h(x).\end{split}\]

In the Lean proof, note the new tactic `ext`. To prove two functions are equal, we have to
show that they are equal on every input. This is what the tactic `ext` does. (The name stands
for “extensionality”.) Before we use it, the goal state is

```
⊢ g ∘ f = h
```

After we use it, the goal state is

```
x : ℝ
⊢ (g ∘ f) x = h x
```

Here is the full Lean proof.

```lean
def f (a : ℝ) : ℝ := a + 3
def g (b : ℝ) : ℝ := 2 * b
def h (c : ℝ) : ℝ := 2 * c + 6

example : g ∘ f = h := by
  ext x
  calc (g ∘ f) x = g (f x) := by rfl
    _ = 2 * (x + 3) := by rfl
    _ = 2 * x + 6 := by ring
    _ = h x := by rfl
```

#### 8.3.3. Definition

> **Definition:**
>
> The *identity function* \(\operatorname{Id}\_X:X\to X\) is the function which sends each
> \(x\) of type \(X\) to itself.

```lean
def id (x : X) : X := x
```

#### 8.3.4. Example

> **Problem:**
>
> Let \(s:\mathbb{R}\to\mathbb{R}\) be the function defined by, \(s(x)=5-x\).
>
> Show that \(s \circ s = \operatorname{Id}\_\mathbb{R}\).

> **Solution:**
>
> Let \(x\) be a real number. We must show that \(5 - (5 - x) = x\), which is true.

```lean
def s (x : ℝ) : ℝ := 5 - x

example : s ∘ s = id := by
  ext x
  dsimp [s]
  ring
```

#### 8.3.5. Definition

> **Definition:**
>
> A function \(g : Y \to X\) is an *inverse* of \(f : X \to Y\), if
> \(g \circ f=\operatorname{Id}\_X\) and \(f \circ g=\operatorname{Id}\_Y\).

```lean
def Inverse (f : X → Y) (g : Y → X) : Prop := g ∘ f = id ∧ f ∘ g = id
```

#### 8.3.6. Example

Consider the following finite inductive type Humour:

```lean
inductive Humour
  | melancholic
  | choleric
  | phlegmatic
  | sanguine
  deriving DecidableEq
```

Consider the following function \(p\) from the Humour type to itself.

```lean
def p : Humour → Humour
  | melancholic => choleric
  | choleric => sanguine
  | phlegmatic => phlegmatic
  | sanguine => melancholic
```

![_images/humour.png](https://hrmacbeth.github.io/math2001/_images/humour.png)

> **Problem:**
>
> Define a function \(q\) from the Humour type to itself which is inverse to \(p\), and
> prove this.

```lean
def q : Humour → Humour
  | melancholic => sanguine
  | choleric => melancholic
  | phlegmatic => phlegmatic
  | sanguine => choleric

example : Inverse p q := by
  constructor
  · ext x
    cases x <;> exhaust
  · ext x
    cases x <;> exhaust
```

#### 8.3.7. Example

> **Proposition:**
>
> Let \(f : X \to Y\) be a bijective function. Then there exists a function
> \(g : Y \to X\) which is inverse to \(f\).

> **Proof:**
>
> Define a function \(g : Y \to X\) as follows: given \(y\) of type \(Y\), by the
> surjectivity of \(f\) there exists an \(x\) of type \(X\) such that \(f(x)=y\),
> and we set \(g(y)\) to be this \(x\). Thus, for all \(y\), it is true that
> \(f(g(y))=y\). (\(\star\))
>
> This immediately shows that \(f \circ g = \operatorname{Id}\_Y\). To show that
> \(g \circ f = \operatorname{Id}\_X\), let \(x\) be of type \(X\). We have, by
> (\(\star\)), that
>
> \[\begin{split}f((g \circ f)(x))&=f(g(f(x)))\\
> &=f(x)\\
> &=f(\operatorname{Id}\_X(x)),\end{split}\]
>
> so by the injectivity of \(f\), it follows that
> \((g \circ f)(x) = \operatorname{Id}\_X(x)\).

To write this proof in Lean, we need a tactic, `choose`, 1 which is not used elsewhere in the
book. This tactic constructs the function \(g : Y \to X\)
as described in the first paragraph of the text proof. More precisely, given the hypothesis

```
h_surj: ∀ (b : Y), ∃ a, f a = b
```

the tactic invocation `choose g hg using h_surj` creates a function consisting of a “choice” of
the `a` for each `b`:

```
g : Y → X
hg : ∀ (b : Y), f (g b) = b
```

Here is the full proof in Lean.

```lean
theorem exists_inverse_of_bijective {f : X → Y} (hf : Bijective f) :
    ∃ g : Y → X, Inverse f g := by
  dsimp [Bijective] at hf
  obtain ⟨h_inj, h_surj⟩ := hf
  dsimp [Surjective] at h_surj
  choose g hg using h_surj
  use g
  dsimp [Inverse]
  constructor
  · -- prove `g ∘ f = id`
    ext x
    dsimp [Injective] at h_inj
    apply h_inj
    calc f ((g ∘ f) x) = f (g (f x)) := by rfl
      _ = f x := by apply hg
      _ = f (id x) := by rfl
  · -- prove `f ∘ g = id`
    ext y
    apply hg
```

#### 8.3.8. Example

> **Proposition:**
>
> Let \(f : X \to Y\) and \(g : Y \to X\) be functions, with \(g : Y \to X\) inverse
> to \(f : X \to Y\). Then \(f : X \to Y\) is bijective.

> **Proof:**
>
> We first show \(f\) is injective. Indeed, let \(x\_1\) and \(x\_2\) be of type
> \(X\), and suppose that \(f(x\_1)=f(x\_2)\). Then
>
> \[\begin{split}x\_1&=\operatorname{Id}\_X(x\_1)\\
> &=(g\circ f)(x\_1)\\
> &=g(f(x\_1))\\
> &=g(f(x\_2))\\
> &=(g\circ f)(x\_2)\\
> &=\operatorname{Id}\_X(x\_2)\\
> &=x\_2.\end{split}\]
>
> We now show that \(f\) is surjective. Indeed, let \(y\) be of type \(Y\). Then
>
> \[\begin{split}f(g(y))&=(f\circ g)(y)\\
> &=\operatorname{Id}\_Y(y)\\
> &=y.\end{split}\]

```lean
theorem bijective_of_inverse {f : X → Y} {g : Y → X} (h : Inverse f g) :
    Bijective f := by
  dsimp [Inverse] at h
  obtain ⟨hgf, hfg⟩ := h
  constructor
  · -- `f` is injective
    intro x1 x2 hx
    calc x1 = id x1 := by rfl
      _ = (g ∘ f) x1 := by rw [hgf]
      _ = g (f x1) := by rfl
      _ = g (f x2) := by rw [hx]
      _ = (g ∘ f) x2 := by rfl
      _ = id x2 := by rw [hgf]
      _ = x2 := by rfl
  · -- `f` is surjective
    intro y
    use g y
    calc f (g y) = (f ∘ g) y := by rfl
      _ = id y := by rw [hfg]
      _ = y := by rfl
```

#### 8.3.9. Example

> **Theorem:**
>
> Let \(f : X \to Y\) be a function. Then \(f\) is bijective, if and only if there
> exists a function \(g : Y \to X\) which is inverse to \(f\).

> **Proof:**
>
> The first direction is given by Example 8.3.7, and the second
> direction by Example 8.3.8.

```lean
theorem bijective_iff_exists_inverse (f : X → Y) :
    Bijective f ↔ ∃ g : Y → X, Inverse f g := by
  constructor
  · apply exists_inverse_of_bijective
  · intro h
    obtain ⟨g, H⟩ := h
    apply bijective_of_inverse H
```

#### 8.3.10. Exercises

1. Consider the following functions \(a\) and \(b\) from the Humour type (see
   Example 8.3.6) to itself. Write down a function \(c\) from the Humour type
   to itself, so that \(b \circ a=c\).

   When you have the right function written down, the included proof will work.

   ```lean
   def a : Humour → Humour
     | melancholic => sanguine
     | choleric => choleric
     | phlegmatic => phlegmatic
     | sanguine => melancholic

   def b : Humour → Humour
     | melancholic => phlegmatic
     | choleric => phlegmatic
     | phlegmatic => melancholic
     | sanguine => sanguine

   def c : Humour → Humour
     | melancholic => sorry
     | choleric => sorry
     | phlegmatic => sorry
     | sanguine => sorry

   example : b ∘ a = c := by
     ext x
     cases x <;> exhaust
   ```
2. Consider the function \(u:\mathbb{R}\to\mathbb{R}\) defined by, \(u(x)=5x+1\). Write
   down a function \(v:\mathbb{R}\to\mathbb{R}\) which is inverse to \(u\), and prove it.

   ```lean
   def u (x : ℝ) : ℝ := 5 * x + 1

   noncomputable def v (x : ℝ) : ℝ := sorry

   example : Inverse u v := by
     sorry
   ```
3. Let \(f : X \to Y\) and \(g : Y \to Z\) be injective functions. Show that
   \(g \circ f\) is also injective.

   ```lean
   example {f : X → Y} (hf : Injective f) {g : Y → Z} (hg : Injective g) :
       Injective (g ∘ f) := by
     sorry
   ```
4. Let \(f : X \to Y\) and \(g : Y \to Z\) be surjective functions. Show that
   \(g \circ f\) is also surjective.

   ```lean
   example {f : X → Y} (hf : Surjective f) {g : Y → Z} (hg : Surjective g) :
       Surjective (g ∘ f) := by
     sorry
   ```
5. Let \(f : X \to Y\) be a surjective function. Show that there exists a function
   \(g : Y \to Z\), such that \(f \circ g=\operatorname{Id}\_Y\).

   ```lean
   example {f : X → Y} (hf : Surjective f) : ∃ g : Y → X, f ∘ g = id := by
     sorry
   ```
6. Let \(f : X \to Y\) and \(g : Y \to X\) be functions, with \(g\) inverse
   to \(f\). Show that \(f\) is inverse to \(g\).

   ```lean
   example {f : X → Y} {g : Y → X} (h : Inverse f g) : Inverse g f := by
     sorry
   ```
7. Let \(f : X \to Y\) and \(g\_1, g\_2 : Y \to X\) be functions, with both \(g\_1\) and
   \(g\_1\) inverse to \(f\). Show that \(g\_1=g\_2\).

   This problem says that if a function \(f\) has an inverse, then that inverse is unique.

   ```lean
   example {f : X → Y} {g1 g2 : Y → X} (h1 : Inverse f g1) (h2 : Inverse f g2) :
       g1 = g2 := by
     sorry
   ```

Footnotes

1
:   Experts will recognize this as the
    [axiom of choice](https://en.wikipedia.org/wiki/Axiom_of_choice).

### 8.4. Product types

#### 8.4.1. Example

> **Problem:**
>
> Consider the function \(q:\mathbb{Z}\to\mathbb{Z}^2\) defined by, \(q(m)=(m + 1, 2 - m)\).
> Show that \(q\) is
>
> 1. injective;
> 2. not surjective.

> **Solution:**
>
> 1. Let \(m\_1\) and \(m\_2\) be integers and suppose that \(q(m\_1)=q(m\_2)\). Then
>    by definition
>
>    \[(m\_1+1,2-m\_1)=(m\_2+1,2-m\_2),\]
>
>    so \(m\_1+1=m\_2+1\) and \(2-m\_1=2-m\_2\), so \(m\_1=m\_2\).
> 2. We will show that there exists \((a,b)\) in \(\mathbb{Z}^2\) such that for all
>    integers \(m\), \(q(m)\ne(a,b)\). Indeed, we will show that \((0,1)\) has this
>    property. Suppose that \(m\) were an integer with \(q(m)=(0,1)\). Then by definition
>
>    \[(m+1,2-m)=(0,1),\]
>
>    so \(m+1=0\) and \(2-m=1\), hence
>
>    \[\begin{split}1&=(m+1)+(2-m)-2\\
>    &=0+1-2\\
>    &=-1,\end{split}\]
>
>    contradiction.

To write these proofs in Lean, notice the use of the tactic `obtain` in the injectivity problem to
convert the hypothesis of a equality in a product type,

```
hm : (m1 + 1, 2 - m1) = (m2 + 1, 2 - m2)
```

to two hypotheses of equality in the two components of the product:

```
hm' : m1 + 1 = m2 + 1
hm'' : 2 - m1 = 2 - m2
```

This is consistent with how you should think about equality in a product type: two ordered pairs are
equal if the two left parts are equal *and* the two right parts are equal. So we use the same
tactic, with the same syntax, as for the logical operator “and”.

```lean
def q (m : ℤ) : ℤ × ℤ := (m + 1, 2 - m)

example : Injective q := by
  dsimp [Injective]
  intro m1 m2 hm
  dsimp [q] at hm
  obtain ⟨hm', hm''⟩ := hm
  addarith [hm']
```

The tactic `obtain` is used similarly in the non-surjectivity problem to break down the hypothesis
of equality in a product type

```
hm : (m + 1, 2 - m) = (0, 1)
```

which arises in that problem.

```lean
example : ¬ Surjective q := by
  dsimp [Surjective]
  push_neg
  use (0, 1)
  intro m hm
  dsimp [q] at hm
  obtain ⟨hm1, hm2⟩ := hm
  have H : 1 = -1 := by addarith [hm1, hm2]
  numbers at H
```

#### 8.4.2. Example

> **Problem:**
>
> Consider the function \((m,n)\mapsto (m + n, m + 2n)\) from \(\mathbb{Z}^2\) to
> \(\mathbb{Z}^2\).
> Show that this function is bijective.

Usually, the most efficient way to prove a function is bijective is to produce an inverse for it.
This is enough because of the theorem we proved in Example 8.3.9.
Coming up with this inverse is something you should do in rough work, probably on paper rather than
in Lean. For example, in this problem, you can set up an equation in \((a,b)\) which the
inverse has to satisfy:

\[(a,b)=(m+n, m+2n)\]

and simplify to a system of integer equations, and then solve for \(m\) and \(n\):

\[\begin{split}a&=m+n\\
b&=m+2n\\
b-a&=(m+2n)-(m+n)\\
&=n\\
n&=b-a\\
a&=m+n\\
&=m+(b-a)\\
a-(b-a)&=m\\
2a-b&=m\\
m&=2a-b\end{split}\]

to conclude that a good candidate for the inverse is the function
\((a,b)\mapsto (2a-b, b-a)\) from \(\mathbb{Z}^2\) to \(\mathbb{Z}^2\). But this rough
work does not get included in the write-up, either on paper or in Lean. Instead, just produce the
inverse out of a hat, and check that it works.

> **Solution:**
>
> By Example 8.3.9 it suffices to show that this function has an
> inverse. We will show that the function \((a,b)\mapsto (2a-b, b-a)\) is inverse to this
> function.
>
> Firstly, for any \((m,n)\) in \(\mathbb{Z}^2\), we have that
>
> \[\left(2 (m + n) - (m + 2 n), (m + 2 n) - (m + n)\right) = (m, n).\]
>
> Secondly, for any \((a,b)\) in \(\mathbb{Z}^2\), we have that
>
> \[\left((2 a - b) + (b - a), (2 a - b) + 2 (b - a)\right) = (a, b).\]

In Lean, note

- the lemma `bijective_iff_exists_inverse`, which is the Lean name for the theorem from
  Example 8.3.9;
- the use of the tactic `ext` (recall Example 8.3.2) to show that two functions
  are equal by showing that they are equal on arbitrary input.

```lean
example : Bijective (fun ((m, n) : ℤ × ℤ) ↦ (m + n, m + 2 * n)) := by
  rw [bijective_iff_exists_inverse]
  use fun (a, b) ↦ (2 * a - b, b - a)
  constructor
  · ext ⟨m, n⟩
    dsimp
    ring
  · ext ⟨a, b⟩
    dsimp
    ring
```

#### 8.4.3. Example

The ideas of Example 8.4.2 can be adapted fairly flexibly, particularly
over the rationals and over the reals. Try this one yourself.

> **Problem:**
>
> Consider the function \((m,n)\mapsto (m + n, m - n)\) from \(\mathbb{R}^2\) to
> \(\mathbb{R}^2\).
> Show that this function is bijective.

```lean
example : Bijective (fun ((m, n) : ℝ × ℝ) ↦ (m + n, m - n)) := by
  sorry
```

But over the integers, complications can ensue. You’ll find that the inverse you come up with for
the previous example involves a division, which works fine over \(\mathbb{R}\) but doesn’t work
over \(\mathbb{Z}\). And in fact in this case the function is *not* bijective as a map from
\(\mathbb{Z}^2\) to \(\mathbb{Z}^2\).

> **Problem:**
>
> Consider the function \((m,n)\mapsto (m + n, m - n)\) from \(\mathbb{Z}^2\) to
> \(\mathbb{Z}^2\).
> Show that this function is not bijective.

> **Solution:**
>
> We will show that the function is not surjective. In fact, we will show that for all
> \((m,n)\) in \(\mathbb{Z}^2\), it is not true that \((m + n, m - n)=(0,1)\). So let
> \((m,n)\) be in \(\mathbb{Z}^2\) and suppose that \((m + n, m - n)=(0,1)\). Then
> \(m+n=0\) and \(m-n=1\), so
>
> \[\begin{split}0&\equiv 2m\mod 2\\
> &=(m-n)+(m+n)\\
> &=1+0\\
> &=1,\end{split}\]
>
> contradiction.

The last part of the problem could play out in several different ways. The hypotheses \(m+n=0\)
and \(m-n=1\) are pretty clearly contradictory (for integers), so there are several variant ways
you could produce the contradiction instead of the numeric contradiction \(0\equiv 1\mod 2\)
which I produced here.

```lean
example : ¬ Bijective (fun ((m, n) : ℤ × ℤ) ↦ (m + n, m - n)) := by
  dsimp [Bijective, Injective, Surjective]
  push_neg
  right
  use (0, 1)
  intro (m, n) h
  dsimp at h
  obtain ⟨h1, h2⟩ := h
  have :=
  calc 0 ≡ 2 * m [ZMOD 2] := by extra
    _ = (m - n) + (m + n) := by ring
    _ = 1 + 0 := by rw [h1, h2]
    _ = 1 := by numbers
  numbers at this
```

#### 8.4.4. Example

> **Problem:**
>
> Consider the function \((x,y)\mapsto (x+y,x-y, y)\) from \(\mathbb{R}^2\) to
> \(\mathbb{R}^3\). Show that this function is injective.

> **Solution:**
>
> Let \((x\_1,y\_1)\) and \((x\_2,y\_2)\) be points in \(\mathbb{R}^2\) and suppose that
>
> \[(x\_1+y\_1,x\_1-y\_1, y\_1)=(x\_2+y\_2,x\_2-y\_2, y\_2).\]
>
> Then, inspecting co-ordinate by co-ordinate, we have that
>
> \[\begin{split}x\_1+y\_1&=x\_2+y\_2\\
> x\_1-y\_1&=x\_2-y\_2\\
> y\_1&=y\_2\end{split}\]
>
> Subtracting the third equation from the first, we also have that \(x\_1=x\_2\). So
> \((x\_1,y\_1)=(x\_2,y\_2)\).

In Lean, note the use of the tactic `constructor` to reduce a goal of equality in a product type,

```
⊢ (x1, y1) = (x2, y2)
```

to two simpler goals, one for each co-ordinate:

```
⊢ x1 = x2
⊢ y1 = y2
```

As with the use of `obtain` for hypotheses of equality in a product type (recall
Example 8.4.1), the point is that an equality in a product type is
effectively an “and” statement about equality in the first and the second co-ordinates.

```lean
example : Injective (fun ((x, y) : ℝ × ℝ) ↦ (x + y, x - y, y)) := by
  intro (x1, y1) (x2, y2) h
  dsimp at h
  obtain ⟨h, h', hy⟩ := h
  constructor
  · addarith [h, hy]
  · apply hy
```

#### 8.4.5. Example

> **Problem:**
>
> Consider the function \((x,y)\mapsto x + y\) from \(\mathbb{R}^2\) to \(\mathbb{R}\).
> Show that this function is
>
> 1. not injective;
> 2. surjective.

> **Solution:**
>
> 1. We will show that there exist points \((x\_1,y\_1)\) and \((x\_2,y\_2)\) in
> \(\mathbb{R}^2\) such that \(x\_1+y\_1=x\_2+y\_2\) and \((x\_1,y\_1)\ne (x\_2,y\_2)\).
> Indeed, consider the points \((0,0)\) and \((1,-1)\). We have that \(0+0=1+-1\) and
> \((0,0)\ne (1,-1)\).
>
> 2. Let \(a\) be a real number. We must show that there exists a point \((x,y)\) in
> \(\mathbb{R}^2\) such that \(x+y=a\). Indeed, \(a+0=a\), so \((a,0)\) has this
> property.

```lean
example : ¬ Injective (fun ((x, y) : ℝ × ℝ) ↦ x + y) := by
  dsimp [Injective]
  push_neg
  use (0, 0), (1, -1)
  dsimp
  constructor
  · numbers
  · numbers

example : Surjective (fun ((x, y) : ℝ × ℝ) ↦ x + y) := by
  intro a
  use (a, 0)
  dsimp
  ring
```

#### 8.4.6. Example

> **Problem:**
>
> Consider the function \((m,n)\mapsto 5m+8n\) from \(\mathbb{Z}^2\) to \(\mathbb{Z}\).
> Show that this function is
>
> 1. not injective;
> 2. surjective.

> **Solution:**
>
> 1. We will show that there exist pairs \((m\_1,n\_1)\) and \((m\_2,n\_2)\) in
> \(\mathbb{Z}^2\) such that \(5m\_1+8n\_1=5m\_2+8n\_2\) and \((m\_1,n\_1)\ne (m\_2,n\_2)\).
> Indeed, consider the pairs \((0,0)\) and \((8,-5)\). We have that
> \(5\cdot 0+8\cdot 0=5\cdot 8+8\cdot -5\) and
> \((0,0)\ne (8,-5)\).
>
> 2. Let \(a\) be an integer. We must show that there exists a pair \((m,n)\) in
> \(\mathbb{Z}^2\) such that \(5m+8n=a\). Indeed, \(5(-3a)+8(2a)=a\), so
> \((-3a,2a)\) has this property.

(Where did the idea for the -3 and the 2 come from in the second part of this proof? Compare with
[Example 3.5.1]](#m2001-bezout-prob1) and [Example 3.5.3]](#m2001-bezout-prob3).)

```lean
example : ¬ Injective (fun ((m, n) : ℤ × ℤ) ↦ 5 * m + 8 * n) := by
  dsimp [Injective]
  push_neg
  use (0, 0), (8, -5)
  constructor
  · numbers
  · numbers

example : Surjective (fun ((m, n) : ℤ × ℤ) ↦ 5 * m + 8 * n) := by
  intro a
  use (-3 * a, 2 * a)
  dsimp
  ring
```

#### 8.4.7. Example

> **Problem:**
>
> Consider the function \((m,n)\mapsto 5m+10n\) from \(\mathbb{Z}^2\) to \(\mathbb{Z}\).
> Show that this function is
>
> 1. not injective;
> 2. not surjective.

We leave the non-injectivity proof as an exercise; it is similar to that in the previous two problems.

> **Solution:**
>
> (Non-surjectivity) We will show that there exists an integer \(x\) such that for all pairs \((m,n)\)
> of integers, \(5m+10n\ne x\). Indeed, let us show that 1 has this property. Let
> \((m,n)\) be a pair of integers and suppose that \(5m+10n=1\). Then
>
> \[\begin{split}0 &\equiv 5(m+2n)\mod 5\\
> &=5m+10n\\
> &=1,\end{split}\]
>
> contradiction.

```lean
example : ¬ Injective (fun ((m, n) : ℤ × ℤ) ↦ 5 * m + 10 * n) := by
  sorry

example : ¬ Surjective (fun ((m, n) : ℤ × ℤ) ↦ 5 * m + 10 * n) := by
  dsimp [Surjective]
  push_neg
  use 1
  intro (m, n) h
  dsimp at h
  have :=
  calc 0 ≡ 5 * (m + 2 * n) [ZMOD 5] := by extra
    _ = 5 * m + 10 * n := by ring
    _ = 1 := h
  numbers at this
```

#### 8.4.8. Example

> **Problem:**
>
> Consider the function \(g:\mathbb{R}^2\to \mathbb{R}^2\) defined by,
> \(g(x,y)=(y,x)\). Show that \(g\circ g=\operatorname{Id}\_\mathbb{R}\).

> **Solution:**
>
> Let \((x,y)\) be a point in \(\mathbb{R}^2\). Then
>
> \[\begin{split}g(g(x,y))&=g(y,x)\\
> &=(x,y).\end{split}\]

```lean
def g : ℝ × ℝ → ℝ × ℝ
  | (x, y) => (y, x)

example : g ∘ g = id := by
  ext ⟨x, y⟩
  dsimp [g]
```

#### 8.4.9. Example

> **Theorem:**
>
> There exists a bijection from \(\mathbb{N}^2\) to \(\mathbb{N}\).

First recall the sequence \(A\_n\) from [Example 6.2.4]](#m2001-triangle); we prove a lemma about
it.

```lean
def A : ℕ → ℕ
  | 0 => 0
  | n + 1 => A n + n + 1

theorem A_mono {n m : ℕ} (h : n ≤ m) : A n ≤ A m := by
  induction_from_starting_point m, h with k hk IH
  · extra
  · calc A n ≤ A k := IH
      _ ≤ A k + (k + 1) := by extra
      _ = A k + k + 1 := by ring
      _ = A (k + 1) := by rw [A]
```

And a corollary, more difficult.

```lean
theorem of_A_add_mono {a1 a2 b1 b2 : ℕ} (h : A (a1 + b1) + b1 ≤ A (a2 + b2) + b2) :
    a1 + b1 ≤ a2 + b2 := by
  obtain h' | h' : _ ∨ a2 + b2 + 1 ≤ a1 + b1 := le_or_lt (a1 + b1) (a2 + b2)
  · apply h'
  rw [← not_lt] at h
  have :=
  calc A (a2 + b2) + b2
     < A (a2 + b2) + b2 + (a2 + 1) := by extra
    _ = A (a2 + b2) + (a2 + b2) + 1 := by ring
    _ = A ((a2 + b2) + 1) := by rw [A]
    _ = A (a2 + b2 + 1) := by ring
    _ ≤ A (a1 + b1) := A_mono h'
    _ ≤ A (a1 + b1) + b1 := by extra
  contradiction
```

We use the sequence \(A\_n\) to define the function \(p:\mathbb{N}^2\to \mathbb{N}\) which
will be the bijection we seek: \(p(a,b)=A\_{a+b}+b\).

```lean
def p : ℕ × ℕ → ℕ
  | (a, b) => A (a + b) + b
```

Finally we prove that this function \(p\) is indeed bijective. We set up an “intertwining”
map \(i\) for \(p\), and invoke the lemma `surjective_of_intertwining` proved in the exercises
to Section 8.1.

```lean
def i : ℕ × ℕ → ℕ × ℕ
  | (0, b) => (b + 1, 0)
  | (a + 1, b) => (a, b + 1)

theorem p_comp_i (x : ℕ × ℕ) : p (i x) = p x + 1 := by
  match x with
  | (0, b) =>
    calc p (i (0, b)) = p (b + 1, 0) := by rw [i]
      _ = A ((b + 1) + 0) + 0 := by dsimp [p]
      _ = A (b + 1) := by ring
      _ = A b + b + 1 := by rw [A]
      _ = (A (0 + b) + b) + 1 := by ring
      _ = p (0, b) + 1 := by dsimp [p]
  | (a + 1, b) =>
    calc p (i (a + 1, b)) = p (a, b + 1) := by rw [i] ; rfl -- FIXME
      _ = A (a + (b + 1)) + (b + 1) := by dsimp [p]
      _ = (A ((a + 1) + b) + b) + 1 := by ring
      _ = p (a + 1, b) + 1 := by rw [p]

example : Bijective p := by
  constructor
  · intro (a1, b1) (a2, b2) hab
    dsimp [p] at hab
    have H : a1 + b1 = a2 + b2
    · apply le_antisymm
      · apply of_A_add_mono
        rw [hab]
      · apply of_A_add_mono
        rw [hab]
    have hb : b1 = b2
    · zify at hab ⊢
      calc (b1:ℤ) = A (a2 + b2) + b2 - A (a1 + b1) := by addarith [hab]
        _ = A (a2 + b2) + b2 - A (a2 + b2) := by rw [H]
        _ = b2 := by ring
    constructor
    · zify at hb H ⊢
      addarith [H, hb]
    · apply hb
  · apply surjective_of_intertwining (x0 := (0, 0)) (i := i)
    · calc p (0, 0) = A (0 + 0) + 0 := by dsimp [p]
        _ = A 0 := by ring
        _ = 0 := by rw [A]
    · intro x
      apply p_comp_i
```

#### 8.4.10. Exercises

1. Consider the function \((r,s)\mapsto (s, r-s)\) from \(\mathbb{Q}^2\) to
   \(\mathbb{Q}^2\).
   Show that this function is bijective.

   ```lean
   example : Bijective (fun ((r, s) : ℚ × ℚ) ↦ (s, r - s)) := by
     rw [bijective_iff_exists_inverse]
     sorry
   ```
2. Consider the function \((x,y)\mapsto x-2y-1\) from \(\mathbb{Z}^2\) to
   \(\mathbb{Z}\).
   Show that this function is

   1. not injective;
   2. surjective.

   ```lean
   example : ¬ Injective (fun ((x, y) : ℤ × ℤ) ↦ x - 2 * y - 1) := by
     sorry
   ```

   ```lean
   example : Surjective (fun ((x, y) : ℤ × ℤ) ↦ x - 2 * y - 1) := by
     sorry
   ```
3. Consider the function \((x,y)\mapsto x^2+y^2\) from \(\mathbb{Q}^2\) to
   \(\mathbb{Q}\).
   Show that this function is not surjective.

   ```lean
   example : ¬ Surjective (fun ((x, y) : ℚ × ℚ) ↦ x ^ 2 + y ^ 2) := by
     sorry
   ```
4. Consider the function \((x,y)\mapsto x^2-y^2\) from \(\mathbb{Q}^2\) to
   \(\mathbb{Q}\).
   Show that this function is surjective.

   ```lean
   example : Surjective (fun ((x, y) : ℚ × ℚ) ↦ x ^ 2 - y ^ 2) := by
     sorry
   ```
5. Consider the function \((a,b)\mapsto a^b\) from \(\mathbb{Q}× \mathbb{N}\) to
   \(\mathbb{Q}\). Show that this function is surjective.

   ```lean
   example : Surjective (fun ((a, b) : ℚ × ℕ) ↦ a ^ b) := by
     sorry
   ```
6. Consider the function \((x,y,z)\mapsto (x+y+z,x+2y+3z)\) from \(\mathbb{R}^3\) to
   \(\mathbb{R}^2\). Show that this function is not injective.

   ```lean
   example : ¬ Injective
       (fun ((x, y, z) : ℝ × ℝ × ℝ) ↦ (x + y + z, x + 2 * y + 3 * z)) := by
     sorry
   ```
7. Consider the function \((x,y)\mapsto (x+y,x+2y, x+3y)\) from \(\mathbb{R}^2\) to
   \(\mathbb{R}^3\). Show that this function is injective.

   ```lean
   example : Injective (fun ((x, y) : ℝ × ℝ) ↦ (x + y, x + 2 * y, x + 3 * y)) := by
     sorry
   ```
8. Consider the function \(h:\mathbb{R}^3\to \mathbb{R}^3\) defined by,
   \(h(x,y,z)=(y,z,x)\). Show that \(h\circ h\circ h=\operatorname{Id}\_\mathbb{R}\).

   ```lean
   def h : ℝ × ℝ × ℝ → ℝ × ℝ × ℝ
     | (x, y, z) => (y, z, x)

   example : h ∘ h ∘ h = id := by
     sorry
   ```

---



## 9. Sets {#m2001-9-sets}

> 📄 Source: https://hrmacbeth.github.io/math2001/09_Sets.html

This chapter introduces the language of *sets*, a convenient way to reason about the
objects in a type which have some property. This language includes
the concept of *membership* in a set, the property of a set being a *subset* of
another set, and a whole zoo of set operations, such as *intersection*, *union* and
*complement*, each of which is a wrapper for a logical symbol relating
the underlying properties.

In the last section of the chapter, Section 9.3, we study the
collection of sets in a type as a type in its own right.

### 9.1. Introduction

#### 9.1.1. Example

In type theory, the logical foundation for this book, a *set* in a type \(X\) is specified
by a predicate on \(X\). For example, “the set of integers \(n\) such that \(n\le 3\)”
is a set in \(\mathbb{Z}\).

There is a standard notation for sets specified by predicates. For example, the set described above
is written notationally as \(\{n:\mathbb{Z} \mid n\le 3\}\). Here is that set in Lean.

```lean
#check {n : ℤ | n ≤ 3}
```

Note that the infoview confirms that the type of the expression is `Set ℤ`, a set of integers.

A term of type \(X\) *belongs to* a set in \(X\) specified by some predicate, if the
predicate holds for that term.

> **Problem:**
>
> Show that the integer 1 belongs to the set of integers \(\{n:\mathbb{Z} \mid n\le 3\}\).

> **Solution:**
>
> \(1\le 3\).

Other phrasings you may see for this concept are that 1 *is a member of* the set
\(\{n:\mathbb{Z} \mid n\le 3\}\), or *is in* \(\{n:\mathbb{Z} \mid n\le 3\}\). The notation is
\(1\in \{n:\mathbb{Z} \mid n\le 3\}\).

Here is this proof in Lean:

```lean
example : 1 ∈ {n : ℤ | n ≤ 3} := by
  dsimp
  numbers
```

The tactic `dsimp` unfolds the definition of the set and of membership in that set, reducing it
to the goal

```lean
⊢ 1 ≤ 3
```

which is resolved by `numbers`.

#### 9.1.2. Example

The symbol \(\notin\) is used for the negation of \(\in\).

> **Problem:**
>
> Prove that \(10\notin \{n:\mathbb{N} \mid n\text{ is odd}\}\).

> **Solution:**
>
> Since \(10=2\cdot 5\), we have that 10 is even, so it is not odd.

In the Lean proof below, the tactic `dsimp` cleans up the goal to

```lean
⊢ ¬Odd 10
```

which can then be solved by general-purpose methods.

```lean
example : 10 ∉ {n : ℕ | Odd n} := by
  dsimp
  rw [← even_iff_not_odd]
  use 5
  numbers
```

#### 9.1.3. Example

> **Definition:**
>
> Let \(U\) and \(V\) be sets in the type \(X\). The set \(U\) *is a subset of*
> the set \(V\), if for all \(x\) of type \(X\), if \(x\in U\), then \(x\in V\).

The statement “\(U\) *is a subset of* \(V\)” is expressed in notation as
\(U \subseteq V\).

In Lean the definition looks like this:

```lean
def Set.Subset (U V : Set α) : Prop := ∀ ⦃x⦄, x ∈ U → x ∈ V
```

and the notation is `U ⊆ V`.

> **Problem:**
>
> Show that \(\{a:\mathbb{N} \mid 4\mid a\}\subseteq\{b:\mathbb{N} \mid 2\mid b\}\).

> **Solution:**
>
> We will show that for all natural numbers \(a\), if \(4\mid a\) then \(2\mid a\).
> Indeed, let \(a\) be a natural number and suppose that \(4\mid a\). Then there exists a
> natural number \(k\) such that \(a=4k\), so
>
> \[\begin{split}a&=4a\\
> &=2(2k),\end{split}\]
>
> so \(2\mid a\).

Here is that solution in Lean. Note that after unfolding the definition using `dsimp`
the goal state is simplified to

```lean
⊢ ∀ (x : ℕ), 4 ∣ x → 2 ∣ x
```

```lean
example : {a : ℕ | 4 ∣ a} ⊆ {b : ℕ | 2 ∣ b} := by
  dsimp [Set.subset_def] -- optional
  intro a ha
  obtain ⟨k, hk⟩ := ha
  use 2 * k
  calc a = 4 * k := hk
    _ = 2 * (2 * k) := by ring
```

You can also check that the proof goes through without the `dsimp` line.

#### 9.1.4. Example

The notation \(\not\subseteq\) stands for “not a subset of”.

> **Problem:**
>
> Prove that \(\{x:\mathbb{R} \mid 0 \le x^2\}\not\subseteq \{t:\mathbb{R} \mid 0\le t\}\).

> **Solution:**
>
> We will show that there exists a real number \(x\), such that \(0\le x^2\) and
> \(x<0\). Indeed, \(0\le (-3)^2\) and \(-3<0\).

In Lean, after unfolding the definitions and negation-normalizing, the goal shows as

```lean
⊢ ∃ x, 0 ≤ x ^ 2 ∧ x < 0
```

This is the reformulation we state in the first sentence of the text proof.

```lean
example : {x : ℝ | 0 ≤ x ^ 2} ⊈ {t : ℝ | 0 ≤ t} := by
  dsimp [Set.subset_def]
  push_neg
  use -3
  constructor
  · numbers
  · numbers
```

#### 9.1.5. Example

To show that two sets are equal in Lean, we show that something is a member of one if and only if
it is a member of the other. This is called the *set extensionality* property – compare with the
*functional extensionality* property discussed in [Example 8.3.2]](#m2001-ext).

> **Problem:**
>
> Prove that
> \(\{x:\mathbb{Z} \mid x\text{ is odd}\}= \{a:\mathbb{Z} \mid \exists k:\mathbb{Z}, a = 2k - 1\}\).

> **Solution:**
>
> Let \(x\) be an integer. We must show that \(x\) is odd if and only if there exists an
> integer \(k\) such that \(x=2k-1\).
>
> First, suppose that \(x\) is odd. Then there exists an integer
> \(l\) such that \(x=2l+1\). So
>
> \[\begin{split}x&=2l+1\\
> &=2(l+1)-1.\end{split}\]
>
> Conversely, suppose that there exists an integer \(k\) such that \(x=2k-1\). Then
>
> \[\begin{split}x&=2k-1\\
> &=2(k-1)+1,\end{split}\]
>
> so \(x\) is odd.

In Lean, as usual, we invoke an extensionality property using the tactic `ext`. After unfolding
the definitions, the goal displays as

```lean
⊢ Int.Odd x ↔ ∃ k, x = 2 * k - 1
```

This is the reformulation of the problem which we had stated in the first paragraph of the text proof.

Here is the full proof in Lean.

```lean
example : {x : ℤ | Int.Odd x} = {a : ℤ | ∃ k, a = 2 * k - 1} := by
  ext x
  dsimp
  constructor
  · intro h
    obtain ⟨l, hl⟩ := h
    use l + 1
    calc x = 2 * l + 1 := by rw [hl]
      _ = 2 * (l + 1) - 1 := by ring
  · intro h
    obtain ⟨k, hk⟩ := h
    use k - 1
    calc x = 2 * k - 1 := by rw [hk]
      _ = 2 * (k - 1) + 1 := by ring
```

#### 9.1.6. Example

And to show that two sets are not equal in Lean, we produce an element of one which is not an
element of the other.

> **Problem:**
>
> Show that \(\{a:\mathbb{N} \mid 4\mid a\}\ne\{b:\mathbb{N} \mid 2\mid b\}\).

> **Solution:**
>
> We will show that there exists a natural number \(x\) such that \(2\mid x\) and
> \(4\not\mid 6\).
>
> Indeed, let us show that \(6\) has this property. We have that \(6=2\cdot 3\), so
> \(2\mid 6\), and \(4\cdot 1 < 6 < 4 \cdot 2\), so \(4\not\mid 6\).

In Lean, after applying set extensionality, unfolding the definitions and negation-normalizing, the
goal state shows as

```
⊢ ∃ x, 4 ∣ x ∧ ¬2 ∣ x ∨ ¬4 ∣ x ∧ 2 ∣ x
```

At this point we indicate our witness, 6, and specify that we will prove the right alternative,
`¬4 ∣ 6 ∧ 2 ∣ 6`.

```lean
example : {a : ℕ | 4 ∣ a} ≠ {b : ℕ | 2 ∣ b} := by
  ext
  dsimp
  push_neg
  use 6
  right
  constructor
  · apply Nat.not_dvd_of_exists_lt_and_lt
    use 1
    constructor <;> numbers
  · use 3
    numbers
```

#### 9.1.7. Example

> **Problem:**
>
> Prove or disprove that \(\{k:\mathbb{Z} \mid 8\mid 5k\}=\{l:\mathbb{Z} \mid 8\mid l\}\).

> **Solution:**
>
> The statement is true.
>
> Let \(n\) be an integer. We will show that \(5n\) is a multiple of 8 if and only if
> \(n\) is.
>
> First, suppose that \(8\mid 5n\). Then there exists an integer \(a\) such that
> \(5n=8a\). So
>
> \[\begin{split}n &= -3 (5 n) + 16 n \\
> &= -3 (8 a) + 16 n \\
> & = 8 (-3 a + 2 n),\end{split}\]
>
> so \(8\mid n\).
>
> Conversely, suppose that \(8\mid n\). Then there exists an integer \(a\) such that
> \(n=8a\). So
>
> \[\begin{split}5n &= 5(8a) \\
> &= 8(5a),\end{split}\]
>
> so \(8\mid 5n\).

This problem turned out to be a disguised version of [Example 4.2.2]](#m2001-bezout-iff)!

```lean
example : {k : ℤ | 8 ∣ 5 * k} = {l : ℤ | 8 ∣ l} := by
  ext n
  dsimp
  constructor
  · intro hn
    obtain ⟨a, ha⟩ := hn
    use -3 * a + 2 * n
    calc
      n = -3 * (5 * n) + 16 * n := by ring
      _ = -3 * (8 * a) + 16 * n := by rw [ha]
      _ = 8 * (-3 * a + 2 * n) := by ring
  · intro hn
    obtain ⟨a, ha⟩ := hn
    use 5 * a
    calc 5 * n = 5 * (8 * a) := by rw [ha]
      _ = 8 * (5 * a) := by ring
```

#### 9.1.8. Example

There is a special notation \(\{1,2,3\}\) to refer to a finite set whose only elements are those
listed, here 1, 2 and 3. By definition, \(\{1,2,3\}\) means
\(\{x \mid x = 1 \lor x = 2 \lor x = 3\}\). (The type, like \(\mathbb{N}\) or
\(\mathbb{R}\), is usually inferred from context.)

> **Problem:**
>
> Prove that \(\{x:\mathbb{R} \mid x^2-x-2=0\}=\{-1, 2\}\).

> **Solution:**
>
> Let \(x\) be a real number. We must show that \(x ^ 2 - x - 2 = 0\) if and only if
> \(x=-1\) or \(x=2\).
>
> First, suppose that \(x ^ 2 - x - 2 = 0\). Then
>
> \[\begin{split}(x+1)(x-2)&=x ^ 2 - x - 2\\
> &=0,\end{split}\]
>
> so either \(x+1=0\) or \(x-2=0\). If the former, \(x=-1\). If the latter,
> \(x=2\).
>
> Conversely, suppose that \(x=-1\) or \(x=2\).
>
> **Case 1** (\(x=-1\)): Then
>
> \[\begin{split}x^2-x-2&=(-1)^2-(-1)-2\\
> &=0.\end{split}\]
>
> **Case 1** (\(x=2\)): Then
>
> \[\begin{split}x^2-x-2&=2^2-2-2\\
> &=0.\end{split}\]

In Lean, after applying set extensionality and unfolding the definitions, the goal is

```lean
x ^ 2 - x - 2 = 0 ↔ x = -1 ∨ x = 2
```

and we start the written proof by explaining this.

```lean
example : {x : ℝ | x ^ 2 - x - 2 = 0} = {-1, 2} := by
  ext x
  dsimp
  constructor
  · intro h
    have hx :=
    calc
      (x + 1) * (x - 2) = x ^ 2 - x - 2 := by ring
        _ = 0 := by rw [h]
    rw [mul_eq_zero] at hx
    obtain hx | hx := hx
    · left
      addarith [hx]
    · right
      addarith [hx]
  · intro h
    obtain h | h := h
    · calc x ^ 2 - x - 2 = (-1) ^ 2 - (-1) - 2 := by rw [h]
        _ = 0 := by numbers
    · calc x ^ 2 - x - 2 = 2 ^ 2 - 2 - 2 := by rw [h]
        _ = 0 := by numbers
```

#### 9.1.9. Example

> **Problem:**
>
> Prove that \(\{1,3,6\} \subseteq\{x:\mathbb{Q} \mid t<10\}\).

> **Solution:**
>
> We must show that for all real numbers \(t\), if \(t=1\) or \(t=3\) or \(t=6\)
> then \(t<10\). Indeed, \(1<10\), \(3<10\) and \(6<10\).

```lean
example : {1, 3, 6} ⊆ {t : ℚ | t < 10} := by
  dsimp [Set.subset_def]
  intro t ht
  obtain h1 | h3 | h6 := ht
  · addarith [h1]
  · addarith [h3]
  · addarith [h6]
```

#### 9.1.10. Exercises

1. Prove or disprove that \(4\in \{a:\mathbb{Q} \mid a<3\}\).

   ```lean
   example : 4 ∈ {a : ℚ | a < 3} := by
     sorry

   example : 4 ∉ {a : ℚ | a < 3} := by
     sorry
   ```
2. Prove or disprove that \(6\in \{n:\mathbb{N} \mid n\mid 42\}\).

   ```lean
   example : 6 ∈ {n : ℕ | n ∣ 42} := by
     sorry

   example : 6 ∉ {n : ℕ | n ∣ 42} := by
     sorry
   ```
3. Prove or disprove that \(8\in \{k:\mathbb{Z} \mid 5\mid k\}\).

   ```lean
   example : 8 ∈ {k : ℤ | 5 ∣ k} := by
     sorry

   example : 8 ∉ {k : ℤ | 5 ∣ k} := by
     sorry
   ```
4. Prove or disprove that \(11\in \{n:\mathbb{N} \mid n\text{ is odd}\}\).

   ```lean
   example : 11 ∈ {n : ℕ | Odd n} := by
     sorry

   example : 11 ∉ {n : ℕ | Odd n} := by
     sorry
   ```
5. Prove or disprove that \(-3\in \{x:\mathbb{R} \mid \forall y :\mathbb{R}, x\le y^2\}\).

   ```lean
   example : -3 ∈ {x : ℝ | ∀ y : ℝ, x ≤ y ^ 2} := by
     sorry

   example : -3 ∉ {x : ℝ | ∀ y : ℝ, x ≤ y ^ 2} := by
     sorry
   ```
6. Prove or disprove that
   \(\{a:\mathbb{N} \mid 20\mid a\}\subseteq \{x:\mathbb{N} \mid 5 \mid x\}\).

   ```lean
   example : {a : ℕ | 20 ∣ a} ⊆ {x : ℕ | 5 ∣ x} := by
     sorry

   example : {a : ℕ | 20 ∣ a} ⊈ {x : ℕ | 5 ∣ x} := by
     sorry
   ```
7. Prove or disprove that
   \(\{a:\mathbb{N} \mid 5\mid a\}\subseteq \{x:\mathbb{N} \mid 20 \mid x\}\).

   ```lean
   example : {a : ℕ | 5 ∣ a} ⊆ {x : ℕ | 20 ∣ x} := by
     sorry

   example : {a : ℕ | 5 ∣ a} ⊈ {x : ℕ | 20 ∣ x} := by
     sorry
   ```
8. Prove or disprove that
   \(\{r:\mathbb{Z} \mid 3\mid r\}\subseteq \{s:\mathbb{Z} \mid 0 \le s\}\).

   ```lean
   example : {r : ℤ | 3 ∣ r} ⊆ {s : ℤ | 0 ≤ s} := by
     sorry

   example : {r : ℤ | 3 ∣ r} ⊈ {s : ℤ | 0 ≤ s} := by
     sorry
   ```
9. Prove or disprove that
   \(\{m:\mathbb{Z} \mid m\ge 10\}\subseteq \{n:\mathbb{Z} \mid n^3-7n^2\geq 4n\}\).

   ```lean
   example : {m : ℤ | m ≥ 10} ⊆ {n : ℤ | n ^ 3 - 7 * n ^ 2 ≥ 4 * n} := by
     sorry

   example : {m : ℤ | m ≥ 10} ⊈ {n : ℤ | n ^ 3 - 7 * n ^ 2 ≥ 4 * n} := by
     sorry
   ```
10. Prove or disprove that
    \(\{n:\mathbb{Z} n\mid\text{ is even}\}=\{a:\mathbb{Z} \mid a\equiv 6\mod 2\}\).

    ```lean
    example : {n : ℤ | Even n} = {a : ℤ | a ≡ 6 [ZMOD 2]} := by
      sorry

    example : {n : ℤ | Even n} ≠ {a : ℤ | a ≡ 6 [ZMOD 2]} := by
      sorry
    ```
11. Prove or disprove that
    \(\{t:\mathbb{R} \mid t^2-5t+4=0\}=\{4\}\).

    ```lean
    example : {t : ℝ | t ^ 2 - 5 * t + 4 = 0} = {4} := by
      sorry

    example : {t : ℝ | t ^ 2 - 5 * t + 4 = 0} ≠ {4} := by
      sorry
    ```
12. Prove or disprove that \(\{k:\mathbb{Z} \mid 8\mid 6k\}=\{l:\mathbb{Z} \mid 8\mid l\}\).

    ```lean
    example : {k : ℤ | 8 ∣ 6 * k} = {l : ℤ | 8 ∣ l} := by
      sorry

    example : {k : ℤ | 8 ∣ 6 * k} ≠ {l : ℤ | 8 ∣ l} := by
      sorry
    ```
13. Prove or disprove that \(\{k:\mathbb{Z} \mid 7\mid 9k\}=\{l:\mathbb{Z} \mid 7\mid l\}\).

    ```lean
    example : {k : ℤ | 7 ∣ 9 * k} = {l : ℤ | 7 ∣ l} := by
      sorry

    example : {k : ℤ | 7 ∣ 9 * k} ≠ {l : ℤ | 7 ∣ l} := by
      sorry
    ```
14. Prove or disprove that \(\{1,2,3\}=\{1,2\}\).

    ```lean
    example : {1, 2, 3} = {1, 2} := by
      sorry

    example : {1, 2, 3} ≠ {1, 2} := by
      sorry
    ```
15. Prove that \(\{x:\mathbb{R} \mid x^2+3x+2=0\}=\{-1, -2\}\).

    ```lean
    example : {x : ℝ | x ^ 2 + 3 * x + 2 = 0} = {-1, -2} := by
      sorry
    ```

### 9.2. Set operations

#### 9.2.1. Example

> **Definition:**
>
> The *union* of two sets \(U\) and \(V\) in a type \(X\), denoted \(U \cup V\),
> is \(\{x : X \mid x \in U \lor x \in V\}\).

> **Problem:**
>
> Let \(t\) be a real number. Show that
> \(t∈\{x:\mathbb{R}\mid-1<x\}\cup \{x:\mathbb{R}\mid x < 1\}\).

> **Solution:**
>
> We must show that either \(-1<t\) or \(t<1\).
>
> **Case 1** (\(t\le 0\)): Then \(t<1\).
>
> **Case 2** (\(t> 0\)): Then \(-1<t\).

After unfolding the definitions in this problem, the goal is to show that

```
⊢ -1 < t ∨ t < 1
```

Here is the full proof in Lean.

```lean
example (t : ℝ) : t ∈ {x : ℝ | -1 < x} ∪ {x : ℝ | x < 1} := by
  dsimp
  obtain h | h := le_or_lt t 0
  · right
    addarith [h]
  · left
    addarith [h]
```

#### 9.2.2. Example

> **Problem:**
>
> Show that \(\{1,2\}\cup\{2,4\}=\{1,2,4\}\).

After applying set extensionality and unfolding definitions in this problem, the goal state is

```
n : ℕ
⊢ (n = 1 ∨ n = 2) ∨ n = 2 ∨ n = 4 ↔ n = 1 ∨ n = 2 ∨ n = 4
```

It’s possible to prove this directly – here’s how the start of such a proof would look.

```lean
example : {1, 2} ∪ {2, 4} = {1, 2, 4} := by
  ext n
  dsimp
  constructor
  · intro h
    obtain (h | h) | (h | h) := h
    · left
      apply h
    · right
      left
      apply h
  -- and much, much more
    · sorry
    · sorry
  · sorry
```

But there’s a better way: this is pure propositional logic, the situation the tactic `exhaust` (see
[Example 8.1.8]](#m2001-musketeer)) was designed for.

```lean
example : {2, 1} ∪ {2, 4} = {1, 2, 4} := by
  ext n
  dsimp
  exhaust
```

#### 9.2.3. Example

> **Definition:**
>
> The *intersection* of two sets \(U\) and \(V\) in a type \(X\), denoted
> \(U \cap V\),
> is \(\{x : X \mid x \in U \land x \in V\}\).

> **Problem:**
>
> Show that
> \(\{-2,3\}\cap \{x:\mathbb{Q}\mid x^2=9\}\subseteq \{a:\mathbb{Q}\mid 0<a\}\).

> **Solution:**
>
> We will show that for all real numbers \(t\), if \(t\) is -2 or 3 and \(t^2=9\) then
> \(0<t\).
>
> Indeed, let \(t\) be a real number and suppose that \(t\) is -2 or 3 and \(t^2=9\).
>
> **Case 1** (\(t=-2\)): We then have that
>
> \[\begin{split}(-2)^2&=t^2\\
> &=9,\end{split}\]
>
> contradiction.
>
> **Case 2** (\(t=3\)): Then \(0<t\) as desired.

```lean
example : {-2, 3} ∩ {x : ℚ | x ^ 2 = 9} ⊆ {a : ℚ | 0 < a} := by
  dsimp [Set.subset_def]
  intro t h
  obtain ⟨(h1 | h1), h2⟩ := h
  · have :=
    calc (-2) ^ 2 = t ^ 2 := by rw [h1]
      _ = 9 := by rw [h2]
    numbers at this
  · addarith [h1]
```

#### 9.2.4. Example

> **Problem:**
>
> Show that
> \(\{n:\mathbb{N}\mid 4\le n\}\cap \{n:\mathbb{N}\mid n<7\}\subseteq\{4,5,6\}\).

```lean
example : {n : ℕ | 4 ≤ n} ∩ {n : ℕ | n < 7} ⊆ {4, 5, 6} := by
  dsimp [Set.subset_def]
  intro n h
  obtain ⟨h1, h2⟩ := h
  interval_cases n <;> exhaust
```

#### 9.2.5. Example

> **Definition:**
>
> The *complement* of a set \(U\) in a type \(X\), denoted
> \(U^{c}\), is \(\{x : X \mid x \notin U\}\).

> **Problem:**
>
> Show that
> \(\{n:\mathbb{Z}\mid n\text{ even}\}^{c}=\{n:\mathbb{N}\mid n\text{ odd}\}\).

> **Solution:**
>
> Let \(n\) be an integer. We must show that \(n\) is odd if and only if it is not even.
> This is precisely [Example 4.5.5]](#m2001-even-iff-not-odd).

```lean
example : {n : ℤ | Even n}ᶜ = {n : ℤ | Odd n} := by
  ext n
  dsimp
  rw [odd_iff_not_even]
```

#### 9.2.6. Example

The *empty set* in a type \(X\) is the set which has no elements. That’s a slightly informal
description: here’s the strict definition.

> **Definition:**
>
> The *empty set* in a type \(X\), denoted \(\emptyset\), is \(\{x : X \mid \operatorname{False}\}\).

It is true by pure logic that no element of type \(X\) belongs to the empty set in \(X\),
and that the empty set in \(X\) is a subset of every set in \(X\).

```lean
example (x : ℤ) : x ∉ ∅ := by
  dsimp
  exhaust

example (U : Set ℤ) : ∅ ⊆ U := by
  dsimp [Set.subset_def]
  intro x
  exhaust
```

To show a set \(U\) in \(X\) is equal to the empty set, you must show that \(U\) has
no elements.

> **Problem:**
>
> Show that
> \(\{n:\mathbb{Z}\mid n\equiv 1\mod 5\}\cap\{n:\mathbb{N}\mid n\equiv 1\mod 5\}=\emptyset\).

Let’s consider the Lean proof first. We can write something like this:

```lean
example : {n : ℤ | n ≡ 1 [ZMOD 5]} ∩ {n : ℤ | n ≡ 2 [ZMOD 5]} = ∅ := by
  ext x
  dsimp
  constructor
  · intro hx
    obtain ⟨hx1, hx2⟩ := hx
    have :=
    calc 1 ≡ x [ZMOD 5] := by rel [hx1]
      _ ≡ 2 [ZMOD 5] := by rel [hx2]
    numbers at this
  · intro hx
    contradiction
```

But a certain amount of that proof (the `constructor`, and the `intro`/`contradiction` in the
second branch of the proof) is just logical messing-around. Now that we are familiar with the full
power of the `exhaust` tactic it is possible to streamline proofs like this. Have a look at the
goal state after the `dsimp`: it is

```
⊢ x ≡ 1 [ZMOD 5] ∧ x ≡ 2 [ZMOD 5] ↔ False
```

and you might mentally simplify this to the logically equivalent

```
⊢ ¬ (x ≡ 1 [ZMOD 5] ∧ x ≡ 2 [ZMOD 5])
```

This reformulation can be carried out in Lean using the incantation

```lean
suffices ¬(x ≡ 1 [ZMOD 5] ∧ x ≡ 2 [ZMOD 5]) by exhaust
```

Here is a full proof in Lean using this approach.

```lean
example : {n : ℤ | n ≡ 1 [ZMOD 5]} ∩ {n : ℤ | n ≡ 2 [ZMOD 5]} = ∅ := by
  ext x
  dsimp
  suffices ¬(x ≡ 1 [ZMOD 5] ∧ x ≡ 2 [ZMOD 5]) by exhaust
  intro hx
  obtain ⟨hx1, hx2⟩ := hx
  have :=
  calc 1 ≡ x [ZMOD 5] := by rel [hx1]
    _ ≡ 2 [ZMOD 5] := by rel [hx2]
  numbers at this
```

And here is that proof in words. We combine the use of set extensionality, the
definition-unfolding, and the logically equivalent reformulation into a single paragraph of setup.

> **Solution:**
>
> Let \(x\) be an integer. We will show that it is not true that both
> \(x\equiv 1\mod 5\) and \(x\equiv 2\mod 5\).
>
> Indeed, suppose that \(x\equiv 1\mod 5\) and \(x\equiv 2\mod 5\). Then
>
> \[\begin{split}1&\equiv x\mod 5\\
> &\equiv 2\mod 5,\end{split}\]
>
> contradiction.

#### 9.2.7. Example

The *universe set* in a type \(X\) is the set which contains everything in \(X\).
That’s again a slightly informal description: here’s the strict definition.

> **Definition:**
>
> The *universe set* in a type \(X\) is \(\{x : X \mid \operatorname{True}\}\).

It is true by pure logic that all elements of type \(X\) belong to the universe set in
\(X\), and that every set in \(X\) is a subset of the universe set in \(X\).

```lean
example (x : ℤ) : x ∈ univ := by dsimp

example (U : Set ℤ) : U ⊆ univ := by
  dsimp [Set.subset_def]
  intro x
  exhaust
```

Note that in Lean the universe set is denoted `univ`. On paper we often refer to the universe
set of \(X\) as \(X\), too (even though this is not strictly correct).

To show a set \(U\) in \(X\) is equal to the universe set, you must show that \(U\)
contains all elements of \(X\). Here we adapt Example 9.2.1 to be a problem about
the universe set.

> **Problem:**
>
> Show that \(\{x:\mathbb{R}\mid-1<x\}\cup \{x:\mathbb{R}\mid x < 1\}=\mathbb{R}\).

> **Solution:**
>
> We must show that for all real numbers \(t\), either \(-1<t\) or \(t<1\).
>
> **Case 1** (\(t\le 0\)): Then \(t<1\).
>
> **Case 2** (\(t> 0\)): Then \(-1<t\).

```lean
example : {x : ℝ | -1 < x} ∪ {x : ℝ | x < 1} = univ := by
  ext t
  dsimp
  suffices -1 < t ∨ t < 1 by exhaust
  obtain h | h := le_or_lt t 0
  · right
    addarith [h]
  · left
    addarith [h]
```

#### 9.2.8. Exercises

For the first five problems, I provide a tactic `check_equality_of_explicit_sets` which will prove
the statement if you have formulated it correctly. This tactic simply runs `ext`, then `dsimp`,
then `exhaust`:

```lean
macro "check_equality_of_explicit_sets" : tactic => `(tactic| (ext; dsimp; exhaust))
```

1. Write in an explicitly-listed finite set without repeats, or \(\emptyset\), which is equal
   to \(\{-1,2,4,4\}\cup\{3,-2,2\}\). When you have the correct answer, the given Lean proof
   will work.

   ```lean
   example : {-1, 2, 4, 4} ∪ {3, -2, 2} = sorry := by check_equality_of_explicit_sets
   ```
2. Write in an explicitly-listed finite set without repeats, or \(\emptyset\), which is equal
   to \(\{-1,2,4,4\}\cup\{3,-2,2\}\). When you have the correct answer, the given Lean proof
   will work.

   ```lean
   example : {0, 1, 2, 3, 4} ∩ {0, 2, 4, 6, 8} = sorry := by
     check_equality_of_explicit_sets
   ```
3. Write in an explicitly-listed finite set without repeats, or \(\emptyset\), which is equal
   to \(\{1,2\}\cap\{3\}\). When you have the correct answer, the given Lean proof
   will work.

   ```lean
   example : {1, 2} ∩ {3} = sorry := by check_equality_of_explicit_sets
   ```
4. Write in an explicitly-listed finite set without repeats, or \(\emptyset\), which is equal
   to \(\{3,4,5\}^c\cap\{1,3,5,7,9\}\). When you have the correct answer, the given Lean proof
   will work.

   ```lean
   example : {3, 4, 5}ᶜ ∩ {1, 3, 5, 7, 9} = sorry := by
     check_equality_of_explicit_sets
   ```
5. Prove that
   \(\{r:\mathbb{Z}\mid r\equiv 7\mod 10\}\subseteq \{s:\mathbb{Z}\mid s\equiv 1\mod 2\}\cap\{t:\mathbb{Z}\mid t\equiv 2\mod 5\}\).

   ```lean
   example : {r : ℤ | r ≡ 7 [ZMOD 10] }
       ⊆ {s : ℤ | s ≡ 1 [ZMOD 2]} ∩ {t : ℤ | t ≡ 2 [ZMOD 5]} := by
     sorry
   ```
6. Prove that
   \(\{n:\mathbb{Z}\mid 5\mid n\}\cap \{n:\mathbb{Z}\mid 8\mid n\}⊆\{n:\mathbb{Z}\mid 40\mid n\}\).

   ```lean
   example : {n : ℤ | 5 ∣ n} ∩ {n : ℤ | 8 ∣ n} ⊆ {n : ℤ | 40 ∣ n} := by
     sorry
   ```
7. Prove that \(\{n:\mathbb{Z}\mid 3\mid n\}\cup \{n:\mathbb{Z}\mid 2\mid n\}⊆\{n:\mathbb{Z}\mid n^2\equiv 1\mod 6\}^{c}\).

   ```lean
   example :
       {n : ℤ | 3 ∣ n} ∪ {n : ℤ | 2 ∣ n} ⊆ {n : ℤ | n ^ 2 ≡ 1 [ZMOD 6]}ᶜ := by
     sorry
   ```
8. Let us define that a set \(s\) in a type \(X\) has *size at least two*, if there exist
   \(x\_1\) and \(x\_2\) in \(s\) which are different, and has *size at least
   three*, if there exist \(x\_1\), \(x\_2\) and \(x\_3\) in \(s\) which are all
   different.

   Let \(s\) and \(t\) be sets of size at least two in some type \(X\), and suppose
   that \(s \cap t\) does not have size at least two. Show that \(s\cup t\) has size at
   least three.

   This problem features a ton of different cases. Use `exhaust` liberally to knock off sub-cases.

   ```lean
   def SizeAtLeastTwo (s : Set X) : Prop := ∃ x1 x2 : X, x1 ≠ x2 ∧ x1 ∈ s ∧ x2 ∈ s
   def SizeAtLeastThree (s : Set X) : Prop :=
     ∃ x1 x2 x3 : X, x1 ≠ x2 ∧ x1 ≠ x3 ∧ x2 ≠ x3 ∧ x1 ∈ s ∧ x2 ∈ s ∧ x3 ∈ s

   example {s t : Set X} (hs : SizeAtLeastTwo s) (ht : SizeAtLeastTwo t)
       (hst : ¬ SizeAtLeastTwo (s ∩ t)) :
       SizeAtLeastThree (s ∪ t) := by
     sorry
   ```

### 9.3. The type of sets

#### 9.3.1. Definition

Let \(X\) be a type. The collection of all sets in \(X\) can itself be
considered as a type. This type is sometimes denoted \(\mathcal{P}(X)\). 1 For
example, \(\{3,4,5\}\), \(\{n:\mathbb{N}\mid 8<n\}\), and
\(\{k:\mathbb{N}\mid\exists \ a, a^2=k\}\) are all sets of natural numbers, which
means they are all of type \(\mathcal{P}(\mathbb{N})\).

In Lean, for a type `X`, the type of sets in `X` is denoted `Set X`. Lean
will confirm that the three objects described above all have type `Set ℕ`:

```lean
#check {3, 4, 5} -- `{3, 4, 5} : Set ℕ`
#check {n : ℕ | 8 < n} -- `{n | 8 < n} : Set ℕ`
#check {k : ℕ | ∃ a, a ^ 2 = k} -- `{k | ∃ a, a ^ 2 = k} : Set ℕ`
```

This operation can be iterated: you can have sets in the type of sets, and so on.

```lean
#check {{3, 4}, {4, 5, 6}} -- `{{3, 4}, {4, 5, 6}} : Set (Set ℕ)`
#check {s : Set ℕ | 3 ∈ s} -- `{s | 3 ∈ s} : Set (Set ℕ)`
```

Exercise: write down an object of type `Set (Set (Set ℕ))`.

#### 9.3.2. Example

> **Problem:**
>
> Show that
> \(\{n:\mathbb{N}\mid n\text{ is even}\}\notin\{s:\mathcal{P}(\mathbb{N})\mid 3 ∈s\}\).

> **Solution:**
>
> We must show 3 is not even. It suffices to show that 3 is odd. Indeed, \(3=2\cdot 1+1\).

```lean
example : {n : ℕ | Nat.Even n} ∉ {s : Set ℕ | 3 ∈ s} := by
  dsimp
  rw [← Nat.odd_iff_not_even]
  use 1
  numbers
```

#### 9.3.3. Example

Since \(\mathcal{P}(X)\), the type of sets in \(X\), is itself a type, we can consider
functions to and from it.

For example, given a set \(s\) of natural numbers, we can build a new set
\(\{n:\mathbb{N}\mid n+1 \in s\}\). Lean confirms that this operation (let us call it
\(p\)) is a function from \(\mathcal{P}(\mathbb{N})\) to \(\mathcal{P}(\mathbb{N})\).

```lean
def p (s : Set ℕ) : Set ℕ := {n : ℕ | n + 1 ∈ s}

#check @p -- `p : Set ℕ → Set ℕ`
```

> **Problem:**
>
> Show that the function \(p\) is not injective.

> **Solution:**
>
> We must show that there exist sets \(s\) and \(t\), such that
> (i) \(\{n:\mathbb{N}\mid n+1 \in s\} = \{n:\mathbb{N}\mid n+1 \in t\}\), and
> (ii) \(s \ne t\).
>
> Indeed, let us show that the sets \(\{0\}\) and \(\emptyset\) have this property. We must
> show,
> (i) \(\{n:\mathbb{N}\mid n+1 = 0\} = \emptyset\), and
> (ii) \(\{0\} \ne \emptyset\).
>
> For the first statement, let \(x\) be a natural number. We must show that \(x+1 \ne 0\),
> which is true since \(x+1 > 0\).
>
> For the second statement, we must show that there exists a natural number \(k\) such that
> \(k\in\{0\}\) and \(k\notin\emptyset\), or vice versa. Indeed, \(k=0\) has this
> property.

```lean
example : ¬ Injective p := by
  dsimp [Injective, p]
  push_neg
  use {0}, ∅
  dsimp
  constructor
  · ext x
    dsimp
    suffices x + 1 ≠ 0 by exhaust
    apply ne_of_gt
    extra
  · ext
    push_neg
    use 0
    dsimp
    exhaust
```

#### 9.3.4. Example

> **Problem:**
>
> Consider the function \(q : \mathcal{P}(\mathbb{Z})\to \mathcal{P}(\mathbb{Z})\) defined by,
> \(q(s)=\{n:\mathbb{Z}\mid n+1\in s\}\). Show that the function \(q\) is injective.

> **Solution:**
>
> We must show that for all sets \(s\) and \(t\),
> if \(\{n:\mathbb{Z}\mid n+1 \in s\} = \{n:\mathbb{Z}\mid n+1 \in t\}\),
> then \(s = t\).
>
> Indeed, let \(s\) and \(t\) be sets and suppose that
> \(\{n:\mathbb{Z}\mid n+1 \in s\} = \{n:\mathbb{Z}\mid n+1 \in t\}\).
>
> Let \(k\) be an integer.
> We must show that \(k\in s\) if and only if \(k\in t\).
> Indeed, by the assumption,
> \(k -1\in\{n:\mathbb{Z}\mid n+1 \in s\}\) if and only if \(k-1\in \{n:\mathbb{Z}\mid n+1 \in t\}\).
> Simplifying,
> \(k -1+1 \in s\) if and only if \(k-1+1\in t\), and so
> \(k \in s\) if and only if \(k\in t\).

```lean
def q (s : Set ℤ) : Set ℤ := {n : ℤ | n + 1 ∈ s}

example : Injective q := by
  dsimp [Injective, q]
  intro s t hst
  ext k
  have hk : k - 1 ∈ {n | n + 1 ∈ s} ↔ k - 1 ∈ {n | n + 1 ∈ t} := by rw [hst]
  dsimp at hk
  conv at hk => ring
  apply hk
```

#### 9.3.5. Example

> **Problem:**
>
> Let \(X\) be a type. Prove that there does not exist a surjective function from \(X\) to
> \(\mathcal{P}(X)\).

> **Solution:**
>
> Suppose for the sake of contradiction that some function \(f:X\to\mathcal{P}(X)\) were
> surjective. We introduce the
> following set in \(X\):
>
> \[s :=\{x:X\mid x\notin f(x)\}.\]
>
> Since \(f\) is surjective, there exists some \(x\) of type \(X\) such that
> \(f(x)=s\). We consider cases according to whether \(x\in s\).
>
> **Case 1** (\(x\in s\)): Then by the definition of \(s\), it is true that
> \(x\notin f(x)\), so since \(f(x)=s\), we have \(x\notin s\), contradiction.
>
> **Case 2** (\(x\notin s\)): Then by the definition of \(s\), it is false that
> \(x\notin f(x)\), so since \(f(x)=s\), we have that it is false that \(x\notin s\),
> contradiction.

```lean
example : ¬ ∃ f : X → Set X, Surjective f := by
  intro h
  obtain ⟨f, hf⟩ := h
  let s : Set X := {x | x ∉ f x}
  obtain ⟨x, hx⟩ := hf s
  by_cases hxs : x ∈ s
  · have hfx : x ∉ f x := hxs
    rw [hx] at hfx
    contradiction
  · have hfx : ¬ x ∉ f x := hxs
    rw [hx] at hfx
    contradiction
```

The idea behind this twisty proof is sometimes called the
[barber paradox](https://en.wikipedia.org/wiki/Barber_paradox). Here is the version featuring
barbers. There is a town with a barber, and in this town, the barber shaves all men who don’t shave
themselves. Paradox: does the barber shave himself?

#### 9.3.6. Exercises

1. Consider the function \(r : \mathcal{P}(\mathbb{N})\to \mathcal{P}(\mathbb{N})\) defined by,
   \(r(s)=s\cup \{3\}\). Show that \(r\) is not injective.

   ```lean
   def r (s : Set ℕ) : Set ℕ := s ∪ {3}

   example : ¬ Injective r := by
     sorry
   ```
2. Consider the sequence \(U\_n\) of sets of integers defined recursively by,

   \[\begin{split}U\_0&=\mathbb{Z} \\
   \text{for }n:\mathbb{N},\quad U\_{n+1} &=\{x:\mathbb{Z}\mid \exists \ y\in U\_n, x = 2y \}\end{split}\]

   Show that for all natural numbers \(n\), \(U\_n=\{x:\mathbb{Z}\mid 2^n\mid x\}\).

   ```lean
   def U : ℕ → Set ℤ
     | 0 => univ
     | n + 1 => {x : ℤ | ∃ y ∈ U n, x = 2 * y}

   example (n : ℕ) : U n = {x : ℤ | (2:ℤ) ^ n ∣ x} := by
     simple_induction n with k hk
     · rw [U]
       sorry
     · rw [U]
       ext x
       dsimp
       sorry
   ```

Footnotes

1
:   In set theory, which we *don’t* use as the logical foundation of this book,
    \(\mathcal{P}(X)\) is referred to as
    the *power set* of \(X\). Maybe in type theory we should refer to it as the “power type” of
    \(X\) …?

---



## 10. Relations {#m2001-10-relations}

> 📄 Source: https://hrmacbeth.github.io/math2001/10_Relations.html

Just as sets provide a convenient language for properties of the objects in a type,
*relations* provide a convenient language for the properties of the pairs of objects
in a type. This might sound dry and abstract, but such properties turn up all over
mathematics: the property of one real
number being less than another; the property of one integer being congruent to
another modulo 5; the property of one set being a subset of another; the property
of one function being inverse to another.

In this chapter we introduce some of the important properties which relations
themselves can have: they can be *reflexive*, *symmetric*, *antisymmetric* or
*transitive*, or any combination of these.

### 10.1. Reflexive, symmetric, antisymmetric, transitive

#### 10.1.1. Example

> **Definition:**
>
> A relation \(\sim\) on a type \(X\) is *reflexive*, if for all \(x\) of type
> \(X\), it is true that \(x\sim x\).

> **Problem:**
>
> Prove that the relation \(|\) on \(\mathbb{N}\) is reflexive.

> **Solution:**
>
> We must show that for all natural numbers \(x\), \(x\mid x\). Indeed, let \(x\) be a
> natural number; then \(x=x\cdot 1\).

```lean
example : Reflexive ((·:ℕ) ∣ ·) := by
  dsimp [Reflexive]
  intro x
  use 1
  ring
```

> **Definition:**
>
> A relation \(\sim\) on a type \(X\) is *symmetric*, if for all \(x\) and \(y\) of
> type \(X\), if \(x\sim y\), then \(y\sim x\).

> **Problem:**
>
> Prove that the relation \(|\) on \(\mathbb{N}\) is not symmetric.

> **Solution:**
>
> We must show that there exist natural numbers \(x\) and \(y\) such that, \(x\mid y\)
> and \(y\mid x\). Indeed, we have \(2=1\cdot 2\), so \(1\mid 2\), and
> \(2\cdot 0<1<2\cdot 1\), so \(2\not \mid 1\).

```lean
example : ¬ Symmetric ((·:ℕ) ∣ ·) := by
  dsimp [Symmetric]
  push_neg
  use 1, 2
  constructor
  · use 2
    numbers
  · apply Nat.not_dvd_of_exists_lt_and_lt
    use 0
    constructor
    · numbers
    · numbers
```

> **Definition:**
>
> A relation \(\sim\) on a type \(X\) is *antisymmetric*, if for all \(x\) and
> \(y\) of type \(X\), if \(x\sim y\) and \(y\sim x\), then \(x=y\).

> **Problem:**
>
> Prove that the relation \(|\) on \(\mathbb{N}\) is antisymmetric.

> **Solution:**
>
> We first note the following fact (\(\star\)): if \(m\) and \(n\) are natural numbers with \(m=0\)
> and \(m\mid n\), then \(m=n\). Indeed, since \(m\mid n\) there exists a natural
> number \(k\) such that \(n=mk\), and then
>
> \[\begin{split}m&=0\\
> &=0\cdot k\\
> &=mk\\
> &=n.\end{split}\]
>
> We now return to the original problem. We must show that for all natural numbers \(x\) and \(y\), if \(x\mid y\) and \(y\mid x\), then \(x=y\). Indeed, let \(x\) and \(y\) be natural numbers and suppose \(x\mid y\) and \(y\mid x\). If
> \(x=0\) then by (\(\star\)) we are done; likewise if \(y=0\). Otherwise, \(x>0\),
> so since \(y\mid x\) we have \(y\le x\), and \(y>0\),
> so since \(x\mid y\) we have \(x\le y\), Putting these together, \(x=y\).

```lean
example : AntiSymmetric ((·:ℕ) ∣ ·) := by
  have H : ∀ {m n}, m = 0 → m ∣ n → m = n
  · intro m n h1 h2
    obtain ⟨k, hk⟩ := h2
    calc m = 0 := by rw [h1]
      _ = 0 * k := by ring
      _ = m * k := by rw [h1]
      _ = n := by rw [hk]
  dsimp [AntiSymmetric]
  intro x y h1 h2
  obtain hx | hx := Nat.eq_zero_or_pos x
  · apply H hx h1
  obtain hy | hy := Nat.eq_zero_or_pos y
  · have : y = x := by apply H hy h2
    rw [this]
  apply le_antisymm
  · apply Nat.le_of_dvd hy h1
  · apply Nat.le_of_dvd hx h2
```

> **Definition:**
>
> A relation \(\sim\) on a type \(X\) is *transitive*, if for all \(x\), \(y\) and
> \(z\) of type \(X\), if \(x\sim y\) and \(y\sim z\), then \(x\sim z\).

> **Problem:**
>
> Prove that the relation \(|\) on \(\mathbb{N}\) is transitive.

> **Solution:**
>
> We must show that for all natural numbers \(a\), \(b\) and \(c\), if \(a\mid b\)
> and \(b\mid c\) then \(a\mid c\).
>
> Indeed, let \(a\), \(b\) and \(c\) be natural numbers with these properties. Since
> \(a\mid b\) there exists a natural number \(k\) such that \(b=ak\), and since
> \(b\mid c\) there exists a natural number \(l\) such that \(c=bl\). We then have
>
> \[\begin{split}c&=bl\\
> &=(ak)l\\
> &=a(kl),\end{split}\]
>
> so \(a\mid c\).

```lean
example : Transitive ((·:ℕ) ∣ ·) := by
  dsimp [Transitive]
  intro a b c hab hbc
  obtain ⟨k, hk⟩ := hab
  obtain ⟨l, hl⟩ := hbc
  use k * l
  calc c = b * l := by rw [hl]
    _ = (a * k) * l := by rw [hk]
    _ = a * (k * l) := by ring
```

#### 10.1.2. Example

> **Problem:**
>
> Determine which of these properties hold for the relation \(=\) on \(\mathbb{R}\):
>
> 1. reflexive;
> 2. symmetric;
> 3. antisymmetric;
> 4. transitive.

```lean
example : Reflexive ((·:ℝ) = ·) := by
  dsimp [Reflexive]
  intro x
  ring

example : Symmetric ((·:ℝ) = ·) := by
  dsimp [Symmetric]
  intro x y h
  rw [h]

example : AntiSymmetric ((·:ℝ) = ·) := by
  dsimp [AntiSymmetric]
  intro x y h1 h2
  rw [h1]

example : Transitive ((·:ℝ) = ·) := by
  dsimp [Transitive]
  intro x y z h1 h2
  rw [h1, h2]
```

#### 10.1.3. Example

> **Problem:**
>
> Determine which of these properties hold for the relation \(\sim\) on \(\mathbb{R}\), defined by, \(x\sim y\) if \((x-y)^2\leq 1\):
>
> 1. reflexive;
> 2. symmetric;
> 3. antisymmetric;
> 4. transitive.

```lean
local infix:50 "∼" => fun (x y : ℝ) ↦ (x - y) ^ 2 ≤ 1

example : Reflexive (· ∼ ·) := by
  dsimp [Reflexive]
  intro x
  calc (x - x) ^ 2 = 0 := by ring
    _ ≤ 1 := by numbers

example : Symmetric (· ∼ ·) := by
  dsimp [Symmetric]
  intro x y h
  calc (y - x) ^ 2 = (x - y) ^ 2 := by ring
    _ ≤ 1 := by rel [h]

example : ¬ AntiSymmetric (· ∼ ·) := by
  dsimp [AntiSymmetric]
  push_neg
  use 1, 1.1
  constructor
  · numbers
  constructor
  · numbers
  · numbers

example : ¬ Transitive (· ∼ ·) := by
  dsimp [Transitive]
  push_neg
  use 1, 1.9, 2.5
  constructor
  · numbers
  constructor
  · numbers
  · numbers
```

#### 10.1.4. Example

Consider the following finite inductive type Hand.

```lean
inductive Hand
  | rock
  | paper
  | scissors
```

Consider the following relation \(\prec\) on the Hand type.

```lean
@[reducible] def r : Hand → Hand → Prop
  | rock, rock => False
  | rock, paper => True
  | rock, scissors => False
  | paper, rock => False
  | paper, paper => False
  | paper, scissors => True
  | scissors, rock => True
  | scissors, paper => False
  | scissors, scissors => False

local infix:50 " ≺ " => r
```

![_images/rock-paper-scissors.svg](https://hrmacbeth.github.io/math2001/_images/rock-paper-scissors.svg)

> **Problem:**
>
> Determine which of these properties hold for the relation \(\prec\):
>
> 1. reflexive;
> 2. symmetric;
> 3. antisymmetric;
> 4. transitive.

```lean
example : ¬ Reflexive (· ≺ ·) := by
  dsimp [Reflexive]
  push_neg
  use rock
  exhaust

example : ¬ Symmetric (· ≺ ·) := by
  dsimp [Symmetric]
  push_neg
  use rock, paper
  exhaust

example : AntiSymmetric (· ≺ ·) := by
  dsimp [AntiSymmetric]
  intro x y
  cases x <;> cases y <;> exhaust

example : ¬ Transitive (· ≺ ·) := by
  dsimp [Transitive]
  push_neg
  use rock, paper, scissors
  exhaust
```

#### 10.1.5. Exercises

1. Show that the relation \(<\) on \(\mathbb{R}\) is not symmetric.

   ```lean
   example : ¬ Symmetric ((·:ℝ) < ·) := by
     sorry
   ```
2. Show that the relation \(\sim\) on \(\mathbb{Z}\), defined by, \(x\sim y\) if
   \(x\equiv y \mod 2\), is not antisymmetric.

   ```lean
   local infix:50 "∼" => fun (x y : ℤ) ↦ x ≡ y [ZMOD 2]

   example : ¬ AntiSymmetric (· ∼ ·) := by
     sorry
   ```
3. Consider the following finite inductive type Little, and the following relation \(\sim\) on
   the Little type. Determine which of these properties hold for the relation \(\sim\):

   1. reflexive;
   2. symmetric;
   3. antisymmetric;
   4. transitive.

   ```lean
   section
   inductive Little
     | meg
     | jo
     | beth
     | amy
     deriving DecidableEq

   open Little

   @[reducible] def s : Little → Little → Prop
     | meg, meg => True
     | meg, jo => True
     | meg, beth => True
     | meg, amy => True
     | jo, meg => True
     | jo, jo => True
     | jo, beth => True
     | jo, amy => False
     | beth, meg => True
     | beth, jo => True
     | beth, beth => False
     | beth, amy => True
     | amy, meg => True
     | amy, jo => False
     | amy, beth => True
     | amy, amy => True

   local infix:50 " ∼ " => s
   ```

   ![_images/meg-jo-beth-amy.svg](https://hrmacbeth.github.io/math2001/_images/meg-jo-beth-amy.svg)

   ```lean
   example : Reflexive (· ∼ ·) := by
     sorry

   example : ¬ Reflexive (· ∼ ·) := by
     sorry

   example : Symmetric (· ∼ ·) := by
     sorry

   example : ¬ Symmetric (· ∼ ·) := by
     sorry

   example : AntiSymmetric (· ∼ ·) := by
     sorry

   example : ¬ AntiSymmetric (· ∼ ·) := by
     sorry

   example : Transitive (· ∼ ·) := by
     sorry

   example : ¬ Transitive (· ∼ ·) := by
     sorry
   ```
4. Determine which of these properties hold for the relation \(\sim\) on \(\mathbb{Z}\),
   defined by, \(x\sim y\) if \(y\equiv x+1\mod 5\):

   1. reflexive;
   2. symmetric;
   3. antisymmetric;
   4. transitive.

   Also sketch (a representative portion of) this relation as a directed graph.

   ```lean
   local infix:50 "∼" => fun (x y : ℤ) ↦ y ≡ x + 1 [ZMOD 5]

   example : Reflexive (· ∼ ·) := by
     sorry

   example : ¬ Reflexive (· ∼ ·) := by
     sorry

   example : Symmetric (· ∼ ·) := by
     sorry

   example : ¬ Symmetric (· ∼ ·) := by
     sorry

   example : AntiSymmetric (· ∼ ·) := by
     sorry

   example : ¬ AntiSymmetric (· ∼ ·) := by
     sorry

   example : Transitive (· ∼ ·) := by
     sorry

   example : ¬ Transitive (· ∼ ·) := by
     sorry
   ```
5. Determine which of these properties hold for the relation \(\sim\) on \(\mathbb{Z}\),
   defined by, \(x\sim y\) if \(x+y\equiv 0\mod 3\):

   1. reflexive;
   2. symmetric;
   3. antisymmetric;
   4. transitive.

   Also sketch (a representative portion of) this relation as a directed graph.

   ```lean
   local infix:50 "∼" => fun (x y : ℤ) ↦ x + y ≡ 0 [ZMOD 3]

   example : Reflexive (· ∼ ·) := by
     sorry

   example : ¬ Reflexive (· ∼ ·) := by
     sorry

   example : Symmetric (· ∼ ·) := by
     sorry

   example : ¬ Symmetric (· ∼ ·) := by
     sorry

   example : AntiSymmetric (· ∼ ·) := by
     sorry

   example : ¬ AntiSymmetric (· ∼ ·) := by
     sorry

   example : Transitive (· ∼ ·) := by
     sorry

   example : ¬ Transitive (· ∼ ·) := by
     sorry
   ```
6. Determine which of these properties hold for the relation \(\subseteq\) on
   \(\mathcal{P}(\mathbb{N})\), the type of sets of natural numbers.

   1. reflexive;
   2. symmetric;
   3. antisymmetric;
   4. transitive.

   Also sketch (a representative portion of) this relation as a directed graph.

   ```lean
   example : Reflexive ((· : Set ℕ) ⊆ ·) := by
     sorry

   example : ¬ Reflexive ((· : Set ℕ) ⊆ ·) := by
     sorry

   example : Symmetric ((· : Set ℕ) ⊆ ·) := by
     sorry

   example : ¬ Symmetric ((· : Set ℕ) ⊆ ·) := by
     sorry

   example : AntiSymmetric ((· : Set ℕ) ⊆ ·) := by
     sorry

   example : ¬ AntiSymmetric ((· : Set ℕ) ⊆ ·) := by
     sorry

   example : Transitive ((· : Set ℕ) ⊆ ·) := by
     sorry

   example : ¬ Transitive ((· : Set ℕ) ⊆ ·) := by
     sorry
   ```
7. Determine which of these properties hold for the relation \(\prec\) on \(\mathbb{R}^2\),
   defined by, \((x\_1, y\_1) \prec (x\_2,y\_2)\) if \(x\_1\le x\_2\) and \(y\_1\le y\_2\):

   1. reflexive;
   2. symmetric;
   3. antisymmetric;
   4. transitive.

   ```lean
   local infix:50 "≺" => fun ((x1, y1) : ℝ × ℝ) (x2, y2) ↦ (x1 ≤ x2 ∧ y1 ≤ y2)

   example : Reflexive (· ≺ ·) := by
     sorry

   example : ¬ Reflexive (· ≺ ·) := by
     sorry

   example : Symmetric (· ≺ ·) := by
     sorry

   example : ¬ Symmetric (· ≺ ·) := by
     sorry

   example : AntiSymmetric (· ≺ ·) := by
     sorry

   example : ¬ AntiSymmetric (· ≺ ·) := by
     sorry

   example : Transitive (· ≺ ·) := by
     sorry

   example : ¬ Transitive (· ≺ ·) := by
     sorry
   ```

### 10.2. Equivalence relations

#### 10.2.1. Example

> **Definition:**
>
> A relation is an *equivalence relation*, if it is reflexive, symmetric and transitive.

> **Problem:**
>
> Let \(n\) be an integer.
> Show that the relation \(\sim\) on \(\mathbb{Z}\) defined by, \(x\sim y\) if
> \(x\equiv y\mod n\) is an equivalence relation.

> **Solution:**
>
> By [Example 3.3.10]](#m2001-modeq-refl), the relation \(\sim\) is reflexive.
>
> By two of the exercises to [Section 3.3]](#m2001-theory-modular), the
> relation \(\sim\) is symmetric and transitive.

```lean
variable (n : ℤ)

local infix:50 "∼" => fun (x y : ℤ) ↦ x ≡ y [ZMOD n]

example : Reflexive (· ∼ ·) := by
  dsimp [Reflexive]
  apply Int.ModEq.refl

example : Symmetric (· ∼ ·) := by
  dsimp [Symmetric]
  apply Int.ModEq.symm

example : Transitive (· ∼ ·) := by
  dsimp [Transitive]
  apply Int.ModEq.trans
```

Let’s sketch (a portion of) this relation as a directed graph, say for \(n=3\).

![_images/mod3alt.png](https://hrmacbeth.github.io/math2001/_images/mod3alt.png)

It’s a mess, but it’s just possible to make out a pattern: the numbers form cliques, with, for
example, … -5, -2, 1, 4, 7, … all connected to each other and to nothing else. We introduce
the following visual shorthand for such a directed graph: when nodes are given different colours,
this represents the directed graph in which all nodes of a given colour are connected
bi-directionally to all nodes of that colour (including themselves) and to no other nodes.

![_images/mod3.png](https://hrmacbeth.github.io/math2001/_images/mod3.png)

#### 10.2.2. Example

> **Problem:**
>
> Show that the relation \(\sim\) on \(\mathbb{Z}\) defined by \(a\sim b\) if
> \(a^2=b^2\) is an equivalence relation.

> **Solution:**
>
> For all integers \(x\), we have \(x^2=x^2\), so \(x\sim x\). Therefore the relation
> \(\sim\) is reflexive.
>
> For all integers \(x\) and \(y\), we have that if \(x\sim y\) then \(x^2=y^2\),
> so \(y^2=x^2\), so \(y\sim z\).
> Therefore the relation \(\sim\) is symmetric.
>
> For all integers \(x\), \(y\) and \(z\), we have that if \(x\sim y\) and
> \(y\sim z\) then \(x^2=y^2\) and \(y^2=z^2\), so
>
> \[\begin{split}x^2&=y^2\\
> &=z^2,\end{split}\]
>
> so \(x\sim z\). Therefore the relation \(\sim\) is transitive.

```lean
local infix:50 "∼" => fun (x y : ℤ) ↦ x ^ 2 = y ^ 2

example : Reflexive (· ∼ ·) := by
  dsimp [Reflexive]
  intro x
  ring

example : Symmetric (· ∼ ·) := by
  dsimp [Symmetric]
  intro x y hxy
  rw [hxy]

example : Transitive (· ∼ ·) := by
  dsimp [Transitive]
  intro x y z hxy hyz
  calc x ^ 2 = y ^ 2 := by rw [hxy]
    _ = z ^ 2 := by rw [hyz]
```

If you try to sketch the directed graph for this relation, you will see that it has the same
“clique” behaviour as the previous relation. After sketching the directed graph, sketch the
multicoloured “clique” version.

#### 10.2.3. Example

You have probably guessed that the colouring can be made consistent in this way for any equivalence
relation. Let’s make this mathematically rigorous.

Let \(r\) be a relation on a type \(\alpha\), denoted \(\sim\) in infix notation.

> **Definition:**
>
> For \(a\) in \(\alpha\), the *equivalence class* of \(a\) (denoted \([a]\)) is
> \(\{b:\alpha\mid a\sim b\}\).

> **Theorem:**
>
> If the relation \(r\) is symmetric and transitive, then for all \(a\_1\) and \(a\_2\),
> if \(a\_1\sim a\_2\), then \([a\_1]=[a\_2]\).

> **Proof:**
>
> We must show that for all \(b\) in \(\alpha\), \(a\_1\sim b\) if and only if
> \(a\_2\sim b\).
>
> First, suppose that \(a\_1\sim b\). Since \(a\_1\sim a\_2\), by symmetry
> \(a\_2\sim a\_1\), and so by transitivity \(a\_2\sim a\_1\sim b\).
>
> Conversely, suppose that \(a\_2\sim b\). Then since \(a\_1\sim a\_2\), by transitivity
> \(a\_1\sim a\_2\sim b\).

```lean
notation:arg "⦍" a "⦐" => { b | a ∼ b }

theorem EquivalenceClass.eq_of_rel (h_symm : @Symmetric α r) (h_trans : @Transitive α r)
    {a1 a2 : α} (ha : a1 ∼ a2) :
    ⦍a1⦐ = ⦍a2⦐ := by
  ext b
  dsimp
  constructor
  · intro ha1b
    apply h_trans (y := a1)
    · apply h_symm ha
    · apply ha1b
  · intro ha2b
    apply h_trans ha ha2b
```

> **Theorem:**
>
> If the relation \(r\) is reflexive, then every \(a\) is an element of its own equivalence
> class: \(a\in [a]\).

> **Proof:**
>
> We must show that for all \(a\), it is true that \(a\sim a\), and this is the definition
> of reflexivity.

```lean
theorem EquivalenceClass.mem_self (h_refl : @Reflexive α r) (a : α) :
    a ∈ { b : α | a ∼ b } := by
  dsimp
  apply h_refl
```

#### 10.2.4. Example

Consider the relation \(=\) on \(\mathbb{Z}\). It is an equivalence relation, by
Example 10.1.2.

Exercise: Sketch this relation, by drawing (a portion of) the underlying type
\(\mathbb{Z}\) along a line, then colouring each equivalence class in a different colour.

#### 10.2.5. Example

> **Problem:**
>
> Show that the relation \(\sim\) on \(\mathbb{Z}\times\mathbb{N}\) defined by,
> \((a,b)\sim(c,d)\) if \(a(d+1)=c(b+1)\), is an equivalence relation.

> **Solution:**
>
> For all \((a,b)\) in \(\mathbb{Z}\times\mathbb{N}\), \(a(b+1)=a(b+1)\), so
> \((a,b)\sim (a,b)\). Therefore \(\sim\) is reflexive.
>
> For all \((a,b)\) and \((c,d)\) in \(\mathbb{Z}\times\mathbb{N}\), if
> \((a,b)\sim (c,d)\) then \(a(d+1)=c(b+1)\), so \(c(b+1)=a(d+1)\), so
> \((c,d)\sim (a,b)\). Therefore \(\sim\) is symmetric.
>
> For all \((a,b)\), \((c,d)\) and \((e,f)\) in \(\mathbb{Z}\times\mathbb{N}\), if
> \((a,b)\sim (c,d)\) and \((c,d)\sim (e,f)\) then \(a(d+1)=c(b+1)\) and
> \(c(f+1)=e(d+1)\). We will show that \(a(f+1)=e(b+1)\), which will imply that
> \((a,b)\sim (e,f)\), which proves the transitivity of \(\sim\).
>
> Since \(d+1>0\), it suffices to prove that
> \((d+1)\left[a(f+1)\right]=(d+1)\left[e(b+1)\right]\). Let
> \(B:= b+1\), \(D:= d+1\), and \(F:= f+1\): then we know that \(aD=cB\) and
> \(cF=eD\) and need to prove that \(D(aF)=D(eB)\).
>
> Indeed,
>
> \[\begin{split}D (a F) &= (a D) F \\
> & = (c B) F \\
> & = (c F) B \\
> & = (e D) B \\
> & = D (e B).\end{split}\]

```lean
local infix:50 "∼" => fun ((a, b) : ℤ × ℕ) (c, d) ↦ a * (d + 1) = c * (b + 1)

example : Reflexive (· ∼ ·) := by
  dsimp [Reflexive]
  intro (a, b)
  dsimp

example : Symmetric (· ∼ ·) := by
  dsimp [Symmetric]
  intro (a, b) (c, d) h
  dsimp at *
  rw [h]

example : Transitive (· ∼ ·) := by
  dsimp [Transitive]
  intro (a, b) (c, d) (e, f) h1 h2
  dsimp at *
  set B := (b:ℤ) + 1
  set D := (d:ℤ) + 1
  set F := (f:ℤ) + 1
  have :=
  calc D * (a * F) = (a * D) * F := by ring
    _ = (c * B) * F := by rw [h1]
    _ = (c * F) * B := by ring
    _ = (e * D) * B := by rw [h2]
    _ = D * (e * B) := by ring
  cancel D at this
```

Now, sketch the relation \(\sim\), by drawing (a portion of) the underlying type
\(\mathbb{Z}\times\mathbb{N}\) in the plane, then colouring each equivalence class in a
different colour.

#### 10.2.6. Example

> **Theorem:**
>
> The relation \(\sim\) on the type of level-0 types defined by,
> \(\alpha\sim\beta\) if there exists a bijective function \(f:\alpha\to\beta\),
> is an equivalence relation.

```lean
local infix:50 "∼" => fun (α β : Type) ↦ ∃ f : α → β, Bijective f

example : Reflexive (· ∼ ·) := by
  dsimp [Reflexive]
  intro α
  use id
  rw [bijective_iff_exists_inverse]
  use id
  constructor
  · rfl
  · rfl

example : Symmetric (· ∼ ·) := by
  dsimp [Symmetric]
  intro α β h
  obtain ⟨f, hf⟩ := h
  rw [bijective_iff_exists_inverse] at hf
  obtain ⟨g, hfg1, hfg2⟩ := hf
  use g
  rw [bijective_iff_exists_inverse]
  use f
  constructor
  · apply hfg2
  · apply hfg1

example : Transitive (· ∼ ·) := by
  dsimp [Transitive]
  intro α β γ h1 h2
  obtain ⟨f1, hf1a, hf1b⟩ := h1
  obtain ⟨f2, hf2a, hf2b⟩ := h2
  use f2 ∘ f1
  constructor
  · apply Injective.comp
    · apply hf2a
    · apply hf1a
  · apply Surjective.comp
    · apply hf2b
    · apply hf1b
```

#### 10.2.7. Exercises

1. Consider the relation \(\sim\) on \(\mathbb{Z}\) defined by, \(a\sim b\) if there
   exist positive integers \(m\) and \(n\) such that
   \(am=bn\).

   - Show that \(\sim\) is an equivalence relation.
   - Sketch the relation \(\sim\), by drawing (a portion of) the underlying type
     \(\mathbb{Z}\) along a line, then
     colouring each equivalence class in a different colour.

   ```lean
   local infix:50 "∼" => fun (a b : ℤ) ↦ ∃ m n, m > 0 ∧ n > 0 ∧ a * m = b * n

   example : Reflexive (· ∼ ·) := by
     sorry

   example : Symmetric (· ∼ ·) := by
     sorry

   example : Transitive (· ∼ ·) := by
     sorry
   ```
2. Consider the relation \(\sim\) on \(\mathbb{N}^2\) defined by, \((a,b)\sim(c,d)\) if
   \(a+d=b+c\).

   - Show that \(\sim\) is an equivalence relation.
   - Sketch the relation \(\sim\), by drawing (a portion of) the underlying type
     \(\mathbb{N}^2\) in the plane, then
     colouring each equivalence class in a different colour.

   ```lean
   local infix:50 "∼" => fun ((a, b) : ℕ × ℕ) (c, d) ↦ a + d = b + c

   example : Reflexive (· ∼ ·) := by
     sorry

   example : Symmetric (· ∼ ·) := by
     sorry

   example : Transitive (· ∼ ·) := by
     sorry
   ```
3. Consider the relation \(\sim\) on \(\mathbb{Z}^2\) defined by, \((a,b)\sim(c,d)\) if
   there exist positive integers \(m\) and \(n\) with \(mb(b^2-3a^2)=nd(d^2-3c^2)\).

   - Show that \(\sim\) is an equivalence relation.
   - Sketch the relation \(\sim\), by drawing (a portion of) the underlying type
     \(\mathbb{Z}^2\) in the plane, then
     colouring each equivalence class in a different colour.

   ```lean
   local infix:50 "∼" => fun ((a, b) : ℤ × ℤ) (c, d) ↦
     ∃ m n, m > 0 ∧ n > 0 ∧ m * b * (b ^ 2 - 3 * a ^ 2) = n * d * (d ^ 2 - 3 * c ^ 2)

   example : Reflexive (· ∼ ·) := by
     sorry

   example : Symmetric (· ∼ ·) := by
     sorry

   example : Transitive (· ∼ ·) := by
     sorry
   ```

---



## Homework {#m2001-homework}

> 📄 Source: https://hrmacbeth.github.io/math2001/Homework.html

### Homework 0

This homework is not for credit. It is just to get you used to the Gradescope system.

1. Let \(n\geq 5\) be an integer. Show that \(n ^ 2 > 2n + 11\).

   ```lean
   @[autograded 5]
   theorem problem1 {n : ℤ} (hn : n ≥ 5) : n ^ 2 > 2 * n + 11 :=
     sorry
   ```

---



## Index of Tactics {#m2001-index-of-tactics}

> 📄 Source: https://hrmacbeth.github.io/math2001/Index_of_Tactics.html

## Index of Lean tactics

Tactics marked \* are specific to this book, so you will not be able to get help with them by
googling/consulting internet forums/etc. Reread the indicated part of the book or ask
your instructor!

\* `addarith` (first use: [Section 1.5]](#m2001-shortcut))

Attempts to solve an equality or inequality by moving terms from the left-hand side to the
right-hand side, or vice versa.

`apply` (first use: [Section 2.2]](#m2001-lemmas); for \(\forall\) and \(\to\) hypotheses, [Section 4.1]](#m2001-forall-implies))

Invokes a specified lemma or hypothesis to modify the goal.

`by_cases` (first use: [Section 5.2]](#m2001-lem))

Case-splits on whether a given statement is true or false.

\* `cancel` (first use: [Section 2.1]](#m2001-tactic-mode))

Cancels a common factor from LHS/RHS of equality/inequality, cancels an exponentiation common to both sides, etc.

`constructor` (first use: [Section 2.4]](#m2001-and); for `↔` goals: [Section 4.2]](#m2001-iff))

Splits an “and” goal (\(\land\)) into sub-goals for its left and right parts.

`contradiction` (first use: [Section 4.4]](#m2001-contradiction-hyp))

If there are two contradictory hypotheses available, this concludes the proof.

`dsimp` (first use: [Section 3.1]](#m2001-parity))

Unfolds a definition. Typically used while working on a proof rather than in the final version; it
is useful for inspecting a goal or hypothesis more carefully, but in most cases the proof will still
work after a `dsimp` line is deleted.

\* `extra` (first use: [Section 1.4]](#m2001-proving-inequalities); for congruences: [Section 3.4]](#m2001-using-modular))

A comparison tactic for inequalities, or other relations such as congruences: checks an inequality
whose two sides differ by the addition of a positive quantity, a congruence mod 3 whose two sides
differ by a multiple of 3, etc.

`interval_cases` (first use: [Section 4.1]](#m2001-forall-implies))

Given a natural-number or integer variable \(n\) for which numeric upper and lower bounds are
available, produce cases for each of the numeric possibilities for \(n\).

`intro` (first use: [Section 4.1]](#m2001-forall-implies); for \(\lnot\) goals: [Section 4.5]](#m2001-contradiction))

Introduces a universally quantified variable (\(\forall\)) or the antecedent of an implication
(\(\to\)) from the goal, or assumes (for the sake of contradiction) the positive version of a
negation (\(\lnot\)) goal.

`left` (first use: [Section 2.3]](#m2001-or))

Selects the left alternative of an “or” goal (\(\lor\)).

`have` (first use: [Section 2.1]](#m2001-tactic-mode); with a lemma: [Section 2.3]](#m2001-or); introducing a new goal: [Section 2.4]](#m2001-and))

Records a fact (followed by the proof of that fact), which then becomes available as an
extra hypothesis.

`mod_cases` (first use: [Section 3.4]](#m2001-using-modular))

Introduces cases for a variable according to its residue modulo a specified number.

\* `numbers` (first use: [Section 1.4]](#m2001-proving-inequalities); with `at` for contradictions: [Section 4.4]](#m2001-contradiction-hyp))

Proves numeric facts, like \(3\cdot 12 < 13 + 25\) or \(3\cdot 5+1=4\cdot 4\).

`obtain` (first use for \(\lor\): [Section 2.3]](#m2001-or); for \(\land\): [Section 2.4]](#m2001-and); for \(\exists\): [Section 2.5]](#m2001-exists))

Takes apart a hypothesis of the form “or” (\(\lor\)), “and” (\(\land\)) or “there exists”
(\(\exists\)).

`push_neg` (first use: [Section 5.3]](#m2001-negation-normalize))

Converts a hypothesis or goal to a logically equivalent form with negations pushed inwards as far
as possible.

`rel` (first use: [Section 1.4]](#m2001-proving-inequalities); for congruences: [Section 3.4]](#m2001-using-modular); for logical equivalences: [Section 5.3]](#m2001-negation-normalize))

A “substitution-like” tactic for inequalities, or other relations such as congruences: looks for
the left-hand side of a specified inequality (or congruence, …) fact in the goal, and replaces it
with the right-hand side of that fact, if that replacement is “obviously” a valid inequality
(or modular arithmetic, …) deduction. Compare with `rw`.

`right` (first use: [Section 2.3]](#m2001-or))

Selects the right alternative of an “or” goal (\(\lor\)).

`ring` (first use: [Section 1.2]](#m2001-proving-equalities-in-lean))

Solves algebraic equality goals such as \((x + y) ^ 2 = x ^ 2 + 2xy + y ^ 2\), when the proof
is effectively “expand out both sides and rearrange”.

`rw` (first use: [Section 1.2]](#m2001-proving-equalities-in-lean); for `↔` hypotheses/lemmas: [Section 4.2]](#m2001-iff))

Substitution: looks for the left-hand side of a specified equality fact in the goal, and replaces it with
the right-hand side of that fact.

With `↔` hypotheses/lemmas, replaces the left-hand side of the specified `↔` fact with
the right-hand side of that fact.

`use` (first use: [Section 2.5]](#m2001-exists))

Provides a witness to an existential goal (\(\exists\)).

---



## Mainstream Lean {#m2001-mainstream-lean}

> 📄 Source: https://hrmacbeth.github.io/math2001/Mainstream_Lean.html

## Transitioning to mainstream Lean

If you have enjoyed this book, you may wish to work further with Lean, for example by working
through the book
[Mathematics in Lean](https://leanprover-community.github.io/mathematics_in_lean/) or by starting
an
[independent formalization project](https://github.com/leanprover-community/mathlib4/wiki/Using-mathlib4-as-a-dependency).

You will discover that the Lean “dialect” used in this book differs from the mainstream mathematical
Lean used in the library [mathlib](https://github.com/leanprover-community/mathlib4) and its
associated literature such as *Mathematics in Lean*. To help you adjust, in this appendix I outline
the major differences.

Some tactics used in this book are deliberately weakened versions of tactics in mathlib: I have
done this to block certain of Lean’s capabilities because prose proofs at the level of this book
would typically write out the details in full. These deliberately weakened tactics include:

Table 1 Tactics in this book, and their mathlib originals





| mathlib tactic | weakened  *Mechanics of*  *Proof* version | differences |
| --- | --- | --- |
| `norm_num` | `numbers` | `norm_num` can do some calculations which in this book we require  the reader to carry out by hand, including reduction mod \(n\),  handling logic, and checking divisibility and primality |
| `gcongr` | `rel` | `gcongr` does not require you to state which hypotheses you are  substituting |
| `linarith` | `addarith` | `linarith` can take constant multiples of linear inequalities as well  as adding/subtracting constants, it can combine many linear  inequalities, and it does not require you to state which hypotheses  you are using |
| `duper` | `exhaust` | `duper` can handle logical tasks involving quantifiers, not just the  quantifier-free ones |

Some tactics used in this book have no mathlib analogues. They are typically wrappers for a small
collection of lemmas, and in mathlib these lemmas would be invoked by name.

Table 2 Tactics in this book with no mathlib analogues




| *Mechanics of Proof* tactic | lemmas wrapped |
| --- | --- |
| `extra` | `Int.modEq_fac_zero`, `le_add_of_nonneg_right`,  `lt_add_of_pos_right`, etc. together with the tactic  `positivity` |
| `cancel` | `mul_left_cancel₀`, `lt_of_pow_lt_pow`,  `pos_of_mul_pos_left`, etc. together with the tactic  `positivity` |
| `simple_induction`  `induction_from_starting_point`  `two_step_induction`  `two_step_induction_from_starting_point` | `Nat.le_induction`, `Nat.twoStepInduction`, etc. together  with the tactics `induction` or `induction'` |

Many of the problems in this book would be solved much more efficiently in mathlib-style
Lean, because there is some sequence of steps which can be carried out as one step by an advanced
algorithm which is beyond the mathematical scope of this book. Some tactics of this kind to be
aware of:

Table 3 Advanced algorithms not used in this book





| algorithm | mathlib tactic | the kinds of steps this tactic can replace |
| --- | --- | --- |
| [Fourier-Motzkin elimination](https://en.wikipedia.org/wiki/Fourier%E2%80%93Motzkin_elimination) | `linarith` | `addarith`, `rel`, `ring`, `numbers` |
| [Gröbner bases](https://en.wikipedia.org/wiki/Gr%C3%B6bner_basis) | `polyrith` | `rw`, `ring` |
| [superposition calculus](https://en.wikipedia.org/wiki/Superposition_calculus) | `duper` | logic tactics at the end of a proof or sub-proof |

Another point not covered in this book is how to interact with the library. Since mathlib contains
over a million lines of Lean code, it is not always easy to find out whether a lemma you want exists
in the library! In this book, I avoid the issue by telling you in advance the name of any lemma I
expect you to use.

A few basic points to be aware of in interacting with the library:

- The [online documentation](https://leanprover-community.github.io/mathlib4_docs/) is generally
  more readable than the source code: it is searchable and it has internal hyperlinks.
- Lemmas are often stated in extreme generality … \((a - b) + c = a - (b - c)\)
  [is stated](https://leanprover-community.github.io/mathlib4_docs/Mathlib/Algebra/Group/Basic.html#sub_sub)
  not for \(\mathbb{R}\) but for a `SubtractionCommMonoid`. You may have better
  luck interacting with the library after first courses on abstract algebra and point-set topology.
- If you can guess the exact statement of a lemma, the tactic `exact?` will find it in the
  library.

All this only scratches the surface: there are many more features of Lean to help you in your
mathematical discoveries. *Mathematics in Lean*, the
[community website](https://leanprover-community.github.io/), and the
[community discussion board](https://leanprover.zulipchat.com/) offer further pointers for
exploration. Have fun!

---



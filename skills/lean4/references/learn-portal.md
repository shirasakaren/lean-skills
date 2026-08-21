# Part II — The Learn Portal (lean-lang.org/learn) {#part-2}



## The Learn Portal (lean-lang.org/learn) {#learn-the-learn-portal-lean-langorglearn}

> 📄 Source: https://lean-lang.org/learn/

## Learn Lean {#learn-learn-lean}

Lean is a functional programming language and theorem prover built for formalizing math and for formal
verification, but is flexible enough for general coding. If you’re a beginner, we recommend the Natural Number Game.
If you feel ready to dive deeper, there are great textbooks, tutorials and interactive games to be found on this page.

[API Reference](https://lean-lang.org/doc/api/)[Install](https://lean-lang.org/install)[Core Docs]](#learn-core-documentation)

### Core documentation {#learn-core-documentation}

[

**Functional Programming in Lean (FPIL)** is the main resource for programmers who want to learn Lean. It assumes a background in programming, but no prior knowledge of functional programming is needed.

](https://lean-lang.org/functional_programming_in_lean/)

[

**Theorem Proving in Lean (TPIL)** is designed to teach you to develop and verify proofs in Lean and covers dependent type theory, automated proof methods, and Lean-specific features for interactive theorem proving.

](https://lean-lang.org/theorem_proving_in_lean4/)

[

**Mathematics in Lean (MIL)** is the main resource for mathematicians who want to learn mathematical formalization through interactive, tactic-based theorem proving using Lean's Mathlib library.

](https://leanprover-community.github.io/mathematics_in_lean/)

[

**The Lean Language Reference** is a comprehensive, precise description of Lean: a reference work in which all aspects of Lean are clearly specified, and demonstrated through succinct examples.

](https://lean-lang.org/doc/reference/latest/)

[

**Lean FAQ** Answers to commonly asked questions about Lean

](https://lean-lang.org/faq)

### Further reading {#learn-further-reading}

[

**Mathlib API Reference** Includes reference information for Lean core, the Lean standard library, Mathlib, and other critical Lean packages.

](https://leanprover-community.github.io/mathlib4_docs/)

[

**The Hitchhiker's Guide to Logical Verification** Originally designed as a companion text for a graduate-level course on interactive theorem proving at Vrije Universiteit Amsterdam.

](https://github.com/lean-forward/logical_verification_2025)

[

**Logic and Proof** A textbook teaching the basics of classical logic, such as propositional logic, first order logic, natural deduction and axiomatic reasoning, using Lean.

](https://leanprover-community.github.io/logic_and_proof/)

[

**The Mechanics of Proof** Originally written as a companion text to the course Math2001 at Fordham University, it teaches the basics of mathematical reasoning to students of mathematics using Lean.

](https://hrmacbeth.github.io/math2001/)

[

**Founder's Blog** Essays about Lean development, proof assistants and AI, written by Chief Architect Leo de Moura.

](https://leodemoura.github.io/blog/)

### Interactive Games and Tutorials {#learn-interactive-games-and-tutorials}

[

**The Natural Number Game** A gamified introduction to mathematical proof that introduces Lean 4 concepts through a purpose-built Lean 4 dialect.

](https://adam.math.hhu.de/#/g/leanprover-community/NNG4)

[

**The Lean Game Server** A collection of games similar to the Natural Number Game.

](https://adam.math.hhu.de/)

### Tools for working with Lean {#learn-tools-for-working-with-lean}

[

**Lean 4 VS Code Extension Manual** Describes how to interact with Lean 4 using the VS Code extension.

](https://github.com/leanprover/vscode-lean4/blob/master/vscode-lean4/manual/manual.md)

[

**Semantic Highlighting** Configuring Lean's semantic highlighing (enabled in VSCode by selecting "Editor > Semantic Highlighting"

](https://lean-lang.org/documentation/semantic-tokens/)

[

**LaTeX** Best practices for highlighting Lean code in LaTeX documents

](https://lean-lang.org/documentation/latex-syntax-highlighting/)

### Resources {#learn-resources}

[

**Loogle!** A Lean and Mathlib search tool for finding definitions and lemmas, available on the web, via CLI or through IDE extensions.

](https://loogle.lean-lang.org/)

[

**Verso** A platform for writing documents, books, course materials, and websites with Lean.

](https://verso.lean-lang.org/)

[

**LeanExplore** A natural language search engine for Lean declarations, indexing commonly used Lean libraries.

](https://www.leanexplore.com/)

[

**LeanSearch** A Mathlib search engine for finding tactics and theorems via natural language queries.

](https://leansearch.net/)

[

**LeanDojo** A tool for data extraction and interacting with Lean programmatically.

](https://leandojo.org/)

[

**REPL** An interactive Read-Eval-Print Loop (REPL) for Lean intended for machine-to-machine interaction and AI applications.

](https://github.com/leanprover-community/repl)

[

**Pantograph** A machine-to-machine interaction system that provides interfaces to execute proofs, construct expressions, and examine the symbol list of a Lean project for machine learning.

](https://github.com/leanprover/Pantograph)

[

**Lean4Web** A web-based version of Lean that allows you to run Lean code directly in your browser.

](https://github.com/leanprover-community/lean4web)

### How to cite Lean {#learn-how-to-cite-lean}

#### Lean 4 {#learn-lean-4}

To cite Lean 4 please reference [The Lean 4 Theorem Prover and Programming Language](https://dl.acm.org/doi/10.1007/978-3-030-79876-5_37) published in CADE-28 - The 28th International Conference on Automated Deduction. [PDF](https://lean-lang.org/papers/lean4.pdf)

##### Bibtex {#learn-bibtex}

```
@inproceedings{10.1007/978-3-030-79876-5_37,
  title = {The Lean 4 Theorem Prover and Programming Language},
  author = {de Moura, Leonardo and Ullrich, Sebastian},
  year = {2021},
  isbn = {978-3-030-79875-8},
  publisher = {Springer-Verlag},
  address = {Berlin, Heidelberg},
  url = {https://doi.org/10.1007/978-3-030-79876-5_37},
  doi = {10.1007/978-3-030-79876-5_37},
  abstract = {Lean 4 is a reimplementation of the Lean interactive theorem prover (ITP) in Lean itself. It addresses many shortcomings of the previous versions and contains many new features. Lean 4 is fully extensible: users can modify and extend the parser, elaborator, tactics, decision procedures, pretty printer, and code generator. The new system has a hygienic macro system custom-built for ITPs. It contains a new typeclass resolution procedure based on tabled resolution, addressing significant performance problems reported by the growing user base. Lean 4 is also an efficient functional programming language based on a novel programming paradigm called functional but in-place. Efficient code generation is crucial for Lean users because many write custom proof automation procedures in Lean itself.},
  booktitle = {Automated Deduction – CADE 28: 28th International Conference on Automated Deduction, Virtual Event, July 12–15, 2021, Proceedings},
  pages = {625–635},
  numpages = {11}
}
```

#### Lean 3 {#learn-lean-3}

To cite Lean 3 please reference [The Lean Theorem Prover (System Description)](https://www.researchgate.net/publication/300636103_The_Lean_Theorem_Prover_System_Description) published in *Lecture Notes in Computer Science*. [PDF](https://lean-lang.org/papers/system.pdf).

##### Bibtex {#learn-bibtex}

```
@inproceedings{inproceedings,
  author = {de Moura, Leonardo and Kong, Soonho and van Doorn, Floris and von Raumer, Jakob},
  year = {2015},
  month = {08},
  pages = {378-388},
  title = {The Lean Theorem Prover (System Description)},
  volume = {9195},
  isbn = {978-3-319-21400-9},
  doi = {10.1007/978-3-319-21401-6_26}
}
```

---



## How to Cite Lean (BibTeX) {#learn-how-to-cite-lean-bibtex}

> 📄 Source: https://lean-lang.org/learn/

## How to Cite Lean

```bibtex
@inproceedings{10.1007/978-3-030-79876-5_37,
  title = {The Lean 4 Theorem Prover and Programming Language},
  author = {de Moura, Leonardo and Ullrich, Sebastian},
  year = {2021},
  isbn = {978-3-030-79875-8},
  publisher = {Springer-Verlag},
  address = {Berlin, Heidelberg},
  url = {https://doi.org/10.1007/978-3-030-79876-5_37},
  doi = {10.1007/978-3-030-79876-5_37},
  abstract = {Lean 4 is a reimplementation of the Lean interactive theorem prover (ITP) in Lean itself. It addresses many shortcomings of the previous versions and contains many new features. Lean 4 is fully extensible: users can modify and extend the parser, elaborator, tactics, decision procedures, pretty printer, and code generator. The new system has a hygienic macro system custom-built for ITPs. It contains a new typeclass resolution procedure based on tabled resolution, addressing significant performance problems reported by the growing user base. Lean 4 is also an efficient functional programming language based on a novel programming paradigm called functional but in-place. Efficient code generation is crucial for Lean users because many write custom proof automation procedures in Lean itself.},
  booktitle = {Automated Deduction – CADE 28: 28th International Conference on Automated Deduction, Virtual Event, July 12–15, 2021, Proceedings},
  pages = {625–635},
  numpages = {11}
}
```

```bibtex
@inproceedings{inproceedings,
  author = {de Moura, Leonardo and Kong, Soonho and van Doorn, Floris and von Raumer, Jakob},
  year = {2015},
  month = {08},
  pages = {378-388},
  title = {The Lean Theorem Prover (System Description)},
  volume = {9195},
  isbn = {978-3-319-21400-9},
  doi = {10.1007/978-3-319-21401-6_26}
}
```

---



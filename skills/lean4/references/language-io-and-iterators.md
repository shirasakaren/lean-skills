## 21. IO {#manual-21-io}

> 📄 Source: https://lean-lang.org/doc/reference/latest/IO/

Lean is a pure functional programming language.
While Lean code is strictly evaluated at run time, the order of evaluation that is used during type checking, especially while checking [definitional equality]](#manual---tech-term-definitional-equality), is formally unspecified and makes use of a number of heuristics that improve performance but are subject to change.
This means that simply adding operations that perform side effects (such as file I/O, exceptions, or mutable references) would lead to programs in which the order of effects is unspecified.
During type checking, even terms with free variables are reduced; this would make side effects even more difficult to predict.
Finally, a basic principle of Lean's logic is that functions are *functions* that map each element of the domain to a unique element of the range.
Including side effects such as console I/O, arbitrary mutable state, or random number generation would violate this principle.

Programs that may have side effects have a type (typically `[IO](https://lean-lang.org/doc/reference/latest/IO/Logical-Model/#IO) α`) that distinguishes them from pure functions.
Logically speaking, `[IO](https://lean-lang.org/doc/reference/latest/IO/Logical-Model/#IO)` describes the sequencing and data dependencies of side effects.
Many of the basic side effects, such as reading from files, are opaque constants from the perspective of Lean's logic.
Others are specified by code that is logically equivalent to the run-time version.
At run time, the compiler produces ordinary code.

1. [21.1. Logical Model](https://lean-lang.org/doc/reference/latest/IO/Logical-Model/#The-Lean-Language-Reference--IO--Logical-Model)
2. [21.2. Control Structures](https://lean-lang.org/doc/reference/latest/IO/Control-Structures/#io-monad-control)
3. [21.3. Console Output](https://lean-lang.org/doc/reference/latest/IO/Console-Output/#The-Lean-Language-Reference--IO--Console-Output)
4. [21.4. Mutable References](https://lean-lang.org/doc/reference/latest/IO/Mutable-References/#The-Lean-Language-Reference--IO--Mutable-References)
5. [21.5. Files, File Handles, and Streams](https://lean-lang.org/doc/reference/latest/IO/Files___-File-Handles___-and-Streams/#The-Lean-Language-Reference--IO--Files___-File-Handles___-and-Streams)
6. [21.6. System and Platform Information](https://lean-lang.org/doc/reference/latest/IO/System-and-Platform-Information/#platform-info)
7. [21.7. Environment Variables](https://lean-lang.org/doc/reference/latest/IO/Environment-Variables/#io-monad-getenv)
8. [21.8. Timing](https://lean-lang.org/doc/reference/latest/IO/Timing/#io-timing)
9. [21.9. Processes](https://lean-lang.org/doc/reference/latest/IO/Processes/#io-processes)
10. [21.10. Random Numbers](https://lean-lang.org/doc/reference/latest/IO/Random-Numbers/#The-Lean-Language-Reference--IO--Random-Numbers)
11. [21.11. Tasks and Threads](https://lean-lang.org/doc/reference/latest/IO/Tasks-and-Threads/#concurrency)

---



## IO — 21.1. Logical Model {#manual-io-211-logical-model}

> 📄 Source: https://lean-lang.org/doc/reference/latest/IO/Logical-Model/

Conceptually, Lean distinguishes evaluation or reduction of terms from *execution* of side effects.
Term reduction is specified by rules such as [β]](#manual---tech-term-___) and [δ]](#manual---tech-term-___-next), which may occur anywhere at any time.
Side effects, which must be executed in the correct order, are abstractly described in Lean's logic.
When programs are run, the Lean runtime system is responsible for actually carrying out the described effects.

The type `[IO]](#manual-IO) α` is a description of a process that, by performing side effects, should either return a value of type `α` or throw an error.
It can be thought of as a [state monad]](#manual---tech-term-State-monads) in which the state is the entire world.
Just as a value of type `[StateM]](#manual-StateM) [Nat]](#manual-Nat___zero) [Bool]](#manual-Bool___false)` computes a `[Bool]](#manual-Bool___false)` while having the ability to mutate a natural number, a value of type `[IO]](#manual-IO) [Bool]](#manual-Bool___false)` computes a `[Bool]](#manual-Bool___false)` while potentially changing the world.
Error handling is accomplished by layering an appropriate exception monad transformer on top of this.

Because the entire world can't be represented in memory, the actual implementation uses an abstract token that stands for its state.
The Lean runtime system is responsible for providing the initial token when the program is run, and each primitive action accepts a token that represents the world and returns another when finished.
This ensures that effects occur in the proper order, and it clearly separates the execution of side effects from the reduction semantics of Lean terms.

Non-termination via general recursion is treated separately from the effects described by `[IO]](#manual-IO)`.
Programs that may not terminate due to infinite loops must be defined as [`partial`]](#manual-partial-unsafe) functions.
From the logical perspective, they are treated as arbitrary constants; `[IO]](#manual-IO)` is not needed.

A very important property of `[IO]](#manual-IO)` is that there is no way for values to “escape”.
Without using one of a few clearly-marked unsafe operators, programs have no way to extract a pure `[Nat]](#manual-Nat___zero)` from an `[IO]](#manual-IO) [Nat]](#manual-Nat___zero)`.
This ensures that the correct ordering of side effects is preserved, and it ensures that programs that have side effects are clearly marked as such.

### 21.1.1. The `IO`, `EIO` and `BaseIO` Monads {#manual-io-monad}

There are two monads that are typically used for programs that interact with the real world:

- Actions in `[IO]](#manual-IO)` may throw exceptions of type `[IO.Error]](#manual-IO___Error___alreadyExists)` or modify the world.
- Actions in `[BaseIO]](#manual-BaseIO)` can't throw exceptions, but they can modify the world.

The distinction makes it possible to tell whether exceptions are possible by looking at an action's type signature.
`[BaseIO]](#manual-BaseIO)` actions are automatically promoted to `[IO]](#manual-IO)` as necessary.

def

```lean
[BaseIO]](#manual-BaseIO) (α : Type) : Type



[BaseIO]](#manual-BaseIO) (α : Type) : Type
```

An `[IO]](#manual-IO)` monad that cannot throw exceptions.

def

```lean
[IO]](#manual-IO) : Type → Type



[IO]](#manual-IO) : Type → Type
```

A monad that supports arbitrary side effects and throwing exceptions of type `[IO.Error]](#manual-IO___Error___alreadyExists)`.

`[IO]](#manual-IO)` is an instance of `[EIO]](#manual-EIO)`, in which the type of errors is a parameter.
In particular, `[IO]](#manual-IO)` is defined as `[EIO]](#manual-EIO) [IO.Error]](#manual-IO___Error___alreadyExists)`.
In some circumstances, such as bindings to non-Lean libraries, it can be convenient to use `[EIO]](#manual-EIO)` with a custom error type, which ensures that errors are handled at the boundaries between these and other `[IO]](#manual-IO)` actions.

def

```lean
[EIO]](#manual-EIO) (ε α : Type) : Type



[EIO]](#manual-EIO) (ε α : Type) : Type
```

A monad that can have side effects on the external world or throw exceptions of type `ε`.

`[BaseIO]](#manual-BaseIO)` is a version of this monad that cannot throw exceptions. `[IO]](#manual-IO)` sets the exception type to
`[IO.Error]](#manual-IO___Error___alreadyExists)`.

def

```lean
[IO.lazyPure]](#manual-IO___lazyPure) {α : Type} (fn : [Unit]](#manual-Unit) → α) : [IO]](#manual-IO) α



[IO.lazyPure]](#manual-IO___lazyPure) {α : Type} (fn : [Unit]](#manual-Unit) → α) :
  [IO]](#manual-IO) α
```

Creates an IO action that will invoke `fn` if and when it is executed, returning the result.

def

```lean
[BaseIO.toIO]](#manual-BaseIO___toIO) {α : Type} (act : [BaseIO]](#manual-BaseIO) α) : [IO]](#manual-IO) α



[BaseIO.toIO]](#manual-BaseIO___toIO) {α : Type} (act : [BaseIO]](#manual-BaseIO) α) :
  [IO]](#manual-IO) α
```

Runs a `[BaseIO]](#manual-BaseIO)` action, which cannot throw an exception, as an `[IO]](#manual-IO)` action.

This function is usually used implicitly via [automatic monadic
lifting](https://lean-lang.org/doc/reference/4.34.0-rc1/find/?domain=Verso.Genre.Manual.section&name=lifting-monads) rather than being called explicitly.

def

```lean
[BaseIO.toEIO]](#manual-BaseIO___toEIO) {α ε : Type} (act : [BaseIO]](#manual-BaseIO) α) : [EIO]](#manual-EIO) ε α



[BaseIO.toEIO]](#manual-BaseIO___toEIO) {α ε : Type}
  (act : [BaseIO]](#manual-BaseIO) α) : [EIO]](#manual-EIO) ε α
```

Runs a `[BaseIO]](#manual-BaseIO)` action, which cannot throw an exception, in any other `[EIO]](#manual-EIO)` monad.

This function is usually used implicitly via [automatic monadic
lifting](https://lean-lang.org/doc/reference/4.34.0-rc1/find/?domain=Verso.Genre.Manual.section&name=lifting-monads) rather being than called explicitly.

def

```lean
[EIO.toBaseIO]](#manual-EIO___toBaseIO) {ε α : Type} (act : [EIO]](#manual-EIO) ε α) : [BaseIO]](#manual-BaseIO) ([Except]](#manual-Except___error) ε α)



[EIO.toBaseIO]](#manual-EIO___toBaseIO) {ε α : Type}
  (act : [EIO]](#manual-EIO) ε α) : [BaseIO]](#manual-BaseIO) ([Except]](#manual-Except___error) ε α)
```

Converts an `[EIO]](#manual-EIO) ε` action that might throw an exception of type `ε` into an exception-free `[BaseIO]](#manual-BaseIO)`
action that returns an `[Except]](#manual-Except___error)` value.

def

```lean
[EIO.toIO]](#manual-EIO___toIO) {ε α : Type} (f : ε → [IO.Error]](#manual-IO___Error___alreadyExists)) (act : [EIO]](#manual-EIO) ε α) : [IO]](#manual-IO) α



[EIO.toIO]](#manual-EIO___toIO) {ε α : Type} (f : ε → [IO.Error]](#manual-IO___Error___alreadyExists))
  (act : [EIO]](#manual-EIO) ε α) : [IO]](#manual-IO) α
```

Converts an `[EIO]](#manual-EIO) ε` action into an `[IO]](#manual-IO)` action by translating any exceptions that it throws into
`[IO.Error]](#manual-IO___Error___alreadyExists)`s using `f`.

def

```lean
[EIO.toIO']](#manual-EIO___toIO___) {ε α : Type} (act : [EIO]](#manual-EIO) ε α) : [IO]](#manual-IO) ([Except]](#manual-Except___error) ε α)



[EIO.toIO']](#manual-EIO___toIO___) {ε α : Type} (act : [EIO]](#manual-EIO) ε α) :
  [IO]](#manual-IO) ([Except]](#manual-Except___error) ε α)
```

Converts an `[EIO]](#manual-EIO) ε` action that might throw an exception of type `ε` into an exception-free `[IO]](#manual-IO)`
action that returns an `[Except]](#manual-Except___error)` value.

def

```lean
[IO.toEIO]](#manual-IO___toEIO) {ε α : Type} (f : [IO.Error]](#manual-IO___Error___alreadyExists) → ε) (act : [IO]](#manual-IO) α) : [EIO]](#manual-EIO) ε α



[IO.toEIO]](#manual-IO___toEIO) {ε α : Type} (f : [IO.Error]](#manual-IO___Error___alreadyExists) → ε)
  (act : [IO]](#manual-IO) α) : [EIO]](#manual-EIO) ε α
```

Runs an `[IO]](#manual-IO)` action in some other `[EIO]](#manual-EIO)` monad, using `f` to translate `[IO]](#manual-IO)` exceptions.

### 21.1.2. Errors and Error Handling in `IO` {#manual-io-monad-errors}

Error handling in the `[IO]](#manual-IO)` monad uses the same facilities as any other [exception monad]](#manual---tech-term-Exception-monads).
In particular, throwing and catching exceptions uses the methods of the `[MonadExceptOf]](#manual-MonadExceptOf___mk)` [type class]](#manual---tech-term-type-class).
The exceptions thrown in `[IO]](#manual-IO)` have the type `[IO.Error]](#manual-IO___Error___alreadyExists)`.
The constructors of this type represent the low-level errors that occur on most operating systems, such as files not existing.
The most-used constructor is `[userError]](#manual-IO___Error___alreadyExists)`, which covers all other cases and includes a string that describes the problem.

inductive type

```lean
[IO.Error]](#manual-IO___Error___alreadyExists) : Type



[IO.Error]](#manual-IO___Error___alreadyExists) : Type
```

Exceptions that may be thrown in the `[IO]](#manual-IO)` monad.

Many of the constructors of `[IO.Error]](#manual-IO___Error___alreadyExists)` correspond to POSIX error numbers. In these cases, the
documentation string lists POSIX standard error macros that correspond to the error. This list is
not necessarily exhaustive, and these constructor includes a field for the underlying error number.

Constructors

```lean
[IO.Error.alreadyExists]](#manual-IO___Error___alreadyExists) (filename : [Option]](#manual-Option___none) [String]](#manual-String___ofByteArray))
  (osCode : [UInt32]](#manual-UInt32___ofBitVec)) (details : [String]](#manual-String___ofByteArray)) : [IO.Error]](#manual-IO___Error___alreadyExists)
```

The operation failed because a file already exists.

This corresponds to POSIX errors `EEXIST`, `EINPROGRESS`, and `EISCONN`.

```lean
[IO.Error.otherError]](#manual-IO___Error___alreadyExists) (osCode : [UInt32]](#manual-UInt32___ofBitVec)) (details : [String]](#manual-String___ofByteArray)) :
  [IO.Error]](#manual-IO___Error___alreadyExists)
```

Some error not covered by the other constructors of `[IO.Error]](#manual-IO___Error___alreadyExists)` occurred.

This also includes POSIX error `EFAULT`.

```lean
[IO.Error.resourceBusy]](#manual-IO___Error___alreadyExists) (osCode : [UInt32]](#manual-UInt32___ofBitVec)) (details : [String]](#manual-String___ofByteArray)) :
  [IO.Error]](#manual-IO___Error___alreadyExists)
```

A necessary resource was busy.

This corresponds to POSIX errors `EADDRINUSE`, `EBUSY`, `EDEADLK`, and `ETXTBSY`.

```lean
[IO.Error.resourceVanished]](#manual-IO___Error___alreadyExists) (osCode : [UInt32]](#manual-UInt32___ofBitVec))
  (details : [String]](#manual-String___ofByteArray)) : [IO.Error]](#manual-IO___Error___alreadyExists)
```

A necessary resource is no longer available.

This corresponds to POSIX errors `ECONNRESET`, `EIDRM`, `ENETDOWN`, `ENETRESET`, `ENOLINK`, and
`EPIPE`.

```lean
[IO.Error.unsupportedOperation]](#manual-IO___Error___alreadyExists) (osCode : [UInt32]](#manual-UInt32___ofBitVec))
  (details : [String]](#manual-String___ofByteArray)) : [IO.Error]](#manual-IO___Error___alreadyExists)
```

An operation was not supported.

This corresponds to POSIX errors `EADDRNOTAVAIL`, `EAFNOSUPPORT`, `ENODEV`, `ENOPROTOOPT`
`ENOSYS`, `EOPNOTSUPP`, `ERANGE`, `ESPIPE`, and `EXDEV`.

```lean
[IO.Error.hardwareFault]](#manual-IO___Error___alreadyExists) (osCode : [UInt32]](#manual-UInt32___ofBitVec))
  (details : [String]](#manual-String___ofByteArray)) : [IO.Error]](#manual-IO___Error___alreadyExists)
```

The operation failed due to a hardware problem, such as an I/O error.

This corresponds to the POSIX error `[EIO]](#manual-EIO)`.

```lean
[IO.Error.unsatisfiedConstraints]](#manual-IO___Error___alreadyExists) (osCode : [UInt32]](#manual-UInt32___ofBitVec))
  (details : [String]](#manual-String___ofByteArray)) : [IO.Error]](#manual-IO___Error___alreadyExists)
```

A constraint required by an operation was not satisfied (e.g. a directory was not empty).

This corresponds to the POSIX error `ENOTEMPTY`.

```lean
[IO.Error.illegalOperation]](#manual-IO___Error___alreadyExists) (osCode : [UInt32]](#manual-UInt32___ofBitVec))
  (details : [String]](#manual-String___ofByteArray)) : [IO.Error]](#manual-IO___Error___alreadyExists)
```

An inappropriate I/O control operation was attempted.

This corresponds to the POSIX error `ENOTTY`.

```lean
[IO.Error.protocolError]](#manual-IO___Error___alreadyExists) (osCode : [UInt32]](#manual-UInt32___ofBitVec))
  (details : [String]](#manual-String___ofByteArray)) : [IO.Error]](#manual-IO___Error___alreadyExists)
```

A protocol error occurred.

This corresponds to the POSIX errors `EPROTO`, `EPROTONOSUPPORT`, and `EPROTOTYPE`.

```lean
[IO.Error.timeExpired]](#manual-IO___Error___alreadyExists) (osCode : [UInt32]](#manual-UInt32___ofBitVec)) (details : [String]](#manual-String___ofByteArray)) :
  [IO.Error]](#manual-IO___Error___alreadyExists)
```

An operation timed out.

This corresponds to the POSIX errors `ETIME`, and `ETIMEDOUT`.

```lean
[IO.Error.interrupted]](#manual-IO___Error___alreadyExists) (filename : [String]](#manual-String___ofByteArray)) (osCode : [UInt32]](#manual-UInt32___ofBitVec))
  (details : [String]](#manual-String___ofByteArray)) : [IO.Error]](#manual-IO___Error___alreadyExists)
```

The operation was interrupted.

This corresponds to the POSIX error `EINTR`.

```lean
[IO.Error.noFileOrDirectory]](#manual-IO___Error___alreadyExists) (filename : [String]](#manual-String___ofByteArray))
  (osCode : [UInt32]](#manual-UInt32___ofBitVec)) (details : [String]](#manual-String___ofByteArray)) : [IO.Error]](#manual-IO___Error___alreadyExists)
```

No such file or directory.

This corresponds to the POSIX error `ENOENT`.

```lean
[IO.Error.invalidArgument]](#manual-IO___Error___alreadyExists) (filename : [Option]](#manual-Option___none) [String]](#manual-String___ofByteArray))
  (osCode : [UInt32]](#manual-UInt32___ofBitVec)) (details : [String]](#manual-String___ofByteArray)) : [IO.Error]](#manual-IO___Error___alreadyExists)
```

An argument to an I/O operation was invalid.

This corresponds to the POSIX errors `ELOOP`, `ENAMETOOLONG`, `EDESTADDRREQ`, `EILSEQ`, `EINVAL`, `EDOM`, `EBADF`
`ENOEXEC`, `ENOSTR`, `ENOTCONN`, and `ENOTSOCK`.

```lean
[IO.Error.permissionDenied]](#manual-IO___Error___alreadyExists) (filename : [Option]](#manual-Option___none) [String]](#manual-String___ofByteArray))
  (osCode : [UInt32]](#manual-UInt32___ofBitVec)) (details : [String]](#manual-String___ofByteArray)) : [IO.Error]](#manual-IO___Error___alreadyExists)
```

An operation failed due to insufficient permissions.

This corresponds to the POSIX errors `EACCES`, `EROFS`, `ECONNABORTED`, `EFBIG`, and `EPERM`.

```lean
[IO.Error.resourceExhausted]](#manual-IO___Error___alreadyExists) (filename : [Option]](#manual-Option___none) [String]](#manual-String___ofByteArray))
  (osCode : [UInt32]](#manual-UInt32___ofBitVec)) (details : [String]](#manual-String___ofByteArray)) : [IO.Error]](#manual-IO___Error___alreadyExists)
```

A resource was exhausted.

This corresponds to the POSIX errors `EMFILE`, `ENFILE`, `ENOSPC`, `E2BIG`, `EAGAIN`, `EMLINK`,
`EMSGSIZE`, `ENOBUFS`, `ENOLCK`, `ENOMEM`, and `ENOSR`.

```lean
[IO.Error.inappropriateType]](#manual-IO___Error___alreadyExists) (filename : [Option]](#manual-Option___none) [String]](#manual-String___ofByteArray))
  (osCode : [UInt32]](#manual-UInt32___ofBitVec)) (details : [String]](#manual-String___ofByteArray)) : [IO.Error]](#manual-IO___Error___alreadyExists)
```

An argument was the wrong type (e.g. a directory when a file was required).

This corresponds to the POSIX errors `EISDIR`, `EBADMSG`, and `ENOTDIR`.

```lean
[IO.Error.noSuchThing]](#manual-IO___Error___alreadyExists) (filename : [Option]](#manual-Option___none) [String]](#manual-String___ofByteArray))
  (osCode : [UInt32]](#manual-UInt32___ofBitVec)) (details : [String]](#manual-String___ofByteArray)) : [IO.Error]](#manual-IO___Error___alreadyExists)
```

A required resource does not exist.

This corresponds to the POSIX errors `ENXIO`, `EHOSTUNREACH`, `ENETUNREACH`, `ECHILD`,
`ECONNREFUSED`, `ENODATA`, `ENOMSG`, and `ESRCH`.

```lean
[IO.Error.unexpectedEof]](#manual-IO___Error___alreadyExists) : [IO.Error]](#manual-IO___Error___alreadyExists)
```

An unexpected end-of-file marker was encountered.

```lean
[IO.Error.userError]](#manual-IO___Error___alreadyExists) (msg : [String]](#manual-String___ofByteArray)) : [IO.Error]](#manual-IO___Error___alreadyExists)
```

Some other error occurred.

def

```lean
[IO.Error.toString]](#manual-IO___Error___toString) : [IO.Error]](#manual-IO___Error___alreadyExists) → [String]](#manual-String___ofByteArray)



[IO.Error.toString]](#manual-IO___Error___toString) : [IO.Error]](#manual-IO___Error___alreadyExists) → [String]](#manual-String___ofByteArray)
```

Converts an `[IO.Error]](#manual-IO___Error___alreadyExists)` to a descriptive string.

`[IO.Error.userError]](#manual-IO___Error___alreadyExists)` is converted to its embedded message. The other constructors are converted in a
way that preserves structured information, such as error codes and filenames, that can help
diagnose the issue.

def

```lean
[IO.ofExcept.{u_1}]](#manual-IO___ofExcept) {ε : Type u_1} {α : Type} [ToString ε]
  (e : [Except]](#manual-Except___error) ε α) : [IO]](#manual-IO) α



[IO.ofExcept.{u_1}]](#manual-IO___ofExcept) {ε : Type u_1}
  {α : Type} [ToString ε]
  (e : [Except]](#manual-Except___error) ε α) : [IO]](#manual-IO) α
```

Converts an `[Except]](#manual-Except___error) ε` action into an `[IO]](#manual-IO)` action.

If the `[Except]](#manual-Except___error) ε` action throws an exception, then the exception type's `ToString` instance is used
to convert it into an `[IO.Error]](#manual-IO___Error___alreadyExists)`, which is thrown. Otherwise, the value is returned.

def

```lean
[EIO.catchExceptions]](#manual-EIO___catchExceptions) {ε α : Type} (act : [EIO]](#manual-EIO) ε α) (h : ε → [BaseIO]](#manual-BaseIO) α) :
  [BaseIO]](#manual-BaseIO) α



[EIO.catchExceptions]](#manual-EIO___catchExceptions) {ε α : Type}
  (act : [EIO]](#manual-EIO) ε α) (h : ε → [BaseIO]](#manual-BaseIO) α) :
  [BaseIO]](#manual-BaseIO) α
```

Handles any exception that might be thrown by an `[EIO]](#manual-EIO) ε` action, transforming it into an
exception-free `[BaseIO]](#manual-BaseIO)` action.

def

```lean
[IO.userError]](#manual-IO___userError) (s : [String]](#manual-String___ofByteArray)) : [IO.Error]](#manual-IO___Error___alreadyExists)



[IO.userError]](#manual-IO___userError) (s : [String]](#manual-String___ofByteArray)) : [IO.Error]](#manual-IO___Error___alreadyExists)
```

Constructs an `[IO.Error]](#manual-IO___Error___alreadyExists)` from a string.

`[IO.Error]](#manual-IO___Error___alreadyExists)` is the type of exceptions thrown by the `[IO]](#manual-IO)` monad.

**Example: Throwing and Catching Errors**

This program repeatedly demands a password, using exceptions for control flow.
The syntax used for exceptions is available in all exception monads, not just `[IO]](#manual-IO)`.
When an incorrect password is provided, an exception is thrown, which is caught by the loop that repeats the password check.
A correct password allows control to proceed past the check, terminating the loop, and any other exceptions are re-thrown.

```lean
def accessControl : [IO]](#manual-IO) [Unit]](#manual-Unit) := [do]](#manual-Lean___Parser___Term___do)
[IO.println](https://lean-lang.org/doc/reference/latest/IO/Console-Output/#IO___println) "What is the password?"
let password ← (← [IO.getStdin](https://lean-lang.org/doc/reference/latest/IO/Files___-File-Handles___-and-Streams/#IO___getStdin)).[getLine](https://lean-lang.org/doc/reference/latest/IO/Files___-File-Handles___-and-Streams/#IO___FS___Stream___mk)
if password.[trimAscii]](#manual-String___trimAscii).[copy]](#manual-String___Slice___copy) != "secret" then
[throw]](#manual-MonadExcept___mk) ([.userError]](#manual-IO___Error___alreadyExists) "Incorrect password")
else return
def repeatAccessControl : [IO]](#manual-IO) [Unit]](#manual-Unit) := [do]](#manual-Lean___Parser___Term___do)
repeat
try
[accessControl]](#manual-accessControl-_LPAR_in-Throwing-and-Catching-Errors_RPAR_)
break
catch
| [.userError]](#manual-IO___Error___alreadyExists) "Incorrect password" =>
continue
| other =>
[throw]](#manual-MonadExcept___mk) other
def main : [IO]](#manual-IO) [Unit]](#manual-Unit) := [do]](#manual-Lean___Parser___Term___do)
[repeatAccessControl]](#manual-repeatAccessControl-_LPAR_in-Throwing-and-Catching-Errors_RPAR_)
[IO.println](https://lean-lang.org/doc/reference/latest/IO/Console-Output/#IO___println) "Access granted!"
```

When run with this input:

`stdin``publicinfo``secondtry``secret`

the program emits:

`stdout``What is the password?``What is the password?``What is the password?``Access granted!`

---



## IO — 21.2. Control Structures {#manual-io-212-control-structures}

> 📄 Source: https://lean-lang.org/doc/reference/latest/IO/Control-Structures/

Normally, programs written in `[IO]](#manual-IO)` use [the same control structures as those written in other monads]](#manual-monads-and-do).
There is one specific `[IO]](#manual-IO)` helper.

opaque

```lean
[IO.iterate]](#manual-IO___iterate) {α β : Type} (a : α) (f : α → [IO]](#manual-IO) [(]](#manual-Sum___inl)α [⊕]](#manual-Sum___inl) β[)]](#manual-Sum___inl)) : [IO]](#manual-IO) β



[IO.iterate]](#manual-IO___iterate) {α β : Type} (a : α)
  (f : α → [IO]](#manual-IO) [(]](#manual-Sum___inl)α [⊕]](#manual-Sum___inl) β[)]](#manual-Sum___inl)) : [IO]](#manual-IO) β
```

Iterates an `[IO]](#manual-IO)` action. Starting with an initial state, the action is applied repeatedly until it
returns a final value in `[Sum.inr]](#manual-Sum___inl)`. Each time it returns `[Sum.inl]](#manual-Sum___inl)`, the returned value is treated as
a new state.

---



## IO — 21.3. Console Output {#manual-io-213-console-output}

> 📄 Source: https://lean-lang.org/doc/reference/latest/IO/Console-Output/

Lean includes convenience functions for writing to [standard output](https://lean-lang.org/doc/reference/latest/IO/Files___-File-Handles___-and-Streams/#--tech-term-standard-output) and [standard error](https://lean-lang.org/doc/reference/latest/IO/Files___-File-Handles___-and-Streams/#--tech-term-standard-error).
All make use of `ToString` instances, and the varieties whose names end in `-ln` add a newline after the output.
These convenience functions only expose a part of the functionality available [using the standard I/O streams](https://lean-lang.org/doc/reference/latest/IO/Files___-File-Handles___-and-Streams/#stdio).
In particular, to read a line from standard input, use a combination of `[IO.getStdin](https://lean-lang.org/doc/reference/latest/IO/Files___-File-Handles___-and-Streams/#IO___getStdin)` and `[IO.FS.Stream.getLine](https://lean-lang.org/doc/reference/latest/IO/Files___-File-Handles___-and-Streams/#IO___FS___Stream___mk)`.

def

```lean
[IO.print.{u_1}]](#manual-IO___print) {α : Type u_1} [ToString α] (s : α) : [IO]](#manual-IO) [Unit]](#manual-Unit)



[IO.print.{u_1}]](#manual-IO___print) {α : Type u_1} [ToString α]
  (s : α) : [IO]](#manual-IO) [Unit]](#manual-Unit)
```

Converts `s` to a string using its `ToString α` instance, and prints it to the current standard
output (as determined by `[IO.getStdout](https://lean-lang.org/doc/reference/latest/IO/Files___-File-Handles___-and-Streams/#IO___getStdout)`).

def

```lean
[IO.println.{u_1}]](#manual-IO___println) {α : Type u_1} [ToString α] (s : α) : [IO]](#manual-IO) [Unit]](#manual-Unit)



[IO.println.{u_1}]](#manual-IO___println) {α : Type u_1}
  [ToString α] (s : α) : [IO]](#manual-IO) [Unit]](#manual-Unit)
```

Converts `s` to a string using its `ToString α` instance, and prints it with a trailing newline to
the current standard output (as determined by `[IO.getStdout](https://lean-lang.org/doc/reference/latest/IO/Files___-File-Handles___-and-Streams/#IO___getStdout)`).

def

```lean
[IO.eprint.{u_1}]](#manual-IO___eprint) {α : Type u_1} [ToString α] (s : α) : [IO]](#manual-IO) [Unit]](#manual-Unit)



[IO.eprint.{u_1}]](#manual-IO___eprint) {α : Type u_1}
  [ToString α] (s : α) : [IO]](#manual-IO) [Unit]](#manual-Unit)
```

Converts `s` to a string using its `ToString α` instance, and prints it to the current standard
error (as determined by `[IO.getStderr](https://lean-lang.org/doc/reference/latest/IO/Files___-File-Handles___-and-Streams/#IO___getStderr)`).

def

```lean
[IO.eprintln.{u_1}]](#manual-IO___eprintln) {α : Type u_1} [ToString α] (s : α) : [IO]](#manual-IO) [Unit]](#manual-Unit)



[IO.eprintln.{u_1}]](#manual-IO___eprintln) {α : Type u_1}
  [ToString α] (s : α) : [IO]](#manual-IO) [Unit]](#manual-Unit)
```

Converts `s` to a string using its `ToString α` instance, and prints it with a trailing newline to
the current standard error (as determined by `[IO.getStderr](https://lean-lang.org/doc/reference/latest/IO/Files___-File-Handles___-and-Streams/#IO___getStderr)`).

**Example: Printing**

This program demonstrates all four convenience functions for console I/O.

```lean
def main : [IO]](#manual-IO) [Unit]](#manual-Unit) := [do]](#manual-Lean___Parser___Term___do)
[IO.print]](#manual-IO___print) "This is the "
[IO.print]](#manual-IO___print) "Lean"
[IO.println]](#manual-IO___println) " language reference."
[IO.println]](#manual-IO___println) "Thank you for reading it!"
[IO.eprint]](#manual-IO___eprint) "Please report any "
[IO.eprint]](#manual-IO___eprint) "errors"
[IO.eprintln]](#manual-IO___eprintln) " so they can be corrected."
```

It outputs the following to the standard output:

`stdout``This is the Lean language reference.``Thank you for reading it!`

and the following to the standard error:

`stderr``Please report any errors so they can be corrected.`

---



## IO — 21.4. Mutable References {#manual-io-214-mutable-references}

> 📄 Source: https://lean-lang.org/doc/reference/latest/IO/Mutable-References/

While ordinary [state monads]](#manual---tech-term-State-monads) encode stateful computations with tuples that track the contents of the state along with the computation's value, Lean's runtime system also provides mutable references that are always backed by mutable memory cells.
Mutable references have a type `[IO.Ref]](#manual-IO___Ref)` that indicates that a cell is mutable, and reads and writes must be explicit.
`[IO.Ref]](#manual-IO___Ref)` is implemented using `[ST.Ref]](#manual-ST___Ref___mk)`, so the entire [`[ST.Ref]](#manual-ST___Ref___mk)` API](https://lean-lang.org/doc/reference/latest/IO/Mutable-References/#mutable-st-references) may also be used with `[IO.Ref]](#manual-IO___Ref)`.

def

```lean
[IO.Ref]](#manual-IO___Ref) (α : Type) : Type



[IO.Ref]](#manual-IO___Ref) (α : Type) : Type
```

Mutable reference cells that contain values of type `α`. These cells can read from and mutated in
the `[IO]](#manual-IO)` monad.

def

```lean
[IO.mkRef]](#manual-IO___mkRef) {α : Type} (a : α) : [BaseIO]](#manual-BaseIO) ([IO.Ref]](#manual-IO___Ref) α)



[IO.mkRef]](#manual-IO___mkRef) {α : Type} (a : α) :
  [BaseIO]](#manual-BaseIO) ([IO.Ref]](#manual-IO___Ref) α)
```

Creates a new mutable reference cell that contains `a`.

### 21.4.1. State Transformers {#manual-mutable-st-references}

Mutable references are often useful in contexts where arbitrary side effects are undesired.
They can give a significant speedup when Lean is unable to optimize pure operations into mutation, and some algorithms are more easily expressed using mutable references than with state monads.
Additionally, it has a property that other side effects do not have: if all of the mutable references used by a piece of code are created during its execution, and no mutable references from the code escape to other code, then the result of evaluation is deterministic.

The `[ST]](#manual-ST)` monad is a restricted version of `[IO]](#manual-IO)` in which mutable state is the only side effect, and mutable references cannot escape.`[ST]](#manual-ST)` was first described by John Launchbury and Simon L Peyton Jones, 1994. “Lazy functional state threads”. In *Proceedings of the ACM SIGPLAN 1994 Conference on Programming Language Design and Implementation.*.
`[ST]](#manual-ST)` takes a type parameter that is never used to classify any terms.
The `[runST]](#manual-runST)` function, which allow escape from `[ST]](#manual-ST)`, requires that the `[ST]](#manual-ST)` action that is passed to it can instantiate this type parameter with *any* type.
This unknown type does not exist except as a parameter to a function, which means that values whose types are “marked” by it cannot escape its scope.

def

```lean
[ST]](#manual-ST) (σ α : Type) : Type



[ST]](#manual-ST) (σ α : Type) : Type
```

A restricted version of `[IO]](#manual-IO)` in which mutable state is the only side effect.

It is possible to run `[ST]](#manual-ST)` computations in a non-monadic context using `[runST]](#manual-runST)`.

def

```lean
[runST]](#manual-runST) {α : Type} (x : (σ : Type) → [ST]](#manual-ST) σ α) : α



[runST]](#manual-runST) {α : Type}
  (x : (σ : Type) → [ST]](#manual-ST) σ α) : α
```

Runs an `[ST]](#manual-ST)` computation, in which mutable state via `[ST.Ref]](#manual-ST___Ref___mk)` is the only side effect.

As with `[IO]](#manual-IO)` and `[EIO]](#manual-EIO)`, there is also a variation of `[ST]](#manual-ST)` that takes a custom error type as a parameter.
Here, `[ST]](#manual-ST)` is analogous to `[BaseIO]](#manual-BaseIO)` rather than `[IO]](#manual-IO)`, because `[ST]](#manual-ST)` cannot result in errors being thrown.

def

```lean
[EST]](#manual-EST) (ε σ α : Type) : Type



[EST]](#manual-EST) (ε σ α : Type) : Type
```

A restricted version of `[IO]](#manual-IO)` in which mutable state and exceptions are the only side effects.

It is possible to run `[EST]](#manual-EST)` computations in a non-monadic context using `[runEST]](#manual-runEST)`.

def

```lean
[runEST]](#manual-runEST) {ε α : Type} (x : (σ : Type) → [EST]](#manual-EST) ε σ α) : [Except]](#manual-Except___error) ε α



[runEST]](#manual-runEST) {ε α : Type}
  (x : (σ : Type) → [EST]](#manual-EST) ε σ α) :
  [Except]](#manual-Except___error) ε α
```

Runs an `[EST]](#manual-EST)` computation, in which mutable state and exceptions are the only side effects.

structure

```lean
[ST.Ref]](#manual-ST___Ref___mk) (σ α : Type) : Type



[ST.Ref]](#manual-ST___Ref___mk) (σ α : Type) : Type
```

Mutable reference cells that contain values of type `α`. These cells can read from and mutated in
the `[ST]](#manual-ST) σ` monad.

Constructor

```lean
[ST.Ref.mk]](#manual-ST___Ref___mk)
```

def

```lean
[ST.mkRef]](#manual-ST___mkRef) {σ : Type} {m : Type → Type} [[MonadLiftT]](#manual-MonadLiftT___mk) ([ST]](#manual-ST) σ) m] {α : Type}
  (a : α) : m ([ST.Ref]](#manual-ST___Ref___mk) σ α)



[ST.mkRef]](#manual-ST___mkRef) {σ : Type} {m : Type → Type}
  [[MonadLiftT]](#manual-MonadLiftT___mk) ([ST]](#manual-ST) σ) m] {α : Type}
  (a : α) : m ([ST.Ref]](#manual-ST___Ref___mk) σ α)
```

Creates a new mutable reference that contains the provided value `a`.

#### 21.4.1.1. Reading and Writing {#manual-The-Lean-Language-Reference--IO--Mutable-References--State-Transformers--Reading-and-Writing}

def

```lean
[ST.Ref.get]](#manual-ST___Ref___get) {σ : Type} {m : Type → Type} [[MonadLiftT]](#manual-MonadLiftT___mk) ([ST]](#manual-ST) σ) m] {α : Type}
  (r : [ST.Ref]](#manual-ST___Ref___mk) σ α) : m α



[ST.Ref.get]](#manual-ST___Ref___get) {σ : Type} {m : Type → Type}
  [[MonadLiftT]](#manual-MonadLiftT___mk) ([ST]](#manual-ST) σ) m] {α : Type}
  (r : [ST.Ref]](#manual-ST___Ref___mk) σ α) : m α
```

Reads the value of a mutable reference.

def

```lean
[ST.Ref.set]](#manual-ST___Ref___set) {σ : Type} {m : Type → Type} [[MonadLiftT]](#manual-MonadLiftT___mk) ([ST]](#manual-ST) σ) m] {α : Type}
  (r : [ST.Ref]](#manual-ST___Ref___mk) σ α) (a : α) : m [Unit]](#manual-Unit)



[ST.Ref.set]](#manual-ST___Ref___set) {σ : Type} {m : Type → Type}
  [[MonadLiftT]](#manual-MonadLiftT___mk) ([ST]](#manual-ST) σ) m] {α : Type}
  (r : [ST.Ref]](#manual-ST___Ref___mk) σ α) (a : α) : m [Unit]](#manual-Unit)
```

Replaces the value of a mutable reference.

**Example: Data races with get and set**

```lean
def main : [IO]](#manual-IO) [Unit]](#manual-Unit) := [do]](#manual-Lean___Parser___Term___do)
let balance ← [IO.mkRef]](#manual-IO___mkRef) (100 : [Int]](#manual-Int___ofNat))
let mut orders := #[]
[IO.println]](#manual-IO___println) "Sending out orders..."
for _ in [0:100] do
let o ← [IO.asTask](https://lean-lang.org/doc/reference/latest/IO/Tasks-and-Threads/#IO___asTask) (prio := [.dedicated](https://lean-lang.org/doc/reference/latest/IO/Tasks-and-Threads/#Task___Priority___dedicated)) [do]](#manual-Lean___Parser___Term___do)
let cost ← [IO.rand](https://lean-lang.org/doc/reference/latest/IO/Random-Numbers/#IO___rand) 1 100
[IO.sleep](https://lean-lang.org/doc/reference/latest/IO/Timing/#IO___sleep) (← [IO.rand](https://lean-lang.org/doc/reference/latest/IO/Random-Numbers/#IO___rand) 10 100).[toUInt32]](#manual-Nat___toUInt32)
if cost < (← balance.[get]](#manual-ST___Ref___get)) then
[IO.sleep](https://lean-lang.org/doc/reference/latest/IO/Timing/#IO___sleep) (← [IO.rand](https://lean-lang.org/doc/reference/latest/IO/Random-Numbers/#IO___rand) 10 100).[toUInt32]](#manual-Nat___toUInt32)
balance.[set]](#manual-ST___Ref___set) ((← balance.[get]](#manual-ST___Ref___get)) - cost)
orders := orders.[push]](#manual-Array___push) o
-- Wait until all orders are completed
for o in orders do
match o.[get](https://lean-lang.org/doc/reference/latest/IO/Tasks-and-Threads/#Task___get) with
| [.ok]](#manual-Except___error) () => [pure]](#manual-Pure___mk) ()
| [.error]](#manual-Except___error) e => [throw]](#manual-MonadExcept___mk) e
if (← balance.[get]](#manual-ST___Ref___get)) < 0 then
[IO.eprintln]](#manual-IO___eprintln) "Final balance is negative!"
else
[IO.println]](#manual-IO___println) "Final balance is zero or positive."
```

`stdout``Sending out orders...`

`stderr``Final balance is negative!`

def

```lean
[ST.Ref.modify]](#manual-ST___Ref___modify) {σ : Type} {m : Type → Type} [[MonadLiftT]](#manual-MonadLiftT___mk) ([ST]](#manual-ST) σ) m]
  {α : Type} (r : [ST.Ref]](#manual-ST___Ref___mk) σ α) (f : α → α) : m [Unit]](#manual-Unit)



[ST.Ref.modify]](#manual-ST___Ref___modify) {σ : Type} {m : Type → Type}
  [[MonadLiftT]](#manual-MonadLiftT___mk) ([ST]](#manual-ST) σ) m] {α : Type}
  (r : [ST.Ref]](#manual-ST___Ref___mk) σ α) (f : α → α) : m [Unit]](#manual-Unit)
```

Atomically modifies a mutable reference cell by replacing its contents with the result of a function
call.

**Example: Avoiding data races with modify**

This program launches 100 threads.
Each thread simulates a purchase attempt: it generates a random price, and if the account balance is sufficient, it decrements it by the price.
The balance check and the computation of the new value occur in an atomic call to `[ST.Ref.modify]](#manual-ST___Ref___modify)`.

```lean
def main : [IO]](#manual-IO) [Unit]](#manual-Unit) := [do]](#manual-Lean___Parser___Term___do)
let balance ← [IO.mkRef]](#manual-IO___mkRef) (100 : [Int]](#manual-Int___ofNat))
let mut orders := #[]
[IO.println]](#manual-IO___println) "Sending out orders..."
for _ in [0:100] do
let o ← [IO.asTask](https://lean-lang.org/doc/reference/latest/IO/Tasks-and-Threads/#IO___asTask) (prio := [.dedicated](https://lean-lang.org/doc/reference/latest/IO/Tasks-and-Threads/#Task___Priority___dedicated)) [do]](#manual-Lean___Parser___Term___do)
let cost ← [IO.rand](https://lean-lang.org/doc/reference/latest/IO/Random-Numbers/#IO___rand) 1 100
[IO.sleep](https://lean-lang.org/doc/reference/latest/IO/Timing/#IO___sleep) (← [IO.rand](https://lean-lang.org/doc/reference/latest/IO/Random-Numbers/#IO___rand) 10 100).[toUInt32]](#manual-Nat___toUInt32)
balance.[modify]](#manual-ST___Ref___modify) fun b =>
[if]](#manual-termIfThenElse) cost < b [then]](#manual-termIfThenElse)
b - cost
[else]](#manual-termIfThenElse) b
orders := orders.[push]](#manual-Array___push) o
-- Wait until all orders are completed
for o in orders do
match o.[get](https://lean-lang.org/doc/reference/latest/IO/Tasks-and-Threads/#Task___get) with
| [.ok]](#manual-Except___error) () => [pure]](#manual-Pure___mk) ()
| [.error]](#manual-Except___error) e => [throw]](#manual-MonadExcept___mk) e
if (← balance.[get]](#manual-ST___Ref___get)) < 0 then
[IO.eprintln]](#manual-IO___eprintln) "Final balance negative!"
else
[IO.println]](#manual-IO___println) "Final balance is zero or positive."
```

`stdout``Sending out orders...``Final balance is zero or positive.`

`stderr``<empty>`

def

```lean
[ST.Ref.modifyGet]](#manual-ST___Ref___modifyGet) {σ : Type} {m : Type → Type} [[MonadLiftT]](#manual-MonadLiftT___mk) ([ST]](#manual-ST) σ) m]
  {α β : Type} (r : [ST.Ref]](#manual-ST___Ref___mk) σ α) (f : α → β [×]](#manual-Prod___mk) α) : m β



[ST.Ref.modifyGet]](#manual-ST___Ref___modifyGet) {σ : Type}
  {m : Type → Type} [[MonadLiftT]](#manual-MonadLiftT___mk) ([ST]](#manual-ST) σ) m]
  {α β : Type} (r : [ST.Ref]](#manual-ST___Ref___mk) σ α)
  (f : α → β [×]](#manual-Prod___mk) α) : m β
```

Atomically modifies a mutable reference cell by replacing its contents with the result of a function
call that simultaneously computes a value to return.

def

```lean
[ST.Ref.swap]](#manual-ST___Ref___swap) {σ : Type} {m : Type → Type} [[MonadLiftT]](#manual-MonadLiftT___mk) ([ST]](#manual-ST) σ) m]
  {α : Type} (r : [ST.Ref]](#manual-ST___Ref___mk) σ α) (a : α) : m α



[ST.Ref.swap]](#manual-ST___Ref___swap) {σ : Type} {m : Type → Type}
  [[MonadLiftT]](#manual-MonadLiftT___mk) ([ST]](#manual-ST) σ) m] {α : Type}
  (r : [ST.Ref]](#manual-ST___Ref___mk) σ α) (a : α) : m α
```

Atomically swaps the value of a mutable reference cell with another value. The reference cell's
original value is returned.

#### 21.4.1.2. Comparisons {#manual-The-Lean-Language-Reference--IO--Mutable-References--State-Transformers--Comparisons}

def

```lean
[ST.Ref.ptrEq]](#manual-ST___Ref___ptrEq) {σ : Type} {m : Type → Type} [[MonadLiftT]](#manual-MonadLiftT___mk) ([ST]](#manual-ST) σ) m]
  {α : Type} (r1 r2 : [ST.Ref]](#manual-ST___Ref___mk) σ α) : m [Bool]](#manual-Bool___false)



[ST.Ref.ptrEq]](#manual-ST___Ref___ptrEq) {σ : Type} {m : Type → Type}
  [[MonadLiftT]](#manual-MonadLiftT___mk) ([ST]](#manual-ST) σ) m] {α : Type}
  (r1 r2 : [ST.Ref]](#manual-ST___Ref___mk) σ α) : m [Bool]](#manual-Bool___false)
```

Checks whether two reference cells are in fact aliases for the same cell.

Even if they contain the same value, two references allocated by different executions of `[IO.mkRef]](#manual-IO___mkRef)`
or `[ST.mkRef]](#manual-ST___mkRef)` are distinct. Modifying one has no effect on the other. Likewise, a single reference
cell may be aliased, and modifications to one alias also modify the other.

#### 21.4.1.3. `ST`-Backed State Monads {#manual-The-Lean-Language-Reference--IO--Mutable-References--State-Transformers--ST--Backed-State-Monads}

def

```lean
[ST.Ref.toMonadStateOf]](#manual-ST___Ref___toMonadStateOf) {σ : Type} {m : Type → Type} [[MonadLiftT]](#manual-MonadLiftT___mk) ([ST]](#manual-ST) σ) m]
  {α : Type} (r : [ST.Ref]](#manual-ST___Ref___mk) σ α) : [MonadStateOf]](#manual-MonadStateOf___mk) α m



[ST.Ref.toMonadStateOf]](#manual-ST___Ref___toMonadStateOf) {σ : Type}
  {m : Type → Type} [[MonadLiftT]](#manual-MonadLiftT___mk) ([ST]](#manual-ST) σ) m]
  {α : Type} (r : [ST.Ref]](#manual-ST___Ref___mk) σ α) :
  [MonadStateOf]](#manual-MonadStateOf___mk) α m
```

Creates a `[MonadStateOf]](#manual-MonadStateOf___mk)` instance from a reference cell.

This allows programs written against the [state monad](https://lean-lang.org/doc/reference/4.34.0-rc1/find/?domain=Verso.Genre.Manual.section&name=state-monads) API to
be executed using a mutable reference cell to track the state.

### 21.4.2. Concurrency {#manual-ref-locks}

Mutable references can be used as a locking mechanism.
*Taking* the contents of the reference causes attempts to take it or to read from it to block until it is `[set]](#manual-ST___Ref___set)` again.
This is a low-level feature that can be used to implement other synchronization mechanisms; it's usually better to rely on higher-level abstractions when possible.

unsafe def

```lean
[ST.Ref.take]](#manual-ST___Ref___take) {σ : Type} {m : Type → Type} [[MonadLiftT]](#manual-MonadLiftT___mk) ([ST]](#manual-ST) σ) m]
  {α : Type} (r : [ST.Ref]](#manual-ST___Ref___mk) σ α) : m α



[ST.Ref.take]](#manual-ST___Ref___take) {σ : Type} {m : Type → Type}
  [[MonadLiftT]](#manual-MonadLiftT___mk) ([ST]](#manual-ST) σ) m] {α : Type}
  (r : [ST.Ref]](#manual-ST___Ref___mk) σ α) : m α
```

Reads the value of a mutable reference cell, removing it.

This causes subsequent attempts to read from or take the reference cell to block until a new value
is written using `[ST.Ref.set]](#manual-ST___Ref___set)`.

**Example: Reference Cells as Locks**

This program launches 100 threads.
Each thread simulates a purchase attempt: it generates a random price, and if the account balance is sufficient, it decrements it by the price.
If the balance is not sufficient, then it is not decremented.
Because each thread `[take]](#manual-ST___Ref___take)`s the balance cell prior to checking it and only returns it when it is finished, the cell acts as a lock.
Unlike using `[ST.Ref.modify]](#manual-ST___Ref___modify)`, which atomically modifies the contents of the cell using a pure function, other `[IO]](#manual-IO)` actions may occur in the critical section
This program's `main` function is marked `unsafe` because `[take]](#manual-ST___Ref___take)` itself is unsafe.

```lean
unsafe def main : [IO]](#manual-IO) [Unit]](#manual-Unit) := [do]](#manual-Lean___Parser___Term___do)
let balance ← [IO.mkRef]](#manual-IO___mkRef) (100 : [Int]](#manual-Int___ofNat))
let validationUsed ← [IO.mkRef]](#manual-IO___mkRef) [false]](#manual-Bool___false)
let mut orders := #[]
[IO.println]](#manual-IO___println) "Sending out orders..."
for _ in [0:100] do
let o ← [IO.asTask](https://lean-lang.org/doc/reference/latest/IO/Tasks-and-Threads/#IO___asTask) (prio := [.dedicated](https://lean-lang.org/doc/reference/latest/IO/Tasks-and-Threads/#Task___Priority___dedicated)) [do]](#manual-Lean___Parser___Term___do)
let cost ← [IO.rand](https://lean-lang.org/doc/reference/latest/IO/Random-Numbers/#IO___rand) 1 100
[IO.sleep](https://lean-lang.org/doc/reference/latest/IO/Timing/#IO___sleep) (← [IO.rand](https://lean-lang.org/doc/reference/latest/IO/Random-Numbers/#IO___rand) 10 100).[toUInt32]](#manual-Nat___toUInt32)
let b ← balance.[take]](#manual-ST___Ref___take)
if cost ≤ b then
balance.[set]](#manual-ST___Ref___set) (b - cost)
else
balance.[set]](#manual-ST___Ref___set) b
validationUsed.[set]](#manual-ST___Ref___set) [true]](#manual-Bool___false)
orders := orders.[push]](#manual-Array___push) o
-- Wait until all orders are completed
for o in orders do
match o.[get](https://lean-lang.org/doc/reference/latest/IO/Tasks-and-Threads/#Task___get) with
| [.ok]](#manual-Except___error) () => [pure]](#manual-Pure___mk) ()
| [.error]](#manual-Except___error) e => [throw]](#manual-MonadExcept___mk) e
if (← validationUsed.[get]](#manual-ST___Ref___get)) then
[IO.println]](#manual-IO___println) "Validation prevented a negative balance."
if (← balance.[get]](#manual-ST___Ref___get)) < 0 then
[IO.eprintln]](#manual-IO___eprintln) "Final balance negative!"
else
[IO.println]](#manual-IO___println) "Final balance is zero or positive."
```

The program's output is:

`stdout``Sending out orders...``Validation prevented a negative balance.``Final balance is zero or positive.`

---



## IO — 21.5. Files, File Handles, and Streams {#manual-io-215-files-file-handles-and-streams}

> 📄 Source: https://lean-lang.org/doc/reference/latest/IO/Files___-File-Handles___-and-Streams/

Lean provides a consistent filesystem API on all supported platforms.
These are the key concepts:

Files
:   Files are an abstraction provided by operating systems that provide random access to persistently-stored data, organized hierarchically into directories.

Directories
:   Directories, also known as *folders*, may contain files or other directories.
    Fundamentally, a directory maps names to the files and/or directories that it contains.

File Handles
:   File handles (`[Handle]](#manual-IO___FS___Handle)`) are abstract references to files that have been opened for reading and/or writing.
    A file handle maintains a mode that determines whether reading and/or writing are allowed, along with a cursor that points at a specific location in the file.
    Reading from or writing to a file handle advances the cursor.
    File handles may be buffered, which means that reading from a file handle may not return the current contents of the persistent data, and writing to a file handle may not modify them immediately.

Paths
:   Files are primarily accessed via *paths* (`[System.FilePath]](#manual-System___FilePath___mk)`).
    A path is a sequence of directory names, potentially terminated by a file name.
    They are represented by strings in which separator characters The current platform's separator characters are listed in `[System.FilePath.pathSeparators]](#manual-System___FilePath___pathSeparators)`. delimit the names.

    The details of paths are platform-specific.
    Absolute paths begin in a *root directory*; some operating systems have a single root, while others may have multiple root directories.
    Relative paths do not begin in a root directory and require that some other directory be taken as a starting point.
    In addition to directories, paths may contain the special directory names `.`, which refers to the directory in which it is found, and `..`, which refers to prior directory in the path.

    Filenames, and thus paths, may end in one or more *extensions* that identify the file's type.
    Extensions are delimited by the character `[System.FilePath.extSeparator]](#manual-System___FilePath___extSeparator)`.
    On some platforms, executable files have a special extension (`[System.FilePath.exeExtension]](#manual-System___FilePath___exeExtension)`).

Streams
:   Streams are a higher-level abstraction over files, both providing additional functionality and hiding some details of files.
    While [file handles]](#manual---tech-term-File-Handles) are essentially a thin wrapper around the operating system's representation, streams are implemented in Lean as a structure called `[IO.FS.Stream]](#manual-IO___FS___Stream___mk)`.
    Because streams are implemented in Lean, user code can create additional streams, which can be used seamlessly together with those provided in the standard library.

### 21.5.1. Low-Level File API {#manual-The-Lean-Language-Reference--IO--Files___-File-Handles___-and-Streams--Low-Level-File-API}

At the lowest level, files are explicitly opened using `[Handle.mk]](#manual-IO___FS___Handle___mk)`.
When the last reference to the handle object is dropped, the file is closed.
There is no explicit way to close a file handle other than by ensuring that there are no references to it.

opaque

```lean
[IO.FS.Handle]](#manual-IO___FS___Handle) : Type



[IO.FS.Handle]](#manual-IO___FS___Handle) : Type
```

A reference to an opened file.

File handles wrap the underlying operating system's file descriptors. There is no explicit operation
to close a file: when the last reference to a file handle is dropped, the file is closed
automatically.

Handles have an associated read/write cursor that determines where reads and writes occur in the
file.

opaque

```lean
[IO.FS.Handle.mk]](#manual-IO___FS___Handle___mk) (fn : [System.FilePath]](#manual-System___FilePath___mk)) (mode : [IO.FS.Mode]](#manual-IO___FS___Mode___read)) :
  [IO]](#manual-IO) [IO.FS.Handle]](#manual-IO___FS___Handle)



[IO.FS.Handle.mk]](#manual-IO___FS___Handle___mk) (fn : [System.FilePath]](#manual-System___FilePath___mk))
  (mode : [IO.FS.Mode]](#manual-IO___FS___Mode___read)) : [IO]](#manual-IO) [IO.FS.Handle]](#manual-IO___FS___Handle)
```

Opens the file at `fn` with the given `mode`.

An exception is thrown if the file cannot be opened.

inductive type

```lean
[IO.FS.Mode]](#manual-IO___FS___Mode___read) : Type



[IO.FS.Mode]](#manual-IO___FS___Mode___read) : Type
```

Whether a file should be opened for reading, writing, creation and writing, or appending.

At the operating system level, this translates to the mode of a file handle (i.e., a set of `[open]](#manual-open)`
flags and an `fdopen` mode).

None of the modes represented by this datatype translate line endings (i.e. `O_BINARY` on Windows).
Furthermore, they are not inherited across process creation (i.e. `O_NOINHERIT` on Windows and
`O_CLOEXEC` elsewhere).

**Operating System Specifics:**

- Windows:
  [`_open`](https://learn.microsoft.com/en-us/cpp/c-runtime-library/reference/open-wopen?view=msvc-170),
  [`_fdopen`](https://learn.microsoft.com/en-us/cpp/c-runtime-library/reference/fdopen-wfdopen?view=msvc-170)
- Linux: [`[open]](#manual-open)`](https://linux.die.net/man/2/open), [`fdopen`](https://linux.die.net/man/3/fdopen)

Constructors

```lean
[IO.FS.Mode.read]](#manual-IO___FS___Mode___read) : [IO.FS.Mode]](#manual-IO___FS___Mode___read)
```

The file should be opened for reading.

The read/write cursor is positioned at the beginning of the file. It is an error if the file does
not exist.

- `[open]](#manual-open)` flags: `O_RDONLY`
- `fdopen` mode: `r`

```lean
[IO.FS.Mode.write]](#manual-IO___FS___Mode___read) : [IO.FS.Mode]](#manual-IO___FS___Mode___read)
```

The file should be opened for writing.

If the file already exists, it is truncated to zero length. Otherwise, a new file is created. The
read/write cursor is positioned at the beginning of the file.

- `[open]](#manual-open)` flags: `O_WRONLY | O_CREAT | O_TRUNC`
- `fdopen` mode: `w`

```lean
[IO.FS.Mode.writeNew]](#manual-IO___FS___Mode___read) : [IO.FS.Mode]](#manual-IO___FS___Mode___read)
```

A new file should be created for writing.

It is an error if the file already exists. A new file is created, with the read/write cursor
positioned at the start.

- `[open]](#manual-open)` flags: `O_WRONLY | O_CREAT | O_TRUNC | O_EXCL`
- `fdopen` mode: `w`

```lean
[IO.FS.Mode.readWrite]](#manual-IO___FS___Mode___read) : [IO.FS.Mode]](#manual-IO___FS___Mode___read)
```

The file should be opened for both reading and writing.

It is an error if the file does not already exist. The read/write cursor is positioned at the
start of the file.

- `[open]](#manual-open)` flags: `O_RDWR`
- `fdopen` mode: `r+`

```lean
[IO.FS.Mode.append]](#manual-IO___FS___Mode___read) : [IO.FS.Mode]](#manual-IO___FS___Mode___read)
```

The file should be opened for writing.

If the file does not already exist, it is created. If the file already exists, it is opened, and
the read/write cursor is positioned at the end of the file.

- `[open]](#manual-open)` flags: `O_WRONLY | O_CREAT | O_APPEND`
- `fdopen` mode: `a`

opaque

```lean
[IO.FS.Handle.read]](#manual-IO___FS___Handle___read) (h : [IO.FS.Handle]](#manual-IO___FS___Handle)) (bytes : [USize]](#manual-USize___ofBitVec)) : [IO]](#manual-IO) [ByteArray]](#manual-ByteArray___mk)



[IO.FS.Handle.read]](#manual-IO___FS___Handle___read) (h : [IO.FS.Handle]](#manual-IO___FS___Handle))
  (bytes : [USize]](#manual-USize___ofBitVec)) : [IO]](#manual-IO) [ByteArray]](#manual-ByteArray___mk)
```

Reads up to the given number of bytes from the handle. If the returned array is empty, an
end-of-file marker (EOF) has been reached.

Encountering an EOF does not close a handle. Subsequent reads may block and return more data.

def

```lean
[IO.FS.Handle.readToEnd]](#manual-IO___FS___Handle___readToEnd) (h : [IO.FS.Handle]](#manual-IO___FS___Handle)) : [IO]](#manual-IO) [String]](#manual-String___ofByteArray)



[IO.FS.Handle.readToEnd]](#manual-IO___FS___Handle___readToEnd)
  (h : [IO.FS.Handle]](#manual-IO___FS___Handle)) : [IO]](#manual-IO) [String]](#manual-String___ofByteArray)
```

Reads the entire remaining contents of the file handle as a UTF-8-encoded string. An exception is
thrown if the contents are not valid UTF-8.

The underlying file is not automatically closed, and subsequent reads from the handle may block
and/or return data.

def

```lean
[IO.FS.Handle.readBinToEnd]](#manual-IO___FS___Handle___readBinToEnd) (h : [IO.FS.Handle]](#manual-IO___FS___Handle)) : [IO]](#manual-IO) [ByteArray]](#manual-ByteArray___mk)



[IO.FS.Handle.readBinToEnd]](#manual-IO___FS___Handle___readBinToEnd)
  (h : [IO.FS.Handle]](#manual-IO___FS___Handle)) : [IO]](#manual-IO) [ByteArray]](#manual-ByteArray___mk)
```

Reads the entire remaining contents of the file handle until an end-of-file marker (EOF) is
encountered.

The underlying file is not automatically closed upon encountering an EOF, and subsequent reads from
the handle may block and/or return data.

def

```lean
[IO.FS.Handle.readBinToEndInto]](#manual-IO___FS___Handle___readBinToEndInto) (h : [IO.FS.Handle]](#manual-IO___FS___Handle)) (buf : [ByteArray]](#manual-ByteArray___mk)) :
  [IO]](#manual-IO) [ByteArray]](#manual-ByteArray___mk)



[IO.FS.Handle.readBinToEndInto]](#manual-IO___FS___Handle___readBinToEndInto)
  (h : [IO.FS.Handle]](#manual-IO___FS___Handle)) (buf : [ByteArray]](#manual-ByteArray___mk)) :
  [IO]](#manual-IO) [ByteArray]](#manual-ByteArray___mk)
```

Reads the entire remaining contents of the file handle until an end-of-file marker (EOF) is
encountered.

The underlying file is not automatically closed upon encountering an EOF, and subsequent reads from
the handle may block and/or return data.

opaque

```lean
[IO.FS.Handle.getLine]](#manual-IO___FS___Handle___getLine) (h : [IO.FS.Handle]](#manual-IO___FS___Handle)) : [IO]](#manual-IO) [String]](#manual-String___ofByteArray)



[IO.FS.Handle.getLine]](#manual-IO___FS___Handle___getLine) (h : [IO.FS.Handle]](#manual-IO___FS___Handle)) :
  [IO]](#manual-IO) [String]](#manual-String___ofByteArray)
```

Reads UTF-8-encoded text up to and including the next line break from the handle. If the returned
string is empty, an end-of-file marker (EOF) has been reached.

Encountering an EOF does not close a handle. Subsequent reads may block and return more data.

opaque

```lean
[IO.FS.Handle.write]](#manual-IO___FS___Handle___write) (h : [IO.FS.Handle]](#manual-IO___FS___Handle)) (buffer : [ByteArray]](#manual-ByteArray___mk)) : [IO]](#manual-IO) [Unit]](#manual-Unit)



[IO.FS.Handle.write]](#manual-IO___FS___Handle___write) (h : [IO.FS.Handle]](#manual-IO___FS___Handle))
  (buffer : [ByteArray]](#manual-ByteArray___mk)) : [IO]](#manual-IO) [Unit]](#manual-Unit)
```

Writes the provided bytes to the handle.

Writing to a handle is typically buffered, and may not immediately modify the file on disk. Use
`[IO.FS.Handle.flush]](#manual-IO___FS___Handle___flush)` to write changes to buffers to the associated device.

opaque

```lean
[IO.FS.Handle.putStr]](#manual-IO___FS___Handle___putStr) (h : [IO.FS.Handle]](#manual-IO___FS___Handle)) (s : [String]](#manual-String___ofByteArray)) : [IO]](#manual-IO) [Unit]](#manual-Unit)



[IO.FS.Handle.putStr]](#manual-IO___FS___Handle___putStr) (h : [IO.FS.Handle]](#manual-IO___FS___Handle))
  (s : [String]](#manual-String___ofByteArray)) : [IO]](#manual-IO) [Unit]](#manual-Unit)
```

Writes the provided string to the file handle using the UTF-8 encoding.

Writing to a handle is typically buffered, and may not immediately modify the file on disk. Use
`[IO.FS.Handle.flush]](#manual-IO___FS___Handle___flush)` to write changes to buffers to the associated device.

def

```lean
[IO.FS.Handle.putStrLn]](#manual-IO___FS___Handle___putStrLn) (h : [IO.FS.Handle]](#manual-IO___FS___Handle)) (s : [String]](#manual-String___ofByteArray)) : [IO]](#manual-IO) [Unit]](#manual-Unit)



[IO.FS.Handle.putStrLn]](#manual-IO___FS___Handle___putStrLn) (h : [IO.FS.Handle]](#manual-IO___FS___Handle))
  (s : [String]](#manual-String___ofByteArray)) : [IO]](#manual-IO) [Unit]](#manual-Unit)
```

Writes the contents of the string to the handle, followed by a newline. Uses UTF-8.

opaque

```lean
[IO.FS.Handle.flush]](#manual-IO___FS___Handle___flush) (h : [IO.FS.Handle]](#manual-IO___FS___Handle)) : [IO]](#manual-IO) [Unit]](#manual-Unit)



[IO.FS.Handle.flush]](#manual-IO___FS___Handle___flush) (h : [IO.FS.Handle]](#manual-IO___FS___Handle)) :
  [IO]](#manual-IO) [Unit]](#manual-Unit)
```

Flushes the output buffer associated with the handle, writing any unwritten data to the associated
output device.

opaque

```lean
[IO.FS.Handle.rewind]](#manual-IO___FS___Handle___rewind) (h : [IO.FS.Handle]](#manual-IO___FS___Handle)) : [IO]](#manual-IO) [Unit]](#manual-Unit)



[IO.FS.Handle.rewind]](#manual-IO___FS___Handle___rewind) (h : [IO.FS.Handle]](#manual-IO___FS___Handle)) :
  [IO]](#manual-IO) [Unit]](#manual-Unit)
```

Rewinds the read/write cursor to the beginning of the handle's file.

opaque

```lean
[IO.FS.Handle.truncate]](#manual-IO___FS___Handle___truncate) (h : [IO.FS.Handle]](#manual-IO___FS___Handle)) : [IO]](#manual-IO) [Unit]](#manual-Unit)



[IO.FS.Handle.truncate]](#manual-IO___FS___Handle___truncate) (h : [IO.FS.Handle]](#manual-IO___FS___Handle)) :
  [IO]](#manual-IO) [Unit]](#manual-Unit)
```

Truncates the handle to its read/write cursor.

This operation does not automatically flush output buffers, so the contents of the output device may
not reflect the change immediately. This does not usually lead to problems because the read/write
cursor includes buffered writes. However, buffered writes followed by `[IO.FS.Handle.rewind]](#manual-IO___FS___Handle___rewind)`, then
`[IO.FS.Handle.truncate]](#manual-IO___FS___Handle___truncate)`, and then closing the file may lead to a non-empty file. If unsure, call
`[IO.FS.Handle.flush]](#manual-IO___FS___Handle___flush)` before truncating.

opaque

```lean
[IO.FS.Handle.isTty]](#manual-IO___FS___Handle___isTty) (h : [IO.FS.Handle]](#manual-IO___FS___Handle)) : [BaseIO]](#manual-BaseIO) [Bool]](#manual-Bool___false)



[IO.FS.Handle.isTty]](#manual-IO___FS___Handle___isTty) (h : [IO.FS.Handle]](#manual-IO___FS___Handle)) :
  [BaseIO]](#manual-BaseIO) [Bool]](#manual-Bool___false)
```

Returns `[true]](#manual-Bool___false)` if a handle refers to a Windows console or a Unix terminal.

opaque

```lean
[IO.FS.Handle.lock]](#manual-IO___FS___Handle___lock) (h : [IO.FS.Handle]](#manual-IO___FS___Handle)) (exclusive : [Bool]](#manual-Bool___false) := [true]](#manual-Bool___false)) :
  [IO]](#manual-IO) [Unit]](#manual-Unit)



[IO.FS.Handle.lock]](#manual-IO___FS___Handle___lock) (h : [IO.FS.Handle]](#manual-IO___FS___Handle))
  (exclusive : [Bool]](#manual-Bool___false) := [true]](#manual-Bool___false)) : [IO]](#manual-IO) [Unit]](#manual-Unit)
```

Acquires an exclusive or shared lock on the handle. Blocks to wait for the lock if necessary.

Acquiring an exclusive lock while already possessing a shared lock will **not** reliably succeed: it
works on Unix-like systems but not on Windows.

opaque

```lean
[IO.FS.Handle.tryLock]](#manual-IO___FS___Handle___tryLock) (h : [IO.FS.Handle]](#manual-IO___FS___Handle)) (exclusive : [Bool]](#manual-Bool___false) := [true]](#manual-Bool___false)) :
  [IO]](#manual-IO) [Bool]](#manual-Bool___false)



[IO.FS.Handle.tryLock]](#manual-IO___FS___Handle___tryLock) (h : [IO.FS.Handle]](#manual-IO___FS___Handle))
  (exclusive : [Bool]](#manual-Bool___false) := [true]](#manual-Bool___false)) : [IO]](#manual-IO) [Bool]](#manual-Bool___false)
```

Tries to acquire an exclusive or shared lock on the handle and returns `[true]](#manual-Bool___false)` if successful. Will
not block if the lock cannot be acquired, but instead returns `[false]](#manual-Bool___false)`.

Acquiring an exclusive lock while already possessing a shared lock will **not** reliably succeed: it
works on Unix-like systems but not on Windows.

opaque

```lean
[IO.FS.Handle.unlock]](#manual-IO___FS___Handle___unlock) (h : [IO.FS.Handle]](#manual-IO___FS___Handle)) : [IO]](#manual-IO) [Unit]](#manual-Unit)



[IO.FS.Handle.unlock]](#manual-IO___FS___Handle___unlock) (h : [IO.FS.Handle]](#manual-IO___FS___Handle)) :
  [IO]](#manual-IO) [Unit]](#manual-Unit)
```

Releases any previously-acquired lock on the handle. Succeeds even if no lock has been acquired.

**Example: One File, Multiple Handles**

This program has two handles to the same file.
Because file I/O may be buffered independently for each handle, `[Handle.flush]](#manual-IO___FS___Handle___flush)` should be called when the buffers need to be synchronized with the file's actual contents.
Here, the two handles proceed in lock-step through the file, with one of them a single byte ahead of the other.
The first handle is used to count the number of occurrences of `'A'`, while the second is used to replace each `'A'` with `'!'`.
The second handle is opened in `[readWrite]](#manual-IO___FS___Mode___read)` mode rather than `[write]](#manual-IO___FS___Mode___read)` mode because opening an existing file in `[write]](#manual-IO___FS___Mode___read)` mode replaces it with an empty file.
In this case, the buffers don't need to be flushed during execution because modifications occur only to parts of the file that will not be read again, but the write handle should be flushed after the loop has completed.

```lean
[open]](#manual-Lean___Parser___Command___open) IO.FS ([Handle]](#manual-IO___FS___Handle))
def main : [IO]](#manual-IO) [Unit]](#manual-Unit) := [do]](#manual-Lean___Parser___Term___do)
[IO.println]](#manual-IO___println) s!"Starting contents: '{(← [IO.FS.readFile]](#manual-IO___FS___readFile) "data").[trimAscii]](#manual-String___trimAscii)}'"
let h ← [Handle.mk]](#manual-IO___FS___Handle___mk) "data" [.read]](#manual-IO___FS___Mode___read)
let h' ← [Handle.mk]](#manual-IO___FS___Handle___mk) "data" [.readWrite]](#manual-IO___FS___Mode___read)
h'.[rewind]](#manual-IO___FS___Handle___rewind)
let mut count := 0
let mut buf : [ByteArray]](#manual-ByteArray___mk) ← h.[read]](#manual-IO___FS___Handle___read) 1
while ok : buf.[size]](#manual-ByteArray___size) = 1 do
if [Char.ofUInt8]](#manual-Char___ofUInt8) buf[0] == 'A' then
count := count + 1
h'.[write]](#manual-IO___FS___Handle___write) ([ByteArray.empty]](#manual-ByteArray___empty).[push]](#manual-ByteArray___push) '!'.[toUInt8]](#manual-Char___toUInt8))
else
h'.[write]](#manual-IO___FS___Handle___write) buf
buf ← h.[read]](#manual-IO___FS___Handle___read) 1
h'.[flush]](#manual-IO___FS___Handle___flush)
[IO.println]](#manual-IO___println) s!"Count: {count}"
[IO.println]](#manual-IO___println) s!"Contents: '{(← [IO.FS.readFile]](#manual-IO___FS___readFile) "data").[trimAscii]](#manual-String___trimAscii)}'"
```

When run on this file:

Input: `data``AABAABCDAB`

the program outputs:

`stdout``Starting contents: 'AABAABCDAB'``Count: 5``Contents: '!!B!!BCD!B'`

Afterwards, the file contains:

Output: `data``!!B!!BCD!B`

### 21.5.2. Streams {#manual-The-Lean-Language-Reference--IO--Files___-File-Handles___-and-Streams--Streams}

structure

```lean
[IO.FS.Stream]](#manual-IO___FS___Stream___mk) : Type



[IO.FS.Stream]](#manual-IO___FS___Stream___mk) : Type
```

A pure-Lean abstraction of POSIX streams. These streams may represent an underlying POSIX stream or
be implemented by Lean code.

Because standard input, standard output, and standard error are all `[IO.FS.Stream]](#manual-IO___FS___Stream___mk)`s that can be
overridden, Lean code may capture and redirect input and output.

Constructor

```lean
[IO.FS.Stream.mk]](#manual-IO___FS___Stream___mk)
```

Fields

```lean
flush : [IO]](#manual-IO) [Unit]](#manual-Unit)
```

Flushes the stream's output buffers.

```lean
read : [USize]](#manual-USize___ofBitVec) → [IO]](#manual-IO) [ByteArray]](#manual-ByteArray___mk)
```

Reads up to the given number of bytes from the stream.

If the returned array is empty, an end-of-file marker (EOF) has been reached. An EOF does not
actually close a stream, so further reads may block and return more data.

```lean
write : [ByteArray]](#manual-ByteArray___mk) → [IO]](#manual-IO) [Unit]](#manual-Unit)
```

Writes the provided bytes to the stream.

If the stream represents a physical output device such as a file on disk, then the results may be
buffered. Call `FS.Stream.flush` to synchronize their contents.

```lean
getLine : [IO]](#manual-IO) [String]](#manual-String___ofByteArray)
```

Reads text up to and including the next newline from the stream.

If the returned string is empty, an end-of-file marker (EOF) has been reached.
An EOF does not actually close a stream, so further reads may block and return more data.

```lean
putStr : [String]](#manual-String___ofByteArray) → [IO]](#manual-IO) [Unit]](#manual-Unit)
```

Writes the provided string to the stream.

```lean
isTty : [BaseIO]](#manual-BaseIO) [Bool]](#manual-Bool___false)
```

Returns `[true]](#manual-Bool___false)` if a stream refers to a Windows console or Unix terminal.

def

```lean
[IO.FS.Stream.ofBuffer]](#manual-IO___FS___Stream___ofBuffer) (r : [IO.Ref]](#manual-IO___Ref) [IO.FS.Stream.Buffer]](#manual-IO___FS___Stream___Buffer___mk)) : [IO.FS.Stream]](#manual-IO___FS___Stream___mk)



[IO.FS.Stream.ofBuffer]](#manual-IO___FS___Stream___ofBuffer)
  (r : [IO.Ref]](#manual-IO___Ref) [IO.FS.Stream.Buffer]](#manual-IO___FS___Stream___Buffer___mk)) :
  [IO.FS.Stream]](#manual-IO___FS___Stream___mk)
```

Creates a stream from a mutable reference to a buffer.

The resulting stream simulates a file, mutating the contents of the reference in response to writes
and reading from it in response to reads. These streams can be used with `[IO.withStdin]](#manual-IO___withStdin)`,
`[IO.setStdin]](#manual-IO___setStdin)`, and the corresponding operators for standard output and standard error to redirect
input and output.

def

```lean
[IO.FS.Stream.ofHandle]](#manual-IO___FS___Stream___ofHandle) (h : [IO.FS.Handle]](#manual-IO___FS___Handle)) : [IO.FS.Stream]](#manual-IO___FS___Stream___mk)



[IO.FS.Stream.ofHandle]](#manual-IO___FS___Stream___ofHandle) (h : [IO.FS.Handle]](#manual-IO___FS___Handle)) :
  [IO.FS.Stream]](#manual-IO___FS___Stream___mk)
```

Creates a Lean stream from a file handle. Each stream operation is implemented by the corresponding
file handle operation.

def

```lean
[IO.FS.Stream.putStrLn]](#manual-IO___FS___Stream___putStrLn) (strm : [IO.FS.Stream]](#manual-IO___FS___Stream___mk)) (s : [String]](#manual-String___ofByteArray)) : [IO]](#manual-IO) [Unit]](#manual-Unit)



[IO.FS.Stream.putStrLn]](#manual-IO___FS___Stream___putStrLn)
  (strm : [IO.FS.Stream]](#manual-IO___FS___Stream___mk)) (s : [String]](#manual-String___ofByteArray)) :
  [IO]](#manual-IO) [Unit]](#manual-Unit)
```

Writes the contents of the string to the stream, followed by a newline.

structure

```lean
[IO.FS.Stream.Buffer]](#manual-IO___FS___Stream___Buffer___mk) : Type



[IO.FS.Stream.Buffer]](#manual-IO___FS___Stream___Buffer___mk) : Type
```

A byte buffer that can simulate a file in memory.

Use `[IO.FS.Stream.ofBuffer]](#manual-IO___FS___Stream___ofBuffer)` to create a stream from a buffer.

Constructor

```lean
[IO.FS.Stream.Buffer.mk]](#manual-IO___FS___Stream___Buffer___mk)
```

Fields

```lean
data : [ByteArray]](#manual-ByteArray___mk)
```

The contents of the buffer.

```lean
pos : [Nat]](#manual-Nat___zero)
```

The read/write cursor's position in the buffer.

### 21.5.3. Paths {#manual-The-Lean-Language-Reference--IO--Files___-File-Handles___-and-Streams--Paths}

Paths are represented by strings.
Different platforms have different conventions for paths: some use slashes (`/`) as directory separators, others use backslashes (`\`).
Some are case-sensitive, others are not.
Different Unicode encodings and normal forms may be used to represent filenames, and some platforms consider filenames to be byte sequences rather than strings.
A string that represents an [absolute path]](#manual---tech-term-Absolute-paths) on one system may not even be a valid path on another system.

To write Lean code that is as compatible as possible with multiple systems, it can be helpful to use Lean's path manipulation primitives instead of raw string manipulation.
Helpers such as `[System.FilePath.join]](#manual-System___FilePath___join)` take platform-specific rules for absolute paths into account, `[System.FilePath.pathSeparator]](#manual-System___FilePath___pathSeparator)` contains the appropriate path separator for the current platform, and `[System.FilePath.exeExtension]](#manual-System___FilePath___exeExtension)` contains any necessary extension for executable files.
Avoid hard-coding these rules.

There is an instance of the `[Div]](#manual-Div___mk)` type class for `[FilePath]](#manual-System___FilePath___mk)` which allows the slash operator to be used to concatenate paths.

structure

```lean
[System.FilePath]](#manual-System___FilePath___mk) : Type



[System.FilePath]](#manual-System___FilePath___mk) : Type
```

A path on the file system.

Paths consist of a sequence of directories followed by the name of a file or directory. They are
delimited by a platform-dependent separator character (see `[System.FilePath.pathSeparator]](#manual-System___FilePath___pathSeparator)`).

Constructor

```lean
[System.FilePath.mk]](#manual-System___FilePath___mk)
```

Fields

```lean
toString : [String]](#manual-String___ofByteArray)
```

The string representation of the path.

def

```lean
[System.mkFilePath]](#manual-System___mkFilePath) (parts : [List]](#manual-List___nil) [String]](#manual-String___ofByteArray)) : [System.FilePath]](#manual-System___FilePath___mk)



[System.mkFilePath]](#manual-System___mkFilePath) (parts : [List]](#manual-List___nil) [String]](#manual-String___ofByteArray)) :
  [System.FilePath]](#manual-System___FilePath___mk)
```

Constructs a path from a list of file names by interspersing them with the current platform's path
separator.

def

```lean
[System.FilePath.join]](#manual-System___FilePath___join) (p sub : [System.FilePath]](#manual-System___FilePath___mk)) : [System.FilePath]](#manual-System___FilePath___mk)



[System.FilePath.join]](#manual-System___FilePath___join)
  (p sub : [System.FilePath]](#manual-System___FilePath___mk)) :
  [System.FilePath]](#manual-System___FilePath___mk)
```

Appends two paths, taking absolute paths into account. This operation is also accessible via the `/`
operator.

If `sub` is an absolute path, then `p` is discarded and `sub` is returned. If `sub` is a relative
path, then it is attached to `p` with the platform-specific path separator.

def

```lean
[System.FilePath.normalize]](#manual-System___FilePath___normalize) (p : [System.FilePath]](#manual-System___FilePath___mk)) : [System.FilePath]](#manual-System___FilePath___mk)



[System.FilePath.normalize]](#manual-System___FilePath___normalize)
  (p : [System.FilePath]](#manual-System___FilePath___mk)) : [System.FilePath]](#manual-System___FilePath___mk)
```

Normalizes a path, returning an equivalent path that may better follow platform conventions.

In particular:

- On Windows, drive letters are made uppercase.
- On platforms that support multiple path separators (that is, where
  `[System.FilePath.pathSeparators]](#manual-System___FilePath___pathSeparators)` has length greater than one), alternative path separators are
  replaced with the preferred path separator.

There is no guarantee that two equivalent paths normalize to the same path.

def

```lean
[System.FilePath.isAbsolute]](#manual-System___FilePath___isAbsolute) (p : [System.FilePath]](#manual-System___FilePath___mk)) : [Bool]](#manual-Bool___false)



[System.FilePath.isAbsolute]](#manual-System___FilePath___isAbsolute)
  (p : [System.FilePath]](#manual-System___FilePath___mk)) : [Bool]](#manual-Bool___false)
```

An absolute path starts at the root directory or a drive letter. Accessing files through an absolute
path does not depend on the current working directory.

def

```lean
[System.FilePath.isRelative]](#manual-System___FilePath___isRelative) (p : [System.FilePath]](#manual-System___FilePath___mk)) : [Bool]](#manual-Bool___false)



[System.FilePath.isRelative]](#manual-System___FilePath___isRelative)
  (p : [System.FilePath]](#manual-System___FilePath___mk)) : [Bool]](#manual-Bool___false)
```

A relative path is one that depends on the current working directory for interpretation. Relative
paths do not start with the root directory or a drive letter.

def

```lean
[System.FilePath.parent]](#manual-System___FilePath___parent) (p : [System.FilePath]](#manual-System___FilePath___mk)) : [Option]](#manual-Option___none) [System.FilePath]](#manual-System___FilePath___mk)



[System.FilePath.parent]](#manual-System___FilePath___parent)
  (p : [System.FilePath]](#manual-System___FilePath___mk)) :
  [Option]](#manual-Option___none) [System.FilePath]](#manual-System___FilePath___mk)
```

Returns the parent directory of a path, if there is one.

If the path is that of the root directory or the root of a drive letter, `[none]](#manual-Option___none)` is returned.
Otherwise, the path's parent directory is returned.

def

```lean
[System.FilePath.components]](#manual-System___FilePath___components) (p : [System.FilePath]](#manual-System___FilePath___mk)) : [List]](#manual-List___nil) [String]](#manual-String___ofByteArray)



[System.FilePath.components]](#manual-System___FilePath___components)
  (p : [System.FilePath]](#manual-System___FilePath___mk)) : [List]](#manual-List___nil) [String]](#manual-String___ofByteArray)
```

Splits a path into a list of individual file names at the platform-specific path separator.

def

```lean
[System.FilePath.fileName]](#manual-System___FilePath___fileName) (p : [System.FilePath]](#manual-System___FilePath___mk)) : [Option]](#manual-Option___none) [String]](#manual-String___ofByteArray)



[System.FilePath.fileName]](#manual-System___FilePath___fileName)
  (p : [System.FilePath]](#manual-System___FilePath___mk)) : [Option]](#manual-Option___none) [String]](#manual-String___ofByteArray)
```

Extracts the last element of a path if it is a file or directory name.

Returns `[none]](#manual-Option___none)` if the last entry is a special name (such as `.` or `..`) or if the path is the root
directory.

def

```lean
[System.FilePath.fileStem]](#manual-System___FilePath___fileStem) (p : [System.FilePath]](#manual-System___FilePath___mk)) : [Option]](#manual-Option___none) [String]](#manual-String___ofByteArray)



[System.FilePath.fileStem]](#manual-System___FilePath___fileStem)
  (p : [System.FilePath]](#manual-System___FilePath___mk)) : [Option]](#manual-Option___none) [String]](#manual-String___ofByteArray)
```

Extracts the stem (non-extension) part of `p.[fileName]](#manual-System___FilePath___fileName)`.

If the filename contains multiple extensions, then only the last one is removed. Returns `[none]](#manual-Option___none)` if
there is no file name at the end of the path.

Examples:

- `("app.exe" : [System.FilePath]](#manual-System___FilePath___mk)).[fileStem]](#manual-System___FilePath___fileStem) = [some]](#manual-Option___none) "app"`
- `("file.tar.gz" : [System.FilePath]](#manual-System___FilePath___mk)).[fileStem]](#manual-System___FilePath___fileStem) = [some]](#manual-Option___none) "file.tar"`
- `("files/" : [System.FilePath]](#manual-System___FilePath___mk)).[fileStem]](#manual-System___FilePath___fileStem) = [none]](#manual-Option___none)`
- `("files/picture.jpg" : [System.FilePath]](#manual-System___FilePath___mk)).[fileStem]](#manual-System___FilePath___fileStem) = [some]](#manual-Option___none) "picture"`

def

```lean
[System.FilePath.extension]](#manual-System___FilePath___extension) (p : [System.FilePath]](#manual-System___FilePath___mk)) : [Option]](#manual-Option___none) [String]](#manual-String___ofByteArray)



[System.FilePath.extension]](#manual-System___FilePath___extension)
  (p : [System.FilePath]](#manual-System___FilePath___mk)) : [Option]](#manual-Option___none) [String]](#manual-String___ofByteArray)
```

Extracts the extension part of `p.[fileName]](#manual-System___FilePath___fileName)`.

If the filename contains multiple extensions, then only the last one is extracted. Returns `[none]](#manual-Option___none)` if
there is no file name at the end of the path.

Examples:

- `("app.exe" : [System.FilePath]](#manual-System___FilePath___mk)).[extension]](#manual-System___FilePath___extension) = [some]](#manual-Option___none) "exe"`
- `("file.tar.gz" : [System.FilePath]](#manual-System___FilePath___mk)).[extension]](#manual-System___FilePath___extension) = [some]](#manual-Option___none) "gz"`
- `("files/" : [System.FilePath]](#manual-System___FilePath___mk)).[extension]](#manual-System___FilePath___extension) = [none]](#manual-Option___none)`
- `("files/picture.jpg" : [System.FilePath]](#manual-System___FilePath___mk)).[extension]](#manual-System___FilePath___extension) = [some]](#manual-Option___none) "jpg"`

def

```lean
[System.FilePath.addExtension]](#manual-System___FilePath___addExtension) (p : [System.FilePath]](#manual-System___FilePath___mk)) (ext : [String]](#manual-String___ofByteArray)) :
  [System.FilePath]](#manual-System___FilePath___mk)



[System.FilePath.addExtension]](#manual-System___FilePath___addExtension)
  (p : [System.FilePath]](#manual-System___FilePath___mk)) (ext : [String]](#manual-String___ofByteArray)) :
  [System.FilePath]](#manual-System___FilePath___mk)
```

Appends the extension `ext` to a path `p`.

`ext` should not have leading `.`, as this function adds one. If `ext` is the empty string, no
`.` is added.

Unlike `[System.FilePath.withExtension]](#manual-System___FilePath___withExtension)`, this does not remove any existing extension.

def

```lean
[System.FilePath.withExtension]](#manual-System___FilePath___withExtension) (p : [System.FilePath]](#manual-System___FilePath___mk)) (ext : [String]](#manual-String___ofByteArray)) :
  [System.FilePath]](#manual-System___FilePath___mk)



[System.FilePath.withExtension]](#manual-System___FilePath___withExtension)
  (p : [System.FilePath]](#manual-System___FilePath___mk)) (ext : [String]](#manual-String___ofByteArray)) :
  [System.FilePath]](#manual-System___FilePath___mk)
```

Replaces the current extension in a path `p` with `ext`, adding it if there is no extension. If the
path has multiple file extensions, only the last one is replaced. If the path has no filename, or if
`ext` is the empty string, then the filename is returned unmodified.

`ext` should not have a leading `.`, as this function adds one.

Examples:

- `("files/picture.jpeg" : [System.FilePath]](#manual-System___FilePath___mk)).[withExtension]](#manual-System___FilePath___withExtension) "jpg" = ⟨"files/picture.jpg"⟩`
- `("files/" : [System.FilePath]](#manual-System___FilePath___mk)).[withExtension]](#manual-System___FilePath___withExtension) "zip" = ⟨"files/"⟩`
- `("files" : [System.FilePath]](#manual-System___FilePath___mk)).[withExtension]](#manual-System___FilePath___withExtension) "zip" = ⟨"files.zip"⟩`
- `("files/archive.tar.gz" : [System.FilePath]](#manual-System___FilePath___mk)).[withExtension]](#manual-System___FilePath___withExtension) "xz" = ⟨"files.tar.xz"⟩`

def

```lean
[System.FilePath.withFileName]](#manual-System___FilePath___withFileName) (p : [System.FilePath]](#manual-System___FilePath___mk)) (fname : [String]](#manual-String___ofByteArray)) :
  [System.FilePath]](#manual-System___FilePath___mk)



[System.FilePath.withFileName]](#manual-System___FilePath___withFileName)
  (p : [System.FilePath]](#manual-System___FilePath___mk)) (fname : [String]](#manual-String___ofByteArray)) :
  [System.FilePath]](#manual-System___FilePath___mk)
```

Replaces the file name at the end of the path `p` with `fname`, placing `fname` in the parent
directory of `p`.

If `p` has no parent directory, then `fname` is returned unmodified.

def

```lean
[System.FilePath.pathSeparator]](#manual-System___FilePath___pathSeparator) : [Char]](#manual-Char___mk)



[System.FilePath.pathSeparator]](#manual-System___FilePath___pathSeparator) : [Char]](#manual-Char___mk)
```

The character that separates directories.

On platforms that support multiple separators, `[System.FilePath.pathSeparator]](#manual-System___FilePath___pathSeparator)` is the “ideal” one expected by users
on the platform. `[System.FilePath.pathSeparators]](#manual-System___FilePath___pathSeparators)` lists all supported separators.

def

```lean
[System.FilePath.pathSeparators]](#manual-System___FilePath___pathSeparators) : [List]](#manual-List___nil) [Char]](#manual-Char___mk)



[System.FilePath.pathSeparators]](#manual-System___FilePath___pathSeparators) : [List]](#manual-List___nil) [Char]](#manual-Char___mk)
```

The list of all path separator characters supported on the current platform.

On platforms that support multiple separators, `[System.FilePath.pathSeparator]](#manual-System___FilePath___pathSeparator)` is the “ideal” one
expected by users on the platform.

def

```lean
[System.FilePath.extSeparator]](#manual-System___FilePath___extSeparator) : [Char]](#manual-Char___mk)



[System.FilePath.extSeparator]](#manual-System___FilePath___extSeparator) : [Char]](#manual-Char___mk)
```

The character that separates file extensions from file names.

def

```lean
[System.FilePath.exeExtension]](#manual-System___FilePath___exeExtension) : [String]](#manual-String___ofByteArray)



[System.FilePath.exeExtension]](#manual-System___FilePath___exeExtension) : [String]](#manual-String___ofByteArray)
```

The file extension expected for executable binaries on the current platform, or `""` if there is no
such extension.

### 21.5.4. Interacting with the Filesystem {#manual-The-Lean-Language-Reference--IO--Files___-File-Handles___-and-Streams--Interacting-with-the-Filesystem}

Some operations on paths consult the filesystem.

structure

```lean
[IO.FS.Metadata]](#manual-IO___FS___Metadata___mk) : Type



[IO.FS.Metadata]](#manual-IO___FS___Metadata___mk) : Type
```

File metadata.

The metadata for a file can be accessed with `[System.FilePath.metadata]](#manual-System___FilePath___metadata)`/
`[System.FilePath.symlinkMetadata]](#manual-System___FilePath___symlinkMetadata)`.

Constructor

```lean
[IO.FS.Metadata.mk]](#manual-IO___FS___Metadata___mk)
```

Fields

```lean
accessed : IO.FS.SystemTime
```

File access time.

```lean
modified : IO.FS.SystemTime
```

File modification time.

```lean
byteSize : [UInt64]](#manual-UInt64___ofBitVec)
```

The size of the file in bytes.

```lean
type : IO.FS.FileType
```

Whether the file is an ordinary file, a directory, a symbolic link, or some other kind of file.

```lean
numLinks : [UInt64]](#manual-UInt64___ofBitVec)
```

The number of hard links to the file.

opaque

```lean
[System.FilePath.metadata]](#manual-System___FilePath___metadata) : [System.FilePath]](#manual-System___FilePath___mk) → [IO]](#manual-IO) [IO.FS.Metadata]](#manual-IO___FS___Metadata___mk)



[System.FilePath.metadata]](#manual-System___FilePath___metadata) :
  [System.FilePath]](#manual-System___FilePath___mk) → [IO]](#manual-IO) [IO.FS.Metadata]](#manual-IO___FS___Metadata___mk)
```

Returns metadata for the indicated file, following symlinks. Throws an exception if the file does
not exist or the metadata cannot be accessed.

opaque

```lean
[System.FilePath.symlinkMetadata]](#manual-System___FilePath___symlinkMetadata) : [System.FilePath]](#manual-System___FilePath___mk) → [IO]](#manual-IO) [IO.FS.Metadata]](#manual-IO___FS___Metadata___mk)



[System.FilePath.symlinkMetadata]](#manual-System___FilePath___symlinkMetadata) :
  [System.FilePath]](#manual-System___FilePath___mk) → [IO]](#manual-IO) [IO.FS.Metadata]](#manual-IO___FS___Metadata___mk)
```

Returns metadata for the indicated file without following symlinks. Throws an exception if the file
does not exist or the metadata cannot be accessed.

def

```lean
[System.FilePath.pathExists]](#manual-System___FilePath___pathExists) (p : [System.FilePath]](#manual-System___FilePath___mk)) : [BaseIO]](#manual-BaseIO) [Bool]](#manual-Bool___false)



[System.FilePath.pathExists]](#manual-System___FilePath___pathExists)
  (p : [System.FilePath]](#manual-System___FilePath___mk)) : [BaseIO]](#manual-BaseIO) [Bool]](#manual-Bool___false)
```

Checks whether the indicated path points to a file that exists. This function will traverse
symlinks.

def

```lean
[System.FilePath.isDir]](#manual-System___FilePath___isDir) (p : [System.FilePath]](#manual-System___FilePath___mk)) : [BaseIO]](#manual-BaseIO) [Bool]](#manual-Bool___false)



[System.FilePath.isDir]](#manual-System___FilePath___isDir)
  (p : [System.FilePath]](#manual-System___FilePath___mk)) : [BaseIO]](#manual-BaseIO) [Bool]](#manual-Bool___false)
```

Checks whether the indicated path can be read and is a directory. This function will traverse
symlinks.

structure

```lean
[IO.FS.DirEntry]](#manual-IO___FS___DirEntry___mk) : Type



[IO.FS.DirEntry]](#manual-IO___FS___DirEntry___mk) : Type
```

An entry in a directory on a filesystem.

Constructor

```lean
[IO.FS.DirEntry.mk]](#manual-IO___FS___DirEntry___mk)
```

Fields

```lean
root : [System.FilePath]](#manual-System___FilePath___mk)
```

The directory in which the entry is found.

```lean
fileName : [String]](#manual-String___ofByteArray)
```

The name of the entry.

def

```lean
[IO.FS.DirEntry.path]](#manual-IO___FS___DirEntry___path) (entry : [IO.FS.DirEntry]](#manual-IO___FS___DirEntry___mk)) : [System.FilePath]](#manual-System___FilePath___mk)



[IO.FS.DirEntry.path]](#manual-IO___FS___DirEntry___path)
  (entry : [IO.FS.DirEntry]](#manual-IO___FS___DirEntry___mk)) :
  [System.FilePath]](#manual-System___FilePath___mk)
```

The path of the file indicated by the directory entry.

opaque

```lean
[System.FilePath.readDir]](#manual-System___FilePath___readDir) : [System.FilePath]](#manual-System___FilePath___mk) → [IO]](#manual-IO) ([Array]](#manual-Array___mk) [IO.FS.DirEntry]](#manual-IO___FS___DirEntry___mk))



[System.FilePath.readDir]](#manual-System___FilePath___readDir) :
  [System.FilePath]](#manual-System___FilePath___mk) →
    [IO]](#manual-IO) ([Array]](#manual-Array___mk) [IO.FS.DirEntry]](#manual-IO___FS___DirEntry___mk))
```

Returns the contents of the indicated directory. Throws an exception if the file does not exist or
is not a directory.

def

```lean
[System.FilePath.walkDir]](#manual-System___FilePath___walkDir) (p : [System.FilePath]](#manual-System___FilePath___mk))
  (enter : [System.FilePath]](#manual-System___FilePath___mk) → [IO]](#manual-IO) [Bool]](#manual-Bool___false) := fun x => [pure]](#manual-Pure___mk) [true]](#manual-Bool___false)) :
  [IO]](#manual-IO) ([Array]](#manual-Array___mk) [System.FilePath]](#manual-System___FilePath___mk))



[System.FilePath.walkDir]](#manual-System___FilePath___walkDir)
  (p : [System.FilePath]](#manual-System___FilePath___mk))
  (enter : [System.FilePath]](#manual-System___FilePath___mk) → [IO]](#manual-IO) [Bool]](#manual-Bool___false) :=
    fun x => [pure]](#manual-Pure___mk) [true]](#manual-Bool___false)) :
  [IO]](#manual-IO) ([Array]](#manual-Array___mk) [System.FilePath]](#manual-System___FilePath___mk))
```

Traverses a filesystem starting at the path `p` and exploring directories that satisfy `enter`,
returning the paths visited.

The traversal is a preorder traversal, in which parent directories occur prior to any of their
children. Symbolic links are followed.

structure

```lean
[IO.AccessRight]](#manual-IO___AccessRight___mk) : Type



[IO.AccessRight]](#manual-IO___AccessRight___mk) : Type
```

POSIX-style file permissions.

The `FileRight` structure describes these permissions for a file's owner, members of its designated
group, and all others.

Constructor

```lean
[IO.AccessRight.mk]](#manual-IO___AccessRight___mk)
```

Fields

```lean
read : [Bool]](#manual-Bool___false)
```

The file can be read.

```lean
write : [Bool]](#manual-Bool___false)
```

The file can be written to.

```lean
execution : [Bool]](#manual-Bool___false)
```

The file can be executed.

def

```lean
[IO.AccessRight.flags]](#manual-IO___AccessRight___flags) (acc : [IO.AccessRight]](#manual-IO___AccessRight___mk)) : [UInt32]](#manual-UInt32___ofBitVec)



[IO.AccessRight.flags]](#manual-IO___AccessRight___flags)
  (acc : [IO.AccessRight]](#manual-IO___AccessRight___mk)) : [UInt32]](#manual-UInt32___ofBitVec)
```

Converts individual POSIX-style file permissions to their conventional three-bit representation.

This is the bitwise `[or]](#manual-Bool___or)` of the following:

- If the file can be read, `0x4`, otherwise `0`.
- If the file can be written, `0x2`, otherwise `0`.
- If the file can be executed, `0x1`, otherwise `0`.

Examples:

- `{read := true : AccessRight}.flags = 4`
- `{read := true, write := true : AccessRight}.flags = 6`
- `{read := true, execution := true : AccessRight}.flags = 5`

structure

```lean
[IO.FileRight]](#manual-IO___FileRight___mk) : Type



[IO.FileRight]](#manual-IO___FileRight___mk) : Type
```

POSIX-style file permissions that describe access rights for a file's owner, members of its
assigned group, and all others.

Constructor

```lean
[IO.FileRight.mk]](#manual-IO___FileRight___mk)
```

Fields

```lean
user : [IO.AccessRight]](#manual-IO___AccessRight___mk)
```

The owner's permissions to access the file.

```lean
group : [IO.AccessRight]](#manual-IO___AccessRight___mk)
```

The assigned group's permissions to access the file.

```lean
other : [IO.AccessRight]](#manual-IO___AccessRight___mk)
```

The permissions that all others have to access the file.

def

```lean
[IO.FileRight.flags]](#manual-IO___FileRight___flags) (acc : [IO.FileRight]](#manual-IO___FileRight___mk)) : [UInt32]](#manual-UInt32___ofBitVec)



[IO.FileRight.flags]](#manual-IO___FileRight___flags) (acc : [IO.FileRight]](#manual-IO___FileRight___mk)) :
  [UInt32]](#manual-UInt32___ofBitVec)
```

Converts POSIX-style file permissions to their numeric representation, with three bits each for the
owner's permissions, the group's permissions, and others' permissions.

def

```lean
[IO.setAccessRights]](#manual-IO___setAccessRights) (filename : [System.FilePath]](#manual-System___FilePath___mk)) (mode : [IO.FileRight]](#manual-IO___FileRight___mk)) :
  [IO]](#manual-IO) [Unit]](#manual-Unit)



[IO.setAccessRights]](#manual-IO___setAccessRights)
  (filename : [System.FilePath]](#manual-System___FilePath___mk))
  (mode : [IO.FileRight]](#manual-IO___FileRight___mk)) : [IO]](#manual-IO) [Unit]](#manual-Unit)
```

Sets the POSIX-style permissions for a file.

opaque

```lean
[IO.FS.removeFile]](#manual-IO___FS___removeFile) (fname : [System.FilePath]](#manual-System___FilePath___mk)) : [IO]](#manual-IO) [Unit]](#manual-Unit)



[IO.FS.removeFile]](#manual-IO___FS___removeFile)
  (fname : [System.FilePath]](#manual-System___FilePath___mk)) : [IO]](#manual-IO) [Unit]](#manual-Unit)
```

Removes (deletes) a file from the filesystem.

To remove a directory, use `[IO.FS.removeDir]](#manual-IO___FS___removeDir)` or `[IO.FS.removeDirAll]](#manual-IO___FS___removeDirAll)` instead.

opaque

```lean
[IO.FS.rename]](#manual-IO___FS___rename) (old new : [System.FilePath]](#manual-System___FilePath___mk)) : [IO]](#manual-IO) [Unit]](#manual-Unit)



[IO.FS.rename]](#manual-IO___FS___rename) (old new : [System.FilePath]](#manual-System___FilePath___mk)) :
  [IO]](#manual-IO) [Unit]](#manual-Unit)
```

Moves a file or directory `old` to the new location `new`.

This function coincides with the [POSIX `[rename]](#manual-rename)`
function](https://pubs.opengroup.org/onlinepubs/9699919799/functions/rename.html).

opaque

```lean
[IO.FS.removeDir]](#manual-IO___FS___removeDir) : [System.FilePath]](#manual-System___FilePath___mk) → [IO]](#manual-IO) [Unit]](#manual-Unit)



[IO.FS.removeDir]](#manual-IO___FS___removeDir) :
  [System.FilePath]](#manual-System___FilePath___mk) → [IO]](#manual-IO) [Unit]](#manual-Unit)
```

Removes (deletes) a directory.

Removing a directory fails if the directory is not empty. Use `[IO.FS.removeDirAll]](#manual-IO___FS___removeDirAll)` to remove
directories along with their contents.

def

```lean
[IO.FS.lines]](#manual-IO___FS___lines) (fname : [System.FilePath]](#manual-System___FilePath___mk)) : [IO]](#manual-IO) ([Array]](#manual-Array___mk) [String]](#manual-String___ofByteArray))



[IO.FS.lines]](#manual-IO___FS___lines) (fname : [System.FilePath]](#manual-System___FilePath___mk)) :
  [IO]](#manual-IO) ([Array]](#manual-Array___mk) [String]](#manual-String___ofByteArray))
```

Returns the contents of a UTF-8-encoded text file as an array of lines.

Newline markers are not included in the lines.

def

```lean
[IO.FS.withTempFile.{u_1}]](#manual-IO___FS___withTempFile) {m : Type → Type u_1} {α : Type} [[Monad]](#manual-Monad___mk) m]
  [[MonadFinally]](#manual-MonadFinally___mk) m] [[MonadLiftT]](#manual-MonadLiftT___mk) [IO]](#manual-IO) m]
  (f : [IO.FS.Handle]](#manual-IO___FS___Handle) → [System.FilePath]](#manual-System___FilePath___mk) → m α) : m α



[IO.FS.withTempFile.{u_1}]](#manual-IO___FS___withTempFile)
  {m : Type → Type u_1} {α : Type}
  [[Monad]](#manual-Monad___mk) m] [[MonadFinally]](#manual-MonadFinally___mk) m]
  [[MonadLiftT]](#manual-MonadLiftT___mk) [IO]](#manual-IO) m]
  (f :
    [IO.FS.Handle]](#manual-IO___FS___Handle) →
      [System.FilePath]](#manual-System___FilePath___mk) → m α) :
  m α
```

Creates a temporary file in the most secure manner possible and calls `f` with both a `Handle` to
the already-opened file and its path. Afterwards, the temporary file is deleted.

There are no race conditions in the file’s creation. The file is readable and writable only by the
creating user ID. Additionally on UNIX style platforms the file is executable by nobody.

Use `[IO.FS.createTempFile]](#manual-IO___FS___createTempFile)` to avoid the automatic deletion of the temporary file.

def

```lean
[IO.FS.withTempDir.{u_1}]](#manual-IO___FS___withTempDir) {m : Type → Type u_1} {α : Type} [[Monad]](#manual-Monad___mk) m]
  [[MonadFinally]](#manual-MonadFinally___mk) m] [[MonadLiftT]](#manual-MonadLiftT___mk) [IO]](#manual-IO) m] (f : [System.FilePath]](#manual-System___FilePath___mk) → m α) : m α



[IO.FS.withTempDir.{u_1}]](#manual-IO___FS___withTempDir)
  {m : Type → Type u_1} {α : Type}
  [[Monad]](#manual-Monad___mk) m] [[MonadFinally]](#manual-MonadFinally___mk) m]
  [[MonadLiftT]](#manual-MonadLiftT___mk) [IO]](#manual-IO) m]
  (f : [System.FilePath]](#manual-System___FilePath___mk) → m α) : m α
```

Creates a temporary directory in the most secure manner possible, providing its path to an `[IO]](#manual-IO)`
action. Afterwards, all files in the temporary directory are recursively deleted, regardless of how
or when they were created.

There are no race conditions in the directory’s creation. The directory is readable and writable
only by the creating user ID. Use `[IO.FS.createTempDir]](#manual-IO___FS___createTempDir)` to avoid the automatic deletion of the
directory's contents.

opaque

```lean
[IO.FS.createDirAll]](#manual-IO___FS___createDirAll) (p : [System.FilePath]](#manual-System___FilePath___mk)) : [IO]](#manual-IO) [Unit]](#manual-Unit)



[IO.FS.createDirAll]](#manual-IO___FS___createDirAll) (p : [System.FilePath]](#manual-System___FilePath___mk)) :
  [IO]](#manual-IO) [Unit]](#manual-Unit)
```

Creates a directory at the specified path, creating all missing parents as directories.

def

```lean
[IO.FS.writeBinFile]](#manual-IO___FS___writeBinFile) (fname : [System.FilePath]](#manual-System___FilePath___mk)) (content : [ByteArray]](#manual-ByteArray___mk)) :
  [IO]](#manual-IO) [Unit]](#manual-Unit)



[IO.FS.writeBinFile]](#manual-IO___FS___writeBinFile)
  (fname : [System.FilePath]](#manual-System___FilePath___mk))
  (content : [ByteArray]](#manual-ByteArray___mk)) : [IO]](#manual-IO) [Unit]](#manual-Unit)
```

Write the provided bytes to a binary file at the specified path.

def

```lean
[IO.FS.withFile]](#manual-IO___FS___withFile) {α : Type} (fn : [System.FilePath]](#manual-System___FilePath___mk)) (mode : [IO.FS.Mode]](#manual-IO___FS___Mode___read))
  (f : [IO.FS.Handle]](#manual-IO___FS___Handle) → [IO]](#manual-IO) α) : [IO]](#manual-IO) α



[IO.FS.withFile]](#manual-IO___FS___withFile) {α : Type}
  (fn : [System.FilePath]](#manual-System___FilePath___mk))
  (mode : [IO.FS.Mode]](#manual-IO___FS___Mode___read))
  (f : [IO.FS.Handle]](#manual-IO___FS___Handle) → [IO]](#manual-IO) α) : [IO]](#manual-IO) α
```

Opens the file `fn` with the specified `mode` and passes the resulting file handle to `f`.

The file handle is closed when the last reference to it is dropped. If references escape `f`, then
the file remains open even after `[IO.FS.withFile]](#manual-IO___FS___withFile)` has finished.

opaque

```lean
[IO.FS.removeDirAll]](#manual-IO___FS___removeDirAll) (p : [System.FilePath]](#manual-System___FilePath___mk)) : [IO]](#manual-IO) [Unit]](#manual-Unit)



[IO.FS.removeDirAll]](#manual-IO___FS___removeDirAll) (p : [System.FilePath]](#manual-System___FilePath___mk)) :
  [IO]](#manual-IO) [Unit]](#manual-Unit)
```

Fully remove given directory by deleting all contained files and directories in an unspecified order.
Symlinks are deleted but not followed. Fails if any contained entry cannot be deleted or was newly
created during execution.

opaque

```lean
[IO.FS.createTempFile]](#manual-IO___FS___createTempFile) : [IO]](#manual-IO) [(]](#manual-Prod___mk)[IO.FS.Handle]](#manual-IO___FS___Handle) [×]](#manual-Prod___mk) [System.FilePath]](#manual-System___FilePath___mk)[)]](#manual-Prod___mk)



[IO.FS.createTempFile]](#manual-IO___FS___createTempFile) :
  [IO]](#manual-IO) [(]](#manual-Prod___mk)[IO.FS.Handle]](#manual-IO___FS___Handle) [×]](#manual-Prod___mk) [System.FilePath]](#manual-System___FilePath___mk)[)]](#manual-Prod___mk)
```

Creates a temporary file in the most secure manner possible, returning both a `Handle` to the
already-opened file and its path.

There are no race conditions in the file’s creation. The file is readable and writable only by the
creating user ID. Additionally on UNIX style platforms the file is executable by nobody.

It is the caller's job to remove the file after use. Use `withTempFile` to ensure that the temporary
file is removed.

opaque

```lean
[IO.FS.createTempDir]](#manual-IO___FS___createTempDir) : [IO]](#manual-IO) [System.FilePath]](#manual-System___FilePath___mk)



[IO.FS.createTempDir]](#manual-IO___FS___createTempDir) : [IO]](#manual-IO) [System.FilePath]](#manual-System___FilePath___mk)
```

Creates a temporary directory in the most secure manner possible, returning the new directory's
path. There are no race conditions in the directory’s creation. The directory is readable and
writable only by the creating user ID.

It is the caller's job to remove the directory after use. Use `withTempDir` to ensure that the
temporary directory is removed.

def

```lean
[IO.FS.readFile]](#manual-IO___FS___readFile) (fname : [System.FilePath]](#manual-System___FilePath___mk)) : [IO]](#manual-IO) [String]](#manual-String___ofByteArray)



[IO.FS.readFile]](#manual-IO___FS___readFile) (fname : [System.FilePath]](#manual-System___FilePath___mk)) :
  [IO]](#manual-IO) [String]](#manual-String___ofByteArray)
```

Reads the entire contents of the UTF-8-encoded file at the given path as a `[String]](#manual-String___ofByteArray)`.

An exception is thrown if the contents of the file are not valid UTF-8. This is in addition to
exceptions that may always be thrown as a result of failing to read files.

opaque

```lean
[IO.FS.realPath]](#manual-IO___FS___realPath) (fname : [System.FilePath]](#manual-System___FilePath___mk)) : [IO]](#manual-IO) [System.FilePath]](#manual-System___FilePath___mk)



[IO.FS.realPath]](#manual-IO___FS___realPath) (fname : [System.FilePath]](#manual-System___FilePath___mk)) :
  [IO]](#manual-IO) [System.FilePath]](#manual-System___FilePath___mk)
```

Resolves a path to an absolute path that contains no '.', '..', or symbolic links.

This function coincides with the [POSIX `realpath`
function](https://pubs.opengroup.org/onlinepubs/9699919799/functions/realpath.html).

def

```lean
[IO.FS.writeFile]](#manual-IO___FS___writeFile) (fname : [System.FilePath]](#manual-System___FilePath___mk)) (content : [String]](#manual-String___ofByteArray)) : [IO]](#manual-IO) [Unit]](#manual-Unit)



[IO.FS.writeFile]](#manual-IO___FS___writeFile) (fname : [System.FilePath]](#manual-System___FilePath___mk))
  (content : [String]](#manual-String___ofByteArray)) : [IO]](#manual-IO) [Unit]](#manual-Unit)
```

Write contents of a string to a file at the specified path using UTF-8 encoding.

def

```lean
[IO.FS.readBinFile]](#manual-IO___FS___readBinFile) (fname : [System.FilePath]](#manual-System___FilePath___mk)) : [IO]](#manual-IO) [ByteArray]](#manual-ByteArray___mk)



[IO.FS.readBinFile]](#manual-IO___FS___readBinFile)
  (fname : [System.FilePath]](#manual-System___FilePath___mk)) : [IO]](#manual-IO) [ByteArray]](#manual-ByteArray___mk)
```

Reads the entire contents of the binary file at the given path as an array of bytes.

opaque

```lean
[IO.FS.createDir]](#manual-IO___FS___createDir) : [System.FilePath]](#manual-System___FilePath___mk) → [IO]](#manual-IO) [Unit]](#manual-Unit)



[IO.FS.createDir]](#manual-IO___FS___createDir) :
  [System.FilePath]](#manual-System___FilePath___mk) → [IO]](#manual-IO) [Unit]](#manual-Unit)
```

Creates a directory at the specified path. The parent directory must already exist.

Throws an exception if the directory cannot be created.

### 21.5.5. Standard I/O {#manual-stdio}

On operating systems that are derived from or inspired by Unix, *standard input*, *standard output*, and *standard error* are the names of three streams that are available in each process.
Generally, programs are expected to read from standard input, write ordinary output to the standard output, and error messages to the standard error.
By default, standard input receives input from the console, while standard output and standard error output to the console, but all three are often redirected to or from pipes or files.

Rather than providing direct access to the operating system's standard I/O facilities, Lean wraps them in `[Stream]](#manual-IO___FS___Stream___mk)`s.
Additionally, the `[IO]](#manual-IO)` monad contains special support for replacing or locally overriding them.
This extra level of indirection makes it possible to redirect input and output within a Lean program.

opaque

```lean
[IO.getStdin]](#manual-IO___getStdin) : [BaseIO]](#manual-BaseIO) [IO.FS.Stream]](#manual-IO___FS___Stream___mk)



[IO.getStdin]](#manual-IO___getStdin) : [BaseIO]](#manual-BaseIO) [IO.FS.Stream]](#manual-IO___FS___Stream___mk)
```

Returns the current thread's standard input stream.

Use `[IO.setStdin]](#manual-IO___setStdin)` to replace the current thread's standard input stream.

**Example: Reading from Standard Input**

In this example, `[IO.getStdin]](#manual-IO___getStdin)` and `[IO.getStdout]](#manual-IO___getStdout)` are used to get the current standard input and output, respectively.
These can be read from and written to.

```lean
def main : [IO]](#manual-IO) [Unit]](#manual-Unit) := [do]](#manual-Lean___Parser___Term___do)
let stdin ← [IO.getStdin]](#manual-IO___getStdin)
let stdout ← [IO.getStdout]](#manual-IO___getStdout)
stdout.[putStrLn]](#manual-IO___FS___Stream___putStrLn) "Who is it?"
let name ← stdin.[getLine]](#manual-IO___FS___Stream___mk)
stdout.[putStr]](#manual-IO___FS___Stream___mk) "Hello, "
stdout.[putStrLn]](#manual-IO___FS___Stream___putStrLn) name
```

With this standard input:

`stdin``Lean user`

the standard output is:

`stdout``Who is it?``Hello, Lean user`

opaque

```lean
[IO.setStdin]](#manual-IO___setStdin) : [IO.FS.Stream]](#manual-IO___FS___Stream___mk) → [BaseIO]](#manual-BaseIO) [IO.FS.Stream]](#manual-IO___FS___Stream___mk)



[IO.setStdin]](#manual-IO___setStdin) :
  [IO.FS.Stream]](#manual-IO___FS___Stream___mk) → [BaseIO]](#manual-BaseIO) [IO.FS.Stream]](#manual-IO___FS___Stream___mk)
```

Replaces the standard input stream of the current thread and returns its previous value.

Use `[IO.getStdin]](#manual-IO___getStdin)` to get the current standard input stream.

def

```lean
[IO.withStdin.{u_1}]](#manual-IO___withStdin) {m : Type → Type u_1} {α : Type} [[Monad]](#manual-Monad___mk) m]
  [[MonadFinally]](#manual-MonadFinally___mk) m] [[MonadLiftT]](#manual-MonadLiftT___mk) [BaseIO]](#manual-BaseIO) m] (h : [IO.FS.Stream]](#manual-IO___FS___Stream___mk)) (x : m α) :
  m α



[IO.withStdin.{u_1}]](#manual-IO___withStdin) {m : Type → Type u_1}
  {α : Type} [[Monad]](#manual-Monad___mk) m] [[MonadFinally]](#manual-MonadFinally___mk) m]
  [[MonadLiftT]](#manual-MonadLiftT___mk) [BaseIO]](#manual-BaseIO) m] (h : [IO.FS.Stream]](#manual-IO___FS___Stream___mk))
  (x : m α) : m α
```

Runs an action with the specified stream `h` as standard input, restoring the original standard
input stream afterwards.

opaque

```lean
[IO.getStdout]](#manual-IO___getStdout) : [BaseIO]](#manual-BaseIO) [IO.FS.Stream]](#manual-IO___FS___Stream___mk)



[IO.getStdout]](#manual-IO___getStdout) : [BaseIO]](#manual-BaseIO) [IO.FS.Stream]](#manual-IO___FS___Stream___mk)
```

Returns the current thread's standard output stream.

Use `[IO.setStdout]](#manual-IO___setStdout)` to replace the current thread's standard output stream.

opaque

```lean
[IO.setStdout]](#manual-IO___setStdout) : [IO.FS.Stream]](#manual-IO___FS___Stream___mk) → [BaseIO]](#manual-BaseIO) [IO.FS.Stream]](#manual-IO___FS___Stream___mk)



[IO.setStdout]](#manual-IO___setStdout) :
  [IO.FS.Stream]](#manual-IO___FS___Stream___mk) → [BaseIO]](#manual-BaseIO) [IO.FS.Stream]](#manual-IO___FS___Stream___mk)
```

Replaces the standard output stream of the current thread and returns its previous value.

Use `[IO.getStdout]](#manual-IO___getStdout)` to get the current standard output stream.

def

```lean
[IO.withStdout.{u_1}]](#manual-IO___withStdout) {m : Type → Type u_1} {α : Type} [[Monad]](#manual-Monad___mk) m]
  [[MonadFinally]](#manual-MonadFinally___mk) m] [[MonadLiftT]](#manual-MonadLiftT___mk) [BaseIO]](#manual-BaseIO) m] (h : [IO.FS.Stream]](#manual-IO___FS___Stream___mk)) (x : m α) :
  m α



[IO.withStdout.{u_1}]](#manual-IO___withStdout) {m : Type → Type u_1}
  {α : Type} [[Monad]](#manual-Monad___mk) m] [[MonadFinally]](#manual-MonadFinally___mk) m]
  [[MonadLiftT]](#manual-MonadLiftT___mk) [BaseIO]](#manual-BaseIO) m] (h : [IO.FS.Stream]](#manual-IO___FS___Stream___mk))
  (x : m α) : m α
```

Runs an action with the specified stream `h` as standard output, restoring the original standard
output stream afterwards.

opaque

```lean
[IO.getStderr]](#manual-IO___getStderr) : [BaseIO]](#manual-BaseIO) [IO.FS.Stream]](#manual-IO___FS___Stream___mk)



[IO.getStderr]](#manual-IO___getStderr) : [BaseIO]](#manual-BaseIO) [IO.FS.Stream]](#manual-IO___FS___Stream___mk)
```

Returns the current thread's standard error stream.

Use `[IO.setStderr]](#manual-IO___setStderr)` to replace the current thread's standard error stream.

opaque

```lean
[IO.setStderr]](#manual-IO___setStderr) : [IO.FS.Stream]](#manual-IO___FS___Stream___mk) → [BaseIO]](#manual-BaseIO) [IO.FS.Stream]](#manual-IO___FS___Stream___mk)



[IO.setStderr]](#manual-IO___setStderr) :
  [IO.FS.Stream]](#manual-IO___FS___Stream___mk) → [BaseIO]](#manual-BaseIO) [IO.FS.Stream]](#manual-IO___FS___Stream___mk)
```

Replaces the standard error stream of the current thread and returns its previous value.

Use `[IO.getStderr]](#manual-IO___getStderr)` to get the current standard error stream.

def

```lean
[IO.withStderr.{u_1}]](#manual-IO___withStderr) {m : Type → Type u_1} {α : Type} [[Monad]](#manual-Monad___mk) m]
  [[MonadFinally]](#manual-MonadFinally___mk) m] [[MonadLiftT]](#manual-MonadLiftT___mk) [BaseIO]](#manual-BaseIO) m] (h : [IO.FS.Stream]](#manual-IO___FS___Stream___mk)) (x : m α) :
  m α



[IO.withStderr.{u_1}]](#manual-IO___withStderr) {m : Type → Type u_1}
  {α : Type} [[Monad]](#manual-Monad___mk) m] [[MonadFinally]](#manual-MonadFinally___mk) m]
  [[MonadLiftT]](#manual-MonadLiftT___mk) [BaseIO]](#manual-BaseIO) m] (h : [IO.FS.Stream]](#manual-IO___FS___Stream___mk))
  (x : m α) : m α
```

Runs an action with the specified stream `h` as standard error, restoring the original standard
error stream afterwards.

def

```lean
[IO.FS.withIsolatedStreams.{u_1}]](#manual-IO___FS___withIsolatedStreams) {m : Type → Type u_1} {α : Type}
  [[Monad]](#manual-Monad___mk) m] [[MonadFinally]](#manual-MonadFinally___mk) m] [[MonadLiftT]](#manual-MonadLiftT___mk) [BaseIO]](#manual-BaseIO) m] (x : m α)
  (isolateStderr : [Bool]](#manual-Bool___false) := [true]](#manual-Bool___false)) : m [(]](#manual-Prod___mk)[String]](#manual-String___ofByteArray) [×]](#manual-Prod___mk) α[)]](#manual-Prod___mk)



[IO.FS.withIsolatedStreams.{u_1}]](#manual-IO___FS___withIsolatedStreams)
  {m : Type → Type u_1} {α : Type}
  [[Monad]](#manual-Monad___mk) m] [[MonadFinally]](#manual-MonadFinally___mk) m]
  [[MonadLiftT]](#manual-MonadLiftT___mk) [BaseIO]](#manual-BaseIO) m] (x : m α)
  (isolateStderr : [Bool]](#manual-Bool___false) := [true]](#manual-Bool___false)) :
  m [(]](#manual-Prod___mk)[String]](#manual-String___ofByteArray) [×]](#manual-Prod___mk) α[)]](#manual-Prod___mk)
```

Runs an action with `stdin` emptied and `stdout` and `stderr` captured into a `[String]](#manual-String___ofByteArray)`. If
`isolateStderr` is `[false]](#manual-Bool___false)`, only `stdout` is captured.

**Example: Redirecting Standard I/O to Strings**

The `[countdown]](#manual-countdown-_LPAR_in-Redirecting-Standard-I___O-to-Strings_RPAR_)` function counts down from a specified number, writing its progress to standard output.
Using `IO.FS.withIsolatedStreams`, this output can be redirected to a string.

```lean
def countdown : [Nat]](#manual-Nat___zero) → [IO]](#manual-IO) [Unit]](#manual-Unit)
| 0 =>
[IO.println]](#manual-IO___println) "Blastoff!"
| n + 1 => [do]](#manual-Lean___Parser___Term___do)
[IO.println]](#manual-IO___println) s!"{n + 1}"
[countdown]](#manual-countdown-_LPAR_in-Redirecting-Standard-I___O-to-Strings_RPAR_) n
def runCountdown : [IO]](#manual-IO) [String]](#manual-String___ofByteArray) := [do]](#manual-Lean___Parser___Term___do)
let (output, ()) ← [IO.FS.withIsolatedStreams]](#manual-IO___FS___withIsolatedStreams) ([countdown]](#manual-countdown-_LPAR_in-Redirecting-Standard-I___O-to-Strings_RPAR_) 10)
return output
[#eval]](#manual-Lean___Parser___Command___eval) [runCountdown]](#manual-runCountdown-_LPAR_in-Redirecting-Standard-I___O-to-Strings_RPAR_)
```

Running `[countdown]](#manual-countdown-_LPAR_in-Redirecting-Standard-I___O-to-Strings_RPAR_)` yields a string that contains the output:

```lean
"10\n9\n8\n7\n6\n5\n4\n3\n2\n1\nBlastoff!\n"
```

### 21.5.6. Files and Directories {#manual-The-Lean-Language-Reference--IO--Files___-File-Handles___-and-Streams--Files-and-Directories}

opaque

```lean
[IO.currentDir]](#manual-IO___currentDir) : [IO]](#manual-IO) [System.FilePath]](#manual-System___FilePath___mk)



[IO.currentDir]](#manual-IO___currentDir) : [IO]](#manual-IO) [System.FilePath]](#manual-System___FilePath___mk)
```

Returns the current working directory of the executing process.

opaque

```lean
[IO.appPath]](#manual-IO___appPath) : [IO]](#manual-IO) [System.FilePath]](#manual-System___FilePath___mk)



[IO.appPath]](#manual-IO___appPath) : [IO]](#manual-IO) [System.FilePath]](#manual-System___FilePath___mk)
```

Returns the file name of the currently-running executable.

def

```lean
[IO.appDir]](#manual-IO___appDir) : [IO]](#manual-IO) [System.FilePath]](#manual-System___FilePath___mk)



[IO.appDir]](#manual-IO___appDir) : [IO]](#manual-IO) [System.FilePath]](#manual-System___FilePath___mk)
```

Returns the directory that the current executable is located in.

---



## IO — 21.6. System and Platform Information {#manual-io-216-system-and-platform-information}

> 📄 Source: https://lean-lang.org/doc/reference/latest/IO/System-and-Platform-Information/

def

```lean
[System.Platform.numBits]](#manual-System___Platform___numBits) : [Nat]](#manual-Nat___zero)



[System.Platform.numBits]](#manual-System___Platform___numBits) : [Nat]](#manual-Nat___zero)
```

The word size of the current platform, which may be 64 or 32 bits.

def

```lean
[System.Platform.target]](#manual-System___Platform___target) : [String]](#manual-String___ofByteArray)



[System.Platform.target]](#manual-System___Platform___target) : [String]](#manual-String___ofByteArray)
```

The LLVM target triple of the current platform. Empty if missing when Lean was compiled.

def

```lean
[System.Platform.isWindows]](#manual-System___Platform___isWindows) : [Bool]](#manual-Bool___false)



[System.Platform.isWindows]](#manual-System___Platform___isWindows) : [Bool]](#manual-Bool___false)
```

Is the current platform Windows?

def

```lean
[System.Platform.isOSX]](#manual-System___Platform___isOSX) : [Bool]](#manual-Bool___false)



[System.Platform.isOSX]](#manual-System___Platform___isOSX) : [Bool]](#manual-Bool___false)
```

Is the current platform macOS?

def

```lean
[System.Platform.isEmscripten]](#manual-System___Platform___isEmscripten) : [Bool]](#manual-Bool___false)



[System.Platform.isEmscripten]](#manual-System___Platform___isEmscripten) : [Bool]](#manual-Bool___false)
```

Is the current platform [Emscripten](https://emscripten.org/)?

---



## IO — 21.7. Environment Variables {#manual-io-217-environment-variables}

> 📄 Source: https://lean-lang.org/doc/reference/latest/IO/Environment-Variables/

opaque

```lean
[IO.getEnv]](#manual-IO___getEnv) (var : [String]](#manual-String___ofByteArray)) : [BaseIO]](#manual-BaseIO) ([Option]](#manual-Option___none) [String]](#manual-String___ofByteArray))



[IO.getEnv]](#manual-IO___getEnv) (var : [String]](#manual-String___ofByteArray)) :
  [BaseIO]](#manual-BaseIO) ([Option]](#manual-Option___none) [String]](#manual-String___ofByteArray))
```

Returns the value of the environment variable `var`, or `[none]](#manual-Option___none)` if it is not present in the
environment.

---



## IO — 21.8. Timing {#manual-io-218-timing}

> 📄 Source: https://lean-lang.org/doc/reference/latest/IO/Timing/

opaque

```lean
[IO.sleep]](#manual-IO___sleep) (ms : [UInt32]](#manual-UInt32___ofBitVec)) : [BaseIO]](#manual-BaseIO) [Unit]](#manual-Unit)



[IO.sleep]](#manual-IO___sleep) (ms : [UInt32]](#manual-UInt32___ofBitVec)) : [BaseIO]](#manual-BaseIO) [Unit]](#manual-Unit)
```

Pauses execution for the specified number of milliseconds.

opaque

```lean
[IO.monoNanosNow]](#manual-IO___monoNanosNow) : [BaseIO]](#manual-BaseIO) [Nat]](#manual-Nat___zero)



[IO.monoNanosNow]](#manual-IO___monoNanosNow) : [BaseIO]](#manual-BaseIO) [Nat]](#manual-Nat___zero)
```

Monotonically increasing time since an unspecified past point in nanoseconds. There is no relation
to wall clock time.

opaque

```lean
[IO.monoMsNow]](#manual-IO___monoMsNow) : [BaseIO]](#manual-BaseIO) [Nat]](#manual-Nat___zero)



[IO.monoMsNow]](#manual-IO___monoMsNow) : [BaseIO]](#manual-BaseIO) [Nat]](#manual-Nat___zero)
```

Monotonically increasing time since an unspecified past point in milliseconds. There is no relation
to wall clock time.

opaque

```lean
[IO.getNumHeartbeats]](#manual-IO___getNumHeartbeats) : [BaseIO]](#manual-BaseIO) [Nat]](#manual-Nat___zero)



[IO.getNumHeartbeats]](#manual-IO___getNumHeartbeats) : [BaseIO]](#manual-BaseIO) [Nat]](#manual-Nat___zero)
```

Returns the number of *heartbeats* that have occurred during the current thread's execution. The
heartbeat count is the number of "small" memory allocations performed in a thread.

Heartbeats used to implement timeouts that are more deterministic across different hardware.

def

```lean
[IO.addHeartbeats]](#manual-IO___addHeartbeats) (count : [Nat]](#manual-Nat___zero)) : [BaseIO]](#manual-BaseIO) [Unit]](#manual-Unit)



[IO.addHeartbeats]](#manual-IO___addHeartbeats) (count : [Nat]](#manual-Nat___zero)) :
  [BaseIO]](#manual-BaseIO) [Unit]](#manual-Unit)
```

Adjusts the heartbeat counter of the current thread by the given amount. This can be useful to give
allocation-avoiding code additional “weight” and is also used to adjust the counter after resuming
from a snapshot.

Heartbeats are a means of implementing “deterministic” timeouts. The heartbeat counter is the number
of “small” memory allocations performed on the current execution thread.

---



## IO — 21.9. Processes {#manual-io-219-processes}

> 📄 Source: https://lean-lang.org/doc/reference/latest/IO/Processes/

### 21.9.1. Current Process {#manual-The-Lean-Language-Reference--IO--Processes--Current-Process}

opaque

```lean
[IO.Process.getCurrentDir]](#manual-IO___Process___getCurrentDir) : [IO]](#manual-IO) [System.FilePath]](#manual-System___FilePath___mk)



[IO.Process.getCurrentDir]](#manual-IO___Process___getCurrentDir) :
  [IO]](#manual-IO) [System.FilePath]](#manual-System___FilePath___mk)
```

Returns the current working directory of the calling process.

opaque

```lean
[IO.Process.setCurrentDir]](#manual-IO___Process___setCurrentDir) (path : [System.FilePath]](#manual-System___FilePath___mk)) : [IO]](#manual-IO) [Unit]](#manual-Unit)



[IO.Process.setCurrentDir]](#manual-IO___Process___setCurrentDir)
  (path : [System.FilePath]](#manual-System___FilePath___mk)) : [IO]](#manual-IO) [Unit]](#manual-Unit)
```

Sets the current working directory of the calling process.

opaque

```lean
[IO.Process.exit]](#manual-IO___Process___exit) {α : Type} : [UInt8]](#manual-UInt8___ofBitVec) → [IO]](#manual-IO) α



[IO.Process.exit]](#manual-IO___Process___exit) {α : Type} : [UInt8]](#manual-UInt8___ofBitVec) → [IO]](#manual-IO) α
```

Terminates the current process with the provided exit code. `0` indicates success, all other values
indicate failure.

opaque

```lean
[IO.Process.getPID]](#manual-IO___Process___getPID) : [BaseIO]](#manual-BaseIO) [UInt32]](#manual-UInt32___ofBitVec)



[IO.Process.getPID]](#manual-IO___Process___getPID) : [BaseIO]](#manual-BaseIO) [UInt32]](#manual-UInt32___ofBitVec)
```

Returns the process ID of the calling process.

### 21.9.2. Running Processes {#manual-The-Lean-Language-Reference--IO--Processes--Running-Processes}

There are three primary ways to run other programs from Lean:

1. `[IO.Process.run]](#manual-IO___Process___run)` synchronously executes another program, returning its standard output as a string. It throws an error if the process exits with an error code other than `0`.
2. `[IO.Process.output]](#manual-IO___Process___output)` synchronously executes another program with an empty standard input, capturing its standard output, standard error, and exit code. No error is thrown if the process terminates unsuccessfully.
3. `[IO.Process.spawn]](#manual-IO___Process___spawn)` starts another program asynchronously and returns a data structure that can be used to access the process's standard input, output, and error streams.

def

```lean
[IO.Process.run]](#manual-IO___Process___run) (args : [IO.Process.SpawnArgs]](#manual-IO___Process___SpawnArgs___mk))
  (input? : [Option]](#manual-Option___none) [String]](#manual-String___ofByteArray) := [none]](#manual-Option___none)) : [IO]](#manual-IO) [String]](#manual-String___ofByteArray)



[IO.Process.run]](#manual-IO___Process___run)
  (args : [IO.Process.SpawnArgs]](#manual-IO___Process___SpawnArgs___mk))
  (input? : [Option]](#manual-Option___none) [String]](#manual-String___ofByteArray) := [none]](#manual-Option___none)) :
  [IO]](#manual-IO) [String]](#manual-String___ofByteArray)
```

Runs a process to completion, blocking until it terminates.
The child process is run with a null standard input or the specified input if provided,
If the child process terminates successfully with exit code 0, its standard output is returned.
An exception is thrown if it terminates with any other exit code.

The specifications of standard input, output, and error handles in `args` are ignored.

**Example: Running a Program**

When run, this program concatenates its own source code with itself twice using the Unix tool `cat`.

```lean
-- Main.lean begins here
def main : [IO]](#manual-IO) [Unit]](#manual-Unit) := [do]](#manual-Lean___Parser___Term___do)
let src2 ← [IO.Process.run]](#manual-IO___Process___run) {[cmd]](#manual-IO___Process___SpawnArgs___mk) := "cat", [args]](#manual-IO___Process___SpawnArgs___mk) := #["Main.lean", "Main.lean"]}
[IO.println]](#manual-IO___println) src2
-- Main.lean ends here
```

Its output is:

`stdout``-- Main.lean begins here``def main : IO Unit := do`` let src2 ← IO.Process.run {cmd := "cat", args := #["Main.lean", "Main.lean"]}`` IO.println src2``-- Main.lean ends here``-- Main.lean begins here``def main : IO Unit := do`` let src2 ← IO.Process.run {cmd := "cat", args := #["Main.lean", "Main.lean"]}`` IO.println src2``-- Main.lean ends here`

**Example: Running a Program on a File**

This program uses the Unix utility `grep` as a filter to find four-digit palindromes.
It creates a file that contains all numbers from `0` through `9999`, and then invokes `grep` on it, reading the result from its standard output.

```lean
def main : [IO]](#manual-IO) [Unit]](#manual-Unit) := [do]](#manual-Lean___Parser___Term___do)
-- Feed the input to the subprocess
[IO.FS.withFile]](#manual-IO___FS___withFile) "numbers.txt" [.write]](#manual-IO___FS___Mode___read) fun h =>
for i in [0:10000] do
h.[putStrLn]](#manual-IO___FS___Handle___putStrLn) (toString i)
let palindromes ← [IO.Process.run]](#manual-IO___Process___run) {
[cmd]](#manual-IO___Process___SpawnArgs___mk) := "grep",
[args]](#manual-IO___Process___SpawnArgs___mk) := #[r#"^\([0-9]\)\([0-9]\)\2\1$"#, "numbers.txt"]
}
let count := palindromes.[trimAscii]](#manual-String___trimAscii).[split]](#manual-String___Slice___split) "\n" |>.[length](https://lean-lang.org/doc/reference/latest/Iterators/Consuming-Iterators/#Std___Iter___length)
[IO.println]](#manual-IO___println) s!"There are {count} four-digit palindromes."
```

Its output is:

`stdout``There are 90 four-digit palindromes.`

def

```lean
[IO.Process.output]](#manual-IO___Process___output) (args : [IO.Process.SpawnArgs]](#manual-IO___Process___SpawnArgs___mk))
  (input? : [Option]](#manual-Option___none) [String]](#manual-String___ofByteArray) := [none]](#manual-Option___none)) : [IO]](#manual-IO) [IO.Process.Output]](#manual-IO___Process___Output___mk)



[IO.Process.output]](#manual-IO___Process___output)
  (args : [IO.Process.SpawnArgs]](#manual-IO___Process___SpawnArgs___mk))
  (input? : [Option]](#manual-Option___none) [String]](#manual-String___ofByteArray) := [none]](#manual-Option___none)) :
  [IO]](#manual-IO) [IO.Process.Output]](#manual-IO___Process___Output___mk)
```

Runs a process to completion and captures its output and exit code.
The child process is run with a null standard input or the specified input if provided,
and the current process blocks until it has run to completion.

The specifications of standard input, output, and error handles in `args` are ignored.

**Example: Checking Exit Codes**

When run, this program first invokes `cat` on a nonexistent file and displays the resulting error code.
It then concatenates its own source code with itself twice using the Unix tool `cat`.

```lean
-- Main.lean begins here
def main : [IO]](#manual-IO) [UInt32]](#manual-UInt32___ofBitVec) := [do]](#manual-Lean___Parser___Term___do)
let src1 ← [IO.Process.output]](#manual-IO___Process___output) {[cmd]](#manual-IO___Process___SpawnArgs___mk) := "cat", [args]](#manual-IO___Process___SpawnArgs___mk) := #["Nonexistent.lean"]}
[IO.println]](#manual-IO___println) s!"Exit code from failed process: {src1.[exitCode]](#manual-IO___Process___Output___mk)}"
let src2 ← [IO.Process.output]](#manual-IO___Process___output) {[cmd]](#manual-IO___Process___SpawnArgs___mk) := "cat", [args]](#manual-IO___Process___SpawnArgs___mk) := #["Main.lean", "Main.lean"]}
if src2.[exitCode]](#manual-IO___Process___Output___mk) == 0 then
[IO.println]](#manual-IO___println) src2.[stdout]](#manual-IO___Process___Output___mk)
else
[IO.eprintln]](#manual-IO___eprintln) "Concatenation failed"
return 1
return 0
-- Main.lean ends here
```

Its output is:

`stdout``Exit code from failed process: 1``-- Main.lean begins here``def main : IO UInt32 := do`` let src1 ← IO.Process.output {cmd := "cat", args := #["Nonexistent.lean"]}`` IO.println s!"Exit code from failed process: {src1.exitCode}"```` let src2 ← IO.Process.output {cmd := "cat", args := #["Main.lean", "Main.lean"]}`` if src2.exitCode == 0 then`` IO.println src2.stdout`` else`` IO.eprintln "Concatenation failed"`` return 1```` return 0``-- Main.lean ends here``-- Main.lean begins here``def main : IO UInt32 := do`` let src1 ← IO.Process.output {cmd := "cat", args := #["Nonexistent.lean"]}`` IO.println s!"Exit code from failed process: {src1.exitCode}"```` let src2 ← IO.Process.output {cmd := "cat", args := #["Main.lean", "Main.lean"]}`` if src2.exitCode == 0 then`` IO.println src2.stdout`` else`` IO.eprintln "Concatenation failed"`` return 1```` return 0``-- Main.lean ends here```

opaque

```lean
[IO.Process.spawn]](#manual-IO___Process___spawn) (args : [IO.Process.SpawnArgs]](#manual-IO___Process___SpawnArgs___mk)) :
  [IO]](#manual-IO) ([IO.Process.Child]](#manual-IO___Process___Child___stdin) args.[toStdioConfig]](#manual-IO___Process___SpawnArgs___mk))



[IO.Process.spawn]](#manual-IO___Process___spawn)
  (args : [IO.Process.SpawnArgs]](#manual-IO___Process___SpawnArgs___mk)) :
  [IO]](#manual-IO) ([IO.Process.Child]](#manual-IO___Process___Child___stdin) args.[toStdioConfig]](#manual-IO___Process___SpawnArgs___mk))
```

Starts a child process with the provided configuration. The child process is spawned using operating
system primitives, and it can be written in any language.

The child process runs in parallel with the parent.

If the child process's standard input is a pipe, use `[IO.Process.Child.takeStdin]](#manual-IO___Process___Child___takeStdin)` to make it
possible to close the child's standard input before the process terminates, which provides the child with an end-of-file marker.

**Example: Asynchronous Subprocesses**

This program uses the Unix utility `grep` as a filter to find four-digit palindromes.
It feeds all numbers from `0` through `9999` to the `grep` process and then reads its result.
This code is only correct when `grep` is sufficiently fast and when the output pipe is large enough to contain all 90 four-digit palindromes.

```lean
def main : [IO]](#manual-IO) [Unit]](#manual-Unit) := [do]](#manual-Lean___Parser___Term___do)
let grep ← [IO.Process.spawn]](#manual-IO___Process___spawn) {
[cmd]](#manual-IO___Process___SpawnArgs___mk) := "grep",
[args]](#manual-IO___Process___SpawnArgs___mk) := #[r#"^\([0-9]\)\([0-9]\)\2\1$"#],
[stdin]](#manual-IO___Process___StdioConfig___mk) := [.piped]](#manual-IO___Process___Stdio___piped),
[stdout]](#manual-IO___Process___StdioConfig___mk) := [.piped]](#manual-IO___Process___Stdio___piped),
[stderr]](#manual-IO___Process___StdioConfig___mk) := [.null]](#manual-IO___Process___Stdio___piped)
}
-- Feed the input to the subprocess
for i in [0:10000] do
grep.[stdin]](#manual-IO___Process___Child___stdin).[putStrLn]](#manual-IO___FS___Handle___putStrLn) (toString i)
-- Consume its output, after waiting 100ms for grep to process the data.
[IO.sleep]](#manual-IO___sleep) 100
let count := (← grep.[stdout]](#manual-IO___Process___Child___stdin).[readToEnd]](#manual-IO___FS___Handle___readToEnd)).[trimAscii]](#manual-String___trimAscii).[split]](#manual-String___Slice___split) "\n" |>.[length](https://lean-lang.org/doc/reference/latest/Iterators/Consuming-Iterators/#Std___Iter___length)
[IO.println]](#manual-IO___println) s!"There are {count} four-digit palindromes."
```

Its output is:

`stdout``There are 90 four-digit palindromes.`

structure

```lean
[IO.Process.SpawnArgs]](#manual-IO___Process___SpawnArgs___mk) : Type



[IO.Process.SpawnArgs]](#manual-IO___Process___SpawnArgs___mk) : Type
```

Configuration for a child process to be spawned.

Use `[IO.Process.spawn]](#manual-IO___Process___spawn)` to start the child process. `[IO.Process.output]](#manual-IO___Process___output)` and `[IO.Process.run]](#manual-IO___Process___run)` can be
used when the child process should be run to completion, with its output and/or error code captured.

Constructor

```lean
[IO.Process.SpawnArgs.mk]](#manual-IO___Process___SpawnArgs___mk)
```

Extends

- `[IO.Process.StdioConfig]](#manual-IO___Process___StdioConfig___mk)`

Fields

```lean
stdin : [IO.Process.Stdio]](#manual-IO___Process___Stdio___piped)
```

Inherited from

1. `[IO.Process.StdioConfig]](#manual-IO___Process___StdioConfig___mk)`

```lean
stdout : [IO.Process.Stdio]](#manual-IO___Process___Stdio___piped)
```

Inherited from

1. `[IO.Process.StdioConfig]](#manual-IO___Process___StdioConfig___mk)`

```lean
stderr : [IO.Process.Stdio]](#manual-IO___Process___Stdio___piped)
```

Inherited from

1. `[IO.Process.StdioConfig]](#manual-IO___Process___StdioConfig___mk)`

```lean
cmd : [String]](#manual-String___ofByteArray)
```

Command name.

```lean
args : [Array]](#manual-Array___mk) [String]](#manual-String___ofByteArray)
```

Arguments for the command.

```lean
cwd : [Option]](#manual-Option___none) [System.FilePath]](#manual-System___FilePath___mk)
```

The child process's working directory. Inherited from the parent current process if `[none]](#manual-Option___none)`.

```lean
env : [Array]](#manual-Array___mk) [(]](#manual-Prod___mk)[String]](#manual-String___ofByteArray) [×]](#manual-Prod___mk) [Option]](#manual-Option___none) [String]](#manual-String___ofByteArray)[)]](#manual-Prod___mk)
```

Add or remove environment variables for the child process.

The child process inherits the parent's environment, as modified by `env`. Keys in the array are
the names of environment variables. A `[none]](#manual-Option___none)`, causes the entry to be removed from the environment,
and `[some]](#manual-Option___none)` sets the variable to the new value, adding it if necessary. Variables are processed from left to right.

```lean
inheritEnv : [Bool]](#manual-Bool___false)
```

Inherit environment variables from the spawning process.

```lean
setsid : [Bool]](#manual-Bool___false)
```

Starts the child process in a new session and process group using `setsid`. Currently a no-op on
non-POSIX platforms.

structure

```lean
[IO.Process.StdioConfig]](#manual-IO___Process___StdioConfig___mk) : Type



[IO.Process.StdioConfig]](#manual-IO___Process___StdioConfig___mk) : Type
```

Configuration for the standard input, output, and error handles of a child process.

Constructor

```lean
[IO.Process.StdioConfig.mk]](#manual-IO___Process___StdioConfig___mk)
```

Fields

```lean
stdin : [IO.Process.Stdio]](#manual-IO___Process___Stdio___piped)
```

Configuration for the process' stdin handle.

```lean
stdout : [IO.Process.Stdio]](#manual-IO___Process___Stdio___piped)
```

Configuration for the process' stdout handle.

```lean
stderr : [IO.Process.Stdio]](#manual-IO___Process___Stdio___piped)
```

Configuration for the process' stderr handle.

inductive type

```lean
[IO.Process.Stdio]](#manual-IO___Process___Stdio___piped) : Type



[IO.Process.Stdio]](#manual-IO___Process___Stdio___piped) : Type
```

Whether the standard input, output, and error handles of a child process should be attached to
pipes, inherited from the parent, or null.

If the stream is a pipe, then the parent process can use it to communicate with the child.

Constructors

```lean
[IO.Process.Stdio.piped]](#manual-IO___Process___Stdio___piped) : [IO.Process.Stdio]](#manual-IO___Process___Stdio___piped)
```

The stream should be attached to a pipe.

```lean
[IO.Process.Stdio.inherit]](#manual-IO___Process___Stdio___piped) : [IO.Process.Stdio]](#manual-IO___Process___Stdio___piped)
```

The stream should be inherited from the parent process.

```lean
[IO.Process.Stdio.null]](#manual-IO___Process___Stdio___piped) : [IO.Process.Stdio]](#manual-IO___Process___Stdio___piped)
```

The stream should be empty.

def

```lean
[IO.Process.Stdio.toHandleType]](#manual-IO___Process___Stdio___toHandleType) : [IO.Process.Stdio]](#manual-IO___Process___Stdio___piped) → Type



[IO.Process.Stdio.toHandleType]](#manual-IO___Process___Stdio___toHandleType) :
  [IO.Process.Stdio]](#manual-IO___Process___Stdio___piped) → Type
```

The type of handles that can be used to communicate with a child process on its standard input,
output, or error streams.

For `[IO.Process.Stdio.piped]](#manual-IO___Process___Stdio___piped)`, this type is `[IO.FS.Handle]](#manual-IO___FS___Handle)`. Otherwise, it is `[Unit]](#manual-Unit)`, because no
communication is possible.

structure

```lean
[IO.Process.Child]](#manual-IO___Process___Child___stdin) (cfg : [IO.Process.StdioConfig]](#manual-IO___Process___StdioConfig___mk)) : Type



[IO.Process.Child]](#manual-IO___Process___Child___stdin)
  (cfg : [IO.Process.StdioConfig]](#manual-IO___Process___StdioConfig___mk)) : Type
```

A child process that was spawned with configuration `cfg`.

The configuration determines whether the child process's standard input, standard output, and
standard error are `[IO.FS.Handle]](#manual-IO___FS___Handle)`s or `[Unit]](#manual-Unit)`.

Fields

```lean
stdin : cfg.[stdin]](#manual-IO___Process___StdioConfig___mk).[toHandleType]](#manual-IO___Process___Stdio___toHandleType)
```

The child process's standard input handle, if it was configured as `[IO.Process.Stdio.piped]](#manual-IO___Process___Stdio___piped)`, or
`()` otherwise.

```lean
stdout : cfg.[stdout]](#manual-IO___Process___StdioConfig___mk).[toHandleType]](#manual-IO___Process___Stdio___toHandleType)
```

The child process's standard output handle, if it was configured as `[IO.Process.Stdio.piped]](#manual-IO___Process___Stdio___piped)`, or
`()` otherwise.

```lean
stderr : cfg.[stderr]](#manual-IO___Process___StdioConfig___mk).[toHandleType]](#manual-IO___Process___Stdio___toHandleType)
```

The child process's standard error handle, if it was configured as `[IO.Process.Stdio.piped]](#manual-IO___Process___Stdio___piped)`, or
`()` otherwise.

opaque

```lean
[IO.Process.Child.wait]](#manual-IO___Process___Child___wait) {cfg : [IO.Process.StdioConfig]](#manual-IO___Process___StdioConfig___mk)} :
  [IO.Process.Child]](#manual-IO___Process___Child___stdin) cfg → [IO]](#manual-IO) [UInt32]](#manual-UInt32___ofBitVec)



[IO.Process.Child.wait]](#manual-IO___Process___Child___wait)
  {cfg : [IO.Process.StdioConfig]](#manual-IO___Process___StdioConfig___mk)} :
  [IO.Process.Child]](#manual-IO___Process___Child___stdin) cfg → [IO]](#manual-IO) [UInt32]](#manual-UInt32___ofBitVec)
```

Blocks until the child process has exited and returns its exit code.

opaque

```lean
[IO.Process.Child.tryWait]](#manual-IO___Process___Child___tryWait) {cfg : [IO.Process.StdioConfig]](#manual-IO___Process___StdioConfig___mk)} :
  [IO.Process.Child]](#manual-IO___Process___Child___stdin) cfg → [IO]](#manual-IO) ([Option]](#manual-Option___none) [UInt32]](#manual-UInt32___ofBitVec))



[IO.Process.Child.tryWait]](#manual-IO___Process___Child___tryWait)
  {cfg : [IO.Process.StdioConfig]](#manual-IO___Process___StdioConfig___mk)} :
  [IO.Process.Child]](#manual-IO___Process___Child___stdin) cfg →
    [IO]](#manual-IO) ([Option]](#manual-Option___none) [UInt32]](#manual-UInt32___ofBitVec))
```

Checks whether the child has exited. Returns `[none]](#manual-Option___none)` if the process has not exited, or its exit code
if it has.

opaque

```lean
[IO.Process.Child.kill]](#manual-IO___Process___Child___kill) {cfg : [IO.Process.StdioConfig]](#manual-IO___Process___StdioConfig___mk)} :
  [IO.Process.Child]](#manual-IO___Process___Child___stdin) cfg → [IO]](#manual-IO) [Unit]](#manual-Unit)



[IO.Process.Child.kill]](#manual-IO___Process___Child___kill)
  {cfg : [IO.Process.StdioConfig]](#manual-IO___Process___StdioConfig___mk)} :
  [IO.Process.Child]](#manual-IO___Process___Child___stdin) cfg → [IO]](#manual-IO) [Unit]](#manual-Unit)
```

Terminates the child process using the `SIGTERM` signal or a platform analogue.

If the process was started using `SpawnArgs.setsid`, terminates the entire process group instead.

opaque

```lean
[IO.Process.Child.takeStdin]](#manual-IO___Process___Child___takeStdin) {cfg : [IO.Process.StdioConfig]](#manual-IO___Process___StdioConfig___mk)} :
  [IO.Process.Child]](#manual-IO___Process___Child___stdin) cfg →
    [IO]](#manual-IO)
      [(]](#manual-Prod___mk)cfg.[stdin]](#manual-IO___Process___StdioConfig___mk).[toHandleType]](#manual-IO___Process___Stdio___toHandleType) [×]](#manual-Prod___mk)
        [IO.Process.Child]](#manual-IO___Process___Child___stdin)
          [{]](#manual-IO___Process___StdioConfig___mk) [stdin]](#manual-IO___Process___StdioConfig___mk) [:=]](#manual-IO___Process___StdioConfig___mk) [IO.Process.Stdio.null]](#manual-IO___Process___Stdio___piped)[,]](#manual-IO___Process___StdioConfig___mk) [stdout]](#manual-IO___Process___StdioConfig___mk) [:=]](#manual-IO___Process___StdioConfig___mk) cfg.[stdout]](#manual-IO___Process___StdioConfig___mk)[,]](#manual-IO___Process___StdioConfig___mk)
            [stderr]](#manual-IO___Process___StdioConfig___mk) [:=]](#manual-IO___Process___StdioConfig___mk) cfg.[stderr]](#manual-IO___Process___StdioConfig___mk) [}]](#manual-IO___Process___StdioConfig___mk)[)]](#manual-Prod___mk)



[IO.Process.Child.takeStdin]](#manual-IO___Process___Child___takeStdin)
  {cfg : [IO.Process.StdioConfig]](#manual-IO___Process___StdioConfig___mk)} :
  [IO.Process.Child]](#manual-IO___Process___Child___stdin) cfg →
    [IO]](#manual-IO)
      [(]](#manual-Prod___mk)cfg.[stdin]](#manual-IO___Process___StdioConfig___mk).[toHandleType]](#manual-IO___Process___Stdio___toHandleType) [×]](#manual-Prod___mk)
        [IO.Process.Child]](#manual-IO___Process___Child___stdin)
          [{]](#manual-IO___Process___StdioConfig___mk)
            [stdin]](#manual-IO___Process___StdioConfig___mk) [:=]](#manual-IO___Process___StdioConfig___mk)
              [IO.Process.Stdio.null]](#manual-IO___Process___Stdio___piped)[,]](#manual-IO___Process___StdioConfig___mk)
            [stdout]](#manual-IO___Process___StdioConfig___mk) [:=]](#manual-IO___Process___StdioConfig___mk) cfg.[stdout]](#manual-IO___Process___StdioConfig___mk)[,]](#manual-IO___Process___StdioConfig___mk)
            [stderr]](#manual-IO___Process___StdioConfig___mk) [:=]](#manual-IO___Process___StdioConfig___mk) cfg.[stderr]](#manual-IO___Process___StdioConfig___mk) [}]](#manual-IO___Process___StdioConfig___mk)[)]](#manual-Prod___mk)
```

Extracts the `stdin` field from a `Child` object, allowing the handle to be closed while maintaining
a reference to the child process.

File handles are closed when the last reference to them is dropped. Closing the child's standard
input causes an end-of-file marker. Because the `Child` object has a reference to the standard
input, this operation is necessary in order to close the stream while the process is running (e.g.
to extract its exit code after calling `Child.wait`). Many processes do not terminate until their
standard input is exhausted.

**Example: Closing a Subprocess's Standard Input**

This program uses the Unix utility `grep` as a filter to find four-digit palindromes, ensuring that the subprocess terminates successfully.
It feeds all numbers from `0` through `9999` to the `grep` process, then closes the process's standard input, which causes it to terminate.
After checking `grep`'s exit code, the program extracts its result.

```lean
def main : [IO]](#manual-IO) [UInt32]](#manual-UInt32___ofBitVec) := [do]](#manual-Lean___Parser___Term___do)
let grep ← do
let (stdin, child) ← (← [IO.Process.spawn]](#manual-IO___Process___spawn) {
[cmd]](#manual-IO___Process___SpawnArgs___mk) := "grep",
[args]](#manual-IO___Process___SpawnArgs___mk) := #[r#"^\([0-9]\)\([0-9]\)\2\1$"#],
[stdin]](#manual-IO___Process___StdioConfig___mk) := [.piped]](#manual-IO___Process___Stdio___piped),
[stdout]](#manual-IO___Process___StdioConfig___mk) := [.piped]](#manual-IO___Process___Stdio___piped),
[stderr]](#manual-IO___Process___StdioConfig___mk) := [.null]](#manual-IO___Process___Stdio___piped)
}).[takeStdin]](#manual-IO___Process___Child___takeStdin)
-- Feed the input to the subprocess
for i in [0:10000] do
stdin.[putStrLn]](#manual-IO___FS___Handle___putStrLn) (toString i)
-- Return the child without its stdin handle.
-- This closes the handle, because there are
-- no more references to it.
[pure]](#manual-Pure___mk) child
-- Wait for grep to terminate
if (← grep.[wait]](#manual-IO___Process___Child___wait)) != 0 then
[IO.eprintln]](#manual-IO___eprintln) s!"grep terminated unsuccessfully"
return 1
-- Consume its output
let count := (← grep.[stdout]](#manual-IO___Process___Child___stdin).[readToEnd]](#manual-IO___FS___Handle___readToEnd)).[trimAscii]](#manual-String___trimAscii).[split]](#manual-String___Slice___split) "\n" |>.[length](https://lean-lang.org/doc/reference/latest/Iterators/Consuming-Iterators/#Std___Iter___length)
[IO.println]](#manual-IO___println) s!"There are {count} four-digit palindromes."
return 0
```

Its output is:

`stdout``There are 90 four-digit palindromes.`

structure

```lean
[IO.Process.Output]](#manual-IO___Process___Output___mk) : Type



[IO.Process.Output]](#manual-IO___Process___Output___mk) : Type
```

The result of running a process to completion.

Constructor

```lean
[IO.Process.Output.mk]](#manual-IO___Process___Output___mk)
```

Fields

```lean
exitCode : [UInt32]](#manual-UInt32___ofBitVec)
```

The process's exit code.

```lean
stdout : [String]](#manual-String___ofByteArray)
```

Everything that was written to the process's standard output.

```lean
stderr : [String]](#manual-String___ofByteArray)
```

Everything that was written to the process's standard error.

---



## IO — 21.10. Random Numbers {#manual-io-2110-random-numbers}

> 📄 Source: https://lean-lang.org/doc/reference/latest/IO/Random-Numbers/

def

```lean
[IO.setRandSeed]](#manual-IO___setRandSeed) (n : [Nat]](#manual-Nat___zero)) : [BaseIO]](#manual-BaseIO) [Unit]](#manual-Unit)



[IO.setRandSeed]](#manual-IO___setRandSeed) (n : [Nat]](#manual-Nat___zero)) : [BaseIO]](#manual-BaseIO) [Unit]](#manual-Unit)
```

Seeds the random number generator state used by `[IO.rand]](#manual-IO___rand)`.

def

```lean
[IO.rand]](#manual-IO___rand) (lo hi : [Nat]](#manual-Nat___zero)) : [BaseIO]](#manual-BaseIO) [Nat]](#manual-Nat___zero)



[IO.rand]](#manual-IO___rand) (lo hi : [Nat]](#manual-Nat___zero)) : [BaseIO]](#manual-BaseIO) [Nat]](#manual-Nat___zero)
```

Returns a pseudorandom number between `lo` and `hi`, using and updating a saved random generator
state.

This state can be seeded using `[IO.setRandSeed]](#manual-IO___setRandSeed)`.

def

```lean
[randBool.{u}]](#manual-randBool) {gen : Type u} [[RandomGen]](#manual-RandomGen___mk) gen] (g : gen) : [Bool]](#manual-Bool___false) [×]](#manual-Prod___mk) gen



[randBool.{u}]](#manual-randBool) {gen : Type u}
  [[RandomGen]](#manual-RandomGen___mk) gen] (g : gen) : [Bool]](#manual-Bool___false) [×]](#manual-Prod___mk) gen
```

Generates a random Boolean.

def

```lean
[randNat.{u}]](#manual-randNat) {gen : Type u} [[RandomGen]](#manual-RandomGen___mk) gen] (g : gen) (lo hi : [Nat]](#manual-Nat___zero)) :
  [Nat]](#manual-Nat___zero) [×]](#manual-Prod___mk) gen



[randNat.{u}]](#manual-randNat) {gen : Type u} [[RandomGen]](#manual-RandomGen___mk) gen]
  (g : gen) (lo hi : [Nat]](#manual-Nat___zero)) : [Nat]](#manual-Nat___zero) [×]](#manual-Prod___mk) gen
```

Generates a random natural number in the interval [lo, hi].

### 21.10.1. Random Generators {#manual-The-Lean-Language-Reference--IO--Random-Numbers--Random-Generators}

type class

```lean
[RandomGen.{u}]](#manual-RandomGen___mk) (g : Type u) : Type u



[RandomGen.{u}]](#manual-RandomGen___mk) (g : Type u) : Type u
```

Interface for random number generators.

Instance Constructor

```lean
[RandomGen.mk]](#manual-RandomGen___mk).{u}
```

Methods

```lean
range : g → [Nat]](#manual-Nat___zero) [×]](#manual-Prod___mk) [Nat]](#manual-Nat___zero)
```

`range` returns the range of values returned by
the generator.

```lean
next : g → [Nat]](#manual-Nat___zero) [×]](#manual-Prod___mk) g
```

`next` operation returns a natural number that is uniformly distributed
the range returned by `range` (including both end points),
and a new generator.

```lean
split : g → g [×]](#manual-Prod___mk) g
```

The 'split' operation allows one to obtain two distinct random number
generators. This is very useful in functional programs (for example, when
passing a random number generator down to recursive calls).

structure

```lean
[StdGen]](#manual-StdGen) : Type



[StdGen]](#manual-StdGen) : Type
```

"Standard" random number generator.

def

```lean
[stdRange]](#manual-stdRange) : [Nat]](#manual-Nat___zero) [×]](#manual-Prod___mk) [Nat]](#manual-Nat___zero)



[stdRange]](#manual-stdRange) : [Nat]](#manual-Nat___zero) [×]](#manual-Prod___mk) [Nat]](#manual-Nat___zero)
```

The range of values returned by `[StdGen]](#manual-StdGen)`

def

```lean
[stdNext]](#manual-stdNext) : [StdGen]](#manual-StdGen) → [Nat]](#manual-Nat___zero) [×]](#manual-Prod___mk) [StdGen]](#manual-StdGen)



[stdNext]](#manual-stdNext) : [StdGen]](#manual-StdGen) → [Nat]](#manual-Nat___zero) [×]](#manual-Prod___mk) [StdGen]](#manual-StdGen)
```

The next value from a `[StdGen]](#manual-StdGen)`, paired with an updated generator state.

def

```lean
[stdSplit]](#manual-stdSplit) : [StdGen]](#manual-StdGen) → [StdGen]](#manual-StdGen) [×]](#manual-Prod___mk) [StdGen]](#manual-StdGen)



[stdSplit]](#manual-stdSplit) : [StdGen]](#manual-StdGen) → [StdGen]](#manual-StdGen) [×]](#manual-Prod___mk) [StdGen]](#manual-StdGen)
```

Splits a `[StdGen]](#manual-StdGen)` into two separate states.

def

```lean
[mkStdGen]](#manual-mkStdGen) (s : [Nat]](#manual-Nat___zero) := 0) : [StdGen]](#manual-StdGen)



[mkStdGen]](#manual-mkStdGen) (s : [Nat]](#manual-Nat___zero) := 0) : [StdGen]](#manual-StdGen)
```

Returns a standard number generator.

### 21.10.2. System Randomness {#manual-The-Lean-Language-Reference--IO--Random-Numbers--System-Randomness}

opaque

```lean
[IO.getRandomBytes]](#manual-IO___getRandomBytes) (nBytes : [USize]](#manual-USize___ofBitVec)) : [IO]](#manual-IO) [ByteArray]](#manual-ByteArray___mk)



[IO.getRandomBytes]](#manual-IO___getRandomBytes) (nBytes : [USize]](#manual-USize___ofBitVec)) :
  [IO]](#manual-IO) [ByteArray]](#manual-ByteArray___mk)
```

Reads bytes from a system entropy source. It is not guaranteed to be cryptographically secure.

If `nBytes` is `0`, returns immediately with an empty buffer.

---



## IO — 21.11. Tasks and Threads {#manual-io-2111-tasks-and-threads}

> 📄 Source: https://lean-lang.org/doc/reference/latest/IO/Tasks-and-Threads/

*Tasks* are the fundamental primitive for writing multi-threaded code.
A `[Task]](#manual-Task) α` represents a computation that, at some point, will [*resolve*]](#manual---tech-term-resolving-next) to a value of type `α`; it may be computed on a separate thread.
When a task has resolved, its value can be read; attempting to get the value of a task before it resolves causes the current thread to block until the task has resolved.
Tasks are similar to promises in JavaScript, `JoinHandle` in Rust, and `Future` in Scala.

Tasks may either carry out pure computations or `[IO]](#manual-IO)` actions.
The API of pure tasks resembles that of [thunks]](#manual---tech-term-thunk): `[Task.spawn]](#manual-Task___spawn)` creates a `[Task]](#manual-Task) α` from a function in `[Unit]](#manual-Unit) → α`, and `[Task.get]](#manual-Task___get)` waits until the function's value has been computed and then returns it.
The value is cached, so subsequent requests do not need to recompute it.
The key difference lies in when the computation occurs: while the values of thunks are not computed until they are forced, tasks execute opportunistically in a separate thread.

Tasks in `[IO]](#manual-IO)` are created using `[IO.asTask]](#manual-IO___asTask)`.
Similarly, `[BaseIO.asTask]](#manual-BaseIO___asTask)` and `[EIO.asTask]](#manual-EIO___asTask)` create tasks in other `[IO]](#manual-IO)` monads.
These tasks may have side effects, and can communicate with other tasks.

When the last reference to a task is dropped it is *cancelled*.
Pure tasks created with `[Task.spawn]](#manual-Task___spawn)` are terminated upon cancellation.
Tasks spawned with `[IO.asTask]](#manual-IO___asTask)`, `[EIO.asTask]](#manual-EIO___asTask)`, or `[BaseIO.asTask]](#manual-BaseIO___asTask)` continue executing and must explicitly check for cancellation using `[IO.checkCanceled]](#manual-IO___checkCanceled)`.
Tasks may be explicitly cancelled using `[IO.cancel]](#manual-IO___cancel)`.

The Lean runtime maintains a thread pool for running tasks.
The size of the thread pool is determined by the environment variable `LEAN_NUM_THREADS` if it is set, or by the number of logical processors on the current machine otherwise.
The size of the thread pool is not a hard limit; in certain situations it may be exceeded to avoid deadlocks.
By default, these threads are used to run tasks; each task has a *priority* (`[Task.Priority]](#manual-Task___Priority)`), and higher-priority tasks take precedence over lower-priority tasks.
Tasks may also be assigned to dedicated threads by spawning them with a sufficiently high priority.

type

```lean
[Task.{u}]](#manual-Task) (α : Type u) : Type u



[Task.{u}]](#manual-Task) (α : Type u) : Type u
```

`[Task]](#manual-Task) α` is a primitive for asynchronous computation.
It represents a computation that will resolve to a value of type `α`,
possibly being computed on another thread. This is similar to `Future` in Scala,
`Promise` in Javascript, and `JoinHandle` in Rust.

The tasks have an overridden representation in the runtime.

### 21.11.1. Creating Tasks {#manual-The-Lean-Language-Reference--IO--Tasks-and-Threads--Creating-Tasks}

Pure tasks should typically be created with `[Task.spawn]](#manual-Task___spawn)`, as `[Task.pure]](#manual-Task___pure)` is a task that's already been resolved with the provided value.
Impure tasks are created by one of the `[asTask]](#manual-BaseIO___asTask)` actions.

#### 21.11.1.1. Pure Tasks {#manual-The-Lean-Language-Reference--IO--Tasks-and-Threads--Creating-Tasks--Pure-Tasks}

Pure tasks may be created outside the `[IO]](#manual-IO)` family of monads.
They are terminated when the last reference to them is dropped.

def

```lean
[Task.spawn.{u}]](#manual-Task___spawn) {α : Type u} (fn : [Unit]](#manual-Unit) → α)
  (prio : [Task.Priority]](#manual-Task___Priority) := [Task.Priority.default]](#manual-Task___Priority___default)) : [Task]](#manual-Task) α



[Task.spawn.{u}]](#manual-Task___spawn) {α : Type u}
  (fn : [Unit]](#manual-Unit) → α)
  (prio : [Task.Priority]](#manual-Task___Priority) :=
    [Task.Priority.default]](#manual-Task___Priority___default)) :
  [Task]](#manual-Task) α
```

`spawn fn : Task α` constructs and immediately launches a new task for
evaluating the function `fn () : α` asynchronously.

`prio`, if provided, is the priority of the task.

constructor of Task

```lean
[Task.pure.{u}]](#manual-Task___pure) {α : Type u} (get : α) : [Task]](#manual-Task) α



[Task.pure.{u}]](#manual-Task___pure) {α : Type u} (get : α) :
  [Task]](#manual-Task) α
```

`[Task.pure]](#manual-Task___pure) (a : α)` constructs a task that is already resolved with value `a`.

#### 21.11.1.2. Impure Tasks {#manual-The-Lean-Language-Reference--IO--Tasks-and-Threads--Creating-Tasks--Impure-Tasks}

When spawning a task with side effects using one of the `[asTask]](#manual-IO___asTask)` functions, it's important to actually execute the resulting `[IO]](#manual-IO)` action.
A task is spawned each time the resulting action is executed, not when `[asTask]](#manual-IO___asTask)` is called.
Impure tasks continue running even when there are no references to them, though this does result in cancellation being requested.
Cancellation may also be explicitly requested using `[IO.cancel]](#manual-IO___cancel)`.
The impure task must check for cancellation using `[IO.checkCanceled]](#manual-IO___checkCanceled)`.

opaque

```lean
[BaseIO.asTask]](#manual-BaseIO___asTask) {α : Type} (act : [BaseIO]](#manual-BaseIO) α)
  (prio : [Task.Priority]](#manual-Task___Priority) := [Task.Priority.default]](#manual-Task___Priority___default)) : [BaseIO]](#manual-BaseIO) ([Task]](#manual-Task) α)



[BaseIO.asTask]](#manual-BaseIO___asTask) {α : Type} (act : [BaseIO]](#manual-BaseIO) α)
  (prio : [Task.Priority]](#manual-Task___Priority) :=
    [Task.Priority.default]](#manual-Task___Priority___default)) :
  [BaseIO]](#manual-BaseIO) ([Task]](#manual-Task) α)
```

Runs `act` in a separate `[Task]](#manual-Task)`, with priority `prio`.

Running the resulting `[BaseIO]](#manual-BaseIO)` action causes the task to be started eagerly. Pure accesses to the
`[Task]](#manual-Task)` do not influence the impure `act`.

Unlike pure tasks created by `[Task.spawn]](#manual-Task___spawn)`, tasks created by this function will run even if the last
reference to the task is dropped. The `act` should explicitly check for cancellation via
`[IO.checkCanceled]](#manual-IO___checkCanceled)` if it should be terminated or otherwise react to the last reference being
dropped.

def

```lean
[EIO.asTask]](#manual-EIO___asTask) {ε α : Type} (act : [EIO]](#manual-EIO) ε α)
  (prio : [Task.Priority]](#manual-Task___Priority) := [Task.Priority.default]](#manual-Task___Priority___default)) :
  [BaseIO]](#manual-BaseIO) ([Task]](#manual-Task) ([Except]](#manual-Except___error) ε α))



[EIO.asTask]](#manual-EIO___asTask) {ε α : Type} (act : [EIO]](#manual-EIO) ε α)
  (prio : [Task.Priority]](#manual-Task___Priority) :=
    [Task.Priority.default]](#manual-Task___Priority___default)) :
  [BaseIO]](#manual-BaseIO) ([Task]](#manual-Task) ([Except]](#manual-Except___error) ε α))
```

Runs `act` in a separate `[Task]](#manual-Task)`, with priority `prio`. Because `[EIO]](#manual-EIO) ε` actions may throw an exception
of type `ε`, the result of the task is an `[Except]](#manual-Except___error) ε α`.

Running the resulting `[IO]](#manual-IO)` action causes the task to be started eagerly. Pure accesses to the `[Task]](#manual-Task)`
do not influence the impure `act`.

Unlike pure tasks created by `[Task.spawn]](#manual-Task___spawn)`, tasks created by this function will run even if the last
reference to the task is dropped. The `act` should explicitly check for cancellation via
`[IO.checkCanceled]](#manual-IO___checkCanceled)` if it should be terminated or otherwise react to the last reference being
dropped.

def

```lean
[IO.asTask]](#manual-IO___asTask) {α : Type} (act : [IO]](#manual-IO) α)
  (prio : [Task.Priority]](#manual-Task___Priority) := [Task.Priority.default]](#manual-Task___Priority___default)) :
  [BaseIO]](#manual-BaseIO) ([Task]](#manual-Task) ([Except]](#manual-Except___error) [IO.Error]](#manual-IO___Error___alreadyExists) α))



[IO.asTask]](#manual-IO___asTask) {α : Type} (act : [IO]](#manual-IO) α)
  (prio : [Task.Priority]](#manual-Task___Priority) :=
    [Task.Priority.default]](#manual-Task___Priority___default)) :
  [BaseIO]](#manual-BaseIO) ([Task]](#manual-Task) ([Except]](#manual-Except___error) [IO.Error]](#manual-IO___Error___alreadyExists) α))
```

Runs `act` in a separate `[Task]](#manual-Task)`, with priority `prio`. Because `[IO]](#manual-IO)` actions may throw an exception
of type `[IO.Error]](#manual-IO___Error___alreadyExists)`, the result of the task is an `[Except]](#manual-Except___error) [IO.Error]](#manual-IO___Error___alreadyExists) α`.

Running the resulting `[BaseIO]](#manual-BaseIO)` action causes the task to be started eagerly. Pure accesses to the
`[Task]](#manual-Task)` do not influence the impure `act`. Because `[IO]](#manual-IO)` actions may throw an exception of type
`[IO.Error]](#manual-IO___Error___alreadyExists)`, the result of the task is an `[Except]](#manual-Except___error) [IO.Error]](#manual-IO___Error___alreadyExists) α`.

Unlike pure tasks created by `[Task.spawn]](#manual-Task___spawn)`, tasks created by this function will run even if the last
reference to the task is dropped. The `act` should explicitly check for cancellation via
`[IO.checkCanceled]](#manual-IO___checkCanceled)` if it should be terminated or otherwise react to the last reference being
dropped.

#### 21.11.1.3. Priorities {#manual-The-Lean-Language-Reference--IO--Tasks-and-Threads--Creating-Tasks--Priorities}

Task priorities are used by the thread scheduler to assign tasks to threads.
Within the priority range `[default]](#manual-Task___Priority___default)`–`[max]](#manual-Task___Priority___max)`, higher-priority tasks always take precedence over lower-priority tasks.
Tasks spawned with priority `[dedicated]](#manual-Task___Priority___dedicated)` are assigned their own dedicated threads and do not contend with other tasks for the threads in the thread pool.

def

```lean
[Task.Priority]](#manual-Task___Priority) : Type



[Task.Priority]](#manual-Task___Priority) : Type
```

Task priority.

Tasks with higher priority will always be scheduled before tasks with lower priority. Tasks with a
priority greater than `[Task.Priority.max]](#manual-Task___Priority___max)` are scheduled on dedicated threads.

def

```lean
[Task.Priority.default]](#manual-Task___Priority___default) : [Task.Priority]](#manual-Task___Priority)



[Task.Priority.default]](#manual-Task___Priority___default) : [Task.Priority]](#manual-Task___Priority)
```

The default priority for spawned tasks, also the lowest priority: `0`.

def

```lean
[Task.Priority.max]](#manual-Task___Priority___max) : [Task.Priority]](#manual-Task___Priority)



[Task.Priority.max]](#manual-Task___Priority___max) : [Task.Priority]](#manual-Task___Priority)
```

The highest regular priority for spawned tasks: `8`.

Spawning a task with a priority higher than `[Task.Priority.max]](#manual-Task___Priority___max)` is not an error but will spawn a
dedicated worker for the task. This is indicated using `[Task.Priority.dedicated]](#manual-Task___Priority___dedicated)`. Regular priority
tasks are placed in a thread pool and worked on according to their priority order.

def

```lean
[Task.Priority.dedicated]](#manual-Task___Priority___dedicated) : [Task.Priority]](#manual-Task___Priority)



[Task.Priority.dedicated]](#manual-Task___Priority___dedicated) : [Task.Priority]](#manual-Task___Priority)
```

Indicates that a task should be scheduled on a dedicated thread.

Any priority higher than `[Task.Priority.max]](#manual-Task___Priority___max)` will result in the task being scheduled
immediately on a dedicated thread. This is particularly useful for long-running and/or
I/O-bound tasks since Lean will, by default, allocate no more non-dedicated workers
than the number of cores to reduce context switches.

### 21.11.2. Task Results {#manual-The-Lean-Language-Reference--IO--Tasks-and-Threads--Task-Results}

def

```lean
[Task.get.{u}]](#manual-Task___get) {α : Type u} (self : [Task]](#manual-Task) α) : α



[Task.get.{u}]](#manual-Task___get) {α : Type u}
  (self : [Task]](#manual-Task) α) : α
```

Blocks the current thread until the given task has finished execution, and then returns the result
of the task. If the current thread is itself executing a (non-dedicated) task, the maximum
threadpool size is temporarily increased by one while waiting so as to ensure the process cannot
be deadlocked by threadpool starvation. Note that when the current thread is unblocked, more tasks
than the configured threadpool size may temporarily be running at the same time until sufficiently
many tasks have finished.

`[Task.map]](#manual-Task___map)` and `[Task.bind]](#manual-Task___bind)` should be preferred over `[Task.get]](#manual-Task___get)` for setting up task dependencies
where possible as they do not require temporarily growing the threadpool in this way. In
particular, calling `[Task.get]](#manual-Task___get)` in a task continuation with `(sync := true)` will panic as the
continuation is decidedly not "cheap" in this case and deadlocks may otherwise occur. The
waited-upon task should instead be returned and unwrapped using `Task.bind/IO.bindTask`.

opaque

```lean
[IO.wait]](#manual-IO___wait) {α : Type} (t : [Task]](#manual-Task) α) : [BaseIO]](#manual-BaseIO) α



[IO.wait]](#manual-IO___wait) {α : Type} (t : [Task]](#manual-Task) α) : [BaseIO]](#manual-BaseIO) α
```

Waits for the task to finish, then returns its result.

opaque

```lean
[IO.waitAny]](#manual-IO___waitAny) {α : Type} (tasks : [List]](#manual-List___nil) ([Task]](#manual-Task) α))
  (h : tasks.[length]](#manual-List___length) > 0 := by exact Nat.zero_lt_succ _) : [BaseIO]](#manual-BaseIO) α



[IO.waitAny]](#manual-IO___waitAny) {α : Type}
  (tasks : [List]](#manual-List___nil) ([Task]](#manual-Task) α))
  (h : tasks.[length]](#manual-List___length) > 0 := by
    exact Nat.zero_lt_succ _) :
  [BaseIO]](#manual-BaseIO) α
```

Waits until any of the tasks in the list has finished, then returns its result.

### 21.11.3. Sequencing Tasks {#manual-The-Lean-Language-Reference--IO--Tasks-and-Threads--Sequencing-Tasks}

These operators create new tasks from old ones.
When possible, it's good to use `[Task.map]](#manual-Task___map)` or `[Task.bind]](#manual-Task___bind)` instead of manually calling `[Task.get]](#manual-Task___get)` in a new task because they don't temporarily increase the size of the thread pool.

def

```lean
[Task.map.{u_1, u_2}]](#manual-Task___map) {α : Type u_1} {β : Type u_2} (f : α → β)
  (x : [Task]](#manual-Task) α) (prio : [Task.Priority]](#manual-Task___Priority) := [Task.Priority.default]](#manual-Task___Priority___default))
  (sync : [Bool]](#manual-Bool___false) := [false]](#manual-Bool___false)) : [Task]](#manual-Task) β



[Task.map.{u_1, u_2}]](#manual-Task___map) {α : Type u_1}
  {β : Type u_2} (f : α → β) (x : [Task]](#manual-Task) α)
  (prio : [Task.Priority]](#manual-Task___Priority) :=
    [Task.Priority.default]](#manual-Task___Priority___default))
  (sync : [Bool]](#manual-Bool___false) := [false]](#manual-Bool___false)) : [Task]](#manual-Task) β
```

`map f x` maps function `f` over the task `x`: that is, it constructs
(and immediately launches) a new task which will wait for the value of `x` to
be available and then calls `f` on the result.

`prio`, if provided, is the priority of the task.
If `sync` is set to true, `f` is executed on the current thread if `x` has already finished and
otherwise on the thread that `x` finished on. `prio` is ignored in this case. This should only be
done when executing `f` is cheap and non-blocking.

def

```lean
[Task.bind.{u_1, u_2}]](#manual-Task___bind) {α : Type u_1} {β : Type u_2} (x : [Task]](#manual-Task) α)
  (f : α → [Task]](#manual-Task) β) (prio : [Task.Priority]](#manual-Task___Priority) := [Task.Priority.default]](#manual-Task___Priority___default))
  (sync : [Bool]](#manual-Bool___false) := [false]](#manual-Bool___false)) : [Task]](#manual-Task) β



[Task.bind.{u_1, u_2}]](#manual-Task___bind) {α : Type u_1}
  {β : Type u_2} (x : [Task]](#manual-Task) α)
  (f : α → [Task]](#manual-Task) β)
  (prio : [Task.Priority]](#manual-Task___Priority) :=
    [Task.Priority.default]](#manual-Task___Priority___default))
  (sync : [Bool]](#manual-Bool___false) := [false]](#manual-Bool___false)) : [Task]](#manual-Task) β
```

`bind x f` does a monad "bind" operation on the task `x` with function `f`:
that is, it constructs (and immediately launches) a new task which will wait
for the value of `x` to be available and then calls `f` on the result,
resulting in a new task which is then run for a result.

`prio`, if provided, is the priority of the task.
If `sync` is set to true, `f` is executed on the current thread if `x` has already finished and
otherwise on the thread that `x` finished on. `prio` is ignored in this case. This should only be
done when executing `f` is cheap and non-blocking.

def

```lean
[Task.mapList.{u_1, u_2}]](#manual-Task___mapList) {α : Type u_1} {β : Type u_2} (f : [List]](#manual-List___nil) α → β)
  (tasks : [List]](#manual-List___nil) ([Task]](#manual-Task) α))
  (prio : [Task.Priority]](#manual-Task___Priority) := [Task.Priority.default]](#manual-Task___Priority___default))
  (sync : [Bool]](#manual-Bool___false) := [false]](#manual-Bool___false)) : [Task]](#manual-Task) β



[Task.mapList.{u_1, u_2}]](#manual-Task___mapList) {α : Type u_1}
  {β : Type u_2} (f : [List]](#manual-List___nil) α → β)
  (tasks : [List]](#manual-List___nil) ([Task]](#manual-Task) α))
  (prio : [Task.Priority]](#manual-Task___Priority) :=
    [Task.Priority.default]](#manual-Task___Priority___default))
  (sync : [Bool]](#manual-Bool___false) := [false]](#manual-Bool___false)) : [Task]](#manual-Task) β
```

Creates a task that, when all `tasks` have finished, computes the result of `f` applied to their
results.

opaque

```lean
[BaseIO.mapTask.{u_1}]](#manual-BaseIO___mapTask) {α : Type u_1} {β : Type} (f : α → [BaseIO]](#manual-BaseIO) β)
  (t : [Task]](#manual-Task) α) (prio : [Task.Priority]](#manual-Task___Priority) := [Task.Priority.default]](#manual-Task___Priority___default))
  (sync : [Bool]](#manual-Bool___false) := [false]](#manual-Bool___false)) : [BaseIO]](#manual-BaseIO) ([Task]](#manual-Task) β)



[BaseIO.mapTask.{u_1}]](#manual-BaseIO___mapTask) {α : Type u_1}
  {β : Type} (f : α → [BaseIO]](#manual-BaseIO) β)
  (t : [Task]](#manual-Task) α)
  (prio : [Task.Priority]](#manual-Task___Priority) :=
    [Task.Priority.default]](#manual-Task___Priority___default))
  (sync : [Bool]](#manual-Bool___false) := [false]](#manual-Bool___false)) : [BaseIO]](#manual-BaseIO) ([Task]](#manual-Task) β)
```

Creates a new task that waits for `t` to complete and then runs the `[BaseIO]](#manual-BaseIO)` action `f` on its
result. This new task has priority `prio`.

Running the resulting `[BaseIO]](#manual-BaseIO)` action causes the task to be started eagerly. Unlike pure tasks
created by `[Task.spawn]](#manual-Task___spawn)`, tasks created by this function will run even if the last reference to the
task is dropped. The `act` should explicitly check for cancellation via `[IO.checkCanceled]](#manual-IO___checkCanceled)` if it
should be terminated or otherwise react to the last reference being dropped.

def

```lean
[EIO.mapTask.{u_1}]](#manual-EIO___mapTask) {α : Type u_1} {ε β : Type} (f : α → [EIO]](#manual-EIO) ε β)
  (t : [Task]](#manual-Task) α) (prio : [Task.Priority]](#manual-Task___Priority) := [Task.Priority.default]](#manual-Task___Priority___default))
  (sync : [Bool]](#manual-Bool___false) := [false]](#manual-Bool___false)) : [BaseIO]](#manual-BaseIO) ([Task]](#manual-Task) ([Except]](#manual-Except___error) ε β))



[EIO.mapTask.{u_1}]](#manual-EIO___mapTask) {α : Type u_1}
  {ε β : Type} (f : α → [EIO]](#manual-EIO) ε β)
  (t : [Task]](#manual-Task) α)
  (prio : [Task.Priority]](#manual-Task___Priority) :=
    [Task.Priority.default]](#manual-Task___Priority___default))
  (sync : [Bool]](#manual-Bool___false) := [false]](#manual-Bool___false)) :
  [BaseIO]](#manual-BaseIO) ([Task]](#manual-Task) ([Except]](#manual-Except___error) ε β))
```

Creates a new task that waits for `t` to complete and then runs the `[IO]](#manual-IO)` action `f` on its result.
This new task has priority `prio`.

Running the resulting `[BaseIO]](#manual-BaseIO)` action causes the task to be started eagerly. Unlike pure tasks
created by `[Task.spawn]](#manual-Task___spawn)`, tasks created by this function will run even if the last reference to the
task is dropped. The `act` should explicitly check for cancellation via `[IO.checkCanceled]](#manual-IO___checkCanceled)` if it
should be terminated or otherwise react to the last reference being dropped. Because `[EIO]](#manual-EIO) ε` actions
may throw an exception of type `ε`, the result of the task is an `[Except]](#manual-Except___error) ε α`.

def

```lean
[IO.mapTask.{u_1}]](#manual-IO___mapTask) {α : Type u_1} {β : Type} (f : α → [IO]](#manual-IO) β) (t : [Task]](#manual-Task) α)
  (prio : [Task.Priority]](#manual-Task___Priority) := [Task.Priority.default]](#manual-Task___Priority___default))
  (sync : [Bool]](#manual-Bool___false) := [false]](#manual-Bool___false)) : [BaseIO]](#manual-BaseIO) ([Task]](#manual-Task) ([Except]](#manual-Except___error) [IO.Error]](#manual-IO___Error___alreadyExists) β))



[IO.mapTask.{u_1}]](#manual-IO___mapTask) {α : Type u_1} {β : Type}
  (f : α → [IO]](#manual-IO) β) (t : [Task]](#manual-Task) α)
  (prio : [Task.Priority]](#manual-Task___Priority) :=
    [Task.Priority.default]](#manual-Task___Priority___default))
  (sync : [Bool]](#manual-Bool___false) := [false]](#manual-Bool___false)) :
  [BaseIO]](#manual-BaseIO) ([Task]](#manual-Task) ([Except]](#manual-Except___error) [IO.Error]](#manual-IO___Error___alreadyExists) β))
```

Creates a new task that waits for `t` to complete and then runs the `[IO]](#manual-IO)` action `f` on its result.
This new task has priority `prio`.

Running the resulting `[BaseIO]](#manual-BaseIO)` action causes the task to be started eagerly. Unlike pure tasks
created by `[Task.spawn]](#manual-Task___spawn)`, tasks created by this function will run even if the last reference to the
task is dropped. The `act` should explicitly check for cancellation via `[IO.checkCanceled]](#manual-IO___checkCanceled)` if it
should be terminated or otherwise react to the last reference being dropped. Because `[IO]](#manual-IO)` actions
may throw an exception of type `[IO.Error]](#manual-IO___Error___alreadyExists)`, the result of the task is an `[Except]](#manual-Except___error) [IO.Error]](#manual-IO___Error___alreadyExists) α`.

def

```lean
[BaseIO.mapTasks.{u_1}]](#manual-BaseIO___mapTasks) {α : Type u_1} {β : Type} (f : [List]](#manual-List___nil) α → [BaseIO]](#manual-BaseIO) β)
  (tasks : [List]](#manual-List___nil) ([Task]](#manual-Task) α))
  (prio : [Task.Priority]](#manual-Task___Priority) := [Task.Priority.default]](#manual-Task___Priority___default))
  (sync : [Bool]](#manual-Bool___false) := [false]](#manual-Bool___false)) : [BaseIO]](#manual-BaseIO) ([Task]](#manual-Task) β)



[BaseIO.mapTasks.{u_1}]](#manual-BaseIO___mapTasks) {α : Type u_1}
  {β : Type} (f : [List]](#manual-List___nil) α → [BaseIO]](#manual-BaseIO) β)
  (tasks : [List]](#manual-List___nil) ([Task]](#manual-Task) α))
  (prio : [Task.Priority]](#manual-Task___Priority) :=
    [Task.Priority.default]](#manual-Task___Priority___default))
  (sync : [Bool]](#manual-Bool___false) := [false]](#manual-Bool___false)) : [BaseIO]](#manual-BaseIO) ([Task]](#manual-Task) β)
```

Creates a new task that waits for all the tasks in the list `tasks` to complete, and then runs the
`[IO]](#manual-IO)` action `f` on their results. This new task has priority `prio`.

Running the resulting `[BaseIO]](#manual-BaseIO)` action causes the task to be started eagerly. Unlike pure tasks
created by `[Task.spawn]](#manual-Task___spawn)`, tasks created by this function will run even if the last reference to the
task is dropped. The `act` should explicitly check for cancellation via `[IO.checkCanceled]](#manual-IO___checkCanceled)` if it
should be terminated or otherwise react to the last reference being dropped.

def

```lean
[EIO.mapTasks.{u_1}]](#manual-EIO___mapTasks) {α : Type u_1} {ε β : Type} (f : [List]](#manual-List___nil) α → [EIO]](#manual-EIO) ε β)
  (tasks : [List]](#manual-List___nil) ([Task]](#manual-Task) α))
  (prio : [Task.Priority]](#manual-Task___Priority) := [Task.Priority.default]](#manual-Task___Priority___default))
  (sync : [Bool]](#manual-Bool___false) := [false]](#manual-Bool___false)) : [BaseIO]](#manual-BaseIO) ([Task]](#manual-Task) ([Except]](#manual-Except___error) ε β))



[EIO.mapTasks.{u_1}]](#manual-EIO___mapTasks) {α : Type u_1}
  {ε β : Type} (f : [List]](#manual-List___nil) α → [EIO]](#manual-EIO) ε β)
  (tasks : [List]](#manual-List___nil) ([Task]](#manual-Task) α))
  (prio : [Task.Priority]](#manual-Task___Priority) :=
    [Task.Priority.default]](#manual-Task___Priority___default))
  (sync : [Bool]](#manual-Bool___false) := [false]](#manual-Bool___false)) :
  [BaseIO]](#manual-BaseIO) ([Task]](#manual-Task) ([Except]](#manual-Except___error) ε β))
```

Creates a new task that waits for all the tasks in the list `tasks` to complete, and then runs the
`[EIO]](#manual-EIO) ε` action `f` on their results. This new task has priority `prio`.

Running the resulting `[BaseIO]](#manual-BaseIO)` action causes the task to be started eagerly. Unlike pure tasks
created by `[Task.spawn]](#manual-Task___spawn)`, tasks created by this function will run even if the last reference to the
task is dropped. The `act` should explicitly check for cancellation via `[IO.checkCanceled]](#manual-IO___checkCanceled)` if it
should be terminated or otherwise react to the last reference being dropped.

def

```lean
[IO.mapTasks.{u_1}]](#manual-IO___mapTasks) {α : Type u_1} {β : Type} (f : [List]](#manual-List___nil) α → [IO]](#manual-IO) β)
  (tasks : [List]](#manual-List___nil) ([Task]](#manual-Task) α))
  (prio : [Task.Priority]](#manual-Task___Priority) := [Task.Priority.default]](#manual-Task___Priority___default))
  (sync : [Bool]](#manual-Bool___false) := [false]](#manual-Bool___false)) : [BaseIO]](#manual-BaseIO) ([Task]](#manual-Task) ([Except]](#manual-Except___error) [IO.Error]](#manual-IO___Error___alreadyExists) β))



[IO.mapTasks.{u_1}]](#manual-IO___mapTasks) {α : Type u_1}
  {β : Type} (f : [List]](#manual-List___nil) α → [IO]](#manual-IO) β)
  (tasks : [List]](#manual-List___nil) ([Task]](#manual-Task) α))
  (prio : [Task.Priority]](#manual-Task___Priority) :=
    [Task.Priority.default]](#manual-Task___Priority___default))
  (sync : [Bool]](#manual-Bool___false) := [false]](#manual-Bool___false)) :
  [BaseIO]](#manual-BaseIO) ([Task]](#manual-Task) ([Except]](#manual-Except___error) [IO.Error]](#manual-IO___Error___alreadyExists) β))
```

`[IO]](#manual-IO)` specialization of `[EIO.mapTasks]](#manual-EIO___mapTasks)`.

opaque

```lean
[BaseIO.bindTask.{u_1}]](#manual-BaseIO___bindTask) {α : Type u_1} {β : Type} (t : [Task]](#manual-Task) α)
  (f : α → [BaseIO]](#manual-BaseIO) ([Task]](#manual-Task) β))
  (prio : [Task.Priority]](#manual-Task___Priority) := [Task.Priority.default]](#manual-Task___Priority___default))
  (sync : [Bool]](#manual-Bool___false) := [false]](#manual-Bool___false)) : [BaseIO]](#manual-BaseIO) ([Task]](#manual-Task) β)



[BaseIO.bindTask.{u_1}]](#manual-BaseIO___bindTask) {α : Type u_1}
  {β : Type} (t : [Task]](#manual-Task) α)
  (f : α → [BaseIO]](#manual-BaseIO) ([Task]](#manual-Task) β))
  (prio : [Task.Priority]](#manual-Task___Priority) :=
    [Task.Priority.default]](#manual-Task___Priority___default))
  (sync : [Bool]](#manual-Bool___false) := [false]](#manual-Bool___false)) : [BaseIO]](#manual-BaseIO) ([Task]](#manual-Task) β)
```

Creates a new task that waits for `t` to complete, runs the `[IO]](#manual-IO)` action `f` on its result, and then
continues as the resulting task. This new task has priority `prio`.

Running the resulting `[BaseIO]](#manual-BaseIO)` action causes this new task to be started eagerly. Unlike pure tasks
created by `[Task.spawn]](#manual-Task___spawn)`, tasks created by this function will run even if the last reference to the
task is dropped. The `act` should explicitly check for cancellation via `[IO.checkCanceled]](#manual-IO___checkCanceled)` if it
should be terminated or otherwise react to the last reference being dropped.

def

```lean
[EIO.bindTask.{u_1}]](#manual-EIO___bindTask) {α : Type u_1} {ε β : Type} (t : [Task]](#manual-Task) α)
  (f : α → [EIO]](#manual-EIO) ε ([Task]](#manual-Task) ([Except]](#manual-Except___error) ε β)))
  (prio : [Task.Priority]](#manual-Task___Priority) := [Task.Priority.default]](#manual-Task___Priority___default))
  (sync : [Bool]](#manual-Bool___false) := [false]](#manual-Bool___false)) : [BaseIO]](#manual-BaseIO) ([Task]](#manual-Task) ([Except]](#manual-Except___error) ε β))



[EIO.bindTask.{u_1}]](#manual-EIO___bindTask) {α : Type u_1}
  {ε β : Type} (t : [Task]](#manual-Task) α)
  (f : α → [EIO]](#manual-EIO) ε ([Task]](#manual-Task) ([Except]](#manual-Except___error) ε β)))
  (prio : [Task.Priority]](#manual-Task___Priority) :=
    [Task.Priority.default]](#manual-Task___Priority___default))
  (sync : [Bool]](#manual-Bool___false) := [false]](#manual-Bool___false)) :
  [BaseIO]](#manual-BaseIO) ([Task]](#manual-Task) ([Except]](#manual-Except___error) ε β))
```

Creates a new task that waits for `t` to complete, runs the `[EIO]](#manual-EIO) ε` action `f` on its result, and
then continues as the resulting task. This new task has priority `prio`.

Running the resulting `[BaseIO]](#manual-BaseIO)` action causes this new task to be started eagerly. Unlike pure tasks
created by `[Task.spawn]](#manual-Task___spawn)`, tasks created by this function will run even if the last reference to the
task is dropped. The `act` should explicitly check for cancellation via `[IO.checkCanceled]](#manual-IO___checkCanceled)` if it
should be terminated or otherwise react to the last reference being dropped. Because `[EIO]](#manual-EIO) ε` actions
may throw an exception of type `ε`, the result of the task is an `[Except]](#manual-Except___error) ε α`.

def

```lean
[IO.bindTask.{u_1}]](#manual-IO___bindTask) {α : Type u_1} {β : Type} (t : [Task]](#manual-Task) α)
  (f : α → [IO]](#manual-IO) ([Task]](#manual-Task) ([Except]](#manual-Except___error) [IO.Error]](#manual-IO___Error___alreadyExists) β)))
  (prio : [Task.Priority]](#manual-Task___Priority) := [Task.Priority.default]](#manual-Task___Priority___default))
  (sync : [Bool]](#manual-Bool___false) := [false]](#manual-Bool___false)) : [BaseIO]](#manual-BaseIO) ([Task]](#manual-Task) ([Except]](#manual-Except___error) [IO.Error]](#manual-IO___Error___alreadyExists) β))



[IO.bindTask.{u_1}]](#manual-IO___bindTask) {α : Type u_1}
  {β : Type} (t : [Task]](#manual-Task) α)
  (f : α → [IO]](#manual-IO) ([Task]](#manual-Task) ([Except]](#manual-Except___error) [IO.Error]](#manual-IO___Error___alreadyExists) β)))
  (prio : [Task.Priority]](#manual-Task___Priority) :=
    [Task.Priority.default]](#manual-Task___Priority___default))
  (sync : [Bool]](#manual-Bool___false) := [false]](#manual-Bool___false)) :
  [BaseIO]](#manual-BaseIO) ([Task]](#manual-Task) ([Except]](#manual-Except___error) [IO.Error]](#manual-IO___Error___alreadyExists) β))
```

Creates a new task that waits for `t` to complete, runs the `[IO]](#manual-IO)` action `f` on its result, and then
continues as the resulting task. This new task has priority `prio`.

Running the resulting `[BaseIO]](#manual-BaseIO)` action causes this new task to be started eagerly. Unlike pure tasks
created by `[Task.spawn]](#manual-Task___spawn)`, tasks created by this function will run even if the last reference to the
task is dropped. The `act` should explicitly check for cancellation via `[IO.checkCanceled]](#manual-IO___checkCanceled)` if it
should be terminated or otherwise react to the last reference being dropped. Because `[IO]](#manual-IO)` actions
may throw an exception of type `[IO.Error]](#manual-IO___Error___alreadyExists)`, the result of the task is an `[Except]](#manual-Except___error) [IO.Error]](#manual-IO___Error___alreadyExists) α`.

def

```lean
[BaseIO.chainTask.{u_1}]](#manual-BaseIO___chainTask) {α : Type u_1} (t : [Task]](#manual-Task) α) (f : α → [BaseIO]](#manual-BaseIO) [Unit]](#manual-Unit))
  (prio : [Task.Priority]](#manual-Task___Priority) := [Task.Priority.default]](#manual-Task___Priority___default))
  (sync : [Bool]](#manual-Bool___false) := [false]](#manual-Bool___false)) : [BaseIO]](#manual-BaseIO) [Unit]](#manual-Unit)



[BaseIO.chainTask.{u_1}]](#manual-BaseIO___chainTask) {α : Type u_1}
  (t : [Task]](#manual-Task) α) (f : α → [BaseIO]](#manual-BaseIO) [Unit]](#manual-Unit))
  (prio : [Task.Priority]](#manual-Task___Priority) :=
    [Task.Priority.default]](#manual-Task___Priority___default))
  (sync : [Bool]](#manual-Bool___false) := [false]](#manual-Bool___false)) : [BaseIO]](#manual-BaseIO) [Unit]](#manual-Unit)
```

Creates a new task that waits for `t` to complete and then runs the `[IO]](#manual-IO)` action `f` on its result.
This new task has priority `prio`.

This is a version of `[BaseIO.mapTask]](#manual-BaseIO___mapTask)` that ignores the result value.

Running the resulting `[BaseIO]](#manual-BaseIO)` action causes the task to be started eagerly. Unlike pure tasks
created by `[Task.spawn]](#manual-Task___spawn)`, tasks created by this function will run even if the last reference to the
task is dropped. The `act` should explicitly check for cancellation via `[IO.checkCanceled]](#manual-IO___checkCanceled)` if it
should be terminated or otherwise react to the last reference being dropped.

def

```lean
[EIO.chainTask.{u_1}]](#manual-EIO___chainTask) {α : Type u_1} {ε : Type} (t : [Task]](#manual-Task) α)
  (f : α → [EIO]](#manual-EIO) ε [Unit]](#manual-Unit)) (prio : [Task.Priority]](#manual-Task___Priority) := [Task.Priority.default]](#manual-Task___Priority___default))
  (sync : [Bool]](#manual-Bool___false) := [false]](#manual-Bool___false)) : [EIO]](#manual-EIO) ε [Unit]](#manual-Unit)



[EIO.chainTask.{u_1}]](#manual-EIO___chainTask) {α : Type u_1}
  {ε : Type} (t : [Task]](#manual-Task) α)
  (f : α → [EIO]](#manual-EIO) ε [Unit]](#manual-Unit))
  (prio : [Task.Priority]](#manual-Task___Priority) :=
    [Task.Priority.default]](#manual-Task___Priority___default))
  (sync : [Bool]](#manual-Bool___false) := [false]](#manual-Bool___false)) : [EIO]](#manual-EIO) ε [Unit]](#manual-Unit)
```

Creates a new task that waits for `t` to complete and then runs the `[EIO]](#manual-EIO) ε` action `f` on its result.
This new task has priority `prio`.

This is a version of `[EIO.mapTask]](#manual-EIO___mapTask)` that ignores the result value.

Running the resulting `[EIO]](#manual-EIO) ε` action causes the task to be started eagerly. Unlike pure tasks
created by `[Task.spawn]](#manual-Task___spawn)`, tasks created by this function will run even if the last reference to the
task is dropped. The `act` should explicitly check for cancellation via `[IO.checkCanceled]](#manual-IO___checkCanceled)` if it
should be terminated or otherwise react to the last reference being dropped.

def

```lean
[IO.chainTask.{u_1}]](#manual-IO___chainTask) {α : Type u_1} (t : [Task]](#manual-Task) α) (f : α → [IO]](#manual-IO) [Unit]](#manual-Unit))
  (prio : [Task.Priority]](#manual-Task___Priority) := [Task.Priority.default]](#manual-Task___Priority___default))
  (sync : [Bool]](#manual-Bool___false) := [false]](#manual-Bool___false)) : [IO]](#manual-IO) [Unit]](#manual-Unit)



[IO.chainTask.{u_1}]](#manual-IO___chainTask) {α : Type u_1}
  (t : [Task]](#manual-Task) α) (f : α → [IO]](#manual-IO) [Unit]](#manual-Unit))
  (prio : [Task.Priority]](#manual-Task___Priority) :=
    [Task.Priority.default]](#manual-Task___Priority___default))
  (sync : [Bool]](#manual-Bool___false) := [false]](#manual-Bool___false)) : [IO]](#manual-IO) [Unit]](#manual-Unit)
```

Creates a new task that waits for `t` to complete and then runs the `[IO]](#manual-IO)` action `f` on its result.
This new task has priority `prio`.

This is a version of `[IO.mapTask]](#manual-IO___mapTask)` that ignores the result value.

Running the resulting `[IO]](#manual-IO)` action causes the task to be started eagerly. Unlike pure tasks created
by `[Task.spawn]](#manual-Task___spawn)`, tasks created by this function will run even if the last reference to the task is
dropped. The act should explicitly check for cancellation via `[IO.checkCanceled]](#manual-IO___checkCanceled)` if it should be
terminated or otherwise react to the last reference being dropped.

### 21.11.4. Cancellation and Status {#manual-The-Lean-Language-Reference--IO--Tasks-and-Threads--Cancellation-and-Status}

Impure tasks should use `IO.checkCanceled` to react to cancellation, which occurs either as a result of `IO.cancel` or when the last reference to the task is dropped.
Pure tasks are terminated automatically upon cancellation.

opaque

```lean
[IO.cancel.{u_1}]](#manual-IO___cancel) {α : Type u_1} : [Task]](#manual-Task) α → [BaseIO]](#manual-BaseIO) [Unit]](#manual-Unit)



[IO.cancel.{u_1}]](#manual-IO___cancel) {α : Type u_1} :
  [Task]](#manual-Task) α → [BaseIO]](#manual-BaseIO) [Unit]](#manual-Unit)
```

Requests cooperative cancellation of the task. The task must explicitly call `[IO.checkCanceled]](#manual-IO___checkCanceled)` to
react to the cancellation.

opaque

```lean
[IO.checkCanceled]](#manual-IO___checkCanceled) : [BaseIO]](#manual-BaseIO) [Bool]](#manual-Bool___false)



[IO.checkCanceled]](#manual-IO___checkCanceled) : [BaseIO]](#manual-BaseIO) [Bool]](#manual-Bool___false)
```

Checks whether the current task's cancellation flag has been set by calling `[IO.cancel]](#manual-IO___cancel)` or by
dropping the last reference to the task.

def

```lean
[IO.hasFinished.{u_1}]](#manual-IO___hasFinished) {α : Type u_1} (task : [Task]](#manual-Task) α) : [BaseIO]](#manual-BaseIO) [Bool]](#manual-Bool___false)



[IO.hasFinished.{u_1}]](#manual-IO___hasFinished) {α : Type u_1}
  (task : [Task]](#manual-Task) α) : [BaseIO]](#manual-BaseIO) [Bool]](#manual-Bool___false)
```

Checks whether the task has finished execution, at which point calling `[Task.get]](#manual-Task___get)` will return
immediately.

opaque

```lean
[IO.getTaskState.{u_1}]](#manual-IO___getTaskState) {α : Type u_1} : [Task]](#manual-Task) α → [BaseIO]](#manual-BaseIO) [IO.TaskState]](#manual-IO___TaskState___waiting)



[IO.getTaskState.{u_1}]](#manual-IO___getTaskState) {α : Type u_1} :
  [Task]](#manual-Task) α → [BaseIO]](#manual-BaseIO) [IO.TaskState]](#manual-IO___TaskState___waiting)
```

Returns the current state of a task in the Lean runtime's task manager.

For tasks derived from `Promise`s, the states `waiting` and `running` should be considered
equivalent.

inductive type

```lean
[IO.TaskState]](#manual-IO___TaskState___waiting) : Type



[IO.TaskState]](#manual-IO___TaskState___waiting) : Type
```

The current state of a `[Task]](#manual-Task)` in the Lean runtime's task manager.

Constructors

```lean
[IO.TaskState.waiting]](#manual-IO___TaskState___waiting) : [IO.TaskState]](#manual-IO___TaskState___waiting)
```

The `[Task]](#manual-Task)` is waiting to be run.

It can be waiting for dependencies to complete or sitting in the task manager queue waiting for a
thread to run on.

```lean
[IO.TaskState.running]](#manual-IO___TaskState___waiting) : [IO.TaskState]](#manual-IO___TaskState___waiting)
```

The `[Task]](#manual-Task)` is actively running on a thread or, in the case of a `[Promise]](#manual-IO___Promise)`, waiting for a call to
`[IO.Promise.resolve]](#manual-IO___Promise___resolve)`.

```lean
[IO.TaskState.finished]](#manual-IO___TaskState___waiting) : [IO.TaskState]](#manual-IO___TaskState___waiting)
```

The `[Task]](#manual-Task)` has finished running and its result is available. Calling `[Task.get]](#manual-Task___get)` or `[IO.wait]](#manual-IO___wait)` on
the task will not block.

opaque

```lean
[IO.getTID]](#manual-IO___getTID) : [BaseIO]](#manual-BaseIO) [UInt64]](#manual-UInt64___ofBitVec)



[IO.getTID]](#manual-IO___getTID) : [BaseIO]](#manual-BaseIO) [UInt64]](#manual-UInt64___ofBitVec)
```

Returns the thread ID of the calling thread.

### 21.11.5. Promises {#manual-The-Lean-Language-Reference--IO--Tasks-and-Threads--Promises}

Promises represent a value that will be supplied in the future.
Supplying the value is called *resolving* the promise.
Once created, a promise can be stored in a data structure or passed around like any other value, and attempts to read from it will block until it is resolved.

structure

```lean
[IO.Promise]](#manual-IO___Promise) (α : Type) : Type



[IO.Promise]](#manual-IO___Promise) (α : Type) : Type
```

`Promise α` allows you to create a `[Task]](#manual-Task) α` whose value is provided later by calling `resolve`.

Typical usage is as follows:

1. `let promise ← Promise.new` creates a promise
2. `promise.result? : Task (Option α)` can now be passed around
3. `promise.result?.get` blocks until the promise is resolved
4. `promise.resolve a` resolves the promise
5. `promise.result?.get` now returns `[some]](#manual-Option___none) a`

If the promise is dropped without ever being resolved, `promise.result?.get` will return `[none]](#manual-Option___none)`.
See `Promise.result!/resultD` for other ways to handle this case.

opaque

```lean
[IO.Promise.new]](#manual-IO___Promise___new) {α : Type} [[Nonempty]](#manual-Nonempty___intro) α] : [BaseIO]](#manual-BaseIO) ([IO.Promise]](#manual-IO___Promise) α)



[IO.Promise.new]](#manual-IO___Promise___new) {α : Type} [[Nonempty]](#manual-Nonempty___intro) α] :
  [BaseIO]](#manual-BaseIO) ([IO.Promise]](#manual-IO___Promise) α)
```

Creates a new `Promise`.

def

```lean
[IO.Promise.isResolved]](#manual-IO___Promise___isResolved) {α : Type} (promise : [IO.Promise]](#manual-IO___Promise) α) : [BaseIO]](#manual-BaseIO) [Bool]](#manual-Bool___false)



[IO.Promise.isResolved]](#manual-IO___Promise___isResolved) {α : Type}
  (promise : [IO.Promise]](#manual-IO___Promise) α) : [BaseIO]](#manual-BaseIO) [Bool]](#manual-Bool___false)
```

Checks whether the promise has already been resolved, i.e. whether access to `result*` will return
immediately.

opaque

```lean
[IO.Promise.result?]](#manual-IO___Promise___result___) {α : Type} (promise : [IO.Promise]](#manual-IO___Promise) α) : [Task]](#manual-Task) ([Option]](#manual-Option___none) α)



[IO.Promise.result?]](#manual-IO___Promise___result___) {α : Type}
  (promise : [IO.Promise]](#manual-IO___Promise) α) :
  [Task]](#manual-Task) ([Option]](#manual-Option___none) α)
```

Like `Promise.result`, but resolves to `[none]](#manual-Option___none)` if the promise is dropped without ever being resolved.

def

```lean
[IO.Promise.result!]](#manual-IO___Promise___result___-next) {α : Type} (promise : [IO.Promise]](#manual-IO___Promise) α) : [Task]](#manual-Task) α



[IO.Promise.result!]](#manual-IO___Promise___result___-next) {α : Type}
  (promise : [IO.Promise]](#manual-IO___Promise) α) : [Task]](#manual-Task) α
```

The result task of a `Promise`.

The task blocks until `Promise.resolve` is called. If the promise is dropped without ever being
resolved, evaluating the task will panic and, when not using fatal panics, block forever. As
`Promise.result!` is a pure value and thus the point of evaluation may not be known precisely, this
means that any promise on which `Promise.result!` *may* be evaluated *must* be resolved eventually.
When in doubt, always prefer `Promise.result?` to handle dropped promises explicitly.

def

```lean
[IO.Promise.resultD]](#manual-IO___Promise___resultD) {α : Type} (promise : [IO.Promise]](#manual-IO___Promise) α) (dflt : α) :
  [Task]](#manual-Task) α



[IO.Promise.resultD]](#manual-IO___Promise___resultD) {α : Type}
  (promise : [IO.Promise]](#manual-IO___Promise) α) (dflt : α) :
  [Task]](#manual-Task) α
```

Like `Promise.result`, but resolves to `dflt` if the promise is dropped without ever being resolved.

opaque

```lean
[IO.Promise.resolve]](#manual-IO___Promise___resolve) {α : Type} (value : α) (promise : [IO.Promise]](#manual-IO___Promise) α) :
  [BaseIO]](#manual-BaseIO) [Unit]](#manual-Unit)



[IO.Promise.resolve]](#manual-IO___Promise___resolve) {α : Type} (value : α)
  (promise : [IO.Promise]](#manual-IO___Promise) α) : [BaseIO]](#manual-BaseIO) [Unit]](#manual-Unit)
```

Resolves a `Promise`.

Only the first call to this function has an effect.

### 21.11.6. Communication Between Tasks {#manual-The-Lean-Language-Reference--IO--Tasks-and-Threads--Communication-Between-Tasks}

In addition to the types and operations described in this section, `[IO.Ref]](#manual-IO___Ref)` can be used as a lock.
Taking the reference (using `[take]](#manual-ST___Ref___take)`) causes other threads to block when reading until the reference is `[set]](#manual-ST___Ref___set)` again.
This pattern is described in [the section on reference cells]](#manual-ref-locks).

#### 21.11.6.1. Channels {#manual-The-Lean-Language-Reference--IO--Tasks-and-Threads--Communication-Between-Tasks--Channels}

The types and functions in this section are available after importing `Std.Sync.Channel`.

structure

```lean
[Std.Channel]](#manual-Std___Channel) (α : Type) : Type



[Std.Channel]](#manual-Std___Channel) (α : Type) : Type
```

A multi-producer multi-consumer FIFO channel that offers both bounded and unbounded buffering
and an asynchronous API. To switch into synchronous mode use `Channel.sync`.

If a channel needs to be closed to indicate some sort of completion event use `[Std.CloseableChannel]](#manual-Std___CloseableChannel)`
instead. Note that `[Std.CloseableChannel]](#manual-Std___CloseableChannel)` introduces a need for error handling in some cases, thus
`[Std.Channel]](#manual-Std___Channel)` is usually easier to use if applicable.

def

```lean
[Std.Channel.new]](#manual-Std___Channel___new) {α : Type} (capacity : [Option]](#manual-Option___none) [Nat]](#manual-Nat___zero) := [none]](#manual-Option___none)) :
  [BaseIO]](#manual-BaseIO) ([Std.Channel]](#manual-Std___Channel) α)



[Std.Channel.new]](#manual-Std___Channel___new) {α : Type}
  (capacity : [Option]](#manual-Option___none) [Nat]](#manual-Nat___zero) := [none]](#manual-Option___none)) :
  [BaseIO]](#manual-BaseIO) ([Std.Channel]](#manual-Std___Channel) α)
```

Create a new channel. If:

- `capacity` is `[none]](#manual-Option___none)` it will be unbounded (the default)
- `capacity` is `[some]](#manual-Option___none) 0` it will always force a rendezvous between sender and receiver
- `capacity` is `[some]](#manual-Option___none) n` with `n > 0` it will use a buffer of size `n` and begin blocking once it
  is filled

def

```lean
[Std.Channel.send]](#manual-Std___Channel___send) {α : Type} (ch : [Std.Channel]](#manual-Std___Channel) α) (v : α) :
  [BaseIO]](#manual-BaseIO) ([Task]](#manual-Task) [Unit]](#manual-Unit))



[Std.Channel.send]](#manual-Std___Channel___send) {α : Type}
  (ch : [Std.Channel]](#manual-Std___Channel) α) (v : α) :
  [BaseIO]](#manual-BaseIO) ([Task]](#manual-Task) [Unit]](#manual-Unit))
```

Send a value through the channel, returning a task that will resolve once the transmission could be
completed.

def

```lean
[Std.Channel.recv]](#manual-Std___Channel___recv) {α : Type} [[Inhabited]](#manual-Inhabited___mk) α] (ch : [Std.Channel]](#manual-Std___Channel) α) :
  [BaseIO]](#manual-BaseIO) ([Task]](#manual-Task) α)



[Std.Channel.recv]](#manual-Std___Channel___recv) {α : Type} [[Inhabited]](#manual-Inhabited___mk) α]
  (ch : [Std.Channel]](#manual-Std___Channel) α) : [BaseIO]](#manual-BaseIO) ([Task]](#manual-Task) α)
```

Receive a value from the channel, returning a task that will resolve once the transmission could be
completed. Note that the task may resolve to `[none]](#manual-Option___none)` if the channel was closed before it could be
completed.

opaque

```lean
[Std.Channel.forAsync]](#manual-Std___Channel___forAsync) {α : Type} [[Inhabited]](#manual-Inhabited___mk) α] (f : α → [BaseIO]](#manual-BaseIO) [Unit]](#manual-Unit))
  (ch : [Std.Channel]](#manual-Std___Channel) α) (prio : [Task.Priority]](#manual-Task___Priority) := [Task.Priority.default]](#manual-Task___Priority___default)) :
  [BaseIO]](#manual-BaseIO) ([Task]](#manual-Task) [Unit]](#manual-Unit))



[Std.Channel.forAsync]](#manual-Std___Channel___forAsync) {α : Type}
  [[Inhabited]](#manual-Inhabited___mk) α] (f : α → [BaseIO]](#manual-BaseIO) [Unit]](#manual-Unit))
  (ch : [Std.Channel]](#manual-Std___Channel) α)
  (prio : [Task.Priority]](#manual-Task___Priority) :=
    [Task.Priority.default]](#manual-Task___Priority___default)) :
  [BaseIO]](#manual-BaseIO) ([Task]](#manual-Task) [Unit]](#manual-Unit))
```

`ch.[forAsync]](#manual-Std___Channel___forAsync) f` calls `f` for every message received on `ch`.

Note that if this function is called twice, each message will only arrive at exactly one invocation.

def

```lean
[Std.Channel.sync]](#manual-Std___Channel___sync) {α : Type} (ch : [Std.Channel]](#manual-Std___Channel) α) : [Std.Channel.Sync]](#manual-Std___Channel___Sync) α



[Std.Channel.sync]](#manual-Std___Channel___sync) {α : Type}
  (ch : [Std.Channel]](#manual-Std___Channel) α) :
  [Std.Channel.Sync]](#manual-Std___Channel___Sync) α
```

This function is a no-op and just a convenient way to expose the synchronous API of the channel.

def

```lean
[Std.Channel.Sync]](#manual-Std___Channel___Sync) (α : Type) : Type



[Std.Channel.Sync]](#manual-Std___Channel___Sync) (α : Type) : Type
```

A multi-producer multi-consumer FIFO channel that offers both bounded and unbounded buffering
and a synchronous API. This type acts as a convenient layer to use a channel in a blocking fashion
and is not actually different from the original channel.

If a channel needs to be closed to indicate some sort of completion event use
`Std.CloseableChannel.Sync` instead. Note that `Std.CloseableChannel.Sync` introduces a need for error
handling in some cases, thus `[Std.Channel.Sync]](#manual-Std___Channel___Sync)` is usually easier to use if applicable.

def

```lean
[Std.CloseableChannel]](#manual-Std___CloseableChannel) (α : Type) : Type



[Std.CloseableChannel]](#manual-Std___CloseableChannel) (α : Type) : Type
```

A multi-producer multi-consumer FIFO channel that offers both bounded and unbounded buffering
and an asynchronous API, to switch into synchronous mode use `CloseableChannel.sync`.

Additionally `[Std.CloseableChannel]](#manual-Std___CloseableChannel)` can be closed if necessary, unlike `[Std.Channel]](#manual-Std___Channel)`.
This introduces a need for error handling in some cases, thus it is usually easier to use
`[Std.Channel]](#manual-Std___Channel)` if applicable.

def

```lean
[Std.CloseableChannel.new]](#manual-Std___CloseableChannel___new) {α : Type} (capacity : [Option]](#manual-Option___none) [Nat]](#manual-Nat___zero) := [none]](#manual-Option___none)) :
  [BaseIO]](#manual-BaseIO) ([Std.CloseableChannel]](#manual-Std___CloseableChannel) α)



[Std.CloseableChannel.new]](#manual-Std___CloseableChannel___new) {α : Type}
  (capacity : [Option]](#manual-Option___none) [Nat]](#manual-Nat___zero) := [none]](#manual-Option___none)) :
  [BaseIO]](#manual-BaseIO) ([Std.CloseableChannel]](#manual-Std___CloseableChannel) α)
```

Create a new channel. If:

- `capacity` is `[none]](#manual-Option___none)` it will be unbounded (the default)
- `capacity` is `[some]](#manual-Option___none) 0` it will always force a rendezvous between sender and receiver
- `capacity` is `[some]](#manual-Option___none) n` with `n > 0` it will use a buffer of size `n` and begin blocking once it
  is filled

Synchronous channels can also be read using `for` loops.
In particular, there is an instance of type `[ForIn]](#manual-ForIn___mk) m ([Std.Channel.Sync]](#manual-Std___Channel___Sync) α) α` for every monad `m` with a `[MonadLiftT]](#manual-MonadLiftT___mk) [BaseIO]](#manual-BaseIO) m` instance and `α` with an `[Inhabited]](#manual-Inhabited___mk) α` instance.

#### 21.11.6.2. Mutexes {#manual-The-Lean-Language-Reference--IO--Tasks-and-Threads--Communication-Between-Tasks--Mutexes}

The types and functions in this section are available after importing `Std.Sync.Mutex`.

type

```lean
[Std.Mutex]](#manual-Std___Mutex) (α : Type) : Type



[Std.Mutex]](#manual-Std___Mutex) (α : Type) : Type
```

Mutual exclusion primitive (lock) guarding shared state of type `α`.

The type `Mutex α` is similar to `[IO.Ref]](#manual-IO___Ref) α`, except that concurrent accesses are guarded by a mutex
instead of atomic pointer operations and busy-waiting.

def

```lean
[Std.Mutex.new]](#manual-Std___Mutex___new) {α : Type} (a : α) : [BaseIO]](#manual-BaseIO) ([Std.Mutex]](#manual-Std___Mutex) α)



[Std.Mutex.new]](#manual-Std___Mutex___new) {α : Type} (a : α) :
  [BaseIO]](#manual-BaseIO) ([Std.Mutex]](#manual-Std___Mutex) α)
```

Creates a new mutex.

def

```lean
[Std.Mutex.atomically]](#manual-Std___Mutex___atomically) {m : Type → Type} {α β : Type} [[Monad]](#manual-Monad___mk) m]
  [[MonadLiftT]](#manual-MonadLiftT___mk) [BaseIO]](#manual-BaseIO) m] [[MonadFinally]](#manual-MonadFinally___mk) m] (mutex : [Std.Mutex]](#manual-Std___Mutex) α)
  (k : [Std.AtomicT]](#manual-Std___AtomicT) α m β) : m β



[Std.Mutex.atomically]](#manual-Std___Mutex___atomically) {m : Type → Type}
  {α β : Type} [[Monad]](#manual-Monad___mk) m]
  [[MonadLiftT]](#manual-MonadLiftT___mk) [BaseIO]](#manual-BaseIO) m] [[MonadFinally]](#manual-MonadFinally___mk) m]
  (mutex : [Std.Mutex]](#manual-Std___Mutex) α)
  (k : [Std.AtomicT]](#manual-Std___AtomicT) α m β) : m β
```

`mutex.[atomically]](#manual-Std___Mutex___atomically) k` runs `k` with access to the mutex's state while locking the mutex.

Calling `mutex.atomically` while already holding the underlying `BaseMutex` in the same thread
is undefined behavior. If this is unavoidable in your code, consider using `RecursiveMutex`.

def

```lean
[Std.Mutex.atomicallyOnce]](#manual-Std___Mutex___atomicallyOnce) {m : Type → Type} {α β : Type} [[Monad]](#manual-Monad___mk) m]
  [[MonadLiftT]](#manual-MonadLiftT___mk) [BaseIO]](#manual-BaseIO) m] [[MonadFinally]](#manual-MonadFinally___mk) m] (mutex : [Std.Mutex]](#manual-Std___Mutex) α)
  (condvar : [Std.Condvar]](#manual-Std___Condvar)) (pred : [Std.AtomicT]](#manual-Std___AtomicT) α m [Bool]](#manual-Bool___false))
  (k : [Std.AtomicT]](#manual-Std___AtomicT) α m β) : m β



[Std.Mutex.atomicallyOnce]](#manual-Std___Mutex___atomicallyOnce) {m : Type → Type}
  {α β : Type} [[Monad]](#manual-Monad___mk) m]
  [[MonadLiftT]](#manual-MonadLiftT___mk) [BaseIO]](#manual-BaseIO) m] [[MonadFinally]](#manual-MonadFinally___mk) m]
  (mutex : [Std.Mutex]](#manual-Std___Mutex) α)
  (condvar : [Std.Condvar]](#manual-Std___Condvar))
  (pred : [Std.AtomicT]](#manual-Std___AtomicT) α m [Bool]](#manual-Bool___false))
  (k : [Std.AtomicT]](#manual-Std___AtomicT) α m β) : m β
```

`mutex.[atomicallyOnce]](#manual-Std___Mutex___atomicallyOnce) condvar pred k` runs `k`, waiting on `condvar` until `pred` returns true.
Both `k` and `pred` have access to the mutex's state.

Calling `mutex.atomicallyOnce` while already holding the underlying `BaseMutex` in the same thread
is undefined behavior. If this is unavoidable in your code, consider using `RecursiveMutex`.

def

```lean
[Std.AtomicT]](#manual-Std___AtomicT) (σ : Type) (m : Type → Type) (α : Type) : Type



[Std.AtomicT]](#manual-Std___AtomicT) (σ : Type) (m : Type → Type)
  (α : Type) : Type
```

`AtomicT α m` is the monad that can be atomically executed inside mutual exclusion primitives like
`Mutex α` with outside monad `m`.
The action has access to the state `α` of the mutex (via `get` and `[set]](#manual-MonadStateOf___mk)`).

#### 21.11.6.3. Condition Variables {#manual-The-Lean-Language-Reference--IO--Tasks-and-Threads--Communication-Between-Tasks--Condition-Variables}

The types and functions in this section are available after importing `Std.Sync.Mutex`.

def

```lean
[Std.Condvar]](#manual-Std___Condvar) : Type



[Std.Condvar]](#manual-Std___Condvar) : Type
```

Condition variable, a synchronization primitive to be used with a `BaseMutex` or `Mutex`.

The thread that wants to modify the shared variable must:

1. Lock the `BaseMutex` or `Mutex`
2. Work on the shared variable
3. Call `Condvar.notifyOne` or `Condvar.notifyAll` after it is done. Note that this may be done
   before or after the mutex is unlocked.

If working with a `Mutex` the thread that waits on the `Condvar` can use `Mutex.atomicallyOnce`
to wait until a condition is true. If working with a `BaseMutex` it must:

1. Lock the `BaseMutex`.
2. Do one of the following:

- Use `Condvar.waitUntil` to (potentially repeatedly wait) on the condition variable until
  the condition is true.
- Implement the waiting manually by:

  1. Checking the condition
  2. Calling `Condvar.wait` which releases the `BaseMutex` and suspends execution until the
     condition variable is notified.
  3. Check the condition and resume waiting if not satisfied.

opaque

```lean
[Std.Condvar.new]](#manual-Std___Condvar___new) : [BaseIO]](#manual-BaseIO) [Std.Condvar]](#manual-Std___Condvar)



[Std.Condvar.new]](#manual-Std___Condvar___new) : [BaseIO]](#manual-BaseIO) [Std.Condvar]](#manual-Std___Condvar)
```

Creates a new condition variable.

opaque

```lean
[Std.Condvar.wait]](#manual-Std___Condvar___wait) (condvar : [Std.Condvar]](#manual-Std___Condvar)) (mutex : Std.BaseMutex) :
  [BaseIO]](#manual-BaseIO) [Unit]](#manual-Unit)



[Std.Condvar.wait]](#manual-Std___Condvar___wait) (condvar : [Std.Condvar]](#manual-Std___Condvar))
  (mutex : Std.BaseMutex) : [BaseIO]](#manual-BaseIO) [Unit]](#manual-Unit)
```

Waits until another thread calls `notifyOne` or `notifyAll`.

opaque

```lean
[Std.Condvar.notifyOne]](#manual-Std___Condvar___notifyOne) (condvar : [Std.Condvar]](#manual-Std___Condvar)) : [BaseIO]](#manual-BaseIO) [Unit]](#manual-Unit)



[Std.Condvar.notifyOne]](#manual-Std___Condvar___notifyOne)
  (condvar : [Std.Condvar]](#manual-Std___Condvar)) : [BaseIO]](#manual-BaseIO) [Unit]](#manual-Unit)
```

Wakes up a single other thread executing `wait`.

opaque

```lean
[Std.Condvar.notifyAll]](#manual-Std___Condvar___notifyAll) (condvar : [Std.Condvar]](#manual-Std___Condvar)) : [BaseIO]](#manual-BaseIO) [Unit]](#manual-Unit)



[Std.Condvar.notifyAll]](#manual-Std___Condvar___notifyAll)
  (condvar : [Std.Condvar]](#manual-Std___Condvar)) : [BaseIO]](#manual-BaseIO) [Unit]](#manual-Unit)
```

Wakes up all other threads executing `wait`.

def

```lean
[Std.Condvar.waitUntil.{u_1}]](#manual-Std___Condvar___waitUntil) {m : Type → Type u_1} [[Monad]](#manual-Monad___mk) m]
  [[MonadLiftT]](#manual-MonadLiftT___mk) [BaseIO]](#manual-BaseIO) m] (condvar : [Std.Condvar]](#manual-Std___Condvar)) (mutex : Std.BaseMutex)
  (pred : m [Bool]](#manual-Bool___false)) : m [Unit]](#manual-Unit)



[Std.Condvar.waitUntil.{u_1}]](#manual-Std___Condvar___waitUntil)
  {m : Type → Type u_1} [[Monad]](#manual-Monad___mk) m]
  [[MonadLiftT]](#manual-MonadLiftT___mk) [BaseIO]](#manual-BaseIO) m]
  (condvar : [Std.Condvar]](#manual-Std___Condvar))
  (mutex : Std.BaseMutex)
  (pred : m [Bool]](#manual-Bool___false)) : m [Unit]](#manual-Unit)
```

Waits on the condition variable until the predicate is true.

---



## Iterators {#manual-iterators}

> 📄 Source: https://lean-lang.org/doc/reference/latest/Iterators/

An *iterator* provides sequential access to each element of some source of data.
Typical iterators allow the elements in a collection, such as a list, array, or `[TreeMap]](#manual-Std___TreeMap)` to be accessed one by one, but they can also provide access to data by carrying out some [monadic]](#manual---tech-term-Monad) effect, such as reading files.
Iterators provide a common interface to all of these operations.
Code that is written to the iterator API can be agnostic as to the source of the data.

Each iterator maintains an internal state that enables it to determine the next value.
Because Lean is a pure functional language, consuming an iterator does not invalidate it, but instead copies it with an updated state.
As usual, [reference counting]](#manual---tech-term-reference-counting) is used to optimize programs that use values only once into programs that destructively modify values.

To use iterators, import `Std.Data.Iterators`.

**Example: Mixing Collections**

Combining a list and an array using `[List.zip]](#manual-List___zip)` or `[Array.zip]](#manual-Array___zip)` would ordinarily require converting one of them into the other collection.
Using iterators, they can be processed without conversion:

```lean
def colors : [Array]](#manual-Array___mk) [String]](#manual-String___ofByteArray) := #["purple", "gray", "blue"]
def codes : [List]](#manual-List___nil) [String]](#manual-String___ofByteArray) := ["aa27d1", "a0a0a0", "0000c5"]
[#eval]](#manual-Lean___Parser___Command___eval) [colors]](#manual-colors-_LPAR_in-Mixing-Collections_RPAR_).[iter]](#manual-Array___iter).[zip](https://lean-lang.org/doc/reference/latest/Iterators/Iterator-Combinators/#Std___Iter___zip) [codes]](#manual-codes-_LPAR_in-Mixing-Collections_RPAR_).[iter]](#manual-List___iter) |>.[toArray](https://lean-lang.org/doc/reference/latest/Iterators/Consuming-Iterators/#Std___Iter___toArray)
```

```lean
[#[]](#manual-List___toArray)[(]](#manual-Prod___mk)"purple"[,]](#manual-Prod___mk) "aa27d1"[)]](#manual-Prod___mk)[,]](#manual-List___nil) [(]](#manual-Prod___mk)"gray"[,]](#manual-Prod___mk) "a0a0a0"[)]](#manual-Prod___mk)[,]](#manual-List___nil) [(]](#manual-Prod___mk)"blue"[,]](#manual-Prod___mk) "0000c5"[)]](#manual-Prod___mk)[]](https://lean-lang.org/doc/reference/latest/Basic-Types/Linked-Lists/#List___toArray)
```

**Example: Avoiding Intermediate Structures**

In this example, an array of colors and a list of color codes are combined.
The program separates three intermediate stages:

1. The names and codes are combined into pairs.
2. The pairs are transformed into readable strings.
3. The strings are combined with newlines.

```lean
def colors : [Array]](#manual-Array___mk) [String]](#manual-String___ofByteArray) := #["purple", "gray", "blue"]
def codes : [List]](#manual-List___nil) [String]](#manual-String___ofByteArray) := ["aa27d1", "a0a0a0", "0000c5"]
def go : [IO]](#manual-IO) [Unit]](#manual-Unit) := [do]](#manual-Lean___Parser___Term___do)
let colorCodes := [colors]](#manual-colors-_LPAR_in-Avoiding-Intermediate-Structures_RPAR_).[iter]](#manual-Array___iter).[zip](https://lean-lang.org/doc/reference/latest/Iterators/Iterator-Combinators/#Std___Iter___zip) [codes]](#manual-codes-_LPAR_in-Avoiding-Intermediate-Structures_RPAR_).[iter]](#manual-List___iter)
let colorCodes := colorCodes.[map](https://lean-lang.org/doc/reference/latest/Iterators/Iterator-Combinators/#Std___Iter___map) fun (name, code) =>
s!"{name} ↦ #{code}"
let colorCodes := colorCodes.[fold](https://lean-lang.org/doc/reference/latest/Iterators/Consuming-Iterators/#Std___Iter___fold) (init := "") fun x y =>
[if]](#manual-termIfThenElse) x.[isEmpty]](#manual-String___isEmpty) [then]](#manual-termIfThenElse) y [else]](#manual-termIfThenElse) x ++ "\n" ++ y
[IO.println]](#manual-IO___println) colorCodes
[#eval]](#manual-Lean___Parser___Command___eval) [go]](#manual-go-_LPAR_in-Avoiding-Intermediate-Structures_RPAR_)
```

```lean
purple ↦ #aa27d1
gray ↦ #a0a0a0
blue ↦ #0000c5
```

The intermediate stages of the computation do not allocate new data structures.
Instead, all the steps of the transformation are fused into a single loop, with `[Iter.fold](https://lean-lang.org/doc/reference/latest/Iterators/Consuming-Iterators/#Std___Iter___fold)` carrying out one step at a time.
In each step, a single color and color code are combined into a pair, rewritten to a string, and added to the result string.

The Lean standard library provides three kinds of iterator operations.
*Producers* create a new iterator from some source of data.
They determine which data is to be returned by an iterator, and how this data is to be computed, but they are not in control of *when* the computations occur.
*Consumers* use the data in an iterator for some purpose.
Consumers request the iterator's data, and the iterator computes only enough data to satisfy a consumer's requests.
*Combinators* are both consumers and producers: they create new iterators from existing iterators.
Examples include `[Iter.map](https://lean-lang.org/doc/reference/latest/Iterators/Iterator-Combinators/#Std___Iter___map)` and `[Iter.filter](https://lean-lang.org/doc/reference/latest/Iterators/Iterator-Combinators/#Std___Iter___filter)`.
The resulting iterators produce data by consuming their underlying iterators, and do not actually iterate over the underlying collection until they themselves are consumed.

Each built-in collection for which it makes sense to do so can be iterated over.
In other words, the collection libraries include iterator [producers]](#manual---tech-term-Producers).
By convention, a collection type `Coll` provides a function `Coll.iter` that returns an iterator over the elements of a collection.
Examples include `[List.iter]](#manual-List___iter)`, `[Array.iter]](#manual-Array___iter)`, and `[TreeMap.iter]](#manual-Std___TreeMap___iter)`.
Additionally, other built-in types such as ranges support iteration using the same convention.

1. [22.1. Run-Time Considerations](https://lean-lang.org/doc/reference/latest/Iterators/Run-Time-Considerations/#The-Lean-Language-Reference--Iterators--Run-Time-Considerations)
2. [22.2. Iterator Definitions](https://lean-lang.org/doc/reference/latest/Iterators/Iterator-Definitions/#The-Lean-Language-Reference--Iterators--Iterator-Definitions)
3. [22.3. Consuming Iterators](https://lean-lang.org/doc/reference/latest/Iterators/Consuming-Iterators/#The-Lean-Language-Reference--Iterators--Consuming-Iterators)
4. [22.4. Iterator Combinators](https://lean-lang.org/doc/reference/latest/Iterators/Iterator-Combinators/#The-Lean-Language-Reference--Iterators--Iterator-Combinators)
5. [22.5. Reasoning About Iterators](https://lean-lang.org/doc/reference/latest/Iterators/Reasoning-About-Iterators/#The-Lean-Language-Reference--Iterators--Reasoning-About-Iterators)

---



## Iterators — 22.1. Run-Time Considerations {#manual-iterators-221-run-time-considerations}

> 📄 Source: https://lean-lang.org/doc/reference/latest/Iterators/Run-Time-Considerations/

For many use cases, using iterators can give a performance benefit by avoiding allocating intermediate data structures.
Without iterators, zipping a list with an array requires first converting one of them to the other type, allocating an intermediate structure, and then using the appropriate `[zip]](#manual-List___zip)` function.
Using iterators, the intermediate structure can be avoided.

When an iterator is consumed, the resulting computation should be thought of as a single loop, even if the iterator itself is built using combinators from a number of underlying iterators.
One step of the loop may carry out multiple steps from the underlying iterators.
In many cases, the Lean compiler can optimize iterator computations, removing the intermediate overhead, but this is not guaranteed.
When profiling shows that significant time is taken by a tight loop that involves multiple sources of data, it can be necessary to inspect the compiler's IR to see whether the iterators' operations were fused.
In particular, if the IR contains many pattern matches over steps, then it can be a sign of a failure to inline or specialize.
If this is the case, it may be necessary to write a tail-recursive function by hand rather than using the higher-level API.

---



## Iterators — 22.2. Iterator Definitions {#manual-iterators-222-iterator-definitions}

> 📄 Source: https://lean-lang.org/doc/reference/latest/Iterators/Iterator-Definitions/

Iterators may be either monadic or pure, and they may be finite, productive, or potentially infinite.
*Monadic* iterators use side effects in some [monad]](#manual---tech-term-Monad) to emit each value, and must therefore be used in the monad, while *pure* iterators do not require side effects.
For example, iterating over all files in a directory requires the `[IO]](#manual-IO)` monad.
Pure iterators have type `[Iter]](#manual-Std___Iter___mk)`, while monadic iterators are represented by `[IterM]](#manual-Std___IterM___mk)`.

structure

```lean
[Std.Iter.{w}]](#manual-Std___Iter___mk) {α : Type w} (β : Type w) : Type w



[Std.Iter.{w}]](#manual-Std___Iter___mk) {α : Type w} (β : Type w) :
  Type w
```

An iterator that sequentially emits values of type `β`. It may be finite
or infinite.

See the root module `Std.Data.Iterators` for a more comprehensive overview over the iterator
framework.

See `Std.Data.Iterators.Producers` for ways to iterate over common data structures.
By convention, the monadic iterator associated with an object can be obtained via dot notation.
For example, `[List.iterM]](#manual-List___iterM) [IO]](#manual-IO)` creates an iterator over a list in the monad `[IO]](#manual-IO)`.

See `Init.Data.Iterators.Consumers` for ways to use an iterator. For example, `it.toList` will
convert an iterator `it` into a list and `it.ensureTermination.toList` guarantees that this
operation will terminate, given a proof that the iterator is finite.
It is also always possible to manually iterate using
`it.step`, relying on the termination measures `it.finitelyManySteps` and `it.finitelyManySkips`.

See `[IterM]](#manual-Std___IterM___mk)` for iterators that operate in a monad.

Internally, `[Iter]](#manual-Std___Iter___mk) β` wraps an element of type `α` containing state information.
The type `α` determines the implementation of the iterator using a typeclass mechanism.
The concrete typeclass implementing the iterator is `[Iterator]](#manual-Std___Iterator___mk) α m β`.

When using combinators, `α` can become very complicated. It is an implicit parameter
of `α` so that the pretty printer will not print this large type by default. If a declaration
returns an iterator, the following will not work:

```
def x : Iter Nat := [1, 2, 3].iter
```

Instead the declaration type needs to be completely omitted:

```
def x := [1, 2, 3].iter

-- if you want to ensure that `x` is an iterator emitting `Nat`
def x := ([1, 2, 3].iter : Iter Nat)
```

Constructor

```lean
[Std.Iter.mk]](#manual-Std___Iter___mk).{w}
```

Fields

```lean
internalState : α
```

Internal implementation detail of the iterator.

structure

```lean
[Std.IterM.{w, w'}]](#manual-Std___IterM___mk) {α : Type w} (m : Type w → Type w') (β : Type w) :
  Type w



[Std.IterM.{w, w'}]](#manual-Std___IterM___mk) {α : Type w}
  (m : Type w → Type w') (β : Type w) :
  Type w
```

An iterator that sequentially emits values of type `β` in the monad `m`. It may be finite
or infinite.

See the root module `Std.Data.Iterators` for a more comprehensive overview over the iterator
framework.

See `Std.Data.Iterators.Producers` for ways to iterate over common data structures.
By convention, the monadic iterator associated with an object can be obtained via dot notation.
For example, `[List.iterM]](#manual-List___iterM) [IO]](#manual-IO)` creates an iterator over a list in the monad `[IO]](#manual-IO)`.

See `Init.Data.Iterators.Consumers` for ways to use an iterator. For example, `it.toList` will
convert an iterator `it` into a list and `it.ensureTermination.toList` guarantees that this
operation will terminate, given a proof that the iterator is finite.
It is also always possible to manually iterate using
`it.step`, relying on the termination measures `it.finitelyManySteps` and `it.finitelyManySkips`.

See `[Iter]](#manual-Std___Iter___mk)` for a more convenient interface in case that no monadic effects are needed (`m = [Id]](#manual-Id)`).

Internally, `[IterM]](#manual-Std___IterM___mk) m β` wraps an element of type `α` containing state information.
The type `α` determines the implementation of the iterator using a typeclass mechanism.
The concrete typeclass implementing the iterator is `[Iterator]](#manual-Std___Iterator___mk) α m β`.

When using combinators, `α` can become very complicated. It is an implicit parameter
of `α` so that the pretty printer will not print this large type by default. If a declaration
returns an iterator, the following will not work:

```
def x : IterM IO Nat := [1, 2, 3].iterM IO
```

Instead the declaration type needs to be completely omitted:

```
def x := [1, 2, 3].iterM IO

-- if you want to ensure that `x` is an iterator in `IO` emitting `Nat`
def x := ([1, 2, 3].iterM IO : IterM IO Nat)
```

Constructor

```lean
Std.IterM.mk.{w, w'}
```

Wraps the state of an iterator into an `[Iter]](#manual-Std___Iter___mk)` object.

Fields

```lean
internalState : α
```

Internal implementation detail of the iterator.

The types `[Iter]](#manual-Std___Iter___mk)` and `[IterM]](#manual-Std___IterM___mk)` are merely wrappers around an internal state.
This inner state type is the implicit parameter to the iterator types.
For basic producer iterators, like the one that results from `[List.iter]](#manual-List___iter)`, this type is fairly simple; however, iterators that result from [combinators]](#manual---tech-term-Combinators) use polymorphic state types that can grow large.
Because Lean elaborates the specified return type of a function before elaborating its body, it may not be possible to automatically determine the internal state type of an iterator type returned by a function.
In these cases, it can be helpful to omit the return type from the signature and instead place a type annotation on the definition's body, which allows the specific iterator combinators invoked from the body to be used to determine the state type.

**Example: Iterator State Types**

Writing the internal state type explicitly for list and array iterators is feasible:

```lean
def reds := ["red", "crimson"]
example : @[Iter]](#manual-Std___Iter___mk) (ListIterator [String]](#manual-String___ofByteArray)) [String]](#manual-String___ofByteArray) := [reds]](#manual-reds-_LPAR_in-Iterator-State-Types_RPAR_).[iter]](#manual-List___iter)
example : @[Iter]](#manual-Std___Iter___mk) (ArrayIterator [String]](#manual-String___ofByteArray)) [String]](#manual-String___ofByteArray) := [reds]](#manual-reds-_LPAR_in-Iterator-State-Types_RPAR_).[toArray]](#manual-List___toArray).[iter]](#manual-Array___iter)
```

However, the internal state type of a use of the `[Iter.map](https://lean-lang.org/doc/reference/latest/Iterators/Iterator-Combinators/#Std___Iter___map)` combinator is quite complicated:

```lean
example :
@[Iter]](#manual-Std___Iter___mk)
(Map (ListIterator [String]](#manual-String___ofByteArray)) [Id]](#manual-Id) [Id]](#manual-Id) @id fun x : [String]](#manual-String___ofByteArray) =>
[pure]](#manual-Pure___mk) x.[length]](#manual-String___length))
[Nat]](#manual-Nat___zero) :=
[reds]](#manual-reds-_LPAR_in-Iterator-State-Types_RPAR_).[iter]](#manual-List___iter).[map](https://lean-lang.org/doc/reference/latest/Iterators/Iterator-Combinators/#Std___Iter___map) [String.length]](#manual-String___length)
```

Omitting the state type leads to an error:

```lean
example : [Iter]](#manual-Std___Iter___mk) [Nat]](#manual-Nat___zero) := [reds]](#manual-reds-_LPAR_in-Iterator-State-Types_RPAR_).[iter]](#manual-List___iter).[map](https://lean-lang.org/doc/reference/latest/Iterators/Iterator-Combinators/#Std___Iter___map) [String.length]](#manual-String___length)
```

```lean
don't know how to synthesize implicit argument `α`
  @[Iter]](#manual-Std___Iter___mk) ?m.1 [Nat]](#manual-Nat___zero)
context:
⊢ Type

Note: Because this declaration's type has been explicitly provided, all parameter types and holes (e.g., `_`) in its header are resolved before its body is processed; information from the declaration body cannot be used to infer what these values should be
```

Rather than writing the state type by hand, it can be convenient to omit the return type and instead provide the annotation around the term:

```lean
example := ([reds]](#manual-reds-_LPAR_in-Iterator-State-Types_RPAR_).[iter]](#manual-List___iter).[map](https://lean-lang.org/doc/reference/latest/Iterators/Iterator-Combinators/#Std___Iter___map) [String.length]](#manual-String___length) : [Iter]](#manual-Std___Iter___mk) [Nat]](#manual-Nat___zero))
example :=
show [Iter]](#manual-Std___Iter___mk) [Nat]](#manual-Nat___zero) from
[reds]](#manual-reds-_LPAR_in-Iterator-State-Types_RPAR_).[iter]](#manual-List___iter).[map](https://lean-lang.org/doc/reference/latest/Iterators/Iterator-Combinators/#Std___Iter___map) [String.length]](#manual-String___length)
```

The actual process of iteration consists of producing a sequence of iteration steps when requested.
Each step returns an updated iterator with a new internal state along with either a data value (in `[IterStep.yield]](#manual-Std___IterStep___yield)`), an indicator that the caller should request a data value again (`[IterStep.skip]](#manual-Std___IterStep___yield)`), or an indication that iteration is finished (`[IterStep.done]](#manual-Std___IterStep___yield)`).
Without the ability to `[skip]](#manual-Std___IterStep___yield)`, it would be much more difficult to work with iterator combinators such as `[Iter.filter](https://lean-lang.org/doc/reference/latest/Iterators/Iterator-Combinators/#Std___Iter___filter)` that do not yield values for all of those yielded by the underlying iterator.
With `[skip]](#manual-Std___IterStep___yield)`, the implementation of `[filter](https://lean-lang.org/doc/reference/latest/Iterators/Iterator-Combinators/#Std___Iter___filter)` doesn't need to worry about whether the underlying iterator is [finite]](#manual---tech-term-Finite) in order to be a well-defined function, and reasoning about its finiteness can be carried out in separate proofs.
Additionally, `[filter](https://lean-lang.org/doc/reference/latest/Iterators/Iterator-Combinators/#Std___Iter___filter)` would require an inner loop, which is much more difficult for the compiler to inline.

inductive type

```lean
[Std.IterStep.{u_1, u_2}]](#manual-Std___IterStep___yield) (α : Sort u_1) (β : Sort u_2) :
  Sort (max (max 1 u_1) u_2)



[Std.IterStep.{u_1, u_2}]](#manual-Std___IterStep___yield) (α : Sort u_1)
  (β : Sort u_2) :
  Sort (max (max 1 u_1) u_2)
```

`[IterStep]](#manual-Std___IterStep___yield) α β` represents a step taken by an iterator (`[Iter]](#manual-Std___Iter___mk) β` or `[IterM]](#manual-Std___IterM___mk) m β`).

Constructors

```lean
[Std.IterStep.yield.{u_1, u_2}]](#manual-Std___IterStep___yield) {α : Sort u_1} {β : Sort u_2}
  (it : α) (out : β) : [IterStep]](#manual-Std___IterStep___yield) α β
```

`[IterStep.yield]](#manual-Std___IterStep___yield) it out` describes the situation that an iterator emits `out` and provides `it`
as the succeeding iterator.

```lean
[Std.IterStep.skip.{u_1, u_2}]](#manual-Std___IterStep___yield) {α : Sort u_1} {β : Sort u_2}
  (it : α) : [IterStep]](#manual-Std___IterStep___yield) α β
```

`[IterStep.skip]](#manual-Std___IterStep___yield) it` describes the situation that an iterator does not emit anything in this
iteration and provides `it'` as the succeeding iterator.

Allowing `[skip]](#manual-Std___IterStep___yield)` steps is necessary to generate efficient code from a loop over an iterator.

```lean
[Std.IterStep.done.{u_1, u_2}]](#manual-Std___IterStep___yield) {α : Sort u_1} {β : Sort u_2} :
  [IterStep]](#manual-Std___IterStep___yield) α β
```

`[IterStep.done]](#manual-Std___IterStep___yield)` describes the situation that an iterator has finished and will neither emit
more values nor cause any monadic effects. In this case, no succeeding iterator is provided.

Steps taken by `[Iter]](#manual-Std___Iter___mk)` and `[IterM]](#manual-Std___IterM___mk)` are respectively represented by the types `[Iter.Step]](#manual-Std___Iter___Step)` and `[IterM.Step]](#manual-Std___IterM___Step)`.
Both types of step are wrappers around `[IterStep]](#manual-Std___IterStep___yield)` that include [additional proofs]](#manual-iterator-plausibility) that are used to track termination behavior.

def

```lean
[Std.Iter.Step.{w}]](#manual-Std___Iter___Step) {α β : Type w} [[Iterator]](#manual-Std___Iterator___mk) α [Id]](#manual-Id) β] (it : [Iter]](#manual-Std___Iter___mk) β) :
  Type w



[Std.Iter.Step.{w}]](#manual-Std___Iter___Step) {α β : Type w}
  [[Iterator]](#manual-Std___Iterator___mk) α [Id]](#manual-Id) β] (it : [Iter]](#manual-Std___Iter___mk) β) : Type w
```

The type of the step object returned by `[Iter.step](https://lean-lang.org/doc/reference/latest/Iterators/Consuming-Iterators/#Std___Iter___step)`, containing an `[IterStep]](#manual-Std___IterStep___yield)`
and a proof that this is a plausible step for the given iterator.

def

```lean
[Std.IterM.Step.{w, w'}]](#manual-Std___IterM___Step) {α : Type w} {m : Type w → Type w'} {β : Type w}
  [[Iterator]](#manual-Std___Iterator___mk) α m β] (it : [IterM]](#manual-Std___IterM___mk) m β) : Type w



[Std.IterM.Step.{w, w'}]](#manual-Std___IterM___Step) {α : Type w}
  {m : Type w → Type w'} {β : Type w}
  [[Iterator]](#manual-Std___Iterator___mk) α m β] (it : [IterM]](#manual-Std___IterM___mk) m β) :
  Type w
```

The type of the step object returned by `[IterM.step](https://lean-lang.org/doc/reference/latest/Iterators/Consuming-Iterators/#Std___IterM___step)`, containing an `[IterStep]](#manual-Std___IterStep___yield)`
and a proof that this is a plausible step for the given iterator.

Steps are produced from iterators using `[Iterator.step]](#manual-Std___Iterator___mk)`, which is a method of the `[Iterator]](#manual-Std___Iterator___mk)` type class.
`[Iterator]](#manual-Std___Iterator___mk)` is used for both pure and monadic iterators; pure iterators can be completely polymorphic in the choice of monad, which allows callers to instantiate it with `[Id]](#manual-Id)`.

type class

```lean
[Std.Iterator.{w, w'}]](#manual-Std___Iterator___mk) (α : Type w) (m : Type w → Type w')
  (β : [outParam]](#manual-outParam) (Type w)) : Type (max w w')



[Std.Iterator.{w, w'}]](#manual-Std___Iterator___mk) (α : Type w)
  (m : Type w → Type w')
  (β : [outParam]](#manual-outParam) (Type w)) :
  Type (max w w')
```

The step function of an iterator in `[Iter]](#manual-Std___Iter___mk) (α := α) β` or `[IterM]](#manual-Std___IterM___mk) (α := α) m β`.

In order to allow intrinsic termination proofs when iterating with the `step` function, the
step object is bundled with a proof that it is a "plausible" step for the given current iterator.

Instance Constructor

```lean
[Std.Iterator.mk]](#manual-Std___Iterator___mk).{w, w'}
```

Methods

```lean
IsPlausibleStep : [IterM]](#manual-Std___IterM___mk) m β → [IterStep]](#manual-Std___IterStep___yield) ([IterM]](#manual-Std___IterM___mk) m β) β → Prop
```

A relation that governs the allowed steps from a given iterator.

The "plausible" steps are those which make sense for a given state; plausibility can ensure
properties such as the successor iterator being drawn from the same collection, that an iterator
resulting from a skip will return the same next value, or that the next item yielded is next one
in the original collection.

```lean
step : (it : [IterM]](#manual-Std___IterM___mk) m β) → m ([Std.Shrink]](#manual-Std___Shrink) ([PlausibleIterStep]](#manual-Std___PlausibleIterStep) ([Iterator.IsPlausibleStep]](#manual-Std___Iterator___mk) it)))
```

Carries out a step of iteration.

### 22.2.1. Plausibility {#manual-iterator-plausibility}

In addition to the step function, instances of `[Iterator]](#manual-Std___Iterator___mk)` include a relation `[Iterator.IsPlausibleStep]](#manual-Std___Iterator___mk)`.
This relation exists because most iterators both maintain invariants over their internal state and yield values in a predictable manner.
For example, array iterators track both an array and a current index into it.
Stepping an array iterator results in an iterator over the same underlying array; it yields a value when the index is small enough, or is done otherwise.
The *plausible steps* from an iterator state are those which are related to it via the iterator's implementation of `[IsPlausibleStep]](#manual-Std___Iterator___mk)`.
Tracking plausibility at the logical level makes it feasible to reason about termination behavior for monadic iterators.

Both `[Iter.Step]](#manual-Std___Iter___Step)` and `[IterM.Step]](#manual-Std___IterM___Step)` are defined in terms of `[PlausibleIterStep]](#manual-Std___PlausibleIterStep)`; thus, both types can be used with [leading dot notation]](#manual---tech-term-leading-dot-notation) for its namespace.
An `[Iter.Step]](#manual-Std___Iter___Step)` or `[IterM.Step]](#manual-Std___IterM___Step)` can be analyzed using the three [match pattern functions]](#manual-match_pattern-functions) `[PlausibleIterStep.yield]](#manual-Std___PlausibleIterStep___yield)`, `[PlausibleIterStep.skip]](#manual-Std___PlausibleIterStep___skip)`, and `[PlausibleIterStep.done]](#manual-Std___PlausibleIterStep___done)`.
These functions pair the information in the underlying `[IterStep]](#manual-Std___IterStep___yield)` with the surrounding proof object.

def

```lean
[Std.PlausibleIterStep.{u, w}]](#manual-Std___PlausibleIterStep) {α : Type u} {β : Type w}
  (IsPlausibleStep : [IterStep]](#manual-Std___IterStep___yield) α β → Prop) : Type (max 0 u w)



[Std.PlausibleIterStep.{u, w}]](#manual-Std___PlausibleIterStep) {α : Type u}
  {β : Type w}
  (IsPlausibleStep :
    [IterStep]](#manual-Std___IterStep___yield) α β → Prop) :
  Type (max 0 u w)
```

A variant of `[IterStep]](#manual-Std___IterStep___yield)` that bundles the step together with a proof that it is "plausible".
The plausibility predicate will later be chosen to assert that a state is a plausible successor
of another state. Having this proof bundled up with the step is important for termination proofs.

See `[IterM.Step]](#manual-Std___IterM___Step)` and `[Iter.Step]](#manual-Std___Iter___Step)` for the concrete choice of the plausibility predicate.

def

```lean
[Std.PlausibleIterStep.yield.{u, w}]](#manual-Std___PlausibleIterStep___yield) {α : Type u} {β : Type w}
  {IsPlausibleStep : [IterStep]](#manual-Std___IterStep___yield) α β → Prop} (it' : α) (out : β)
  (h : IsPlausibleStep ([IterStep.yield]](#manual-Std___IterStep___yield) it' out)) :
  [PlausibleIterStep]](#manual-Std___PlausibleIterStep) IsPlausibleStep



[Std.PlausibleIterStep.yield.{u, w}]](#manual-Std___PlausibleIterStep___yield)
  {α : Type u} {β : Type w}
  {IsPlausibleStep : [IterStep]](#manual-Std___IterStep___yield) α β → Prop}
  (it' : α) (out : β)
  (h :
    IsPlausibleStep
      ([IterStep.yield]](#manual-Std___IterStep___yield) it' out)) :
  [PlausibleIterStep]](#manual-Std___PlausibleIterStep) IsPlausibleStep
```

Match pattern for the `yield` case. See also `[IterStep.yield]](#manual-Std___IterStep___yield)`.

def

```lean
[Std.PlausibleIterStep.skip.{u, w}]](#manual-Std___PlausibleIterStep___skip) {α : Type u} {β : Type w}
  {IsPlausibleStep : [IterStep]](#manual-Std___IterStep___yield) α β → Prop} (it' : α)
  (h : IsPlausibleStep ([IterStep.skip]](#manual-Std___IterStep___yield) it')) :
  [PlausibleIterStep]](#manual-Std___PlausibleIterStep) IsPlausibleStep



[Std.PlausibleIterStep.skip.{u, w}]](#manual-Std___PlausibleIterStep___skip)
  {α : Type u} {β : Type w}
  {IsPlausibleStep : [IterStep]](#manual-Std___IterStep___yield) α β → Prop}
  (it' : α)
  (h :
    IsPlausibleStep ([IterStep.skip]](#manual-Std___IterStep___yield) it')) :
  [PlausibleIterStep]](#manual-Std___PlausibleIterStep) IsPlausibleStep
```

Match pattern for the `[skip]](#manual-skip)` case. See also `[IterStep.skip]](#manual-Std___IterStep___yield)`.

def

```lean
[Std.PlausibleIterStep.done.{u, w}]](#manual-Std___PlausibleIterStep___done) {α : Type u} {β : Type w}
  {IsPlausibleStep : [IterStep]](#manual-Std___IterStep___yield) α β → Prop}
  (h : IsPlausibleStep [IterStep.done]](#manual-Std___IterStep___yield)) :
  [PlausibleIterStep]](#manual-Std___PlausibleIterStep) IsPlausibleStep



[Std.PlausibleIterStep.done.{u, w}]](#manual-Std___PlausibleIterStep___done)
  {α : Type u} {β : Type w}
  {IsPlausibleStep : [IterStep]](#manual-Std___IterStep___yield) α β → Prop}
  (h : IsPlausibleStep [IterStep.done]](#manual-Std___IterStep___yield)) :
  [PlausibleIterStep]](#manual-Std___PlausibleIterStep) IsPlausibleStep
```

Match pattern for the `[done]](#manual-done)` case. See also `[IterStep.done]](#manual-Std___IterStep___yield)`.

### 22.2.2. Finite and Productive Iterators {#manual-The-Lean-Language-Reference--Iterators--Iterator-Definitions--Finite-and-Productive-Iterators}

Not all iterators are guaranteed to return a finite number of results; it is perfectly sensible to iterate over all of the natural numbers.
Similarly, not all iterators are guaranteed to either return a single result or terminate; iterators may be defined using arbitrary programs.
Thus, Lean divides iterators into three termination classes:

- *Finite* iterators are guaranteed to finish iterating after a finite number of steps. These iterators have a `[Finite]](#manual-Std___Iterators___Finite___mk)` instance.
- *Productive* iterators are guaranteed to yield a value or terminate in finitely many steps, but they may yield infinitely many values. These iterators have a `[Productive]](#manual-Std___Iterators___Productive___mk)` instance.
- All other iterators, whose termination behavior is unknown. These iterators have neither instance.

All finite iterators are necessarily productive.

type class

```lean
[Std.Iterators.Finite.{w, w'}]](#manual-Std___Iterators___Finite___mk) (α : Type w) (m : Type w → Type w')
  {β : Type w} [[Iterator]](#manual-Std___Iterator___mk) α m β] : Prop



[Std.Iterators.Finite.{w, w'}]](#manual-Std___Iterators___Finite___mk) (α : Type w)
  (m : Type w → Type w') {β : Type w}
  [[Iterator]](#manual-Std___Iterator___mk) α m β] : Prop
```

`[Finite]](#manual-Std___Iterators___Finite___mk) α m` asserts that `[IterM]](#manual-Std___IterM___mk) (α := α) m` terminates after finitely many steps. Technically,
this means that the relation of plausible successors is well-founded.
Given this typeclass, termination proofs for well-founded recursion over an iterator `it` can use
`it.finitelyManySteps` as a termination measure.

Instance Constructor

```lean
[Std.Iterators.Finite.mk]](#manual-Std___Iterators___Finite___mk).{w, w'}
```

Methods

```lean
wf : [WellFounded]](#manual-WellFounded___intro) IterM.IsPlausibleSuccessorOf
```

The relation of plausible successors is well-founded.

type class

```lean
[Std.Iterators.Productive.{u_1, u_2}]](#manual-Std___Iterators___Productive___mk) (α : Type u_1)
  (m : Type u_1 → Type u_2) {β : Type u_1} [[Iterator]](#manual-Std___Iterator___mk) α m β] : Prop



[Std.Iterators.Productive.{u_1, u_2}]](#manual-Std___Iterators___Productive___mk)
  (α : Type u_1) (m : Type u_1 → Type u_2)
  {β : Type u_1} [[Iterator]](#manual-Std___Iterator___mk) α m β] : Prop
```

`[Productive]](#manual-Std___Iterators___Productive___mk) α m` asserts that `[IterM]](#manual-Std___IterM___mk) (α := α) m` terminates or emits a value after finitely many
skips. Technically, this means that the relation of plausible successors during skips is
well-founded.
Given this typeclass, termination proofs for well-founded recursion over an iterator `it` can use
`it.finitelyManySkips` as a termination measure.

Instance Constructor

```lean
[Std.Iterators.Productive.mk]](#manual-Std___Iterators___Productive___mk).{u_1, u_2}
```

Methods

```lean
wf : [WellFounded]](#manual-WellFounded___intro) IterM.IsPlausibleSkipSuccessorOf
```

The relation of plausible successors during skips is well-founded.

Lean's standard library provides many functions that iterate over an iterator. These consumer functions usually do not
make any assumptions about the underlying iterator. In particular, such functions may run forever for certain iterators.

Sometimes, it is of utmost importance that a function does terminate.
For these cases, the combinator `[Iter.ensureTermination]](#manual-Std___Iter___ensureTermination)` results in an iterator that provides variants of consumers that are guaranteed to terminate.
They usually require proof that the involved iterator is finite.

def

```lean
[Std.Iter.ensureTermination.{w}]](#manual-Std___Iter___ensureTermination) {α β : Type w} (it : [Iter]](#manual-Std___Iter___mk) β) :
  Iter.Total β



[Std.Iter.ensureTermination.{w}]](#manual-Std___Iter___ensureTermination)
  {α β : Type w} (it : [Iter]](#manual-Std___Iter___mk) β) :
  Iter.Total β
```

For an iterator `it`, `it.[ensureTermination]](#manual-Std___Iter___ensureTermination)` provides variants of consumers that always
terminate.

def

```lean
[Std.IterM.ensureTermination.{w, w'}]](#manual-Std___IterM___ensureTermination) {α β : Type w}
  {m : Type w → Type w'} (it : [IterM]](#manual-Std___IterM___mk) m β) : IterM.Total m β



[Std.IterM.ensureTermination.{w, w'}]](#manual-Std___IterM___ensureTermination)
  {α β : Type w} {m : Type w → Type w'}
  (it : [IterM]](#manual-Std___IterM___mk) m β) : IterM.Total m β
```

For an iterator `it`, `it.[ensureTermination]](#manual-Std___IterM___ensureTermination)` provides variants of consumers that always
terminate.

**Example: Iterating Over Nat**

To write an iterator that yields each natural number in turn, the first step is to implement its internal state.
This iterator only needs to remember the next natural number:

```lean
structure Nats where
next : [Nat]](#manual-Nat___zero)
```

This iterator will only ever yield the next natural number.
Thus, its step function will never return `[skip]](#manual-Std___IterStep___yield)` or `[done]](#manual-Std___IterStep___yield)`.
Whenever it yields a value, the value will be the internal state's `[next]](#manual-Nats___next-_LPAR_in-Iterating-Over--Nat_RPAR_)` field, and the successor iterator's `[next]](#manual-Nats___next-_LPAR_in-Iterating-Over--Nat_RPAR_)` field will be one greater.
The `[grind]](#manual-grind)` tactic suffices to show that the step is indeed plausible:

```lean
instance [[Pure]](#manual-Pure___mk) m] : [Iterator]](#manual-Std___Iterator___mk) [Nats]](#manual-Nats-_LPAR_in-Iterating-Over--Nat_RPAR_) m [Nat]](#manual-Nat___zero) where
[IsPlausibleStep]](#manual-Std___Iterator___mk) it
| [.yield]](#manual-Std___IterStep___yield) it' n =>
n = it.[internalState]](#manual-Std___IterM___mk).[next]](#manual-Nats___next-_LPAR_in-Iterating-Over--Nat_RPAR_) ∧
it'.[internalState]](#manual-Std___IterM___mk).[next]](#manual-Nats___next-_LPAR_in-Iterating-Over--Nat_RPAR_) = n + 1
| _ => [False]](#manual-False)
[step]](#manual-Std___Iterator___mk) it :=
let n := it.[internalState]](#manual-Std___IterM___mk).[next]](#manual-Nats___next-_LPAR_in-Iterating-Over--Nat_RPAR_)
[pure]](#manual-Pure___mk) <| [.deflate]](#manual-Std___Shrink___deflate) <|
[.yield]](#manual-Std___PlausibleIterStep___yield) { it with [internalState]](#manual-Std___IterM___mk).[next]](#manual-Nats___next-_LPAR_in-Iterating-Over--Nat_RPAR_) := n + 1 } n (bym:Type → Type ?u.3inst✝:[Pure]](#manual-Pure___mk) mit:[IterM]](#manual-Std___IterM___mk) m [Nat]](#manual-Nat___zero)n:[Nat]](#manual-Nat___zero) := it.[internalState]](#manual-Std___IterM___mk).[next]](#manual-Nats___next-_LPAR_in-Iterating-Over--Nat_RPAR_)⊢ match
[IterStep.yield]](#manual-Std___IterStep___yield)
{
[internalState]](#manual-Std___IterM___mk) :=
let __src := it.[internalState]](#manual-Std___IterM___mk);
{ [next]](#manual-Nats___next-_LPAR_in-Iterating-Over--Nat_RPAR_) := n [+]](#manual-HAdd___mk) 1 } }
n with
| [IterStep.yield]](#manual-Std___IterStep___yield) it' n => n [=]](#manual-Eq___refl) it.[internalState]](#manual-Std___IterM___mk).[next]](#manual-Nats___next-_LPAR_in-Iterating-Over--Nat_RPAR_) [∧]](#manual-And___intro) it'.[internalState]](#manual-Std___IterM___mk).[next]](#manual-Nats___next-_LPAR_in-Iterating-Over--Nat_RPAR_) [=]](#manual-Eq___refl) n [+]](#manual-HAdd___mk) 1
| x => [False]](#manual-False) [grind]](#manual-grind)All goals completed! 🐙)
```

Whenever an iterator is defined, an `[IteratorLoop]](#manual-Std___IteratorLoop___mk)` instance should be provided.
They are required for most consumers of iterators such as `[Iter.toList](https://lean-lang.org/doc/reference/latest/Iterators/Consuming-Iterators/#Std___Iter___toList)` or the `for` loops.
One can use their default implementations as follows:

```lean
instance [[Pure]](#manual-Pure___mk) m] [[Monad]](#manual-Monad___mk) n] : [IteratorLoop]](#manual-Std___IteratorLoop___mk) [Nats]](#manual-Nats-_LPAR_in-Iterating-Over--Nat_RPAR_) m n :=
[.defaultImplementation]](#manual-Std___IteratorLoop___defaultImplementation)
```

This `[step]](#manual-Std___Iterator___mk)` function is productive because it never returns `[skip]](#manual-Std___IterStep___yield)`.
Thus, the proof that each chain of `[skip]](#manual-Std___IterStep___yield)`s has finite length can rely on the fact that when `it` is a `[Nats]](#manual-Nats-_LPAR_in-Iterating-Over--Nat_RPAR_)` iterator, `[Iterator.IsPlausibleStep]](#manual-Std___Iterator___mk) it ([.skip]](#manual-Std___IterStep___yield) it') = [False]](#manual-False)`:

```lean
instance [[Pure]](#manual-Pure___mk) m] : [Productive]](#manual-Std___Iterators___Productive___mk) [Nats]](#manual-Nats-_LPAR_in-Iterating-Over--Nat_RPAR_) m where
[wf]](#manual-Std___Iterators___Productive___mk) := [.intro]](#manual-WellFounded___intro) <| fun _ => [.intro]](#manual-Acc___intro) _ [nofun]](#manual-Lean___Parser___Term___nofun)
```

Because there are infinitely many `[Nat]](#manual-Nat___zero)`s, the iterator is not finite.

A `[Nats]](#manual-Nats-_LPAR_in-Iterating-Over--Nat_RPAR_)` iterator can be created using this function:

```lean
def Nats.iter : [Iter]](#manual-Std___Iter___mk) (α := [Nats]](#manual-Nats-_LPAR_in-Iterating-Over--Nat_RPAR_)) [Nat]](#manual-Nat___zero) :=
IterM.mk { [next]](#manual-Nats___next-_LPAR_in-Iterating-Over--Nat_RPAR_) := 0 } |>.[toIter](https://lean-lang.org/doc/reference/latest/Iterators/Iterator-Combinators/#Std___IterM___toIter)
```

One can print all natural numbers by running the following function:

```lean
def f : [IO]](#manual-IO) [Unit]](#manual-Unit) := [do]](#manual-Lean___Parser___Term___do)
for x in [Nats.iter]](#manual-Nats___iter-_LPAR_in-Iterating-Over--Nat_RPAR_) do
[IO.println]](#manual-IO___println) s!"{x}"
```

This function never terminates, printing all natural numbers in increasing order, one
after another.

This iterator is most useful with combinators such as `[Iter.zip](https://lean-lang.org/doc/reference/latest/Iterators/Iterator-Combinators/#Std___Iter___zip)`:

```lean
[#eval]](#manual-Lean___Parser___Command___eval) show [IO]](#manual-IO) [Unit]](#manual-Unit) from [do]](#manual-Lean___Parser___Term___do)
let xs : [List]](#manual-List___nil) [String]](#manual-String___ofByteArray) := ["cat", "dog", "pachycephalosaurus"]
for (x, y) in [Nats.iter]](#manual-Nats___iter-_LPAR_in-Iterating-Over--Nat_RPAR_).[zip](https://lean-lang.org/doc/reference/latest/Iterators/Iterator-Combinators/#Std___Iter___zip) xs.[iter]](#manual-List___iter) do
[IO.println]](#manual-IO___println) s!"{x}: {y}"
```

```lean
0: cat
1: dog
2: pachycephalosaurus
```

In contrast to the previous example, this loop terminates because `xs.iter` is a finite iterator,
One can make sure that a loop actually terminates by providing a `[Finite]](#manual-Std___Iterators___Finite___mk)` instance:

```lean
[#check]](#manual-Lean___Parser___Command___check) type_of% ([Nats.iter]](#manual-Nats___iter-_LPAR_in-Iterating-Over--Nat_RPAR_).[zip](https://lean-lang.org/doc/reference/latest/Iterators/Iterator-Combinators/#Std___Iter___zip) ["cat", "dog"].[iter]](#manual-List___iter)).[internalState]](#manual-Std___Iter___mk)
[#synth]](#manual-Lean___Parser___Command___synth) [Finite]](#manual-Std___Iterators___Finite___mk) (Zip [Nats]](#manual-Nats-_LPAR_in-Iterating-Over--Nat_RPAR_) [Id]](#manual-Id) (ListIterator [String]](#manual-String___ofByteArray)) [String]](#manual-String___ofByteArray)) [Id]](#manual-Id)
```

```lean
Zip [Nats]](#manual-Nats-_LPAR_in-Iterating-Over--Nat_RPAR_) [Id]](#manual-Id) (ListIterator [String]](#manual-String___ofByteArray)) [String]](#manual-String___ofByteArray) : Type
```

```lean
Zip.instFinite₂
```

In contrast, `Nats.iter` has no `Finite` instance because it yields infinitely many values:

```lean
[#synth]](#manual-Lean___Parser___Command___synth) [Finite]](#manual-Std___Iterators___Finite___mk) [Nats]](#manual-Nats-_LPAR_in-Iterating-Over--Nat_RPAR_) [Id]](#manual-Id)
```

```lean
failed to synthesize
  [Finite]](#manual-Std___Iterators___Finite___mk) [Nats]](#manual-Nats-_LPAR_in-Iterating-Over--Nat_RPAR_) [Id]](#manual-Id)

Hint: Additional diagnostic information may be available using the `set_option diagnostics true` command.
```

Because there are infinitely many `[Nat]](#manual-Nat___zero)`s, using `[Iter.ensureTermination]](#manual-Std___Iter___ensureTermination)` results in an error:

```lean
[#eval]](#manual-Lean___Parser___Command___eval) show [IO]](#manual-IO) [Unit]](#manual-Unit) from [do]](#manual-Lean___Parser___Term___do)
for x in [Nats.iter]](#manual-Nats___iter-_LPAR_in-Iterating-Over--Nat_RPAR_).[ensureTermination]](#manual-Std___Iter___ensureTermination) do
IO.println s!"{x}"
```

```lean
failed to synthesize instance of type class
  [ForIn]](#manual-ForIn___mk) [IO]](#manual-IO) (Iter.Total [Nat]](#manual-Nat___zero)) ?α

Hint: Type class instance resolution failures can be inspected with the `set_option trace.Meta.synthInstance true` command.
```

**Example: Iterating Over Triples**

The type `[Triple]](#manual-Triple-_LPAR_in-Iterating-Over-Triples_RPAR_)` contains three values of the same type:

```lean
structure Triple α where
fst : α
snd : α
thd : α
```

The internal state of an iterator over `[Triple]](#manual-Triple-_LPAR_in-Iterating-Over-Triples_RPAR_)` can consist of a triple paired with a current position.
This position may either be one of the fields or an indication that iteration is finished.

```lean
inductive TriplePos where
| fst | snd | thd | done
```

Positions can be used to look up elements:

```lean
def Triple.get? (xs : [Triple]](#manual-Triple-_LPAR_in-Iterating-Over-Triples_RPAR_) α) (pos : [TriplePos]](#manual-TriplePos-_LPAR_in-Iterating-Over-Triples_RPAR_)) : [Option]](#manual-Option___none) α :=
[match]](#manual-Lean___Parser___Term___match) pos [with]](#manual-Lean___Parser___Term___match)
| [.fst]](#manual-TriplePos___fst-_LPAR_in-Iterating-Over-Triples_RPAR_) => [some]](#manual-Option___none) xs.[fst]](#manual-Triple___fst-_LPAR_in-Iterating-Over-Triples_RPAR_)
| [.snd]](#manual-TriplePos___snd-_LPAR_in-Iterating-Over-Triples_RPAR_) => [some]](#manual-Option___none) xs.[snd]](#manual-Triple___snd-_LPAR_in-Iterating-Over-Triples_RPAR_)
| [.thd]](#manual-TriplePos___thd-_LPAR_in-Iterating-Over-Triples_RPAR_) => [some]](#manual-Option___none) xs.[thd]](#manual-Triple___thd-_LPAR_in-Iterating-Over-Triples_RPAR_)
| _ => [none]](#manual-Option___none)
```

Each field's position has a successor position:

```lean
@[grind, grind cases]
inductive TriplePos.Succ : [TriplePos]](#manual-TriplePos-_LPAR_in-Iterating-Over-Triples_RPAR_) → [TriplePos]](#manual-TriplePos-_LPAR_in-Iterating-Over-Triples_RPAR_) → Prop where
| fst : [Succ]](#manual-TriplePos___Succ-_LPAR_in-Iterating-Over-Triples_RPAR_) [.fst]](#manual-TriplePos___fst-_LPAR_in-Iterating-Over-Triples_RPAR_) [.snd]](#manual-TriplePos___snd-_LPAR_in-Iterating-Over-Triples_RPAR_)
| snd : [Succ]](#manual-TriplePos___Succ-_LPAR_in-Iterating-Over-Triples_RPAR_) [.snd]](#manual-TriplePos___snd-_LPAR_in-Iterating-Over-Triples_RPAR_) [.thd]](#manual-TriplePos___thd-_LPAR_in-Iterating-Over-Triples_RPAR_)
| thd : [Succ]](#manual-TriplePos___Succ-_LPAR_in-Iterating-Over-Triples_RPAR_) [.thd]](#manual-TriplePos___thd-_LPAR_in-Iterating-Over-Triples_RPAR_) [.done]](#manual-TriplePos___done-_LPAR_in-Iterating-Over-Triples_RPAR_)
```

The iterator itself pairs a triple with the position of the next element:

```lean
structure TripleIterator α where
triple : [Triple]](#manual-Triple-_LPAR_in-Iterating-Over-Triples_RPAR_) α
pos : [TriplePos]](#manual-TriplePos-_LPAR_in-Iterating-Over-Triples_RPAR_)
```

Iteration begins at `[fst]](#manual-TriplePos___fst-_LPAR_in-Iterating-Over-Triples_RPAR_)`:

```lean
def Triple.iter (xs : [Triple]](#manual-Triple-_LPAR_in-Iterating-Over-Triples_RPAR_) α) : [Iter]](#manual-Std___Iter___mk) (α := [TripleIterator]](#manual-TripleIterator-_LPAR_in-Iterating-Over-Triples_RPAR_) α) α :=
IterM.mk {[triple]](#manual-TripleIterator___triple-_LPAR_in-Iterating-Over-Triples_RPAR_) := xs, [pos]](#manual-TripleIterator___pos-_LPAR_in-Iterating-Over-Triples_RPAR_) := [.fst]](#manual-TriplePos___fst-_LPAR_in-Iterating-Over-Triples_RPAR_) : [TripleIterator]](#manual-TripleIterator-_LPAR_in-Iterating-Over-Triples_RPAR_) α} |>.[toIter](https://lean-lang.org/doc/reference/latest/Iterators/Iterator-Combinators/#Std___IterM___toIter)
```

There are two plausible steps: either the iterator's position has a successor, in which case the next iterator is one that points at the same triple with the successor position, or it does not, in which case iteration is complete.

```lean
@[grind]
inductive TripleIterator.IsPlausibleStep :
@[IterM]](#manual-Std___IterM___mk) ([TripleIterator]](#manual-TripleIterator-_LPAR_in-Iterating-Over-Triples_RPAR_) α) m α →
[IterStep]](#manual-Std___IterStep___yield) (@[IterM]](#manual-Std___IterM___mk) ([TripleIterator]](#manual-TripleIterator-_LPAR_in-Iterating-Over-Triples_RPAR_) α) m α) α →
Prop where
| yield :
it.[internalState]](#manual-Std___IterM___mk).[triple]](#manual-TripleIterator___triple-_LPAR_in-Iterating-Over-Triples_RPAR_) = it'.[internalState]](#manual-Std___IterM___mk).[triple]](#manual-TripleIterator___triple-_LPAR_in-Iterating-Over-Triples_RPAR_) →
it.[internalState]](#manual-Std___IterM___mk).[pos]](#manual-TripleIterator___pos-_LPAR_in-Iterating-Over-Triples_RPAR_).[Succ]](#manual-TriplePos___Succ-_LPAR_in-Iterating-Over-Triples_RPAR_) it'.[internalState]](#manual-Std___IterM___mk).[pos]](#manual-TripleIterator___pos-_LPAR_in-Iterating-Over-Triples_RPAR_) →
it.[internalState]](#manual-Std___IterM___mk).[triple]](#manual-TripleIterator___triple-_LPAR_in-Iterating-Over-Triples_RPAR_).[get?]](#manual-Triple___get___-_LPAR_in-Iterating-Over-Triples_RPAR_) it.[internalState]](#manual-Std___IterM___mk).[pos]](#manual-TripleIterator___pos-_LPAR_in-Iterating-Over-Triples_RPAR_) = [some]](#manual-Option___none) out →
[IsPlausibleStep]](#manual-TripleIterator___IsPlausibleStep-_LPAR_in-Iterating-Over-Triples_RPAR_) it ([.yield]](#manual-Std___IterStep___yield) it' out)
| done :
it.[internalState]](#manual-Std___IterM___mk).[pos]](#manual-TripleIterator___pos-_LPAR_in-Iterating-Over-Triples_RPAR_) = [.done]](#manual-TriplePos___done-_LPAR_in-Iterating-Over-Triples_RPAR_) →
[IsPlausibleStep]](#manual-TripleIterator___IsPlausibleStep-_LPAR_in-Iterating-Over-Triples_RPAR_) it [.done]](#manual-Std___IterStep___yield)
```

The corresponding step function yields the iterator and value describe by the relation:

```lean
instance [[Pure]](#manual-Pure___mk) m] : [Iterator]](#manual-Std___Iterator___mk) ([TripleIterator]](#manual-TripleIterator-_LPAR_in-Iterating-Over-Triples_RPAR_) α) m α where
[IsPlausibleStep]](#manual-Std___Iterator___mk) := [TripleIterator.IsPlausibleStep]](#manual-TripleIterator___IsPlausibleStep-_LPAR_in-Iterating-Over-Triples_RPAR_)
[step]](#manual-Std___Iterator___mk)
| ⟨xs, pos⟩ =>
[pure]](#manual-Pure___mk) <| [.deflate]](#manual-Std___Shrink___deflate) <|
[match]](#manual-Lean___Parser___Term___match) pos [with]](#manual-Lean___Parser___Term___match)
| [.fst]](#manual-TriplePos___fst-_LPAR_in-Iterating-Over-Triples_RPAR_) => [.yield]](#manual-Std___PlausibleIterStep___yield) ⟨xs, [.snd]](#manual-TriplePos___snd-_LPAR_in-Iterating-Over-Triples_RPAR_)⟩ xs.[fst]](#manual-Triple___fst-_LPAR_in-Iterating-Over-Triples_RPAR_) ?_
| [.snd]](#manual-TriplePos___snd-_LPAR_in-Iterating-Over-Triples_RPAR_) => [.yield]](#manual-Std___PlausibleIterStep___yield) ⟨xs, [.thd]](#manual-TriplePos___thd-_LPAR_in-Iterating-Over-Triples_RPAR_)⟩ xs.[snd]](#manual-Triple___snd-_LPAR_in-Iterating-Over-Triples_RPAR_) ?_
| [.thd]](#manual-TriplePos___thd-_LPAR_in-Iterating-Over-Triples_RPAR_) => [.yield]](#manual-Std___PlausibleIterStep___yield) ⟨xs, [.done]](#manual-TriplePos___done-_LPAR_in-Iterating-Over-Triples_RPAR_)⟩ xs.[thd]](#manual-Triple___thd-_LPAR_in-Iterating-Over-Triples_RPAR_) ?_
| [.done]](#manual-TriplePos___done-_LPAR_in-Iterating-Over-Triples_RPAR_) => [.done]](#manual-Std___PlausibleIterStep___done) <| ?_
where finally
[all_goals]](#manual-all_goals) [grind]](#manual-grind) [[Triple.get?]](#manual-Triple___get___-_LPAR_in-Iterating-Over-Triples_RPAR_)]All goals completed! 🐙
```

This iterator can now be converted to an array:

```lean
def abc : [Triple]](#manual-Triple-_LPAR_in-Iterating-Over-Triples_RPAR_) [Char]](#manual-Char___mk) := ⟨'a', 'b', 'c'⟩
```

```lean
[#eval]](#manual-Lean___Parser___Command___eval) [abc]](#manual-abc-_LPAR_in-Iterating-Over-Triples_RPAR_).[iter]](#manual-Triple___iter-_LPAR_in-Iterating-Over-Triples_RPAR_).[toArray](https://lean-lang.org/doc/reference/latest/Iterators/Consuming-Iterators/#Std___Iter___toArray)
```

```lean
[#[]](#manual-List___toArray)'a'[,]](#manual-List___nil) 'b'[,]](#manual-List___nil) 'c'[]](https://lean-lang.org/doc/reference/latest/Basic-Types/Linked-Lists/#List___toArray)
```

In general, `Iter.toArray` might run forever. One can prove that `abc` is finite, and the above example will terminate after finitely many steps, by
constructing a `Finite (Triple Char) Id` instance.
It's easiest to start at `[TriplePos.done]](#manual-TriplePos___done-_LPAR_in-Iterating-Over-Triples_RPAR_)` and work backwards toward `[TriplePos.fst]](#manual-TriplePos___fst-_LPAR_in-Iterating-Over-Triples_RPAR_)`, showing that each position in turn has a finite chain of successors:

```lean
@[[grind!]](#manual-Lean___Parser___Attr___grind___) .]
theorem acc_done [[Pure]](#manual-Pure___mk) m] :
[Acc]](#manual-Acc___intro) (IterM.IsPlausibleSuccessorOf (m := m))
⟨{ [triple]](#manual-TripleIterator___triple-_LPAR_in-Iterating-Over-Triples_RPAR_), [pos]](#manual-TripleIterator___pos-_LPAR_in-Iterating-Over-Triples_RPAR_) := [.done]](#manual-TriplePos___done-_LPAR_in-Iterating-Over-Triples_RPAR_) : [TripleIterator]](#manual-TripleIterator-_LPAR_in-Iterating-Over-Triples_RPAR_) α}⟩ :=
[Acc.intro]](#manual-Acc___intro) _ fun
| _, ⟨_, ⟨_, h⟩⟩ =>m:Type u_1 → Type u_2α:Type u_1triple:[Triple]](#manual-Triple-_LPAR_in-Iterating-Over-Triples_RPAR_) αinst✝:[Pure]](#manual-Pure___mk) mx✝:[IterM]](#manual-Std___IterM___mk) m αw✝:[IterStep]](#manual-Std___IterStep___yield) ([IterM]](#manual-Std___IterM___mk) m α) αleft✝:w✝.successor [=]](#manual-Eq___refl) [some]](#manual-Option___none) x✝h:{ [internalState]](#manual-Std___IterM___mk) := { [triple]](#manual-TripleIterator___triple-_LPAR_in-Iterating-Over-Triples_RPAR_) := triple, [pos]](#manual-TripleIterator___pos-_LPAR_in-Iterating-Over-Triples_RPAR_) := [TriplePos.done]](#manual-TriplePos___done-_LPAR_in-Iterating-Over-Triples_RPAR_) } }.IsPlausibleStep w✝⊢ [Acc]](#manual-Acc___intro) IterM.IsPlausibleSuccessorOf x✝ bym:Type u_1 → Type u_2α:Type u_1triple:[Triple]](#manual-Triple-_LPAR_in-Iterating-Over-Triples_RPAR_) αinst✝:[Pure]](#manual-Pure___mk) mx✝:[IterM]](#manual-Std___IterM___mk) m αw✝:[IterStep]](#manual-Std___IterStep___yield) ([IterM]](#manual-Std___IterM___mk) m α) αleft✝:w✝.successor [=]](#manual-Eq___refl) [some]](#manual-Option___none) x✝h:{ [internalState]](#manual-Std___IterM___mk) := { [triple]](#manual-TripleIterator___triple-_LPAR_in-Iterating-Over-Triples_RPAR_) := triple, [pos]](#manual-TripleIterator___pos-_LPAR_in-Iterating-Over-Triples_RPAR_) := [TriplePos.done]](#manual-TriplePos___done-_LPAR_in-Iterating-Over-Triples_RPAR_) } }.IsPlausibleStep w✝⊢ [Acc]](#manual-Acc___intro) IterM.IsPlausibleSuccessorOf x✝
[cases]](#manual-cases) hyieldm:Type u_1 → Type u_2α:Type u_1triple:[Triple]](#manual-Triple-_LPAR_in-Iterating-Over-Triples_RPAR_) αinst✝:[Pure]](#manual-Pure___mk) mx✝:[IterM]](#manual-Std___IterM___mk) m αout✝:αit'✝:[IterM]](#manual-Std___IterM___mk) m αa✝²:{ [internalState]](#manual-Std___IterM___mk) := { [triple]](#manual-TripleIterator___triple-_LPAR_in-Iterating-Over-Triples_RPAR_) := triple, [pos]](#manual-TripleIterator___pos-_LPAR_in-Iterating-Over-Triples_RPAR_) := [TriplePos.done]](#manual-TriplePos___done-_LPAR_in-Iterating-Over-Triples_RPAR_) } }.[internalState]](#manual-Std___IterM___mk).[triple]](#manual-TripleIterator___triple-_LPAR_in-Iterating-Over-Triples_RPAR_) [=]](#manual-Eq___refl) it'✝.[internalState]](#manual-Std___IterM___mk).[triple]](#manual-TripleIterator___triple-_LPAR_in-Iterating-Over-Triples_RPAR_)a✝¹:{ [internalState]](#manual-Std___IterM___mk) := { [triple]](#manual-TripleIterator___triple-_LPAR_in-Iterating-Over-Triples_RPAR_) := triple, [pos]](#manual-TripleIterator___pos-_LPAR_in-Iterating-Over-Triples_RPAR_) := [TriplePos.done]](#manual-TriplePos___done-_LPAR_in-Iterating-Over-Triples_RPAR_) } }.[internalState]](#manual-Std___IterM___mk).[pos]](#manual-TripleIterator___pos-_LPAR_in-Iterating-Over-Triples_RPAR_).[Succ]](#manual-TriplePos___Succ-_LPAR_in-Iterating-Over-Triples_RPAR_) it'✝.[internalState]](#manual-Std___IterM___mk).[pos]](#manual-TripleIterator___pos-_LPAR_in-Iterating-Over-Triples_RPAR_)a✝:{ [internalState]](#manual-Std___IterM___mk) := { [triple]](#manual-TripleIterator___triple-_LPAR_in-Iterating-Over-Triples_RPAR_) := triple, [pos]](#manual-TripleIterator___pos-_LPAR_in-Iterating-Over-Triples_RPAR_) := [TriplePos.done]](#manual-TriplePos___done-_LPAR_in-Iterating-Over-Triples_RPAR_) } }.[internalState]](#manual-Std___IterM___mk).[triple]](#manual-TripleIterator___triple-_LPAR_in-Iterating-Over-Triples_RPAR_).[get?]](#manual-Triple___get___-_LPAR_in-Iterating-Over-Triples_RPAR_)
{ [internalState]](#manual-Std___IterM___mk) := { [triple]](#manual-TripleIterator___triple-_LPAR_in-Iterating-Over-Triples_RPAR_) := triple, [pos]](#manual-TripleIterator___pos-_LPAR_in-Iterating-Over-Triples_RPAR_) := [TriplePos.done]](#manual-TriplePos___done-_LPAR_in-Iterating-Over-Triples_RPAR_) } }.[internalState]](#manual-Std___IterM___mk).[pos]](#manual-TripleIterator___pos-_LPAR_in-Iterating-Over-Triples_RPAR_) [=]](#manual-Eq___refl)
[some]](#manual-Option___none) out✝left✝:([IterStep.yield]](#manual-Std___IterStep___yield) it'✝ out✝).successor [=]](#manual-Eq___refl) [some]](#manual-Option___none) x✝⊢ [Acc]](#manual-Acc___intro) IterM.IsPlausibleSuccessorOf x✝donem:Type u_1 → Type u_2α:Type u_1triple:[Triple]](#manual-Triple-_LPAR_in-Iterating-Over-Triples_RPAR_) αinst✝:[Pure]](#manual-Pure___mk) mx✝:[IterM]](#manual-Std___IterM___mk) m αa✝:{ [internalState]](#manual-Std___IterM___mk) := { [triple]](#manual-TripleIterator___triple-_LPAR_in-Iterating-Over-Triples_RPAR_) := triple, [pos]](#manual-TripleIterator___pos-_LPAR_in-Iterating-Over-Triples_RPAR_) := [TriplePos.done]](#manual-TriplePos___done-_LPAR_in-Iterating-Over-Triples_RPAR_) } }.[internalState]](#manual-Std___IterM___mk).[pos]](#manual-TripleIterator___pos-_LPAR_in-Iterating-Over-Triples_RPAR_) [=]](#manual-Eq___refl) [TriplePos.done]](#manual-TriplePos___done-_LPAR_in-Iterating-Over-Triples_RPAR_)left✝:[IterStep.done]](#manual-Std___IterStep___yield).successor [=]](#manual-Eq___refl) [some]](#manual-Option___none) x✝⊢ [Acc]](#manual-Acc___intro) IterM.IsPlausibleSuccessorOf x✝ <;>yieldm:Type u_1 → Type u_2α:Type u_1triple:[Triple]](#manual-Triple-_LPAR_in-Iterating-Over-Triples_RPAR_) αinst✝:[Pure]](#manual-Pure___mk) mx✝:[IterM]](#manual-Std___IterM___mk) m αout✝:αit'✝:[IterM]](#manual-Std___IterM___mk) m αa✝²:{ [internalState]](#manual-Std___IterM___mk) := { [triple]](#manual-TripleIterator___triple-_LPAR_in-Iterating-Over-Triples_RPAR_) := triple, [pos]](#manual-TripleIterator___pos-_LPAR_in-Iterating-Over-Triples_RPAR_) := [TriplePos.done]](#manual-TriplePos___done-_LPAR_in-Iterating-Over-Triples_RPAR_) } }.[internalState]](#manual-Std___IterM___mk).[triple]](#manual-TripleIterator___triple-_LPAR_in-Iterating-Over-Triples_RPAR_) [=]](#manual-Eq___refl) it'✝.[internalState]](#manual-Std___IterM___mk).[triple]](#manual-TripleIterator___triple-_LPAR_in-Iterating-Over-Triples_RPAR_)a✝¹:{ [internalState]](#manual-Std___IterM___mk) := { [triple]](#manual-TripleIterator___triple-_LPAR_in-Iterating-Over-Triples_RPAR_) := triple, [pos]](#manual-TripleIterator___pos-_LPAR_in-Iterating-Over-Triples_RPAR_) := [TriplePos.done]](#manual-TriplePos___done-_LPAR_in-Iterating-Over-Triples_RPAR_) } }.[internalState]](#manual-Std___IterM___mk).[pos]](#manual-TripleIterator___pos-_LPAR_in-Iterating-Over-Triples_RPAR_).[Succ]](#manual-TriplePos___Succ-_LPAR_in-Iterating-Over-Triples_RPAR_) it'✝.[internalState]](#manual-Std___IterM___mk).[pos]](#manual-TripleIterator___pos-_LPAR_in-Iterating-Over-Triples_RPAR_)a✝:{ [internalState]](#manual-Std___IterM___mk) := { [triple]](#manual-TripleIterator___triple-_LPAR_in-Iterating-Over-Triples_RPAR_) := triple, [pos]](#manual-TripleIterator___pos-_LPAR_in-Iterating-Over-Triples_RPAR_) := [TriplePos.done]](#manual-TriplePos___done-_LPAR_in-Iterating-Over-Triples_RPAR_) } }.[internalState]](#manual-Std___IterM___mk).[triple]](#manual-TripleIterator___triple-_LPAR_in-Iterating-Over-Triples_RPAR_).[get?]](#manual-Triple___get___-_LPAR_in-Iterating-Over-Triples_RPAR_)
{ [internalState]](#manual-Std___IterM___mk) := { [triple]](#manual-TripleIterator___triple-_LPAR_in-Iterating-Over-Triples_RPAR_) := triple, [pos]](#manual-TripleIterator___pos-_LPAR_in-Iterating-Over-Triples_RPAR_) := [TriplePos.done]](#manual-TriplePos___done-_LPAR_in-Iterating-Over-Triples_RPAR_) } }.[internalState]](#manual-Std___IterM___mk).[pos]](#manual-TripleIterator___pos-_LPAR_in-Iterating-Over-Triples_RPAR_) [=]](#manual-Eq___refl)
[some]](#manual-Option___none) out✝left✝:([IterStep.yield]](#manual-Std___IterStep___yield) it'✝ out✝).successor [=]](#manual-Eq___refl) [some]](#manual-Option___none) x✝⊢ [Acc]](#manual-Acc___intro) IterM.IsPlausibleSuccessorOf x✝donem:Type u_1 → Type u_2α:Type u_1triple:[Triple]](#manual-Triple-_LPAR_in-Iterating-Over-Triples_RPAR_) αinst✝:[Pure]](#manual-Pure___mk) mx✝:[IterM]](#manual-Std___IterM___mk) m αa✝:{ [internalState]](#manual-Std___IterM___mk) := { [triple]](#manual-TripleIterator___triple-_LPAR_in-Iterating-Over-Triples_RPAR_) := triple, [pos]](#manual-TripleIterator___pos-_LPAR_in-Iterating-Over-Triples_RPAR_) := [TriplePos.done]](#manual-TriplePos___done-_LPAR_in-Iterating-Over-Triples_RPAR_) } }.[internalState]](#manual-Std___IterM___mk).[pos]](#manual-TripleIterator___pos-_LPAR_in-Iterating-Over-Triples_RPAR_) [=]](#manual-Eq___refl) [TriplePos.done]](#manual-TriplePos___done-_LPAR_in-Iterating-Over-Triples_RPAR_)left✝:[IterStep.done]](#manual-Std___IterStep___yield).successor [=]](#manual-Eq___refl) [some]](#manual-Option___none) x✝⊢ [Acc]](#manual-Acc___intro) IterM.IsPlausibleSuccessorOf x✝ [grind]](#manual-grind) [IterStep.successor_done]All goals completed! 🐙
@[[grind!]](#manual-Lean___Parser___Attr___grind___) .]
theorem acc_thd [[Pure]](#manual-Pure___mk) m] :
[Acc]](#manual-Acc___intro) (IterM.IsPlausibleSuccessorOf (m := m))
⟨{ [triple]](#manual-TripleIterator___triple-_LPAR_in-Iterating-Over-Triples_RPAR_), [pos]](#manual-TripleIterator___pos-_LPAR_in-Iterating-Over-Triples_RPAR_) := [.thd]](#manual-TriplePos___thd-_LPAR_in-Iterating-Over-Triples_RPAR_) : [TripleIterator]](#manual-TripleIterator-_LPAR_in-Iterating-Over-Triples_RPAR_) α}⟩ :=
[Acc.intro]](#manual-Acc___intro) _ fun
| ⟨{ [triple]](#manual-TripleIterator___triple-_LPAR_in-Iterating-Over-Triples_RPAR_), [pos]](#manual-TripleIterator___pos-_LPAR_in-Iterating-Over-Triples_RPAR_) }⟩, ⟨h, h', h''⟩ =>m:Type u_1 → Type u_2α:Type u_1triple✝:[Triple]](#manual-Triple-_LPAR_in-Iterating-Over-Triples_RPAR_) αinst✝:[Pure]](#manual-Pure___mk) m[triple]](#manual-TripleIterator___triple-_LPAR_in-Iterating-Over-Triples_RPAR_):[Triple]](#manual-Triple-_LPAR_in-Iterating-Over-Triples_RPAR_) α[pos]](#manual-TripleIterator___pos-_LPAR_in-Iterating-Over-Triples_RPAR_):[TriplePos]](#manual-TriplePos-_LPAR_in-Iterating-Over-Triples_RPAR_)h:[IterStep]](#manual-Std___IterStep___yield) ([IterM]](#manual-Std___IterM___mk) m α) αh':h.successor [=]](#manual-Eq___refl) [some]](#manual-Option___none) { [internalState]](#manual-Std___IterM___mk) := { [triple]](#manual-TripleIterator___triple-_LPAR_in-Iterating-Over-Triples_RPAR_) := [triple]](#manual-TripleIterator___triple-_LPAR_in-Iterating-Over-Triples_RPAR_), [pos]](#manual-TripleIterator___pos-_LPAR_in-Iterating-Over-Triples_RPAR_) := [pos]](#manual-TripleIterator___pos-_LPAR_in-Iterating-Over-Triples_RPAR_) } }h'':{ [internalState]](#manual-Std___IterM___mk) := { [triple]](#manual-TripleIterator___triple-_LPAR_in-Iterating-Over-Triples_RPAR_) := triple✝, [pos]](#manual-TripleIterator___pos-_LPAR_in-Iterating-Over-Triples_RPAR_) := [TriplePos.thd]](#manual-TriplePos___thd-_LPAR_in-Iterating-Over-Triples_RPAR_) } }.IsPlausibleStep h⊢ [Acc]](#manual-Acc___intro) IterM.IsPlausibleSuccessorOf { [internalState]](#manual-Std___IterM___mk) := { [triple]](#manual-TripleIterator___triple-_LPAR_in-Iterating-Over-Triples_RPAR_) := [triple]](#manual-TripleIterator___triple-_LPAR_in-Iterating-Over-Triples_RPAR_), [pos]](#manual-TripleIterator___pos-_LPAR_in-Iterating-Over-Triples_RPAR_) := [pos]](#manual-TripleIterator___pos-_LPAR_in-Iterating-Over-Triples_RPAR_) } } bym:Type u_1 → Type u_2α:Type u_1triple✝:[Triple]](#manual-Triple-_LPAR_in-Iterating-Over-Triples_RPAR_) αinst✝:[Pure]](#manual-Pure___mk) m[triple]](#manual-TripleIterator___triple-_LPAR_in-Iterating-Over-Triples_RPAR_):[Triple]](#manual-Triple-_LPAR_in-Iterating-Over-Triples_RPAR_) α[pos]](#manual-TripleIterator___pos-_LPAR_in-Iterating-Over-Triples_RPAR_):[TriplePos]](#manual-TriplePos-_LPAR_in-Iterating-Over-Triples_RPAR_)h:[IterStep]](#manual-Std___IterStep___yield) ([IterM]](#manual-Std___IterM___mk) m α) αh':h.successor [=]](#manual-Eq___refl) [some]](#manual-Option___none) { [internalState]](#manual-Std___IterM___mk) := { [triple]](#manual-TripleIterator___triple-_LPAR_in-Iterating-Over-Triples_RPAR_) := [triple]](#manual-TripleIterator___triple-_LPAR_in-Iterating-Over-Triples_RPAR_), [pos]](#manual-TripleIterator___pos-_LPAR_in-Iterating-Over-Triples_RPAR_) := [pos]](#manual-TripleIterator___pos-_LPAR_in-Iterating-Over-Triples_RPAR_) } }h'':{ [internalState]](#manual-Std___IterM___mk) := { [triple]](#manual-TripleIterator___triple-_LPAR_in-Iterating-Over-Triples_RPAR_) := triple✝, [pos]](#manual-TripleIterator___pos-_LPAR_in-Iterating-Over-Triples_RPAR_) := [TriplePos.thd]](#manual-TriplePos___thd-_LPAR_in-Iterating-Over-Triples_RPAR_) } }.IsPlausibleStep h⊢ [Acc]](#manual-Acc___intro) IterM.IsPlausibleSuccessorOf { [internalState]](#manual-Std___IterM___mk) := { [triple]](#manual-TripleIterator___triple-_LPAR_in-Iterating-Over-Triples_RPAR_) := [triple]](#manual-TripleIterator___triple-_LPAR_in-Iterating-Over-Triples_RPAR_), [pos]](#manual-TripleIterator___pos-_LPAR_in-Iterating-Over-Triples_RPAR_) := [pos]](#manual-TripleIterator___pos-_LPAR_in-Iterating-Over-Triples_RPAR_) } }
[cases]](#manual-cases) h''yieldm:Type u_1 → Type u_2α:Type u_1triple✝:[Triple]](#manual-Triple-_LPAR_in-Iterating-Over-Triples_RPAR_) αinst✝:[Pure]](#manual-Pure___mk) m[triple]](#manual-TripleIterator___triple-_LPAR_in-Iterating-Over-Triples_RPAR_):[Triple]](#manual-Triple-_LPAR_in-Iterating-Over-Triples_RPAR_) α[pos]](#manual-TripleIterator___pos-_LPAR_in-Iterating-Over-Triples_RPAR_):[TriplePos]](#manual-TriplePos-_LPAR_in-Iterating-Over-Triples_RPAR_)out✝:αit'✝:[IterM]](#manual-Std___IterM___mk) m αa✝²:{ [internalState]](#manual-Std___IterM___mk) := { [triple]](#manual-TripleIterator___triple-_LPAR_in-Iterating-Over-Triples_RPAR_) := triple✝, [pos]](#manual-TripleIterator___pos-_LPAR_in-Iterating-Over-Triples_RPAR_) := [TriplePos.thd]](#manual-TriplePos___thd-_LPAR_in-Iterating-Over-Triples_RPAR_) } }.[internalState]](#manual-Std___IterM___mk).[triple]](#manual-TripleIterator___triple-_LPAR_in-Iterating-Over-Triples_RPAR_) [=]](#manual-Eq___refl) it'✝.[internalState]](#manual-Std___IterM___mk).[triple]](#manual-TripleIterator___triple-_LPAR_in-Iterating-Over-Triples_RPAR_)a✝¹:{ [internalState]](#manual-Std___IterM___mk) := { [triple]](#manual-TripleIterator___triple-_LPAR_in-Iterating-Over-Triples_RPAR_) := triple✝, [pos]](#manual-TripleIterator___pos-_LPAR_in-Iterating-Over-Triples_RPAR_) := [TriplePos.thd]](#manual-TriplePos___thd-_LPAR_in-Iterating-Over-Triples_RPAR_) } }.[internalState]](#manual-Std___IterM___mk).[pos]](#manual-TripleIterator___pos-_LPAR_in-Iterating-Over-Triples_RPAR_).[Succ]](#manual-TriplePos___Succ-_LPAR_in-Iterating-Over-Triples_RPAR_) it'✝.[internalState]](#manual-Std___IterM___mk).[pos]](#manual-TripleIterator___pos-_LPAR_in-Iterating-Over-Triples_RPAR_)a✝:{ [internalState]](#manual-Std___IterM___mk) := { [triple]](#manual-TripleIterator___triple-_LPAR_in-Iterating-Over-Triples_RPAR_) := triple✝, [pos]](#manual-TripleIterator___pos-_LPAR_in-Iterating-Over-Triples_RPAR_) := [TriplePos.thd]](#manual-TriplePos___thd-_LPAR_in-Iterating-Over-Triples_RPAR_) } }.[internalState]](#manual-Std___IterM___mk).[triple]](#manual-TripleIterator___triple-_LPAR_in-Iterating-Over-Triples_RPAR_).[get?]](#manual-Triple___get___-_LPAR_in-Iterating-Over-Triples_RPAR_)
{ [internalState]](#manual-Std___IterM___mk) := { [triple]](#manual-TripleIterator___triple-_LPAR_in-Iterating-Over-Triples_RPAR_) := triple✝, [pos]](#manual-TripleIterator___pos-_LPAR_in-Iterating-Over-Triples_RPAR_) := [TriplePos.thd]](#manual-TriplePos___thd-_LPAR_in-Iterating-Over-Triples_RPAR_) } }.[internalState]](#manual-Std___IterM___mk).[pos]](#manual-TripleIterator___pos-_LPAR_in-Iterating-Over-Triples_RPAR_) [=]](#manual-Eq___refl)
[some]](#manual-Option___none) out✝h':([IterStep.yield]](#manual-Std___IterStep___yield) it'✝ out✝).successor [=]](#manual-Eq___refl) [some]](#manual-Option___none) { [internalState]](#manual-Std___IterM___mk) := { [triple]](#manual-TripleIterator___triple-_LPAR_in-Iterating-Over-Triples_RPAR_) := [triple]](#manual-TripleIterator___triple-_LPAR_in-Iterating-Over-Triples_RPAR_), [pos]](#manual-TripleIterator___pos-_LPAR_in-Iterating-Over-Triples_RPAR_) := [pos]](#manual-TripleIterator___pos-_LPAR_in-Iterating-Over-Triples_RPAR_) } }⊢ [Acc]](#manual-Acc___intro) IterM.IsPlausibleSuccessorOf { [internalState]](#manual-Std___IterM___mk) := { [triple]](#manual-TripleIterator___triple-_LPAR_in-Iterating-Over-Triples_RPAR_) := [triple]](#manual-TripleIterator___triple-_LPAR_in-Iterating-Over-Triples_RPAR_), [pos]](#manual-TripleIterator___pos-_LPAR_in-Iterating-Over-Triples_RPAR_) := [pos]](#manual-TripleIterator___pos-_LPAR_in-Iterating-Over-Triples_RPAR_) } }donem:Type u_1 → Type u_2α:Type u_1triple✝:[Triple]](#manual-Triple-_LPAR_in-Iterating-Over-Triples_RPAR_) αinst✝:[Pure]](#manual-Pure___mk) m[triple]](#manual-TripleIterator___triple-_LPAR_in-Iterating-Over-Triples_RPAR_):[Triple]](#manual-Triple-_LPAR_in-Iterating-Over-Triples_RPAR_) α[pos]](#manual-TripleIterator___pos-_LPAR_in-Iterating-Over-Triples_RPAR_):[TriplePos]](#manual-TriplePos-_LPAR_in-Iterating-Over-Triples_RPAR_)a✝:{ [internalState]](#manual-Std___IterM___mk) := { [triple]](#manual-TripleIterator___triple-_LPAR_in-Iterating-Over-Triples_RPAR_) := triple✝, [pos]](#manual-TripleIterator___pos-_LPAR_in-Iterating-Over-Triples_RPAR_) := [TriplePos.thd]](#manual-TriplePos___thd-_LPAR_in-Iterating-Over-Triples_RPAR_) } }.[internalState]](#manual-Std___IterM___mk).[pos]](#manual-TripleIterator___pos-_LPAR_in-Iterating-Over-Triples_RPAR_) [=]](#manual-Eq___refl) [TriplePos.done]](#manual-TriplePos___done-_LPAR_in-Iterating-Over-Triples_RPAR_)h':[IterStep.done]](#manual-Std___IterStep___yield).successor [=]](#manual-Eq___refl) [some]](#manual-Option___none) { [internalState]](#manual-Std___IterM___mk) := { [triple]](#manual-TripleIterator___triple-_LPAR_in-Iterating-Over-Triples_RPAR_) := [triple]](#manual-TripleIterator___triple-_LPAR_in-Iterating-Over-Triples_RPAR_), [pos]](#manual-TripleIterator___pos-_LPAR_in-Iterating-Over-Triples_RPAR_) := [pos]](#manual-TripleIterator___pos-_LPAR_in-Iterating-Over-Triples_RPAR_) } }⊢ [Acc]](#manual-Acc___intro) IterM.IsPlausibleSuccessorOf { [internalState]](#manual-Std___IterM___mk) := { [triple]](#manual-TripleIterator___triple-_LPAR_in-Iterating-Over-Triples_RPAR_) := [triple]](#manual-TripleIterator___triple-_LPAR_in-Iterating-Over-Triples_RPAR_), [pos]](#manual-TripleIterator___pos-_LPAR_in-Iterating-Over-Triples_RPAR_) := [pos]](#manual-TripleIterator___pos-_LPAR_in-Iterating-Over-Triples_RPAR_) } } <;>yieldm:Type u_1 → Type u_2α:Type u_1triple✝:[Triple]](#manual-Triple-_LPAR_in-Iterating-Over-Triples_RPAR_) αinst✝:[Pure]](#manual-Pure___mk) m[triple]](#manual-TripleIterator___triple-_LPAR_in-Iterating-Over-Triples_RPAR_):[Triple]](#manual-Triple-_LPAR_in-Iterating-Over-Triples_RPAR_) α[pos]](#manual-TripleIterator___pos-_LPAR_in-Iterating-Over-Triples_RPAR_):[TriplePos]](#manual-TriplePos-_LPAR_in-Iterating-Over-Triples_RPAR_)out✝:αit'✝:[IterM]](#manual-Std___IterM___mk) m αa✝²:{ [internalState]](#manual-Std___IterM___mk) := { [triple]](#manual-TripleIterator___triple-_LPAR_in-Iterating-Over-Triples_RPAR_) := triple✝, [pos]](#manual-TripleIterator___pos-_LPAR_in-Iterating-Over-Triples_RPAR_) := [TriplePos.thd]](#manual-TriplePos___thd-_LPAR_in-Iterating-Over-Triples_RPAR_) } }.[internalState]](#manual-Std___IterM___mk).[triple]](#manual-TripleIterator___triple-_LPAR_in-Iterating-Over-Triples_RPAR_) [=]](#manual-Eq___refl) it'✝.[internalState]](#manual-Std___IterM___mk).[triple]](#manual-TripleIterator___triple-_LPAR_in-Iterating-Over-Triples_RPAR_)a✝¹:{ [internalState]](#manual-Std___IterM___mk) := { [triple]](#manual-TripleIterator___triple-_LPAR_in-Iterating-Over-Triples_RPAR_) := triple✝, [pos]](#manual-TripleIterator___pos-_LPAR_in-Iterating-Over-Triples_RPAR_) := [TriplePos.thd]](#manual-TriplePos___thd-_LPAR_in-Iterating-Over-Triples_RPAR_) } }.[internalState]](#manual-Std___IterM___mk).[pos]](#manual-TripleIterator___pos-_LPAR_in-Iterating-Over-Triples_RPAR_).[Succ]](#manual-TriplePos___Succ-_LPAR_in-Iterating-Over-Triples_RPAR_) it'✝.[internalState]](#manual-Std___IterM___mk).[pos]](#manual-TripleIterator___pos-_LPAR_in-Iterating-Over-Triples_RPAR_)a✝:{ [internalState]](#manual-Std___IterM___mk) := { [triple]](#manual-TripleIterator___triple-_LPAR_in-Iterating-Over-Triples_RPAR_) := triple✝, [pos]](#manual-TripleIterator___pos-_LPAR_in-Iterating-Over-Triples_RPAR_) := [TriplePos.thd]](#manual-TriplePos___thd-_LPAR_in-Iterating-Over-Triples_RPAR_) } }.[internalState]](#manual-Std___IterM___mk).[triple]](#manual-TripleIterator___triple-_LPAR_in-Iterating-Over-Triples_RPAR_).[get?]](#manual-Triple___get___-_LPAR_in-Iterating-Over-Triples_RPAR_)
{ [internalState]](#manual-Std___IterM___mk) := { [triple]](#manual-TripleIterator___triple-_LPAR_in-Iterating-Over-Triples_RPAR_) := triple✝, [pos]](#manual-TripleIterator___pos-_LPAR_in-Iterating-Over-Triples_RPAR_) := [TriplePos.thd]](#manual-TriplePos___thd-_LPAR_in-Iterating-Over-Triples_RPAR_) } }.[internalState]](#manual-Std___IterM___mk).[pos]](#manual-TripleIterator___pos-_LPAR_in-Iterating-Over-Triples_RPAR_) [=]](#manual-Eq___refl)
[some]](#manual-Option___none) out✝h':([IterStep.yield]](#manual-Std___IterStep___yield) it'✝ out✝).successor [=]](#manual-Eq___refl) [some]](#manual-Option___none) { [internalState]](#manual-Std___IterM___mk) := { [triple]](#manual-TripleIterator___triple-_LPAR_in-Iterating-Over-Triples_RPAR_) := [triple]](#manual-TripleIterator___triple-_LPAR_in-Iterating-Over-Triples_RPAR_), [pos]](#manual-TripleIterator___pos-_LPAR_in-Iterating-Over-Triples_RPAR_) := [pos]](#manual-TripleIterator___pos-_LPAR_in-Iterating-Over-Triples_RPAR_) } }⊢ [Acc]](#manual-Acc___intro) IterM.IsPlausibleSuccessorOf { [internalState]](#manual-Std___IterM___mk) := { [triple]](#manual-TripleIterator___triple-_LPAR_in-Iterating-Over-Triples_RPAR_) := [triple]](#manual-TripleIterator___triple-_LPAR_in-Iterating-Over-Triples_RPAR_), [pos]](#manual-TripleIterator___pos-_LPAR_in-Iterating-Over-Triples_RPAR_) := [pos]](#manual-TripleIterator___pos-_LPAR_in-Iterating-Over-Triples_RPAR_) } }donem:Type u_1 → Type u_2α:Type u_1triple✝:[Triple]](#manual-Triple-_LPAR_in-Iterating-Over-Triples_RPAR_) αinst✝:[Pure]](#manual-Pure___mk) m[triple]](#manual-TripleIterator___triple-_LPAR_in-Iterating-Over-Triples_RPAR_):[Triple]](#manual-Triple-_LPAR_in-Iterating-Over-Triples_RPAR_) α[pos]](#manual-TripleIterator___pos-_LPAR_in-Iterating-Over-Triples_RPAR_):[TriplePos]](#manual-TriplePos-_LPAR_in-Iterating-Over-Triples_RPAR_)a✝:{ [internalState]](#manual-Std___IterM___mk) := { [triple]](#manual-TripleIterator___triple-_LPAR_in-Iterating-Over-Triples_RPAR_) := triple✝, [pos]](#manual-TripleIterator___pos-_LPAR_in-Iterating-Over-Triples_RPAR_) := [TriplePos.thd]](#manual-TriplePos___thd-_LPAR_in-Iterating-Over-Triples_RPAR_) } }.[internalState]](#manual-Std___IterM___mk).[pos]](#manual-TripleIterator___pos-_LPAR_in-Iterating-Over-Triples_RPAR_) [=]](#manual-Eq___refl) [TriplePos.done]](#manual-TriplePos___done-_LPAR_in-Iterating-Over-Triples_RPAR_)h':[IterStep.done]](#manual-Std___IterStep___yield).successor [=]](#manual-Eq___refl) [some]](#manual-Option___none) { [internalState]](#manual-Std___IterM___mk) := { [triple]](#manual-TripleIterator___triple-_LPAR_in-Iterating-Over-Triples_RPAR_) := [triple]](#manual-TripleIterator___triple-_LPAR_in-Iterating-Over-Triples_RPAR_), [pos]](#manual-TripleIterator___pos-_LPAR_in-Iterating-Over-Triples_RPAR_) := [pos]](#manual-TripleIterator___pos-_LPAR_in-Iterating-Over-Triples_RPAR_) } }⊢ [Acc]](#manual-Acc___intro) IterM.IsPlausibleSuccessorOf { [internalState]](#manual-Std___IterM___mk) := { [triple]](#manual-TripleIterator___triple-_LPAR_in-Iterating-Over-Triples_RPAR_) := [triple]](#manual-TripleIterator___triple-_LPAR_in-Iterating-Over-Triples_RPAR_), [pos]](#manual-TripleIterator___pos-_LPAR_in-Iterating-Over-Triples_RPAR_) := [pos]](#manual-TripleIterator___pos-_LPAR_in-Iterating-Over-Triples_RPAR_) } } [grind]](#manual-grind) [IterStep.successor_yield]All goals completed! 🐙
@[[grind!]](#manual-Lean___Parser___Attr___grind___) .]
theorem acc_snd [[Pure]](#manual-Pure___mk) m] :
[Acc]](#manual-Acc___intro) (IterM.IsPlausibleSuccessorOf (m := m))
⟨{ [triple]](#manual-TripleIterator___triple-_LPAR_in-Iterating-Over-Triples_RPAR_), [pos]](#manual-TripleIterator___pos-_LPAR_in-Iterating-Over-Triples_RPAR_) := [.snd]](#manual-TriplePos___snd-_LPAR_in-Iterating-Over-Triples_RPAR_) : [TripleIterator]](#manual-TripleIterator-_LPAR_in-Iterating-Over-Triples_RPAR_) α}⟩ :=
[Acc.intro]](#manual-Acc___intro) _ fun
| ⟨{ [triple]](#manual-TripleIterator___triple-_LPAR_in-Iterating-Over-Triples_RPAR_), [pos]](#manual-TripleIterator___pos-_LPAR_in-Iterating-Over-Triples_RPAR_) }⟩, ⟨h, h', h''⟩ =>m:Type u_1 → Type u_2α:Type u_1triple✝:[Triple]](#manual-Triple-_LPAR_in-Iterating-Over-Triples_RPAR_) αinst✝:[Pure]](#manual-Pure___mk) m[triple]](#manual-TripleIterator___triple-_LPAR_in-Iterating-Over-Triples_RPAR_):[Triple]](#manual-Triple-_LPAR_in-Iterating-Over-Triples_RPAR_) α[pos]](#manual-TripleIterator___pos-_LPAR_in-Iterating-Over-Triples_RPAR_):[TriplePos]](#manual-TriplePos-_LPAR_in-Iterating-Over-Triples_RPAR_)h:[IterStep]](#manual-Std___IterStep___yield) ([IterM]](#manual-Std___IterM___mk) m α) αh':h.successor [=]](#manual-Eq___refl) [some]](#manual-Option___none) { [internalState]](#manual-Std___IterM___mk) := { [triple]](#manual-TripleIterator___triple-_LPAR_in-Iterating-Over-Triples_RPAR_) := [triple]](#manual-TripleIterator___triple-_LPAR_in-Iterating-Over-Triples_RPAR_), [pos]](#manual-TripleIterator___pos-_LPAR_in-Iterating-Over-Triples_RPAR_) := [pos]](#manual-TripleIterator___pos-_LPAR_in-Iterating-Over-Triples_RPAR_) } }h'':{ [internalState]](#manual-Std___IterM___mk) := { [triple]](#manual-TripleIterator___triple-_LPAR_in-Iterating-Over-Triples_RPAR_) := triple✝, [pos]](#manual-TripleIterator___pos-_LPAR_in-Iterating-Over-Triples_RPAR_) := [TriplePos.snd]](#manual-TriplePos___snd-_LPAR_in-Iterating-Over-Triples_RPAR_) } }.IsPlausibleStep h⊢ [Acc]](#manual-Acc___intro) IterM.IsPlausibleSuccessorOf { [internalState]](#manual-Std___IterM___mk) := { [triple]](#manual-TripleIterator___triple-_LPAR_in-Iterating-Over-Triples_RPAR_) := [triple]](#manual-TripleIterator___triple-_LPAR_in-Iterating-Over-Triples_RPAR_), [pos]](#manual-TripleIterator___pos-_LPAR_in-Iterating-Over-Triples_RPAR_) := [pos]](#manual-TripleIterator___pos-_LPAR_in-Iterating-Over-Triples_RPAR_) } } bym:Type u_1 → Type u_2α:Type u_1triple✝:[Triple]](#manual-Triple-_LPAR_in-Iterating-Over-Triples_RPAR_) αinst✝:[Pure]](#manual-Pure___mk) m[triple]](#manual-TripleIterator___triple-_LPAR_in-Iterating-Over-Triples_RPAR_):[Triple]](#manual-Triple-_LPAR_in-Iterating-Over-Triples_RPAR_) α[pos]](#manual-TripleIterator___pos-_LPAR_in-Iterating-Over-Triples_RPAR_):[TriplePos]](#manual-TriplePos-_LPAR_in-Iterating-Over-Triples_RPAR_)h:[IterStep]](#manual-Std___IterStep___yield) ([IterM]](#manual-Std___IterM___mk) m α) αh':h.successor [=]](#manual-Eq___refl) [some]](#manual-Option___none) { [internalState]](#manual-Std___IterM___mk) := { [triple]](#manual-TripleIterator___triple-_LPAR_in-Iterating-Over-Triples_RPAR_) := [triple]](#manual-TripleIterator___triple-_LPAR_in-Iterating-Over-Triples_RPAR_), [pos]](#manual-TripleIterator___pos-_LPAR_in-Iterating-Over-Triples_RPAR_) := [pos]](#manual-TripleIterator___pos-_LPAR_in-Iterating-Over-Triples_RPAR_) } }h'':{ [internalState]](#manual-Std___IterM___mk) := { [triple]](#manual-TripleIterator___triple-_LPAR_in-Iterating-Over-Triples_RPAR_) := triple✝, [pos]](#manual-TripleIterator___pos-_LPAR_in-Iterating-Over-Triples_RPAR_) := [TriplePos.snd]](#manual-TriplePos___snd-_LPAR_in-Iterating-Over-Triples_RPAR_) } }.IsPlausibleStep h⊢ [Acc]](#manual-Acc___intro) IterM.IsPlausibleSuccessorOf { [internalState]](#manual-Std___IterM___mk) := { [triple]](#manual-TripleIterator___triple-_LPAR_in-Iterating-Over-Triples_RPAR_) := [triple]](#manual-TripleIterator___triple-_LPAR_in-Iterating-Over-Triples_RPAR_), [pos]](#manual-TripleIterator___pos-_LPAR_in-Iterating-Over-Triples_RPAR_) := [pos]](#manual-TripleIterator___pos-_LPAR_in-Iterating-Over-Triples_RPAR_) } }
[cases]](#manual-cases) h''yieldm:Type u_1 → Type u_2α:Type u_1triple✝:[Triple]](#manual-Triple-_LPAR_in-Iterating-Over-Triples_RPAR_) αinst✝:[Pure]](#manual-Pure___mk) m[triple]](#manual-TripleIterator___triple-_LPAR_in-Iterating-Over-Triples_RPAR_):[Triple]](#manual-Triple-_LPAR_in-Iterating-Over-Triples_RPAR_) α[pos]](#manual-TripleIterator___pos-_LPAR_in-Iterating-Over-Triples_RPAR_):[TriplePos]](#manual-TriplePos-_LPAR_in-Iterating-Over-Triples_RPAR_)out✝:αit'✝:[IterM]](#manual-Std___IterM___mk) m αa✝²:{ [internalState]](#manual-Std___IterM___mk) := { [triple]](#manual-TripleIterator___triple-_LPAR_in-Iterating-Over-Triples_RPAR_) := triple✝, [pos]](#manual-TripleIterator___pos-_LPAR_in-Iterating-Over-Triples_RPAR_) := [TriplePos.snd]](#manual-TriplePos___snd-_LPAR_in-Iterating-Over-Triples_RPAR_) } }.[internalState]](#manual-Std___IterM___mk).[triple]](#manual-TripleIterator___triple-_LPAR_in-Iterating-Over-Triples_RPAR_) [=]](#manual-Eq___refl) it'✝.[internalState]](#manual-Std___IterM___mk).[triple]](#manual-TripleIterator___triple-_LPAR_in-Iterating-Over-Triples_RPAR_)a✝¹:{ [internalState]](#manual-Std___IterM___mk) := { [triple]](#manual-TripleIterator___triple-_LPAR_in-Iterating-Over-Triples_RPAR_) := triple✝, [pos]](#manual-TripleIterator___pos-_LPAR_in-Iterating-Over-Triples_RPAR_) := [TriplePos.snd]](#manual-TriplePos___snd-_LPAR_in-Iterating-Over-Triples_RPAR_) } }.[internalState]](#manual-Std___IterM___mk).[pos]](#manual-TripleIterator___pos-_LPAR_in-Iterating-Over-Triples_RPAR_).[Succ]](#manual-TriplePos___Succ-_LPAR_in-Iterating-Over-Triples_RPAR_) it'✝.[internalState]](#manual-Std___IterM___mk).[pos]](#manual-TripleIterator___pos-_LPAR_in-Iterating-Over-Triples_RPAR_)a✝:{ [internalState]](#manual-Std___IterM___mk) := { [triple]](#manual-TripleIterator___triple-_LPAR_in-Iterating-Over-Triples_RPAR_) := triple✝, [pos]](#manual-TripleIterator___pos-_LPAR_in-Iterating-Over-Triples_RPAR_) := [TriplePos.snd]](#manual-TriplePos___snd-_LPAR_in-Iterating-Over-Triples_RPAR_) } }.[internalState]](#manual-Std___IterM___mk).[triple]](#manual-TripleIterator___triple-_LPAR_in-Iterating-Over-Triples_RPAR_).[get?]](#manual-Triple___get___-_LPAR_in-Iterating-Over-Triples_RPAR_)
{ [internalState]](#manual-Std___IterM___mk) := { [triple]](#manual-TripleIterator___triple-_LPAR_in-Iterating-Over-Triples_RPAR_) := triple✝, [pos]](#manual-TripleIterator___pos-_LPAR_in-Iterating-Over-Triples_RPAR_) := [TriplePos.snd]](#manual-TriplePos___snd-_LPAR_in-Iterating-Over-Triples_RPAR_) } }.[internalState]](#manual-Std___IterM___mk).[pos]](#manual-TripleIterator___pos-_LPAR_in-Iterating-Over-Triples_RPAR_) [=]](#manual-Eq___refl)
[some]](#manual-Option___none) out✝h':([IterStep.yield]](#manual-Std___IterStep___yield) it'✝ out✝).successor [=]](#manual-Eq___refl) [some]](#manual-Option___none) { [internalState]](#manual-Std___IterM___mk) := { [triple]](#manual-TripleIterator___triple-_LPAR_in-Iterating-Over-Triples_RPAR_) := [triple]](#manual-TripleIterator___triple-_LPAR_in-Iterating-Over-Triples_RPAR_), [pos]](#manual-TripleIterator___pos-_LPAR_in-Iterating-Over-Triples_RPAR_) := [pos]](#manual-TripleIterator___pos-_LPAR_in-Iterating-Over-Triples_RPAR_) } }⊢ [Acc]](#manual-Acc___intro) IterM.IsPlausibleSuccessorOf { [internalState]](#manual-Std___IterM___mk) := { [triple]](#manual-TripleIterator___triple-_LPAR_in-Iterating-Over-Triples_RPAR_) := [triple]](#manual-TripleIterator___triple-_LPAR_in-Iterating-Over-Triples_RPAR_), [pos]](#manual-TripleIterator___pos-_LPAR_in-Iterating-Over-Triples_RPAR_) := [pos]](#manual-TripleIterator___pos-_LPAR_in-Iterating-Over-Triples_RPAR_) } }donem:Type u_1 → Type u_2α:Type u_1triple✝:[Triple]](#manual-Triple-_LPAR_in-Iterating-Over-Triples_RPAR_) αinst✝:[Pure]](#manual-Pure___mk) m[triple]](#manual-TripleIterator___triple-_LPAR_in-Iterating-Over-Triples_RPAR_):[Triple]](#manual-Triple-_LPAR_in-Iterating-Over-Triples_RPAR_) α[pos]](#manual-TripleIterator___pos-_LPAR_in-Iterating-Over-Triples_RPAR_):[TriplePos]](#manual-TriplePos-_LPAR_in-Iterating-Over-Triples_RPAR_)a✝:{ [internalState]](#manual-Std___IterM___mk) := { [triple]](#manual-TripleIterator___triple-_LPAR_in-Iterating-Over-Triples_RPAR_) := triple✝, [pos]](#manual-TripleIterator___pos-_LPAR_in-Iterating-Over-Triples_RPAR_) := [TriplePos.snd]](#manual-TriplePos___snd-_LPAR_in-Iterating-Over-Triples_RPAR_) } }.[internalState]](#manual-Std___IterM___mk).[pos]](#manual-TripleIterator___pos-_LPAR_in-Iterating-Over-Triples_RPAR_) [=]](#manual-Eq___refl) [TriplePos.done]](#manual-TriplePos___done-_LPAR_in-Iterating-Over-Triples_RPAR_)h':[IterStep.done]](#manual-Std___IterStep___yield).successor [=]](#manual-Eq___refl) [some]](#manual-Option___none) { [internalState]](#manual-Std___IterM___mk) := { [triple]](#manual-TripleIterator___triple-_LPAR_in-Iterating-Over-Triples_RPAR_) := [triple]](#manual-TripleIterator___triple-_LPAR_in-Iterating-Over-Triples_RPAR_), [pos]](#manual-TripleIterator___pos-_LPAR_in-Iterating-Over-Triples_RPAR_) := [pos]](#manual-TripleIterator___pos-_LPAR_in-Iterating-Over-Triples_RPAR_) } }⊢ [Acc]](#manual-Acc___intro) IterM.IsPlausibleSuccessorOf { [internalState]](#manual-Std___IterM___mk) := { [triple]](#manual-TripleIterator___triple-_LPAR_in-Iterating-Over-Triples_RPAR_) := [triple]](#manual-TripleIterator___triple-_LPAR_in-Iterating-Over-Triples_RPAR_), [pos]](#manual-TripleIterator___pos-_LPAR_in-Iterating-Over-Triples_RPAR_) := [pos]](#manual-TripleIterator___pos-_LPAR_in-Iterating-Over-Triples_RPAR_) } } <;>yieldm:Type u_1 → Type u_2α:Type u_1triple✝:[Triple]](#manual-Triple-_LPAR_in-Iterating-Over-Triples_RPAR_) αinst✝:[Pure]](#manual-Pure___mk) m[triple]](#manual-TripleIterator___triple-_LPAR_in-Iterating-Over-Triples_RPAR_):[Triple]](#manual-Triple-_LPAR_in-Iterating-Over-Triples_RPAR_) α[pos]](#manual-TripleIterator___pos-_LPAR_in-Iterating-Over-Triples_RPAR_):[TriplePos]](#manual-TriplePos-_LPAR_in-Iterating-Over-Triples_RPAR_)out✝:αit'✝:[IterM]](#manual-Std___IterM___mk) m αa✝²:{ [internalState]](#manual-Std___IterM___mk) := { [triple]](#manual-TripleIterator___triple-_LPAR_in-Iterating-Over-Triples_RPAR_) := triple✝, [pos]](#manual-TripleIterator___pos-_LPAR_in-Iterating-Over-Triples_RPAR_) := [TriplePos.snd]](#manual-TriplePos___snd-_LPAR_in-Iterating-Over-Triples_RPAR_) } }.[internalState]](#manual-Std___IterM___mk).[triple]](#manual-TripleIterator___triple-_LPAR_in-Iterating-Over-Triples_RPAR_) [=]](#manual-Eq___refl) it'✝.[internalState]](#manual-Std___IterM___mk).[triple]](#manual-TripleIterator___triple-_LPAR_in-Iterating-Over-Triples_RPAR_)a✝¹:{ [internalState]](#manual-Std___IterM___mk) := { [triple]](#manual-TripleIterator___triple-_LPAR_in-Iterating-Over-Triples_RPAR_) := triple✝, [pos]](#manual-TripleIterator___pos-_LPAR_in-Iterating-Over-Triples_RPAR_) := [TriplePos.snd]](#manual-TriplePos___snd-_LPAR_in-Iterating-Over-Triples_RPAR_) } }.[internalState]](#manual-Std___IterM___mk).[pos]](#manual-TripleIterator___pos-_LPAR_in-Iterating-Over-Triples_RPAR_).[Succ]](#manual-TriplePos___Succ-_LPAR_in-Iterating-Over-Triples_RPAR_) it'✝.[internalState]](#manual-Std___IterM___mk).[pos]](#manual-TripleIterator___pos-_LPAR_in-Iterating-Over-Triples_RPAR_)a✝:{ [internalState]](#manual-Std___IterM___mk) := { [triple]](#manual-TripleIterator___triple-_LPAR_in-Iterating-Over-Triples_RPAR_) := triple✝, [pos]](#manual-TripleIterator___pos-_LPAR_in-Iterating-Over-Triples_RPAR_) := [TriplePos.snd]](#manual-TriplePos___snd-_LPAR_in-Iterating-Over-Triples_RPAR_) } }.[internalState]](#manual-Std___IterM___mk).[triple]](#manual-TripleIterator___triple-_LPAR_in-Iterating-Over-Triples_RPAR_).[get?]](#manual-Triple___get___-_LPAR_in-Iterating-Over-Triples_RPAR_)
{ [internalState]](#manual-Std___IterM___mk) := { [triple]](#manual-TripleIterator___triple-_LPAR_in-Iterating-Over-Triples_RPAR_) := triple✝, [pos]](#manual-TripleIterator___pos-_LPAR_in-Iterating-Over-Triples_RPAR_) := [TriplePos.snd]](#manual-TriplePos___snd-_LPAR_in-Iterating-Over-Triples_RPAR_) } }.[internalState]](#manual-Std___IterM___mk).[pos]](#manual-TripleIterator___pos-_LPAR_in-Iterating-Over-Triples_RPAR_) [=]](#manual-Eq___refl)
[some]](#manual-Option___none) out✝h':([IterStep.yield]](#manual-Std___IterStep___yield) it'✝ out✝).successor [=]](#manual-Eq___refl) [some]](#manual-Option___none) { [internalState]](#manual-Std___IterM___mk) := { [triple]](#manual-TripleIterator___triple-_LPAR_in-Iterating-Over-Triples_RPAR_) := [triple]](#manual-TripleIterator___triple-_LPAR_in-Iterating-Over-Triples_RPAR_), [pos]](#manual-TripleIterator___pos-_LPAR_in-Iterating-Over-Triples_RPAR_) := [pos]](#manual-TripleIterator___pos-_LPAR_in-Iterating-Over-Triples_RPAR_) } }⊢ [Acc]](#manual-Acc___intro) IterM.IsPlausibleSuccessorOf { [internalState]](#manual-Std___IterM___mk) := { [triple]](#manual-TripleIterator___triple-_LPAR_in-Iterating-Over-Triples_RPAR_) := [triple]](#manual-TripleIterator___triple-_LPAR_in-Iterating-Over-Triples_RPAR_), [pos]](#manual-TripleIterator___pos-_LPAR_in-Iterating-Over-Triples_RPAR_) := [pos]](#manual-TripleIterator___pos-_LPAR_in-Iterating-Over-Triples_RPAR_) } }donem:Type u_1 → Type u_2α:Type u_1triple✝:[Triple]](#manual-Triple-_LPAR_in-Iterating-Over-Triples_RPAR_) αinst✝:[Pure]](#manual-Pure___mk) m[triple]](#manual-TripleIterator___triple-_LPAR_in-Iterating-Over-Triples_RPAR_):[Triple]](#manual-Triple-_LPAR_in-Iterating-Over-Triples_RPAR_) α[pos]](#manual-TripleIterator___pos-_LPAR_in-Iterating-Over-Triples_RPAR_):[TriplePos]](#manual-TriplePos-_LPAR_in-Iterating-Over-Triples_RPAR_)a✝:{ [internalState]](#manual-Std___IterM___mk) := { [triple]](#manual-TripleIterator___triple-_LPAR_in-Iterating-Over-Triples_RPAR_) := triple✝, [pos]](#manual-TripleIterator___pos-_LPAR_in-Iterating-Over-Triples_RPAR_) := [TriplePos.snd]](#manual-TriplePos___snd-_LPAR_in-Iterating-Over-Triples_RPAR_) } }.[internalState]](#manual-Std___IterM___mk).[pos]](#manual-TripleIterator___pos-_LPAR_in-Iterating-Over-Triples_RPAR_) [=]](#manual-Eq___refl) [TriplePos.done]](#manual-TriplePos___done-_LPAR_in-Iterating-Over-Triples_RPAR_)h':[IterStep.done]](#manual-Std___IterStep___yield).successor [=]](#manual-Eq___refl) [some]](#manual-Option___none) { [internalState]](#manual-Std___IterM___mk) := { [triple]](#manual-TripleIterator___triple-_LPAR_in-Iterating-Over-Triples_RPAR_) := [triple]](#manual-TripleIterator___triple-_LPAR_in-Iterating-Over-Triples_RPAR_), [pos]](#manual-TripleIterator___pos-_LPAR_in-Iterating-Over-Triples_RPAR_) := [pos]](#manual-TripleIterator___pos-_LPAR_in-Iterating-Over-Triples_RPAR_) } }⊢ [Acc]](#manual-Acc___intro) IterM.IsPlausibleSuccessorOf { [internalState]](#manual-Std___IterM___mk) := { [triple]](#manual-TripleIterator___triple-_LPAR_in-Iterating-Over-Triples_RPAR_) := [triple]](#manual-TripleIterator___triple-_LPAR_in-Iterating-Over-Triples_RPAR_), [pos]](#manual-TripleIterator___pos-_LPAR_in-Iterating-Over-Triples_RPAR_) := [pos]](#manual-TripleIterator___pos-_LPAR_in-Iterating-Over-Triples_RPAR_) } } [grind]](#manual-grind) [IterStep.successor_yield]All goals completed! 🐙
@[[grind!]](#manual-Lean___Parser___Attr___grind___) .]
theorem acc_fst [[Pure]](#manual-Pure___mk) m] :
[Acc]](#manual-Acc___intro) (IterM.IsPlausibleSuccessorOf (m := m))
⟨{ [triple]](#manual-TripleIterator___triple-_LPAR_in-Iterating-Over-Triples_RPAR_), [pos]](#manual-TripleIterator___pos-_LPAR_in-Iterating-Over-Triples_RPAR_) := [.fst]](#manual-TriplePos___fst-_LPAR_in-Iterating-Over-Triples_RPAR_) : [TripleIterator]](#manual-TripleIterator-_LPAR_in-Iterating-Over-Triples_RPAR_) α}⟩ :=
[Acc.intro]](#manual-Acc___intro) _ fun
| ⟨{ [triple]](#manual-TripleIterator___triple-_LPAR_in-Iterating-Over-Triples_RPAR_), [pos]](#manual-TripleIterator___pos-_LPAR_in-Iterating-Over-Triples_RPAR_) }⟩, ⟨h, h', h''⟩ =>m:Type u_1 → Type u_2α:Type u_1triple✝:[Triple]](#manual-Triple-_LPAR_in-Iterating-Over-Triples_RPAR_) αinst✝:[Pure]](#manual-Pure___mk) m[triple]](#manual-TripleIterator___triple-_LPAR_in-Iterating-Over-Triples_RPAR_):[Triple]](#manual-Triple-_LPAR_in-Iterating-Over-Triples_RPAR_) α[pos]](#manual-TripleIterator___pos-_LPAR_in-Iterating-Over-Triples_RPAR_):[TriplePos]](#manual-TriplePos-_LPAR_in-Iterating-Over-Triples_RPAR_)h:[IterStep]](#manual-Std___IterStep___yield) ([IterM]](#manual-Std___IterM___mk) m α) αh':h.successor [=]](#manual-Eq___refl) [some]](#manual-Option___none) { [internalState]](#manual-Std___IterM___mk) := { [triple]](#manual-TripleIterator___triple-_LPAR_in-Iterating-Over-Triples_RPAR_) := [triple]](#manual-TripleIterator___triple-_LPAR_in-Iterating-Over-Triples_RPAR_), [pos]](#manual-TripleIterator___pos-_LPAR_in-Iterating-Over-Triples_RPAR_) := [pos]](#manual-TripleIterator___pos-_LPAR_in-Iterating-Over-Triples_RPAR_) } }h'':{ [internalState]](#manual-Std___IterM___mk) := { [triple]](#manual-TripleIterator___triple-_LPAR_in-Iterating-Over-Triples_RPAR_) := triple✝, [pos]](#manual-TripleIterator___pos-_LPAR_in-Iterating-Over-Triples_RPAR_) := [TriplePos.fst]](#manual-TriplePos___fst-_LPAR_in-Iterating-Over-Triples_RPAR_) } }.IsPlausibleStep h⊢ [Acc]](#manual-Acc___intro) IterM.IsPlausibleSuccessorOf { [internalState]](#manual-Std___IterM___mk) := { [triple]](#manual-TripleIterator___triple-_LPAR_in-Iterating-Over-Triples_RPAR_) := [triple]](#manual-TripleIterator___triple-_LPAR_in-Iterating-Over-Triples_RPAR_), [pos]](#manual-TripleIterator___pos-_LPAR_in-Iterating-Over-Triples_RPAR_) := [pos]](#manual-TripleIterator___pos-_LPAR_in-Iterating-Over-Triples_RPAR_) } } bym:Type u_1 → Type u_2α:Type u_1triple✝:[Triple]](#manual-Triple-_LPAR_in-Iterating-Over-Triples_RPAR_) αinst✝:[Pure]](#manual-Pure___mk) m[triple]](#manual-TripleIterator___triple-_LPAR_in-Iterating-Over-Triples_RPAR_):[Triple]](#manual-Triple-_LPAR_in-Iterating-Over-Triples_RPAR_) α[pos]](#manual-TripleIterator___pos-_LPAR_in-Iterating-Over-Triples_RPAR_):[TriplePos]](#manual-TriplePos-_LPAR_in-Iterating-Over-Triples_RPAR_)h:[IterStep]](#manual-Std___IterStep___yield) ([IterM]](#manual-Std___IterM___mk) m α) αh':h.successor [=]](#manual-Eq___refl) [some]](#manual-Option___none) { [internalState]](#manual-Std___IterM___mk) := { [triple]](#manual-TripleIterator___triple-_LPAR_in-Iterating-Over-Triples_RPAR_) := [triple]](#manual-TripleIterator___triple-_LPAR_in-Iterating-Over-Triples_RPAR_), [pos]](#manual-TripleIterator___pos-_LPAR_in-Iterating-Over-Triples_RPAR_) := [pos]](#manual-TripleIterator___pos-_LPAR_in-Iterating-Over-Triples_RPAR_) } }h'':{ [internalState]](#manual-Std___IterM___mk) := { [triple]](#manual-TripleIterator___triple-_LPAR_in-Iterating-Over-Triples_RPAR_) := triple✝, [pos]](#manual-TripleIterator___pos-_LPAR_in-Iterating-Over-Triples_RPAR_) := [TriplePos.fst]](#manual-TriplePos___fst-_LPAR_in-Iterating-Over-Triples_RPAR_) } }.IsPlausibleStep h⊢ [Acc]](#manual-Acc___intro) IterM.IsPlausibleSuccessorOf { [internalState]](#manual-Std___IterM___mk) := { [triple]](#manual-TripleIterator___triple-_LPAR_in-Iterating-Over-Triples_RPAR_) := [triple]](#manual-TripleIterator___triple-_LPAR_in-Iterating-Over-Triples_RPAR_), [pos]](#manual-TripleIterator___pos-_LPAR_in-Iterating-Over-Triples_RPAR_) := [pos]](#manual-TripleIterator___pos-_LPAR_in-Iterating-Over-Triples_RPAR_) } }
[cases]](#manual-cases) h''yieldm:Type u_1 → Type u_2α:Type u_1triple✝:[Triple]](#manual-Triple-_LPAR_in-Iterating-Over-Triples_RPAR_) αinst✝:[Pure]](#manual-Pure___mk) m[triple]](#manual-TripleIterator___triple-_LPAR_in-Iterating-Over-Triples_RPAR_):[Triple]](#manual-Triple-_LPAR_in-Iterating-Over-Triples_RPAR_) α[pos]](#manual-TripleIterator___pos-_LPAR_in-Iterating-Over-Triples_RPAR_):[TriplePos]](#manual-TriplePos-_LPAR_in-Iterating-Over-Triples_RPAR_)out✝:αit'✝:[IterM]](#manual-Std___IterM___mk) m αa✝²:{ [internalState]](#manual-Std___IterM___mk) := { [triple]](#manual-TripleIterator___triple-_LPAR_in-Iterating-Over-Triples_RPAR_) := triple✝, [pos]](#manual-TripleIterator___pos-_LPAR_in-Iterating-Over-Triples_RPAR_) := [TriplePos.fst]](#manual-TriplePos___fst-_LPAR_in-Iterating-Over-Triples_RPAR_) } }.[internalState]](#manual-Std___IterM___mk).[triple]](#manual-TripleIterator___triple-_LPAR_in-Iterating-Over-Triples_RPAR_) [=]](#manual-Eq___refl) it'✝.[internalState]](#manual-Std___IterM___mk).[triple]](#manual-TripleIterator___triple-_LPAR_in-Iterating-Over-Triples_RPAR_)a✝¹:{ [internalState]](#manual-Std___IterM___mk) := { [triple]](#manual-TripleIterator___triple-_LPAR_in-Iterating-Over-Triples_RPAR_) := triple✝, [pos]](#manual-TripleIterator___pos-_LPAR_in-Iterating-Over-Triples_RPAR_) := [TriplePos.fst]](#manual-TriplePos___fst-_LPAR_in-Iterating-Over-Triples_RPAR_) } }.[internalState]](#manual-Std___IterM___mk).[pos]](#manual-TripleIterator___pos-_LPAR_in-Iterating-Over-Triples_RPAR_).[Succ]](#manual-TriplePos___Succ-_LPAR_in-Iterating-Over-Triples_RPAR_) it'✝.[internalState]](#manual-Std___IterM___mk).[pos]](#manual-TripleIterator___pos-_LPAR_in-Iterating-Over-Triples_RPAR_)a✝:{ [internalState]](#manual-Std___IterM___mk) := { [triple]](#manual-TripleIterator___triple-_LPAR_in-Iterating-Over-Triples_RPAR_) := triple✝, [pos]](#manual-TripleIterator___pos-_LPAR_in-Iterating-Over-Triples_RPAR_) := [TriplePos.fst]](#manual-TriplePos___fst-_LPAR_in-Iterating-Over-Triples_RPAR_) } }.[internalState]](#manual-Std___IterM___mk).[triple]](#manual-TripleIterator___triple-_LPAR_in-Iterating-Over-Triples_RPAR_).[get?]](#manual-Triple___get___-_LPAR_in-Iterating-Over-Triples_RPAR_)
{ [internalState]](#manual-Std___IterM___mk) := { [triple]](#manual-TripleIterator___triple-_LPAR_in-Iterating-Over-Triples_RPAR_) := triple✝, [pos]](#manual-TripleIterator___pos-_LPAR_in-Iterating-Over-Triples_RPAR_) := [TriplePos.fst]](#manual-TriplePos___fst-_LPAR_in-Iterating-Over-Triples_RPAR_) } }.[internalState]](#manual-Std___IterM___mk).[pos]](#manual-TripleIterator___pos-_LPAR_in-Iterating-Over-Triples_RPAR_) [=]](#manual-Eq___refl)
[some]](#manual-Option___none) out✝h':([IterStep.yield]](#manual-Std___IterStep___yield) it'✝ out✝).successor [=]](#manual-Eq___refl) [some]](#manual-Option___none) { [internalState]](#manual-Std___IterM___mk) := { [triple]](#manual-TripleIterator___triple-_LPAR_in-Iterating-Over-Triples_RPAR_) := [triple]](#manual-TripleIterator___triple-_LPAR_in-Iterating-Over-Triples_RPAR_), [pos]](#manual-TripleIterator___pos-_LPAR_in-Iterating-Over-Triples_RPAR_) := [pos]](#manual-TripleIterator___pos-_LPAR_in-Iterating-Over-Triples_RPAR_) } }⊢ [Acc]](#manual-Acc___intro) IterM.IsPlausibleSuccessorOf { [internalState]](#manual-Std___IterM___mk) := { [triple]](#manual-TripleIterator___triple-_LPAR_in-Iterating-Over-Triples_RPAR_) := [triple]](#manual-TripleIterator___triple-_LPAR_in-Iterating-Over-Triples_RPAR_), [pos]](#manual-TripleIterator___pos-_LPAR_in-Iterating-Over-Triples_RPAR_) := [pos]](#manual-TripleIterator___pos-_LPAR_in-Iterating-Over-Triples_RPAR_) } }donem:Type u_1 → Type u_2α:Type u_1triple✝:[Triple]](#manual-Triple-_LPAR_in-Iterating-Over-Triples_RPAR_) αinst✝:[Pure]](#manual-Pure___mk) m[triple]](#manual-TripleIterator___triple-_LPAR_in-Iterating-Over-Triples_RPAR_):[Triple]](#manual-Triple-_LPAR_in-Iterating-Over-Triples_RPAR_) α[pos]](#manual-TripleIterator___pos-_LPAR_in-Iterating-Over-Triples_RPAR_):[TriplePos]](#manual-TriplePos-_LPAR_in-Iterating-Over-Triples_RPAR_)a✝:{ [internalState]](#manual-Std___IterM___mk) := { [triple]](#manual-TripleIterator___triple-_LPAR_in-Iterating-Over-Triples_RPAR_) := triple✝, [pos]](#manual-TripleIterator___pos-_LPAR_in-Iterating-Over-Triples_RPAR_) := [TriplePos.fst]](#manual-TriplePos___fst-_LPAR_in-Iterating-Over-Triples_RPAR_) } }.[internalState]](#manual-Std___IterM___mk).[pos]](#manual-TripleIterator___pos-_LPAR_in-Iterating-Over-Triples_RPAR_) [=]](#manual-Eq___refl) [TriplePos.done]](#manual-TriplePos___done-_LPAR_in-Iterating-Over-Triples_RPAR_)h':[IterStep.done]](#manual-Std___IterStep___yield).successor [=]](#manual-Eq___refl) [some]](#manual-Option___none) { [internalState]](#manual-Std___IterM___mk) := { [triple]](#manual-TripleIterator___triple-_LPAR_in-Iterating-Over-Triples_RPAR_) := [triple]](#manual-TripleIterator___triple-_LPAR_in-Iterating-Over-Triples_RPAR_), [pos]](#manual-TripleIterator___pos-_LPAR_in-Iterating-Over-Triples_RPAR_) := [pos]](#manual-TripleIterator___pos-_LPAR_in-Iterating-Over-Triples_RPAR_) } }⊢ [Acc]](#manual-Acc___intro) IterM.IsPlausibleSuccessorOf { [internalState]](#manual-Std___IterM___mk) := { [triple]](#manual-TripleIterator___triple-_LPAR_in-Iterating-Over-Triples_RPAR_) := [triple]](#manual-TripleIterator___triple-_LPAR_in-Iterating-Over-Triples_RPAR_), [pos]](#manual-TripleIterator___pos-_LPAR_in-Iterating-Over-Triples_RPAR_) := [pos]](#manual-TripleIterator___pos-_LPAR_in-Iterating-Over-Triples_RPAR_) } } <;>yieldm:Type u_1 → Type u_2α:Type u_1triple✝:[Triple]](#manual-Triple-_LPAR_in-Iterating-Over-Triples_RPAR_) αinst✝:[Pure]](#manual-Pure___mk) m[triple]](#manual-TripleIterator___triple-_LPAR_in-Iterating-Over-Triples_RPAR_):[Triple]](#manual-Triple-_LPAR_in-Iterating-Over-Triples_RPAR_) α[pos]](#manual-TripleIterator___pos-_LPAR_in-Iterating-Over-Triples_RPAR_):[TriplePos]](#manual-TriplePos-_LPAR_in-Iterating-Over-Triples_RPAR_)out✝:αit'✝:[IterM]](#manual-Std___IterM___mk) m αa✝²:{ [internalState]](#manual-Std___IterM___mk) := { [triple]](#manual-TripleIterator___triple-_LPAR_in-Iterating-Over-Triples_RPAR_) := triple✝, [pos]](#manual-TripleIterator___pos-_LPAR_in-Iterating-Over-Triples_RPAR_) := [TriplePos.fst]](#manual-TriplePos___fst-_LPAR_in-Iterating-Over-Triples_RPAR_) } }.[internalState]](#manual-Std___IterM___mk).[triple]](#manual-TripleIterator___triple-_LPAR_in-Iterating-Over-Triples_RPAR_) [=]](#manual-Eq___refl) it'✝.[internalState]](#manual-Std___IterM___mk).[triple]](#manual-TripleIterator___triple-_LPAR_in-Iterating-Over-Triples_RPAR_)a✝¹:{ [internalState]](#manual-Std___IterM___mk) := { [triple]](#manual-TripleIterator___triple-_LPAR_in-Iterating-Over-Triples_RPAR_) := triple✝, [pos]](#manual-TripleIterator___pos-_LPAR_in-Iterating-Over-Triples_RPAR_) := [TriplePos.fst]](#manual-TriplePos___fst-_LPAR_in-Iterating-Over-Triples_RPAR_) } }.[internalState]](#manual-Std___IterM___mk).[pos]](#manual-TripleIterator___pos-_LPAR_in-Iterating-Over-Triples_RPAR_).[Succ]](#manual-TriplePos___Succ-_LPAR_in-Iterating-Over-Triples_RPAR_) it'✝.[internalState]](#manual-Std___IterM___mk).[pos]](#manual-TripleIterator___pos-_LPAR_in-Iterating-Over-Triples_RPAR_)a✝:{ [internalState]](#manual-Std___IterM___mk) := { [triple]](#manual-TripleIterator___triple-_LPAR_in-Iterating-Over-Triples_RPAR_) := triple✝, [pos]](#manual-TripleIterator___pos-_LPAR_in-Iterating-Over-Triples_RPAR_) := [TriplePos.fst]](#manual-TriplePos___fst-_LPAR_in-Iterating-Over-Triples_RPAR_) } }.[internalState]](#manual-Std___IterM___mk).[triple]](#manual-TripleIterator___triple-_LPAR_in-Iterating-Over-Triples_RPAR_).[get?]](#manual-Triple___get___-_LPAR_in-Iterating-Over-Triples_RPAR_)
{ [internalState]](#manual-Std___IterM___mk) := { [triple]](#manual-TripleIterator___triple-_LPAR_in-Iterating-Over-Triples_RPAR_) := triple✝, [pos]](#manual-TripleIterator___pos-_LPAR_in-Iterating-Over-Triples_RPAR_) := [TriplePos.fst]](#manual-TriplePos___fst-_LPAR_in-Iterating-Over-Triples_RPAR_) } }.[internalState]](#manual-Std___IterM___mk).[pos]](#manual-TripleIterator___pos-_LPAR_in-Iterating-Over-Triples_RPAR_) [=]](#manual-Eq___refl)
[some]](#manual-Option___none) out✝h':([IterStep.yield]](#manual-Std___IterStep___yield) it'✝ out✝).successor [=]](#manual-Eq___refl) [some]](#manual-Option___none) { [internalState]](#manual-Std___IterM___mk) := { [triple]](#manual-TripleIterator___triple-_LPAR_in-Iterating-Over-Triples_RPAR_) := [triple]](#manual-TripleIterator___triple-_LPAR_in-Iterating-Over-Triples_RPAR_), [pos]](#manual-TripleIterator___pos-_LPAR_in-Iterating-Over-Triples_RPAR_) := [pos]](#manual-TripleIterator___pos-_LPAR_in-Iterating-Over-Triples_RPAR_) } }⊢ [Acc]](#manual-Acc___intro) IterM.IsPlausibleSuccessorOf { [internalState]](#manual-Std___IterM___mk) := { [triple]](#manual-TripleIterator___triple-_LPAR_in-Iterating-Over-Triples_RPAR_) := [triple]](#manual-TripleIterator___triple-_LPAR_in-Iterating-Over-Triples_RPAR_), [pos]](#manual-TripleIterator___pos-_LPAR_in-Iterating-Over-Triples_RPAR_) := [pos]](#manual-TripleIterator___pos-_LPAR_in-Iterating-Over-Triples_RPAR_) } }donem:Type u_1 → Type u_2α:Type u_1triple✝:[Triple]](#manual-Triple-_LPAR_in-Iterating-Over-Triples_RPAR_) αinst✝:[Pure]](#manual-Pure___mk) m[triple]](#manual-TripleIterator___triple-_LPAR_in-Iterating-Over-Triples_RPAR_):[Triple]](#manual-Triple-_LPAR_in-Iterating-Over-Triples_RPAR_) α[pos]](#manual-TripleIterator___pos-_LPAR_in-Iterating-Over-Triples_RPAR_):[TriplePos]](#manual-TriplePos-_LPAR_in-Iterating-Over-Triples_RPAR_)a✝:{ [internalState]](#manual-Std___IterM___mk) := { [triple]](#manual-TripleIterator___triple-_LPAR_in-Iterating-Over-Triples_RPAR_) := triple✝, [pos]](#manual-TripleIterator___pos-_LPAR_in-Iterating-Over-Triples_RPAR_) := [TriplePos.fst]](#manual-TriplePos___fst-_LPAR_in-Iterating-Over-Triples_RPAR_) } }.[internalState]](#manual-Std___IterM___mk).[pos]](#manual-TripleIterator___pos-_LPAR_in-Iterating-Over-Triples_RPAR_) [=]](#manual-Eq___refl) [TriplePos.done]](#manual-TriplePos___done-_LPAR_in-Iterating-Over-Triples_RPAR_)h':[IterStep.done]](#manual-Std___IterStep___yield).successor [=]](#manual-Eq___refl) [some]](#manual-Option___none) { [internalState]](#manual-Std___IterM___mk) := { [triple]](#manual-TripleIterator___triple-_LPAR_in-Iterating-Over-Triples_RPAR_) := [triple]](#manual-TripleIterator___triple-_LPAR_in-Iterating-Over-Triples_RPAR_), [pos]](#manual-TripleIterator___pos-_LPAR_in-Iterating-Over-Triples_RPAR_) := [pos]](#manual-TripleIterator___pos-_LPAR_in-Iterating-Over-Triples_RPAR_) } }⊢ [Acc]](#manual-Acc___intro) IterM.IsPlausibleSuccessorOf { [internalState]](#manual-Std___IterM___mk) := { [triple]](#manual-TripleIterator___triple-_LPAR_in-Iterating-Over-Triples_RPAR_) := [triple]](#manual-TripleIterator___triple-_LPAR_in-Iterating-Over-Triples_RPAR_), [pos]](#manual-TripleIterator___pos-_LPAR_in-Iterating-Over-Triples_RPAR_) := [pos]](#manual-TripleIterator___pos-_LPAR_in-Iterating-Over-Triples_RPAR_) } } [grind]](#manual-grind) [IterStep.successor_yield]All goals completed! 🐙
instance [[Pure]](#manual-Pure___mk) m] : [Finite]](#manual-Std___Iterators___Finite___mk) ([TripleIterator]](#manual-TripleIterator-_LPAR_in-Iterating-Over-Triples_RPAR_) α) m where
[wf]](#manual-Std___Iterators___Finite___mk) := [.intro]](#manual-WellFounded___intro) <| fun
| { [internalState]](#manual-Std___IterM___mk) := { [triple]](#manual-TripleIterator___triple-_LPAR_in-Iterating-Over-Triples_RPAR_), [pos]](#manual-TripleIterator___pos-_LPAR_in-Iterating-Over-Triples_RPAR_) } } =>m:Type u_1 → Type u_2α:Type u_1inst✝:[Pure]](#manual-Pure___mk) m[triple]](#manual-TripleIterator___triple-_LPAR_in-Iterating-Over-Triples_RPAR_):[Triple]](#manual-Triple-_LPAR_in-Iterating-Over-Triples_RPAR_) α[pos]](#manual-TripleIterator___pos-_LPAR_in-Iterating-Over-Triples_RPAR_):[TriplePos]](#manual-TriplePos-_LPAR_in-Iterating-Over-Triples_RPAR_)⊢ [Acc]](#manual-Acc___intro) IterM.IsPlausibleSuccessorOf { [internalState]](#manual-Std___IterM___mk) := { [triple]](#manual-TripleIterator___triple-_LPAR_in-Iterating-Over-Triples_RPAR_) := [triple]](#manual-TripleIterator___triple-_LPAR_in-Iterating-Over-Triples_RPAR_), [pos]](#manual-TripleIterator___pos-_LPAR_in-Iterating-Over-Triples_RPAR_) := [pos]](#manual-TripleIterator___pos-_LPAR_in-Iterating-Over-Triples_RPAR_) } } bym:Type u_1 → Type u_2α:Type u_1inst✝:[Pure]](#manual-Pure___mk) m[triple]](#manual-TripleIterator___triple-_LPAR_in-Iterating-Over-Triples_RPAR_):[Triple]](#manual-Triple-_LPAR_in-Iterating-Over-Triples_RPAR_) α[pos]](#manual-TripleIterator___pos-_LPAR_in-Iterating-Over-Triples_RPAR_):[TriplePos]](#manual-TriplePos-_LPAR_in-Iterating-Over-Triples_RPAR_)⊢ [Acc]](#manual-Acc___intro) IterM.IsPlausibleSuccessorOf { [internalState]](#manual-Std___IterM___mk) := { [triple]](#manual-TripleIterator___triple-_LPAR_in-Iterating-Over-Triples_RPAR_) := [triple]](#manual-TripleIterator___triple-_LPAR_in-Iterating-Over-Triples_RPAR_), [pos]](#manual-TripleIterator___pos-_LPAR_in-Iterating-Over-Triples_RPAR_) := [pos]](#manual-TripleIterator___pos-_LPAR_in-Iterating-Over-Triples_RPAR_) } }
[cases]](#manual-cases) [pos]](#manual-TripleIterator___pos-_LPAR_in-Iterating-Over-Triples_RPAR_)fstm:Type u_1 → Type u_2α:Type u_1inst✝:[Pure]](#manual-Pure___mk) m[triple]](#manual-TripleIterator___triple-_LPAR_in-Iterating-Over-Triples_RPAR_):[Triple]](#manual-Triple-_LPAR_in-Iterating-Over-Triples_RPAR_) α⊢ [Acc]](#manual-Acc___intro) IterM.IsPlausibleSuccessorOf { [internalState]](#manual-Std___IterM___mk) := { [triple]](#manual-TripleIterator___triple-_LPAR_in-Iterating-Over-Triples_RPAR_) := [triple]](#manual-TripleIterator___triple-_LPAR_in-Iterating-Over-Triples_RPAR_), [pos]](#manual-TripleIterator___pos-_LPAR_in-Iterating-Over-Triples_RPAR_) := [TriplePos.fst]](#manual-TriplePos___fst-_LPAR_in-Iterating-Over-Triples_RPAR_) } }sndm:Type u_1 → Type u_2α:Type u_1inst✝:[Pure]](#manual-Pure___mk) m[triple]](#manual-TripleIterator___triple-_LPAR_in-Iterating-Over-Triples_RPAR_):[Triple]](#manual-Triple-_LPAR_in-Iterating-Over-Triples_RPAR_) α⊢ [Acc]](#manual-Acc___intro) IterM.IsPlausibleSuccessorOf { [internalState]](#manual-Std___IterM___mk) := { [triple]](#manual-TripleIterator___triple-_LPAR_in-Iterating-Over-Triples_RPAR_) := [triple]](#manual-TripleIterator___triple-_LPAR_in-Iterating-Over-Triples_RPAR_), [pos]](#manual-TripleIterator___pos-_LPAR_in-Iterating-Over-Triples_RPAR_) := [TriplePos.snd]](#manual-TriplePos___snd-_LPAR_in-Iterating-Over-Triples_RPAR_) } }thdm:Type u_1 → Type u_2α:Type u_1inst✝:[Pure]](#manual-Pure___mk) m[triple]](#manual-TripleIterator___triple-_LPAR_in-Iterating-Over-Triples_RPAR_):[Triple]](#manual-Triple-_LPAR_in-Iterating-Over-Triples_RPAR_) α⊢ [Acc]](#manual-Acc___intro) IterM.IsPlausibleSuccessorOf { [internalState]](#manual-Std___IterM___mk) := { [triple]](#manual-TripleIterator___triple-_LPAR_in-Iterating-Over-Triples_RPAR_) := [triple]](#manual-TripleIterator___triple-_LPAR_in-Iterating-Over-Triples_RPAR_), [pos]](#manual-TripleIterator___pos-_LPAR_in-Iterating-Over-Triples_RPAR_) := [TriplePos.thd]](#manual-TriplePos___thd-_LPAR_in-Iterating-Over-Triples_RPAR_) } }donem:Type u_1 → Type u_2α:Type u_1inst✝:[Pure]](#manual-Pure___mk) m[triple]](#manual-TripleIterator___triple-_LPAR_in-Iterating-Over-Triples_RPAR_):[Triple]](#manual-Triple-_LPAR_in-Iterating-Over-Triples_RPAR_) α⊢ [Acc]](#manual-Acc___intro) IterM.IsPlausibleSuccessorOf { [internalState]](#manual-Std___IterM___mk) := { [triple]](#manual-TripleIterator___triple-_LPAR_in-Iterating-Over-Triples_RPAR_) := [triple]](#manual-TripleIterator___triple-_LPAR_in-Iterating-Over-Triples_RPAR_), [pos]](#manual-TripleIterator___pos-_LPAR_in-Iterating-Over-Triples_RPAR_) := [TriplePos.done]](#manual-TriplePos___done-_LPAR_in-Iterating-Over-Triples_RPAR_) } } <;>fstm:Type u_1 → Type u_2α:Type u_1inst✝:[Pure]](#manual-Pure___mk) m[triple]](#manual-TripleIterator___triple-_LPAR_in-Iterating-Over-Triples_RPAR_):[Triple]](#manual-Triple-_LPAR_in-Iterating-Over-Triples_RPAR_) α⊢ [Acc]](#manual-Acc___intro) IterM.IsPlausibleSuccessorOf { [internalState]](#manual-Std___IterM___mk) := { [triple]](#manual-TripleIterator___triple-_LPAR_in-Iterating-Over-Triples_RPAR_) := [triple]](#manual-TripleIterator___triple-_LPAR_in-Iterating-Over-Triples_RPAR_), [pos]](#manual-TripleIterator___pos-_LPAR_in-Iterating-Over-Triples_RPAR_) := [TriplePos.fst]](#manual-TriplePos___fst-_LPAR_in-Iterating-Over-Triples_RPAR_) } }sndm:Type u_1 → Type u_2α:Type u_1inst✝:[Pure]](#manual-Pure___mk) m[triple]](#manual-TripleIterator___triple-_LPAR_in-Iterating-Over-Triples_RPAR_):[Triple]](#manual-Triple-_LPAR_in-Iterating-Over-Triples_RPAR_) α⊢ [Acc]](#manual-Acc___intro) IterM.IsPlausibleSuccessorOf { [internalState]](#manual-Std___IterM___mk) := { [triple]](#manual-TripleIterator___triple-_LPAR_in-Iterating-Over-Triples_RPAR_) := [triple]](#manual-TripleIterator___triple-_LPAR_in-Iterating-Over-Triples_RPAR_), [pos]](#manual-TripleIterator___pos-_LPAR_in-Iterating-Over-Triples_RPAR_) := [TriplePos.snd]](#manual-TriplePos___snd-_LPAR_in-Iterating-Over-Triples_RPAR_) } }thdm:Type u_1 → Type u_2α:Type u_1inst✝:[Pure]](#manual-Pure___mk) m[triple]](#manual-TripleIterator___triple-_LPAR_in-Iterating-Over-Triples_RPAR_):[Triple]](#manual-Triple-_LPAR_in-Iterating-Over-Triples_RPAR_) α⊢ [Acc]](#manual-Acc___intro) IterM.IsPlausibleSuccessorOf { [internalState]](#manual-Std___IterM___mk) := { [triple]](#manual-TripleIterator___triple-_LPAR_in-Iterating-Over-Triples_RPAR_) := [triple]](#manual-TripleIterator___triple-_LPAR_in-Iterating-Over-Triples_RPAR_), [pos]](#manual-TripleIterator___pos-_LPAR_in-Iterating-Over-Triples_RPAR_) := [TriplePos.thd]](#manual-TriplePos___thd-_LPAR_in-Iterating-Over-Triples_RPAR_) } }donem:Type u_1 → Type u_2α:Type u_1inst✝:[Pure]](#manual-Pure___mk) m[triple]](#manual-TripleIterator___triple-_LPAR_in-Iterating-Over-Triples_RPAR_):[Triple]](#manual-Triple-_LPAR_in-Iterating-Over-Triples_RPAR_) α⊢ [Acc]](#manual-Acc___intro) IterM.IsPlausibleSuccessorOf { [internalState]](#manual-Std___IterM___mk) := { [triple]](#manual-TripleIterator___triple-_LPAR_in-Iterating-Over-Triples_RPAR_) := [triple]](#manual-TripleIterator___triple-_LPAR_in-Iterating-Over-Triples_RPAR_), [pos]](#manual-TripleIterator___pos-_LPAR_in-Iterating-Over-Triples_RPAR_) := [TriplePos.done]](#manual-TriplePos___done-_LPAR_in-Iterating-Over-Triples_RPAR_) } } [grind]](#manual-grind)All goals completed! 🐙
```

To enable the iterator in `for` loops, an instance of `[IteratorLoop]](#manual-Std___IteratorLoop___mk)` are needed:

```lean
instance [[Monad]](#manual-Monad___mk) m] [[Monad]](#manual-Monad___mk) n] :
[IteratorLoop]](#manual-Std___IteratorLoop___mk) ([TripleIterator]](#manual-TripleIterator-_LPAR_in-Iterating-Over-Triples_RPAR_) α) m n :=
[.defaultImplementation]](#manual-Std___IteratorLoop___defaultImplementation)
```

```lean
[#eval]](#manual-Lean___Parser___Command___eval) show [IO]](#manual-IO) [Unit]](#manual-Unit) from [do]](#manual-Lean___Parser___Term___do)
for x in [abc]](#manual-abc-_LPAR_in-Iterating-Over-Triples_RPAR_).[iter]](#manual-Triple___iter-_LPAR_in-Iterating-Over-Triples_RPAR_) do
[IO.println]](#manual-IO___println) x
```

```lean
a
b
c
```

**Example: Iterators and Effects**

One way to iterate over the contents of a file is to read a specified number of bytes from a `[Stream]](#manual-IO___FS___Stream___mk)` at each step.
When EOF is reached, the iterator can close the file by letting its reference count drop to zero:

```lean
structure FileIterator where
stream? : [Option]](#manual-Option___none) [IO.FS.Stream]](#manual-IO___FS___Stream___mk)
count : [USize]](#manual-USize___ofBitVec) := 8192
```

An iterator can be created by opening a file and converting its handle to a stream:

```lean
def iterFile
(path : [System.FilePath]](#manual-System___FilePath___mk))
(count : [USize]](#manual-USize___ofBitVec) := 8192) :
[IO]](#manual-IO) ([IterM]](#manual-Std___IterM___mk) (α := [FileIterator]](#manual-FileIterator-_LPAR_in-Iterators-and-Effects_RPAR_)) [IO]](#manual-IO) [ByteArray]](#manual-ByteArray___mk)) := [do]](#manual-Lean___Parser___Term___do)
let h ← [IO.FS.Handle.mk]](#manual-IO___FS___Handle___mk) path [.read]](#manual-IO___FS___Mode___read)
let stream? := [some]](#manual-Option___none) ([IO.FS.Stream.ofHandle]](#manual-IO___FS___Stream___ofHandle) h)
return IterM.mk { [stream?]](#manual-FileIterator___stream___-_LPAR_in-Iterators-and-Effects_RPAR_), [count]](#manual-FileIterator___count-_LPAR_in-Iterators-and-Effects_RPAR_) }
```

For this iterator, a `[yield]](#manual-Std___IterStep___yield)` is plausible when the file is still open, and `[done]](#manual-Std___IterStep___yield)` is plausible when the file is closed.
The actual step function performs a read and closes the file if no bytes were returned:

```lean
instance : [Iterator]](#manual-Std___Iterator___mk) [FileIterator]](#manual-FileIterator-_LPAR_in-Iterators-and-Effects_RPAR_) [IO]](#manual-IO) [ByteArray]](#manual-ByteArray___mk) where
[IsPlausibleStep]](#manual-Std___Iterator___mk) it
| [.yield]](#manual-Std___IterStep___yield) .. =>
it.[internalState]](#manual-Std___IterM___mk).[stream?]](#manual-FileIterator___stream___-_LPAR_in-Iterators-and-Effects_RPAR_).[isSome]](#manual-Option___isSome)
| [.skip]](#manual-Std___IterStep___yield) .. => [False]](#manual-False)
| [.done]](#manual-Std___IterStep___yield) => it.[internalState]](#manual-Std___IterM___mk).[stream?]](#manual-FileIterator___stream___-_LPAR_in-Iterators-and-Effects_RPAR_).[isNone]](#manual-Option___isNone)
[step]](#manual-Std___Iterator___mk) it := [do]](#manual-Lean___Parser___Term___do)
match h : it.[internalState]](#manual-Std___IterM___mk).[stream?]](#manual-FileIterator___stream___-_LPAR_in-Iterators-and-Effects_RPAR_) with
| [none]](#manual-Option___none) => return [.deflate]](#manual-Std___Shrink___deflate) <| [.done]](#manual-Std___PlausibleIterStep___done) (byit:[IterM]](#manual-Std___IterM___mk) [IO]](#manual-IO) [ByteArray]](#manual-ByteArray___mk)h:it.[internalState]](#manual-Std___IterM___mk).[stream?]](#manual-FileIterator___stream___-_LPAR_in-Iterators-and-Effects_RPAR_) [=]](#manual-Eq___refl) [none]](#manual-Option___none)⊢ match [IterStep.done]](#manual-Std___IterStep___yield) with
| [IterStep.yield]](#manual-Std___IterStep___yield) it_1 out => it.[internalState]](#manual-Std___IterM___mk).[stream?]](#manual-FileIterator___stream___-_LPAR_in-Iterators-and-Effects_RPAR_).[isSome]](#manual-Option___isSome) [=]](#manual-Eq___refl) [true]](#manual-Bool___false)
| [IterStep.skip]](#manual-Std___IterStep___yield) it => [False]](#manual-False)
| [IterStep.done]](#manual-Std___IterStep___yield) => it.[internalState]](#manual-Std___IterM___mk).[stream?]](#manual-FileIterator___stream___-_LPAR_in-Iterators-and-Effects_RPAR_).[isNone]](#manual-Option___isNone) [=]](#manual-Eq___refl) [true]](#manual-Bool___false) [simp]](#manual-simp) [h]All goals completed! 🐙)
| [some]](#manual-Option___none) stream =>
let bytes ← stream.[read]](#manual-IO___FS___Stream___mk) it.[internalState]](#manual-Std___IterM___mk).[count]](#manual-FileIterator___count-_LPAR_in-Iterators-and-Effects_RPAR_)
let it' :=
{ it with [internalState]](#manual-Std___IterM___mk).[stream?]](#manual-FileIterator___stream___-_LPAR_in-Iterators-and-Effects_RPAR_) :=
[if]](#manual-termIfThenElse) bytes.[size]](#manual-ByteArray___size) == 0 [then]](#manual-termIfThenElse) [none]](#manual-Option___none) [else]](#manual-termIfThenElse) [some]](#manual-Option___none) stream
}
return [.deflate]](#manual-Std___Shrink___deflate) <| [.yield]](#manual-Std___PlausibleIterStep___yield) it' bytes (byit:[IterM]](#manual-Std___IterM___mk) [IO]](#manual-IO) [ByteArray]](#manual-ByteArray___mk)stream:[IO.FS.Stream]](#manual-IO___FS___Stream___mk)h:it.[internalState]](#manual-Std___IterM___mk).[stream?]](#manual-FileIterator___stream___-_LPAR_in-Iterators-and-Effects_RPAR_) [=]](#manual-Eq___refl) [some]](#manual-Option___none) streambytes:[ByteArray]](#manual-ByteArray___mk)it':[IterM]](#manual-Std___IterM___mk) [IO]](#manual-IO) [ByteArray]](#manual-ByteArray___mk) :=
{
[internalState]](#manual-Std___IterM___mk) :=
let __src := it.[internalState]](#manual-Std___IterM___mk);
{ [stream?]](#manual-FileIterator___stream___-_LPAR_in-Iterators-and-Effects_RPAR_) := if [(]](#manual-BEq___mk)bytes.[size]](#manual-ByteArray___size) [==]](#manual-BEq___mk) 0[)]](#manual-BEq___mk) [=]](#manual-Eq___refl) [true]](#manual-Bool___false) then [none]](#manual-Option___none) else [some]](#manual-Option___none) stream, [count]](#manual-FileIterator___count-_LPAR_in-Iterators-and-Effects_RPAR_) := __src.[count]](#manual-FileIterator___count-_LPAR_in-Iterators-and-Effects_RPAR_) } }⊢ match [IterStep.yield]](#manual-Std___IterStep___yield) it' bytes with
| [IterStep.yield]](#manual-Std___IterStep___yield) it_1 out => it.[internalState]](#manual-Std___IterM___mk).[stream?]](#manual-FileIterator___stream___-_LPAR_in-Iterators-and-Effects_RPAR_).[isSome]](#manual-Option___isSome) [=]](#manual-Eq___refl) [true]](#manual-Bool___false)
| [IterStep.skip]](#manual-Std___IterStep___yield) it => [False]](#manual-False)
| [IterStep.done]](#manual-Std___IterStep___yield) => it.[internalState]](#manual-Std___IterM___mk).[stream?]](#manual-FileIterator___stream___-_LPAR_in-Iterators-and-Effects_RPAR_).[isNone]](#manual-Option___isNone) [=]](#manual-Eq___refl) [true]](#manual-Bool___false) [simp]](#manual-simp) [h]All goals completed! 🐙)
```

To use it in loops, an `[IteratorLoop]](#manual-Std___IteratorLoop___mk)` instance will be necessary.

```lean
instance [[Monad]](#manual-Monad___mk) n] : [IteratorLoop]](#manual-Std___IteratorLoop___mk) [FileIterator]](#manual-FileIterator-_LPAR_in-Iterators-and-Effects_RPAR_) [IO]](#manual-IO) n :=
[.defaultImplementation]](#manual-Std___IteratorLoop___defaultImplementation)
```

This is enough support code to use the iterator to calculate file sizes:

```lean
def fileSize (name : [System.FilePath]](#manual-System___FilePath___mk)) : [IO]](#manual-IO) [Nat]](#manual-Nat___zero) := [do]](#manual-Lean___Parser___Term___do)
let mut size := 0
let f := (← [iterFile]](#manual-iterFile-_LPAR_in-Iterators-and-Effects_RPAR_) name)
for bytes in f do
size := size + bytes.[size]](#manual-ByteArray___size)
return size
```

### 22.2.3. Accessing Elements {#manual-The-Lean-Language-Reference--Iterators--Iterator-Definitions--Accessing-Elements}

Some iterators support efficient random access.
For example, an array iterator can skip any number of elements in constant time by incrementing the index that it maintains into the array.

type class

```lean
[Std.IteratorAccess.{w, w'}]](#manual-Std___IteratorAccess___mk) (α : Type w) (m : Type w → Type w')
  {β : Type w} [[Iterator]](#manual-Std___Iterator___mk) α m β] : Type (max w w')



[Std.IteratorAccess.{w, w'}]](#manual-Std___IteratorAccess___mk) (α : Type w)
  (m : Type w → Type w') {β : Type w}
  [[Iterator]](#manual-Std___Iterator___mk) α m β] : Type (max w w')
```

`[IteratorAccess]](#manual-Std___IteratorAccess___mk) α m` provides efficient implementations for random access or iterators that support
it. `it.nextAtIdx? n` either returns the step in which the `n`th value of `it` is emitted
(necessarily of the form `.yield _ _`) or `.[done]](#manual-done)` if `it` terminates before emitting the `n`th
value.

For monadic iterators, the monadic effects of this operation may differ from manually iterating
to the `n`-th value because `nextAtIdx?` can take shortcuts. By the signature, the return value
is guaranteed to plausible in the sense of `IterM.IsPlausibleNthOutputStep`.

This class is experimental and users of the iterator API should not explicitly depend on it.

Instance Constructor

```lean
[Std.IteratorAccess.mk]](#manual-Std___IteratorAccess___mk).{w, w'}
```

Methods

```lean
nextAtIdx? : (it : [IterM]](#manual-Std___IterM___mk) m β) → (n : [Nat]](#manual-Nat___zero)) → m ([PlausibleIterStep]](#manual-Std___PlausibleIterStep) (IterM.IsPlausibleNthOutputStep n it))
```

`nextAtIdx? it n` either returns the step in which the `n`th value of `it` is emitted
(necessarily of the form `.yield _ _`) or `.[done]](#manual-done)` if `it` terminates before emitting the `n`th
value.

def

```lean
[Std.IterM.nextAtIdx?.{u_1, u_2}]](#manual-Std___IterM___nextAtIdx___) {α : Type u_1} {m : Type u_1 → Type u_2}
  {β : Type u_1} [[Iterator]](#manual-Std___Iterator___mk) α m β] [[IteratorAccess]](#manual-Std___IteratorAccess___mk) α m] (it : [IterM]](#manual-Std___IterM___mk) m β)
  (n : [Nat]](#manual-Nat___zero)) :
  m ([PlausibleIterStep]](#manual-Std___PlausibleIterStep) (IterM.IsPlausibleNthOutputStep n it))



[Std.IterM.nextAtIdx?.{u_1, u_2}]](#manual-Std___IterM___nextAtIdx___)
  {α : Type u_1} {m : Type u_1 → Type u_2}
  {β : Type u_1} [[Iterator]](#manual-Std___Iterator___mk) α m β]
  [[IteratorAccess]](#manual-Std___IteratorAccess___mk) α m] (it : [IterM]](#manual-Std___IterM___mk) m β)
  (n : [Nat]](#manual-Nat___zero)) :
  m
    ([PlausibleIterStep]](#manual-Std___PlausibleIterStep)
      (IterM.IsPlausibleNthOutputStep n
        it))
```

Returns the step in which `it` yields its `n`-th element, or `.[done]](#manual-done)` if it terminates earlier.
In contrast to `step`, this function will always return either `.yield` or `.[done]](#manual-done)` but never a
`.[skip]](#manual-skip)` step.

For monadic iterators, the monadic effects of this operation may differ from manually iterating
to the `n`-th value because `nextAtIdx?` can take shortcuts. By the signature, the return value
is guaranteed to plausible in the sense of `IterM.IsPlausibleNthOutputStep`.

This function is only available for iterators that explicitly support it by implementing
the `[IteratorAccess]](#manual-Std___IteratorAccess___mk)` typeclass.

### 22.2.4. Loops {#manual-The-Lean-Language-Reference--Iterators--Iterator-Definitions--Loops}

type class

```lean
[Std.IteratorLoop.{w, w', x, x'}]](#manual-Std___IteratorLoop___mk) (α : Type w) (m : Type w → Type w')
  {β : Type w} [[Iterator]](#manual-Std___Iterator___mk) α m β] (n : Type x → Type x') :
  Type (max (max (max (w + 1) w') (x + 1)) x')



[Std.IteratorLoop.{w, w', x, x'}]](#manual-Std___IteratorLoop___mk)
  (α : Type w) (m : Type w → Type w')
  {β : Type w} [[Iterator]](#manual-Std___Iterator___mk) α m β]
  (n : Type x → Type x') :
  Type
    (max (max (max (w + 1) w') (x + 1))
        x')
```

`[IteratorLoop]](#manual-Std___IteratorLoop___mk) α m` provides efficient implementations of loop-based consumers for `α`-based
iterators. The basis is a `[ForIn]](#manual-ForIn___mk)`-style loop construct.

Its behavior for well-founded loops is fully characterized by the `[LawfulIteratorLoop]](#manual-Std___LawfulIteratorLoop___mk)` type class.

This class is experimental and users of the iterator API should not explicitly depend on it.
They can, however, assume that consumers that require an instance will work for all iterators
provided by the standard library.

Instance Constructor

```lean
[Std.IteratorLoop.mk]](#manual-Std___IteratorLoop___mk).{w, w', x, x'}
```

Methods

```lean
forIn : ((γ : Type w) → (δ : Type x) → (γ → n δ) → m γ → n δ) →
  (γ : Type x) →
    (plausible_forInStep : β → γ → [ForInStep]](#manual-ForInStep___done) γ → Prop) →
      (it : [IterM]](#manual-Std___IterM___mk) m β) →
        γ → ((b : β) → it.IsPlausibleIndirectOutput b → (c : γ) → n ([Subtype]](#manual-Subtype___mk) (plausible_forInStep b c))) → n γ
```

Iteration over the iterator `it` in the manner expected by `for` loops.

def

```lean
[Std.IteratorLoop.defaultImplementation.{w, w', x, x'}]](#manual-Std___IteratorLoop___defaultImplementation) {β α : Type w}
  {m : Type w → Type w'} {n : Type x → Type x'} [[Monad]](#manual-Monad___mk) n]
  [[Iterator]](#manual-Std___Iterator___mk) α m β] : [IteratorLoop]](#manual-Std___IteratorLoop___mk) α m n



[Std.IteratorLoop.defaultImplementation.{w,
    w', x, x'}]](#manual-Std___IteratorLoop___defaultImplementation)
  {β α : Type w} {m : Type w → Type w'}
  {n : Type x → Type x'} [[Monad]](#manual-Monad___mk) n]
  [[Iterator]](#manual-Std___Iterator___mk) α m β] : [IteratorLoop]](#manual-Std___IteratorLoop___mk) α m n
```

This is the default implementation of the `[IteratorLoop]](#manual-Std___IteratorLoop___mk)` class.
It simply iterates through the iterator using `[IterM.step](https://lean-lang.org/doc/reference/latest/Iterators/Consuming-Iterators/#Std___IterM___step)`. For certain iterators, more efficient
implementations are possible and should be used instead.

type class

```lean
[Std.LawfulIteratorLoop.{w, w', x, x'}]](#manual-Std___LawfulIteratorLoop___mk) {β : Type w} (α : Type w)
  (m : Type w → Type w') (n : Type x → Type x') [[Monad]](#manual-Monad___mk) m] [[Monad]](#manual-Monad___mk) n]
  [[Iterator]](#manual-Std___Iterator___mk) α m β] [i : [IteratorLoop]](#manual-Std___IteratorLoop___mk) α m n] : Prop



[Std.LawfulIteratorLoop.{w, w', x, x'}]](#manual-Std___LawfulIteratorLoop___mk)
  {β : Type w} (α : Type w)
  (m : Type w → Type w')
  (n : Type x → Type x') [[Monad]](#manual-Monad___mk) m]
  [[Monad]](#manual-Monad___mk) n] [[Iterator]](#manual-Std___Iterator___mk) α m β]
  [i : [IteratorLoop]](#manual-Std___IteratorLoop___mk) α m n] : Prop
```

Asserts that a given `[IteratorLoop]](#manual-Std___IteratorLoop___mk)` instance is equal to `[IteratorLoop.defaultImplementation]](#manual-Std___IteratorLoop___defaultImplementation)`.
(Even though equal, the given instance might be vastly more efficient.)

Instance Constructor

```lean
[Std.LawfulIteratorLoop.mk]](#manual-Std___LawfulIteratorLoop___mk).{w, w', x, x'}
```

Methods

```lean
lawful : ∀ (lift : (γ : Type w) → (δ : Type x) → (γ → n δ) → m γ → n δ) [Std.Internal.LawfulMonadLiftBindFunction lift]
  (γ : Type x) (it : [IterM]](#manual-Std___IterM___mk) m β) (init : γ) (Pl : β → γ → [ForInStep]](#manual-ForInStep___done) γ → Prop),
  IteratorLoop.WellFounded α m Pl →
    ∀ (f : (b : β) → it.IsPlausibleIndirectOutput b → (c : γ) → n ([Subtype]](#manual-Subtype___mk) (Pl b c))),
      [IteratorLoop.forIn]](#manual-Std___IteratorLoop___mk) lift γ Pl it init f [=]](#manual-Eq___refl) [IteratorLoop.forIn]](#manual-Std___IteratorLoop___mk) lift γ Pl it init f
```

The implementation of `[IteratorLoop.forIn]](#manual-Std___IteratorLoop___mk)` in `i` is equal to the default implementation.

### 22.2.5. Universe Levels {#manual-The-Lean-Language-Reference--Iterators--Iterator-Definitions--Universe-Levels}

To make the [universe levels]](#manual---tech-term-level) of iterators more flexible, a wrapper type `[Shrink]](#manual-Std___Shrink)` is applied around the result of `[Iterator.step]](#manual-Std___Iterator___mk)`.
This type is presently a placeholder.
It is present to reduce the scope of the breaking change when the full implementation is available.

def

```lean
[Std.Shrink.{u}]](#manual-Std___Shrink) (α : Type u) : Type u



[Std.Shrink.{u}]](#manual-Std___Shrink) (α : Type u) : Type u
```

Currently, `Shrink α` is just a wrapper around `α`.

In the future, `Shrink` should allow shrinking `α` into a potentially smaller universe,
given a proof that `α` is actually small, just like Mathlib's `Shrink`, except that
the latter's conversion functions are noncomputable. Until then, `Shrink α` is always in the
same universe as `α`.

This no-op type exists so that fewer breaking changes will be needed when the
real `Shrink` type is available and the iterators will be made more flexible with regard to
universes.

The conversion functions `Shrink.deflate` and
`Shrink.inflate` form an equivalence between
`α` and `Shrink α`, but this equivalence is intentionally not definitional.

def

```lean
[Std.Shrink.inflate.{u_1}]](#manual-Std___Shrink___inflate) {α : Type u_1} (x : [Std.Shrink]](#manual-Std___Shrink) α) : α



[Std.Shrink.inflate.{u_1}]](#manual-Std___Shrink___inflate) {α : Type u_1}
  (x : [Std.Shrink]](#manual-Std___Shrink) α) : α
```

Converts elements of `Shrink α` into elements of `α`.

def

```lean
[Std.Shrink.deflate.{u_1}]](#manual-Std___Shrink___deflate) {α : Type u_1} (x : α) : [Std.Shrink]](#manual-Std___Shrink) α



[Std.Shrink.deflate.{u_1}]](#manual-Std___Shrink___deflate) {α : Type u_1}
  (x : α) : [Std.Shrink]](#manual-Std___Shrink) α
```

Converts elements of `α` into elements of `Shrink α`.

### 22.2.6. Basic Iterators {#manual-The-Lean-Language-Reference--Iterators--Iterator-Definitions--Basic-Iterators}

In addition to the iterators provided by collection types, there are two basic iterators that are not connected to any underlying data structure.
`[Iter.empty]](#manual-Std___Iter___empty)` finishes iteration immediately after yielding no data, and `[Iter.repeat]](#manual-Std___Iter___repeat)` yields the same element forever.
These iterators are primarily useful as parts of larger iterators built with combinators.

def

```lean
[Std.Iter.empty.{w}]](#manual-Std___Iter___empty) (β : Type w) : [Iter]](#manual-Std___Iter___mk) β



[Std.Iter.empty.{w}]](#manual-Std___Iter___empty) (β : Type w) : [Iter]](#manual-Std___Iter___mk) β
```

Returns an iterator that terminates immediately.

**Termination properties:**

- `[Finite]](#manual-Std___Iterators___Finite___mk)` instance: always
- `[Productive]](#manual-Std___Iterators___Productive___mk)` instance: always

def

```lean
[Std.IterM.empty.{w, w'}]](#manual-Std___IterM___empty) (m : Type w → Type w') (β : Type w) : [IterM]](#manual-Std___IterM___mk) m β



[Std.IterM.empty.{w, w'}]](#manual-Std___IterM___empty)
  (m : Type w → Type w') (β : Type w) :
  [IterM]](#manual-Std___IterM___mk) m β
```

Returns an iterator that terminates immediately.

**Termination properties:**

- `[Finite]](#manual-Std___Iterators___Finite___mk)` instance: always
- `[Productive]](#manual-Std___Iterators___Productive___mk)` instance: always

def

```lean
[Std.Iter.repeat.{w}]](#manual-Std___Iter___repeat) {α : Type w} (f : α → α) (init : α) : [Iter]](#manual-Std___Iter___mk) α



[Std.Iter.repeat.{w}]](#manual-Std___Iter___repeat) {α : Type w}
  (f : α → α) (init : α) : [Iter]](#manual-Std___Iter___mk) α
```

Creates an infinite iterator from an initial value `init` and a function `f : α → α`.
First it yields `init`, and in each successive step, the iterator applies `f` to the previous value.
So if the iterator just emitted `a`, in the next step it will yield `f a`. In other words, the
`n`-th value is `[Nat.repeat]](#manual-Nat___repeat) f n init`.

For example, if `f := (· + 1)` and `init := 0`, then the iterator emits all natural numbers in
order.

**Termination properties:**

- `[Finite]](#manual-Std___Iterators___Finite___mk)` instance: not available and never possible
- `[Productive]](#manual-Std___Iterators___Productive___mk)` instance: always

---



## Iterators — 22.3. Consuming Iterators {#manual-iterators-223-consuming-iterators}

> 📄 Source: https://lean-lang.org/doc/reference/latest/Iterators/Consuming-Iterators/

There are three primary ways to consume an iterator:

Converting it to a sequential data structure
:   The functions `[Iter.toList]](#manual-Std___Iter___toList)`, `[Iter.toArray]](#manual-Std___Iter___toArray)`, and their monadic equivalents `[IterM.toList]](#manual-Std___IterM___toList)` and `[IterM.toArray]](#manual-Std___IterM___toArray)`, construct a lists or arrays that contain the values from the iterator, in order.
    Only [finite iterators]](#manual---tech-term-Finite) can be converted to sequential data structures.

`for` loops
:   A `for` loop can consume an iterator, making each value available in its body.
    This requires that the iterator have an instance of `[IteratorLoop]](#manual-Std___IteratorLoop___mk)` for the loop's monad.

Stepping through iterators
:   Iterators can provide their values one-by-one, with client code explicitly requesting each new value in turn.
    When stepped through, iterators perform only enough computation to yield the requested value.

**Example: Converting Iterators to Lists**

In `[countdown]](#manual-countdown-_LPAR_in-Converting-Iterators-to-Lists_RPAR_)`, an iterator over a range is transformed into an iterator over strings using `[Iter.map](https://lean-lang.org/doc/reference/latest/Iterators/Iterator-Combinators/#Std___Iter___map)`.
This call to `[Iter.map](https://lean-lang.org/doc/reference/latest/Iterators/Iterator-Combinators/#Std___Iter___map)` does not result in any iteration over the range until `[Iter.toList]](#manual-Std___Iter___toList)` is called, at which point each element of the range is produced and transformed into a string.

```lean
def countdown : [String]](#manual-String___ofByteArray) :=
let steps : [Iter]](#manual-Std___Iter___mk) [String]](#manual-String___ofByteArray) := (0...10).[iter]](#manual-Std___Rco___iter).[map](https://lean-lang.org/doc/reference/latest/Iterators/Iterator-Combinators/#Std___Iter___map) (s!"{10 - ·}!\n")
[String.join]](#manual-String___join) steps.[toList]](#manual-Std___Iter___toList)
[#eval]](#manual-Lean___Parser___Command___eval) [IO.println]](#manual-IO___println) [countdown]](#manual-countdown-_LPAR_in-Converting-Iterators-to-Lists_RPAR_)
```

```lean
10!
9!
8!
7!
6!
5!
4!
3!
2!
1!
```

**Example: Converting Infinite Iterators to Lists**

Attempting to construct a list of all the natural numbers from an iterator will produce an endless loop:

```lean
def allNats : [List]](#manual-List___nil) [Nat]](#manual-Nat___zero) :=
let steps : [Iter]](#manual-Std___Iter___mk) [Nat]](#manual-Nat___zero) := (0...*).[iter]](#manual-Std___Rci___iter)
steps.[toList]](#manual-Std___Iter___toList)
```

The combinator `[Iter.ensureTermination]](#manual-Std___Iter___ensureTermination)` results in an iterator where non-termination is ruled out.
These iterators are guaranteed to terminate after finitely many steps, and thus cannot be used when Lean cannot prove the iterator finite.

```lean
def allNats : [List]](#manual-List___nil) [Nat]](#manual-Nat___zero) :=
let steps := (0...*).[iter]](#manual-Std___Rci___iter).[ensureTermination]](#manual-Std___Iter___ensureTermination)
steps.toList
```

The resulting error message states that there is no `[Finite]](#manual-Std___Iterators___Finite___mk)` instance:

```lean
failed to synthesize instance of type class
  [Finite]](#manual-Std___Iterators___Finite___mk) (Rxi.Iterator [Nat]](#manual-Nat___zero)) [Id]](#manual-Id)

Hint: Type class instance resolution failures can be inspected with the `set_option trace.Meta.synthInstance true` command.
```

**Example: Consuming Iterators in Loops**

This program creates an iterator of strings from a range, and then consumes the strings in a `for` loop:

```lean
def countdown (n : [Nat]](#manual-Nat___zero)) : [IO]](#manual-IO) [Unit]](#manual-Unit) := [do]](#manual-Lean___Parser___Term___do)
let steps : [Iter]](#manual-Std___Iter___mk) [String]](#manual-String___ofByteArray) := (0...n).[iter]](#manual-Std___Rco___iter).[map](https://lean-lang.org/doc/reference/latest/Iterators/Iterator-Combinators/#Std___Iter___map) (s!"{n - ·}!")
for i in steps do
[IO.println]](#manual-IO___println) i
[IO.println]](#manual-IO___println) "Blastoff!"
[#eval]](#manual-Lean___Parser___Command___eval) [countdown]](#manual-countdown-_LPAR_in-Consuming-Iterators-in-Loops_RPAR_) 5
```

```lean
5!
4!
3!
2!
1!
Blastoff!
```

**Example: Consuming Iterators Directly**

The function `[countdown]](#manual-countdown-_LPAR_in-Consuming-Iterators-Directly_RPAR_)` calls the range iterator's `[step]](#manual-Std___Iter___step)` function directly, handling each of the three possible cases.

```lean
def countdown (n : [Nat]](#manual-Nat___zero)) : [IO]](#manual-IO) [Unit]](#manual-Unit) := [do]](#manual-Lean___Parser___Term___do)
let steps : [Iter]](#manual-Std___Iter___mk) [Nat]](#manual-Nat___zero) := (0...n).[iter]](#manual-Std___Rco___iter)
[go]](#manual-countdown___go-_LPAR_in-Consuming-Iterators-Directly_RPAR_) steps
where
go iter := [do]](#manual-Lean___Parser___Term___do)
match iter.[step]](#manual-Std___Iter___step) with
| [.done]](#manual-Std___PlausibleIterStep___done) _ => [pure]](#manual-Pure___mk) ()
| [.skip]](#manual-Std___PlausibleIterStep___skip) iter' _ => [go]](#manual-countdown___go-_LPAR_in-Consuming-Iterators-Directly_RPAR_) iter'
| [.yield]](#manual-Std___PlausibleIterStep___yield) iter' i _ => do
[IO.println]](#manual-IO___println) s!"{i}!"
if i == 2 then
[IO.println]](#manual-IO___println) s!"Almost there..."
[go]](#manual-countdown___go-_LPAR_in-Consuming-Iterators-Directly_RPAR_) iter'
termination_by iter.[finitelyManySteps]](#manual-Std___Iter___finitelyManySteps)
```

### 22.3.1. Stepping Iterators {#manual-The-Lean-Language-Reference--Iterators--Consuming-Iterators--Stepping-Iterators}

Iterators are manually stepped using `[Iter.step]](#manual-Std___Iter___step)` or `[IterM.step]](#manual-Std___IterM___step)`.

def

```lean
[Std.Iter.step.{w}]](#manual-Std___Iter___step) {α β : Type w} [[Iterator]](#manual-Std___Iterator___mk) α [Id]](#manual-Id) β] (it : [Iter]](#manual-Std___Iter___mk) β) :
  it.[Step]](#manual-Std___Iter___Step)



[Std.Iter.step.{w}]](#manual-Std___Iter___step) {α β : Type w}
  [[Iterator]](#manual-Std___Iterator___mk) α [Id]](#manual-Id) β] (it : [Iter]](#manual-Std___Iter___mk) β) :
  it.[Step]](#manual-Std___Iter___Step)
```

Makes a single step with the given iterator `it`, potentially emitting a value and providing a
succeeding iterator. If this function is used recursively, termination can sometimes be proved with
the termination measures `it.[finitelyManySteps]](#manual-Std___Iter___finitelyManySteps)` and `it.[finitelyManySkips]](#manual-Std___Iter___finitelyManySkips)`.

def

```lean
[Std.IterM.step.{w, w'}]](#manual-Std___IterM___step) {α : Type w} {m : Type w → Type w'} {β : Type w}
  [[Iterator]](#manual-Std___Iterator___mk) α m β] (it : [IterM]](#manual-Std___IterM___mk) m β) : m ([Std.Shrink]](#manual-Std___Shrink) it.[Step]](#manual-Std___IterM___Step))



[Std.IterM.step.{w, w'}]](#manual-Std___IterM___step) {α : Type w}
  {m : Type w → Type w'} {β : Type w}
  [[Iterator]](#manual-Std___Iterator___mk) α m β] (it : [IterM]](#manual-Std___IterM___mk) m β) :
  m ([Std.Shrink]](#manual-Std___Shrink) it.[Step]](#manual-Std___IterM___Step))
```

Makes a single step with the given iterator `it`, potentially emitting a value and providing a
succeeding iterator. If this function is used recursively, termination can sometimes be proved with
the termination measures `it.[finitelyManySteps]](#manual-Std___IterM___finitelyManySteps)` and `it.[finitelyManySkips]](#manual-Std___IterM___finitelyManySkips)`.

#### 22.3.1.1. Termination {#manual-The-Lean-Language-Reference--Iterators--Consuming-Iterators--Stepping-Iterators--Termination}

When manually stepping an finite iterator, the termination measures `[finitelyManySteps]](#manual-Std___Iter___finitelyManySteps)` and `[finitelyManySkips]](#manual-Std___Iter___finitelyManySkips)` can be used to express that each step brings iteration closer to the end.
The proof automation for [well-founded recursion]](#manual-well-founded-recursion) is pre-configured to prove that recursive calls after steps reduce these measures.

**Example: Finitely Many Skips**

This function returns the first element of an iterator, if there is one, or `[none]](#manual-Option___none)` otherwise.
Because the iterator must be productive, it is guaranteed to return an element after at most a finite number of `[skip]](#manual-Std___PlausibleIterStep___skip)`s.
This function terminates even for infinite iterators.

```lean
def getFirst {α β} [[Iterator]](#manual-Std___Iterator___mk) α [Id]](#manual-Id) β] [[Productive]](#manual-Std___Iterators___Productive___mk) α [Id]](#manual-Id)]
(it : @[Iter]](#manual-Std___Iter___mk) α β) : [Option]](#manual-Option___none) β :=
[match]](#manual-Lean___Parser___Term___match) it.[step]](#manual-Std___Iter___step) [with]](#manual-Lean___Parser___Term___match)
| [.done]](#manual-Std___PlausibleIterStep___done) .. => [none]](#manual-Option___none)
| [.skip]](#manual-Std___PlausibleIterStep___skip) it' .. => [getFirst]](#manual-getFirst-_LPAR_in-Finitely-Many-Skips_RPAR_) it'
| [.yield]](#manual-Std___PlausibleIterStep___yield) _ x .. => [pure]](#manual-Pure___mk) x
termination_by it.[finitelyManySkips]](#manual-Std___Iter___finitelyManySkips)
```

def

```lean
[Std.Iter.finitelyManySteps.{w}]](#manual-Std___Iter___finitelyManySteps) {α β : Type w} [[Iterator]](#manual-Std___Iterator___mk) α [Id]](#manual-Id) β]
  [[Finite]](#manual-Std___Iterators___Finite___mk) α [Id]](#manual-Id)] (it : [Iter]](#manual-Std___Iter___mk) β) : [IterM.TerminationMeasures.Finite]](#manual-Std___IterM___TerminationMeasures___Finite___mk) α [Id]](#manual-Id)



[Std.Iter.finitelyManySteps.{w}]](#manual-Std___Iter___finitelyManySteps)
  {α β : Type w} [[Iterator]](#manual-Std___Iterator___mk) α [Id]](#manual-Id) β]
  [[Finite]](#manual-Std___Iterators___Finite___mk) α [Id]](#manual-Id)] (it : [Iter]](#manual-Std___Iter___mk) β) :
  [IterM.TerminationMeasures.Finite]](#manual-Std___IterM___TerminationMeasures___Finite___mk) α [Id]](#manual-Id)
```

Termination measure to be used in well-founded recursive functions recursing over a finite iterator
(see also `[Finite]](#manual-Std___Iterators___Finite___mk)`).

def

```lean
[Std.IterM.finitelyManySteps.{w, w'}]](#manual-Std___IterM___finitelyManySteps) {α : Type w} {m : Type w → Type w'}
  {β : Type w} [[Iterator]](#manual-Std___Iterator___mk) α m β] [[Finite]](#manual-Std___Iterators___Finite___mk) α m] (it : [IterM]](#manual-Std___IterM___mk) m β) :
  [IterM.TerminationMeasures.Finite]](#manual-Std___IterM___TerminationMeasures___Finite___mk) α m



[Std.IterM.finitelyManySteps.{w, w'}]](#manual-Std___IterM___finitelyManySteps)
  {α : Type w} {m : Type w → Type w'}
  {β : Type w} [[Iterator]](#manual-Std___Iterator___mk) α m β]
  [[Finite]](#manual-Std___Iterators___Finite___mk) α m] (it : [IterM]](#manual-Std___IterM___mk) m β) :
  [IterM.TerminationMeasures.Finite]](#manual-Std___IterM___TerminationMeasures___Finite___mk) α m
```

Termination measure to be used in well-founded recursive functions recursing over a finite iterator
(see also `[Finite]](#manual-Std___Iterators___Finite___mk)`).

structure

```lean
[Std.IterM.TerminationMeasures.Finite.{w, w'}]](#manual-Std___IterM___TerminationMeasures___Finite___mk) (α : Type w)
  (m : Type w → Type w') {β : Type w} [[Iterator]](#manual-Std___Iterator___mk) α m β] : Type w



[Std.IterM.TerminationMeasures.Finite.{w,
    w'}]](#manual-Std___IterM___TerminationMeasures___Finite___mk)
  (α : Type w) (m : Type w → Type w')
  {β : Type w} [[Iterator]](#manual-Std___Iterator___mk) α m β] : Type w
```

This type is a wrapper around `[IterM]](#manual-Std___IterM___mk)` so that it becomes a useful termination measure for
recursion over finite iterators. See also `[IterM.finitelyManySteps]](#manual-Std___IterM___finitelyManySteps)` and `[Iter.finitelyManySteps]](#manual-Std___Iter___finitelyManySteps)`.

Constructor

```lean
[Std.IterM.TerminationMeasures.Finite.mk]](#manual-Std___IterM___TerminationMeasures___Finite___mk).{w, w'}
```

Fields

```lean
it : [IterM]](#manual-Std___IterM___mk) m β
```

The wrapped iterator.

In the wrapper, its finiteness is used as a termination measure.

def

```lean
[Std.Iter.finitelyManySkips.{w}]](#manual-Std___Iter___finitelyManySkips) {α β : Type w} [[Iterator]](#manual-Std___Iterator___mk) α [Id]](#manual-Id) β]
  [[Productive]](#manual-Std___Iterators___Productive___mk) α [Id]](#manual-Id)] (it : [Iter]](#manual-Std___Iter___mk) β) :
  [IterM.TerminationMeasures.Productive]](#manual-Std___IterM___TerminationMeasures___Productive___mk) α [Id]](#manual-Id)



[Std.Iter.finitelyManySkips.{w}]](#manual-Std___Iter___finitelyManySkips)
  {α β : Type w} [[Iterator]](#manual-Std___Iterator___mk) α [Id]](#manual-Id) β]
  [[Productive]](#manual-Std___Iterators___Productive___mk) α [Id]](#manual-Id)] (it : [Iter]](#manual-Std___Iter___mk) β) :
  [IterM.TerminationMeasures.Productive]](#manual-Std___IterM___TerminationMeasures___Productive___mk) α
    [Id]](#manual-Id)
```

Termination measure to be used in well-founded recursive functions recursing over a productive
iterator (see also `[Productive]](#manual-Std___Iterators___Productive___mk)`).

def

```lean
[Std.IterM.finitelyManySkips.{w, w'}]](#manual-Std___IterM___finitelyManySkips) {α : Type w} {m : Type w → Type w'}
  {β : Type w} [[Iterator]](#manual-Std___Iterator___mk) α m β] [[Productive]](#manual-Std___Iterators___Productive___mk) α m] (it : [IterM]](#manual-Std___IterM___mk) m β) :
  [IterM.TerminationMeasures.Productive]](#manual-Std___IterM___TerminationMeasures___Productive___mk) α m



[Std.IterM.finitelyManySkips.{w, w'}]](#manual-Std___IterM___finitelyManySkips)
  {α : Type w} {m : Type w → Type w'}
  {β : Type w} [[Iterator]](#manual-Std___Iterator___mk) α m β]
  [[Productive]](#manual-Std___Iterators___Productive___mk) α m] (it : [IterM]](#manual-Std___IterM___mk) m β) :
  [IterM.TerminationMeasures.Productive]](#manual-Std___IterM___TerminationMeasures___Productive___mk) α m
```

Termination measure to be used in well-founded recursive functions recursing over a productive
iterator (see also `[Productive]](#manual-Std___Iterators___Productive___mk)`).

structure

```lean
[Std.IterM.TerminationMeasures.Productive.{w, w'}]](#manual-Std___IterM___TerminationMeasures___Productive___mk) (α : Type w)
  (m : Type w → Type w') {β : Type w} [[Iterator]](#manual-Std___Iterator___mk) α m β] : Type w



[Std.IterM.TerminationMeasures.Productive.{w,
    w'}]](#manual-Std___IterM___TerminationMeasures___Productive___mk)
  (α : Type w) (m : Type w → Type w')
  {β : Type w} [[Iterator]](#manual-Std___Iterator___mk) α m β] : Type w
```

This type is a wrapper around `[IterM]](#manual-Std___IterM___mk)` so that it becomes a useful termination measure for
recursion over productive iterators. See also `[IterM.finitelyManySkips]](#manual-Std___IterM___finitelyManySkips)` and `[Iter.finitelyManySkips]](#manual-Std___Iter___finitelyManySkips)`.

Constructor

```lean
[Std.IterM.TerminationMeasures.Productive.mk]](#manual-Std___IterM___TerminationMeasures___Productive___mk).{w, w'}
```

Fields

```lean
it : [IterM]](#manual-Std___IterM___mk) m β
```

The wrapped iterator.

In the wrapper, its productivity is used as a termination measure.

### 22.3.2. Consuming Pure Iterators {#manual-The-Lean-Language-Reference--Iterators--Consuming-Iterators--Consuming-Pure-Iterators}

def

```lean
[Std.Iter.fold.{w, x}]](#manual-Std___Iter___fold) {α β : Type w} {γ : Type x} [[Iterator]](#manual-Std___Iterator___mk) α [Id]](#manual-Id) β]
  [[IteratorLoop]](#manual-Std___IteratorLoop___mk) α [Id]](#manual-Id) [Id]](#manual-Id)] (f : γ → β → γ) (init : γ) (it : [Iter]](#manual-Std___Iter___mk) β) : γ



[Std.Iter.fold.{w, x}]](#manual-Std___Iter___fold) {α β : Type w}
  {γ : Type x} [[Iterator]](#manual-Std___Iterator___mk) α [Id]](#manual-Id) β]
  [[IteratorLoop]](#manual-Std___IteratorLoop___mk) α [Id]](#manual-Id) [Id]](#manual-Id)] (f : γ → β → γ)
  (init : γ) (it : [Iter]](#manual-Std___Iter___mk) β) : γ
```

Folds a function over an iterator from the left, accumulating a value starting with `init`.
The accumulated value is combined with the each element of the list in order, using `f`.

It is equivalent to `it.[toList]](#manual-Std___Iter___toList).[foldl]](#manual-List___foldl)`.

def

```lean
[Std.Iter.foldM.{x, x', w}]](#manual-Std___Iter___foldM) {m : Type x → Type x'} [[Monad]](#manual-Monad___mk) m]
  {α β : Type w} {γ : Type x} [[Iterator]](#manual-Std___Iterator___mk) α [Id]](#manual-Id) β] [[IteratorLoop]](#manual-Std___IteratorLoop___mk) α [Id]](#manual-Id) m]
  (f : γ → β → m γ) (init : γ) (it : [Iter]](#manual-Std___Iter___mk) β) : m γ



[Std.Iter.foldM.{x, x', w}]](#manual-Std___Iter___foldM)
  {m : Type x → Type x'} [[Monad]](#manual-Monad___mk) m]
  {α β : Type w} {γ : Type x}
  [[Iterator]](#manual-Std___Iterator___mk) α [Id]](#manual-Id) β] [[IteratorLoop]](#manual-Std___IteratorLoop___mk) α [Id]](#manual-Id) m]
  (f : γ → β → m γ) (init : γ)
  (it : [Iter]](#manual-Std___Iter___mk) β) : m γ
```

Folds a monadic function over an iterator from the left, accumulating a value starting with `init`.
The accumulated value is combined with the each element of the list in order, using `f`.

It is equivalent to `it.toList.foldlM`.

def

```lean
[Std.Iter.length.{w}]](#manual-Std___Iter___length) {α β : Type w} [[Iterator]](#manual-Std___Iterator___mk) α [Id]](#manual-Id) β]
  [[IteratorLoop]](#manual-Std___IteratorLoop___mk) α [Id]](#manual-Id) [Id]](#manual-Id)] (it : [Iter]](#manual-Std___Iter___mk) β) : [Nat]](#manual-Nat___zero)



[Std.Iter.length.{w}]](#manual-Std___Iter___length) {α β : Type w}
  [[Iterator]](#manual-Std___Iterator___mk) α [Id]](#manual-Id) β] [[IteratorLoop]](#manual-Std___IteratorLoop___mk) α [Id]](#manual-Id) [Id]](#manual-Id)]
  (it : [Iter]](#manual-Std___Iter___mk) β) : [Nat]](#manual-Nat___zero)
```

Steps through the whole iterator, counting the number of outputs emitted.

**Performance**:

This function's runtime is linear in the number of steps taken by the iterator.

def

```lean
[Std.Iter.any.{w}]](#manual-Std___Iter___any) {α β : Type w} [[Iterator]](#manual-Std___Iterator___mk) α [Id]](#manual-Id) β] [[IteratorLoop]](#manual-Std___IteratorLoop___mk) α [Id]](#manual-Id) [Id]](#manual-Id)]
  (p : β → [Bool]](#manual-Bool___false)) (it : [Iter]](#manual-Std___Iter___mk) β) : [Bool]](#manual-Bool___false)



[Std.Iter.any.{w}]](#manual-Std___Iter___any) {α β : Type w}
  [[Iterator]](#manual-Std___Iterator___mk) α [Id]](#manual-Id) β] [[IteratorLoop]](#manual-Std___IteratorLoop___mk) α [Id]](#manual-Id) [Id]](#manual-Id)]
  (p : β → [Bool]](#manual-Bool___false)) (it : [Iter]](#manual-Std___Iter___mk) β) : [Bool]](#manual-Bool___false)
```

Returns `[true]](#manual-Bool___false)` if the pure predicate `p` returns `[true]](#manual-Bool___false)` for
any element emitted by the iterator `it`.

`O(|xs|)`. Short-circuits upon encountering the first match. The elements in `it` are
examined in order of iteration.

def

```lean
[Std.Iter.anyM.{w, w'}]](#manual-Std___Iter___anyM) {α β : Type w} {m : Type → Type w'} [[Monad]](#manual-Monad___mk) m]
  [[Iterator]](#manual-Std___Iterator___mk) α [Id]](#manual-Id) β] [[IteratorLoop]](#manual-Std___IteratorLoop___mk) α [Id]](#manual-Id) m] (p : β → m [Bool]](#manual-Bool___false))
  (it : [Iter]](#manual-Std___Iter___mk) β) : m [Bool]](#manual-Bool___false)



[Std.Iter.anyM.{w, w'}]](#manual-Std___Iter___anyM) {α β : Type w}
  {m : Type → Type w'} [[Monad]](#manual-Monad___mk) m]
  [[Iterator]](#manual-Std___Iterator___mk) α [Id]](#manual-Id) β] [[IteratorLoop]](#manual-Std___IteratorLoop___mk) α [Id]](#manual-Id) m]
  (p : β → m [Bool]](#manual-Bool___false)) (it : [Iter]](#manual-Std___Iter___mk) β) : m [Bool]](#manual-Bool___false)
```

Returns `[true]](#manual-Bool___false)` if the monadic predicate `p` returns `[true]](#manual-Bool___false)` for
any element emitted by the iterator `it`.

`O(|xs|)`. Short-circuits upon encountering the first match. The elements in `it` are
examined in order of iteration.

def

```lean
[Std.Iter.all.{w}]](#manual-Std___Iter___all) {α β : Type w} [[Iterator]](#manual-Std___Iterator___mk) α [Id]](#manual-Id) β] [[IteratorLoop]](#manual-Std___IteratorLoop___mk) α [Id]](#manual-Id) [Id]](#manual-Id)]
  (p : β → [Bool]](#manual-Bool___false)) (it : [Iter]](#manual-Std___Iter___mk) β) : [Bool]](#manual-Bool___false)



[Std.Iter.all.{w}]](#manual-Std___Iter___all) {α β : Type w}
  [[Iterator]](#manual-Std___Iterator___mk) α [Id]](#manual-Id) β] [[IteratorLoop]](#manual-Std___IteratorLoop___mk) α [Id]](#manual-Id) [Id]](#manual-Id)]
  (p : β → [Bool]](#manual-Bool___false)) (it : [Iter]](#manual-Std___Iter___mk) β) : [Bool]](#manual-Bool___false)
```

Returns `[true]](#manual-Bool___false)` if the pure predicate `p` returns `[true]](#manual-Bool___false)` for
all element emitted by the iterator `it`.

`O(|xs|)`. Short-circuits upon encountering the first match. The elements in `it` are
examined in order of iteration.

def

```lean
[Std.Iter.allM.{w, w'}]](#manual-Std___Iter___allM) {α β : Type w} {m : Type → Type w'} [[Monad]](#manual-Monad___mk) m]
  [[Iterator]](#manual-Std___Iterator___mk) α [Id]](#manual-Id) β] [[IteratorLoop]](#manual-Std___IteratorLoop___mk) α [Id]](#manual-Id) m] (p : β → m [Bool]](#manual-Bool___false))
  (it : [Iter]](#manual-Std___Iter___mk) β) : m [Bool]](#manual-Bool___false)



[Std.Iter.allM.{w, w'}]](#manual-Std___Iter___allM) {α β : Type w}
  {m : Type → Type w'} [[Monad]](#manual-Monad___mk) m]
  [[Iterator]](#manual-Std___Iterator___mk) α [Id]](#manual-Id) β] [[IteratorLoop]](#manual-Std___IteratorLoop___mk) α [Id]](#manual-Id) m]
  (p : β → m [Bool]](#manual-Bool___false)) (it : [Iter]](#manual-Std___Iter___mk) β) : m [Bool]](#manual-Bool___false)
```

Returns `[true]](#manual-Bool___false)` if the monadic predicate `p` returns `[true]](#manual-Bool___false)` for
all element emitted by the iterator `it`.

`O(|xs|)`. Short-circuits upon encountering the first match. The elements in `it` are
examined in order of iteration.

def

```lean
[Std.Iter.find?.{w}]](#manual-Std___Iter___find___) {α β : Type w} [[Iterator]](#manual-Std___Iterator___mk) α [Id]](#manual-Id) β]
  [[IteratorLoop]](#manual-Std___IteratorLoop___mk) α [Id]](#manual-Id) [Id]](#manual-Id)] (it : [Iter]](#manual-Std___Iter___mk) β) (f : β → [Bool]](#manual-Bool___false)) : [Option]](#manual-Option___none) β



[Std.Iter.find?.{w}]](#manual-Std___Iter___find___) {α β : Type w}
  [[Iterator]](#manual-Std___Iterator___mk) α [Id]](#manual-Id) β] [[IteratorLoop]](#manual-Std___IteratorLoop___mk) α [Id]](#manual-Id) [Id]](#manual-Id)]
  (it : [Iter]](#manual-Std___Iter___mk) β) (f : β → [Bool]](#manual-Bool___false)) : [Option]](#manual-Option___none) β
```

Returns the first output of the iterator for which the predicate `p` returns `[true]](#manual-Bool___false)`, or `[none]](#manual-Option___none)` if
no such output is found.

`O(|it|)`. Short-circuits upon encountering the first match. The elements in `it` are examined in
order of iteration.

If the iterator is not finite, this function might run forever. The variant
`it.[ensureTermination]](#manual-Std___Iter___ensureTermination).find?` always terminates after finitely many steps.

Examples:

- `[7, 6, 5, 8, 1, 2, 6].[iter]](#manual-List___iter).[find?]](#manual-Std___Iter___find___) (· < 5) = [some]](#manual-Option___none) 1`
- `[7, 6, 5, 8, 1, 2, 6].[iter]](#manual-List___iter).[find?]](#manual-Std___Iter___find___) (· < 1) = [none]](#manual-Option___none)`

def

```lean
[Std.Iter.findM?.{w, w'}]](#manual-Std___Iter___findM___) {α β : Type w} {m : Type w → Type w'} [[Monad]](#manual-Monad___mk) m]
  [[Iterator]](#manual-Std___Iterator___mk) α [Id]](#manual-Id) β] [[IteratorLoop]](#manual-Std___IteratorLoop___mk) α [Id]](#manual-Id) m] (it : [Iter]](#manual-Std___Iter___mk) β)
  (f : β → m ([ULift]](#manual-ULift___up) [Bool]](#manual-Bool___false))) : m ([Option]](#manual-Option___none) β)



[Std.Iter.findM?.{w, w'}]](#manual-Std___Iter___findM___) {α β : Type w}
  {m : Type w → Type w'} [[Monad]](#manual-Monad___mk) m]
  [[Iterator]](#manual-Std___Iterator___mk) α [Id]](#manual-Id) β] [[IteratorLoop]](#manual-Std___IteratorLoop___mk) α [Id]](#manual-Id) m]
  (it : [Iter]](#manual-Std___Iter___mk) β) (f : β → m ([ULift]](#manual-ULift___up) [Bool]](#manual-Bool___false))) :
  m ([Option]](#manual-Option___none) β)
```

Returns the first output of the iterator for which the monadic predicate `p` returns `[true]](#manual-Bool___false)`, or
`[none]](#manual-Option___none)` if no such element is found.

`O(|it|)`. Short-circuits when `f` returns `[true]](#manual-Bool___false)`. The outputs of `it` are examined in order of
iteration.

If the iterator is not finite, this function might run forever. The variant
`it.[ensureTermination]](#manual-Std___Iter___ensureTermination).findM?` always terminates after finitely many steps.

Example:

```
#eval [7, 6, 5, 8, 1, 2, 6].iter.findM? fun i => do
  if i < 5 then
    return true
  if i ≤ 6 then
    IO.println s!"Almost! {i}"
  return false
```

```lean
Almost! 6
Almost! 5
```

```lean
[some]](#manual-Option___none) 1
```

def

```lean
[Std.Iter.findSome?.{w, x}]](#manual-Std___Iter___findSome___) {α β : Type w} {γ : Type x} [[Iterator]](#manual-Std___Iterator___mk) α [Id]](#manual-Id) β]
  [[IteratorLoop]](#manual-Std___IteratorLoop___mk) α [Id]](#manual-Id) [Id]](#manual-Id)] (it : [Iter]](#manual-Std___Iter___mk) β) (f : β → [Option]](#manual-Option___none) γ) : [Option]](#manual-Option___none) γ



[Std.Iter.findSome?.{w, x}]](#manual-Std___Iter___findSome___) {α β : Type w}
  {γ : Type x} [[Iterator]](#manual-Std___Iterator___mk) α [Id]](#manual-Id) β]
  [[IteratorLoop]](#manual-Std___IteratorLoop___mk) α [Id]](#manual-Id) [Id]](#manual-Id)] (it : [Iter]](#manual-Std___Iter___mk) β)
  (f : β → [Option]](#manual-Option___none) γ) : [Option]](#manual-Option___none) γ
```

Returns the first non-`[none]](#manual-Option___none)` result of applying `f` to each output of the iterator, in order.
Returns `[none]](#manual-Option___none)` if `f` returns `[none]](#manual-Option___none)` for all outputs.

`O(|it|)`. Short-circuits when `f` returns `[some]](#manual-Option___none) _`.The outputs of `it` are examined in order of
iteration.

If the iterator is not finite, this function might run forever. The variant
`it.[ensureTermination]](#manual-Std___Iter___ensureTermination).findSome?` always terminates after finitely many steps.

Examples:

- `[7, 6, 5, 8, 1, 2, 6].[iter]](#manual-List___iter).[findSome?]](#manual-Std___Iter___findSome___) (fun x => [if]](#manual-termIfThenElse) x < 5 [then]](#manual-termIfThenElse) [some]](#manual-Option___none) (10 * x) [else]](#manual-termIfThenElse) [none]](#manual-Option___none)) = [some]](#manual-Option___none) 10`
- `[7, 6, 5, 8, 1, 2, 6].[iter]](#manual-List___iter).[findSome?]](#manual-Std___Iter___findSome___) (fun x => [if]](#manual-termIfThenElse) x < 1 [then]](#manual-termIfThenElse) [some]](#manual-Option___none) (10 * x) [else]](#manual-termIfThenElse) [none]](#manual-Option___none)) = [none]](#manual-Option___none)`

def

```lean
[Std.Iter.findSomeM?.{w, x, w'}]](#manual-Std___Iter___findSomeM___) {α β : Type w} {γ : Type x}
  {m : Type x → Type w'} [[Monad]](#manual-Monad___mk) m] [[Iterator]](#manual-Std___Iterator___mk) α [Id]](#manual-Id) β]
  [[IteratorLoop]](#manual-Std___IteratorLoop___mk) α [Id]](#manual-Id) m] (it : [Iter]](#manual-Std___Iter___mk) β) (f : β → m ([Option]](#manual-Option___none) γ)) :
  m ([Option]](#manual-Option___none) γ)



[Std.Iter.findSomeM?.{w, x, w'}]](#manual-Std___Iter___findSomeM___)
  {α β : Type w} {γ : Type x}
  {m : Type x → Type w'} [[Monad]](#manual-Monad___mk) m]
  [[Iterator]](#manual-Std___Iterator___mk) α [Id]](#manual-Id) β] [[IteratorLoop]](#manual-Std___IteratorLoop___mk) α [Id]](#manual-Id) m]
  (it : [Iter]](#manual-Std___Iter___mk) β) (f : β → m ([Option]](#manual-Option___none) γ)) :
  m ([Option]](#manual-Option___none) γ)
```

Returns the first non-`[none]](#manual-Option___none)` result of applying the monadic function `f` to each output
of the iterator, in order. Returns `[none]](#manual-Option___none)` if `f` returns `[none]](#manual-Option___none)` for all outputs.

`O(|it|)`. Short-circuits when `f` returns `[some]](#manual-Option___none) _`. The outputs of `it` are
examined in order of iteration.

If the iterator is not finite, this function might run forever. The variant
`it.[ensureTermination]](#manual-Std___Iter___ensureTermination).findSomeM?` always terminates after finitely many steps.

Example:

```lean
[#eval]](#manual-Lean___Parser___Command___eval) [7, 6, 5, 8, 1, 2, 6].[iter]](#manual-List___iter).[findSomeM?]](#manual-Std___Iter___findSomeM___) fun i => [do]](#manual-Lean___Parser___Term___do)
if i < 5 then
return [some]](#manual-Option___none) (i * 10)
if i ≤ 6 then
[IO.println]](#manual-IO___println) s!"Almost! {i}"
return [none]](#manual-Option___none)
```

```lean
Almost! 6
Almost! 5
```

```lean
[some]](#manual-Option___none) 10
```

def

```lean
[Std.Iter.atIdx?.{u_1}]](#manual-Std___Iter___atIdx___) {α β : Type u_1} [[Iterator]](#manual-Std___Iterator___mk) α [Id]](#manual-Id) β]
  [[IteratorAccess]](#manual-Std___IteratorAccess___mk) α [Id]](#manual-Id)] (n : [Nat]](#manual-Nat___zero)) (it : [Iter]](#manual-Std___Iter___mk) β) : [Option]](#manual-Option___none) β



[Std.Iter.atIdx?.{u_1}]](#manual-Std___Iter___atIdx___) {α β : Type u_1}
  [[Iterator]](#manual-Std___Iterator___mk) α [Id]](#manual-Id) β] [[IteratorAccess]](#manual-Std___IteratorAccess___mk) α [Id]](#manual-Id)]
  (n : [Nat]](#manual-Nat___zero)) (it : [Iter]](#manual-Std___Iter___mk) β) : [Option]](#manual-Option___none) β
```

Returns the `n`-th value emitted by `it`, or `[none]](#manual-Option___none)` if `it` terminates earlier.

For monadic iterators, the monadic effects of this operation may differ from manually iterating
to the `n`-th value because `atIdx?` can take shortcuts. By the signature, the return value
is guaranteed to plausible in the sense of `IterM.IsPlausibleNthOutputStep`.

This function is only available for iterators that explicitly support it by implementing
the `[IteratorAccess]](#manual-Std___IteratorAccess___mk)` typeclass.

def

```lean
[Std.Iter.atIdxSlow?.{u_1}]](#manual-Std___Iter___atIdxSlow___) {α β : Type u_1} [[Iterator]](#manual-Std___Iterator___mk) α [Id]](#manual-Id) β] (n : [Nat]](#manual-Nat___zero))
  (it : [Iter]](#manual-Std___Iter___mk) β) : [Option]](#manual-Option___none) β



[Std.Iter.atIdxSlow?.{u_1}]](#manual-Std___Iter___atIdxSlow___) {α β : Type u_1}
  [[Iterator]](#manual-Std___Iterator___mk) α [Id]](#manual-Id) β] (n : [Nat]](#manual-Nat___zero))
  (it : [Iter]](#manual-Std___Iter___mk) β) : [Option]](#manual-Option___none) β
```

If possible, takes `n` steps with the iterator `it` and
returns the `n`-th emitted value, or `[none]](#manual-Option___none)` if `it` finished
before emitting `n` values.

If the iterator is not productive, this function might run forever in an endless loop of iterator
steps. The variant `it.[ensureTermination]](#manual-Std___Iter___ensureTermination).atIdxSlow?` is guaranteed to terminate after finitely many
steps.

### 22.3.3. Consuming Monadic Iterators {#manual-The-Lean-Language-Reference--Iterators--Consuming-Iterators--Consuming-Monadic-Iterators}

def

```lean
[Std.IterM.drain.{w, w'}]](#manual-Std___IterM___drain) {α : Type w} {m : Type w → Type w'} [[Monad]](#manual-Monad___mk) m]
  {β : Type w} [[Iterator]](#manual-Std___Iterator___mk) α m β] (it : [IterM]](#manual-Std___IterM___mk) m β) [[IteratorLoop]](#manual-Std___IteratorLoop___mk) α m m] :
  m [PUnit]](#manual-PUnit___unit)



[Std.IterM.drain.{w, w'}]](#manual-Std___IterM___drain) {α : Type w}
  {m : Type w → Type w'} [[Monad]](#manual-Monad___mk) m]
  {β : Type w} [[Iterator]](#manual-Std___Iterator___mk) α m β]
  (it : [IterM]](#manual-Std___IterM___mk) m β) [[IteratorLoop]](#manual-Std___IteratorLoop___mk) α m m] :
  m [PUnit]](#manual-PUnit___unit)
```

Iterates over the whole iterator, applying the monadic effects of each step, discarding all
emitted values.

def

```lean
[Std.IterM.fold.{w, w'}]](#manual-Std___IterM___fold) {m : Type w → Type w'} {α β γ : Type w} [[Monad]](#manual-Monad___mk) m]
  [[Iterator]](#manual-Std___Iterator___mk) α m β] [[IteratorLoop]](#manual-Std___IteratorLoop___mk) α m m] (f : γ → β → γ) (init : γ)
  (it : [IterM]](#manual-Std___IterM___mk) m β) : m γ



[Std.IterM.fold.{w, w'}]](#manual-Std___IterM___fold)
  {m : Type w → Type w'} {α β γ : Type w}
  [[Monad]](#manual-Monad___mk) m] [[Iterator]](#manual-Std___Iterator___mk) α m β]
  [[IteratorLoop]](#manual-Std___IteratorLoop___mk) α m m] (f : γ → β → γ)
  (init : γ) (it : [IterM]](#manual-Std___IterM___mk) m β) : m γ
```

Folds a function over an iterator from the left, accumulating a value starting with `init`.
The accumulated value is combined with the each element of the list in order, using `f`.

It is equivalent to `it.[toList]](#manual-Std___IterM___toList).foldl`.

def

```lean
[Std.IterM.foldM.{w, w', w''}]](#manual-Std___IterM___foldM) {m : Type w → Type w'}
  {n : Type w → Type w''} [[Monad]](#manual-Monad___mk) n] {α β γ : Type w} [[Iterator]](#manual-Std___Iterator___mk) α m β]
  [[IteratorLoop]](#manual-Std___IteratorLoop___mk) α m n] [[MonadLiftT]](#manual-MonadLiftT___mk) m n] (f : γ → β → n γ) (init : γ)
  (it : [IterM]](#manual-Std___IterM___mk) m β) : n γ



[Std.IterM.foldM.{w, w', w''}]](#manual-Std___IterM___foldM)
  {m : Type w → Type w'}
  {n : Type w → Type w''} [[Monad]](#manual-Monad___mk) n]
  {α β γ : Type w} [[Iterator]](#manual-Std___Iterator___mk) α m β]
  [[IteratorLoop]](#manual-Std___IteratorLoop___mk) α m n] [[MonadLiftT]](#manual-MonadLiftT___mk) m n]
  (f : γ → β → n γ) (init : γ)
  (it : [IterM]](#manual-Std___IterM___mk) m β) : n γ
```

Folds a monadic function over an iterator from the left, accumulating a value starting with `init`.
The accumulated value is combined with the each element of the list in order, using `f`.

The monadic effects of `f` are interleaved with potential effects caused by the iterator's step
function. Therefore, it may *not* be equivalent to `(← it.toList).foldlM`.

def

```lean
[Std.IterM.length.{w, w'}]](#manual-Std___IterM___length) {α : Type w} {m : Type w → Type w'}
  {β : Type w} [[Iterator]](#manual-Std___Iterator___mk) α m β] [[IteratorLoop]](#manual-Std___IteratorLoop___mk) α m m] [[Monad]](#manual-Monad___mk) m]
  (it : [IterM]](#manual-Std___IterM___mk) m β) : m ([ULift]](#manual-ULift___up) [Nat]](#manual-Nat___zero))



[Std.IterM.length.{w, w'}]](#manual-Std___IterM___length) {α : Type w}
  {m : Type w → Type w'} {β : Type w}
  [[Iterator]](#manual-Std___Iterator___mk) α m β] [[IteratorLoop]](#manual-Std___IteratorLoop___mk) α m m]
  [[Monad]](#manual-Monad___mk) m] (it : [IterM]](#manual-Std___IterM___mk) m β) :
  m ([ULift]](#manual-ULift___up) [Nat]](#manual-Nat___zero))
```

Steps through the whole iterator, counting the number of outputs emitted.

**Performance**:

This function's runtime is linear in the number of steps taken by the iterator.

def

```lean
[Std.IterM.any.{w, w'}]](#manual-Std___IterM___any) {α β : Type w} {m : Type w → Type w'} [[Monad]](#manual-Monad___mk) m]
  [[Iterator]](#manual-Std___Iterator___mk) α m β] [[IteratorLoop]](#manual-Std___IteratorLoop___mk) α m m] (p : β → [Bool]](#manual-Bool___false))
  (it : [IterM]](#manual-Std___IterM___mk) m β) : m ([ULift]](#manual-ULift___up) [Bool]](#manual-Bool___false))



[Std.IterM.any.{w, w'}]](#manual-Std___IterM___any) {α β : Type w}
  {m : Type w → Type w'} [[Monad]](#manual-Monad___mk) m]
  [[Iterator]](#manual-Std___Iterator___mk) α m β] [[IteratorLoop]](#manual-Std___IteratorLoop___mk) α m m]
  (p : β → [Bool]](#manual-Bool___false)) (it : [IterM]](#manual-Std___IterM___mk) m β) :
  m ([ULift]](#manual-ULift___up) [Bool]](#manual-Bool___false))
```

Returns `[ULift.up]](#manual-ULift___up) [true]](#manual-Bool___false)` if the pure predicate `p` returns `[true]](#manual-Bool___false)` for
any element emitted by the iterator `it`.

`O(|it|)`. Short-circuits upon encountering the first match. The outputs of `it` are
examined in order of iteration.

def

```lean
[Std.IterM.anyM.{w, w'}]](#manual-Std___IterM___anyM) {α β : Type w} {m : Type w → Type w'} [[Monad]](#manual-Monad___mk) m]
  [[Iterator]](#manual-Std___Iterator___mk) α m β] [[IteratorLoop]](#manual-Std___IteratorLoop___mk) α m m] (p : β → m ([ULift]](#manual-ULift___up) [Bool]](#manual-Bool___false)))
  (it : [IterM]](#manual-Std___IterM___mk) m β) : m ([ULift]](#manual-ULift___up) [Bool]](#manual-Bool___false))



[Std.IterM.anyM.{w, w'}]](#manual-Std___IterM___anyM) {α β : Type w}
  {m : Type w → Type w'} [[Monad]](#manual-Monad___mk) m]
  [[Iterator]](#manual-Std___Iterator___mk) α m β] [[IteratorLoop]](#manual-Std___IteratorLoop___mk) α m m]
  (p : β → m ([ULift]](#manual-ULift___up) [Bool]](#manual-Bool___false)))
  (it : [IterM]](#manual-Std___IterM___mk) m β) : m ([ULift]](#manual-ULift___up) [Bool]](#manual-Bool___false))
```

Returns `[ULift.up]](#manual-ULift___up) [true]](#manual-Bool___false)` if the monadic predicate `p` returns `[ULift.up]](#manual-ULift___up) [true]](#manual-Bool___false)` for
any element emitted by the iterator `it`.

`O(|it|)`. Short-circuits upon encountering the first match. The outputs of `it` are
examined in order of iteration.

def

```lean
[Std.IterM.all.{w, w'}]](#manual-Std___IterM___all) {α β : Type w} {m : Type w → Type w'} [[Monad]](#manual-Monad___mk) m]
  [[Iterator]](#manual-Std___Iterator___mk) α m β] [[IteratorLoop]](#manual-Std___IteratorLoop___mk) α m m] (p : β → [Bool]](#manual-Bool___false))
  (it : [IterM]](#manual-Std___IterM___mk) m β) : m ([ULift]](#manual-ULift___up) [Bool]](#manual-Bool___false))



[Std.IterM.all.{w, w'}]](#manual-Std___IterM___all) {α β : Type w}
  {m : Type w → Type w'} [[Monad]](#manual-Monad___mk) m]
  [[Iterator]](#manual-Std___Iterator___mk) α m β] [[IteratorLoop]](#manual-Std___IteratorLoop___mk) α m m]
  (p : β → [Bool]](#manual-Bool___false)) (it : [IterM]](#manual-Std___IterM___mk) m β) :
  m ([ULift]](#manual-ULift___up) [Bool]](#manual-Bool___false))
```

Returns `[ULift.up]](#manual-ULift___up) [true]](#manual-Bool___false)` if the pure predicate `p` returns `[true]](#manual-Bool___false)` for
all elements emitted by the iterator `it`.

`O(|it|)`. Short-circuits upon encountering the first mismatch. The outputs of `it` are
examined in order of iteration.

If the iterator is not finite, this function might run forever. The variant
`it.[ensureTermination]](#manual-Std___IterM___ensureTermination).toListRev` always terminates after finitely many steps.

def

```lean
[Std.IterM.allM.{w, w'}]](#manual-Std___IterM___allM) {α β : Type w} {m : Type w → Type w'} [[Monad]](#manual-Monad___mk) m]
  [[Iterator]](#manual-Std___Iterator___mk) α m β] [[IteratorLoop]](#manual-Std___IteratorLoop___mk) α m m] (p : β → m ([ULift]](#manual-ULift___up) [Bool]](#manual-Bool___false)))
  (it : [IterM]](#manual-Std___IterM___mk) m β) : m ([ULift]](#manual-ULift___up) [Bool]](#manual-Bool___false))



[Std.IterM.allM.{w, w'}]](#manual-Std___IterM___allM) {α β : Type w}
  {m : Type w → Type w'} [[Monad]](#manual-Monad___mk) m]
  [[Iterator]](#manual-Std___Iterator___mk) α m β] [[IteratorLoop]](#manual-Std___IteratorLoop___mk) α m m]
  (p : β → m ([ULift]](#manual-ULift___up) [Bool]](#manual-Bool___false)))
  (it : [IterM]](#manual-Std___IterM___mk) m β) : m ([ULift]](#manual-ULift___up) [Bool]](#manual-Bool___false))
```

Returns `[ULift.up]](#manual-ULift___up) [true]](#manual-Bool___false)` if the monadic predicate `p` returns `[ULift.up]](#manual-ULift___up) [true]](#manual-Bool___false)` for
all elements emitted by the iterator `it`.

`O(|it|)`. Short-circuits upon encountering the first mismatch. The outputs of `it` are
examined in order of iteration.

def

```lean
[Std.IterM.find?.{w, w'}]](#manual-Std___IterM___find___) {α β : Type w} {m : Type w → Type w'} [[Monad]](#manual-Monad___mk) m]
  [[Iterator]](#manual-Std___Iterator___mk) α m β] [[IteratorLoop]](#manual-Std___IteratorLoop___mk) α m m] (it : [IterM]](#manual-Std___IterM___mk) m β)
  (f : β → [Bool]](#manual-Bool___false)) : m ([Option]](#manual-Option___none) β)



[Std.IterM.find?.{w, w'}]](#manual-Std___IterM___find___) {α β : Type w}
  {m : Type w → Type w'} [[Monad]](#manual-Monad___mk) m]
  [[Iterator]](#manual-Std___Iterator___mk) α m β] [[IteratorLoop]](#manual-Std___IteratorLoop___mk) α m m]
  (it : [IterM]](#manual-Std___IterM___mk) m β) (f : β → [Bool]](#manual-Bool___false)) :
  m ([Option]](#manual-Option___none) β)
```

Returns the first output of the iterator for which the predicate `p` returns `[true]](#manual-Bool___false)`, or `[none]](#manual-Option___none)` if
no such output is found.

`O(|it|)`. Short-circuits upon encountering the first match. The elements in `it` are examined in
order of iteration.

If the iterator is not finite, this function might run forever. The variant
`it.[ensureTermination]](#manual-Std___IterM___ensureTermination).find?` always terminates after finitely many steps.

Examples:

- `([7, 6, 5, 8, 1, 2, 6].[iterM]](#manual-List___iterM) [Id]](#manual-Id)).[find?]](#manual-Std___IterM___find___) (· < 5) = [pure]](#manual-Pure___mk) ([some]](#manual-Option___none) 1)`
- `([7, 6, 5, 8, 1, 2, 6].[iterM]](#manual-List___iterM) [Id]](#manual-Id)).[find?]](#manual-Std___IterM___find___) (· < 1) = [pure]](#manual-Pure___mk) [none]](#manual-Option___none)`

def

```lean
[Std.IterM.findM?.{w, w'}]](#manual-Std___IterM___findM___) {α β : Type w} {m : Type w → Type w'} [[Monad]](#manual-Monad___mk) m]
  [[Iterator]](#manual-Std___Iterator___mk) α m β] [[IteratorLoop]](#manual-Std___IteratorLoop___mk) α m m] (it : [IterM]](#manual-Std___IterM___mk) m β)
  (f : β → m ([ULift]](#manual-ULift___up) [Bool]](#manual-Bool___false))) : m ([Option]](#manual-Option___none) β)



[Std.IterM.findM?.{w, w'}]](#manual-Std___IterM___findM___) {α β : Type w}
  {m : Type w → Type w'} [[Monad]](#manual-Monad___mk) m]
  [[Iterator]](#manual-Std___Iterator___mk) α m β] [[IteratorLoop]](#manual-Std___IteratorLoop___mk) α m m]
  (it : [IterM]](#manual-Std___IterM___mk) m β)
  (f : β → m ([ULift]](#manual-ULift___up) [Bool]](#manual-Bool___false))) : m ([Option]](#manual-Option___none) β)
```

Returns the first output of the iterator for which the monadic predicate `p` returns `[true]](#manual-Bool___false)`, or
`[none]](#manual-Option___none)` if no such element is found.

`O(|it|)`. Short-circuits when `f` returns `[true]](#manual-Bool___false)`. The outputs of `it` are examined in order of
iteration.

If the iterator is not finite, this function might run forever. The variant
`it.[ensureTermination]](#manual-Std___IterM___ensureTermination).findM?` always terminates after finitely many steps.

Example:

```
#eval ([7, 6, 5, 8, 1, 2, 6].iterM IO).findM? fun i => do
  if i < 5 then
    return true
  if i ≤ 6 then
    IO.println s!"Almost! {i}"
  return false
```

```lean
Almost! 6
Almost! 5
```

```lean
[some]](#manual-Option___none) 1
```

def

```lean
[Std.IterM.findSome?.{w, w'}]](#manual-Std___IterM___findSome___) {α β γ : Type w} {m : Type w → Type w'}
  [[Monad]](#manual-Monad___mk) m] [[Iterator]](#manual-Std___Iterator___mk) α m β] [[IteratorLoop]](#manual-Std___IteratorLoop___mk) α m m] (it : [IterM]](#manual-Std___IterM___mk) m β)
  (f : β → [Option]](#manual-Option___none) γ) : m ([Option]](#manual-Option___none) γ)



[Std.IterM.findSome?.{w, w'}]](#manual-Std___IterM___findSome___)
  {α β γ : Type w} {m : Type w → Type w'}
  [[Monad]](#manual-Monad___mk) m] [[Iterator]](#manual-Std___Iterator___mk) α m β]
  [[IteratorLoop]](#manual-Std___IteratorLoop___mk) α m m] (it : [IterM]](#manual-Std___IterM___mk) m β)
  (f : β → [Option]](#manual-Option___none) γ) : m ([Option]](#manual-Option___none) γ)
```

Returns the first non-`[none]](#manual-Option___none)` result of applying `f` to each output of the iterator, in order.
Returns `[none]](#manual-Option___none)` if `f` returns `[none]](#manual-Option___none)` for all outputs.

`O(|it|)`. Short-circuits when `f` returns `[some]](#manual-Option___none) _`.The outputs of `it` are examined in order of
iteration.

If the iterator is not finite, this function might run forever. The variant
`it.[ensureTermination]](#manual-Std___IterM___ensureTermination).findSome?` always terminates after finitely many steps.

Examples:

- `([7, 6, 5, 8, 1, 2, 6].[iterM]](#manual-List___iterM) [Id]](#manual-Id)).[findSome?]](#manual-Std___IterM___findSome___) (fun x => [if]](#manual-termIfThenElse) x < 5 [then]](#manual-termIfThenElse) [some]](#manual-Option___none) (10 * x) [else]](#manual-termIfThenElse) [none]](#manual-Option___none)) = [pure]](#manual-Pure___mk) ([some]](#manual-Option___none) 10)`
- `([7, 6, 5, 8, 1, 2, 6].[iterM]](#manual-List___iterM) [Id]](#manual-Id)).[findSome?]](#manual-Std___IterM___findSome___) (fun x => [if]](#manual-termIfThenElse) x < 1 [then]](#manual-termIfThenElse) [some]](#manual-Option___none) (10 * x) [else]](#manual-termIfThenElse) [none]](#manual-Option___none)) = [pure]](#manual-Pure___mk) [none]](#manual-Option___none)`

def

```lean
[Std.IterM.findSomeM?.{w, w'}]](#manual-Std___IterM___findSomeM___) {α β γ : Type w} {m : Type w → Type w'}
  [[Monad]](#manual-Monad___mk) m] [[Iterator]](#manual-Std___Iterator___mk) α m β] [[IteratorLoop]](#manual-Std___IteratorLoop___mk) α m m] (it : [IterM]](#manual-Std___IterM___mk) m β)
  (f : β → m ([Option]](#manual-Option___none) γ)) : m ([Option]](#manual-Option___none) γ)



[Std.IterM.findSomeM?.{w, w'}]](#manual-Std___IterM___findSomeM___)
  {α β γ : Type w} {m : Type w → Type w'}
  [[Monad]](#manual-Monad___mk) m] [[Iterator]](#manual-Std___Iterator___mk) α m β]
  [[IteratorLoop]](#manual-Std___IteratorLoop___mk) α m m] (it : [IterM]](#manual-Std___IterM___mk) m β)
  (f : β → m ([Option]](#manual-Option___none) γ)) : m ([Option]](#manual-Option___none) γ)
```

Returns the first non-`[none]](#manual-Option___none)` result of applying the monadic function `f` to each output
of the iterator, in order. Returns `[none]](#manual-Option___none)` if `f` returns `[none]](#manual-Option___none)` for all outputs.

`O(|it|)`. Short-circuits when `f` returns `[some]](#manual-Option___none) _`. The outputs of `it` are
examined in order of iteration.

If the iterator is not finite, this function might run forever. The variant
`it.[ensureTermination]](#manual-Std___IterM___ensureTermination).findSomeM?` always terminates after finitely many steps.

Example:

```lean
[#eval]](#manual-Lean___Parser___Command___eval) ([7, 6, 5, 8, 1, 2, 6].[iterM]](#manual-List___iterM) [IO]](#manual-IO)).[findSomeM?]](#manual-Std___IterM___findSomeM___) fun i => [do]](#manual-Lean___Parser___Term___do)
if i < 5 then
return [some]](#manual-Option___none) (i * 10)
if i ≤ 6 then
[IO.println]](#manual-IO___println) s!"Almost! {i}"
return [none]](#manual-Option___none)
```

```lean
Almost! 6
Almost! 5
```

```lean
[some]](#manual-Option___none) 10
```

def

```lean
[Std.IterM.atIdx?.{u_1, u_2}]](#manual-Std___IterM___atIdx___) {α : Type u_1} {m : Type u_1 → Type u_2}
  {β : Type u_1} [[Iterator]](#manual-Std___Iterator___mk) α m β] [[IteratorAccess]](#manual-Std___IteratorAccess___mk) α m] [[Monad]](#manual-Monad___mk) m]
  (it : [IterM]](#manual-Std___IterM___mk) m β) (n : [Nat]](#manual-Nat___zero)) : m ([Option]](#manual-Option___none) β)



[Std.IterM.atIdx?.{u_1, u_2}]](#manual-Std___IterM___atIdx___) {α : Type u_1}
  {m : Type u_1 → Type u_2} {β : Type u_1}
  [[Iterator]](#manual-Std___Iterator___mk) α m β] [[IteratorAccess]](#manual-Std___IteratorAccess___mk) α m]
  [[Monad]](#manual-Monad___mk) m] (it : [IterM]](#manual-Std___IterM___mk) m β) (n : [Nat]](#manual-Nat___zero)) :
  m ([Option]](#manual-Option___none) β)
```

Returns the `n`-th value emitted by `it`, or `[none]](#manual-Option___none)` if `it` terminates earlier.

For monadic iterators, the monadic effects of this operation may differ from manually iterating
to the `n`-th value because `atIdx?` can take shortcuts. By the signature, the return value
is guaranteed to plausible in the sense of `IterM.IsPlausibleNthOutputStep`.

This function is only available for iterators that explicitly support it by implementing
the `[IteratorAccess]](#manual-Std___IteratorAccess___mk)` typeclass.

### 22.3.4. Collectors {#manual-The-Lean-Language-Reference--Iterators--Consuming-Iterators--Collectors}

Collectors consume an iterator, returning all of its data in a list or array.
To be collected, an iterator must be finite.

def

```lean
[Std.Iter.toArray.{w}]](#manual-Std___Iter___toArray) {α β : Type w} [[Iterator]](#manual-Std___Iterator___mk) α [Id]](#manual-Id) β] (it : [Iter]](#manual-Std___Iter___mk) β) :
  [Array]](#manual-Array___mk) β



[Std.Iter.toArray.{w}]](#manual-Std___Iter___toArray) {α β : Type w}
  [[Iterator]](#manual-Std___Iterator___mk) α [Id]](#manual-Id) β] (it : [Iter]](#manual-Std___Iter___mk) β) :
  [Array]](#manual-Array___mk) β
```

Traverses the given iterator and stores the emitted values in an array.

If the iterator is not finite, this function might run forever. The variant
`it.[ensureTermination]](#manual-Std___Iter___ensureTermination).toArray` always terminates after finitely many steps.

def

```lean
[Std.IterM.toArray.{w, w'}]](#manual-Std___IterM___toArray) {α β : Type w} {m : Type w → Type w'}
  [[Monad]](#manual-Monad___mk) m] [[Iterator]](#manual-Std___Iterator___mk) α m β] (it : [IterM]](#manual-Std___IterM___mk) m β) : m ([Array]](#manual-Array___mk) β)



[Std.IterM.toArray.{w, w'}]](#manual-Std___IterM___toArray) {α β : Type w}
  {m : Type w → Type w'} [[Monad]](#manual-Monad___mk) m]
  [[Iterator]](#manual-Std___Iterator___mk) α m β] (it : [IterM]](#manual-Std___IterM___mk) m β) :
  m ([Array]](#manual-Array___mk) β)
```

Traverses the given iterator and stores the emitted values in an array.

If the iterator is not finite, this function might run forever. The variant
`it.[ensureTermination]](#manual-Std___IterM___ensureTermination).toArray` always terminates after finitely many steps.

def

```lean
[Std.Iter.toList.{w}]](#manual-Std___Iter___toList) {α β : Type w} [[Iterator]](#manual-Std___Iterator___mk) α [Id]](#manual-Id) β] (it : [Iter]](#manual-Std___Iter___mk) β) :
  [List]](#manual-List___nil) β



[Std.Iter.toList.{w}]](#manual-Std___Iter___toList) {α β : Type w}
  [[Iterator]](#manual-Std___Iterator___mk) α [Id]](#manual-Id) β] (it : [Iter]](#manual-Std___Iter___mk) β) : [List]](#manual-List___nil) β
```

Traverses the given iterator and stores the emitted values in a list. Because
lists are prepend-only, `toListRev` is usually more efficient that `toList`.

If the iterator is not finite, this function might run forever. The variant
`it.[ensureTermination]](#manual-Std___Iter___ensureTermination).toList` always terminates after finitely many steps.

def

```lean
[Std.IterM.toList.{w, w'}]](#manual-Std___IterM___toList) {α : Type w} {m : Type w → Type w'} [[Monad]](#manual-Monad___mk) m]
  {β : Type w} [[Iterator]](#manual-Std___Iterator___mk) α m β] (it : [IterM]](#manual-Std___IterM___mk) m β) : m ([List]](#manual-List___nil) β)



[Std.IterM.toList.{w, w'}]](#manual-Std___IterM___toList) {α : Type w}
  {m : Type w → Type w'} [[Monad]](#manual-Monad___mk) m]
  {β : Type w} [[Iterator]](#manual-Std___Iterator___mk) α m β]
  (it : [IterM]](#manual-Std___IterM___mk) m β) : m ([List]](#manual-List___nil) β)
```

Traverses the given iterator and stores the emitted values in a list. Because
lists are prepend-only, `toListRev` is usually more efficient that `toList`.

If the iterator is not finite, this function might run forever. The variant
`it.[ensureTermination]](#manual-Std___IterM___ensureTermination).toList` always terminates after finitely many steps.

def

```lean
[Std.Iter.toListRev.{w}]](#manual-Std___Iter___toListRev) {α β : Type w} [[Iterator]](#manual-Std___Iterator___mk) α [Id]](#manual-Id) β] (it : [Iter]](#manual-Std___Iter___mk) β) :
  [List]](#manual-List___nil) β



[Std.Iter.toListRev.{w}]](#manual-Std___Iter___toListRev) {α β : Type w}
  [[Iterator]](#manual-Std___Iterator___mk) α [Id]](#manual-Id) β] (it : [Iter]](#manual-Std___Iter___mk) β) : [List]](#manual-List___nil) β
```

Traverses the given iterator and stores the emitted values in reverse order in a list. Because
lists are prepend-only, this `toListRev` is usually more efficient that `toList`.

If the iterator is not finite, this function might run forever. The variant
`it.[ensureTermination]](#manual-Std___Iter___ensureTermination).toListRev` always terminates after finitely many steps.

def

```lean
[Std.IterM.toListRev.{w, w'}]](#manual-Std___IterM___toListRev) {α : Type w} {m : Type w → Type w'}
  [[Monad]](#manual-Monad___mk) m] {β : Type w} [[Iterator]](#manual-Std___Iterator___mk) α m β] (it : [IterM]](#manual-Std___IterM___mk) m β) : m ([List]](#manual-List___nil) β)



[Std.IterM.toListRev.{w, w'}]](#manual-Std___IterM___toListRev) {α : Type w}
  {m : Type w → Type w'} [[Monad]](#manual-Monad___mk) m]
  {β : Type w} [[Iterator]](#manual-Std___Iterator___mk) α m β]
  (it : [IterM]](#manual-Std___IterM___mk) m β) : m ([List]](#manual-List___nil) β)
```

Traverses the given iterator and stores the emitted values in reverse order in a list. Because
lists are prepend-only, this `toListRev` is usually more efficient that `toList`.

If the iterator is not finite, this function might run forever. The variant
`it.[ensureTermination]](#manual-Std___IterM___ensureTermination).toListRev` always terminates after finitely many steps.

---



## Iterators — 22.4. Iterator Combinators {#manual-iterators-224-iterator-combinators}

> 📄 Source: https://lean-lang.org/doc/reference/latest/Iterators/Iterator-Combinators/

The documentation for iterator combinators often includes *marble diagrams* that show the relationship between the elements returned by the underlying iterators and the elements returned by the combinator's iterator.
Marble diagrams provide examples, not full specifications.
These diagrams consist of a number of rows.
Each row shows an example of an iterator's output, where `-` indicates a `[skip]](#manual-Std___PlausibleIterStep___skip)`, a term indicates a value returned with `[yield]](#manual-Std___PlausibleIterStep___yield)`, and `⊥` indicates the end of iteration.
Spaces indicate that iteration did not occur.
Unbound identifiers in the marble diagram stand for arbitrary values of the iterator's element type.

Vertical alignment in the marble diagram indicates a causal relationship: when two elements are aligned, it means that consuming the iterator in the lower row results in the upper rows being consumed.
In particular, consuming up to the `n`th column of the lower iterator results in the consumption of the first `n` columns from the upper iterator.

A marble diagram for an identity iterator combinator that returns each element from the underlying iterator looks like this:

```
it    ---a-----b---c----d⊥
it.id ---a-----b---c----d⊥
```

A marble diagram for an iterator combinator that duplicates each element of the underlying iterator looks like this:

```
it           ---a  ---b  ---c  ---d⊥
it.double    ---a-a---b-b---c-c---d-d⊥
```

The marble diagram for `[Iter.filter]](#manual-Std___Iter___filter)` shows how some elements of the underlying iterator do not occur in the filtered iterator, but also that stepping the filtered iterator results in a `[skip]](#manual-Std___PlausibleIterStep___skip)` when the underlying iterator returns a value that doesn't satisfy the predicate:

```
it            ---a--b--c--d-e--⊥
it.filter     ---a-----c-------⊥
```

The diagram requires an explanatory note:

> (given that `f a = f c = true` and `f b = f d = d e = false`)

The diagram for `[Iter.zip]](#manual-Std___Iter___zip)` shows how consuming the combined iterator consumes the underlying iterators:

```
left               --a        ---b        --c
right                 --x         --y        --⊥
left.zip right     -----(a, x)------(b, y)-----⊥
```

The zipped iterator emits `[skip]](#manual-Std___PlausibleIterStep___skip)`s so long as `left` does.
When `left` emits `a`, the zipped iterator emits one more `[skip]](#manual-Std___PlausibleIterStep___skip)`.
After this, the zipped iterator switches to consuming `right`, and it emits `[skip]](#manual-Std___PlausibleIterStep___skip)`s so long as `right` does.
When `right` emits `x`, the zipped iterator emits the pair `(a, x)`.
This interleaving of `left` and `right` continues until one of them stops, at which point the zipped iterator stops.
Blank spaces in the upper rows of the marble diagram indicate that the iterator is not being consumed at that step.

### 22.4.1. Pure Combinators {#manual-The-Lean-Language-Reference--Iterators--Iterator-Combinators--Pure-Combinators}

constructor of Std.IterM

```lean
Std.IterM.mk.{w, w'} {α : Type w} {m : Type w → Type w'} {β : Type w}
  (internalState : α) : [IterM]](#manual-Std___IterM___mk) m β



Std.IterM.mk.{w, w'} {α : Type w}
  {m : Type w → Type w'} {β : Type w}
  (internalState : α) : [IterM]](#manual-Std___IterM___mk) m β
```

Wraps the state of an iterator into an `[Iter]](#manual-Std___Iter___mk)` object.

def

```lean
[Std.Iter.toIterM.{w}]](#manual-Std___Iter___toIterM) {α β : Type w} (it : [Iter]](#manual-Std___Iter___mk) β) : [IterM]](#manual-Std___IterM___mk) [Id]](#manual-Id) β



[Std.Iter.toIterM.{w}]](#manual-Std___Iter___toIterM) {α β : Type w}
  (it : [Iter]](#manual-Std___Iter___mk) β) : [IterM]](#manual-Std___IterM___mk) [Id]](#manual-Id) β
```

Converts a pure iterator (`[Iter]](#manual-Std___Iter___mk) β`) into a monadic iterator (`[IterM]](#manual-Std___IterM___mk) [Id]](#manual-Id) β`) in the
identity monad `[Id]](#manual-Id)`.

def

```lean
[Std.Iter.take.{w}]](#manual-Std___Iter___take) {α β : Type w} [[Iterator]](#manual-Std___Iterator___mk) α [Id]](#manual-Id) β] (n : [Nat]](#manual-Nat___zero))
  (it : [Iter]](#manual-Std___Iter___mk) β) : [Iter]](#manual-Std___Iter___mk) β



[Std.Iter.take.{w}]](#manual-Std___Iter___take) {α β : Type w}
  [[Iterator]](#manual-Std___Iterator___mk) α [Id]](#manual-Id) β] (n : [Nat]](#manual-Nat___zero))
  (it : [Iter]](#manual-Std___Iter___mk) β) : [Iter]](#manual-Std___Iter___mk) β
```

Given an iterator `it` and a natural number `n`, `it.[take]](#manual-Std___Iter___take) n` is an iterator that outputs
up to the first `n` of `it`'s values in order and then terminates.

**Marble diagram:**

```lean
it ---a----b---c--d-e--⊥
it.take 3 ---a----b---c⊥
it ---a--⊥
it.take 3 ---a--⊥
```

**Termination properties:**

- `[Finite]](#manual-Std___Iterators___Finite___mk)` instance: only if `it` is productive
- `[Productive]](#manual-Std___Iterators___Productive___mk)` instance: only if `it` is productive

**Performance:**

This combinator incurs an additional O(1) cost with each output of `it`.

def

```lean
[Std.Iter.takeWhile.{w}]](#manual-Std___Iter___takeWhile) {α β : Type w} (P : β → [Bool]](#manual-Bool___false)) (it : [Iter]](#manual-Std___Iter___mk) β) :
  [Iter]](#manual-Std___Iter___mk) β



[Std.Iter.takeWhile.{w}]](#manual-Std___Iter___takeWhile) {α β : Type w}
  (P : β → [Bool]](#manual-Bool___false)) (it : [Iter]](#manual-Std___Iter___mk) β) : [Iter]](#manual-Std___Iter___mk) β
```

Given an iterator `it` and a predicate `P`, `it.[takeWhile]](#manual-Std___Iter___takeWhile) P` is an iterator that outputs
the values emitted by `it` until one of those values is rejected by `P`.
If some emitted value is rejected by `P`, the value is dropped and the iterator terminates.

**Marble diagram:**

Assuming that the predicate `P` accepts `a` and `b` but rejects `c`:

```lean
it ---a----b---c--d-e--⊥
it.takeWhile P ---a----b---⊥
it ---a----⊥
it.takeWhile P ---a----⊥
```

**Termination properties:**

- `[Finite]](#manual-Std___Iterators___Finite___mk)` instance: only if `it` is finite
- `[Productive]](#manual-Std___Iterators___Productive___mk)` instance: only if `it` is productive

Depending on `P`, it is possible that `it.[takeWhile]](#manual-Std___Iter___takeWhile) P` is finite (or productive) although `it` is not.
In this case, the `[Finite]](#manual-Std___Iterators___Finite___mk)` (or `[Productive]](#manual-Std___Iterators___Productive___mk)`) instance needs to be proved manually.

**Performance:**

This combinator calls `P` on each output of `it` until the predicate evaluates to false. Then
it terminates.

def

```lean
[Std.Iter.toTake.{w}]](#manual-Std___Iter___toTake) {α β : Type w} [[Iterator]](#manual-Std___Iterator___mk) α [Id]](#manual-Id) β] [[Finite]](#manual-Std___Iterators___Finite___mk) α [Id]](#manual-Id)]
  (it : [Iter]](#manual-Std___Iter___mk) β) : [Iter]](#manual-Std___Iter___mk) β



[Std.Iter.toTake.{w}]](#manual-Std___Iter___toTake) {α β : Type w}
  [[Iterator]](#manual-Std___Iterator___mk) α [Id]](#manual-Id) β] [[Finite]](#manual-Std___Iterators___Finite___mk) α [Id]](#manual-Id)]
  (it : [Iter]](#manual-Std___Iter___mk) β) : [Iter]](#manual-Std___Iter___mk) β
```

This combinator is only useful for advanced use cases.

Given a finite iterator `it`, returns an iterator that behaves exactly like `it` but is of the same
type as `it.[take]](#manual-Std___Iter___take) n`.

**Marble diagram:**

```lean
it ---a----b---c--d-e--⊥
it.toTake ---a----b---c--d-e--⊥
```

**Termination properties:**

- `[Finite]](#manual-Std___Iterators___Finite___mk)` instance: always
- `[Productive]](#manual-Std___Iterators___Productive___mk)` instance: always

**Performance:**

This combinator incurs an additional O(1) cost with each output of `it`.

def

```lean
[Std.Iter.drop.{w}]](#manual-Std___Iter___drop) {α β : Type w} (n : [Nat]](#manual-Nat___zero)) (it : [Iter]](#manual-Std___Iter___mk) β) : [Iter]](#manual-Std___Iter___mk) β



[Std.Iter.drop.{w}]](#manual-Std___Iter___drop) {α β : Type w} (n : [Nat]](#manual-Nat___zero))
  (it : [Iter]](#manual-Std___Iter___mk) β) : [Iter]](#manual-Std___Iter___mk) β
```

Given an iterator `it` and a natural number `n`, `it.[drop]](#manual-Std___Iter___drop) n` is an iterator that forwards all of
`it`'s output values except for the first `n`.

**Marble diagram:**

```lean
it ---a----b---c--d-e--⊥
it.drop 3 ---------------d-e--⊥
it ---a--⊥
it.drop 3 ------⊥
```

**Termination properties:**

- `[Finite]](#manual-Std___Iterators___Finite___mk)` instance: only if `it` is finite
- `[Productive]](#manual-Std___Iterators___Productive___mk)` instance: only if `it` is productive

**Performance:**

Currently, this combinator incurs an additional O(1) cost with each output of `it`, even when the iterator
does not drop any elements anymore.

def

```lean
[Std.Iter.dropWhile.{w}]](#manual-Std___Iter___dropWhile) {α β : Type w} (P : β → [Bool]](#manual-Bool___false)) (it : [Iter]](#manual-Std___Iter___mk) β) :
  [Iter]](#manual-Std___Iter___mk) β



[Std.Iter.dropWhile.{w}]](#manual-Std___Iter___dropWhile) {α β : Type w}
  (P : β → [Bool]](#manual-Bool___false)) (it : [Iter]](#manual-Std___Iter___mk) β) : [Iter]](#manual-Std___Iter___mk) β
```

Given an iterator `it` and a predicate `P`, `it.[dropWhile]](#manual-Std___Iter___dropWhile) P` is an iterator that
emits the values emitted by `it` starting from the first value that is rejected by `P`.
The elements before are dropped.

In situations where `P` is monadic, use `dropWhileM` instead.

**Marble diagram:**

Assuming that the predicate `P` accepts `a` and `b` but rejects `c`:

```lean
it ---a----b---c--d-e--⊥
it.dropWhile P ------------c--d-e--⊥
it ---a----⊥
it.dropWhile P --------⊥
```

**Termination properties:**

- `[Finite]](#manual-Std___Iterators___Finite___mk)` instance: only if `it` is finite
- `[Productive]](#manual-Std___Iterators___Productive___mk)` instance: only if `it` is finite

Depending on `P`, it is possible that `it.dropWhileM P` is productive although
`it` is not. In this case, the `[Productive]](#manual-Std___Iterators___Productive___mk)` instance needs to be proved manually.

**Performance:**

This combinator calls `P` on each output of `it` until the predicate evaluates to false. After
that, the combinator incurs an additional O(1) cost for each value emitted by `it`.

def

```lean
[Std.Iter.stepSize.{u_1}]](#manual-Std___Iter___stepSize) {α β : Type u_1} [[Iterator]](#manual-Std___Iterator___mk) α [Id]](#manual-Id) β]
  [[IteratorAccess]](#manual-Std___IteratorAccess___mk) α [Id]](#manual-Id)] (it : [Iter]](#manual-Std___Iter___mk) β) (n : [Nat]](#manual-Nat___zero)) : [Iter]](#manual-Std___Iter___mk) β



[Std.Iter.stepSize.{u_1}]](#manual-Std___Iter___stepSize) {α β : Type u_1}
  [[Iterator]](#manual-Std___Iterator___mk) α [Id]](#manual-Id) β] [[IteratorAccess]](#manual-Std___IteratorAccess___mk) α [Id]](#manual-Id)]
  (it : [Iter]](#manual-Std___Iter___mk) β) (n : [Nat]](#manual-Nat___zero)) : [Iter]](#manual-Std___Iter___mk) β
```

Produces an iterator that emits one value of `it`, then drops `n - 1` elements, then emits another
value, and so on. In other words, it emits every `n`-th value of `it`, starting with the first one.

If `n = 0`, the iterator behaves like for `n = 1`: It emits all values of `it`.

**Marble diagram:**

```lean
it ---1----2----3---4----5
it.stepSize 2 ---1---------3--------5
```

**Availability:**

This operation is currently only available for iterators implementing `[IteratorAccess]](#manual-Std___IteratorAccess___mk)`,
such as `PRange.iter` range iterators.

**Termination properties:**

- `[Finite]](#manual-Std___Iterators___Finite___mk)` instance: only if the base iterator `it` is finite
- `[Productive]](#manual-Std___Iterators___Productive___mk)` instance: always

def

```lean
[Std.Iter.map.{w}]](#manual-Std___Iter___map) {α β γ : Type w} [[Iterator]](#manual-Std___Iterator___mk) α [Id]](#manual-Id) β] (f : β → γ)
  (it : [Iter]](#manual-Std___Iter___mk) β) : [Iter]](#manual-Std___Iter___mk) γ



[Std.Iter.map.{w}]](#manual-Std___Iter___map) {α β γ : Type w}
  [[Iterator]](#manual-Std___Iterator___mk) α [Id]](#manual-Id) β] (f : β → γ)
  (it : [Iter]](#manual-Std___Iter___mk) β) : [Iter]](#manual-Std___Iter___mk) γ
```

If `it` is an iterator, then `it.[map]](#manual-Std___Iter___map) f` is another iterator that applies a
function `f` to all values emitted by `it` and emits the result.

In situations where `f` is monadic, use `mapM` instead.

**Marble diagram:**

```lean
it ---a --b --c --d -e ----⊥
it.map ---a'--b'--c'--d'-e'----⊥
```

(given that `f a = a'`, `f b = b'` etc.)

**Termination properties:**

- `[Finite]](#manual-Std___Iterators___Finite___mk)` instance: only if `it` is finite
- `[Productive]](#manual-Std___Iterators___Productive___mk)` instance: only if `it` is productive

**Performance:**

For each value emitted by the base iterator `it`, this combinator calls `f`.

def

```lean
[Std.Iter.mapM.{w, w'}]](#manual-Std___Iter___mapM) {α β γ : Type w} [[Iterator]](#manual-Std___Iterator___mk) α [Id]](#manual-Id) β]
  {m : Type w → Type w'} [[Monad]](#manual-Monad___mk) m] [MonadAttach m] (f : β → m γ)
  (it : [Iter]](#manual-Std___Iter___mk) β) : [IterM]](#manual-Std___IterM___mk) m γ



[Std.Iter.mapM.{w, w'}]](#manual-Std___Iter___mapM) {α β γ : Type w}
  [[Iterator]](#manual-Std___Iterator___mk) α [Id]](#manual-Id) β] {m : Type w → Type w'}
  [[Monad]](#manual-Monad___mk) m] [MonadAttach m] (f : β → m γ)
  (it : [Iter]](#manual-Std___Iter___mk) β) : [IterM]](#manual-Std___IterM___mk) m γ
```

If `it` is an iterator, then `it.[mapM]](#manual-Std___Iter___mapM) f` is another iterator that applies a monadic
function `f` to all values emitted by `it` and emits the result.

The base iterator `it` being monadic in `m`, `f` can return values in any monad `n` for which a
`[MonadLiftT]](#manual-MonadLiftT___mk) m n` instance is available.

If `f` is pure, then the simpler variant `it.[map]](#manual-Std___Iter___map)` can be used instead.

**Marble diagram (without monadic effects):**

```lean
it ---a --b --c --d -e ----⊥
it.mapM ---a'--b'--c'--d'-e'----⊥
```

(given that `f a = [pure]](#manual-Pure___mk) a'`, `f b = [pure]](#manual-Pure___mk) b'` etc.)

**Termination properties:**

- `[Finite]](#manual-Std___Iterators___Finite___mk)` instance: only if `it` is finite
- `[Productive]](#manual-Std___Iterators___Productive___mk)` instance: only if `it` is productive

For certain mapping functions `f`, the resulting iterator will be finite (or productive) even though
no `[Finite]](#manual-Std___Iterators___Finite___mk)` (or `[Productive]](#manual-Std___Iterators___Productive___mk)`) instance is provided. For example, if `f` is an `[ExceptT]](#manual-ExceptT)` monad and
will always fail, then `it.mapM` will be finite even if `it` isn't. In such cases, the termination
proof needs to be done manually.

**Performance:**

For each value emitted by the base iterator `it`, this combinator calls `f`.

def

```lean
[Std.Iter.mapWithPostcondition.{w, w'}]](#manual-Std___Iter___mapWithPostcondition) {α β γ : Type w} [[Iterator]](#manual-Std___Iterator___mk) α [Id]](#manual-Id) β]
  {m : Type w → Type w'} [[Monad]](#manual-Monad___mk) m] (f : β → [PostconditionT](https://lean-lang.org/doc/reference/latest/Iterators/Reasoning-About-Iterators/#Std___Iterators___PostconditionT___mk) m γ)
  (it : [Iter]](#manual-Std___Iter___mk) β) : [IterM]](#manual-Std___IterM___mk) m γ



[Std.Iter.mapWithPostcondition.{w, w'}]](#manual-Std___Iter___mapWithPostcondition)
  {α β γ : Type w} [[Iterator]](#manual-Std___Iterator___mk) α [Id]](#manual-Id) β]
  {m : Type w → Type w'} [[Monad]](#manual-Monad___mk) m]
  (f : β → [PostconditionT](https://lean-lang.org/doc/reference/latest/Iterators/Reasoning-About-Iterators/#Std___Iterators___PostconditionT___mk) m γ)
  (it : [Iter]](#manual-Std___Iter___mk) β) : [IterM]](#manual-Std___IterM___mk) m γ
```

*Note: This is a very general combinator that requires an advanced understanding of monads,
dependent types and termination proofs. The variants `map` and `mapM` are easier to use and
sufficient for most use cases.*

If `it` is an iterator, then `it.[mapWithPostcondition]](#manual-Std___Iter___mapWithPostcondition) f` is another iterator that applies a monadic
function `f` to all values emitted by `it` and emits the result.

`f` is expected to return `[PostconditionT](https://lean-lang.org/doc/reference/latest/Iterators/Reasoning-About-Iterators/#Std___Iterators___PostconditionT___mk) n _`, where `n` is an arbitrary monad.
The `[PostconditionT](https://lean-lang.org/doc/reference/latest/Iterators/Reasoning-About-Iterators/#Std___Iterators___PostconditionT___mk)` transformer allows the caller to intrinsically prove properties about
`f`'s return value in the monad `n`, enabling termination proofs depending on the specific behavior
of `f`.

**Marble diagram (without monadic effects):**

```lean
it ---a --b --c --d -e ----⊥
it.mapWithPostcondition ---a'--b'--c'--d'-e'----⊥
```

(given that `f a = [pure]](#manual-Pure___mk) a'`, `f b = [pure]](#manual-Pure___mk) b'` etc.)

**Termination properties:**

- `[Finite]](#manual-Std___Iterators___Finite___mk)` instance: only if `it` is finite
- `[Productive]](#manual-Std___Iterators___Productive___mk)` instance: only if `it` is productive

For certain mapping functions `f`, the resulting iterator will be finite (or productive) even though
no `[Finite]](#manual-Std___Iterators___Finite___mk)` (or `[Productive]](#manual-Std___Iterators___Productive___mk)`) instance is provided. For example, if `f` is an `[ExceptT]](#manual-ExceptT)` monad and
will always fail, then `it.mapWithPostcondition` will be finite even if `it` isn't.

In such situations, the missing instances can be proved manually if the postcondition bundled in
the `[PostconditionT](https://lean-lang.org/doc/reference/latest/Iterators/Reasoning-About-Iterators/#Std___Iterators___PostconditionT___mk) n` monad is strong enough. In the given example, a suitable postcondition might
be `fun _ => [False]](#manual-False)`.

**Performance:**

For each value emitted by the base iterator `it`, this combinator calls `f`.

def

```lean
[Std.Iter.uLift.{v, u}]](#manual-Std___Iter___uLift) {α β : Type u} (it : [Iter]](#manual-Std___Iter___mk) β) : [Iter]](#manual-Std___Iter___mk) ([ULift]](#manual-ULift___up) β)



[Std.Iter.uLift.{v, u}]](#manual-Std___Iter___uLift) {α β : Type u}
  (it : [Iter]](#manual-Std___Iter___mk) β) : [Iter]](#manual-Std___Iter___mk) ([ULift]](#manual-ULift___up) β)
```

Transforms an iterator with values in `β` into one with values in `[ULift]](#manual-ULift___up) β`.

Most other combinators like `map` cannot switch between universe levels. This combinators
makes it possible to transition to a higher universe.

**Marble diagram:**

```lean
it ---a ----b ---c --d ---⊥
it.uLift n ---.up a----.up b---.up c--.up d---⊥
```

**Termination properties:**

- `[Finite]](#manual-Std___Iterators___Finite___mk)`: only if the original iterator is finite
- `[Productive]](#manual-Std___Iterators___Productive___mk)`: only if the original iterator is productive

def

```lean
[Std.Iter.flatMap.{w}]](#manual-Std___Iter___flatMap) {α β α₂ γ : Type w} [[Iterator]](#manual-Std___Iterator___mk) α [Id]](#manual-Id) β]
  [[Iterator]](#manual-Std___Iterator___mk) α₂ [Id]](#manual-Id) γ] (f : β → [Iter]](#manual-Std___Iter___mk) γ) (it : [Iter]](#manual-Std___Iter___mk) β) : [Iter]](#manual-Std___Iter___mk) γ



[Std.Iter.flatMap.{w}]](#manual-Std___Iter___flatMap) {α β α₂ γ : Type w}
  [[Iterator]](#manual-Std___Iterator___mk) α [Id]](#manual-Id) β] [[Iterator]](#manual-Std___Iterator___mk) α₂ [Id]](#manual-Id) γ]
  (f : β → [Iter]](#manual-Std___Iter___mk) γ) (it : [Iter]](#manual-Std___Iter___mk) β) : [Iter]](#manual-Std___Iter___mk) γ
```

Let `it` be an iterator and `f` a function mapping `it`'s outputs to iterators.
Then `it.[flatMap]](#manual-Std___Iter___flatMap) f` is an iterator that goes over `it` and for each output, it applies `f` and
iterates over the resulting iterator. `it.[flatMap]](#manual-Std___Iter___flatMap) f` emits all values obtained from the inner
iterators -- first, all of the first inner iterator, then all of the second one, and so on.

**Marble diagram:**

```
it                 ---a      --b      c    --d -⊥
f a                    a1-a2⊥
f b                             b1-b2⊥
f c                                    c1-c2⊥
f d                                           ⊥
it.flatMap         ----a1-a2----b1-b2--c1-c2----⊥
```

**Termination properties:**

- `[Finite]](#manual-Std___Iterators___Finite___mk)` instance: only if `it` and the inner iterators are finite
- `[Productive]](#manual-Std___Iterators___Productive___mk)` instance: only if `it` is finite and the inner iterators are productive

For certain functions `f`, the resulting iterator will be finite (or productive) even though
no `[Finite]](#manual-Std___Iterators___Finite___mk)` (or `[Productive]](#manual-Std___Iterators___Productive___mk)`) instance is provided out of the box. For example, if the outer
iterator is productive and the inner
iterators are productive *and provably never empty*, then the resulting iterator is also productive.

**Performance:**

This combinator incurs an additional O(1) cost with each output of `it` or an internal iterator.

For each value emitted by the outer iterator `it`, this combinator calls `f`.

def

```lean
[Std.Iter.flatMapM.{w, w'}]](#manual-Std___Iter___flatMapM) {α β α₂ γ : Type w} {m : Type w → Type w'}
  [[Monad]](#manual-Monad___mk) m] [MonadAttach m] [[Iterator]](#manual-Std___Iterator___mk) α [Id]](#manual-Id) β] [[Iterator]](#manual-Std___Iterator___mk) α₂ m γ]
  (f : β → m ([IterM]](#manual-Std___IterM___mk) m γ)) (it : [Iter]](#manual-Std___Iter___mk) β) : [IterM]](#manual-Std___IterM___mk) m γ



[Std.Iter.flatMapM.{w, w'}]](#manual-Std___Iter___flatMapM)
  {α β α₂ γ : Type w}
  {m : Type w → Type w'} [[Monad]](#manual-Monad___mk) m]
  [MonadAttach m] [[Iterator]](#manual-Std___Iterator___mk) α [Id]](#manual-Id) β]
  [[Iterator]](#manual-Std___Iterator___mk) α₂ m γ]
  (f : β → m ([IterM]](#manual-Std___IterM___mk) m γ)) (it : [Iter]](#manual-Std___Iter___mk) β) :
  [IterM]](#manual-Std___IterM___mk) m γ
```

Let `it` be an iterator and `f` a monadic function mapping `it`'s outputs to iterators.
Then `it.[flatMapM]](#manual-Std___Iter___flatMapM) f` is an iterator that goes over `it` and for each output, it applies `f` and
iterates over the resulting iterator. `it.[flatMapM]](#manual-Std___Iter___flatMapM) f` emits all values obtained from the inner
iterators -- first, all of the first inner iterator, then all of the second one, and so on.

**Marble diagram (without monadic effects):**

```
it                 ---a      --b      c    --d -⊥
f a                    a1-a2⊥
f b                             b1-b2⊥
f c                                    c1-c2⊥
f d                                           ⊥
it.flatMapM        ----a1-a2----b1-b2--c1-c2----⊥
```

**Termination properties:**

- `[Finite]](#manual-Std___Iterators___Finite___mk)` instance: only if `it` and the inner iterators are finite
- `[Productive]](#manual-Std___Iterators___Productive___mk)` instance: only if `it` is finite and the inner iterators are productive

For certain functions `f`, the resulting iterator will be finite (or productive) even though
no `[Finite]](#manual-Std___Iterators___Finite___mk)` (or `[Productive]](#manual-Std___Iterators___Productive___mk)`) instance is provided out of the box. For example, if the outer
iterator is productive and the inner
iterators are productive *and provably never empty*, then the resulting iterator is also productive.

**Performance:**

This combinator incurs an additional O(1) cost with each output of `it` or an internal iterator.

For each value emitted by the outer iterator `it`, this combinator calls `f`.

def

```lean
[Std.Iter.flatMapAfter.{w}]](#manual-Std___Iter___flatMapAfter) {α β α₂ γ : Type w} [[Iterator]](#manual-Std___Iterator___mk) α [Id]](#manual-Id) β]
  [[Iterator]](#manual-Std___Iterator___mk) α₂ [Id]](#manual-Id) γ] (f : β → [Iter]](#manual-Std___Iter___mk) γ) (it₁ : [Iter]](#manual-Std___Iter___mk) β)
  (it₂ : [Option]](#manual-Option___none) ([Iter]](#manual-Std___Iter___mk) γ)) : [Iter]](#manual-Std___Iter___mk) γ



[Std.Iter.flatMapAfter.{w}]](#manual-Std___Iter___flatMapAfter)
  {α β α₂ γ : Type w} [[Iterator]](#manual-Std___Iterator___mk) α [Id]](#manual-Id) β]
  [[Iterator]](#manual-Std___Iterator___mk) α₂ [Id]](#manual-Id) γ] (f : β → [Iter]](#manual-Std___Iter___mk) γ)
  (it₁ : [Iter]](#manual-Std___Iter___mk) β) (it₂ : [Option]](#manual-Option___none) ([Iter]](#manual-Std___Iter___mk) γ)) :
  [Iter]](#manual-Std___Iter___mk) γ
```

Let `it₁` and `it₂` be iterators and `f` a function mapping `it₁`'s outputs to iterators
of the same type as `it₂`. Then `it₁.[flatMapAfter]](#manual-Std___Iter___flatMapAfter) f it₂` first goes over `it₂` and then over
`it₁.[flatMap]](#manual-Std___Iter___flatMap) f it₂`, emitting all their values.

The main purpose of this combinator is to represent the intermediate state of a `flatMap` iterator
that is currently iterating over one of the inner iterators.

**Marble diagram:**

```
it₁                            --b      c    --d -⊥
it₂                      a1-a2⊥
f b                               b1-b2⊥
f c                                      c1-c2⊥
f d                                             ⊥
it.flatMapAfter  f it₂   a1-a2----b1-b2--c1-c2----⊥
```

**Termination properties:**

- `[Finite]](#manual-Std___Iterators___Finite___mk)` instance: only if `it₁`, `it₂` and the inner iterators are finite
- `[Productive]](#manual-Std___Iterators___Productive___mk)` instance: only if `it₁` is finite and `it₂` and the inner iterators are productive

For certain functions `f`, the resulting iterator will be finite (or productive) even though
no `[Finite]](#manual-Std___Iterators___Finite___mk)` (or `[Productive]](#manual-Std___Iterators___Productive___mk)`) instance is provided out of the box. For example, if the outer
iterator is productive and the inner
iterators are productive *and provably never empty*, then the resulting iterator is also productive.

**Performance:**

This combinator incurs an additional O(1) cost with each output of `it₁`, `it₂` or an internal
iterator.

For each value emitted by the outer iterator `it₁`, this combinator calls `f`.

def

```lean
[Std.Iter.flatMapAfterM.{w, w'}]](#manual-Std___Iter___flatMapAfterM) {α β α₂ γ : Type w}
  {m : Type w → Type w'} [[Monad]](#manual-Monad___mk) m] [MonadAttach m] [[Iterator]](#manual-Std___Iterator___mk) α [Id]](#manual-Id) β]
  [[Iterator]](#manual-Std___Iterator___mk) α₂ m γ] (f : β → m ([IterM]](#manual-Std___IterM___mk) m γ)) (it₁ : [Iter]](#manual-Std___Iter___mk) β)
  (it₂ : [Option]](#manual-Option___none) ([IterM]](#manual-Std___IterM___mk) m γ)) : [IterM]](#manual-Std___IterM___mk) m γ



[Std.Iter.flatMapAfterM.{w, w'}]](#manual-Std___Iter___flatMapAfterM)
  {α β α₂ γ : Type w}
  {m : Type w → Type w'} [[Monad]](#manual-Monad___mk) m]
  [MonadAttach m] [[Iterator]](#manual-Std___Iterator___mk) α [Id]](#manual-Id) β]
  [[Iterator]](#manual-Std___Iterator___mk) α₂ m γ]
  (f : β → m ([IterM]](#manual-Std___IterM___mk) m γ)) (it₁ : [Iter]](#manual-Std___Iter___mk) β)
  (it₂ : [Option]](#manual-Option___none) ([IterM]](#manual-Std___IterM___mk) m γ)) : [IterM]](#manual-Std___IterM___mk) m γ
```

Let `it₁` and `it₂` be iterators and `f` a monadic function mapping `it₁`'s outputs to iterators
of the same type as `it₂`. Then `it₁.[flatMapAfterM]](#manual-Std___Iter___flatMapAfterM) f it₂` first goes over `it₂` and then over
`it₁.flatMap f it₂`, emitting all their values.

The main purpose of this combinator is to represent the intermediate state of a `flatMap` iterator
that is currently iterating over one of the inner iterators.

**Marble diagram (without monadic effects):**

```
it₁                            --b      c    --d -⊥
it₂                      a1-a2⊥
f b                               b1-b2⊥
f c                                      c1-c2⊥
f d                                             ⊥
it.flatMapAfterM f it₂   a1-a2----b1-b2--c1-c2----⊥
```

**Termination properties:**

- `[Finite]](#manual-Std___Iterators___Finite___mk)` instance: only if `it₁`, `it₂` and the inner iterators are finite
- `[Productive]](#manual-Std___Iterators___Productive___mk)` instance: only if `it₁` is finite and `it₂` and the inner iterators are productive

For certain functions `f`, the resulting iterator will be finite (or productive) even though
no `[Finite]](#manual-Std___Iterators___Finite___mk)` (or `[Productive]](#manual-Std___Iterators___Productive___mk)`) instance is provided out of the box. For example, if the outer
iterator is productive and the inner
iterators are productive *and provably never empty*, then the resulting iterator is also productive.

**Performance:**

This combinator incurs an additional O(1) cost with each output of `it₁`, `it₂` or an internal
iterator.

For each value emitted by the outer iterator `it₁`, this combinator calls `f`.

def

```lean
[Std.Iter.filter.{w}]](#manual-Std___Iter___filter) {α β : Type w} [[Iterator]](#manual-Std___Iterator___mk) α [Id]](#manual-Id) β] (f : β → [Bool]](#manual-Bool___false))
  (it : [Iter]](#manual-Std___Iter___mk) β) : [Iter]](#manual-Std___Iter___mk) β



[Std.Iter.filter.{w}]](#manual-Std___Iter___filter) {α β : Type w}
  [[Iterator]](#manual-Std___Iterator___mk) α [Id]](#manual-Id) β] (f : β → [Bool]](#manual-Bool___false))
  (it : [Iter]](#manual-Std___Iter___mk) β) : [Iter]](#manual-Std___Iter___mk) β
```

If `it` is an iterator, then `it.[filter]](#manual-Std___Iter___filter) f` is another iterator that applies a
predicate `f` to all values emitted by `it` and emits them only if they are accepted by `f`.

In situations where `f` is monadic, use `filterM` instead.

**Marble diagram (without monadic effects):**

```lean
it ---a--b--c--d-e--⊥
it.filter ---a-----c-------⊥
```

(given that `f a = f c = true` and `f b = f d = d e = false`)

**Termination properties:**

- `[Finite]](#manual-Std___Iterators___Finite___mk)` instance: only if `it` is finite
- `[Productive]](#manual-Std___Iterators___Productive___mk)` instance: only if `it` is finite`

For certain mapping functions `f`, the resulting iterator will be productive even though
no `[Productive]](#manual-Std___Iterators___Productive___mk)` instance is provided. For example, if `f` always returns `[True]](#manual-True___intro)`, the resulting
iterator will be productive as long as `it` is. In such situations, the missing instance needs to
be proved manually.

**Performance:**

For each value emitted by the base iterator `it`, this combinator calls `f` and matches on the
returned value.

def

```lean
[Std.Iter.filterM.{w, w'}]](#manual-Std___Iter___filterM) {α β : Type w} [[Iterator]](#manual-Std___Iterator___mk) α [Id]](#manual-Id) β]
  {m : Type w → Type w'} [[Monad]](#manual-Monad___mk) m] [MonadAttach m]
  (f : β → m ([ULift]](#manual-ULift___up) [Bool]](#manual-Bool___false))) (it : [Iter]](#manual-Std___Iter___mk) β) : [IterM]](#manual-Std___IterM___mk) m β



[Std.Iter.filterM.{w, w'}]](#manual-Std___Iter___filterM) {α β : Type w}
  [[Iterator]](#manual-Std___Iterator___mk) α [Id]](#manual-Id) β] {m : Type w → Type w'}
  [[Monad]](#manual-Monad___mk) m] [MonadAttach m]
  (f : β → m ([ULift]](#manual-ULift___up) [Bool]](#manual-Bool___false))) (it : [Iter]](#manual-Std___Iter___mk) β) :
  [IterM]](#manual-Std___IterM___mk) m β
```

If `it` is an iterator, then `it.[filterM]](#manual-Std___Iter___filterM) f` is another iterator that applies a monadic
predicate `f` to all values emitted by `it` and emits them only if they are accepted by `f`.

If `f` is pure, then the simpler variant `it.[filter]](#manual-Std___Iter___filter)` can be used instead.

**Marble diagram (without monadic effects):**

```lean
it ---a--b--c--d-e--⊥
it.filterM ---a-----c-------⊥
```

(given that `f a = f c = pure true` and `f b = f d = d e = pure false`)

**Termination properties:**

- `[Finite]](#manual-Std___Iterators___Finite___mk)` instance: only if `it` is finite
- `[Productive]](#manual-Std___Iterators___Productive___mk)` instance: only if `it` is finite`

For certain mapping functions `f`, the resulting iterator will be finite (or productive) even though
no `[Finite]](#manual-Std___Iterators___Finite___mk)` (or `[Productive]](#manual-Std___Iterators___Productive___mk)`) instance is provided. For example, if `f` is an `[ExceptT]](#manual-ExceptT)` monad and
will always fail, then `it.filterWithPostcondition` will be finite -- and productive -- even if `it`
isn't. In such cases, the termination proof needs to be done manually.

**Performance:**

For each value emitted by the base iterator `it`, this combinator calls `f`.

def

```lean
[Std.Iter.filterWithPostcondition.{w, w'}]](#manual-Std___Iter___filterWithPostcondition) {α β : Type w}
  [[Iterator]](#manual-Std___Iterator___mk) α [Id]](#manual-Id) β] {m : Type w → Type w'} [[Monad]](#manual-Monad___mk) m]
  (f : β → [PostconditionT](https://lean-lang.org/doc/reference/latest/Iterators/Reasoning-About-Iterators/#Std___Iterators___PostconditionT___mk) m ([ULift]](#manual-ULift___up) [Bool]](#manual-Bool___false))) (it : [Iter]](#manual-Std___Iter___mk) β) : [IterM]](#manual-Std___IterM___mk) m β



[Std.Iter.filterWithPostcondition.{w, w'}]](#manual-Std___Iter___filterWithPostcondition)
  {α β : Type w} [[Iterator]](#manual-Std___Iterator___mk) α [Id]](#manual-Id) β]
  {m : Type w → Type w'} [[Monad]](#manual-Monad___mk) m]
  (f : β → [PostconditionT](https://lean-lang.org/doc/reference/latest/Iterators/Reasoning-About-Iterators/#Std___Iterators___PostconditionT___mk) m ([ULift]](#manual-ULift___up) [Bool]](#manual-Bool___false)))
  (it : [Iter]](#manual-Std___Iter___mk) β) : [IterM]](#manual-Std___IterM___mk) m β
```

*Note: This is a very general combinator that requires an advanced understanding of monads,
dependent types and termination proofs. The variants `filter` and `filterM` are easier to use and
sufficient for most use cases.*

If `it` is an iterator, then `it.[filterWithPostcondition]](#manual-Std___Iter___filterWithPostcondition) f` is another iterator that applies a monadic
predicate `f` to all values emitted by `it` and emits them only if they are accepted by `f`.

`f` is expected to return `[PostconditionT](https://lean-lang.org/doc/reference/latest/Iterators/Reasoning-About-Iterators/#Std___Iterators___PostconditionT___mk) n ([ULift]](#manual-ULift___up) [Bool]](#manual-Bool___false))`, where `n` is an arbitrary monad.
The `[PostconditionT](https://lean-lang.org/doc/reference/latest/Iterators/Reasoning-About-Iterators/#Std___Iterators___PostconditionT___mk)` transformer allows the caller to intrinsically prove properties about
`f`'s return value in the monad `n`, enabling termination proofs depending on the specific behavior
of `f`.

**Marble diagram (without monadic effects):**

```lean
it ---a--b--c--d-e--⊥
it.filterWithPostcondition ---a-----c-------⊥
```

(given that `f a = f c = pure true` and `f b = f d = d e = pure false`)

**Termination properties:**

- `[Finite]](#manual-Std___Iterators___Finite___mk)` instance: only if `it` is finite
- `[Productive]](#manual-Std___Iterators___Productive___mk)` instance: only if `it` is finite`

For certain mapping functions `f`, the resulting iterator will be finite (or productive) even though
no `[Finite]](#manual-Std___Iterators___Finite___mk)` (or `[Productive]](#manual-Std___Iterators___Productive___mk)`) instance is provided. For example, if `f` is an `[ExceptT]](#manual-ExceptT)` monad and
will always fail, then `it.filterWithPostcondition` will be finite -- and productive -- even if `it`
isn't.

In such situations, the missing instances can be proved manually if the postcondition bundled in
the `[PostconditionT](https://lean-lang.org/doc/reference/latest/Iterators/Reasoning-About-Iterators/#Std___Iterators___PostconditionT___mk) n` monad is strong enough. In the given example, a suitable postcondition might
be `fun _ => [False]](#manual-False)`.

**Performance:**

For each value emitted by the base iterator `it`, this combinator calls `f`.

def

```lean
[Std.Iter.filterMap.{w}]](#manual-Std___Iter___filterMap) {α β γ : Type w} [[Iterator]](#manual-Std___Iterator___mk) α [Id]](#manual-Id) β]
  (f : β → [Option]](#manual-Option___none) γ) (it : [Iter]](#manual-Std___Iter___mk) β) : [Iter]](#manual-Std___Iter___mk) γ



[Std.Iter.filterMap.{w}]](#manual-Std___Iter___filterMap) {α β γ : Type w}
  [[Iterator]](#manual-Std___Iterator___mk) α [Id]](#manual-Id) β] (f : β → [Option]](#manual-Option___none) γ)
  (it : [Iter]](#manual-Std___Iter___mk) β) : [Iter]](#manual-Std___Iter___mk) γ
```

If `it` is an iterator, then `it.[filterMap]](#manual-Std___Iter___filterMap) f` is another iterator that applies a function `f` to all
values emitted by `it`. `f` is expected to return an `[Option]](#manual-Option___none)`. If it returns `[none]](#manual-Option___none)`, then nothing is
emitted; if it returns `[some]](#manual-Option___none) x`, then `x` is emitted.

In situations where `f` is monadic, use `filterMapM` instead.

**Marble diagram:**

```lean
it ---a --b--c --d-e--⊥
it.filterMap ---a'-----c'-------⊥
```

(given that `f a = [some]](#manual-Option___none) a'`, `f c = c'` and `f b = f d = d e = none`)

**Termination properties:**

- `[Finite]](#manual-Std___Iterators___Finite___mk)` instance: only if `it` is finite
- `[Productive]](#manual-Std___Iterators___Productive___mk)` instance: only if `it` is finite`

For certain mapping functions `f`, the resulting iterator will be productive even though
no `[Productive]](#manual-Std___Iterators___Productive___mk)` instance is provided. For example, if `f` never returns `[none]](#manual-Option___none)`, then
this combinator will preserve productiveness. In such situations, the missing instance needs to
be proved manually.

**Performance:**

For each value emitted by the base iterator `it`, this combinator calls `f` and matches on the
returned `[Option]](#manual-Option___none)` value.

def

```lean
[Std.Iter.filterMapM.{w, w'}]](#manual-Std___Iter___filterMapM) {α β γ : Type w} [[Iterator]](#manual-Std___Iterator___mk) α [Id]](#manual-Id) β]
  {m : Type w → Type w'} [[Monad]](#manual-Monad___mk) m] [MonadAttach m]
  (f : β → m ([Option]](#manual-Option___none) γ)) (it : [Iter]](#manual-Std___Iter___mk) β) : [IterM]](#manual-Std___IterM___mk) m γ



[Std.Iter.filterMapM.{w, w'}]](#manual-Std___Iter___filterMapM)
  {α β γ : Type w} [[Iterator]](#manual-Std___Iterator___mk) α [Id]](#manual-Id) β]
  {m : Type w → Type w'} [[Monad]](#manual-Monad___mk) m]
  [MonadAttach m] (f : β → m ([Option]](#manual-Option___none) γ))
  (it : [Iter]](#manual-Std___Iter___mk) β) : [IterM]](#manual-Std___IterM___mk) m γ
```

If `it` is an iterator, then `it.[filterMapM]](#manual-Std___Iter___filterMapM) f` is another iterator that applies a monadic
function `f` to all values emitted by `it`. `f` is expected to return an `[Option]](#manual-Option___none)` inside the monad.
If `f` returns `[none]](#manual-Option___none)`, then nothing is emitted; if it returns `[some]](#manual-Option___none) x`, then `x` is emitted.

If `f` is pure, then the simpler variant `it.[filterMap]](#manual-Std___Iter___filterMap)` can be used instead.

**Marble diagram (without monadic effects):**

```lean
it ---a --b--c --d-e--⊥
it.filterMapM ---a'-----c'-------⊥
```

(given that `f a = pure (some a)'`, `f c = [pure]](#manual-Pure___mk) ([some]](#manual-Option___none) c')` and `f b = f d = d e = pure none`)

**Termination properties:**

- `[Finite]](#manual-Std___Iterators___Finite___mk)` instance: only if `it` is finite
- `[Productive]](#manual-Std___Iterators___Productive___mk)` instance: only if `it` is finite`

For certain mapping functions `f`, the resulting iterator will be finite (or productive) even though
no `[Finite]](#manual-Std___Iterators___Finite___mk)` (or `[Productive]](#manual-Std___Iterators___Productive___mk)`) instance is provided. For example, if `f` never returns `[none]](#manual-Option___none)`, then
this combinator will preserve productiveness. If `f` is an `[ExceptT]](#manual-ExceptT)` monad and will always fail,
then `it.filterMapM` will be finite even if `it` isn't. In such cases, the termination proof needs
to be done manually.

**Performance:**

For each value emitted by the base iterator `it`, this combinator calls `f` and matches on the
returned `[Option]](#manual-Option___none)` value.

def

```lean
[Std.Iter.filterMapWithPostcondition.{w, w'}]](#manual-Std___Iter___filterMapWithPostcondition) {α β γ : Type w}
  [[Iterator]](#manual-Std___Iterator___mk) α [Id]](#manual-Id) β] {m : Type w → Type w'} [[Monad]](#manual-Monad___mk) m]
  (f : β → [PostconditionT](https://lean-lang.org/doc/reference/latest/Iterators/Reasoning-About-Iterators/#Std___Iterators___PostconditionT___mk) m ([Option]](#manual-Option___none) γ)) (it : [Iter]](#manual-Std___Iter___mk) β) : [IterM]](#manual-Std___IterM___mk) m γ



[Std.Iter.filterMapWithPostcondition.{w,
    w'}]](#manual-Std___Iter___filterMapWithPostcondition)
  {α β γ : Type w} [[Iterator]](#manual-Std___Iterator___mk) α [Id]](#manual-Id) β]
  {m : Type w → Type w'} [[Monad]](#manual-Monad___mk) m]
  (f : β → [PostconditionT](https://lean-lang.org/doc/reference/latest/Iterators/Reasoning-About-Iterators/#Std___Iterators___PostconditionT___mk) m ([Option]](#manual-Option___none) γ))
  (it : [Iter]](#manual-Std___Iter___mk) β) : [IterM]](#manual-Std___IterM___mk) m γ
```

*Note: This is a very general combinator that requires an advanced understanding of monads,
dependent types and termination proofs. The variants `filterMap` and `filterMapM` are easier to use
and sufficient for most use cases.*

If `it` is an iterator, then `it.[filterMapWithPostcondition]](#manual-Std___Iter___filterMapWithPostcondition) f` is another iterator that applies a monadic
function `f` to all values emitted by `it`. `f` is expected to return an `[Option]](#manual-Option___none)` inside the monad.
If `f` returns `[none]](#manual-Option___none)`, then nothing is emitted; if it returns `[some]](#manual-Option___none) x`, then `x` is emitted.

`f` is expected to return `[PostconditionT](https://lean-lang.org/doc/reference/latest/Iterators/Reasoning-About-Iterators/#Std___Iterators___PostconditionT___mk) n ([Option]](#manual-Option___none) _)`, where `n` is an arbitrary monad.
The `[PostconditionT](https://lean-lang.org/doc/reference/latest/Iterators/Reasoning-About-Iterators/#Std___Iterators___PostconditionT___mk)` transformer allows the caller to intrinsically prove properties about
`f`'s return value in the monad `n`, enabling termination proofs depending on the specific behavior
of `f`.

**Marble diagram (without monadic effects):**

```lean
it ---a --b--c --d-e--⊥
it.filterMapWithPostcondition ---a'-----c'-------⊥
```

(given that `f a = [pure]](#manual-Pure___mk) ([some]](#manual-Option___none) a')`, `f c = [pure]](#manual-Pure___mk) ([some]](#manual-Option___none) c')` and `f b = f d = d e = pure none`)

**Termination properties:**

- `[Finite]](#manual-Std___Iterators___Finite___mk)` instance: only if `it` is finite
- `[Productive]](#manual-Std___Iterators___Productive___mk)` instance: only if `it` is finite

For certain mapping functions `f`, the resulting iterator will be finite (or productive) even though
no `[Finite]](#manual-Std___Iterators___Finite___mk)` (or `[Productive]](#manual-Std___Iterators___Productive___mk)`) instance is provided. For example, if `f` never returns `[none]](#manual-Option___none)`, then
this combinator will preserve productiveness. If `f` is an `[ExceptT]](#manual-ExceptT)` monad and will always fail,
then `it.filterMapWithPostcondition` will be finite even if `it` isn't. In the first case, consider
using the `map`/`mapM`/`mapWithPostcondition` combinators instead, which provide more instances out of
the box.

In such situations, the missing instances can be proved manually if the postcondition bundled in
the `[PostconditionT](https://lean-lang.org/doc/reference/latest/Iterators/Reasoning-About-Iterators/#Std___Iterators___PostconditionT___mk) n` monad is strong enough. If `f` always returns `[some]](#manual-Option___none) _`, a suitable
postcondition is `fun x => x.isSome`; if `f` always fails, a suitable postcondition might be
`fun _ => [False]](#manual-False)`.

**Performance:**

For each value emitted by the base iterator `it`, this combinator calls `f` and matches on the
returned `[Option]](#manual-Option___none)` value.

def

```lean
[Std.Iter.zip.{w}]](#manual-Std___Iter___zip) {α₁ β₁ α₂ β₂ : Type w} [[Iterator]](#manual-Std___Iterator___mk) α₁ [Id]](#manual-Id) β₁]
  [[Iterator]](#manual-Std___Iterator___mk) α₂ [Id]](#manual-Id) β₂] (left : [Iter]](#manual-Std___Iter___mk) β₁) (right : [Iter]](#manual-Std___Iter___mk) β₂) :
  [Iter]](#manual-Std___Iter___mk) [(]](#manual-Prod___mk)β₁ [×]](#manual-Prod___mk) β₂[)]](#manual-Prod___mk)



[Std.Iter.zip.{w}]](#manual-Std___Iter___zip) {α₁ β₁ α₂ β₂ : Type w}
  [[Iterator]](#manual-Std___Iterator___mk) α₁ [Id]](#manual-Id) β₁] [[Iterator]](#manual-Std___Iterator___mk) α₂ [Id]](#manual-Id) β₂]
  (left : [Iter]](#manual-Std___Iter___mk) β₁) (right : [Iter]](#manual-Std___Iter___mk) β₂) :
  [Iter]](#manual-Std___Iter___mk) [(]](#manual-Prod___mk)β₁ [×]](#manual-Prod___mk) β₂[)]](#manual-Prod___mk)
```

Given two iterators `left` and `right`, `left.[zip]](#manual-Std___Iter___zip) right` is an iterator that yields pairs of
outputs of `left` and `right`. When one of them terminates,
the `zip` iterator will also terminate.

**Marble diagram:**

```lean
left --a ---b --c
right --x --y --⊥
left.zip right -----(a, x)------(b, y)-----⊥
```

**Termination properties:**

- `[Finite]](#manual-Std___Iterators___Finite___mk)` instance: only if either `left` or `right` is finite and the other is productive
- `[Productive]](#manual-Std___Iterators___Productive___mk)` instance: only if `left` and `right` are productive

There are situations where `left.[zip]](#manual-Std___Iter___zip) right` is finite (or productive) but none of the instances
above applies. For example, if `left` immediately terminates but `right` always skips, then
`left.[zip]](#manual-Std___Iter___zip).right` is finite even though no `[Finite]](#manual-Std___Iterators___Finite___mk)` (or even `[Productive]](#manual-Std___Iterators___Productive___mk)`) instance is available.
Such instances need to be proved manually.

**Performance:**

This combinator incurs an additional O(1) cost with each step taken by `left` or `right`.

Right now, the compiler does not unbox the internal state, leading to worse performance than
theoretically possible.

def

```lean
[Std.Iter.attachWith.{w}]](#manual-Std___Iter___attachWith) {α β : Type w} [[Iterator]](#manual-Std___Iterator___mk) α [Id]](#manual-Id) β] (it : [Iter]](#manual-Std___Iter___mk) β)
  (P : β → Prop)
  (h : ∀ (out : β), it.[IsPlausibleIndirectOutput](https://lean-lang.org/doc/reference/latest/Iterators/Reasoning-About-Iterators/#Std___Iter___IsPlausibleIndirectOutput___direct) out → P out) :
  [Iter]](#manual-Std___Iter___mk) [{]](#manual-Subtype___mk) out [//]](#manual-Subtype___mk) P out [}]](#manual-Subtype___mk)



[Std.Iter.attachWith.{w}]](#manual-Std___Iter___attachWith) {α β : Type w}
  [[Iterator]](#manual-Std___Iterator___mk) α [Id]](#manual-Id) β] (it : [Iter]](#manual-Std___Iter___mk) β)
  (P : β → Prop)
  (h :
    ∀ (out : β),
      it.[IsPlausibleIndirectOutput](https://lean-lang.org/doc/reference/latest/Iterators/Reasoning-About-Iterators/#Std___Iter___IsPlausibleIndirectOutput___direct) out →
        P out) :
  [Iter]](#manual-Std___Iter___mk) [{]](#manual-Subtype___mk) out [//]](#manual-Subtype___mk) P out [}]](#manual-Subtype___mk)
```

“Attaches” individual proofs to an iterator of values that satisfy a predicate `P`, returning an
iterator with values in the corresponding subtype `{ x // P x }`.

**Termination properties:**

- `[Finite]](#manual-Std___Iterators___Finite___mk)` instance: only if the base iterator is finite
- `[Productive]](#manual-Std___Iterators___Productive___mk)` instance: only if the base iterator is productive

### 22.4.2. Monadic Combinators {#manual-The-Lean-Language-Reference--Iterators--Iterator-Combinators--Monadic-Combinators}

def

```lean
[Std.IterM.toIter.{w}]](#manual-Std___IterM___toIter) {α β : Type w} (it : [IterM]](#manual-Std___IterM___mk) [Id]](#manual-Id) β) : [Iter]](#manual-Std___Iter___mk) β



[Std.IterM.toIter.{w}]](#manual-Std___IterM___toIter) {α β : Type w}
  (it : [IterM]](#manual-Std___IterM___mk) [Id]](#manual-Id) β) : [Iter]](#manual-Std___Iter___mk) β
```

Converts a monadic iterator (`[IterM]](#manual-Std___IterM___mk) [Id]](#manual-Id) β`) over `[Id]](#manual-Id)` into a pure iterator (`[Iter]](#manual-Std___Iter___mk) β`).

def

```lean
[Std.IterM.take.{w, w'}]](#manual-Std___IterM___take) {α : Type w} {m : Type w → Type w'} {β : Type w}
  [[Iterator]](#manual-Std___Iterator___mk) α m β] (n : [Nat]](#manual-Nat___zero)) (it : [IterM]](#manual-Std___IterM___mk) m β) : [IterM]](#manual-Std___IterM___mk) m β



[Std.IterM.take.{w, w'}]](#manual-Std___IterM___take) {α : Type w}
  {m : Type w → Type w'} {β : Type w}
  [[Iterator]](#manual-Std___Iterator___mk) α m β] (n : [Nat]](#manual-Nat___zero))
  (it : [IterM]](#manual-Std___IterM___mk) m β) : [IterM]](#manual-Std___IterM___mk) m β
```

Given an iterator `it` and a natural number `n`, `it.[take]](#manual-Std___IterM___take) n` is an iterator that outputs
up to the first `n` of `it`'s values in order and then terminates.

**Marble diagram:**

```lean
it ---a----b---c--d-e--⊥
it.take 3 ---a----b---c⊥
it ---a--⊥
it.take 3 ---a--⊥
```

**Termination properties:**

- `[Finite]](#manual-Std___Iterators___Finite___mk)` instance: only if `it` is productive
- `[Productive]](#manual-Std___Iterators___Productive___mk)` instance: only if `it` is productive

**Performance:**

This combinator incurs an additional O(1) cost with each output of `it`.

def

```lean
[Std.IterM.takeWhile.{w, w'}]](#manual-Std___IterM___takeWhile) {α : Type w} {m : Type w → Type w'}
  {β : Type w} [[Monad]](#manual-Monad___mk) m] (P : β → [Bool]](#manual-Bool___false)) (it : [IterM]](#manual-Std___IterM___mk) m β) : [IterM]](#manual-Std___IterM___mk) m β



[Std.IterM.takeWhile.{w, w'}]](#manual-Std___IterM___takeWhile) {α : Type w}
  {m : Type w → Type w'} {β : Type w}
  [[Monad]](#manual-Monad___mk) m] (P : β → [Bool]](#manual-Bool___false))
  (it : [IterM]](#manual-Std___IterM___mk) m β) : [IterM]](#manual-Std___IterM___mk) m β
```

Given an iterator `it` and a predicate `P`, `it.[takeWhile]](#manual-Std___IterM___takeWhile) P` is an iterator that outputs
the values emitted by `it` until one of those values is rejected by `P`.
If some emitted value is rejected by `P`, the value is dropped and the iterator terminates.

In situations where `P` is monadic, use `takeWhileM` instead.

**Marble diagram (ignoring monadic effects):**

Assuming that the predicate `P` accepts `a` and `b` but rejects `c`:

```lean
it ---a----b---c--d-e--⊥
it.takeWhile P ---a----b---⊥
it ---a----⊥
it.takeWhile P ---a----⊥
```

**Termination properties:**

- `[Finite]](#manual-Std___Iterators___Finite___mk)` instance: only if `it` is finite
- `[Productive]](#manual-Std___Iterators___Productive___mk)` instance: only if `it` is productive

Depending on `P`, it is possible that `it.[takeWhile]](#manual-Std___IterM___takeWhile) P` is finite (or productive) although `it` is not.
In this case, the `[Finite]](#manual-Std___Iterators___Finite___mk)` (or `[Productive]](#manual-Std___Iterators___Productive___mk)`) instance needs to be proved manually.

**Performance:**

This combinator calls `P` on each output of `it` until the predicate evaluates to false. Then
it terminates.

def

```lean
[Std.IterM.takeWhileM.{w, w'}]](#manual-Std___IterM___takeWhileM) {α : Type w} {m : Type w → Type w'}
  {β : Type w} [[Monad]](#manual-Monad___mk) m] [MonadAttach m] (P : β → m ([ULift]](#manual-ULift___up) [Bool]](#manual-Bool___false)))
  (it : [IterM]](#manual-Std___IterM___mk) m β) : [IterM]](#manual-Std___IterM___mk) m β



[Std.IterM.takeWhileM.{w, w'}]](#manual-Std___IterM___takeWhileM) {α : Type w}
  {m : Type w → Type w'} {β : Type w}
  [[Monad]](#manual-Monad___mk) m] [MonadAttach m]
  (P : β → m ([ULift]](#manual-ULift___up) [Bool]](#manual-Bool___false)))
  (it : [IterM]](#manual-Std___IterM___mk) m β) : [IterM]](#manual-Std___IterM___mk) m β
```

Given an iterator `it` and a monadic predicate `P`, `it.[takeWhileM]](#manual-Std___IterM___takeWhileM) P` is an iterator that outputs
the values emitted by `it` until one of those values is rejected by `P`.
If some emitted value is rejected by `P`, the value is dropped and the iterator terminates.

If `P` is pure, then the simpler variant `takeWhile` can be used instead.

**Marble diagram (ignoring monadic effects):**

Assuming that the predicate `P` accepts `a` and `b` but rejects `c`:

```lean
it ---a----b---c--d-e--⊥
it.takeWhileM P ---a----b---⊥
it ---a----⊥
it.takeWhileM P ---a----⊥
```

**Termination properties:**

- `[Finite]](#manual-Std___Iterators___Finite___mk)` instance: only if `it` is finite
- `[Productive]](#manual-Std___Iterators___Productive___mk)` instance: only if `it` is productive

Depending on `P`, it is possible that `it.[takeWhileM]](#manual-Std___IterM___takeWhileM) P` is finite (or productive) although `it` is not.
In this case, the `[Finite]](#manual-Std___Iterators___Finite___mk)` (or `[Productive]](#manual-Std___Iterators___Productive___mk)`) instance needs to be proved manually.

**Performance:**

This combinator calls `P` on each output of `it` until the predicate evaluates to false. Then
it terminates.

def

```lean
[Std.IterM.takeWhileWithPostcondition.{w, w'}]](#manual-Std___IterM___takeWhileWithPostcondition) {α : Type w}
  {m : Type w → Type w'} {β : Type w}
  (P : β → [PostconditionT](https://lean-lang.org/doc/reference/latest/Iterators/Reasoning-About-Iterators/#Std___Iterators___PostconditionT___mk) m ([ULift]](#manual-ULift___up) [Bool]](#manual-Bool___false))) (it : [IterM]](#manual-Std___IterM___mk) m β) : [IterM]](#manual-Std___IterM___mk) m β



[Std.IterM.takeWhileWithPostcondition.{w,
    w'}]](#manual-Std___IterM___takeWhileWithPostcondition)
  {α : Type w} {m : Type w → Type w'}
  {β : Type w}
  (P : β → [PostconditionT](https://lean-lang.org/doc/reference/latest/Iterators/Reasoning-About-Iterators/#Std___Iterators___PostconditionT___mk) m ([ULift]](#manual-ULift___up) [Bool]](#manual-Bool___false)))
  (it : [IterM]](#manual-Std___IterM___mk) m β) : [IterM]](#manual-Std___IterM___mk) m β
```

*Note: This is a very general combinator that requires an advanced understanding of monads,
dependent types and termination proofs. The variants `takeWhile` and `takeWhileM` are easier to use
and sufficient for most use cases.*

Given an iterator `it` and a monadic predicate `P`, `it.[takeWhileWithPostcondition]](#manual-Std___IterM___takeWhileWithPostcondition) P` is an iterator
that emits the values emitted by `it` until one of those values is rejected by `P`.
If some emitted value is rejected by `P`, the value is dropped and the iterator terminates.

`P` is expected to return `[PostconditionT](https://lean-lang.org/doc/reference/latest/Iterators/Reasoning-About-Iterators/#Std___Iterators___PostconditionT___mk) m ([ULift]](#manual-ULift___up) [Bool]](#manual-Bool___false))`. The `[PostconditionT](https://lean-lang.org/doc/reference/latest/Iterators/Reasoning-About-Iterators/#Std___Iterators___PostconditionT___mk)` transformer allows
the caller to intrinsically prove properties about `P`'s return value in the monad `m`, enabling
termination proofs depending on the specific behavior of `P`.

**Marble diagram (ignoring monadic effects):**

Assuming that the predicate `P` accepts `a` and `b` but rejects `c`:

```lean
it ---a----b---c--d-e--⊥
it.takeWhileWithPostcondition P ---a----b---⊥
it ---a----⊥
it.takeWhileWithPostcondition P ---a----⊥
```

**Termination properties:**

- `[Finite]](#manual-Std___Iterators___Finite___mk)` instance: only if `it` is finite
- `[Productive]](#manual-Std___Iterators___Productive___mk)` instance: only if `it` is productive

Depending on `P`, it is possible that `it.[takeWhileWithPostcondition]](#manual-Std___IterM___takeWhileWithPostcondition) P` is finite (or productive)
although `it` is not. In this case, the `[Finite]](#manual-Std___Iterators___Finite___mk)` (or `[Productive]](#manual-Std___Iterators___Productive___mk)`) instance needs to be proved
manually.

**Performance:**

This combinator calls `P` on each output of `it` until the predicate evaluates to false. Then
it terminates.

def

```lean
[Std.IterM.toTake.{w, w'}]](#manual-Std___IterM___toTake) {α : Type w} {m : Type w → Type w'}
  {β : Type w} [[Iterator]](#manual-Std___Iterator___mk) α m β] [[Finite]](#manual-Std___Iterators___Finite___mk) α m] (it : [IterM]](#manual-Std___IterM___mk) m β) :
  [IterM]](#manual-Std___IterM___mk) m β



[Std.IterM.toTake.{w, w'}]](#manual-Std___IterM___toTake) {α : Type w}
  {m : Type w → Type w'} {β : Type w}
  [[Iterator]](#manual-Std___Iterator___mk) α m β] [[Finite]](#manual-Std___Iterators___Finite___mk) α m]
  (it : [IterM]](#manual-Std___IterM___mk) m β) : [IterM]](#manual-Std___IterM___mk) m β
```

This combinator is only useful for advanced use cases.

Given a finite iterator `it`, returns an iterator that behaves exactly like `it` but is of the same
type as `it.[take]](#manual-Std___IterM___take) n`.

**Marble diagram:**

```lean
it ---a----b---c--d-e--⊥
it.toTake ---a----b---c--d-e--⊥
```

**Termination properties:**

- `[Finite]](#manual-Std___Iterators___Finite___mk)` instance: always
- `[Productive]](#manual-Std___Iterators___Productive___mk)` instance: always

**Performance:**

This combinator incurs an additional O(1) cost with each output of `it`.

def

```lean
[Std.IterM.drop.{w, w'}]](#manual-Std___IterM___drop) {α : Type w} {m : Type w → Type w'} {β : Type w}
  (n : [Nat]](#manual-Nat___zero)) (it : [IterM]](#manual-Std___IterM___mk) m β) : [IterM]](#manual-Std___IterM___mk) m β



[Std.IterM.drop.{w, w'}]](#manual-Std___IterM___drop) {α : Type w}
  {m : Type w → Type w'} {β : Type w}
  (n : [Nat]](#manual-Nat___zero)) (it : [IterM]](#manual-Std___IterM___mk) m β) : [IterM]](#manual-Std___IterM___mk) m β
```

Given an iterator `it` and a natural number `n`, `it.[drop]](#manual-Std___IterM___drop) n` is an iterator that forwards all of
`it`'s output values except for the first `n`.

**Marble diagram:**

```lean
it ---a----b---c--d-e--⊥
it.drop 3 ---------------d-e--⊥
it ---a--⊥
it.drop 3 ------⊥
```

**Termination properties:**

- `[Finite]](#manual-Std___Iterators___Finite___mk)` instance: only if `it` is finite
- `[Productive]](#manual-Std___Iterators___Productive___mk)` instance: only if `it` is productive

**Performance:**

Currently, this combinator incurs an additional O(1) cost with each output of `it`, even when the iterator
does not drop any elements anymore.

def

```lean
[Std.IterM.dropWhile.{w, w'}]](#manual-Std___IterM___dropWhile) {α : Type w} {m : Type w → Type w'}
  {β : Type w} [[Monad]](#manual-Monad___mk) m] (P : β → [Bool]](#manual-Bool___false)) (it : [IterM]](#manual-Std___IterM___mk) m β) : [IterM]](#manual-Std___IterM___mk) m β



[Std.IterM.dropWhile.{w, w'}]](#manual-Std___IterM___dropWhile) {α : Type w}
  {m : Type w → Type w'} {β : Type w}
  [[Monad]](#manual-Monad___mk) m] (P : β → [Bool]](#manual-Bool___false))
  (it : [IterM]](#manual-Std___IterM___mk) m β) : [IterM]](#manual-Std___IterM___mk) m β
```

Given an iterator `it` and a predicate `P`, `it.[dropWhile]](#manual-Std___IterM___dropWhile) P` is an iterator that
emits the values emitted by `it` starting from the first value that is rejected by `P`.
The elements before are dropped.

In situations where `P` is monadic, use `dropWhileM` instead.

**Marble diagram (ignoring monadic effects):**

Assuming that the predicate `P` accepts `a` and `b` but rejects `c`:

```lean
it ---a----b---c--d-e--⊥
it.dropWhile P ------------c--d-e--⊥
it ---a----⊥
it.dropWhile P --------⊥
```

**Termination properties:**

- `[Finite]](#manual-Std___Iterators___Finite___mk)` instance: only if `it` is finite
- `[Productive]](#manual-Std___Iterators___Productive___mk)` instance: only if `it` is finite

**Performance:**

This combinator calls `P` on each output of `it` until the predicate evaluates to false. After
that, the combinator incurs an addictional O(1) cost for each value emitted by `it`.

def

```lean
[Std.IterM.dropWhileM.{w, w'}]](#manual-Std___IterM___dropWhileM) {α : Type w} {m : Type w → Type w'}
  {β : Type w} [[Monad]](#manual-Monad___mk) m] [MonadAttach m] (P : β → m ([ULift]](#manual-ULift___up) [Bool]](#manual-Bool___false)))
  (it : [IterM]](#manual-Std___IterM___mk) m β) : [IterM]](#manual-Std___IterM___mk) m β



[Std.IterM.dropWhileM.{w, w'}]](#manual-Std___IterM___dropWhileM) {α : Type w}
  {m : Type w → Type w'} {β : Type w}
  [[Monad]](#manual-Monad___mk) m] [MonadAttach m]
  (P : β → m ([ULift]](#manual-ULift___up) [Bool]](#manual-Bool___false)))
  (it : [IterM]](#manual-Std___IterM___mk) m β) : [IterM]](#manual-Std___IterM___mk) m β
```

Given an iterator `it` and a monadic predicate `P`, `it.[dropWhileM]](#manual-Std___IterM___dropWhileM) P` is an iterator that
emits the values emitted by `it` starting from the first value that is rejected by `P`.
The elements before are dropped.

If `P` is pure, then the simpler variant `dropWhile` can be used instead.

**Marble diagram (ignoring monadic effects):**

Assuming that the predicate `P` accepts `a` and `b` but rejects `c`:

```lean
it ---a----b---c--d-e--⊥
it.dropWhileM P ------------c--d-e--⊥
it ---a----⊥
it.dropWhileM P --------⊥
```

**Termination properties:**

- `[Finite]](#manual-Std___Iterators___Finite___mk)` instance: only if `it` is finite
- `[Productive]](#manual-Std___Iterators___Productive___mk)` instance: only if `it` is finite

Depending on `P`, it is possible that `it.[dropWhileM]](#manual-Std___IterM___dropWhileM) P` is finite (or productive) although
`it` is not. In this case, the `[Finite]](#manual-Std___Iterators___Finite___mk)` (or `[Productive]](#manual-Std___Iterators___Productive___mk)`) instance needs to be proved manually.

**Performance:**

This combinator calls `P` on each output of `it` until the predicate evaluates to false. After
that, the combinator incurs an addictional O(1) cost for each value emitted by `it`.

def

```lean
[Std.IterM.dropWhileWithPostcondition.{w, w'}]](#manual-Std___IterM___dropWhileWithPostcondition) {α : Type w}
  {m : Type w → Type w'} {β : Type w}
  (P : β → [PostconditionT](https://lean-lang.org/doc/reference/latest/Iterators/Reasoning-About-Iterators/#Std___Iterators___PostconditionT___mk) m ([ULift]](#manual-ULift___up) [Bool]](#manual-Bool___false))) (it : [IterM]](#manual-Std___IterM___mk) m β) : [IterM]](#manual-Std___IterM___mk) m β



[Std.IterM.dropWhileWithPostcondition.{w,
    w'}]](#manual-Std___IterM___dropWhileWithPostcondition)
  {α : Type w} {m : Type w → Type w'}
  {β : Type w}
  (P : β → [PostconditionT](https://lean-lang.org/doc/reference/latest/Iterators/Reasoning-About-Iterators/#Std___Iterators___PostconditionT___mk) m ([ULift]](#manual-ULift___up) [Bool]](#manual-Bool___false)))
  (it : [IterM]](#manual-Std___IterM___mk) m β) : [IterM]](#manual-Std___IterM___mk) m β
```

*Note: This is a very general combinator that requires an advanced understanding of monads,
dependent types and termination proofs. The variants `dropWhile` and `dropWhileM` are easier to use
and sufficient for most use cases.*

Given an iterator `it` and a monadic predicate `P`, `it.[dropWhileWithPostcondition]](#manual-Std___IterM___dropWhileWithPostcondition) P` is an iterator
that emits the values emitted by `it` starting from the first value that is rejected by `P`.
The elements before are dropped.

`P` is expected to return `[PostconditionT](https://lean-lang.org/doc/reference/latest/Iterators/Reasoning-About-Iterators/#Std___Iterators___PostconditionT___mk) m ([ULift]](#manual-ULift___up) [Bool]](#manual-Bool___false))`. The `[PostconditionT](https://lean-lang.org/doc/reference/latest/Iterators/Reasoning-About-Iterators/#Std___Iterators___PostconditionT___mk)` transformer allows
the caller to intrinsically prove properties about `P`'s return value in the monad `m`, enabling
termination proofs depending on the specific behavior of `P`.

**Marble diagram (ignoring monadic effects):**

Assuming that the predicate `P` accepts `a` and `b` but rejects `c`:

```lean
it ---a----b---c--d-e--⊥
it.dropWhileWithPostcondition P ------------c--d-e--⊥
it ---a----⊥
it.dropWhileWithPostcondition P --------⊥
```

**Termination properties:**

- `[Finite]](#manual-Std___Iterators___Finite___mk)` instance: only if `it` is finite
- `[Productive]](#manual-Std___Iterators___Productive___mk)` instance: only if `it` is finite

Depending on `P`, it is possible that `it.[dropWhileWithPostcondition]](#manual-Std___IterM___dropWhileWithPostcondition) P` is finite (or productive) although
`it` is not. In this case, the `[Finite]](#manual-Std___Iterators___Finite___mk)` (or `[Productive]](#manual-Std___Iterators___Productive___mk)`) instance needs to be proved manually.

**Performance:**

This combinator calls `P` on each output of `it` until the predicate evaluates to false. After
that, the combinator incurs an additional O(1) cost for each value emitted by `it`.

def

```lean
[Std.IterM.stepSize.{u_1, u_2}]](#manual-Std___IterM___stepSize) {α : Type u_1} {m : Type u_1 → Type u_2}
  {β : Type u_1} [[Iterator]](#manual-Std___Iterator___mk) α m β] [[IteratorAccess]](#manual-Std___IteratorAccess___mk) α m] [[Monad]](#manual-Monad___mk) m]
  (it : [IterM]](#manual-Std___IterM___mk) m β) (n : [Nat]](#manual-Nat___zero)) : [IterM]](#manual-Std___IterM___mk) m β



[Std.IterM.stepSize.{u_1, u_2}]](#manual-Std___IterM___stepSize)
  {α : Type u_1} {m : Type u_1 → Type u_2}
  {β : Type u_1} [[Iterator]](#manual-Std___Iterator___mk) α m β]
  [[IteratorAccess]](#manual-Std___IteratorAccess___mk) α m] [[Monad]](#manual-Monad___mk) m]
  (it : [IterM]](#manual-Std___IterM___mk) m β) (n : [Nat]](#manual-Nat___zero)) : [IterM]](#manual-Std___IterM___mk) m β
```

Produces an iterator that emits one value of `it`, then drops `n - 1` elements, then emits another
value, and so on. In other words, it emits every `n`-th value of `it`, starting with the first one.

If `n = 0`, the iterator behaves like for `n = 1`: It emits all values of `it`.

**Marble diagram:**

```lean
it ---1----2----3---4----5
it.stepSize 2 ---1---------3--------5
```

**Availability:**

This operation is currently only available for iterators implementing `[IteratorAccess]](#manual-Std___IteratorAccess___mk)`,
such as `PRange.iter` range iterators.

**Termination properties:**

- `[Finite]](#manual-Std___Iterators___Finite___mk)` instance: only if the base iterator `it` is finite
- `[Productive]](#manual-Std___Iterators___Productive___mk)` instance: always

def

```lean
[Std.IterM.map.{w, w'}]](#manual-Std___IterM___map) {α β γ : Type w} {m : Type w → Type w'}
  [[Iterator]](#manual-Std___Iterator___mk) α m β] [[Monad]](#manual-Monad___mk) m] (f : β → γ) (it : [IterM]](#manual-Std___IterM___mk) m β) : [IterM]](#manual-Std___IterM___mk) m γ



[Std.IterM.map.{w, w'}]](#manual-Std___IterM___map) {α β γ : Type w}
  {m : Type w → Type w'} [[Iterator]](#manual-Std___Iterator___mk) α m β]
  [[Monad]](#manual-Monad___mk) m] (f : β → γ) (it : [IterM]](#manual-Std___IterM___mk) m β) :
  [IterM]](#manual-Std___IterM___mk) m γ
```

If `it` is an iterator, then `it.[map]](#manual-Std___IterM___map) f` is another iterator that applies a
function `f` to all values emitted by `it` and emits the result.

In situations where `f` is monadic, use `mapM` instead.

**Marble diagram:**

```lean
it ---a --b --c --d -e ----⊥
it.map ---a'--b'--c'--d'-e'----⊥
```

(given that `f a = a'`, `f b = b'` etc.)

**Termination properties:**

- `[Finite]](#manual-Std___Iterators___Finite___mk)` instance: only if `it` is finite
- `[Productive]](#manual-Std___Iterators___Productive___mk)` instance: only if `it` is productive

**Performance:**

For each value emitted by the base iterator `it`, this combinator calls `f`.

def

```lean
[Std.IterM.mapM.{w, w', w''}]](#manual-Std___IterM___mapM) {α β γ : Type w} {m : Type w → Type w'}
  {n : Type w → Type w''} [[Iterator]](#manual-Std___Iterator___mk) α m β] [[Monad]](#manual-Monad___mk) n] [MonadAttach n]
  [[MonadLiftT]](#manual-MonadLiftT___mk) m n] (f : β → n γ) (it : [IterM]](#manual-Std___IterM___mk) m β) : [IterM]](#manual-Std___IterM___mk) n γ



[Std.IterM.mapM.{w, w', w''}]](#manual-Std___IterM___mapM)
  {α β γ : Type w} {m : Type w → Type w'}
  {n : Type w → Type w''} [[Iterator]](#manual-Std___Iterator___mk) α m β]
  [[Monad]](#manual-Monad___mk) n] [MonadAttach n]
  [[MonadLiftT]](#manual-MonadLiftT___mk) m n] (f : β → n γ)
  (it : [IterM]](#manual-Std___IterM___mk) m β) : [IterM]](#manual-Std___IterM___mk) n γ
```

If `it` is an iterator, then `it.[mapM]](#manual-Std___IterM___mapM) f` is another iterator that applies a monadic
function `f` to all values emitted by `it` and emits the result.

The base iterator `it` being monadic in `m`, `f` can return values in any monad `n` for which a
`[MonadLiftT]](#manual-MonadLiftT___mk) m n` instance is available.

If `f` is pure, then the simpler variant `it.[map]](#manual-Std___IterM___map)` can be used instead.

**Marble diagram (without monadic effects):**

```lean
it ---a --b --c --d -e ----⊥
it.mapM ---a'--b'--c'--d'-e'----⊥
```

(given that `f a = [pure]](#manual-Pure___mk) a'`, `f b = [pure]](#manual-Pure___mk) b'` etc.)

**Termination properties:**

- `[Finite]](#manual-Std___Iterators___Finite___mk)` instance: only if `it` is finite
- `[Productive]](#manual-Std___Iterators___Productive___mk)` instance: only if `it` is productive

For certain mapping functions `f`, the resulting iterator will be finite (or productive) even though
no `[Finite]](#manual-Std___Iterators___Finite___mk)` (or `[Productive]](#manual-Std___Iterators___Productive___mk)`) instance is provided. For example, if `f` is an `[ExceptT]](#manual-ExceptT)` monad and
will always fail, then `it.mapM` will be finite even if `it` isn't. In such cases, the termination
proof needs to be done manually.

**Performance:**

For each value emitted by the base iterator `it`, this combinator calls `f`.

def

```lean
[Std.IterM.mapWithPostcondition.{w, w', w''}]](#manual-Std___IterM___mapWithPostcondition) {α β γ : Type w}
  {m : Type w → Type w'} {n : Type w → Type w''} [[Monad]](#manual-Monad___mk) n]
  [[MonadLiftT]](#manual-MonadLiftT___mk) m n] [[Iterator]](#manual-Std___Iterator___mk) α m β] (f : β → [PostconditionT](https://lean-lang.org/doc/reference/latest/Iterators/Reasoning-About-Iterators/#Std___Iterators___PostconditionT___mk) n γ)
  (it : [IterM]](#manual-Std___IterM___mk) m β) : [IterM]](#manual-Std___IterM___mk) n γ



[Std.IterM.mapWithPostcondition.{w, w',
    w''}]](#manual-Std___IterM___mapWithPostcondition)
  {α β γ : Type w} {m : Type w → Type w'}
  {n : Type w → Type w''} [[Monad]](#manual-Monad___mk) n]
  [[MonadLiftT]](#manual-MonadLiftT___mk) m n] [[Iterator]](#manual-Std___Iterator___mk) α m β]
  (f : β → [PostconditionT](https://lean-lang.org/doc/reference/latest/Iterators/Reasoning-About-Iterators/#Std___Iterators___PostconditionT___mk) n γ)
  (it : [IterM]](#manual-Std___IterM___mk) m β) : [IterM]](#manual-Std___IterM___mk) n γ
```

*Note: This is a very general combinator that requires an advanced understanding of monads, dependent
types and termination proofs. The variants `map` and `mapM` are easier to use and sufficient
for most use cases.*

If `it` is an iterator, then `it.[mapWithPostcondition]](#manual-Std___IterM___mapWithPostcondition) f` is another iterator that applies a monadic
function `f` to all values emitted by `it` and emits the result.

`f` is expected to return `[PostconditionT](https://lean-lang.org/doc/reference/latest/Iterators/Reasoning-About-Iterators/#Std___Iterators___PostconditionT___mk) n _`. The base iterator `it` being monadic in
`m`, `n` can be different from `m`, but `it.[mapWithPostcondition]](#manual-Std___IterM___mapWithPostcondition) f` expects a `[MonadLiftT]](#manual-MonadLiftT___mk) m n`
instance. The `[PostconditionT](https://lean-lang.org/doc/reference/latest/Iterators/Reasoning-About-Iterators/#Std___Iterators___PostconditionT___mk)` transformer allows the caller to intrinsically prove properties about
`f`'s return value in the monad `n`, enabling termination proofs depending on the specific behavior
of `f`.

**Marble diagram (without monadic effects):**

```lean
it ---a --b --c --d -e ----⊥
it.mapWithPostcondition ---a'--b'--c'--d'-e'----⊥
```

(given that `f a = [pure]](#manual-Pure___mk) a'`, `f b = [pure]](#manual-Pure___mk) b'` etc.)

**Termination properties:**

- `[Finite]](#manual-Std___Iterators___Finite___mk)` instance: only if `it` is finite
- `[Productive]](#manual-Std___Iterators___Productive___mk)` instance: only if `it` is productive

For certain mapping functions `f`, the resulting iterator will be finite (or productive) even though
no `[Finite]](#manual-Std___Iterators___Finite___mk)` (or `[Productive]](#manual-Std___Iterators___Productive___mk)`) instance is provided. For example, if `f` is an `[ExceptT]](#manual-ExceptT)` monad and
will always fail, then `it.mapWithPostcondition` will be finite even if `it` isn't.

In such situations, the missing instances can be proved manually if the postcondition bundled in
the `[PostconditionT](https://lean-lang.org/doc/reference/latest/Iterators/Reasoning-About-Iterators/#Std___Iterators___PostconditionT___mk) n` monad is strong enough. In the given example, a suitable postcondition might
be `fun _ => [False]](#manual-False)`.

**Performance:**

For each value emitted by the base iterator `it`, this combinator calls `f`.

def

```lean
[Std.IterM.uLift.{v, u, v', u'}]](#manual-Std___IterM___uLift) {α β : Type u} {m : Type u → Type u'}
  (it : [IterM]](#manual-Std___IterM___mk) m β) (n : Type (max u v) → Type v')
  [lift : [MonadLiftT]](#manual-MonadLiftT___mk) m (ULiftT n)] : [IterM]](#manual-Std___IterM___mk) n ([ULift]](#manual-ULift___up) β)



[Std.IterM.uLift.{v, u, v', u'}]](#manual-Std___IterM___uLift)
  {α β : Type u} {m : Type u → Type u'}
  (it : [IterM]](#manual-Std___IterM___mk) m β)
  (n : Type (max u v) → Type v')
  [lift : [MonadLiftT]](#manual-MonadLiftT___mk) m (ULiftT n)] :
  [IterM]](#manual-Std___IterM___mk) n ([ULift]](#manual-ULift___up) β)
```

Transforms an `m`-monadic iterator with values in `β` into an `n`-monadic iterator with
values in `[ULift]](#manual-ULift___up) β`. Requires a `[MonadLift]](#manual-MonadLift___mk) m (ULiftT n)` instance.

**Marble diagram:**

```lean
it ---a ----b ---c --d ---⊥
it.uLift n ---.up a----.up b---.up c--.up d---⊥
```

**Termination properties:**

- `[Finite]](#manual-Std___Iterators___Finite___mk)`: only if the original iterator is finite
- `[Productive]](#manual-Std___Iterators___Productive___mk)`: only if the original iterator is productive

def

```lean
[Std.IterM.flatMap.{w, w'}]](#manual-Std___IterM___flatMap) {α β α₂ γ : Type w} {m : Type w → Type w'}
  [[Monad]](#manual-Monad___mk) m] [[Iterator]](#manual-Std___Iterator___mk) α m β] [[Iterator]](#manual-Std___Iterator___mk) α₂ m γ] (f : β → [IterM]](#manual-Std___IterM___mk) m γ)
  (it : [IterM]](#manual-Std___IterM___mk) m β) : [IterM]](#manual-Std___IterM___mk) m γ



[Std.IterM.flatMap.{w, w'}]](#manual-Std___IterM___flatMap)
  {α β α₂ γ : Type w}
  {m : Type w → Type w'} [[Monad]](#manual-Monad___mk) m]
  [[Iterator]](#manual-Std___Iterator___mk) α m β] [[Iterator]](#manual-Std___Iterator___mk) α₂ m γ]
  (f : β → [IterM]](#manual-Std___IterM___mk) m γ) (it : [IterM]](#manual-Std___IterM___mk) m β) :
  [IterM]](#manual-Std___IterM___mk) m γ
```

Let `it` be an iterator and `f` a function mapping `it`'s outputs to iterators.
Then `it.[flatMap]](#manual-Std___IterM___flatMap) f` is an iterator that goes over `it` and for each output, it applies `f` and
iterates over the resulting iterator. `it.[flatMap]](#manual-Std___IterM___flatMap) f` emits all values obtained from the inner
iterators -- first, all of the first inner iterator, then all of the second one, and so on.

**Marble diagram:**

```
it                 ---a      --b      c    --d -⊥
f a                    a1-a2⊥
f b                             b1-b2⊥
f c                                    c1-c2⊥
f d                                           ⊥
it.flatMap         ----a1-a2----b1-b2--c1-c2----⊥
```

**Termination properties:**

- `[Finite]](#manual-Std___Iterators___Finite___mk)` instance: only if `it` and the inner iterators are finite
- `[Productive]](#manual-Std___Iterators___Productive___mk)` instance: only if `it` is finite and the inner iterators are productive

For certain functions `f`, the resulting iterator will be finite (or productive) even though
no `[Finite]](#manual-Std___Iterators___Finite___mk)` (or `[Productive]](#manual-Std___Iterators___Productive___mk)`) instance is provided out of the box. For example, if the outer
iterator is productive and the inner
iterators are productive *and provably never empty*, then the resulting iterator is also productive.

**Performance:**

This combinator incurs an additional O(1) cost with each output of `it` or an internal iterator.

For each value emitted by the outer iterator `it`, this combinator calls `f`.

def

```lean
[Std.IterM.flatMapM.{w, w'}]](#manual-Std___IterM___flatMapM) {α β α₂ γ : Type w} {m : Type w → Type w'}
  [[Monad]](#manual-Monad___mk) m] [MonadAttach m] [[Iterator]](#manual-Std___Iterator___mk) α m β] [[Iterator]](#manual-Std___Iterator___mk) α₂ m γ]
  (f : β → m ([IterM]](#manual-Std___IterM___mk) m γ)) (it : [IterM]](#manual-Std___IterM___mk) m β) : [IterM]](#manual-Std___IterM___mk) m γ



[Std.IterM.flatMapM.{w, w'}]](#manual-Std___IterM___flatMapM)
  {α β α₂ γ : Type w}
  {m : Type w → Type w'} [[Monad]](#manual-Monad___mk) m]
  [MonadAttach m] [[Iterator]](#manual-Std___Iterator___mk) α m β]
  [[Iterator]](#manual-Std___Iterator___mk) α₂ m γ]
  (f : β → m ([IterM]](#manual-Std___IterM___mk) m γ))
  (it : [IterM]](#manual-Std___IterM___mk) m β) : [IterM]](#manual-Std___IterM___mk) m γ
```

Let `it` be an iterator and `f` a monadic function mapping `it`'s outputs to iterators.
Then `it.[flatMapM]](#manual-Std___IterM___flatMapM) f` is an iterator that goes over `it` and for each output, it applies `f` and
iterates over the resulting iterator. `it.[flatMapM]](#manual-Std___IterM___flatMapM) f` emits all values obtained from the inner
iterators -- first, all of the first inner iterator, then all of the second one, and so on.

**Marble diagram (without monadic effects):**

```
it                 ---a      --b      c    --d -⊥
f a                    a1-a2⊥
f b                             b1-b2⊥
f c                                    c1-c2⊥
f d                                           ⊥
it.flatMapM        ----a1-a2----b1-b2--c1-c2----⊥
```

**Termination properties:**

- `[Finite]](#manual-Std___Iterators___Finite___mk)` instance: only if `it` and the inner iterators are finite
- `[Productive]](#manual-Std___Iterators___Productive___mk)` instance: only if `it` is finite and the inner iterators are productive

For certain functions `f`, the resulting iterator will be finite (or productive) even though
no `[Finite]](#manual-Std___Iterators___Finite___mk)` (or `[Productive]](#manual-Std___Iterators___Productive___mk)`) instance is provided out of the box. For example, if the outer
iterator is productive and the inner
iterators are productive *and provably never empty*, then the resulting iterator is also productive.

**Performance:**

This combinator incurs an additional O(1) cost with each output of `it` or an internal iterator.

For each value emitted by the outer iterator `it`, this combinator calls `f`.

def

```lean
[Std.IterM.flatMapAfter.{w, w'}]](#manual-Std___IterM___flatMapAfter) {α β α₂ γ : Type w}
  {m : Type w → Type w'} [[Monad]](#manual-Monad___mk) m] [[Iterator]](#manual-Std___Iterator___mk) α m β] [[Iterator]](#manual-Std___Iterator___mk) α₂ m γ]
  (f : β → [IterM]](#manual-Std___IterM___mk) m γ) (it₁ : [IterM]](#manual-Std___IterM___mk) m β) (it₂ : [Option]](#manual-Option___none) ([IterM]](#manual-Std___IterM___mk) m γ)) :
  [IterM]](#manual-Std___IterM___mk) m γ



[Std.IterM.flatMapAfter.{w, w'}]](#manual-Std___IterM___flatMapAfter)
  {α β α₂ γ : Type w}
  {m : Type w → Type w'} [[Monad]](#manual-Monad___mk) m]
  [[Iterator]](#manual-Std___Iterator___mk) α m β] [[Iterator]](#manual-Std___Iterator___mk) α₂ m γ]
  (f : β → [IterM]](#manual-Std___IterM___mk) m γ) (it₁ : [IterM]](#manual-Std___IterM___mk) m β)
  (it₂ : [Option]](#manual-Option___none) ([IterM]](#manual-Std___IterM___mk) m γ)) : [IterM]](#manual-Std___IterM___mk) m γ
```

Let `it₁` and `it₂` be iterators and `f` a function mapping `it₁`'s outputs to iterators
of the same type as `it₂`. Then `it₁.[flatMapAfter]](#manual-Std___IterM___flatMapAfter) f it₂` first goes over `it₂` and then over
`it₁.[flatMap]](#manual-Std___IterM___flatMap) f it₂`, emitting all their values.

The main purpose of this combinator is to represent the intermediate state of a `flatMap` iterator
that is currently iterating over one of the inner iterators.

**Marble diagram:**

```
it₁                            --b      c    --d -⊥
it₂                      a1-a2⊥
f b                               b1-b2⊥
f c                                      c1-c2⊥
f d                                             ⊥
it.flatMapAfter  f it₂   a1-a2----b1-b2--c1-c2----⊥
```

**Termination properties:**

- `[Finite]](#manual-Std___Iterators___Finite___mk)` instance: only if `it₁`, `it₂` and the inner iterators are finite
- `[Productive]](#manual-Std___Iterators___Productive___mk)` instance: only if `it₁` is finite and `it₂` and the inner iterators are productive

For certain functions `f`, the resulting iterator will be finite (or productive) even though
no `[Finite]](#manual-Std___Iterators___Finite___mk)` (or `[Productive]](#manual-Std___Iterators___Productive___mk)`) instance is provided out of the box. For example, if the outer
iterator is productive and the inner
iterators are productive *and provably never empty*, then the resulting iterator is also productive.

**Performance:**

This combinator incurs an additional O(1) cost with each output of `it₁`, `it₂` or an internal
iterator.

For each value emitted by the outer iterator `it₁`, this combinator calls `f`.

def

```lean
[Std.IterM.flatMapAfterM.{w, w'}]](#manual-Std___IterM___flatMapAfterM) {α β α₂ γ : Type w}
  {m : Type w → Type w'} [[Monad]](#manual-Monad___mk) m] [MonadAttach m] [[Iterator]](#manual-Std___Iterator___mk) α m β]
  [[Iterator]](#manual-Std___Iterator___mk) α₂ m γ] (f : β → m ([IterM]](#manual-Std___IterM___mk) m γ)) (it₁ : [IterM]](#manual-Std___IterM___mk) m β)
  (it₂ : [Option]](#manual-Option___none) ([IterM]](#manual-Std___IterM___mk) m γ)) : [IterM]](#manual-Std___IterM___mk) m γ



[Std.IterM.flatMapAfterM.{w, w'}]](#manual-Std___IterM___flatMapAfterM)
  {α β α₂ γ : Type w}
  {m : Type w → Type w'} [[Monad]](#manual-Monad___mk) m]
  [MonadAttach m] [[Iterator]](#manual-Std___Iterator___mk) α m β]
  [[Iterator]](#manual-Std___Iterator___mk) α₂ m γ]
  (f : β → m ([IterM]](#manual-Std___IterM___mk) m γ))
  (it₁ : [IterM]](#manual-Std___IterM___mk) m β)
  (it₂ : [Option]](#manual-Option___none) ([IterM]](#manual-Std___IterM___mk) m γ)) : [IterM]](#manual-Std___IterM___mk) m γ
```

Let `it₁` and `it₂` be iterators and `f` a monadic function mapping `it₁`'s outputs to iterators
of the same type as `it₂`. Then `it₁.[flatMapAfterM]](#manual-Std___IterM___flatMapAfterM) f it₂` first goes over `it₂` and then over
`it₁.flatMap f it₂`, emitting all their values.

The main purpose of this combinator is to represent the intermediate state of a `flatMap` iterator
that is currently iterating over one of the inner iterators.

**Marble diagram (without monadic effects):**

```
it₁                            --b      c    --d -⊥
it₂                      a1-a2⊥
f b                               b1-b2⊥
f c                                      c1-c2⊥
f d                                             ⊥
it.flatMapAfterM f it₂   a1-a2----b1-b2--c1-c2----⊥
```

**Termination properties:**

- `[Finite]](#manual-Std___Iterators___Finite___mk)` instance: only if `it₁`, `it₂` and the inner iterators are finite
- `[Productive]](#manual-Std___Iterators___Productive___mk)` instance: only if `it₁` is finite and `it₂` and the inner iterators are productive

For certain functions `f`, the resulting iterator will be finite (or productive) even though
no `[Finite]](#manual-Std___Iterators___Finite___mk)` (or `[Productive]](#manual-Std___Iterators___Productive___mk)`) instance is provided out of the box. For example, if the outer
iterator is productive and the inner
iterators are productive *and provably never empty*, then the resulting iterator is also productive.

**Performance:**

This combinator incurs an additional O(1) cost with each output of `it₁`, `it₂` or an internal
iterator.

For each value emitted by the outer iterator `it₁`, this combinator calls `f`.

def

```lean
[Std.IterM.filter.{w, w'}]](#manual-Std___IterM___filter) {α β : Type w} {m : Type w → Type w'}
  [[Iterator]](#manual-Std___Iterator___mk) α m β] [[Monad]](#manual-Monad___mk) m] (f : β → [Bool]](#manual-Bool___false)) (it : [IterM]](#manual-Std___IterM___mk) m β) : [IterM]](#manual-Std___IterM___mk) m β



[Std.IterM.filter.{w, w'}]](#manual-Std___IterM___filter) {α β : Type w}
  {m : Type w → Type w'} [[Iterator]](#manual-Std___Iterator___mk) α m β]
  [[Monad]](#manual-Monad___mk) m] (f : β → [Bool]](#manual-Bool___false))
  (it : [IterM]](#manual-Std___IterM___mk) m β) : [IterM]](#manual-Std___IterM___mk) m β
```

If `it` is an iterator, then `it.[filter]](#manual-Std___IterM___filter) f` is another iterator that applies a
predicate `f` to all values emitted by `it` and emits them only if they are accepted by `f`.

In situations where `f` is monadic, use `filterM` instead.

**Marble diagram (without monadic effects):**

```lean
it ---a--b--c--d-e--⊥
it.filter ---a-----c-------⊥
```

(given that `f a = f c = true` and `f b = f d = d e = false`)

**Termination properties:**

- `[Finite]](#manual-Std___Iterators___Finite___mk)` instance: only if `it` is finite
- `[Productive]](#manual-Std___Iterators___Productive___mk)` instance: only if `it` is finite`

For certain mapping functions `f`, the resulting iterator will be productive even though
no `[Productive]](#manual-Std___Iterators___Productive___mk)` instance is provided. For example, if `f` always returns `[True]](#manual-True___intro)`, the resulting
iterator will be productive as long as `it` is. In such situations, the missing instance needs to
be proved manually.

**Performance:**

For each value emitted by the base iterator `it`, this combinator calls `f` and matches on the
returned value.

def

```lean
[Std.IterM.filterM.{w, w', w''}]](#manual-Std___IterM___filterM) {α β : Type w} {m : Type w → Type w'}
  {n : Type w → Type w''} [[Iterator]](#manual-Std___Iterator___mk) α m β] [[Monad]](#manual-Monad___mk) n] [MonadAttach n]
  [[MonadLiftT]](#manual-MonadLiftT___mk) m n] (f : β → n ([ULift]](#manual-ULift___up) [Bool]](#manual-Bool___false))) (it : [IterM]](#manual-Std___IterM___mk) m β) : [IterM]](#manual-Std___IterM___mk) n β



[Std.IterM.filterM.{w, w', w''}]](#manual-Std___IterM___filterM)
  {α β : Type w} {m : Type w → Type w'}
  {n : Type w → Type w''} [[Iterator]](#manual-Std___Iterator___mk) α m β]
  [[Monad]](#manual-Monad___mk) n] [MonadAttach n]
  [[MonadLiftT]](#manual-MonadLiftT___mk) m n]
  (f : β → n ([ULift]](#manual-ULift___up) [Bool]](#manual-Bool___false)))
  (it : [IterM]](#manual-Std___IterM___mk) m β) : [IterM]](#manual-Std___IterM___mk) n β
```

If `it` is an iterator, then `it.[filterM]](#manual-Std___IterM___filterM) f` is another iterator that applies a monadic
predicate `f` to all values emitted by `it` and emits them only if they are accepted by `f`.

The base iterator `it` being monadic in `m`, `f` can return values in any monad `n` for which a
`[MonadLiftT]](#manual-MonadLiftT___mk) m n` instance is available.

If `f` is pure, then the simpler variant `it.[filter]](#manual-Std___IterM___filter)` can be used instead.

**Marble diagram (without monadic effects):**

```lean
it ---a--b--c--d-e--⊥
it.filterM ---a-----c-------⊥
```

(given that `f a = f c = pure true` and `f b = f d = d e = pure false`)

**Termination properties:**

- `[Finite]](#manual-Std___Iterators___Finite___mk)` instance: only if `it` is finite
- `[Productive]](#manual-Std___Iterators___Productive___mk)` instance: only if `it` is finite`

For certain mapping functions `f`, the resulting iterator will be finite (or productive) even though
no `[Finite]](#manual-Std___Iterators___Finite___mk)` (or `[Productive]](#manual-Std___Iterators___Productive___mk)`) instance is provided. For example, if `f` is an `[ExceptT]](#manual-ExceptT)` monad and
will always fail, then `it.filterWithPostcondition` will be finite -- and productive -- even if `it`
isn't. In such cases, the termination proof needs to be done manually.

**Performance:**

For each value emitted by the base iterator `it`, this combinator calls `f`.

def

```lean
[Std.IterM.filterWithPostcondition.{w, w', w''}]](#manual-Std___IterM___filterWithPostcondition) {α β : Type w}
  {m : Type w → Type w'} {n : Type w → Type w''} [[Monad]](#manual-Monad___mk) n]
  [[MonadLiftT]](#manual-MonadLiftT___mk) m n] [[Iterator]](#manual-Std___Iterator___mk) α m β]
  (f : β → [PostconditionT](https://lean-lang.org/doc/reference/latest/Iterators/Reasoning-About-Iterators/#Std___Iterators___PostconditionT___mk) n ([ULift]](#manual-ULift___up) [Bool]](#manual-Bool___false))) (it : [IterM]](#manual-Std___IterM___mk) m β) : [IterM]](#manual-Std___IterM___mk) n β



[Std.IterM.filterWithPostcondition.{w, w',
    w''}]](#manual-Std___IterM___filterWithPostcondition)
  {α β : Type w} {m : Type w → Type w'}
  {n : Type w → Type w''} [[Monad]](#manual-Monad___mk) n]
  [[MonadLiftT]](#manual-MonadLiftT___mk) m n] [[Iterator]](#manual-Std___Iterator___mk) α m β]
  (f : β → [PostconditionT](https://lean-lang.org/doc/reference/latest/Iterators/Reasoning-About-Iterators/#Std___Iterators___PostconditionT___mk) n ([ULift]](#manual-ULift___up) [Bool]](#manual-Bool___false)))
  (it : [IterM]](#manual-Std___IterM___mk) m β) : [IterM]](#manual-Std___IterM___mk) n β
```

*Note: This is a very general combinator that requires an advanced understanding of monads,
dependent types and termination proofs. The variants `filter` and `filterM` are easier to use and
sufficient for most use cases.*

If `it` is an iterator, then `it.[filterWithPostcondition]](#manual-Std___IterM___filterWithPostcondition) f` is another iterator that applies a monadic
predicate `f` to all values emitted by `it` and emits them only if they are accepted by `f`.

`f` is expected to return `[PostconditionT](https://lean-lang.org/doc/reference/latest/Iterators/Reasoning-About-Iterators/#Std___Iterators___PostconditionT___mk) n ([ULift]](#manual-ULift___up) [Bool]](#manual-Bool___false))`. The base iterator `it` being monadic in
`m`, `n` can be different from `m`, but `it.[filterWithPostcondition]](#manual-Std___IterM___filterWithPostcondition) f` expects a `[MonadLiftT]](#manual-MonadLiftT___mk) m n`
instance. The `[PostconditionT](https://lean-lang.org/doc/reference/latest/Iterators/Reasoning-About-Iterators/#Std___Iterators___PostconditionT___mk)` transformer allows the caller to intrinsically prove properties about
`f`'s return value in the monad `n`, enabling termination proofs depending on the specific behavior
of `f`.

**Marble diagram (without monadic effects):**

```lean
it ---a--b--c--d-e--⊥
it.filterWithPostcondition ---a-----c-------⊥
```

(given that `f a = f c = pure true` and `f b = f d = d e = pure false`)

**Termination properties:**

- `[Finite]](#manual-Std___Iterators___Finite___mk)` instance: only if `it` is finite
- `[Productive]](#manual-Std___Iterators___Productive___mk)` instance: only if `it` is finite`

For certain mapping functions `f`, the resulting iterator will be finite (or productive) even though
no `[Finite]](#manual-Std___Iterators___Finite___mk)` (or `[Productive]](#manual-Std___Iterators___Productive___mk)`) instance is provided. For example, if `f` is an `[ExceptT]](#manual-ExceptT)` monad and
will always fail, then `it.filterWithPostcondition` will be finite -- and productive -- even if `it`
isn't.

In such situations, the missing instances can be proved manually if the postcondition bundled in
the `[PostconditionT](https://lean-lang.org/doc/reference/latest/Iterators/Reasoning-About-Iterators/#Std___Iterators___PostconditionT___mk) n` monad is strong enough. In the given example, a suitable postcondition might
be `fun _ => [False]](#manual-False)`.

**Performance:**

For each value emitted by the base iterator `it`, this combinator calls `f`.

def

```lean
[Std.IterM.filterMap.{w, w'}]](#manual-Std___IterM___filterMap) {α β γ : Type w} {m : Type w → Type w'}
  [[Iterator]](#manual-Std___Iterator___mk) α m β] [[Monad]](#manual-Monad___mk) m] (f : β → [Option]](#manual-Option___none) γ) (it : [IterM]](#manual-Std___IterM___mk) m β) :
  [IterM]](#manual-Std___IterM___mk) m γ



[Std.IterM.filterMap.{w, w'}]](#manual-Std___IterM___filterMap)
  {α β γ : Type w} {m : Type w → Type w'}
  [[Iterator]](#manual-Std___Iterator___mk) α m β] [[Monad]](#manual-Monad___mk) m]
  (f : β → [Option]](#manual-Option___none) γ) (it : [IterM]](#manual-Std___IterM___mk) m β) :
  [IterM]](#manual-Std___IterM___mk) m γ
```

If `it` is an iterator, then `it.[filterMap]](#manual-Std___IterM___filterMap) f` is another iterator that applies a function `f` to all
values emitted by `it`. `f` is expected to return an `[Option]](#manual-Option___none)`. If it returns `[none]](#manual-Option___none)`, then nothing is
emitted; if it returns `[some]](#manual-Option___none) x`, then `x` is emitted.

In situations where `f` is monadic, use `filterMapM` instead.

**Marble diagram:**

```lean
it ---a --b--c --d-e--⊥
it.filterMap ---a'-----c'-------⊥
```

(given that `f a = [some]](#manual-Option___none) a'`, `f c = c'` and `f b = f d = d e = none`)

**Termination properties:**

- `[Finite]](#manual-Std___Iterators___Finite___mk)` instance: only if `it` is finite
- `[Productive]](#manual-Std___Iterators___Productive___mk)` instance: only if `it` is finite`

For certain mapping functions `f`, the resulting iterator will be productive even though
no `[Productive]](#manual-Std___Iterators___Productive___mk)` instance is provided. For example, if `f` never returns `[none]](#manual-Option___none)`, then
this combinator will preserve productiveness. In such situations, the missing instance needs to
be proved manually.

**Performance:**

For each value emitted by the base iterator `it`, this combinator calls `f` and matches on the
returned `[Option]](#manual-Option___none)` value.

def

```lean
[Std.IterM.filterMapM.{w, w', w''}]](#manual-Std___IterM___filterMapM) {α β γ : Type w}
  {m : Type w → Type w'} {n : Type w → Type w''} [[Iterator]](#manual-Std___Iterator___mk) α m β]
  [[Monad]](#manual-Monad___mk) n] [MonadAttach n] [[MonadLiftT]](#manual-MonadLiftT___mk) m n] (f : β → n ([Option]](#manual-Option___none) γ))
  (it : [IterM]](#manual-Std___IterM___mk) m β) : [IterM]](#manual-Std___IterM___mk) n γ



[Std.IterM.filterMapM.{w, w', w''}]](#manual-Std___IterM___filterMapM)
  {α β γ : Type w} {m : Type w → Type w'}
  {n : Type w → Type w''} [[Iterator]](#manual-Std___Iterator___mk) α m β]
  [[Monad]](#manual-Monad___mk) n] [MonadAttach n]
  [[MonadLiftT]](#manual-MonadLiftT___mk) m n] (f : β → n ([Option]](#manual-Option___none) γ))
  (it : [IterM]](#manual-Std___IterM___mk) m β) : [IterM]](#manual-Std___IterM___mk) n γ
```

If `it` is an iterator, then `it.[filterMapM]](#manual-Std___IterM___filterMapM) f` is another iterator that applies a monadic
function `f` to all values emitted by `it`. `f` is expected to return an `[Option]](#manual-Option___none)` inside the monad.
If `f` returns `[none]](#manual-Option___none)`, then nothing is emitted; if it returns `[some]](#manual-Option___none) x`, then `x` is emitted.

The base iterator `it` being monadic in `m`, `f` can return values in any monad `n` for which a
`[MonadLiftT]](#manual-MonadLiftT___mk) m n` instance is available.

If `f` is pure, then the simpler variant `it.[filterMap]](#manual-Std___IterM___filterMap)` can be used instead.

**Marble diagram (without monadic effects):**

```lean
it ---a --b--c --d-e--⊥
it.filterMapM ---a'-----c'-------⊥
```

(given that `f a = pure (some a)'`, `f c = [pure]](#manual-Pure___mk) ([some]](#manual-Option___none) c')` and `f b = f d = d e = pure none`)

**Termination properties:**

- `[Finite]](#manual-Std___Iterators___Finite___mk)` instance: only if `it` is finite
- `[Productive]](#manual-Std___Iterators___Productive___mk)` instance: only if `it` is finite`

For certain mapping functions `f`, the resulting iterator will be finite (or productive) even though
no `[Finite]](#manual-Std___Iterators___Finite___mk)` (or `[Productive]](#manual-Std___Iterators___Productive___mk)`) instance is provided. For example, if `f` never returns `[none]](#manual-Option___none)`, then
this combinator will preserve productiveness. If `f` is an `[ExceptT]](#manual-ExceptT)` monad and will always fail,
then `it.filterMapM` will be finite even if `it` isn't. In such cases, the termination proof needs
to be done manually.

**Performance:**

For each value emitted by the base iterator `it`, this combinator calls `f` and matches on the
returned `[Option]](#manual-Option___none)` value.

def

```lean
[Std.IterM.filterMapWithPostcondition.{w, w', w''}]](#manual-Std___IterM___filterMapWithPostcondition) {α β γ : Type w}
  {m : Type w → Type w'} {n : Type w → Type w''} [[MonadLiftT]](#manual-MonadLiftT___mk) m n]
  [[Iterator]](#manual-Std___Iterator___mk) α m β] (f : β → [PostconditionT](https://lean-lang.org/doc/reference/latest/Iterators/Reasoning-About-Iterators/#Std___Iterators___PostconditionT___mk) n ([Option]](#manual-Option___none) γ))
  (it : [IterM]](#manual-Std___IterM___mk) m β) : [IterM]](#manual-Std___IterM___mk) n γ



[Std.IterM.filterMapWithPostcondition.{w,
    w', w''}]](#manual-Std___IterM___filterMapWithPostcondition)
  {α β γ : Type w} {m : Type w → Type w'}
  {n : Type w → Type w''} [[MonadLiftT]](#manual-MonadLiftT___mk) m n]
  [[Iterator]](#manual-Std___Iterator___mk) α m β]
  (f : β → [PostconditionT](https://lean-lang.org/doc/reference/latest/Iterators/Reasoning-About-Iterators/#Std___Iterators___PostconditionT___mk) n ([Option]](#manual-Option___none) γ))
  (it : [IterM]](#manual-Std___IterM___mk) m β) : [IterM]](#manual-Std___IterM___mk) n γ
```

*Note: This is a very general combinator that requires an advanced understanding of monads,
dependent types and termination proofs. The variants `filterMap` and `filterMapM` are easier to use
and sufficient for most use cases.*

If `it` is an iterator, then `it.[filterMapWithPostcondition]](#manual-Std___IterM___filterMapWithPostcondition) f` is another iterator that applies a
monadic function `f` to all values emitted by `it`. `f` is expected to return an `[Option]](#manual-Option___none)` inside the
monad. If `f` returns `[none]](#manual-Option___none)`, then nothing is emitted; if it returns `[some]](#manual-Option___none) x`, then `x` is emitted.

`f` is expected to return `[PostconditionT](https://lean-lang.org/doc/reference/latest/Iterators/Reasoning-About-Iterators/#Std___Iterators___PostconditionT___mk) n ([Option]](#manual-Option___none) _)`. The base iterator `it` being monadic in
`m`, `n` can be different from `m`, but `it.[filterMapWithPostcondition]](#manual-Std___IterM___filterMapWithPostcondition) f` expects a `[MonadLiftT]](#manual-MonadLiftT___mk) m n`
instance. The `[PostconditionT](https://lean-lang.org/doc/reference/latest/Iterators/Reasoning-About-Iterators/#Std___Iterators___PostconditionT___mk)` transformer allows the caller to intrinsically prove properties about
`f`'s return value in the monad `n`, enabling termination proofs depending on the specific behavior
of `f`.

**Marble diagram (without monadic effects):**

```lean
it ---a --b--c --d-e--⊥
it.filterMapWithPostcondition ---a'-----c'-------⊥
```

(given that `f a = pure (some a)'`, `f c = [pure]](#manual-Pure___mk) ([some]](#manual-Option___none) c')` and `f b = f d = d e = pure none`)

**Termination properties:**

- `[Finite]](#manual-Std___Iterators___Finite___mk)` instance: only if `it` is finite
- `[Productive]](#manual-Std___Iterators___Productive___mk)` instance: only if `it` is finite

For certain mapping functions `f`, the resulting iterator will be finite (or productive) even though
no `[Finite]](#manual-Std___Iterators___Finite___mk)` (or `[Productive]](#manual-Std___Iterators___Productive___mk)`) instance is provided. For example, if `f` never returns `[none]](#manual-Option___none)`, then
this combinator will preserve productiveness. If `f` is an `[ExceptT]](#manual-ExceptT)` monad and will always fail,
then `it.filterMapWithPostcondition` will be finite even if `it` isn't. In the first case, consider
using the `map`/`mapM`/`mapWithPostcondition` combinators instead, which provide more instances out of
the box.

In such situations, the missing instances can be proved manually if the postcondition bundled in
the `[PostconditionT](https://lean-lang.org/doc/reference/latest/Iterators/Reasoning-About-Iterators/#Std___Iterators___PostconditionT___mk) n` monad is strong enough. If `f` always returns `[some]](#manual-Option___none) _`, a suitable
postcondition is `fun x => x.isSome`; if `f` always fails, a suitable postcondition might be
`fun _ => [False]](#manual-False)`.

**Performance:**

For each value emitted by the base iterator `it`, this combinator calls `f` and matches on the
returned `[Option]](#manual-Option___none)` value.

def

```lean
[Std.IterM.zip.{w, w'}]](#manual-Std___IterM___zip) {m : Type w → Type w'} {α₁ β₁ : Type w}
  [[Iterator]](#manual-Std___Iterator___mk) α₁ m β₁] {α₂ β₂ : Type w} (left : [IterM]](#manual-Std___IterM___mk) m β₁)
  (right : [IterM]](#manual-Std___IterM___mk) m β₂) : [IterM]](#manual-Std___IterM___mk) m [(]](#manual-Prod___mk)β₁ [×]](#manual-Prod___mk) β₂[)]](#manual-Prod___mk)



[Std.IterM.zip.{w, w'}]](#manual-Std___IterM___zip)
  {m : Type w → Type w'} {α₁ β₁ : Type w}
  [[Iterator]](#manual-Std___Iterator___mk) α₁ m β₁] {α₂ β₂ : Type w}
  (left : [IterM]](#manual-Std___IterM___mk) m β₁)
  (right : [IterM]](#manual-Std___IterM___mk) m β₂) : [IterM]](#manual-Std___IterM___mk) m [(]](#manual-Prod___mk)β₁ [×]](#manual-Prod___mk) β₂[)]](#manual-Prod___mk)
```

Given two iterators `left` and `right`, `left.[zip]](#manual-Std___IterM___zip) right` is an iterator that yields pairs of
outputs of `left` and `right`. When one of them terminates,
the `zip` iterator will also terminate.

**Marble diagram:**

```lean
left --a ---b --c
right --x --y --⊥
left.zip right -----(a, x)------(b, y)-----⊥
```

**Termination properties:**

- `[Finite]](#manual-Std___Iterators___Finite___mk)` instance: only if either `left` or `right` is finite and the other is productive
- `[Productive]](#manual-Std___Iterators___Productive___mk)` instance: only if `left` and `right` are productive

There are situations where `left.[zip]](#manual-Std___IterM___zip) right` is finite (or productive) but none of the instances
above applies. For example, if the computation happens in an `[Except]](#manual-Except___error)` monad and `left` immediately
fails when calling `step`, then `left.[zip]](#manual-Std___IterM___zip) right` will also do so. In such a case, the `[Finite]](#manual-Std___Iterators___Finite___mk)`
(or `[Productive]](#manual-Std___Iterators___Productive___mk)`) instance needs to be proved manually.

**Performance:**

This combinator incurs an additional O(1) cost with each step taken by `left` or `right`.

Right now, the compiler does not unbox the internal state, leading to worse performance than
possible.

def

```lean
[Std.IterM.attachWith.{w, w'}]](#manual-Std___IterM___attachWith) {α β : Type w} {m : Type w → Type w'}
  [[Monad]](#manual-Monad___mk) m] [[Iterator]](#manual-Std___Iterator___mk) α m β] (it : [IterM]](#manual-Std___IterM___mk) m β) (P : β → Prop)
  (h : ∀ (out : β), it.IsPlausibleIndirectOutput out → P out) :
  [IterM]](#manual-Std___IterM___mk) m [{]](#manual-Subtype___mk) out [//]](#manual-Subtype___mk) P out [}]](#manual-Subtype___mk)



[Std.IterM.attachWith.{w, w'}]](#manual-Std___IterM___attachWith)
  {α β : Type w} {m : Type w → Type w'}
  [[Monad]](#manual-Monad___mk) m] [[Iterator]](#manual-Std___Iterator___mk) α m β]
  (it : [IterM]](#manual-Std___IterM___mk) m β) (P : β → Prop)
  (h :
    ∀ (out : β),
      it.IsPlausibleIndirectOutput out →
        P out) :
  [IterM]](#manual-Std___IterM___mk) m [{]](#manual-Subtype___mk) out [//]](#manual-Subtype___mk) P out [}]](#manual-Subtype___mk)
```

“Attaches” individual proofs to an iterator of values that satisfy a predicate `P`, returning an
iterator with values in the corresponding subtype `{ x // P x }`.

**Termination properties:**

- `[Finite]](#manual-Std___Iterators___Finite___mk)` instance: only if the base iterator is finite
- `[Productive]](#manual-Std___Iterators___Productive___mk)` instance: only if the base iterator is productive

---



## Iterators — 22.5. Reasoning About Iterators {#manual-iterators-225-reasoning-about-iterators}

> 📄 Source: https://lean-lang.org/doc/reference/latest/Iterators/Reasoning-About-Iterators/

### 22.5.1. Reasoning About Consumers {#manual-The-Lean-Language-Reference--Iterators--Reasoning-About-Iterators--Reasoning-About-Consumers}

The iterator library provides a large number of useful lemmas.
Most theorems about finite iterators can be proven by rewriting the statement to one about lists, using the fact that the correspondence between iterator combinators and corresponding list operations has already been proved.
In practice, many of these theorems are already registered as `[simp]](#manual-simp)` lemmas.

The lemmas have a very predictable naming system, and many are in the [default simp set]](#manual---tech-term-default-simp-set).
Some of the most important include:

- Consumer lemmas such as `Iter.all_toList`, `Iter.any_toList`, and `Iter.foldl_toList` that introduce lists as a model.
- Simplification lemmas such as `Iter.toList_map` that `Iter.toList_filter` push the list model “inwards” in the goal.
- Producer lemmas such as `List.toList_iter` and `Array.toList_iter` that replace a producer with a list model, removing iterators from the goal entirely.

The latter two categories are typically automatic with `[simp]](#manual-simp)`.

**Example: Reasoning via Lists**

Every element returned by an iterator that multiplies the numbers consumed some other iterator by two is even.
To prove this statement, `Iter.all_toList`, `Iter.toList_map`, and `Array.toList_iter` are used to replace the statement about iterators with one about lists, after which `[simp]](#manual-simp)` discharges the goal:

```lean
example (l : [Array]](#manual-Array___mk) [Nat]](#manual-Nat___zero)) :
(l.[iter]](#manual-Array___iter).[map]](#manual-Std___Iter___map) (· * 2)).[all]](#manual-Std___Iter___all) (· % 2 = 0) := byl:[Array]](#manual-Array___mk) [Nat]](#manual-Nat___zero)⊢ [Iter.all]](#manual-Std___Iter___all) (fun x => [decide]](#manual-Decidable___decide) [(]](#manual-Eq___refl)x [%]](#manual-HMod___mk) 2 [=]](#manual-Eq___refl) 0[)]](#manual-Eq___refl)) ([Iter.map]](#manual-Std___Iter___map) (fun x => x [*]](#manual-HMul___mk) 2) l.[iter]](#manual-Array___iter)) [=]](#manual-Eq___refl) [true]](#manual-Bool___false)
[rw]](#manual-rw) [← Iter.all_toListl:[Array]](#manual-Array___mk) [Nat]](#manual-Nat___zero)⊢ (([Iter.map]](#manual-Std___Iter___map) (fun x => x [*]](#manual-HMul___mk) 2) l.[iter]](#manual-Array___iter)).[toList]](#manual-Std___Iter___toList).[all]](#manual-List___all) fun x => [decide]](#manual-Decidable___decide) [(]](#manual-Eq___refl)x [%]](#manual-HMod___mk) 2 [=]](#manual-Eq___refl) 0[)]](#manual-Eq___refl)) [=]](#manual-Eq___refl) [true]](#manual-Bool___false)]l:[Array]](#manual-Array___mk) [Nat]](#manual-Nat___zero)⊢ (([Iter.map]](#manual-Std___Iter___map) (fun x => x [*]](#manual-HMul___mk) 2) l.[iter]](#manual-Array___iter)).[toList]](#manual-Std___Iter___toList).[all]](#manual-List___all) fun x => [decide]](#manual-Decidable___decide) [(]](#manual-Eq___refl)x [%]](#manual-HMod___mk) 2 [=]](#manual-Eq___refl) 0[)]](#manual-Eq___refl)) [=]](#manual-Eq___refl) [true]](#manual-Bool___false)
[rw]](#manual-rw) [Iter.toList_mapl:[Array]](#manual-Array___mk) [Nat]](#manual-Nat___zero)⊢ (([List.map]](#manual-List___map) (fun x => x [*]](#manual-HMul___mk) 2) l.[iter]](#manual-Array___iter).[toList]](#manual-Std___Iter___toList)).[all]](#manual-List___all) fun x => [decide]](#manual-Decidable___decide) [(]](#manual-Eq___refl)x [%]](#manual-HMod___mk) 2 [=]](#manual-Eq___refl) 0[)]](#manual-Eq___refl)) [=]](#manual-Eq___refl) [true]](#manual-Bool___false)]l:[Array]](#manual-Array___mk) [Nat]](#manual-Nat___zero)⊢ (([List.map]](#manual-List___map) (fun x => x [*]](#manual-HMul___mk) 2) l.[iter]](#manual-Array___iter).[toList]](#manual-Std___Iter___toList)).[all]](#manual-List___all) fun x => [decide]](#manual-Decidable___decide) [(]](#manual-Eq___refl)x [%]](#manual-HMod___mk) 2 [=]](#manual-Eq___refl) 0[)]](#manual-Eq___refl)) [=]](#manual-Eq___refl) [true]](#manual-Bool___false)
[rw]](#manual-rw) [Array.toList_iterl:[Array]](#manual-Array___mk) [Nat]](#manual-Nat___zero)⊢ (([List.map]](#manual-List___map) (fun x => x [*]](#manual-HMul___mk) 2) l.toList).[all]](#manual-List___all) fun x => [decide]](#manual-Decidable___decide) [(]](#manual-Eq___refl)x [%]](#manual-HMod___mk) 2 [=]](#manual-Eq___refl) 0[)]](#manual-Eq___refl)) [=]](#manual-Eq___refl) [true]](#manual-Bool___false)]l:[Array]](#manual-Array___mk) [Nat]](#manual-Nat___zero)⊢ (([List.map]](#manual-List___map) (fun x => x [*]](#manual-HMul___mk) 2) l.toList).[all]](#manual-List___all) fun x => [decide]](#manual-Decidable___decide) [(]](#manual-Eq___refl)x [%]](#manual-HMod___mk) 2 [=]](#manual-Eq___refl) 0[)]](#manual-Eq___refl)) [=]](#manual-Eq___refl) [true]](#manual-Bool___false)
[simp]](#manual-simp)All goals completed! 🐙
```

In fact, because most of the needed lemmas are in the [default simp set]](#manual---tech-term-default-simp-set), the proof can be quite short:

```lean
example (l : [Array]](#manual-Array___mk) [Nat]](#manual-Nat___zero)) :
(l.[iter]](#manual-Array___iter).[map]](#manual-Std___Iter___map) (· * 2)).[all]](#manual-Std___Iter___all) (· % 2 = 0) := byl:[Array]](#manual-Array___mk) [Nat]](#manual-Nat___zero)⊢ [Iter.all]](#manual-Std___Iter___all) (fun x => [decide]](#manual-Decidable___decide) [(]](#manual-Eq___refl)x [%]](#manual-HMod___mk) 2 [=]](#manual-Eq___refl) 0[)]](#manual-Eq___refl)) ([Iter.map]](#manual-Std___Iter___map) (fun x => x [*]](#manual-HMul___mk) 2) l.[iter]](#manual-Array___iter)) [=]](#manual-Eq___refl) [true]](#manual-Bool___false)
[simp]](#manual-simp) [← Iter.all_toList]All goals completed! 🐙
```

### 22.5.2. Stepwise Reasoning {#manual-The-Lean-Language-Reference--Iterators--Reasoning-About-Iterators--Stepwise-Reasoning}

When there are not enough lemmas to prove a property by rewriting to a list model, it can be necessary to prove things about iterators by reasoning directly about their step functions.
The induction principles in this section are useful for stepwise reasoning.

def

```lean
[Std.Iter.inductSkips.{x, u_1}]](#manual-Std___Iter___inductSkips) {α β : Type u_1} [[Iterator]](#manual-Std___Iterator___mk) α [Id]](#manual-Id) β]
  [[Productive]](#manual-Std___Iterators___Productive___mk) α [Id]](#manual-Id)] (motive : [Iter]](#manual-Std___Iter___mk) β → Sort x)
  (step :
    (it : [Iter]](#manual-Std___Iter___mk) β) →
      ({it' : [Iter]](#manual-Std___Iter___mk) β} →
          it.IsPlausibleStep ([IterStep.skip]](#manual-Std___IterStep___yield) it') → motive it') →
        motive it)
  (it : [Iter]](#manual-Std___Iter___mk) β) : motive it



[Std.Iter.inductSkips.{x, u_1}]](#manual-Std___Iter___inductSkips)
  {α β : Type u_1} [[Iterator]](#manual-Std___Iterator___mk) α [Id]](#manual-Id) β]
  [[Productive]](#manual-Std___Iterators___Productive___mk) α [Id]](#manual-Id)]
  (motive : [Iter]](#manual-Std___Iter___mk) β → Sort x)
  (step :
    (it : [Iter]](#manual-Std___Iter___mk) β) →
      ({it' : [Iter]](#manual-Std___Iter___mk) β} →
          it.IsPlausibleStep
              ([IterStep.skip]](#manual-Std___IterStep___yield) it') →
            motive it') →
        motive it)
  (it : [Iter]](#manual-Std___Iter___mk) β) : motive it
```

Induction principle for productive iterators: One can define a function `f` that maps every
iterator `it` to an element of `motive it` by defining `f it` in terms of the values of `f` on
the plausible skip successors of `it'.

def

```lean
[Std.IterM.inductSkips.{x, u_1, u_2}]](#manual-Std___IterM___inductSkips) {α : Type u_1}
  {m : Type u_1 → Type u_2} {β : Type u_1} [[Iterator]](#manual-Std___Iterator___mk) α m β]
  [[Productive]](#manual-Std___Iterators___Productive___mk) α m] (motive : [IterM]](#manual-Std___IterM___mk) m β → Sort x)
  (step :
    (it : [IterM]](#manual-Std___IterM___mk) m β) →
      ({it' : [IterM]](#manual-Std___IterM___mk) m β} →
          it.IsPlausibleStep ([IterStep.skip]](#manual-Std___IterStep___yield) it') → motive it') →
        motive it)
  (it : [IterM]](#manual-Std___IterM___mk) m β) : motive it



[Std.IterM.inductSkips.{x, u_1, u_2}]](#manual-Std___IterM___inductSkips)
  {α : Type u_1} {m : Type u_1 → Type u_2}
  {β : Type u_1} [[Iterator]](#manual-Std___Iterator___mk) α m β]
  [[Productive]](#manual-Std___Iterators___Productive___mk) α m]
  (motive : [IterM]](#manual-Std___IterM___mk) m β → Sort x)
  (step :
    (it : [IterM]](#manual-Std___IterM___mk) m β) →
      ({it' : [IterM]](#manual-Std___IterM___mk) m β} →
          it.IsPlausibleStep
              ([IterStep.skip]](#manual-Std___IterStep___yield) it') →
            motive it') →
        motive it)
  (it : [IterM]](#manual-Std___IterM___mk) m β) : motive it
```

Induction principle for productive monadic iterators: One can define a function `f` that maps every
iterator `it` to an element of `motive it` by defining `f it` in terms of the values of `f` on
the plausible skip successors of `it'.

def

```lean
[Std.Iter.inductSteps.{x, u_1}]](#manual-Std___Iter___inductSteps) {α β : Type u_1} [[Iterator]](#manual-Std___Iterator___mk) α [Id]](#manual-Id) β]
  [[Finite]](#manual-Std___Iterators___Finite___mk) α [Id]](#manual-Id)] (motive : [Iter]](#manual-Std___Iter___mk) β → Sort x)
  (step :
    (it : [Iter]](#manual-Std___Iter___mk) β) →
      ({it' : [Iter]](#manual-Std___Iter___mk) β} →
          {out : β} →
            it.IsPlausibleStep ([IterStep.yield]](#manual-Std___IterStep___yield) it' out) → motive it') →
        ({it' : [Iter]](#manual-Std___Iter___mk) β} →
            it.IsPlausibleStep ([IterStep.skip]](#manual-Std___IterStep___yield) it') → motive it') →
          motive it)
  (it : [Iter]](#manual-Std___Iter___mk) β) : motive it



[Std.Iter.inductSteps.{x, u_1}]](#manual-Std___Iter___inductSteps)
  {α β : Type u_1} [[Iterator]](#manual-Std___Iterator___mk) α [Id]](#manual-Id) β]
  [[Finite]](#manual-Std___Iterators___Finite___mk) α [Id]](#manual-Id)] (motive : [Iter]](#manual-Std___Iter___mk) β → Sort x)
  (step :
    (it : [Iter]](#manual-Std___Iter___mk) β) →
      ({it' : [Iter]](#manual-Std___Iter___mk) β} →
          {out : β} →
            it.IsPlausibleStep
                ([IterStep.yield]](#manual-Std___IterStep___yield) it' out) →
              motive it') →
        ({it' : [Iter]](#manual-Std___Iter___mk) β} →
            it.IsPlausibleStep
                ([IterStep.skip]](#manual-Std___IterStep___yield) it') →
              motive it') →
          motive it)
  (it : [Iter]](#manual-Std___Iter___mk) β) : motive it
```

Induction principle for finite iterators: One can define a function `f` that maps every
iterator `it` to an element of `motive it` by defining `f it` in terms of the values of `f` on
the plausible successors of `it'.

def

```lean
[Std.IterM.inductSteps.{x, u_1, u_2}]](#manual-Std___IterM___inductSteps) {α : Type u_1}
  {m : Type u_1 → Type u_2} {β : Type u_1} [[Iterator]](#manual-Std___Iterator___mk) α m β] [[Finite]](#manual-Std___Iterators___Finite___mk) α m]
  (motive : [IterM]](#manual-Std___IterM___mk) m β → Sort x)
  (step :
    (it : [IterM]](#manual-Std___IterM___mk) m β) →
      ({it' : [IterM]](#manual-Std___IterM___mk) m β} →
          {out : β} →
            it.IsPlausibleStep ([IterStep.yield]](#manual-Std___IterStep___yield) it' out) → motive it') →
        ({it' : [IterM]](#manual-Std___IterM___mk) m β} →
            it.IsPlausibleStep ([IterStep.skip]](#manual-Std___IterStep___yield) it') → motive it') →
          motive it)
  (it : [IterM]](#manual-Std___IterM___mk) m β) : motive it



[Std.IterM.inductSteps.{x, u_1, u_2}]](#manual-Std___IterM___inductSteps)
  {α : Type u_1} {m : Type u_1 → Type u_2}
  {β : Type u_1} [[Iterator]](#manual-Std___Iterator___mk) α m β]
  [[Finite]](#manual-Std___Iterators___Finite___mk) α m]
  (motive : [IterM]](#manual-Std___IterM___mk) m β → Sort x)
  (step :
    (it : [IterM]](#manual-Std___IterM___mk) m β) →
      ({it' : [IterM]](#manual-Std___IterM___mk) m β} →
          {out : β} →
            it.IsPlausibleStep
                ([IterStep.yield]](#manual-Std___IterStep___yield) it' out) →
              motive it') →
        ({it' : [IterM]](#manual-Std___IterM___mk) m β} →
            it.IsPlausibleStep
                ([IterStep.skip]](#manual-Std___IterStep___yield) it') →
              motive it') →
          motive it)
  (it : [IterM]](#manual-Std___IterM___mk) m β) : motive it
```

Induction principle for finite monadic iterators: One can define a function `f` that maps every
iterator `it` to an element of `motive it` by defining `f it` in terms of the values of `f` on
the plausible successors of `it'.

The standard library also includes lemmas for the stepwise behavior of all the producers and combinators.
Examples include `List.step_iter_nil`, `List.step_iter_cons`, `IterM.step_map`.

### 22.5.3. Monads for Reasoning {#manual-The-Lean-Language-Reference--Iterators--Reasoning-About-Iterators--Monads-for-Reasoning}

structure

```lean
[Std.Iterators.PostconditionT.{w, w'}]](#manual-Std___Iterators___PostconditionT___mk) (m : Type w → Type w')
  (α : Type w) : Type (max w w')



[Std.Iterators.PostconditionT.{w, w'}]](#manual-Std___Iterators___PostconditionT___mk)
  (m : Type w → Type w') (α : Type w) :
  Type (max w w')
```

`[PostconditionT]](#manual-Std___Iterators___PostconditionT___mk) m α` represents an operation in the monad `m` together with a
intrinsic proof that some postcondition holds for the `α` valued monadic result.
It consists of a predicate `P` about `α` and an element of `m ({ a // P a })` and is a helpful tool
for intrinsic verification, notably termination proofs, in the context of iterators.

`[PostconditionT]](#manual-Std___Iterators___PostconditionT___mk) m` is a monad if `m` is. However, note that `[PostconditionT]](#manual-Std___Iterators___PostconditionT___mk) m α` is a structure,
so that the compiler will generate inefficient code from recursive functions returning
`[PostconditionT]](#manual-Std___Iterators___PostconditionT___mk) m α`. Optimizations for `[ReaderT]](#manual-ReaderT)`, `[StateT]](#manual-StateT)` etc. aren't applicable for structures.

Moreover, `[PostconditionT]](#manual-Std___Iterators___PostconditionT___mk) m α` is not a well-behaved monad transformer because `[PostconditionT.lift]](#manual-Std___Iterators___PostconditionT___lift)`
neither commutes with `[pure]](#manual-Pure___mk)` nor with `[bind]](#manual-Bind___mk)`.

Constructor

```lean
[Std.Iterators.PostconditionT.mk]](#manual-Std___Iterators___PostconditionT___mk).{w, w'}
```

Fields

```lean
Property : α → Prop
```

A predicate that holds for the return value(s) of the `m`-monadic operation.

```lean
operation : m ([Subtype]](#manual-Subtype___mk) self.[Property]](#manual-Std___Iterators___PostconditionT___mk))
```

The actual monadic operation. Its return value is bundled together with a proof that
it satisfies `Property`.

def

```lean
[Std.Iterators.PostconditionT.run.{w, w'}]](#manual-Std___Iterators___PostconditionT___run) {m : Type w → Type w'}
  [[Monad]](#manual-Monad___mk) m] {α : Type w} (x : [PostconditionT]](#manual-Std___Iterators___PostconditionT___mk) m α) : m α



[Std.Iterators.PostconditionT.run.{w, w'}]](#manual-Std___Iterators___PostconditionT___run)
  {m : Type w → Type w'} [[Monad]](#manual-Monad___mk) m]
  {α : Type w} (x : [PostconditionT]](#manual-Std___Iterators___PostconditionT___mk) m α) :
  m α
```

Converts an operation from `PostConditionT m` to `m`, discarding the postcondition.

def

```lean
[Std.Iterators.PostconditionT.lift.{w, w'}]](#manual-Std___Iterators___PostconditionT___lift) {α : Type w}
  {m : Type w → Type w'} [[Functor]](#manual-Functor___mk) m] (x : m α) : [PostconditionT]](#manual-Std___Iterators___PostconditionT___mk) m α



[Std.Iterators.PostconditionT.lift.{w, w'}]](#manual-Std___Iterators___PostconditionT___lift)
  {α : Type w} {m : Type w → Type w'}
  [[Functor]](#manual-Functor___mk) m] (x : m α) :
  [PostconditionT]](#manual-Std___Iterators___PostconditionT___mk) m α
```

Lifts an operation from `m` to `[PostconditionT]](#manual-Std___Iterators___PostconditionT___mk) m` without asserting any nontrivial postcondition.

Caution: `lift` is not a lawful lift function.
For example, `pure a : PostconditionT m α` is not the same as
`[PostconditionT.lift]](#manual-Std___Iterators___PostconditionT___lift) ([pure]](#manual-Pure___mk) a : m α)`.

def

```lean
[Std.Iterators.PostconditionT.liftWithProperty.{w, w'}]](#manual-Std___Iterators___PostconditionT___liftWithProperty) {α : Type w}
  {m : Type w → Type w'} {P : α → Prop} (x : m [{]](#manual-Subtype___mk) α [//]](#manual-Subtype___mk) P α [}]](#manual-Subtype___mk)) :
  [PostconditionT]](#manual-Std___Iterators___PostconditionT___mk) m α



[Std.Iterators.PostconditionT.liftWithProperty.{w,
    w'}]](#manual-Std___Iterators___PostconditionT___liftWithProperty)
  {α : Type w} {m : Type w → Type w'}
  {P : α → Prop} (x : m [{]](#manual-Subtype___mk) α [//]](#manual-Subtype___mk) P α [}]](#manual-Subtype___mk)) :
  [PostconditionT]](#manual-Std___Iterators___PostconditionT___mk) m α
```

Lifts a monadic value from `m { a : α // P a }` to a value `[PostconditionT]](#manual-Std___Iterators___PostconditionT___mk) m α`.

inductive predicate

```lean
[Std.Iter.IsPlausibleIndirectOutput.{w}]](#manual-Std___Iter___IsPlausibleIndirectOutput___direct) {α β : Type w}
  [[Iterator]](#manual-Std___Iterator___mk) α [Id]](#manual-Id) β] : [Iter]](#manual-Std___Iter___mk) β → β → Prop



[Std.Iter.IsPlausibleIndirectOutput.{w}]](#manual-Std___Iter___IsPlausibleIndirectOutput___direct)
  {α β : Type w} [[Iterator]](#manual-Std___Iterator___mk) α [Id]](#manual-Id) β] :
  [Iter]](#manual-Std___Iter___mk) β → β → Prop
```

Asserts that a certain iterator `it` could plausibly yield the value `out` after an arbitrary
number of steps.

Constructors

```lean
[Std.Iter.IsPlausibleIndirectOutput.direct.{w}]](#manual-Std___Iter___IsPlausibleIndirectOutput___direct) {α β : Type w}
  [[Iterator]](#manual-Std___Iterator___mk) α [Id]](#manual-Id) β] {it : [Iter]](#manual-Std___Iter___mk) β} {out : β} :
  it.IsPlausibleOutput out →
    it.[IsPlausibleIndirectOutput]](#manual-Std___Iter___IsPlausibleIndirectOutput___direct) out
```

The output value could plausibly be emitted in the next step.

```lean
[Std.Iter.IsPlausibleIndirectOutput.indirect.{w}]](#manual-Std___Iter___IsPlausibleIndirectOutput___direct)
  {α β : Type w} [[Iterator]](#manual-Std___Iterator___mk) α [Id]](#manual-Id) β] {it it' : [Iter]](#manual-Std___Iter___mk) β}
  {out : β} :
  it'.IsPlausibleSuccessorOf it →
    it'.[IsPlausibleIndirectOutput]](#manual-Std___Iter___IsPlausibleIndirectOutput___direct) out →
      it.[IsPlausibleIndirectOutput]](#manual-Std___Iter___IsPlausibleIndirectOutput___direct) out
```

The output value could plausibly be emitted in a step after the next step.

structure

```lean
[Std.Iterators.HetT.{w, w', v}]](#manual-Std___Iterators___HetT___mk) (m : Type w → Type w') (α : Type v) :
  Type (max v w')



[Std.Iterators.HetT.{w, w', v}]](#manual-Std___Iterators___HetT___mk)
  (m : Type w → Type w') (α : Type v) :
  Type (max v w')
```

If `m` is a monad, then `[HetT]](#manual-Std___Iterators___HetT___mk) m` is a monad that has two features:

- It generalizes `m` to arbitrary universes.
- It tracks a postcondition property that holds for the monadic return value, similarly to
  `[PostconditionT]](#manual-Std___Iterators___PostconditionT___mk)`.

This monad is noncomputable and is merely a vehicle for more convenient proofs, especially proofs
about the equivalence of iterators, because it avoids universe issues and spares users the work
to handle the postconditions manually.

Caution: Just like `[PostconditionT]](#manual-Std___Iterators___PostconditionT___mk)`, this is not a lawful monad transformer.
To lift from `m` to `[HetT]](#manual-Std___Iterators___HetT___mk) m`, use `[HetT.lift]](#manual-Std___Iterators___HetT___lift)`.

Because this monad is fundamentally universe-polymorphic, it is recommended for consistency to
always use the methods `[HetT.pure]](#manual-Std___Iterators___HetT___pure)`, `[HetT.map]](#manual-Std___Iterators___HetT___map)` and `[HetT.bind]](#manual-Std___Iterators___HetT___bind)` instead of the homogeneous versions
`[Pure.pure]](#manual-Pure___mk)`, `[Functor.map]](#manual-Functor___mk)` and `[Bind.bind]](#manual-Bind___mk)`.

Constructor

```lean
[Std.Iterators.HetT.mk]](#manual-Std___Iterators___HetT___mk).{w, w', v}
```

Fields

```lean
Property : α → Prop
```

A predicate that holds for the return value(s) of the `m`-monadic operation.

```lean
small : Std.Internal.Small ([Subtype]](#manual-Subtype___mk) self.[Property]](#manual-Std___Iterators___HetT___mk))
```

A proof that the possible return values are equivalent to a `w`-small type.

```lean
operation : m (Std.Internal.USquash ([Subtype]](#manual-Subtype___mk) self.[Property]](#manual-Std___Iterators___HetT___mk)))
```

The actual monadic operation. Its return value is bundled together with a proof that
it satisfies `Property` and squashed so that it fits into the monad `m`.

def

```lean
[Std.IterM.stepAsHetT.{u_1, u_2}]](#manual-Std___IterM___stepAsHetT) {α : Type u_1} {m : Type u_1 → Type u_2}
  {β : Type u_1} [[Iterator]](#manual-Std___Iterator___mk) α m β] [[Monad]](#manual-Monad___mk) m] (it : [IterM]](#manual-Std___IterM___mk) m β) :
  [HetT]](#manual-Std___Iterators___HetT___mk) m ([IterStep]](#manual-Std___IterStep___yield) ([IterM]](#manual-Std___IterM___mk) m β) β)



[Std.IterM.stepAsHetT.{u_1, u_2}]](#manual-Std___IterM___stepAsHetT)
  {α : Type u_1} {m : Type u_1 → Type u_2}
  {β : Type u_1} [[Iterator]](#manual-Std___Iterator___mk) α m β]
  [[Monad]](#manual-Monad___mk) m] (it : [IterM]](#manual-Std___IterM___mk) m β) :
  [HetT]](#manual-Std___Iterators___HetT___mk) m ([IterStep]](#manual-Std___IterStep___yield) ([IterM]](#manual-Std___IterM___mk) m β) β)
```

A noncomputable variant of `[IterM.step]](#manual-Std___IterM___step)` using the `[HetT]](#manual-Std___Iterators___HetT___mk)` monad.
It is used in the definition of the equivalence relations on iterators,
namely `[IterM.Equiv]](#manual-Std___IterM___Equiv)` and `[Iter.Equiv]](#manual-Std___Iter___Equiv)`.

def

```lean
[Std.Iterators.HetT.lift.{w, w'}]](#manual-Std___Iterators___HetT___lift) {α : Type w} {m : Type w → Type w'}
  [[Monad]](#manual-Monad___mk) m] (x : m α) : [HetT]](#manual-Std___Iterators___HetT___mk) m α



[Std.Iterators.HetT.lift.{w, w'}]](#manual-Std___Iterators___HetT___lift)
  {α : Type w} {m : Type w → Type w'}
  [[Monad]](#manual-Monad___mk) m] (x : m α) : [HetT]](#manual-Std___Iterators___HetT___mk) m α
```

Lifts `x : m α` into `[HetT]](#manual-Std___Iterators___HetT___mk) m α` with the trivial postcondition.

Caution: This is not a lawful monad lifting function

def

```lean
[Std.Iterators.HetT.prun.{u_1, u_2, u_3}]](#manual-Std___Iterators___HetT___prun) {m : Type u_1 → Type u_2}
  {α : Type u_3} {β : Type u_1} [[Monad]](#manual-Monad___mk) m] (x : [HetT]](#manual-Std___Iterators___HetT___mk) m α)
  (f : (a : α) → x.[Property]](#manual-Std___Iterators___HetT___mk) a → m β) : m β



[Std.Iterators.HetT.prun.{u_1, u_2, u_3}]](#manual-Std___Iterators___HetT___prun)
  {m : Type u_1 → Type u_2} {α : Type u_3}
  {β : Type u_1} [[Monad]](#manual-Monad___mk) m] (x : [HetT]](#manual-Std___Iterators___HetT___mk) m α)
  (f : (a : α) → x.[Property]](#manual-Std___Iterators___HetT___mk) a → m β) : m β
```

Applies the given function to the result of the contained `m`-monadic operation with a
proof that the postcondition property holds, returning another operation in `m`.

def

```lean
[Std.Iterators.HetT.pure.{w, w', v}]](#manual-Std___Iterators___HetT___pure) {m : Type w → Type w'} [[Pure]](#manual-Pure___mk) m]
  {α : Type v} (a : α) : [HetT]](#manual-Std___Iterators___HetT___mk) m α



[Std.Iterators.HetT.pure.{w, w', v}]](#manual-Std___Iterators___HetT___pure)
  {m : Type w → Type w'} [[Pure]](#manual-Pure___mk) m]
  {α : Type v} (a : α) : [HetT]](#manual-Std___Iterators___HetT___mk) m α
```

A universe-heterogeneous version of `[Pure.pure]](#manual-Pure___mk)`. Given `a : α`, it returns an element of `[HetT]](#manual-Std___Iterators___HetT___mk) m α`
with the postcondition `(a = ·)`.

def

```lean
[Std.Iterators.HetT.map.{w, w', u, v}]](#manual-Std___Iterators___HetT___map) {m : Type w → Type w'} [[Functor]](#manual-Functor___mk) m]
  {α : Type u} {β : Type v} (f : α → β) (x : [HetT]](#manual-Std___Iterators___HetT___mk) m α) : [HetT]](#manual-Std___Iterators___HetT___mk) m β



[Std.Iterators.HetT.map.{w, w', u, v}]](#manual-Std___Iterators___HetT___map)
  {m : Type w → Type w'} [[Functor]](#manual-Functor___mk) m]
  {α : Type u} {β : Type v} (f : α → β)
  (x : [HetT]](#manual-Std___Iterators___HetT___mk) m α) : [HetT]](#manual-Std___Iterators___HetT___mk) m β
```

A universe-heterogeneous version of `[Functor.map]](#manual-Functor___mk)`.

def

```lean
[Std.Iterators.HetT.pmap.{w, w', u, v}]](#manual-Std___Iterators___HetT___pmap) {m : Type w → Type w'} [[Functor]](#manual-Functor___mk) m]
  {α : Type u} {β : Type v} (x : [HetT]](#manual-Std___Iterators___HetT___mk) m α)
  (f : (a : α) → x.[Property]](#manual-Std___Iterators___HetT___mk) a → β) : [HetT]](#manual-Std___Iterators___HetT___mk) m β



[Std.Iterators.HetT.pmap.{w, w', u, v}]](#manual-Std___Iterators___HetT___pmap)
  {m : Type w → Type w'} [[Functor]](#manual-Functor___mk) m]
  {α : Type u} {β : Type v} (x : [HetT]](#manual-Std___Iterators___HetT___mk) m α)
  (f : (a : α) → x.[Property]](#manual-Std___Iterators___HetT___mk) a → β) :
  [HetT]](#manual-Std___Iterators___HetT___mk) m β
```

A generalization of `[HetT.map]](#manual-Std___Iterators___HetT___map)` that provides the postcondition property to the mapping function.

def

```lean
[Std.Iterators.HetT.bind.{w, w', u, v}]](#manual-Std___Iterators___HetT___bind) {m : Type w → Type w'} [[Monad]](#manual-Monad___mk) m]
  {α : Type u} {β : Type v} (x : [HetT]](#manual-Std___Iterators___HetT___mk) m α) (f : α → [HetT]](#manual-Std___Iterators___HetT___mk) m β) : [HetT]](#manual-Std___Iterators___HetT___mk) m β



[Std.Iterators.HetT.bind.{w, w', u, v}]](#manual-Std___Iterators___HetT___bind)
  {m : Type w → Type w'} [[Monad]](#manual-Monad___mk) m]
  {α : Type u} {β : Type v} (x : [HetT]](#manual-Std___Iterators___HetT___mk) m α)
  (f : α → [HetT]](#manual-Std___Iterators___HetT___mk) m β) : [HetT]](#manual-Std___Iterators___HetT___mk) m β
```

A universe-heterogeneous version of `[Bind.bind]](#manual-Bind___mk)`.

def

```lean
[Std.Iterators.HetT.pbind.{w, w', u, v}]](#manual-Std___Iterators___HetT___pbind) {m : Type w → Type w'} [[Monad]](#manual-Monad___mk) m]
  {α : Type u} {β : Type v} (x : [HetT]](#manual-Std___Iterators___HetT___mk) m α)
  (f : (a : α) → x.[Property]](#manual-Std___Iterators___HetT___mk) a → [HetT]](#manual-Std___Iterators___HetT___mk) m β) : [HetT]](#manual-Std___Iterators___HetT___mk) m β



[Std.Iterators.HetT.pbind.{w, w', u, v}]](#manual-Std___Iterators___HetT___pbind)
  {m : Type w → Type w'} [[Monad]](#manual-Monad___mk) m]
  {α : Type u} {β : Type v} (x : [HetT]](#manual-Std___Iterators___HetT___mk) m α)
  (f :
    (a : α) → x.[Property]](#manual-Std___Iterators___HetT___mk) a → [HetT]](#manual-Std___Iterators___HetT___mk) m β) :
  [HetT]](#manual-Std___Iterators___HetT___mk) m β
```

A generalization of `[HetT.bind]](#manual-Std___Iterators___HetT___bind)` that provides the postcondition property to the mapping function.

### 22.5.4. Equivalence {#manual-The-Lean-Language-Reference--Iterators--Reasoning-About-Iterators--Equivalence}

Iterator equivalence is defined in terms of the observable behavior of iterators, rather than their implementations.
In particular, the internal state is ignored.

def

```lean
[Std.Iter.Equiv.{u_1}]](#manual-Std___Iter___Equiv) {α₁ α₂ β : Type u_1} [[Iterator]](#manual-Std___Iterator___mk) α₁ [Id]](#manual-Id) β]
  [[Iterator]](#manual-Std___Iterator___mk) α₂ [Id]](#manual-Id) β] (ita : [Iter]](#manual-Std___Iter___mk) β) (itb : [Iter]](#manual-Std___Iter___mk) β) : Prop



[Std.Iter.Equiv.{u_1}]](#manual-Std___Iter___Equiv) {α₁ α₂ β : Type u_1}
  [[Iterator]](#manual-Std___Iterator___mk) α₁ [Id]](#manual-Id) β] [[Iterator]](#manual-Std___Iterator___mk) α₂ [Id]](#manual-Id) β]
  (ita : [Iter]](#manual-Std___Iter___mk) β) (itb : [Iter]](#manual-Std___Iter___mk) β) : Prop
```

Equivalence relation on iterators. Equivalent iterators behave the same as long as the
internal state of them is not directly inspected.

Two iterators (possibly of different types) are equivalent if they have the same
`[Iterator.IsPlausibleStep]](#manual-Std___Iterator___mk)` relation and their step functions are the same *up to equivalence of the
successor iterators*. This coinductive definition captures the idea that the only relevant feature
of an iterator is its step function. Other information retrievable from the iterator -- for example,
whether it is a list iterator or an array iterator -- is totally irrelevant for the equivalence
judgement.

def

```lean
[Std.IterM.Equiv.{w, w'}]](#manual-Std___IterM___Equiv) {m : Type w → Type w'} [[Monad]](#manual-Monad___mk) m] [[LawfulMonad]](#manual-LawfulMonad___mk) m]
  {β α₁ α₂ : Type w} [[Iterator]](#manual-Std___Iterator___mk) α₁ m β] [[Iterator]](#manual-Std___Iterator___mk) α₂ m β]
  (ita : [IterM]](#manual-Std___IterM___mk) m β) (itb : [IterM]](#manual-Std___IterM___mk) m β) : Prop



[Std.IterM.Equiv.{w, w'}]](#manual-Std___IterM___Equiv)
  {m : Type w → Type w'} [[Monad]](#manual-Monad___mk) m]
  [[LawfulMonad]](#manual-LawfulMonad___mk) m] {β α₁ α₂ : Type w}
  [[Iterator]](#manual-Std___Iterator___mk) α₁ m β] [[Iterator]](#manual-Std___Iterator___mk) α₂ m β]
  (ita : [IterM]](#manual-Std___IterM___mk) m β) (itb : [IterM]](#manual-Std___IterM___mk) m β) :
  Prop
```

Equivalence relation on monadic iterators. Equivalent iterators behave the same as long as the
internal state of them is not directly inspected.

Two iterators (possibly of different types) are equivalent if they have the same
`[Iterator.IsPlausibleStep]](#manual-Std___Iterator___mk)` relation and their step functions are the same *up to equivalence of the
successor iterators*. This coinductive definition captures the idea that the only relevant feature
of an iterator is its step function. Other information retrievable from the iterator -- for example,
whether it is a list iterator or an array iterator -- is totally irrelevant for the equivalence
judgement.

---




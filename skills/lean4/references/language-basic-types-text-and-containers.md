## Basic Types — 20.7. Characters {#manual-basic-types-207-characters}

> 📄 Source: https://lean-lang.org/doc/reference/latest/Basic-Types/Characters/

Characters are represented by the type `[Char]](#manual-Char___mk)`, which may be any Unicode [scalar value](http://www.unicode.org/glossary/#unicode_scalar_value).
While [strings](https://lean-lang.org/doc/reference/latest/Basic-Types/Strings/#String) are UTF-8-encoded arrays of bytes, characters are represented by full 32-bit values.
Lean provides special [syntax]](#manual-char-syntax) for character literals.

### 20.7.1. Logical Model {#manual-char-model}

From the perspective of Lean's logic, characters consist of a 32-bit unsigned integer paired with a proof that it is a valid Unicode scalar value.

structure

```lean
[Char]](#manual-Char___mk) : Type



[Char]](#manual-Char___mk) : Type
```

Characters are Unicode [scalar values](http://www.unicode.org/glossary/#unicode_scalar_value).

Constructor

```lean
[Char.mk]](#manual-Char___mk)
```

Fields

```lean
val : [UInt32]](#manual-UInt32___ofBitVec)
```

The underlying Unicode scalar value as a `[UInt32]](#manual-UInt32___ofBitVec)`.

```lean
valid : self.[val]](#manual-Char___mk).[isValidChar]](#manual-UInt32___isValidChar)
```

The value must be a legal scalar value.

### 20.7.2. Run-Time Representation {#manual-char-runtime}

As a [trivial wrapper]](#manual-inductive-types-trivial-wrappers), characters are represented identically to `[UInt32]](#manual-UInt32___ofBitVec)`.
In particular, characters are represented as 32-bit immediate values in monomorphic contexts.
In other words, a field of a constructor or structure of type `[Char]](#manual-Char___mk)` does not require indirection to access.
In polymorphic contexts, characters are [boxed]](#manual---tech-term-Boxed).

### 20.7.3. Syntax {#manual-char-syntax}

Character literals consist of a single character or an escape sequence enclosed in single quotes (`'`, Unicode `'APOSTROPHE' (U+0027)`).
Between these single quotes, the character literal may contain character other that `'`, including newlines, which are included literally (with the caveat that all newlines in a Lean source file are interpreted as `'\n'`, regardless of file encoding and platform).
Special characters may be escaped with a backslash, so `'\''` is a character literal that contains a single quote.
The following forms of escape sequences are accepted:

`\r`, `\n`, `\t`, `\\`, `\"`, `\'`
:   These escape sequences have the usual meaning, mapping to `CR`, `LF`, tab, backslash, double quote, and single quote, respectively.

`\xNN`
:   When `NN` is a sequence of two hexadecimal digits, this escape denotes the character whose Unicode code point is indicated by the two-digit hexadecimal code.

`\uNNNN`
:   When `NN` is a sequence of two hexadecimal digits, this escape denotes the character whose Unicode code point is indicated by the four-digit hexadecimal code.

### 20.7.4. API Reference {#manual-char-api}

#### 20.7.4.1. Conversions {#manual-The-Lean-Language-Reference--Basic-Types--Characters--API-Reference--Conversions}

def

```lean
[Char.ofNat]](#manual-Char___ofNat) (n : [Nat]](#manual-Nat___zero)) : [Char]](#manual-Char___mk)



[Char.ofNat]](#manual-Char___ofNat) (n : [Nat]](#manual-Nat___zero)) : [Char]](#manual-Char___mk)
```

Converts a `[Nat]](#manual-Nat___zero)` into a `[Char]](#manual-Char___mk)`. If the `[Nat]](#manual-Nat___zero)` does not encode a valid Unicode scalar value, `'\0'` is
returned instead.

def

```lean
[Char.toNat]](#manual-Char___toNat) (c : [Char]](#manual-Char___mk)) : [Nat]](#manual-Nat___zero)



[Char.toNat]](#manual-Char___toNat) (c : [Char]](#manual-Char___mk)) : [Nat]](#manual-Nat___zero)
```

The character's Unicode code point as a `[Nat]](#manual-Nat___zero)`.

def

```lean
[Char.isValidCharNat]](#manual-Char___isValidCharNat) (n : [Nat]](#manual-Nat___zero)) : Prop



[Char.isValidCharNat]](#manual-Char___isValidCharNat) (n : [Nat]](#manual-Nat___zero)) : Prop
```

True for natural numbers that are valid [Unicode scalar
values](https://www.unicode.org/glossary/#unicode_scalar_value).

def

```lean
[Char.ofUInt8]](#manual-Char___ofUInt8) (n : [UInt8]](#manual-UInt8___ofBitVec)) : [Char]](#manual-Char___mk)



[Char.ofUInt8]](#manual-Char___ofUInt8) (n : [UInt8]](#manual-UInt8___ofBitVec)) : [Char]](#manual-Char___mk)
```

Converts an 8-bit unsigned integer into a character.

The integer's value is interpreted as a Unicode code point.

def

```lean
[Char.toUInt8]](#manual-Char___toUInt8) (c : [Char]](#manual-Char___mk)) : [UInt8]](#manual-UInt8___ofBitVec)



[Char.toUInt8]](#manual-Char___toUInt8) (c : [Char]](#manual-Char___mk)) : [UInt8]](#manual-UInt8___ofBitVec)
```

Converts a character into a `[UInt8]](#manual-UInt8___ofBitVec)` that contains its code point.

If the code point is larger than 255, it is truncated (reduced modulo 256).

There are two ways to convert a character to a string.
`[Char.toString]](#manual-Char___toString)` converts a character to a singleton string that consists of only that character, while `[Char.quote]](#manual-Char___quote)` converts the character to a string representation of the corresponding character literal.

def

```lean
[Char.toString]](#manual-Char___toString) (c : [Char]](#manual-Char___mk)) : [String](https://lean-lang.org/doc/reference/latest/Basic-Types/Strings/#String___ofByteArray)



[Char.toString]](#manual-Char___toString) (c : [Char]](#manual-Char___mk)) : [String](https://lean-lang.org/doc/reference/latest/Basic-Types/Strings/#String___ofByteArray)
```

Constructs a singleton string that contains only the provided character.

Examples:

- `'L'.[toString]](#manual-Char___toString) = "L"`
- `'"'.[toString]](#manual-Char___toString) = "\""`

def

```lean
[Char.quote]](#manual-Char___quote) (c : [Char]](#manual-Char___mk)) : [String](https://lean-lang.org/doc/reference/latest/Basic-Types/Strings/#String___ofByteArray)



[Char.quote]](#manual-Char___quote) (c : [Char]](#manual-Char___mk)) : [String](https://lean-lang.org/doc/reference/latest/Basic-Types/Strings/#String___ofByteArray)
```

Quotes the character to its representation as a character literal, surrounded by single quotes and
escaped as necessary.

Examples:

- `'L'.[quote]](#manual-Char___quote) = "'L'"`
- `'"'.[quote]](#manual-Char___quote) = "'\\\"'"`

**Example: From Characters to Strings**

`[Char.toString]](#manual-Char___toString)` produces a string that contains only the character in question:

```lean
[#eval]](#manual-Lean___Parser___Command___eval) 'e'.[toString]](#manual-Char___toString)
```

```lean
"e"
```

```lean
[#eval]](#manual-Lean___Parser___Command___eval) '\x65'.[toString]](#manual-Char___toString)
```

```lean
"e"
```

```lean
[#eval]](#manual-Lean___Parser___Command___eval) '"'.[toString]](#manual-Char___toString)
```

```lean
"\""
```

`[Char.quote]](#manual-Char___quote)` produces a string that contains a character literal, suitably escaped:

```lean
[#eval]](#manual-Lean___Parser___Command___eval) 'e'.[quote]](#manual-Char___quote)
```

```lean
"'e'"
```

```lean
[#eval]](#manual-Lean___Parser___Command___eval) '\x65'.[quote]](#manual-Char___quote)
```

```lean
"'e'"
```

```lean
[#eval]](#manual-Lean___Parser___Command___eval) '"'.[quote]](#manual-Char___quote)
```

```lean
"'\\\"'"
```

#### 20.7.4.2. Character Classes {#manual-char-api-classes}

def

```lean
[Char.isAlpha]](#manual-Char___isAlpha) (c : [Char]](#manual-Char___mk)) : [Bool](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)



[Char.isAlpha]](#manual-Char___isAlpha) (c : [Char]](#manual-Char___mk)) : [Bool](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)
```

Returns `[true](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)` if the character is an ASCII letter.

The ASCII letters are the following: `ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz`.

def

```lean
[Char.isAlphanum]](#manual-Char___isAlphanum) (c : [Char]](#manual-Char___mk)) : [Bool](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)



[Char.isAlphanum]](#manual-Char___isAlphanum) (c : [Char]](#manual-Char___mk)) : [Bool](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)
```

Returns `[true](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)` if the character is an ASCII letter or digit.

The ASCII letters are the following: `ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz`.
The ASCII digits are the following: `0123456789`.

def

```lean
[Char.isDigit]](#manual-Char___isDigit) (c : [Char]](#manual-Char___mk)) : [Bool](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)



[Char.isDigit]](#manual-Char___isDigit) (c : [Char]](#manual-Char___mk)) : [Bool](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)
```

Returns `[true](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)` if the character is an ASCII digit.

The ASCII digits are the following: `0123456789`.

def

```lean
[Char.isLower]](#manual-Char___isLower) (c : [Char]](#manual-Char___mk)) : [Bool](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)



[Char.isLower]](#manual-Char___isLower) (c : [Char]](#manual-Char___mk)) : [Bool](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)
```

Returns `[true](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)` if the character is a lowercase ASCII letter.

The lowercase ASCII letters are the following: `abcdefghijklmnopqrstuvwxyz`.

def

```lean
[Char.isUpper]](#manual-Char___isUpper) (c : [Char]](#manual-Char___mk)) : [Bool](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)



[Char.isUpper]](#manual-Char___isUpper) (c : [Char]](#manual-Char___mk)) : [Bool](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)
```

Returns `[true](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)` if the character is a uppercase ASCII letter.

The uppercase ASCII letters are the following: `ABCDEFGHIJKLMNOPQRSTUVWXYZ`.

def

```lean
[Char.isWhitespace]](#manual-Char___isWhitespace) (c : [Char]](#manual-Char___mk)) : [Bool](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)



[Char.isWhitespace]](#manual-Char___isWhitespace) (c : [Char]](#manual-Char___mk)) : [Bool](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)
```

Returns `[true](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)` if the character is a space `(' ', U+0020)`, a tab `('\t', U+0009)`, a carriage
return `('\r', U+000D)`, or a newline `('\n', U+000A)`.

#### 20.7.4.3. Case Conversion {#manual-The-Lean-Language-Reference--Basic-Types--Characters--API-Reference--Case-Conversion}

def

```lean
[Char.toUpper]](#manual-Char___toUpper) (c : [Char]](#manual-Char___mk)) : [Char]](#manual-Char___mk)



[Char.toUpper]](#manual-Char___toUpper) (c : [Char]](#manual-Char___mk)) : [Char]](#manual-Char___mk)
```

Converts a lowercase ASCII letter to the corresponding uppercase letter. Letters outside the ASCII
alphabet are returned unchanged.

The lowercase ASCII letters are the following: `abcdefghijklmnopqrstuvwxyz`.

def

```lean
[Char.toLower]](#manual-Char___toLower) (c : [Char]](#manual-Char___mk)) : [Char]](#manual-Char___mk)



[Char.toLower]](#manual-Char___toLower) (c : [Char]](#manual-Char___mk)) : [Char]](#manual-Char___mk)
```

Converts an uppercase ASCII letter to the corresponding lowercase letter. Letters outside the ASCII
alphabet are returned unchanged.

The uppercase ASCII letters are the following: `ABCDEFGHIJKLMNOPQRSTUVWXYZ`.

#### 20.7.4.4. Comparisons {#manual-The-Lean-Language-Reference--Basic-Types--Characters--API-Reference--Comparisons}

def

```lean
[Char.le]](#manual-Char___le) (a b : [Char]](#manual-Char___mk)) : Prop



[Char.le]](#manual-Char___le) (a b : [Char]](#manual-Char___mk)) : Prop
```

One character is less than or equal to another if its code point is less than or equal to the
other's.

def

```lean
[Char.lt]](#manual-Char___lt) (a b : [Char]](#manual-Char___mk)) : Prop



[Char.lt]](#manual-Char___lt) (a b : [Char]](#manual-Char___mk)) : Prop
```

One character is less than another if its code point is strictly less than the other's.

#### 20.7.4.5. Unicode {#manual-The-Lean-Language-Reference--Basic-Types--Characters--API-Reference--Unicode}

def

```lean
[Char.utf8Size]](#manual-Char___utf8Size) (c : [Char]](#manual-Char___mk)) : [Nat]](#manual-Nat___zero)



[Char.utf8Size]](#manual-Char___utf8Size) (c : [Char]](#manual-Char___mk)) : [Nat]](#manual-Nat___zero)
```

Returns the number of bytes required to encode this `[Char]](#manual-Char___mk)` in UTF-8.

---



## Basic Types — 20.8. Strings {#manual-basic-types-208-strings}

> 📄 Source: https://lean-lang.org/doc/reference/latest/Basic-Types/Strings/

Strings represent Unicode text.
Strings are specially supported by Lean:

- They have a *logical model* that specifies their behavior in terms of `[ByteArray](https://lean-lang.org/doc/reference/latest/Basic-Types/Byte-Arrays/#ByteArray___mk)`s that contain UTF-8 scalar values.
- In compiled code, they have a run-time representation that additionally includes a cached length, measured as the number of scalar values.
  The Lean runtime provides optimized implementations of string operations.
- There is [string literal syntax]](#manual-string-syntax) for writing strings.

UTF-8 is a variable-width encoding.
A character may be encoded as a one, two, three, or four byte code unit.
The fact that strings are UTF-8-encoded byte arrays is visible in the API:

- There is no operation to project a particular character out of the string, as this would be a performance trap. [Use an iterator]](#manual-string-iterators) in a loop instead of a `[Nat]](#manual-Nat___zero)`.
- Strings are indexed by `[String.Pos]](#manual-String___Pos___mk)`, which internally records *byte counts* rather than *character counts*, and thus takes constant time.
  `[String.Pos]](#manual-String___Pos___mk)` includes a proof that the byte count in fact points at the beginning of a UTF-8 code unit.
  Aside from `0`, these should not be constructed directly, but rather updated using `String.next` and `String.prev`.

### 20.8.1. Logical Model {#manual-The-Lean-Language-Reference--Basic-Types--Strings--Logical-Model}

structure

```lean
[String]](#manual-String___ofByteArray) : Type



[String]](#manual-String___ofByteArray) : Type
```

A string is a sequence of Unicode scalar values.

At runtime, strings are represented by [dynamic arrays](https://en.wikipedia.org/wiki/Dynamic_array)
of bytes using the UTF-8 encoding. Both the size in bytes (`[String.utf8ByteSize]](#manual-String___utf8ByteSize)`) and in characters
(`[String.length]](#manual-String___length)`) are cached and take constant time. Many operations on strings perform in-place
modifications when the reference to the string is unique.

Constructor

```lean
[String.ofByteArray]](#manual-String___ofByteArray)
```

Fields

```lean
toByteArray : [ByteArray](https://lean-lang.org/doc/reference/latest/Basic-Types/Byte-Arrays/#ByteArray___mk)
```

The bytes of the UTF-8 encoding of the string. Since strings have a special representation in
the runtime, this function actually takes linear time and space at runtime. For efficient access
to the string's bytes, use `[String.utf8ByteSize]](#manual-String___utf8ByteSize)` and `[String.getUTF8Byte]](#manual-String___getUTF8Byte)`.

```lean
isValidUTF8 : self.[toByteArray]](#manual-String___ofByteArray).IsValidUTF8
```

The bytes of the string form valid UTF-8.

The logical model of strings in Lean is a structure that contains two fields:

- `[String.toByteArray]](#manual-String___ofByteArray)` is a `[ByteArray](https://lean-lang.org/doc/reference/latest/Basic-Types/Byte-Arrays/#ByteArray___mk)`, which contains the UTF-8 encoding of the string.
- `[String.isValidUTF8]](#manual-String___ofByteArray)` is a proof that the bytes are in fact a valid UTF-8 encoding of a string.

This model allows operations on byte arrays to be used to specify and prove properties about string operations at a low level while still building on the theory of byte arrays.
At the same time, it is close enough to the real run-time representation to avoid impedance mismatches between the logical model and the operations that make sense in the run-time representation.

#### 20.8.1.1. Backwards Compatibility {#manual-The-Lean-Language-Reference--Basic-Types--Strings--Logical-Model--Backwards-Compatibility}

In prior versions of Lean, the logical model of strings was a structure that contained a list of characters.
This model is still useful.
It is still accessible using `[String.ofList]](#manual-String___ofList)`, which converts a list of characters into a `[String]](#manual-String___ofByteArray)`, and `String.toList`, which converts a `[String]](#manual-String___ofByteArray)` into a list of characters.

def

```lean
[String.ofList]](#manual-String___ofList) (data : [List](https://lean-lang.org/doc/reference/latest/Basic-Types/Linked-Lists/#List___nil) [Char]](#manual-Char___mk)) : [String]](#manual-String___ofByteArray)



[String.ofList]](#manual-String___ofList) (data : [List](https://lean-lang.org/doc/reference/latest/Basic-Types/Linked-Lists/#List___nil) [Char]](#manual-Char___mk)) : [String]](#manual-String___ofByteArray)
```

Creates a string that contains the characters in a list, in order.

Examples:

- `[String.ofList]](#manual-String___ofList) ['L', '∃', '∀', 'N'] = "L∃∀N"`
- `[String.ofList]](#manual-String___ofList) [] = ""`
- `[String.ofList]](#manual-String___ofList) ['a', 'a', 'a'] = "aaa"`

def

```lean
String.toList (s : [String]](#manual-String___ofByteArray)) : [List](https://lean-lang.org/doc/reference/latest/Basic-Types/Linked-Lists/#List___nil) [Char]](#manual-Char___mk)



String.toList (s : [String]](#manual-String___ofByteArray)) : [List](https://lean-lang.org/doc/reference/latest/Basic-Types/Linked-Lists/#List___nil) [Char]](#manual-Char___mk)
```

Converts a string to a list of characters.

Since strings are represented as dynamic arrays of bytes containing the string encoded using
UTF-8, this operation takes time and space linear in the length of the string.

Examples:

- `"abc".toList = ['a', 'b', 'c']`
- `"".toList = []`
- `"\n".toList = ['\n']`

### 20.8.2. Run-Time Representation {#manual-string-runtime}

m\_header






Lean object header




















m\_size






Byte countsize\_t




















m\_capacity






Allocated spacesize\_t




















m\_length






Characterssize\_t




















m\_data






String datachar array



















'\0'

Memory layout of strings

Strings are represented as [dynamic arrays](https://lean-lang.org/doc/reference/latest/Basic-Types/Arrays/#--tech-term-dynamic-arrays) of bytes, encoded in UTF-8.
After the object header, a string contains:

byte count
:   The number of bytes that currently contain valid string data

capacity
:   The number of bytes presently allocated for the string

length
:   The length of the encoded string, which may be shorter than the byte count due to UTF-8 multi-byte characters

data
:   The actual character data in the string, null-terminated

Many string functions in the Lean runtime check whether they have exclusive access to their argument by consulting the reference count in the object header.
If they do, and the string's capacity is sufficient, then the existing string can be mutated rather than allocating fresh memory.
Otherwise, a new string must be allocated.

#### 20.8.2.1. Performance Notes {#manual-string-performance}

Despite the fact that they appear to be an ordinary constructor and projection, `[String.ofByteArray]](#manual-String___ofByteArray)` and `[String.toByteArray]](#manual-String___ofByteArray)` take **time linear in the length of the string**.
This is because byte arrays and strings do not have an identical representation, so the contents of the byte array must be copied to a new object.

### 20.8.3. Syntax {#manual-string-syntax}

Lean has three kinds of string literals: ordinary string literals, interpolated string literals, and raw string literals.

#### 20.8.3.1. String Literals {#manual-string-literals}

String literals begin and end with a double-quote character `"`. 
Between these characters, they may contain any other character, including newlines, which are included literally (with the caveat that all newlines in a Lean source file are interpreted as `'\n'`, regardless of file encoding and platform).
Special characters that cannot otherwise be written in string literals may be escaped with a backslash, so `"\"Quotes\""` is a string literal that begins and ends with double quotes.
The following forms of escape sequences are accepted:

`\r`, `\n`, `\t`, `\\`, `\"`, `\'`
:   These escape sequences have the usual meaning, mapping to `CR`, `LF`, tab, backslash, double quote, and single quote, respectively.

`\xNN`
:   When `NN` is a sequence of two hexadecimal digits, this escape denotes the character whose Unicode code point is indicated by the two-digit hexadecimal code.

`\uNNNN`
:   When `NN` is a sequence of two hexadecimal digits, this escape denotes the character whose Unicode code point is indicated by the four-digit hexadecimal code.

String literals may contain *gaps*.
A gap is indicated by an escaped newline, with no intervening characters between the escaping backslash and the newline.
In this case, the string denoted by the literal is missing the newline and all leading whitespace from the next line.
String gaps may not precede lines that contain only whitespace.

Here, `str1` and `str2` are the same string:

```lean
def str1 := "String with \
a gap"
def str2 := "String with a gap"
example : [str1]](#manual-str1) = [str2]](#manual-str2) := [rfl]](#manual-rfl-next)
```

If the line following the gap is empty, the string is rejected:

```lean
```lean
def str3 := "String with \
```



```lean
 
```



```lean
             a gap"
```
```

The parser error is:

```lean
<example>:2:0-3:0: unexpected additional newline in string gap
```

#### 20.8.3.2. Interpolated Strings {#manual-string-interpolation}

Preceding a string literal with `s!` causes it to be processed as an *interpolated string*, in which regions of the string surrounded by `{` and `}` characters are parsed and interpreted as Lean expressions.
Interpolated strings are interpreted by appending the string that precedes the interpolation, the expression (with an added call to `toString` surrounding it), and the string that follows the interpolation.

For example:

```lean
example :
s!"1 + 1 = {1 + 1}\n" =
"1 + 1 = " ++ toString (1 + 1) ++ "\n" :=
[rfl]](#manual-rfl-next)
```

Preceding a literal with `m!` causes the interpolation to result in an instance of `MessageData`, the compiler's internal data structure for messages to be shown to users.

#### 20.8.3.3. Raw String Literals {#manual-raw-string-literals}

In raw string literals,  there are no escape sequences or gaps, and each character denotes itself exactly.
Raw string literals are preceded by `r`, followed by zero or more hash characters (`#`) and a double quote `"`.
The string literal is completed at a double quote that is followed by *the same number* of hash characters.
For example, they can be used to avoid the need to double-escape certain characters:

```lean
example : r"\t" = "\\t" := [rfl]](#manual-rfl-next)
[#eval]](#manual-Lean___Parser___Command___eval) r"Write backslash in a string using '\\\\'"
```

The `#eval` yields:

```lean
"Write backslash in a string using '\\\\\\\\'"
```

Including hash marks allows the strings to contain unescaped quotes:

```lean
example :
r#"This is "literally" quoted"# =
"This is \"literally\" quoted" :=
[rfl]](#manual-rfl-next)
```

Adding sufficiently many hash marks allows any raw literal to be written literally:

```lean
example :
r##"This is r#"literally"# quoted"## =
"This is r#\"literally\"# quoted" :=
[rfl]](#manual-rfl-next)
```

### 20.8.4. API Reference {#manual-string-api}

#### 20.8.4.1. Constructing {#manual-string-api-build}

def

```lean
[String.singleton]](#manual-String___singleton) (c : [Char]](#manual-Char___mk)) : [String]](#manual-String___ofByteArray)



[String.singleton]](#manual-String___singleton) (c : [Char]](#manual-Char___mk)) : [String]](#manual-String___ofByteArray)
```

Returns a new string that contains only the character `c`.

Because strings are encoded in UTF-8, the resulting string may take multiple bytes.

Examples:

- `[String.singleton]](#manual-String___singleton) 'L' = "L"`
- `[String.singleton]](#manual-String___singleton) ' ' = " "`
- `[String.singleton]](#manual-String___singleton) '"' = "\""`
- `[String.singleton]](#manual-String___singleton) '𝒫' = "𝒫"`

def

```lean
[String.append]](#manual-String___append) (s : [String]](#manual-String___ofByteArray)) (t : [String]](#manual-String___ofByteArray)) : [String]](#manual-String___ofByteArray)



[String.append]](#manual-String___append) (s : [String]](#manual-String___ofByteArray)) (t : [String]](#manual-String___ofByteArray)) :
  [String]](#manual-String___ofByteArray)
```

Appends two strings. Usually accessed via the `++` operator.

The internal implementation will perform destructive updates if the string is not shared.

Examples:

- `"abc".[append]](#manual-String___append) "def" = "abcdef"`
- `"abc" ++ "def" = "abcdef"`
- `"" ++ "" = ""`

def

```lean
[String.join]](#manual-String___join) (l : [List](https://lean-lang.org/doc/reference/latest/Basic-Types/Linked-Lists/#List___nil) [String]](#manual-String___ofByteArray)) : [String]](#manual-String___ofByteArray)



[String.join]](#manual-String___join) (l : [List](https://lean-lang.org/doc/reference/latest/Basic-Types/Linked-Lists/#List___nil) [String]](#manual-String___ofByteArray)) : [String]](#manual-String___ofByteArray)
```

Appends all the strings in a list of strings, in order.

Use `[String.intercalate]](#manual-String___intercalate)` to place a separator string between the strings in a list.

Examples:

- `[String.join]](#manual-String___join) ["gr", "ee", "n"] = "green"`
- `[String.join]](#manual-String___join) ["b", "", "l", "", "ue"] = "blue"`
- `[String.join]](#manual-String___join) [] = ""`

def

```lean
[String.intercalate]](#manual-String___intercalate) (s : [String]](#manual-String___ofByteArray)) : [List](https://lean-lang.org/doc/reference/latest/Basic-Types/Linked-Lists/#List___nil) [String]](#manual-String___ofByteArray) → [String]](#manual-String___ofByteArray)



[String.intercalate]](#manual-String___intercalate) (s : [String]](#manual-String___ofByteArray)) :
  [List](https://lean-lang.org/doc/reference/latest/Basic-Types/Linked-Lists/#List___nil) [String]](#manual-String___ofByteArray) → [String]](#manual-String___ofByteArray)
```

Appends the strings in a list of strings, placing the separator `s` between each pair.

Examples:

- `", ".[intercalate]](#manual-String___intercalate) ["red", "green", "blue"] = "red, green, blue"`
- `" and ".[intercalate]](#manual-String___intercalate) ["tea", "coffee"] = "tea and coffee"`
- `" | ".[intercalate]](#manual-String___intercalate) ["M", "", "N"] = "M | | N"`

#### 20.8.4.2. Conversions {#manual-string-api-convert}

def

```lean
String.toList (s : [String]](#manual-String___ofByteArray)) : [List](https://lean-lang.org/doc/reference/latest/Basic-Types/Linked-Lists/#List___nil) [Char]](#manual-Char___mk)



String.toList (s : [String]](#manual-String___ofByteArray)) : [List](https://lean-lang.org/doc/reference/latest/Basic-Types/Linked-Lists/#List___nil) [Char]](#manual-Char___mk)
```

Converts a string to a list of characters.

Since strings are represented as dynamic arrays of bytes containing the string encoded using
UTF-8, this operation takes time and space linear in the length of the string.

Examples:

- `"abc".toList = ['a', 'b', 'c']`
- `"".toList = []`
- `"\n".toList = ['\n']`

def

```lean
[String.isNat]](#manual-String___isNat) (s : [String]](#manual-String___ofByteArray)) : [Bool](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)



[String.isNat]](#manual-String___isNat) (s : [String]](#manual-String___ofByteArray)) : [Bool](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)
```

Checks whether the string can be interpreted as the decimal representation of a natural number.

A slice can be interpreted as a decimal natural number if it is not empty and all the characters in
it are digits.

Use `toNat?` or
`toNat!` to convert such a slice to a natural number.

Examples:

- `"".[isNat]](#manual-String___isNat) = [false](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)`
- `"0".[isNat]](#manual-String___isNat) = [true](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)`
- `"5".[isNat]](#manual-String___isNat) = [true](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)`
- `"05".[isNat]](#manual-String___isNat) = [true](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)`
- `"587".[isNat]](#manual-String___isNat) = [true](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)`
- `"-587".[isNat]](#manual-String___isNat) = [false](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)`
- `" 5".[isNat]](#manual-String___isNat) = [false](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)`
- `"2+3".[isNat]](#manual-String___isNat) = [false](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)`
- `"0xff".[isNat]](#manual-String___isNat) = [false](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)`

def

```lean
[String.toNat?]](#manual-String___toNat___) (s : [String]](#manual-String___ofByteArray)) : [Option](https://lean-lang.org/doc/reference/latest/Basic-Types/Optional-Values/#Option___none) [Nat]](#manual-Nat___zero)



[String.toNat?]](#manual-String___toNat___) (s : [String]](#manual-String___ofByteArray)) : [Option](https://lean-lang.org/doc/reference/latest/Basic-Types/Optional-Values/#Option___none) [Nat]](#manual-Nat___zero)
```

Interprets a string as the decimal representation of a natural number, returning it. Returns
`[none](https://lean-lang.org/doc/reference/latest/Basic-Types/Optional-Values/#Option___none)` if the slice does not contain a decimal natural number.

A slice can be interpreted as a decimal natural number if it is not empty and all the characters in
it are digits.

Use `isNat` to check whether `toNat?` would return `[some](https://lean-lang.org/doc/reference/latest/Basic-Types/Optional-Values/#Option___none)`.
`toNat!` is an alternative that panics instead of
returning `[none](https://lean-lang.org/doc/reference/latest/Basic-Types/Optional-Values/#Option___none)` when the slice is not a natural number.

Examples:

- `"".[toNat?]](#manual-String___toNat___) = [none](https://lean-lang.org/doc/reference/latest/Basic-Types/Optional-Values/#Option___none)`
- `"0".[toNat?]](#manual-String___toNat___) = [some](https://lean-lang.org/doc/reference/latest/Basic-Types/Optional-Values/#Option___none) 0`
- `"5".[toNat?]](#manual-String___toNat___) = [some](https://lean-lang.org/doc/reference/latest/Basic-Types/Optional-Values/#Option___none) 5`
- `"587".[toNat?]](#manual-String___toNat___) = [some](https://lean-lang.org/doc/reference/latest/Basic-Types/Optional-Values/#Option___none) 587`
- `"-587".[toNat?]](#manual-String___toNat___) = [none](https://lean-lang.org/doc/reference/latest/Basic-Types/Optional-Values/#Option___none)`
- `" 5".[toNat?]](#manual-String___toNat___) = [none](https://lean-lang.org/doc/reference/latest/Basic-Types/Optional-Values/#Option___none)`
- `"2+3".[toNat?]](#manual-String___toNat___) = [none](https://lean-lang.org/doc/reference/latest/Basic-Types/Optional-Values/#Option___none)`
- `"0xff".[toNat?]](#manual-String___toNat___) = [none](https://lean-lang.org/doc/reference/latest/Basic-Types/Optional-Values/#Option___none)`

def

```lean
[String.toNat!]](#manual-String___toNat___-next) (s : [String]](#manual-String___ofByteArray)) : [Nat]](#manual-Nat___zero)



[String.toNat!]](#manual-String___toNat___-next) (s : [String]](#manual-String___ofByteArray)) : [Nat]](#manual-Nat___zero)
```

Interprets a string as the decimal representation of a natural number, returning it. Panics if the
slice does not contain a decimal natural number.

A slice can be interpreted as a decimal natural number if it is not empty and all the characters in
it are digits.

Use `isNat` to check whether `toNat!` would return a value. `toNat?` is a safer
alternative that returns `[none](https://lean-lang.org/doc/reference/latest/Basic-Types/Optional-Values/#Option___none)` instead of panicking when the string is not a natural number.

Examples:

- `"0".[toNat!]](#manual-String___toNat___-next) = 0`
- `"5".[toNat!]](#manual-String___toNat___-next) = 5`
- `"587".[toNat!]](#manual-String___toNat___-next) = 587`

def

```lean
[String.isInt]](#manual-String___isInt) (s : [String]](#manual-String___ofByteArray)) : [Bool](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)



[String.isInt]](#manual-String___isInt) (s : [String]](#manual-String___ofByteArray)) : [Bool](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)
```

Checks whether the string can be interpreted as the decimal representation of an integer.

A string can be interpreted as a decimal integer if it only consists of at least one decimal digit
and optionally `-` in front. Leading `+` characters are not allowed.

Use `[String.toInt?]](#manual-String___toInt___)` or `[String.toInt!]](#manual-String___toInt___-next)` to convert
such a string to an integer.

Examples:

- `"".[isInt]](#manual-String___isInt) = [false](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)`
- `"-".[isInt]](#manual-String___isInt) = [false](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)`
- `"0".[isInt]](#manual-String___isInt) = [true](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)`
- `"-0".[isInt]](#manual-String___isInt) = [true](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)`
- `"5".[isInt]](#manual-String___isInt) = [true](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)`
- `"587".[isInt]](#manual-String___isInt) = [true](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)`
- `"-587".[isInt]](#manual-String___isInt) = [true](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)`
- `"+587".[isInt]](#manual-String___isInt) = [false](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)`
- `" 5".[isInt]](#manual-String___isInt) = [false](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)`
- `"2-3".[isInt]](#manual-String___isInt) = [false](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)`
- `"0xff".[isInt]](#manual-String___isInt) = [false](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)`

def

```lean
[String.toInt?]](#manual-String___toInt___) (s : [String]](#manual-String___ofByteArray)) : [Option](https://lean-lang.org/doc/reference/latest/Basic-Types/Optional-Values/#Option___none) [Int]](#manual-Int___ofNat)



[String.toInt?]](#manual-String___toInt___) (s : [String]](#manual-String___ofByteArray)) : [Option](https://lean-lang.org/doc/reference/latest/Basic-Types/Optional-Values/#Option___none) [Int]](#manual-Int___ofNat)
```

Interprets a string as the decimal representation of an integer, returning it. Returns `[none](https://lean-lang.org/doc/reference/latest/Basic-Types/Optional-Values/#Option___none)`
if the string does not contain a decimal integer.

A string can be interpreted as a decimal integer if it only consists of at least one decimal digit
and optionally `-` in front. Leading `+` characters are not allowed.

Use `[String.isInt]](#manual-String___isInt)` to check whether `[String.toInt?]](#manual-String___toInt___)`
would return `[some](https://lean-lang.org/doc/reference/latest/Basic-Types/Optional-Values/#Option___none)`. `[String.toInt!]](#manual-String___toInt___-next)` is an
alternative that panics instead of returning `[none](https://lean-lang.org/doc/reference/latest/Basic-Types/Optional-Values/#Option___none)` when the string is not an integer.

Examples:

- `"".[toInt?]](#manual-String___toInt___) = [none](https://lean-lang.org/doc/reference/latest/Basic-Types/Optional-Values/#Option___none)`
- `"-".[toInt?]](#manual-String___toInt___) = [none](https://lean-lang.org/doc/reference/latest/Basic-Types/Optional-Values/#Option___none)`
- `"0".[toInt?]](#manual-String___toInt___) = [some](https://lean-lang.org/doc/reference/latest/Basic-Types/Optional-Values/#Option___none) 0`
- `"5".[toInt?]](#manual-String___toInt___) = [some](https://lean-lang.org/doc/reference/latest/Basic-Types/Optional-Values/#Option___none) 5`
- `"-5".[toInt?]](#manual-String___toInt___) = [some](https://lean-lang.org/doc/reference/latest/Basic-Types/Optional-Values/#Option___none) (-5)`
- `"587".[toInt?]](#manual-String___toInt___) = [some](https://lean-lang.org/doc/reference/latest/Basic-Types/Optional-Values/#Option___none) 587`
- `"-587".[toInt?]](#manual-String___toInt___) = [some](https://lean-lang.org/doc/reference/latest/Basic-Types/Optional-Values/#Option___none) (-587)`
- `" 5".[toInt?]](#manual-String___toInt___) = [none](https://lean-lang.org/doc/reference/latest/Basic-Types/Optional-Values/#Option___none)`
- `"2-3".[toInt?]](#manual-String___toInt___) = [none](https://lean-lang.org/doc/reference/latest/Basic-Types/Optional-Values/#Option___none)`
- `"0xff".[toInt?]](#manual-String___toInt___) = [none](https://lean-lang.org/doc/reference/latest/Basic-Types/Optional-Values/#Option___none)`

def

```lean
[String.toInt!]](#manual-String___toInt___-next) (s : [String]](#manual-String___ofByteArray)) : [Int]](#manual-Int___ofNat)



[String.toInt!]](#manual-String___toInt___-next) (s : [String]](#manual-String___ofByteArray)) : [Int]](#manual-Int___ofNat)
```

Interprets a string as the decimal representation of an integer, returning it. Panics if the string
does not contain a decimal integer.

A string can be interpreted as a decimal integer if it only consists of at least one decimal digit
and optionally `-` in front. Leading `+` characters are not allowed.

Use `[String.isInt]](#manual-String___isInt)` to check whether `[String.toInt!]](#manual-String___toInt___-next)` would return a value.
`[String.toInt?]](#manual-String___toInt___)` is a safer alternative that returns `[none](https://lean-lang.org/doc/reference/latest/Basic-Types/Optional-Values/#Option___none)` instead of panicking when the
string is not an integer.

Examples:

- `"0".[toInt!]](#manual-String___toInt___-next) = 0`
- `"5".[toInt!]](#manual-String___toInt___-next) = 5`
- `"587".[toInt!]](#manual-String___toInt___-next) = 587`
- `"-587".[toInt!]](#manual-String___toInt___-next) = -587`

def

```lean
[String.toFormat]](#manual-String___toFormat) (s : [String]](#manual-String___ofByteArray)) : [Std.Format]](#manual-Std___Format___nil)



[String.toFormat]](#manual-String___toFormat) (s : [String]](#manual-String___ofByteArray)) : [Std.Format]](#manual-Std___Format___nil)
```

Converts a string to a pretty-printer document, replacing newlines in the string with
`[Std.Format.line]](#manual-Std___Format___nil)`.

#### 20.8.4.3. Properties {#manual-string-api-props}

def

```lean
[String.isEmpty]](#manual-String___isEmpty) (s : [String]](#manual-String___ofByteArray)) : [Bool](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)



[String.isEmpty]](#manual-String___isEmpty) (s : [String]](#manual-String___ofByteArray)) : [Bool](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)
```

Checks whether a string is empty.

Empty strings are equal to `""` and have length and end position `0`.

Examples:

- `"".[isEmpty]](#manual-String___isEmpty) = [true](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)`
- `"empty".[isEmpty]](#manual-String___isEmpty) = [false](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)`
- `" ".[isEmpty]](#manual-String___isEmpty) = [false](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)`

def

```lean
[String.length]](#manual-String___length) (b : [String]](#manual-String___ofByteArray)) : [Nat]](#manual-Nat___zero)



[String.length]](#manual-String___length) (b : [String]](#manual-String___ofByteArray)) : [Nat]](#manual-Nat___zero)
```

Returns the length of a string in Unicode code points.

Examples:

- `"".[length]](#manual-String___length) = 0`
- `"abc".[length]](#manual-String___length) = 3`
- `"L∃∀N".[length]](#manual-String___length) = 4`

#### 20.8.4.4. Positions {#manual-string-api-valid-pos}

structure

```lean
[String.Pos]](#manual-String___Pos___mk) (s : [String]](#manual-String___ofByteArray)) : Type



[String.Pos]](#manual-String___Pos___mk) (s : [String]](#manual-String___ofByteArray)) : Type
```

A `Pos s` is a byte offset in `s` together with a proof that this position is at a UTF-8
character boundary.

Constructor

```lean
[String.Pos.mk]](#manual-String___Pos___mk)
```

Fields

```lean
offset : [String.Pos.Raw]](#manual-String___Pos___Raw___mk)
```

The underlying byte offset of the `Pos`.

```lean
isValid : String.Pos.Raw.IsValid s self.[offset]](#manual-String___Pos___mk)
```

The proof that `offset` is valid for the string `s`.

##### 20.8.4.4.1. In Strings {#manual-The-Lean-Language-Reference--Basic-Types--Strings--API-Reference--Positions--In-Strings}

def

```lean
[String.startPos]](#manual-String___startPos) (s : [String]](#manual-String___ofByteArray)) : s.[Pos]](#manual-String___Pos___mk)



[String.startPos]](#manual-String___startPos) (s : [String]](#manual-String___ofByteArray)) : s.[Pos]](#manual-String___Pos___mk)
```

The start position of `s`, as an `s.[Pos]](#manual-String___Pos___mk)`.

def

```lean
[String.endPos]](#manual-String___endPos) (s : [String]](#manual-String___ofByteArray)) : s.[Pos]](#manual-String___Pos___mk)



[String.endPos]](#manual-String___endPos) (s : [String]](#manual-String___ofByteArray)) : s.[Pos]](#manual-String___Pos___mk)
```

The past-the-end position of `s`, as an `s.[Pos]](#manual-String___Pos___mk)`.

def

```lean
[String.pos]](#manual-String___pos) (s : [String]](#manual-String___ofByteArray)) (off : [String.Pos.Raw]](#manual-String___Pos___Raw___mk))
  (h : String.Pos.Raw.IsValid s off) : s.[Pos]](#manual-String___Pos___mk)



[String.pos]](#manual-String___pos) (s : [String]](#manual-String___ofByteArray))
  (off : [String.Pos.Raw]](#manual-String___Pos___Raw___mk))
  (h : String.Pos.Raw.IsValid s off) :
  s.[Pos]](#manual-String___Pos___mk)
```

Constructs a valid position on `s` from a position and a proof that it is valid.

def

```lean
[String.pos?]](#manual-String___pos___) (s : [String]](#manual-String___ofByteArray)) (off : [String.Pos.Raw]](#manual-String___Pos___Raw___mk)) : [Option](https://lean-lang.org/doc/reference/latest/Basic-Types/Optional-Values/#Option___none) s.[Pos]](#manual-String___Pos___mk)



[String.pos?]](#manual-String___pos___) (s : [String]](#manual-String___ofByteArray))
  (off : [String.Pos.Raw]](#manual-String___Pos___Raw___mk)) : [Option](https://lean-lang.org/doc/reference/latest/Basic-Types/Optional-Values/#Option___none) s.[Pos]](#manual-String___Pos___mk)
```

Constructs a valid position on `s` from a position, returning `[none](https://lean-lang.org/doc/reference/latest/Basic-Types/Optional-Values/#Option___none)` if the position is not valid.

def

```lean
[String.pos!]](#manual-String___pos___-next) (s : [String]](#manual-String___ofByteArray)) (off : [String.Pos.Raw]](#manual-String___Pos___Raw___mk)) : s.[Pos]](#manual-String___Pos___mk)



[String.pos!]](#manual-String___pos___-next) (s : [String]](#manual-String___ofByteArray))
  (off : [String.Pos.Raw]](#manual-String___Pos___Raw___mk)) : s.[Pos]](#manual-String___Pos___mk)
```

Constructs a valid position `s` from a position, panicking if the position is not valid.

def

```lean
[String.extract]](#manual-String___extract) {s : [String]](#manual-String___ofByteArray)} (b e : s.[Pos]](#manual-String___Pos___mk)) : [String]](#manual-String___ofByteArray)



[String.extract]](#manual-String___extract) {s : [String]](#manual-String___ofByteArray)}
  (b e : s.[Pos]](#manual-String___Pos___mk)) : [String]](#manual-String___ofByteArray)
```

Copies a region of a string to a new string.

The region of `s` from `b` (inclusive) to `e` (exclusive) is copied to a newly-allocated `[String]](#manual-String___ofByteArray)`.

If `b`'s offset is greater than or equal to that of `e`, then the resulting string is `""`.

If possible, prefer `String.slice`, which avoids the allocation.

##### 20.8.4.4.2. Lookups {#manual-The-Lean-Language-Reference--Basic-Types--Strings--API-Reference--Positions--Lookups}

def

```lean
[String.Pos.get]](#manual-String___Pos___get) {s : [String]](#manual-String___ofByteArray)} (pos : s.[Pos]](#manual-String___Pos___mk)) (h : pos ≠ s.[endPos]](#manual-String___endPos)) : [Char]](#manual-Char___mk)



[String.Pos.get]](#manual-String___Pos___get) {s : [String]](#manual-String___ofByteArray)} (pos : s.[Pos]](#manual-String___Pos___mk))
  (h : pos ≠ s.[endPos]](#manual-String___endPos)) : [Char]](#manual-Char___mk)
```

Returns the character at the position `pos` of a string, taking a proof that `p` is not the
past-the-end position.

This function is overridden with an efficient implementation in runtime code.

Examples:

- `("abc".[pos]](#manual-String___pos) ⟨1⟩ (by [decide]](#manual-decide))).[get]](#manual-String___Pos___get) (by [decide]](#manual-decide)) = 'b'`
- `("L∃∀N".[pos]](#manual-String___pos) ⟨1⟩ (by [decide]](#manual-decide))).[get]](#manual-String___Pos___get) (by [decide]](#manual-decide)) = '∃'`

def

```lean
[String.Pos.get!]](#manual-String___Pos___get___) {s : [String]](#manual-String___ofByteArray)} (pos : s.[Pos]](#manual-String___Pos___mk)) : [Char]](#manual-Char___mk)



[String.Pos.get!]](#manual-String___Pos___get___) {s : [String]](#manual-String___ofByteArray)}
  (pos : s.[Pos]](#manual-String___Pos___mk)) : [Char]](#manual-Char___mk)
```

Returns the character at the position `pos` of a string, or panics if the position is the
past-the-end position.

This function is overridden with an efficient implementation in runtime code.

def

```lean
[String.Pos.get?]](#manual-String___Pos___get___-next) {s : [String]](#manual-String___ofByteArray)} (pos : s.[Pos]](#manual-String___Pos___mk)) : [Option](https://lean-lang.org/doc/reference/latest/Basic-Types/Optional-Values/#Option___none) [Char]](#manual-Char___mk)



[String.Pos.get?]](#manual-String___Pos___get___-next) {s : [String]](#manual-String___ofByteArray)}
  (pos : s.[Pos]](#manual-String___Pos___mk)) : [Option](https://lean-lang.org/doc/reference/latest/Basic-Types/Optional-Values/#Option___none) [Char]](#manual-Char___mk)
```

Returns the character at the position `pos` of a string, or `[none](https://lean-lang.org/doc/reference/latest/Basic-Types/Optional-Values/#Option___none)` if the position is the
past-the-end position.

This function is overridden with an efficient implementation in runtime code.

def

```lean
[String.Pos.set]](#manual-String___Pos___set) {s : [String]](#manual-String___ofByteArray)} (p : s.[Pos]](#manual-String___Pos___mk)) (c : [Char]](#manual-Char___mk)) (hp : p ≠ s.[endPos]](#manual-String___endPos)) :
  [String]](#manual-String___ofByteArray)



[String.Pos.set]](#manual-String___Pos___set) {s : [String]](#manual-String___ofByteArray)} (p : s.[Pos]](#manual-String___Pos___mk))
  (c : [Char]](#manual-Char___mk)) (hp : p ≠ s.[endPos]](#manual-String___endPos)) : [String]](#manual-String___ofByteArray)
```

Replaces the character at a specified position in a string with a new character.

If both the replacement character and the replaced character are 7-bit ASCII characters and the
string is not shared, then it is updated in-place and not copied.

Examples:

- `("abc".[pos]](#manual-String___pos) ⟨1⟩ (by [decide]](#manual-decide))).[set]](#manual-String___Pos___set) 'B' (by [decide]](#manual-decide)) = "aBc"`
- `("L∃∀N".[pos]](#manual-String___pos) ⟨4⟩ (by [decide]](#manual-decide))).[set]](#manual-String___Pos___set) 'X' (by [decide]](#manual-decide)) = "L∃XN"`

##### 20.8.4.4.3. Modifications {#manual-The-Lean-Language-Reference--Basic-Types--Strings--API-Reference--Positions--Modifications}

def

```lean
[String.Pos.modify]](#manual-String___Pos___modify) {s : [String]](#manual-String___ofByteArray)} (p : s.[Pos]](#manual-String___Pos___mk)) (f : [Char]](#manual-Char___mk) → [Char]](#manual-Char___mk))
  (hp : p ≠ s.[endPos]](#manual-String___endPos)) : [String]](#manual-String___ofByteArray)



[String.Pos.modify]](#manual-String___Pos___modify) {s : [String]](#manual-String___ofByteArray)} (p : s.[Pos]](#manual-String___Pos___mk))
  (f : [Char]](#manual-Char___mk) → [Char]](#manual-Char___mk)) (hp : p ≠ s.[endPos]](#manual-String___endPos)) :
  [String]](#manual-String___ofByteArray)
```

Replaces the character at position `p` in the string `s` with the result of applying `f` to that
character.

If both the replacement character and the replaced character are 7-bit ASCII characters and the
string is not shared, then it is updated in-place and not copied.

Examples:

- `("abc".[pos]](#manual-String___pos) ⟨1⟩ (by [decide]](#manual-decide))).[modify]](#manual-String___Pos___modify) [Char.toUpper]](#manual-Char___toUpper) (by [decide]](#manual-decide)) = "aBc"`

def

```lean
[String.Pos.byte]](#manual-String___Pos___byte) {s : [String]](#manual-String___ofByteArray)} (pos : s.[Pos]](#manual-String___Pos___mk)) (h : pos ≠ s.[endPos]](#manual-String___endPos)) : [UInt8]](#manual-UInt8___ofBitVec)



[String.Pos.byte]](#manual-String___Pos___byte) {s : [String]](#manual-String___ofByteArray)} (pos : s.[Pos]](#manual-String___Pos___mk))
  (h : pos ≠ s.[endPos]](#manual-String___endPos)) : [UInt8]](#manual-UInt8___ofBitVec)
```

Returns the byte at the position `pos` of a string.

##### 20.8.4.4.4. Adjustment {#manual-The-Lean-Language-Reference--Basic-Types--Strings--API-Reference--Positions--Adjustment}

def

```lean
[String.Pos.prev]](#manual-String___Pos___prev) {s : [String]](#manual-String___ofByteArray)} (pos : s.[Pos]](#manual-String___Pos___mk)) (h : pos ≠ s.[startPos]](#manual-String___startPos)) :
  s.[Pos]](#manual-String___Pos___mk)



[String.Pos.prev]](#manual-String___Pos___prev) {s : [String]](#manual-String___ofByteArray)} (pos : s.[Pos]](#manual-String___Pos___mk))
  (h : pos ≠ s.[startPos]](#manual-String___startPos)) : s.[Pos]](#manual-String___Pos___mk)
```

Returns the previous valid position before the given position, given a proof that the position
is not the start position, which guarantees that such a position exists.

def

```lean
[String.Pos.prev!]](#manual-String___Pos___prev___) {s : [String]](#manual-String___ofByteArray)} (pos : s.[Pos]](#manual-String___Pos___mk)) : s.[Pos]](#manual-String___Pos___mk)



[String.Pos.prev!]](#manual-String___Pos___prev___) {s : [String]](#manual-String___ofByteArray)}
  (pos : s.[Pos]](#manual-String___Pos___mk)) : s.[Pos]](#manual-String___Pos___mk)
```

Returns the previous valid position before the given position, or panics if the position is
the start position.

def

```lean
[String.Pos.prev?]](#manual-String___Pos___prev___-next) {s : [String]](#manual-String___ofByteArray)} (pos : s.[Pos]](#manual-String___Pos___mk)) : [Option](https://lean-lang.org/doc/reference/latest/Basic-Types/Optional-Values/#Option___none) s.[Pos]](#manual-String___Pos___mk)



[String.Pos.prev?]](#manual-String___Pos___prev___-next) {s : [String]](#manual-String___ofByteArray)}
  (pos : s.[Pos]](#manual-String___Pos___mk)) : [Option](https://lean-lang.org/doc/reference/latest/Basic-Types/Optional-Values/#Option___none) s.[Pos]](#manual-String___Pos___mk)
```

Returns the previous valid position before the given position, or `[none](https://lean-lang.org/doc/reference/latest/Basic-Types/Optional-Values/#Option___none)` if the position is
the start position.

def

```lean
[String.Pos.next]](#manual-String___Pos___next) {s : [String]](#manual-String___ofByteArray)} (pos : s.[Pos]](#manual-String___Pos___mk)) (h : pos ≠ s.[endPos]](#manual-String___endPos)) : s.[Pos]](#manual-String___Pos___mk)



[String.Pos.next]](#manual-String___Pos___next) {s : [String]](#manual-String___ofByteArray)} (pos : s.[Pos]](#manual-String___Pos___mk))
  (h : pos ≠ s.[endPos]](#manual-String___endPos)) : s.[Pos]](#manual-String___Pos___mk)
```

Advances a valid position on a string to the next valid position, given a proof that the
position is not the past-the-end position, which guarantees that such a position exists.

def

```lean
[String.Pos.next!]](#manual-String___Pos___next___) {s : [String]](#manual-String___ofByteArray)} (pos : s.[Pos]](#manual-String___Pos___mk)) : s.[Pos]](#manual-String___Pos___mk)



[String.Pos.next!]](#manual-String___Pos___next___) {s : [String]](#manual-String___ofByteArray)}
  (pos : s.[Pos]](#manual-String___Pos___mk)) : s.[Pos]](#manual-String___Pos___mk)
```

Advances a valid position on a string to the next valid position, or panics if the given
position is the past-the-end position.

def

```lean
[String.Pos.next?]](#manual-String___Pos___next___-next) {s : [String]](#manual-String___ofByteArray)} (pos : s.[Pos]](#manual-String___Pos___mk)) : [Option](https://lean-lang.org/doc/reference/latest/Basic-Types/Optional-Values/#Option___none) s.[Pos]](#manual-String___Pos___mk)



[String.Pos.next?]](#manual-String___Pos___next___-next) {s : [String]](#manual-String___ofByteArray)}
  (pos : s.[Pos]](#manual-String___Pos___mk)) : [Option](https://lean-lang.org/doc/reference/latest/Basic-Types/Optional-Values/#Option___none) s.[Pos]](#manual-String___Pos___mk)
```

Advances a valid position on a string to the next valid position, or returns `[none](https://lean-lang.org/doc/reference/latest/Basic-Types/Optional-Values/#Option___none)` if the
given position is the past-the-end position.

##### 20.8.4.4.5. Other Strings {#manual-The-Lean-Language-Reference--Basic-Types--Strings--API-Reference--Positions--Other-Strings}

def

```lean
[String.Pos.cast]](#manual-String___Pos___cast) {s t : [String]](#manual-String___ofByteArray)} (pos : s.[Pos]](#manual-String___Pos___mk)) (h : s [=]](#manual-Eq___refl) t) : t.[Pos]](#manual-String___Pos___mk)



[String.Pos.cast]](#manual-String___Pos___cast) {s t : [String]](#manual-String___ofByteArray)}
  (pos : s.[Pos]](#manual-String___Pos___mk)) (h : s [=]](#manual-Eq___refl) t) : t.[Pos]](#manual-String___Pos___mk)
```

Constructs a valid position on `t` from a valid position on `s` and a proof that `s = t`.

def

```lean
[String.Pos.ofCopy]](#manual-String___Pos___ofCopy) {s : [String.Slice]](#manual-String___Slice___mk)} (pos : s.[copy]](#manual-String___Slice___copy).[Pos]](#manual-String___Pos___mk)) : s.[Pos]](#manual-String___Slice___Pos___mk)



[String.Pos.ofCopy]](#manual-String___Pos___ofCopy) {s : [String.Slice]](#manual-String___Slice___mk)}
  (pos : s.[copy]](#manual-String___Slice___copy).[Pos]](#manual-String___Pos___mk)) : s.[Pos]](#manual-String___Slice___Pos___mk)
```

Given a slice `s` and a position on `s.[copy]](#manual-String___Slice___copy)`, obtain the corresponding position on `s`.

def

```lean
[String.Pos.toSetOfLE]](#manual-String___Pos___toSetOfLE) {s : [String]](#manual-String___ofByteArray)} (q p : s.[Pos]](#manual-String___Pos___mk)) (c : [Char]](#manual-Char___mk))
  (hp : p ≠ s.[endPos]](#manual-String___endPos)) (hpq : q [≤]](#manual-LE___mk) p) : (p.[set]](#manual-String___Pos___set) c hp).[Pos]](#manual-String___Pos___mk)



[String.Pos.toSetOfLE]](#manual-String___Pos___toSetOfLE) {s : [String]](#manual-String___ofByteArray)}
  (q p : s.[Pos]](#manual-String___Pos___mk)) (c : [Char]](#manual-Char___mk))
  (hp : p ≠ s.[endPos]](#manual-String___endPos)) (hpq : q [≤]](#manual-LE___mk) p) :
  (p.[set]](#manual-String___Pos___set) c hp).[Pos]](#manual-String___Pos___mk)
```

Given a valid position in a string, obtain the corresponding position after setting a character on
that string, provided that the position was before the changed position.

def

```lean
[String.Pos.toModifyOfLE]](#manual-String___Pos___toModifyOfLE) {s : [String]](#manual-String___ofByteArray)} (q p : s.[Pos]](#manual-String___Pos___mk)) (f : [Char]](#manual-Char___mk) → [Char]](#manual-Char___mk))
  (hp : p ≠ s.[endPos]](#manual-String___endPos)) (hpq : q [≤]](#manual-LE___mk) p) : (p.[modify]](#manual-String___Pos___modify) f hp).[Pos]](#manual-String___Pos___mk)



[String.Pos.toModifyOfLE]](#manual-String___Pos___toModifyOfLE) {s : [String]](#manual-String___ofByteArray)}
  (q p : s.[Pos]](#manual-String___Pos___mk)) (f : [Char]](#manual-Char___mk) → [Char]](#manual-Char___mk))
  (hp : p ≠ s.[endPos]](#manual-String___endPos)) (hpq : q [≤]](#manual-LE___mk) p) :
  (p.[modify]](#manual-String___Pos___modify) f hp).[Pos]](#manual-String___Pos___mk)
```

Given a valid position in a string, obtain the corresponding position after modifying a character
in that string, provided that the position was before the changed position.

def

```lean
[String.Pos.toSlice]](#manual-String___Pos___toSlice) {s : [String]](#manual-String___ofByteArray)} (pos : s.[Pos]](#manual-String___Pos___mk)) : s.[toSlice]](#manual-String___toSlice).[Pos]](#manual-String___Slice___Pos___mk)



[String.Pos.toSlice]](#manual-String___Pos___toSlice) {s : [String]](#manual-String___ofByteArray)}
  (pos : s.[Pos]](#manual-String___Pos___mk)) : s.[toSlice]](#manual-String___toSlice).[Pos]](#manual-String___Slice___Pos___mk)
```

Turns a valid position on the string `s` into a valid position on the slice `s.[toSlice]](#manual-String___toSlice)`.

#### 20.8.4.5. Raw Positions {#manual-string-api-pos}

structure

```lean
[String.Pos.Raw]](#manual-String___Pos___Raw___mk) : Type



[String.Pos.Raw]](#manual-String___Pos___Raw___mk) : Type
```

A byte position in a `[String]](#manual-String___ofByteArray)`, according to its UTF-8 encoding.

Character positions (counting the Unicode code points rather than bytes) are represented by plain
`[Nat]](#manual-Nat___zero)`s. Indexing a `[String]](#manual-String___ofByteArray)` by a `[String.Pos.Raw]](#manual-String___Pos___Raw___mk)` takes constant time, while character positions need to
be translated internally to byte positions, which takes linear time.

A byte position `p` is *valid* for a string `s` if `0 ≤ p ≤ s.rawEndPos` and `p` lies on a UTF-8
character boundary, see `[String.Pos]](#manual-String___Pos___mk).IsValid`.

There is another type, `[String.Pos]](#manual-String___Pos___mk)`, which bundles the validity predicate. Using `[String.Pos]](#manual-String___Pos___mk)`
instead of `[String.Pos.Raw]](#manual-String___Pos___Raw___mk)` is recommended because it will lead to less error handling and fewer edge cases.

Constructor

```lean
[String.Pos.Raw.mk]](#manual-String___Pos___Raw___mk)
```

Fields

```lean
byteIdx : [Nat]](#manual-Nat___zero)
```

Get the underlying byte index of a `[String.Pos.Raw]](#manual-String___Pos___Raw___mk)`

##### 20.8.4.5.1. Byte Position {#manual-The-Lean-Language-Reference--Basic-Types--Strings--API-Reference--Raw-Positions--Byte-Position}

def

```lean
[String.Pos.Raw.offsetOfPos]](#manual-String___Pos___Raw___offsetOfPos) (s : [String]](#manual-String___ofByteArray)) (pos : [String.Pos.Raw]](#manual-String___Pos___Raw___mk)) : [Nat]](#manual-Nat___zero)



[String.Pos.Raw.offsetOfPos]](#manual-String___Pos___Raw___offsetOfPos) (s : [String]](#manual-String___ofByteArray))
  (pos : [String.Pos.Raw]](#manual-String___Pos___Raw___mk)) : [Nat]](#manual-Nat___zero)
```

Returns the character index that corresponds to the provided position (i.e. UTF-8 byte index) in a
string.

If the position is at the end of the string, then the string's length in characters is returned. If
the position is invalid due to pointing at the middle of a UTF-8 byte sequence, then the character
index of the next character after the position is returned.

Examples:

- `"L∃∀N".offsetOfPos ⟨0⟩ = 0`
- `"L∃∀N".offsetOfPos ⟨1⟩ = 1`
- `"L∃∀N".offsetOfPos ⟨2⟩ = 2`
- `"L∃∀N".offsetOfPos ⟨4⟩ = 2`
- `"L∃∀N".offsetOfPos ⟨5⟩ = 3`
- `"L∃∀N".offsetOfPos ⟨50⟩ = 4`

##### 20.8.4.5.2. Validity {#manual-The-Lean-Language-Reference--Basic-Types--Strings--API-Reference--Raw-Positions--Validity}

def

```lean
[String.Pos.Raw.isValid]](#manual-String___Pos___Raw___isValid) (s : [String]](#manual-String___ofByteArray)) (p : [String.Pos.Raw]](#manual-String___Pos___Raw___mk)) : [Bool](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)



[String.Pos.Raw.isValid]](#manual-String___Pos___Raw___isValid) (s : [String]](#manual-String___ofByteArray))
  (p : [String.Pos.Raw]](#manual-String___Pos___Raw___mk)) : [Bool](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)
```

Returns `[true](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)` if `p` is a valid UTF-8 position in the string `s`.

This means that `p ≤ s.[rawEndPos]](#manual-String___rawEndPos)` and `p` lies on a UTF-8 character boundary. At runtime, this
operation takes constant time.

Examples:

- `[String.Pos.isValid]](#manual-String___Pos___mk) "abc" ⟨0⟩ = [true](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)`
- `[String.Pos.isValid]](#manual-String___Pos___mk) "abc" ⟨1⟩ = [true](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)`
- `[String.Pos.isValid]](#manual-String___Pos___mk) "abc" ⟨3⟩ = [true](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)`
- `[String.Pos.isValid]](#manual-String___Pos___mk) "abc" ⟨4⟩ = [false](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)`
- `[String.Pos.isValid]](#manual-String___Pos___mk) "𝒫(A)" ⟨0⟩ = [true](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)`
- `[String.Pos.isValid]](#manual-String___Pos___mk) "𝒫(A)" ⟨1⟩ = [false](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)`
- `[String.Pos.isValid]](#manual-String___Pos___mk) "𝒫(A)" ⟨2⟩ = [false](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)`
- `[String.Pos.isValid]](#manual-String___Pos___mk) "𝒫(A)" ⟨3⟩ = [false](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)`
- `[String.Pos.isValid]](#manual-String___Pos___mk) "𝒫(A)" ⟨4⟩ = [true](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)`

def

```lean
[String.Pos.Raw.isValidForSlice]](#manual-String___Pos___Raw___isValidForSlice) (s : [String.Slice]](#manual-String___Slice___mk)) (p : [String.Pos.Raw]](#manual-String___Pos___Raw___mk)) :
  [Bool](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)



[String.Pos.Raw.isValidForSlice]](#manual-String___Pos___Raw___isValidForSlice)
  (s : [String.Slice]](#manual-String___Slice___mk))
  (p : [String.Pos.Raw]](#manual-String___Pos___Raw___mk)) : [Bool](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)
```

Efficiently checks whether a position is at a UTF-8 character boundary of the slice `s`.

##### 20.8.4.5.3. Boundaries {#manual-The-Lean-Language-Reference--Basic-Types--Strings--API-Reference--Raw-Positions--Boundaries}

def

```lean
[String.rawEndPos]](#manual-String___rawEndPos) (s : [String]](#manual-String___ofByteArray)) : [String.Pos.Raw]](#manual-String___Pos___Raw___mk)



[String.rawEndPos]](#manual-String___rawEndPos) (s : [String]](#manual-String___ofByteArray)) :
  [String.Pos.Raw]](#manual-String___Pos___Raw___mk)
```

A UTF-8 byte position that points at the end of a string, just after the last character.

- `"abc".[rawEndPos]](#manual-String___rawEndPos) = ⟨3⟩`
- `"L∃∀N".[rawEndPos]](#manual-String___rawEndPos) = ⟨8⟩`

def

```lean
[String.Pos.Raw.atEnd]](#manual-String___Pos___Raw___atEnd) : [String]](#manual-String___ofByteArray) → [String.Pos.Raw]](#manual-String___Pos___Raw___mk) → [Bool](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)



[String.Pos.Raw.atEnd]](#manual-String___Pos___Raw___atEnd) :
  [String]](#manual-String___ofByteArray) → [String.Pos.Raw]](#manual-String___Pos___Raw___mk) → [Bool](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)
```

Returns `[true](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)` if a specified byte position is greater than or equal to the position which points to
the end of a string. Otherwise, returns `[false](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)`.

Examples:

- `(0 |> "abc".next |> "abc".next |> "abc".atEnd) = [false](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)`
- `(0 |> "abc".next |> "abc".next |> "abc".next |> "abc".next |> "abc".atEnd) = [true](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)`
- `(0 |> "L∃∀N".next |> "L∃∀N".next |> "L∃∀N".next |> "L∃∀N".atEnd) = [false](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)`
- `(0 |> "L∃∀N".next |> "L∃∀N".next |> "L∃∀N".next |> "L∃∀N".next |> "L∃∀N".atEnd) = [true](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)`
- `"abc".atEnd ⟨4⟩ = [true](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)`
- `"L∃∀N".atEnd ⟨7⟩ = [false](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)`
- `"L∃∀N".atEnd ⟨8⟩ = [true](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)`

##### 20.8.4.5.4. Comparisons {#manual-The-Lean-Language-Reference--Basic-Types--Strings--API-Reference--Raw-Positions--Comparisons}

def

```lean
[String.Pos.Raw.min]](#manual-String___Pos___Raw___min) (p₁ p₂ : [String.Pos.Raw]](#manual-String___Pos___Raw___mk)) : [String.Pos.Raw]](#manual-String___Pos___Raw___mk)



[String.Pos.Raw.min]](#manual-String___Pos___Raw___min)
  (p₁ p₂ : [String.Pos.Raw]](#manual-String___Pos___Raw___mk)) :
  [String.Pos.Raw]](#manual-String___Pos___Raw___mk)
```

Returns either `p₁` or `p₂`, whichever has the least byte index.

def

```lean
[String.Pos.Raw.byteDistance]](#manual-String___Pos___Raw___byteDistance) (lo hi : [String.Pos.Raw]](#manual-String___Pos___Raw___mk)) : [Nat]](#manual-Nat___zero)



[String.Pos.Raw.byteDistance]](#manual-String___Pos___Raw___byteDistance)
  (lo hi : [String.Pos.Raw]](#manual-String___Pos___Raw___mk)) : [Nat]](#manual-Nat___zero)
```

Returns the size of the byte slice delineated by the positions `lo` and `hi`.

def

```lean
[String.Pos.Raw.substrEq]](#manual-String___Pos___Raw___substrEq) (s1 : [String]](#manual-String___ofByteArray)) (pos1 : [String.Pos.Raw]](#manual-String___Pos___Raw___mk))
  (s2 : [String]](#manual-String___ofByteArray)) (pos2 : [String.Pos.Raw]](#manual-String___Pos___Raw___mk)) (sz : [Nat]](#manual-Nat___zero)) : [Bool](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)



[String.Pos.Raw.substrEq]](#manual-String___Pos___Raw___substrEq) (s1 : [String]](#manual-String___ofByteArray))
  (pos1 : [String.Pos.Raw]](#manual-String___Pos___Raw___mk)) (s2 : [String]](#manual-String___ofByteArray))
  (pos2 : [String.Pos.Raw]](#manual-String___Pos___Raw___mk)) (sz : [Nat]](#manual-Nat___zero)) :
  [Bool](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)
```

Checks whether substrings of two strings are equal. Substrings are indicated by their starting
positions and a size in *UTF-8 bytes*. Returns `[false](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)` if the indicated substring does not exist in
either string.

This is a legacy function. The recommended alternative is to construct slices representing the
strings to be compared and use the `[BEq]](#manual-BEq___mk)` instance of `[String.Slice]](#manual-String___Slice___mk)`.

##### 20.8.4.5.5. Adjustment {#manual-The-Lean-Language-Reference--Basic-Types--Strings--API-Reference--Raw-Positions--Adjustment}

def

```lean
[String.Pos.Raw.prev]](#manual-String___Pos___Raw___prev) : [String]](#manual-String___ofByteArray) → [String.Pos.Raw]](#manual-String___Pos___Raw___mk) → [String.Pos.Raw]](#manual-String___Pos___Raw___mk)



[String.Pos.Raw.prev]](#manual-String___Pos___Raw___prev) :
  [String]](#manual-String___ofByteArray) → [String.Pos.Raw]](#manual-String___Pos___Raw___mk) → [String.Pos.Raw]](#manual-String___Pos___Raw___mk)
```

Returns the position in a string before a specified position, `p`. If `p = ⟨0⟩`, returns `0`. If `p`
is greater than `rawEndPos`, returns the position one byte before `p`. Otherwise, if `p` occurs in the
middle of a multi-byte character, returns the beginning position of that character.

For example, `"L∃∀N".prev ⟨3⟩` is `⟨1⟩`, since byte 3 occurs in the middle of the multi-byte
character `'∃'` that starts at byte 1.

This is a legacy function. The recommended alternative is `[String.Pos.prev]](#manual-String___Pos___prev)` or one of its
variants like `[String.Pos.prev?]](#manual-String___Pos___prev___-next)`, combined with `[String.pos]](#manual-String___pos)` or another means of obtaining
a `[String.Pos]](#manual-String___Pos___mk)`.

Examples:

- `"abc".get ("abc".[rawEndPos]](#manual-String___rawEndPos) |> "abc".prev) = 'c'`
- `"L∃∀N".get ("L∃∀N".[rawEndPos]](#manual-String___rawEndPos) |> "L∃∀N".prev |> "L∃∀N".prev |> "L∃∀N".prev) = '∃'`

def

```lean
[String.Pos.Raw.next]](#manual-String___Pos___Raw___next) (s : [String]](#manual-String___ofByteArray)) (p : [String.Pos.Raw]](#manual-String___Pos___Raw___mk)) : [String.Pos.Raw]](#manual-String___Pos___Raw___mk)



[String.Pos.Raw.next]](#manual-String___Pos___Raw___next) (s : [String]](#manual-String___ofByteArray))
  (p : [String.Pos.Raw]](#manual-String___Pos___Raw___mk)) : [String.Pos.Raw]](#manual-String___Pos___Raw___mk)
```

Returns the next position in a string after position `p`. If `p` is not a valid position or
`p = s.[endPos]](#manual-String___endPos)`, returns the position one byte after `p`.

A run-time bounds check is performed to determine whether `p` is at the end of the string. If a
bounds check has already been performed, use `String.next'` to avoid a repeated check.

This is a legacy function. The recommended alternative is `[String.Pos.next]](#manual-String___Pos___next)` or one of its
variants like `[String.Pos.next?]](#manual-String___Pos___next___-next)`, combined with `[String.pos]](#manual-String___pos)` or another means of obtaining
a `[String]](#manual-String___ofByteArray).ValisPos`.

Some examples of edge cases:

- `"abc".next ⟨3⟩ = ⟨4⟩`, since `3 = "abc".[endPos]](#manual-String___endPos)`
- `"L∃∀N".next ⟨2⟩ = ⟨3⟩`, since `2` points into the middle of a multi-byte UTF-8 character

Examples:

- `"abc".get ("abc".next 0) = 'b'`
- `"L∃∀N".get (0 |> "L∃∀N".next |> "L∃∀N".next) = '∀'`

def

```lean
[String.Pos.Raw.next']](#manual-String___Pos___Raw___next___) (s : [String]](#manual-String___ofByteArray)) (p : [String.Pos.Raw]](#manual-String___Pos___Raw___mk))
  (h : [¬]](#manual-Not)[String.Pos.Raw.atEnd]](#manual-String___Pos___Raw___atEnd) s p [=]](#manual-Eq___refl) [true](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)) : [String.Pos.Raw]](#manual-String___Pos___Raw___mk)



[String.Pos.Raw.next']](#manual-String___Pos___Raw___next___) (s : [String]](#manual-String___ofByteArray))
  (p : [String.Pos.Raw]](#manual-String___Pos___Raw___mk))
  (h : [¬]](#manual-Not)[String.Pos.Raw.atEnd]](#manual-String___Pos___Raw___atEnd) s p [=]](#manual-Eq___refl) [true](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)) :
  [String.Pos.Raw]](#manual-String___Pos___Raw___mk)
```

Returns the next position in a string after position `p`. The result is unspecified if `p` is not a
valid position.

Requires evidence, `h`, that `p` is within bounds. No run-time bounds check is performed, as in
`String.next`.

A typical pattern combines `String.next'` with a dependent `[if]](#manual-if)`-expression to avoid the overhead of
an additional bounds check. For example:

```
def next? (s : String) (p : String.Pos) : Option Char :=
  if h : s.atEnd p then none else s.get (s.next' p h)
```

This is a legacy function. The recommended alternative is `[String.Pos.next]](#manual-String___Pos___next)`, combined with
`[String.pos]](#manual-String___pos)` or another means of obtaining a `[String.Pos]](#manual-String___Pos___mk)`.

Example:

- `let abc := "abc"; abc.get (abc.next' 0 (by [decide]](#manual-decide))) = 'b'`

def

```lean
[String.Pos.Raw.nextUntil]](#manual-String___Pos___Raw___nextUntil) (s : [String]](#manual-String___ofByteArray)) (p : [Char]](#manual-Char___mk) → [Bool](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false))
  (i : [String.Pos.Raw]](#manual-String___Pos___Raw___mk)) : [String.Pos.Raw]](#manual-String___Pos___Raw___mk)



[String.Pos.Raw.nextUntil]](#manual-String___Pos___Raw___nextUntil) (s : [String]](#manual-String___ofByteArray))
  (p : [Char]](#manual-Char___mk) → [Bool](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)) (i : [String.Pos.Raw]](#manual-String___Pos___Raw___mk)) :
  [String.Pos.Raw]](#manual-String___Pos___Raw___mk)
```

Repeatedly increments a position in a string, as if by `[String.Pos.Raw.next]](#manual-String___Pos___Raw___next)`, while the predicate
`p` returns `[false](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)` for the character at the position. Stops incrementing at the end of
the string or when `p` returns `[true](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)` for the current character.

Examples:

- `let s := " a "; (Pos.Raw.nextUntil s Char.isWhitespace 0).get s = ' '`
- `let s := " a "; (Pos.Raw.nextUntil s Char.isAlpha 0).get s = 'a'`
- `let s := "a "; (Pos.Raw.nextUntil s Char.isWhitespace 0).get s = ' '`

def

```lean
[String.Pos.Raw.nextWhile]](#manual-String___Pos___Raw___nextWhile) (s : [String]](#manual-String___ofByteArray)) (p : [Char]](#manual-Char___mk) → [Bool](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false))
  (i : [String.Pos.Raw]](#manual-String___Pos___Raw___mk)) : [String.Pos.Raw]](#manual-String___Pos___Raw___mk)



[String.Pos.Raw.nextWhile]](#manual-String___Pos___Raw___nextWhile) (s : [String]](#manual-String___ofByteArray))
  (p : [Char]](#manual-Char___mk) → [Bool](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)) (i : [String.Pos.Raw]](#manual-String___Pos___Raw___mk)) :
  [String.Pos.Raw]](#manual-String___Pos___Raw___mk)
```

Repeatedly increments a position in a string, as if by `[String.Pos.Raw.next]](#manual-String___Pos___Raw___next)`, while the
predicate `p` returns `[true](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)` for the character at the position. Stops incrementing at
the end of the string or when `p` returns `[false](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)` for the current character.

Examples:

- `let s := " a "; ((0 : Pos.Raw).nextWhile s Char.isWhitespace).get s = 'a'`
- `let s := "a "; ((0 : Pos.Raw).nextWhile s Char.isWhitespace).get s = 'a'`
- `let s := "ba "; (Pos.Raw.nextWhile s Char.isWhitespace 0).get s = 'b'`

def

```lean
[String.Pos.Raw.inc]](#manual-String___Pos___Raw___inc) (p : [String.Pos.Raw]](#manual-String___Pos___Raw___mk)) : [String.Pos.Raw]](#manual-String___Pos___Raw___mk)



[String.Pos.Raw.inc]](#manual-String___Pos___Raw___inc) (p : [String.Pos.Raw]](#manual-String___Pos___Raw___mk)) :
  [String.Pos.Raw]](#manual-String___Pos___Raw___mk)
```

Increases the byte offset of the position by `1`. Not to be confused with `Pos.next`.

def

```lean
[String.Pos.Raw.increaseBy]](#manual-String___Pos___Raw___increaseBy) (p : [String.Pos.Raw]](#manual-String___Pos___Raw___mk)) (n : [Nat]](#manual-Nat___zero)) :
  [String.Pos.Raw]](#manual-String___Pos___Raw___mk)



[String.Pos.Raw.increaseBy]](#manual-String___Pos___Raw___increaseBy)
  (p : [String.Pos.Raw]](#manual-String___Pos___Raw___mk)) (n : [Nat]](#manual-Nat___zero)) :
  [String.Pos.Raw]](#manual-String___Pos___Raw___mk)
```

Advances `p` by `n` bytes. This is not an `[HAdd]](#manual-HAdd___mk)` instance because it should be a relatively
rare operation, so we use a name to make accidental use less likely. To add the size of a
character `c` or string `s` to a raw position `p`, you can use `p + c` resp. `p + s`.

This should be seen as an "advance" or "skip".

See also `Pos.Raw.offsetBy`, which turns relative positions into absolute positions.

def

```lean
[String.Pos.Raw.offsetBy]](#manual-String___Pos___Raw___offsetBy) (p offset : [String.Pos.Raw]](#manual-String___Pos___Raw___mk)) : [String.Pos.Raw]](#manual-String___Pos___Raw___mk)



[String.Pos.Raw.offsetBy]](#manual-String___Pos___Raw___offsetBy)
  (p offset : [String.Pos.Raw]](#manual-String___Pos___Raw___mk)) :
  [String.Pos.Raw]](#manual-String___Pos___Raw___mk)
```

Offsets `p` by `offset` on the left. This is not an `[HAdd]](#manual-HAdd___mk)` instance because it should be a
relatively rare operation, so we use a name to make accidental use less likely. To offset a position
by the size of a character character `c` or string `s`, you can use `c + p` resp. `s + p`.

This should be seen as an operation that converts relative positions into absolute positions.

See also `Pos.Raw.increaseBy`, which is an "advancing" operation.

def

```lean
[String.Pos.Raw.dec]](#manual-String___Pos___Raw___dec) (p : [String.Pos.Raw]](#manual-String___Pos___Raw___mk)) : [String.Pos.Raw]](#manual-String___Pos___Raw___mk)



[String.Pos.Raw.dec]](#manual-String___Pos___Raw___dec) (p : [String.Pos.Raw]](#manual-String___Pos___Raw___mk)) :
  [String.Pos.Raw]](#manual-String___Pos___Raw___mk)
```

Decreases the byte offset of the position by `1`. Not to be confused with `Pos.prev`.

def

```lean
[String.Pos.Raw.decreaseBy]](#manual-String___Pos___Raw___decreaseBy) (p : [String.Pos.Raw]](#manual-String___Pos___Raw___mk)) (n : [Nat]](#manual-Nat___zero)) :
  [String.Pos.Raw]](#manual-String___Pos___Raw___mk)



[String.Pos.Raw.decreaseBy]](#manual-String___Pos___Raw___decreaseBy)
  (p : [String.Pos.Raw]](#manual-String___Pos___Raw___mk)) (n : [Nat]](#manual-Nat___zero)) :
  [String.Pos.Raw]](#manual-String___Pos___Raw___mk)
```

Move the position `p` back by `n` bytes. This is not an `[HSub]](#manual-HSub___mk)` instance because it should be a
relatively rare operation, so we use a name to make accidental use less likely. To remove the size
of a character `c` or string `s` from a raw position `p`, you can use `p - c` resp. `p - s`.

This should be seen as the inverse of an "advance" or "skip".

See also `Pos.Raw.unoffsetBy`, which turns absolute positions into relative positions.

def

```lean
[String.Pos.Raw.unoffsetBy]](#manual-String___Pos___Raw___unoffsetBy) (p offset : [String.Pos.Raw]](#manual-String___Pos___Raw___mk)) : [String.Pos.Raw]](#manual-String___Pos___Raw___mk)



[String.Pos.Raw.unoffsetBy]](#manual-String___Pos___Raw___unoffsetBy)
  (p offset : [String.Pos.Raw]](#manual-String___Pos___Raw___mk)) :
  [String.Pos.Raw]](#manual-String___Pos___Raw___mk)
```

Decreases `p` by `offset`. This is not an `[HSub]](#manual-HSub___mk)` instance because it should be a relatively
rare operation, so we use a name to make accidental use less likely. To unoffset a position
by the size of a character `c` or string `s`, you can use `p - c` resp. `p - s`.

This should be seen as an operation that converts absolute positions into relative positions.

See also `Pos.Raw.decreaseBy`, which is an "unadvancing" operation.

##### 20.8.4.5.6. String Lookups {#manual-The-Lean-Language-Reference--Basic-Types--Strings--API-Reference--Raw-Positions--String-Lookups}

def

```lean
[String.Pos.Raw.extract]](#manual-String___Pos___Raw___extract) :
  [String]](#manual-String___ofByteArray) → [String.Pos.Raw]](#manual-String___Pos___Raw___mk) → [String.Pos.Raw]](#manual-String___Pos___Raw___mk) → [String]](#manual-String___ofByteArray)



[String.Pos.Raw.extract]](#manual-String___Pos___Raw___extract) :
  [String]](#manual-String___ofByteArray) →
    [String.Pos.Raw]](#manual-String___Pos___Raw___mk) →
      [String.Pos.Raw]](#manual-String___Pos___Raw___mk) → [String]](#manual-String___ofByteArray)
```

Creates a new string that consists of the region of the input string delimited by the two positions.

The result is `""` if the start position is greater than or equal to the end position or if the
start position is at the end of the string. If either position is invalid (that is, if either points
at the middle of a multi-byte UTF-8 character) then the result is unspecified.

This is a legacy function. The recommended alternative is `[String.extract]](#manual-String___extract)`, but usually
it is even better to operate on `[String.Slice]](#manual-String___Slice___mk)` instead and call `[String.Slice.copy]](#manual-String___Slice___copy)` (only) if
required.

Examples:

- `[String.Pos.Raw.extract]](#manual-String___Pos___Raw___extract) "red green blue" ⟨0⟩ ⟨3⟩ = "red"`
- `[String.Pos.Raw.extract]](#manual-String___Pos___Raw___extract) "red green blue" ⟨3⟩ ⟨0⟩ = ""`
- `[String.Pos.Raw.extract]](#manual-String___Pos___Raw___extract) "red green blue" ⟨0⟩ ⟨100⟩ = "red green blue"`
- `[String.Pos.Raw.extract]](#manual-String___Pos___Raw___extract) "red green blue" ⟨4⟩ ⟨100⟩ = "green blue"`
- `[String.Pos.Raw.extract]](#manual-String___Pos___Raw___extract) "L∃∀N" ⟨1⟩ ⟨2⟩ = "∃∀N"`
- `[String.Pos.Raw.extract]](#manual-String___Pos___Raw___extract) "L∃∀N" ⟨2⟩ ⟨100⟩ = ""`

def

```lean
[String.Pos.Raw.get]](#manual-String___Pos___Raw___get) (s : [String]](#manual-String___ofByteArray)) (p : [String.Pos.Raw]](#manual-String___Pos___Raw___mk)) : [Char]](#manual-Char___mk)



[String.Pos.Raw.get]](#manual-String___Pos___Raw___get) (s : [String]](#manual-String___ofByteArray))
  (p : [String.Pos.Raw]](#manual-String___Pos___Raw___mk)) : [Char]](#manual-Char___mk)
```

Returns the character at position `p` of a string. If `p` is not a valid position, returns the
fallback value `([default]](#manual-Inhabited___mk) : [Char]](#manual-Char___mk))`, which is `'A'`, but does not panic.

This function is overridden with an efficient implementation in runtime code. See
`String.Pos.Raw.utf8GetAux` for the reference implementation.

This is a legacy function. The recommended alternative is `[String.Pos.get]](#manual-String___Pos___get)`, combined with
`[String.pos]](#manual-String___pos)` or another means of obtaining a `[String.Pos]](#manual-String___Pos___mk)`.

Examples:

- `"abc".get ⟨1⟩ = 'b'`
- `"abc".get ⟨3⟩ = ([default]](#manual-Inhabited___mk) : [Char]](#manual-Char___mk))` because byte `3` is at the end of the string.
- `"L∃∀N".get ⟨2⟩ = ([default]](#manual-Inhabited___mk) : [Char]](#manual-Char___mk))` because byte `2` is in the middle of `'∃'`.

def

```lean
[String.Pos.Raw.get!]](#manual-String___Pos___Raw___get___) (s : [String]](#manual-String___ofByteArray)) (p : [String.Pos.Raw]](#manual-String___Pos___Raw___mk)) : [Char]](#manual-Char___mk)



[String.Pos.Raw.get!]](#manual-String___Pos___Raw___get___) (s : [String]](#manual-String___ofByteArray))
  (p : [String.Pos.Raw]](#manual-String___Pos___Raw___mk)) : [Char]](#manual-Char___mk)
```

Returns the character at position `p` of a string. Panics if `p` is not a valid position.

See `[String.pos?]](#manual-String___pos___)` and `[String.Pos.get]](#manual-String___Pos___get)` for a safer alternative.

This function is overridden with an efficient implementation in runtime code. See
`String.utf8GetAux` for the reference implementation.

This is a legacy function. The recommended alternative is `[String.Pos.get]](#manual-String___Pos___get)`, combined with
`[String.pos!]](#manual-String___pos___-next)` or another means of obtaining a `[String.Pos]](#manual-String___Pos___mk)`.

Examples

- `"abc".get! ⟨1⟩ = 'b'`

def

```lean
[String.Pos.Raw.get']](#manual-String___Pos___Raw___get___-next) (s : [String]](#manual-String___ofByteArray)) (p : [String.Pos.Raw]](#manual-String___Pos___Raw___mk))
  (h : [¬]](#manual-Not)[String.Pos.Raw.atEnd]](#manual-String___Pos___Raw___atEnd) s p [=]](#manual-Eq___refl) [true](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)) : [Char]](#manual-Char___mk)



[String.Pos.Raw.get']](#manual-String___Pos___Raw___get___-next) (s : [String]](#manual-String___ofByteArray))
  (p : [String.Pos.Raw]](#manual-String___Pos___Raw___mk))
  (h : [¬]](#manual-Not)[String.Pos.Raw.atEnd]](#manual-String___Pos___Raw___atEnd) s p [=]](#manual-Eq___refl) [true](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)) :
  [Char]](#manual-Char___mk)
```

Returns the character at position `p` of a string. Returns `([default]](#manual-Inhabited___mk) : [Char]](#manual-Char___mk))`, which is `'A'`, if
`p` is not a valid position.

Requires evidence, `h`, that `p` is within bounds instead of performing a run-time bounds check as
in `String.get`.

A typical pattern combines `get'` with a dependent `[if]](#manual-if)`-expression to avoid the overhead of an
additional bounds check. For example:

```
def getInBounds? (s : String) (p : String.Pos) : Option Char :=
  if h : s.atEnd p then none else some (s.get' p h)
```

Even with evidence of `¬ s.atEnd p`, `p` may be invalid if a byte index points into the middle of a
multi-byte UTF-8 character. For example, `"L∃∀N".get' ⟨2⟩ (by [decide]](#manual-decide)) = ([default]](#manual-Inhabited___mk) : [Char]](#manual-Char___mk))`.

This is a legacy function. The recommended alternative is `[String.Pos.get]](#manual-String___Pos___get)`, combined with
`[String.pos]](#manual-String___pos)` or another means of obtaining a `[String.Pos]](#manual-String___Pos___mk)`.

Examples:

- `"abc".get' 0 (by [decide]](#manual-decide)) = 'a'`
- `let lean := "L∃∀N"; lean.get' (0 |> lean.next |> lean.next) (by [decide]](#manual-decide)) = '∀'`

def

```lean
[String.Pos.Raw.get?]](#manual-String___Pos___Raw___get___-next-next) : [String]](#manual-String___ofByteArray) → [String.Pos.Raw]](#manual-String___Pos___Raw___mk) → [Option](https://lean-lang.org/doc/reference/latest/Basic-Types/Optional-Values/#Option___none) [Char]](#manual-Char___mk)



[String.Pos.Raw.get?]](#manual-String___Pos___Raw___get___-next-next) :
  [String]](#manual-String___ofByteArray) → [String.Pos.Raw]](#manual-String___Pos___Raw___mk) → [Option](https://lean-lang.org/doc/reference/latest/Basic-Types/Optional-Values/#Option___none) [Char]](#manual-Char___mk)
```

Returns the character at position `p` of a string. If `p` is not a valid position, returns `[none](https://lean-lang.org/doc/reference/latest/Basic-Types/Optional-Values/#Option___none)`.

This function is overridden with an efficient implementation in runtime code. See
`String.utf8GetAux?` for the reference implementation.

This is a legacy function. The recommended alternative is `[String.Pos.get]](#manual-String___Pos___get)`, combined with
`[String.pos?]](#manual-String___pos___)` or another means of obtaining a `[String.Pos]](#manual-String___Pos___mk)`.

Examples:

- `"abc".get? ⟨1⟩ = [some](https://lean-lang.org/doc/reference/latest/Basic-Types/Optional-Values/#Option___none) 'b'`
- `"abc".get? ⟨3⟩ = [none](https://lean-lang.org/doc/reference/latest/Basic-Types/Optional-Values/#Option___none)`
- `"L∃∀N".get? ⟨1⟩ = [some](https://lean-lang.org/doc/reference/latest/Basic-Types/Optional-Values/#Option___none) '∃'`
- `"L∃∀N".get? ⟨2⟩ = [none](https://lean-lang.org/doc/reference/latest/Basic-Types/Optional-Values/#Option___none)`

##### 20.8.4.5.7. String Modifications {#manual-The-Lean-Language-Reference--Basic-Types--Strings--API-Reference--Raw-Positions--String-Modifications}

def

```lean
[String.Pos.Raw.set]](#manual-String___Pos___Raw___set) : [String]](#manual-String___ofByteArray) → [String.Pos.Raw]](#manual-String___Pos___Raw___mk) → [Char]](#manual-Char___mk) → [String]](#manual-String___ofByteArray)



[String.Pos.Raw.set]](#manual-String___Pos___Raw___set) :
  [String]](#manual-String___ofByteArray) → [String.Pos.Raw]](#manual-String___Pos___Raw___mk) → [Char]](#manual-Char___mk) → [String]](#manual-String___ofByteArray)
```

Replaces the character at a specified position in a string with a new character. If the position is
invalid, the string is returned unchanged.

If both the replacement character and the replaced character are 7-bit ASCII characters and the
string is not shared, then it is updated in-place and not copied.

This is a legacy function. The recommended alternative is `[String.Pos.set]](#manual-String___Pos___set)`, combined with
`[String.pos]](#manual-String___pos)` or another means of obtaining a `[String.Pos]](#manual-String___Pos___mk)`.

Examples:

- `"abc".set ⟨1⟩ 'B' = "aBc"`
- `"abc".set ⟨3⟩ 'D' = "abc"`
- `"L∃∀N".set ⟨4⟩ 'X' = "L∃XN"`
- `"L∃∀N".set ⟨2⟩ 'X' = "L∃∀N"` because `'∃'` is a multi-byte character, so the byte index `2` is an
  invalid position.

def

```lean
[String.Pos.Raw.modify]](#manual-String___Pos___Raw___modify) (s : [String]](#manual-String___ofByteArray)) (i : [String.Pos.Raw]](#manual-String___Pos___Raw___mk))
  (f : [Char]](#manual-Char___mk) → [Char]](#manual-Char___mk)) : [String]](#manual-String___ofByteArray)



[String.Pos.Raw.modify]](#manual-String___Pos___Raw___modify) (s : [String]](#manual-String___ofByteArray))
  (i : [String.Pos.Raw]](#manual-String___Pos___Raw___mk)) (f : [Char]](#manual-Char___mk) → [Char]](#manual-Char___mk)) :
  [String]](#manual-String___ofByteArray)
```

Replaces the character at position `p` in the string `s` with the result of applying `f` to that
character. If `p` is an invalid position, the string is returned unchanged.

If both the replacement character and the replaced character are 7-bit ASCII characters and the
string is not shared, then it is updated in-place and not copied.

This is a legacy function. The recommended alternative is `[String.Pos.set]](#manual-String___Pos___set)`, combined with
`[String.pos]](#manual-String___pos)` or another means of obtaining a `[String.Pos]](#manual-String___Pos___mk)`.

Examples:

- `"abc".modify ⟨1⟩ [Char.toUpper]](#manual-Char___toUpper) = "aBc"`
- `"abc".modify ⟨3⟩ [Char.toUpper]](#manual-Char___toUpper) = "abc"`

#### 20.8.4.6. Lookups and Modifications {#manual-string-api-lookup}

Operations that select a sub-region of a string (for example, a prefix or suffix of it) return a [slice]](#manual-string-api-slice) into the original string rather than allocating a new string.
Use `[String.Slice.copy]](#manual-String___Slice___copy)` to convert the slice into a new string.

def

```lean
[String.take]](#manual-String___take) (s : [String]](#manual-String___ofByteArray)) (n : [Nat]](#manual-Nat___zero)) : [String.Slice]](#manual-String___Slice___mk)



[String.take]](#manual-String___take) (s : [String]](#manual-String___ofByteArray)) (n : [Nat]](#manual-Nat___zero)) :
  [String.Slice]](#manual-String___Slice___mk)
```

Returns a `[String.Slice]](#manual-String___Slice___mk)` that contains the first `n` characters (Unicode code points) of
`s`.

If `n` is greater than `s.toList.[length](https://lean-lang.org/doc/reference/latest/Basic-Types/Linked-Lists/#List___length)`, returns `s.[toSlice]](#manual-String___toSlice)`.

This is a cheap operation because it does not allocate a new string to hold the result.
To convert the result into a string, use `[String.Slice.copy]](#manual-String___Slice___copy)`.

Examples:

- `"red green blue".[take]](#manual-String___take) 3 == "red".[toSlice]](#manual-String___toSlice)`
- `"red green blue".[take]](#manual-String___take) 1 == "r".[toSlice]](#manual-String___toSlice)`
- `"red green blue".[take]](#manual-String___take) 0 == "".[toSlice]](#manual-String___toSlice)`
- `"red green blue".[take]](#manual-String___take) 100 == "red green blue".[toSlice]](#manual-String___toSlice)`
- `"مرحبا بالعالم".[take]](#manual-String___take) 5 == "مرحبا".[toSlice]](#manual-String___toSlice)`

def

```lean
[String.takeWhile]](#manual-String___takeWhile) {ρ : Type} (s : [String]](#manual-String___ofByteArray)) (pat : ρ)
  [[String.Slice.Pattern.ForwardPattern]](#manual-String___Slice___Pattern___ForwardPattern___mk) pat] : [String.Slice]](#manual-String___Slice___mk)



[String.takeWhile]](#manual-String___takeWhile) {ρ : Type} (s : [String]](#manual-String___ofByteArray))
  (pat : ρ)
  [[String.Slice.Pattern.ForwardPattern]](#manual-String___Slice___Pattern___ForwardPattern___mk)
      pat] :
  [String.Slice]](#manual-String___Slice___mk)
```

Creates a string slice that contains the longest prefix of `s` in which `pat` matched
(potentially repeatedly).

This is a cheap operation because it does not allocate a new string to hold the result.
To convert the result into a string, use `[String.Slice.copy]](#manual-String___Slice___copy)`.

This function is generic over all currently supported patterns.

Examples:

- `"red green blue".[takeWhile]](#manual-String___takeWhile) [Char.isLower]](#manual-Char___isLower) == "red".[toSlice]](#manual-String___toSlice)`
- `"red green blue".[takeWhile]](#manual-String___takeWhile) 'r' == "r".[toSlice]](#manual-String___toSlice)`
- `"red red green blue".[takeWhile]](#manual-String___takeWhile) "red " == "red red ".[toSlice]](#manual-String___toSlice)`
- `"red green blue".[takeWhile]](#manual-String___takeWhile) (fun (_ : [Char]](#manual-Char___mk)) => [true](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)) == "red green blue".[toSlice]](#manual-String___toSlice)`

def

```lean
[String.takeEnd]](#manual-String___takeEnd) (s : [String]](#manual-String___ofByteArray)) (n : [Nat]](#manual-Nat___zero)) : [String.Slice]](#manual-String___Slice___mk)



[String.takeEnd]](#manual-String___takeEnd) (s : [String]](#manual-String___ofByteArray)) (n : [Nat]](#manual-Nat___zero)) :
  [String.Slice]](#manual-String___Slice___mk)
```

Returns a `[String.Slice]](#manual-String___Slice___mk)` that contains the last `n` characters (Unicode code points) of
`s`.

If `n` is greater than `s.toList.[length](https://lean-lang.org/doc/reference/latest/Basic-Types/Linked-Lists/#List___length)`, returns `s.[toSlice]](#manual-String___toSlice)`.

This is a cheap operation because it does not allocate a new string to hold the result.
To convert the result into a string, use `[String.Slice.copy]](#manual-String___Slice___copy)`.

Examples:

- `"red green blue".[takeEnd]](#manual-String___takeEnd) 4 == "blue".[toSlice]](#manual-String___toSlice)`
- `"red green blue".[takeEnd]](#manual-String___takeEnd) 1 == "e".[toSlice]](#manual-String___toSlice)`
- `"red green blue".[takeEnd]](#manual-String___takeEnd) 0 == "".[toSlice]](#manual-String___toSlice)`
- `"red green blue".[takeEnd]](#manual-String___takeEnd) 100 == "red green blue".[toSlice]](#manual-String___toSlice)`
- `"مرحبا بالعالم".[takeEnd]](#manual-String___takeEnd) 5 == "لعالم".[toSlice]](#manual-String___toSlice)`

def

```lean
[String.takeEndWhile]](#manual-String___takeEndWhile) {ρ : Type} (s : [String]](#manual-String___ofByteArray)) (pat : ρ)
  [[String.Slice.Pattern.BackwardPattern]](#manual-String___Slice___Pattern___BackwardPattern___mk) pat] : [String.Slice]](#manual-String___Slice___mk)



[String.takeEndWhile]](#manual-String___takeEndWhile) {ρ : Type}
  (s : [String]](#manual-String___ofByteArray)) (pat : ρ)
  [[String.Slice.Pattern.BackwardPattern]](#manual-String___Slice___Pattern___BackwardPattern___mk)
      pat] :
  [String.Slice]](#manual-String___Slice___mk)
```

Creates a string slice that contains the longest suffix of `s` in which `pat` matched
(potentially repeatedly).

This is a cheap operation because it does not allocate a new string to hold the result.
To convert the result into a string, use `[String.Slice.copy]](#manual-String___Slice___copy)`.

This function is generic over all currently supported patterns.

Examples:

- `"red green blue".[takeEndWhile]](#manual-String___takeEndWhile) [Char.isLower]](#manual-Char___isLower) == "blue".[toSlice]](#manual-String___toSlice)`
- `"red green blue".[takeEndWhile]](#manual-String___takeEndWhile) 'e' == "e".[toSlice]](#manual-String___toSlice)`
- `"red green blue".[takeEndWhile]](#manual-String___takeEndWhile) (fun (_ : [Char]](#manual-Char___mk)) => [true](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)) == "red green blue".[toSlice]](#manual-String___toSlice)`

def

```lean
[String.drop]](#manual-String___drop) (s : [String]](#manual-String___ofByteArray)) (n : [Nat]](#manual-Nat___zero)) : [String.Slice]](#manual-String___Slice___mk)



[String.drop]](#manual-String___drop) (s : [String]](#manual-String___ofByteArray)) (n : [Nat]](#manual-Nat___zero)) :
  [String.Slice]](#manual-String___Slice___mk)
```

Returns a `[String.Slice]](#manual-String___Slice___mk)` obtained by removing the specified number of characters (Unicode code
points) from the start of the string.

If `n` is greater than `s.toList.[length](https://lean-lang.org/doc/reference/latest/Basic-Types/Linked-Lists/#List___length)`, returns an empty slice.

This is a cheap operation because it does not allocate a new string to hold the result.
To convert the result into a string, use `[String.Slice.copy]](#manual-String___Slice___copy)`.

Examples:

- `"red green blue".[drop]](#manual-String___drop) 4 == "green blue".[toSlice]](#manual-String___toSlice)`
- `"red green blue".[drop]](#manual-String___drop) 10 == "blue".[toSlice]](#manual-String___toSlice)`
- `"red green blue".[drop]](#manual-String___drop) 50 == "".[toSlice]](#manual-String___toSlice)`
- `"مرحبا بالعالم".[drop]](#manual-String___drop) 3 == "با بالعالم".[toSlice]](#manual-String___toSlice)`

def

```lean
[String.dropWhile]](#manual-String___dropWhile) {ρ : Type} (s : [String]](#manual-String___ofByteArray)) (pat : ρ)
  [[String.Slice.Pattern.ForwardPattern]](#manual-String___Slice___Pattern___ForwardPattern___mk) pat] : [String.Slice]](#manual-String___Slice___mk)



[String.dropWhile]](#manual-String___dropWhile) {ρ : Type} (s : [String]](#manual-String___ofByteArray))
  (pat : ρ)
  [[String.Slice.Pattern.ForwardPattern]](#manual-String___Slice___Pattern___ForwardPattern___mk)
      pat] :
  [String.Slice]](#manual-String___Slice___mk)
```

Creates a string slice by removing the longest prefix from `s` in which `pat` matched
(potentially repeatedly).

This is a cheap operation because it does not allocate a new string to hold the result.
To convert the result into a string, use `[String.Slice.copy]](#manual-String___Slice___copy)`.

This function is generic over all currently supported patterns.

Examples:

- `"red green blue".[dropWhile]](#manual-String___dropWhile) [Char.isLower]](#manual-Char___isLower) == " green blue".[toSlice]](#manual-String___toSlice)`
- `"red green blue".[dropWhile]](#manual-String___dropWhile) 'r' == "ed green blue".[toSlice]](#manual-String___toSlice)`
- `"red red green blue".[dropWhile]](#manual-String___dropWhile) "red " == "green blue".[toSlice]](#manual-String___toSlice)`
- `"red green blue".[dropWhile]](#manual-String___dropWhile) (fun (_ : [Char]](#manual-Char___mk)) => [true](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)) == "".[toSlice]](#manual-String___toSlice)`

def

```lean
[String.dropEnd]](#manual-String___dropEnd) (s : [String]](#manual-String___ofByteArray)) (n : [Nat]](#manual-Nat___zero)) : [String.Slice]](#manual-String___Slice___mk)



[String.dropEnd]](#manual-String___dropEnd) (s : [String]](#manual-String___ofByteArray)) (n : [Nat]](#manual-Nat___zero)) :
  [String.Slice]](#manual-String___Slice___mk)
```

Returns a `[String.Slice]](#manual-String___Slice___mk)` obtained by removing the specified number of characters (Unicode code
points) from the end of the string.

If `n` is greater than `s.toList.[length](https://lean-lang.org/doc/reference/latest/Basic-Types/Linked-Lists/#List___length)`, returns an empty slice.

This is a cheap operation because it does not allocate a new string to hold the result.
To convert the result into a string, use `[String.Slice.copy]](#manual-String___Slice___copy)`.

Examples:

- `"red green blue".[dropEnd]](#manual-String___dropEnd) 5 == "red green".[toSlice]](#manual-String___toSlice)`
- `"red green blue".[dropEnd]](#manual-String___dropEnd) 11 == "red".[toSlice]](#manual-String___toSlice)`
- `"red green blue".[dropEnd]](#manual-String___dropEnd) 50 == "".[toSlice]](#manual-String___toSlice)`
- `"مرحبا بالعالم".[dropEnd]](#manual-String___dropEnd) 3 == "مرحبا بالع".[toSlice]](#manual-String___toSlice)`

def

```lean
[String.dropEndWhile]](#manual-String___dropEndWhile) {ρ : Type} (s : [String]](#manual-String___ofByteArray)) (pat : ρ)
  [[String.Slice.Pattern.BackwardPattern]](#manual-String___Slice___Pattern___BackwardPattern___mk) pat] : [String.Slice]](#manual-String___Slice___mk)



[String.dropEndWhile]](#manual-String___dropEndWhile) {ρ : Type}
  (s : [String]](#manual-String___ofByteArray)) (pat : ρ)
  [[String.Slice.Pattern.BackwardPattern]](#manual-String___Slice___Pattern___BackwardPattern___mk)
      pat] :
  [String.Slice]](#manual-String___Slice___mk)
```

Creates a new string by removing the longest suffix from `s` in which `pat` matches
(potentially repeatedly).

This is a cheap operation because it does not allocate a new string to hold the result.
To convert the result into a string, use `[String.Slice.copy]](#manual-String___Slice___copy)`.

This function is generic over all currently supported patterns.

Examples:

- `"red green blue".[dropEndWhile]](#manual-String___dropEndWhile) [Char.isLower]](#manual-Char___isLower) == "red green ".[toSlice]](#manual-String___toSlice)`
- `"red green blue".[dropEndWhile]](#manual-String___dropEndWhile) 'e' == "red green blu".[toSlice]](#manual-String___toSlice)`
- `"red green blue".[dropEndWhile]](#manual-String___dropEndWhile) (fun (_ : [Char]](#manual-Char___mk)) => [true](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)) == "".[toSlice]](#manual-String___toSlice)`

def

```lean
[String.dropPrefix?]](#manual-String___dropPrefix___) {ρ : Type} (s : [String]](#manual-String___ofByteArray)) (pat : ρ)
  [[String.Slice.Pattern.ForwardPattern]](#manual-String___Slice___Pattern___ForwardPattern___mk) pat] : [Option](https://lean-lang.org/doc/reference/latest/Basic-Types/Optional-Values/#Option___none) [String.Slice]](#manual-String___Slice___mk)



[String.dropPrefix?]](#manual-String___dropPrefix___) {ρ : Type} (s : [String]](#manual-String___ofByteArray))
  (pat : ρ)
  [[String.Slice.Pattern.ForwardPattern]](#manual-String___Slice___Pattern___ForwardPattern___mk)
      pat] :
  [Option](https://lean-lang.org/doc/reference/latest/Basic-Types/Optional-Values/#Option___none) [String.Slice]](#manual-String___Slice___mk)
```

If `pat` matches a prefix of `s`, returns the remainder. Returns `[none](https://lean-lang.org/doc/reference/latest/Basic-Types/Optional-Values/#Option___none)` otherwise.

Use `[String.dropPrefix]](#manual-String___dropPrefix)` to return the slice
unchanged when `pat` does not match a prefix.

This is a cheap operation because it does not allocate a new string to hold the result.
To convert the result into a string, use `[String.Slice.copy]](#manual-String___Slice___copy)`.

This function is generic over all currently supported patterns.

Examples:

- `"red green blue".[dropPrefix?]](#manual-String___dropPrefix___) "red " == [some](https://lean-lang.org/doc/reference/latest/Basic-Types/Optional-Values/#Option___none) "green blue".[toSlice]](#manual-String___toSlice)`
- `"red green blue".[dropPrefix?]](#manual-String___dropPrefix___) "reed " == [none](https://lean-lang.org/doc/reference/latest/Basic-Types/Optional-Values/#Option___none)`
- `"red green blue".[dropPrefix?]](#manual-String___dropPrefix___) 'r' == [some](https://lean-lang.org/doc/reference/latest/Basic-Types/Optional-Values/#Option___none) "ed green blue".[toSlice]](#manual-String___toSlice)`
- `"red green blue".[dropPrefix?]](#manual-String___dropPrefix___) [Char.isLower]](#manual-Char___isLower) == [some](https://lean-lang.org/doc/reference/latest/Basic-Types/Optional-Values/#Option___none) "ed green blue".[toSlice]](#manual-String___toSlice)`

def

```lean
[String.dropPrefix]](#manual-String___dropPrefix) {ρ : Type} (s : [String]](#manual-String___ofByteArray)) (pat : ρ)
  [[String.Slice.Pattern.ForwardPattern]](#manual-String___Slice___Pattern___ForwardPattern___mk) pat] : [String.Slice]](#manual-String___Slice___mk)



[String.dropPrefix]](#manual-String___dropPrefix) {ρ : Type} (s : [String]](#manual-String___ofByteArray))
  (pat : ρ)
  [[String.Slice.Pattern.ForwardPattern]](#manual-String___Slice___Pattern___ForwardPattern___mk)
      pat] :
  [String.Slice]](#manual-String___Slice___mk)
```

If `pat` matches a prefix of `s`, returns the remainder. Returns `s` unmodified
otherwise.

Use `[String.dropPrefix?]](#manual-String___dropPrefix___)` to return `[none](https://lean-lang.org/doc/reference/latest/Basic-Types/Optional-Values/#Option___none)` when `pat` does not match a prefix.

This is a cheap operation because it does not allocate a new string to hold the result.
To convert the result into a string, use `[String.Slice.copy]](#manual-String___Slice___copy)`.

This function is generic over all currently supported patterns.

Examples:

- `"red green blue".[dropPrefix]](#manual-String___dropPrefix) "red " == "green blue".[toSlice]](#manual-String___toSlice)`
- `"red green blue".[dropPrefix]](#manual-String___dropPrefix) "reed " == "red green blue".[toSlice]](#manual-String___toSlice)`
- `"red green blue".[dropPrefix]](#manual-String___dropPrefix) 'r' == "ed green blue".[toSlice]](#manual-String___toSlice)`
- `"red green blue".[dropPrefix]](#manual-String___dropPrefix) [Char.isLower]](#manual-Char___isLower) == "ed green blue".[toSlice]](#manual-String___toSlice)`

def

```lean
[String.dropSuffix?]](#manual-String___dropSuffix___) {ρ : Type} (s : [String]](#manual-String___ofByteArray)) (pat : ρ)
  [[String.Slice.Pattern.BackwardPattern]](#manual-String___Slice___Pattern___BackwardPattern___mk) pat] : [Option](https://lean-lang.org/doc/reference/latest/Basic-Types/Optional-Values/#Option___none) [String.Slice]](#manual-String___Slice___mk)



[String.dropSuffix?]](#manual-String___dropSuffix___) {ρ : Type} (s : [String]](#manual-String___ofByteArray))
  (pat : ρ)
  [[String.Slice.Pattern.BackwardPattern]](#manual-String___Slice___Pattern___BackwardPattern___mk)
      pat] :
  [Option](https://lean-lang.org/doc/reference/latest/Basic-Types/Optional-Values/#Option___none) [String.Slice]](#manual-String___Slice___mk)
```

If `pat` matches a suffix of `s`, returns the remainder. Returns `[none](https://lean-lang.org/doc/reference/latest/Basic-Types/Optional-Values/#Option___none)` otherwise.

Use `[String.dropSuffix]](#manual-String___dropSuffix)` to return the slice
unchanged when `pat` does not match a suffix.

This is a cheap operation because it does not allocate a new string to hold the result.
To convert the result into a string, use `[String.Slice.copy]](#manual-String___Slice___copy)`.

This function is generic over all currently supported patterns.

Examples:

- `"red green blue".[dropSuffix?]](#manual-String___dropSuffix___) " blue" == [some](https://lean-lang.org/doc/reference/latest/Basic-Types/Optional-Values/#Option___none) "red green".[toSlice]](#manual-String___toSlice)`
- `"red green blue".[dropSuffix?]](#manual-String___dropSuffix___) "bluu " == [none](https://lean-lang.org/doc/reference/latest/Basic-Types/Optional-Values/#Option___none)`
- `"red green blue".[dropSuffix?]](#manual-String___dropSuffix___) 'e' == [some](https://lean-lang.org/doc/reference/latest/Basic-Types/Optional-Values/#Option___none) "red green blu".[toSlice]](#manual-String___toSlice)`
- `"red green blue".[dropSuffix?]](#manual-String___dropSuffix___) [Char.isLower]](#manual-Char___isLower) == [some](https://lean-lang.org/doc/reference/latest/Basic-Types/Optional-Values/#Option___none) "red green blu".[toSlice]](#manual-String___toSlice)`

def

```lean
[String.dropSuffix]](#manual-String___dropSuffix) {ρ : Type} (s : [String]](#manual-String___ofByteArray)) (pat : ρ)
  [[String.Slice.Pattern.BackwardPattern]](#manual-String___Slice___Pattern___BackwardPattern___mk) pat] : [String.Slice]](#manual-String___Slice___mk)



[String.dropSuffix]](#manual-String___dropSuffix) {ρ : Type} (s : [String]](#manual-String___ofByteArray))
  (pat : ρ)
  [[String.Slice.Pattern.BackwardPattern]](#manual-String___Slice___Pattern___BackwardPattern___mk)
      pat] :
  [String.Slice]](#manual-String___Slice___mk)
```

If `pat` matches a suffix of `s`, returns the remainder. Returns `s` unmodified
otherwise.

Use `[String.dropSuffix?]](#manual-String___dropSuffix___)` to return `[none](https://lean-lang.org/doc/reference/latest/Basic-Types/Optional-Values/#Option___none)` when `pat` does not match a prefix.

This is a cheap operation because it does not allocate a new string to hold the result.
To convert the result into a string, use `[String.Slice.copy]](#manual-String___Slice___copy)`.

This function is generic over all currently supported patterns.

Examples:

- `"red green blue".[dropSuffix]](#manual-String___dropSuffix) " blue" == "red green".[toSlice]](#manual-String___toSlice)`
- `"red green blue".[dropSuffix]](#manual-String___dropSuffix) "bluu " == "red green blue".[toSlice]](#manual-String___toSlice)`
- `"red green blue".[dropSuffix]](#manual-String___dropSuffix) 'e' == "red green blu".[toSlice]](#manual-String___toSlice)`
- `"red green blue".[dropSuffix]](#manual-String___dropSuffix) [Char.isLower]](#manual-Char___isLower) == "red green blu".[toSlice]](#manual-String___toSlice)`

def

```lean
[String.trimAscii]](#manual-String___trimAscii) (s : [String]](#manual-String___ofByteArray)) : [String.Slice]](#manual-String___Slice___mk)



[String.trimAscii]](#manual-String___trimAscii) (s : [String]](#manual-String___ofByteArray)) :
  [String.Slice]](#manual-String___Slice___mk)
```

Removes leading and trailing whitespace from a string.

“Whitespace” is defined as characters for which `[Char.isWhitespace]](#manual-Char___isWhitespace)` returns `[true](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)`.

Examples:

- `"abc".[trimAscii]](#manual-String___trimAscii) == "abc".[toSlice]](#manual-String___toSlice)`
- `" abc".[trimAscii]](#manual-String___trimAscii) == "abc".[toSlice]](#manual-String___toSlice)`
- `"abc \t ".[trimAscii]](#manual-String___trimAscii) == "abc".[toSlice]](#manual-String___toSlice)`
- `" abc ".[trimAscii]](#manual-String___trimAscii) == "abc".[toSlice]](#manual-String___toSlice)`
- `"abc\ndef\n".[trimAscii]](#manual-String___trimAscii) == "abc\ndef".[toSlice]](#manual-String___toSlice)`

def

```lean
[String.trimAsciiStart]](#manual-String___trimAsciiStart) (s : [String]](#manual-String___ofByteArray)) : [String.Slice]](#manual-String___Slice___mk)



[String.trimAsciiStart]](#manual-String___trimAsciiStart) (s : [String]](#manual-String___ofByteArray)) :
  [String.Slice]](#manual-String___Slice___mk)
```

Removes leading whitespace from a string by returning a slice whose start position is the first
non-whitespace character, or the end position if there is no non-whitespace character.

“Whitespace” is defined as characters for which `[Char.isWhitespace]](#manual-Char___isWhitespace)` returns `[true](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)`.

Examples:

- `"abc".[trimAsciiStart]](#manual-String___trimAsciiStart) == "abc".[toSlice]](#manual-String___toSlice)`
- `" abc".[trimAsciiStart]](#manual-String___trimAsciiStart) == "abc".[toSlice]](#manual-String___toSlice)`
- `"abc \t ".[trimAsciiStart]](#manual-String___trimAsciiStart) == "abc \t ".[toSlice]](#manual-String___toSlice)`
- `" abc ".[trimAsciiStart]](#manual-String___trimAsciiStart) == "abc ".[toSlice]](#manual-String___toSlice)`
- `"abc\ndef\n".[trimAsciiStart]](#manual-String___trimAsciiStart) == "abc\ndef\n".[toSlice]](#manual-String___toSlice)`

def

```lean
[String.trimAsciiEnd]](#manual-String___trimAsciiEnd) (s : [String]](#manual-String___ofByteArray)) : [String.Slice]](#manual-String___Slice___mk)



[String.trimAsciiEnd]](#manual-String___trimAsciiEnd) (s : [String]](#manual-String___ofByteArray)) :
  [String.Slice]](#manual-String___Slice___mk)
```

Removes trailing whitespace from a string by returning a slice whose end position is the last
non-whitespace character, or the start position if there is no non-whitespace character.

“Whitespace” is defined as characters for which `[Char.isWhitespace]](#manual-Char___isWhitespace)` returns `[true](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)`.

Examples:

- `"abc".[trimAsciiEnd]](#manual-String___trimAsciiEnd) == "abc".[toSlice]](#manual-String___toSlice)`
- `" abc".[trimAsciiEnd]](#manual-String___trimAsciiEnd) == " abc".[toSlice]](#manual-String___toSlice)`
- `"abc \t ".[trimAsciiEnd]](#manual-String___trimAsciiEnd) == "abc".[toSlice]](#manual-String___toSlice)`
- `" abc ".[trimAsciiEnd]](#manual-String___trimAsciiEnd) == " abc".[toSlice]](#manual-String___toSlice)`
- `"abc\ndef\n".[trimAsciiEnd]](#manual-String___trimAsciiEnd) == "abc\ndef".[toSlice]](#manual-String___toSlice)`

def

```lean
[String.removeLeadingSpaces]](#manual-String___removeLeadingSpaces) (s : [String]](#manual-String___ofByteArray)) : [String]](#manual-String___ofByteArray)



[String.removeLeadingSpaces]](#manual-String___removeLeadingSpaces) (s : [String]](#manual-String___ofByteArray)) :
  [String]](#manual-String___ofByteArray)
```

Consistently de-indents the lines in a string, removing the same amount of leading whitespace from
each line such that the least-indented line has no leading whitespace.

The number of leading whitespace characters to remove from each line is determined by counting the
number of leading space (`' '`) and tab (`'\t'`) characters on lines after the first line that also
contain non-whitespace characters. No distinction is made between tab and space characters; both
count equally.

The least number of leading whitespace characters found is then removed from the beginning of each
line. The first line's leading whitespace is not counted when determining how far to de-indent the
string, but leading whitespace is removed from it.

Examples:

- `"Here:\n fun x =>\n x + 1".[removeLeadingSpaces]](#manual-String___removeLeadingSpaces) = "Here:\nfun x =>\n x + 1"`
- `"Here:\n\t\tfun x =>\n\t \tx + 1".[removeLeadingSpaces]](#manual-String___removeLeadingSpaces) = "Here:\nfun x =>\n \tx + 1"`
- `"Here:\n\t\tfun x =>\n \n\t \tx + 1".[removeLeadingSpaces]](#manual-String___removeLeadingSpaces) = "Here:\nfun x =>\n\n \tx + 1"`

def

```lean
[String.front]](#manual-String___front) (s : [String]](#manual-String___ofByteArray)) : [Char]](#manual-Char___mk)



[String.front]](#manual-String___front) (s : [String]](#manual-String___ofByteArray)) : [Char]](#manual-Char___mk)
```

Returns the first character in `s`. If `s = ""`, returns `([default]](#manual-Inhabited___mk) : [Char]](#manual-Char___mk))`.

Examples:

- `"abc".[front]](#manual-String___front) = 'a'`
- `"".[front]](#manual-String___front) = ([default]](#manual-Inhabited___mk) : [Char]](#manual-Char___mk))`

def

```lean
[String.back]](#manual-String___back) (s : [String]](#manual-String___ofByteArray)) : [Char]](#manual-Char___mk)



[String.back]](#manual-String___back) (s : [String]](#manual-String___ofByteArray)) : [Char]](#manual-Char___mk)
```

Returns the last character in `s`. If `s = ""`, returns `([default]](#manual-Inhabited___mk) : [Char]](#manual-Char___mk))`.

Examples:

- `"abc".[back]](#manual-String___back) = 'c'`
- `"".[back]](#manual-String___back) = ([default]](#manual-Inhabited___mk) : [Char]](#manual-Char___mk))`

def

```lean
String.find {ρ : Type} {σ : [String.Slice]](#manual-String___Slice___mk) → Type}
  [(s : [String.Slice]](#manual-String___Slice___mk)) →
      [Std.Iterator](https://lean-lang.org/doc/reference/latest/Iterators/Iterator-Definitions/#Std___Iterator___mk) (σ s) [Id]](#manual-Id) (String.Slice.Pattern.SearchStep s)]
  [(s : [String.Slice]](#manual-String___Slice___mk)) → [Std.IteratorLoop](https://lean-lang.org/doc/reference/latest/Iterators/Iterator-Definitions/#Std___IteratorLoop___mk) (σ s) [Id]](#manual-Id) [Id]](#manual-Id)] (s : [String]](#manual-String___ofByteArray))
  (pattern : ρ) [[String.Slice.Pattern.ToForwardSearcher]](#manual-String___Slice___Pattern___ToForwardSearcher___mk) pattern σ] :
  s.[Pos]](#manual-String___Pos___mk)



String.find {ρ : Type}
  {σ : [String.Slice]](#manual-String___Slice___mk) → Type}
  [(s : [String.Slice]](#manual-String___Slice___mk)) →
      [Std.Iterator](https://lean-lang.org/doc/reference/latest/Iterators/Iterator-Definitions/#Std___Iterator___mk) (σ s) [Id]](#manual-Id)
        (String.Slice.Pattern.SearchStep
          s)]
  [(s : [String.Slice]](#manual-String___Slice___mk)) →
      [Std.IteratorLoop](https://lean-lang.org/doc/reference/latest/Iterators/Iterator-Definitions/#Std___IteratorLoop___mk) (σ s) [Id]](#manual-Id) [Id]](#manual-Id)]
  (s : [String]](#manual-String___ofByteArray)) (pattern : ρ)
  [[String.Slice.Pattern.ToForwardSearcher]](#manual-String___Slice___Pattern___ToForwardSearcher___mk)
      pattern σ] :
  s.[Pos]](#manual-String___Pos___mk)
```

Finds the position of the first match of the pattern `pattern` in a slice `s`. If there
is no match `s.[endPos]](#manual-String___endPos)` is returned.

This function is generic over all currently supported patterns.

Examples:

- `("coffee tea water".find [Char.isWhitespace]](#manual-Char___isWhitespace)).[get!]](#manual-String___Pos___get___) == ' '`
- `"tea".find (fun (c : [Char]](#manual-Char___mk)) => c == 'X') == "tea".[endPos]](#manual-String___endPos)`
- `("coffee tea water".find "tea").[get!]](#manual-String___Pos___get___) == 't'`

def

```lean
[String.revFind?]](#manual-String___revFind___) {ρ : Type} {σ : [String.Slice]](#manual-String___Slice___mk) → Type}
  [(s : [String.Slice]](#manual-String___Slice___mk)) →
      [Std.Iterator](https://lean-lang.org/doc/reference/latest/Iterators/Iterator-Definitions/#Std___Iterator___mk) (σ s) [Id]](#manual-Id) (String.Slice.Pattern.SearchStep s)]
  [(s : [String.Slice]](#manual-String___Slice___mk)) → [Std.IteratorLoop](https://lean-lang.org/doc/reference/latest/Iterators/Iterator-Definitions/#Std___IteratorLoop___mk) (σ s) [Id]](#manual-Id) [Id]](#manual-Id)] (s : [String]](#manual-String___ofByteArray))
  (pattern : ρ) [[String.Slice.Pattern.ToBackwardSearcher]](#manual-String___Slice___Pattern___ToBackwardSearcher___mk) pattern σ] :
  [Option](https://lean-lang.org/doc/reference/latest/Basic-Types/Optional-Values/#Option___none) s.[Pos]](#manual-String___Pos___mk)



[String.revFind?]](#manual-String___revFind___) {ρ : Type}
  {σ : [String.Slice]](#manual-String___Slice___mk) → Type}
  [(s : [String.Slice]](#manual-String___Slice___mk)) →
      [Std.Iterator](https://lean-lang.org/doc/reference/latest/Iterators/Iterator-Definitions/#Std___Iterator___mk) (σ s) [Id]](#manual-Id)
        (String.Slice.Pattern.SearchStep
          s)]
  [(s : [String.Slice]](#manual-String___Slice___mk)) →
      [Std.IteratorLoop](https://lean-lang.org/doc/reference/latest/Iterators/Iterator-Definitions/#Std___IteratorLoop___mk) (σ s) [Id]](#manual-Id) [Id]](#manual-Id)]
  (s : [String]](#manual-String___ofByteArray)) (pattern : ρ)
  [[String.Slice.Pattern.ToBackwardSearcher]](#manual-String___Slice___Pattern___ToBackwardSearcher___mk)
      pattern σ] :
  [Option](https://lean-lang.org/doc/reference/latest/Basic-Types/Optional-Values/#Option___none) s.[Pos]](#manual-String___Pos___mk)
```

Finds the position of the first match of the pattern `pattern` in a string, starting
from the end of the slice and traversing towards the start. If there is no match `[none](https://lean-lang.org/doc/reference/latest/Basic-Types/Optional-Values/#Option___none)` is
returned.

This function is generic over all currently supported patterns except
`[String]](#manual-String___ofByteArray)`/`[String.Slice]](#manual-String___Slice___mk)`.

Examples:

- `("coffee tea water".[toSlice]](#manual-String___toSlice).[revFind?]](#manual-String___Slice___revFind___) [Char.isWhitespace]](#manual-Char___isWhitespace)).[map](https://lean-lang.org/doc/reference/latest/Basic-Types/Optional-Values/#Option___map) (·.[get!]](#manual-String___Slice___Pos___get___)) == [some](https://lean-lang.org/doc/reference/latest/Basic-Types/Optional-Values/#Option___none) ' '`
- `"tea".[toSlice]](#manual-String___toSlice).[revFind?]](#manual-String___Slice___revFind___) (fun (c : [Char]](#manual-Char___mk)) => c == 'X') == [none](https://lean-lang.org/doc/reference/latest/Basic-Types/Optional-Values/#Option___none)`

def

```lean
[String.contains]](#manual-String___contains) {ρ : Type} {σ : [String.Slice]](#manual-String___Slice___mk) → Type}
  [(s : [String.Slice]](#manual-String___Slice___mk)) →
      [Std.Iterator](https://lean-lang.org/doc/reference/latest/Iterators/Iterator-Definitions/#Std___Iterator___mk) (σ s) [Id]](#manual-Id) (String.Slice.Pattern.SearchStep s)]
  [(s : [String.Slice]](#manual-String___Slice___mk)) → [Std.IteratorLoop](https://lean-lang.org/doc/reference/latest/Iterators/Iterator-Definitions/#Std___IteratorLoop___mk) (σ s) [Id]](#manual-Id) [Id]](#manual-Id)] (s : [String]](#manual-String___ofByteArray))
  (pat : ρ) [[String.Slice.Pattern.ToForwardSearcher]](#manual-String___Slice___Pattern___ToForwardSearcher___mk) pat σ] : [Bool](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)



[String.contains]](#manual-String___contains) {ρ : Type}
  {σ : [String.Slice]](#manual-String___Slice___mk) → Type}
  [(s : [String.Slice]](#manual-String___Slice___mk)) →
      [Std.Iterator](https://lean-lang.org/doc/reference/latest/Iterators/Iterator-Definitions/#Std___Iterator___mk) (σ s) [Id]](#manual-Id)
        (String.Slice.Pattern.SearchStep
          s)]
  [(s : [String.Slice]](#manual-String___Slice___mk)) →
      [Std.IteratorLoop](https://lean-lang.org/doc/reference/latest/Iterators/Iterator-Definitions/#Std___IteratorLoop___mk) (σ s) [Id]](#manual-Id) [Id]](#manual-Id)]
  (s : [String]](#manual-String___ofByteArray)) (pat : ρ)
  [[String.Slice.Pattern.ToForwardSearcher]](#manual-String___Slice___Pattern___ToForwardSearcher___mk)
      pat σ] :
  [Bool](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)
```

Checks whether a string has a match of the pattern `pat` anywhere.

This function is generic over all currently supported patterns.

Examples:

- `"coffee tea water".[contains]](#manual-String___contains) [Char.isWhitespace]](#manual-Char___isWhitespace) = [true](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)`
- `"tea".[contains]](#manual-String___contains) (fun (c : [Char]](#manual-Char___mk)) => c == 'X') = [false](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)`
- `"coffee tea water".[contains]](#manual-String___contains) "tea" = [true](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)`

def

```lean
[String.replace.{u_1}]](#manual-String___replace) {ρ : Type} {σ : [String.Slice]](#manual-String___Slice___mk) → Type}
  [(s : [String.Slice]](#manual-String___Slice___mk)) →
      [Std.Iterator](https://lean-lang.org/doc/reference/latest/Iterators/Iterator-Definitions/#Std___Iterator___mk) (σ s) [Id]](#manual-Id) (String.Slice.Pattern.SearchStep s)]
  [(s : [String.Slice]](#manual-String___Slice___mk)) → [Std.IteratorLoop](https://lean-lang.org/doc/reference/latest/Iterators/Iterator-Definitions/#Std___IteratorLoop___mk) (σ s) [Id]](#manual-Id) [Id]](#manual-Id)] {α : Type u_1}
  [String.ToSlice α] (s : [String]](#manual-String___ofByteArray)) (pattern : ρ)
  [[String.Slice.Pattern.ToForwardSearcher]](#manual-String___Slice___Pattern___ToForwardSearcher___mk) pattern σ] (replacement : α) :
  [String]](#manual-String___ofByteArray)



[String.replace.{u_1}]](#manual-String___replace) {ρ : Type}
  {σ : [String.Slice]](#manual-String___Slice___mk) → Type}
  [(s : [String.Slice]](#manual-String___Slice___mk)) →
      [Std.Iterator](https://lean-lang.org/doc/reference/latest/Iterators/Iterator-Definitions/#Std___Iterator___mk) (σ s) [Id]](#manual-Id)
        (String.Slice.Pattern.SearchStep
          s)]
  [(s : [String.Slice]](#manual-String___Slice___mk)) →
      [Std.IteratorLoop](https://lean-lang.org/doc/reference/latest/Iterators/Iterator-Definitions/#Std___IteratorLoop___mk) (σ s) [Id]](#manual-Id) [Id]](#manual-Id)]
  {α : Type u_1} [String.ToSlice α]
  (s : [String]](#manual-String___ofByteArray)) (pattern : ρ)
  [[String.Slice.Pattern.ToForwardSearcher]](#manual-String___Slice___Pattern___ToForwardSearcher___mk)
      pattern σ]
  (replacement : α) : [String]](#manual-String___ofByteArray)
```

Constructs a new string obtained by replacing all occurrences of `pattern` with
`replacement` in `s`.

This function is generic over all currently supported patterns. The replacement may be a
`[String]](#manual-String___ofByteArray)` or a `[String.Slice]](#manual-String___Slice___mk)`.

Examples:

- `"red green blue".[replace]](#manual-String___replace) 'e' "" = "rd grn blu"`
- `"red green blue".[replace]](#manual-String___replace) (fun c => c == 'u' || c == 'e') "" = "rd grn bl"`
- `"red green blue".[replace]](#manual-String___replace) "e" "" = "rd grn blu"`
- `"red green blue".[replace]](#manual-String___replace) "ee" "E" = "red grEn blue"`
- `"red green blue".[replace]](#manual-String___replace) "e" "E" = "rEd grEEn bluE"`
- `"aaaaa".[replace]](#manual-String___replace) "aa" "b" = "bba"`
- `"abc".[replace]](#manual-String___replace) "" "k" = "kakbkck"`

def

```lean
String.find {ρ : Type} {σ : [String.Slice]](#manual-String___Slice___mk) → Type}
  [(s : [String.Slice]](#manual-String___Slice___mk)) →
      [Std.Iterator](https://lean-lang.org/doc/reference/latest/Iterators/Iterator-Definitions/#Std___Iterator___mk) (σ s) [Id]](#manual-Id) (String.Slice.Pattern.SearchStep s)]
  [(s : [String.Slice]](#manual-String___Slice___mk)) → [Std.IteratorLoop](https://lean-lang.org/doc/reference/latest/Iterators/Iterator-Definitions/#Std___IteratorLoop___mk) (σ s) [Id]](#manual-Id) [Id]](#manual-Id)] (s : [String]](#manual-String___ofByteArray))
  (pattern : ρ) [[String.Slice.Pattern.ToForwardSearcher]](#manual-String___Slice___Pattern___ToForwardSearcher___mk) pattern σ] :
  s.[Pos]](#manual-String___Pos___mk)



String.find {ρ : Type}
  {σ : [String.Slice]](#manual-String___Slice___mk) → Type}
  [(s : [String.Slice]](#manual-String___Slice___mk)) →
      [Std.Iterator](https://lean-lang.org/doc/reference/latest/Iterators/Iterator-Definitions/#Std___Iterator___mk) (σ s) [Id]](#manual-Id)
        (String.Slice.Pattern.SearchStep
          s)]
  [(s : [String.Slice]](#manual-String___Slice___mk)) →
      [Std.IteratorLoop](https://lean-lang.org/doc/reference/latest/Iterators/Iterator-Definitions/#Std___IteratorLoop___mk) (σ s) [Id]](#manual-Id) [Id]](#manual-Id)]
  (s : [String]](#manual-String___ofByteArray)) (pattern : ρ)
  [[String.Slice.Pattern.ToForwardSearcher]](#manual-String___Slice___Pattern___ToForwardSearcher___mk)
      pattern σ] :
  s.[Pos]](#manual-String___Pos___mk)
```

Finds the position of the first match of the pattern `pattern` in a slice `s`. If there
is no match `s.[endPos]](#manual-String___endPos)` is returned.

This function is generic over all currently supported patterns.

Examples:

- `("coffee tea water".find [Char.isWhitespace]](#manual-Char___isWhitespace)).[get!]](#manual-String___Pos___get___) == ' '`
- `"tea".find (fun (c : [Char]](#manual-Char___mk)) => c == 'X') == "tea".[endPos]](#manual-String___endPos)`
- `("coffee tea water".find "tea").[get!]](#manual-String___Pos___get___) == 't'`

#### 20.8.4.7. Folds and Aggregation {#manual-string-api-fold}

def

```lean
[String.map]](#manual-String___map) (f : [Char]](#manual-Char___mk) → [Char]](#manual-Char___mk)) (s : [String]](#manual-String___ofByteArray)) : [String]](#manual-String___ofByteArray)



[String.map]](#manual-String___map) (f : [Char]](#manual-Char___mk) → [Char]](#manual-Char___mk))
  (s : [String]](#manual-String___ofByteArray)) : [String]](#manual-String___ofByteArray)
```

Applies the function `f` to every character in a string, returning a string that contains the
resulting characters.

Examples:

- `"abc123".[map]](#manual-String___map) [Char.toUpper]](#manual-Char___toUpper) = "ABC123"`
- `"".[map]](#manual-String___map) [Char.toUpper]](#manual-Char___toUpper) = ""`

def

```lean
[String.foldl.{u}]](#manual-String___foldl) {α : Type u} (f : α → [Char]](#manual-Char___mk) → α) (init : α)
  (s : [String]](#manual-String___ofByteArray)) : α



[String.foldl.{u}]](#manual-String___foldl) {α : Type u}
  (f : α → [Char]](#manual-Char___mk) → α) (init : α)
  (s : [String]](#manual-String___ofByteArray)) : α
```

Folds a function over a string from the start, accumulating a value starting with `init`. The
accumulated value is combined with each character in order, using `f`.

Examples:

- `"coffee tea water".[foldl]](#manual-String___foldl) (fun n c => [if]](#manual-termIfThenElse) c.[isWhitespace]](#manual-Char___isWhitespace) [then]](#manual-termIfThenElse) n + 1 [else]](#manual-termIfThenElse) n) 0 = 2`
- `"coffee tea and water".[foldl]](#manual-String___foldl) (fun n c => [if]](#manual-termIfThenElse) c.[isWhitespace]](#manual-Char___isWhitespace) [then]](#manual-termIfThenElse) n + 1 [else]](#manual-termIfThenElse) n) 0 = 3`
- `"coffee tea water".[foldl]](#manual-String___foldl) (·.[push]](#manual-String___push) ·) "" = "coffee tea water"`

def

```lean
[String.foldr.{u}]](#manual-String___foldr) {α : Type u} (f : [Char]](#manual-Char___mk) → α → α) (init : α)
  (s : [String]](#manual-String___ofByteArray)) : α



[String.foldr.{u}]](#manual-String___foldr) {α : Type u}
  (f : [Char]](#manual-Char___mk) → α → α) (init : α)
  (s : [String]](#manual-String___ofByteArray)) : α
```

Folds a function over a string from the right, accumulating a value starting with `init`. The
accumulated value is combined with each character in reverse order, using `f`.

Examples:

- `"coffee tea water".[foldr]](#manual-String___foldr) (fun c n => [if]](#manual-termIfThenElse) c.[isWhitespace]](#manual-Char___isWhitespace) [then]](#manual-termIfThenElse) n + 1 [else]](#manual-termIfThenElse) n) 0 = 2`
- `"coffee tea and water".[foldr]](#manual-String___foldr) (fun c n => [if]](#manual-termIfThenElse) c.[isWhitespace]](#manual-Char___isWhitespace) [then]](#manual-termIfThenElse) n + 1 [else]](#manual-termIfThenElse) n) 0 = 3`
- `"coffee tea water".[foldr]](#manual-String___foldr) (fun c s => s.[push]](#manual-String___push) c) "" = "retaw aet eeffoc"`

def

```lean
[String.all]](#manual-String___all) {ρ : Type} (s : [String]](#manual-String___ofByteArray)) (pat : ρ)
  [[String.Slice.Pattern.ForwardPattern]](#manual-String___Slice___Pattern___ForwardPattern___mk) pat] : [Bool](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)



[String.all]](#manual-String___all) {ρ : Type} (s : [String]](#manual-String___ofByteArray))
  (pat : ρ)
  [[String.Slice.Pattern.ForwardPattern]](#manual-String___Slice___Pattern___ForwardPattern___mk)
      pat] :
  [Bool](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)
```

Checks whether a string only consists of matches of the pattern `pat`.

Short-circuits at the first pattern mis-match.

This function is generic over all currently supported patterns.

Examples:

- `"brown".[all]](#manual-String___all) [Char.isLower]](#manual-Char___isLower) = [true](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)`
- `"brown and orange".[all]](#manual-String___all) [Char.isLower]](#manual-Char___isLower) = [false](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)`
- `"aaaaaa".[all]](#manual-String___all) 'a' = [true](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)`
- `"aaaaaa".[all]](#manual-String___all) "aa" = [true](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)`
- `"aaaaaaa".[all]](#manual-String___all) "aa" = [false](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)`

def

```lean
[String.any]](#manual-String___any) {ρ : Type} {σ : [String.Slice]](#manual-String___Slice___mk) → Type}
  [(s : [String.Slice]](#manual-String___Slice___mk)) →
      [Std.Iterator](https://lean-lang.org/doc/reference/latest/Iterators/Iterator-Definitions/#Std___Iterator___mk) (σ s) [Id]](#manual-Id) (String.Slice.Pattern.SearchStep s)]
  [(s : [String.Slice]](#manual-String___Slice___mk)) → [Std.IteratorLoop](https://lean-lang.org/doc/reference/latest/Iterators/Iterator-Definitions/#Std___IteratorLoop___mk) (σ s) [Id]](#manual-Id) [Id]](#manual-Id)] (s : [String]](#manual-String___ofByteArray))
  (pat : ρ) [[String.Slice.Pattern.ToForwardSearcher]](#manual-String___Slice___Pattern___ToForwardSearcher___mk) pat σ] : [Bool](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)



[String.any]](#manual-String___any) {ρ : Type}
  {σ : [String.Slice]](#manual-String___Slice___mk) → Type}
  [(s : [String.Slice]](#manual-String___Slice___mk)) →
      [Std.Iterator](https://lean-lang.org/doc/reference/latest/Iterators/Iterator-Definitions/#Std___Iterator___mk) (σ s) [Id]](#manual-Id)
        (String.Slice.Pattern.SearchStep
          s)]
  [(s : [String.Slice]](#manual-String___Slice___mk)) →
      [Std.IteratorLoop](https://lean-lang.org/doc/reference/latest/Iterators/Iterator-Definitions/#Std___IteratorLoop___mk) (σ s) [Id]](#manual-Id) [Id]](#manual-Id)]
  (s : [String]](#manual-String___ofByteArray)) (pat : ρ)
  [[String.Slice.Pattern.ToForwardSearcher]](#manual-String___Slice___Pattern___ToForwardSearcher___mk)
      pat σ] :
  [Bool](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)
```

Checks whether a string has a match of the pattern `pat` anywhere.

This function is generic over all currently supported patterns.

Examples:

- `"coffee tea water".[contains]](#manual-String___contains) [Char.isWhitespace]](#manual-Char___isWhitespace) = [true](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)`
- `"tea".[contains]](#manual-String___contains) (fun (c : [Char]](#manual-Char___mk)) => c == 'X') = [false](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)`
- `"coffee tea water".[contains]](#manual-String___contains) "tea" = [true](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)`

#### 20.8.4.8. Comparisons {#manual-string-api-compare}

The `[LT]](#manual-LT___mk) [String]](#manual-String___ofByteArray)` instance is defined by the lexicographic ordering on strings based on the `[LT]](#manual-LT___mk) [Char]](#manual-Char___mk)` instance.
Logically, this is modeled by the lexicographic ordering on the lists that model strings, so `List.Lex` defines the order.
It is decidable, and the decision procedure is overridden at runtime with efficient code that takes advantage of the run-time representation of strings.

def

```lean
[String.le]](#manual-String___le) (a b : [String]](#manual-String___ofByteArray)) : Prop



[String.le]](#manual-String___le) (a b : [String]](#manual-String___ofByteArray)) : Prop
```

Non-strict inequality on strings, typically used via the `≤` operator.

`a ≤ b` is defined to mean `¬ b < a`.

def

```lean
[String.firstDiffPos]](#manual-String___firstDiffPos) (a b : [String]](#manual-String___ofByteArray)) : [String.Pos.Raw]](#manual-String___Pos___Raw___mk)



[String.firstDiffPos]](#manual-String___firstDiffPos) (a b : [String]](#manual-String___ofByteArray)) :
  [String.Pos.Raw]](#manual-String___Pos___Raw___mk)
```

Returns the first position where the two strings differ.

If one string is a prefix of the other, then the returned position is the end position of the
shorter string. If the strings are identical, then their end position is returned.

Examples:

- `"tea".[firstDiffPos]](#manual-String___firstDiffPos) "ten" = ⟨2⟩`
- `"tea".[firstDiffPos]](#manual-String___firstDiffPos) "tea" = ⟨3⟩`
- `"tea".[firstDiffPos]](#manual-String___firstDiffPos) "teas" = ⟨3⟩`
- `"teas".[firstDiffPos]](#manual-String___firstDiffPos) "tea" = ⟨3⟩`

def

```lean
[String.isPrefixOf]](#manual-String___isPrefixOf) (p s : [String]](#manual-String___ofByteArray)) : [Bool](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)



[String.isPrefixOf]](#manual-String___isPrefixOf) (p s : [String]](#manual-String___ofByteArray)) : [Bool](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)
```

Checks whether the second string (`s`) begins with a prefix (`p`).

This function is generic over all currently supported patterns.

`[String.startsWith]](#manual-String___startsWith)` is a version that takes the potential prefix after the string.

Examples:

- `"red".[isPrefixOf]](#manual-String___isPrefixOf) "red green blue" = [true](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)`
- `"green".[isPrefixOf]](#manual-String___isPrefixOf) "red green blue" = [false](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)`
- `"".[isPrefixOf]](#manual-String___isPrefixOf) "red green blue" = [true](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)`

def

```lean
[String.startsWith]](#manual-String___startsWith) {ρ : Type} (s : [String]](#manual-String___ofByteArray)) (pat : ρ)
  [[String.Slice.Pattern.ForwardPattern]](#manual-String___Slice___Pattern___ForwardPattern___mk) pat] : [Bool](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)



[String.startsWith]](#manual-String___startsWith) {ρ : Type} (s : [String]](#manual-String___ofByteArray))
  (pat : ρ)
  [[String.Slice.Pattern.ForwardPattern]](#manual-String___Slice___Pattern___ForwardPattern___mk)
      pat] :
  [Bool](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)
```

Checks whether the first string (`s`) begins with the pattern (`pat`).

`[String.isPrefixOf]](#manual-String___isPrefixOf)` is a version that takes the
potential prefix before the string.

Examples:

- `"red green blue".[startsWith]](#manual-String___startsWith) "red" = [true](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)`
- `"red green blue".[startsWith]](#manual-String___startsWith) "green" = [false](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)`
- `"red green blue".[startsWith]](#manual-String___startsWith) "" = [true](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)`
- `"red green blue".[startsWith]](#manual-String___startsWith) 'r' = [true](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)`
- `"red green blue".[startsWith]](#manual-String___startsWith) [Char.isLower]](#manual-Char___isLower) = [true](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)`

def

```lean
[String.endsWith]](#manual-String___endsWith) {ρ : Type} (s : [String]](#manual-String___ofByteArray)) (pat : ρ)
  [[String.Slice.Pattern.BackwardPattern]](#manual-String___Slice___Pattern___BackwardPattern___mk) pat] : [Bool](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)



[String.endsWith]](#manual-String___endsWith) {ρ : Type} (s : [String]](#manual-String___ofByteArray))
  (pat : ρ)
  [[String.Slice.Pattern.BackwardPattern]](#manual-String___Slice___Pattern___BackwardPattern___mk)
      pat] :
  [Bool](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)
```

Checks whether the string (`s`) ends with the pattern (`pat`).

This function is generic over all currently supported patterns.

Examples:

- `"red green blue".[endsWith]](#manual-String___endsWith) "blue" = [true](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)`
- `"red green blue".[endsWith]](#manual-String___endsWith) "green" = [false](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)`
- `"red green blue".[endsWith]](#manual-String___endsWith) "" = [true](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)`
- `"red green blue".[endsWith]](#manual-String___endsWith) 'e' = [true](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)`
- `"red green blue".[endsWith]](#manual-String___endsWith) [Char.isLower]](#manual-Char___isLower) = [true](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)`

def

```lean
[String.decEq]](#manual-String___decEq) (s₁ s₂ : [String]](#manual-String___ofByteArray)) : [Decidable]](#manual-Decidable___isFalse) [(]](#manual-Eq___refl)s₁ [=]](#manual-Eq___refl) s₂[)]](#manual-Eq___refl)



[String.decEq]](#manual-String___decEq) (s₁ s₂ : [String]](#manual-String___ofByteArray)) :
  [Decidable]](#manual-Decidable___isFalse) [(]](#manual-Eq___refl)s₁ [=]](#manual-Eq___refl) s₂[)]](#manual-Eq___refl)
```

Decides whether two strings are equal. Normally used via the `[DecidableEq]](#manual-DecidableEq) [String]](#manual-String___ofByteArray)` instance and the
`=` operator.

At runtime, this function is overridden with an efficient native implementation.

opaque

```lean
[String.hash]](#manual-String___hash) (s : [String]](#manual-String___ofByteArray)) : [UInt64]](#manual-UInt64___ofBitVec)



[String.hash]](#manual-String___hash) (s : [String]](#manual-String___ofByteArray)) : [UInt64]](#manual-UInt64___ofBitVec)
```

Computes a hash for strings.

#### 20.8.4.9. Manipulation {#manual-string-api-modify}

def

```lean
[String.splitToList]](#manual-String___splitToList) (s : [String]](#manual-String___ofByteArray)) (p : [Char]](#manual-Char___mk) → [Bool](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)) : [List](https://lean-lang.org/doc/reference/latest/Basic-Types/Linked-Lists/#List___nil) [String]](#manual-String___ofByteArray)



[String.splitToList]](#manual-String___splitToList) (s : [String]](#manual-String___ofByteArray))
  (p : [Char]](#manual-Char___mk) → [Bool](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)) : [List](https://lean-lang.org/doc/reference/latest/Basic-Types/Linked-Lists/#List___nil) [String]](#manual-String___ofByteArray)
```

Splits a string at each character for which `p` returns `[true](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)`.

The characters that satisfy `p` are not included in any of the resulting strings. If multiple
characters in a row satisfy `p`, then the resulting list will contain empty strings.

This is a legacy function. Use `String.split` instead.

Examples:

- `"coffee tea water".split (·.isWhitespace) = ["coffee", "tea", "water"]`
- `"coffee tea water".split (·.isWhitespace) = ["coffee", "", "tea", "", "water"]`
- `"fun x =>\n x + 1\n".split (· == '\n') = ["fun x =>", " x + 1", ""]`

def

```lean
[String.splitOn]](#manual-String___splitOn) (s : [String]](#manual-String___ofByteArray)) (sep : [String]](#manual-String___ofByteArray) := " ") : [List](https://lean-lang.org/doc/reference/latest/Basic-Types/Linked-Lists/#List___nil) [String]](#manual-String___ofByteArray)



[String.splitOn]](#manual-String___splitOn) (s : [String]](#manual-String___ofByteArray))
  (sep : [String]](#manual-String___ofByteArray) := " ") : [List](https://lean-lang.org/doc/reference/latest/Basic-Types/Linked-Lists/#List___nil) [String]](#manual-String___ofByteArray)
```

Splits a string `s` on occurrences of the separator string `sep`. The default separator is `" "`.

When `sep` is empty, the result is `[s]`. When `sep` occurs in overlapping patterns, the first match
is taken. There will always be exactly `n+1` elements in the returned list if there were `n`
non-overlapping matches of `sep` in the string. The separators are not included in the returned
substrings.

This is a legacy function. Use `String.split` instead.

Examples:

- `"here is some text ".[splitOn]](#manual-String___splitOn) = ["here", "is", "some", "text", ""]`
- `"here is some text ".[splitOn]](#manual-String___splitOn) "some" = ["here is ", " text "]`
- `"here is some text ".[splitOn]](#manual-String___splitOn) "" = ["here is some text "]`
- `"ababacabac".[splitOn]](#manual-String___splitOn) "aba" = ["", "bac", "c"]`

def

```lean
[String.push]](#manual-String___push) : [String]](#manual-String___ofByteArray) → [Char]](#manual-Char___mk) → [String]](#manual-String___ofByteArray)



[String.push]](#manual-String___push) : [String]](#manual-String___ofByteArray) → [Char]](#manual-Char___mk) → [String]](#manual-String___ofByteArray)
```

Adds a character to the end of a string.

The internal implementation uses dynamic arrays and will perform destructive updates
if the string is not shared.

Examples:

- `"abc".[push]](#manual-String___push) 'd' = "abcd"`
- `"".[push]](#manual-String___push) 'a' = "a"`

def

```lean
[String.pushn]](#manual-String___pushn) (s : [String]](#manual-String___ofByteArray)) (c : [Char]](#manual-Char___mk)) (n : [Nat]](#manual-Nat___zero)) : [String]](#manual-String___ofByteArray)



[String.pushn]](#manual-String___pushn) (s : [String]](#manual-String___ofByteArray)) (c : [Char]](#manual-Char___mk))
  (n : [Nat]](#manual-Nat___zero)) : [String]](#manual-String___ofByteArray)
```

Adds multiple repetitions of a character to the end of a string.

Returns `s`, with `n` repetitions of `c` at the end. Internally, the implementation repeatedly calls
`[String.push]](#manual-String___push)`, so the string is modified in-place if there is a unique reference to it.

Examples:

- `"indeed".[pushn]](#manual-String___pushn) '!' 2 = "indeed!!"`
- `"indeed".[pushn]](#manual-String___pushn) '!' 0 = "indeed"`
- `"".[pushn]](#manual-String___pushn) ' ' 4 = " "`

def

```lean
[String.capitalize]](#manual-String___capitalize) (s : [String]](#manual-String___ofByteArray)) : [String]](#manual-String___ofByteArray)



[String.capitalize]](#manual-String___capitalize) (s : [String]](#manual-String___ofByteArray)) : [String]](#manual-String___ofByteArray)
```

Replaces the first character in `s` with the result of applying `[Char.toUpper]](#manual-Char___toUpper)` to it. Returns the
empty string if the string is empty.

`[Char.toUpper]](#manual-Char___toUpper)` has no effect on characters outside of the range `'a'`–`'z'`.

Examples:

- `"orange".[capitalize]](#manual-String___capitalize) = "Orange"`
- `"ORANGE".[capitalize]](#manual-String___capitalize) = "ORANGE"`
- `"".[capitalize]](#manual-String___capitalize) = ""`

def

```lean
[String.decapitalize]](#manual-String___decapitalize) (s : [String]](#manual-String___ofByteArray)) : [String]](#manual-String___ofByteArray)



[String.decapitalize]](#manual-String___decapitalize) (s : [String]](#manual-String___ofByteArray)) : [String]](#manual-String___ofByteArray)
```

Replaces the first character in `s` with the result of applying `[Char.toLower]](#manual-Char___toLower)` to it. Returns the
empty string if the string is empty.

`[Char.toLower]](#manual-Char___toLower)` has no effect on characters outside of the range `'A'`–`'Z'`.

Examples:

- `"Orange".[decapitalize]](#manual-String___decapitalize) = "orange"`
- `"ORANGE".[decapitalize]](#manual-String___decapitalize) = "oRANGE"`
- `"".[decapitalize]](#manual-String___decapitalize) = ""`

def

```lean
[String.toUpper]](#manual-String___toUpper) (s : [String]](#manual-String___ofByteArray)) : [String]](#manual-String___ofByteArray)



[String.toUpper]](#manual-String___toUpper) (s : [String]](#manual-String___ofByteArray)) : [String]](#manual-String___ofByteArray)
```

Replaces each character in `s` with the result of applying `[Char.toUpper]](#manual-Char___toUpper)` to it.

`[Char.toUpper]](#manual-Char___toUpper)` has no effect on characters outside of the range `'a'`–`'z'`.

Examples:

- `"orange".[toUpper]](#manual-String___toUpper) = "ORANGE"`
- `"abc123".[toUpper]](#manual-String___toUpper) = "ABC123"`

def

```lean
[String.toLower]](#manual-String___toLower) (s : [String]](#manual-String___ofByteArray)) : [String]](#manual-String___ofByteArray)



[String.toLower]](#manual-String___toLower) (s : [String]](#manual-String___ofByteArray)) : [String]](#manual-String___ofByteArray)
```

Replaces each character in `s` with the result of applying `[Char.toLower]](#manual-Char___toLower)` to it.

`[Char.toLower]](#manual-Char___toLower)` has no effect on characters outside of the range `'A'`–`'Z'`.

Examples:

- `"ORANGE".[toLower]](#manual-String___toLower) = "orange"`
- `"Orange".[toLower]](#manual-String___toLower) = "orange"`
- `"ABc123".[toLower]](#manual-String___toLower) = "abc123"`

#### 20.8.4.10. Legacy Iterators {#manual-string-iterators}

For backwards compatibility, Lean includes legacy string iterators.
Fundamentally, a `[String.Legacy.Iterator]](#manual-String___Legacy___Iterator___mk)` is a pair of a string and a valid position in the string.
Iterators provide functions for getting the current character (`[curr]](#manual-String___Legacy___Iterator___curr)`), replacing the current character (`[setCurr]](#manual-String___Legacy___Iterator___setCurr)`), checking whether the iterator can move to the left or the right (`[hasPrev]](#manual-String___Legacy___Iterator___hasPrev)` and `[hasNext]](#manual-String___Legacy___Iterator___hasNext)`, respectively), and moving the iterator (`[prev]](#manual-String___Legacy___Iterator___prev)` and `[next]](#manual-String___Legacy___Iterator___next)`, respectively).
Clients are responsible for checking whether they've reached the beginning or end of the string; otherwise, the iterator ensures that its position always points at a character.
However, `[String.Legacy.Iterator]](#manual-String___Legacy___Iterator___mk)` does not include proofs of these well-formedness conditions, which can make it more difficult to use in verified code.

structure

```lean
[String.Legacy.Iterator]](#manual-String___Legacy___Iterator___mk) : Type



[String.Legacy.Iterator]](#manual-String___Legacy___Iterator___mk) : Type
```

An iterator over the characters (Unicode code points) in a `[String]](#manual-String___ofByteArray)`. Typically created by
`String.iter`.

This is a no-longer-supported legacy API that will be removed in a future release. You should use
`[String.Pos]](#manual-String___Pos___mk)` instead, which is similar, but safer. To iterate over a string `s`, start with
`p : s.startPos`, advance it using `p.next`, access the current character using `p.get` and
check if the position is at the end using `p = s.endPos` or `p.IsAtEnd`.

String iterators pair a string with a valid byte index. This allows efficient character-by-character
processing of strings while avoiding the need to manually ensure that byte indices are used with the
correct strings.

An iterator is *valid* if the position `i` is *valid* for the string `s`, meaning `0 ≤ i ≤ s.rawEndPos`
and `i` lies on a UTF8 byte boundary. If `i = s.rawEndPos`, the iterator is at the end of the string.

Most operations on iterators return unspecified values if the iterator is not valid. The functions
in the `String.Iterator` API rule out the creation of invalid iterators, with two exceptions:

- `Iterator.next iter` is invalid if `iter` is already at the end of the string (`iter.atEnd` is
  `[true](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)`), and
- `Iterator.forward iter n`/`Iterator.nextn iter n` is invalid if `n` is strictly greater than the
  number of remaining characters.

Constructor

```lean
[String.Legacy.Iterator.mk]](#manual-String___Legacy___Iterator___mk)
```

Fields

```lean
s : [String]](#manual-String___ofByteArray)
```

The string being iterated over.

```lean
i : [String.Pos.Raw]](#manual-String___Pos___Raw___mk)
```

The current UTF-8 byte position in the string `s`.

This position is not guaranteed to be valid for the string. If the position is not valid, then the
current character is `([default]](#manual-Inhabited___mk) : [Char]](#manual-Char___mk))`, similar to `String.get` on an invalid position.

def

```lean
[String.Legacy.iter]](#manual-String___Legacy___iter) (s : [String]](#manual-String___ofByteArray)) : [String.Legacy.Iterator]](#manual-String___Legacy___Iterator___mk)



[String.Legacy.iter]](#manual-String___Legacy___iter) (s : [String]](#manual-String___ofByteArray)) :
  [String.Legacy.Iterator]](#manual-String___Legacy___Iterator___mk)
```

Creates an iterator at the beginning of the string.

This is a no-longer-supported legacy API that will be removed in a future release. You should use
`[String.Pos]](#manual-String___Pos___mk)` instead, which is similar, but safer. To iterate over a string `s`, start with
`p : s.startPos`, advance it using `p.next`, access the current character using `p.get` and
check if the position is at the end using `p = s.[endPos]](#manual-String___endPos)` or `p.IsAtEnd`.

def

```lean
[String.Legacy.mkIterator]](#manual-String___Legacy___mkIterator) (s : [String]](#manual-String___ofByteArray)) : [String.Legacy.Iterator]](#manual-String___Legacy___Iterator___mk)



[String.Legacy.mkIterator]](#manual-String___Legacy___mkIterator) (s : [String]](#manual-String___ofByteArray)) :
  [String.Legacy.Iterator]](#manual-String___Legacy___Iterator___mk)
```

Creates an iterator at the beginning of the string.

This is a no-longer-supported legacy API that will be removed in a future release. You should use
`[String.Pos]](#manual-String___Pos___mk)` instead, which is similar, but safer. To iterate over a string `s`, start with
`p : s.startPos`, advance it using `p.next`, access the current character using `p.get` and
check if the position is at the end using `p = s.[endPos]](#manual-String___endPos)` or `p.IsAtEnd`.

def

```lean
[String.Legacy.Iterator.curr]](#manual-String___Legacy___Iterator___curr) : [String.Legacy.Iterator]](#manual-String___Legacy___Iterator___mk) → [Char]](#manual-Char___mk)



[String.Legacy.Iterator.curr]](#manual-String___Legacy___Iterator___curr) :
  [String.Legacy.Iterator]](#manual-String___Legacy___Iterator___mk) → [Char]](#manual-Char___mk)
```

Gets the character at the iterator's current position.

This is a no-longer-supported legacy API that will be removed in a future release. You should use
`[String.Pos]](#manual-String___Pos___mk)` instead, which is similar, but safer. To iterate over a string `s`, start with
`p : s.startPos`, advance it using `p.next`, access the current character using `p.get` and
check if the position is at the end using `p = s.endPos` or `p.IsAtEnd`.

A run-time bounds check is performed. Use `String.Iterator.curr'` to avoid redundant bounds checks.

If the position is invalid, returns `([default]](#manual-Inhabited___mk) : [Char]](#manual-Char___mk))`.

def

```lean
[String.Legacy.Iterator.curr']](#manual-String___Legacy___Iterator___curr___) (it : [String.Legacy.Iterator]](#manual-String___Legacy___Iterator___mk))
  (h : it.[hasNext]](#manual-String___Legacy___Iterator___hasNext) [=]](#manual-Eq___refl) [true](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)) : [Char]](#manual-Char___mk)



[String.Legacy.Iterator.curr']](#manual-String___Legacy___Iterator___curr___)
  (it : [String.Legacy.Iterator]](#manual-String___Legacy___Iterator___mk))
  (h : it.[hasNext]](#manual-String___Legacy___Iterator___hasNext) [=]](#manual-Eq___refl) [true](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)) : [Char]](#manual-Char___mk)
```

Gets the character at the iterator's current position.

The proof of `it.[hasNext]](#manual-String___Legacy___Iterator___hasNext)` ensures that there is, in fact, a character at the current position. This
function is faster that `String.Iterator.curr` due to avoiding a run-time bounds check.

def

```lean
[String.Legacy.Iterator.hasNext]](#manual-String___Legacy___Iterator___hasNext) : [String.Legacy.Iterator]](#manual-String___Legacy___Iterator___mk) → [Bool](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)



[String.Legacy.Iterator.hasNext]](#manual-String___Legacy___Iterator___hasNext) :
  [String.Legacy.Iterator]](#manual-String___Legacy___Iterator___mk) → [Bool](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)
```

Checks whether the iterator is at or before the string's last character.

def

```lean
[String.Legacy.Iterator.next]](#manual-String___Legacy___Iterator___next) :
  [String.Legacy.Iterator]](#manual-String___Legacy___Iterator___mk) → [String.Legacy.Iterator]](#manual-String___Legacy___Iterator___mk)



[String.Legacy.Iterator.next]](#manual-String___Legacy___Iterator___next) :
  [String.Legacy.Iterator]](#manual-String___Legacy___Iterator___mk) →
    [String.Legacy.Iterator]](#manual-String___Legacy___Iterator___mk)
```

Moves the iterator's position forward by one character, unconditionally.

This is a no-longer-supported legacy API that will be removed in a future release. You should use
`[String.Pos]](#manual-String___Pos___mk)` instead, which is similar, but safer. To iterate over a string `s`, start with
`p : s.startPos`, advance it using `p.next`, access the current character using `p.get` and
check if the position is at the end using `p = s.endPos` or `p.IsAtEnd`.

It is only valid to call this function if the iterator is not at the end of the string (i.e.
if `Iterator.atEnd` is `[false](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)`); otherwise, the resulting iterator will be invalid.

def

```lean
[String.Legacy.Iterator.next']](#manual-String___Legacy___Iterator___next___) (it : [String.Legacy.Iterator]](#manual-String___Legacy___Iterator___mk))
  (h : it.[hasNext]](#manual-String___Legacy___Iterator___hasNext) [=]](#manual-Eq___refl) [true](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)) : [String.Legacy.Iterator]](#manual-String___Legacy___Iterator___mk)



[String.Legacy.Iterator.next']](#manual-String___Legacy___Iterator___next___)
  (it : [String.Legacy.Iterator]](#manual-String___Legacy___Iterator___mk))
  (h : it.[hasNext]](#manual-String___Legacy___Iterator___hasNext) [=]](#manual-Eq___refl) [true](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)) :
  [String.Legacy.Iterator]](#manual-String___Legacy___Iterator___mk)
```

Moves the iterator's position forward by one character, unconditionally.

The proof of `it.[hasNext]](#manual-String___Legacy___Iterator___hasNext)` ensures that there is, in fact, a position that's one character forwards.
This function is faster that `String.Iterator.next` due to avoiding a run-time bounds check.

def

```lean
[String.Legacy.Iterator.forward]](#manual-String___Legacy___Iterator___forward) :
  [String.Legacy.Iterator]](#manual-String___Legacy___Iterator___mk) → [Nat]](#manual-Nat___zero) → [String.Legacy.Iterator]](#manual-String___Legacy___Iterator___mk)



[String.Legacy.Iterator.forward]](#manual-String___Legacy___Iterator___forward) :
  [String.Legacy.Iterator]](#manual-String___Legacy___Iterator___mk) →
    [Nat]](#manual-Nat___zero) → [String.Legacy.Iterator]](#manual-String___Legacy___Iterator___mk)
```

Moves the iterator's position forward by the specified number of characters.

The resulting iterator is only valid if the number of characters to skip is less than or equal
to the number of characters left in the iterator.

def

```lean
[String.Legacy.Iterator.nextn]](#manual-String___Legacy___Iterator___nextn) :
  [String.Legacy.Iterator]](#manual-String___Legacy___Iterator___mk) → [Nat]](#manual-Nat___zero) → [String.Legacy.Iterator]](#manual-String___Legacy___Iterator___mk)



[String.Legacy.Iterator.nextn]](#manual-String___Legacy___Iterator___nextn) :
  [String.Legacy.Iterator]](#manual-String___Legacy___Iterator___mk) →
    [Nat]](#manual-Nat___zero) → [String.Legacy.Iterator]](#manual-String___Legacy___Iterator___mk)
```

Moves the iterator's position forward by the specified number of characters.

The resulting iterator is only valid if the number of characters to skip is less than or equal
to the number of characters left in the iterator.

def

```lean
[String.Legacy.Iterator.hasPrev]](#manual-String___Legacy___Iterator___hasPrev) : [String.Legacy.Iterator]](#manual-String___Legacy___Iterator___mk) → [Bool](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)



[String.Legacy.Iterator.hasPrev]](#manual-String___Legacy___Iterator___hasPrev) :
  [String.Legacy.Iterator]](#manual-String___Legacy___Iterator___mk) → [Bool](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)
```

Checks whether the iterator is after the beginning of the string.

def

```lean
[String.Legacy.Iterator.prev]](#manual-String___Legacy___Iterator___prev) :
  [String.Legacy.Iterator]](#manual-String___Legacy___Iterator___mk) → [String.Legacy.Iterator]](#manual-String___Legacy___Iterator___mk)



[String.Legacy.Iterator.prev]](#manual-String___Legacy___Iterator___prev) :
  [String.Legacy.Iterator]](#manual-String___Legacy___Iterator___mk) →
    [String.Legacy.Iterator]](#manual-String___Legacy___Iterator___mk)
```

Moves the iterator's position backward by one character, unconditionally.

The position is not changed if the iterator is at the beginning of the string.

def

```lean
[String.Legacy.Iterator.prevn]](#manual-String___Legacy___Iterator___prevn) :
  [String.Legacy.Iterator]](#manual-String___Legacy___Iterator___mk) → [Nat]](#manual-Nat___zero) → [String.Legacy.Iterator]](#manual-String___Legacy___Iterator___mk)



[String.Legacy.Iterator.prevn]](#manual-String___Legacy___Iterator___prevn) :
  [String.Legacy.Iterator]](#manual-String___Legacy___Iterator___mk) →
    [Nat]](#manual-Nat___zero) → [String.Legacy.Iterator]](#manual-String___Legacy___Iterator___mk)
```

Moves the iterator's position back by the specified number of characters, stopping at the beginning
of the string.

def

```lean
[String.Legacy.Iterator.atEnd]](#manual-String___Legacy___Iterator___atEnd) : [String.Legacy.Iterator]](#manual-String___Legacy___Iterator___mk) → [Bool](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)



[String.Legacy.Iterator.atEnd]](#manual-String___Legacy___Iterator___atEnd) :
  [String.Legacy.Iterator]](#manual-String___Legacy___Iterator___mk) → [Bool](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)
```

Checks whether the iterator is past its string's last character.

def

```lean
[String.Legacy.Iterator.toEnd]](#manual-String___Legacy___Iterator___toEnd) :
  [String.Legacy.Iterator]](#manual-String___Legacy___Iterator___mk) → [String.Legacy.Iterator]](#manual-String___Legacy___Iterator___mk)



[String.Legacy.Iterator.toEnd]](#manual-String___Legacy___Iterator___toEnd) :
  [String.Legacy.Iterator]](#manual-String___Legacy___Iterator___mk) →
    [String.Legacy.Iterator]](#manual-String___Legacy___Iterator___mk)
```

Moves the iterator's position to the end of the string, just past the last character.

def

```lean
[String.Legacy.Iterator.setCurr]](#manual-String___Legacy___Iterator___setCurr) :
  [String.Legacy.Iterator]](#manual-String___Legacy___Iterator___mk) → [Char]](#manual-Char___mk) → [String.Legacy.Iterator]](#manual-String___Legacy___Iterator___mk)



[String.Legacy.Iterator.setCurr]](#manual-String___Legacy___Iterator___setCurr) :
  [String.Legacy.Iterator]](#manual-String___Legacy___Iterator___mk) →
    [Char]](#manual-Char___mk) → [String.Legacy.Iterator]](#manual-String___Legacy___Iterator___mk)
```

Replaces the current character in the string.

Does nothing if the iterator is at the end of the string. If both the replacement character and the
replaced character are 7-bit ASCII characters and the string is not shared, then it is updated
in-place and not copied.

def

```lean
[String.Legacy.Iterator.find]](#manual-String___Legacy___Iterator___find) (it : [String.Legacy.Iterator]](#manual-String___Legacy___Iterator___mk))
  (p : [Char]](#manual-Char___mk) → [Bool](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)) : [String.Legacy.Iterator]](#manual-String___Legacy___Iterator___mk)



[String.Legacy.Iterator.find]](#manual-String___Legacy___Iterator___find)
  (it : [String.Legacy.Iterator]](#manual-String___Legacy___Iterator___mk))
  (p : [Char]](#manual-Char___mk) → [Bool](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)) :
  [String.Legacy.Iterator]](#manual-String___Legacy___Iterator___mk)
```

Moves the iterator forward until the Boolean predicate `p` returns `[true](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)` for the iterator's current
character or until the end of the string is reached. Does nothing if the current character already
satisfies `p`.

def

```lean
[String.Legacy.Iterator.foldUntil.{u_1}]](#manual-String___Legacy___Iterator___foldUntil) {α : Type u_1}
  (it : [String.Legacy.Iterator]](#manual-String___Legacy___Iterator___mk)) (init : α) (f : α → [Char]](#manual-Char___mk) → [Option](https://lean-lang.org/doc/reference/latest/Basic-Types/Optional-Values/#Option___none) α) :
  α [×](https://lean-lang.org/doc/reference/latest/Basic-Types/Tuples/#Prod___mk) [String.Legacy.Iterator]](#manual-String___Legacy___Iterator___mk)



[String.Legacy.Iterator.foldUntil.{u_1}]](#manual-String___Legacy___Iterator___foldUntil)
  {α : Type u_1}
  (it : [String.Legacy.Iterator]](#manual-String___Legacy___Iterator___mk)) (init : α)
  (f : α → [Char]](#manual-Char___mk) → [Option](https://lean-lang.org/doc/reference/latest/Basic-Types/Optional-Values/#Option___none) α) :
  α [×](https://lean-lang.org/doc/reference/latest/Basic-Types/Tuples/#Prod___mk) [String.Legacy.Iterator]](#manual-String___Legacy___Iterator___mk)
```

Iterates over a string, updating a state at each character using the provided function `f`, until
`f` returns `[none](https://lean-lang.org/doc/reference/latest/Basic-Types/Optional-Values/#Option___none)`. Begins with the state `init`. Returns the state and character for which `f`
returns `[none](https://lean-lang.org/doc/reference/latest/Basic-Types/Optional-Values/#Option___none)`.

def

```lean
[String.Legacy.Iterator.extract]](#manual-String___Legacy___Iterator___extract) :
  [String.Legacy.Iterator]](#manual-String___Legacy___Iterator___mk) → [String.Legacy.Iterator]](#manual-String___Legacy___Iterator___mk) → [String]](#manual-String___ofByteArray)



[String.Legacy.Iterator.extract]](#manual-String___Legacy___Iterator___extract) :
  [String.Legacy.Iterator]](#manual-String___Legacy___Iterator___mk) →
    [String.Legacy.Iterator]](#manual-String___Legacy___Iterator___mk) → [String]](#manual-String___ofByteArray)
```

Extracts the substring between the positions of two iterators. The first iterator's position is the
start of the substring, and the second iterator's position is the end.

Returns the empty string if the iterators are for different strings, or if the position of the first
iterator is past the position of the second iterator.

def

```lean
[String.Legacy.Iterator.remainingToString]](#manual-String___Legacy___Iterator___remainingToString) :
  [String.Legacy.Iterator]](#manual-String___Legacy___Iterator___mk) → [String]](#manual-String___ofByteArray)



[String.Legacy.Iterator.remainingToString]](#manual-String___Legacy___Iterator___remainingToString) :
  [String.Legacy.Iterator]](#manual-String___Legacy___Iterator___mk) → [String]](#manual-String___ofByteArray)
```

The remaining characters in an iterator, as a string.

def

```lean
[String.Legacy.Iterator.remainingBytes]](#manual-String___Legacy___Iterator___remainingBytes) : [String.Legacy.Iterator]](#manual-String___Legacy___Iterator___mk) → [Nat]](#manual-Nat___zero)



[String.Legacy.Iterator.remainingBytes]](#manual-String___Legacy___Iterator___remainingBytes) :
  [String.Legacy.Iterator]](#manual-String___Legacy___Iterator___mk) → [Nat]](#manual-Nat___zero)
```

The number of UTF-8 bytes remaining in the iterator.

def

```lean
[String.Legacy.Iterator.pos]](#manual-String___Legacy___Iterator___pos) (self : [String.Legacy.Iterator]](#manual-String___Legacy___Iterator___mk)) :
  [String.Pos.Raw]](#manual-String___Pos___Raw___mk)



[String.Legacy.Iterator.pos]](#manual-String___Legacy___Iterator___pos)
  (self : [String.Legacy.Iterator]](#manual-String___Legacy___Iterator___mk)) :
  [String.Pos.Raw]](#manual-String___Pos___Raw___mk)
```

The current UTF-8 byte position in the string `s`.

This position is not guaranteed to be valid for the string. If the position is not valid, then the
current character is `([default]](#manual-Inhabited___mk) : [Char]](#manual-Char___mk))`, similar to `String.get` on an invalid position.

def

```lean
[String.Legacy.Iterator.toString]](#manual-String___Legacy___Iterator___toString) (self : [String.Legacy.Iterator]](#manual-String___Legacy___Iterator___mk)) : [String]](#manual-String___ofByteArray)



[String.Legacy.Iterator.toString]](#manual-String___Legacy___Iterator___toString)
  (self : [String.Legacy.Iterator]](#manual-String___Legacy___Iterator___mk)) : [String]](#manual-String___ofByteArray)
```

The string being iterated over.

#### 20.8.4.11. String Slices {#manual-string-api-slice}

structure

```lean
[String.Slice]](#manual-String___Slice___mk) : Type



[String.Slice]](#manual-String___Slice___mk) : Type
```

A region or slice of some underlying string.

A slice consists of a string together with the start and end byte positions of a region of
interest. Actually extracting a substring requires copying and memory allocation, while many
slices of the same underlying string may exist with very little overhead. While this could be
achieved by tracking the bounds by hand, the slice API is much more convenient.

`[String.Slice]](#manual-String___Slice___mk)` bundles proofs to ensure that the start and end positions always delineate a valid
string. For this reason, it should be preferred over `[Substring.Raw]](#manual-Substring___Raw___mk)`.

Constructor

```lean
[String.Slice.mk]](#manual-String___Slice___mk)
```

Fields

```lean
str : [String]](#manual-String___ofByteArray)
```

The underlying strings.

```lean
startInclusive : self.[str]](#manual-String___Slice___mk).[Pos]](#manual-String___Pos___mk)
```

The byte position of the start of the string slice.

```lean
endExclusive : self.[str]](#manual-String___Slice___mk).[Pos]](#manual-String___Pos___mk)
```

The byte position of the end of the string slice.

```lean
startInclusive_le_endExclusive : self.[startInclusive]](#manual-String___Slice___mk) [≤]](#manual-LE___mk) self.[endExclusive]](#manual-String___Slice___mk)
```

The slice is not degenerate (but it may be empty).

def

```lean
[String.toSlice]](#manual-String___toSlice) (s : [String]](#manual-String___ofByteArray)) : [String.Slice]](#manual-String___Slice___mk)



[String.toSlice]](#manual-String___toSlice) (s : [String]](#manual-String___ofByteArray)) : [String.Slice]](#manual-String___Slice___mk)
```

Returns a slice that contains the entire string.

def

```lean
[String.sliceFrom]](#manual-String___sliceFrom) (s : [String]](#manual-String___ofByteArray)) (p : s.[Pos]](#manual-String___Pos___mk)) : [String.Slice]](#manual-String___Slice___mk)



[String.sliceFrom]](#manual-String___sliceFrom) (s : [String]](#manual-String___ofByteArray))
  (p : s.[Pos]](#manual-String___Pos___mk)) : [String.Slice]](#manual-String___Slice___mk)
```

The slice from `p` (inclusive) up to the end of `s`.

def

```lean
[String.sliceTo]](#manual-String___sliceTo) (s : [String]](#manual-String___ofByteArray)) (p : s.[Pos]](#manual-String___Pos___mk)) : [String.Slice]](#manual-String___Slice___mk)



[String.sliceTo]](#manual-String___sliceTo) (s : [String]](#manual-String___ofByteArray)) (p : s.[Pos]](#manual-String___Pos___mk)) :
  [String.Slice]](#manual-String___Slice___mk)
```

The slice from the beginning of `s` up to `p` (exclusive).

structure

```lean
[String.Slice.Pos]](#manual-String___Slice___Pos___mk) (s : [String.Slice]](#manual-String___Slice___mk)) : Type



[String.Slice.Pos]](#manual-String___Slice___Pos___mk) (s : [String.Slice]](#manual-String___Slice___mk)) : Type
```

A `Slice.Pos s` is a byte offset in `s` together with a proof that this position is at a UTF-8
character boundary.

Constructor

```lean
[String.Slice.Pos.mk]](#manual-String___Slice___Pos___mk)
```

Fields

```lean
offset : [String.Pos.Raw]](#manual-String___Pos___Raw___mk)
```

The underlying byte offset of the `Slice.Pos`.

```lean
isValidForSlice : String.Pos.Raw.IsValidForSlice s self.[offset]](#manual-String___Slice___Pos___mk)
```

The proof that `offset` is valid for the string slice `s`.

##### 20.8.4.11.1. API Reference {#manual-The-Lean-Language-Reference--Basic-Types--Strings--API-Reference--String-Slices--API-Reference}

###### 20.8.4.11.1.1. Copying {#manual-The-Lean-Language-Reference--Basic-Types--Strings--API-Reference--String-Slices--API-Reference--Copying}

def

```lean
[String.Slice.copy]](#manual-String___Slice___copy) (s : [String.Slice]](#manual-String___Slice___mk)) : [String]](#manual-String___ofByteArray)



[String.Slice.copy]](#manual-String___Slice___copy) (s : [String.Slice]](#manual-String___Slice___mk)) :
  [String]](#manual-String___ofByteArray)
```

Creates a `[String]](#manual-String___ofByteArray)` from a `[String.Slice]](#manual-String___Slice___mk)` by copying the bytes.

###### 20.8.4.11.1.2. Size {#manual-The-Lean-Language-Reference--Basic-Types--Strings--API-Reference--String-Slices--API-Reference--Size}

def

```lean
[String.Slice.isEmpty]](#manual-String___Slice___isEmpty) (s : [String.Slice]](#manual-String___Slice___mk)) : [Bool](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)



[String.Slice.isEmpty]](#manual-String___Slice___isEmpty) (s : [String.Slice]](#manual-String___Slice___mk)) :
  [Bool](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)
```

Checks whether a slice is empty.

Empty slices have {name}`utf8ByteSize` {lean}`0`.

Examples:

- {lean}`"".[toSlice]](#manual-String___toSlice).[isEmpty]](#manual-String___Slice___isEmpty) = [true](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)`
- {lean}`" ".[toSlice]](#manual-String___toSlice).[isEmpty]](#manual-String___Slice___isEmpty) = [false](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)`

def

```lean
[String.Slice.utf8ByteSize]](#manual-String___Slice___utf8ByteSize) (s : [String.Slice]](#manual-String___Slice___mk)) : [Nat]](#manual-Nat___zero)



[String.Slice.utf8ByteSize]](#manual-String___Slice___utf8ByteSize)
  (s : [String.Slice]](#manual-String___Slice___mk)) : [Nat]](#manual-Nat___zero)
```

The number of bytes of the UTF-8 encoding of the string slice.

###### 20.8.4.11.1.3. Boundaries {#manual-The-Lean-Language-Reference--Basic-Types--Strings--API-Reference--String-Slices--API-Reference--Boundaries}

def

```lean
[String.Slice.pos]](#manual-String___Slice___pos) (s : [String.Slice]](#manual-String___Slice___mk)) (off : [String.Pos.Raw]](#manual-String___Pos___Raw___mk))
  (h : String.Pos.Raw.IsValidForSlice s off) : s.[Pos]](#manual-String___Slice___Pos___mk)



[String.Slice.pos]](#manual-String___Slice___pos) (s : [String.Slice]](#manual-String___Slice___mk))
  (off : [String.Pos.Raw]](#manual-String___Pos___Raw___mk))
  (h :
    String.Pos.Raw.IsValidForSlice s
      off) :
  s.[Pos]](#manual-String___Slice___Pos___mk)
```

Constructs a valid position on `s` from a position and a proof that it is valid.

def

```lean
[String.Slice.pos!]](#manual-String___Slice___pos___) (s : [String.Slice]](#manual-String___Slice___mk)) (off : [String.Pos.Raw]](#manual-String___Pos___Raw___mk)) : s.[Pos]](#manual-String___Slice___Pos___mk)



[String.Slice.pos!]](#manual-String___Slice___pos___) (s : [String.Slice]](#manual-String___Slice___mk))
  (off : [String.Pos.Raw]](#manual-String___Pos___Raw___mk)) : s.[Pos]](#manual-String___Slice___Pos___mk)
```

Constructs a valid position `s` from a position, panicking if the position is not valid.

def

```lean
[String.Slice.pos?]](#manual-String___Slice___pos___-next) (s : [String.Slice]](#manual-String___Slice___mk)) (off : [String.Pos.Raw]](#manual-String___Pos___Raw___mk)) :
  [Option](https://lean-lang.org/doc/reference/latest/Basic-Types/Optional-Values/#Option___none) s.[Pos]](#manual-String___Slice___Pos___mk)



[String.Slice.pos?]](#manual-String___Slice___pos___-next) (s : [String.Slice]](#manual-String___Slice___mk))
  (off : [String.Pos.Raw]](#manual-String___Pos___Raw___mk)) : [Option](https://lean-lang.org/doc/reference/latest/Basic-Types/Optional-Values/#Option___none) s.[Pos]](#manual-String___Slice___Pos___mk)
```

Constructs a valid position on `s` from a position, returning `[none](https://lean-lang.org/doc/reference/latest/Basic-Types/Optional-Values/#Option___none)` if the position is not valid.

def

```lean
[String.Slice.startPos]](#manual-String___Slice___startPos) (s : [String.Slice]](#manual-String___Slice___mk)) : s.[Pos]](#manual-String___Slice___Pos___mk)



[String.Slice.startPos]](#manual-String___Slice___startPos) (s : [String.Slice]](#manual-String___Slice___mk)) :
  s.[Pos]](#manual-String___Slice___Pos___mk)
```

The start position of `s`, as an `s.[Pos]](#manual-String___Slice___Pos___mk)`.

def

```lean
[String.Slice.endPos]](#manual-String___Slice___endPos) (s : [String.Slice]](#manual-String___Slice___mk)) : s.[Pos]](#manual-String___Slice___Pos___mk)



[String.Slice.endPos]](#manual-String___Slice___endPos) (s : [String.Slice]](#manual-String___Slice___mk)) :
  s.[Pos]](#manual-String___Slice___Pos___mk)
```

The past-the-end position of `s`, as an `s.[Pos]](#manual-String___Slice___Pos___mk)`.

def

```lean
[String.Slice.rawEndPos]](#manual-String___Slice___rawEndPos) (s : [String.Slice]](#manual-String___Slice___mk)) : [String.Pos.Raw]](#manual-String___Pos___Raw___mk)



[String.Slice.rawEndPos]](#manual-String___Slice___rawEndPos)
  (s : [String.Slice]](#manual-String___Slice___mk)) : [String.Pos.Raw]](#manual-String___Pos___Raw___mk)
```

The end position of a slice, as a `Pos.Raw`.

###### 20.8.4.11.1.3.1. Adjustment {#manual-The-Lean-Language-Reference--Basic-Types--Strings--API-Reference--String-Slices--API-Reference--Boundaries--Adjustment}

def

```lean
[String.Slice.sliceFrom]](#manual-String___Slice___sliceFrom) (s : [String.Slice]](#manual-String___Slice___mk)) (pos : s.[Pos]](#manual-String___Slice___Pos___mk)) : [String.Slice]](#manual-String___Slice___mk)



[String.Slice.sliceFrom]](#manual-String___Slice___sliceFrom) (s : [String.Slice]](#manual-String___Slice___mk))
  (pos : s.[Pos]](#manual-String___Slice___Pos___mk)) : [String.Slice]](#manual-String___Slice___mk)
```

Given a slice and a valid position within the slice, obtain a new slice on the same underlying
string by replacing the start of the slice with the given position.

def

```lean
[String.Slice.sliceTo]](#manual-String___Slice___sliceTo) (s : [String.Slice]](#manual-String___Slice___mk)) (pos : s.[Pos]](#manual-String___Slice___Pos___mk)) : [String.Slice]](#manual-String___Slice___mk)



[String.Slice.sliceTo]](#manual-String___Slice___sliceTo) (s : [String.Slice]](#manual-String___Slice___mk))
  (pos : s.[Pos]](#manual-String___Slice___Pos___mk)) : [String.Slice]](#manual-String___Slice___mk)
```

Given a slice and a valid position within the slice, obtain a new slice on the same underlying
string by replacing the end of the slice with the given position.

def

```lean
[String.Slice.slice]](#manual-String___Slice___slice) (s : [String.Slice]](#manual-String___Slice___mk)) (newStart newEnd : s.[Pos]](#manual-String___Slice___Pos___mk))
  (h : newStart [≤]](#manual-LE___mk) newEnd) : [String.Slice]](#manual-String___Slice___mk)



[String.Slice.slice]](#manual-String___Slice___slice) (s : [String.Slice]](#manual-String___Slice___mk))
  (newStart newEnd : s.[Pos]](#manual-String___Slice___Pos___mk))
  (h : newStart [≤]](#manual-LE___mk) newEnd) : [String.Slice]](#manual-String___Slice___mk)
```

Given a slice and two valid positions within the slice, obtain a new slice on the same underlying
string formed by the new bounds.

def

```lean
[String.Slice.slice!]](#manual-String___Slice___slice___) (s : [String.Slice]](#manual-String___Slice___mk)) (newStart newEnd : s.[Pos]](#manual-String___Slice___Pos___mk)) :
  [String.Slice]](#manual-String___Slice___mk)



[String.Slice.slice!]](#manual-String___Slice___slice___) (s : [String.Slice]](#manual-String___Slice___mk))
  (newStart newEnd : s.[Pos]](#manual-String___Slice___Pos___mk)) : [String.Slice]](#manual-String___Slice___mk)
```

Given a slice and two valid positions within the slice, obtain a new slice on the same underlying
string formed by the new bounds, or panic if the given end is strictly less than the given start.

def

```lean
[String.Slice.drop]](#manual-String___Slice___drop) (s : [String.Slice]](#manual-String___Slice___mk)) (n : [Nat]](#manual-Nat___zero)) : [String.Slice]](#manual-String___Slice___mk)



[String.Slice.drop]](#manual-String___Slice___drop) (s : [String.Slice]](#manual-String___Slice___mk))
  (n : [Nat]](#manual-Nat___zero)) : [String.Slice]](#manual-String___Slice___mk)
```

Removes the specified number of characters (Unicode code points) from the start of the slice.

If `n` is greater than the amount of characters in `s`, returns an empty slice.

Examples:

- `"red green blue".[toSlice]](#manual-String___toSlice).[drop]](#manual-String___Slice___drop) 4 == "green blue".[toSlice]](#manual-String___toSlice)`
- `"red green blue".[toSlice]](#manual-String___toSlice).[drop]](#manual-String___Slice___drop) 10 == "blue".[toSlice]](#manual-String___toSlice)`
- `"red green blue".[toSlice]](#manual-String___toSlice).[drop]](#manual-String___Slice___drop) 50 == "".[toSlice]](#manual-String___toSlice)`

def

```lean
[String.Slice.dropEnd]](#manual-String___Slice___dropEnd) (s : [String.Slice]](#manual-String___Slice___mk)) (n : [Nat]](#manual-Nat___zero)) : [String.Slice]](#manual-String___Slice___mk)



[String.Slice.dropEnd]](#manual-String___Slice___dropEnd) (s : [String.Slice]](#manual-String___Slice___mk))
  (n : [Nat]](#manual-Nat___zero)) : [String.Slice]](#manual-String___Slice___mk)
```

Removes the specified number of characters (Unicode code points) from the end of the slice.

If `n` is greater than the amount of characters in `s`, returns an empty slice.

Examples:

- `"red green blue".[toSlice]](#manual-String___toSlice).[dropEnd]](#manual-String___Slice___dropEnd) 5 == "red green".[toSlice]](#manual-String___toSlice)`
- `"red green blue".[toSlice]](#manual-String___toSlice).[dropEnd]](#manual-String___Slice___dropEnd) 11 == "red".[toSlice]](#manual-String___toSlice)`
- `"red green blue".[toSlice]](#manual-String___toSlice).[dropEnd]](#manual-String___Slice___dropEnd) 50 == "".[toSlice]](#manual-String___toSlice)`

def

```lean
[String.Slice.dropEndWhile]](#manual-String___Slice___dropEndWhile) {ρ : Type} (s : [String.Slice]](#manual-String___Slice___mk)) (pat : ρ)
  [[String.Slice.Pattern.BackwardPattern]](#manual-String___Slice___Pattern___BackwardPattern___mk) pat] : [String.Slice]](#manual-String___Slice___mk)



[String.Slice.dropEndWhile]](#manual-String___Slice___dropEndWhile) {ρ : Type}
  (s : [String.Slice]](#manual-String___Slice___mk)) (pat : ρ)
  [[String.Slice.Pattern.BackwardPattern]](#manual-String___Slice___Pattern___BackwardPattern___mk)
      pat] :
  [String.Slice]](#manual-String___Slice___mk)
```

Creates a new slice that contains the longest suffix of `s` for which `pat` matched
(potentially repeatedly).

Examples:

- `"red green blue".[toSlice]](#manual-String___toSlice).[dropEndWhile]](#manual-String___Slice___dropEndWhile) [Char.isLower]](#manual-Char___isLower) == "red green ".[toSlice]](#manual-String___toSlice)`
- `"red green blue".[toSlice]](#manual-String___toSlice).[dropEndWhile]](#manual-String___Slice___dropEndWhile) 'e' == "red green blu".[toSlice]](#manual-String___toSlice)`
- `"red green blue".[toSlice]](#manual-String___toSlice).[dropEndWhile]](#manual-String___Slice___dropEndWhile) (fun (_ : [Char]](#manual-Char___mk)) => [true](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)) == "".[toSlice]](#manual-String___toSlice)`

def

```lean
[String.Slice.dropPrefix]](#manual-String___Slice___dropPrefix) {ρ : Type} (s : [String.Slice]](#manual-String___Slice___mk)) (pat : ρ)
  [[String.Slice.Pattern.ForwardPattern]](#manual-String___Slice___Pattern___ForwardPattern___mk) pat] : [String.Slice]](#manual-String___Slice___mk)



[String.Slice.dropPrefix]](#manual-String___Slice___dropPrefix) {ρ : Type}
  (s : [String.Slice]](#manual-String___Slice___mk)) (pat : ρ)
  [[String.Slice.Pattern.ForwardPattern]](#manual-String___Slice___Pattern___ForwardPattern___mk)
      pat] :
  [String.Slice]](#manual-String___Slice___mk)
```

If `pat` matches a prefix of `s`, returns the remainder. Returns `s` unmodified
otherwise.

Use `[String.Slice.dropPrefix?]](#manual-String___Slice___dropPrefix___)` to return `[none](https://lean-lang.org/doc/reference/latest/Basic-Types/Optional-Values/#Option___none)` when `pat` does not match a prefix.

This function is generic over all currently supported patterns.

Examples:

- `"red green blue".[toSlice]](#manual-String___toSlice).[dropPrefix]](#manual-String___Slice___dropPrefix) "red " == "green blue".[toSlice]](#manual-String___toSlice)`
- `"red green blue".[toSlice]](#manual-String___toSlice).[dropPrefix]](#manual-String___Slice___dropPrefix) "reed " == "red green blue".[toSlice]](#manual-String___toSlice)`
- `"red green blue".[toSlice]](#manual-String___toSlice).[dropPrefix]](#manual-String___Slice___dropPrefix) 'r' == "ed green blue".[toSlice]](#manual-String___toSlice)`
- `"red green blue".[toSlice]](#manual-String___toSlice).[dropPrefix]](#manual-String___Slice___dropPrefix) [Char.isLower]](#manual-Char___isLower) == "ed green blue".[toSlice]](#manual-String___toSlice)`

def

```lean
[String.Slice.dropPrefix?]](#manual-String___Slice___dropPrefix___) {ρ : Type} (s : [String.Slice]](#manual-String___Slice___mk)) (pat : ρ)
  [[String.Slice.Pattern.ForwardPattern]](#manual-String___Slice___Pattern___ForwardPattern___mk) pat] : [Option](https://lean-lang.org/doc/reference/latest/Basic-Types/Optional-Values/#Option___none) [String.Slice]](#manual-String___Slice___mk)



[String.Slice.dropPrefix?]](#manual-String___Slice___dropPrefix___) {ρ : Type}
  (s : [String.Slice]](#manual-String___Slice___mk)) (pat : ρ)
  [[String.Slice.Pattern.ForwardPattern]](#manual-String___Slice___Pattern___ForwardPattern___mk)
      pat] :
  [Option](https://lean-lang.org/doc/reference/latest/Basic-Types/Optional-Values/#Option___none) [String.Slice]](#manual-String___Slice___mk)
```

If `pat` matches a prefix of `s`, returns the remainder. Returns `[none](https://lean-lang.org/doc/reference/latest/Basic-Types/Optional-Values/#Option___none)` otherwise.

Use `[String.Slice.dropPrefix]](#manual-String___Slice___dropPrefix)` to return the slice
unchanged when `pat` does not match a prefix.

This function is generic over all currently supported patterns.

Examples:

- `"red green blue".[toSlice]](#manual-String___toSlice).[dropPrefix?]](#manual-String___Slice___dropPrefix___) "red " == [some](https://lean-lang.org/doc/reference/latest/Basic-Types/Optional-Values/#Option___none) "green blue".[toSlice]](#manual-String___toSlice)`
- `"red green blue".[toSlice]](#manual-String___toSlice).[dropPrefix?]](#manual-String___Slice___dropPrefix___) "reed " == [none](https://lean-lang.org/doc/reference/latest/Basic-Types/Optional-Values/#Option___none)`
- `"red green blue".[toSlice]](#manual-String___toSlice).[dropPrefix?]](#manual-String___Slice___dropPrefix___) 'r' == [some](https://lean-lang.org/doc/reference/latest/Basic-Types/Optional-Values/#Option___none) "ed green blue".[toSlice]](#manual-String___toSlice)`
- `"red green blue".[toSlice]](#manual-String___toSlice).[dropPrefix?]](#manual-String___Slice___dropPrefix___) [Char.isLower]](#manual-Char___isLower) == [some](https://lean-lang.org/doc/reference/latest/Basic-Types/Optional-Values/#Option___none) "ed green blue".[toSlice]](#manual-String___toSlice)`

def

```lean
[String.Slice.dropSuffix]](#manual-String___Slice___dropSuffix) {ρ : Type} (s : [String.Slice]](#manual-String___Slice___mk)) (pat : ρ)
  [[String.Slice.Pattern.BackwardPattern]](#manual-String___Slice___Pattern___BackwardPattern___mk) pat] : [String.Slice]](#manual-String___Slice___mk)



[String.Slice.dropSuffix]](#manual-String___Slice___dropSuffix) {ρ : Type}
  (s : [String.Slice]](#manual-String___Slice___mk)) (pat : ρ)
  [[String.Slice.Pattern.BackwardPattern]](#manual-String___Slice___Pattern___BackwardPattern___mk)
      pat] :
  [String.Slice]](#manual-String___Slice___mk)
```

If `pat` matches a suffix of `s`, returns the remainder. Returns `s` unmodified
otherwise.

Use `[String.Slice.dropSuffix?]](#manual-String___Slice___dropSuffix___)` to return `[none](https://lean-lang.org/doc/reference/latest/Basic-Types/Optional-Values/#Option___none)` when `pat` does not match a
prefix.

This function is generic over all currently supported patterns.

Examples:

- `"red green blue".[toSlice]](#manual-String___toSlice).[dropSuffix]](#manual-String___Slice___dropSuffix) " blue" == "red green".[toSlice]](#manual-String___toSlice)`
- `"red green blue".[toSlice]](#manual-String___toSlice).[dropSuffix]](#manual-String___Slice___dropSuffix) "bluu " == "red green blue".[toSlice]](#manual-String___toSlice)`
- `"red green blue".[toSlice]](#manual-String___toSlice).[dropSuffix]](#manual-String___Slice___dropSuffix) 'e' == "red green blu".[toSlice]](#manual-String___toSlice)`
- `"red green blue".[toSlice]](#manual-String___toSlice).[dropSuffix]](#manual-String___Slice___dropSuffix) [Char.isLower]](#manual-Char___isLower) == "red green blu".[toSlice]](#manual-String___toSlice)`

def

```lean
[String.Slice.dropSuffix?]](#manual-String___Slice___dropSuffix___) {ρ : Type} (s : [String.Slice]](#manual-String___Slice___mk)) (pat : ρ)
  [[String.Slice.Pattern.BackwardPattern]](#manual-String___Slice___Pattern___BackwardPattern___mk) pat] : [Option](https://lean-lang.org/doc/reference/latest/Basic-Types/Optional-Values/#Option___none) [String.Slice]](#manual-String___Slice___mk)



[String.Slice.dropSuffix?]](#manual-String___Slice___dropSuffix___) {ρ : Type}
  (s : [String.Slice]](#manual-String___Slice___mk)) (pat : ρ)
  [[String.Slice.Pattern.BackwardPattern]](#manual-String___Slice___Pattern___BackwardPattern___mk)
      pat] :
  [Option](https://lean-lang.org/doc/reference/latest/Basic-Types/Optional-Values/#Option___none) [String.Slice]](#manual-String___Slice___mk)
```

If `pat` matches a suffix of `s`, returns the remainder. Returns `[none](https://lean-lang.org/doc/reference/latest/Basic-Types/Optional-Values/#Option___none)` otherwise.

Use `[String.Slice.dropSuffix]](#manual-String___Slice___dropSuffix)` to return the slice
unchanged when `pat` does not match a suffix.

This function is generic over all currently supported patterns.

Examples:

- `"red green blue".[toSlice]](#manual-String___toSlice).[dropSuffix?]](#manual-String___Slice___dropSuffix___) " blue" == [some](https://lean-lang.org/doc/reference/latest/Basic-Types/Optional-Values/#Option___none) "red green".[toSlice]](#manual-String___toSlice)`
- `"red green blue".[toSlice]](#manual-String___toSlice).[dropSuffix?]](#manual-String___Slice___dropSuffix___) "bluu " == [none](https://lean-lang.org/doc/reference/latest/Basic-Types/Optional-Values/#Option___none)`
- `"red green blue".[toSlice]](#manual-String___toSlice).[dropSuffix?]](#manual-String___Slice___dropSuffix___) 'e' == [some](https://lean-lang.org/doc/reference/latest/Basic-Types/Optional-Values/#Option___none) "red green blu".[toSlice]](#manual-String___toSlice)`
- `"red green blue".[toSlice]](#manual-String___toSlice).[dropSuffix?]](#manual-String___Slice___dropSuffix___) [Char.isLower]](#manual-Char___isLower) == [some](https://lean-lang.org/doc/reference/latest/Basic-Types/Optional-Values/#Option___none) "red green blu".[toSlice]](#manual-String___toSlice)`

def

```lean
[String.Slice.dropWhile]](#manual-String___Slice___dropWhile) {ρ : Type} (s : [String.Slice]](#manual-String___Slice___mk)) (pat : ρ)
  [[String.Slice.Pattern.ForwardPattern]](#manual-String___Slice___Pattern___ForwardPattern___mk) pat] : [String.Slice]](#manual-String___Slice___mk)



[String.Slice.dropWhile]](#manual-String___Slice___dropWhile) {ρ : Type}
  (s : [String.Slice]](#manual-String___Slice___mk)) (pat : ρ)
  [[String.Slice.Pattern.ForwardPattern]](#manual-String___Slice___Pattern___ForwardPattern___mk)
      pat] :
  [String.Slice]](#manual-String___Slice___mk)
```

Creates a new slice that contains the longest prefix of `s` for which `pat` matched
(potentially repeatedly).

Examples:

- `"red green blue".[toSlice]](#manual-String___toSlice).[dropWhile]](#manual-String___Slice___dropWhile) [Char.isLower]](#manual-Char___isLower) == " green blue".[toSlice]](#manual-String___toSlice)`
- `"red green blue".[toSlice]](#manual-String___toSlice).[dropWhile]](#manual-String___Slice___dropWhile) 'r' == "ed green blue".[toSlice]](#manual-String___toSlice)`
- `"red red green blue".[toSlice]](#manual-String___toSlice).[dropWhile]](#manual-String___Slice___dropWhile) "red " == "green blue".[toSlice]](#manual-String___toSlice)`
- `"red green blue".[toSlice]](#manual-String___toSlice).[dropWhile]](#manual-String___Slice___dropWhile) (fun (_ : [Char]](#manual-Char___mk)) => [true](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)) == "".[toSlice]](#manual-String___toSlice)`

def

```lean
[String.Slice.take]](#manual-String___Slice___take) (s : [String.Slice]](#manual-String___Slice___mk)) (n : [Nat]](#manual-Nat___zero)) : [String.Slice]](#manual-String___Slice___mk)



[String.Slice.take]](#manual-String___Slice___take) (s : [String.Slice]](#manual-String___Slice___mk))
  (n : [Nat]](#manual-Nat___zero)) : [String.Slice]](#manual-String___Slice___mk)
```

Creates a new slice that contains the first `n` characters (Unicode code points) of `s`.

If `n` is greater than the amount of characters in `s`, returns `s`.

Examples:

- `"red green blue".[toSlice]](#manual-String___toSlice).[take]](#manual-String___Slice___take) 3 == "red".[toSlice]](#manual-String___toSlice)`
- `"red green blue".[toSlice]](#manual-String___toSlice).[take]](#manual-String___Slice___take) 1 == "r".[toSlice]](#manual-String___toSlice)`
- `"red green blue".[toSlice]](#manual-String___toSlice).[take]](#manual-String___Slice___take) 0 == "".[toSlice]](#manual-String___toSlice)`
- `"red green blue".[toSlice]](#manual-String___toSlice).[take]](#manual-String___Slice___take) 100 == "red green blue".[toSlice]](#manual-String___toSlice)`

def

```lean
[String.Slice.takeEnd]](#manual-String___Slice___takeEnd) (s : [String.Slice]](#manual-String___Slice___mk)) (n : [Nat]](#manual-Nat___zero)) : [String.Slice]](#manual-String___Slice___mk)



[String.Slice.takeEnd]](#manual-String___Slice___takeEnd) (s : [String.Slice]](#manual-String___Slice___mk))
  (n : [Nat]](#manual-Nat___zero)) : [String.Slice]](#manual-String___Slice___mk)
```

Creates a new slice that contains the last `n` characters (Unicode code points) of `s`.

If `n` is greater than the amount of characters in `s`, returns `s`.

Examples:

- `"red green blue".[toSlice]](#manual-String___toSlice).[takeEnd]](#manual-String___Slice___takeEnd) 4 == "blue".[toSlice]](#manual-String___toSlice)`
- `"red green blue".[toSlice]](#manual-String___toSlice).[takeEnd]](#manual-String___Slice___takeEnd) 1 == "e".[toSlice]](#manual-String___toSlice)`
- `"red green blue".[toSlice]](#manual-String___toSlice).[takeEnd]](#manual-String___Slice___takeEnd) 0 == "".[toSlice]](#manual-String___toSlice)`
- `"red green blue".[toSlice]](#manual-String___toSlice).[takeEnd]](#manual-String___Slice___takeEnd) 100 == "red green blue".[toSlice]](#manual-String___toSlice)`

def

```lean
[String.Slice.takeEndWhile]](#manual-String___Slice___takeEndWhile) {ρ : Type} (s : [String.Slice]](#manual-String___Slice___mk)) (pat : ρ)
  [[String.Slice.Pattern.BackwardPattern]](#manual-String___Slice___Pattern___BackwardPattern___mk) pat] : [String.Slice]](#manual-String___Slice___mk)



[String.Slice.takeEndWhile]](#manual-String___Slice___takeEndWhile) {ρ : Type}
  (s : [String.Slice]](#manual-String___Slice___mk)) (pat : ρ)
  [[String.Slice.Pattern.BackwardPattern]](#manual-String___Slice___Pattern___BackwardPattern___mk)
      pat] :
  [String.Slice]](#manual-String___Slice___mk)
```

Creates a new slice that contains the suffix prefix of `s` for which `pat` matched
(potentially repeatedly).

This function is generic over all currently supported patterns.

Examples:

- `"red green blue".[toSlice]](#manual-String___toSlice).[takeEndWhile]](#manual-String___Slice___takeEndWhile) [Char.isLower]](#manual-Char___isLower) == "blue".[toSlice]](#manual-String___toSlice)`
- `"red green blue".[toSlice]](#manual-String___toSlice).[takeEndWhile]](#manual-String___Slice___takeEndWhile) 'e' == "e".[toSlice]](#manual-String___toSlice)`
- `"red green blue".[toSlice]](#manual-String___toSlice).[takeEndWhile]](#manual-String___Slice___takeEndWhile) (fun (_ : [Char]](#manual-Char___mk)) => [true](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)) == "red green blue".[toSlice]](#manual-String___toSlice)`

def

```lean
[String.Slice.takeWhile]](#manual-String___Slice___takeWhile) {ρ : Type} (s : [String.Slice]](#manual-String___Slice___mk)) (pat : ρ)
  [[String.Slice.Pattern.ForwardPattern]](#manual-String___Slice___Pattern___ForwardPattern___mk) pat] : [String.Slice]](#manual-String___Slice___mk)



[String.Slice.takeWhile]](#manual-String___Slice___takeWhile) {ρ : Type}
  (s : [String.Slice]](#manual-String___Slice___mk)) (pat : ρ)
  [[String.Slice.Pattern.ForwardPattern]](#manual-String___Slice___Pattern___ForwardPattern___mk)
      pat] :
  [String.Slice]](#manual-String___Slice___mk)
```

Creates a new slice that contains the longest prefix of `s` for which `pat` matched
(potentially repeatedly).

This function is generic over all currently supported patterns.

Examples:

- `"red green blue".[toSlice]](#manual-String___toSlice).[takeWhile]](#manual-String___Slice___takeWhile) [Char.isLower]](#manual-Char___isLower) == "red".[toSlice]](#manual-String___toSlice)`
- `"red green blue".[toSlice]](#manual-String___toSlice).[takeWhile]](#manual-String___Slice___takeWhile) 'r' == "r".[toSlice]](#manual-String___toSlice)`
- `"red red green blue".[toSlice]](#manual-String___toSlice).[takeWhile]](#manual-String___Slice___takeWhile) "red " == "red red ".[toSlice]](#manual-String___toSlice)`
- `"red green blue".[toSlice]](#manual-String___toSlice).[takeWhile]](#manual-String___Slice___takeWhile) (fun (_ : [Char]](#manual-Char___mk)) => [true](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)) == "red green blue".[toSlice]](#manual-String___toSlice)`

###### 20.8.4.11.1.4. Characters {#manual-The-Lean-Language-Reference--Basic-Types--Strings--API-Reference--String-Slices--API-Reference--Characters}

def

```lean
[String.Slice.front]](#manual-String___Slice___front) (s : [String.Slice]](#manual-String___Slice___mk)) : [Char]](#manual-Char___mk)



[String.Slice.front]](#manual-String___Slice___front) (s : [String.Slice]](#manual-String___Slice___mk)) :
  [Char]](#manual-Char___mk)
```

Returns the first character in `s`. If `s` is empty, returns `([default]](#manual-Inhabited___mk) : [Char]](#manual-Char___mk))`.

Examples:

- `"abc".[toSlice]](#manual-String___toSlice).[front]](#manual-String___Slice___front) = 'a'`
- `"".[toSlice]](#manual-String___toSlice).[front]](#manual-String___Slice___front) = ([default]](#manual-Inhabited___mk) : [Char]](#manual-Char___mk))`

def

```lean
[String.Slice.front?]](#manual-String___Slice___front___) (s : [String.Slice]](#manual-String___Slice___mk)) : [Option](https://lean-lang.org/doc/reference/latest/Basic-Types/Optional-Values/#Option___none) [Char]](#manual-Char___mk)



[String.Slice.front?]](#manual-String___Slice___front___) (s : [String.Slice]](#manual-String___Slice___mk)) :
  [Option](https://lean-lang.org/doc/reference/latest/Basic-Types/Optional-Values/#Option___none) [Char]](#manual-Char___mk)
```

Returns the first character in `s`. If `s` is empty, returns `[none](https://lean-lang.org/doc/reference/latest/Basic-Types/Optional-Values/#Option___none)`.

Examples:

- `"abc".[toSlice]](#manual-String___toSlice).[front?]](#manual-String___Slice___front___) = [some](https://lean-lang.org/doc/reference/latest/Basic-Types/Optional-Values/#Option___none) 'a'`
- `"".[toSlice]](#manual-String___toSlice).[front?]](#manual-String___Slice___front___) = [none](https://lean-lang.org/doc/reference/latest/Basic-Types/Optional-Values/#Option___none)`

def

```lean
[String.Slice.back]](#manual-String___Slice___back) (s : [String.Slice]](#manual-String___Slice___mk)) : [Char]](#manual-Char___mk)



[String.Slice.back]](#manual-String___Slice___back) (s : [String.Slice]](#manual-String___Slice___mk)) :
  [Char]](#manual-Char___mk)
```

Returns the last character in `s`. If `s` is empty, returns `([default]](#manual-Inhabited___mk) : [Char]](#manual-Char___mk))`.

Examples:

- `"abc".[toSlice]](#manual-String___toSlice).[back]](#manual-String___Slice___back) = 'c'`
- `"".[toSlice]](#manual-String___toSlice).[back]](#manual-String___Slice___back) = ([default]](#manual-Inhabited___mk) : [Char]](#manual-Char___mk))`

def

```lean
[String.Slice.back?]](#manual-String___Slice___back___) (s : [String.Slice]](#manual-String___Slice___mk)) : [Option](https://lean-lang.org/doc/reference/latest/Basic-Types/Optional-Values/#Option___none) [Char]](#manual-Char___mk)



[String.Slice.back?]](#manual-String___Slice___back___) (s : [String.Slice]](#manual-String___Slice___mk)) :
  [Option](https://lean-lang.org/doc/reference/latest/Basic-Types/Optional-Values/#Option___none) [Char]](#manual-Char___mk)
```

Returns the last character in `s`. If `s` is empty, returns `[none](https://lean-lang.org/doc/reference/latest/Basic-Types/Optional-Values/#Option___none)`.

Examples:

- `"abc".[toSlice]](#manual-String___toSlice).[back?]](#manual-String___Slice___back___) = [some](https://lean-lang.org/doc/reference/latest/Basic-Types/Optional-Values/#Option___none) 'c'`
- `"".[toSlice]](#manual-String___toSlice).[back?]](#manual-String___Slice___back___) = [none](https://lean-lang.org/doc/reference/latest/Basic-Types/Optional-Values/#Option___none)`

###### 20.8.4.11.1.5. Bytes {#manual-The-Lean-Language-Reference--Basic-Types--Strings--API-Reference--String-Slices--API-Reference--Bytes}

def

```lean
[String.Slice.getUTF8Byte]](#manual-String___Slice___getUTF8Byte) (s : [String.Slice]](#manual-String___Slice___mk)) (p : [String.Pos.Raw]](#manual-String___Pos___Raw___mk))
  (h : p [<]](#manual-LT___mk) s.[rawEndPos]](#manual-String___Slice___rawEndPos)) : [UInt8]](#manual-UInt8___ofBitVec)



[String.Slice.getUTF8Byte]](#manual-String___Slice___getUTF8Byte)
  (s : [String.Slice]](#manual-String___Slice___mk)) (p : [String.Pos.Raw]](#manual-String___Pos___Raw___mk))
  (h : p [<]](#manual-LT___mk) s.[rawEndPos]](#manual-String___Slice___rawEndPos)) : [UInt8]](#manual-UInt8___ofBitVec)
```

Accesses the indicated byte in the UTF-8 encoding of a string slice.

At runtime, this function is implemented by efficient, constant-time code.

def

```lean
[String.Slice.getUTF8Byte!]](#manual-String___Slice___getUTF8Byte___) (s : [String.Slice]](#manual-String___Slice___mk)) (p : [String.Pos.Raw]](#manual-String___Pos___Raw___mk)) :
  [UInt8]](#manual-UInt8___ofBitVec)



[String.Slice.getUTF8Byte!]](#manual-String___Slice___getUTF8Byte___)
  (s : [String.Slice]](#manual-String___Slice___mk))
  (p : [String.Pos.Raw]](#manual-String___Pos___Raw___mk)) : [UInt8]](#manual-UInt8___ofBitVec)
```

Accesses the indicated byte in the UTF-8 encoding of the string slice, or panics if the position
is out-of-bounds.

###### 20.8.4.11.1.6. Positions {#manual-The-Lean-Language-Reference--Basic-Types--Strings--API-Reference--String-Slices--API-Reference--Positions}

def

```lean
[String.Slice.posGE]](#manual-String___Slice___posGE) (s : [String.Slice]](#manual-String___Slice___mk)) (offset : [String.Pos.Raw]](#manual-String___Pos___Raw___mk))
  (h : offset [≤]](#manual-LE___mk) s.[rawEndPos]](#manual-String___Slice___rawEndPos)) : s.[Pos]](#manual-String___Slice___Pos___mk)



[String.Slice.posGE]](#manual-String___Slice___posGE) (s : [String.Slice]](#manual-String___Slice___mk))
  (offset : [String.Pos.Raw]](#manual-String___Pos___Raw___mk))
  (h : offset [≤]](#manual-LE___mk) s.[rawEndPos]](#manual-String___Slice___rawEndPos)) : s.[Pos]](#manual-String___Slice___Pos___mk)
```

Obtains the smallest valid position that is greater than or equal to the given byte position.

def

```lean
[String.Slice.posGT]](#manual-String___Slice___posGT) (s : [String.Slice]](#manual-String___Slice___mk)) (offset : [String.Pos.Raw]](#manual-String___Pos___Raw___mk))
  (h : offset [<]](#manual-LT___mk) s.[rawEndPos]](#manual-String___Slice___rawEndPos)) : s.[Pos]](#manual-String___Slice___Pos___mk)



[String.Slice.posGT]](#manual-String___Slice___posGT) (s : [String.Slice]](#manual-String___Slice___mk))
  (offset : [String.Pos.Raw]](#manual-String___Pos___Raw___mk))
  (h : offset [<]](#manual-LT___mk) s.[rawEndPos]](#manual-String___Slice___rawEndPos)) : s.[Pos]](#manual-String___Slice___Pos___mk)
```

Obtains the smallest valid position that is strictly greater than the given byte position.

###### 20.8.4.11.1.7. Searching {#manual-The-Lean-Language-Reference--Basic-Types--Strings--API-Reference--String-Slices--API-Reference--Searching}

def

```lean
[String.Slice.contains]](#manual-String___Slice___contains) {ρ : Type} {σ : [String.Slice]](#manual-String___Slice___mk) → Type}
  [(s : [String.Slice]](#manual-String___Slice___mk)) →
      [Std.Iterator](https://lean-lang.org/doc/reference/latest/Iterators/Iterator-Definitions/#Std___Iterator___mk) (σ s) [Id]](#manual-Id) (String.Slice.Pattern.SearchStep s)]
  [(s : [String.Slice]](#manual-String___Slice___mk)) → [Std.IteratorLoop](https://lean-lang.org/doc/reference/latest/Iterators/Iterator-Definitions/#Std___IteratorLoop___mk) (σ s) [Id]](#manual-Id) [Id]](#manual-Id)] (s : [String.Slice]](#manual-String___Slice___mk))
  (pat : ρ) [[String.Slice.Pattern.ToForwardSearcher]](#manual-String___Slice___Pattern___ToForwardSearcher___mk) pat σ] : [Bool](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)



[String.Slice.contains]](#manual-String___Slice___contains) {ρ : Type}
  {σ : [String.Slice]](#manual-String___Slice___mk) → Type}
  [(s : [String.Slice]](#manual-String___Slice___mk)) →
      [Std.Iterator](https://lean-lang.org/doc/reference/latest/Iterators/Iterator-Definitions/#Std___Iterator___mk) (σ s) [Id]](#manual-Id)
        (String.Slice.Pattern.SearchStep
          s)]
  [(s : [String.Slice]](#manual-String___Slice___mk)) →
      [Std.IteratorLoop](https://lean-lang.org/doc/reference/latest/Iterators/Iterator-Definitions/#Std___IteratorLoop___mk) (σ s) [Id]](#manual-Id) [Id]](#manual-Id)]
  (s : [String.Slice]](#manual-String___Slice___mk)) (pat : ρ)
  [[String.Slice.Pattern.ToForwardSearcher]](#manual-String___Slice___Pattern___ToForwardSearcher___mk)
      pat σ] :
  [Bool](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)
```

Checks whether a slice has a match of the pattern `pat` anywhere.

This function is generic over all currently supported patterns.

Examples:

- `"coffee tea water".[toSlice]](#manual-String___toSlice).[contains]](#manual-String___Slice___contains) [Char.isWhitespace]](#manual-Char___isWhitespace) = [true](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)`
- `"tea".[toSlice]](#manual-String___toSlice).[contains]](#manual-String___Slice___contains) (fun (c : [Char]](#manual-Char___mk)) => c == 'X') = [false](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)`
- `"coffee tea water".[toSlice]](#manual-String___toSlice).[contains]](#manual-String___Slice___contains) "tea" = [true](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)`

def

```lean
[String.Slice.startsWith]](#manual-String___Slice___startsWith) {ρ : Type} (s : [String.Slice]](#manual-String___Slice___mk)) (pat : ρ)
  [[String.Slice.Pattern.ForwardPattern]](#manual-String___Slice___Pattern___ForwardPattern___mk) pat] : [Bool](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)



[String.Slice.startsWith]](#manual-String___Slice___startsWith) {ρ : Type}
  (s : [String.Slice]](#manual-String___Slice___mk)) (pat : ρ)
  [[String.Slice.Pattern.ForwardPattern]](#manual-String___Slice___Pattern___ForwardPattern___mk)
      pat] :
  [Bool](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)
```

Checks whether the slice (`s`) begins with the pattern (`pat`).

This function is generic over all currently supported patterns.

Examples:

- `"red green blue".[toSlice]](#manual-String___toSlice).[startsWith]](#manual-String___Slice___startsWith) "red" = [true](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)`
- `"red green blue".[toSlice]](#manual-String___toSlice).[startsWith]](#manual-String___Slice___startsWith) "green" = [false](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)`
- `"red green blue".[toSlice]](#manual-String___toSlice).[startsWith]](#manual-String___Slice___startsWith) "" = [true](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)`
- `"red green blue".[toSlice]](#manual-String___toSlice).[startsWith]](#manual-String___Slice___startsWith) 'r' = [true](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)`
- `"red green blue".[toSlice]](#manual-String___toSlice).[startsWith]](#manual-String___Slice___startsWith) [Char.isLower]](#manual-Char___isLower) = [true](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)`

def

```lean
[String.Slice.endsWith]](#manual-String___Slice___endsWith) {ρ : Type} (s : [String.Slice]](#manual-String___Slice___mk)) (pat : ρ)
  [[String.Slice.Pattern.BackwardPattern]](#manual-String___Slice___Pattern___BackwardPattern___mk) pat] : [Bool](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)



[String.Slice.endsWith]](#manual-String___Slice___endsWith) {ρ : Type}
  (s : [String.Slice]](#manual-String___Slice___mk)) (pat : ρ)
  [[String.Slice.Pattern.BackwardPattern]](#manual-String___Slice___Pattern___BackwardPattern___mk)
      pat] :
  [Bool](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)
```

Checks whether the slice (`s`) ends with the pattern (`pat`).

This function is generic over all currently supported patterns.

Examples:

- `"red green blue".[toSlice]](#manual-String___toSlice).[endsWith]](#manual-String___Slice___endsWith) "blue" = [true](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)`
- `"red green blue".[toSlice]](#manual-String___toSlice).[endsWith]](#manual-String___Slice___endsWith) "green" = [false](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)`
- `"red green blue".[toSlice]](#manual-String___toSlice).[endsWith]](#manual-String___Slice___endsWith) "" = [true](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)`
- `"red green blue".[toSlice]](#manual-String___toSlice).[endsWith]](#manual-String___Slice___endsWith) 'e' = [true](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)`
- `"red green blue".[toSlice]](#manual-String___toSlice).[endsWith]](#manual-String___Slice___endsWith) [Char.isLower]](#manual-Char___isLower) = [true](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)`

def

```lean
[String.Slice.all]](#manual-String___Slice___all) {ρ : Type} (s : [String.Slice]](#manual-String___Slice___mk)) (pat : ρ)
  [[String.Slice.Pattern.ForwardPattern]](#manual-String___Slice___Pattern___ForwardPattern___mk) pat] : [Bool](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)



[String.Slice.all]](#manual-String___Slice___all) {ρ : Type}
  (s : [String.Slice]](#manual-String___Slice___mk)) (pat : ρ)
  [[String.Slice.Pattern.ForwardPattern]](#manual-String___Slice___Pattern___ForwardPattern___mk)
      pat] :
  [Bool](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)
```

Checks whether a slice only consists of matches of the pattern `pat`.

Short-circuits at the first pattern mis-match.

This function is generic over all currently supported patterns.

Examples:

- `"brown".[toSlice]](#manual-String___toSlice).[all]](#manual-String___Slice___all) [Char.isLower]](#manual-Char___isLower) = [true](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)`
- `"brown and orange".[toSlice]](#manual-String___toSlice).[all]](#manual-String___Slice___all) [Char.isLower]](#manual-Char___isLower) = [false](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)`
- `"aaaaaa".[toSlice]](#manual-String___toSlice).[all]](#manual-String___Slice___all) 'a' = [true](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)`
- `"aaaaaa".[toSlice]](#manual-String___toSlice).[all]](#manual-String___Slice___all) "aa" = [true](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)`
- `"aaaaaaa".[toSlice]](#manual-String___toSlice).[all]](#manual-String___Slice___all) "aa" = [false](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)`

def

```lean
[String.Slice.find?]](#manual-String___Slice___find___) {ρ : Type} {σ : [String.Slice]](#manual-String___Slice___mk) → Type}
  [(s : [String.Slice]](#manual-String___Slice___mk)) →
      [Std.Iterator](https://lean-lang.org/doc/reference/latest/Iterators/Iterator-Definitions/#Std___Iterator___mk) (σ s) [Id]](#manual-Id) (String.Slice.Pattern.SearchStep s)]
  [(s : [String.Slice]](#manual-String___Slice___mk)) → [Std.IteratorLoop](https://lean-lang.org/doc/reference/latest/Iterators/Iterator-Definitions/#Std___IteratorLoop___mk) (σ s) [Id]](#manual-Id) [Id]](#manual-Id)] (s : [String.Slice]](#manual-String___Slice___mk))
  (pat : ρ) [[String.Slice.Pattern.ToForwardSearcher]](#manual-String___Slice___Pattern___ToForwardSearcher___mk) pat σ] :
  [Option](https://lean-lang.org/doc/reference/latest/Basic-Types/Optional-Values/#Option___none) s.[Pos]](#manual-String___Slice___Pos___mk)



[String.Slice.find?]](#manual-String___Slice___find___) {ρ : Type}
  {σ : [String.Slice]](#manual-String___Slice___mk) → Type}
  [(s : [String.Slice]](#manual-String___Slice___mk)) →
      [Std.Iterator](https://lean-lang.org/doc/reference/latest/Iterators/Iterator-Definitions/#Std___Iterator___mk) (σ s) [Id]](#manual-Id)
        (String.Slice.Pattern.SearchStep
          s)]
  [(s : [String.Slice]](#manual-String___Slice___mk)) →
      [Std.IteratorLoop](https://lean-lang.org/doc/reference/latest/Iterators/Iterator-Definitions/#Std___IteratorLoop___mk) (σ s) [Id]](#manual-Id) [Id]](#manual-Id)]
  (s : [String.Slice]](#manual-String___Slice___mk)) (pat : ρ)
  [[String.Slice.Pattern.ToForwardSearcher]](#manual-String___Slice___Pattern___ToForwardSearcher___mk)
      pat σ] :
  [Option](https://lean-lang.org/doc/reference/latest/Basic-Types/Optional-Values/#Option___none) s.[Pos]](#manual-String___Slice___Pos___mk)
```

Finds the position of the first match of the pattern `pat` in a slice `s`. If there
is no match `[none](https://lean-lang.org/doc/reference/latest/Basic-Types/Optional-Values/#Option___none)` is returned.

This function is generic over all currently supported patterns.

Examples:

- `("coffee tea water".[toSlice]](#manual-String___toSlice).[find?]](#manual-String___Slice___find___) [Char.isWhitespace]](#manual-Char___isWhitespace)).[map](https://lean-lang.org/doc/reference/latest/Basic-Types/Optional-Values/#Option___map) (·.[get!]](#manual-String___Slice___Pos___get___)) == [some](https://lean-lang.org/doc/reference/latest/Basic-Types/Optional-Values/#Option___none) ' '`
- `"tea".[toSlice]](#manual-String___toSlice).[find?]](#manual-String___Slice___find___) (fun (c : [Char]](#manual-Char___mk)) => c == 'X') == [none](https://lean-lang.org/doc/reference/latest/Basic-Types/Optional-Values/#Option___none)`
- `("coffee tea water".[toSlice]](#manual-String___toSlice).[find?]](#manual-String___Slice___find___) "tea").[map](https://lean-lang.org/doc/reference/latest/Basic-Types/Optional-Values/#Option___map) (·.[get!]](#manual-String___Slice___Pos___get___)) == [some](https://lean-lang.org/doc/reference/latest/Basic-Types/Optional-Values/#Option___none) 't'`

def

```lean
[String.Slice.revFind?]](#manual-String___Slice___revFind___) {σ : [String.Slice]](#manual-String___Slice___mk) → Type}
  [(s : [String.Slice]](#manual-String___Slice___mk)) →
      [Std.Iterator](https://lean-lang.org/doc/reference/latest/Iterators/Iterator-Definitions/#Std___Iterator___mk) (σ s) [Id]](#manual-Id) (String.Slice.Pattern.SearchStep s)]
  [(s : [String.Slice]](#manual-String___Slice___mk)) → [Std.IteratorLoop](https://lean-lang.org/doc/reference/latest/Iterators/Iterator-Definitions/#Std___IteratorLoop___mk) (σ s) [Id]](#manual-Id) [Id]](#manual-Id)] {ρ : Type}
  (s : [String.Slice]](#manual-String___Slice___mk)) (pat : ρ)
  [[String.Slice.Pattern.ToBackwardSearcher]](#manual-String___Slice___Pattern___ToBackwardSearcher___mk) pat σ] : [Option](https://lean-lang.org/doc/reference/latest/Basic-Types/Optional-Values/#Option___none) s.[Pos]](#manual-String___Slice___Pos___mk)



[String.Slice.revFind?]](#manual-String___Slice___revFind___)
  {σ : [String.Slice]](#manual-String___Slice___mk) → Type}
  [(s : [String.Slice]](#manual-String___Slice___mk)) →
      [Std.Iterator](https://lean-lang.org/doc/reference/latest/Iterators/Iterator-Definitions/#Std___Iterator___mk) (σ s) [Id]](#manual-Id)
        (String.Slice.Pattern.SearchStep
          s)]
  [(s : [String.Slice]](#manual-String___Slice___mk)) →
      [Std.IteratorLoop](https://lean-lang.org/doc/reference/latest/Iterators/Iterator-Definitions/#Std___IteratorLoop___mk) (σ s) [Id]](#manual-Id) [Id]](#manual-Id)]
  {ρ : Type} (s : [String.Slice]](#manual-String___Slice___mk)) (pat : ρ)
  [[String.Slice.Pattern.ToBackwardSearcher]](#manual-String___Slice___Pattern___ToBackwardSearcher___mk)
      pat σ] :
  [Option](https://lean-lang.org/doc/reference/latest/Basic-Types/Optional-Values/#Option___none) s.[Pos]](#manual-String___Slice___Pos___mk)
```

Finds the position of the first match of the pattern `pat` in a slice, starting
from the end of the slice and traversing towards the start. If there is no match `[none](https://lean-lang.org/doc/reference/latest/Basic-Types/Optional-Values/#Option___none)` is
returned.

This function is generic over all currently supported patterns except
`[String]](#manual-String___ofByteArray)`/`[String.Slice]](#manual-String___Slice___mk)`.

Examples:

- `("coffee tea water".[toSlice]](#manual-String___toSlice).[revFind?]](#manual-String___Slice___revFind___) [Char.isWhitespace]](#manual-Char___isWhitespace)).[map](https://lean-lang.org/doc/reference/latest/Basic-Types/Optional-Values/#Option___map) (·.[get!]](#manual-String___Slice___Pos___get___)) == [some](https://lean-lang.org/doc/reference/latest/Basic-Types/Optional-Values/#Option___none) ' '`
- `"tea".[toSlice]](#manual-String___toSlice).[revFind?]](#manual-String___Slice___revFind___) (fun (c : [Char]](#manual-Char___mk)) => c == 'X') == [none](https://lean-lang.org/doc/reference/latest/Basic-Types/Optional-Values/#Option___none)`

###### 20.8.4.11.1.8. Manipulation {#manual-The-Lean-Language-Reference--Basic-Types--Strings--API-Reference--String-Slices--API-Reference--Manipulation}

def

```lean
[String.Slice.split]](#manual-String___Slice___split) {ρ : Type} {σ : [String.Slice]](#manual-String___Slice___mk) → Type}
  [(s : [String.Slice]](#manual-String___Slice___mk)) →
      [Std.Iterator](https://lean-lang.org/doc/reference/latest/Iterators/Iterator-Definitions/#Std___Iterator___mk) (σ s) [Id]](#manual-Id) (String.Slice.Pattern.SearchStep s)]
  (s : [String.Slice]](#manual-String___Slice___mk)) (pat : ρ)
  [[String.Slice.Pattern.ToForwardSearcher]](#manual-String___Slice___Pattern___ToForwardSearcher___mk) pat σ] : [Std.Iter](https://lean-lang.org/doc/reference/latest/Iterators/Iterator-Definitions/#Std___Iter___mk) [String.Slice]](#manual-String___Slice___mk)



[String.Slice.split]](#manual-String___Slice___split) {ρ : Type}
  {σ : [String.Slice]](#manual-String___Slice___mk) → Type}
  [(s : [String.Slice]](#manual-String___Slice___mk)) →
      [Std.Iterator](https://lean-lang.org/doc/reference/latest/Iterators/Iterator-Definitions/#Std___Iterator___mk) (σ s) [Id]](#manual-Id)
        (String.Slice.Pattern.SearchStep
          s)]
  (s : [String.Slice]](#manual-String___Slice___mk)) (pat : ρ)
  [[String.Slice.Pattern.ToForwardSearcher]](#manual-String___Slice___Pattern___ToForwardSearcher___mk)
      pat σ] :
  [Std.Iter](https://lean-lang.org/doc/reference/latest/Iterators/Iterator-Definitions/#Std___Iter___mk) [String.Slice]](#manual-String___Slice___mk)
```

Splits a slice at each subslice that matches the pattern `pat`.

The subslices that matched the pattern are not included in any of the resulting subslices. If
multiple subslices in a row match the pattern, the resulting list will contain empty strings.

This function is generic over all currently supported patterns.

Examples:

- `("coffee tea water".[toSlice]](#manual-String___toSlice).[split]](#manual-String___Slice___split) [Char.isWhitespace]](#manual-Char___isWhitespace)).toStringList == ["coffee", "tea", "water"]`
- `("coffee tea water".[toSlice]](#manual-String___toSlice).[split]](#manual-String___Slice___split) ' ').toStringList == ["coffee", "tea", "water"]`
- `("coffee tea water".[toSlice]](#manual-String___toSlice).[split]](#manual-String___Slice___split) " tea ").toStringList == ["coffee", "water"]`
- `("ababababa".[toSlice]](#manual-String___toSlice).[split]](#manual-String___Slice___split) "aba").toStringList == ["coffee", "water"]`
- `("baaab".[toSlice]](#manual-String___toSlice).[split]](#manual-String___Slice___split) "aa").toStringList == ["b", "ab"]`

def

```lean
[String.Slice.splitInclusive]](#manual-String___Slice___splitInclusive) {ρ : Type} {σ : [String.Slice]](#manual-String___Slice___mk) → Type}
  (s : [String.Slice]](#manual-String___Slice___mk)) (pat : ρ)
  [[String.Slice.Pattern.ToForwardSearcher]](#manual-String___Slice___Pattern___ToForwardSearcher___mk) pat σ] : [Std.Iter](https://lean-lang.org/doc/reference/latest/Iterators/Iterator-Definitions/#Std___Iter___mk) [String.Slice]](#manual-String___Slice___mk)



[String.Slice.splitInclusive]](#manual-String___Slice___splitInclusive) {ρ : Type}
  {σ : [String.Slice]](#manual-String___Slice___mk) → Type}
  (s : [String.Slice]](#manual-String___Slice___mk)) (pat : ρ)
  [[String.Slice.Pattern.ToForwardSearcher]](#manual-String___Slice___Pattern___ToForwardSearcher___mk)
      pat σ] :
  [Std.Iter](https://lean-lang.org/doc/reference/latest/Iterators/Iterator-Definitions/#Std___Iter___mk) [String.Slice]](#manual-String___Slice___mk)
```

Splits a slice at each subslice that matches the pattern `pat`. Unlike `[split]](#manual-split)` the
matched subslices are included at the end of each subslice.

This function is generic over all currently supported patterns.

Examples:

- `("coffee tea water".[toSlice]](#manual-String___toSlice).[splitInclusive]](#manual-String___Slice___splitInclusive) [Char.isWhitespace]](#manual-Char___isWhitespace)).[toList](https://lean-lang.org/doc/reference/latest/Iterators/Consuming-Iterators/#Std___Iter___toList) == ["coffee ".[toSlice]](#manual-String___toSlice), "tea ".[toSlice]](#manual-String___toSlice), "water".[toSlice]](#manual-String___toSlice)]`
- `("coffee tea water".[toSlice]](#manual-String___toSlice).[splitInclusive]](#manual-String___Slice___splitInclusive) ' ').[toList](https://lean-lang.org/doc/reference/latest/Iterators/Consuming-Iterators/#Std___Iter___toList) == ["coffee ".[toSlice]](#manual-String___toSlice), "tea ".[toSlice]](#manual-String___toSlice), "water".[toSlice]](#manual-String___toSlice)]`
- `("coffee tea water".[toSlice]](#manual-String___toSlice).[splitInclusive]](#manual-String___Slice___splitInclusive) " tea ").[toList](https://lean-lang.org/doc/reference/latest/Iterators/Consuming-Iterators/#Std___Iter___toList) == ["coffee tea ".[toSlice]](#manual-String___toSlice), "water".[toSlice]](#manual-String___toSlice)]`
- `("baaab".[toSlice]](#manual-String___toSlice).[splitInclusive]](#manual-String___Slice___splitInclusive) "aa").[toList](https://lean-lang.org/doc/reference/latest/Iterators/Consuming-Iterators/#Std___Iter___toList) == ["baa".[toSlice]](#manual-String___toSlice), "ab".[toSlice]](#manual-String___toSlice)]`

def

```lean
[String.Slice.lines]](#manual-String___Slice___lines) (s : [String.Slice]](#manual-String___Slice___mk)) : [Std.Iter](https://lean-lang.org/doc/reference/latest/Iterators/Iterator-Definitions/#Std___Iter___mk) [String.Slice]](#manual-String___Slice___mk)



[String.Slice.lines]](#manual-String___Slice___lines) (s : [String.Slice]](#manual-String___Slice___mk)) :
  [Std.Iter](https://lean-lang.org/doc/reference/latest/Iterators/Iterator-Definitions/#Std___Iter___mk) [String.Slice]](#manual-String___Slice___mk)
```

Creates an iterator over all lines in `s` with the line ending characters `\r\n` or `\n` being
stripped.

Examples:

- `"foo\r\nbar\n\nbaz\n".[toSlice]](#manual-String___toSlice).[lines]](#manual-String___Slice___lines).[toList](https://lean-lang.org/doc/reference/latest/Iterators/Consuming-Iterators/#Std___Iter___toList) == ["foo".[toSlice]](#manual-String___toSlice), "bar".[toSlice]](#manual-String___toSlice), "".[toSlice]](#manual-String___toSlice), "baz".[toSlice]](#manual-String___toSlice)]`
- `"foo\r\nbar\n\nbaz".[toSlice]](#manual-String___toSlice).[lines]](#manual-String___Slice___lines).[toList](https://lean-lang.org/doc/reference/latest/Iterators/Consuming-Iterators/#Std___Iter___toList) == ["foo".[toSlice]](#manual-String___toSlice), "bar".[toSlice]](#manual-String___toSlice), "".[toSlice]](#manual-String___toSlice), "baz".[toSlice]](#manual-String___toSlice)]`
- `"foo\r\nbar\n\nbaz\r".[toSlice]](#manual-String___toSlice).[lines]](#manual-String___Slice___lines).[toList](https://lean-lang.org/doc/reference/latest/Iterators/Consuming-Iterators/#Std___Iter___toList) == ["foo".[toSlice]](#manual-String___toSlice), "bar".[toSlice]](#manual-String___toSlice), "".[toSlice]](#manual-String___toSlice), "baz\r".[toSlice]](#manual-String___toSlice)]`

def

```lean
[String.Slice.trimAscii]](#manual-String___Slice___trimAscii) (s : [String.Slice]](#manual-String___Slice___mk)) : [String.Slice]](#manual-String___Slice___mk)



[String.Slice.trimAscii]](#manual-String___Slice___trimAscii)
  (s : [String.Slice]](#manual-String___Slice___mk)) : [String.Slice]](#manual-String___Slice___mk)
```

Removes leading and trailing whitespace from a slice.

“Whitespace” is defined as characters for which `[Char.isWhitespace]](#manual-Char___isWhitespace)` returns `[true](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)`.

Examples:

- `"abc".[toSlice]](#manual-String___toSlice).[trimAscii]](#manual-String___Slice___trimAscii) == "abc".[toSlice]](#manual-String___toSlice)`
- `" abc".[toSlice]](#manual-String___toSlice).[trimAscii]](#manual-String___Slice___trimAscii) == "abc".[toSlice]](#manual-String___toSlice)`
- `"abc \t ".[toSlice]](#manual-String___toSlice).[trimAscii]](#manual-String___Slice___trimAscii) == "abc".[toSlice]](#manual-String___toSlice)`
- `" abc ".[toSlice]](#manual-String___toSlice).[trimAscii]](#manual-String___Slice___trimAscii) == "abc".[toSlice]](#manual-String___toSlice)`
- `"abc\ndef\n".[toSlice]](#manual-String___toSlice).[trimAscii]](#manual-String___Slice___trimAscii) == "abc\ndef".[toSlice]](#manual-String___toSlice)`

def

```lean
[String.Slice.trimAsciiEnd]](#manual-String___Slice___trimAsciiEnd) (s : [String.Slice]](#manual-String___Slice___mk)) : [String.Slice]](#manual-String___Slice___mk)



[String.Slice.trimAsciiEnd]](#manual-String___Slice___trimAsciiEnd)
  (s : [String.Slice]](#manual-String___Slice___mk)) : [String.Slice]](#manual-String___Slice___mk)
```

Removes trailing whitespace from a slice by moving its end position to the last non-whitespace
character, or to its start position if there is no non-whitespace character.

“Whitespace” is defined as characters for which `[Char.isWhitespace]](#manual-Char___isWhitespace)` returns `[true](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)`.

Examples:

- `"abc".[toSlice]](#manual-String___toSlice).[trimAsciiEnd]](#manual-String___Slice___trimAsciiEnd) == "abc".[toSlice]](#manual-String___toSlice)`
- `" abc".[toSlice]](#manual-String___toSlice).[trimAsciiEnd]](#manual-String___Slice___trimAsciiEnd) == " abc".[toSlice]](#manual-String___toSlice)`
- `"abc \t ".[toSlice]](#manual-String___toSlice).[trimAsciiEnd]](#manual-String___Slice___trimAsciiEnd) == "abc".[toSlice]](#manual-String___toSlice)`
- `" abc ".[toSlice]](#manual-String___toSlice).[trimAsciiEnd]](#manual-String___Slice___trimAsciiEnd) == " abc".[toSlice]](#manual-String___toSlice)`
- `"abc\ndef\n".[toSlice]](#manual-String___toSlice).[trimAsciiEnd]](#manual-String___Slice___trimAsciiEnd) == "abc\ndef".[toSlice]](#manual-String___toSlice)`

def

```lean
[String.Slice.trimAsciiStart]](#manual-String___Slice___trimAsciiStart) (s : [String.Slice]](#manual-String___Slice___mk)) : [String.Slice]](#manual-String___Slice___mk)



[String.Slice.trimAsciiStart]](#manual-String___Slice___trimAsciiStart)
  (s : [String.Slice]](#manual-String___Slice___mk)) : [String.Slice]](#manual-String___Slice___mk)
```

Removes leading whitespace from a slice by moving its start position to the first non-whitespace
character, or to its end position if there is no non-whitespace character.

“Whitespace” is defined as characters for which `[Char.isWhitespace]](#manual-Char___isWhitespace)` returns `[true](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)`.

Examples:

- `"abc".[toSlice]](#manual-String___toSlice).[trimAsciiStart]](#manual-String___Slice___trimAsciiStart) == "abc".[toSlice]](#manual-String___toSlice)`
- `" abc".[toSlice]](#manual-String___toSlice).[trimAsciiStart]](#manual-String___Slice___trimAsciiStart) == "abc".[toSlice]](#manual-String___toSlice)`
- `"abc \t ".[toSlice]](#manual-String___toSlice).[trimAsciiStart]](#manual-String___Slice___trimAsciiStart) == "abc \t ".[toSlice]](#manual-String___toSlice)`
- `" abc ".[toSlice]](#manual-String___toSlice).[trimAsciiStart]](#manual-String___Slice___trimAsciiStart) == "abc ".[toSlice]](#manual-String___toSlice)`
- `"abc\ndef\n".[toSlice]](#manual-String___toSlice).[trimAsciiStart]](#manual-String___Slice___trimAsciiStart) == "abc\ndef\n".[toSlice]](#manual-String___toSlice)`

###### 20.8.4.11.1.9. Iteration {#manual-The-Lean-Language-Reference--Basic-Types--Strings--API-Reference--String-Slices--API-Reference--Iteration}

def

```lean
[String.Slice.chars]](#manual-String___Slice___chars) (s : [String.Slice]](#manual-String___Slice___mk)) : [Std.Iter](https://lean-lang.org/doc/reference/latest/Iterators/Iterator-Definitions/#Std___Iter___mk) [Char]](#manual-Char___mk)



[String.Slice.chars]](#manual-String___Slice___chars) (s : [String.Slice]](#manual-String___Slice___mk)) :
  [Std.Iter](https://lean-lang.org/doc/reference/latest/Iterators/Iterator-Definitions/#Std___Iter___mk) [Char]](#manual-Char___mk)
```

Creates an iterator over all characters (Unicode code points) in `s`.

Examples:

- `"abc".[toSlice]](#manual-String___toSlice).[chars]](#manual-String___Slice___chars).[toList](https://lean-lang.org/doc/reference/latest/Iterators/Consuming-Iterators/#Std___Iter___toList) = ['a', 'b', 'c']`
- `"ab∀c".[toSlice]](#manual-String___toSlice).[chars]](#manual-String___Slice___chars).[toList](https://lean-lang.org/doc/reference/latest/Iterators/Consuming-Iterators/#Std___Iter___toList) = ['a', 'b', '∀', 'c']`

def

```lean
[String.Slice.revChars]](#manual-String___Slice___revChars) (s : [String.Slice]](#manual-String___Slice___mk)) : [Std.Iter](https://lean-lang.org/doc/reference/latest/Iterators/Iterator-Definitions/#Std___Iter___mk) [Char]](#manual-Char___mk)



[String.Slice.revChars]](#manual-String___Slice___revChars) (s : [String.Slice]](#manual-String___Slice___mk)) :
  [Std.Iter](https://lean-lang.org/doc/reference/latest/Iterators/Iterator-Definitions/#Std___Iter___mk) [Char]](#manual-Char___mk)
```

Creates an iterator over all characters (Unicode code points) in `s`, starting from the end
of the slice and iterating towards the start.

Example:

- `"abc".[toSlice]](#manual-String___toSlice).[revChars]](#manual-String___Slice___revChars).[toList](https://lean-lang.org/doc/reference/latest/Iterators/Consuming-Iterators/#Std___Iter___toList) = ['c', 'b', 'a']`
- `"ab∀c".[toSlice]](#manual-String___toSlice).[revChars]](#manual-String___Slice___revChars).[toList](https://lean-lang.org/doc/reference/latest/Iterators/Consuming-Iterators/#Std___Iter___toList) = ['c', '∀', 'b', 'a']`

def

```lean
[String.Slice.positions]](#manual-String___Slice___positions) (s : [String.Slice]](#manual-String___Slice___mk)) :
  [Std.Iter](https://lean-lang.org/doc/reference/latest/Iterators/Iterator-Definitions/#Std___Iter___mk) [{](https://lean-lang.org/doc/reference/latest/Basic-Types/Subtypes/#Subtype___mk) p [//](https://lean-lang.org/doc/reference/latest/Basic-Types/Subtypes/#Subtype___mk) p ≠ s.[endPos]](#manual-String___Slice___endPos) [}](https://lean-lang.org/doc/reference/latest/Basic-Types/Subtypes/#Subtype___mk)



[String.Slice.positions]](#manual-String___Slice___positions)
  (s : [String.Slice]](#manual-String___Slice___mk)) :
  [Std.Iter](https://lean-lang.org/doc/reference/latest/Iterators/Iterator-Definitions/#Std___Iter___mk) [{](https://lean-lang.org/doc/reference/latest/Basic-Types/Subtypes/#Subtype___mk) p [//](https://lean-lang.org/doc/reference/latest/Basic-Types/Subtypes/#Subtype___mk) p ≠ s.[endPos]](#manual-String___Slice___endPos) [}](https://lean-lang.org/doc/reference/latest/Basic-Types/Subtypes/#Subtype___mk)
```

Creates an iterator over all valid positions within {name}`s`.

Examples:

- {lean}`("abc".[toSlice]](#manual-String___toSlice).[positions]](#manual-String___Slice___positions).[map](https://lean-lang.org/doc/reference/latest/Iterators/Iterator-Combinators/#Std___Iter___map) (fun ⟨p, h⟩ => p.[get]](#manual-String___Slice___Pos___get) h) |>.[toList](https://lean-lang.org/doc/reference/latest/Iterators/Consuming-Iterators/#Std___Iter___toList)) = ['a', 'b', 'c']`
- {lean}`("abc".[toSlice]](#manual-String___toSlice).[positions]](#manual-String___Slice___positions).[map](https://lean-lang.org/doc/reference/latest/Iterators/Iterator-Combinators/#Std___Iter___map) (·.[val](https://lean-lang.org/doc/reference/latest/Basic-Types/Subtypes/#Subtype___mk).[offset]](#manual-String___Slice___Pos___mk).[byteIdx]](#manual-String___Pos___Raw___mk)) |>.[toList](https://lean-lang.org/doc/reference/latest/Iterators/Consuming-Iterators/#Std___Iter___toList)) = [0, 1, 2]`
- {lean}`("ab∀c".[toSlice]](#manual-String___toSlice).[positions]](#manual-String___Slice___positions).[map](https://lean-lang.org/doc/reference/latest/Iterators/Iterator-Combinators/#Std___Iter___map) (fun ⟨p, h⟩ => p.[get]](#manual-String___Slice___Pos___get) h) |>.[toList](https://lean-lang.org/doc/reference/latest/Iterators/Consuming-Iterators/#Std___Iter___toList)) = ['a', 'b', '∀', 'c']`
- {lean}`("ab∀c".[toSlice]](#manual-String___toSlice).[positions]](#manual-String___Slice___positions).[map](https://lean-lang.org/doc/reference/latest/Iterators/Iterator-Combinators/#Std___Iter___map) (·.[val](https://lean-lang.org/doc/reference/latest/Basic-Types/Subtypes/#Subtype___mk).[offset]](#manual-String___Slice___Pos___mk).[byteIdx]](#manual-String___Pos___Raw___mk)) |>.[toList](https://lean-lang.org/doc/reference/latest/Iterators/Consuming-Iterators/#Std___Iter___toList)) = [0, 1, 2, 5]`

def

```lean
[String.Slice.revPositions]](#manual-String___Slice___revPositions) (s : [String.Slice]](#manual-String___Slice___mk)) :
  [Std.Iter](https://lean-lang.org/doc/reference/latest/Iterators/Iterator-Definitions/#Std___Iter___mk) [{](https://lean-lang.org/doc/reference/latest/Basic-Types/Subtypes/#Subtype___mk) p [//](https://lean-lang.org/doc/reference/latest/Basic-Types/Subtypes/#Subtype___mk) p ≠ s.[endPos]](#manual-String___Slice___endPos) [}](https://lean-lang.org/doc/reference/latest/Basic-Types/Subtypes/#Subtype___mk)



[String.Slice.revPositions]](#manual-String___Slice___revPositions)
  (s : [String.Slice]](#manual-String___Slice___mk)) :
  [Std.Iter](https://lean-lang.org/doc/reference/latest/Iterators/Iterator-Definitions/#Std___Iter___mk) [{](https://lean-lang.org/doc/reference/latest/Basic-Types/Subtypes/#Subtype___mk) p [//](https://lean-lang.org/doc/reference/latest/Basic-Types/Subtypes/#Subtype___mk) p ≠ s.[endPos]](#manual-String___Slice___endPos) [}](https://lean-lang.org/doc/reference/latest/Basic-Types/Subtypes/#Subtype___mk)
```

Creates an iterator over all valid positions within {name}`s`, starting from the last valid
position and iterating towards the first one.

Examples

- {lean}`("abc".[toSlice]](#manual-String___toSlice).[revPositions]](#manual-String___Slice___revPositions).[map](https://lean-lang.org/doc/reference/latest/Iterators/Iterator-Combinators/#Std___Iter___map) (fun ⟨p, h⟩ => p.[get]](#manual-String___Slice___Pos___get) h) |>.[toList](https://lean-lang.org/doc/reference/latest/Iterators/Consuming-Iterators/#Std___Iter___toList)) = ['c', 'b', 'a']`
- {lean}`("abc".[toSlice]](#manual-String___toSlice).[revPositions]](#manual-String___Slice___revPositions).[map](https://lean-lang.org/doc/reference/latest/Iterators/Iterator-Combinators/#Std___Iter___map) (·.[val](https://lean-lang.org/doc/reference/latest/Basic-Types/Subtypes/#Subtype___mk).[offset]](#manual-String___Slice___Pos___mk).[byteIdx]](#manual-String___Pos___Raw___mk)) |>.[toList](https://lean-lang.org/doc/reference/latest/Iterators/Consuming-Iterators/#Std___Iter___toList)) = [2, 1, 0]`
- {lean}`("ab∀c".[toSlice]](#manual-String___toSlice).[revPositions]](#manual-String___Slice___revPositions).[map](https://lean-lang.org/doc/reference/latest/Iterators/Iterator-Combinators/#Std___Iter___map) (fun ⟨p, h⟩ => p.[get]](#manual-String___Slice___Pos___get) h) |>.[toList](https://lean-lang.org/doc/reference/latest/Iterators/Consuming-Iterators/#Std___Iter___toList)) = ['c', '∀', 'b', 'a']`
- {lean}`("ab∀c".[toSlice]](#manual-String___toSlice).[revPositions]](#manual-String___Slice___revPositions).[map](https://lean-lang.org/doc/reference/latest/Iterators/Iterator-Combinators/#Std___Iter___map) (·.[val](https://lean-lang.org/doc/reference/latest/Basic-Types/Subtypes/#Subtype___mk).[offset]](#manual-String___Slice___Pos___mk).[byteIdx]](#manual-String___Pos___Raw___mk)) |>.[toList](https://lean-lang.org/doc/reference/latest/Iterators/Consuming-Iterators/#Std___Iter___toList)) = [5, 2, 1, 0]`

def

```lean
[String.Slice.bytes]](#manual-String___Slice___bytes) (s : [String.Slice]](#manual-String___Slice___mk)) : [Std.Iter](https://lean-lang.org/doc/reference/latest/Iterators/Iterator-Definitions/#Std___Iter___mk) [UInt8]](#manual-UInt8___ofBitVec)



[String.Slice.bytes]](#manual-String___Slice___bytes) (s : [String.Slice]](#manual-String___Slice___mk)) :
  [Std.Iter](https://lean-lang.org/doc/reference/latest/Iterators/Iterator-Definitions/#Std___Iter___mk) [UInt8]](#manual-UInt8___ofBitVec)
```

Creates an iterator over all bytes in {name}`s`.

Examples:

- {lean}`"abc".[toSlice]](#manual-String___toSlice).[bytes]](#manual-String___Slice___bytes).[toList](https://lean-lang.org/doc/reference/latest/Iterators/Consuming-Iterators/#Std___Iter___toList) = [97, 98, 99]`
- {lean}`"ab∀c".[toSlice]](#manual-String___toSlice).[bytes]](#manual-String___Slice___bytes).[toList](https://lean-lang.org/doc/reference/latest/Iterators/Consuming-Iterators/#Std___Iter___toList) = [97, 98, 226, 136, 128, 99]`

def

```lean
[String.Slice.revBytes]](#manual-String___Slice___revBytes) (s : [String.Slice]](#manual-String___Slice___mk)) : [Std.Iter](https://lean-lang.org/doc/reference/latest/Iterators/Iterator-Definitions/#Std___Iter___mk) [UInt8]](#manual-UInt8___ofBitVec)



[String.Slice.revBytes]](#manual-String___Slice___revBytes) (s : [String.Slice]](#manual-String___Slice___mk)) :
  [Std.Iter](https://lean-lang.org/doc/reference/latest/Iterators/Iterator-Definitions/#Std___Iter___mk) [UInt8]](#manual-UInt8___ofBitVec)
```

Creates an iterator over all bytes in {name}`s`, starting from the last one and iterating towards
the first one.

Examples:

- {lean}`"abc".[toSlice]](#manual-String___toSlice).[revBytes]](#manual-String___Slice___revBytes).[toList](https://lean-lang.org/doc/reference/latest/Iterators/Consuming-Iterators/#Std___Iter___toList) = [99, 98, 97]`
- {lean}`"ab∀c".[toSlice]](#manual-String___toSlice).[revBytes]](#manual-String___Slice___revBytes).[toList](https://lean-lang.org/doc/reference/latest/Iterators/Consuming-Iterators/#Std___Iter___toList) = [99, 128, 136, 226, 98, 97]`

def

```lean
[String.Slice.revSplit]](#manual-String___Slice___revSplit) {σ : [String.Slice]](#manual-String___Slice___mk) → Type} {ρ : Type}
  (s : [String.Slice]](#manual-String___Slice___mk)) (pat : ρ)
  [[String.Slice.Pattern.ToBackwardSearcher]](#manual-String___Slice___Pattern___ToBackwardSearcher___mk) pat σ] :
  [Std.Iter](https://lean-lang.org/doc/reference/latest/Iterators/Iterator-Definitions/#Std___Iter___mk) [String.Slice]](#manual-String___Slice___mk)



[String.Slice.revSplit]](#manual-String___Slice___revSplit)
  {σ : [String.Slice]](#manual-String___Slice___mk) → Type} {ρ : Type}
  (s : [String.Slice]](#manual-String___Slice___mk)) (pat : ρ)
  [[String.Slice.Pattern.ToBackwardSearcher]](#manual-String___Slice___Pattern___ToBackwardSearcher___mk)
      pat σ] :
  [Std.Iter](https://lean-lang.org/doc/reference/latest/Iterators/Iterator-Definitions/#Std___Iter___mk) [String.Slice]](#manual-String___Slice___mk)
```

Splits a slice at each subslice that matches the pattern `pat`, starting from the end of the
slice and traversing towards the start.

The subslices that matched the pattern are not included in any of the resulting subslices. If
multiple subslices in a row match the pattern, the resulting list will contain empty slices.

This function is generic over all currently supported patterns except
`[String]](#manual-String___ofByteArray)`/`[String.Slice]](#manual-String___Slice___mk)`.

Examples:

- `("coffee tea water".[toSlice]](#manual-String___toSlice).[revSplit]](#manual-String___Slice___revSplit) [Char.isWhitespace]](#manual-Char___isWhitespace)).[toList](https://lean-lang.org/doc/reference/latest/Iterators/Consuming-Iterators/#Std___Iter___toList) == ["water".[toSlice]](#manual-String___toSlice), "tea".[toSlice]](#manual-String___toSlice), "coffee".[toSlice]](#manual-String___toSlice)]`
- `("coffee tea water".[toSlice]](#manual-String___toSlice).[revSplit]](#manual-String___Slice___revSplit) ' ').[toList](https://lean-lang.org/doc/reference/latest/Iterators/Consuming-Iterators/#Std___Iter___toList) == ["water".[toSlice]](#manual-String___toSlice), "tea".[toSlice]](#manual-String___toSlice), "coffee".[toSlice]](#manual-String___toSlice)]`

def

```lean
[String.Slice.foldl.{u}]](#manual-String___Slice___foldl) {α : Type u} (f : α → [Char]](#manual-Char___mk) → α) (init : α)
  (s : [String.Slice]](#manual-String___Slice___mk)) : α



[String.Slice.foldl.{u}]](#manual-String___Slice___foldl) {α : Type u}
  (f : α → [Char]](#manual-Char___mk) → α) (init : α)
  (s : [String.Slice]](#manual-String___Slice___mk)) : α
```

Folds a function over a slice from the start, accumulating a value starting with `init`. The
accumulated value is combined with each character in order, using `f`.

Examples:

- `"coffee tea water".[toSlice]](#manual-String___toSlice).[foldl]](#manual-String___Slice___foldl) (fun n c => [if]](#manual-termIfThenElse) c.[isWhitespace]](#manual-Char___isWhitespace) [then]](#manual-termIfThenElse) n + 1 [else]](#manual-termIfThenElse) n) 0 = 2`
- `"coffee tea and water".[toSlice]](#manual-String___toSlice).[foldl]](#manual-String___Slice___foldl) (fun n c => [if]](#manual-termIfThenElse) c.[isWhitespace]](#manual-Char___isWhitespace) [then]](#manual-termIfThenElse) n + 1 [else]](#manual-termIfThenElse) n) 0 = 3`
- `"coffee tea water".[toSlice]](#manual-String___toSlice).[foldl]](#manual-String___Slice___foldl) (·.[push]](#manual-String___push) ·) "" = "coffee tea water"`

def

```lean
[String.Slice.foldr.{u}]](#manual-String___Slice___foldr) {α : Type u} (f : [Char]](#manual-Char___mk) → α → α) (init : α)
  (s : [String.Slice]](#manual-String___Slice___mk)) : α



[String.Slice.foldr.{u}]](#manual-String___Slice___foldr) {α : Type u}
  (f : [Char]](#manual-Char___mk) → α → α) (init : α)
  (s : [String.Slice]](#manual-String___Slice___mk)) : α
```

Folds a function over a slice from the end, accumulating a value starting with `init`. The
accumulated value is combined with each character in reverse order, using `f`.

Examples:

- `"coffee tea water".[toSlice]](#manual-String___toSlice).[foldr]](#manual-String___Slice___foldr) (fun c n => [if]](#manual-termIfThenElse) c.[isWhitespace]](#manual-Char___isWhitespace) [then]](#manual-termIfThenElse) n + 1 [else]](#manual-termIfThenElse) n) 0 = 2`
- `"coffee tea and water".[toSlice]](#manual-String___toSlice).[foldr]](#manual-String___Slice___foldr) (fun c n => [if]](#manual-termIfThenElse) c.[isWhitespace]](#manual-Char___isWhitespace) [then]](#manual-termIfThenElse) n + 1 [else]](#manual-termIfThenElse) n) 0 = 3`
- `"coffee tea water".[toSlice]](#manual-String___toSlice).[foldr]](#manual-String___Slice___foldr) (fun c s => s.[push]](#manual-String___push) c) "" = "retaw aet eeffoc"`

###### 20.8.4.11.1.10. Conversions {#manual-The-Lean-Language-Reference--Basic-Types--Strings--API-Reference--String-Slices--API-Reference--Conversions}

def

```lean
[String.Slice.isNat]](#manual-String___Slice___isNat) (s : [String.Slice]](#manual-String___Slice___mk)) : [Bool](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)



[String.Slice.isNat]](#manual-String___Slice___isNat) (s : [String.Slice]](#manual-String___Slice___mk)) :
  [Bool](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)
```

Checks whether the slice can be interpreted as the decimal representation of a natural number.

A slice can be interpreted as a decimal natural number if it is not empty and all the characters in
it are digits. Underscores (`_`) are allowed as digit separators for readability, but cannot appear
at the start, at the end, or consecutively.

Use `toNat?` or
`toNat!` to convert such a slice to a natural number.

Examples:

- `"".[toSlice]](#manual-String___toSlice).[isNat]](#manual-String___Slice___isNat) = [false](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)`
- `"0".[toSlice]](#manual-String___toSlice).[isNat]](#manual-String___Slice___isNat) = [true](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)`
- `"5".[toSlice]](#manual-String___toSlice).[isNat]](#manual-String___Slice___isNat) = [true](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)`
- `"05".[toSlice]](#manual-String___toSlice).[isNat]](#manual-String___Slice___isNat) = [true](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)`
- `"587".[toSlice]](#manual-String___toSlice).[isNat]](#manual-String___Slice___isNat) = [true](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)`
- `"1_000".[toSlice]](#manual-String___toSlice).[isNat]](#manual-String___Slice___isNat) = [true](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)`
- `"100_000_000".[toSlice]](#manual-String___toSlice).[isNat]](#manual-String___Slice___isNat) = [true](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)`
- `"-587".[toSlice]](#manual-String___toSlice).[isNat]](#manual-String___Slice___isNat) = [false](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)`
- `" 5".[toSlice]](#manual-String___toSlice).[isNat]](#manual-String___Slice___isNat) = [false](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)`
- `"2+3".[toSlice]](#manual-String___toSlice).[isNat]](#manual-String___Slice___isNat) = [false](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)`
- `"0xff".[toSlice]](#manual-String___toSlice).[isNat]](#manual-String___Slice___isNat) = [false](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)`
- `"_123".[toSlice]](#manual-String___toSlice).[isNat]](#manual-String___Slice___isNat) = [false](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)`
- `"123_".[toSlice]](#manual-String___toSlice).[isNat]](#manual-String___Slice___isNat) = [false](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)`
- `"12__34".[toSlice]](#manual-String___toSlice).[isNat]](#manual-String___Slice___isNat) = [false](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)`

def

```lean
[String.Slice.toNat!]](#manual-String___Slice___toNat___) (s : [String.Slice]](#manual-String___Slice___mk)) : [Nat]](#manual-Nat___zero)



[String.Slice.toNat!]](#manual-String___Slice___toNat___) (s : [String.Slice]](#manual-String___Slice___mk)) :
  [Nat]](#manual-Nat___zero)
```

Interprets a slice as the decimal representation of a natural number, returning it. Panics if the
slice does not contain a decimal natural number.

A slice can be interpreted as a decimal natural number if it is not empty and all the characters in
it are digits. Underscores (`_`) are allowed as digit separators and are ignored during parsing.

Use `isNat` to check whether `toNat!` would return a value. `toNat?` is a safer
alternative that returns `[none](https://lean-lang.org/doc/reference/latest/Basic-Types/Optional-Values/#Option___none)` instead of panicking when the string is not a natural number.

Examples:

- `"0".[toSlice]](#manual-String___toSlice).[toNat!]](#manual-String___Slice___toNat___) = 0`
- `"5".[toSlice]](#manual-String___toSlice).[toNat!]](#manual-String___Slice___toNat___) = 5`
- `"587".[toSlice]](#manual-String___toSlice).[toNat!]](#manual-String___Slice___toNat___) = 587`
- `"1_000".[toSlice]](#manual-String___toSlice).[toNat!]](#manual-String___Slice___toNat___) = 1000`

def

```lean
[String.Slice.toNat?]](#manual-String___Slice___toNat___-next) (s : [String.Slice]](#manual-String___Slice___mk)) : [Option](https://lean-lang.org/doc/reference/latest/Basic-Types/Optional-Values/#Option___none) [Nat]](#manual-Nat___zero)



[String.Slice.toNat?]](#manual-String___Slice___toNat___-next) (s : [String.Slice]](#manual-String___Slice___mk)) :
  [Option](https://lean-lang.org/doc/reference/latest/Basic-Types/Optional-Values/#Option___none) [Nat]](#manual-Nat___zero)
```

Interprets a slice as the decimal representation of a natural number, returning it. Returns
`[none](https://lean-lang.org/doc/reference/latest/Basic-Types/Optional-Values/#Option___none)` if the slice does not contain a decimal natural number.

A slice can be interpreted as a decimal natural number if it is not empty and all the characters in
it are digits. Underscores (`_`) are allowed as digit separators and are ignored during parsing.

Use `isNat` to check whether `toNat?` would return `[some](https://lean-lang.org/doc/reference/latest/Basic-Types/Optional-Values/#Option___none)`.
`toNat!` is an alternative that panics instead of
returning `[none](https://lean-lang.org/doc/reference/latest/Basic-Types/Optional-Values/#Option___none)` when the slice is not a natural number.

Examples:

- `"".[toSlice]](#manual-String___toSlice).[toNat?]](#manual-String___Slice___toNat___-next) = [none](https://lean-lang.org/doc/reference/latest/Basic-Types/Optional-Values/#Option___none)`
- `"0".[toSlice]](#manual-String___toSlice).[toNat?]](#manual-String___Slice___toNat___-next) = [some](https://lean-lang.org/doc/reference/latest/Basic-Types/Optional-Values/#Option___none) 0`
- `"5".[toSlice]](#manual-String___toSlice).[toNat?]](#manual-String___Slice___toNat___-next) = [some](https://lean-lang.org/doc/reference/latest/Basic-Types/Optional-Values/#Option___none) 5`
- `"587".[toSlice]](#manual-String___toSlice).[toNat?]](#manual-String___Slice___toNat___-next) = [some](https://lean-lang.org/doc/reference/latest/Basic-Types/Optional-Values/#Option___none) 587`
- `"1_000".[toSlice]](#manual-String___toSlice).[toNat?]](#manual-String___Slice___toNat___-next) = [some](https://lean-lang.org/doc/reference/latest/Basic-Types/Optional-Values/#Option___none) 1000`
- `"100_000_000".[toSlice]](#manual-String___toSlice).[toNat?]](#manual-String___Slice___toNat___-next) = [some](https://lean-lang.org/doc/reference/latest/Basic-Types/Optional-Values/#Option___none) 100000000`
- `"-587".[toSlice]](#manual-String___toSlice).[toNat?]](#manual-String___Slice___toNat___-next) = [none](https://lean-lang.org/doc/reference/latest/Basic-Types/Optional-Values/#Option___none)`
- `" 5".[toSlice]](#manual-String___toSlice).[toNat?]](#manual-String___Slice___toNat___-next) = [none](https://lean-lang.org/doc/reference/latest/Basic-Types/Optional-Values/#Option___none)`
- `"2+3".[toSlice]](#manual-String___toSlice).[toNat?]](#manual-String___Slice___toNat___-next) = [none](https://lean-lang.org/doc/reference/latest/Basic-Types/Optional-Values/#Option___none)`
- `"0xff".[toSlice]](#manual-String___toSlice).[toNat?]](#manual-String___Slice___toNat___-next) = [none](https://lean-lang.org/doc/reference/latest/Basic-Types/Optional-Values/#Option___none)`

###### 20.8.4.11.1.11. Equality {#manual-The-Lean-Language-Reference--Basic-Types--Strings--API-Reference--String-Slices--API-Reference--Equality}

def

```lean
[String.Slice.beq]](#manual-String___Slice___beq) (s1 s2 : [String.Slice]](#manual-String___Slice___mk)) : [Bool](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)



[String.Slice.beq]](#manual-String___Slice___beq) (s1 s2 : [String.Slice]](#manual-String___Slice___mk)) :
  [Bool](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)
```

Checks whether `s1` and `s2` represent the same string, even if they are slices of
different base strings or different slices within the same string.

The implementation is an efficient equivalent of `s1.[copy]](#manual-String___Slice___copy) == s2.[copy]](#manual-String___Slice___copy)`

def

```lean
[String.Slice.eqIgnoreAsciiCase]](#manual-String___Slice___eqIgnoreAsciiCase) (s1 s2 : [String.Slice]](#manual-String___Slice___mk)) : [Bool](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)



[String.Slice.eqIgnoreAsciiCase]](#manual-String___Slice___eqIgnoreAsciiCase)
  (s1 s2 : [String.Slice]](#manual-String___Slice___mk)) : [Bool](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)
```

Checks whether `s1 == s2` if ASCII upper/lowercase are ignored.

##### 20.8.4.11.2. Patterns {#manual-The-Lean-Language-Reference--Basic-Types--Strings--API-Reference--String-Slices--Patterns}

String slices feature generalized search patterns.
Rather than being defined to work only for characters or for strings, many operations on slices accept arbitrary patterns.
New types can be made into patterns by defining instances of the classes in this section.
The Lean standard library provides instances that allow the following types to be used for both forward and backward searching:

| Pattern Type | Meaning |
| --- | --- |
| `[Char]](#manual-Char___mk)` | Matches the provided character |
| `[Char]](#manual-Char___mk) → [Bool](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)` | Matches any character that satisfies the predicate |
| `[String]](#manual-String___ofByteArray)` | Matches occurrences of the given string |
| `[String.Slice]](#manual-String___Slice___mk)` | Matches occurrences of the string represented by the slice |

type class

```lean
[String.Slice.Pattern.ToForwardSearcher]](#manual-String___Slice___Pattern___ToForwardSearcher___mk) {ρ : Type} (pat : ρ)
  (σ : [outParam]](#manual-outParam) ([String.Slice]](#manual-String___Slice___mk) → Type)) : Type



[String.Slice.Pattern.ToForwardSearcher]](#manual-String___Slice___Pattern___ToForwardSearcher___mk)
  {ρ : Type} (pat : ρ)
  (σ : [outParam]](#manual-outParam) ([String.Slice]](#manual-String___Slice___mk) → Type)) :
  Type
```

Provides a conversion from a pattern to an iterator of `SearchStep` that searches for matches
of the pattern from the start towards the end of a `Slice`.

While these operations can be implemented on top of `ForwardPattern`, some patterns allow
for more efficient implementations. For example, a searcher for `[String]](#manual-String___ofByteArray)` patterns derived
from the `ForwardPattern` instance on strings would try to match the pattern at every
position in the string, but more efficient string matching routines are known. Indeed, the Lean
standard library uses the Knuth-Morris-Pratt algorithm. See the module
`Init.Data.String.Pattern.String` for the implementation.

This class can be used to provide such an efficient implementation. If there is no
need to specialize in this fashion, then
`ToForwardSearcher.defaultImplementation` can be
used to automatically derive an instance.

Instance Constructor

```lean
[String.Slice.Pattern.ToForwardSearcher.mk]](#manual-String___Slice___Pattern___ToForwardSearcher___mk)
```

Methods

```lean
toSearcher : (s : [String.Slice]](#manual-String___Slice___mk)) → [Std.Iter](https://lean-lang.org/doc/reference/latest/Iterators/Iterator-Definitions/#Std___Iter___mk) (String.Slice.Pattern.SearchStep s)
```

Builds an iterator of `SearchStep` corresponding to matches of `pat` along the slice
`s`. The `SearchStep`s returned by this iterator must contain ranges that are
adjacent, non-overlapping and cover all of `s`.

type class

```lean
[String.Slice.Pattern.ForwardPattern]](#manual-String___Slice___Pattern___ForwardPattern___mk) {ρ : Type} (pat : ρ) : Type



[String.Slice.Pattern.ForwardPattern]](#manual-String___Slice___Pattern___ForwardPattern___mk)
  {ρ : Type} (pat : ρ) : Type
```

Provides simple pattern matching capabilities from the start of a `Slice`.

Instance Constructor

```lean
[String.Slice.Pattern.ForwardPattern.mk]](#manual-String___Slice___Pattern___ForwardPattern___mk)
```

Methods

```lean
skipPrefix? : (s : [String.Slice]](#manual-String___Slice___mk)) → [Option](https://lean-lang.org/doc/reference/latest/Basic-Types/Optional-Values/#Option___none) s.[Pos]](#manual-String___Slice___Pos___mk)
```

Checks whether the slice starts with the pattern. If it does, the slice is returned with the
prefix removed; otherwise the result is `[none](https://lean-lang.org/doc/reference/latest/Basic-Types/Optional-Values/#Option___none)`.

```lean
skipPrefixOfNonempty? : (s : [String.Slice]](#manual-String___Slice___mk)) → s.[isEmpty]](#manual-String___Slice___isEmpty) [=]](#manual-Eq___refl) [false](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false) → [Option](https://lean-lang.org/doc/reference/latest/Basic-Types/Optional-Values/#Option___none) s.[Pos]](#manual-String___Slice___Pos___mk)
```

Checks whether the slice starts with the pattern. If it does, the slice is returned with the
prefix removed; otherwise the result is `[none](https://lean-lang.org/doc/reference/latest/Basic-Types/Optional-Values/#Option___none)`.

```lean
startsWith : [String.Slice]](#manual-String___Slice___mk) → [Bool](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)
```

Checks whether the slice starts with the pattern.

type class

```lean
[String.Slice.Pattern.ToBackwardSearcher]](#manual-String___Slice___Pattern___ToBackwardSearcher___mk) {ρ : Type} (pat : ρ)
  (σ : [outParam]](#manual-outParam) ([String.Slice]](#manual-String___Slice___mk) → Type)) : Type



[String.Slice.Pattern.ToBackwardSearcher]](#manual-String___Slice___Pattern___ToBackwardSearcher___mk)
  {ρ : Type} (pat : ρ)
  (σ : [outParam]](#manual-outParam) ([String.Slice]](#manual-String___Slice___mk) → Type)) :
  Type
```

Provides a conversion from a pattern to an iterator of `SearchStep` searching for matches of
the pattern from the end towards the start of a `Slice`.

While these operations can be implemented on top of `BackwardPattern`, some patterns allow
for more efficient implementations. For example, a searcher for `[String]](#manual-String___ofByteArray)` patterns derived
from the `BackwardPattern` instance on strings would try to match the pattern at every
position in the string, but more efficient string matching routines are known. Indeed, the Lean
standard library uses the Knuth-Morris-Pratt algorithm. See the module
`Init.Data.String.Pattern.String` for the implementation.

This class can be used to provide such an efficient implementation. If there is no
need to specialize in this fashion, then
`ToBackwardSearcher.defaultImplementation` can be
used to automatically derive an instance.

Instance Constructor

```lean
[String.Slice.Pattern.ToBackwardSearcher.mk]](#manual-String___Slice___Pattern___ToBackwardSearcher___mk)
```

Methods

```lean
toSearcher : (s : [String.Slice]](#manual-String___Slice___mk)) → [Std.Iter](https://lean-lang.org/doc/reference/latest/Iterators/Iterator-Definitions/#Std___Iter___mk) (String.Slice.Pattern.SearchStep s)
```

Build an iterator of `SearchStep` corresponding to matches of `pat` along the slice
`s`. The `SearchStep`s returned by this iterator must contain ranges that are
adjacent, non-overlapping and cover all of `s`.

type class

```lean
[String.Slice.Pattern.BackwardPattern]](#manual-String___Slice___Pattern___BackwardPattern___mk) {ρ : Type} (pat : ρ) : Type



[String.Slice.Pattern.BackwardPattern]](#manual-String___Slice___Pattern___BackwardPattern___mk)
  {ρ : Type} (pat : ρ) : Type
```

Provides simple pattern matching capabilities from the end of a `Slice`.

Instance Constructor

```lean
[String.Slice.Pattern.BackwardPattern.mk]](#manual-String___Slice___Pattern___BackwardPattern___mk)
```

Methods

```lean
skipSuffix? : (s : [String.Slice]](#manual-String___Slice___mk)) → [Option](https://lean-lang.org/doc/reference/latest/Basic-Types/Optional-Values/#Option___none) s.[Pos]](#manual-String___Slice___Pos___mk)
```

Checks whether the slice ends with the pattern. If it does, the slice is returned with the
suffix removed; otherwise the result is `[none](https://lean-lang.org/doc/reference/latest/Basic-Types/Optional-Values/#Option___none)`.

```lean
skipSuffixOfNonempty? : (s : [String.Slice]](#manual-String___Slice___mk)) → s.[isEmpty]](#manual-String___Slice___isEmpty) [=]](#manual-Eq___refl) [false](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false) → [Option](https://lean-lang.org/doc/reference/latest/Basic-Types/Optional-Values/#Option___none) s.[Pos]](#manual-String___Slice___Pos___mk)
```

Checks whether the slice ends with the pattern. If it does, the slice is returned with the
suffix removed; otherwise the result is `[none](https://lean-lang.org/doc/reference/latest/Basic-Types/Optional-Values/#Option___none)`.

```lean
endsWith : [String.Slice]](#manual-String___Slice___mk) → [Bool](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)
```

Checks whether the slice ends with the pattern.

##### 20.8.4.11.3. Positions {#manual-The-Lean-Language-Reference--Basic-Types--Strings--API-Reference--String-Slices--Positions}

###### 20.8.4.11.3.1. Lookups {#manual-The-Lean-Language-Reference--Basic-Types--Strings--API-Reference--String-Slices--Positions--Lookups}

Because they retain a reference to the slice from which they were drawn, slice positions allow individual characters or bytes to be looked up.

def

```lean
[String.Slice.Pos.byte]](#manual-String___Slice___Pos___byte) {s : [String.Slice]](#manual-String___Slice___mk)} (pos : s.[Pos]](#manual-String___Slice___Pos___mk))
  (h : pos ≠ s.[endPos]](#manual-String___Slice___endPos)) : [UInt8]](#manual-UInt8___ofBitVec)



[String.Slice.Pos.byte]](#manual-String___Slice___Pos___byte) {s : [String.Slice]](#manual-String___Slice___mk)}
  (pos : s.[Pos]](#manual-String___Slice___Pos___mk)) (h : pos ≠ s.[endPos]](#manual-String___Slice___endPos)) :
  [UInt8]](#manual-UInt8___ofBitVec)
```

Returns the byte at a position in a slice that is not the end position.

def

```lean
[String.Slice.Pos.get]](#manual-String___Slice___Pos___get) {s : [String.Slice]](#manual-String___Slice___mk)} (pos : s.[Pos]](#manual-String___Slice___Pos___mk))
  (h : pos ≠ s.[endPos]](#manual-String___Slice___endPos)) : [Char]](#manual-Char___mk)



[String.Slice.Pos.get]](#manual-String___Slice___Pos___get) {s : [String.Slice]](#manual-String___Slice___mk)}
  (pos : s.[Pos]](#manual-String___Slice___Pos___mk)) (h : pos ≠ s.[endPos]](#manual-String___Slice___endPos)) :
  [Char]](#manual-Char___mk)
```

Obtains the character at the given position in the string.

def

```lean
[String.Slice.Pos.get!]](#manual-String___Slice___Pos___get___) {s : [String.Slice]](#manual-String___Slice___mk)} (pos : s.[Pos]](#manual-String___Slice___Pos___mk)) : [Char]](#manual-Char___mk)



[String.Slice.Pos.get!]](#manual-String___Slice___Pos___get___) {s : [String.Slice]](#manual-String___Slice___mk)}
  (pos : s.[Pos]](#manual-String___Slice___Pos___mk)) : [Char]](#manual-Char___mk)
```

Returns the byte at the given position in the string, or panics if the position is the end
position.

def

```lean
[String.Slice.Pos.get?]](#manual-String___Slice___Pos___get___-next) {s : [String.Slice]](#manual-String___Slice___mk)} (pos : s.[Pos]](#manual-String___Slice___Pos___mk)) : [Option](https://lean-lang.org/doc/reference/latest/Basic-Types/Optional-Values/#Option___none) [Char]](#manual-Char___mk)



[String.Slice.Pos.get?]](#manual-String___Slice___Pos___get___-next) {s : [String.Slice]](#manual-String___Slice___mk)}
  (pos : s.[Pos]](#manual-String___Slice___Pos___mk)) : [Option](https://lean-lang.org/doc/reference/latest/Basic-Types/Optional-Values/#Option___none) [Char]](#manual-Char___mk)
```

Returns the byte at the given position in the string, or `[none](https://lean-lang.org/doc/reference/latest/Basic-Types/Optional-Values/#Option___none)` if the position is the end
position.

###### 20.8.4.11.3.2. Incrementing and Decrementing {#manual-The-Lean-Language-Reference--Basic-Types--Strings--API-Reference--String-Slices--Positions--Incrementing-and-Decrementing}

def

```lean
[String.Slice.Pos.prev]](#manual-String___Slice___Pos___prev) {s : [String.Slice]](#manual-String___Slice___mk)} (pos : s.[Pos]](#manual-String___Slice___Pos___mk))
  (h : pos ≠ s.[startPos]](#manual-String___Slice___startPos)) : s.[Pos]](#manual-String___Slice___Pos___mk)



[String.Slice.Pos.prev]](#manual-String___Slice___Pos___prev) {s : [String.Slice]](#manual-String___Slice___mk)}
  (pos : s.[Pos]](#manual-String___Slice___Pos___mk)) (h : pos ≠ s.[startPos]](#manual-String___Slice___startPos)) :
  s.[Pos]](#manual-String___Slice___Pos___mk)
```

Returns the previous valid position before the given position, given a proof that the position
is not the start position, which guarantees that such a position exists.

def

```lean
[String.Slice.Pos.prev!]](#manual-String___Slice___Pos___prev___) {s : [String.Slice]](#manual-String___Slice___mk)} (pos : s.[Pos]](#manual-String___Slice___Pos___mk)) : s.[Pos]](#manual-String___Slice___Pos___mk)



[String.Slice.Pos.prev!]](#manual-String___Slice___Pos___prev___) {s : [String.Slice]](#manual-String___Slice___mk)}
  (pos : s.[Pos]](#manual-String___Slice___Pos___mk)) : s.[Pos]](#manual-String___Slice___Pos___mk)
```

Returns the previous valid position before the given position, or panics if the position is
the start position.

def

```lean
[String.Slice.Pos.prev?]](#manual-String___Slice___Pos___prev___-next) {s : [String.Slice]](#manual-String___Slice___mk)} (pos : s.[Pos]](#manual-String___Slice___Pos___mk)) : [Option](https://lean-lang.org/doc/reference/latest/Basic-Types/Optional-Values/#Option___none) s.[Pos]](#manual-String___Slice___Pos___mk)



[String.Slice.Pos.prev?]](#manual-String___Slice___Pos___prev___-next) {s : [String.Slice]](#manual-String___Slice___mk)}
  (pos : s.[Pos]](#manual-String___Slice___Pos___mk)) : [Option](https://lean-lang.org/doc/reference/latest/Basic-Types/Optional-Values/#Option___none) s.[Pos]](#manual-String___Slice___Pos___mk)
```

Returns the previous valid position before the given position, or `[none](https://lean-lang.org/doc/reference/latest/Basic-Types/Optional-Values/#Option___none)` if the position is
the start position.

def

```lean
[String.Slice.Pos.prevn]](#manual-String___Slice___Pos___prevn) {s : [String.Slice]](#manual-String___Slice___mk)} (p : s.[Pos]](#manual-String___Slice___Pos___mk)) (n : [Nat]](#manual-Nat___zero)) : s.[Pos]](#manual-String___Slice___Pos___mk)



[String.Slice.Pos.prevn]](#manual-String___Slice___Pos___prevn) {s : [String.Slice]](#manual-String___Slice___mk)}
  (p : s.[Pos]](#manual-String___Slice___Pos___mk)) (n : [Nat]](#manual-Nat___zero)) : s.[Pos]](#manual-String___Slice___Pos___mk)
```

Iterates `p.[prev]](#manual-String___Slice___Pos___prev)` `n` times.

If this would move `p` past the start of `s`, the result is `s.[endPos]](#manual-String___Slice___endPos)`.

def

```lean
[String.Slice.Pos.next]](#manual-String___Slice___Pos___next) {s : [String.Slice]](#manual-String___Slice___mk)} (pos : s.[Pos]](#manual-String___Slice___Pos___mk))
  (h : pos ≠ s.[endPos]](#manual-String___Slice___endPos)) : s.[Pos]](#manual-String___Slice___Pos___mk)



[String.Slice.Pos.next]](#manual-String___Slice___Pos___next) {s : [String.Slice]](#manual-String___Slice___mk)}
  (pos : s.[Pos]](#manual-String___Slice___Pos___mk)) (h : pos ≠ s.[endPos]](#manual-String___Slice___endPos)) :
  s.[Pos]](#manual-String___Slice___Pos___mk)
```

Advances a valid position on a slice to the next valid position, given a proof that the
position is not the past-the-end position, which guarantees that such a position exists.

def

```lean
[String.Slice.Pos.next!]](#manual-String___Slice___Pos___next___) {s : [String.Slice]](#manual-String___Slice___mk)} (pos : s.[Pos]](#manual-String___Slice___Pos___mk)) : s.[Pos]](#manual-String___Slice___Pos___mk)



[String.Slice.Pos.next!]](#manual-String___Slice___Pos___next___) {s : [String.Slice]](#manual-String___Slice___mk)}
  (pos : s.[Pos]](#manual-String___Slice___Pos___mk)) : s.[Pos]](#manual-String___Slice___Pos___mk)
```

Advances a valid position on a slice to the next valid position, or panics if the given
position is the past-the-end position.

def

```lean
[String.Slice.Pos.next?]](#manual-String___Slice___Pos___next___-next) {s : [String.Slice]](#manual-String___Slice___mk)} (pos : s.[Pos]](#manual-String___Slice___Pos___mk)) : [Option](https://lean-lang.org/doc/reference/latest/Basic-Types/Optional-Values/#Option___none) s.[Pos]](#manual-String___Slice___Pos___mk)



[String.Slice.Pos.next?]](#manual-String___Slice___Pos___next___-next) {s : [String.Slice]](#manual-String___Slice___mk)}
  (pos : s.[Pos]](#manual-String___Slice___Pos___mk)) : [Option](https://lean-lang.org/doc/reference/latest/Basic-Types/Optional-Values/#Option___none) s.[Pos]](#manual-String___Slice___Pos___mk)
```

Advances a valid position on a slice to the next valid position, or returns `[none](https://lean-lang.org/doc/reference/latest/Basic-Types/Optional-Values/#Option___none)` if the
given position is the past-the-end position.

def

```lean
[String.Slice.Pos.nextn]](#manual-String___Slice___Pos___nextn) {s : [String.Slice]](#manual-String___Slice___mk)} (p : s.[Pos]](#manual-String___Slice___Pos___mk)) (n : [Nat]](#manual-Nat___zero)) : s.[Pos]](#manual-String___Slice___Pos___mk)



[String.Slice.Pos.nextn]](#manual-String___Slice___Pos___nextn) {s : [String.Slice]](#manual-String___Slice___mk)}
  (p : s.[Pos]](#manual-String___Slice___Pos___mk)) (n : [Nat]](#manual-Nat___zero)) : s.[Pos]](#manual-String___Slice___Pos___mk)
```

Advances the position `p` `n` times.

If this would move `p` past the end of `s`, the result is `s.[endPos]](#manual-String___Slice___endPos)`.

###### 20.8.4.11.3.3. Other Strings or Slices {#manual-The-Lean-Language-Reference--Basic-Types--Strings--API-Reference--String-Slices--Positions--Other-Strings-or-Slices}

def

```lean
[String.Slice.Pos.cast]](#manual-String___Slice___Pos___cast) {s t : [String.Slice]](#manual-String___Slice___mk)} (pos : s.[Pos]](#manual-String___Slice___Pos___mk))
  (h : s.[copy]](#manual-String___Slice___copy) [=]](#manual-Eq___refl) t.[copy]](#manual-String___Slice___copy)) : t.[Pos]](#manual-String___Slice___Pos___mk)



[String.Slice.Pos.cast]](#manual-String___Slice___Pos___cast) {s t : [String.Slice]](#manual-String___Slice___mk)}
  (pos : s.[Pos]](#manual-String___Slice___Pos___mk)) (h : s.[copy]](#manual-String___Slice___copy) [=]](#manual-Eq___refl) t.[copy]](#manual-String___Slice___copy)) :
  t.[Pos]](#manual-String___Slice___Pos___mk)
```

Constructs a valid position on `t` from a valid position on `s` and a proof that
`s.[copy]](#manual-String___Slice___copy) = t.[copy]](#manual-String___Slice___copy)`.

def

```lean
[String.Slice.Pos.ofSlice]](#manual-String___Slice___Pos___ofSlice) {s : [String.Slice]](#manual-String___Slice___mk)} {p₀ p₁ : s.[Pos]](#manual-String___Slice___Pos___mk)}
  {h : p₀ [≤]](#manual-LE___mk) p₁} (pos : (s.[slice]](#manual-String___Slice___slice) p₀ p₁ h).[Pos]](#manual-String___Slice___Pos___mk)) : s.[Pos]](#manual-String___Slice___Pos___mk)



[String.Slice.Pos.ofSlice]](#manual-String___Slice___Pos___ofSlice)
  {s : [String.Slice]](#manual-String___Slice___mk)} {p₀ p₁ : s.[Pos]](#manual-String___Slice___Pos___mk)}
  {h : p₀ [≤]](#manual-LE___mk) p₁}
  (pos : (s.[slice]](#manual-String___Slice___slice) p₀ p₁ h).[Pos]](#manual-String___Slice___Pos___mk)) : s.[Pos]](#manual-String___Slice___Pos___mk)
```

Given a position in `s.[slice]](#manual-String___Slice___slice) p₀ p₁ h`, obtain the corresponding position in `s`.

def

```lean
[String.Slice.Pos.str]](#manual-String___Slice___Pos___str) {s : [String.Slice]](#manual-String___Slice___mk)} (pos : s.[Pos]](#manual-String___Slice___Pos___mk)) : s.[str]](#manual-String___Slice___mk).[Pos]](#manual-String___Pos___mk)



[String.Slice.Pos.str]](#manual-String___Slice___Pos___str) {s : [String.Slice]](#manual-String___Slice___mk)}
  (pos : s.[Pos]](#manual-String___Slice___Pos___mk)) : s.[str]](#manual-String___Slice___mk).[Pos]](#manual-String___Pos___mk)
```

Given a valid position on a slice `s`, obtains the corresponding valid position on the
underlying string `s.[str]](#manual-String___Slice___mk)`.

def

```lean
[String.Slice.Pos.copy]](#manual-String___Slice___Pos___copy) {s : [String.Slice]](#manual-String___Slice___mk)} (pos : s.[Pos]](#manual-String___Slice___Pos___mk)) : s.[copy]](#manual-String___Slice___copy).[Pos]](#manual-String___Pos___mk)



[String.Slice.Pos.copy]](#manual-String___Slice___Pos___copy) {s : [String.Slice]](#manual-String___Slice___mk)}
  (pos : s.[Pos]](#manual-String___Slice___Pos___mk)) : s.[copy]](#manual-String___Slice___copy).[Pos]](#manual-String___Pos___mk)
```

Given a slice `s` and a position on `s`, obtain the corresponding position on `s.[copy]](#manual-String___Slice___copy).`

def

```lean
[String.Slice.Pos.ofSliceFrom]](#manual-String___Slice___Pos___ofSliceFrom) {s : [String.Slice]](#manual-String___Slice___mk)} {p₀ : s.[Pos]](#manual-String___Slice___Pos___mk)}
  (pos : (s.[sliceFrom]](#manual-String___Slice___sliceFrom) p₀).[Pos]](#manual-String___Slice___Pos___mk)) : s.[Pos]](#manual-String___Slice___Pos___mk)



[String.Slice.Pos.ofSliceFrom]](#manual-String___Slice___Pos___ofSliceFrom)
  {s : [String.Slice]](#manual-String___Slice___mk)} {p₀ : s.[Pos]](#manual-String___Slice___Pos___mk)}
  (pos : (s.[sliceFrom]](#manual-String___Slice___sliceFrom) p₀).[Pos]](#manual-String___Slice___Pos___mk)) : s.[Pos]](#manual-String___Slice___Pos___mk)
```

Given a position in `s.[sliceFrom]](#manual-String___Slice___sliceFrom) p₀`, obtain the corresponding position in `s`.

def

```lean
[String.Slice.Pos.ofSliceTo]](#manual-String___Slice___Pos___ofSliceTo) {s : [String.Slice]](#manual-String___Slice___mk)} {p₀ : s.[Pos]](#manual-String___Slice___Pos___mk)}
  (pos : (s.[sliceTo]](#manual-String___Slice___sliceTo) p₀).[Pos]](#manual-String___Slice___Pos___mk)) : s.[Pos]](#manual-String___Slice___Pos___mk)



[String.Slice.Pos.ofSliceTo]](#manual-String___Slice___Pos___ofSliceTo)
  {s : [String.Slice]](#manual-String___Slice___mk)} {p₀ : s.[Pos]](#manual-String___Slice___Pos___mk)}
  (pos : (s.[sliceTo]](#manual-String___Slice___sliceTo) p₀).[Pos]](#manual-String___Slice___Pos___mk)) : s.[Pos]](#manual-String___Slice___Pos___mk)
```

Given a position in `s.[sliceTo]](#manual-String___Slice___sliceTo) p₀`, obtain the corresponding position in `s`.

#### 20.8.4.12. Raw Substrings {#manual-string-api-substring}

Raw substrings are a low-level type that groups a string together with byte positions that delimit a region in the string.
Most code should use [slices]](#manual-string-api-slice) instead, because they are safer and more convenient.

def

```lean
[String.toRawSubstring]](#manual-String___toRawSubstring) (s : [String]](#manual-String___ofByteArray)) : [Substring.Raw]](#manual-Substring___Raw___mk)



[String.toRawSubstring]](#manual-String___toRawSubstring) (s : [String]](#manual-String___ofByteArray)) :
  [Substring.Raw]](#manual-Substring___Raw___mk)
```

Converts a `[String]](#manual-String___ofByteArray)` into a `Substring` that denotes the entire string.

def

```lean
[String.toRawSubstring']](#manual-String___toRawSubstring___) (s : [String]](#manual-String___ofByteArray)) : [Substring.Raw]](#manual-Substring___Raw___mk)



[String.toRawSubstring']](#manual-String___toRawSubstring___) (s : [String]](#manual-String___ofByteArray)) :
  [Substring.Raw]](#manual-Substring___Raw___mk)
```

Converts a `[String]](#manual-String___ofByteArray)` into a `Substring` that denotes the entire string.

This is a version of `[String.toRawSubstring]](#manual-String___toRawSubstring)` that doesn't have an `@[inline]` annotation.

structure

```lean
[Substring.Raw]](#manual-Substring___Raw___mk) : Type



[Substring.Raw]](#manual-Substring___Raw___mk) : Type
```

A region or slice of some underlying string.

A substring contains a string together with the start and end byte positions of a region of
interest. Actually extracting a substring requires copying and memory allocation, while many
substrings of the same underlying string may exist with very little overhead, and they are more
convenient than tracking the bounds by hand.

Using its constructor explicitly, it is possible to construct a `Substring` in which one or both of
the positions is invalid for the string. Many operations will return unexpected or confusing results
if the start and stop positions are not valid. For this reason, `Substring` will be deprecated in
favor of `[String.Slice]](#manual-String___Slice___mk)`, which always represents a valid substring.

Constructor

```lean
[Substring.Raw.mk]](#manual-Substring___Raw___mk)
```

Fields

```lean
str : [String]](#manual-String___ofByteArray)
```

The underlying string.

```lean
startPos : [String.Pos.Raw]](#manual-String___Pos___Raw___mk)
```

The byte position of the start of the string slice.

```lean
stopPos : [String.Pos.Raw]](#manual-String___Pos___Raw___mk)
```

The byte position of the end of the string slice.

##### 20.8.4.12.1. Properties {#manual-The-Lean-Language-Reference--Basic-Types--Strings--API-Reference--Raw-Substrings--Properties}

def

```lean
[Substring.Raw.isEmpty]](#manual-Substring___Raw___isEmpty) (ss : [Substring.Raw]](#manual-Substring___Raw___mk)) : [Bool](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)



[Substring.Raw.isEmpty]](#manual-Substring___Raw___isEmpty)
  (ss : [Substring.Raw]](#manual-Substring___Raw___mk)) : [Bool](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)
```

Checks whether a substring is empty.

A substring is empty if its start and end positions are the same.

def

```lean
[Substring.Raw.bsize]](#manual-Substring___Raw___bsize) : [Substring.Raw]](#manual-Substring___Raw___mk) → [Nat]](#manual-Nat___zero)



[Substring.Raw.bsize]](#manual-Substring___Raw___bsize) : [Substring.Raw]](#manual-Substring___Raw___mk) → [Nat]](#manual-Nat___zero)
```

The number of bytes used by the string's UTF-8 encoding.

##### 20.8.4.12.2. Positions {#manual-The-Lean-Language-Reference--Basic-Types--Strings--API-Reference--Raw-Substrings--Positions}

def

```lean
[Substring.Raw.atEnd]](#manual-Substring___Raw___atEnd) : [Substring.Raw]](#manual-Substring___Raw___mk) → [String.Pos.Raw]](#manual-String___Pos___Raw___mk) → [Bool](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)



[Substring.Raw.atEnd]](#manual-Substring___Raw___atEnd) :
  [Substring.Raw]](#manual-Substring___Raw___mk) → [String.Pos.Raw]](#manual-String___Pos___Raw___mk) → [Bool](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)
```

Checks whether a position in a substring is precisely equal to its ending position.

The position is understood relative to the substring's starting position, rather than the underlying
string's starting position.

def

```lean
[Substring.Raw.posOf]](#manual-Substring___Raw___posOf) (s : [Substring.Raw]](#manual-Substring___Raw___mk)) (c : [Char]](#manual-Char___mk)) : [String.Pos.Raw]](#manual-String___Pos___Raw___mk)



[Substring.Raw.posOf]](#manual-Substring___Raw___posOf) (s : [Substring.Raw]](#manual-Substring___Raw___mk))
  (c : [Char]](#manual-Char___mk)) : [String.Pos.Raw]](#manual-String___Pos___Raw___mk)
```

Returns the substring-relative position of the first occurrence of `c` in `s`, or `s.[bsize]](#manual-Substring___Raw___bsize)` if `c`
doesn't occur.

def

```lean
[Substring.Raw.next]](#manual-Substring___Raw___next) : [Substring.Raw]](#manual-Substring___Raw___mk) → [String.Pos.Raw]](#manual-String___Pos___Raw___mk) → [String.Pos.Raw]](#manual-String___Pos___Raw___mk)



[Substring.Raw.next]](#manual-Substring___Raw___next) :
  [Substring.Raw]](#manual-Substring___Raw___mk) →
    [String.Pos.Raw]](#manual-String___Pos___Raw___mk) → [String.Pos.Raw]](#manual-String___Pos___Raw___mk)
```

Returns the next position in a substring after the given position. If the position is at the end of
the substring, it is returned unmodified.

Both the input position and the returned position are interpreted relative to the substring's start
position, not the underlying string.

def

```lean
[Substring.Raw.nextn]](#manual-Substring___Raw___nextn) :
  [Substring.Raw]](#manual-Substring___Raw___mk) → [Nat]](#manual-Nat___zero) → [String.Pos.Raw]](#manual-String___Pos___Raw___mk) → [String.Pos.Raw]](#manual-String___Pos___Raw___mk)



[Substring.Raw.nextn]](#manual-Substring___Raw___nextn) :
  [Substring.Raw]](#manual-Substring___Raw___mk) →
    [Nat]](#manual-Nat___zero) → [String.Pos.Raw]](#manual-String___Pos___Raw___mk) → [String.Pos.Raw]](#manual-String___Pos___Raw___mk)
```

Returns the position that's the specified number of characters forward from the given position in a
substring. If the end position of the substring is reached, it is returned.

Both the input position and the returned position are interpreted relative to the substring's start
position, not the underlying string.

def

```lean
[Substring.Raw.prev]](#manual-Substring___Raw___prev) : [Substring.Raw]](#manual-Substring___Raw___mk) → [String.Pos.Raw]](#manual-String___Pos___Raw___mk) → [String.Pos.Raw]](#manual-String___Pos___Raw___mk)



[Substring.Raw.prev]](#manual-Substring___Raw___prev) :
  [Substring.Raw]](#manual-Substring___Raw___mk) →
    [String.Pos.Raw]](#manual-String___Pos___Raw___mk) → [String.Pos.Raw]](#manual-String___Pos___Raw___mk)
```

Returns the previous position in a substring, just prior to the given position. If the position is
at the beginning of the substring, it is returned unmodified.

Both the input position and the returned position are interpreted relative to the substring's start
position, not the underlying string.

def

```lean
[Substring.Raw.prevn]](#manual-Substring___Raw___prevn) :
  [Substring.Raw]](#manual-Substring___Raw___mk) → [Nat]](#manual-Nat___zero) → [String.Pos.Raw]](#manual-String___Pos___Raw___mk) → [String.Pos.Raw]](#manual-String___Pos___Raw___mk)



[Substring.Raw.prevn]](#manual-Substring___Raw___prevn) :
  [Substring.Raw]](#manual-Substring___Raw___mk) →
    [Nat]](#manual-Nat___zero) → [String.Pos.Raw]](#manual-String___Pos___Raw___mk) → [String.Pos.Raw]](#manual-String___Pos___Raw___mk)
```

Returns the position that's the specified number of characters prior to the given position in a
substring. If the start position of the substring is reached, it is returned.

Both the input position and the returned position are interpreted relative to the substring's start
position, not the underlying string.

##### 20.8.4.12.3. Folds and Aggregation {#manual-The-Lean-Language-Reference--Basic-Types--Strings--API-Reference--Raw-Substrings--Folds-and-Aggregation}

def

```lean
[Substring.Raw.foldl.{u}]](#manual-Substring___Raw___foldl) {α : Type u} (f : α → [Char]](#manual-Char___mk) → α) (init : α)
  (s : [Substring.Raw]](#manual-Substring___Raw___mk)) : α



[Substring.Raw.foldl.{u}]](#manual-Substring___Raw___foldl) {α : Type u}
  (f : α → [Char]](#manual-Char___mk) → α) (init : α)
  (s : [Substring.Raw]](#manual-Substring___Raw___mk)) : α
```

Folds a function over a substring from the left, accumulating a value starting with `init`. The
accumulated value is combined with each character in order, using `f`.

def

```lean
[Substring.Raw.foldr.{u}]](#manual-Substring___Raw___foldr) {α : Type u} (f : [Char]](#manual-Char___mk) → α → α) (init : α)
  (s : [Substring.Raw]](#manual-Substring___Raw___mk)) : α



[Substring.Raw.foldr.{u}]](#manual-Substring___Raw___foldr) {α : Type u}
  (f : [Char]](#manual-Char___mk) → α → α) (init : α)
  (s : [Substring.Raw]](#manual-Substring___Raw___mk)) : α
```

Folds a function over a substring from the right, accumulating a value starting with `init`. The
accumulated value is combined with each character in reverse order, using `f`.

def

```lean
[Substring.Raw.all]](#manual-Substring___Raw___all) (s : [Substring.Raw]](#manual-Substring___Raw___mk)) (p : [Char]](#manual-Char___mk) → [Bool](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)) : [Bool](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)



[Substring.Raw.all]](#manual-Substring___Raw___all) (s : [Substring.Raw]](#manual-Substring___Raw___mk))
  (p : [Char]](#manual-Char___mk) → [Bool](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)) : [Bool](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)
```

Checks whether the Boolean predicate `p` returns `[true](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)` for every character in a substring.

Short-circuits at the first character for which `p` returns `[false](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)`.

def

```lean
[Substring.Raw.any]](#manual-Substring___Raw___any) (s : [Substring.Raw]](#manual-Substring___Raw___mk)) (p : [Char]](#manual-Char___mk) → [Bool](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)) : [Bool](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)



[Substring.Raw.any]](#manual-Substring___Raw___any) (s : [Substring.Raw]](#manual-Substring___Raw___mk))
  (p : [Char]](#manual-Char___mk) → [Bool](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)) : [Bool](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)
```

Checks whether the Boolean predicate `p` returns `[true](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)` for any character in a substring.

Short-circuits at the first character for which `p` returns `[true](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)`.

##### 20.8.4.12.4. Comparisons {#manual-The-Lean-Language-Reference--Basic-Types--Strings--API-Reference--Raw-Substrings--Comparisons}

def

```lean
[Substring.Raw.beq]](#manual-Substring___Raw___beq) (ss1 ss2 : [Substring.Raw]](#manual-Substring___Raw___mk)) : [Bool](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)



[Substring.Raw.beq]](#manual-Substring___Raw___beq)
  (ss1 ss2 : [Substring.Raw]](#manual-Substring___Raw___mk)) : [Bool](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)
```

Checks whether two substrings represent equal strings. Usually accessed via the `==` operator.

Two substrings do not need to have the same underlying string or the same start and end positions;
instead, they are equal if they contain the same sequence of characters.

def

```lean
[Substring.Raw.sameAs]](#manual-Substring___Raw___sameAs) (ss1 ss2 : [Substring.Raw]](#manual-Substring___Raw___mk)) : [Bool](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)



[Substring.Raw.sameAs]](#manual-Substring___Raw___sameAs)
  (ss1 ss2 : [Substring.Raw]](#manual-Substring___Raw___mk)) : [Bool](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)
```

Checks whether two substrings have the same position and content.

The two substrings do not need to have the same underlying string for this check to succeed.

##### 20.8.4.12.5. Prefix and Suffix {#manual-The-Lean-Language-Reference--Basic-Types--Strings--API-Reference--Raw-Substrings--Prefix-and-Suffix}

def

```lean
[Substring.Raw.commonPrefix]](#manual-Substring___Raw___commonPrefix) (s t : [Substring.Raw]](#manual-Substring___Raw___mk)) : [Substring.Raw]](#manual-Substring___Raw___mk)



[Substring.Raw.commonPrefix]](#manual-Substring___Raw___commonPrefix)
  (s t : [Substring.Raw]](#manual-Substring___Raw___mk)) : [Substring.Raw]](#manual-Substring___Raw___mk)
```

Returns the longest common prefix of two substrings.

The returned substring uses the same underlying string as `s`.

def

```lean
[Substring.Raw.commonSuffix]](#manual-Substring___Raw___commonSuffix) (s t : [Substring.Raw]](#manual-Substring___Raw___mk)) : [Substring.Raw]](#manual-Substring___Raw___mk)



[Substring.Raw.commonSuffix]](#manual-Substring___Raw___commonSuffix)
  (s t : [Substring.Raw]](#manual-Substring___Raw___mk)) : [Substring.Raw]](#manual-Substring___Raw___mk)
```

Returns the longest common suffix of two substrings.

The returned substring uses the same underlying string as `s`.

def

```lean
[Substring.Raw.dropPrefix?]](#manual-Substring___Raw___dropPrefix___) (s pre : [Substring.Raw]](#manual-Substring___Raw___mk)) : [Option](https://lean-lang.org/doc/reference/latest/Basic-Types/Optional-Values/#Option___none) [Substring.Raw]](#manual-Substring___Raw___mk)



[Substring.Raw.dropPrefix?]](#manual-Substring___Raw___dropPrefix___)
  (s pre : [Substring.Raw]](#manual-Substring___Raw___mk)) :
  [Option](https://lean-lang.org/doc/reference/latest/Basic-Types/Optional-Values/#Option___none) [Substring.Raw]](#manual-Substring___Raw___mk)
```

If `pre` is a prefix of `s`, returns the remainder. Returns `[none](https://lean-lang.org/doc/reference/latest/Basic-Types/Optional-Values/#Option___none)` otherwise.

The substring `pre` is a prefix of `s` if there exists a `t : Substring` such that
`s.[toString]](#manual-Substring___Raw___toString) = pre.[toString]](#manual-Substring___Raw___toString) ++ t.toString`. If so, the result is the substring of `s` without the
prefix.

def

```lean
[Substring.Raw.dropSuffix?]](#manual-Substring___Raw___dropSuffix___) (s suff : [Substring.Raw]](#manual-Substring___Raw___mk)) :
  [Option](https://lean-lang.org/doc/reference/latest/Basic-Types/Optional-Values/#Option___none) [Substring.Raw]](#manual-Substring___Raw___mk)



[Substring.Raw.dropSuffix?]](#manual-Substring___Raw___dropSuffix___)
  (s suff : [Substring.Raw]](#manual-Substring___Raw___mk)) :
  [Option](https://lean-lang.org/doc/reference/latest/Basic-Types/Optional-Values/#Option___none) [Substring.Raw]](#manual-Substring___Raw___mk)
```

If `suff` is a suffix of `s`, returns the remainder. Returns `[none](https://lean-lang.org/doc/reference/latest/Basic-Types/Optional-Values/#Option___none)` otherwise.

The substring `suff` is a suffix of `s` if there exists a `t : Substring` such that
`s.[toString]](#manual-Substring___Raw___toString) = t.toString ++ suff.[toString]](#manual-Substring___Raw___toString)`. If so, the result the substring of `s` without the
suffix.

##### 20.8.4.12.6. Lookups {#manual-The-Lean-Language-Reference--Basic-Types--Strings--API-Reference--Raw-Substrings--Lookups}

def

```lean
[Substring.Raw.get]](#manual-Substring___Raw___get) : [Substring.Raw]](#manual-Substring___Raw___mk) → [String.Pos.Raw]](#manual-String___Pos___Raw___mk) → [Char]](#manual-Char___mk)



[Substring.Raw.get]](#manual-Substring___Raw___get) :
  [Substring.Raw]](#manual-Substring___Raw___mk) → [String.Pos.Raw]](#manual-String___Pos___Raw___mk) → [Char]](#manual-Char___mk)
```

Returns the character at the given position in the substring.

The position is relative to the substring, rather than the underlying string, and no bounds checking
is performed with respect to the substring's end position. If the relative position is not a valid
position in the underlying string, the fallback value `([default]](#manual-Inhabited___mk) : [Char]](#manual-Char___mk))`, which is `'A'`, is
returned. Does not panic.

def

```lean
[Substring.Raw.contains]](#manual-Substring___Raw___contains) (s : [Substring.Raw]](#manual-Substring___Raw___mk)) (c : [Char]](#manual-Char___mk)) : [Bool](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)



[Substring.Raw.contains]](#manual-Substring___Raw___contains) (s : [Substring.Raw]](#manual-Substring___Raw___mk))
  (c : [Char]](#manual-Char___mk)) : [Bool](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)
```

Checks whether a substring contains the specified character.

def

```lean
[Substring.Raw.front]](#manual-Substring___Raw___front) (s : [Substring.Raw]](#manual-Substring___Raw___mk)) : [Char]](#manual-Char___mk)



[Substring.Raw.front]](#manual-Substring___Raw___front) (s : [Substring.Raw]](#manual-Substring___Raw___mk)) :
  [Char]](#manual-Char___mk)
```

Returns the first character in the substring.

If the substring is empty, but the substring's start position is a valid position in the underlying
string, then the character at the start position is returned. If the substring's start position is
not a valid position in the string, the fallback value `([default]](#manual-Inhabited___mk) : [Char]](#manual-Char___mk))`, which is `'A'`, is
returned. Does not panic.

##### 20.8.4.12.7. Modifications {#manual-The-Lean-Language-Reference--Basic-Types--Strings--API-Reference--Raw-Substrings--Modifications}

def

```lean
[Substring.Raw.drop]](#manual-Substring___Raw___drop) : [Substring.Raw]](#manual-Substring___Raw___mk) → [Nat]](#manual-Nat___zero) → [Substring.Raw]](#manual-Substring___Raw___mk)



[Substring.Raw.drop]](#manual-Substring___Raw___drop) :
  [Substring.Raw]](#manual-Substring___Raw___mk) → [Nat]](#manual-Nat___zero) → [Substring.Raw]](#manual-Substring___Raw___mk)
```

Removes the specified number of characters (Unicode code points) from the beginning of a substring
by advancing its start position.

If the substring's end position is reached, the start position is not advanced past it.

def

```lean
[Substring.Raw.dropWhile]](#manual-Substring___Raw___dropWhile) : [Substring.Raw]](#manual-Substring___Raw___mk) → ([Char]](#manual-Char___mk) → [Bool](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)) → [Substring.Raw]](#manual-Substring___Raw___mk)



[Substring.Raw.dropWhile]](#manual-Substring___Raw___dropWhile) :
  [Substring.Raw]](#manual-Substring___Raw___mk) →
    ([Char]](#manual-Char___mk) → [Bool](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)) → [Substring.Raw]](#manual-Substring___Raw___mk)
```

Removes the longest prefix of a substring in which a Boolean predicate returns `[true](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)` for all
characters by moving the substring's start position. The start position is moved to the position of
the first character for which the predicate returns `[false](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)`, or to the substring's end position if
the predicate always returns `[true](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)`.

def

```lean
[Substring.Raw.dropRight]](#manual-Substring___Raw___dropRight) : [Substring.Raw]](#manual-Substring___Raw___mk) → [Nat]](#manual-Nat___zero) → [Substring.Raw]](#manual-Substring___Raw___mk)



[Substring.Raw.dropRight]](#manual-Substring___Raw___dropRight) :
  [Substring.Raw]](#manual-Substring___Raw___mk) → [Nat]](#manual-Nat___zero) → [Substring.Raw]](#manual-Substring___Raw___mk)
```

Removes the specified number of characters (Unicode code points) from the end of a substring
by moving its end position towards its start position.

If the substring's start position is reached, the end position is not retracted past it.

def

```lean
[Substring.Raw.dropRightWhile]](#manual-Substring___Raw___dropRightWhile) :
  [Substring.Raw]](#manual-Substring___Raw___mk) → ([Char]](#manual-Char___mk) → [Bool](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)) → [Substring.Raw]](#manual-Substring___Raw___mk)



[Substring.Raw.dropRightWhile]](#manual-Substring___Raw___dropRightWhile) :
  [Substring.Raw]](#manual-Substring___Raw___mk) →
    ([Char]](#manual-Char___mk) → [Bool](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)) → [Substring.Raw]](#manual-Substring___Raw___mk)
```

Removes the longest suffix of a substring in which a Boolean predicate returns `[true](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)` for all
characters by moving the substring's end position. The end position is moved just after the position
of the last character for which the predicate returns `[false](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)`, or to the substring's start position
if the predicate always returns `[true](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)`.

def

```lean
[Substring.Raw.take]](#manual-Substring___Raw___take) : [Substring.Raw]](#manual-Substring___Raw___mk) → [Nat]](#manual-Nat___zero) → [Substring.Raw]](#manual-Substring___Raw___mk)



[Substring.Raw.take]](#manual-Substring___Raw___take) :
  [Substring.Raw]](#manual-Substring___Raw___mk) → [Nat]](#manual-Nat___zero) → [Substring.Raw]](#manual-Substring___Raw___mk)
```

Retains only the specified number of characters (Unicode code points) at the beginning of a
substring, by moving its end position towards its start position.

If the substring's start position is reached, the end position is not retracted past it.

def

```lean
[Substring.Raw.takeWhile]](#manual-Substring___Raw___takeWhile) : [Substring.Raw]](#manual-Substring___Raw___mk) → ([Char]](#manual-Char___mk) → [Bool](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)) → [Substring.Raw]](#manual-Substring___Raw___mk)



[Substring.Raw.takeWhile]](#manual-Substring___Raw___takeWhile) :
  [Substring.Raw]](#manual-Substring___Raw___mk) →
    ([Char]](#manual-Char___mk) → [Bool](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)) → [Substring.Raw]](#manual-Substring___Raw___mk)
```

Retains only the longest prefix of a substring in which a Boolean predicate returns `[true](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)` for all
characters by moving the substring's end position towards its start position.

def

```lean
[Substring.Raw.takeRight]](#manual-Substring___Raw___takeRight) : [Substring.Raw]](#manual-Substring___Raw___mk) → [Nat]](#manual-Nat___zero) → [Substring.Raw]](#manual-Substring___Raw___mk)



[Substring.Raw.takeRight]](#manual-Substring___Raw___takeRight) :
  [Substring.Raw]](#manual-Substring___Raw___mk) → [Nat]](#manual-Nat___zero) → [Substring.Raw]](#manual-Substring___Raw___mk)
```

Retains only the specified number of characters (Unicode code points) at the end of a substring, by
moving its start position towards its end position.

If the substring's end position is reached, the start position is not advanced past it.

def

```lean
[Substring.Raw.takeRightWhile]](#manual-Substring___Raw___takeRightWhile) :
  [Substring.Raw]](#manual-Substring___Raw___mk) → ([Char]](#manual-Char___mk) → [Bool](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)) → [Substring.Raw]](#manual-Substring___Raw___mk)



[Substring.Raw.takeRightWhile]](#manual-Substring___Raw___takeRightWhile) :
  [Substring.Raw]](#manual-Substring___Raw___mk) →
    ([Char]](#manual-Char___mk) → [Bool](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)) → [Substring.Raw]](#manual-Substring___Raw___mk)
```

Retains only the longest suffix of a substring in which a Boolean predicate returns `[true](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)` for all
characters by moving the substring's start position towards its end position.

def

```lean
[Substring.Raw.extract]](#manual-Substring___Raw___extract) :
  [Substring.Raw]](#manual-Substring___Raw___mk) → [String.Pos.Raw]](#manual-String___Pos___Raw___mk) → [String.Pos.Raw]](#manual-String___Pos___Raw___mk) → [Substring.Raw]](#manual-Substring___Raw___mk)



[Substring.Raw.extract]](#manual-Substring___Raw___extract) :
  [Substring.Raw]](#manual-Substring___Raw___mk) →
    [String.Pos.Raw]](#manual-String___Pos___Raw___mk) →
      [String.Pos.Raw]](#manual-String___Pos___Raw___mk) → [Substring.Raw]](#manual-Substring___Raw___mk)
```

Returns the region of the substring delimited by the provided start and stop positions, as a
substring. The positions are interpreted with respect to the substring's start position, rather than
the underlying string.

If the resulting substring is empty, then the resulting substring is a substring of the empty string
`""`. Otherwise, the underlying string is that of the input substring with the beginning and end
positions adjusted.

def

```lean
[Substring.Raw.trim]](#manual-Substring___Raw___trim) : [Substring.Raw]](#manual-Substring___Raw___mk) → [Substring.Raw]](#manual-Substring___Raw___mk)



[Substring.Raw.trim]](#manual-Substring___Raw___trim) :
  [Substring.Raw]](#manual-Substring___Raw___mk) → [Substring.Raw]](#manual-Substring___Raw___mk)
```

Removes leading and trailing whitespace from a substring by first moving its start position to the
first non-whitespace character, and then moving its end position to the last non-whitespace
character.

If the substring consists only of whitespace, then the resulting substring's start position is moved
to its end position.

“Whitespace” is defined as characters for which `[Char.isWhitespace]](#manual-Char___isWhitespace)` returns `[true](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)`.

Examples:

- `" red green blue ".[toRawSubstring]](#manual-String___toRawSubstring).[trim]](#manual-Substring___Raw___trim).[toString]](#manual-Substring___Raw___toString) = "red green blue"`
- `" red green blue ".[toRawSubstring]](#manual-String___toRawSubstring).[trim]](#manual-Substring___Raw___trim).[startPos]](#manual-Substring___Raw___mk) = ⟨1⟩`
- `" red green blue ".[toRawSubstring]](#manual-String___toRawSubstring).[trim]](#manual-Substring___Raw___trim).[stopPos]](#manual-Substring___Raw___mk) = ⟨15⟩`
- `" ".[toRawSubstring]](#manual-String___toRawSubstring).[trim]](#manual-Substring___Raw___trim).[startPos]](#manual-Substring___Raw___mk) = ⟨5⟩`

def

```lean
[Substring.Raw.trimLeft]](#manual-Substring___Raw___trimLeft) (s : [Substring.Raw]](#manual-Substring___Raw___mk)) : [Substring.Raw]](#manual-Substring___Raw___mk)



[Substring.Raw.trimLeft]](#manual-Substring___Raw___trimLeft)
  (s : [Substring.Raw]](#manual-Substring___Raw___mk)) : [Substring.Raw]](#manual-Substring___Raw___mk)
```

Removes leading whitespace from a substring by moving its start position to the first non-whitespace
character, or to its end position if there is no non-whitespace character.

“Whitespace” is defined as characters for which `[Char.isWhitespace]](#manual-Char___isWhitespace)` returns `[true](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)`.

def

```lean
[Substring.Raw.trimRight]](#manual-Substring___Raw___trimRight) (s : [Substring.Raw]](#manual-Substring___Raw___mk)) : [Substring.Raw]](#manual-Substring___Raw___mk)



[Substring.Raw.trimRight]](#manual-Substring___Raw___trimRight)
  (s : [Substring.Raw]](#manual-Substring___Raw___mk)) : [Substring.Raw]](#manual-Substring___Raw___mk)
```

Removes trailing whitespace from a substring by moving its end position to the last non-whitespace
character, or to its start position if there is no non-whitespace character.

“Whitespace” is defined as characters for which `[Char.isWhitespace]](#manual-Char___isWhitespace)` returns `[true](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)`.

def

```lean
[Substring.Raw.splitOn]](#manual-Substring___Raw___splitOn) (s : [Substring.Raw]](#manual-Substring___Raw___mk)) (sep : [String]](#manual-String___ofByteArray) := " ") :
  [List](https://lean-lang.org/doc/reference/latest/Basic-Types/Linked-Lists/#List___nil) [Substring.Raw]](#manual-Substring___Raw___mk)



[Substring.Raw.splitOn]](#manual-Substring___Raw___splitOn) (s : [Substring.Raw]](#manual-Substring___Raw___mk))
  (sep : [String]](#manual-String___ofByteArray) := " ") :
  [List](https://lean-lang.org/doc/reference/latest/Basic-Types/Linked-Lists/#List___nil) [Substring.Raw]](#manual-Substring___Raw___mk)
```

Splits a substring `s` on occurrences of the separator string `sep`. The default separator is `" "`.

When `sep` is empty, the result is `[s]`. When `sep` occurs in overlapping patterns, the first match
is taken. There will always be exactly `n+1` elements in the returned list if there were `n`
non-overlapping matches of `sep` in the string. The separators are not included in the returned
substrings, which are all substrings of `s`'s string.

def

```lean
[Substring.Raw.repair]](#manual-Substring___Raw___repair) : [Substring.Raw]](#manual-Substring___Raw___mk) → [Substring.Raw]](#manual-Substring___Raw___mk)



[Substring.Raw.repair]](#manual-Substring___Raw___repair) :
  [Substring.Raw]](#manual-Substring___Raw___mk) → [Substring.Raw]](#manual-Substring___Raw___mk)
```

Given a `Substring`, returns another one which has valid endpoints
and represents the same substring according to `Substring.toString`.
(Note, the substring may still be inverted, i.e. beginning greater than end.)

##### 20.8.4.12.8. Conversions {#manual-The-Lean-Language-Reference--Basic-Types--Strings--API-Reference--Raw-Substrings--Conversions}

def

```lean
[Substring.Raw.toString]](#manual-Substring___Raw___toString) : [Substring.Raw]](#manual-Substring___Raw___mk) → [String]](#manual-String___ofByteArray)



[Substring.Raw.toString]](#manual-Substring___Raw___toString) :
  [Substring.Raw]](#manual-Substring___Raw___mk) → [String]](#manual-String___ofByteArray)
```

{}
Copies the region of the underlying string pointed to by a substring into a fresh string.

def

```lean
[Substring.Raw.isNat]](#manual-Substring___Raw___isNat) (s : [Substring.Raw]](#manual-Substring___Raw___mk)) : [Bool](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)



[Substring.Raw.isNat]](#manual-Substring___Raw___isNat) (s : [Substring.Raw]](#manual-Substring___Raw___mk)) :
  [Bool](https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/#Bool___false)
```

Checks whether the substring can be interpreted as the decimal representation of a natural number.

A substring can be interpreted as a decimal natural number if it is not empty and all the characters
in it are digits. Underscores ({lit}`_`) are allowed as digit separators for readability, but cannot appear
at the start, at the end, or consecutively.

Use `Substring.toNat?` to convert such a substring to a natural number.

def

```lean
[Substring.Raw.toNat?]](#manual-Substring___Raw___toNat___) (s : [Substring.Raw]](#manual-Substring___Raw___mk)) : [Option](https://lean-lang.org/doc/reference/latest/Basic-Types/Optional-Values/#Option___none) [Nat]](#manual-Nat___zero)



[Substring.Raw.toNat?]](#manual-Substring___Raw___toNat___) (s : [Substring.Raw]](#manual-Substring___Raw___mk)) :
  [Option](https://lean-lang.org/doc/reference/latest/Basic-Types/Optional-Values/#Option___none) [Nat]](#manual-Nat___zero)
```

Checks whether the substring can be interpreted as the decimal representation of a natural number,
returning the number if it can.

A substring can be interpreted as a decimal natural number if it is not empty and all the characters
in it are digits. Underscores ({lit}`_`) are allowed as digit separators and are ignored during parsing.

Use `Substring.isNat` to check whether the substring is such a substring.

def

```lean
[Substring.Raw.toLegacyIterator]](#manual-Substring___Raw___toLegacyIterator) : [Substring.Raw]](#manual-Substring___Raw___mk) → [String.Legacy.Iterator]](#manual-String___Legacy___Iterator___mk)



[Substring.Raw.toLegacyIterator]](#manual-Substring___Raw___toLegacyIterator) :
  [Substring.Raw]](#manual-Substring___Raw___mk) → [String.Legacy.Iterator]](#manual-String___Legacy___Iterator___mk)
```

Returns an iterator into the underlying string, at the substring's starting position. The ending
position is discarded, so the iterator alone cannot be used to determine whether its current
position is within the original substring.

def

```lean
[Substring.Raw.toName]](#manual-Substring___Raw___toName) (s : [Substring.Raw]](#manual-Substring___Raw___mk)) : Lean.Name



[Substring.Raw.toName]](#manual-Substring___Raw___toName) (s : [Substring.Raw]](#manual-Substring___Raw___mk)) :
  Lean.Name
```

Converts a substring to the Lean compiler's representation of names. The resulting name is
hierarchical, and the string is split at the dots (`'.'`).

`"a.b".[toRawSubstring]](#manual-String___toRawSubstring).[toName]](#manual-Substring___Raw___toName)` is the name `a.b`, not `«a.b»`. For the latter, use
`Name.mkSimple ∘ [Substring.Raw.toString]](#manual-Substring___Raw___toString)`.
-- TODO: deprecate old name

#### 20.8.4.13. Metaprogramming {#manual-string-api-meta}

def

```lean
[String.toName]](#manual-String___toName) (s : [String]](#manual-String___ofByteArray)) : Lean.Name



[String.toName]](#manual-String___toName) (s : [String]](#manual-String___ofByteArray)) : Lean.Name
```

Converts a string to the Lean compiler's representation of names. The resulting name is
hierarchical, and the string is split at the dots (`'.'`).

`"a.b".[toName]](#manual-String___toName)` is the name `a.b`, not `«a.b»`. For the latter, use `Name.mkSimple`.

def

```lean
[String.quote]](#manual-String___quote) (s : [String]](#manual-String___ofByteArray)) : [String]](#manual-String___ofByteArray)



[String.quote]](#manual-String___quote) (s : [String]](#manual-String___ofByteArray)) : [String]](#manual-String___ofByteArray)
```

Converts a string to its corresponding Lean string literal syntax. Double quotes are added to each
end, and internal characters are escaped as needed.

Examples:

- `"abc".[quote]](#manual-String___quote) = "\"abc\""`
- `"\"".[quote]](#manual-String___quote) = "\"\\\"\""`

#### 20.8.4.14. Encodings {#manual-string-api-encoding}

def

```lean
[String.getUTF8Byte]](#manual-String___getUTF8Byte) (s : [String]](#manual-String___ofByteArray)) (p : [String.Pos.Raw]](#manual-String___Pos___Raw___mk))
  (h : p [<]](#manual-LT___mk) s.[rawEndPos]](#manual-String___rawEndPos)) : [UInt8]](#manual-UInt8___ofBitVec)



[String.getUTF8Byte]](#manual-String___getUTF8Byte) (s : [String]](#manual-String___ofByteArray))
  (p : [String.Pos.Raw]](#manual-String___Pos___Raw___mk))
  (h : p [<]](#manual-LT___mk) s.[rawEndPos]](#manual-String___rawEndPos)) : [UInt8]](#manual-UInt8___ofBitVec)
```

Accesses the indicated byte in the UTF-8 encoding of a string.

At runtime, this function is implemented by efficient, constant-time code.

def

```lean
[String.utf8ByteSize]](#manual-String___utf8ByteSize) (s : [String]](#manual-String___ofByteArray)) : [Nat]](#manual-Nat___zero)



[String.utf8ByteSize]](#manual-String___utf8ByteSize) (s : [String]](#manual-String___ofByteArray)) : [Nat]](#manual-Nat___zero)
```

The number of bytes used by the string's UTF-8 encoding.

At runtime, this function takes constant time because the byte length of strings is cached.

def

```lean
[String.utf8EncodeChar]](#manual-String___utf8EncodeChar) (c : [Char]](#manual-Char___mk)) : [List](https://lean-lang.org/doc/reference/latest/Basic-Types/Linked-Lists/#List___nil) [UInt8]](#manual-UInt8___ofBitVec)



[String.utf8EncodeChar]](#manual-String___utf8EncodeChar) (c : [Char]](#manual-Char___mk)) :
  [List](https://lean-lang.org/doc/reference/latest/Basic-Types/Linked-Lists/#List___nil) [UInt8]](#manual-UInt8___ofBitVec)
```

Returns the sequence of bytes in a character's UTF-8 encoding.

def

```lean
[String.fromUTF8]](#manual-String___fromUTF8) (a : [ByteArray](https://lean-lang.org/doc/reference/latest/Basic-Types/Byte-Arrays/#ByteArray___mk)) (h : a.IsValidUTF8) : [String]](#manual-String___ofByteArray)



[String.fromUTF8]](#manual-String___fromUTF8) (a : [ByteArray](https://lean-lang.org/doc/reference/latest/Basic-Types/Byte-Arrays/#ByteArray___mk))
  (h : a.IsValidUTF8) : [String]](#manual-String___ofByteArray)
```

Decodes an array of bytes that encode a string as [UTF-8](https://en.wikipedia.org/wiki/UTF-8) into
the corresponding string.

def

```lean
[String.fromUTF8?]](#manual-String___fromUTF8___) (a : [ByteArray](https://lean-lang.org/doc/reference/latest/Basic-Types/Byte-Arrays/#ByteArray___mk)) : [Option](https://lean-lang.org/doc/reference/latest/Basic-Types/Optional-Values/#Option___none) [String]](#manual-String___ofByteArray)



[String.fromUTF8?]](#manual-String___fromUTF8___) (a : [ByteArray](https://lean-lang.org/doc/reference/latest/Basic-Types/Byte-Arrays/#ByteArray___mk)) :
  [Option](https://lean-lang.org/doc/reference/latest/Basic-Types/Optional-Values/#Option___none) [String]](#manual-String___ofByteArray)
```

Decodes an array of bytes that encode a string as [UTF-8](https://en.wikipedia.org/wiki/UTF-8) into
the corresponding string, or returns `[none](https://lean-lang.org/doc/reference/latest/Basic-Types/Optional-Values/#Option___none)` if the array is not a valid UTF-8 encoding of a string.

def

```lean
[String.fromUTF8!]](#manual-String___fromUTF8___-next) (a : [ByteArray](https://lean-lang.org/doc/reference/latest/Basic-Types/Byte-Arrays/#ByteArray___mk)) : [String]](#manual-String___ofByteArray)



[String.fromUTF8!]](#manual-String___fromUTF8___-next) (a : [ByteArray](https://lean-lang.org/doc/reference/latest/Basic-Types/Byte-Arrays/#ByteArray___mk)) : [String]](#manual-String___ofByteArray)
```

Decodes an array of bytes that encode a string as [UTF-8](https://en.wikipedia.org/wiki/UTF-8) into
the corresponding string, or panics if the array is not a valid UTF-8 encoding of a string.

def

```lean
[String.toUTF8]](#manual-String___toUTF8) (a : [String]](#manual-String___ofByteArray)) : [ByteArray](https://lean-lang.org/doc/reference/latest/Basic-Types/Byte-Arrays/#ByteArray___mk)



[String.toUTF8]](#manual-String___toUTF8) (a : [String]](#manual-String___ofByteArray)) : [ByteArray](https://lean-lang.org/doc/reference/latest/Basic-Types/Byte-Arrays/#ByteArray___mk)
```

Encodes a string in UTF-8 as an array of bytes.

def

```lean
[String.crlfToLf]](#manual-String___crlfToLf) (text : [String]](#manual-String___ofByteArray)) : [String]](#manual-String___ofByteArray)



[String.crlfToLf]](#manual-String___crlfToLf) (text : [String]](#manual-String___ofByteArray)) : [String]](#manual-String___ofByteArray)
```

Replaces each `\r\n` with `\n` to normalize line endings, but does not validate that there are no
isolated `\r` characters.

This is an optimized version of `[String.replace]](#manual-String___replace) text "\r\n" "\n"`.

### 20.8.5. FFI {#manual-string-ffi}

FFI type

```
```
typedef struct {
    lean_object m_header;
    /* byte length including '\0' terminator */
    size_t      m_size;
    size_t      m_capacity;
    /* UTF8 length */
    size_t      m_length;
    char        m_data[0];
} lean_string_object;
```
```

The representation of strings in C. See [the description of run-time `[String]](#manual-String___ofByteArray)`s](https://lean-lang.org/doc/reference/latest/Basic-Types/Strings/#string-runtime) for more details.

FFI function

```
```
bool lean_is_string(lean_object * o)
```
```

Returns `true` if `o` is a string, or `false` otherwise.

FFI function

```
```
lean_string_object * lean_to_string(lean_object * o)
```
```

Performs a runtime check that `o` is indeed a string. If `o` is not a string, an assertion fails.

---



## Basic Types — 20.9. The Unit Type {#manual-basic-types-209-the-unit-type}

> 📄 Source: https://lean-lang.org/doc/reference/latest/Basic-Types/The-Unit-Type/

The unit type is the canonical type with exactly one element, named `[unit]](#manual-Unit___unit)` and represented by the empty tuple `()`.
It describes only a single value, which consists of said constructor applied to no arguments whatsoever.

`[Unit]](#manual-Unit)` is analogous to `void` in languages derived from C: even though `void` has no elements that can be named, it represents the return of control flow from a function with no additional information.
In functional programming, `[Unit]](#manual-Unit)` is the return type of things that “return nothing”.
Mathematically, this is represented by a single completely uninformative value, as opposed to an empty type such as `[Empty](https://lean-lang.org/doc/reference/latest/Basic-Types/The-Empty-Type/#Empty)`, which represents unreachable code.

When programming with [monads]](#manual-monads-and-do), `[Unit]](#manual-Unit)` is especially useful.
For any type `α`, `m α` represents an action that has side effects and returns a value of type `α`.
The type `m [Unit]](#manual-Unit)` represents an action that has some side effects but does not return a value.

There are two variants of the unit type:

- `[Unit]](#manual-Unit)` is a `Type` that exists in the smallest non-propositional [universe]](#manual---tech-term-universes).
- `[PUnit]](#manual-PUnit___unit)` is [universe polymorphic]](#manual---tech-term-universe-polymorphism) and can be used in any non-propositional [universe]](#manual---tech-term-universes).

Behind the scenes, `[Unit]](#manual-Unit)` is actually defined as `[PUnit]](#manual-PUnit___unit).{1}`.
`[Unit]](#manual-Unit)` should be preferred over `[PUnit]](#manual-PUnit___unit)` when possible to avoid unnecessary universe parameters.
If in doubt, use `[Unit]](#manual-Unit)` until universe errors occur.

def

```lean
[Unit]](#manual-Unit) : Type



[Unit]](#manual-Unit) : Type
```

The canonical type with one element. This element is written `()`.

`[Unit]](#manual-Unit)` has a number of uses:

- It can be used to model control flow that returns from a function call without providing other
  information.
- Monadic actions that return `[Unit]](#manual-Unit)` have side effects without computing values.
- In polymorphic types, it can be used to indicate that no data is to be stored in a particular
  field.

def

```lean
[Unit.unit]](#manual-Unit___unit) : [Unit]](#manual-Unit)



[Unit.unit]](#manual-Unit___unit) : [Unit]](#manual-Unit)
```

The only element of the unit type.

It can be written as an empty tuple: `()`.

inductive type

```lean
[PUnit.{u}]](#manual-PUnit___unit) : Sort u



[PUnit.{u}]](#manual-PUnit___unit) : Sort u
```

The canonical universe-polymorphic type with just one element.

It should be used in contexts that require a type to be universe polymorphic, thus disallowing
`[Unit]](#manual-Unit)`.

Constructors

```lean
[PUnit.unit.{u}]](#manual-PUnit___unit) : [PUnit]](#manual-PUnit___unit)
```

The only element of the universe-polymorphic unit type.

### 20.9.1. Definitional Equality {#manual-The-Lean-Language-Reference--Basic-Types--The-Unit-Type--Definitional-Equality}

*Unit-like types* are inductive types that have a single constructor which takes no non-proof parameters.
`[PUnit]](#manual-PUnit___unit)` is one such type.
All elements of unit-like types are [definitionally equal]](#manual---tech-term-definitional-equality) to all other elements.

**Example: Definitional Equality of Unit**

Every term with type `[Unit]](#manual-Unit)` is definitionally equal to every other term with type `[Unit]](#manual-Unit)`:

```lean
example (e1 e2 : [Unit]](#manual-Unit)) : e1 = e2 := [rfl]](#manual-rfl-next)
```

**Example: Definitional Equality of Unit-Like Types**

Both `[CustomUnit]](#manual-CustomUnit-_LPAR_in-Definitional-Equality-of-Unit-Like-Types_RPAR_)` and `[AlsoUnit]](#manual-AlsoUnit-_LPAR_in-Definitional-Equality-of-Unit-Like-Types_RPAR_)` are unit-like types, with a single constructor that takes no parameters.
Every pair of terms with either type is definitionally equal.

```lean
inductive CustomUnit where
| customUnit
example (e1 e2 : [CustomUnit]](#manual-CustomUnit-_LPAR_in-Definitional-Equality-of-Unit-Like-Types_RPAR_)) : e1 = e2 := [rfl]](#manual-rfl-next)
structure AlsoUnit where
example (e1 e2 : [AlsoUnit]](#manual-AlsoUnit-_LPAR_in-Definitional-Equality-of-Unit-Like-Types_RPAR_)) : e1 = e2 := [rfl]](#manual-rfl-next)
```

Types with parameters, such as `[WithParam]](#manual-WithParam-_LPAR_in-Definitional-Equality-of-Unit-Like-Types_RPAR_)`, are also unit-like if they have a single constructor that does not take parameters.

```lean
inductive WithParam (n : [Nat]](#manual-Nat___zero)) where
| mk
example (x y : [WithParam]](#manual-WithParam-_LPAR_in-Definitional-Equality-of-Unit-Like-Types_RPAR_) 3) : x = y := [rfl]](#manual-rfl-next)
```

Constructors with non-proof parameters are not unit-like, even if the parameters are all unit-like types.

```lean
inductive NotUnitLike where
| mk (u : [Unit]](#manual-Unit))
```

```lean
example (e1 e2 : [NotUnitLike]](#manual-NotUnitLike-_LPAR_in-Definitional-Equality-of-Unit-Like-Types_RPAR_)) : e1 = e2 := [rfl]](#manual-rfl-next)
```

```lean
Type mismatch
  [rfl]](#manual-rfl-next)
has type
  ?m.3 [=]](#manual-Eq___refl) ?m.3
but is expected to have type
  e1 [=]](#manual-Eq___refl) e2
```

Constructors of unit-like types may take parameters that are proofs.

```lean
inductive ProofUnitLike where
| mk : 2 = 2 → [ProofUnitLike]](#manual-ProofUnitLike-_LPAR_in-Definitional-Equality-of-Unit-Like-Types_RPAR_)
example (e1 e2 : [ProofUnitLike]](#manual-ProofUnitLike-_LPAR_in-Definitional-Equality-of-Unit-Like-Types_RPAR_)) : e1 = e2 := [rfl]](#manual-rfl-next)
```

---



## Basic Types — 20.10. The Empty Type {#manual-basic-types-2010-the-empty-type}

> 📄 Source: https://lean-lang.org/doc/reference/latest/Basic-Types/The-Empty-Type/

The empty type `[Empty]](#manual-Empty)` represents impossible values.
It is an inductive type with no constructors whatsoever.

While the trivial type `[Unit]](#manual-Unit)`, which has a single constructor that takes no parameters, can be used to model computations where a result is unwanted or uninteresting, `[Empty]](#manual-Empty)` can be used in situations where no computation should be possible at all.
Instantiating a polymorphic type with `[Empty]](#manual-Empty)` can mark some of its constructors—those with a parameter of the corresponding type—as impossible; this can rule out certain code paths that are not desired.

The presence of a term with type `[Empty]](#manual-Empty)` indicates that an impossible code path has been reached.
There will never be a value with this type, due to the lack of constructors.
On an impossible code path, there's no reason to write further code; the function `[Empty.elim]](#manual-Empty___elim)` can be used to escape an impossible path.

The universe-polymorphic equivalent of `[Empty]](#manual-Empty)` is `[PEmpty]](#manual-PEmpty)`.

inductive type

```lean
[Empty]](#manual-Empty) : Type



[Empty]](#manual-Empty) : Type
```

The empty type. It has no constructors.

Use `[Empty.elim]](#manual-Empty___elim)` in contexts where a value of type `[Empty]](#manual-Empty)` is in scope.

Constructors

inductive type

```lean
[PEmpty.{u}]](#manual-PEmpty) : Sort u



[PEmpty.{u}]](#manual-PEmpty) : Sort u
```

The universe-polymorphic empty type, with no constructors.

`[PEmpty]](#manual-PEmpty)` can be used in any universe, but this flexibility can lead to worse error messages and more
challenges with universe level unification. Prefer the type `[Empty]](#manual-Empty)` or the proposition `[False]](#manual-False)` when
possible.

Constructors

**Example: Impossible Code Paths**

The type signature of the function `[f]](#manual-f-_LPAR_in-Impossible-Code-Paths_RPAR_)` indicates that it might throw exceptions, but allows the exception type to be anything:

```lean
def f (n : [Nat]](#manual-Nat___zero)) : [Except]](#manual-Except___error) ε [Nat]](#manual-Nat___zero) := [pure]](#manual-Pure___mk) n
```

Instantiating `[f]](#manual-f-_LPAR_in-Impossible-Code-Paths_RPAR_)`'s exception type with `[Empty]](#manual-Empty)` exploits the fact that `[f]](#manual-f-_LPAR_in-Impossible-Code-Paths_RPAR_)` never actually throws an exception to convert it to a function whose type indicates that no exceptions will be thrown.
In particular, it allows `[Empty.elim]](#manual-Empty___elim)` to be used to avoid handing the impossible exception value.

```lean
def g (n : [Nat]](#manual-Nat___zero)) : [Nat]](#manual-Nat___zero) :=
[match]](#manual-Lean___Parser___Term___match) [f]](#manual-f-_LPAR_in-Impossible-Code-Paths_RPAR_) (ε := [Empty]](#manual-Empty)) n [with]](#manual-Lean___Parser___Term___match)
| [.error]](#manual-Except___error) e =>
[Empty.elim]](#manual-Empty___elim) e
| [.ok]](#manual-Except___error) v => v
```

### 20.10.1. API Reference {#manual-The-Lean-Language-Reference--Basic-Types--The-Empty-Type--API-Reference}

def

```lean
[Empty.elim.{u}]](#manual-Empty___elim) {C : Sort u} : [Empty]](#manual-Empty) → C



[Empty.elim.{u}]](#manual-Empty___elim) {C : Sort u} : [Empty]](#manual-Empty) → C
```

`[Empty.elim]](#manual-Empty___elim) : [Empty]](#manual-Empty) → C` says that a value of any type can be constructed from
`[Empty]](#manual-Empty)`. This can be thought of as a compiler-checked assertion that a code path is unreachable.

def

```lean
[PEmpty.elim.{u_1, u_2}]](#manual-PEmpty___elim) {C : Sort u_1} : [PEmpty]](#manual-PEmpty) → C



[PEmpty.elim.{u_1, u_2}]](#manual-PEmpty___elim) {C : Sort u_1} :
  [PEmpty]](#manual-PEmpty) → C
```

`PEmpty.elim : Empty → C` says that a value of any type can be constructed from
`[PEmpty]](#manual-PEmpty)`. This can be thought of as a compiler-checked assertion that a code path is unreachable.

---



## Basic Types — 20.11. Booleans {#manual-basic-types-2011-booleans}

> 📄 Source: https://lean-lang.org/doc/reference/latest/Basic-Types/Booleans/

inductive type

```lean
[Bool]](#manual-Bool___false) : Type



[Bool]](#manual-Bool___false) : Type
```

The Boolean values, `[true]](#manual-Bool___false)` and `[false]](#manual-Bool___false)`.

Logically speaking, this is equivalent to `Prop` (the type of propositions). The distinction is
public important for programming: both propositions and their proofs are erased in the code generator,
while `[Bool]](#manual-Bool___false)` corresponds to the Boolean type in most programming languages and carries precisely one
bit of run-time information.

Constructors

```lean
[Bool.false]](#manual-Bool___false) : [Bool]](#manual-Bool___false)
```

The Boolean value `[false]](#manual-Bool___false)`, not to be confused with the proposition `[False]](#manual-False)`.

```lean
[Bool.true]](#manual-Bool___false) : [Bool]](#manual-Bool___false)
```

The Boolean value `[true]](#manual-Bool___false)`, not to be confused with the proposition `[True]](#manual-True___intro)`.

The constructors `[Bool.true]](#manual-Bool___false)` and `[Bool.false]](#manual-Bool___false)` are exported from the `[Bool]](#manual-Bool___false)` namespace, so they can be written `[true]](#manual-Bool___false)` and `[false]](#manual-Bool___false)`.

### 20.11.1. Run-Time Representation {#manual-The-Lean-Language-Reference--Basic-Types--Booleans--Run-Time-Representation}

Because `[Bool]](#manual-Bool___false)` is an [enum inductive]](#manual---tech-term-enum-inductive) type, it is represented by a single byte in compiled code.

### 20.11.2. Booleans and Propositions {#manual-The-Lean-Language-Reference--Basic-Types--Booleans--Booleans-and-Propositions}

Both `[Bool]](#manual-Bool___false)` and `Prop` represent notions of truth.
From a purely logical perspective, they are equivalent: [propositional extensionality]](#manual---tech-term-Extensionality) means that there are fundamentally only two propositions, namely `[True]](#manual-True___intro)` and `[False]](#manual-False)`.
However, there is an important pragmatic difference: `[Bool]](#manual-Bool___false)` classifies *values* that can be computed by programs, while `Prop` classifies statements for which code generation doesn't make sense.
In other words, `[Bool]](#manual-Bool___false)` is the notion of truth and falsehood that's appropriate for programs, while `Prop` is the notion that's appropriate for mathematics.
Because proofs are erased from compiled programs, keeping `[Bool]](#manual-Bool___false)` and `Prop` distinct makes it clear which parts of a Lean file are intended for computation.

A `[Bool]](#manual-Bool___false)` can be used wherever a `Prop` is expected.
There is a [coercion]](#manual---tech-term-coercion) from every `[Bool]](#manual-Bool___false)` `b` to the proposition `b = [true]](#manual-Bool___false)`.
By `[propext]](#manual-propext)`, `[true]](#manual-Bool___false) = [true]](#manual-Bool___false)` is equal to `[True]](#manual-True___intro)`, and `[false]](#manual-Bool___false) = [true]](#manual-Bool___false)` is equal to `[False]](#manual-False)`.

Not every proposition can be used by programs to make run-time decisions.
Otherwise, a program could branch on whether the Collatz conjecture is true or false!
Many propositions, however, can be checked algorithmically.
These propositions are called [*decidable*]](#manual---tech-term-decidable) propositions, and have instances of the `[Decidable]](#manual-Decidable___isFalse)` type class.
The function `[Decidable.decide]](#manual-Decidable___decide)` converts a proof-carrying `[Decidable]](#manual-Decidable___isFalse)` result into a `[Bool]](#manual-Bool___false)`.
This function is also a coercion from decidable propositions to `[Bool]](#manual-Bool___false)`, so `(2 = 2 : [Bool]](#manual-Bool___false))` evaluates to `[true]](#manual-Bool___false)`.

### 20.11.3. Syntax {#manual-The-Lean-Language-Reference--Basic-Types--Booleans--Syntax}

syntaxBoolean Infix Operators

The infix operators `&&`, `||`, and `^^` are notations for `[Bool.and]](#manual-Bool___and)`, `[Bool.or]](#manual-Bool___or)`, and `[Bool.xor]](#manual-Bool___xor)`, respectively.

```lean
term ::= ...
    | term && term
```

```lean
term ::= ...
    | term || term
```

```lean
term ::= ...
    | term ^^ term
```

syntaxBoolean Negation

The prefix operator `!` is notation for `[Bool.not]](#manual-Bool___not)`.

```lean
term ::= ...
    | !term
```

### 20.11.4. API Reference {#manual-The-Lean-Language-Reference--Basic-Types--Booleans--API-Reference}

#### 20.11.4.1. Logical Operations {#manual-The-Lean-Language-Reference--Basic-Types--Booleans--API-Reference--Logical-Operations}

The functions `[cond]](#manual-cond)`, `[and]](#manual-Bool___and)`, and `[or]](#manual-Bool___or)` are short-circuiting.
In other words, `[false]](#manual-Bool___false) && BIG_EXPENSIVE_COMPUTATION` does not need to execute `BIG_EXPENSIVE_COMPUTATION` before returning `false`.
These functions are defined using the `macro_inline` attribute, which causes the compiler to replace calls to them with their definitions while generating code, and the definitions use nested pattern matching to achieve the short-circuiting behavior.

def

```lean
[cond.{u}]](#manual-cond) {α : Sort u} (c : [Bool]](#manual-Bool___false)) (x y : α) : α



[cond.{u}]](#manual-cond) {α : Sort u} (c : [Bool]](#manual-Bool___false))
  (x y : α) : α
```

The conditional function.

`[cond]](#manual-cond) c x y` is the same as `[if]](#manual-termIfThenElse) c [then]](#manual-termIfThenElse) x [else]](#manual-termIfThenElse) y`, but optimized for a Boolean condition rather than
a decidable proposition. It can also be written using the notation `[bif]](#manual-boolIfThenElse) c [then]](#manual-boolIfThenElse) x [else]](#manual-boolIfThenElse) y`.

Just like `ite`, `[cond]](#manual-cond)` is declared `@[macro_inline]`, which causes applications of `[cond]](#manual-cond)` to be
unfolded. As a result, `x` and `y` are not evaluated at runtime until one of them is selected, and
only the selected branch is evaluated.

def

```lean
[Bool.dcond.{u}]](#manual-Bool___dcond) {α : Sort u} (c : [Bool]](#manual-Bool___false)) (x : c [=]](#manual-Eq___refl) [true]](#manual-Bool___false) → α)
  (y : c [=]](#manual-Eq___refl) [false]](#manual-Bool___false) → α) : α



[Bool.dcond.{u}]](#manual-Bool___dcond) {α : Sort u} (c : [Bool]](#manual-Bool___false))
  (x : c [=]](#manual-Eq___refl) [true]](#manual-Bool___false) → α) (y : c [=]](#manual-Eq___refl) [false]](#manual-Bool___false) → α) :
  α
```

The dependent conditional function, in which each branch is provided with a local assumption about
the condition's value. This allows the value to be used in proofs as well as for control flow.

`dcond c (fun h => x) (fun h => y)` is the same as `[if]](#manual-termDepIfThenElse) h : c [then]](#manual-termDepIfThenElse) x [else]](#manual-termDepIfThenElse) y`, but optimized for a
Boolean condition rather than a decidable proposition. Unlike the non-dependent version `[cond]](#manual-cond)`,
there is no special notation for `dcond`.

Just like `ite`, `dite`, and `[cond]](#manual-cond)`, `dcond` is declared `@[macro_inline]`, which causes
applications of `dcond` to be unfolded. As a result, `x` and `y` are not evaluated at runtime until
one of them is selected, and only the selected branch is evaluated. `dcond` is intended for
metaprogramming use, rather than for use in verified programs, so behavioral lemmas are not
provided.

def

```lean
[Bool.not]](#manual-Bool___not) : [Bool]](#manual-Bool___false) → [Bool]](#manual-Bool___false)



[Bool.not]](#manual-Bool___not) : [Bool]](#manual-Bool___false) → [Bool]](#manual-Bool___false)
```

Boolean negation, also known as Boolean complement. `[not]](#manual-Bool___not) x` can be written `!x`.

This is a function that maps the value `[true]](#manual-Bool___false)` to `[false]](#manual-Bool___false)` and the value `[false]](#manual-Bool___false)` to `[true]](#manual-Bool___false)`. The
propositional connective is `[Not]](#manual-Not) : Prop → Prop`.

Conventions for notations in identifiers:

- The recommended spelling of `!` in identifiers is `[not]](#manual-Bool___not)`.

def

```lean
[Bool.and]](#manual-Bool___and) (x y : [Bool]](#manual-Bool___false)) : [Bool]](#manual-Bool___false)



[Bool.and]](#manual-Bool___and) (x y : [Bool]](#manual-Bool___false)) : [Bool]](#manual-Bool___false)
```

Boolean “and”, also known as conjunction. `[and]](#manual-Bool___and) x y` can be written `x && y`.

The corresponding propositional connective is `[And]](#manual-And___intro) : Prop → Prop → Prop`, written with the `∧`
operator.

The Boolean `[and]](#manual-Bool___and)` is a `@[macro_inline]` function in order to give it short-circuiting evaluation:
if `x` is `[false]](#manual-Bool___false)` then `y` is not evaluated at runtime.

Conventions for notations in identifiers:

- The recommended spelling of `&&` in identifiers is `[and]](#manual-Bool___and)`.
- The recommended spelling of `||` in identifiers is `[or]](#manual-Bool___or)`.

def

```lean
[Bool.or]](#manual-Bool___or) (x y : [Bool]](#manual-Bool___false)) : [Bool]](#manual-Bool___false)



[Bool.or]](#manual-Bool___or) (x y : [Bool]](#manual-Bool___false)) : [Bool]](#manual-Bool___false)
```

Boolean “or”, also known as disjunction. `[or]](#manual-Bool___or) x y` can be written `x || y`.

The corresponding propositional connective is `[Or]](#manual-Or___inl) : Prop → Prop → Prop`, written with the `∨`
operator.

The Boolean `[or]](#manual-Bool___or)` is a `@[macro_inline]` function in order to give it short-circuiting evaluation:
if `x` is `[true]](#manual-Bool___false)` then `y` is not evaluated at runtime.

def

```lean
[Bool.xor]](#manual-Bool___xor) : [Bool]](#manual-Bool___false) → [Bool]](#manual-Bool___false) → [Bool]](#manual-Bool___false)



[Bool.xor]](#manual-Bool___xor) : [Bool]](#manual-Bool___false) → [Bool]](#manual-Bool___false) → [Bool]](#manual-Bool___false)
```

Boolean “exclusive or”. `[xor]](#manual-Bool___xor) x y` can be written `x ^^ y`.

`x ^^ y` is `[true]](#manual-Bool___false)` when precisely one of `x` or `y` is `[true]](#manual-Bool___false)`. Unlike `[and]](#manual-Bool___and)` and `[or]](#manual-Bool___or)`, it does not
have short-circuiting behavior, because one argument's value never determines the final value. Also
unlike `[and]](#manual-Bool___and)` and `[or]](#manual-Bool___or)`, there is no commonly-used corresponding propositional connective.

Examples:

- `[false]](#manual-Bool___false) ^^ [false]](#manual-Bool___false) = [false]](#manual-Bool___false)`
- `[true]](#manual-Bool___false) ^^ [false]](#manual-Bool___false) = [true]](#manual-Bool___false)`
- `[false]](#manual-Bool___false) ^^ [true]](#manual-Bool___false) = [true]](#manual-Bool___false)`
- `[true]](#manual-Bool___false) ^^ [true]](#manual-Bool___false) = [false]](#manual-Bool___false)`

Conventions for notations in identifiers:

- The recommended spelling of `^^` in identifiers is `[xor]](#manual-Bool___xor)`.

#### 20.11.4.2. Comparisons {#manual-The-Lean-Language-Reference--Basic-Types--Booleans--API-Reference--Comparisons}

Most comparisons on Booleans should be performed using the `[DecidableEq]](#manual-DecidableEq) [Bool]](#manual-Bool___false)`, `[LT]](#manual-LT___mk) [Bool]](#manual-Bool___false)`, `[LE]](#manual-LE___mk) [Bool]](#manual-Bool___false)` instances.

def

```lean
[Bool.decEq]](#manual-Bool___decEq) (a b : [Bool]](#manual-Bool___false)) : [Decidable]](#manual-Decidable___isFalse) [(]](#manual-Eq___refl)a [=]](#manual-Eq___refl) b[)]](#manual-Eq___refl)



[Bool.decEq]](#manual-Bool___decEq) (a b : [Bool]](#manual-Bool___false)) :
  [Decidable]](#manual-Decidable___isFalse) [(]](#manual-Eq___refl)a [=]](#manual-Eq___refl) b[)]](#manual-Eq___refl)
```

Decides whether two Booleans are equal.

This function should normally be called via the `[DecidableEq]](#manual-DecidableEq) [Bool]](#manual-Bool___false)` instance that it exists to
support.

#### 20.11.4.3. Conversions {#manual-The-Lean-Language-Reference--Basic-Types--Booleans--API-Reference--Conversions}

def

```lean
[Bool.toISize]](#manual-Bool___toISize) (b : [Bool]](#manual-Bool___false)) : [ISize]](#manual-ISize___ofUSize)



[Bool.toISize]](#manual-Bool___toISize) (b : [Bool]](#manual-Bool___false)) : [ISize]](#manual-ISize___ofUSize)
```

Converts `[true]](#manual-Bool___false)` to `1` and `[false]](#manual-Bool___false)` to `0`.

def

```lean
[Bool.toUInt8]](#manual-Bool___toUInt8) (b : [Bool]](#manual-Bool___false)) : [UInt8]](#manual-UInt8___ofBitVec)



[Bool.toUInt8]](#manual-Bool___toUInt8) (b : [Bool]](#manual-Bool___false)) : [UInt8]](#manual-UInt8___ofBitVec)
```

Converts `[true]](#manual-Bool___false)` to `1` and `[false]](#manual-Bool___false)` to `0`.

def

```lean
[Bool.toUInt16]](#manual-Bool___toUInt16) (b : [Bool]](#manual-Bool___false)) : [UInt16]](#manual-UInt16___ofBitVec)



[Bool.toUInt16]](#manual-Bool___toUInt16) (b : [Bool]](#manual-Bool___false)) : [UInt16]](#manual-UInt16___ofBitVec)
```

Converts `[true]](#manual-Bool___false)` to `1` and `[false]](#manual-Bool___false)` to `0`.

def

```lean
[Bool.toUInt32]](#manual-Bool___toUInt32) (b : [Bool]](#manual-Bool___false)) : [UInt32]](#manual-UInt32___ofBitVec)



[Bool.toUInt32]](#manual-Bool___toUInt32) (b : [Bool]](#manual-Bool___false)) : [UInt32]](#manual-UInt32___ofBitVec)
```

Converts `[true]](#manual-Bool___false)` to `1` and `[false]](#manual-Bool___false)` to `0`.

def

```lean
[Bool.toUInt64]](#manual-Bool___toUInt64) (b : [Bool]](#manual-Bool___false)) : [UInt64]](#manual-UInt64___ofBitVec)



[Bool.toUInt64]](#manual-Bool___toUInt64) (b : [Bool]](#manual-Bool___false)) : [UInt64]](#manual-UInt64___ofBitVec)
```

Converts `[true]](#manual-Bool___false)` to `1` and `[false]](#manual-Bool___false)` to `0`.

def

```lean
[Bool.toUSize]](#manual-Bool___toUSize) (b : [Bool]](#manual-Bool___false)) : [USize]](#manual-USize___ofBitVec)



[Bool.toUSize]](#manual-Bool___toUSize) (b : [Bool]](#manual-Bool___false)) : [USize]](#manual-USize___ofBitVec)
```

Converts `[true]](#manual-Bool___false)` to `1` and `[false]](#manual-Bool___false)` to `0`.

def

```lean
[Bool.toInt8]](#manual-Bool___toInt8) (b : [Bool]](#manual-Bool___false)) : [Int8]](#manual-Int8___ofUInt8)



[Bool.toInt8]](#manual-Bool___toInt8) (b : [Bool]](#manual-Bool___false)) : [Int8]](#manual-Int8___ofUInt8)
```

Converts `[true]](#manual-Bool___false)` to `1` and `[false]](#manual-Bool___false)` to `0`.

def

```lean
[Bool.toInt16]](#manual-Bool___toInt16) (b : [Bool]](#manual-Bool___false)) : [Int16]](#manual-Int16___ofUInt16)



[Bool.toInt16]](#manual-Bool___toInt16) (b : [Bool]](#manual-Bool___false)) : [Int16]](#manual-Int16___ofUInt16)
```

Converts `[true]](#manual-Bool___false)` to `1` and `[false]](#manual-Bool___false)` to `0`.

def

```lean
[Bool.toInt32]](#manual-Bool___toInt32) (b : [Bool]](#manual-Bool___false)) : [Int32]](#manual-Int32___ofUInt32)



[Bool.toInt32]](#manual-Bool___toInt32) (b : [Bool]](#manual-Bool___false)) : [Int32]](#manual-Int32___ofUInt32)
```

Converts `[true]](#manual-Bool___false)` to `1` and `[false]](#manual-Bool___false)` to `0`.

def

```lean
[Bool.toInt64]](#manual-Bool___toInt64) (b : [Bool]](#manual-Bool___false)) : [Int64]](#manual-Int64___ofUInt64)



[Bool.toInt64]](#manual-Bool___toInt64) (b : [Bool]](#manual-Bool___false)) : [Int64]](#manual-Int64___ofUInt64)
```

Converts `[true]](#manual-Bool___false)` to `1` and `[false]](#manual-Bool___false)` to `0`.

def

```lean
[Bool.toNat]](#manual-Bool___toNat) (b : [Bool]](#manual-Bool___false)) : [Nat]](#manual-Nat___zero)



[Bool.toNat]](#manual-Bool___toNat) (b : [Bool]](#manual-Bool___false)) : [Nat]](#manual-Nat___zero)
```

Converts `[true]](#manual-Bool___false)` to `1` and `[false]](#manual-Bool___false)` to `0`.

def

```lean
[Bool.toInt]](#manual-Bool___toInt) (b : [Bool]](#manual-Bool___false)) : [Int]](#manual-Int___ofNat)



[Bool.toInt]](#manual-Bool___toInt) (b : [Bool]](#manual-Bool___false)) : [Int]](#manual-Int___ofNat)
```

Converts `[true]](#manual-Bool___false)` to `1` and `[false]](#manual-Bool___false)` to `0`.

---



## Basic Types — 20.12. Optional Values {#manual-basic-types-2012-optional-values}

> 📄 Source: https://lean-lang.org/doc/reference/latest/Basic-Types/Optional-Values/

`[Option]](#manual-Option___none) α` is the type of values which are either `[some]](#manual-Option___none) v` for some `v`﻿`:`﻿`α`, or `[none]](#manual-Option___none)`.
In functional programming, this type is used similarly to nullable types: `[none]](#manual-Option___none)` represents the absence of a value.
Additionally, partial functions from `α` to `β` can be represented by the type `α → [Option]](#manual-Option___none) β`, where `[none]](#manual-Option___none)` results when the function is undefined for some input.
Computationally, these partial functions represent the possibility of failure or errors, and they correspond to a program that can terminate early but not throw an informative exception.

`[Option]](#manual-Option___none)` can also be thought of as being similar to a list that contains at most one element.
From this perspective, iterating over `[Option]](#manual-Option___none)` consists of carrying out an operation only when a value is present.
The `[Option]](#manual-Option___none)` API makes frequent use of this perspective.

**Example: Options as Nullability**

The function `[Std.HashMap.get?](https://lean-lang.org/doc/reference/latest/Basic-Types/Maps-and-Sets/#Std___HashMap___get___-next)` looks up a specified key `a : α` inside a `[HashMap](https://lean-lang.org/doc/reference/latest/Basic-Types/Maps-and-Sets/#Std___HashMap) α β`:

```lean
[Std.HashMap.get?](https://lean-lang.org/doc/reference/latest/Basic-Types/Maps-and-Sets/#Std___HashMap___get___-next).{u, v} {α : Type u} {β : Type v}
[[BEq]](#manual-BEq___mk) α] [[Hashable]](#manual-Hashable___mk) α]
(m : [HashMap](https://lean-lang.org/doc/reference/latest/Basic-Types/Maps-and-Sets/#Std___HashMap) α β) (a : α) :
[Option]](#manual-Option___none) β
```

Because there is no way to know in advance whether the key is actually in the map, the return type is `[Option]](#manual-Option___none) β`, where `[none]](#manual-Option___none)` means the key was not in the map, and `[some]](#manual-Option___none) b` means that the key was found and `b` is the value retrieved.

The `xs[i]` syntax, which is used to index into collections when there is an available proof that `i` is a valid index into `xs`, has a variant `xs[i]?` that returns an optional value depending on whether the given index is valid.
If `m`﻿`:`﻿`[HashMap](https://lean-lang.org/doc/reference/latest/Basic-Types/Maps-and-Sets/#Std___HashMap) α β` and `a`﻿`:`﻿`α`, then `m[a]?` is equivalent to `[HashMap.get?](https://lean-lang.org/doc/reference/latest/Basic-Types/Maps-and-Sets/#Std___HashMap___get___-next) m a`.

**Example: Options as Safe Nullability**

In many programming languages, it is important to remember to check for the null value.
When using `[Option]](#manual-Option___none)`, the type system requires these checks in the right places: `[Option]](#manual-Option___none) α` and `α` are not the same type, and converting from one to the other requires handling the case of `[none]](#manual-Option___none)`.
This can be done via helpers such as `[Option.getD]](#manual-Option___getD)`, or with pattern matching.

```lean
def postalCodes : [Std.HashMap](https://lean-lang.org/doc/reference/latest/Basic-Types/Maps-and-Sets/#Std___HashMap) [Nat]](#manual-Nat___zero) [String]](#manual-String___ofByteArray) :=
[Std.HashMap.emptyWithCapacity](https://lean-lang.org/doc/reference/latest/Basic-Types/Maps-and-Sets/#Std___HashMap___emptyWithCapacity) 1 |>.[insert](https://lean-lang.org/doc/reference/latest/Basic-Types/Maps-and-Sets/#Std___HashMap___insert) 12345 "Schenectady"
```

```lean
[#eval]](#manual-Lean___Parser___Command___eval) [postalCodes]](#manual-postalCodes-_LPAR_in-Options-as-Safe-Nullability_RPAR_)[12346]?.[getD]](#manual-Option___getD) "not found"
```

```lean
"not found"
```

```lean
[#eval]](#manual-Lean___Parser___Command___eval)
[match]](#manual-Lean___Parser___Term___match) [postalCodes]](#manual-postalCodes-_LPAR_in-Options-as-Safe-Nullability_RPAR_)[12346]? [with]](#manual-Lean___Parser___Term___match)
| [none]](#manual-Option___none) => "not found"
| [some]](#manual-Option___none) city => city
```

```lean
"not found"
```

```lean
[#eval]](#manual-Lean___Parser___Command___eval)
[if]](#manual-termIfLet) [let]](#manual-termIfLet) [some]](#manual-Option___none) city := [postalCodes]](#manual-postalCodes-_LPAR_in-Options-as-Safe-Nullability_RPAR_)[12345]? [then]](#manual-termIfLet)
city
[else]](#manual-termIfLet)
"not found"
```

```lean
"Schenectady"
```

inductive type

```lean
[Option.{u}]](#manual-Option___none) (α : Type u) : Type u



[Option.{u}]](#manual-Option___none) (α : Type u) : Type u
```

Optional values, which are either `[some]](#manual-Option___none)` around a value from the underlying type or `[none]](#manual-Option___none)`.

`[Option]](#manual-Option___none)` can represent nullable types or computations that might fail. In the codomain of a function
type, it can also represent partiality.

Constructors

```lean
[Option.none.{u}]](#manual-Option___none) {α : Type u} : [Option]](#manual-Option___none) α
```

No value.

```lean
[Option.some.{u}]](#manual-Option___none) {α : Type u} (val : α) : [Option]](#manual-Option___none) α
```

Some value of type `α`.

### 20.12.1. Coercions {#manual-The-Lean-Language-Reference--Basic-Types--Optional-Values--Coercions}

There is a [coercion]](#manual---tech-term-coercion) from `α` to `[Option]](#manual-Option___none) α` that wraps a value in `[some]](#manual-Option___none)`.
This allows `[Option]](#manual-Option___none)` to be used in a style similar to nullable types in other languages, where values that are missing are indicated by `[none]](#manual-Option___none)` and values that are present are not specially marked.

**Example: Coercions and Option**

In `[getAlpha]](#manual-getAlpha-_LPAR_in-Coercions-and--Option_RPAR_)`, a line of input is read.
If the line consists only of letters (after removing whitespace from the beginning and end of it), then it is returned; otherwise, the function returns `[none]](#manual-Option___none)`.

```lean
def getAlpha : [IO](https://lean-lang.org/doc/reference/latest/IO/Logical-Model/#IO) ([Option]](#manual-Option___none) [String]](#manual-String___ofByteArray)) := [do]](#manual-Lean___Parser___Term___do)
let line := (← (← [IO.getStdin](https://lean-lang.org/doc/reference/latest/IO/Files___-File-Handles___-and-Streams/#IO___getStdin)).[getLine](https://lean-lang.org/doc/reference/latest/IO/Files___-File-Handles___-and-Streams/#IO___FS___Stream___mk)).trim
if line.[length]](#manual-String___length) > 0 && line.[all]](#manual-String___all) [Char.isAlpha]](#manual-Char___isAlpha) then
return line
else
return [none]](#manual-Option___none)
```

In the successful case, there is no explicit `[some]](#manual-Option___none)` wrapped around `line`.
The `[some]](#manual-Option___none)` is automatically inserted by the coercion.

### 20.12.2. API Reference {#manual-The-Lean-Language-Reference--Basic-Types--Optional-Values--API-Reference}

#### 20.12.2.1. Extracting Values {#manual-The-Lean-Language-Reference--Basic-Types--Optional-Values--API-Reference--Extracting-Values}

def

```lean
[Option.get.{u}]](#manual-Option___get) {α : Type u} (o : [Option]](#manual-Option___none) α) : o.[isSome]](#manual-Option___isSome) [=]](#manual-Eq___refl) [true]](#manual-Bool___false) → α



[Option.get.{u}]](#manual-Option___get) {α : Type u}
  (o : [Option]](#manual-Option___none) α) : o.[isSome]](#manual-Option___isSome) [=]](#manual-Eq___refl) [true]](#manual-Bool___false) → α
```

Extracts the value from an option that can be proven to be `[some]](#manual-Option___none)`.

def

```lean
[Option.get!.{u}]](#manual-Option___get___) {α : Type u} [[Inhabited]](#manual-Inhabited___mk) α] : [Option]](#manual-Option___none) α → α



[Option.get!.{u}]](#manual-Option___get___) {α : Type u}
  [[Inhabited]](#manual-Inhabited___mk) α] : [Option]](#manual-Option___none) α → α
```

Extracts the value from an `[Option]](#manual-Option___none)`, panicking on `[none]](#manual-Option___none)`.

def

```lean
[Option.getD.{u_1}]](#manual-Option___getD) {α : Type u_1} (opt : [Option]](#manual-Option___none) α) (dflt : α) : α



[Option.getD.{u_1}]](#manual-Option___getD) {α : Type u_1}
  (opt : [Option]](#manual-Option___none) α) (dflt : α) : α
```

Gets an optional value, returning a given default on `[none]](#manual-Option___none)`.

This function is `@[macro_inline]`, so `dflt` will not be evaluated unless `opt` turns out to be
`[none]](#manual-Option___none)`.

Examples:

- `([some]](#manual-Option___none) "hello").[getD]](#manual-Option___getD) "goodbye" = "hello"`
- `[none]](#manual-Option___none).[getD]](#manual-Option___getD) "goodbye" = "goodbye"`

def

```lean
[Option.getDM.{u_1, u_2}]](#manual-Option___getDM) {m : Type u_1 → Type u_2} {α : Type u_1}
  [[Pure]](#manual-Pure___mk) m] (x : [Option]](#manual-Option___none) α) (y : m α) : m α



[Option.getDM.{u_1, u_2}]](#manual-Option___getDM)
  {m : Type u_1 → Type u_2} {α : Type u_1}
  [[Pure]](#manual-Pure___mk) m] (x : [Option]](#manual-Option___none) α) (y : m α) : m α
```

Gets the value in an option, monadically computing a default value on `[none]](#manual-Option___none)`.

This is the monadic analogue of `[Option.getD]](#manual-Option___getD)`.

def

```lean
[Option.getM.{u_1, u_2}]](#manual-Option___getM) {m : Type u_1 → Type u_2} {α : Type u_1}
  [[Alternative]](#manual-Alternative___mk) m] : [Option]](#manual-Option___none) α → m α



[Option.getM.{u_1, u_2}]](#manual-Option___getM)
  {m : Type u_1 → Type u_2} {α : Type u_1}
  [[Alternative]](#manual-Alternative___mk) m] : [Option]](#manual-Option___none) α → m α
```

Lifts an optional value to any `[Alternative]](#manual-Alternative___mk)`, sending `[none]](#manual-Option___none)` to `failure`.

def

```lean
[Option.elim.{u_1, u_2}]](#manual-Option___elim) {α : Type u_1} {β : Sort u_2} :
  [Option]](#manual-Option___none) α → β → (α → β) → β



[Option.elim.{u_1, u_2}]](#manual-Option___elim) {α : Type u_1}
  {β : Sort u_2} :
  [Option]](#manual-Option___none) α → β → (α → β) → β
```

A case analysis function for `[Option]](#manual-Option___none)`.

Given a value for `[none]](#manual-Option___none)` and a function to apply to the contents of `[some]](#manual-Option___none)`, `[Option.elim]](#manual-Option___elim)` checks
which constructor a given `[Option]](#manual-Option___none)` consists of, and uses the appropriate argument.

`[Option.elim]](#manual-Option___elim)` is an elimination principle for `[Option]](#manual-Option___none)`. In particular, it is a non-dependent version
of `Option.recOn`. It can also be seen as a combination of `[Option.map]](#manual-Option___map)` and `[Option.getD]](#manual-Option___getD)`.

Examples:

- `([some]](#manual-Option___none) "hello").[elim]](#manual-Option___elim) 0 [String.length]](#manual-String___length) = 5`
- `[none]](#manual-Option___none).[elim]](#manual-Option___elim) 0 [String.length]](#manual-String___length) = 0`

def

```lean
[Option.elimM.{u_1, u_2}]](#manual-Option___elimM) {m : Type u_1 → Type u_2} {α β : Type u_1}
  [[Monad]](#manual-Monad___mk) m] (x : m ([Option]](#manual-Option___none) α)) (y : m β) (z : α → m β) : m β



[Option.elimM.{u_1, u_2}]](#manual-Option___elimM)
  {m : Type u_1 → Type u_2}
  {α β : Type u_1} [[Monad]](#manual-Monad___mk) m]
  (x : m ([Option]](#manual-Option___none) α)) (y : m β)
  (z : α → m β) : m β
```

A monadic case analysis function for `[Option]](#manual-Option___none)`.

Given a fallback computation for `[none]](#manual-Option___none)` and a monadic operation to apply to the contents of `[some]](#manual-Option___none)`,
`[Option.elimM]](#manual-Option___elimM)` checks which constructor a given `[Option]](#manual-Option___none)` consists of, and uses the appropriate
argument.

`[Option.elimM]](#manual-Option___elimM)` can also be seen as a combination of `[Option.mapM]](#manual-Option___mapM)` and `[Option.getDM]](#manual-Option___getDM)`. It is a
monadic analogue of `[Option.elim]](#manual-Option___elim)`.

def

```lean
[Option.merge.{u_1}]](#manual-Option___merge) {α : Type u_1} (fn : α → α → α) :
  [Option]](#manual-Option___none) α → [Option]](#manual-Option___none) α → [Option]](#manual-Option___none) α



[Option.merge.{u_1}]](#manual-Option___merge) {α : Type u_1}
  (fn : α → α → α) :
  [Option]](#manual-Option___none) α → [Option]](#manual-Option___none) α → [Option]](#manual-Option___none) α
```

Applies a function to a two optional values if both are present. Otherwise, if one value is present,
it is returned and the function is not used.

The value is `[some]](#manual-Option___none) (fn a b)` if the inputs are `[some]](#manual-Option___none) a` and `[some]](#manual-Option___none) b`. Otherwise, the behavior is
equivalent to `[Option.orElse]](#manual-Option___orElse)`: if only one input is `[some]](#manual-Option___none) x`, then the value is `[some]](#manual-Option___none) x`, and if
both are `[none]](#manual-Option___none)`, then the value is `[none]](#manual-Option___none)`.

Examples:

- `[Option.merge]](#manual-Option___merge) (· + ·) [none]](#manual-Option___none) ([some]](#manual-Option___none) 3) = [some]](#manual-Option___none) 3`
- `[Option.merge]](#manual-Option___merge) (· + ·) ([some]](#manual-Option___none) 2) ([some]](#manual-Option___none) 3) = [some]](#manual-Option___none) 5`
- `[Option.merge]](#manual-Option___merge) (· + ·) ([some]](#manual-Option___none) 2) [none]](#manual-Option___none) = [some]](#manual-Option___none) 2`
- `Option.merge (· + ·) none none = none`

#### 20.12.2.2. Properties and Comparisons {#manual-The-Lean-Language-Reference--Basic-Types--Optional-Values--API-Reference--Properties-and-Comparisons}

def

```lean
[Option.isNone.{u_1}]](#manual-Option___isNone) {α : Type u_1} : [Option]](#manual-Option___none) α → [Bool]](#manual-Bool___false)



[Option.isNone.{u_1}]](#manual-Option___isNone) {α : Type u_1} :
  [Option]](#manual-Option___none) α → [Bool]](#manual-Bool___false)
```

Returns `[true]](#manual-Bool___false)` on `[none]](#manual-Option___none)` and `[false]](#manual-Bool___false)` on `[some]](#manual-Option___none) x`.

This function is more flexible than `(· == none)` because it does not require a `[BEq]](#manual-BEq___mk) α` instance.

Examples:

- `([none]](#manual-Option___none) : [Option]](#manual-Option___none) [Nat]](#manual-Nat___zero)).[isNone]](#manual-Option___isNone) = [true]](#manual-Bool___false)`
- `([some]](#manual-Option___none) [Nat.add]](#manual-Nat___add)).[isNone]](#manual-Option___isNone) = [false]](#manual-Bool___false)`

def

```lean
[Option.isSome.{u_1}]](#manual-Option___isSome) {α : Type u_1} : [Option]](#manual-Option___none) α → [Bool]](#manual-Bool___false)



[Option.isSome.{u_1}]](#manual-Option___isSome) {α : Type u_1} :
  [Option]](#manual-Option___none) α → [Bool]](#manual-Bool___false)
```

Returns `[true]](#manual-Bool___false)` on `[some]](#manual-Option___none) x` and `[false]](#manual-Bool___false)` on `[none]](#manual-Option___none)`.

def

```lean
[Option.isEqSome.{u_1}]](#manual-Option___isEqSome) {α : Type u_1} [[BEq]](#manual-BEq___mk) α] : [Option]](#manual-Option___none) α → α → [Bool]](#manual-Bool___false)



[Option.isEqSome.{u_1}]](#manual-Option___isEqSome) {α : Type u_1}
  [[BEq]](#manual-BEq___mk) α] : [Option]](#manual-Option___none) α → α → [Bool]](#manual-Bool___false)
```

Checks whether an optional value is both present and equal to some other value.

Given `x? : [Option]](#manual-Option___none) α` and `y : α`, `x?.[isEqSome]](#manual-Option___isEqSome) y` is equivalent to `x? == [some]](#manual-Option___none) y`. It is more
efficient because it avoids an allocation.

Ordering of optional values typically uses the `[DecidableEq]](#manual-DecidableEq) ([Option]](#manual-Option___none) α)`, `[LT]](#manual-LT___mk) ([Option]](#manual-Option___none) α)`, `[Min]](#manual-Min___mk) ([Option]](#manual-Option___none) α)`, and `[Max]](#manual-Max___mk) ([Option]](#manual-Option___none) α)` instances.

def

```lean
[Option.min.{u_1}]](#manual-Option___min) {α : Type u_1} [[Min]](#manual-Min___mk) α] : [Option]](#manual-Option___none) α → [Option]](#manual-Option___none) α → [Option]](#manual-Option___none) α



[Option.min.{u_1}]](#manual-Option___min) {α : Type u_1} [[Min]](#manual-Min___mk) α] :
  [Option]](#manual-Option___none) α → [Option]](#manual-Option___none) α → [Option]](#manual-Option___none) α
```

The minimum of two optional values, with `[none]](#manual-Option___none)` treated as the least element. This function is
usually accessed through the `[Min]](#manual-Min___mk) ([Option]](#manual-Option___none) α)` instance, rather than directly.

Prior to `nightly-2025-02-27`, `[none]](#manual-Option___none)` was treated as the greatest element, so
`min none (some x) = min (some x) none = some x`.

Examples:

- `[Option.min]](#manual-Option___min) ([some]](#manual-Option___none) 2) ([some]](#manual-Option___none) 5) = [some]](#manual-Option___none) 2`
- `[Option.min]](#manual-Option___min) ([some]](#manual-Option___none) 5) ([some]](#manual-Option___none) 2) = [some]](#manual-Option___none) 2`
- `[Option.min]](#manual-Option___min) ([some]](#manual-Option___none) 2) [none]](#manual-Option___none) = [none]](#manual-Option___none)`
- `[Option.min]](#manual-Option___min) [none]](#manual-Option___none) ([some]](#manual-Option___none) 5) = [none]](#manual-Option___none)`
- `Option.min none none = none`

def

```lean
[Option.max.{u_1}]](#manual-Option___max) {α : Type u_1} [[Max]](#manual-Max___mk) α] : [Option]](#manual-Option___none) α → [Option]](#manual-Option___none) α → [Option]](#manual-Option___none) α



[Option.max.{u_1}]](#manual-Option___max) {α : Type u_1} [[Max]](#manual-Max___mk) α] :
  [Option]](#manual-Option___none) α → [Option]](#manual-Option___none) α → [Option]](#manual-Option___none) α
```

The maximum of two optional values.

This function is usually accessed through the `[Max]](#manual-Max___mk) ([Option]](#manual-Option___none) α)` instance, rather than directly.

Examples:

- `[Option.max]](#manual-Option___max) ([some]](#manual-Option___none) 2) ([some]](#manual-Option___none) 5) = [some]](#manual-Option___none) 5`
- `[Option.max]](#manual-Option___max) ([some]](#manual-Option___none) 5) ([some]](#manual-Option___none) 2) = [some]](#manual-Option___none) 5`
- `[Option.max]](#manual-Option___max) ([some]](#manual-Option___none) 2) [none]](#manual-Option___none) = [some]](#manual-Option___none) 2`
- `[Option.max]](#manual-Option___max) [none]](#manual-Option___none) ([some]](#manual-Option___none) 5) = [some]](#manual-Option___none) 5`
- `Option.max none none = none`

def

```lean
[Option.lt.{u_1, u_2}]](#manual-Option___lt) {α : Type u_1} {β : Type u_2} (r : α → β → Prop) :
  [Option]](#manual-Option___none) α → [Option]](#manual-Option___none) β → Prop



[Option.lt.{u_1, u_2}]](#manual-Option___lt) {α : Type u_1}
  {β : Type u_2} (r : α → β → Prop) :
  [Option]](#manual-Option___none) α → [Option]](#manual-Option___none) β → Prop
```

Lifts an ordering relation to `[Option]](#manual-Option___none)`, such that `[none]](#manual-Option___none)` is the least element.

It can be understood as adding a distinguished least element, represented by `[none]](#manual-Option___none)`, to both `α` and
`β`.

This definition is part of the implementation of the `[LT]](#manual-LT___mk) ([Option]](#manual-Option___none) α)` instance. However, because it
can be used with heterogeneous relations, it is sometimes useful on its own.

Examples:

- `[Option.lt]](#manual-Option___lt) (fun n k : [Nat]](#manual-Nat___zero) => n < k) [none]](#manual-Option___none) [none]](#manual-Option___none) = [False]](#manual-False)`
- `[Option.lt]](#manual-Option___lt) (fun n k : [Nat]](#manual-Nat___zero) => n < k) [none]](#manual-Option___none) ([some]](#manual-Option___none) 3) = [True]](#manual-True___intro)`
- `[Option.lt]](#manual-Option___lt) (fun n k : [Nat]](#manual-Nat___zero) => n < k) ([some]](#manual-Option___none) 3) [none]](#manual-Option___none) = [False]](#manual-False)`
- `[Option.lt]](#manual-Option___lt) (fun n k : [Nat]](#manual-Nat___zero) => n < k) ([some]](#manual-Option___none) 4) ([some]](#manual-Option___none) 5) = [True]](#manual-True___intro)`
- `[Option.lt]](#manual-Option___lt) (fun n k : [Nat]](#manual-Nat___zero) => n < k) ([some]](#manual-Option___none) 4) ([some]](#manual-Option___none) 4) = [False]](#manual-False)`

def

```lean
[Option.decidableEqNone.{u_1}]](#manual-Option___decidableEqNone) {α : Type u_1} (o : [Option]](#manual-Option___none) α) :
  [Decidable]](#manual-Decidable___isFalse) [(]](#manual-Eq___refl)o [=]](#manual-Eq___refl) [none]](#manual-Option___none)[)]](#manual-Eq___refl)



[Option.decidableEqNone.{u_1}]](#manual-Option___decidableEqNone)
  {α : Type u_1} (o : [Option]](#manual-Option___none) α) :
  [Decidable]](#manual-Decidable___isFalse) [(]](#manual-Eq___refl)o [=]](#manual-Eq___refl) [none]](#manual-Option___none)[)]](#manual-Eq___refl)
```

Equality with `[none]](#manual-Option___none)` is decidable even if the wrapped type does not have decidable equality.

#### 20.12.2.3. Conversion {#manual-The-Lean-Language-Reference--Basic-Types--Optional-Values--API-Reference--Conversion}

def

```lean
[Option.toArray.{u_1}]](#manual-Option___toArray) {α : Type u_1} : [Option]](#manual-Option___none) α → [Array](https://lean-lang.org/doc/reference/latest/Basic-Types/Arrays/#Array___mk) α



[Option.toArray.{u_1}]](#manual-Option___toArray) {α : Type u_1} :
  [Option]](#manual-Option___none) α → [Array](https://lean-lang.org/doc/reference/latest/Basic-Types/Arrays/#Array___mk) α
```

Converts an optional value to an array with zero or one element.

Examples:

- `([some]](#manual-Option___none) "value").[toArray]](#manual-Option___toArray) = #["value"]`
- `[none]](#manual-Option___none).[toArray]](#manual-Option___toArray) = #[]`

def

```lean
[Option.toList.{u_1}]](#manual-Option___toList) {α : Type u_1} : [Option]](#manual-Option___none) α → [List](https://lean-lang.org/doc/reference/latest/Basic-Types/Linked-Lists/#List___nil) α



[Option.toList.{u_1}]](#manual-Option___toList) {α : Type u_1} :
  [Option]](#manual-Option___none) α → [List](https://lean-lang.org/doc/reference/latest/Basic-Types/Linked-Lists/#List___nil) α
```

Converts an optional value to a list with zero or one element.

Examples:

- `([some]](#manual-Option___none) "value").[toList]](#manual-Option___toList) = ["value"]`
- `[none]](#manual-Option___none).[toList]](#manual-Option___toList) = []`

def

```lean
[Option.repr.{u_1}]](#manual-Option___repr) {α : Type u_1} [[Repr]](#manual-Repr___mk) α] : [Option]](#manual-Option___none) α → [Nat]](#manual-Nat___zero) → [Std.Format]](#manual-Std___Format___nil)



[Option.repr.{u_1}]](#manual-Option___repr) {α : Type u_1}
  [[Repr]](#manual-Repr___mk) α] : [Option]](#manual-Option___none) α → [Nat]](#manual-Nat___zero) → [Std.Format]](#manual-Std___Format___nil)
```

Returns a representation of an optional value that should be able to be parsed as an equivalent
optional value.

This function is typically accessed through the `[Repr]](#manual-Repr___mk) ([Option]](#manual-Option___none) α)` instance.

def

```lean
[Option.format.{u}]](#manual-Option___format) {α : Type u} [[Std.ToFormat]](#manual-Std___ToFormat___mk) α] : [Option]](#manual-Option___none) α → [Std.Format]](#manual-Std___Format___nil)



[Option.format.{u}]](#manual-Option___format) {α : Type u}
  [[Std.ToFormat]](#manual-Std___ToFormat___mk) α] : [Option]](#manual-Option___none) α → [Std.Format]](#manual-Std___Format___nil)
```

Formats an optional value, with no expectation that the Lean parser should be able to parse the
result.

This function is usually accessed through the `ToFormat (Option α)` instance.

#### 20.12.2.4. Control {#manual-The-Lean-Language-Reference--Basic-Types--Optional-Values--API-Reference--Control}

`[Option]](#manual-Option___none)` can be thought of as describing a computation that may fail to return a value.
The `[Monad]](#manual-Monad___mk) [Option]](#manual-Option___none)` instance, along with `[Alternative]](#manual-Alternative___mk) [Option]](#manual-Option___none)`, is based on this understanding.
Returning `[none]](#manual-Option___none)` can also be thought of as throwing an exception that contains no interesting information, which is captured in the `[MonadExcept]](#manual-MonadExcept___mk) [Unit]](#manual-Unit) [Option]](#manual-Option___none)` instance.

def

```lean
[Option.guard.{u_1}]](#manual-Option___guard) {α : Type u_1} (p : α → [Bool]](#manual-Bool___false)) (a : α) : [Option]](#manual-Option___none) α



[Option.guard.{u_1}]](#manual-Option___guard) {α : Type u_1}
  (p : α → [Bool]](#manual-Bool___false)) (a : α) : [Option]](#manual-Option___none) α
```

Returns `[none]](#manual-Option___none)` if a value doesn't satisfy a Boolean predicate, or the value itself otherwise.

From the perspective of `[Option]](#manual-Option___none)` as computations that might fail, this function is a run-time
assertion operator in the `[Option]](#manual-Option___none)` monad.

Examples:

- `[Option.guard]](#manual-Option___guard) (· > 2) 1 = [none]](#manual-Option___none)`
- `[Option.guard]](#manual-Option___guard) (· > 2) 5 = [some]](#manual-Option___none) 5`

def

```lean
[Option.bind.{u_1, u_2}]](#manual-Option___bind) {α : Type u_1} {β : Type u_2} :
  [Option]](#manual-Option___none) α → (α → [Option]](#manual-Option___none) β) → [Option]](#manual-Option___none) β



[Option.bind.{u_1, u_2}]](#manual-Option___bind) {α : Type u_1}
  {β : Type u_2} :
  [Option]](#manual-Option___none) α → (α → [Option]](#manual-Option___none) β) → [Option]](#manual-Option___none) β
```

Sequencing of `[Option]](#manual-Option___none)` computations.

From the perspective of `[Option]](#manual-Option___none)` as computations that might fail, this function sequences
potentially-failing computations, failing if either fails. From the perspective of `[Option]](#manual-Option___none)` as a
collection with at most one element, the function is applied to the element if present, and the
final result is empty if either the initial or the resulting collections are empty.

This function is often accessed via the `>>=` operator from the `[Bind]](#manual-Bind___mk) ([Option]](#manual-Option___none) α)` instance, or
implicitly via `do`-notation, but it is also idiomatic to call it using [generalized field
notation](https://lean-lang.org/doc/reference/4.34.0-rc1/find/?domain=Verso.Genre.Manual.section&name=generalized-field-notation).

Examples:

- `[none]](#manual-Option___none).[bind]](#manual-Option___bind) (fun x => [some]](#manual-Option___none) x) = [none]](#manual-Option___none)`
- `([some]](#manual-Option___none) 4).[bind]](#manual-Option___bind) (fun x => [some]](#manual-Option___none) x) = [some]](#manual-Option___none) 4`
- `[none]](#manual-Option___none).[bind]](#manual-Option___bind) ([Option.guard]](#manual-Option___guard) (· > 2)) = [none]](#manual-Option___none)`
- `([some]](#manual-Option___none) 2).[bind]](#manual-Option___bind) ([Option.guard]](#manual-Option___guard) (· > 2)) = [none]](#manual-Option___none)`
- `([some]](#manual-Option___none) 4).[bind]](#manual-Option___bind) ([Option.guard]](#manual-Option___guard) (· > 2)) = [some]](#manual-Option___none) 4`

def

```lean
[Option.bindM.{u_1, u_2, u_3}]](#manual-Option___bindM) {m : Type u_1 → Type u_2} {α : Type u_3}
  {β : Type u_1} [[Pure]](#manual-Pure___mk) m] (f : α → m ([Option]](#manual-Option___none) β)) :
  [Option]](#manual-Option___none) α → m ([Option]](#manual-Option___none) β)



[Option.bindM.{u_1, u_2, u_3}]](#manual-Option___bindM)
  {m : Type u_1 → Type u_2} {α : Type u_3}
  {β : Type u_1} [[Pure]](#manual-Pure___mk) m]
  (f : α → m ([Option]](#manual-Option___none) β)) :
  [Option]](#manual-Option___none) α → m ([Option]](#manual-Option___none) β)
```

Runs the monadic action `f` on `o`'s value, if any, and returns the result, or `[none]](#manual-Option___none)` if there is
no value.

From the perspective of `[Option]](#manual-Option___none)` as a collection with at most one element, the monadic the function
is applied to the element if present, and the final result is empty if either the initial or the
resulting collections are empty.

def

```lean
[Option.join.{u_1}]](#manual-Option___join) {α : Type u_1} (x : [Option]](#manual-Option___none) ([Option]](#manual-Option___none) α)) : [Option]](#manual-Option___none) α



[Option.join.{u_1}]](#manual-Option___join) {α : Type u_1}
  (x : [Option]](#manual-Option___none) ([Option]](#manual-Option___none) α)) : [Option]](#manual-Option___none) α
```

Flattens nested optional values, preserving any value found.

This is analogous to `[List.flatten](https://lean-lang.org/doc/reference/latest/Basic-Types/Linked-Lists/#List___flatten)`.

Examples:

- `[none]](#manual-Option___none).[join]](#manual-Option___join) = [none]](#manual-Option___none)`
- `([some]](#manual-Option___none) [none]](#manual-Option___none)).[join]](#manual-Option___join) = [none]](#manual-Option___none)`
- `([some]](#manual-Option___none) ([some]](#manual-Option___none) v)).[join]](#manual-Option___join) = [some]](#manual-Option___none) v`

def

```lean
[Option.sequence.{u, u_1}]](#manual-Option___sequence) {m : Type u → Type u_1} [[Applicative]](#manual-Applicative___mk) m]
  {α : Type u} : [Option]](#manual-Option___none) (m α) → m ([Option]](#manual-Option___none) α)



[Option.sequence.{u, u_1}]](#manual-Option___sequence)
  {m : Type u → Type u_1} [[Applicative]](#manual-Applicative___mk) m]
  {α : Type u} :
  [Option]](#manual-Option___none) (m α) → m ([Option]](#manual-Option___none) α)
```

Converts an optional monadic computation into a monadic computation of an optional value.

This function only requires `m` to be an applicative functor.

Example:

```lean
[#eval]](#manual-Lean___Parser___Command___eval) show [IO](https://lean-lang.org/doc/reference/latest/IO/Logical-Model/#IO) ([Option]](#manual-Option___none) [String]](#manual-String___ofByteArray)) from
[Option.sequence]](#manual-Option___sequence) <| [some]](#manual-Option___none) [do]](#manual-Lean___Parser___Term___do)
[IO.println](https://lean-lang.org/doc/reference/latest/IO/Console-Output/#IO___println) "hello"
return "world"
```

```
hello
```

```lean
[some]](#manual-Option___none) "world"
```

def

```lean
[Option.tryCatch.{u_1}]](#manual-Option___tryCatch) {α : Type u_1} (x : [Option]](#manual-Option___none) α)
  (handle : [Unit]](#manual-Unit) → [Option]](#manual-Option___none) α) : [Option]](#manual-Option___none) α



[Option.tryCatch.{u_1}]](#manual-Option___tryCatch) {α : Type u_1}
  (x : [Option]](#manual-Option___none) α)
  (handle : [Unit]](#manual-Unit) → [Option]](#manual-Option___none) α) : [Option]](#manual-Option___none) α
```

Recover from failing `[Option]](#manual-Option___none)` computations with a handler function.

This function is usually accessed through the `[MonadExceptOf]](#manual-MonadExceptOf___mk) [Unit]](#manual-Unit) [Option]](#manual-Option___none)` instance.

Examples:

- `[Option.tryCatch]](#manual-Option___tryCatch) [none]](#manual-Option___none) (fun () => [some]](#manual-Option___none) "handled") = [some]](#manual-Option___none) "handled"`
- `[Option.tryCatch]](#manual-Option___tryCatch) ([some]](#manual-Option___none) "succeeded") (fun () => [some]](#manual-Option___none) "handled") = [some]](#manual-Option___none) "succeeded"`

def

```lean
[Option.or.{u_1}]](#manual-Option___or) {α : Type u_1} : [Option]](#manual-Option___none) α → [Option]](#manual-Option___none) α → [Option]](#manual-Option___none) α



[Option.or.{u_1}]](#manual-Option___or) {α : Type u_1} :
  [Option]](#manual-Option___none) α → [Option]](#manual-Option___none) α → [Option]](#manual-Option___none) α
```

Returns the first of its arguments that is `[some]](#manual-Option___none)`, or `[none]](#manual-Option___none)` if neither is `[some]](#manual-Option___none)`.

This is similar to the `<|>` operator, also known as `OrElse.orElse`, but both arguments are always
evaluated without short-circuiting.

def

```lean
[Option.orElse.{u_1}]](#manual-Option___orElse) {α : Type u_1} :
  [Option]](#manual-Option___none) α → ([Unit]](#manual-Unit) → [Option]](#manual-Option___none) α) → [Option]](#manual-Option___none) α



[Option.orElse.{u_1}]](#manual-Option___orElse) {α : Type u_1} :
  [Option]](#manual-Option___none) α → ([Unit]](#manual-Unit) → [Option]](#manual-Option___none) α) → [Option]](#manual-Option___none) α
```

Implementation of `OrElse`'s `<|>` syntax for `[Option]](#manual-Option___none)`. If the first argument is `[some]](#manual-Option___none) a`, returns
`[some]](#manual-Option___none) a`, otherwise evaluates and returns the second argument.

See also `[or]](#manual-Bool___or)` for a version that is strict in the second argument.

#### 20.12.2.5. Iteration {#manual-The-Lean-Language-Reference--Basic-Types--Optional-Values--API-Reference--Iteration}

`[Option]](#manual-Option___none)` can be thought of as a collection that contains at most one value.
From this perspective, iteration operators can be understood as performing some operation on the contained value, if present, or doing nothing if not.

def

```lean
[Option.all.{u_1}]](#manual-Option___all) {α : Type u_1} (p : α → [Bool]](#manual-Bool___false)) : [Option]](#manual-Option___none) α → [Bool]](#manual-Bool___false)



[Option.all.{u_1}]](#manual-Option___all) {α : Type u_1}
  (p : α → [Bool]](#manual-Bool___false)) : [Option]](#manual-Option___none) α → [Bool]](#manual-Bool___false)
```

Checks whether an optional value either satisfies a Boolean predicate or is `[none]](#manual-Option___none)`.

Examples:

- `(some 33).all (· % 2 == 0) = false
- `(some 22).all (· % 2 == 0) = true
- `none.all (fun x : Nat => x % 2 == 0) = true

def

```lean
[Option.any.{u_1}]](#manual-Option___any) {α : Type u_1} (p : α → [Bool]](#manual-Bool___false)) : [Option]](#manual-Option___none) α → [Bool]](#manual-Bool___false)



[Option.any.{u_1}]](#manual-Option___any) {α : Type u_1}
  (p : α → [Bool]](#manual-Bool___false)) : [Option]](#manual-Option___none) α → [Bool]](#manual-Bool___false)
```

Checks whether an optional value is not `[none]](#manual-Option___none)` and satisfies a Boolean predicate.

Examples:

- `(some 33).any (· % 2 == 0) = false
- `(some 22).any (· % 2 == 0) = true
- `none.any (fun x : Nat => true) = false

def

```lean
[Option.filter.{u_1}]](#manual-Option___filter) {α : Type u_1} (p : α → [Bool]](#manual-Bool___false)) : [Option]](#manual-Option___none) α → [Option]](#manual-Option___none) α



[Option.filter.{u_1}]](#manual-Option___filter) {α : Type u_1}
  (p : α → [Bool]](#manual-Bool___false)) : [Option]](#manual-Option___none) α → [Option]](#manual-Option___none) α
```

Keeps an optional value only if it satisfies a Boolean predicate.

If `[Option]](#manual-Option___none)` is thought of as a collection that contains at most one element, then `[Option.filter]](#manual-Option___filter)` is
analogous to `[List.filter](https://lean-lang.org/doc/reference/latest/Basic-Types/Linked-Lists/#List___filter)` or `[Array.filter](https://lean-lang.org/doc/reference/latest/Basic-Types/Arrays/#Array___filter)`.

Examples:

- `([some]](#manual-Option___none) 5).[filter]](#manual-Option___filter) (· % 2 == 0) = [none]](#manual-Option___none)`
- `([some]](#manual-Option___none) 4).[filter]](#manual-Option___filter) (· % 2 == 0) = [some]](#manual-Option___none) 4`
- `[none]](#manual-Option___none).[filter]](#manual-Option___filter) (fun x : [Nat]](#manual-Nat___zero) => x % 2 == 0) = [none]](#manual-Option___none)`
- `[none]](#manual-Option___none).[filter]](#manual-Option___filter) (fun x : [Nat]](#manual-Nat___zero) => [true]](#manual-Bool___false)) = [none]](#manual-Option___none)`

def

```lean
[Option.filterM.{u_1}]](#manual-Option___filterM) {m : Type → Type u_1} {α : Type} [[Applicative]](#manual-Applicative___mk) m]
  (p : α → m [Bool]](#manual-Bool___false)) : [Option]](#manual-Option___none) α → m ([Option]](#manual-Option___none) α)



[Option.filterM.{u_1}]](#manual-Option___filterM) {m : Type → Type u_1}
  {α : Type} [[Applicative]](#manual-Applicative___mk) m]
  (p : α → m [Bool]](#manual-Bool___false)) :
  [Option]](#manual-Option___none) α → m ([Option]](#manual-Option___none) α)
```

Keeps an optional value only if it satisfies a monadic Boolean predicate.

If `[Option]](#manual-Option___none)` is thought of as a collection that contains at most one element, then `[Option.filterM]](#manual-Option___filterM)`
is analogous to `[List.filterM](https://lean-lang.org/doc/reference/latest/Basic-Types/Linked-Lists/#List___filterM)`.

def

```lean
[Option.forM.{u_1, u_2, u_3}]](#manual-Option___forM) {m : Type u_1 → Type u_2} {α : Type u_3}
  [[Pure]](#manual-Pure___mk) m] : [Option]](#manual-Option___none) α → (α → m [PUnit]](#manual-PUnit___unit)) → m [PUnit]](#manual-PUnit___unit)



[Option.forM.{u_1, u_2, u_3}]](#manual-Option___forM)
  {m : Type u_1 → Type u_2} {α : Type u_3}
  [[Pure]](#manual-Pure___mk) m] :
  [Option]](#manual-Option___none) α → (α → m [PUnit]](#manual-PUnit___unit)) → m [PUnit]](#manual-PUnit___unit)
```

Executes a monadic action on an optional value if it is present, or does nothing if there is no
value.

Examples:

```lean
[#eval]](#manual-Lean___Parser___Command___eval) (([some]](#manual-Option___none) 5).[forM]](#manual-Option___forM) [set]](#manual-MonadStateOf___mk) : [StateM]](#manual-StateM) [Nat]](#manual-Nat___zero) [Unit]](#manual-Unit)).[run]](#manual-StateT___run) 0
```

```lean
((), 5)
```

```lean
[#eval]](#manual-Lean___Parser___Command___eval) ([none]](#manual-Option___none).[forM]](#manual-Option___forM) (fun x : [Nat]](#manual-Nat___zero) => [set]](#manual-MonadStateOf___mk) x) : [StateM]](#manual-StateM) [Nat]](#manual-Nat___zero) [Unit]](#manual-Unit)).[run]](#manual-StateT___run) 0
```

```lean
((), 0)
```

def

```lean
[Option.map.{u_1, u_2}]](#manual-Option___map) {α : Type u_1} {β : Type u_2} (f : α → β) :
  [Option]](#manual-Option___none) α → [Option]](#manual-Option___none) β



[Option.map.{u_1, u_2}]](#manual-Option___map) {α : Type u_1}
  {β : Type u_2} (f : α → β) :
  [Option]](#manual-Option___none) α → [Option]](#manual-Option___none) β
```

Apply a function to an optional value, if present.

From the perspective of `[Option]](#manual-Option___none)` as a container with at most one value, this is analogous to
`[List.map](https://lean-lang.org/doc/reference/latest/Basic-Types/Linked-Lists/#List___map)`. It can also be accessed via the `[Functor]](#manual-Functor___mk) [Option]](#manual-Option___none)` instance.

Examples:

- `([none]](#manual-Option___none) : [Option]](#manual-Option___none) [Nat]](#manual-Nat___zero)).[map]](#manual-Option___map) (· + 1) = [none]](#manual-Option___none)`
- `([some]](#manual-Option___none) 3).[map]](#manual-Option___map) (· + 1) = [some]](#manual-Option___none) 4`

def

```lean
[Option.mapA.{u_1, u_2, u_3}]](#manual-Option___mapA) {m : Type u_1 → Type u_2} {α : Type u_3}
  {β : Type u_1} [[Applicative]](#manual-Applicative___mk) m] (f : α → m β) : [Option]](#manual-Option___none) α → m ([Option]](#manual-Option___none) β)



[Option.mapA.{u_1, u_2, u_3}]](#manual-Option___mapA)
  {m : Type u_1 → Type u_2} {α : Type u_3}
  {β : Type u_1} [[Applicative]](#manual-Applicative___mk) m]
  (f : α → m β) : [Option]](#manual-Option___none) α → m ([Option]](#manual-Option___none) β)
```

Applies a function in some applicative functor to an optional value, returning `[none]](#manual-Option___none)` with no
effects if the value is missing.

This is an alias for `[Option.mapM]](#manual-Option___mapM)`, which already works for applicative functors.

def

```lean
[Option.mapM.{u_1, u_2, u_3}]](#manual-Option___mapM) {m : Type u_1 → Type u_2} {α : Type u_3}
  {β : Type u_1} [[Applicative]](#manual-Applicative___mk) m] (f : α → m β) : [Option]](#manual-Option___none) α → m ([Option]](#manual-Option___none) β)



[Option.mapM.{u_1, u_2, u_3}]](#manual-Option___mapM)
  {m : Type u_1 → Type u_2} {α : Type u_3}
  {β : Type u_1} [[Applicative]](#manual-Applicative___mk) m]
  (f : α → m β) : [Option]](#manual-Option___none) α → m ([Option]](#manual-Option___none) β)
```

Applies a function in some applicative functor to an optional value, returning `[none]](#manual-Option___none)` with no
effects if the value is missing.

Runs a monadic function `f` on an optional value, returning the result. If the optional value is
`[none]](#manual-Option___none)`, the function is not called and the result is also `[none]](#manual-Option___none)`.

From the perspective of `[Option]](#manual-Option___none)` as a container with at most one element, this is analogous to
`[List.mapM](https://lean-lang.org/doc/reference/latest/Basic-Types/Linked-Lists/#List___mapM)`, returning the result of running the monadic function on all elements of the container.

This function only requires `m` to be an applicative functor. An alias `[Option.mapA]](#manual-Option___mapA)` is provided.

#### 20.12.2.6. Recursion Helpers {#manual-The-Lean-Language-Reference--Basic-Types--Optional-Values--API-Reference--Recursion-Helpers}

def

```lean
[Option.attach.{u_1}]](#manual-Option___attach) {α : Type u_1} (xs : [Option]](#manual-Option___none) α) :
  [Option]](#manual-Option___none) [{](https://lean-lang.org/doc/reference/latest/Basic-Types/Subtypes/#Subtype___mk) x [//](https://lean-lang.org/doc/reference/latest/Basic-Types/Subtypes/#Subtype___mk) xs [=]](#manual-Eq___refl) [some]](#manual-Option___none) x [}](https://lean-lang.org/doc/reference/latest/Basic-Types/Subtypes/#Subtype___mk)



[Option.attach.{u_1}]](#manual-Option___attach) {α : Type u_1}
  (xs : [Option]](#manual-Option___none) α) :
  [Option]](#manual-Option___none) [{](https://lean-lang.org/doc/reference/latest/Basic-Types/Subtypes/#Subtype___mk) x [//](https://lean-lang.org/doc/reference/latest/Basic-Types/Subtypes/#Subtype___mk) xs [=]](#manual-Eq___refl) [some]](#manual-Option___none) x [}](https://lean-lang.org/doc/reference/latest/Basic-Types/Subtypes/#Subtype___mk)
```

“Attaches” a proof that an optional value, if present, is indeed this value, returning a subtype
that expresses this fact.

This function is primarily used to allow definitions by well-founded recursion that use iteration
operators (such as `[Option.map]](#manual-Option___map)`) to prove that an optional value drawn from a parameter is smaller
than the parameter. This allows the well-founded recursion mechanism to prove that the function
terminates.

def

```lean
[Option.attachWith.{u_1}]](#manual-Option___attachWith) {α : Type u_1} (xs : [Option]](#manual-Option___none) α) (P : α → Prop)
  (H : ∀ (x : α), xs [=]](#manual-Eq___refl) [some]](#manual-Option___none) x → P x) : [Option]](#manual-Option___none) [{](https://lean-lang.org/doc/reference/latest/Basic-Types/Subtypes/#Subtype___mk) x [//](https://lean-lang.org/doc/reference/latest/Basic-Types/Subtypes/#Subtype___mk) P x [}](https://lean-lang.org/doc/reference/latest/Basic-Types/Subtypes/#Subtype___mk)



[Option.attachWith.{u_1}]](#manual-Option___attachWith) {α : Type u_1}
  (xs : [Option]](#manual-Option___none) α) (P : α → Prop)
  (H : ∀ (x : α), xs [=]](#manual-Eq___refl) [some]](#manual-Option___none) x → P x) :
  [Option]](#manual-Option___none) [{](https://lean-lang.org/doc/reference/latest/Basic-Types/Subtypes/#Subtype___mk) x [//](https://lean-lang.org/doc/reference/latest/Basic-Types/Subtypes/#Subtype___mk) P x [}](https://lean-lang.org/doc/reference/latest/Basic-Types/Subtypes/#Subtype___mk)
```

“Attaches” a proof that some predicate holds for an optional value, if present, returning a subtype
that expresses this fact.

This function is primarily used to implement `[Option.attach]](#manual-Option___attach)`, which allows definitions by
well-founded recursion that use iteration operators (such as `[Option.map]](#manual-Option___map)`) to prove that an optional
value drawn from a parameter is smaller than the parameter. This allows the well-founded recursion
mechanism to prove that the function terminates.

def

```lean
[Option.unattach.{u_1}]](#manual-Option___unattach) {α : Type u_1} {p : α → Prop}
  (o : [Option]](#manual-Option___none) [{](https://lean-lang.org/doc/reference/latest/Basic-Types/Subtypes/#Subtype___mk) x [//](https://lean-lang.org/doc/reference/latest/Basic-Types/Subtypes/#Subtype___mk) p x [}](https://lean-lang.org/doc/reference/latest/Basic-Types/Subtypes/#Subtype___mk)) : [Option]](#manual-Option___none) α



[Option.unattach.{u_1}]](#manual-Option___unattach) {α : Type u_1}
  {p : α → Prop}
  (o : [Option]](#manual-Option___none) [{](https://lean-lang.org/doc/reference/latest/Basic-Types/Subtypes/#Subtype___mk) x [//](https://lean-lang.org/doc/reference/latest/Basic-Types/Subtypes/#Subtype___mk) p x [}](https://lean-lang.org/doc/reference/latest/Basic-Types/Subtypes/#Subtype___mk)) : [Option]](#manual-Option___none) α
```

Remove an attached proof that the value in an `[Option]](#manual-Option___none)` is indeed that value.

This function is usually inserted automatically by Lean, rather than explicitly in code. It is
introduced as an intermediate step during the elaboration of definitions by well-founded recursion.

If this function is encountered in a proof state, the right approach is usually the tactic
`[simp]](#manual-simp) [Option.unattach, -Option.map_subtype]`.

It is a synonym for `[Option.map]](#manual-Option___map) [Subtype.val](https://lean-lang.org/doc/reference/latest/Basic-Types/Subtypes/#Subtype___mk)`.

#### 20.12.2.7. Reasoning {#manual-The-Lean-Language-Reference--Basic-Types--Optional-Values--API-Reference--Reasoning}

def

```lean
[Option.choice.{u_1}]](#manual-Option___choice) (α : Type u_1) : [Option]](#manual-Option___none) α



[Option.choice.{u_1}]](#manual-Option___choice) (α : Type u_1) :
  [Option]](#manual-Option___none) α
```

An optional arbitrary element of a given type.

If `α` is non-empty, then there exists some `v : α` and this arbitrary element is `[some]](#manual-Option___none) v`.
Otherwise, it is `[none]](#manual-Option___none)`.

def

```lean
[Option.pbind.{u_1, u_2}]](#manual-Option___pbind) {α : Type u_1} {β : Type u_2} (o : [Option]](#manual-Option___none) α)
  (f : (a : α) → o [=]](#manual-Eq___refl) [some]](#manual-Option___none) a → [Option]](#manual-Option___none) β) : [Option]](#manual-Option___none) β



[Option.pbind.{u_1, u_2}]](#manual-Option___pbind) {α : Type u_1}
  {β : Type u_2} (o : [Option]](#manual-Option___none) α)
  (f : (a : α) → o [=]](#manual-Eq___refl) [some]](#manual-Option___none) a → [Option]](#manual-Option___none) β) :
  [Option]](#manual-Option___none) β
```

Given an optional value and a function that can be applied when the value is `[some]](#manual-Option___none)`, returns the
result of applying the function if this is possible.

The function `f` is *partial* because it is only defined for the values `a : α` such that
`o = [some]](#manual-Option___none) a`. This restriction allows the function to use the fact that it can only be called when
`o` is not `[none]](#manual-Option___none)`: it can relate its argument to the optional value `o`. Its runtime behavior is
equivalent to that of `[Option.bind]](#manual-Option___bind)`.

Examples:

```lean
def attach (v : [Option]](#manual-Option___none) α) : [Option]](#manual-Option___none) { y : α // v = [some]](#manual-Option___none) y } :=
v.[pbind]](#manual-Option___pbind) fun x h => [some]](#manual-Option___none) ⟨x, h⟩
```

```
#reduce attach (some 3)
```

```lean
[some]](#manual-Option___none) ⟨3, ⋯⟩
```

```
#reduce attach none
```

```lean
[none]](#manual-Option___none)
```

def

```lean
[Option.pelim.{u_1, u_2}]](#manual-Option___pelim) {α : Type u_1} {β : Sort u_2} (o : [Option]](#manual-Option___none) α)
  (b : β) (f : (a : α) → o [=]](#manual-Eq___refl) [some]](#manual-Option___none) a → β) : β



[Option.pelim.{u_1, u_2}]](#manual-Option___pelim) {α : Type u_1}
  {β : Sort u_2} (o : [Option]](#manual-Option___none) α) (b : β)
  (f : (a : α) → o [=]](#manual-Eq___refl) [some]](#manual-Option___none) a → β) : β
```

Given an optional value and a function that can be applied when the value is `[some]](#manual-Option___none)`, returns the
result of applying the function if this is possible, or a fallback value otherwise.

The function `f` is *partial* because it is only defined for the values `a : α` such that
`o = [some]](#manual-Option___none) a`. This restriction allows the function to use the fact that it can only be called when
`o` is not `[none]](#manual-Option___none)`: it can relate its argument to the optional value `o`. Its runtime behavior is
equivalent to that of `[Option.elim]](#manual-Option___elim)`.

Examples:

```lean
def attach (v : [Option]](#manual-Option___none) α) : [Option]](#manual-Option___none) { y : α // v = [some]](#manual-Option___none) y } :=
v.[pelim]](#manual-Option___pelim) [none]](#manual-Option___none) fun x h => [some]](#manual-Option___none) ⟨x, h⟩
```

```
#reduce attach (some 3)
```

```lean
[some]](#manual-Option___none) ⟨3, ⋯⟩
```

```
#reduce attach none
```

```lean
[none]](#manual-Option___none)
```

def

```lean
[Option.pmap.{u_1, u_2}]](#manual-Option___pmap) {α : Type u_1} {β : Type u_2} {p : α → Prop}
  (f : (a : α) → p a → β) (o : [Option]](#manual-Option___none) α) :
  (∀ (a : α), o [=]](#manual-Eq___refl) [some]](#manual-Option___none) a → p a) → [Option]](#manual-Option___none) β



[Option.pmap.{u_1, u_2}]](#manual-Option___pmap) {α : Type u_1}
  {β : Type u_2} {p : α → Prop}
  (f : (a : α) → p a → β) (o : [Option]](#manual-Option___none) α) :
  (∀ (a : α), o [=]](#manual-Eq___refl) [some]](#manual-Option___none) a → p a) → [Option]](#manual-Option___none) β
```

Given a function from the elements of `α` that satisfy `p` to `β` and a proof that an optional value
satisfies `p` if it's present, applies the function to the value.

Examples:

```lean
def attach (v : [Option]](#manual-Option___none) α) : [Option]](#manual-Option___none) { y : α // v = [some]](#manual-Option___none) y } :=
v.[pmap]](#manual-Option___pmap) (fun a (h : a ∈ v) => ⟨_, h⟩) (fun _ h => h)
```

```
#reduce attach (some 3)
```

```lean
[some]](#manual-Option___none) ⟨3, ⋯⟩
```

```
#reduce attach none
```

```lean
[none]](#manual-Option___none)
```

---



## Basic Types — 20.13. Tuples {#manual-basic-types-2013-tuples}

> 📄 Source: https://lean-lang.org/doc/reference/latest/Basic-Types/Tuples/

The Lean standard library includes a variety of tuple-like types.
In practice, they differ in four ways:

- whether the first projection is a type or a proposition
- whether the second projection is a type or a proposition
- whether the second projection's type depends on the first projection's value
- whether the type as a whole is a proposition or type

| Type | First Projection | Second Projection | Dependent? | Universe |
| --- | --- | --- | --- | --- |
| `[Prod]](#manual-Prod___mk)` | `Type u` | `Type v` | ❌️ | `Type (max u v)` |
| `[And]](#manual-And___intro)` | `Prop` | `Prop` | ❌️ | `Prop` |
| `[Sigma]](#manual-Sigma___mk)` | `Type u` | `Type v` | ✔ | `Type (max u v)` |
| `[Subtype](https://lean-lang.org/doc/reference/latest/Basic-Types/Subtypes/#Subtype___mk)` | `Type u` | `Prop` | ✔ | `Type u` |
| `[Exists]](#manual-Exists___intro)` | `Type u` | `Prop` | ✔ | `Prop` |

Some potential rows in this table do not exist in the library:

- There is no dependent pair where the first projection is a proposition, because [proof irrelevance]](#manual---tech-term-proof-irrelevance) renders this meaningless.
- There is no non-dependent pair that combines a type with a proposition because the situation is rare in practice: grouping data with *unrelated* proofs is uncommon.

These differences lead to very different use cases.
`[Prod]](#manual-Prod___mk)` and its variants `[PProd]](#manual-PProd___mk)` and `[MProd]](#manual-MProd___mk)` simply group data together—they are products.
Because its second projection is dependent, `[Sigma]](#manual-Sigma___mk)` has the character of a sum: for each element of the first projection's type, there may be a different type in the second projection.
`[Subtype](https://lean-lang.org/doc/reference/latest/Basic-Types/Subtypes/#Subtype___mk)` selects the values of a type that satisfy a predicate.
Even though it syntactically resembles a pair, in practice it is treated as an actual subset.
`[And]](#manual-And___intro)` is a logical connective, and `[Exists]](#manual-Exists___intro)` is a quantifier.
This chapter documents the tuple-like pairs, namely `[Prod]](#manual-Prod___mk)` and `[Sigma]](#manual-Sigma___mk)`.

### 20.13.1. Ordered Pairs {#manual-pairs}

The type `α × β`, which is a [notation](https://lean-lang.org/doc/reference/latest/Notations-and-Macros/Notations/#--tech-term-notation) for `[Prod]](#manual-Prod___mk) α β`, contains ordered pairs in which the first item is an `α` and the second is a `β`.
These pairs are written in parentheses, separated by commas.
Larger tuples are represented as nested tuples, so `α × β × γ` is equivalent to `α × (β × γ)` and `(x, y, z)` is equivalent to `(x, (y, z))`.

syntaxProduct Types

```lean
term ::= ...
    | term × term
```

The product `[Prod]](#manual-Prod___mk) α β` is written `α × β`.

syntaxPairs

```lean
term ::= ...
    | ([anonymous]term, term)
```

structure

```lean
[Prod.{u, v}]](#manual-Prod___mk) (α : Type u) (β : Type v) : Type (max u v)



[Prod.{u, v}]](#manual-Prod___mk) (α : Type u) (β : Type v) :
  Type (max u v)
```

The product type, usually written `α × β`. Product types are also called pair or tuple types.
Elements of this type are pairs in which the first element is an `α` and the second element is a
`β`.

Products nest to the right, so `(x, y, z) : α × β × γ` is equivalent to `(x, (y, z)) : α × (β × γ)`.

Conventions for notations in identifiers:

- The recommended spelling of `×` in identifiers is `[Prod]](#manual-Prod___mk)`.

Constructor

```lean
[Prod.mk]](#manual-Prod___mk).{u, v}
```

Constructs a pair. This is usually written `(x, y)` instead of `[Prod.mk]](#manual-Prod___mk) x y`.

Conventions for notations in identifiers:

- The recommended spelling of `(a, b)` in identifiers is `mk`.

Fields

```lean
fst : α
```

The first element of a pair.

```lean
snd : β
```

The second element of a pair.

There are also the variants `α ×' β` (which is notation for `[PProd]](#manual-PProd___mk) α β`) and `[MProd]](#manual-MProd___mk)`, which differ with respect to [universe]](#manual---tech-term-universes) levels: like `[PSum](https://lean-lang.org/doc/reference/latest/Basic-Types/Sum-Types/#PSum___inl)`, `[PProd]](#manual-PProd___mk)` allows either `α` or `β` to be a proposition, while `[MProd]](#manual-MProd___mk)` requires that both be types at the *same* universe level.
Generally speaking, `[PProd]](#manual-PProd___mk)` is primarily used in the implementation of proof automation and the elaborator, as it tends to give rise to universe level unification problems that can't be solved.
`[MProd]](#manual-MProd___mk)`, on the other hand, can simplify universe level issues in certain advanced use cases.

syntaxProducts of Arbitrary Sorts

```lean
term ::= ...
    | term ×' term
```

The product `[PProd]](#manual-PProd___mk) α β`, in which both types could be propositions, is written `α × β`.

structure

```lean
[PProd.{u, v}]](#manual-PProd___mk) (α : Sort u) (β : Sort v) : Sort (max (max 1 u) v)



[PProd.{u, v}]](#manual-PProd___mk) (α : Sort u) (β : Sort v) :
  Sort (max (max 1 u) v)
```

A product type in which the types may be propositions, usually written `α ×' β`.

This type is primarily used internally and as an implementation detail of proof automation. It is
rarely useful in hand-written code.

Conventions for notations in identifiers:

- The recommended spelling of `×'` in identifiers is `[PProd]](#manual-PProd___mk)`.

Constructor

```lean
[PProd.mk]](#manual-PProd___mk).{u, v}
```

Fields

```lean
fst : α
```

The first element of a pair.

```lean
snd : β
```

The second element of a pair.

structure

```lean
[MProd.{u}]](#manual-MProd___mk) (α β : Type u) : Type u



[MProd.{u}]](#manual-MProd___mk) (α β : Type u) : Type u
```

A product type in which both `α` and `β` are in the same universe.

It is called `[MProd]](#manual-MProd___mk)` is because it is the *universe-monomorphic* product type.

Constructor

```lean
[MProd.mk]](#manual-MProd___mk).{u}
```

Fields

```lean
fst : α
```

The first element of a pair.

```lean
snd : β
```

The second element of a pair.

#### 20.13.1.1. API Reference {#manual-prod-api}

As a mere pair, the primary API for `[Prod]](#manual-Prod___mk)` is provided by pattern matching and by the first and second projections `[Prod.fst]](#manual-Prod___mk)` and `[Prod.snd]](#manual-Prod___mk)`.

##### 20.13.1.1.1. Transformation {#manual-The-Lean-Language-Reference--Basic-Types--Tuples--Ordered-Pairs--API-Reference--Transformation}

def

```lean
[Prod.map.{u₁, u₂, v₁, v₂}]](#manual-Prod___map) {α₁ : Type u₁} {α₂ : Type u₂} {β₁ : Type v₁}
  {β₂ : Type v₂} (f : α₁ → α₂) (g : β₁ → β₂) : α₁ [×]](#manual-Prod___mk) β₁ → α₂ [×]](#manual-Prod___mk) β₂



[Prod.map.{u₁, u₂, v₁, v₂}]](#manual-Prod___map) {α₁ : Type u₁}
  {α₂ : Type u₂} {β₁ : Type v₁}
  {β₂ : Type v₂} (f : α₁ → α₂)
  (g : β₁ → β₂) : α₁ [×]](#manual-Prod___mk) β₁ → α₂ [×]](#manual-Prod___mk) β₂
```

Transforms a pair by applying functions to both elements.

Examples:

- `(1, 2).[map]](#manual-Prod___map) (· + 1) (· * 3) = (2, 6)`
- `(1, 2).[map]](#manual-Prod___map) toString (· * 3) = ("1", 6)`

def

```lean
[Prod.swap.{u_1, u_2}]](#manual-Prod___swap) {α : Type u_1} {β : Type u_2} : α [×]](#manual-Prod___mk) β → β [×]](#manual-Prod___mk) α



[Prod.swap.{u_1, u_2}]](#manual-Prod___swap) {α : Type u_1}
  {β : Type u_2} : α [×]](#manual-Prod___mk) β → β [×]](#manual-Prod___mk) α
```

Swaps the elements in a pair.

Examples:

- `(1, 2).[swap]](#manual-Prod___swap) = (2, 1)`
- `("orange", -87).[swap]](#manual-Prod___swap) = (-87, "orange")`

##### 20.13.1.1.2. Natural Number Ranges {#manual-The-Lean-Language-Reference--Basic-Types--Tuples--Ordered-Pairs--API-Reference--Natural-Number-Ranges}

def

```lean
[Prod.allI]](#manual-Prod___allI) (i : [Nat]](#manual-Nat___zero) [×]](#manual-Prod___mk) [Nat]](#manual-Nat___zero))
  (f : (j : [Nat]](#manual-Nat___zero)) → i.[fst]](#manual-Prod___mk) [≤]](#manual-LE___mk) j → j [<]](#manual-LT___mk) i.[snd]](#manual-Prod___mk) → [Bool]](#manual-Bool___false)) : [Bool]](#manual-Bool___false)



[Prod.allI]](#manual-Prod___allI) (i : [Nat]](#manual-Nat___zero) [×]](#manual-Prod___mk) [Nat]](#manual-Nat___zero))
  (f :
    (j : [Nat]](#manual-Nat___zero)) →
      i.[fst]](#manual-Prod___mk) [≤]](#manual-LE___mk) j → j [<]](#manual-LT___mk) i.[snd]](#manual-Prod___mk) → [Bool]](#manual-Bool___false)) :
  [Bool]](#manual-Bool___false)
```

Checks whether a predicate holds for all natural numbers in a range.

In particular, `(start, stop).[allI]](#manual-Prod___allI) f` returns true if `f` is true for all natural numbers from
`start` (inclusive) to `stop` (exclusive).

Examples:

- `(5, 8).[allI]](#manual-Prod___allI) (fun j _ _ => j < 10) = (5 < 10) && (6 < 10) && (7 < 10)`
- `(5, 8).[allI]](#manual-Prod___allI) (fun j _ _ => j % 2 = 0) = [false]](#manual-Bool___false)`
- `(6, 7).[allI]](#manual-Prod___allI) (fun j _ _ => j % 2 = 0) = [true]](#manual-Bool___false)`

def

```lean
[Prod.anyI]](#manual-Prod___anyI) (i : [Nat]](#manual-Nat___zero) [×]](#manual-Prod___mk) [Nat]](#manual-Nat___zero))
  (f : (j : [Nat]](#manual-Nat___zero)) → i.[fst]](#manual-Prod___mk) [≤]](#manual-LE___mk) j → j [<]](#manual-LT___mk) i.[snd]](#manual-Prod___mk) → [Bool]](#manual-Bool___false)) : [Bool]](#manual-Bool___false)



[Prod.anyI]](#manual-Prod___anyI) (i : [Nat]](#manual-Nat___zero) [×]](#manual-Prod___mk) [Nat]](#manual-Nat___zero))
  (f :
    (j : [Nat]](#manual-Nat___zero)) →
      i.[fst]](#manual-Prod___mk) [≤]](#manual-LE___mk) j → j [<]](#manual-LT___mk) i.[snd]](#manual-Prod___mk) → [Bool]](#manual-Bool___false)) :
  [Bool]](#manual-Bool___false)
```

Checks whether a predicate holds for any natural number in a range.

In particular, `(start, stop).[allI]](#manual-Prod___allI) f` returns true if `f` is true for any natural number from
`start` (inclusive) to `stop` (exclusive).

Examples:

- `(5, 8).[anyI]](#manual-Prod___anyI) (fun j _ _ => j == 6) = (5 == 6) || (6 == 6) || (7 == 6)`
- `(5, 8).[anyI]](#manual-Prod___anyI) (fun j _ _ => j % 2 = 0) = [true]](#manual-Bool___false)`
- `(6, 6).[anyI]](#manual-Prod___anyI) (fun j _ _ => j % 2 = 0) = [false]](#manual-Bool___false)`

def

```lean
[Prod.foldI.{u}]](#manual-Prod___foldI) {α : Type u} (i : [Nat]](#manual-Nat___zero) [×]](#manual-Prod___mk) [Nat]](#manual-Nat___zero))
  (f : (j : [Nat]](#manual-Nat___zero)) → i.[fst]](#manual-Prod___mk) [≤]](#manual-LE___mk) j → j [<]](#manual-LT___mk) i.[snd]](#manual-Prod___mk) → α → α) (init : α) : α



[Prod.foldI.{u}]](#manual-Prod___foldI) {α : Type u}
  (i : [Nat]](#manual-Nat___zero) [×]](#manual-Prod___mk) [Nat]](#manual-Nat___zero))
  (f :
    (j : [Nat]](#manual-Nat___zero)) →
      i.[fst]](#manual-Prod___mk) [≤]](#manual-LE___mk) j → j [<]](#manual-LT___mk) i.[snd]](#manual-Prod___mk) → α → α)
  (init : α) : α
```

Combines an initial value with each natural number from a range, in increasing order.

In particular, `(start, stop).[foldI]](#manual-Prod___foldI) f init` applies `f`on all the numbers
from `start` (inclusive) to `stop` (exclusive) in increasing order:

Examples:

- `(5, 8).[foldI]](#manual-Prod___foldI) (fun j _ _ xs => xs.[push](https://lean-lang.org/doc/reference/latest/Basic-Types/Arrays/#Array___push) j) #[] = (#[] |>.[push](https://lean-lang.org/doc/reference/latest/Basic-Types/Arrays/#Array___push) 5 |>.[push](https://lean-lang.org/doc/reference/latest/Basic-Types/Arrays/#Array___push) 6 |>.[push](https://lean-lang.org/doc/reference/latest/Basic-Types/Arrays/#Array___push) 7)`
- `(5, 8).[foldI]](#manual-Prod___foldI) (fun j _ _ xs => xs.[push](https://lean-lang.org/doc/reference/latest/Basic-Types/Arrays/#Array___push) j) #[] = #[5, 6, 7]`
- `(5, 8).[foldI]](#manual-Prod___foldI) (fun j _ _ xs => toString j :: xs) [] = ["7", "6", "5"]`

##### 20.13.1.1.3. Ordering {#manual-The-Lean-Language-Reference--Basic-Types--Tuples--Ordered-Pairs--API-Reference--Ordering}

def

```lean
[Prod.lexLt.{u_1, u_2}]](#manual-Prod___lexLt) {α : Type u_1} {β : Type u_2} [[LT]](#manual-LT___mk) α] [[LT]](#manual-LT___mk) β]
  (s t : α [×]](#manual-Prod___mk) β) : Prop



[Prod.lexLt.{u_1, u_2}]](#manual-Prod___lexLt) {α : Type u_1}
  {β : Type u_2} [[LT]](#manual-LT___mk) α] [[LT]](#manual-LT___mk) β]
  (s t : α [×]](#manual-Prod___mk) β) : Prop
```

Lexicographical order for products.

Two pairs are lexicographically ordered if their first elements are ordered or if their first
elements are equal and their second elements are ordered.

### 20.13.2. Dependent Pairs {#manual-sigma-types}

*Dependent pairs*, also known as *dependent sums* or *Σ-types*, are pairs in which the second term's type may depend on the *value* of the first term.
They are closely related to the existential quantifier and `[Subtype](https://lean-lang.org/doc/reference/latest/Basic-Types/Subtypes/#Subtype___mk)`.
Unlike existentially quantified statements, dependent pairs are in the `Type` universe and are computationally relevant data.
Unlike subtypes, the second term is also computationally relevant data.
Like ordinary pairs, dependent pairs may be nested; this nesting is right-associative.

syntaxDependent Pair Types

```lean
term ::= ...
    | (ident : term) × term
```

```lean
term ::= ...
    | Σ ident ident* (: term)?, term
```

```lean
term ::= ...
    | Σ (ident ident* : term), term
```

Dependent pair types bind one or more variables, which are then in scope in the final term.
If there is one variable, then its type is a that of the first element in the pair and the final term is the type of the second element in the pair.
If there is more than one variable, the types are nested right-associatively.
The identifiers may also be `_`.
With parentheses, multiple bound variables may have different types, while the unparenthesized variant requires that all have the same type.

**Example: Nested Dependent Pair Types**

The type

```lean
Σ n k : [Nat]](#manual-Nat___zero), [Fin]](#manual-Fin___mk) (n * k)
```

is equivalent to

```lean
Σ n : [Nat]](#manual-Nat___zero), Σ k : [Nat]](#manual-Nat___zero), [Fin]](#manual-Fin___mk) (n * k)
```

and

```lean
(n : [Nat]](#manual-Nat___zero)) × (k : [Nat]](#manual-Nat___zero)) × [Fin]](#manual-Fin___mk) (n * k)
```

The type

```lean
Σ (n k : [Nat]](#manual-Nat___zero)) (i : [Fin]](#manual-Fin___mk) (n * k)) , [Fin]](#manual-Fin___mk) i.[val]](#manual-Fin___mk)
```

is equivalent to

```lean
Σ (n : [Nat]](#manual-Nat___zero)), Σ (k : [Nat]](#manual-Nat___zero)), Σ (i : [Fin]](#manual-Fin___mk) (n * k)) , [Fin]](#manual-Fin___mk) i.[val]](#manual-Fin___mk)
```

and

```lean
(n : [Nat]](#manual-Nat___zero)) × (k : [Nat]](#manual-Nat___zero)) × (i : [Fin]](#manual-Fin___mk) (n * k)) × [Fin]](#manual-Fin___mk) i.[val]](#manual-Fin___mk)
```

The two styles of annotation cannot be mixed in a single `Σ`-type:

```lean
```lean
Σ n k (i : Fin (n * k)) , Fin i.val
```
```

```lean
<example>:1:5-1:7: unexpected token '('; expected ','
```

Dependent pairs are typically used in one of two ways:

1. They can be used to “package” a concrete type index together with a value of the indexed family, used when the index value is not known ahead of time.
   The type `Σ n, [Fin]](#manual-Fin___mk) n` is a pair of a natural number and some other number that's strictly smaller.
   This is the most common way to use dependent pairs.
2. The first element can be thought of as a “tag” that's used to select from among different types for the second term.
   This is similar to the way that selecting a constructor of a sum type determines the types of the constructor's arguments.
   For example, the type

   ```lean
   Σ (b : [Bool]](#manual-Bool___false)), [if]](#manual-termIfThenElse) b [then]](#manual-termIfThenElse) [Unit]](#manual-Unit) [else]](#manual-termIfThenElse) α
   ```

   is equivalent to `[Option]](#manual-Option___none) α`, where `[none]](#manual-Option___none)` is `⟨[true]](#manual-Bool___false), ()⟩` and `[some]](#manual-Option___none) x` is `⟨[false]](#manual-Bool___false), x⟩`.
   Using dependent pairs this way is uncommon, because it's typically much easier to define a special-purpose [inductive type]](#manual---tech-term-Inductive-types) directly.

structure

```lean
[Sigma.{u, v}]](#manual-Sigma___mk) {α : Type u} (β : α → Type v) : Type (max u v)



[Sigma.{u, v}]](#manual-Sigma___mk) {α : Type u}
  (β : α → Type v) : Type (max u v)
```

Dependent pairs, in which the second element's type depends on the value of the first element. The
type `[Sigma]](#manual-Sigma___mk) β` is typically written `Σ a : α, β a` or `(a : α) × β a`.

Although its values are pairs, `[Sigma]](#manual-Sigma___mk)` is sometimes known as the *dependent sum type*, since it is
the type level version of an indexed summation.

Constructor

```lean
[Sigma.mk]](#manual-Sigma___mk).{u, v}
```

Constructs a dependent pair.

Using this constructor in a context in which the type is not known usually requires a type
ascription to determine `β`. This is because the desired relationship between the two values can't
generally be determined automatically.

Fields

```lean
fst : α
```

The first component of a dependent pair.

```lean
snd : β self.[fst]](#manual-Sigma___mk)
```

The second component of a dependent pair. Its type depends on the first component.

**Example: Dependent Pairs with Data**

The type `Vector`, which associates a known length with an array, can be placed in a dependent pair with the length itself.
While this is logically equivalent to just using `[Array](https://lean-lang.org/doc/reference/latest/Basic-Types/Arrays/#Array___mk)`, this construction is sometimes necessary to bridge gaps in an API.

```lean
def getNLinesRev : (n : [Nat]](#manual-Nat___zero)) → [IO](https://lean-lang.org/doc/reference/latest/IO/Logical-Model/#IO) (Vector [String]](#manual-String___ofByteArray) n)
| 0 => [pure]](#manual-Pure___mk) #v[]
| n + 1 => [do]](#manual-Lean___Parser___Term___do)
let xs ← [getNLinesRev]](#manual-getNLinesRev-_LPAR_in-Dependent-Pairs-with-Data_RPAR_) n
return xs.push (← (← [IO.getStdin](https://lean-lang.org/doc/reference/latest/IO/Files___-File-Handles___-and-Streams/#IO___getStdin)).[getLine](https://lean-lang.org/doc/reference/latest/IO/Files___-File-Handles___-and-Streams/#IO___FS___Stream___mk))
def getNLines (n : [Nat]](#manual-Nat___zero)) : [IO](https://lean-lang.org/doc/reference/latest/IO/Logical-Model/#IO) (Vector [String]](#manual-String___ofByteArray) n) := [do]](#manual-Lean___Parser___Term___do)
return (← [getNLinesRev]](#manual-getNLinesRev-_LPAR_in-Dependent-Pairs-with-Data_RPAR_) n).reverse
partial def getValues : [IO](https://lean-lang.org/doc/reference/latest/IO/Logical-Model/#IO) (Σ n, Vector [String]](#manual-String___ofByteArray) n) := [do]](#manual-Lean___Parser___Term___do)
let stdin ← [IO.getStdin](https://lean-lang.org/doc/reference/latest/IO/Files___-File-Handles___-and-Streams/#IO___getStdin)
[IO.println](https://lean-lang.org/doc/reference/latest/IO/Console-Output/#IO___println) "How many lines to read?"
let howMany ← stdin.[getLine](https://lean-lang.org/doc/reference/latest/IO/Files___-File-Handles___-and-Streams/#IO___FS___Stream___mk)
if let [some]](#manual-Option___none) howMany := howMany.[trimAscii]](#manual-String___trimAscii).[copy]](#manual-String___Slice___copy).[toNat?]](#manual-String___toNat___) then
return ⟨howMany, (← [getNLines]](#manual-getNLines-_LPAR_in-Dependent-Pairs-with-Data_RPAR_) howMany)⟩
else
[IO.eprintln](https://lean-lang.org/doc/reference/latest/IO/Console-Output/#IO___eprintln) "Please enter a number."
[getValues]](#manual-getValues-_LPAR_in-Dependent-Pairs-with-Data_RPAR_)
def main : [IO](https://lean-lang.org/doc/reference/latest/IO/Logical-Model/#IO) [Unit]](#manual-Unit) := [do]](#manual-Lean___Parser___Term___do)
let values ← [getValues]](#manual-getValues-_LPAR_in-Dependent-Pairs-with-Data_RPAR_)
[IO.println](https://lean-lang.org/doc/reference/latest/IO/Console-Output/#IO___println) s!"Got {values.[fst]](#manual-Sigma___mk)} values. They are:"
for x in values.[snd]](#manual-Sigma___mk) do
[IO.println](https://lean-lang.org/doc/reference/latest/IO/Console-Output/#IO___println) x.[trimAscii]](#manual-String___trimAscii)
```

When calling the program with this standard input:

`stdin``4``Apples``Quince``Plums``Raspberries`

the output is:

`stdout``How many lines to read?``Got 4 values. They are:``Raspberries``Plums``Quince``Apples`

**Example: Dependent Pairs as Sums**

`[Sigma]](#manual-Sigma___mk)` can be used to implement sum types.
The `[Bool]](#manual-Bool___false)` in the first projection of `[Sum']](#manual-Sum___-_LPAR_in-Dependent-Pairs-as-Sums_RPAR_)` indicates which type the second projection is drawn from.

```lean
def Sum' (α : Type) (β : Type) : Type :=
Σ (b : [Bool]](#manual-Bool___false)),
[match]](#manual-Lean___Parser___Term___match) b [with]](#manual-Lean___Parser___Term___match)
| [true]](#manual-Bool___false) => α
| [false]](#manual-Bool___false) => β
```

The injections pair a tag (a `[Bool]](#manual-Bool___false)`) with a value of the indicated type.
Annotating them with `match_pattern` allows them to be used in patterns as well as in ordinary terms.

```lean
[variable]](#manual-Lean___Parser___Command___variable) {α β : Type}
@[match_pattern]
def Sum'.inl (x : α) : [Sum']](#manual-Sum___-_LPAR_in-Dependent-Pairs-as-Sums_RPAR_) α β := ⟨[true]](#manual-Bool___false), x⟩
@[match_pattern]
def Sum'.inr (x : β) : [Sum']](#manual-Sum___-_LPAR_in-Dependent-Pairs-as-Sums_RPAR_) α β := ⟨[false]](#manual-Bool___false), x⟩
def Sum'.swap : [Sum']](#manual-Sum___-_LPAR_in-Dependent-Pairs-as-Sums_RPAR_) α β → [Sum']](#manual-Sum___-_LPAR_in-Dependent-Pairs-as-Sums_RPAR_) β α
| [.inl]](#manual-Sum______inl-_LPAR_in-Dependent-Pairs-as-Sums_RPAR_) x => [.inr]](#manual-Sum______inr-_LPAR_in-Dependent-Pairs-as-Sums_RPAR_) x
| [.inr]](#manual-Sum______inr-_LPAR_in-Dependent-Pairs-as-Sums_RPAR_) y => [.inl]](#manual-Sum______inl-_LPAR_in-Dependent-Pairs-as-Sums_RPAR_) y
```

Just as `[Prod]](#manual-Prod___mk)` has a variant `[PProd]](#manual-PProd___mk)` that accepts propositions as well as types, `[PSigma]](#manual-PSigma___mk)` allows its projections to be propositions.
This has the same drawbacks as `[PProd]](#manual-PProd___mk)`: it is much more likely to lead to failures of universe level unification.
However, `[PSigma]](#manual-PSigma___mk)` can be necessary when implementing custom proof automation or in some rare, advanced use cases.

syntaxFully-Polymorphic Dependent Pair Types

```lean
term ::= ...
    | Σ' ident ident* (: term)? , term
```

```lean
term ::= ...
    | Σ' (ident ident* : term), term
```

The rules for nesting `Σ'`, as well as those that govern its binding structure, are the same as those for `Σ`.

structure

```lean
[PSigma.{u, v}]](#manual-PSigma___mk) {α : Sort u} (β : α → Sort v) : Sort (max (max 1 u) v)



[PSigma.{u, v}]](#manual-PSigma___mk) {α : Sort u}
  (β : α → Sort v) :
  Sort (max (max 1 u) v)
```

Fully universe-polymorphic dependent pairs, in which the second element's type depends on the value
of the first element and both types are allowed to be propositions. The type `[PSigma]](#manual-PSigma___mk) β` is typically
written `Σ' a : α, β a` or `(a : α) ×' β a`.

In practice, this generality leads to universe level constraints that are difficult to solve, so
`[PSigma]](#manual-PSigma___mk)` is rarely used in manually-written code. It is usually only used in automation that
constructs pairs of arbitrary types.

To pair a value with a proof that a predicate holds for it, use `[Subtype](https://lean-lang.org/doc/reference/latest/Basic-Types/Subtypes/#Subtype___mk)`. To demonstrate that a
value exists that satisfies a predicate, use `[Exists]](#manual-Exists___intro)`. A dependent pair with a proposition as its
first component is not typically useful due to proof irrelevance: there's no point in depending on a
specific proof because all proofs are equal anyway.

Constructor

```lean
[PSigma.mk]](#manual-PSigma___mk).{u, v}
```

Constructs a fully universe-polymorphic dependent pair.

Fields

```lean
fst : α
```

The first component of a dependent pair.

```lean
snd : β self.[fst]](#manual-PSigma___mk)
```

The second component of a dependent pair. Its type depends on the first component.

---



## Basic Types — 20.14. Sum Types {#manual-basic-types-2014-sum-types}

> 📄 Source: https://lean-lang.org/doc/reference/latest/Basic-Types/Sum-Types/

*Sum types* represent a choice between two types: an element of the sum is an element of one of the other types, paired with an indication of which type it came from.
Sums are also known as disjoint unions, discriminated unions, or tagged unions.
The constructors of a sum are also called *injections*; mathematically, they can be considered as injective functions from each summand to the sum.

There are two varieties of the sum type:

- `[Sum]](#manual-Sum___inl)` is [polymorphic]](#manual---tech-term-universe-polymorphism) over all `Type` [universes]](#manual---tech-term-universes), and is never a [proposition]](#manual---tech-term-Propositions).
- `[PSum]](#manual-PSum___inl)` is allows the summands to be propositions or types. Unlike `[Or]](#manual-Or___inl)`, the `[PSum]](#manual-PSum___inl)` of two propositions is still a type, and non-propositional code can check which injection was used to construct a given value.

Manually-written Lean code almost always uses only `[Sum]](#manual-Sum___inl)`, while `[PSum]](#manual-PSum___inl)` is used as part of the implementation of proof automation.
This is because it imposes problematic constraints that universe level unification cannot solve.
In particular, this type is in the universe `Sort (max 1 u v)`, which can cause problems for universe level unification because the equation `max 1 u v = ?u + 1` has no solution in level arithmetic.
`PSum` is usually only used in automation that constructs sums of arbitrary types.

inductive type

```lean
[Sum.{u, v}]](#manual-Sum___inl) (α : Type u) (β : Type v) : Type (max u v)



[Sum.{u, v}]](#manual-Sum___inl) (α : Type u) (β : Type v) :
  Type (max u v)
```

The disjoint union of types `α` and `β`, ordinarily written `α ⊕ β`.

An element of `α ⊕ β` is either an `a : α` wrapped in `[Sum.inl]](#manual-Sum___inl)` or a `b : β` wrapped in `[Sum.inr]](#manual-Sum___inl)`.
`α ⊕ β` is not equivalent to the set-theoretic union of `α` and `β` because its values include an
indication of which of the two types was chosen. The union of a singleton set with itself contains
one element, while `[Unit]](#manual-Unit) ⊕ [Unit]](#manual-Unit)` contains distinct values `inl ()` and `inr ()`.

Constructors

```lean
[Sum.inl.{u, v}]](#manual-Sum___inl) {α : Type u} {β : Type v} (val : α) : α [⊕]](#manual-Sum___inl) β
```

Left injection into the sum type `α ⊕ β`.

```lean
[Sum.inr.{u, v}]](#manual-Sum___inl) {α : Type u} {β : Type v} (val : β) : α [⊕]](#manual-Sum___inl) β
```

Right injection into the sum type `α ⊕ β`.

inductive type

```lean
[PSum.{u, v}]](#manual-PSum___inl) (α : Sort u) (β : Sort v) : Sort (max (max 1 u) v)



[PSum.{u, v}]](#manual-PSum___inl) (α : Sort u) (β : Sort v) :
  Sort (max (max 1 u) v)
```

The disjoint union of arbitrary sorts `α` `β`, or `α ⊕' β`.

It differs from `α ⊕ β` in that it allows `α` and `β` to have arbitrary sorts `Sort u` and `Sort v`,
instead of restricting them to `Type u` and `Type v`. This means that it can be used in situations
where one side is a proposition, like `[True]](#manual-True___intro) ⊕' [Nat]](#manual-Nat___zero)`. However, the resulting universe level
constraints are often more difficult to solve than those that result from `[Sum]](#manual-Sum___inl)`.

Constructors

```lean
[PSum.inl.{u, v}]](#manual-PSum___inl) {α : Sort u} {β : Sort v} (val : α) : α [⊕']](#manual-PSum___inl) β
```

Left injection into the sum type `α ⊕' β`.

```lean
[PSum.inr.{u, v}]](#manual-PSum___inl) {α : Sort u} {β : Sort v} (val : β) : α [⊕']](#manual-PSum___inl) β
```

Right injection into the sum type `α ⊕' β`.

### 20.14.1. Syntax {#manual-sum-syntax}

The names `[Sum]](#manual-Sum___inl)` and `[PSum]](#manual-PSum___inl)` are rarely written explicitly.
Most code uses the corresponding infix operators.

syntaxSum Types

```lean
term ::= ...
    | term ⊕ term
```

`α ⊕ β` is notation for `[Sum]](#manual-Sum___inl) α β`.

syntaxPotentially-Propositional Sum Types

```lean
term ::= ...
    | term ⊕' term
```

`α ⊕' β` is notation for `[PSum]](#manual-PSum___inl) α β`.

### 20.14.2. API Reference {#manual-sum-api}

Sum types are primarily used with [pattern matching]](#manual---tech-term-Pattern-matching) rather than explicit function calls from an API.
As such, their primary API is the constructors `[inl]](#manual-Sum___inl)` and `[inr]](#manual-Sum___inl)`.

#### 20.14.2.1. Case Distinction {#manual-The-Lean-Language-Reference--Basic-Types--Sum-Types--API-Reference--Case-Distinction}

def

```lean
[Sum.isLeft.{u_1, u_2}]](#manual-Sum___isLeft) {α : Type u_1} {β : Type u_2} : α [⊕]](#manual-Sum___inl) β → [Bool]](#manual-Bool___false)



[Sum.isLeft.{u_1, u_2}]](#manual-Sum___isLeft) {α : Type u_1}
  {β : Type u_2} : α [⊕]](#manual-Sum___inl) β → [Bool]](#manual-Bool___false)
```

Checks whether a sum is the left injection `inl`.

def

```lean
[Sum.isRight.{u_1, u_2}]](#manual-Sum___isRight) {α : Type u_1} {β : Type u_2} : α [⊕]](#manual-Sum___inl) β → [Bool]](#manual-Bool___false)



[Sum.isRight.{u_1, u_2}]](#manual-Sum___isRight) {α : Type u_1}
  {β : Type u_2} : α [⊕]](#manual-Sum___inl) β → [Bool]](#manual-Bool___false)
```

Checks whether a sum is the right injection `inr`.

#### 20.14.2.2. Extracting Values {#manual-The-Lean-Language-Reference--Basic-Types--Sum-Types--API-Reference--Extracting-Values}

def

```lean
[Sum.elim.{u_1, u_2, u_3}]](#manual-Sum___elim) {α : Type u_1} {β : Type u_2} {γ : Sort u_3}
  (f : α → γ) (g : β → γ) : α [⊕]](#manual-Sum___inl) β → γ



[Sum.elim.{u_1, u_2, u_3}]](#manual-Sum___elim) {α : Type u_1}
  {β : Type u_2} {γ : Sort u_3}
  (f : α → γ) (g : β → γ) : α [⊕]](#manual-Sum___inl) β → γ
```

Case analysis for sums that applies the appropriate function `f` or `g` after checking which
constructor is present.

def

```lean
[Sum.getLeft.{u_1, u_2}]](#manual-Sum___getLeft) {α : Type u_1} {β : Type u_2} (ab : α [⊕]](#manual-Sum___inl) β) :
  ab.[isLeft]](#manual-Sum___isLeft) [=]](#manual-Eq___refl) [true]](#manual-Bool___false) → α



[Sum.getLeft.{u_1, u_2}]](#manual-Sum___getLeft) {α : Type u_1}
  {β : Type u_2} (ab : α [⊕]](#manual-Sum___inl) β) :
  ab.[isLeft]](#manual-Sum___isLeft) [=]](#manual-Eq___refl) [true]](#manual-Bool___false) → α
```

Retrieves the contents from a sum known to be `inl`.

def

```lean
[Sum.getLeft?.{u_1, u_2}]](#manual-Sum___getLeft___) {α : Type u_1} {β : Type u_2} : α [⊕]](#manual-Sum___inl) β → [Option]](#manual-Option___none) α



[Sum.getLeft?.{u_1, u_2}]](#manual-Sum___getLeft___) {α : Type u_1}
  {β : Type u_2} : α [⊕]](#manual-Sum___inl) β → [Option]](#manual-Option___none) α
```

Checks whether a sum is the left injection `inl` and, if so, retrieves its contents.

def

```lean
[Sum.getRight.{u_1, u_2}]](#manual-Sum___getRight) {α : Type u_1} {β : Type u_2} (ab : α [⊕]](#manual-Sum___inl) β) :
  ab.[isRight]](#manual-Sum___isRight) [=]](#manual-Eq___refl) [true]](#manual-Bool___false) → β



[Sum.getRight.{u_1, u_2}]](#manual-Sum___getRight) {α : Type u_1}
  {β : Type u_2} (ab : α [⊕]](#manual-Sum___inl) β) :
  ab.[isRight]](#manual-Sum___isRight) [=]](#manual-Eq___refl) [true]](#manual-Bool___false) → β
```

Retrieves the contents from a sum known to be `inr`.

def

```lean
[Sum.getRight?.{u_1, u_2}]](#manual-Sum___getRight___) {α : Type u_1} {β : Type u_2} :
  α [⊕]](#manual-Sum___inl) β → [Option]](#manual-Option___none) β



[Sum.getRight?.{u_1, u_2}]](#manual-Sum___getRight___) {α : Type u_1}
  {β : Type u_2} : α [⊕]](#manual-Sum___inl) β → [Option]](#manual-Option___none) β
```

Checks whether a sum is the right injection `inr` and, if so, retrieves its contents.

#### 20.14.2.3. Transformations {#manual-The-Lean-Language-Reference--Basic-Types--Sum-Types--API-Reference--Transformations}

def

```lean
[Sum.map.{u_1, u_2, u_3, u_4}]](#manual-Sum___map) {α : Type u_1} {α' : Type u_2}
  {β : Type u_3} {β' : Type u_4} (f : α → α') (g : β → β') :
  α [⊕]](#manual-Sum___inl) β → α' [⊕]](#manual-Sum___inl) β'



[Sum.map.{u_1, u_2, u_3, u_4}]](#manual-Sum___map)
  {α : Type u_1} {α' : Type u_2}
  {β : Type u_3} {β' : Type u_4}
  (f : α → α') (g : β → β') :
  α [⊕]](#manual-Sum___inl) β → α' [⊕]](#manual-Sum___inl) β'
```

Transforms a sum according to functions on each type.

This function maps `α ⊕ β` to `α' ⊕ β'`, sending `α` to `α'` and `β` to `β'`.

def

```lean
[Sum.swap.{u_1, u_2}]](#manual-Sum___swap) {α : Type u_1} {β : Type u_2} : α [⊕]](#manual-Sum___inl) β → β [⊕]](#manual-Sum___inl) α



[Sum.swap.{u_1, u_2}]](#manual-Sum___swap) {α : Type u_1}
  {β : Type u_2} : α [⊕]](#manual-Sum___inl) β → β [⊕]](#manual-Sum___inl) α
```

Swaps the factors of a sum type.

The constructor `[Sum.inl]](#manual-Sum___inl)` is replaced with `[Sum.inr]](#manual-Sum___inl)`, and vice versa.

#### 20.14.2.4. Inhabited {#manual-The-Lean-Language-Reference--Basic-Types--Sum-Types--API-Reference--Inhabited}

The `[Inhabited]](#manual-Inhabited___mk)` definitions for `[Sum]](#manual-Sum___inl)` and `[PSum]](#manual-PSum___inl)` are not registered as instances.
This is because there are two separate ways to construct a default value (via `[inl]](#manual-Sum___inl)` or `[inr]](#manual-Sum___inl)`), and instance synthesis might result in either choice.
The result could be situations where two identically-written terms elaborate differently and are not [definitionally equal]](#manual---tech-term-definitional-equality).

Both types have `[Nonempty]](#manual-Nonempty___intro)` instances, for which [proof irrelevance]](#manual---tech-term-proof-irrelevance) makes the choice of `[inl]](#manual-Sum___inl)` or `[inr]](#manual-Sum___inl)` not matter.
This is enough to enable `partial` functions.
For situations that require an `[Inhabited]](#manual-Inhabited___mk)` instance, such as programs that use `panic!`, the instance can be explicitly used by adding it to the local context with `have` or `let`.

**Example: Inhabited Sum Types**

In Lean's logic, `panic!` is equivalent to the default value specified in its type's `[Inhabited]](#manual-Inhabited___mk)` instance.
This means that the type must have such an instance—a `[Nonempty]](#manual-Nonempty___intro)` instance combined with the axiom of choice would render the program non-computable.

Products have the right instance:

```lean
example : [Nat]](#manual-Nat___zero) × [String]](#manual-String___ofByteArray) := panic! "Can't find it"
```

Sums do not, by default:

```lean
example : [Nat]](#manual-Nat___zero) ⊕ [String]](#manual-String___ofByteArray) := panic! "Can't find it"
```

```lean
failed to synthesize instance of type class
  [Inhabited]](#manual-Inhabited___mk) [(]](#manual-Sum___inl)[Nat]](#manual-Nat___zero) [⊕]](#manual-Sum___inl) [String]](#manual-String___ofByteArray)[)]](#manual-Sum___inl)

Hint: Type class instance resolution failures can be inspected with the `set_option trace.Meta.synthInstance true` command.
```

The desired instance can be made available to instance synthesis using `have`:

```lean
example : [Nat]](#manual-Nat___zero) ⊕ [String]](#manual-String___ofByteArray) :=
have : [Inhabited]](#manual-Inhabited___mk) ([Nat]](#manual-Nat___zero) ⊕ [String]](#manual-String___ofByteArray)) := [Sum.inhabitedLeft]](#manual-Sum___inhabitedLeft)
panic! "Can't find it"
```

def

```lean
[Sum.inhabitedLeft.{u, v}]](#manual-Sum___inhabitedLeft) {α : Type u} {β : Type v} [[Inhabited]](#manual-Inhabited___mk) α] :
  [Inhabited]](#manual-Inhabited___mk) [(]](#manual-Sum___inl)α [⊕]](#manual-Sum___inl) β[)]](#manual-Sum___inl)



[Sum.inhabitedLeft.{u, v}]](#manual-Sum___inhabitedLeft) {α : Type u}
  {β : Type v} [[Inhabited]](#manual-Inhabited___mk) α] :
  [Inhabited]](#manual-Inhabited___mk) [(]](#manual-Sum___inl)α [⊕]](#manual-Sum___inl) β[)]](#manual-Sum___inl)
```

If the left type in a sum is inhabited then the sum is inhabited.

This is not an instance to avoid non-canonical instances when both the left and right types are
inhabited.

def

```lean
[Sum.inhabitedRight.{u, v}]](#manual-Sum___inhabitedRight) {α : Type u} {β : Type v} [[Inhabited]](#manual-Inhabited___mk) β] :
  [Inhabited]](#manual-Inhabited___mk) [(]](#manual-Sum___inl)α [⊕]](#manual-Sum___inl) β[)]](#manual-Sum___inl)



[Sum.inhabitedRight.{u, v}]](#manual-Sum___inhabitedRight) {α : Type u}
  {β : Type v} [[Inhabited]](#manual-Inhabited___mk) β] :
  [Inhabited]](#manual-Inhabited___mk) [(]](#manual-Sum___inl)α [⊕]](#manual-Sum___inl) β[)]](#manual-Sum___inl)
```

If the right type in a sum is inhabited then the sum is inhabited.

This is not an instance to avoid non-canonical instances when both the left and right types are
inhabited.

def

```lean
[PSum.inhabitedLeft.{u_1, u_2}]](#manual-PSum___inhabitedLeft) {α : Sort u_1} {β : Sort u_2}
  [[Inhabited]](#manual-Inhabited___mk) α] : [Inhabited]](#manual-Inhabited___mk) [(]](#manual-PSum___inl)α [⊕']](#manual-PSum___inl) β[)]](#manual-PSum___inl)



[PSum.inhabitedLeft.{u_1, u_2}]](#manual-PSum___inhabitedLeft)
  {α : Sort u_1} {β : Sort u_2}
  [[Inhabited]](#manual-Inhabited___mk) α] : [Inhabited]](#manual-Inhabited___mk) [(]](#manual-PSum___inl)α [⊕']](#manual-PSum___inl) β[)]](#manual-PSum___inl)
```

If the left type in a sum is inhabited then the sum is inhabited.

This is not an instance to avoid non-canonical instances when both the left and right types are
inhabited.

def

```lean
[PSum.inhabitedRight.{u_1, u_2}]](#manual-PSum___inhabitedRight) {α : Sort u_1} {β : Sort u_2}
  [[Inhabited]](#manual-Inhabited___mk) β] : [Inhabited]](#manual-Inhabited___mk) [(]](#manual-PSum___inl)α [⊕']](#manual-PSum___inl) β[)]](#manual-PSum___inl)



[PSum.inhabitedRight.{u_1, u_2}]](#manual-PSum___inhabitedRight)
  {α : Sort u_1} {β : Sort u_2}
  [[Inhabited]](#manual-Inhabited___mk) β] : [Inhabited]](#manual-Inhabited___mk) [(]](#manual-PSum___inl)α [⊕']](#manual-PSum___inl) β[)]](#manual-PSum___inl)
```

If the right type in a sum is inhabited then the sum is inhabited.

This is not an instance to avoid non-canonical instances when both the left and right types are
inhabited.

---



## Basic Types — 20.15. Linked Lists {#manual-basic-types-2015-linked-lists}

> 📄 Source: https://lean-lang.org/doc/reference/latest/Basic-Types/Linked-Lists/

Linked lists, implemented as the [inductive type]](#manual---tech-term-Inductive-types) `[List]](#manual-List___nil)`, contain an ordered sequence of elements.
Unlike [arrays](https://lean-lang.org/doc/reference/latest/Basic-Types/Arrays/#Array), Lean compiles lists according to the ordinary rules for inductive types; however, some operations on lists are replaced by tail-recursive equivalents in compiled code using the `csimp` mechanism.
Lean provides syntax for both literal lists and the constructor `[List.cons]](#manual-List___nil)`.

inductive type

```lean
[List.{u}]](#manual-List___nil) (α : Type u) : Type u



[List.{u}]](#manual-List___nil) (α : Type u) : Type u
```

Linked lists: ordered lists, in which each element has a reference to the next element.

Most operations on linked lists take time proportional to the length of the list, because each
element must be traversed to find the next element.

`[List]](#manual-List___nil) α` is isomorphic to `[Array](https://lean-lang.org/doc/reference/latest/Basic-Types/Arrays/#Array___mk) α`, but they are useful for different things:

- `[List]](#manual-List___nil) α` is easier for reasoning, and `[Array](https://lean-lang.org/doc/reference/latest/Basic-Types/Arrays/#Array___mk) α` is modeled as a wrapper around `[List]](#manual-List___nil) α`.
- `[List]](#manual-List___nil) α` works well as a persistent data structure, when many copies of the tail are shared. When
  the value is not shared, `[Array](https://lean-lang.org/doc/reference/latest/Basic-Types/Arrays/#Array___mk) α` will have better performance because it can do destructive
  updates.

Constructors

```lean
[List.nil.{u}]](#manual-List___nil) {α : Type u} : [List]](#manual-List___nil) α
```

The empty list, usually written `[]`.

Conventions for notations in identifiers:

- The recommended spelling of `[]` in identifiers is `[nil]](#manual-List___nil)`.

```lean
[List.cons.{u}]](#manual-List___nil) {α : Type u} (head : α) (tail : [List]](#manual-List___nil) α) :
  [List]](#manual-List___nil) α
```

The list whose first element is `[head]](#manual-List___head)`, where `[tail]](#manual-List___tail)` is the rest of the list.
Usually written `head :: tail`.

Conventions for notations in identifiers:

- The recommended spelling of `::` in identifiers is `[cons]](#manual-List___nil)`.
- The recommended spelling of `[a]` in identifiers is `singleton`.

### 20.15.1. Syntax {#manual-list-syntax}

List literals are written in square brackets, with the elements of the list separated by commas.
The constructor `[List.cons]](#manual-List___nil)` that adds an element to the front of a list is represented by the infix operator [`::`]](#manual-_FLQQ_term_________FLQQ_-next-next-next-next-next-next-next-next).
The syntax for lists can be used both in ordinary terms and in patterns.

syntaxList Literals

```lean
term ::= ...
    | [term,*]
```

The syntax `[a, b, c]` is shorthand for `a :: b :: c :: []`, or
`[List.cons]](#manual-List___nil) a ([List.cons]](#manual-List___nil) b ([List.cons]](#manual-List___nil) c [List.nil]](#manual-List___nil)))`. It allows conveniently constructing
list literals.

For lists of length at least 64, an alternative desugaring strategy is used
which uses let bindings as intermediates as in
`let left := [d, e, f]; a :: b :: c :: left` to avoid creating very deep expressions.
Note that this changes the order of evaluation, although it should not be observable
unless you use side effecting operations like `[dbg_trace]](#manual-dbg_trace)`.

Conventions for notations in identifiers:

- The recommended spelling of `[]` in identifiers is `nil`.
- The recommended spelling of `[a]` in identifiers is `singleton`.

syntaxList Construction

```lean
term ::= ...
    | term :: term
```

The list whose first element is `head`, where `tail` is the rest of the list.
Usually written `head :: tail`.

Conventions for notations in identifiers:

- The recommended spelling of `::` in identifiers is `cons`.

**Example: Constructing Lists**

All of these examples are equivalent:

```lean
example : [List]](#manual-List___nil) [Nat]](#manual-Nat___zero) := [1, 2, 3]
example : [List]](#manual-List___nil) [Nat]](#manual-Nat___zero) := 1 :: [2, 3]
example : [List]](#manual-List___nil) [Nat]](#manual-Nat___zero) := 1 :: 2 :: [3]
example : [List]](#manual-List___nil) [Nat]](#manual-Nat___zero) := 1 :: 2 :: 3 :: []
example : [List]](#manual-List___nil) [Nat]](#manual-Nat___zero) := 1 :: 2 :: 3 :: [.nil]](#manual-List___nil)
example : [List]](#manual-List___nil) [Nat]](#manual-Nat___zero) := 1 :: 2 :: [.cons]](#manual-List___nil) 3 [.nil]](#manual-List___nil)
example : [List]](#manual-List___nil) [Nat]](#manual-Nat___zero) := [.cons]](#manual-List___nil) 1 ([.cons]](#manual-List___nil) 2 ([.cons]](#manual-List___nil) 3 [.nil]](#manual-List___nil)))
```

**Example: Pattern Matching and Lists**

All of these functions are equivalent:

```lean
def split : [List]](#manual-List___nil) α → [List]](#manual-List___nil) α × [List]](#manual-List___nil) α
| [] => ([], [])
| [x] => ([x], [])
| x :: x' :: xs =>
let (ys, zs) := [split]](#manual-split-_LPAR_in-Pattern-Matching-and-Lists_RPAR_) xs
(x :: ys, x' :: zs)
```

```lean
def split' : [List]](#manual-List___nil) α → [List]](#manual-List___nil) α × [List]](#manual-List___nil) α
| [.nil]](#manual-List___nil) => ([.nil]](#manual-List___nil), [.nil]](#manual-List___nil))
| x :: [] => ([.singleton]](#manual-List___singleton) x, [.nil]](#manual-List___nil))
| x :: x' :: xs =>
let (ys, zs) := [split]](#manual-split-_LPAR_in-Pattern-Matching-and-Lists_RPAR_) xs
(x :: ys, x' :: zs)
```

```lean
def split'' : [List]](#manual-List___nil) α → [List]](#manual-List___nil) α × [List]](#manual-List___nil) α
| [.nil]](#manual-List___nil) => ([.nil]](#manual-List___nil), [.nil]](#manual-List___nil))
| [.cons]](#manual-List___nil) x [.nil]](#manual-List___nil) => ([.singleton]](#manual-List___singleton) x, [.nil]](#manual-List___nil))
| [.cons]](#manual-List___nil) x ([.cons]](#manual-List___nil) x' xs) =>
let (ys, zs) := [split]](#manual-split-_LPAR_in-Pattern-Matching-and-Lists_RPAR_) xs
([.cons]](#manual-List___nil) x ys, [.cons]](#manual-List___nil) x' zs)
```

### 20.15.2. Performance Notes {#manual-list-performance}

The representation of lists is not overridden or modified by the compiler: they are linked lists, with a pointer indirection for each element.
Calculating the length of a list requires a full traversal, and modifying an element in a list requires a traversal and reallocation of the prefix of the list that is prior to the element being modified.
Due to Lean's reference-counting-based memory management, operations such as `[List.map]](#manual-List___map)` that traverse a list, allocating a new `[List.cons]](#manual-List___nil)` constructor for each in the prior list, can reuse the original list's memory when there are no other references to it.

Because of the important role played by lists in specifications, most list functions are written as straightforwardly as possible using structural recursion.
This makes it easier to write proofs by induction, but it also means that these operations consume stack space proportional to the length of the list.
There are tail-recursive versions of many list functions that are equivalent to the non-tail-recursive versions, but are more difficult to use when reasoning.
In compiled code, the tail-recursive versions are automatically used instead of the non-tail-recursive versions.

### 20.15.3. API Reference {#manual-list-api-reference}

#### 20.15.3.1. Predicates and Relations {#manual-The-Lean-Language-Reference--Basic-Types--Linked-Lists--API-Reference--Predicates-and-Relations}

def

```lean
[List.IsPrefix.{u}]](#manual-List___IsPrefix) {α : Type u} (l₁ l₂ : [List]](#manual-List___nil) α) : Prop



[List.IsPrefix.{u}]](#manual-List___IsPrefix) {α : Type u}
  (l₁ l₂ : [List]](#manual-List___nil) α) : Prop
```

The first list is a prefix of the second.

`IsPrefix l₁ l₂`, written `l₁ <+: l₂`, means that there exists some `t : [List]](#manual-List___nil) α` such that `l₂` has
the form `l₁ ++ t`.

The function `[List.isPrefixOf]](#manual-List___isPrefixOf)` is a Boolean equivalent.

Conventions for notations in identifiers:

- The recommended spelling of `<+:` in identifiers is `prefix` (not `isPrefix`).

syntaxList Prefix

```lean
term ::= ...
    | term <+: term
```

The first list is a prefix of the second.

`IsPrefix l₁ l₂`, written `l₁ <+: l₂`, means that there exists some `t : [List]](#manual-List___nil) α` such that `l₂` has
the form `l₁ ++ t`.

The function `[List.isPrefixOf]](#manual-List___isPrefixOf)` is a Boolean equivalent.

Conventions for notations in identifiers:

- The recommended spelling of `<+:` in identifiers is `prefix` (not `isPrefix`).

def

```lean
[List.IsSuffix.{u}]](#manual-List___IsSuffix) {α : Type u} (l₁ l₂ : [List]](#manual-List___nil) α) : Prop



[List.IsSuffix.{u}]](#manual-List___IsSuffix) {α : Type u}
  (l₁ l₂ : [List]](#manual-List___nil) α) : Prop
```

The first list is a suffix of the second.

`IsSuffix l₁ l₂`, written `l₁ <:+ l₂`, means that there exists some `t : [List]](#manual-List___nil) α` such that `l₂` has
the form `t ++ l₁`.

The function `[List.isSuffixOf]](#manual-List___isSuffixOf)` is a Boolean equivalent.

Conventions for notations in identifiers:

- The recommended spelling of `<:+` in identifiers is `suffix` (not `isSuffix`).

syntaxList Suffix

```lean
term ::= ...
    | term <:+ term
```

The first list is a suffix of the second.

`IsSuffix l₁ l₂`, written `l₁ <:+ l₂`, means that there exists some `t : [List]](#manual-List___nil) α` such that `l₂` has
the form `t ++ l₁`.

The function `[List.isSuffixOf]](#manual-List___isSuffixOf)` is a Boolean equivalent.

Conventions for notations in identifiers:

- The recommended spelling of `<:+` in identifiers is `suffix` (not `isSuffix`).

def

```lean
[List.IsInfix.{u}]](#manual-List___IsInfix) {α : Type u} (l₁ l₂ : [List]](#manual-List___nil) α) : Prop



[List.IsInfix.{u}]](#manual-List___IsInfix) {α : Type u}
  (l₁ l₂ : [List]](#manual-List___nil) α) : Prop
```

The first list is a contiguous sub-list of the second list. Typically written with the `<:+:`
operator.

In other words, `l₁ <:+: l₂` means that there exist lists `s : [List]](#manual-List___nil) α` and `t : [List]](#manual-List___nil) α` such that
`l₂` has the form `s ++ l₁ ++ t`.

Conventions for notations in identifiers:

- The recommended spelling of `<:+:` in identifiers is `infix` (not `isInfix`).

syntaxList Infix

```lean
term ::= ...
    | term <:+: term
```

The first list is a contiguous sub-list of the second list. Typically written with the `<:+:`
operator.

In other words, `l₁ <:+: l₂` means that there exist lists `s : [List]](#manual-List___nil) α` and `t : [List]](#manual-List___nil) α` such that
`l₂` has the form `s ++ l₁ ++ t`.

Conventions for notations in identifiers:

- The recommended spelling of `<:+:` in identifiers is `infix` (not `isInfix`).

inductive predicate

```lean
[List.Sublist.{u_1}]](#manual-List___Sublist___slnil) {α : Type u_1} : [List]](#manual-List___nil) α → [List]](#manual-List___nil) α → Prop



[List.Sublist.{u_1}]](#manual-List___Sublist___slnil) {α : Type u_1} :
  [List]](#manual-List___nil) α → [List]](#manual-List___nil) α → Prop
```

The first list is a non-contiguous sub-list of the second list. Typically written with the `<+`
operator.

In other words, `l₁ <+ l₂` means that `l₁` can be transformed into `l₂` by repeatedly inserting new
elements.

Constructors

```lean
[List.Sublist.slnil.{u_1}]](#manual-List___Sublist___slnil) {α : Type u_1} : [[]](#manual-List___nil)[]](https://lean-lang.org/doc/reference/latest/Basic-Types/Linked-Lists/#List___nil).[Sublist]](#manual-List___Sublist___slnil) [[]](#manual-List___nil)[]](https://lean-lang.org/doc/reference/latest/Basic-Types/Linked-Lists/#List___nil)
```

The base case: `[]` is a sublist of `[]`

```lean
[List.Sublist.cons.{u_1}]](#manual-List___Sublist___slnil) {α : Type u_1} {l₁ l₂ : [List]](#manual-List___nil) α}
  (a : α) : l₁.[Sublist]](#manual-List___Sublist___slnil) l₂ → l₁.[Sublist]](#manual-List___Sublist___slnil) [(]](#manual-List___nil)a [::]](#manual-List___nil) l₂[)]](#manual-List___nil)
```

If `l₁` is a subsequence of `l₂`, then it is also a subsequence of `a :: l₂`.

```lean
[List.Sublist.cons_cons.{u_1}]](#manual-List___Sublist___slnil) {α : Type u_1} {l₁ l₂ : [List]](#manual-List___nil) α}
  (a : α) : l₁.[Sublist]](#manual-List___Sublist___slnil) l₂ → [(]](#manual-List___nil)a [::]](#manual-List___nil) l₁[)]](#manual-List___nil).[Sublist]](#manual-List___Sublist___slnil) [(]](#manual-List___nil)a [::]](#manual-List___nil) l₂[)]](#manual-List___nil)
```

If `l₁` is a subsequence of `l₂`, then `a :: l₁` is a subsequence of `a :: l₂`.

syntaxSublists

```lean
term ::= ...
    | term <+ term
```

The first list is a non-contiguous sub-list of the second list. Typically written with the `<+`
operator.

In other words, `l₁ <+ l₂` means that `l₁` can be transformed into `l₂` by repeatedly inserting new
elements.

This syntax is only available when the `List` namespace is opened.

inductive predicate

```lean
[List.Perm.{u}]](#manual-List___Perm___nil) {α : Type u} : [List]](#manual-List___nil) α → [List]](#manual-List___nil) α → Prop



[List.Perm.{u}]](#manual-List___Perm___nil) {α : Type u} :
  [List]](#manual-List___nil) α → [List]](#manual-List___nil) α → Prop
```

Two lists are permutations of each other if they contain the same elements, each occurring the same
number of times but not necessarily in the same order.

One list can be proven to be a permutation of another by showing how to transform one into the other
by repeatedly swapping adjacent elements.

`[List.isPerm]](#manual-List___isPerm)` is a Boolean equivalent of this relation.

Constructors

```lean
[List.Perm.nil.{u}]](#manual-List___Perm___nil) {α : Type u} : [[]](#manual-List___nil)[]](https://lean-lang.org/doc/reference/latest/Basic-Types/Linked-Lists/#List___nil).[Perm]](#manual-List___Perm___nil) [[]](#manual-List___nil)[]](https://lean-lang.org/doc/reference/latest/Basic-Types/Linked-Lists/#List___nil)
```

The empty list is a permutation of the empty list: `[] ~ []`.

```lean
[List.Perm.cons.{u}]](#manual-List___Perm___nil) {α : Type u} (x : α) {l₁ l₂ : [List]](#manual-List___nil) α} :
  l₁.[Perm]](#manual-List___Perm___nil) l₂ → [(]](#manual-List___nil)x [::]](#manual-List___nil) l₁[)]](#manual-List___nil).[Perm]](#manual-List___Perm___nil) [(]](#manual-List___nil)x [::]](#manual-List___nil) l₂[)]](#manual-List___nil)
```

If one list is a permutation of the other, adding the same element as the head of each yields
lists that are permutations of each other: `l₁ ~ l₂ → x::l₁ ~ x::l₂`.

```lean
[List.Perm.swap.{u}]](#manual-List___Perm___nil) {α : Type u} (x y : α) (l : [List]](#manual-List___nil) α) :
  [(]](#manual-List___nil)y [::]](#manual-List___nil) x [::]](#manual-List___nil) l[)]](#manual-List___nil).[Perm]](#manual-List___Perm___nil) [(]](#manual-List___nil)x [::]](#manual-List___nil) y [::]](#manual-List___nil) l[)]](#manual-List___nil)
```

If two lists are identical except for having their first two elements swapped, then they are
permutations of each other: `x::y::l ~ y::x::l`.

```lean
[List.Perm.trans.{u}]](#manual-List___Perm___nil) {α : Type u} {l₁ l₂ l₃ : [List]](#manual-List___nil) α} :
  l₁.[Perm]](#manual-List___Perm___nil) l₂ → l₂.[Perm]](#manual-List___Perm___nil) l₃ → l₁.[Perm]](#manual-List___Perm___nil) l₃
```

Permutation is transitive: `l₁ ~ l₂ → l₂ ~ l₃ → l₁ ~ l₃`.

syntaxList Permutation

```lean
term ::= ...
    | term ~ term
```

Two lists are permutations of each other if they contain the same elements, each occurring the same
number of times but not necessarily in the same order.

One list can be proven to be a permutation of another by showing how to transform one into the other
by repeatedly swapping adjacent elements.

`[List.isPerm]](#manual-List___isPerm)` is a Boolean equivalent of this relation.

This syntax is only available when the `List` namespace is opened.

inductive predicate

```lean
[List.Pairwise.{u}]](#manual-List___Pairwise___nil) {α : Type u} (R : α → α → Prop) : [List]](#manual-List___nil) α → Prop



[List.Pairwise.{u}]](#manual-List___Pairwise___nil) {α : Type u}
  (R : α → α → Prop) : [List]](#manual-List___nil) α → Prop
```

Each element of a list is related to all later elements of the list by `R`.

`Pairwise R l` means that all the elements of `l` with earlier indexes are `R`-related to all the
elements with later indexes.

For example, `Pairwise (· ≠ ·) l` asserts that `l` has no duplicates, and `Pairwise (· < ·) l`
asserts that `l` is (strictly) sorted.

Examples:

- `Pairwise (· < ·) [1, 2, 3] ↔ (1 < 2 ∧ 1 < 3) ∧ 2 < 3`
- `Pairwise (· = ·) [1, 2, 3] = [False]](#manual-False)`
- `Pairwise (· ≠ ·) [1, 2, 3] = [True]](#manual-True___intro)`

Constructors

```lean
[List.Pairwise.nil.{u}]](#manual-List___Pairwise___nil) {α : Type u} {R : α → α → Prop} :
  [List.Pairwise]](#manual-List___Pairwise___nil) R [[]](#manual-List___nil)[]](https://lean-lang.org/doc/reference/latest/Basic-Types/Linked-Lists/#List___nil)
```

All elements of the empty list are vacuously pairwise related.

```lean
[List.Pairwise.cons.{u}]](#manual-List___Pairwise___nil) {α : Type u} {R : α → α → Prop}
  {a : α} {l : [List]](#manual-List___nil) α} :
  (∀ (a' : α), a' ∈ l → R a a') →
    [List.Pairwise]](#manual-List___Pairwise___nil) R l → [List.Pairwise]](#manual-List___Pairwise___nil) R [(]](#manual-List___nil)a [::]](#manual-List___nil) l[)]](#manual-List___nil)
```

A nonempty list is pairwise related with `R` if the head is related to every element of the tail
and the tail is itself pairwise related.

That is, `a :: l` is `[Pairwise]](#manual-List___Pairwise___nil) R` if:

- `R` relates `a` to every element of `l`
- `l` is `[Pairwise]](#manual-List___Pairwise___nil) R`.

def

```lean
[List.Nodup.{u}]](#manual-List___Nodup) {α : Type u} : [List]](#manual-List___nil) α → Prop



[List.Nodup.{u}]](#manual-List___Nodup) {α : Type u} :
  [List]](#manual-List___nil) α → Prop
```

The list has no duplicates: it contains every element at most once.

It is defined as `Pairwise (· ≠ ·)`: each element is unequal to all other elements.

inductive predicate

```lean
[List.Lex.{u}]](#manual-List___Lex___nil) {α : Type u} (r : α → α → Prop) (as bs : [List]](#manual-List___nil) α) : Prop



[List.Lex.{u}]](#manual-List___Lex___nil) {α : Type u}
  (r : α → α → Prop) (as bs : [List]](#manual-List___nil) α) :
  Prop
```

Lexicographic ordering for lists with respect to an ordering of elements.

`as` is lexicographically smaller than `bs` if

- `as` is empty and `bs` is non-empty, or
- both `as` and `bs` are non-empty, and the head of `as` is less than the head of `bs` according to
  `r`, or
- both `as` and `bs` are non-empty, their heads are equal, and the tail of `as` is less than the
  tail of `bs`.

Constructors

```lean
[List.Lex.nil.{u}]](#manual-List___Lex___nil) {α : Type u} {r : α → α → Prop} {a : α}
  {l : [List]](#manual-List___nil) α} : [List.Lex]](#manual-List___Lex___nil) r [[]](#manual-List___nil)[]](https://lean-lang.org/doc/reference/latest/Basic-Types/Linked-Lists/#List___nil) [(]](#manual-List___nil)a [::]](#manual-List___nil) l[)]](#manual-List___nil)
```

`[]` is the smallest element in the lexicographic order.

```lean
[List.Lex.rel.{u}]](#manual-List___Lex___nil) {α : Type u} {r : α → α → Prop} {a₁ : α}
  {l₁ : [List]](#manual-List___nil) α} {a₂ : α} {l₂ : [List]](#manual-List___nil) α} (h : r a₁ a₂) :
  [List.Lex]](#manual-List___Lex___nil) r [(]](#manual-List___nil)a₁ [::]](#manual-List___nil) l₁[)]](#manual-List___nil) [(]](#manual-List___nil)a₂ [::]](#manual-List___nil) l₂[)]](#manual-List___nil)
```

If the head of the first list is smaller than the head of the second, then the first list is
lexicographically smaller than the second list.

```lean
[List.Lex.cons.{u}]](#manual-List___Lex___nil) {α : Type u} {r : α → α → Prop} {a : α}
  {l₁ l₂ : [List]](#manual-List___nil) α} (h : [List.Lex]](#manual-List___Lex___nil) r l₁ l₂) :
  [List.Lex]](#manual-List___Lex___nil) r [(]](#manual-List___nil)a [::]](#manual-List___nil) l₁[)]](#manual-List___nil) [(]](#manual-List___nil)a [::]](#manual-List___nil) l₂[)]](#manual-List___nil)
```

If two lists have the same head, then their tails determine their lexicographic order. If the tail
of the first list is lexicographically smaller than the tail of the second list, then the entire
first list is lexicographically smaller than the entire second list.

inductive predicate

```lean
[List.Mem.{u}]](#manual-List___Mem___head) {α : Type u} (a : α) : [List]](#manual-List___nil) α → Prop



[List.Mem.{u}]](#manual-List___Mem___head) {α : Type u} (a : α) :
  [List]](#manual-List___nil) α → Prop
```

List membership, typically accessed via the `∈` operator.

`a ∈ l` means that `a` is an element of the list `l`. Elements are compared according to Lean's
logical equality.

The related function `[List.elem]](#manual-List___elem)` is a Boolean membership test that uses a `[BEq]](#manual-BEq___mk) α` instance.

Examples:

- `a ∈ [x, y, z] ↔ a = x ∨ a = y ∨ a = z`

Constructors

```lean
[List.Mem.head.{u}]](#manual-List___Mem___head) {α : Type u} {a : α} (as : [List]](#manual-List___nil) α) :
  [List.Mem]](#manual-List___Mem___head) a [(]](#manual-List___nil)a [::]](#manual-List___nil) as[)]](#manual-List___nil)
```

The head of a list is a member: `a ∈ a :: as`.

```lean
[List.Mem.tail.{u}]](#manual-List___Mem___head) {α : Type u} {a : α} (b : α)
  {as : [List]](#manual-List___nil) α} : [List.Mem]](#manual-List___Mem___head) a as → [List.Mem]](#manual-List___Mem___head) a [(]](#manual-List___nil)b [::]](#manual-List___nil) as[)]](#manual-List___nil)
```

A member of the tail of a list is a member of the list: `a ∈ l → a ∈ b :: l`.

#### 20.15.3.2. Constructing Lists {#manual-The-Lean-Language-Reference--Basic-Types--Linked-Lists--API-Reference--Constructing-Lists}

def

```lean
[List.singleton.{u}]](#manual-List___singleton) {α : Type u} (a : α) : [List]](#manual-List___nil) α



[List.singleton.{u}]](#manual-List___singleton) {α : Type u} (a : α) :
  [List]](#manual-List___nil) α
```

Constructs a single-element list.

Examples:

- `[List.singleton]](#manual-List___singleton) 5 = [5]`.
- `[List.singleton]](#manual-List___singleton) "green" = ["green"]`.
- `[List.singleton]](#manual-List___singleton) [1, 2, 3] = [[1, 2, 3]]`

def

```lean
[List.concat.{u}]](#manual-List___concat) {α : Type u} : [List]](#manual-List___nil) α → α → [List]](#manual-List___nil) α



[List.concat.{u}]](#manual-List___concat) {α : Type u} :
  [List]](#manual-List___nil) α → α → [List]](#manual-List___nil) α
```

Adds an element to the *end* of a list.

The added element is the last element of the resulting list.

Examples:

- `[List.concat]](#manual-List___concat) ["red", "yellow"] "green" = ["red", "yellow", "green"]`
- `[List.concat]](#manual-List___concat) [1, 2, 3] 4 = [1, 2, 3, 4]`
- `[List.concat]](#manual-List___concat) [] () = [()]`

def

```lean
[List.replicate.{u}]](#manual-List___replicate) {α : Type u} (n : [Nat]](#manual-Nat___zero)) (a : α) : [List]](#manual-List___nil) α



[List.replicate.{u}]](#manual-List___replicate) {α : Type u} (n : [Nat]](#manual-Nat___zero))
  (a : α) : [List]](#manual-List___nil) α
```

Creates a list that contains `n` copies of `a`.

- `[List.replicate]](#manual-List___replicate) 5 "five" = ["five", "five", "five", "five", "five"]`
- `[List.replicate]](#manual-List___replicate) 0 "zero" = []`
- `[List.replicate]](#manual-List___replicate) 2 ' ' = [' ', ' ']`

def

```lean
[List.replicateTR.{u}]](#manual-List___replicateTR) {α : Type u} (n : [Nat]](#manual-Nat___zero)) (a : α) : [List]](#manual-List___nil) α



[List.replicateTR.{u}]](#manual-List___replicateTR) {α : Type u}
  (n : [Nat]](#manual-Nat___zero)) (a : α) : [List]](#manual-List___nil) α
```

Creates a list that contains `n` copies of `a`.

This is a tail-recursive version of `[List.replicate]](#manual-List___replicate)`.

- `[List.replicateTR]](#manual-List___replicateTR) 5 "five" = ["five", "five", "five", "five", "five"]`
- `[List.replicateTR]](#manual-List___replicateTR) 0 "zero" = []`
- `[List.replicateTR]](#manual-List___replicateTR) 2 ' ' = [' ', ' ']`

def

```lean
[List.ofFn.{u_1}]](#manual-List___ofFn) {α : Type u_1} {n : [Nat]](#manual-Nat___zero)} (f : [Fin]](#manual-Fin___mk) n → α) : [List]](#manual-List___nil) α



[List.ofFn.{u_1}]](#manual-List___ofFn) {α : Type u_1} {n : [Nat]](#manual-Nat___zero)}
  (f : [Fin]](#manual-Fin___mk) n → α) : [List]](#manual-List___nil) α
```

Creates a list by applying `f` to each potential index in order, starting at `0`.

Examples:

- `[List.ofFn]](#manual-List___ofFn) (n := 3) toString = ["0", "1", "2"]`
- `[List.ofFn]](#manual-List___ofFn) (fun i => #["red", "green", "blue"].get i.val i.isLt) = ["red", "green", "blue"]`

def

```lean
[List.append.{u_1}]](#manual-List___append) {α : Type u_1} (xs ys : [List]](#manual-List___nil) α) : [List]](#manual-List___nil) α



[List.append.{u_1}]](#manual-List___append) {α : Type u_1}
  (xs ys : [List]](#manual-List___nil) α) : [List]](#manual-List___nil) α
```

Appends two lists. Normally used via the `++` operator.

Appending lists takes time proportional to the length of the first list: `O(|xs|)`.

Examples:

- `[1, 2, 3] ++ [4, 5] = [1, 2, 3, 4, 5]`.
- `[] ++ [4, 5] = [4, 5]`.
- `[1, 2, 3] ++ [] = [1, 2, 3]`.

def

```lean
[List.appendTR.{u}]](#manual-List___appendTR) {α : Type u} (as bs : [List]](#manual-List___nil) α) : [List]](#manual-List___nil) α



[List.appendTR.{u}]](#manual-List___appendTR) {α : Type u}
  (as bs : [List]](#manual-List___nil) α) : [List]](#manual-List___nil) α
```

Appends two lists. Normally used via the `++` operator.

Appending lists takes time proportional to the length of the first list: `O(|xs|)`.

This is a tail-recursive version of `[List.append]](#manual-List___append)`.

Examples:

- `[1, 2, 3] ++ [4, 5] = [1, 2, 3, 4, 5]`.
- `[] ++ [4, 5] = [4, 5]`.
- `[1, 2, 3] ++ [] = [1, 2, 3]`.

def

```lean
[List.range]](#manual-List___range) (n : [Nat]](#manual-Nat___zero)) : [List]](#manual-List___nil) [Nat]](#manual-Nat___zero)



[List.range]](#manual-List___range) (n : [Nat]](#manual-Nat___zero)) : [List]](#manual-List___nil) [Nat]](#manual-Nat___zero)
```

Returns a list of the numbers from `0` to `n` exclusive, in increasing order.

`O(n)`.

Examples:

- `range 5 = [0, 1, 2, 3, 4]`
- `range 0 = []`
- `range 2 = [0, 1]`

def

```lean
[List.range']](#manual-List___range___) (start len : [Nat]](#manual-Nat___zero)) (step : [Nat]](#manual-Nat___zero) := 1) : [List]](#manual-List___nil) [Nat]](#manual-Nat___zero)



[List.range']](#manual-List___range___) (start len : [Nat]](#manual-Nat___zero))
  (step : [Nat]](#manual-Nat___zero) := 1) : [List]](#manual-List___nil) [Nat]](#manual-Nat___zero)
```

Returns a list of the numbers with the given length `len`, starting at `start` and increasing by
`step` at each element.

In other words, `[List.range']](#manual-List___range___) start len step` is `[start, start+step, ..., start+(len-1)*step]`.

Examples:

- `[List.range']](#manual-List___range___) 0 3 (step := 1) = [0, 1, 2]`
- `[List.range']](#manual-List___range___) 0 3 (step := 2) = [0, 2, 4]`
- `[List.range']](#manual-List___range___) 0 4 (step := 2) = [0, 2, 4, 6]`
- `[List.range']](#manual-List___range___) 3 4 (step := 2) = [3, 5, 7, 9]`

def

```lean
[List.range'TR]](#manual-List___range___TR) (s n : [Nat]](#manual-Nat___zero)) (step : [Nat]](#manual-Nat___zero) := 1) : [List]](#manual-List___nil) [Nat]](#manual-Nat___zero)



[List.range'TR]](#manual-List___range___TR) (s n : [Nat]](#manual-Nat___zero))
  (step : [Nat]](#manual-Nat___zero) := 1) : [List]](#manual-List___nil) [Nat]](#manual-Nat___zero)
```

Returns a list of the numbers with the given length `len`, starting at `start` and increasing by
`step` at each element.

In other words, `[List.range'TR]](#manual-List___range___TR) start len step` is `[start, start+step, ..., start+(len-1)*step]`.

This is a tail-recursive version of `[List.range']](#manual-List___range___)`.

Examples:

- `[List.range'TR]](#manual-List___range___TR) 0 3 (step := 1) = [0, 1, 2]`
- `[List.range'TR]](#manual-List___range___TR) 0 3 (step := 2) = [0, 2, 4]`
- `[List.range'TR]](#manual-List___range___TR) 0 4 (step := 2) = [0, 2, 4, 6]`
- `[List.range'TR]](#manual-List___range___TR) 3 4 (step := 2) = [3, 5, 7, 9]`

def

```lean
[List.finRange]](#manual-List___finRange) (n : [Nat]](#manual-Nat___zero)) : [List]](#manual-List___nil) ([Fin]](#manual-Fin___mk) n)



[List.finRange]](#manual-List___finRange) (n : [Nat]](#manual-Nat___zero)) : [List]](#manual-List___nil) ([Fin]](#manual-Fin___mk) n)
```

Lists all elements of `[Fin]](#manual-Fin___mk) n` in order, starting at `0`.

Examples:

- `[List.finRange]](#manual-List___finRange) 0 = ([] : [List]](#manual-List___nil) ([Fin]](#manual-Fin___mk) 0))`
- `[List.finRange]](#manual-List___finRange) 2 = ([0, 1] : [List]](#manual-List___nil) ([Fin]](#manual-Fin___mk) 2))`

#### 20.15.3.3. Length {#manual-The-Lean-Language-Reference--Basic-Types--Linked-Lists--API-Reference--Length}

def

```lean
[List.length.{u_1}]](#manual-List___length) {α : Type u_1} : [List]](#manual-List___nil) α → [Nat]](#manual-Nat___zero)



[List.length.{u_1}]](#manual-List___length) {α : Type u_1} :
  [List]](#manual-List___nil) α → [Nat]](#manual-Nat___zero)
```

The length of a list.

This function is overridden in the compiler to `lengthTR`, which uses constant stack space.

Examples:

- `([] : [List]](#manual-List___nil) [String]](#manual-String___ofByteArray)).[length]](#manual-List___length) = 0`
- `["green", "brown"].[length]](#manual-List___length) = 2`

def

```lean
[List.lengthTR.{u_1}]](#manual-List___lengthTR) {α : Type u_1} (as : [List]](#manual-List___nil) α) : [Nat]](#manual-Nat___zero)



[List.lengthTR.{u_1}]](#manual-List___lengthTR) {α : Type u_1}
  (as : [List]](#manual-List___nil) α) : [Nat]](#manual-Nat___zero)
```

The length of a list.

This is a tail-recursive version of `[List.length]](#manual-List___length)`, used to implement `[List.length]](#manual-List___length)` without running
out of stack space.

Examples:

- `([] : [List]](#manual-List___nil) [String]](#manual-String___ofByteArray)).[lengthTR]](#manual-List___lengthTR) = 0`
- `["green", "brown"].[lengthTR]](#manual-List___lengthTR) = 2`

def

```lean
[List.isEmpty.{u}]](#manual-List___isEmpty) {α : Type u} : [List]](#manual-List___nil) α → [Bool]](#manual-Bool___false)



[List.isEmpty.{u}]](#manual-List___isEmpty) {α : Type u} :
  [List]](#manual-List___nil) α → [Bool]](#manual-Bool___false)
```

Checks whether a list is empty.

`O(1)`.

Examples:

- `[].[isEmpty]](#manual-List___isEmpty) = [true]](#manual-Bool___false)`
- `["grape"].[isEmpty]](#manual-List___isEmpty) = [false]](#manual-Bool___false)`
- `["apple", "banana"].[isEmpty]](#manual-List___isEmpty) = [false]](#manual-Bool___false)`

#### 20.15.3.4. Head and Tail {#manual-The-Lean-Language-Reference--Basic-Types--Linked-Lists--API-Reference--Head-and-Tail}

def

```lean
[List.head.{u}]](#manual-List___head) {α : Type u} (as : [List]](#manual-List___nil) α) : as ≠ [[]](#manual-List___nil)[]](https://lean-lang.org/doc/reference/latest/Basic-Types/Linked-Lists/#List___nil) → α



[List.head.{u}]](#manual-List___head) {α : Type u} (as : [List]](#manual-List___nil) α) :
  as ≠ [[]](#manual-List___nil)[]](https://lean-lang.org/doc/reference/latest/Basic-Types/Linked-Lists/#List___nil) → α
```

Returns the first element of a non-empty list.

def

```lean
[List.head?.{u}]](#manual-List___head___) {α : Type u} : [List]](#manual-List___nil) α → [Option]](#manual-Option___none) α



[List.head?.{u}]](#manual-List___head___) {α : Type u} :
  [List]](#manual-List___nil) α → [Option]](#manual-Option___none) α
```

Returns the first element in the list, if there is one. Returns `[none]](#manual-Option___none)` if the list is empty.

Use `[List.headD]](#manual-List___headD)` to provide a fallback value for empty lists, or `[List.head!]](#manual-List___head___-next)` to panic on empty
lists.

Examples:

- `([] : [List]](#manual-List___nil) [Nat]](#manual-Nat___zero)).[head?]](#manual-List___head___) = [none]](#manual-Option___none)`
- `[3, 2, 1].[head?]](#manual-List___head___) = [some]](#manual-Option___none) 3`

def

```lean
[List.headD.{u}]](#manual-List___headD) {α : Type u} (as : [List]](#manual-List___nil) α) (fallback : α) : α



[List.headD.{u}]](#manual-List___headD) {α : Type u} (as : [List]](#manual-List___nil) α)
  (fallback : α) : α
```

Returns the first element in the list if there is one, or `fallback` if the list is empty.

Use `[List.head?]](#manual-List___head___)` to return an `[Option]](#manual-Option___none)`, and `[List.head!]](#manual-List___head___-next)` to panic on empty lists.

Examples:

- `[].[headD]](#manual-List___headD) "empty" = "empty"`
- `[].[headD]](#manual-List___headD) 2 = 2`
- `["head", "shoulders", "knees"].[headD]](#manual-List___headD) "toes" = "head"`

def

```lean
[List.head!.{u_1}]](#manual-List___head___-next) {α : Type u_1} [[Inhabited]](#manual-Inhabited___mk) α] : [List]](#manual-List___nil) α → α



[List.head!.{u_1}]](#manual-List___head___-next) {α : Type u_1}
  [[Inhabited]](#manual-Inhabited___mk) α] : [List]](#manual-List___nil) α → α
```

Returns the first element in the list. If the list is empty, panics and returns `[default]](#manual-Inhabited___mk)`.

Safer alternatives include:

- `[List.head]](#manual-List___head)`, which requires a proof that the list is non-empty,
- `[List.head?]](#manual-List___head___)`, which returns an `[Option]](#manual-Option___none)`, and
- `[List.headD]](#manual-List___headD)`, which returns an explicitly-provided fallback value on empty lists.

def

```lean
[List.tail.{u}]](#manual-List___tail) {α : Type u} : [List]](#manual-List___nil) α → [List]](#manual-List___nil) α



[List.tail.{u}]](#manual-List___tail) {α : Type u} :
  [List]](#manual-List___nil) α → [List]](#manual-List___nil) α
```

Drops the first element of a nonempty list, returning the tail. Returns `[]` when the argument is
empty.

Examples:

- `["apple", "banana", "grape"].[tail]](#manual-List___tail) = ["banana", "grape"]`
- `["apple"].[tail]](#manual-List___tail) = []`
- `([] : [List]](#manual-List___nil) [String]](#manual-String___ofByteArray)).[tail]](#manual-List___tail) = []`

def

```lean
[List.tail!.{u_1}]](#manual-List___tail___) {α : Type u_1} : [List]](#manual-List___nil) α → [List]](#manual-List___nil) α



[List.tail!.{u_1}]](#manual-List___tail___) {α : Type u_1} :
  [List]](#manual-List___nil) α → [List]](#manual-List___nil) α
```

Drops the first element of a nonempty list, returning the tail. If the list is empty, this function
panics when executed and returns the empty list.

Safer alternatives include

- `tail`, which returns the empty list without panicking,
- `tail?`, which returns an `[Option]](#manual-Option___none)`, and
- `tailD`, which returns a fallback value when passed the empty list.

Examples:

- `["apple", "banana", "grape"].[tail!]](#manual-List___tail___) = ["banana", "grape"]`
- `["banana", "grape"].[tail!]](#manual-List___tail___) = ["grape"]`

def

```lean
[List.tail?.{u}]](#manual-List___tail___-next) {α : Type u} : [List]](#manual-List___nil) α → [Option]](#manual-Option___none) ([List]](#manual-List___nil) α)



[List.tail?.{u}]](#manual-List___tail___-next) {α : Type u} :
  [List]](#manual-List___nil) α → [Option]](#manual-Option___none) ([List]](#manual-List___nil) α)
```

Drops the first element of a nonempty list, returning the tail. Returns `[none]](#manual-Option___none)` when the argument is
empty.

Alternatives include `[List.tail]](#manual-List___tail)`, which returns the empty list on failure, `[List.tailD]](#manual-List___tailD)`, which
returns an explicit fallback value, and `[List.tail!]](#manual-List___tail___)`, which panics on the empty list.

Examples:

- `["apple", "banana", "grape"].[tail?]](#manual-List___tail___-next) = [some]](#manual-Option___none) ["banana", "grape"]`
- `["apple"].[tail?]](#manual-List___tail___-next) = [some]](#manual-Option___none) []`
- `([] : [List]](#manual-List___nil) [String]](#manual-String___ofByteArray)).[tail]](#manual-List___tail) = [none]](#manual-Option___none)`

def

```lean
[List.tailD.{u}]](#manual-List___tailD) {α : Type u} (l fallback : [List]](#manual-List___nil) α) : [List]](#manual-List___nil) α



[List.tailD.{u}]](#manual-List___tailD) {α : Type u}
  (l fallback : [List]](#manual-List___nil) α) : [List]](#manual-List___nil) α
```

Drops the first element of a nonempty list, returning the tail. Returns `[none]](#manual-Option___none)` when the argument is
empty.

Alternatives include `[List.tail]](#manual-List___tail)`, which returns the empty list on failure, `[List.tail?]](#manual-List___tail___-next)`, which
returns an `[Option]](#manual-Option___none)`, and `[List.tail!]](#manual-List___tail___)`, which panics on the empty list.

Examples:

- `["apple", "banana", "grape"].[tailD]](#manual-List___tailD) ["orange"] = ["banana", "grape"]`
- `["apple"].[tailD]](#manual-List___tailD) ["orange"] = []`
- `[].[tailD]](#manual-List___tailD) ["orange"] = ["orange"]`

#### 20.15.3.5. Lookups {#manual-The-Lean-Language-Reference--Basic-Types--Linked-Lists--API-Reference--Lookups}

def

```lean
[List.get.{u}]](#manual-List___get) {α : Type u} (as : [List]](#manual-List___nil) α) : [Fin]](#manual-Fin___mk) as.[length]](#manual-List___length) → α



[List.get.{u}]](#manual-List___get) {α : Type u} (as : [List]](#manual-List___nil) α) :
  [Fin]](#manual-Fin___mk) as.[length]](#manual-List___length) → α
```

Returns the element at the provided index, counting from `0`.

In other words, for `i : [Fin]](#manual-Fin___mk) as.[length]](#manual-List___length)`, `as.[get]](#manual-List___get) i` returns the `i`'th element of the list `as`.
Because the index is a `[Fin]](#manual-Fin___mk)` bounded by the list's length, the index will never be out of bounds.

Examples:

- `["spring", "summer", "fall", "winter"].[get]](#manual-List___get) (2 : [Fin]](#manual-Fin___mk) 4) = "fall"`
- `["spring", "summer", "fall", "winter"].[get]](#manual-List___get) (0 : [Fin]](#manual-Fin___mk) 4) = "spring"`

def

```lean
[List.getD.{u_1}]](#manual-List___getD) {α : Type u_1} (as : [List]](#manual-List___nil) α) (i : [Nat]](#manual-Nat___zero)) (fallback : α) :
  α



[List.getD.{u_1}]](#manual-List___getD) {α : Type u_1}
  (as : [List]](#manual-List___nil) α) (i : [Nat]](#manual-Nat___zero)) (fallback : α) :
  α
```

Returns the element at the provided index, counting from `0`. Returns `fallback` if the index is out
of bounds.

To return an `[Option]](#manual-Option___none)` depending on whether the index is in bounds, use `as[i]?`. To panic if the
index is out of bounds, use `as[i]!`.

Examples:

- `["spring", "summer", "fall", "winter"].[getD]](#manual-List___getD) 2 "never" = "fall"`
- `["spring", "summer", "fall", "winter"].[getD]](#manual-List___getD) 0 "never" = "spring"`
- `["spring", "summer", "fall", "winter"].[getD]](#manual-List___getD) 4 "never" = "never"`

def

```lean
[List.getLast.{u}]](#manual-List___getLast) {α : Type u} (as : [List]](#manual-List___nil) α) : as ≠ [[]](#manual-List___nil)[]](https://lean-lang.org/doc/reference/latest/Basic-Types/Linked-Lists/#List___nil) → α



[List.getLast.{u}]](#manual-List___getLast) {α : Type u}
  (as : [List]](#manual-List___nil) α) : as ≠ [[]](#manual-List___nil)[]](https://lean-lang.org/doc/reference/latest/Basic-Types/Linked-Lists/#List___nil) → α
```

Returns the last element of a non-empty list.

Examples:

- `["circle", "rectangle"].[getLast]](#manual-List___getLast) (by [decide]](#manual-decide)) = "rectangle"`
- `["circle"].[getLast]](#manual-List___getLast) (by [decide]](#manual-decide)) = "circle"`

def

```lean
[List.getLast?.{u}]](#manual-List___getLast___) {α : Type u} : [List]](#manual-List___nil) α → [Option]](#manual-Option___none) α



[List.getLast?.{u}]](#manual-List___getLast___) {α : Type u} :
  [List]](#manual-List___nil) α → [Option]](#manual-Option___none) α
```

Returns the last element in the list, or `[none]](#manual-Option___none)` if the list is empty.

Alternatives include `[List.getLastD]](#manual-List___getLastD)`, which takes a fallback value for empty lists, and
`[List.getLast!]](#manual-List___getLast___-next)`, which panics on empty lists.

Examples:

- `["circle", "rectangle"].[getLast?]](#manual-List___getLast___) = [some]](#manual-Option___none) "rectangle"`
- `["circle"].[getLast?]](#manual-List___getLast___) = [some]](#manual-Option___none) "circle"`
- `([] : [List]](#manual-List___nil) [String]](#manual-String___ofByteArray)).[getLast?]](#manual-List___getLast___) = [none]](#manual-Option___none)`

def

```lean
[List.getLastD.{u}]](#manual-List___getLastD) {α : Type u} (as : [List]](#manual-List___nil) α) (fallback : α) : α



[List.getLastD.{u}]](#manual-List___getLastD) {α : Type u}
  (as : [List]](#manual-List___nil) α) (fallback : α) : α
```

Returns the last element in the list, or `fallback` if the list is empty.

Alternatives include `[List.getLast?]](#manual-List___getLast___)`, which returns an `[Option]](#manual-Option___none)`, and `[List.getLast!]](#manual-List___getLast___-next)`, which panics
on empty lists.

Examples:

- `["circle", "rectangle"].[getLastD]](#manual-List___getLastD) "oval" = "rectangle"`
- `["circle"].[getLastD]](#manual-List___getLastD) "oval" = "circle"`
- `([] : [List]](#manual-List___nil) [String]](#manual-String___ofByteArray)).[getLastD]](#manual-List___getLastD) "oval" = "oval"`

def

```lean
[List.getLast!.{u_1}]](#manual-List___getLast___-next) {α : Type u_1} [[Inhabited]](#manual-Inhabited___mk) α] : [List]](#manual-List___nil) α → α



[List.getLast!.{u_1}]](#manual-List___getLast___-next) {α : Type u_1}
  [[Inhabited]](#manual-Inhabited___mk) α] : [List]](#manual-List___nil) α → α
```

Returns the last element in the list. Panics and returns `[default]](#manual-Inhabited___mk)` if the list is empty.

Safer alternatives include:

- `getLast?`, which returns an `[Option]](#manual-Option___none)`,
- `getLastD`, which takes a fallback value for empty lists, and
- `getLast`, which requires a proof that the list is non-empty.

Examples:

- `["circle", "rectangle"].[getLast!]](#manual-List___getLast___-next) = "rectangle"`
- `["circle"].[getLast!]](#manual-List___getLast___-next) = "circle"`

def

```lean
[List.lookup.{u, v}]](#manual-List___lookup) {α : Type u} {β : Type v} [[BEq]](#manual-BEq___mk) α] :
  α → [List]](#manual-List___nil) [(]](#manual-Prod___mk)α [×]](#manual-Prod___mk) β[)]](#manual-Prod___mk) → [Option]](#manual-Option___none) β



[List.lookup.{u, v}]](#manual-List___lookup) {α : Type u}
  {β : Type v} [[BEq]](#manual-BEq___mk) α] :
  α → [List]](#manual-List___nil) [(]](#manual-Prod___mk)α [×]](#manual-Prod___mk) β[)]](#manual-Prod___mk) → [Option]](#manual-Option___none) β
```

Treats the list as an association list that maps keys to values, returning the first value whose key
is equal to the specified key.

`O(|l|)`.

Examples:

- `[(1, "one"), (3, "three"), (3, "other")].[lookup]](#manual-List___lookup) 3 = [some]](#manual-Option___none) "three"`
- `[(1, "one"), (3, "three"), (3, "other")].[lookup]](#manual-List___lookup) 2 = [none]](#manual-Option___none)`

def

```lean
[List.max?.{u}]](#manual-List___max___) {α : Type u} [[Max]](#manual-Max___mk) α] : [List]](#manual-List___nil) α → [Option]](#manual-Option___none) α



[List.max?.{u}]](#manual-List___max___) {α : Type u} [[Max]](#manual-Max___mk) α] :
  [List]](#manual-List___nil) α → [Option]](#manual-Option___none) α
```

Returns the largest element of the list if it is not empty, or `[none]](#manual-Option___none)` if it is empty.

Examples:

- `[].max? = none`
- `[4].[max?]](#manual-List___max___) = [some]](#manual-Option___none) 4`
- `[1, 4, 2, 10, 6].[max?]](#manual-List___max___) = [some]](#manual-Option___none) 10`

def

```lean
[List.min?.{u}]](#manual-List___min___) {α : Type u} [[Min]](#manual-Min___mk) α] : [List]](#manual-List___nil) α → [Option]](#manual-Option___none) α



[List.min?.{u}]](#manual-List___min___) {α : Type u} [[Min]](#manual-Min___mk) α] :
  [List]](#manual-List___nil) α → [Option]](#manual-Option___none) α
```

Returns the smallest element of the list if it is not empty, or `[none]](#manual-Option___none)` if it is empty.

Examples:

- `[].min? = none`
- `[4].[min?]](#manual-List___min___) = [some]](#manual-Option___none) 4`
- `[1, 4, 2, 10, 6].[min?]](#manual-List___min___) = [some]](#manual-Option___none) 1`

#### 20.15.3.6. Queries {#manual-The-Lean-Language-Reference--Basic-Types--Linked-Lists--API-Reference--Queries}

def

```lean
[List.count.{u}]](#manual-List___count) {α : Type u} [[BEq]](#manual-BEq___mk) α] (a : α) : [List]](#manual-List___nil) α → [Nat]](#manual-Nat___zero)



[List.count.{u}]](#manual-List___count) {α : Type u} [[BEq]](#manual-BEq___mk) α]
  (a : α) : [List]](#manual-List___nil) α → [Nat]](#manual-Nat___zero)
```

Counts the number of times an element occurs in a list.

Examples:

- `[1, 1, 2, 3, 5].[count]](#manual-List___count) 1 = 2`
- `[1, 1, 2, 3, 5].[count]](#manual-List___count) 5 = 1`
- `[1, 1, 2, 3, 5].[count]](#manual-List___count) 4 = 0`

def

```lean
[List.countP.{u}]](#manual-List___countP) {α : Type u} (p : α → [Bool]](#manual-Bool___false)) (l : [List]](#manual-List___nil) α) : [Nat]](#manual-Nat___zero)



[List.countP.{u}]](#manual-List___countP) {α : Type u}
  (p : α → [Bool]](#manual-Bool___false)) (l : [List]](#manual-List___nil) α) : [Nat]](#manual-Nat___zero)
```

Counts the number of elements in the list `l` that satisfy the Boolean predicate `p`.

Examples:

- `[1, 2, 3, 4, 5].[countP]](#manual-List___countP) (· % 2 == 0) = 2`
- `[1, 2, 3, 4, 5].[countP]](#manual-List___countP) (· < 5) = 4`
- `[1, 2, 3, 4, 5].[countP]](#manual-List___countP) (· > 5) = 0`

def

```lean
[List.idxOf.{u}]](#manual-List___idxOf) {α : Type u} [[BEq]](#manual-BEq___mk) α] (a : α) : [List]](#manual-List___nil) α → [Nat]](#manual-Nat___zero)



[List.idxOf.{u}]](#manual-List___idxOf) {α : Type u} [[BEq]](#manual-BEq___mk) α]
  (a : α) : [List]](#manual-List___nil) α → [Nat]](#manual-Nat___zero)
```

Returns the index of the first element equal to `a`, or the length of the list if no element is
equal to `a`.

Examples:

- `["carrot", "potato", "broccoli"].[idxOf]](#manual-List___idxOf) "carrot" = 0`
- `["carrot", "potato", "broccoli"].[idxOf]](#manual-List___idxOf) "broccoli" = 2`
- `["carrot", "potato", "broccoli"].[idxOf]](#manual-List___idxOf) "tomato" = 3`
- `["carrot", "potato", "broccoli"].[idxOf]](#manual-List___idxOf) "anything else" = 3`

def

```lean
[List.idxOf?.{u}]](#manual-List___idxOf___) {α : Type u} [[BEq]](#manual-BEq___mk) α] (a : α) : [List]](#manual-List___nil) α → [Option]](#manual-Option___none) [Nat]](#manual-Nat___zero)



[List.idxOf?.{u}]](#manual-List___idxOf___) {α : Type u} [[BEq]](#manual-BEq___mk) α]
  (a : α) : [List]](#manual-List___nil) α → [Option]](#manual-Option___none) [Nat]](#manual-Nat___zero)
```

Returns the index of the first element equal to `a`, or `[none]](#manual-Option___none)` if no element is equal to `a`.

Examples:

- `["carrot", "potato", "broccoli"].[idxOf?]](#manual-List___idxOf___) "carrot" = [some]](#manual-Option___none) 0`
- `["carrot", "potato", "broccoli"].[idxOf?]](#manual-List___idxOf___) "broccoli" = [some]](#manual-Option___none) 2`
- `["carrot", "potato", "broccoli"].[idxOf?]](#manual-List___idxOf___) "tomato" = [none]](#manual-Option___none)`
- `["carrot", "potato", "broccoli"].[idxOf?]](#manual-List___idxOf___) "anything else" = [none]](#manual-Option___none)`

def

```lean
[List.finIdxOf?.{u}]](#manual-List___finIdxOf___) {α : Type u} [[BEq]](#manual-BEq___mk) α] (a : α) (l : [List]](#manual-List___nil) α) :
  [Option]](#manual-Option___none) ([Fin]](#manual-Fin___mk) l.[length]](#manual-List___length))



[List.finIdxOf?.{u}]](#manual-List___finIdxOf___) {α : Type u} [[BEq]](#manual-BEq___mk) α]
  (a : α) (l : [List]](#manual-List___nil) α) :
  [Option]](#manual-Option___none) ([Fin]](#manual-Fin___mk) l.[length]](#manual-List___length))
```

Returns the index of the first element equal to `a`, or the length of the list if no element is
equal to `a`. The index is returned as a `[Fin]](#manual-Fin___mk)`, which guarantees that it is in bounds.

Examples:

- `["carrot", "potato", "broccoli"].[finIdxOf?]](#manual-List___finIdxOf___) "carrot" = [some]](#manual-Option___none) 0`
- `["carrot", "potato", "broccoli"].[finIdxOf?]](#manual-List___finIdxOf___) "broccoli" = [some]](#manual-Option___none) 2`
- `["carrot", "potato", "broccoli"].[finIdxOf?]](#manual-List___finIdxOf___) "tomato" = [none]](#manual-Option___none)`
- `["carrot", "potato", "broccoli"].[finIdxOf?]](#manual-List___finIdxOf___) "anything else" = [none]](#manual-Option___none)`

def

```lean
[List.find?.{u}]](#manual-List___find___) {α : Type u} (p : α → [Bool]](#manual-Bool___false)) : [List]](#manual-List___nil) α → [Option]](#manual-Option___none) α



[List.find?.{u}]](#manual-List___find___) {α : Type u}
  (p : α → [Bool]](#manual-Bool___false)) : [List]](#manual-List___nil) α → [Option]](#manual-Option___none) α
```

Returns the first element of the list for which the predicate `p` returns `[true]](#manual-Bool___false)`, or `[none]](#manual-Option___none)` if no
such element is found.

`O(|l|)`.

Examples:

- `[7, 6, 5, 8, 1, 2, 6].[find?]](#manual-List___find___) (· < 5) = [some]](#manual-Option___none) 1`
- `[7, 6, 5, 8, 1, 2, 6].[find?]](#manual-List___find___) (· < 1) = [none]](#manual-Option___none)`

def

```lean
[List.findFinIdx?.{u}]](#manual-List___findFinIdx___) {α : Type u} (p : α → [Bool]](#manual-Bool___false)) (l : [List]](#manual-List___nil) α) :
  [Option]](#manual-Option___none) ([Fin]](#manual-Fin___mk) l.[length]](#manual-List___length))



[List.findFinIdx?.{u}]](#manual-List___findFinIdx___) {α : Type u}
  (p : α → [Bool]](#manual-Bool___false)) (l : [List]](#manual-List___nil) α) :
  [Option]](#manual-Option___none) ([Fin]](#manual-Fin___mk) l.[length]](#manual-List___length))
```

Returns the index of the first element for which `p` returns `[true]](#manual-Bool___false)`, or `[none]](#manual-Option___none)` if there is no such
element. The index is returned as a `[Fin]](#manual-Fin___mk)`, which guarantees that it is in bounds.

Examples:

- `[7, 6, 5, 8, 1, 2, 6].[findFinIdx?]](#manual-List___findFinIdx___) (· < 5) = [some]](#manual-Option___none) (4 : [Fin]](#manual-Fin___mk) 7)`
- `[7, 6, 5, 8, 1, 2, 6].[findFinIdx?]](#manual-List___findFinIdx___) (· < 1) = [none]](#manual-Option___none)`

def

```lean
[List.findIdx.{u}]](#manual-List___findIdx) {α : Type u} (p : α → [Bool]](#manual-Bool___false)) (l : [List]](#manual-List___nil) α) : [Nat]](#manual-Nat___zero)



[List.findIdx.{u}]](#manual-List___findIdx) {α : Type u}
  (p : α → [Bool]](#manual-Bool___false)) (l : [List]](#manual-List___nil) α) : [Nat]](#manual-Nat___zero)
```

Returns the index of the first element for which `p` returns `[true]](#manual-Bool___false)`, or the length of the list if
there is no such element.

Examples:

- `[7, 6, 5, 8, 1, 2, 6].[findIdx]](#manual-List___findIdx) (· < 5) = 4`
- `[7, 6, 5, 8, 1, 2, 6].[findIdx]](#manual-List___findIdx) (· < 1) = 7`

def

```lean
[List.findIdx?.{u}]](#manual-List___findIdx___) {α : Type u} (p : α → [Bool]](#manual-Bool___false)) (l : [List]](#manual-List___nil) α) : [Option]](#manual-Option___none) [Nat]](#manual-Nat___zero)



[List.findIdx?.{u}]](#manual-List___findIdx___) {α : Type u}
  (p : α → [Bool]](#manual-Bool___false)) (l : [List]](#manual-List___nil) α) : [Option]](#manual-Option___none) [Nat]](#manual-Nat___zero)
```

Returns the index of the first element for which `p` returns `[true]](#manual-Bool___false)`, or `[none]](#manual-Option___none)` if there is no such
element.

Examples:

- `[7, 6, 5, 8, 1, 2, 6].[findIdx]](#manual-List___findIdx) (· < 5) = [some]](#manual-Option___none) 4`
- `[7, 6, 5, 8, 1, 2, 6].[findIdx]](#manual-List___findIdx) (· < 1) = [none]](#manual-Option___none)`

def

```lean
[List.findM?.{u}]](#manual-List___findM___) {m : Type → Type u} [[Monad]](#manual-Monad___mk) m] {α : Type}
  (p : α → m [Bool]](#manual-Bool___false)) : [List]](#manual-List___nil) α → m ([Option]](#manual-Option___none) α)



[List.findM?.{u}]](#manual-List___findM___) {m : Type → Type u}
  [[Monad]](#manual-Monad___mk) m] {α : Type} (p : α → m [Bool]](#manual-Bool___false)) :
  [List]](#manual-List___nil) α → m ([Option]](#manual-Option___none) α)
```

Returns the first element of the list for which the monadic predicate `p` returns `[true]](#manual-Bool___false)`, or `[none]](#manual-Option___none)`
if no such element is found. Elements of the list are checked in order.

`O(|l|)`.

Example:

```lean
[#eval]](#manual-Lean___Parser___Command___eval) [7, 6, 5, 8, 1, 2, 6].[findM?]](#manual-List___findM___) fun i => [do]](#manual-Lean___Parser___Term___do)
if i < 5 then
return [true]](#manual-Bool___false)
if i ≤ 6 then
[IO.println](https://lean-lang.org/doc/reference/latest/IO/Console-Output/#IO___println) s!"Almost! {i}"
return [false]](#manual-Bool___false)
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
[List.findSome?.{u, v}]](#manual-List___findSome___) {α : Type u} {β : Type v} (f : α → [Option]](#manual-Option___none) β) :
  [List]](#manual-List___nil) α → [Option]](#manual-Option___none) β



[List.findSome?.{u, v}]](#manual-List___findSome___) {α : Type u}
  {β : Type v} (f : α → [Option]](#manual-Option___none) β) :
  [List]](#manual-List___nil) α → [Option]](#manual-Option___none) β
```

Returns the first non-`[none]](#manual-Option___none)` result of applying `f` to each element of the list in order. Returns
`[none]](#manual-Option___none)` if `f` returns `[none]](#manual-Option___none)` for all elements of the list.

`O(|l|)`.

Examples:

- `[7, 6, 5, 8, 1, 2, 6].[findSome?]](#manual-List___findSome___) (fun x => [if]](#manual-termIfThenElse) x < 5 [then]](#manual-termIfThenElse) [some]](#manual-Option___none) (10 * x) [else]](#manual-termIfThenElse) [none]](#manual-Option___none)) = [some]](#manual-Option___none) 10`
- `[7, 6, 5, 8, 1, 2, 6].[findSome?]](#manual-List___findSome___) (fun x => [if]](#manual-termIfThenElse) x < 1 [then]](#manual-termIfThenElse) [some]](#manual-Option___none) (10 * x) [else]](#manual-termIfThenElse) [none]](#manual-Option___none)) = [none]](#manual-Option___none)`

def

```lean
[List.findSomeM?.{u, v, w}]](#manual-List___findSomeM___) {m : Type u → Type v} [[Monad]](#manual-Monad___mk) m] {α : Type w}
  {β : Type u} (f : α → m ([Option]](#manual-Option___none) β)) : [List]](#manual-List___nil) α → m ([Option]](#manual-Option___none) β)



[List.findSomeM?.{u, v, w}]](#manual-List___findSomeM___)
  {m : Type u → Type v} [[Monad]](#manual-Monad___mk) m]
  {α : Type w} {β : Type u}
  (f : α → m ([Option]](#manual-Option___none) β)) :
  [List]](#manual-List___nil) α → m ([Option]](#manual-Option___none) β)
```

Returns the first non-`[none]](#manual-Option___none)` result of applying the monadic function `f` to each element of the
list, in order. Returns `[none]](#manual-Option___none)` if `f` returns `[none]](#manual-Option___none)` for all elements.

`O(|l|)`.

Example:

```lean
[#eval]](#manual-Lean___Parser___Command___eval) [7, 6, 5, 8, 1, 2, 6].[findSomeM?]](#manual-List___findSomeM___) fun i => [do]](#manual-Lean___Parser___Term___do)
if i < 5 then
return [some]](#manual-Option___none) (i * 10)
if i ≤ 6 then
[IO.println](https://lean-lang.org/doc/reference/latest/IO/Console-Output/#IO___println) s!"Almost! {i}"
return [none]](#manual-Option___none)
```

```lean
Almost! 6
Almost! 5
```

```lean
[some]](#manual-Option___none) 10
```

#### 20.15.3.7. Conversions {#manual-The-Lean-Language-Reference--Basic-Types--Linked-Lists--API-Reference--Conversions}

def

```lean
[List.toArray.{u_1}]](#manual-List___toArray) {α : Type u_1} (xs : [List]](#manual-List___nil) α) : [Array](https://lean-lang.org/doc/reference/latest/Basic-Types/Arrays/#Array___mk) α



[List.toArray.{u_1}]](#manual-List___toArray) {α : Type u_1}
  (xs : [List]](#manual-List___nil) α) : [Array](https://lean-lang.org/doc/reference/latest/Basic-Types/Arrays/#Array___mk) α
```

Converts a `[List]](#manual-List___nil) α` into an `[Array](https://lean-lang.org/doc/reference/latest/Basic-Types/Arrays/#Array___mk) α`.

`O(|xs|)`. At runtime, this operation is implemented by `[List.toArrayImpl]](#manual-List___toArrayImpl)` and takes time linear in
the length of the list. `[List.toArray]](#manual-List___toArray)` should be used instead of `[Array.mk](https://lean-lang.org/doc/reference/latest/Basic-Types/Arrays/#Array___mk)`.

Examples:

- `[1, 2, 3].[toArray]](#manual-List___toArray) = #[1, 2, 3]`
- `["monday", "wednesday", friday"].toArray = #["monday", "wednesday", friday"].`

def

```lean
[List.toArrayImpl.{u_1}]](#manual-List___toArrayImpl) {α : Type u_1} (xs : [List]](#manual-List___nil) α) : [Array](https://lean-lang.org/doc/reference/latest/Basic-Types/Arrays/#Array___mk) α



[List.toArrayImpl.{u_1}]](#manual-List___toArrayImpl) {α : Type u_1}
  (xs : [List]](#manual-List___nil) α) : [Array](https://lean-lang.org/doc/reference/latest/Basic-Types/Arrays/#Array___mk) α
```

Converts a `[List]](#manual-List___nil) α` into an `[Array](https://lean-lang.org/doc/reference/latest/Basic-Types/Arrays/#Array___mk) α` by repeatedly pushing elements from the list onto an empty
array. `O(|xs|)`.

Use `[List.toArray]](#manual-List___toArray)` instead of calling this function directly. At runtime, this operation implements
both `[List.toArray]](#manual-List___toArray)` and `[Array.mk](https://lean-lang.org/doc/reference/latest/Basic-Types/Arrays/#Array___mk)`.

def

```lean
[List.toByteArray]](#manual-List___toByteArray) (bs : [List]](#manual-List___nil) [UInt8]](#manual-UInt8___ofBitVec)) : [ByteArray](https://lean-lang.org/doc/reference/latest/Basic-Types/Byte-Arrays/#ByteArray___mk)



[List.toByteArray]](#manual-List___toByteArray) (bs : [List]](#manual-List___nil) [UInt8]](#manual-UInt8___ofBitVec)) :
  [ByteArray](https://lean-lang.org/doc/reference/latest/Basic-Types/Byte-Arrays/#ByteArray___mk)
```

Converts a list of bytes into a `[ByteArray](https://lean-lang.org/doc/reference/latest/Basic-Types/Byte-Arrays/#ByteArray___mk)`.

def

```lean
[List.toFloatArray]](#manual-List___toFloatArray) (ds : [List]](#manual-List___nil) [Float]](#manual-Float___ofModel)) : FloatArray



[List.toFloatArray]](#manual-List___toFloatArray) (ds : [List]](#manual-List___nil) [Float]](#manual-Float___ofModel)) :
  FloatArray
```

Converts a list of floats into a `FloatArray`.

def

```lean
[List.toString.{u_1}]](#manual-List___toString) {α : Type u_1} [ToString α] : [List]](#manual-List___nil) α → [String]](#manual-String___ofByteArray)



[List.toString.{u_1}]](#manual-List___toString) {α : Type u_1}
  [ToString α] : [List]](#manual-List___nil) α → [String]](#manual-String___ofByteArray)
```

Converts a list into a string, using `ToString.toString` to convert its elements.

The resulting string resembles list literal syntax, with the elements separated by `", "` and
enclosed in square brackets.

The resulting string may not be valid Lean syntax, because there's no such expectation for
`ToString` instances.

Examples:

- `[1, 2, 3].[toString]](#manual-List___toString) = "[1, 2, 3]"`
- `["cat", "dog"].[toString]](#manual-List___toString) = "[cat, dog]"`
- `["cat", "dog", ""].[toString]](#manual-List___toString) = "[cat, dog, ]"`

#### 20.15.3.8. Modification {#manual-The-Lean-Language-Reference--Basic-Types--Linked-Lists--API-Reference--Modification}

def

```lean
[List.set.{u_1}]](#manual-List___set) {α : Type u_1} (l : [List]](#manual-List___nil) α) (n : [Nat]](#manual-Nat___zero)) (a : α) : [List]](#manual-List___nil) α



[List.set.{u_1}]](#manual-List___set) {α : Type u_1} (l : [List]](#manual-List___nil) α)
  (n : [Nat]](#manual-Nat___zero)) (a : α) : [List]](#manual-List___nil) α
```

Replaces the value at (zero-based) index `n` in `l` with `a`. If the index is out of bounds, then
the list is returned unmodified.

Examples:

- `["water", "coffee", "soda", "juice"].[set]](#manual-List___set) 1 "tea" = ["water", "tea", "soda", "juice"]`
- `["water", "coffee", "soda", "juice"].[set]](#manual-List___set) 4 "tea" = ["water", "coffee", "soda", "juice"]`

def

```lean
[List.setTR.{u_1}]](#manual-List___setTR) {α : Type u_1} (l : [List]](#manual-List___nil) α) (n : [Nat]](#manual-Nat___zero)) (a : α) : [List]](#manual-List___nil) α



[List.setTR.{u_1}]](#manual-List___setTR) {α : Type u_1}
  (l : [List]](#manual-List___nil) α) (n : [Nat]](#manual-Nat___zero)) (a : α) : [List]](#manual-List___nil) α
```

Replaces the value at (zero-based) index `n` in `l` with `a`. If the index is out of bounds, then
the list is returned unmodified.

This is a tail-recursive version of `[List.set]](#manual-List___set)` that's used at runtime.

Examples:

- `["water", "coffee", "soda", "juice"].[set]](#manual-List___set) 1 "tea" = ["water", "tea", "soda", "juice"]`
- `["water", "coffee", "soda", "juice"].[set]](#manual-List___set) 4 "tea" = ["water", "coffee", "soda", "juice"]`

def

```lean
[List.modify.{u}]](#manual-List___modify) {α : Type u} (l : [List]](#manual-List___nil) α) (i : [Nat]](#manual-Nat___zero)) (f : α → α) : [List]](#manual-List___nil) α



[List.modify.{u}]](#manual-List___modify) {α : Type u} (l : [List]](#manual-List___nil) α)
  (i : [Nat]](#manual-Nat___zero)) (f : α → α) : [List]](#manual-List___nil) α
```

Replaces the element at the given index, if it exists, with the result of applying `f` to it. If the
index is invalid, the list is returned unmodified.

Examples:

- `[1, 2, 3].[modify]](#manual-List___modify) 0 (· * 10) = [10, 2, 3]`
- `[1, 2, 3].[modify]](#manual-List___modify) 2 (· * 10) = [1, 2, 30]`
- `[1, 2, 3].[modify]](#manual-List___modify) 3 (· * 10) = [1, 2, 3]`

def

```lean
[List.modifyTR.{u_1}]](#manual-List___modifyTR) {α : Type u_1} (l : [List]](#manual-List___nil) α) (i : [Nat]](#manual-Nat___zero)) (f : α → α) :
  [List]](#manual-List___nil) α



[List.modifyTR.{u_1}]](#manual-List___modifyTR) {α : Type u_1}
  (l : [List]](#manual-List___nil) α) (i : [Nat]](#manual-Nat___zero)) (f : α → α) :
  [List]](#manual-List___nil) α
```

Replaces the element at the given index, if it exists, with the result of applying `f` to it.

This is a tail-recursive version of `[List.modify]](#manual-List___modify)`.

Examples:

- `[1, 2, 3].[modifyTR]](#manual-List___modifyTR) 0 (· * 10) = [10, 2, 3]`
- `[1, 2, 3].[modifyTR]](#manual-List___modifyTR) 2 (· * 10) = [1, 2, 30]`
- `[1, 2, 3].[modifyTR]](#manual-List___modifyTR) 3 (· * 10) = [1, 2, 3]`

def

```lean
[List.modifyHead.{u}]](#manual-List___modifyHead) {α : Type u} (f : α → α) : [List]](#manual-List___nil) α → [List]](#manual-List___nil) α



[List.modifyHead.{u}]](#manual-List___modifyHead) {α : Type u}
  (f : α → α) : [List]](#manual-List___nil) α → [List]](#manual-List___nil) α
```

Replace the head of the list with the result of applying `f` to it. Returns the empty list if the
list is empty.

Examples:

- `[1, 2, 3].[modifyHead]](#manual-List___modifyHead) (· * 10) = [10, 2, 3]`
- `[].[modifyHead]](#manual-List___modifyHead) (· * 10) = []`

def

```lean
[List.modifyTailIdx.{u}]](#manual-List___modifyTailIdx) {α : Type u} (l : [List]](#manual-List___nil) α) (i : [Nat]](#manual-Nat___zero))
  (f : [List]](#manual-List___nil) α → [List]](#manual-List___nil) α) : [List]](#manual-List___nil) α



[List.modifyTailIdx.{u}]](#manual-List___modifyTailIdx) {α : Type u}
  (l : [List]](#manual-List___nil) α) (i : [Nat]](#manual-Nat___zero))
  (f : [List]](#manual-List___nil) α → [List]](#manual-List___nil) α) : [List]](#manual-List___nil) α
```

Replaces the `n`th tail of `l` with the result of applying `f` to it. Returns the input without
using `f` if the index is larger than the length of the List.

Examples:

```lean
["circle", "square", "triangle"].[modifyTailIdx]](#manual-List___modifyTailIdx) 1 [List.reverse]](#manual-List___reverse)
```

```lean
["circle", "triangle", "square"]
```

```lean
["circle", "square", "triangle"].[modifyTailIdx]](#manual-List___modifyTailIdx) 1 (fun xs => xs ++ xs)
```

```lean
["circle", "square", "triangle", "square", "triangle"]
```

```lean
["circle", "square", "triangle"].[modifyTailIdx]](#manual-List___modifyTailIdx) 2 (fun xs => xs ++ xs)
```

```lean
["circle", "square", "triangle", "triangle"]
```

```lean
["circle", "square", "triangle"].[modifyTailIdx]](#manual-List___modifyTailIdx) 5 (fun xs => xs ++ xs)
```

```lean
["circle", "square", "triangle"]
```

def

```lean
[List.erase.{u_1}]](#manual-List___erase) {α : Type u_1} [[BEq]](#manual-BEq___mk) α] : [List]](#manual-List___nil) α → α → [List]](#manual-List___nil) α



[List.erase.{u_1}]](#manual-List___erase) {α : Type u_1} [[BEq]](#manual-BEq___mk) α] :
  [List]](#manual-List___nil) α → α → [List]](#manual-List___nil) α
```

Removes the first occurrence of `a` from `l`. If `a` does not occur in `l`, the list is returned
unmodified.

`O(|l|)`.

Examples:

- `[1, 5, 3, 2, 5].[erase]](#manual-List___erase) 5 = [1, 3, 2, 5]`
- `[1, 5, 3, 2, 5].[erase]](#manual-List___erase) 6 = [1, 5, 3, 2, 5]`

def

```lean
[List.eraseTR.{u_1}]](#manual-List___eraseTR) {α : Type u_1} [[BEq]](#manual-BEq___mk) α] (l : [List]](#manual-List___nil) α) (a : α) : [List]](#manual-List___nil) α



[List.eraseTR.{u_1}]](#manual-List___eraseTR) {α : Type u_1} [[BEq]](#manual-BEq___mk) α]
  (l : [List]](#manual-List___nil) α) (a : α) : [List]](#manual-List___nil) α
```

Removes the first occurrence of `a` from `l`. If `a` does not occur in `l`, the list is returned
unmodified.

`O(|l|)`.

This is a tail-recursive version of `[List.erase]](#manual-List___erase)`, used in runtime code.

Examples:

- `[1, 5, 3, 2, 5].[eraseTR]](#manual-List___eraseTR) 5 = [1, 3, 2, 5]`
- `[1, 5, 3, 2, 5].[eraseTR]](#manual-List___eraseTR) 6 = [1, 5, 3, 2, 5]`

def

```lean
[List.eraseDups.{u_1}]](#manual-List___eraseDups) {α : Type u_1} [[BEq]](#manual-BEq___mk) α] (as : [List]](#manual-List___nil) α) : [List]](#manual-List___nil) α



[List.eraseDups.{u_1}]](#manual-List___eraseDups) {α : Type u_1}
  [[BEq]](#manual-BEq___mk) α] (as : [List]](#manual-List___nil) α) : [List]](#manual-List___nil) α
```

Erases duplicated elements in the list, keeping the first occurrence of duplicated elements.

`O(|l|^2)`.

Examples:

- `[1, 3, 2, 2, 3, 5].[eraseDups]](#manual-List___eraseDups) = [1, 3, 2, 5]`
- `["red", "green", "green", "blue"].[eraseDups]](#manual-List___eraseDups) = ["red", "green", "blue"]`

def

```lean
[List.eraseIdx.{u}]](#manual-List___eraseIdx) {α : Type u} (l : [List]](#manual-List___nil) α) (i : [Nat]](#manual-Nat___zero)) : [List]](#manual-List___nil) α



[List.eraseIdx.{u}]](#manual-List___eraseIdx) {α : Type u}
  (l : [List]](#manual-List___nil) α) (i : [Nat]](#manual-Nat___zero)) : [List]](#manual-List___nil) α
```

Removes the element at the specified index. If the index is out of bounds, the list is returned
unmodified.

`O(i)`.

Examples:

- `[0, 1, 2, 3, 4].[eraseIdx]](#manual-List___eraseIdx) 0 = [1, 2, 3, 4]`
- `[0, 1, 2, 3, 4].[eraseIdx]](#manual-List___eraseIdx) 1 = [0, 2, 3, 4]`
- `[0, 1, 2, 3, 4].[eraseIdx]](#manual-List___eraseIdx) 5 = [0, 1, 2, 3, 4]`

def

```lean
[List.eraseIdxTR.{u_1}]](#manual-List___eraseIdxTR) {α : Type u_1} (l : [List]](#manual-List___nil) α) (n : [Nat]](#manual-Nat___zero)) : [List]](#manual-List___nil) α



[List.eraseIdxTR.{u_1}]](#manual-List___eraseIdxTR) {α : Type u_1}
  (l : [List]](#manual-List___nil) α) (n : [Nat]](#manual-Nat___zero)) : [List]](#manual-List___nil) α
```

Removes the element at the specified index. If the index is out of bounds, the list is returned
unmodified.

`O(i)`.

This is a tail-recursive version of `[List.eraseIdx]](#manual-List___eraseIdx)`, used at runtime.

Examples:

- `[0, 1, 2, 3, 4].[eraseIdxTR]](#manual-List___eraseIdxTR) 0 = [1, 2, 3, 4]`
- `[0, 1, 2, 3, 4].[eraseIdxTR]](#manual-List___eraseIdxTR) 1 = [0, 2, 3, 4]`
- `[0, 1, 2, 3, 4].[eraseIdxTR]](#manual-List___eraseIdxTR) 5 = [0, 1, 2, 3, 4]`

def

```lean
[List.eraseP.{u}]](#manual-List___eraseP) {α : Type u} (p : α → [Bool]](#manual-Bool___false)) : [List]](#manual-List___nil) α → [List]](#manual-List___nil) α



[List.eraseP.{u}]](#manual-List___eraseP) {α : Type u}
  (p : α → [Bool]](#manual-Bool___false)) : [List]](#manual-List___nil) α → [List]](#manual-List___nil) α
```

Removes the first element of a list for which `p` returns `[true]](#manual-Bool___false)`. If no element satisfies `p`, then
the list is returned unchanged.

Examples:

- `[2, 1, 2, 1, 3, 4].[eraseP]](#manual-List___eraseP) (· < 2) = [2, 2, 1, 3, 4]`
- `[2, 1, 2, 1, 3, 4].[eraseP]](#manual-List___eraseP) (· > 2) = [2, 1, 2, 1, 4]`
- `[2, 1, 2, 1, 3, 4].[eraseP]](#manual-List___eraseP) (· > 8) = [2, 1, 2, 1, 3, 4]`

def

```lean
[List.erasePTR.{u_1}]](#manual-List___erasePTR) {α : Type u_1} (p : α → [Bool]](#manual-Bool___false)) (l : [List]](#manual-List___nil) α) : [List]](#manual-List___nil) α



[List.erasePTR.{u_1}]](#manual-List___erasePTR) {α : Type u_1}
  (p : α → [Bool]](#manual-Bool___false)) (l : [List]](#manual-List___nil) α) : [List]](#manual-List___nil) α
```

Removes the first element of a list for which `p` returns `[true]](#manual-Bool___false)`. If no element satisfies `p`, then
the list is returned unchanged.

This is a tail-recursive version of `eraseP`, used at runtime.

Examples:

- `[2, 1, 2, 1, 3, 4].[erasePTR]](#manual-List___erasePTR) (· < 2) = [2, 2, 1, 3, 4]`
- `[2, 1, 2, 1, 3, 4].[erasePTR]](#manual-List___erasePTR) (· > 2) = [2, 1, 2, 1, 4]`
- `[2, 1, 2, 1, 3, 4].[erasePTR]](#manual-List___erasePTR) (· > 8) = [2, 1, 2, 1, 3, 4]`

def

```lean
[List.eraseReps.{u_1}]](#manual-List___eraseReps) {α : Type u_1} [[BEq]](#manual-BEq___mk) α] (as : [List]](#manual-List___nil) α) : [List]](#manual-List___nil) α



[List.eraseReps.{u_1}]](#manual-List___eraseReps) {α : Type u_1}
  [[BEq]](#manual-BEq___mk) α] (as : [List]](#manual-List___nil) α) : [List]](#manual-List___nil) α
```

Erases repeated elements, keeping the first element of each run.

`O(|l|)`.

Example:

- `[1, 3, 2, 2, 2, 3, 3, 5].[eraseReps]](#manual-List___eraseReps) = [1, 3, 2, 3, 5]`

def

```lean
[List.extract.{u}]](#manual-List___extract) {α : Type u} (l : [List]](#manual-List___nil) α) (start : [Nat]](#manual-Nat___zero) := 0)
  (stop : [Nat]](#manual-Nat___zero) := l.[length]](#manual-List___length)) : [List]](#manual-List___nil) α



[List.extract.{u}]](#manual-List___extract) {α : Type u} (l : [List]](#manual-List___nil) α)
  (start : [Nat]](#manual-Nat___zero) := 0)
  (stop : [Nat]](#manual-Nat___zero) := l.[length]](#manual-List___length)) : [List]](#manual-List___nil) α
```

Returns the slice of `l` from indices `start` (inclusive) to `stop` (exclusive).

Examples:

- [0, 1, 2, 3, 4, 5].extract 1 2 = [1]
- [0, 1, 2, 3, 4, 5].extract 2 2 = []
- [0, 1, 2, 3, 4, 5].extract 2 4 = [2, 3]
- [0, 1, 2, 3, 4, 5].extract 2 = [2, 3, 4, 5]
- [0, 1, 2, 3, 4, 5].extract (stop := 2) = [0, 1]

def

```lean
[List.removeAll.{u}]](#manual-List___removeAll) {α : Type u} [[BEq]](#manual-BEq___mk) α] (xs ys : [List]](#manual-List___nil) α) : [List]](#manual-List___nil) α



[List.removeAll.{u}]](#manual-List___removeAll) {α : Type u} [[BEq]](#manual-BEq___mk) α]
  (xs ys : [List]](#manual-List___nil) α) : [List]](#manual-List___nil) α
```

Removes all elements of `xs` that are present in `ys`.

`O(|xs| * |ys|)`.

Examples:

- `[1, 1, 5, 1, 2, 4, 5].[removeAll]](#manual-List___removeAll) [1, 2, 2] = [5, 4, 5]`
- `[1, 2, 3, 2].[removeAll]](#manual-List___removeAll) [] = [1, 2, 3, 2]`
- `[1, 2, 3, 2].[removeAll]](#manual-List___removeAll) [3] = [1, 2, 2]`

def

```lean
[List.replace.{u}]](#manual-List___replace) {α : Type u} [[BEq]](#manual-BEq___mk) α] (l : [List]](#manual-List___nil) α) (a b : α) : [List]](#manual-List___nil) α



[List.replace.{u}]](#manual-List___replace) {α : Type u} [[BEq]](#manual-BEq___mk) α]
  (l : [List]](#manual-List___nil) α) (a b : α) : [List]](#manual-List___nil) α
```

Replaces the first element of the list `l` that is equal to `a` with `b`. If no element is equal to
`a`, then the list is returned unchanged.

`O(|l|)`.

Examples:

- `[1, 4, 2, 3, 3, 7].[replace]](#manual-List___replace) 3 6 = [1, 4, 2, 6, 3, 7]`
- `[1, 4, 2, 3, 3, 7].[replace]](#manual-List___replace) 5 6 = [1, 4, 2, 3, 3, 7]`

def

```lean
[List.replaceTR.{u_1}]](#manual-List___replaceTR) {α : Type u_1} [[BEq]](#manual-BEq___mk) α] (l : [List]](#manual-List___nil) α) (b c : α) :
  [List]](#manual-List___nil) α



[List.replaceTR.{u_1}]](#manual-List___replaceTR) {α : Type u_1}
  [[BEq]](#manual-BEq___mk) α] (l : [List]](#manual-List___nil) α) (b c : α) : [List]](#manual-List___nil) α
```

Replaces the first element of the list `l` that is equal to `a` with `b`. If no element is equal to
`a`, then the list is returned unchanged.

`O(|l|)`. This is a tail-recursive version of `[List.replace]](#manual-List___replace)` that's used in runtime code.

Examples:

- `[1, 4, 2, 3, 3, 7].[replaceTR]](#manual-List___replaceTR) 3 6 = [1, 4, 2, 6, 3, 7]`
- `[1, 4, 2, 3, 3, 7].[replaceTR]](#manual-List___replaceTR) 5 6 = [1, 4, 2, 3, 3, 7]`

def

```lean
[List.reverse.{u}]](#manual-List___reverse) {α : Type u} (as : [List]](#manual-List___nil) α) : [List]](#manual-List___nil) α



[List.reverse.{u}]](#manual-List___reverse) {α : Type u}
  (as : [List]](#manual-List___nil) α) : [List]](#manual-List___nil) α
```

Reverses a list.

`O(|as|)`.

Because of the “functional but in place” optimization implemented by Lean's compiler, this function
does not allocate a new list when its reference to the input list is unshared: it simply walks the
linked list and reverses all the node pointers.

Examples:

- `[1, 2, 3, 4].[reverse]](#manual-List___reverse) = [4, 3, 2, 1]`
- `[].[reverse]](#manual-List___reverse) = []`

def

```lean
[List.flatten.{u_1}]](#manual-List___flatten) {α : Type u_1} : [List]](#manual-List___nil) ([List]](#manual-List___nil) α) → [List]](#manual-List___nil) α



[List.flatten.{u_1}]](#manual-List___flatten) {α : Type u_1} :
  [List]](#manual-List___nil) ([List]](#manual-List___nil) α) → [List]](#manual-List___nil) α
```

Concatenates a list of lists into a single list, preserving the order of the elements.

`O(|flatten L|)`.

Examples:

- `[["a"], ["b", "c"]].[flatten]](#manual-List___flatten) = ["a", "b", "c"]`
- `[["a"], [], ["b", "c"], ["d", "e", "f"]].[flatten]](#manual-List___flatten) = ["a", "b", "c", "d", "e", "f"]`

def

```lean
[List.flattenTR.{u_1}]](#manual-List___flattenTR) {α : Type u_1} (l : [List]](#manual-List___nil) ([List]](#manual-List___nil) α)) : [List]](#manual-List___nil) α



[List.flattenTR.{u_1}]](#manual-List___flattenTR) {α : Type u_1}
  (l : [List]](#manual-List___nil) ([List]](#manual-List___nil) α)) : [List]](#manual-List___nil) α
```

Concatenates a list of lists into a single list, preserving the order of the elements.

`O(|flatten L|)`. This is a tail-recursive version of `[List.flatten]](#manual-List___flatten)`, used in runtime code.

Examples:

- `[["a"], ["b", "c"]].[flattenTR]](#manual-List___flattenTR) = ["a", "b", "c"]`
- `[["a"], [], ["b", "c"], ["d", "e", "f"]].[flattenTR]](#manual-List___flattenTR) = ["a", "b", "c", "d", "e", "f"]`

def

```lean
[List.rotateLeft.{u}]](#manual-List___rotateLeft) {α : Type u} (xs : [List]](#manual-List___nil) α) (i : [Nat]](#manual-Nat___zero) := 1) : [List]](#manual-List___nil) α



[List.rotateLeft.{u}]](#manual-List___rotateLeft) {α : Type u}
  (xs : [List]](#manual-List___nil) α) (i : [Nat]](#manual-Nat___zero) := 1) : [List]](#manual-List___nil) α
```

Rotates the elements of `xs` to the left, moving `i % xs.[length]](#manual-List___length)` elements from the start of the list
to the end.

`O(|xs|)`.

Examples:

- `[1, 2, 3, 4, 5].[rotateLeft]](#manual-List___rotateLeft) 3 = [4, 5, 1, 2, 3]`
- `[1, 2, 3, 4, 5].[rotateLeft]](#manual-List___rotateLeft) 5 = [1, 2, 3, 4, 5]`
- `[1, 2, 3, 4, 5].[rotateLeft]](#manual-List___rotateLeft) 1 = [2, 3, 4, 5, 1]`

def

```lean
[List.rotateRight.{u}]](#manual-List___rotateRight) {α : Type u} (xs : [List]](#manual-List___nil) α) (i : [Nat]](#manual-Nat___zero) := 1) : [List]](#manual-List___nil) α



[List.rotateRight.{u}]](#manual-List___rotateRight) {α : Type u}
  (xs : [List]](#manual-List___nil) α) (i : [Nat]](#manual-Nat___zero) := 1) : [List]](#manual-List___nil) α
```

Rotates the elements of `xs` to the right, moving `i % xs.[length]](#manual-List___length)` elements from the end of the list
to the start.

After rotation, the element at `xs[n]` is at index `(i + n) % l.length`. `O(|xs|)`.

Examples:

- `[1, 2, 3, 4, 5].[rotateRight]](#manual-List___rotateRight) 3 = [3, 4, 5, 1, 2]`
- `[1, 2, 3, 4, 5].[rotateRight]](#manual-List___rotateRight) 5 = [1, 2, 3, 4, 5]`
- `[1, 2, 3, 4, 5].[rotateRight]](#manual-List___rotateRight) 1 = [5, 1, 2, 3, 4]`

def

```lean
[List.leftpad.{u}]](#manual-List___leftpad) {α : Type u} (n : [Nat]](#manual-Nat___zero)) (a : α) (l : [List]](#manual-List___nil) α) : [List]](#manual-List___nil) α



[List.leftpad.{u}]](#manual-List___leftpad) {α : Type u} (n : [Nat]](#manual-Nat___zero))
  (a : α) (l : [List]](#manual-List___nil) α) : [List]](#manual-List___nil) α
```

Pads `l : [List]](#manual-List___nil) α` on the left with repeated occurrences of `a : α` until it is of length `n`. If `l`
already has at least `n` elements, it is returned unmodified.

Examples:

- `[1, 2, 3].[leftpad]](#manual-List___leftpad) 5 0 = [0, 0, 1, 2, 3]`
- `["red", "green", "blue"].[leftpad]](#manual-List___leftpad) 4 "blank" = ["blank", "red", "green", "blue"]`
- `["red", "green", "blue"].[leftpad]](#manual-List___leftpad) 3 "blank" = ["red", "green", "blue"]`
- `["red", "green", "blue"].[leftpad]](#manual-List___leftpad) 1 "blank" = ["red", "green", "blue"]`

def

```lean
[List.leftpadTR.{u}]](#manual-List___leftpadTR) {α : Type u} (n : [Nat]](#manual-Nat___zero)) (a : α) (l : [List]](#manual-List___nil) α) : [List]](#manual-List___nil) α



[List.leftpadTR.{u}]](#manual-List___leftpadTR) {α : Type u} (n : [Nat]](#manual-Nat___zero))
  (a : α) (l : [List]](#manual-List___nil) α) : [List]](#manual-List___nil) α
```

Pads `l : [List]](#manual-List___nil) α` on the left with repeated occurrences of `a : α` until it is of length `n`. If `l`
already has at least `n` elements, it is returned unmodified.

This is a tail-recursive version of `[List.leftpad]](#manual-List___leftpad)`, used at runtime.

Examples:

- `[1, 2, 3].leftPadTR 5 0 = [0, 0, 1, 2, 3]`
- `["red", "green", "blue"].leftPadTR 4 "blank" = ["blank", "red", "green", "blue"]`
- `["red", "green", "blue"].leftPadTR 3 "blank" = ["red", "green", "blue"]`
- `["red", "green", "blue"].leftPadTR 1 "blank" = ["red", "green", "blue"]`

def

```lean
[List.rightpad.{u}]](#manual-List___rightpad) {α : Type u} (n : [Nat]](#manual-Nat___zero)) (a : α) (l : [List]](#manual-List___nil) α) : [List]](#manual-List___nil) α



[List.rightpad.{u}]](#manual-List___rightpad) {α : Type u} (n : [Nat]](#manual-Nat___zero))
  (a : α) (l : [List]](#manual-List___nil) α) : [List]](#manual-List___nil) α
```

Pads `l : [List]](#manual-List___nil) α` on the right with repeated occurrences of `a : α` until it is of length `n`. If
`l` already has at least `n` elements, it is returned unmodified.

Examples:

- `[1, 2, 3].[rightpad]](#manual-List___rightpad) 5 0 = [1, 2, 3, 0, 0]`
- `["red", "green", "blue"].[rightpad]](#manual-List___rightpad) 4 "blank" = ["red", "green", "blue", "blank"]`
- `["red", "green", "blue"].[rightpad]](#manual-List___rightpad) 3 "blank" = ["red", "green", "blue"]`
- `["red", "green", "blue"].[rightpad]](#manual-List___rightpad) 1 "blank" = ["red", "green", "blue"]`

##### 20.15.3.8.1. Insertion {#manual-The-Lean-Language-Reference--Basic-Types--Linked-Lists--API-Reference--Modification--Insertion}

def

```lean
[List.insert.{u}]](#manual-List___insert) {α : Type u} [[BEq]](#manual-BEq___mk) α] (a : α) (l : [List]](#manual-List___nil) α) : [List]](#manual-List___nil) α



[List.insert.{u}]](#manual-List___insert) {α : Type u} [[BEq]](#manual-BEq___mk) α]
  (a : α) (l : [List]](#manual-List___nil) α) : [List]](#manual-List___nil) α
```

Inserts an element into a list without duplication.

If the element is present in the list, the list is returned unmodified. Otherwise, the new element
is inserted at the head of the list.

Examples:

- `[1, 2, 3].[insert]](#manual-List___insert) 0 = [0, 1, 2, 3]`
- `[1, 2, 3].[insert]](#manual-List___insert) 4 = [4, 1, 2, 3]`
- `[1, 2, 3].[insert]](#manual-List___insert) 2 = [1, 2, 3]`

def

```lean
[List.insertIdx.{u}]](#manual-List___insertIdx) {α : Type u} (xs : [List]](#manual-List___nil) α) (i : [Nat]](#manual-Nat___zero)) (a : α) : [List]](#manual-List___nil) α



[List.insertIdx.{u}]](#manual-List___insertIdx) {α : Type u}
  (xs : [List]](#manual-List___nil) α) (i : [Nat]](#manual-Nat___zero)) (a : α) : [List]](#manual-List___nil) α
```

Inserts an element into a list at the specified index. If the index is greater than the length of
the list, then the list is returned unmodified.

In other words, the new element is inserted into the list `l` after the first `i` elements of `l`.

Examples:

- `["tues", "thur", "sat"].[insertIdx]](#manual-List___insertIdx) 1 "wed" = ["tues", "wed", "thur", "sat"]`
- `["tues", "thur", "sat"].[insertIdx]](#manual-List___insertIdx) 2 "wed" = ["tues", "thur", "wed", "sat"]`
- `["tues", "thur", "sat"].[insertIdx]](#manual-List___insertIdx) 3 "wed" = ["tues", "thur", "sat", "wed"]`
- `["tues", "thur", "sat"].[insertIdx]](#manual-List___insertIdx) 4 "wed" = ["tues", "thur", "sat"]`

def

```lean
[List.insertIdxTR.{u_1}]](#manual-List___insertIdxTR) {α : Type u_1} (l : [List]](#manual-List___nil) α) (n : [Nat]](#manual-Nat___zero)) (a : α) :
  [List]](#manual-List___nil) α



[List.insertIdxTR.{u_1}]](#manual-List___insertIdxTR) {α : Type u_1}
  (l : [List]](#manual-List___nil) α) (n : [Nat]](#manual-Nat___zero)) (a : α) : [List]](#manual-List___nil) α
```

Inserts an element into a list at the specified index. If the index is greater than the length of
the list, then the list is returned unmodified.

In other words, the new element is inserted into the list `l` after the first `i` elements of `l`.

This is a tail-recursive version of `[List.insertIdx]](#manual-List___insertIdx)`, used at runtime.

Examples:

- `["tues", "thur", "sat"].[insertIdxTR]](#manual-List___insertIdxTR) 1 "wed" = ["tues", "wed", "thur", "sat"]`
- `["tues", "thur", "sat"].[insertIdxTR]](#manual-List___insertIdxTR) 2 "wed" = ["tues", "thur", "wed", "sat"]`
- `["tues", "thur", "sat"].[insertIdxTR]](#manual-List___insertIdxTR) 3 "wed" = ["tues", "thur", "sat", "wed"]`
- `["tues", "thur", "sat"].[insertIdxTR]](#manual-List___insertIdxTR) 4 "wed" = ["tues", "thur", "sat"]`

def

```lean
[List.intersperse.{u}]](#manual-List___intersperse) {α : Type u} (sep : α) (l : [List]](#manual-List___nil) α) : [List]](#manual-List___nil) α



[List.intersperse.{u}]](#manual-List___intersperse) {α : Type u}
  (sep : α) (l : [List]](#manual-List___nil) α) : [List]](#manual-List___nil) α
```

Alternates the elements of `l` with `sep`.

`O(|l|)`.

`[List.intercalate]](#manual-List___intercalate)` is a similar function that alternates a separator list with elements of a list of
lists.

Examples:

- `[List.intersperse]](#manual-List___intersperse) "then" [] = []`
- `[List.intersperse]](#manual-List___intersperse) "then" ["walk"] = ["walk"]`
- `[List.intersperse]](#manual-List___intersperse) "then" ["walk", "run"] = ["walk", "then", "run"]`
- `[List.intersperse]](#manual-List___intersperse) "then" ["walk", "run", "rest"] = ["walk", "then", "run", "then", "rest"]`

def

```lean
[List.intersperseTR.{u}]](#manual-List___intersperseTR) {α : Type u} (sep : α) (l : [List]](#manual-List___nil) α) : [List]](#manual-List___nil) α



[List.intersperseTR.{u}]](#manual-List___intersperseTR) {α : Type u}
  (sep : α) (l : [List]](#manual-List___nil) α) : [List]](#manual-List___nil) α
```

Alternates the elements of `l` with `sep`.

`O(|l|)`.

This is a tail-recursive version of `[List.intersperse]](#manual-List___intersperse)`, used at runtime.

Examples:

- `[List.intersperseTR]](#manual-List___intersperseTR) "then" [] = []`
- `[List.intersperseTR]](#manual-List___intersperseTR) "then" ["walk"] = ["walk"]`
- `[List.intersperseTR]](#manual-List___intersperseTR) "then" ["walk", "run"] = ["walk", "then", "run"]`
- `[List.intersperseTR]](#manual-List___intersperseTR) "then" ["walk", "run", "rest"] = ["walk", "then", "run", "then", "rest"]`

def

```lean
[List.intercalate.{u}]](#manual-List___intercalate) {α : Type u} (sep : [List]](#manual-List___nil) α) (xs : [List]](#manual-List___nil) ([List]](#manual-List___nil) α)) :
  [List]](#manual-List___nil) α



[List.intercalate.{u}]](#manual-List___intercalate) {α : Type u}
  (sep : [List]](#manual-List___nil) α) (xs : [List]](#manual-List___nil) ([List]](#manual-List___nil) α)) :
  [List]](#manual-List___nil) α
```

Alternates the lists in `xs` with the separator `sep`, appending them. The resulting list is
flattened.

`O(|xs|)`.

`[List.intersperse]](#manual-List___intersperse)` is a similar function that alternates a separator element with the elements of a
list.

Examples:

- `[List.intercalate]](#manual-List___intercalate) sep [] = []`
- `[List.intercalate]](#manual-List___intercalate) sep [a] = a`
- `[List.intercalate]](#manual-List___intercalate) sep [a, b] = a ++ sep ++ b`
- `[List.intercalate]](#manual-List___intercalate) sep [a, b, c] = a ++ sep ++ b ++ sep ++ c`

def

```lean
[List.intercalateTR.{u_1}]](#manual-List___intercalateTR) {α : Type u_1} (sep : [List]](#manual-List___nil) α)
  (xs : [List]](#manual-List___nil) ([List]](#manual-List___nil) α)) : [List]](#manual-List___nil) α



[List.intercalateTR.{u_1}]](#manual-List___intercalateTR) {α : Type u_1}
  (sep : [List]](#manual-List___nil) α) (xs : [List]](#manual-List___nil) ([List]](#manual-List___nil) α)) :
  [List]](#manual-List___nil) α
```

Alternates the lists in `xs` with the separator `sep`.

This is a tail-recursive version of `[List.intercalate]](#manual-List___intercalate)` used at runtime.

Examples:

- `[List.intercalateTR]](#manual-List___intercalateTR) sep [] = []`
- `[List.intercalateTR]](#manual-List___intercalateTR) sep [a] = a`
- `[List.intercalateTR]](#manual-List___intercalateTR) sep [a, b] = a ++ sep ++ b`
- `[List.intercalateTR]](#manual-List___intercalateTR) sep [a, b, c] = a ++ sep ++ b ++ sep ++ c`

#### 20.15.3.9. Sorting {#manual-The-Lean-Language-Reference--Basic-Types--Linked-Lists--API-Reference--Sorting}

def

```lean
[List.mergeSort.{u_1}]](#manual-List___mergeSort) {α : Type u_1} (xs : [List]](#manual-List___nil) α)
  (le : α → α → [Bool]](#manual-Bool___false) := by exact fun a b => a ≤ b) : [List]](#manual-List___nil) α



[List.mergeSort.{u_1}]](#manual-List___mergeSort) {α : Type u_1}
  (xs : [List]](#manual-List___nil) α)
  (le : α → α → [Bool]](#manual-Bool___false) := by
    exact fun a b => a ≤ b) :
  [List]](#manual-List___nil) α
```

A stable merge sort.

This function is a simplified implementation that's designed to be easy to reason about, rather than
for efficiency. In particular, it uses the non-tail-recursive `[List.merge]](#manual-List___merge)` function and traverses
lists unnecessarily.

It is replaced at runtime by an efficient implementation that has been proven to be equivalent.

def

```lean
[List.merge.{u_1}]](#manual-List___merge) {α : Type u_1} (xs ys : [List]](#manual-List___nil) α)
  (le : α → α → [Bool]](#manual-Bool___false) := by exact fun a b => a ≤ b) : [List]](#manual-List___nil) α



[List.merge.{u_1}]](#manual-List___merge) {α : Type u_1}
  (xs ys : [List]](#manual-List___nil) α)
  (le : α → α → [Bool]](#manual-Bool___false) := by
    exact fun a b => a ≤ b) :
  [List]](#manual-List___nil) α
```

Merges two lists, using `le` to select the first element of the resulting list if both are
non-empty.

If both input lists are sorted according to `le`, then the resulting list is also sorted according
to `le`. `O(|xs| + |ys|)`.

This implementation is not tail-recursive, but it is replaced at runtime by a proven-equivalent
tail-recursive merge.

#### 20.15.3.10. Iteration {#manual-The-Lean-Language-Reference--Basic-Types--Linked-Lists--API-Reference--Iteration}

def

```lean
[List.iter.{w}]](#manual-List___iter) {α : Type w} (l : [List]](#manual-List___nil) α) : [Std.Iter](https://lean-lang.org/doc/reference/latest/Iterators/Iterator-Definitions/#Std___Iter___mk) α



[List.iter.{w}]](#manual-List___iter) {α : Type w} (l : [List]](#manual-List___nil) α) :
  [Std.Iter](https://lean-lang.org/doc/reference/latest/Iterators/Iterator-Definitions/#Std___Iter___mk) α
```

Returns a finite iterator for the given list.
The iterator yields the elements of the list in order and then terminates.

The monadic version of this iterator is `[List.iterM]](#manual-List___iterM)`.

**Termination properties:**

- `Finite` instance: always
- `Productive` instance: always

def

```lean
[List.iterM.{w, w'}]](#manual-List___iterM) {α : Type w} (l : [List]](#manual-List___nil) α) (m : Type w → Type w')
  [[Pure]](#manual-Pure___mk) m] : [Std.IterM](https://lean-lang.org/doc/reference/latest/Iterators/Iterator-Definitions/#Std___IterM___mk) m α



[List.iterM.{w, w'}]](#manual-List___iterM) {α : Type w}
  (l : [List]](#manual-List___nil) α) (m : Type w → Type w')
  [[Pure]](#manual-Pure___mk) m] : [Std.IterM](https://lean-lang.org/doc/reference/latest/Iterators/Iterator-Definitions/#Std___IterM___mk) m α
```

Returns a finite iterator for the given list.
The iterator yields the elements of the list in order and then terminates.

The non-monadic version of this iterator is `[List.iter]](#manual-List___iter)`.

**Termination properties:**

- `Finite` instance: always
- `Productive` instance: always

def

```lean
[List.forA.{u, v, w}]](#manual-List___forA) {m : Type u → Type v} [[Applicative]](#manual-Applicative___mk) m] {α : Type w}
  (as : [List]](#manual-List___nil) α) (f : α → m [PUnit]](#manual-PUnit___unit)) : m [PUnit]](#manual-PUnit___unit)



[List.forA.{u, v, w}]](#manual-List___forA) {m : Type u → Type v}
  [[Applicative]](#manual-Applicative___mk) m] {α : Type w}
  (as : [List]](#manual-List___nil) α) (f : α → m [PUnit]](#manual-PUnit___unit)) :
  m [PUnit]](#manual-PUnit___unit)
```

Applies the applicative action `f` to every element in the list, in order.

If `m` is also a `[Monad]](#manual-Monad___mk)`, then using `[List.forM]](#manual-List___forM)` can be more efficient.

`[List.mapA]](#manual-List___mapA)` is a variant that collects results.

def

```lean
[List.forM.{u, v, w}]](#manual-List___forM) {m : Type u → Type v} [[Monad]](#manual-Monad___mk) m] {α : Type w}
  (as : [List]](#manual-List___nil) α) (f : α → m [PUnit]](#manual-PUnit___unit)) : m [PUnit]](#manual-PUnit___unit)



[List.forM.{u, v, w}]](#manual-List___forM) {m : Type u → Type v}
  [[Monad]](#manual-Monad___mk) m] {α : Type w} (as : [List]](#manual-List___nil) α)
  (f : α → m [PUnit]](#manual-PUnit___unit)) : m [PUnit]](#manual-PUnit___unit)
```

Applies the monadic action `f` to every element in the list, in order.

`[List.mapM]](#manual-List___mapM)` is a variant that collects results. `[List.forA]](#manual-List___forA)` is a variant that works on any
`[Applicative]](#manual-Applicative___mk)`.

def

```lean
[List.firstM.{u, v, w}]](#manual-List___firstM) {m : Type u → Type v} [[Alternative]](#manual-Alternative___mk) m] {α : Type w}
  {β : Type u} (f : α → m β) : [List]](#manual-List___nil) α → m β



[List.firstM.{u, v, w}]](#manual-List___firstM)
  {m : Type u → Type v} [[Alternative]](#manual-Alternative___mk) m]
  {α : Type w} {β : Type u}
  (f : α → m β) : [List]](#manual-List___nil) α → m β
```

Maps `f` over the list and collects the results with `<|>`. The result for the end of the list is
`failure`.

Examples:

- `[[], [1, 2], [], [2]].[firstM]](#manual-List___firstM) [List.head?]](#manual-List___head___) = [some]](#manual-Option___none) 1`
- `[[], [], []].[firstM]](#manual-List___firstM) [List.head?]](#manual-List___head___) = [none]](#manual-Option___none)`
- `[].[firstM]](#manual-List___firstM) [List.head?]](#manual-List___head___) = [none]](#manual-Option___none)`

def

```lean
[List.sum.{u_1}]](#manual-List___sum) {α : Type u_1} [[Add]](#manual-Add___mk) α] [[Zero]](#manual-Zero___mk) α] : [List]](#manual-List___nil) α → α



[List.sum.{u_1}]](#manual-List___sum) {α : Type u_1} [[Add]](#manual-Add___mk) α]
  [[Zero]](#manual-Zero___mk) α] : [List]](#manual-List___nil) α → α
```

Computes the sum of the elements of a list.

Examples:

- `[a, b, c].[sum]](#manual-List___sum) = a + (b + (c + 0))`
- `[1, 2, 5].[sum]](#manual-List___sum) = 8`

##### 20.15.3.10.1. Folds {#manual-The-Lean-Language-Reference--Basic-Types--Linked-Lists--API-Reference--Iteration--Folds}

Folds are operators that combine the elements of a list using a function.
They come in two varieties, named after the nesting of the function calls:

Left folds
:   Left folds combine the elements from the head of the list towards the end.
    The head of the list is combined with the initial value, and the result of this operation is then combined with the next value, and so forth.

Right folds
:   Right folds combine the elements from the end of the list towards the start, as if each `[cons]](#manual-List___nil)` constructor were replaced by a call to the combining function and `[nil]](#manual-List___nil)` were replaced by the initial value.

Monadic folds, indicated with an `-M` suffix, allow the combining function to use effects in a [monad]](#manual---tech-term-Monad), which may include stopping the fold early.

def

```lean
[List.foldl.{u, v}]](#manual-List___foldl) {α : Type u} {β : Type v} (f : α → β → α) (init : α) :
  [List]](#manual-List___nil) β → α



[List.foldl.{u, v}]](#manual-List___foldl) {α : Type u}
  {β : Type v} (f : α → β → α)
  (init : α) : [List]](#manual-List___nil) β → α
```

Folds a function over a list from the left, accumulating a value starting with `init`. The
accumulated value is combined with the each element of the list in order, using `f`.

Examples:

- `[a, b, c].[foldl]](#manual-List___foldl) f z = f (f (f z a) b) c`
- `[1, 2, 3].[foldl]](#manual-List___foldl) (· ++ toString ·) "" = "123"`
- `[1, 2, 3].[foldl]](#manual-List___foldl) (s!"({·} {·})") "" = "((( 1) 2) 3)"`

def

```lean
[List.foldlM.{u, v, w}]](#manual-List___foldlM) {m : Type u → Type v} [[Monad]](#manual-Monad___mk) m] {s : Type u}
  {α : Type w} (f : s → α → m s) (init : s) : [List]](#manual-List___nil) α → m s



[List.foldlM.{u, v, w}]](#manual-List___foldlM)
  {m : Type u → Type v} [[Monad]](#manual-Monad___mk) m]
  {s : Type u} {α : Type w}
  (f : s → α → m s) (init : s) :
  [List]](#manual-List___nil) α → m s
```

Folds a monadic function over a list from the left, accumulating a value starting with `init`. The
accumulated value is combined with the each element of the list in order, using `f`.

Example:

```
example [Monad m] (f : α → β → m α) :
    List.foldlM (m := m) f x₀ [a, b, c] = (do
      let x₁ ← f x₀ a
      let x₂ ← f x₁ b
      let x₃ ← f x₂ c
      pure x₃)
  := by rfl
```

def

```lean
[List.foldlRecOn.{u_1, u_2, u_3}]](#manual-List___foldlRecOn) {β : Type u_1} {α : Type u_2}
  {motive : β → Sort u_3} (l : [List]](#manual-List___nil) α) (op : β → α → β) {b : β} :
  motive b →
    ((b : β) → motive b → (a : α) → a ∈ l → motive (op b a)) →
      motive ([List.foldl]](#manual-List___foldl) op b l)



[List.foldlRecOn.{u_1, u_2, u_3}]](#manual-List___foldlRecOn)
  {β : Type u_1} {α : Type u_2}
  {motive : β → Sort u_3} (l : [List]](#manual-List___nil) α)
  (op : β → α → β) {b : β} :
  motive b →
    ((b : β) →
        motive b →
          (a : α) →
            a ∈ l → motive (op b a)) →
      motive ([List.foldl]](#manual-List___foldl) op b l)
```

A reasoning principle for proving propositions about the result of `[List.foldl]](#manual-List___foldl)` by establishing an
invariant that is true for the initial data and preserved by the operation being folded.

Because the motive can return a type in any sort, this function may be used to construct data as
well as to prove propositions.

Example:

```lean
example {xs : [List]](#manual-List___nil) [Nat]](#manual-Nat___zero)} : xs.[foldl]](#manual-List___foldl) (· + ·) 1 > 0 := byxs:[List]](#manual-List___nil) [Nat]](#manual-Nat___zero)⊢ [List.foldl]](#manual-List___foldl) (fun x1 x2 => x1 [+]](#manual-HAdd___mk) x2) 1 xs > 0
[apply]](#manual-apply) [List.foldlRecOn]](#manual-List___foldlRecOn)xxs:[List]](#manual-List___nil) [Nat]](#manual-Nat___zero)⊢ 0 [<]](#manual-LT___mk) 1xxs:[List]](#manual-List___nil) [Nat]](#manual-Nat___zero)⊢ ∀ (b : [Nat]](#manual-Nat___zero)), 0 [<]](#manual-LT___mk) b → ∀ (a : [Nat]](#manual-Nat___zero)), a ∈ xs → 0 [<]](#manual-LT___mk) b [+]](#manual-HAdd___mk) a
.xxs:[List]](#manual-List___nil) [Nat]](#manual-Nat___zero)⊢ 0 [<]](#manual-LT___mk) 1 [show]](#manual-show) 0 < 1xxs:[List]](#manual-List___nil) [Nat]](#manual-Nat___zero)⊢ 0 [<]](#manual-LT___mk) 1; [trivial]](#manual-trivial)All goals completed! 🐙
.xxs:[List]](#manual-List___nil) [Nat]](#manual-Nat___zero)⊢ ∀ (b : [Nat]](#manual-Nat___zero)), 0 [<]](#manual-LT___mk) b → ∀ (a : [Nat]](#manual-Nat___zero)), a ∈ xs → 0 [<]](#manual-LT___mk) b [+]](#manual-HAdd___mk) a [show]](#manual-show) ∀ (b : [Nat]](#manual-Nat___zero)), 0 < b → ∀ (a : [Nat]](#manual-Nat___zero)), a ∈ xs → 0 < b + axxs:[List]](#manual-List___nil) [Nat]](#manual-Nat___zero)⊢ ∀ (b : [Nat]](#manual-Nat___zero)), 0 [<]](#manual-LT___mk) b → ∀ (a : [Nat]](#manual-Nat___zero)), a ∈ xs → 0 [<]](#manual-LT___mk) b [+]](#manual-HAdd___mk) a
[intros]](#manual-intros)xxs:[List]](#manual-List___nil) [Nat]](#manual-Nat___zero)b✝:[Nat]](#manual-Nat___zero)a✝²:0 [<]](#manual-LT___mk) b✝a✝¹:[Nat]](#manual-Nat___zero)a✝:a✝¹ ∈ xs⊢ 0 [<]](#manual-LT___mk) b✝ [+]](#manual-HAdd___mk) a✝¹; [omega]](#manual-omega)All goals completed! 🐙
```

def

```lean
[List.foldr.{u, v}]](#manual-List___foldr) {α : Type u} {β : Type v} (f : α → β → β) (init : β)
  (l : [List]](#manual-List___nil) α) : β



[List.foldr.{u, v}]](#manual-List___foldr) {α : Type u}
  {β : Type v} (f : α → β → β) (init : β)
  (l : [List]](#manual-List___nil) α) : β
```

Folds a function over a list from the right, accumulating a value starting with `init`. The
accumulated value is combined with each element of the list in reverse order, using `f`.

`O(|l|)`. Replaced at runtime with `[List.foldrTR]](#manual-List___foldrTR)`.

Examples:

- `[a, b, c].[foldr]](#manual-List___foldr) f init = f a (f b (f c init))`
- `[1, 2, 3].[foldr]](#manual-List___foldr) (toString · ++ ·) "" = "123"`
- `[1, 2, 3].[foldr]](#manual-List___foldr) (s!"({·} {·})") "!" = "(1 (2 (3 !)))"`

def

```lean
[List.foldrM.{u, v, w}]](#manual-List___foldrM) {m : Type u → Type v} [[Monad]](#manual-Monad___mk) m] {s : Type u}
  {α : Type w} (f : α → s → m s) (init : s) (l : [List]](#manual-List___nil) α) : m s



[List.foldrM.{u, v, w}]](#manual-List___foldrM)
  {m : Type u → Type v} [[Monad]](#manual-Monad___mk) m]
  {s : Type u} {α : Type w}
  (f : α → s → m s) (init : s)
  (l : [List]](#manual-List___nil) α) : m s
```

Folds a monadic function over a list from the right, accumulating a value starting with `init`. The
accumulated value is combined with the each element of the list in reverse order, using `f`.

Example:

```
example [Monad m] (f : α → β → m β) :
  List.foldrM (m := m) f x₀ [a, b, c] = (do
    let x₁ ← f c x₀
    let x₂ ← f b x₁
    let x₃ ← f a x₂
    pure x₃)
  := by rfl
```

def

```lean
[List.foldrRecOn.{u_1, u_2, u_3}]](#manual-List___foldrRecOn) {β : Type u_1} {α : Type u_2}
  {motive : β → Sort u_3} (l : [List]](#manual-List___nil) α) (op : α → β → β) {b : β} :
  motive b →
    ((b : β) → motive b → (a : α) → a ∈ l → motive (op a b)) →
      motive ([List.foldr]](#manual-List___foldr) op b l)



[List.foldrRecOn.{u_1, u_2, u_3}]](#manual-List___foldrRecOn)
  {β : Type u_1} {α : Type u_2}
  {motive : β → Sort u_3} (l : [List]](#manual-List___nil) α)
  (op : α → β → β) {b : β} :
  motive b →
    ((b : β) →
        motive b →
          (a : α) →
            a ∈ l → motive (op a b)) →
      motive ([List.foldr]](#manual-List___foldr) op b l)
```

A reasoning principle for proving propositions about the result of `[List.foldr]](#manual-List___foldr)` by establishing an
invariant that is true for the initial data and preserved by the operation being folded.

Because the motive can return a type in any sort, this function may be used to construct data as
well as to prove propositions.

Example:

```lean
example {xs : [List]](#manual-List___nil) [Nat]](#manual-Nat___zero)} : xs.[foldr]](#manual-List___foldr) (· + ·) 1 > 0 := byxs:[List]](#manual-List___nil) [Nat]](#manual-Nat___zero)⊢ [List.foldr]](#manual-List___foldr) (fun x1 x2 => x1 [+]](#manual-HAdd___mk) x2) 1 xs > 0
[apply]](#manual-apply) [List.foldrRecOn]](#manual-List___foldrRecOn)xxs:[List]](#manual-List___nil) [Nat]](#manual-Nat___zero)⊢ 0 [<]](#manual-LT___mk) 1xxs:[List]](#manual-List___nil) [Nat]](#manual-Nat___zero)⊢ ∀ (b : [Nat]](#manual-Nat___zero)), 0 [<]](#manual-LT___mk) b → ∀ (a : [Nat]](#manual-Nat___zero)), a ∈ xs → 0 [<]](#manual-LT___mk) a [+]](#manual-HAdd___mk) b
.xxs:[List]](#manual-List___nil) [Nat]](#manual-Nat___zero)⊢ 0 [<]](#manual-LT___mk) 1 [show]](#manual-show) 0 < 1xxs:[List]](#manual-List___nil) [Nat]](#manual-Nat___zero)⊢ 0 [<]](#manual-LT___mk) 1; [trivial]](#manual-trivial)All goals completed! 🐙
.xxs:[List]](#manual-List___nil) [Nat]](#manual-Nat___zero)⊢ ∀ (b : [Nat]](#manual-Nat___zero)), 0 [<]](#manual-LT___mk) b → ∀ (a : [Nat]](#manual-Nat___zero)), a ∈ xs → 0 [<]](#manual-LT___mk) a [+]](#manual-HAdd___mk) b [show]](#manual-show) ∀ (b : [Nat]](#manual-Nat___zero)), 0 < b → ∀ (a : [Nat]](#manual-Nat___zero)), a ∈ xs → 0 < a + bxxs:[List]](#manual-List___nil) [Nat]](#manual-Nat___zero)⊢ ∀ (b : [Nat]](#manual-Nat___zero)), 0 [<]](#manual-LT___mk) b → ∀ (a : [Nat]](#manual-Nat___zero)), a ∈ xs → 0 [<]](#manual-LT___mk) a [+]](#manual-HAdd___mk) b
[intros]](#manual-intros)xxs:[List]](#manual-List___nil) [Nat]](#manual-Nat___zero)b✝:[Nat]](#manual-Nat___zero)a✝²:0 [<]](#manual-LT___mk) b✝a✝¹:[Nat]](#manual-Nat___zero)a✝:a✝¹ ∈ xs⊢ 0 [<]](#manual-LT___mk) a✝¹ [+]](#manual-HAdd___mk) b✝; [omega]](#manual-omega)All goals completed! 🐙
```

def

```lean
[List.foldrTR.{u_1, u_2}]](#manual-List___foldrTR) {α : Type u_1} {β : Type u_2} (f : α → β → β)
  (init : β) (l : [List]](#manual-List___nil) α) : β



[List.foldrTR.{u_1, u_2}]](#manual-List___foldrTR) {α : Type u_1}
  {β : Type u_2} (f : α → β → β)
  (init : β) (l : [List]](#manual-List___nil) α) : β
```

Folds a function over a list from the right, accumulating a value starting with `init`. The
accumulated value is combined with the each element of the list in reverse order, using `f`.

`O(|l|)`. This is the tail-recursive replacement for `[List.foldr]](#manual-List___foldr)` in runtime code.

Examples:

- `[a, b, c].[foldrTR]](#manual-List___foldrTR) f init = f a (f b (f c init))`
- `[1, 2, 3].[foldrTR]](#manual-List___foldrTR) (toString · ++ ·) "" = "123"`
- `[1, 2, 3].[foldrTR]](#manual-List___foldrTR) (s!"({·} {·})") "!" = "(1 (2 (3 !)))"`

#### 20.15.3.11. Transformation {#manual-The-Lean-Language-Reference--Basic-Types--Linked-Lists--API-Reference--Transformation}

def

```lean
[List.map.{u_1, u_2}]](#manual-List___map) {α : Type u_1} {β : Type u_2} (f : α → β)
  (l : [List]](#manual-List___nil) α) : [List]](#manual-List___nil) β



[List.map.{u_1, u_2}]](#manual-List___map) {α : Type u_1}
  {β : Type u_2} (f : α → β)
  (l : [List]](#manual-List___nil) α) : [List]](#manual-List___nil) β
```

Applies a function to each element of the list, returning the resulting list of values.

`O(|l|)`.

Examples:

- `[a, b, c].[map]](#manual-List___map) f = [f a, f b, f c]`
- `[].[map]](#manual-List___map) [Nat.succ]](#manual-Nat___zero) = []`
- `["one", "two", "three"].[map]](#manual-List___map) (·.[length]](#manual-String___length)) = [3, 3, 5]`
- `["one", "two", "three"].[map]](#manual-List___map) (·.reverse) = ["eno", "owt", "eerht"]`

def

```lean
[List.mapTR.{u, v}]](#manual-List___mapTR) {α : Type u} {β : Type v} (f : α → β) (as : [List]](#manual-List___nil) α) :
  [List]](#manual-List___nil) β



[List.mapTR.{u, v}]](#manual-List___mapTR) {α : Type u}
  {β : Type v} (f : α → β) (as : [List]](#manual-List___nil) α) :
  [List]](#manual-List___nil) β
```

Applies a function to each element of the list, returning the resulting list of values.

`O(|l|)`. This is the tail-recursive variant of `[List.map]](#manual-List___map)`, used in runtime code.

Examples:

- `[a, b, c].[mapTR]](#manual-List___mapTR) f = [f a, f b, f c]`
- `[].[mapTR]](#manual-List___mapTR) [Nat.succ]](#manual-Nat___zero) = []`
- `["one", "two", "three"].[mapTR]](#manual-List___mapTR) (·.[length]](#manual-String___length)) = [3, 3, 5]`
- `["one", "two", "three"].[mapTR]](#manual-List___mapTR) (·.reverse) = ["eno", "owt", "eerht"]`

def

```lean
[List.mapM.{u, v, w}]](#manual-List___mapM) {m : Type u → Type v} [[Monad]](#manual-Monad___mk) m] {α : Type w}
  {β : Type u} (f : α → m β) (as : [List]](#manual-List___nil) α) : m ([List]](#manual-List___nil) β)



[List.mapM.{u, v, w}]](#manual-List___mapM) {m : Type u → Type v}
  [[Monad]](#manual-Monad___mk) m] {α : Type w} {β : Type u}
  (f : α → m β) (as : [List]](#manual-List___nil) α) : m ([List]](#manual-List___nil) β)
```

Applies the monadic action `f` to every element in the list, left-to-right, and returns the list of
results.

This implementation is tail recursive. `[List.mapM']](#manual-List___mapM___)` is a non-tail-recursive variant that may be
more convenient to reason about. `[List.forM]](#manual-List___forM)` is the variant that discards the results and
`[List.mapA]](#manual-List___mapA)` is the variant that works with `[Applicative]](#manual-Applicative___mk)`.

def

```lean
[List.mapM'.{u_1, u_2, u_3}]](#manual-List___mapM___) {m : Type u_1 → Type u_2} {α : Type u_3}
  {β : Type u_1} [[Monad]](#manual-Monad___mk) m] (f : α → m β) : [List]](#manual-List___nil) α → m ([List]](#manual-List___nil) β)



[List.mapM'.{u_1, u_2, u_3}]](#manual-List___mapM___)
  {m : Type u_1 → Type u_2} {α : Type u_3}
  {β : Type u_1} [[Monad]](#manual-Monad___mk) m] (f : α → m β) :
  [List]](#manual-List___nil) α → m ([List]](#manual-List___nil) β)
```

Applies the monadic action `f` on every element in the list, left-to-right, and returns the list of
results.

This is a non-tail-recursive variant of `[List.mapM]](#manual-List___mapM)` that's easier to reason about. It cannot be used
as the main definition and replaced by the tail-recursive version because they can only be proved
equal when `m` is a `[LawfulMonad]](#manual-LawfulMonad___mk)`.

def

```lean
[List.mapA.{u, v, w}]](#manual-List___mapA) {m : Type u → Type v} [[Applicative]](#manual-Applicative___mk) m] {α : Type w}
  {β : Type u} (f : α → m β) : [List]](#manual-List___nil) α → m ([List]](#manual-List___nil) β)



[List.mapA.{u, v, w}]](#manual-List___mapA) {m : Type u → Type v}
  [[Applicative]](#manual-Applicative___mk) m] {α : Type w}
  {β : Type u} (f : α → m β) :
  [List]](#manual-List___nil) α → m ([List]](#manual-List___nil) β)
```

Applies the applicative action `f` on every element in the list, left-to-right, and returns the list
of results.

If `m` is also a `[Monad]](#manual-Monad___mk)`, then using `mapM` can be more efficient.

See `[List.forA]](#manual-List___forA)` for the variant that discards the results. See `[List.mapM]](#manual-List___mapM)` for the variant that
works with `[Monad]](#manual-Monad___mk)`.

This function is not tail-recursive, so it may fail with a stack overflow on long lists.

def

```lean
[List.mapFinIdx.{u_1, u_2}]](#manual-List___mapFinIdx) {α : Type u_1} {β : Type u_2} (as : [List]](#manual-List___nil) α)
  (f : (i : [Nat]](#manual-Nat___zero)) → α → i [<]](#manual-LT___mk) as.[length]](#manual-List___length) → β) : [List]](#manual-List___nil) β



[List.mapFinIdx.{u_1, u_2}]](#manual-List___mapFinIdx) {α : Type u_1}
  {β : Type u_2} (as : [List]](#manual-List___nil) α)
  (f :
    (i : [Nat]](#manual-Nat___zero)) → α → i [<]](#manual-LT___mk) as.[length]](#manual-List___length) → β) :
  [List]](#manual-List___nil) β
```

Applies a function to each element of the list along with the index at which that element is found,
returning the list of results. In addition to the index, the function is also provided with a proof
that the index is valid.

`[List.mapIdx]](#manual-List___mapIdx)` is a variant that does not provide the function with evidence that the index is valid.

def

```lean
[List.mapFinIdxM.{u_1, u_2, u_3}]](#manual-List___mapFinIdxM) {m : Type u_1 → Type u_2} {α : Type u_3}
  {β : Type u_1} [[Monad]](#manual-Monad___mk) m] (as : [List]](#manual-List___nil) α)
  (f : (i : [Nat]](#manual-Nat___zero)) → α → i [<]](#manual-LT___mk) as.[length]](#manual-List___length) → m β) : m ([List]](#manual-List___nil) β)



[List.mapFinIdxM.{u_1, u_2, u_3}]](#manual-List___mapFinIdxM)
  {m : Type u_1 → Type u_2} {α : Type u_3}
  {β : Type u_1} [[Monad]](#manual-Monad___mk) m] (as : [List]](#manual-List___nil) α)
  (f :
    (i : [Nat]](#manual-Nat___zero)) → α → i [<]](#manual-LT___mk) as.[length]](#manual-List___length) → m β) :
  m ([List]](#manual-List___nil) β)
```

Applies a monadic function to each element of the list along with the index at which that element is
found, returning the list of results. In addition to the index, the function is also provided with a
proof that the index is valid.

`[List.mapIdxM]](#manual-List___mapIdxM)` is a variant that does not provide the function with evidence that the index is
valid.

def

```lean
[List.mapIdx.{u_1, u_2}]](#manual-List___mapIdx) {α : Type u_1} {β : Type u_2} (f : [Nat]](#manual-Nat___zero) → α → β)
  (as : [List]](#manual-List___nil) α) : [List]](#manual-List___nil) β



[List.mapIdx.{u_1, u_2}]](#manual-List___mapIdx) {α : Type u_1}
  {β : Type u_2} (f : [Nat]](#manual-Nat___zero) → α → β)
  (as : [List]](#manual-List___nil) α) : [List]](#manual-List___nil) β
```

Applies a function to each element of the list along with the index at which that element is found,
returning the list of results.

`[List.mapFinIdx]](#manual-List___mapFinIdx)` is a variant that additionally provides the function with a proof that the index
is valid.

def

```lean
[List.mapIdxM.{u_1, u_2, u_3}]](#manual-List___mapIdxM) {m : Type u_1 → Type u_2} {α : Type u_3}
  {β : Type u_1} [[Monad]](#manual-Monad___mk) m] (f : [Nat]](#manual-Nat___zero) → α → m β) (as : [List]](#manual-List___nil) α) :
  m ([List]](#manual-List___nil) β)



[List.mapIdxM.{u_1, u_2, u_3}]](#manual-List___mapIdxM)
  {m : Type u_1 → Type u_2} {α : Type u_3}
  {β : Type u_1} [[Monad]](#manual-Monad___mk) m]
  (f : [Nat]](#manual-Nat___zero) → α → m β) (as : [List]](#manual-List___nil) α) :
  m ([List]](#manual-List___nil) β)
```

Applies a monadic function to each element of the list along with the index at which that element is
found, returning the list of results.

`[List.mapFinIdxM]](#manual-List___mapFinIdxM)` is a variant that additionally provides the function with a proof that the index
is valid.

def

```lean
[List.mapMono.{u_1}]](#manual-List___mapMono) {α : Type u_1} (as : [List]](#manual-List___nil) α) (f : α → α) : [List]](#manual-List___nil) α



[List.mapMono.{u_1}]](#manual-List___mapMono) {α : Type u_1}
  (as : [List]](#manual-List___nil) α) (f : α → α) : [List]](#manual-List___nil) α
```

Applies a function to each element of a list, returning the list of results. The function is
monomorphic: it is required to return a value of the same type. The internal implementation uses
pointer equality, and does not allocate a new list if the result of each function call is
pointer-equal to its argument.

For verification purposes, `[List.mapMono]](#manual-List___mapMono) = [List.map]](#manual-List___map)`.

def

```lean
[List.mapMonoM.{u_1, u_2}]](#manual-List___mapMonoM) {m : Type u_1 → Type u_2} {α : Type u_1}
  [[Monad]](#manual-Monad___mk) m] (as : [List]](#manual-List___nil) α) (f : α → m α) : m ([List]](#manual-List___nil) α)



[List.mapMonoM.{u_1, u_2}]](#manual-List___mapMonoM)
  {m : Type u_1 → Type u_2} {α : Type u_1}
  [[Monad]](#manual-Monad___mk) m] (as : [List]](#manual-List___nil) α) (f : α → m α) :
  m ([List]](#manual-List___nil) α)
```

Applies a monadic function to each element of a list, returning the list of results. The function is
monomorphic: it is required to return a value of the same type. The internal implementation uses
pointer equality, and does not allocate a new list if the result of each function call is
pointer-equal to its argument.

def

```lean
[List.flatMap.{u, v}]](#manual-List___flatMap) {α : Type u} {β : Type v} (b : α → [List]](#manual-List___nil) β)
  (as : [List]](#manual-List___nil) α) : [List]](#manual-List___nil) β



[List.flatMap.{u, v}]](#manual-List___flatMap) {α : Type u}
  {β : Type v} (b : α → [List]](#manual-List___nil) β)
  (as : [List]](#manual-List___nil) α) : [List]](#manual-List___nil) β
```

Applies a function that returns a list to each element of a list, and concatenates the resulting
lists.

Examples:

- `[2, 3, 2].[flatMap]](#manual-List___flatMap) [List.range]](#manual-List___range) = [0, 1, 0, 1, 2, 0, 1]`
- `["red", "blue"].[flatMap]](#manual-List___flatMap) String.toList = ['r', 'e', 'd', 'b', 'l', 'u', 'e']`

def

```lean
[List.flatMapTR.{u_1, u_2}]](#manual-List___flatMapTR) {α : Type u_1} {β : Type u_2} (f : α → [List]](#manual-List___nil) β)
  (as : [List]](#manual-List___nil) α) : [List]](#manual-List___nil) β



[List.flatMapTR.{u_1, u_2}]](#manual-List___flatMapTR) {α : Type u_1}
  {β : Type u_2} (f : α → [List]](#manual-List___nil) β)
  (as : [List]](#manual-List___nil) α) : [List]](#manual-List___nil) β
```

Applies a function that returns a list to each element of a list, and concatenates the resulting
lists.

This is the tail-recursive version of `[List.flatMap]](#manual-List___flatMap)` that's used at runtime.

Examples:

- `[2, 3, 2].[flatMapTR]](#manual-List___flatMapTR) [List.range]](#manual-List___range) = [0, 1, 0, 1, 2, 0, 1]`
- `["red", "blue"].[flatMapTR]](#manual-List___flatMapTR) String.toList = ['r', 'e', 'd', 'b', 'l', 'u', 'e']`

def

```lean
[List.flatMapM.{u, v, w}]](#manual-List___flatMapM) {m : Type u → Type v} [[Monad]](#manual-Monad___mk) m] {α : Type w}
  {β : Type u} (f : α → m ([List]](#manual-List___nil) β)) (as : [List]](#manual-List___nil) α) : m ([List]](#manual-List___nil) β)



[List.flatMapM.{u, v, w}]](#manual-List___flatMapM)
  {m : Type u → Type v} [[Monad]](#manual-Monad___mk) m]
  {α : Type w} {β : Type u}
  (f : α → m ([List]](#manual-List___nil) β)) (as : [List]](#manual-List___nil) α) :
  m ([List]](#manual-List___nil) β)
```

Applies a monadic function that returns a list to each element of a list, from left to right, and
concatenates the resulting lists.

def

```lean
[List.zip.{u, v}]](#manual-List___zip) {α : Type u} {β : Type v} :
  [List]](#manual-List___nil) α → [List]](#manual-List___nil) β → [List]](#manual-List___nil) [(]](#manual-Prod___mk)α [×]](#manual-Prod___mk) β[)]](#manual-Prod___mk)



[List.zip.{u, v}]](#manual-List___zip) {α : Type u}
  {β : Type v} :
  [List]](#manual-List___nil) α → [List]](#manual-List___nil) β → [List]](#manual-List___nil) [(]](#manual-Prod___mk)α [×]](#manual-Prod___mk) β[)]](#manual-Prod___mk)
```

Combines two lists into a list of pairs in which the first and second components are the
corresponding elements of each list. The resulting list is the length of the shorter of the input
lists.

`O(min |xs| |ys|)`.

Examples:

- `["Mon", "Tue", "Wed"].[zip]](#manual-List___zip) [1, 2, 3] = [("Mon", 1), ("Tue", 2), ("Wed", 3)]`
- `["Mon", "Tue", "Wed"].[zip]](#manual-List___zip) [1, 2] = [("Mon", 1), ("Tue", 2)]`
- `[x₁, x₂, x₃].[zip]](#manual-List___zip) [y₁, y₂, y₃, y₄] = [(x₁, y₁), (x₂, y₂), (x₃, y₃)]`

def

```lean
[List.zipIdx.{u}]](#manual-List___zipIdx) {α : Type u} (l : [List]](#manual-List___nil) α) (n : [Nat]](#manual-Nat___zero) := 0) :
  [List]](#manual-List___nil) [(]](#manual-Prod___mk)α [×]](#manual-Prod___mk) [Nat]](#manual-Nat___zero)[)]](#manual-Prod___mk)



[List.zipIdx.{u}]](#manual-List___zipIdx) {α : Type u} (l : [List]](#manual-List___nil) α)
  (n : [Nat]](#manual-Nat___zero) := 0) : [List]](#manual-List___nil) [(]](#manual-Prod___mk)α [×]](#manual-Prod___mk) [Nat]](#manual-Nat___zero)[)]](#manual-Prod___mk)
```

Pairs each element of a list with its index, optionally starting from an index other than `0`.

`O(|l|)`.

Examples:

- `[a, b, c].[zipIdx]](#manual-List___zipIdx) = [(a, 0), (b, 1), (c, 2)]`
- `[a, b, c].[zipIdx]](#manual-List___zipIdx) 5 = [(a, 5), (b, 6), (c, 7)]`

def

```lean
[List.zipIdxTR.{u_1}]](#manual-List___zipIdxTR) {α : Type u_1} (l : [List]](#manual-List___nil) α) (n : [Nat]](#manual-Nat___zero) := 0) :
  [List]](#manual-List___nil) [(]](#manual-Prod___mk)α [×]](#manual-Prod___mk) [Nat]](#manual-Nat___zero)[)]](#manual-Prod___mk)



[List.zipIdxTR.{u_1}]](#manual-List___zipIdxTR) {α : Type u_1}
  (l : [List]](#manual-List___nil) α) (n : [Nat]](#manual-Nat___zero) := 0) :
  [List]](#manual-List___nil) [(]](#manual-Prod___mk)α [×]](#manual-Prod___mk) [Nat]](#manual-Nat___zero)[)]](#manual-Prod___mk)
```

Pairs each element of a list with its index, optionally starting from an index other than `0`.

`O(|l|)`. This is a tail-recursive version of `[List.zipIdx]](#manual-List___zipIdx)` that's used at runtime.

Examples:

- `[a, b, c].[zipIdxTR]](#manual-List___zipIdxTR) = [(a, 0), (b, 1), (c, 2)]`
- `[a, b, c].[zipIdxTR]](#manual-List___zipIdxTR) 5 = [(a, 5), (b, 6), (c, 7)]`

def

```lean
[List.zipWith.{u, v, w}]](#manual-List___zipWith) {α : Type u} {β : Type v} {γ : Type w}
  (f : α → β → γ) (xs : [List]](#manual-List___nil) α) (ys : [List]](#manual-List___nil) β) : [List]](#manual-List___nil) γ



[List.zipWith.{u, v, w}]](#manual-List___zipWith) {α : Type u}
  {β : Type v} {γ : Type w}
  (f : α → β → γ) (xs : [List]](#manual-List___nil) α)
  (ys : [List]](#manual-List___nil) β) : [List]](#manual-List___nil) γ
```

Applies a function to the corresponding elements of two lists, stopping at the end of the shorter
list.

`O(min |xs| |ys|)`.

Examples:

- `[1, 2].[zipWith]](#manual-List___zipWith) (· + ·) [5, 6] = [6, 8]`
- `[1, 2, 3].[zipWith]](#manual-List___zipWith) (· + ·) [5, 6, 10] = [6, 8, 13]`
- `[].[zipWith]](#manual-List___zipWith) (· + ·) [5, 6] = []`
- `[x₁, x₂, x₃].[zipWith]](#manual-List___zipWith) f [y₁, y₂, y₃, y₄] = [f x₁ y₁, f x₂ y₂, f x₃ y₃]`

def

```lean
[List.zipWithTR.{u_1, u_2, u_3}]](#manual-List___zipWithTR) {α : Type u_1} {β : Type u_2}
  {γ : Type u_3} (f : α → β → γ) (as : [List]](#manual-List___nil) α) (bs : [List]](#manual-List___nil) β) : [List]](#manual-List___nil) γ



[List.zipWithTR.{u_1, u_2, u_3}]](#manual-List___zipWithTR)
  {α : Type u_1} {β : Type u_2}
  {γ : Type u_3} (f : α → β → γ)
  (as : [List]](#manual-List___nil) α) (bs : [List]](#manual-List___nil) β) : [List]](#manual-List___nil) γ
```

Applies a function to the corresponding elements of two lists, stopping at the end of the shorter
list.

`O(min |xs| |ys|)`. This is a tail-recursive version of `[List.zipWith]](#manual-List___zipWith)` that's used at runtime.

Examples:

- `[1, 2].[zipWithTR]](#manual-List___zipWithTR) (· + ·) [5, 6] = [6, 8]`
- `[1, 2, 3].[zipWithTR]](#manual-List___zipWithTR) (· + ·) [5, 6, 10] = [6, 8, 13]`
- `[].[zipWithTR]](#manual-List___zipWithTR) (· + ·) [5, 6] = []`
- `[x₁, x₂, x₃].[zipWithTR]](#manual-List___zipWithTR) f [y₁, y₂, y₃, y₄] = [f x₁ y₁, f x₂ y₂, f x₃ y₃]`

def

```lean
[List.zipWithAll.{u, v, w}]](#manual-List___zipWithAll) {α : Type u} {β : Type v} {γ : Type w}
  (f : [Option]](#manual-Option___none) α → [Option]](#manual-Option___none) β → γ) : [List]](#manual-List___nil) α → [List]](#manual-List___nil) β → [List]](#manual-List___nil) γ



[List.zipWithAll.{u, v, w}]](#manual-List___zipWithAll) {α : Type u}
  {β : Type v} {γ : Type w}
  (f : [Option]](#manual-Option___none) α → [Option]](#manual-Option___none) β → γ) :
  [List]](#manual-List___nil) α → [List]](#manual-List___nil) β → [List]](#manual-List___nil) γ
```

Applies a function to the corresponding elements of both lists, stopping when there are no more
elements in either list. If one list is shorter than the other, the function is passed `[none]](#manual-Option___none)` for
the missing elements.

Examples:

- `[1, 6].[zipWithAll]](#manual-List___zipWithAll) [min]](#manual-Min___mk) [5, 2] = [[some]](#manual-Option___none) 1, [some]](#manual-Option___none) 2]`
- `[1, 2, 3].[zipWithAll]](#manual-List___zipWithAll) [Prod.mk]](#manual-Prod___mk) [5, 6] = [([some]](#manual-Option___none) 1, [some]](#manual-Option___none) 5), ([some]](#manual-Option___none) 2, [some]](#manual-Option___none) 6), ([some]](#manual-Option___none) 3, [none]](#manual-Option___none))]`
- `[x₁, x₂].[zipWithAll]](#manual-List___zipWithAll) f [y] = [f ([some]](#manual-Option___none) x₁) ([some]](#manual-Option___none) y), f ([some]](#manual-Option___none) x₂) [none]](#manual-Option___none)]`

def

```lean
[List.unzip.{u, v}]](#manual-List___unzip) {α : Type u} {β : Type v} (l : [List]](#manual-List___nil) [(]](#manual-Prod___mk)α [×]](#manual-Prod___mk) β[)]](#manual-Prod___mk)) :
  [List]](#manual-List___nil) α [×]](#manual-Prod___mk) [List]](#manual-List___nil) β



[List.unzip.{u, v}]](#manual-List___unzip) {α : Type u}
  {β : Type v} (l : [List]](#manual-List___nil) [(]](#manual-Prod___mk)α [×]](#manual-Prod___mk) β[)]](#manual-Prod___mk)) :
  [List]](#manual-List___nil) α [×]](#manual-Prod___mk) [List]](#manual-List___nil) β
```

Separates a list of pairs into two lists that contain the respective first and second components.

`O(|l|)`.

Examples:

- `[("Monday", 1), ("Tuesday", 2)].[unzip]](#manual-List___unzip) = (["Monday", "Tuesday"], [1, 2])`
- `[(x₁, y₁), (x₂, y₂), (x₃, y₃)].[unzip]](#manual-List___unzip) = ([x₁, x₂, x₃], [y₁, y₂, y₃])`
- `([] : [List]](#manual-List___nil) ([Nat]](#manual-Nat___zero) × [String]](#manual-String___ofByteArray))).[unzip]](#manual-List___unzip) = (([], []) : [List]](#manual-List___nil) [Nat]](#manual-Nat___zero) × [List]](#manual-List___nil) [String]](#manual-String___ofByteArray))`

def

```lean
[List.unzipTR.{u, v}]](#manual-List___unzipTR) {α : Type u} {β : Type v} (l : [List]](#manual-List___nil) [(]](#manual-Prod___mk)α [×]](#manual-Prod___mk) β[)]](#manual-Prod___mk)) :
  [List]](#manual-List___nil) α [×]](#manual-Prod___mk) [List]](#manual-List___nil) β



[List.unzipTR.{u, v}]](#manual-List___unzipTR) {α : Type u}
  {β : Type v} (l : [List]](#manual-List___nil) [(]](#manual-Prod___mk)α [×]](#manual-Prod___mk) β[)]](#manual-Prod___mk)) :
  [List]](#manual-List___nil) α [×]](#manual-Prod___mk) [List]](#manual-List___nil) β
```

Separates a list of pairs into two lists that contain the respective first and second components.

`O(|l|)`. This is a tail-recursive version of `[List.unzip]](#manual-List___unzip)` that's used at runtime.

Examples:

- `[("Monday", 1), ("Tuesday", 2)].[unzipTR]](#manual-List___unzipTR) = (["Monday", "Tuesday"], [1, 2])`
- `[(x₁, y₁), (x₂, y₂), (x₃, y₃)].[unzipTR]](#manual-List___unzipTR) = ([x₁, x₂, x₃], [y₁, y₂, y₃])`
- `([] : [List]](#manual-List___nil) ([Nat]](#manual-Nat___zero) × [String]](#manual-String___ofByteArray))).[unzipTR]](#manual-List___unzipTR) = (([], []) : [List]](#manual-List___nil) [Nat]](#manual-Nat___zero) × [List]](#manual-List___nil) [String]](#manual-String___ofByteArray))`

#### 20.15.3.12. Filtering {#manual-The-Lean-Language-Reference--Basic-Types--Linked-Lists--API-Reference--Filtering}

def

```lean
[List.filter.{u}]](#manual-List___filter) {α : Type u} (p : α → [Bool]](#manual-Bool___false)) (l : [List]](#manual-List___nil) α) : [List]](#manual-List___nil) α



[List.filter.{u}]](#manual-List___filter) {α : Type u}
  (p : α → [Bool]](#manual-Bool___false)) (l : [List]](#manual-List___nil) α) : [List]](#manual-List___nil) α
```

Returns the list of elements in `l` for which `p` returns `[true]](#manual-Bool___false)`.

`O(|l|)`.

Examples:

- `[1, 2, 5, 2, 7, 7].[filter]](#manual-List___filter) (· > 2) = [5, 7, 7]`
- `[1, 2, 5, 2, 7, 7].[filter]](#manual-List___filter) (fun _ => [false]](#manual-Bool___false)) = []`
- `[1, 2, 5, 2, 7, 7].[filter]](#manual-List___filter) (fun _ => [true]](#manual-Bool___false)) = [1, 2, 5, 2, 7, 7]`

def

```lean
[List.filterTR.{u}]](#manual-List___filterTR) {α : Type u} (p : α → [Bool]](#manual-Bool___false)) (as : [List]](#manual-List___nil) α) : [List]](#manual-List___nil) α



[List.filterTR.{u}]](#manual-List___filterTR) {α : Type u}
  (p : α → [Bool]](#manual-Bool___false)) (as : [List]](#manual-List___nil) α) : [List]](#manual-List___nil) α
```

Returns the list of elements in `l` for which `p` returns `[true]](#manual-Bool___false)`.

`O(|l|)`. This is a tail-recursive version of `[List.filter]](#manual-List___filter)`, used at runtime.

Examples:

- `[1, 2, 5, 2, 7, 7].[filterTR]](#manual-List___filterTR) (· > 2) = [5, 7, 7]`
- `[1, 2, 5, 2, 7, 7].[filterTR]](#manual-List___filterTR) (fun _ => [false]](#manual-Bool___false)) = []`
- `[1, 2, 5, 2, 7, 7].filterTR (fun _ => true) = * [1, 2, 5, 2, 7, 7]`

def

```lean
[List.filterM.{v}]](#manual-List___filterM) {m : Type → Type v} [[Monad]](#manual-Monad___mk) m] {α : Type}
  (p : α → m [Bool]](#manual-Bool___false)) (as : [List]](#manual-List___nil) α) : m ([List]](#manual-List___nil) α)



[List.filterM.{v}]](#manual-List___filterM) {m : Type → Type v}
  [[Monad]](#manual-Monad___mk) m] {α : Type} (p : α → m [Bool]](#manual-Bool___false))
  (as : [List]](#manual-List___nil) α) : m ([List]](#manual-List___nil) α)
```

Applies the monadic predicate `p` to every element in the list, in order from left to right, and
returns the list of elements for which `p` returns `[true]](#manual-Bool___false)`.

`O(|l|)`.

Example:

```lean
[#eval]](#manual-Lean___Parser___Command___eval) [1, 2, 5, 2, 7, 7].[filterM]](#manual-List___filterM) fun x => [do]](#manual-Lean___Parser___Term___do)
[IO.println](https://lean-lang.org/doc/reference/latest/IO/Console-Output/#IO___println) s!"Checking {x}"
return x < 3
```

```lean
Checking 1
Checking 2
Checking 5
Checking 2
Checking 7
Checking 7
```

```lean
[1, 2, 2]
```

def

```lean
[List.filterRevM.{v}]](#manual-List___filterRevM) {m : Type → Type v} [[Monad]](#manual-Monad___mk) m] {α : Type}
  (p : α → m [Bool]](#manual-Bool___false)) (as : [List]](#manual-List___nil) α) : m ([List]](#manual-List___nil) α)



[List.filterRevM.{v}]](#manual-List___filterRevM) {m : Type → Type v}
  [[Monad]](#manual-Monad___mk) m] {α : Type} (p : α → m [Bool]](#manual-Bool___false))
  (as : [List]](#manual-List___nil) α) : m ([List]](#manual-List___nil) α)
```

Applies the monadic predicate `p` on every element in the list in reverse order, from right to left,
and returns those elements for which `p` returns `[true]](#manual-Bool___false)`. The elements of the returned list are in
the same order as in the input list.

Example:

```lean
[#eval]](#manual-Lean___Parser___Command___eval) [1, 2, 5, 2, 7, 7].[filterRevM]](#manual-List___filterRevM) fun x => [do]](#manual-Lean___Parser___Term___do)
[IO.println](https://lean-lang.org/doc/reference/latest/IO/Console-Output/#IO___println) s!"Checking {x}"
return x < 3
```

```lean
Checking 7
Checking 7
Checking 2
Checking 5
Checking 2
Checking 1
```

```lean
[1, 2, 2]
```

def

```lean
[List.filterMap.{u, v}]](#manual-List___filterMap) {α : Type u} {β : Type v} (f : α → [Option]](#manual-Option___none) β) :
  [List]](#manual-List___nil) α → [List]](#manual-List___nil) β



[List.filterMap.{u, v}]](#manual-List___filterMap) {α : Type u}
  {β : Type v} (f : α → [Option]](#manual-Option___none) β) :
  [List]](#manual-List___nil) α → [List]](#manual-List___nil) β
```

Applies a function that returns an `[Option]](#manual-Option___none)` to each element of a list, collecting the non-`[none]](#manual-Option___none)`
values.

`O(|l|)`.

Example:

```lean
[#eval]](#manual-Lean___Parser___Command___eval) [1, 2, 5, 2, 7, 7].[filterMap]](#manual-List___filterMap) fun x =>
[if]](#manual-termIfThenElse) x > 2 [then]](#manual-termIfThenElse) [some]](#manual-Option___none) (2 * x) [else]](#manual-termIfThenElse) [none]](#manual-Option___none)
```

```lean
[10, 14, 14]
```

def

```lean
[List.filterMapTR.{u_1, u_2}]](#manual-List___filterMapTR) {α : Type u_1} {β : Type u_2}
  (f : α → [Option]](#manual-Option___none) β) (l : [List]](#manual-List___nil) α) : [List]](#manual-List___nil) β



[List.filterMapTR.{u_1, u_2}]](#manual-List___filterMapTR) {α : Type u_1}
  {β : Type u_2} (f : α → [Option]](#manual-Option___none) β)
  (l : [List]](#manual-List___nil) α) : [List]](#manual-List___nil) β
```

Applies a function that returns an `[Option]](#manual-Option___none)` to each element of a list, collecting the non-`[none]](#manual-Option___none)`
values.

`O(|l|)`. This is a tail-recursive version of `[List.filterMap]](#manual-List___filterMap)`, used at runtime.

Example:

```lean
[#eval]](#manual-Lean___Parser___Command___eval) [1, 2, 5, 2, 7, 7].[filterMapTR]](#manual-List___filterMapTR) fun x =>
[if]](#manual-termIfThenElse) x > 2 [then]](#manual-termIfThenElse) [some]](#manual-Option___none) (2 * x) [else]](#manual-termIfThenElse) [none]](#manual-Option___none)
```

```lean
[10, 14, 14]
```

def

```lean
[List.filterMapM.{u, v, w}]](#manual-List___filterMapM) {m : Type u → Type v} [[Monad]](#manual-Monad___mk) m] {α : Type w}
  {β : Type u} (f : α → m ([Option]](#manual-Option___none) β)) (as : [List]](#manual-List___nil) α) : m ([List]](#manual-List___nil) β)



[List.filterMapM.{u, v, w}]](#manual-List___filterMapM)
  {m : Type u → Type v} [[Monad]](#manual-Monad___mk) m]
  {α : Type w} {β : Type u}
  (f : α → m ([Option]](#manual-Option___none) β)) (as : [List]](#manual-List___nil) α) :
  m ([List]](#manual-List___nil) β)
```

Applies a monadic function that returns an `[Option]](#manual-Option___none)` to each element of a list, collecting the
non-`[none]](#manual-Option___none)` values.

`O(|l|)`.

Example:

```lean
[#eval]](#manual-Lean___Parser___Command___eval) [1, 2, 5, 2, 7, 7].[filterMapM]](#manual-List___filterMapM) fun x => [do]](#manual-Lean___Parser___Term___do)
[IO.println](https://lean-lang.org/doc/reference/latest/IO/Console-Output/#IO___println) s!"Examining {x}"
if x > 2 then return [some]](#manual-Option___none) (2 * x)
else return [none]](#manual-Option___none)
```

```lean
Examining 1
Examining 2
Examining 5
Examining 2
Examining 7
Examining 7
```

```lean
[10, 14, 14]
```

##### 20.15.3.12.1. Partitioning {#manual-The-Lean-Language-Reference--Basic-Types--Linked-Lists--API-Reference--Filtering--Partitioning}

def

```lean
[List.take.{u}]](#manual-List___take) {α : Type u} (n : [Nat]](#manual-Nat___zero)) (xs : [List]](#manual-List___nil) α) : [List]](#manual-List___nil) α



[List.take.{u}]](#manual-List___take) {α : Type u} (n : [Nat]](#manual-Nat___zero))
  (xs : [List]](#manual-List___nil) α) : [List]](#manual-List___nil) α
```

Extracts the first `n` elements of `xs`, or the whole list if `n` is greater than `xs.[length]](#manual-List___length)`.

`O(min n |xs|)`.

Examples:

- `[a, b, c, d, e].[take]](#manual-List___take) 0 = []`
- `[a, b, c, d, e].[take]](#manual-List___take) 3 = [a, b, c]`
- `[a, b, c, d, e].[take]](#manual-List___take) 6 = [a, b, c, d, e]`

def

```lean
[List.takeTR.{u_1}]](#manual-List___takeTR) {α : Type u_1} (n : [Nat]](#manual-Nat___zero)) (l : [List]](#manual-List___nil) α) : [List]](#manual-List___nil) α



[List.takeTR.{u_1}]](#manual-List___takeTR) {α : Type u_1} (n : [Nat]](#manual-Nat___zero))
  (l : [List]](#manual-List___nil) α) : [List]](#manual-List___nil) α
```

Extracts the first `n` elements of `xs`, or the whole list if `n` is greater than `xs.length`.

`O(min n |xs|)`. This is a tail-recursive version of `[List.take]](#manual-List___take)`, used at runtime.

Examples:

- `[a, b, c, d, e].[takeTR]](#manual-List___takeTR) 0 = []`
- `[a, b, c, d, e].[takeTR]](#manual-List___takeTR) 3 = [a, b, c]`
- `[a, b, c, d, e].[takeTR]](#manual-List___takeTR) 6 = [a, b, c, d, e]`

def

```lean
[List.takeWhile.{u}]](#manual-List___takeWhile) {α : Type u} (p : α → [Bool]](#manual-Bool___false)) (xs : [List]](#manual-List___nil) α) : [List]](#manual-List___nil) α



[List.takeWhile.{u}]](#manual-List___takeWhile) {α : Type u}
  (p : α → [Bool]](#manual-Bool___false)) (xs : [List]](#manual-List___nil) α) : [List]](#manual-List___nil) α
```

Returns the longest initial segment of `xs` for which `p` returns true.

`O(|xs|)`.

Examples:

- `[7, 6, 4, 8].[takeWhile]](#manual-List___takeWhile) (· > 5) = [7, 6]`
- `[7, 6, 6, 5].[takeWhile]](#manual-List___takeWhile) (· > 5) = [7, 6, 6]`
- `[7, 6, 6, 8].[takeWhile]](#manual-List___takeWhile) (· > 5) = [7, 6, 6, 8]`

def

```lean
[List.takeWhileTR.{u_1}]](#manual-List___takeWhileTR) {α : Type u_1} (p : α → [Bool]](#manual-Bool___false)) (l : [List]](#manual-List___nil) α) :
  [List]](#manual-List___nil) α



[List.takeWhileTR.{u_1}]](#manual-List___takeWhileTR) {α : Type u_1}
  (p : α → [Bool]](#manual-Bool___false)) (l : [List]](#manual-List___nil) α) : [List]](#manual-List___nil) α
```

Returns the longest initial segment of `xs` for which `p` returns true.

`O(|xs|)`. This is a tail-recursive version of `[List.take]](#manual-List___take)`, used at runtime.

Examples:

- `[7, 6, 4, 8].[takeWhileTR]](#manual-List___takeWhileTR) (· > 5) = [7, 6]`
- `[7, 6, 6, 5].[takeWhileTR]](#manual-List___takeWhileTR) (· > 5) = [7, 6, 6]`
- `[7, 6, 6, 8].[takeWhileTR]](#manual-List___takeWhileTR) (· > 5) = [7, 6, 6, 8]`

def

```lean
[List.drop.{u}]](#manual-List___drop) {α : Type u} (n : [Nat]](#manual-Nat___zero)) (xs : [List]](#manual-List___nil) α) : [List]](#manual-List___nil) α



[List.drop.{u}]](#manual-List___drop) {α : Type u} (n : [Nat]](#manual-Nat___zero))
  (xs : [List]](#manual-List___nil) α) : [List]](#manual-List___nil) α
```

Removes the first `n` elements of the list `xs`. Returns the empty list if `n` is greater than the
length of the list.

`O(min n |xs|)`.

Examples:

- `[0, 1, 2, 3, 4].[drop]](#manual-List___drop) 0 = [0, 1, 2, 3, 4]`
- `[0, 1, 2, 3, 4].[drop]](#manual-List___drop) 3 = [3, 4]`
- `[0, 1, 2, 3, 4].[drop]](#manual-List___drop) 6 = []`

def

```lean
[List.dropWhile.{u}]](#manual-List___dropWhile) {α : Type u} (p : α → [Bool]](#manual-Bool___false)) : [List]](#manual-List___nil) α → [List]](#manual-List___nil) α



[List.dropWhile.{u}]](#manual-List___dropWhile) {α : Type u}
  (p : α → [Bool]](#manual-Bool___false)) : [List]](#manual-List___nil) α → [List]](#manual-List___nil) α
```

Removes the longest prefix of a list for which `p` returns `[true]](#manual-Bool___false)`.

Elements are removed from the list until one is encountered for which `p` returns `[false]](#manual-Bool___false)`. This
element and the remainder of the list are returned.

`O(|l|)`.

Examples:

- `[1, 3, 2, 4, 2, 7, 4].[dropWhile]](#manual-List___dropWhile) (· < 4) = [4, 2, 7, 4]`
- `[8, 3, 2, 4, 2, 7, 4].[dropWhile]](#manual-List___dropWhile) (· < 4) = [8, 3, 2, 4, 2, 7, 4]`
- `[8, 3, 2, 4, 2, 7, 4].[dropWhile]](#manual-List___dropWhile) (· < 100) = []`

def

```lean
[List.dropLast.{u_1}]](#manual-List___dropLast) {α : Type u_1} : [List]](#manual-List___nil) α → [List]](#manual-List___nil) α



[List.dropLast.{u_1}]](#manual-List___dropLast) {α : Type u_1} :
  [List]](#manual-List___nil) α → [List]](#manual-List___nil) α
```

Removes the last element of the list, if one exists.

Examples:

- `[].[dropLast]](#manual-List___dropLast) = []`
- `["tea"].[dropLast]](#manual-List___dropLast) = []`
- `["tea", "coffee", "juice"].[dropLast]](#manual-List___dropLast) = ["tea", "coffee"]`

def

```lean
[List.dropLastTR.{u_1}]](#manual-List___dropLastTR) {α : Type u_1} (l : [List]](#manual-List___nil) α) : [List]](#manual-List___nil) α



[List.dropLastTR.{u_1}]](#manual-List___dropLastTR) {α : Type u_1}
  (l : [List]](#manual-List___nil) α) : [List]](#manual-List___nil) α
```

Removes the last element of the list, if one exists.

This is a tail-recursive version of `[List.dropLast]](#manual-List___dropLast)`, used at runtime.

Examples:

- `[].[dropLastTR]](#manual-List___dropLastTR) = []`
- `["tea"].[dropLastTR]](#manual-List___dropLastTR) = []`
- `["tea", "coffee", "juice"].[dropLastTR]](#manual-List___dropLastTR) = ["tea", "coffee"]`

def

```lean
[List.splitAt.{u}]](#manual-List___splitAt) {α : Type u} (n : [Nat]](#manual-Nat___zero)) (l : [List]](#manual-List___nil) α) : [List]](#manual-List___nil) α [×]](#manual-Prod___mk) [List]](#manual-List___nil) α



[List.splitAt.{u}]](#manual-List___splitAt) {α : Type u} (n : [Nat]](#manual-Nat___zero))
  (l : [List]](#manual-List___nil) α) : [List]](#manual-List___nil) α [×]](#manual-Prod___mk) [List]](#manual-List___nil) α
```

Splits a list at an index, resulting in the first `n` elements of `l` paired with the remaining
elements.

If `n` is greater than the length of `l`, then the resulting pair consists of `l` and the empty
list. `[List.splitAt]](#manual-List___splitAt)` is equivalent to a combination of `[List.take]](#manual-List___take)` and `[List.drop]](#manual-List___drop)`, but it is more
efficient.

Examples:

- `["red", "green", "blue"].[splitAt]](#manual-List___splitAt) 2 = (["red", "green"], ["blue"])`
- `["red", "green", "blue"].splitAt 3 = (["red", "green", "blue], [])`
- `["red", "green", "blue"].splitAt 4 = (["red", "green", "blue], [])`

def

```lean
[List.span.{u}]](#manual-List___span) {α : Type u} (p : α → [Bool]](#manual-Bool___false)) (as : [List]](#manual-List___nil) α) :
  [List]](#manual-List___nil) α [×]](#manual-Prod___mk) [List]](#manual-List___nil) α



[List.span.{u}]](#manual-List___span) {α : Type u} (p : α → [Bool]](#manual-Bool___false))
  (as : [List]](#manual-List___nil) α) : [List]](#manual-List___nil) α [×]](#manual-Prod___mk) [List]](#manual-List___nil) α
```

Splits a list into the longest initial segment for which `p` returns `[true]](#manual-Bool___false)`, paired with the
remainder of the list.

`O(|l|)`.

Examples:

- `[6, 8, 9, 5, 2, 9].[span]](#manual-List___span) (· > 5) = ([6, 8, 9], [5, 2, 9])`
- `[6, 8, 9, 5, 2, 9].[span]](#manual-List___span) (· > 10) = ([], [6, 8, 9, 5, 2, 9])`
- `[6, 8, 9, 5, 2, 9].[span]](#manual-List___span) (· > 0) = ([6, 8, 9, 5, 2, 9], [])`

def

```lean
[List.splitBy.{u}]](#manual-List___splitBy) {α : Type u} (R : α → α → [Bool]](#manual-Bool___false)) :
  [List]](#manual-List___nil) α → [List]](#manual-List___nil) ([List]](#manual-List___nil) α)



[List.splitBy.{u}]](#manual-List___splitBy) {α : Type u}
  (R : α → α → [Bool]](#manual-Bool___false)) :
  [List]](#manual-List___nil) α → [List]](#manual-List___nil) ([List]](#manual-List___nil) α)
```

Splits a list into the longest segments in which each pair of adjacent elements are related by `R`.

`O(|l|)`.

Examples:

- `[1, 1, 2, 2, 2, 3, 2].[splitBy]](#manual-List___splitBy) (· == ·) = [[1, 1], [2, 2, 2], [3], [2]]`
- `[1, 2, 5, 4, 5, 1, 4].[splitBy]](#manual-List___splitBy) (· < ·) = [[1, 2, 5], [4, 5], [1, 4]]`
- `[1, 2, 5, 4, 5, 1, 4].[splitBy]](#manual-List___splitBy) (fun _ _ => [true]](#manual-Bool___false)) = [[1, 2, 5, 4, 5, 1, 4]]`
- `[1, 2, 5, 4, 5, 1, 4].[splitBy]](#manual-List___splitBy) (fun _ _ => [false]](#manual-Bool___false)) = [[1], [2], [5], [4], [5], [1], [4]]`

def

```lean
[List.partition.{u}]](#manual-List___partition) {α : Type u} (p : α → [Bool]](#manual-Bool___false)) (as : [List]](#manual-List___nil) α) :
  [List]](#manual-List___nil) α [×]](#manual-Prod___mk) [List]](#manual-List___nil) α



[List.partition.{u}]](#manual-List___partition) {α : Type u}
  (p : α → [Bool]](#manual-Bool___false)) (as : [List]](#manual-List___nil) α) :
  [List]](#manual-List___nil) α [×]](#manual-Prod___mk) [List]](#manual-List___nil) α
```

Returns a pair of lists that together contain all the elements of `as`. The first list contains
those elements for which `p` returns `[true]](#manual-Bool___false)`, and the second contains those for which `p` returns
`[false]](#manual-Bool___false)`.

`O(|l|)`. `as.[partition]](#manual-List___partition) p` is equivalent to `(as.[filter]](#manual-List___filter) p, as.[filter]](#manual-List___filter) ([not]](#manual-Bool___not) ∘ p))`, but it is slightly
more efficient since it only has to do one pass over the list.

Examples:

- `[1, 2, 5, 2, 7, 7].[partition]](#manual-List___partition) (· > 2) = ([5, 7, 7], [1, 2, 2])`
- `[1, 2, 5, 2, 7, 7].[partition]](#manual-List___partition) (fun _ => [false]](#manual-Bool___false)) = ([], [1, 2, 5, 2, 7, 7])`
- `[1, 2, 5, 2, 7, 7].[partition]](#manual-List___partition) (fun _ => [true]](#manual-Bool___false)) = ([1, 2, 5, 2, 7, 7], [])`

def

```lean
[List.partitionM.{u_1}]](#manual-List___partitionM) {m : Type → Type u_1} {α : Type} [[Monad]](#manual-Monad___mk) m]
  (p : α → m [Bool]](#manual-Bool___false)) (l : [List]](#manual-List___nil) α) : m [(]](#manual-Prod___mk)[List]](#manual-List___nil) α [×]](#manual-Prod___mk) [List]](#manual-List___nil) α[)]](#manual-Prod___mk)



[List.partitionM.{u_1}]](#manual-List___partitionM)
  {m : Type → Type u_1} {α : Type}
  [[Monad]](#manual-Monad___mk) m] (p : α → m [Bool]](#manual-Bool___false))
  (l : [List]](#manual-List___nil) α) : m [(]](#manual-Prod___mk)[List]](#manual-List___nil) α [×]](#manual-Prod___mk) [List]](#manual-List___nil) α[)]](#manual-Prod___mk)
```

Returns a pair of lists that together contain all the elements of `as`. The first list contains
those elements for which the monadic predicate `p` returns `[true]](#manual-Bool___false)`, and the second contains those for
which `p` returns `[false]](#manual-Bool___false)`. The list's elements are examined in order, from left to right.

This is a monadic version of `[List.partition]](#manual-List___partition)`.

Example:

```lean
def posOrNeg (x : [Int]](#manual-Int___ofNat)) : [Except]](#manual-Except___error) [String]](#manual-String___ofByteArray) [Bool]](#manual-Bool___false) :=
[if]](#manual-termIfThenElse) x > 0 [then]](#manual-termIfThenElse) [pure]](#manual-Pure___mk) [true]](#manual-Bool___false)
[else]](#manual-termIfThenElse) [if]](#manual-termIfThenElse) x < 0 [then]](#manual-termIfThenElse) [pure]](#manual-Pure___mk) [false]](#manual-Bool___false)
[else]](#manual-termIfThenElse) [throw]](#manual-MonadExcept___mk) "Zero is not positive or negative"
```

```
#eval [-1, 2, 3].partitionM posOrNeg
```

```lean
[Except.ok]](#manual-Except___error) ([2, 3], [-1])
```

```
#eval [0, 2, 3].partitionM posOrNeg
```

```lean
[Except.error]](#manual-Except___error) "Zero is not positive or negative"
```

def

```lean
[List.partitionMap.{u_1, u_2, u_3}]](#manual-List___partitionMap) {α : Type u_1} {β : Type u_2}
  {γ : Type u_3} (f : α → β [⊕]](#manual-Sum___inl) γ) (l : [List]](#manual-List___nil) α) : [List]](#manual-List___nil) β [×]](#manual-Prod___mk) [List]](#manual-List___nil) γ



[List.partitionMap.{u_1, u_2, u_3}]](#manual-List___partitionMap)
  {α : Type u_1} {β : Type u_2}
  {γ : Type u_3} (f : α → β [⊕]](#manual-Sum___inl) γ)
  (l : [List]](#manual-List___nil) α) : [List]](#manual-List___nil) β [×]](#manual-Prod___mk) [List]](#manual-List___nil) γ
```

Applies a function that returns a disjoint union to each element of a list, collecting the `[Sum.inl]](#manual-Sum___inl)`
and `[Sum.inr]](#manual-Sum___inl)` results into separate lists.

Examples:

- `[0, 1, 2, 3].[partitionMap]](#manual-List___partitionMap) (fun x => [if]](#manual-termIfThenElse) x % 2 = 0 [then]](#manual-termIfThenElse) [.inl]](#manual-Sum___inl) x [else]](#manual-termIfThenElse) [.inr]](#manual-Sum___inl) x) = ([0, 2], [1, 3])`
- `[0, 1, 2, 3].[partitionMap]](#manual-List___partitionMap) (fun x => [if]](#manual-termIfThenElse) x = 0 [then]](#manual-termIfThenElse) [.inl]](#manual-Sum___inl) x [else]](#manual-termIfThenElse) [.inr]](#manual-Sum___inl) x) = ([0], [1, 2, 3])`

def

```lean
[List.groupByKey.{u, v}]](#manual-List___groupByKey) {α : Type u} {β : Type v} [[BEq]](#manual-BEq___mk) α] [[Hashable]](#manual-Hashable___mk) α]
  (key : β → α) (xs : [List]](#manual-List___nil) β) : [Std.HashMap](https://lean-lang.org/doc/reference/latest/Basic-Types/Maps-and-Sets/#Std___HashMap) α ([List]](#manual-List___nil) β)



[List.groupByKey.{u, v}]](#manual-List___groupByKey) {α : Type u}
  {β : Type v} [[BEq]](#manual-BEq___mk) α] [[Hashable]](#manual-Hashable___mk) α]
  (key : β → α) (xs : [List]](#manual-List___nil) β) :
  [Std.HashMap](https://lean-lang.org/doc/reference/latest/Basic-Types/Maps-and-Sets/#Std___HashMap) α ([List]](#manual-List___nil) β)
```

Groups the elements of a list `xs` according to the function `key`, returning a hash map in which
each group is associated with its key. Groups preserve the relative order of elements in `xs`.

Example:

```lean
[#eval]](#manual-Lean___Parser___Command___eval) [0, 1, 2, 3, 4, 5, 6].[groupByKey]](#manual-List___groupByKey) (· % 2)
```

```lean
[Std.HashMap.ofList](https://lean-lang.org/doc/reference/latest/Basic-Types/Maps-and-Sets/#Std___HashMap___ofList) [(0, [0, 2, 4, 6]), (1, [1, 3, 5])]
```

#### 20.15.3.13. Element Predicates {#manual-The-Lean-Language-Reference--Basic-Types--Linked-Lists--API-Reference--Element-Predicates}

def

```lean
[List.contains.{u}]](#manual-List___contains) {α : Type u} [[BEq]](#manual-BEq___mk) α] (as : [List]](#manual-List___nil) α) (a : α) : [Bool]](#manual-Bool___false)



[List.contains.{u}]](#manual-List___contains) {α : Type u} [[BEq]](#manual-BEq___mk) α]
  (as : [List]](#manual-List___nil) α) (a : α) : [Bool]](#manual-Bool___false)
```

Checks whether `a` is an element of `as`, using `==` to compare elements.

`O(|as|)`. `[List.elem]](#manual-List___elem)` is a synonym that takes the element before the list.

The preferred simp normal form is `l.contains a`, and when `[LawfulBEq]](#manual-LawfulBEq___mk) α` is available,
`l.contains a = [true]](#manual-Bool___false) ↔ a ∈ l` and `l.contains a = [false]](#manual-Bool___false) ↔ a ∉ l`.

Examples:

- `[1, 4, 2, 3, 3, 7].[contains]](#manual-List___contains) 3 = [true]](#manual-Bool___false)`
- `[List.contains]](#manual-List___contains) [1, 4, 2, 3, 3, 7] 5 = [false]](#manual-Bool___false)`

def

```lean
[List.elem.{u}]](#manual-List___elem) {α : Type u} [[BEq]](#manual-BEq___mk) α] (a : α) (l : [List]](#manual-List___nil) α) : [Bool]](#manual-Bool___false)



[List.elem.{u}]](#manual-List___elem) {α : Type u} [[BEq]](#manual-BEq___mk) α] (a : α)
  (l : [List]](#manual-List___nil) α) : [Bool]](#manual-Bool___false)
```

Checks whether `a` is an element of `l`, using `==` to compare elements.

`O(|l|)`. `[List.contains]](#manual-List___contains)` is a synonym that takes the list before the element.

The preferred simp normal form is `l.[contains]](#manual-List___contains) a`. When `[LawfulBEq]](#manual-LawfulBEq___mk) α` is available,
`l.[contains]](#manual-List___contains) a = [true]](#manual-Bool___false) ↔ a ∈ l` and `l.[contains]](#manual-List___contains) a = [false]](#manual-Bool___false) ↔ a ∉ l`.

Example:

- `[List.elem]](#manual-List___elem) 3 [1, 4, 2, 3, 3, 7] = [true]](#manual-Bool___false)`
- `[List.elem]](#manual-List___elem) 5 [1, 4, 2, 3, 3, 7] = [false]](#manual-Bool___false)`

def

```lean
[List.all.{u}]](#manual-List___all) {α : Type u} : [List]](#manual-List___nil) α → (α → [Bool]](#manual-Bool___false)) → [Bool]](#manual-Bool___false)



[List.all.{u}]](#manual-List___all) {α : Type u} :
  [List]](#manual-List___nil) α → (α → [Bool]](#manual-Bool___false)) → [Bool]](#manual-Bool___false)
```

Returns `[true]](#manual-Bool___false)` if `p` returns `[true]](#manual-Bool___false)` for every element of `l`.

`O(|l|)`. Short-circuits upon encountering the first `[false]](#manual-Bool___false)`.

Examples:

- `[a, b, c].[all]](#manual-List___all) p = (p a && (p b && p c))`
- `[2, 4, 6].[all]](#manual-List___all) (· % 2 = 0) = [true]](#manual-Bool___false)`
- `[2, 4, 5, 6].[all]](#manual-List___all) (· % 2 = 0) = [false]](#manual-Bool___false)`

def

```lean
[List.allM.{u, v}]](#manual-List___allM) {m : Type → Type u} [[Monad]](#manual-Monad___mk) m] {α : Type v}
  (p : α → m [Bool]](#manual-Bool___false)) (l : [List]](#manual-List___nil) α) : m [Bool]](#manual-Bool___false)



[List.allM.{u, v}]](#manual-List___allM) {m : Type → Type u}
  [[Monad]](#manual-Monad___mk) m] {α : Type v} (p : α → m [Bool]](#manual-Bool___false))
  (l : [List]](#manual-List___nil) α) : m [Bool]](#manual-Bool___false)
```

Returns true if the monadic predicate `p` returns `[true]](#manual-Bool___false)` for every element of `l`.

`O(|l|)`. Short-circuits upon encountering the first `[false]](#manual-Bool___false)`. The elements in `l` are examined in
order from left to right.

def

```lean
[List.any.{u}]](#manual-List___any) {α : Type u} (l : [List]](#manual-List___nil) α) (p : α → [Bool]](#manual-Bool___false)) : [Bool]](#manual-Bool___false)



[List.any.{u}]](#manual-List___any) {α : Type u} (l : [List]](#manual-List___nil) α)
  (p : α → [Bool]](#manual-Bool___false)) : [Bool]](#manual-Bool___false)
```

Returns `[true]](#manual-Bool___false)` if `p` returns `[true]](#manual-Bool___false)` for any element of `l`.

`O(|l|)`. Short-circuits upon encountering the first `[true]](#manual-Bool___false)`.

Examples:

- `[2, 4, 6].[any]](#manual-List___any) (· % 2 = 0) = [true]](#manual-Bool___false)`
- `[2, 4, 6].[any]](#manual-List___any) (· % 2 = 1) = [false]](#manual-Bool___false)`
- `[2, 4, 5, 6].[any]](#manual-List___any) (· % 2 = 0) = [true]](#manual-Bool___false)`
- `[2, 4, 5, 6].[any]](#manual-List___any) (· % 2 = 1) = [true]](#manual-Bool___false)`

def

```lean
[List.anyM.{u, v}]](#manual-List___anyM) {m : Type → Type u} [[Monad]](#manual-Monad___mk) m] {α : Type v}
  (p : α → m [Bool]](#manual-Bool___false)) (l : [List]](#manual-List___nil) α) : m [Bool]](#manual-Bool___false)



[List.anyM.{u, v}]](#manual-List___anyM) {m : Type → Type u}
  [[Monad]](#manual-Monad___mk) m] {α : Type v} (p : α → m [Bool]](#manual-Bool___false))
  (l : [List]](#manual-List___nil) α) : m [Bool]](#manual-Bool___false)
```

Returns true if the monadic predicate `p` returns `[true]](#manual-Bool___false)` for any element of `l`.

`O(|l|)`. Short-circuits upon encountering the first `[true]](#manual-Bool___false)`. The elements in `l` are examined in
order from left to right.

def

```lean
[List.and]](#manual-List___and) (bs : [List]](#manual-List___nil) [Bool]](#manual-Bool___false)) : [Bool]](#manual-Bool___false)



[List.and]](#manual-List___and) (bs : [List]](#manual-List___nil) [Bool]](#manual-Bool___false)) : [Bool]](#manual-Bool___false)
```

Returns `[true]](#manual-Bool___false)` if every element of `bs` is the value `[true]](#manual-Bool___false)`.

`O(|bs|)`. Short-circuits at the first `[false]](#manual-Bool___false)` value.

- `[[true]](#manual-Bool___false), [true]](#manual-Bool___false), [true]](#manual-Bool___false)].[and]](#manual-List___and) = [true]](#manual-Bool___false)`
- `[[true]](#manual-Bool___false), [false]](#manual-Bool___false), [true]](#manual-Bool___false)].[and]](#manual-List___and) = [false]](#manual-Bool___false)`
- `[[true]](#manual-Bool___false), [false]](#manual-Bool___false), [false]](#manual-Bool___false)].[and]](#manual-List___and) = [false]](#manual-Bool___false)`
- `[].[and]](#manual-List___and) = [true]](#manual-Bool___false)`

def

```lean
[List.or]](#manual-List___or) (bs : [List]](#manual-List___nil) [Bool]](#manual-Bool___false)) : [Bool]](#manual-Bool___false)



[List.or]](#manual-List___or) (bs : [List]](#manual-List___nil) [Bool]](#manual-Bool___false)) : [Bool]](#manual-Bool___false)
```

Returns `[true]](#manual-Bool___false)` if `[true]](#manual-Bool___false)` is an element of the list `bs`.

`O(|bs|)`. Short-circuits at the first `[true]](#manual-Bool___false)` value.

- `[[true]](#manual-Bool___false), [true]](#manual-Bool___false), [true]](#manual-Bool___false)].[or]](#manual-List___or) = [true]](#manual-Bool___false)`
- `[[true]](#manual-Bool___false), [false]](#manual-Bool___false), [true]](#manual-Bool___false)].[or]](#manual-List___or) = [true]](#manual-Bool___false)`
- `[[false]](#manual-Bool___false), [false]](#manual-Bool___false), [false]](#manual-Bool___false)].[or]](#manual-List___or) = [false]](#manual-Bool___false)`
- `[[false]](#manual-Bool___false), [false]](#manual-Bool___false), [true]](#manual-Bool___false)].[or]](#manual-List___or) = [true]](#manual-Bool___false)`
- `[].[or]](#manual-List___or) = [false]](#manual-Bool___false)`

#### 20.15.3.14. Comparisons {#manual-The-Lean-Language-Reference--Basic-Types--Linked-Lists--API-Reference--Comparisons}

def

```lean
[List.beq.{u}]](#manual-List___beq) {α : Type u} [[BEq]](#manual-BEq___mk) α] : [List]](#manual-List___nil) α → [List]](#manual-List___nil) α → [Bool]](#manual-Bool___false)



[List.beq.{u}]](#manual-List___beq) {α : Type u} [[BEq]](#manual-BEq___mk) α] :
  [List]](#manual-List___nil) α → [List]](#manual-List___nil) α → [Bool]](#manual-Bool___false)
```

Checks whether two lists have the same length and their elements are pairwise `[BEq]](#manual-BEq___mk)`. Normally used
via the `==` operator.

def

```lean
[List.isEqv.{u}]](#manual-List___isEqv) {α : Type u} (as bs : [List]](#manual-List___nil) α) (eqv : α → α → [Bool]](#manual-Bool___false)) : [Bool]](#manual-Bool___false)



[List.isEqv.{u}]](#manual-List___isEqv) {α : Type u}
  (as bs : [List]](#manual-List___nil) α) (eqv : α → α → [Bool]](#manual-Bool___false)) :
  [Bool]](#manual-Bool___false)
```

Returns `[true]](#manual-Bool___false)` if `as` and `bs` have the same length and they are pairwise related by `eqv`.

`O(min |as| |bs|)`. Short-circuits at the first non-related pair of elements.

Examples:

- `[1, 2, 3].[isEqv]](#manual-List___isEqv) [2, 3, 4] (· < ·) = [true]](#manual-Bool___false)`
- `[1, 2, 3].[isEqv]](#manual-List___isEqv) [2, 2, 4] (· < ·) = [false]](#manual-Bool___false)`
- `[1, 2, 3].[isEqv]](#manual-List___isEqv) [2, 3] (· < ·) = [false]](#manual-Bool___false)`

def

```lean
[List.isPerm.{u}]](#manual-List___isPerm) {α : Type u} [[BEq]](#manual-BEq___mk) α] : [List]](#manual-List___nil) α → [List]](#manual-List___nil) α → [Bool]](#manual-Bool___false)



[List.isPerm.{u}]](#manual-List___isPerm) {α : Type u} [[BEq]](#manual-BEq___mk) α] :
  [List]](#manual-List___nil) α → [List]](#manual-List___nil) α → [Bool]](#manual-Bool___false)
```

Returns `[true]](#manual-Bool___false)` if `l₁` and `l₂` are permutations of each other. `O(|l₁| * |l₂|)`.

The relation `[List.Perm]](#manual-List___Perm___nil)` is a logical characterization of permutations. When the `[BEq]](#manual-BEq___mk) α` instance
corresponds to `[DecidableEq]](#manual-DecidableEq) α`, `isPerm l₁ l₂ ↔ l₁ ~ l₂` (use the theorem `isPerm_iff`).

def

```lean
[List.isPrefixOf.{u}]](#manual-List___isPrefixOf) {α : Type u} [[BEq]](#manual-BEq___mk) α] : [List]](#manual-List___nil) α → [List]](#manual-List___nil) α → [Bool]](#manual-Bool___false)



[List.isPrefixOf.{u}]](#manual-List___isPrefixOf) {α : Type u} [[BEq]](#manual-BEq___mk) α] :
  [List]](#manual-List___nil) α → [List]](#manual-List___nil) α → [Bool]](#manual-Bool___false)
```

Checks whether the first list is a prefix of the second.

The relation `[List]](#manual-List___nil).IsPrefixOf` expresses this property with respect to logical equality.

Examples:

- `[1, 2].[isPrefixOf]](#manual-List___isPrefixOf) [1, 2, 3] = [true]](#manual-Bool___false)`
- `[1, 2].[isPrefixOf]](#manual-List___isPrefixOf) [1, 2] = [true]](#manual-Bool___false)`
- `[1, 2].[isPrefixOf]](#manual-List___isPrefixOf) [1] = [false]](#manual-Bool___false)`
- `[1, 2].[isPrefixOf]](#manual-List___isPrefixOf) [1, 1, 2, 3] = [false]](#manual-Bool___false)`

def

```lean
[List.isPrefixOf?.{u}]](#manual-List___isPrefixOf___) {α : Type u} [[BEq]](#manual-BEq___mk) α] (l₁ l₂ : [List]](#manual-List___nil) α) :
  [Option]](#manual-Option___none) ([List]](#manual-List___nil) α)



[List.isPrefixOf?.{u}]](#manual-List___isPrefixOf___) {α : Type u} [[BEq]](#manual-BEq___mk) α]
  (l₁ l₂ : [List]](#manual-List___nil) α) : [Option]](#manual-Option___none) ([List]](#manual-List___nil) α)
```

If the first list is a prefix of the second, returns the result of dropping the prefix.

In other words, `isPrefixOf? l₁ l₂` returns `[some]](#manual-Option___none) t` when `l₂ == l₁ ++ t`.

Examples:

- `[1, 2].[isPrefixOf?]](#manual-List___isPrefixOf___) [1, 2, 3] = [some]](#manual-Option___none) [3]`
- `[1, 2].[isPrefixOf?]](#manual-List___isPrefixOf___) [1, 2] = [some]](#manual-Option___none) []`
- `[1, 2].[isPrefixOf?]](#manual-List___isPrefixOf___) [1] = [none]](#manual-Option___none)`
- `[1, 2].[isPrefixOf?]](#manual-List___isPrefixOf___) [1, 1, 2, 3] = [none]](#manual-Option___none)`

def

```lean
[List.isSublist.{u}]](#manual-List___isSublist) {α : Type u} [[BEq]](#manual-BEq___mk) α] : [List]](#manual-List___nil) α → [List]](#manual-List___nil) α → [Bool]](#manual-Bool___false)



[List.isSublist.{u}]](#manual-List___isSublist) {α : Type u} [[BEq]](#manual-BEq___mk) α] :
  [List]](#manual-List___nil) α → [List]](#manual-List___nil) α → [Bool]](#manual-Bool___false)
```

True if the first list is a potentially non-contiguous sub-sequence of the second list, comparing
elements with the `==` operator.

The relation `[List.Sublist]](#manual-List___Sublist___slnil)` is a logical characterization of this property.

Examples:

- `[1, 3].[isSublist]](#manual-List___isSublist) [0, 1, 2, 3, 4] = [true]](#manual-Bool___false)`
- `[1, 3].[isSublist]](#manual-List___isSublist) [0, 1, 2, 4] = [false]](#manual-Bool___false)`

def

```lean
[List.isSuffixOf.{u}]](#manual-List___isSuffixOf) {α : Type u} [[BEq]](#manual-BEq___mk) α] (l₁ l₂ : [List]](#manual-List___nil) α) : [Bool]](#manual-Bool___false)



[List.isSuffixOf.{u}]](#manual-List___isSuffixOf) {α : Type u} [[BEq]](#manual-BEq___mk) α]
  (l₁ l₂ : [List]](#manual-List___nil) α) : [Bool]](#manual-Bool___false)
```

Checks whether the first list is a suffix of the second.

The relation `[List]](#manual-List___nil).IsSuffixOf` expresses this property with respect to logical equality.

Examples:

- `[2, 3].[isSuffixOf]](#manual-List___isSuffixOf) [1, 2, 3] = [true]](#manual-Bool___false)`
- `[2, 3].[isSuffixOf]](#manual-List___isSuffixOf) [1, 2, 3, 4] = [false]](#manual-Bool___false)`
- `[2, 3].[isSuffixOf]](#manual-List___isSuffixOf) [1, 2] = [false]](#manual-Bool___false)`
- `[2, 3].[isSuffixOf]](#manual-List___isSuffixOf) [1, 1, 2, 3] = [true]](#manual-Bool___false)`

def

```lean
[List.isSuffixOf?.{u}]](#manual-List___isSuffixOf___) {α : Type u} [[BEq]](#manual-BEq___mk) α] (l₁ l₂ : [List]](#manual-List___nil) α) :
  [Option]](#manual-Option___none) ([List]](#manual-List___nil) α)



[List.isSuffixOf?.{u}]](#manual-List___isSuffixOf___) {α : Type u} [[BEq]](#manual-BEq___mk) α]
  (l₁ l₂ : [List]](#manual-List___nil) α) : [Option]](#manual-Option___none) ([List]](#manual-List___nil) α)
```

If the first list is a suffix of the second, returns the result of dropping the suffix from the
second.

In other words, `isSuffixOf? l₁ l₂` returns `[some]](#manual-Option___none) t` when `l₂ == t ++ l₁`.

Examples:

- `[2, 3].[isSuffixOf?]](#manual-List___isSuffixOf___) [1, 2, 3] = [some]](#manual-Option___none) [1]`
- `[2, 3].[isSuffixOf?]](#manual-List___isSuffixOf___) [1, 2, 3, 4] = [none]](#manual-Option___none)`
- `[2, 3].[isSuffixOf?]](#manual-List___isSuffixOf___) [1, 2] = [none]](#manual-Option___none)`
- `[2, 3].[isSuffixOf?]](#manual-List___isSuffixOf___) [1, 1, 2, 3] = [some]](#manual-Option___none) [1, 1]`

def

```lean
[List.le.{u}]](#manual-List___le) {α : Type u} [[LT]](#manual-LT___mk) α] (as bs : [List]](#manual-List___nil) α) : Prop



[List.le.{u}]](#manual-List___le) {α : Type u} [[LT]](#manual-LT___mk) α]
  (as bs : [List]](#manual-List___nil) α) : Prop
```

Non-strict ordering of lists with respect to a strict ordering of their elements.

`as ≤ bs` if `¬ bs < as`.

This relation can be treated as a lexicographic order if the underlying `[LT]](#manual-LT___mk) α` instance is
well-behaved. In particular, it should be irreflexive, asymmetric, and antisymmetric. These
requirements are precisely formulated in `List.cons_le_cons_iff`. If these hold, then `as ≤ bs` if
and only if:

- `as` is empty, or
- both `as` and `bs` are non-empty, and the head of `as` is less than the head of `bs`, or
- both `as` and `bs` are non-empty, their heads are equal, and the tail of `as` is less than or
  equal to the tail of `bs`.

def

```lean
[List.lt.{u}]](#manual-List___lt) {α : Type u} [[LT]](#manual-LT___mk) α] : [List]](#manual-List___nil) α → [List]](#manual-List___nil) α → Prop



[List.lt.{u}]](#manual-List___lt) {α : Type u} [[LT]](#manual-LT___mk) α] :
  [List]](#manual-List___nil) α → [List]](#manual-List___nil) α → Prop
```

Lexicographic ordering of lists with respect to an ordering on their elements.

`as < bs` if

- `as` is empty and `bs` is non-empty, or
- both `as` and `bs` are non-empty, and the head of `as` is less than the head of `bs`, or
- both `as` and `bs` are non-empty, their heads are equal, and the tail of `as` is less than the
  tail of `bs`.

def

```lean
[List.lex.{u}]](#manual-List___lex) {α : Type u} [[BEq]](#manual-BEq___mk) α] (l₁ l₂ : [List]](#manual-List___nil) α)
  (lt : α → α → [Bool]](#manual-Bool___false) := by exact (· < ·)) : [Bool]](#manual-Bool___false)



[List.lex.{u}]](#manual-List___lex) {α : Type u} [[BEq]](#manual-BEq___mk) α]
  (l₁ l₂ : [List]](#manual-List___nil) α)
  (lt : α → α → [Bool]](#manual-Bool___false) := by
    exact (· < ·)) :
  [Bool]](#manual-Bool___false)
```

Compares lists lexicographically with respect to a comparison on their elements.

The lexicographic order with respect to `lt` is:

- `[].[lex]](#manual-List___lex) (b :: bs)` is `[true]](#manual-Bool___false)`
- `as.lex [] = [false]](#manual-Bool___false)` is `[false]](#manual-Bool___false)`
- `(a :: as).[lex]](#manual-List___lex) (b :: bs)` is true if `lt a b` or `a == b` and `lex lt as bs` is true.

#### 20.15.3.15. Termination Helpers {#manual-The-Lean-Language-Reference--Basic-Types--Linked-Lists--API-Reference--Termination-Helpers}

def

```lean
[List.attach.{u_1}]](#manual-List___attach) {α : Type u_1} (l : [List]](#manual-List___nil) α) : [List]](#manual-List___nil) [{](https://lean-lang.org/doc/reference/latest/Basic-Types/Subtypes/#Subtype___mk) x [//](https://lean-lang.org/doc/reference/latest/Basic-Types/Subtypes/#Subtype___mk) x ∈ l [}](https://lean-lang.org/doc/reference/latest/Basic-Types/Subtypes/#Subtype___mk)



[List.attach.{u_1}]](#manual-List___attach) {α : Type u_1}
  (l : [List]](#manual-List___nil) α) : [List]](#manual-List___nil) [{](https://lean-lang.org/doc/reference/latest/Basic-Types/Subtypes/#Subtype___mk) x [//](https://lean-lang.org/doc/reference/latest/Basic-Types/Subtypes/#Subtype___mk) x ∈ l [}](https://lean-lang.org/doc/reference/latest/Basic-Types/Subtypes/#Subtype___mk)
```

“Attaches” the proof that the elements of `l` are in fact elements of `l`, producing a new list with
the same elements but in the subtype `{ x // x ∈ l }`.

`O(1)`.

This function is primarily used to allow definitions by [well-founded
recursion](https://lean-lang.org/doc/reference/4.34.0-rc1/find/?domain=Verso.Genre.Manual.section&name=well-founded-recursion) that use higher-order functions (such as
`[List.map]](#manual-List___map)`) to prove that an value taken from a list is smaller than the list. This allows the
well-founded recursion mechanism to prove that the function terminates.

def

```lean
[List.attachWith.{u_1}]](#manual-List___attachWith) {α : Type u_1} (l : [List]](#manual-List___nil) α) (P : α → Prop)
  (H : ∀ (x : α), x ∈ l → P x) : [List]](#manual-List___nil) [{](https://lean-lang.org/doc/reference/latest/Basic-Types/Subtypes/#Subtype___mk) x [//](https://lean-lang.org/doc/reference/latest/Basic-Types/Subtypes/#Subtype___mk) P x [}](https://lean-lang.org/doc/reference/latest/Basic-Types/Subtypes/#Subtype___mk)



[List.attachWith.{u_1}]](#manual-List___attachWith) {α : Type u_1}
  (l : [List]](#manual-List___nil) α) (P : α → Prop)
  (H : ∀ (x : α), x ∈ l → P x) :
  [List]](#manual-List___nil) [{](https://lean-lang.org/doc/reference/latest/Basic-Types/Subtypes/#Subtype___mk) x [//](https://lean-lang.org/doc/reference/latest/Basic-Types/Subtypes/#Subtype___mk) P x [}](https://lean-lang.org/doc/reference/latest/Basic-Types/Subtypes/#Subtype___mk)
```

“Attaches” individual proofs to a list of values that satisfy a predicate `P`, returning a list of
elements in the corresponding subtype `{ x // P x }`.

`O(1)`.

def

```lean
[List.unattach.{u_1}]](#manual-List___unattach) {α : Type u_1} {p : α → Prop}
  (l : [List]](#manual-List___nil) [{](https://lean-lang.org/doc/reference/latest/Basic-Types/Subtypes/#Subtype___mk) x [//](https://lean-lang.org/doc/reference/latest/Basic-Types/Subtypes/#Subtype___mk) p x [}](https://lean-lang.org/doc/reference/latest/Basic-Types/Subtypes/#Subtype___mk)) : [List]](#manual-List___nil) α



[List.unattach.{u_1}]](#manual-List___unattach) {α : Type u_1}
  {p : α → Prop} (l : [List]](#manual-List___nil) [{](https://lean-lang.org/doc/reference/latest/Basic-Types/Subtypes/#Subtype___mk) x [//](https://lean-lang.org/doc/reference/latest/Basic-Types/Subtypes/#Subtype___mk) p x [}](https://lean-lang.org/doc/reference/latest/Basic-Types/Subtypes/#Subtype___mk)) :
  [List]](#manual-List___nil) α
```

Maps a list of terms in a subtype to the corresponding terms in the type by forgetting that they
satisfy the predicate.

This is the inverse of `[List.attachWith]](#manual-List___attachWith)` and a synonym for `l.[map]](#manual-List___map) (·.val)`.

Mostly this should not be needed by users. It is introduced as an intermediate step by lemmas such
as `map_subtype`, and is ideally subsequently simplified away by `unattach_attach`.

This function is usually inserted automatically by Lean as an intermediate step while proving
termination. It is rarely used explicitly in code. It is introduced as an intermediate step during
the elaboration of definitions by [well-founded
recursion](https://lean-lang.org/doc/reference/4.34.0-rc1/find/?domain=Verso.Genre.Manual.section&name=well-founded-recursion). If this function is encountered in a proof
state, the right approach is usually the tactic `[simp]](#manual-simp) [List.unattach, -List.map_subtype]`.

def

```lean
[List.pmap.{u_1, u_2}]](#manual-List___pmap) {α : Type u_1} {β : Type u_2} {P : α → Prop}
  (f : (a : α) → P a → β) (l : [List]](#manual-List___nil) α) (H : ∀ (a : α), a ∈ l → P a) :
  [List]](#manual-List___nil) β



[List.pmap.{u_1, u_2}]](#manual-List___pmap) {α : Type u_1}
  {β : Type u_2} {P : α → Prop}
  (f : (a : α) → P a → β) (l : [List]](#manual-List___nil) α)
  (H : ∀ (a : α), a ∈ l → P a) : [List]](#manual-List___nil) β
```

Maps a partially defined function (defined on those terms of `α` that satisfy a predicate `P`) over
a list `l : [List]](#manual-List___nil) α`, given a proof that every element of `l` in fact satisfies `P`.

`O(|l|)`. `[List.pmap]](#manual-List___pmap)`, named for “partial map,” is the equivalent of `[List.map]](#manual-List___map)` for such partial
functions.

---



## Basic Types — 20.16. Arrays {#manual-basic-types-2016-arrays}

> 📄 Source: https://lean-lang.org/doc/reference/latest/Basic-Types/Arrays/

The `[Array]](#manual-Array___mk)` type represents sequences of elements, addressable by their position in the sequence.
Arrays are specially supported by Lean:

- They have a *logical model* that specifies their behavior in terms of lists of elements, which specifies the meaning of each operation on arrays.
- They have an optimized run-time representation in compiled code as [dynamic arrays]](#manual---tech-term-dynamic-arrays), and the Lean runtime specially optimizes array operations.
- There is [array literal syntax]](#manual-array-syntax) for writing arrays.

Arrays can be vastly more efficient than lists or other sequences in compiled code.
In part, this is because they offer good locality: because all the elements of the sequence are next to each other in memory, the processor's caches can be used efficiently.
Even more importantly, if there is only a single reference to an array, operations that might otherwise copy or allocate a data structure can be implemented via mutation.
Lean code that uses an array in such a way that there's only ever one unique reference (that is, uses it *linearly*) avoids the performance overhead of persistent data structures while still being as convenient to write, read, and prove things about as ordinary pure functional programs.

### 20.16.1. Logical Model {#manual-The-Lean-Language-Reference--Basic-Types--Arrays--Logical-Model}

structure

```lean
[Array.{u}]](#manual-Array___mk) (α : Type u) : Type u



[Array.{u}]](#manual-Array___mk) (α : Type u) : Type u
```

`[Array]](#manual-Array___mk) α` is the type of [dynamic arrays](https://en.wikipedia.org/wiki/Dynamic_array) with elements
from `α`. This type has special support in the runtime.

Arrays perform best when unshared. As long as there is never more than one reference to an array,
all updates will be performed *destructively*. This results in performance comparable to mutable
arrays in imperative programming languages.

An array has a size and a capacity. The size is the number of elements present in the array, while
the capacity is the amount of memory currently allocated for elements. The size is accessible via
`[Array.size]](#manual-Array___size)`, but the capacity is not observable from Lean code. `[Array.emptyWithCapacity]](#manual-Array___emptyWithCapacity) n` creates
an array which is equal to `#[]`, but internally allocates an array of capacity `n`. When the size
exceeds the capacity, allocation is required to grow the array.

From the point of view of proofs, `[Array]](#manual-Array___mk) α` is just a wrapper around `[List]](#manual-List___nil) α`.

Constructor

```lean
[Array.mk]](#manual-Array___mk).{u}
```

Converts a `[List]](#manual-List___nil) α` into an `[Array]](#manual-Array___mk) α`.

The function `[List.toArray]](#manual-List___toArray)` is preferred.

At runtime, this constructor is overridden by `[List.toArrayImpl]](#manual-List___toArrayImpl)` and is `O(n)` in the length of
the list.

Fields

```lean
toList : [List]](#manual-List___nil) α
```

Converts an `[Array]](#manual-Array___mk) α` into a `[List]](#manual-List___nil) α` that contains the same elements in the same order.

At runtime, this is implemented by `Array.toListImpl` and is `O(n)` in the length of the
array.

The logical model of arrays is a structure that contains a single field, which is a list of elements.
This is convenient when specifying and proving properties of array-processing functions at a low level.

### 20.16.2. Run-Time Representation {#manual-array-runtime}

Lean's arrays are *dynamic arrays*, which are blocks of continuous memory with a defined capacity, not all of which is typically in use.
As long as the number of elements in the array is less than the capacity, new items can be added to the end without reallocating or moving the data.
Adding items to an array that has no extra space results in a reallocation that doubles the capacity.
The amortized overhead scales linearly with the size of the array.
The values in the array are represented as described in the [section on the foreign function interface]](#manual-inductive-types-ffi).

m\_header






Lean object header




















m\_size






Byte countsize\_t




















m\_capacity






Allocated spacesize\_t




















m\_data






Array dataArray of lean\_object \*

Memory layout of arrays

After the object header, an array contains:

size
:   The number of objects currently stored in the array

capacity
:   The number of objects that fit in the memory allocated for the array

data
:   The values in the array

Many array functions in the Lean runtime check whether they have exclusive access to their argument by consulting the reference count in the object header.
If they do, and the array's capacity is sufficient, then the existing array can be mutated rather than allocating fresh memory.
Otherwise, a new array must be allocated.

#### 20.16.2.1. Performance Notes {#manual-array-performance}

Despite the fact that they appear to be an ordinary constructor and projection, `[Array.mk]](#manual-Array___mk)` and `Array.toList` take **time linear in the size of the array** in compiled code.
This is because converting between linked lists and packed arrays must necessarily visit each element.

Mutable arrays can be used to write very efficient code.
However, they are a poor persistent data structure.
Updating a shared array rules out mutation, and requires time linear in the size of the array.
When using arrays in performance-critical code, it's important to ensure that they are used [linearly]](#manual---tech-term-linearly).

### 20.16.3. Syntax {#manual-array-syntax}

Array literals allow arrays to be written directly in code.
They may be used in expression or pattern contexts.

syntaxArray Literals

Array literals begin with `#[` and contain a comma-separated sequence of terms, terminating with `]`.

```lean
term ::= ...
    | #[term,*]
```

**Example: Array Literals**

Array literals may be used as expressions or as patterns.

```lean
def oneTwoThree : [Array]](#manual-Array___mk) [Nat]](#manual-Nat___zero) := #[1, 2, 3]
[#eval]](#manual-Lean___Parser___Command___eval)
[match]](#manual-Lean___Parser___Term___match) [oneTwoThree]](#manual-oneTwoThree-_LPAR_in-Array-Literals_RPAR_) [with]](#manual-Lean___Parser___Term___match)
| #[x, y, z] => [some]](#manual-Option___none) ((x + z) / y)
| _ => [none]](#manual-Option___none)
```

Additionally, [sub-arrays]](#manual-subarray) may be extracted using the following syntax:

syntaxSub-Arrays

A start index followed by a colon constructs a sub-array that contains the values from the start index onwards (inclusive):

```lean
term ::= ...
    | term[term :]
```

Providing start and end indices constructs a sub-array that contains the values from the start index (inclusive) to the end index (exclusive):

```lean
term ::= ...
    | term[term : term]
```

**Example: Sub-Array Syntax**

The array `[ten]](#manual-ten-_LPAR_in-Sub-Array-Syntax_RPAR_)` contains the first ten natural numbers.

```lean
def ten : [Array]](#manual-Array___mk) [Nat]](#manual-Nat___zero) :=
[.range]](#manual-Array___range) 10
```

A sub-array that represents the second half of `[ten]](#manual-ten-_LPAR_in-Sub-Array-Syntax_RPAR_)` can be constructed using the sub-array syntax:

```lean
[#eval]](#manual-Lean___Parser___Command___eval) [ten]](#manual-ten-_LPAR_in-Sub-Array-Syntax_RPAR_)[5:]
```

```lean
#[5, 6, 7, 8, 9].toSubarray
```

Similarly, sub-array that contains two through five can be constructed by providing a stopping point:

```lean
[#eval]](#manual-Lean___Parser___Command___eval) [ten]](#manual-ten-_LPAR_in-Sub-Array-Syntax_RPAR_)[2:6]
```

```lean
#[2, 3, 4, 5].toSubarray
```

Because sub-arrays merely store the start and end indices of interest in the underlying array, the array itself can be recovered:

```lean
[#eval]](#manual-Lean___Parser___Command___eval) [ten]](#manual-ten-_LPAR_in-Sub-Array-Syntax_RPAR_)[2:6].[array]](#manual-Subarray___array) == [ten]](#manual-ten-_LPAR_in-Sub-Array-Syntax_RPAR_)
```

```lean
[true]](#manual-Bool___false)
```

### 20.16.4. API Reference {#manual-array-api}

#### 20.16.4.1. Constructing Arrays {#manual-The-Lean-Language-Reference--Basic-Types--Arrays--API-Reference--Constructing-Arrays}

def

```lean
[Array.empty.{u}]](#manual-Array___empty) {α : Type u} : [Array]](#manual-Array___mk) α



[Array.empty.{u}]](#manual-Array___empty) {α : Type u} : [Array]](#manual-Array___mk) α
```

Constructs a new empty array with initial capacity `0`.

Use `[Array.emptyWithCapacity]](#manual-Array___emptyWithCapacity)` to create an array with a greater initial capacity.

def

```lean
[Array.emptyWithCapacity.{u}]](#manual-Array___emptyWithCapacity) {α : Type u} (c : [Nat]](#manual-Nat___zero)) : [Array]](#manual-Array___mk) α



[Array.emptyWithCapacity.{u}]](#manual-Array___emptyWithCapacity) {α : Type u}
  (c : [Nat]](#manual-Nat___zero)) : [Array]](#manual-Array___mk) α
```

Constructs a new empty array with initial capacity `c`.

def

```lean
[Array.singleton.{u}]](#manual-Array___singleton) {α : Type u} (v : α) : [Array]](#manual-Array___mk) α



[Array.singleton.{u}]](#manual-Array___singleton) {α : Type u} (v : α) :
  [Array]](#manual-Array___mk) α
```

Constructs a single-element array that contains `v`.

Examples:

- `[Array.singleton]](#manual-Array___singleton) 5 = #[5]`
- `[Array.singleton]](#manual-Array___singleton) "one" = #["one"]`

def

```lean
[Array.range]](#manual-Array___range) (n : [Nat]](#manual-Nat___zero)) : [Array]](#manual-Array___mk) [Nat]](#manual-Nat___zero)



[Array.range]](#manual-Array___range) (n : [Nat]](#manual-Nat___zero)) : [Array]](#manual-Array___mk) [Nat]](#manual-Nat___zero)
```

Constructs an array that contains all the numbers from `0` to `n`, exclusive.

Examples:

- `Array.range 5 := #[0, 1, 2, 3, 4]`
- `Array.range 0 := #[]`
- `Array.range 1 := #[0]`

def

```lean
[Array.range']](#manual-Array___range___) (start size : [Nat]](#manual-Nat___zero)) (step : [Nat]](#manual-Nat___zero) := 1) : [Array]](#manual-Array___mk) [Nat]](#manual-Nat___zero)



[Array.range']](#manual-Array___range___) (start size : [Nat]](#manual-Nat___zero))
  (step : [Nat]](#manual-Nat___zero) := 1) : [Array]](#manual-Array___mk) [Nat]](#manual-Nat___zero)
```

Constructs an array of numbers of size `size`, starting at `start` and increasing by
`step` at each element.

In other words, `[Array.range']](#manual-Array___range___) start size step` is `#[start, start+step, ..., start+(len-1)*step]`.

Examples:

- `[Array.range']](#manual-Array___range___) 0 3 (step := 1) = #[0, 1, 2]`
- `[Array.range']](#manual-Array___range___) 0 3 (step := 2) = #[0, 2, 4]`
- `[Array.range']](#manual-Array___range___) 0 4 (step := 2) = #[0, 2, 4, 6]`
- `[Array.range']](#manual-Array___range___) 3 4 (step := 2) = #[3, 5, 7, 9]`

def

```lean
[Array.finRange]](#manual-Array___finRange) (n : [Nat]](#manual-Nat___zero)) : [Array]](#manual-Array___mk) ([Fin]](#manual-Fin___mk) n)



[Array.finRange]](#manual-Array___finRange) (n : [Nat]](#manual-Nat___zero)) : [Array]](#manual-Array___mk) ([Fin]](#manual-Fin___mk) n)
```

Returns an array of all elements of `[Fin]](#manual-Fin___mk) n` in order, starting at `0`.

Examples:

- `[Array.finRange]](#manual-Array___finRange) 0 = (#[] : [Array]](#manual-Array___mk) ([Fin]](#manual-Fin___mk) 0))`
- `[Array.finRange]](#manual-Array___finRange) 2 = (#[0, 1] : [Array]](#manual-Array___mk) ([Fin]](#manual-Fin___mk) 2))`

def

```lean
[Array.ofFn.{u}]](#manual-Array___ofFn) {α : Type u} {n : [Nat]](#manual-Nat___zero)} (f : [Fin]](#manual-Fin___mk) n → α) : [Array]](#manual-Array___mk) α



[Array.ofFn.{u}]](#manual-Array___ofFn) {α : Type u} {n : [Nat]](#manual-Nat___zero)}
  (f : [Fin]](#manual-Fin___mk) n → α) : [Array]](#manual-Array___mk) α
```

Creates an array by applying `f` to each potential index in order, starting at `0`.

Examples:

- `[Array.ofFn]](#manual-Array___ofFn) (n := 3) toString = #["0", "1", "2"]`
- `[Array.ofFn]](#manual-Array___ofFn) (fun i => #["red", "green", "blue"].get i.val i.isLt) = #["red", "green", "blue"]`

def

```lean
[Array.replicate.{u}]](#manual-Array___replicate) {α : Type u} (n : [Nat]](#manual-Nat___zero)) (v : α) : [Array]](#manual-Array___mk) α



[Array.replicate.{u}]](#manual-Array___replicate) {α : Type u} (n : [Nat]](#manual-Nat___zero))
  (v : α) : [Array]](#manual-Array___mk) α
```

Creates an array that contains `n` repetitions of `v`.

The corresponding `[List]](#manual-List___nil)` function is `[List.replicate]](#manual-List___replicate)`.

Examples:

- `[Array.replicate]](#manual-Array___replicate) 2 [true]](#manual-Bool___false) = #[[true]](#manual-Bool___false), [true]](#manual-Bool___false)]`
- `[Array.replicate]](#manual-Array___replicate) 3 () = #[(), (), ()]`
- `[Array.replicate]](#manual-Array___replicate) 0 "anything" = #[]`

def

```lean
[Array.append.{u}]](#manual-Array___append) {α : Type u} (as bs : [Array]](#manual-Array___mk) α) : [Array]](#manual-Array___mk) α



[Array.append.{u}]](#manual-Array___append) {α : Type u}
  (as bs : [Array]](#manual-Array___mk) α) : [Array]](#manual-Array___mk) α
```

Appends two arrays. Normally used via the `++` operator.

Appending arrays takes time proportional to the length of the second array.

Examples:

- `#[1, 2, 3] ++ #[4, 5] = #[1, 2, 3, 4, 5]`.
- `#[] ++ #[4, 5] = #[4, 5]`.
- `#[1, 2, 3] ++ #[] = #[1, 2, 3]`.

def

```lean
[Array.appendList.{u}]](#manual-Array___appendList) {α : Type u} (as : [Array]](#manual-Array___mk) α) (bs : [List]](#manual-List___nil) α) : [Array]](#manual-Array___mk) α



[Array.appendList.{u}]](#manual-Array___appendList) {α : Type u}
  (as : [Array]](#manual-Array___mk) α) (bs : [List]](#manual-List___nil) α) : [Array]](#manual-Array___mk) α
```

Appends an array and a list.

Takes time proportional to the length of the list..

Examples:

- `#[1, 2, 3].[appendList]](#manual-Array___appendList) [4, 5] = #[1, 2, 3, 4, 5]`.
- `#[].[appendList]](#manual-Array___appendList) [4, 5] = #[4, 5]`.
- `#[1, 2, 3].[appendList]](#manual-Array___appendList) [] = #[1, 2, 3]`.

def

```lean
[Array.leftpad.{u}]](#manual-Array___leftpad) {α : Type u} (n : [Nat]](#manual-Nat___zero)) (a : α) (xs : [Array]](#manual-Array___mk) α) :
  [Array]](#manual-Array___mk) α



[Array.leftpad.{u}]](#manual-Array___leftpad) {α : Type u} (n : [Nat]](#manual-Nat___zero))
  (a : α) (xs : [Array]](#manual-Array___mk) α) : [Array]](#manual-Array___mk) α
```

Pads `xs : [Array]](#manual-Array___mk) α` on the left with repeated occurrences of `a : α` until it is of size `n`. If `xs`
already has at least `n` elements, it is returned unmodified.

Examples:

- `#[1, 2, 3].[leftpad]](#manual-Array___leftpad) 5 0 = #[0, 0, 1, 2, 3]`
- `#["red", "green", "blue"].[leftpad]](#manual-Array___leftpad) 4 "blank" = #["blank", "red", "green", "blue"]`
- `#["red", "green", "blue"].[leftpad]](#manual-Array___leftpad) 3 "blank" = #["red", "green", "blue"]`
- `#["red", "green", "blue"].[leftpad]](#manual-Array___leftpad) 1 "blank" = #["red", "green", "blue"]`

def

```lean
[Array.rightpad.{u}]](#manual-Array___rightpad) {α : Type u} (n : [Nat]](#manual-Nat___zero)) (a : α) (xs : [Array]](#manual-Array___mk) α) :
  [Array]](#manual-Array___mk) α



[Array.rightpad.{u}]](#manual-Array___rightpad) {α : Type u} (n : [Nat]](#manual-Nat___zero))
  (a : α) (xs : [Array]](#manual-Array___mk) α) : [Array]](#manual-Array___mk) α
```

Pads `xs : [Array]](#manual-Array___mk) α` on the right with repeated occurrences of `a : α` until it is of length `n`. If
`l` already has at least `n` elements, it is returned unmodified.

Examples:

- `#[1, 2, 3].[rightpad]](#manual-Array___rightpad) 5 0 = #[1, 2, 3, 0, 0]`
- `#["red", "green", "blue"].[rightpad]](#manual-Array___rightpad) 4 "blank" = #["red", "green", "blue", "blank"]`
- `#["red", "green", "blue"].[rightpad]](#manual-Array___rightpad) 3 "blank" = #["red", "green", "blue"]`
- `#["red", "green", "blue"].[rightpad]](#manual-Array___rightpad) 1 "blank" = #["red", "green", "blue"]`

#### 20.16.4.2. Size {#manual-The-Lean-Language-Reference--Basic-Types--Arrays--API-Reference--Size}

def

```lean
[Array.size.{u}]](#manual-Array___size) {α : Type u} (a : [Array]](#manual-Array___mk) α) : [Nat]](#manual-Nat___zero)



[Array.size.{u}]](#manual-Array___size) {α : Type u}
  (a : [Array]](#manual-Array___mk) α) : [Nat]](#manual-Nat___zero)
```

Gets the number of elements stored in an array.

This is a cached value, so it is `O(1)` to access. The space allocated for an array, referred to as
its *capacity*, is at least as large as its size, but may be larger. The capacity of an array is an
internal detail that's not observable by Lean code.

def

```lean
[Array.usize.{u}]](#manual-Array___usize) {α : Type u} (xs : [Array]](#manual-Array___mk) α) : [USize]](#manual-USize___ofBitVec)



[Array.usize.{u}]](#manual-Array___usize) {α : Type u}
  (xs : [Array]](#manual-Array___mk) α) : [USize]](#manual-USize___ofBitVec)
```

Returns the size of the array as a platform-native unsigned integer.

This is a low-level version of `[Array.size]](#manual-Array___size)` that directly queries the runtime system's
representation of arrays. While this is not provable, `[Array.usize]](#manual-Array___usize)` always returns the exact size of
the array since the implementation only supports arrays of size less than `[USize.size]](#manual-USize___size)`.

def

```lean
[Array.isEmpty.{u}]](#manual-Array___isEmpty) {α : Type u} (xs : [Array]](#manual-Array___mk) α) : [Bool]](#manual-Bool___false)



[Array.isEmpty.{u}]](#manual-Array___isEmpty) {α : Type u}
  (xs : [Array]](#manual-Array___mk) α) : [Bool]](#manual-Bool___false)
```

Checks whether an array is empty.

An array is empty if its size is `0`.

Examples:

- `(#[] : [Array]](#manual-Array___mk) [String]](#manual-String___ofByteArray)).[isEmpty]](#manual-Array___isEmpty) = [true]](#manual-Bool___false)`
- `#[1, 2].[isEmpty]](#manual-Array___isEmpty) = [false]](#manual-Bool___false)`
- `#[()].[isEmpty]](#manual-Array___isEmpty) = [false]](#manual-Bool___false)`

#### 20.16.4.3. Lookups {#manual-The-Lean-Language-Reference--Basic-Types--Arrays--API-Reference--Lookups}

def

```lean
[Array.extract.{u_1}]](#manual-Array___extract) {α : Type u_1} (as : [Array]](#manual-Array___mk) α) (start : [Nat]](#manual-Nat___zero) := 0)
  (stop : [Nat]](#manual-Nat___zero) := as.[size]](#manual-Array___size)) : [Array]](#manual-Array___mk) α



[Array.extract.{u_1}]](#manual-Array___extract) {α : Type u_1}
  (as : [Array]](#manual-Array___mk) α) (start : [Nat]](#manual-Nat___zero) := 0)
  (stop : [Nat]](#manual-Nat___zero) := as.[size]](#manual-Array___size)) : [Array]](#manual-Array___mk) α
```

Returns the slice of `as` from indices `start` to `stop` (exclusive). The resulting array has size
`([min]](#manual-Min___mk) stop as.[size]](#manual-Array___size)) - start`.

If `start` is greater or equal to `stop`, the result is empty. If `stop` is greater than the size of
`as`, the size is used instead.

Examples:

- `#[0, 1, 2, 3, 4].[extract]](#manual-Array___extract) 1 3 = #[1, 2]`
- `#[0, 1, 2, 3, 4].[extract]](#manual-Array___extract) 1 30 = #[1, 2, 3, 4]`
- `#[0, 1, 2, 3, 4].[extract]](#manual-Array___extract) 0 0 = #[]`
- `#[0, 1, 2, 3, 4].[extract]](#manual-Array___extract) 2 1 = #[]`
- `#[0, 1, 2, 3, 4].[extract]](#manual-Array___extract) 2 2 = #[]`
- `#[0, 1, 2, 3, 4].[extract]](#manual-Array___extract) 2 3 = #[2]`
- `#[0, 1, 2, 3, 4].[extract]](#manual-Array___extract) 2 4 = #[2, 3]`

def

```lean
[Array.getD.{u_1}]](#manual-Array___getD) {α : Type u_1} (a : [Array]](#manual-Array___mk) α) (i : [Nat]](#manual-Nat___zero)) (v₀ : α) : α



[Array.getD.{u_1}]](#manual-Array___getD) {α : Type u_1}
  (a : [Array]](#manual-Array___mk) α) (i : [Nat]](#manual-Nat___zero)) (v₀ : α) : α
```

Returns the element at the provided index, counting from `0`. Returns the fallback value `v₀` if the
index is out of bounds.

To return an `[Option]](#manual-Option___none)` depending on whether the index is in bounds, use `a[i]?`. To panic if the
index is out of bounds, use `a[i]!`.

Examples:

- `#["spring", "summer", "fall", "winter"].[getD]](#manual-Array___getD) 2 "never" = "fall"`
- `#["spring", "summer", "fall", "winter"].[getD]](#manual-Array___getD) 0 "never" = "spring"`
- `#["spring", "summer", "fall", "winter"].[getD]](#manual-Array___getD) 4 "never" = "never"`

def

```lean
[Array.uget.{u}]](#manual-Array___uget) {α : Type u} (xs : [Array]](#manual-Array___mk) α) (i : [USize]](#manual-USize___ofBitVec))
  (h : i.[toNat]](#manual-USize___toNat) [<]](#manual-LT___mk) xs.[size]](#manual-Array___size)) : α



[Array.uget.{u}]](#manual-Array___uget) {α : Type u} (xs : [Array]](#manual-Array___mk) α)
  (i : [USize]](#manual-USize___ofBitVec)) (h : i.[toNat]](#manual-USize___toNat) [<]](#manual-LT___mk) xs.[size]](#manual-Array___size)) : α
```

Low-level indexing operator which is as fast as a C array read.

This avoids overhead due to unboxing a `[Nat]](#manual-Nat___zero)` used as an index.

def

```lean
[Array.back.{u}]](#manual-Array___back) {α : Type u} (xs : [Array]](#manual-Array___mk) α)
  (h : 0 [<]](#manual-LT___mk) xs.[size]](#manual-Array___size) := by get_elem_tactic) : α



[Array.back.{u}]](#manual-Array___back) {α : Type u} (xs : [Array]](#manual-Array___mk) α)
  (h : 0 [<]](#manual-LT___mk) xs.[size]](#manual-Array___size) := by
    get_elem_tactic) :
  α
```

Returns the last element of an array, given a proof that the array is not empty.

See `[Array.back!]](#manual-Array___back___-next)` for the version that panics if the array is empty, or `[Array.back?]](#manual-Array___back___)` for the
version that returns an option.

def

```lean
[Array.back?.{u}]](#manual-Array___back___) {α : Type u} (xs : [Array]](#manual-Array___mk) α) : [Option]](#manual-Option___none) α



[Array.back?.{u}]](#manual-Array___back___) {α : Type u}
  (xs : [Array]](#manual-Array___mk) α) : [Option]](#manual-Option___none) α
```

Returns the last element of an array, or `[none]](#manual-Option___none)` if the array is empty.

See `[Array.back!]](#manual-Array___back___-next)` for the version that panics if the array is empty, or `[Array.back]](#manual-Array___back)` for the version
that requires a proof the array is non-empty.

def

```lean
[Array.back!.{u}]](#manual-Array___back___-next) {α : Type u} [[Inhabited]](#manual-Inhabited___mk) α] (xs : [Array]](#manual-Array___mk) α) : α



[Array.back!.{u}]](#manual-Array___back___-next) {α : Type u} [[Inhabited]](#manual-Inhabited___mk) α]
  (xs : [Array]](#manual-Array___mk) α) : α
```

Returns the last element of an array, or panics if the array is empty.

Safer alternatives include `[Array.back]](#manual-Array___back)`, which requires a proof the array is non-empty, and
`[Array.back?]](#manual-Array___back___)`, which returns an `[Option]](#manual-Option___none)`.

def

```lean
[Array.getMax?.{u}]](#manual-Array___getMax___) {α : Type u} (as : [Array]](#manual-Array___mk) α) (lt : α → α → [Bool]](#manual-Bool___false)) :
  [Option]](#manual-Option___none) α



[Array.getMax?.{u}]](#manual-Array___getMax___) {α : Type u}
  (as : [Array]](#manual-Array___mk) α) (lt : α → α → [Bool]](#manual-Bool___false)) :
  [Option]](#manual-Option___none) α
```

Returns the largest element of the array, as determined by the comparison `lt`, or `[none]](#manual-Option___none)` if
the array is empty.

Examples:

- `(#[] : [Array]](#manual-Array___mk) [Nat]](#manual-Nat___zero)).[getMax?]](#manual-Array___getMax___) (· < ·) = [none]](#manual-Option___none)`
- `#["red", "green", "blue"].[getMax?]](#manual-Array___getMax___) (·.[length]](#manual-String___length) < ·.[length]](#manual-String___length)) = [some]](#manual-Option___none) "green"`
- `#["red", "green", "blue"].[getMax?]](#manual-Array___getMax___) (· < ·) = [some]](#manual-Option___none) "red"`

#### 20.16.4.4. Queries {#manual-The-Lean-Language-Reference--Basic-Types--Arrays--API-Reference--Queries}

def

```lean
[Array.count.{u}]](#manual-Array___count) {α : Type u} [[BEq]](#manual-BEq___mk) α] (a : α) (as : [Array]](#manual-Array___mk) α) : [Nat]](#manual-Nat___zero)



[Array.count.{u}]](#manual-Array___count) {α : Type u} [[BEq]](#manual-BEq___mk) α]
  (a : α) (as : [Array]](#manual-Array___mk) α) : [Nat]](#manual-Nat___zero)
```

Counts the number of times an element occurs in an array.

Examples:

- `#[1, 1, 2, 3, 5].[count]](#manual-Array___count) 1 = 2`
- `#[1, 1, 2, 3, 5].[count]](#manual-Array___count) 5 = 1`
- `#[1, 1, 2, 3, 5].[count]](#manual-Array___count) 4 = 0`

def

```lean
[Array.countP.{u}]](#manual-Array___countP) {α : Type u} (p : α → [Bool]](#manual-Bool___false)) (as : [Array]](#manual-Array___mk) α) : [Nat]](#manual-Nat___zero)



[Array.countP.{u}]](#manual-Array___countP) {α : Type u}
  (p : α → [Bool]](#manual-Bool___false)) (as : [Array]](#manual-Array___mk) α) : [Nat]](#manual-Nat___zero)
```

Counts the number of elements in the array `as` that satisfy the Boolean predicate `p`.

Examples:

- `#[1, 2, 3, 4, 5].[countP]](#manual-Array___countP) (· % 2 == 0) = 2`
- `#[1, 2, 3, 4, 5].[countP]](#manual-Array___countP) (· < 5) = 4`
- `#[1, 2, 3, 4, 5].[countP]](#manual-Array___countP) (· > 5) = 0`

def

```lean
[Array.idxOf.{u}]](#manual-Array___idxOf) {α : Type u} [[BEq]](#manual-BEq___mk) α] (a : α) : [Array]](#manual-Array___mk) α → [Nat]](#manual-Nat___zero)



[Array.idxOf.{u}]](#manual-Array___idxOf) {α : Type u} [[BEq]](#manual-BEq___mk) α]
  (a : α) : [Array]](#manual-Array___mk) α → [Nat]](#manual-Nat___zero)
```

Returns the index of the first element equal to `a`, or the size of the array if no element is equal
to `a`.

Examples:

- `#["carrot", "potato", "broccoli"].[idxOf]](#manual-Array___idxOf) "carrot" = 0`
- `#["carrot", "potato", "broccoli"].[idxOf]](#manual-Array___idxOf) "broccoli" = 2`
- `#["carrot", "potato", "broccoli"].[idxOf]](#manual-Array___idxOf) "tomato" = 3`
- `#["carrot", "potato", "broccoli"].[idxOf]](#manual-Array___idxOf) "anything else" = 3`

def

```lean
[Array.idxOf?.{u}]](#manual-Array___idxOf___) {α : Type u} [[BEq]](#manual-BEq___mk) α] (xs : [Array]](#manual-Array___mk) α) (v : α) :
  [Option]](#manual-Option___none) [Nat]](#manual-Nat___zero)



[Array.idxOf?.{u}]](#manual-Array___idxOf___) {α : Type u} [[BEq]](#manual-BEq___mk) α]
  (xs : [Array]](#manual-Array___mk) α) (v : α) : [Option]](#manual-Option___none) [Nat]](#manual-Nat___zero)
```

Returns the index of the first element equal to `a`, or `[none]](#manual-Option___none)` if no element is equal to `a`.

Examples:

- `#["carrot", "potato", "broccoli"].[idxOf?]](#manual-Array___idxOf___) "carrot" = [some]](#manual-Option___none) 0`
- `#["carrot", "potato", "broccoli"].[idxOf?]](#manual-Array___idxOf___) "broccoli" = [some]](#manual-Option___none) 2`
- `#["carrot", "potato", "broccoli"].[idxOf?]](#manual-Array___idxOf___) "tomato" = [none]](#manual-Option___none)`
- `#["carrot", "potato", "broccoli"].[idxOf?]](#manual-Array___idxOf___) "anything else" = [none]](#manual-Option___none)`

def

```lean
[Array.finIdxOf?.{u}]](#manual-Array___finIdxOf___) {α : Type u} [[BEq]](#manual-BEq___mk) α] (xs : [Array]](#manual-Array___mk) α) (v : α) :
  [Option]](#manual-Option___none) ([Fin]](#manual-Fin___mk) xs.[size]](#manual-Array___size))



[Array.finIdxOf?.{u}]](#manual-Array___finIdxOf___) {α : Type u} [[BEq]](#manual-BEq___mk) α]
  (xs : [Array]](#manual-Array___mk) α) (v : α) :
  [Option]](#manual-Option___none) ([Fin]](#manual-Fin___mk) xs.[size]](#manual-Array___size))
```

Returns the index of the first element equal to `a`, or `[none]](#manual-Option___none)` if no element is equal
to `a`. The index is returned as a `[Fin]](#manual-Fin___mk)`, which guarantees that it is in bounds.

Examples:

- `#["carrot", "potato", "broccoli"].[finIdxOf?]](#manual-Array___finIdxOf___) "carrot" = [some]](#manual-Option___none) 0`
- `#["carrot", "potato", "broccoli"].[finIdxOf?]](#manual-Array___finIdxOf___) "broccoli" = [some]](#manual-Option___none) 2`
- `#["carrot", "potato", "broccoli"].[finIdxOf?]](#manual-Array___finIdxOf___) "tomato" = [none]](#manual-Option___none)`
- `#["carrot", "potato", "broccoli"].[finIdxOf?]](#manual-Array___finIdxOf___) "anything else" = [none]](#manual-Option___none)`

#### 20.16.4.5. Conversions {#manual-The-Lean-Language-Reference--Basic-Types--Arrays--API-Reference--Conversions}

def

```lean
Array.toList.{u} {α : Type u} (self : [Array]](#manual-Array___mk) α) : [List]](#manual-List___nil) α



Array.toList.{u} {α : Type u}
  (self : [Array]](#manual-Array___mk) α) : [List]](#manual-List___nil) α
```

Converts an `[Array]](#manual-Array___mk) α` into a `[List]](#manual-List___nil) α` that contains the same elements in the same order.

At runtime, this is implemented by `Array.toListImpl` and is `O(n)` in the length of the
array.

def

```lean
[Array.toListRev.{u_1}]](#manual-Array___toListRev) {α : Type u_1} (xs : [Array]](#manual-Array___mk) α) : [List]](#manual-List___nil) α



[Array.toListRev.{u_1}]](#manual-Array___toListRev) {α : Type u_1}
  (xs : [Array]](#manual-Array___mk) α) : [List]](#manual-List___nil) α
```

Converts an array to a list that contains the same elements in the opposite order.

This is equivalent to, but more efficient than, `Array.toList ∘ List.reverse`.

Examples:

- `#[1, 2, 3].[toListRev]](#manual-Array___toListRev) = [3, 2, 1]`
- `#["blue", "yellow"].[toListRev]](#manual-Array___toListRev) = ["yellow", "blue"]`

def

```lean
[Array.toListAppend.{u}]](#manual-Array___toListAppend) {α : Type u} (as : [Array]](#manual-Array___mk) α) (l : [List]](#manual-List___nil) α) : [List]](#manual-List___nil) α



[Array.toListAppend.{u}]](#manual-Array___toListAppend) {α : Type u}
  (as : [Array]](#manual-Array___mk) α) (l : [List]](#manual-List___nil) α) : [List]](#manual-List___nil) α
```

Prepends an array to a list. The elements of the array are at the beginning of the resulting list.

Equivalent to `as.toList ++ l`.

Examples:

- `#[1, 2].[toListAppend]](#manual-Array___toListAppend) [3, 4] = [1, 2, 3, 4]`
- `#[1, 2].[toListAppend]](#manual-Array___toListAppend) [] = [1, 2]`
- `#[].[toListAppend]](#manual-Array___toListAppend) [3, 4, 5] = [3, 4, 5]`

def

```lean
[Array.toVector.{u_1}]](#manual-Array___toVector) {α : Type u_1} (xs : [Array]](#manual-Array___mk) α) : Vector α xs.[size]](#manual-Array___size)



[Array.toVector.{u_1}]](#manual-Array___toVector) {α : Type u_1}
  (xs : [Array]](#manual-Array___mk) α) : Vector α xs.[size]](#manual-Array___size)
```

Converts an array to a vector. The resulting vector's size is the array's size.

def

```lean
[Array.toSubarray.{u}]](#manual-Array___toSubarray) {α : Type u} (as : [Array]](#manual-Array___mk) α) (start : [Nat]](#manual-Nat___zero) := 0)
  (stop : [Nat]](#manual-Nat___zero) := as.[size]](#manual-Array___size)) : [Subarray]](#manual-Subarray) α



[Array.toSubarray.{u}]](#manual-Array___toSubarray) {α : Type u}
  (as : [Array]](#manual-Array___mk) α) (start : [Nat]](#manual-Nat___zero) := 0)
  (stop : [Nat]](#manual-Nat___zero) := as.[size]](#manual-Array___size)) : [Subarray]](#manual-Subarray) α
```

Returns a subarray of an array, with the given bounds.

If `start` or `stop` are not valid bounds for a subarray, then they are clamped to array's size.
Additionally, the starting index is clamped to the ending index.

def

```lean
[Array.ofSubarray.{u}]](#manual-Array___ofSubarray) {α : Type u} (s : [Subarray]](#manual-Subarray) α) : [Array]](#manual-Array___mk) α



[Array.ofSubarray.{u}]](#manual-Array___ofSubarray) {α : Type u}
  (s : [Subarray]](#manual-Subarray) α) : [Array]](#manual-Array___mk) α
```

Allocates a new array that contains the contents of the subarray.

#### 20.16.4.6. Modification {#manual-The-Lean-Language-Reference--Basic-Types--Arrays--API-Reference--Modification}

def

```lean
[Array.push.{u}]](#manual-Array___push) {α : Type u} (a : [Array]](#manual-Array___mk) α) (v : α) : [Array]](#manual-Array___mk) α



[Array.push.{u}]](#manual-Array___push) {α : Type u} (a : [Array]](#manual-Array___mk) α)
  (v : α) : [Array]](#manual-Array___mk) α
```

Adds an element to the end of an array. The resulting array's size is one greater than the input
array. If there are no other references to the array, then it is modified in-place.

This takes amortized `O(1)` time because `[Array]](#manual-Array___mk) α` is represented by a dynamic array.

Examples:

- `#[].[push]](#manual-Array___push) "apple" = #["apple"]`
- `#["apple"].[push]](#manual-Array___push) "orange" = #["apple", "orange"]`

def

```lean
[Array.pop.{u}]](#manual-Array___pop) {α : Type u} (xs : [Array]](#manual-Array___mk) α) : [Array]](#manual-Array___mk) α



[Array.pop.{u}]](#manual-Array___pop) {α : Type u}
  (xs : [Array]](#manual-Array___mk) α) : [Array]](#manual-Array___mk) α
```

Removes the last element of an array. If the array is empty, then it is returned unmodified. The
modification is performed in-place when the reference to the array is unique.

Examples:

- `#[1, 2, 3].[pop]](#manual-Array___pop) = #[1, 2]`
- `#["orange", "yellow"].[pop]](#manual-Array___pop) = #["orange"]`
- `(#[] : [Array]](#manual-Array___mk) [String]](#manual-String___ofByteArray)).[pop]](#manual-Array___pop) = #[]`

def

```lean
[Array.popWhile.{u}]](#manual-Array___popWhile) {α : Type u} (p : α → [Bool]](#manual-Bool___false)) (as : [Array]](#manual-Array___mk) α) : [Array]](#manual-Array___mk) α



[Array.popWhile.{u}]](#manual-Array___popWhile) {α : Type u}
  (p : α → [Bool]](#manual-Bool___false)) (as : [Array]](#manual-Array___mk) α) : [Array]](#manual-Array___mk) α
```

Removes all the elements that satisfy a predicate from the end of an array.

The longest contiguous sequence of elements that all satisfy the predicate is removed.

Examples:

- `#[0, 1, 2, 3, 4].[popWhile]](#manual-Array___popWhile) (· > 2) = #[0, 1, 2]`
- `#[3, 2, 3, 4].[popWhile]](#manual-Array___popWhile) (· > 2) = #[3, 2]`
- `(#[] : [Array]](#manual-Array___mk) [Nat]](#manual-Nat___zero)).[popWhile]](#manual-Array___popWhile) (· > 2) = #[]`

def

```lean
[Array.erase.{u}]](#manual-Array___erase) {α : Type u} [[BEq]](#manual-BEq___mk) α] (as : [Array]](#manual-Array___mk) α) (a : α) : [Array]](#manual-Array___mk) α



[Array.erase.{u}]](#manual-Array___erase) {α : Type u} [[BEq]](#manual-BEq___mk) α]
  (as : [Array]](#manual-Array___mk) α) (a : α) : [Array]](#manual-Array___mk) α
```

Removes the first occurrence of a specified element from an array, or does nothing if it is not
present.

This function takes worst-case `O(n)` time because it back-shifts all later elements.

Examples:

- `#[1, 2, 3].[erase]](#manual-Array___erase) 2 = #[1, 3]`
- `#[1, 2, 3].[erase]](#manual-Array___erase) 5 = #[1, 2, 3]`
- `#[1, 2, 3, 2, 1].[erase]](#manual-Array___erase) 2 = #[1, 3, 2, 1]`
- `(#[] : List Nat).erase 2 = #[]`

def

```lean
[Array.eraseP.{u}]](#manual-Array___eraseP) {α : Type u} (as : [Array]](#manual-Array___mk) α) (p : α → [Bool]](#manual-Bool___false)) : [Array]](#manual-Array___mk) α



[Array.eraseP.{u}]](#manual-Array___eraseP) {α : Type u}
  (as : [Array]](#manual-Array___mk) α) (p : α → [Bool]](#manual-Bool___false)) : [Array]](#manual-Array___mk) α
```

Removes the first element that satisfies the predicate `p`. If no element satisfies `p`, the array
is returned unmodified.

This function takes worst-case `O(n)` time because it back-shifts all later elements.

Examples:

- `#["red", "green", "", "blue"].[eraseP]](#manual-Array___eraseP) (·.[isEmpty]](#manual-String___isEmpty)) = #["red", "green", "blue"]`
- `#["red", "green", "", "blue", ""].[eraseP]](#manual-Array___eraseP) (·.[isEmpty]](#manual-String___isEmpty)) = #["red", "green", "blue", ""]`
- `#["red", "green", "blue"].[eraseP]](#manual-Array___eraseP) (·.[length]](#manual-String___length) % 2 == 0) = #["red", "green"]`
- `#["red", "green", "blue"].[eraseP]](#manual-Array___eraseP) (fun _ => [true]](#manual-Bool___false)) = #["green", "blue"]`
- `(#[] : [Array]](#manual-Array___mk) [String]](#manual-String___ofByteArray)).[eraseP]](#manual-Array___eraseP) (fun _ => [true]](#manual-Bool___false)) = #[]`

def

```lean
[Array.eraseIdx.{u}]](#manual-Array___eraseIdx) {α : Type u} (xs : [Array]](#manual-Array___mk) α) (i : [Nat]](#manual-Nat___zero))
  (h : i [<]](#manual-LT___mk) xs.[size]](#manual-Array___size) := by get_elem_tactic) : [Array]](#manual-Array___mk) α



[Array.eraseIdx.{u}]](#manual-Array___eraseIdx) {α : Type u}
  (xs : [Array]](#manual-Array___mk) α) (i : [Nat]](#manual-Nat___zero))
  (h : i [<]](#manual-LT___mk) xs.[size]](#manual-Array___size) := by
    get_elem_tactic) :
  [Array]](#manual-Array___mk) α
```

Removes the element at a given index from an array without a run-time bounds check.

This function takes worst-case `O(n)` time because it back-shifts all elements at positions
greater than `i`.

Examples:

- `#["apple", "pear", "orange"].[eraseIdx]](#manual-Array___eraseIdx) 0 = #["pear", "orange"]`
- `#["apple", "pear", "orange"].[eraseIdx]](#manual-Array___eraseIdx) 1 = #["apple", "orange"]`
- `#["apple", "pear", "orange"].[eraseIdx]](#manual-Array___eraseIdx) 2 = #["apple", "pear"]`

def

```lean
[Array.eraseIdx!.{u}]](#manual-Array___eraseIdx___) {α : Type u} (xs : [Array]](#manual-Array___mk) α) (i : [Nat]](#manual-Nat___zero)) : [Array]](#manual-Array___mk) α



[Array.eraseIdx!.{u}]](#manual-Array___eraseIdx___) {α : Type u}
  (xs : [Array]](#manual-Array___mk) α) (i : [Nat]](#manual-Nat___zero)) : [Array]](#manual-Array___mk) α
```

Removes the element at a given index from an array. Panics if the index is out of bounds.

This function takes worst-case `O(n)` time because it back-shifts all elements at positions
greater than `i`.

def

```lean
[Array.eraseIdxIfInBounds.{u}]](#manual-Array___eraseIdxIfInBounds) {α : Type u} (xs : [Array]](#manual-Array___mk) α) (i : [Nat]](#manual-Nat___zero)) :
  [Array]](#manual-Array___mk) α



[Array.eraseIdxIfInBounds.{u}]](#manual-Array___eraseIdxIfInBounds) {α : Type u}
  (xs : [Array]](#manual-Array___mk) α) (i : [Nat]](#manual-Nat___zero)) : [Array]](#manual-Array___mk) α
```

Removes the element at a given index from an array. Does nothing if the index is out of bounds.

This function takes worst-case `O(n)` time because it back-shifts all elements at positions greater
than `i`.

Examples:

- `#["apple", "pear", "orange"].[eraseIdxIfInBounds]](#manual-Array___eraseIdxIfInBounds) 0 = #["pear", "orange"]`
- `#["apple", "pear", "orange"].[eraseIdxIfInBounds]](#manual-Array___eraseIdxIfInBounds) 1 = #["apple", "orange"]`
- `#["apple", "pear", "orange"].[eraseIdxIfInBounds]](#manual-Array___eraseIdxIfInBounds) 2 = #["apple", "pear"]`
- `#["apple", "pear", "orange"].[eraseIdxIfInBounds]](#manual-Array___eraseIdxIfInBounds) 3 = #["apple", "pear", "orange"]`
- `#["apple", "pear", "orange"].[eraseIdxIfInBounds]](#manual-Array___eraseIdxIfInBounds) 5 = #["apple", "pear", "orange"]`

def

```lean
[Array.eraseReps.{u_1}]](#manual-Array___eraseReps) {α : Type u_1} [[BEq]](#manual-BEq___mk) α] (as : [Array]](#manual-Array___mk) α) : [Array]](#manual-Array___mk) α



[Array.eraseReps.{u_1}]](#manual-Array___eraseReps) {α : Type u_1}
  [[BEq]](#manual-BEq___mk) α] (as : [Array]](#manual-Array___mk) α) : [Array]](#manual-Array___mk) α
```

Erases repeated elements, keeping the first element of each run.

`O(|as|)`.

Example:

- `#[1, 3, 2, 2, 2, 3, 3, 5].[eraseReps]](#manual-Array___eraseReps) = #[1, 3, 2, 3, 5]`

def

```lean
[Array.swap.{u}]](#manual-Array___swap) {α : Type u} (xs : [Array]](#manual-Array___mk) α) (i j : [Nat]](#manual-Nat___zero))
  (hi : i [<]](#manual-LT___mk) xs.[size]](#manual-Array___size) := by get_elem_tactic)
  (hj : j [<]](#manual-LT___mk) xs.[size]](#manual-Array___size) := by get_elem_tactic) : [Array]](#manual-Array___mk) α



[Array.swap.{u}]](#manual-Array___swap) {α : Type u} (xs : [Array]](#manual-Array___mk) α)
  (i j : [Nat]](#manual-Nat___zero))
  (hi : i [<]](#manual-LT___mk) xs.[size]](#manual-Array___size) := by get_elem_tactic)
  (hj : j [<]](#manual-LT___mk) xs.[size]](#manual-Array___size) := by
    get_elem_tactic) :
  [Array]](#manual-Array___mk) α
```

Swaps two elements of an array. The modification is performed in-place when the reference to the
array is unique.

Examples:

- `#["red", "green", "blue", "brown"].[swap]](#manual-Array___swap) 0 3 = #["brown", "green", "blue", "red"]`
- `#["red", "green", "blue", "brown"].[swap]](#manual-Array___swap) 0 2 = #["blue", "green", "red", "brown"]`
- `#["red", "green", "blue", "brown"].[swap]](#manual-Array___swap) 1 2 = #["red", "blue", "green", "brown"]`
- `#["red", "green", "blue", "brown"].[swap]](#manual-Array___swap) 3 0 = #["brown", "green", "blue", "red"]`

def

```lean
[Array.swapIfInBounds.{u}]](#manual-Array___swapIfInBounds) {α : Type u} (xs : [Array]](#manual-Array___mk) α) (i j : [Nat]](#manual-Nat___zero)) :
  [Array]](#manual-Array___mk) α



[Array.swapIfInBounds.{u}]](#manual-Array___swapIfInBounds) {α : Type u}
  (xs : [Array]](#manual-Array___mk) α) (i j : [Nat]](#manual-Nat___zero)) : [Array]](#manual-Array___mk) α
```

Swaps two elements of an array, returning the array unchanged if either index is out of bounds. The
modification is performed in-place when the reference to the array is unique.

Examples:

- `#["red", "green", "blue", "brown"].[swapIfInBounds]](#manual-Array___swapIfInBounds) 0 3 = #["brown", "green", "blue", "red"]`
- `#["red", "green", "blue", "brown"].[swapIfInBounds]](#manual-Array___swapIfInBounds) 0 2 = #["blue", "green", "red", "brown"]`
- `#["red", "green", "blue", "brown"].[swapIfInBounds]](#manual-Array___swapIfInBounds) 1 2 = #["red", "blue", "green", "brown"]`
- `#["red", "green", "blue", "brown"].[swapIfInBounds]](#manual-Array___swapIfInBounds) 0 4 = #["red", "green", "blue", "brown"]`
- `#["red", "green", "blue", "brown"].[swapIfInBounds]](#manual-Array___swapIfInBounds) 9 2 = #["red", "green", "blue", "brown"]`

def

```lean
[Array.swapAt.{u}]](#manual-Array___swapAt) {α : Type u} (xs : [Array]](#manual-Array___mk) α) (i : [Nat]](#manual-Nat___zero)) (v : α)
  (hi : i [<]](#manual-LT___mk) xs.[size]](#manual-Array___size) := by get_elem_tactic) : α [×]](#manual-Prod___mk) [Array]](#manual-Array___mk) α



[Array.swapAt.{u}]](#manual-Array___swapAt) {α : Type u}
  (xs : [Array]](#manual-Array___mk) α) (i : [Nat]](#manual-Nat___zero)) (v : α)
  (hi : i [<]](#manual-LT___mk) xs.[size]](#manual-Array___size) := by
    get_elem_tactic) :
  α [×]](#manual-Prod___mk) [Array]](#manual-Array___mk) α
```

Swaps a new element with the element at the given index.

Returns the value formerly found at `i`, paired with an array in which the value at `i` has been
replaced with `v`.

Examples:

- `#["spinach", "broccoli", "carrot"].[swapAt]](#manual-Array___swapAt) 1 "pepper" = ("broccoli", #["spinach", "pepper", "carrot"])`
- `#["spinach", "broccoli", "carrot"].[swapAt]](#manual-Array___swapAt) 2 "pepper" = ("carrot", #["spinach", "broccoli", "pepper"])`

def

```lean
[Array.swapAt!.{u}]](#manual-Array___swapAt___) {α : Type u} (xs : [Array]](#manual-Array___mk) α) (i : [Nat]](#manual-Nat___zero)) (v : α) :
  α [×]](#manual-Prod___mk) [Array]](#manual-Array___mk) α



[Array.swapAt!.{u}]](#manual-Array___swapAt___) {α : Type u}
  (xs : [Array]](#manual-Array___mk) α) (i : [Nat]](#manual-Nat___zero)) (v : α) :
  α [×]](#manual-Prod___mk) [Array]](#manual-Array___mk) α
```

Swaps a new element with the element at the given index. Panics if the index is out of bounds.

Returns the value formerly found at `i`, paired with an array in which the value at `i` has been
replaced with `v`.

Examples:

- `#["spinach", "broccoli", "carrot"].[swapAt!]](#manual-Array___swapAt___) 1 "pepper" = (#["spinach", "pepper", "carrot"], "broccoli")`
- `#["spinach", "broccoli", "carrot"].[swapAt!]](#manual-Array___swapAt___) 2 "pepper" = (#["spinach", "broccoli", "pepper"], "carrot")`

def

```lean
[Array.replace.{u}]](#manual-Array___replace) {α : Type u} [[BEq]](#manual-BEq___mk) α] (xs : [Array]](#manual-Array___mk) α) (a b : α) :
  [Array]](#manual-Array___mk) α



[Array.replace.{u}]](#manual-Array___replace) {α : Type u} [[BEq]](#manual-BEq___mk) α]
  (xs : [Array]](#manual-Array___mk) α) (a b : α) : [Array]](#manual-Array___mk) α
```

Replaces the first occurrence of `a` with `b` in an array. The modification is performed in-place
when the reference to the array is unique. Returns the array unmodified when `a` is not present.

Examples:

- `#[1, 2, 3, 2, 1].[replace]](#manual-Array___replace) 2 5 = #[1, 5, 3, 2, 1]`
- `#[1, 2, 3, 2, 1].[replace]](#manual-Array___replace) 0 5 = #[1, 2, 3, 2, 1]`
- `#[].[replace]](#manual-Array___replace) 2 5 = #[]`

def

```lean
[Array.set.{u_1}]](#manual-Array___set) {α : Type u_1} (xs : [Array]](#manual-Array___mk) α) (i : [Nat]](#manual-Nat___zero)) (v : α)
  (h : i [<]](#manual-LT___mk) xs.[size]](#manual-Array___size) := by get_elem_tactic) : [Array]](#manual-Array___mk) α



[Array.set.{u_1}]](#manual-Array___set) {α : Type u_1}
  (xs : [Array]](#manual-Array___mk) α) (i : [Nat]](#manual-Nat___zero)) (v : α)
  (h : i [<]](#manual-LT___mk) xs.[size]](#manual-Array___size) := by
    get_elem_tactic) :
  [Array]](#manual-Array___mk) α
```

Replaces the element at a given index in an array.

No bounds check is performed, but the function requires a proof that the index is in bounds. This
proof can usually be omitted, and will be synthesized automatically.

The array is modified in-place if there are no other references to it.

Examples:

- `#[0, 1, 2].[set]](#manual-Array___set) 1 5 = #[0, 5, 2]`
- `#["orange", "apple"].[set]](#manual-Array___set) 1 "grape" = #["orange", "grape"]`

def

```lean
[Array.set!.{u_1}]](#manual-Array___set___) {α : Type u_1} (xs : [Array]](#manual-Array___mk) α) (i : [Nat]](#manual-Nat___zero)) (v : α) :
  [Array]](#manual-Array___mk) α



[Array.set!.{u_1}]](#manual-Array___set___) {α : Type u_1}
  (xs : [Array]](#manual-Array___mk) α) (i : [Nat]](#manual-Nat___zero)) (v : α) :
  [Array]](#manual-Array___mk) α
```

Set an element in an array, or panic if the index is out of bounds.

This will perform the update destructively provided that `a` has a reference
count of 1 when called.

def

```lean
[Array.setIfInBounds.{u_1}]](#manual-Array___setIfInBounds) {α : Type u_1} (xs : [Array]](#manual-Array___mk) α) (i : [Nat]](#manual-Nat___zero))
  (v : α) : [Array]](#manual-Array___mk) α



[Array.setIfInBounds.{u_1}]](#manual-Array___setIfInBounds) {α : Type u_1}
  (xs : [Array]](#manual-Array___mk) α) (i : [Nat]](#manual-Nat___zero)) (v : α) :
  [Array]](#manual-Array___mk) α
```

Replaces the element at the provided index in an array. The array is returned unmodified if the
index is out of bounds.

The array is modified in-place if there are no other references to it.

Examples:

- `#[0, 1, 2].[setIfInBounds]](#manual-Array___setIfInBounds) 1 5 = #[0, 5, 2]`
- `#["orange", "apple"].[setIfInBounds]](#manual-Array___setIfInBounds) 1 "grape" = #["orange", "grape"]`
- `#["orange", "apple"].[setIfInBounds]](#manual-Array___setIfInBounds) 5 "grape" = #["orange", "apple"]`

def

```lean
[Array.uset.{u}]](#manual-Array___uset) {α : Type u} (xs : [Array]](#manual-Array___mk) α) (i : [USize]](#manual-USize___ofBitVec)) (v : α)
  (h : i.[toNat]](#manual-USize___toNat) [<]](#manual-LT___mk) xs.[size]](#manual-Array___size)) : [Array]](#manual-Array___mk) α



[Array.uset.{u}]](#manual-Array___uset) {α : Type u} (xs : [Array]](#manual-Array___mk) α)
  (i : [USize]](#manual-USize___ofBitVec)) (v : α)
  (h : i.[toNat]](#manual-USize___toNat) [<]](#manual-LT___mk) xs.[size]](#manual-Array___size)) : [Array]](#manual-Array___mk) α
```

Low-level modification operator which is as fast as a C array write. The modification is performed
in-place when the reference to the array is unique.

This avoids overhead due to unboxing a `[Nat]](#manual-Nat___zero)` used as an index.

def

```lean
[Array.modify.{u}]](#manual-Array___modify) {α : Type u} (xs : [Array]](#manual-Array___mk) α) (i : [Nat]](#manual-Nat___zero)) (f : α → α) :
  [Array]](#manual-Array___mk) α



[Array.modify.{u}]](#manual-Array___modify) {α : Type u}
  (xs : [Array]](#manual-Array___mk) α) (i : [Nat]](#manual-Nat___zero)) (f : α → α) :
  [Array]](#manual-Array___mk) α
```

Replaces the element at the given index, if it exists, with the result of applying `f` to it. If the
index is invalid, the array is returned unmodified.

Examples:

- `#[1, 2, 3].[modify]](#manual-Array___modify) 0 (· * 10) = #[10, 2, 3]`
- `#[1, 2, 3].[modify]](#manual-Array___modify) 2 (· * 10) = #[1, 2, 30]`
- `#[1, 2, 3].[modify]](#manual-Array___modify) 3 (· * 10) = #[1, 2, 3]`

def

```lean
[Array.modifyM.{u, u_1}]](#manual-Array___modifyM) {α : Type u} {m : Type u → Type u_1} [[Monad]](#manual-Monad___mk) m]
  (xs : [Array]](#manual-Array___mk) α) (i : [Nat]](#manual-Nat___zero)) (f : α → m α) : m ([Array]](#manual-Array___mk) α)



[Array.modifyM.{u, u_1}]](#manual-Array___modifyM) {α : Type u}
  {m : Type u → Type u_1} [[Monad]](#manual-Monad___mk) m]
  (xs : [Array]](#manual-Array___mk) α) (i : [Nat]](#manual-Nat___zero)) (f : α → m α) :
  m ([Array]](#manual-Array___mk) α)
```

Replaces the element at the given index, if it exists, with the result of applying the monadic
function `f` to it. If the index is invalid, the array is returned unmodified and `f` is not called.

Examples:

```lean
[#eval]](#manual-Lean___Parser___Command___eval) #[1, 2, 3, 4].[modifyM]](#manual-Array___modifyM) 2 fun x => [do]](#manual-Lean___Parser___Term___do)
[IO.println](https://lean-lang.org/doc/reference/latest/IO/Console-Output/#IO___println) s!"It was {x}"
return x * 10
```

```lean
It was 3
```

```lean
#[1, 2, 30, 4]
```

```lean
[#eval]](#manual-Lean___Parser___Command___eval) #[1, 2, 3, 4].[modifyM]](#manual-Array___modifyM) 6 fun x => [do]](#manual-Lean___Parser___Term___do)
[IO.println](https://lean-lang.org/doc/reference/latest/IO/Console-Output/#IO___println) s!"It was {x}"
return x * 10
```

```lean
#[1, 2, 3, 4]
```

def

```lean
[Array.modifyOp.{u}]](#manual-Array___modifyOp) {α : Type u} (xs : [Array]](#manual-Array___mk) α) (idx : [Nat]](#manual-Nat___zero)) (f : α → α) :
  [Array]](#manual-Array___mk) α



[Array.modifyOp.{u}]](#manual-Array___modifyOp) {α : Type u}
  (xs : [Array]](#manual-Array___mk) α) (idx : [Nat]](#manual-Nat___zero)) (f : α → α) :
  [Array]](#manual-Array___mk) α
```

Replaces the element at the given index, if it exists, with the result of applying `f` to it. If the
index is invalid, the array is returned unmodified.

Examples:

- `#[1, 2, 3].[modifyOp]](#manual-Array___modifyOp) 0 (· * 10) = #[10, 2, 3]`
- `#[1, 2, 3].[modifyOp]](#manual-Array___modifyOp) 2 (· * 10) = #[1, 2, 30]`
- `#[1, 2, 3].[modifyOp]](#manual-Array___modifyOp) 3 (· * 10) = #[1, 2, 3]`

def

```lean
[Array.insertIdx.{u}]](#manual-Array___insertIdx) {α : Type u} (as : [Array]](#manual-Array___mk) α) (i : [Nat]](#manual-Nat___zero)) (a : α) :
  [autoParam]](#manual-autoParam) [(]](#manual-LE___mk)i [≤]](#manual-LE___mk) as.[size]](#manual-Array___size)[)]](#manual-LE___mk) Array.insertIdx._auto_1 → [Array]](#manual-Array___mk) α



[Array.insertIdx.{u}]](#manual-Array___insertIdx) {α : Type u}
  (as : [Array]](#manual-Array___mk) α) (i : [Nat]](#manual-Nat___zero)) (a : α) :
  [autoParam]](#manual-autoParam) [(]](#manual-LE___mk)i [≤]](#manual-LE___mk) as.[size]](#manual-Array___size)[)]](#manual-LE___mk)
      Array.insertIdx._auto_1 →
    [Array]](#manual-Array___mk) α
```

Inserts an element into an array at the specified index. If the index is greater than the size of
the array, then the array is returned unmodified.

In other words, the new element is inserted into the array `as` after the first `i` elements of
`as`.

This function takes worst case `O(n)` time because it has to swap the inserted element into place.

Examples:

- `#["tues", "thur", "sat"].[insertIdx]](#manual-Array___insertIdx) 1 "wed" = #["tues", "wed", "thur", "sat"]`
- `#["tues", "thur", "sat"].[insertIdx]](#manual-Array___insertIdx) 2 "wed" = #["tues", "thur", "wed", "sat"]`
- `#["tues", "thur", "sat"].[insertIdx]](#manual-Array___insertIdx) 3 "wed" = #["tues", "thur", "sat", "wed"]`

def

```lean
[Array.insertIdx!.{u}]](#manual-Array___insertIdx___) {α : Type u} (as : [Array]](#manual-Array___mk) α) (i : [Nat]](#manual-Nat___zero)) (a : α) :
  [Array]](#manual-Array___mk) α



[Array.insertIdx!.{u}]](#manual-Array___insertIdx___) {α : Type u}
  (as : [Array]](#manual-Array___mk) α) (i : [Nat]](#manual-Nat___zero)) (a : α) :
  [Array]](#manual-Array___mk) α
```

Inserts an element into an array at the specified index. Panics if the index is greater than the
size of the array.

In other words, the new element is inserted into the array `as` after the first `i` elements of
`as`.

This function takes worst case `O(n)` time because it has to swap the inserted element into place.
`[Array.insertIdx]](#manual-Array___insertIdx)` and `[Array.insertIdxIfInBounds]](#manual-Array___insertIdxIfInBounds)` are safer alternatives.

Examples:

- `#["tues", "thur", "sat"].[insertIdx!]](#manual-Array___insertIdx___) 1 "wed" = #["tues", "wed", "thur", "sat"]`
- `#["tues", "thur", "sat"].[insertIdx!]](#manual-Array___insertIdx___) 2 "wed" = #["tues", "thur", "wed", "sat"]`
- `#["tues", "thur", "sat"].[insertIdx!]](#manual-Array___insertIdx___) 3 "wed" = #["tues", "thur", "sat", "wed"]`

def

```lean
[Array.insertIdxIfInBounds.{u}]](#manual-Array___insertIdxIfInBounds) {α : Type u} (as : [Array]](#manual-Array___mk) α) (i : [Nat]](#manual-Nat___zero))
  (a : α) : [Array]](#manual-Array___mk) α



[Array.insertIdxIfInBounds.{u}]](#manual-Array___insertIdxIfInBounds) {α : Type u}
  (as : [Array]](#manual-Array___mk) α) (i : [Nat]](#manual-Nat___zero)) (a : α) :
  [Array]](#manual-Array___mk) α
```

Inserts an element into an array at the specified index. The array is returned unmodified if the
index is greater than the size of the array.

In other words, the new element is inserted into the array `as` after the first `i` elements of
`as`.

This function takes worst case `O(n)` time because it has to swap the inserted element into place.

Examples:

- `#["tues", "thur", "sat"].[insertIdxIfInBounds]](#manual-Array___insertIdxIfInBounds) 1 "wed" = #["tues", "wed", "thur", "sat"]`
- `#["tues", "thur", "sat"].[insertIdxIfInBounds]](#manual-Array___insertIdxIfInBounds) 2 "wed" = #["tues", "thur", "wed", "sat"]`
- `#["tues", "thur", "sat"].[insertIdxIfInBounds]](#manual-Array___insertIdxIfInBounds) 3 "wed" = #["tues", "thur", "sat", "wed"]`
- `#["tues", "thur", "sat"].[insertIdxIfInBounds]](#manual-Array___insertIdxIfInBounds) 4 "wed" = #["tues", "thur", "sat"]`

def

```lean
[Array.reverse.{u}]](#manual-Array___reverse) {α : Type u} (as : [Array]](#manual-Array___mk) α) : [Array]](#manual-Array___mk) α



[Array.reverse.{u}]](#manual-Array___reverse) {α : Type u}
  (as : [Array]](#manual-Array___mk) α) : [Array]](#manual-Array___mk) α
```

Reverses an array by repeatedly swapping elements.

The original array is modified in place if there are no other references to it.

Examples:

- `(#[] : [Array]](#manual-Array___mk) [Nat]](#manual-Nat___zero)).[reverse]](#manual-Array___reverse) = #[]`
- `#[0, 1].[reverse]](#manual-Array___reverse) = #[1, 0]`
- `#[0, 1, 2].[reverse]](#manual-Array___reverse) = #[2, 1, 0]`

def

```lean
[Array.take.{u}]](#manual-Array___take) {α : Type u} (xs : [Array]](#manual-Array___mk) α) (i : [Nat]](#manual-Nat___zero)) : [Array]](#manual-Array___mk) α



[Array.take.{u}]](#manual-Array___take) {α : Type u} (xs : [Array]](#manual-Array___mk) α)
  (i : [Nat]](#manual-Nat___zero)) : [Array]](#manual-Array___mk) α
```

Returns a new array that contains the first `i` elements of `xs`. If `xs` has fewer than `i`
elements, the new array contains all the elements of `xs`.

The returned array is always a new array, even if it contains the same elements as the input array.

Examples:

- `#["red", "green", "blue"].[take]](#manual-Array___take) 1 = #["red"]`
- `#["red", "green", "blue"].[take]](#manual-Array___take) 2 = #["red", "green"]`
- `#["red", "green", "blue"].[take]](#manual-Array___take) 5 = #["red", "green", "blue"]`

def

```lean
[Array.takeWhile.{u}]](#manual-Array___takeWhile) {α : Type u} (p : α → [Bool]](#manual-Bool___false)) (as : [Array]](#manual-Array___mk) α) : [Array]](#manual-Array___mk) α



[Array.takeWhile.{u}]](#manual-Array___takeWhile) {α : Type u}
  (p : α → [Bool]](#manual-Bool___false)) (as : [Array]](#manual-Array___mk) α) : [Array]](#manual-Array___mk) α
```

Returns a new array that contains the longest prefix of elements that satisfy the predicate `p` from
an array.

Examples:

- `#[0, 1, 2, 3, 2, 1].[takeWhile]](#manual-Array___takeWhile) (· < 2) = #[0, 1]`
- `#[0, 1, 2, 3, 2, 1].[takeWhile]](#manual-Array___takeWhile) (· < 20) = #[0, 1, 2, 3, 2, 1]`
- `#[0, 1, 2, 3, 2, 1].[takeWhile]](#manual-Array___takeWhile) (· < 0) = #[]`

def

```lean
[Array.drop.{u}]](#manual-Array___drop) {α : Type u} (xs : [Array]](#manual-Array___mk) α) (i : [Nat]](#manual-Nat___zero)) : [Array]](#manual-Array___mk) α



[Array.drop.{u}]](#manual-Array___drop) {α : Type u} (xs : [Array]](#manual-Array___mk) α)
  (i : [Nat]](#manual-Nat___zero)) : [Array]](#manual-Array___mk) α
```

Removes the first `i` elements of `xs`. If `xs` has fewer than `i` elements, the new array is empty.

The returned array is always a new array, even if it contains the same elements as the input array.

Examples:

- `#["red", "green", "blue"].[drop]](#manual-Array___drop) 1 = #["green", "blue"]`
- `#["red", "green", "blue"].[drop]](#manual-Array___drop) 2 = #["blue"]`
- `#["red", "green", "blue"].[drop]](#manual-Array___drop) 5 = #[]`

def

```lean
[Array.shrink.{u}]](#manual-Array___shrink) {α : Type u} (xs : [Array]](#manual-Array___mk) α) (n : [Nat]](#manual-Nat___zero)) : [Array]](#manual-Array___mk) α



[Array.shrink.{u}]](#manual-Array___shrink) {α : Type u}
  (xs : [Array]](#manual-Array___mk) α) (n : [Nat]](#manual-Nat___zero)) : [Array]](#manual-Array___mk) α
```

Returns the first `n` elements of an array. The resulting array is produced by repeatedly calling
`[Array.pop]](#manual-Array___pop)`. If `n` is greater than the size of the array, it is returned unmodified.

If the reference to the array is unique, then this function uses in-place modification.

Examples:

- `#[0, 1, 2, 3, 4].[shrink]](#manual-Array___shrink) 2 = #[0, 1]`
- `#[0, 1, 2, 3, 4].[shrink]](#manual-Array___shrink) 0 = #[]`
- `#[0, 1, 2, 3, 4].[shrink]](#manual-Array___shrink) 10 = #[0, 1, 2, 3, 4]`

def

```lean
[Array.flatten.{u}]](#manual-Array___flatten) {α : Type u} (xss : [Array]](#manual-Array___mk) ([Array]](#manual-Array___mk) α)) : [Array]](#manual-Array___mk) α



[Array.flatten.{u}]](#manual-Array___flatten) {α : Type u}
  (xss : [Array]](#manual-Array___mk) ([Array]](#manual-Array___mk) α)) : [Array]](#manual-Array___mk) α
```

Appends the contents of array of arrays into a single array. The resulting array contains the same
elements as the nested arrays in the same order.

Examples:

- `#[#[5], #[4], #[3, 2]].[flatten]](#manual-Array___flatten) = #[5, 4, 3, 2]`
- `#[#[0, 1], #[], #[2], #[1, 0, 1]].[flatten]](#manual-Array___flatten) = #[0, 1, 2, 1, 0, 1]`
- `(#[] : [Array]](#manual-Array___mk) [Nat]](#manual-Nat___zero)).[flatten]](#manual-Array___flatten) = #[]`

def

```lean
[Array.getEvenElems.{u}]](#manual-Array___getEvenElems) {α : Type u} (as : [Array]](#manual-Array___mk) α) : [Array]](#manual-Array___mk) α



[Array.getEvenElems.{u}]](#manual-Array___getEvenElems) {α : Type u}
  (as : [Array]](#manual-Array___mk) α) : [Array]](#manual-Array___mk) α
```

Returns a new array that contains the elements at even indices in `as`, starting with the element at
index `0`.

Examples:

- `#[0, 1, 2, 3, 4].[getEvenElems]](#manual-Array___getEvenElems) = #[0, 2, 4]`
- `#[1, 2, 3, 4].[getEvenElems]](#manual-Array___getEvenElems) = #[1, 3]`
- `#["red", "green", "blue"].[getEvenElems]](#manual-Array___getEvenElems) = #["red", "blue"]`
- `(#[] : [Array]](#manual-Array___mk) [String]](#manual-String___ofByteArray)).[getEvenElems]](#manual-Array___getEvenElems) = #[]`

#### 20.16.4.7. Sorted Arrays {#manual-The-Lean-Language-Reference--Basic-Types--Arrays--API-Reference--Sorted-Arrays}

def

```lean
[Array.qsort.{u_1}]](#manual-Array___qsort) {α : Type u_1} (as : [Array]](#manual-Array___mk) α)
  (lt : α → α → [Bool]](#manual-Bool___false) := by exact (· < ·)) (lo : [Nat]](#manual-Nat___zero) := 0)
  (hi : [Nat]](#manual-Nat___zero) := as.[size]](#manual-Array___size) [-]](#manual-HSub___mk) 1) : [Array]](#manual-Array___mk) α



[Array.qsort.{u_1}]](#manual-Array___qsort) {α : Type u_1}
  (as : [Array]](#manual-Array___mk) α)
  (lt : α → α → [Bool]](#manual-Bool___false) := by exact (· < ·))
  (lo : [Nat]](#manual-Nat___zero) := 0)
  (hi : [Nat]](#manual-Nat___zero) := as.[size]](#manual-Array___size) [-]](#manual-HSub___mk) 1) : [Array]](#manual-Array___mk) α
```

In-place quicksort.

`qsort as lt lo hi` sorts the subarray `as[lo...=hi]` in-place using `lt` to compare elements.

def

```lean
[Array.qsortOrd.{u_1}]](#manual-Array___qsortOrd) {α : Type u_1} [ord : [Ord]](#manual-Ord___mk) α] (xs : [Array]](#manual-Array___mk) α) :
  [Array]](#manual-Array___mk) α



[Array.qsortOrd.{u_1}]](#manual-Array___qsortOrd) {α : Type u_1}
  [ord : [Ord]](#manual-Ord___mk) α] (xs : [Array]](#manual-Array___mk) α) : [Array]](#manual-Array___mk) α
```

Sort an array using `[compare]](#manual-Ord___mk)` to compare elements.

def

```lean
[Array.insertionSort.{u_1}]](#manual-Array___insertionSort) {α : Type u_1} (xs : [Array]](#manual-Array___mk) α)
  (lt : α → α → [Bool]](#manual-Bool___false) := by exact (· < ·)) : [Array]](#manual-Array___mk) α



[Array.insertionSort.{u_1}]](#manual-Array___insertionSort) {α : Type u_1}
  (xs : [Array]](#manual-Array___mk) α)
  (lt : α → α → [Bool]](#manual-Bool___false) := by
    exact (· < ·)) :
  [Array]](#manual-Array___mk) α
```

Sorts an array using insertion sort.

The optional parameter `lt` specifies an ordering predicate. It defaults to `[LT.lt]](#manual-LT___mk)`, which must be
decidable to be used for sorting.

def

```lean
[Array.binInsert.{u}]](#manual-Array___binInsert) {α : Type u} (lt : α → α → [Bool]](#manual-Bool___false)) (as : [Array]](#manual-Array___mk) α)
  (k : α) : [Array]](#manual-Array___mk) α



[Array.binInsert.{u}]](#manual-Array___binInsert) {α : Type u}
  (lt : α → α → [Bool]](#manual-Bool___false)) (as : [Array]](#manual-Array___mk) α)
  (k : α) : [Array]](#manual-Array___mk) α
```

Inserts an element into a sorted array such that the resulting array is sorted. If the element is
already present in the array, it is not inserted.

The ordering predicate `lt` should be a total order on elements, and the array `as` should be sorted
with respect to `lt`.

`[Array.binInsertM]](#manual-Array___binInsertM)` is a more general operator that provides greater control over the handling of
duplicate elements in addition to running in a monad.

Examples:

- `#[0, 1, 3, 5].[binInsert]](#manual-Array___binInsert) (· < ·) 2 = #[0, 1, 2, 3, 5]`
- `#[0, 1, 3, 5].[binInsert]](#manual-Array___binInsert) (· < ·) 1 = #[0, 1, 3, 5]`
- `#[].[binInsert]](#manual-Array___binInsert) (· < ·) 1 = #[1]`

def

```lean
[Array.binInsertM.{u, v}]](#manual-Array___binInsertM) {α : Type u} {m : Type u → Type v} [[Monad]](#manual-Monad___mk) m]
  (lt : α → α → [Bool]](#manual-Bool___false)) (merge : α → m α) (add : [Unit]](#manual-Unit) → m α)
  (as : [Array]](#manual-Array___mk) α) (k : α) : m ([Array]](#manual-Array___mk) α)



[Array.binInsertM.{u, v}]](#manual-Array___binInsertM) {α : Type u}
  {m : Type u → Type v} [[Monad]](#manual-Monad___mk) m]
  (lt : α → α → [Bool]](#manual-Bool___false)) (merge : α → m α)
  (add : [Unit]](#manual-Unit) → m α) (as : [Array]](#manual-Array___mk) α)
  (k : α) : m ([Array]](#manual-Array___mk) α)
```

Inserts an element `k` into a sorted array `as` such that the resulting array is sorted.

The ordering predicate `lt` should be a total order on elements, and the array `as` should be sorted
with respect to `lt`.

If an element that `lt` equates to `k` is already present in `as`, then `merge` is applied to the
existing element to determine the value of that position in the resulting array. If no element equal
to `k` is present, then `add` is used to determine the value to be inserted.

def

```lean
[Array.binSearch]](#manual-Array___binSearch) {α : Type} (as : [Array]](#manual-Array___mk) α) (k : α) (lt : α → α → [Bool]](#manual-Bool___false))
  (lo : [Nat]](#manual-Nat___zero) := 0) (hi : [Nat]](#manual-Nat___zero) := as.[size]](#manual-Array___size) [-]](#manual-HSub___mk) 1) : [Option]](#manual-Option___none) α



[Array.binSearch]](#manual-Array___binSearch) {α : Type} (as : [Array]](#manual-Array___mk) α)
  (k : α) (lt : α → α → [Bool]](#manual-Bool___false))
  (lo : [Nat]](#manual-Nat___zero) := 0)
  (hi : [Nat]](#manual-Nat___zero) := as.[size]](#manual-Array___size) [-]](#manual-HSub___mk) 1) : [Option]](#manual-Option___none) α
```

Binary search for an element equivalent to `k` in the sorted array `as`. Returns the element from
the array, if it is found, or `[none]](#manual-Option___none)` otherwise.

The array `as` must be sorted according to the comparison operator `lt`, which should be a total
order.

The optional parameters `lo` and `hi` determine the region of the array indices to be searched. Both
are inclusive, and default to searching the entire array.

def

```lean
[Array.binSearchContains]](#manual-Array___binSearchContains) {α : Type} (as : [Array]](#manual-Array___mk) α) (k : α)
  (lt : α → α → [Bool]](#manual-Bool___false)) (lo : [Nat]](#manual-Nat___zero) := 0) (hi : [Nat]](#manual-Nat___zero) := as.[size]](#manual-Array___size) [-]](#manual-HSub___mk) 1) : [Bool]](#manual-Bool___false)



[Array.binSearchContains]](#manual-Array___binSearchContains) {α : Type}
  (as : [Array]](#manual-Array___mk) α) (k : α)
  (lt : α → α → [Bool]](#manual-Bool___false)) (lo : [Nat]](#manual-Nat___zero) := 0)
  (hi : [Nat]](#manual-Nat___zero) := as.[size]](#manual-Array___size) [-]](#manual-HSub___mk) 1) : [Bool]](#manual-Bool___false)
```

Binary search for an element equivalent to `k` in the sorted array `as`. Returns `[true]](#manual-Bool___false)` if the
element is found, or `[false]](#manual-Bool___false)` otherwise.

The array `as` must be sorted according to the comparison operator `lt`, which should be a total
order.

The optional parameters `lo` and `hi` determine the region of the array indices to be searched. Both
are inclusive, and default to searching the entire array.

#### 20.16.4.8. Iteration {#manual-The-Lean-Language-Reference--Basic-Types--Arrays--API-Reference--Iteration}

def

```lean
[Array.iter.{w}]](#manual-Array___iter) {α : Type w} (l : [Array]](#manual-Array___mk) α) : [Std.Iter](https://lean-lang.org/doc/reference/latest/Iterators/Iterator-Definitions/#Std___Iter___mk) α



[Array.iter.{w}]](#manual-Array___iter) {α : Type w}
  (l : [Array]](#manual-Array___mk) α) : [Std.Iter](https://lean-lang.org/doc/reference/latest/Iterators/Iterator-Definitions/#Std___Iter___mk) α
```

Returns a finite iterator for the given array.
The iterator yields the elements of the array in order and then terminates.

The monadic version of this iterator is `[Array.iterM]](#manual-Array___iterM)`.

**Termination properties:**

- `Finite` instance: always
- `Productive` instance: always

def

```lean
[Array.iterFromIdx.{w}]](#manual-Array___iterFromIdx) {α : Type w} (l : [Array]](#manual-Array___mk) α) (pos : [Nat]](#manual-Nat___zero)) :
  [Std.Iter](https://lean-lang.org/doc/reference/latest/Iterators/Iterator-Definitions/#Std___Iter___mk) α



[Array.iterFromIdx.{w}]](#manual-Array___iterFromIdx) {α : Type w}
  (l : [Array]](#manual-Array___mk) α) (pos : [Nat]](#manual-Nat___zero)) : [Std.Iter](https://lean-lang.org/doc/reference/latest/Iterators/Iterator-Definitions/#Std___Iter___mk) α
```

Returns a finite iterator for the given array starting at the given index.
The iterator yields the elements of the array in order and then terminates.

The monadic version of this iterator is `[Array.iterFromIdxM]](#manual-Array___iterFromIdxM)`.

**Termination properties:**

- `Finite` instance: always
- `Productive` instance: always

def

```lean
[Array.iterM.{w, w'}]](#manual-Array___iterM) {α : Type w} (array : [Array]](#manual-Array___mk) α)
  (m : Type w → Type w') [[Pure]](#manual-Pure___mk) m] : [Std.IterM](https://lean-lang.org/doc/reference/latest/Iterators/Iterator-Definitions/#Std___IterM___mk) m α



[Array.iterM.{w, w'}]](#manual-Array___iterM) {α : Type w}
  (array : [Array]](#manual-Array___mk) α) (m : Type w → Type w')
  [[Pure]](#manual-Pure___mk) m] : [Std.IterM](https://lean-lang.org/doc/reference/latest/Iterators/Iterator-Definitions/#Std___IterM___mk) m α
```

Returns a finite monadic iterator for the given array.
The iterator yields the elements of the array in order and then terminates. There are no side
effects.

The pure version of this iterator is `[Array.iter]](#manual-Array___iter)`.

**Termination properties:**

- `Finite` instance: always
- `Productive` instance: always

def

```lean
[Array.iterFromIdxM.{w, w'}]](#manual-Array___iterFromIdxM) {α : Type w} (array : [Array]](#manual-Array___mk) α)
  (m : Type w → Type w') (pos : [Nat]](#manual-Nat___zero)) [[Pure]](#manual-Pure___mk) m] : [Std.IterM](https://lean-lang.org/doc/reference/latest/Iterators/Iterator-Definitions/#Std___IterM___mk) m α



[Array.iterFromIdxM.{w, w'}]](#manual-Array___iterFromIdxM) {α : Type w}
  (array : [Array]](#manual-Array___mk) α) (m : Type w → Type w')
  (pos : [Nat]](#manual-Nat___zero)) [[Pure]](#manual-Pure___mk) m] : [Std.IterM](https://lean-lang.org/doc/reference/latest/Iterators/Iterator-Definitions/#Std___IterM___mk) m α
```

Returns a finite monadic iterator for the given array starting at the given index.
The iterator yields the elements of the array in order and then terminates.

The pure version of this iterator is `[Array.iterFromIdx]](#manual-Array___iterFromIdx)`.

**Termination properties:**

- `Finite` instance: always
- `Productive` instance: always

def

```lean
[Array.foldr.{u, v}]](#manual-Array___foldr) {α : Type u} {β : Type v} (f : α → β → β) (init : β)
  (as : [Array]](#manual-Array___mk) α) (start : [Nat]](#manual-Nat___zero) := as.[size]](#manual-Array___size)) (stop : [Nat]](#manual-Nat___zero) := 0) : β



[Array.foldr.{u, v}]](#manual-Array___foldr) {α : Type u}
  {β : Type v} (f : α → β → β) (init : β)
  (as : [Array]](#manual-Array___mk) α) (start : [Nat]](#manual-Nat___zero) := as.[size]](#manual-Array___size))
  (stop : [Nat]](#manual-Nat___zero) := 0) : β
```

Folds a function over an array from the right, accumulating a value starting with `init`. The
accumulated value is combined with the each element of the array in reverse order, using `f`.

The optional parameters `start` and `stop` control the region of the array to be folded. Folding
proceeds from `start` (exclusive) to `stop` (inclusive), so no folding occurs unless `start > stop`.
By default, the entire array is used.

Examples:

- `#[a, b, c].[foldr]](#manual-Array___foldr) f init = f a (f b (f c init))`
- `#[1, 2, 3].[foldr]](#manual-Array___foldr) (toString · ++ ·) "" = "123"`
- `#[1, 2, 3].[foldr]](#manual-Array___foldr) (s!"({·} {·})") "!" = "(1 (2 (3 !)))"`

def

```lean
[Array.foldrM.{u, v, w}]](#manual-Array___foldrM) {α : Type u} {β : Type v} {m : Type v → Type w}
  [[Monad]](#manual-Monad___mk) m] (f : α → β → m β) (init : β) (as : [Array]](#manual-Array___mk) α)
  (start : [Nat]](#manual-Nat___zero) := as.[size]](#manual-Array___size)) (stop : [Nat]](#manual-Nat___zero) := 0) : m β



[Array.foldrM.{u, v, w}]](#manual-Array___foldrM) {α : Type u}
  {β : Type v} {m : Type v → Type w}
  [[Monad]](#manual-Monad___mk) m] (f : α → β → m β) (init : β)
  (as : [Array]](#manual-Array___mk) α) (start : [Nat]](#manual-Nat___zero) := as.[size]](#manual-Array___size))
  (stop : [Nat]](#manual-Nat___zero) := 0) : m β
```

Folds a monadic function over an array from the right, accumulating a value starting with `init`.
The accumulated value is combined with the each element of the list in reverse order, using `f`.

The optional parameters `start` and `stop` control the region of the array to be folded. Folding
proceeds from `start` (exclusive) to `stop` (inclusive), so no folding occurs unless `start > stop`.
By default, the entire array is folded.

Examples:

```
example [Monad m] (f : α → β → m β) :
  Array.foldrM (m := m) f x₀ #[a, b, c] = (do
    let x₁ ← f c x₀
    let x₂ ← f b x₁
    let x₃ ← f a x₂
    pure x₃)
  := by rfl
```

```
example [Monad m] (f : α → β → m β) :
  Array.foldrM (m := m) f x₀ #[a, b, c] (start := 2) = (do
    let x₁ ← f b x₀
    let x₂ ← f a x₁
    pure x₂)
  := by rfl
```

def

```lean
[Array.foldl.{u, v}]](#manual-Array___foldl) {α : Type u} {β : Type v} (f : β → α → β) (init : β)
  (as : [Array]](#manual-Array___mk) α) (start : [Nat]](#manual-Nat___zero) := 0) (stop : [Nat]](#manual-Nat___zero) := as.[size]](#manual-Array___size)) : β



[Array.foldl.{u, v}]](#manual-Array___foldl) {α : Type u}
  {β : Type v} (f : β → α → β) (init : β)
  (as : [Array]](#manual-Array___mk) α) (start : [Nat]](#manual-Nat___zero) := 0)
  (stop : [Nat]](#manual-Nat___zero) := as.[size]](#manual-Array___size)) : β
```

Folds a function over an array from the left, accumulating a value starting with `init`. The
accumulated value is combined with the each element of the array in order, using `f`.

The optional parameters `start` and `stop` control the region of the array to be folded. Folding
proceeds from `start` (inclusive) to `stop` (exclusive), so no folding occurs unless `start < stop`.
By default, the entire array is used.

Examples:

- `#[a, b, c].[foldl]](#manual-Array___foldl) f z = f (f (f z a) b) c`
- `#[1, 2, 3].[foldl]](#manual-Array___foldl) (· ++ toString ·) "" = "123"`
- `#[1, 2, 3].[foldl]](#manual-Array___foldl) (s!"({·} {·})") "" = "((( 1) 2) 3)"`

def

```lean
[Array.foldlM.{u, v, w}]](#manual-Array___foldlM) {α : Type u} {β : Type v} {m : Type v → Type w}
  [[Monad]](#manual-Monad___mk) m] (f : β → α → m β) (init : β) (as : [Array]](#manual-Array___mk) α)
  (start : [Nat]](#manual-Nat___zero) := 0) (stop : [Nat]](#manual-Nat___zero) := as.[size]](#manual-Array___size)) : m β



[Array.foldlM.{u, v, w}]](#manual-Array___foldlM) {α : Type u}
  {β : Type v} {m : Type v → Type w}
  [[Monad]](#manual-Monad___mk) m] (f : β → α → m β) (init : β)
  (as : [Array]](#manual-Array___mk) α) (start : [Nat]](#manual-Nat___zero) := 0)
  (stop : [Nat]](#manual-Nat___zero) := as.[size]](#manual-Array___size)) : m β
```

Folds a monadic function over a list from the left, accumulating a value starting with `init`. The
accumulated value is combined with the each element of the list in order, using `f`.

The optional parameters `start` and `stop` control the region of the array to be folded. Folding
proceeds from `start` (inclusive) to `stop` (exclusive), so no folding occurs unless `start < stop`.
By default, the entire array is folded.

Examples:

```
example [Monad m] (f : α → β → m α) :
    Array.foldlM (m := m) f x₀ #[a, b, c] = (do
      let x₁ ← f x₀ a
      let x₂ ← f x₁ b
      let x₃ ← f x₂ c
      pure x₃)
  := by rfl
```

```
example [Monad m] (f : α → β → m α) :
    Array.foldlM (m := m) f x₀ #[a, b, c] (start := 1) = (do
      let x₁ ← f x₀ b
      let x₂ ← f x₁ c
      pure x₂)
  := by rfl
```

def

```lean
[Array.forM.{u, v, w}]](#manual-Array___forM) {α : Type u} {m : Type v → Type w} [[Monad]](#manual-Monad___mk) m]
  (f : α → m [PUnit]](#manual-PUnit___unit)) (as : [Array]](#manual-Array___mk) α) (start : [Nat]](#manual-Nat___zero) := 0)
  (stop : [Nat]](#manual-Nat___zero) := as.[size]](#manual-Array___size)) : m [PUnit]](#manual-PUnit___unit)



[Array.forM.{u, v, w}]](#manual-Array___forM) {α : Type u}
  {m : Type v → Type w} [[Monad]](#manual-Monad___mk) m]
  (f : α → m [PUnit]](#manual-PUnit___unit)) (as : [Array]](#manual-Array___mk) α)
  (start : [Nat]](#manual-Nat___zero) := 0)
  (stop : [Nat]](#manual-Nat___zero) := as.[size]](#manual-Array___size)) : m [PUnit]](#manual-PUnit___unit)
```

Applies the monadic action `f` to each element of an array, in order.

The optional parameters `start` and `stop` control the region of the array to which `f` should be
applied. Iteration proceeds from `start` (inclusive) to `stop` (exclusive), so `f` is not invoked
unless `start < stop`. By default, the entire array is used.

def

```lean
[Array.forRevM.{u, v, w}]](#manual-Array___forRevM) {α : Type u} {m : Type v → Type w} [[Monad]](#manual-Monad___mk) m]
  (f : α → m [PUnit]](#manual-PUnit___unit)) (as : [Array]](#manual-Array___mk) α) (start : [Nat]](#manual-Nat___zero) := as.[size]](#manual-Array___size))
  (stop : [Nat]](#manual-Nat___zero) := 0) : m [PUnit]](#manual-PUnit___unit)



[Array.forRevM.{u, v, w}]](#manual-Array___forRevM) {α : Type u}
  {m : Type v → Type w} [[Monad]](#manual-Monad___mk) m]
  (f : α → m [PUnit]](#manual-PUnit___unit)) (as : [Array]](#manual-Array___mk) α)
  (start : [Nat]](#manual-Nat___zero) := as.[size]](#manual-Array___size))
  (stop : [Nat]](#manual-Nat___zero) := 0) : m [PUnit]](#manual-PUnit___unit)
```

Applies the monadic action `f` to each element of an array from right to left, in reverse order.

The optional parameters `start` and `stop` control the region of the array to which `f` should be
applied. Iteration proceeds from `start` (exclusive) to `stop` (inclusive), so no `f` is not invoked
unless `start > stop`. By default, the entire array is used.

def

```lean
[Array.firstM.{u, v, w}]](#manual-Array___firstM) {β : Type v} {α : Type u} {m : Type v → Type w}
  [[Alternative]](#manual-Alternative___mk) m] (f : α → m β) (as : [Array]](#manual-Array___mk) α) : m β



[Array.firstM.{u, v, w}]](#manual-Array___firstM) {β : Type v}
  {α : Type u} {m : Type v → Type w}
  [[Alternative]](#manual-Alternative___mk) m] (f : α → m β)
  (as : [Array]](#manual-Array___mk) α) : m β
```

Maps `f` over the array and collects the results with `<|>`. The result for the end of the array is
`failure`.

Examples:

- `#[[], [1, 2], [], [2]].[firstM]](#manual-Array___firstM) [List.head?]](#manual-List___head___) = [some]](#manual-Option___none) 1`
- `#[[], [], []].[firstM]](#manual-Array___firstM) [List.head?]](#manual-List___head___) = [none]](#manual-Option___none)`
- `#[].[firstM]](#manual-Array___firstM) [List.head?]](#manual-List___head___) = [none]](#manual-Option___none)`

def

```lean
[Array.sum.{u_1}]](#manual-Array___sum) {α : Type u_1} [[Add]](#manual-Add___mk) α] [[Zero]](#manual-Zero___mk) α] : [Array]](#manual-Array___mk) α → α



[Array.sum.{u_1}]](#manual-Array___sum) {α : Type u_1} [[Add]](#manual-Add___mk) α]
  [[Zero]](#manual-Zero___mk) α] : [Array]](#manual-Array___mk) α → α
```

Computes the sum of the elements of an array.

Examples:

- `#[a, b, c].[sum]](#manual-Array___sum) = a + (b + (c + 0))`
- `#[1, 2, 5].[sum]](#manual-Array___sum) = 8`

#### 20.16.4.9. Transformation {#manual-The-Lean-Language-Reference--Basic-Types--Arrays--API-Reference--Transformation}

def

```lean
[Array.map.{u, v}]](#manual-Array___map) {α : Type u} {β : Type v} (f : α → β) (as : [Array]](#manual-Array___mk) α) :
  [Array]](#manual-Array___mk) β



[Array.map.{u, v}]](#manual-Array___map) {α : Type u} {β : Type v}
  (f : α → β) (as : [Array]](#manual-Array___mk) α) : [Array]](#manual-Array___mk) β
```

Applies a function to each element of the array, returning the resulting array of values.

Examples:

- `#[a, b, c].[map]](#manual-Array___map) f = #[f a, f b, f c]`
- `#[].[map]](#manual-Array___map) [Nat.succ]](#manual-Nat___zero) = #[]`
- `#["one", "two", "three"].[map]](#manual-Array___map) (·.[length]](#manual-String___length)) = #[3, 3, 5]`
- `#["one", "two", "three"].[map]](#manual-Array___map) (·.reverse) = #["eno", "owt", "eerht"]`

def

```lean
[Array.mapMono.{u_1}]](#manual-Array___mapMono) {α : Type u_1} (as : [Array]](#manual-Array___mk) α) (f : α → α) : [Array]](#manual-Array___mk) α



[Array.mapMono.{u_1}]](#manual-Array___mapMono) {α : Type u_1}
  (as : [Array]](#manual-Array___mk) α) (f : α → α) : [Array]](#manual-Array___mk) α
```

Applies a function to each element of an array, returning the array of results. The function is
monomorphic: it is required to return a value of the same type. The internal implementation uses
pointer equality, and does not allocate a new array if the result of each function call is
pointer-equal to its argument.

def

```lean
[Array.mapM.{u, v, w}]](#manual-Array___mapM) {α : Type u} {β : Type v} {m : Type v → Type w}
  [[Monad]](#manual-Monad___mk) m] (f : α → m β) (as : [Array]](#manual-Array___mk) α) : m ([Array]](#manual-Array___mk) β)



[Array.mapM.{u, v, w}]](#manual-Array___mapM) {α : Type u}
  {β : Type v} {m : Type v → Type w}
  [[Monad]](#manual-Monad___mk) m] (f : α → m β) (as : [Array]](#manual-Array___mk) α) :
  m ([Array]](#manual-Array___mk) β)
```

Applies the monadic action `f` to every element in the array, left-to-right, and returns the array
of results.

def

```lean
[Array.mapM'.{u_1, u_2, u_3}]](#manual-Array___mapM___) {m : Type u_1 → Type u_2} {α : Type u_3}
  {β : Type u_1} [[Monad]](#manual-Monad___mk) m] (f : α → m β) (as : [Array]](#manual-Array___mk) α) :
  m [{](https://lean-lang.org/doc/reference/latest/Basic-Types/Subtypes/#Subtype___mk) bs [//](https://lean-lang.org/doc/reference/latest/Basic-Types/Subtypes/#Subtype___mk) bs.[size]](#manual-Array___size) [=]](#manual-Eq___refl) as.[size]](#manual-Array___size) [}](https://lean-lang.org/doc/reference/latest/Basic-Types/Subtypes/#Subtype___mk)



[Array.mapM'.{u_1, u_2, u_3}]](#manual-Array___mapM___)
  {m : Type u_1 → Type u_2} {α : Type u_3}
  {β : Type u_1} [[Monad]](#manual-Monad___mk) m] (f : α → m β)
  (as : [Array]](#manual-Array___mk) α) :
  m [{](https://lean-lang.org/doc/reference/latest/Basic-Types/Subtypes/#Subtype___mk) bs [//](https://lean-lang.org/doc/reference/latest/Basic-Types/Subtypes/#Subtype___mk) bs.[size]](#manual-Array___size) [=]](#manual-Eq___refl) as.[size]](#manual-Array___size) [}](https://lean-lang.org/doc/reference/latest/Basic-Types/Subtypes/#Subtype___mk)
```

Applies the monadic action `f` to every element in the array, left-to-right, and returns the array
of results. Furthermore, the resulting array's type guarantees that it contains the same number of
elements as the input array.

def

```lean
[Array.mapMonoM.{u_1, u_2}]](#manual-Array___mapMonoM) {m : Type u_1 → Type u_2} {α : Type u_1}
  [[Monad]](#manual-Monad___mk) m] (as : [Array]](#manual-Array___mk) α) (f : α → m α) : m ([Array]](#manual-Array___mk) α)



[Array.mapMonoM.{u_1, u_2}]](#manual-Array___mapMonoM)
  {m : Type u_1 → Type u_2} {α : Type u_1}
  [[Monad]](#manual-Monad___mk) m] (as : [Array]](#manual-Array___mk) α) (f : α → m α) :
  m ([Array]](#manual-Array___mk) α)
```

Applies a monadic function to each element of an array, returning the array of results. The function is
monomorphic: it is required to return a value of the same type. The internal implementation uses
pointer equality, and does not allocate a new array if the result of each function call is
pointer-equal to its argument.

def

```lean
[Array.mapIdx.{u, v}]](#manual-Array___mapIdx) {α : Type u} {β : Type v} (f : [Nat]](#manual-Nat___zero) → α → β)
  (as : [Array]](#manual-Array___mk) α) : [Array]](#manual-Array___mk) β



[Array.mapIdx.{u, v}]](#manual-Array___mapIdx) {α : Type u}
  {β : Type v} (f : [Nat]](#manual-Nat___zero) → α → β)
  (as : [Array]](#manual-Array___mk) α) : [Array]](#manual-Array___mk) β
```

Applies a function to each element of the array along with the index at which that element is found,
returning the array of results.

`[Array.mapFinIdx]](#manual-Array___mapFinIdx)` is a variant that additionally provides the function with a proof that the index
is valid.

def

```lean
[Array.mapIdxM.{u, v, w}]](#manual-Array___mapIdxM) {α : Type u} {β : Type v} {m : Type v → Type w}
  [[Monad]](#manual-Monad___mk) m] (f : [Nat]](#manual-Nat___zero) → α → m β) (as : [Array]](#manual-Array___mk) α) : m ([Array]](#manual-Array___mk) β)



[Array.mapIdxM.{u, v, w}]](#manual-Array___mapIdxM) {α : Type u}
  {β : Type v} {m : Type v → Type w}
  [[Monad]](#manual-Monad___mk) m] (f : [Nat]](#manual-Nat___zero) → α → m β)
  (as : [Array]](#manual-Array___mk) α) : m ([Array]](#manual-Array___mk) β)
```

Applies the monadic action `f` to every element in the array, along with the element's index, from
left to right. Returns the array of results.

def

```lean
[Array.mapFinIdx.{u, v}]](#manual-Array___mapFinIdx) {α : Type u} {β : Type v} (as : [Array]](#manual-Array___mk) α)
  (f : (i : [Nat]](#manual-Nat___zero)) → α → i [<]](#manual-LT___mk) as.[size]](#manual-Array___size) → β) : [Array]](#manual-Array___mk) β



[Array.mapFinIdx.{u, v}]](#manual-Array___mapFinIdx) {α : Type u}
  {β : Type v} (as : [Array]](#manual-Array___mk) α)
  (f : (i : [Nat]](#manual-Nat___zero)) → α → i [<]](#manual-LT___mk) as.[size]](#manual-Array___size) → β) :
  [Array]](#manual-Array___mk) β
```

Applies a function to each element of the array along with the index at which that element is found,
returning the array of results. In addition to the index, the function is also provided with a proof
that the index is valid.

`[Array.mapIdx]](#manual-Array___mapIdx)` is a variant that does not provide the function with evidence that the index is
valid.

def

```lean
[Array.mapFinIdxM.{u, v, w}]](#manual-Array___mapFinIdxM) {α : Type u} {β : Type v}
  {m : Type v → Type w} [[Monad]](#manual-Monad___mk) m] (as : [Array]](#manual-Array___mk) α)
  (f : (i : [Nat]](#manual-Nat___zero)) → α → i [<]](#manual-LT___mk) as.[size]](#manual-Array___size) → m β) : m ([Array]](#manual-Array___mk) β)



[Array.mapFinIdxM.{u, v, w}]](#manual-Array___mapFinIdxM) {α : Type u}
  {β : Type v} {m : Type v → Type w}
  [[Monad]](#manual-Monad___mk) m] (as : [Array]](#manual-Array___mk) α)
  (f :
    (i : [Nat]](#manual-Nat___zero)) → α → i [<]](#manual-LT___mk) as.[size]](#manual-Array___size) → m β) :
  m ([Array]](#manual-Array___mk) β)
```

Applies the monadic action `f` to every element in the array, along with the element's index and a
proof that the index is in bounds, from left to right. Returns the array of results.

def

```lean
[Array.flatMap.{u, u_1}]](#manual-Array___flatMap) {α : Type u} {β : Type u_1} (f : α → [Array]](#manual-Array___mk) β)
  (as : [Array]](#manual-Array___mk) α) : [Array]](#manual-Array___mk) β



[Array.flatMap.{u, u_1}]](#manual-Array___flatMap) {α : Type u}
  {β : Type u_1} (f : α → [Array]](#manual-Array___mk) β)
  (as : [Array]](#manual-Array___mk) α) : [Array]](#manual-Array___mk) β
```

Applies a function that returns an array to each element of an array. The resulting arrays are
appended.

Examples:

- `#[2, 3, 2].[flatMap]](#manual-Array___flatMap) [Array.range]](#manual-Array___range) = #[0, 1, 0, 1, 2, 0, 1]`
- `#[['a', 'b'], ['c', 'd', 'e']].[flatMap]](#manual-Array___flatMap) [List.toArray]](#manual-List___toArray) = #['a', 'b', 'c', 'd', 'e']`

def

```lean
[Array.flatMapM.{u, u_1, u_2}]](#manual-Array___flatMapM) {α : Type u} {m : Type u_1 → Type u_2}
  {β : Type u_1} [[Monad]](#manual-Monad___mk) m] (f : α → m ([Array]](#manual-Array___mk) β)) (as : [Array]](#manual-Array___mk) α) :
  m ([Array]](#manual-Array___mk) β)



[Array.flatMapM.{u, u_1, u_2}]](#manual-Array___flatMapM) {α : Type u}
  {m : Type u_1 → Type u_2} {β : Type u_1}
  [[Monad]](#manual-Monad___mk) m] (f : α → m ([Array]](#manual-Array___mk) β))
  (as : [Array]](#manual-Array___mk) α) : m ([Array]](#manual-Array___mk) β)
```

Applies a monadic function that returns an array to each element of an array, from left to right.
The resulting arrays are appended.

def

```lean
[Array.zip.{u, u_1}]](#manual-Array___zip) {α : Type u} {β : Type u_1} (as : [Array]](#manual-Array___mk) α)
  (bs : [Array]](#manual-Array___mk) β) : [Array]](#manual-Array___mk) [(]](#manual-Prod___mk)α [×]](#manual-Prod___mk) β[)]](#manual-Prod___mk)



[Array.zip.{u, u_1}]](#manual-Array___zip) {α : Type u}
  {β : Type u_1} (as : [Array]](#manual-Array___mk) α)
  (bs : [Array]](#manual-Array___mk) β) : [Array]](#manual-Array___mk) [(]](#manual-Prod___mk)α [×]](#manual-Prod___mk) β[)]](#manual-Prod___mk)
```

Combines two arrays into an array of pairs in which the first and second components are the
corresponding elements of each input array. The resulting array is the length of the shorter of the
input arrays.

Examples:

- `#["Mon", "Tue", "Wed"].[zip]](#manual-Array___zip) #[1, 2, 3] = #[("Mon", 1), ("Tue", 2), ("Wed", 3)]`
- `#["Mon", "Tue", "Wed"].[zip]](#manual-Array___zip) #[1, 2] = #[("Mon", 1), ("Tue", 2)]`
- `#[x₁, x₂, x₃].[zip]](#manual-Array___zip) #[y₁, y₂, y₃, y₄] = #[(x₁, y₁), (x₂, y₂), (x₃, y₃)]`

def

```lean
[Array.zipWith.{u, u_1, u_2}]](#manual-Array___zipWith) {α : Type u} {β : Type u_1} {γ : Type u_2}
  (f : α → β → γ) (as : [Array]](#manual-Array___mk) α) (bs : [Array]](#manual-Array___mk) β) : [Array]](#manual-Array___mk) γ



[Array.zipWith.{u, u_1, u_2}]](#manual-Array___zipWith) {α : Type u}
  {β : Type u_1} {γ : Type u_2}
  (f : α → β → γ) (as : [Array]](#manual-Array___mk) α)
  (bs : [Array]](#manual-Array___mk) β) : [Array]](#manual-Array___mk) γ
```

Applies a function to the corresponding elements of two arrays, stopping at the end of the shorter
array.

Examples:

- `#[1, 2].[zipWith]](#manual-Array___zipWith) (· + ·) #[5, 6] = #[6, 8]`
- `#[1, 2, 3].[zipWith]](#manual-Array___zipWith) (· + ·) #[5, 6, 10] = #[6, 8, 13]`
- `#[].[zipWith]](#manual-Array___zipWith) (· + ·) #[5, 6] = #[]`
- `#[x₁, x₂, x₃].[zipWith]](#manual-Array___zipWith) f #[y₁, y₂, y₃, y₄] = #[f x₁ y₁, f x₂ y₂, f x₃ y₃]`

def

```lean
[Array.zipWithAll.{u, u_1, u_2}]](#manual-Array___zipWithAll) {α : Type u} {β : Type u_1}
  {γ : Type u_2} (f : [Option]](#manual-Option___none) α → [Option]](#manual-Option___none) β → γ) (as : [Array]](#manual-Array___mk) α)
  (bs : [Array]](#manual-Array___mk) β) : [Array]](#manual-Array___mk) γ



[Array.zipWithAll.{u, u_1, u_2}]](#manual-Array___zipWithAll)
  {α : Type u} {β : Type u_1}
  {γ : Type u_2}
  (f : [Option]](#manual-Option___none) α → [Option]](#manual-Option___none) β → γ)
  (as : [Array]](#manual-Array___mk) α) (bs : [Array]](#manual-Array___mk) β) : [Array]](#manual-Array___mk) γ
```

Applies a function to the corresponding elements of both arrays, stopping when there are no more
elements in either array. If one array is shorter than the other, the function is passed `[none]](#manual-Option___none)` for
the missing elements.

Examples:

- `#[1, 6].[zipWithAll]](#manual-Array___zipWithAll) [min]](#manual-Min___mk) #[5, 2] = #[[some]](#manual-Option___none) 1, [some]](#manual-Option___none) 2]`
- `#[1, 2, 3].[zipWithAll]](#manual-Array___zipWithAll) [Prod.mk]](#manual-Prod___mk) #[5, 6] = #[([some]](#manual-Option___none) 1, [some]](#manual-Option___none) 5), ([some]](#manual-Option___none) 2, [some]](#manual-Option___none) 6), ([some]](#manual-Option___none) 3, [none]](#manual-Option___none))]`
- `#[x₁, x₂].[zipWithAll]](#manual-Array___zipWithAll) f #[y] = #[f ([some]](#manual-Option___none) x₁) ([some]](#manual-Option___none) y), f ([some]](#manual-Option___none) x₂) [none]](#manual-Option___none)]`

def

```lean
[Array.zipIdx.{u}]](#manual-Array___zipIdx) {α : Type u} (xs : [Array]](#manual-Array___mk) α) (start : [Nat]](#manual-Nat___zero) := 0) :
  [Array]](#manual-Array___mk) [(]](#manual-Prod___mk)α [×]](#manual-Prod___mk) [Nat]](#manual-Nat___zero)[)]](#manual-Prod___mk)



[Array.zipIdx.{u}]](#manual-Array___zipIdx) {α : Type u}
  (xs : [Array]](#manual-Array___mk) α) (start : [Nat]](#manual-Nat___zero) := 0) :
  [Array]](#manual-Array___mk) [(]](#manual-Prod___mk)α [×]](#manual-Prod___mk) [Nat]](#manual-Nat___zero)[)]](#manual-Prod___mk)
```

Pairs each element of an array with its index, optionally starting from an index other than `0`.

Examples:

- `#[a, b, c].[zipIdx]](#manual-Array___zipIdx) = #[(a, 0), (b, 1), (c, 2)]`
- `#[a, b, c].[zipIdx]](#manual-Array___zipIdx) 5 = #[(a, 5), (b, 6), (c, 7)]`

def

```lean
[Array.unzip.{u, u_1}]](#manual-Array___unzip) {α : Type u} {β : Type u_1} (as : [Array]](#manual-Array___mk) [(]](#manual-Prod___mk)α [×]](#manual-Prod___mk) β[)]](#manual-Prod___mk)) :
  [Array]](#manual-Array___mk) α [×]](#manual-Prod___mk) [Array]](#manual-Array___mk) β



[Array.unzip.{u, u_1}]](#manual-Array___unzip) {α : Type u}
  {β : Type u_1} (as : [Array]](#manual-Array___mk) [(]](#manual-Prod___mk)α [×]](#manual-Prod___mk) β[)]](#manual-Prod___mk)) :
  [Array]](#manual-Array___mk) α [×]](#manual-Prod___mk) [Array]](#manual-Array___mk) β
```

Separates an array of pairs into two arrays that contain the respective first and second components.

Examples:

- `#[("Monday", 1), ("Tuesday", 2)].[unzip]](#manual-Array___unzip) = (#["Monday", "Tuesday"], #[1, 2])`
- `#[(x₁, y₁), (x₂, y₂), (x₃, y₃)].[unzip]](#manual-Array___unzip) = (#[x₁, x₂, x₃], #[y₁, y₂, y₃])`
- `(#[] : Array (Nat × String)).unzip = ((#[], #[]) : List Nat × List String)`

#### 20.16.4.10. Filtering {#manual-The-Lean-Language-Reference--Basic-Types--Arrays--API-Reference--Filtering}

def

```lean
[Array.filter.{u}]](#manual-Array___filter) {α : Type u} (p : α → [Bool]](#manual-Bool___false)) (as : [Array]](#manual-Array___mk) α)
  (start : [Nat]](#manual-Nat___zero) := 0) (stop : [Nat]](#manual-Nat___zero) := as.[size]](#manual-Array___size)) : [Array]](#manual-Array___mk) α



[Array.filter.{u}]](#manual-Array___filter) {α : Type u}
  (p : α → [Bool]](#manual-Bool___false)) (as : [Array]](#manual-Array___mk) α)
  (start : [Nat]](#manual-Nat___zero) := 0)
  (stop : [Nat]](#manual-Nat___zero) := as.[size]](#manual-Array___size)) : [Array]](#manual-Array___mk) α
```

Returns the array of elements in `as` for which `p` returns `[true]](#manual-Bool___false)`.

Only elements from `start` (inclusive) to `stop` (exclusive) are considered. Elements outside that
range are discarded. By default, the entire array is considered.

Examples:

- `#[1, 2, 5, 2, 7, 7].[filter]](#manual-Array___filter) (· > 2) = #[5, 7, 7]`
- `#[1, 2, 5, 2, 7, 7].[filter]](#manual-Array___filter) (fun _ => [false]](#manual-Bool___false)) = #[]`
- `#[1, 2, 5, 2, 7, 7].[filter]](#manual-Array___filter) (fun _ => [true]](#manual-Bool___false)) = #[1, 2, 5, 2, 7, 7]`
- `#[1, 2, 5, 2, 7, 7].[filter]](#manual-Array___filter) (· > 2) (start := 3) = #[7, 7]`
- `#[1, 2, 5, 2, 7, 7].[filter]](#manual-Array___filter) (fun _ => [true]](#manual-Bool___false)) (start := 3) = #[2, 7, 7]`
- `#[1, 2, 5, 2, 7, 7].[filter]](#manual-Array___filter) (fun _ => [true]](#manual-Bool___false)) (stop := 3) = #[1, 2, 5]`

def

```lean
[Array.filterM.{u_1}]](#manual-Array___filterM) {m : Type → Type u_1} {α : Type} [[Monad]](#manual-Monad___mk) m]
  (p : α → m [Bool]](#manual-Bool___false)) (as : [Array]](#manual-Array___mk) α) (start : [Nat]](#manual-Nat___zero) := 0)
  (stop : [Nat]](#manual-Nat___zero) := as.[size]](#manual-Array___size)) : m ([Array]](#manual-Array___mk) α)



[Array.filterM.{u_1}]](#manual-Array___filterM) {m : Type → Type u_1}
  {α : Type} [[Monad]](#manual-Monad___mk) m] (p : α → m [Bool]](#manual-Bool___false))
  (as : [Array]](#manual-Array___mk) α) (start : [Nat]](#manual-Nat___zero) := 0)
  (stop : [Nat]](#manual-Nat___zero) := as.[size]](#manual-Array___size)) : m ([Array]](#manual-Array___mk) α)
```

Applies the monadic predicate `p` to every element in the array, in order from left to right, and
returns the array of elements for which `p` returns `[true]](#manual-Bool___false)`.

Only elements from `start` (inclusive) to `stop` (exclusive) are considered. Elements outside that
range are discarded. By default, the entire array is checked.

Example:

```lean
[#eval]](#manual-Lean___Parser___Command___eval) #[1, 2, 5, 2, 7, 7].[filterM]](#manual-Array___filterM) fun x => [do]](#manual-Lean___Parser___Term___do)
[IO.println](https://lean-lang.org/doc/reference/latest/IO/Console-Output/#IO___println) s!"Checking {x}"
return x < 3
```

```lean
Checking 1
Checking 2
Checking 5
Checking 2
Checking 7
Checking 7
```

```lean
#[1, 2, 2]
```

def

```lean
[Array.filterRevM.{u_1}]](#manual-Array___filterRevM) {m : Type → Type u_1} {α : Type} [[Monad]](#manual-Monad___mk) m]
  (p : α → m [Bool]](#manual-Bool___false)) (as : [Array]](#manual-Array___mk) α) (start : [Nat]](#manual-Nat___zero) := as.[size]](#manual-Array___size))
  (stop : [Nat]](#manual-Nat___zero) := 0) : m ([Array]](#manual-Array___mk) α)



[Array.filterRevM.{u_1}]](#manual-Array___filterRevM)
  {m : Type → Type u_1} {α : Type}
  [[Monad]](#manual-Monad___mk) m] (p : α → m [Bool]](#manual-Bool___false))
  (as : [Array]](#manual-Array___mk) α) (start : [Nat]](#manual-Nat___zero) := as.[size]](#manual-Array___size))
  (stop : [Nat]](#manual-Nat___zero) := 0) : m ([Array]](#manual-Array___mk) α)
```

Applies the monadic predicate `p` on every element in the array in reverse order, from right to
left, and returns those elements for which `p` returns `[true]](#manual-Bool___false)`. The elements of the returned list are
in the same order as in the input list.

Only elements from `start` (exclusive) to `stop` (inclusive) are considered. Elements outside that
range are discarded. Because the array is examined in reverse order, elements are only examined when
`start > stop`. By default, the entire array is considered.

Example:

```lean
[#eval]](#manual-Lean___Parser___Command___eval) #[1, 2, 5, 2, 7, 7].[filterRevM]](#manual-Array___filterRevM) fun x => [do]](#manual-Lean___Parser___Term___do)
[IO.println](https://lean-lang.org/doc/reference/latest/IO/Console-Output/#IO___println) s!"Checking {x}"
return x < 3
```

```lean
Checking 7
Checking 7
Checking 2
Checking 5
Checking 2
Checking 1
```

```lean
#[1, 2, 2]
```

def

```lean
[Array.filterMap.{u, u_1}]](#manual-Array___filterMap) {α : Type u} {β : Type u_1} (f : α → [Option]](#manual-Option___none) β)
  (as : [Array]](#manual-Array___mk) α) (start : [Nat]](#manual-Nat___zero) := 0) (stop : [Nat]](#manual-Nat___zero) := as.[size]](#manual-Array___size)) : [Array]](#manual-Array___mk) β



[Array.filterMap.{u, u_1}]](#manual-Array___filterMap) {α : Type u}
  {β : Type u_1} (f : α → [Option]](#manual-Option___none) β)
  (as : [Array]](#manual-Array___mk) α) (start : [Nat]](#manual-Nat___zero) := 0)
  (stop : [Nat]](#manual-Nat___zero) := as.[size]](#manual-Array___size)) : [Array]](#manual-Array___mk) β
```

Applies a function that returns an `[Option]](#manual-Option___none)` to each element of an array, collecting the non-`[none]](#manual-Option___none)`
values.

Example:

```lean
[#eval]](#manual-Lean___Parser___Command___eval) #[1, 2, 5, 2, 7, 7].[filterMap]](#manual-Array___filterMap) fun x =>
[if]](#manual-termIfThenElse) x > 2 [then]](#manual-termIfThenElse) [some]](#manual-Option___none) (2 * x) [else]](#manual-termIfThenElse) [none]](#manual-Option___none)
```

```lean
#[10, 14, 14]
```

def

```lean
[Array.filterMapM.{u, u_1, u_2}]](#manual-Array___filterMapM) {α : Type u} {m : Type u_1 → Type u_2}
  {β : Type u_1} [[Monad]](#manual-Monad___mk) m] (f : α → m ([Option]](#manual-Option___none) β)) (as : [Array]](#manual-Array___mk) α)
  (start : [Nat]](#manual-Nat___zero) := 0) (stop : [Nat]](#manual-Nat___zero) := as.[size]](#manual-Array___size)) : m ([Array]](#manual-Array___mk) β)



[Array.filterMapM.{u, u_1, u_2}]](#manual-Array___filterMapM)
  {α : Type u} {m : Type u_1 → Type u_2}
  {β : Type u_1} [[Monad]](#manual-Monad___mk) m]
  (f : α → m ([Option]](#manual-Option___none) β)) (as : [Array]](#manual-Array___mk) α)
  (start : [Nat]](#manual-Nat___zero) := 0)
  (stop : [Nat]](#manual-Nat___zero) := as.[size]](#manual-Array___size)) : m ([Array]](#manual-Array___mk) β)
```

Applies a monadic function that returns an `[Option]](#manual-Option___none)` to each element of an array, collecting the
non-`[none]](#manual-Option___none)` values.

Only elements from `start` (inclusive) to `stop` (exclusive) are considered. Elements outside that
range are discarded. By default, the entire array is considered.

Example:

```lean
[#eval]](#manual-Lean___Parser___Command___eval) #[1, 2, 5, 2, 7, 7].[filterMapM]](#manual-Array___filterMapM) fun x => [do]](#manual-Lean___Parser___Term___do)
[IO.println](https://lean-lang.org/doc/reference/latest/IO/Console-Output/#IO___println) s!"Examining {x}"
if x > 2 then return [some]](#manual-Option___none) (2 * x)
else return [none]](#manual-Option___none)
```

```lean
Examining 1
Examining 2
Examining 5
Examining 2
Examining 7
Examining 7
```

```lean
#[10, 14, 14]
```

def

```lean
[Array.filterSepElems]](#manual-Array___filterSepElems) (a : [Array]](#manual-Array___mk) [Lean.Syntax](https://lean-lang.org/doc/reference/latest/Notations-and-Macros/Defining-New-Syntax/#Lean___Syntax___missing)) (p : [Lean.Syntax](https://lean-lang.org/doc/reference/latest/Notations-and-Macros/Defining-New-Syntax/#Lean___Syntax___missing) → [Bool]](#manual-Bool___false)) :
  [Array]](#manual-Array___mk) [Lean.Syntax](https://lean-lang.org/doc/reference/latest/Notations-and-Macros/Defining-New-Syntax/#Lean___Syntax___missing)



[Array.filterSepElems]](#manual-Array___filterSepElems)
  (a : [Array]](#manual-Array___mk) [Lean.Syntax](https://lean-lang.org/doc/reference/latest/Notations-and-Macros/Defining-New-Syntax/#Lean___Syntax___missing))
  (p : [Lean.Syntax](https://lean-lang.org/doc/reference/latest/Notations-and-Macros/Defining-New-Syntax/#Lean___Syntax___missing) → [Bool]](#manual-Bool___false)) :
  [Array]](#manual-Array___mk) [Lean.Syntax](https://lean-lang.org/doc/reference/latest/Notations-and-Macros/Defining-New-Syntax/#Lean___Syntax___missing)
```

Filters an array of syntax, treating every other element as a separator rather than an element to
test with the predicate `p`. The resulting array contains the tested elements for which `p` returns
`[true]](#manual-Bool___false)`, separated by the corresponding separator elements.

def

```lean
[Array.filterSepElemsM]](#manual-Array___filterSepElemsM) {m : Type → Type} [[Monad]](#manual-Monad___mk) m]
  (a : [Array]](#manual-Array___mk) [Lean.Syntax](https://lean-lang.org/doc/reference/latest/Notations-and-Macros/Defining-New-Syntax/#Lean___Syntax___missing)) (p : [Lean.Syntax](https://lean-lang.org/doc/reference/latest/Notations-and-Macros/Defining-New-Syntax/#Lean___Syntax___missing) → m [Bool]](#manual-Bool___false)) :
  m ([Array]](#manual-Array___mk) [Lean.Syntax](https://lean-lang.org/doc/reference/latest/Notations-and-Macros/Defining-New-Syntax/#Lean___Syntax___missing))



[Array.filterSepElemsM]](#manual-Array___filterSepElemsM) {m : Type → Type}
  [[Monad]](#manual-Monad___mk) m] (a : [Array]](#manual-Array___mk) [Lean.Syntax](https://lean-lang.org/doc/reference/latest/Notations-and-Macros/Defining-New-Syntax/#Lean___Syntax___missing))
  (p : [Lean.Syntax](https://lean-lang.org/doc/reference/latest/Notations-and-Macros/Defining-New-Syntax/#Lean___Syntax___missing) → m [Bool]](#manual-Bool___false)) :
  m ([Array]](#manual-Array___mk) [Lean.Syntax](https://lean-lang.org/doc/reference/latest/Notations-and-Macros/Defining-New-Syntax/#Lean___Syntax___missing))
```

Filters an array of syntax, treating every other element as a separator rather than an element to
test with the monadic predicate `p`. The resulting array contains the tested elements for which `p`
returns `[true]](#manual-Bool___false)`, separated by the corresponding separator elements.

#### 20.16.4.11. Partitioning {#manual-The-Lean-Language-Reference--Basic-Types--Arrays--API-Reference--Partitioning}

def

```lean
[Array.partition.{u}]](#manual-Array___partition) {α : Type u} (p : α → [Bool]](#manual-Bool___false)) (as : [Array]](#manual-Array___mk) α) :
  [Array]](#manual-Array___mk) α [×]](#manual-Prod___mk) [Array]](#manual-Array___mk) α



[Array.partition.{u}]](#manual-Array___partition) {α : Type u}
  (p : α → [Bool]](#manual-Bool___false)) (as : [Array]](#manual-Array___mk) α) :
  [Array]](#manual-Array___mk) α [×]](#manual-Prod___mk) [Array]](#manual-Array___mk) α
```

Returns a pair of arrays that together contain all the elements of `as`. The first array contains
those elements for which `p` returns `[true]](#manual-Bool___false)`, and the second contains those for which `p` returns
`[false]](#manual-Bool___false)`.

`as.[partition]](#manual-Array___partition) p` is equivalent to `(as.[filter]](#manual-Array___filter) p, as.[filter]](#manual-Array___filter) ([not]](#manual-Bool___not) ∘ p))`, but it is
more efficient since it only has to do one pass over the array.

Examples:

- `#[1, 2, 5, 2, 7, 7].[partition]](#manual-Array___partition) (· > 2) = (#[5, 7, 7], #[1, 2, 2])`
- `#[1, 2, 5, 2, 7, 7].[partition]](#manual-Array___partition) (fun _ => [false]](#manual-Bool___false)) = (#[], #[1, 2, 5, 2, 7, 7])`
- `#[1, 2, 5, 2, 7, 7].[partition]](#manual-Array___partition) (fun _ => [true]](#manual-Bool___false)) = (#[1, 2, 5, 2, 7, 7], #[])`

def

```lean
[Array.groupByKey.{u, v}]](#manual-Array___groupByKey) {α : Type u} {β : Type v} [[BEq]](#manual-BEq___mk) α] [[Hashable]](#manual-Hashable___mk) α]
  (key : β → α) (xs : [Array]](#manual-Array___mk) β) : [Std.HashMap](https://lean-lang.org/doc/reference/latest/Basic-Types/Maps-and-Sets/#Std___HashMap) α ([Array]](#manual-Array___mk) β)



[Array.groupByKey.{u, v}]](#manual-Array___groupByKey) {α : Type u}
  {β : Type v} [[BEq]](#manual-BEq___mk) α] [[Hashable]](#manual-Hashable___mk) α]
  (key : β → α) (xs : [Array]](#manual-Array___mk) β) :
  [Std.HashMap](https://lean-lang.org/doc/reference/latest/Basic-Types/Maps-and-Sets/#Std___HashMap) α ([Array]](#manual-Array___mk) β)
```

Groups the elements of an array `xs` according to the function `key`, returning a hash map in which
each group is associated with its key. Groups preserve the relative order of elements in `xs`.

Example:

```lean
[#eval]](#manual-Lean___Parser___Command___eval) #[0, 1, 2, 3, 4, 5, 6].[groupByKey]](#manual-Array___groupByKey) (· % 2)
```

```lean
[Std.HashMap.ofList](https://lean-lang.org/doc/reference/latest/Basic-Types/Maps-and-Sets/#Std___HashMap___ofList) [(0, #[0, 2, 4, 6]), (1, #[1, 3, 5])]
```

#### 20.16.4.12. Element Predicates {#manual-The-Lean-Language-Reference--Basic-Types--Arrays--API-Reference--Element-Predicates}

def

```lean
[Array.contains.{u}]](#manual-Array___contains) {α : Type u} [[BEq]](#manual-BEq___mk) α] (as : [Array]](#manual-Array___mk) α) (a : α) : [Bool]](#manual-Bool___false)



[Array.contains.{u}]](#manual-Array___contains) {α : Type u} [[BEq]](#manual-BEq___mk) α]
  (as : [Array]](#manual-Array___mk) α) (a : α) : [Bool]](#manual-Bool___false)
```

Checks whether `a` is an element of `as`, using `==` to compare elements.

`[Array.elem]](#manual-Array___elem)` is a synonym that takes the element before the array.

Examples:

- `#[1, 4, 2, 3, 3, 7].[contains]](#manual-Array___contains) 3 = [true]](#manual-Bool___false)`
- `[Array.contains]](#manual-Array___contains) #[1, 4, 2, 3, 3, 7] 5 = [false]](#manual-Bool___false)`

def

```lean
[Array.elem.{u}]](#manual-Array___elem) {α : Type u} [[BEq]](#manual-BEq___mk) α] (a : α) (as : [Array]](#manual-Array___mk) α) : [Bool]](#manual-Bool___false)



[Array.elem.{u}]](#manual-Array___elem) {α : Type u} [[BEq]](#manual-BEq___mk) α]
  (a : α) (as : [Array]](#manual-Array___mk) α) : [Bool]](#manual-Bool___false)
```

Checks whether `a` is an element of `as`, using `==` to compare elements.

`[Array.contains]](#manual-Array___contains)` is a synonym that takes the array before the element.

For verification purposes, `[Array.elem]](#manual-Array___elem)` is simplified to `[Array.contains]](#manual-Array___contains)`.

Example:

- `[Array.elem]](#manual-Array___elem) 3 #[1, 4, 2, 3, 3, 7] = [true]](#manual-Bool___false)`
- `[Array.elem]](#manual-Array___elem) 5 #[1, 4, 2, 3, 3, 7] = [false]](#manual-Bool___false)`

def

```lean
[Array.find?.{u}]](#manual-Array___find___) {α : Type u} (p : α → [Bool]](#manual-Bool___false)) (as : [Array]](#manual-Array___mk) α) : [Option]](#manual-Option___none) α



[Array.find?.{u}]](#manual-Array___find___) {α : Type u}
  (p : α → [Bool]](#manual-Bool___false)) (as : [Array]](#manual-Array___mk) α) : [Option]](#manual-Option___none) α
```

Returns the first element of the array for which the predicate `p` returns `[true]](#manual-Bool___false)`, or `[none]](#manual-Option___none)` if no
such element is found.

Examples:

- `#[7, 6, 5, 8, 1, 2, 6].[find?]](#manual-Array___find___) (· < 5) = [some]](#manual-Option___none) 1`
- `#[7, 6, 5, 8, 1, 2, 6].[find?]](#manual-Array___find___) (· < 1) = [none]](#manual-Option___none)`

def

```lean
[Array.findRev?]](#manual-Array___findRev___) {α : Type} (p : α → [Bool]](#manual-Bool___false)) (as : [Array]](#manual-Array___mk) α) : [Option]](#manual-Option___none) α



[Array.findRev?]](#manual-Array___findRev___) {α : Type} (p : α → [Bool]](#manual-Bool___false))
  (as : [Array]](#manual-Array___mk) α) : [Option]](#manual-Option___none) α
```

Returns the last element of the array for which the predicate `p` returns `[true]](#manual-Bool___false)`, or `[none]](#manual-Option___none)` if no
such element is found.

Examples:

- `#[7, 6, 5, 8, 1, 2, 6].[findRev?]](#manual-Array___findRev___) (· < 5) = [some]](#manual-Option___none) 2`
- `#[7, 6, 5, 8, 1, 2, 6].[findRev?]](#manual-Array___findRev___) (· < 1) = [none]](#manual-Option___none)`

def

```lean
[Array.findIdx.{u}]](#manual-Array___findIdx) {α : Type u} (p : α → [Bool]](#manual-Bool___false)) (as : [Array]](#manual-Array___mk) α) : [Nat]](#manual-Nat___zero)



[Array.findIdx.{u}]](#manual-Array___findIdx) {α : Type u}
  (p : α → [Bool]](#manual-Bool___false)) (as : [Array]](#manual-Array___mk) α) : [Nat]](#manual-Nat___zero)
```

Returns the index of the first element for which `p` returns `[true]](#manual-Bool___false)`, or the size of the array if
there is no such element.

Examples:

- `#[7, 6, 5, 8, 1, 2, 6].[findIdx]](#manual-Array___findIdx) (· < 5) = 4`
- `#[7, 6, 5, 8, 1, 2, 6].[findIdx]](#manual-Array___findIdx) (· < 1) = 7`

def

```lean
[Array.findIdx?.{u}]](#manual-Array___findIdx___) {α : Type u} (p : α → [Bool]](#manual-Bool___false)) (as : [Array]](#manual-Array___mk) α) :
  [Option]](#manual-Option___none) [Nat]](#manual-Nat___zero)



[Array.findIdx?.{u}]](#manual-Array___findIdx___) {α : Type u}
  (p : α → [Bool]](#manual-Bool___false)) (as : [Array]](#manual-Array___mk) α) :
  [Option]](#manual-Option___none) [Nat]](#manual-Nat___zero)
```

Returns the index of the first element for which `p` returns `[true]](#manual-Bool___false)`, or `[none]](#manual-Option___none)` if there is no such
element.

Examples:

- `#[7, 6, 5, 8, 1, 2, 6].[findIdx]](#manual-Array___findIdx) (· < 5) = [some]](#manual-Option___none) 4`
- `#[7, 6, 5, 8, 1, 2, 6].[findIdx]](#manual-Array___findIdx) (· < 1) = [none]](#manual-Option___none)`

def

```lean
[Array.findIdxM?.{u, u_1}]](#manual-Array___findIdxM___) {α : Type u} {m : Type → Type u_1} [[Monad]](#manual-Monad___mk) m]
  (p : α → m [Bool]](#manual-Bool___false)) (as : [Array]](#manual-Array___mk) α) : m ([Option]](#manual-Option___none) [Nat]](#manual-Nat___zero))



[Array.findIdxM?.{u, u_1}]](#manual-Array___findIdxM___) {α : Type u}
  {m : Type → Type u_1} [[Monad]](#manual-Monad___mk) m]
  (p : α → m [Bool]](#manual-Bool___false)) (as : [Array]](#manual-Array___mk) α) :
  m ([Option]](#manual-Option___none) [Nat]](#manual-Nat___zero))
```

Finds the index of the first element of an array for which the monadic predicate `p` returns `[true]](#manual-Bool___false)`.
Elements are examined in order from left to right, and the search is terminated when an element that
satisfies `p` is found. If no such element exists in the array, then `[none]](#manual-Option___none)` is returned.

def

```lean
[Array.findFinIdx?.{u}]](#manual-Array___findFinIdx___) {α : Type u} (p : α → [Bool]](#manual-Bool___false)) (as : [Array]](#manual-Array___mk) α) :
  [Option]](#manual-Option___none) ([Fin]](#manual-Fin___mk) as.[size]](#manual-Array___size))



[Array.findFinIdx?.{u}]](#manual-Array___findFinIdx___) {α : Type u}
  (p : α → [Bool]](#manual-Bool___false)) (as : [Array]](#manual-Array___mk) α) :
  [Option]](#manual-Option___none) ([Fin]](#manual-Fin___mk) as.[size]](#manual-Array___size))
```

Returns the index of the first element for which `p` returns `[true]](#manual-Bool___false)`, or `[none]](#manual-Option___none)` if there is no such
element. The index is returned as a `[Fin]](#manual-Fin___mk)`, which guarantees that it is in bounds.

Examples:

- `#[7, 6, 5, 8, 1, 2, 6].[findFinIdx?]](#manual-Array___findFinIdx___) (· < 5) = [some]](#manual-Option___none) (4 : [Fin]](#manual-Fin___mk) 7)`
- `#[7, 6, 5, 8, 1, 2, 6].[findFinIdx?]](#manual-Array___findFinIdx___) (· < 1) = [none]](#manual-Option___none)`

def

```lean
[Array.findM?.{u_1}]](#manual-Array___findM___) {m : Type → Type u_1} {α : Type} [[Monad]](#manual-Monad___mk) m]
  (p : α → m [Bool]](#manual-Bool___false)) (as : [Array]](#manual-Array___mk) α) : m ([Option]](#manual-Option___none) α)



[Array.findM?.{u_1}]](#manual-Array___findM___) {m : Type → Type u_1}
  {α : Type} [[Monad]](#manual-Monad___mk) m] (p : α → m [Bool]](#manual-Bool___false))
  (as : [Array]](#manual-Array___mk) α) : m ([Option]](#manual-Option___none) α)
```

Returns the first element of the array for which the monadic predicate `p` returns `[true]](#manual-Bool___false)`, or `[none]](#manual-Option___none)`
if no such element is found. Elements of the array are checked in order.

The monad `m` is restricted to `Type → Type` to avoid needing to use `[ULift]](#manual-ULift___up) [Bool]](#manual-Bool___false)` in `p`'s type.

Example:

```lean
[#eval]](#manual-Lean___Parser___Command___eval) #[7, 6, 5, 8, 1, 2, 6].[findM?]](#manual-Array___findM___) fun i => [do]](#manual-Lean___Parser___Term___do)
if i < 5 then
return [true]](#manual-Bool___false)
if i ≤ 6 then
[IO.println](https://lean-lang.org/doc/reference/latest/IO/Console-Output/#IO___println) s!"Almost! {i}"
return [false]](#manual-Bool___false)
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
[Array.findRevM?.{w}]](#manual-Array___findRevM___) {α : Type} {m : Type → Type w} [[Monad]](#manual-Monad___mk) m]
  (p : α → m [Bool]](#manual-Bool___false)) (as : [Array]](#manual-Array___mk) α) : m ([Option]](#manual-Option___none) α)



[Array.findRevM?.{w}]](#manual-Array___findRevM___) {α : Type}
  {m : Type → Type w} [[Monad]](#manual-Monad___mk) m]
  (p : α → m [Bool]](#manual-Bool___false)) (as : [Array]](#manual-Array___mk) α) :
  m ([Option]](#manual-Option___none) α)
```

Returns the last element of the array for which the monadic predicate `p` returns `[true]](#manual-Bool___false)`, or `[none]](#manual-Option___none)`
if no such element is found. Elements of the array are checked in reverse, from right to left..

The monad `m` is restricted to `Type → Type` to avoid needing to use `[ULift]](#manual-ULift___up) [Bool]](#manual-Bool___false)` in `p`'s type.

Example:

```lean
[#eval]](#manual-Lean___Parser___Command___eval) #[7, 5, 8, 1, 2, 6, 5, 8].[findRevM?]](#manual-Array___findRevM___) fun i => [do]](#manual-Lean___Parser___Term___do)
if i < 5 then
return [true]](#manual-Bool___false)
if i ≤ 6 then
[IO.println](https://lean-lang.org/doc/reference/latest/IO/Console-Output/#IO___println) s!"Almost! {i}"
return [false]](#manual-Bool___false)
```

```lean
Almost! 5
Almost! 6
```

```lean
[some]](#manual-Option___none) 2
```

def

```lean
[Array.findSome?.{u, v}]](#manual-Array___findSome___) {α : Type u} {β : Type v} (f : α → [Option]](#manual-Option___none) β)
  (as : [Array]](#manual-Array___mk) α) : [Option]](#manual-Option___none) β



[Array.findSome?.{u, v}]](#manual-Array___findSome___) {α : Type u}
  {β : Type v} (f : α → [Option]](#manual-Option___none) β)
  (as : [Array]](#manual-Array___mk) α) : [Option]](#manual-Option___none) β
```

Returns the first non-`[none]](#manual-Option___none)` result of applying the function `f` to each element of the
array, in order. Returns `[none]](#manual-Option___none)` if `f` returns `[none]](#manual-Option___none)` for all elements.

Example:

```lean
[#eval]](#manual-Lean___Parser___Command___eval) #[7, 6, 5, 8, 1, 2, 6].[findSome?]](#manual-Array___findSome___) fun i =>
[if]](#manual-termIfThenElse) i < 5 [then]](#manual-termIfThenElse)
[some]](#manual-Option___none) (i * 10)
[else]](#manual-termIfThenElse)
[none]](#manual-Option___none)
```

```lean
[some]](#manual-Option___none) 10
```

def

```lean
[Array.findSome!.{u, v}]](#manual-Array___findSome___-next) {α : Type u} {β : Type v} [[Inhabited]](#manual-Inhabited___mk) β]
  (f : α → [Option]](#manual-Option___none) β) (xs : [Array]](#manual-Array___mk) α) : β



[Array.findSome!.{u, v}]](#manual-Array___findSome___-next) {α : Type u}
  {β : Type v} [[Inhabited]](#manual-Inhabited___mk) β]
  (f : α → [Option]](#manual-Option___none) β) (xs : [Array]](#manual-Array___mk) α) : β
```

Returns the first non-`[none]](#manual-Option___none)` result of applying the function `f` to each element of the
array, in order. Panics if `f` returns `[none]](#manual-Option___none)` for all elements.

Example:

```lean
[#eval]](#manual-Lean___Parser___Command___eval) #[7, 6, 5, 8, 1, 2, 6].[findSome?]](#manual-Array___findSome___) fun i =>
[if]](#manual-termIfThenElse) i < 5 [then]](#manual-termIfThenElse)
[some]](#manual-Option___none) (i * 10)
[else]](#manual-termIfThenElse)
[none]](#manual-Option___none)
```

```lean
[some]](#manual-Option___none) 10
```

def

```lean
[Array.findSomeM?.{u, v, w}]](#manual-Array___findSomeM___) {α : Type u} {β : Type v}
  {m : Type v → Type w} [[Monad]](#manual-Monad___mk) m] (f : α → m ([Option]](#manual-Option___none) β))
  (as : [Array]](#manual-Array___mk) α) : m ([Option]](#manual-Option___none) β)



[Array.findSomeM?.{u, v, w}]](#manual-Array___findSomeM___) {α : Type u}
  {β : Type v} {m : Type v → Type w}
  [[Monad]](#manual-Monad___mk) m] (f : α → m ([Option]](#manual-Option___none) β))
  (as : [Array]](#manual-Array___mk) α) : m ([Option]](#manual-Option___none) β)
```

Returns the first non-`[none]](#manual-Option___none)` result of applying the monadic function `f` to each element of the
array, in order. Returns `[none]](#manual-Option___none)` if `f` returns `[none]](#manual-Option___none)` for all elements.

Example:

```lean
[#eval]](#manual-Lean___Parser___Command___eval) #[7, 6, 5, 8, 1, 2, 6].[findSomeM?]](#manual-Array___findSomeM___) fun i => [do]](#manual-Lean___Parser___Term___do)
if i < 5 then
return [some]](#manual-Option___none) (i * 10)
if i ≤ 6 then
[IO.println](https://lean-lang.org/doc/reference/latest/IO/Console-Output/#IO___println) s!"Almost! {i}"
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
[Array.findSomeRev?.{u, v}]](#manual-Array___findSomeRev___) {α : Type u} {β : Type v} (f : α → [Option]](#manual-Option___none) β)
  (as : [Array]](#manual-Array___mk) α) : [Option]](#manual-Option___none) β



[Array.findSomeRev?.{u, v}]](#manual-Array___findSomeRev___) {α : Type u}
  {β : Type v} (f : α → [Option]](#manual-Option___none) β)
  (as : [Array]](#manual-Array___mk) α) : [Option]](#manual-Option___none) β
```

Returns the first non-`[none]](#manual-Option___none)` result of applying `f` to each element of the array in reverse order,
from right to left. Returns `[none]](#manual-Option___none)` if `f` returns `[none]](#manual-Option___none)` for all elements of the array.

Examples:

- `#[7, 6, 5, 8, 1, 2, 6].[findSome?]](#manual-Array___findSome___) (fun x => [if]](#manual-termIfThenElse) x < 5 [then]](#manual-termIfThenElse) [some]](#manual-Option___none) (10 * x) [else]](#manual-termIfThenElse) [none]](#manual-Option___none)) = [some]](#manual-Option___none) 10`
- `#[7, 6, 5, 8, 1, 2, 6].[findSome?]](#manual-Array___findSome___) (fun x => [if]](#manual-termIfThenElse) x < 1 [then]](#manual-termIfThenElse) [some]](#manual-Option___none) (10 * x) [else]](#manual-termIfThenElse) [none]](#manual-Option___none)) = [none]](#manual-Option___none)`

def

```lean
[Array.findSomeRevM?.{u, v, w}]](#manual-Array___findSomeRevM___) {α : Type u} {β : Type v}
  {m : Type v → Type w} [[Monad]](#manual-Monad___mk) m] (f : α → m ([Option]](#manual-Option___none) β))
  (as : [Array]](#manual-Array___mk) α) : m ([Option]](#manual-Option___none) β)



[Array.findSomeRevM?.{u, v, w}]](#manual-Array___findSomeRevM___) {α : Type u}
  {β : Type v} {m : Type v → Type w}
  [[Monad]](#manual-Monad___mk) m] (f : α → m ([Option]](#manual-Option___none) β))
  (as : [Array]](#manual-Array___mk) α) : m ([Option]](#manual-Option___none) β)
```

Returns the first non-`[none]](#manual-Option___none)` result of applying the monadic function `f` to each element of the
array in reverse order, from right to left. Once a non-`[none]](#manual-Option___none)` result is found, no further elements
are checked. Returns `[none]](#manual-Option___none)` if `f` returns `[none]](#manual-Option___none)` for all elements of the array.

Examples:

```lean
[#eval]](#manual-Lean___Parser___Command___eval) #[1, 2, 0, -4, 1].[findSomeRevM?]](#manual-Array___findSomeRevM___) (m := [Except]](#manual-Except___error) [String]](#manual-String___ofByteArray)) fun x => [do]](#manual-Lean___Parser___Term___do)
if x = 0 then [throw]](#manual-MonadExcept___mk) "Zero!"
else if x < 0 then return ([some]](#manual-Option___none) x)
else return [none]](#manual-Option___none)
```

```lean
[Except.ok]](#manual-Except___error) ([some]](#manual-Option___none) (-4))
```

```lean
[#eval]](#manual-Lean___Parser___Command___eval) #[1, 2, 0, 4, 1].[findSomeRevM?]](#manual-Array___findSomeRevM___) (m := [Except]](#manual-Except___error) [String]](#manual-String___ofByteArray)) fun x => [do]](#manual-Lean___Parser___Term___do)
if x = 0 then [throw]](#manual-MonadExcept___mk) "Zero!"
else if x < 0 then return ([some]](#manual-Option___none) x)
else return [none]](#manual-Option___none)
```

```lean
[Except.error]](#manual-Except___error) "Zero!"
```

def

```lean
[Array.all.{u}]](#manual-Array___all) {α : Type u} (as : [Array]](#manual-Array___mk) α) (p : α → [Bool]](#manual-Bool___false))
  (start : [Nat]](#manual-Nat___zero) := 0) (stop : [Nat]](#manual-Nat___zero) := as.[size]](#manual-Array___size)) : [Bool]](#manual-Bool___false)



[Array.all.{u}]](#manual-Array___all) {α : Type u} (as : [Array]](#manual-Array___mk) α)
  (p : α → [Bool]](#manual-Bool___false)) (start : [Nat]](#manual-Nat___zero) := 0)
  (stop : [Nat]](#manual-Nat___zero) := as.[size]](#manual-Array___size)) : [Bool]](#manual-Bool___false)
```

Returns `[true]](#manual-Bool___false)` if `p` returns `[true]](#manual-Bool___false)` for every element of `as`.

Short-circuits upon encountering the first `[false]](#manual-Bool___false)`.

The optional parameters `start` and `stop` control the region of the array to be checked. Only the
elements with indices from `start` (inclusive) to `stop` (exclusive) are checked. By default, the
entire array is checked.

Examples:

- `#[a, b, c].[all]](#manual-Array___all) p = (p a && (p b && p c))`
- `#[2, 4, 6].[all]](#manual-Array___all) (· % 2 = 0) = [true]](#manual-Bool___false)`
- `#[2, 4, 5, 6].[all]](#manual-Array___all) (· % 2 = 0) = [false]](#manual-Bool___false)`

def

```lean
[Array.allM.{u, w}]](#manual-Array___allM) {α : Type u} {m : Type → Type w} [[Monad]](#manual-Monad___mk) m]
  (p : α → m [Bool]](#manual-Bool___false)) (as : [Array]](#manual-Array___mk) α) (start : [Nat]](#manual-Nat___zero) := 0)
  (stop : [Nat]](#manual-Nat___zero) := as.[size]](#manual-Array___size)) : m [Bool]](#manual-Bool___false)



[Array.allM.{u, w}]](#manual-Array___allM) {α : Type u}
  {m : Type → Type w} [[Monad]](#manual-Monad___mk) m]
  (p : α → m [Bool]](#manual-Bool___false)) (as : [Array]](#manual-Array___mk) α)
  (start : [Nat]](#manual-Nat___zero) := 0)
  (stop : [Nat]](#manual-Nat___zero) := as.[size]](#manual-Array___size)) : m [Bool]](#manual-Bool___false)
```

Returns `[true]](#manual-Bool___false)` if the monadic predicate `p` returns `[true]](#manual-Bool___false)` for every element of `as`.

Short-circuits upon encountering the first `[false]](#manual-Bool___false)`. The elements in `as` are examined in order from
left to right.

The optional parameters `start` and `stop` control the region of the array to be checked. Only the
elements with indices from `start` (inclusive) to `stop` (exclusive) are checked. By default, the
entire array is checked.

def

```lean
[Array.any.{u}]](#manual-Array___any) {α : Type u} (as : [Array]](#manual-Array___mk) α) (p : α → [Bool]](#manual-Bool___false))
  (start : [Nat]](#manual-Nat___zero) := 0) (stop : [Nat]](#manual-Nat___zero) := as.[size]](#manual-Array___size)) : [Bool]](#manual-Bool___false)



[Array.any.{u}]](#manual-Array___any) {α : Type u} (as : [Array]](#manual-Array___mk) α)
  (p : α → [Bool]](#manual-Bool___false)) (start : [Nat]](#manual-Nat___zero) := 0)
  (stop : [Nat]](#manual-Nat___zero) := as.[size]](#manual-Array___size)) : [Bool]](#manual-Bool___false)
```

Returns `[true]](#manual-Bool___false)` if `p` returns `[true]](#manual-Bool___false)` for any element of `as`.

Short-circuits upon encountering the first `[true]](#manual-Bool___false)`.

The optional parameters `start` and `stop` control the region of the array to be checked. Only the
elements with indices from `start` (inclusive) to `stop` (exclusive) are checked. By default, the
entire array is checked.

Examples:

- `#[2, 4, 6].[any]](#manual-Array___any) (· % 2 = 0) = [true]](#manual-Bool___false)`
- `#[2, 4, 6].[any]](#manual-Array___any) (· % 2 = 1) = [false]](#manual-Bool___false)`
- `#[2, 4, 5, 6].[any]](#manual-Array___any) (· % 2 = 0) = [true]](#manual-Bool___false)`
- `#[2, 4, 5, 6].[any]](#manual-Array___any) (· % 2 = 1) = [true]](#manual-Bool___false)`

def

```lean
[Array.anyM.{u, w}]](#manual-Array___anyM) {α : Type u} {m : Type → Type w} [[Monad]](#manual-Monad___mk) m]
  (p : α → m [Bool]](#manual-Bool___false)) (as : [Array]](#manual-Array___mk) α) (start : [Nat]](#manual-Nat___zero) := 0)
  (stop : [Nat]](#manual-Nat___zero) := as.[size]](#manual-Array___size)) : m [Bool]](#manual-Bool___false)



[Array.anyM.{u, w}]](#manual-Array___anyM) {α : Type u}
  {m : Type → Type w} [[Monad]](#manual-Monad___mk) m]
  (p : α → m [Bool]](#manual-Bool___false)) (as : [Array]](#manual-Array___mk) α)
  (start : [Nat]](#manual-Nat___zero) := 0)
  (stop : [Nat]](#manual-Nat___zero) := as.[size]](#manual-Array___size)) : m [Bool]](#manual-Bool___false)
```

Returns `[true]](#manual-Bool___false)` if the monadic predicate `p` returns `[true]](#manual-Bool___false)` for any element of `as`.

Short-circuits upon encountering the first `[true]](#manual-Bool___false)`. The elements in `as` are examined in order from
left to right.

The optional parameters `start` and `stop` control the region of the array to be checked. Only the
elements with indices from `start` (inclusive) to `stop` (exclusive) are checked. By default, the
entire array is checked.

def

```lean
[Array.allDiff.{u}]](#manual-Array___allDiff) {α : Type u} [[BEq]](#manual-BEq___mk) α] (as : [Array]](#manual-Array___mk) α) : [Bool]](#manual-Bool___false)



[Array.allDiff.{u}]](#manual-Array___allDiff) {α : Type u} [[BEq]](#manual-BEq___mk) α]
  (as : [Array]](#manual-Array___mk) α) : [Bool]](#manual-Bool___false)
```

Returns `[true]](#manual-Bool___false)` if no two elements of `as` are equal according to the `==` operator.

Examples:

- `#["red", "green", "blue"].[allDiff]](#manual-Array___allDiff) = [true]](#manual-Bool___false)`
- `#["red", "green", "red"].[allDiff]](#manual-Array___allDiff) = [false]](#manual-Bool___false)`
- `(#[] : [Array]](#manual-Array___mk) [Nat]](#manual-Nat___zero)).[allDiff]](#manual-Array___allDiff) = [true]](#manual-Bool___false)`

def

```lean
[Array.isEqv.{u}]](#manual-Array___isEqv) {α : Type u} (xs ys : [Array]](#manual-Array___mk) α) (p : α → α → [Bool]](#manual-Bool___false)) : [Bool]](#manual-Bool___false)



[Array.isEqv.{u}]](#manual-Array___isEqv) {α : Type u}
  (xs ys : [Array]](#manual-Array___mk) α) (p : α → α → [Bool]](#manual-Bool___false)) :
  [Bool]](#manual-Bool___false)
```

Returns `[true]](#manual-Bool___false)` if `as` and `bs` have the same length and they are pairwise related by `eqv`.

Short-circuits at the first non-related pair of elements.

Examples:

- `#[1, 2, 3].[isEqv]](#manual-Array___isEqv) #[2, 3, 4] (· < ·) = [true]](#manual-Bool___false)`
- `#[1, 2, 3].[isEqv]](#manual-Array___isEqv) #[2, 2, 4] (· < ·) = [false]](#manual-Bool___false)`
- `#[1, 2, 3].[isEqv]](#manual-Array___isEqv) #[2, 3] (· < ·) = [false]](#manual-Bool___false)`

#### 20.16.4.13. Comparisons {#manual-The-Lean-Language-Reference--Basic-Types--Arrays--API-Reference--Comparisons}

def

```lean
[Array.isPrefixOf.{u}]](#manual-Array___isPrefixOf) {α : Type u} [[BEq]](#manual-BEq___mk) α] (as bs : [Array]](#manual-Array___mk) α) : [Bool]](#manual-Bool___false)



[Array.isPrefixOf.{u}]](#manual-Array___isPrefixOf) {α : Type u} [[BEq]](#manual-BEq___mk) α]
  (as bs : [Array]](#manual-Array___mk) α) : [Bool]](#manual-Bool___false)
```

Return `[true]](#manual-Bool___false)` if `as` is a prefix of `bs`, or `[false]](#manual-Bool___false)` otherwise.

Examples:

- `#[0, 1, 2].[isPrefixOf]](#manual-Array___isPrefixOf) #[0, 1, 2, 3] = [true]](#manual-Bool___false)`
- `#[0, 1, 2].[isPrefixOf]](#manual-Array___isPrefixOf) #[0, 1, 2] = [true]](#manual-Bool___false)`
- `#[0, 1, 2].[isPrefixOf]](#manual-Array___isPrefixOf) #[0, 1] = [false]](#manual-Bool___false)`
- `#[].[isPrefixOf]](#manual-Array___isPrefixOf) #[0, 1] = [true]](#manual-Bool___false)`

def

```lean
[Array.lex.{u_1}]](#manual-Array___lex) {α : Type u_1} [[BEq]](#manual-BEq___mk) α] (as bs : [Array]](#manual-Array___mk) α)
  (lt : α → α → [Bool]](#manual-Bool___false) := by exact (· < ·)) : [Bool]](#manual-Bool___false)



[Array.lex.{u_1}]](#manual-Array___lex) {α : Type u_1} [[BEq]](#manual-BEq___mk) α]
  (as bs : [Array]](#manual-Array___mk) α)
  (lt : α → α → [Bool]](#manual-Bool___false) := by
    exact (· < ·)) :
  [Bool]](#manual-Bool___false)
```

Compares arrays lexicographically with respect to a comparison `lt` on their elements.

Specifically, `[Array.lex]](#manual-Array___lex) as bs lt` is true if

- `bs` is larger than `as` and `as` is pairwise equivalent via `==` to the initial segment of `bs`,
  or
- there is an index `i` such that `lt as[i] bs[i]`, and for all `j < i`, `as[j] == bs[j]`.

#### 20.16.4.14. Termination Helpers {#manual-The-Lean-Language-Reference--Basic-Types--Arrays--API-Reference--Termination-Helpers}

def

```lean
[Array.attach.{u_1}]](#manual-Array___attach) {α : Type u_1} (xs : [Array]](#manual-Array___mk) α) : [Array]](#manual-Array___mk) [{](https://lean-lang.org/doc/reference/latest/Basic-Types/Subtypes/#Subtype___mk) x [//](https://lean-lang.org/doc/reference/latest/Basic-Types/Subtypes/#Subtype___mk) x ∈ xs [}](https://lean-lang.org/doc/reference/latest/Basic-Types/Subtypes/#Subtype___mk)



[Array.attach.{u_1}]](#manual-Array___attach) {α : Type u_1}
  (xs : [Array]](#manual-Array___mk) α) : [Array]](#manual-Array___mk) [{](https://lean-lang.org/doc/reference/latest/Basic-Types/Subtypes/#Subtype___mk) x [//](https://lean-lang.org/doc/reference/latest/Basic-Types/Subtypes/#Subtype___mk) x ∈ xs [}](https://lean-lang.org/doc/reference/latest/Basic-Types/Subtypes/#Subtype___mk)
```

“Attaches” the proof that the elements of `xs` are in fact elements of `xs`, producing a new array with
the same elements but in the subtype `{ x // x ∈ xs }`.

`O(1)`.

This function is primarily used to allow definitions by [well-founded
recursion](https://lean-lang.org/doc/reference/4.34.0-rc1/find/?domain=Verso.Genre.Manual.section&name=well-founded-recursion) that use higher-order functions (such as
`[Array.map]](#manual-Array___map)`) to prove that an value taken from a list is smaller than the list. This allows the
well-founded recursion mechanism to prove that the function terminates.

def

```lean
[Array.attachWith.{u_1}]](#manual-Array___attachWith) {α : Type u_1} (xs : [Array]](#manual-Array___mk) α) (P : α → Prop)
  (H : ∀ (x : α), x ∈ xs → P x) : [Array]](#manual-Array___mk) [{](https://lean-lang.org/doc/reference/latest/Basic-Types/Subtypes/#Subtype___mk) x [//](https://lean-lang.org/doc/reference/latest/Basic-Types/Subtypes/#Subtype___mk) P x [}](https://lean-lang.org/doc/reference/latest/Basic-Types/Subtypes/#Subtype___mk)



[Array.attachWith.{u_1}]](#manual-Array___attachWith) {α : Type u_1}
  (xs : [Array]](#manual-Array___mk) α) (P : α → Prop)
  (H : ∀ (x : α), x ∈ xs → P x) :
  [Array]](#manual-Array___mk) [{](https://lean-lang.org/doc/reference/latest/Basic-Types/Subtypes/#Subtype___mk) x [//](https://lean-lang.org/doc/reference/latest/Basic-Types/Subtypes/#Subtype___mk) P x [}](https://lean-lang.org/doc/reference/latest/Basic-Types/Subtypes/#Subtype___mk)
```

“Attaches” individual proofs to an array of values that satisfy a predicate `P`, returning an array
of elements in the corresponding subtype `{ x // P x }`.

`O(1)`.

def

```lean
[Array.unattach.{u_1}]](#manual-Array___unattach) {α : Type u_1} {p : α → Prop}
  (xs : [Array]](#manual-Array___mk) [{](https://lean-lang.org/doc/reference/latest/Basic-Types/Subtypes/#Subtype___mk) x [//](https://lean-lang.org/doc/reference/latest/Basic-Types/Subtypes/#Subtype___mk) p x [}](https://lean-lang.org/doc/reference/latest/Basic-Types/Subtypes/#Subtype___mk)) : [Array]](#manual-Array___mk) α



[Array.unattach.{u_1}]](#manual-Array___unattach) {α : Type u_1}
  {p : α → Prop}
  (xs : [Array]](#manual-Array___mk) [{](https://lean-lang.org/doc/reference/latest/Basic-Types/Subtypes/#Subtype___mk) x [//](https://lean-lang.org/doc/reference/latest/Basic-Types/Subtypes/#Subtype___mk) p x [}](https://lean-lang.org/doc/reference/latest/Basic-Types/Subtypes/#Subtype___mk)) : [Array]](#manual-Array___mk) α
```

Maps an array of terms in a subtype to the corresponding terms in the type by forgetting that they
satisfy the predicate.

This is the inverse of `[Array.attachWith]](#manual-Array___attachWith)` and a synonym for `xs.[map]](#manual-Array___map) (·.val)`.

Mostly this should not be needed by users. It is introduced as an intermediate step by lemmas such
as `map_subtype`, and is ideally subsequently simplified away by `unattach_attach`.

This function is usually inserted automatically by Lean as an intermediate step while proving
termination. It is rarely used explicitly in code. It is introduced as an intermediate step during
the elaboration of definitions by [well-founded
recursion](https://lean-lang.org/doc/reference/4.34.0-rc1/find/?domain=Verso.Genre.Manual.section&name=well-founded-recursion). If this function is encountered in a proof
state, the right approach is usually the tactic `[simp]](#manual-simp) [Array.unattach, -Array.map_subtype]`.

def

```lean
[Array.pmap.{u_1, u_2}]](#manual-Array___pmap) {α : Type u_1} {β : Type u_2} {P : α → Prop}
  (f : (a : α) → P a → β) (xs : [Array]](#manual-Array___mk) α) (H : ∀ (a : α), a ∈ xs → P a) :
  [Array]](#manual-Array___mk) β



[Array.pmap.{u_1, u_2}]](#manual-Array___pmap) {α : Type u_1}
  {β : Type u_2} {P : α → Prop}
  (f : (a : α) → P a → β) (xs : [Array]](#manual-Array___mk) α)
  (H : ∀ (a : α), a ∈ xs → P a) : [Array]](#manual-Array___mk) β
```

Maps a partially defined function (defined on those terms of `α` that satisfy a predicate `P`) over
an array `xs : [Array]](#manual-Array___mk) α`, given a proof that every element of `xs` in fact satisfies `P`.

`[Array.pmap]](#manual-Array___pmap)`, named for “partial map,” is the equivalent of `[Array.map]](#manual-Array___map)` for such partial functions.

### 20.16.5. Subarrays {#manual-subarray}

The type `[Subarray]](#manual-Subarray) α` is an abbreviations for `Std.Slice α`.
This means that, in addition to the operators in this section, [generalized field notation]](#manual---tech-term-generalized-field-notation) can be used to call functions in the `Std.Slice` namespace, such as `Std.Slice.foldl`.

def

```lean
[Subarray.{u}]](#manual-Subarray) (α : Type u) : Type u



[Subarray.{u}]](#manual-Subarray) (α : Type u) : Type u
```

A region of some underlying array.

A subarray contains an array together with the start and end indices of a region of interest.
Subarrays can be used to avoid copying or allocating space, while being more convenient than
tracking the bounds by hand. The region of interest consists of every index that is both greater
than or equal to `start` and strictly less than `[stop]](#manual-stop)`.

def

```lean
[Subarray.empty.{u_1}]](#manual-Subarray___empty) {α : Type u_1} : [Subarray]](#manual-Subarray) α



[Subarray.empty.{u_1}]](#manual-Subarray___empty) {α : Type u_1} :
  [Subarray]](#manual-Subarray) α
```

The empty subarray.

This empty subarray is backed by an empty array.

#### 20.16.5.1. Array Data {#manual-The-Lean-Language-Reference--Basic-Types--Arrays--Subarrays--Array-Data}

def

```lean
[Subarray.array.{u_1}]](#manual-Subarray___array) {α : Type u_1} (xs : [Subarray]](#manual-Subarray) α) : [Array]](#manual-Array___mk) α



[Subarray.array.{u_1}]](#manual-Subarray___array) {α : Type u_1}
  (xs : [Subarray]](#manual-Subarray) α) : [Array]](#manual-Array___mk) α
```

The underlying array.

def

```lean
[Subarray.start.{u_1}]](#manual-Subarray___start) {α : Type u_1} (xs : [Subarray]](#manual-Subarray) α) : [Nat]](#manual-Nat___zero)



[Subarray.start.{u_1}]](#manual-Subarray___start) {α : Type u_1}
  (xs : [Subarray]](#manual-Subarray) α) : [Nat]](#manual-Nat___zero)
```

The starting index of the region of interest (inclusive).

def

```lean
[Subarray.stop.{u_1}]](#manual-Subarray___stop) {α : Type u_1} (xs : [Subarray]](#manual-Subarray) α) : [Nat]](#manual-Nat___zero)



[Subarray.stop.{u_1}]](#manual-Subarray___stop) {α : Type u_1}
  (xs : [Subarray]](#manual-Subarray) α) : [Nat]](#manual-Nat___zero)
```

The ending index of the region of interest (exclusive).

def

```lean
[Subarray.start_le_stop.{u_1}]](#manual-Subarray___start_le_stop) {α : Type u_1} (xs : [Subarray]](#manual-Subarray) α) :
  xs.[start]](#manual-Subarray___start) [≤]](#manual-LE___mk) xs.[stop]](#manual-Subarray___stop)



[Subarray.start_le_stop.{u_1}]](#manual-Subarray___start_le_stop)
  {α : Type u_1} (xs : [Subarray]](#manual-Subarray) α) :
  xs.[start]](#manual-Subarray___start) [≤]](#manual-LE___mk) xs.[stop]](#manual-Subarray___stop)
```

The starting index is no later than the ending index.

The ending index is exclusive. If the starting and ending indices are equal, then the subarray is
empty.

def

```lean
[Subarray.stop_le_array_size.{u_1}]](#manual-Subarray___stop_le_array_size) {α : Type u_1} (xs : [Subarray]](#manual-Subarray) α) :
  xs.[stop]](#manual-Subarray___stop) [≤]](#manual-LE___mk) xs.[array]](#manual-Subarray___array).[size]](#manual-Array___size)



[Subarray.stop_le_array_size.{u_1}]](#manual-Subarray___stop_le_array_size)
  {α : Type u_1} (xs : [Subarray]](#manual-Subarray) α) :
  xs.[stop]](#manual-Subarray___stop) [≤]](#manual-LE___mk) xs.[array]](#manual-Subarray___array).[size]](#manual-Array___size)
```

The stopping index is no later than the end of the array.

The ending index is exclusive. If it is equal to the size of the array, then the last element of
the array is in the subarray.

#### 20.16.5.2. Resizing {#manual-The-Lean-Language-Reference--Basic-Types--Arrays--Subarrays--Resizing}

def

```lean
[Subarray.drop.{u_1}]](#manual-Subarray___drop) {α : Type u_1} (arr : [Subarray]](#manual-Subarray) α) (i : [Nat]](#manual-Nat___zero)) :
  [Subarray]](#manual-Subarray) α



[Subarray.drop.{u_1}]](#manual-Subarray___drop) {α : Type u_1}
  (arr : [Subarray]](#manual-Subarray) α) (i : [Nat]](#manual-Nat___zero)) :
  [Subarray]](#manual-Subarray) α
```

Removes the first `i` elements of the subarray. If there are `i` or fewer elements, the resulting
subarray is empty.

def

```lean
[Subarray.take.{u_1}]](#manual-Subarray___take) {α : Type u_1} (arr : [Subarray]](#manual-Subarray) α) (i : [Nat]](#manual-Nat___zero)) :
  [Subarray]](#manual-Subarray) α



[Subarray.take.{u_1}]](#manual-Subarray___take) {α : Type u_1}
  (arr : [Subarray]](#manual-Subarray) α) (i : [Nat]](#manual-Nat___zero)) :
  [Subarray]](#manual-Subarray) α
```

Keeps only the first `i` elements of the subarray. If there are `i` or fewer elements, the resulting
subarray is empty.

def

```lean
[Subarray.popFront.{u_1}]](#manual-Subarray___popFront) {α : Type u_1} (s : [Subarray]](#manual-Subarray) α) : [Subarray]](#manual-Subarray) α



[Subarray.popFront.{u_1}]](#manual-Subarray___popFront) {α : Type u_1}
  (s : [Subarray]](#manual-Subarray) α) : [Subarray]](#manual-Subarray) α
```

Shrinks the subarray by incrementing its starting index if possible, returning it unchanged if not.

Examples:

- `#[1,2,3].[toSubarray]](#manual-Array___toSubarray).[popFront]](#manual-Subarray___popFront).toArray = #[2, 3]`
- `#[1,2,3].[toSubarray]](#manual-Array___toSubarray).[popFront]](#manual-Subarray___popFront).[popFront]](#manual-Subarray___popFront).toArray = #[3]`
- `#[1,2,3].[toSubarray]](#manual-Array___toSubarray).[popFront]](#manual-Subarray___popFront).[popFront]](#manual-Subarray___popFront).[popFront]](#manual-Subarray___popFront).toArray = #[]`
- `#[1,2,3].[toSubarray]](#manual-Array___toSubarray).[popFront]](#manual-Subarray___popFront).[popFront]](#manual-Subarray___popFront).[popFront]](#manual-Subarray___popFront).[popFront]](#manual-Subarray___popFront).toArray = #[]`

def

```lean
[Subarray.split.{u_1}]](#manual-Subarray___split) {α : Type u_1} (s : [Subarray]](#manual-Subarray) α)
  (i : [Fin]](#manual-Fin___mk) (Std.Slice.size s).[succ]](#manual-Nat___zero)) : [Subarray]](#manual-Subarray) α [×]](#manual-Prod___mk) [Subarray]](#manual-Subarray) α



[Subarray.split.{u_1}]](#manual-Subarray___split) {α : Type u_1}
  (s : [Subarray]](#manual-Subarray) α)
  (i : [Fin]](#manual-Fin___mk) (Std.Slice.size s).[succ]](#manual-Nat___zero)) :
  [Subarray]](#manual-Subarray) α [×]](#manual-Prod___mk) [Subarray]](#manual-Subarray) α
```

Splits a subarray into two parts, the first of which contains the first `i` elements and the second
of which contains the remainder.

#### 20.16.5.3. Lookups {#manual-The-Lean-Language-Reference--Basic-Types--Arrays--Subarrays--Lookups}

def

```lean
[Subarray.get.{u_1}]](#manual-Subarray___get) {α : Type u_1} (s : [Subarray]](#manual-Subarray) α)
  (i : [Fin]](#manual-Fin___mk) (Std.Slice.size s)) : α



[Subarray.get.{u_1}]](#manual-Subarray___get) {α : Type u_1}
  (s : [Subarray]](#manual-Subarray) α)
  (i : [Fin]](#manual-Fin___mk) (Std.Slice.size s)) : α
```

Extracts an element from the subarray.

The index is relative to the start of the subarray, rather than the underlying array.

def

```lean
[Subarray.get!.{u_1}]](#manual-Subarray___get___) {α : Type u_1} [[Inhabited]](#manual-Inhabited___mk) α] (s : [Subarray]](#manual-Subarray) α)
  (i : [Nat]](#manual-Nat___zero)) : α



[Subarray.get!.{u_1}]](#manual-Subarray___get___) {α : Type u_1}
  [[Inhabited]](#manual-Inhabited___mk) α] (s : [Subarray]](#manual-Subarray) α)
  (i : [Nat]](#manual-Nat___zero)) : α
```

Extracts an element from the subarray, or returns a default value when the index is out of bounds.

The index is relative to the start and end of the subarray, rather than the underlying array. The
default value is that provided by the `[Inhabited]](#manual-Inhabited___mk) α` instance.

def

```lean
[Subarray.getD.{u_1}]](#manual-Subarray___getD) {α : Type u_1} (s : [Subarray]](#manual-Subarray) α) (i : [Nat]](#manual-Nat___zero)) (v₀ : α) :
  α



[Subarray.getD.{u_1}]](#manual-Subarray___getD) {α : Type u_1}
  (s : [Subarray]](#manual-Subarray) α) (i : [Nat]](#manual-Nat___zero)) (v₀ : α) : α
```

Extracts an element from the subarray, or returns a default value `v₀` when the index is out of
bounds.

The index is relative to the start and end of the subarray, rather than the underlying array.

#### 20.16.5.4. Iteration {#manual-The-Lean-Language-Reference--Basic-Types--Arrays--Subarrays--Iteration}

def

```lean
[Subarray.foldr.{u, v}]](#manual-Subarray___foldr) {α : Type u} {β : Type v} (f : α → β → β)
  (init : β) (as : [Subarray]](#manual-Subarray) α) : β



[Subarray.foldr.{u, v}]](#manual-Subarray___foldr) {α : Type u}
  {β : Type v} (f : α → β → β) (init : β)
  (as : [Subarray]](#manual-Subarray) α) : β
```

Folds an operation from right to left over the elements in a subarray.

An accumulator of type `β` is constructed by starting with `init` and combining each element of the
subarray with the current accumulator value in turn, moving from the end to the start.

Examples:

- `#["red", "green", "blue"].[toSubarray]](#manual-Array___toSubarray).[foldr]](#manual-Subarray___foldr) (·.[length]](#manual-String___length) + ·) 0 = 12`
- `#["red", "green", "blue"].[toSubarray]](#manual-Array___toSubarray).[popFront]](#manual-Subarray___popFront).[foldr]](#manual-Subarray___foldr) (·.[length]](#manual-String___length) + ·) 0 = 9`

def

```lean
[Subarray.foldrM.{u, v, w}]](#manual-Subarray___foldrM) {α : Type u} {β : Type v}
  {m : Type v → Type w} [[Monad]](#manual-Monad___mk) m] (f : α → β → m β) (init : β)
  (as : [Subarray]](#manual-Subarray) α) : m β



[Subarray.foldrM.{u, v, w}]](#manual-Subarray___foldrM) {α : Type u}
  {β : Type v} {m : Type v → Type w}
  [[Monad]](#manual-Monad___mk) m] (f : α → β → m β) (init : β)
  (as : [Subarray]](#manual-Subarray) α) : m β
```

Folds a monadic operation from right to left over the elements in a subarray.

An accumulator of type `β` is constructed by starting with `init` and monadically combining each
element of the subarray with the current accumulator value in turn, moving from the end to the
start. The monad in question may permit early termination or repetition.

Examples:

```lean
[#eval]](#manual-Lean___Parser___Command___eval) #["red", "green", "blue"].[toSubarray]](#manual-Array___toSubarray).[foldrM]](#manual-Subarray___foldrM) (init := "") fun x acc => [do]](#manual-Lean___Parser___Term___do)
let l ← [Option.guard]](#manual-Option___guard) (· ≠ 0) x.[length]](#manual-String___length)
return s!"{acc}({l}){x} "
```

```lean
[some]](#manual-Option___none) "(4)blue (5)green (3)red "
```

```
#eval #["red", "green", "blue"].toSubarray.foldrM (init := 0) fun x acc => do
  let l ← Option.guard (· ≠ 5) x.length
  return s!"{acc}({l}){x} "
```

```lean
[none]](#manual-Option___none)
```

def

```lean
[Subarray.forM.{u, v, w}]](#manual-Subarray___forM) {α : Type u} {m : Type v → Type w} [[Monad]](#manual-Monad___mk) m]
  (f : α → m [PUnit]](#manual-PUnit___unit)) (as : [Subarray]](#manual-Subarray) α) : m [PUnit]](#manual-PUnit___unit)



[Subarray.forM.{u, v, w}]](#manual-Subarray___forM) {α : Type u}
  {m : Type v → Type w} [[Monad]](#manual-Monad___mk) m]
  (f : α → m [PUnit]](#manual-PUnit___unit)) (as : [Subarray]](#manual-Subarray) α) :
  m [PUnit]](#manual-PUnit___unit)
```

Runs a monadic action on each element of a subarray.

The elements are processed starting at the lowest index and moving up.

def

```lean
[Subarray.forRevM.{u, v, w}]](#manual-Subarray___forRevM) {α : Type u} {m : Type v → Type w} [[Monad]](#manual-Monad___mk) m]
  (f : α → m [PUnit]](#manual-PUnit___unit)) (as : [Subarray]](#manual-Subarray) α) : m [PUnit]](#manual-PUnit___unit)



[Subarray.forRevM.{u, v, w}]](#manual-Subarray___forRevM) {α : Type u}
  {m : Type v → Type w} [[Monad]](#manual-Monad___mk) m]
  (f : α → m [PUnit]](#manual-PUnit___unit)) (as : [Subarray]](#manual-Subarray) α) :
  m [PUnit]](#manual-PUnit___unit)
```

Runs a monadic action on each element of a subarray, in reverse order.

The elements are processed starting at the highest index and moving down.

def

```lean
[Subarray.forIn.{v, w, u}]](#manual-Subarray___forIn) {α : Type u} {β : Type v} {m : Type v → Type w}
  [[Monad]](#manual-Monad___mk) m] (s : [Subarray]](#manual-Subarray) α) (b : β) (f : α → β → m ([ForInStep]](#manual-ForInStep___done) β)) : m β



[Subarray.forIn.{v, w, u}]](#manual-Subarray___forIn) {α : Type u}
  {β : Type v} {m : Type v → Type w}
  [[Monad]](#manual-Monad___mk) m] (s : [Subarray]](#manual-Subarray) α) (b : β)
  (f : α → β → m ([ForInStep]](#manual-ForInStep___done) β)) : m β
```

The implementation of `[ForIn.forIn]](#manual-ForIn___mk)` for `[Subarray]](#manual-Subarray)`, which allows it to be used with `for` loops in
`do`-notation.

#### 20.16.5.5. Element Predicates {#manual-The-Lean-Language-Reference--Basic-Types--Arrays--Subarrays--Element-Predicates}

def

```lean
[Subarray.findRev?]](#manual-Subarray___findRev___) {α : Type} (as : [Subarray]](#manual-Subarray) α) (p : α → [Bool]](#manual-Bool___false)) : [Option]](#manual-Option___none) α



[Subarray.findRev?]](#manual-Subarray___findRev___) {α : Type}
  (as : [Subarray]](#manual-Subarray) α) (p : α → [Bool]](#manual-Bool___false)) :
  [Option]](#manual-Option___none) α
```

Tests each element in a subarray with a Boolean predicate in reverse order, stopping at the first
element that satisfies the predicate. The element that satisfies the predicate is returned, or
`[none]](#manual-Option___none)` if no element satisfies the predicate.

Examples:

- `#["red", "green", "blue"].[toSubarray]](#manual-Array___toSubarray).[findRev?]](#manual-Subarray___findRev___) (·.[length]](#manual-String___length) ≠ 4) = [some]](#manual-Option___none) "green"`
- `#["red", "green", "blue"].[toSubarray]](#manual-Array___toSubarray).[findRev?]](#manual-Subarray___findRev___) (fun _ => [true]](#manual-Bool___false)) = [some]](#manual-Option___none) "blue"`
- `#["red", "green", "blue"].toSubarray 0 0 |>.findRev? (fun _ => true) = none`

def

```lean
[Subarray.findRevM?.{w}]](#manual-Subarray___findRevM___) {α : Type} {m : Type → Type w} [[Monad]](#manual-Monad___mk) m]
  (as : [Subarray]](#manual-Subarray) α) (p : α → m [Bool]](#manual-Bool___false)) : m ([Option]](#manual-Option___none) α)



[Subarray.findRevM?.{w}]](#manual-Subarray___findRevM___) {α : Type}
  {m : Type → Type w} [[Monad]](#manual-Monad___mk) m]
  (as : [Subarray]](#manual-Subarray) α) (p : α → m [Bool]](#manual-Bool___false)) :
  m ([Option]](#manual-Option___none) α)
```

Applies a monadic Boolean predicate to each element in a subarray in reverse order, stopping at the
first element that satisfies the predicate. The element that satisfies the predicate is returned, or
`[none]](#manual-Option___none)` if no element satisfies it.

Example:

```lean
[#eval]](#manual-Lean___Parser___Command___eval) #["red", "green", "blue"].[toSubarray]](#manual-Array___toSubarray).[findRevM?]](#manual-Subarray___findRevM___) fun x => [do]](#manual-Lean___Parser___Term___do)
[IO.println](https://lean-lang.org/doc/reference/latest/IO/Console-Output/#IO___println) x
return (x.[length]](#manual-String___length) = 5)
```

```lean
blue
green
```

```lean
[some]](#manual-Option___none) 5
```

def

```lean
[Subarray.findSomeRevM?.{u, v, w}]](#manual-Subarray___findSomeRevM___) {α : Type u} {β : Type v}
  {m : Type v → Type w} [[Monad]](#manual-Monad___mk) m] (as : [Subarray]](#manual-Subarray) α)
  (f : α → m ([Option]](#manual-Option___none) β)) : m ([Option]](#manual-Option___none) β)



[Subarray.findSomeRevM?.{u, v, w}]](#manual-Subarray___findSomeRevM___)
  {α : Type u} {β : Type v}
  {m : Type v → Type w} [[Monad]](#manual-Monad___mk) m]
  (as : [Subarray]](#manual-Subarray) α)
  (f : α → m ([Option]](#manual-Option___none) β)) : m ([Option]](#manual-Option___none) β)
```

Applies a monadic function to each element in a subarray in reverse order, stopping at the first
element for which the function succeeds by returning a value other than `[none]](#manual-Option___none)`. The succeeding value
is returned, or `[none]](#manual-Option___none)` if there is no success.

Example:

```lean
[#eval]](#manual-Lean___Parser___Command___eval) #["red", "green", "blue"].[toSubarray]](#manual-Array___toSubarray).[findSomeRevM?]](#manual-Subarray___findSomeRevM___) fun x => [do]](#manual-Lean___Parser___Term___do)
[IO.println](https://lean-lang.org/doc/reference/latest/IO/Console-Output/#IO___println) x
return [Option.guard]](#manual-Option___guard) (· = 5) x.[length]](#manual-String___length)
```

```lean
blue
green
```

```lean
[some]](#manual-Option___none) 5
```

def

```lean
[Subarray.all.{u}]](#manual-Subarray___all) {α : Type u} (p : α → [Bool]](#manual-Bool___false)) (as : [Subarray]](#manual-Subarray) α) : [Bool]](#manual-Bool___false)



[Subarray.all.{u}]](#manual-Subarray___all) {α : Type u}
  (p : α → [Bool]](#manual-Bool___false)) (as : [Subarray]](#manual-Subarray) α) : [Bool]](#manual-Bool___false)
```

Checks whether all of the elements in a subarray satisfy a Boolean predicate.

The elements are tested starting at the lowest index and moving up. The search terminates as soon as
an element that does not satisfy the predicate is found.

def

```lean
[Subarray.allM.{u, w}]](#manual-Subarray___allM) {α : Type u} {m : Type → Type w} [[Monad]](#manual-Monad___mk) m]
  (p : α → m [Bool]](#manual-Bool___false)) (as : [Subarray]](#manual-Subarray) α) : m [Bool]](#manual-Bool___false)



[Subarray.allM.{u, w}]](#manual-Subarray___allM) {α : Type u}
  {m : Type → Type w} [[Monad]](#manual-Monad___mk) m]
  (p : α → m [Bool]](#manual-Bool___false)) (as : [Subarray]](#manual-Subarray) α) :
  m [Bool]](#manual-Bool___false)
```

Checks whether all of the elements in a subarray satisfy a monadic Boolean predicate.

The elements are tested starting at the lowest index and moving up. The search terminates as soon as
an element that does not satisfy the predicate is found.

Example:

```lean
[#eval]](#manual-Lean___Parser___Command___eval) #["red", "green", "blue", "orange"].[toSubarray]](#manual-Array___toSubarray).[popFront]](#manual-Subarray___popFront).[allM]](#manual-Subarray___allM) fun x => [do]](#manual-Lean___Parser___Term___do)
[IO.println](https://lean-lang.org/doc/reference/latest/IO/Console-Output/#IO___println) x
[pure]](#manual-Pure___mk) (x.[length]](#manual-String___length) == 5)
```

```lean
green
blue
```

```lean
[false]](#manual-Bool___false)
```

def

```lean
[Subarray.any.{u}]](#manual-Subarray___any) {α : Type u} (p : α → [Bool]](#manual-Bool___false)) (as : [Subarray]](#manual-Subarray) α) : [Bool]](#manual-Bool___false)



[Subarray.any.{u}]](#manual-Subarray___any) {α : Type u}
  (p : α → [Bool]](#manual-Bool___false)) (as : [Subarray]](#manual-Subarray) α) : [Bool]](#manual-Bool___false)
```

Checks whether any of the elements in a subarray satisfy a Boolean predicate.

The elements are tested starting at the lowest index and moving up. The search terminates as soon as
an element that satisfies the predicate is found.

def

```lean
[Subarray.anyM.{u, w}]](#manual-Subarray___anyM) {α : Type u} {m : Type → Type w} [[Monad]](#manual-Monad___mk) m]
  (p : α → m [Bool]](#manual-Bool___false)) (as : [Subarray]](#manual-Subarray) α) : m [Bool]](#manual-Bool___false)



[Subarray.anyM.{u, w}]](#manual-Subarray___anyM) {α : Type u}
  {m : Type → Type w} [[Monad]](#manual-Monad___mk) m]
  (p : α → m [Bool]](#manual-Bool___false)) (as : [Subarray]](#manual-Subarray) α) :
  m [Bool]](#manual-Bool___false)
```

Checks whether any of the elements in a subarray satisfy a monadic Boolean predicate.

The elements are tested starting at the lowest index and moving up. The search terminates as soon as
an element that satisfies the predicate is found.

Example:

```lean
[#eval]](#manual-Lean___Parser___Command___eval) #["red", "green", "blue", "orange"].[toSubarray]](#manual-Array___toSubarray).[popFront]](#manual-Subarray___popFront).[anyM]](#manual-Subarray___anyM) fun x => [do]](#manual-Lean___Parser___Term___do)
[IO.println](https://lean-lang.org/doc/reference/latest/IO/Console-Output/#IO___println) x
[pure]](#manual-Pure___mk) (x == "blue")
```

```lean
green
blue
```

```lean
[true]](#manual-Bool___false)
```

### 20.16.6. FFI {#manual-array-ffi}

FFI type

```
```
typedef struct {
    lean_object   m_header;
    size_t        m_size;
    size_t        m_capacity;
    lean_object * m_data[];
} lean_array_object;
```
```

The representation of arrays in C. See [the description of run-time `[Array]](#manual-Array___mk)`s](https://lean-lang.org/doc/reference/latest/Basic-Types/Arrays/#array-runtime) for more details.

FFI function

```
```
bool lean_is_array(lean_object * o)
```
```

Returns `true` if `o` is an array, or `false` otherwise.

FFI function

```
```
lean_array_object * lean_to_array(lean_object * o)
```
```

Performs a runtime check that `o` is indeed an array. If `o` is not an array, an assertion fails.

---




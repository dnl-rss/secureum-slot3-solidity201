### 130. Reserved Keywords

Several keywords are reserved in Solidity, which may become part of the syntax in the future:

- `after`, `alias`, `apply`, `auto`
- `case`, `copyof`,
- `default`, `define`,
- `final`,
- `immutable`, `implements`, `in`, `inline`,
- `let`,
- `macro`, `match`, `mutable`,
- `null`,
- `of`,
- `partial`, `promise`,
- `reference`, `relocatable`,
- `sealed`, `sizeof`, `static`, `supports`, `switch`
- `typedef`, `typeof`
- `unchecked`

### 132. Inline Assembly

Inline assembly is a way to access the EVM at a low level.

This bypasses several safety features and checks.

Use it only for tasks that require it, and only if you are confident using it.

1. The language "Yul" is used for inline assembly
2. An inline assembly block is marked as `assembly {...}` (code inside the curly braces is in Yul)

### 133. Inline Assembly Access to External Variables, Functions, and Libraries

### 134. Yul Syntax

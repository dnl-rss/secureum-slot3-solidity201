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

### 133. Assembly Access

Inline Assembly Access to External Variables, Functions, and Libraries:
1. access variables or identifiers using their name
2. local variables of value type are directly usable in inline assembly
3. local variables that refer to memory/calldata evaluate to the address of the variable in memory/calldata and not the value itself
4. local storage or state variables do not necessarily occupy a single full slot, so a single Yul identifier will not suffice.   
    - For variable `x`, the "address" is composed of a *slot* and a *byte offset* inside the slot
    - Use `x.slot` to retrieve the slot pointed to by the variable
    - Use `x.offset` to retrieve the byte offset
    - Using `x` itself throws an `Error`
5. solidity local variables are available for assignments within Yul
6. assembly local variables and function local variables may be assigned to. Take care when assigning to variables pointing to memory or storage, as it only changes the pointer (not the data)
7. the `x.slot` member of a local storage variable may be assigned to. For these (structs, arrays, mappings) the `x.offset` part is always zero. It is not possible to assign to `x.slot` or `x.offset` of a state variable.

### 134. Yul Syntax

Yul parses comment, literals, and identifiers in the same way as Solidity. Inside a code block, the following elements may be used:

| element | example |
| - | - |
| literals up to 32 characters | `0x123`, `42`, or `"abc"` |
| calls to builtin functions | `add(1, mload(0))` |
| variable declarations | `let x := 7`, `let x := add(y,3)`, `let x` |
| identifiers | `add(3, x)` |
| assignments | `x := add(y, 3)` |
| blocks with local variables scoped inside | `{ let x := 3 { let y := add(x, 1)} }` |
| if statements | `if lt(a, b) { sstore(0, 1) }` |
| switch statements | `switch mload(0) case 0 { revert() } default { mstore(0, 1) }` |
| for loops | `for { let i := 0 } lt(i, 10) { i := add(i, 1) } { mstore(i, 7) }` |
| function definitions | `function f(a, b) -> c { c := add(a, b) }` |

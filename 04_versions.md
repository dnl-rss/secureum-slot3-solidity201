### 135. Solidity v0.6.0 Breaking Semantic Changes

*Semantic changes* are those where existing code changes its behavior and the compiler will not notify the user about it.

- The resulting type of an *exponentiation* is the now *type of the base*. It used to be the smallest type that could hold both the type of the base and the type of the exponent, as with symmetric operations.
- The exponentiation *base* now allows *signed types*

### 136. Solidity v0.6.0 Explicitness Requirements

- Functions (and modifiers) may now be *overriden* only if they are marked `virtual` or defined in an `interface`. The new keyword `override` must be used. If the overriding function is defined in multiple bases, they must be listed in parentheses after the keyword `override(Base1, Base1)`.
- Member access to length of arrays is now always *read-only* even for `storage` arrays. It is no longer possible to resize storage arrays by assigning a new value to their length. Use `push()`, `push(value)`, or `pop()` instead. Otherwise, one may assigned a full array and overwrite the existing content. The reason behind this is to prevent storage collisions of large storage arrays.
- The new keyword `abstract` must be used to mark a `contract` that does not implement at least one of its functions. Abstract contracts cannot be created with the `new` operator. It is not possible to generate bytecode for them during compilation.
- A `library` must implement *all* of its functions, not just the internal ones.
- Variable *declarations in inline assembly* may no longer *shadow* any declaration outside the inline assembly block. If the name contains a dot, its prefix up the dot may not conflict with any declations outside the inline assembly block.
- *State variable shadowing* is now **disallowed**. A derived contract can only declare a state variable x if there is no visible state variable with the same name in any of its bases.

### 137. Solidity v0.6.0 Semantic and Syntactic Changes

- Conversions from external `function` types to `address` are now **disallowed**. Instead, use `<function>.address`
- The function `push(value)` for dynamic storage arrays does not return the new length anymore (it returns nothing)
- The unnamed function commonly referred to as "fallback function" was split up into a new fallback function defined using the `fallback()` keyword and a receive ether function using the `receive()` keyword
- If present, `receive()` is called whenever `calldata` is empty (whether or not ether is received). It is implicitly `payable`.
- The new `fallback()` function is called when no other function matched the selector, or upon receipt of a `msg` with empty `calldata` and no `receive()` is defined. `fallback()` is optionally `payable`; if it is not `payable` then calls with `value` will revert. You should only need to implement the new `fallback()` function if you are following an upgrade or proxy pattern.

### 138. Solidity v0.6.0 New Features

- The `try/catch` statement allows you to react on failed external calls.
- `struct` and `enum` types may be declared at file level
- Array slices can be used for `calldata` arrays. For example, `abi.decode(msg.data[4:],(uint, uint))` is a low-level way to decode the function call payload.
- Natspec supports multiple return parameters in developer
- Yul and Inline assembly have a new statement called `leave` that exits the current function
- Conversion from `address` to `address payabe` are now possible via `payable(x)` where `x` must be of type `address`.

### 139. Solidity v0.7.0 Breaking Semantic Changes

*Semantic changes* are those where existing code changes its behavior and the compiler will not notify the user about it.

- *Exponentiation* and *shifts of literals* by non-literals (`1 << x` or `2**x`) will always use either the type `uint256` (for non-negative literals) or `int256` (for negative literals) to perform the operations. Previously, the operation was performed in the type of the shift amount / the exponent which can be misleading.

### 140. Solidity v0.7.0 Syntactic Changes

*Syntactic changes* will cause existing contracts not to compile anymore.

- In external functions and contract creation calls, Ether and gas is now specified using a new syntax: `x.f{gas: 10000, value: 2 ether}(art1, arg2)`. The old syntax `x.f.gas(10000).value(2ether)(arg1, arg2)` will throw an `Error`
- The global variable `now` is deprecated, `block.timestamp` should be used instead. The single identifier `now` is too generic for a global variable and could give the impression that it changes during the transaction processing, whereas `block.timestamp` correctly reflects the fact that it is just a property of the block
- NatSpec comments on variables are only allowed for public state variables and not for local or internal variables.
- The token `gwei` is a keyword now and cannot be used as an identifier.
- String literals now can only contain printable ASCII characters and this also includes a variety of escape sequences, such as hexadecimal (`\xff`) and unicode escapes (`\u20ac`)
- Unicode string literals are supported now to accommodate valid UTF-8 sequences. They are identified with the unicode prefix: `unicode"Hello 😃"`
- State mutability of functions can now be restricted during inheritance. Functions with default state mutability can be overridden by `pure` and `view` functions, while `view` functions can be overriden by `pure` functions. Ath the same time, public state variables are considered `view` and even `pure` if they are constants.
- Disallow `.` in user defined function and variables names in inline assembly. It is still valid if you use solidity in Yul-only mode.
- Slot and offset of storage pointer variable `x` are accessed via `x.slot` and `x.offset` instead of `x_slot` and `x_offset`

### 141. Solidity v0.7.0 Removal of Unused or Unsafe Features

- If a struct or array contains a mapping, it can only be used in storage. Previously, mapping members were silently skipped in memory, which is confusing and error-prone.
- Assignments to structs or arrays in storage do not work if they contain mappings. Previously, mappings were silently skipped during the copy operation, which is misleading and error-prone.
- Visibility (public/ external) is not needed for constructors anymore: to prevent a contract from being created, it can be marked `abstract`. This makes the visibility concept for `constructor()` obsolete
- Disallow `virtual` for `library` functions. Since libraries cannot be inherited from, library functions should not be virtual.
- Multiple events with the same name and parameter types in the same inheritance hierarchy are disallowed.
- `using A for B` only affects the contract it is mentioned in. Previously, the effect was inherited. Now, you have to repeat the using statement in all derived contracts that make use of the feature.
- Shifts by signed types are disallowed. Previously, shifts by negative amounts where allowed, but reverted at runtime.
- The `finney` and `szabo` denominations are removed. They are rarely used and do not make the actual amount readily visible. Instead, explicit values like `1e20` or the very common `qwei` can be used.
- The keyword `var` cannot be used anymore. Previously, this keyword would parse but result in a type error and a suggestion about which type to use. Now, it results in a parser error.

### 142. Solidity v0.8.0 Breaking Semantic Changes

### 143. Solidity v0.8.0 New Restrictions

### 144. Zero Address Check

### 145. Tx origin Check

### 146. Overflow/Underflow Check

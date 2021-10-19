### 135. Solidity v0.6.0 Breaking Semantic Changes

Semantic changes are those where existing code changes its behavior and the compiler will not notify the user about it.
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

### 140. Solidity v0.7.0 Syntactic Changes

### 141. Solidity v0.7.0 Removal of Unused or Unsafe Features

### 142. Solidity v0.8.0 Breaking Semantic Changes

### 143. Solidity v0.8.0 New Restrictions

### 144. Zero Address Check

### 145. Tx origin Check

### 146. Overflow/Underflow Check

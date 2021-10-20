## Solidity v0.6.0 Changes

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

## Solidity v0.7.0 Changes

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

## Solidity v0.8.0 Changes

### 142. Solidity v0.8.0 Breaking Semantic Changes

*Semantic changes* are those where existing code changes its behavior and the compiler will not notify the user about it.

- Arithmetic operations revert on underflow and overflow. You can use `unchecked {...}` to use the previous wrapping behavior. Checks for overflow are very common, so they are the default to increase readability of code, even if it comes at a slight increase of gas costs.
- `abicoder v2` is activated by default. You can choose the old behavior using `pragma abicoder v1`; the syntax `pragma experimental ABIEncoderV2` is still valid, but it is deprecated and has no effect. If you want to be explicity, please use `pragma abicoder v2` instead.
- Exponentiation is right associated, i.e. the expression `a**b**c` is parsed as `a**(b**c)`. Before `v0.8.0` is as parsed as `(a**b)**c`. This is the common way to parse the exponentiation operator.
- Failing assertions and other internal checks like division by zero or arithmetic overflow do not use the `INVALID` opcode but instead the `REVERT` opcode. Specifically, they use error data equal to a function call to `Panic(uint256)` with an error code specific to the circumstances. This will save gas on errors while still allowing static analysis tools to distinguish these situations from a revert on invalid input, like a failing `require()`.
- If a byte array in `storage` is accessed whose length is encoded incorrectly, a `Panic()` is caused. A contract cannot get into this situation unless inline assemby is used to modify the raw representation of storage byte arrays.
- If constants are used in array length expressions, previous versions of Solidity would use arbitrary precision in all branches of the evaluation tree. Now, if constant variables are used as intermediate expressions, their values will be properly rounded in the same way as when they are used in run-time expressions.
- The type `byte` has been removed. It was an alias of `bytes1`.

### 143. Solidity v0.8.0 New Restrictions

Changes that might cause existing contracts to not compile anymore

- Explicit conversions from negative literals and literals larger than `type(uint160).max` to `address` are disallowed.
- Explicit conversions between literals and an integer type `T` are only allowed if the literal lies between `type(T).min` and `type(T).max`. In particular, replace usages of `uint(-1)` with `type(uint).max`.
- Explicit conversions between liberals and enums are only allowed if the literal can represent a value in the enum
- Explicit conversions between literals and `address` type (`address(literal)`) have the type `address` instead of `address payable`. One can get a `payable address` type using an explicit conversion (`payable(literal)`)
- Address literals have the type `address` instead of `address payable`. They can be converted to `address payable` by using an explicit conversion
- Function call options can be given only once:
    - `f{gas: 10000}{value: 1}()` is **invalid**
    - `f{gas: 10000, value: 1}()` is **valid**
- Global functions `log0`, `log1`, `log2`, `log3`, and `log4` have been removed. These are low-level functions that were largely unused. Their behavior can be accessed from inline assembly.
- `enum` definitions cannot contain more than 256 members. This will make it safe to assume that the underlying type in the ABI is always `uint8`
- Declarations with the name `this`, `super`, and `_` are disallowed, with the exception of `public` functions and events
- The global variables `tx.origin` and `msg.sender` have the type `address` instead of `address payable`. One can convert tham into `address payable` by using an explicit conversion.
- Explict conversion into `address` type always returns a non-payable `address` type.
- The `chainid` builtin in inline assembly is now considered `view` instead of `pure`

## Various Checks

### 144. Zero Address Check

The zero-address `address(0)` is 20-bytes of `0`s: `0x00000000000000000000`

 `address(0)` treated specially in Solidity contracts because the private key corresponding to this address is unknown.

Ether and tokens sent to `address(0)` cannot be retrieved and setting access control roles to this address also will not work (no private key to sign tx).

Therefore, `address(0)` should be used with care, and checks should be implemented for user supplied address parameters.

```solidity
require( destination != address(0), "Error: this will burn tokens");
```

### 145. Tx origin Check

Ethereum has two types of accounts: Externally Owned Accounts (EOA) and Contract Accounts.

Transactions can originate only from EOAs. Messages may originate from either.

In situations where contracts would like to determine if the `msg.sender` was a contract or not, checking if `msg.sender` is `tx.origin` is an effective check.

```solidity
require( msg.sender == tx.origin, "Error: msg from contract account");
```

### 146. Overflow/Underflow Check

Until Solidity `v0.8.0` introduced checked arithmetic by default, arithmetic was unchecked and susceptible to overflows and underflows, leading to critical vulnerabilities.

The recommended best practice for contracts before `v0.8.0` is to use OZ's SafeMath library for arithmetic.

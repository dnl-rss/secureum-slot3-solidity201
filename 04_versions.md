# Solidity Version Change Notes

The change notes from major solidity revisions `v0.6.0`, `v0.7.0`, and `v0.8.0` are included here.

There are distinctions between several types of changes:
- **Breaking semantic changes** are those where existing code will still compile, but its behavior may change, and the compiler will not notify the user about it.
- **Explicitness requirements** ...
- **Syntactic changes** will cause existing code not to compile anymore.
- **New features** include new keywords and properties
- **Removal** of unused or unsafe features
- **New restrictions** might cause existing code not to compile anymore.

## Solidity v0.6.0 Changes

### 135. Solidity v0.6.0 Breaking Semantic Changes

- The **resulting type of an exponentiation** is the now **type of the base**
    - used to be the smallest type that could hold both the type of the base and the type of the exponent, as with symmetric operations.
- The **exponentiation base** now allows **signed types**

### 136. Solidity v0.6.0 Explicitness Requirements

- Functions (and modifiers) may now be *overriden* only if they are marked `virtual` or defined in an `interface`.
    - the new keyword `override` must be used.
    - if the overriding function is **defined in multiple bases**, they must be listed in parentheses after the keyword `override(Base1, Base2)`
- Member **access to array length** is now **always read-only**, even for `storage` arrays.
    - **may no longer resize storage arrays by reassigning the length**
    - use `push()`, `push(value)`, or `pop()` methods instead.
    - otherwise, one may assign a full array and **overwrite** the existing content.
    - reason behind this change is to **prevent storage collisions** of large storage arrays
- The new keyword `abstract` must be used to mark a `contract` that **does not implement at least one of its functions**
    - `abstract` contracts cannot be created with the `new` operator
    - they **do not generate bytecode** at compilation
- A `library` **must implement *all* of its functions**, not just the internal ones.
- **Variable declarations in inline assembly may no longer shadow** any declaration outside the inline assembly block.
    - If the name contains a dot, its prefix up the dot may not conflict with any declarations outside the inline assembly block.
- **State variable shadowing** is now **disallowed**.
    - a derived contract can only declare a state variable `x` if there is no visible state variable `x` in any of its bases.

### 137. Solidity v0.6.0 Semantic and Syntactic Changes

- Conversions from `external` function types to `address` are now **disallowed**.
    - instead, use `<function>.address`
- The function `push(value)` for dynamic storage arrays **does not return the new length** anymore
    - it **returns nothing**
- The unnamed function commonly referred to as "fallback function" was split up into:
   - a **new `fallback()` function**
       - called when:
           - **no function matches the selector**
           - `msg.data` is **empty**, and **no** `receive()` is defined
       - optionally `payable`
           - calls with `msg.value` revert otherwise
       - only need to implement if you are following an upgrade or proxy pattern
   - a **`receive()` ether function**
       - called whenever `calldata` is empty (whether or not ether is received)
       - implicitly `payable`

### 138. Solidity v0.6.0 New Features

- The `try/catch` statement allows you to **react on failed external calls**
- `struct` and `enum` types may be **declared at file level**
- Array **slices** can be used for `calldata` arrays.
    - `abi.decode(msg.data[4:],(uint, uint))` is a low-level way to decode the function call payload
- **Natspec** supports **multiple return parameters** in developer tag
- **Yul** has a new statement called `leave` that **exits the current function**
- **Conversion** from `address` to `address payable` are now possible via `payable(x)`
    - `x` must be of type `address`.

## Solidity v0.7.0 Changes

### 139. Solidity v0.7.0 Breaking Semantic Changes

- **Exponentiation** and **shifts of literals by non-literals** (`1 << x` or `2**x`) will always use either the type:
   - `uint256` (for non-negative literals)
   - `int256` (for negative literals)
   - Previously, the operation was performed in the type of the shift amount / the exponent which can be misleading.

### 140. Solidity v0.7.0 Syntactic Changes

- **Ether and gas is now specified using a new syntax** in external functions and contract creation calls. The old syntax throw an `Error`:
    - new syntax: `x.f{gas: 10000, value: 2 ether}(art1, arg2)`.
    - old syntax `x.f.gas(10000).value(2 ether)(arg1, arg2)`
- **The global variable `now` is deprecated**:
    - instead use `block.timestamp`
    - `now` is too generic for a global variable and could give the impression that it changes during the transaction processing, whereas `block.timestamp` correctly reflects the fact that it is just a **property of the block**
- **NatSpec comments are only allowed for public state variables**
    - not for local or internal variables
- **The token `gwei` is a keyword now**
    - it cannot be used as an identifier anymore
- **String literals now can only contain printable ASCII characters and escape sequences**
    -  hexadecimal escape (`\xff`) and unicode escapes (`\u20ac`)
- **Unicode string literals support valid UTF-8 sequences**
    - identify with the `unicode` prefix: `unicode"Hello 😃"`
- **State mutability of functions can now be restricted during inheritance**
    - default can be overridden by `pure` and `view` functions
    - `view` can be overridden by `pure`
    - public state variables are considered `view` and even `pure` if they are constants.
- **Disallow `.` in user defined function and variables names in inline assembly**
    - it is still valid if you use solidity in Yul-only mode.
- **Slot and offset of storage pointer variable `x` have changed**
    - access via `x.slot` and `x.offset`
    - not via `x_slot` and `x_offset`

### 141. Solidity v0.7.0 Removal of Unused or Unsafe Features

- **Structs or arrays containing a `mapping` can only be used in `storage`**
    - previously, `mapping` members were silently skipped in `memory`, which is confusing and error-prone.
- **Structs or arrays containing a `mapping` in `storage` cannot be assigned to**
    - previously, mappings were silently skipped during the copy operation, which is misleading and error-prone.
- **Visibility (`public`/`external`) is not needed for `constructor()` anymore**
    - to prevent a contract from being created, it can be marked `abstract`
    - this makes the visibility concept for `constructor()` obsolete
- **Disallow `virtual` for `library` functions**
    - since libraries cannot be inherited from, library functions should not be `virtual`
- **Multiple events with the same name and parameter types in the same inheritance hierarchy are disallowed**
- **`using A for B` only affects the contract it is stated in**
    - previously, the effect was inherited
    - now, the using statement must be repeated in all derived contracts that make use of the feature
- **Shifts by signed types are disallowed**
    - previously, shifts by negative amounts where allowed, but reverted at runtime.
- **`finney` and `szabo` denominations are removed**
    - they are rarely used and do not make the actual amount readily visible
    - instead, explicit values like `1e20` or the very common `qwei` can be used.
- **Keyword `var` cannot be used anymore**
    - previously, this keyword would parse but result in a `TypeError` and a suggestion about which type to use
    - now, it results in a `ParserError`

## Solidity v0.8.0 Changes

### 142. Solidity v0.8.0 Breaking Semantic Changes

*Semantic changes* are those where existing code changes its behavior and the compiler will not notify the user about it.

- **Arithmetic operations revert on underflow and overflow**
    - use `unchecked {...}` to use the previous wrapping behavior
    - checks for overflow are very common, so they are the default to increase readability of code, even if it comes at a slight increase of gas costs
- **`abicoder v2` is activated by default**
    - revert to the old behavior using `pragma abicoder v1`
    - the syntax `pragma experimental ABIEncoderV2` is still valid, but it is deprecated and has no effect
    - to explicitly declare v2, use `pragma abicoder v2` instead.
- **Exponentiation is right associated**
    - the expression `a**b**c` is parsed as `a**(b**c)`, this is the common way to parse the exponentiation operator
    - before `v0.8.0` it was as parsed as `(a**b)**c`
- **Failing assertions and other internal checks like division by zero or arithmetic overflow do not use the `INVALID` opcode but instead the `REVERT` opcode**
    - specifically, they use error data equal to a function call to `Panic(uint256)` with an error code specific to the circumstances.
    - this will save gas on errors while still allowing static analysis tools to distinguish these situations from a revert on invalid input, like a failing `require()`.
- **If a byte array in `storage` is accessed whose length is encoded incorrectly, a `Panic()` is caused**
    - A contract cannot get into this situation unless inline assembly is used to modify the raw representation of storage byte arrays.
- **Constants used in array length expressions are now rounded in the same way as when they are used in run-time expressions**
    - previous versions of Solidity would use arbitrary precision in all branches of the evaluation tree
- **The type `byte` has been removed**
    - it was an alias of `bytes1`.

### 143. Solidity v0.8.0 New Restrictions

- **Explicit conversions from negative literals and literals larger than `type(uint160).max` to `address` are disallowed**
- **Explicit conversions between literals and an integer type `T` are only allowed if the literal lies between `type(T).min` and `type(T).max`**
    - In particular, replace usages of `uint(-1)` with `type(uint).max`.
- **Explicit conversions between literals and enums are only allowed if the literal can represent a value in the enum**
- **Explicit conversions via `address(literal)` have the type `address` instead of `address payable`**
    -  use `payable(literal)` to convert to `address payable`
- **Address literals have the type `address` instead of `address payable`**
    - They can be converted to `address payable` by using an explicit conversion
- **Function call options can be given only once**
    - `f{gas: 10000}{value: 1}()` is **invalid**
    - `f{gas: 10000, value: 1}()` is **valid**
- **Global functions `log0`, `log1`, `log2`, `log3`, and `log4` have been removed**
    - These are low-level functions that were largely unused.
    - Their behavior can be accessed from inline assembly.
- **`enum` definitions cannot contain more than 256 members**
    - this will make it safe to assume that the underlying type in the ABI is always `uint8`
- **Declarations with the name `this`, `super`, and `_` are disallowed**
    - exception to this rule in `public` functions and events
- **The global variables `tx.origin` and `msg.sender` have the type `address` instead of `address payable`**
    - One can convert tham into `address payable` by using an explicit conversion.
- **Explicit conversion into `address` type always returns a non-payable `address` type**
- **The `chainid` builtin in inline assembly is now considered `view` instead of `pure`**

## Various Checks

### 144. Zero Address Check

The zero-address `address(0)` is 20-bytes of `0`s: `0x00000000000000000000`
- `address(0)` treated specially in Solidity contracts because the private key corresponding to this address is unknown.
- Ether and tokens sent to `address(0)` cannot be retrieved
- setting access control roles to `address(0)` cannot sign messages
- `address(0)` should be used with care, and checks should be implemented for user supplied address parameters.

```solidity
require( destination != address(0), "Error: this will burn tokens");
```

### 145. Tx origin Check

Ethereum has two types of accounts: Externally Owned Accounts (EOA) and Contract Accounts.
- **Transactions** can originate only from **EOAs**
- **Messages** may originate from either **EOAs or contracts**

To determine if the `msg.sender` is a contract or not, checking if `msg.sender` is `tx.origin` is an effective check.

```solidity
require( msg.sender == tx.origin, "Error: msg from contract account");
```

### 146. Overflow/Underflow Check

Until Solidity `v0.8.0` introduced checked arithmetic by default, arithmetic was unchecked and susceptible to overflows and underflows, leading to critical vulnerabilities.

The recommended best practice for contracts before `v0.8.0` is to use OZ's `SafeMath` library for arithmetic.

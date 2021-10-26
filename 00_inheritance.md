### 102. Inheritance

Solidity supports **multiple inheritance**, including **polymorphism**.

1. **Polymorphism** means that a function call (internal and external) **always executes the function** in the **most derived contract** of the inheritance hierarchy, which matches the specified name and parameter types
2. When a contract **inherits from multiple contracts**, only a **single contract is instantiated**
    - the code from all the base contracts is compiled into the created contract
3. Solidity supports function **overriding**
    - **base functions** can be **overridden** by **inheriting contracts**, changing their behavior
    - **base functions** must be marked `virtual`
    - **overriding function** must be marked `override`
4. There are several **problems with multiple inheritance** that languages must address:
    - **Diamond Problem**:
        - *C3 linearization* forces a specific order in the directed acyclic graph (DAG) of base classes
        - when a **function that is defined multiples times is called**, the given bases are searched from **right to left** (depth-first) to stop at the first match (python goes left to right).

### 103. Contract Types

In addition to the regular `contract` type, contracts may be marked `abstract contract`, `interface`, or `library`

1. `abstract contract`
    - **at least one function is not implemented**
2. `interface`:
    - **no functions are implemented**
    - further restrictions:
        - cannot inherit from `contract` types, but can from `interface`
        - declared functions must be `external`
        - cannot declare a `constructor()`
        - cannot declare state variables
3. `library`:
    - **deployed once to specific address** and **code is reused** via `DELEGATECALL`
    - library functions are **executed in the context of the calling contract**

### 104. Using For

The directive `using A for B` is used to **attach library functions** from `library A` to any `type(B)`,
- the directive **is only active** within the context of the current `contract` and its functions (it has no effect outside that contract)
- the directive **may only be declared inside a contract, outside of its functions**

The attached library functions receive the object `type(B)` that they are called on as their first parameter

> This contract attaches the functions from `SafeMath` library to `uint256` variables:

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.6.0;

import "https://github.com/OpenZeppelin/openzeppelin-contracts/blob/v3.0.0/contracts/math/SafeMath.sol";

contract LibraryDemo {

    // attach "SafeMath" library functions to uint256 type variables
    using SafeMath for uint256;

    // declare a uint256 state variable, which has SafeMath functions attached
    uint256 public counter;

    function increment() public {

        // calls SafeMath function add(uint256 a, uint256 b)
        // where the first parameter "a" is "counter"
        // reverts if counter exceeds 2**256
        counter = counter.add(1);
    }

    function decrement() public {

        // reverts if counter falls below 0
        counter = counter.sub(1);
    }
}
```

> Note that the SafeMath function `add(uint256 a, uint256 b)` receives the first parameter `a` as the variable `counter` when it is called in the contract above

```solidity
function add(uint256 a, uint256 b) internal pure returns (uint256) {
    uint256 c = a + b;
    require(c >= a, "SafeMath: addition overflow");

    return c;
}
```

### 105. Base Class Functions

It is possible to **call functions from base classes**, further up the **inheritance hierarchy**:
- `ContractName.functionName()`
    - **explicitly calls** the function in another contract
    - may be a parent, grandparent, etc
- `super.functionName()`
    - calls the function in the contract **one level up**

```solidity
// TODO: needs code example with more explanation
```

### 106. State Variable Shadowing

A derived contract may only declare a state variable `x` if there is **no visible state variable** `x` in any of its bases.

This throws an `Error` after `v0.6.0` due to confusion and security concerns with derived variables.

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.6.0;

contract Base {
    uint public myNum;
}

contract OnceDerived is Base {
    // this fails due to shadowing
    // uint public myNum;
    // DeclarationError Identifier already declared
}
```

### 107. Function Overriding Changes


A function marked `override` in a derived class may change the behavior of a function marked `virtual` in a base class:
- redefine the **logic**
- alter the **visibility**:
    - `external` to `public`
- change the **mutability**:
    - non-payable to `view` or `pure`
    - `view` to `pure`
    - `payable` cannot be overridden

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.7;

contract Base {

    function myFunction() virtual external {}

    function otherFunction() public {
        // this fails. myFunction() is external and cannot be called within this contract
        // myFunction();
        // DeclarationError: Undeclared identifier. "myFunction" is not (or not yet) visible at this point    
    }
}

contract OnceDerived is Base {

    function myFunction() override public {
        // changes the visibility of myFunction, inherited from Base, from external to public
    }
    /*
    function otherFunction() override public {
        // this fails. otherFunction, inheritied from Base, is not virtual and cannot be ovverriden
        // TypeError: trying to override a non-virtual function
    }
    */
    function anotherFunction() public {
        // this succeeds. myFunction is now public and may be called within OnceDerived contract
        myFunction();
    }
}

```
### 108. Virtual Functions

Functions marked `virtual` may **not** define an implementation in the `contract`.

In `interface` types, all functions are automatically considered `virtual` and do not need to be marked so.

`private` functions may **not** be `virtual`

### 109. Public State Variable Override

`public` state variables have automatic getter functions generated by the solidity compiler that return the value of the variable.

The getter function will `override` external functions if their parameter and return types match.

`public` state variables cannot be overriden themselves.

### 110. Modifier Overriding

Function `modifier` types can `override` each other.

This works in the same way as `function` overriding (except that modifiers are not overloaded).

The overriding modifier must be marked `override` and the overriden modifier must be `virtual`.

### 111. Base Constructors

The `constructor()` functions of all base contracts are called with the following *linearization rules*:
- If base constructors *have arguments*, derived contracts must *specify them* (either in inheritance list or in derived constructor)

### 112. Name Collision Error

An `Error` is thrown when any of the following pairs in a contract have the same name due to inheritance:
1. a `function` and a `modifier`
2. a `function` and an `event`
3. an `event` and a `modifier`

### 113. Library Restrictions

In comparison to contracts, libraries are more restricted:
- cannot have state variables
- cannot inherit nor be inherited
- cannot receive Ether
- cannot be destroyed
- can only access state variables of the calling contract if explicitly supplied (has no way to name them otherwise)
- functions can only be called directly (without `DELEGATECALL`) if they do not modify the state (`view` or `pure` functions) because libraries are assumed to be stateless

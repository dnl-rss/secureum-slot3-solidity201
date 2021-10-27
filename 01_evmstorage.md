### 114. EVM storage

EVM contract `storage` is a **key-value store**:
- **mapping** of **256-bit words** to **256-bit words**
- accessed with EVM's `SSTORE`/`SLOAD` instructions.
- all locations in `storage` are initialized as *zero*.

### 115. Storage Layout

**State variables** are stored within **slots** contained in `storage`
- slots contain **256-bit words**
- variables **map to slots compactly**, such that **multiple values** sometimes use the **same slot**
- data is **stored contiguously**, item-after-item, starting with the first state variable at slot 0.
- **mappings** and **dynamic arrays** are an **exception** to this.

### 116. Storage Layout Packing

**Contiguous variables** are stored **compactly**, within a single slot, if their **byte size** allows.

The variables must require **less than 32 bytes** in `storage`:
1. **first item** is stored **lower order aligned**
2. **value types** only use the **bytes needed** to represent them
3. value types that will **not fit**, are stored in the **next slot**

```solidity
contract StorageLayout {
    uint256 a; // fills entire slot 0 (256-bits)
    uint128 b; // fills half of slot 1 (128-bits)
    uint256 c; // fills entire slot 2 (256-bits)
    uint126 d; // fills half of slot 3 (128-bits)
    uint128 e; // fills half of slot 3 (128-bits)
}
```
### 117. Storage Layout & Structs/Arrays

**Structs and array data** are stored according to these **rules**:
1. **always start a new slot**
2. **following variables also always start a new slot**
3. **elements** are stored **compactly**, as if they were given as individual values (in same slot as a struct?)

### 118. Storage Layout & Inheritance

For **derived contracts**, the ordering of state variables is determined by **C3 linearization** of inherited contracts:
- **starting** with the most **base-ward contract**
- **ending** with the most **derived contract**

If allowed by the above rules in [117], state **variables from different contracts may share the same slot**.

```solidity
contract Base {
    uint128 a; // declared first. fills half of slot 0
}

contract Derived is Base {
    uint128 b; // declared second. fills other half of slot 0
}
```

### 119. Storage Layout & Types

A developer may use **reduced size types** with storage values so that the elements can pack compactly into a single storage slot:
- **pro**: may **combine multiple reads or writes** into a **single operation**
- **con**: this **increases the gas cost** of reading or writing an **isolated variable** within the slot

### 120. Storage Layout & Ordering

**Ordering of storage variables** and struct members affects the **compactness of packing**, and thus effecting the **gas costs**:
- declaring in order of `uint128`, `uint128`, `uint256` uses **2 slots**
- declaring in order of `uint128`, `uint256`, `uint128` uses **3 slots**

`SLOAD` and `SSTORE` are some of the most expensive EVM operations, costing up to 2100 or 20_000 gas, respectively. Thus, considering the efficient packing of variables is very important for a contract engineer.

### 121. Storage Layout for Mapping & Dynamic Arrays

Storage layout rules for mappings and dynamic arrays are more complex than value types.

Mappings and dynamic arrays are **unpredictable in size**
- they cannot be stored "in-between" slots of the state variables preceding and following them
- the variable itself is stored in a **single slot** `p` (32 bytes), according to the rules above [117]
- the **elements** are stored starting at a **different storage slot**, computed using a `keccak256` hash
- this provision allows elements to be added to the array while insuring that their slot will not shadow an existing variable

#### 122. Storage Layout for Dynamic Arrays

For dynamic array stored in slot `p`:
- slot `p` stores the only **number of elements** in the array (except for byte arrays and strings).
- array **elements** are stored starting at slot `keccak256(p)`.
- array **elements** are stored **compactly**, similarly to a static array

*Dynamic arrays containing dynamic arrays* apply these rules *recursively*.

#### 123. Storage Layout for Mappings

For a mapping stored in slot `p`:
- slot `p` stays **empty** (it is needed to ensure the data of two consecutive mappings is stored in different locations)
- a **value mapped to key** `k` is stored at **slot** `keccack256(h(k).p)`:
    - `.` is concatenation
    - `h(k)` is a function applied to `k` depending on its type:
        - For **value types**, `h(k)` **pads the value to 32 bytes**, as if storing in memory. The data is offset within the computed slot.
        - For **string and byte arrays**, `h(k)` **does not pad** the value. The computed slot marks the start of the data.

> TODO: Needs clarity

### 124. Storage Layout for Bytes & Strings

Bytes and strings are encoded identically, similar to arrays. With an interesting optimization

For **byte arrays** of **32 bytes or longer** at **slot** `p`:
- **slot** `p` stores `length*2 + 1` in the **lowest order bytes**
- **elements** are **stored separately** starting in slot `keccack256(p)`

However, for **byte arrays** **shorter than 32 bytes** at slot `p`:
- **elements are also stored in slot `p`**  
- **slot** `p` stores `length * 2` in the **lowest order bytes**
- **slot** `p` also **stores the elements** in the **higher order bytes**

This convention allows the EVM to determine where the array stores its elements, separately or together, by examining the **lowest order bit** of **slot** `p`:
- `0`: short array, stored together in slot `p`
- `1`: long array, stored separately in slots starting at `keccack256(p)`

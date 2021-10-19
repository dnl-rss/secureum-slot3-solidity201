### 114. EVM storage

`storage` is a key-value store *mapping 256-bit words to 256-bit words* and is accessed with EVM's `SSTORE`/`SLOAD` instructions.

All locations in `storage` are initialized as *zero*.

### 115. Storage Layout

State variables of contracts are stored in `storage` in slots.

Slots contain 256-bit words.

Variables maps to slots *compactly* such that *multiple values* sometimes use the *same slot*.

Data is *stored contiguously*, item-after-item, starting with the first state variable at slot 0.

Mappings and dynamic arrays are an exception to this.

### 116. Storage Layout Packing

If the byte size of two or more *contiguous variables* allows, they are stored *compactly* to occupy a *single slot*:

The variables must require less than *32 bytes* of storage:
1. The first item of the slot is stored *lower order aligned*
2. Value types *only use the bytes needed* to represent them
3. If a value type will not fit, it is stored in the *next slot*

### 117. Storage Layout & Structs/Arrays

Structs and array data are stored according to these rules:
1. Structs and arrays always start a *new slot*
2. *Variables following a struct* or array data always start a *new slot*
3. *Elements* of structs and arrays are stored *compactly*, just as if they were given as individual values (in same slot as a struct?)

### 118. Storage Layout & Inheritance

For derived contracts, the ordering of state variables is determined by *C3 linearization* of inherited contracts:
- starting with the most *base-ward contract*
- ending with the most *derived contract*

If allowed by the above rules in [117], state variables from different contracts *may share the same slot*.

### 119. Storage Layout & Types

If might be beneficial to use *reduced size types* with storage values because the compiler will pack multiple elements into one storage slot, therefore combining *multiple reads or writes* into a *single operation*.
- This has a *negative effect* if values within a slot are not read/written at the same time. When one value is written, the slot must first be read and them combined with the new value in the same slot such that the other data is not destroyed.

### 120. Storage Layout & Ordering

Ordering of storage variables and struct members affects how tightly they can be *packed*, thus effecting the *gas costs*:
- declaring in order of `uint128`, `uint128`, `uint256` uses **2 slots**
- declaring in order of `uint128`, `uint256`, `uint128` uses **3 slots**

`SLOAD` and `SSTORE` are some of the most expensive EVM operations, costing up to 2100 or 20_000 gas, respectively. Thus, considering the efficient packing of variables is very important for a contract engineer.

### 121. Storage Layout for Mapping & Dynamic Arrays

Mappings and dynamic arrays are *unpredictable in size*

Thus, they cannot be stored "in-between" the state variables preceding and following them.

Instead, the variable itself is stored in a single slot `p` (32 bytes), with regards to the rules above [117].

Its elements are stored starting at a *different storage slot*, computed using a `keccak256` hash.

#### 122. Storage Layout for Dynamic Arrays

For an array stored in slot `p`, this slot stores the only *number of elements* in the array (except for byte arrays and strings).

The array data is stored starting at slot `keccak256(p)`.

The array data laid out similarly to a static array: one element following another, potentially sharing storage slots if elements are not longer than 16 bytes.

*Dynamic arrays containing dynamic arrays* apply these rules *recursively*.

#### 123. Storage Layout for Mappings

For a mappings stored in slot `p`, this slot stays **empty**, but it is still needed to ensure the data of two consecutive mappings is stored in different locations.

The value mapped to key `k` is stored at slot `keccack256(h(k) . p)` where `.` is concatenation and `h(k)` is a function applied to `k` depending on its type:
- For value types, `h(k)` pads the value to 32 bytes as if storing in memory. The data is offset within the computed slot.
- For string and byte arrays, `h(k)` does not pad the value. The computed slot marks the start of the data.

> TODO: Needs clarity

### 124. Storage Layout for Bytes & Strings

Bytes and strings are encoded identically, similar to arrays. With an interesting optimization

For arrays of *32 bytes or longer* at slot `p`:
- slot `p` stores `length * 2 + 1` in the lowest order bytes
- elements are stored separately in slots `keccack256(p)`

However, for arrays *shorter than 32 bytes* at slot `p` the elements are also stored in slot `p`:  
- slot `p` stores `length * 2` in the lowest order bytes
- slot `p` also stores the elements in the higher order bytes

Examining the *lowest order bit* of slot `p` allows the EVM to determine if the array is stored together or separately:
- `0`: short array, stored together
- `1`: long array, stored separately

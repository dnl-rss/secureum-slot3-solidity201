### 125. EVM memory

EVM `memory` is:
- stack based
- linear
- addressable at byte level
- accessed with `MSTORE`, `MSTORE8`, and `MLOAD` opcodes
- zero-initialized for all locations

### 126. Memory Layout

- Solidity places **new memory objects** at the **free memory pointer**
- Memory is **never freed**
- The **free memory pointer** points to `0x80` initially.
- The memory layout only really matters, from a security perspective, if someone is using solidity assembly to manipulate the memory.
### 127. Reserved Memory

Solidity reserves **four 32-byte slots** with specific byte ranges (inclusive of endpoints) used as follows:
1. `0x00` - `0x3f` (64 bytes): **scratch space for hashing**
2. `0x40` - `0x5f` (32 bytes): **currently allocated memory size** (aka free memory pointer)
3. `0x60` - `0x7f` (32 bytes): **zero slot** (used for initialization of dynamic memory arrays and should never be written to)

### 128. Memory Layout & Arrays

**Elements** in `memory` arrays always occupy **multiples of 32 bytes**
- also true for `byte[]`
- not true for `bytes` or `string`
- multi-dimensional `memory` arrays are **pointers** to underlying `memory` arrays
- for dynamic arrays:
    - the **length** is stored at the **first slot**
    - **followed by the array elements**

### 129. Free Memory Pointer

There is a **free memory pointer** at position `0x40`.
- the **initial pointer value** is `0x80`, just beyond the reserved slots
- points to **allocatable memory**
- whenever **memory is allocated**, it **updates the free memory pointer**

### 130. Zeroed Memory

There is **no guarantee** that the **memory has not been used before**,
- do not assume that its contents are zero bytes
- there is no built-in mechanism to release for free allocated memory
- security impact is that memory may not be zero, if it has been manipulated by assembly code

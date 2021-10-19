### 125. EVM memory

EVM `memory` is linear and addressable at byte level.
- Accessed with `MSTORE`, `MSTORE8`, and `MLOAD` opcodes
- All locations in memory are *zero-initialized*

### 126. Memory Layout

Solidity places new memory objects at the free memory pointer.

Memory is never freed.

The free memory pointer points to `0x80` initially.

### 127. Reserved Memory

Solidity reserves four 32-byte slots with specific byte ranges (inclusive of endpoints) used as follows:
1. `0x00` - `0x3f` (64 bytes): scratch space for hashing
2. `0x40` - `0x5f` (32 bytes): currently allocated memory size (aka free memory pointer)
3. `0x60` - `0x7f` (32 bytes): zero slot (used for initialization of dynamic memory arrays and should never be written to)

### 128. Memory Layout & Arrays

Elements in memory arrays in Solidity always occupy multiples of 32 bytes (also true for `byte[]` but not `bytes` nor `string`)
- multi-dimensional memory arrays are *pointers* to memory arrays
- the length of a dynamic array is stored at the first slot and followed by the array elements

### 129. Free Memory Pointer

There is a free memory pointer at position `0x40` in memory.

To allocate memory, use the memory starting from where this pointer points and update it.

Considering the reserved memory, allocatable memory starts at `0x80`, which is the initial value of the free memory pointer.

### 130. Zeroed Memory

There is no guarantee that the memory has not been used before, thus do not assume that its contents are zero bytes.

There is no built-in mechanism to release for free allocated memory.

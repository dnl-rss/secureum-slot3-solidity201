
### 193. Dappsys DSProxy

Implements a proxy deployed as a standalone contract, which can then be used by the owner to execute code.

A user would pass in the bytecode for the contract as well as the calldata for the function they want to execute.

The proxy will create a contract using the bytecode. It will then `DELEGATECALL` the function and arguments specified in the calldata.

### 194. Daapsys DSMath

Provides arithmetic functions for the common numerical primitive types of Solidity.
- safely add, subtract, multiply, and divide uint numbers without fear of integer overflow.
- find the minimum and maximum of two numbers
- two new high level concepts:
    1. `wad`: decimal number with 18 digit precision
    2. `ray`: decimal number with 27 digit precision

The standard functions are in the `uint` set, so their function names are not prefixed: `add`, `sub`, `mul`, `min`, and `max`. There is not div function, as divide-by-zero checking is built into the Solidity compiler.

Int functions have an `i` prefix: `imin` and `imax`

Wad functions have a `w` prefix: `wmul` and `wdiv`

Ray functions have an `r` prefix: `rmul`, `rdiv`, and `rpow`

### 195. Daapsys DSAuth

Provides a flexible and updatable auth pattern which is completely separate from applicaiton logic.

By default, the auth modifier will restrict function-call access to the including contract owner and the including contract itself.

The auth modifier provided by `DSAuth` triggers the internal `isAuthorized` function to require that the `msg.sender` is authorized, i.e. the sender is either:
1. the contract owner
2. the contract itself
3. has been granted permission via a specified authority

### 196. Daapsys DSGuard

Manages an Access Control List which maps source and destination addresses to function signatures.

Intended to be used as an authority for `DSAuth` where it acts as a lookup table for the `canCall` function to provide `bool` answers as to whether a particular address is authorized to call a given function at another address.

The ACL is a mapping of `[src][dst][sig] => bool`, where an address `src` can be either permitted or forbidden access to a function `sig` at address `dst` according to the `bool` value.

When used as an authority by `DSAuth` the `src` is considered to be the `msg.sender`, the `dst` is the including contract and `sig` is the function which invoked the auth modifier.

### 197. Dappsys DSRoles

A role-driven authority for ds-auth which facilitates access to list of user roles and capabilities.

Works as a set of lookup tables for the `canCall` function to provide `bool` answers as to whether a user is authroized to call a given function at a given address.

`DSRoles` provides 3 different ways of permitting/forbidding function call access to users:
1. **Root Users**: any users added to `_root_users` whitelist will be authorized to call any function, regardless of what roles or capabilities might be defined
2. **Public Capabilities**: public capabilities are global capabilities which apply to all users and take precedence over any user specific role-capabilities which might be defined
3. **Role Capabilities**: capabilities which are associated with a partifular role. Role capabilities are only checked if the user does not have root access and the capability is not public.

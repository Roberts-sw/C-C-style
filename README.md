C/C++ style for hardware interfacing
---
## A little background:
### Data types
Data types are used for efficiently working with constant or variable data.
Historically the smallest were the most efficient, but nowadays this only
applies to storage, as microcontrollers, even 8-bit ones, tend to have at
least some registers and instructions to handle "more-than-a-byte" data.
Mixing types in expressions can lead to some overhead hidden by the compiler,
and to failures where the programmer wasn't aware of how the overhead is
implemented. Most of the times it comes from "type promotion" arranged by
the C compiler and C++ compilers will force you produce more bloated source
code by explicitly doing the typecasts.
In C I usually stick to unsigned or signed integer types in expressions
where the sizes differ and only cast the result if needed.
- _Bool (C99)
- _Complex (C99)
- char: minimum size 8 bits, POSIX requires it to be exactly 8 bits
- double
- float
- int: minimum size 16 bits
- long (or as modifier to int or long or double): minimum size 32 bits
- short (or as modifier to int)
- union: storage type with element type choice
- struct: storage type for multiple elements
- data type modifiers:
  - signed
  - unsigned
- float size restrictions: float <= double <= long double
- int size restrictions: char <= short <= int <= long <= long long

### Type qualifiers
IO-functionality is often decribed as an address being a
constant pointer to a volatile aggregate type
containing the IO-registers and void fills for unused addresses.
Luckily even the old C89 standard has the words `const` and `volatile`:
- _Atomic (C11)
- const (C89)
- volatile (C89)
- restrict (C99)

## My style
C99 defines width-specified data types, useful for hardware control, accessible
with inclusion of `<inttypes.h>` or `<stdint.h>`. 
Hardware control needs them together with address definitions. 
If it is known which microcontroller and
compiler are used, it is trivial to typedef exact width data types for signed
and unsigned types. 
I oten use a small header file with typedefs for the names
s08, u08, s16, u16, s32, u32, s64, and u64. It should be clear which ones
are signed and unsigned respectively, and how many bits constitue them.

Address definitions are in capitals with a trailing underscore for single 
registers and a leading underscore for register-arrays, without underscore
for the indexes into the register-arrays. Bit-constants for specific
registers are named `bxy_R_E`, with xy the decimal lsb-shift designator in
register R, known by the name E in the hardware-manual.
Multi-bit register-constants are similarly defined as `x#_R_E`, where \# is
a hexadecimal value of register-width divided by 4 nibbles.
- `R_`, like a "private" variable, example: `SDCMD_'
- `_A`, like a constant "pointer", example: `_IPR[17]`
- `AI`, like a "constant" value, example: `_IPR[IRQ0]`
- `bxy_R_E`, example: `b04_DMINT_DTIE`
- `x#_R_E`, example: `xC000_ICU_IRQFLTC0_FCLKSEL7_PCLKBdiv64`

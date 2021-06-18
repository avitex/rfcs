- Feature Name: `saturating_bit_shifts`
- Start Date: (fill me in with today's date, 2021-06-18)
- RFC PR: [rust-lang/rfcs#0000](https://github.com/rust-lang/rfcs/pull/0000)
- Rust Issue: [rust-lang/rust#0000](https://github.com/rust-lang/rust/issues/0000)

# Summary
[summary]: #summary

This RFC proposes to add `saturating_shl` and `saturating_shr` to integer types
to accommodate shifting equal to and greater than the total number of bits
without requiring a check or panicking.

# Motivation
[motivation]: #motivation

This RFC would bring available shift operations inline with other `saturating_*`
functionality on integer types.

Currently if a developer wishes to shift a total number of times equal or
greater than the number of bits in the type, saturating at the value `0`, they
must add a check manually before shifting otherwise they will be met with a
panic.

# Guide-level explanation
[guide-level-explanation]: #guide-level-explanation

A shift value less than the number of bits in the type will be functionally
equivalent to shifting normally. A shift value equal or greater than the number
of bits in the type will always produce the saturated value `0`.

# Reference-level explanation
[reference-level-explanation]: #reference-level-explanation

```rust
// Here we shift left the max number of times we can in a `u8`.
assert_eq!(0b0000_0001_u8 << (u8::BITS - 1), 0b1000_0000_u8);

// Again this is the same in the `saturating_shl` variant.
assert_eq!(0b0000_0001_u8.saturating_shl(u8::BITS - 1), 0b1000_0000_u8);

// When we shift left or right, to and above the max number of bits in a u8 (8) our result is always `0`.
assert_eq!(0b0000_0001_u8.saturating_shl(u8::BITS), 0b0000_0000_u8);
assert_eq!(0b1000_0000_u8.saturating_shr(u8::BITS), 0b0000_0000_u8);
```

Implementation where `$ty` is an integer (ie. `u8`, `i8`, etc):

```rust
impl $ty {
    fn saturating_shl(self, rhs: u32) -> $ty {
        if rhs >= $ty::BITS {
            0
        } else {
            self << rhs
        }
    }

    fn saturating_shr(self, rhs: u32) -> $ty {
        if rhs >= $ty::BITS {
            0
        } else {
            self >> rhs
        }
    }
}
```

# Drawbacks
[drawbacks]: #drawbacks

No drawbacks.

# Rationale and alternatives
[rationale-and-alternatives]: #rationale-and-alternatives

Not implementing this will require developers to continue to manually implement
this trival functionality.

# Prior art
[prior-art]: #prior-art

This is inline with other saturating functionality on integer types.

# Unresolved questions
[unresolved-questions]: #unresolved-questions

No unresolved issues.

# Considering Portability

## Know Your Types

Most portability issues that generate warnings are because we are not careful about our types. Standard library and arrays are indexed with `size_t`. Standard container sizes are reported in `size_t`. If you get the handling of `size_t` wrong, you can create subtle lurking 64-bit issues that arise only after you start to overflow the indexing of 32-bit integers. char vs unsigned char.

http://www.viva64.com/en/a/0010/

## Other Concerns

Most of the other concerns in this document ultimately come back to portability issues. [Avoid statics](07-Considering_Threadability.md#statics) is particularly of note.

# Considering Portability

## Know your types

Most portability issues that generate warnings are because we are not careful about our types. standard library and arrays are indexed with size_t. Standard containers sizes are reported in size_t. If you get the handling of size_t wrong, you can create subtle lurking 64bit issues that arise only after you start to overflow the indexing of 32bit integers. char vs unsigned char.

http://www.viva64.com/en/a/0010/

# Introduction: Two Approaches to ID Generation

When marking items (e.g., diamonds) that require **unique identifiers**, there are two common approaches:

1. **Full UUID (v4)**  
   - **128 bits** of random data, typically serialized as a 36-character string (e.g., `123e4567-e89b-12d3-a456-426614174000`).  
   - Very well-known standard, supported by abundant libraries.  
   - **Downside**: Larger data footprint → bigger codes if embedding in a barcode or 2D symbol.  

2. **Compact “Day + Random”**  
   - ~**38 bits** (14 bits for “days since reference date,” + 24 bits random).  
   - Stored in **5 bytes**, often shown as a **10-character hex** string.  
   - **Downside**: Not a universal format like UUID, but much **smaller** code size.

Below is a summary comparison:

| **Aspect**         | **UUID v4**                                            | **Compact ID**                                                |
|--------------------|--------------------------------------------------------|----------------------------------------------------------------|
| **Bit Length**     | 128 bits (16 bytes)                                    | ~38 bits (5 bytes, 2 bits unused)                              |
| **Typical String** | 36 chars (hyphenated hex)                              | 10 chars hex (if you convert the 5 bytes to hex)               |
| **Collision Risk** | Astronomically low (far below 1 in 10^18 for many use cases) | Very low for millions of items/day (< 1 in 10^6 at daily scale) |
| **Time Element**   | None (pure random)                                     | 14-bit “day offset” ensures chronological uniqueness           |
| **Ease of Use**    | Well-known, trivial libraries exist (`uuid` in Node)   | Simple logic, minimal overhead, though requires a small snippet of code |
| **Barcode Size**   | Larger (128 bits → more modules in 2D codes)           | Smaller (38 bits → fewer modules)                               |
| **Recognition**    | Universally recognized format                          | Custom format but easy to parse once documented                |

**Choosing Between Them**  
- If you need a **widely recognized** standard and **code size** is not critical, **UUID v4** is simplest.  
- If you’re marking **millions of tiny surfaces** (e.g., diamonds) and code space is **very tight**, the **compact ID** approach can reduce your 2D symbol by more than half.

You can find more details in the following sub-guides:

- [**Compact ID Guide**](./id-generation-guide-compact.md)  
- [**UUID v4 Guide**](./id-generation-guide-uuid.md)  

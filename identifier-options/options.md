# Introduction: Two Approaches to ID Generation

When marking items (e.g., diamonds) that require **unique identifiers**, there are many ways to generate IDs along a **spectrum** of complexity and size. Below, we highlight **two examples** at different points on that spectrum:

1. **Full UUID (v4)**  
   - **128 bits** of random data, typically serialized as a 36-character string (e.g., `123e4567-e89b-12d3-a456-426614174000`).  
   - Very well-known standard, supported by abundant libraries.  
   - **Downside**: Larger data footprint → bigger codes if embedding in a barcode or 2D symbol.

2. **Compact “Day + Random”**  
   - ~**38 bits** (14 bits for “days since reference date,” + 24 bits random).  
   - Stored in **5 bytes**, often shown as a **10-character hex** string.  
   - **Downside**: Not a universal format like UUID, but it yields a much **smaller** code size.

We illustrate these **two** as representative approaches, but there are many variants in between, depending on how much entropy you want and whether you embed timestamps or station IDs, etc.

---

## Why High Entropy? Decentralized Generation

A key principle here is that **ID generation should be possible in a completely decentralized manner**:

- We **don’t** want a central authority to dole out sequential numbers.  
- Anyone, anywhere in the world, can generate an ID **on their own**.  
- This requires **high entropy** to ensure **collisions** (where two generators pick the same ID) are so improbable that, in realistic scenarios, it effectively never happens.

**UUID v4** is a classic example: it’s purely random, giving a **massive** 128-bit space. Collisions are so unlikely that no realistic volume of generation will create duplicates. In the **rare** event of a collision, typically “nothing immediately breaks”—it just means two items share the same ID (which is improbable enough to ignore). But for a **decentralized protocol**, ensuring uniqueness is key, so **big random spaces** are crucial.

---

## Comparison Table

| **Aspect**         | **UUID v4**                                             | **Compact ID**                                                  |
|--------------------|---------------------------------------------------------|------------------------------------------------------------------|
| **Bit Length**     | 128 bits (16 bytes)                                     | ~38 bits (5 bytes, 2 bits unused)                                |
| **Typical String** | 36 chars (hyphenated hex)                               | 10 chars hex (if you convert the 5 bytes to hex)                 |
| **Collision Risk** | Astronomically low (far below 1 in 10^18 for many uses) | Extremely low for millions/day (24-bit random is 1 in 16.7M/day) |
| **Time Element**   | None (pure random)                                      | 14-bit “day offset” for chronological component                  |
| **Ease of Use**    | Universally recognized format (libraries everywhere)    | Small snippet of code, custom but straightforward                 |
| **Barcode Size**   | Larger (128 bits → more modules in 2D code)             | Smaller (38 bits → fewer modules)                                |
| **Recognition**    | Standard in many ecosystems, widely known              | Custom format; simpler scanning once documented                  |

### Which to Choose?

- If you need a **universally recognized** format, and **storage or code size** isn’t a big concern, **UUID v4** is easiest (tons of libraries, no custom logic).  
- If you must fit into **tiny 2D codes** (e.g., marking physical items with minimal space), the **compact ID** drastically reduces overhead.  
- Both preserve the principle of **decentralized** ID generation with minimal collision risk.

For more details on each approach, see:  
- [**Compact ID Guide**](./id-generation-guide-compact.md)  
- [**UUID v4 Guide**](./id-generation-guide-uuid.md)

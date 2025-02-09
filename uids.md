# ID Generation Guide (No Station ID)

This guide explains how to generate a **compact, collision-resistant ID** for high-volume item marking (e.g., diamonds), **without** including a “station ID.” Instead, we use:

1. **Day Offset (14 bits)**: Days since a reference date (e.g., `2025-01-01`).  
2. **Random (24 bits)**: Ensures uniqueness among items produced on the same day.  

**Total**: ~38 bits stored in 5 bytes (2 unused bits). This is significantly smaller than a 128-bit UUID, making it ideal for small-format markings.

---

## 1. Field Definitions

### 1.1. Day Offset (14 bits)

- **Definition**: `dayOffset = floor((currentDate - referenceDate) / 1 day)`  
- Must fit in **14 bits** → `0..16383` range (~44.9 years).  
- Example: If reference date is `2025-01-01`, then on `2025-01-10` → `dayOffset = 9`.

### 1.2. Random (24 bits)

- A 24-bit random value → `0..16,777,215`.  
- This ensures that items produced on the **same day** will likely remain unique.  
- If **no collisions** are tolerable in a single day, you can use a strict **counter** instead of random.

---

## 2. Collision Probability

With **24 random bits** per day, the chance of a collision among \( N \) items in one day is roughly governed by the **birthday paradox**:

\[
  \text{Probability} \approx \frac{N (N - 1)}{2 \times 2^{24}}
\]

- If \( N = 1{,}000{,}000 \) (one million items in a single day), the collision probability is still quite low.  
- Over multiple days, collisions remain unlikely for typical volumes.  
- For extremely high volumes, consider a larger random field or use a daily **counter** that never wraps around.

---

## 3. Bit Layout

We have **38 bits** in total:

[ dayOffset (14 bits) | random (24 bits) ]


This fits into a **5-byte** (40-bit) array, with 2 bits unused (typically at the high end).

---

## 4. Step-by-Step Generation

1. **Compute `dayOffset`**  
   - `dayOffset = floor((now - referenceDate) / MS_PER_DAY)`  
   - Validate it’s within `0..16383`.  

2. **Generate `randomVal`**  
   - 24-bit random → range `0..16,777,215`.  
   - e.g. `Math.floor(Math.random() * 0x1000000)` in JavaScript/TypeScript.

3. **Combine**  
   - Shift `dayOffset` left by 24 bits to occupy the top 14 bits (of a 38-bit field).  
   - Bitwise-OR the 24-bit `randomVal` into the lower bits.

4. **Pack** into 5 bytes  
   - You’ll have 2 unused bits at the top of byte 0 or 4, depending on your approach.  
   - Typically, you do big-endian, meaning the most significant bits go in the first byte.

---

## 5. Example (TypeScript)

```ts
/**
 * Generate a 5-byte ID with bit layout:
 *   dayOffset (14 bits) | random (24 bits)
 */
function generateCompactId(): Uint8Array {
  const referenceDate = new Date("2025-01-01T00:00:00Z");
  const now = new Date();
  const msPerDay = 24 * 60 * 60 * 1000;

  // 1) Compute day offset (0..16383)
  const dayOffset = Math.floor(
    (now.getTime() - referenceDate.getTime()) / msPerDay
  );
  if (dayOffset < 0 || dayOffset > 16383) {
    throw new Error("Day offset out of range (0..16383).");
  }

  // 2) Generate a 24-bit random (0..16777215)
  const randomVal = Math.floor(Math.random() * 0x1000000);

  // 3) Combine into a 38-bit BigInt
  //    - Shift dayOffset (14 bits) left by 24 bits
  //    - Then OR with randomVal (24 bits)
  let idBig = BigInt(dayOffset) << 24n;
  idBig |= BigInt(randomVal) & 0xffffffn;

  // 4) Convert to a 5-byte (40-bit) array in big-endian
  const bytes = new Uint8Array(5);
  for (let i = 0; i < 5; i++) {
    bytes[i] = Number((idBig >> BigInt((4 - i) * 8)) & 0xffn);
  }

  return bytes;
}

// Example usage:
const idBytes = generateCompactId();
console.log("Raw bytes:", idBytes);
// e.g., Uint8Array [ 0, 99, 185, 13, 224 ]

// Convert to hex
const hexString = Array.from(idBytes)
  .map((b) => b.toString(16).padStart(2, "0"))
  .join("");
console.log("Hex:", hexString); 
// e.g., "0063b90de0"

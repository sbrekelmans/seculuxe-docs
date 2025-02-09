# ID Generation Guide (Using UUID v4)

This guide explains how to generate an identifier using the well-known **UUID v4** (random-based) scheme. While it **does not** minimize data size as aggressively as our [compact day+random ID](../id-generation-guide.md), it is an industry-standard approach that offers:

- **Universally recognized** format (36-character hex string by default).  
- **Purely random** 128-bit space (UUID v4).  
- Widely supported in libraries and tooling.  

---

## 1. Overview of UUID v4

A **UUID (Universally Unique Identifier)** version 4 uses a 128-bit random number. It is typically represented in a **36-character** textual form:

xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx

- **Total**: 128 bits (16 bytes).  
- **Typical Text Representation**: 32 hexadecimal digits plus 4 hyphens = 36 characters.

### Pros
- Very common in many systems and languages.  
- Established libraries exist to generate and parse them.  
- Easily recognizable format.

### Cons
- Larger data size (128 bits vs ~38 bits in a compact ID).  
- Larger 2D codes if you embed the **full 36-character** string directly.

---

## 2. How to Generate a UUID v4

Many programming environments provide a built-in library or a widely used package to generate UUIDs. In **Node.js / TypeScript**, the common approach is using the [`uuid`](https://www.npmjs.com/package/uuid) library.

### 2.1. Installing (Node/TypeScript)

```bash
npm install uuid
```

(or)

```
yarn add uuid
```

### 2.2. Example in TypeScript

```ts
import { v4 as uuidv4 } from "uuid";

function generateUuidV4(): string {
  return uuidv4(); // e.g. "b3d8e2fe-c038-478e-9a7f-27dd6f6377b4"
}

// Usage:
const myUuid = generateUuidV4();
console.log("Generated UUID v4:", myUuid);
```
By default, uuidv4() returns the canonical 36-character string with hyphens.

## 3. Storing UUIDs: Text vs. Binary
1. Textual (36-char)
- Easier to read and type.
- Typically used in databases as a string column (e.g., CHAR(36)).

2. Binary (16-byte)
- More space-efficient storage and smaller barcodes if you convert directly to raw bytes.
- If your scanning and decoding pipeline supports binary data, you can skip the overhead of hex.

Converting to or from text:

- The uuid library gives you a 36-char string by default.
- If you want the raw 16 bytes, you can parse the string and store the bytes, or vice versa.

Example for raw bytes in TypeScript:
```ts
import { parse, stringify } from "uuid";

const uuidText = uuidv4(); // "b3d8e2fe-c038-478e-9a7f-27dd6f6377b4"
const uuidBytes = parse(uuidText); // Uint8Array of length 16
console.log(uuidBytes); // e.g. [179,216,226,254,192,56,71,142,154,127,39,221,111,99,119,180]

// Convert back
const textAgain = stringify(uuidBytes);
console.log(textAgain); // "b3d8e2fe-c038-478e-9a7f-27dd6f6377b4"
```
## 4. Considerations for Barcodes / 2D Codes
1. Full Text: If you store the 36-char UUID in a barcode (QR, Data Matrix, DotCode), it significantly increases the number of modules or dots.
- The code might be larger physically if printed at the same module size.
2. Raw 16 Bytes: If your pipeline can handle binary encoding, you can embed the 16 raw bytes in the code. This reduces the data size by more than half compared to a 36-character ASCII string.
3. Scanning: Many standard QR code scanners assume text. If you embed raw bytes, scanners might display “garbled” characters unless your workflow expects binary data.

## 5. Collision Probability
- A UUID v4 is 128 bits of random data (~ 3.4 × 10 38 3.4×10 38 total space).
- Collisions are astronomically unlikely unless you generate enormous volumes (far beyond typical millions per year).
- Overkill for small-item marking but extremely safe from a uniqueness standpoint.
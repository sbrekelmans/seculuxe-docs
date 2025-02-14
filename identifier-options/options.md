# Options

wxWhen marking items (e.g., diamonds) that require **unique identifiers**, there are many ways to generate IDs along a **spectrum** of complexity and size. Below, we highlight **two examples** at different points on that spectrum:

1. **Full UUID (v4)**
   * **128 bits** of random data, typically serialized as a 36-character string (e.g., `123e4567-e89b-12d3-a456-426614174000`).
   * Very well-known standard, supported by abundant libraries.
   * **Downside**: Larger data footprint → bigger codes if embedding in a barcode or 2D symbol.
2. **Compact “Day + Random”**
   * \~**38 bits** (14 bits for “days since reference date,” + 24 bits random).
   * Stored in **5 bytes**, often shown as a **10-character hex** string.
   * **Downside**: Not a universal format like UUID, but it yields a much **smaller** code size.

We illustrate these **two** as representative approaches, but there are many variants in between, depending on how much entropy you want and whether you embed timestamps or station IDs, etc.

***

## Why High Entropy? Decentralized Generation

A key principle here is that **ID generation should be possible in a completely decentralized manner**:

* We **don’t** want a central authority to dole out sequential numbers.
* Anyone, anywhere in the world, can generate an ID **on their own**.
* This requires **high entropy** to ensure **collisions** (where two generators pick the same ID) are so improbable that, in realistic scenarios, it effectively never happens.

**UUID v4** is a classic example: it’s purely random, giving a **massive** 128-bit space. Collisions are so unlikely that no realistic volume of generation will create duplicates. In the **rare** event of a collision, typically “nothing immediately breaks”—it just means two items share the same ID (which is improbable enough to ignore). But for a **decentralized protocol**, ensuring uniqueness is key, so **big random spaces** are crucial.

***

## Comparison Table - hex

| Aspect                 | UUID v4                                                                           | Compact ID                                                       |
| ---------------------- | --------------------------------------------------------------------------------- | ---------------------------------------------------------------- |
| **Bit Length**         | 128 bits (16 bytes)                                                               | \~38 bits (5 bytes, 2 bits unused)                               |
| **Typical String**     | 36 chars (hyphenated hex)                                                         | 10 chars hex (if you convert the 5 bytes to hex)                 |
| **Collision Risk**     | Astronomically low (far below 1 in 10^18 for many uses)                           | Extremely low for millions/day (24-bit random is 1 in 16.7M/day) |
| **Time Element**       | None (pure random)                                                                | 14-bit “day offset” for chronological component                  |
| **Ease of Use**        | Universally recognized format (libraries everywhere)                              | Small snippet of code, custom but straightforward                |
| **Barcode Size**       | Larger (128 bits → more modules in 2D code)                                       | Smaller (38 bits → fewer modules)                                |
| **Recognition**        | Standard in many ecosystems, widely known                                         | Custom format; simpler scanning once documented                  |
| **ID Example**         | 8fc60e7c-3b3c-48e9-a6a7-a5fe4f1fbc31                                              | abcd1234                                                         |
| **Datamatrix Example** | ![](../.gitbook/assets/datamatrix-ba9ec44be71946a29430d37b2692b7d0.png)wxh: 22x22 | ![](../.gitbook/assets/datamatrix-abcde123.png)wxh: 14x14        |

## Comparison Table - Base62

<table><thead><tr><th width="233">Aspect</th><th>UUID v4</th><th>Custom Base62</th><th>Smaller Custom base62</th></tr></thead><tbody><tr><td><strong>Bit Length</strong></td><td>128 bits (16 bytes)</td><td>approximately 107 bits</td><td>approximately 65 bits</td></tr><tr><td><strong>Typical String</strong></td><td>22 chars</td><td>18 Chars</td><td>11 Chars</td></tr><tr><td><strong>Collision Risk</strong></td><td>Astronomically low (far below 1 in 10^18 for many uses)</td><td>Extremely low if time element is added</td><td>Pretty low</td></tr><tr><td><strong>Time Element</strong></td><td>None (pure random)</td><td>None (should add though)</td><td>Needs a time elemetn</td></tr><tr><td><strong>Ease of Use</strong></td><td>Universally recognized format (libraries everywhere)</td><td>Small snippet of code, custom but straightforward</td><td></td></tr><tr><td><strong>Barcode Size</strong></td><td>Larger (128 bits → more modules in 2D code) </td><td>Smaller (107 bits → fewer modules)</td><td></td></tr><tr><td><strong>Recognition</strong></td><td>Standard in many ecosystems, widely known</td><td>Custom format; simpler scanning once documented</td><td></td></tr><tr><td><strong>ID Example</strong></td><td>2fNwVYePN8WqqDFvVf7XMN</td><td>2fNwVYePN8WqqDFvVf</td><td>2fNwVYePN8W</td></tr><tr><td><strong>Datamatrix Example</strong></td><td><img src="../.gitbook/assets/image (1).png" alt="" data-size="original">wxh: 20x20</td><td><img src="../.gitbook/assets/image (1) (1).png" alt="" data-size="original">wxh: 18x18</td><td><img src="../.gitbook/assets/image.png" alt="" data-size="original">wxh: 16x16</td></tr></tbody></table>

| String size | Datamatrix size |
| ----------- | --------------- |
| 22-43       | 22x22           |
| 19-21       | 20x20           |
| 12-18       | 18x18           |
| 9-11        | 16x16           |
| 6-8         | 14x14           |
| 4-5         | 12x12           |
| < 4         | 10x10           |



### Which to Choose?

* If you need a **universally recognized** format, and **storage or code size** isn’t a big concern, **UUID v4** is easiest (tons of libraries, no custom logic).
* If you must fit into **tiny 2D codes** (e.g., marking physical items with minimal space), the **compact ID** drastically reduces overhead.
* Both preserve the principle of **decentralized** ID generation with minimal collision risk.
* The optimal choice allows maximum entropy within a code that can be printed without repositioning.&#x20;

For more details on each approach, see:

* [**Compact ID Guide**](uids.md)
* [**UUID v4 Guide**](uuids.md)

Excellent question — this doubt is **very important**, and I’m glad you caught it.
Let’s clear it **slowly, visually, and correctly**.

---

## 1️⃣ First: what does **“packing”** mean here?

### ❓ What is packing?

**Packing** means:

> Taking **multiple smaller words (32-bit)** and **combining them into one larger word (128-bit)** in a **specific order**.

In your case:

```
4 × 32-bit plaintext words  →  1 × 128-bit AES block
```

---

## 2️⃣ How plaintext arrives from PS (very important)

From your **C code**:

```c
for (int i = 0; i < 4; i++) {
    REG_AES_DATA = plaintext[i];
}
```

This means:

| AXI write # | wdata value  |
| ----------- | ------------ |
| 1           | plaintext[0] |
| 2           | plaintext[1] |
| 3           | plaintext[2] |
| 4           | plaintext[3] |

📌 **wdata is NOT the same each time**
It changes on every AXI write.

---

## 3️⃣ Why it *looked* like the same `wdata` was written everywhere

You noticed this code 👇

```verilog
plaintext_block <= {plaintext_block[95:0], wdata};
```

This **does NOT** mean:

> “same data written into all 128 bits”

Instead it means:

> **Shift left by 32 bits and insert new 32-bit word**

---

## 4️⃣ Let’s break the packing logic step-by-step

### Initial state:

```
plaintext_block = 0
```

---

### 🟢 Write #1 (wdata = W0)

```verilog
plaintext_block <= {plaintext_block[95:0], wdata};
```

Result:

```
[127:96] = 0
[95:64]  = 0
[63:32]  = 0
[31:0]   = W0
```

---

### 🟢 Write #2 (wdata = W1)

```
plaintext_block[95:0] = {0, 0, W0}
```

After shift + insert:

```
[127:96] = 0
[95:64]  = 0
[63:32]  = W0
[31:0]   = W1
```

---

### 🟢 Write #3 (wdata = W2)

```
[127:96] = 0
[95:64]  = W0
[63:32]  = W1
[31:0]   = W2
```

---

### 🟢 Write #4 (wdata = W3)

```
[127:96] = W0
[95:64]  = W1
[63:32]  = W2
[31:0]   = W3
```

🎯 **Final packed block =**

```
{W0, W1, W2, W3}
```

---

## 5️⃣ Visual summary (best way to remember)

```
Write order from PS:
W0 → W1 → W2 → W3

Packing logic:
plaintext_block = { old[95:0], new_word }

Final result:
plaintext_block = 128'h W0_W1_W2_W3
```

---

## 6️⃣ Why packing is done this way (engineering reason)

✔ AXI-Lite is **32-bit**
✔ AES core expects **128-bit**
✔ Packing avoids FIFOs at first stage
✔ Matches **NIST test vector order**

---

## 7️⃣ Alternate (more explicit) packing style (if you prefer clarity)

If the shift method feels confusing, here’s a **clearer version**:

```verilog
case (word_count)
    2'd0: plaintext_block[127:96] <= wdata;
    2'd1: plaintext_block[95:64]  <= wdata;
    2'd2: plaintext_block[63:32]  <= wdata;
    2'd3: plaintext_block[31:0]   <= wdata;
endcase
```

This is **logically identical**, just more readable.

---

## 8️⃣ Final takeaway (lock this in your mind)

> **Packing ≠ duplicating data**
> **Packing = shifting old data + inserting new data**

The **same signal (`wdata`)** is used because:
• AXI sends **one word at a time**
• The **register position changes**, not the data

---

If you want, next I can:

* Animate this packing with waveforms
* Compare big-endian vs little-endian
* Show how AES core consumes this block

Just tell me 👍

Great, this is the **right time** to ask this question 👍
I’ll give you a **clean, practical checklist** and also explain **what runs on your laptop vs what needs hardware**.

I’ll structure this as:

1️⃣ Minimum required setup (recommended)
2️⃣ Software list (what each tool is used for)
3️⃣ Hardware list (primary + alternatives)
4️⃣ What you can do **fully on your laptop (no board)**
5️⃣ Upgrade path (if you want to go deeper later)

No fluff.

---

## 1️⃣ MINIMUM RECOMMENDED SETUP (BEST BALANCE)

### ✔ Goal

* Low latency AES-GCM datapath
* AXI-Lite control + AXI-Stream data
* Testable in simulation **before hardware**

### ✔ Recommended combo

* **Vivado + Zynq-7000 board**
* **ModelSim/Questa (or Vivado Simulator)**
* **Bare-metal C (no Linux initially)**

---

## 2️⃣ SOFTWARE REQUIREMENTS (WITH PURPOSE)

### 🔹 1. Xilinx Vivado (MANDATORY)

📌 **What it does**

* Write Verilog/VHDL
* Build block design (PS + AXI)
* Generate bitstream
* Simulate RTL

📌 **Use for**

* AXI-Lite slave
* AXI-Stream FIFO
* AES core
* Zynq PS configuration

📌 **Laptop?**
✅ YES

📌 **Alternative**

* None (for Zynq)

---

### 🔹 2. Simulator (Choose ONE)

#### Option A: Vivado Simulator (FREE)

* Integrated
* Slower but sufficient

#### Option B: ModelSim / Questa (Better)

* Faster waveform debugging
* Industry-standard

📌 **Laptop?**
✅ YES

---

### 🔹 3. Vitis / Xilinx SDK (MANDATORY for C)

📌 **What it does**

* Write PS-side C code
* Access DDR
* Write AXI-Lite registers

📌 **Modes**

* Bare-metal (recommended first)
* Linux userspace (advanced)

📌 **Laptop?**
✅ YES

---

### 🔹 4. C Compiler (for learning / testing)

📌 **Purpose**

* Understand memory access
* Practice pointer-based MMIO

📌 **Options**

* GCC (Linux / Windows WSL)
* Online compilers (logic only)

📌 **Limitation**
❌ Cannot access real AXI hardware
✔ Only for conceptual understanding

---

### 🔹 5. GTKWave (Optional but Useful)

📌 **Purpose**

* View VCD waveform files
* Lightweight alternative to ModelSim

📌 **Laptop?**
✅ YES

---

## 3️⃣ HARDWARE REQUIREMENTS (BOARDS)

### 🔹 PRIMARY RECOMMENDED BOARD

### ✅ Zynq-7000 (Best for Learning + Industry)

Examples:

* **ZedBoard**
* **Zybo Z7-10 / Z7-20**
* **PYNQ-Z2**
* **Arty Z7**

📌 Why?

* PS + PL tightly coupled
* AXI already supported
* DDR connected to PS

📌 Needed for:

* Real DDR access
* Real AXI transactions
* Latency measurement

---

### 🔹 ALTERNATIVE ZYNQ BOARDS

| Board      | Cost   | Notes              |
| ---------- | ------ | ------------------ |
| Zybo Z7-10 | Low    | Student friendly   |
| PYNQ-Z2    | Low    | Python support too |
| ZedBoard   | Medium | Academic standard  |
| Ultra96    | Higher | Zynq Ultrascale+   |

---

### 🔹 NO-ZYNQ ALTERNATIVE (PL ONLY)

If PS is not required:

#### FPGA-only boards

* Artix-7
* Spartan-7

📌 Limitation:

* No ARM
* No DDR access from CPU
* AXI-Lite must be driven by testbench

❌ Not ideal for your project goals

---

## 4️⃣ WHAT YOU CAN DO **ONLY ON LAPTOP** (NO BOARD)

This is important 👇

### ✅ You can fully do these without hardware:

✔ Write AXI-Lite slave RTL
✔ Write AXI-Stream FIFO
✔ Pack 32-bit → 128-bit logic
✔ AES core RTL
✔ ModelSim testbench
✔ Validate NIST test vectors
✔ Verify block_valid timing
✔ Measure cycle latency (simulation)

📌 **80% of your project is doable on laptop**

---

### ❌ What you CANNOT do without board

❌ Real DDR access
❌ PS booting
❌ Actual AXI interconnect delays
❌ Throughput benchmarking on silicon

---

## 5️⃣ ALTERNATIVE: NO HARDWARE AT ALL (SIMULATION-ONLY FLOW)

If you **don’t have a board now**, do this:

```
C behavior → ModelSim TB
AXI-Lite TB → Regfile
AXI-Stream → FIFO
AES core → Output check
```

📌 You **replace PS with testbench**
📌 Professors usually accept this for academic projects

---

## 6️⃣ OPTIONAL ADVANCED TOOLS (LATER)

| Tool                | Why                   |
| ------------------- | --------------------- |
| Verilator           | Fast simulation       |
| SystemVerilog UVM   | AXI verification      |
| QEMU                | PS emulation          |
| Linux on Zynq       | Driver-based access   |
| Xilinx AXIS FIFO IP | Production-grade FIFO |

---

## 7️⃣ FINAL SUMMARY (PRINT THIS)

### Minimum Software

✔ Vivado
✔ Vitis
✔ ModelSim / Vivado Sim

### Minimum Hardware

✔ Any Zynq-7000 board (or none initially)

### Laptop Only?

✔ Yes (design + verify)
❌ No (real DDR + PS)

---

If you want, next I can:

* Suggest **exact Zybo / PYNQ board** based on budget
* Give **installation order**
* Provide **folder structure for project**
* Give **final block diagram used in thesis**

Just tell me 👍

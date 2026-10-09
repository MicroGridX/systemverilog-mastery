<div align="center">

# ⚡ Intro to SystemVerilog

**Design it. Verify it. Ship it.**

`HDL` · `OOP` · `Constrained-Random` · `Layered TB` · `Coverage` · `UVM-ready`

</div>

---

## 🧠 1. What is SystemVerilog?

**SystemVerilog (SV)** = **HDL + HVL** in one language (IEEE 1800).
It extends Verilog with **OOP, randomization, assertions, and coverage**.

| 🔧 SV for Simulation (Design) | 🧪 SV for Verification |
|---|---|
| Describes **RTL hardware** | Builds **testbenches** that test the RTL |
| `logic`, `always_comb`, `always_ff`, `interface` | `class`, `rand`, `constraint`, `mailbox`, `covergroup` |
| Synthesizable | **Non-synthesizable** (runs only in simulator) |
| Output: hardware (DUT) | Output: confidence + bugs found 🐞 |

> 💡 **One-liner:** *Verilog designs the chip, SystemVerilog **proves** it works.*

---

## ❓ 2. Why SystemVerilog if Verilog Already Exists?

Verilog is great for **design**, weak for **verification**. SV fixes that.

| Feature | Verilog | SystemVerilog |
|---|---|---|
| Paradigm | Procedural | **OOP** (class, inheritance, polymorphism) |
| Reusability | Low (copy-paste) | **High** (reusable classes, packages) |
| Stimulus | Manual / directed | **Constrained-Random (CRV)** |
| Data types | `reg`, `wire` | `logic`, `bit`, `enum`, `struct`, queues, dynamic arrays |
| Communication | Hard-wired ports | **Interface**, `modport`, `mailbox`, `event` |
| Checking | `$display` + eyeballing | **Assertions (SVA)**, scoreboard |
| Coverage | ❌ | **Functional coverage** (`covergroup`) |
| Methodology | None | **UVM** foundation |

---

## 🎯 3. Directed (Static) vs Constrained-Random Testbench

| | 🧱 Directed / Static TB | 🎲 Constrained-Random TB |
|---|---|---|
| Stimulus | Hand-written, fixed | **Randomized within rules** |
| Effort | Linear: 1 test = 1 scenario | Write once, run **thousands** of scenarios |
| Corner cases | Only what you thought of | **Finds unexpected bugs** |
| Reusability | Low | **High** |
| Closure metric | Test count | **Functional coverage %** |
| Best for | Small blocks, sanity tests | **Industry standard** for real designs |

```systemverilog
// Directed                    // Constrained-Random
a = 0; b = 1; #10;             class transaction;
a = 1; b = 1; #10;               rand bit a, b;
                                 constraint c { a dist {0:=1, 1:=3}; }
                               endclass
```

> 💡 **Flow:** Random stimulus → Check (scoreboard) → Measure coverage → Add constraints → Repeat until **100% coverage**.

---

## 🏗️ 4. Testbench Architecture (Layered TB)

<div align="center">
<img src="images/tb_architecture.png" alt="SystemVerilog Testbench Architecture" width="420"/>
</div>

**Key idea:** Each component has **one job**, so components are **reusable** and **independent** of the DUT.

### 🧩 Components, Job & What They Contain

| Component | 🎯 Job | 📦 Should Contain |
|---|---|---|
| **Transaction** | Data packet (the "unit" of stimulus) | `rand` fields, `constraint`s, `display()` |
| **Generator** | Creates **randomized** transactions | `randomize()`, loop of N items, `mailbox` put → driver |
| **Driver** | Converts transaction → **pin-level signals** | `virtual interface`, `mailbox` get, drive logic |
| **Monitor** | **Passively** samples DUT signals → transaction | `virtual interface`, sampling, `mailbox` put → scoreboard |
| **Agent** | Bundles Generator + Driver + Monitor | Instances of the three, their mailboxes |
| **Scoreboard** | **Checks correctness** (DUT output vs expected) | Reference model, compare logic, PASS/FAIL counters |
| **Environment** | Builds & connects all components | Agent, Scoreboard, mailboxes, `run()` |
| **Test** | Top-level control; picks the scenario | Environment, test config (count, constraints) |
| **Interface** | Signal bundle between TB and DUT | Signals, clock, `modport`/`clocking block` |
| **DUT** | Design under test | RTL only |

> ⚠️ **Interview gold:** Driver **drives**, Monitor **never drives**. Scoreboard checks, it doesn't generate. `virtual interface` is how **classes** (dynamic) reach **module-world** (static) signals.

---

## ➕ 5. Example: Full Adder with Layered TB

### Just Take an Overview of How TB is written according to Prescribed Architecture.

**DUT:** `sum = a ^ b ^ c`, `carry = a & b | b & c | c & a`

### 🗺️ Mapping to Architecture

| Component | In Half Adder TB |
|---|---|
| Transaction | `a`, `b`,`cin` (rand), `sum`, `carry` |
| Generator | Produces random `(a, b, cin)` pairs |
| Driver | Drives `a`, `b`,`cin` onto the interface |
| Monitor | Samples `a`, `b`,`cin`, `sum`, `carry` |
| Scoreboard | Checks `sum == a ^ b ^ c` and `carry = a & b | b & c | c & a` |

### 💻 Code (`thanks to Aham-Akash`)
https://github.com/Aham-Akash/Full-Adder-in-System-Verilog

<div align="center">

**⭐ Star the repo if it helps your prep. Happy verifying! ⚡**

</div>

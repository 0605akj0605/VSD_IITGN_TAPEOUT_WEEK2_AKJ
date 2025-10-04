
---

# 🧠 Fundamentals of System-on-Chip (SoC) Design

---

## 🔹 What is a System-on-Chip (SoC)?

A **System-on-Chip (SoC)** integrates all essential components of a complete computing system onto a **single silicon chip**.
It typically combines:

* 🧮 **Processor (CPU)** – Executes instructions
* 🧠 **Memory** – Stores data and code
* ⚙️ **Peripherals** – Handles I/O and system operations
* 🔗 **Interconnect Fabric** – Manages communication between all components

---

### ✳️ Why SoCs?

By integrating these subsystems, an SoC achieves:

* ⚡ **Higher performance** – Faster data transfer between components
* 🔋 **Lower power consumption** – Less off-chip communication
* 📏 **Reduced size and cost** – Fewer chips and external components
* 🧱 **Improved reliability** – Fewer inter-chip interfaces

Modern SoCs power:

* 📱 Smartphones
* 🚗 Automotive controllers
* 🔌 Embedded systems
* 🧩 Edge AI devices

They blend **hardware and software co-design** into one tightly coupled platform.

---

## 🔹 Components of a Typical SoC

| Component                          | Description                                                                         |
| ---------------------------------- | ----------------------------------------------------------------------------------- |
| **CPU / Processor Core**           | Executes instructions, manages computation and control flow.                        |
| **Memory Subsystem**               | Includes on-chip SRAM, ROM/Flash, cache; may also connect to off-chip DRAM.         |
| **Peripherals / IP Blocks**        | Handle I/O and system functions (UART, SPI, I²C, GPIO, timers, DMA).                |
| **Interconnect / Bus / NoC**       | Provides data communication between CPU, memory, and peripherals (e.g., AMBA, AXI). |
| **Clock, Reset, Power Management** | Synchronizes timing and controls power across subsystems.                           |

💡 *Each block can be treated as a reusable **IP core**, enabling modular design and easy integration.*

---

## 🔹 Why BabySoC?

**BabySoC** is a **simplified learning model** for understanding SoC design concepts.

While industrial SoCs are **large and complex**, BabySoC focuses on a **minimal yet complete system** that demonstrates:

* 🧩 CPU–Memory–Peripheral communication
* 🔁 Basic bus operations and interrupts
* 📜 Memory mapping and register interfacing

**Key Features:**
*RVMYTH – A simple RISC-V-based CPU core for instruction execution.
*PLL (Phase-Locked Loop) – 8× clock multiplier providing a stable clock for the CPU.
*DAC (10-bit Digital-to-Analog Converter) – Interfaces with analog devices and outputs analog signals.

**Primary Purpose:**
*To integrate these IPs in a single SoC.
*To calibrate and test the analog part of the SoC.

![VSDBabySoC Architecture](image/babysoc_architecture.png)
---

### 🎯 Why Use BabySoC?

BabySoC helps learners **grasp fundamental SoC concepts** such as:

* Data flow between modules
* Functional partitioning
* Integration of components

It avoids the overwhelming complexity of industrial SoCs like power domains, advanced verification, or timing closure — making it an **ideal entry point** for beginners.

---

## 🔹 Role of Functional Modelling

Before designing at the hardware (RTL) level, SoC designers begin with **functional modelling**, representing the **system’s behavior** at a higher abstraction level (in C/C++ or SystemC).

### ✅ Purpose and Advantages

* 🧩 **Validate architecture early** – Ensure correct functionality and data flow
* ⚖️ **Explore design trade-offs** – Evaluate different architectures before committing to RTL
* 💻 **Enable early software development** – Firmware can be built even before hardware exists
* 🔍 **Reduce verification time and cost** – Catch logical errors before RTL simulation

Functional models act as **golden references** for later stages such as **RTL verification, synthesis, and physical design**.

---

## 🔹 BabySoC in the Learning Journey

Learning SoC design through **BabySoC** mirrors the **real-world SoC development flow**:

1. 🧠 **Functional Modelling** – Understand overall behavior at a high level
2. 🧾 **RTL Design** – Implement CPU, memory, and bus logic in Verilog/VHDL
3. 🧪 **Integration & Verification** – Simulate modules and compare with functional model
4. ⚙️ *(Optional)* **Physical Design** – Perform synthesis, place-and-route, and timing analysis

---

### 🚀 The Learning Outcome

This step-by-step journey — from **concept to silicon** — helps learners build a strong foundation in:

* SoC architecture
* RTL and functional design
* Verification flow
* Hardware–software co-design

---

> 🗣️ **In summary:**
> A System-on-Chip is not just a processor — it’s a **complete mini-computer on a chip**, and **BabySoC** is the perfect sandbox to understand how every component inside that chip works together.

---
# Project:- Functional Simulation of BabySoC and verification of its workings
🏗️ Project Structure
VSDBabySoC/
├── src/
│   ├── include/       # Header files (*.vh)
│   ├── module/        # Verilog + TLV modules
│   │   ├── vsdbabysoc.v   # Top-level module
│   │   ├── rvmyth.v       # CPU
│   │   ├── avsdpll.v      # PLL
│   │   ├── avsddac.v      # DAC
│   │   └── testbench.v    # Testbench
└── output/            # Simulation outputs

🛠️ Setup
📥 Clone the Project
cd ~/VLSI
git clone https://github.com/manili/VSDBabySoC.git
cd VSDBabySoC/


You’ll see:

src/ – Verilog/TLV modules

images/ – Visuals and diagrams

output/ – Simulation results

🔧 TLV → Verilog Conversion

Since RVMYTH is written in TL-Verilog (.tlv), we convert it to standard Verilog before simulation.

# Install dependencies
sudo apt update
sudo apt install python3-venv python3-pip

# Create virtual environment
python3 -m venv sp_env
source sp_env/bin/activate

# Install SandPiper-SaaS
pip install pyyaml click sandpiper-saas

# Convert TLV → Verilog
sandpiper-saas -i ./src/module/*.tlv -o rvmyth.v --bestsv --noline -p verilog --outdir ./src/module/


✅ Output: rvmyth.v alongside other Verilog files.

🧪 Simulation Flow
🔹 Pre-Synthesis Simulation
mkdir -p output/pre_synth_sim

iverilog -o output/pre_synth_sim/pre_synth_sim.out \
  -DPRE_SYNTH_SIM \
  -I src/include -I src/module \
  src/module/testbench.v

cd output/pre_synth_sim
./pre_synth_sim.out

🔹 View in GTKWave
gtkwave output/pre_synth_sim/pre_synth_sim.vcd

🔍 Signals to Observe

⏱️ CLK → Input clock from PLL

🔄 reset → Reset signal

🎚 OUT → DAC output (digital representation in simulation)

🔢 RV_TO_DAC[9:0] → 10-bit CPU output fed to DAC

🧠 Instruction Program Driving BabySoC
#	Instruction	Action
0	ADDI r9, r0, 1	r9 = 1 (decrement step)
1	ADDI r10, r0, 43	r10 = 43 (loop limit)
2	ADDI r11, r0, 0	r11 = 0 (counter)
3	ADDI r17, r0, 0	r17 = 0 (DAC input)
4	ADD r17, r17, r11	Accumulate into r17
5	ADDI r11, r11, 1	Increment counter
6	BNE r11, r10, -4	Repeat loop until r11=43
7	ADD r17, r17, r11	Accumulate r17
8	SUB r17, r17, r11	Adjust r17
9	SUB r11, r11, r9	Decrement counter
10	BNE r11, r9, -4	Repeat loop until r11=1
11	SUB r17, r17, r11	Final adjust
12	BEQ r0, r0, ...	Infinite loop
🔄 Execution Timeline
Phase	Registers	r17 Value	Behavior
Ramp (Loop1)	r11 = 0→42	r17 = Σ0..42 = 903	Monotonic increase
Peak	r11 = 43	r17 = 946	Transient maximum
Oscillation (Loop2)	r11 = 43→1	r17 = 903 ± r11	Oscillating decay
Final	r11 = 1	r17 adjusted	Holds steady

Data Flow: Instruction Memory → CPU Pipeline → Register r17 → DAC → Analog OUT

⚖️ DAC Conversion Example

Scaling formula:

𝑉
OUT
=
𝑟
17
1023
×
𝑉
REFSPAN
V
OUT
	​

=
1023
r17
	​

×V
REFSPAN
	​


With VREFSPAN = 1.0 V:

r17 Value	DAC Output Voltage
903	0.882 V
946 (peak)	0.925 V

💡 Tip: In GTKWave, switch OUT to Analog Step format for proper DAC visualization.

🛠️ Troubleshooting

⚠️ Module Redefinition Error: Ensure each Verilog/TLV file is included only once during compilation.

Verify signal names and file paths are correct when including headers or modules.

📸 Architecture Diagram Placeholder:

![VSDBabySoC Architecture](path/to/babysoc_architecture.png)


✅ Deliverables for Lab

Simulation logs (pre_synth_sim.out)

GTKWave waveform screenshots

Short explanations for each waveform (reset, clock, DAC output)

If you want, I can merge this with your Part 1 README into one polished, professional GitHub README that includes:

SoC fundamentals

BabySoC intro

VSDBabySoC details

Project structure & simulation steps

# ✅ Model Answers for Chapter 7 – FPGA Architecture and Resources

---

### 1. **Identify FPGA Resources (e.g., Artix-7 XC7A100T)**

- **CLBs (Configurable Logic Blocks):** Contain 8 LUTs and 16 FFs per slice; used for combinational and sequential logic.
- **BRAM:** 135 blocks of 36Kb each; supports true dual-port operation.
- **DSP48E1:** 240 DSP slices for arithmetic (multiply-accumulate) operations.
- **I/O Blocks:** 250 user I/O pins supporting multiple voltage standards.

Use Vivado or `xc7a100t-1csG324` datasheet for specific part info.

---

### 2. **4-bit Multiplier and Resource Report**

```verilog
module multiplier_4bit (
    input [3:0] a, b,
    output [7:0] product
);
    assign product = a * b;
endmodule
```

**Synthesis Result (Example – Vivado):**
- LUTs: ~30
- FFs: ~16
- DSPs: 0 (uses logic by default for small bit widths)

**Optimization Tip:**  
Force use of DSPs with larger widths or DSP directives/pragmas.

---

### 3. **Dual-Port RAM Module (Synthesizable)**

```verilog
module dual_port_ram (
    input clk,
    input [7:0] data_a, data_b,
    input [4:0] addr_a, addr_b,
    input we_a, we_b,
    output reg [7:0] q_a, q_b
);
    reg [7:0] mem [0:31];

    always @(posedge clk) begin
        if (we_a) mem[addr_a] <= data_a;
        q_a <= mem[addr_a];
        if (we_b) mem[addr_b] <= data_b;
        q_b <= mem[addr_b];
    end
endmodule
```

**Inference Result:**
- BRAM inferred (1 block)
- Lower LUT usage than distributed RAM

---

### 4. **8-bit MAC with DSP Usage**

```verilog
module mac_unit (
    input clk,
    input [7:0] a, b,
    input [15:0] acc_in,
    output reg [15:0] acc_out
);
    always @(posedge clk)
        acc_out <= acc_in + (a * b);
endmodule
```

**Synthesis Note:**  
Ensure tool uses DSP slices. Check reports (`Utilization Report`) → `DSP48E1 used: 1`.

---

### 5. **Clock Divider using MMCM (Vivado)**

Use Clocking Wizard IP in Vivado:

- Input Clock: 100 MHz
- Output: 25 MHz

**XDC Constraints Example:**
```xdc
create_clock -period 10.0 -name clk_in -waveform {0 5} [get_ports clk_in]
```

Derived frequencies visible in Clock Summary Report.

---

### 6. **BRAM vs Distributed RAM for FIFO**

**Comparison Summary:**

| Resource     | BRAM FIFO       | LUT FIFO        |
|--------------|------------------|------------------|
| Storage Size | 1K+ entries       | ~256 entries     |
| Performance  | Higher throughput | Lower (for large)|
| Area         | Uses BRAM block   | Consumes LUTs    |

BRAM preferred for large depth; LUT-based is fine for small buffers.

---

### 7. **I/O Block Constraints (XDC)**

```xdc
set_property PACKAGE_PIN A9 [get_ports data_in]
set_property IOSTANDARD LVCMOS33 [get_ports data_in]
set_property DRIVE 12 [get_ports data_in]
set_property SLEW FAST [get_ports data_in]
```

Apply for each input/output pin in the design.

---

### 8. **FPGA Floorplanning (Vivado)**

Steps:
1. Open synthesized design.
2. Use "Pblock" to create a region.
3. Assign instance via `set_property LOC` or GUI.
4. Re-implement and observe placement/routing.

**Impact:**  
Can improve performance for critical modules or isolate noisy logic.

---

> 📁 Save this as `README_Chapter7_Model_Answers.md` for lab use or documentation.

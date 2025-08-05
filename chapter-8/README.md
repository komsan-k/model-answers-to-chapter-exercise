# Model Answers for Chapter 8

---

### 1. **Complete FPGA Flow: 4-bit Counter Example**

```verilog
module counter_4bit (
    input clk, reset,
    output reg [3:0] count
);
    always @(posedge clk or posedge reset) begin
        if (reset)
            count <= 4'b0000;
        else
            count <= count + 1;
    end
endmodule
```

**Steps:**
1. Simulate using Vivado Simulator or ModelSim.
2. Synthesize using Vivado/Quartus.
3. Implement and generate bitstream (`.bit` or `.sof`).
4. Program FPGA board (e.g., Nexys A7, DE10-Lite).

---

### 2. **Toolchain Comparison: Vivado vs. Quartus**

| Feature         | Vivado (Xilinx)             | Quartus Prime (Intel)        |
|-----------------|-----------------------------|------------------------------|
| UI Experience   | Modern, Tcl scripting       | Integrated but dated         |
| Simulator       | Vivado Simulator            | ModelSim/Questa              |
| Synthesis       | Fast with resource reports  | Integrated synthesis         |
| Device Support  | Artix/Kintex/Zynq           | Cyclone/Arria/Stratix        |
| IP Integration  | IP Integrator               | Platform Designer            |

---

### 3. **Basic XDC Timing Constraint (100 MHz)**

```xdc
create_clock -name sys_clk -period 10.000 [get_ports clk]
set_input_delay -clock sys_clk 2.5 [get_ports input_signal]
set_output_delay -clock sys_clk 2.5 [get_ports output_signal]
```

---

### 4. **Analyze Timing Reports**

**Worst Negative Slack (WNS):** Indicates how much timing is violated.

**Fixes:**
- Add pipeline registers
- Reduce combinational logic depth
- Adjust placement via floorplanning
- Relax timing constraints if possible

---

### 5. **IP Core Integration Example (UART)**

Steps:
1. Add UART IP core via IP catalog.
2. Configure baud rate, data bits, etc.
3. Connect to `top.v` module.
4. Update constraints for TX/RX I/O pins:
```xdc
set_property PACKAGE_PIN A9 [get_ports uart_tx]
set_property IOSTANDARD LVCMOS33 [get_ports uart_tx]
```

---

### 6. **2-to-1 MUX Simulation Workflow**

```verilog
module mux2to1 (
    input a, b,
    input sel,
    output y
);
    assign y = sel ? b : a;
endmodule
```

**Testbench:**
```verilog
module tb_mux2to1;
    reg a, b, sel;
    wire y;
    mux2to1 uut (.a(a), .b(b), .sel(sel), .y(y));

    initial begin
        a = 0; b = 1; sel = 0; #10;
        sel = 1; #10;
        $finish;
    end
endmodule
```

Use waveform viewer to confirm transitions on `y`.

---

### 7. **Bitstream Programming**

1. Connect FPGA board via USB.
2. Open Hardware Manager (Vivado) or Programmer (Quartus).
3. Load `.bit` (Vivado) or `.sof` (Quartus) file.
4. Click “Program Device”.
5. Verify with switches, buttons, and LEDs.

---

### 8. **Understanding FPGA Project Files**

| File         | Description                                           |
|--------------|-------------------------------------------------------|
| `.v`         | Verilog source file                                  |
| `.xdc`       | Xilinx Design Constraint file                        |
| `.bit`       | FPGA bitstream (used to program the device)          |
| `.sof`       | Quartus bitstream equivalent                         |
| `.sdf`       | Standard Delay Format for timing simulation          |
| `.rpt`       | Synthesis/implementation report (utilization, timing)|

---


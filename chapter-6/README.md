# Model Answers for Chapter 6

---

### 1. **Code Optimization for Synthesis**

**Original (non-optimal):**
```verilog
always @(posedge clk) begin
    a = b;
    c = a + d;
    if (sel)
        y = c;
end
```

**Optimized (synthesizable, non-blocking):**
```verilog
always @(posedge clk) begin
    a <= b;
    c <= a + d;
    if (sel)
        y <= c;
    else
        y <= y; // or default assignment outside conditional
end
```

Use non-blocking assignments for sequential logic and avoid inferred latches by covering all conditional branches.

---

### 2. **Timing Violation Detection**

**Detection:**
- Use Vivado or Quartus timing analysis reports.
- Look for negative slack on setup/hold constraints.

**Fixes:**
- Add pipeline registers to break long paths.
- Balance path delays using retiming.
- Reduce logic depth or increase clock period.

---

### 3. **Pipelined 8-bit Adder**

```verilog
module pipelined_adder (
    input clk,
    input [7:0] a, b,
    output reg [8:0] sum_out
);
    reg [7:0] a_reg1, b_reg1;
    reg [8:0] sum_stage;

    always @(posedge clk) begin
        a_reg1 <= a;
        b_reg1 <= b;
        sum_stage <= a_reg1 + b_reg1;
        sum_out <= sum_stage;
    end
endmodule
```

**Latency:** 2 clock cycles  
**Throughput:** 1 addition per clock after initial delay

---

### 4. **Use of Constraints (XDC)**

```xdc
create_clock -name clk -period 10.000 [get_ports clk]
set_input_delay -clock clk 2.0 [get_ports din]
set_output_delay -clock clk 2.0 [get_ports dout]
```

These constraints define clock period and timing margins for I/O.

---

### 5. **Critical Path Identification**

- Synthesize in Vivado → Open "Timing Report"
- Look for "Worst Negative Slack (WNS)"
- Check the logic path: longest combinational delay

**Fix Suggestions:**
- Insert pipeline stages.
- Optimize combinational logic (e.g., restructure adders/muxes).
- Use faster primitives or reduce fanout.

---

### 6. **Compare Clocking Strategies**

**Synchronous Clocking:**
```verilog
always @(posedge clk)
    q <= d;
```

**Gated Clocking (not recommended):**
```verilog
wire gated_clk = clk & enable;
always @(posedge gated_clk)
    q <= d;
```

**Comparison:**
- Gated clocks reduce power but may introduce glitches.
- Synchronous clocking is safer for synthesis and CDC handling.

---

### 7. **Avoiding Inferred Latches**

**Latch Inferred (bad):**
```verilog
always @(*) begin
    if (sel == 1)
        y = a;
    // no else clause → latch inferred
end
```

**Fixed (fully specified):**
```verilog
always @(*) begin
    if (sel == 1)
        y = a;
    else
        y = b;
end
```

Always ensure all paths assign values to prevent unintended storage.

---

### 8. **Clock Domain Crossing (CDC) – Safe Synchronizer**

```verilog
module cdc_synchronizer (
    input clk_dst,
    input async_in,
    output reg sync_out
);
    reg sync_stage1;

    always @(posedge clk_dst) begin
        sync_stage1 <= async_in;
        sync_out <= sync_stage1;
    end
endmodule
```

**Metastability Handling:**
- Two-stage synchronizer minimizes risk.
- Simulate in tools supporting metastability injection or observe behavior under CDC stress.

---


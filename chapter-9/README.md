# ✅ Model Answers for Chapter 9 – System Design and Integration

---

### 1. **Modular 4-bit ALU**

```verilog
module alu_4bit (
    input [3:0] a, b,
    input [1:0] op,
    output reg [3:0] result
);
    always @(*) begin
        case (op)
            2'b00: result = a & b;
            2'b01: result = a | b;
            2'b10: result = a + b;
            2'b11: result = a - b;
        endcase
    end
endmodule
```

---

### 2. **IP Core Integration (Vivado/Quartus)**

Example: Instantiate UART or Multiplier using IP Catalog.

Steps:
- Open IP Catalog
- Configure IP (e.g., Multiplier: `input_a`, `input_b`, `result`)
- Generate output products
- Instantiate in `top.v`:
```verilog
my_multiplier u_mult (
    .a(in_a),
    .b(in_b),
    .result(out_p)
);
```
- Simulate and verify output.

---

### 3. **Bus Interface Example**

```verilog
module bus_slave (
    input clk, rst,
    input [3:0] addr,
    input [7:0] wdata,
    input wr_en, rd_en,
    output reg [7:0] rdata
);
    reg [7:0] mem [0:15];

    always @(posedge clk) begin
        if (wr_en)
            mem[addr] <= wdata;
        if (rd_en)
            rdata <= mem[addr];
    end
endmodule
```

Simulate with a master module driving address/data lines.

---

### 4. **Clock Domain Crossing with Synchronizer**

```verilog
module cdc_sync (
    input clk_dst,
    input async_signal,
    output reg sync_out
);
    reg sync_1;

    always @(posedge clk_dst) begin
        sync_1 <= async_signal;
        sync_out <= sync_1;
    end
endmodule
```

Use dual flip-flop synchronizer to avoid metastability when crossing clock domains.

---

### 5. **Custom IP Packaging**

Steps (Vivado):
1. Write and verify Verilog module (e.g., PWM).
2. Go to `Tools → Create and Package New IP`.
3. Set ports and parameters in GUI.
4. Add documentation (port names, widths, default values).
5. Add to IP Catalog for reuse in other designs.

---

### 6. **System Integration Example**

```verilog
module top_system (
    input clk, rst,
    input [3:0] data_in,
    output [3:0] data_out
);
    wire [3:0] alu_result;
    wire alu_ready;

    controller ctrl (
        .clk(clk), .rst(rst),
        .ready(alu_ready)
    );

    datapath dp (
        .a(data_in), .b(4'h1), .op(2'b10),
        .result(alu_result)
    );

    memory_if mem (
        .clk(clk),
        .data_in(alu_result),
        .data_out(data_out)
    );
endmodule
```

Use a testbench to apply sequences and verify each submodule response.

---

### 7. **AXI/Avalon Bus Summary (Optional)**

- **AXI (AMBA):** Advanced eXtensible Interface by ARM
  - Separate Read/Write channels
  - Burst transfers, out-of-order support
  - Handshake: `VALID/READY`

- **Avalon:** Altera/Intel bus used in Quartus
  - Supports Memory-Mapped, Streaming, and Interrupt interfaces
  - Simpler than AXI but less scalable

**Used For:** Communication between processor cores and IP blocks within SoCs.

---

> 📁 Save this as `README_Chapter9_Model_Answers.md` for full-system Verilog design integration exercises.

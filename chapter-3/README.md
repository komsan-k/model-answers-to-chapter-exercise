Model Answers to Chapter 3 Exercise

---

### 1. **4-bit Comparator**

```verilog
module comparator_4bit (
    input  [3:0] A, B,
    output A_gt_B,
    output A_eq_B,
    output A_lt_B
);
    assign A_gt_B = (A > B);
    assign A_eq_B = (A == B);
    assign A_lt_B = (A < B);
endmodule
```

---

### 2. **2-to-4 Decoder with Enable**

```verilog
module decoder_2to4 (
    input [1:0] in,
    input en,
    output reg [3:0] out
);
    always @(*) begin
        if (en) begin
            case (in)
                2'b00: out = 4'b0001;
                2'b01: out = 4'b0010;
                2'b10: out = 4'b0100;
                2'b11: out = 4'b1000;
            endcase
        end else
            out = 4'b0000;
    end
endmodule
```

---

### 3. **8-bit Adder/Subtractor**

```verilog
module adder_subtractor_8bit (
    input  [7:0] a, b,
    input        ctrl,       // 0: add, 1: subtract
    output [7:0] result,
    output       carry_out
);
    assign {carry_out, result} = ctrl ? (a - b) : (a + b);
endmodule
```

---

### 4. **Multiplexer Testbench**

```verilog
module tb_mux4to1;
    reg [3:0] in;
    reg [1:0] sel;
    wire out;

    mux4to1 uut (
        .a(in[0]), .b(in[1]), .c(in[2]), .d(in[3]),
        .sel(sel), .y(out)
    );

    initial begin
        in = 4'b1010;
        $display("sel | out");
        for (sel = 0; sel < 4; sel = sel + 1) begin
            #10 $display("%b   | %b", sel, out);
        end
        $finish;
    end
endmodule
```

---

### 5. **Behavioral Full-Adder**

```verilog
module full_adder_behavioral (
    input a, b, cin,
    output reg sum, cout
);
    always @(*) begin
        case ({a, b, cin})
            3'b000: {cout, sum} = 2'b00;
            3'b001: {cout, sum} = 2'b01;
            3'b010: {cout, sum} = 2'b01;
            3'b011: {cout, sum} = 2'b10;
            3'b100: {cout, sum} = 2'b01;
            3'b101: {cout, sum} = 2'b10;
            3'b110: {cout, sum} = 2'b10;
            3'b111: {cout, sum} = 2'b11;
        endcase
    end
endmodule
```

---

### 6. **Logic Simplification (Bonus)**

**Given truth table (example):**

| A | B | C | Y |
|---|---|---|---|
| 0 | 0 | 0 | 0 |
| 0 | 0 | 1 | 1 |
| 0 | 1 | 0 | 1 |
| 0 | 1 | 1 | 0 |
| 1 | 0 | 0 | 1 |
| 1 | 0 | 1 | 0 |
| 1 | 1 | 0 | 0 |
| 1 | 1 | 1 | 1 |

**K-map Simplification Result (example):**

Y = A'B + A'C + AB'C' + ABC

**Verilog Implementation:**

```verilog
module logic_simplified (
    input A, B, C,
    output Y
);
    assign Y = (~A & B) | (~A & C) | (A & ~B & ~C) | (A & B & C);
endmodule
```

---

### 7. **Gate Count Estimation (Challenge)**

**4-bit Ripple-Carry Adder:**
- Each full-adder consists of:
  - 2 XOR gates (sum)
  - 2 AND gates and 1 OR gate (carry logic)

**Total for 4 bits:**
- 8 XOR gates
- 8 AND gates
- 4 OR gates

**Gate Count Estimate:**
- Approx. 20 logic gates (excluding wiring/fanout buffers)

**Delay Analysis:**
- Ripple carry delay is linear: T_total = 4 × T_FA
- Carry has to ripple through each stage → slow for large bit widths

**Trade-offs:**
- ✅ Simple design, minimal area
- ❌ Slower at higher widths due to carry propagation

# Model Answers for Chapter 2

### 1. **Dataflow Adder**
A 4-bit binary adder using dataflow modeling:

```verilog
module dataflow_adder (
    input  [3:0] a,
    input  [3:0] b,
    output [4:0] sum
);
    assign sum = a + b;
endmodule
```

---

### 2. **Behavioral Counter**
A 3-bit up-counter with synchronous reset using behavioral modeling:

```verilog
module up_counter (
    input clk,
    input reset,
    output reg [2:0] count
);
    always @(posedge clk) begin
        if (reset)
            count <= 3'b000;
        else
            count <= count + 1;
    end
endmodule
```

---

### 3. **Multiplexer (Gate-Level)**
A 4-to-1 multiplexer using gate-level primitives:

```verilog
module mux4to1 (
    input a, b, c, d,
    input [1:0] sel,
    output y
);
    wire s0n, s1n;
    wire y0, y1, y2, y3;

    not (s0n, sel[0]);
    not (s1n, sel[1]);

    and (y0, a, s1n, s0n);
    and (y1, b, s1n, sel[0]);
    and (y2, c, sel[1], s0n);
    and (y3, d, sel[1], sel[0]);

    or  (y, y0, y1, y2, y3);
endmodule
```

---

### 4. **Testbench Practice**
Testbench for a 2-input AND gate:

```verilog
module tb_and_gate;
    reg a, b;
    wire y;

    and uut (y, a, b);

    initial begin
        $display("A B | Y");
        a = 0; b = 0; #10 $display("%b %b | %b", a, b, y);
        a = 0; b = 1; #10 $display("%b %b | %b", a, b, y);
        a = 1; b = 0; #10 $display("%b %b | %b", a, b, y);
        a = 1; b = 1; #10 $display("%b %b | %b", a, b, y);
        $finish;
    end
endmodule
```

---

### 5. **Blocking vs Non-blocking**
Demonstration with difference in assignments:

```verilog
// Blocking example
always @(posedge clk) begin
    a = b;
    c = a;  // c gets old 'a', not new 'b'
end

// Non-blocking example
always @(posedge clk) begin
    a <= b;
    c <= a;  // c gets value of 'a' from previous cycle
end
```

**Explanation:**  
Blocking (`=`) executes sequentially. In the first example, `c` receives the old value of `a` because the update hasn’t taken effect.  
Non-blocking (`<=`) schedules updates for the next simulation cycle, making it suitable for modeling flip-flops correctly.

---

### 6. **FSM Sketching (Bonus)**
**Traffic Light FSM:**

- **States:**
  - `S0`: Green
  - `S1`: Yellow
  - `S2`: Red

- **Transitions:**
  - `S0` → `S1` → `S2` → `S0`

**Verilog Encoding Tips:**
- Use `typedef enum logic [1:0]` or `parameter` for states.
- Use a `case` statement inside `always @(posedge clk)`.

---

### 7. **Code Debugging (Challenge)**

**Faulty Code with Combinational Loop:**
```verilog
always @(*) begin
    a = b;
    b = a; // Loop: a depends on b and b depends on a
end
```

**Fixed Version Using Temporary Variable:**
```verilog
always @(*) begin
    temp = b;
    a = temp;
    // b is no longer updated, breaking the loop
end
```

**OR (Separate combinational blocks):**
```verilog
always @(*) a = b;
always @(*) b = c; // Avoid direct circular dependency
```

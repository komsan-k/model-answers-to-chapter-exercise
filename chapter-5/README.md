# Model Answers for Chapter 5

---

### 1. **Moore FSM: Sequence Detector for `1101`**

```verilog
module moore_1101_detector (
    input clk, reset, in,
    output reg detected
);
    reg [4:0] state;
    parameter S0 = 5'b00001, S1 = 5'b00010, S2 = 5'b00100, S3 = 5'b01000, S4 = 5'b10000;

    always @(posedge clk or posedge reset) begin
        if (reset)
            state <= S0;
        else begin
            case (state)
                S0: state <= in ? S1 : S0;
                S1: state <= in ? S2 : S0;
                S2: state <= in ? S2 : S3;
                S3: state <= in ? S4 : S0;
                S4: state <= in ? S1 : S0;
            endcase
        end
    end

    always @(*) begin
        detected = (state == S4);
    end
endmodule
```

---

### 2. **Mealy FSM: Sequence Detector for `1101`**

```verilog
module mealy_1101_detector (
    input clk, reset, in,
    output reg detected
);
    reg [1:0] state;
    parameter S0 = 2'b00, S1 = 2'b01, S2 = 2'b10, S3 = 2'b11;

    always @(posedge clk or posedge reset) begin
        if (reset)
            state <= S0;
        else begin
            case (state)
                S0: state <= in ? S1 : S0;
                S1: state <= in ? S2 : S0;
                S2: state <= in ? S2 : S3;
                S3: state <= in ? S1 : S0;
            endcase
        end
    end

    always @(*) begin
        case (state)
            S3: detected = (in == 1);
            default: detected = 0;
        endcase
    end
endmodule
```

---

### 3. **State Transition Table + FSM Implementation**

| State | Input=0 | Input=1 |
|-------|---------|---------|
| A     | A       | B       |
| B     | C       | D       |
| C     | A       | B       |
| D     | D       | A       |

```verilog
module fsm_example (
    input clk, reset, in,
    output reg [1:0] state
);
    parameter A=2'b00, B=2'b01, C=2'b10, D=2'b11;

    always @(posedge clk or posedge reset) begin
        if (reset)
            state <= A;
        else begin
            case (state)
                A: state <= (in ? B : A);
                B: state <= (in ? D : C);
                C: state <= (in ? B : A);
                D: state <= (in ? A : D);
            endcase
        end
    end
endmodule
```

---

### 4. **FSM Testbench Example**

```verilog
module tb_fsm_example;
    reg clk = 0, reset, in;
    wire [1:0] state;

    fsm_example uut (.clk(clk), .reset(reset), .in(in), .state(state));

    always #5 clk = ~clk;

    initial begin
        reset = 1; in = 0;
        #10 reset = 0;
        #10 in = 1;
        #10 in = 0;
        #10 in = 1;
        #10 in = 1;
        #10 $finish;
    end
endmodule
```

---

### 5. **3-Floor Elevator Controller FSM (Simplified)**

States: `IDLE`, `MOVING_UP`, `MOVING_DOWN`, `DOOR_OPEN`  
Inputs: request[2:0], current_floor  
Outputs: door, direction

Design logic depends on state transitions and floor comparisons. Verilog omitted for brevity; implement using `case` with 4 states and a floor counter.

---

### 6. **Synchronous vs Asynchronous Reset Example**

```verilog
// Asynchronous Reset
always @(posedge clk or posedge reset)
    if (reset) state <= 0;
    else       state <= next_state;

// Synchronous Reset
always @(posedge clk)
    if (reset) state <= 0;
    else       state <= next_state;
```

**Comparison:**  
Asynchronous reset responds immediately to reset, while synchronous reset waits for the next clock edge.

---

### 7. **FSM Optimization**

Given redundant states in a diagram:
- Merge equivalent states with same output and transitions.
- Use state minimization algorithms.
- Rewrite FSM using fewer states in `case` or `if-else`.

Verilog structure remains the same but with reduced number of cases/states.

---

### 8. **Binary to Gray Code FSM**

```verilog
module binary_to_gray_fsm (
    input clk, reset,
    input [2:0] binary_in,
    output reg [2:0] gray_out
);
    always @(posedge clk or posedge reset) begin
        if (reset)
            gray_out <= 3'b000;
        else
            gray_out <= binary_in ^ (binary_in >> 1);
    end
endmodule
```

---


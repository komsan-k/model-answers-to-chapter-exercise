# Model Answers for Chapter 4

---

### 1. **D Flip-Flop with Asynchronous Reset**

```verilog
module d_flip_flop (
    input clk,
    input reset,
    input d,
    output reg q
);
    always @(posedge clk or posedge reset) begin
        if (reset)
            q <= 1'b0;
        else
            q <= d;
    end
endmodule
```

---

### 2. **3-bit Synchronous Counter with Enable and Reset**

```verilog
module sync_counter (
    input clk,
    input reset,
    input enable,
    output reg [2:0] count
);
    always @(posedge clk) begin
        if (reset)
            count <= 3'b000;
        else if (enable)
            count <= count + 1;
    end
endmodule
```

---

### 3. **4-bit SIPO Shift Register**

```verilog
module sipo_shift_register (
    input clk,
    input reset,
    input din,
    output reg [3:0] dout
);
    always @(posedge clk or posedge reset) begin
        if (reset)
            dout <= 4'b0000;
        else
            dout <= {dout[2:0], din};
    end
endmodule
```

---

### 4. **Moore FSM: Sequence Detector for 1011 (One-Hot Encoding)**

```verilog
module seq_detector (
    input clk, reset,
    input in,
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
                S1: state <= in ? S1 : S2;
                S2: state <= in ? S3 : S0;
                S3: state <= in ? S4 : S2;
                S4: state <= in ? S1 : S2;
            endcase
        end
    end

    always @(*) begin
        detected = (state == S4);
    end
endmodule
```

---

### 5. **Traffic Light Controller (FSM + Counter)**

```verilog
module traffic_light (
    input clk, reset,
    output reg [1:0] lights // 2-bit: 00=Red, 01=Green, 10=Yellow
);
    reg [3:0] timer;
    reg [1:0] state;

    parameter RED = 2'b00, GREEN = 2'b01, YELLOW = 2'b10;

    always @(posedge clk or posedge reset) begin
        if (reset) begin
            state <= RED;
            timer <= 0;
        end else begin
            timer <= timer + 1;
            case (state)
                RED:    if (timer == 4) begin state <= GREEN; timer <= 0; end
                GREEN:  if (timer == 3) begin state <= YELLOW; timer <= 0; end
                YELLOW: if (timer == 2) begin state <= RED; timer <= 0; end
            endcase
        end
    end

    always @(*) begin
        lights = state;
    end
endmodule
```

---

### 6. **Blocking vs Non-Blocking Assignments**

```verilog
// Blocking example
always @(posedge clk) begin
    a = b;
    c = a;  // c gets old 'a'
end

// Non-blocking example
always @(posedge clk) begin
    a <= b;
    c <= a;  // c gets old 'a' from previous clock
end
```

**Explanation:**  
Blocking (`=`) executes in order, causing unintended results in sequential logic.  
Non-blocking (`<=`) schedules all updates to happen simultaneously at the end of the time step, preserving flip-flop behavior.

---

### 7. **Parameterized Up/Down Counter**

```verilog
module param_counter #(
    parameter WIDTH = 4
)(
    input clk, reset, enable, up_down,
    output reg [WIDTH-1:0] count
);
    always @(posedge clk) begin
        if (reset)
            count <= 0;
        else if (enable)
            count <= up_down ? count + 1 : count - 1;
    end
endmodule
```

---

### 8. **Clock Divider by 8**

```verilog
module clk_div8 (
    input clk,
    input reset,
    output reg clk_out
);
    reg [2:0] count;

    always @(posedge clk or posedge reset) begin
        if (reset) begin
            count <= 3'b000;
            clk_out <= 1'b0;
        end else begin
            count <= count + 1;
            if (count == 3'b111)
                clk_out <= ~clk_out;
        end
    end
endmodule
```

---


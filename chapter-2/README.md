# Model Answers to Chapter 2 Exercises



\### 1. \*\*Dataflow Adder\*\*

A 4-bit binary adder using dataflow modeling:



```verilog

module dataflow\_adder (

&nbsp;   input  \[3:0] a,

&nbsp;   input  \[3:0] b,

&nbsp;   output \[4:0] sum

);

&nbsp;   assign sum = a + b;

endmodule

```



---



\### 2. \*\*Behavioral Counter\*\*

A 3-bit up-counter with synchronous reset using behavioral modeling:



```verilog

module up\_counter (

&nbsp;   input clk,

&nbsp;   input reset,

&nbsp;   output reg \[2:0] count

);

&nbsp;   always @(posedge clk) begin

&nbsp;       if (reset)

&nbsp;           count <= 3'b000;

&nbsp;       else

&nbsp;           count <= count + 1;

&nbsp;   end

endmodule

```



---



\### 3. \*\*Multiplexer (Gate-Level)\*\*

A 4-to-1 multiplexer using gate-level primitives:



```verilog

module mux4to1 (

&nbsp;   input a, b, c, d,

&nbsp;   input \[1:0] sel,

&nbsp;   output y

);

&nbsp;   wire s0n, s1n;

&nbsp;   wire y0, y1, y2, y3;



&nbsp;   not (s0n, sel\[0]);

&nbsp;   not (s1n, sel\[1]);



&nbsp;   and (y0, a, s1n, s0n);

&nbsp;   and (y1, b, s1n, sel\[0]);

&nbsp;   and (y2, c, sel\[1], s0n);

&nbsp;   and (y3, d, sel\[1], sel\[0]);



&nbsp;   or  (y, y0, y1, y2, y3);

endmodule

```



---



\### 4. \*\*Testbench Practice\*\*

Testbench for a 2-input AND gate:



```verilog

module tb\_and\_gate;

&nbsp;   reg a, b;

&nbsp;   wire y;



&nbsp;   and uut (y, a, b);



&nbsp;   initial begin

&nbsp;       $display("A B | Y");

&nbsp;       a = 0; b = 0; #10 $display("%b %b | %b", a, b, y);

&nbsp;       a = 0; b = 1; #10 $display("%b %b | %b", a, b, y);

&nbsp;       a = 1; b = 0; #10 $display("%b %b | %b", a, b, y);

&nbsp;       a = 1; b = 1; #10 $display("%b %b | %b", a, b, y);

&nbsp;       $finish;

&nbsp;   end

endmodule

```



---



\### 5. \*\*Blocking vs Non-blocking\*\*

Demonstration with difference in assignments:



```verilog

// Blocking example

always @(posedge clk) begin

&nbsp;   a = b;

&nbsp;   c = a;  // c gets old 'a', not new 'b'

end



// Non-blocking example

always @(posedge clk) begin

&nbsp;   a <= b;

&nbsp;   c <= a;  // c gets value of 'a' from previous cycle

end

```



\*\*Explanation:\*\*  

Blocking (`=`) executes sequentially. In the first example, `c` receives the old value of `a` because the update hasn’t taken effect.  

Non-blocking (`<=`) schedules updates for the next simulation cycle, making it suitable for modeling flip-flops correctly.



---



\### 6. \*\*FSM Sketching (Bonus)\*\*

\*\*Traffic Light FSM:\*\*



\- \*\*States:\*\*

&nbsp; - `S0`: Green

&nbsp; - `S1`: Yellow

&nbsp; - `S2`: Red



\- \*\*Transitions:\*\*

&nbsp; - `S0` → `S1` → `S2` → `S0`



\*\*Verilog Encoding Tips:\*\*

\- Use `typedef enum logic \[1:0]` or `parameter` for states.

\- Use a `case` statement inside `always @(posedge clk)`.



---



\### 7. \*\*Code Debugging (Challenge)\*\*



\*\*Faulty Code with Combinational Loop:\*\*

```verilog

always @(\*) begin

&nbsp;   a = b;

&nbsp;   b = a; // Loop: a depends on b and b depends on a

end

```



\*\*Fixed Version Using Temporary Variable:\*\*

```verilog

always @(\*) begin

&nbsp;   temp = b;

&nbsp;   a = temp;

&nbsp;   // b is no longer updated, breaking the loop

end

```



\*\*OR (Separate combinational blocks):\*\*

```verilog

always @(\*) a = b;

always @(\*) b = c; // Avoid direct circular dependency

```





1. **Combinational logic** produces outputs based solely on current inputs (e.g., adders, multiplexers), while **sequential logic** uses memory elements and depends on both current inputs and past states (e.g., flip-flops, counters).
2. **HDLs (Hardware Description Languages)** like Verilog and VHDL describe the structure and behavior of digital circuits. They are essential for simulation, synthesis, and implementation of complex digital systems.
3. **Verilog** is C-like and less strict in typing, widely used in U.S. industries. **VHDL** is Ada-like and strongly typed, more common in academia and Europe. Verilog is generally easier for beginners; VHDL promotes design discipline.
4. An **FPGA** is a programmable logic device with reconfigurable logic blocks. It differs from:

   * **ASIC**: Fixed-function, custom-silicon, not reprogrammable.
   * **Microcontroller**: Processor-based with built-in peripherals and instruction memory.

5. Major FPGA components include:

   * **CLBs (Configurable Logic Blocks)** – Logic implementation.
   * **DSP slices** – For arithmetic and signal processing.
   * **BRAM (Block RAM)** – On-chip memory blocks.
   * **Interconnects** – Routing fabric.
   * **I/O blocks** – Interface with external signals.
   * **Clock management (MMCM/PLL)** – Timing and synchronization.

6. FPGA design flow steps:

   1. Specification
   2. HDL coding (Verilog/VHDL)
   3. Simulation
   4. Synthesis
   5. Place and Route
   6. Bitstream generation
   7. Programming the FPGA

7. Common digital design challenges:

   * Meeting timing constraints
   * Debugging logic and signal issues
   * Managing resource constraints
   * Power and thermal limitations
   * Toolchain complexity

8. Real-world FPGA applications:

   * **High-frequency trading systems**
   * **Software-defined radios (SDR)**
   * **Real-time video/image processing**

9. **HLS (High-Level Synthesis)** translates C/C++ code into HDL, making hardware design accessible to software developers and speeding up prototyping and verification.
10. Emerging trends in FPGA usage:

* AI/ML accelerator integration
* Growth in edge computing and reconfigurable SoCs
* Open-source toolchains (e.g., SymbiFlow)
* Applications in 5G, automotive, IoT, and robotics

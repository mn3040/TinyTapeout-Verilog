# Game Show Buzzer System

This project implements a simple game show buzzer system using Verilog. The system allows multiple contestants to buzz in, and it locks out additional inputs once the first contestant has buzzed in.

## Features
- **Multiple Contestant Inputs:** Supports up to 6 contestants.
- **Lockout Mechanism:** Once a contestant buzzes in, further inputs are ignored until the system is reset.
- **Clock and Reset Inputs:** Uses a clock signal for synchronization and a reset signal to clear the lockout.

## Hardware Requirements
- FPGA or suitable simulation environment that supports Verilog.
- 8 input pins: 6 for contestant buzzers, 1 for clock, and 1 for reset.
- 8 output pins: 6 to indicate which contestant buzzed in first.

## Software Requirements
- Verilog compiler or FPGA development environment (e.g., Xilinx Vivado, Intel Quartus).

## Module Overview

### Ports
- `io_in` [7:0]: Input port array.
  - `io_in[0]`: Clock signal (`clk`).
  - `io_in[1]`: Reset signal (`rst`).
  - `io_in[7:2]`: Contestant buzzer inputs (`buzzer_inputs`).
- `io_out` [7:0]: Output port array.
  - `io_out[5:0]`: Output indicating which contestant buzzed in first (`buzzer_lockout_reg`).

### Internal Signals
- `buzzer_inputs` [5:0]: Internal wire for contestant buzzer inputs.
- `clk`: Internal wire for the clock signal.
- `rst`: Internal wire for the reset signal.
- `buzzer_lockout_reg` [5:0]: Register to store the state of the buzzer lockout mechanism.

## Functionality
- **Clock and Reset:** The module uses the `clk` signal for synchronization and the `rst` signal to reset the lockout register.
- **Buzzer Inputs:** The module monitors the `buzzer_inputs` for contestant buzzers. When a contestant buzzes in, the corresponding bit in the `buzzer_lockout_reg` is set, locking out further inputs until reset.
- **Lockout Mechanism:** Once the `buzzer_lockout_reg` is set (i.e., not equal to `6'b000000`), no additional buzzer inputs are accepted until the system is reset.

## Usage
1. **Connect Inputs:**
   - Connect the `io_in` pins to the appropriate signals: 6 contestant buzzers, a clock signal, and a reset signal.
2. **Observe Outputs:**
   - The `io_out` pins will indicate which contestant buzzed in first by setting the corresponding bit in the `buzzer_lockout_reg`.

## Example

Here is an example of how to instantiate the `gameshow_buzzer` module in a Verilog testbench:

```verilog
module testbench;
  reg [7:0] io_in;
  wire [7:0] io_out;

  gameshow_buzzer uut (
    .io_in(io_in),
    .io_out(io_out)
  );

  initial begin
    // Initialize inputs
    io_in = 8'b00000000;

    // Apply reset
    io_in[1] = 1;
    #10;
    io_in[1] = 0;

    // Simulate contestant 1 buzzing in
    io_in[2] = 1;
    #10;
    io_in[2] = 0;

    // Check output
    #10;
    $display("Buzzer Lockout Register: %b", io_out[5:0]);

    // Simulate contestant 2 trying to buzz in
    io_in[3] = 1;
    #10;
    io_in[3] = 0;

    // Check output remains unchanged
    #10;
    $display("Buzzer Lockout Register: %b", io_out[5:0]);

    // Apply reset
    io_in[1] = 1;
    #10;
    io_in[1] = 0;

    // Simulate contestant 2 buzzing in
    io_in[3] = 1;
    #10;
    io_in[3] = 0;

    // Check output
    #10;
    $display("Buzzer Lockout Register: %b", io_out[5:0]);
  end
endmodule

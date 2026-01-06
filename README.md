# UART Protocol Design and Implementation

A complete Universal Asynchronous Receiver-Transmitter (UART) system implemented in VHDL for the Altera DE2 Development Board (Cyclone IV FPGA). This project integrates serial communication with a traffic light controller to enable real-time debugging via ASCII messages.

## Project Overview

This project transforms an isolated traffic light FSM controller into a debuggable real-time system by implementing a full UART communication interface. The system transmits human-readable ASCII debug messages (e.g., "Mg-Sr" for Main Green, Side Red) to an external terminal whenever a traffic light state transition occurs.

### Key Features

- **Full UART Implementation**: Complete transmitter and receiver modules with configurable baud rates
- **Asynchronous Serial Communication**: RS-232 protocol support with proper level conversion
- **Structural VHDL Design**: All components built from RTL logic without behavioral constructs
- **Real-time Debugging**: Automatic transmission of state transition messages
- **Multi-baud Support**: Selectable baud rates from 300 to 38400 bps
- **Robust Reception**: 8× oversampling technique for reliable data capture

## Technical Specifications

### UART Configuration
- **Frame Format**: 1 start bit + 8 data bits (LSB first) + 1 stop bit = 10 bits/character
- **Baud Rates**: 300, 600, 1200, 2400, 4800, 9600, 19200, 38400 bps (selectable via 3-bit SEL signal)
- **Oversampling**: 8× baud rate for receiver (BClk×8)
- **Parity**: None
- **Data Encoding**: ASCII

### Hardware Requirements
- Altera DE2 Development and Education Board
- Cyclone IV EP4CE115F29C7 FPGA
- MAX232 level-shifter IC for RS-232 conversion
- 50 MHz system clock

### Software Requirements
- Quartus II Web Edition
- Terminal emulator (PuTTY, TeraTerm, etc.)
- VHDL compiler

## Architecture

### Major Components

1. **Baud Rate Generator**: Cascaded frequency dividers producing selectable baud rates from 50 MHz system clock
2. **UART Transmitter**: 8-bit Transmit Data Register (TDR), 9-bit Transmit Shift Register (TSR), and control FSM
3. **UART Receiver**: 8-bit Receive Shift Register (RSR), 8-bit Receive Data Register (RDR), and control FSM
4. **UART FSM Controller**: Manages data flow and handshaking between traffic light controller and UART
5. **Traffic Light Controller**: Main FSM that generates state transitions
6. **State Change Detector**: Monitors state transitions and triggers debug message transmission

### Memory-Mapped Registers

- **TDR (Transmit Data Register)**: Holds data to be transmitted
- **RDR (Receive Data Register)**: Stores received data
- **SCCR (Serial Communications Control Register)**: Control bits (TIE, RIE, SEL[2:0])
- **SCSR (Serial Communications Status Register)**: Status flags (TDRE, RDRF, OE, FE)

## Design Methodology

The system was designed using the Finite State Machine (FSM) procedure with the following steps:

1. Specification interpretation and requirements analysis
2. State diagram construction for all light configurations
3. State table derivation with present/next states and outputs
4. State minimization and binary encoding assignment
5. Transition table generation and design equations
6. Structural VHDL implementation and FPGA synthesis

## Key Algorithms

### Transmitter Algorithm
1. **Idle State**: Waits for load signal, TxD line held high
2. **Load State**: Latches 8-bit data into TDR, loads TSR with start bit (0) appended
3. **Shift Loop**: Right-shifts TSR at baud rate, transmitting start bit, data bits, and stop bit
4. **Completion**: Asserts TDRE flag when ready for next byte

### Receiver Algorithm
1. **Start Bit Detection**: Waits for RxD falling edge
2. **Alignment**: Counts 4 clock cycles (half bit period) for center alignment
3. **Sampling Loop**: Samples at 8× baud rate, shifts data into RSR (9 iterations)
4. **Load and Flag**: Transfers RSR to RDR, asserts RDRF flag

### Debug Message Transmission
1. **State Monitoring**: Continuously monitors 3-bit state output from traffic controller
2. **Edge Detection**: Compares current state S(t) with previous state S(t-1)
3. **Transmission Trigger**: On state change, initiates 6-character ASCII message transmission
4. **Handshaking**: Polls TDRE flag before each byte to prevent buffer overrun

## File Structure

```
├── UART_System/
│   ├── uart_transmitter.vhd
│   ├── uart_receiver.vhd
│   ├── transmitter_controller.vhd
│   ├── receiver_control_path.vhd
│   └── transmitter_controlpath.vhd
├── Baud_Rate_Generator/
│   ├── BaudRateGenerator.vhd
│   ├── clock_div_41.vhd
│   └── clock_div_8.vhd
├── UART_FSM/
│   ├── uart_fsm_top.vhd
│   ├── uart_fsm_controller.vhd
│   ├── state_change_detector.vhd
│   └── CBR.vhd (Character Byte Register)
├── Registers/
│   ├── Register_8_Bit.vhd
│   ├── SHR_Register_9_Bit.vhd
│   ├── RSR_8bit.vhd
│   └── RDR_8bit.vhd
├── Control_Registers/
│   ├── SCCR.vhd
│   ├── SCSR.vhd
│   └── Address_Decoder.vhd
├── Traffic_Light/
│   └── traffic_light_controller.vhd
└── Top_Level/
    └── top_level.bdf
```

## Authors

**Group 17 - CEG 3155 Digital Systems II**
- Isaiah Kwapisz (300377196)
- David Rosocha (300366191)


---

*This project demonstrates modular system-on-chip design, clock domain crossing, finite state machine coordination, and real-time debugging methodologies applicable to embedded systems and FPGA-based prototyping.*

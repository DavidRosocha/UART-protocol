# UART Protocol Design and Implementation

A complete Universal Asynchronous Receiver-Transmitter (UART) system implemented in VHDL for the Altera DE2 Development Board (Cyclone IV FPGA). This project integrates serial communication with a traffic light controller to enable real-time debugging via ASCII messages.

## Project Overview

This project transforms an isolated traffic light FSM controller into a debuggable real-time system by implementing a full UART communication interface. The system transmits human-readable ASCII debug messages (e.g., "Mg-Sr" for Main Green, Side Red) to an external terminal whenever a traffic light state transition occurs.

### Key Features

- **Full UART Implementation**: Complete transmitter and receiver modules with configurable baud rates
- **Asynchronous Serial Communication**: RS-232 protocol support with proper level conversion
- **Structural VHDL Design**: All components built from RTL logic without behavioral constructs
- **Real-time Debugging**:

# SDRAM Controller

Because synchronous dynamic RAM (SDRAM) has complex timing and signalling requirements, a memory controller is necessary to avoid having to deal with the nitty-gritty details when reading or writing to memory. Its job is to hide the complexity of things like row and column addressing, precharging, and refreshing. Instead it lets us treat SDRAM just like plain old static memory.

This SDRAM controller provides a symmetric 32-bit synchronous read/write interface for a 16Mx16-bit SDRAM chip (e.g. AS4C16M16SA-6TCN, IS42S16400F, etc.).

Even though the SDRAM chip only has a 16-bit data bus, the controller uses a 32-bit data bus because it is more efficient to burst multiple words from the SDRAM than it is to do individual reads and writes.

## Documentation

### Signals :traffic_light:

| name   | direction | description                                                 |
| ---    | ---       | ---                                                         |
| reset  | input     | resets the SDRAM controller when asserted                   |
| clk    | input     | clock                                                       |
| addr   | input     | address bus                                                 |
| data   | input     | input data bus                                              |
| we     | input     | write enable                                                |
| req    | input     | requests a read or write operation when asserted            |
| ack    | output    | asserted when a request is accepted by the SDRAM controller |
| valid  | output    | asserted when there is valid data on the output data bus    |
| q      | output    | output data bus                                             |

#### Reset

The `reset` signal can be used to reset the internal state machine for the SDRAM controller.

#### Clock

The `clk` signal is the system clock used by the SDRAM controller.

Ideally, it should be different to the SDRAM clock (i.e. the clock for the actual SDRAM chip). By adjusting the phase relationship so that the SDRAM clock leads the system clock, we can ensure that the SDRAM output signals arrive in time to meet our timing constraints.

#### Address Bus

The `addr` signal should be set to the address of the memory location being accessed.

#### Input Data Bus

The `data` signal should be set to the 32-bit value to be _written_ to the memory location at the given address.

#### Write Enable

The `we` signal should be asserted when you want to write to the SDRAM. It should be asserted together with the `req` signal.

#### Request

The `req` signal should be asserted when you want to read or write to the SDRAM. When making a request, the `req`, `we`, `addr`, and `data` signals should not be changed until the request has been acknowledged.

#### Acknowledge

The `ack` signal is asserted by the SDRAM controller when a request has been acknowledged.

#### Valid

The `valid` signal is asserted by the SDRAM controller when there is valid data on the output data bus.

#### Output Data Bus

The `q` signal is set to the last 32-bit word read from the SDRAM. The data on the output bus is only valid while the `valid` signal is asserted.

### Reading

The SDRAM controller allows read operations to be performed using a simple interface.

Read requests can be chained so that a new read operation can be requested before the current operation has completed. Using this strategy, we can read any number of words from the SDRAM without wasting clock cycles, thus using the maximum available bandwidth of the SDRAM.

The following example describes how to read two 32-bit words using the SDRAM controller. This method can be used to read any number of words:

1. Write the address to the `addr` bus.
2. Request a read operation by deasserting the `we` signal and asserting the `req` signal.
3. Wait for the `ack` signal to be asserted. This means that the read request has been acknowledged, and a read operation has begun.
4. Write another address to the `addr` bus.
5. Wait for the `valid` signal to be asserted. This means that the first read request has been completed and the data is available on the data bus.
6. Read the value on the `q` data bus.
7. Wait for `ack` signal to be asserted. This means that the second read request has been acknowledged, and a read operation has begun.
8. Deassert the `req` signal when we're done making requests.
9. Wait for the `valid` signal to be asserted. This means that the second read request has been completed and the data is available on the data bus.
10. Read the value on the `q` data bus.

<p align="center">
  <img alt="SDRAM Controller Read" src="https://raw.githubusercontent.com/nullobject/sdram-ctrl-fpga/master/sdram-ctrl-read.png" height="140px" />
</p>

### Writing

The SDRAM controller handles write operations similarly to read operations.

Write requests can also be chained, so that a new write operation can be requested before the current operation has completed.

The following example describes how to write two 32-bit words using the SDRAM controller. This method can be used to write any number of words:

1. Write an address to the `addr` bus and a value to the data bus.
2. Request a write operation by asserting the `we` and `req` signals.
3. Wait for the `ack` signal to be asserted. This means that the write request has been acknowledged, and a write operation has begun.
4. Write another address to the `addr` bus and a value to the data bus.
5. Wait for `ack` signal to be asserted. This means that the second write request has been acknowledged, and a write operation has begun.
6. Deassert the `we` and `req` signals when we're done making requests.

<p align="center">
  <img alt="SDRAM Controller Write" src="https://raw.githubusercontent.com/nullobject/sdram-ctrl-fpga/master/sdram-ctrl-write.png" height="140px" />
</p>

### State Diagram

<p align="center">
  <img alt="State Diagram" src="https://raw.githubusercontent.com/nullobject/sdram-ctrl-fpga/master/state-diagram.png" height="488px" />
</p>

## Licence

This project is licensed under the MIT licence. See the LICENCE file for more details.

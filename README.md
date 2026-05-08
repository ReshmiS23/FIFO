# FIFO
# Introduction:
A FIFO is a special type of buffer. The name FIFO stands for first in first out and means that the data written into the buffer
first comes out of it first. There are other kinds of buffers like the LIFO (last in first out), often called a stack memory, and the
shared memory.

# FIFO Types
Every memory in which the data word that is written in first also comes out first when the memory is read is a first-in first-out
memory. Figure 1 illustrates the data flow in a FIFO. There are three kinds of FIFO:
 ## • Shift register
  FIFO with an invariable number of stored data words and, thus, the necessary synchronism between
the read and the write operations because a data word must be read every time one is written
 ## • Exclusive read/write FIFO 
 FIFO with a variable number of stored data words and, because of the internal structure,
the necessary synchronism between the read and the write operations
## • Concurrent read/write FIFO 
 FIFO with a variable number of stored data words and possible asynchronism
between the read and the write operation

<img width="452" height="688" alt="image" src="https://github.com/user-attachments/assets/d66f4344-7701-447d-b17f-a9ca886c798a" />

# Synchronous_FIFO 
## Test case write operation:
<img width="940" height="370" alt="image" src="https://github.com/user-attachments/assets/4d0663a9-e235-4433-9427-94a9f6569151" />

Observation:
| Time       | Operation    | Result      |
| ---------- | ------------ | ----------- |
| 0–10 ns    | Reset active | FIFO empty  |
| 10–20 ns   | Write AA     | empty=0     |
| 20–30 ns   | Read AA      | data_out=AA |
| After read | FIFO empty   | empty=1     |

## Read  operation:
<img width="940" height="431" alt="image" src="https://github.com/user-attachments/assets/bd41db81-b7af-4704-b9dd-f6fe4f0a9188" />

### Observation:
The input data AA was written into the FIFO when wr_en became HIGH. Later, when rd_en became HIGH, the FIFO read the stored data and the output became AA. After the read operation, the FIFO became empty again (empty = 1).

## Full condition:
<img width="940" height="429" alt="image" src="https://github.com/user-attachments/assets/380396e4-e2b6-497f-b7ee-505adfcea925" />

### Observation:
Different data values were continuously written into the FIFO when wr_en became HIGH repeatedly. Since no read operation occurred (rd_en = 0), the FIFO gradually filled up and finally the full signal became 1. The empty signal became 0 after the first write because the FIFO contained data.

## Empty condition:
<img width="940" height="339" alt="image" src="https://github.com/user-attachments/assets/5dee01bf-1dab-45fa-92a5-374613321b0c" />

### Observation:
Initially, no data was written into the FIFO (wr_en = 0) and no read operation occurred (rd_en = 0). Therefore, the FIFO remained empty and the empty signal stayed 1 while the full signal stayed 0. The output remained 00 because no data was stored in the FIFO.

## Single element:
<img width="940" height="450" alt="image" src="https://github.com/user-attachments/assets/f702f3a1-f85e-43d5-a2bc-99c95932f6a6" />

### Observation:

A single data value 55 was written into the FIFO when wr_en became HIGH. Later, when rd_en became HIGH, the same data 55 was read from the FIFO and appeared at data_out. After the read operation, the FIFO became empty again (empty = 1)._

## Multiple writes:
<img width="940" height="407" alt="image" src="https://github.com/user-attachments/assets/c79b5c9c-cbde-4886-bd5c-9533a9df60ab" />

### Observation:
Multiple data values (00 to 0F) were written into the FIFO as wr_en became HIGH repeatedly. Since no read operation occurred (rd_en = 0), the FIFO kept storing data and the empty signal became 0 after the first write. The full signal remained 0 because the FIFO was not completely filled yet.

## Multiple reads:
<img width="940" height="398" alt="image" src="https://github.com/user-attachments/assets/66a7daa8-c08b-4a6b-af20-bdd9a5358dd9" />

### Observation:

The waveform shows a FIFO performing multiple write and read operations.
Inputs are clk, rst_n, wr_en, rd_en, and data_in[7:0]; outputs are data_out[7:0], full, and empty. Data values (00, aa, 01–07, 55, 66) are written into the FIFO when wr_en=1, and during rd_en=1 the same values appear sequentially on data_out, proving the FIFO preserves the order of data (First-In First-Out).

## wrap around:
<img width="940" height="418" alt="image" src="https://github.com/user-attachments/assets/c747e272-6a0a-48c1-9b78-cbb666ebaf59" />


### Observation:
In the waveform, the FIFO successfully writes a data sequence from 00 to 0f (hex), filling its total depth of 16 slots. After the address wraps around, the data_in returns to 77, but the internal count and full flags show a red X, indicating a logic error or an uninitialized state in the counter. Consequently, while the data bus behaves as expected, the control signals are failing to track the "full" condition during the wrap-around.

## Simultaneous read write:
<img width="940" height="423" alt="image" src="https://github.com/user-attachments/assets/ee13f4fc-ccff-4bf3-8a91-c0e35f667df8" />


### Observation:
Inputs: At the 10ns mark, wr_en (write enable) and rd_en (read enable) are both set to 1 (high), while data_in transitions from an unknown state to the value 99.
 
 Resultant Output: The data_out remains at 00, and the empty flag drops to 0, indicating the FIFO is no longer empty; however, the count remains at X.

Observation: Because both read and write are active, the FIFO should ideally maintain its current level, but the X on the count bus suggests the pointer logic is failing to calculate the net change of zero during concurrent pulses.

## Reset :
<img width="940" height="394" alt="image" src="https://github.com/user-attachments/assets/523e4daf-5ab9-463b-98b1-bc14416c1573" />

### Observation:

In this waveform, the active-low reset rst_n is high (inactive), but the FIFO remains in an uninitialized state with the count signal showing a red X. Even though a write operation is attempted at 10ns with data_in at ff, the internal logic fails to resolve, indicating that the FIFO was likely never properly cleared by a low pulse on the reset line.

## Overflow:
<img width="940" height="526" alt="image" src="https://github.com/user-attachments/assets/93ac4078-bd01-47d5-8647-2d8692d131a1" />

### observation:
In this overflow test case, the wr_en remains high while a continuous stream of data (from 24 to aa) is pushed into the FIFO, eventually exceeding its defined depth of 16. The empty flag correctly stays low, but the full flag fails to assert high, indicating that the overflow protection or status logic is not functioning as intended.

## Underflow:
<img width="940" height="368" alt="image" src="https://github.com/user-attachments/assets/04d916f9-49b1-4a6e-816e-b9d5148d41fd" />

### Observation:
Inputs: At 10ns, the rd_en (read enable) is pulled high while the empty flag is already at 1, indicating no data is available to be read.

Resultant Output: The data_out remains at 00, and since there is no data to remove, the empty flag stays high and the count remains at X.

This confirms that the read logic is correctly ignored when the FIFO is empty, preventing the pointer from moving further into an invalid state.

## Conclusion

The synchronous FIFO was successfully designed and verified using Verilog HDL. Different test cases such as write operation, read operation, full condition, empty condition, single element transfer, multiple writes, multiple reads, wrap-around, simultaneous read/write, reset, overflow, and underflow were simulated and analyzed using waveforms.
















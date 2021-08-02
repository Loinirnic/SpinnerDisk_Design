# Spinning disk design guide
The following guide covers the design of the spinning disk remote lab's Printed Circuit Board during my internship.

## Experiment brief

The spinning disk experiment consists of a weighted disk attached to a DC motor where a voltage is applied causing the disk to move. An encoder is used to read the velocity output and provide the results to the user via an arduino and raspberry pi. A main PCB will be used to control where the inputs and outputs of the experiement go, and how they work together.

# Choosing a motor

The first step in designing the electronics for the experiment is selecting a motor.

### Our motor requirements:
- A deadband of <0.5V
- A quick linear gain response after the deadband
- Good repeatability for long life time

An ideal response can be shown from Quanser's motor, where its gain graph as shown below, demonstrates what we are looking for:

<img width="420" alt="QuanserGain_graph" src="https://user-images.githubusercontent.com/87417442/125777520-b5089581-b4e3-4b79-ad56-eedee1e55f10.PNG">

## Characteristics to look for when ordering a motor:

|Motor Parameters| Quanser motor values for reference|
|----------------|-----------------------------------|
| Voltage 12V-24V | 18V |
| Low Inertia | 4.0 × 10−6 kg-m^2 |
| Nominal speed | 3050 RPM |
| Nominal torque | 22 mNm |

One of the key points to keep in mind is the speed to torque ratio, if the torque is too low then the motor will take a long time to reach its maximum speed and its movements won't be as precise. A motor with a very high torque will usually be implemented with large weights - that we won't be dealing with. 

## Capacitors and where to place them:
The motor needs a decoupling capacitor of **100nF** to be soldered between the power terminals of its base. This decoupling capacitor reduces the noise and is the general value to be used for any motor. The will also be two **47nF** capacitors soldered from each terminal to the shell of the motor itself, acting to specifiy the frequency  range. Soldering the capacitors to the shell is the same as grounding.

## Testing the motor
When choosing a motor we must first test its velocity/ voltage responses. This consists of using a tachometer (measures velocity in RPM) and a power supply box running from 0-10V with 0.5V increments. Once the results have been obtained we must calculate the gain in rad/s and compare the results to how we wish them to appear in the graph above - Quanser's results. 

# Encoder PCB Design

The Optical encoder we are using is - AEDB-9140 Series.

The Encoder must be connected to the main board to transfer the data it collects through incremental channels, we might assume that this can be acheived attaching wires directly from the encoder pins to the main pcb however, as per the datasheet we must include pull up resistors for the different channels which have to be positioned as close to the encoder as possible. To achieve this a separate encpder bpard must be dsigned with the Resistors on it.

## Requirements for the Encoder Board:
- Five input connecting terminals for the encoders pins
- Three **2k7ohm** pull up resistors for the signals - as required from the [datasheet](http://www.farnell.com/datasheets/20523.pdf)
- Five Output connecting terminals for the wires to go to the main PCB 

The open source software used to design these boards is [KiCAD](https://www.kicad.org/)

### Encoder Final Circuit Schematic drawing
<img width="420" alt="Encoder final schematic" src="https://user-images.githubusercontent.com/87417442/125802305-4f761adf-d002-4eea-b290-1487a04a4102.PNG">

### Footprints used in design:
- Resistors R1, R2, R3 -> **Resistor_SMD:R_1206_3216Metric**
- Encoder board terminal J1 -> **Connector_PinHeader_2.00mm:PinHeader_1x05_P2.00mm_Vertical**
- Output terminal J2 -> **Connector_PinHeader_2.54mm:PinHeader_1x05_P2.54mm_Vertical**

### My Encoder PCB final Design 
<img width="420" alt="Encoder dimension" src="https://user-images.githubusercontent.com/87417442/125812643-c6282e4e-b15b-4746-a37f-4d1f3aa421b1.PNG">

Above you can see the dimensions of the board. It is very small so that it can be neatly attached directly to the encoder board using terminal J1 without getting in the way of the experiment itself. I have chosen SMD Resistors as they don't take up as much room as Through-hole ones and can be easily applied using pasting or careful soldering.

# Main PID PCB Design 
When designing the board we must include all of the following parts in order to interact with the motor and encoder board:

- **Arduino Nano** - V3 (A1) - used to communicate to the motor, encoder and raspberry pi. The arduino will take the information from the raspberry pi and control the motor using a driver. It will then receive the feedback from the encoder readings and return those to the raspberry pi.

- Two 3.3V-5V **Bi-directional level converters from arduino** (J1 & J2) - Change the voltage from 3.3V to 5V or vise versa. These consist of power terminals and 4 channels for both the low and high voltage ends of a 02x06 connector. Click [here](https://www.digchip.com/datasheets/download_datasheet.php?id=436483&part-number=BOB-12009&type=prod) to see the datasheet. One will be used for the encoder input and one for the motor driver output from the arduino nano since it only needs 3.3V to work. 

- 6 pin output terminal for motor driver (J3) - Male pin terminal from pcb to motor driver. Connected to the high voltage side of a level converter before attaching to the arduino nano.

- 5 Pin input terminal from encoder (J4) - Male pin terminal from pcb to encoder board. Connected to the high voltage of a levek converter before attaching to arduino nano

- Motor terminal supply (J5) - Screw terminal Supply for DC motor

- DC power supply Barrel Jack (J6) - Power supply port

- Alternative Screw terminal for power supply (J7) - Connect pcb directly to a DC power supply by screwing in cables instead of the barrel jack/audio jack 

- 100uF Through hole decoupling capacitor (C1) - Used reduce noise from power supply, these are recommended to being places as close to terminal as possible, preferrably made from ceramic or tentalum. Here is a link for further reading on decoupling capacitors in your own circuits - [When and how to use a Decoupling capacitor](https://blog.upverter.com/2019/11/12/when-and-how-to-use-a-decoupling-capacitor-in-your-pcb/)

- 8A Fuse for the arduino THT/SMD (F1) - Ensures that the current does not exceed 8A since that is the supply current available. The value of fuse will depend on your own current supply as well as the maximum value required by the motor.

## Final drawing schematic of the pcb

<img width="500" alt="Final_board_main_sch" src="https://user-images.githubusercontent.com/87417442/127645443-46c0f1e9-f01e-453d-9938-83d1dc093d54.PNG">

The arduino is connected directly to the level converters with the following pin specification

| Arduino Pin | Motor Diver pin | Encoder Pin |
|-------------|-----------------|-------------|
| 4 & 29 (GND) | 3 | 3 |
| 5 | - | 6 |
| 6 | - | 1 |
| 7(DIR) | 2 | - |
| 9 | 5 | - |
| 10 | 1 | - |
| 14 (INDEX) | - | 5 |
| 17 (Vcc) | 4 | 4 |


Some other details to notice are:

...The pins from the motor driver terminals have been labelled in correspondence to the pins on the actual motor driver for connections between the two boards. There are 2 pins on the motor driver pin terminal that are connected to the same pin on the voltage converter.

### The next step is to create the layout of the final board, to do this we need the footprints of the components

For all of the components apart from the level converters, the footprints are available on the library. The footprint I made for the converter can be found in the attachments and will be described later.

## Footprints

1. Arduino nano - Module:Arduino_Nano 
2. Motor Driver Pin terminal(J3) - Connector_PinHeader_2.54mm:PinHeader_1x06_P2.54mm_Vertical
3. Encoder Pin terminal(J4) - Connector_PinHeader_2.54mm:PinHeader_1x05_P2.54mm_Vertical
4. Motor power terminal(J5) and Alternative Vss(J7) - TerminalBlock:TerminalBlock_bornier-2_P5.08mm
5. 8A SMD Fuse - Fuse:Fuse_1206_3216Metric
6. Polarised 100uF Capacitor - Capacitor_THT:CP_Radial_D6.3mm_P2.50mm
7. Barrel Jack - Connector_BarrelJack:BarrelJack_Horizontal
8. Level Converters(J1, J2) - I have specially designed the footprint for the pins on these boards to be directly connected to the pcb as the chips consist of two **2.54mm** 6 pin terminals adjacently positioned with a separation of around **9.97mm**. The .mod file available for this footprint is available in the attachments folder of this repo.

You can see below the layout of my footprint:

<img width="156" alt="footprint" src="https://user-images.githubusercontent.com/87417442/127123439-84998efd-94e4-4624-8b12-5f986bf3b9b3.PNG">

Where Pin 4 represents the GND pin on the Low Voltage side of the board.
The Boards will be placed with the components facing the PCB since the tracks are much easier to layout on the pcb. The pins soldered onto the chips are male, female pins are then soldered to the main pcb so that the chips can be easily attached or removed.

## Final PCB design layout

<img width="420" alt="Dimensions of board base" src="https://user-images.githubusercontent.com/87417442/127857721-643cf321-efda-4e60-91d7-68a30bad9466.PNG">

Above is the layout I followed myself in the final design. There are holes on the board for possible mounting or attaching to an insulating layer of acrylic to eliminate the chances of shorts occuring.

# What the final PCB shoul look like

<img width="420" alt="Main_PCB" src="https://user-images.githubusercontent.com/87417442/127858483-793a2feb-620c-41f5-82b7-d1441a5a99b0.PNG">


# Spinning disk design 
Design outline for pcb and motor choices made for the spinning disk experiment 

## Experiment brief

The spinning disk experiment consists of a weighted disk attached to a DC motor where a voltage is applied causing the disk to move. An encoder is used to read the velocity output and provide the results to the user via an arduino and raspberry pi. A main PCB will be used to control where the inputs and outputs of the experiement go, and how they work together.

# Choosing a motor

The first step in designing the electronics for the experiment is selecting a motor.

### Our motor requirements:
- A deadband of <0.5V
- A quick linear gain response after the deadband
- Good repeatability for long life time

An ideal response can be shown from Quanser's motor, where its gain graph as shown below, demonstrates what we are looking for:

<img width="527" alt="QuanserGain_graph" src="https://user-images.githubusercontent.com/87417442/125777520-b5089581-b4e3-4b79-ad56-eedee1e55f10.PNG">

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
<img width="416" alt="Encoder final schematic" src="https://user-images.githubusercontent.com/87417442/125802305-4f761adf-d002-4eea-b290-1487a04a4102.PNG">

### Footprints used in design:
- Resistors R1, R2, R3 -> **Resistor_SMD:R_1206_3216Metric**
- Encoder board terminal J1 -> **Connector_PinHeader_2.00mm:PinHeader_1x05_P2.00mm_Vertical**
- Output terminal J2 -> **Connector_PinHeader_2.54mm:PinHeader_1x05_P2.54mm_Vertical**

### My Encoder PCB final Design 
<img width="416" alt="Encoder dimension" src="https://user-images.githubusercontent.com/87417442/125812643-c6282e4e-b15b-4746-a37f-4d1f3aa421b1.PNG">

Above you can see the dimensions of the board. It is very small so that it can be neatly attached directly to the encoder board using terminal J1 without getting in the way of the experiment itself. I have chosen SMD Resistors as they don't take up as much room as Through-hole ones and can be easily applied using pasting or careful soldering.

# Main PID PCB Design

The Main PCB Board will use an arduino nano to communicate to the motor, encoder and raspberry pi. The arduino will take the information from the raspberry pi and control the motor using a driver. It will then receive the feedback from the encoder readings and raturn those to the raspberry pi.

Here is the final drawing schematic of the pcb 

<img width="590" alt="Final_board_main_sch" src="https://user-images.githubusercontent.com/87417442/127623518-b51f1023-4008-4504-84a3-e6e53f64511a.PNG"> <img width="590" alt="Final_power_sch" src="https://user-images.githubusercontent.com/87417442/127623536-c6dfc85e-9fbf-4247-918f-b1ca87b02588.PNG">

Overall the components we need to achieve this circuit are:

- Arduino Nano - V3 (A1)
- DC power supply Barrel Jack (J6)
- Motor terminal supply (J5)
- Alternative Screw terminal for power supply (J7)
- 5 Pin input terminal from encoder (J4)
- 6 pin output terminal for motor driver (J3)
- Two 3.3V-5V Bi-directional level converters from arduino (J1 & J2)
- 2A Fuse for the arduino THT/SMD (F1)
- 100uF Through hole decoupling capacitor (C1)

### Arduino Nano

To attach the arduino nano to the pcb we will need two 15 pin terminals. The Female pin terminals will be soldered onto the pcb whereas the male pins will be soldered onto the arduino where they can be attached together easily. **Make sure when designing pcb to position the footprint in an area where the usb port has clear access**.

### Level converters:

The two level converters are used to change the voltage from 3.3V to 5V or vise versa. These consist of power terminals and 4 channels for both the low and high voltage ends. Click [here](https://www.digchip.com/datasheets/download_datasheet.php?id=436483&part-number=BOB-12009&type=prod) to see the datasheet.
One will be used for the encoder input and one for the motor driver output. 

I have specially designed the footprint for the pins on these boards to be directly connected to the pcb as the chips consist of two **2.54mm** 6 pin terminals adjacently positioned with a separation of around **9.97mm**.
You can see below the layout of my footprint:
<img width="156" alt="footprint" src="https://user-images.githubusercontent.com/87417442/127123439-84998efd-94e4-4624-8b12-5f986bf3b9b3.PNG">

Where Pin 4 represents the GND pin on the Low Voltage side of the board.
The Boards will be placed with the components facing the PCB since the tracks are much easier to layout on the pcb. The pins soldered onto the chips are male, female pins are then soldered to the main pcb so that the chips can be easily attached or removed.

### 

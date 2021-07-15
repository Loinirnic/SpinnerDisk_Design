# Spinning disk design outline
Design outline for pcb and motor choices made for the spinning disk experiment 

# Experiment brief

The spinning disk experiment consists of a weighted disk attached to a DC motor where a voltage is applied causing the disk to move. An encoder is used to read the velocity output and provide the results to the user via an arduino and raspberry pi. A main PCB will be used to control where the inputs and outputs of the experiement go, and how the work together.

# Choosing a motor

The first step in designing the circuit board for the experiment is selecting a motor as this circuit will have to be modified around the motors specification which will be described later in this document.

Our motor requirements:
- A deadband of <0.5V
- A quick linear gain response after the deadband
- Good repeatability for long life time

An example of an ideal response can be from Quanser's motor. The output Gain graph show below:

<img width="527" alt="QuanserGain_graph" src="https://user-images.githubusercontent.com/87417442/125777520-b5089581-b4e3-4b79-ad56-eedee1e55f10.PNG">

# Characteristics to look for when ordering a motor:

|Motor Parameters| Quanser motor values for reference|
|----------------|-----------------------------------|
|Voltage | 12V-24V (Quanser motor is 18V)|
|Low Inertia for quick response when Voltage is applied | 4.0 × 10−6 kg-m^2 |
|Nominal speed| 3050 RPM |
|Nominal torque | 22 mNm |


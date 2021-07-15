# Spinning disk design outline
Design outline for pcb and motor choices made for the spinning disk experiment 

## Experiment brief

The spinning disk experiment consists of a weighted disk attached to a DC motor where a voltage is applied causing the disk to move. An encoder is used to read the velocity output and provide the results to the user via an arduino and raspberry pi. A main PCB will be used to control where the inputs and outputs of the experiement go, and how the work together.

# Choosing a motor

The first step in designing the circuit board for the experiment is selecting a motor as this circuit will have to be modified around the motors specification which will be described later in this document.

Our motor requirements:
- A deadband of <0.5V
- A quick linear gain response after the deadband
- Good repeatability for long life time

An ideal response can be shown from Quanser's motor, where its gain graph as shown below demonstrates what we are looking for:

<img width="527" alt="QuanserGain_graph" src="https://user-images.githubusercontent.com/87417442/125777520-b5089581-b4e3-4b79-ad56-eedee1e55f10.PNG">

## Characteristics to look for when ordering a motor:

|Motor Parameters| Quanser motor values for reference|
|----------------|-----------------------------------|
| Voltage 12V-24V | 18V |
| Low Inertia | 4.0 × 10−6 kg-m^2 |
| Nominal speed | 3050 RPM |
| Nominal torque | 22 mNm |

One of the key points to keep in mind is the speed to torque ratio, if the torque is too low then the motor will take a long time to reach its maximum speed and its movements won't be as precise. A motor with a very high torque will usually be implemented with large weights that we won't be dealing with.

## Our Motor choice

The motor we have chosen is the Maxon Coreless Motor; It has very similar characterisitcs to quanser's motor apart from a higher velocity of. Below we can see the gain graph after testing the motor without a weight compared to the quanser's response. 

<img width="527" alt="maxonVquanser" src="https://user-images.githubusercontent.com/87417442/125797198-7a27112d-0e25-4391-be40-0b0f557556fd.PNG">

## Encoder PCB Design



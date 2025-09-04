# Work in progress. Not all information is accurate

# Retract control module 85964-17010

<img width="2000" height="1414" alt="download" src="https://github.com/user-attachments/assets/b11a10ef-68cb-43d6-aabc-3a19b8b033d3" />
<img width="1085" height="926" alt="PCB Top" src="https://github.com/user-attachments/assets/a5387109-9186-4426-9568-327eda5a4a18" />
<img width="1085" height="926" alt="PCB BOttom" src="https://github.com/user-attachments/assets/e8651e67-8e56-4b33-8456-a9be84d1bc47" />
<img width="726" height="939" alt="PCB Layout" src="https://github.com/user-attachments/assets/3b029153-0159-4bb8-8012-5470c0d493a4" />
<img width="640" height="480" alt="module" src="https://github.com/user-attachments/assets/91b1352e-1e63-438d-b16e-f90134846796" />

## Basic Operation:

This is only applicable to the MK1a model of AW11. Years 1984-1986.

* When tails lights are switched on with the dashboard knob, the head lamps do not raise up. In this position, the dashboard switch grounds pin 6 on the module connector.

* Turning the dashboard switch further to the "head lamp on" position, raises the head lamps and powers the bulbs. The switch grounds pin 6 and 8 on the connector.

* Moving the switch on the dash board to the "hold" position switches off the head lamp bulbs and leaves them in the raised position. The switch releases pin 8 from ground and pin 6 remains grounded, while turning off all lights.

* Going back to head lamps position will switch the bulbs back on. Switching to the tail lamps position will switch the head lamps off again while keeping the head lamps raised. This is because pin 6 is still grounded.

* The head lamps will only go down when pin 6 is released from ground. This is in the off position.

* There is no down signal pin. The down command is handled automatically. As soon as the headlamps are fully up, the limit switch for down is triggered and the lamps will go down if pin 6 is not grounded.

* Pin 8 is head lamps up/down request input. The is controlled by the head lamp switch on the dash board.

* Pin 6 is head lamps hold position (disables head lamps down output). Also controlled by the head lamp switch.

Even though both headlamp actuate at the same time there is a relay for each light. This allows them to move at different speeds and still rest in the same position. If one lamp was in position first, then the other would be stuck in the position it was in when the first on got into the fully up position.
The head lamps cannot run in reverse direction. The head lamp motor runs in one direction only. To go down they must go all the way up first, the same is true for the opposite. This is due to the limit/encoder wheel system used.

## Technical Operation:

* D2 is a reverse bias diode for polarity protection. Should the car battery be connected backwards, D2 provides a short circuit to break the 7.5A fuse and prevent any damage to the module.

* Q2 serves a dual purpose function, a clever use of components to keep costs down. When either, the pin 7 output is activated, or the pin 6 input is grounded by the head light switch, the head lamp down circuit is disabled.
This makes sure that out on pin 11 cannot be enabled while the output pin 7 is enabled. Should somehow both outputs be enabled at the same time, pin 7 output always overrides pin 11. In other words, should the head lamps currently be going down, and there is an up signal given, the head lamp motor will continue bringing the had lamps into the down position, then the motor will not stop and bring the head lamps into the fully up position.

* Each PNP transistor base (Q1, Q2, Q3) is tied loosely to 12v (pin 10) through a 4.3K resistor. Each PNP transistor also has a 47nF disc capacitor between the base and ground for noise suppression and stability.
This will filter out micro second voltage spikes on the 12v system from things like the iginition coil. These transistors act as a “pre-amp” to drive the base of the NPN transistors firmly.

* The two NPN transistors (Q4, Q5) are what directly control the relay the provide power to the head lamp motors. There are two NPN transistors, one for controlling lamps up actuation and one for down. One transistor controls both relays, the head lamp up transistor Q5 will switch on both relays, then the down transistor Q4 will also power both relays in turn.

* Each relay has a flyback diode for back EMF protection (D5, D6). These relays control the head lamp motors directly. The motors are grounded local to them on the head lamps and the power wire to the motors is switched through the relays inside the module.
When in the resting state (headlamps completely off), the NC contacts of the relays keep the motor power cable grounded through pin 12.

* The relay coils are activated by the output pins 7 and 11 via the limit/encoder wheel in the motor units. The pins 7 and 11 connect to the limit/encoder wheels on the head lamp assembly. After going through those, the switched signal returns to the control module on pins 2 and 5. **(Update schematic to show this)**

* When head lights are commanded to go down, there is a delay where they do nothing for a few seconds. This time is up to 3.5 seconds (Ref Jimi). This delay is controlled by the discharge of C2. While there is charge in C2, the PNP transistor Q1 is held in the off state, unable to switch on Q4, which provides ground for the relays to bring the head lamps down.

* Capacitors C2 and C4 are charged through pin 6 of the module when it is grounded (technically also transistor Q5 when switched on but it will be mostly charged when the head lamp switch is moved into the tail lamp position from the off position). These capacitors control the delay before the head lamps lower when switching the head lamp switch to the off position. This is the trickiest part of the module to understand it's operation. Simply put, C4 controls the time delay before the lamps retract. C2 is a time delay that only allows the lamps to be driven downwards for so long. It stops the circuit constantly trying to drive the lamps down all the time, though the relays controlling the motor are switched off by the down position limit switch. It just keeps the transistor Q4 switched off.

* The time delay for head lamp retraction is controlled by the resistor network around C4. While C4 is charged and pin 6 of the module is grounded, Q2 is switched on. The base of Q2 is only lightly driven, but enough to saturate the transistor. The transistor solidly connects the emittor and collector together. This allows 12v through it and onto the base of the Q1. This keeps Q1 switched off, this then keeps Q4 switched off. The result is the relays are not grounded, meaning they are off and the motors are not powered.

* When pin 6 is released from ground there is no longer any ground connection on the two capacitors C2 and C4. Due to the diodes, there is also no positive voltage. The two capacitors now begin to discharge as 12v makes it's way onto both sides of them, making zero potential across them in the end. For now there is still potential across them as they slowly discharge. When C4 discharges enough through the resistor network to 12v, the transistor Q2 will be switched off by the increasing positive voltage. Q2 will no longer be holding 12v on the base of Q1. Due to the resistor R8, the capacitor C2 is only loosely coupled to the base of Q1. Then pin 6 was grounded, the resistor allowed 12v on one side of it and ground on the other. Now that there is no ground on pin 6 and also no strong connection to 12v, the capacitor charge of C2 is loosely coupled through to Q1. This loosely grounds the base of Q1 (due to the resistor R8 and R2) and switches Q1 on. When Q1 is on, it now allows 12v through it and loosely drives Q4, but enough to saturate it. Q4 strongly connects the module pin 11 to pin 12. This is the relay coil to ground, switching the relay on. R12 is there to stop Q4 driving the base of Q1 hard to ground, this would self latch and be stuck in an on state. Q1 and Q4 are only on while C2 has a charge, which has been discharging the whole time. When the charge has gone in C2, Q1 and Q4 switch off and the circuit is in its off resting state until pin 6 is once more connectoed to ground.

* If pin 6 is switched to ground and then disconnected without the head lamps going up, the circuit still functions as though the head lamps are up. That is with the exception of the motors being powered as the lowered position limit switch is activated, not allowing power to the relay coils. Such as turning on the tail lamps and then switching off the tail lamps without switching on the head lamps.

* D1 is a voltage blocking diode. It is connected to A2 of the head lamp bulb relay. When the head lamps are not on, there is no ground connection and pin 8 is pulled to 12v through the relay coil. This prevents the 12v passing into the pin 8 input and leaves R14 and R4 to bias the base of Q3 properly.

* When a head lamp up request is given on pin 8 (by grounding the pin through the head lamp switch on the dash cluster), the base of Q3 is pulled to ground from being held at 12v through resistor R4.
The RC network of R15, C5 and C6 are for stability and switch debounce. (R14 is to stop Q3 base being switched hard to the ground connection through Q5, preventing self latching).
This now allows 12v through the emitter to the collector pin of Q3, switching on Q5.

* Q5 in its resting state has it’s base held to ground through the resistor R13. This NPN transistor is switched on with 12v at its base when Q3 is switched on.
Q5 then connects the collector to the emitter, which grounds output pin 7, along with parts of the hold circuit, and lamp down output pin 11. It also charges C4 quickly through the current limiting resistor R11 and D4, setting up the head lamp down circuitry.

* The relays are switched on through the motor positioning wheel/encoder/limit switch. When the lights are fully up, the connection is broken and the relays turn off. So long as pin 6 stays grounded by the head lamp switch, the lights will stay up.

* When pin 6 is no longer grounded, when the headlights are turned off and the bulbs go out. The down circuit then works as described already.

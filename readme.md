# Work in progress. Not all information is accurate

# Retract control module

<img width="2000" height="1414" alt="download" src="https://github.com/user-attachments/assets/b11a10ef-68cb-43d6-aabc-3a19b8b033d3" />
<img width="1085" height="926" alt="PCB Top" src="https://github.com/user-attachments/assets/a5387109-9186-4426-9568-327eda5a4a18" />
<img width="1085" height="926" alt="PCB BOttom" src="https://github.com/user-attachments/assets/e8651e67-8e56-4b33-8456-a9be84d1bc47" />
<img width="726" height="939" alt="PCB Layout" src="https://github.com/user-attachments/assets/3b029153-0159-4bb8-8012-5470c0d493a4" />
<img width="640" height="480" alt="module" src="https://github.com/user-attachments/assets/91b1352e-1e63-438d-b16e-f90134846796" />

### Basic Operation:

Tail lights on does not raise the head lamps. Tail lamps on grounds pin 6.
When head lamps are switched on pin 6 & 8 are grounded. This raises the head lamps.
Switching to head lamp hold position un-grounds pin 8 and ties back to 12v through the headlamp relay coil.
Swtiching to tail lamps, pin 8 is as above and pin 6 stays grounded. Keeping lamps in the up position.

Pin 8 is head lamps up/down request input from “retractor module”
Pin 6 is head lamps hold position. (disables head lamps down output)

Even though both headlamp actuate at the same time there is a relay for each light. This allows them to move at different speeds and still rest in the same position. If one lamp was in position first, then the other would be stuck in the position it was in when the first got into position.
The head lamps cannot reverse direction. The motor runs in one direction only. To go back up they must go all the way down first, the same is true for the opposite. This is due to the limit/encoder wheel system used for positioning. It would also lower cost by not implementing a reversing circuit. 
Pin 7 output is switched on by providing a signal to pin 8 input. This input is controlled by the headlight switch on the dashboard cluster.

### Technical Operation:

D2 is a reverse bias diode for polarity protection. Should the car battery be connected backwards, D2 provides a short circuit to break the 7.5A fuse and prevent any damage.

Q2 serves a dual purpose function, a clever use of components to keep costs down. When either the pin 7 output is activated (pulled to ground) or the pin 6 input is grounded by the head light switch, the headlamp down circuit is disabled.
This makes sure that pin 11 output cannot be enabled while pin 7 output is enabled. Should somehow both outputs be enabled at the same time, pin 7 output always overrides pin 11. In other words, should the lamps be going down and there is an up signal given, they will immediately go back up after going all the way down.

Each PNP transistor base (Q1, Q2,Q3) is tied loosely to the main 12v rail (pin 10) through a 4.3K resistor. Each one also has a 47nF disc capacitor between the same points for noise suppression and stability.
This should prevent unwanted actuation due to supply voltage fluctuations. These transistors act as a “pre-amp” to drive the base of each NPN transistor firmly.

The two NPN transistors (Q4, Q5) are what directly control the relay coils by grounding A2. One transistor controls both relays. There are two NPN transistors, one for controlling lamps up actuation and one for down. Q4 is for lamp down actuation and Q5 for up.

Each relay has a flyback diode for back EMF protection (D5, D6). The relays control the retraction motors directly. The motors are grounded local to them and the power wire is switched through the relays.
When in the resting state, the NC contacts of the relays keep the motor power cable grounded through pin 12. The coils are activated by the output pins 7 and 11 via the limit/encoder wheel in the motor units.

When head lights are commanded to go down there is a delay where they stay up for a few seconds. This delay is controlled by the discharge of C2. (find out discharge path)

D1 is a voltage blocking diode. It is connected to A2 of the head lamp bulb relay. When the head lamps are not, on there is no ground connection and pin 8 is pulled to 12v through the relay coil. This prevents the 12v passing into the pin 8 input and leaves R14 and R4 to bias the base of Q3 properly.

When a head lamp up request is given on pin 8, by grounding the pin through the head lamp switch on the dash cluster, the base of Q3 is pulled to ground from being held near 12v through a pull up resistor R4.
The RC network of R15, C5 and C6 are for stability and switch debounce. (I am unsure of the function of R14 as it only comes into play when the pin 7 output is grounded. It may be for stability by adding some more bias to ground, a pull down resistor).
This now allows 12v through the emitter to the collector pin. In a resting state, the collector is held to ground through the resistors R16 and R13. When the transistor is switched on R16 acts as a current limiting resistor, a loose coupling as it were.

Q5 in its resting state has it’s base held to ground through the resistor R13. It’s emitter is always grounded through pin 12. This NPN transistor is switched on with 12v at its base. When Q3 is switched on the base of Q5 is tied to 12v through R16.
Q5 then connects the collector to the emitter, which grounds output pin 7 along with parts of the hold circuit and lamp down output pin 11 through D4. It also charges C4 quickly through the current limiting resistor R11 and D4. Before Q3 and Q5 are switched on, R5 holds pin 7 loosely to 12v.

The relays will be switched on through the motor positioning wheel. When the lights are fully up, the connection is broken and the relays turn off. So long as pin 8 stays grounded by the head lamp switch, the lights will stay up.

When pin 8 is no longer grounded, when the headlights are turned off and the bulbs go out, the down circuit is “un-disabled” and can do its job.

Pin 7 gets released from ground and biased back to 12v by R5. This voltage is blocked by diode D4.

For pin 11 output to be grounded, to energise the relays, Q4 needs to be switched on. Q4 switching on directly grounds pin 11 through the collector and emitter. Before being switched on the base is held to ground through R6 pull down resistor.

The base of Q4 is pulled up to 12v by Q1. It is a very similar situation to the Q5 and Q3 arrangement. There is the same filtering and current limiting but no debounce as there is no switch controlling this, only the positioning encoder wheel.

As soon as the lights are in the up position, the encoder wheel makes the connection to engage the relays to go back down again. While in the up position and pin 8 or pin 6 is grounded, the circuitry to ground pin 11 and engage the relays to go down is disabled.
To re-enable the circuitry, both pin 8 and 6 input needs to be disabled.

What disables the circuit is Q2. The base is tied to 12v via a pull-up resistor R3 and also has a filtering capacitor C3. The base is pulled to ground when pin 6 is pulled to ground or pin 7 output is enabled. This is through R10, D4 and/or D7.

When Q2 is on, the base of Q1 is pulled directly to 12v, firmly holding it off and therefore unable to pull the base of Q4 to 12v and then pulling pin 11 to ground. This would engage the relays to send the lights down just after they were fully up.

When the headlamp switch is turned off Q2 gets released

Before the base can be released from ground, capacitor C4 needs to be discharged as it is holding the base to ground through R11 and R10. For it to discharge, current must flow through R3, R10 and R11. This takes a few seconds due to the high amount of resistance limiting the current. 

# July 10 - Starting Research + Starting Schematic

I've wanted a sim racing setup for a while now, so I've decided to challenge myself into building one on my own. For now, I'm just going to start with the wheel, then do the pedals, shifter, and hand brake later on. This wheel is going to be able to connect wihth most racing games using the OpenFFBoard firmware library, which I can flash onto an STM32 board. Technically, they already sell a premade board with everything you need on it... but that's boring so instead I'm going to design my own. It's essentially just a modified STM32 devboard. For force feedback, I'm going to use an Eaglepower LA8308 motor with an ODrive S1 motor controller, both of which I already have from a past project. I'm not sure if I'm going to have it be direct driven or if I'm going to have to gear it down.

<img width="447" height="447" alt="image" src="https://github.com/user-attachments/assets/379b00a8-7881-4f8e-b8ca-0cc40a77067f" />

With that out of the way, let's get started with some research. So first, as I mentioned earlier, I'm going to be using the [OpenFFBoard](https://github.com/Ultrawipf/OpenFFBoard) firmware library. It can be flashed to STM32 and connected to an ODrive, it also interfaces with a lot of common driving sims, Assetto Corsa being the one that I'm most interested in. Again, as mentioned earlier, I can technically buy an OpenFFBoard, but I want to challenege myself by making my own.

<img width="600" height="600" alt="image" src="https://github.com/user-attachments/assets/0f5a8406-cca9-4fa9-a19f-d82c60eb611c" />

Anyways, with that out of the way I'm going to jump straight into designing the schematic. To be completely transparent, a good 70% of the time I spent during this session was dedicated to actually researching about how to go about making the schematic and not actually making it... I am very new to PCB design and this is a very difficult board. So far, the only thing I've actually started setting up are the CAN busses, here is a photo of my progress so far:

<img width="931" height="677" alt="image" src="https://github.com/user-attachments/assets/c5fedf20-bd93-4103-a59f-ce2e855c7423" />

**Total Time Spent: 2.23 Hours**

# July 16 - Continued Work on Schematic

Ok, so in the gap between entries, I realized that I kind of suck at PCB design still, so I actually made an STM32 devboard as practice. I already have a better grasp of the software and PCB design in general so this should be much easier. I don't know why I tried to CAN first earlier, so I'm going to move to working on the basic MCU setup. First, the MCU that I'm actually going to be using is the STM32F407VGT6. This is the same MCU that the premade OpenFFBoard uses. Using a software called STM32CubeMX I can tell what pins I need to breakout. I'm currently breaking out:
- CAN
- SWD Debug
- USB
- Crystal

<img width="853" height="796" alt="image" src="https://github.com/user-attachments/assets/bd0afba8-771d-4ba0-94d8-289eca58e66c" />

I'm going to start with my decoupling caps for power. The STM32 I'm using has 6 VDD pins + 1 additional VBAT pin, so I'm using 7 100nF decoupling caps as well as an additional 10uF bulk decoupling cap. I'm also using a pretty standard setup to setup VDDA with a ferrite bead. Next are the VCAP pins, these need to be pulled down to ground with a 2.2uF cap for each pin. For NRST, I just attached a 100nF decoupling cap to GND so it doesn't accidently activate and put the board into reset mode, then for BOOT0 I just hooked up a switch.

<img width="849" height="781" alt="image" src="https://github.com/user-attachments/assets/627e8cf3-ff85-49f7-a300-619b6586bafc" />

Next is the crystal oscillator. This MCU can use a 4-26 MHz crystal, but I'm just going to settle on a 16 MHz one. Crystals also need two symmetrical external caps to run correctly. The capacitance of these can be calcuated relatively simply:

$C_ext=2*(C_L-C_{stray})$

$C_L$ refers to the crystal's load capacitance, which for the one I'm using is 9pF and $C_{stray}$ refers to the stray capacitance of the PCB, which I'm going to assume is 5pF. Plugging this into the formula gets me a capacitance of 8pF.

<img width="591" height="519" alt="image" src="https://github.com/user-attachments/assets/59cfafe3-cd63-4075-8dc5-26bac4d93a8b" />

Then I added the other connections:

<img width="746" height="403" alt="image" src="https://github.com/user-attachments/assets/90eb31f9-b105-46ba-9a7b-582deec3a69b" />

And this connector for SWD:

<img width="255" height="232" alt="image" src="https://github.com/user-attachments/assets/f8bf84fc-64aa-4f79-b9c7-8c3627d432d8" />

Additionally, on a seperate sheet, I wired my CAN bus setup. I'm using the SN65HVD230 CAN transceiver as opposed to the TCAN332 because it is cheaper. As you can see, I have two CAN busses, one for input, and one for if I want to add some external CAN device later, then I also have a jumper that controls if the CAN bus terminates there or not:

<img width="1166" height="610" alt="image" src="https://github.com/user-attachments/assets/e65d9279-b8d6-44ed-8792-63dbaf2a352a" />

Another thing you might have noticed is that I'm using an explicit CAN_GND connection for this rather than normal CAN. This is because (according to Claude) having everything on one GND domain would cause a lot of interference. To fix this, I'm going to have three seperate domains, one for MCU GND, one for CAN GND, and one for USB GND.

**Total Time Spent: 1.85 Hours**

# July 18 - Continued Work on Schematic

I started work on the USB isolator. OpenFFBoard documentation says to have an external USB isolator, but I'm just going to integrate one on the board itself. To do this I need two components, both a power and data isolator. For the data isolator, I decided to use the ADUM3160, it's pretty standard for isolation applications like this. The power isolator was a bit more difficult to chose, mainly because the one that's most commonly used was made by a brand that was sanctioned by the US government, and all the other alternatives had very large profiles. In the end, I decided on the NTE0503MC-R. It's cheap, and has a small enough profile to where it is viable to use (although it is still going to be an eyesore on the board).

<img width="447" height="447" alt="image" src="https://github.com/user-attachments/assets/38bc912d-6d6d-407e-a331-99d4d125d758" />

This is everything connected together. I'm using a USB-C receptacle for ease.

<img width="1880" height="864" alt="image" src="https://github.com/user-attachments/assets/56a7420c-9f51-407b-a762-a423e5e318bd" />

I also added an indicator LED.

<img width="412" height="248" alt="image" src="https://github.com/user-attachments/assets/98618c77-3d4c-4d6e-acb3-55ea01a229db" />

I think that's actually all. Here's the entire schematic:

<img width="1202" height="307" alt="image" src="https://github.com/user-attachments/assets/a0e9ff35-4162-4a38-95a6-f14e6d24317f" />
<img width="1292" height="889" alt="image" src="https://github.com/user-attachments/assets/bef8514f-8c3a-424e-8f9b-04755b2db466" />
<img width="1179" height="796" alt="image" src="https://github.com/user-attachments/assets/81921709-4d43-4d6f-8d55-e51cce7efb5a" />
<img width="1495" height="1001" alt="image" src="https://github.com/user-attachments/assets/6efc74f2-80e5-4d84-824d-3eaf2ce7556c" />

(Yes ts took almost 3 hours. Figuring out USB was a pain in the ass, plus there were a lot of other small things I didn't mention)

**Total Time Spent: 2.7 Hours**

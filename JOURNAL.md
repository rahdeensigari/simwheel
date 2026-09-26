<img width="327" height="354" alt="image" src="https://github.com/user-attachments/assets/7c0aa4a1-cc94-461b-9a30-c3439ccf9ed4" /># July 10 - Starting Research + Starting Schematic

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

# July 25 - PCB Layout

I actually skipped through a lot of stuff by accident so let me briefly summarize everything.

First, I assigned footprints to all of the parts I am going to be using. A lot of them were hard to find (I actually had to make my own for the inductor) but I eventually got all of them.

<img width="729" height="820" alt="image" src="https://github.com/user-attachments/assets/03540224-fb7b-4518-b169-af194f438e33" />

After that, I imported everything in my PCB (don't have a photo I already started). I don't think I can do this as 2 layer, so I'm going to make this a 4 layer board (signal-ground-power-signal). I started my layout with my VDD decoupling capacitors:

<img width="875" height="825" alt="image" src="https://github.com/user-attachments/assets/2fe84a0b-0fda-4559-b532-d2b826156bc3" />

**Total Time Spent: 2.2**

# September 23 - Some Rescoping

As you can see, it's been a long while since I've worked on this. I spent that period learning a bit more about PCB design, and there's a couple of important changes I want to make.

First, I'm going to break out some more GPIO pins. Specifically, all of the GPIO pins that are on the official OpenFFBoard. I found a list of these [here](https://github.com/Ultrawipf/OpenFFBoard/wiki/Pinouts-and-peripherals) and I just copied them into STM32CubeMX. 90% of these aren't necessary, but I'm going to include them for good measure.

Digital inputs:
<img width="243" height="501" alt="image" src="https://github.com/user-attachments/assets/365f4fca-449e-4ea8-af9b-e32000daea7e" />

Analog inputs:
<img width="281" height="717" alt="image" src="https://github.com/user-attachments/assets/fac07377-97ef-4afd-bb53-0b1faf4b1d29" />

SPI2:
<img width="311" height="295" alt="image" src="https://github.com/user-attachments/assets/69c759e9-7347-4001-a8f6-a92591d6c046" />

PWM:
<img width="361" height="267" alt="image" src="https://github.com/user-attachments/assets/ac09a52c-c108-497e-b34f-4a4d4b894394" />

<img width="1113" height="1037" alt="image" src="https://github.com/user-attachments/assets/05d854df-af71-4ac9-b12b-c08d74908773" />

Huge change I'm going to make, I'm going to remove the USB isolator and instead have it separately like the official OpenFFBoard recommends. This simplifies my board by a lot, it completely removes the need for separate ground domains and means I don't have to integrate the large DC-DC converter in my design. I completely removed both the NTE0503MC-R converter and the ADuM3160 isolator and instead replaced it with a simple 5V to 3v3 regulator. I replaced these with a simple LDO regulator, specifically the AMS1117-3.3. I also added some ESD protection in the form of a USBLC6-2SC6, which I have past experience with.

ESD protection:
<img width="918" height="843" alt="image" src="https://github.com/user-attachments/assets/7c37de99-e80d-409c-92c1-c8360a9d17d4" />

Voltage regulation. The datasheet for the AMS1117-3.3 says to use tantalum caps, but I instead added a 1R resistor to the output 22uF cap to increase the ESR to match that of a tantalum capacitor:
<img width="905" height="316" alt="image" src="https://github.com/user-attachments/assets/eb44710f-e61f-426d-bb4a-270e44ad57ff" />

Also, for good measure, I added some pi filtering to VBUS (credit to Claude):
<img width="1048" height="408" alt="image" src="https://github.com/user-attachments/assets/db9cab75-f3a8-42f8-8b4a-c3bd6d21af91" />

**Total Time Spent: 2.34 Hours**

# September 24/25 - Continued Schematic Changes

First, I changed the values of these caps to 100n according to AN4488 (credit to Claude):
<img width="986" height="352" alt="image" src="https://github.com/user-attachments/assets/848a865f-aced-412e-b44f-791350a6fda7" />

I then added the net labels to all of the other pins I broke out yesterday:
<img width="792" height="559" alt="image" src="https://github.com/user-attachments/assets/4cc7638c-e803-4bbf-a179-f48ab32a72b0" />

I then started to break these pins out to headers. I started with SPI2, and I think I'm probably going to have this be a JST-XH header:
<img width="488" height="596" alt="image" src="https://github.com/user-attachments/assets/140a4fbe-6273-4ef0-a495-2ebe0b335356" />

Then, I broke out all of the analog inputs. I'm going to give each one a 3v3 pin, a GND pin, and a signal pin. Just like before, these are going to be JST-XH headers:
<img width="881" height="564" alt="image" src="https://github.com/user-attachments/assets/6f2ae247-7732-4652-b507-20423a50457b" />

For digital pins, I'm going to use one large header block with a pin for each digital pin (10 in total, 2 GND, 8 digital). To do this, I'm using the S10B-PUDSS-1 header, which is the same type that the ODrive S1 uses:
<img width="319" height="495" alt="image" src="https://github.com/user-attachments/assets/0a8ecf10-1967-4fef-824b-b8b036def6db" />
<img width="167" height="177" alt="image" src="https://github.com/user-attachments/assets/37e97035-dabe-4f12-9405-ee43ce68aa16" />

Finally, I added PWM. Pretty simple, just a 5 pin JST-XH header:
<img width="327" height="354" alt="image" src="https://github.com/user-attachments/assets/65ef56b9-0cd5-4136-a614-1cc92af3f779" />

I also just pushed everything on the digital header down a port and added a 3.3V pin instead of just leaving an extra pin unconnected:
<img width="506" height="759" alt="image" src="https://github.com/user-attachments/assets/dd8bd5e4-9340-41c7-b097-0e80e513d38d" />

This is kind of random, but I also just thought to add a pulldown resistor for the Rs pin on the CAN transceiver:
<img width="221" height="268" alt="image" src="https://github.com/user-attachments/assets/6cad3b8e-f597-4e43-8e3b-d5f02134d9fb" />

I also decided to add an RC filter to the analog ports, apparently this is a pretty common practice:
<img width="599" height="442" alt="image" src="https://github.com/user-attachments/assets/623c78ad-725a-4710-9735-d0ce4434d2e6" />
<img width="1230" height="619" alt="image" src="https://github.com/user-attachments/assets/fed399f7-c1a7-426e-9e8b-cd367455f7c5" />

These are all of the connectors overall, with the exception of CAN. I'm not sure as to why I decided on having CAN be on a separate page, but I am starting to regret it a bit now. It's not really worth changing though:
<img width="1751" height="413" alt="image" src="https://github.com/user-attachments/assets/c428ec2e-5f79-4749-b03c-fc61a864c223" />

I also added a proper NRST button based off of ST's recommendations:
<img width="591" height="514" alt="image" src="https://github.com/user-attachments/assets/e15dd70e-4a56-470a-b01f-aa71b6079824" />

I also hooked up NRST to the SWD header:
<img width="584" height="646" alt="image" src="https://github.com/user-attachments/assets/a4c3d8cc-22f4-4789-b040-1c95eb0649c3" />

I want to add a proper status LED that can be controlled with firmware. To do this, I first broke out a GPIO output pin from STM32CubeMX and in KiCad that will control the LED:
<img width="265" height="470" alt="image" src="https://github.com/user-attachments/assets/5c45fb06-cb3f-4b98-b020-df5bb6e90e5c" />
<img width="627" height="269" alt="image" src="https://github.com/user-attachments/assets/bc455559-99c4-4a2d-b4f8-2a4ac9ca38cf" />

I decided to use a 120R resistor to simplify my BOM, as I'm already using one for CAN:
<img width="889" height="455" alt="image" src="https://github.com/user-attachments/assets/36fe9132-1720-4fd0-82d0-74b80a025252" />

**Total Time Spent: 1.83 Hours**

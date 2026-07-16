# July 10 - Starting Research + Starting Schematic

I've wanted a sim racing setup for a while now, so I've decided to challenge myself into building one on my own. For now, I'm just going to start with the wheel, then do the pedals, shifter, and hand brake later on. This wheel is going to be able to connect wihth most racing games using the OpenFFBoard firmware library, which I can flash onto an STM32 board. Technically, they already sell a premade board with everything you need on it... but that's boring so instead I'm going to design my own. It's essentially just a modified STM32 devboard. For force feedback, I'm going to use an Eaglepower LA8308 motor with an ODrive S1 motor controller, both of which I already have from a past project. I'm not sure if I'm going to have it be direct driven or if I'm going to have to gear it down.

<img width="447" height="447" alt="image" src="https://github.com/user-attachments/assets/379b00a8-7881-4f8e-b8ca-0cc40a77067f" />

With that out of the way, let's get started with some research. So first, as I mentioned earlier, I'm going to be using the [OpenFFBoard](https://github.com/Ultrawipf/OpenFFBoard) firmware library. It can be flashed to STM32 and connected to an ODrive, it also interfaces with a lot of common driving sims, Assetto Corsa being the one that I'm most interested in. Again, as mentioned earlier, I can technically buy an OpenFFBoard, but I want to challenege myself by making my own.

<img width="600" height="600" alt="image" src="https://github.com/user-attachments/assets/0f5a8406-cca9-4fa9-a19f-d82c60eb611c" />

Anyways, with that out of the way I'm going to jump straight into designing the schematic. To be completely transparent, a good 70% of the time I spent during this session was dedicated to actually researching about how to go about making the schematic and not actually making it... I am very new to PCB design and this is a very difficult board. So far, the only thing I've actually started setting up are the CAN busses, here is a photo of my progress so far:

<img width="931" height="677" alt="image" src="https://github.com/user-attachments/assets/c5fedf20-bd93-4103-a59f-ce2e855c7423" />

**Total Time Spent: 2.23 Hours**

# July 15 - Continued Work on Schematic

Ok, so in the gap between entries, I realized that I kind of suck at PCB design still, so I actually made an STM32 devboard as practice. I already have a better grasp of the software and PCB design in general so this should be much easier. I don't know why I tried to CAN first earlier, so I'm going to move to working on the basic MCU setup. First, the MCU that I'm actually going to be using is the STM32F407VGT6. This is the same MCU that the premade OpenFFBoard uses. Using a software called STM32CubeMX I can tell what pins I need to breakout.

<img width="487" height="492" alt="image" src="https://github.com/user-attachments/assets/3d2fd351-9aa1-48cb-bf26-4a52e27d9591" />

I'm going to start with my decoupling caps for power.

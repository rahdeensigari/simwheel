# July 9 - Start

I've wanted a sim racing setup for a while now, so I've decided to challenge myself into building one on my own. For now, I'm just going to start with the wheel, then do the pedals, shifter, and hand brake later on. This wheel is going to be able to connect wihth most racing games using the OpenFFBoard firmware library, which I can flash onto an STM32 board. Technically, they already sell a premade board with everything you need on it... but that's boring so instead I'm going to design my own. It's essentially just a modified STM32 devboard. For force feedback, I'm going to use an Eaglepower LA8308 motor with an ODrive S1 motor controller, both of which I already have from a past project. I'm not sure if I'm going to have it be direct driven or if I'm going to have to gear it down.

With that out of the way, let's get started with some research. So first, as I mentioned earlier, I'm going to be using the [OpenFFBoard](https://github.com/Ultrawipf/OpenFFBoard) firmware library. It can be flashed to STM32 and connected to an ODrive, it also interfaces with a lot of common driving sims, Assetto Corsa being the one that I'm most interested in. Again, as mentioned earlier, I can technically buy an OpenFFBoard, but I want to challenege myself by making my own.

<img width="600" height="600" alt="image" src="https://github.com/user-attachments/assets/0f5a8406-cca9-4fa9-a19f-d82c60eb611c" />


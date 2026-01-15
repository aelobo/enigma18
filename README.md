### Introduction 
---
- [Amelia Lobo](https://www.linkedin.com/in/amelia-lobo/), [Nancy Anderson](https://www.linkedin.com/in/nancy-l-anderson/), [Tanisha Sethi](https://www.linkedin.com/in/tanishasethi3/) 
- 18-500 Capstone Design, Spring 2025 
- Electrical and Computer Engineering Department 
- Carnegie Mellon University

### Enigma Machine Background
---
<img width="520" height="853" alt="enigma_machine" src="https://github.com/user-attachments/assets/bfea5659-48ca-4158-b1d9-92d9e02bdcf6" />

- The Enigma machine was developed in the early 20th century and played a crucial role in cryptography and WWII history
- It was a cipher device resembling a typewriter, using a rotor system to encrypt letters
- The encryption could be configured through:
    - the rotors themselves
    - the starting positions of the rotors
    - the ring settings on the rotors
    - the plugboard
- Used by the German military for secure communications due to its complexity
- Eventually broken by Allied cryptanalysts, most famously at Bletchley Park by Alan Turing and others
- Its legacy shaped modern encryption systems and influenced culture through literature and film
	- Notably featured in the 2014 film _The Imitation Game_

### Enigma18 Overview
---
<img width="642" height="528" alt="engima18" src="https://github.com/user-attachments/assets/71069803-d99c-4a4b-a2c5-622dbc89fb02" />

Enigma18 is a modernized implementation of the WWII Enigma machine developed as an exhibition piece to educate on cryptography

- Computation handled on **Altera DE10-Standard FPGA**
- I/O handled on **Custom PCB**
- Hands-on encryption and decryption
- Use in museums and classrooms
- Compact + open-source

### System Architecture
---
See [final report](http://course.ece.cmu.edu/~ece500/projects/s25-teamb5/wp-content/uploads/sites/347/2025/05/Final_Report_Anderson_Lobo_Sethi.pdf) and [final poster](https://course.ece.cmu.edu/~ece500/projects/s25-teamb5/wp-content/uploads/sites/347/2025/04/Poster_Lobo_Sethi_Anderson.pdf) for more in-depth explanation

<img width="519" height="541" alt="block_diagram" src="https://github.com/user-attachments/assets/e24d18d0-6dea-44d6-8a2e-f948dab3fc30" />

- Plaintext sent from PS/2 keyboard to DE10-Standard FPGA through PS/2 protocol
- Configurable rotor number, rotor starting position, and ring position using rotary encoder and buttons
- Rotor settings displayed on MAX7219 seven-segment display through SPI protocol
- Ciphertext displayed on LED matrix lampboard using shift registers to minimize GPIO pin usage

### Final Design 
---
<img width="510" height="467" alt="labeled_engima18" src="https://github.com/user-attachments/assets/3a52e3e6-6865-4626-8a55-f4c69a7d8617" />

### File Structure
---
Engima18 repo contains the following important files: 
#### Top level
- `integrated_demo.v` -- top level!
	- Port declarations
	- Internal wires and register declarations
	- PS/2 state machines (make/break codes)
	- Clock divider for MAX 7-segment display
	- Button synchronizers
	- All internal modules (below)
#### Important internal modules
- `PS2_Controller.v` -- contains PS/2 data in (`Altera_UP_PS2_Data_In.v`) and PS/2 command out (`Altera_UP_PS2_Command_Out.v`) modules. Our PS/2 protocol modified from [University of Toronto ECE241](https://www.eecg.utoronto.ca/~jayar/ece241_08F/AudioVideoCores/ps2/ps2.html)
- `Scan_Code_to_ASCII.v` -- converts PS/2 scan code to seven segment display mapping
- `State_Machine.v` -- top level module to control encryption, some logic from [enigmaFPGA repo](https://github.com/mmicko/enigmaFPGA)
- `Synchronizer.v` -- synchronizer module for buttons and rotary encoders
- `ASCII_to_MAX.v` -- display ASCII on MAX 7-segment display. Could be rotor starting position or ring position
- `Decimal_to_MAX.v` -- display decimal on MAX 7-segment display, rotor number
- `top.v` -- MAX 7-Segment display top level
- `ASCII_to_Lampboard.v` -- converts 
- `shifter.v` -- shift register for lampboard display
### Engima18 Usage
---
1. Plug DE10-Standard FPGA power cable into standard wall socket and connect to power port next to red button on left side of Enigma machine
2. Connect PS/2 keyboard (purple cable) to FPGA through slot on left side of Engima machine, 3rd port to the left of the red button

<img width="915" height="452" alt="image" src="https://github.com/user-attachments/assets/016a308a-364e-4994-9fa9-d11e0a44e8f1" />

3. Attach the "rotor" to the top of the PCB (see image)
	1. Do not use the connector on the top right
<img width="606" height="583" alt="image" src="https://github.com/user-attachments/assets/a6fa21fb-d954-4c8f-ab4d-c31ad6dc6810" />

4. Ensure all wires connecting GPIO from FPGA to PCB are secured (see image) - please do not disconnect these wires ever :) 
	1. Each wire is connected to its corresponding location on FPGA and PCB
	2. All pins on inner row should be occupied except for the 6th pin from the top 
	3. On outer row, only the 6th from the bottom should be connected (GND, black wire)
<img width="760" height="682" alt="image" src="https://github.com/user-attachments/assets/1d30e29b-5476-4338-841d-6eb407d30fdf" />
    
5. Power on FPGA using red power button 
6. Configure rotor number, rotor starting position, and ring position by pressing the corresponding button and turning the rotary encoder
	1. Look at screen printed text on PCB to determine which button to press
7. Press down and hold on plaintext key on PS/2 keyboard to see the ciphertext on FPGA
	1. NOTE: releasing the key will "turn" the rotors (see on PCB seven segment display) and the ciphertext will disappear from FPGA seven segment display 
8. Verify encryption logic on this online [Engima simulator](https://www.101computing.net/enigma-machine-emulator/)
9. Reset the Enigma machine by pressing the rightmost button on FPGA (KEY0) (see image)
<img width="769" height="415" alt="image" src="https://github.com/user-attachments/assets/34b47519-80c9-4ce0-9f8f-7caee57be17f" />

11. Power off FPGA by pressing red power button

### Troubleshooting
---
1. It is important to not type quickly on the keyboard (definitely not at normal typing speed or above). Keep the speed to around 1 keypress per second. 
	1. Typing too fast may result in the machine malfunctioning. You will need to reset the machine by pressing KEY0. 
2. When powering the machine on, the 7-segment displays should read 1AA 2AA 3A and the LED keyboard should light up. If this does not happen, turn off the FPGA, recheck the connections, and turn it on again. 

### Additional Resources
---
- Final [report](http://course.ece.cmu.edu/~ece500/projects/s25-teamb5/wp-content/uploads/sites/347/2025/05/Final_Report_Anderson_Lobo_Sethi.pdf)
- Final [poster](https://course.ece.cmu.edu/~ece500/projects/s25-teamb5/wp-content/uploads/sites/347/2025/04/Poster_Lobo_Sethi_Anderson.pdf)
- Final [video](https://youtu.be/ryhHtJOhAuE)
- Additional information (weekly status reports, testing videos) on [our website](https://course.ece.cmu.edu/~ece500/projects/s25-teamb5/)

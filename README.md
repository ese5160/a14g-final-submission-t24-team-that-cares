[![Review Assignment Due Date](https://classroom.github.com/assets/deadline-readme-button-24ddc0f5d75046c5622901739e7c5dd533143b0c8e959d652212380cedb1ea36.svg)](https://classroom.github.com/a/kzkUPShx)
# a14g-final-submission

    * Team Number: 24
    * Team Name: Team that Cares
    * Team Members: Yu Feng & Xiang Li
    * Github Repository URL: https://github.com/ese5160/a14g-final-submission-t24-team-that-cares/tree/main
    * Description of test hardware: Smart Hat for people with disabilities

## 1. Video Presentation

Video is available at the following link:

https://youtu.be/OUcvav57-Wk?si=e9zNmfLbQJUyDDRR

## 2. Project Summary

### Device Description
The smart hat device aims to enhance the safety for people with disabilities especially those visually impaired. It utilizes ultrasonic distance sensors to help users detect and avoid adjacent pedestrians and obstacles, and an accelerometer to detect free-falls by analyzing acceleration and tile in three dimensions.

The device utilizes the Internet to enhance safety features. When a fall is detected, it sends the relevant data to the cloud. This data can be used for real-time monitoring and prompt emergency responses. Additionally, the device automatically sends an emergency notification to pre-set contacts via email, ensuring that help can be quickly dispatched if the wearer is in distress. 

### Inspiration
Our motivation for this project comes from our concern for visually impaired individuals and our passion for assistive technology. In many countries, there are not enough guide dogs available to assist visually impaired people with their mobility. Many visually impaired individuals face various challenges that prevent them from traveling outside, and staying home for extended periods can lead to numerous psychological issues.

### Device Functionality

Two sensors, as mentioned, ADXL345 and US100 are communicating to MCU with I2C and UART respectively. The actuatior buzzer is achieved with GPIO. With the help from WINC1500, we have internet conncetion capcabilities to enable real-time data display for accelerometer and over-the-air firmware update.

![alt text](<BlockDiagram.png>)

### Challenges

1. Firmware: I2C communication for ADXL345

   We initially struggled with the I2C communication between the ADXL345 and the MCU. After debugging with logic analyzer and code scrudinize,  we realized that the message structure for I2C is quite different between the HAL level I2C package enclosed in the Microchip code package and the ADXL345 requirement. Thus, we modified the read and write functions deep down the I2C packages and reconstructed the I2C setup/message structure.

2. Hardware: Memory limitation to run amplifier (TPA2016) to Speaker (1314)

   We successfully implemented all the codes for the amplifier and speaker, including the implementation of DAC functionalities, the implementation of the TPA2016 amplifier. But we found out that the IO speed is gating the performance of the speaker. More details can be found in section 3: Hardware and SOftware Requirements.

### Prototype Learnings

· What lessons did you learn by building and testing this prototype?

We learned how to disconnect jumpers and test the outputs of the buck and booster circuits. We also gained knowledge on how to find
communication protocols for different sensors from their datasheets. Understanding the importance and necessity of jumpers and test points was also acquired.

· If you had to build this device again, what would you do differently?

We might consider abandoning the 5V booster circuit, as all sensors can operate stably under a 3.3V voltage. Some jumpers, especially
those input/output jumpers for power regulators and buck circuit, will be adjusted to default to the open circuit (not soldered).

### Next Steps

We might consider adding a temperature and humidity sensor, attempting to utilize the built-in free fall detection feature of the accelerometer, 
adding more buttons to toggle functions (such as temporarily disabling the distance sensor and only using the accelerometer), attempting once
again to replace the buzzer with DAC audio output, and processing more data in the cloud, such as readings from the distance sensor.

### Takeaways from ESE5160

Hardware: Learned multi-layer PCB design. Application of Jumpers and Test Points. Buck, Booster circuits, and power regulation circuits.
Software: Learned I2C, UART, and SPI communication, SD card access, NODE-RED IoT design, use of FreeRTOS, and DAC utilization.

### Project Links

#### Node-RED instance

A screenshot of our node red setup is attached at the end

http://20.242.125.234:1880

#### A12G code repository
https://github.com/ese5160/a12g-firmware-drivers-t24-team-that-cares.git

#### Final PCBA
https://upenn-eselabs.365.altium.com/designs/AB685EF0-E911-4FC0-888C-18EAAA2C1136

## 3. Hardware & Software Requirements

### Hardware Requirements Evaluation:

a. Original Plan:

The smart hat is based on Atmel ATSAMW25-MR210PB and shall be powered by a 3.7V single cell Li-Ion battery. Through the Atmel ATSAMW25-MR210PB, 4 other hardware components(sensors and actuators) are embedded in the system, specifically:

Triple-axis accelerometer: Analog Devices: ADXL345
Ultrasonic distance & Temperature sensor: Adafruit:US100
Stereo audio amplifier: Adafruit: AGC - TPA2016
Speaker: Adafruit-1314
Buttons

b. Power Architecture:

We succesfully implemented the power architecture as our initial design. A 3.7V Li-Ion battery can be used and the device works as a stand-alone device. A buck and a booster is responsible for converting the battery power into needed 3.3V and 5V outputs and delivers to the rest of components used.

c. Triple-axis accelerometer: Analog Devices: ADXL345

The ADXL345 was succesfully integrated in the PCB design flawlessly and was implemented with I2C communication. It is powered by the 3.3V power from buck circuitry. It is responsible for detection free-falls and did a great job at catching both 'forward fall' and 'backward fall'. It is sensitive to tilt which is beneficial for our application.

d. Ultrasonic distance & Temperature sensor: Adafruit:US100

The US100 distance sensor is not mounted on the PCB, since it needs to detect nearby pedestrains and obstacles, it needs to be mounted in the front of the hat. It is connected to the 5V port(from booster) on the PCB, via wires. It is eventually taped on the front side of the hat and face forward. It is implemented using UART and returns the distance in millimeters. It can pick up all objects within a certain range(the range is set by our criterion at our discretion).

e. Stereo audio amplifier and Speaker: Adafruit: AGC - TPA2016 and Speaker: Adafruit - 1314

The amplifier TPA2016 is powered by 5V (booster) and is integrated on the PCB. It takes in decoded audio files from DAC and sends over to the speaker unit. It is successfully implemented in I2C and manages to process the audio files. 

However, it was not eventually used in the device because of the limitation of memory size of the MCU. The work flow of the speaker system is as follows: SD card(with audio file) -> MCU -> DAC-> Amplifier -> Speaker. We stored the audio file (WAV file) in the SD card. MCU reads from SD card in chunks, while sending read chunk to DAC concurrently. Then DAC would forward decoded information to Amplifier and would then drive the speaker. Everything was successfully implemented until we noticed a an issue with reading from SD card. It takes a lot longer to read from SD card than all the rest of the steps. The result of that is we never able to hear a completed work/sentence, instead syllable is being played followed by a longer period of white noise. 

A few limitations that stopped us from using other approaches:
1. limited memory space: we tried to read all of the WAV file into a buffer but have failed to do so due to the limit in size
2. We tried to read from SD card with larger size chunks, hoping it would mitigate the blank periods between each syllable. Unfortunately with the memory size limitation, even if we read 2048 samples a time(2 bytes per sample), the blank periods is still significant.
   
We reviewed the issue with logic analyzer, the behavior mentioned above is proved by a waveform corresponding to the audio message(0.05ms) followed by a period of blank signal(0.1ms), then repeat. 



## 4. Project Photos & Screenshots

Your final project, including any casework or interfacing elements that make up the full project (3D prints, screens, buttons, etc)
1. Project photos:

   Completed project:
   
   ![alt text](<hat1.jpg>)
   
   Demo wearing hat:

   ![alt text](<wearhat.jpg>)

   Break-down pictures:
   
   ![alt text](<act1.jpg>)

   ![alt text](<act2.jpg>)


3. PCBA standalone:

   ![alt text](<pcb1.jpg>)
   
   ![alt text](<pcb2.jpg>)
   
   ![alt text](<pcb3.jpg>)

4. Thermo image of PCB under load:

   ![alt text](<thermo.jpg>)

5. Altium screenshots:

   ![alt text](<PCBA_2D.png>)

   ![alt text](<PCBA_3D.png>)

6. Node red screenshots:

   ![alt text](<nodered.png>)

   ![alt text](<nodered2.png>)  

7. Block diagram:

![alt text](<BlockDiagram.png>)



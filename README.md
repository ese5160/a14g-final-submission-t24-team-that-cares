[![Review Assignment Due Date](https://classroom.github.com/assets/deadline-readme-button-24ddc0f5d75046c5622901739e7c5dd533143b0c8e959d652212380cedb1ea36.svg)](https://classroom.github.com/a/kzkUPShx)
# a14g-final-submission

    * Team Number: 24
    * Team Name: Team that Cares
    * Team Members: Yu Feng & Xiang Li
    * Github Repository URL: https://github.com/ese5160/a14g-final-submission-t24-team-that-cares/tree/main
    * Description of test hardware: Smart Hat for people with disabilities

## 1. Video Presentation

## 2. Project Summary

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

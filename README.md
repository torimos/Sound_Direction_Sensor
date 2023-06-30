#  Sound Direction Sensor Board
# Sound_Direction_Sensor
This board detects the direction of sound with 4-pdm mems mic using GCC-PHAT algorithm.

<img src="https://github.com/meerstern/Sound_Direction_Sensor/blob/main/IMG/sds5.gif" width="360">

## Summary
  * This is a board that calculates and outputs the result (angle) using eight LEDs, I2C, and UART based on the time difference of sound.
  * It is equipped with four PDM digital microphones, SPH0641LM, and STM32F411RC.
  * It uses the GCC-PHAT (Generalized Cross-Correlation, PHAse Transform) method.
  * It filters sound in the frequency range of a few kHz and detects the direction from the time difference of the sound.
  * To reduce false detections, it outputs the angle when there is a significant change (standard deviation) in the sound.
  * The threshold for sound change (standard deviation) can be changed using the variable resistor on the board or through a command.
  * It is suitable for detecting the direction of human voices, rhythmic sounds, and similar sounds.
  * For momentary sounds such as hitting hands or objects, there is a greater influence from reflections, leading to increased chances of false detections.
  
## Specifications
  * Angular resolution is approximately 22.5 to 30 degrees (dependent on sound source, environment, and processing).
  * Equipped with 8 direction LEDs.
  * Measures approximately 5-6 times per second and outputs the results.
  * Sampling rate of 36kHz.
  * Power supply: 3.3-5V. Supports UART or I2C communication (5V tolerant).
  * I2C interface with Grove-compatible connector, UART connection available via pin header on the board.
  * Default UART settings: Baud rate: 9600bps, data size: 8 bits, no parity, 1 stop bit.
  * Upon startup, the board reads the value of the variable resistor only once and sets it as the threshold for sound variation (standard deviation).
  * The threshold for sound variation (standard deviation) can be overwritten and set through I2C or UART.
  * The sound variation (standard deviation) threshold is cleared and not retained during reset or power cycle (the variable resistor value is re-applied).
  * After power-on, the direction detection automatically starts, allowing direction detection with only power supply.
  
<img src="https://github.com/meerstern/Sound_Direction_Sensor/blob/main/IMG/sds3.jpg" width="360">
<img src="https://github.com/meerstern/Sound_Direction_Sensor/blob/main/IMG/sds4.jpg" width="360">
  
## Exterior:
  * Circular board with a diameter of 40mm (excluding connectors).
  * Microphone distance: Circular with a diameter of 36mm.
  * Three M2 mounting holes with a spacing of 24.3mm horizontally and vertically.

##Application Examples:
  * Small swivel robot that orients towards the sound source.
  * Camera stage that adjusts the camera direction for shooting using voice commands.

## Notes:
  * Not suitable for applications requiring precise angle detection.
  * May experience false detections due to sound reflections or sampling timing.
  * Not suitable for use in noisy environments or environments with shocks/vibrations.
  * Intended for detecting sound direction within a range of approximately 5cm to 1m.
  * Detection accuracy may be compromised for sound coming from distances greater than a few meters due to influences such as reflections.
  * If the direction LEDs frequently illuminate or there are frequent false detections, consider increasing the sound variation threshold.
  * In some cases, the direction detection may not stabilize immediately after startup due to the effects of software filtering.
  * Grove cables are not included. Please prepare them separately.

## Threshold Adjustment Guidelines
  * Set the threshold for the sound variation value (Act) in an environment without sound to approximately 1.2 to 1.5 times its value.
  * If there are many false detections, set the threshold higher than the sound variation value (Act) according to the environment.
    Example: In a quiet state with a sound variation value around 0.8, set the sound variation threshold to approximately 1.2.

## UART Communication
  * After startup, the firmware version, build date, and the threshold for sound variation will be outputted.
  * Approximately 5 to 6 times per second, the detected angle (Deg), pre-filter angle (DegOrg), and sound variation value (Act) will be outputted.
  * Values below the sound variation threshold will be outputted as -1 degrees, indicating undetected.
  * You can change the sound variation threshold by entering act=XX.XX and pressing Enter.
  * You can check the current sound variation threshold by entering act? and pressing Enter.
  * You can perform a software reset by entering rst! and pressing Enter.
  * In the example below, with a sound variation threshold of 1.0, the fourth value with a sound variation of 1.6 is detected as 206.6 degrees.

	Sound Direction Sensor vX.X (ビルド日時)[\r\n]  
	Activity Threshold: 1.0[\r\n]  
	Deg:-1.0,        DegOrg:201.8,   Act:0.69,[\r\n]  
	Deg:-1.0,        DegOrg:0.0,     Act:0.71,[\r\n]  
	Deg:-1.0,        DegOrg:90.0,    Act:0.73,[\r\n]  
	Deg:206.6,       DegOrg:206.6,   Act:1.60,[\r\n]  
	Deg:-1.0,        DegOrg:90.0,    Act:0.73,[\r\n]  

| Command Name | Command | Argument | Example |
| --- | --- | --- | --- |
| Set Sound Variation Threshold | act= | Decimal setting value | act=1.2 (Press Enter) |
| Check Sound Variation Threshold | act? | None | act? (Press Enter) |
| Reset | rst! | None | rst! (Press Enter) |


I2C Communication
-----------------
  * The I2C device addresses are 0x60 (8-bit address) and 0x30 (7-bit address).
  * The angles and sound variation values are stored in the registers as integer values multiplied by 10.
  * If necessary, multiply by 0.1 on the master side to convert to angles or other values.
  * Similarly, when writing, write integer values that are 10 times the desired threshold value.
  * Read operation
  
| Register Name | Offset | Details |
| --- | --- | --- |
| Angle H (Post-filter) | 0x00 | High byte of angle |
| Angle L (Post-filter) | 0x01 | Low byte of angle |
| Angle H (Pre-filter) | 0x02 | High byte of angle |
| Angle L (Pre-filter) | 0x03 | Low byte of angle |
| Sound Variation Value H | 0x04 | High byte of observed value |
| Sound Variation Value L | 0x05 | Low byte of observed value |
| Update Flag | 0x06 | Value update flag (0x01) |
| Device ID | 0x07 | Fixed value: 0x43 |

* Write Operation  
  
| Register Name | Offset | Argument |
| --- | --- | --- |
| Sound Variation Threshold H | 0x04 | High byte of the threshold value |
| Sound Variation Threshold L | 0x05 | Low byte of the threshold value |

*Please write the 2-byte value of the threshold, consisting of the High Byte and Low Byte, at offset 0x04. (Slave address) + (Offset) + (2-byte threshold value)

## Sales Website
  * [Switch Science](https://www.switch-science.com/)

<img src="https://github.com/meerstern/Sound_Direction_Sensor/blob/main/IMG/sds1.jpg" width="360">

<img src="https://github.com/meerstern/Sound_Direction_Sensor/blob/main/IMG/sds2.jpg" width="360">

[2]: https://www.switch-science.com/products/8129


https://youtu.be/5QTcDwFN5Rs

https://youtu.be/rlhwlwNsYnQ




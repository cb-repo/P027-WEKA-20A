# WEKA - 20A v2.2

<img src="assets/WEKA_Front.JPG" width="300"> <img src="assets/WEKA_Back.JPG" width="300">

<img src="assets/weka.jpg" width="300">

The WEKA is a compact dual DC-motor speed controller, designed to be a robust lightweight control platform for combat robots and other high-performance robotic systems. It features flexible mixing options, braking control, and direct radio input compatibility, offering smooth integration without bulky wiring or external logic. 

The product is named after the [Weka](https://en.wikipedia.org/wiki/Weka), a curious and tenacious flightless bird endemic to New Zealand. 

Developed, assembled, and tested in [Christchurch, New Zealand ](https://www.google.co.nz/maps/place/Christchurch+New+Zealand) by Connor Benton with additional testing and support in [Nashua, USA](https://www.google.co.nz/maps/place/Nashua+USA) by Seth Scafer.  

## FEATURES

- 2x Bi-Directional DC-motor outputs
- Status LED's to indicate run, fault and calibration modes
- Under-voltage, over-current, and over-temperature protection
- Internal BEC to provide power to the radio reciever
- Failsafe checks for loss of connection with radio
- Calibrate function to customise drive mode, channel mapping, and channel inversion
- Includes a 100nF capacitor across each motor to filter noise and voltage spikes

## SPECIFICATIONS

- **Dimensions:** 30x20x4 mm
- **Weight:** 3g *excluding wires*
- **Voltage Input:** 2-6S LiHV (6.0-26.1V)
- **Motor Output:** 20A per channel 
- **BEC:** 5V, 1A. Designed to power the radio reciever but no motors or servos
- **Signal Input:** Servo PWM

## USAGE
### Status LEDs

There are 3 LEDs on the WEKA (1x red, 2x blue). The red LED is in the center and a blue LEDs are on each edge beside the motor outputs. See table below to detail LED behaviour:

| &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; <br> State| Red <br> LED   | Blue <br> LEDs | | 
| :---:                     | :---:     | :---:     | :--- 
| No Power                  | OFF       | OFF       |  
| Standby                   | ON        | OFF       |         
| Driving                   | ON        | ON        | Each blue LED will immunimate when their corresponding input is being driven. 
| Fault <br> Signal-Input        | ON        | ALTERNATING FLASH       | 1Hz  
| Fault <br> Under-Voltage       | ON        | FLASH         | 1Hz
| Fault <br> Over-Temp    | ON        | FAST FLASH    | 5Hz 
| Calibration               | ON        | PATTERN       | See Calibration section for specific LED patterns

### Fault Conditions

WEKA continuously monitors for several fault conditions during operation. The monitored faults, listed from highest to lowest priority, are:

1. **Over-Temperature:** An over-temperature fault occurs when the primary sense circuit reaches 100°C. 

2. **Under-Voltage:** An under-voltage fault occurs if the battery voltage falls below 3.0V (per cell).

    Important note: When WEKA powers on, it automatically detects how many cells your battery has to set the appropriate low voltage level. Some battery cell counts have a voltage overlap with adjacent counts, see table below. So, the WEKA then assumes the battery is more charged when powering on to solve the overlap. If you start up with a low battery, it might detect and set the undervoltage threshold too low. To avoid issues, we recommend powering on with a fully-charged battery. 

    | Battery Cells | Low Voltage   | High Voltage  | At Risk   | Percentage Overlap |
    | :---:         | :---:         | :---:         | :---:     | :---: |
    | 2s            | 6.0V          | 8.70V         | No        | n/a |
    | 3s            | 9.0V          | 13.05V        | No        | n/a |
    | 4s            | 12.0V         | 17.40V        | Yes       | 19.4% |
    | 5s            | 15.0V         | 21.75V        | Yes       | 35.6% |
    | 6s            | 18.0V         | 26.1V         | Yes       | 46.3% |

 - **Signal-Input:** A signal-input fault occurs if either S1 or S2 inputs from the radio is lost. 
 
    *Note: You still need to set the failsafe on the radio reciever to handle a loss of connection between the reciever and transmitter*.

## Calibration

The WEKA ESC has a number of parameters that are auto-detected during the calibration process. These are:

- **Driving Mode:**
    - Tank (Each input controls a different motor)
    - Arcade (One input controls both motor speeds and the second controls steering) 
- **Input Mapping:**
    - Any input channel can control any output.
    - eg: Using Arcade mode, the throttle channel from the radio can be soldered to either S1 or S2 on the WEKA, and vice-versa for the steering.  
- **Input Inversion:**
    - Any input channel can be reversed.
    - eg: No resoldering the motor wires or reversing the signal in radio.

If at any point the calibration is corrupted, the WEKA will roll back to the factory settings:
- **Driving Mode:** ARCADE
- **Motor A:**
    - Channel: 1
    - Reverse: False
- **Motor B:**
    - Channel: 2
    - Reverse: False

### Calibration Procedure

Please read this section in full before initiating calibration for the first time. The process moves quickly once started, but becomes intuitive after your first go around.

Calibration settings are only saved once the entire procedure is completed. If you make a mistake, just power cycle the device and start again, nothing is saved until the end. 

1. Preparation
    - Install the motors in the orientation you want in the robot. This allows the system to correctly detect mapping and direction during calibration.
    - Connect the motors and radio receiver to the WEKA. 
    - Ensure the receiver is bound to your transmitter.
    - Make sure the robot is elevated or safe to move within a test-box. The robot will twitch the during calibration.

2. Entering Calibraiton Mode
    - Power on the WEKA.
    - Within 10 seconds, wiggle any connected input stick 20 times (must be wiggled full forward and full reverse).
    - Calibration will only begin if both input channels are detected and the WEKA is not in a fault condition.
    - Both blue LEDs will pulse 10× to confirm entering calibration mode.
    - While the LEDs are pulsing, the user must stop wiggling the stick and return all inputs to their neutral positions.
    - Once the LEDs have finished pulsing, they will remain solid and wait for the user to start the calibratrion.

3. Drive Mode Detection.
    
    This stage uses a "Simon-says" process that twitches the motors (moves the robot in a direction) and getting the user to input that motion back on the remote. For example:
    - Robot twitches forward: Simualtaneously push both sticks forward for tank drive, or push one stick forward for arcade.
    - Robot twitches left: Simualtaneously push left stick back and right forward for tank, or push one stick left for arcade.

    The steps for this stage are:
    - Both LEDs are ON from step 2.
    - Move any stick to MAX and back to CENTER to start.
    - The Simon-says process is: 
        1. Both LEDs turn OFF.
        2. Robot twitch in a direction.
        3. One LED turns ON.
        4. Match that robot motion on your radio using the desired driving style.
        5. Second LED turns ON.
        6. Return stick(s) to CENTER.
        7. Both LEDs turn OFF.
        8. Both LEDs pulse 3× to confirm completion of current step.
    - The Simon-says process repeats for a second direction.

 4. Calibration Complete
     - Both blue LEDs pulse 10× to indicate completion of calibration and the parameters are saved.  
     - The WEKA immediately enters Normal Operation using the new calibration settings.

## DISCLIAMER

This product, and all other cb-technology or Just ‘Cuz Robotics products, are intended for use in hobby projects, and for educational and experimental uses. These devices are not suitable for any application where human life or health or significant property value depend on their proper operation. Neither cb-technology ltd or Just ‘Cuz Robotics, LLC are responsible for any loss or damage incurred by the operation or failure of this product or any others. The specifications listed are accurate to the best of our knowledge but are not guaranteed in any way. The buyer assumes all responsibility for proper use, testing, and verification of this unit in any application. cb-technology ltd and Just ‘Cuz Robotics, LLC’s liability is limited to replacement of defective DOA units. By installing and using this unit you are agreeing to these terms. If you do not agree you may return any unused units for a refund.

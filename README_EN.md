This is an automatic translation, may be incorrect in some places. See sources and examples!

# Accelmotor
Library for expanded control and stabilization of the engine with encoder for Arduino
- inherits all the chips from the Gyvermotor library (support for different drivers and modes)
- mode of maintaining feedback speed
- turning mode to a given angle with feedback
- custom -made pid regulator coefficients
- restriction of acceleration and speed
- The library accepts any type of feedback: encoder, potentiometer, etc.
- support for motor-tractors, tuning the transfer of encoder
- the regulator takes into account the "dead zone" of the motor
- all functions work in degrees and Tiki Encoder

## compatibility
Compatible with all arduino platforms (used arduino functions)

### Documentation
There is [extended documentation] to the library (https://alexgyver.ru/accellemotor/)

## Content
- [installation] (# Install)
- [initialization] (#init)
- [use] (#usage)
- [Example] (# Example)
- [versions] (#varsions)
- [bugs and feedback] (#fedback)

<a id="install"> </a>
## Installation
- The library can be found by the name ** Accelmotor ** and installed through the library manager in:
    - Arduino ide
    - Arduino ide v2
    - Platformio
- [download the library] (https://github.com/gyverlibs/accellmotor/archive/refs/heads/main.zip) .Zip archive for manual installation:
    - unpack and put in * C: \ Program Files (X86) \ Arduino \ Libraries * (Windows X64)
    - unpack and put in * C: \ Program Files \ Arduino \ Libraries * (Windows X32)
    - unpack and put in *documents/arduino/libraries/ *
    - (Arduino id) Automatic installation from. Zip: * sketch/connect the library/add .Zip library ... * and specify downloaded archive
- Read more detailed instructions for installing libraries [here] (https://alexgyver.ru/arduino-first/#%D0%A3%D1%81%D1%82%D0%B0%BD%D0%BE%BE%BE%BED0%B2%D0%BA%D0%B0_%D0%B1%D0%B8%D0%B1%D0%BB%D0%B8%D0%BE%D1%82%D0%B5%D0%BA)
### Update
- I recommend always updating the library: errors and bugs are corrected in the new versions, as well as optimization and new features are added
- through the IDE library manager: find the library how to install and click "update"
- Manually: ** remove the folder with the old version **, and then put a new one in its place.“Replacement” cannot be done: sometimes in new versions, files that remain when replacing are deleted and can lead to errors!


<a id="init"> </a>
## initialization
`` `CPP
// initialization inherited from gyvermotor
// Prevention options depending on the type of driver:
Accelmotor Motor (Driver2wire, Dig_pin, Pwm_pin, Level);
Accelmotor Motor (Driver3wire, Dig_pin_a, Dig_pin_b, Pwm_pin, Level);
Accelmotor Motor (Relay2wire, Dig_pin_a, Dig_pin_b, Level);
/*
  Driver2wire - two -wire driver (direction + ShIM)
  Driver3wire - a three -wire driver (two directions + shim)
  Relay2wire - Relay as a driver (two pins of the direction)

  dig_pin, dig_pin_a, dig_pin_b - any digital PIN MK
  Pwm_pin - any shim pin mk
  Level - Low / High - Driver level.If, with an increase in speed, the motor, on the contrary, slows down, change the level
*/
`` `

<a id="usage"> </a>
## Usage
`` `CPP
// controls the motor.Call as often as possible (inside the timer with a period dt)
// accepts the current position of VALa Motor (according to the encoder)
// Returns True if the motor still moves towards the goal
Bool Tick (Long Pos);

// Installation of the gear ratio of the gearbox and encoder
// Example: if the gearbox is 1:30 - we transfer to function 30
// Example: if the gearbox is 1:30 and an encoder for 12 ticks - we transfer 30*12
VOID setratio (Float Ratio);

// Installation of the regulator period (2-50 milliseconds are recommended)
VOID Setdt (Int DT);

// Installation and receipt of the current position in the tics of encoder and degrees.
// setcurrent (POS) is equivalent to call Tick (POS) and, in principle, is not needed!
VOID setcurrent (LONG POS);
Long getcurrent ();
Long getcurrentdeg ();

// Installation and receipt of the target position in the tics of encoder and degrees
VOID settarget (LONG POS);
VOID settargetdeg (Long POS);
Long gettarget ();
Long gettargetdeg ();

// Installation of maximum speed in ticks of encoder/second and degrees/second
VOID SetmaxSpeed (Intsed);
VOID setmaxSpeedDeg (int spEED);

// Installation of acceleration ticks of encoder and degrees per second
VOID setaccoleration (Intscel);
VOID setaccolerationdeg (intactel);

// Installation and receipt of targeted speed in the ticks of encoder/second and degrees/second
VOID settargetSpeed (int sponeD);
VOID settargetSpeeddeg
IntargetSpeed ();
IntargetSpeedDeg ();

// Get the current speed in ticks of encoder/second and degrees/second
Intspeed ();
Intspeeddeg ();

// Get the current PWM signal (Float from PID of the regulator)
Float Getduuty ();

// manual installation of the operating mode
// IDLE_RUN - Tick () does not control the motor.Can be used for debugging
// Accel_pos - Tick () works in a smooth follow -up mode to the target corner
// pid_pos - tick () operates in a sharp follow -up mode to the target corner
// Accel_Speed - Tick () works in a smooth maintenance mode (with a given acceleration)
// pid_Speed - Tick () works in the mode of maintaining speed on PID to the regulator
VOID Setrunmode (am_runmode mode);

// returns True if the shaft of the motor is blocked and the signal is supplied
Bool ISBLOCKED ();

// Regulator PID coefficients
// proportional - aggressiveness of management depends on it, you need to increase KP
// with an increase in the load on the shaft, so that the regulator supplies a larger control PWM signal
Float KP = 2.0;// (value by default)

// Integral - allows you to level the error over time, has a cumulative effect
Float Ki = 0.9;// (value by default)

// differential.Allows you to slightly smooth out jerks, but with great meaning
// itself becomes the cause of jerks and build -up systems!
Float KD = 0.1;// (value by default)

// Install the zone of stopping the motor for the stabilization regimen (in silence 8)
VOID SetStopzone (Int Zone);

// set the limits of steps/degrees, outside which the motor will be hard to disable for safety.If by zero, there is no restriction (by the silence)
VOID SETRANGE (LONG MIN, LONG MAX);
VOID SETRANGEDEG (LONG MIN, LONG MAX);

Long Controlpos = 0;// for debugging
`` `

<a id="EXAMPLE"> </a>
## Example
`` `CPP
/*
An example of a motor management using a driver of a full bridge and a potentiometer
For the regimes for the position and maintenance of speed
*/
#include "Accelmotor.h"
Accelmotor Motor (Driver2wire, 2, 3, High);

VOID setup () {
  Serial.Begin (9600);
  // I use the jga25 motor
  // Reducer 1: 21.3
  // Encoder 12 ticks for circulation
  Motor.Setratio (21.3 * 12);

  // Integration period (in silence 20)
  Motor.Setdt (30);// milliseconds

  // Installation of maximum speed for the accel_pos mode
  Motor.SetmaxSpeedDeg (600);// in degrees/s
  //motor.SetmaxSpeed(400);// in ticks/sec

  // Installation of acceleration for the accel_pos mode
  Motor.Setaccelerationdeg (300);// in degrees/second
  //motor.setacceleration(300);// in ticks

  // Minimum (according to the module) shim signal (at which the motor touches)
  Motor.Setminduty (50);

  // Regulator PID coefficients
  Motor.kp = 3;// is responsible for the sharpness of regulation.
  // with small values of the signal, there will be no signal at all, with too large ones - it will shake

  Motor.ki = 0.2;// is responsible for correction of errors in the flowe
  Motor.kd = 0.1;// is responsible for compensation for sharp changes
  
  // Install the zone of stopping the motor for the stabilization mode of the position in ticks (according to the silence 8)
  Motor.SetStopzone (10);

  Motor.Setrunmode (Accel_pos);
  // IDLE_RUN - Tick () does not control the motor.Can be used for debugging
  // Accel_pos - Tick () works in a smooth follow -up mode to the target corner
  // pid_pos - tick () operates in a sharp follow -up mode to the target corner
  // Accel_Speed - Tick () works in a smooth maintenance mode (with a given acceleration)
  // pid_Speed - Tick () works in the mode of maintaining speed on PID to the regulator
}

VOID loop () {
  // Potentiometer on A0
  // transform the value of --255 .. 255
  Static Float Val;
  val += (255 - analogread (0) / 2 - val) * 0.3;// Filter

  // for pid_Speed/acceel_Speed
  //motor.SettargetSpeeddeg(val*3);// set targeted speed in degrees/s

  // for PID_POS/Accel_POS mode
  Motor.Settargetdeg (val * 2);// set a new target angle in degrees

  // Mandatory function.Makes all calculations
  // accepts the current value from the encoder or potentiometer
  Motor.tick (Enctick (4));

  static uint32_t tmr = 0;
  if (millis () - tmr> 100) {// Timer for 100ms for graphs
    TMR += 100;
    // debugging position (open Plotter)
    Serial.print (motor.gettargetdeg ());
    Serial.print (',');
    Serial.print (motor.getduut ());
    Serial.print (',');
    Serial.println (Motor.getcurrentdeg ());

    /*
      // Speed debugging (open Plotter)
      Serial.print (Motor.gettargetSpeeddeg ());
      Serial.print (',');
      Serial.print (motor.getduut ());
      Serial.print (',');
      Serial.println (Motor.getSpeeddeg ());
    */
  }
}

// We read the encoder manually through DigitalRead ()
LONG ENCTICK (Byte PIN) {
  Static Bool Laststate;
  Static Long Encounter = 0;
  Bool Curstate = DigitalRead (PIN);// survey
  IfState! = curstate) {// caught the change
    Laststate = Curstate;
    if (curstate) {// on the front
      ENCCOUNTER += MOTOR.GETSTATE ();// remembered the turn
    }
  }
  Return ENCCOUNTER;
}
`` `

<a id="versions"> </a>
## versions
- V1.1 - the algorithm has been improved
- V1.2 - Compatibility with ESP
- V1.3 - small improvements and fixes

<a id="feedback"> </a>
## bugs and feedback
Create ** Issue ** when you find the bugs, and better immediately write to the mail [alex@alexgyver.ru] (mailto: alex@alexgyver.ru)
The library is open for refinement and your ** pull Request ** 'ow!


When reporting about bugs or incorrect work of the library, it is necessary to indicate:
- The version of the library
- What is MK used
- SDK version (for ESP)
- version of Arduino ide
- whether the built -in examples work correctly, in which the functions and designs are used, leading to a bug in your code
- what code has been loaded, what work was expected from it and how it works in reality
- Ideally, attach the minimum code in which the bug is observed.Not a canvas of a thousand lines, but a minimum code
#versions)
[![Foo](https://img.shields.io/badge/Website-AlexGyver.ru-blue.svg?style=flat-square)](https://alexgyver.ru/)
[![Foo](https://img.shields.io/badge/%E2%82%BD$%E2%82%AC%20%D0%9D%D0%B0%20%D0%BF%D0% B8%D0%B2%D0%BE-%D1%81%20%D1%80%D1%8B%D0%B1%D0%BA%D0%BE%D0%B9-orange.svg?style=flat-square )](https://alexgyver.ru/support_alex/)

[![Foo](https://img.shields.io/badge/README-ENGLISH-brightgreen.svg?style=for-the-badge)](https://github-com.translate.goog/GyverLibs/ AccelMotor?_x_tr_sl=ru&_x_tr_tl=en)

#AccelMotor
Library for advanced control and stabilization of a motor with an encoder for Arduino
- Inherits all chips from the GyverMotor library (support for different drivers and modes)
- Speed ​​control mode with feedback
- Rotate to a given angle with feedback
- Adjustable PID controller gains
- Acceleration and speed limit
- The library accepts any type of feedback: encoder, potentiometer, etc.
- Support for gearmotors, encoder gear ratio setting
- The regulator takes into account the "dead zone" of the motor
- All functions work in degrees and ticks of the encoder

### Compatibility
Compatible with all Arduino platforms (using Arduino functions)

### Documentation
The library has [extended documentation](https://alexgyver.ru/accelmotor/)

## Content
- [Install](#install)
- [Initialization](#init)
- [Usage](#usage)
- [Example](#example)
- [Versions](#versions)
- [Bugs and feedback](#feedback)

<a id="install"></a>
## Installation
- The library can be found by the name **AccelMotor** and installed through the library manager in:
    - Arduino IDE
    - Arduino IDE v2
    - PlatformIO
- [Download library](https://github.com/GyverLibs/AccelMotor/archive/refs/heads/main.zip) .zip archive for manual installation:
    - Unzip and put in *C:\Program Files (x86)\Arduino\libraries* (Windows x64)
    - Unpack and put in *C:\ProgramFiles\Arduino\libraries* (Windows x32)
    - Unpack and put in *Documents/Arduino/libraries/*
    - (Arduino IDE) automatic installation from .zip: *Sketch/Include library/Add .ZIP library…* and specify the downloaded archive
- Read more detailed instructions for installing libraries [here] (https://alexgyver.ru/arduino-first/#%D0%A3%D1%81%D1%82%D0%B0%D0%BD%D0%BE% D0%B2%D0%BA%D0%B0_%D0%B1%D0%B8%D0%B1%D0%BB%D0%B8%D0%BE%D1%82%D0%B5%D0%BA)

<a id="init"></a>
## Initialization
```cpp
// initialization is inherited from GyverMotor
// initialization options depending on the driver type:
AccelMotor motor(DRIVER2WIRE, dig_pin, PWM_pin, level);
AccelMotor motor(DRIVER3WIRE, dig_pin_A, dig_pin_B, PWM_pin, level);
AccelMotor motor(RELAY2WIRE, dig_pin_A, dig_pin_B, level);
/*
  DRIVER2WIRE - two-wire driver (direction + PWM)
  DRIVER3WIRE - three-wire driver (two direction pins + PWM)
  RELAY2WIRE - relay as driver (two direction pins)

  dig_pin, dig_pin_A, dig_pin_B - any MK digital pin
  PWM_pin - any PWM pin of the MK
  level - LOW / HIGH - driver level. If the motor slows down when the speed increases, change the level.
*/
```

<a id="usage"></a>
## Usage
```cpp
// controls the motor. Call as often as possible (inside a timer with period dt)
// takes the current position of the motor shaft (according to the encoder count)
// returns true if the motor is still moving towards the target
bool tick(long pos);

// setting the gear ratio of the gearbox and encoder
// example: if the reducer is 1:30 - pass 30 to the function
// example: if the reducer is 1:30 and the encoder is 12 ticks, we send 30*12
void setRatio(float ratio);

// set the period of the regulator (recommended 2-50 milliseconds)
void setDt(int dt);

// setting and getting the current position in encoder ticks and degrees.
// setCurrent(pos) is equivalent to calling tick(pos) and is basically not needed!
void setCurrent(long pos);
long getCurrent();
long getCurrentDeg();

// setting and getting the target position in encoder ticks and grcranberry adusah
void setTarget(long pos);
void setTargetDeg(long pos);
long getTarget();
long getTargetDeg();

// setting the maximum speed in encoder ticks/second and degrees/second
void setMaxSpeed(int speed);
void setMaxSpeedDeg(int speed);

// set encoder acceleration in ticks and degrees per second
void setAcceleration(int accel);
void setAccelerationDeg(int accel);

// set and get the target speed in encoder ticks/second and degrees/second
void setTargetSpeed(int speed);
void setTargetSpeedDeg(int speed);
int getTargetSpeed();
int getTargetSpeedDeg();

// get the current speed in encoder ticks/second and degrees/second
int getSpeed();
int getSpeedDeg();

// get current PWM signal (float from PID controller)
float getDuty();

// manual setting of the operating mode
// IDLE_RUN - tick() does not control the motor. Can be used for debugging
// ACCEL_POS - tick() works in smooth following to the target corner
// PID_POS - tick() works in sharp following to the target corner
// ACCEL_SPEED - tick() works in the mode of smooth speed maintenance (with a given acceleration)
// PID_SPEED - tick() works in PID speed control mode
void setRunMode(AM_runMode);

// returns true if the motor shaft is locked and the signal is on
bool isBlocked();

// PID controller coefficients
// proportional - the aggressiveness of the control depends on it, you need to increase kp
// when the load on the shaft increases, so that the regulator gives a larger control PWM signal
float kp = 2.0; // (default value)

// integral - allows you to level the error over time, has a cumulative effect
float ki = 0.9; // (default value)

// differential. Allows you to slightly smooth out jerks, but with a large value
// he himself becomes the cause of jerks and buildup of the system!
float kd = 0.1; // (default value)

// set motor stop zone for position stabilization mode (default 8)
void setStopZone(int zone);

// set step limitsov/degrees outside of which the motor will be hard shut off for safety. If zero, no limit (default)
void setRange(long min, long max);
void setRangeDeg(long min, long max);

long controlPos = 0; // for debugging
```

<a id="example"></a>
## Example
```cpp
/*
Example of motor control with full bridge driver and potentiometer
For follow-to-position and speed-hold modes
*/
#include "AccelMotor.h"
AccelMotor motor(DRIVER2WIRE, 2, 3, HIGH);

void setup() {
  Serial.begin(9600);
  // use JGA25 motor
  // reducer 1:21.3
  // encoder 12 ticks per revolution
  motor.setRatio(21.3 * 12);

  // integration period (default 20)
  motor.setDt(30); // milliseconds

  // set maximum speed for ACCEL_POS mode
  motor.setMaxSpeedDeg(600); // in degrees/sec
  //motor.setMaxSpeed(400); // in ticks/sec

  // set acceleration for ACCEL_POS mode
  motor.setAccelerationDeg(300); // in degrees/sec/sec
  //motor.setAcceleration(300); // in ticks

  // minimum (modulo) PWM signal (at which the motor starts)
  motor.setMinDuty(50);

  // PID controller coefficients
  motor.kp = 3; // is responsible for the sharpness of the regulation.
  // If the values ​​are small, there will be no signal at all, if the values ​​are too high, it will shake

  motor.ki = 0.2; // is responsible for correcting the error over time
  motor.kd = 0.1; // responsible for compensating for sudden changes
  
  // set the motor stop zone for the position stabilization mode in ticks (default 8)
  motor.setStopZone(10);

  motor.setRunMode(ACCEL_POS);
  // IDLE_RUN - tick() does not control the motor. Can be used for debugging
  // ACCEL_POS - tick() works in smooth following to the target corner
  // PID_POS - tick() works in sharp following to the target corner
  // ACCEL_SPEED - tick() works in the mode of smooth speed maintenance (with a given acceleration)
  // PID_SPEED - tick() works in PID speed control mode
}

void loop() {
  // potentiometer on A0// convert the value to -255.. 255
  static float val;
  val += (255 - analogRead(0) / 2 - val) * 0.3; // filter

  // for PID_SPEED/ACCEL_SPEED mode
  //motor.setTargetSpeedDeg(val*3); // set target speed in degrees/sec

  // for PID_POS/ACCEL_POS mode
  motor.setTargetDeg(val * 2); // set new target angle in degrees

  // required function. Does all the calculations
  // takes the current value from the encoder or potentiometer
  motor.tick(encTick(4));

  static uint32_t tmr = 0;
  if (millis() - tmr > 100) { // 100ms timer for charts
    tmr += 100;
    // debug position (open plotter)
    Serial.print(motor.getTargetDeg());
    Serial print(',');
    Serial.print(motor.getDuty());
    Serial print(',');
    Serial.println(motor.getCurrentDeg());

    /*
      // debug speed (open plotter)
      Serial.print(motor.getTargetSpeedDeg());
      Serial print(',');
      Serial.print(motor.getDuty());
      Serial print(',');
      Serial.println(motor.getSpeedDeg());
    */
  }
}

// read encoder manually via digitalRead()
long encTick(byte pin) {
  static bool lastState;
  static long encCounter = 0;
  bool curState = digitalRead(pin); // poll
  if (lastState != curState) { // caught the change
    lastState = curState;
    if (curState) { // on the front
      encCounter += motor.getState(); // remember the turn
    }
  }
  return encCounter;
}
```

<a id="versions"></a>
## Versions
- v1.1 - improved algorithm
- v1.2 - compatible with esp
- v1.3 - minor improvements and fixes

<a id="feedback"></a>
## Bugs and feedback
When you find bugs, create an **Issue**, or better, immediately write to the mail [alex@alexgyver.ru](mailto:alex@alexgyver.ru)
The library is open for revision and your **Pull Request**'s!
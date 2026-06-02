This is an automatic translation and may be incorrect in some places. See the source README and examples for authoritative information.

[![latest](https://img.shields.io/github/v/release/GyverLibs/AccelMotor.svg?color=brightgreen)](https://github.com/GyverLibs/AccelMotor/releases/latest/download/AccelMotor.zip)
[![PIO](https://badges.registry.platformio.org/packages/gyverlibs/library/AccelMotor.svg)](https://registry.platformio.org/libraries/gyverlibs/AccelMotor)
[![Foo](https://img.shields.io/badge/Website-AlexGyver.ru-blue.svg?style=flat-square)](https://alexgyver.ru/)
[![Foo](https://img.shields.io/badge/%E2%82%BD%24%E2%82%AC%20%D0%9F%D0%BE%D0%B4%D0%B4%D0%B5%D1%80%D0%B6%D0%B0%D1%82%D1%8C-%D0%B0%D0%B2%D1%82%D0%BE%D1%80%D0%B0-orange.svg?style=flat-square)](https://alexgyver.ru/support_alex/)
[![Foo](https://img.shields.io/badge/README-ENGLISH-blueviolet.svg?style=flat-square)](https://github-com.translate.goog/GyverLibs/AccelMotor?_x_tr_sl=ru&_x_tr_tl=en)  

[![Foo](https://img.shields.io/badge/ПОДПИСАТЬСЯ-НА%20ОБНОВЛЕНИЯ-brightgreen.svg?style=social&logo=telegram&color=blue)](https://t.me/GyverLibs)

# AccelMotor
Advanced motor control and stabilization library with encoder for Arduino
- Inherits all chips from the GyverMotor library (support for different drivers and modes)
- Speed mode with feedback
- Turning mode at a given angle with feedback
- Adjustable PID coefficients of the regulator
- Limitation of acceleration and speed
- The library receives any type of feedback: encoder, potentiometer, etc.
- Support for motor-reductors, setting the transmission ratio of the encoder
- The regulator takes into account the dead zone of the engine
- All functions work in degrees and "ticks" of the encoder

### Compatibility
Compatible with all Arduino platforms (Arduino features are used)

### Documentation.
There's a library[extended documentation](https://alexgyver.ru/accelmotor/)

## Contents
- [Installation](#install)
- [Initialization](#init)
- [Use of use](#usage)
- [Example](#example)
- [Versions](#versions)
- [Bugs and feedback](#feedback)

<a id="install"></a>
## Installation
- The library can be found under the name **AccelMotor** and installed through the library manager in:
    - Arduino IDE
    - Arduino IDE v2
    - PlatformIO
- [Download the library](https://github.com/GyverLibs/AccelMotor/archive/refs/heads/main.zip).zip archive for manual installation:
    - Unpack and put in *C:\Program Files (x86)\Arduino\libraries* (Windows x64)
    - Unpack and put in *C:\Program Files\Arduino\libraries* (Windows x32)
    - Unpack and put in *Documents/Arduino/libraries/ *
    - (Arduino IDE) Automatic installation from .zip: *Sketch/Connect library/Add .ZIP library...* and specify downloaded archive
- Read more detailed instructions for installing libraries[here](https://alexgyver.ru/arduino-first/#%D0%A3%D1%81%D1%82%D0%B0%D0%BD%D0%BE%D0%B2%D0%BA%D0%B0_%D0%B1%D0%B8%D0%B1%D0%BB%D0%B8%D0%BE%D1%82%D0%B5%D0%BA)
### Update
- I recommend always updating the library: new versions fix errors and bugs, as well as optimize and add new features.
- Through the library manager IDE: find the library as when installing and click "Update"
- Manually: **Delete the folder with the old version** and then put the new one in its place. “Replacement” can not be done: sometimes new versions delete files that will remain when replaced and can lead to errors!

<a id="init"></a>
## Initialization
```cpp
// Initialization inherited from GyverMotor
// Initialization options depending on the type of driver:
AccelMotor motor(DRIVER2WIRE, dig_pin, PWM_pin, level);
AccelMotor motor(DRIVER3WIRE, dig_pin_A, dig_pin_B, PWM_pin, level);
AccelMotor motor(RELAY2WIRE, dig_pin_A, dig_pin_B, level);
/*
  DRIVER2WIRE - двухпроводной драйвер (направление + ШИМ)
  DRIVER3WIRE - трёхпроводной драйвер (два пина направления + ШИМ)
  RELAY2WIRE - реле в качестве драйвера (два пина направления)

  dig_pin, dig_pin_A, dig_pin_B - любой цифровой пин МК
  PWM_pin - любой ШИМ пин МК
  level - LOW / HIGH - уровень драйвера. Если при увеличении скорости мотор наоборот тормозит - смени уровень
*/
```

<a id="usage"></a>
## Use of use
```cpp
// It controls the engine. Call as often as possible (internal timer with period dt)
// takes the current position of the motor shaft (according to the encoder)
// returns true if the motor is still moving
bool tick(long pos);

// gearing
// Example: if the gearbox is 1:30, transmit to the function 30
// Example: if the reducer 1:30 and encoder for 12 ticks - transmit 30 *12
void setRatio(float ratio);	

// setting the regulator period (recommended 2-50 milliseconds)
void setDt(int dt);

// setting and obtaining the current position in encoder tics and degrees.
// SetCurrent(pos) is tantamount to calling a tick(pos) and is not required in principle!
void setCurrent(long pos);
long getCurrent();
long getCurrentDeg();

// setting and obtaining a target position in encoder tics and degrees
void setTarget(long pos);
void setTargetDeg(long pos);
long getTarget();
long getTargetDeg();

// setting the maximum speed in encoder tics/second and degrees/second
void setMaxSpeed(int speed);
void setMaxSpeedDeg(int speed);

// acceleration of encoder tics and degrees per second
void setAcceleration(int accel);
void setAccelerationDeg(int accel);

// setting and obtaining the target speed in encoder tics/second and degrees/second
void setTargetSpeed(int speed);
void setTargetSpeedDeg(int speed);
int getTargetSpeed();
int getTargetSpeedDeg();

// get the current speed in encoder tics/second and degrees/second
int getSpeed();
int getSpeedDeg();	

// receive the current PWM signal (float from the PID regulator)
float getDuty();

// manual
// IDLE RUN - Tick() does not control the motor. Can be used for debugging
// ACCEL POS - tick() runs smoothly to target corner
// PID POS - Tick() operates in a sharp path to the target corner
// ACCEL SPEED - tick() operates in smooth speed maintenance mode (with a specified acceleration)
// PID SPEED - tick() operates in the mode of maintaining the speed of the PID regulator
void setRunMode(AM_runMode mode);

// returns true if the motor shaft is blocked and the signal is given
bool isBlocked();

// regulator
// proportional - the aggressiveness of management depends on it, you need to increase kp
// when increasing the load on the shaft, so that the regulator gives a larger PWM control signal
float kp = 2.0;		// (default)

// Integral - allows you to level the error over time, has a cumulative effect
float ki = 0.9;		// (default)

// differential. Allows you to smooth out the jerks a little, but with great importance
// It itself becomes a cause of jerks and swaying of the system!
float kd = 0.1;		// (default)

// set the stop zone of the motor for stabilization of the position (in silence). (8)
void setStopZone(int zone);

// set the limits of steps/degrees beyond which the motor will be rigidly disabled for safety. If there are zeros, there is no limit (by omission).
void setRange(long min, long max);
void setRangeDeg(long min, long max);

long controlPos = 0;	// debug
```

<a id="example"></a>
## Example
```cpp
/*
	Пример управления мотором при помощи драйвера полного моста и потенциометра
	Для режимов следования к позиции и удержания скорости
*/
#include "AccelMotor.h"
AccelMotor motor(DRIVER2WIRE, 2, 3, HIGH);

void setup() {
  Serial.begin(9600);
  // Using the JGA25 engine
  // reducer 1:21.3
  // encoder 12 tics per turn
  motor.setRatio(21.3 * 12);

  // Integration period (by omission 20)
  motor.setDt(30);  // milliseconds

  // Maximum speed setting for ACCEL POS
  motor.setMaxSpeedDeg(600);  // degrees/sec
  //motor.setMaxSpeed(400); // in ticks/sec

  // Acceleration setting for ACCEL POS
  motor.setAccelerationDeg(300);  // degrees/sec/sec
  //motor.setAcceleration(300); // in ticks

  // Minimum (module) PWM signal (at which the motor starts)
  motor.setMinDuty(50);

  // regulator
  motor.kp = 3;   // It is responsible for the harshness of regulation.
  // At small values, the signal will not be at all, at too large - it will shake.

  motor.ki = 0.2; // Responsible for correcting the error over time
  motor.kd = 0.1; // Responsible for compensation for sudden changes
  
  // set the stop zone of the motor for stabilization of the position in ticks (by default). (8)
  motor.setStopZone(10);

  motor.setRunMode(ACCEL_POS);
  // IDLE RUN - Tick() does not control the motor. Can be used for debugging
  // ACCEL POS - tick() runs smoothly to target corner
  // PID POS - Tick() operates in a sharp path to the target corner
  // ACCEL SPEED - tick() operates in smooth speed maintenance mode (with a specified acceleration)
  // PID SPEED - tick() operates in the mode of maintaining the speed of the PID regulator
}

void loop() {
  // potentiometer
  // Turn the value to -255. 255
  static float val;
  val += (255 - analogRead(0) / 2 - val) * 0.3; // filter

  // PID SPEED/ACCEL SPEED mode
  //motor.setTargetSpeedDeg(val*3) // set the target speed in degrees/sec

  // PID POS/ACCEL POS
  motor.setTargetDeg(val * 2);  // set a new target angle in degrees

  // mandatory function. Doing all the calculations.
  // takes the current value from an encoder or potentiometer
  motor.tick(encTick(4));

  static uint32_t tmr = 0;
  if (millis() - tmr > 100) {   // 100ms timer for charts
    tmr += 100;
    // Position debugging (open plotter)
    Serial.print(motor.getTargetDeg());
    Serial.print(',');
    Serial.print(motor.getDuty());
    Serial.print(',');
    Serial.println(motor.getCurrentDeg());

    /*
      // speed debugging (open plotter)
      Serial.print(motor.getTargetSpeedDeg());
      Serial.print(',');
      Serial.print(motor.getDuty());
      Serial.print(',');
      Serial.println(motor.getSpeedDeg());
    */
  }
}

// Read the encoder manually through digitalRead()
long encTick(byte pin) {
  static bool lastState;
  static long encCounter = 0;
  bool curState = digitalRead(pin);       // survey
  if (lastState != curState) {            // catch
    lastState = curState;
    if (curState) {                       // frontward
      encCounter += motor.getState();     // remember the turn
    }
  }
  return encCounter;
}
```

<a id="versions"></a>
## Versions
- v1.1 - improved algorithm
- v1.2 - compatibility with esp
- v1.3 - small improvements and fixes

<a id="feedback"></a>
## Bugs and feedback
If you find bugs, create **Issue**, or better write to the mail immediately.[alex@alexgyver.ru](mailto:alex@alexgyver.ru)  
The library is open for revision and your **Pull Requests*!

When reporting bugs or incorrect work of the library, it is necessary to specify:
- Library version
- What is used by the IC
- SDK version (for ESP)
- Arduino IDE version
- Are embedded examples that use features and designs that cause bugs in your code working correctly?
- What code was downloaded, what work was expected from it and how it works in reality
- Ideally, attach the minimum code in which the bug is observed. Not a canvas of a thousand lines, but a minimum code.

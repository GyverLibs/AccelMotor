[![latest](https://img.shields.io/github/v/release/GyverLibs/AccelMotor.svg?color=brightgreen)](https://github.com/GyverLibs/AccelMotor/releases/latest/download/AccelMotor.zip)
[![Foo](https://img.shields.io/badge/Website-AlexGyver.ru-blue.svg?style=flat-square)](https://alexgyver.ru/)
[![Foo](https://img.shields.io/badge/%E2%82%BD$%E2%82%AC%20%D0%9D%D0%B0%20%D0%BF%D0%B8%D0%B2%D0%BE-%D1%81%20%D1%80%D1%8B%D0%B1%D0%BA%D0%BE%D0%B9-orange.svg?style=flat-square)](https://alexgyver.ru/support_alex/)
[![Foo](https://img.shields.io/badge/README-ENGLISH-blueviolet.svg?style=flat-square)](https://github-com.translate.goog/GyverLibs/AccelMotor?_x_tr_sl=ru&_x_tr_tl=en)  

[![Foo](https://img.shields.io/badge/ПОДПИСАТЬСЯ-НА%20ОБНОВЛЕНИЯ-brightgreen.svg?style=social&logo=telegram&color=blue)](https://t.me/GyverLibs)

# AccelMotor
Библиотека для расширенного управления и стабилизации мотора с энкодером для Arduino
- Наследует все фишки из библиотеки GyverMotor (поддержка разных драйверов и режимов)
- Режим поддержания скорости с обратной связью
- Режим поворота на заданный угол с обратной связью
- Настраиваемые коэффициенты PID регулятора
- Ограничение ускорения и скорости
- Библиотека принимает любой тип обратной связи: энкодер, потенциометр, и т.д.
- Поддержка мотор-редукторов, настройка передаточного отношения энкодера
- Регулятор учитывает "мёртвую зону" мотора
- Все функции работают в градусах и "тиках" энкодера

### Совместимость
Совместима со всеми Arduino платформами (используются Arduino-функции)

### Документация
К библиотеке есть [расширенная документация](https://alexgyver.ru/accelmotor/)

## Содержание
- [Установка](#install)
- [Инициализация](#init)
- [Использование](#usage)
- [Пример](#example)
- [Версии](#versions)
- [Баги и обратная связь](#feedback)

<a id="install"></a>
## Установка
- Библиотеку можно найти по названию **AccelMotor** и установить через менеджер библиотек в:
    - Arduino IDE
    - Arduino IDE v2
    - PlatformIO
- [Скачать библиотеку](https://github.com/GyverLibs/AccelMotor/archive/refs/heads/main.zip) .zip архивом для ручной установки:
    - Распаковать и положить в *C:\Program Files (x86)\Arduino\libraries* (Windows x64)
    - Распаковать и положить в *C:\Program Files\Arduino\libraries* (Windows x32)
    - Распаковать и положить в *Документы/Arduino/libraries/*
    - (Arduino IDE) автоматическая установка из .zip: *Скетч/Подключить библиотеку/Добавить .ZIP библиотеку…* и указать скачанный архив
- Читай более подробную инструкцию по установке библиотек [здесь](https://alexgyver.ru/arduino-first/#%D0%A3%D1%81%D1%82%D0%B0%D0%BD%D0%BE%D0%B2%D0%BA%D0%B0_%D0%B1%D0%B8%D0%B1%D0%BB%D0%B8%D0%BE%D1%82%D0%B5%D0%BA)
### Обновление
- Рекомендую всегда обновлять библиотеку: в новых версиях исправляются ошибки и баги, а также проводится оптимизация и добавляются новые фичи
- Через менеджер библиотек IDE: найти библиотеку как при установке и нажать "Обновить"
- Вручную: **удалить папку со старой версией**, а затем положить на её место новую. "Замену" делать нельзя: иногда в новых версиях удаляются файлы, которые останутся при замене и могут привести к ошибкам!


<a id="init"></a>
## Инициализация
```cpp
// инициализация наследуется от GyverMotor
// варианты инициализации в зависимости от типа драйвера:
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
## Использование
```cpp
// управляет мотором. Вызывать как можно чаще (внутри таймер с периодом dt)
// принимает текущее положение вала мотора (по счёту энкодера)
// возвращает true если мотор всё ещё движется к цели
bool tick(long pos);

// установка передаточного отношения редуктора и энкодера
// пример: если редуктор 1:30 - передаём в функцию 30
// пример: если редуктор 1:30 и энкодер на 12 тиков - передаём 30*12
void setRatio(float ratio);	

// установка периода регулятора (рекомендуется 2-50 миллисекунд)
void setDt(int dt);

// установка и получение текущей позиции в тиках энкодера и градусах.
// setCurrent(pos) равносильна вызову tick(pos) и в принципе не нужна!
void setCurrent(long pos);
long getCurrent();
long getCurrentDeg();

// установка и получение целевой позиции в тиках энкодера и градусах
void setTarget(long pos);
void setTargetDeg(long pos);
long getTarget();
long getTargetDeg();

// установка максимальной скорости в тиках энкодера/секунду и градусах/секунду
void setMaxSpeed(int speed);
void setMaxSpeedDeg(int speed);

// установка ускорения тиках энкодера и градусах в секунду
void setAcceleration(int accel);
void setAccelerationDeg(int accel);

// установка и получение целевой скорости в тиках энкодера/секунду и градусах/секунду
void setTargetSpeed(int speed);
void setTargetSpeedDeg(int speed);
int getTargetSpeed();
int getTargetSpeedDeg();

// получить текущую скорость в тиках энкодера/секунду и градусах/секунду
int getSpeed();
int getSpeedDeg();	

// получить текущий ШИМ сигнал (float из ПИД регулятора)
float getDuty();

// ручная установка режима работы
// IDLE_RUN - tick() не управляет мотором. Может использоваться для отладки
// ACCEL_POS - tick() работает в режиме плавного следования к целевому углу
// PID_POS - tick() работает в режиме резкого следования к целевому углу
// ACCEL_SPEED - tick() работает в режиме плавного поддержания скорости (с заданным ускорением)
// PID_SPEED - tick() работает в режиме поддержания скорости по ПИД регулятору
void setRunMode(AM_runMode mode);

// возвращает true, если вал мотора заблокирован, а сигнал подаётся
bool isBlocked();

// коэффициенты ПИД регулятора
// пропорциональный - от него зависит агрессивность управления, нужно увеличивать kp
// при увеличении нагрузки на вал, чтобы регулятор подавал больший управляющий ШИМ сигнал
float kp = 2.0;		// (знач. по умолчанию)

// интегральный - позволяет нивелировать ошибку со временем, имеет накопительный эффект
float ki = 0.9;		// (знач. по умолчанию)

// дифференциальный. Позволяет чуть сгладить рывки, но при большом значении
// сам становится причиной рывков и раскачки системы!
float kd = 0.1;		// (знач. по умолчанию)

// установить зону остановки мотора для режима стабилизации позиции (по умолч. 8)
void setStopZone(int zone);

// установить пределы шагов/градусов, вне которых мотор будет жёстко отключен для безопасности. Если по нулям, ограничения нет (по умолч.)
void setRange(long min, long max);
void setRangeDeg(long min, long max);

long controlPos = 0;	// для отладки
```

<a id="example"></a>
## Пример
```cpp
/*
	Пример управления мотором при помощи драйвера полного моста и потенциометра
	Для режимов следования к позиции и удержания скорости
*/
#include "AccelMotor.h"
AccelMotor motor(DRIVER2WIRE, 2, 3, HIGH);

void setup() {
  Serial.begin(9600);
  // использую мотор JGA25
  // редуктор 1:21.3
  // энкодер 12 тиков на оборот
  motor.setRatio(21.3 * 12);

  // период интегрирования (по умолч. 20)
  motor.setDt(30);  // миллисекунды

  // установка максимальной скорости для режима ACCEL_POS
  motor.setMaxSpeedDeg(600);  // в градусах/сек
  //motor.setMaxSpeed(400); // в тиках/сек

  // установка ускорения для режима ACCEL_POS
  motor.setAccelerationDeg(300);  // в градусах/сек/сек
  //motor.setAcceleration(300);  // в тиках

  // минимальный (по модулю) ШИМ сигнал (при котором мотор трогается)
  motor.setMinDuty(50);

  // коэффициенты ПИД регулятора
  motor.kp = 3;   // отвечает за резкость регулирования.
  // При малых значениях сигнала вообще не будет, при слишком больших – будет трясти

  motor.ki = 0.2; // отвечает за коррекцию ошибки в течение времени
  motor.kd = 0.1; // отвечает за компенсацию резких изменений
  
  // установить зону остановки мотора для режима стабилизации позиции в тиках (по умолч. 8)
  motor.setStopZone(10);

  motor.setRunMode(ACCEL_POS);
  // IDLE_RUN - tick() не управляет мотором. Может использоваться для отладки
  // ACCEL_POS - tick() работает в режиме плавного следования к целевому углу
  // PID_POS - tick() работает в режиме резкого следования к целевому углу
  // ACCEL_SPEED - tick() работает в режиме плавного поддержания скорости (с заданным ускорением)
  // PID_SPEED - tick() работает в режиме поддержания скорости по ПИД регулятору
}

void loop() {
  // потенциометр на А0
  // преобразуем значение в -255.. 255
  static float val;
  val += (255 - analogRead(0) / 2 - val) * 0.3; // фильтор

  // для режима PID_SPEED/ACCEL_SPEED
  //motor.setTargetSpeedDeg(val*3);  // задаём целевую скорость в градусах/сек

  // для режима PID_POS/ACCEL_POS
  motor.setTargetDeg(val * 2);  // задаём новый целевой угол в градусах

  // обязательная функция. Делает все вычисления
  // принимает текущее значение с энкодера или потенциометра
  motor.tick(encTick(4));

  static uint32_t tmr = 0;
  if (millis() - tmr > 100) {   // таймер на 100мс для графиков
    tmr += 100;
    // отладка позиции (открой плоттер)
    Serial.print(motor.getTargetDeg());
    Serial.print(',');
    Serial.print(motor.getDuty());
    Serial.print(',');
    Serial.println(motor.getCurrentDeg());

    /*
      // отладка скорости (открой плоттер)
      Serial.print(motor.getTargetSpeedDeg());
      Serial.print(',');
      Serial.print(motor.getDuty());
      Serial.print(',');
      Serial.println(motor.getSpeedDeg());
    */
  }
}

// читаем энкодер вручную, через digitalRead()
long encTick(byte pin) {
  static bool lastState;
  static long encCounter = 0;
  bool curState = digitalRead(pin);       // опрос
  if (lastState != curState) {            // словили изменение
    lastState = curState;
    if (curState) {                       // по фронту
      encCounter += motor.getState();     // запомнили поворот
    }
  }
  return encCounter;
}
```

<a id="versions"></a>
## Версии
- v1.1 - улучшен алгоритм
- v1.2 - совместимость с esp
- v1.3 - небольшие улучшения и фиксы

<a id="feedback"></a>
## Баги и обратная связь
При нахождении багов создавайте **Issue**, а лучше сразу пишите на почту [alex@alexgyver.ru](mailto:alex@alexgyver.ru)  
Библиотека открыта для доработки и ваших **Pull Request**'ов!


При сообщении о багах или некорректной работе библиотеки нужно обязательно указывать:
- Версия библиотеки
- Какой используется МК
- Версия SDK (для ESP)
- Версия Arduino IDE
- Корректно ли работают ли встроенные примеры, в которых используются функции и конструкции, приводящие к багу в вашем коде
- Какой код загружался, какая работа от него ожидалась и как он работает в реальности
- В идеале приложить минимальный код, в котором наблюдается баг. Не полотно из тысячи строк, а минимальный код

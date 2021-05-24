![License: MIT](https://img.shields.io/badge/License-MIT-green.svg)
![author](https://img.shields.io/badge/author-AlexGyver-informational.svg)
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
К библиотеке есть расширенная [расширенная документация](https://alexgyver.ru/accelmotor/)

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

<a id="init"></a>
## Инициализация
'''
// kek
'''

<a id="usage"></a>
## Использование

<a id="example"></a>
## Пример

<a id="versions"></a>
## Версии

<a id="feedback"></a>
## Баги и обратная связь
При нахождении багов создавайте **Issue**, а лучше сразу пишите на почту [alex@alexgyver.ru](mailto:alex@alexgyver.ru)  
Библиотека открыта для доработки и ваших **Pull Request**'ов!
# Программное обеспечение

## Среда разработки (IDE)

Windows:
 - Atmel AVRStudio - интегрированная среда разработки (IDE) для разработки 8-ми и 32-х битных AVR приложений. IDE + ассемблер + отладчик.
 - VSCode
 - Eclipse

Для разработки под Windows можно использовать AVRStudio от производителя. Также можно настроить VSCode: [шаблон проекта AVR на C для VSCODE под Windows](https://github.com/nf-zvv/AVR_project)

Linux:
 - Geany
 - VSCode
 - Eclipse

## Симуляторы

 - Proteus - САПР электронных схем. Мощнейший симулятор электрических цепей, компонентов, включая различные МК и др. периферийное оборудование.
 - SimulIDE - это кроссплатформенная среда для моделирования цифровых схем, в том числе содержащих микроконтроллеры.

## Программы для черчения электрических схем

 - Kicad - САПР сквозного проектирования, позволяющая создавать профессиональные электрические схемы и разрабатывать для них печатные платы. Редактор eeschema позволяет создавать многолистовые иерархические схемы и проводить их проверку на соответствие электрическим правилам. На русском языке. Бесплатная. Open Source.
 - Eagle - Популярное ПО для черчения электрических схем и последующей автотрассировки печатных плат. Есть бесплатная версия с ограничениями.
 - Altium Designer - комплексная система автоматизированного проектирования электронных модулей на базе печатных плат, которая позволяет выполнять полный спектр проектных задач: от создания концепции функционирования до выпуска полного комплекта конструкторских и производственных данных.
 - DipTrace - Система автоматизированного сквозного проектирования электрических схем и разводки печатных плат. ПО состоит из модулей: Schematic (для создания многолистовых многоуровневых схем с встроенным простейшим симулятором) и PCB Layout. Программа полностью на русском языке. Распространение: Freeware (бесплатная с ограничениями) и Shareware (платная)
 - sPlan - Одна из наиболее популярных в России программ для черчения электрических принципиальных схем. Для sPlan существует большое количество дополнений и библиотек с Российскими радиоэлементами.

## Компиляция программ на языке C

Для компиляции программ на языке C для микроконтроллеров AVR используется GNU AVR toolchain, или AVR GCC toolchain.

Тулчейн AVR GCC для Windows можно скачать, например, [здесь](https://blog.zakkemble.net/avr-gcc-builds/)

Для компиляции файла `main.c` в зависимости от типа используемого микроконтроллера команды будут отличаться аргументом `-mmcu`.

Для микроконтроллера `ATmega16u4` используется команда:

```
avr-gcc -mmcu=atmega16u4 -Wall -g -Os -o main.o main.c
```

Для микроконтроллера `AT90USB162`:

```
avr-gcc -mmcu=at90usb162 -Wall -g -Os -o main.o main.c
```

После компиляции получается объектный файл `main.o`. Для прошивки необходим машинный код в формате Intel HEX.

Генерация HEX:

```
avr-objcopy -j .text -j .data -O ihex main.o main.hex
```

На выходе получается файл `main.hex`, который используется далее для прошивки микроконтроллера.

Узнать размер прошивки:

```
avr-size -C main.o
```

 ## Компиляция программ на языке Assembler

Под Windows для компиляции программ на языке Ассемблер используется компилятор в составе среды разработки AVRStudio.

Под Linux используется компилятор `avra`.


## Прошивка микроконтроллера

Для загрузки прошивки в микроконтроллер `ATmega16u4` использовать команду:

```
avrdude -c flip1 -p m16u4 -U flash:w:main.hex:i
```

где `main.hex` - имя файла прошивки.

Для прошивки микроконтроллера `AT90USB162`:

```
avrdude -c flip1 -p usb162 -U flash:w:main.hex:i
```

## Make и Makefile

Чтобы каждый раз не вводить несколько команд для компиляции программы удобно использовать Makefile с набором правил сборки.

Примеры Makefile'ов для разных микроконтроллеров приведены ниже:

 - [AT90USB162](Makefile.usb162);
 - [ATmega16U4](Makefile.m16u4);
 - [ATmega128rfa1](Makefile.m128rfa1).

Файл необходимо переименовать в `Makefile` (без расширения) и положить в папку с проектом.

Компиляция осуществляется из дирректории проекта командой `make` или через IDE  Geany:  Menu -> Build -> Make Custom Target, в появившемся окне набрать `all`, нажать Ok.

Загрузка программы на МК (прошивка) осуществляется командой  `make flush`  или через IDE  Geany:  Menu -> Build -> Make Custom Target, в появившемся окне набрать `flush`, нажать Ok.


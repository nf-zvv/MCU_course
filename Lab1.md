# Лабораторная работа №1

## Цель

Изучение базовых возможностей оборудования и среды программирования. Работа с портами ввода-вывода.

## Оборудование

1. Миниблок микроконтроллера AT90USB162
2. Вспомогательный миниблок с четырьмя светодиодами
3. Вспомогательный миниблок с семисегментным светодиодным индикатором
4. Провода соединительные
5. Кабель USB - miniUSB
6. Блок БИЦУ (наборное поле с источником питания)
7. ПК с ОС GNU/Linux Ubuntu

## Теоретические сведения

### Представление чисел

В программе можно использовать числа в десятичной, двоичной и шестнадцатеричной формах записи. Для указания компилятору в какой системе счисления представлено число добавляется префикс `0b` для двоичных чисел или `0x` для шестнадцатеричных чисел. Десятичные числа указываются без префикса.

Примеры:
 - `0b01000001` - двоичное число;
 - `129` - десятичное число;
 - `0xA8` - шестнадцатеричное число.

### Типы данных

Для 8-битного микроконтроллера (AVR как раз относится к 8-битным) самым предпочтительным типом данных для переменной будет `char` или `unsigned char`.

Тип `char` (или `int8_t`) - знаковый (самый старший бит 8-битного значения хранит знак числа), и он может кодировать ряд значений от -128 до +127 в дополнительном коде.

Тип `unsigned char` (или `uint8_t`) - беззнаковый - может хранить положительные числа в диапазоне от 0 до 255.

Кроме того, компилятором `avr-gcc` поддерживаются ещё следующие типы данных:

 - `int` или `int16_t` - 16-битная переменная со знаком;
 - `unsigned` или `int uint16_t` - 16-битная переменная без знака;
 - `long` или `int32_t` - 32-битная переменная со знаком;
 - `unsigned` или `long uint32_t` - 32-битная переменная без знака;
 - `long long` или `int64_t` - 64-битная переменная со знаком;
 - `unsigned long long` или `uint64_t` -  64-битная переменная без знака.

Обратите внимание, что здесь в имени типа сразу закодирована его разрядность. Это очень удобно, потому что не надо помнить, какая разрядность у какого типа.

### Порты ввода/вывода

Порт ввода/вывода используется в микроконтроллерах при обмене данными с внешним аппаратным обеспечением.

В микроконтроллерах AVR используются двунаправленные порты ввода/вывода, выводы которых также могут иметь альтернативные функции (служить входами АЦП, выводами интерфейсов UART, SPI, TWI, выходами PWM и т.д.). По умолчанию, выводы портов работают в режиме ввода/вывода данных.

Для работы с портами существуют три регистра на каждый порт: `DDRx`, `PORTx`, `PINx`, где _х_ – имя порта (например, для порта `A` это регистры `DDRA`, `PORTA`, `PINA`).

`DDRx` (Data Direction Register) - регистр, отвечающий за направление передачи данных каждой линии порта _x_: 1 – выход, 0 - вход. Регистр `DDRx` содержит 8 бит `DDx0...DDx7`, каждый из которых отвечает за свою, совпадающую по номеру линию порта `Px0...Px7`.

`PORTx` (The Port X Data Register) - регистр данных порта _x_. Принцип работы с этим регистром зависит от того, в каком режиме (входа или выхода) работает линия порта:

 - если линия порта работает на _выход_, то биты данного регистра управляют состоянием выходной линии;
 - если линия порта работает на _вход_, то биты данного регистра отвечают за подключение к линии внутреннего подтягивающего резистора (т.е. сопротивления, подтягивающего напряжение линии к напряжению питания).

Режимом по умолчанию является режим входа при отключенных подтягивающих резисторах (вход Hi-Z или режим высокоимпендансного входа).

В режиме работы порта на вывод каждый бит регистра `PORTx` отвечает за свой физический вывод (ножку) микроконтроллера.

### Битовые операции

При выполнении следующей команды в PORTA будет записана 1 в позицию PA2, остальные биты будут обнулены

```
PORTA = 1<<PA2;
```

Данная запись аналогична:

```
PORTA = 0b00000100;
```

Для изменения отдельных битов в регистре, не затрагивая остальных необходимо использовать поразрядные (битовые) операции `AND (&)`, `OR (|)`, `NOT (~)`, `XOR (^)`.

> [!NOTE]  
> Не путать с логическими операциями типа `&&`, `||`.

Например:

```C
// Установить бит в позиции PA2
PORTA |= (1<<PA2);

// Сбросить (обнулить) бит в позиции PA2
PORTA &= ~(1<<PA2);

// Инвертировать бит в позиции PA2
PORTA ^= (1<<PA2);
```

Записи типа `(1<<PA2)` означает битовую маску, например, `0b00000100`.

Записи типа `~(1<<PA2)` означает поразрядную инверсию битовой маски, т.е.: `0b11111011`.

Подробно:

```C
// Установить бит в позиции PA2
PORTA = PORTA | (1<<PA2);

// Сбросить (обнулить) бит в позиции PA2
PORTA = PORTA & ~(1<<PA2);

// Инвертировать бит в позиции PA2
PORTA = PORTA ^ (1<<PA2);
```

### Временная задержка

Для возможности использования функций временной задержки необходимо подключить библиотеку для работы с задержками, а также задать тактовую частоту микроконтроллера:

```C
#define F_CPU 8000000UL // Директива установки частоты микроконтроллера
#include <util/delay.h> // Библиотека для работы с временной задержкой
```

Доступны две функции для формирования задержки - в микросекундах и миллисекундах:

```C
_delay_us(double mircoseconds);
_delay_ms(double milliseconds);
```

## Ход работы

1. Подключите блок БИЦУ к источнику питания
2. Подключите миниблок с микроконтроллером AT90USB162 к компьютеру, используя кабель miniUSB
3. Установите миниблок с микроконтроллером AT90USB162 на наборное поле блока БИЦУ
4. Соберите схему в соответствии с рекомендуемой схемой соединений
5. В программе Geany создайте новый проект

В папке `/home/student/workspace` создать подпапку со своей Фамилией . В ней создать папку с лабораторной работой №1.

> [!NOTE] 
> 1. каждую лабораторную работу хранить в отдельной папке.
> 2. в качестве названий папок рекомендуется использовать символы латинского алфавита.

Собрать электрическую цепь по схеме, показанной на рисунке ниже:

![Lab 1 scheme](/img/lab1.png)

Написать программу на языке Си. Код программы для управления светодиодом, подключенным к линии `PB0` представлен в листинге 1.1. Для успешной компиляции файл с кодом необходимо назвать `main.c`.

Листинг 1.1:

```C
#define F_CPU 8000000UL // Директива установки частоты микроконтроллера

#include <avr/io.h> // Библиотека для работы с портами ввода-вывода
#include <util/delay.h> // Библиотека для работы с временной задержкой

int main ()
{
    // Настройка линии PB0 на выход
    DDRB |= (1 << PB0);
    // Главный цикл работы МК
    while (1)
    {
        // Включение напряжения на выводе PB0
        PORTB |= (1 << PB0);
        _delay_ms(500); // Пауза 500 мс (0.5 с)
        // Отключение напряжения на выводе PB0
        PORTB &= ~(1 << PB0);
        _delay_ms(500);
    }
}
```

Скомпилировать полученный код (см. [Компиляция](Software.md#компиляция-программ-на-языке-c) или с помощью [Makefile](Software.md#make-и-makefile)).

Перевести микроконтроллер в режим программирования: одновременно нажать кнопки "СБРОС" и "ПРОГ", отпустить "СБРОС", затем отпустить "ПРОГ". Микроконтроллер перейдет в режим программирования (DFU) и будет ожидать прошивку.

Выполнить загрузку программы в микроконтроллер (см. [Прошивка](Software.md#прошивка-микроконтроллера) или с помощью [Makefile](Software.md#make-и-makefile)).

Нажать кнопку "СБРОС", микроконтроллер перейдет в рабочий режим.

Светодиод будет мигать с интервалом 500 мс. 

> [!NOTE]  
> Если интервал мигания больше, чем 500 мс, требуется запрограммировать фьюзы.

## Задания для самостоятельной работы

1. Собрать схему и написать программу, реализующие «Бегущий огонек».
2. Собрать схему и написать программу, реализующие «Бинарные часы». Использовать только секунды и минуты.
3. Собрать схему и написать программу, реализующие «Семисегментный числовой индикатор». Индикатор должен бесконечно вести отсчет от 0 до 9.

## Содержание отчета

1. Титульный лист
2. Цель, задание на лабораторную работу
3. Электрическая схема
4. Алгоритм работы
5. Заключение
6. Приложения

## Контрольные вопросы

1. Как осуществляется программирование микроконтроллера AT90USB162 на миниблоке?
2. Какой регистр отвечает за направление данных порта?
3. Какой регистр отвечает за установку значения на порту?
4. Как установить бит в регистре?
5. Как сбросить бит в регистре?
6. Как инвертировать бит в регистре?
7. На какой частоте работает микроконтрллер? Какова максимальная частота? Как ее поменять?

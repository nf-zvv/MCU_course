# Жидкокристаллический индикатор

## Цель

Изучить работу жидкокристаллического индикатора (ЖКИ).

## Оборудование

1. Миниблок микроконтроллера ATmega16
2. Семисегментный светодиодный индикатор
3. Провода соединительные
4. Кабель USB - miniUSB
5. Блок БИЦУ (наборное поле с источником питания)
6. ПК с ОС GNU/Linux Ubuntu

## Теоретические сведения

Типовой алфавитно-цифровой ЖКИ дисплей содержит от 1 до 4 строк, в каждой из которых от 8 до 40 символов. Каждое знакоместо на дисплее представляет собой матрицу размером 5×8 точек. В основе индикатора лежит контроллер HD44780 или его аналог (индикатор 40×4 содержит два таких контроллера). Интерфейс подключения к микроконтроллеру состоит из следующих линий:

 - Шина данных DB0-DB7. Шина двунаправленная, 8-разрядная, но для сокращения количества линий для обмена данными можно использовать только старшие 4 разряда DB4-DB7.
 - Сигнал выбора регистра RS. При RS = 0 выполняется запись в регистр команд и считывание регистров состояния. При RS = 1 для чтения или записи выбирается регистр данных.
 - Сигнал чтения/записи R/W. Когда R/W = 0, выполняется запись, если R/W = 1 - чтение.
 - Сигнал стробирования E. По этому сигналу осуществляется операция записи или чтения. Сигнал прямой, т.е. активный уровень у него лог. 1.

Все три управляющих сигнала (RS, R/W, E) генерирует микроконтроллер, который управляет дисплеем. Порядок обмена таков. Для записи:

1. Выбрать регистр (команд или данных) сигналом RS;
2. Задать режим записи R/W = 0;
3. Вывести данные на шину данных;
4. Установить строб E = 1;
5. Снять строб E = 0;
6. Перевести шину данных в отключенное состояние (как вариант, переключить ее на ввод).

Чтение выполняется похожим образом:
1. Выбрать регистр (команд или данных) сигналом RS;
2. Установить режим чтения R/W = 1;
3. Установить строб E = 1;
4. Считать данные с шины данных;
5. Снять строб E = 0.

В случае обмена по усеченной 4-битной шине принцип тот же, но данные выставляются или считываются тетрадами (полубайтами), а сигнал строба выставляется дважды. Вначале записывается или считывается старшая тетрада, затем младшая.

Следует учитывать, что индикатор не сможет работать в том же темпе, что и современный быстрый микроконтроллер, поэтому при выставлении сигналов необходимо задавать задержки. Временные диаграммы передачи данных по 4-разрядной шине показаны на рис. 1.

![LCD timings](/img/lcd_timings.png)

Рис. 1. Последовательность передачи данных в HD44780 по 4-разрядной шине команд/данных


## Ход работы


## Задания для самостоятельной работы


## Содержание отчета

1. Титульный лист
2. Цель, задание на лабораторную работу
3. Электрическая схема
4. Алгоритм работы
5. Заключение
6. Приложения

## Контрольные вопросы

1. Какие выводы имеются у ЖКИ, каково их назначение?
2. Какие основные команды имеет ЖКИ?
3. Какие отличия работы дисплея в четырехбитном и восьмибитном режимах?
4. В чем особенность кодировки дисплея?
5. Какой формат имеет видеопамять ЖКИ?
6. Какой адрес будет у 7 символа 2 строки ЖКИ дисплея 40×4?
7. Изучив документацию на индикатор, посчитайте, какое минимальное время потребуется, чтобы вывести на индикатор 4 строки по 16 символов. Посчитайте как для четырехбитного, так и для восьмибитного режимов.
8. Для чего можно использовать программно включаемый курсор индикатора? Как его включить?
## Объекты vs Структуры данных

Структуры данных - можно сравнить со стандартными блоками конструктора LEGO. Они реализуют хорошо известные (часто используемые) абстракции и формируют базу для реализации Объектов. 

Работа программиста (написание кода программы) заключается в том, чтобы сформулировать решение задачи на "человеческом" языке а затем - трансформировать это решение в компьютерный язык (реализовать Объекты и их взаимодействие через методы).

Объекты **скрывают** структуру своих данных. Структуры - напротив, позволяют работать со своими данными **напрямую**.

ArrayList и LinkedList из JDK, формально, являются объектами классов, однако, по сути, это структуры - работа с ними **предполагает знание внутреннего устройства**.

## Concurrency vs Parallelism
Concurrency is an aspect of the problem domain — your program needs to handle multiple simultaneous (or near-simultaneous) events. Parallelism, by contrast, is an aspect of the solution domain.

Concurrency is not a way to make a program run faster. It is not a complex juggling trick that ninja coders use to keep multiple balls in the air at one time. On the contrary, it is the apparent sequential execution of a program that is the complex trick. Sequential execution is an illusion maintained by a cabal of compiler writers and hardware architects. Concurrency is simply the relaxation of a fabricated constraint.”

(C) Meike, G. Blake. “Android Concurrency”

Таким образом, современный runtime практически всегда предполагает конкурентное выполнение кода. В этом смысле, Concurrency напоминает Р-компонент из знаменитой CAP-теоремы. Они оба - "данность" современного мира разработки ПО (должно работать в многоядерном runtime и уметь горизонтально масштабироваться по множеству узлов - быть partition tolerance).

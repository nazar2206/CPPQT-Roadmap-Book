# Компіляція програми: як це працює

## Виконання програм

Виконувані файли \(_executable files_\), які ми звикли бачити \(і які, мають різний форматв залежності від операційної системи\) містять набір **машинних інструкцій та даних**, а також інформацію про те, де ці інструкції та дані знаходяться всередині файлу.

Під час запуску програми, операційна система вантажить її у оперативну пам'ять. Як саме відбувається заванатаження, а також, які саме частини виконуваного файлу будуть вантажитись залежить від формату цього файлу. Різні операційні системи можуть підтримувати різні формати виконуваного файлу. Тож зазвичай виконувані файли з Windows не можна запустити на ОС Linux, та навпаки \(хоча, можливо і таке, якщо якось "навчити" операційну систему виконувати такі файли, див. наприклад [Wine](https://uk.wikipedia.org/wiki/Wine)\).

Після завантаження у пам'ять даних та інструкцій з файлу, операційна система передає виконання на початок програми \(пам'ятаємо про функцію `main()`?\). Машинні інструкції є спеціальним кодом, зрозумілим для процесора.Різні процесори мають різний набір машинних інструкцій. Наприклад, виконувані файли, які запускаються на процесорах Intel \(який містять усі сучасні лептопи\) не можна запустити на міні-комп'ютерах Raspberry Pi остащених процесором з архітектурою ARM, хоча і там і там може бути встановлена OS Linux.

Машинні інструкції зручні лише для машин. Людині зрозуміти їх неймовірно важко, оскільки виглядають вони як набір цифр. Щоб упоратись з цим, кожній машинній команді задають скорочення. Цю мову для зручнішого текстового виразу машинних команд називають [**мовою Асемблеру**](https://uk.wikipedia.org/wiki/Мова_асемблера) \(_assembler_\).

Але й це не надто зручно. Набагато легше мислити словами та абстрактними поняттями. Тож були розроблені мови програмування, які дають нам можливість оформити ідеї синтаксично згідно з заданими правилами, а далі перетворити їх з допомогою програми, яка зветься **компілятором** \(_compiler_\). Відповідно, процес аналізу програми з наступним перетворенням у набір машинних команд та даних називають **компіляцією** \(_compilation_\).

## С++: процес компіляції

Процес створення виконуваного файлу з програми на мові С++ проходить у два важливі етапи:

* Спочатку програма **компілюється** \(_compile_\). Опрацьовується окремо кожен файл, який входить до складу програми.
  * Текст файлу попедедньо опрацьовується. Про роботу препроцесора та те, навіщо це може знадобитися ми обговоримо пізніше, коли будемо вивчати те, як писати програми, які складаються з багатьох файлів мовою С++.
  * Компілятор перевіряє текст програми на наявність синтаксичних помилок. Якщо вони є, то процес компіляції зупиняється.
  * Компілятор перетворює текст мовою С++ у набір машинніх кодів. До прикладу, [ось](https://godbolt.org/g/VeSnRy) як виглядає перетворений текст нашої найменьшої програми мовою С++. У правій манелі ми можемо побачити текст на асемблері та відповідний машинний код у шістнадцятковій системі числення. 
* Далі, усі скомпільовані файли \(якщо їх було кілька\) об'єднуються та перетворюються у викоуваний файл. Цей процес називається **зв'язуванням** \(лінкуванням, _linking_\). І виконується програмою, яка зветься лінкер \(_linker_\). Цей крок виконується окремо тому, що це дає змогу повторно використовувати скомпільований код, використовувати бібліотеки готових функцій та багато чого іншого.

## Живий приклад: GCC - компілюємо найменьшу програму у консолі

Тут ми наведемо коротку покрокову інструкцію, як виконати компіляцію найменьшої програми з попереднього розділу прамо у командному рядку.

1. Найперше, нам необхідно створити файл з текстом нашої програми:

   ```bash
   ~ touch main.cpp
   ```

2. Далі ми можемо відкрити та поредагувати цей файл, додавши текст програми.

   Можна зробити це прямо з консолі з допомогою команди `nano main.cpp` \(наша суперконсоль на Windows або консоль Linux якщо встановлено консольний редактор nano\).

   Інший варіант - відкрити у будь-якому зручному текстовому редакторі \(Для того, щоб відкрити поточну теку з файлом скористайтеся командою `explorer .` на Windows чи `open .` на Mac OS X \(зауважте, у цих командах в кінці крапка, що означає поточну теку!\)\).

3. Введіть текст програми. Наприклад:

   ```cpp
   #include <iostream>
   int main()
   {
    std::cout << "Hello!";
   }
   ```

4. Збережіть зміни та відкрийте знов командний рядок \(у редакторі nano `ctrl+s` для збереження та `ctrl+x` для виходу з редактора\).

   Переконайтеся, що ви маєте встановлений компілятор для мови С++. Для того, щоб переконатися чи встановлено компілятор GCC виконайте команду:

   ```bash
   ~ g++ --version
   g++.exe (Rev1, Built by MSYS2 project) 7.3.0
   Copyright (C) 2017 Free Software Foundation, Inc.
   This is free software; see the source for copying conditions.  There is NO
   warranty; not even for MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.
   ```

   Якщо ж ні - встановіть пакет компіляторів GCC на Linux з допомогою вашого пакетного менеджера, а на Windows скористайтеся командою `install-dev-env` у суперконсолі, яка встановить усе необхідне програмне забезпечення \(буде запит на підтвердження встановлення - просто натисніть Enter\).

5. Скомпілюйте програму:
   * На Windows \(суперконсоль\):

     ```bash
     ~ g++ -o main.exe main.cpp
     ```

   * На Linux:

     ```bash
     ~ g++ -o main main.cpp
     ```

     Тут ми викликаємо компілятор мови С++ `g++` з параметрами `-o <назва виконуваного файлу>`, а останнім параметром передаємо шлях до файлу програми. Команда має виконатись без повідомлень \(будь які повідомлення можуть означати помилку у тексті програми чи під час компіляції\).
6. Запустіть програму та переконайтеся, що вона виводить повідомлення `Hello!` у консоль.
   * На Windows \(суперконсоль\):

     ```bash
     ~ ./main.exe 
     Hello!
     ```

   * На Linux:

     ```bash
     ~ ./main
     Hello!
     ```


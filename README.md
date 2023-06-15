# PumLang

Это руководство содержит информацию о проекте PumLang - интерпретаторе языка программирования PumLang. Это был учебный проект, поэтому в коде я постараюсь довить некоторое количество комментариев и максимально подробно описать что и как работает.

# Описание

PumLang - язык программирования, который реализует следующие требования:

- Переменные: Язык поддерживает переменные, которым можно присвоить значения, а затем использовать эти значения в программе.
- Строгая типизация: Переменные в PumLang имеют строгую типизацию. При объявлении переменной программист должен указать тип данных, который она будет хранить.
- Типы данных: Язык поддерживает целые числа, вещественные числа и булевы значения (истина и ложь).
- Условный оператор: Поддерживается оператор условного перехода, который позволяет выполнить блок кода при соблюдении определенного условия.
- Оператор цикла: Поддерживается оператор цикла, который позволяет выполнять блок кода циклично, пока выполняется некоторое условие.
- Обработка ошибок: В случае синтаксической ошибки в программе, интерпретатор должен сообщить о ней, указав строку, в которой она произошла, и кратко описав ее суть.
- Ввод значения переменной: Язык позволяет пользователю вводить значения переменных, которые считываются с клавиатуры с помощью функции `input()` в Python.
- Вывод значения выражения на экран: Язык предоставляет функцию для отображения значений выражений на экране.

# Архитектура

Архитектура интерпретатора PumLang следует следующим основным этапам:

1. Лексический анализ: На этом этапе исходный код программы разбивается на лексемы, такие как операторы, ключевые слова, идентификаторы и т.д. Лексический анализатор обрабатывает строку и создает список лексем.
2. Синтаксический анализ: Список лексем анализируется для проверки синтаксической корректности программы. Используется метод рекурсивного спуска для построения синтаксического дерева или другой формы внутреннего представления программы.
3. Исполнение программы: Интерпретатор выполняет программу, используя внутреннее представление. Это включает работу со стеком для хранения значений и выполнение операций в соответствии с логикой языка PumLang.

# Грамматика языка Pumlang

```python
P  -> B&
B  -> {S [;S]^}
S  -> [int | bool | float] I [,I]^ | [int | bool | float] I := E| I := E | if (E) B [else B] | while (E) B | input(I) | print(E)
E  -> E1 [[> | < | = | !=] E1]
E1 -> T [[+ | - | or] T]^
T  -> F [[* | / | % | // | and] F]^
F  -> I | N | L | not F | (E)
L  -> true | false
I  -> C[C]^
N  -> R[.R]
R  -> D[D]^
C  -> a | b | ... | z
D  -> 0 | 1 | ... | 9

```


Попробуем разобраться в написанном. Данная грамматика содержит два типа сущностей: терминалы, например int, bool, a и т.д. и нетерминалы, обозначенные большими буквами. Под каждым нетерминальным символом скрывается правило его вывода. Названия нетерминальных символов в этой грамматике выбраны не случайно:
Вертикальная черта | означает выбор одного из вариантов, [] означают, что все, что находится внутри скобок, опционально, т.е. может отсутствовать, запись []^ означает, что написанное в скобках может повторяться несколько раз (возможно 0).
Рассмотрим некоторые правила чуть более подробно.


Распишем каждый пункт грамматики языка:

1. P (Programm) -> B&
   - Программа состоит из блока инструкций B, за которым следует символ "&".

2. B (Block) -> {S [;S]^}
   - Блок инструкций заключен в фигурные скобки "{}".
   - Блок содержит одну или несколько инструкций S, разделенных символом ";".
   - Инструкции могут повторяться ноль или более раз.

3. S (Statement) -> [int | bool | float] I [,I]^ | [int | bool | float] I := E| I := E | if (E) B [else B] | while (E) B | input(I) | print(E)
   - Инструкция может быть одной из следующих:
     - Объявление переменных типа "int", "bool" или "float", за которым следует идентификатор I. Может быть одно или несколько объявлений, разделенных запятой.
     - Присваивание значения выражения E переменной с именем I.
     - Объявление переменной с присваиванием значения и типа данных. int hello := 5. 
     - Условный оператор "if", который проверяет выражение E. За ним следует блок инструкций B. Может быть указан также блок инструкций для случая "else".
     - Цикл "while", который выполняется пока условие выражения E истинно. За ним следует блок инструкций B.
     - Инструкция "input", которая считывает значение с консоли и присваивает его переменной с именем I.
     - Инструкция "print", которая выводит значение выражения E на экран.

4. E (Expression) -> E1 [[> | < | == | != >= <=] E1]
   - Выражение может быть сравнением двух выражений E1, используя операторы сравнения ">" (больше), "<" (меньше), ">=" (больше либо равно), "<=" (меньше либо равно), "=" (равно) или "!=" (не равно).

5. E1 (Expression1) -> T [[+ | - | or] T]^
   - Выражение может содержать одно или несколько слагаемых T, которые могут быть сложены (+), вычтены (-) или объединены логическим оператором "or".

6. T (Term) -> F [[* | / | % | // | and] F]^
   - Выражение может содержать один или несколько множителей F, которые могут быть умножены (*), поделены (/), взяты остаток от деления (%), целочисленно поделены (//) или объединены логическим оператором "and".

7. F (Factor) -> I | N | L | not F | (E)
   - Выражение может быть идентификатором I, числом N, логическим значением L, логической операцией "not" от другого выражения F или выражением в скобках (E).

8. L (Boolean literal) -> true | false
   - Логическое значение может быть либо "true" (истина), либо "false" (ложь).

9. I (Identifier) -> C[C]^
   - Идентификатор начинается с символа C, за которым может следовать ноль или несколько символов C.

10. N (Number) -> R[.R]
    - Число состоит из целой части R, за которой может следовать десятичная часть, отделенная символом ".".

11. R (Digit) -> D[D]^
    - Число или целая часть числа состоит из одной или нескольких цифр D.

12. C (Letter) -> a | b | ... | z
    - Символы идентификаторов могут быть маленькими латинскими буквами от "a" до "z".

13. D (Digit) -> 0 | 1 | ... | 9
    - Цифры представлены символами от "0" до "9".


# Интерпретация

При написании кода не использовались какие-либо специальные библиотеки, кроме math и sys, да и то для того чтобы запускать все через командную строку.
Допустим мы хотим написать программу которая выводит факториал числа на языке Pumlang. Зная грамматику языка мы набросали вот такой код.

```python
{
    int n, s;
    input(n);
    s := 1;
    while (n > 1) {
        s := s * n;
        n := n - 1
    };
    print(s);
}
```


# Запуск своего кода

Изначально планируется что код написанный на языке Pumlang будет запускаться через командную строку. Файл в котором содержится программа написанная на языке Pumlang называется main.plg. Основной файл в проекте на который мы и будем ссылаться в командной строке называется pumlang.py. 

```bash

directory/Pumlang/python pumlang.py main.plg
```

# Лексический анализ. 

Этап лексического анализа является одним из первых шагов в обработке и анализе исходного кода в компьютерных программах. Он представляет собой процесс разбора исходного кода на последовательность лексем, или токенов, которые являются минимальными смысловыми единицами в языке программирования. Цель лексического анализа состоит в том, чтобы разбить исходный код на более простые элементы, которые могут быть легче обработаны и проанализированы на более высоких уровнях компиляции или интерпретации программы. Этот этап является важной частью процесса разработки программного обеспечения и основой для дальнейшего анализа и исполнения кода. Вот общая последовательность шагов, которые обычно включаются в этап лексического анализа:


1. Токенизация: Исходный код разбивается на отдельные символы и последовательности символов, называемые токенами. Токены могут представлять собой ключевые слова, идентификаторы переменных, операторы, числа, строки и другие элементы языка программирования. Пробелы и комментарии обычно игнорируются.

```bash
[ "{", "\n", " ", "int", " ", "n", ",", " ", "s", ";", "\n", " ", "input", "(", " ", "n", ")", ";", "\n", " ", "s", " ", ":=", " ", "1", ";",  "\n", " ", "while", " ", "(", " ", "n", " ", ">", " ", "0", " ", ")", " ", "{", "\n", " ", "s", " ", ":=", " ", "s", " ", "*", " ", "n", ";", "\n", " ", "n", " ", ":=", " ", "n", " ", "-", " ", "1", ";", "\n", " ", "}", " ", ";", "\n", " ", "print", "(", " ", "s", " ", ")", ";" ,"\n",  " ", "}", "

```

2. Удаление незначащих символов: Лексический анализатор удаляет незначащие символы, такие как пробелы, табуляции и переводы строк, которые не влияют на смысл программы.

```bash
[{, int, n, ',', s, ;, input, (, n, ), ;, s, :=, 1, ;, while, (, n, >, 0, ), {, s, :=, s, *, n, ;, n, :=, n, -, 1, ;, }, ;, print, (, s, ), ;, }]


```

3. Классификация токенов: Каждый токен классифицируется в соответствии с его типом. Например, ключевые слова, операторы и идентификаторы могут быть классифицированы по разным категориям.

- `{`, `}`: `LexBraceOpen`, `LexBraceClose`
- `(`, `)`: `LexBktOpen`, `LexBktClose`
- `,`: `LexComma`
- `;`: `LexSemicolon`
- `&`: `LexEnd`
- `int`, `float`, `bool`: `LexType`
- Идентификаторы (например, `n`, `s`): `LexId`
- Целочисленные константы (например, `1`): `LexInt`
- Вещественные константы (нет в данном списке токенов)
- Булевые константы (нет в данном списке токенов)
- `:=`: `LexAssign`
- `input`: `LexInput`
- `print`: `LexPrint`
- `if`: `LexIf`
- `else`: `LexElse`
- `while`: `LexWhile`
- `and`: `LexAnd`
- `or`: `LexOr`
- `not`: `LexNot`
- Бинарные операции (нет в данном списке токенов)

4. Обработка ошибок: Лексический анализатор может обнаруживать и обрабатывать ошибки в коде, такие как неверные символы, недопустимые комбинации токенов и другие нарушения синтаксиса языка.

```python
if tokens.count('{') != tokens.count('}') or tokens.count('(') != tokens.count(')'):
    print('Program must have an equal number of opening and closing brackets of all types')
    exit(-1)
elif tokens[0] == '{' and tokens[len(tokens) - 1] == '}':
    return tokens
else:
    print('Program must be in curly brackets: {your code}')
    exit(-1)

```

В этом фрагменте кода мы удостовериваемся, что количество открывающихся и закрывающихся скобочек всех видов совпадает и сама программа находится внутри скобок {}. 


5. Генерация выходных данных: В результате лексического анализа получается последовательность токенов или лексем, которая передается на следующий этап обработки, такой как синтаксический анализ.

```python
[{, int, n, ',', s, ;, input, (, n, ), ;, s, :=, 1, ;, while, (, n, >, 0, ), {, s, :=, s, *, n, ;, n, :=, n, -, 1, ;, }, ;, print, (, s, ), ;, }]

```


# Cинтаксический анализ 

Синтаксический анализ кода — это процесс анализа и разбора программного кода для определения его структуры и соответствия правилам синтаксиса конкретного языка программирования. Синтаксический анализатор (также известный как парсер) использует грамматические правила, заданные для языка, чтобы определить, правильно ли написан код и какие операции выполняются в коде.
В нашем случае для парсинга кода мы используем метод рекурсивного спуска, оснванный на нашей грамматике. То есть программа будет переходить между состояниями пропуская через себя код, и в случае несоотвествия выдавать ошибку. 

Здесь реализован простой переход между состояниями при начале программы.

```python
   def P(self):
       self.state = "B"
       self.B()
       return
```

А здесь реализован переход из состояния B в состояние S. Заодно мы считаем количество скобок, чтобы поймать ошибку до оновного парсинга, что ускорит работу и без того быстрой программы. Также проверяется наличие ";" после каждой строчки.

```python
   def B(self):
       self.current = self.get_next_elem()
       self.match('{')
       self.current = self.get_next_elem()
       self.state = "S"
       self.S()
       self.state = 'B'
       if self.current != ';' and self.previous not in ['{', '}']:
           self.error(
               f"Expected ; but got {'nothing' if self.current in ['{', '}', '&', ';'] else self.current}")
       while True:
           self.current = self.get_next_elem()
           if self.current == '}':
               self.current = self.get_next_elem()
               if self.current == '&':
                   return
               else:
                   return
           self.state = 'S'
           self.S()
           if self.current == '}' or '{':
               return
           elif self.current == ';':
               continue
           else:
               self.error(
                   f"Expected ; but got {'nothing' if self.current in ['{', '}', '&', ';'] else self.current}")

```
Так пройдя парсером по всему коду и не получив ошибку мы можем переходить к следующему этапу.

#Исполнение программы

Исполнение программы будет разделено на два этапа. 

1. Перевод в постфиксную запись
2. Внутреннее исполнение программы


## Тесты

Тесты являются важной частью разработки программного обеспечения. Для вашего проекта PumLang рекомендуется написать тесты, чтобы проверить корректность работы интерпретатора и соответствие языку PumLang.

Вы можете создать файлы с тестами и описать ожидаемые результаты для каждого теста. Ваша задача - заполнить этот файл самостоятельно.

Примеры тестов:

1. Тест на присвоение значения переменной и вывод значения на экран.
2. Тест на условный оператор.
3. Тест на оператор цикла.
4. Тест на обработку ошибок.
5. Тесты на различные выражения и операции.

Вы можете использовать любую удобную вам библиотеку для написания тестов на языке Python, например, `unittest` или `pytest`.

## Завершение

Это руководство описывает основные требования к проекту PumLang, а также предлагает написать тесты и ра

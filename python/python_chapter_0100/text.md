# Глава 10. Строки
> Современные программы должны обрабатывать Unicode — в питоне Unicode прекрасно поддерживается, и эта поддержка будет развиваться.  
Гвидо ван Россум

Итак, скалярные (простые) типы данных в питоне: числа, булевы значения и `NoneType`. Перейдем к составным типам данных, называемым коллекциями. И начнем со строк.

## Какие бывают коллекции
Коллекция — это контейнер, который хранит набор объектов. Если коллекция позволяет хранить объекты разных типов, то она называется **гетерогенной**; если только одного типа, то **гомогенной.**

Коллекции делятся на три категории:
- **Последовательности** (sequence). Допускают обращение к элементам по индексам. Уникальности элементов не требуется. К последовательностям относятся встроенные типы: 
    - `str` (строка),
    - `list` (список, а по сути массив),
    - `tuple` (кортеж, то есть неизменяемый список),
    - `range` (диапазон — неизменяемая последовательность чисел).
- **Множества** (sets). Хранят уникальные элементы, доступ к которым происходит по ключу, а не по индексу. Есть два варианта множеств: 
    - `set` — изменяемое множество,
    - `frozenset` — неизменяемое после создания множество.
- **Отображения** (mappings). Хранят набор пар ключ-значение. Доступ осуществляется по уникальному ключу. Среди встроенных типов есть всего один вариант отображений:
    - `dict` — словарь.

Этих коллекций хватает для решения многих типовых задач. Но неизбежны ситуации, когда функциональности встроенных типов становится маловато. Тогда в дело вступают библиотечные типы: `array`, `defaultdict`, `deque` и другие. С ними мы познакомимся позже. А пока обсудим каждую из встроенных коллекций.

Начнем со строк, но помните: операции, применимые к строкам, применимы и к другим типам последовательностей.

## Строки {#block-strings}
`str` — неизменяемый тип; гомогенная последовательность юникод-символов. 
Варианты инстанцирования пустой строки:

```python
empty1 = ""
empty2 = str()
```

Объявление строки, содержащей текст:

```python
bootstrap_server = "localhost:9092"
```

Вообще строки можно определять через одинарные кавычки `'ABC'`, двойные кавычки `"ABC"` и тройные кавычки (`'''ABC'''` либо `"""ABC"""`). Тройные кавычки используются для представления многострочных объектов:

```python
str_descr = """Textual data in Python is handled with str objects, or strings.
Strings are immutable sequences of Unicode code points.

Citation from docs.python.org
"""
```

PEP8 [рекомендует](https://peps.python.org/pep-0008/#documentation-strings) двойные кавычки `"Some text"` для однострочников и три двойных кавычки `"""Some text"""` для многострочных текстов.

В питоне любой объект можно превратить в строку с помощью функции `str()`. Даже комплексное число:

```python
s = str(3 + 5j)
```
В данном случае мы получим строку `s`, равную "(3 + 5j)"

**Присваивание строки** выглядит следующим образом:

```python
s2 = s1
```

Временная сложность присваивания — O(1), потому что строка — это неизменяемый тип, и вместо выделения памяти и копирования просто создается второй указатель на уже существующую строку. Однако если впоследствии модифицировать `s2` (например, удалить из строки все пробелы), id объекта `s2` изменится. Он будет указывать на новую область памяти.

А все потому, что любая «модификация» неизменяемого объекта на самом деле — это неявное создание нового объекта по той же ссылке. Причем при изменении `s2` содержимое `s1` останется прежним: `s2` будет смотреть на новую область памяти, а `s1` на старую.

Создайте переменную `a`, равную `"TEXT"`. {.task_text}

Присвойте переменной `b` переменную `a`.  {.task_text}

Последовательно на отдельных строках выведите: значение переменной `a`, id `a`, значение `b` и id `b`.  {.task_text}

Затем добавьте к переменной `b` подстроку `"2"` (оператор `+=` вам в помощь).  {.task_text}

Снова выведите значение `a`, ее id, значение `b` и ее id. {.task_text}

```python {.task_source #python_chapter_0100_task_0010}
```
Для определения id воспользуйтесь функцией `id()`. {.task_hint}
```python {.task_answer}
a = "TEXT"
b = a

print(a)
print(id(a))
print(b)
print(id(b))

b += "2"

print(a)
print(id(a))
print(b)
print(id(b))
```

Этот эксперимент демонстрирует, что пока `a` и `b` указывают на одну и ту же строку, их id совпадают. Но id `b` меняется после изменения строки по данному указателю: `b` становится новым объектом в памяти.

## Посимвольный доступ {#block-string-indices}
Получение символа строки по индексу в питоне реализовано через квадратные скобки `[]`:

```python {.example_for_playground}
bootstrap_server = "localhost:9092"

c = bootstrap_server[3]
print(c)
```
```
a
```

Этот код выполнился за O(1) и вывел в консоль символ по индексу 3.

Также по строке можно итерироваться:

```python
for c in s:
    print(c)
```

Здесь `c` — это обрабатываемый на каждом витке цикла символ.

Альтернативный вариант итерации — с помощью индексов. Используем для этого встроенную функцию `range`, возвращающую диапазон чисел от нуля до длины строки `s` не включительно. Каждое число диапазона является корректным индексом строки:

```python
for i in range(len(s)):
    print(s[i])
```

Заведите функцию `print_letters(s)`, которая принимает на вход строку и выводит по одному символу на одну строку, пропуская при этом символы пробела. {.task_text}

```python {.task_source #python_chapter_0100_task_0020}
```
Для итерирования по строке воспользуйтесь циклом `for`. {.task_hint}
```python {.task_answer}
def print_letters(s):
    for letter in s:
        if letter != " ":
            print(letter)
```

## Срезы {#block-slices}
Помимо получения символов строки по индексу в питоне есть мощный инструмент для извлечения подстроки по заданным параметрам — срезы. 

**Срезы** (слайсы, slices) — объекты для представления определенной части последовательности (в нашем случае строки). Для взятия срезов тоже используются квадратные скобки, но с двоеточиями внутри. 

Допустим, у нас есть строка `s`. Тогда взятие среза выглядит так:

```python
s[start:end:step]
```

Здесь:
- `start` — индекс начала среза. Может не указываться, то есть быть пустым: `s[:end:step]`. Тогда за начало принимается индекс 0.
- `end` — конец среза. Указывает, до какого индекса **не включительно** формировать срез. Может быть пустым (`s[start::step]`), и тогда срез ограничивается концом строки. А может быть и отрицательным! -1 означает индекс последнего символа, -2 — предпоследнего (не включительно) и так далее.
- `step` — шаг, с которым добавлять символы в срез. По умолчанию 1. Можно не указывать и при этом опустить второе двоеточие (`s[start:end]`).

Несколько примеров с различными срезами строки `s = "ABCDEF"`:

```python
print(s[2:4])
```

Получаем символы со 2 по 3 индекс:

```
CD
```

```python
print(s[::3])
```

Берем каждый третий символ с нулевого по последний:

```
AD
```

```python
print(s[:-2])
```

Срезаем подстроку от нулевого до предпоследнего индекса не включительно:

```
ABCD
```

Важно помнить, что взятие среза — это всегда неявное создание новой строки. В питоне реализован подход **slice-by-copy,** то есть при каждом получении среза все символы, входящие в срез, копируются в новый объект строки. Временная сложность взятия среза равна O(end - start), так как для формирования среза нужно проитерироваться по последовательности от индекса `start` до `end`.

Присвойте переменной `x` срез строки `commands` до первой запятой не включительно. {.task_text}

```python {.task_source #python_chapter_0100_task_0030}
commands = "grep, sed, awk"
```
Определите индекс символа, являющегося первой запятой в строке. И возьмите срез до этого индекса. {.task_hint}
```python {.task_answer}
commands = "grep, sed, awk"
x = commands[:4]
```

Присвойте переменной `y` срез строки `vals` с последнего двоеточия не включительно. {.task_text}

```python {.task_source #python_chapter_0100_task_0040}
vals = "1:10:89"
```
Определите индекс последнего двоеточия в строке. Возьмите срез, начиная со следующего за ним символа. {.task_hint}
```python {.task_answer}
vals = "1:10:89"
y = vals[5:]
```

А вот распространенный трюк с использованием срезов:

```python
b = a[:]
```

Он применяется для создания независимой копии последовательности вместо заведения на нее ссылки. Для строк это бессмысленно, так как строки неизменяемые. А вот создавать копии списков через `[:]` вполне удобно (об этом чуть позже).

## Распространенные операции над строками {#block-operators}
Приведем распространенные **операции** над строками и другими контейнерами-последовательностями с указанием их временной сложности:
- `s2 = s1` — присваивание путем создания новой ссылки на существующую область памяти. O(1).
- `s1 == s2` — поэлементное сравнение. Сложность O(1), если длины последовательностей отличаются. Иначе O(n), где n — длина последовательности.
- `s[i]` — взятие элемента по индексу `i`. O(1).
- `s[start:end:step]` или `s[start:end]` — взятие среза. O(`end` - `start`).
- `c in s` — возвращает `True`, если `c` содержится в `s`. Иначе возвращает `False`. Инверсия этого условия (проверка, что `c` не содержится в `s`) выглядит так: `c not in s`. Для строк таким образом можно проверять вхождение не только отдельных символов, но и целых подстрок: `"on" in "python"`. Для поиска подстроки используется алгоритм Бойера — Мура — Хорспула, эффективность которого зависит от данных. В среднем случае это O(n). До выхода питона 3.10 в худшем случае эффективность падала до `O(n * c)`, как у наивного поиска со сравнением с подстрокой `c` в цикле по строке `s` длины `n`. Но в питоне 3.10 была добавлена эвристика, улучшившая время до O(n + c).
- `s1 + s2` — конкатенация последовательностей `s1` и `s2`. Сработает и упрощенный синтаксис `+=`: `s1 += s2`. O(n).
- `s * n` или `n * s` (где `n` — целое число) — повторение последовательности `s` `n` раз. O(m), где m - длина новой последовательности.

Самые популярные встроенные **функции,** применимые к строкам и другим последовательностям:
- `len(s)` — получение длины последовательности. Временная сложность — O(1), так как все встроенные последовательности содержат внутренний счетчик объектов.
- `min(s)` — поиск минимального элемента в `s`. O(n).
- `max(s)` — поиск максимального элемента в `s`. O(n).

## Распространенные методы строк
Перечислим самые популярные **методы** класса `str`:
- `s.count(c)` — подсчитывает количество элементов последовательности, равных `c`. Этот метод реализован не только для строк, но и для других последовательностей.
- `s.startswith(c)`, `s.endswith(c)` — методы для проверки, начинается/заканчивается ли строка подстрокой `c`. Возвращают `True` либо `False`.
- `s.upper()`, `s.lower()` — методы для перевода строки в верхний/нижний регистр. Возвращают измененную копию строки.
- `s.find(c)` — ищет подстроку и возвращает индекс ее первого вхождения. В случае неудачи возвращает -1.
- `s.rfind(c)` — отличается от `find()` тем, что ищет подстроку, начиная с конца строки.
- `s.strip()` — удаляет с двух концов строки пробельные символы (в том числе пробел, символ табуляции `\t`, перевод строки `\n`) и возвращает измененную копию строки. В метод можно передать аргумент - набор символов для удаления. Например, `s.strip("\n")` вернет копию с удаленными символами переноса строки.
- `s.replace(src, target)` — возвращает копию строки, в которой заменены все вхождения подстроки `src` на подстроку `target`. В качестве `target` можно указать пустую строку.
- `s.split()` — разбивает строку по символу-разделителю и возвращает список. По умолчанию разделителем считается пробел, но это переопределяется. Например, `s.split("\t")` разделит строку по символу табуляции.
- `delim.join(seq)` — метод, обратный `.split()`: склеивает элементы последовательности `seq` в строку, разделяя их строкой `delim`. Например, `",".join(["A", "B"])` вернет новую строку `"A,B"`.
- `s.encode(encoding)` — возвращает строку `s` в заданной кодировке (по умолчанию utf-8). Например, `"питон".encode(encoding="cp1251")` вернет `b'\xef\xe8\xf2\xee\xed'`.

У типа `str` есть множество других методов, но эти — наиболее распространенные.

Напишите функцию `concat(sep, s1, s2, s3)`, которая принимает 4 строки: `sep` (разделитель) и строки `s1`, `s2`, `s3`, которые нужно склеить с помощью разделителя и вернуть из функции. Например, `concat("~", "A", "B", "C")` вернет строку `"A~B~C"`. {.task_text}

```python {.task_source #python_chapter_0100_task_0050}
```
Нужно воспользоваться методом `join()` объекта `sep` и передать в него список из `s1`, `s2`, `s3`. {.task_hint}
```python {.task_answer}
def concat(sep, s1, s2, s3):
    return sep.join([s1, s2, s3])
```

Напишите функцию `count(s, c)`, которая принимает строку `s`, символ `c` и возвращает количество раз, которое `c` встретился в `s`. {.task_text}

```python {.task_source #python_chapter_0100_task_0060}
```
Нужно воспользоваться методом `count()` объекта `s`. {.task_hint}
```python {.task_answer}
def count(s, c):
    return s.count(c)
```

## Форматирование строк {#block-formatting}
Форматирование строк добавлено в питон, чтобы избавиться от боли конкатенации строк для их сохранения или вывода.

Допустим, мы хотим залогировать строку и ее длину. Это легко устроить через конкатенацию:
```python
s = "ABC"

print("String "  + s + " has " + str(len(s)) + " letters")
```

Но у подобного подхода слишком много минусов: громоздкость, подверженность ошибкам и опечаткам, просто-напросто низкая скорость.

Начиная с питона 3.6 форматирование строк удобно реализуется через f-строки (f-strings): 

```python {.example_for_playground}
s = "ABC"

print(f"String {s} has {len(s)} letters")
```

Этот код выведет в консоль отформатированный текст:

```
String ABC has 3 letters
```

Для формирования f-строки перед кавычками указывается префикс `f`, а внутри самой строки в фигурных скобках перечисляются переменные и выражения, которые необходимо добавить в строку.

В более старом коде вы встретите и альтернативные способы форматирования:

```python  {.example_for_playground}
s = "ABC"

print("String %s has %d letters" % (s, len(s)))
print("String {} has {} letters".format(s, len(s)))
```

Для того чтобы в отформатированную строку добавить имя переменной и ее значение, используется компактная запись с именем переменной и `=` внутри фигурных скобок:

```python  {.example_for_playground}
x = 5
print(f"{x=}")
```
```
x=5
```

Не путайте этот синтаксис с синтаксисом оператора моржа `:=`. Кстати, его тоже можно использовать при форматировании строк:

```python  {.example_for_playground}
from datetime import datetime

print(f"Today: {(today:=datetime.today()):%Y-%m-%d}, day of week: {today:%A}")
```
```
Today: 2024-06-27, day of week: Thursday
```

Чтобы внутри f-строки правильно обрабатывалось выражение с оператором моржа, его нужно заключать в круглые скобки.

Напишите функцию `f(user_id, visits)`, которая принимает два целых числа `user_id` и `visits` и возвращает отформатированную строку со статистикой по пользователю: `"User '498258' visited page 2 times"`. {.task_text}

Обратите внимание, что в отформатированной строке id пользователя заключен в одинарные кавычки. {.task_text}

```python {.task_source #python_chapter_0100_task_0070}
```
Воспользуйтесь f-строкой, внутри которой в фигурных скобках будут присутствовать переменные `user_id` и `visits`. {.task_hint}
```python {.task_answer}
def f(user_id, visits):
    return f"User '{user_id}' visited page {visits} times"
```

## Резюмируем
- В питоне есть гетерогенные и гомогенные коллекции.
- Коллекции делятся на последовательности, множества и отображения.
- Тип `str` — это неизменяемая последовательность юникод-символов.
- Для взятия среза используется синтаксис `s[start:end:step]`.
- Многие операции над строками применимы и к другим последовательностям. Однако у типа `str` есть свой уникальный набор методов.
- Для форматирования строк удобнее всего использовать f-строки.

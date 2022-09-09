# 1. intro + bash

## Полезные ссылки

- ридинг Печатнова [https://github.com/yuri-pechatnov/caos/tree/master/sem01-intro-linux](https://github.com/yuri-pechatnov/caos/tree/master/sem01-intro-linux)
- ридинг Пономарёва  [https://github.com/Alexponomarev7/caos_seminars/tree/master/sem1](https://github.com/Alexponomarev7/caos_seminars/tree/master/sem1)
- презентация Горбунова. тут весь курс, может быть очень удобно!
    [Семинары АКОС 023 026.pdf](gorbunov_seminars.pdf)
- bandit [https://overthewire.org/wargames/bandit/](https://overthewire.org/wargames/bandit/)
*минус сдача по башу тому кто пройдёт всё*

## Основные команды баша

мы начнём с того как пользоваться башом как языком, а только потом, используя эти фичи, будем играться с разными командами/инструментами.

### Переменные

- объявляем `foo=15`
- читаем с командной строки в переменную
`read`; `read varname`; (переменная `reply`)
- `echo`, `echo` + переменные
`apples=500; echo "I have $apples apples"`

### Код возврата

- при выполнении команды возвращают “код возврата”.  (помните `return 0;` из си?).
0 - значит программа завершилась правильно, >0 значит с ошибкой.
проверяем код возврата ошибки при помощи команды `$?`

### Скобки

- `[]` , `test` - это (внезапно) обычные команды. используются для проверки условий. 
залетаем в `man test`/ `man [` чтобы посмотреть что они умеют.
Пример: `a="hello"; [ $a == "hello" ]; echo $?`
Можно юзать и команду `[[]]` поддерживается в korn, bash, zsh (те на всех +- современных системах его юзать норм, если не требуется иного). Но для обратной совместимости мы пользуемся именно `[]`, `test`.  
Для тех, кому интересно, больше про это можно почитать здесь[http://mywiki.wooledge.org/BashFAQ/031](http://mywiki.wooledge.org/BashFAQ/031).
- `(( ))` для работы с арифметическими выражениями.
- `${}` - можно просто вывести значение переменной `${a}`, а можно (и нужно) делать всякие интересные штуки в духе:
    - `${foo:-default}` - заменяем дефолтным значением, если у переменной нет значения
    - `echo foo{baz,bar}` генерим все возможные слова, которые начинаются с первой части и заканчиваются вариантами второй
    - см. Массивы
    больше про `${}` можно почитать тут [http://tiswww.case.edu/php/chet/bash/bashref.html#Brace-Expansion](http://tiswww.case.edu/php/chet/bash/bashref.html#Brace-Expansion)
    
- \`\` или `$()` исполняют команду “внутри скобок” и возвращают её результат

### Условия

```bash
a=hello
if [ $a = "hello" ]; then  #  remember the [ command?
	echo "a is hello";
else
	echo "a is not hello";
fi;

# a=1; b=2; c=3;
# same works with (($a + $b == $c )) 
```

### Циклы

IFS = internal field separator

То, за что он отвечает, лучше всего опишет документация:

> The shell scans the results of parameter expansion, command substitution, and arithmetic expansion that did not occur within double quotes for word splitting.
The shell treats each character of `$IFS` as a delimiter, and splits the results of the other expansions into words using these characters as field terminators.
source: [https://www.gnu.org/software/bash/manual/html_node/Word-Splitting.html](https://www.gnu.org/software/bash/manual/html_node/Word-Splitting.html)
> 

ВАЖНО: в **zsh** по дефолту фишка с `var="foo bar"; for foo in $var`  работать не будет. Челики объяснили почему вот [тут](https://zsh.sourceforge.io/FAQ/zshfaq03.html#31). 
Мы же пока переключаемся на bash.

```bash
# for loop using IFS
IFS=":"
myvalues="1:2:3:4:5"
for val in $myvalues; 
do 
	echo "$val";
done;

# you can even use emojis as separators
IFS=💞
for item in  $( echo "i💞love💞💞💞akos"); 
do
    echo "$item"
done


# for loop using a list of values
for val in 1 2 ; 3 4 5;
do 
	echo  "$val";
done;
```

### Массивы

(Остаёмся на баше). Массивы везде разные, так что для каждого отдельного интерпретатора нужно курить свой отдельный мануал. ~~Массивами пользоваться в баше вам скорее всего нужно будет не очень часто.~~

Читаем код

```bash
# declare an array of numbers
array=(1 2 3 4 5)
# we can create an array of strings as well
# same as declare -a array_str
array_str=("hello dolly" "this is" "louis dolly")

echo ${array_str[@]} # same as `echo ${array_str[*]}`

${array_str[*]}  # all array elements
${!array_str[*]} # all array indices
${#array_str[*]} # array length
${#array_str[0]} # length of the 0th value

for el in "${array_str[*]}"; # let's compare it to "${array_str[*]}"
do
	echo "$el";
done;
```

Если сильно хочется ~~обмазаться~~ разобраться с массивами в баше, полезно будет почитать это [https://habr.com/ru/post/511608/](https://habr.com/ru/post/511608/)

По дефолту в массивы будут индексируемые (за индексы можно будет использовать только чиселки). Но можно сделать мапу, для этого нужно сказать `declare -A map`.
`map=([key]=value [key2]=value2)` - добавляем значения
`echo ${map[key]}` -  печатаем значения
`map[key3]=value3` - добавляем новый ключ со своим значением.

Тут есть больше инфы про массивы в баше - https://www.gnu.org/software/bash/manual/html_node/Arrays.html

## Команды баша

### Простое

`pwd`, `cd` ,`man`, `du`, `cat`, `wc` ,`rm`, `mkdir # -p`

### `vim` - базовые команды

**главное** - **не пугаемся, выйти отсюда можно, нужно просто знать как!**
Есть два режима: command & insert. В командный вас по дефолту кидает, когда вы делаете `vim some_file.txt`. Чтобы выйти, нажимаем Esc и пишем `:q!` - выход без сохранения. Если нужно сохранить, пишем `:wq`. Чтобы начать писать, в командном режиме тыкаем `i` и пишем начинаем писать.
Искать по файлу можно набрав `/something` в командом режиме (предварительно нужно нажать `esc`).
Вот неплохой гайд [https://www.openvim.com/](https://www.openvim.com/)

### `ls`, `ls -lah`, `chmod`, права доступа

Флаги:
 - `-а` отобразит скрытые файлы.
 - `-l` отобразит “расширенную инфу” про файлы
 - `-h` разрмеры файлов будет отображать в human-readable форме.

В режиме расширенной инфы, в первой колонке будут какие-то буквы - права доступа. Символов будет всего 10:
Первый “d” или “-” - директория или нет. Дальше три тройки символов rwx (на любой из позиций может стоять минусик).
Первая тройка - права владельца файла, вторая - права группы-владельца, третья - права всех остальных юзеров. r-read, w-write, x-execute. 

Примеры:

`r--r--r--` - значит что владельцу, группе и всем можно только читать. 

`rwx------` - можно читать только владельцу. 

Эти тройки также могут быть представлены в виде чиселок от 0 до 7 (0= `---`, 7=`rwx`).
Права меняются при помощи команды `chmod`.

### `#!` (shebang) в начале файла
Исполняемый файл, которым надо исполнить скрипт передаётся после `#!` (нужен полный путь!).
Файл, который мы указываем после шебанга, должен быть исполняемым.
Наример, если у нас будет файл начинаться со строчки #! /path/to/python , а внутри будет какой-то скрипт на питоне, то если вы в терминале введёте `./my_file` - то файл исполнится на питончике (только надо не забыть `chmod +x my_file` сделать перед этим).

### `top`, `ps`

`top`, `ps` 

Можно посмотреть директорию `/proc` и понять, что (по крайней мере на линуксе) вся инфа которая есть в top/ps на самом деле берётся из этой директории. 

Получается что мы процессы умеем представлять как файлы? хмм…. (оставляем где-то эти знания, они нам пригодятся). 
[https://www.linux.com/news/discover-possibilities-proc-directory/](https://www.linux.com/news/discover-possibilities-proc-directory/)

### `grep`

для начала просто грепаем файл: `grep error example.log`

`grep -v example.log`

### pipes

Основная мысль состоит в том, что хорошо известные нам stdin, stdout, stderr - на самом деле тоже “файлы”. В кавычках, потому что это файловые дескрипторы. Когда мы пишем в stdout, мы в скрипте/программе пишем в файл, а отображается всё у нас на экране. 

В линуксе есть достаточно удобный инструмент “перенаправления” потоков вывода команд в другие команды. 

Простой пример: у нас есть большой файл и мы хотим узнать сколько в нём строк, как будем поступать? 

А теперь этот же файл нам надо погрепать? Например, найти все строки в которых есть слово error (или его нет).

Тот же пример с `[log-generator.sh](http://log-generator.sh)` ,`less`

### IO redirection
Попробуйте скомпилить `gcc -oerr error.c` и поиграться с тем что написано ниже.  
- `n>` перенаправляет какой-то fd c номером N в какой-то файл. `>` перенаправит stdout в файл.
- `n>&m` - направляет один файловый дескриптор в другой <!--(stdout && stderr redirection)-->  
    Попробуйте сделать `./err > my_log.txt 2>&1`. Что произошло?
- `&>` - перенаправит stdout+stderr в файл (но не везде работает)
- `>>` добавляем в файл (не пишем заново)
- `<` перенаправляем ввод из файла в нашу программу

Пример: 
`python -c "print(int(input()) + 100500)" < input > output`

### `awk`

awk - на самом деле ещё один язык программирования. Мы посмотрим на некоторые простые прикольные  штуки, которые с его помощью можно делать.

Структура команды:
`awk 'pattern {action}'`  
Вход - строка. С каждой строкой происходит следующая логика: если `pattern` - правда, то выполняй `action`. Если `action` пуст - выводится вся строка. Если pattern пуст, считается, что он возвращает правду.

`$0` - вся строка, `$1, $2...` - номера колонок (в случае если в каждой строке колонки разделяются пробелами).

Пример: `cat names.log | awk '$3 > 18 {print $1 $2}'`  
А ещё можно пользоваться переменными, и можно их не объявлять, например: 
`cat names.log | awk '{s+=$3} END {print s}'` - просуммировали возраст всех людей из списка.

### `sed`
`sed` - `s`tream `ed`itor, из названия понятно что можно делать с его помощью.
Если сильно хочется много про него знать - ман + инет в помощь, мы же опять посмотрим несколько простых применений:

- `./log-generator.sh | sed 's/INFO/NOT_IMPORTANT/'` - во всех строках заменяем INFO на NOT_IMPORTANT
- `echo "hello hello hello" | sed 's/hello/privet/2'` - заменяем второй hello который встретим.
- `echo "hello hello hello" | sed 's/hello/privet/g'`
- `cat example.log | sed -in "s/error/blabla/pg"`

### регулярки
. - один символ
.* - сколько угодно символов
.*? - ищем жадно: находим самую длинную подходящую подстроку
.+ - хотя бы один элемент
[0-9] - цифра
[0-9]* - несколько циферок (но может быть и ни одной)

Про группы: если мы какую-то часть возьмём в скобки, то она будет группой, потом мы по номеру скобочек сможем выбирать заматченную часть.
Пример строчки:
`bulk api stats for 5s: batches=93, docs=36760, simyzone3.4 MB, took/batch=11.5ms, took/doc=0.0291ms, took/simyzone4.6ms, append queue=0, write queue=0`
Нам надо из всей этой строчки вывести только чиселку после docs.
```
.*docs=([0-9]*).*
^ ^    ^^    ^ ^
| |    ||    | |
| |    ||    | +-- какие-то символы в концe
| |    ||    +---- несколько символов от 0 до 9   
| |    |+---> символы от 0 до 9
| |    +----> начало группы (потом её можно получить по номеру 1)
| +----> начало текста который нас интересует
+------> какие-то символы до начала того что "docs="
```


Примерчики:
В примере ниже мы берём регулярку, разобранную выше, и заменяем всю регулярку на первую группу.
- `cat example.log | grep "bulk api stats" | sed -n "s/.*docs=\([0-9]*\).*/\1/p" | awk '{s+=$1} END {print s}'`

Тут считаем сколько всего вариантов значений есть в 4 столбике и сколько раз каждый из них встречается в файлике.
- `awk '{print $4}' example.log | sort -u | wc -l`

Тут просто смотрим сколько есть вариантов значений 4 столбика
- `cat example.log | awk '{print $4}' | sort | uniq -c`

## О чём нужно задуматься
- в UNIX всё - файл (файловый дескриптор (поток байтов))
- Почему в директории `/proc` можно видеть инфу про процессы?

## Разные интересности

- `fuck` [https://github.com/nvbn/thefuck](https://github.com/nvbn/thefuck)  - прикольная утилитка, которая исправляет ошибки команд.
- [https://www.youtube.com/watch?v=tc4ROCJYbm0](https://www.youtube.com/watch?v=tc4ROCJYbm0) - видео из архивов AT&T, где, например, на 6 минуте, Керниган (да, тот самый!) с ногами на столе про пайпы (и не только) рассказывает
- почему вообще юникс такой? откуда взялись все эти фичи [https://en.wikipedia.org/wiki/Unix_philosophy](https://en.wikipedia.org/wiki/Unix_philosophy)
- переписка Линуса Торвальдса про файлы
[https://yarchive.net/comp/linux/everything_is_file.html](https://yarchive.net/comp/linux/everything_is_file.html)
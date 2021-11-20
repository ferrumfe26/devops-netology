1. какой переменной можно задать длину журнала history, и на какой строчке manual это описывается?

882 строка HISTSIZE(файл ManBash.png)

2. что делает директива ignoreboth в bash?

of ignoreboth is shorthand for ignorespace and ignoredups.  A value of erasedups  causes  all  previous
              lines  matching  the  current  line to be removed from the history list before that line is saved.  Any
              value not in the above list is ignored.
*** не записывать команду, которая начинается с пробела, либо команду, которая дублирует предыдущую.

3. В каких сценариях использования применимы скобки {} и на какой строчке man bash это описано?

  (list) list is executed in a subshell environment (see COMMAND EXECUTION ENVIRONMENT below).  Variable assign‐
              ments  and  builtin commands that affect the shell's environment do not remain in effect after the com‐
              mand completes.  The return status is the exit status of list.

  { list; }
             list is simply executed in the current shell environment.  list must be terminated with  a  newline  or
             semicolon.  This is known as a group command.  The return status is the exit status of list.  Note that
             unlike the metacharacters ( and ), { and } are reserved words and must occur where a reserved  word  is
             permitted  to be recognized.  Since they do not cause a word break, they must be separated from list by
             whitespace or another shell metacharacter.

стр.252 

** То, что находится между круглыми скобками, выполняется в отдельном подпроцессе. А то, что находится между фигурными скобками — выполняется в контексте текущей оболочки.
Используется в циклах, условных операторах, функции.

4. учётом ответа на предыдущий вопрос, как создать однократным вызовом touch 100000 файлов? 
touch {000001..100000}.txt
5. Получится ли аналогичным образом создать 300000? Если нет, то почему?

getconf ARG_MAX = 2097152

те получается, что touch {000000..110247}.txt = это порядка 
2097152/19 те общее количество делится на кол-во символов в каждой строке
плюс видимо перевод каретки... без .txt получится больше диапазон 130к+

5. Что делает конструкция [[ -d /tmp ]]

[[ -d /tmp ]] - команда test, возвращает 1 или 0, если /tmp
-d file — истина, если file существует и является каталогом

По сути проверяет есть ли каталог tmp

6. Основываясь на знаниях о просмотре текущих (например, PATH) и установке новых переменных; командах, которые мы рассматривали, добейтесь в выводе type -a bash в виртуальной машине наличия первым пунктом в списке:

vagrant@vagrant:~$ mkdir /tmp/new_path_dir

vagrant@vagrant:~$ cp /bin/bash /tmp/new_path_dir/

vagrant@vagrant:~$ ll /tmp/new_path_dir/

total 1164
vagrant@vagrant:~$ type -a bash

bash is /tmp/new_path_dir/bash

bash is /usr/bin/bash

bash is /bin/bash

7. Чем отличается планирование команд с помощью batch и at

at      executes commands at a specified time.  
batch   executes commands when system load levels permit; in other words, when the load average drops below 1.5, or the value specified in the invocation of atd. 

8. Завершите работу виртуальной машины чтобы не расходовать ресурсы компьютера и/или батарею ноутбука.
sudo shutdown -P
Домашнее задание к занятию "3.2. Работа в терминале, лекция 2"

1. Какого типа команда cd? Попробуйте объяснить, почему она именно такого типа; опишите ход своих мыслей, если считаете что она могла бы быть другого типа.

        $ type cd
        cd is a shell builtin
        
        CD внутренняя команда, которая необходима для смены каталока внутри оболочки, 
        если предположить, что cd можно сделать внешней, то при вызове ее, shell будет родителем этого процесса,
        соответственно получается, что вызов будет происходить из того же каталога где вы изначально и находитесь,
        а цель изменить этот каталог. Ну видимо, без каких то дополнительных извращений не получиться сменить каталог,
        внешеней программой cd, или придется вызвать новый шелл из нового каталога.
        поэтому эту команду и сделали внутреней. Как то так.

2. Какая альтернатива без pipe команде grep <some_string> <some_file> | wc -l? man grep поможет в ответе на этот вопрос. Ознакомьтесь с документом о других подобных некорректных вариантах использования pipe.

        vagrant@vagrant:~$ grep user /var/log/syslog | wc -l
        20
        vagrant@vagrant:~$ grep user /var/log/syslog -c
        20

3. Какой процесс с PID 1 является родителем для всех процессов в вашей виртуальной машине Ubuntu 20.04?3/ 

        vagrant@vagrant:~$ pstree -p 1
        systemd(1)─┬─VBoxService(804)─┬─{VBoxService}(806)
                   │                  ├─{VBoxService}(807)
                   │                  ├─{VBoxService}(808)
                   │                  ├─{VBoxService}(810)
                   │                  ├─{VBoxService}(811)
                   │                  ├─{VBoxService}(812)
                   │                  ├─{VBoxService}(813)
                   │                  └─{VBoxService}(814)
                   ├─accounts-daemon(588)─┬─{accounts-daemon}(595)
                   │                      └─{accounts-daemon}(638)
                   ├─agetty(691)
                   ├─atd(671)
                   ├─cron(664)
                   ├─dbus-daemon(593)
                   ├─irqbalance(616)───{irqbalance}(620)
                   ├─multipathd(537)─┬─{multipathd}(538)
                   │                 ├─{multipathd}(539)
                   │                 ├─{multipathd}(540)
                   │                 ├─{multipathd}(541)
                   │                 ├─{multipathd}(542)
                   │                 └─{multipathd}(543)
                   ├─networkd-dispat(617)
                   ├─polkitd(648)─┬─{polkitd}(649)
                   │              └─{polkitd}(651)
                   ├─rpcbind(567)
                   ├─rsyslogd(618)─┬─{rsyslogd}(629)
                   │               ├─{rsyslogd}(630)
                   │               └─{rsyslogd}(631)
                   ├─sshd(669)───sshd(1091)───sshd(1139)───bash(1140)───pstree(1238)
                   ├─systemd(1104)───(sd-pam)(1105)
                   ├─systemd-journal(369)
                   ├─systemd-logind(621)
                   ├─systemd-network(414)
                   ├─systemd-resolve(568)
                   └─systemd-udevd(399)
        
        
        или 
        
        vagrant@vagrant:~$ ps aux
        USER         PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND
        root           1  0.1  1.1 101644 11136 ?        Ss   06:35   0:04 /sbin/init
        
4. Как будет выглядеть команда, которая перенаправит вывод stderr ls на другую сессию терминала?

        sudo ls -al /root 2>/dev/pts1

5. Получится ли одновременно передать команде файл на stdin и вывести ее stdout в другой файл? Приведите работающий пример.

        _vagrant@vagrant:~$ cat /var/log/syslog | wc -l
        1019
        vagrant@vagrant:~$ cat /var/log/syslog | wc -l 1>/home/vagrant/wclog
        vagrant@vagrant:~$ cat /home/vagrant/wclog
        1019_
        
         **vagrant@vagrant:~$ cat < /var/log/syslog >/home/vagrant/wclog.1
         vagrant@vagrant:~$ cat /home/vagrant/wclog.1 | wc -l
         29**
         

7. Получится ли находясь в графическом режиме, вывести данные из PTY в какой-либо из эмуляторов TTY? Сможете ли вы наблюдать выводимые данные?

        Да
        
        vagrant@vagrant:~$ cat /var/log/syslog | wc -l 1>/dev/tty1
        
        файл tty.png


7. Выполните команду bash 5>&1. К чему она приведет? Что будет, если вы выполните echo netology > /proc/$$/fd/5? Почему так происходит?

       vagrant@vagrant:~$ echo netology > /proc/$$/fd/
       0    1    2    255  3
       vagrant@vagrant:~$ bash 5>&1
       vagrant@vagrant:~$ echo netology > /proc/$$/fd/
       0    1    2    255  3    5
       vagrant@vagrant:~$ echo netology > /proc/$$/fd/5
       netology
    
       Создает дискриптор и перенаправляет вывод в stfout, соответственно echo в дискритер выводит нам текст 

8. Получится ли в качестве входного потока для pipe использовать только stderr команды, не потеряв при этом отображение stdout на pty? Напоминаем: по умолчанию через pipe передается только stdout команды слева от | на stdin команды справа. Это можно сделать, поменяв стандартные потоки местами через промежуточный новый дескриптор, который вы научились создавать в предыдущем вопросе.

      vagrant@vagrant:~$ ls -al /root/ 4>&2 2>&1 1>&4 | grep Permission
      ls: cannot open directory '/root/': _Permission_ denied

      Созадет новый дискриптор 4 перенаправляем в stderr
      stderr перенаправили в stdout, а его в 4 дискриптор

9. Что выведет команда cat /proc/$$/environ? Как еще можно получить аналогичный по содержанию вывод?


               переменные окружения, также можно env(в читабельном формате)



10. Используя man, опишите что доступно по адресам /proc/<PID>/cmdline, /proc/<PID>/exe.

       строка 226
       /proc/[pid]/cmdline
              This read-only file holds the complete command line for the process, unless the process  is  a  zombie.
              In  the  latter case, there is nothing in this file: that is, a read on this file will return 0 charac‐
              ters.  The command-line arguments appear in this file as a set  of  strings  separated  by  null  bytes
              ('\0'), with a further null byte after the last string.
               - командная строка для процесса (где PID – идентификатор процесса или self);
              _vagrant@vagrant:~$ find /proc/*/cmdline
              /proc/1088/cmdline
              /proc/10/cmdline
              /proc/110/cmdline и тд..._ 


       стр 279
       /proc/<PID>/exe       
              Under  Linux 2.2 and later, this file is a symbolic link containing the actual pathname of the executed
              command.  This symbolic link can be dereferenced normally; attempting to open it  will  open  the  exe‐
              cutable.   You  can  even type /proc/[pid]/exe to run another copy of the same executable that is being
              run by process [pid].  If the pathname has been unlinked, the symbolic link  will  contain  the  string
              '(deleted)'  appended  to the original pathname.  In a multithreaded process, the contents of this sym‐
              bolic link are not  available  if  the  main  thread  has  already  terminated  (typically  by  calling
              pthread_exit(3)).
              /proc/$pid/exe — является символьной ссылкой на исполненный бинарный файл


      /proc/ - виртуальная файловая система, которая монтируется в оперативную память и предоставляет пользователям доступ к внутренним структурам ядра Linux.

11. Узнайте, какую наиболее старшую версию набора инструкций SSE поддерживает ваш процессор с помощью /proc/cpuinfo.

        vagrant@vagrant:~$ cat /proc/cpuinfo | grep sse
                flags           : fpu vme de pse tsc msr pae mce cx8 apic sep mtrr pge mca cmov pat pse36 clflush mmx fxsr sse sse2 ht syscall nx rdtscp lm constant_tsc rep_good nopl xtopology nonstop_tsc cpuid tsc_known_freq pni pclmulqdq ssse3 cx16 pcid
                sse4_1 sse4_2 x2apic movbe popcnt aes xsave avx rdrand hypervisor lahf_lm abm invpcid_single pti fsgsbase avx2 invpcid md_clear flush_l1d
        Ответ: SSE4.2 

12. При открытии нового окна терминала и vagrant ssh создается новая сессия и выделяется pty. Это можно подтвердить командой tty, которая упоминалась в лекции 3.2. Однако:
vagrant@netology1:~$ ssh localhost 'tty'
not a tty
Почитайте, почему так происходит, и как изменить поведение.
    
       vagrant@vagrant:~$ ssh localhost 'cat /var/log/syslog | wc -c'
       vagrant@localhost's password:
       25253
       vagrant@vagrant:~$ ssh localhost 'tty'
       vagrant@localhost's password:
       not a tty

        Происходит то, что это команда, которая выполняет на удал терминале некую команду, не создает терминальную сессию крафическую, 
        соответственно не получает эмулятор графический /dev/pts/, поэтому в персом случае нам вернулся результат, кол-во строк,
        а во втором, то что у нас не выделенно графич эмуляторов(команда tty служит как раз для вывода текущ граф эмулятора)
    
        vagrant@vagrant:~$ tty
        /dev/pts/0  

13. Бывает, что есть необходимость переместить запущенный процесс из одной сессии в другую. Попробуйте сделать это, воспользовавшись reptyr. Например, так можно перенести в screen процесс, который вы запустили по ошибке в обычной SSH-сессии.
    
         vagrant@vagrant:~$ ps aux
         root        2177  0.1  0.0   8112   596 tty1     S+   19:45   0:00 tail -f /var/log/syslog
         vagrant     2179  0.0  0.3  11492  3292 pts/0    R+   19:46   0:00 ps aux
         vagrant@vagrant:~$ reptyr 2177
         -bash: reptyr: command not found
    
          vagrant@vagrant:~$ sudo apt-get install reptyr
          
          vagrant@vagrant:~$ sudo reptyr 2177
      
14. sudo echo string > /root/new_file не даст выполнить перенаправление под обычным пользователем, 
так как перенаправлением занимается процесс shell'а, который запущен без sudo под вашим пользователем.
Для решения данной проблемы можно использовать конструкцию echo string | sudo tee /root/new_file. 
Узнайте что делает команда tee и почему в отличие от sudo echo команда с sudo tee будет работать.

        При использовании sudo вы получаете рутовые привилегии, но перенаправление в bsh («>») работает уже от вашей обычной учётной записи.
        Поэтому sudo echo string > /root/new_file работать не будет.
        Команда tee в Linux нужна для записи вывода любой команды в один или несколько файлов.
        echo string | sudo tee /root/new_file - в этом случае мы в stdout получаем строку и с помощью pipe
        перенаправляем его в teeкоторая отрабатывает с привилиг правами.
        

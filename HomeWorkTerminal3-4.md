<h1>Домашнее задание к занятию "3.4. Операционные системы, лекция 2"</h1>

 

1. На лекции мы познакомились с node_exporter. В демонстрации его исполняемый файл запускался в background. Этого достаточно для демо, но не для настоящей production-системы, где процессы должны находиться под внешним управлением. Используя знания из лекции по systemd, создайте самостоятельно простой unit-файл для node_exporter:
        поместите его в автозагрузку,
        предусмотрите возможность добавления опций к запускаемому процессу через внешний файл (посмотрите, например, на systemctl cat cron),
        удостоверьтесь, что с помощью systemctl процесс корректно стартует, завершается, а после перезагрузки автоматически поднимается.
     
     Файл конфигурации и опционный файл: 
      vagrant@vagrant:~/node_exporter-1.3.1.linux-amd64$ cat /etc/systemd/system/node_exporter.service
   
        [Unit]
          Description=Node Exporter

        [Service]
          User=node_exporter
          EnvironmentFile=/etc/sysconfig/node_exporter
          ExecStart=/usr/sbin/node_exporter $OPTIONS

        [Install]
          WantedBy=multi-user.target

      vagrant@vagrant:~/node_exporter-1.3.1.linux-amd64$ sudo cat /etc/sysconfig/node_exporter
        OPTIONS="--collector.textfile.directory /var/lib/node_exporter/textfile_collector" 
    
      Запущено и проверка статуса:

      vagrant@vagrant:~/node_exporter-1.3.1.linux-amd64$ sudo systemctl status node_exporter

         ● node_exporter.service - Node Exporter

           Loaded: loaded (/etc/systemd/system/node_exporter.service; enabled; vendor preset: enabled)

           Active: active (running) since Sun 2022-01-30 06:15:04 UTC; 28s ago

         Main PID: 12950 (node_exporter)

            Tasks: 5 (limit: 1071)

           Memory: 5.0M

           CGroup: /system.slice/node_exporter.service

                   └─12950 /usr/sbin/node_exporter --collector.textfile.directory /var/lib/node_exporter/textfile_collector

2. Ознакомьтесь с опциями node_exporter и выводом /metrics по-умолчанию. Приведите несколько опций, которые вы бы выбрали для базового мониторинга хоста по CPU, памяти, диску и сети.

        Метрик много, можно сделать выборку по:
           CPU:
                vagrant@vagrant:~/node_exporter-1.3.1.linux-amd64$ curl http://localhost:9100/metrics | grep "node_cpu"
           Memory:
                curl http://localhost:9100/metrics | grep "node_memory_"
           Disk:
                curl http://localhost:9100/metrics | grep "node_disk"
           Network:
                curl http://localhost:9100/metrics | grep node_network

3. Установите в свою виртуальную машину Netdata. Воспользуйтесь готовыми пакетами для установки (sudo apt install -y netdata). После успешной установки:
        в конфигурационном файле /etc/netdata/netdata.conf в секции [web] замените значение с localhost на bind to = 0.0.0.0,
        добавьте в Vagrantfile проброс порта Netdata на свой локальный компьютер и сделайте vagrant reload:

    config.vm.network "forwarded_port", guest: 19999, host: 19999

   После успешной перезагрузки в браузере на своем ПК (не в виртуальной машине) вы должны суметь зайти на localhost:19999. Ознакомьтесь с метриками, которые по умолчанию собираются Netdata и с комментариями, которые даны к этим метрикам.

        netdata.png

   1. Можно ли по выводу dmesg понять, осознает ли ОС, что загружена не на настоящем оборудовании, а на системе виртуализации?

       Да:
        vagrant@vagrant:~$ dmesg | grep virtualiz
   
            [    0.008902] CPU MTRRs all blank - virtualized system.
   
            [    0.149842] Booting paravirtualized kernel on KVM
   
            [    3.544631] systemd[1]: Detected virtualization oracle.

4. Как настроен sysctl fs.nr_open на системе по-умолчанию? Узнайте, что означает этот параметр. Какой другой существующий лимит не позволит достичь такого числа (ulimit --help)?

            vagrant@vagrant:~$ sudo /sbin/sysctl -n fs.nr_open 
              1048576   -   Лимит на количество открытых дескрипторов.

            sysctl -w fs.file-max=500000  - увеличить системно можно так или так:
            ulimit -n 50000
            или в файле  /etc/sysctl.conf  - предел:
            vagrant@vagrant:~$ cat /proc/sys/fs/file-max
               9223372036854775807
            


5. Запустите любой долгоживущий процесс (не ls, который отработает мгновенно, а, например, sleep 1h) в отдельном неймспейсе процессов; покажите, что ваш процесс работает под PID 1 через nsenter. Для простоты работайте в данном задании под root (sudo -i). Под обычным пользователем требуются дополнительные опции (--map-root-user) и т.д.

           root@vagrant:~# unshare -f --pid --mount-proc sleep 1h
            ^Z[1]   Killed                  sleep 1h
            [2]+  Stopped                 unshare -f --pid --mount-proc sleep 1h
           root@vagrant:~# ps aux | grep sleep
            root        1873  0.0  0.0   8080   532 pts/0    T    18:21   0:00 unshare -f --pid --mount-proc sleep 1h
            root        1874  0.0  0.0   8076   592 pts/0    S    18:21   0:00 sleep 1h
            root        1877  0.0  0.0   8900   668 pts/0    S+   18:22   0:00 grep --color=auto sleep
            
            root@vagrant:~# nsenter -t 1874 -p -m
            root@vagrant:/# ps
              PID TTY          TIME CMD
              1 pts/0    00:00:00 sleep
              2 pts/0    00:00:00 bash
              11 pts/0    00:00:00 ps
            

6. Найдите информацию о том, что такое :(){ :|:& };:. Запустите эту команду в своей виртуальной машине Vagrant с Ubuntu 20.04 (это важно, поведение в других ОС не проверялось). Некоторое время все будет "плохо", после чего (минуты) – ОС должна стабилизироваться. Вызов dmesg расскажет, какой механизм помог автоматической стабилизации. Как настроен этот механизм по-умолчанию, и как изменить число процессов, которое можно создать в сессии?

         эта команда является логической бомбой. Она оперирует определением функции с именем ‘:‘, которая вызывает сама себя дважды: один раз на переднем плане и один раз в фоне. Она продолжает своё выполнение снова и снова, пока система не зависнет. 
            
        [ 6419.463875] cgroup: fork rejected by pids controller in /user.slice/user-1000.slice/session-4.scope
        - сработал механизм сgroups(Process Number Controller)
        The process number controller is used to allow a cgroup hierarchy to stop any
        new tasks from being fork()'d or clone()'d after a certain limit is reached.

        Since it is trivial to hit the task limit without hitting any kmemcg limits in
        place, PIDs are a fundamental resource. As such, PID exhaustion must be
        preventable in the scope of a cgroup hierarchy by allowing resource limiting of
        the number of tasks in a cgroup.

        In order to use the `pids` controller, set the maximum number of tasks in
        pids.max (this is not available in the root cgroup for obvious reasons). The
        number of processes currently in the cgroup is given by pids.current.

        vagrant@vagrant:~$ cat /sys/fs/cgroup/pids/user.slice/user-1000.slice/pids.max
        2356
        Максимальное кол-во процессов

        vagrant@vagrant:~$ cat /sys/fs/cgroup/pids/user.slice/user-1000.slice/pids.current
        2351
        Также можно посмотреть top
        Tasks: 2450 total,   1 running, 105 sleeping,   0 stopped, _2344 zombie_ - тут видно что сколько зомби процессов, и что они не растут за максимум
        
        Для увеличения кол-во процессов необходимо поменять конфиг:
        root@vagrant:~# cat /usr/lib/systemd/system/user-.slice.d/10-defaults.conf
        TasksMax=infinity - так будет без ограничений(сейчас занчение 33%)
        


        


    
Домашнее задание к занятию "3.9. Элементы безопасности информационных систем"

    1. Установите Bitwarden плагин для браузера. Зарегестрируйтесь и сохраните несколько паролей.

        bitwarden.png

    2. Установите Google authenticator на мобильный телефон. Настройте вход в Bitwarden акаунт через Google authenticator OTP.

        2Auth.png

    3. Установите apache2, сгенерируйте самоподписанный сертификат, настройте тестовый сайт для работы по HTTPS.

        cert.png

    4. Проверьте на TLS уязвимости произвольный сайт в интернете (кроме сайтов МВД, ФСБ, МинОбр, НацБанк, РосКосмос, РосАтом, РосНАНО и любых госкомпаний, объектов КИИ, ВПК ... и тому подобное).

        vagrant@vagrant:~/testssl.sh-3.0.6$ ./testssl.sh https://hlbprime.com

###########################################################
    testssl.sh       3.0.6 from https://testssl.sh/

      This program is free software. Distribution and
             modification under GPLv2 permitted.
      USAGE w/o ANY WARRANTY. USE IT AT YOUR OWN RISK!

       Please file bugs @ https://testssl.sh/bugs/

###########################################################

    5. Установите на Ubuntu ssh сервер, сгенерируйте новый приватный ключ. Скопируйте свой публичный ключ на другой сервер. Подключитесь к серверу по SSH-ключу.

vagrant@node1:~/.ssh$ ssh vagrant@192.168.213.151
Welcome to Ubuntu 20.04.2 LTS (GNU/Linux 5.4.0-80-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

  System information as of Sun 20 Feb 2022 02:40:48 PM UTC

  System load:  0.0               Processes:             112
  Usage of /:   2.5% of 61.31GB   Users logged in:       1
  Memory usage: 16%               IPv4 address for eth0: 10.0.2.15
  Swap usage:   0%                IPv4 address for eth1: 192.168.213.151


This system is built by the Bento project by Chef Software
More information can be found at https://github.com/chef/bento
Last login: Sun Feb 20 09:39:28 2022 from 10.0.2.2
vagrant@node2:~$

    6. Переименуйте файлы ключей из задания 5. Настройте файл конфигурации SSH клиента, так чтобы вход на удаленный сервер осуществлялся по имени сервера.

vagrant@node1:~/.ssh$ ssh vagrant@node2
Welcome to Ubuntu 20.04.2 LTS (GNU/Linux 5.4.0-80-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

  System information as of Sun 20 Feb 2022 03:13:38 PM UTC

  System load:  0.0               Processes:             112
  Usage of /:   2.5% of 61.31GB   Users logged in:       1
  Memory usage: 17%               IPv4 address for eth0: 10.0.2.15
  Swap usage:   0%                IPv4 address for eth1: 192.168.213.151


This system is built by the Bento project by Chef Software
More information can be found at https://github.com/chef/bento
Last login: Sun Feb 20 15:10:55 2022 from 192.168.213.150
vagrant@node2:~$

    7. Соберите дамп трафика утилитой tcpdump в формате pcap, 100 пакетов. Откройте файл pcap в Wireshark.

vagrant@node2:/$ sudo tcpdump -c 10 port 22 -w ssh.pcap
tcpdump: listening on eth0, link-type EN10MB (Ethernet), capture size 262144 bytes
ex10 packets captured
12 packets received by filter
0 packets dropped by kernel

tcpdump.png
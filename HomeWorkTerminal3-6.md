<h1>Домашнее задание к занятию "3.8. Компьютерные сети, лекция 3"</h1>


1. Подключитесь к публичному маршрутизатору в интернет. Найдите маршрут к вашему публичному IP

        telnet route-views.routeviews.org
        Username: rviews
        show ip route x.x.x.x/32
        show bgp x.x.x.x/32


            route-views>show ip route 178.70.131.13
            Routing entry for 178.70.0.0/15, supernet
            Known via "bgp 6447", distance 20, metric 0
            Tag 3356, type external
            Last update from 4.68.4.46 11:58:15 ago
            Routing Descriptor Blocks:
            * 4.68.4.46, from 4.68.4.46, 11:58:15 ago
            Route metric is 0, traffic share count is 1
            AS Hops 2
            Route tag 3356
            MPLS label: none


2. Создайте dummy0 интерфейс в Ubuntu. Добавьте несколько статических маршрутов. Проверьте таблицу маршрутизации.

        3: dummy0: <BROADCAST,NOARP,UP,LOWER_UP> mtu 1500 qdisc noqueue state UNKNOWN group default qlen 1000
        link/ether 8e:82:9f:2a:b7:f0 brd ff:ff:ff:ff:ff:ff
        inet 10.0.3.1/24 brd 10.0.3.255 scope global dummy0
        valid_lft forever preferred_lft forever
        inet6 fe80::8c82:9fff:fe2a:b7f0/64 scope link

         vagrant@vagrant:~$ sudo ip ro add 192.168.213.0/24 via 10.0.2.15

         vagrant@vagrant:~$ ip r
         default via 10.0.2.2 dev eth0 proto dhcp src 10.0.2.15 metric 100
         10.0.2.0/24 dev eth0 proto kernel scope link src 10.0.2.15
         10.0.2.2 dev eth0 proto dhcp scope link src 10.0.2.15 metric 100
         10.0.3.0/24 dev dummy0 proto kernel scope link src 10.0.3.1
         192.168.213.0/24 via 10.0.2.15 dev eth0

4. Проверьте открытые TCP порты в Ubuntu, какие протоколы и приложения используют эти порты? Приведите несколько примеров.

           vagrant@vagrant:~$ ss -tnlp
           State         Recv-Q        Send-Q               Local Address:Port               Peer Address:Port       Process
           LISTEN        0             4096                 127.0.0.53%lo:53                      0.0.0.0:*
           LISTEN        0             128                        0.0.0.0:22                      0.0.0.0:*
           LISTEN        0             4096                       0.0.0.0:111                     0.0.0.0:*
           LISTEN        0             128                           [::]:22                         [::]:*
           LISTEN        0             4096                          [::]:111                        [::]:*

         22 порт слушвет SSH
         53 порт локальный DNS-сервер (resolver)       
         

5. Проверьте используемые UDP сокеты в Ubuntu, какие протоколы и приложения используют эти порты?

            vagrant@vagrant:~$ ss -unap
            State        Recv-Q        Send-Q                Local Address:Port               Peer Address:Port       Process
            UNCONN       0             0                     127.0.0.53%lo:53                      0.0.0.0:*
            UNCONN       0             0                    10.0.2.15%eth0:68                      0.0.0.0:*
            UNCONN       0             0                           0.0.0.0:111                     0.0.0.0:*
            UNCONN       0             0                              [::]:111                        [::]:*

            53 порт локальный DNS-сервер (resolver)
            68 порт DHCP

6. Используя diagrams.net, создайте L3 диаграмму вашей домашней сети или любой другой сети, с которой вы работали.

            LAN.png
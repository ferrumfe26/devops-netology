<h1>Домашнее задание к занятию "3.6. Компьютерные сети, лекция 1"</h1>

1. Работа c HTTP через телнет.

    Подключитесь утилитой телнет к сайту stackoverflow.com telnet stackoverflow.com 80
    отправьте HTTP запрос

         GET /questions HTTP/1.0
         HOST: stackoverflow.com
         [press enter]
         [press enter]

    В ответе укажите полученный HTTP код, что он означает?

      vagrant@vagrant:~$ telnet stackoverflow.com 80

         HTTP/1.1 301 Moved Permanently
         cache-control: no-cache, no-store, must-revalidate
         location: https://stackoverflow.com/questions
         x-request-guid: b9b8ada8-b9ee-49a8-9272-6b502ad0386d
         feature-policy: microphone 'none'; speaker 'none'
         content-security-policy: upgrade-insecure-requests; frame-ancestors 'self' https://stackexchange.com
         Accept-Ranges: bytes
         Date: Fri, 14 Jan 2022 11:03:41 GMT
         Via: 1.1 varnish
         Connection: close
         X-Served-By: cache-fra19155-FRA
         X-Cache: MISS
         X-Cache-Hits: 0
         X-Timer: S1642158222.838390,VS0,VE92
         Vary: Fastly-SSL
         X-DNS-Prefetch-Control: off
         Set-Cookie: prov=ccac173a-2b1d-73b7-0e58-ae3ed38f6141; domain=.stackoverflow.com; expires=Fri, 01-Jan-2055 00:00:00 GMT; path=/; HttpOnly
   
   301 код ответа, это редирект на https://stackoverflow.com/questions

2. Повторите задание 1 в браузере, используя консоль разработчика F12.

    откройте вкладку Network
    отправьте запрос http://stackoverflow.com
    найдите первый ответ HTTP сервера, откройте вкладку Headers
    укажите в ответе полученный HTTP код.

         307 Documement /Redirect

    проверьте время загрузки страницы, какой запрос обрабатывался дольше всего?

         345ms document https://stackoverflow.com

    приложите скриншот консоли браузера в ответ.

      stackoverflow.png

3. Какой IP адрес у вас в интернете?

      178.70.131.13      

4. Какому провайдеру принадлежит ваш IP адрес? Какой автономной системе AS? Воспользуйтесь утилитой whois

      Ростелеком, AS12389б 

5. Через какие сети проходит пакет, отправленный с вашего компьютера на адрес 8.8.8.8? Через какие AS? Воспользуйтесь утилитой traceroute

           vagrant@vagrant:~$  traceroute -AIn 8.8.8.8
   
                 traceroute to 8.8.8.8 (8.8.8.8), 30 hops max, 60 byte packets
                 1  10.0.2.2 [*]  0.248 ms  0.184 ms  0.159 ms
                 2  192.168.213.1 [*]  1.363 ms  1.644 ms  1.617 ms
                 3  95.55.24.1 [AS12389]  3.775 ms  4.652 ms  5.548 ms
                 4  212.48.204.164 [AS12389]  5.517 ms  5.648 ms  5.623 ms
                 5  188.254.2.6 [AS12389]  9.014 ms  9.115 ms  9.349 ms
                 6  87.226.194.47 [AS12389]  8.407 ms  6.922 ms  7.095 ms
                 7  74.125.244.181 [AS15169]  7.522 ms  7.215 ms  8.497 ms
                 8  72.14.232.84 [AS15169]  8.466 ms  8.689 ms  8.917 ms
                 9  142.251.61.219 [AS15169]  11.921 ms  11.956 ms  12.327 ms
                 10  172.253.79.113 [AS15169]  11.328 ms  11.575 ms  11.534 ms
                 11  * * *
                 12  * * *
                 13  * * *
                 14  * * *
                 15  * * *
                 16  * * *
                 17  * * *
                 18  * * *
                 19  * * *
                 20  8.8.8.8 [AS15169]  14.923 ms  14.920 ms  15.066 ms


6. Повторите задание 5 в утилите mtr. На каком участке наибольшая задержка - delay?

     AS15169  72.14.232.84                                                     0.0%    89    8.0   9.5   7.8  25.1   2.9

7. Какие DNS сервера отвечают за доменное имя dns.google? Какие A записи? воспользуйтесь утилитой dig

         Addresses:  2001:4860:4860::8844
                 2001:4860:4860::8888
                 8.8.8.8
                 8.8.4.4

             vagrant@vagrant:~$ dig +short NS dns.google
                  ns1.zdns.google.
                  ns3.zdns.google.
                  ns4.zdns.google.
                  ns2.zdns.google.

8. Проверьте PTR записи для IP адресов из задания 7. Какое доменное имя привязано к IP? воспользуйтесь утилитой dig

           vagrant@vagrant:~$ dig -x 8.8.8.8 | grep arpa
               8.8.8.8.in-addr.arpa.   7175    IN      PTR     dns.google.
           
           vagrant@vagrant:~$ dig -x 8.8.4.4 | grep arpa

               4.4.8.8.in-addr.arpa.   14400   IN      PTR     dns.google.


<h1>Домашнее задание к занятию "3.5. Файловые системы"</h1>

1. Узнайте о sparse (разряженных) файлах.
        
        Почитал, напоминает динамические диски в виртуальных машинах. Возможно там так и реализовано. 

2.Могут ли файлы, являющиеся жесткой ссылкой на один объект, иметь разные права доступа и владельца? Почему?

    Нет, потому что хардлинки это ссылки на один и тот же объект и они имеют одинаковые права. 

3. Сделайте vagrant destroy на имеющийся инстанс Ubuntu. Замените содержимое Vagrantfile следующим:


       Vagrant.configure("2") do |config|
         config.vm.box = "bento/ubuntu-20.04"
         config.vm.provider :virtualbox do |vb|
           lvm_experiments_disk0_path = "/tmp/lvm_experiments_disk0.vmdk"
           lvm_experiments_disk1_path = "/tmp/lvm_experiments_disk1.vmdk"
           vb.customize ['createmedium', '--filename', lvm_experiments_disk0_path, '--size', 2560]
           vb.customize ['createmedium', '--filename', lvm_experiments_disk1_path, '--size', 2560]
           vb.customize ['storageattach', :id, '--storagectl', 'SATA Controller', '--port', 1, '--device', 0, '--type', 'hdd', '--medium', lvm_experiments_disk0_path]
           vb.customize ['storageattach', :id, '--storagectl', 'SATA Controller', '--port', 2, '--device', 0, '--type', 'hdd', '--medium', lvm_experiments_disk1_path]
         end
       end
    
       Данная конфигурация создаст новую виртуальную машину с двумя дополнительными неразмеченными дисками по 2.5 Гб.
    
    vagrant@vagrant:~$ lsblk
    NAME                 MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
    sda                    8:0    0   64G  0 disk
    ├─sda1                 8:1    0  512M  0 part /boot/efi
    ├─sda2                 8:2    0    1K  0 part
    └─sda5                 8:5    0 63.5G  0 part
      ├─vgvagrant-root   253:0    0 62.6G  0 lvm  /
      └─vgvagrant-swap_1 253:1    0  980M  0 lvm  [SWAP]
    sdb                    8:16   0  2.5G  0 disk
    sdc                    8:32   0  2.5G  0 disk
    

4. Используя fdisk, разбейте первый диск на 2 раздела: 2 Гб, оставшееся пространство.


    _vagrant@vagrant:~$ lsblk
    NAME                 MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
    sda                    8:0    0   64G  0 disk
    ├─sda1                 8:1    0  512M  0 part /boot/efi
    ├─sda2                 8:2    0    1K  0 part
    └─sda5                 8:5    0 63.5G  0 part
      ├─vgvagrant-root   253:0    0 62.6G  0 lvm  /
      └─vgvagrant-swap_1 253:1    0  980M  0 lvm  [SWAP]
    sdb                    8:16   0  2.5G  0 disk
    ├─sdb1                 8:17   0    2G  0 part
    └─sdb2                 8:18   0  500M  0 part_

5. Используя sfdisk, перенесите данную таблицу разделов на второй диск.

    
       vagrant@vagrant:~$ sudo sfdisk -d /dev/sdb | sudo sfdisk --force /dev/sdc

       _vagrant@vagrant:~$ lsblk
         NAME                 MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
         sda                    8:0    0   64G  0 disk
         ├─sda1                 8:1    0  512M  0 part /boot/efi
         ├─sda2                 8:2    0    1K  0 part
         └─sda5                 8:5    0 63.5G  0 part
           ├─vgvagrant-root   253:0    0 62.6G  0 lvm  /
           └─vgvagrant-swap_1 253:1    0  980M  0 lvm  [SWAP]
         sdb                    8:16   0  2.5G  0 disk
         ├─sdb1                 8:17   0    2G  0 part
         └─sdb2                 8:18   0  500M  0 part
         sdc                    8:32   0  2.5G  0 disk
         ├─sdc1                 8:33   0    2G  0 part
         └─sdc2                 8:34   0  500M  0 part_


6. Соберите mdadm RAID1 на паре разделов 2 Гб.

        sudo mdadm --create /dev/md0 --level=1 --raid-devices=2 /dev/sdb1 /dev/sdc1

7. Соберите mdadm RAID0 на второй паре маленьких разделов.


        sudo mdadm --create /dev/md0 --level=1 --raid-devices=2 /dev/sdb1 /dev/sdc1

8. Создайте 2 независимых PV на получившихся md-устройствах.


    vagrant@vagrant:~$ sudo pvcreate /dev/md1 /dev/md0
    Physical volume "/dev/md1" successfully created.
    Physical volume "/dev/md0" successfully created.

9. Создайте общую volume-group на этих двух PV.


       vgcreate vg1 /dev/md1 /dev/md0

10. Создайте LV размером 100 Мб, указав его расположение на PV с RAID0.


           vagrant@vagrant:~$ sudo lvcreate -L 100M vg1 /dev/md0
           Logical volume "lvol0" created.

11. Создайте mkfs.ext4 ФС на получившемся LV.


          Vagrant@vagrant:~$ sudo mkfs.ext4 /dev/vg1/lvol0
          mke2fs 1.45.5 (07-Jan-2020)
          Creating filesystem with 25600 4k blocks and 25600 inodes

          Allocating group tables: done
          Writing inode tables: done
          Creating journal (1024 blocks): done
          Writing superblocks and filesystem accounting information: done

12. Смонтируйте этот раздел в любую директорию, например, /tmp/new.


    vagrant@vagrant:~$ mkdir /tmp/new
    vagrant@vagrant:~$ sudo mount /dev/vg1/lvol0 /tmp/new

13. Поместите туда тестовый файл, например wget https://mirror.yandex.ru/ubuntu/ls-lR.gz -O /tmp/new/test.gz.


       vagrant@vagrant:~$ sudo wget https://mirror.yandex.ru/ubuntu/ls-lR.gz -O /tmp/new/test.gz    
    
14. Прикрепите вывод lsblk.


      _vagrant@vagrant:~$ lsblk
           NAME                 MAJ:MIN RM  SIZE RO TYPE  MOUNTPOINT
           sda                    8:0    0   64G  0 disk

           ├─sda1                 8:1    0  512M  0 part  /boot/efi

           ├─sda2                 8:2    0    1K  0 part

           └─sda5                 8:5    0 63.5G  0 part

           ├─vgvagrant-root   253:0    0 62.6G  0 lvm   /

           └─vgvagrant-swap_1 253:1    0  980M  0 lvm   [SWAP]

           sdb                    8:16   0  2.5G  0 disk

           ├─sdb1                 8:17   0    2G  0 part

           │ └─md0                9:0    0    2G  0 raid1

           │   └─vg1-lvol0      253:2    0  100M  0 lvm   /tmp/new

           └─sdb2                 8:18   0  500M  0 part

              └─md1                9:1    0  996M  0 raid0

           sdc                    8:32   0  2.5G  0 disk

           ├─sdc1                 8:33   0    2G  0 part

           │ └─md0                9:0    0    2G  0 raid1

           │   └─vg1-lvol0      253:2    0  100M  0 lvm   /tmp/new

           └─sdc2                 8:34   0  500M  0 part

              └─md1                9:1    0  996M  0 raid0_   


15. Протестируйте целостность файла:


    root@vagrant:~# gzip -t /tmp/new/test.gz
    root@vagrant:~# echo $?
    0
16. Используя pvmove, переместите содержимое PV с RAID0 на RAID1.


           vagrant@vagrant:~$ sudo pvmove /dev/md0
             /dev/md0: Moved: 24.00%
             /dev/md0: Moved: 100.00%_
           
            vagrant@vagrant:~$ lsblk
             NAME                 MAJ:MIN RM  SIZE RO TYPE  MOUNTPOINT

             sda                    8:0    0   64G  0 disk

             ├─sda1                 8:1    0  512M  0 part  /boot/efi

             ├─sda2                 8:2    0    1K  0 part

             └─sda5                 8:5    0 63.5G  0 part

               ├─vgvagrant-root   253:0    0 62.6G  0 lvm   /

               └─vgvagrant-swap_1 253:1    0  980M  0 lvm   [SWAP]

             sdb                    8:16   0  2.5G  0 disk

             ├─sdb1                 8:17   0    2G  0 part

             │ └─md0                9:0    0    2G  0 raid1

             └─sdb2                 8:18   0  500M  0 part

               └─md1                9:1    0  996M  0 raid0

                └─vg1-lvol0      253:2    0  100M  0 lvm   /tmp/new

             sdc                    8:32   0  2.5G  0 disk

             ├─sdc1                 8:33   0    2G  0 part

             │ └─md0                9:0    0    2G  0 raid1

             └─sdc2                 8:34   0  500M  0 part

               └─md1                9:1    0  996M  0 raid0

                └─vg1-lvol0      253:2    0  100M  0 lvm   /tmp/new


17. Сделайте --fail на устройство в вашем RAID1 md.


       vagrant@vagrant:~$ sudo mdadm /dev/md127 --fail /dev/sdc1(после перезагрузки поменялось имя райда)
       mdadm: set /dev/sdc1 faulty in /dev/md127 
 
 

       vagrant@vagrant:~$ sudo mdadm -D /dev/md127 
        ...
        Number   Major   Minor   RaidDevice State
       0       8       17        0      active sync   /dev/sdb1
       -       0        0        1      removed

       1       8       33        -      faulty   /dev/sdc1

18. Подтвердите выводом dmesg, что RAID1 работает в деградированном состоянии.


        vagrant@vagrant:~$ dmesg |grep md127
        [    2.387643] md/raid1:md127: active with 2 out of 2 mirrors
        [    2.387661] md127: detected capacity change from 0 to 2144337920
        [ 1272.879850] md/raid1:md127: Disk failure on sdc1, disabling device.
               md/raid1:md127: Operation continuing on 1 devices.

19. Протестируйте целостность файла, несмотря на "сбойный" диск он должен продолжать быть доступен:


    root@vagrant:~# gzip -t /tmp/new/test.gz
    root@vagrant:~# echo $?
    0

        vagrant@vagrant:~$ gzip -t /tmp/new/test.gz
        vagrant@vagrant:~$ echo $?
        0

20. Погасите тестовый хост, vagrant destroy.
    
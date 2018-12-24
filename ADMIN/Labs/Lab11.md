# Лабораторная работа 11. Инсталляция программного обеспечения
### Упражнение 11.1. Управление программным обеспечением

Пользуйтесь только менеджероми пакетов **dpkg**.

1. Получите список установленного программного обеспечения в системе:

   ```console
   root@bc30138:~$ dpkg -l
   Desired=Unknown/Install/Remove/Purge/Hold
   | Status=Not/Inst/Conf-files/Unpacked/halF-conf/Half-inst/trig-aWait/Trig-pend
   |/ Err?=(none)/Reinst-required (Status,Err: uppercase=bad)
   ||/ Name           Version      Architecture Description
   +++-==============-============-============-=================================
   ii  accountsservic 0.6.43-1     amd64        query and manipulate user account
   ii  acl            2.2.52-3+b1  amd64        Access control list utilities
   ii  adduser        3.115        all          add and remove users and groups
   ii  adwaita-icon-t 3.22.0-1+deb all          default icon theme of GNOME
   ii  anacron        2.3-24       amd64        cron-like program that doesn't go
   ii  apache2        2.4.25-3+deb amd64        Apache HTTP Server
   ii  apache2-bin    2.4.25-3+deb amd64        Apache HTTP Server (modules and o
   ii  apache2-data   2.4.25-3+deb all          Apache HTTP Server (common files)
   ii  apache2-utils  2.4.25-3+deb amd64        Apache HTTP Server (utility progr
   ii  appstream      0.10.6-2     amd64        Software component metadata manag
   ii  apt            1.4.8        amd64        commandline package manager
   ii  apt-listchange 3.10         all          package change history notificati
   ii  apt-utils      1.4.8        amd64        package management related utilit
   ...
   ```
2. Получите расширенную информацию о пакетах подсистемы печати (ключевое слово - **cups, Common UNIX Printing System**) и подсистемы журнализации событий (ключевые слова - **sysklog, rsyslog**):

   **CUPS**:
   ```console
      root@bc30138:~$ dpkg -l cups
   Desired=Unknown/Install/Remove/Purge/Hold
   | Status=Not/Inst/Conf-files/Unpacked/halF-conf/Half-inst/trig-aWait/Trig-pend
   |/ Err?=(none)/Reinst-required (Status,Err: uppercase=bad)
   ||/ Name           Version      Architecture Description
   +++-==============-============-============-=================================
   ii  cups           2.2.1-8+deb9 amd64        Common UNIX Printing System(tm) -
   ```

   **rsyslog**:
   ```console
   root@bc30138:~$ dpkg -l rsyslog
   Desired=Unknown/Install/Remove/Purge/Hold
   | Status=Not/Inst/Conf-files/Unpacked/halF-conf/Half-inst/trig-aWait/Trig-pend
   |/ Err?=(none)/Reinst-required (Status,Err: uppercase=bad)
   ||/ Name           Version      Architecture Description
   +++-==============-============-============-=================================
   ii  rsyslog        8.24.0-1     amd64        reliable system and kernel loggin
   ```
   
3. Загрузите из соотвествующего репозитория на сервере mirror.yandex.ru пакет(ы) с документацией по ядру операционной системы и установите их в систему:
   
   ```console
   root@bc30138:~$ wget https://mirror.yandex.ru/debian/pool/main/d/debian-faq/debian-faq-ru_8.1_all.deb   
   --2018-12-23 22:02:02--  https://mirror.yandex.ru/debian/pool/main/d/debian-faq/debian-faq-ru_8.1_all.deb
   Resolving mirror.yandex.ru (mirror.yandex.ru)... 213.180.204.183, 2a02:6b8::183
   Connecting to mirror.yandex.ru (mirror.yandex.ru)|213.180.204.183|:443... connected.
   HTTP request sent, awaiting response... 200 OK
   Length: 771050 (753K) [application/octet-stream]
   Saving to: ‘debian-faq-ru_8.1_all.deb’

   debian-faq-ru_8.1_ 100%[==============>] 752.98K  --.-KB/s    in 0.08s   

   2018-12-23 22:02:03 (8.67 MB/s) - ‘debian-faq-ru_8.1_all.deb’ saved [771050/771050]
   ```

   Файл будет сохранен в текущую директорию:
   ```console
   root@bc30138:~$ ls
   debian-faq-ru_8.1_all.deb  Music     Temp	    Videos
   Desktop			   PDF	     Templates	    xorg.conf.new
   Documents		   Pictures  TEST
   Downloads		   Public    TEST_TRUST_ME
   index.html		   share     tutor
   ```

4. Получите список файлов, находящихся в установленном (в предыдущем пункте) пакете, удостоверьтесь в присутствии перечисленных файлов в системе:

   ```console
   dpkg -i debian-faq-ru_8.1_all.deb 
   Selecting previously unselected package debian-faq-ru.
   (Reading database ... 145226 files and directories currently installed.)
   Preparing to unpack debian-faq-ru_8.1_all.deb ...
   Unpacking debian-faq-ru (8.1) ...
   Setting up debian-faq-ru (8.1) ...
   ```

5. Удалите пакет архиватора **zip** из системы:
   
   ```console
   root@bc30138:~$ dpkg -r zip
   (Reading database ... 145267 files and directories currently installed.)
   Removing zip (3.0-11+b1) ...
   Processing triggers for man-db (2.7.6.1-2) ...
   ```

### Упражнение 11.2. Управление зависимостями пакетов программного обеспечения
Пользуйтесь только менеджером зависимостей пакетов **apt**

1. Подключите соотвествующий сетевой репозитарий пакетов с сервера mirror.yandex.ru к системе управления зависимостями и обновите локальную базу данных репозитария:
   
   Настроить список используемых сетевых репозиториев можно в файле **/etc/apt/sources.list**:
   ```console
   # deb cdrom:[Debian GNU/Linux 9.5.0 _Stretch_ - Official amd64 NETINST 20$

   #deb cdrom:[Debian GNU/Linux 9.5.0 _Stretch_ - Official amd64 NETINST 201$

   deb http://mirror.yandex.ru/debian/ stretch main non-free contrib
   deb-src http://mirror.yandex.ru/debian/ stretch main non-free contrib

   deb http://security.debian.org/debian-security stretch/updates main contr$
   deb-src http://security.debian.org/debian-security stretch/updates main c$

   # stretch-updates, previously known as 'volatile'
   deb http://mirror.yandex.ru/debian/ stretch-updates main contrib non-free
   deb-src http://mirror.yandex.ru/debian/ stretch-updates main contrib non-$
   ```

   Обновляем базу:
   ```console
   root@bc30138:~$ apt update 
   Ign:1 http://mirror.yandex.ru/debian stretch InRelease
   Get:2 http://mirror.yandex.ru/debian stretch-updates InRelease [91.0 kB]
   Hit:3 http://mirror.yandex.ru/debian stretch Release                     
   Hit:4 http://security.debian.org/debian-security stretch/updates InRelease
   Get:6 http://mirror.yandex.ru/debian stretch/main amd64 DEP-11 Metadata [3,066 kB]
   Get:7 http://mirror.yandex.ru/debian stretch/main DEP-11 64x64 Icons [6,804 kB]
   Get:8 http://mirror.yandex.ru/debian stretch/main DEP-11 128x128 Icons [15.8 MB]
   Get:9 http://mirror.yandex.ru/debian stretch/non-free amd64 DEP-11 Metadata [7,180 B]
   Get:10 http://mirror.yandex.ru/debian stretch/non-free DEP-11 64x64 Icons [30.0 kB]
   Get:11 http://mirror.yandex.ru/debian stretch/non-free DEP-11 128x128 Icons [85.2 kB]
   Get:12 http://mirror.yandex.ru/debian stretch/contrib amd64 DEP-11 Metadata [7,308 B]
   Get:13 http://mirror.yandex.ru/debian stretch/contrib DEP-11 64x64 Icons [100 kB]
   Get:14 http://mirror.yandex.ru/debian stretch/contrib DEP-11 128x128 Icons [254 kB]
   Fetched 26.2 MB in 13s (1,971 kB/s)                                      
   Reading package lists... Done
   Building dependency tree       
   Reading state information... Done
   1 package can be upgraded. Run 'apt list --upgradable' to see it.
   root@bc30138:~$ 
   ```

   Как можно заметить - на данной виртуальной машине обновление было проведено не зря.

2. Инсталлируйте пакет архиватора **zip** в систему:

   ```console
   root@bc30138:~$ apt install zip
   ```

3. Установите пакет терминального мультиплексора screen в систему:

   ```console
   root@bc30138:~$ apt install screen
   ```

4. Проведите обновление всех пакетов до последних версий:

   ```console
   root@bc30138:~$ apt update
   ...
   root@bc30138:~$ apt upgrade 
   ...
   ```        
   
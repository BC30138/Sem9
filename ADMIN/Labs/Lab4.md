# Лабораторная работа 4: Начальная загрузка и останов системы
### Упражнение 4.1. Этапы начальной загрузки
1. Загрузите операционную систему *Linux*, убрав параметр **quite** из параметров загрузчика.

    (Как это делается в лаб.3)
   
2. Проследите за загрузкой и инициализацией модулей ядра, монтированием корневой и других файловой систем, запуском прародителя процессов **init** и служб операционной системы.

    <!-- ![quiet](https://github.com/BC30138/Studying/blob/master/ADMIN/Labs/Screens/quiet.png?raw=true) -->

    Здесь мы наблюдаем следующее: 
    - множество строк процесса загрузки ядра, драйверов устройств
    - монтирование файловых систем происходит в виде: 
        ```console
        Mounting root file system ... 
        ```
    - Дистрибутив Debian 9 использует реализацию команды init в виде systemd (ID процесса 1).

3. Ознакомьтесь с конфигурацией прародителя процессов **init** и организацией сценариев запуска системы. Отметьте текущий уровень исполнения системы (при загрузке по умолчанию) и конфигурационные файлы, участвующие в загрузке на этом уровне исполнения).

    Для отображения текущего путя поиска сценария, зависимостей и модулей для команды **systemd** 
    используем следующую команду:

    ```console
    root@bc30138:~$ systemctl -p UnitPath show
    UnitPath=/run/systemd/transient /etc/systemd/system /run/systemd/system /run/system
    ```

    Узнаем текущий уровень исполнения системы: 

    ```console
    root@bc30138:~$ who -r
    run-level 5  2018-12-16 13:40
    ```

    Уровень выводится из-за аргумента -r. 

    Уровень 5 - это конец загрузки и приглашение в систему. Время показывает когда был осуществлен переход на этот уровень.

### Упражнение 4.2. Уровни исполнения системы
1. Загрузите операционную систему в однопользовательском (**single**) уровне исполнения.
   
   (Как это делается в лаб.3)

    Узнаем текущий уровень исполнения системы: 

    ```console
    root@bc30138:~$ who -r
    run-level 1  2018-12-16 14:06
    ```

    Первый уровень исполнения предназначен сугубо для работ по восстановлению и администрированию системы. На этом уровне не запущен ни один сервис и из 
    пользователей может работать только *root*.

2. Завершите сеанс однопользовательского режима работы. Проследите за реакцией системы.

    Для выхода из однопользовательского режима можно либо ввести команду **exit**, 
    либо воспользоваться макросом **ctrl+d**, который выполнит эту же команду.

    Система переходит на экран входа стандартный для многопользовательского режима. 
    Узнаем уровень после входа:

    ```console
    root@bc30138:~$ who -r
         run-level 5  2018-12-16 14:13                   last=1
    ```

    Здесь мы наблюдаем, что уровень стал 5 и добавилась пометка о том, что предыдущий уровень был 1. 

3. Переведите систему на первый уровень исполнения.Проследите за реакцией системы.
   
    Для перехода на первый уровень используем следующую команду:

    ```console
    root@bc30138:~$ telinit 1
    ```

    После чего все процессы завершаются и система переходит на экран авторизации однопользовательского режима.

    Посмотрим уровень системы:

    ```console
    root@bc30138:~$ who -r
         run-level 1  2018-12-16 14:16                   last=5
    ``` 

    Уровень стал снова 1. Пометка last, указывает, что мы перешли с уровня 5.


4. Возвратите систему на уровень исполнения по умолчанию. Проследите за реакцией системы.
   
    Вернуться можно с помощью перезагрузки системы (если воспользоваться командой exit может наблюдаться некорректное поведение).

### Упражнение 4.3. Командные файлы начальной загрузки
1. Ознакомьтесь с командными сценариями начальной загрузки **/etc/init.d/rc**, **/etc/init.d/rcS** и зафиксируйте смысл основных действий, выполняемых этими сценариями (из комментариев в сценариях).
   
   Сценарии для выполнения на различных уровнях расположены в **/etc/init.d/**

    ```console
    root@bc30138:/etc/init.d$ ls
    anacron		    cups		kmod	       procps   udev
    avahi-daemon        cups-browsed	lvm2	       rsyslog  x11-common
    bluetoot            dbus		lvm2-lvmetad   saned
    console-setup.sh    hwclock.sh	        lvm2-lvmpolld  ssh
    cron		    keyboard-setup.sh   networking     sudo
    ```

    Сами же списки сценариев для каждого уровня в виде ссылок на объекты из каталога **/etc/init.d/** находятся в каталогах **rc0.d ... rc6.d** (7 штук, соответственно для каждого уровня системы).

2. Ознакомьтесь с командными сценариями запуска и останова служб **/etc/init.d/***, зафиксируйте смысл основных действий, выполняемых этими сценариями (из комментариев в сценариях).
   
   Рассмотрим каталог применяемых сценариев для уровней 5 и 1:

    ```console
    root@bc30138:/etc/rc5.d$ ls -la
    total 8
    drwxr-xr-x  2 root root 4096 Dec 16 10:24 .
    drwxr-xr-x 96 root root 4096 Dec 16 14:26 ..
    lrwxrwxrwx  1 root root   17 Sep 14 04:20 S01anacron -> ../init.d/anacron
    lrwxrwxrwx  1 root root   22 Sep 14 04:20 S01avahi-daemon -> ../init.d/avahi-daemon
    lrwxrwxrwx  1 root root   19 Sep 14 04:20 S01bluetooth -> ../init.d/bluetooth
    lrwxrwxrwx  1 root root   26 Sep 14 04:02 S01console-setup.sh -> ../init.d/console-setup.sh
    lrwxrwxrwx  1 root root   14 Sep 14 03:57 S01cron -> ../init.d/cron
    lrwxrwxrwx  1 root root   14 Sep 14 04:20 S01cups -> ../init.d/cups
    lrwxrwxrwx  1 root root   22 Sep 14 04:20 S01cups-browsed -> ../init.d/cups-browsed
    lrwxrwxrwx  1 root root   14 Sep 14 04:20 S01dbus -> ../init.d/dbus
    lrwxrwxrwx  1 root root   22 Oct 12 04:03 S01lvm2-lvmetad -> ../init.d/lvm2-lvmetad
    lrwxrwxrwx  1 root root   23 Oct 12 04:03 S01lvm2-lvmpolld -> ../init.d/lvm2-lvmpolld
    lrwxrwxrwx  1 root root   17 Sep 14 03:57 S01rsyslog -> ../init.d/rsyslog
    lrwxrwxrwx  1 root root   15 Sep 14 04:20 S01saned -> ../init.d/saned
    lrwxrwxrwx  1 root root   13 Dec 16 10:24 S01ssh -> ../init.d/ssh
    root@bc30138:/etc/rc5.d$ ls -la /etc/rc1.d
    total 8
    drwxr-xr-x  2 root root 4096 Oct 12 04:03 .
    drwxr-xr-x 96 root root 4096 Dec 16 14:26 ..
    lrwxrwxrwx  1 root root   22 Sep 14 04:20 K01avahi-daemon -> ../init.d/avahi-daemon
    lrwxrwxrwx  1 root root   19 Sep 14 04:20 K01bluetooth -> ../init.d/bluetooth
    lrwxrwxrwx  1 root root   14 Sep 14 04:20 K01cups -> ../init.d/cups
    lrwxrwxrwx  1 root root   22 Sep 14 04:20 K01cups-browsed -> ../init.d/cups-browsed
    lrwxrwxrwx  1 root root   22 Oct 12 04:03 K01lvm2-lvmetad -> ../init.d/lvm2-lvmetad
    lrwxrwxrwx  1 root root   23 Oct 12 04:03 K01lvm2-lvmpolld -> ../init.d/lvm2-lvmpolld
    lrwxrwxrwx  1 root root   17 Sep 14 03:57 K01rsyslog -> ../init.d/rsyslog
    lrwxrwxrwx  1 root root   15 Sep 14 04:20 K01saned -> ../init.d/saned
    ```

    Сценарии которые должны быть применены здесь отображены символьными ссылками. Буква перед ссылкой (S или K) говорит о том, что надо сделать с сценарием: начать (Start) или остановить (Kill). Цифар после буквы характеризует порядок выполнения. Каждый сценарий из папки **/etc/init.d** - это скрипт.
3. Остановите службу **cron** и запустите службу **exim4**.
    
    Остановить службу **cron** можно с помощью команды **service**:
    
    ```console
    root@bc30138:~$ service cron stop
    root@bc30138:~$ service cron status 
    ● cron.service - Regular background program processing daemon
        Loaded: loaded (/lib/systemd/system/cron.service; enabled; vendor preset: enabled)
        Active: inactive (dead) since Sun 2018-12-16 14:57:22 EST; 1min 13s ago
          Docs: man:cron(8)
       Process: 443 ExecStart=/usr/sbin/cron -f $EXTRA_OPTS (code=killed, signal=TERM)
      Main PID: 443 (code=killed, signal=TERM)

    Dec 16 14:26:41 bc30138 systemd[1]: Started Regular background program processing daemon.
    Dec 16 14:26:41 bc30138 cron[443]: (CRON) INFO (pidfile fd = 3)
    Dec 16 14:26:41 bc30138 cron[443]: (CRON) INFO (Running @reboot jobs)
    Dec 16 14:57:22 bc30138 systemd[1]: Stopping Regular background program processing daemon...
    Dec 16 14:57:22 bc30138 systemd[1]: Stopped Regular background program processing daemon.
    ```

    Либо используя сценарий:
    ```console
    root@bc30138:/etc/init.d$ ./cron stop
    [ ok ] Stopping cron (via systemctl): cron.service.
    ```

    Запустить **exim4** можно также с помошью сценария:
    ```console
    root@bc30138:/etc/init.d$ ./exim4 start
    [ ok ] Starting exim4 (via systemctl): exim4.service.
    ```

4. Ознакомьтесь с конфигурационными файлами командных сценариев начальной загрузки: **/etc/default/***, зафиксируйте названия служб, имеющих настроечные параметры в данных файлах.

    Рассмотрим содержимое каталога **/etc/default**:
    ```console
    root@bc30138:/$ ls /etc/default/
    anacron       bsdmainutils   cron   grub      locale	  rsyslog  useradd
    avahi-daemon  console-setup  dbus   hwclock   networking  saned
    bluetooth     crda	     exim4  keyboard  nss	  ssh
    ```

    Здесь храняться файлы с аргументами для запуска служб. 

    Например посмотрим часть файла **grub** (полезно):

    ```console
    GRUB_DEFAULT=0
    GRUB_TIMEOUT=5
    GRUB_DISTRIBUTOR=`lsb_release -i -s 2> /dev/null || echo Debian`
    GRUB_CMDLINE_LINUX_DEFAULT="quiet nomodeset vga=795"
    GRUB_CMDLINE_LINUX=""
    ```

    Это параметры конфигурации GRUB.

### Упражнение 4.4. Перезагрузка и останов системы
1. Выполните перезагрузку системы. Проследите за реакцией системы.

    Перезагрузка осуществляется следующей командой:
    ```console
    root@bc30138:/$ reboot
    ```
    
    При перезагрузке мы видим как срабатывают сценарии останова всех служб, после чего система запускается заново.
   
2. Выполните останов системы. Проследите за реакцией системы.
   
   Происходит тоже самое, что и при перезагрузке, за исключением того, что система не включается заново.

3. Выполните отложенный останов системы (1 минута) с оповещением пользователей. Проследите за реакцией системы.

    Запланировать останов системы можно с помошью следующей команды: 
    ```console
    root@bc30138:~$ shutdown -h 1
    Shutdown scheduled for Sun 2018-12-16 15:30:37 EST, use 'shutdown -c' to cancel.
    ```

    Система произвела останов через одну минуту.
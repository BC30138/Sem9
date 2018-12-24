# Лабораторная работа 10. Графическая подсистема X Window System
### Упражнение 10.1. X сервер
1. Сконфигурируйте **X** сервер для работы с глубиной цвета 24bpp по умолчанию, разрешением 1024x768 по умолчанию и возможностью переключения в разрешения 800x600 и 640x480

   Сконфигурируем шаблон **xorg.conf.new**:
   ```console
   root@bc30138:~$ Xorg -configure 

   X.Org X Server 1.19.2
   Release Date: 2017-03-02
   X Protocol Version 11, Revision 0
   Build Operating System: Linux 4.9.0-8-amd64 x86_64 Debian
   Current Operating System: Linux bc30138 4.9.0-7-amd64 #1 SMP Debian 4.9.110-3+deb9u2 (2018-08-13) x86_64
   Kernel command line: BOOT_IMAGE=/boot/vmlinuz-4.9.0-7-amd64 root=UUID=973239f6-1805-4b4e-b539-429e33550ecc ro quiet nomodeset vga=795
   Build Date: 03 November 2018  03:09:11AM
   xorg-server 2:1.19.2-1+deb9u5 (https://www.debian.org/support) 
   Current version of pixman: 0.34.0
   	Before reporting problems, check http://wiki.x.org
   	to make sure that you have the latest version.
   Markers: (--) probed, (**) from config file, (==) default setting,
   	(++) from command line, (!!) notice, (II) informational,
   	(WW) warning, (EE) error, (NI) not implemented, (??) unknown.
   (==) Log file: "/var/log/Xorg.0.log", Time: Sun Dec 23 20:22:17 2018
   List of video drivers:
   	amdgpu
   	ati
   	intel
   	nouveau
   	qxl
   	radeon
   	vesa
   	fbdev
   	modesetting
   	vmware
   (++) Using config file: "/root/xorg.conf.new"
   (==) Using system config directory "/usr/share/X11/xorg.conf.d"


   Xorg detected your mouse at device /dev/input/mice.
   Please check your config if the mouse is still not
   operational, as by default Xorg tries to autodetect
   the protocol.

   Your xorg.conf file is /root/xorg.conf.new

   To test the server, run 'X -config /root/xorg.conf.new'

   (EE) Server terminated with error (2). Closing log file.
   ```
   
   Изменим сгенерированный файл до такого состояния:
   ```console

   ```

2. Убедитесь в правильности настройки, сделанной в предыдущем пункте

   Запускаем x сервер:

   ```console
   root@bc30138:/etc/X11$ startx
   ```

   ![startx](https://github.com/BC30138/Studying/blob/master/ADMIN/Labs/Screens/startx.png?raw=true)

### Упражнение 10.2. Настольные окружения
1. Сконфигурируйте систему так, чтобы по умолчанию для всех пользователей использовалось окружение **KDE**:

   ```console
   root@bc30138:~$ apt install plasma-desktop
   root@bc30138:~$ apt install lightdm
   service lightdm start
   ```

   Чтобы отключить **KDE**:
   ```console
   root@bc30138:~$ systemctl disable lightdm.service
   Synchronizing state of lightdm.service with SysV service script with /lib/systemd/systemd-sysv-install.
   Executing: /lib/systemd/systemd-sysv-install disable lightdm
   Removed /etc/systemd/system/display-manager.service.
   ```

2. Войдите под пользователем *vinnie*, удостоверьтесь в правильности конфигурации:

   ![vinnie](https://github.com/BC30138/Studying/blob/master/ADMIN/Labs/Screens/vinnie.png?raw=true)
   ![KDE](https://github.com/BC30138/Studying/blob/master/ADMIN/Labs/Screens/KDE.png?raw=true)
### Упражнение 10.3. Графический вход в систему
Было установлено и проверено в прошлом пункте.

1. Настройте автоматический запуск графической среды с использованием менеджера дисплеев **kdm**:

2. Перезагрузите операционную систему. Убедитесь, что доступен графический вход в систему.
   
3. Закончите графический сеанс работы в операционной системе.

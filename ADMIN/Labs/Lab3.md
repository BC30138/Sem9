# Лабораторная работа 3: Начальная загрузка системы

### Упражнение 3.1. Параметры загрузки ядра операционной системы

1. При помощи параметров загрузки ядра операционной системы загрузите операционную систему в однопользовательском (**single**) уровне исполнения:
   
    Для этого при запуске системы, когда откроется GRUB, требуется нажать клавишу 'e', таким образом откроется файл "**Advanced options for Debian GNU/Linux**", где нужно дописать в строку загрузки ядра параметр **single**:
   
    ```console
    linux /boot/<дистрибутив> root=UID= ... ro single
    ```  

2. При помощи параметров загрузки ядра операционной системы загрузите операционную систему в режиме работы драйвера виртуальных терминалов 80x25:
    
    Для этого в строку из первого пункта нужно добавить параметр **vga=769**:

    ```console
    linux /boot/<дистрибутив> root=UID= ... ro vga=769
    ``` 

### Упражнение 3.2. Неисправности в процессе загрузки 1. 

Пока что не могу, нету флешки.

1. Создайте загрузочную флешку LILO:

    Для этого установим пакет LILO.
    ```console
    root@bc30138:/$ apt-get install lilo 
    root@bc30138:~$ liloconfig
    1 images '/boot/vmlinuz*' found.
    New file created as: /etc/lilo.conf 
    Now you must execute '/sbin/lilo' to activate this new configuation!
    ```

    Монтируем флешку. 

    ```console
    root@bc30138:~$ mount /dev/sdf1 /mnt/usb
    ```

    Скоприуем файл ядра и драйверов на флешку с таким же путем (в нашем случае возьмем их из папки **/boot/**)

    ```console
    root@bc30138:~$ mkdir /mnt/usb/boot
    root@bc30138:~$ cd /boot
    root@bc30138:/boot$ cp initrd.img-4.9.0-7-amd64 vmlinuz-4.9.0-7-amd64 /mnt/usb/boot
    ```

    Отредактируем файл **/etc/lilo.conf**. Требуется изменить следующие параметры: 
    ```console
    root@bc30138:~$ vim /etc/lilo.conf
    boot = /dev/sdf # устройство
    root = /dev/sdf1 # корень
    map = /mnt/usb/boot/map # поменять именно на такого рода
    image = /mnt/usb/boot/vmlinuz-4.9.0-7-amd64 # расположение ядра
    initrd = /mnt/usb/boot/initrd.img-4.9.0-7-amd64 # расположение драйверов
    ```

    Запускаем LILO:
    ```console
    root@bc30138:~$ lilo
    Warning: /dev/sdf is not on the first disk
    Added Linux  +  *
    One warning was issued.
    ```

    Размонтируем флешку: 

    ```console
    root@bc30138:~$ umount /dev/sdf1
    ```

2. Перезагрузите систему. Загрузитесь с загрузочной флешки:

   ![Boot](https://github.com/BC30138/Studying/blob/master/ADMIN/Labs/Screens/boot.png?raw=true)
4. Создайте загрузочную дискету GRUB:
   ```console
   ```
5. Перезагрузите систему. Загрузитесь с загрузочной дискеты:
   ```console
   ```

   /Applications/VirtualBox.app/Contents/MacOS/VBoxGuestAdditions.iso
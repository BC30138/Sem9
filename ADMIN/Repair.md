### BAD SYSTEM #0

1. Запускается неправильное устройство.
    
   Для того, чтобы запустить систему нужно при запуске в грабе нажать клавишу 'e' и в файле, где команды: 

   ```console
   ro quiet
   ``` 

   Изменить 
   ```console
   root=/dev/sdb1
   ```

   На правильное устройство (чаще всего /dev/sda1)

   Для входа без пароля дописать 
   ```console
   init=/bin/bash
   ```

   Для того, чтобы конфиг граба требуется перемонтировать фс, чтобы она была rw:

   ```console
   mount -o remount,rw /
   ```

   в файле boot/grub/grub.cfg меняем все упоминания root=/dev/sdb1 на root=/dev/sdа1, сохраняем.

2. Для того, чтобы оценить работу многопользовательского режима - установим пустой пароль для пользователя root

    Для входа без пароля дописать 
   ```console
   init=/bin/bash
   ```

   ```console
   mount -o remount,rw /
   ```

   Изменим файл /etc/shadow: удалим в строке пароля root все.

3.  SYSTEM HALTED или reboot после входа

   Найти странные команды типа halt, reboot в файлах: 
   ```console
   /root/.bashrc
   /etc/bash.bashrc
   /etc/crontab
   /etc/profile
   ```
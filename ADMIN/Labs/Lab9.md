# Лабораторная работа 9. Служба периодического выполнения заданий. Служба журнализации событий. Служба печати
### Упражнение 9.1. Подсистема периодического выполнения заданий
1. Настройте подсистему периодического выполнения заданий так, чтобы:
    - ежедневно в 2 часа утра выполнялась резервная копия баз данных пользовательских учетных записей, с помещением сжатого архива c названием **users-<дата создания резервной копии>.tar.gz** в поддиректорию **backup** домашней директории суперпользователя:

    - Ежедневно в 3 часа утра выполнялся поиск и удаление старых (возрастом более недели) архивов баз данных пользовательских учетных записей, в поддиректории **backup**, домашней директории суперпользователя:
  
    Для этого требуется изменить файл, возвращаемый следующей командой:
    ```console
    root@bc30138:~$ crontab -e
    ```
    
    В файл допишем строки:
    ```console
      0 2   *   *   *    tar -zcf /var/backups/users/user-$(date +%y-%m-%d).tgz /home
      0 3   *   *   *    find /var/backups/users -type f -mtime +7 -print0 | xargs -0 rm -f  
    ```

2. Путем перевода текущего времени в системе, проверьте корректность выполнения настроенных заданий:
    Потребуется пакет **ntp**.

    С помощью следующей команды отключаем синхронизацию времени:
    ```console
    root@bc30138:~$ timedatectl set-ntp 0
    ```

    Переводим время на нужное: 
    ```console
    root@bc30138:~$ timedatectl set-time "01:59:59"
    ```

    Проверим:
    ```console
    root@bc30138:~$ ls /var/backups/users/
    user-18-12-23.tgz
    ```

    Теперь проверим удаление старых архивов:
    ```console
    root@bc30138:~$ date +%y-%m-%d -s "2019-01-01"
    19-01-01
    root@bc30138:~$ date -s "02:59:59"
    Tue Jan  1 02:59:59 EST 2019
    root@bc30138:~$ ls /var/backups/users/
    root@bc30138:~$ 
    ```

### Упражнение 9.2. Подсистема журнализации событий. Системные журналы
1. Настройте подсистему журнализации событий так,чтобы:

    a. Информация о событиях высокой важности (**warning,error,emerg**) всех подсистем посылалась суперпользователю немедленно;

    b. информация о событиях процесса загрузки (**facility=local7**) посылалась на терминал *tty10*;
    
    c. информация о событиях всех подсистем кроме ядра, за исключением отладочной, посылалась на терминал *tty11*;

    d. информация о событиях ядра посылалась на терминал *tty12*.

    Добавим следующие строчки в **/etc/rsyslog.conf**:
   ```console
   *.emerg                         :omusrmsg:*
   *.warn                          :omusrmsg:root
   *.err                           :omusrmsg:root
   local7.*                        /dev/tty10
   *.!=debug;kern.none             /dev/tty11
   kern.*                          /dev/tty12
   ```

2. Переинициализируйте подсистему журнализации событий. Проследите за сообщениями на терминалах *tty10, tty11, tty12*:
   
   Переинициализацию осуществим так:
   ```console
   root@bc30138:~$ service rsyslog restart
   root@bc30138:~$ 
   ```

   Меняем *tty* командой:
   ```console
   root@bc30138:~$ chvt 10
   ```
   10 - номер нужного терминала.


   На *tty10* отображена следующая информация:

   <!-- ![tty10f](https://github.com/BC30138/Studying/blob/master/ADMIN/Labs/Screens/tty10f.png?raw=true)  -->

   *tty11* и *tty12* пустые.

3. Перезапустите операционную систему, проследите за сообщениями на терминалах *tty10, tty11, tty12*:

  *tty11* по-прежнему пуст, чего не скажешь о tty *tty10* и *tty12*:

   *tty10*
  <!-- ![tty10s](https://github.com/BC30138/Studying/blob/master/ADMIN/Labs/Screens/tty10s.png?raw=true) -->

   *tty12*
  <!-- ![tty12s](https://github.com/BC30138/Studying/blob/master/ADMIN/Labs/Screens/tty12s.png?raw=true) -->

4. Настройте сценарий запуска подсистемы журнализации событий так, чтобы демон **syslogd** разрешал возможность приема сообщений от узлов сети:

   Раскомментируем строки в файле **/etc/rsyslog.conf**:
   ```console
   # provides TCP syslog reception
   module(load="imtcp")
   input(type="imtcp" port="514")
   ```

5. Настройте подсистему журнализации событий так, что бы вся информация о событиях всех подсистем любой важности посылалась на соседний узел сети:

   Для простоты будем посылать себе же, для этого добавим в файл **/etc/rsyslog.conf**:
   ```console
   *.*                             root@127.0.0.1
   ```

6. Переинициализируйте подсистему журнализации событий. Проследите за сообщениями на терминалах *tty10, tty11, tty12*:

   ```console
   root@bc30138:~$ service rsyslog restart
   root@bc30138:~$  liblogging-stdlog:  [origin software="rsyslogd" swVersion="8.24.0" x-pid="2181" x-info="http://www.rsyslog.com"] exiting on signal 15.
    systemd[1]: Stopping System Logging Service...
    systemd[1]: Stopped System Logging Service.
    systemd[1]: Starting System Logging Service...
    liblogging-stdlog:  [origin software="rsyslogd" swVersion="8.24.0" x-pid="2221" x-info="http://www.rsyslog.com"] start
    systemd[1]: Started System Logging Service.
    liblogging-stdlog: action 'root' treated as ':omusrmsg:root' - please use ':omusrmsg:root' syntax instead, 'root' will not be supported in the future [v8.24.0 try http://www.rsyslog.com/e/2184 ]
    liblogging-stdlog: action 'root' treated as ':omusrmsg:root' - please use ':omusrmsg:root' syntax instead, 'root' will not be supported in the future [v8.24.0 try http://www.rsyslog.com/e/2184 ]
    liblogging-stdlog: error during parsing file /etc/rsyslog.conf, on or before line 98: warnings occured in file '/etc/rsyslog.conf' around line 98 [v8.24.0 try http://www.rsyslog.com/e/2207 ]
    liblogging-stdlog: error during parsing file /etc/rsyslog.conf, on or before line 98: warnings occured in file '/etc/rsyslog.conf' around line 98 [v8.24.0 try http://www.rsyslog.com/e/2207 ]
    liblogging-stdlog: error during parsing file /etc/rsyslog.conf, on or before line 98: warnings occured in file '/etc/rsyslog.conf' around line 98 [v8.24.0 try http://www.rsyslog.com/e/2207 ]
   ```

   на *tty12* все по старому. 
   
   *tty11* - думаю неправильно сконфигурировано, но вариант с "!=kern.!=debug          /dev/tty11" тоже не подходит.


### Упражнение 9.3. Средства печати UNIX
1. Установите систему печати **cups**:
   
2. Установите виртуальный драйвер для печати в PDF в систему печати **cups**:
   
3. Используя браузер в графической среде, зайдите по адресу http://127.0.0.1:631 и добавьте принтер с именем *LocalPrinter* использующий драйвер PDF.
   
4. При помощи команд **lpr,lpq,lprm (lp,lpstat,cancel)**:
   
    a. просмотрите состояния принтера с именем *LocalPrinter*:
    
    b. распечатайте любой файл на принтере *LocalPrinter*, проследите за сообщениями:
    
    c. просмотрите состояния принтера *LocalPrinter*, проследите за сообщениями:
    
    d. удалите задание на печать из очереди принтера *LocalPrinter*, проследите за сообщениями:

    e. распечатайте любую известную страницу руководства **man** на принтере *LocalPrinter*, проследите за сообщениями:
# Лабораторная работа 8. Квотирование ресурсов файловых систем
### Упражнение 8.1. Активизация системы дискового квотирования
1. Настройте таблицу монтируемых файловых систем так, чтобы активизировать квотирование дискового пространства на корневой файловой системе, для пользовательских и групповых учетных записей:
   
    Требуется изменить файл **/etc/fstab**:
    
   ```console
   UUID=973239f6-1805-4b4e-b539-429e33550ecc /               ext4    errors=remount-ro,usrquota,grpquota 0       1
   UUID=b2ffeb34-4cbb-485a-8219-1fd82a9d6953 /home           ext4    defaults,usrquoata,grpquota        0       2
   ```

2. Активизируйте механизм дисковых квот, путем перемонтирования файловой системы c новыми параметрами:

   ```console
   root@bc30138:~$ mount -a -o remount
   root@bc30138:~$ 
   ```
   
3. Создайте файлы с информацией о уже использованных дисковых ресурсах файловых систем пользователями:
   
   Для этого пункта требуется установить пакет **quota**.
   ```console
   root@bc30138:~$ quotacheck -f -ugcma
   root@bc30138:~$ repquota -a
   *** Report for user quotas on device /dev/sda1
   Block grace time: 7days; Inode grace time: 7days
                           Block limits                File limits
   User            used    soft    hard  grace    used  soft  hard  grace
   ----------------------------------------------------------------------
   root      -- 1929252       0       0          80515     0     0       
   man       --    1532       0       0             76     0     0       
   lp        --       0       0       0              1     0     0       
   systemd-timesync --       0       0       0              1     0     0       
   _apt      --       8       0       0              2     0     0       
   avahi-autoipd --       4       0       0              1     0     0       
   colord    --       8       0       0              2     0     0       
   leha      --      12       0       0              1     0     0       
   student   --      96       0       0              1     0     0       
   Debian-exim --      36       0       0             10     0     0       


   *** Report for user quotas on device /dev/sda5
   Block grace time: 7days; Inode grace time: 7days
                           Block limits                File limits
   User            used    soft    hard  grace    used  soft  hard  grace
   ----------------------------------------------------------------------
   root      --      24       0       0              3     0     0       
   leha      --      20       0       0              5     0     0       
   guest     --       4       0       0              1     0     0       
   vinnie    --      68       0       0              7     0     0       
   student   --      60       0       0             15     0     0       
   ```
   Потребовалось помимо других параметров также использовать параметр -f, из-за предупреждения.

### Упражнение 8.2. Настройка дисковых квот для пользователей и групп
1. Для пользователя *vinnie*:
    
   a. Настройте мягкую квоту по количеству занимаемых блоков так, чтобы ее значение было немного больше текущего занимаемого этим пользователем количества блоков на диске.

   ```console
   root@bc30138:~$ edquota -u vinnie
   ```
    Откроется редактор по-умолчанию, где можно настроить квоты:

   Изменим открытый файл например следующим образом:
   ```console
   Disk quotas for user vinnie (uid 1003):
     Filesystem                   blocks       soft       hard     inodes     soft     hard
     /dev/sda1                         0          0          0          0        0        0
     /dev/sda5                        68         67          0          7        0        0
   ```

   Оценим изменения:
   ```console
   root@bc30138:~$ repquota -a
   *** Report for user quotas on device /dev/sda1
   Block grace time: 7days; Inode grace time: 7days
                           Block limits                File limits
   User            used    soft    hard  grace    used  soft  hard  grace
   ----------------------------------------------------------------------
   root      -- 1929488       0       0          80515     0     0       
   man       --    1532       0       0             76     0     0       
   lp        --       0       0       0              1     0     0       
   systemd-timesync --       0       0       0              1     0     0       
   _apt      --       8       0       0              2     0     0       
   avahi-autoipd --       4       0       0              1     0     0       
   colord    --       8       0       0              2     0     0       
   leha      --      12       0       0              1     0     0       
   student   --      96       0       0              1     0     0       
   Debian-exim --      36       0       0             10     0     0       


   *** Report for user quotas on device /dev/sda5
   Block grace time: 7days; Inode grace time: 7days
                           Block limits                File limits
   User            used    soft    hard  grace    used  soft  hard  grace
   ----------------------------------------------------------------------
   root      --      24       0       0              3     0     0       
   leha      --      20       0       0              5     0     0       
   guest     --       4       0       0              1     0     0       
   vinnie    +-      68      67       0  6days       7     0     0       
   student   --      60       0       0             15     0     0       
   user      --      16       0       0              4     0     0       
   ```

   Таймер **grace** запустился.

   b. Настройте жесткую квоту по количеству занимаемых блоков так, чтобы ее значение было на **1Mb** больше установленной выше мягкой квоты.

   Узнать размер блока данной ФС можно следующим образом:
   ```console
   root@bc30138:~$ stat -c %o /dev/sda5
   4096
   ```

   Таким образом нам нужно установить жесткую квоту на ~244 блока больше:
   ```console
   Disk quotas for user vinnie (uid 1003):
     Filesystem                   blocks       soft       hard     inodes     soft     hard
     /dev/sda1                         0          0          0          0        0        0
     /dev/sda5                        68         67        311          7        0        0
   ```

2. Для группы *vgroup*:
    
    a. Настройте мягкую квоту по количеству файлов так, чтобы ее значение было немного больше текущего занимаемого этой группой количества файлов на диске.

   !!!Стоит обратить внимание, что будут учитываться файлы только тех пользователей, у которых *vgroup* является **primary group**!!!

    b. Настройте жесткую квоту по количеству файлов так, чтобы ее значение было на **10** файлов больше установленной выше мягкой квоты.
    

   ```console                                     
   Disk quotas for group vgroup (gid 1004):
     Filesystem                   blocks       soft       hard     inodes     soft     hard
     /dev/sda1                         0          0          0          0        0        0
     /dev/sda5                        68          0          0          7       10       20
   ``` 

3. Для всех пользователей и групп, настройте период форы (**grace period**) по объему файлов в **1** минуту, а по количеству файлов в **2** минуты:
   
   ```console
   root@bc30138:~$ edquota -t
   ```

   В файле меняем:
   ```console
   Grace period before enforcing soft limits for users:
   Time units may be: days, hours, minutes, or seconds
     Filesystem             Block grace period     Inode grace period
     /dev/sda1                     7days                  7days
     /dev/sda5                  1minutes               2minutes
   ```

   **repquota**:
   ```console
   *** Report for user quotas on device /dev/sda5
   Block grace time: 00:01; Inode grace time: 00:02
                           Block limits                File limits
   User            used    soft    hard  grace    used  soft  hard  grace
   ----------------------------------------------------------------------
   root      --      24       0       0              3     0     0       
   leha      --      20       0       0              5     0     0       
   guest     --       4       0       0              1     0     0       
   vinnie    +-      68      67     311  00:01       7     0     0       
   student   --      60       0       0             15     0     0       
   user      --      16       0       0              4     0     0       
   ```
4. Войдите под учетной записью *vinnie* и убедитесь в действии жестких и мягких ограничений на занимаемое дисковое пространство и количество файлов путем создания в домашней директории различных файлов. Проследите за реакцией системы:

   ```console
   vinnie@bc30138:~$ dd if=/dev/urandom of=test.dat count=245 bs=4096
   sda5: write failed, user block limit reached.
   dd: error writing 'test.dat': Disk quota exceeded
   8+0 records in
   7+0 records out
   28672 bytes (29 kB, 28 KiB) copied, 0.00242244 s, 11.8 MB/s
   ```

    ```console
   *** Report for user quotas on device /dev/sda5
   Block grace time: 00:01; Inode grace time: 00:02
                           Block limits                File limits
   User            used    soft    hard  grace    used  soft  hard  grace
   ----------------------------------------------------------------------
   root      --      24       0       0              3     0     0       
   leha      --      20       0       0              5     0     0       
   guest     --       4       0       0              1     0     0       
   vinnie    +-      68      67     311   none       7     0     0       
   student   --      60       0       0             15     0     0       
   user      --      16       0       0              4     0     0   
   ```
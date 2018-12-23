# Лабораторная работа 7. Дерево каталогов и файловые системы
### Упражнение 7.1. Монтирование файловых систем
1. Осуществите создание файловой системы **ext2** на флеш накопителе, с проверкой
поврежденных блоков:

   Пусть флешка выглядит следующими образом:
   ```console
   sdn                   8:208  1  7.2G  0 disk  
   └─sdn1                8:209  1  7.2G  0 part 
   ```

   Создаем файловую систему используя команду **mkfs**.
   ```console
   root@bc30138:~$ mkfs -c -v -t ext2 /dev/sdn1
   mke2fs 1.43.4 (31-Jan-2017)
   /dev/sdn1 contains a vfat file system labelled 'USB DISK'
   Proceed anyway? (y,N) y
   fs_types for mke2fs.conf resolution: 'ext2'
   Filesystem label=
   OS type: Linux
   Block size=4096 (log=2)
   Fragment size=4096 (log=2)
   Stride=0 blocks, Stripe width=0 blocks
   473280 inodes, 1891344 blocks
   94567 blocks (5.00%) reserved for the super user
   First data block=0
   Maximum filesystem blocks=1937768448
   58 block groups
   32768 blocks per group, 32768 fragments per group
   8160 inodes per group
   Filesystem UUID: 52952336-9931-4cfa-8f65-8948885ff129
   Superblock backups stored on blocks: 
   	32768, 98304, 163840, 229376, 294912, 819200, 884736, 1605632

   Running command: badblocks -b 4096 -X -s /dev/sdn1 1891343
   Checking for bad blocks (read-only test): done                                                 
   Allocating group tables: done                            
   Writing inode tables: done                            
   Writing superblocks and filesystem accounting information: done 
   ```
   Аргумент **-с** для проверки поврежденных блоков, а аргумент **-v** для вывода информации.

2. Осуществите монтирование файловой системы носителя на флеш накопителе, убедитесь в корректности файловой системы:

   ```console
   root@bc30138:~$ mkdir /mnt/USB
   root@bc30138:~$ mount /dev/sdn1 /mnt/USB
   ```
   **lsblk**:
   ```console
   sdn                   8:208  1  7.2G  0 disk  
   └─sdn1                8:209  1  7.2G  0 part  /mnt/USB
   ```

3. Размонтируйте файловую систему флеш накопителя:
   
   ```console
   root@bc30138:~$ umount /dev/sdn1
   ```

4. Осуществите создание файловой системы msdos на флеш накопителе, с проверкой поврежденных блоков, задайте собственный текст предупреждения об отсутствии операционной системы на носителе, который будет отображаться при попытке загрузки с данного носителя:
   
   ```console
   root@bc30138:~$ mkfs.msdos -m Temp/message.dat -v -c /dev/sdn1
   mkfs.fat 4.1 (2017-01-24)
   Auto-selecting FAT32 for large filesystem
   /dev/sdn1 has 239 heads and 62 sectors per track,
   hidden sectors 0x1f80;
   logical sector size is 512,
   using 0xf8 media descriptor, with 15130752 sectors;
   drive number 0x80;
   filesystem has 2 32-bit FATs and 8 sectors per cluster.
   FAT size is 14752 sectors, and provides 1887652 clusters.
   There are 32 reserved sectors.
   Volume ID is 24cb369f, no volume label.
   Searching for bad blocks ...
   ```
   message.dat содержит сообщение.

5. Перегрузите операционную систему, попробуйте загрузиться с флеш накопителя. Проследите за сообщениями:

   ![USB](https://github.com/BC30138/Studying/blob/master/ADMIN/Labs/Screens/emptyUSB.jpg?raw=true)
   
6. Осуществите создание файловой системы **ext3** на чередующемся наборе томов **/dev/md0**, созданном в лаб. 6:
   
   ```console
   root@bc30138:~$ mkfs -t ext3 /dev/md127
   mke2fs 1.43.4 (31-Jan-2017)
   Creating filesystem with 268032 4k blocks and 67104 inodes
   Filesystem UUID: 613c2884-aa84-4bf0-9021-e00f518a12a1
   Superblock backups stored on blocks: 
   	32768, 98304, 163840, 229376

   Allocating group tables: done                            
   Writing inode tables: done                            
   Creating journal (8192 blocks): done
   Writing superblocks and filesystem accounting information: done
   ```
   
   

7. Осуществите монтирование файловой системы чередующегося набора томов **/dev/md0**, убедитесь в корректности файловой системы:
   
   Монтируем:
   ```console
   root@bc30138:~$ mkdir /mnt/raiddisk
   root@bc30138:~$ mount /dev/md127 /mnt/raiddisk
   ```
   Проверяем
   ```console
   root@bc30138:~$ echo "I am not NEO">>/mnt/raiddisk/Test.dat
   root@bc30138:~$ cat /mnt/raiddisk/Test.dat 
   I am not NEO
   ```

8. Осуществите создание файловой системы **reiserfs** на логическом томе **/dev/vg/lv0**, созданном в лаб. 6:
   
   Для этого требуется пакет **libguestfs-reiserfs**.

   После чего создаем **reiserfs**: 
   ```console
   root@bc30138:~$ mkreiserfs -f /dev/mapper/vg-lvol0
   mkreiserfs 3.6.25

   Guessing about desired format.. Kernel 4.9.0-7-amd64 is running.
   Format 3.6 with standard journal
   Count of blocks on the device: 38912
   Number of blocks consumed by mkreiserfs formatting process: 8213
   Blocksize: 4096
   Hash function used to sort names: "r5"
   Journal Size 8193 blocks (first block 18)
   Journal Max transaction length 1024
   inode generation number: 0
   UUID: fbe2bf66-3974-4921-9f90-6201804b54ab
   Initializing journal - 0%....20%....40%....60%....80%....100%
   Syncing..ok
   ReiserFS is successfully created on /dev/mapper/vg-lvol0.
   ```


9.  Осуществите монтирование файловой системы логического тома **/dev/vg/lv0**, убедитесь в корректности файловой системы:

    ```console
    root@bc30138:~$ mkdir /mnt/lvol
    root@bc30138:~$ mount /dev/mapper/vg-lvol0 /mnt/lvol/
    root@bc30138:~$ echo "smth">>/mnt/lvol/test.dat
    root@bc30138:~$ cat /mnt/lvol/test.dat 
    smth 
    ```
    
### Упражнение 7.2. Монтирование файловых систем
1. Cконфигурируйте таблицу монтируемых файловых систем (**fstab**) так, что бы все разделы с файловыми системами **FAT** монтировались бы автоматически при старте операционной системы со следующими параметрами:
   
    a) владелец файлов: псевдопользователь **bin**

    b) группа-владелец файлов: псевдопгруппа **bin**

    c) права доступа: **rwxrw-r--**

    d) имена файлов транслировались из кодовой страницы **866** в кодировку **utf8**

    Для этого добавим в файл **/etc/fstab** строку:
    ```console
    UUID=9DE7-BEE1  /media/dos      vfat    codepage=866,utf8,gid=bin,umask=013     0       0
    ```
    UUID - идентификатор раздела с фс **FAT**, далее указано место, куда монтировать, тип фс и требуемые параметры.

2. Осуществите монтирование всех разделов файлов, имеющих тип **FAT** (без перезагрузки):
   
   ```console
   root@bc30138:/home/student$ mount /dev/sdl1 /mnt/test/
   root@bc30138:/home/student$ lsblk
   ```
   **lsblk**:
   ```console
   sdl                   8:176  0    1G  0 disk  
   ├─sdl1                8:177  0  100M  0 part  /mnt/test
   └─sdl2                8:178  0  100M  0 part  
   ```

3. Перезагрузите операционную систему. Проследите за наличием смонтированных файловых систем, имеющих тип **FAT**: 
    
    **lsblk** после перезагрузки:
   ```console
   sdm                   8:192  0    1G  0 disk  
   ├─sdm1                8:193  0  100M  0 part  /media/dos
   └─sdm2                8:194  0  100M  0 part  
   ```
   
### Упражнение 7.3. Проверка целостности внешних файловых систем.
1. Осуществите проверку целостности всех файловых систем, созданных в упр. 7.1:
   
   ```console
   root@bc30138:~$ fsck /dev/mapper/vg-lvol0
   fsck from util-linux 2.29.2
   reiserfsck 3.6.25

   Will read-only check consistency of the filesystem on /dev/mapper/vg-lvol0
   Will put log info to 'stdout'

   Do you want to run this program?[N/Yes] (note need to type Yes if you do):Yes
   ###########
   reiserfsck --check started at Sun Dec 23 13:31:33 2018
   ###########
   Replaying journal: Done.
   Reiserfs journal '/dev/mapper/vg-lvol0' in blocks [18..8211]: 0 transactions replayed
   Checking internal tree.. finished
   Comparing bitmaps..finished
   Checking Semantic tree:
   finished
   No corruptions found
   There are on the filesystem:
   	Leaves 1
   	Internal nodes 0
   	Directories 2
   	Other files 1
   	Data block pointers 0 (0 of them are zero)
   	Safe links 0
   ###########
   reiserfsck finished at Sun Dec 23 13:31:33 2018
   ###########
   ```

2. Осуществите проверку целостности корневой файловой системы, путем предварительного перемонтирования файловой системы в режиме **readonly**:

   Сначала зайдем под суперпользователем:
   ```console
   root@bc30138:~$ init 1
   ```
   
   Затем:
   ```console
   root@bc30138:~$ mount -o remount, ro /
   root@bc30138:~$ fsck /
   fsck from util-linux 2.29.2
   e2fsck 1.43.4 (31-Jan-2017)
   /dev/sda1: clean, 80533/427392 files, 532501/1708800 blocks
   ```
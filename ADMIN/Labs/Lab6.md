# Лабораторная работа 6. Дисковые накопители: базовые тома, наборы томов и динамические тома
### Упражнение 6.1. Специальные файлы устройств дисковых накопителей. Управление базовыми томами
1. Определите количество дисков подсистемы IDE и SCSI, установленных в системе:
   
    Чтобы посмотреть доступные диски используем следующую команду: 

    ```console
    root@bc30138:~$ fdisk -l
   Disk /dev/sdb: 1 GiB, 1073741824 bytes, 2097152 sectors
   Units: sectors of 1 * 512 = 512 bytes
   Sector size (logical/physical): 512 bytes / 512 bytes
   I/O size (minimum/optimal): 512 bytes / 512 bytes


   Disk /dev/sdd: 1 GiB, 1098907648 bytes, 2146304 sectors
   Units: sectors of 1 * 512 = 512 bytes
   Sector size (logical/physical): 512 bytes / 512 bytes
   I/O size (minimum/optimal): 512 bytes / 512 bytes
   Disklabel type: dos
   Disk identifier: 0x5b356356


   Disk /dev/sdg: 1 GiB, 1098907648 bytes, 2146304 sectors
   Units: sectors of 1 * 512 = 512 bytes
   Sector size (logical/physical): 512 bytes / 512 bytes
   I/O size (minimum/optimal): 512 bytes / 512 bytes


   Disk /dev/sdi: 1 GiB, 1098907648 bytes, 2146304 sectors
   Units: sectors of 1 * 512 = 512 bytes
   Sector size (logical/physical): 512 bytes / 512 bytes
   I/O size (minimum/optimal): 512 bytes / 512 bytes


   Disk /dev/sde: 1 GiB, 1098907648 bytes, 2146304 sectors
   Units: sectors of 1 * 512 = 512 bytes
   Sector size (logical/physical): 512 bytes / 512 bytes
   I/O size (minimum/optimal): 512 bytes / 512 bytes


   Disk /dev/sdf: 1 GiB, 1098907648 bytes, 2146304 sectors
   Units: sectors of 1 * 512 = 512 bytes
   Sector size (logical/physical): 512 bytes / 512 bytes
   I/O size (minimum/optimal): 512 bytes / 512 bytes


   Disk /dev/sdh: 1 GiB, 1098907648 bytes, 2146304 sectors
   Units: sectors of 1 * 512 = 512 bytes
   Sector size (logical/physical): 512 bytes / 512 bytes
   I/O size (minimum/optimal): 512 bytes / 512 bytes


   Disk /dev/sdc: 1 GiB, 1098907648 bytes, 2146304 sectors
   Units: sectors of 1 * 512 = 512 bytes
   Sector size (logical/physical): 512 bytes / 512 bytes
   I/O size (minimum/optimal): 512 bytes / 512 bytes
   Disklabel type: dos
   Disk identifier: 0x117a4c7b


   Disk /dev/sda: 8 GiB, 8589934592 bytes, 16777216 sectors
   Units: sectors of 1 * 512 = 512 bytes
   Sector size (logical/physical): 512 bytes / 512 bytes
   I/O size (minimum/optimal): 512 bytes / 512 bytes
   Disklabel type: dos
   Disk identifier: 0xf094a24b
    ```

    Устройство название которого начинается на *sd* - это устройство подключенное по SCSI. 

    В списке мы видим 9 таких дисков: sd[a - i].

2. Определите количество разделов дисков подсистемы IDE и SCSI:
   
    Команда из первого пункта задания также выводит список разделов:
    ```console
    Device     Boot    Start      End  Sectors  Size Id Type
   /dev/sda1  *        2048 13672447 13670400  6.5G 83 Linux
   /dev/sda2       13674494 16775167  3100674  1.5G  5 Extended
   /dev/sda5       13674496 15626239  1951744  953M 83 Linux
   /dev/sda6       15628288 16775167  1146880  560M 82 Linux swap / Sol
    ```
    Итого 4 раздела: sda1, sda2, sda5, sda6.
   
3. Определите тип файловой системы на каждом из разделов дисков IDE и SCSI:

    Эту информацию можно узнать из файла **/etc/fstab**:
    ```console
    # /etc/fstab: static file system information.
    #
    # Use 'blkid' to print the universally unique identifier for a
    # device; this may be used with UUID= as a more robust way to name $
    # that works even if disks are added and removed. See fstab(5).
    #
    # <file system> <mount point>   <type>  <options>       <dump>  <pa$
    # / was on /dev/sda1 during installation
    UUID=973239f6-1805-4b4e-b539-429e33550ecc /               ext4    e$
    # /home was on /dev/sda5 during installation
    UUID=b2ffeb34-4cbb-485a-8219-1fd82a9d6953 /home           ext4    d$
    # swap was on /dev/sda6 during installation
    UUID=691bab4d-d3d5-4895-a48a-a92294007530 none            swap    s$
    /dev/sr0        /media/cdrom0   udf,iso9660 user,noauto     0      $
    ```
   
   - **ext4** (2 раздела) - журналируемая ФС, используемая в ОС с ядром Linux.
   - **swap** (1 раздел) - используется в реализации виртуальной памяти. 
   
### Упражнение 6.2. Создание программных наборов томов (RAID-массивов)
1. Создайте чередующийся набор томов **/dev/md0**, используя имеющиеся SCSI диски:
    
    Это можно сделать используя следующую команду: 
    ```console
    root@bc30138:~$ mdadm --create /dev/md0 --level=stripe --raid-devices=2 /dev/sdc /dev/sdd
    mdadm: /dev/sdc appears to be part of a raid array:
          level=raid0 devices=0 ctime=Wed Dec 31 19:00:00 1969
    mdadm: partition table exists on /dev/sdc but will be lost or
          meaningless after creating array
    mdadm: /dev/sdd appears to be part of a raid array:
          level=raid0 devices=0 ctime=Wed Dec 31 19:00:00 1969
    mdadm: partition table exists on /dev/sdd but will be lost or
          meaningless after creating array
    Continue creating array? y
    mdadm: Defaulting to version 1.2 metadata
    mdadm: array /dev/md0 started.
    ```
    Аргументы: аргумент md0 устанавливает четность, далее --level=stripe значит чередующийся (RAID 0), далее количество и расположение томов.

    Проверяем следующим образом:
    ```console
   root@bc30138:~$ lsblk
   NAME          MAJ:MIN RM  SIZE RO TYPE  MOUNTPOINT
   sda             8:0    0    8G  0 disk  
   ├─sda1          8:1    0  6.5G  0 part  /
   ├─sda2          8:2    0    1K  0 part  
   ├─sda5          8:5    0  953M  0 part  /home
   └─sda6          8:6    0  560M  0 part  [SWAP]
   sdb             8:16   0    1G  0 disk  
   ├─lab308-soft 253:0    0  100M  0 lvm   
   └─lab308-docs 253:1    0  100M  0 lvm   
   sdc             8:32   0    1G  0 disk  
   └─md0           9:0    0    2G  0 raid0 
   sdd             8:48   0    1G  0 disk  
   └─md0           9:0    0    2G  0 raid0 
   sde             8:64   0    1G  0 disk  
   sdf             8:80   0    1G  0 disk  
   sdg             8:96   0    1G  0 disk  
   sdh             8:112  0    1G  0 disk  
   sdi             8:128  0    1G  0 disk  
   sr0            11:0    1 55.3M  0 rom  
    ```

    Как мы видим на устройствах sdc и sdd появились нужные тома.
2. Создайте чередующийся набор томов с четностью **/dev/md1**, используя имеющиеся SCSI диски:

    ```console
    root@bc30138:~$ mdadm --create /dev/md1 --level=stripe --raid-devices=2 /dev/sdf /dev/sdg
    mdadm: Defaulting to version 1.2 metadata
    mdadm: array /dev/md1 started.
    ```

    ```console
    root@bc30138:~$ lsblk
    NAME          MAJ:MIN RM  SIZE RO TYPE  MOUNTPOINT
    sda             8:0    0    8G  0 disk  
    ├─sda1          8:1    0  6.5G  0 part  /
    ├─sda2          8:2    0    1K  0 part  
    ├─sda5          8:5    0  953M  0 part  /home
    └─sda6          8:6    0  560M  0 part  [SWAP]
    sdb             8:16   0    1G  0 disk  
    ├─lab308-soft 254:0    0  100M  0 lvm   
    └─lab308-docs 254:1    0  100M  0 lvm   
    sdc             8:32   0    1G  0 disk  
    └─md0           9:0    0    2G  0 raid0 
    sdd             8:48   0    1G  0 disk  
    └─md0           9:0    0    2G  0 raid0 
    sde             8:64   0    1G  0 disk  
    sdf             8:80   0    1G  0 disk  
    └─md1           9:1    0    2G  0 raid0 
    sdg             8:96   0    1G  0 disk  
    └─md1           9:1    0    2G  0 raid0 
    sdh             8:112  0    1G  0 disk  
    sdi             8:128  0    1G  0 disk  
    sr0            11:0    1 55.3M  0 rom  
    ```
    
    Здесь все аналогично пункту один, кроме четности.
    
   
3. Создайте зеркальный набор томов **/dev/md2**, используя имеющиеся SCSI диски:
   
   ```console
   mdadm: invalid raid level: miracle
   root@bc30138:~$ mdadm --create /dev/md2 --level=mirror --raid-devices=2 /dev/sdh /dev/sdi
   mdadm: Note: this array has metadata at the start and
       may not be suitable as a boot device.  If you plan to
       store '/boot' on this device please ensure that
       your boot-loader understands md/v1.x metadata, or use
       --metadata=0.90
   Continue creating array? y
   mdadm: Defaulting to version 1.2 metadata
   mdadm: array /dev/md2 started.
   ```
   
   ```console
   root@bc30138:~$ lsblk
   NAME          MAJ:MIN RM  SIZE RO TYPE  MOUNTPOINT
   sda             8:0    0    8G  0 disk  
   ├─sda1          8:1    0  6.5G  0 part  /
   ├─sda2          8:2    0    1K  0 part  
   ├─sda5          8:5    0  953M  0 part  /home
   └─sda6          8:6    0  560M  0 part  [SWAP]
   sdb             8:16   0    1G  0 disk  
   ├─lab308-soft 254:0    0  100M  0 lvm   
   └─lab308-docs 254:1    0  100M  0 lvm   
   sdc             8:32   0    1G  0 disk  
   └─md0           9:0    0    2G  0 raid0 
   sdd             8:48   0    1G  0 disk  
   └─md0           9:0    0    2G  0 raid0 
   sde             8:64   0    1G  0 disk  
   sdf             8:80   0    1G  0 disk  
   └─md1           9:1    0    2G  0 raid0 
   sdg             8:96   0    1G  0 disk  
   └─md1           9:1    0    2G  0 raid0 
   sdh             8:112  0    1G  0 disk  
   └─md2           9:2    0    1G  0 raid1 
   sdi             8:128  0    1G  0 disk  
   └─md2           9:2    0    1G  0 raid1 
   sr0            11:0    1 55.3M  0 rom   
   ```

   Здесь стоит обратить внимание на то, что это уже RAID 1 тома.

4. Разделите полученные **/dev/md1** и **/dev/md2** на 2 раздела каждый:
   
### Упражнение 6.3. Создание динамических томов
1. Создайте группу томов с названием *vg* и два линейных динамических тома *lv0*, *lv1* на ее основе, используя имеющиеся IDE и SCSI диски:
   
2. Создайте два динамических тома *mirror* (зеркало) и *stripe* (чередующийся набор томов с размером блока 8k) на основе группы *vg*, используя имеющиеся IDE и SCSI диски:
   
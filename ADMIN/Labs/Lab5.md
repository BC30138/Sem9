# Лабораторная работа 5: Ядро и драйвера устройств 
### Упражнение 5.1. Драйвера устройств
1. Проанализируйте и перечислите загруженные и активизированные драйвера устройств (как статически скомпонованных в ядро, так и динамически загружаемых):
   
   Для просмотра динамически загруженных драйверов используем следующую команду:
   ```console
   root@bc30138:~$ lsmod
   Module                  Size  Used by
   vboxsf                 45056  0
   intel_powerclamp       16384  0
   crct10dif_pclmul       16384  0
   crc32_pclmul           16384  0
   ghash_clmulni_intel    16384  0
   intel_rapl_perf        16384  0
   snd_intel8x0           40960  0
   snd_ac97_codec        126976  1 snd_intel8x0
   pcspkr                 16384  0
   joydev                 20480  0
   ac97_bus               16384  1 snd_ac97_codec
   sg                     32768  0
   evdev                  24576  3
   serio_raw              16384  0
   vboxguest             286720  2 vboxsf
   snd_pcm               110592  2 snd_ac97_codec,snd_intel8x0
   snd_timer              32768  1 snd_pcm
   snd                    86016  4 snd_ac97_codec,snd_timer,snd_intel8x0,snd_pcm
   soundcore              16384  1 snd
   ttm                    98304  0
   drm_kms_helper        155648  0
   drm                   360448  2 ttm,drm_kms_helper
   battery                20480  0
   ac                     16384  0
   video                  40960  0
   button                 16384  0
   parport_pc             28672  0
   ppdev                  20480  0
   lp                     20480  0
   parport                49152  3 lp,parport_pc,ppdev
   ip_tables              24576  0
   x_tables               36864  1 ip_tables
   autofs4                40960  2
   ext4                  585728  2
   crc16                  16384  1 ext4
   jbd2                  106496  1 ext4
   crc32c_generic         16384  0
   fscrypto               28672  1 ext4
   ecb                    16384  0
   mbcache                16384  3 ext4
   dm_mod                118784  4
   sr_mod                 24576  0
   cdrom                  61440  1 sr_mod
   sd_mod                 49152  5
   ata_generic            16384  0
   hid_generic            16384  0
   usbhid                 53248  0
   hid                   122880  2 hid_generic,usbhid
   crc32c_intel           24576  4
   aesni_intel           167936  1
   ohci_pci               16384  0
   ehci_pci               16384  0
   ohci_hcd               53248  1 ohci_pci
   ehci_hcd               81920  1 ehci_pci
   ata_piix               36864  0
   ahci                   36864  5
   libahci                32768  1 ahci
   aes_x86_64             20480  1 aesni_intel
   glue_helper            16384  1 aesni_intel
   lrw                    16384  1 aesni_intel
   gf128mul               16384  1 lrw
   ablk_helper            16384  1 aesni_intel
   cryptd                 24576  3 ablk_helper,ghash_clmulni_intel,aesni_intel
   psmouse               135168  0
   i2c_piix4              24576  0
   usbcore               253952  5 usbhid,ehci_hcd,ohci_pci,ohci_hcd,ehci_pci
   usb_common             16384  1 usbcore
   e1000                 143360  0
   libata                249856  4 ahci,ata_piix,libahci,ata_generic
   scsi_mod              225280  4 sd_mod,libata,sr_mod,sg
   ```

   Список статических драйверов можно следующим образом:
   ```console
   root@bc30138:~$ cat /lib/modules/$(uname -r)/modules.builtin
   ```

2. Проанализируйте и перечислите конфигурацию динамически загружаемых драйверов устройств:
   
    Просмотреть информацию о динамически загруженном драйвер можно используя следующую команду
   ```console
   root@bc30138:~$ modinfo <имя_модуля>
   ```

### Упражнение 5.2. Переменные ядра
1. Проанализируйте значения переменных ядра операционной системы:
   
   Выводим переменные ядер используя следующую команду:
    ```console
    root@bc30138:/etc/modprobe.d$ sysctl -a
    abi.vsyscall32 = 1
    debug.exception-trace = 1
    debug.kprobes-optimization = 1
    dev.cdrom.autoclose = 1
    dev.cdrom.autoeject = 0
    dev.cdrom.check_media = 0
    dev.cdrom.debug = 0
    dev.cdrom.info = CD-ROM information, Id: cdrom.c 3.20 2003/12/17
    dev.cdrom.info = 
    dev.cdrom.info = drive name:		sr0
    dev.cdrom.info = drive speed:		32
    dev.cdrom.info = drive # of slots:	1

    ...

    vm.stat_interval = 1
    vm.swappiness = 60
    vm.user_reserve_kbytes = 30993
    vm.vfs_cache_pressure = 100
    vm.watermark_scale_factor = 10
    vm.zone_reclaim_mode = 0
    ```

1. Измените значение переменной ядра операционной системы, отвечающей за имя узла сети, проследите за изменениями:

    Переменная имени ядра называется **kernel.hostname** и если изменить ее значение соответственно изменится и название узла сети:
    ```console
    root@bc30138:~$ sysctl -w kernel.hostname=BC30138
    kernel.hostname = BC30138
    root@bc30138:~$ hostname
    BC30138
    ```
          
# Лекция 10. 
## 23.11.18

### Некоторым сайтам браузер доверяет, а некоторым нет - с чем это связано? (о SSL вроде бы)

например вспомните окно, что браузер не хочет заходить на сайт, потому что считает подозрительным - это вроде и есть SSL.

Когда еще и подписано например "Bank SPB" - это значит есть еще и EV (extended validation) (там еще подписан адрес например физический в самом сертификате).

Чтобы на ваш сайт не ругался браузер (например вы ему полностью доверяете) - можно создать свой корневой сертификат и импортировать его в браузер.

Сделать свой сертификат можно с помощью "Let's Encrypt".

### Как зашифровать свой трафик?

(Подготовка) установить apache2:SSL, если не установлен и это все происходит на основе сайтов созданных в лекции 9

HTTP - порт 80

HTTPS - порт 443

Включим SSL

```console
$ a2enmod ssl
```

После этого требуется перезагрузить apache2

```console
$ service apache2 restart
```

создадим сертификат для сайта lab307

```console
$ make-ssl-cert /usr/share/ssl-cert/ssleay.cnf lab307cert
```

Далее откроется интерактивный режим **host-name** задаем например *lab307*

а **Alternative Name** : *DNS:www.lab307.com*

Перенесите файл

```console
mv lab307cert /etc/ssl/private
```

отредактируем файл **/etc/apache/sites-available/lab307.conf** (лекция 9) добавим в конец

```console
#SSL PORT
<VirtualHost *:443>
    ServerAdmin (копируем выше из этого файла)
    ServerName lab307.com (копируем выше)
     
    ServerAdmin (копируем выше)
    DocumentRoot /var/www/lab307 (копируем выше)

#START SSL    
    SSLEngine on

    SSLCertificateFile /etc/ssl/private/lab307cert
#END SSL
    ErrorLog -||- (копируем выше)
    CustomLog -||- (копируем выше)
</VirtualHost *:443>
```

После этого требуется перезагрузить *apache2*

```console
$ service apache2 restart
```

Зайдем на сайты, но по адресу

```console
https://lab307.com
```

Теперь браузер будет ругаться на то, что "Ваше соединение не защищено"

чтобы запустить *defaults-ssl* сайт достаточно написать команду 

```console
$ a2ensite default-ssl
```

а чтобы зайти на него нужно зайти в браузере на (как на default-000, только с приставкой https://)

```console
https://<свой-ip-адрес>
```

### Создание загрузочной дискеты для grub и lilo. Здесь будет рассмотрен lilo.

(Подготовка) вставим флешку и подключим ее к виртуальной машине

Устанавливаем lilo: 

```console
$ apt-get install lilo
```

Скопируем файл куда-нибудь по-ближе и раззипуем в lilo.conf

```console
$ cp /etc/share/doc/lilo/examples/lilo.example.conf.gz (или lilo.conf) /
```

посмотрим какие файлы initrd и img есть файлы
```console
$ ls -l /boot/
```


далее 

```console
$ mount /dev/sdb1 /mnt
```
копируем эти файлы в /mnt/

вот что требуется оставить (и изменить кое - что) в этом файле:
```console 
lba32

boot = /dev/sdb
prompt 
timeout = (как был)
map = /map/mnt/boot/map

image = /boot/vmlinux-3.12-1-generic #пишем свой файл, который лежит в /mnt - приписать /mnt
        label = "Linux"
        root = /dev/sda1
        read-only
        initrd = /boot/initrd.img-3.12-1-generic # пишем свой файл, который лежит в /mnt - приписать /mnt
        label = "Linux"
```
Выполним команду

```console
$ lilo -C /lilo.conf
```

после этого если загрузим какой-нибудь компьютер с этой флешки, то будет запущен такой минимальный линукс, но если бы был установлен хоть какой линукс, то был бы загружен он.

# Лекция 11
## 30.11.18

### DNS

```console
$ apt-get install bind9
```
в  файлы **/etc/bind/named.conf** и **/etc/bind/named.conf.default_zones** не очень хорошо писать 

нужно дописать в файл **/etc/bind/named.conf.local**

```console
zone "" {
    type master;
    file "/var/lib/bind/db.lab309";
}
```

для простоты 
```console
$ cp /etc/bind/db.local /var/lib/bind/db.lab309
```

и изменим в этом файле 
```console
                        это как бы емэил адммна
@   IN  SOA lab309.com. admin.lab309.com. (
              2             ; Serial
         604800             ; Refresh
          86400             ; Retry
        2419200             ; Expire
         604800  )          ; Negative Cache TTL

@     IN  NS  lab309.com
@     IN  A       <Наш ip-адрес>
www   IN  A       <Наш ip-адрес>
@     IN  MX 10   mail1
@     IN  MX 20   mail2
mail1 IN A        <Наш ip-адрес *.*.*.*+1>
mail2 IN A        <Наш ip-адрес *.*.*.*+2>
ftp   IN  CNAME   @
```

чтобы проверить что мы натворили
```console
$ named-checkconf #если все хорошо то ничего не скажет
$ named-checkzone lab309.com /var/lib/bind/db.lab309 #скажет ок
$ service bind9 restart
$ service bind9 status
$ apt install dnsutils
$ nslookup
> server <наш ip-адрес сервера>
```

добавим в файл **/etc/bind/named.conf.options**

```console
allow-query { any; };
```

рестарт бинда (service который)

можно пользоваться командой
```console
$ dig @<наш ip-адрес> lab309.com MX
```

Зайдем в файл **/etc/resolv.com**
```console
nameserver <наш ip>
```

после этого с помощью команды *ping* можно все проверять

```console
$ cp /etc/bind/db.127 /var/lib/bind/db.192
```

нужно дописать в файл **/etc/bind/named.conf.local**

```console
zone "<ip-адрес наоборот 1.2.3.4 в 4.3.2 (без одного октета, который запишем потом)>.in-addr.arpa" {
    type master;
    file "/var/lib/bind/db.192";
}
```

а в файле **/var/lib/bind/db.192**
```console
@   IN  SOA lab309.com. admin.lab309.com. (
              2             ; Serial
         604800             ; Refresh
          86400             ; Retry
        2419200             ; Expire
         604800  )          ; Negative Cache TTL

@                    IN  NS  lab309.com
<последний октет>    IN  A   lab309.com
```

```console
$ named-checkzone <ip-адрес наоборот 1.2.3.4 в 4.3.2 (без одного октета, который запишем потом)>.in-addr.arpa /var/lib/bind/db.192
```

# Лекция 12
## 07.12.18
### SysLog (Система управления журналом) (Лабораторная #9.2)
    

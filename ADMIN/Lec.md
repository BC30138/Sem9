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

```console
$ man rsyslog.conf
```

Сам файл находится в var/log/syslog.conf (также есть rsyslog.conf - это немного более продвинутый сислог)

также в папке есть такие полезные журналы как kern.log, auth.log

#### 9.2.1.a

Зайдем в файл rsyslog.conf и добавим строку (либо отредактируем существующую)
```console
*.warn          :omusrsmg:root
```      

Эмулируем предупреждение от рута

```console
$ server syslog restart #сохраняем изменения
$ logger -p warn "PRIV CHE DEL" 
```
После этого другие пользователи в системе получат это предупреждение 

    
#### 9.2.1.b


Зайдем в файл rsyslog.conf и добавим строку 

```console
local7.*          /dev/tty10
```      

#### 9.2.1.c

Зайдем в файл rsyslog.conf и добавим строку 

```console
!=kern.!=debug          /dev/tty11
``` 

Эмулируем предупреждение от рута

```console
$ server syslog restart
$ logger -p debug "PRIV CHE DEL" 
$ logger -p warn "PRIV CHE DEL" 
```

#### 9.2.1.d

Зайдем в файл rsyslog.conf и добавим строку 

```console
kern.*          /dev/tty12
``` 

```console
$ server syslog restart
```

#### остальные пункты
Включить слушающий порт

Раскомментировать там где про TCP написано пару строк ниже 

и проверить
```console
$ server syslog restart
netstat -nap|grep syslog
```

Отправляем все на соседний хост (например себе лучше)
```console
*.*          @127.0.0.1
``` 

Отправляем сообщение и проверяем, что записано в var/log/syslog 

```console
$ server syslog restart 
$ logger -p warn "PRIV CHE DEL" 
```

там должно появиться множественное количество раз сообщение. (зациклится, но должно сработать)

### SAMBA (CIFS)

CIFS работает по TCP на порте 445.

```console
$ apt update
$ apt install samba
```

в самбу входит два демона, с помощью проверки этих процессов можно понять запущен ли самба 
```console
$ service smbd status
$ service NMB status
```

зайдем в конфиг самбы /etc/samba/smb.config
 p.s. browseable = no (скрытая директория)

зайдем в папку \\\айпи_линукса\пользователь_линукса (в юниксе слэши конечно другие) из виндоуса

нас не пустили, не смотря на то, что мы вводим правильный пароль.

потому что самба инородная и она берет аккаунты и пароли юникса, однако в самбе пароли работают
по-другому, поэтому надо установить пароль пользователя для самбы (чисто теоретически основной 
пароль и пароль самбы могут отличаться).

```console
$ smbpasswd -a user # аккаунт user точно должен быть
```

добавили пароль.

теперь если снова зайдем в папку \\\айпи_линукса\пользователь_линукса  из виндоуса, но используя 
пароль самбы. 

теперь нас пустят. 

добавим в конфиг самбы /etc/samba/smb.config

```console
[share]
    path = /srv/ftp # такую шару на предыдущих лабах (занятиях делали)
    browseable = yes
$ service smbd restart
```

зайдем в папку \\\айпи_линукса\  из проводника виндоуса

так как шара браузэйбл она будет отображена

# Лекция 13
## 14.12.18

Зайдем в файл /etc/samba/samba.conf и допишем строку
```console
guest account = ftp # либо не ftp, а другого пользователя, которого удобно использовать
```
и в блоке [share] надо дописать

```console
[share]
    path = ...
    browseable = yes
    guest ok = yes
```

```console
$ service smbd restart
```

теперь если зайдем через проводник как на прошлой лекции и попробуем зайти в папку share -- ввод пароля не потребуется

если вы до этого входили в аккаунт, то он постоянно будет заходить без пароля, поэтому проверить все как следует не получится. Чтобы сбросить вход проделайте следующее:

----
теперь если в виндоусе набрать команду, то список будет не пуст 
```console
net use 
```

чтобы отчистить пользователя
```console
net use \\айпи_линукса\user /delete /yes
```

либо можно удалить вообще всех
```console
net use * /delete /yes
```
-----

Дальше вроде бы с принтерами будем разбираться

```console
apt install cups
apt install cups-pdf
```

сконфигурируем cups. Хотелось бы сконфигурировать принтеры с помощью браузера. 

для этого зайдем в папку /etc/cups/cupsd.conf 

коментируем строчку 
```console
Listen localhost:631
```

и пишем вместо нее 
```console
Listen 0.0.0.0:631
```

и допишем в <Location /> и <Location /admin> 
```console
allow from all
```

а в браузере виндоуса после этого зайдем по \наш_айпи:631 

попробуем добавить пароль, для этого идем во вкладку администрирование и во вкладке принтеры "добавить принтеры" и выйдет предложение войти, зайдем как root. Выберем cups-pdf, название и описание как угодно (пусть например это myprinter), ставим галочку совместный доступ, продолжить. Выберем компанию "Generic" и выберем модель "Generic CUPS pdf ..". Нажмем на "обслуживание" и отправим тестовую страницу. 

Теперь в линуксе перенесем тестовую страницу в шейру
```console
$ cp /root/PDF/Test_page.pdf /srv/ftp
$ chmod +r /srv/ftp/Test_page.pdf
```

теперь выберем принтер в проводнике виндоуса в папке с шейром PDF далее, выбираем модель "MS Publisher Color Printer". 

------
ЧТО-ТО НЕ ПОЛУЧИЛОСЬ
попробуем в папке шейр создать текстовый файл - доступ запрещен. Для этого доишем в конфиге /etc/samba/samba.conf в блоке [share] надо дописать
```console
[share]
    path = ...
    browseable = yes
    guest ok = yes
    writeable = yes
```
теперь можно создавать файлы в папке шейр.

-----

зайдем под пользователем в проводнике (\айпи_линукса\user)
попробуем распечатать. Двойной клик по принтеру, далее принтер-свойства-пробная печать. Дальше опять перетащим пробную страницу на шару и посмотрим. (файл вроде _.pdf)

### SSH (Secury SHell)

Скачаем программу PuTTY. 
Скачиваем с официального сайта саму putty.exe (не установочник) и puttygen.exe.
Нажимаем генерэйт кейген. 

Запускаем путти. Вместо Host Name, пишем свой айпи. Войдем под пользователем. (не дает зайти под рутом, но это можно как то изменить, да и роль можно поменять с помощью su, либо sudo)

Зайдем в каталог .ssh/ (если его нету, то mdkir .ssh) 

Создадим файл
```console
$ vim authorized_keys 
```

копируем в него сгенерированный ключ ранее в puttygen (shift + v, либо правой кнопкой мыши).

Зайдем в путти конфигурэйшн. /connection/SSH/auth, там найдем этот файл
А в connection/data можно ввести сразу имя пользователя

полезно
```console
$ vim /ets/SSH/sshd_config # или что-то вроде
    password authentification  yes
    PermitRootLogin prohibit-password # либо yes.
```
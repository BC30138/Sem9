# Лабораторная работа 2: Делегирование полномочий

### Упражнение 2.1
1. При помощи команды **su** измените контекст текущего пользователя *student* на контекст пользователя *vinnie* и зафиксируйте его идентификаторы UID, GID и идентификаторы вторичных групп.
   
   Предполагаем, что пользователь *student* уже создан. Мы вошли в систему под логином *student*. Сменим пользователя на *vinnie*:

    ```console
    student@bc30138:~$ su vinnie
    Password: 
    vinnie@bc30138:/home/student$
    ```

    Зафиксируем идентификаторы *vinnie*:

    ```console
    vinnie@bc30138:/home/student$ id
    uid=1003(vinnie) gid=1003(vinnie) groups=1003(vinnie),1004(vgroup) 
    ```

2. При помощи команды **exit** вернитесь в контекст текущего пользователя *student*, убедитесь в этом, проверив его идентификаторы. 

    Использование команды **exit**:

    ```console
    vinnie@bc30138:/home/student$ exit
    exit
    student@bc30138:~$ 
    ```

    Проверка идентификаторов:

    ```console
    student@bc30138:~$ id
    uid=1002(student) gid=1002(student) groups=1002(student) 
    ```

    Заметим, что идентификатор вторичной группы отсутствует.

### Упражнение 2.2
1. Осуществите передачу **полных** полномочий администратора *root* пользователю *student*.
    
    Для того чтобы установить права администратора для пользователя *student* требуется внести изменения в файл **/etc/passwd**: 

    ```console
    root@bc30138:~$ vim /etc/passwd
    ```

    Требуется изменить UID и GID пользователя в следующей строке:

    ```console
    student:x:1002:1002:,,,:/home/student:/bin/bash
    ```

    на "0":

    ```console
    student:x:0:0:,,,:/home/student:/bin/bash
    ```

    Можно легко проверить, что этот пользователь теперь обладает правами root: при входе в систему, используя логин *student* контекст будет следующим: 

    ```console
    root@bc30138:~$
    ```

2.  Заблокируйте пользователю *root* интерактивный вход.

    Требуется изменить начальный интерпретатор для пользователя *root* в следующей строке файла **/etc/passwd**:

    ```console
    root:x:0:0:root:/root:/bin/bash
    ```

    На следующий: ДРУГОЙ СПОСОБ (sudo) 

    ```console
    root:x:0:0:root:/root:/bin/false
    ```

    P.S.: Однако далее я вернул значение /bin/bash для *root* используя пользователя *student*.  

### Упражнение 2.3

1. Создайте пользовательскую запись *netadmin*.
   
   Так как мы заблокировали вход для пользователя *root* в этом задании &mdash; новый пользователь создается с помощью пользователя *student*

    ```console
    root@bc30138:~$ adduser netadmin
    ```

2. Осуществите передачу полномочий администратора *root* пользователю *netadmin* для выполнения команд **/sbin/iptables**, **/sbin/ipconfig**, **/sbin/ip**, **/sbin/route**, **/bin/netstat** и редактирования файла **/etc/network/interfaces**.
    

    ТОЖЕ ЧЕРЕЗ sudo разрешить редактирование (edit)

    Передать пользователю права на файл можно с помощью команды **chown**:

    ```console
    root@bc30138:~$ chown netadmin /sbin/iptables
    root@bc30138:~$ chown netadmin /sbin/ifconfig
    root@bc30138:~$ chown netadmin /sbin/ip
    root@bc30138:~$ chown netadmin /sbin/route
    root@bc30138:~$ chown netadmin /bin/netstat
    root@bc30138:~$ chown netadmin /etc/network/interfaces
    ```

    <span style="color:red"> Немного не понял, так как вроде все эти команды итак доступны всем пользователям </span>
    ИМЕНННО ИСПОЛЬЗОВАНИЕ

3. Проверьте корректность делегирования полномочий, попытавшись выполнить неразрешенные пользователю *netadmin* команды от лица администратора. 

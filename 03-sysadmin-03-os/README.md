1. Вводим `strace 2>&1 /bin/bash -c 'cd /tmp' | grep '/tmp'` для вывода `stderr` команды `strace` по запросу `/tmp`:
        
        execve("/bin/bash", ["/bin/bash", "-c", "cd /tmp"], 0x7ffd9647fbd0 /* 24 vars */) = 0
        stat("/tmp", {st_mode=S_IFDIR|S_ISVTX|0777, st_size=4096, ...}) = 0
        chdir("/tmp")                           = 0
   Требуемый системный вызов - это `chdir("/tmp")`.
2. Ищем в выводе команды информацию о базе данных: `strace 2>&1 file /dev/tty | grep 'mgc'`
        
        stat("/home/vagrant/.magic.mgc", 0x7ffd5d4d34a0) = -1 ENOENT (No such file or directory)
        openat(AT_FDCWD, "/etc/magic.mgc", O_RDONLY) = -1 ENOENT (No such file or directory)
        openat(AT_FDCWD, "/usr/share/misc/magic.mgc", O_RDONLY) = 3
   Ответ: `/usr/share/misc/magic.mgc`.
3. Создаю файл `123` с помощью `vi`. Вписываю любое содержимое в файл и сохраняю его, не закрывая редактор. Переключаюсь на второй терминал и просматриваю какой PID имеет процесс `vi`: `ps -a`

        PID TTY          TIME CMD
        1670 pts/0    00:00:00 vi
        1752 pts/1    00:00:00 ps
   Выполняю `lsof -p 1670`:  
        `vi      1670 vagrant    3u   REG  253,0    12288 131093 /home/vagrant/.123.swp`  
   Удаляю файл `.123.swp` и он становится `deleted` в `lsof`.  
        `vi      1670 vagrant    3u   REG  253,0    12288 131093 /home/vagrant/.123.swp (deleted)`  
   Т.к. `vi` использует дескриптор `3` для записи в файл `123`, то используя `echo '' > /proc/1670/fd/3` можно обнулить файл:  
        `vi      1670 vagrant    3u   REG  253,0        1 131093 /home/vagrant/.123.swp (deleted)`.
4. Зомби-процессы завершаются и не потребляют ресурсы, но остаются висеть строкой в таблице процессов ядра. Зомби не занимают памяти (как процессы-сироты), но блокируют записи в таблице процессов, размер которой ограничен для каждого пользователя и системы в целом.
5. Выполняю `sudo opensnoop-bpfcc -d 1` для вывода вызовов за одну секунду:
        
        PID    COMM               FD ERR PATH
        785    vminfo              4   0 /var/run/utmp
        585    dbus-daemon        -1   2 /usr/local/share/dbus-1/system-services
        585    dbus-daemon        18   0 /usr/share/dbus-1/system-services
        585    dbus-daemon        -1   2 /lib/dbus-1/system-services
        585    dbus-daemon        18   0 /var/lib/snapd/dbus-1/system-services/
6. `strace 2>&1 uname -a | grep 'uname'`:

        execve("/usr/bin/uname", ["uname", "-a"], 0x7fff3a5b17d8 /* 24 vars */) = 0
        uname({sysname="Linux", nodename="vagrant", ...}) = 0
        uname({sysname="Linux", nodename="vagrant", ...}) = 0
        uname({sysname="Linux", nodename="vagrant", ...}) = 0
   Используется системный вызов `uname()`.
   
   Выполняю `man 2 uname`:  
   `Part  of  the  utsname information is also accessible via /proc/sys/kernel/{ostype, hostname, osre‐
       lease, version, domainname}.`
7. При выполнении последовательности команд, разделенных `;`, выполнение каждой последующей команды не зависит от результата выполнения предыдущей. А при использовании условного оператора `&&` выполнение каждой последующей команды будет зависеть от результата выполнения предыдущей, т.е. если предыдущая команда выполнилась без ошибок (вернет 0), то последующая команда начнет исполняться. В противном случае выполнение прервется.  
   `set -e` будет проигнорированно, если в последовательности команд встречается конструкция `&&`. Т.е. оболочка не прервет выполнение команд, если `&&` не находится в конце последовательности.
8. 

# Pam

`Настройка запрета для всех пользователей (кроме группы Admin) логина в выходные дни (Праздники не учитываются)`

Стенд поднят на Vagrant с box на ubuntu 22.04 (Vagrantfile прилагается)

Поднимаем виртуалку `vagrant up`

Заходим на нее по SSH `vagrant ssh`

Переходим в Root `sudo -i`

Создаём пользователя otusadm и otus: `sudo useradd otusadm && sudo useradd otus`

Создаём пользователям пароли: `echo "otusadm:Otus2025!" | sudo chpasswd && echo "otus:Otus2025!" | sudo chpasswd`

Создаём группу admin: `sudo groupadd -f admin`

Добавляем пользователей vagrant, root и otusadm в группу admin: `usermod otusadm -a -G admin && usermod root -a -G admin && usermod vagrant -a -G admin`

Мы просто добавили пользователя otusadm в группу admin. Это не делает пользователя otusadm администратором.

После создания пользователей, нужно проверить, что они могут подключаться по SSH к нашей ВМ. Для этого пытаемся подключиться с хостовой машины:

`ssh otus@192.168.0.234` вводим пароль

`ssh otusadm@192.168.0.234` вводим пароль

Если всё настроено правильно, на этом моменте мы сможем подключиться по SSH под пользователем otus и otusadm

Настроим правило, по которому все пользователи кроме тех, что указаны в группе admin не смогут подключаться в выходные дни:

Проверим, что пользователи root, vagrant и otusadm есть в группе admin: `cat /etc/group | grep admin`

Выберем метод PAM-аутентификации, так как у нас используется только ограничение по времени, то было бы логично использовать метод pam_time, однако, данный метод не работает с локальными группами пользователей, и, получается, что использование данного метода добавит нам большое количество однообразных строк с разными пользователями. В текущей ситуации лучше написать небольшой скрипт контроля и использовать модуль pam_exec

Создадим файл-скрипт /usr/local/bin/login.sh (скрипт прилагается)

`nano /usr/local/bin/login.sh`

![Image alt](https://github.com/NikPuskov/Pam/blob/main/pam1.jpg)

Добавим права на исполнение файла: `chmod +x /usr/local/bin/login.sh`

Укажем в файле /etc/pam.d/sshd модуль pam_exec и наш скрипт:

`nano /etc/pam.d/sshd`

![Image alt](https://github.com/NikPuskov/Pam/blob/main/pam2.jpg)

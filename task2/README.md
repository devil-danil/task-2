# Домашнее задание 2

### Тема лекции
> "Устройство и загрузка современного сервера"

### Среда выполнения
> macOS 15.5

## Задание
Дан образ машины, в которой перед её выключением было затёрто начало диска, и она теперь не загружается. Пароль пользователя неизвестен. Нужно восстановить работоспособность системы и суметь в неё залогиниться.

## Бонусная часть
Найти и получить секрет.

*Ссылка на архив с образом: https://disk.yandex.ru/d/xwbbmLCWbogOsg*

## Решение основной части
1. Сначала загружаю архив по ссылке

2. Чтобы распаковать файл c zst-расширением устанавливаю zstd

`brew install zstd`

3. Распаковываю архив

`unzstd task2.tar.zst`

4. Теперь распаковываю tar

`tar -xvf task2.tar`

5. Читаю README и пытаюсь запустить скрипт run.sh

`VNCPASSWORD=12345 ./run.sh`

![](https://getfile.dokpub.com/yandex/get/https://disk.yandex.ru/i/GYNCND47czg08g)

Получаю ошибку из-за отсутствия образа systemrescue-12.01-amd64.iso

6. Загрузим недостающий образ

`curl -L -o systemrescue-12.01-amd64.iso \
  https://download.system-rescue.org/releases/12.01/systemrescue-12.01-amd64.iso`

7. Получу хеш_контрольной_суммы

`curl -L -o systemrescue-12.01-amd64.iso.sha256 \
  https://www.system-rescue.org/releases/12.01/systemrescue-12.01-amd64.iso.sha256`

8. Проверяю целостность образа

`sha256sum --check systemrescue-12.01-amd64.iso.sha256`

![](https://getfile.dokpub.com/yandex/get/https://disk.yandex.ru/i/W2--lXCBsW3JlQ)

> Всё ок!

9. Проверю в другом терминале все TCP-соединения с процессами и отфильтрую по имени процесса qemu

`sudo lsof -iTCP -sTCP:LISTEN -n -P | grep qemu`

-	-iTCP — сетевые TCP-соединения  
-	-sTCP:LISTEN — только слушающие (прослушивающие) порты  
-	-n — не резолвит имена хостов (ускоряет)  
-	-P — показывает порты цифрами (без имен)  

![screenshot_3]()

10. Устанавливаю VNC-клиент (TigerVNC) для macOS

`brew install tigervnc`

Запускаю скрипт run.sh
VNCPASSWORD=12345 ./run.sh

11. Запускаю TigerVNC и ввожу данные для подключения к серверу

![screenshot_4]()
![screenshot_5]()

12. Вижу ошибку при попытке загрузки с диска

![screenshot_6]()
![screenshot_7]()

13. Захожу в Boot Manager Menu

![screenshot_8]()

14. Перехожу к настройкам SecureBoot, отключаю его и перезагружаю систему

![screenshot_9]()

15. В меню GRUB выбираю загрузку с параметрами по-умолчанию

![screenshot_10]()

16. Видим, что загружена SystemRescue, вошли под root

![screenshot_11]()

17. Далее проверим настройки для подключения по ssh

> Посмотрим, какие юнит-файлы доступны в systemd (поищем для служб ssh и firewall)

`systemctl list-unit-files | grep -E 'ssh|fire'`

![screenshot_12]()

18. Проверяю состояние ssh-сервера

`systemctl status sshd`

![screenshot_13]()

19. Проверяю правила iptables и добавляю правило для SSH

`iptables -L`

![screenshot_14]()

`iptables -I INPUT -p tcp --dport 22 -j ACCEPT`

`iptables -L INPUT`

![screenshot_15]()

С помощью **passwd** меняю пароль для root пользователя

![screenshot_16]()

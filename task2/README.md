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

Получу хеш_контрольной_суммы

`curl -L -o systemrescue-12.01-amd64.iso.sha256 \
  https://www.system-rescue.org/releases/12.01/systemrescue-12.01-amd64.iso.sha256`

Проверяю целостность образа

`sha256sum --check systemrescue-12.01-amd64.iso.sha256`

![](https://getfile.dokpub.com/yandex/get/https://disk.yandex.ru/i/W2--lXCBsW3JlQ)

Всё ок!

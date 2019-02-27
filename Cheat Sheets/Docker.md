# Подсказки для Docker

- `docker images`: показать список доступных на локальной машине образов
- `docker rmi <ID>`: удалить образ по ID, если у него нет потомков
- `docker rmi -f <ID>`: удалить образ по ID вместе с потомками
- `docker ps -a`: показать список запущенных контейнеров; _-a_: показать в т.ч. _exited_-контейнеры
- `docker rm <ID>`: удалить контейнер по ID
- `docker system prune`: очистить мусор (например, остановленные контейнеры)
- `docker pull <name>[:<tag>]`: скачать образ с именем _name_ и тегом _tag_. Если тег не указан, то будет скачан образ _name_:latest
- `docker build -t <name>[:<tag>] <path>`: создать образ с именем _name_:_tag_ из директории по адресу _path_ (внутри должен быть **Dockerfile**)

## docker run
`docker run <image> [<command>]`: запустить образ. Если указан аргумент _command_, то в качетстве корневого процесса будет запущена указанная программа

### Параметры:
- `--rm`: удалить контейнер после его остановки
- `-a <stream>`: присоединить поток контейнера к потоку хостовой машины. В качестве _stream_ может быть, например, **STDIN**, **STDOUT**, **STDERR**. Если задача выполняется в фоне (например, при запуске с помощью серввиса systemd), то соответствующий вывод попадает в журнал systemd
- `-t`: присоединить к вводу-выводу псевдо-TTY контейнера
- `-i`: присоединить ввод-вывод хостовой машины к вводу-выводу контейнера (интерактивный режим)
- `-p <hport>:<cport>`: отобразить порт хостовой машины _hport_ на порт контейнера _cport_
- `-v <hpath>:<cpath>`: примонтировать каталог или файл хостовой машины _hpath_ по пути внутри контейнера _cpath_
- `--name <name>`: задать имя запускаемому контейнеру. Имя имеет формат _name_:_tag_
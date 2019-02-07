#### Установка и настройка VMware ####

1. Чтобы узнать версию ядра `uname -r`

2. Устанавливаем заголовки ядра (внимательно с версиями). В моем случае обновляемся до последнего ядра и устанавливаем актуальные заголовки.
```bash
pacman -Syu
pacman -S linux-headers
```

3. Устанавливаем дополнительные инструменты (первые три у меня уже имелись в наличии)
```bash
pacman -S fuse2 gtkmm libcanberra pcsclite
yay -S ncurses5-compat-libs
```

4. Качаем bundle и запускаем установку
```bash
sudo sh <...>.bundle
```
Соглашаемся с 2 лицензионными соглашениями, вводим /etc/init.d, вводим директорию для Shared virtual machines (я не использую), вводим ключ активации, нажимаем ОК на диалоговом окне с ошибкой.

5. После обновления ядра и его заголовочных файлов требуется пересобирать пакеты VMware:
```bash
vmware-modconfig --console --install-all
```

6. Создаем сервисы Systemd
```bash
vim /etc/systemd/system/vmware.service
```
```
[Unit]
Description=VMware daemon
Requires=vmware-usbarbitrator.service
Before=vmware-usbarbitrator.service
After=network.target

[Service]
ExecStart=/etc/init.d/vmware start
ExecStop=/etc/init.d/vmware stop
PIDFile=/var/lock/subsys/vmware
RemainAfterExit=yes

[Install]
WantedBy=multi-user.target
```bash
vim /etc/systemd/system/vmware-usbarbitrator.service
```
```
[Unit]
Description=VMware USB Arbitrator
Requires=vmware.service
After=vmware.service

[Service]
ExecStart=/usr/bin/vmware-usbarbitrator
ExecStop=/usr/bin/vmware-usbarbitrator --kill
RemainAfterExit=yes

[Install]
WantedBy=multi-user.target
```

```bash
vim /etc/systemd/system/vmware-workstation-server.service
```
```
[Unit]
Description=VMware Workstation Server
Requires=vmware.service
After=vmware.service

[Service]
ExecStart=/etc/init.d/vmware-workstation-server start
ExecStop=/etc/init.d/vmware-workstation-server stop
PIDFile=/var/lock/subsys/vmware-workstation-server
RemainAfterExit=yes

[Install]
WantedBy=multi-user.target
```

Затем включите (enable) нужные сервисы (в моем случае первые два).

# Установка Arch Linux

## Отключение динамика материнской платы
`setterm -blength 0`

## Очистка загрузочных записей в EFI
- Проверка работы EFI: должно вывестись много строк. Если этого не произошло, значит загрузились не в режиме EFI.
`efivar -l`
- Синхронизация системных часов
`timedatectl set-ntp true`
- Вывод списка загрузочных записей в EFI
`efibootmgr`
- Если после предыдущей операционных систем остались ненужные загрузочные записи, то удаляем их
`efibootmgr -Bb <номер>` 

## Настройка сети
Если машина подключена с помощью провода по протоколу DHCP, то подключение к интернету должно уже быть установлено. Проверяем:
`ping -c 3 ya.ru`

## Разметка дисков
Для просмотра текущего состояния разметки
`fdisk -l` или `lsblk`

В моем случае имеются 3 диска: 2 SSD и 1 HDD. На первом SSD располагается уже установленный Windows 10 с EFI разделом. На второй SSD я установлю корневой раздел **/** Arch Linux. Отдельно под раздел **/var** я выделю 64 Гб на HDD. На прошлой системе **/var** был забит более, чем на 30 Гб. 

- Для разметки отдельного диска
`parted /dev/<устройство>`
- Для вывода информации о текущей разметке диска
`print`
- Для вывода справки 
`help`

> Используем тип разметки GPT (если на диске тип dos, то надо менять командами mklabel или mktable)
> Так как с ядра Linux 5.0 btrfs поддерживает SWAP-файлы, то ставим btrfs. Она уже стабильна. 

### Разметка SSD
- Не заморачиваемся с partitionless-схемой, но создаем обычный раздел на диске
`mkpart primary btrfs 0% 100%`
- Ставим метку для раздела, чтобы было красиво
`name 1 'Arch Linux'`

### Разметка HDD
> Чтобы узнать последний сектор существующего раздела следует из командной оболочки (не из parted) вызвать команду `parted /dev/<устройство> 'unit s print'`

Создаем раздел **/var** на следующем секторе после предыдущего раздела
`parted /dev/<устройство> mkpart primary btrfs 123456789s 100%`

### Форматирование разделов
`mkfs.btrfs -L <метка> -f /dev/<раздел>`
_опция -f нужна, если раздел содержит следы предыдущих файловых систем_

## Монтирование разделов
```sh
mount /dev/<корневой_раздел> /mnt
mkdir /mnt/{var,efi}
mount /dev/<раздел EFI> /mnt/efi
mount /dev/<раздел var> /mnt/var
```

## Разворачивание первоначальной системы и вход в нее
- Чтобы _btrfs_ подхватилась, необходимо также установить дополнительные пакеты при разворачивании начальной системы
`pacstrap /mnt base base-devel btrfs-progs`
- Генерируем файл точками автоматического монтирования fstab
`genfstab -U /mnt >> /mnt/etc/fstab`
_В файл /mnt/etc/fstab в записях, относящихся к разделам на SSD, следует добавить опцию 'discard', например `UUID=2c... / btrfs rw,relatime,discard 0 0`_
- Входим в установленную систему
`arch-chroot /mnt`

## Установка региональных настроек
- Часовой пояс
`ln -sf /usr/share/zoneinfo/Europe/Moscow /etc/localtime`
- Установка стандарта времени
`hwclock --systohc`
- В файле _/etc/locale.gen_ следует раскомментировать строки
`en_US.UTF-8 UTF-8`
`ru_RU.UTF-8 UTF-8`
Затем выполнить команду
`locale-gen`
- В файл _/etc/locale.conf_ вставить строку
`LANG=en_US.UTF-8`

## Настройка компьютера
- Установка имени компьютера
`echo <имя_компьютера> >> /etc/hostname`
- Добавить записи следующего вида в файл _/etc/hosts_
```
127.0.0.1    localhost
::1          localhost
127.0.1.1    <имя_компьютера>.localdomain <имя_компьютера>
```
- Установка пароля root
`passwd`

## Установка и настройка загрузчика
- Установить загрузчик, менеджер EFI и утилиту для поиска других ОС
`pacman -S grub efibootmgr os-prober`
`grub-install --target=x86_64-efi --efi-directory=/efi --bootloader-id=GRUB`
_Значение -efi-directory должно соответствовать реальной точке монтирования раздела EFI, а --bootloader может быть произвольным._
- Создать конфигурационный файл для загрузчика. os-prober автоматически должен найти запись загрузки Windows. Если этого не произошло можно попробовать повторить этот шаг повторно при обычной загрузке ОС, а не в режиме arch-chroot
`grub-mkconfig -o /boot/grub/grub.cfg`

## Перезагрузка
```sh
exit
umount -R /mnt
reboot
```

## Добавление пользователя
- Создание пользователя
`useradd -m -G ftp,games,http,log,rfkill,sys,systemd-journal,users,wheel -s /bin/bash <имя_пользователя>`
- Устанавка пароля
`passwd <имя_пользователя>`
- Установка пользователю привилегий sudo
`EDITOR=nano visudo`
В открывшемся файле добавляем строчку
`<имя_пользователя> ALL=(ALL) ALL`

## Временное включение сети
- Узнаем обозначение нужного интерфейса
`ip addr`
- Подключаемся по протоколу DHCP
`dhcpcd <интерфейс>`

## Настраиваем список зеркал
- Устанавливаем необходимый пакет
`pacman -S pacman-contrib`
- Создаем копию стандартного списка зеркал
`cp /etc/pacman.d/mirrorlist /etc/pacman.d/mirrorlist.backup`
- Запускаем сортировку зеркал (отбираются 10 самых быстрых)
`rankmirrors -n 10 /etc/pacman.d/mirrorlist.backup > /etc/pacman.d/mirrorlist`

## Настройка графики
- Установка X.org и XServer
`pacman -S xorg-server xorg-apps xorg xorg-xinit xterm xorg-twm xorg-xclock`
- Установка драйвера видеокарты
`pacman -S nvidia nvidia-settings`

> На данном этапе можно проверить работоспособность графической системы: `xinit` (используйте команду `exit` в эмуляторе терминала для выхода)

- Настройка DRM при загрузке. В файле _/etc/default/grub_ дополнить параметр `GRUB_CMDLINE_LINUX_DEFAULT` через пробел значением `nvidia-drm.modeset=1` и пересобрать конфигурационный файл загрузчика
`grub-mkconfig -o /boot/grub/grub.cfg`
- Чтобы при инициализации операционной системы подгружались модули видеокарты, необходимо их добавить в файл _/etc/mkinitcpio.conf_ в параметр MODULES (в моем случае других элементов в параметре MODULES не было): 
`MODULES=(nvidia nvidia_modeset nvidia_uvm nvidia_drm)`
Затем необходимо пересобрать образ
`mkinitcpio -P`

- При обновлении ядра Linux или драйверов видеокарты, необходимо каждый раз пересобирать образ как в пункте выше. Чтобы не делать это вручную, добавляем хук для pacman:
    + Если нет, создаем директорию
    `mkdir /etc/pacman.d/hooks`
    + Создаем файл _/etc/pacman.d/hooks/nvidia.hook_
    ```
    [Trigger]
    Operation=Install
    Operation=Upgrade
    Operation=Remove
    Type=Package
    Target=nvidia
    Target=linux

    [Action]
    Description=Update Nvidia module in initcpio
    Depends=mkinitcpio
    When=PostTransaction
    NeedsTargets
    Exec=/bin/sh -c 'while read -r trg; do case $trg in linux) exit 0; esac; done; /usr/bin/mkinitcpio -P'
    ```

- Генерируем конфигурационный файл xorg.conf с настройками видеокарты
`nvidia-xconfig`

## Установка и первоначальная настройка оконного и дисплейного менеджера
- Установка оконного менеджера и дисплейного менеджера
`pacman -S i3-gaps lightdm lightdm-gtk-greeter`
- Выбор темы дисплейного менеджера. В файле _/etc/lightdm/lightdm.conf_ раскомментировать строку и добавить следующее значение для параметра **greeter-session**
```
[Seat:*]
...
greeter-session=lightdm-gtk-greeter
```
- Включаем сервис LightDM в автозагрузку
`systemctl enable lightdm`
- Входим в графическую оболочку
`systemctl start lightdm` 

## Настройка рабочего окружения
- Установка NetworkManager
`sudo pacman -S networkmanager network-manager-applet`
`sudo systemctl enable NetworkManager`
- Установка клиента для AUR
```
mkdir ~/Downloads
cd ~/Downloads
curl -o ./yay.tar.gz https://aur.archlinux.org/cgit/aur.git/snapshot/yay.tar.gz
tar -xzf yay.tar.gz
cd yay
makepkg -s
sudo pacman -U ./yay*pkg.tar.xz
cd ..
rm -R ./yay*
```
- Установка необходимых шрифтов
`yay -S ttf-sourcesanspro nerd-fonts-complete`
- Загрузка конфигурационных файлов
```sh
sudo pacman -S git
mkdir ~/Development
cd ~/Development
git clone https://github.com/zer0jump/Configs.git

```

### Установка и настройка Zsh
- Устанавливаем Zsh
`sudo pacman -S zsh`
- Устанавливаем Oh-My-Zsh
`sh -c "$(curl -fsSL https://raw.githubusercontent.com/robbyrussell/oh-my-zsh/master/tools/install.sh)"`
- Копируем мою кастомную тему
`cp ~/Development/Configs/.oh-my-zsh/themes/gnzh-mod.zsh-theme ~/.oh-my-zsh/themes/`
- В файле _~/.zshrc_ устанавливаем нужную тему
`ZSH_THEME="gnzh-mod"`

> Чтобы увидеть изменения сразу выполните команду `source ~/.zshrc`

- Делаем небольшие правки в файлах конфигурации
_~/.zshrc_
```sh
alias vi=nvim
alias vim=nvim
```
_~/.zshenv_
```sh
typeset -U TERMINAL
TERMINAL=terminator
```

### Установка Neovim
- Устанавливаем Neovim и свежий python
`sudo pacman -S neovim python python-pip`
- Устанавливаем через PIP python-клиент для Neovim 
`sudo pip install neovim`
- Устанавливаем менеджер плагинов Plug для Neovim
`curl -fLo ~/.local/share/nvim/site/autoload/plug.vim --create-dirs \
    https://raw.githubusercontent.com/junegunn/vim-plug/master/plug.vim`
- Создаем папку с настройками
`cp -r ~/Development/Configs/.config/nvim ~/.config/nvim`
- Скачиваем цветовую схему
`curl -fLo ~/.config/nvim/colors/vim-sublime-monokai.vim --create-dirs \
    https://raw.githubusercontent.com/ErichDonGubler/vim-sublime-monokai/master/colors/sublimemonokai.vim`
- Устанавливаем утилиту для управления состоянием раскладки внутри окна
`yay -S xkb-switch`
- Внутри Neovim устанавливаем плагины
`:PlugInstall`

### Yandex.Disk
- Копируем папку c PKGBUILD
`cp -r ~/Development/Configs/packages/yandex-disk /tmp`
- При необходимости меняем версию программы и контрольные суммы. Устанавливаем
`makepkg -si`
- Инициализируем
`yandex-disk setup`
- Автозапуск
`systemctl --user enable yandex-disk.service`
- Устанавливаем индикатор для трея
`yay -S yandex-disk-indicator`

### Конфигурация i3wm
- Установка утилит для корректной работы i3wm с моим конфигурационным файлом
`sudo pacman -S i3blocks rofi pulseaudio pulsemixer pavucontrol compton dunst udiskie nitrogen guake terminator`
`sudo pacman -S firefox geoip keepass`
`yay -S kbdd-git discord vk-messenger telegram-desktop-bin`
- Чтобы PulseAudio заработал сразу, выполняем
`pulseaudio --daemonize`
- Перемещение конфигурационных файлов (не забываем слеш в конце)
`cp -r ~/Development/Configs/.config/{i3,i3blocks} ~/.config/`
- Перемещение скриптов для i3blocks
`cp -r ~/Development/Configs/.scripts ~/`
- Делаем их исполняемыми
`chmod +x ~/.scripts/statusbar/*`

### Подключение общего диска
- Создаем точку монтирования
`sudo mkdir /mnt/Storage`
- Создаем ссылку быстрого доступа к тому в папке пользователя
`ln -s /mnt/Storage ~/Storage`
- Узнаем UUID нужного раздела
`sudo blkid /dev/<раздел>`
- Добавляем запись в _/etc/fstab_
```
UUID=<значение_из_предыдущего пункта> /mnt/Storage ntfs-3g uid=1000,gid=1000,dmask=022,fmask=133 0 0
```

### Установка темных тем для GTK
- Для удобства установим wget
`sudo pacman -S wget`
- Скачиваем тему для иконок **Papirus**
`wget -qO- https://raw.githubusercontent.com/PapirusDevelopmentTeam/papirus-icon-theme/master/install.sh | sh`
- Устанавливаем тему для GTK **Materia**
`sudo pacman -S materia-gtk-theme`
- Правим конфигурационные файлы для GTK 2 и 3
_/usr/share/gtk-2.0/gtkrc_
```
gtk-icon-theme-name = "Papirus"
gtk-theme-name = "Materia-dark"
gtk-font-name = "Noto Sans 11"
```
_/usr/share/gtk-3.0/settings.ini_
```
[Settings]
gtk-icon-theme-name = Papirus
gtk-theme-name = Materia-dark
gtk-font-name = Noto Sans 11
```

#### Исправление недочетов
Чтобы избежать ситуации "темный текст на темном фоне" в Firefox и Keepass2 поменяем для них тему на Materia (светлая тема с темными служебными элементами)
1. В Firefox в поле адреса вводим `about:config`
2. По правой кнопке мыши создаем новый параметр типа _string_ с названием **widget.content.gtk-theme-override** и значением `Materia`.
3. Перезапускаем браузер.
4. Правим файл `sudo vim /usr/share/applications/keepass.desktop`
5. Значение параметра **Exec** меняем на `env GTK2_RC_FILES=/usr/share/themes/Materia/gtk-2.0/gtkrc keepass`
6. Перезапускаем Keepass

## Дополнительные программы

### Официальные репозитории
- nemo _файловый менеджер_
- nemo-fileroller _плагин к нему для работы с архивами на месте_

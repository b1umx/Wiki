# Установка VirtualBox

1. Устанавливаем сам пакет (выбираем в процессе установки virtualbox-host-modules-arch, если вы не строили систему на альтернативных ядрах): `sudo pacman -S virtualbox`
2. Чтобы запустить модули ядра для VirtualBox вручную без перезагрузки: `sudo modprobe vboxdrv vboxnetadp vboxnetflt vboxpci`
3. После обновления ядра необходимо перезагрузить модули с помощью команды `sudo vboxreload`
4. Чтобы использовать USB-устройства на гостевой машине надо добавить пользователя в группу: `sudo usermod -a -G vboxusers <username>`
5. Чтобы устанавливать на гостевых машинах гостевые дополнения, необходимо установить: `sudo pacman -S virtualbox-guest-iso`.
6. Устанавливаем дополнения: `yay -S virtualbox-ext-oracle`

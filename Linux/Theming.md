# GTK-темы

Установка темы Papirus для иконок
`wget -qO- https://raw.githubusercontent.com/PapirusDevelopmentTeam/papirus-icon-theme/master/install.sh | sh`

Установка темы Materia для GTK
`sudo pacman -S materia-gtk-theme`

Настройка конфигов
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

## Исправление недочетов

Чтобы избежать ситуации "темный текст на темном фоне" в Firefox и Keepass2 поменяем для них тему на Materia (светлая тема с темными служебными элементами)
1. В Firefox в поле адреса вводим `about:config`
2. По правой кнопке мыши создаем новый параметр типа _string_ с названием **widget.content.gtk-theme-override** и значением `Materia`.
3. Перезапускаем браузер.
4. Правим файл `sudo vim /usr/share/applications/keepass.desktop`
5. Значение параметра **Exec** меняем на `env GTK2_RC_FILES=/usr/share/themes/Materia/gtk-2.0/gtkrc keepass`
6. Перезапускаем Keepass

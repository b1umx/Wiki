# Настройка клавиатуры и шрифтов

## Настройка раскладки в Xorg

Настраиваем переключение раскладки (US, RU) по сочетанию клавиш Alt+Shift
_/etc/X11/xorg.conf.d/00-keyboard.conf_
```sh
Section "InputClass"
	Identifier "system-keyboard"
	MatchIsKeyboard "on"
	Option "XkbLayout" "us,ru"
	Option "XkbModel" "pc105"
	Option "XkbVariant" ","
	Option "XkbOptions" "grp:alt_shift_toggle"
EndSection
```

## Список интересных шрифтов
*Моноширинные*
- PT Mono
- Droid Sans Mono
- DejaVu Sans Mono
- Cousine
- Ubuntu Mono
- Fira Mono
- Fira Code
- Inconsolata
- Anonymous Pro
- Hack

*Текстовые*
- DejaVu
- Fira Sans

### Шрифты, доступные через Pacman:
```
pacman -S ttf-ubuntu-font-family ttf-inconsolata ttf-fira-mono ttf-fira-sans ttf-fira-code ttf-droid ttf-anonymous-pro ttf-hack ttf-dejavu
```

### Обновления кэша
После добавления шрифтов в директорию ~/.fonts выполните команду
```bash
fc-cache -vf
```

# Конфигурация i3

В качестве менеджера обоев используем Nitrogen
`pacman -S nitrogen`

# Обои
Установить обои можно следующим образом
`nitrogen <dir>`

Чтобы обои устанавливались после перезагрузки:
_~/.xsession_
```
#!/bin/zsh
nitrogen --restore &
```

Установить i3blocks
`pacman -S i3blocks`


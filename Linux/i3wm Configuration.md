# Конфигурация i3

В качестве менеджера обоев используем Nitrogen
`pacman -S nitrogen`

## Обои
Установить обои можно следующим образом
`nitrogen <dir>`

Чтобы обои устанавливались после перезагрузки:
_~/.xsession_
```
#!/bin/zsh
nitrogen --restore &
```

## Сделать окно плавающим
На примере KeePass

_~/.config/i3/config_
```
for_window [class="KeePass2"] floating enable
for_window [class="KeePass2"] move scratchpad
for_window [class="KeePass2"] sticky enable 
for_window [class="KeePass2"] scratchpad show 
for_window [class="KeePass2"] resize set 1200 675
for_window [class="KeePass2"] move position center
```




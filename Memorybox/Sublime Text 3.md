# Установка и настройка Sublime Text 3

- Для работы Sublime Text 3 необходимы библиотеки GTK2
```pacman -S gtk2```

## Устанавливка Package Control
- Открываем консоль Ctrl+`
- Выполняем команду
```python
import urllib.request,os,hashlib; h = '6f4c264a24d933ce70df5dedcf1dcaee' + 'ebe013ee18cced0ef93d5f746d80ef60'; pf = 'Package Control.sublime-package'; ipp = sublime.installed_packages_path(); urllib.request.install_opener( urllib.request.build_opener( urllib.request.ProxyHandler()) ); by = urllib.request.urlopen( 'http://packagecontrol.io/' + pf.replace(' ', '%20')).read(); dh = hashlib.sha256(by).hexdigest(); print('Error validating download (got %s instead of %s), please try manual install' % (dh, h)) if dh != h else open(os.path.join( ipp, pf), 'wb' ).write(by)
```
- Открываем меню, из которого можно вызвать Package Control, можно сочетанием клавиш `Ctrl+Shift+P`

## Полезные плагины для Sublime Text 3
- Emmet
- SideBarEnhancements
- Git
- Terminal
- CSSComb
- Trimmer
- ColorPicker
- MarkdownPreview
- LiveReload (включите после установки плагин 'Simple Reload' в выпадающем меню Sublime)
- MarkdownEditing
- Modific
- BracketHighlighter
- SublimeCodeIntel
- SublimeREPL
- PlainTasks
- Afterglow (theme)
- AutoSetSyntax

## Конфиг для Sublime Text 3
```json
{
    "color_scheme": "Packages/Theme - Afterglow/Afterglow.tmTheme",
    "ensure_newline_at_eof_on_save": true,
    "ignored_packages":
    [
        "Vintage"
    ],
    "tabs_small": true,
    "theme": "Afterglow-green.sublime-theme",
    "translate_tabs_to_spaces": true,
    "trim_trailing_white_space_on_save": true,
    "show_encoding": false
}
```

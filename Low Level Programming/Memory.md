# Особенности памяти в программах

## Расположение локальных переменных в памяти
Исходный текст программы:
```c
void main()
{
    int a = 9872635;
    char b = 'a';
    int c = 2343452;
}
```
Вид стека, начиная с последней переменной 'c':
```
0xbffff83c: 0x1c 0xc2 0x23 0x00   ; 0x0023c21c = 2343452
0xbffff840: 0xe0 0x0c 0x00 0x61   ; 0x61 = 'a'
0xbffff844: 0xfb 0xa4 0x96 0x00   ; 0x0096a4fb = 9872635
```

## Сегментация памяти
Исходный текст программы:
```c
#include <stdio.h>

int global_int = 2077777777;
short global_short = 31777;

void inner(int inner_arg_int, short inner_arg_short)
{
    static int inner_stat_int = 2066666666;

    int inner_local_int = 2055555555;
    short inner_local_short = 31555;

    printf("\t\t[функция inner] global_int @ %p = %d (%08x)\n", &global_int, global_int, global_int);
    printf("\t\t[функция inner] global_short @ %p = %d (%04x)\n", &global_short, global_short, global_short);
    printf("\t\t[функция inner] inner_stat_int @ %p = %d (%08x)\n", &inner_stat_int, inner_stat_int, inner_stat_int);

    printf("\t\t[функция inner] inner_local_int @ %p = %d (%08x)\n", &inner_local_int, inner_local_int, inner_local_int);
    printf("\t\t[функция inner] inner_local_short @ %p = %d (%04x)\n", &inner_local_short, inner_local_short, inner_local_short);
    printf("\t\t[функция inner] inner_arg_int @ %p = %d (%08x)\n", &inner_arg_int, inner_arg_int, inner_arg_int);
    printf("\t\t[функция inner] inner_arg_short @ %p = %d (%04x)\n", &inner_arg_short, inner_arg_short, inner_arg_short);
}

void outer(int outer_arg_int, short outer_arg_short)
{
    static int outer_stat_int = 2033333333;

    int outer_local_int = 2022222222;
    short outer_local_short = 31222;

    printf("\t[функция outer] global_int @ %p = %d (%08x)\n", &global_int, global_int, global_int);
    printf("\t[функция outer] global_short @ %p = %d (%04x)\n", &global_short, global_short, global_short);
    printf("\t[функция outer] outer_stat_int @ %p = %d (%08x)\n", &outer_stat_int, outer_stat_int, outer_stat_int);

    printf("\t[функция outer] outer_local_int @ %p = %d (%08x)\n", &outer_local_int, outer_local_int, outer_local_int);
    printf("\t[функция outer] outer_local_short @ %p = %d (%04x)\n", &outer_local_short, outer_local_short, outer_local_short);
    printf("\t[функция outer] outer_arg_int @ %p = %d (%08x)\n", &outer_arg_int, outer_arg_int, outer_arg_int);
    printf("\t[функция outer] outer_arg_short @ %p = %d (%04x)\n", &outer_arg_short, outer_arg_short, outer_arg_short);

    inner(2044444444, 31444);
}

int main()
{
    outer(2011111111, 31111);

    return 0;
}
```

Вывод программы:

>[функция outer] global_int @ 0x8049b54 = 2077777777 (7bd85f71)
>[функция outer] global_short @ 0x8049b58 = 31777 (7c21)
>[функция outer] outer_stat_int @ 0x8049b60 = 2033333333 (79323455)
>[функция outer] outer_local_int @ 0xbffff830 = 2022222222 (7888a98e)
>[функция outer] outer_local_short @ 0xbffff82e = 31222 (79f6)
>[функция outer] outer_arg_int @ 0xbffff840 = 2011111111 (77df1ec7)
>[функция outer] outer_arg_short @ 0xbffff836 = 31111 (7987)
>    [функция inner] global_int @ 0x8049b54 = 2077777777 (7bd85f71)
>    [функция inner] global_short @ 0x8049b58 = 31777 (7c21)
>    [функция inner] inner_stat_int @ 0x8049b5c = 2066666666 (7b2ed4aa)
>    [функция inner] inner_arg_int @ 0xbffff800 = 2044444444 (79dbbf1c)
>    [функция inner] inner_arg_short @ 0xbffff7fe = 31444 (7ad4)
>    [функция inner] inner_arg_int @ 0xbffff810 = 2044444444 (79dbbf1c)
>    [функция inner] inner_arg_short @ 0xbffff806 = 31444 (7ad4)

Адреса секций
0x080482d4 - 0x080486a0 .text
...
0x08049b48 - 0x08049b5c .data
0x08049b5c - 0x08049b64 .bss

## Структура стека
`EBP - <N>`: локальные переменные
`EBP + <N>`: аргументы функции

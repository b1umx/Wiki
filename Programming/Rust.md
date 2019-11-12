# Полезные сведения о Rust

## Одно ограничение уникальной (изменяемой ссылки)
Единственное, что нельзя делать с уникольной ссылкой `&mut` - перемещать значения из-под нее (без замены).

Код ниже не скомпилиурется:
```rs
struct A {
    field: Option<String>
}

let mut a = A { field: Some("a".to_string()) };
let r = &mut a;
let r2 = r.field;
```

Чтобы обойти этот эффект, можно использовать `std::mem::replace`
```rs
use std::mem;

struct A {
    field: Option<String>
}

let mut a = A { field: Some("a".to_string()) };
let r = &mut a;
let r2 = mem::replace(&mut r.field, None);
```

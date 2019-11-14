# Полезные сведения о Rust

## Одно ограничение уникальной (изменяемой ссылки)
Единственное, что нельзя делать с уникальной ссылкой `&mut` - перемещать значения из-под нее (без замены).

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

## Нативная замена для Option
Предыдущий код можно заменить на более лаконичный с использованием аналогичной по эффекту функции `take()` из API Option
```rs
struct A {
    field: Option<String>
}

let mut a = A { field: Some("a".to_string()) };
let r = &mut a;
let r2 = r.field.take();
```

## Отображение в Option
Выражение
```rs
match option {
    None => None,
    Some(x) => Some(y),
}
```

можно заменить на
```rs
option.map(|x| { y });
```

## Организация тестов
Чтобы тесты собирались только при тестировании:
```rs
#[cfg(test)]
```

Чтобы имена функций тестов не пересекались с именами в остальном коде:
```rs
mod test {

}
```

Чтобы ввести в область видимости тестируемый код (для случая, когда тесты в том же файле):
```rs
use super::Foo;
```

Все вместе:
```rs
#[cfg(test)]
mod test {
    use super::Foo

    #[test]
    fn bar() {
        unimplemented!();
    }
}
```

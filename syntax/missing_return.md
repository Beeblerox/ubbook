# Забытый `return`

Про C/C++ иногда говорят, что это языки, в которых есть специальный синтаксис для написания невалидных программ.

В C/C++ в функции, возвращающей что-то, отличное от `void`, не обязательно должен быть `return что-то`. 

```C++
int add(int x, int y) {
    x + y;
}
```

Это синтаксически корректная функция, которая приведет к неопределенному поведению. Может быть мусор, может быть провал в код следующей далее по коду функции, а может быть и [«все нормально»](https://gcc.godbolt.org/z/6Y4T66).

Особенную боль это недоразумение может доставить тем, кто пришел в C++ после какого-нибудь ориентированного на выражения языка, в котором похожий код абсолютно нормален:

```Rust
fn add(x: i32, y: i32) -> i32 {
    x + y
}
```

Обоснования, почему не обязательно писать в конце функции `return`, следующие:
1. В функции может быть ветвление логики. В одной из веток может вызываться код, который не предполагает возврата: бесконечный цикл, исключение, `std::exit`, `std::longjmp` или что-то иное, помеченное аттрибутом `[[noreturn]]`. Проверить на наличие такого кода не всегда возможно.
2. Функция может содержать ассемблерную вставку со специальным кодом финализации и инструкцией `ret`.

Проверить наличие формального `return`, конечно, можно. Но нам разрешили не писать иногда (очень иногда!) чисто формальную строчку, а компиляторам разрешили не считать это ошибкой.

------

С флагом `-Wreturn-type` GCC и clang во многих случаях сообщают о проблеме.

-----
Единственным исключением, начиная с C++11, является функция `main`. В ней отсутствующий `return` к неопределенному поведению не приводит и трактуется как возврат 0.

## Полезные ссылки
1. https://stackoverflow.com/questions/1610030/why-does-flowing-off-the-end-of-a-non-void-function-without-returning-a-value-no
2. https://en.cppreference.com/w/cpp/language/attributes/noreturn
Відповідь: `1`.

```js run
let i = 3;

while (i) {
  alert( i-- );
}
```

Кожна ітерація циклу зменшує `i` на `1`. Перевірка `while (i)` зупинить цикл, коли `i = 0`.

Відповідно, ось так буде виконуватися цикл («розгорнемо» цикл):

```js
let i = 3;

alert(i--); // покаже 3, потім зменшить i до 2

alert(i--) // покаже 2, потім зменшить i до 1

alert(i--) // покаже 1, потім зменшить i до 0

// все, перевірка while (i) зупинить цикл
```

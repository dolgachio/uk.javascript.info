Нам потрібно знайти початок коментарю `match:<!--`, та його зміст до самого кінця `match:-->`.

Прийнятним є варіант `pattern:<!--.*?-->` -- лінивий квантифікатор зупиняє крапку (будь-який символ, за винятком символу нового рядку) прямо перед `match:-->`. Нам також треба додати прапорець `pattern:s`, аби крапка включала символи нового рядку.

Інакше коментарі на кілька рядків не знаходитимуться:

```js run
let regexp = /<!--.*?-->/gs;

let str = `... <!-- Мій -- коментар
 test --> ..  <!----> ..
`;

alert( str.match(regexp) ); // '<!-- Мій -- коментар \n test -->', '<!---->'
```

Щоб точно відповідати функціональності конструкції `switch`, `if` повинен використовувати строге порівняння `'==='`.

Хоча для даних рядків звичайне `'=='` також працює.

```js no-beautify
if(browser == 'Edge') {
  alert("You've got the Edge!");
} else if (browser == 'Chrome'
 || browser == 'Firefox'
 || browser == 'Safari'
 || browser == 'Opera') {
  alert( 'Ми підтримуємо і ці браузери' );
} else {
  alert( 'Маємо надію, що ця сторінка виглядає добре!' );
}
```

Зверніть увагу: конструкція `browser == 'Chrome' || browser == 'Firefox' …` розділена на кілька рядків для кращої читабельності.

Але конструкція `switch` більш чистіша та наочніша.

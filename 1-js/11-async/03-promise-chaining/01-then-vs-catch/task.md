# Проміс: then проти catch

Чи рівні ці фрагменти коду? Іншими словами, чи поводяться вони однаково за будь-яких обставин для будь-яких функцій обробника?

```js
promise.then(f1).catch(f2);
```

Проти:

```js
promise.then(f1, f2);
```

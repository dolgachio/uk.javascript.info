importance: 5

---

# Максимальна зарплата 

Є об’єкт `salaries`:

```js
let salaries = {
  "Іван": 100,
  "Петро": 300,
  "Марія": 250
};
```

Створіть функцію `topSalary(salaries)` яка повертає ім’я найбільш високооплачуваної особи.

- Якщо об’єкт `salaries` пустий, функція повинна повернути `null`.
- Якщо є кілька високооплачуваних осіб, поверніть будь-якого з них.

P.S. Використовуйте `Object.entries` і деструктурування для перебору пар ключ/значення.

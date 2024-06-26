importance: 5

---

# Створіть дерево з об’єкту

Напишіть функцію `createTree` яка створює вкладений `ul/li` список з вкладеного об’єкта.

Наприклад:

```js
let data = {
  Риба: {
    форель: {},
    лосось: {}
  },

  Дерево: {
    Величезні: {
      секвойя: {},
      дуб: {}
    },
    Квітучі: {
      яблуня: {},
      магнолія: {}
    }
  }
};
```

Синтаксис:

```js
let container = document.getElementById('container');
*!*
createTree(container, data); // створює дерево в контейнері
*/!*
```

Результат (дерево) має виглядати так:

[iframe border=1 src="build-tree-dom"]

Виберіть один із двох способів вирішення цього завдання:

1. Створіть HTML для дерева, а потім призначте в `container.innerHTML`.
2. Створіть вузли дерева та додайте їх за допомогою методів DOM.

Було б чудово, якби ви могли зробити обидва.

P.S. Дерево не повинно мати "зайвих" елементів, як-от порожній `<ul></ul>` для листя.

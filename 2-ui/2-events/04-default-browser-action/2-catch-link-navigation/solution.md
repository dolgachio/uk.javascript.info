Це чудове використання шаблону делегування подій.

У реальному житті замість того, щоб запитувати, ми можемо надіслати запит на «реєстрацію» на сервер, який зберігає інформацію про те, куди пішов відвідувач. Або ми можемо завантажити вміст і показати його прямо на сторінці (якщо це дозволено).

Все, що нам потрібно, це зловити `contents.onclick` і використати `confirm`, щоб запитати користувача. Хорошою ідеєю було б використовувати `link.getAttribute('href')` замість `link.href` для URL-адреси. Подробиці дивіться у рішенні.

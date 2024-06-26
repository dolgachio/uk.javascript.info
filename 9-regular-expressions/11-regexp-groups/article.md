# Групи захоплення

Частину виразу можна обгорнути в круглі дужки `pattern:(...)`. Це називається "група захоплення".

Такий прийом має два наслідки:

1. Він дозволяє отримати частину збігу в якості окремого елементу в масиві результатів.
2. Якщо ми поставимо квантифікатор після дужок, він застосується до всього їх вмісту.

## Приклади

Розглянемо як працюють круглі дужки на прикладах.

### Приклад: gogogo

Без круглих дужок, вираз `pattern:go+` означає символ `subject:g`, за яким слідує `subject:o` на повторі один чи кілька разів. Тобто, `match:goooo` чи `match:gooooooooo`.

Круглі дужки об’єднують символи в групи, тож `pattern:(go)+` означає `match:go`, `match:gogo`, `match:gogogo` і так далі.

```js run
alert( 'Gogogo now!'.match(/(go)+/ig) ); // "Gogogo"
```

### Приклад: домен

Зробимо дещо складніше -- регулярний вираз для пошуку домену сайту.

Наприклад:

```
mail.com
users.mail.com
smith.users.mail.com
```

Як бачимо, домен складається з повторюваних слів та крапки після кожного з них (окрім останнього).

В регулярних виразах це `pattern:(\w+\.)+\w+`:

```js run
let regexp = /(\w+\.)+\w+/g;

alert( "site.com my.site.com".match(regexp) ); // site.com,my.site.com
```

Пошук працює, але патерн не збігатиметься з доменом з дефісом: наприклад, `my-site.com`, бо дефіс не належить до класу `pattern:\w`.

Ми можемо виправити це, замінивши `pattern:\w` на `pattern:[\w-]` в кожному слові (окрім останнього): `pattern:([\w-]+\.)+\w+`.

### Приклад: email

Попередній приклад можна розширити. Спираючись на нього, ми можемо створити регулярний вираз для адрес електронної пошти.

Формат електронної пошти: `name@domain`. Name може бути будь-яким словом, дефіси та крапки допускаються. В регулярних виразах це `pattern:[-.\w]+`.

Вираз є наступним:

```js run
let regexp = /[-.\w]+@([\w-]+\.)+[\w-]+/g;

alert("my@mail.com @ his@site.com.uk".match(regexp)); // my@mail.com, his@site.com.uk
```

Це неідеальний регулярний вираз, але здебільшого робочий та здатний виправити випадкові помилки. Єдина справді надійна перевірка електронної пошти -- відправка листа.

## Вміст дужок всередині збігу

Дужки нумеруються зліва направо. Пошукова система запам'ятовує вміст збігу для кожної з них та дозволяє отримати його всередині результату.

У випадку, якщо `regexp` не містить прапорця `g`, метод `str.match(regexp)` шукає перший збіг та повертає його як масив:

1. Індекс `0`: повний збіг.
2. Індекс `1`: вміст перших дужок.
3. Індекс `2`: вміст других дужок.
4. ...і так далі...

Для прикладу, ми б хотіли знайти HTML теги `pattern:<.*?>` та обробити їх. Було б зручно мати вміст тегу (все, що всередині кутових дужок) в окремій змінній.

Огорнемо внутрішній вміст у круглі дужки, ось так: `pattern:<(.*?)>`.

Тепер ми отримаємо як тег в цілому `match:<h1>`, так і його вміст `match:h1` в отриманому масиві:

```js run
let str = '<h1>Вітаю, світе!</h1>';

let tag = str.match(/<(.*?)>/);

alert( tag[0] ); // <h1>
alert( tag[1] ); // h1
```

### Вкладені групи

Дужки можуть бути вкладеними. В нашому випадку, нумерація також проводиться зліва направо.

Наприклад, під час пошуку тегу в `subject:<span class="my">`, нас може цікавити:

1. Вміст тегу загалом: `match:span class="my"`.
2. Ім'я тегу: `match:span`.
3. Атрибути тегу: `match:class="my"`.

Додамо до них дужки: `pattern:<(([a-z]+)\s*([^>]*))>`.

Ось як вони нумеруються (зліва направо, за відкриваючою дужкою):

![](regexp-nested-groups-pattern.svg)

Код у дії:

```js run
let str = '<span class="my">';

let regexp = /<(([a-z]+)\s*([^>]*))>/;

let result = str.match(regexp);
alert(result[0]); // <span class="my">
alert(result[1]); // span class="my"
alert(result[2]); // span
alert(result[3]); // class="my"
```

Повний збіг завжди представлений в елементі з нульовим індексом в `result`.

Далі йдуть групи, нумеровані зліва направо за відкриваючою дужкою. Перша група розміщена в `result[1]`. В даному випадку, вона охоплює весь вміст тегу.

Далі, в `result[2]` отримуємо групу з других дужок `pattern:([a-z]+)` - ім’я тегу, в `result[3]` - тег `pattern:([^>]*)`.

Вміст кожної групи в рядку:

![](regexp-nested-groups-matches.svg)

### Необов’язкові групи

Навіть якщо група необов’язкова та не існує всередині збігу (тобто має квантифікатор `pattern:(...)?`), відповідний елемент масиву `result` наявний та дорівнює `undefined`.

Для прикладу, розглянемо регулярний вираз `pattern:a(z)?(c)?`. Він шукає `"a"`, після якої може йти `"z"`, після якої може йти `"c"`.

Якщо використати цей вираз для рядку з єдиною літерою `subject:a`, тоді результатом буде:

```js run
let match = 'a'.match(/a(z)?(c)?/);

alert( match.length ); // 3
alert( match[0] ); // a (повний збіг)
alert( match[1] ); // undefined
alert( match[2] ); // undefined
```

Довжина масиву дорівнює `3`, але всі групи порожні.

Ось приклад більш комплексного збігу для рядку `subject:ac`:

```js run
let match = 'ac'.match(/a(z)?(c)?/)

alert( match.length ); // 3
alert( match[0] ); // ac (повний збіг)
alert( match[1] ); // undefined, бо для (z)? нічого не знайшлось
alert( match[2] ); // c
```

Довжина масиву є незмінною: `3`. Але для групи `pattern:(z)?` нічого не знайшлось, тому в результаті отримуємо `["ac", undefined, "c"]`.

## Пошук усіх збігів з групами: matchAll

```warn header="`matchAll` є новим методом, може знадобитись поліфіл"
Метод `matchAll` не підтримується старими браузерами.

Поліфіл може бути обов’язковим, такий як <https://github.com/ljharb/String.prototype.matchAll>.
```

Коли ми шукаємо всі збіги (прапорець `pattern:g`), метод `match` не повертає вміст для груп.

Для прикладу, знайдемо всі теги в рядку:

```js run
let str = '<h1> <h2>';

let tags = str.match(/<(.*?)>/g);

alert( tags ); // <h1>,<h2>
```

Як результат отримуємо масив збігів, але без індивідуальних деталей. На практиці ж ми зазвичай потребуємо вміст груп захоплення.

Аби їх отримати, ми маємо здійснювати пошук методом `str.matchAll(regexp)`.

Його додали в JavaScript набагато пізніше за `match` в якості "нової та покращеної версії".

Як і `match`, метод шукає збіги, але з урахуванням 3-ьох відмінностей:

1. Він повертає не масив, а ітерований об’єкт.
2. За наявності прапорцю `pattern:g`, він повертає кожен збіг у вигляді масиву з групами.
3. Якщо збігів немає, він повертає замість `null` порожній ітерований об’єкт.

Наприклад:

```js run
let results = '<h1> <h2>'.matchAll(/<(.*?)>/gi);

// results - є не масивом, а ітерованим об’єктом
alert(results); // [object RegExp String Iterator]

alert(results[0]); // undefined (*)

results = Array.from(results); // перетворимо його на масив

alert(results[0]); // <h1>,h1 (перший тег)
alert(results[1]); // <h2>,h2 (другий тег)
```

Як можемо бачити, перша відмінність дуже важлива, як демонструється в рядку `(*)`. Ми не можемо отримати збіг у вигляді `results[0]`, бо цей об’єкт не є псевдомасивом. Ми можемо перетворити його на реальний `Array`, використовуючи `Array.from`. Детальніше про псевдомасиви та ітеровані об’єкти в статті <info:iterable>.

Нема потреби в `Array.from`, якщо ми циклічно проходимось по результатам:

```js run
let results = '<h1> <h2>'.matchAll(/<(.*?)>/gi);

for(let result of results) {
  alert(result);
  // перший alert: <h1>,h1
  // другий: <h2>,h2
}
```

...Або використаємо деструктуроване присвоєння:

```js
let [tag1, tag2] = '<h1> <h2>'.matchAll(/<(.*?)>/gi);
```

Кожен збіг, повернутий `matchAll`, форматується аналогічно до результату функції `match` без прапору `pattern:g`: масив з додатковими властивостями `index` (позиція збігу в рядку) та `input` (вихідний рядок):

```js run
let results = '<h1> <h2>'.matchAll(/<(.*?)>/gi);

let [tag1, tag2] = results;

alert( tag1[0] ); // <h1>
alert( tag1[1] ); // h1
alert( tag1.index ); // 0
alert( tag1.input ); // <h1> <h2>
```

```smart header="Чому результатом `matchAll` є ітерований об’єкт, а не масив?"
Чому цей метод так спроектовано? Причина проста - заради оптимізації.

Виклик `matchAll` не проводить пошуку. Натомість, повертається ітерований об’єкт, спочатку без результатів. Пошук проводиться кожного разу ми проходимось об’єктом, наприклад, всередині циклу.

Тобто, буде знайдено потрібну кількість результатів, не більше.

Наведемо приклад: потенційно, в тексті існує 100 збігів, але всередині циклу `for..of` ми знайшли лише 5, тоді вирішили, що цього достатньо, та викликали `break`. Тоді система не витрачатиме час на пошук 95 інших збігів.
```

## Іменовані групи

Пам’ятати групи за номерами важко. З простими виразами впоратись можна, але з підвищенням рівня складності рахувати дужки стає незручно. Існує кращий варіант: дати імена круглим дужкам.

Робиться це за допомогою `pattern:?<name>` одразу після відкриваючої дужки.

На прикладі нижче, пошукаємо дату в форматі "year-month-day":

```js run
*!*
let dateRegexp = /(?<year>[0-9]{4})-(?<month>[0-9]{2})-(?<day>[0-9]{2})/;
*/!*
let str = "2019-04-30";

let groups = str.match(dateRegexp).groups;

alert(groups.year); // 2019
alert(groups.month); // 04
alert(groups.day); // 30
```

Як ви можете бачити, групи розташовані всередині властивості збігу `.groups`.

Аби зробити пошук по всіх датах, ми можемо додати прапор `pattern:g`.

Нам також знадобиться `matchAll` для отримання повних збігів, разом з групами:

```js run
let dateRegexp = /(?<year>[0-9]{4})-(?<month>[0-9]{2})-(?<day>[0-9]{2})/g;

let str = "2019-10-30 2020-01-01";

let results = str.matchAll(dateRegexp);

for(let result of results) {
  let {year, month, day} = result.groups;

  alert(`${day}.${month}.${year}`);
  // перший alert: 30.10.2019
  // другий: 01.01.2020
}
```

## Групи захоплення для заміни

Метод `str.replace(regexp, replacement)` замінює всі збіги з `regexp` всередині `str` дозволяє користуватись вмістом дужок в рядку `replacement`. Це зроблено за допомогою `pattern:$n`, де `pattern:n` - номер групи.

Наприклад,

```js run
let str = "John Bull";
let regexp = /(\w+) (\w+)/;

alert( str.replace(regexp, '$2, $1') ); // Bull, John
```

Посилання на іменовані дужки можливе через `pattern:$<name>`.

Наприклад, реформатуємо дати з "year-month-day" на "day.month.year":

```js run
let regexp = /(?<year>[0-9]{4})-(?<month>[0-9]{2})-(?<day>[0-9]{2})/g;

let str = "2019-10-30, 2020-01-01";

alert( str.replace(regexp, '$<day>.$<month>.$<year>') );
// 30.10.2019, 01.01.2020
```

## Групи без захоплення з ?:

Іноді ми потребуємо дужки для коректного застосування квантифікатора, але не хочемо бачити їх вміст в результатах.

Групу можна виключити, додавши на початку `pattern:?:`.

Для прикладу, якщо ми хочемо знайти `pattern:(go)+`, але не потребуємо вміст дужок (`go`) окремим елементом масиву, ми можемо написати: `pattern:(?:go)+`.

В прикладі нижче, ми отримуємо лише ім’я `match:John` окремим елементом збігу:

```js run
let str = "Gogogo John!";

*!*
// ?: захоплення не включає в себе 'go'
let regexp = /(?:go)+ (\w+)/i;
*/!*

let result = str.match(regexp);

alert( result[0] ); // Gogogo John (повний збіг)
alert( result[1] ); // John
alert( result.length ); // 2 (інших елементів у масиві немає)
```

## Підсумки

Круглі дужки об’єднують в групу частину регулярного виразу, аби повноцінно застосувати до неї квантифікатор.

Групи дужок нумеруються зліва направо та за бажанням можуть бути іменовані за допомогою `(?<name>...)`.

Вміст, що збігається з групою, може бути отриманий в результатах:

- Метод `str.match` повертає групи захоплення лише без прапорцю `pattern:g`.
- Метод `str.matchAll` завжди повертає групи захоплення.

Якщо в дужок нема імені, їх вміст доступний в масиві збігу за їх номером. Іменовані дужки також доступні через властивість `groups`.

Ми також можемо використовувати вміст дужок в рядку для заміни в `str.replace`: за номером `$n` або ім’ям `$<name>`.

Групу можна виключити з нумерації, додавши `pattern:?:` на її початку. Цей прийом використовується за необхідності застосувати квантифікатор до всієї групи, але без появи окремого елементу в масиві результатів. Також, ми не можемо посилатись на такі дужки в рядку для заміни.

![КДПВ: Жёлтый магнит с надписью «JS ES10» на экране монитора —  от kasper.green & elfafeya.art](https://habrastorage.org/webt/j-/g9/na/j-g9nan1y54iew_qzum-eodzfgw.png)
        <cite>Фото: kasper.green; Жёлтый магнит: elfafeya.art & kasper.green</cite>

## Stage 4 — Final

•      **```catch```** — аргумент стал необязательным; <sup>[#](#neobyazatelnyy-argument-u-catch)</sup>

•      **```Symbol().description```** — акцессор к описанию символа;

•      **```'строки EcmaScript'```** — улучшеная совместимость с **JSON** форматом; <sup>[#](#stroki-ecmascript-sovmestimye-s-json)</sup>

•      **```.toString()```** — прототипный метод обновлён.

---------------------------

## Stage 3 — Pre-release

•      **```#```** —  приватное всё у классов, через октоторп;

•      **```#!/usr/bin/env node```**  — шебанг грамматика для скриптов;

•      **```BigInt()```** — новый примитив, для чисел произвольной точности;

•      **```globalThis```** — новый способ доступа к глобальному контексту;

•      **```import(dynamic)```** — динамический импорт;

•      **```import.meta```** — мета-информация о загружаемом модуле;

•      **```Object.fromEntries()```** — создание объекта через массив пар — ключ\значение;

•      **```RegExp```** — устаревшие возможности;

•      **```.trimStart()```** и **```.trimEnd()```** — прототипные методы строк;

•      **```.matchAll()```** — **```.match()```** с глобальным флагом;

•      **```.flat()```** и **```.flatMap()```** — прототипные методы массивов.



<cut />

----------------------

### Всего пять стадий:

   <sub>*Stage*</sub> **0**   ↓  &thinsp;**Strawman**  <sup>**Наметка**</sup>          &thinsp;Идея которую можно реализовать через **Babel**-плагин.;

   <sub>*Stage*</sub> **1**   ↓  &thinsp;**Proposal**  <sup>**Предложение**</sup>    &thinsp;Проверка жизнеспособности идеи.;

   <sub>*Stage*</sub> **2**   ↓  &thinsp;**Draft**  <sup>**Черновик**</sup>                 &thinsp;Начало разработки спецификации.;

   <sub>*Stage*</sub> **3**   ↓  &thinsp;**Candidate**  <sup>**Кандидат**</sup>         Предварительная версия спецификации.;

   <sub>*Stage*</sub> **4**  ֍  **Finished**  <sup>**Завершён**</sup>          &thinsp;Финальная версия спецификации на этот год.

----------------------------

Мы рассмотрим только *Stage* **4** — дефакто вошедший в стандарт.
И *Stage* **3** — который вот-вот станет его частью.

----------------------------






## Stage 4

Эти изменения уже вошли в стандарт.

 
 




### Необязательный аргумент у `catch`

<https://github.com/tc39/proposal-optional-catch-binding>

До *ES*10 блок ```catch``` требовал обязательного аргумента,
для сбора информации об ошибке, даже если она не используется:

```javascript
function isValidJSON(text) {
  try {
    JSON.parse(text);
    return true;
  } catch(unusedVariable) { // переменная не используется
    return false;
  }
}
```

*Edge* пока не обновлён до *ES*10 и ожидаемо валится с ошибкой.

![](https://habrastorage.org/webt/ez/l2/2d/ezl22di9ciu-4g60nlqyuqne7lk.png)



Начиная с редакции *ES*10, круглые скобки можно опустить
и ```catch``` стенет как две капли воды похож на ```try```:

![](https://habrastorage.org/webt/yi/ia/qg/yiiaqgiclyxz_i7bf3gq14dj-8m.png)
<spoiler title="исходный код">
```javascript
function isValidJSON(text) {
  try {
    JSON.parse(text);
    return true;
  } catch { // без аргумента
    return false;
  }
}
```
</spoiler>
<sup>* мой Chrome уже обновился до *ES*10, а местами и до *Stage* **3**. Последующие примеры будут в нём.


 
 


### Строки EcmaScript совместимые с JSON
<https://github.com/tc39/proposal-json-superset>

EcmaScript до десятой редакции утверждает,
что *JSON* является подмножеством `JSON.parse`, но это неверно.

*JSON* строки могут содержать неэкранированные символы
разделителей линий <sup>`U+2028` *LINE SEPARATOR*</sup> и абзацев <sup>**`U+2029`** *PARAGRAPH SEPARATOR*</sup>.
Строки *ECMAScript* до десятой версии  — нет.

Если в *Edge* вызвать `eval()` со строкой `"\u2029"`,
он ведёт себя так, словно мы сделали перенос строки — прямо посреди кода.

![](https://habrastorage.org/webt/vc/yg/-v/vcyg-vy7larz8y4kswiqinjhfh4.png)

C *ES*10 строками — всё в порядке:

![](https://habrastorage.org/webt/tc/he/tm/tchetmze4axxtp5wghm7pmz-0xc.png)


 
 


## Доступ к описанию символьной ссылки

<https://tc39.github.io/proposal-Symbol-description/>

Описание символьной ссылки можно косвенно получить методом toString():
```javascript
const symbol_link = Symbol("Symbol description")
String(symbol_link) // "Symbol(Symbol description)"
```

Теперь у символов появилось свойство description, доступное только для чтения:
```javascript
symbol_link.description // Symbol description"
```


 
 

### Доработка прототипного метода `.toString()`

http://tc39.github.io/Function-prototype-toString-revision/

<spoiler title="Цели изменений">
* убрать обратно несовместимое требование:
«Если реализация не может создать строку исходного кода, соответствующую этим критериям, она должна вернуть строку, для которой  eval будет выброшено исключение с ошибкой синтаксиса.»;
* уточнить «функционально эквивалентное» требование;
* стандартизировать строковое представление встроенных функций и хост-объектов;
* уточнить требования к представлению на основе «фактических характеристик» объекта;
* убедиться, что синтаксический анализ строки содержит то же тело функции и список параметров, что и оригинал;
* для функций, определенных с использованием кода ECMAScript, toString должен возвращать фрагмент исходного текста от начала первого токена до конца последнего токена, соответствующего соответствующей грамматической конструкции;
* для встроенных функциональных объектов toStringне должны возвращать ничего, кроме NativeFunction;
* для вызываемых объектов, которые не были определены с использованием кода ECMAScript, toStringнеобходимо вернуть NativeFunction;
* для функций, создаваемых динамически (конструкторы функции или генератора) toString должен синтезировать исходный текст;
* для всех других объектов, toString должен бросить TypeError исключение.
</spoiler>


```javascript
// Пользовательская функция
function () { console.log('My Function!'); }.toString();
// function () { console.log('My Function!'); }

// Метод встроенного объекта объект
Number.parseInt.toString();
// function parseInt() { [native code] }

// Функция с привязкой контекста
function () { }.bind(0).toString();
// function () { [native code] }


// Встроенные вызываемые функциональный объекты
Symbol.toString();
// function Symbol() { [native code] }

// Динамечески создаваемый функциональный объект
Function().toString();
// function anonymous() {}

// Динамечески создаваемый функциональный объект-генератор
function* () { }.toString();
// function* () { }

// .call теперь обязательно ждёт, в качестве аргумента, функцию
Function.prototype.toString.call({});
// Function.prototype.toString requires that 'this' be a Function"
```



----------



## Stage 3

Предложения выдшие из статуса черновика, но ещё не вошедшие в финальную версию стандарта.


 
### Приватные\статические\публичные методы\свойства\атрибуты у классов.

<https://github.com/tc39/proposal-class-fields><br />
<https://github.com/tc39/proposal-private-methods><br />
<https://github.com/tc39/proposal-static-class-features><br />

В некоторых языках есть договорённость называть приватные методы через видимый пробел. Например так:
```php
<?php
class AdultContent {
	private $_age = 0;
	private $_content = '…is dummy example content (•)(•) —3 (.)(.) only for adults…';
	function __construct($age) {
		$this->_age = $age;
	}
	function __get($name) {
		if($name === 'content') {
			return " (age: ".$this->_age.") → ".$this->getContent()."\r\n";
		}
		else {
			return 'without info';
		}
	}
	private function getContent() {
		if($this->_contentIsAllowed()) {
			return $this->_content;
		}
		return 'Sorry. Content not for you.';
	}
	private function _contentIsAllowed() {
		return $this->_age >= 18;
	}
	function __toString() {
		return $this->content;
	}
}
echo "<pre>";

echo strval(new AdultContent(10));
//  (age: 10) →  Sorry. Content not for you

echo strval(new AdultContent(25));
// (age: 25) →  …is dummy example content (•)(•) —3 only for adults…

$ObjectAdultContent = new AdultContent(32);
echo $ObjectAdultContent->content;
// (age: 32) →  …is dummy example content (•)(•) —3 only for adults…
?>

```


Напомню это только договорённость. Ничто не мешает использовать префикс для других целей или не использовать вовсе.

Разработчики спецификации *EcmaScript* пошли дальше и сделали префикс-**октоторп** (**#**, решётка, хеш) частью синтаксиса.

Предыдущий пример на *ES*10 можно переписать так:

```javascript
export default class AdultContent {

  // Приватные атрибуты класса
  #age = 0
  #adult_content = '…is dummy example content (•)(•) —3 (.)(.) only for adults…'

  constructor(age) {
    this.#setAge(age)
  }

  // Статический приватный метод
  static #userIsAdult(age) {
    return age > 18
  }

  // Публичное свойство
  get content () {
    return `(age: ${this.#age}) → ` + this.#allowed_content
  }

  // Приватное свойство
  get #allowed_content() {
      if(AdultContent.userIsAdult(this.age)){
		return this.#adult_content
	}
	else {
		return 'Sorry. Content not for you.'
	}
  }

  // Приватный метод
  #setAge(age) {
	  this.#age = age
  }

  toString () {
    return this.#content
  }
}

const AdultContentForKid = new AdultContent(10)

console.log(String(AdultContentForKid))
// (age: 10) → Sorry. Content not for you.

console.log(AdultContentForKid.content)
// (age: 10) → Sorry. Content not for you.

const AdultContentForAdult = new AdultContent(25)

console.log(String(AdultContentForAdult))
// (age: 25) → …is dummy example content (•)(•) —3 (.)(.) only for adults…

console.log(AdultContentForAdult.content)
// (age: 25) → …is dummy example content (•)(•) —3 (.)(.) only for adults…

```

Пример переусложнён для демонстрации приватных свойств\методов\атрибутов. Но в целом JS радует глаз своей лаконичность по сравнению с PHP вариантом. Никаких тебе private function _…, ни точек с запятой в конце строки и точка вместо «->»  для перехода вглубь объекта. Геттеры именованные. Для динамических имён — прокси-объекты.

Вроде бы мелочи, но после перехода на JS всё меньше желания возвращаться к PHP.

К слову приватные акцессоры доступны только с Babel 7.3.0 и старше. Крайняя версия на npmjs.com — 7.2.2

Ждём в Stage 4!


 
 

## Прототипные методы строк `.trimStart()` и `.trimEnd()`

<https://github.com/tc39/proposal-string-left-right-trim>

По аналогии с методами `.padStart()` и `.padEnd()` обрезают пробельные символы в начале и конце строки соответственно.

```javascript
const one = "      hello and let ";
const two = "us begin.        ";
console.log( one.trimStart() + two.trimEnd() )
// "hello and let us begin."
```


 
 


### Болшие числа с BigInt

https://github.com/tc39/proposal-bigint

<sup>* *уже добавлено в Chrome*</sup>

Максимальное целое число, которое можно безопасно использовать в JavaScript (2⁵³ - 1).
```javascript
console.log(Number.MAX_SAFE_INTEGER)
// 9007199254740991
```

BigInt нужен для использования чисел произвольной точности.
Объявляется этот тип несколькими способами:
```javascript
// используя 'n' постфикс в конце более длинных чисел
console.log( 910000000000000100500n )

// 910000000000000100500n
// напрямую передав в конструктор примитива BigInt() без постфикса
console.log(BigInt( 910000000000000200500 ))
// 910000000000000200500n

// или передав строку в тот-же конструктор
console.log(BigInt( "910000000000000300500" )
// 910000000000000300500n

// ну и так тоже можно
BigInt( "9999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999" );
\\ 9999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999n
```


Это новый примитивный тип:
```javascript
typeof 123;
// → 'number'
typeof 123n;
// → 'bigint'
```

Его можно сравнивать с обычними числами:
```javascript
42n === BigInt(42);
// → true
42n == 42;
// → true
```

Но математические оерации нужно проводить в пределах одного типа:

```javascript
20000000000000n/20n
// 1000000000000n

20000000000000n/20
// Uncaught TypeError: Cannot mix BigInt and other types, use explicit conversions

```


 
 



## Одномерные массивы с `.flat()` и `.flatMap()`

https://github.com/tc39/proposal-flatMap

<sup>* *уже добавлено в Chrome*</sup>

Массив обзавёлся прототипами `.flat()` и `.flatMap()`, которые в целом  похожи на реализации в *lodash*,
но всё же имеют некотые отличия. Необязательный аргумент — устанавливает максимальную глубину обхода дерева.

```javascript
const deep_deep_array = [
  '≥0 — первый уровень',
  [
    '≥1 — второй уровень',
    [
      '≥2 — третий уровень',
      [
        '≥3 — четвёртый уровень',
        [
          '≥4 — пятый уровень'
        ]
      ]
    ]
  ]
]

// 0 — вернёт массив безизменений
deep_deep_array.flat(0)
//  ["≥0 — первый уровень", Array(2)]

// 1 — глубина по умолчанию
deep_deep_array.flat()
//  ["первый уровень", "второй уровень", Array(2)]

deep_deep_array.flat(2)
//  ["первый уровень", "второй уровень", "третий уровень", Array(2)]

deep_deep_array.flat(100500)
// ["первый уровень", "второй уровень", "третий уровень", "четвёртый уровень", "пятый уровень"]
```

.flatMap  не экфивалентно последовательному вызову .flat().map().
```javascript
['Hello', 'World'].flatMap(word => [...word])
// ["H", "e", "l", "l", "o", "W", "o", "r", "l", "d"]
```


 
 




## `globalThis` — новый способ доступа к глобальному контексту

<https://github.com/tc39/proposal-global>

<sup>* *уже добавлено в Chrome*</sup>

Поскольку реализации глобальной области видимости зависят от конкретного движка, раньше приходилось делать что-то вроде этого:

```javascript
var getGlobal = function () {
	if (typeof self !== 'undefined') { return self; }
	if (typeof window !== 'undefined') { return window; }
	if (typeof global !== 'undefined') { return global; }
	throw new Error('unable to locate global object');
};
```

И даже такой вариант не гарантировал, что всё точно будет работать.

`globalThis` — общий для всех платформ способ доступа к глобальной бласти видимости:

```javascript
// Обращение к глобальному конструктору массива
globalThis.Array(1,2,3)
// [1, 2, 3]

// Запись собственных данных в глобальную область видимости
globalThis.myGLobalSettings = {
	it_is_cool: true
}

// Чтение собственных данных из глобальной области видимости
globalThis.myGLobalSettings
//{it_is_cool: true}
```



 
 


## .matchAll() — новый прототипный метод строк

https://github.com/tc39/proposal-string-matchall

<sup>* *уже добавлено в Chrome*</sup>

Работает похоже на метод `.match()` с включенным  флагом `g`, но возвращает итератор:

```javascript
const string_for_searh = 'olololo'

// Вернёт первое вхождение с дополнительной информацией о нём
string_for_searh.match(/o/)
// ["o", index: 0, input: "olololo", groups: undefined]


//Вернёт массив всех вхожденийбез дополнительной информации
string_for_searh.match(/o/g)
// ["o", "o", "o", "o"]

// Вернёт итератор
string_for_searh.matchAll(/o/)
// {_r: /o/g, _s: "olololo"}

// Итератор возвращает каждое последующее вхождение с подробной информацией,
// как если бы мы использовали .match без глобального флага
for(const item of string_for_searh.matchAll(/o/)) {
  console.log(item)
}
// ["o", index: 0, input: "olololo", groups: undefined]
// ["o", index: 2, input: "olololo", groups: undefined]
// ["o", index: 4, input: "olololo", groups: undefined]
// ["o", index: 6, input: "olololo", groups: undefined]
```


Аргумент должен быть регулярным выражением, иначе будет выброшено исключение:
```javascript
'olololo'.matchAll('o')
// Uncaught TypeError: o is not a regexp!
```




 






















## Материалы по теме

https://developer.okta.com/blog/2019/01/22/whats-new-in-es2019



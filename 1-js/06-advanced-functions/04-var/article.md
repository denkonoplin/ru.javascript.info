
# Устаревшая директива "var"

В самой первой главе про [переменные](info:variables), мы ознакомились с 3 способами объявления переменных:

1. `let`
2. `const`
3. `var`

`let` и `const` имеют одинаковое поведение в пределах лексического окружения.

Но `var` это совершенно другая сущность, берущая своё начало с давних времен. Обычно она не используется в современных скриптах, но всё ещё может скрываться в старых.

Если в данный момент вы не работаете с подобными скриптами вы можете пропустить или отложить прочтение данной главы, однако, есть шанс, что вы столкнетесь с этим в будущем. 

На первый взгляд, поведение `var` подобно `let`. Например, объявление переменной:

```js run
function sayHi() {
  var phrase = "Hello"; // local variable, "var" instead of "let"

  alert(phrase); // Hello
}

sayHi();

alert(phrase); // Error, phrase is not defined
```

...Однако отличия всё же есть.

## "var" не ограничивается блочной областью видимости

Область видимости переменных `var` ограничивается либо функцией либо они могут быть глобальными, они доступны за пределами блоков.

Например:

```js
if (true) {
  var test = true; // используем "var" вместо "let"
}

*!*
alert(test); // true, переменная существует за блоком if
*/!*
```

Если мы используем `let test` на строке 2 - в этом случае переменная не будет доступна при вызове `alert`. Но `var` игнорирует блочную область видимости, поэтому мы получили глобальную переменную `test`.

Аналогично для циклов: `var` не может быть блочной или локальной внутри цикла:

```js
for (var i = 0; i < 10; i++) {
  // ...
}

*!*
alert(i); // 10, переменная "i" доступна вне цикла, т.к. является глобальной переменной
*/!*
```

Если блок кода находится внутри функции - в этом случае область видимости `var` ограничена только функцией:

```js
function sayHi() {
  if (true) {
    var phrase = "Hello";
  }

  alert(phrase); // "Hello"
}

sayHi();
alert(phrase); // Error: phrase is not defined
```

Как видим, `var` выходит за пределы блоков `if`, `for` и подобных. Это происходит потому, что на заре развития JavaScript блоки кода не имели лексического окружения. Поэтому, можно сказать, `var` это пережиток прошлого.

## "var" обрабатывается в начале функции

Объявления переменных `var` обрабатываются в начале выполнения функции (или запуска скрипта, в случае, когда переменная является глобальной).

Другими словами, переменные `var` объявляются в начале функции, вне зависимости от того, в каком месте функции реально находится её объявление (при условии, что оно не находится во вложенной функции).

Т.е. этот код:

```js
function sayHi() {
  phrase = "Hello";

  alert(phrase);

*!*
  var phrase;
*/!*
}
```

...Технически является равноценным следующему (объявление переменной `var phrase` перемещено в начало функции):

```js
function sayHi() {
*!*
  var phrase;
*/!*

  phrase = "Hello";

  alert(phrase);
}
```

...И даже коду ниже (запомните, блочная область видимости игнорируется):

```js
function sayHi() {
  phrase = "Hello"; // (*)

  *!*
  if (false) {
    var phrase;
  }
  */!*

  alert(phrase);
}
```

Это поведение называется "hoisting" (всплытие, поднятие), потому что все объявления переменных `var` "всплывают" в самый верх функции.

В примере выше, `if (false)` условие никогда не выполнится. Но это никаким образом не препятствует созданию переменной `var phrase`, которое находится внутри него, поскольку объявления `var` "всплывают" в начало функции. Т.е. в момент присвоения значения `(*)` переменная уже существует.

**Объявления переменных "всплывают", однако присвоения значения - нет."**

Нагляднее будет продемонстрировать следующий пример:

```js run
function sayHi() {
  alert(phrase);  

*!*
  var phrase = "Hello";
*/!*
}

sayHi();
```

Строка `var phrase = "Hello"` состоит из двух действий:

1. Объявление переменной `var`
2. Присвоение значения в переменную `=`.

Объявление переменной обрабатывается в начале выполнения функции ("всплытие"), однако присвоение значения всегда происходит в том строке кода, где оно указано. Т.е. код выполняется по следующему сценарию:

```js run
function sayHi() {
*!*
  var phrase; // объявление переменной срабатывает в начале...
*/!*

  alert(phrase); // undefined

*!*
  phrase = "Hello"; // ...присвоение - в момент, когда исполнится данная строка кода.
*/!*
}

sayHi();
```

Поскольку все объявления переменных `var` обрабатываются в начале функции - мы можем ссылаться на них в любом месте. Однако, переменные имеют значение undefined до строки с присвоением значения.

В обоих примерах выше вызов `alert` происходил без ошибки, потому что переменная `phrase` уже существовала. Но её значение ещё не было присвоено, поэтому мы получали `undefined`.

## Итого

Существует 2 основных отличия `var`:

1. Переменные `var` не имеют блочной области видимости, они ограничены, как минимум, телом функции.
2. Объявления (инициализация) переменных производится в начале исполнения функции.

Есть ещё одно небольшое отличие, относящееся к глобальному объекту, мы рассмотрим его в следующей главе.

Все эти отличия могут послужить причиной появления не совсем очевидных последствий. Переменные с блочной видимостью - отличное решение. Поэтому, много времени назад, переменные `let` и `const` были введены в стандарт и сейчас являются основным способом объявления переменных.

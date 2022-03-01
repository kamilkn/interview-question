# JS

## Замыкание
  https://learn.javascript.ru/closure
  все функции в JavaScript являются замыканиями

  https://jsfiddle.net/d1cuzLwm/


## Event loop, макро и микротаски (macrotask & microtask)
  `event loop`
  Сразу после каждой макрозадачи движок исполняет все задачи из очереди микрозадач перед тем, как выполнить следующую макрозадачу или отобразить изменения на странице, или сделать что-то ещё.
  https://jsfiddle.net/7pb1ngj8/3/
  Порядок
  1. основной код
  2. микро
    - promise (await, then)
    - queueMicrotask(func)
  3. один макро
    - settimeot 0 
  4. снова все микро

  window.requestAnimationFrame()
  нанотаск - https://www.npmjs.com/package/nanotask

## Promise, async+await
### Статические Операции над промисами
`Promise.all`(iterable) - получает на вход массив промисов и параллельно резолвит в массив ответов
резолвится после того как выполнятся все резолвы ЛИБО режектится после провала первого промиса

`Promise.allSettled`(iterable)
так же как и Promise.all, но резолвится в ЛЮБОМ случае при окончании работы всех промисов (резолв/режект)

`Promise.any`(iterable)
ждет резолва первого промиса и его только возвращает

`Promise.race`(iterable)
ждет резолва или режекта первого промиса и его только возвращает

### Статичские оперции возврата промиса
`Promise.reject`(reason)
возвращает режектнутый промис с заданной причиной

`Promise.resolve`(value)
возвращает резолвленный промис с заданным значением

### Методы объектов промисов
Promise.prototype.catch()
Promise.prototype.then()
Promise.prototype.finally()


### Создание и использование промиса
```
const myFirstPromise = new Promise((resolve, reject) => {
  // We call resolve(...) when what we were doing asynchronously was successful, and reject(...) when it failed.
  setTimeout( function() {
    resolve("Success!")  // Yay! Everything went well!
  }, 250)
})

myFirstPromise.then((successMessage) => {
  // successMessage is whatever we passed in the resolve(...) function above.
  // It doesn't have to be a string, but if it is only a succeed message, it probably will be.
  console.log("Yay! " + successMessage)
});
```

## Стек и куча
### Стек 
примитивные значения (string, number, boolean, undefined и null) и ссылки на функции и объекты.
Движок знает, что размер данных не изменится, и поэтому выделяет фиксированный объем памяти для каждого значения (статически)

О ссылках на кучу
Адрес, хранящийся в памяти стека, указывает на объект в памяти кучи. При запросе переменной ссылочного типа адрес памяти сначала читается из стека, а затем по значению в куче определяется адрес.


### Куча
Куча (memory heap) используется для хранения таких данных, как объекты и функции.
В отличие от случая со стеком, движок не знает, какой объем памяти понадобится для тех или иных объектов, а потому выделяет память по мере необходимости (динамически).


## Cборщик мусора
https://learn.javascript.ru/garbage-collection
```
let objectA = {};
let textA = 'garbage text';
objectA.text = textA;
this.objectA = objectA;
delete objectA.text;
```
// objectA останется, textA сотрется GC


```
let objectB = {};
let textB = 'garbage text';
objectB.text = textB;
this.objectB = objectB;
```
оба объекта останутся, так как this ссылается на objectB, objectB на textB

```
let objectA = {};
let objectB = {};
objectA.link = objectB;
objectB.link = objectA;
```
сотрутся, так как на this ничего не выносится
весь «остров» становится недостижимым и будет удалён

### Алгоритм сборки
Основной алгоритм сборки мусора – «алгоритм пометок» (англ. «mark-and-sweep»).

Согласно этому алгоритму, сборщик мусора регулярно выполняет следующие шаги:
1. Сборщик мусора «помечает» (запоминает) все корневые объекты.
2. Затем он идёт по их ссылкам и помечает все найденные объекты.
3. Затем он идёт по ссылкам помеченных объектов и помечает объекты, на которые есть ссылка от них. Все объекты запоминаются, чтобы в будущем не посещать один и тот же объект дважды.
4. …И так далее, пока не будут посещены все ссылки (достижимые от корней).
5. Все непомеченные объекты удаляются.

### Оптимизации
- `Сборка по поколениям` (Generational collection) – объекты делятся на «новые» и «старые». Многие объекты появляются, выполняют свою задачу и быстро умирают, их можно удалять более агрессивно. Те, которые живут достаточно долго, становятся «старыми» и проверяются реже.
- `Инкрементальная сборка` (Incremental collection) – если объектов много, то обход всех ссылок и пометка достижимых объектов может занять значительное время и привести к видимым задержкам выполнения скрипта. Поэтому интерпретатор пытается организовать сборку мусора поэтапно. Этапы выполняются по отдельности один за другим. Это требует дополнительного учёта для отслеживания изменений между этапами, но зато теперь у нас есть много крошечных задержек вместо одной большой.
- `Сборка в свободное время` (Idle-time collection) – чтобы уменьшить возможное влияние на производительность, сборщик мусора старается работать только во время простоя процессора.


## 8 основных типов.
  Примитивные (передача по значению)
  - number для любых чисел: целочисленных или чисел с плавающей точкой; целочисленные значения ограничены диапазоном ±(253-1).
  - bigint для целых чисел произвольной длины.
  - string для строк. Строка может содержать ноль или больше символов, нет отдельного символьного типа.
  - boolean для true/false.

  Объекты (передача по ссылке)
  - object для более сложных структур данных.
    - функции
    - массивы, сеты, мапы
  - symbol для уникальных идентификаторов.

  - null для неизвестных значений – отдельный тип, имеющий одно значение null. если брать через typeOf - то это тип Object (баг js)
  - undefined для неприсвоенных значений – отдельный тип, имеющий одно значение undefined.


## Symbol
Символ (symbol) – примитивный тип данных, использующийся для создания уникальных идентификаторов.

Символы создаются вызовом функции Symbol(), в которую можно передать описание (имя) символа.

Даже если символы имеют одно и то же имя, это – разные символы. Если мы хотим, чтобы одноимённые символы были равны, то следует использовать глобальный реестр: вызов Symbol.for(key) возвращает (или создаёт) глобальный символ с key в качестве имени. Многократные вызовы команды Symbol.for с одним и тем же аргументом возвращают один и тот же символ.

Символы имеют два основных варианта использования:

* «Скрытые» свойства объектов. Если мы хотим добавить свойство в объект, который «принадлежит» другому скрипту или библиотеке, мы можем создать символ и использовать его в качестве ключа. Символьное свойство не появится в for..in, так что оно не будет нечаянно обработано вместе с другими. Также оно не будет модифицировано прямым обращением, так как другой скрипт не знает о нашем символе. Таким образом, свойство будет защищено от случайной перезаписи или использования.
Так что, используя символьные свойства, мы можем спрятать что-то нужное нам, но что другие видеть не должны.

* Существует множество системных символов, используемых внутри JavaScript, доступных как Symbol.*. Мы можем использовать их, чтобы изменять встроенное поведение ряда объектов. Например, в дальнейших главах мы будем использовать Symbol.iterator для итераторов, Symbol.toPrimitive для настройки преобразования объектов в примитивы и так далее.


## Классы обертки для примитивов
  Упаковка (обертка) примитивов - позволяет вызывать методы у примитивов как у классов
  const foo = 1;
  foo.toString();
  `String` для string примитива.
  `Number` для number примитива.
  `Boolean` для Boolean примитива.
  `Symbol` для Symbol примитива.
    https://developer.mozilla.org/ru/docs/Web/JavaScript/Reference/Global_Objects/Symbol

## Структуры данных
https://learn.javascript.ru/weakmap-weakset
weakmap weakset
Set
Map

## 2 вида сравнения
* ===  
  * NaN === Nan // false
* == сравнивает два значения приводя по типам, если они разные
* is
    Object.is(NaN, Nan) // true
    Object.is(0, -0);                 // false
    Object.is(+0, -0);                // false
    Object.is(-0, -0);                // true
    Object.is(0n, -0n);               // true

    // Case 3: NaN
    Object.is(NaN, 0/0);
    Object.is(NaN, Number.NaN)        // true

    Number.isNaN(value) // проверка на NaN


30rps нагрузка?


## Контекст (this)
  function func() {
      console.log(this);
  }
  func();

  apply(массив)  call(переменная)  - заряжает и запускает
  bind - заряжает только. если привязывается несколько конекстов, то идет первый
  под капотом vue (options api)

https://code.yandex-team.ru/a4d5e6e6-1ace-4622-8b3f-4f2f16267b34


## Стрелочные функции
(acc, curr) => {......}

Стрелочные функции - более короткий синтаксис по сравнению с функциональными выражениями и лексически привязаны к значению this (но не привязаны к собственному this, arguments, super, или new.target)

Так как значение this определяется лексикой, вызов стрелочных функций с помощью методов call() или apply() не меняет дело

У анонимной функции есть собственный контекст
В случае же со стрелочной функцией оказывается, что её контекст совпадает с контекстом создавшей её функции

если после function есть имя — функция именованная, во всех остальных случая анонимная.

forEach, then лучше юзать стрелочные

лямбда-функции (также стрелочные)
  map
  reduce, reduceRight
  filter, find
  some, every
  sort

Другие
  split, join
  concat


## Repaint and Reflow
- repaint - occurs when changes are made to an elements skin that changes visibly, but do not affect its layout. Examples of this include
  * outline, visibility, background, or color

- reflow - even more critical to performance because it involves changes that affect the layout of a portion of the page (or the whole page).
  * width, height, font-family, font-size
https://medium.com/swlh/what-the-heck-is-repaint-and-reflow-in-the-browser-b2d0fb980c08
  !!! .createDocumentFragment()

## async, defer
- обычный
загружает и выполняет скрипт, затем дозагружает страницу
1. Скрипты не видят DOM-элементы ниже себя, поэтому к ним нельзя добавить обработчики и т.д.
2. Если вверху страницы объёмный скрипт, он «блокирует» страницу. Пользователи не видят содержимое страницы, пока он не загрузится и не запустится:

- async - никто никого не ждет. загрузка и выполнение параллельно. между собой нет порядка. что быстрее загрузится - то и быстрей запустится
!Динамически загружаемые скрипты по умолчанию ведут себя как «async».

- defer - загрузка в фоне, выполнение после загрузки страницы (дерево DOM готово), но до события DOMContentLoaded
они сохраняют порядок относительно друг друга, как и обычные скрипты.


## СORS и JSONP
  спецификация требует, чтобы браузеры "предпроверяли" запрос (pre-flight), запрашивая поддерживающие методы с сервера с помощью метода HTTP-запроса OPTIONS и затем, поверх "подтверждения" с сервера, отсылали фактический запрос с фактическим методом HTTP-запроса
  * curl -X OPTIONS http://example.org -i

  Access-Control-Allow-Origin: http://api.bob.com
  Access-Control-Allow-Methods: GET, POST, PUT
  Access-Control-Allow-Headers: X-Custom-Header


что происходит если вызвать a.b ?


NodeJS выполняется в консоли или на бэкенде


Similar arrays are not equal.
let a1 = [1]
let b1 = [1]
a1 === b1 // false
Here, we see that although array1 and array2 contain seemingly the same array specifications, they are not equal. This is because the reference to each of the arrays points to a different location in memory.
https://jsfiddle.net/uw01h2j3/16/


var array1 = [1, 2, 3];
var array2 = array1;
console.log(array1 == array2);
array2.pop();
console.log(array1); // [1, 2]
console.log(array2); // [1, 2]



есть доп команды, для работы
- с файлами (require 'fs')
- бд


Увемление от сайтов - тоже service worker



'use strict' - позволяет в рантайме отслеживать некоторые ошибки





FMP (first meaningful paint)
TTI (time to interactive)
Гидрейт (hydrate)


https://jsfiddle.net/9850mwkg/10/
  var user = {
    car: 'lada',
    showName() {
      return this.car;
    }
  }
  var showDataV = user.showName;
  var showDataB = user.showName.bind(user);
  var showDataF = function() {return user.showName()};

  console.log(showDataV()); // undefined
  console.log(showDataF()); // 'lada'
  console.log(showDataB()); // 'lada'
  console.log(showDataV.call(user)); // 'lada'


Как проект сделать хайлод
1. Что нужно сделать для того чтобы разработчики не могли накосячить
2. Что нужно сделать чтобы построить большое приложение



## IIFE (Immediately Invoked Function Expression)
  (function(){
  return 2;
  })(); // it's iife

   (function(name){
  return name;
  })(2); // it's iife too

  let true_iife = (function(){
  return 3;
  })();
  console.log(true_iife); // 3

  let true_iife1_2 = (function(name){
  return name;
  })(2);
  console.log(true_iife_2); // 2

  let false_iife = function(){
  return 3;
  };
  console.log(false_iife); // function(){return 3;}

  function(name){
  return name;
  }(2); // it's not iife, it's error

      // like as:
      function(name){
      return name;
      }
      
      (2)


var x = 7;
console.log(x++) // 7
console.log(x); // 8

var y = 7;
console.log(++x) // 8


console.log(0.1 + 0.2 - 0.2 == 0.1);  // false

console.log(0.1 + 0.2); // 0.30000000000000004
что делать = округление или умножение на 100


## Приоритеты операторов
https://puzzleweb.ru/javascript/6_operators.php 

Убывание приоритета
()
!
++
**
* / %
+ -
>< <=  => 
== != ===
||
=

Сравнивать можно только примитивы, ссылочные нельзя. Всегда будет false


3 > 2 > 1 // false  => (3 > 2) > 1  =>  true > 1


function f1(a, b) {
  console.log(a,b);
}

let f2 = f1.bind(null, 'foo'); - null - контекст, foo - первый аргумент

f2('bar','baz); // foo, bar  - склеиваются аргументы из bind



let name = 'rabbit'
function go() {
    let name = 'Forest';
    console.log(name); 
}

name = 'Alice';
go(); // Forest


## var, let и const
  https://jsfiddle.net/jo6eL54f/
  Кратко
  `var` в пределах функции. можно повторно присваивать и объявлять
  `let` в пределах блока, повторно присваивать можно, повторно объявлять нельзя на том же уровне
  `const` в пределах блока + запрет на повторное присваивания. но само значение может меняться в результате операций

`var`
  поднимаются и инициализируются сразу и равны undefined
  `функциональная`  область видимости

  console.log(foo); //logs →undefined 
  // это не приводит к ошибке, однако значение →undefined;
  //это происходит из-за поднятия
  var foo = “something”; //Инициализация
  console.log(foo); //logs →something

  Как можно упростить оценку интерпретатора JS для кода выше:
  var foo; // Поднятое объявление ‘foo’
  console.log(foo); записывает значение →undefined;
  foo = “something”;
  console.log(foo); //записывает значение →something


`let и const`
  поднимаются но не инициализируются
  поэтому нужно при инициализации сразу указать значение
  `блочная` область видимости


`не указана`
  сразу поднимается вверх и глобальная область видимости


Функции тоже поднимаются (хостятся)!!! Но не функциональные выражения
 

## Деструктуризация переменных
let o = {a:1, b:2, c:3};
let aProp = o.a; // устаревший вариант, больше кода
console.log(aProp);
let {a} = o; // деструктуриазация, более лаконично


## Функции
* Функция первого класса 
* Функции высшего порядка (High order functions - HOF) - это функции, которые работают с другими функциями, либо принимая их в виде параметров, либо возвращая их
  примеры .map, .filter и .reduce

  const withCount = fn => {
    let count = 0;

    return (...args) => {
      console.log("calling with", args);
      console.log(`Call count: ${++count}`);
      const result = fn(...args); // вот признак
      console.log("called with", args, ", returned", result);
      return result;
    };
  };

  const add = (x, y) => x + y;
  const countedAdd = withCount(add);



## Функциональное выражение vs объявление функций
  Функциональные выражения в JavaScript `НЕ поднимаются` (hoisting)
    console.log(notHoisted) // undefined
    //Хотя объявленная переменная и поднимается, определение переменной происходит позже
    notHoisted(); // TypeError: notHoisted is not a function

    var notHoisted = function() {
      console.log('bar');
    };

  Объявленные функции `поднимаются`
    hoisted(); // выводит "foo"
    function hoisted() {
      console.log("foo");
    }

Что раньше объявляется и инициализируется? функции или переменные?
    `Объявление` именованной функции ВЫШЕ переменной.
    `Назначение` переменных ВЫШЕ именованных функции.

you dont know js - книга

## Func() vs new Func()
function Func() {

}
function FuncWithReturn() {
  return 1;
}
const func1 = Func(); // undefined
const func2 = new Func(); // Object

const funcReturn1 = FuncWithReturn(); // 1
const funcReturn2 = new FuncWithReturn(); // Object

## Чистая функция
1. Одинаковый вход => Одинаковый выход
2. Нет побочных эффектов
    Все что не связано с вычислением результата
    - Видоизменение входных параметров
    - console.log
    - HTTP вызовы (AJAX/fetch)
    - Изменение в файловой системе
    - Запросы DOM


https://jsfiddle.net/w8kg7jtn/12/



Falsy
  0 null indefined '' false
  vue - '' не подходит
Truthy
  все остальное



## Null vs undefined
  null - пустое значение
  undefined - не инициализированная переменная


## rest параметеры (остаточные параметры)
function(a, b, ...theArgs) {
  // ...
}

function multiplyTwo(...restArgs) {
    return restArgs.map(el=>el*2);
}
multiplyTwo(1, 2, 3)  // 4, 5, 6


## Каррирование
  function sum(a) {
    return function(b) {
        return a + b;
    }
  }
  sum(3)(4) // 7

## Рекурсия  https://jsfiddle.net/ojab1k5q/2/
  let sum = (a) => {
    return (b) => {
      return (c) => {
        return (c > 0) ? sum(a + b)(c) : a + b;
      }
    }
  }  
  console.log(sum(3)(2)(1)(3)());

  // short
  let sum = (a) => (b) => (c) => (c > 0) ? sum(a + b)(c) : a + b;


## Отладка
- console.log
  - выводит на совсем то в асинхронных операциях
  - если вывести в консоль непримитивный объект и далее мутировать его, то консоль выведет уже мутированный объект
- debugger (во вкладке Source)
  * делаем точки останова (breakpoints)
  * проверяем переменные
  * смотрим callstack
  * выполняем по шагам


WebAssembly


## Модули
  при повторных импортах в конечном bundle повторений не будет
  даже если прописать console.log(123), то увидим это только 1 раз

  экспортированные из модуля непримитивные переменные извне менять можно, и тогда это поменяется во всех местах, где импортируется модуль

Как сделать так чтобы 2 script файла не видели друг друга (были изолированы)?
  - как вариант использовать блоки
  - использовать замыкания


## Прототипное наследование  https://jsfiddle.net/3rh2sfy0/8/
  ```
  function Func(x, y) {
    this.x = x;
    this.y = y;
  }
  let myFunc = new Func();
  ```

  Func.method() - статический метод
  Func.prototype - основной способ доступа к методам
  myFunc.__proto__ - deprecated, сейчас Object.getPrototypeOf/Object.setPrototypeOf
  
  myFunc.__proto__ == Func.prototype // true
  myFunc.__proto__.__proto__  == Func.prototype // true

  ```
  function Foo(){}
  Foo.prototype = {bar: 'baz'};
  f = new Foo();
  console.log(f.bar);
  // logs "baz" because the object f doesn't have an attribute "bar"
  // so it checks the prototype
  f.bar = 'buzz';
  console.log( f.bar ); // logs "buzz" because f has an attribute "bar" set

  z.__proto__ = f;
  ```

  Множественное наследование - https://jsfiddle.net/435msun7/7/

  Прототип используется только для чтения свойств.
  Операции записи/удаления работают напрямую с объектом.

  Свойства-аксессоры – исключение, так как запись в него обрабатывается функцией-сеттером. То есть, это, фактически, вызов функции.

  А что с this?
    Неважно, где находится метод: в объекте или его прототипе. При вызове метода this — всегда объект перед точкой.

  b.b - что будет пытаться сделать?


## for...in vs for...of
  for of
  - `iterable` collections: Array, Map, Set, String, arguments
  - = forEach + continue + break

  for in
  - `enumerable` collections (перечисляемые): ключи массивов
  - на каждом шаге ключ, а не значение

## for vs forEach
for дает возможность остановиться через break


## ES (ECMA Script) Versions
  1st - 1997
  2nd - 1998
  3rd - 1999
  4th (abandoned)
  5th - 2009
  6th – 2015
    - стрелочные функции
    - let и const
    - yield (генераторы)
    - промисы
  7th – 2016
    - **
    - .includes()
  8th – 2017
    - async/await
    - padStart() и padEnd()
    - getOwnPropertyDescriptors
    
  9th – 2018
    - разделение памяти
    - Atomics
    - rest (...)
    - spread (...)
    - finally для promise
    - Асинхронная итерация (как выпендрились в озоне) for await
  10th – 2019
  11th – 2020
    - matchAll
    - import()
    - BigInt
    - Promise.allSettled
    - объект globalThis

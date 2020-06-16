# 📝 Типи ресурсів

Як описано в [документації по ресурсам](assets.html), Parcel представляє кожен вхідний файл в якості `Asset`. Типи ресурсів представлені як класи, успадковують від базового класу `Asset`, і реалізують необхідний інтерфейс для синтаксичного аналізу, аналізу залежностей, перетворення і генерації коду.

Оскільки Parcel обробляє ресурси паралельно з декількох процесорних ядер, перетворення, які можуть виконувати типи ресурсів, обмежені тими, які працюють з одним файлом одночасно. Для перетворень декількох файлів, може використовуватися для користувацький [Пакувальник](packagers.html).

## Інтерфейс ресурсу

```Javascript
const {Asset} = require ('parcel-bundler');

class MyAsset extends Asset {
  type = 'foo'; // встановлюємо основний тип виведення.

  async parse (code) {
    // розбір коду в AST.
    return ast;
  }

  async pretransform () {
    // перетворити до збору залежностей. (Опціонально)
  }

  collectDependencies () {
    // аналіз залежностей.
    this.addDependency ('my-dep');
  }

  async transform () {
    // перетворити після збору залежностей. (Опціонально)
  }

  async generate () {
    // генерація коду, при необхідності ви можете повертати кілька розширень.
    // результати передаються відповідним пакувальникам для генерації готових бандлів.
    return [
      {
        type: 'foo',
        value: 'my stuff here' // основний висновок
      },
      {
        type: 'js',
        value: 'some javascript', // альтернативне виконання для розміщення в JS-бандл, якщо необхідно
        sourceMap
      }
    ];
  }

  async postProcess (generated) {
    // Процес після завершення генерації всього коду
    // Може використовуватися для об'єднання типів ресурсів
  }
}
```

## Реєстрація типу ресурсу

Ви можете зареєструвати свій тип ресурсу, використовуючи метод `addAssetType`. Він приймає розширення файлу для реєстрації і шлях до модуля типу ресурсу. Це шлях, а не фактичний об'єкт, щоб він міг передаватися робочим процесам.

```Javascript
const Bundler = require ('parcel-bundler');

let bundler = new Bundler ( 'input.js');
bundler.addAssetType ('.ext', require.resolve ('./MyAsset'));
```
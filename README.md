# Интеграция october c livewire

# Как использовать livewire в проекте

## Отображение компонентов Livewire

Используйте компонент CMS `livewire` для активации Livewire на странице или макете темы CMS. Например, ваша страница может выглядеть так.

```twig
url = "/test"
layout = "default"

[livewire]
==
<div class="container">
{% livewire 'counter' %}
</div>
```

Далее отобразите компонент Livewire с помощью тега Twig `{% livewire %}`.

```twig
{% livewire 'counter' %}
```

Вы можете передавать переменные в компонент, используя знак равенства (`=`).

```twig
{% livewire 'counter' count=3 %}
```

> **Примечание**: для правильной работы ваш макет CMS должен включать теги-заполнители `{% styles %}` и `{% scripts %}`, как описано в [документации по заполнителям](https://docs.octobercms.com/3.x/markup/tag/placeholder.html#scripts).

## Расположение файлов

По умолчанию классы хранятся в каталоге **app/Livewire**. Однако вы можете зарегистрировать пользовательские классы в плагинах (см. ниже).

Представления по умолчанию хранятся в каталоге **app/views/livewire**. В каталоге представлений поддерживаются следующие синтаксисы шаблонов, определяемые расширением их файлов.

Расширение | Шаблонный движок
--------- | --------------
**.htm** | October Twig
**.blade.php** | Laravel Blade
**.php** | Шаблон PHP

## Регистрация плагина

Чтобы зарегистрировать компоненты Livewire в своих плагинах, используйте переопределенный метод `registerLivewireComponents`. Метод должен возвращать имя класса в качестве ключа и псевдоним компонента в качестве значения.

```php
public function registerLivewireComponents()
{
return [
\October\Demo\Livewire\Todo::class => 'demoTodo'
];
}
```

В приведенном выше примере класс `October\Demo\Livewire\Todo` ссылается на следующие расположения файлов:

- Файл класса: **plugins/october/demo/livewire/Todo.php**
- Файл представления: **plugins/october/demo/views/livewire/todo.htm**.

Класс должен возвращать свой путь представления, переопределяя метод `render`, и возвращает [экземпляр представления](https://docs.octobercms.com/3.x/extend/services/response-view.html) относительно плагина.

```php
namespace October\Demo\Livewire;

use RainLab\Livewire\Classes\LivewireBase;

class Todo extends LivewireBase
{
public function render()
{
return \View::make('october.demo::livewire.todo');
}
}
```

Компонент может быть отображен в любом месте с помощью псевдонима `demoTodo`.

```twig
{% livewire 'demoTodo' %}
```

### Использование в компонентах CMS

Вы можете реализовать Livewire в своих [компонентах CMS](https://docs.octobercms.com/3.x/extend/cms-components.html) с помощью поведения `RainLab\Livewire\Behaviors\LivewireComponent`. Эта реализация гарантирует, что необходимые зависимости будут зарегистрированы при использовании компонента.

```php
class MyComponent extends \Cms\Classes\ComponentBase
{
public $implement = [
\RainLab\Livewire\Behaviors\LivewireComponent::class
];
}
```

Затем визуализируйте компонент с помощью тега `{% livewire %}`.

```twig
{% livewire 'counter' %}
```

В качестве альтернативы вы можете отобразить компонент в PHP с помощью метода `renderLivewire`.

```php
$this->renderLivewire('counter');
```

### Использование в контроллерах бэкенда

Вы можете реализовать Livewire в своих [контроллерах бэкенда](https://docs.octobercms.com/3.x/extend/system/controllers.html) с помощью поведения `RainLab\Livewire\Behaviors\LivewireController`. Эта реализация гарантирует, что необходимые зависимости будут зарегистрированы в контроллере.

```php
class MyController extends \Backend\Classes\Controller
{
public $implement = [
\RainLab\Livewire\Behaviors\LivewireController::class
];
}
```

Затем визуализируйте компонент с помощью метода `makeLivewire`.

```php
<?= $this->makeLivewire('counter') ?>
```

## Пример использования

Здесь мы создадим компонент в каталоге **app** и визуализируем его на странице CMS отдельно. Сначала создайте файл с именем **app/views/livewire/counter.htm** со следующим содержимым.

```twig
<div class="input-group py-3 w-25">
<button wire:click="add" class="btn btn-outline-secondary">
Добавить
</button>
<div class="form-control">
{{ count }}
</div>
<button wire:click="subtract" class="btn btn-outline-secondary">
Вычесть
</button>
</div>
```

Создайте файл с именем **app/Livewire/Counter.php** со следующим содержимым.

```php
<?php namespace App\Livewire;

use RainLab\Livewire\Classes\LivewireBase;

class Counter extends LivewireBase
{
public $count = 1;

public function add()
{
$this->count++;
}

public function subtract()
{
$this->count--;
}
}
```

Этот компонент теперь отображается как **counter** в шаблонах CMS. Имя компонента выводится из имени класса.

```twig
{% livewire 'counter' %}
```

Например, в демо-теме создайте шаблон с именем **test.htm** со следующим содержимым. Затем откройте URL `/test`.

```twig
url = "/test"
layout = "default"

[livewire]
==
<div class="container">
{% livewire 'counter' %}
</div>
```

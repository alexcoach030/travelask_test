# travelask_test

## Project setup
```
npm install
```

### Compiles and hot-reloads for development
```
npm run serve
```

### Compiles and minifies for production
```
npm run build
```

### Lints and fixes files
```
npm run lint
```

### Customize configuration
See [Configuration Reference](https://cli.vuejs.org/config/).

Техническая информация

Допускается реализация с использованием след. стэка технологий:
ECMAScript до 2020 (будет плюсом реализация ES6 классов)
Vue.js (можно Vuex, но необязательно)

Разрешается и поощряется использование готовых компонентов / пакетов (к примеру, vue-slider-component).

Мобильная версия не требуется.


Логика работы фильтров

Фильтр "Страна" - выпадающий список с поиском по странам (список стран формировать на основе данных)
Фильтр "Тип" - кнопки с множественным выбором
Фильтр "Звезды" - группа чекбоксов с множественным выбором
Фильтр "Цена" - слайдер, при наведении на ползунок или его перемещении над ним показывается tooltip с текущим значением

При изменении любого из фильтров список в правой части должен отфильтроваться, в соответствии с установленными значениями фильтров.
По нажатию кнопки "Очистить фильтры" все фильтры должны принять начальное состояние.
Максимальное выводимое количество записей на странице - 6 штук.
Если по установленным условиям фильтров записи не найдены вместо таблицы отображается текст "Записей не найдено".

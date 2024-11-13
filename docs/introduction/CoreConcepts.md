# Основные понятия

Представьте, что [состояние](https://rajdee.gitbooks.io/redux-in-russian/content/docs/Glossary.html#%D1%81%D0%BE%D1%81%D1%82%D0%BE%D1%8F%D0%BD%D0%B8%D0%B5-state) (state) приложения описан в виде простого объекта. Например, состояние приложения todo может выглядеть следующим образом:
```js
{
  todos: [{
    text: 'Съесть вкусняшку',
    completed: true
  }, {
    text: 'Пойти в спортзал',
    completed: false
  }],
  visibilityFilter: 'SHOW_COMPLETED'
}
```

Этот объект похож на "модель", с тем исключением, что в нём нет сеттеров. Это сделано для того, чтобы разные части кода не могли произвольно менять состояние, вызывая тем самым баги, которые сложно воспроизвести.

Чтобы поменять что-то в состоянии нужно отправить [действие или экшен](https://rajdee.gitbooks.io/redux-in-russian/content/docs/Glossary.html#%D1%81%D0%BE%D1%81%D1%82%D0%BE%D1%8F%D0%BD%D0%B8%D0%B5-state) (to dispatch an action). Экшен - это обычный объект JS (заметьте - никакой магии!), который описывает, что изменилось. Вот несколько примеров таких действий: 
```js
{ type: 'ADD_TODO', text: 'Сходить в бассейн' }
{ type: 'TOGGLE_TODO', index: 1 }
{ type: 'SET_VISIBILITY_FILTER', filter: 'SHOW_ALL' }
```

Если сделать так, что каждое изменение описывается экшеном, это позволит нам чётко понимать, что происходит в приложении. Если что-то изменилось - мы знаем, почему так произошло.
Действия это как хлебные крошки, которые помогают отследить, что произошло. Чтобы подружить состояние с действиями, мы пишем специальную функцию, которая называется [редьюсер](https://rajdee.gitbooks.io/redux-in-russian/content/docs/Glossary.html#%D1%80%D0%B5%D0%B4%D1%8E%D1%81%D0%B5%D1%80-reducer) (reducer). 
Опять же - никакой магии! Редьюсер - это всего лишь функция, которая принимает в качестве аргументов состояние и действие и возвращает обновленное состояние приложения.
Писать редьюсеры для больших и сложных приложений было бы сложным занятием, поэтому, мы делаем несколько маленьких функций, которые управляют не всем состоянием в целом, а отдельными его частями:

```js
function visibilityFilter(state = 'SHOW_ALL', action) {
  if (action.type === 'SET_VISIBILITY_FILTER') {
    return action.filter
  } else {
    return state
  }
}

function todos(state = [], action) {
  switch (action.type) {
    case 'ADD_TODO':
      return state.concat([{ text: action.text, completed: false }])
    case 'TOGGLE_TODO':
      return state.map((todo, index) =>
        action.index === index
          ? { text: todo.text, completed: !todo.completed }
          : todo
      )
    default:
      return state
  }
}
```

А затем мы пишем еще один редьюсер, который управляет состоянием в целом, вызывая те два редьюсера, созданных для соответствующих частей общего состояния:
```js
function todoApp(state = {}, action) {
  return {
    todos: todos(state.todos, action),
    visibilityFilter: visibilityFilter(state.visibilityFilter, action)
  }
}
```

Собственно, в этом заключается вся суть Redux. Заметьте, что в примерах выше не использовалось Redux API. В нём есть несколько утилит для работы с описанными выше паттернами, но главная идея в том, что вы описываете, каким образом нужно обновлять состояние в ответ на объекты-экшены. 90% кода, который вы будете писать будет чистым JavaScript без использования Redux, его API или колдовства.

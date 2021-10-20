# Список задач из {{tracker-full-name}}

Блок `not_var{{tasks}}` содержит список задач {{ tracker-name }}, которые входят в очередь или удовлетворяют определенному фильтру.

## Вызов блока {#task-call}

```
{{tasks url="адрес фильтра или очереди"}}
```

Дополнительных параметров у блока `not_var{{tasks}}` нет.

* Чтобы вывести все задачи очереди, в параметре `url` укажите ее [ключ](../../tracker/manager/create-queue.md#key) или ссылку из адресной строки браузера.

* Чтобы вывести [фильтр](../../tracker/user/create-filter.md) задач, в параметре `url` укажите ссылку на фильтр из адресной строки браузера. 

    {% note info %}

    Параметр `url` блока `tasks` не поддерживает символ `"`. Если в ссылке на фильтр из адресной строки встречается такой символ, замените его на `%22`.

    {% endnote %}
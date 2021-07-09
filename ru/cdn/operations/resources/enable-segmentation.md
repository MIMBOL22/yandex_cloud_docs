# Включение сегментации файлов

Чтобы включить [сегментацию](../../concepts/slicing.md) файлов [ресурса](../../concepts/resource.md):

{% list tabs %}

- Консоль управления
  
  1. В [консоли управления]({{ link-console-main }}) выберите каталог, в котором расположен ресурс.

  1. Выберите сервис **{{ cdn-name }}**.

  1. На вкладке **CDN-ресурсы** нажмите на имя необходимого ресурса.

  1. Перейдите на вкладку **Контент**.

  1. Справа вверху нажмите кнопку **Редактировать**.

  1. Включите опцию **Сегментировать**.
    
     {% note info %}

     Файлы больше 10 МБ будут запрашиваться и кешироваться по частям, каждая размером не больше 10 МБ. Чтобы сегментация работала, источники контента должны поддерживать частичные GET-запросы, с заголовком `Range`.

     {% endnote %}

  1. Нажмите кнопку **Сохранить**.

{% endlist %}
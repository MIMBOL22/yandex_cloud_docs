# Устранение ошибки «The limit on maximum number of cores has exceeded»


## Описание проблемы {#issue-description}

При попытке создания виртуальной машины в {{ compute-name }} или группы узлов {{ managed-k8s-name }} появляется сообщение `The limit on maximum number of cores has exceeded»`.

## Решение {#issue-resolution}

Эта ошибка означает, что в вашем облаке превышена квота **«Количество vCPU виртуальных машин»**.

Запросите повышение квоты **«Количество vCPU виртуальных машин»** через [соответствующую форму]({{ link-console-quotas }}).

Возможно, для создания и использования новых виртуальных машин {{ compute-name }} или узлов {{ managed-k8s-name }} вам также понадобится повысить и другие квоты, такие, как:

* **Общий объём HDD-дисков**;
* **Общий объём SSD-дисков**;
* **Общий объём снимков дисков**;
* **Количество дисков**;
* **Общий объём RAM виртуальных машин**;
* **Количество виртуальных машин**;

Обратите внимание на текущий уровень потребления по этим квотам.

## Если проблема осталась {#if-issue-still-persists}

Если вышеописанные действия не помогли решить проблему, [создайте запрос в техническую поддержку]({{ link-console-support }}).
При создании запроса просим указать следующую информацию:

1. Идентификатор операции создания новой виртуальной машины или группы узлов {{ managed-k8s-name }}, которая завершилась с ошибкой.
2. Текст сообщения об ошибке, получаемой при попытке создания новых объектов.
# Остановка и запуск кластера

При необходимости вы можете остановить кластер {{ ES }} и запустить его заново. При остановке кластера все данные в нем сохранятся: они снова станут доступны, когда вы запустите кластер.

Время работы остановленного кластера не тарифицируется: вы продолжаете платить только за объем хранилища в соответствии с [тарифом](../pricing.md#prices-storage).

{% include [pricing-status-warning.md](../../_includes/mdb/pricing-status-warning.md) %}

## Остановить кластер {#stop-cluster}

{% list tabs %}

- Консоль управления

  Чтобы остановить работу всех хостов кластера:
  1. Перейдите на страницу каталога и выберите сервис **{{ mes-name }}**.
  1. Найдите нужный кластер в списке, нажмите значок ![options](../../_assets/horizontal-ellipsis.svg) и выберите пункт **Остановить**.
  1. В открывшемся диалоге подтвердите остановку кластера и нажмите кнопку **Остановить**.

- CLI

    {% include [cli-install](../../_includes/cli-install.md) %}

    {% include [default-catalogue](../../_includes/default-catalogue.md) %}

    Чтобы остановить работу кластера, выполните команду:

    ```bash
    {{ yc-mdb-es }} cluster stop <имя или идентификатор кластера>
    ```

    Имя и идентификатор кластера можно запросить со [списком кластеров в каталоге](cluster-list.md#list-clusters).

- API

  Воспользуйтесь методом API `stop` для остановки работы всех хостов кластера: передайте значение идентификатора требуемого кластера в параметре `clusterId` запроса.

  Чтобы узнать идентификатор кластера, [получите список кластеров в каталоге](cluster-list.md#list-clusters).

{% endlist %}

## Запустить кластер {#start-cluster}

Кластер в статусе **Stopped** можно запустить заново.

{% list tabs %}

- Консоль управления

  Чтобы запустить кластер:
  1. Перейдите на страницу каталога и выберите сервис **{{ mes-name }}**.
  1. Найдите нужный остановленный кластер в списке, нажмите значок ![options](../../_assets/horizontal-ellipsis.svg) и выберите пункт **Запустить**.
  1. В открывшемся диалоге подтвердите запуск кластера нажатием на кнопку **Запустить**.

- CLI

    {% include [cli-install](../../_includes/cli-install.md) %}

    {% include [default-catalogue](../../_includes/default-catalogue.md) %}

    Для запуска остановленного кластера выполните команду:

    ```bash
    {{ yc-mdb-es }} cluster start <имя или идентификатор кластера>
    ```

    Имя и идентификатор кластера можно запросить со [списком кластеров в каталоге](cluster-list.md#list-clusters).

- API

  Чтобы запустить кластер, воспользуйтесь методом API `start`: передайте значение идентификатора требуемого кластера в параметре `clusterId` запроса.

  Чтобы узнать идентификатор кластера, [получите список кластеров в каталоге](cluster-list.md#list-clusters).

{% endlist %}
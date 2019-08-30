# Типы хранилища

Managed Service for ClickHouse позволяет использовать для кластеров баз данных сетевое и локальное хранилища. Сетевое хранилище реализовано на базе сетевых блоков — виртуальных дисков в инфраструктуре Облака. Локальное хранилище организуется на дисках, которые физически размещаются в серверах хостов БД.

{% include [storage-type.md](../../_includes/mdb/storage-type.md) %}

{% note info %}

Локальное хранилище не обеспечивает отказоустойчивости: при отказе локального диска данные теряются безвозвратно. Поэтому при создании нового кластера Managed Service for ClickHouse с использованием локального хранилища автоматически настраивается отказоустойчивая конфигурация из 2 хостов.

{% endnote %}
---
sourcePath: ru/ydb/ydb-docs-core/ru/core/concepts/_includes/secondary_indexes.md
---
# Вторичные индексы

В {{ ydb-short-name }} автоматически создается индекс по первичному ключу, поэтому выборки с условием по первичному ключу всегда выполняются эффективно, затрагивая только требуемые строки. Выборка с условием, наложенным на одну или несколько неключевых колонок, как правило, приводит к полному сканированию таблицы. Для того чтобы такие выборки были эффективными, нужно использовать _вторичные индексы_.

В текущей версии {{ ydb-short-name }} реализованы _синхронные_ и _асинхронные_ глобальные вторичные индексы. Каждый индекс представляет собой скрытую таблицу, которая обновляется:

* для синхронных индексов — транзакционно при изменении основной таблицы;
* для асинхронных индексов — в фоне, получая необходимые изменения из основной таблицы.

Когда пользователь присылает SQL-запрос на вставку, изменение или удаление данных, БД прозрачно для пользователя формирует команды на изменение индексной таблицы. У таблицы может быть несколько вторичных индексов. Индекс может включать несколько колонок, при этом важен порядок указания колонок в индексе. Одна колонка может состоять в нескольких индексах, а также быть одновременно частью первичного ключа и вторичного индекса.

## Синхронный вторичный индекс {#sync}

Синхронный индекс обновляется одновременно с таблицей, которую он индексирует. Такой индекс обеспечивает [строгую согласованность данных](https://en.wikipedia.org/wiki/Consistency_model) и использует для этого механизм [распределенных транзакций](../transactions.md#distributed-tx). Таким образом, если операции чтения и слепой записи в таблицу без индекса можно выполнить без стадии планирования, и за счет этого существенно сократить задержки, то для записи в таблицу с синхронным индексом такая оптимизация невозможна.

## Асинхронный вторичный индекс {#async}

Асинхронный индекс, в отличие от синхронного, не использует механизм распределенных транзакций, а в фоне получает изменения из индексируемой таблицы. Транзакции записи в таблицу с таким индексом выполняются без дополнительных затрат на планирование, за счет снижения гарантий: асинхронный индекс обеспечивает [согласованность данных в конечном счете](https://en.wikipedia.org/wiki/Eventual_consistency), но не строгую согласованность. Использование асинхронного индекса в транзакциях чтения возможно только в режиме [Stale Read Only](transactions.md#modes).

## Покрывающий вторичный индекс {#covering}

Имеется возможность сделать копию содержимого колонок в индекс (covering index), таким образом это исключает необходимость чтения из основной таблицы в операциях чтения по индексу, что заметно снижает задержки. В то же время такая денормализация приводит к увеличенному потреблению дискового пространства и к возможному замедлению операции вставки и обновления из-за необходимости дополнительного копирования данных.

## Онлайн-создание вторичного индекса {#index-add}

В {{ ydb-short-name }} доступно создание вторичного индекса, а также удаление существующего вторичного индекса без остановки обслуживания. Для одной таблицы можно создать только один индекс за раз.

Операция онлайн-создания индекса состоит из следующих шагов:

1. Взятие снапшота таблицы с данными, создание таблицы индекса с пометкой доступности для записи.

    После этого шага пишущие транзакции становятся распределенными, происходит запись в основную таблицу и индекс. Для пользователя индекс пока не доступен.
1. Чтение снапшота основной таблицы и запись в индекс.

    Реализуется «запись в прошлое»: разрешаются ситуации, когда обновления данных на шаге 1 меняют данные, записанные на шаге 2.
1. Публикация результата, удаление снапшота.

    Индекс готов к использованию.

Возможное влияние на пользовательские транзакции:

* Может наблюдаться увеличение задержек из-за того, что транзакции становятся распределенными (при создании синхронного индекса).
* Возможен повышенный фон ошибок `OVERLOADED` из-за того, что во время записи данных активно работает автоматическое разделение шардов индексной таблицы.

Скорость записи данных выбрана таким образом, чтобы минимизировать влияние процесса записи на пользовательские транзакции. Для быстрого завершения операции рекомендуется запускать онлайн-создание вторичного индекса во время минимальной пользовательской нагрузки.

Создание индекса — асинхронная операция. Если после запуска операции произойдет разрыв клиент-серверной связности, то построение индекса будет продолжено. Управлять асинхронной операцией можно через {{ ydb-short-name }} CLI.

## Создание и удаление вторичных индексов {#ddl}

Вторичный индекс может быть:

- Создан при создании таблицы командой YQL [`CREATE TABLE`](../../yql/reference/syntax/create_table.md).
- Добавлен к существующей таблице командой YQL [`ALTER TABLE`](../../yql/reference/syntax/alter_table.md) или командой YDB CLI [`table index add`](../../reference/ydb-cli/commands/secondary_index.md#add)
- Удален у существующей таблицы командой YQL [`ALTER TABLE`](../../yql/reference/syntax/alter_table.md) или командой YDB CLI [`table index drop`](../../reference/ydb-cli/commands/secondary_index.md#drop).
- Удален вместе с таблицей командой YQL [`DROP TABLE`](../../yql/reference/syntax/drop_table.md) или командой YDB CLI `table drop`.

## Назначение и применение вторичных индексов {#best_practices}

О назначении и применении вторичных индексов при разработке приложений смотрите в [рекомендациях](../../best_practices/secondary_indexes.md).
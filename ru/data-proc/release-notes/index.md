# История изменений в {{ dataproc-full-name }}

{% include [Tags](../../_includes/mdb/release-notes-tags.md) %}

## 01.04.2022 {#01.04.2022}

* Доступно создание кластеров на сетевых нереплицируемых дисках размером до 8 ТБ. Устройство нереплицируемых дисков существенно проще стандартных сетевых хранилищ SSD, благодаря чему их производительность выше в несколько раз.
* Добавлена возможность [отмены заданий](operations/jobs-spark#cancel). {{ tag-con }} {{ tag-cli }}
* Добавлен номер сборки в версии [образов {{ dataproc-name }}](../concepts/environment.md).
* Добавлена возможность передачи параметров `packages`, `repositories` и `exclude_packages` для заданий Spark и PySpark. Использование этих параметров позволяет скачивать дополнительные зависимости и пакеты из внешних репозиториев. {{ tag-con }} {{ tag-cli }}

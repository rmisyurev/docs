# Распределенное обучение в {{ ml-platform-name }}

{% note info %}

Возможность распределенного обучения находится на стадии [Preview](../../overview/concepts/launch-stages.md). Доступ предоставляется по запросу в техническую поддержку.

{% endnote %}

{{ taas-name }} дает возможность быстро обучить модель, используя распределенное обучение <q>под ключ</q>. Чтобы использовать {{ taas }} в {{ ml-platform-name }}, не нужны специальные навыки: платформа поможет оптимально масштабировать обучение, написанное для одного GPU, на несколько ускорителей одной или нескольких виртуальных машин. 

{% note info %}

Распределение обучения на несколько ВМ может быть ограничено скоростью сети между отдельными ВМ.

{% endnote %}

## Распределенная доставка данных для обучения {#distributed-dataloader}

Часто при обучении модели на ВМ с GPU возникают простои графического ускорителя из-за того, что подготовка и чтение данных занимает больше времени, чем непосредственно вычисления. {{ taas }} решает эту проблему, загружая и обрабатывая данные для обучения на отдельных, более дешевых ВМ с CPU. Процессы подготовки данных на ВМ с CPU и обучения на ВМ с GPU могут проходить как последовательно, так и параллельно: после подготовки части данных они будут доставлены на ВМ с GPU.

{{ taas }} позволяет эффективно использовать GPU и экономить при обучении ресурсоемких моделей.

Чтобы реализовать распределенную доставку данных, загрузчик данных PyTorch должен удовлетворять условиям:

* Должен быть создан в отдельной ячейке.
* Должен быть зарегистрирован.
* Должен поддерживать неограниченную итерацию по нему (быть <q>бесконечным</q>).

#### См. также

* [{#T}](../operations/taas-run.md)


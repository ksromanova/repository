# Описание проекта



### Краткое описание проекта
Металлургический комбинат "Стальная птица" стремится оптимизировать свои производственные расходы, особенно на этапе обработки стали. Один из ключевых способов снижения затрат энергии заключается в контроле и управлении температурой сплава. Для достижения этой цели требуется разработать модель, которая способна предсказывать температуру сплава в процессе обработки. Заказчик планирует использовать данную модель для имитации технологического процесса и оптимизации параметров обработки стали.

**Описание процесса обработки:**

Процесс обработки стали на комбинате "Стальная птица" включает следующие основные этапы. Сталь подвергается плавке в металлическом ковше, который облицован огнеупорным кирпичом, чтобы выдерживать высокие температуры. Затем сталь подогревается до необходимой температуры с помощью графитовых электродов, установленных на крышке ковша.

Первый этап обработки - десульфурация. На этом этапе из стали выводят серу и корректируют её химический состав с помощью примесей. Затем происходит легирование стали, добавление кусков сплава из бункера для сыпучих материалов или порошковой проволоки через специальный трайб-аппарат.

Перед внесением легирующих добавок специалисты производят химический анализ стали и измеряют её температуру. Затем температуру повышают на несколько минут, после чего добавляют легирующие материалы и проводят процесс перемешивания стали с помощью инертного газа, выполняя повторные измерения температуры. Этот цикл повторяется до достижения нужного химического состава стали и оптимальной температуры плавки.

В конечном этапе расплавленная сталь проходит доводку металла или направляется в машину непрерывной разливки, и из неё выходят готовые продукты в виде заготовок-слябов (англ. slab, «плита»).

**Цель проекта** состоит в разработке точной и надежной модели предсказания температуры сплава на основе данных о химическом составе стали и параметрах производственного процесса. Это позволит "Стальной птице" значительно сократить энергопотребление, оптимизировать производственные расходы и повысить эффективность процесса обработки стали.
### Описание данных

Данные хранятся в базе данных PostgreSQL. Она состоит из нескольких таблиц:
* `steel.data_arc` — данные об электродах;
* `steel.data_bulk` — данные об объёме сыпучих материалов;
* `steel.data_bulk_time` — данные о времени подачи сыпучих материалов;
* `steel.data_gas` — данные о продувке сплава газом;
* `steel.data_temp` — данные об измерениях температуры;
* `steel.data_wire` — данные об объёме проволочных материалов;
* `steel.data_wire_time` — данные о времени подачи проволочных материалов.


Таблица `steel.data_arc`

* `key` — номер партии;
* `BeginHeat` — время начала нагрева;
* `EndHeat` — время окончания нагрева;
* `ActivePower` — значение активной мощности;
* `ReactivePower` — значение реактивной мощности.

Таблица `steel.data_bulk`

* `key` — номер партии;
* `Bulk1` … `Bulk15` — объём подаваемого материала.

Таблица `steel.data_bulk_time`

* `key` — номер партии;
* `Bulk1` … `Bulk15` — время подачи материала.

Таблица `steel.data_gas`

* `key` — номер партии;
* `gas` — объём подаваемого газа.

Таблица `steel.data_temp`

* `key` — номер партии;
* `MesaureTime` — время замера;
* `Temperature` — значение температуры.

Таблица `steel.data_wire`

* `key` — номер партии;
* `Wire1` … `Wire15` — объём подаваемых проволочных материалов.

Таблица `steel.data_wire_time`

* `key` — номер партии;
* `Wire1` … `Wire15` — время подачи проволочных материалов.
**Особенности данных**

1. Во всех файлах столбец `key` содержит номер партии. В таблицах может быть несколько строк с одинаковым значением `key`: они соответствуют разным итерациям обработки.

2. Dо все партии точно добавлялись сыпучие и проволочные материалы, везде была выполнена продувка газом и сплав всегда нагревался. При этом возможную асинхронность в заданном времени разных датчиков не нужно воспринимать как ошибку.
### Этапы выполнения проекта
Шаг 1. Загрузить данные:
- Подключиться к базе данных.

- Задать константу RANDOM_STATE:
  - Установить значение константы RANDOM_STATE, равное дате начала работы над проектом (например, если начало работы 1 сентября 2022 года, то RANDOM_STATE = 10922). Использовать эту константу во всех необходимых местах, включая разделение данных на выборки.

Шаг 2. Провести исследовательский анализ и предобработку данных:
- Проанализировать исходные данные, проверить наличие всех таблиц и соответствие их количества условиям задачи.

- Для таблицы steel.data_arc:
  - Провести исследовательский анализ данных: проверить наличие пропусков и аномалий, изучить распределение признаков.
  - Удалить или заменить аномальные значения, опираясь на нормальные наблюдения.
  - Сгенерировать новые признаки, которые могут быть полезны при обучении, например, длительность нагрева, общая мощность, соотношение активной мощности к реактивной, количество запусков нагрева электродами и другие.

- Для таблицы steel.data_bulk:
  - Провести исследовательский анализ данных: проверить наличие пропусков и аномалий, изучить распределение признаков.
  - Обработать пропуски, которые свидетельствуют о том, что материал не добавляли в партию.

- Для таблицы steel.data_bulk_time:
  - Провести исследовательский анализ данных: проверить наличие пропусков и аномалий, изучить распределение признаков.
  - Проверить данные на адекватность, чтобы убедиться, что подача материала не измеряется сутками. Учесть, что перед вами не стоит задача временных рядов.

- Для таблицы steel.data_gas:
  - Провести исследовательский анализ данных: проверить наличие пропусков и аномалий, изучить распределение признаков.

- Для таблицы steel.data_temp:
  - Провести исследовательский анализ данных: проверить наличие пропусков и аномалий, изучить распределение признаков.
  - Использовать последнюю температуру партии в качестве целевого признака. Начальную температуру партии можно использовать как входной признак, однако любые промежуточные значения температуры следует исключить, чтобы избежать утечки целевого признака.
  - При агрегировании наблюдений использовать только ключи, у которых есть как минимум два наблюдения: первый и последний замеры температуры.
  - Исключить аномальные значения температуры, которые меньше 1500 градусов.

- Для таблицы steel.data_wire:
  - Обработать пропуски, которые свидетельствуют о том, что материал не добавляли в эту партию.

- Для таблицы steel.data_wire_time:
  - Провести исследовательский анализ данных: проверить наличие пропусков и аномалий, изучить распределение признаков.
  - Проверить данные на адекватность, чтобы убедиться, что подача материала не измеряется сутками. Учесть, что перед вами не стоит задача временных рядов.

- Объединить таблицы по ключу, чтобы каждой партии соответствовало одно наблюдение.

- Провести исследовательский анализ данных объединённой таблицы и визуализировать распределение каждого признака, сделать выводы.

- Провести корреляционный анализ.

- Подготовить данные для обучения:
  - Выбрать признаки, которые будут использоваться для обучения, учитывая особенности данных и выбранных моделей.
  - Разделить данные на тренировочную и тестовую выборки с помощью метода test_size = 0.25.
  - Подготовить выборки для обучения, учитывая особенности выбранных моделей.

Шаг 3. Обучить модель:
- Рассмотреть классы моделей, такие как решающее дерево или случайный лес, бустинги и нейронные сети.

- Найти лучшую модель для прогноза последней измеренной температуры и оценить её качество метрикой MAE. Выбрать лучшую модель, исходя из значения метрики на кросс-валидации.

- Подобрать значения как минимум двум гиперпараметрам хотя бы для одной модели. Использовать методы автоматизированного подбора гиперпараметров: GridSearchCV, RandomizedSearchCV, OptunaSearchCV, Optuna и другие.

Шаг 4. Тестирование модели и продемонстрирация её работы
- Проверить качество лучшей модели на тестовой выборке. Значение метрики MAE должно быть менее 6.8.

- Дополнительно оценить $R^2$

- Сравнить результаты лучшей и константной моделей.

- Проанализировать важность основных признаков.

- Для одного из важных признаков провести дополнительное исследование:
построить график зависимости входного и целевого признаков.

Шаг 5. Сделать общий вывод по работе
Общие выводы, предложения способов для дальнейшего улучшения модели и бизнес-рекомендации для заказчика.

**Используемые библиотеки:**

- [catboost](https://catboost.ai/): для обучения градиентного бустинга над решающими деревьями
- [math](https://docs.python.org/3/library/math.html): для выполнения математических операций
- [matplotlib.pyplot](https://matplotlib.org/): для создания графиков и визуализации данных
- [numpy](https://numpy.org/): для работы с массивами и выполнения математических операций
- [pandas](https://pandas.pydata.org/): для работы с данными в виде таблиц и серий
- [pkg_resources](https://setuptools.pypa.io/en/latest/pkg_resources.html): для управления ресурсами, включая версии пакетов Python
- [psycopg2](https://www.psycopg.org/docs/): для взаимодействия с базой данных PostgreSQL из Python
- [scipy.stats.randint](https://docs.scipy.org/doc/scipy/reference/generated/scipy.stats.randint.html): для генерации случайных целых чисел в заданном диапазоне
- [seaborn](https://seaborn.pydata.org/): для создания статистических графиков в Python
- [sklearn.ensemble.RandomForestRegressor](https://scikit-learn.org/stable/modules/generated/sklearn.ensemble.RandomForestRegressor.html): для обучения случайного леса для регрессии в scikit-learn
- [sklearn.metrics.mean_absolute_error](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_absolute_error.html): для вычисления средней абсолютной ошибки в scikit-learn
- [sklearn.metrics.r2_score](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.r2_score.html): для вычисления коэффициента детерминации в scikit-learn
- [sklearn.model_selection.GridSearchCV](https://scikit-learn.org/stable/modules/generated/sklearn.model_selection.GridSearchCV.html) и [sklearn.model_selection.RandomizedSearchCV](https://scikit-learn.org/stable/modules/generated/sklearn.model_selection.RandomizedSearchCV.html): для поиска гиперпараметров модели
- [sklearn.tree.DecisionTreeRegressor](https://scikit-learn.org/stable/modules/generated/sklearn.tree.DecisionTreeRegressor.html): для обучения дерева решений для регрессии в scikit-learn
- [sqlalchemy.create_engine](https://docs.sqlalchemy.org/en/14/core/engines.html): для создания движка базы данных SQLAlchemy
- [sqlalchemy.text](https://docs.sqlalchemy.org/en/14/core/tutorial.html#using-text): для создания текстового выражения SQLAlchemy
- [torch](https://pytorch.org/): для работы с нейронными сетями и тензорами в PyTorch
- [tqdm.trange](https://tqdm.github.io/): для отображения прогресса в циклах с помощью tqdm
- [warnings](https://docs.python.org/3/library/warnings.html): для управления предупреждениями в Python
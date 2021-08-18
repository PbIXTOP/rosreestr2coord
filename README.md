# ROSREESTR TO COORDINATE

![PyPI](https://img.shields.io/pypi/v/rosreestr2coord?style=flat-square)

Инструмент, позволяющий вычислять координаты участка по его кадастровому номеру.
Данные берутся с сайта публичной кадастровой карты [http://pkk.rosreestr.ru/](http://pkk.rosreestr.ru/).

Результат работы скрипта **не соответствует информации в кадастровой выписке**

__Удобно получать данные прямо в QGIS можно с помощью [вот этого инструмента](https://nextgis.ru/blog/ngq-rr/)__ (нужна подписка)

![Plot output](https://raw.githubusercontent.com/rendrom/rosreestr2coord/master/images/plot_example.png)

## Зависимости

- Python3
- [OpenCV](http://opencv.org/)
- Pillow

## Установка

Установка через пакетный менеджер

```bash
pip install rosreestr2coord
```

Установка девелоперской версии

```bash
git clone https://github.com/rendrom/rosreestr2coord
cd ./rosreestr2coord
python setup.py install
```

Установка через виртуальное окружение

```bash
git clone https://github.com/rendrom/rosreestr2coord
cd ./rosreestr2coord
# создание виртуального окружения
python -m venv ./.env
# активация виртуального окружения Linux and MacOS
. ./.env/bin/activate
# активация виртуального окружения для Windows
. ./.env/Scripts/activate
# установка зависимостей
pip install -r requirements.txt
```

## Использование

### Из консоли

```bash
# если установлен через `pip install rosreestr2coord` или `python setup.py install`
rosreestr2coord -c 38:06:144003:4723
rosreestr2coord -l ./cadastral_numbers_list.txt
# запуск из директории проекта
python -m rosreestr2coord -c 38:06:144003:4723
```

Во время выполнения скрипта, в директории откуда был произведен запуск будут созданы файлы и папки.
Поэтому рекомендуется создать отдельную директорию для работы с приложением из консоли.

Опции:

- -h - справка
- -c - кадастровый номер
- -p - путь для промежуточных файлов
- -o - путь для полученного geojson файла
- -e - параметр, определяющий точность аппроксимации. Чем меньше, тем больше точек на полигоне:
  - ![Plot output](https://raw.githubusercontent.com/rendrom/rosreestr2coord/master/images/epsilon.png)
- -t - тип площади:
  - 1 - Участки
  - 2 - Кварталы
  - 3 - Районы
  - 4 - Округа
  - 5 - ОКС
  - 6 - Тер. зоны; 7 - Границы; 9 - ГОК; 10 - ЗОУИТ; 12 - Лес; 13 - Красные линии; 15 - СРЗУ; 16 - ОЭЗ 16
- -l - пакетная загрузка из списка в текстовом файле ( `rosreestr2coord -l list_example.txt` )
- -d - визуализация распознанных точек (для режима --code)
- -r - не использовать кэширование
- -P - загрузка через прокси
- -C - экспортировать только центры участков
- -v - показать версию

### Программно

```python
from rosreestr2coord import Area

area = Area("38:06:144003:4723")
# аргументы
#   code='' - кадастровый номер участка
#   area_type=1 - тип площади
#   epsilon=5 - точность аппроксимации
#   media_path='' - путь для временных файлов
#   with_log=True - логирование
#   coord_out='EPSG:4326' - или EPSG:3857 (будет удалена в последующих версиях)
#   center_only=False - экспорт координат центров участка
#   with_proxy=False - запросы через прокси
#   use_cache=True - использовать кэширование запросов
area.to_geojson()
area.to_geojson_poly()
area.get_coord() # [[[area1_xy], [hole1_xy], [hole2_xy]], [[area2_xyl]]]
area.get_attrs()
```

## Журнал

- 20.07.2021 - **v.4.0.12** - **v.4.0.14**
  - [Fix "Invalid 'layerDefs' is specified" error](https://github.com/rendrom/rosreestr2coord/pull/45) by [sergeybarkov](https://github.com/sergeybarkov)
  - Обработка ошибок загрузки данных
  - Декодирование ответа в utf-8 by [jonimnim](https://github.com/jonimnim)
- 30.01.2021 - **v.4.0.11**
- 09.07.2020 - **v.4.0.10**
  - Исправлен экспорт в KML [#36](https://github.com/rendrom/rosreestr2coord/issues/36).
  - Добавлена опция консоли `-v` для вывода текущей версии библиотеки
- 28.05.2020 - **v.4.0.9**
  - Исправлена проблема с путями в Windows [#34](https://github.com/rendrom/rosreestr2coord/issues/30).
- 14.05.2020 - **v.4.0.8** by [alexandervlpl](https://github.com/alexandervlpl)
  - Оптимизировать склеивание тайлов [#30](https://github.com/rendrom/rosreestr2coord/issues/30).
- 12.05.2020 - **v.4.0.7**
  - Исправление экспорта мультиполигональной геометрии.
  - Экспорт в KML [#31](https://github.com/rendrom/rosreestr2coord/issues/31).
- 10.05.2020 - **v.4.0.6**
  - Обработка запросов с ошибоками, исправление кэширования.
- 01.05.2020 - **v.4.0.5**
  - Загрузка ЗОУИТ (-t 10).
  - Добавлен файл привязки к получаемому растру.
- 21.04.2020 - **v.4.0.4** by [magican](https://github.com/magican)
  - небольшой рефакторинг - подготовка к type и pep тестам.
  - добавлен poetry.
  - добавлен Makefile.
  - добавлены тесты (pytest).
- 08.04.2020 - **v.4.0.0**
  - Координаты всегда в WGS84.
  - Всегда добавляются атрибуты.
  - Каталог заменен на кэширование запростов.
  - Из проекта удалён GUI (будет переписан на вебсервер).
- 18.03.2020 - Обеспечение совместимости с новой версией api [публичной кадастровой карты](https://pkk.rosreestr.ru/).
- 28.10.2019 - Перевод на Python3 by [botanegg](https://github.com/botanegg).
- 16.10.2019 - Исправление функции загрузки данных с росреестра by [botanegg](https://github.com/botanegg).
- 11.09.2018 - Исправление ошибки формирование полигональной геометрии при экспорте в GEOJSON [#8](https://github.com/rendrom/rosreestr2coord/issues/8) by [denny123](https://github.com/denny123).
- 12.03.2018 - Исправление функции завершения выполнения операций в консоли при нажатии на Ctrl+C.
- 05.03.2018 - Добавлена возможность загрузки через прокси [#7](https://github.com/rendrom/rosreestr2coord/issues/5) by [Niakr1s](https://github.com/Niakr1s).
- 09.03.2017 - Добавлена поддержка пользовательского интерфейса с интерактивной картой.
- 17.10.2016 - Увеличена точность вычисления контуров участков.
- 14.10.2016 - Обработка участков с несколькими полигонами.
- 06.10.2016 - Осуществление экспорта таблиц в формате csv.
- 05.10.2016 - Пакетная загрузка участков по списку кадастровых номеров из файла, перевод координат в WGS84.
- 03.10.2016 - Добавлена возможность выбора типа площади.
- 05.09.2016 - Изменен формат записи координат, добавлена возможность хранить мультиполигональную геометрию.
- 23.05.2016 - В тестовом режиме работает восстановление полигонов с отверстиями по PNG.
- 21.05.2016 - Были внесены изменения, чтобы вернуть работу с распознаванием точек по PNG. Упала точность, пропала способность рисовать полигоны и выделять отверстия.
- 21.05.2016 - На публичных кадастровых картах заблокировали SVG и внесли ещё некоторые изменения в работу сервисов. В связи с этим перестало работать приложение.

## Разработка

[INSTALL.md](https://github.com/rendrom/rosreestr2coord/edit/master/README.md)

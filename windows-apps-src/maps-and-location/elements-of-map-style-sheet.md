---
author: normesta
description: "Записи и свойства таблицы стилей карты"
MSHAttr: PreferredLib:/library/windows/apps
Search.Product: eADQiWindows 10XVcnh
title: "Справка о таблицах стилей карты"
ms.author: normesta
ms.date: 03/16/2017
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: "windows 10, uwp, карты, таблица стилей карты"
ms.openlocfilehash: 9e2605917027d7be96ac86421e83082aa5f368f9
ms.sourcegitcommit: 23cda44f10059bcaef38ae73fd1d7c8b8330c95e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/19/2017
---
# <a name="map-style-sheet-reference"></a>Справка о таблицах стилей карты

Можно создать таблицы стилей карты с помощью нотации объектов JavaScript (JSON).

Например, можно использовать указанный ниже JSON, чтобы области воды отображались красным цветом, метки воды — зеленым, а области земли — синим:

```json
    {"version":"1.*",
        "settings":{"landColor":"#0000FF"},
        "elements":{"water":{"fillColor":"#FF0000", "labelColor":"#00FF00"}}
    }
```
JSON можно также использовать, чтобы удалить все метки и точки с карты.

```json

    {"version":"1.*", "elements":{"mapElement":{"labelVisible":false},"point":{"visible":false}}}
```

Иногда для получения окончательного результата значение свойства преобразуется.  Например, fillColor растительности имеет немного различающиеся оттенки в зависимости от типа отображаемого объекта.  Это поведение можно отключить, используя точно указанное значение, с помощью свойства ignoreTransform.

```json
    {"version":"1.*",
        "settings":{"shadedReliefVisible":false},
        "elements":{"vegetation":{"fillColor":{"value":"#999999","ignoreTransform":true}}}
    }
```

В этой статье показаны записи JSON и [свойства](#properties), которые можно использовать для настройки внешнего вида карт.

<span id="entries" />
## <a name="entries"></a>Записи
В этой таблице символы ">" используются для представления уровней в иерархии записей.   

| Название                         | Группа свойств            | Описание    |
|------------------------------|---------------------------|----------------|
| version                      | [Version](#version)       | Версия таблицы стилей, которая будет использоваться. |
| settings                     | [Settings](#settings)     | Параметры, которые применяются ко всей таблице стилей. |
| mapElement                   | [MapElement](#mapelement) | Родительский элемент для всех записей карты. |
| > baseMapElement             | [MapElement](#mapelement) | Родительский элемент для всех записей, которые не относятся к пользователям. |
| >> area                      | [MapElement](#mapelement) | Области использования земли (не следует путать с записями структуры). |
| >>> airport                  | [MapElement](#mapelement) | Области, охватывающие аэропорт. |
| >>> cemetery                 | [MapElement](#mapelement) | Области кладбищ. |
| >>> continent                | [MapElement](#mapelement) | Области всего континента. |
| >>> education                | [MapElement](#mapelement) |  |
| >>> indigenousPeoplesReserve | [MapElement](#mapelement) |  |
| >>> island                   | [MapElement](#mapelement) | Метки в области остров. |
| >>> medical                  | [MapElement](#mapelement) | Области земли, которые используются для медицинских целей (например, территория больницы). |
| >>> military                 | [MapElement](#mapelement) | Области военных баз. |
| >>> nautical                 | [MapElement](#mapelement) | Области земли, которые используются для морских целей. |
| >>> neighborhood             | [MapElement](#mapelement) | Метки в областях, определенных как районы. |
| >>> runway                   | [MapElement](#mapelement) | Области земли, которые покрыты взлетной полосой. |
| >>> sand                     | [MapElement](#mapelement) | Песчаные области, например пляжи. |
| >>> shoppingCenter           | [MapElement](#mapelement) | Области земли, предназначенные для торговых улиц и других торговых центров. |
| >>> stadium                  | [MapElement](#mapelement) | Область стадиона. |
| >>> vegetation               | [MapElement](#mapelement) | Леса, области травы и т.д. |
| >>>> forest                  | [MapElement](#mapelement) | Области земли с лесом. |
| >>>> golfCourse              | [MapElement](#mapelement) |  |
| >>>> park                    | [MapElement](#mapelement) | Области парков. |
| >>>> playingField            | [MapElement](#mapelement) | Спортивные поля, например поле для игры в бейсбол или теннисный корт. |
| >>>> reserve                 | [MapElement](#mapelement) | Области природных заповедников. |
| >> point                     | [PointStyle](#pointstyle) | Все функции точки, которые отображаются с определенным значком. |
| >>> naturalPoint             | [PointStyle](#pointstyle) |  |
| >>>> peak                    | [PointStyle](#pointstyle) | Значки, которые представляют вершины гор. |
| >>>>> volcanicPeak           | [PointStyle](#pointstyle) | Значки, которые представляют вершины вулканов. |
| >>>> waterPoint              | [PointStyle](#pointstyle) | Значки, которые представляют расположение водных точек, например водопадов. |
| >>> pointOfInterest          | [PointStyle](#pointstyle) | Рестораны, больницы, школы, набережные, лыжные курорты и т. д. |
| >>>> business                | [PointStyle](#pointstyle) | Рестораны, больницы, школы и т. д. |
| >>>>> foodPoint              | [PointStyle](#pointstyle) | Рестораны, кафе и т. д. |
| >>> populatedPlace           | [PointStyle](#pointstyle) | Значки, которые представляют размер населенных пунктов (например, город или поселок). |
| >>>> capital                 | [PointStyle](#pointstyle) | Значки, которые представляют столицу населенного пункта. |
| >>>>> adminDistrictCapital   | [PointStyle](#pointstyle) | Значки, которые представляют столицу штата или провинции. |
| >>>>> countryRegionCapital   | [PointStyle](#pointstyle) | Значки, которые представляют столицу страны или региона. |
| >>> roadShield               | [PointStyle](#pointstyle) | Знаки, которые представляют короткое имя дороги. (Например: I-5). Используйте только значения палитры, если для свойства **ImageFamily** записи параметров установлено значение *Palette* |
| >>> roadExit                 | [PointStyle](#pointstyle) | Значки, которые представляют съезды, обычно со скоростной автомагистрали с контролируемым въездом на нее. |
| >>> transit                  | [PointStyle](#pointstyle) | Значки, которые представляют остановки автобусов, поездов, аэропорты и т. д. |
| >> political                 | [BorderedMapElement](#borderedmapelement) | Политические регионы, например страны, регионы и штаты. |
| >>> countryRegion            | [BorderedMapElement](#borderedmapelement) |  |
| >>> adminDistrict            | [BorderedMapElement](#borderedmapelement) | Административные единицы 1, штаты, провинции и т. д. |
| >>> district                 | [BorderedMapElement](#borderedmapelement) | Административные единицы 2, округи и т. д. |
| >> structure                 | [MapElement](#mapelement) | Здания и другие структуры на подобие зданий. |
| >>> building                 | [MapElement](#mapelement) |  |
| >>>> educationBuilding       | [MapElement](#mapelement) |  |
| >>>> medicalBuilding         | [MapElement](#mapelement) |  |
| >>>> transitBuilding         | [MapElement](#mapelement) |  |
| >> transportation            | [TwoToneLineStyle](#twotonelinestyle) | Линии, которые являются частью транспортной сети (например, дороги, поезда и паромы). |
| >>> road                     | [TwoToneLineStyle](#twotonelinestyle) | Линии, которые представляют все дороги. |
| >>>> controlledAccessHighway | [TwoToneLineStyle](#twotonelinestyle) |  |
| >>>>> highSpeedRamp          | [TwoToneLineStyle](#twotonelinestyle) | Линии, которые представляют съезды. Обычно эти съезды отображаются на скоростных автомагистралях с контролируемым въездом. |
| >>>> highway                 | [TwoToneLineStyle](#twotonelinestyle) |  |
| >>>> majorRoad               | [TwoToneLineStyle](#twotonelinestyle) |  |
| >>>> arterialRoad            | [TwoToneLineStyle](#twotonelinestyle) |  |
| >>>> street                  | [TwoToneLineStyle](#twotonelinestyle) |  |
| >>>>> ramp                   | [TwoToneLineStyle](#twotonelinestyle) | Линии, которые представляют въезд на скоростную автомагистраль и съезд с нее. |
| >>>>> unpavedStreet          | [TwoToneLineStyle](#twotonelinestyle) |  |
| >>>> tollRoad                | [TwoToneLineStyle](#twotonelinestyle) | Дороги, за использование которых взимается плата. |
| >>> railway                  | [TwoToneLineStyle](#twotonelinestyle) | Железные дороги. |
| >>> trail                    | [TwoToneLineStyle](#twotonelinestyle) | Дороги для прогулок через парки или пешеходные тропы. |
| >>> waterRoute               | [TwoToneLineStyle](#twotonelinestyle) | Линии маршрута парома. |
| >> water                     | [MapElement](#mapelement) | Все, что выглядит как вода. Сюда относятся океаны и течения. |
| >>> river                    | [MapElement](#mapelement) | Реки, течения и другие водные каналы.  Обратите внимание, что это может быть линия или многоугольник, которые могут связываться с резервуарами воды, которые не являются реками. |
| > routeMapElement            | [MapElement](#mapelement) | Все записи о маршрутах относятся к этой записи. |
| >> routeLine                 | [TwoToneLineStyle](#twotonelinestyle) | Оформление для всех линий маршрута. |
| >>> walkingRoute             | [TwoToneLineStyle](#twotonelinestyle) |  |
| >>> drivingRoute             | [TwoToneLineStyle](#twotonelinestyle) |  |
| > userMapElement             | [MapElement](#mapelement) | Все записи о пользователях относятся к этой записи. |
| >> userPoint                 | [PointStyle](#pointstyle) | Оформление стандартных пользовательских точек. |
| >> userLine                  | [MapElement](#mapelement) | Оформление для стандартных пользовательских линий. |

<span id="properties" />
## <a name="properties"></a>Свойства

В этом разделе описаны свойства, которые можно использовать для каждой записи.

<span id="version" />
### <a name="version-properties"></a>Свойства Version

| Свойство                     | Тип    | Описание                                                                                                           |
|------------------------------|---------|-----------------------------------------------------------------------------------------------------------------------|
| version                      | String  | Версия целевой таблицы стилей. Используется для применимости. "1,0" — по умолчанию, "1,*" для дополнительных обновлений вспомогательных функций. |

<span id="settings" />
### <a name="settings-properties"></a>Определение Settings

| Свойство                     | Тип    | Описание                                                                                                                                                                                                                 |
|------------------------------|---------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| atmosphereVisible            | Логический    | Флажок, который указывает, отображается ли атмосфера в трехмерном элементе управления.                                                                                                                                                     |
| fogColor                     | Color (Цвет)   | Значение цвета ARGB расстояния до тумана, который отображается в трехмерном элементе управления.                                                                                                                                                    |
| glowColor                    | Color (Цвет)   | Значение цвета ARGB, которое можно применить к свечению метки и свечению значка.                                                                                                                                                     |
| imageFamily                  | String (Строка)  | Имя образа, настроенного для использования с этим стилем. Установите значение *Default* (По умолчанию) для знаков, которые используют фиксированные цвета, основанные на реальных знаках. Установите значение *Palette* (Палитра) для знаков, которые используют цвета, настраиваемые с помощью палитры. |
| landColor                    | Color (Цвет)   | Значение цвета ARGB для земли до того, как что-либо нарисовано на этой земле.                                                                                                                                                     |
| logosVisible                 | Логический    | Флажок, который указывает, нужно ли для элементов со свойством **Organization** рисовать соответствующий логотип или следует использовать общий значок.                                                                                         |
| officialColorVisible         | Логический    | Флажок, который указывает, нужно ли рисовать цветом элементы, у которых есть официальное свойство цвета (например, линии транспорта в Китае). Например, отключите это значение для черно-белой карты.                               |
| rasterRegionsVisible         | Логический    | Флажок, который указывает, следует ли рисовать растровые регионы там, где не нужно прорисовывать векторы (например, Япония и Корея).                                                                                                |
| shadedReliefVisible          | Логический    | Флажок, который указывает, следует ли рисовать затенение подъема на карте.                                                                                                                                                  |
| shadedReliefDarkColor        | Color (Цвет)   | Цвет темной части затененного рельефа.  Альфа-канал представляет максимальное значение альфа.                                                                                                                            |
| shadedReliefLightColor       | Color (Цвет)   | Цвет светлой части затененного рельефа.  Альфа-канал представляет максимальное значение альфа.                                                                                                                           |
| spaceColor                   | Color (Цвет)   | Значение цвета ARGB для области вокруг карты.                                                                                                                                                                               |
| useDefaultImageColors        | Логический    | Флажок, который указывает, следует ли использовать оригинальные цвета в SVG вместо поиска цветов изображения в палитре.                                                                                |

<span id="mapelement" />
### <a name="mapelement-properties"></a>Свойства MapElement

| Свойство                     | Тип    | Описание                                                                                                                 |
|------------------------------|---------|-----------------------------------------------------------------------------------------------------------------------------|
| fillColor                    | Color (Цвет)   | Цвет, который используется для заполнения многоугольников, фона значков точек и центра строк, если они разделены. |
| fontFamily                   | String (Строка)  |                                                                                                                             |
| labelColor                   | Color (Цвет)   |                                                                                                                             |
| labelOutlineColor            | Color (Цвет)   |                                                                                                                             |
| labelScale                   | Плавающий   | Сумма, на которую будут изменены стандартные размеры меток. Например, используйте значение *1* для стандартного размера и значение *2* для вдвое большего размера.            |
| labelVisible                 | Логический    |                                                                                                                             |
| strokeColor                  | Color (Цвет)   | Цвет, который используется для контура вокруг многоугольников, контура вокруг значков точек и цвета линий.                   |
| strokeWidthScale             | Плавающий   | Сумма, на которую масштабируется шаг линии. Например, используйте значение *1* для стандартного размера и значение *2* для вдвое большего размера.            |
| visible                      | Логический    |                                                                                                                             |

<span id="borderedmap" />
### <a name="borderedmapelement"></a>BorderedMapElement

Эта группа свойств наследуется от группы свойств [MapElement](#mapelement).

| Свойство                     | Тип    | Описание                                                           |
|------------------------------|---------|-----------------------------------------------------------------------|
| borderOutlineColor           | Color (Цвет)   | Дополнительный цвет или цвет линии границы заполненного многоугольника. |
| borderStrokeColor            | Color (Цвет)   | Основной цвет линии границы заполненного многоугольника.             |
| borderVisible                | Логический    |                                                                       |
| borderWidthScale             | Плавающий   |                                                                       |

<span id="pointstyle" />
### <a name="pointstyle-properties"></a>Свойства PointStyle

Эта группа свойств наследуется от группы свойств [MapElement](#mapelement).

| Свойство                     | Тип    | Описание                                                                                                        |
|------------------------------|---------|--------------------------------------------------------------------------------------------------------------------|
| iconColor                    | Color (Цвет)   | Цвет глифа, показанный посередине значка точки.                                                        |
| scale                        | Плавающий   | Сумма, на которую масштабируется размер точки полностью. Например, используйте значение *1* для стандартного размера и значение *2* для вдвое большего размера. |
| stemColor                    | Color (Цвет)   | Цвет основы, которая выступает в нижней части значка в трехмерном режиме.                                             |
| stemOutlineColor             | Color (Цвет)   | Цвет контура вокруг основы, которая выступает в нижней части значка в трехмерном режиме.                          |

<span id="twotonelinestyle" />
### <a name="twotonelinestyle-properties"></a>Свойства TwoToneLineStyle

Эта группа свойств наследуется от группы свойств [MapElement](#mapelement).

| Свойство                     | Тип    | Описание                                                                                          |
|------------------------------|---------|------------------------------------------------------------------------------------------------------|
| overwriteColor               | Логический    |  Перезаписывает альфа-значение **FillColor** на **StrokeColor** вместо смешивания с ним. |

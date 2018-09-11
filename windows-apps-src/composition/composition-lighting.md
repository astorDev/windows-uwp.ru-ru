---
author: daneuber
title: Освещение композиции
description: API-интерфейсов освещение композиции можно использовать для добавления динамического освещения 3D для вашего приложения.
ms.author: jimwalk
ms.date: 07/16/2018
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: e634b18fffc4f601f6512d6ceeed51efbe9c1886
ms.sourcegitcommit: 72710baeee8c898b5ab77ceb66d884eaa9db4cb8
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/11/2018
ms.locfileid: "3849978"
---
# <a name="using-lights-in-windows-ui"></a>С помощью источников света в Интерфейсе Windows

API-интерфейсы Windows.UI.Composition позволяют создавать в режиме реального времени анимации и эффектов. Освещение композиции позволяет 3D освещения в двухмерных приложениях. В этом обзоре мы будет выполняться функциональные возможности как настройки источников света композиции, определить визуальные элементы для получения каждого источника света и использовать эффекты для определения материалы для содержимого.

> [!NOTE]
> Для чтения, как применить объекты [XamlLight](/uwp/api/windows.ui.xaml.media.xamllight) [CompositionLights](/uwp/api/Windows.UI.Composition.CompositionLight) для освещения объектов XAML UIElement, см. в разделе [XAML освещения](xaml-lighting.md).

Освещение композиции позволяет создавать интересные пользовательского интерфейса, позволяя:

- Преобразование света не зависит от других объектов в сцене для включения впечатляющие сценариев, таких как воспроизведение музыки сцены.
- Возможность связать объект с подсветкой, поэтому они перемещаются вместе независимо от остальной сцены для включения сценариев, таких как [Отображение](/design/style/reveal.md) Fluent выделения.
- Преобразование света и всей сцены, как группу, чтобы создать материалы и глубины.

Освещение композиции поддерживает три основные понятия: **свет**, **целевые объекты**и **SceneLightingEffect**.

## <a name="light"></a>Light

[CompositionLight](https://docs.microsoft.com/uwp/api/windows.ui.composition.compositionlight) позволяет создавать различные источники света и поместить их в координатном пространстве. Эти источники света предназначенные для визуальных элементов, которые вы хотите определить как освещении с света.

### <a name="light-types"></a>Типы света

| Тип | Описание |
| --- | --- |
| [AmbientLight](/uwp/api/windows.ui.composition.ambientlight) | Источник света, излучающий без направления света, который отображается отражаться на все элементы в сцене. |
| [DistantLight](/uwp/api/windows.ui.composition.distantlight) | Бесконечно больших дорогостоящим источника света, излучающий свет в одном направлении. Как солнце. |
| [Класс "PointLight"](/uwp/api/windows.ui.composition.pointlight) | Точечный источник света, излучающий свет во всех направлениях. Например, лампочке. |
| [Вспышки](/uwp/api/windows.ui.composition.spotlight) | Источник света, излучающий внутреннего и внешнего конусов света. Например, фонарика. |

## <a name="targets"></a>Целевые объекты

Когда индикаторы предназначенные для визуального объекта (добавить в список [конечных объектов](/uwp/api/windows.ui.composition.compositionlight.targets) ), визуальный элемент и все дочерние элементы, которые и реагировать на этот источник света. Это может быть что-то же простым, как параметр источника класс "PointLight" в корне дерева и все визуальные элементы ниже реагировать на анимацию точки направления света.

**ExclusionsFromTargets** дает возможность удаления освещения визуального объекта или поддерева визуальных элементов так же, как добавление конечных объектов. Дочерние элементы в дереве корнем, визуальный элемент, который будет исключен есть, не освещаются в результате.

### <a name="sample-targets"></a>Пример (цели)

В следующем примере мы используем CompositionPointLight интересующих XAML TextBlock.

```cs
    _pointLight = _compositor.CreatePointLight();
    _pointLight.Color = Colors.White;
    _pointLight.CoordinateSpace = text; //set up co-ordinate space for offset
    _pointLight.Targets.Add(text); //target XAML TextBlock
```

Добавляя анимацию в смещение точечного источника света, легко достигается shimmering эффекта.

```cs
_pointLight.Offset = new Vector3(-(float)TextBlock.ActualWidth, (float)TextBlock.ActualHeight / 2, (float)TextBlock.FontSize);
```

См. Полный пример [Текста может дрожать при смене](https://github.com/Microsoft/WindowsUIDevLabs/tree/master/SampleGallery/Samples/SDK%2014393/TextShimmer) на Гранки пример WindowUIDevLabs для получения дополнительных сведений.

## <a name="restrictions"></a>Ограничения

Существует несколько факторов, которые необходимо учитывать при определении содержимое, которое будет освещении с CompositionLight.

Концепция | Сведения
--- | ---
**Фоновое освещение** | Добавление-внешнее освещение сцены будут отключены все существующие света.  Элементы, отличных от внешнего освещения не предназначено будут отображаться черным.  Для освещения не целевых светом естественным образом окружающих визуальных элементов, используйте освещения в сочетании с другими источников света.
**Количество света** | Вы можете использовать любые два источники света не внешнее композиции в любом сочетании интересующих пользовательского интерфейса. Внешнее источники света не ограничены; затруднительно, точки и филиалы источники света являются.
**Время существования** | CompositionLight могут возникнуть жизненным циклом условий (пример: сборщик мусора может сбросить light объекта, прежде чем он используется).  Мы рекомендуем отслеживание ссылку на ваш источники света, добавив источники света в качестве элемента для обеспечения управления временем существования приложения.
**Преобразования** | Источники света должен быть размещен в узле выше пользовательского интерфейса, который использует эффектов, как [преобразования перспективы](/design/layout/3-d-perspective-effects.md) в вашем визуальной структуры необходимо нарисовать надлежащим образом.
**Целевые объекты и пространство координат** | CoordinateSpace — это visual пространство, в котором все необходимо задать свойства источников света. CompositionLight.Targets должен находиться в дереве CoordinateSpace.

## <a name="lighting-properties"></a>Свойства освещения

В зависимости от типа света используется свет может иметь свойства затухания и места. Не все типы источников света используют все доступные свойства.

Свойство | Описание
--- | ---
**Color (Цвет)** | [Цвет](/uwp/api/windows.ui.color) источника света. Освещение цвет значения определяются Диффузия [D3D](https://docs.microsoft.com/windows/uwp/graphics-concepts/light-properties) , температура при работе и Specular, определяющий отображаемый цвет. Освещение использует значения RGBA для источников света; цветовой альфа-компонент не используется.
**Direction** | Направления света. Направление, в котором указывает источник света задается относительно его Visual [CoordinateSpace](/uwp/api/windows.ui.composition.distantlight.coordinatespace) .
**Пространство координат** | Каждый визуальный элемент имеет неявный трехмерном пространстве координат. Оси X — слева направо. Оси Y — сверху вниз. Направлении Z — это точка за пределы плоскости. Исходное точки координаты — в верхний левый угол визуального объекта, а единицы — независимых пикселей устройства (DIP). Смещение источника света, определенные в этом координат.
**Внутреннего и внешнего конусов** | Прожекторы излучают свет, состоящий из двух частей: яркий внутренний конус и внешний конус. Композиция позволяет управлять углов внутреннего и внешнего конусов и цвет.
**Смещение** | Смещение относительно координатного пространства Visual источника света.

> [!NOTE]
> Когда же визуальный элемент для проверки нажатия несколько источников света или всякий раз, когда значение цвета света получает достаточно большой, чтобы превышать 1.0, цвет света может измениться из-за закрепления канала цвета источников света.

### <a name="advanced-lighting-properties"></a>Расширенные свойства освещения

Свойство | Описание
--- | ---
**Интенсивность** | Элементы управления яркость света.
**Затухание** | Затухание определяет, как интенсивность света снижается по мере удаления от источника к максимальному расстоянию, заданному в свойстве дальности действия.  Константы, свойства затухания Quadradic и линейной можно использовать.

## <a name="getting-started-with-lighting"></a>Начало работы с освещения

Выполните следующие общие действия для добавления источников света.

- Создайте и поместите индикаторы: необязательно создавать источники и поместить их в указанном системе координат.
- Идентифицировать объекты света: предназначенные для свет в соответствующих визуальных элементов.
- [Необязательно] Определение того, как отдельные объекты реагировать на источники света: использование SceneLightingEffect с EffectBrush для настройки отражения света для отображения SpriteVisual. Значения по умолчанию отражения поддерживают освещение дочерних элементов CoordinateSpace источника света.  Visual, закрашенная с SceneLightingEffect перезаписывает освещения по умолчанию для этого visual.

## <a name="scenelightingeffect"></a>SceneLightingEffect

[SceneLightingEffect](/uwp/api/Windows.UI.Composition.Effects.SceneLightingEffect) используется для изменения применяются к содержимому объекта [SpriteVisual](/uwp/api/Windows.UI.Composition.SpriteVisual) , [CompositionLight](/uwp/api/windows.ui.composition.compositionlight)предназначено освещения по умолчанию.

[SceneLightingEffect](/uwp/api/Windows.UI.Composition.Effects.SceneLightingEffect) часто используется для создания материалов. SceneLightingEffect является результатом используется, если вы хотите добиться более сложной, например включение отражающие свойства на изображение и/или иллюзию глубины предоставляющего обычная карта. SceneLightingEffect предоставляет возможность настройки пользовательского интерфейса с помощью свойства освещения, например зеркальное и сумм. Можно настроить эффекты освещения остальной части конвейера эффектов, позволяя по отдельности наложения и составления реакции различных освещения с содержимым.

> [!NOTE]
> Освещение сцены не создают тени; Это стремясь Двухмерная отрисовка эффекта.  Он не учитывают рассмотрения 3D освещения сценариях, которые включают моделями реального освещения, включая тени.


Свойство | Описание
--- | ---
**Обычная карта** | NormalMaps создания эффекта текстуры, где обычной указания по направлению к света будут ярче и обычных указания сразу же будет темнее. Чтобы добавить NormalMap целевое использование visual [CompositionSurfaceBrush](/uwp/api/Windows.UI.Composition.CompositionSurfaceBrush) с помощью LoadedImageSurface для загрузки ресурсов NormalMap.
**Эмбиент** | Свойства окружения в основном используются для управления общий цвет отражения.
**Отраженный** | Зеркальное отражение создает блики на объекты, делая их отображаются глянцевый. Вы можете контролировать уровень отраженного света, а также уровень светят.  Эти свойства являются рукой для создания эффектов материалов металлическими металлы или глянцевый бумаги.
**Рассеянное** | Рассеянное отражение scatters свет во всех направлениях.
**Модель отражения** | [Модель отражения](/uwp/api/windows.ui.composition.effects.scenelightingeffectreflectancemodel) позволяет выбирать между [Фонга Blinn](https://docs.microsoft.com/visualstudio/designers/how-to-create-a-basic-phong-shader) и физически на основе Фонга Blinn.  Если вы хотите сжимается Блики, предпринимать физически на основе Фонга Blinn.

### <a name="sample-scenelightingeffect"></a>Пример (SceneLightingEffect)

В примере ниже показано, как добавить обычная карта SceneLightingEffect.

```cs
CompositionBrush CreateNormalMapBrush(ICompositionSurface normalMapImage)
{
    var colorSourceEffect = new ColorSourceEffect()
    {
        Color = Colors.White
    };
    var sceneLightingEffect = new SceneLightingEffect()
    {
        NormalMapSource = new CompositionEffectSourceParameter("NormalMap")
    };

    var compositeEffect = new ArithmeticCompositeEffect()
    {
        Source1 = colorSourceEffect,
        Source2 = sceneLightingEffect,
    };

    var factory = _compositor.CreateEffectFactory(sceneLightingEffect);

    var normalMapBrush = _compositor.CreateSurfaceBrush();
    normalMapBrush.Surface = normalMapImage;
    normalMapBrush.Stretch = CompositionStretch.Fill;

    var brush = factory.CreateBrush();
    brush.SetSourceParameter("NormalMap", normalMapBrush);

    return brush;
}
```

## <a name="related-articles"></a>Еще по теме

- [Создание материалов и источников света в визуальном уровне](https://blogs.windows.com/buildingapps/2017/08/04/creating-materials-lights-visual-layer/)
- [Обзор освещения](https://docs.microsoft.com/windows/uwp/graphics-concepts/lighting-overview)
- [API-Интерфейс compositioncapabilities](https://docs.microsoft.com/uwp/api/windows.ui.composition.compositioncapabilities)
- [Математические аспекты освещения](https://docs.microsoft.com/windows/uwp/graphics-concepts/mathematics-of-lighting)
- [SceneLightingEffect](https://docs.microsoft.com/uwp/api/windows.ui.composition.effects.scenelightingeffect)
- [Репозитории WindowsUIDevLabs GitHub](https://github.com/Microsoft/WindowsUIDevLabs)

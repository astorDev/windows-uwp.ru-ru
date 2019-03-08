---
title: Композиция освещения
description: API-интерфейсы композиции освещения можно использовать для добавления динамических трехмерного освещения в приложение.
ms.date: 07/16/2018
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: 733ce75942a05482ade88c1510e788f1cbd515d4
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57602209"
---
# <a name="using-lights-in-windows-ui"></a>С помощью источников света в пользовательском Интерфейсе Windows

Windows.UI.Composition API-интерфейсы позволяют создавать анимации в реальном времени и эффекты. Освещение композиции позволяет трехмерного освещения в 2D приложениях. В этом обзоре мы проведем функциональные возможности того, как настроить источники света композиции, определить визуальные элементы для получения каждого свет и использование эффектов для определения материалов для содержимого.

> [!NOTE]
> Для считывания как [XamlLight](/uwp/api/windows.ui.xaml.media.xamllight) применяются объекты [CompositionLights](/uwp/api/Windows.UI.Composition.CompositionLight) для освещения XAML UIElements, см. в разделе [освещение XAML](xaml-lighting.md).

Освещение композиции позволяет создавать интересные пользовательского интерфейса, позволяя:

- Преобразование света независимо от других объектов в сцене, чтобы включить иммерсивное сценариев, таких как сцены воспроизведения музыки.
- Возможность связать объект с источника света, перемещать их друг с другом независимо от остальной части сцены, чтобы включить такие сценарии, как Fluent [Показать](/windows/uwp/design/style/reveal) выделения.
- Преобразование света и всей сцены как группу для создания материалов и глубина.

Освещение композиции поддерживает три основных понятия: **Свет**, **целевых объектов**, и **SceneLightingEffect**.

## <a name="light"></a>Светлая

[CompositionLight](https://docs.microsoft.com/uwp/api/windows.ui.composition.compositionlight) позволяет создавать различные источники света и помещать их в пространстве координат. Эти индикаторы целевой визуальных элементов, которые вы хотите определить как освещены.

### <a name="light-types"></a>Типы света

| Тип | Описание |
| --- | --- |
| [AmbientLight](/uwp/api/windows.ui.composition.ambientlight) | Источник света, выдает Ненаправленные свет, который отображается отражаться на все, что в сцене. |
| [DistantLight](/uwp/api/windows.ui.composition.distantlight) | Бесконечно больших удаленному источнику света, выпустившей свет в одном направлении. Как и sun. |
| [PointLight](/uwp/api/windows.ui.composition.pointlight) | Точка источника света, который выдает свет во всех направлениях. Например лампочки. |
| [SpotLight](/uwp/api/windows.ui.composition.spotlight) | Источник света, выдает конусы внутреннего и внешнего источника света. Как и фонарик. |

## <a name="targets"></a>Целевые объекты

Если индикаторы целевой визуальный элемент (Добавить [целевых объектов](/uwp/api/windows.ui.composition.compositionlight.targets) списка), визуального элемента и всех его потомков поддерживают и отвечать на этот источник света. Это может быть что-то простое, как параметр источника PointLight в корне дерева и все визуальные элементы ниже реагировать на них анимации точки направления источников света.

**ExclusionsFromTargets** дает возможность удалить освещения визуального элемента или поддерева визуальных элементов так же, как добавить целевые объекты. В результате дочерних элементов в дерево, корнем которого является визуальный элемент, который исключен не включаются.

### <a name="sample-targets"></a>Пример (целевые объекты)

В приведенном ниже примере мы используем CompositionPointLight целевой блок текста XAML.

```cs
    _pointLight = _compositor.CreatePointLight();
    _pointLight.Color = Colors.White;
    _pointLight.CoordinateSpace = text; //set up co-ordinate space for offset
    _pointLight.Targets.Add(text); //target XAML TextBlock
```

Добавление анимации в смещении Точечный свет, эффект shimmering легко реализовать.

```cs
_pointLight.Offset = new Vector3(-(float)TextBlock.ActualWidth, (float)TextBlock.ActualHeight / 2, (float)TextBlock.FontSize);
```

См. в полном [Пишущая машинка текст](https://github.com/Microsoft/WindowsUIDevLabs/tree/master/SampleGallery/Samples/SDK%2014393/TextShimmer) пример в совместимых образец WindowUIDevLabs для получения дополнительных сведений.

## <a name="restrictions"></a>Ограничения

Существует несколько факторов, которые следует учитывать при определении содержимое, которое будет гореть CompositionLight.

Концепция | Подробности
--- | ---
**Внешнее освещение** | Добавление не внешними свет в сцену будут отключены все существующий свет.  Элементы, не распространяется не внешними свет будут отображаться черным.  Для включения окружающей визуальных элементов, которым не назначены света естественным образом, используйте внешнее освещение в сочетании с другими источниками света.
**Число источников света** | Вы можете использовать любые два индикаторы не внешними композиции в любой комбинации для пользовательского интерфейса. Индикаторы окружения не ограничено; именованной, точки и удаленных источников света являются.
**время существования** | CompositionLight могут возникнуть условия времени существования (пример: сборщик мусора может перезапуститься объект light, до его использования).  Мы рекомендуем сохранении ссылки на источники света, добавив источники света в качестве члена для управления временем существования приложения.
**Преобразует** | Индикаторы должны быть расположены в узле выше пользовательский Интерфейс, который использует такие эффекты, как [преобразования перспективы](/windows/uwp/design/layout/3-d-perspective-effects) в структуре visual рисуется должным образом.
**Целевые объекты и систему координат** | CoordinateSpace является видимого пространства, в котором все необходимо задать свойства источников света. CompositionLight.Targets должно быть в пределах дерева CoordinateSpace.

## <a name="lighting-properties"></a>Свойства освещения

В зависимости от типа источника света, которые используются свет может иметь свойства затухания и пространства. Не все типы источников света используют все доступные свойства.

Свойство | Описание
--- | ---
**Цвет** | [Цвет](/uwp/api/windows.ui.color) источника света. Значения цвета освещения определяются [D3D](https://docs.microsoft.com/windows/uwp/graphics-concepts/light-properties) Диффузия Ambient и отражающий, определяющий цвет Исходящие. Освещение использует значения RGBA для света; цвет альфа-компонент не используется.
**Направление** | Направление света. Направление, в котором указывает источник света указывается относительно его [CoordinateSpace](/uwp/api/windows.ui.composition.distantlight.coordinatespace) Visual.
**Координатное пространство** | Каждый визуальный элемент имеет неявное трехмерном пространстве координат. Направление X — слева направо. Оси Y — сверху вниз. Направление Z — это точка из плоскости. Исходное точки координаты является верхнего левого угла визуального элемента, а единицы устройства независимых пикселях (DIP). Смещение источник света, определенные в этом координат.
**Внутренний и внешний конусы** | Прожекторы излучают свет, состоящий из двух частей: яркий внутренний конус и внешний конус. Композиция позволяет контролировать углов конуса должны быть внутренними и внешними и цвет.
**Смещение** | Смещение источника света относительно координатного пространства Visual.

> [!NOTE]
> Когда несколько источников света достигнута тот же визуальный или всякий раз, когда значение цвета света возвращает значение, превышающее 1.0, цвет источника света может измениться из-за фиксацией канала цвета источников света.

### <a name="advanced-lighting-properties"></a>Дополнительные свойства освещения

Свойство | Описание
--- | ---
**Интенсивность** | Управляет яркость источника света.
**Ослабление** | Затухание определяет, как интенсивность света снижается по мере удаления от источника к максимальному расстоянию, заданному в свойстве дальности действия.  Константы, свойства ослабления Quadradic и линейный может использоваться.

## <a name="getting-started-with-lighting"></a>Приступая к работе с освещением

Выполните следующие шаги, чтобы добавить источники света:

- Создайте и поместите индикаторы: Создание источников света и помещать их в указанный пространстве координат.
- Определение объектов для освещения: Целевой объект света в соответствующие визуальные элементы.
- [Необязательно] Определить как отдельные объекты реагировать на них источники света: SceneLightingEffect с EffectBrush можно используйте для настройки отражение света, для отображения SpriteVisual. Значения по умолчанию отражения поддерживает освещения дочерних элементов данного CoordinateSpace источника света.  Визуальный элемент закрашивается с SceneLightingEffect перезаписывает освещения по умолчанию для этого элемента.

## <a name="scenelightingeffect"></a>SceneLightingEffect

[SceneLightingEffect](/uwp/api/Windows.UI.Composition.Effects.SceneLightingEffect) используется для изменения освещения по умолчанию, примененная к содержимому из [SpriteVisual](/uwp/api/Windows.UI.Composition.SpriteVisual) мишенью [CompositionLight](/uwp/api/windows.ui.composition.compositionlight).

[SceneLightingEffect](/uwp/api/Windows.UI.Composition.Effects.SceneLightingEffect) часто используется для создания материала. SceneLightingEffect является результатом используется, если для достижения более сложной, такие как включение отражающие свойства на изображении и/или предоставить карту нормалей иллюзии глубины. SceneLightingEffect предоставляет возможность настройки пользовательского интерфейса с помощью свойств как суммы зеркального отражения и диффузного освещения. Можно выполнить дальнейшую настройку эффектов освещения с остальной частью конвейера эффекты, что позволяет по отдельности в blend и compose различным реакции на ваше содержимое.

> [!NOTE]
> Освещение сцены не создает теней; это эффект, посвященные Двухмерная отрисовка.  Он не учитывает сценариев трехмерного освещения рассмотрения, включающих моделей реальных освещения, включая теней.


Свойство | Описание
--- | ---
**Карты нормалей** | NormalMaps создания эффекта текстуры, где обычный указывает сторону света будет ярче и обычный указывает сейчас будет темнее. Добавление NormalMap к целевой использованию visual [CompositionSurfaceBrush](/uwp/api/Windows.UI.Composition.CompositionSurfaceBrush) использование LoadedImageSurface для загрузки ресурса NormalMap.
**Окружения** | Свойства окружения главным образом используются для управления общей отражения цвет.
**Отражающий** | Зеркального отражения создает объекты, которые отображаются на блестящей основные особенности. Вы можете контролировать уровень зеркального отражения, а также уровень успешно работать.  Эти свойства управляют Создание материалов эффекты, например металлов металлическими или глянцевая бумага.
**Диффузный** | Размытая отражения scatters свет во всех направлениях.
**Отражающая способность модели** | [Отражающая способность модели](/uwp/api/windows.ui.composition.effects.scenelightingeffectreflectancemodel) позволяет выбирать между [Фонга Blinn](https://docs.microsoft.com/visualstudio/designers/how-to-create-a-basic-phong-shader) и физически основе Blinn по методу Фонга.  Физически основе Blinn по методу Фонга следует выбирать, если вы хотите сжимается зеркального отражения.

### <a name="sample-scenelightingeffect"></a>Пример (SceneLightingEffect)

В следующем примере показано, как добавить карту нормалей SceneLightingEffect.

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

## <a name="related-articles"></a>Связанные статьи

- [Создание материалов и света на визуальном уровне](https://blogs.windows.com/buildingapps/2017/08/04/creating-materials-lights-visual-layer/)
- [Общие сведения о освещения](https://docs.microsoft.com/windows/uwp/graphics-concepts/lighting-overview)
- [CompositionCapabilities API](https://docs.microsoft.com/uwp/api/windows.ui.composition.compositioncapabilities)
- [Математика освещения](https://docs.microsoft.com/windows/uwp/graphics-concepts/mathematics-of-lighting)
- [SceneLightingEffect](https://docs.microsoft.com/uwp/api/windows.ui.composition.effects.scenelightingeffect)
- [Репозиторий GitHub WindowsUIDevLabs](https://github.com/Microsoft/WindowsUIDevLabs)

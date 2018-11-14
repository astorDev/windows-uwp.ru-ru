---
author: daneuber
title: Тени композиции
description: Тени API-интерфейсы позволяют добавлять динамических настраиваемые теней содержимое пользовательского интерфейса.
ms.author: jimwalk
ms.date: 07/16/2018
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: 2c2f42235e6a74747059723841d3082037b558c7
ms.sourcegitcommit: 4d88adfaf544a3dab05f4660e2f59bbe60311c00
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/13/2018
ms.locfileid: "6468103"
---
# <a name="shadows-in-windows-ui"></a>Тени в Windows пользовательского интерфейса

Класс [DropShadow](/uwp/api/Windows.UI.Composition.DropShadow) предоставляет средства для создания настраиваемой теней, который может быть применен к [SpriteVisual](/uwp/api/windows.ui.composition.spritevisual) или [LayerVisual](/uwp/api/windows.ui.composition.layervisual) (поддерево визуальных элементов). Так как традиционные для объектов в визуальном уровне, все свойства DropShadow можно анимировать при помощи CompositionAnimations.

## <a name="basic-drop-shadow"></a>Основные тени

Для создания базового тени, просто создайте новый DropShadow и сопоставить его с вашей visual. Тени — прямоугольную по умолчанию. Стандартный набор свойств, доступны для настройки внешнего вида вашего теней.

```cs
var basicRectVisual = _compositor.CreateSpriteVisual();
basicRectVisual.Brush = _compositor.CreateColorBrush(Colors.Blue);
basicRectVisual.Offset = new Vector3(100, 100, 20);
basicRectVisual.Size = new Vector2(300, 300);

var basicShadow = _compositor.CreateDropShadow();
basicShadow.BlurRadius = 25f;
basicShadow.Offset = new Vector3(20, 20, 20);

basicRectVisual.Shadow = basicShadow;
```

![Прямоугольный визуальный основные DropShadow](images/rectangular-dropshadow.png)

## <a name="shaping-the-shadow"></a>Формирования тени

Существует несколько способов для определения формы для вашего DropShadow:

- **Используйте значение по умолчанию** — по умолчанию фигуры DropShadow определяется режима «По умолчанию» на CompositionDropShadowSourcePolicy. Для SpriteVisual по умолчанию используется прямоугольные, если не предоставляется маски. Для LayerVisual по умолчанию — наследовать маски с помощью альфа-канал кисти визуального элемента.
- **Настроить маску** — можно задать свойство [маски](/uwp/api/windows.ui.composition.dropshadow.mask) , чтобы определить маску непрозрачности для тени.
- **Укажите, использовать унаследованные маска** — задайте свойство [SourcePolicy](/uwp/api/windows.ui.composition.dropshadow.sourcepolicy) использовать [CompositionDropShadowSourcePolicy](/uwp/api/windows.ui.composition.compositiondropshadowsourcepolicy). InheritFromVisualContent используется маска, созданное из альфа-канал кисти визуального элемента.

## <a name="masking-to-match-your-content"></a>Маскировка в соответствии содержимого

Если вы хотите, чтобы ваш теней в соответствии с содержимым визуального элемента можно использовать кисть визуального элемента для свойства маски теней или задать тени автоматически наследовать маска от содержимого. Если вы используете LayerVisual, тени будет наследовать маски по умолчанию.

```cs
var imageSurface = LoadedImageSurface.StartLoadFromUri(new Uri("ms-appx:///Assets/myImage.png"));
var imageBrush = _compositor.CreateSurfaceBrush(imageSurface);

var imageSpriteVisual = _compositor.CreateSpriteVisual();
imageSpriteVisual.Size = new Vector2(400,400);
imageSpriteVisual.Offset = new Vector3(100, 500, 20);
imageSpriteVisual.Brush = imageBrush;

var shadow = _compositor.CreateDropShadow();
shadow.Mask = imageBrush;
// or use shadow.SourcePolicy = CompositionDropShadowSourcePolicy.InheritFromVisualContent;
shadow.BlurRadius = 25f;
shadow.Offset = new Vector3(20, 20, 20);

imageSpriteVisual.Shadow = shadow;
```

![Подключенные веб-изображения с маской тени](images/ms-brand-web-dropshadow.png)

## <a name="using-an-alternative-mask"></a>С помощью альтернативных маски

В некоторых случаях может потребоваться фигур тени, таким образом, чтобы он не соответствует содержимое визуального элемента. Для достижения этого эффекта, необходимо явно задать свойство маски, с помощью кисти с альфа-канала.

В следующем примере мы загрузим две поверхности — один для визуального содержимого и по одному для маски тени:

```cs
var imageSurface = LoadedImageSurface.StartLoadFromUri(new Uri("ms-appx:///Assets/myImage.png"));
var imageBrush = _compositor.CreateSurfaceBrush(imageSurface);

var circleSurface = LoadedImageSurface.StartLoadFromUri(new Uri("ms-appx:///Assets/myCircleImage.png"));
var customMask = _compositor.CreateSurfaceBrush(circleSurface);

var imageSpriteVisual = _compositor.CreateSpriteVisual();
imageSpriteVisual.Size = new Vector2(400,400);
imageSpriteVisual.Offset = new Vector3(100, 500, 20);
imageSpriteVisual.Brush = imageBrush;

var shadow = _compositor.CreateDropShadow();
shadow.Mask = customMask;
shadow.BlurRadius = 25f;
shadow.Offset = new Vector3(20, 20, 20);

imageSpriteVisual.Shadow = shadow;
```

![Подключенные веб-изображения с помощью круг маскируется тени](images/ms-brand-web-masked-dropshadow.png)

## <a name="animating"></a>Анимация

Так как стандартный в визуальном уровне, DropShadow свойства можно анимировать с помощью анимации композиции. Ниже мы измените код из примера брызги выше анимировать radius размытия тени.

```cs
ScalarKeyFrameAnimation blurAnimation = _compositor.CreateScalarKeyFrameAnimation();
blurAnimation.InsertKeyFrame(0.0f, 25.0f);
blurAnimation.InsertKeyFrame(0.7f, 50.0f);
blurAnimation.InsertKeyFrame(1.0f, 25.0f);
blurAnimation.Duration = TimeSpan.FromSeconds(4);
blurAnimation.IterationBehavior = AnimationIterationBehavior.Forever;
shadow.StartAnimation("BlurRadius", blurAnimation);
```

## <a name="shadows-in-xaml"></a>Тени в XAML

Если вы хотите добавить тени для более сложных элементов framework, для взаимодействия с тенями между XAML и композиции несколькими способами:

1. Используйте [DropShadowPanel](https://github.com/Microsoft/UWPCommunityToolkit/blob/master/Microsoft.Toolkit.Uwp.UI.Controls/DropShadowPanel/DropShadowPanel.Properties.cs) из набора средств сообщества Windows. См. в [документации DropShadowPanel](https://docs.microsoft.com/windows/uwpcommunitytoolkit/controls/DropShadowPanel) сведения о том, как его использовать.
1. Создайте Visual, чтобы использовать в качестве узла тени и привязать к XAML выданного объекта Visual.
1. Используйте элемент управления пользовательских CompositionShadow Галерея образцов композиции [SamplesCommon](https://github.com/Microsoft/WindowsUIDevLabs/tree/master/SamplesCommon/SamplesCommon) . См. в примере для использования.

## <a name="performance"></a>Производительность

Несмотря на то, что визуальный уровень снабжена многих возможностей оптимизации для создания эффектов эффективный и удобным, создающего тени может быть относительно дорого операции в зависимости от того, какие параметры можно задать. Ниже приведены высокого уровня «затраты» для разных типов тени. Обратите внимание, что несмотря на то, что некоторые тени может быть затратной они по-прежнему возможно, стоит использовать слишком часто, в некоторых сценариях.

Характеристики теней| Стоимость
------------- | -------------
"Прямоугольный"    | Низкая
Shadow.Mask      | Высока
CompositionDropShadowSourcePolicy.InheritFromVisualContent | Высока
Статические размытия Radius | Низкая
Анимация размытия Radius | Высока

## <a name="additional-resources"></a>Дополнительные ресурсы

- [DropShadow композиции API](/uwp/api/Windows.UI.Composition.DropShadow)
- [Репозитории WindowsUIDevLabs GitHub](https://github.com/Microsoft/WindowsUIDevLabs)
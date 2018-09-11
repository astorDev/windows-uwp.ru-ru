---
author: daneuber
title: Тени композиции
description: Тени API-интерфейсы позволяют добавлять динамические настраиваемые тени содержимое пользовательского интерфейса.
ms.author: jimwalk
ms.date: 07/16/2018
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: 84e12d6c3e25a18902aaa55011949dd5b5ff97ca
ms.sourcegitcommit: 72710baeee8c898b5ab77ceb66d884eaa9db4cb8
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/11/2018
ms.locfileid: "3849688"
---
# <a name="shadows-in-windows-ui"></a>Тени в окнах пользовательского интерфейса

Класс [DropShadow](/uwp/api/Windows.UI.Composition.DropShadow) предоставляет средства для создания настраиваемой тени, применяемый к [SpriteVisual](/uwp/api/windows.ui.composition.spritevisual) или [LayerVisual](/uwp/api/windows.ui.composition.layervisual) (поддерево визуальных элементов). Как и традиционные для объектов в визуальном уровне, DropShadow все свойства можно анимировать при помощи CompositionAnimations.

## <a name="basic-drop-shadow"></a>Основные тени

Для создания базового тени, просто создайте новый DropShadow и сопоставить его с вашей visual. Тени по умолчанию имеет прямоугольную. Стандартный набор свойств, доступны для настройки внешнего вида вашего теней.

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

## <a name="shaping-the-shadow"></a>Формирование тени

Существует несколько способов для определения формы для вашего DropShadow:

- **Используйте значение по умолчанию** — по умолчанию фигуры DropShadow определяется свойством режима «По умолчанию» на CompositionDropShadowSourcePolicy. Для SpriteVisual по умолчанию используется прямоугольные, если не предоставляется маски. Для LayerVisual по умолчанию — наследовать маски с помощью альфа-канал visual кисти.
- **Задайте маску** — можно задать свойство [маски](/uwp/api/windows.ui.composition.dropshadow.mask) , чтобы определить маску непрозрачности тени.
- **Укажите, использовать унаследованные маска** — свойства [SourcePolicy](/uwp/api/windows.ui.composition.dropshadow.sourcepolicy) использовать [CompositionDropShadowSourcePolicy](/uwp/api/windows.ui.composition.compositiondropshadowsourcepolicy). InheritFromVisualContent используется маска, созданное из альфа-канал visual кисти.

## <a name="masking-to-match-your-content"></a>Маскировка в соответствии содержимого

Если вы хотите, чтобы ваш теней в соответствии с содержимым Visual можно использовать Visual кисти для свойства маски теней или задать тени автоматически наследовать маска от содержимого. Если вы используете LayerVisual, тени будет наследовать маски по умолчанию.

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

В некоторых случаях может потребоваться фигур тени, таким образом, чтобы она не соответствует содержимое визуального элемента. Для достижения этого эффекта, необходимо явно задать свойство маски с помощью кисти с альфа-канала.

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

Как и стандартные в визуальном уровне, DropShadow свойства можно анимировать с помощью анимации композиции. Ниже мы изменить код из примера выше анимировать радиуса размытия тени брызги.

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

Если вы хотите добавить тени для более сложных элементов платформы, для взаимодействия с тенями между XAML и композиции несколькими способами:

1. Используйте [DropShadowPanel](https://github.com/Microsoft/UWPCommunityToolkit/blob/master/Microsoft.Toolkit.Uwp.UI.Controls/DropShadowPanel/DropShadowPanel.Properties.cs) доступны в наборе средств сообщества Windows. См. в [документации DropShadowPanel](https://docs.microsoft.com/windows/uwpcommunitytoolkit/controls/DropShadowPanel) подробные сведения о том, как его использовать.
1. Создание визуальной использовать как узел теней & привязать к XAML выданного объекта Visual.
1. Используйте элемент управления галереи пример композиции [SamplesCommon](https://github.com/Microsoft/WindowsUIDevLabs/tree/master/SamplesCommon/SamplesCommon) пользовательские CompositionShadow. См. пример ниже для использования.

## <a name="performance"></a>Производительность

Несмотря на то, что визуальный уровень снабжена многих возможностей оптимизации для создания эффектов эффективный и удобным, создающего тени может быть относительно дорого операции в зависимости от того, какие параметры можно задать. Ниже приведены высокого уровня «затраты» для разных типов тени. Обратите внимание, что несмотря на то, что некоторые тени может быть затратной они по-прежнему возможно, стоит слишком часто используется в некоторых сценариях.

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
---
title: Shadows композиции
description: Тени API-интерфейсы позволяют добавить динамические настраиваемые shadows содержимого пользовательского интерфейса.
ms.date: 07/16/2018
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: 9541ea1c00d473bc4881a80d8597625592e278f9
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57630839"
---
# <a name="shadows-in-windows-ui"></a>Shadows в пользовательский Интерфейс Windows

[DropShadow](/uwp/api/Windows.UI.Composition.DropShadow) класс предоставляет средства для создания настраиваемых тени, который может быть применен к [SpriteVisual](/uwp/api/windows.ui.composition.spritevisual) или [LayerVisual](/uwp/api/windows.ui.composition.layervisual) (поддерево визуальных элементов). Как и традиционные для объектов на визуальном уровне, все свойства DropShadow могут быть анимированы с помощью CompositionAnimations.

## <a name="basic-drop-shadow"></a>Основные тени

Создание основных тени, создайте новый DropShadow и свяжите ее в визуальный элемент. Тени представляет собой прямоугольник, по умолчанию. Стандартный набор свойств не может настроить внешний вид вашего тени.

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

![Прямоугольная визуализацию с помощью основных DropShadow](images/rectangular-dropshadow.png)

## <a name="shaping-the-shadow"></a>Формирование тени

Определение фигуры для вашей DropShadow несколькими способами:

- **Используйте значение по умолчанию** — по умолчанию форма DropShadow определяется в режиме «Default» на CompositionDropShadowSourcePolicy. Для SpriteVisual значение по умолчанию является прямоугольных, если маска не указан. Для LayerVisual значение по умолчанию — наследовать маски с помощью альфа-канал кисть визуального элемента.
- **Задайте маску** — можно установить [маска](/uwp/api/windows.ui.composition.dropshadow.mask) свойство для определения маски непрозрачности тени.
- **Укажите маску унаследованное** — задайте [SourcePolicy](/uwp/api/windows.ui.composition.dropshadow.sourcepolicy) свойства, используемого [CompositionDropShadowSourcePolicy](/uwp/api/windows.ui.composition.compositiondropshadowsourcepolicy). InheritFromVisualContent использовать маску, созданный из альфа-канал кисть визуального элемента.

## <a name="masking-to-match-your-content"></a>Маски для сопоставления содержимого

Если требуется, чтобы ваши тени для сопоставления содержимого визуального элемента можно использовать кисть визуального элемента для свойства маска теневого, или задать тени, чтобы она автоматически унаследовала маску из содержимого. При использовании LayerVisual тени унаследует маски по умолчанию.

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

![Подключенных веб-образ с маской тени](images/ms-brand-web-dropshadow.png)

## <a name="using-an-alternative-mask"></a>С помощью альтернативных маски

В некоторых случаях может потребоваться тени фигуры, таким образом, что он не соответствует содержимого визуального элемента. Чтобы этого добиться, необходимо явно задать свойство Mask, с помощью кисти с альфа-канала.

В приведенном ниже примере, мы загружаем двух областей — один для визуального содержимого и один для маски тени:

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

![Подключенных веб-образ с кружком маскируются тени](images/ms-brand-web-masked-dropshadow.png)

## <a name="animating"></a>Анимация

Как и стандартные на визуальном уровне, DropShadow свойства могут быть анимированы с помощью композиции анимаций. Ниже мы изменим код из примера выше, чтобы анимировать радиуса размытия тени фрагментами.

```cs
ScalarKeyFrameAnimation blurAnimation = _compositor.CreateScalarKeyFrameAnimation();
blurAnimation.InsertKeyFrame(0.0f, 25.0f);
blurAnimation.InsertKeyFrame(0.7f, 50.0f);
blurAnimation.InsertKeyFrame(1.0f, 25.0f);
blurAnimation.Duration = TimeSpan.FromSeconds(4);
blurAnimation.IterationBehavior = AnimationIterationBehavior.Forever;
shadow.StartAnimation("BlurRadius", blurAnimation);
```

## <a name="shadows-in-xaml"></a>Shadows в XAML

Если вы хотите добавить тень для более сложных элементов framework, существует несколько способов взаимодействия с тенями между XAML и композиции:

1. Используйте [DropShadowPanel](https://github.com/Microsoft/UWPCommunityToolkit/blob/master/Microsoft.Toolkit.Uwp.UI.Controls/DropShadowPanel/DropShadowPanel.Properties.cs) доступны в наборе средств сообщества Windows. См. в разделе [DropShadowPanel документации](https://docs.microsoft.com/windows/uwpcommunitytoolkit/controls/DropShadowPanel) Дополнительные сведения о том, как использовать его.
1. Создайте визуальный элемент, чтобы использовать в качестве узла тени & привязать его к XAML буклет Visual.
1. Использование коллекции примеров композиции [SamplesCommon](https://github.com/Microsoft/WindowsUIDevLabs/tree/master/SamplesCommon/SamplesCommon) пользовательский элемент управления CompositionShadow. См. пример для использования.

## <a name="performance"></a>Производительность

Несмотря на то, что на визуальном уровне предусмотрена многие оптимизации для создания эффектов, эффективной и может использоваться, создание shadows может быть довольно дорогой операцией, в зависимости от того, какие параметры можно задать. Ниже приведены высокой уровня «затраты» для различных типов теней. Обратите внимание, что несмотря на то, что определенные shadows может потреблять много ресурсов они могут по-прежнему можно использовать только в случае необходимости, в некоторых сценариях.

Характеристики тени| Стоимость
------------- | -------------
"Прямоугольный"    | Низкий
Shadow.Mask      | Высокий
CompositionDropShadowSourcePolicy.InheritFromVisualContent | Высокий
Статические размытия Radius | Низкий
Анимация радиуса размытия | Высокий

## <a name="additional-resources"></a>Дополнительные ресурсы

- [Композиция DropShadow API](/uwp/api/Windows.UI.Composition.DropShadow)
- [Репозиторий GitHub WindowsUIDevLabs](https://github.com/Microsoft/WindowsUIDevLabs)
---
ms.assetid: 03dd256f-78c0-e1b1-3d9f-7b3afab29b2f
title: Кисти композиции
description: Кисть заполняет пространство объекта класса Visual своими выводимыми данными. Разные кисти имеют различные типы выводимых данных.
ms.date: 02/08/2017
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: e8c995c5a9513bea44664bcb395cd604ba2668c3
ms.sourcegitcommit: d7613c791107f74b6a3dc12a372d9de916c0454b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/05/2018
ms.locfileid: "8732833"
---
# <a name="composition-brushes"></a>Кисти композиции
Все отображаемые на экране из приложения UWP отображается, так как они нарисованы кистью. Кисти позволяют рисовать объекты пользовательского интерфейса пользователя с содержимым, начиная от простого одноцветных изображения или рисунки цепочку эффекты. В этом разделе рассматриваются понятия рисования с помощью CompositionBrush.

Обратите внимание, что при работе с приложением XAML UWP, можно выбрать, для рисования UIElement с помощью [Кисти XAML](/windows/uwp/design/style/brushes) или [CompositionBrush](https://docs.microsoft.com/uwp/api/Windows.UI.Composition.CompositionBrush). Как правило это проще и рекомендуется выбирать кисти XAML, если ваш сценарий поддерживается кисти XAML. Например анимацию цвета кнопки, изменение заливки текст или фигуру с изображением. С другой стороны Если вы пытаетесь сделать что-то, которое не поддерживается кисти XAML как рисования с анимированной маски или анимированных stretch сетки или цепи эффектов, можно использовать CompositionBrush для рисования UIElement с помощью [ XamlCompositionBrushBase](https://docs.microsoft.com/uwp/api/windows.ui.xaml.media.xamlcompositionbrushbase).

При работе с визуального уровня, CompositionBrush должен использоваться для рисования области [SpriteVisual](https://docs.microsoft.com/uwp/api/Windows.UI.Composition.SpriteVisual).

-   [Необходимые условия](./composition-brushes.md#prerequisites)
-   [Рисование с CompositionBrush](./composition-brushes.md#paint-with-a-compositionbrush)
    -   [Рисования сплошным цветом](./composition-brushes.md#paint-with-a-solid-color)
    -   [Рисование с помощью линейного градиента](./composition-brushes.md#paint-with-a-linear-gradient)
    -   [Рисование с изображением](./composition-brushes.md#paint-with-an-image)
    -   [Рисовать с помощью пользовательской отрисовки](./composition-brushes.md#paint-with-a-custom-drawing)
    -   [Рисование с видео](./composition-brushes.md#paint-with-a-video)
    -   [Рисование с эффект фильтра](./composition-brushes.md#paint-with-a-filter-effect)
    -   [Рисование с CompositionBrush с маску непрозрачности](./composition-brushes.md#paint-with-a-compositionbrush-with-opacity-mask-applied)
    -   [Рисование с CompositionBrush, с помощью NineGrid stretch](./composition-brushes.md#paint-with-a-compositionbrush-using-ninegrid-stretch)
    -   [Рисование с помощью фоновой пикселей](./composition-brushes.md#paint-using-background-pixels)
-   [Объединение CompositionBrushes](./composition-brushes.md#combining-compositionbrushes)
-   [С помощью CompositionBrush и с кисти XAML](./composition-brushes.md#using-a-xaml-brush-vs-compositionbrush)
-   [Еще по теме](./composition-brushes.md#related-topics)

## <a name="prerequisites"></a>Необходимые условия
В этом разделе предполагается, что вы знакомы со структурой базового приложения композиции, как описано в [Обзор визуального уровня](visual-layer.md).

## <a name="paint-with-a-compositionbrush"></a>Рисование с CompositionBrush

[CompositionBrush](https://docs.microsoft.com/uwp/api/Windows.UI.Composition.CompositionBrush) «закрашивает» область с выходными данными. Разные кисти имеют различные типы выводимых данных. Некоторые кисти закрашивают область сплошным цветом, другим пользователям, имеющим градиент, изображения, пользовательское рисование или эффекта. Существуют также специализированных кистей, которые изменяют поведение других кистей. Например маску непрозрачности можно использовать для управления, какие области закрашивается путем CompositionBrush или сетки можно использовать для управления stretch, применяются к CompositionBrush при рисовании области. CompositionBrush может иметь одно из следующих типов:

|Класс                                   |Сведения                                         |Представлена в|
|-------------------------------------|---------------------------------------------------------|--------------------------------------|
|[CompositionColorBrush](https://docs.microsoft.com/uwp/api/Windows.UI.Composition.CompositionColorBrush)         |Закрашивает область сплошным цветом                        |Обновление за ноябрь Windows10 (SDK 10586)|
|[CompositionSurfaceBrush](https://docs.microsoft.com/uwp/api/Windows.UI.Composition.CompositionSurfaceBrush)       |Закрашивает область содержимого [ICompositionSurface](https://docs.microsoft.com/en-us/uwp/api/Windows.UI.Composition.ICompositionSurface)|Обновление за ноябрь Windows10 (SDK 10586)|
|[CompositionEffectBrush](https://docs.microsoft.com/uwp/api/Windows.UI.Composition.CompositionEffectBrush)        |Закрашивает область с содержимым эффекта композиции |Обновление за ноябрь Windows10 (SDK 10586)|
|[CompositionMaskBrush](https://docs.microsoft.com/uwp/api/Windows.UI.Composition.CompositionMaskBrush)          |Закрашивает визуальный CompositionBrush с маску непрозрачности |Юбилейного обновления Windows10 (пакет SDK 14393)
|[CompositionNineGridBrush](https://docs.microsoft.com/uwp/api/Windows.UI.Composition.CompositionNineGridBrush)      |Закрашивает область с CompositionBrush, с помощью NineGrid stretch |Юбилейного обновления Windows10 SDK (14393)
|[CompositionLinearGradientBrush](https://docs.microsoft.com/uwp/api/windows.ui.composition.compositionlineargradientbrush)|Закрашивает область с помощью линейного градиента                    |Windows 10 Fall Creators Update (Insider Preview SDK)
|[CompositionBackdropBrush](https://docs.microsoft.com/uwp/api/Windows.UI.Composition.CompositionBackdropBrush)     |Закрашивает область по выборке точки фона либо приложения или пиксели непосредственно программной части окна приложения на рабочем столе. Используется в качестве входных данных для другой CompositionBrush как CompositionEffectBrush | Юбилейное обновление Windows 10 (пакет SDK 14393)

### <a name="paint-with-a-solid-color"></a>Рисования сплошным цветом

[CompositionColorBrush](https://docs.microsoft.com/uwp/api/Windows.UI.Composition.CompositionColorBrush) закрашивает область сплошным цветом. Существует множество способов указать цвета SolidColorBrush. Например можно указать его каналы альфа, красного, синего и зеленого (ARGB) или использовать один из предопределенных цветов, предоставленных классом [цвета](https://docs.microsoft.com/uwp/api/windows.ui.colors) .

Следующий рисунок и фрагмент кода демонстрируют небольшое визуальное дерево для создания прямоугольника, заштрихованного с помощью кисти черного цвета и заполненного с помощью кисти сплошного цвета с кодом 0x9ACD32.

![CompositionColorBrush](images/composition-compositioncolorbrush.png)

```cs
Compositor _compositor;
ContainerVisual _container;
SpriteVisual _colorVisual1, _colorVisual2;
CompositionColorBrush _blackBrush, _greenBrush;

_compositor = Window.Current.Compositor;
_container = _compositor.CreateContainerVisual();

_blackBrush = _compositor.CreateColorBrush(Colors.Black);
_colorVisual1= _compositor.CreateSpriteVisual();
_colorVisual1.Brush = _blackBrush;
_colorVisual1.Size = new Vector2(156, 156);
_colorVisual1.Offset = new Vector3(0, 0, 0);
_container.Children.InsertAtBottom(_colorVisual1);

_ greenBrush = _compositor.CreateColorBrush(Color.FromArgb(0xff, 0x9A, 0xCD, 0x32));
_colorVisual2 = _compositor.CreateSpriteVisual();
_colorVisual2.Brush = _greenBrush;
_colorVisual2.Size = new Vector2(150, 150);
_colorVisual2.Offset = new Vector3(3, 3, 0);
_container.Children.InsertAtBottom(_colorVisual2);
```

### <a name="paint-with-a-linear-gradient"></a>Рисование с помощью линейного градиента

[CompositionLinearGradientBrush](https://docs.microsoft.com/uwp/api/windows.ui.composition.compositionlineargradientbrush) закрашивает область линейный градиент. Линейный градиент сочетает два или более цветов через строку оси градиента. Объекты GradientStop используется для указания цвета градиента и их позиции.

Следующий рисунок и код показывает SpriteVisual, закрашенная с LinearGradientBrush с 2 останавливается, с помощью красного и желтого цветов.

![CompositionLinearGradientBrush](images/composition-compositionlineargradientbrush.png)

```cs
Compositor _compositor;
SpriteVisual _gradientVisual;
CompositionLinearGradientBrush _redyellowBrush;

_compositor = Window.Current.Compositor;

_redyellowBrush = _compositor.CreateLinearGradientBrush();
_redyellowBrush.ColorStops.Add(_compositor.CreateColorGradientStop(0, Colors.Red));
_redyellowBrush.ColorStops.Add(_compositor.CreateColorGradientStop(1, Colors.Yellow));
_gradientVisual = _compositor.CreateSpriteVisual();
_gradientVisual.Brush = _redyellowBrush;
_gradientVisual.Size = new Vector2(156, 156);
```

### <a name="paint-with-an-image"></a>Рисование с изображением

[CompositionSurfaceBrush](https://docs.microsoft.com/uwp/api/Windows.UI.Composition.CompositionSurfaceBrush) закрашивает область с пикселями, отображаемые в ICompositionSurface. Например CompositionSurfaceBrush может использоваться для рисования области с изображением отрисовки поверхность ICompositionSurface, с помощью [LoadedImageSurface](https://docs.microsoft.com/uwp/api/windows.ui.xaml.media.loadedimagesurface) API.

Следующий рисунок и кода показывают, что объекта SpriteVisual заполнен точечный licorice, отображаемые в ICompositionSurface, с помощью LoadedImageSurface. Свойства CompositionSurfaceBrush может использоваться растягивается и они будут согласованы растровое изображение в пределах границ визуального объекта.

![CompositionSurfaceBrush](images/composition-compositionsurfacebrush.png)

```cs
Compositor _compositor;
SpriteVisual _imageVisual;
CompositionSurfaceBrush _imageBrush;

_compositor = Window.Current.Compositor;

_imageBrush = _compositor.CreateSurfaceBrush();

// The loadedSurface has a size of 0x0 till the image has been been downloaded, decoded and loaded to the surface. We can assign the surface to the CompositionSurfaceBrush and it will show up once the image is loaded to the surface.
LoadedImageSurface _loadedSurface = LoadedImageSurface.StartLoadFromUri(new Uri("ms-appx:///Assets/licorice.jpg"));
_imageBrush.Surface = _loadedSurface;

_imageVisual = _compositor.CreateSpriteVisual();
_imageVisual.Brush = _imageBrush;
_imageVisual.Size = new Vector2(156, 156);
```

### <a name="paint-with-a-custom-drawing"></a>Рисовать с помощью пользовательской отрисовки
[CompositionSurfaceBrush](https://docs.microsoft.com/uwp/api/Windows.UI.Composition.CompositionSurfaceBrush) также может использоваться для рисования области с пикселей из ICompositionSurface, обработан с помощью [Win2D](http://microsoft.github.io/Win2D/html/Introduction.htm) (или D2D).

Следующий код показывает, что объект SpriteVisual окрашен с текстом, запустите обработанный на ICompositionSurface с помощью Win2D. Обратите внимание, что для использования Win2D, необходимо добавить пакет [Win2D NuGet](http://www.nuget.org/packages/Win2D.uwp) в проект.

```cs
Compositor _compositor;
CanvasDevice _device;
CompositionGraphicsDevice _compositionGraphicsDevice;
SpriteVisual _drawingVisual;
CompositionSurfaceBrush _drawingBrush;

_device = CanvasDevice.GetSharedDevice();
_compositionGraphicsDevice = CanvasComposition.CreateCompositionGraphicsDevice(_compositor, _device);

_drawingBrush = _compositor.CreateSurfaceBrush();
CompositionDrawingSurface _drawingSurface = _compositionGraphicsDevice.CreateDrawingSurface(new Size(256, 256), DirectXPixelFormat.B8G8R8A8UIntNormalized, DirectXAlphaMode.Premultiplied);

using (var ds = CanvasComposition.CreateDrawingSession(_drawingSurface))
{
     ds.Clear(Colors.Transparent);
     var rect = new Rect(new Point(2, 2), (_drawingSurface.Size.ToVector2() - new Vector2(4, 4)).ToSize());
     ds.FillRoundedRectangle(rect, 15, 15, Colors.LightBlue);
     ds.DrawRoundedRectangle(rect, 15, 15, Colors.Gray, 2);
     ds.DrawText("This is a composition drawing surface", rect, Colors.Black, new CanvasTextFormat()
     {
          FontFamily = "Comic Sans MS",
          FontSize = 32,
          WordWrapping = CanvasWordWrapping.WholeWord,
          VerticalAlignment = CanvasVerticalAlignment.Center,
          HorizontalAlignment = CanvasHorizontalAlignment.Center
     }
);

_drawingBrush.Surface = _drawingSurface;

_drawingVisual = _compositor.CreateSpriteVisual();
_drawingVisual.Brush = _drawingBrush;
_drawingVisual.Size = new Vector2(156, 156);
```

Аналогичным образом CompositionSurfaceBrush также может использоваться для рисования SpriteVisual с следует использовать подобласть, с помощью Win2D взаимодействия. [В этом примере](https://github.com/Microsoft/Win2D-Samples/tree/master/CompositionExample) приведен пример использования Win2D для рисования SpriteVisual с следует использовать подобласть.

### <a name="paint-with-a-video"></a>Рисование с видео
[CompositionSurfaceBrush](https://docs.microsoft.com/uwp/api/Windows.UI.Composition.CompositionSurfaceBrush) также может использоваться для рисования области с пикселей из ICompositionSurface, отрисовка с использованием видео, загружаются с помощью класса [MediaPlayer](https://docs.microsoft.com/en-us/uwp/api/Windows.Media.Playback.MediaPlayer) .

Следующий код показывает, что объект SpriteVisual заполнен загрузке в ICompositionSurface видео.

```cs
Compositor _compositor;
SpriteVisual _videoVisual;
CompositionSurfaceBrush _videoBrush;

// MediaPlayer set up with a create from URI

_mediaPlayer = new MediaPlayer();

// Get a source from a URI. This could also be from a file via a picker or a stream
var source = MediaSource.CreateFromUri(new Uri("http://go.microsoft.com/fwlink/?LinkID=809007&clcid=0x409"));
var item = new MediaPlaybackItem(source);
_mediaPlayer.Source = item;
_mediaPlayer.IsLoopingEnabled = true;

// Get the surface from MediaPlayer and put it on a brush
_videoSurface = _mediaPlayer.GetSurface(_compositor);
_videoBrush = _compositor.CreateSurfaceBrush(_videoSurface.CompositionSurface);

_videoVisual = _compositor.CreateSpriteVisual();
_videoVisual.Brush = _videoBrush;
_videoVisual.Size = new Vector2(156, 156);
```

### <a name="paint-with-a-filter-effect"></a>Рисование с эффект фильтра

[CompositionEffectBrush](https://docs.microsoft.com/uwp/api/Windows.UI.Composition.CompositionEffectBrush) закрашивает область вывода CompositionEffect. Эффекты в визуальном уровне могут рассматриваться как эффектов анимируемые фильтра, применяются к коллекции источника содержимого, например цветов, градиентов, изображений, видео, swapchains, области пользовательского интерфейса или деревья визуальных элементов. Содержимое исходного обычно задается с помощью другого CompositionBrush.

Следующий рисунок и код показывает SpriteVisual, заполнен изображение с минимальным уровнем насыщенности с примененным эффектом насыщенности фильтра.

![CompositionEffectBrush](images/composition-cat-desaturated.png)

```cs
Compositor _compositor;
SpriteVisual _effectVisual;
CompositionEffectBrush _effectBrush;

_compositor = Window.Current.Compositor;

var graphicsEffect = new SaturationEffect {
                              Saturation = 0.0f,
                              Source = new CompositionEffectSourceParameter("mySource")
                         };

var effectFactory = _compositor.CreateEffectFactory(graphicsEffect);
_effectBrush = effectFactory.CreateBrush();

CompositionSurfaceBrush surfaceBrush =_compositor.CreateSurfaceBrush();
LoadedImageSurface loadedSurface = LoadedImageSurface.StartLoadFromUri(new Uri("ms-appx:///Assets/cat.jpg"));
SurfaceBrush.surface = loadedSurface;

_effectBrush.SetSourceParameter("mySource", surfaceBrush);

_effectVisual = _compositor.CreateSpriteVisual();
_effectVisual.Brush = _effectBrush;
_effectVisual.Size = new Vector2(156, 156);
```

Дополнительные сведения о создании эффекта с помощью CompositionBrushes см. в разделе [эффектов в визуальном уровне](https://docs.microsoft.com/en-us/windows/uwp/composition/composition-effects)

### <a name="paint-with-a-compositionbrush-with-opacity-mask-applied"></a>Рисование с помощью CompositionBrush с применить маску непрозрачности

[CompositionMaskBrush](https://docs.microsoft.com/uwp/api/Windows.UI.Composition.CompositionMaskBrush) закрашивает область CompositionBrush с маску непрозрачности, примененным к ней. Источник маску непрозрачности может быть любой CompositionBrush типа CompositionColorBrush, CompositionLinearGradientBrush, CompositionSurfaceBrush, CompositionEffectBrush или CompositionNineGridBrush. В качестве CompositionSurfaceBrush необходимо указать маску непрозрачности.

Следующий рисунок и код показывает SpriteVisual, закрашенная с помощью CompositionMaskBrush. Источник маска — CompositionLinearGradientBrush, который маскируется следующим круг, используя изображение круг в качестве маски.

![CompositionMaskBrush](images/composition-compositionmaskbrush.png)

```cs
Compositor _compositor;
SpriteVisual _maskVisual;
CompositionMaskBrush _maskBrush;

_compositor = Window.Current.Compositor;

_maskBrush = _compositor.CreateMaskBrush();

CompositionLinearGradientBrush _sourceGradient = _compositor.CreateLinearGradientBrush();
_sourceGradient.ColorStops.Add(_compositor.CreateColorGradientStop(0,Colors.Red));
_sourceGradient.ColorStops.Add(_compositor.CreateColorGradientStop(1,Colors.Yellow));
_maskBrush.Source = _sourceGradient;

LoadedImageSurface loadedSurface = LoadedImageSurface.StartLoadFromUri(new Uri("ms-appx:///Assets/circle.png"), new Size(156.0, 156.0));
_maskBrush.Mask = _compositor.CreateSurfaceBrush(loadedSurface);

_maskVisual = _compositor.CreateSpriteVisual();
_maskVisual.Brush = _maskBrush;
_maskVisual.Size = new Vector2(156, 156);
```

### <a name="paint-with-a-compositionbrush-using-ninegrid-stretch"></a>Рисование с CompositionBrush, с помощью NineGrid stretch

[CompositionNineGridBrush](https://docs.microsoft.com/uwp/api/Windows.UI.Composition.CompositionNineGridBrush) закрашивает область с CompositionBrush, растягивается с помощью метафора сетки. Метафора сетки позволяет по-разному растягивается края и углы CompositionBrush от своего центра. Источник stretch сетки можно с помощью любого CompositionBrush типа CompositionColorBrush, CompositionSurfaceBrush или CompositionEffectBrush.

Следующий код показывает, что объект SpriteVisual заполнен CompositionNineGridBrush. Источник маска — CompositionSurfaceBrush, который растянут с помощью сетки.

```cs
Compositor _compositor;
SpriteVisual _nineGridVisual;
CompositionNineGridBrush _nineGridBrush;

_compositor = Window.Current.Compositor;

_ninegridBrush = _compositor.CreateNineGridBrush();

// nineGridImage.png is 50x50 pixels; nine-grid insets, as measured relative to the actual size of the image, are: left = 1, top = 5, right = 10, bottom = 20 (in pixels)

LoadedImageSurface _imageSurface = LoadedImageSurface.StartLoadFromUri(new Uri("ms-appx:///Assets/nineGridImage.png"));
_nineGridBrush.Source = _compositor.CreateSurfaceBrush(_imageSurface);

// set Nine-Grid Insets

_ninegridBrush.SetInsets(1, 5, 10, 20);

// set appropriate Stretch on SurfaceBrush for Center of Nine-Grid

sourceBrush.Stretch = CompositionStretch.Fill;

_nineGridVisual = _compositor.CreateSpriteVisual();
_nineGridVisual.Brush = _ninegridBrush;
_nineGridVisual.Size = new Vector2(100, 75);
```

### <a name="paint-using-background-pixels"></a>Рисование с помощью фоновой пикселей

[CompositionBackdropBrush](https://docs.microsoft.com/uwp/api/Windows.UI.Composition.CompositionBackdropBrush) закрашивает область содержимому позади области. CompositionBackdropBrush никогда не используется сама по себе, но вместо этого используется в качестве входных данных для другой CompositionBrush как EffectBrush. Например с помощью CompositionBackdropBrush в качестве входных данных для эффекта размытия, можно добиться эффекта матированного стекла.

В следующем коде показано небольшое визуальное дерево для создания образа с помощью CompositionSurfaceBrush и наложение матированное стекло поверх изображения. Наложение матированное стекло создается путем размещения SpriteVisual, заполненного EffectBrush над изображением. EffectBrush использует CompositionBackdropBrush в качестве входных данных для эффекта размытия.

```cs
Compositor _compositor;
SpriteVisual _containerVisual;
SpriteVisual _imageVisual;
SpriteVisual _backdropVisual;

_compositor = Window.Current.Compositor;

// Create container visual to host the visual tree
_containerVisual = _compositor.CreateContainerVisual();

// Create _imageVisual and add it to the bottom of the container visual.
// Paint the visual with an image.

CompositionSurfaceBrush _licoriceBrush = _compositor.CreateSurfaceBrush();

LoadedImageSurface loadedSurface = 
    LoadedImageSurface.StartLoadFromUri(new Uri("ms-appx:///Assets/licorice.jpg"));
_licoriceBrush.Surface = loadedSurface;

_imageVisual = _compositor.CreateSpriteVisual();
_imageVisual.Brush = _licoriceBrush;
_imageVisual.Size = new Vector2(156, 156);
_imageVisual.Offset = new Vector3(0, 0, 0);
_containerVisual.Children.InsertAtBottom(_imageVisual)

// Create a SpriteVisual and add it to the top of the containerVisual.
// Paint the visual with an EffectBrush that applies blur to the content
// underneath the Visual to create a frosted glass effect.

GaussianBlurEffect blurEffect = new GaussianBlurEffect(){
                                    Name = "Blur",
                                    BlurAmount = 1.0f,
                                    BorderMode = EffectBorderMode.Hard,
                                    Source = new CompositionEffectSourceParameter("source");
                                    };

CompositionEffectFactory blurEffectFactory = _compositor.CreateEffectFactory(blurEffect);
CompositionEffectBrush _backdropBrush = blurEffectFactory.CreateBrush();

// Create a BackdropBrush and bind it to the EffectSourceParameter source

_backdropBrush.SetSourceParameter("source", _compositor.CreateBackdropBrush());
_backdropVisual = _compositor.CreateSpriteVisual();
_backdropVisual.Brush = _licoriceBrush;
_backdropVisual.Size = new Vector2(78, 78);
_backdropVisual.Offset = new Vector3(39, 39, 0);
_containerVisual.Children.InsertAtTop(_backdropVisual);
```

## <a name="combining-compositionbrushes"></a>Объединение CompositionBrushes
Количество CompositionBrushes использовать другие CompositionBrushes в качестве входных данных. Например с помощью метода SetSourceParameter может использоваться для установки другого CompositionBrush в качестве входных данных для CompositionEffectBrush. В следующей таблице описаны поддерживаемые сочетания CompositionBrushes. Обратите внимание, что с помощью использования неподдерживаемого сочетания вызовет исключение.

<table>
<tbody>
<tr>
<th>Brush</th>
<th>EffectBrush.SetSourceParameter()</th>
<th>MaskBrush.Mask</th>
<th>MaskBrush.Source</th>
<th>NineGridBrush.Source</th>
</tr>
<tr>
<td>CompositionColorBrush</td>
<td>ДА</td>
<td>ДА</td>
<td>ДА</td>
<td>ДА</td>
</tr>
<tr>
<td>CompositionLinear<br />GradientBrush</td>
<td>ДА</td>
<td>ДА</td>
<td>ДА</td>
<td>NO</td>
</tr>
<tr>
<td>CompositionSurfaceBrush</td>
<td>ДА</td>
<td>ДА</td>
<td>ДА</td>
<td>ДА</td>
</tr>
<tr>
<td>CompositionEffectBrush</td>
<td>НЕТ</td>
<td>НЕТ</td>
<td>ДА</td>
<td>NO</td>
</tr>
<tr>
<td>CompositionMaskBrush</td>
<td>НЕТ</td>
<td>НЕТ</td>
<td>НЕТ</td>
<td>НЕТ</td>
</tr>
<tr>
<td>CompositionNineGridBrush</td>
<td>ДА</td>
<td>ДА</td>
<td>ДА</td>
<td>NO</td>
</tr>
<tr>
<td>CompositionBackdropBrush</td>
<td>ДА</td>
<td>НЕТ</td>
<td>НЕТ</td>
<td>НЕТ</td>
</tr>
</tbody>
</table>


## <a name="using-a-xaml-brush-vs-compositionbrush"></a>С помощью CompositionBrush и с кисти XAML

В таблице ниже представлены список сценариев и ли стандартные использования кисти XAML или композиции при рисовании UIElement или SpriteVisual в приложении. 

> [!NOTE]
> Если CompositionBrush предлагается для UIElement для XAML, предполагается, что CompositionBrush упаковано с использованием XamlCompositionBrushBase.

|Сценарий                                                                   | UIElement для XAML                                                                                                |SpriteVisual композиции
|---------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------|----------------------------------
|Закрашивают область со сплошным цветом                                             |[SolidColorBrush](https://msdn.microsoft.com/library/windows/apps/BR242962)                                |[CompositionColorBrush](https://msdn.microsoft.com/library/windows/apps/Mt589399)
|Закрашивают область с анимированных цвета                                          |[SolidColorBrush](https://msdn.microsoft.com/library/windows/apps/BR242962)                                |[CompositionColorBrush](https://msdn.microsoft.com/library/windows/apps/Mt589399)
|Закрашивают область градиентом, статические                                       |[LinearGradientBrush](https://msdn.microsoft.com/library/windows/apps/BR210108)                            |[CompositionLinearGradientBrush](https://docs.microsoft.com/uwp/api/windows.ui.composition.compositionlineargradientbrush)
|Закрашивают область с анимированных градиента                                 |[CompositionLinearGradientBrush](https://docs.microsoft.com/uwp/api/windows.ui.composition.compositionlineargradientbrush)                                                                                 |[CompositionLinearGradientBrush](https://docs.microsoft.com/uwp/api/windows.ui.composition.compositionlineargradientbrush)
|Закрашивают область с изображением                                                |[ImageBrush](https://msdn.microsoft.com/library/windows/apps/BR210101)                                     |[CompositionSurfaceBrush](https://msdn.microsoft.com/library/windows/apps/Mt589415)
|Закрашивают область с веб-страницы                                               |[WebViewBrush](https://msdn.microsoft.com/library/windows/apps/BR227703)                                   |Н/Д
|Закрашивают область изображением, воспользовавшись NineGrid stretch                         |[Элемент управления Image](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Image)                   |[CompositionNineGridBrush](https://docs.microsoft.com/uwp/api/Windows.UI.Composition.CompositionNineGridBrush)
|Закрашивают область с анимированных stretch NineGrid                               |[CompositionNineGridBrush](https://docs.microsoft.com/uwp/api/Windows.UI.Composition.CompositionNineGridBrush)                                                                                       |[CompositionNineGridBrush](https://docs.microsoft.com/uwp/api/Windows.UI.Composition.CompositionNineGridBrush)
|Закрашивают область следует использовать подобласть                                             |[SwapChainPanel](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.SwapChainPanel)                                                                                                 |[CompositionSurfaceBrush](https://msdn.microsoft.com/library/windows/apps/Mt589415) с взаимодействия следует использовать подобласть
|Закрашивают область с видео                                                 |[MediaElement](https://msdn.microsoft.com/library/windows/apps/mt187272.aspx)                                                                                                  |[CompositionSurfaceBrush](https://msdn.microsoft.com/library/windows/apps/Mt589415) с взаимодействия мультимедиа
|Закрашивают область с пользовательской двумерной отрисовки                                       |[CanvasControl](http://microsoft.github.io/Win2D/html/T_Microsoft_Graphics_Canvas_UI_Xaml_CanvasControl.htm) из Win2D                                                                                                 |[CompositionSurfaceBrush](https://msdn.microsoft.com/library/windows/apps/Mt589415) с Win2D взаимодействия
|Закрашивают область с неанимированные маски                                       |Используйте XAML [фигуры](https://docs.microsoft.com/windows/uwp/graphics/drawing-shapes) , чтобы определить маски   |[CompositionMaskBrush](https://docs.microsoft.com/uwp/api/Windows.UI.Composition.CompositionMaskBrush)
|Закрашивают область с анимированной маски                                        |[CompositionMaskBrush](https://docs.microsoft.com/uwp/api/Windows.UI.Composition.CompositionMaskBrush)                                                                                           |[CompositionMaskBrush](https://docs.microsoft.com/uwp/api/Windows.UI.Composition.CompositionMaskBrush)
|Закрашивают область с эффектом анимированных фильтра                               |[CompositionEffectBrush](https://docs.microsoft.com/uwp/api/Windows.UI.Composition.CompositionEffectBrush)                                                                                         |[CompositionEffectBrush](https://docs.microsoft.com/uwp/api/Windows.UI.Composition.CompositionEffectBrush)
|Закрашивают область с эффект, примененный к пикселям фона        |[CompositionBackdropBrush](https://docs.microsoft.com/uwp/api/Windows.UI.Composition.CompositionBackdropBrush)                                                                                        |[CompositionBackdropBrush](https://docs.microsoft.com/uwp/api/Windows.UI.Composition.CompositionBackdropBrush)

## <a name="related-topics"></a>Еще по теме

[Композиция встроенного DirectX и Direct2D взаимодействия с BeginDraw и EndDraw](composition-native-interop.md)

[Взаимодействие с XAML кисти с XamlCompositionBrushBase](/windows/uwp/design/style/brushes#xamlcompositionbrushbase)

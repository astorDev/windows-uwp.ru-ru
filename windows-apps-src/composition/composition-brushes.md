---
author: jwmsft
ms.assetid: 03dd256f-78c0-e1b1-3d9f-7b3afab29b2f
title: Кисти композиции
description: Кисть заполняет пространство объекта класса Visual своими выводимыми данными. Разные кисти имеют различные типы выводимых данных.
ms.author: jimwalk
ms.date: 02/08/2017
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: 103ecd24c35d75d3ea1d305d7294048dc628d2e2
ms.sourcegitcommit: 897a111e8fc5d38d483800288ad01c523e924ef4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/13/2018
ms.locfileid: "1038574"
---
# <a name="composition-brushes"></a>Кисти композиции
Все элементы, видимые на экране из UWP приложения отображается, поскольку осуществлялся с помощью кисти. Кисти позволяют рисовать объекты пользовательского интерфейса пользователя с контентом, от простого, сплошные цвета изображения или документы в цепочку сложных эффектов. В этом разделе понятия рисования с CompositionBrush.

Обратите внимание на то, при работе с XAML UWP приложения можно выбрать для рисования элементов интерфейса пользователя с помощью [XAML кисти](/windows/uwp/design/style/brushes) или [CompositionBrush](https://docs.microsoft.com/uwp/api/Windows.UI.Composition.CompositionBrush). Как правило проще и рекомендуется выберите кисть XAML, если сценария поддерживается точкой кисти XAML. Например анимация цвета кнопки, изменение заливки текста или фигуры с изображением. С другой стороны Если вы пытаетесь создать то, что не поддерживается в XAML кисть как как рисования маски анимационной или анимационной растяните девяти сетки или в силу цепочке, можно использовать CompositionBrush для рисования элементов интерфейса пользователя посредством использования [ XamlCompositionBrushBase](https://docs.microsoft.com/uwp/api/windows.ui.xaml.media.xamlcompositionbrushbase).

При работе с Visual слой CompositionBrush должен использоваться для рисования области [SpriteVisual](https://docs.microsoft.com/uwp/api/Windows.UI.Composition.SpriteVisual).

-   [Предварительные условия](./composition-brushes.md#prerequisites)
-   [Рисование с CompositionBrush](./composition-brushes.md#paint-with-a-compositionbrush)
    -   [Цветом сплошной](./composition-brushes.md#paint-with-a-solid-color)
    -   [Рисование с линейным градиентом](./composition-brushes.md#paint-with-a-linear-gradient)
    -   [Рисование с изображения](./composition-brushes.md#paint-with-an-image)
    -   [Рисование с настраиваемого документа](./composition-brushes.md#paint-with-a-custom-drawing)
    -   [Рисование с видео](./composition-brushes.md#paint-with-a-video)
    -   [Рисование с эффект фильтра](./composition-brushes.md#paint-with-a-filter-effect)
    -   [Рисование с CompositionBrush с непрозрачную маску](./composition-brushes.md#paint-with-a-compositionbrush-with-opacity-mask-applied)
    -   [Рисование с CompositionBrush, с помощью растяните NineGrid](./composition-brushes.md#paint-with-a-compositionbrush-using-ninegrid-stretch)
    -   [Рисование с помощью пикселей фона](./composition-brushes.md#paint-using-background-pixels)
-   [Совместное использование CompositionBrushes](./composition-brushes.md#combining-compositionbrushes)
-   [С помощью CompositionBrush в сравнении с кисти XAML](./composition-brushes.md#using-a-xaml-brush-vs-compositionbrush)
-   [Еще по теме](./composition-brushes.md#related-topics)

## <a name="prerequisites"></a>Предварительные условия
Этот обзор предполагает, что вы знакомы с структура базового приложения композиции, как описано в разделе [Общие сведения о визуальном уровне](visual-layer.md).

## <a name="paint-with-a-compositionbrush"></a>Рисование с CompositionBrush

[CompositionBrush](https://docs.microsoft.com/uwp/api/Windows.UI.Composition.CompositionBrush) «заполняет» область с его выходные данные. Разные кисти имеют различные типы выводимых данных. Некоторые кисти рисования область твердотельные цветом, другим пользователям, имеющим градиентом, изображения, настраиваемого документа или влияние. Существует также специализированных кисти, которые изменяют поведение других кисти. Например непрозрачную маску можно использовать для управления, какие области прорисовывается с CompositionBrush или сетку девяти можно использовать для управления растяните, применяется к CompositionBrush при рисовании области. CompositionBrush может быть одного из следующих типов:

|Класс                                   |Сведения                                         |Представлено в|
|-------------------------------------|---------------------------------------------------------|--------------------------------------|
|[CompositionColorBrush](https://docs.microsoft.com/uwp/api/Windows.UI.Composition.CompositionColorBrush)         |Заполняет область твердотельные цветом                        |10 ноября Windows Update (пакет SDK для 10586)|
|[CompositionSurfaceBrush](https://docs.microsoft.com/uwp/api/Windows.UI.Composition.CompositionSurfaceBrush)       |Заполняет область с содержимым [ICompositionSurface](https://docs.microsoft.com/en-us/uwp/api/Windows.UI.Composition.ICompositionSurface)|10 ноября Windows Update (пакет SDK для 10586)|
|[CompositionEffectBrush](https://docs.microsoft.com/uwp/api/Windows.UI.Composition.CompositionEffectBrush)        |Заполняет область с содержимым влияние композиции |10 ноября Windows Update (пакет SDK для 10586)|
|[CompositionMaskBrush](https://docs.microsoft.com/uwp/api/Windows.UI.Composition.CompositionMaskBrush)          |Заполняет visual с CompositionBrush с непрозрачную маску |Центр обновления Windows 10 Годовщина (пакет SDK для 14393)
|[CompositionNineGridBrush](https://docs.microsoft.com/uwp/api/Windows.UI.Composition.CompositionNineGridBrush)      |Заполняет область с CompositionBrush, используя расширение NineGrid |Центр обновления Windows 10 Годовщина SDK (14393)
|[CompositionLinearGradientBrush](https://docs.microsoft.com/uwp/api/windows.ui.composition.compositionlineargradientbrush)|Заполняет область линейным градиентом                    |Windows 10 могут быть разделены создателей обновления (Предварительная версия изнутри SDK)
|[CompositionBackdropBrush](https://docs.microsoft.com/uwp/api/Windows.UI.Composition.CompositionBackdropBrush)     |Заполняет область с выборки фона точки либо приложения или пикселей непосредственно под окном приложения на рабочем столе. Используется в качестве входных данных для другой CompositionBrush как CompositionEffectBrush | Центр обновления Windows 10 Годовщина (пакет SDK для 14393)

### <a name="paint-with-a-solid-color"></a>Цветом сплошной

[CompositionColorBrush](https://docs.microsoft.com/uwp/api/Windows.UI.Composition.CompositionColorBrush) заполняет область твердотельные цветом. Существуют различные способы задания цвета SolidColorBrush. Например можно указать его каналы альфа, красного, синего и зеленого (ARGB) или использовать один из предварительно заданных цветов в классе [цвета](https://docs.microsoft.com/uwp/api/windows.ui.colors) .

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

### <a name="paint-with-a-linear-gradient"></a>Рисование с линейным градиентом

[CompositionLinearGradientBrush](https://docs.microsoft.com/uwp/api/windows.ui.composition.compositionlineargradientbrush) заполняет область линейным градиентом. Линейный градиент сочетает два или более цветов через линию, оси градиента. Объекты GradientStop используется для указания цветов градиента и их позиции.

Следующие иллюстрации и кода показано SpriteVisual, имеющая LinearGradientBrush с 2 точками градиента с помощью красный и желтый цвет.

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

### <a name="paint-with-an-image"></a>Рисование с изображения

[CompositionSurfaceBrush](https://docs.microsoft.com/uwp/api/Windows.UI.Composition.CompositionSurfaceBrush) заполняет область с помощью пикселов, отображаемые в ICompositionSurface. Например CompositionSurfaceBrush можно использовать для рисования области с изображением отображается область ICompositionSurface с помощью [LoadedImageSurface](https://docs.microsoft.com/uwp/api/windows.ui.xaml.media.loadedimagesurface) API.

Следующие иллюстрации и код отображает SpriteVisual, имеющая растровое изображение licorice, отображаемые в ICompositionSurface, с помощью LoadedImageSurface. Свойства CompositionSurfaceBrush можно использовать для увеличить и выровнять растровое изображение в пределах границ визуального.

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

### <a name="paint-with-a-custom-drawing"></a>Рисование с настраиваемого документа
[CompositionSurfaceBrush](https://docs.microsoft.com/uwp/api/Windows.UI.Composition.CompositionSurfaceBrush) можно также использовать для рисования области с пикселей из ICompositionSurface, для просмотра с помощью [Win2D](http://microsoft.github.io/Win2D/html/Introduction.htm) (или D2D).

В следующем коде показан, SpriteVisual, имеющая прогона отображаемой на ICompositionSurface текста с помощью Win2D. Обратите внимание на то, перед использованием Win2D, необходимо включить пакетов [Win2D NuGet](http://www.nuget.org/packages/Win2D.uwp) в проект.

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

Аналогично CompositionSurfaceBrush также используется для рисования SpriteVisual с SwapChain, с помощью Win2D взаимодействия. [В этом примере](https://github.com/Microsoft/Win2D-Samples/tree/master/CompositionExample) приведен пример использования Win2D для рисования SpriteVisual с swapchain.

### <a name="paint-with-a-video"></a>Рисование с видео
[CompositionSurfaceBrush](https://docs.microsoft.com/uwp/api/Windows.UI.Composition.CompositionSurfaceBrush) можно также использовать для рисования области с пикселей из ICompositionSurface, отображаются с использованием видео загружается через класс [MediaPlayer](https://docs.microsoft.com/en-us/uwp/api/Windows.Media.Playback.MediaPlayer) .

В следующем коде показано, что SpriteVisual, имеющая загрузке в ICompositionSurface видео.

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

[CompositionEffectBrush](https://docs.microsoft.com/uwp/api/Windows.UI.Composition.CompositionEffectBrush) заполняет область с вывода CompositionEffect. Эффекты в визуальном уровне может рассматриваться как анимируемым фильтра эффекты, примененные к коллекции из источника контента, например цвета, градиентом, изображения, видео, swapchains, области пользовательского интерфейса или деревья визуальных элементов. Источник контента обычно задается с помощью другого CompositionBrush.

Следующие иллюстрации и кода показано SpriteVisual, цветом изображение cat, имеющей уменьшение насыщения эффект фильтра.

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

Дополнительные сведения о создании эффектов, с помощью CompositionBrushes можно [эффектов в визуальном уровне](https://docs.microsoft.com/en-us/windows/uwp/composition/composition-effects)

### <a name="paint-with-a-compositionbrush-with-opacity-mask-applied"></a>Рисование с CompositionBrush с непрозрачность маску, применяемую

[CompositionMaskBrush](https://docs.microsoft.com/uwp/api/Windows.UI.Composition.CompositionMaskBrush) заполняет область с CompositionBrush с непрозрачную маску, примененных к нему. Источник непрозрачную маску может быть любой CompositionBrush тип CompositionColorBrush, CompositionLinearGradientBrush, CompositionSurfaceBrush, CompositionEffectBrush или CompositionNineGridBrush. Маска прозрачности должен быть указан как CompositionSurfaceBrush.

Следующие иллюстрации и кода показано SpriteVisual, имеющая CompositionMaskBrush. Источник маски — CompositionLinearGradientBrush, которая скрывается следующим circle, используя изображение круга в качестве маску.

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

### <a name="paint-with-a-compositionbrush-using-ninegrid-stretch"></a>Рисование с CompositionBrush, с помощью растяните NineGrid

[CompositionNineGridBrush](https://docs.microsoft.com/uwp/api/Windows.UI.Composition.CompositionNineGridBrush) заполняет область с CompositionBrush, растягивается с помощью модели девяти сетки. Сравнение девяти сетки позволяет увеличить края и углы CompositionBrush по-разному чем его центра. Источник растяните девяти сетки можно по любой CompositionBrush типа CompositionColorBrush, CompositionSurfaceBrush или CompositionEffectBrush.

В следующем коде показано, что SpriteVisual, имеющая CompositionNineGridBrush. Источник маски — CompositionSurfaceBrush которого растягивается использование девяти сетки.

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

### <a name="paint-using-background-pixels"></a>Рисование с помощью пикселей фона

[CompositionBackdropBrush](https://docs.microsoft.com/uwp/api/Windows.UI.Composition.CompositionBackdropBrush) заполняет область с содержимым за область. CompositionBackdropBrush никогда не используется самостоятельно, но вместо этого используется в качестве входных данных для другой CompositionBrush как EffectBrush. Например, используя CompositionBackdropBrush в качестве входных данных для эффекта размытия, можно достичь эффект матовый стекла.

В следующем коде показан небольшой визуального дерева для создания образа с помощью CompositionSurfaceBrush и перекрытия матовый стекло над изображением. Перекрытие матовый стекло создается, поместив SpriteVisual, заполненный EffectBrush над изображением. EffectBrush использует CompositionBackdropBrush в качестве входных данных для эффекта размытия.

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

## <a name="combining-compositionbrushes"></a>Совместное использование CompositionBrushes
Число CompositionBrushes использовать другие CompositionBrushes в качестве входных данных. Например с помощью метода SetSourceParameter можно использовать для установите другой CompositionBrush в качестве входных данных для CompositionEffectBrush. В следующей таблице описываются поддерживаемые сочетания CompositionBrushes. Обратите внимание на то, что с помощью неподдерживаемых сочетание вызовет исключение.

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


## <a name="using-a-xaml-brush-vs-compositionbrush"></a>С помощью CompositionBrush в сравнении с кисти XAML

В следующей таблице приведен список сценариев и ли заданные XAML или формирование использования кисти при рисовании элементов интерфейса пользователя или SpriteVisual в приложении. 

> [!NOTE]
> Если CompositionBrush предлагается для элементов интерфейса пользователя XAML, предполагается, что CompositionBrush упакован с помощью XamlCompositionBrushBase.

|Сценарий                                                                   | XAML элементов интерфейса пользователя                                                                                                |Формирование SpriteVisual
|---------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------|----------------------------------
|Рисование области с цвета                                             |[SolidColorBrush](https://msdn.microsoft.com/library/windows/apps/BR242962)                                |[CompositionColorBrush](https://msdn.microsoft.com/library/windows/apps/Mt589399)
|Рисование области анимационной цветом                                          |[SolidColorBrush](https://msdn.microsoft.com/library/windows/apps/BR242962)                                |[CompositionColorBrush](https://msdn.microsoft.com/library/windows/apps/Mt589399)
|Рисование область статического градиентом                                       |[LinearGradientBrush](https://msdn.microsoft.com/library/windows/apps/BR210108)                            |[CompositionLinearGradientBrush](https://docs.microsoft.com/uwp/api/windows.ui.composition.compositionlineargradientbrush)
|Рисование области с анимационной градиента                                 |[CompositionLinearGradientBrush](https://docs.microsoft.com/uwp/api/windows.ui.composition.compositionlineargradientbrush)                                                                                 |[CompositionLinearGradientBrush](https://docs.microsoft.com/uwp/api/windows.ui.composition.compositionlineargradientbrush)
|Рисование области с изображением                                                |[ImageBrush](https://msdn.microsoft.com/library/windows/apps/BR210101)                                     |[CompositionSurfaceBrush](https://msdn.microsoft.com/library/windows/apps/Mt589415)
|Рисование области с веб-страницы                                               |[WebViewBrush](https://msdn.microsoft.com/library/windows/apps/BR227703)                                   |Н/Д
|Рисование области с изображения с помощью растяните NineGrid                         |[Элемент управления изображения](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Image)                   |[CompositionNineGridBrush](https://docs.microsoft.com/uwp/api/Windows.UI.Composition.CompositionNineGridBrush)
|Рисование области с анимационной растяните NineGrid                               |[CompositionNineGridBrush](https://docs.microsoft.com/uwp/api/Windows.UI.Composition.CompositionNineGridBrush)                                                                                       |[CompositionNineGridBrush](https://docs.microsoft.com/uwp/api/Windows.UI.Composition.CompositionNineGridBrush)
|Рисование области с swapchain                                             |[SwapChainPanel](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.SwapChainPanel)                                                                                                 |[CompositionSurfaceBrush](https://msdn.microsoft.com/library/windows/apps/Mt589415) с swapchain взаимодействия
|Рисование области с видео                                                 |[MediaElement](https://msdn.microsoft.com/library/windows/apps/mt187272.aspx)                                                                                                  |[CompositionSurfaceBrush](https://msdn.microsoft.com/library/windows/apps/Mt589415) подключение с мультимедиа взаимодействия
|Рисование области с настраиваемой 2D документа                                       |[CanvasControl](http://microsoft.github.io/Win2D/html/T_Microsoft_Graphics_Canvas_UI_Xaml_CanvasControl.htm) из Win2D                                                                                                 |[CompositionSurfaceBrush](https://msdn.microsoft.com/library/windows/apps/Mt589415) с Win2D взаимодействия
|Рисование области с простое маска                                       |Использование XAML [фигур](https://docs.microsoft.com/windows/uwp/graphics/drawing-shapes) для определения маски   |[CompositionMaskBrush](https://docs.microsoft.com/uwp/api/Windows.UI.Composition.CompositionMaskBrush)
|Рисование области с анимационной маска                                        |[CompositionMaskBrush](https://docs.microsoft.com/uwp/api/Windows.UI.Composition.CompositionMaskBrush)                                                                                           |[CompositionMaskBrush](https://docs.microsoft.com/uwp/api/Windows.UI.Composition.CompositionMaskBrush)
|Рисование области с эффектом анимационной фильтра                               |[CompositionEffectBrush](https://docs.microsoft.com/uwp/api/Windows.UI.Composition.CompositionEffectBrush)                                                                                         |[CompositionEffectBrush](https://docs.microsoft.com/uwp/api/Windows.UI.Composition.CompositionEffectBrush)
|Рисование области с эффекта, применяемого к пикселей фона        |[CompositionBackdropBrush](https://docs.microsoft.com/uwp/api/Windows.UI.Composition.CompositionBackdropBrush)                                                                                        |[CompositionBackdropBrush](https://docs.microsoft.com/uwp/api/Windows.UI.Composition.CompositionBackdropBrush)

## <a name="related-topics"></a>Еще по теме

[Формирование встроенного DirectX и возможности Direct2D взаимодействия с BeginDraw и EndDraw](composition-native-interop.md)

[XAML кисти взаимодействия с XamlCompositionBrushBase](/windows/uwp/design/style/brushes#xamlcompositionbrushbase)

---
ms.assetid: 03dd256f-78c0-e1b1-3d9f-7b3afab29b2f
title: Кисти композиции
description: Кисть заполняет пространство объекта класса Visual своими выводимыми данными. Разные кисти имеют различные типы выводимых данных.
ms.date: 04/19/2019
ms.topic: article
ms.custom: 19H1
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: d51bc945c721ae15889dece8f84959f9a78192bc
ms.sourcegitcommit: fca0132794ec187e90b2ebdad862f22d9f6c0db8
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/24/2019
ms.locfileid: "63790518"
---
# <a name="composition-brushes"></a>Кисти композиции
Все, что отображается на экране в приложении UWP, отображается, поскольку нарисовано с помощью кисти. Кисти позволяют рисовать объекты пользовательского интерфейса с содержимым, начиная с простых сплошных цветов в изображениях и рисунках до цепочек сложных эффектов. В этом разделе представлены понятия, связанные с CompositionBrush.

Обратите внимание, что при работе с приложением UWP на XAML можно рисовать UIElement с помощью [кисти XAML](/windows/uwp/design/style/brushes) или [CompositionBrush](https://docs.microsoft.com/uwp/api/Windows.UI.Composition.CompositionBrush). Как правило, проще и понятнее выбрать кисть XAML, если ваш сценарий поддерживается кистью XAML. Например, анимация цвета кнопки, изменение заливки текста или фигуры с изображением. С другой стороны, если вы пытаетесь создать то, что не поддерживается в XAML brush, например рисования с помощью анимированной маски или анимированных stretch девять сетки или в цепочке эффект, можно использовать CompositionBrush для рисования элемента UIElement помощи [ XamlCompositionBrushBase](https://docs.microsoft.com/uwp/api/windows.ui.xaml.media.xamlcompositionbrushbase).

При работе с использованием визуального уровня необходимо использовать CompositionBrush для рисования области [SpriteVisual](https://docs.microsoft.com/uwp/api/Windows.UI.Composition.SpriteVisual).

-   [Предварительные условия](./composition-brushes.md#prerequisites)
-   [Рисование с CompositionBrush](./composition-brushes.md#paint-with-a-compositionbrush)
    -   [Закраска сплошным цветом](./composition-brushes.md#paint-with-a-solid-color)
    -   [Рисование с линейным градиентом](./composition-brushes.md#paint-with-a-linear-gradient) 
    -   [Рисование с применением радиального градиента](./composition-brushes.md#paint-with-a-radial-gradient)
    -   [Рисование с изображением](./composition-brushes.md#paint-with-an-image)
    -   [Рисование с нестандартных операций рисования](./composition-brushes.md#paint-with-a-custom-drawing)
    -   [Рисование с видео](./composition-brushes.md#paint-with-a-video)
    -   [Рисование с эффект фильтра](./composition-brushes.md#paint-with-a-filter-effect)
    -   [Рисование с CompositionBrush с маской непрозрачности](./composition-brushes.md#paint-with-a-compositionbrush-with-opacity-mask-applied)
    -   [Рисование с CompositionBrush, с помощью NineGrid stretch](./composition-brushes.md#paint-with-a-compositionbrush-using-ninegrid-stretch)
    -   [Рисование с помощью фона пикселей](./composition-brushes.md#paint-using-background-pixels)
-   [Объединение CompositionBrushes](./composition-brushes.md#combining-compositionbrushes)
-   [С помощью vs кисть XAML. CompositionBrush](./composition-brushes.md#using-a-xaml-brush-vs-compositionbrush)
-   [Связанные разделы](./composition-brushes.md#related-topics)

## <a name="prerequisites"></a>Предварительные требования
Материал данного раздела предполагает, что вы знакомы со структурой базового приложения композиции, которая описана в разделе [Обзор визуального уровня](visual-layer.md).

## <a name="paint-with-a-compositionbrush"></a>Рисование с помощью CompositionBrush

[CompositionBrush](https://docs.microsoft.com/uwp/api/Windows.UI.Composition.CompositionBrush) заполняет пространство своими выводимыми данными. Разные кисти имеют различные типы выводимых данных. Некоторые кисти заполняют область сплошным цветом, другие — градиентом, изображением, собственным рисунком или эффектом. Существуют также специальные кисти, которые изменяют поведение других кистей. Например, маску непрозрачности можно использовать для управления тем, на какую область воздействует CompositionBrush, а сетку из девяти сегментов можно использовать для управления растягиванием, применяемым к CompositionBrush при рисовании в какой-либо области. CompositionBrush может относиться к одному из следующих типов:

|Класс                                   |Подробности                                         |Представлено в|
|-------------------------------------|---------------------------------------------------------|--------------------------------------|
|[CompositionColorBrush](https://docs.microsoft.com/uwp/api/Windows.UI.Composition.CompositionColorBrush)         |Заполняет область сплошным цветом                        |Windows 10 версии 1511 (SDK 10586)|
|[CompositionSurfaceBrush](https://docs.microsoft.com/uwp/api/Windows.UI.Composition.CompositionSurfaceBrush)       |Заполняет область содержимым [ICompositionSurface](https://docs.microsoft.com/en-us/uwp/api/Windows.UI.Composition.ICompositionSurface)|Windows 10 версии 1511 (SDK 10586)|
|[CompositionEffectBrush](https://docs.microsoft.com/uwp/api/Windows.UI.Composition.CompositionEffectBrush)        |Заполняет область содержимым эффекта композиции |Windows 10 версии 1511 (SDK 10586)|
|[CompositionMaskBrush](https://docs.microsoft.com/uwp/api/Windows.UI.Composition.CompositionMaskBrush)          |Изображает визуальный элемент с помощью CompositionBrush с маской непрозрачности |Windows 10 версии 1607 (SDK 14393)
|[CompositionNineGridBrush](https://docs.microsoft.com/uwp/api/Windows.UI.Composition.CompositionNineGridBrush)      |Заполняет область с помощью CompositionBrush с использованием растягивания NineGrid |Windows 10 версии 1607 (SDK 14393)
|[CompositionLinearGradientBrush](https://docs.microsoft.com/uwp/api/windows.ui.composition.compositionlineargradientbrush)|Заполняет область линейным градиентом                    |Windows 10 версии 1709 (SDK 16299)
|[CompositionRadialGradientBrush](https://docs.microsoft.com/uwp/api/windows.ui.composition.compositionradialgradientbrush)|Закрашивает область с применением радиального градиента                    |Windows 10, версия 1903 года (пакет SDK для предварительной версии программы предварительной оценки)
|[CompositionBackdropBrush](https://docs.microsoft.com/uwp/api/Windows.UI.Composition.CompositionBackdropBrush)     |Заполняет область, производя выборку фоновых пикселей либо из приложения, либо из пикселей непосредственно за окном приложения на рабочем столе. Используется в качестве входных данных для другого инструмента CompositionBrush, например CompositionEffectBrush | Windows 10 версии 1607 (SDK 14393)

### <a name="paint-with-a-solid-color"></a>Рисование сплошным цветом

[CompositionColorBrush](https://docs.microsoft.com/uwp/api/Windows.UI.Composition.CompositionColorBrush) закрашивает область сплошным цветом. Существует множество способов задать цвет SolidColorBrush. Например, можно указать значения его каналов альфа, красного, синего и зеленого (ARGB) либо использовать один из предопределенных цветов, предоставляемых классом [Colors](https://docs.microsoft.com/uwp/api/windows.ui.colors).

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

[CompositionLinearGradientBrush](https://docs.microsoft.com/uwp/api/windows.ui.composition.compositionlineargradientbrush) закрашивает область линейным градиентом. Линейный градиент смешивает два или более цветов вдоль линии, то есть оси градиента. Объекты GradientStop используются для указания цветов в градиенте и их положения.

На иллюстрации и в коде ниже показан объект SpriteVisual, закрашенный с помощью LinearGradientBrush с 2 остановками с красным и желтым цветами.

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

### <a name="paint-with-a-radial-gradient"></a>Рисование с применением радиального градиента

Объект [CompositionRadialGradientBrush](/uwp/api/windows.ui.composition.compositionradialgradientbrush) закрашивает область с применением радиального градиента. Радиальный градиент сочетает несколько цветов с градиентом, начиная с центра эллипса и заканчивая радиус эллипса. GradientStop-объекты используются для определения цвета и их расположение в градиенте.

Следующие иллюстрации и код показывает SpriteVisual, закрашивается с использованием RadialGradientBrush с 2 GradientStops.

![CompositionRadialGradientBrush](images/radial-gradient-brush.png)

```cs
Compositor _compositor;
SpriteVisual _gradientVisual;
CompositionRadialGradientBrush RGBrush;

_compositor = Window.Current.Compositor;

RGBrush = _compositor.CreateRadialGradientBrush();
RGBrush.ColorStops.Add(_compositor.CreateColorGradientStop(0, Colors.Aquamarine));
RGBrush.ColorStops.Add(_compositor.CreateColorGradientStop(1, Colors.DeepPink));
_gradientVisual = _compositor.CreateSpriteVisual();
_gradientVisual.Brush = RGBrush;
_gradientVisual.Size = new Vector2(200, 200);
```

### <a name="paint-with-an-image"></a>Рисование с помощью изображения

[CompositionSurfaceBrush](https://docs.microsoft.com/uwp/api/Windows.UI.Composition.CompositionSurfaceBrush) заполняет область пикселями, отображаемыми на ICompositionSurface. Например, можно использовать CompositionSurfaceBrush для заполнения области изображением, отображаемым на поверхности ICompositionSurface surface с помощью API [LoadedImageSurface](https://docs.microsoft.com/uwp/api/windows.ui.xaml.media.loadedimagesurface).

На рисунке и в коде ниже показан объект SpriteVisual, заполненный растровым изображением лакричных конфет, отрисованным на поверхности ICompositionSurface с помощью LoadedImageSurface. Можно использовать свойства CompositionSurfaceBrush, чтобы растянуть и выровнять точечный рисунок в границах визуального объекта.

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

### <a name="paint-with-a-custom-drawing"></a>Рисование с помощью пользовательской отрисовки
[CompositionSurfaceBrush](https://docs.microsoft.com/uwp/api/Windows.UI.Composition.CompositionSurfaceBrush) также может использоваться для заполнения области пикселями из ICompositionSurface, отрисованными с использованием [Win2D](https://microsoft.github.io/Win2D/html/Introduction.htm) (или D2D).

В коде ниже показан объект SpriteVisual, заполненный фрагментом текста, отрисованным на ICompositionSurface с помощью Win2D. Обратите внимание, что для использования Win2D необходимо включить пакет [Win2D NuGet](https://www.nuget.org/packages/Win2D.uwp) в свой проект.

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

Аналогичным образом CompositionSurfaceBrush также может использоваться для рисования SpriteVisual с помощью SwapChain с использованием взаимодействия Win2D. [В этом коде](https://github.com/Microsoft/Win2D-Samples/tree/master/CompositionExample) приведен пример того, как можно использовать Win2D для рисования SpriteVisual с помощью цепочки буферов.

### <a name="paint-with-a-video"></a>Рисование с помощью видео
[CompositionSurfaceBrush](https://docs.microsoft.com/uwp/api/Windows.UI.Composition.CompositionSurfaceBrush) также может использоваться для заполнения области пикселями из ICompositionSurface, отрисованными с использованием видео, загруженного с помощью класса [MediaPlayer](https://docs.microsoft.com/en-us/uwp/api/Windows.Media.Playback.MediaPlayer).

В коде ниже показан объект SpriteVisual, заполненный фрагментом видео, загруженным на ICompositionSurface.

```cs
Compositor _compositor;
SpriteVisual _videoVisual;
CompositionSurfaceBrush _videoBrush;

// MediaPlayer set up with a create from URI

_mediaPlayer = new MediaPlayer();

// Get a source from a URI. This could also be from a file via a picker or a stream
var source = MediaSource.CreateFromUri(new Uri("https://go.microsoft.com/fwlink/?LinkID=809007&clcid=0x409"));
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

### <a name="paint-with-a-filter-effect"></a>Рисование с использованием эффекта фильтра

[CompositionEffectBrush](https://docs.microsoft.com/uwp/api/Windows.UI.Composition.CompositionEffectBrush) заполняет область выводимыми данными CompositionEffect. Эффекты на визуальном уровне могут рассматриваться как анимируемые эффекты фильтров, применяемые к коллекции исходного содержимого, например цветов, градиентов, изображений, видео, цепочек буферов, областей пользовательского интерфейса или деревьев визуальных элементов. Исходное содержимое обычно указывается с помощью другой CompositionBrush.

На иллюстрации и в коде ниже показан объект SpriteVisual, заполненный изображением кошка с примененным эффектом фильтра уменьшения насыщенности.

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

Дополнительные сведения о создании эффектов с помощью CompositionBrushes см. в разделе [Эффекты на визуальном уровне](https://docs.microsoft.com/en-us/windows/uwp/composition/composition-effects)

### <a name="paint-with-a-compositionbrush-with-opacity-mask-applied"></a>Рисование с помощью CompositionBrush с примененной маской непрозрачности

[CompositionMaskBrush](https://docs.microsoft.com/uwp/api/Windows.UI.Composition.CompositionMaskBrush) заполняет область содержимым CompositionBrush с примененной к нему маской непрозрачности. Источником маски непрозрачности может быть любая CompositionBrush типа CompositionColorBrush, CompositionLinearGradientBrush, CompositionSurfaceBrush, CompositionEffectBrush или CompositionNineGridBrush. Маска непрозрачности должна быть указана как CompositionSurfaceBrush.

На иллюстрации и в коде ниже показан объект SpriteVisual, закрашенный с помощью CompositionMaskBrush. Источником маски является кисть CompositionLinearGradientBrush, которая использует маску для отображения окружности, и в качестве маски используется изображение окружности.

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

### <a name="paint-with-a-compositionbrush-using-ninegrid-stretch"></a>Рисование с помощью CompositionBrush с использованием растягивания NineGrid

[CompositionNineGridBrush](https://docs.microsoft.com/uwp/api/Windows.UI.Composition.CompositionNineGridBrush) заполняет область с помощью кисти CompositionBrush, которая растянута с использованием модели сетки из девяти сегментов. Модель сетки из 9 сегментов позволяет растягивать края и углы изображения CompositionBrush иначе, чем его центр. Источником растягиваемой сетки из девяти сегментов может быть любая CompositionBrush типа CompositionColorBrush, CompositionSurfaceBrush или CompositionEffectBrush.

В коде ниже показан объект SpriteVisual, закрашенный с помощью CompositionNineGridBrush. Источником маски является кисть CompositionSurfaceBrush, которая растянута с помощью сетки из девяти сегментов.

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

### <a name="paint-using-background-pixels"></a>Рисование с помощью фоновых пикселей

[CompositionBackdropBrush](https://docs.microsoft.com/uwp/api/Windows.UI.Composition.CompositionBackdropBrush) заполняет область содержимым, расположенным позади этой области. CompositionBackdropBrush никогда не используется самостоятельно, но вместо этого используется в качестве входных данных для другой CompositionBrush, например EffectBrush. Например, используя CompositionBackdropBrush в качестве входных данных для эффекта размытия, можно добиться эффекта матированного стекла.

В следующем коде показано небольшое дерева визуальных объектов для создания изображения с использованием CompositionSurfaceBrush и слоя матированного стекла над изображением. Слой матированного стекла создается путем размещения объекта SpriteVisual, заполненного с использованием EffectBrush, над изображением. EffectBrush использует CompositionBackdropBrush в качестве входных данных для эффекта размытия.

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
Ряд кистей CompositionBrushes может использовать другие CompositionBrushes в качестве входных данных. Например, с помощью метода SetSourceParameter можно задать другую кисть CompositionBrush в качестве входных данных для CompositionEffectBrush. В таблице ниже описаны поддерживаемые сочетания CompositionBrushes. Обратите внимание, что при использовании неподдерживаемого сочетания будет вызвано исключение.

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
<td>НЕТ</td>
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
<td>НЕТ</td>
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
<td>НЕТ</td>
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


## <a name="using-a-xaml-brush-vs-compositionbrush"></a>С помощью vs кисть XAML. CompositionBrush

Ниже приводится список сценариев и указано, какую из двух кистей — XAML или Composition — рекомендуется использовать для рисования UIElement или SpriteVisual в приложении. 

> [!NOTE]
> Если CompositionBrush предлагается для UIElement на XAML, предполагается, что CompositionBrush упакована с помощью XamlCompositionBrushBase.

|Сценарий                                                                   | UIElement на XAML                                                                                                |Composition SpriteVisual
|---------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------|----------------------------------
|Заполнение области сплошным цветом                                             |[SolidColorBrush](https://msdn.microsoft.com/library/windows/apps/BR242962)                                |[CompositionColorBrush](https://msdn.microsoft.com/library/windows/apps/Mt589399)
|Заполнение области анимированным цветом                                          |[SolidColorBrush](https://msdn.microsoft.com/library/windows/apps/BR242962)                                |[CompositionColorBrush](https://msdn.microsoft.com/library/windows/apps/Mt589399)
|Заполнение области статичным градиентом                                       |[LinearGradientBrush](https://msdn.microsoft.com/library/windows/apps/BR210108)                            |[CompositionLinearGradientBrush](https://docs.microsoft.com/uwp/api/windows.ui.composition.compositionlineargradientbrush)
|Заполнение области анимированными ограничениями градиента                                 |[CompositionLinearGradientBrush](https://docs.microsoft.com/uwp/api/windows.ui.composition.compositionlineargradientbrush)                                                                                 |[CompositionLinearGradientBrush](https://docs.microsoft.com/uwp/api/windows.ui.composition.compositionlineargradientbrush)
|Заполнение области изображением                                                |[ImageBrush](https://msdn.microsoft.com/library/windows/apps/BR210101)                                     |[CompositionSurfaceBrush](https://msdn.microsoft.com/library/windows/apps/Mt589415)
|Заполнение области веб-страницей                                               |[WebViewBrush](https://msdn.microsoft.com/library/windows/apps/BR227703)                                   |Н/Д
|Заполнение области изображением с использованием растягивания NineGrid                         |[Элемент управления изображения](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Image)                   |[CompositionNineGridBrush](https://docs.microsoft.com/uwp/api/Windows.UI.Composition.CompositionNineGridBrush)
|Заполнение области анимацией с использованием растягивания NineGrid                               |[CompositionNineGridBrush](https://docs.microsoft.com/uwp/api/Windows.UI.Composition.CompositionNineGridBrush)                                                                                       |[CompositionNineGridBrush](https://docs.microsoft.com/uwp/api/Windows.UI.Composition.CompositionNineGridBrush)
|Заполнение области цепочкой буферов                                             |[SwapChainPanel](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.SwapChainPanel)                                                                                                 |[CompositionSurfaceBrush](https://msdn.microsoft.com/library/windows/apps/Mt589415) со взаимодействием с цепочкой буферов
|Заполнение области видеосодержимым                                                 |[MediaElement](https://msdn.microsoft.com/library/windows/apps/mt187272.aspx)                                                                                                  |[CompositionSurfaceBrush](https://msdn.microsoft.com/library/windows/apps/Mt589415) со взаимодействием с медиа
|Заполнение области пользовательским двумерным рисунком                                       |[CanvasControl](https://microsoft.github.io/Win2D/html/T_Microsoft_Graphics_Canvas_UI_Xaml_CanvasControl.htm) из Win2D                                                                                                 |[CompositionSurfaceBrush](https://msdn.microsoft.com/library/windows/apps/Mt589415) со взаимодействием с Win2D
|Заполнение области неанимированной маской                                       |Используйте [фигуры](https://docs.microsoft.com/windows/uwp/graphics/drawing-shapes) XAML для определения маски   |[CompositionMaskBrush](https://docs.microsoft.com/uwp/api/Windows.UI.Composition.CompositionMaskBrush)
|Заполнение области анимированной маской                                        |[CompositionMaskBrush](https://docs.microsoft.com/uwp/api/Windows.UI.Composition.CompositionMaskBrush)                                                                                           |[CompositionMaskBrush](https://docs.microsoft.com/uwp/api/Windows.UI.Composition.CompositionMaskBrush)
|Заполнение области анимированным эффектом фильтра                               |[CompositionEffectBrush](https://docs.microsoft.com/uwp/api/Windows.UI.Composition.CompositionEffectBrush)                                                                                         |[CompositionEffectBrush](https://docs.microsoft.com/uwp/api/Windows.UI.Composition.CompositionEffectBrush)
|Заполнение области эффектом, примененным к фоновым пикселям        |[CompositionBackdropBrush](https://docs.microsoft.com/uwp/api/Windows.UI.Composition.CompositionBackdropBrush)                                                                                        |[CompositionBackdropBrush](https://docs.microsoft.com/uwp/api/Windows.UI.Composition.CompositionBackdropBrush)

## <a name="related-topics"></a>Связанные разделы

[Композиции собственного DirectX и Direct2D взаимодействия с BeginDraw и EndDraw](composition-native-interop.md)

[Взаимодействие XAML кисти с XamlCompositionBrushBase](/windows/uwp/design/style/brushes#xamlcompositionbrushbase)

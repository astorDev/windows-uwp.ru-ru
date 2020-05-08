---
ms.assetid: 02141F86-355E-4046-86EA-2A89D615B7DB
title: Использование кистей
description: Объекты Brush используются для окраски внутренних областей или контуров фигур, текста и частей элементов управления, чтобы окрашиваемый объект отображался в пользовательском интерфейсе.
ms.date: 04/28/2020
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: 350b07e96d95b043116f2eb8029a2352360c50d6
ms.sourcegitcommit: 490b563462853f10f14825f2358e4852ee1011fb
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/01/2020
ms.locfileid: "82633004"
---
# <a name="using-brushes-to-paint-backgrounds-foregrounds-and-outlines"></a>Использование кистей для рисования фона, переднего плана и контуров

Используйте объекты [**Кисть**](/uwp/api/Windows.UI.Xaml.Media.Brush) для рисования внутреннего наполнения и контуров фигур XAML, текста и частей элементов управления, чтобы они были видны в пользовательском интерфейсе приложения.

> **Важные API**:  [класс Brush](/uwp/api/Windows.UI.Xaml.Media.Brush).

## <a name="introduction-to-brushes"></a>Знакомство с кистями

Чтобы закрасить [**фигуру**](/uwp/api/Windows.UI.Xaml.Shapes.Shape), текст или части [**элемента управления**](/uwp/api/Windows.UI.Xaml.Controls.Control), отображаемые на полотне приложения, задайте для объекта **фигуры** свойство [**Заливка**](/uwp/api/windows.ui.xaml.shapes.shape.fill) или для свойства **элемента управления** [**Фон**](/uwp/api/windows.ui.xaml.controls.control.background) и [**Передний план**](/uwp/api/windows.ui.xaml.controls.control.foreground) значение **Кисть**.

Существуют различные типы кистей: 
-   [**AcrylicBrush**](/uwp/api/windows.ui.xaml.media.acrylicbrush);
-   [**SolidColorBrush**](/uwp/api/Windows.UI.Xaml.Media.SolidColorBrush);
-   [**LinearGradientBrush**](/uwp/api/Windows.UI.Xaml.Media.LinearGradientBrush); 
-   [**RadialGradientBrush**](/uwp/api/microsoft.ui.xaml.media.radialgradientbrush); 
-   [**ImageBrush**](/uwp/api/Windows.UI.Xaml.Media.ImageBrush);
-   [**WebViewBrush**](/uwp/api/Windows.UI.Xaml.Controls.WebViewBrush);
-   [**XamlCompositionBrushBase**](/uwp/api/windows.ui.xaml.media.xamlcompositionbrushbase).

## <a name="solid-color-brushes"></a>Кисти сплошной заливки

Объект [**SolidColorBrush**](/uwp/api/Windows.UI.Xaml.Media.SolidColorBrush) закрашивает область одним цветом ([**Color**](/uwp/api/Windows.UI.Color)), например красным или синим. Это самая простая кисть. Объект **SolidColorBrush** и его цвет можно задать в XAML тремя способами: стандартным названием цвета, шестнадцатеричным значением цвета или в синтаксисе свойств.

### <a name="predefined-color-names"></a>Стандартные имена цветов

Можно использовать стандартное имя цвета, например [**Yellow**](/uwp/api/windows.ui.colors.yellow) или [**Magenta**](/uwp/api/windows.ui.colors.magenta). Доступно 256 цветов с именами. Средство синтаксического анализа XAML преобразует имя цвета в структуру [**Color**](/uwp/api/Windows.UI.Color) с правильными цветовыми каналами. 256 именованных цветов основаны на именах цветов *X11* из каскадных таблиц стилей версии 3 (CSS3). Если у вас есть опыт веб-разработки или дизайна, возможно, вы уже знакомы со списком именованных цветов.

В следующем примере в свойстве [**Fill**](/uwp/api/windows.ui.xaml.shapes.shape.fill) объекта [**Rectangle**](/uwp/api/Windows.UI.Xaml.Shapes.Rectangle) задается стандартный цвет [**Red**](/uwp/api/windows.ui.colors.red).

```xaml
<Rectangle Width="100" Height="100" Fill="Red" />
```

![Отображение SolidColorBrush](images/brushes-solidcolorbrush.jpg)

*Объект SolidColorBrush, примененный к прямоугольнику*

При определении [**SolidColorBrush**](/uwp/api/Windows.UI.Xaml.Media.SolidColorBrush) с помощью кода вместо XAML каждый именованный цвет доступен в виде статического значения свойства класса [**Colors**](https://docs.microsoft.com/uwp/api/windows.ui.colors). Например, чтобы объявить значение свойства [**Color**](/uwp/api/windows.ui.xaml.media.solidcolorbrush.color) объекта **SolidColorBrush** для представления именованного цвета Orchid, задайте в качестве значения **Color** статическое значение [**Colors.Orchid**](/uwp/api/windows.ui.colors.orchid).

### <a name="hexadecimal-color-values"></a>Шестнадцатеричные значения цвета

Можно использовать строку в шестнадцатеричном формате для объявления точных 24-битных значений цвета с 8-битным альфа-каналом для [**SolidColorBrush**](/uwp/api/Windows.UI.Xaml.Media.SolidColorBrush). Два символа в диапазоне от 0 до F определяют значение каждого из компонентов. Порядок значений компонентов в шестнадцатеричной строке следующий: альфа-канал (непрозрачность), красный, зеленый и синий каналы (**ARGB**). Например, шестнадцатеричное значение \#FFFF0000 задает полностью непрозрачный красный (альфа=FF, красный=FF, зеленый=00 и синий=00).

В данном примере XAML для свойства [**Fill**](/uwp/api/windows.ui.xaml.shapes.shape.fill) объекта [**Rectangle**](/uwp/api/Windows.UI.Xaml.Shapes.Rectangle) задается шестнадцатеричное значение \#FFFF0000 и достигается результат, аналогичный именованному цвету [**Colors.Red**](/uwp/api/windows.ui.colors.red).

```xml
<StackPanel>
  <Rectangle Width="100" Height="100" Fill="#FFFF0000" />
</StackPanel>
```

### <a name="property-element-syntax"></a>Синтаксис свойств

Объект [**SolidColorBrush**](/uwp/api/Windows.UI.Xaml.Media.SolidColorBrush) можно определить в синтаксисе элементов свойств. Этот способ не такой компактный, как предыдущие, но позволяет задавать дополнительные значения свойств для элемента, например [**Opacity**](/uwp/api/windows.ui.xaml.media.brush.opacity). Подробнее о синтаксисе XAML, в том числе синтаксисе элементов свойств, можно узнать в статьях [Обзор языка XAML](https://docs.microsoft.com/windows/uwp/xaml-platform/xaml-overview) и [Руководство по основам синтаксиса языка XAML](https://docs.microsoft.com/windows/uwp/xaml-platform/xaml-syntax-guide).

В предыдущих примерах создаваемая кисть создается неявно и автоматически как часть произвольного условного обозначения на языке XAML, что помогает сохранять простоту определения пользовательского интерфейса в большинстве случаев. В следующем примере создается [**Rectangle**](/uwp/api/Windows.UI.Xaml.Shapes.Rectangle), а затем явно создается [**SolidColorBrush**](/uwp/api/Windows.UI.Xaml.Media.SolidColorBrush) как значение элемента для свойства [**Rectangle.Fill**](/uwp/api/windows.ui.xaml.shapes.shape.fill). Свойство [**Color**](/uwp/api/windows.ui.xaml.media.solidcolorbrush.color) объекта **SolidColorBrush** получает значение [**Blue**](/uwp/api/windows.ui.colors.blue), а свойство [**Opacity**](/uwp/api/windows.ui.xaml.media.brush.opacity) — значение 0,5.

```xml
<Rectangle Width="200" Height="150">
    <Rectangle.Fill>
        <SolidColorBrush Color="Blue" Opacity="0.5" />
    </Rectangle.Fill>
</Rectangle>
```

## <a name="linear-gradient-brushes"></a>Кисти линейного градиента

Объект [**LinearGradientBrush**](/uwp/api/Windows.UI.Xaml.Media.LinearGradientBrush) закрашивает область градиентом, который определяется вдоль линии. Эта линия называется *осью градиента*. Цвета градиента и их расположение по оси градиента задаются с помощью объектов [**GradientStop**](/uwp/api/Windows.UI.Xaml.Media.GradientStop). По умолчанию ось градиента проходит из левого верхнего в правый нижний угол области, закрашиваемой кистью, в результате чего получается диагональное затенение.

Градиентная кисть состоит из стандартных элементов [**GradientStop**](/uwp/api/Windows.UI.Xaml.Media.GradientStop). Ограничение градиента указывает, какое значение [**Color**](/uwp/api/windows.ui.xaml.media.gradientstop.color) для кисти устанавливается в позиции [**Offset**](/uwp/api/windows.ui.xaml.media.gradientstop.offset) вдоль оси градиента при применении кисти к закрашиваемой области.

Цвет ограничения градиента задается в его свойстве [**Color**](/uwp/api/windows.ui.xaml.media.gradientstop.color). Цвет можно задать, используя стандартное имя или шестнадцатеричное значение **ARGB**.

Свойство [**Offset**](/uwp/api/windows.ui.xaml.media.gradientstop.offset) объекта [**GradientStop**](/uwp/api/Windows.UI.Xaml.Media.GradientStop) задает положение каждого **GradientStop** вдоль оси градиента. Значение **Offset** имеет тип **double** и находится в диапазоне от 0 до 1. Значение **Offset**, равное 0, помещает **GradientStop** в начало оси градиента, иными словами, вблизи его начальной точки [**StartPoint**](/uwp/api/windows.ui.xaml.media.lineargradientbrush.startpoint). Значение **Offset**, равное 1, помещает **GradientStop** в конечную точку [**EndPoint**](/uwp/api/windows.ui.xaml.media.lineargradientbrush.endpoint). Объект [**LinearGradientBrush**](/uwp/api/Windows.UI.Xaml.Media.LinearGradientBrush) должен иметь по меньшей мере два значения **GradientStop**, где каждый элемент **GradientStop** определяет разные свойства [**Color**](/uwp/api/windows.ui.xaml.media.gradientstop.color) и имеет разные значения **Offset** между 0 и 1.

В следующем примере создается линейный градиент из четырех цветов, а затем им закрашивается объект [**Rectangle**](/uwp/api/Windows.UI.Xaml.Shapes.Rectangle).

```xml
<!-- This rectangle is painted with a diagonal linear gradient. -->
<Rectangle Width="200" Height="100">
    <Rectangle.Fill>
        <LinearGradientBrush StartPoint="0,0" EndPoint="1,1">
            <GradientStop Color="Yellow" Offset="0.0" x:Name="GradientStop1"/>
            <GradientStop Color="Red" Offset="0.25" x:Name="GradientStop2"/>
            <GradientStop Color="Blue" Offset="0.75" x:Name="GradientStop3"/>
            <GradientStop Color="LimeGreen" Offset="1.0" x:Name="GradientStop4"/>
        </LinearGradientBrush>
    </Rectangle.Fill>
</Rectangle>
```

Цвет каждой точки между ограничениями градиента определяется линейной интерполяцией сочетания цветов, заданных соседними позициями градиента. На рисунке ниже отмечены позиции градиента из предыдущего примера. Положение позиций градиента отмечено кружками, а ось градиента показана пунктирной линией.

![Позиции градиента](images/linear-gradients-stops.png)

*Сочетание цветов, заданных двумя соседними позициями градиента*

Можно изменить линию, по которой определяются позиции ограничений градиента, установив для свойств [**StartPoint**](/uwp/api/windows.ui.xaml.media.lineargradientbrush.startpoint) и [**EndPoint**](/uwp/api/windows.ui.xaml.media.lineargradientbrush.endpoint) значения, отличающиеся от принятых по умолчанию `(0,0)` и `(1,1)`. Изменяя значения координат **StartPoint** и **EndPoint**, можно создать горизонтальные и вертикальные градиенты, обратить направление градиента или сжать охват градиента, применив его к меньшему диапазону, чем полная закрашенная область. Чтобы сжать градиент, следует задать значения **StartPoint** и (или) **EndPoint**, отличные от 0 и 1. Например, если требуется горизонтальный градиент, где затухание цвета происходит на левой половине кисти, а с правой стороны используется сплошной цвет последнего [**GradientStop**](/uwp/api/Windows.UI.Xaml.Media.GradientStop), следует указать для **StartPoint** значение `(0,0)`, а для **EndPoint** — значение `(0.5,0)`.

### <a name="use-tools-to-make-gradients"></a>Использование средств для создания градиента

Теперь, когда вы знаете, как работают линейные градиенты, вам будет легче создавать их с помощью Visual Studio или Blend. Чтобы создать градиент, выделите в рабочей области конструирования или в представлении XAML объект, к которому нужно применить градиент. Разверните раздел **Кисть** и выберите вкладку **Линейный градиент**.

![Создание линейного градиента с помощью Visual Studio](images/tool-gradient-brush-1.png)

*Создание линейного градиента с помощью Visual Studio*

Теперь вы можете изменять цвета позиций градиента и перемещать их помощью панели в нижней части экрана. Кроме того, вы можете добавить новые позиции градиента, щелкнув на панели и удалив их методом перетаскивания с панели (см. снимок экрана ниже).

![Панель в нижней части окна свойств, управляющая позициями градиента](images/tool-gradient-brush-2.png)

*Ползунок параметра градиента*

## <a name="radial-gradient-brushes"></a>Кисти радиального градиента

Объект [**RadialGradientBrush**](/uwp/api/microsoft.ui.xaml.media.radialgradientbrush) заполняет область изображением внутри эллипса, который определяется свойствами [**Center**](/uwp/api/microsoft.ui.xaml.media.radialgradientbrush.center), [**RadiusX**](/uwp/api/microsoft.ui.xaml.media.radialgradientbrush.radiusx) и [**RadiusY**](/uwp/api/microsoft.ui.xaml.media.radialgradientbrush.radiusy). Начальный цвет градиента определяется в центре эллипса, а конечный — в конце радиуса.

Цвета радиального градиента определяются ограничениями цвета, которые добавляются в свойство коллекции [**GradientStops**](/uwp/api/microsoft.ui.xaml.media.radialgradientbrush.gradientstops). Каждая позиция градиента задает цвет и смещение вдоль градиента.

Начальная точка градиента по умолчанию принимает значение центральной точки и может быть смещена с помощью свойства [**GradientOrigin**](/uwp/api/microsoft.ui.xaml.media.radialgradientbrush.gradientorigin).

[MappingMode](/uwp/api/microsoft.ui.xaml.media.radialgradientbrush.mappingmode) определяет, представляют ли свойства [**Center**](/uwp/api/microsoft.ui.xaml.media.radialgradientbrush.center), [**RadiusX**](/uwp/api/microsoft.ui.xaml.media.radialgradientbrush.radiusx), [**RadiusY**](/uwp/api/microsoft.ui.xaml.media.radialgradientbrush.radiusy) и [**GradientOrigin**](/uwp/api/microsoft.ui.xaml.media.radialgradientbrush.gradientorigin) относительные или абсолютные координаты.

Если [**MappingMode**](/uwp/api/microsoft.ui.xaml.media.radialgradientbrush.mappingmode) имеет значение `RelativeToBoundingBox`, значения X и Y этих трех свойств обрабатываются как значения, которые относятся к границам элементов, где `(0,0)` представляет верхние левые, а `(1,1)` — нижние правые границы элементов для свойств [**Center**](/uwp/api/microsoft.ui.xaml.media.radialgradientbrush.center), [**RadiusX**](/uwp/api/microsoft.ui.xaml.media.radialgradientbrush.radiusx) и [**RadiusY**](/uwp/api/microsoft.ui.xaml.media.radialgradientbrush.radiusy), а `(0,0)` представляет центральную точку свойства [**GradientOrigin**](/uwp/api/microsoft.ui.xaml.media.radialgradientbrush.gradientorigin).

Если [**MappingMode**](/uwp/api/microsoft.ui.xaml.media.radialgradientbrush.mappingmode) имеет значение `Absolute`, то значения X и Y этих трех свойств обрабатываются как абсолютные координаты в пределах границ элемента.

В следующем примере создается линейный градиент из четырех цветов, а затем им закрашивается объект [**Rectangle**](/uwp/api/Windows.UI.Xaml.Shapes.Rectangle).

```xml
<!-- This rectangle is painted with a radial gradient. -->
<Rectangle Width="200" Height="200">
    <Rectangle.Fill>
        <media:RadialGradientBrush>
            <GradientStop Color="Blue" Offset="0.0" />
            <GradientStop Color="Yellow" Offset="0.2" />
            <GradientStop Color="LimeGreen" Offset="0.4" />
            <GradientStop Color="LightBlue" Offset="0.6" />
            <GradientStop Color="Blue" Offset="0.8" />
            <GradientStop Color="LightGray" Offset="1" />
        </media:RadialGradientBrush>
    </Rectangle.Fill>
</Rectangle>
```

Цвет каждой точки между позициями градиента определяется радиальной интерполяцией сочетания цветов, заданных двумя соседними позициями градиента. На рисунке ниже отмечены позиции градиента из предыдущего примера. 

![Позиции градиента](images/radial-gradient.png)

*Ограничения градиента*

## <a name="image-brushes"></a>Кисти изображений

Объект [**ImageBrush**](/uwp/api/Windows.UI.Xaml.Media.ImageBrush) заполняет область изображением, которое поступает из файла изображения. В свойстве [**ImageSource**](/uwp/api/Windows.UI.Xaml.Media.ImageSource) задается путь к загружаемому изображению. Обычно источник изображения поступает из элемента **Content**, являющегося частью ресурсов вашего приложения.

По умолчанию [**ImageBrush**](/uwp/api/Windows.UI.Xaml.Media.ImageBrush) растягивает изображение так, чтобы полностью закрыть закрашиваемую область. При этом изображение может искажаться, если пропорции области отличаются от пропорций изображения. Чтобы исключить такое искажение, измените в свойстве [**Stretch**](/uwp/api/windows.ui.xaml.media.tilebrush.stretch) значение по умолчанию **Fill** на **None**, **Uniform** или **UniformToFill**.

В следующем примере создается объект [**ImageBrush**](/uwp/api/Windows.UI.Xaml.Media.ImageBrush), а в свойстве [**ImageSource**](/uwp/api/Windows.UI.Xaml.Media.ImageSource) задается изображение с именем licorice.jpg, которое необходимо включить в приложение в качестве ресурса. Затем **ImageBrush** закрашивает область, определенную формой [**Ellipse**](/uwp/api/Windows.UI.Xaml.Shapes.Ellipse).

```xml
<Ellipse Height="200" Width="300">
   <Ellipse.Fill>
     <ImageBrush ImageSource="licorice.jpg" />
   </Ellipse.Fill>
</Ellipse>
```

![Отображение ImageBrush.](images/brushes-imagebrush.jpg)

*Отображение ImageBrush*

[**ImageBrush**](/uwp/api/Windows.UI.Xaml.Media.ImageBrush) и [**Image**](/uwp/api/Windows.UI.Xaml.Controls.Image) ссылаются на исходный файл изображения по универсальному коду ресурса (URI). Для этого файла допустимы несколько графических форматов. Такие исходные файлы изображений указываются в виде URI. См. сведения об определении источников изображений, их возможных форматах и упаковке в приложении в описании [классов Image и ImageBrush](https://docs.microsoft.com/windows/uwp/controls-and-patterns/images-imagebrushes).

## <a name="brushes-and-text"></a>Кисти и текст

С помощью кистей также можно применить характеристики отрисовки к текстовым элементам. Например, свойство [**Foreground**](/uwp/api/windows.ui.xaml.controls.textblock.foreground) объекта [**TextBlock**](/uwp/api/Windows.UI.Xaml.Controls.TextBlock) принимает объект [**Brush**](/uwp/api/Windows.UI.Xaml.Media.Brush). К тексту можно применить любую из описанных здесь кистей. Но кисти следует применять к тексту с осторожностью, так как любой фон может сделать текст нечитаемым, если использовать кисти, сливающиеся с фоном. Для обеспечения читаемости текстовых элементов в большинстве случаев ограничьтесь объектом [**SolidColorBrush**](/uwp/api/Windows.UI.Xaml.Media.SolidColorBrush), если только не нужно намеренно придать декоративность текстовому элементу.

Даже при использовании сплошного цвета убедитесь, что выбранный цвет текста достаточно контрастирует с фоновым цветом контейнера макета текста. Уровень контрастности между основным цветом текста и фоном текстового контейнера влияет на доступность приложения для людей с ограниченными возможностями.

## <a name="webviewbrush"></a>WebViewBrush

Объект [**WebViewBrush**](/uwp/api/Windows.UI.Xaml.Controls.WebViewBrush) — это специальный тип кисти, которому доступно содержимое, обычно просматриваемое в элементе управления [**WebView**](/uwp/api/Windows.UI.Xaml.Controls.WebView). Вместо отрисовки содержимого в прямоугольной области элемента управления **WebView** объект **WebViewBrush** выводит это содержимое поверх другого элемента со свойством типа [**Brush**](/uwp/api/Windows.UI.Xaml.Media.Brush) для поверхности отрисовки. Объект **WebViewBrush** подходит не для каждого сценария использования кисти, но его удобно использовать при переходах **WebView**. Дополнительные сведения см. в статье о [**WebViewBrush**](/uwp/api/Windows.UI.Xaml.Controls.WebViewBrush).

## <a name="xamlcompositionbrushbase"></a>XamlCompositionBrushBase

[**XamlCompositionBrushBase**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.media.xamlcompositionbrushbase) – это базовый класс, который используется для создания настраиваемых кистей, использующих [**CompositionBrush**](https://docs.microsoft.com/uwp/api/Windows.UI.Composition.CompositionBrush) для рисования элементов пользовательского интерфейса XAML.

Это включает "нисходящее" взаимодействие между слоями Windows.UI.Xaml и Windows.UI.Composition, как описано в статье о [**визуальном уровне**](/windows/uwp/composition/visual-layer). 

Чтобы создать настраиваемую кисть, создайте новый класс, производный от XamlCompositionBrushBase и реализующий необходимые методы.

Например, она может использоваться для применения [**эффектов**](/uwp/composition/composition-effects) к элементам пользовательского интерфейса XAML с помощью [**CompositionEffectBrush**](/uwp/api/Windows.UI.Composition.CompositionEffectBrush), например **GaussianBlurEffect** или [**SceneLightingEffect**](/uwp/api/Windows.UI.Composition.Effects.SceneLightingEffect), который контролирует отражающие свойства UIElement XAML при его освещении с помощью [**XamlLight**](/uwp/api/windows.ui.xaml.media.xamllight).

Примеры кода см. в статье о [**XamlCompositionBrushBase**](/uwp/api/windows.ui.xaml.media.xamlcompositionbrushbase).

## <a name="brushes-as-xaml-resources"></a>Кисти как ресурсы XAML

Любую кисть можно объявить ресурсом XAML с ключом в словаре ресурсов XAML. Это облегчает репликацию одинаковых значений кистей при применении ко многим элементам пользовательского интерфейса. Затем значения кистей предоставляются в общий доступ и применяются при обращении к ресурсу кисти в виде [{StaticResource}](/uwp/xaml-platform/staticresource-markup-extension) в языке XAML. Сюда относится и использование шаблона элемента управления XAML, ссылающегося на общую кисть, когда сам этот шаблон элемента управления является ресурсом XAML с ключом.

## <a name="brushes-in-code"></a>Определение кистей в коде

Обычно кисти указываются с помощью XAML, а не определяются в коде. Это связано с тем, что кисти обычно определяются как ресурсы XAML, а значения кистей часто являются результатами работы инструментов проектирования или входят в определение пользовательского интерфейса XAML. Но иногда удобно определить кисть в кода, и все типы [**Brush**](/uwp/api/Windows.UI.Xaml.Media.Brush) будут доступными для создания экземпляра в коде.

Чтобы создать [**SolidColorBrush**](/uwp/api/Windows.UI.Xaml.Media.SolidColorBrush) в коде, используйте конструктор, который принимает параметр [**Color**](/uwp/api/Windows.UI.Color). Передайте значение, которое является статическим свойством класса [**Colors**](/uwp/api/windows.ui.colors), например:

```cs
SolidColorBrush blueBrush = new SolidColorBrush(Windows.UI.Colors.Blue);
```

```vb
Dim blueBrush as SolidColorBrush = New SolidColorBrush(Windows.UI.Colors.Blue)
```

```cppwinrt
Windows::UI::Xaml::Media::SolidColorBrush blueBrush{ Windows::UI::Colors::Blue() };
```

```cpp
blueBrush = ref new SolidColorBrush(Windows::UI::Colors::Blue);
```

Для [**WebViewBrush**](/uwp/api/Windows.UI.Xaml.Controls.WebViewBrush) и [**ImageBrush**](/uwp/api/Windows.UI.Xaml.Media.ImageBrush) используйте конструктор по умолчанию. Затем, прежде чем вы попытаетесь использовать эту кисть для свойства пользовательского интерфейса, вызовите другие API.

-   [**ImageSource**](/uwp/api/windows.ui.xaml.media.imagebrush.imagesourceproperty) требует [**BitmapImage**](/uwp/api/Windows.UI.Xaml.Media.Imaging.BitmapImage) (а не URI), когда вы определяете [**ImageBrush**](/uwp/api/Windows.UI.Xaml.Media.ImageBrush) с помощью кода. Если источником является поток, для инициализации значения используйте метод [**SetSourceAsync**](/uwp/api/windows.ui.xaml.media.imaging.bitmapsource.setsourceasync). Если источником является универсальный код ресурса (URI), который включает в себя используемое в вашем приложении содержимое со схемами **ms-appx** или **ms-resource**, используйте конструктор [**BitmapImage**](/uwp/api/windows.ui.xaml.media.imaging.bitmapimage), который применяет универсальный код ресурса (URI). Также можно обрабатывать события [**ImageOpened**](/uwp/api/windows.ui.xaml.media.imagebrush.imageopened), если существуют проблемы со своевременным получением или декодированием источника изображения, когда вы решите отображать альтернативное содержимое, пока источник изображения не станет доступным.
-   Для [**WebViewBrush**](/uwp/api/Windows.UI.Xaml.Controls.WebViewBrush) вам может понадобиться вызвать [**Redraw**](/uwp/api/windows.ui.xaml.controls.webviewbrush.redraw), если вы недавно сбросили свойство [**SourceName**](/uwp/api/windows.ui.xaml.controls.webviewbrush.sourcename) или если содержимое [**WebView**](/uwp/api/Windows.UI.Xaml.Controls.WebView) также было изменено в коде.

Примеры кода см. в статьях о [**WebViewBrush**](/uwp/api/Windows.UI.Xaml.Controls.WebViewBrush), [**ImageBrush**](/uwp/api/Windows.UI.Xaml.Media.ImageBrush) и [**XamlCompositionBrushBase**](/uwp/api/windows.ui.xaml.media.xamlcompositionbrushbase).
 

 





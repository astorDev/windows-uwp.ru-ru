---
ms.assetid: 02141F86-355E-4046-86EA-2A89D615B7DB
title: Использование кистей
description: Объекты Brush используются для окраски внутренних областей или контуров фигур, текста и частей элементов управления, чтобы окрашиваемый объект отображался в пользовательском интерфейсе.
ms.date: 07/13/2018
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: 20e38b24195bf3e476fa68284813bfd3a8cd2e6c
ms.sourcegitcommit: 26bb75084b9d2d2b4a76d4aa131066e8da716679
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/06/2020
ms.locfileid: "75684176"
---
# <a name="using-brushes-to-paint-backgrounds-foregrounds-and-outlines"></a>Использование кистей для рисования фона, переднего плана и контуров

Объекты [**Brush**](/uwp/api/Windows.UI.Xaml.Media.Brush) применяются для рисования внутреннего наполнения и контуров фигур XAML, текста и частей элементов управления, чтобы рисуемый объект был виден в пользовательском интерфейсе. Так какие же кисти доступны и как ими пользоваться?

> **Важные API**:  [класс Brush](/uwp/api/Windows.UI.Xaml.Media.Brush).

## <a name="introduction-to-brushes"></a>Знакомство с кистями

Для окраски объекта, например [**Shape**](/uwp/api/Windows.UI.Xaml.Shapes.Shape) или частей [**Control**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Control), отображаемых на полотне приложения, используется [**Brush**](/uwp/api/Windows.UI.Xaml.Media.Brush). Например, для свойства [**Fill**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.shapes.shape.fill) объекта **Shape** или [**Background**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.control.background) и свойства [**Foreground**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.control.foreground) объекта **Control** настраивается **Brush**, и этот объект **Brush** определяет, как элемент пользовательского интерфейса окрашивается или отрисовывается в пользовательском интерфейсе. 

Существуют различные типы кистей: 
-   [**AcrylicBrush**](/uwp/api/windows.ui.xaml.media.acrylicbrush);
-   [**SolidColorBrush**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.SolidColorBrush);
-   [**LinearGradientBrush**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.LinearGradientBrush); 
-   [**ImageBrush**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.ImageBrush);
-   [**WebViewBrush**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.WebViewBrush);
-   [**XamlCompositionBrushBase**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.media.xamlcompositionbrushbase).

## <a name="solid-color-brushes"></a>Кисти сплошной заливки

Объект [**SolidColorBrush**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.SolidColorBrush) закрашивает область одним цветом ([**Color**](https://docs.microsoft.com/uwp/api/Windows.UI.Color)), например красным или синим. Это самая простая кисть. Объект **SolidColorBrush** и его сплошной цвет можно задать в XAML тремя способами: стандартным именем цвета, шестнадцатеричным значением цвета или в синтаксисе свойств.

### <a name="predefined-color-names"></a>Стандартные имена цветов

Можно использовать стандартное имя цвета, например [**Yellow**](https://docs.microsoft.com/uwp/api/windows.ui.colors.yellow) или [**Magenta**](https://docs.microsoft.com/uwp/api/windows.ui.colors.magenta). Доступно 256 цветов с именами. Средство синтаксического анализа XAML преобразует имя цвета в структуру [**Color**](https://docs.microsoft.com/uwp/api/Windows.UI.Color) с правильными цветовыми каналами. 256 именованных цветов основаны на именах цветов *X11* из каскадных таблиц стилей версии 3 (CSS3). Если у вас есть опыт веб-разработки или дизайна, возможно, вы уже знакомы со списком именованных цветов.

В следующем примере в свойстве [**Fill**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.shapes.shape.fill) объекта [**Rectangle**](/uwp/api/Windows.UI.Xaml.Shapes.Rectangle) задается стандартный цвет [**Red**](https://docs.microsoft.com/uwp/api/windows.ui.colors.red).

```xml
<Rectangle Width="100" Height="100" Fill="Red" />
```

На этом рисунке показан объект [**SolidColorBrush**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.SolidColorBrush), примененный к [**Rectangle**](/uwp/api/Windows.UI.Xaml.Shapes.Rectangle).

![Отображение SolidColorBrush.](images/brushes-solidcolorbrush.jpg)

При определении [**SolidColorBrush**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.SolidColorBrush) с помощью кода вместо XAML каждый именованный цвет доступен в виде статического значения свойства класса [**Colors**](https://docs.microsoft.com/uwp/api/windows.ui.colors). Например, чтобы объявить значение свойства [**Color**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.media.solidcolorbrush.color) объекта **SolidColorBrush** для представления именованного цвета Orchid, задайте в качестве значения **Color** статическое значение [**Colors.Orchid**](https://docs.microsoft.com/uwp/api/windows.ui.colors.orchid).

### <a name="hexadecimal-color-values"></a>Шестнадцатеричные значения цвета

Можно использовать строку в шестнадцатеричном формате для объявления точных 24-битных значений цвета с 8-битным альфа-каналом для [**SolidColorBrush**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.SolidColorBrush). Два символа в диапазоне от 0 до F определяют значение каждого из компонентов. Порядок значений компонентов в шестнадцатеричной строке следующий: альфа-канал (непрозрачность), красный, зеленый и синий каналы (**ARGB**). Например, шестнадцатеричное значение \#FFFF0000 задает полностью непрозрачный красный (альфа=FF, красный=FF, зеленый=00 и синий=00).

В данном примере XAML для свойства [**Fill**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.shapes.shape.fill) объекта [**Rectangle**](/uwp/api/Windows.UI.Xaml.Shapes.Rectangle) задается шестнадцатеричное значение \#FFFF0000 и достигается результат, аналогичный именованному цвету [**Colors.Red**](https://docs.microsoft.com/uwp/api/windows.ui.colors.red).

```xml
<StackPanel>
  <Rectangle Width="100" Height="100" Fill="#FFFF0000" />
</StackPanel>
```

### <a name="span-idproperty_element_syntax__spanspan-idproperty_element_syntax__spanspan-idproperty_element_syntax__spanproperty-element-syntax"></a><span id="Property_element_syntax__"></span><span id="property_element_syntax__"></span><span id="PROPERTY_ELEMENT_SYNTAX__"></span>Синтаксис элементов свойств

Объект [**SolidColorBrush**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.SolidColorBrush) можно определить в синтаксисе элементов свойств. Этот способ не такой компактный, как предыдущие, но позволяет задавать дополнительные значения свойств для элемента, например [**Opacity**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.media.brush.opacity). См. сведения о синтаксисе XAML, включая синтаксис элементов свойств, в [описании языка XAML](https://docs.microsoft.com/windows/uwp/xaml-platform/xaml-overview) и [руководстве по основам синтаксиса языка XAML](https://docs.microsoft.com/windows/uwp/xaml-platform/xaml-syntax-guide).

В предыдущих примерах строка SolidColorBrush никогда не появлялась в синтаксисе. Создаваемая кисть создается неявно и автоматически как часть произвольного условного обозначения на языке XAML, что помогает сохранять простоту определения пользовательского интерфейса в большинстве случаев. В следующем примере создается [**Rectangle**](/uwp/api/Windows.UI.Xaml.Shapes.Rectangle), а затем явно создается [**SolidColorBrush**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.SolidColorBrush) как значение элемента для свойства [**Rectangle.Fill**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.shapes.shape.fill). Свойство [**Color**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.media.solidcolorbrush.color) объекта **SolidColorBrush** получает значение [**Blue**](https://docs.microsoft.com/uwp/api/windows.ui.colors.blue), а свойство [**Opacity**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.media.brush.opacity) — значение 0,5.

```xml
<Rectangle Width="200" Height="150">
    <Rectangle.Fill>
        <SolidColorBrush Color="Blue" Opacity="0.5" />
    </Rectangle.Fill>
</Rectangle>
```

## <a name="span-idlinear_gradient_brushes_spanspan-idlinear_gradient_brushes_spanspan-idlinear_gradient_brushes_spanlinear-gradient-brushes"></a><span id="Linear_gradient_brushes_"></span><span id="linear_gradient_brushes_"></span><span id="LINEAR_GRADIENT_BRUSHES_"></span>Кисти линейного градиента

Объект [**LinearGradientBrush**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.LinearGradientBrush) закрашивает область градиентом, который определяется вдоль линии. Эта линия называется *осью градиента*. Цвета градиента и их расположение по оси градиента задаются с помощью объектов [**GradientStop**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.GradientStop). По умолчанию ось градиента проходит из левого верхнего в правый нижний угол области, закрашиваемой кистью, в результате чего получается диагональное затенение.

Градиентная кисть состоит из стандартных элементов [**GradientStop**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.GradientStop). Ограничение градиента указывает, какое значение [**Color**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.media.gradientstop.color) для кисти устанавливается в позиции [**Offset**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.media.gradientstop.offset) вдоль оси градиента при применении кисти к закрашиваемой области.

Цвет ограничения градиента задается в его свойстве [**Color**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.media.gradientstop.color). Цвет можно задать, используя стандартное имя или шестнадцатеричное значение **ARGB**.

Свойство [**Offset**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.media.gradientstop.offset) объекта [**GradientStop**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.GradientStop) задает положение каждого **GradientStop** вдоль оси градиента. Значение **Offset** имеет тип **double** и находится в диапазоне от 0 до 1. Значение **Offset**, равное 0, помещает **GradientStop** в начало оси градиента, иными словами, вблизи его начальной точки [**StartPoint**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.media.lineargradientbrush.startpoint). Значение **Offset**, равное 1, помещает **GradientStop** в конечную точку [**EndPoint**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.media.lineargradientbrush.endpoint). Объект [**LinearGradientBrush**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.LinearGradientBrush) должен иметь по меньшей мере два значения **GradientStop**, где каждый элемент **GradientStop** определяет разные свойства [**Color**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.media.gradientstop.color) и имеет разные значения **Offset** между 0 и 1.

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

Цвет каждой точки между ограничениями градиента определяется линейной интерполяцией сочетания цветов, заданных соседними позициями градиента. На рисунке отмечены позиции градиента из предыдущего примера. Положение позиций градиента отмечено кружками, а ось градиента показана пунктирной линией.

![Ограничения градиента](images/linear-gradients-stops.png) Можно изменить линию, по которой определяются позиции ограничений градиента, установив для свойств [**StartPoint**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.media.lineargradientbrush.startpoint) и [**EndPoint**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.media.lineargradientbrush.endpoint) значения, отличающиеся от принятых по умолчанию `(0,0)` и `(1,1)`. Изменяя значения координат **StartPoint** и **EndPoint**, можно создать горизонтальные и вертикальные градиенты, обратить направление градиента или сжать охват градиента, применив его к меньшему диапазону, чем полная закрашенная область. Чтобы сжать градиент, следует задать значения **StartPoint** и (или) **EndPoint**, отличные от 0 и 1. Например, если требуется горизонтальный градиент, где затухание цвета происходит на левой половине кисти, а с правой стороны используется сплошной цвет последнего [**GradientStop**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.GradientStop), следует указать для **StartPoint** значение `(0,0)`, а для **EndPoint** — значение `(0.5,0)`.

### <a name="span-iduse_tools_to_make_gradientsspanspan-iduse_tools_to_make_gradientsspanspan-iduse_tools_to_make_gradientsspanuse-tools-to-make-gradients"></a><span id="Use_tools_to_make_gradients"></span><span id="use_tools_to_make_gradients"></span><span id="USE_TOOLS_TO_MAKE_GRADIENTS"></span>Создание градиента с помощью средств

Теперь, когда вы знаете, как работают линейные градиенты, вам будет легче создавать их с помощью Visual Studio или Blend. Чтобы создать градиент, выделите в рабочей области конструирования или в представлении XAML объект, к которому нужно применить градиент. Разверните раздел **Кисть** и выберите вкладку **Линейный градиент** (см. снимок экрана ниже).

![Создайте линейный градиент с помощью Visual Studio.](images/tool-gradient-brush-1.png)

Теперь вы можете изменять цвета позиций градиента и перемещать их помощью панели в нижней части экрана. Кроме того, вы можете добавить новые позиции градиента, щелкнув на панели и удалив их методом перетаскивания с панели (см. снимок экрана ниже).

![Панель в нижней части окна свойств, управляющая позициями градиента.](images/tool-gradient-brush-2.png)

## <a name="span-idimage_brushesspanspan-idimage_brushesspanspan-idimage_brushesspanimage-brushes"></a><span id="Image_brushes"></span><span id="image_brushes"></span><span id="IMAGE_BRUSHES"></span>Кисти изображений

Объект [**ImageBrush**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.ImageBrush) заполняет область изображением, которое поступает из файла изображения. В свойстве [**ImageSource**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.ImageSource) задается путь к загружаемому изображению. Обычно источник изображения поступает из элемента **Content**, являющегося частью ресурсов вашего приложения.

По умолчанию [**ImageBrush**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.ImageBrush) растягивает изображение так, чтобы полностью закрыть закрашиваемую область. При этом изображение может искажаться, если пропорции области отличаются от пропорций изображения. Чтобы исключить такое искажение, измените в свойстве [**Stretch**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.media.tilebrush.stretch) значение по умолчанию **Fill** на **None**, **Uniform** или **UniformToFill**.

В следующем примере создается объект [**ImageBrush**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.ImageBrush), а в свойстве [**ImageSource**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.ImageSource) задается изображение с именем licorice.jpg, которое необходимо включить в приложение в качестве ресурса. Затем **ImageBrush** закрашивает область, определенную формой [**Ellipse**](/uwp/api/Windows.UI.Xaml.Shapes.Ellipse).

```xml
<Ellipse Height="200" Width="300">
   <Ellipse.Fill>
     <ImageBrush ImageSource="licorice.jpg" />
   </Ellipse.Fill>
</Ellipse>
```

Результат работы [**ImageBrush**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.ImageBrush) выглядит так:

![Отображение ImageBrush.](images/brushes-imagebrush.jpg)

[**ImageBrush**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.ImageBrush) и [**Image**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Image) ссылаются на исходный файл изображения по универсальному коду ресурса (URI). Для этого файла допустимы несколько графических форматов. Такие исходные файлы изображений указываются в виде URI. См. сведения об определении источников изображений, их возможных форматах и упаковке в приложении в описании [классов Image и ImageBrush](https://docs.microsoft.com/windows/uwp/controls-and-patterns/images-imagebrushes).

## <a name="brushes-and-text"></a>Кисти и текст

С помощью кистей также можно применить характеристики отрисовки к текстовым элементам. Например, свойство [**Foreground**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.textblock.foreground) объекта [**TextBlock**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.TextBlock) принимает объект [**Brush**](/uwp/api/Windows.UI.Xaml.Media.Brush). К тексту можно применить любую из описанных здесь кистей. Но кисти следует применять к тексту с осторожностью, так как можно сделать такой текст нечитаемым, если используются кисти, сливающиеся с фоном, поверх которого отображается текст, или отвлекающие от контуров символов текста. Для обеспечения читаемости текстовых элементов в большинстве случаев ограничьтесь объектом [**SolidColorBrush**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.SolidColorBrush), если только не нужно намеренно придать декоративность текстовому элементу.

Даже при использовании сплошного цвета убедитесь, что выбранный цвет текста достаточно контрастирует с фоновым цветом контейнера макета текста. Уровень контрастности между основным цветом текста и фоном текстового контейнера влияет на доступность приложения для людей с ограниченными возможностями.

## <a name="webviewbrush"></a>WebViewBrush

Объект [**WebViewBrush**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.WebViewBrush) — это специальный тип кисти, которому доступно содержимое, обычно просматриваемое в элементе управления [**WebView**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.WebView). Вместо отрисовки содержимого в прямоугольной области элемента управления **WebView** объект **WebViewBrush** выводит это содержимое поверх другого элемента со свойством типа [**Brush**](/uwp/api/Windows.UI.Xaml.Media.Brush) для поверхности отрисовки. Объект **WebViewBrush** подходит не для каждого сценария использования кисти, но его удобно использовать при переходах **WebView**. Дополнительные сведения см. в статье о [**WebViewBrush**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.WebViewBrush).

## <a name="xamlcompositionbrushbase"></a>XamlCompositionBrushBase

[**XamlCompositionBrushBase**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.media.xamlcompositionbrushbase) – это базовый класс, который используется для создания настраиваемых кистей, использующих [**CompositionBrush**](https://docs.microsoft.com/uwp/api/Windows.UI.Composition.CompositionBrush) для рисования элементов пользовательского интерфейса XAML.

Это включает "нисходящее" взаимодействие между слоями Windows.UI.Xaml и Windows.UI.Composition, как описано в статье о [**визуальном уровне**](/windows/uwp/composition/visual-layer). 

Чтобы создать настраиваемую кисть, создайте новый класс, производный от XamlCompositionBrushBase и реализующий необходимые методы.

Например, она может использоваться для применения [**эффектов**](/windows/uwp/composition/composition-effects) к элементам пользовательского интерфейса XAML с помощью [**CompositionEffectBrush**](https://docs.microsoft.com/uwp/api/Windows.UI.Composition.CompositionEffectBrush), например **GaussianBlurEffect** или [**SceneLightingEffect**](https://docs.microsoft.com/uwp/api/Windows.UI.Composition.Effects.SceneLightingEffect), который контролирует отражающие свойства UIElement XAML при его освещении с помощью [**XamlLight**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.media.xamllight).

Примеры кода см. на странице со справочной документацией по [**XamlCompositionBrushBase**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.media.xamlcompositionbrushbase).

## <a name="brushes-as-xaml-resources"></a>Кисти как ресурсы XAML

Любую кисть можно объявить ресурсом XAML с ключом в словаре ресурсов XAML. Это облегчает репликацию одинаковых значений кистей при применении ко многим элементам пользовательского интерфейса. Затем значения кистей предоставляются в общий доступ и применяются при обращении к ресурсу кисти в виде [{StaticResource}](https://docs.microsoft.com/windows/uwp/xaml-platform/staticresource-markup-extension) в языке XAML. Сюда относится и использование шаблона элемента управления XAML, ссылающегося на общую кисть, когда сам этот шаблон элемента управления является ресурсом XAML с ключом.

## <a name="brushes-in-code"></a>Определение кистей в коде

Обычно кисти указываются с помощью XAML, а не определяются в коде. Это связано с тем, что кисти обычно определяются как ресурсы XAML, а значения кистей часто являются результатами работы инструментов проектирования или входят в определение пользовательского интерфейса XAML. Но иногда удобно определить кисть в кода, и все типы [**Brush**](/uwp/api/Windows.UI.Xaml.Media.Brush) будут доступными для создания экземпляра в коде.

Чтобы создать [**SolidColorBrush**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.SolidColorBrush) в коде, используйте конструктор, который принимает параметр [**Color**](https://docs.microsoft.com/uwp/api/Windows.UI.Color). Передайте значение, которое является статическим свойством класса [**Colors**](https://docs.microsoft.com/uwp/api/windows.ui.colors), например:

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

Для [**WebViewBrush**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.WebViewBrush) и [**ImageBrush**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.ImageBrush) используйте конструктор по умолчанию. Затем, прежде чем вы попытаетесь использовать эту кисть для свойства пользовательского интерфейса, вызовите другие API.

-   [**ImageSource**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.media.imagebrush.imagesourceproperty) требует [**BitmapImage**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.Imaging.BitmapImage) (а не URI), когда вы определяете [**ImageBrush**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.ImageBrush) с помощью кода. Если источником является поток, для инициализации значения используйте метод [**SetSourceAsync**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.media.imaging.bitmapsource.setsourceasync). Если источником является универсальный код ресурса (URI), который включает в себя используемое в вашем приложении содержимое со схемами **ms-appx** или **ms-resource**, используйте конструктор [**BitmapImage**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.media.imaging.bitmapimage), который применяет универсальный код ресурса (URI). Также можно обрабатывать события [**ImageOpened**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.media.imagebrush.imageopened), если существуют проблемы со своевременным получением или декодированием источника изображения, когда вы решите отображать альтернативное содержимое, пока источник изображения не станет доступным.
-   Для [**WebViewBrush**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.WebViewBrush) вам может понадобиться вызвать [**Redraw**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.webviewbrush.redraw), если вы недавно сбросили свойство [**SourceName**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.webviewbrush.sourcename) или если содержимое [**WebView**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.WebView) также было изменено в коде.

Примеры кода см. на страницах справки по [**WebViewBrush**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.WebViewBrush), [**ImageBrush**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.ImageBrush) и [**XamlCompositionBrushBase**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.media.xamlcompositionbrushbase).
 

 





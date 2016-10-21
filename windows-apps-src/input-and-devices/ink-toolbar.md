---
author: Karl-Bridge-Microsoft
Description: "Добавьте InkToolbar по умолчанию в приложение универсальной платформы Windows (UWP) для рукописного ввода, добавьте кнопку специального пера в InkToolbar и привяжите кнопку специального пера к определению специального пера."
title: "Добавление элемента управления InkToolbar в приложение рукописного ввода UWP"
label: Add an InkToolbar to a Universal Windows Platform (UWP) inking app
template: detail.hbs
keyword: Windows Ink, Windows Inking, DirectInk, InkPresenter, InkCanvas, InkToolbar, Universal Windows Platform, UWP
translationtype: Human Translation
ms.sourcegitcommit: 71b73605bab71dad36977d0506c090c34359a3e2
ms.openlocfilehash: c4a5b0ae2893fda7697457b9e7449a996707de4b

---

# Добавление элемента управления InkToolbar в приложение рукописного ввода UWP

В приложениях универсальной платформы Windows (UWP) рукописный ввод контролируется с помощью двух разных элементов управления: [**InkCanvas**](https://msdn.microsoft.com/en-us/library/windows/apps/windows.ui.xaml.controls.inkcanvas.aspx) и [**InkToolbar**](https://msdn.microsoft.com/en-us/library/windows/apps/windows.ui.xaml.controls.inktoolbar.aspx).

Элемент управления [**InkCanvas**](https://msdn.microsoft.com/en-us/library/windows/apps/windows.ui.xaml.controls.inkcanvas.aspx) предоставляет базовую функциональность Windows Ink. Используйте его для отрисовки ввода с помощью пера в качестве росчерка пера (с параметрами цвета и толщины по умолчанию) или росчерка стирания.

> Подробные сведения о реализации элемента управления InkCanvas см. в разделе [Взаимодействие с помощью пера в приложениях UWP](pen-and-stylus-interactions.md).

Элемент управления InkCanvas представляет собой полностью прозрачное наложение и не предоставляет никакого встроенного пользовательского интерфейса для настройки свойств росчерка пера. Изменить стандартные настройки рукописного ввода, предоставить пользователям возможность настраивать свойства росчерка пера и обеспечить поддержку других специальных функций рукописного ввода можно двумя способами.

- В коде программной части с помощью объекта [**InkPresenter**](https://msdn.microsoft.com/en-us/library/windows/apps/windows.ui.input.inking.inkpresenter.aspx), привязанного к элементу управления InkCanvas.

  API InkPresenter предоставляют широкие возможности пользовательской настройки рукописного ввода. Подробные сведения см. в разделе [Взаимодействие с помощью пера в приложениях UWP](pen-and-stylus-interactions.md).

- Привяжите элемент управления [**InkToolbar**](https://msdn.microsoft.com/en-us/library/windows/apps/windows.ui.xaml.controls.inktoolbar.aspx) к элементу InkCanvas. По умолчанию InkToolbar предоставляет базовый пользовательский интерфейс для активации функций рукописного ввода и настройки параметров ввода, таких как размер росчерка, цвет чернил и форма кончика пера.

  В этом разделе мы рассмотрим элемент управления InkToolbar.

## Важные API

  -   [**Класс InkCanvas**](https://msdn.microsoft.com/en-us/library/windows/apps/windows.ui.xaml.controls.inkcanvas.aspx)
  -   [**Класс InkToolbar**](https://msdn.microsoft.com/en-us/library/windows/apps/windows.ui.xaml.controls.inktoolbar.aspx)
  -   [**Класс InkPresenter**](https://msdn.microsoft.com/en-us/library/windows/apps/windows.ui.input.inking.inkpresenter.aspx)
  -   [**Windows.UI.Input.Inking**](https://msdn.microsoft.com/library/windows/apps/br208524)


## InkToolbar по умолчанию

По умолчанию элемент управления InkToolbar содержит кнопки для рисования, стирания, выделения и отображения линейки. В зависимости от функции во всплывающем элементе могут отображаться другие параметры и команды, например цвет чернил, толщина росчерка и стирание всего написанного.

![InkToolbar](.\images\ink\ink-tools-invoked-toolbar-small.png)  
*Windows Ink toolbar по умолчанию*

Чтобы добавить базовый элемент управления InkToolbar по умолчанию, выполните следующие действия.
1. В файле MainPage.xaml объявите объект-контейнер (в этом примере мы используем элемент управления «сетка») для поверхности рукописного ввода.
2. Объявите объект InkCanvas в качестве дочернего элемента контейнера. (Размер элемента управления InkCanvas наследуется от контейнера.)
3. Объявите элемент управления InkToolbar и используйте атрибут TargetInkCanvas, чтобы привязать этот элемент к InkCanvas.
  Убедитесь, что элемент управления InkToolbar объявлен после InkCanvas. В противном случае наложение InkCanvas сделает InkToolbar недоступным.

```xaml
<Grid Background="{ThemeResource ApplicationPageBackgroundThemeBrush}">
    <Grid.RowDefinitions>
        <RowDefinition Height="Auto"/>
        <RowDefinition Height="*"/>
    </Grid.RowDefinitions>
    <StackPanel x:Name="HeaderPanel" Orientation="Horizontal" Grid.Row="0">
        <TextBlock x:Name="Header"
                   Text="Basic ink sample"
                   Style="{ThemeResource HeaderTextBlockStyle}"
                   Margin="10,0,0,0" />
    </StackPanel>
    <Grid Grid.Row="1">
        <Image Source="Assets\StoreLogo.png" />
        <InkCanvas x:Name="inkCanvas" />
        <InkToolbar x:Name="inkToolbar"
          VerticalAlignment="Top"
          TargetInkCanvas="{x:Bind inkCanvas}" />
    </Grid>
</Grid>
```

## Базовая настройка

В этом разделе мы рассмотрим некоторые базовые сценарии пользовательской настройки панели инструментов Windows Ink.

### Укажите выделенную кнопку  
![Кнопка карандаша, выбранная при инициализации](.\images\ink\ink-tools-default-toolbar.png)  
*Панель инструментов Windows Ink с кнопкой карандаша, выбранной при инициализации*

По умолчанию первая (или крайняя левая) кнопка выбрана, когда запускается приложение и инициируется панель инструментов. На панели инструментов Windows Ink по умолчанию это кнопка шариковой ручки.

Поскольку порядок встроенных кнопок определяется платформой, первая кнопка не обязательно связана с пером или инструментом, который требуется активировать по умолчанию.

Можно переопределить это поведение по умолчанию и указать выбранную кнопку на панели инструментов.

В этом примере мы инициализируем панель инструментов по умолчанию с выбранной кнопкой карандаша и активированным карандашом (вместо шариковой ручки).

1. Используйте объявление XAML для элементов управления InkToolbar и InkCanvas из предыдущего примера.
2. В коде программной части настройте обработчик для события [Loaded](https://msdn.microsoft.com/en-us/library/windows/apps/windows.ui.xaml.frameworkelement.loaded.aspx) объекта [InkToolbar](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.inktoolbar.aspx).

  ```csharp
  /// <summary>
  /// An empty page that can be used on its own or navigated to within a Frame.
  /// Here, we set up InkToolbar event listeners.
  /// </summary>
  public MainPage_CodeBehind()
  {
      this.InitializeComponent();
      // Add handlers for InkToolbar events.
      inkToolbar.Loaded += inkToolbar_Loaded;
  }
  ```

3. В обработчике события [Loaded](https://msdn.microsoft.com/en-us/library/windows/apps/windows.ui.xaml.frameworkelement.loaded.aspx) выполните следующие действия:
  1. Получите ссылку на встроенную кнопку [InkToolbarPencilButton](https://msdn.microsoft.com/en-us/library/windows/apps/windows.ui.xaml.controls.inktoolbarpencilbutton.aspx).

    При передаче объекта [InkToolbarTool.Pencil](https://msdn.microsoft.com/en-us/library/windows/apps/windows.ui.xaml.controls.inktoolbartool.aspx) в метод [GetToolButton](https://msdn.microsoft.com/en-us/library/windows/apps/windows.ui.xaml.controls.inktoolbar.gettoolbutton.aspx) возвращается объект [InkToolbarToolButton](https://msdn.microsoft.com/en-us/library/windows/apps/windows.ui.xaml.controls.inktoolbartoolbutton.aspx) для элемента [InkToolbarPencilButton](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.inktoolbarpencilbutton.aspx).

  2. Настройте [ActiveTool](https://msdn.microsoft.com/en-us/library/windows/apps/windows.ui.xaml.controls.inktoolbar.activetool.aspx) для объекта, возвращенного в предыдущем шаге.

```CSharp
/// <summary>
/// Handle the Loaded event of the InkToolbar.
/// By default, the active tool is set to the first tool on the toolbar.
/// Here, we set the active tool to the pencil button.
/// </summary>
/// <param name="sender"></param>
/// <param name="e"></param>
private void inkToolbar_Loaded(object sender, RoutedEventArgs e)
{
    InkToolbarToolButton pencilButton = inkToolbar.GetToolButton(InkToolbarTool.Pencil);
    inkToolbar.ActiveTool = pencilButton;
}
```

### Определение встроенных кнопок

![Конкретные кнопки, включенные при инициализации](.\images\ink\ink-tools-specific.png)  
*Конкретные кнопки, включенные при инициализации*

Как уже упоминалось, панель инструментов Windows Ink содержит коллекцию встроенных кнопок по умолчанию. Эти кнопки отображаются в следующем порядке (слева направо):

- [InkToolbarBallpointPenButton](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.inktoolbarballpointpenbutton.aspx)
- [InkToolbarPencilButton](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.inktoolbarpencilbutton.aspx)
- [InkToolbarHighlighterButton](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.inktoolbarhighlighterbutton.aspx)
- [InkToolbarEraserButton](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.inktoolbareraserbutton.aspx)
- [InkToolbarRulerButton](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.inktoolbarrulerbutton.aspx)

В этом примере мы инициализируем панель инструментов только с встроенными кнопками шариковой ручки, карандаша и ластика.

Это можно сделать с помощью XAML или кода программной части.

**XAML**

Измените объявление XAML для элементов управления InkCanvas и InkToolbar из первого примера.
- Добавьте атрибут [InitialControls](https://msdn.microsoft.com/en-us/library/windows/apps/windows.ui.xaml.controls.inktoolbar.initialcontrols.aspx) и сделайте его значение равным [None](https://msdn.microsoft.com/en-us/library/windows/apps/windows.ui.xaml.controls.inktoolbarinitialcontrols.aspx). Коллекция встроенных кнопок по умолчанию будет очищена.
- Добавьте кнопки InkToolbar, необходимые в вашем приложении. Здесь мы добавим только кнопки [InkToolbarBallpointPenButton](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.inktoolbarballpointpenbutton.aspx), [InkToolbarPencilButton](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.inktoolbarpencilbutton.aspx) и [InkToolbarEraserButton](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.inktoolbareraserbutton.aspx).
> [!NOTE]
> Кнопки добавляются на панель инструментов в порядке, определяемом платформой, а не в указанном здесь порядке.

```xaml
<Grid Background="{ThemeResource ApplicationPageBackgroundThemeBrush}">
    <Grid.RowDefinitions>
        <RowDefinition Height="Auto"/>
        <RowDefinition Height="*"/>
    </Grid.RowDefinitions>
    <StackPanel x:Name="HeaderPanel" Orientation="Horizontal" Grid.Row="0">
        <TextBlock x:Name="Header"
                   Text="Basic ink sample"
                   Style="{ThemeResource HeaderTextBlockStyle}"
                   Margin="10,0,0,0" />
    </StackPanel>
    <Grid Grid.Row="1">
        <Image Source="Assets\StoreLogo.png" />
        <!-- Clear the default InkToolbar buttons by setting InitialControls to None. -->
        <!-- Set the active tool to the pencil button. -->
        <InkCanvas x:Name="inkCanvas" />
        <InkToolbar x:Name="inkToolbar"
                    VerticalAlignment="Top"
                    TargetInkCanvas="{x:Bind inkCanvas}"
                    InitialControls="None">
            <!--
             Add only the ballpoint pen, pencil, and eraser.
             Note that the buttons are added to the toolbar in the order
             defined by the framework, not the order we specify here.
            -->
            <InkToolbarEraserButton />
            <InkToolbarBallpointPenButton />
            <InkToolbarPencilButton/>
        </InkToolbar>
    </Grid>
</Grid>
```

**Поддерживающий код**
1. Используйте объявление XAML для элементов управления InkCanvas и InkToolbar из первого примера.

  ```xaml
  <Grid Background="{ThemeResource ApplicationPageBackgroundThemeBrush}">
      <Grid.RowDefinitions>
          <RowDefinition Height="Auto"/>
          <RowDefinition Height="*"/>
      </Grid.RowDefinitions>
      <StackPanel x:Name="HeaderPanel" Orientation="Horizontal" Grid.Row="0">
          <TextBlock x:Name="Header"
                     Text="Basic ink sample"
                     Style="{ThemeResource HeaderTextBlockStyle}"
                     Margin="10,0,0,0" />
      </StackPanel>
      <Grid Grid.Row="1">
          <Image Source="Assets\StoreLogo.png" />
          <InkCanvas x:Name="inkCanvas" />
          <InkToolbar x:Name="inkToolbar"
          VerticalAlignment="Top"
          TargetInkCanvas="{x:Bind inkCanvas}" />
      </Grid>
  </Grid>
  ```

2. В коде программной части настройте обработчик для события [Loading](https://msdn.microsoft.com/en-us/library/windows/apps/windows.ui.xaml.frameworkelement.loading.aspx) объекта [InkToolbar](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.inktoolbar.aspx).

  ```csharp
  /// <summary>
  /// An empty page that can be used on its own or navigated to within a Frame.
  /// Here, we set up InkToolbar event listeners.
  /// </summary>
  public MainPage_CodeBehind()
  {
      this.InitializeComponent();
      // Add handlers for InkToolbar events.
      inkToolbar.Loading += inkToolbar_Loading;
  }
  ```

3. Задайте для параметра [InitialControls](https://msdn.microsoft.com/en-us/library/windows/apps/windows.ui.xaml.controls.inktoolbar.initialcontrols.aspx) значение [None](https://msdn.microsoft.com/en-us/library/windows/apps/windows.ui.xaml.controls.inktoolbarinitialcontrols.aspx).
4. Создайте ссылки на объекты для кнопок, которые требуются вашему приложению. Здесь мы добавим только кнопки [InkToolbarBallpointPenButton](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.inktoolbarballpointpenbutton.aspx), [InkToolbarPencilButton](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.inktoolbarpencilbutton.aspx) и [InkToolbarEraserButton](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.inktoolbareraserbutton.aspx).
  > [!NOTE]
  > Кнопки добавляются на панель инструментов в порядке, определяемом платформой, а не в указанном здесь порядке.

5. [Добавьте](https://msdn.microsoft.com/en-us/library/windows/apps/windows.ui.xaml.dependencyobjectcollection.add.aspx) кнопки в элемент управления InkToolbar.

  ```csharp
  /// <summary>
  /// Handles the Loading event of the InkToolbar.
  /// Here, we identify the buttons to include on the InkToolbar.
  /// </summary>
  /// <param name="sender">The InkToolbar</param>
  /// <param name="args">The InkToolbar event data.
  /// If there is no event data, this parameter is null</param>
  private void inkToolbar_Loading(FrameworkElement sender, object args)
  {
      // Clear all built-in buttons from the InkToolbar.
      inkToolbar.InitialControls = InkToolbarInitialControls.None;

      // Add only the ballpoint pen, pencil, and eraser.
      // Note that the buttons are added to the toolbar in the order
      // defined by the framework, not the order we specify here.
      InkToolbarBallpointPenButton ballpoint = new InkToolbarBallpointPenButton();
      InkToolbarPencilButton pencil = new InkToolbarPencilButton();
      InkToolbarEraserButton eraser = new InkToolbarEraserButton();
      inkToolbar.Children.Add(eraser);
      inkToolbar.Children.Add(ballpoint);
      inkToolbar.Children.Add(pencil);
  }
  ```

<!--
### Support touch input
By default, the InkToolbar supports both pen and mouse input, you have to enable support for touch input.
-->

## Специальные кнопки и функции рукописного ввода

Можно настроить и расширить коллекцию кнопок (и связанных с ними функций рукописного ввода), предоставляемую элементом управления InkToolbar.

В элементе управления InkToolbar представлены две группы кнопок разных типов.

1. Группа кнопок-инструментов, которая включает встроенные кнопки для рисования, стирания и выделения. Сюда добавляются специальные перья и инструменты.
> **Примечание.**&nbsp;&nbsp;Функции являются взаимоисключающими.

2. Группа кнопок-переключателей, которая содержит встроенную кнопку линейки. Сюда добавляются специальные переключатели.
> **Примечание.**&nbsp;&nbsp;Эти функции не являются взаимоисключающими, их можно использовать вместе с другими активными элементами.

В зависимости от используемого приложения и требуемой функции рукописного ввода в элемент управления InkToolbar можно добавить любые нижеперечисленные кнопки (связанные со специальными функциями пера).

- Специальное перо— перо, для которого соответствующее приложение определяет цветовую палитру и свойства кончика пера, такие как форма, поворот и размер.
- Специальный инструмент— отличный от пера инструмент, который определяется соответствующим приложением.
- Специальный переключатель— включает или выключает определенную приложением функцию. Если функция включена, она действует вместе с активным инструментом.

> **Примечание.**&nbsp;&nbsp;Изменить порядок отображения встроенных кнопок невозможно. Они отображаются в следующем порядке: шариковая ручка, карандаш, маркер, ластик и линейка. Специальные перья добавляются после последнего пера по умолчанию, кнопки специальных инструментов располагаются между кнопкой последнего пера и кнопкой ластика, а кнопки специальных переключателей— после кнопки линейки (специальные кнопки добавляются в порядке настройки).

### Специальное перо

![Кнопка специального каллиграфического пера](.\images\ink\ink-tools-custompen.png)  
*Кнопка специального каллиграфического пера*

В этом примере мы определяем специальное перо с широким кончиком, с помощью которого можно делать основные каллиграфические росчерки пера. Кроме того, мы настроим коллекцию кистей в палитре, которая отображается во всплывающем элементе кнопки.

**Поддерживающий код**

Сначала мы определим специальное перо и зададим атрибуты рисования в коде программной части. Впоследствии мы будем ссылаться на это специальное перо из XAML.

1. Щелкните правой кнопкой мыши проект в обозревателе решений и выберите команду «Добавить» > «Новый элемент».
2. В разделе Visual C# -> «Код» добавьте новый файл класса и назовите его CalligraphicPen.cs.
3. В этом файле Calligraphic.cs замените блок использования по умолчанию следующим кодом:
```csharp
using System.Numerics;
using Windows.UI;
using Windows.UI.Input.Inking;
using Windows.UI.Xaml.Controls;
using Windows.UI.Xaml.Media;
```

4. Укажите, что класс CalligraphicPen наследуется от [InkToolbarCustomPen](https://msdn.microsoft.com/en-us/library/windows/apps/windows.ui.xaml.controls.inktoolbarcustompen.aspx).
```csharp
class CalligraphicPen : InkToolbarCustomPen
{
}
```

5. Переопределите [CreateInkDrawingAttributesCore](https://msdn.microsoft.com/en-us/library/windows/apps/windows.ui.xaml.controls.inktoolbarcustompen.createinkdrawingattributescore.aspx), чтобы задать собственную кисть и нужный размер росчерка.
```csharp
class CalligraphicPen : InkToolbarCustomPen
{
    protected override InkDrawingAttributes
      CreateInkDrawingAttributesCore(Brush brush, double strokeWidth)
    {
    }
}
```

6. Создайте объект [InkDrawingAttributes](https://msdn.microsoft.com/en-us/library/windows/apps/windows.ui.input.inking.inkdrawingattributes.aspx) и настройте [форму наконечника пера](https://msdn.microsoft.com/en-us/library/windows/apps/windows.ui.input.inking.inkdrawingattributes.pentip.aspx), [поворот кончика](https://msdn.microsoft.com/en-us/library/windows/apps/windows.ui.input.inking.inkdrawingattributes.pentiptransform.aspx), [размер росчерка](https://msdn.microsoft.com/en-us/library/windows/apps/windows.ui.input.inking.inkdrawingattributes.size.aspx) и [цвет чернил](https://msdn.microsoft.com/en-us/library/windows/apps/windows.ui.input.inking.inkdrawingattributes.color.aspx).
```csharp
class CalligraphicPen : InkToolbarCustomPen
{
    protected override InkDrawingAttributes
      CreateInkDrawingAttributesCore(Brush brush, double strokeWidth)
    {
        InkDrawingAttributes inkDrawingAttributes =
          new InkDrawingAttributes();
        inkDrawingAttributes.PenTip = PenTipShape.Circle;
        inkDrawingAttributes.Size =
          new Windows.Foundation.Size(strokeWidth, strokeWidth * 20);
        SolidColorBrush solidColorBrush = brush as SolidColorBrush;
        if (solidColorBrush != null)
        {
            inkDrawingAttributes.Color = solidColorBrush.Color;
        }
        else
        {
            inkDrawingAttributes.Color = Colors.Black;
        }

        Matrix3x2 matrix = Matrix3x2.CreateRotation(45);
        inkDrawingAttributes.PenTipTransform = matrix;

        return inkDrawingAttributes;
    }
}
```

**XAML**

Затем добавим необходимые ссылки на специальное перо в файле MainPage.xaml.

1. Мы объявим словарь ресурсов локальной страницы, который создает ссылку на специальное перо (`CalligraphicPen`), определенное в файле CalligraphicPen.cs, и [коллекцию кистей](https://msdn.microsoft.com/en-us/library/windows/apps/Windows.UI.Xaml.Media.BrushCollection.aspx), поддерживаемых специальным пером (`CalligraphicPenPalette`).
```xaml
<Page.Resources>
    <!-- Add the custom CalligraphicPen to the page resources. -->
    <local:CalligraphicPen x:Key="CalligraphicPen" />
    <!-- Specify the colors for the palette of the custom pen. -->
    <BrushCollection x:Key="CalligraphicPenPalette">
        <SolidColorBrush Color="Blue" />
        <SolidColorBrush Color="Red" />
    </BrushCollection>
</Page.Resources>
```

2. Затем добавим элемент управления InkToolbar с дочерним элементом управления [InkToolbarCustomPenButton](https://msdn.microsoft.com/en-us/library/windows/apps/windows.ui.xaml.controls.inktoolbarcustompenbutton.aspx).

  Кнопка специального пера содержит две статические ссылки на ресурсы, объявленные в ресурсах страницы: `CalligraphicPen` и `CalligraphicPenPalette`.

  Кроме того, зададим диапазон для ползунка, регулирующего размер штриха ([MinStrokeWidth](https://msdn.microsoft.com/en-us/library/windows/apps/windows.ui.xaml.controls.inktoolbarpenbutton.minstrokewidth.aspx), [MaxStrokeWidth](https://msdn.microsoft.com/en-us/library/windows/apps/windows.ui.xaml.controls.inktoolbarpenbutton.maxstrokewidth.aspx) и [SelectedStrokeWidth](https://msdn.microsoft.com/en-us/library/windows/apps/windows.ui.xaml.controls.inktoolbarpenbutton.selectedstrokewidthproperty.aspx)), выбранной кисти ([SelectedBrushIndex](https://msdn.microsoft.com/en-us/library/windows/apps/windows.ui.xaml.controls.inktoolbarpenbutton.selectedbrushindex.aspx)) и значка для кнопки специального пера ([SymbolIcon](https://msdn.microsoft.com/en-us/library/windows/apps/windows.ui.xaml.controls.symbolicon.aspx)).
```xaml
<Grid Grid.Row="1">
    <InkCanvas x:Name="inkCanvas" />
    <InkToolbar x:Name="inkToolbar"
                VerticalAlignment="Top"
                TargetInkCanvas="{x:Bind inkCanvas}">
        <InkToolbarCustomPenButton
            CustomPen="{StaticResource CalligraphicPen}"
            Palette="{StaticResource CalligraphicPenPalette}"
            MinStrokeWidth="1" MaxStrokeWidth="3" SelectedStrokeWidth="2"
            SelectedBrushIndex ="1">
            <SymbolIcon Symbol="Favorite" />
            <InkToolbarCustomPenButton.ConfigurationContent>
                <InkToolbarPenConfigurationControl />
            </InkToolbarCustomPenButton.ConfigurationContent>
        </InkToolbarCustomPenButton>
    </InkToolbar>
</Grid>
```

<!--
### Custom toggle

Enable touch inking

>**Note**&nbsp;&nbsp;InkToolbar supports pen and mouse input and can be configured to recognize touch input.
-->


## Связанные разделы

* [Взаимодействие с помощью пера](pen-and-stylus-interactions.md)

**Примеры**
* [Пример рукописного ввода](http://go.microsoft.com/fwlink/p/?LinkID=620308)
* [Простой пример рукописного ввода](http://go.microsoft.com/fwlink/p/?LinkID=620312)
* [Сложный пример рукописного ввода](http://go.microsoft.com/fwlink/p/?LinkID=620314)



<!--HONumber=Sep16_HO2-->



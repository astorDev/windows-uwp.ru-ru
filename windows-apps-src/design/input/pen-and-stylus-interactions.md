---
Description: Создавайте приложения универсальной платформы Windows (UWP), поддерживающие специальные взаимодействия с помощью пера, в том числе рукописный ввод для естественного письма и рисования.
title: Взаимодействие с пером и Windows Ink в приложениях UWP
ms.assetid: 3DA4F2D2-5405-42A1-9ED9-3A87BCD84C43
label: Pen interactions and Windows Ink in UWP apps
template: detail.hbs
keywords: Windows Ink, Windows Inking, DirectInk, InkPresenter, InkCanvas, распознавание рукописного ввода, взаимодействие с пользователем, ввод
ms.date: 02/08/2017
ms.topic: article
ms.localizationpriority: medium
ms.openlocfilehash: a8d4299eb361fb804419af687bdcaa25ffa54bb8
ms.sourcegitcommit: 26bb75084b9d2d2b4a76d4aa131066e8da716679
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/06/2020
ms.locfileid: "75684469"
---
# <a name="pen-interactions-and-windows-ink-in-uwp-apps"></a>Взаимодействие с пером и Windows Ink в приложениях UWP

Перо ![Surface](images/ink/hero-small.png)  
*Ручка Surface* (доступна для приобретения в [Магазине Microsoft](https://www.microsoft.com/p/surface-pen/8zl5c82qmg6b)).

## <a name="overview"></a>Обзор

Оптимизируйте взаимодействия с помощью пера в своем приложении универсальной платформы Windows (UWP), чтобы обеспечить как стандартные функциональные возможности для работы с [**указателем**](https://docs.microsoft.com/uwp/api/Windows.Devices.Input.PointerDevice), так и возможности Windows Ink.

> [!NOTE]
> Данный раздел посвящен платформе Windows Ink. Общую информацию о работе с входными данными указателей, аналогично мыши, сенсорному экрану и сенсорной панели, см. в разделе [Работа с входными данными указателя](handle-pointer-input.md).

| Просмотр видео |   |
| --- | --- |
| <iframe src="https://channel9.msdn.com/Blogs/One-Dev-Minute/Using-Ink-in-Your-UWP-App/player" width="300" height="200" allowFullScreen frameBorder="0"></iframe> | <iframe src="https://channel9.msdn.com/Events/Ignite/2016/BRK2060/player" width="300" height="200" allowFullScreen frameBorder="0"></iframe> |
| *Использование рукописного ввода в приложении UWP* | *Использование пера и рукописного ввода Windows для создания более привлекательных корпоративных приложений* |

Платформа Windows Ink — наряду с пером — обеспечивает естественный способ создания цифровых рукописных примечаний, рисунков и заметок. Платформа поддерживает получение входных данных дигитайзера в виде рукописных данных, создание рукописных данных, управление этими данными, отображение их в виде росчерков пера на устройстве вывода и преобразование этих росчерков в текст с помощью функции распознавания рукописного ввода.

В дополнение к получению данных об основном положении и движении пера, когда пользователь пишет или рисует, ваше приложение также может отслеживать и собирать сведения об изменяющейся силе нажатия, используемой в росчерке. Эти сведения вместе с параметрами формы, размера и поворота кончика пера, цвета чернил и цели (обычный рукописный ввод, стирание, выделение и выбор) позволяют обеспечить результат взаимодействия, очень похожий на письмо или рисунок ручкой, карандашом или кистью на бумаге.

> [!NOTE]
> Ваше приложение может также поддерживать рукописный ввод и от других устройств на основе указателя, например дигитайзеров сенсорного ввода и мыши. 

Платформа рукописного ввода очень гибкая. Она предназначена для поддержки различных уровней функциональности в зависимости от ваших требований.

Рекомендации по взаимодействию с пользователем для Windows Ink см. в разделе [Элементы управления рукописным вводом](../controls-and-patterns/inking-controls.md).

## <a name="components-of-the-windows-ink-platform"></a>Компоненты платформы Windows Ink

| Component | Описание |
| --- | --- |
| [**InkCanvas**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.InkCanvas) | Элемент управления платформы пользовательского интерфейса XAML, который по умолчанию получает и отображает все входные данные пера в виде рукописного штриха или стирания.<br/>Дополнительные сведения о том, как использовать InkCanvas, см. в разделах [Распознавание росчерков пера Windows Ink как текста](convert-ink-to-text.md) и [Хранение и извлечение данных движения пера Windows Ink](save-and-load-ink.md). |
| [**Элемента**](https://docs.microsoft.com/uwp/api/Windows.UI.Input.Inking.InkPresenter) | Объект кода программной части, создаваемый вместе с элементом управления [**InkCanvas**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.InkCanvas) (предоставляется свойством [**InkCanvas.InkPresenter**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.inkcanvas.inkpresenter)). Этот объект обеспечивает все возможности рукописного ввода по умолчанию, предоставляемые элементом **InkCanvas**, вместе с полным набором API для дополнительной настройки и персонализации.<br/>Дополнительные сведения о том, как использовать InkPresenter, см. в разделах [Распознавание росчерков пера Windows Ink как текста](convert-ink-to-text.md) и [Хранение и извлечение данных движения пера Windows Ink](save-and-load-ink.md). |
| [**инктулбар**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.inktoolbar) | Элемент управления платформы пользовательского интерфейса XAML, содержащий настраиваемую и расширяемую коллекцию кнопок, которые активируют функции, связанные с рукописным вводом, в связанном [**InkCanvas**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.inkcanvas).<br/>Дополнительные сведения о том, как использовать InkToolbar, см. в разделе [Добавление InkToolbar в приложение универсальной платформы Windows (UWP) для рукописного ввода](ink-toolbar.md). |
| [**IInkD2DRenderer**](https://docs.microsoft.com/windows/desktop/api/inkrenderer/nn-inkrenderer-iinkd2drenderer) | Позволяет преобразовать росчерки пера в специальный контекст устройства Direct2D универсального приложения для Windows вместо элемента управления по умолчанию [**InkCanvas**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.InkCanvas). Это делает возможным полную настройку рукописного ввода.<br/>Дополнительные сведения см. в разделе [Сложный пример рукописного ввода](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/ComplexInk). |

## <a name="basic-inking-with-inkcanvas"></a>Базовый рукописный ввод с использованием InkCanvas

Чтобы добавить базовую функцию рукописного ввода, просто поместите элемент управления платформы UWP [**InkCanvas**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.InkCanvas) на соответствующую страницу в приложении.

По умолчанию [**InkCanvas**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.InkCanvas) поддерживает рукописный ввод только с помощью пера. Входные данные обрабатываются как росчерк пера с использованием параметров цвета и толщины по умолчанию (черная шариковая ручка с толщиной 2 пикселя) или трактуются как ластик для росчерков (когда ввод осуществляется кончиком ластика или кончиком пера, модифицированным при помощи кнопки стирания).

> [!NOTE]
> Если кнопка или кончик ластика отсутствуют, InkCanvas можно настроить для обработки ввода кончика пера как росчерка стирания.

В этом примере [**InkCanvas**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.InkCanvas) перекрывает фоновое изображение.

> [!NOTE]
> Свойство InkCanvas имеет свойства [**высоты**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.frameworkelement.Height) и [**ширины**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.frameworkelement.Width) по умолчанию, если оно не является дочерним элементом элемента, который автоматически изменяет размеры своих дочерних элементов, таких как [StackPanel](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.stackpanel) или [Grid](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.grid) Controls.

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
    </Grid>
</Grid>
```

В этой серии изображений показано, как ввод с помощью пера обрабатывается этим элементом управления [**InkCanvas**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.InkCanvas).

| ![Пустой элемент InkCanvas с фоновым изображением](images/ink_basic_1_small.png) | ![InkCanvas с росчерками пера](images/ink_basic_2_small.png) | ![InkCanvas с одним стертым росчерком](images/ink_basic_3_small.png) |
| --- | --- | ---|
| Пустой элемент [**InkCanvas**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.InkCanvas) с фоновым изображением. | [  **InkCanvas**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.InkCanvas) с росчерками пера. | [  **InkCanvas**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.InkCanvas) с одним росчерком (обратите внимание, как операция стирания применяется ко всему росчерку, а не его части). |

Возможности рукописного ввода, поддерживаемые элементом управления [**InkCanvas**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.InkCanvas), предоставляются объектом кода программной части [**InkPresenter**](https://docs.microsoft.com/uwp/api/Windows.UI.Input.Inking.InkPresenter).

При выполнении базового рукописного ввода вам не нужно беспокоиться об элементе [**InkPresenter**](https://docs.microsoft.com/uwp/api/Windows.UI.Input.Inking.InkPresenter). Однако для настройки и конфигурации реакции на рукописный ввод в элементе [**InkCanvas**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.InkCanvas) необходимо получить доступ к соответствующему объекту **InkPresenter**.

## <a name="basic-customization-with-inkpresenter"></a>Базовая настройка с использованием InkPresenter

Объект [**InkPresenter**](https://docs.microsoft.com/uwp/api/Windows.UI.Input.Inking.InkPresenter) создается с каждым элементом управления [**InkCanvas**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.InkCanvas).

> [!NOTE]
> [  **InkPresenter**](https://docs.microsoft.com/uwp/api/Windows.UI.Input.Inking.InkPresenter) нельзя создавать напрямую. Вместо этого доступ к нему осуществляется с помощью свойства [**InkPresenter**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.inkcanvas.inkpresenter) элемента [**InkCanvas**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.InkCanvas). 

Помимо всех реакций на рукописный ввод по умолчанию для соответствующего элемента управления [**InkCanvas**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.InkCanvas), [**InkPresenter**](https://docs.microsoft.com/uwp/api/Windows.UI.Input.Inking.InkPresenter) предоставляет полный набор API для дополнительной настройки росчерков и более детализированного управления вводом с помощью пера (стандартного и измененного). Это касается свойств росчерков, поддерживаемых типов устройств ввода и того, обрабатываются входные данные объектом или передаются приложению для обработки.

> [!NOTE]
> Стандартный рукописный ввод (кончик пера или кнопка/кончик ластика) не изменены с помощью таких дополнительных возможностей, как кнопка пера, правая кнопка мыши или подобные механизмы. 

По умолчанию рукописный ввод поддерживает только ввод с помощью пера. Здесь мы настраиваем [**InkPresenter**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.inkcanvas.inkpresenter) для интерпретации данных, вводимые пером или мышью, в качестве росчерков пера. Мы также задаем некоторые исходные атрибуты росчерка пера, используемые для вывода росчерков на [**InkCanvas**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.InkCanvas).

Чтобы включить рукописный ввод с помощью мыши и сенсорного ввода, задайте свойство [**InputDeviceTypes**](https://docs.microsoft.com/uwp/api/windows.ui.input.inking.inkpresenter.InputDeviceTypes)[**InkPresenter**](https://docs.microsoft.com/uwp/api/windows.ui.input.inking.inkpresenter) для сочетания значений [**CoreInputDeviceTypes**](https://docs.microsoft.com/uwp/api/windows.ui.core.coreinputdevicetypes), которые вам необходимы.

```csharp
public MainPage()
{
    this.InitializeComponent();

    // Set supported inking device types.
    inkCanvas.InkPresenter.InputDeviceTypes =
        Windows.UI.Core.CoreInputDeviceTypes.Mouse |
        Windows.UI.Core.CoreInputDeviceTypes.Pen;

    // Set initial ink stroke attributes.
    InkDrawingAttributes drawingAttributes = new InkDrawingAttributes();
    drawingAttributes.Color = Windows.UI.Colors.Black;
    drawingAttributes.IgnorePressure = false;
    drawingAttributes.FitToCurve = true;
    inkCanvas.InkPresenter.UpdateDefaultDrawingAttributes(drawingAttributes);
}
```

Атрибуты росчерка пера можно задавать динамически в соответствии с предпочтениями пользователя или требованиями к приложению.

Здесь мы позволяем пользователю выбирать цвет чернил в списке.

```xaml
<Grid Background="{ThemeResource ApplicationPageBackgroundThemeBrush}">
    <Grid.RowDefinitions>
        <RowDefinition Height="Auto"/>
        <RowDefinition Height="*"/>
    </Grid.RowDefinitions>
    <StackPanel x:Name="HeaderPanel" Orientation="Horizontal" Grid.Row="0">
        <TextBlock x:Name="Header"
                   Text="Basic ink customization sample"
                   VerticalAlignment="Center"
                   Style="{ThemeResource HeaderTextBlockStyle}"
                   Margin="10,0,0,0" />
        <TextBlock Text="Color:"
                   Style="{StaticResource SubheaderTextBlockStyle}"
                   VerticalAlignment="Center"
                   Margin="50,0,10,0"/>
        <ComboBox x:Name="PenColor"
                  VerticalAlignment="Center"
                  SelectedIndex="0"
                  SelectionChanged="OnPenColorChanged">
            <ComboBoxItem Content="Black"/>
            <ComboBoxItem Content="Red"/>
        </ComboBox>
    </StackPanel>
    <Grid Grid.Row="1">
        <Image Source="Assets\StoreLogo.png" />
        <InkCanvas x:Name="inkCanvas" />
    </Grid>
</Grid>
```

Затем мы обрабатываем изменения в выбранном цвете и обновляем соответствующим образом атрибуты росчерка пера.

```csharp
// Update ink stroke color for new strokes.
private void OnPenColorChanged(object sender, SelectionChangedEventArgs e)
{
    if (inkCanvas != null)
    {
        InkDrawingAttributes drawingAttributes =
            inkCanvas.InkPresenter.CopyDefaultDrawingAttributes();

        string value = ((ComboBoxItem)PenColor.SelectedItem).Content.ToString();

        switch (value)
        {
            case "Black":
                drawingAttributes.Color = Windows.UI.Colors.Black;
                break;
            case "Red":
                drawingAttributes.Color = Windows.UI.Colors.Red;
                break;
            default:
                drawingAttributes.Color = Windows.UI.Colors.Black;
                break;
        };

        inkCanvas.InkPresenter.UpdateDefaultDrawingAttributes(drawingAttributes);
    }
}
```

На этих изображениях показано, как ввод с помощью пера обрабатывается и настраивается элементом [**InkPresenter**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.inkcanvas.inkpresenter).

| ![InkCanvas с черными росчерками пера по умолчанию](images/ink-basic-custom-1-small.png) | ![InkCanvas с выбранными пользователем красными росчерками пера](images/ink-basic-custom-2-small.png) |
| --- | --- |
| [  **InkCanvas**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.InkCanvas) с черными росчерками пера по умолчанию. | [  **InkCanvas**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.InkCanvas) с выбранными пользователем красными росчерками пера. | 

Для предоставления функций помимо рукописного ввода и стирания, таких как возможность выбора росчерка, ваше приложение должно определить конкретные входные данные для элемента [**InkPresenter**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.inkcanvas.inkpresenter), чтобы передать необработанные данные для обработки вашим приложением.

## <a name="pass-through-input-for-advanced-processing"></a>Передача входных данных для расширенной обработки

По умолчанию [**InkPresenter**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.inkcanvas.inkpresenter) обрабатывает все входные данные как росчерк пера или росчерк стирания, включая данные ввода, которые модифицированы дополнительной возможностью оборудования, например кнопкой пера, правой кнопкой мыши или чем-то подобным. Тем не менее при использовании этих дополнительных возможностей пользователи обычно ожидают большей функциональности или изменения поведения.

В некоторых случаях может также понадобиться предоставить дополнительные функции для пера без дополнительных возможностей (функций, которые обычно не связаны с кончиком пера), других типов устройств ввода или изменения поведения на основе выбора пользователя в пользовательском интерфейсе вашего приложения.

Для поддержки этого [**InkPresenter**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.inkcanvas.inkpresenter) можно настроить оставлять определенные входные данные необработанными. Эти необработанные входные данные затем передаются в ваше приложение для обработки.

### <a name="example---use-unprocessed-input-to-implement-stroke-selection"></a>Пример. Использование необработанные входных данных для реализации возможности выбора росчерка 

Платформа Windows Ink не предоставляет встроенную поддержку для действий, требующих модификации ввода, таких как возможность выбора росчерка. Для обеспечения поддержки таких функций необходимо предоставить специальное решение в приложениях. 

В следующем примере кода (для размещения всего кода используются файлы MainPage.xaml и MainPage.xaml.cs) показаны этапы добавления возможности выбора росчерка, когда входные данные изменяются кнопкой пера (или правой кнопкой мыши).

1.  Сначала мы настраиваем пользовательский интерфейс в файле MainPage.xaml.

    Здесь мы добавляем холст (под элементом [**InkCanvas**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.InkCanvas)) для рисования росчерка выбора. Если использовать отдельный слой для рисования росчерка выбора, элемент **InkCanvas** и его содержимое останутся без изменений.

    ![Пустой элемент InkCanvas с базовым холстом выбора](images/ink-unprocessed-1-small.png)

      ```xaml
        <Grid Background="{ThemeResource ApplicationPageBackgroundThemeBrush}">
          <Grid.RowDefinitions>
            <RowDefinition Height="Auto"/>
            <RowDefinition Height="*"/>
          </Grid.RowDefinitions>
          <StackPanel x:Name="HeaderPanel" Orientation="Horizontal" Grid.Row="0">
            <TextBlock x:Name="Header"
              Text="Advanced ink customization sample"
              VerticalAlignment="Center"
              Style="{ThemeResource HeaderTextBlockStyle}"
              Margin="10,0,0,0" />
          </StackPanel>
          <Grid Grid.Row="1">
            <!-- Canvas for displaying selection UI. -->
            <Canvas x:Name="selectionCanvas"/>
            <!-- Inking area -->
            <InkCanvas x:Name="inkCanvas"/>
          </Grid>
        </Grid>
      ```

2.  В файле MainPage.xaml.cs мы объявляем несколько глобальных переменных для сохранения ссылок на аспекты пользовательского интерфейса выбора. В частности, росчерк лассо выбора и ограничивающий прямоугольник, который выделяет выбранные росчерки.

      ```csharp
        // Stroke selection tool.
        private Polyline lasso;
        // Stroke selection area.
        private Rect boundingRect;
      ```

3.  Затем мы настраиваем [**InkPresenter**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.inkcanvas.inkpresenter) интерпретировать данные, вводимые пером или мышью, как росчерки пера и задаем некоторые исходные атрибуты росчерка пера, используемые для вывода росчерков на [**InkCanvas**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.InkCanvas).

    Важнее всего то, что мы используем свойство [**InputProcessingConfiguration**](https://docs.microsoft.com/uwp/api/windows.ui.input.inking.inkpresenter.inputprocessingconfiguration) элемента [InkPresenter](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.inkcanvas.inkpresenter) для указания того, что любые измененные входные данные должны обрабатываться приложением. Измененные входные данные указываются путем назначения перечислению **InputProcessingConfiguration.RightDragAction** значения [**InkInputRightDragAction.LeaveUnprocessed**](https://docs.microsoft.com/uwp/api/Windows.UI.Input.Inking.InkInputRightDragAction). Когда установлено это значение, элемент [InkPresenter](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.inkcanvas.inkpresenter) передается в класс [InkUnprocessedInput](https://docs.microsoft.com/uwp/api/windows.ui.input.inking.inkunprocessedinput) — набор событий указателя для обработки.

    Затем мы назначаем прослушиватели для необработанных событий [**PointerPressed**](https://docs.microsoft.com/uwp/api/windows.ui.input.inking.inkunprocessedinput.pointerpressed), [**PointerMoved**](https://docs.microsoft.com/uwp/api/windows.ui.input.inking.inkunprocessedinput.pointermoved) и [**PointerReleased**](https://docs.microsoft.com/uwp/api/windows.ui.input.inking.inkunprocessedinput.pointerreleased), переданных элементом [**InkPresenter**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.inkcanvas.inkpresenter). Все функции выбора реализованы в обработчиках для этих событий.

    Наконец, мы назначаем прослушиватели для событий [**StrokeStarted**](https://docs.microsoft.com/uwp/api/windows.ui.input.inking.inkstrokeinput.strokestarted) и [**StrokesErased**](https://docs.microsoft.com/uwp/api/windows.ui.input.inking.inkpresenter.strokeserased) элемента [**InkPresenter**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.inkcanvas.inkpresenter). Мы используем обработчики для этих событий, чтобы очистить пользовательский интерфейс выбора, если начинается новый росчерк или стирается существующий.

    ![InkCanvas с черными росчерками пера по умолчанию](images/ink-unprocessed-2-small.png)

      ```csharp
        public MainPage()
        {
          this.InitializeComponent();

          // Set supported inking device types.
          inkCanvas.InkPresenter.InputDeviceTypes =
            Windows.UI.Core.CoreInputDeviceTypes.Mouse |
            Windows.UI.Core.CoreInputDeviceTypes.Pen;

          // Set initial ink stroke attributes.
          InkDrawingAttributes drawingAttributes = new InkDrawingAttributes();
          drawingAttributes.Color = Windows.UI.Colors.Black;
          drawingAttributes.IgnorePressure = false;
          drawingAttributes.FitToCurve = true;
          inkCanvas.InkPresenter.UpdateDefaultDrawingAttributes(drawingAttributes);

          // By default, the InkPresenter processes input modified by
          // a secondary affordance (pen barrel button, right mouse
          // button, or similar) as ink.
          // To pass through modified input to the app for custom processing
          // on the app UI thread instead of the background ink thread, set
          // InputProcessingConfiguration.RightDragAction to LeaveUnprocessed.
          inkCanvas.InkPresenter.InputProcessingConfiguration.RightDragAction =
              InkInputRightDragAction.LeaveUnprocessed;

          // Listen for unprocessed pointer events from modified input.
          // The input is used to provide selection functionality.
          inkCanvas.InkPresenter.UnprocessedInput.PointerPressed +=
              UnprocessedInput_PointerPressed;
          inkCanvas.InkPresenter.UnprocessedInput.PointerMoved +=
              UnprocessedInput_PointerMoved;
          inkCanvas.InkPresenter.UnprocessedInput.PointerReleased +=
              UnprocessedInput_PointerReleased;

          // Listen for new ink or erase strokes to clean up selection UI.
          inkCanvas.InkPresenter.StrokeInput.StrokeStarted +=
              StrokeInput_StrokeStarted;
          inkCanvas.InkPresenter.StrokesErased +=
              InkPresenter_StrokesErased;
        }
      ```

4.  Затем мы определяем обработчики для необработанных событий [**PointerPressed**](https://docs.microsoft.com/uwp/api/windows.ui.input.inking.inkunprocessedinput.pointerpressed), [**PointerMoved**](https://docs.microsoft.com/uwp/api/windows.ui.input.inking.inkunprocessedinput.pointermoved) и [**PointerReleased**](https://docs.microsoft.com/uwp/api/windows.ui.input.inking.inkunprocessedinput.pointerreleased), переданных элементом [**InkPresenter**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.inkcanvas.inkpresenter).

    Все функции выбора реализованы в этих обработчиках, включая росчерк лассо и ограничивающий прямоугольник.

    ![Лассо выбора](images/ink-unprocessed-3-small.png)

      ```csharp
        // Handle unprocessed pointer events from modified input.
        // The input is used to provide selection functionality.
        // Selection UI is drawn on a canvas under the InkCanvas.
        private void UnprocessedInput_PointerPressed(
          InkUnprocessedInput sender, PointerEventArgs args)
        {
          // Initialize a selection lasso.
          lasso = new Polyline()
          {
              Stroke = new SolidColorBrush(Windows.UI.Colors.Blue),
              StrokeThickness = 1,
              StrokeDashArray = new DoubleCollection() { 5, 2 },
              };

              lasso.Points.Add(args.CurrentPoint.RawPosition);

              selectionCanvas.Children.Add(lasso);
          }

          private void UnprocessedInput_PointerMoved(
            InkUnprocessedInput sender, PointerEventArgs args)
          {
            // Add a point to the lasso Polyline object.
            lasso.Points.Add(args.CurrentPoint.RawPosition);
          }

          private void UnprocessedInput_PointerReleased(
            InkUnprocessedInput sender, PointerEventArgs args)
          {
            // Add the final point to the Polyline object and
            // select strokes within the lasso area.
            // Draw a bounding box on the selection canvas
            // around the selected ink strokes.
            lasso.Points.Add(args.CurrentPoint.RawPosition);

            boundingRect =
              inkCanvas.InkPresenter.StrokeContainer.SelectWithPolyLine(
                lasso.Points);

            DrawBoundingRect();
          }
      ```

5.  Чтобы закончить обработчик событий PointerReleased, мы очищаем слой выбора всего содержимого (росчерк лассо) и рисуем один ограничивающий прямоугольник вокруг росчерков пера, включенных в область лассо.

    ![Ограничивающий прямоугольник выбора](images/ink-unprocessed-4-small.png)

      ```csharp
        // Draw a bounding rectangle, on the selection canvas, encompassing
        // all ink strokes within the lasso area.
        private void DrawBoundingRect()
        {
          // Clear all existing content from the selection canvas.
          selectionCanvas.Children.Clear();

          // Draw a bounding rectangle only if there are ink strokes
          // within the lasso area.
          if (!((boundingRect.Width == 0) ||
            (boundingRect.Height == 0) ||
            boundingRect.IsEmpty))
            {
              var rectangle = new Rectangle()
              {
                Stroke = new SolidColorBrush(Windows.UI.Colors.Blue),
                  StrokeThickness = 1,
                  StrokeDashArray = new DoubleCollection() { 5, 2 },
                  Width = boundingRect.Width,
                  Height = boundingRect.Height
              };

              Canvas.SetLeft(rectangle, boundingRect.X);
              Canvas.SetTop(rectangle, boundingRect.Y);

              selectionCanvas.Children.Add(rectangle);
            }
          }
      ```

6.  Наконец мы определяем обработчики для событий InkPresenter [**StrokeStarted**](https://docs.microsoft.com/uwp/api/windows.ui.input.inking.inkstrokeinput.strokestarted) и [**StrokesErased**](https://docs.microsoft.com/uwp/api/windows.ui.input.inking.inkpresenter.strokeserased).

    Оба эти события просто вызывают одну и ту же функцию очистки, чтобы сбросить текущий выбор при обнаружении нового росчерка.

      ```csharp
        // Handle new ink or erase strokes to clean up selection UI.
        private void StrokeInput_StrokeStarted(
          InkStrokeInput sender, Windows.UI.Core.PointerEventArgs args)
        {
          ClearSelection();
        }

        private void InkPresenter_StrokesErased(
          InkPresenter sender, InkStrokesErasedEventArgs args)
        {
          ClearSelection();
        }
      ```

7.  Вот функция для удаления всего пользовательского интерфейса выбора с холста выбора, когда начинается новый росчерк или стирается существующий.

      ```csharp
        // Clean up selection UI.
        private void ClearSelection()
        {
          var strokes = inkCanvas.InkPresenter.StrokeContainer.GetStrokes();
          foreach (var stroke in strokes)
          {
            stroke.Selected = false;
          }
          ClearDrawnBoundingRect();
         }

        private void ClearDrawnBoundingRect()
        {
          if (selectionCanvas.Children.Any())
          {
            selectionCanvas.Children.Clear();
            boundingRect = Rect.Empty;
          }
        }
      ```

## <a name="custom-ink-rendering"></a>Пользовательская передача рукописного ввода

По умолчанию рукописные данные обрабатываются в фоновом потоке с низкой задержкой и отображаются во время выполнения как "влажные" в процессе ввода. После завершения росчерка (когда пользователь поднял перо или палец либо отпустил кнопку мыши) росчерк обрабатывается в потоке пользовательского интерфейса и отображается «сухим» на слое [**InkCanvas**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.InkCanvas) (над содержимым приложения, вместо влажных рукописных данных).

Вы можете переопределить это поведение по умолчанию и полностью управлять рукописным вводом за счет "пользовательской сушки" влажных росчерков пера. Тогда как поведения по умолчанию обычно достаточно для большинства приложений, есть несколько случаев, когда может потребоваться пользовательская сушка:
- Более эффективное управление большими (сложными) коллекциями росчерков пера
- Более эффективная поддержка сдвига и масштабирования на больших полотнах рукописного ввода
- Чередование рукописного ввода и других объектов, таких как фигуры или текст с сохранением z-порядка 
- Сушка и синхронное преобразование рукописного ввода в форму DirectX (например, в прямую линию или растровую форму, интегрированную в содержимое приложения, а не в качестве отдельного слоя **InkCanvas**).

Для пользовательской сушки требуется объект [**IInkD2DRenderer**](https://docs.microsoft.com/windows/desktop/api/inkrenderer/nn-inkrenderer-iinkd2drenderer), который управляет рукописными данными и преобразовывает их в контекст устройства Direct2D вашего универсального приложения для Windows, вместо элемента управления по умолчанию [**InkCanvas**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.InkCanvas).

Вызывая [**ActivateCustomDrying**](https://docs.microsoft.com/uwp/api/windows.ui.input.inking.inkpresenter.activatecustomdrying) (до загрузки элемента [**InkCanvas**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.InkCanvas)), приложение создает объект [**InkSynchronizer**](https://docs.microsoft.com/uwp/api/Windows.UI.Input.Inking.InkSynchronizer), чтобы настроить отображение росчерка пера сухим в [**SurfaceImageSource**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.Imaging.SurfaceImageSource) или [**VirtualSurfaceImageSource**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.media.imaging.virtualsurfaceimagesource). 

Оба объекта [**SurfaceImageSource**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.Imaging.SurfaceImageSource) и [**VirtualSurfaceImageSource**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.media.imaging.virtualsurfaceimagesource) предоставляют приложению поверхность в форме DirectX для рисования, а затем объединяют все биты в содержимое приложения, несмотря на то, что VSIS предоставляет виртуальную поверхность, которая больше экрана, для эффективного сдвига и масштабирования. Поскольку визуальные обновления на этих поверхностях синхронизируются с потоком пользовательского интерфейса XAML, при отображении рукописного ввода на любой из них влажные рукописные данные могут быть одновременно удалены из InkCanvas. 

Вы также можете выполнить пользовательскую сушку данных рукописного ввода для [SwapChainPanel](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.swapchainpanel), однако синхронизация с потоком пользовательского интерфейса не гарантируется, и может возникнуть задержка между тем, когда данные рукописного ввода отображаются в SwapChainPanel и тем, когда данные рукописного ввода удаляются из InkCanvas.

Полный пример этой функциональности см. в статье [Сложный пример рукописного ввода](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/ComplexInk).

> [!NOTE]
> Пользовательская сушка и [**InkToolbar**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.inktoolbar)  
> Если ваше приложение переопределяет поведение по умолчанию для отрисовки рукописных данных объекта [**InkPresenter**](https://docs.microsoft.com/uwp/api/Windows.UI.Input.Inking.InkPresenter) пользовательской реализацией сушки, отрисованные росчерки пера более недоступны для InkToolbar, а встроенные команды стирания InkToolbar не работают должным образом. Для реализации функции стирания необходимо обрабатывать все события указателя, выполнять проверку нажатия для каждого штриха и переопределить встроенную команду «Удалить все рукописные данные».

## <a name="other-articles-in-this-section"></a>Другие статьи в этом разделе

| Статья | Описание |
| --- | --- |
| [Распознавание рукописных штрихов](convert-ink-to-text.md) | Преобразуйте росчерки пера в текст с помощью распознавания рукописного ввода или в фигуры с помощью настраиваемого распознавания. |
| [Хранение и получение рукописных штрихов](save-and-load-ink.md) | Храните данные росчерка пера в GIF-файле, используя встроенные метаданные формата Ink Serialized Format (ISF). |
| [Добавление Инктулбар в приложение для рукописного ввода UWP](ink-toolbar.md) | Добавьте InkToolbar по умолчанию в приложение универсальной платформы Windows (UWP) для рукописного ввода, добавьте кнопку специального пера в InkToolbar и привяжите кнопку специального пера к определению специального пера. |

## <a name="related-articles"></a>Смежные разделы

* [Начало работы. Поддержка рукописного ввода в приложении UWP](../../get-started/ink-walkthrough.md)
* [Работа с данными указателя](handle-pointer-input.md)
* [Распознавание устройств ввода](identify-input-devices.md)

**API-интерфейсы**

* [**Windows. Devices. input**](https://docs.microsoft.com/uwp/api/Windows.Devices.Input)
* [**Windows. UI. input. рукописный ввод**](https://docs.microsoft.com/uwp/api/Windows.UI.Input.Inking)
* [**Windows. UI. input. Ввод рукописного ввода. Core**](https://docs.microsoft.com/uwp/api/Windows.UI.Input.Inking.Core)

**Примеры**
* [Руководство по началу работы. Поддержка рукописного ввода в приложении UWP](https://github.com/Microsoft/Windows-tutorials-inputs-and-devices/tree/master/GettingStarted-Ink)
* [Пример простого рукописногоC#вводаC++(/)](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/SimpleInk)
* [Образец сложных рукописныхC++данных ()](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/ComplexInk)
* [Образец рукописного ввода (JavaScript)](https://go.microsoft.com/fwlink/p/?LinkID=620308)
* [Образец цветовой книги](https://github.com/Microsoft/Windows-appsample-coloringbook)
* [Образец заметок для семьи](https://github.com/Microsoft/Windows-appsample-familynotes)
* [Пример базового ввода](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/BasicInput)
* [Пример ввода с небольшой задержкой](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/LowLatencyInput)
* [Пример режима взаимодействия с пользователем](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/UserInteractionMode)
* [Пример визуальных элементов фокуса](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/XamlFocusVisuals)

**Примеры из архива**
* [Входные данные: пример возможностей устройства](https://code.msdn.microsoft.com/windowsapps/Input-device-capabilities-31b67745)
* [Входные данные: пример событий пользовательского ввода XAML](https://code.msdn.microsoft.com/windowsapps/Input-3dff271b)
* [Пример прокрутки XAML, панорамирования и масштабирования](https://code.msdn.microsoft.com/windowsapps/xaml-scrollviewer-pan-and-949d29e9)
* [Входные данные: жесты и манипуляции с GestureRecognizer](https://code.msdn.microsoft.com/windowsapps/Manipulations-and-gestures-362b6b59)

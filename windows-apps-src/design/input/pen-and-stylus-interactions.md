---
Description: Build Universal Windows Platform (UWP) apps that support custom interactions from pen and stylus devices, including digital ink for natural writing and drawing experiences.
title: Взаимодействие с пером и Windows Ink в приложениях UWP
ms.assetid: 3DA4F2D2-5405-42A1-9ED9-3A87BCD84C43
label: Pen interactions and Windows Ink in UWP apps
template: detail.hbs
keywords: Windows Ink, Windows Inking, DirectInk, InkPresenter, InkCanvas, распознавание рукописного ввода, взаимодействие с пользователем, ввод
ms.date: 02/08/2017
ms.topic: article
ms.localizationpriority: medium
ms.openlocfilehash: 22477ab0facfcb67d44057a91c7c3a49df57f8b9
ms.sourcegitcommit: b5c9c18e70625ab770946b8243f3465ee1013184
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/29/2018
ms.locfileid: "7988261"
---
# <a name="pen-interactions-and-windows-ink-in-uwp-apps"></a>Взаимодействие с пером и Windows Ink в приложениях UWP

![Ручка Surface](images/ink/hero-small.png)  
*Ручка Surface* (доступна для приобретения в [Магазине Microsoft](https://aka.ms/purchasesurfacepen)).

## <a name="overview"></a>Обзор

Оптимизируйте взаимодействия с помощью пера в своем приложении универсальной платформы Windows (UWP), чтобы обеспечить как стандартные функциональные возможности для работы с [**указателем**](https://msdn.microsoft.com/library/windows/apps/br225633), так и возможности Windows Ink.

> [!NOTE]
> Данный раздел посвящен платформе Windows Ink. Общую информацию о работе с входными данными указателей, аналогично мыши, сенсорному экрану и сенсорной панели, см. в разделе [Работа с входными данными указателя](handle-pointer-input.md).

| Видео |   |
| --- | --- |
| <iframe src="https://channel9.msdn.com/Blogs/One-Dev-Minute/Using-Ink-in-Your-UWP-App/player" width="300" height="200" allowFullScreen frameBorder="0"></iframe> | <iframe src="https://channel9.msdn.com/Events/Ignite/2016/BRK2060/player" width="300" height="200" allowFullScreen frameBorder="0"></iframe> |
| *Использование рукописного ввода в приложении UWP* | *Использование пера Windows и рукописного ввода для создания более привлекательных enterpriseapps* |

Платформа Windows Ink — наряду с пером — обеспечивает естественный способ создания цифровых рукописных примечаний, рисунков и заметок. Платформа поддерживает получение входных данных дигитайзера в виде рукописных данных, создание рукописных данных, управление этими данными, отображение их в виде росчерков пера на устройстве вывода и преобразование этих росчерков в текст с помощью функции распознавания рукописного ввода.

В дополнение к получению данных об основном положении и движении пера, когда пользователь пишет или рисует, ваше приложение также может отслеживать и собирать сведения об изменяющейся силе нажатия, используемой в росчерке. Эти сведения вместе с параметрами формы, размера и поворота кончика пера, цвета чернил и цели (обычный рукописный ввод, стирание, выделение и выбор) позволяют обеспечить результат взаимодействия, очень похожий на письмо или рисунок ручкой, карандашом или кистью на бумаге.

> [!NOTE]
> Ваше приложение может также поддерживать рукописный ввод и от других устройств на основе указателя, например дигитайзеров сенсорного ввода и мыши. 

Платформа рукописного ввода очень гибкая. Она предназначена для поддержки различных уровней функциональности в зависимости от ваших требований.

Рекомендации по взаимодействию с пользователем для Windows Ink см. в разделе [Элементы управления рукописным вводом](../controls-and-patterns/inking-controls.md).

## <a name="components-of-the-windows-ink-platform"></a>Компоненты платформы Windows Ink

| Компонент | Описание |
| --- | --- |
| [**InkCanvas**](https://msdn.microsoft.com/library/windows/apps/dn858535) | Элемент управления платформы XAMLUI, который по умолчанию получает и отображает все входные данные от пера как росчерк пера или росчерк стирания.<br/>Дополнительные сведения о том, как использовать InkCanvas, см. в разделах [Распознавание росчерков пера Windows Ink как текста](convert-ink-to-text.md) и [Хранение и извлечение данных движения пера Windows Ink](save-and-load-ink.md). |
| [**InkPresenter**](https://msdn.microsoft.com/library/windows/apps/dn922011) | Объект кода программной части, создаваемый вместе с элементом управления [**InkCanvas**](https://msdn.microsoft.com/library/windows/apps/dn858535) (предоставляется свойством [**InkCanvas.InkPresenter**](https://msdn.microsoft.com/library/windows/apps/dn899081)). Этот объект обеспечивает все возможности рукописного ввода по умолчанию, предоставляемые элементом **InkCanvas**, вместе с полным набором API для дополнительной настройки и персонализации.<br/>Дополнительные сведения о том, как использовать InkPresenter, см. в разделах [Распознавание росчерков пера Windows Ink как текста](convert-ink-to-text.md) и [Хранение и извлечение данных движения пера Windows Ink](save-and-load-ink.md). |
| [**InkToolbar**](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.inktoolbar.aspx) | Элемент управления платформы XAMLUI, содержащий и расширяемую коллекцию кнопок, которые активируют функции рукописного ввода в связанных [**InkCanvas**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.inkcanvas).<br/>Дополнительные сведения о том, как использовать InkToolbar, см. в разделе [Добавление InkToolbar в приложение универсальной платформы Windows (UWP) для рукописного ввода](ink-toolbar.md). |
| [**IInkD2DRenderer**](https://msdn.microsoft.com/library/mt147263) | Позволяет преобразовать росчерки пера в специальный контекст устройства Direct2D универсального приложения для Windows вместо элемента управления по умолчанию [**InkCanvas**](https://msdn.microsoft.com/library/windows/apps/dn858535). Это делает возможным полную настройку рукописного ввода.<br/>Дополнительные сведения см. в разделе [Сложный пример рукописного ввода](https://go.microsoft.com/fwlink/p/?LinkID=620314). |

## <a name="basic-inking-with-inkcanvas"></a>Базовый рукописный ввод с использованием InkCanvas

Чтобы добавить базовую функцию рукописного ввода, просто поместите элемент управления платформы UWP [**InkCanvas**](https://msdn.microsoft.com/library/windows/apps/dn858535) на соответствующую страницу в приложении.

По умолчанию [**InkCanvas**](https://msdn.microsoft.com/library/windows/apps/dn858535) поддерживает рукописный ввод только с помощью пера. Входные данные обрабатываются как росчерк пера с использованием параметров цвета и толщины по умолчанию (черная шариковая ручка с толщиной 2 пикселя) или трактуются как ластик для росчерков (когда ввод осуществляется кончиком ластика или кончиком пера, модифицированным при помощи кнопки стирания).

> [!NOTE]
> Если кнопка или кончик ластика отсутствуют, InkCanvas можно настроить для обработки ввода кончика пера как росчерка стирания.

В этом примере [**InkCanvas**](https://msdn.microsoft.com/library/windows/apps/dn858535) перекрывает фоновое изображение.

> [!NOTE]
> InkCanvas по умолчанию имеет значения свойств [**Height**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.frameworkelement.Height) и [**Width**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.frameworkelement.Width) равными нулю, если он не является дочерним элементом, который автоматически задает размеры своих дочерних элементов. 

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

В этой серии изображений показано, как ввод с помощью пера обрабатывается этим элементом управления [**InkCanvas**](https://msdn.microsoft.com/library/windows/apps/dn858535).

| ![Пустой элемент InkCanvas с фоновым изображением](images/ink_basic_1_small.png) | ![InkCanvas с росчерками пера](images/ink_basic_2_small.png) | ![InkCanvas с одним стертым росчерком](images/ink_basic_3_small.png) |
| --- | --- | ---|
| Пустой элемент [**InkCanvas**](https://msdn.microsoft.com/library/windows/apps/dn858535) с фоновым изображением. | [**InkCanvas**](https://msdn.microsoft.com/library/windows/apps/dn858535) с росчерками пера. | [**InkCanvas**](https://msdn.microsoft.com/library/windows/apps/dn858535) с одним росчерком (обратите внимание, как операция стирания применяется ко всему росчерку, а не его части). |

Возможности рукописного ввода, поддерживаемые элементом управления [**InkCanvas**](https://msdn.microsoft.com/library/windows/apps/dn858535), предоставляются объектом кода программной части [**InkPresenter**](https://msdn.microsoft.com/library/windows/apps/dn922011).

При выполнении базового рукописного ввода вам не нужно беспокоиться об элементе [**InkPresenter**](https://msdn.microsoft.com/library/windows/apps/dn922011). Однако для настройки и конфигурации реакции на рукописный ввод в элементе [**InkCanvas**](https://msdn.microsoft.com/library/windows/apps/dn858535) необходимо получить доступ к соответствующему объекту **InkPresenter**.

## <a name="basic-customization-with-inkpresenter"></a>Базовая настройка с использованием InkPresenter

Объект [**InkPresenter**](https://msdn.microsoft.com/library/windows/apps/dn922011) создается с каждым элементом управления [**InkCanvas**](https://msdn.microsoft.com/library/windows/apps/dn858535).

> [!NOTE]
> [**InkPresenter**](https://msdn.microsoft.com/library/windows/apps/dn922011) нельзя создавать напрямую. Вместо этого доступ к нему осуществляется с помощью свойства [**InkPresenter**](https://msdn.microsoft.com/library/windows/apps/dn899081) элемента [**InkCanvas**](https://msdn.microsoft.com/library/windows/apps/dn858535). 

Помимо всех реакций на рукописный ввод по умолчанию для соответствующего элемента управления [**InkCanvas**](https://msdn.microsoft.com/library/windows/apps/dn858535), [**InkPresenter**](https://msdn.microsoft.com/library/windows/apps/dn922011) предоставляет полный набор API для дополнительной настройки росчерков и более детализированного управления вводом с помощью пера (стандартного и измененного). Это касается свойств росчерков, поддерживаемых типов устройств ввода и того, обрабатываются входные данные объектом или передаются приложению для обработки.

> [!NOTE]
> Стандартный рукописный ввод (кончик пера или кнопка/кончик ластика) не изменены с помощью таких дополнительных возможностей, как кнопка пера, правая кнопка мыши или подобные механизмы. 

По умолчанию рукописный ввод поддерживает только ввод с помощью пера. Здесь мы настраиваем [**InkPresenter**](https://msdn.microsoft.com/library/windows/apps/dn899081) для интерпретации данных, вводимые пером или мышью, в качестве росчерков пера. Мы также задаем некоторые исходные атрибуты росчерка пера, используемые для вывода росчерков на [**InkCanvas**](https://msdn.microsoft.com/library/windows/apps/dn858535).

Чтобы включить рукописный ввод с помощью мыши и сенсорного ввода, задайте свойство [**InputDeviceTypes**](https://docs.microsoft.com/uwp/api/windows.ui.input.inking.inkpresenter.InputDeviceTypes) [**InkPresenter**](https://docs.microsoft.com/en-us/uwp/api/windows.ui.input.inking.inkpresenter) для сочетания значений [**CoreInputDeviceTypes**](https://docs.microsoft.com/uwp/api/windows.ui.core.coreinputdevicetypes), которые вам необходимы.

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

На этих изображениях показано, как ввод с помощью пера обрабатывается и настраивается элементом [**InkPresenter**](https://msdn.microsoft.com/library/windows/apps/dn899081).

| ![InkCanvas с черными росчерками пера по умолчанию](images/ink-basic-custom-1-small.png) | ![InkCanvas с выбранными пользователем красными росчерками пера](images/ink-basic-custom-2-small.png) |
| --- | --- |
| [**InkCanvas**](https://msdn.microsoft.com/library/windows/apps/dn858535) с черными росчерками пера по умолчанию. | [**InkCanvas**](https://msdn.microsoft.com/library/windows/apps/dn858535) с выбранными пользователем красными росчерками пера. | 

Для предоставления функций помимо рукописного ввода и стирания, таких как возможность выбора росчерка, ваше приложение должно определить конкретные входные данные для элемента [**InkPresenter**](https://msdn.microsoft.com/library/windows/apps/dn899081), чтобы передать необработанные данные для обработки вашим приложением.

## <a name="pass-through-input-for-advanced-processing"></a>Передача входных данных для расширенной обработки

По умолчанию [**InkPresenter**](https://msdn.microsoft.com/library/windows/apps/dn899081) обрабатывает все входные данные как росчерк пера или росчерк стирания, включая данные ввода, которые модифицированы дополнительной возможностью оборудования, например кнопкой пера, правой кнопкой мыши или чем-то подобным. Тем не менее при использовании этих дополнительных возможностей пользователи обычно ожидают большей функциональности или изменения поведения.

В некоторых случаях может также понадобиться предоставить дополнительные функции для пера без дополнительных возможностей (функций, которые обычно не связаны с кончиком пера), других типов устройств ввода или изменения поведения на основе выбора пользователя в пользовательском интерфейсе вашего приложения.

Для поддержки этого [**InkPresenter**](https://msdn.microsoft.com/library/windows/apps/dn899081) можно настроить оставлять определенные входные данные необработанными. Эти необработанные входные данные затем передаются в ваше приложение для обработки.

### <a name="example---use-unprocessed-input-to-implement-stroke-selection"></a>Пример. Использование необработанные входных данных для реализации возможности выбора росчерка 

Платформа Windows Ink не предоставляет встроенную поддержку для действий, требующих модификации ввода, таких как возможность выбора росчерка. Для обеспечения поддержки таких функций необходимо предоставить специальное решение в приложениях. 

В следующем примере кода (для размещения всего кода используются файлы MainPage.xaml и MainPage.xaml.cs) показаны этапы добавления возможности выбора росчерка, когда входные данные изменяются кнопкой пера (или правой кнопкой мыши).

1.  Сначала мы настраиваем пользовательский интерфейс в файле MainPage.xaml.

    Здесь мы добавляем холст (под элементом [**InkCanvas**](https://msdn.microsoft.com/library/windows/apps/dn858535)) для рисования росчерка выбора. Если использовать отдельный слой для рисования росчерка выбора, элемент **InkCanvas** и его содержимое останутся без изменений.

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

3.  Затем мы настраиваем [**InkPresenter**](https://msdn.microsoft.com/library/windows/apps/dn899081) интерпретировать данные, вводимые пером или мышью, как росчерки пера и задаем некоторые исходные атрибуты росчерка пера, используемые для вывода росчерков на [**InkCanvas**](https://msdn.microsoft.com/library/windows/apps/dn858535).

    Важнее всего то, что мы используем свойство [**InputProcessingConfiguration**](https://msdn.microsoft.com/library/windows/apps/dn948764) элемента [InkPresenter](https://msdn.microsoft.com/library/windows/apps/dn899081) для указания того, что любые измененные входные данные должны обрабатываться приложением. Измененные входные данные указываются путем назначения перечислению **InputProcessingConfiguration.RightDragAction** значения [**InkInputRightDragAction.LeaveUnprocessed**](https://msdn.microsoft.com/library/windows/apps/dn948760). Когда установлено это значение, элемент [InkPresenter](https://msdn.microsoft.com/library/windows/apps/dn899081) передается в класс [InkUnprocessedInput](https://docs.microsoft.com/uwp/api/windows.ui.input.inking.inkunprocessedinput) — набор событий указателя для обработки.

    Затем мы назначаем прослушиватели для необработанных событий [**PointerPressed**](https://msdn.microsoft.com/library/windows/apps/dn914712), [**PointerMoved**](https://msdn.microsoft.com/library/windows/apps/dn914711) и [**PointerReleased**](https://msdn.microsoft.com/library/windows/apps/dn914713), переданных элементом [**InkPresenter**](https://msdn.microsoft.com/library/windows/apps/dn899081). Все функции выбора реализованы в обработчиках для этих событий.

    Наконец, мы назначаем прослушиватели для событий [**StrokeStarted**](https://msdn.microsoft.com/library/windows/apps/dn914702) и [**StrokesErased**](https://msdn.microsoft.com/library/windows/apps/dn948767) элемента [**InkPresenter**](https://msdn.microsoft.com/library/windows/apps/dn899081). Мы используем обработчики для этих событий, чтобы очистить пользовательский интерфейс выбора, если начинается новый росчерк или стирается существующий.

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

4.  Затем мы определяем обработчики для необработанных событий [**PointerPressed**](https://msdn.microsoft.com/library/windows/apps/dn914712), [**PointerMoved**](https://msdn.microsoft.com/library/windows/apps/dn914711) и [**PointerReleased**](https://msdn.microsoft.com/library/windows/apps/dn914713), переданных элементом [**InkPresenter**](https://msdn.microsoft.com/library/windows/apps/dn899081).

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

6.  Наконец мы определяем обработчики для событий InkPresenter [**StrokeStarted**](https://msdn.microsoft.com/library/windows/apps/dn914702) и [**StrokesErased**](https://msdn.microsoft.com/library/windows/apps/dn948767).

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

По умолчанию рукописные данные обрабатываются в фоновом потоке с низкой задержкой и отображаются во время выполнения как "влажные" в процессе ввода. После завершения росчерка (когда пользователь поднял перо или палец либо отпустил кнопку мыши) росчерк обрабатывается в потоке пользовательского интерфейса и отображается «сухим» на слое [**InkCanvas**](https://msdn.microsoft.com/library/windows/apps/dn858535) (над содержимым приложения, вместо влажных рукописных данных).

Вы можете переопределить это поведение по умолчанию и полностью управлять рукописным вводом за счет "пользовательской сушки" влажных росчерков пера. Тогда как поведения по умолчанию обычно достаточно для большинства приложений, есть несколько случаев, когда может потребоваться пользовательская сушка:
- Более эффективное управление большими (сложными) коллекциями росчерков пера
- Более эффективная поддержка сдвига и масштабирования на больших полотнах рукописного ввода
- Чередование рукописного ввода и других объектов, таких как фигуры или текст с сохранением z-порядка 
- Сушка и синхронное преобразование рукописного ввода в форму DirectX (например, в прямую линию или растровую форму, интегрированную в содержимое приложения, а не в качестве отдельного слоя **InkCanvas**).

Для пользовательской сушки требуется объект [**IInkD2DRenderer**](https://msdn.microsoft.com/library/mt147263), который управляет рукописными данными и преобразовывает их в контекст устройства Direct2D вашего универсального приложения для Windows, вместо элемента управления по умолчанию [**InkCanvas**](https://msdn.microsoft.com/library/windows/apps/dn858535).

Вызывая [**ActivateCustomDrying**](https://msdn.microsoft.com/library/windows/apps/dn922012) (до загрузки элемента [**InkCanvas**](https://msdn.microsoft.com/library/windows/apps/dn858535)), приложение создает объект [**InkSynchronizer**](https://msdn.microsoft.com/library/windows/apps/dn903979), чтобы настроить отображение росчерка пера сухим в [**SurfaceImageSource**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.Imaging.SurfaceImageSource) или [**VirtualSurfaceImageSource**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.media.imaging.virtualsurfaceimagesource). 

Оба объекта [**SurfaceImageSource**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.Imaging.SurfaceImageSource) и [**VirtualSurfaceImageSource**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.media.imaging.virtualsurfaceimagesource) предоставляют приложению поверхность в форме DirectX для рисования, а затем объединяют все биты в содержимое приложения, несмотря на то, что VSIS предоставляет виртуальную поверхность, которая больше экрана, для эффективного сдвига и масштабирования. Поскольку визуальные обновления на этих поверхностях синхронизируются с потоком пользовательского интерфейса XAML, при отображении рукописного ввода на любой из них влажные рукописные данные могут быть одновременно удалены из InkCanvas. 

Вы также можете выполнить пользовательскую сушку данных рукописного ввода для [SwapChainPanel](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.swapchainpanel), однако синхронизация с потоком пользовательского интерфейса не гарантируется, и может возникнуть задержка между тем, когда данные рукописного ввода отображаются в SwapChainPanel и тем, когда данные рукописного ввода удаляются из InkCanvas.

Полный пример этой функциональности см. в статье [Сложный пример рукописного ввода](https://go.microsoft.com/fwlink/p/?LinkID=620314).

> [!NOTE]
> Пользовательская сушка и [**InkToolbar**](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.inktoolbar.aspx)  
> Если ваше приложение переопределяет поведение по умолчанию для отрисовки рукописных данных объекта [**InkPresenter**](https://msdn.microsoft.com/library/windows/apps/dn922011) пользовательской реализацией сушки, отрисованные росчерки пера более недоступны для InkToolbar, а встроенные команды стирания InkToolbar не работают должным образом. Для реализации функции стирания необходимо обрабатывать все события указателя, выполнять проверку нажатия для каждого штриха и переопределить встроенную команду «Удалить все рукописные данные».

## <a name="other-articles-in-this-section"></a>Другие статьи в этом разделе

| Тема | Описание |
| --- | --- |
| [Распознавание росчерков пера](convert-ink-to-text.md) | Преобразуйте росчерки пера в текст с помощью распознавания рукописного ввода или в фигуры с помощью настраиваемого распознавания. |
| [Хранение и извлечение росчерков пера](save-and-load-ink.md) | Храните данные росчерка пера в GIF-файле, используя встроенные метаданные формата Ink Serialized Format (ISF). |
| [Добавление InkToolbar в приложение UWP для рукописного ввода](ink-toolbar.md) | Добавьте InkToolbar по умолчанию в приложение универсальной платформы Windows (UWP) для рукописного ввода, добавьте кнопку специального пера в InkToolbar и привяжите кнопку специального пера к определению специального пера. |

## <a name="related-articles"></a>Статьи по теме

* [Начало работы: поддержка рукописного ввода в приложении UWP](../../get-started/ink-walkthrough.md)
* [Работа с входными данными указателя](handle-pointer-input.md)
* [Определение устройств ввода](identify-input-devices.md)

**Интерфейсы API**

* [**Windows.Devices.Input**](https://msdn.microsoft.com/library/windows/apps/br225648)
* [**Windows.UI.Input.Inking**](https://msdn.microsoft.com/library/windows/apps/br208524)
* [**Windows.UI.Input.Inking.Core**](https://msdn.microsoft.com/library/windows/apps/dn958452)

**Примеры**
* [Руководство по началу работы: поддержка рукописного ввода в приложении UWP](https://aka.ms/appsample-ink)
* [Простой пример рукописного ввода (C#/C++)](https://go.microsoft.com/fwlink/p/?LinkID=620312)
* [Сложный пример рукописного ввода (C++)](https://go.microsoft.com/fwlink/p/?LinkID=620314)
* [Пример рукописного ввода (JavaScript)](https://go.microsoft.com/fwlink/p/?LinkID=620308)
* [Пример раскраски](https://aka.ms/cpubsample-coloringbook)
* [Пример семейных заметок](https://aka.ms/cpubsample-familynotessample)
* [Пример базового ввода](https://go.microsoft.com/fwlink/p/?LinkID=620302)
* [Пример ввода с малой задержкой](https://go.microsoft.com/fwlink/p/?LinkID=620304)
* [Пример режима взаимодействия с пользователем](https://go.microsoft.com/fwlink/p/?LinkID=619894)
* [Пример визуальных элементов фокуса](https://go.microsoft.com/fwlink/p/?LinkID=619895)

**Примеры архивов**
* [Ввод: пример возможностей устройства](https://go.microsoft.com/fwlink/p/?linkid=231530)
* [Ввод: пример событий пользовательского ввода на XAML](https://go.microsoft.com/fwlink/p/?linkid=226855)
* [Пример прокрутки, сдвига и масштабирования на XAML](https://go.microsoft.com/fwlink/p/?linkid=251717)
* [Ввод: жесты и манипуляции с помощью GestureRecognizer](https://go.microsoft.com/fwlink/p/?LinkID=231605)

---
author: Karl-Bridge-Microsoft
Description: "Преобразуйте росчерки пера в текст с помощью распознавания рукописного ввода или в фигуры с помощью настраиваемого распознавания."
title: "Распознавание росчерков пера Windows Ink как текста"
ms.assetid: C2F3F3CE-737F-4652-98B7-5278A462F9D3
label: Recognize Windows Ink strokes as text
template: detail.hbs
keywords: "Windows Ink, Windows Inking, DirectInk, InkPresenter, InkCanvas, распознавание рукописного ввода, взаимодействие с пользователем, ввод"
ms.author: kbridge
ms.date: 02/08/2017
ms.topic: article
ms.prod: windows
ms.technology: uwp
ms.openlocfilehash: 555e340d55c9a2fec6204ffd4759e17d68d8a746
ms.sourcegitcommit: 909d859a0f11981a8d1beac0da35f779786a6889
translationtype: HT
---
# <a name="recognize-windows-ink-strokes-as-text"></a>Распознавание росчерков пера Windows Ink как текста
<link rel="stylesheet" href="https://az835927.vo.msecnd.net/sites/uwp/Resources/css/custom.css">

Преобразуйте росчерки пера в текст с помощью функции распознавания рукописного ввода в Windows Ink.

<div class="important-apis" >
<b>Важные API</b><br/>
<ul>
<li>[**InkCanvas**](https://msdn.microsoft.com/library/windows/apps/dn858535)</li>
<li>[**Windows.UI.Input.Inking**](https://msdn.microsoft.com/library/windows/apps/br208524)</li>
</ul>
</div> 


Распознавание рукописного ввода встроено в платформу рукописного ввода Windows и поддерживает множество языковых стандартов и языков.

Во всех приведенных здесь примерах добавьте ссылки на пространства имен, необходимые для функции рукописного ввода. Это относится к "Windows.UI.Input.Inking".

## <a name="basic-handwriting-recognition"></a>Базовое распознавание рукописного ввода


Здесь мы покажем, как использовать модуль распознавания рукописного ввода, связанный с установленным по умолчанию языковым пакетом, для интерпретации набора росчерков на [**InkCanvas**](https://msdn.microsoft.com/library/windows/apps/dn858535).

Распознавание вызывается пользователем путем нажатия кнопки после того, как он закончил писать.

1.  Сначала мы настраиваем пользовательский интерфейс.

    Пользовательский интерфейс включает кнопку "Распознать", [**InkCanvas**](https://msdn.microsoft.com/library/windows/apps/dn858535) и область для отображения результатов распознавания.    
```    XAML
<Grid Background="{ThemeResource ApplicationPageBackgroundThemeBrush}">
        <Grid.RowDefinitions>
            <RowDefinition Height="Auto"/>
            <RowDefinition Height="*"/>
        </Grid.RowDefinitions>
        <StackPanel x:Name="HeaderPanel"
                    Orientation="Horizontal"
                    Grid.Row="0">
            <TextBlock x:Name="Header"
                       Text="Basic ink recognition sample"
                       Style="{ThemeResource HeaderTextBlockStyle}"
                       Margin="10,0,0,0" />
            <Button x:Name="recognize"
                    Content="Recognize"
                    Margin="50,0,10,0"/>
        </StackPanel>
        <Grid Grid.Row="1">
            <Grid.RowDefinitions>
                <RowDefinition Height="*"/>
                <RowDefinition Height="Auto"/>
            </Grid.RowDefinitions>
            <InkCanvas x:Name="inkCanvas"
                       Grid.Row="0"/>
            <TextBlock x:Name="recognitionResult"
                       Grid.Row="1"
                       Margin="50,0,10,0"/>
        </Grid>
    </Grid>
```

2.  Затем мы задаем некоторые основные реакции на рукописный ввод.

    Элемент [**InkPresenter**](https://msdn.microsoft.com/library/windows/apps/dn899081) настраивается интерпретировать данные, вводимые пером или мышью, как росчерки пера ([**InputDeviceTypes**](https://msdn.microsoft.com/library/windows/apps/dn922019)). Росчерки пера выводятся на [**InkCanvas**](https://msdn.microsoft.com/library/windows/apps/dn858535) с помощью указанных атрибутов [**InkDrawingAttributes**](https://msdn.microsoft.com/library/windows/desktop/ms695050). Также объявляется прослушиватель для события нажатия кнопки "Распознать".
```    CSharp
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

        // Listen for button click to initiate recognition.
        recognize.Click += Recognize_Click;
    }
```

3.  Наконец, мы выполняем базовое распознавание рукописного ввода. В этом примере для выполнения распознавания рукописного ввода мы используем обработчик событий для нажатия кнопки "Распознать".

    [**InkPresenter**](https://msdn.microsoft.com/library/windows/apps/dn899081) сохраняет все росчерки пера в объекте [**InkStrokeContainer**](https://msdn.microsoft.com/library/windows/apps/br208492). Росчерки отображаются через свойство [**StrokeContainer**](https://msdn.microsoft.com/library/windows/apps/dn948766) **InkPresenter** и извлекаются методом [**GetStrokes**](https://msdn.microsoft.com/library/windows/apps/br208499).
```    CSharp
// Get all strokes on the InkCanvas.
    IReadOnlyList<InkStroke> currentStrokes = inkCanvas.InkPresenter.StrokeContainer.GetStrokes();
```

    An [**InkRecognizerContainer**](https://msdn.microsoft.com/library/windows/apps/br208479) is created to manage the handwriting recognition process.
```    CSharp
// Create a manager for the InkRecognizer object
    // used in handwriting recognition.
    InkRecognizerContainer inkRecognizerContainer =
        new InkRecognizerContainer();
```

    [**RecognizeAsync**](https://msdn.microsoft.com/library/windows/apps/br208446) is called to retrieve a set of [**InkRecognitionResult**](https://msdn.microsoft.com/library/windows/apps/br208464) objects.

    Recognition results are produced for each word that is detected by an [**InkRecognizer**](https://msdn.microsoft.com/library/windows/apps/br208478).
```    CSharp
// Recognize all ink strokes on the ink canvas.
    IReadOnlyList<InkRecognitionResult> recognitionResults =
        await inkRecognizerContainer.RecognizeAsync(
            inkCanvas.InkPresenter.StrokeContainer,
            InkRecognitionTarget.All);
```

    Each [**InkRecognitionResult**](https://msdn.microsoft.com/library/windows/apps/br208464) object contains a set of text candidates. The topmost item in this list is considered by the recognition engine to be the best match, followed by the remaining candidates in order of decreasing confidence.

    We iterate through each [**InkRecognitionResult**](https://msdn.microsoft.com/library/windows/apps/br208464) and compile the list of candidates. The candidates are then displayed and the [**InkStrokeContainer**](https://msdn.microsoft.com/library/windows/apps/br208492) is cleared (which also clears the [**InkCanvas**](https://msdn.microsoft.com/library/windows/apps/dn858535)).
```    CSharp
string str = "Recognition result\n";
    // Iterate through the recognition results.
    foreach (var result in recognitionResults)
    {
        // Get all recognition candidates from each recognition result.
        IReadOnlyList<string> candidates = result.GetTextCandidates();
        str += "Candidates: " + candidates.Count.ToString() + "\n";
        foreach (string candidate in candidates)
        {
            str += candidate + " ";
        }
    }
    // Display the recognition candidates.
    recognitionResult.Text = str;
    // Clear the ink canvas once recognition is complete.
    inkCanvas.InkPresenter.StrokeContainer.Clear();
```

    Here's the click handler example, in full.
```    CSharp
// Handle button click to initiate recognition.
    private async void Recognize_Click(object sender, RoutedEventArgs e)
    {
        // Get all strokes on the InkCanvas.
        IReadOnlyList<InkStroke> currentStrokes = inkCanvas.InkPresenter.StrokeContainer.GetStrokes();

        // Ensure an ink stroke is present.
        if (currentStrokes.Count > 0)
        {
            // Create a manager for the InkRecognizer object
            // used in handwriting recognition.
            InkRecognizerContainer inkRecognizerContainer =
                new InkRecognizerContainer();

            // inkRecognizerContainer is null if a recognition engine is not available.
            if (!(inkRecognizerContainer == null))
            {
                // Recognize all ink strokes on the ink canvas.
                IReadOnlyList<InkRecognitionResult> recognitionResults =
                    await inkRecognizerContainer.RecognizeAsync(
                        inkCanvas.InkPresenter.StrokeContainer,
                        InkRecognitionTarget.All);
                // Process and display the recognition results.
                if (recognitionResults.Count > 0)
                {
                    string str = "Recognition result\n";
                    // Iterate through the recognition results.
                    foreach (var result in recognitionResults)
                    {
                        // Get all recognition candidates from each recognition result.
                        IReadOnlyList<string> candidates = result.GetTextCandidates();
                        str += "Candidates: " + candidates.Count.ToString() + "\n";
                        foreach (string candidate in candidates)
                        {
                            str += candidate + " ";
                        }
                    }
                    // Display the recognition candidates.
                    recognitionResult.Text = str;
                    // Clear the ink canvas once recognition is complete.
                    inkCanvas.InkPresenter.StrokeContainer.Clear();
                }
                else
                {
                    recognitionResult.Text = "No recognition results.";
                }
            }
            else
            {
                Windows.UI.Popups.MessageDialog messageDialog = new Windows.UI.Popups.MessageDialog("You must install handwriting recognition engine.");
                await messageDialog.ShowAsync();
            }
        }
        else
        {
            recognitionResult.Text = "No ink strokes to recognize.";
        }
    }
```

## <a name="international-recognition"></a>Международное распознавание


Для распознавания рукописного ввода можно использовать обширное подмножество языков, поддерживаемых Windows.

Список языков, поддерживаемых [**InkRecognizer**](https://msdn.microsoft.com/library/windows/apps/br208478), содержится в свойстве [**InkRecognizer.Name**](https://msdn.microsoft.com/library/windows/apps/windows.ui.input.inking.inkrecognizer.name.aspx).

Ваше приложение может запросить набор установленных модулей распознавания рукописного ввода и использовать один из них или позволить пользователю выбрать предпочитаемый язык.

**Примечание**  
Пользователи могут просмотреть список установленных языков в меню **Параметры -&gt; Время и язык**. Установленные языки перечислены в разделе **Языки**.

Вот как установить новые языковые пакеты и включить распознавание рукописного ввода для конкретного языка.

1.  Перейдите в меню **Параметры &gt; Время и язык &gt; Язык и региональные стандарты**.
2.  Выберите **Добавить язык**.
3.  Выберите язык из списка, а затем — региональную версию. Язык теперь указан на странице **Язык и региональные стандарты**.
4.  Щелкните язык и выберите **Параметры**.
5.  На странице **Параметры языка** скачайте **Модуль распознавания рукописного ввода** (здесь также можно скачать полный языковой пакет, модуль распознавания речи и раскладку клавиатуры).

 

Здесь мы покажем, как использовать модуль распознавания рукописного ввода для интерпретации набора росчерков на [**InkCanvas**](https://msdn.microsoft.com/library/windows/apps/dn858535) на основе выбранного распознавателя.

Распознавание вызывается пользователем путем нажатия кнопки после того, как он закончил писать.

1.  Сначала мы настраиваем пользовательский интерфейс.

    Пользовательский интерфейс включает кнопку "Распознать", поле со списком всех установленных распознавателей рукописного ввода, [**InkCanvas**](https://msdn.microsoft.com/library/windows/apps/dn858535) и область для отображения результатов распознавания.
```    XAML
<Grid Background="{ThemeResource ApplicationPageBackgroundThemeBrush}">
        <Grid.RowDefinitions>
            <RowDefinition Height="Auto"/>
            <RowDefinition Height="*"/>
        </Grid.RowDefinitions>
        <StackPanel x:Name="HeaderPanel"
                    Orientation="Horizontal"
                    Grid.Row="0">
            <TextBlock x:Name="Header"
                       Text="Advanced international ink recognition sample"
                       Style="{ThemeResource HeaderTextBlockStyle}"
                       Margin="10,0,0,0" />
            <ComboBox x:Name="comboInstalledRecognizers"
                     Margin="50,0,10,0">
                <ComboBox.ItemTemplate>
                    <DataTemplate>
                        <StackPanel Orientation="Horizontal">
                            <TextBlock Text="{Binding Name}" />
                        </StackPanel>
                    </DataTemplate>
                </ComboBox.ItemTemplate>
            </ComboBox>
            <Button x:Name="buttonRecognize"
                    Content="Recognize"
                    IsEnabled="False"
                    Margin="50,0,10,0"/>
        </StackPanel>
        <Grid Grid.Row="1">
            <Grid.RowDefinitions>
                <RowDefinition Height="*"/>
                <RowDefinition Height="Auto"/>
            </Grid.RowDefinitions>
            <InkCanvas x:Name="inkCanvas"
                       Grid.Row="0"/>
            <TextBlock x:Name="recognitionResult"
                       Grid.Row="1"
                       Margin="50,0,10,0"/>
        </Grid>
    </Grid>
```

2.  Затем мы задаем некоторые основные реакции на рукописный ввод.

    Элемент [**InkPresenter**](https://msdn.microsoft.com/library/windows/apps/dn899081) настраивается интерпретировать данные, вводимые пером или мышью, как росчерки пера ([**InputDeviceTypes**](https://msdn.microsoft.com/library/windows/apps/dn922019)). Росчерки пера выводятся на [**InkCanvas**](https://msdn.microsoft.com/library/windows/apps/dn858535) с помощью указанных атрибутов [**InkDrawingAttributes**](https://msdn.microsoft.com/library/windows/desktop/ms695050).

    Мы вызываем функцию `InitializeRecognizerList`, чтобы заполнить поле со списком распознавателя списком установленных распознавателей рукописного ввода.

    Мы также объявляем прослушиватели для события нажатия кнопки "Распознать" и для события изменения выбора в поле со списком распознавателя.
```    CSharp
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

         // Populate the recognizer combo box with installed recognizers.
         InitializeRecognizerList();

         // Listen for combo box selection.
         comboInstalledRecognizers.SelectionChanged +=
             comboInstalledRecognizers_SelectionChanged;

         // Listen for button click to initiate recognition.
         buttonRecognize.Click += Recognize_Click;
     }
```

3.  Мы заполняем поле со списком распознавателя списком установленных распознавателей рукописного ввода.

    Объект [**InkRecognizerContainer**](https://msdn.microsoft.com/library/windows/apps/br208479) создается для управления процессом распознавания рукописного ввода. Используйте этот объект, чтобы вызвать [**GetRecognizers**](https://msdn.microsoft.com/library/windows/apps/br208480) и извлечь список установленных распознавателей для заполнения поля со списком распознавателя.
```    CSharp
// Populate the recognizer combo box with installed recognizers.
    private void InitializeRecognizerList()
    {
        // Create a manager for the handwriting recognition process.
        inkRecognizerContainer = new InkRecognizerContainer();
        // Retrieve the collection of installed handwriting recognizers.
        IReadOnlyList<InkRecognizer> installedRecognizers =
            inkRecognizerContainer.GetRecognizers();
        // inkRecognizerContainer is null if a recognition engine is not available.
        if (!(inkRecognizerContainer == null))
        {
            comboInstalledRecognizers.ItemsSource = installedRecognizers;
            buttonRecognize.IsEnabled = true;
        }
    }
```

4.  Обновите распознаватель рукописного ввода при изменении выбора в поле со списком распознавателя.

    Используйте [**InkRecognizerContainer**](https://msdn.microsoft.com/library/windows/apps/br208479), чтобы вызвать [**SetDefaultRecognizer**](https://msdn.microsoft.com/library/windows/apps/hh920328) на основе распознавателя, выбранного в поле со списком распознавателя.
```    CSharp
// Handle recognizer change.
    private void comboInstalledRecognizers_SelectionChanged(
        object sender, SelectionChangedEventArgs e)
    {
        inkRecognizerContainer.SetDefaultRecognizer(
            (InkRecognizer)comboInstalledRecognizers.SelectedItem);
    }
```

5.  Наконец, мы выполняем распознавание рукописного ввода на основе выбранного распознавателя рукописного ввода. В этом примере для выполнения распознавания рукописного ввода мы используем обработчик событий для нажатия кнопки "Распознать".

    [**InkPresenter**](https://msdn.microsoft.com/library/windows/apps/dn899081) сохраняет все росчерки пера в объекте [**InkStrokeContainer**](https://msdn.microsoft.com/library/windows/apps/br208492). Росчерки отображаются через свойство [**StrokeContainer**](https://msdn.microsoft.com/library/windows/apps/dn948766) **InkPresenter** и извлекаются методом [**GetStrokes**](https://msdn.microsoft.com/library/windows/apps/br208499).
```    CSharp
// Get all strokes on the InkCanvas.
    IReadOnlyList<InkStroke> currentStrokes =
        inkCanvas.InkPresenter.StrokeContainer.GetStrokes();
```

    [**RecognizeAsync**](https://msdn.microsoft.com/library/windows/apps/br208446) is called to retrieve a set of [**InkRecognitionResult**](https://msdn.microsoft.com/library/windows/apps/br208464) objects.

    Recognition results are produced for each word that is detected by an [**InkRecognizer**](https://msdn.microsoft.com/library/windows/apps/br208478).
```    CSharp
// Recognize all ink strokes on the ink canvas.
    IReadOnlyList<InkRecognitionResult> recognitionResults =
        await inkRecognizerContainer.RecognizeAsync(
            inkCanvas.InkPresenter.StrokeContainer,
            InkRecognitionTarget.All);
```

    Each [**InkRecognitionResult**](https://msdn.microsoft.com/library/windows/apps/br208464) object contains a set of text candidates. The topmost item in this list is considered by the recognition engine to be the best match, followed by the remaining candidates in order of decreasing confidence.

    We iterate through each [**InkRecognitionResult**](https://msdn.microsoft.com/library/windows/apps/br208464) and compile the list of candidates. The candidates are then displayed and the [**InkStrokeContainer**](https://msdn.microsoft.com/library/windows/apps/br208492) is cleared (which also clears the [**InkCanvas**](https://msdn.microsoft.com/library/windows/apps/dn858535)).
```    CSharp
string str = "Recognition result\n";
    // Iterate through the recognition results.
    foreach (InkRecognitionResult result in recognitionResults)
    {
        // Get all recognition candidates from each recognition result.
        IReadOnlyList<string> candidates =
            result.GetTextCandidates();
        str += "Candidates: " + candidates.Count.ToString() + "\n";
        foreach (string candidate in candidates)
        {
            str += candidate + " ";
        }
    }
    // Display the recognition candidates.
    recognitionResult.Text = str;
    // Clear the ink canvas once recognition is complete.
    inkCanvas.InkPresenter.StrokeContainer.Clear();
```

    Here's the click handler example, in full.
```    CSharp
// Handle button click to initiate recognition.
    private async void Recognize_Click(object sender, RoutedEventArgs e)
    {
        // Get all strokes on the InkCanvas.
        IReadOnlyList<InkStroke> currentStrokes =
            inkCanvas.InkPresenter.StrokeContainer.GetStrokes();

        // Ensure an ink stroke is present.
        if (currentStrokes.Count > 0)
        {
            // inkRecognizerContainer is null if a recognition engine is not available.
            if (!(inkRecognizerContainer == null))
            {
                // Recognize all ink strokes on the ink canvas.
                IReadOnlyList<InkRecognitionResult> recognitionResults =
                    await inkRecognizerContainer.RecognizeAsync(
                        inkCanvas.InkPresenter.StrokeContainer,
                        InkRecognitionTarget.All);
                // Process and display the recognition results.
                if (recognitionResults.Count > 0)
                {
                    string str = "Recognition result\n";
                    // Iterate through the recognition results.
                    foreach (InkRecognitionResult result in recognitionResults)
                    {
                        // Get all recognition candidates from each recognition result.
                        IReadOnlyList<string> candidates =
                            result.GetTextCandidates();
                        str += "Candidates: " + candidates.Count.ToString() + "\n";
                        foreach (string candidate in candidates)
                        {
                            str += candidate + " ";
                        }
                    }
                    // Display the recognition candidates.
                    recognitionResult.Text = str;
                    // Clear the ink canvas once recognition is complete.
                    inkCanvas.InkPresenter.StrokeContainer.Clear();
                }
                else
                {
                    recognitionResult.Text = "No recognition results.";
                }
            }
            else
            {
                Windows.UI.Popups.MessageDialog messageDialog =
                    new Windows.UI.Popups.MessageDialog(
                        "You must install handwriting recognition engine.");
                await messageDialog.ShowAsync();
            }
        }
        else
        {
            recognitionResult.Text = "No ink strokes to recognize.";
        }
    }
```

## <a name="dynamic-handwriting-recognition"></a>Динамическое распознавание рукописного ввода


В двух предыдущих примерах для запуска распознавания пользователь должен нажать кнопку. Ваше приложение также может выполнять динамическое распознавание, используя ввод росчерков в сочетании с основной функцией синхронизации.

В этом примере мы будем использовать тот же пользовательский интерфейс и параметры росчерка, что и в предыдущем примере международного распознавания.

1.  Как и в предыдущих примерах, элемент [**InkPresenter**](https://msdn.microsoft.com/library/windows/apps/dn899081) настраивается интерпретировать данные, вводимые пером или мышью, как росчерки пера ([**InputDeviceTypes**](https://msdn.microsoft.com/library/windows/apps/dn922019)), а росчерки пера выводятся на [**InkCanvas**](https://msdn.microsoft.com/library/windows/apps/dn858535) с помощью указанных атрибутов [**InkDrawingAttributes**](https://msdn.microsoft.com/library/windows/desktop/ms695050).

    Вместо кнопки для запуска распознавания, мы добавили прослушиватели для двух событий росчерка [**InkPresenter**](https://msdn.microsoft.com/library/windows/apps/dn899081) ([**StrokesCollected**](https://msdn.microsoft.com/library/windows/apps/dn922024) и [**StrokeStarted**](https://msdn.microsoft.com/library/windows/apps/dn914702)) и настроили основной таймер ([**DispatcherTimer**](https://msdn.microsoft.com/library/windows/apps/br244250)) на секундный интервал [**Tick**](https://msdn.microsoft.com/library/windows/apps/br244256).    
```    CSharp
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

        // Populate the recognizer combo box with installed recognizers.
        InitializeRecognizerList();

        // Listen for combo box selection.
        comboInstalledRecognizers.SelectionChanged +=
            comboInstalledRecognizers_SelectionChanged;

        // Listen for stroke events on the InkPresenter to
        // enable dynamic recognition.
        // StrokesCollected is fired when the user stops inking by
        // lifting their pen or finger, or releasing the mouse button.
        inkCanvas.InkPresenter.StrokesCollected +=
            inkCanvas_StrokesCollected;
        // StrokeStarted is fired when ink input is first detected.
        inkCanvas.InkPresenter.StrokeInput.StrokeStarted +=
            inkCanvas_StrokeStarted;

        // Timer to manage dynamic recognition.
        recoTimer = new DispatcherTimer();
        recoTimer.Interval = new TimeSpan(0, 0, 1);
        recoTimer.Tick += recoTimer_Tick;
    }

    // Handler for the timer tick event calls the recognition function.
    private void recoTimer_Tick(object sender, object e)
    {
        Recognize_Tick();
    }

    // Handler for the InkPresenter StrokeStarted event.
    // If a new stroke starts before the next timer tick event,
    // stop the timer as the new stroke is likely the continuation
    // of a single handwriting entry.
    private void inkCanvas_StrokeStarted(InkStrokeInput sender, PointerEventArgs args)
    {
        recoTimer.Stop();
    }

    // Handler for the InkPresenter StrokesCollected event.
    // Start the recognition timer when the user stops inking by
    // lifting their pen or finger, or releasing the mouse button.
    // After one second of no ink input, recognition is initiated.
    private void inkCanvas_StrokesCollected(InkPresenter sender, InkStrokesCollectedEventArgs args)
    {
        recoTimer.Start();
    }    
```

2.  Здесь есть обработчики событий, которые мы добавили в первом шаге.

    [**StrokesCollected**](https://msdn.microsoft.com/library/windows/apps/dn922024)  
    Запускать таймер распознавания, когда пользователь прерывает рукописный ввод, поднимая перо или палец либо отпуская кнопку мыши. Распознавание вызывается через одну секунду отсутствия рукописного ввода.

    [**StrokeStarted**](https://msdn.microsoft.com/library/windows/apps/dn914702)  
    Если новый росчерк начинается до следующего события такта таймера, останавливать таймер, так как новый росчерк — это, скорее всего, продолжение одной рукописной записи.

    [**Tick**](https://msdn.microsoft.com/library/windows/apps/br244256)  
    Функция распознавания вызывается через одну секунду отсутствия рукописного ввода.
```    CSharp
// Handler for the timer tick event calls the recognition function.
    private void recoTimer_Tick(object sender, object e)
    {
        Recognize_Tick();
    }

    // Handler for the InkPresenter StrokeStarted event.
    // If a new stroke starts before the next timer tick event,
    // stop the timer as the new stroke is likely the continuation
    // of a single handwriting entry.
    private void inkCanvas_StrokeStarted(InkStrokeInput sender, PointerEventArgs args)
    {
        recoTimer.Stop();
    }

    // Handler for the InkPresenter StrokesCollected event.
    // Start the recognition timer when the user stops inking by
    // lifting their pen or finger, or releasing the mouse button.
    // After one second of no ink input, recognition is initiated.
    private void inkCanvas_StrokesCollected(InkPresenter sender, InkStrokesCollectedEventArgs args)
    {
        recoTimer.Start();
    }
```

3.  Наконец, мы выполняем распознавание рукописного ввода на основе выбранного распознавателя рукописного ввода. В этом примере мы используем обработчик событий [**Tick**](https://msdn.microsoft.com/library/windows/apps/br244256) элемента [**DispatcherTimer**](https://msdn.microsoft.com/library/windows/apps/br244250) для вызова функции распознавания рукописного ввода.

    [**InkPresenter**](https://msdn.microsoft.com/library/windows/apps/dn899081) сохраняет все росчерки пера в объекте [**InkStrokeContainer**](https://msdn.microsoft.com/library/windows/apps/br208492). Росчерки отображаются через свойство [**StrokeContainer**](https://msdn.microsoft.com/library/windows/apps/dn948766) **InkPresenter** и извлекаются методом [**GetStrokes**](https://msdn.microsoft.com/library/windows/apps/br208499).
```    CSharp
// Get all strokes on the InkCanvas.
    IReadOnlyList<InkStroke> currentStrokes = inkCanvas.InkPresenter.StrokeContainer.GetStrokes();
```

    [**RecognizeAsync**](https://msdn.microsoft.com/library/windows/apps/br208446) is called to retrieve a set of [**InkRecognitionResult**](https://msdn.microsoft.com/library/windows/apps/br208464) objects.

    Recognition results are produced for each word that is detected by an [**InkRecognizer**](https://msdn.microsoft.com/library/windows/apps/br208478).
```    CSharp
// Recognize all ink strokes on the ink canvas.
    IReadOnlyList<InkRecognitionResult> recognitionResults =
        await inkRecognizerContainer.RecognizeAsync(
            inkCanvas.InkPresenter.StrokeContainer,
            InkRecognitionTarget.All);
```

    Each [**InkRecognitionResult**](https://msdn.microsoft.com/library/windows/apps/br208464) object contains a set of text candidates. The topmost item in this list is considered by the recognition engine to be the best match, followed by the remaining candidates in order of decreasing confidence.

    We iterate through each [**InkRecognitionResult**](https://msdn.microsoft.com/library/windows/apps/br208464) and compile the list of candidates. The candidates are then displayed and the [**InkStrokeContainer**](https://msdn.microsoft.com/library/windows/apps/br208492) is cleared (which also clears the [**InkCanvas**](https://msdn.microsoft.com/library/windows/apps/dn858535)).
```    CSharp
string str = "Recognition result\n";
    // Iterate through the recognition results.
    foreach (InkRecognitionResult result in recognitionResults)
    {
        // Get all recognition candidates from each recognition result.
        IReadOnlyList<string> candidates = result.GetTextCandidates();
        str += "Candidates: " + candidates.Count.ToString() + "\n";
        foreach (string candidate in candidates)
        {
            str += candidate + " ";
        }
    }
    // Display the recognition candidates.
    recognitionResult.Text = str;
    // Clear the ink canvas once recognition is complete.
    inkCanvas.InkPresenter.StrokeContainer.Clear();
```

    Here's the recognition function, in full.
```    CSharp
// Respond to timer Tick and initiate recognition.
    private async void Recognize_Tick()
    {
        // Get all strokes on the InkCanvas.
        IReadOnlyList<InkStroke> currentStrokes = inkCanvas.InkPresenter.StrokeContainer.GetStrokes();

        // Ensure an ink stroke is present.
        if (currentStrokes.Count > 0)
        {
            // inkRecognizerContainer is null if a recognition engine is not available.
            if (!(inkRecognizerContainer == null))
            {
                // Recognize all ink strokes on the ink canvas.
                IReadOnlyList<InkRecognitionResult> recognitionResults =
                    await inkRecognizerContainer.RecognizeAsync(
                        inkCanvas.InkPresenter.StrokeContainer,
                        InkRecognitionTarget.All);
                // Process and display the recognition results.
                if (recognitionResults.Count > 0)
                {
                    string str = "Recognition result\n";
                    // Iterate through the recognition results.
                    foreach (InkRecognitionResult result in recognitionResults)
                    {
                        // Get all recognition candidates from each recognition result.
                        IReadOnlyList<string> candidates = result.GetTextCandidates();
                        str += "Candidates: " + candidates.Count.ToString() + "\n";
                        foreach (string candidate in candidates)
                        {
                            str += candidate + " ";
                        }
                    }
                    // Display the recognition candidates.
                    recognitionResult.Text = str;
                    // Clear the ink canvas once recognition is complete.
                    inkCanvas.InkPresenter.StrokeContainer.Clear();
                }
                else
                {
                    recognitionResult.Text = "No recognition results.";
                }
            }
            else
            {
                Windows.UI.Popups.MessageDialog messageDialog = new Windows.UI.Popups.MessageDialog("You must install handwriting recognition engine.");
                await messageDialog.ShowAsync();
            }
        }
        else
        {
            recognitionResult.Text = "No ink strokes to recognize.";
        }

        // Stop the dynamic recognition timer.
        recoTimer.Stop();
    }
```

## <a name="related-articles"></a>Связанные разделы

* [Взаимодействие с помощью пера](pen-and-stylus-interactions.md)

**Примеры**
* [Пример рукописного ввода](http://go.microsoft.com/fwlink/p/?LinkID=620308)
* [Простой пример рукописного ввода](http://go.microsoft.com/fwlink/p/?LinkID=620312)
* [Сложный пример рукописного ввода](http://go.microsoft.com/fwlink/p/?LinkID=620314)
* [Пример раскраски](https://aka.ms/cpubsample-coloringbook)
* [Пример семейных заметок](https://aka.ms/cpubsample-familynotessample)


 

---
Description: Приложения Windows, поддерживающие рукописный ввод Windows, могут сериализовать и десериализовать рукописные штрихи в файл сериализованного формата рукописного ввода (ISF). Файл ISF — это изображение GIF с дополнительными метаданными для всех свойств и поведений росчерков пера. Приложения без соответствующей функции могут просматривать статическое изображение GIF, включая прозрачный фон альфа-канала.
title: Хранение и извлечение данных о росчерках пера Windows Ink
ms.assetid: C96C9D2F-DB69-4883-9809-4A0DF7CEC506
label: Store and retrieve Windows Ink stroke data
template: detail.hbs
keywords: Windows Ink, Windows Inking, DirectInk, InkPresenter, InkCanvas, ISF, сериализованный формат Ink, взаимодействие с пользователем, ввод
ms.date: 02/08/2017
ms.topic: article
ms.localizationpriority: medium
ms.openlocfilehash: 819358fb775444d62cbad414668a779fc5c305ca
ms.sourcegitcommit: 0dee502484df798a0595ac1fe7fb7d0f5a982821
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/08/2020
ms.locfileid: "82970249"
---
# <a name="store-and-retrieve-windows-ink-stroke-data"></a>Хранение и извлечение данных о росчерках пера Windows Ink


Приложения Windows, поддерживающие рукописный ввод Windows, могут сериализовать и десериализовать рукописные штрихи в файл сериализованного формата рукописного ввода (ISF). Файл ISF — это изображение GIF с дополнительными метаданными для всех свойств и поведений росчерков пера. Приложения без соответствующей функции могут просматривать статическое изображение GIF, включая прозрачный фон альфа-канала.

> [!NOTE]
> Формат ISF — наиболее компактное постоянное представление рукописного ввода. Он может быть встроен в двоичный формат документа, например GIF-файл, или помещен непосредственно в буфер обмена.

> **Важные API-интерфейсы**: [**InkCanvas**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.InkCanvas), [**Windows.UI.Input.Inking**](https://docs.microsoft.com/uwp/api/Windows.UI.Input.Inking)

## <a name="save-ink-strokes-to-a-file"></a>Сохранение росчерков пера в файл

Здесь мы покажем, как сохранить росчерки пера, нарисованные в элементе управления [**InkCanvas**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.InkCanvas).

**Загрузите этот пример с [помощью сохранения и загрузки рукописных штрихов из файла сериализованного формата рукописного ввода (ISF)](https://github.com/MicrosoftDocs/windows-topic-specific-samples/archive/uwp-ink-store.zip) .**

1.  Сначала мы настраиваем пользовательский интерфейс.

    Пользовательский интерфейс включает кнопки «Сохранить», «Загрузить» и «Очистить», а также элемент [**InkCanvas**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.InkCanvas).
```    XAML
<Grid Background="{ThemeResource ApplicationPageBackgroundThemeBrush}">
        <Grid.RowDefinitions>
            <RowDefinition Height="Auto"/>
            <RowDefinition Height="*"/>
        </Grid.RowDefinitions>
        <StackPanel x:Name="HeaderPanel" Orientation="Horizontal" Grid.Row="0">
            <TextBlock x:Name="Header" 
                       Text="Basic ink store sample" 
                       Style="{ThemeResource HeaderTextBlockStyle}" 
                       Margin="10,0,0,0" />
            <Button x:Name="btnSave" 
                    Content="Save" 
                    Margin="50,0,10,0"/>
            <Button x:Name="btnLoad" 
                    Content="Load" 
                    Margin="50,0,10,0"/>
            <Button x:Name="btnClear" 
                    Content="Clear" 
                    Margin="50,0,10,0"/>
        </StackPanel>
        <Grid Grid.Row="1">
            <InkCanvas x:Name="inkCanvas" />
        </Grid>
    </Grid>
```

2.  Затем мы задаем некоторые основные реакции на рукописный ввод.

    Элемент [**InkPresenter**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.inkcanvas.inkpresenter) настраивается интерпретировать данные, вводимые пером или мышью, как росчерки пера ([**InputDeviceTypes**](https://docs.microsoft.com/uwp/api/windows.ui.input.inking.inkpresenter.inputdevicetypes)), и объявляются прослушиватели для событий нажатия кнопок.
```csharp
public MainPage()
    {
        this.InitializeComponent();

        // Set supported inking device types.
        inkCanvas.InkPresenter.InputDeviceTypes =
            Windows.UI.Core.CoreInputDeviceTypes.Mouse |
            Windows.UI.Core.CoreInputDeviceTypes.Pen;

        // Listen for button click to initiate save.
        btnSave.Click += btnSave_Click;
        // Listen for button click to initiate load.
        btnLoad.Click += btnLoad_Click;
        // Listen for button click to clear ink canvas.
        btnClear.Click += btnClear_Click;
    }
```

3.  Наконец, мы сохраняем рукописный ввод в обработчике событий нажатия кнопки **Сохранить**.

    [**FileSavePicker**](https://docs.microsoft.com/uwp/api/Windows.Storage.Pickers.FileSavePicker) позволяет пользователю выбрать и файл, и расположение, где будут сохраняться данные рукописного ввода.

    После выбора файла мы открываем поток [**IRandomAccessStream**](https://docs.microsoft.com/uwp/api/Windows.Storage.Streams.IRandomAccessStream), для которого установлено значение [**ReadWrite**](https://docs.microsoft.com/uwp/api/Windows.Storage.FileAccessMode).

    Затем мы вызываем [**SaveAsync**](https://docs.microsoft.com/uwp/api/windows.ui.input.inking.iinkstrokecontainer.saveasync), чтобы сериализовать росчерки пера, управляемые элементом [**InkStrokeContainer**](https://docs.microsoft.com/uwp/api/Windows.UI.Input.Inking.InkStrokeContainer), в поток.

```csharp
// Save ink data to a file.
    private async void btnSave_Click(object sender, RoutedEventArgs e)
    {
        // Get all strokes on the InkCanvas.
        IReadOnlyList<InkStroke> currentStrokes = inkCanvas.InkPresenter.StrokeContainer.GetStrokes();

        // Strokes present on ink canvas.
        if (currentStrokes.Count > 0)
        {
            // Let users choose their ink file using a file picker.
            // Initialize the picker.
            Windows.Storage.Pickers.FileSavePicker savePicker = 
                new Windows.Storage.Pickers.FileSavePicker();
            savePicker.SuggestedStartLocation = 
                Windows.Storage.Pickers.PickerLocationId.DocumentsLibrary;
            savePicker.FileTypeChoices.Add(
                "GIF with embedded ISF", 
                new List<string>() { ".gif" });
            savePicker.DefaultFileExtension = ".gif";
            savePicker.SuggestedFileName = "InkSample";

            // Show the file picker.
            Windows.Storage.StorageFile file = 
                await savePicker.PickSaveFileAsync();
            // When chosen, picker returns a reference to the selected file.
            if (file != null)
            {
                // Prevent updates to the file until updates are 
                // finalized with call to CompleteUpdatesAsync.
                Windows.Storage.CachedFileManager.DeferUpdates(file);
                // Open a file stream for writing.
                IRandomAccessStream stream = await file.OpenAsync(Windows.Storage.FileAccessMode.ReadWrite);
                // Write the ink strokes to the output stream.
                using (IOutputStream outputStream = stream.GetOutputStreamAt(0))
                {
                    await inkCanvas.InkPresenter.StrokeContainer.SaveAsync(outputStream);
                    await outputStream.FlushAsync();
                }
                stream.Dispose();

                // Finalize write so other apps can update file.
                Windows.Storage.Provider.FileUpdateStatus status =
                    await Windows.Storage.CachedFileManager.CompleteUpdatesAsync(file);

                if (status == Windows.Storage.Provider.FileUpdateStatus.Complete)
                {
                    // File saved.
                }
                else
                {
                    // File couldn't be saved.
                }
            }
            // User selects Cancel and picker returns null.
            else
            {
                // Operation cancelled.
            }
        }
    }
```

> [!NOTE]
> GIF — единственный поддерживаемый формат файла для сохранения данных рукописного ввода. Однако метод [**LoadAsync**](https://docs.microsoft.com/uwp/api/windows.ui.input.inking.inkmanager.loadasync) (демонстрируемый в следующем разделе) поддерживает и дополнительные форматы для обратной совместимости.

## <a name="load-ink-strokes-from-a-file"></a>Загрузка росчерков пера из файла

Здесь мы покажем, как загрузить росчерки пера из файла и отобразить их в элементе управления [**InkCanvas**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.InkCanvas).

**Загрузите этот пример с [помощью сохранения и загрузки рукописных штрихов из файла сериализованного формата рукописного ввода (ISF)](https://github.com/MicrosoftDocs/windows-topic-specific-samples/archive/uwp-ink-store.zip) .**

1.  Сначала мы настраиваем пользовательский интерфейс.

    Пользовательский интерфейс включает кнопки «Сохранить», «Загрузить» и «Очистить», а также элемент [**InkCanvas**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.InkCanvas).
```    XAML
<Grid Background="{ThemeResource ApplicationPageBackgroundThemeBrush}">
        <Grid.RowDefinitions>
            <RowDefinition Height="Auto"/>
            <RowDefinition Height="*"/>
        </Grid.RowDefinitions>
        <StackPanel x:Name="HeaderPanel" Orientation="Horizontal" Grid.Row="0">
            <TextBlock x:Name="Header" 
                       Text="Basic ink store sample" 
                       Style="{ThemeResource HeaderTextBlockStyle}" 
                       Margin="10,0,0,0" />
            <Button x:Name="btnSave" 
                    Content="Save" 
                    Margin="50,0,10,0"/>
            <Button x:Name="btnLoad" 
                    Content="Load" 
                    Margin="50,0,10,0"/>
            <Button x:Name="btnClear" 
                    Content="Clear" 
                    Margin="50,0,10,0"/>
        </StackPanel>
        <Grid Grid.Row="1">
            <InkCanvas x:Name="inkCanvas" />
        </Grid>
    </Grid>
```

2.  Затем мы задаем некоторые основные реакции на рукописный ввод.

    Элемент [**InkPresenter**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.inkcanvas.inkpresenter) настраивается интерпретировать данные, вводимые пером или мышью, как росчерки пера ([**InputDeviceTypes**](https://docs.microsoft.com/uwp/api/windows.ui.input.inking.inkpresenter.inputdevicetypes)), и объявляются прослушиватели для событий нажатия кнопок.
```csharp
public MainPage()
    {
        this.InitializeComponent();

        // Set supported inking device types.
        inkCanvas.InkPresenter.InputDeviceTypes =
            Windows.UI.Core.CoreInputDeviceTypes.Mouse |
            Windows.UI.Core.CoreInputDeviceTypes.Pen;

        // Listen for button click to initiate save.
        btnSave.Click += btnSave_Click;
        // Listen for button click to initiate load.
        btnLoad.Click += btnLoad_Click;
        // Listen for button click to clear ink canvas.
        btnClear.Click += btnClear_Click;
    }
```

3.  Наконец, мы загружаем рукописный ввод в обработчике событий нажатия кнопки **Загрузить**.

    [**FileOpenPicker**](https://docs.microsoft.com/uwp/api/Windows.Storage.Pickers.FileOpenPicker) позволяет пользователю выбрать и файл, и расположение, откуда будут извлекаться сохраненные данные рукописного ввода.

    После выбора файла мы открываем поток [**IRandomAccessStream**](https://docs.microsoft.com/uwp/api/Windows.Storage.Streams.IRandomAccessStream), для которого установлено значение [**Read**](https://docs.microsoft.com/uwp/api/Windows.Storage.FileAccessMode).

    Затем мы вызываем [**LoadAsync**](https://docs.microsoft.com/uwp/api/windows.ui.input.inking.inkmanager.loadasync) для чтения, десериализации и загрузки сохраненных росчерков пера в элемент [**InkStrokeContainer**](https://docs.microsoft.com/uwp/api/Windows.UI.Input.Inking.InkStrokeContainer). Загрузка росчерков в элемент **InkStrokeContainer** приводит к тому, что [**InkPresenter**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.inkcanvas.inkpresenter) немедленно выводит их на [**InkCanvas**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.InkCanvas).

    > [!NOTE]
    > Все существующие штрихи в InkStrokeContainer удаляются перед загрузкой новых.

``` csharp
// Load ink data from a file.
private async void btnLoad_Click(object sender, RoutedEventArgs e)
{
    // Let users choose their ink file using a file picker.
    // Initialize the picker.
    Windows.Storage.Pickers.FileOpenPicker openPicker =
        new Windows.Storage.Pickers.FileOpenPicker();
    openPicker.SuggestedStartLocation =
        Windows.Storage.Pickers.PickerLocationId.DocumentsLibrary;
    openPicker.FileTypeFilter.Add(".gif");
    // Show the file picker.
    Windows.Storage.StorageFile file = await openPicker.PickSingleFileAsync();
    // User selects a file and picker returns a reference to the selected file.
    if (file != null)
    {
        // Open a file stream for reading.
        IRandomAccessStream stream = await file.OpenAsync(Windows.Storage.FileAccessMode.Read);
        // Read from file.
        using (var inputStream = stream.GetInputStreamAt(0))
        {
            await inkCanvas.InkPresenter.StrokeContainer.LoadAsync(inputStream);
        }
        stream.Dispose();
    }
    // User selects Cancel and picker returns null.
    else
    {
        // Operation cancelled.
    }
}
```

> [!NOTE]
> GIF — единственный поддерживаемый формат файла для сохранения данных рукописного ввода. Тем не менее, метод [**LoadAsync**](https://docs.microsoft.com/uwp/api/windows.ui.input.inking.inkmanager.loadasync) поддерживает и дополнительные форматы для обратной совместимости.

| Формат                    | Описание |
|---------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| InkSerializedFormat       | Определяет рукописный ввод, который постоянно использует формат ISF. Формат ISF — наиболее компактное постоянное представление рукописного ввода. Он может быть встроен в двоичный формат документа или помещен непосредственно в буфер обмена.                                                                                                                                                                                                         |
| Формат Base64InkSerializedFormat | Определяет рукописный ввод, который постоянно кодирует ISF как поток base64. Этот формат предоставляется для того, чтобы рукописный ввод был непосредственно закодирован в XML- или в HTML-файл.                                                                                                                                                                                                                                                |
| Gif                       | Определяет рукописный ввод, который постоянно использует формат GIF, содержащий ISF как метаданные, встроенные в файл. Это позволяет просматривать рукописные фрагменты в приложениях без соответствующей функции, сохраняя при этом полноценное качество в приложениях с функцией рукописного ввода. Этот формат идеален при перемещении рукописного содержимого в рамках HTML-файла и для обеспечения его использования приложениями с функцией рукописного ввода и приложениями без нее. |
| Формат Base64Gif                 | Определяет рукописный ввод, который постоянно использует формат GIF, закодированный и усиленный форматом base64. Этот формат предоставляется, когда рукописный ввод должен быть закодирован непосредственно в XML- или HTML-файл для дальнейшего преобразования в изображение. Его можно использовать в формате XML, который создается для хранения всей информации о рукописном вводе и используется для создания HTML с помощью XSLT. 

## <a name="copy-and-paste-ink-strokes-with-the-clipboard"></a>Копирование и вставка росчерков пера через буфер обмена

Здесь мы покажем, как использовать буфер обмена для перемещения росчерков пера между приложениями.

Чтобы обеспечить поддержку функции буфера обмена, для встроенных команд вырезания и копирования элемента [**InkStrokeContainer**](https://docs.microsoft.com/uwp/api/Windows.UI.Input.Inking.InkStrokeContainer) необходимо выбрать один или несколько росчерков пера.

В этом примере мы добавляем возможность выбора росчерка, когда входные данные изменяются кнопкой пера (или правой кнопкой мыши). Полный пример реализации возможности выбора росчерка см. в подразделе "Передача входных данных для расширенной обработки" раздела [Взаимодействие с помощью пера](pen-and-stylus-interactions.md).

**Скачайте этот пример с [помощью сохранения и загрузки рукописных штрихов из буфера обмена](https://github.com/MicrosoftDocs/windows-topic-specific-samples/archive/uwp-ink-store-clipboard.zip)**

1.  Сначала мы настраиваем пользовательский интерфейс.

    Пользовательский интерфейс включает кнопки «Вырезать», «Копировать», «Вставить» и «Очистить», а также элемент [**InkCanvas**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.InkCanvas) и холст выбора.
```    XAML
<Grid Background="{ThemeResource ApplicationPageBackgroundThemeBrush}">
        <Grid.RowDefinitions>
            <RowDefinition Height="Auto"/>
            <RowDefinition Height="*"/>
        </Grid.RowDefinitions>
        <StackPanel x:Name="HeaderPanel" Orientation="Horizontal" Grid.Row="0">
            <TextBlock x:Name="tbHeader" 
                       Text="Basic ink store sample" 
                       Style="{ThemeResource HeaderTextBlockStyle}" 
                       Margin="10,0,0,0" />
            <Button x:Name="btnCut" 
                    Content="Cut" 
                    Margin="20,0,10,0"/>
            <Button x:Name="btnCopy" 
                    Content="Copy" 
                    Margin="20,0,10,0"/>
            <Button x:Name="btnPaste" 
                    Content="Paste" 
                    Margin="20,0,10,0"/>
            <Button x:Name="btnClear" 
                    Content="Clear" 
                    Margin="20,0,10,0"/>
        </StackPanel>
        <Grid x:Name="gridCanvas" Grid.Row="1">
            <!-- Canvas for displaying selection UI. -->
            <Canvas x:Name="selectionCanvas"/>
            <!-- Inking area -->
            <InkCanvas x:Name="inkCanvas"/>
        </Grid>
    </Grid>
```

2.  Затем мы задаем некоторые основные реакции на рукописный ввод.

    Элемент [**InkPresenter**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.inkcanvas.inkpresenter) настраивается интерпретировать данные, вводимые пером или мышью, как росчерки пера ([**InputDeviceTypes**](https://docs.microsoft.com/uwp/api/windows.ui.input.inking.inkpresenter.inputdevicetypes)). Кроме того, здесь объявляются прослушиватели для событий нажатия кнопок, а также событий указателя и событий росчерка для функции выбора.

    Полный пример реализации возможности выбора росчерка см. в подразделе "Передача входных данных для расширенной обработки" раздела [Взаимодействие с помощью пера](pen-and-stylus-interactions.md).
```csharp
public MainPage()
    {
        this.InitializeComponent();

        // Set supported inking device types.
        inkCanvas.InkPresenter.InputDeviceTypes =
            Windows.UI.Core.CoreInputDeviceTypes.Mouse |
            Windows.UI.Core.CoreInputDeviceTypes.Pen;

        // Listen for button click to cut ink strokes.
        btnCut.Click += btnCut_Click;
        // Listen for button click to copy ink strokes.
        btnCopy.Click += btnCopy_Click;
        // Listen for button click to paste ink strokes.
        btnPaste.Click += btnPaste_Click;
        // Listen for button click to clear ink canvas.
        btnClear.Click += btnClear_Click;

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

3.  Наконец, добавив поддержку возможности выбора росчерка, мы реализуем функцию буфера обмена в обработчиках событий нажатия кнопок **Вырезать**, **Копировать** и **Вставить**.

    Чтобы вырезать, мы сначала вызываем [**CopySelectedToClipboard**](https://docs.microsoft.com/uwp/api/windows.ui.input.inking.inkstrokecontainer.copyselectedtoclipboard) в объекте [**InkStrokeContainer**](https://docs.microsoft.com/uwp/api/Windows.UI.Input.Inking.InkStrokeContainer) элемента [**InkPresenter**](https://docs.microsoft.com/uwp/api/Windows.UI.Input.Inking.InkPresenter).

    Затем мы вызываем [**DeleteSelected**](https://docs.microsoft.com/uwp/api/windows.ui.input.inking.inkstrokecontainer.deleteselected), чтобы убрать росчерки с холста рукописного ввода.

    Наконец, мы удаляем все росчерки выбора с холста выбора.
    
```csharp
private void btnCut_Click(object sender, RoutedEventArgs e)
    {
        inkCanvas.InkPresenter.StrokeContainer.CopySelectedToClipboard();
        inkCanvas.InkPresenter.StrokeContainer.DeleteSelected();
        ClearSelection();
    }
```
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

Чтобы выполнить копирование, мы просто вызываем [**CopySelectedToClipboard**](https://docs.microsoft.com/uwp/api/windows.ui.input.inking.inkstrokecontainer.copyselectedtoclipboard) в объекте [**InkStrokeContainer**](https://docs.microsoft.com/uwp/api/Windows.UI.Input.Inking.InkStrokeContainer) элемента [**InkPresenter**](https://docs.microsoft.com/uwp/api/Windows.UI.Input.Inking.InkPresenter).


```csharp
private void btnCopy_Click(object sender, RoutedEventArgs e)
    {
        inkCanvas.InkPresenter.StrokeContainer.CopySelectedToClipboard();
    }
```

Чтобы вставить, мы вызываем [**CanPasteFromClipboard**](https://docs.microsoft.com/uwp/api/windows.ui.input.inking.inkstrokecontainer.canpastefromclipboard) для обеспечения того, что содержимое в буфере обмена можно вставить на холст рукописного ввода.

Если это так, мы вызываем [**PasteFromClipboard**](https://docs.microsoft.com/uwp/api/windows.ui.input.inking.inkstrokecontainer.pastefromclipboard), чтобы вставить росчерки пера в объект [**InkStrokeContainer**](https://docs.microsoft.com/uwp/api/Windows.UI.Input.Inking.InkStrokeContainer) элемента [**InkPresenter**](https://docs.microsoft.com/uwp/api/Windows.UI.Input.Inking.InkPresenter), который затем выводит росчерки на холст рукописного ввода.

```csharp
private void btnPaste_Click(object sender, RoutedEventArgs e)
    {
        if (inkCanvas.InkPresenter.StrokeContainer.CanPasteFromClipboard())
        {
            inkCanvas.InkPresenter.StrokeContainer.PasteFromClipboard(
                new Point(0, 0));
        }
        else
        {
            // Cannot paste from clipboard.
        }
    }
```

## <a name="related-articles"></a>Похожие статьи

* [Взаимодействие с помощью пера](pen-and-stylus-interactions.md)

**Примеры в статье**
* [Сохранение и загрузка росчерков пера из файла Ink Serialized Format (ISF)](https://github.com/MicrosoftDocs/windows-topic-specific-samples/archive/uwp-ink-store.zip)
* [Сохранение и загрузка росчерков пера из буфера обмена](https://github.com/MicrosoftDocs/windows-topic-specific-samples/archive/uwp-ink-store-clipboard.zip)

**Другие примеры**
* [Простой пример рукописного ввода (C#/C++)](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/SimpleInk)
* [Сложный пример рукописного ввода (C++)](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/ComplexInk)
* [Пример рукописного ввода (JavaScript)](https://github.com/microsoftarchive/msdn-code-gallery-microsoft/tree/411c271e537727d737a53fa2cbe99eaecac00cc0/Official%20Windows%20Platform%20Sample/Windows%208%20app%20samples/%5BJavaScript%5D-Windows%208%20app%20samples/JavaScript/Windows%208%20app%20samples/Input%20Ink%20sample%20(Windows%208))
* [Руководство по началу работы. Поддержка рукописного ввода в приложении Windows](https://github.com/Microsoft/Windows-tutorials-inputs-and-devices/tree/master/GettingStarted-Ink)
* [Пример раскраски](https://github.com/Microsoft/Windows-appsample-coloringbook)
* [Пример семейных заметок](https://github.com/Microsoft/Windows-appsample-familynotes)




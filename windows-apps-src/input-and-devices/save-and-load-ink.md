---
author: Karl-Bridge-Microsoft
Description: "Приложения UWP с поддержкой Windows Ink могут сериализовать и десериализовать росчерки пера в файл ISF. Файл ISF — это изображение GIF с дополнительными метаданными для всех свойств и поведений росчерков пера. Приложения без соответствующей функции могут просматривать статическое изображение GIF, включая прозрачный фон альфа-канала."
title: "Хранение и извлечение данных движения пера Windows Ink"
ms.assetid: C96C9D2F-DB69-4883-9809-4A0DF7CEC506
label: Store and retrieve Windows Ink stroke data
template: detail.hbs
keyword: Windows Ink, Windows Inking, DirectInk, InkPresenter, InkCanvas, ISF, Ink Serialized Format
ms.sourcegitcommit: a2ec5e64b91c9d0e401c48902a18e5496fc987ab
ms.openlocfilehash: cdef00304e1835532eceb8e51fecc8045f2ff300

---

# Хранение и извлечение данных движения пера Windows Ink


Приложения UWP с поддержкой Windows Ink могут сериализовать и десериализовать росчерки пера в файл ISF. Файл ISF — это изображение GIF с дополнительными метаданными для всех свойств и поведений росчерков пера. Приложения без соответствующей функции могут просматривать статическое изображение GIF, включая прозрачный фон альфа-канала.


**Важные API**

-   [**InkCanvas**](https://msdn.microsoft.com/library/windows/apps/dn858535)
-   [**Windows.UI.Input.Inking**](https://msdn.microsoft.com/library/windows/apps/br208524)



**Примечание.**  
Формат ISF — наиболее компактное постоянное представление рукописного ввода. Он может быть встроен в двоичный формат документа, например GIF-файл, или помещен непосредственно в буфер обмена.

 

## Сохранение росчерков пера в файл


Здесь мы покажем, как сохранить росчерки пера, нарисованные в элементе управления [**InkCanvas**](https://msdn.microsoft.com/library/windows/apps/dn858535).

1.  Сначала мы настраиваем пользовательский интерфейс.

    Пользовательский интерфейс включает кнопки «Сохранить», «Загрузить» и «Очистить», а также элемент [**InkCanvas**](https://msdn.microsoft.com/library/windows/apps/dn858535).
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

    Элемент [**InkPresenter**](https://msdn.microsoft.com/library/windows/apps/dn899081) настраивается интерпретировать данные, вводимые пером или мышью, как росчерки пера ([**InputDeviceTypes**](https://msdn.microsoft.com/library/windows/apps/dn922019)), и объявляются прослушиватели для событий нажатия кнопок.
```    CSharp
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

    [
            **FileSavePicker**](https://msdn.microsoft.com/library/windows/apps/br207871) позволяет пользователю выбрать и файл, и расположение, где будут сохраняться данные рукописного ввода.

    После выбора файла мы открываем поток [**IRandomAccessStream**](https://msdn.microsoft.com/library/windows/apps/br241731), для которого установлено значение [**ReadWrite**](https://msdn.microsoft.com/library/windows/apps/br241635).

    Затем мы вызываем [**SaveAsync**](https://msdn.microsoft.com/library/windows/apps/br242114), чтобы сериализовать росчерки пера, управляемые элементом [**InkStrokeContainer**](https://msdn.microsoft.com/library/windows/apps/br208492), в поток.
```    CSharp
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
                    // File couldn&#39;t be saved.
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

[!NOTE]  
GIF — единственный поддерживаемый формат для сохранения данных рукописного ввода. Однако метод [**LoadAsync**](https://msdn.microsoft.com/library/windows/apps/hh701607) (демонстрируемый в следующем разделе) поддерживает и дополнительные форматы для обратной совместимости.

 

## Загрузка росчерков пера из файла


Здесь мы покажем, как загрузить росчерки пера из файла и отобразить их в элементе управления [**InkCanvas**](https://msdn.microsoft.com/library/windows/apps/dn858535).

1.  Сначала мы настраиваем пользовательский интерфейс.

    Пользовательский интерфейс включает кнопки «Сохранить», «Загрузить» и «Очистить», а также элемент [**InkCanvas**](https://msdn.microsoft.com/library/windows/apps/dn858535).
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

    Элемент [**InkPresenter**](https://msdn.microsoft.com/library/windows/apps/dn899081) настраивается интерпретировать данные, вводимые пером или мышью, как росчерки пера ([**InputDeviceTypes**](https://msdn.microsoft.com/library/windows/apps/dn922019)), и объявляются прослушиватели для событий нажатия кнопок.
```    CSharp
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

    [
            **FileOpenPicker**](https://msdn.microsoft.com/library/windows/apps/br207847) позволяет пользователю выбрать и файл, и расположение, откуда будут извлекаться сохраненные данные рукописного ввода.

    После выбора файла мы открываем поток [**IRandomAccessStream**](https://msdn.microsoft.com/library/windows/apps/br241731), для которого установлено значение [**Read**](https://msdn.microsoft.com/library/windows/apps/br241635).

    Затем мы вызываем [**LoadAsync**](https://msdn.microsoft.com/library/windows/apps/hh701607) для чтения, десериализации и загрузки сохраненных росчерков пера в элемент [**InkStrokeContainer**](https://msdn.microsoft.com/library/windows/apps/br208492). Загрузка росчерков в элемент **InkStrokeContainer** приводит к тому, что [**InkPresenter**](https://msdn.microsoft.com/library/windows/apps/dn899081) немедленно выводит их на [**InkCanvas**](https://msdn.microsoft.com/library/windows/apps/dn858535).
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
            await inkCanvas.InkPresenter.StrokeContainer.LoadAsync(stream);
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

**Примечание.**  
GIF — единственный поддерживаемый формат для сохранения данных рукописного ввода. Тем не менее, метод [**LoadAsync**](https://msdn.microsoft.com/library/windows/apps/hh701607) поддерживает и дополнительные форматы для обратной совместимости.

| Формат                    | Описание |
|---------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| InkSerializedFormat       | Определяет рукописный ввод, который постоянно использует формат ISF. Формат ISF — наиболее компактное постоянное представление рукописного ввода. Он может быть встроен в двоичный формат документа или помещен непосредственно в буфер обмена.                                                                                                                                                                                                         |
| Формат Base64InkSerializedFormat | Определяет рукописный ввод, который постоянно кодирует ISF как поток base64. Этот формат предоставляется для того, чтобы рукописный ввод был непосредственно закодирован в XML- или в HTML-файл.                                                                                                                                                                                                                                                |
| Формат GIF                       | Определяет рукописный ввод, который постоянно использует формат GIF, содержащий ISF как метаданные, встроенные в файл. Это позволяет просматривать рукописные фрагменты в приложениях без соответствующей функции, сохраняя при этом полноценное качество в приложениях с функцией рукописного ввода. Этот формат идеален при перемещении рукописного содержимого в рамках HTML-файла и для обеспечения его использования приложениями с функцией рукописного ввода и приложениями без нее. |
| Формат Base64Gif                 | Определяет рукописный ввод, который постоянно использует формат GIF, закодированный и усиленный форматом base64. Этот формат предоставляется, когда рукописный ввод должен быть закодирован непосредственно в XML- или HTML-файл для дальнейшего преобразования в изображение. Его можно использовать в формате XML, который создается для хранения всей информации о рукописном вводе и используется для создания HTML с помощью XSLT. 

## Копирование и вставка росчерков пера через буфер обмена


Здесь мы покажем, как использовать буфер обмена для перемещения росчерков пера между приложениями.

Чтобы обеспечить поддержку функции буфера обмена, для встроенных команд вырезания и копирования элемента [**InkStrokeContainer**](https://msdn.microsoft.com/library/windows/apps/br208492) необходимо выбрать один или несколько росчерков пера.

В этом примере мы добавляем возможность выбора росчерка, когда входные данные изменяются кнопкой пера (или правой кнопкой мыши). Полный пример реализации возможности выбора росчерка см. в подразделе [Передача входных данных для расширенной обработки](pen-and-stylus-interactions.md#passthrough) раздела [Взаимодействие с помощью пера](pen-and-stylus-interactions.md).

1.  Сначала мы настраиваем пользовательский интерфейс.

    Пользовательский интерфейс включает кнопки «Вырезать», «Копировать», «Вставить» и «Очистить», а также элемент [**InkCanvas**](https://msdn.microsoft.com/library/windows/apps/dn858535) и холст выбора.
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

    Элемент [**InkPresenter**](https://msdn.microsoft.com/library/windows/apps/dn899081) настраивается интерпретировать данные, вводимые пером или мышью, как росчерки пера ([**InputDeviceTypes**](https://msdn.microsoft.com/library/windows/apps/dn922019)). Кроме того, здесь объявляются прослушиватели для событий нажатия кнопок, а также событий указателя и событий росчерка для функции выбора.

    Полный пример реализации возможности выбора росчерка см. в подразделе [Передача входных данных для расширенной обработки](pen-and-stylus-interactions.md#passthrough) раздела [Взаимодействие с помощью пера](pen-and-stylus-interactions.md).
```    CSharp
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

    Чтобы вырезать, мы сначала вызываем [**CopySelectedToClipboard**](https://msdn.microsoft.com/library/windows/apps/br244232) в объекте [**InkStrokeContainer**](https://msdn.microsoft.com/library/windows/apps/br208492) элемента [**InkPresenter**](https://msdn.microsoft.com/library/windows/apps/dn922011).

    Затем мы вызываем [**DeleteSelected**](https://msdn.microsoft.com/library/windows/apps/br244233), чтобы убрать росчерки с холста рукописного ввода.

    Наконец, мы удаляем все росчерки выбора с холста выбора.
```    CSharp
private void btnCut_Click(object sender, RoutedEventArgs e)
    {
        inkCanvas.InkPresenter.StrokeContainer.CopySelectedToClipboard();
        inkCanvas.InkPresenter.StrokeContainer.DeleteSelected();
        ClearSelection();
    }
```
```    CSharp
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

    For copy, we simply call [**CopySelectedToClipboard**](https://msdn.microsoft.com/library/windows/apps/br244232) on the [**InkStrokeContainer**](https://msdn.microsoft.com/library/windows/apps/br208492) of the [**InkPresenter**](https://msdn.microsoft.com/library/windows/apps/dn922011).
```    CSharp
private void btnCopy_Click(object sender, RoutedEventArgs e)
    {
        inkCanvas.InkPresenter.StrokeContainer.CopySelectedToClipboard();
    }
```

    For paste, we call [**CanPasteFromClipboard**](https://msdn.microsoft.com/library/windows/apps/br208495) to ensure that the content on the clipboard can be pasted to the ink canvas.

    If so, we call [**PasteFromClipboard**](https://msdn.microsoft.com/library/windows/apps/br208503) to insert the clipboard ink strokes into the [**InkStrokeContainer**](https://msdn.microsoft.com/library/windows/apps/br208492) of the [**InkPresenter**](https://msdn.microsoft.com/library/windows/apps/dn922011), which then renders the strokes to the ink canvas.
```    CSharp
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

## Связанные разделы

* [Взаимодействие с помощью пера](pen-and-stylus-interactions.md)

**Примеры**
* [Пример рукописного ввода](http://go.microsoft.com/fwlink/p/?LinkID=620308)
* [Простой пример рукописного ввода](http://go.microsoft.com/fwlink/p/?LinkID=620312)
* [Сложный пример рукописного ввода](http://go.microsoft.com/fwlink/p/?LinkID=620314)
 

 







<!--HONumber=Jun16_HO5-->



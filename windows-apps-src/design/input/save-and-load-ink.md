---
author: Karl-Bridge-Microsoft
Description: UWP apps that support Windows Ink can serialize and deserialize ink strokes to an Ink Serialized Format (ISF) file. The ISF file is a GIF image with additional metadata for all ink stroke properties and behaviors. Apps that are not ink-enabled, can view the static GIF image, including alpha-channel background transparency.
title: Хранение и извлечение данных о росчерках пера Windows Ink
ms.assetid: C96C9D2F-DB69-4883-9809-4A0DF7CEC506
label: Store and retrieve Windows Ink stroke data
template: detail.hbs
keywords: Windows Ink, Windows Inking, DirectInk, InkPresenter, InkCanvas, ISF, сериализованный формат Ink, взаимодействие с пользователем, ввод
ms.author: kbridge
ms.date: 02/08/2017
ms.topic: article
ms.localizationpriority: medium
ms.openlocfilehash: 946e8421cf21c37c929e7a9f9687117c3d7aca92
ms.sourcegitcommit: cd00bb829306871e5103db481cf224ea7fb613f0
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/31/2018
ms.locfileid: "5878775"
---
# <a name="store-and-retrieve-windows-ink-stroke-data"></a>Хранение и извлечение данных о росчерках пера Windows Ink


Приложения UWP с поддержкой Windows Ink могут сериализовать и десериализовать росчерки пера в файл ISF. Файл ISF— это изображение GIF с дополнительными метаданными для всех свойств и поведений росчерков пера. Приложения без соответствующей функции могут просматривать статическое изображение GIF, включая прозрачный фон альфа-канала.

> [!NOTE]
> Формат ISF — наиболее компактное постоянное представление рукописного ввода. Он может быть встроен в двоичный формат документа, например GIF-файл, или помещен непосредственно в буфер обмена.

> **Важные API-интерфейсы**: [**InkCanvas**](https://msdn.microsoft.com/library/windows/apps/dn858535), [**Windows.UI.Input.Inking**](https://msdn.microsoft.com/library/windows/apps/br208524)

## <a name="save-ink-strokes-to-a-file"></a>Сохранение росчерков пера в файл

Здесь мы покажем, как сохранить росчерки пера, нарисованные в элементе управления [**InkCanvas**](https://msdn.microsoft.com/library/windows/apps/dn858535).

**Скачайте этот пример в разделе [Сохранение и загрузка росчерков пера из файла Ink Serialized Format (ISF)](https://github.com/MicrosoftDocs/windows-topic-specific-samples/archive/uwp-ink-store.zip)**

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

    [**FileSavePicker**](https://msdn.microsoft.com/library/windows/apps/br207871) позволяет пользователю выбрать и файл, и расположение, где будут сохраняться данные рукописного ввода.

    После выбора файла мы открываем поток [**IRandomAccessStream**](https://msdn.microsoft.com/library/windows/apps/br241731), для которого установлено значение [**ReadWrite**](https://msdn.microsoft.com/library/windows/apps/br241635).

    Затем мы вызываем [**SaveAsync**](https://msdn.microsoft.com/library/windows/apps/br242114), чтобы сериализовать росчерки пера, управляемые элементом [**InkStrokeContainer**](https://msdn.microsoft.com/library/windows/apps/br208492), в поток.

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
> GIF — единственный поддерживаемый формат файла для сохранения данных рукописного ввода. Однако метод [**LoadAsync**](https://msdn.microsoft.com/library/windows/apps/hh701607) (демонстрируемый в следующем разделе) поддерживает и дополнительные форматы для обратной совместимости.

## <a name="load-ink-strokes-from-a-file"></a>Загрузка росчерков пера из файла

Здесь мы покажем, как загрузить росчерки пера из файла и отобразить их в элементе управления [**InkCanvas**](https://msdn.microsoft.com/library/windows/apps/dn858535).

**Скачайте этот пример в разделе [Сохранение и загрузка росчерков пера из файла Ink Serialized Format (ISF)](https://github.com/MicrosoftDocs/windows-topic-specific-samples/archive/uwp-ink-store.zip)**

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

    [**FileOpenPicker**](https://msdn.microsoft.com/library/windows/apps/br207847) позволяет пользователю выбрать и файл, и расположение, откуда будут извлекаться сохраненные данные рукописного ввода.

    После выбора файла мы открываем поток [**IRandomAccessStream**](https://msdn.microsoft.com/library/windows/apps/br241731), для которого установлено значение [**Read**](https://msdn.microsoft.com/library/windows/apps/br241635).

    Затем мы вызываем [**LoadAsync**](https://msdn.microsoft.com/library/windows/apps/hh701607) для чтения, десериализации и загрузки сохраненных росчерков пера в элемент [**InkStrokeContainer**](https://msdn.microsoft.com/library/windows/apps/br208492). Загрузка росчерков в элемент **InkStrokeContainer** приводит к тому, что [**InkPresenter**](https://msdn.microsoft.com/library/windows/apps/dn899081) немедленно выводит их на [**InkCanvas**](https://msdn.microsoft.com/library/windows/apps/dn858535).

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
> GIF — единственный поддерживаемый формат файла для сохранения данных рукописного ввода. Тем не менее, метод [**LoadAsync**](https://msdn.microsoft.com/library/windows/apps/hh701607) поддерживает и дополнительные форматы для обратной совместимости.

| Формат                    | Описание |
|---------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| InkSerializedFormat       | Определяет рукописный ввод, который постоянно использует формат ISF. Формат ISF — наиболее компактное постоянное представление рукописного ввода. Он может быть встроен в двоичный формат документа или помещен непосредственно в буфер обмена.                                                                                                                                                                                                         |
| Формат Base64InkSerializedFormat | Определяет рукописный ввод, который постоянно кодирует ISF как поток base64. Этот формат предоставляется для того, чтобы рукописный ввод был непосредственно закодирован в XML- или в HTML-файл.                                                                                                                                                                                                                                                |
| Формат GIF                       | Определяет рукописный ввод, который постоянно использует формат GIF, содержащий ISF как метаданные, встроенные в файл. Это позволяет просматривать рукописные фрагменты в приложениях без соответствующей функции, сохраняя при этом полноценное качество в приложениях с функцией рукописного ввода. Этот формат идеален при перемещении рукописного содержимого в рамках HTML-файла и для обеспечения его использования приложениями с функцией рукописного ввода и приложениями без нее. |
| Формат Base64Gif                 | Определяет рукописный ввод, который постоянно использует формат GIF, закодированный и усиленный форматом base64. Этот формат предоставляется, когда рукописный ввод должен быть закодирован непосредственно в XML- или HTML-файл для дальнейшего преобразования в изображение. Его можно использовать в формате XML, который создается для хранения всей информации о рукописном вводе и используется для создания HTML с помощью XSLT. 

## <a name="copy-and-paste-ink-strokes-with-the-clipboard"></a>Копирование и вставка росчерков пера через буфер обмена

Здесь мы покажем, как использовать буфер обмена для перемещения росчерков пера между приложениями.

Чтобы обеспечить поддержку функции буфера обмена, для встроенных команд вырезания и копирования элемента [**InkStrokeContainer**](https://msdn.microsoft.com/library/windows/apps/br208492) необходимо выбрать один или несколько росчерков пера.

В этом примере мы добавляем возможность выбора росчерка, когда входные данные изменяются кнопкой пера (или правой кнопкой мыши). Полный пример реализации возможности выбора росчерка см. в подразделе "Передача входных данных для расширенной обработки" раздела [Взаимодействие с помощью пера](pen-and-stylus-interactions.md).

**Скачать этот пример в разделе [Сохранение и загрузка росчерков пера из буфера обмена](https://github.com/MicrosoftDocs/windows-topic-specific-samples/archive/uwp-ink-store-clipboard.zip)**

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

    Чтобы вырезать, мы сначала вызываем [**CopySelectedToClipboard**](https://msdn.microsoft.com/library/windows/apps/br244232) в объекте [**InkStrokeContainer**](https://msdn.microsoft.com/library/windows/apps/br208492) элемента [**InkPresenter**](https://msdn.microsoft.com/library/windows/apps/dn922011).

    Затем мы вызываем [**DeleteSelected**](https://msdn.microsoft.com/library/windows/apps/br244233), чтобы убрать росчерки с холста рукописного ввода.

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

Чтобы выполнить копирование, мы просто вызываем [**CopySelectedToClipboard**](https://msdn.microsoft.com/library/windows/apps/br244232) в объекте [**InkStrokeContainer**](https://msdn.microsoft.com/library/windows/apps/br208492) элемента [**InkPresenter**](https://msdn.microsoft.com/library/windows/apps/dn922011).


```csharp
private void btnCopy_Click(object sender, RoutedEventArgs e)
    {
        inkCanvas.InkPresenter.StrokeContainer.CopySelectedToClipboard();
    }
```

Чтобы вставить, мы вызываем [**CanPasteFromClipboard**](https://msdn.microsoft.com/library/windows/apps/br208495) для обеспечения того, что содержимое в буфере обмена можно вставить на холст рукописного ввода.

Если это так, мы вызываем [**PasteFromClipboard**](https://msdn.microsoft.com/library/windows/apps/br208503), чтобы вставить росчерки пера в объект [**InkStrokeContainer**](https://msdn.microsoft.com/library/windows/apps/br208492) элемента [**InkPresenter**](https://msdn.microsoft.com/library/windows/apps/dn922011), который затем выводит росчерки на холст рукописного ввода.

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

## <a name="related-articles"></a>Смежные разделы

* [Взаимодействие с помощью пера](pen-and-stylus-interactions.md)

**Примеры в разделе**
* [Сохранение и загрузка росчерков пера из файла Ink Serialized Format (ISF)](https://github.com/MicrosoftDocs/windows-topic-specific-samples/archive/uwp-ink-store.zip)
* [Сохранение и загрузка росчерков пера из буфера обмена](https://github.com/MicrosoftDocs/windows-topic-specific-samples/archive/uwp-ink-store-clipboard.zip)

**Другие примеры**
* [Простой пример рукописного ввода (C#/C++)](http://go.microsoft.com/fwlink/p/?LinkID=620312)
* [Сложный пример рукописного ввода (C++)](http://go.microsoft.com/fwlink/p/?LinkID=620314)
* [Пример рукописного ввода (JavaScript)](http://go.microsoft.com/fwlink/p/?LinkID=620308)
* [Руководство по началу работы: поддержка рукописного ввода в приложении UWP](https://aka.ms/appsample-ink)
* [Пример раскраски](https://aka.ms/cpubsample-coloringbook)
* [Пример семейных заметок](https://aka.ms/cpubsample-familynotessample)




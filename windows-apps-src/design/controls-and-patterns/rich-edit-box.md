---
author: Jwmsft
Description: You can use a RichEditBox control to enter and edit rich text documents that contain formatted text, hyperlinks, and images. You can make a RichEditBox read-only by setting its IsReadOnly property to true.
title: Элемент управления RichEditBox
ms.assetid: 4AFC0DFA-3B89-434D-9F86-4309CCFF7839
label: Rich edit box
template: detail.hbs
ms.author: jimwalk
ms.date: 05/19/2017
ms.topic: article
keywords: Windows 10, uwp
pm-contact: miguelrb
design-contact: ksulliv
doc-status: Published
ms.localizationpriority: medium
ms.openlocfilehash: 09b75eccdfcdd1d046618b92565fa3453a8b300e
ms.sourcegitcommit: d0e836dfc937ebf7dfa9c424620f93f3c8e0a7e8
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/26/2018
ms.locfileid: "5637765"
---
# <a name="rich-edit-box"></a>Блок форматируемого текста

 

Элемент управления RichEditBox можно использовать для ввода и правки документов в формате RTF, содержащих форматированный текст, изображения и гиперссылки. Элемент управления RichEditBox можно сделать доступным только для чтения, задав для его свойства IsReadOnly значение **true**.

> **Важные API-интерфейсы**: [класс RichEditBox](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.richeditbox.aspx), [свойство Document](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.richeditbox.document.aspx), [свойство IsReadOnly](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.richeditbox.isreadonly.aspx), [свойство IsSpellCheckEnabled](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.richeditbox.isspellcheckenabled.aspx)

## <a name="is-this-the-right-control"></a>Выбор правильного элемента управления

**RichEditBox** служит для отображения и правки текстовых файлов. RichEditBox не используется для получения пользовательского ввода в приложении, как в случае других стандартных текстовых полей. Он используется для работы с текстовыми файлами отдельно от вашего приложения. Обычно текст, введенный в элемент RichEditBox, сохраняется в RTF-файле.
-   Если основное назначение многострочного поля — создание документов (например, записей блога или сообщений электронной почты), которые требуют форматирования, используйте поле с форматом.
-   Если вы хотите предоставить пользователям возможность форматировать текст, используйте поле с форматом.
-   Если текст не предназначен для повторного отображения пользователям, используйте элементы управления для ввода обычного текста.
-   Во всех остальных сценариях используйте элементы управления для ввода обычного текста.

Дополнительные сведения о выборе подходящего элемента управления текстом см. в статье [Элементы управления текстом](text-controls.md).

## <a name="examples"></a>Примеры

<table>
<th align="left">Галерея элементов управления XAML<th>
<tr>
<td><img src="images/xaml-controls-gallery-sm.png" alt="XAML controls gallery"></img></td>
<td>
    <p>Если у вас установлено приложение <strong style="font-weight: semi-bold">галереи элементов управления XAML</strong>, щелкните здесь, чтобы <a href="xamlcontrolsgallery:/item/RichEditBox">открыть приложение и увидеть RichEditBox в действии</a>.</p>
    <ul>
    <li><a href="https://www.microsoft.com/store/productId/9MSVH128X2ZT">Скачать приложение галереи элементов управления XAML (Microsoft Store)</a></li>
    <li><a href="https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/XamlUIBasics">Получить исходный код (GitHub)</a></li>
    </ul>
</td>
</tr>
</table>

В этом поле с форматом открыт документ RTF. Кнопки форматирования и файла не являются частью поля с форматом, но необходимо предоставить по крайней мере минимальный набор кнопок оформления и реализовать их действия.

![Поле с форматом с открытым в нем документом](images/rich-edit-box.png)

## <a name="create-a-rich-edit-box"></a>Создание поля с форматом

По умолчанию элемент управления RichEditBox поддерживает проверку орфографии. Чтобы отключить функцию проверки орфографии, задайте для свойства [IsSpellCheckEnabled](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.richeditbox.isspellcheckenabled.aspx) значение **false**. Более подробные сведения см. в статье [Инструкции по проверке орфографии](text-controls.md).

Свойство [Document](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.richeditbox.document.aspx) элемента RichEditBox используется для получения его содержимого. Содержимое RichEditBox — это объект [Windows.UI.Text.ITextDocument](https://msdn.microsoft.com/library/windows/apps/xaml/bb774052.aspx), в отличие от элемента управления RichTextBlock, использующего объекты [Windows.UI.Xaml.Documents.Block](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.documents.block.aspx) в качестве своего содержимого. С помощью интерфейса ITextDocument можно загрузить и сохранить документ в потоке, получить фрагменты текста, выделить текст, отменить и повторить изменения, задать атрибуты форматирования по умолчанию и т.д.

В этом примере показано, как редактировать, загружать и сохранять файл в формате RTF в элементе управления RichEditBox.

```xaml
<RelativePanel Margin="20" HorizontalAlignment="Stretch">
    <RelativePanel.Resources>
        <Style TargetType="AppBarButton">
            <Setter Property="IsCompact" Value="True"/>
        </Style>
    </RelativePanel.Resources>
    <AppBarButton x:Name="openFileButton" Icon="OpenFile"
                  Click="OpenButton_Click" ToolTipService.ToolTip="Open file"/>
    <AppBarButton Icon="Save" Click="SaveButton_Click"
                  ToolTipService.ToolTip="Save file"
                  RelativePanel.RightOf="openFileButton" Margin="8,0,0,0"/>

    <AppBarButton Icon="Bold" Click="BoldButton_Click" ToolTipService.ToolTip="Bold"
                  RelativePanel.LeftOf="italicButton" Margin="0,0,8,0"/>
    <AppBarButton x:Name="italicButton" Icon="Italic" Click="ItalicButton_Click"
                  ToolTipService.ToolTip="Italic" RelativePanel.LeftOf="underlineButton" Margin="0,0,8,0"/>
    <AppBarButton x:Name="underlineButton" Icon="Underline" Click="UnderlineButton_Click"
                  ToolTipService.ToolTip="Underline" RelativePanel.AlignRightWithPanel="True"/>

    <RichEditBox x:Name="editor" Height="200" RelativePanel.Below="openFileButton"
                 RelativePanel.AlignLeftWithPanel="True" RelativePanel.AlignRightWithPanel="True"/>
</RelativePanel>
```


```csharp
private async void OpenButton_Click(object sender, RoutedEventArgs e)
{
    // Open a text file.
    Windows.Storage.Pickers.FileOpenPicker open =
        new Windows.Storage.Pickers.FileOpenPicker();
    open.SuggestedStartLocation =
        Windows.Storage.Pickers.PickerLocationId.DocumentsLibrary;
    open.FileTypeFilter.Add(".rtf");

    Windows.Storage.StorageFile file = await open.PickSingleFileAsync();

    if (file != null)
    {
        try
        {
            Windows.Storage.Streams.IRandomAccessStream randAccStream =
        await file.OpenAsync(Windows.Storage.FileAccessMode.Read);

            // Load the file into the Document property of the RichEditBox.
            editor.Document.LoadFromStream(Windows.UI.Text.TextSetOptions.FormatRtf, randAccStream);
        }
        catch (Exception)
        {
            ContentDialog errorDialog = new ContentDialog()
            {
                Title = "File open error",
                Content = "Sorry, I couldn't open the file.",
                PrimaryButtonText = "Ok"
            };

            await errorDialog.ShowAsync();
        }
    }
}

private async void SaveButton_Click(object sender, RoutedEventArgs e)
{
    Windows.Storage.Pickers.FileSavePicker savePicker = new Windows.Storage.Pickers.FileSavePicker();
    savePicker.SuggestedStartLocation = Windows.Storage.Pickers.PickerLocationId.DocumentsLibrary;

    // Dropdown of file types the user can save the file as
    savePicker.FileTypeChoices.Add("Rich Text", new List<string>() { ".rtf" });

    // Default file name if the user does not type one in or select a file to replace
    savePicker.SuggestedFileName = "New Document";

    Windows.Storage.StorageFile file = await savePicker.PickSaveFileAsync();
    if (file != null)
    {
        // Prevent updates to the remote version of the file until we
        // finish making changes and call CompleteUpdatesAsync.
        Windows.Storage.CachedFileManager.DeferUpdates(file);
        // write to file
        Windows.Storage.Streams.IRandomAccessStream randAccStream =
            await file.OpenAsync(Windows.Storage.FileAccessMode.ReadWrite);

        editor.Document.SaveToStream(Windows.UI.Text.TextGetOptions.FormatRtf, randAccStream);

        // Let Windows know that we're finished changing the file so the
        // other app can update the remote version of the file.
        Windows.Storage.Provider.FileUpdateStatus status = await Windows.Storage.CachedFileManager.CompleteUpdatesAsync(file);
        if (status != Windows.Storage.Provider.FileUpdateStatus.Complete)
        {
            Windows.UI.Popups.MessageDialog errorBox =
                new Windows.UI.Popups.MessageDialog("File " + file.Name + " couldn't be saved.");
            await errorBox.ShowAsync();
        }
    }
}

private void BoldButton_Click(object sender, RoutedEventArgs e)
{
    Windows.UI.Text.ITextSelection selectedText = editor.Document.Selection;
    if (selectedText != null)
    {
        Windows.UI.Text.ITextCharacterFormat charFormatting = selectedText.CharacterFormat;
        charFormatting.Bold = Windows.UI.Text.FormatEffect.Toggle;
        selectedText.CharacterFormat = charFormatting;
    }
}

private void ItalicButton_Click(object sender, RoutedEventArgs e)
{
    Windows.UI.Text.ITextSelection selectedText = editor.Document.Selection;
    if (selectedText != null)
    {
        Windows.UI.Text.ITextCharacterFormat charFormatting = selectedText.CharacterFormat;
        charFormatting.Italic = Windows.UI.Text.FormatEffect.Toggle;
        selectedText.CharacterFormat = charFormatting;
    }
}

private void UnderlineButton_Click(object sender, RoutedEventArgs e)
{
    Windows.UI.Text.ITextSelection selectedText = editor.Document.Selection;
    if (selectedText != null)
    {
        Windows.UI.Text.ITextCharacterFormat charFormatting = selectedText.CharacterFormat;
        if (charFormatting.Underline == Windows.UI.Text.UnderlineType.None)
        {
            charFormatting.Underline = Windows.UI.Text.UnderlineType.Single;
        }
        else {
            charFormatting.Underline = Windows.UI.Text.UnderlineType.None;
        }
        selectedText.CharacterFormat = charFormatting;
    }
}
```

## <a name="choose-the-right-keyboard-for-your-text-control"></a>Выбор подходящей клавиатуры для элемента управления текстом

Чтобы упростить пользователям ввод данных с помощью сенсорной клавиатуры или панели функционального ввода, можно настроить тип вводимых данных элемента управления для ввода текста, чтобы он соответствовал типу данных, которые должен вводить пользователь. Раскладка клавиатуры по умолчанию обычно подходит для работы с документами RTF.

Дополнительные сведения об использовании типов вводимых данных см. в разделе [Использование типа вводимых данных для изменения сенсорной клавиатуры](https://msdn.microsoft.com/library/windows/apps/mt280229).

## <a name="dos-and-donts"></a>Возможности и ограничения

- При создании текстового поля в формате RTF следует предусмотреть кнопки стиля и реализовать их действия.
- Используйте шрифт, соответствующий стилю вашего приложения.
- Высота элемента управления должна быть достаточной для ввода стандартных записей.
- При вводе пользователем текста элементы управления не должны увеличиваться по высоте.
- Не используйте поле для многострочного текста там, где требуется ввести всего одну строку.
- Не используйте поле с форматом там, где достаточно элемента управления обычного текста.

## <a name="get-the-sample-code"></a>Получить пример кода

- [Образец галереи элементов управления XAML](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/XamlUIBasics) — ознакомьтесь со всеми элементами управления XAML в интерактивном формате.

## <a name="related-articles"></a>Еще по теме

- [Текстовые элементы управления](text-controls.md)
- [Руководство по проверке орфографии](text-controls.md)
- [Добавление поиска](search.md)
- [Руководство по текстовому вводу](text-controls.md)
- [Класс TextBox](https://msdn.microsoft.com/library/windows/apps/br209683)
- [Класс Windows.UI.Xaml.Controls PasswordBox](https://msdn.microsoft.com/library/windows/apps/br227519)

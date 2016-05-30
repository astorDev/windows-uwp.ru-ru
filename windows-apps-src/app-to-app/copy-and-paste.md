---
description: В этой статье объясняется, как обеспечить поддержку копирования и вставки в приложениях универсальной платформы Windows (UWP) с помощью буфера обмена.
title: Копирование и вставка
ms.assetid: E882DC15-E12D-4420-B49D-F495BB484BEE
author: awkoren
---
#Копирование и вставка

\[ Обновлено для приложений UWP в Windows 10. Статьи для Windows 8.x можно найти в [архиве](http://go.microsoft.com/fwlink/p/?linkid=619132) \]


В этой статье объясняется, как обеспечить поддержку копирования и вставки в приложениях универсальной платформы Windows (UWP) с помощью буфера обмена. Копирование и вставка — классический способ обмена данными между приложениями или внутри одного приложения. Почти каждое приложение в той или иной степени поддерживает операции буфера обмена.

## Проверка встроенной поддержки буфера обмена


Во многих случаях вам не потребуется писать какой-либо код для поддержки операций буфера обмена. Многие элементы управления XAML, которые вы по умолчанию можете использовать для создания приложений, поддерживают операции буфера обмена. Подробнее о доступных элементах управления см. в [списке элементов управления][ControlsList].

## Подготовка

Сначала добавьте в приложение пространство имен [**Windows.ApplicationModel.DataTransfer**][DataTransfer]. Затем добавьте экземпляр объекта [**DataPackage**][DataPackage]. Этот объект содержит данные, которые пользователь хочет скопировать, и все свойства (например описание), которые вы захотите в него включить.

<!-- For some reason, the snippets in this file are all inline in the WDCML topic. Suggest moving to VS project with rest of snippets. -->
```cs
DataPackage dataPackage = new DataPackage();
```

## Копирование и вырезание

Копирование и вырезание (или по-другому перемещение) работают практически так же. Выберите необходимую операцию с помощью свойства [**DataPackage.RequestedOperation**][RequestedOperation].

```cs
// copy 
dataPackage.RequestedOperation = DataPackageOperation.Copy;
// or cut
dataPackage.RequestedOperation = DataPackageOperation.Move;
```

Затем вы можете добавить выбранные пользователем данные в объект [**DataPackage**][DataPackage]. Если класс **DataPackage** поддерживает эти данные, вы можете использовать один из соответствующих методов в объекте **DataPackage**. Вот как добавить текст:

```cs
dataPackage.SetText("Hello World!");
```

Наконец добавьте [**DataPackage**][DataPackage] в буфер обмена, вызвав статический метод [**Clipboard.SetContent**][SetContent].

```cs
Clipboard.SetContent(dataPackage);
```
## Вставка

Чтобы получить содержимое буфера обмена, вызовите статический метод [**Clipboard.GetContent**[GetContent]. Этот метод возвращает объект [**DataPackageView**][DataPackageView] с его содержимым. Этот объект практически идентичен объекту [**DataPackage**][DataPackage], но его содержимое доступно только для чтения. Чтобы определить доступные форматы данных, вы можете использовать метод [**AvailableFormats**][AvailableFormats] или [**Contains**][Contains] этого объекта. Затем вы можете вызвать соответствующий метод **DataPackageView**, чтобы получить эти данные.

```cs
DataPackageView dataPackageView = Clipboard.GetContent();
if (dataPackageView.Contains(StandardDataFormats.Text))
{
    string text = await dataPackageView.GetTextAsync();
    // To output the text from this example, you need a TextBlock control
    TextOutput.Text = "Clipboard now contains: " + text;
}
```

## Отслеживание изменений в буфере обмена

В дополнение к командам копирования и вставки вам также может потребоваться отслеживать изменения в буфере обмена. Это можно сделать с помощью обработки события [**Clipboard.ContentChanged**][ContentChanged] из буфера обмена.

```cs
Clipboard.ContentChanged += (s, e) => 
{
    DataPackageView dataPackageView = Clipboard.GetContent();
    if (dataPackageView.Contains(StandardDataFormats.Text))
    {
        string text = await dataPackageView.GetTextAsync();
        // To output the text from this example, you need a TextBlock control
        TextOutput.Text = "Clipboard now contains: " + text;
    }
}
```

<!-- LINKS --> 
[DataTransfer]: https://msdn.microsoft.com/en-us/library/windows/apps/windows.applicationmodel.datatransfer.aspx 
[DataPackage]: https://msdn.microsoft.com/en-us/library/windows/apps/windows.applicationmodel.datatransfer.datapackage.aspx 
[DataPackageView]: https://msdn.microsoft.com/en-us/library/windows/apps/windows.applicationmodel.datatransfer.datapackageview.aspx
[DataPackagePropertySet]: https://msdn.microsoft.com/en-us/library/windows/apps/windows.applicationmodel.datatransfer.datapackagepropertyset.aspx 
[DataRequest]: https://msdn.microsoft.com/en-us/library/windows/apps/windows.applicationmodel.datatransfer.datarequest.aspx 
[DataRequested]: https://msdn.microsoft.com/en-us/library/windows/apps/windows.applicationmodel.datatransfer.datatransfermanager.datarequested.aspx 
[FailWithDisplayText]: https://msdn.microsoft.com/en-us/library/windows/apps/windows.applicationmodel.datatransfer.datarequest.failwithdisplaytext.aspx
[ShowShareUi]: https://msdn.microsoft.com/en-us/library/windows/apps/windows.applicationmodel.datatransfer.datatransfermanager.showshareui.aspx
[RequestedOperation]: https://msdn.microsoft.com/en-us/library/windows/apps/windows.applicationmodel.datatransfer.datapackage.requestedoperation.aspx 
[ControlsList]: https://msdn.microsoft.com/en-us/library/windows/apps/xaml/mt185406.aspx 
[SetContent]: https://msdn.microsoft.com/en-us/library/windows/apps/xaml/windows.applicationmodel.datatransfer.clipboard.setcontent.aspx 
[GetContent]: https://msdn.microsoft.com/en-us/library/windows/apps/xaml/windows.applicationmodel.datatransfer.clipboard.getcontent.aspx
[AvailableFormats]: https://msdn.microsoft.com/en-us/library/windows/apps/windows.applicationmodel.datatransfer.datapackageview.availableformats.aspx 
[Contains]: https://msdn.microsoft.com/en-us/library/windows/apps/windows.applicationmodel.datatransfer.datapackageview.contains.aspx
[ContentChanged]: https://msdn.microsoft.com/en-us/library/windows/apps/xaml/windows.applicationmodel.datatransfer.clipboard.contentchanged.aspx 



<!--HONumber=May16_HO2-->



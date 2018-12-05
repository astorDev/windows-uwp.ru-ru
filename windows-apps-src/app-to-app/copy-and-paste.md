---
description: В этой статье объясняется, как обеспечить поддержку копирования и вставки в приложениях универсальной платформы Windows (UWP) с помощью буфера обмена.
title: Копирование и вставка
ms.assetid: E882DC15-E12D-4420-B49D-F495BB484BEE
ms.date: 02/08/2017
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: ba498a9bc6541f45f01e74b1ea90f4f9a1bfde90
ms.sourcegitcommit: d7613c791107f74b6a3dc12a372d9de916c0454b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/05/2018
ms.locfileid: "8736972"
---
# <a name="copy-and-paste"></a>Копирование и вставка

В этой статье объясняется, как обеспечить поддержку копирования и вставки в приложениях универсальной платформы Windows (UWP) с помощью буфера обмена. Копирование и вставка — классический способ обмена данными между приложениями или внутри одного приложения. Почти каждое приложение в той или иной степени поддерживает операции буфера обмена.

## <a name="check-for-built-in-clipboard-support"></a>Проверка встроенной поддержки буфера обмена

Во многих случаях вам не потребуется писать какой-либо код для поддержки операций буфера обмена. Многие элементы управления XAML, которые вы по умолчанию можете использовать для создания приложений, поддерживают операции буфера обмена. 

## <a name="get-set-up"></a>Подготовка

Сначала добавьте в приложение пространство имен [**Windows.ApplicationModel.DataTransfer**](https://msdn.microsoft.com/library/windows/apps/Windows.ApplicationModel.DataTransfer). Затем добавьте экземпляр объекта [**DataPackage**](https://msdn.microsoft.com/library/windows/apps/Windows.ApplicationModel.DataTransfer.DataPackage). Этот объект содержит данные, которые пользователь хочет скопировать, и все свойства (например описание), которые вы захотите в него включить.

<!-- For some reason, the snippets in this file are all inline in the WDCML topic. Suggest moving to VS project with rest of snippets. -->
```cs
DataPackage dataPackage = new DataPackage();
```

<!-- AuthenticateAsync-->

## <a name="copy-and-cut"></a>Копирование и вырезание

Копирование и вырезание (или по-другому *перемещение*) работают практически так же. Выберите необходимую операцию с помощью свойства [**RequestedOperation**](https://msdn.microsoft.com/library/windows/apps/Windows.ApplicationModel.DataTransfer.DataPackage.RequestedOperation).

```cs
// copy 
dataPackage.RequestedOperation = DataPackageOperation.Copy;
// or cut
dataPackage.RequestedOperation = DataPackageOperation.Move;
```
## <a name="drag-and-drop"></a>Перетаскивание

Затем вы можете добавить выбранные пользователем данные в объект [**DataPackage**](https://msdn.microsoft.com/library/windows/apps/Windows.ApplicationModel.DataTransfer.DataPackage). Если класс **DataPackage** поддерживает эти данные, вы можете использовать один из соответствующих методов в объекте **DataPackage**. Вот как добавить текст:

```cs
dataPackage.SetText("Hello World!");
```

Наконец добавьте [**DataPackage**](https://msdn.microsoft.com/library/windows/apps/Windows.ApplicationModel.DataTransfer.DataPackage) в буфер обмена, вызвав статический метод [**SetContent**](https://msdn.microsoft.com/library/windows/apps/Windows.ApplicationModel.DataTransfer.Clipboard.SetContent(Windows.ApplicationModel.DataTransfer.DataPackage)).

```cs
Clipboard.SetContent(dataPackage);
```
## <a name="paste"></a>Вставка

Чтобы получить содержимое буфера обмена, вызовите статический метод [**GetContent**](https://msdn.microsoft.com/library/windows/apps/Windows.ApplicationModel.DataTransfer.Clipboard.GetContent). Этот метод возвращает объект [**DataPackageView**](https://msdn.microsoft.com/library/windows/apps/Windows.ApplicationModel.DataTransfer.DataPackageView) с содержимым. Этот объект практически идентичен объекту [**DataPackage**](https://msdn.microsoft.com/library/windows/apps/Windows.ApplicationModel.DataTransfer.DataPackage), но его содержимое доступно только для чтения. Чтобы определить доступные форматы данных, вы можете использовать метод [**AvailableFormats**](https://msdn.microsoft.com/library/windows/apps/Windows.ApplicationModel.DataTransfer.DataPackageView.AvailableFormats) или [**Contains**](https://msdn.microsoft.com/library/windows/apps/Windows.ApplicationModel.DataTransfer.DataPackageView.Contains(System.String)) этого объекта. Затем вы можете вызвать соответствующий метод [**DataPackageView**](https://msdn.microsoft.com/library/windows/apps/Windows.ApplicationModel.DataTransfer.DataPackageView), чтобы получить эти данные.

```cs
async void OutputClipboardText()
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

## <a name="track-changes-to-the-clipboard"></a>Отслеживание изменений в буфере обмена

В дополнение к командам копирования и вставки вам также может потребоваться отслеживать изменения в буфере обмена. Это можно сделать путем обработки события [**ContentChanged**](https://msdn.microsoft.com/library/windows/apps/Windows.ApplicationModel.DataTransfer.Clipboard.ContentChanged) буфера обмена.

```cs
Clipboard.ContentChanged += async (s, e) => 
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

## <a name="see-also"></a>См. также

* [Связь между приложениями](index.md)
* [DataTransfer](https://msdn.microsoft.com/library/windows/apps/windows.applicationmodel.datatransfer.aspx)
* [DataPackage](https://msdn.microsoft.com/library/windows/apps/windows.applicationmodel.datatransfer.datapackage.aspx)
* [DataPackageView](https://msdn.microsoft.com/library/windows/apps/windows.applicationmodel.datatransfer.datapackageview.aspx)
* [DataPackagePropertySet]( https://msdn.microsoft.com/library/windows/apps/windows.applicationmodel.datatransfer.datapackagepropertyset.aspx)
* [DataRequest](https://msdn.microsoft.com/library/windows/apps/windows.applicationmodel.datatransfer.datarequest.aspx) 
* [DataRequested]( https://msdn.microsoft.com/library/windows/apps/windows.applicationmodel.datatransfer.datatransfermanager.datarequested.aspx)
* [FailWithDisplayText](https://msdn.microsoft.com/library/windows/apps/windows.applicationmodel.datatransfer.datarequest.failwithdisplaytext.aspx)
* [ShowShareUi](https://msdn.microsoft.com/library/windows/apps/windows.applicationmodel.datatransfer.datatransfermanager.showshareui.aspx)
* [RequestedOperation](https://msdn.microsoft.com/library/windows/apps/windows.applicationmodel.datatransfer.datapackage.requestedoperation.aspx) 
* [ControlsList](https://msdn.microsoft.com/library/windows/apps/xaml/mt185406.aspx)
* [SetContent](https://msdn.microsoft.com/library/windows/apps/xaml/windows.applicationmodel.datatransfer.clipboard.setcontent.aspx)
* [GetContent](https://msdn.microsoft.com/library/windows/apps/xaml/windows.applicationmodel.datatransfer.clipboard.getcontent.aspx)
* [AvailableFormats](https://msdn.microsoft.com/library/windows/apps/windows.applicationmodel.datatransfer.datapackageview.availableformats.aspx)
* [Contains](https://msdn.microsoft.com/library/windows/apps/windows.applicationmodel.datatransfer.datapackageview.contains.aspx)
* [ContentChanged](https://msdn.microsoft.com/library/windows/apps/xaml/windows.applicationmodel.datatransfer.clipboard.contentchanged.aspx)


---
description: В этой статье объясняется, как обеспечить поддержку копирования и вставки в приложениях универсальной платформы Windows (UWP) с помощью буфера обмена.
title: Копирование и вставка
ms.assetid: E882DC15-E12D-4420-B49D-F495BB484BEE
ms.date: 12/19/2019
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: dfa5c15c2bd4d82588e0b197dc265c4b529e64c9
ms.sourcegitcommit: cc108c791842789464c38a10e5d596c9bd878871
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/20/2019
ms.locfileid: "75302688"
---
# <a name="copy-and-paste"></a>Копирование и вставка

В этой статье объясняется, как обеспечить поддержку копирования и вставки в приложениях универсальной платформы Windows (UWP) с помощью буфера обмена. Копирование и вставка — классический способ обмена данными между приложениями или внутри одного приложения. Почти каждое приложение в той или иной степени поддерживает операции буфера обмена. Полные примеры кода, демонстрирующие несколько различных сценариев копирования и вставки, см. в разделе [Пример буфера обмена](https://github.com/microsoft/Windows-universal-samples/tree/master/Samples/Clipboard).

## <a name="check-for-built-in-clipboard-support"></a>Проверка встроенной поддержки буфера обмена

Во многих случаях вам не потребуется писать какой-либо код для поддержки операций буфера обмена. Многие элементы управления XAML, которые вы по умолчанию можете использовать для создания приложений, поддерживают операции буфера обмена. 

## <a name="get-set-up"></a>Подготовка

Сначала добавьте в приложение пространство имен [**Windows.ApplicationModel.DataTransfer**](https://docs.microsoft.com/uwp/api/Windows.ApplicationModel.DataTransfer). Затем добавьте экземпляр объекта [**DataPackage**](https://docs.microsoft.com/uwp/api/Windows.ApplicationModel.DataTransfer.DataPackage). Этот объект содержит данные, которые пользователь хочет скопировать, и все свойства (например описание), которые вы захотите в него включить.

```cs
DataPackage dataPackage = new DataPackage();
```

<!-- AuthenticateAsync-->

## <a name="copy-and-cut"></a>Копирование и вырезание

Копирование и вырезание (или по-другому *перемещение*) работают практически так же. Выберите необходимую операцию с помощью свойства [**RequestedOperation**](https://docs.microsoft.com/uwp/api/windows.applicationmodel.datatransfer.datapackage.requestedoperation).

```cs
// copy 
dataPackage.RequestedOperation = DataPackageOperation.Copy;
// or cut
dataPackage.RequestedOperation = DataPackageOperation.Move;
```

## <a name="set-the-copied-content"></a>Задание скопированного содержимого

Затем вы можете добавить выбранные пользователем данные в объект [**DataPackage**](https://docs.microsoft.com/uwp/api/Windows.ApplicationModel.DataTransfer.DataPackage). Если эти данные поддерживаются классом **Package** , можно использовать один из соответствующих [методов](https://docs.microsoft.com/uwp/api/windows.applicationmodel.datatransfer.datapackage#methods) объекта **Package** . Вот как можно добавить текст с помощью метода [**SetText**](https://docs.microsoft.com/uwp/api/windows.applicationmodel.datatransfer.datapackage.settext) :

```cs
dataPackage.SetText("Hello World!");
```

Наконец добавьте [**DataPackage**](https://docs.microsoft.com/uwp/api/Windows.ApplicationModel.DataTransfer.DataPackage) в буфер обмена, вызвав статический метод [**SetContent**](https://docs.microsoft.com/uwp/api/windows.applicationmodel.datatransfer.clipboard.setcontent).

```cs
Clipboard.SetContent(dataPackage);
```

## <a name="paste"></a>Вставка

Чтобы получить содержимое буфера обмена, вызовите статический метод [**GetContent**](https://docs.microsoft.com/uwp/api/windows.applicationmodel.datatransfer.clipboard.getcontent). Этот метод возвращает объект [**DataPackageView**](https://docs.microsoft.com/uwp/api/Windows.ApplicationModel.DataTransfer.DataPackageView) с содержимым. Этот объект практически идентичен объекту [**DataPackage**](https://docs.microsoft.com/uwp/api/Windows.ApplicationModel.DataTransfer.DataPackage), но его содержимое доступно только для чтения. Чтобы определить доступные форматы данных, вы можете использовать метод [**AvailableFormats**](https://docs.microsoft.com/uwp/api/windows.applicationmodel.datatransfer.datapackageview.availableformats) или [**Contains**](https://docs.microsoft.com/uwp/api/windows.applicationmodel.datatransfer.datapackageview.contains) этого объекта. Затем вы можете вызвать соответствующий метод [**DataPackageView**](https://docs.microsoft.com/uwp/api/Windows.ApplicationModel.DataTransfer.DataPackageView), чтобы получить эти данные.

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

В дополнение к командам копирования и вставки вам также может потребоваться отслеживать изменения в буфере обмена. Это можно сделать путем обработки события [**ContentChanged**](https://docs.microsoft.com/uwp/api/windows.applicationmodel.datatransfer.clipboard.contentchanged) буфера обмена.

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

* [Пример буфера обмена](https://github.com/microsoft/Windows-universal-samples/tree/master/Samples/Clipboard)
* [Связь между приложениями](index.md)
* [DataTransfer](https://docs.microsoft.com/uwp/api/windows.applicationmodel.datatransfer)
* [DataPackage](https://docs.microsoft.com/uwp/api/windows.applicationmodel.datatransfer.datapackage)
* [датапаккажевиев](https://docs.microsoft.com/uwp/api/windows.applicationmodel.datatransfer.datapackageview)
* [датапаккажепропертисет]( https://msdn.microsoft.com/library/windows/apps/windows.applicationmodel.datatransfer.datapackagepropertyset.aspx)
* [Запрос на](https://docs.microsoft.com/uwp/api/windows.applicationmodel.datatransfer.datarequest) 
* [Запрос только для запроса]( https://msdn.microsoft.com/library/windows/apps/windows.applicationmodel.datatransfer.datatransfermanager.datarequested.aspx)
* [фаилвисдисплайтекст](https://docs.microsoft.com/uwp/api/windows.applicationmodel.datatransfer.datarequest.failwithdisplaytext)
* [шовшареуи](https://docs.microsoft.com/uwp/api/windows.applicationmodel.datatransfer.datatransfermanager.showshareui)
* [RequestedOperation](https://docs.microsoft.com/uwp/api/windows.applicationmodel.datatransfer.datapackage.requestedoperation) 
* [контролслист](https://docs.microsoft.com/windows/uwp/design/controls-and-patterns/)
* [сетконтент](https://docs.microsoft.com/uwp/api/windows.applicationmodel.datatransfer.clipboard.setcontent)
* [Контент](https://docs.microsoft.com/uwp/api/windows.applicationmodel.datatransfer.clipboard.getcontent)
* [аваилаблеформатс](https://docs.microsoft.com/uwp/api/windows.applicationmodel.datatransfer.datapackageview.availableformats)
* [Содержащих](https://docs.microsoft.com/uwp/api/windows.applicationmodel.datatransfer.datapackageview.contains)
* [контентчанжед](https://docs.microsoft.com/uwp/api/windows.applicationmodel.datatransfer.clipboard.contentchanged)


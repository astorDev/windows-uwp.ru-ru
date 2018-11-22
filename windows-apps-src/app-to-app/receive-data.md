---
description: В данной статье рассказывается, как в приложении универсальной платформы Windows (UWP) получить содержимое, общий доступ к которому предоставлен из другого приложения с помощью контракта отправки данных. Контракт отправки данных позволяет предлагать ваше приложение как вариант, когда пользователь вызывает функцию общего доступа.
title: Получение данных
ms.assetid: 0AFF9E0D-DFF4-4018-B393-A26B11AFDB41
author: msatranjr
ms.author: misatran
ms.date: 02/08/2017
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: 7d64e4a2d4251aca6bbce39b5f24e3e5f35295b8
ms.sourcegitcommit: 93c0a60cf531c7d9fe7b00e7cf78df86906f9d6e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/21/2018
ms.locfileid: "7563275"
---
# <a name="receive-data"></a>Получение данных



В данной статье рассказывается, как в приложении универсальной платформы Windows (UWP) получить содержимое, общий доступ к которому предоставлен из другого приложения с помощью контракта отправки данных. Контракт отправки данных позволяет предлагать ваше приложение как вариант, когда пользователь вызывает функцию общего доступа.

## <a name="declare-your-app-as-a-share-target"></a>Объявление приложения получателем данных

Система показывает список возможных конечных приложений для получения данных, когда пользователь вызывает функцию общего доступа. Для отображения в списке ваше приложения должно объявить поддержку контракта отправки данных. Это сообщает системе, что приложение может получать содержимое.

1.  Откройте файл манифеста. У него должно быть примерно следующее имя: **package.appxmanifest**.
2.  Откройте вкладку **Объявления**.
3.  В списке **Доступные объявления** выберите **получателя данных** и нажмите кнопку **Добавить**.

## <a name="choose-file-types-and-formats"></a>Выбор форматов и типов файлов

Затем укажите поддерживаемые типы файлов и форматы данных. API общего доступа поддерживают несколько стандартных форматов, таких как текст, HTML и растровые изображения. Вы также можете задать пользовательские типы файлов и форматы данных. При этом помните: приложения-источники должны распознавать эти типы и форматы, иначе приложения не смогут использовать их для общего доступа к данным.

Регистрируйте только те форматы, которые могут обрабатываться вашим приложением. Когда пользователь вызывает функцию общего доступа, отображаются только те конечные приложения, которые поддерживают формат отправляемых данных.

Чтобы задать необходимые типы файлов, выполните указанные ниже действия.

1.  Откройте файл манифеста. У него должно быть примерно следующее имя: **package.appxmanifest**.
2.  В разделе **Поддерживаемые типы файлов** страницы **Объявления** нажмите кнопку **Добавить**.
3.  Введите расширение имени файла, которое вы хотите поддерживать, например ".docx". Введите точку. Если вы хотите поддерживать все типы файлов, установите флажок **SupportsAnyFileType**.

Чтобы задать необходимые форматы данных, выполните указанные ниже действия.

1.  Откройте файл манифеста.
2.  В разделе **Форматы данных** страницы **Объявления** нажмите кнопку **Добавить**.
3.  Введите имя поддерживаемого формата данных, например "Текст".

## <a name="handle-share-activation"></a>Обработка активации общего доступа

Когда пользователь выбирает ваше приложение (обычно из списка доступных приложений-получателей данных в пользовательском интерфейсе общего ресурса), создается событие [**OnShareTargetActivated**](https://msdn.microsoft.com/library/windows/apps/Windows.UI.Xaml.Application.OnShareTargetActivated(Windows.ApplicationModel.Activation.ShareTargetActivatedEventArgs)). Ваше приложение должно обработать это событие, чтобы принять данные, к которым пользователь хочет предоставить общий доступ.

<!-- For some reason, the snippets in this file are all inline in the WDCML topic. Suggest moving to VS project with rest of snippets. -->
```cs
protected override async void OnShareTargetActivated(ShareTargetActivatedEventArgs args)
{
    // Code to handle activation goes here. 
} 
```

Данные, которые хочет предоставить пользователь, содержатся в объекте [**ShareOperation**](https://msdn.microsoft.com/library/windows/apps/Windows.ApplicationModel.DataTransfer.ShareTarget.ShareOperation). Этот объект можно использовать для проверки формата содержащихся в нем данных.

```cs
ShareOperation shareOperation = args.ShareOperation;
if (shareOperation.Data.Contains(StandardDataFormats.Text))
{
    string text = await shareOperation.Data.GetTextAsync();

    // To output the text from this example, you need a TextBlock control
    // with a name of "sharedContent".
    sharedContent.Text = "Text: " + text;
} 
```

## <a name="report-sharing-status"></a>Отчет о состоянии общего доступа

В некоторых случаях вашей программе может потребоваться время на обработку данных, предназначенных для общего доступа. Примером является предоставление пользователями общего доступа к коллекциям своих файлов или изображений. Размер этих элементов превышает размер простой текстовой строки, поэтому их обработка занимает больше времени.

```cs
shareOperation.ReportStarted(); 
```

После вызова метода [**ReportStarted**](https://msdn.microsoft.com/library/windows/apps/Windows.ApplicationModel.DataTransfer.ShareTarget.ShareOperation.ReportStarted) не стоит рассчитывать на продолжение взаимодействия пользователя с вашим приложением. Поэтому вам не следует вызывать этот метод, если пользователь еще не готов закрыть ваше приложение.

При выборе расширенного общего доступа пользователь может закрыть исходное приложение раньше, чем все данные от объекта DataPackage будут получены вашей программой. Поэтому мы рекомендуем уведомлять систему, когда ваше приложение получит все требуемые данные. В этом случае система сможет приостановить или завершить работу исходной программы, если это требуется.

```cs
shareOperation.ReportSubmittedBackgroundTask(); 
```

При возникновении сбоя вызовите [**ReportError**](https://msdn.microsoft.com/library/windows/apps/Windows.ApplicationModel.DataTransfer.ShareTarget.ShareOperation.ReportError(System.String)), чтобы отправить системе сообщение об ошибке. Пользователь увидит это сообщение при проверке состояния общего доступа. В этот момент ваше приложение завершило работу, и общий доступ закрыт. Пользователь должен запустить его снова, чтобы установить общий доступ к содержимому. В зависимости от сценария вы можете счесть некоторые ошибки недостаточно серьезными для завершения общего доступа. В этом случае вы можете не вызывать **ReportError** и продолжать работу с общим доступом.

```cs
shareOperation.ReportError("Could not reach the server! Try again later."); 
```

Наконец, когда ваша программа обработала общее содержимое, вам нужно вызвать метод [**ReportCompleted**](https://msdn.microsoft.com/library/windows/apps/Windows.ApplicationModel.DataTransfer.ShareTarget.ShareOperation.ReportCompleted), чтобы сообщить об этом системе.

```cs
shareOperation.ReportCompleted();
```

Если вы используете эти методы, в обыкновенных ситуациях применяйте их в указанном выше порядке и не вызывайте их больше одного раза. Бывают случаи, когда приложение — получатель данных может вызывать [**ReportDataRetrieved**](https://msdn.microsoft.com/library/windows/apps/Windows.ApplicationModel.DataTransfer.ShareTarget.ShareOperation.ReportDataRetrieved) до [**ReportStarted**](https://msdn.microsoft.com/library/windows/apps/Windows.ApplicationModel.DataTransfer.ShareTarget.ShareOperation.ReportStarted). Например, приложение может получать данные в рамках задачи обработчика события активации, но не вызывать **ReportStarted**, пока пользователь не щелкнет **Общий доступ**.

## <a name="return-a-quicklink-if-sharing-was-successful"></a>Возвращение QuickLink в случае успешного выполнения общего доступа

Если пользователь выбирает ваше приложение для получения содержимого, рекомендуется создать объект [**QuickLink**](https://msdn.microsoft.com/library/windows/apps/Windows.ApplicationModel.DataTransfer.ShareTarget.QuickLink). Объект **QuickLink** является своеобразным ярлыком, который облегчает для пользователей отправку данных вашему приложению. Например, можно создать объект **QuickLink**, открывающий новое сообщение электронной почты, в котором уже указан адрес электронной почты друга.

У объекта **QuickLink** должен быть заголовок, значок и идентификатор. Название (например, "Написать маме") и значок появляются, когда пользователь касается чудо-кнопки «Поделиться». Идентификатор — это элемент, который ваше приложение использует для доступа к любой пользовательской информации, такой как адрес электронной почты или учетные данные для входа. Когда ваше приложение создает объект **QuickLink**, оно возвращает **QuickLink** системе путем вызова [**ReportCompleted**](https://msdn.microsoft.com/library/windows/apps/Windows.ApplicationModel.DataTransfer.ShareTarget.ShareOperation.ReportCompleted).

**QuickLink** не хранит никаких данных. Он содержит только идентификатор, который, будучи выбранным, отправляется вашему приложению. Ваше приложение отвечает за хранение идентификатора **QuickLink** и соответствующих пользовательских данных. Когда пользователь касается элемента **QuickLink**, вы можете получить его идентификатор с помощью свойства [**QuickLinkId**](https://msdn.microsoft.com/library/windows/apps/Windows.ApplicationModel.DataTransfer.ShareTarget.ShareOperation.QuickLinkId).

```cs
async void ReportCompleted(ShareOperation shareOperation, string quickLinkId, string quickLinkTitle)
{
    QuickLink quickLinkInfo = new QuickLink
    {
        Id = quickLinkId,
        Title = quickLinkTitle,

        // For quicklinks, the supported FileTypes and DataFormats are set 
        // independently from the manifest
        SupportedFileTypes = { "*" },
        SupportedDataFormats = { StandardDataFormats.Text, StandardDataFormats.Uri, 
                StandardDataFormats.Bitmap, StandardDataFormats.StorageItems }
    };

    StorageFile iconFile = await Windows.ApplicationModel.Package.Current.InstalledLocation.CreateFileAsync(
            "assets\\user.png", CreationCollisionOption.OpenIfExists);
    quickLinkInfo.Thumbnail = RandomAccessStreamReference.CreateFromFile(iconFile);
    shareOperation.ReportCompleted(quickLinkInfo);
}
```

## <a name="see-also"></a>См. также 

* [Связь между приложениями](index.md)
* [Предоставление общего доступа к данным](share-data.md)
* [OnShareTargetActivated](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.application.onsharetargetactivated.aspx)
* [ReportStarted](https://msdn.microsoft.com/library/windows/apps/windows.applicationmodel.datatransfer.sharetarget.shareoperation.reportstarted.aspx)
* [ReportError](https://msdn.microsoft.com/library/windows/apps/windows.applicationmodel.datatransfer.sharetarget.shareoperation.reporterror.aspx)
* [ReportCompleted](https://msdn.microsoft.com/library/windows/apps/windows.applicationmodel.datatransfer.sharetarget.shareoperation.reportcompleted.aspx)
* [ReportDataRetrieved](https://msdn.microsoft.com/library/windows/apps/windows.applicationmodel.datatransfer.sharetarget.shareoperation.reportdataretrieved.aspx)
* [ReportStarted](https://msdn.microsoft.com/library/windows/apps/windows.applicationmodel.datatransfer.sharetarget.shareoperation.reportstarted.aspx)
* [QuickLink](https://msdn.microsoft.com/library/windows/apps/windows.applicationmodel.datatransfer.sharetarget.quicklink.aspx)
* [QuickLInkId](https://msdn.microsoft.com/library/windows/apps/windows.applicationmodel.datatransfer.sharetarget.quicklink.id.aspx)

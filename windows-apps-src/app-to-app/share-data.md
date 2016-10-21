---
description: "В этой статье объясняется, как обеспечить поддержку контракта отправки данных в приложении универсальной платформы Windows (UWP)."
title: "Предоставление общего доступа к данным"
ms.assetid: 32287F5E-EB86-4B98-97FF-8F6228D06782
author: awkoren
translationtype: Human Translation
ms.sourcegitcommit: 554a2cd1db0f950b8a04a5d562f6a6ba43f1be23
ms.openlocfilehash: 8b4f9ae45ed549ba5f10062e6bad25a4fb2e2a6f

---

# Предоставление общего доступа к данным

\[ Обновлено для приложений UWP в Windows10. Статьи о Windows8.x см. в [архиве](http://go.microsoft.com/fwlink/p/?linkid=619132) \]

В этой статье объясняется, как обеспечить поддержку контракта отправки данных в приложении универсальной платформы Windows (UWP). Контракт отправки данных— это простой способ, которым приложения могут быстро предоставлять друг другу общий доступ к данным, например к тексту, ссылкам, фотографиям и видео. Например, пользователю может быть необходимо поделиться со своими друзьями ссылкой на веб-страницу в приложении социальной сети или сохранить ее в приложении для заметок, чтобы вернуться к ней позже.

## Настройка обработчика событий

Добавьте обработчик событий [**DataRequested**](https://msdn.microsoft.com/library/windows/apps/Windows.ApplicationModel.DataTransfer.DataTransferManager.DataRequested), который будет срабатывать при каждом вызове функции общего доступа пользователем. Это может происходить, когда пользователь касается элемента управления в приложении (например, кнопки или команды на панели приложения), или автоматически в определенном сценарии (если, например, пользователь заканчивает уровень и получает рекордный результат).

[!code-cs[Основной блок](./code/share_data/cs/MainPage.xaml.cs#SnippetPrepareToShare)]

При возникновении события [**DataRequested**](https://msdn.microsoft.com/library/windows/apps/Windows.ApplicationModel.DataTransfer.DataTransferManager.DataRequested) приложение получает объект [**DataRequest**](https://msdn.microsoft.com/library/windows/apps/Windows.ApplicationModel.DataTransfer.DataRequest). Этот объект содержит объект [**DataPackage**](https://msdn.microsoft.com/library/windows/apps/Windows.ApplicationModel.DataTransfer.DataPackage), который можно использовать, чтобы предоставить содержимое, которым пользователь хочет поделиться. Вы должны указать название и данные, к которым необходимо предоставить общий доступ. Описание необязательно, но рекомендуется.

[!code-cs[Основной блок](./code/share_data/cs/MainPage.xaml.cs#SnippetCreateRequest)]

## Выбор данных

Можно делиться разными типами данных, включая:

-   Обычный текст
-   Универсальные коды ресурсов (URI)
-   HTML
-   Форматированный текст
-   Растровые изображения
-   Обычный текст
-   Файлы
-   Пользовательские данные, определенные разработчиком

Объект [**DataPackage**](https://msdn.microsoft.com/library/windows/apps/Windows.ApplicationModel.DataTransfer.DataPackage) может содержать один или несколько указанных форматов в любом сочетании. В примере ниже показано, как предоставить общий доступ к тексту.

[!code-cs[Основной блок](./code/share_data/cs/MainPage.xaml.cs#SnippetSetContent)]

## Задание свойств

При подготовке данных пакета к совместному использованию можно установить ряд свойств, дающих дополнительную информацию о содержимом, предназначенном для общего доступа. Благодаря этим свойствам конечное приложение может улучшить взаимодействие с пользователем. Например, предоставление описания может быть полезным, если пользователь предоставляет общий доступ более чем одному приложению. Добавление эскиза при совместном использовании рисунка или ссылки на веб-страницу устанавливает визуальную связь с пользователем. Подробнее об этом см. в разделе [**DataPackagePropertySet**](https://msdn.microsoft.com/library/windows/apps/Windows.ApplicationModel.DataTransfer.DataPackagePropertySet).

Все свойства, кроме заголовка, не являются обязательными. Свойство заголовка является обязательным и должно быть задано.

[!code-cs[Основной блок](./code/share_data/cs/MainPage.xaml.cs#SnippetSetProperties)]

## Запуск пользовательского интерфейса общего доступа

Пользовательский интерфейс для общего доступа предоставляется системой. Чтобы запустить его, вызовите метод [**ShowShareUI**](https://msdn.microsoft.com/library/windows/apps/Windows.ApplicationModel.DataTransfer.DataTransferManager.ShowShareUI).

[!code-cs[Основной блок](./code/share_data/cs/MainPage.xaml.cs#SnippetShowUI)]

## Обработка ошибок

В большинстве случаев предоставление общего доступа к содержимому—линейный процесс. Тем не менее всегда существует вероятность непредвиденных событий. Например, приложению может потребоваться, чтобы пользователь выбрал содержимое для общего доступа, но при этом пользователь ничего не выбирает. Для обработки таких ситуаций используйте метод [**FailWithDisplayText**](https://msdn.microsoft.com/library/windows/apps/Windows.ApplicationModel.DataTransfer.DataRequest.FailWithDisplayText(System.String)), который отобразит соответствующее сообщение для пользователя, если что-то пойдет не так.

## Задержка общего доступа с помощью делегатов

Готовить данные для общего доступа сразу не всегда целесообразно. Например, если ваше приложение поддерживает отправку большого файла изображения в нескольких различных графических форматах, то создавать все эти изображения до того, как пользователь сделает свой выбор, малоэффективно.

Чтобы решить эту проблему, [**DataPackage**](https://msdn.microsoft.com/library/windows/apps/Windows.ApplicationModel.DataTransfer.DataPackage) может содержать делегат–функцию, которая вызывается, когда принимающее приложение запрашивает данные. Рекомендуется использовать делегат, когда пользователь хочет предоставить общий доступ к ресурсоемким данным.

<!-- For some reason, this snippet was inline in the WDCML topic. Suggest moving to VS project with rest of snippets. -->
```cs
async void OnDeferredImageRequestedHandler(DataProviderRequest request)
{
    // Provide updated bitmap data using delayed rendering
    if (this.imageStream != null)
    {
        DataProviderDeferral deferral = request.GetDeferral();
        InMemoryRandomAccessStream inMemoryStream = new InMemoryRandomAccessStream();

        // Decode the image.
        BitmapDecoder imageDecoder = await BitmapDecoder.CreateAsync(this.imageStream);

        // Re-encode the image at 50% width and height.
        BitmapEncoder imageEncoder = await BitmapEncoder.CreateForTranscodingAsync(inMemoryStream, imageDecoder);
        imageEncoder.BitmapTransform.ScaledWidth = (uint)(imageDecoder.OrientedPixelHeight * 0.5);
        imageEncoder.BitmapTransform.ScaledHeight = (uint)(imageDecoder.OrientedPixelHeight * 0.5);
        await imageEncoder.FlushAsync();

        request.SetData(RandomAccessStreamReference.CreateFromStream(inMemoryStream));
        deferral.Complete();
    }
}
```

## См. также 

* [Связь между приложениями](index.md)
* [Получение данных](receive-data.md)
* [DataPackage](https://msdn.microsoft.com/library/windows/apps/windows.applicationmodel.datatransfer.datapackage.aspx)
* [DataPackagePropertySet](https://msdn.microsoft.com/library/windows/apps/windows.applicationmodel.datatransfer.datapackagepropertyset.aspx)
* [DataRequest](https://msdn.microsoft.com/library/windows/apps/windows.applicationmodel.datatransfer.datarequest.aspx)
* [DataRequested](https://msdn.microsoft.com/library/windows/apps/windows.applicationmodel.datatransfer.datatransfermanager.datarequested.aspx)
* [FailWithDisplayText](https://msdn.microsoft.com/library/windows/apps/windows.applicationmodel.datatransfer.datarequest.failwithdisplaytext.aspx)
* [ShowShareUi](https://msdn.microsoft.com/library/windows/apps/windows.applicationmodel.datatransfer.datatransfermanager.showshareui.aspx)
 




<!--HONumber=Aug16_HO3-->



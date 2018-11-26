---
ms.assetid: cb7380d0-bc14-4936-aa1c-206304b3dc70
description: Узнайте, как обрабатывать ошибки, созданные классом AdControl в библиотеках Microsoft Advertising.
title: Обработка ошибок рекламы
ms.date: 05/11/2018
ms.topic: article
keywords: Windows 10, UWP, рекламные объявления, реклама, обработка ошибок, javascript, XAML, c#
ms.localizationpriority: medium
ms.openlocfilehash: a6c14ecf8e8909ab6cd95a54ca8144fbf8a8d912
ms.sourcegitcommit: 681c70f964210ab49ac5d06357ae96505bb78741
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/26/2018
ms.locfileid: "7712488"
---
# <a name="handle-ad-errors"></a>Обработка ошибок рекламы

Классы [AdControl](https://docs.microsoft.com/uwp/api/microsoft.advertising.winrt.ui.adcontrol), [InterstitialAd](https://docs.microsoft.com/uwp/api/microsoft.advertising.winrt.ui.interstitialad) и [NativeAdsManagerV2](https://docs.microsoft.com/uwp/api/microsoft.advertising.winrt.ui.nativeadsmanagerv2) имеют событие **ErrorOccurred**, которое возникает при возникновении связанной с рекламой ошибки. Код приложения может обрабатывать это событие и изучать свойства [ErrorCode](https://docs.microsoft.com/uwp/api/microsoft.advertising.winrt.ui.aderroreventargs.errorcode) и [ErrorMessage](https://docs.microsoft.com/uwp/api/microsoft.advertising.winrt.ui.aderroreventargs.errormessage) объекта аргументов события для определения причины ошибки.

<span id="bkmk-dotnet"/>

## <a name="xaml-apps"></a>Приложения XAML

Чтобы обработать связанные с рекламой ошибки в приложении XAML, выполните следующие действия:

1. Назначьте событие **ErrorOccurred** вашего объекта **AdControl**, **InterstitialAd** или **NativeAdsManagerV2** имени делегата обработчика событий.

2. Создайте код для делегата обработки события ошибки так, чтобы он принимал два параметра: **Object** для отправителя и объект [AdErrorEventArgs](https://docs.microsoft.com/uwp/api/microsoft.advertising.winrt.ui.aderroreventargs).

Здесь приводится пример, в котором делегат с именем **OnAdError** назначается событию **ErrorOccurred** объекта **AdControl** под названием *myBannerAdControl*.

> [!div class="tabbedCodeSnippets"]
``` csharp
myBannerAdControl.ErrorOccurred = OnAdError;
```

Это пример определения делегата **OnAdError**, который записывает сведения об ошибке в окно вывода в Visual Studio.

> [!div class="tabbedCodeSnippets"]
``` csharp
private void OnAdError(object sender, AdErrorEventArgs e)
{
    System.Diagnostics.Debug.WriteLine("AdControl error (" + ((AdControl)sender).Name + "): " + e.Error +
        " ErrorCode: " + e.ErrorCode.ToString());
}
```

См. пошаговые инструкции по обработке ошибок **AdControl** в XAML и C# в разделе [Пошаговое руководство по обработке ошибок в XAML/C#](error-handling-in-xamlc-walkthrough.md).

<span id="bkmk-javascript"/>

## <a name="javascripthtml-apps"></a>Приложения JavaScript/HTML

Чтобы обработать ошибки **ErrorOccur** в приложении JavaScript, выполните следующие действия:

1.  Назначьте событие **onErrorOccurred** обработчику событий.

2.  Создайте код для разработчика событий.

Здесь приводится пример, в котором обработчик событий с именем **errorLogger** назначается событию **ErrorOccurred** объекта **AdControl**.

> [!div class="tabbedCodeSnippets"]
``` html
<div id="myAd" style="position: absolute; top: 53px; left: 0px; width: 250px; height: 250px; z-index: 1"
     data-win-control="MicrosoftNSJS.Advertising.AdControl"
     data-win-options="{applicationId: '3f83fe91-d6be-434d-a0ae-7351c5a997f1', adUnitId: 'test', onErrorOccurred: errorLogger}">
</div>
```

Функция обработки ошибок является декларативной и должна быть заключена в функцию [markSupportedForProcessing](http://msdn.microsoft.com/library/windows/apps/Hh967819.aspx).

Обработчик ошибок перехватывает объект ошибки JavaScript, когда происходит ошибка. Объект ошибки предоставляет два аргумента обработчику ошибок. Дополнительные сведения см. в разделе [Специальные свойства ошибок из асинхронных методов среды выполнения Windows](http://msdn.microsoft.com/library/windows/apps/hh994690.aspx).

Ниже приводится пример функции обработки ошибок с именем **errorLogger**, которая обрабатывает событие **onErrorOccurred**.

> [!div class="tabbedCodeSnippets"]
``` javascript
WinJS.Utilities.markSupportedForProcessing(
window.errorLogger = function (sender, evt) {
    console.log(new Date()).toLocaleTimeString() + ": " + sender.element.id + " error: " + evt.errorMessage +
    " error code: " + evt.errorCode + \n");
});
```

См. пошаговое руководство по обработке ошибки **AdControl** в JavaScript в разделе [Пошаговое руководство по обработке ошибок в JavaScript](error-handling-in-javascript-walkthrough.md).

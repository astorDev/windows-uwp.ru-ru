---
author: mcleanbyron
ms.assetid: cb7380d0-bc14-4936-aa1c-206304b3dc70
description: "Изучите процесс обработки ошибок, формируемых классом AdControl, в библиотеках Microsoft Advertising."
title: "Обработка ошибок в библиотеках Microsoft Advertising"
ms.sourcegitcommit: cf695b5c20378f7bbadafb5b98cdd3327bcb0be6
ms.openlocfilehash: 5e0c7e6328247e5f686b14b10c80d8aafc13e0e4

---

# Обработка ошибок в библиотеках Microsoft Advertising


\[ Обновлено для приложений UWP в Windows10. Статьи о Windows 8.x см. в [архиве](http://go.microsoft.com/fwlink/p/?linkid=619132) \]

В этом разделе содержатся основные сведения об обработке ошибок, созданных классом [AdControl](https://msdn.microsoft.com/library/windows/apps/microsoft.advertising.winrt.ui.adcontrol.aspx) в библиотеках Microsoft Advertising.

<span id="bkmk-javascript"/>
## Приложения JavaScript/HTML

Чтобы обработать ошибки **AdControl** в приложении JavaScript, выполните следующие действия:

1.  Назначьте событие **onErrorOccurred** обработчику событий.

2.  Создайте код для разработчика событий.

Разработчик событий **onErrorOccurred** настраивается в атрибуте **data-win-options** параметра **div** элемента управления **AdControl**. В следующем примере настраивается обработка события **onErrorOccurred** функцией с именем **errorLogger**.

``` syntax
<div id="myAd" style="position: absolute; top: 53px; left: 0px; width: 250px; height: 250px; z-index: 1"
     data-win-control="MicrosoftNSJS.Advertising.AdControl"
     data-win-options="{applicationId: 'd25517cb-12d4-4699-8bdc-52040c712cab', adUnitId: 'ADPT33', onErrorOccurred: errorLogger}">
</div>
```

Функция обработки ошибок является декларативной и должна быть заключена в функцию [markSupportedForProcessing](http://msdn.microsoft.com/library/windows/apps/Hh967819.aspx).

Обработчик ошибок перехватывает объект ошибки JavaScript, когда происходит ошибка **AdControl**. Объект ошибки предоставляет два аргумента обработчику ошибок. Дополнительные сведения см. в разделе [Специальные свойства ошибок из асинхронных методов среды выполнения Windows](http://msdn.microsoft.com/library/windows/apps/hh994690.aspx).

Ниже приводится пример функции обработки ошибок с именем **errorLogger**, которая обрабатывает событие **onErrorOccurred**.

``` syntax
WinJS.Utilities.markSupportedForProcessing(
window.errorLogger = function (sender, evt) {
    console.log(new Date()).toLocaleTimeString() + ": " + sender.element.id + " error: " + evt.errorMessage + " error code: " + evt.errorCode + \n");
});
```

См. пошаговое руководство по обработке ошибки **AdControl** в JavaScript в разделе [Пошаговое руководство по обработке ошибок в JavaScript](error-handling-in-javascript-walkthrough.md).

<span id="bkmk-dotnet"/>
## Приложения XAML

Чтобы обработать ошибки **AdControl** в приложении XAML, выполните следующие действия:

* Назначьте событие **ErrorOccurred** имени делегата обработчика событий.

* Создайте код для делегата обработки события ошибки так, чтобы он принимал два параметра: **Object** для отправителя и объект **AdErrorEventArgs**.

Здесь приводится пример, в котором делегат с именем **OnAdError** назначается событию **ErrorOccurred**.

``` syntax
this.ErrorOccurred = OnAdError;
```

Это пример определения делегата **OnAdError**, который записывает сведения об ошибке в окно вывода в Visual Studio.

``` syntax
private void OnAdError(object sender, AdErrorEventArgs e)
{
    System.Diagnostics.Debug.WriteLine("AdControl error (" + ((AdControl)sender).Name + "): " + e.Error + " ErrorCode: " + e.ErrorCode.ToString());
}
```

См. пошаговые инструкции по обработке ошибок **AdControl** в XAML и C# в разделе [Пошаговое руководство по обработке ошибок в XAML/C#](error-handling-in-xamlc-walkthrough.md).

 

 



<!--HONumber=Jun16_HO4-->



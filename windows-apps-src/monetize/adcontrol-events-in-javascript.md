---
author: mcleanbyron
ms.assetid: 2383296e-c3d7-4b49-bcd2-621391228fdb
description: "Узнайте, как обрабатывать события класса AdControl."
title: "События AdControl в JavaScript"
translationtype: Human Translation
ms.sourcegitcommit: cf695b5c20378f7bbadafb5b98cdd3327bcb0be6
ms.openlocfilehash: 5a47915b0dd2792ed50cc5d556b1181ee2c259e1


---

# События AdControl в JavaScript


\[ Обновлено для приложений UWP в Windows 10. Статьи для Windows 8.x см. в [архиве](http://go.microsoft.com/fwlink/p/?linkid=619132) \]

В следующих примерах показано, как обрабатывать события класса [AdControl](https://msdn.microsoft.com/library/windows/apps/microsoft.advertising.winrt.ui.adcontrol.aspx). В этих примерах предполагается, что ранее вы назначили обработчики для событий **AdControl**. Подробнее об этом см. в [Примере свойств HTML](html-properties-example.md).

В JavaScript события **AdControl** должны быть расположены внутри функции [MarkSupportedForProcessing](http://msdn.microsoft.com/en-us/library/windows/apps/Hh967819.aspx). Дополнительные сведения об обработке событий в JavaScript см. в разделе [Создание простых приложений (HTML)](https://msdn.microsoft.com/en-us/library/windows/apps/hh780660.aspx#adding-event-handlers).

## Примеры

``` syntax
WinJS.Utilities.markSupportedForProcessing(
window.myAdError = function (sender, msg) {
  // place code here for when there is an error serving an ad.
  // e.g. you may opt to show a default experience, or reclaim the div for other purposes.
});

WinJS.Utilities.markSupportedForProcessing(
window.myAdRefreshed = function (sender) {
  // place code here that you wish to execute when the ad refreshes.
});

WinJS.Utilities.markSupportedForProcessing(
window.myAdEngagedChanged = function (sender) {
  if (true == sender.isEngaged) {
    // code here for when user engaged with ad, e.g. if a game, pause it.
  }
  else {
    // user no longer engaged with ad, include code to unpause.
  }
});
```

## Ссылки по теме

* [Примеры рекламы на GitHub](http://aka.ms/githubads)
* [Обработка ошибок AdControl](adcontrol-error-handling.md)
* [Класс RoutedEventArgs](http://msdn.microsoft.com/en-us/library/system.windows.routedeventargs.aspx)

 

 



<!--HONumber=Jun16_HO4-->



---
author: mcleanbyron
ms.assetid: 2fba38c4-11be-4058-bfa3-5f979390791c
description: "Узнайте, как обрабатывать события класса AdControl."
title: "События AdControl в C#"
translationtype: Human Translation
ms.sourcegitcommit: cf695b5c20378f7bbadafb5b98cdd3327bcb0be6
ms.openlocfilehash: cc5dfbfacefb4be32153f3266878be41a3b923f8

---

# События AdControl в C\# #  


\[ Обновлено для приложений UWP в Windows 10. Статьи для Windows 8.x см. в [архиве](http://go.microsoft.com/fwlink/p/?linkid=619132) \]

В следующих примерах показано, как обрабатывать события класса [AdControl](https://msdn.microsoft.com/library/windows/apps/microsoft.advertising.winrt.ui.adcontrol.aspx). В этих примерах предполагается, что ранее вы назначили обработчики для событий **AdControl** в XAML. Подробнее об этом см. в [Примере свойств XAML](xaml-properties-example.md).

Дополнительные сведения об обработке событий на C# см. в разделе [Обзор событий и перенаправленных событий (универсальные приложения для Windows на C#, VB, C++ и XAML)](http://msdn.microsoft.com/library/windows/apps/hh758286).

## Примеры


``` syntax
private void OnAdError(object sender, AdErrorEventArgs e) {
  // place code here for when there is an error serving an ad
  // e.g. you may opt to show a default experience, or reclaim the div for other purposes
  return;
}

private void OnAdRefresh(object sender, RoutedEventArgs e) {
  // place code here that you wish to execute when the ad refreshes.
 return;
}

private void OnAdEngagedChanged(object sender, RoutedEventArgs e) {
  // place code here for when there is an error serving an ad
  // e.g. you may opt to show a default experience, or reclaim the div for other purposes
  return;
}
```

## Ссылки по теме

* [Примеры рекламы на GitHub](http://aka.ms/githubads)
* [Обработка ошибок AdControl](adcontrol-error-handling.md)
* [Класс RoutedEventArgs](http://msdn.microsoft.com/en-us/library/system.windows.routedeventargs.aspx)

 

 



<!--HONumber=Jun16_HO4-->



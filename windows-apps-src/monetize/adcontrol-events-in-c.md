---
author: mcleanbyron
ms.assetid: 2fba38c4-11be-4058-bfa3-5f979390791c
description: "Узнайте, как обрабатывать события класса AdControl."
title: "События AdControl в C#"
translationtype: Human Translation
ms.sourcegitcommit: 3de603aec1dd4d4e716acbbb3daa52a306dfa403
ms.openlocfilehash: f92cbbb00a064ce7569d44ad952838df4d21ac8c

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

## Связанные разделы

* [Примеры рекламы на GitHub](http://aka.ms/githubads)
* [Обработка ошибок AdControl](adcontrol-error-handling.md)
* [Класс RoutedEventArgs](http://msdn.microsoft.com/library/system.windows.routedeventargs.aspx)

 

 



<!--HONumber=Jul16_HO2-->



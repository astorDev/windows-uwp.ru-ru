---
author: mcleanbyron
ms.assetid: 2fba38c4-11be-4058-bfa3-5f979390791c
description: "Узнайте, как обрабатывать события класса AdControl."
title: "События AdControl в C#"
ms.author: mcleans
ms.date: 02/08/2017
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: "windows 10, uwp, рекламы, реклама, AdControl, события"
translationtype: Human Translation
ms.sourcegitcommit: c6b64cff1bbebc8ba69bc6e03d34b69f85e798fc
ms.openlocfilehash: cf285ebb4207b9a9a215bfb4a739b0bc6a2d934b
ms.lasthandoff: 02/07/2017

---

# <a name="adcontrol-events-in-c"></a>События AdControl в C\# #  


В следующих примерах показаны основные обработчики событий для следующих событий [AdControl](https://msdn.microsoft.com/library/windows/apps/microsoft.advertising.winrt.ui.adcontrol.aspx): [ErrorOccurred](https://msdn.microsoft.com/library/windows/apps/xaml/microsoft.advertising.winrt.ui.adcontrol.erroroccurred.aspx), [AdRefreshed](https://msdn.microsoft.com/library/windows/apps/xaml/microsoft.advertising.winrt.ui.adcontrol.adrefreshed.aspx)и [IsEngagedChanged](https://msdn.microsoft.com/library/windows/apps/xaml/microsoft.advertising.winrt.ui.adcontrol.isengagedchanged.aspx). В этих примерах предполагается, что вы уже назначили обработчики для этих событий в коде XAML. Подробнее об этом см. в [Примере свойств XAML](xaml-properties-example.md).

Дополнительные сведения об обработке событий на C# см. в разделе [Обзор событий и перенаправленных событий (универсальные приложения для Windows на C#, VB, C++ и XAML)](http://msdn.microsoft.com/library/windows/apps/hh758286).

## <a name="examples"></a>Примеры

> [!div class="tabbedCodeSnippets"]
[!code-cs[AdControl](./code/AdvertisingSamples/AdControlSamples/cs/MainPage.xaml.cs#EventHandlers)]

## <a name="related-topics"></a>Связанные статьи

* [Примеры рекламы на GitHub](http://aka.ms/githubads)
* [Обработка ошибок AdControl](adcontrol-error-handling.md)
* [Класс RoutedEventArgs](http://msdn.microsoft.com/library/system.windows.routedeventargs.aspx)

 

 


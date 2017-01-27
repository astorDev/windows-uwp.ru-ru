---
author: mcleanbyron
ms.assetid: 2383296e-c3d7-4b49-bcd2-621391228fdb
description: "Узнайте, как обрабатывать события класса AdControl."
title: "События AdControl в JavaScript"
translationtype: Human Translation
ms.sourcegitcommit: f88a71491e185aec84a86248c44e1200a65ff179
ms.openlocfilehash: e652fe6b5f295c0f4b4808e5a4605c13fdcfea68

---

# <a name="adcontrol-events-in-javascript"></a>События AdControl в JavaScript

В следующих примерах показаны основные обработчики событий для следующих событий [AdControl](https://msdn.microsoft.com/library/windows/apps/microsoft.advertising.winrt.ui.adcontrol.aspx): [ErrorOccurred](https://msdn.microsoft.com/library/windows/apps/xaml/microsoft.advertising.winrt.ui.adcontrol.erroroccurred.aspx), [AdRefreshed](https://msdn.microsoft.com/library/windows/apps/xaml/microsoft.advertising.winrt.ui.adcontrol.adrefreshed.aspx)и [IsEngagedChanged](https://msdn.microsoft.com/library/windows/apps/xaml/microsoft.advertising.winrt.ui.adcontrol.isengagedchanged.aspx). В этих примерах предполагается, что вы уже назначили обработчики для этих событий в разметке HTML. Подробнее об этом см. в [Примере свойств HTML](html-properties-example.md).

В JavaScript события **AdControl** должны быть расположены внутри функции [MarkSupportedForProcessing](http://msdn.microsoft.com/library/windows/apps/Hh967819.aspx). Дополнительные сведения об обработке событий в JavaScript см. в разделе [Создание простых приложений (HTML)](https://msdn.microsoft.com/library/windows/apps/hh780660.aspx#adding-event-handlers).

## <a name="examples"></a>Примеры

> [!div class="tabbedCodeSnippets"]
[!code-javascript[AdControl](./code/AdvertisingSamples/AdControlSamples/js/main.js#EventHandlers)]

## <a name="related-topics"></a>Связанные статьи

* [Примеры рекламы на GitHub](http://aka.ms/githubads)
* [Обработка ошибок AdControl](adcontrol-error-handling.md)
* [Класс RoutedEventArgs](http://msdn.microsoft.com/library/system.windows.routedeventargs.aspx)

 

 



<!--HONumber=Dec16_HO2-->



---
author: mcleanbyron
ms.assetid: 5fa16a27-fdc0-43b2-84cd-8547fd4915de
description: "Узнайте, как назначать свойства **AdControl** в HTML."
title: "Пример свойств HTML AdControl"
ms.author: mcleans
ms.date: 02/08/2017
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: "windows 10, uwp, рекламные объявления, реклама, AdControl, HTML, свойства"
translationtype: Human Translation
ms.sourcegitcommit: c6b64cff1bbebc8ba69bc6e03d34b69f85e798fc
ms.openlocfilehash: a879944dc14ca161c3e52a49ffe4a8cebfa7d69f
ms.lasthandoff: 02/07/2017

---

# <a name="adcontrol-html-properties-example"></a>Пример свойств HTML AdControl

В следующем примере показано, как назначать свойства [AdControl](https://msdn.microsoft.com/library/windows/apps/microsoft.advertising.winrt.ui.adcontrol.aspx) в HTML. **applicationId** и **adUnitId** являются обязательными свойствами. Другие свойства и события являются дополнительными. Если вы не зададите значения для дополнительных свойств, элемент управления будет использовать значения по умолчанию, обеспечивающие согласованность рекламного объявления с процессом взаимодействия пользователя с приложением.

Последние пять строк представляют собой пример регистрации функций для событий **AdControl**. Вы можете зарегистрировать только те функции, которые были определены в коде JavaScript.

Эти значения являются примерами. В своем коде вы зададите значения для этих функций и свойств таким образом, чтобы они подходили для вашего приложения.

> [!div class="tabbedCodeSnippets"]
``` html
<div id="myAd" style="position: absolute; top: 50px; left: 0px; width: 300px; height: 250px; z-index: 1"
    data-win-control="MicrosoftNSJS.Advertising.AdControl"
    data-win-options="{applicationId: '3f83fe91-d6be-434d-a0ae-7351c5a997f1',
                       adUnitId: '10865270',
                       isAutoRefreshEnabled: false,
                       onAdRefreshed: myAdRefreshed,
                       onErrorOccurred: myAdError,
                       onEngagedChanged: myAdEngagedChanged,
                       onPointerDown: myPointerDown,
                       onPointerUp: myPointerUp }" />
```

## <a name="related-topics"></a>Статьи по теме

* [AdControl в HTML 5 и JavaScript](adcontrol-in-html-5-and-javascript.md)
* [Примеры рекламы на GitHub](http://aka.ms/githubads)

 


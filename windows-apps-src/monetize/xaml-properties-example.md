---
author: mcleanbyron
ms.assetid: d074e9d5-b3e0-4f16-b1e4-02b32ac99b2c
description: "Узнайте, как присваивать значения свойствам **AdControl**."
title: "Пример свойств XAML AdControl"
ms.author: mcleans
ms.date: 02/08/2017
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: "windows 10, uwp, рекламные объявления, реклама, AdControl, XAML, свойства"
translationtype: Human Translation
ms.sourcegitcommit: c6b64cff1bbebc8ba69bc6e03d34b69f85e798fc
ms.openlocfilehash: dbcc08b1373c7f73d5b9ebf541ec82bb01fd9df3
ms.lasthandoff: 02/07/2017

---

# <a name="adcontrol-xaml-properties-example"></a>Пример свойств XAML AdControl

В следующем примере XAML показано, как присвоить значения свойствам [AdControl](https://msdn.microsoft.com/library/windows/apps/microsoft.advertising.winrt.ui.adcontrol.aspx). Если значение свойства не задано, **AdControl** использует значения по умолчанию, обеспечивающие согласованность рекламного объявления с процессом взаимодействия пользователя с приложением.

Эти значения являются примерами. В своем коде вы зададите значения для этих функций и свойств, подходящий для вашего приложения.

> [!div class="tabbedCodeSnippets"]
``` xml
<UI:AdControl Width="300",
    Height="250",
    AdUnitId="10865270",
    ApplicationId="3f83fe91-d6be-434d-a0ae-7351c5a997f1",
    IsAutoRefreshEnabled="false",
    AdRefreshed="OnAdRefresh",
    ErrorOcurred="OnAdError",
    IsEngagedChanged="OnAdEngagedChanged" />
```

## <a name="related-topics"></a>Связанные статьи

* [AdControl в XAML и .NET](adcontrol-in-xaml-and--net.md)
* [Примеры рекламы на GitHub](http://aka.ms/githubads)

 


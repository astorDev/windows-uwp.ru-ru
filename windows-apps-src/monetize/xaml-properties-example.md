---
author: mcleanbyron
ms.assetid: d074e9d5-b3e0-4f16-b1e4-02b32ac99b2c
description: "Сведения об установке значений свойств **AdControl**."
title: "Пример свойств XAML AdControl"
translationtype: Human Translation
ms.sourcegitcommit: f88a71491e185aec84a86248c44e1200a65ff179
ms.openlocfilehash: f33e4048a1a9aa68ecc627d81ca15858027720c1

---

# <a name="adcontrol-xaml-properties-example"></a>Пример свойств XAML AdControl

В следующем примере XAML показано, как установить значения свойств [AdControl](https://msdn.microsoft.com/library/windows/apps/microsoft.advertising.winrt.ui.adcontrol.aspx). Если значение свойства не задано, **AdControl** использует значения по умолчанию, обеспечивающие согласованность рекламного объявления с процессом взаимодействия пользователя с приложением.

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

 



<!--HONumber=Dec16_HO2-->



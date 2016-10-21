---
author: mcleanbyron
ms.assetid: d074e9d5-b3e0-4f16-b1e4-02b32ac99b2c
description: "Сведения об установке значений свойств **AdControl**."
title: "Пример свойств XAML"
translationtype: Human Translation
ms.sourcegitcommit: 5bf07d3001e92ed16931be516fe059ad33c08bb9
ms.openlocfilehash: fb0533aa0ea760bca686276f886f0afcb21bf6f7


---

# Пример свойств XAML




В следующем примере XAML показано, как установить значения свойств [AdControl](https://msdn.microsoft.com/library/windows/apps/microsoft.advertising.winrt.ui.adcontrol.aspx). Если значение свойства не задано, **AdControl** использует значения по умолчанию, обеспечивающие согласованность рекламного объявления с процессом взаимодействия пользователя с приложением.

Эти значения являются примерами. В своем коде вы зададите значения для этих функций и свойств, подходящий для вашего приложения.

``` syntax
Width="300",
Height="250",
AdUnitId="10865270",
ApplicationId="3f83fe91-d6be-434d-a0ae-7351c5a997f1",
IsAutoRefreshEnabled="false",
AdRefreshed="OnAdRefresh",
ErrorOcurred="OnAdError",
IsEngagedChanged="OnAdEngagedChanged"
```

## Ссылки по теме

* [Примеры рекламы на GitHub](http://aka.ms/githubads)

 



<!--HONumber=Aug16_HO3-->



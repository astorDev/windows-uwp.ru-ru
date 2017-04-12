---
author: mcleanbyron
ms.assetid: 7a16b0ca-6b8e-4ade-9853-85690e06bda6
description: "Узнайте, как запускать промежуточную рекламу с помощью C#."
title: "Пример кода промежуточной рекламы в C#"
ms.author: mcleans
ms.date: 02/08/2017
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: "windows 10, uwp, реклама, рекламные объявления, промежуточная, c#, пример кода"
ms.openlocfilehash: 2c0020ff7d750a97380c351358ec6aafb996125f
ms.sourcegitcommit: 909d859a0f11981a8d1beac0da35f779786a6889
translationtype: HT
---
# <a name="interstitial-ad-sample-code-in-c"></a>Пример кода промежуточной рекламы в C\# #  

В этом разделе представлен полный пример кода для простого приложения универсальной платформы Windows (UWP) на C# и XAML, в котором отображается межстраничная видеореклама. Пошаговые инструкции по настройке проекта для использования этого кода см. в разделе [Межстраничные объявления](interstitial-ads.md). Полный пример с проектом см. в разделе [рекламы примеров на GitHub](http://aka.ms/githubads).

## <a name="code-example"></a>Пример кода

В этом разделе представлено содержимое файлов MainPage.xaml и MainPage.xaml.cs простого приложения, в котором отображается промежуточная реклама. Чтобы использовать эти примеры, скопируйте код в проект **Пустое приложение (универсальные приложения для Windows)**, созданный на Visual C# в Visual Studio 2015.

В этом примере приложения используются две кнопки для запроса и последующего запуска промежуточной рекламы. Замените значения полей ```myAppId``` и ```myAdUnitId``` фактическими значениями из Центра разработки для Windows перед отправкой своего приложения в Магазин. Дополнительные сведения см. в разделе [Настройка групп объявлений в приложении](set-up-ad-units-in-your-app.md).

>**Примечание.**&nbsp;&nbsp;Чтобы изменить этот пример для отображения межстраничного баннера вместо межстраничной видеорекламы, передайте значение **AdType.Display** в качестве первого параметра для метода [RequestAd](https://msdn.microsoft.com/library/windows/apps/microsoft.advertising.winrt.ui.interstitialad.requestad.aspx), а не значение **AdType.Video**. Дополнительные сведения см. в разделе [Межстраничные объявления](interstitial-ads.md).

### <a name="mainpagexaml"></a>MainPage.xaml

> [!div class="tabbedCodeSnippets"]
[!code-xml[InterstitialAd](./code/AdvertisingSamples/InterstitialAdSamples/cs/MainPage.xaml#L1-L13)]

### <a name="mainpagexamlcs"></a>MainPage.xaml.cs

> [!div class="tabbedCodeSnippets"]
[!code-cs[InterstitialAd](./code/AdvertisingSamples/InterstitialAdSamples/cs/MainPage.xaml.cs#CompleteSample)]

 
## <a name="related-topics"></a>Статьи по теме

* [Примеры рекламы на GitHub](http://aka.ms/githubads)
 

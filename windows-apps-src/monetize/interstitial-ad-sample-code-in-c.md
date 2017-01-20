---
author: mcleanbyron
ms.assetid: 7a16b0ca-6b8e-4ade-9853-85690e06bda6
description: "Узнайте, как запускать промежуточную рекламу с помощью C#."
title: "Пример кода промежуточной рекламы в C#"
translationtype: Human Translation
ms.sourcegitcommit: 2b5dbf872dd7aad48373f6a6df3dffbcbaee8090
ms.openlocfilehash: c7554b94e67ce7f4b83a9ad4360819881d09f0fb

---

# <a name="interstitial-ad-sample-code-in-c"></a>Пример кода промежуточной рекламы в C\# #  

В этом разделе представлен полный пример кода для простого приложения универсальной платформы Windows (UWP) на C# и XAML, в котором отображается промежуточная реклама. Пошаговые инструкции по настройке проекта для использования этого кода см. в разделе [Промежуточная реклама](interstitial-ads.md). Полный пример с проектом см. в разделе [рекламы примеров на GitHub](http://aka.ms/githubads).

## <a name="code-example"></a>Пример кода

В этом разделе представлено содержимое файлов MainPage.xaml и MainPage.xaml.cs простого приложения, в котором отображается промежуточная реклама. Чтобы использовать эти примеры, скопируйте код в проект **Пустое приложение (универсальные приложения для Windows)**, созданный на Visual C# в Visual Studio 2015.

В этом примере приложения используются две кнопки для запроса и последующего запуска промежуточной рекламы. Замените значения полей ```myAppId``` и ```myAdUnitId``` фактическими значениями из Центра разработки для Windows перед отправкой своего приложения в Магазин. Дополнительные сведения см. в разделе [Настройка блоков рекламы в приложении](set-up-ad-units-in-your-app.md).

### <a name="mainpagexaml"></a>MainPage.xaml

> [!div class="tabbedCodeSnippets"]
[!code-xml[InterstitialAd](./code/AdvertisingSamples/InterstitialAdSamples/cs/MainPage.xaml#L1-L13)]

### <a name="mainpagexamlcs"></a>MainPage.xaml.cs

> [!div class="tabbedCodeSnippets"]
[!code-cs[InterstitialAd](./code/AdvertisingSamples/InterstitialAdSamples/cs/MainPage.xaml.cs#CompleteSample)]

 
## <a name="related-topics"></a>Статьи по теме

* [Примеры рекламы на GitHub](http://aka.ms/githubads)
 



<!--HONumber=Dec16_HO2-->



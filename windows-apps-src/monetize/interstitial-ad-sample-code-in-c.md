---
ms.assetid: 7a16b0ca-6b8e-4ade-9853-85690e06bda6
description: Узнайте, как запускать промежуточную рекламу с помощью C#.
title: Пример кода промежуточной рекламы в C
ms.date: 02/18/2020
ms.topic: article
keywords: windows 10, uwp, реклама, рекламные объявления, промежуточная, c#, пример кода
ms.localizationpriority: medium
ms.openlocfilehash: 4179d0dde5577093a2ae30c539adf0a8c544d56b
ms.sourcegitcommit: 6af7ce0e3c27f8e52922118deea1b7aad0ae026e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/19/2020
ms.locfileid: "77463366"
---
# <a name="interstitial-ad-sample-code-in-c"></a>Пример кода внутреннего AD на языке C\# #  

>[!WARNING]
> Начиная с 1 июня 2020 г. платформа Microsoft AD монетизацию для приложений Windows UWP будет выключена. [Подробнее](https://aka.ms/ad-monetization-shutdown)

В этом разделе представлен полный пример кода для простого приложения универсальной платформы Windows (UWP) на C# и XAML, в котором отображается межстраничная видеореклама. Пошаговые инструкции по настройке проекта для использования этого кода см. в разделе [Промежуточная реклама](interstitial-ads.md). Полный пример с проектом см. в разделе [рекламы примеров на GitHub](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/Advertising).

## <a name="code-example"></a>Пример кода

В этом разделе представлено содержимое файлов MainPage.xaml и MainPage.xaml.cs простого приложения, в котором отображается промежуточная реклама. Чтобы использовать эти примеры, скопируйте код в проект **Пустое приложение (универсальные приложения для Windows)** , созданный на Visual C# в Visual Studio.

В этом примере приложения используются две кнопки для запроса и последующего запуска промежуточной рекламы. Перед отправкой приложения в магазин замените значения полей ```myAppId``` и ```myAdUnitId``` на значения Live из центра партнеров. Дополнительные сведения см. в разделе [Настройка блоков рекламы в приложении](set-up-ad-units-in-your-app.md#live-ad-units).

> [!NOTE]
> Чтобы изменить этот пример для отображения межстраничного баннера вместо межстраничной видеорекламы, передайте значение **AdType.Display** в качестве первого параметра для метода [RequestAd](https://docs.microsoft.com/uwp/api/microsoft.advertising.winrt.ui.interstitialad.requestad), а не значение **AdType.Video**. Дополнительные сведения см. в разделе [Промежуточная видеореклама](interstitial-ads.md).

### <a name="mainpagexaml"></a>MainPage.xaml

> [!div class="tabbedCodeSnippets"]
[!code-xml[InterstitialAd](./code/AdvertisingSamples/InterstitialAdSamples/cs/MainPage.xaml#L1-L13)]

### <a name="mainpagexamlcs"></a>MainPage.xaml.cs

> [!div class="tabbedCodeSnippets"]
[!code-csharp[InterstitialAd](./code/AdvertisingSamples/InterstitialAdSamples/cs/MainPage.xaml.cs#CompleteSample)]

 
## <a name="related-topics"></a>Связанные разделы

* [Примеры рекламы на GitHub](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/Advertising)
 

---
ms.assetid: 7a16b0ca-6b8e-4ade-9853-85690e06bda6
description: Узнайте, как запускать промежуточную рекламу с помощью C#.
title: Пример кода межстраничного объявления на C#
ms.date: 03/22/2018
ms.topic: article
keywords: windows 10, uwp, реклама, рекламные объявления, промежуточная, c#, пример кода
ms.localizationpriority: medium
ms.openlocfilehash: 075d98d49ba7e878abc7e800af84984bdb93e3a2
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57640989"
---
# <a name="interstitial-ad-sample-code-in-c"></a>Вставки ad пример кода на языке C\# #  

В этом разделе представлен полный пример кода для простого приложения универсальной платформы Windows (UWP) на C# и XAML, в котором отображается межстраничная видеореклама. Пошаговые инструкции по настройке проекта для использования этого кода см. в разделе [Промежуточная реклама](interstitial-ads.md). Полный пример с проектом см. в разделе [рекламы примеров на GitHub](https://aka.ms/githubads).

## <a name="code-example"></a>Пример кода

В этом разделе представлено содержимое файлов MainPage.xaml и MainPage.xaml.cs простого приложения, в котором отображается промежуточная реклама. Чтобы использовать эти примеры, скопируйте код в проект **Пустое приложение (универсальные приложения для Windows)**, созданный на Visual C# в Visual Studio.

В этом примере приложения используются две кнопки для запроса и последующего запуска промежуточной рекламы. Замените значения ```myAppId``` и ```myAdUnitId``` поля значениями динамической из центра партнеров перед отправкой приложения Store. Дополнительные сведения см. в разделе [Настройка блоков рекламы в приложении](set-up-ad-units-in-your-app.md#live-ad-units).

> [!NOTE]
> Чтобы изменить этот пример для отображения межстраничного баннера вместо межстраничной видеорекламы, передайте значение **AdType.Display** в качестве первого параметра для метода [RequestAd](https://docs.microsoft.com/uwp/api/microsoft.advertising.winrt.ui.interstitialad.requestad), а не значение **AdType.Video**. Дополнительные сведения см. в разделе [Промежуточная видеореклама](interstitial-ads.md).

### <a name="mainpagexaml"></a>MainPage.xaml

> [!div class="tabbedCodeSnippets"]
[!code-xml[InterstitialAd](./code/AdvertisingSamples/InterstitialAdSamples/cs/MainPage.xaml#L1-L13)]

### <a name="mainpagexamlcs"></a>MainPage.xaml.cs

> [!div class="tabbedCodeSnippets"]
[!code-cs[InterstitialAd](./code/AdvertisingSamples/InterstitialAdSamples/cs/MainPage.xaml.cs#CompleteSample)]

 
## <a name="related-topics"></a>Статьи по теме

* [Примеры рекламы на GitHub](https://aka.ms/githubads)
 

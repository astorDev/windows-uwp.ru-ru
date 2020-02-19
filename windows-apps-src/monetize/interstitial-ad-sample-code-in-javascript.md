---
ms.assetid: 646977ed-1705-4ea7-a3db-a6b9aac70703
description: Узнайте, как запускать промежуточную рекламу с помощью JavaScript И HTML.
title: Пример кода промежуточной рекламы в JavaScript
ms.date: 02/18/2020
ms.topic: article
keywords: windows 10, uwp, реклама, рекламные объявления, промежуточная, javascript, пример кода
ms.localizationpriority: medium
ms.openlocfilehash: 2f08e3425b8364bf8c6beb8a8d136534aea4a9b2
ms.sourcegitcommit: 6af7ce0e3c27f8e52922118deea1b7aad0ae026e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/19/2020
ms.locfileid: "77463516"
---
# <a name="interstitial-ad-sample-code-in-javascript"></a>Пример кода промежуточной рекламы в JavaScript

>[!WARNING]
> Начиная с 1 июня 2020 г. платформа Microsoft AD монетизацию для приложений Windows UWP будет выключена. [Подробнее](https://aka.ms/ad-monetization-shutdown)

В этом разделе представлен полный пример кода для простого приложения универсальной платформы Windows (UWP) на JavaScript и HTML, в котором отображается промежуточная реклама. Пошаговые инструкции по настройке проекта для использования этого кода см. в разделе [Промежуточная реклама](interstitial-ads.md). Полный пример с проектом см. в разделе [рекламы примеров на GitHub](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/Advertising).

## <a name="code-example"></a>Пример кода

В этом разделе представлено содержимое файлов HTML и JavaScript простого приложения, в котором отображается промежуточная реклама. Чтобы использовать эти примеры, скопируйте этот код в проект **Приложение WinJS (универсальные приложения для Windows)** , созданный на JavaScript в Visual Studio.

В этом примере приложения используются две кнопки для запроса и последующего запуска промежуточной рекламы. Файлы main.js и index.html, созданные Visual Studio, были изменены и показаны ниже. Представленный ниже файл script.js содержит большую часть кода в примере. Этот файл необходимо добавить в папку **js** вашего проекта.

Перед отправкой приложения в магазин замените значения переменных ```applicationId``` и ```adUnitId``` на динамические значения из центра партнеров. Дополнительные сведения см. в разделе [Настройка блоков рекламы в приложении](set-up-ad-units-in-your-app.md#live-ad-units).

> [!NOTE]
> Чтобы изменить этот пример для отображения межстраничного баннера вместо межстраничной видеорекламы, передайте значение **InterstitialAdType.display** в качестве первого параметра для метода [RequestAd](https://docs.microsoft.com/uwp/api/microsoft.advertising.winrt.ui.interstitialad.requestad) вместо значения **InterstitialAdType.video**. Дополнительные сведения см. в разделе [Промежуточная видеореклама](interstitial-ads.md).

### <a name="indexhtml"></a>index.html

> [!div class="tabbedCodeSnippets"]
[!code-html[InterstitialAd](./code/AdvertisingSamples/InterstitialAdSamples/js/index.html#L1-L21)]

### <a name="scriptjs"></a>script.js

> [!div class="tabbedCodeSnippets"]
[!code-javascript[InterstitialAd](./code/AdvertisingSamples/InterstitialAdSamples/js/script.js#script)]

### <a name="mainjs"></a>main.js

> [!div class="tabbedCodeSnippets"]
[!code-javascript[InterstitialAd](./code/AdvertisingSamples/InterstitialAdSamples/js/main.js#main)]

## <a name="related-topics"></a>Связанные разделы

* [Примеры рекламы на GitHub](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/Advertising)

 

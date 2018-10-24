---
author: Xansky
ms.assetid: 646977ed-1705-4ea7-a3db-a6b9aac70703
description: Узнайте, как запускать промежуточную рекламу с помощью JavaScript И HTML.
title: Пример кода промежуточной рекламы в JavaScript
ms.author: mhopkins
ms.date: 03/22/2018
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: windows 10, uwp, реклама, рекламные объявления, промежуточная, javascript, пример кода
ms.localizationpriority: medium
ms.openlocfilehash: 894053298428818c2f3304220f14afb6c44ba2af
ms.sourcegitcommit: 4b97117d3aff38db89d560502a3c372f12bb6ed5
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/23/2018
ms.locfileid: "5435315"
---
# <a name="interstitial-ad-sample-code-in-javascript"></a>Пример кода промежуточной рекламы в JavaScript

В этом разделе представлен полный пример кода для простого приложения универсальной платформы Windows (UWP) на JavaScript и HTML, в котором отображается промежуточная реклама. Пошаговые инструкции по настройке проекта для использования этого кода см. в разделе [Промежуточная реклама](interstitial-ads.md). Полный пример с проектом см. в разделе [рекламы примеров на GitHub](http://aka.ms/githubads).

## <a name="code-example"></a>Пример кода

В этом разделе представлено содержимое файлов HTML и JavaScript простого приложения, в котором отображается промежуточная реклама. Чтобы использовать эти примеры, скопируйте этот код в проект **Приложение WinJS (универсальные приложения для Windows)**, созданный на JavaScript в Visual Studio.

В этом примере приложения используются две кнопки для запроса и последующего запуска промежуточной рекламы. Файлы main.js и index.html, созданные Visual Studio, были изменены и показаны ниже. Представленный ниже файл script.js содержит большую часть кода в примере. Этот файл необходимо добавить в папку **js** вашего проекта.

Замените значения переменных ```applicationId``` и ```adUnitId``` фактическими значениями из Центра разработки для Windows перед отправкой своего приложения в Microsoft Store. Дополнительные сведения см. в разделе [Настройка блоков рекламы в приложении](set-up-ad-units-in-your-app.md#live-ad-units).

> [!NOTE]
> Чтобы изменить этот пример для отображения межстраничного баннера вместо межстраничной видеорекламы, передайте значение **InterstitialAdType.display** в качестве первого параметра для метода [RequestAd](https://docs.microsoft.com/uwp/api/microsoft.advertising.winrt.ui.interstitialad.requestad) вместо значения **InterstitialAdType.video**. Дополнительные сведения см. в разделе [Межстраничные объявления](interstitial-ads.md).

### <a name="indexhtml"></a>index.html

> [!div class="tabbedCodeSnippets"]
[!code-html[InterstitialAd](./code/AdvertisingSamples/InterstitialAdSamples/js/index.html#L1-L21)]

### <a name="scriptjs"></a>script.js

> [!div class="tabbedCodeSnippets"]
[!code-javascript[InterstitialAd](./code/AdvertisingSamples/InterstitialAdSamples/js/script.js#script)]

### <a name="mainjs"></a>main.js

> [!div class="tabbedCodeSnippets"]
[!code-javascript[InterstitialAd](./code/AdvertisingSamples/InterstitialAdSamples/js/main.js#main)]

## <a name="related-topics"></a>Статьи по теме

* [Примеры рекламы на GitHub](http://aka.ms/githubads)

 

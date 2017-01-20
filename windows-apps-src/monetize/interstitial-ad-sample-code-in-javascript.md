---
author: mcleanbyron
ms.assetid: 646977ed-1705-4ea7-a3db-a6b9aac70703
description: "Узнайте, как запускать промежуточную рекламу с помощью JavaScript И HTML."
title: "Пример кода промежуточной рекламы в JavaScript"
translationtype: Human Translation
ms.sourcegitcommit: 2b5dbf872dd7aad48373f6a6df3dffbcbaee8090
ms.openlocfilehash: 5d7f81e16f3ecdc73fba5010cfbc6082a19cd24c


---

# <a name="interstitial-ad-sample-code-in-javascript"></a>Пример кода промежуточной рекламы в JavaScript

В этом разделе представлен полный пример кода для простого приложения универсальной платформы Windows (UWP) на JavaScript и HTML, в котором отображается промежуточная реклама. Пошаговые инструкции по настройке проекта для использования этого кода см. в разделе [Промежуточная реклама](interstitial-ads.md). Полный пример с проектом см. в разделе [рекламы примеров на GitHub](http://aka.ms/githubads).

## <a name="code-example"></a>Пример кода

В этом разделе представлено содержимое файлов HTML и JavaScript простого приложения, в котором отображается промежуточная реклама. Чтобы использовать эти примеры, скопируйте этот код в проект **Приложение WinJS (универсальные приложения для Windows)**, созданный на JavaScript в Visual Studio 2015.

В этом примере приложения используются две кнопки для запроса и последующего запуска промежуточной рекламы. Файлы main.js и index.html, созданные Visual Studio, были изменены и показаны ниже. Представленный ниже файл script.js содержит большую часть кода в примере. Этот файл необходимо добавить в папку **js** вашего проекта.

>**Примечание для Windows 8.x и Windows Phone 8.1.**&nbsp;&nbsp;Если ваш проект ориентирован на Windows 8.1 или Windows Phone 8.1, именем HTML-файла по умолчанию в вашем проекте будет default.html вместо index.html, а именем JavaScript-файла по умолчанию в вашем проекте будет default.js вместо main.js.

Замените значения переменных ```applicationId``` и ```adUnitId``` фактическими значениями из Центра разработки для Windows перед отправкой своего приложения в Магазин. Дополнительные сведения см. в разделе [Настройка рекламных блоков в приложении](set-up-ad-units-in-your-app.md).

### <a name="indexhtml"></a>index.html

> [!div class="tabbedCodeSnippets"]
[!code-html[InterstitialAd](./code/AdvertisingSamples/InterstitialAdSamples/js/index.html#L1-L21)]

<span/>
>**Примечание для Windows 8.x и Windows Phone 8.1**&nbsp;&nbsp;Если ваш проект ориентирован на Windows 8.1 или Windows Phone 8.1, замените строку ```<script src="//Microsoft.Advertising.JavaScript/ad.js"></script>``` в примере строкой ```<script src="/MSAdvertisingJS/ads/ad.js"></script>```.

### <a name="scriptjs"></a>script.js

> [!div class="tabbedCodeSnippets"]
[!code-javascript[InterstitialAd](./code/AdvertisingSamples/InterstitialAdSamples/js/script.js#script)]

### <a name="mainjs"></a>main.js

> [!div class="tabbedCodeSnippets"]
[!code-javascript[InterstitialAd](./code/AdvertisingSamples/InterstitialAdSamples/js/main.js#main)]

## <a name="related-topics"></a>Статьи по теме

* [Примеры рекламы на GitHub](http://aka.ms/githubads)

 



<!--HONumber=Dec16_HO2-->



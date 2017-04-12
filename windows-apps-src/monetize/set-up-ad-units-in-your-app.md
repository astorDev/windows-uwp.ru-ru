---
author: mcleanbyron
ms.assetid: bb105fbe-bbbd-4d78-899b-345af2757720
description: "Сведения о добавлении в приложение значений идентификатора приложения и идентификатора рекламного блока из информационной панели Центра разработки для Windows перед отправкой приложения в Магазин."
title: "Настройка рекламных блоков в приложении"
ms.author: mcleans
ms.date: 02/08/2017
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: "Windows 10, uwp, рекламные объявления, реклама, рекламные блоки"
ms.openlocfilehash: daf0887462a4c84aa827a6261793a0eaf4d512ca
ms.sourcegitcommit: 909d859a0f11981a8d1beac0da35f779786a6889
translationtype: HT
---
# <a name="set-up-ad-units-in-your-app"></a>Настройка рекламных блоков в приложении




При использовании [AdControl](https://msdn.microsoft.com/library/windows/apps/microsoft.advertising.winrt.ui.adcontrol.aspx) или [InterstitialAd](https://msdn.microsoft.com/library/windows/apps/microsoft.advertising.winrt.ui.interstitialad.aspx) для отображения рекламных объявлений в приложении вы должны указать идентификатор приложения и идентификатор рекламного блока. Во время разработки приложения используйте соответствующие [тестовые значения идентификатора приложения и идентификатора рекламного блока](test-mode-values.md), чтобы увидеть, как отображается реклама в вашем приложении во время тестирования.

После завершения тестирования приложения и перед отправкой его в Центр разработки для Windows необходимо обновить код приложения для использования значений ИД приложения и ИД рекламного блока из информационной панели [Центра разработки для Windows](https://msdn.microsoft.com/library/windows/apps/mt170658.aspx). При использовании тестовых значений в опубликованном приложении оно не будет получать рекламу.

Настройка идентификатора приложения и рекламных блоков для рабочего приложения:

1.  Выберите ваше приложение на информационной панели Центра разработки для Windows, после чего щелкните **Получение дохода > Получение дохода с помощью рекламы**.

2.  На этой странице в разделе **Рекламные блоки Майкрософт** создайте рекламный блок. Чтобы выбрать тип рекламного блока, выберите один из следующих параметров:

  * Если для показа рекламного блока в вашем приложении используется элемент **AdControl**, выберите тип **Баннер**.

  * Если для отображения межстраничной видеорекламы или межстраничных баннеров используется элемент **InterstitialAd**, выберите **Вставка видео** или **Вставка баннера** (укажите параметр, соответствующий требуемому типу межстраничного объявления).

3.  На этой странице вы увидите **ИД приложения** и **ИД рекламного блока** для каждого из созданных рекламных блоков. Для отображения рекламы в приложении вам потребуется использовать эти значения в коде приложения следующим образом:

  * Если ваше приложение отображает баннеры, установите эти значения для свойств [ApplicationId](https://msdn.microsoft.com/library/windows/apps/microsoft.advertising.winrt.ui.adcontrol.applicationid.aspx) и [AdUnitId](https://msdn.microsoft.com/library/windows/apps/microsoft.advertising.winrt.ui.adcontrol.adunitid.aspx) вашего объекта **AdControl**. Дополнительные сведения см. в разделах [AdControl в XAML и .NET](adcontrol-in-xaml-and--net.md) и [AdControl в HTML5 и JavaScript](adcontrol-in-html-5-and-javascript.md).

  * Если в вашем приложении содержится межстраничное объявление, установите эти значения для метода [RequestAd](https://msdn.microsoft.com/library/windows/apps/microsoft.advertising.winrt.ui.interstitialad.requestad.aspx) объекта **InterstitialAd**. Дополнительные сведения см. в разделе [Межстраничные объявления](interstitial-ads.md).

Дополнительные сведения о странице **Монетизировать с помощью рекламы** см. в разделе [Монетизация с использованием рекламы](../publish/monetize-with-ads.md).

## <a name="related-topics"></a>Связанные разделы

* [Значения тестового режима](test-mode-values.md)
* [AdControl в XAML и .NET](adcontrol-in-xaml-and--net.md)
* [AdControl в HTML 5 и JavaScript](adcontrol-in-html-5-and-javascript.md)
* [Межстраничные объявления](interstitial-ads.md)


 

 

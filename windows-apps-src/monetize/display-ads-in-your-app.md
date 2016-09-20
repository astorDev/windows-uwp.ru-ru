---
author: mcleanbyron
ms.assetid: 63A9EDCF-A418-476C-8677-D8770B45D1D7
description: "Пакет SDK Microsoft Store Engagement and Monetization предоставляет несколько способов получать доход от приложения с помощью рекламы."
title: "Показ рекламы в приложении"
ms.sourcegitcommit: 8a5b02dbc40f3f0cd9be32aa7d5184e60a3b2707
ms.openlocfilehash: c79ba96908cc7b52afefbe44c3f56ce009c87f16

---

# Показ рекламы в приложении


\[ Обновлено для приложений UWP в Windows 10. Статьи о Windows8.x см. в [архиве](http://go.microsoft.com/fwlink/p/?linkid=619132) \]

Пакет [SDK Microsoft Store Engagement and Monetization](monetize-your-app-with-the-microsoft-store-engagement-and-monetization-sdk.md) предоставляет несколько способов получения дохода от приложений с помощью рекламы.

## Отображение баннеров и промежуточной видеорекламы с использованием библиотек Microsoft Advertising

Зарабатывайте больше с помощью своих приложений для Windows, включая в них баннеры промежуточную видеорекламу. Реклама отображается в приложениях для Windows для компьютеров, планшетов и телефонов. Вы можете отслеживать результативность рекламы в режиме реального времени с помощью панели мониторинга Центра разработки для Windows.

Чтобы включить рекламу в приложения, воспользуйтесь элементами управления **AdControl** и **InterstitialAd** из библиотек рекламы, которые предоставляются в рамках пакета SDK Microsoft Store Engagement and Monetization. Эти элементы управления можно использовать для отображения баннеров и промежуточной видеорекламы из Microsoft в приложениях XAML или JavaScript/HTML для Windows 10, Windows 8.1, Windows Phone 8.1 и Windows Phone 8.

Дополнительные сведения см. в разделе [Отображение рекламы с использованием библиотек Microsoft Advertising](display-ads-using-the-microsoft-advertising-libraries.md). После публикации приложения с рекламой можно отслеживать эффективность рекламы с помощью [отчета об эффективности рекламы](../publish/advertising-performance-report.md).                                           

## Использование рекламного посредника для показа рекламных баннеров из нескольких рекламных сетей

В приложениях XAML можно использовать класс **AdMediatorControl**, чтобы оптимизировать доход от рекламы, отображая рекламные баннеры из нескольких рекламных сетей. После добавления этого элемента управления в приложение на панели Центра разработки для Windows можно настроить параметры рекламного посредника, а мы обработаем запросы на передачу рекламных баннеров из выбранных вами рекламных сетей. Дополнительные сведения см. в разделе [Использование рекламного посредника для повышения прибыли от рекламы](use-ad-mediation-to-maximize-revenue.md).

## Различия между библиотеками Microsoft Advertising и рекламным посредником

Пакет SDK Microsoft Store Engagement and Monetization включает библиотеки Microsoft Advertising и рекламного посредника. Однако эти библиотеки предоставляют разные классы и служат разным целям.

* Используйте классы **AdControl** и **InterstitialAd** из библиотек Microsoft Advertising, если требуется показать баннерную или промежуточную видеорекламу в приложении XAML или JavaScript.
* Используйте класс **AdMediatorControl** из библиотек рекламного посредника, если в приложении XAML требуется показать рекламные баннеры из различных рекламных сетей.

Дополнительные сведения см. в разделе [Отличия класса AdMediatorControl от класса AdControl](what-is-the-difference-admediatorcontrol-or-adcontrol.md).

## Связанные разделы

* [Пакет SDK Microsoft Store Engagement and Monetization](monetize-your-app-with-the-microsoft-store-engagement-and-monetization-sdk.md)
* [Монетизация приложений с помощью рекламы]( http://go.microsoft.com/fwlink/p/?LinkId=699559)



<!--HONumber=Jun16_HO4-->



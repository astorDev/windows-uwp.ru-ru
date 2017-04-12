---
author: mcleanbyron
ms.assetid: 63A9EDCF-A418-476C-8677-D8770B45D1D7
description: "Microsoft Store Services SDK предоставляет несколько способов получать доход от приложения с помощью рекламы."
title: "Показ рекламы в приложении"
ms.author: mcleans
ms.date: 02/08/2017
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: "windows 10, uwp, рекламные объявления, реклама, баннер, промежуточная реклама"
ms.openlocfilehash: ceae17d34ba400876f912ba9932d76f59d773e63
ms.sourcegitcommit: d053f28b127e39bf2aee616aa52bb5612194dc53
translationtype: HT
---
# <a name="display-ads-in-your-app"></a>Показ рекламы в приложении


Универсальная платформа Windows (UWP) и Магазин Windows предоставляют несколько способов получения дохода от приложений с помощью рекламы.

## <a name="display-banner-and-interstitial-ads-using-the-microsoft-advertising-libraries"></a>Отображение баннеров и межстраничных объявлений с использованием библиотек Microsoft Advertising

Получайте доход от своих приложений путем добавления баннеров или межстраничных объявлений.

* *Рекламные баннеры*— небольшие рекламные материалы, занимающие часть страницы в приложении (обычно сверху или снизу).
* *Межстраничные объявления*— рекламные материалы во весь экран, которые обычно вынуждают пользователя просмотреть видео или требуют нажать на них, чтобы продолжить использовать приложение или игру. Мы поддерживаем два типа межстраничных объявлений для приложений UWP: видеоролики и баннеры.

Чтобы включить эти типы рекламы в приложения, используйте элементы управления **AdControl** и **InterstitialAd** в библиотеках рекламы, распространяемых в составе пакетов [Microsoft Store Services SDK](http://aka.ms/store-em-sdk) (для приложений UWP) и [Microsoft Advertising SDK для Windows и Windows Phone 8.x](http://aka.ms/store-8-sdk) (для приложений Windows 8.1 и Windows Phone 8.x).

Вы можете отслеживать эффективность рекламы в режиме реального времени с помощью [отчета о результативности рекламы](../publish/advertising-performance-report.md) на информационной панели Центра разработки для Windows.

Следующие разделы содержат сведения об общих задачах, выполняемых с помощью библиотек Windows Advertising.

|  Задача    | Раздел |               
|----------|-------|
| Установка библиотек Microsoft Advertising и начало работы.     | См. раздел [Начало работы с библиотеками Microsoft Advertising](get-started-with-microsoft-advertising-libraries.md).        |
| Отображение рекламных баннеров в приложении XAML/C#.     | См. раздел [AdControl в XAML и .NET](adcontrol-in-xaml-and--net.md).        |
| Отображение рекламных баннеров в приложении HTML/JavaScript.     | См. раздел [AdControl в HTML 5 и Javascript](adcontrol-in-html-5-and-javascript.md).        |
| Показ рекламных баннеров в приложении Windows Phone Silverlight 8.x.     | См. раздел [AdControl в Windows Phone Silverlight](adcontrol-in-windows-phone-silverlight.md).        |
| Показ межстраничных объявлений в приложении.     | См. раздел [Межстраничные объявления](interstitial-ads.md).       |
| Добавление рекламы в видеосодержимое в приложениях универсальной платформы Windows (UWP), написанных при помощи JavaScript с HTML.   |  См. раздел [Добавление рекламы в видеосодержимое в HTML 5 и JavaScript](add-advertisements-to-video-content.md).  |
| Скачайте примеры проектов, демонстрирующие, как добавлять баннеры и промежуточную видеорекламу в приложения.     |См. [примеры рекламы на GitHub](http://aka.ms/githubads).       |
| Обработка ошибок [AdControl](https://msdn.microsoft.com/library/windows/apps/microsoft.advertising.winrt.ui.adcontrol.aspx) в приложении.     | См. раздел [Обработка ошибок](error-handling-with-advertising-libraries.md) и пошаговые руководства в разделе [Обработка ошибок AdControl](adcontrol-error-handling.md).       |
| Сообщение об ошибке в библиотеках Microsoft Advertising.     | Посетите [Страницу поддержки](https://go.microsoft.com/fwlink/p/?LinkId=331508).        |
| Получение поддержки сообщества.     | Посетите [форум](http://go.microsoft.com/fwlink/p/?LinkId=401266).       |

                            

## <a name="use-ad-mediation-for-banner-ads-windows-81-and-windows-phone-8x"></a>Использование рекламного посредника для рекламных баннеров (Windows 8.1 и Windows Phone 8.x)

В приложениях Windows 8.1 и Windows Phone 8.x вы можете использовать класс **AdMediatorControl**, чтобы оптимизировать доход от рекламы, отображая рекламные баннеры из нескольких рекламных сетей. После добавления этого элемента управления в приложение на панели Центра разработки для Windows можно настроить параметры рекламного посредника, а мы обработаем запросы на передачу рекламных баннеров из выбранных вами рекламных сетей. Дополнительные сведения см. в разделе [Использование рекламного посредника для повышения дохода от рекламы](https://msdn.microsoft.com/library/windows/apps/xaml/dn864359.aspx).

> [!NOTE]
> В настоящее время для приложений UWP для Windows10 не поддерживается рекламный посредник, использующий класс **AdMediatorControl**. Посредники на стороне сервера для приложений UWP станут доступны в ближайшее время на основе тех же API-интерфейсов, что используются для баннеров (**AdControl**) и межстраничных объявлений (**InterstitialAd**). Руководство по переходу с **AdMediatorControl** на **AdControl** в приложении UWP см. в разделе [Переход с AdMediatorControl на AdControl для приложений UWP](migrate-from-admediatorcontrol-to-adcontrol.md).

<span id="silverlight_support"/>
## <a name="advertising-support-for-windows-phone-8x-silverlight-projects"></a>Поддержка рекламы для проектов Windows Phone 8.x Silverlight

Некоторые сценарии разработчиков больше не поддерживаются в проектах Windows Phone 8.x Silverlight. Дополнительные сведения см. в следующей таблице.

|  Версия платформы  |  Существующие проекты    |   Новые проекты  |
|-----------------|----------------|--------------|
| Windows Phone 8.0 Silverlight     |  Если у вас есть существующий проект Windows Phone 8.0 Silverlight, в котором уже используются **AdControl** или **AdMediatorControl** из предыдущего выпуска пакета SDK Universal Ad Client или SDK Microsoft Advertising, и это приложение уже опубликовано в Магазине Windows, вы можете изменить и пересобрать проект, а также выполнить отладку или тестирование изменений на устройстве. Отладка или тестирование проекта в эмуляторе не поддерживается.  |  Не поддерживается.  |
| Windows Phone 8.1 Silverlight    |  Если у вас есть существующий проект Windows Phone 8.1 Silverlight, в котором уже используются **AdControl** или **AdMediatorControl** из предыдущего пакета SDK, вы можете изменить и пересобрать его. Но для отладки или тестирования приложения необходимо запустить его в эмуляторе и использовать [значения тестового режима](test-mode-values.md) для идентификаторов приложения и рекламных блоков. Отладка или тестирование приложения на устройстве не поддерживается.  |   Вы можете добавить **AdControl** или **AdMediatorControl** в новый проект Windows Phone 8.1 Silverlight. Но для отладки или тестирования приложения необходимо запустить его в эмуляторе и использовать [значения тестового режима](test-mode-values.md) для идентификаторов приложения и рекламных блоков. Отладка или тестирование приложения на устройстве не поддерживается. |

## <a name="related-topics"></a>Связанные статьи

* [Microsoft Store Services SDK](microsoft-store-services-sdk.md)
* [Монетизация приложений с помощью рекламы](http://go.microsoft.com/fwlink/p/?LinkId=699559)
* [Отчет о результативности рекламы](../publish/advertising-performance-report.md)

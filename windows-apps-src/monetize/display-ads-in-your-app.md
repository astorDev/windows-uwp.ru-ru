---
ms.assetid: 63A9EDCF-A418-476C-8677-D8770B45D1D7
description: Microsoft Advertising SDK предоставляет несколько способов монетизации приложения с помощью рекламы.
title: Показ рекламы в приложении с помощью Microsoft Advertising SDK
ms.date: 06/20/2018
ms.topic: article
keywords: windows 10, uwp, рекламные объявления, баннер, управление рекламой, межстраничная
ms.localizationpriority: medium
ms.openlocfilehash: 0ef3050e2583674bf6cd5a601dbde1500f6b457e
ms.sourcegitcommit: ac7f3422f8d83618f9b6b5615a37f8e5c115b3c4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/29/2019
ms.locfileid: "66372554"
---
# <a name="display-ads-in-your-app-with-the-microsoft-advertising-sdk"></a>Показ рекламы в приложении с помощью Microsoft Advertising SDK

Увеличьте свою прибыль, размещая рекламу в своих приложениях универсальной платформы Windows (UWP) для Windows 10 с помощью Microsoft Advertising SDK. Нашей платформы монетизацию ad предлагает различные форматы рекламы, которые можно легко интегрировать в ваши приложения и поддерживает mediation с многих популярных рекламным сетям. Наша платформа соответствует стандартам OpenRTB, VAST 2.x, MRAID 2 и VPAID 3, а также MOAT и IAS. 

<br/>

<table style="border: none !important;">
<colgroup>
<col width="10%" />
<col width="23%" />
<col width="10%" />
<col width="23%" />
<col width="10%" />
<col width="23%" />
</colgroup>
<tbody>
<tr>
<td align="left"><img src="images/install-sdk.png" alt="Install SDK icon" /></td>
<td align="left"><b>Начало работы</b><br/><br/>
    <a href="https://aka.ms/ads-sdk-uwp">Установите пакет SDK Microsoft Advertising</a>
</td>
<td align="left"><img src="images/write-code.png" alt="Develop icon" /></td>
<td align="left"><b>Руководства для разработчиков</b><br/><br/>
    <a href="banner-ads.md">Рекламные объявления</a>
    <br/>
    <a href="interstitial-ads.md">Вставки рекламы</a>
    <br/>
    <a href="native-ads.md">Собственный рекламы</a>
    </td>
<td align="left"><img src="images/api-reference.png" alt="API ref icon" /></td>
<td align="left"><b>Другие ресурсы</b><br/><br/>
    <a href="set-up-ad-units-in-your-app.md">Настройка единиц измерения ad в приложении</a>
    <br/>
    <a href="best-practices-for-ads-in-apps.md">Советы и рекомендации</a>
    <br/>
    <a href="https://docs.microsoft.com/uwp/api/overview/advertising">Справочник по API</a>
    </td>
</tr>
</tbody>
</table>

## <a name="step-1-install-the-microsoft-advertising-sdk"></a>Шаг 1. Установить пакет Microsoft Advertising SDK

Чтобы начать работу, установите [Microsoft Advertising SDK](https://aka.ms/ads-sdk-uwp) на компьютере разработчика, который вы используете для создания приложения. Инструкции по установке см. в [этой статье](install-the-microsoft-advertising-libraries.md).

## <a name="step-2-implement-ads-in-your-app"></a>Шаг 2. Реализация рекламы в приложении

Microsoft Advertising SDK предоставляет несколько различных типов элементов управления рекламой, которые можно использовать в приложении. Выберите, какие типы рекламы лучше всего подходят для вашей ситуации, и затем добавьте код в свое приложение для отображения таких объявлений. На этом шаге будет использоваться тестовая группа объявлений, чтобы вы могли видеть, как отображается реклама в приложении во время тестирования.

### <a name="banner-ads"></a>Рекламные баннеры

Это статические рекламные изображения, которые размещаются в прямоугольном сегменте страницы в приложении, чтобы отображать рекламное содержимое. Эти объявления можно автоматически обновлять с регулярным интервалом. Это хорошая возможность для начала работы, если вы еще не размещали рекламу в своем приложении.

Инструкции и примеры кода см. в [этой статье](adcontrol-in-xaml-and--net.md).

![addreferences](images/banner-ad.png)

### <a name="interstitial-video-and-interstitial-banner-ads"></a>Межстраничные баннеры и видеореклама

Межстраничные объявления — полноэкранные рекламные материалы, которые обычно вынуждают пользователя просмотреть видео или требуют нажать на них, чтобы продолжить использовать приложение или игру. Мы поддерживаем два типа межстраничных объявлений: видеоролики и баннеры.

Инструкции и примеры кода см. в [этой статье](interstitial-ads.md).

![addreferences](images/interstitial-ad.png)

### <a name="native-ads"></a>Собственные объявления

Это рекламные объявления на основе компонентов. Каждый компонент рекламного объявления (например, заголовок, изображение, описание, текст с призывом к действию) передается вашему приложению в виде отдельного элемента, который можно интегрировать в приложение с использованием собственных шрифтов, цветов и других компонентов пользовательского интерфейса.

Инструкции и примеры кода см. в [этой статье](native-ads.md).

![addreferences](images/native-ad.png)

<span id="ad-mediation"/>

## <a name="step-3-create-an-ad-unit-and-configure-mediation"></a>Шаг 3. Создать единицу ad и настроить посредника

После завершения тестирования приложения, и вы будете готовы отправить ее в Store, создать ad единицу на [рекламы в приложении](../publish/in-app-ads.md) страницы в центре партнеров. Обновите код приложения для использования этой группы объявлений, чтобы ваше приложение получало реальные объявления. Дополнительные сведения см. в разделе [Настройка блоков рекламы в приложении](set-up-ad-units-in-your-app.md#live-ad-units).

По умолчанию приложение будет отображать рекламу из сети платной рекламы корпорации Майкрософт. Чтобы максимально увеличить доход от рекламы, можно использовать [рекламный посредник](ad-mediation-service.md) для группы объявлений в целях показа рекламы из дополнительных платных рекламных сетей, таких как Taboola и Smaato. Также можно расширить возможности продвижения вашего приложения путем показа объявлений из кампаний по продвижению приложений корпорации Майкрософт.

Чтобы начать использовать рекламный посредник в своем приложении UWP, [настройте параметры рекламного посредника](../publish/in-app-ads.md#mediation-settings) для своей группы объявлений. По умолчанию мы автоматически настраиваем параметры посредника с помощью алгоритмов машинного обучения, чтобы помочь вам получить максимальную выручку от рекламы на рынках, поддерживаемых вашим приложением. Однако используемые сети можно выбирать и вручную. В любом случае параметры посредника полностью настроены на наших серверах. Вам не нужно менять код в вашем приложении.    

## <a name="step-4-submit-your-app-and-review-performance"></a>Шаг 4. Отправка приложения и проверка результатов

После завершения разработки приложения с помощью решения ads, вы можете [Отправка обновленного приложения](https://docs.microsoft.com/windows/uwp/publish/app-submissions) в центре партнеров, чтобы сделать его доступным в Store. Приложения, отображающие рекламу, должны отвечать дополнительным требованиям, изложенным в [разделе 10.10 Политики Microsoft Store](https://docs.microsoft.com/legal/windows/agreements/store-policies#1010-advertising-conduct-and-content) и [приложении E к Соглашению с разработчиком приложений](https://docs.microsoft.com/legal/windows/agreements/app-developer-agreement).

После публикации приложения и доступный в Store, вы можете просмотреть ваши [рекламу отчеты о производительности](../publish/advertising-performance-report.md) в центре партнеров и внести изменения настроек посредника для оптимизации производительности рекламных материалах по-прежнему. Доход от рекламы входит в состав вашей [Сводки по выплатам](../publish/payout-summary.md).

<span id="additional-help" />

## <a name="additional-help"></a>Дополнительная справка

Дополнительные сведения по использованию Microsoft Advertising SDK можно найти на следующих ресурсах.

|  Задача    | Resource |               
|----------|-------|
| Сообщить об ошибке или получить техническую поддержку по вопросам рекламы     | Посетите [страницу поддержки](https://developer.microsoft.com/en-us/windows/support) и выберите **Реклама в приложениях**.        |
| Получить поддержку сообщества     | Посетите [форум](https://go.microsoft.com/fwlink/p/?LinkId=401266).       |
| Скачайте примеры проектов, демонстрирующие, как добавлять баннеры и промежуточную видеорекламу в приложения.     | См. [примеры рекламы на GitHub](https://aka.ms/githubads).       |
| Сведения о последних возможностях получения дохода из приложений для Windows     | Посетите раздел [Монетизация приложений](https://developer.microsoft.com/store/monetize).        |

## <a name="windows-81-and-windows-phone-8x-apps"></a>Приложения для Windows 8.1 и Windows Phone 8.x

Для приложений для Windows 8.1 и Windows Phone 8.x предусмотрен [Microsoft Advertising SDK для Windows и Windows Phone 8.x](https://aka.ms/store-8-sdk). Дополнительные сведения об использовании этого SDK для показа рекламы в приложениях для Windows 8.1 и Windows Phone 8.x см. в [этой статье](https://docs.microsoft.com/en-us/previous-versions/windows/apps/dn792120(v=win.10)).

## <a name="related-topics"></a>См. также

* [Microsoft Advertising SDK](https://aka.ms/ads-sdk-uwp)
* [Отчет о результативности рекламы](../publish/advertising-performance-report.md)
* [Программа издателей Ads "премиум" Windows](windows-premium-ads-publishers-program.md)

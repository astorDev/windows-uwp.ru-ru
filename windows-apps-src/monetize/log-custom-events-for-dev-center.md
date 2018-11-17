---
author: Xansky
Description: You can log custom events from your UWP app and review those events in the Usage report in Partner Center.
title: Ведение журнала пользовательских событий для Центра партнеров
ms.author: mhopkins
ms.date: 06/01/2018
ms.topic: article
keywords: windows 10, uwp, Microsoft Store Services SDK, журналы событий
ms.assetid: 4aa591e0-c22a-4c90-b316-0b5d0410af19
ms.localizationpriority: medium
ms.openlocfilehash: 47c1eb02434dc71cb7da949d58ec38cf3b4cf65a
ms.sourcegitcommit: 3257416aebb5a7b1515e107866806f8bd57845a8
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/16/2018
ms.locfileid: "7146533"
---
# <a name="log-custom-events-for-partner-center"></a>Ведение журнала пользовательских событий для Центра партнеров

[Отчет об использовании](https://msdn.microsoft.com/windows/uwp/publish/usage-report) в центре партнеров позволяет получить информацию о событиях, которые вы определили в своем приложении универсальной платформы Windows (UWP). Пользовательское событие — это произвольная строка, представляющая событие или активность в вашем приложении. Например, игра может определять пользовательские события с именем *firstLevelPassed*, *secondLevelPassed* и т. д., которые регистрируются при прохождении пользователем каждого уровня в игре.

Для регистрации пользовательского события из вашего приложения передайте строку пользовательского события методу [Log](https://docs.microsoft.com/uwp/api/microsoft.services.store.engagement.storeservicescustomeventlogger.log), предоставляемому пакетом Microsoft Store Services SDK. Можно просмотреть общее число для вашего пользовательского события в разделе **пользовательские события** в [отчете об использовании](https://msdn.microsoft.com/windows/uwp/publish/usage-report) в центре партнеров.

> [!NOTE]
> Пользовательских событий, войдите в центр партнеров не связаны с [событиями Windows](https://msdn.microsoft.com/library/windows/desktop/aa964766.aspx), и они не отображаются в **Средстве просмотра событий**.

## <a name="prerequisites"></a>Предварительные условия

Прежде чем сможете просматривать зарегистрированные пользовательские события в **отчете об использовании** для вашего приложения в центре партнеров, ваше приложение должно быть опубликовано в магазине.

## <a name="how-to-log-custom-events"></a>Регистрация пользовательских событий

1. Если это еще не сделано, [установите Microsoft Store Services SDK](microsoft-store-services-sdk.md#install-the-sdk) на компьютер разработки.

2. Откройте проект в Visual Studio.

3. В обозревателе решений щелкните правой кнопкой мыши узел **Ссылки** вашего проекта и выберите команду **Добавить ссылку**.

4. В диалоговом окне **Диспетчер ссылок** разверните список **Универсальная платформа Windows** и выберите **Расширения**.

5. В списке пакетов SDK установите флажок рядом с пунктом **Microsoft Engagement Framework** и нажмите кнопку **ОК**.

6. Добавьте следующий оператор в верхнюю часть каждого файла кода, где вы хотите регистрировать пользовательские события.
    [!code-cs[EventLogger](./code/StoreSDKSamples/cs/LogEvents.cs#EngagementNamespace)]

7. В каждом разделе своего кода, где требуется регистрировать пользовательские события, получите объект [StoreServicesCustomEventLogger](https://docs.microsoft.com/uwp/api/microsoft.services.store.engagement.storeservicescustomeventlogger.log), а затем вызовите метод [Log](https://docs.microsoft.com/uwp/api/microsoft.services.store.engagement.storeservicescustomeventlogger.log). Передайте строку пользовательского события в метод.
    [!code-cs[EventLogger](./code/StoreSDKSamples/cs/LogEvents.cs#Log)]

    > [!NOTE]
    > Загрузка [отчета об использовании](https://msdn.microsoft.com/windows/uwp/publish/usage-report) может занять много времени, если ваше приложение регистрирует много пользовательских событий с длинными именами. Мы рекомендуем использовать краткие имена для пользовательских событий. 

## <a name="related-topics"></a>Смежные разделы

* [Отчет об использовании](https://msdn.microsoft.com/windows/uwp/publish/usage-report)
* [Метод Log](https://docs.microsoft.com/uwp/api/microsoft.services.store.engagement.storeservicescustomeventlogger.log)
* [Microsoft Store Services SDK](https://msdn.microsoft.com/windows/uwp/monetize/microsoft-store-services-sdk)

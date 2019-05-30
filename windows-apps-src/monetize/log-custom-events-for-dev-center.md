---
Description: Можно ведения журнала пользовательских событий из приложения универсальной платформы Windows и просмотреть эти события в отчете об использовании в центре партнеров.
title: Ведение журнала пользовательских событий для Центра партнеров
ms.date: 06/01/2018
ms.topic: article
keywords: windows 10, uwp, Microsoft Store Services SDK, журналы событий
ms.assetid: 4aa591e0-c22a-4c90-b316-0b5d0410af19
ms.localizationpriority: medium
ms.openlocfilehash: e45b14daf6951142cb0d0ed8714e981eb6a55628
ms.sourcegitcommit: ac7f3422f8d83618f9b6b5615a37f8e5c115b3c4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/29/2019
ms.locfileid: "66371043"
---
# <a name="log-custom-events-for-partner-center"></a>Ведение журнала пользовательских событий для Центра партнеров

[Отчет об использовании](https://docs.microsoft.com/windows/uwp/publish/usage-report) в центре партнеров позволяет получить сведения о пользовательских событиях, определенных в приложении универсальной платформы Windows (UWP). Пользовательское событие — это произвольная строка, представляющая событие или активность в вашем приложении. Например, игра может определять пользовательские события с именем *firstLevelPassed*, *secondLevelPassed* и т. д., которые регистрируются при прохождении пользователем каждого уровня в игре.

Для регистрации пользовательского события из вашего приложения передайте строку пользовательского события методу [Log](https://docs.microsoft.com/uwp/api/microsoft.services.store.engagement.storeservicescustomeventlogger.log), предоставляемому пакетом Microsoft Store Services SDK. Вы можете просмотреть общее вхождений для пользовательских событий в **пользовательские события** раздел [отчет об использовании](https://docs.microsoft.com/windows/uwp/publish/usage-report) в центре партнеров.

> [!NOTE]
> Пользовательские события, которые запротоколированы центр партнеров не связанных с [события Windows](https://docs.microsoft.com/windows/desktop/Events/windows-events), и они не отображаются в **средство просмотра событий**.

## <a name="prerequisites"></a>Предварительные требования

Перед просмотром настраиваемого протоколирования событий в **отчет об использовании** для вашего приложения в центре партнеров, приложения должны быть опубликованы в Store.

## <a name="how-to-log-custom-events"></a>Регистрация пользовательских событий

1. Если это еще не сделано, [установите Microsoft Store Services SDK](microsoft-store-services-sdk.md#install-the-sdk) на компьютер разработки.

2. Откройте проект в Visual Studio.

3. В обозревателе решений щелкните правой кнопкой мыши узел **Ссылки** вашего проекта и выберите команду **Добавить ссылку**.

4. В диалоговом окне **Диспетчер ссылок** разверните список **Универсальная платформа Windows** и выберите **Расширения**.

5. В списке пакетов SDK установите флажок рядом с пунктом **Microsoft Engagement Framework** и нажмите кнопку **ОК**.

6. Добавьте следующий оператор в верхнюю часть каждого файла кода, где требуется регистрировать пользовательские события.
    [!code-csharp[EventLogger](./code/StoreSDKSamples/cs/LogEvents.cs#EngagementNamespace)]

7. В каждом разделе своего кода, где требуется регистрировать пользовательские события, получите объект [StoreServicesCustomEventLogger](https://docs.microsoft.com/uwp/api/microsoft.services.store.engagement.storeservicescustomeventlogger.log), а затем вызовите метод [Log](https://docs.microsoft.com/uwp/api/microsoft.services.store.engagement.storeservicescustomeventlogger.log). Передайте строку пользовательского события в метод.
    [!code-csharp[EventLogger](./code/StoreSDKSamples/cs/LogEvents.cs#Log)]

    > [!NOTE]
    > Загрузка [отчета об использовании](https://docs.microsoft.com/windows/uwp/publish/usage-report) может занять много времени, если ваше приложение регистрирует много пользовательских событий с длинными именами. Мы рекомендуем использовать краткие имена для пользовательских событий. 

## <a name="related-topics"></a>См. также

* [Отчет об использовании](https://docs.microsoft.com/windows/uwp/publish/usage-report)
* [Метод log](https://docs.microsoft.com/uwp/api/microsoft.services.store.engagement.storeservicescustomeventlogger.log)
* [Microsoft Store Services SDK](https://docs.microsoft.com/windows/uwp/monetize/microsoft-store-services-sdk)

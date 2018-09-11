---
author: mcleanbyron
Description: You can log custom events from your UWP app and review those events in the Usage report on the Windows Dev Center dashboard.
title: Регистрация пользовательских событий в журнале для Центра разработки
ms.author: mcleans
ms.date: 06/01/2018
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: windows 10, uwp, Microsoft Store Services SDK, журналы событий
ms.assetid: 4aa591e0-c22a-4c90-b316-0b5d0410af19
ms.localizationpriority: medium
ms.openlocfilehash: 2b9cd4d7c527001bb382596c9c805be4ad5e7b08
ms.sourcegitcommit: 72710baeee8c898b5ab77ceb66d884eaa9db4cb8
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/11/2018
ms.locfileid: "3851334"
---
# <a name="log-custom-events-for-dev-center"></a>Регистрация пользовательских событий в журнале для Центра разработки

[Отчет об использовании](https://msdn.microsoft.com/windows/uwp/publish/usage-report) в информационной панели Центра разработки для Windows позволяет получить информацию о событиях, которые вы определили в своем приложении универсальной платформы Windows (UWP). Пользовательское событие — это произвольная строка, представляющая событие или активность в вашем приложении. Например, игра может определять пользовательские события с именем *firstLevelPassed*, *secondLevelPassed* и т. д., которые регистрируются при прохождении пользователем каждого уровня в игре.

Для регистрации пользовательского события из вашего приложения передайте строку пользовательского события методу [Log](https://docs.microsoft.com/uwp/api/microsoft.services.store.engagement.storeservicescustomeventlogger.log), предоставляемому пакетом Microsoft Store Services SDK. Можно просматривать общее число возникновений вашего пользовательского события в разделе **Пользовательские события** [Отчета об использовании](https://msdn.microsoft.com/windows/uwp/publish/usage-report) на информационной панели Центра разработки.

> [!NOTE]
> Пользовательские события, регистрируемые в Центре разработки, не связаны с [событиями Windows](https://msdn.microsoft.com/library/windows/desktop/aa964766.aspx) и не отображаются в средстве **Просмотр событий**.

## <a name="prerequisites"></a>Предварительные условия

Прежде чем вы сможете просматривать зарегистрированные пользовательские события в **Отчете об использовании** для вашего приложения на информационной панели, ваше приложение должно быть опубликовано в Магазине.

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

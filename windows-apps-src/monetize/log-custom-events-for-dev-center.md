---
author: mcleanbyron
Description: "Можно регистрировать пользовательские события из приложения UWP и просматривать эти события в отчете об использовании на информационной панели Центра разработки для Windows."
title: "Регистрация пользовательских событий в журнале для Центра разработки"
ms.author: mcleans
ms.date: 02/08/2017
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: "windows 10, uwp, Microsoft Store Services SDK, журналы событий"
ms.assetid: 4aa591e0-c22a-4c90-b316-0b5d0410af19
translationtype: Human Translation
ms.sourcegitcommit: 5645eee3dc2ef67b5263b08800b0f96eb8a0a7da
ms.openlocfilehash: 80cc3ec6aab90549c55ff8c8f78b54f5827f61ff
ms.lasthandoff: 02/08/2017

---

# <a name="log-custom-events-for-dev-center"></a>Регистрация пользовательских событий в журнале для Центра разработки

[Отчет об использовании](https://msdn.microsoft.com/windows/uwp/publish/usage-report) в информационной панели Центра разработки для Windows позволяет получить информацию о событиях, которые вы определили в своем приложении универсальной платформы Windows (UWP). Пользовательское событие — это произвольная строка, представляющая событие или активность в вашем приложении. Например, игра может определять пользовательские события с именем *firstLevelPassed*, *secondLevelPassed* и т. д., которые регистрируются при прохождении пользователем каждого уровня в игре.

Для регистрации пользовательского события из вашего приложения передайте строку пользовательского события методу [Log](https://msdn.microsoft.com/library/windows/apps/microsoft.services.store.engagement.storeservicescustomeventlogger.log.aspx), предоставляемому пакетом Microsoft Store Services SDK. Можно просматривать общее число возникновений вашего пользовательского события в разделе **Пользовательские события** [Отчета об использовании](https://msdn.microsoft.com/windows/uwp/publish/usage-report) на информационной панели Центра разработки.

>**Примечание.**&nbsp;&nbsp;Пользовательские события, регистрируемые в Центре разработки, не связаны с [событиями Windows](https://msdn.microsoft.com/library/windows/desktop/aa964766.aspx), и они не отображаются в **средстве просмотра событий**.

## <a name="prerequisites"></a>Предварительные условия

Прежде чем вы сможете просматривать зарегистрированные пользовательские события в **Отчете об использовании** для вашего приложения на информационной панели, ваше приложение должно быть опубликовано в Магазине.

## <a name="how-to-log-custom-events"></a>Регистрация пользовательских событий

1. Если это еще не сделано, [установите Microsoft Store Services SDK](microsoft-store-services-sdk.md#install-the-sdk) на компьютер разработки. Помимо API-интерфейса для регистрации пользовательских событий этот пакет SDK также предоставляет API-интерфейсы для других функций, например для проведения экспериментов с A/B-тестированием и показа рекламы.
2. Откройте проект в Visual Studio.
3. В обозревателе решений щелкните правой кнопкой мыши узел **Ссылки** вашего проекта и выберите команду **Добавить ссылку**.
4. В диалоговом окне **Диспетчер ссылок** разверните список **Универсальная платформа Windows** и выберите **Расширения**.
5. В списке пакетов SDK установите флажок рядом с пунктом **Microsoft Engagement Framework** и нажмите кнопку **ОК**.
7. Добавьте следующий оператор в верхнюю часть каждого файла кода, где требуется регистрировать пользовательские события.

  > [!div class="tabbedCodeSnippets"]
  [!code-cs[EventLogger](./code/StoreSDKSamples/cs/LogEvents.cs#EngagementNamespace)]

8. В каждом разделе своего кода, где требуется регистрировать пользовательские события, получите объект [StoreServicesCustomEventLogger](https://msdn.microsoft.com/library/windows/apps/microsoft.services.store.engagement.storeservicescustomeventlogger.log.aspx), а затем вызовите метод [Log](https://msdn.microsoft.com/library/windows/apps/microsoft.services.store.engagement.storeservicescustomeventlogger.log.aspx). Передайте строку пользовательского события в метод.

  > [!div class="tabbedCodeSnippets"]
  [!code-cs[EventLogger](./code/StoreSDKSamples/cs/LogEvents.cs#Log)]

## <a name="related-topics"></a>Статьи по теме

* [Отчет об использовании](https://msdn.microsoft.com/windows/uwp/publish/usage-report)
* [Метод Log](https://msdn.microsoft.com/library/windows/apps/microsoft.services.store.engagement.storeservicescustomeventlogger.log.aspx)
* [Microsoft Store Services SDK](https://msdn.microsoft.com/windows/uwp/monetize/microsoft-store-services-sdk)


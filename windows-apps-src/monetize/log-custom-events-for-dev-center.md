---
author: mcleanbyron
Description: "Можно регистрировать пользовательские события из приложения UWP и просматривать эти события в отчете об использовании на информационной панели Центра разработки для Windows."
title: "Регистрация пользовательских событий в журнале для Центра разработки"
translationtype: Human Translation
ms.sourcegitcommit: 126fee708d82f64fd2a49b844306c53bb3d4cc86
ms.openlocfilehash: 61874c700ecd31c7246effef5b05ffbf1153dfd5

---

# Регистрация пользовательских событий в журнале для Центра разработки

[Отчет об использовании](https://msdn.microsoft.com/windows/uwp/publish/usage-report) в информационной панели Центра разработки для Windows позволяет получить информацию о событиях, которые вы определили в своем приложении универсальной платформы Windows (UWP). Пользовательское событие — это произвольная строка, представляющая событие или активность в вашем приложении. Например, игра может определять пользовательские события с именем *firstLevelPassed*, *secondLevelPassed* и т. д., которые регистрируются при прохождении пользователем каждого уровня в игре.

Для регистрации пользовательского события из вашего приложения передайте строку пользовательского события методу [Log](https://msdn.microsoft.com/library/windows/apps/microsoft.services.store.engagement.storeservicescustomeventlogger.log.aspx), предоставляемому пакетом Microsoft Store Services SDK. Можно просматривать общее число возникновений вашего пользовательского события в разделе **Пользовательские события** [Отчета об использовании](https://msdn.microsoft.com/windows/uwp/publish/usage-report) на информационной панели Центра разработки.

## Необходимые компоненты

Прежде чем вы сможете просматривать зарегистрированные пользовательские события в **Отчете об использовании** для вашего приложения на информационной панели, ваше приложение должно быть опубликовано в Магазине.

## Регистрация пользовательских событий

1. Если это еще не сделано, [установите Microsoft Store Services SDK](microsoft-store-services-sdk.md#install-the-sdk) на компьютер разработки. Помимо API-интерфейса для регистрации пользовательских событий этот пакет SDK также предоставляет API-интерфейсы для других функций, например для проведения экспериментов с A/B-тестированием и показа рекламы. 
2. Откройте проект в Visual Studio.
3. В обозревателе решений щелкните правой кнопкой мыши узел **Ссылки** вашего проекта и выберите команду **Добавить ссылку**.
4. В диалоговом окне **Диспетчер ссылок** разверните список **Универсальная платформа Windows** и выберите **Расширения**.
5. В списке пакетов SDK установите флажок рядом с пунктом **Microsoft Engagement Framework** и нажмите кнопку **ОК**.
7. Добавьте следующий оператор в верхнюю часть каждого файла кода, где вы хотите регистрировать пользовательские события.

  ```csharp
  using Microsoft.Services.Store.Engagement;
  ```
8. В каждом разделе своего кода, где требуется регистрировать пользовательские события, получите объект [StoreServicesCustomEventLogger](https://msdn.microsoft.com/library/windows/apps/microsoft.services.store.engagement.storeservicescustomeventlogger.log.aspx), а затем вызовите метод [Log](https://msdn.microsoft.com/library/windows/apps/microsoft.services.store.engagement.storeservicescustomeventlogger.log.aspx). Передайте строку пользовательского события в метод.

  ```csharp
  StoreServicesCustomEventLogger logger = StoreServicesCustomEventLogger.GetDefault();
  logger.Log("myCustomEvent");
  ```

## Статьи по теме

* [Отчет об использовании](https://msdn.microsoft.com/windows/uwp/publish/usage-report)
* [Метод Log](https://msdn.microsoft.com/library/windows/apps/microsoft.services.store.engagement.storeservicescustomeventlogger.log.aspx)
* [Microsoft Store Services SDK](https://msdn.microsoft.com/windows/uwp/monetize/microsoft-store-services-sdk)



<!--HONumber=Nov16_HO1-->



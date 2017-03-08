---
author: mcleanbyron
Description: "Пакет Microsoft Store Services SDK предоставляет библиотеки и средства, которые вы можете использовать для добавления в приложения функций, помогающие заработать и привлечь пользователей."
title: "Пакет Microsoft Store Services SDK"
ms.assetid: 518516DB-70A7-49C4-B3B6-CD8A98320B9C
ms.author: mcleans
ms.date: 02/08/2017
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: windows 10, uwp, Microsoft Store Services SDK
translationtype: Human Translation
ms.sourcegitcommit: c6b64cff1bbebc8ba69bc6e03d34b69f85e798fc
ms.openlocfilehash: 7a8dcb282ea3df16ee8a12247a07af27cbf65b3a
ms.lasthandoff: 02/07/2017

---

# <a name="microsoft-store-services-sdk"></a>Пакет Microsoft Store Services SDK

Пакет Microsoft Store Services SDK предоставляет функции, которые помогают заработать и повышать интерес пользователей к приложениям универсальной платформы Windows (UWP), например показ рекламы в приложениях и проведение экспериментов с A/B-тестированием. Этот пакет SDK является расширением Visual Studio 2015 и более поздних версий Visual Studio.

## <a name="scenarios-supported-by-the-sdk"></a>Сценарии, поддерживаемые SDK

Пакет SDK в настоящее время поддерживает следующие сценарии для приложений UWP. Со временем этот пакет SDK станет поддерживать новые сценарии привлечения пользователей и монетизации. Справочную документацию по API-интерфейсам в пакете SDK см. в [справочнике по API пакета Microsoft Store Services SDK](https://msdn.microsoft.com/library/windows/apps/mt691886.aspx).

|  Сценарий  |  Описание   |
|------------|----------------|
|  [Проведение экспериментов с A/B-тестированием в приложениях UWP](run-app-experiments-with-a-b-testing.md)    |  Проводите A/B-тестирование в своем приложении для универсальной платформы Windows (UWP), чтобы оценить эффективность функций на некоторых пользователях перед выпуском этих функций для всех пользователей. После определения эксперимента в информационной панели Центра разработки используйте класс [StoreServicesExperimentVariation](https://msdn.microsoft.com/library/windows/apps/microsoft.services.store.engagement.storeservicesexperimentvariation.aspx), чтобы получить варианты эксперимента. Воспользуйтесь этими данными, чтобы изменить поведение тестируемой функции, а затем вызовите метод [LogForVariation](https://msdn.microsoft.com/library/windows/apps/microsoft.services.store.engagement.storeservicescustomeventlogger.logforvariation.aspx), чтобы отправить событие просмотра и события преобразования в Центр разработки. Наконец, используйте информационную панель для просмотра результатов и управления экспериментом.  |
|  [Запуск Центра отзывов из приложения UWP](launch-feedback-hub-from-your-app.md)    |  Используйте класс [StoreServicesFeedbackLauncher](https://msdn.microsoft.com/library/windows/apps/microsoft.services.store.engagement.storeservicesfeedbacklauncher.aspx) в своем приложении UWP, чтобы направлять пользователей Windows 10 в Центр отзывов, где они смогут сообщать о проблемах, делиться предложениями и голосовать за комментарии других пользователей. Затем вы сможете проанализировать все эти данные в [отчете об отзывах](../publish/feedback-report.md) на информационной панели Центра разработки. |
|  [Настройка приложения UWP для получения push-уведомлений Центра разработки](configure-your-app-to-receive-dev-center-notifications.md)    |  Используйте класс [StoreServicesEngagementManager](https://msdn.microsoft.com/library/windows/apps/microsoft.services.store.engagement.storeservicesengagementmanager.aspx) в своем приложении UWP, чтобы зарегистрировать приложение для получения целевых push-уведомлений, отправляемых пользователям с помощью информационной панели Центра разработки для Windows.  |
|   [Записывайте пользовательские события в журнал в своем приложении UWP для создания отчетов об использовании в Центре разработки](log-custom-events-for-dev-center.md)   |  Используйте класс [StoreServicesCustomEventLogger](https://msdn.microsoft.com/library/windows/apps/microsoft.services.store.engagement.storeservicescustomeventlogger.log.aspx) в своем приложении UWP для регистрации в журнале пользовательских событий вашего приложения в Центре разработки. Просмотрите общее число возникновений вашего пользовательского события в разделе **Пользовательские события** [Отчета об использовании](https://msdn.microsoft.com/windows/uwp/publish/usage-report) в информационной панели Центра разработки.  |
|  [Показ рекламы в приложении UWP](display-ads-in-your-app.md)    |  Используйте элементы управления [AdControl](https://msdn.microsoft.com/library/windows/apps/microsoft.advertising.winrt.ui.adcontrol.aspx) или [InterstitialAd](https://msdn.microsoft.com/library/windows/apps/microsoft.advertising.winrt.ui.interstitialad.aspx) в своем приложении UWP, чтобы увеличить прибыль, показывая рекламные баннеры или промежуточную рекламу.<br/><br/>**Примечание.**&nbsp;&nbsp;Microsoft Store Services SDK поддерживает только приложения UWP для Windows 10. Чтобы показывать рекламу в приложениях Windows 8.1 и Windows Phone 8.x, следуйте инструкциям из раздела [Microsoft Advertising SDK для Windows и Windows Phone 8.x](http://aka.ms/store-8-sdk).  |

<span id="prerequisites" />
## <a name="prerequisites"></a>Необходимые компоненты

Microsoft Store Services SDK предъявляет следующие требования.

* Visual Studio 2015 или более поздняя версия.
* Инструменты Visual Studio для универсальных приложений для Windows, установленные в вашей версии Visual Studio.

>**Примечание.**&nbsp;&nbsp;Для установки пакета Microsoft Store Services SDK с помощью Visual Studio 2015 должны быть установлены инструменты Visual Studio для универсальных приложений для Windows версии 1.1 или более поздней. Дополнительные сведения об этом обновлении инструментов Visual Studio для универсальных приложений для Windows см. в [примечаниях к выпуску](http://go.microsoft.com/fwlink/?LinkID=624516).

<span id="install" />
## <a name="install-the-sdk"></a>Установка SDK

Существует два варианта установки Microsoft Store Services SDK для использования с Visual Studio 2015 (или более поздней версией) на компьютере разработчика.

* **Установщик MSI.**&nbsp;&nbsp;Можно установить пакет SDK с помощью установщика MSI, доступного [здесь](http://aka.ms/store-em-sdk). В этом случае библиотеки SDK устанавливаются в общей папке на компьютере разработчика, чтобы на них мог ссылаться любой проект UWP в Visual Studio.
* **Пакет NuGet.**&nbsp;&nbsp;Можно установить библиотеки SDK для конкретного проекта UWP в Visual Studio с использованием NuGet. В этом случае библиотеки SDK устанавливаются только для проекта, в котором установлен пакет NuGet.

Корпорация Майкрософт периодически выпускает новые версии пакета Microsoft Store Services SDK для повышения производительности и расширения возможностей. Если у вас уже есть проекты, в которых используется этот пакет SDK, и вы хотите использовать его последнюю версию, скачайте и установите последнюю версию SDK на свой компьютер разработки.

>**Примечание.**&nbsp;&nbsp;Для установки пакета Microsoft Store Services SDK с помощью Visual Studio 2015 должны быть установлены инструменты Visual Studio для универсальных приложений для Windows версии 1.1 или более поздней. Дополнительные сведения об этом обновлении инструментов Visual Studio для универсальных приложений для Windows см. в [примечаниях к выпуску](http://go.microsoft.com/fwlink/?LinkID=624516).

<span id="install-msi" />
### <a name="install-via-msi"></a>Установка с помощью MSI

Установка Microsoft Store Services SDK с помощью установщика MSI.

1.  Закройте все экземпляры Visual Studio 2015 (или более поздней версии). Если вы ранее устанавливали какую-либо из предыдущих версий пакетов Microsoft Advertising SDK, Universal Ad Client SDK, расширения Ad Mediator или Microsoft Store Engagement and Monetization SDK, теперь необходимо удалить эти версии пакетов SDK.

2.    Откройте окно **командной строки** и выполните эти команды для удаления всех более ранних версий пакетов рекламных SDK, которые могли быть установлены вместе с Visual Studio, но, возможно, не отображаются в списке установленных программ на компьютере:
  ```
  MsiExec.exe /x{5C87A4DB-31C7-465E-9356-71B485B69EC8}
  MsiExec.exe /x{6AB13C21-C3EC-46E1-8009-6FD5EBEE515B}
  MsiExec.exe /x{6AC81125-8485-463D-9352-3F35A2508C11}
  ```

3.  Скачайте и установите пакет [Microsoft Store Services SDK](http://aka.ms/store-em-sdk). Установка может занять несколько минут. Обязательно дождитесь завершения процесса.

4.  Перезапустите Visual Studio.

5.  Если у вас есть существующий проект, который ссылается на библиотеки из какой-либо более ранней версии пакетов Microsoft Store Services SDK, Microsoft Advertising SDK, Universal Ad Client SDK или Microsoft Store Engagement and Monetization SDK, рекомендуется открыть этот проект в Visual Studio, очистить и заново собрать проект (в **Обозревателе решений** щелкните правой кнопкой мыши по узлу проекта и выберите пункт **Очистить**, затем снова щелкните правой кнопкой мыши по узлу проекта и выберите **Пересобрать**).

  В противном случае, если вы используете пакет SDK в первый раз в своем проекте, теперь вы готовы [добавить соответствующие ссылки на библиотеки Microsoft Store Services SDK в проект](#references).

<span id="install-nuget" />
### <a name="install-via-nuget"></a>Установка с помощью NuGet

Установка библиотек Microsoft Store Services SDK для конкретного проекта с помощью NuGet.

1.  Закройте все экземпляры Visual Studio 2015 (или более поздней версии). Если вы ранее устанавливали какую-либо из предыдущих версий пакетов Microsoft Advertising SDK, Universal Ad Client SDK, расширения Ad Mediator или Microsoft Store Engagement and Monetization SDK, теперь необходимо удалить эти версии пакетов SDK.

2.    Откройте окно **командной строки** и выполните эти команды для удаления всех более ранних версий пакетов рекламных SDK, которые могли быть установлены вместе с Visual Studio, но, возможно, не отображаются в списке установленных программ на компьютере:
  ```
  MsiExec.exe /x{5C87A4DB-31C7-465E-9356-71B485B69EC8}
  MsiExec.exe /x{6AB13C21-C3EC-46E1-8009-6FD5EBEE515B}
  MsiExec.exe /x{6AC81125-8485-463D-9352-3F35A2508C11}
  ```

3.  Запустите Visual Studio и откройте проект, в котором вы хотите использовать библиотеки Microsoft Store Services SDK.

  >**Примечание.**&nbsp;&nbsp;Если ваш проект уже содержит ссылки на библиотеки из предыдущей установки пакета SDK с помощью MSI, необходимо удалить эти ссылки из проекта. Рядом с этими ссылками будут расположены предупреждающие значки, поскольку библиотеки, на которые они ссылаются, были удалены ранее.

4. В Visual Studio щелкните **Проект** и выберите параметр **Управление пакетами NuGet**.

5. В поле поиска введите **Microsoft.Services.Store.SDK** и установите пакет Microsoft.Services.Store.SDK.

  >**Примечание.**&nbsp;&nbsp;Если в окне **Вывод** содержится ошибка *Пакета установки*, указывающая, что заданный путь слишком длинный, вам может потребоваться настроить NuGet на извлечение пакетов в альтернативное расположение с более коротким путем, чем у расположения по умолчанию. Для этого добавьте значение ```repositoryPath``` в файл nuget.config на компьютере и задайте ему более короткий путь к папке, куда можно извлечь пакеты NuGet. Дополнительные сведения см. в [этой статье](http://docs.nuget.org/ndocs/consume-packages/configuring-nuget-behavior) в документации NuGet. Кроме того можно попробовать переместить проект Visual Studio в другую папку с более коротким путем.

6. Закройте проект и снова откройте его.

7.  Если проект уже содержит ссылки на библиотеки из более ранней версии Microsoft Store Services SDK, которая была установлена с помощью NuGet, и вы обновили свой проект до более нового выпуска SDK, рекомендуется очистить и пересобрать проект (в **Обозревателе решений** щелкните правой кнопкой мыши по узлу проекта и выберите пункт **Очистить**, затем снова щелкните правой кнопкой мыши по узлу проекта и выберите **Пересобрать**).

  В противном случае, если вы используете пакет SDK в первый раз в своем проекте, теперь вы готовы [добавить соответствующие ссылки на библиотеки Microsoft Store Services SDK в проект](#references).

<span id="references" />
## <a name="add-sdk-library-references-to-your-project"></a>Добавление ссылок на библиотеки SDK в проект

После установки Microsoft Store Services SDK с помощью установщика MSI или NuGet следуйте этим инструкциям для добавления ссылок на библиотеки SDK в свой проект UWP.

1. Откройте проект в Visual Studio.

  >**Примечание.**&nbsp;&nbsp;Если ваш проект представляет собой приложение JavaScript и направлен на работу на **Любом ЦП**, обновите его, чтобы он использовал результаты сборки, предназначенные для определенной архитектуры (например **x86**).

2. В **обозревателе решений**щелкните правой кнопкой мыши пункт **Ссылки** и выберите **Добавить ссылку...**.

3. В **Диспетчере ссылок** разверните **универсальные для Windows**, нажмите **Расширения**и затем установите флажок рядом с одним из следующих элементов.

  * Для использования API в пространстве имен [Microsoft.Services.Store.Engagement](https://msdn.microsoft.com/library/windows/apps/microsoft.services.store.engagement.aspx) для сценариев привлечения клиентов установите флажок рядом с **Microsoft Engagement Framework**. Выберите этот вариант, если хотите [проводить A/B-эксперименты](run-app-experiments-with-a-b-testing.md), [запускать Центр отзывов](launch-feedback-hub-from-your-app.md), [получать целевые push-уведомления из Центра разработки](configure-your-app-to-receive-dev-center-notifications.md) или [регистрировать пользовательские события в журнале в Центре разработки](log-custom-events-for-dev-center.md).

  * Для использования API для [показа рекламных баннеров или промежуточной рекламы в вашем приложении](display-ads-in-your-app.md) установите флажок рядом с **Microsoft Advertising SDK для XAML** или **Microsoft Advertising SDK для JavaScript** в зависимости от типа проекта.

3. Нажмите **ОК**.

>**Примечание.**&nbsp;&nbsp;Если вы установили библиотеки SDK с помощью NuGet, ваш проект будет содержать ссылку на **Microsoft.Services.Store.SDK** в дополнение к **Microsoft Advertising SDK для XAML** или **Microsoft Advertising SDK для JavaScript**. Ссылка на **Microsoft.Services.Store.SDK** представляет пакет NuGet (а не библиотеки в нем), и вы можете проигнорировать ее.

<span id="framework" />
## <a name="understanding-framework-packages-in-the-sdk"></a>Понимание пакетов платформы в SDK

Следующие библиотеки в пакете Microsoft Store Services SDK настроены как *пакеты платформы*:

* Microsoft.Advertising.dll. Эта библиотека содержит API-интерфейсы рекламы в пространствах имен [Microsoft.Advertising](https://msdn.microsoft.com/library/windows/apps/mt313187.aspx) и [Microsoft.Advertising.WinRT.UI](https://msdn.microsoft.com/library/windows/apps/microsoft.advertising.winrt.ui.aspx).
* Microsoft.Services.Store.Engagement.dll. Эта библиотека содержит API-интерфейсы в пространстве имен [Microsoft.Services.Store.Engagement](https://msdn.microsoft.com/library/windows/apps/microsoft.services.store.engagement.aspx).

Это означает, что после установки пользователем версии вашего приложения, которое применяет эти библиотеки, они также будут автоматически обновляться на устройстве пользователя через Центр обновления Windows, когда мы опубликуем их новые версии с исправлениями и улучшениями производительности. Это позволяет гарантировать, что пользователи всегда будут иметь последнюю версию библиотек на своих устройствах.

Если мы выпустим новую версию SDK с новыми API-интерфейсами или функциями в этих библиотеках, вам придется установить последнюю версию пакета SDK, чтобы использовать их. В этом случае вам также понадобится опубликовать обновленное приложение в Магазине.

## <a name="related-topics"></a>Связанные статьи

* [Справочник по API пакета Microsoft Store Services SDK](https://msdn.microsoft.com/library/windows/apps/mt691886.aspx)
* [Проведение экспериментов с использованием A/B-тестирования](run-app-experiments-with-a-b-testing.md)
* [Запуск Центра отзывов из приложения](launch-feedback-hub-from-your-app.md)
* [Настройка приложения для получения push-уведомлений Центра разработки](configure-your-app-to-receive-dev-center-notifications.md)
* [Регистрация пользовательских событий в журнале для Центра разработки](log-custom-events-for-dev-center.md)
* [Показ рекламы в приложении](display-ads-in-your-app.md)


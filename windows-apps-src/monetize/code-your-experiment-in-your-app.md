---
Description: Чтобы выполнить эксперимент в приложении универсальной платформы Windows (UWP) с использованием A/B-тестирования, необходимо создать код эксперимента в вашем приложении.
title: Кодирование приложения для экспериментов
ms.assetid: 6A5063E1-28CD-4087-A4FA-FBB511E9CED5
ms.date: 02/08/2017
ms.topic: article
keywords: Windows 10, uwp, Microsoft Store Services SDK, А/B тестирование, эксперименты
ms.localizationpriority: medium
ms.openlocfilehash: edd0fbcf841dc9d8fa43873da95dc08b276a5418
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57636749"
---
# <a name="code-your-app-for-experimentation"></a>Кодирование приложения для экспериментов

После того как вы [Создание проекта и определения удаленной переменных в центре партнеров](create-a-project-and-define-remote-variables-in-the-dev-center-dashboard.md), вы будете готовы обновить код в приложении универсальной платформы Windows (UWP):
* Получение удаленного значения переменных из центра партнеров.
* Использование удаленных переменных для настройки процесса взаимодействия пользователей с приложением.
* Регистрировать события в центре партнеров, которые указывают, когда пользователи просматривать свой эксперимент и выполнить нужное действие (также называется *преобразования*).

Чтобы добавить это поведение в приложение, используйте API-интерфейсы из пакета Microsoft Store Services SDK.

Следующие разделы описывают общий процесс получения вариантов для эксперимента, и ведение журнала событий для центра партнеров. После вы код приложения для службы "Экспериментирование", вы можете [определить эксперимента в центре партнеров](define-your-experiment-in-the-dev-center-dashboard.md). Пошаговое руководство, демонстрирующее весь процесс создания и выполнения эксперимента, можно найти в разделе [Создание и проведение первого эксперимента с использованием A/B-тестирования](create-and-run-your-first-experiment-with-a-b-testing.md).

> [!NOTE]
> Некоторые из службы "Экспериментирование" API-интерфейсов в Microsoft Store Services SDK используют [асинхронной модели](../threading-async/asynchronous-programming-universal-windows-platform-apps.md) для получения данных из центра партнеров. Это означает, что эти методы могут частично выполняться после их вызова, чтобы интерфейс приложения реагировал на действия пользователя во время выполнения операции. В рамках асинхронной модели приложение должно использовать ключевое слово **async** и оператор **await** при вызове API-интерфейсов, как показано в примерах кода в этой статье. По соглашению асинхронные методы заканчиваются на **Async**.

## <a name="configure-your-project"></a>Настройка проекта

Сначала необходимо установить пакет Microsoft Store Services SDK на компьютер, на котором ведется разработка, и добавить в проект необходимые ссылки.

1. [Установите пакет Microsoft Store Services SDK](microsoft-store-services-sdk.md#install-the-sdk).
2. Откройте проект в Visual Studio.
3. В обозревателе решений разверните узел проекта, щелкните правой кнопкой мыши **Ссылки**, а затем — **Добавить ссылку**.
3. В диалоговом окне **Диспетчер ссылок** разверните список **Универсальная платформа Windows** и выберите **Расширения**.
4. В списке пакетов SDK установите флажок рядом с пунктом **Microsoft Engagement Framework** и нажмите кнопку **ОК**.

> [!NOTE]
> В примерах кода в этой статье предполагается, что файл кода содержит инструкции **using** для пространств имен **System.Threading.Tasks** и **Microsoft.Services.Store.Engagement**.

## <a name="get-variation-data-and-log-the-view-event-for-your-experiment"></a>Получение данных о вариантах и запись события просмотра в журнал для эксперимента

Откройте проект и найдите код для функции, которую требуется изменить в рамках эксперимента. Добавьте код, который извлекает данные для варианта, эти данные можно использовать для изменения поведения функции, тестировании и войдите событие представления для своего эксперимента к A / B-тестирования службы в центре партнеров.

Требуемый код зависит от приложения, но в следующем примере показан базовый процесс. Полный пример кода можно найти в разделе [Создание и проведение первого эксперимента с использованием A/B-тестирования](create-and-run-your-first-experiment-with-a-b-testing.md).

[!code-cs[ExperimentExamples](./code/StoreSDKSamples/cs/ExperimentExamples.cs#ExperimentCodeSample)]

Ниже важные части этого процесса показаны более подробно.

1. Объявите [StoreServicesExperimentVariation](https://docs.microsoft.com/uwp/api/microsoft.services.store.engagement.storeservicesexperimentvariation) , представляющий назначения текущего варианта и [StoreServicesCustomEventLogger](https://docs.microsoft.com/uwp/api/microsoft.services.store.engagement.storeservicescustomeventlogger) объект, который будет использоваться для представления журнала и преобразования события для центра партнеров.

    [!code-cs[ExperimentExamples](./code/StoreSDKSamples/cs/ExperimentExamples.cs#Snippet1)]

2. Объявите переменную типа string и присвойте в качестве значения [идентификатор проекта](run-app-experiments-with-a-b-testing.md#terms) для эксперимента, который вы хотите получить.
    > [!NOTE]
    > Получить проект код, когда вы [Создание проекта в центре партнеров](create-a-project-and-define-remote-variables-in-the-dev-center-dashboard.md). Указанный ниже идентификатор проекта используется только в качестве примера.

    [!code-cs[ExperimentExamples](./code/StoreSDKSamples/cs/ExperimentExamples.cs#Snippet2)]

3. Получите текущее назначение кэшированного варианта для эксперимента, вызвав статический метод [GetCachedVariationAsync](https://docs.microsoft.com/uwp/api/microsoft.services.store.engagement.storeservicesexperimentvariation.getcachedvariationasync), и передайте методу идентификатор проекта для вашего эксперимента. Этот метод возвращает объект [StoreServicesExperimentVariationResult](https://docs.microsoft.com/uwp/api/microsoft.services.store.engagement.storeservicesexperimentvariationresult), предоставляющий доступ к назначению варианта через свойство [ExperimentVariation](https://docs.microsoft.com/uwp/api/microsoft.services.store.engagement.storeservicesexperimentvariationresult.experimentvariation).

    [!code-cs[ExperimentExamples](./code/StoreSDKSamples/cs/ExperimentExamples.cs#Snippet3)]

4. Проверьте свойство [IsStale](https://docs.microsoft.com/uwp/api/microsoft.services.store.engagement.storeservicesexperimentvariation.isstale), чтобы определить, требуется ли обновить назначение кэшированного варианта с помощью удаленного назначения варианта с сервера. Если необходимость в обновлении отсутствует, вызовите статический метод [GetRefreshedVariationAsync](https://docs.microsoft.com/uwp/api/microsoft.services.store.engagement.storeservicesexperimentvariation.getrefreshedvariationasync), чтобы проверить наличие обновленного назначения варианта на сервере и обновить локальный кэшированный вариант.

    [!code-cs[ExperimentExamples](./code/StoreSDKSamples/cs/ExperimentExamples.cs#Snippet4)]

5. Используйте методы [GetBoolean](https://docs.microsoft.com/uwp/api/microsoft.services.store.engagement.storeservicesexperimentvariation.getboolean), [GetDouble](https://docs.microsoft.com/uwp/api/microsoft.services.store.engagement.storeservicesexperimentvariation.getdouble), [GetInt32](https://docs.microsoft.com/uwp/api/microsoft.services.store.engagement.storeservicesexperimentvariation.getint32) или [GetString](https://docs.microsoft.com/uwp/api/microsoft.services.store.engagement.storeservicesexperimentvariation.getstring) объекта [StoreServicesExperimentVariation](https://docs.microsoft.com/uwp/api/microsoft.services.store.engagement.storeservicesexperimentvariation) для получения значений назначения варианта. В каждом методе первый параметр — имя варианта, который вы хотите получить (это же имя вариант, введите в центре партнеров). Второй параметр является значением по умолчанию, метод должен вернуть, если он не сможет получить указанное значение из центра партнеров (например, если отсутствует сетевое подключение), и кэшированная версия вариант недоступен.

    В следующем примере используется метод [GetString](https://docs.microsoft.com/uwp/api/microsoft.services.store.engagement.storeservicesexperimentvariation.getstring), чтобы получить переменную с именем *buttonText*, а в качестве значения по умолчанию для варианта указано значение **Grey Button**.

    [!code-cs[ExperimentExamples](./code/StoreSDKSamples/cs/ExperimentExamples.cs#Snippet5)]

6. В своем коде используйте значения варианта для изменения поведения тестируемой функции. Например, в следующем коде содержимому кнопки назначается значение *buttonText* в приложении. В этом примере предполагается, что вы уже определили эту кнопку в другом месте в проекте.

    [!code-cs[ExperimentExamples](./code/StoreSDKSamples/cs/ExperimentExamples.cs#Snippet6)]

7. Наконец, входа [событие представления](run-app-experiments-with-a-b-testing.md#terms) для своего эксперимента в a / B-тестирования службы в центре партнеров. Инициализируйте поле ```logger``` как объект [StoreServicesCustomEventLogger](https://docs.microsoft.com/uwp/api/microsoft.services.store.engagement.storeservicescustomeventlogger) и вызовите метод [LogForVariation](https://docs.microsoft.com/uwp/api/microsoft.services.store.engagement.storeservicescustomeventlogger.logforvariation). Передайте [StoreServicesExperimentVariation](https://docs.microsoft.com/uwp/api/microsoft.services.store.engagement.storeservicesexperimentvariation) объект, представляющий текущий вариант назначения (этот объект предоставляет контекст о событии центра партнеров) и имя события представления для своего эксперимента. Это должно совпадать с именем представления событий, которое вводится для эксперимента в центре партнеров. Код должен записать в журнал событие просмотра, когда пользователь начинает просмотр варианта, являющегося частью эксперимента.

    В следующем примере показано, как записать в журнал событие просмотра с именем **userViewedButton**. В этом примере цель эксперимента — заставить пользователя нажать кнопку в приложении, чтобы записать событие просмотра в журнал после того, как приложение получит данные о вариантах (в данном случае это текст кнопки) и сделает их содержимым кнопки.

    [!code-cs[ExperimentExamples](./code/StoreSDKSamples/cs/ExperimentExamples.cs#Snippet7)]

## <a name="log-conversion-events-to-partner-center"></a>Регистрировать события преобразования в центре партнеров

Добавьте следующий код, который регистрирует [события преобразования](run-app-experiments-with-a-b-testing.md#terms) к A / B-тестирования службы в центре партнеров. Код должен заносить в журнал событие преобразования, когда пользователь достигает цели эксперимента. В каждом случае требуемый код зависит от приложения, но есть несколько общих действий. Полный пример кода можно найти в разделе [Создание и проведение первого эксперимента с использованием A/B-тестирования](create-and-run-your-first-experiment-with-a-b-testing.md).

1. В коде, который выполняется, когда пользователь достигает цель одной из задач эксперимента, повторно вызовите метод [LogForVariation](https://docs.microsoft.com/uwp/api/microsoft.services.store.engagement.storeservicescustomeventlogger.logforvariation) и передайте объект [StoreServicesExperimentVariation](https://docs.microsoft.com/uwp/api/microsoft.services.store.engagement.storeservicesexperimentvariation) и имя события преобразования для эксперимента. Это должно соответствовать одному из имена событий преобразования, введенные для эксперимента в центре партнеров.

    В следующем примере в журнал записывается событие преобразования с именем **userClickedButton** из обработчика события **Click** для кнопки. В этом примере цель эксперимента — заставить пользователя нажать кнопку.

    [!code-cs[ExperimentExamples](./code/StoreSDKSamples/cs/ExperimentExamples.cs#Snippet8)]

## <a name="next-steps"></a>Дальнейшие действия

После создания кода для проведения эксперимента в приложении можно выполнять следующие действия.
1. [Определите свой эксперимент в центре партнеров](define-your-experiment-in-the-dev-center-dashboard.md). (создание эксперимента, определяющего события просмотра и преобразования, а также уникальные варианты для A/B-теста).
2. [Запуск и контроль эксперимента в центре партнеров](manage-your-experiment.md).


## <a name="related-topics"></a>Статьи по теме

* [Создание проекта и определения удаленной переменных в центре партнеров](create-a-project-and-define-remote-variables-in-the-dev-center-dashboard.md)
* [Определите свой эксперимент в центре партнеров](define-your-experiment-in-the-dev-center-dashboard.md)
* [Управление эксперимента в центре партнеров](manage-your-experiment.md)
* [Создание и запуск первого эксперимента с A / B-тестирование](create-and-run-your-first-experiment-with-a-b-testing.md)
* [Запустите приложение экспериментов с A / B-тестирование](run-app-experiments-with-a-b-testing.md)

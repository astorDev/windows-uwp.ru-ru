---
author: mcleanbyron
Description: "Чтобы выполнить эксперимент в приложении универсальной платформы Windows (UWP) с использованием A/B-тестирования, необходимо создать код эксперимента в вашем приложении."
title: "Кодирование приложения для экспериментов"
ms.assetid: 6A5063E1-28CD-4087-A4FA-FBB511E9CED5
translationtype: Human Translation
ms.sourcegitcommit: 126fee708d82f64fd2a49b844306c53bb3d4cc86
ms.openlocfilehash: ae0ddedf09913d42a036f48d2f60d7a62769b4bb

---

# Кодирование приложения для экспериментов

После [создания проекта и определения удаленных переменных на панели мониторинга Центра разработки](create-a-project-and-define-remote-variables-in-the-dev-center-dashboard.md) можно приступить к обновлению кода вашего приложения универсальной платформы Windows (UWP) для реализации следующих целей:
* Получение значений удаленных переменных из Центра разработки для Windows.
* Использование удаленных переменных для настройки процесса взаимодействия пользователей с приложением.
* Ведение журнала событий в Центре разработки, содержащего сведения о том, когда пользователи просмотрели ваш эксперимент и выполнили соответствующее действие (также называемое *преобразованием*).

Чтобы добавить это поведение в приложение, используйте API-интерфейсы из пакета Microsoft Store Services SDK.

В следующих разделах описывается общий процесс получения вариантов для эксперимента и ведения журнала событий в Центре разработки. После завершения кодирования приложения для экспериментов можно [определить эксперимент на панели мониторинга Центра разработки](define-your-experiment-in-the-dev-center-dashboard.md). Пошаговое руководство, демонстрирующее весь процесс создания и выполнения эксперимента, можно найти в разделе [Создание и проведение первого эксперимента с использованием A/B-тестирования](create-and-run-your-first-experiment-with-a-b-testing.md).

>**Примечание.**&nbsp;&nbsp;Некоторые экспериментальные API в пакете SDK служб Магазина Windows используют [асинхронную+ модель](../threading-async/asynchronous-programming-universal-windows-platform-apps.md) для извлечения данных из Центра разработки. Это означает, что эти методы могут частично выполняться после их вызова, чтобы интерфейс приложения реагировал на действия пользователя во время выполнения операции. В рамках асинхронной модели приложение должно использовать ключевое слово **async** и оператор **await** при вызове API-интерфейсов, как показано в примерах кода в этой статье. По соглашению асинхронные методы заканчиваются на **Async**.

## Настройка проекта

Сначала необходимо установить пакет Microsoft Store Services SDK на компьютер, на котором ведется разработка, и добавить в проект необходимые ссылки.

1. [Установите пакет Microsoft Store Services SDK](microsoft-store-services-sdk.md#install-the-sdk).
2. Откройте проект в Visual Studio.
3. В обозревателе решений разверните узел проекта, щелкните правой кнопкой мыши **Ссылки**, а затем— **Добавить ссылку**.
3. В диалоговом окне **Диспетчер ссылок** разверните список **Универсальная платформа Windows** и выберите **Расширения**.
4. В списке пакетов SDK установите флажок рядом с пунктом **Microsoft Engagement Framework** и нажмите кнопку **ОК**.

>**Примечание**&nbsp;&nbsp;В примерах кода в этой статье предполагается, что файл кода содержит инструкции **using** для пространств имен **System.Threading.Tasks** и **Microsoft.Services.Store.Engagement**.

## Получение данных о вариантах и запись события просмотра в журнал для эксперимента

Откройте проект и найдите код для функции, которую требуется изменить в рамках эксперимента. Добавьте код, который извлекает данные варианта, используйте их для изменения поведения тестируемой функции, а затем запишите событие просмотра для вашего эксперимента в журнал службы A/B-тестирования в Центре разработки.

Требуемый код зависит от приложения, но в следующем примере показан базовый процесс. Полный пример кода можно найти в разделе [Создание и проведение первого эксперимента с использованием A/B-тестирования](create-and-run-your-first-experiment-with-a-b-testing.md).

```CSharp
private StoreServicesExperimentVariation variation;
private StoreServicesCustomEventLogger logger;

// Assign this variable to the project ID for your experiment from Dev Center.
// The project ID shown below is for example purposes only.
private string projectId = "F48AC670-4472-4387-AB7D-D65B095153FB";

private async Task InitializeExperiment()
{
    // Get the current cached variation assignment for the experiment.
    var result = await StoreServicesExperimentVariation.GetCachedVariationAsync(projectId);
    variation = result.ExperimentVariation;

    // Refresh the cached variation assignment if necessary.
    if (result.ErrorCode != StoreServicesEngagementErrorCode.None || result.ExperimentVariation.IsStale)
    {
        result = await StoreServicesExperimentVariation.GetRefreshedVariationAsync(projectId);

        if (result.ErrorCode == StoreServicesEngagementErrorCode.None)
        {
            variation = result.ExperimentVariation;
        }
    }

    // Get the remote variable named "buttonText" and assign the value
    // to the button.
    var buttonText = variation.GetString("buttonText", "Grey Button");
    await button.Dispatcher.RunAsync(
        Windows.UI.Core.CoreDispatcherPriority.Normal,
        () =>
        {
            button.Content = buttonText;
        });

    // Log the view event named "userViewedButton" to Dev Center.
    if (logger == null)
    {
        logger = StoreServicesCustomEventLogger.GetDefault();
    }

    logger.LogForVariation(variation, "userViewedButton");
}
```

Ниже важные части этого процесса показаны более подробно.

1. Объявите объект [StoreServicesExperimentVariation](https://msdn.microsoft.com/library/windows/apps/microsoft.services.store.engagement.storeservicesexperimentvariation.aspx), представляющий собой текущее назначение вариантов, и объект [StoreServicesCustomEventLogger](https://msdn.microsoft.com/library/windows/apps/microsoft.services.store.engagement.storeservicescustomeventlogger.aspx), который вы будете использовать для ведения журнала событий просмотра и преобразований в Центре разработки.
  ```CSharp
  private StoreServicesExperimentVariation variation;
  private StoreServicesCustomEventLogger logger;
  ```

1. Объявите переменную типа string и присвойте в качестве значения [идентификатор проекта](run-app-experiments-with-a-b-testing.md#terms) для эксперимента, который вы хотите получить.
  >**Примечание.**&nbsp;&nbsp;Идентификатор проекта вы получаете при [создании проекта на панели мониторинга Центра разработки](create-a-project-and-define-remote-variables-in-the-dev-center-dashboard.md). Указанный ниже идентификатор проекта используется только в качестве примера.

  ```CSharp
  private string projectId = "F48AC670-4472-4387-AB7D-D65B095153FB";
  ```

2. Получите текущее назначение кэшированного варианта для эксперимента, вызвав статический метод [GetCachedVariationAsync](https://msdn.microsoft.com/library/windows/apps/microsoft.services.store.engagement.storeservicesexperimentvariation.getcachedvariationasync.aspx), и передайте методу идентификатор проекта для вашего эксперимента. Этот метод возвращает объект [StoreServicesExperimentVariationResult](https://msdn.microsoft.com/library/windows/apps/microsoft.services.store.engagement.storeservicesexperimentvariationresult.aspx), предоставляющий доступ к назначению варианта через свойство [ExperimentVariation](https://msdn.microsoft.com/library/windows/apps/microsoft.services.store.engagement.storeservicesexperimentvariationresult.experimentvariation.aspx).

   ```CSharp
   var result = await StoreServicesExperimentVariation.GetCachedVariationAsync(projectId);
   variation = result.ExperimentVariation;
   ```

4. Проверьте свойство [IsStale](https://msdn.microsoft.com/library/windows/apps/microsoft.services.store.engagement.storeservicesexperimentvariation.isstale.aspx), чтобы определить, требуется ли обновить назначение кэшированного варианта с помощью удаленного назначения варианта с сервера. Если необходимость в обновлении отсутствует, вызовите статический метод [GetRefreshedVariationAsync](https://msdn.microsoft.com/library/windows/apps/microsoft.services.store.engagement.storeservicesexperimentvariation.getrefreshedvariationasync.aspx), чтобы проверить наличие обновленного назначения варианта на сервере и обновить локальный кэшированный вариант.

  ```CSharp
  if (result.ErrorCode != StoreServicesEngagementErrorCode.None || result.ExperimentVariation.IsStale)
  {
        result = await StoreServicesExperimentVariation.GetRefreshedVariationAsync(projectId);

        if (result.ErrorCode == StoreServicesEngagementErrorCode.None)
        {
            variation = result.ExperimentVariation;
        }
  }
  ```

5. Используйте методы [GetBoolean](https://msdn.microsoft.com/library/windows/apps/microsoft.services.store.engagement.storeservicesexperimentvariation.getboolean.aspx), [GetDouble](https://msdn.microsoft.com/library/windows/apps/microsoft.services.store.engagement.storeservicesexperimentvariation.getdouble.aspx), [GetInt32](https://msdn.microsoft.com/library/windows/apps/microsoft.services.store.engagement.storeservicesexperimentvariation.getint32.aspx) или [GetString](https://msdn.microsoft.com/library/windows/apps/microsoft.services.store.engagement.storeservicesexperimentvariation.getstring.aspx) объекта [StoreServicesExperimentVariation](https://msdn.microsoft.com/library/windows/apps/microsoft.services.store.engagement.storeservicesexperimentvariation.aspx) для получения значений назначения варианта. В каждом методе первый параметр— это имя варианта, который требуется получить (имя, указанное вами на панели мониторинга в Центре разработки). Второй параметр— это значение по умолчанию, которое метод должен вернуть, если ему не удастся получить указанное значение из Центра разработки (например, если отсутствует подключение к сети), а кэшированная версия варианта недоступна.

  В следующем примере используется метод [GetString](https://msdn.microsoft.com/library/windows/apps/microsoft.services.store.engagement.storeservicesexperimentvariation.getstring.aspx), чтобы получить переменную с именем *buttonText*, а в качестве значения по умолчанию для варианта указано значение **Grey Button**.

  ```CSharp
  var buttonText = variation.GetString("buttonText", "Grey Button");
  ```

4. В своем коде используйте значения варианта для изменения поведения тестируемой функции. Например, в следующем коде содержимому кнопки назначается значение *buttonText* в приложении. В этом примере предполагается, что вы уже определили эту кнопку в другом месте в проекте.

  ```CSharp
  await button.Dispatcher.RunAsync(
      CoreDispatcherPriority.Normal,
      () =>
      {
          button.Content = buttonText;
      });
  ```

5. Наконец, запишите [событие просмотра](run-app-experiments-with-a-b-testing.md#terms) для эксперимента в журнал службы A/B-тестирования в Центре разработки. Инициализируйте поле ```logger``` как объект [StoreServicesCustomEventLogger](https://msdn.microsoft.com/library/windows/apps/microsoft.services.store.engagement.storeservicescustomeventlogger.aspx) и вызовите метод [LogForVariation](https://msdn.microsoft.com/library/windows/apps/microsoft.services.store.engagement.storeservicescustomeventlogger.logforvariation.aspx). Передайте объект [StoreServicesExperimentVariation](https://msdn.microsoft.com/library/windows/apps/microsoft.services.store.engagement.storeservicesexperimentvariation.aspx), представляющий текущее назначение варианта (этот объект направляет контекст события в Центр разработки) и имя события просмотра для вашего эксперимента. Оно должно соответствовать имени события просмотра, которое вы ввели для эксперимента на информационной панели Центра разработки. Код должен записать в журнал событие просмотра, когда пользователь начинает просмотр варианта, являющегося частью эксперимента.

  В следующем примере показано, как записать в журнал событие просмотра с именем **userViewedButton**. В этом примере цель эксперимента — заставить пользователя нажать кнопку в приложении, чтобы записать событие просмотра в журнал после того, как приложение получит данные о вариантах (в данном случае это текст кнопки) и сделает их содержимым кнопки.

  ```CSharp
  if (logger == null)
  {
      logger = StoreServicesCustomEventLogger.GetDefault();
  }

  logger.LogForVariation(variation, "userViewedButton");
  ```

## Запись в журнал событий преобразования в Центре разработки

Далее добавьте код, ведущий журнал [событий преобразования](run-app-experiments-with-a-b-testing.md#terms) в службе A/B-тестирования в Центре разработки. Код должен заносить в журнал событие преобразования, когда пользователь достигает цели эксперимента. В каждом случае требуемый код зависит от приложения, но есть несколько общих действий. Полный пример кода можно найти в разделе [Создание и проведение первого эксперимента с использованием A/B-тестирования](create-and-run-your-first-experiment-with-a-b-testing.md).

1. В коде, который выполняется, когда пользователь достигает цель одной из задач эксперимента, повторно вызовите метод [LogForVariation](https://msdn.microsoft.com/library/windows/apps/microsoft.services.store.engagement.storeservicescustomeventlogger.logforvariation.aspx) и передайте объект [StoreServicesExperimentVariation](https://msdn.microsoft.com/library/windows/apps/microsoft.services.store.engagement.storeservicesexperimentvariation.aspx) и имя события преобразования для эксперимента. Оно должно совпадать с одним из имен событий преобразования, который вы ввели для эксперимента на информационной панели Центра разработки должны совпадать.

  В следующем примере в журнал записывается событие преобразования с именем **userClickedButton** из обработчика события **Click** для кнопки. В этом примере цель эксперимента — заставить пользователя нажать кнопку.

  ```CSharp
  private void button_Click(object sender, RoutedEventArgs e)
  {
      if (logger == null)
      {
          logger = StoreServicesCustomEventLogger.GetDefault();
      }

      logger.LogForVariation(variation, "userClickedButton");
  }
  ```

## Дальнейшие действия

После создания кода для проведения эксперимента в приложении можно выполнять следующие действия.
1. [Определять эксперимент на панели мониторинга Центра разработки](define-your-experiment-in-the-dev-center-dashboard.md) (создание эксперимента, определяющего события просмотра и преобразования, а также уникальные варианты для A/B-теста).
2. [Запускать эксперимент и управлять им на панели мониторинга Центра разработки](manage-your-experiment.md).


## Связанные разделы

* [Создание проекта и определение удаленных переменных на панели мониторинга Центра разработки](create-a-project-and-define-remote-variables-in-the-dev-center-dashboard.md)
* [Определение эксперимента на панели мониторинга Центра разработки](define-your-experiment-in-the-dev-center-dashboard.md)
* [Управление экспериментом на панели мониторинга Центра разработки](manage-your-experiment.md)
* [Создание и запуск первого эксперимента с использованием A/B-тестирования](create-and-run-your-first-experiment-with-a-b-testing.md)
* [Выполнение экспериментов в приложении с использованием A/B-тестирования](run-app-experiments-with-a-b-testing.md)



<!--HONumber=Nov16_HO1-->



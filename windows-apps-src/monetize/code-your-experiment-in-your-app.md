---
author: mcleanbyron
Description: "Чтобы выполнить эксперимент в приложении универсальной платформы Windows (UWP) с использованием A/B-тестирования, необходимо создать код эксперимента в вашем приложении."
title: "Кодирование приложения для экспериментов"
ms.assetid: 6A5063E1-28CD-4087-A4FA-FBB511E9CED5
translationtype: Human Translation
ms.sourcegitcommit: 29a94fd14d11256ade28463c04abfec81287cf39
ms.openlocfilehash: e5de32dcc7b0694e72d9686b3b9a64de17a02277

---

# Кодирование приложения для экспериментов

После [создания проекта и определения удаленных переменных на панели мониторинга Центра разработки](create-a-project-and-define-remote-variables-in-the-dev-center-dashboard.md) можно приступить к обновлению кода вашего приложения универсальной платформы Windows (UWP) для реализации следующих целей:
* Получение значений удаленных переменных из Центра разработки для Windows.
* Использование удаленных переменных для настройки процесса взаимодействия пользователей с приложением.
* Ведение журнала событий в Центре разработки, содержащего сведения о том, когда пользователи просмотрели ваш эксперимент и выполнили соответствующее действие (также называемое *преобразованием*).

В следующих разделах описывается общий процесс получения вариантов для эксперимента и ведения журнала событий в Центре разработки. После завершения кодирования приложения для экспериментов можно [определить эксперимент на панели мониторинга Центра разработки](define-your-experiment-in-the-dev-center-dashboard.md). Пошаговое руководство, демонстрирующее весь процесс создания и выполнения эксперимента, можно найти в разделе [Создание и проведение первого эксперимента с использованием A/B-тестирования](create-and-run-your-first-experiment-with-a-b-testing.md).

## Настройка проекта

Сначала необходимо установить пакет Microsoft Store Services SDK на компьютер, на котором ведется разработка, и добавить в проект необходимые ссылки.

1. Установите пакет [Microsoft Store Services SDK](http://aka.ms/store-em-sdk).
2. Откройте проект в Visual Studio.
3. В обозревателе решений разверните узел проекта, щелкните правой кнопкой мыши **Ссылки**, а затем— **Добавить ссылку**.
3. В диалоговом окне **Диспетчер ссылок** разверните список **Универсальная платформа Windows** и выберите **Расширения**.
4. В списке пакетов SDK установите флажок рядом с пунктом **Microsoft Engagement Framework** и нажмите кнопку **ОК**.

## Добавление кода для получения данных о вариантах

Откройте проект и найдите код для функции, которую требуется изменить в рамках эксперимента. Добавьте код, позволяющий получить данные о вариантах, и используйте полученные данные для изменения поведения тестируемой функции. В каждом случае требуемый код зависит от приложения, но есть несколько общих действий. Полный пример кода можно найти в разделе [Создание и проведение первого эксперимента с использованием A/B-тестирования](create-and-run-your-first-experiment-with-a-b-testing.md).

1. Объявите объект [StoreServicesExperimentVariation](https://msdn.microsoft.com/library/windows/apps/microsoft.services.store.engagement.storeservicesexperimentvariation.aspx), представляющий собой текущее назначение вариантов, и объект [StoreServicesCustomEventLogger](https://msdn.microsoft.com/library/windows/apps/microsoft.services.store.engagement.storeservicescustomeventlogger.aspx), который вы будете использовать для ведения журнала событий просмотра и преобразований в Центре разработки.
```CSharp
private StoreServicesExperimentVariation variation;
private StoreServicesCustomEventLogger logger;
```

2. Получите текущее назначение кэшированного варианта для эксперимента, вызвав статический метод [GetCachedVariationAsync](https://msdn.microsoft.com/library/windows/apps/microsoft.services.store.engagement.storeservicesexperimentvariation.getcachedvariationasync.aspx), и передайте методу [идентификатор проекта](run-app-experiments-with-a-b-testing.md#terms) для вашего эксперимента. Этот метод возвращает объект [StoreServicesExperimentVariationResult](https://msdn.microsoft.com/library/windows/apps/microsoft.services.store.engagement.storeservicesexperimentvariationresult.aspx), предоставляющий доступ к назначению варианта через свойство [ExperimentVariation](https://msdn.microsoft.com/library/windows/apps/microsoft.services.store.engagement.storeservicesexperimentvariationresult.experimentvariation.aspx).
  >**Примечание.**&nbsp;&nbsp;Идентификатор проекта вы получаете при [создании проекта на панели мониторинга Центра разработки](create-a-project-and-define-remote-variables-in-the-dev-center-dashboard.md). Указанный ниже идентификатор проекта используется только в качестве примера.

  ```CSharp
var result = await StoreServicesExperimentVariation.GetCachedVariationAsync(
      "F48AC670-4472-4387-AB7D-D65B095153FB");
variation = result.ExperimentVariation;
```

4. Проверьте свойство [IsStale](https://msdn.microsoft.com/library/windows/apps/microsoft.services.store.engagement.storeservicesexperimentvariation.isstale.aspx), чтобы определить, требуется ли обновить назначение кэшированного варианта с помощью удаленного назначения варианта с сервера. Если необходимость в обновлении отсутствует, вызовите статический метод [GetRefreshedVariationAsync](https://msdn.microsoft.com/library/windows/apps/microsoft.services.store.engagement.storeservicesexperimentvariation.getrefreshedvariationasync.aspx), чтобы проверить наличие обновленного назначения варианта на сервере и обновить локальный кэшированный вариант.
```CSharp
// Check whether the cached variation assignment needs to be refreshed.
// If so, then refresh it.
if (result.ErrorCode != StoreServicesEngagementErrorCode.None || result.ExperimentVariation.IsStale)
{
      result = await StoreServicesExperimentVariation.GetRefreshedVariationAsync(projectId);

      // If the call succeeds, use the new result. Otherwise, use the cached value.
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
4. В своем коде используйте значения варианта для изменения поведения тестируемой функции. Например, в следующем коде содержимому кнопки назначается значение *buttonText*.
```CSharp
button.Content = buttonText;
```

## Добавление кода для ведения журнала событий просмотра и преобразования в Центре разработки

Далее добавьте код, ведущий [журнал событий просмотра](run-app-experiments-with-a-b-testing.md#terms) и [событий преобразования](run-app-experiments-with-a-b-testing.md#terms) в службе A/B-тестирования в Центре разработки. Этот код должен заносить в журнал событие просмотра, когда пользователь начинает просматривать вариант в составе вашего эксперимента, и событие преобразования, когда пользователь достигает цели эксперимента.

В каждом случае требуемый код зависит от приложения, но есть несколько общих действий. Полный пример кода можно найти в разделе [Создание и проведение первого эксперимента с использованием A/B-тестирования](create-and-run-your-first-experiment-with-a-b-testing.md).

1. В коде, который выполняется, когда пользователь начинает просматривать вариант, инициализируйте поле ```logger``` в объекте [StoreServicesCustomEventLogger](https://msdn.microsoft.com/library/windows/apps/microsoft.services.store.engagement.storeservicescustomeventlogger.aspx) и вызовите метод [LogForVariation](https://msdn.microsoft.com/library/windows/apps/microsoft.services.store.engagement.storeservicescustomeventlogger.logforvariation.aspx). Передайте объект [StoreServicesExperimentVariation](https://msdn.microsoft.com/library/windows/apps/microsoft.services.store.engagement.storeservicesexperimentvariation.aspx), представляющий собой текущее назначение варианта (этот объект направляет контекст события в Центр разработки), и имя события просмотра (указанное вами на панели мониторинга в Центре разработки). В следующем примере в журнал заносится событие просмотра с именем **userViewedButton**.

  ```CSharp
  if (logger == null)
  {
      logger = StoreServicesCustomEventLogger.GetDefault();
  }

  logger.LogForVariation(variation, "userViewedButton");
  ```

2. В коде, который выполняется, когда пользователь достигает цели одной из задач эксперимента, повторно вызовите метод [LogForVariation](https://msdn.microsoft.com/library/windows/apps/microsoft.services.store.engagement.storeservicescustomeventlogger.logforvariation.aspx) и передайте объект [StoreServicesExperimentVariation](https://msdn.microsoft.com/library/windows/apps/microsoft.services.store.engagement.storeservicesexperimentvariation.aspx) и имя события преобразования (указанное вами на панели мониторинга в Центре разработки). В следующем примере в журнал заносится событие преобразования с именем **userClickedButton**.
```CSharp
logger.LogForVariation(variation, "userClickedButton");
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



<!--HONumber=Sep16_HO1-->



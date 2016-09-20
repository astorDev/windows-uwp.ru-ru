---
author: mcleanbyron
Description: "После того как вы определили свой эксперимент на панели мониторинга в Центре разработки, вы можете приступить к написанию кода для проведения эксперимента в своем приложении."
title: "Создание кода для проведения эксперимента в приложении"
ms.assetid: 6A5063E1-28CD-4087-A4FA-FBB511E9CED5
ms.sourcegitcommit: d403e78b775af0f842ba2172295a09e35015dcc8
ms.openlocfilehash: 4e6706624e71c6d448a3d457c27d11c9f6ecc156

---

# Создание кода для проведения эксперимента в приложении

После того как вы [определили свой эксперимент на панели мониторинга в Центре разработки](define-your-experiment-in-the-dev-center-dashboard.md), вы можете приступить к обновлению кода своего приложения универсальной платформы Windows (UWP), чтобы получать данные о вариантах для эксперимента, использовать эти данные для изменения поведения тестируемой функции и ведения журнала событий просмотра и преобразования в Центре разработки.

В следующих разделах описывается общий процесс получения вариантов для эксперимента и ведения журнала событий в Центре разработки. Пошаговое руководство, демонстрирующее весь процесс создания и выполнения эксперимента можно найти в разделе [Создание и проведение первого эксперимента с использованием A/B-тестирования](create-and-run-your-first-experiment-with-a-b-testing.md).

## Настройка проекта

Для начала установите пакет SDK Microsoft Store Engagement and Monetization на компьютер, на котором ведется разработка, и добавьте в проект необходимые ссылки.

1. Установите пакет [Microsoft Store Engagement and Monetization SDK](http://aka.ms/store-em-sdk).
2. Откройте проект в Visual Studio.
3. В обозревателе решений разверните узел проекта, щелкните правой кнопкой мыши **Ссылки**, а затем— **Добавить ссылку**.
3. В диалоговом окне **Диспетчер ссылок** разверните список **Универсальная платформа Windows** и выберите **Расширения**.
4. В списке пакетов SDK выберите флажок рядом с пунктом **Microsoft Store Engagement SDK** и нажмите кнопку **ОК**.

## Добавление кода для получения параметров вариантов

Откройте проект и найдите код для функции, которую требуется изменить в рамках эксперимента. Добавьте код, позволяющий получить параметры варианта, и используйте полученные данные для изменения поведения тестируемой функции. В каждом случае требуемый код зависит от приложения, но есть несколько общих действий. Полный пример кода можно найти в разделе [Создание и проведение первого эксперимента с использованием A/B-тестирования](create-and-run-your-first-experiment-with-a-b-testing.md).

1. Объявите объект [ExperimentClient](https://msdn.microsoft.com/library/windows/apps/microsoft.services.store.engagement.experimentclient.aspx), с помощью которого вы будете получать варианты для эксперимента, и объект [ExperimentVariation](https://msdn.microsoft.com/library/windows/apps/microsoft.services.store.engagement.experimentvariation.aspx), представляющий текущее назначение вариантов.
```CSharp
private readonly ExperimentClient experiment;
private ExperimentVariation variation;
```

2. Инициализируйте объект [ExperimentClient](https://msdn.microsoft.com/library/windows/apps/microsoft.services.store.engagement.experimentclient.aspx) и передайте конструктору ключ API, полученный на странице **Эксперименты** панели мониторинга. Дополнительные сведения о ключе API можно найти в разделе [Определение эксперимента на панели мониторинга в Центре разработки](define-your-experiment-in-the-dev-center-dashboard.md#generate-an-api-key). Указанный ниже ключ API используется только в качестве примера.
```CSharp
experiment = new ExperimentClient("F48AC670-4472-4387-AB7D-D65B095153FB");
```

3. Получите текущее назначение кэшированного варианта для эксперимента, вызвав метод [GetVariationAsync](https://msdn.microsoft.com/library/windows/apps/microsoft.services.store.engagement.experimentclient.getvariationasync.aspx). Этот метод возвращает объект [ExperimentVariationResult](https://msdn.microsoft.com/library/windows/apps/microsoft.services.store.engagement.experimentvariationresult.aspx), предоставляющий доступ к назначению варианта через свойство [Variation](https://msdn.microsoft.com/library/windows/apps/microsoft.services.store.engagement.experimentvariationresult.variation.aspx).
```CSharp
ExperimentVariationResult result = await experiment.GetVariationAsync();
variation = result.Variation;
```

4. Проверьте свойство [NeedsRefresh](https://msdn.microsoft.com/library/windows/apps/microsoft.services.store.engagement.experimentvariation.needsrefresh.aspx), чтобы определить необходимость обновления назначения кэшированного варианта. Если оно не требует обновления, вызовите метод [RefreshVariationAsync](https://msdn.microsoft.com/library/windows/apps/microsoft.services.store.engagement.experimentclient.refreshvariationasync.aspx), чтобы проверить наличие обновленного назначения варианта на сервере и обновить локальный кэшированный вариант.
```CSharp
// Check whether the cached variation assignment needs to be refreshed.
// If so, then refresh it.
if (result.ErrorCode != EngagementErrorCode.Success || result.Variation.NeedsRefresh)
{
      result = await experiment.RefreshVariationAsync();

      // If the call succeeds, use the new result. Otherwise, use the
      // cached value we retrieved earlier.
      if (result.ErrorCode == EngagementErrorCode.Success)
      {
          variation = result.Variation;
      }
}
```

5. Используйте методы [GetBoolean](https://msdn.microsoft.com/library/windows/apps/microsoft.services.store.engagement.experimentvariation.getboolean.aspx), [GetDouble](https://msdn.microsoft.com/library/windows/apps/microsoft.services.store.engagement.experimentvariation.getdouble.aspx), [GetInteger](https://msdn.microsoft.com/library/windows/apps/microsoft.services.store.engagement.experimentvariation.getinteger.aspx) или [GetString](https://msdn.microsoft.com/library/windows/apps/microsoft.services.store.engagement.experimentvariation.getstring.aspx) объекта [ExperimentVariation](https://msdn.microsoft.com/library/windows/apps/microsoft.services.store.engagement.experimentvariation.aspx) для получения параметров назначения варианта. В каждом методе первый параметр— это имя параметра, который требуется получить (указанное вами на панели мониторинга в Центре разработки). Второй параметр— это значение по умолчанию, которое метод должен вернуть, если ему не удастся получить указанное значение из Центра разработки (например, если отсутствует подключение к сети), а кэшированная версия варианта недоступна.

  В следующем примере используется метод [GetString](https://msdn.microsoft.com/library/windows/apps/microsoft.services.store.engagement.experimentvariation.getstring.aspx), чтобы получить параметр с именем *buttonText*, а в качестве значения по умолчанию для параметра указано значение **Grey Button**.
```CSharp
var buttonText = currentVariation.GetString("buttonText", "Grey Button");
```
4. В своем коде используйте значения параметров для изменения поведения тестируемой функции. Например, в следующем коде содержимому кнопки назначается значение *buttonText*.
```CSharp
button.Content = buttonText;
```

## Добавление кода для ведения журнала событий просмотра и преобразования в Центре разработки

Далее добавьте код, ведущий журнал событий просмотра и преобразования в службе A/B-тестирования в Центре разработки. Этот код должен заносить в журнал событие просмотра, когда пользователь начинает просматривать вариант в составе вашего эксперимента, и событие преобразования, когда пользователь достигает цели эксперимента.

В каждом случае требуемый код зависит от приложения, но есть несколько общих действий. Полный пример кода можно найти в разделе [Создание и проведение первого эксперимента с использованием A/B-тестирования](create-and-run-your-first-experiment-with-a-b-testing.md).

1. В коде, который выполняется, когда пользователь начинает просматривать вариант, вызовите статический метод [Log](https://msdn.microsoft.com/library/windows/apps/microsoft.services.store.engagement.storeservicescustomevents.log.aspx) объекта [StoreServicesCustomEvents](https://msdn.microsoft.com/library/windows/apps/microsoft.services.store.engagement.storeservicescustomevents.aspx). Передайте имя события просмотра, определенное вами в эксперименте на панели мониторинга в Центр разработки, и объект [ExperimentVariation](https://msdn.microsoft.com/library/windows/apps/microsoft.services.store.engagement.experimentvariation.aspx), представляющий текущее назначение варианта (этот объект направляет контекст события в Центр разработки). В следующем примере в журнал заносится событие просмотра с именем **userViewedButton**.
```CSharp
StoreServicesCustomEvents.Log("userViewedButton", variation);
```
2. В коде, который выполняется, когда пользователь достигает цель одной из задач эксперимента, повторно вызовите метод [Log](https://msdn.microsoft.com/library/windows/apps/microsoft.services.store.engagement.storeservicescustomevents.log.aspx) и передайте имя события преобразования, определенное вами в эксперименте, и объект [ExperimentVariation](https://msdn.microsoft.com/library/windows/apps/microsoft.services.store.engagement.experimentvariation.aspx). В следующем примере в журнал заносится событие преобразования с именем **userClickedButton**.
```CSharp
StoreServicesCustomEvents.Log("userClickedButton", variation);
```

## Дальнейшие действия

После того как вы определили свой эксперимент на панели мониторинга в Центре разработки и написали код для проведения эксперимента в приложении, вы можете приступить к [запуску эксперимента на панели мониторинга в Центре разработки и управлению им](manage-your-experiment.md).

## Связанные разделы

  * [Определение эксперимента на панели мониторинга в Центре разработки](define-your-experiment-in-the-dev-center-dashboard.md)
  * [Управление экспериментом на панели мониторинга в Центре разработки](manage-your-experiment.md)
  * [Создание и запуск первого эксперимента с использованием A/B-тестирования](create-and-run-your-first-experiment-with-a-b-testing.md)
  * [Выполнение экспериментов в приложении с использованием A/B-тестирования](run-app-experiments-with-a-b-testing.md)



<!--HONumber=Jun16_HO4-->



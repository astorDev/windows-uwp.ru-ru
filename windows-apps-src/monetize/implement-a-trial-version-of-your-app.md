---
author: mcleanbyron
ms.assetid: 571697B7-6064-4C50-9A68-1374F2C3F931
description: "Узнайте, как использовать пространство имен Windows.Services.Store для предоставления пользователям пробной версии приложения."
title: "Реализация пробной версии приложения"
keywords: "пример кода бесплатной пробной версии"
translationtype: Human Translation
ms.sourcegitcommit: 18d5c2ecf7d438355c3103ad2aae32dc84fc89ed
ms.openlocfilehash: 8858c9f7f9b40e2bca30054b99ab47c7388aef57

---

# Внедрение пробной версии приложения

Если приложение настроено на [информационной панели Центра разработки для Windows в качестве бесплатной пробной версии](../publish/set-app-pricing-and-availability.md#free-trial) (то есть клиенты могут пользоваться им бесплатно в течение пробного периода), можно убедить клиентов выполнить обновление до полной версии приложения, исключив или ограничив определенные функции в течение пробного периода. До начала программирования решите, какие функции лучше ограничить, и сделайте так, чтобы они были доступны только после покупки полной лицензии. Вы можете также включить такие компоненты, как баннеры или водяные знаки, которые будут отображаться только во время испытательного срока, пока пользователь не купит приложение.

Приложения, предназначенные для Windows 10 версии 1607 или старше, могут использовать члены класса [StoreContext](https://msdn.microsoft.com/library/windows/apps/windows.services.store.storecontext.aspx) в пространстве имен [Windows.Services.Store](https://msdn.microsoft.com/library/windows/apps/windows.services.store.aspx), чтобы определять, есть ли у пользователя лицензия на пробную версию приложения, и получать уведомления в случае изменения состояния лицензии во время работы приложения.

>**Примечание.**&nbsp;&nbsp;Эта статья относится к приложениям, предназначенным для Windows 10 версии 1607 и старше. Если приложение предназначено для предыдущих версий Windows 10, необходимо использовать пространство имен [Windows.ApplicationModel.Store](https://msdn.microsoft.com/library/windows/apps/windows.applicationmodel.store.aspx), а не пространство имен **Windows.Services.Store**. Подробнее см. в разделе [Покупки из приложения и пробные версии, использующие пространство имен Windows.ApplicationModel.Store](in-app-purchases-and-trials-using-the-windows-applicationmodel-store-namespace.md).

## Рекомендации по реализации пробной версии

Текущее состояние лицензии приложения хранится в свойствах класса [StoreAppLicense](https://msdn.microsoft.com/library/windows/apps/windows.services.store.storeapplicense.aspx). Обычно функции, которые зависят от состояния лицензии, помещают в условный блок. Как это сделать, будет показано на следующем этапе. Разрабатывая эти функции, убедитесь, что они будут работать во всех состояниях лицензии.

Решите, каким образом вы будете обрабатывать изменения в лицензии приложения во время его работы. Ваше приложение может быть полнофункциональным, но в отличие от купленного иметь рекламные баннеры. Вы также можете запретить некоторые функции или регулярно показывать сообщения с предложением купить приложение.

Подумайте, какой способ ограничения функциональности больше всего подходит для вашего типа приложения. Для игры больше всего подойдет ограничение игрового контента, доступного пользователю. Для пробной версии служебной программы можно установить дату окончания пробного периода или ограничить некоторые функции, нужные потенциальному покупателю.

Для большинства неигровых приложений установка испытательного срока является удачным решением, так как пользователи смогут лучше разобраться в приложении в целом. Ниже приведено несколько распространенных сценариев окончания срока действия и варианты их обработки.

-   **Срок действия лицензии пробной версии истек во время работы приложения**

    Если срок действия лицензии пробной версии истек во время работы приложения, то можно:

    -   Не выполнять никаких действий.
    -   Вывести сообщение для клиента.
    -   Закрыть приложение.
    -   Предложить клиенту купить приложение.

    Рекомендуется вывести сообщение с предложением купить приложение и, если клиент его купит, включить все функции и продолжить работу. Если пользователь решит не покупать приложение, следует закрыть его или регулярно напоминать о покупке приложения.

-   **Срок действия лицензии пробной версии истек до запуска приложения**

    Если срок действия пробной версии окончится до того, как пользователь запустит программу, она не запустится. Вместо этого пользователи увидят диалоговое окно, с помощью которого они смогут приобрести ваше приложение в Магазине.

-   **Клиент покупает приложение во время его работы**

    Если клиент покупает приложение во время его работы, возможно несколько вариантов действий.

    -   Не выполнять никаких действий и предоставить клиенту возможность работать в режиме пробной версии до перезапуска приложения.
    -   Поблагодарить клиента за покупку или отобразить сообщение.
    -   Без предупреждения включить возможности, доступные в полной лицензионной версии (или отключить уведомления об использовании пробной версии).

Не забудьте объяснить, как будет работать ваше приложение во время и после бесплатного испытательного срока, чтобы поведение приложения не стало неожиданностью для клиентов. Дополнительные сведения об описании приложения см. в разделе [Создание описаний приложений](https://msdn.microsoft.com/library/windows/apps/mt148529).

## Необходимые условия

Для этого примера необходимо выполнение следующих предварительных условий:
* Создан проект Visual Studio для приложения универсальной платформы Windows (UWP), предназначенный для Windows 10 версии 1607 и выше.
* На информационной панели Центра разработки для Windows создано приложение, которое настроено как [бесплатная пробная версия](https://msdn.microsoft.com/windows/uwp/publish/set-app-pricing-and-availability) без ограничения времени, и это приложение опубликовано и доступно в Магазине. Это может быть приложение, которое вы хотите предложить пользователям, или базовое приложение, соответствующее минимальным требованиям [комплекта сертификации приложений для Windows](https://developer.microsoft.com/windows/develop/app-certification-kit) и используемое только для тестирования. Подробнее см. в [руководстве по тестированию](in-app-purchases-and-trials.md#testing).

В коде из этого примера предполагается следующее:
* Код выполняется в контексте страницы [Page](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.page.aspx), которая содержит [ProgressRing](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.progressring.aspx) с именем ```workingProgressRing``` и [TextBlock](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.textblock.aspx) с именем ```textBlock```. Эти объекты используются для индикации выполнения асинхронной операции и отображения выводимых сообщений, соответственно.
* Файл кода содержит оператор **using** для пространства имен **Windows.Services.Store**.
* Приложение — однопользовательское и выполняется только в контексте пользователя, запустившего его. Подробнее см. в разделе [Покупки из приложения и пробные версии](in-app-purchases-and-trials.md#api_intro).

>**Примечание.**&nbsp;&nbsp;Если у вас есть классическое приложение, которое использует [мост для настольных ПК](https://developer.microsoft.com/windows/bridges/desktop), вам может потребоваться добавить дополнительный код, не показанный в этом примере, для настройки объекта [StoreContext](https://msdn.microsoft.com/library/windows/apps/windows.services.store.storecontext.aspx). Дополнительные сведения см. в разделе [Использование класса StoreContext в классическом приложение, в котором применяется мост для настольных компьютеров](in-app-purchases-and-trials.md#desktop).

## Пример кода

При инициализации приложения получите для приложения объект [StoreAppLicense](https://msdn.microsoft.com/library/windows/apps/windows.services.store.storeapplicense.aspx) и обрабатывайте событие [OfflineLicensesChanged](https://msdn.microsoft.com/library/windows/apps/windows.services.store.storecontext.offlinelicenseschanged.aspx) для получения уведомлений об изменении лицензии во время работы приложения. Например, лицензия приложения может измениться, если закончился пробный период или пользователь приобрел приложение в Магазине. При изменении лицензии получите новую лицензию и соответственно включите или отключите функции приложения.

Если пользователь купил приложение, рекомендуется сообщить ему об изменении состояния лицензирования. При необходимости попросите пользователя перезапустить приложение. Перевод приложения в новое состояние лицензирования должен быть максимально комфортным для пользователя.


```csharp
private StoreContext context = null;
private StoreAppLicense appLicense = null;

// Call this while your app is initializing.
private async void InitializeLicense()
{
    if (context == null)
    {
        context = StoreContext.GetDefault();
        // If your app is a desktop app that uses the Desktop Bridge, you
        // may need additional code to configure the StoreContext object.
        // For more info, see https://aka.ms/storecontext-for-desktop.
    }

    workingProgressRing.IsActive = true;
    appLicense = await context.GetAppLicenseAsync();
    workingProgressRing.IsActive = false;

    // Register for the licenced changed event.
    context.OfflineLicensesChanged += context_OfflineLicensesChanged;
}

private async void context_OfflineLicensesChanged(StoreContext sender, object args)
{
    // Reload the license.
    workingProgressRing.IsActive = true;
    appLicense = await context.GetAppLicenseAsync();
    workingProgressRing.IsActive = false;

    if (appLicense.IsActive)
    {
        if (appLicense.IsTrial)
        {
            textBlock.Text = $"This is the trial version. Expiration date: {appLicense.ExpirationDate}";

            // Show the features that are available during trial only.
        }
        else
        {
            // Show the features that are available only with a full license.
        }
    }
}
```

Полный пример приложения см. в разделе [Пример для Магазина](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/Store).

## Связанные разделы

* [Покупки из приложения и пробные версии](in-app-purchases-and-trials.md)
* [Получение информации о продукте для приложений и надстроек](get-product-info-for-apps-and-add-ons.md)
* [Получение информации о лицензии для приложений и надстроек](get-license-info-for-apps-and-add-ons.md)
* [Поддержка покупок приложений и надстроек из приложения](enable-in-app-purchases-of-apps-and-add-ons.md)
* [Поддержка покупок потребляемых надстроек](enable-consumable-add-on-purchases.md)
* [Пример для Магазина](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/Store)



<!--HONumber=Nov16_HO1-->



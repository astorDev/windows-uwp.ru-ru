---
Description: Если вы разрешаете пользователям бесплатно пользоваться вашим приложением в течение пробного периода, можно побудить их приобрести полную версию приложения путем исключения или ограничения некоторых функций в течение пробного периода.
title: Исключение или ограничение функций в пробной версии
ms.assetid: 1B62318F-9EF5-432A-8593-F3E095CA7056
keywords: бесплатная пробная версия
keywords: бесплатный пробный период
keywords: пример кода бесплатной пробной версии
keywords: пример приложения с бесплатным пробным периодом
---

# Исключение или ограничение функций в пробной версии


\[ Обновлено для приложений UWP в Windows 10. Статьи о Windows 8.x см. в [архиве](http://go.microsoft.com/fwlink/p/?linkid=619132) \]

Если вы разрешаете пользователям бесплатно пользоваться вашим приложением в течение пробного периода, можно побудить их приобрести полную версию приложения путем исключения или ограничения некоторых функций в течение пробного периода. До начала программирования решите, какие функции лучше ограничить, и сделайте так, чтобы они были доступны только после покупки полной лицензии. Вы можете также включить такие компоненты, как баннеры или водяные знаки, которые будут отображаться только во время пробного периода, до того как пользователь купит приложение.

Посмотрим, как добавить такие возможности в приложение.

## Необходимые условия

Приложение для Windows, в которое предполагается добавить компоненты для продажи.

## Шаг 1. Выбор функций, которые вы хотите включить или выключить на время испытательного срока

Текущее состояние лицензии вашего приложения можно узнать из свойств класса [**LicenseInformation**](https://msdn.microsoft.com/library/windows/apps/br225157). Обычно функции, которые зависят от состояния лицензии, помещают в условный блок. Как это сделать, будет показано на следующем этапе. Разрабатывая эти функции, убедитесь, что они будут работать во всех состояниях лицензии.

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

Если вы хотите обнаруживать изменение лицензии и выполнять то или иное действие в приложении, нужно добавить обработчик событий для этого сценария, как показано на следующем этапе.
## Шаг 2. Инициализация информации о лицензии

При инициализации приложения получите для него объект [**LicenseInformation**](https://msdn.microsoft.com/library/windows/apps/br225157), как показано в следующем примере. Будем считать, что **licenseInformation** является глобальной переменной или полем типа **LicenseInformation**.

Инициализируйте объект [**CurrentApp**](https://msdn.microsoft.com/library/windows/apps/hh779765) или [**CurrentAppSimulator**](https://msdn.microsoft.com/library/windows/apps/hh779766), чтобы получить доступ к информации о лицензии.

```CSharp
void initializeLicense()
{
    // Initialize the license info for use in the app that is uploaded to the Store.
    // uncomment for release
    //   licenseInformation = CurrentApp.LicenseInformation;

    // Initialize the license info for testing.
    // comment the next line for release
    licenseInformation = CurrentAppSimulator.LicenseInformation;
      
}
```

Добавьте обработчик событий для получения уведомлений об изменении лицензии во время работы приложения. Лицензия приложения может изменяться — например, после окончания пробного периода или при покупке пользователем приложения в Магазине.

```CSharp
void InitializeLicense()
{
    // Initialize the license info for use in the app that is uploaded to the Store.
    // uncomment for release
    //   licenseInformation = CurrentApp.LicenseInformation;

    // Initialize the license info for testing.
    // comment the next line for release
    licenseInformation = CurrentAppSimulator.LicenseInformation;

    // Register for the license state change event.
     licenseInformation.LicenseChanged += new LicenseChangedEventHandler(licenseChangedEventHandler);

}

// ...

void licenseChangedEventHandler()
{
    ReloadLicense(); // code is in next steps
}
```

## Шаг 3. Написание кода компонентов в условных блоках

Когда возникает событие, связанное с изменением лицензии, ваше приложение должно вызвать API License, чтобы определить, изменился ли статус пробного периода. В примере кода показано, как организовать обработку этого события. Если пользователь купил приложение, рекомендуется сообщить ему об изменении состояния лицензирования. При необходимости попросите пользователя перезапустить приложение. Перевод приложения в новое состояние лицензирования должен быть максимально комфортным для пользователя.

В следующем примере показано, как оценить состояние лицензии приложения и провести соответствующее включение или выключение функции.

```CSharp
void ReloadLicense()
{
    if (licenseInformation.IsActive)
    {
         if (licenseInformation.IsTrial)
         {
             // Show the features that are available during trial only.
         }
         else
         {
             // Show the features that are available only with a full license.
         }
     }
     else
     {
         // A license is inactive only when there' s an error.
     }
}
```

## Шаг 4. Получение даты окончания срока действия пробной версии

Включите в приложение следующий код, определяющий дату истечения испытательного срока.

В этом примере приводится код функции, получающей дату окончания срока пробной версии приложения. Если лицензия еще действует, отображайте дату истечения испытательного срока и количество оставшихся до нее дней.

```CSharp
void DisplayTrialVersionExpirationTime()
{
    if (licenseInformation.IsActive)
    {
        if (licenseInformation.IsTrial)
        {
            var longDateFormat = new Windows.Globalization.DateTimeFormatting.DateTimeFormatter("longdate");
                                                
            // Display the expiration date using the DateTimeFormatter. 
            // For example, longDateFormat.Format(licenseInformation.ExpirationDate)

            var daysRemaining = (licenseInformation.ExpirationDate - DateTime.Now).Days;

            // Let the user know the number of days remaining before the feature expires
        }
        else
        {
            // ...
        }
    }
    else
    {
       // ...
    }
}
```

## Шаг 5. Проверка работы функций с использованием имитации вызовов лицензии API

Теперь можно протестировать ваше приложение, используя имитацию обращения к серверу лицензирования. В приложениях, написанных на JavaScript, C#, Visual Basic или Visual C++, замените все ссылки на [**CurrentApp**](https://msdn.microsoft.com/library/windows/apps/hh779765) в коде инициализации элементом [**CurrentAppSimulator**](https://msdn.microsoft.com/library/windows/apps/hh779766).

[**CurrentAppSimulator**](https://msdn.microsoft.com/library/windows/apps/hh779766) получает тестовую информацию о лицензии из XML-файла WindowsStoreProxy.xml, расположенного в папке %userprofile%\\AppData\\local\\packages\\&lt;package name&gt;\\LocalState\\Microsoft\\Windows Store\\ApiData. Если этот путь и файл не существуют, их нужно создать во время установки или выполнения приложения. Если файл WindowsStoreProxy.xml отсутствует в указанном выше расположении, при попытке обратиться к свойству [**CurrentAppSimulator.LicenseInformation**](https://msdn.microsoft.com/library/windows/apps/hh779768) возникнет ошибка.

В следующем примере показано, как можно добавить код в ваше приложение и проверить его работу в различных состояниях лицензирования.

```CSharp
void appInit()
{
    // some app initialization functions

    // Initialize the license info for use in the app that is uploaded to the Store.
    // uncomment for release
    //   licenseInformation = CurrentApp.LicenseInformation;

    // Initialize the license info for testing.
    // comment the next line for release
    licenseInformation = CurrentAppSimulator.LicenseInformation;

    // other app initialization functions
}
```

Файл WindowsStoreProxy.xml можно отредактировать, изменив даты истечения испытательного срока для вашего приложения и его компонентов. Протестируйте все возможные конфигурации лицензирования и испытательных сроков и убедитесь, что все работает как надо.

## Шаг 6. Замена сымитированных вызовов лицензии API на настоящие

После тестирования приложения с имитацией сервера лицензирования замените все [**CurrentAppSimulator**](https://msdn.microsoft.com/library/windows/apps/hh779766) элементами [**CurrentApp**](https://msdn.microsoft.com/library/windows/apps/hh779765), как показано в следующем примере кода. После этого вы можете отправить приложение в Магазин для сертификации.

**Важно!** Приложение, отправляемое в Магазин, должно использовать объект [**CurrentApp**](https://msdn.microsoft.com/library/windows/apps/hh779765), иначе оно не пройдет сертификацию.

```CSharp
void appInit()
{
    // some app initialization functions

    // Initialize the license info for use in the app that is uploaded to the Store.
    // uncomment for release
    licenseInformation = CurrentApp.LicenseInformation;

    // Initialize the license info for testing.
    // comment the next line for release
    //   licenseInformation = CurrentAppSimulator.LicenseInformation;

    // other app initialization functions
}
```

## Шаг 7. Описание работы с бесплатной пробной версией для пользователей

Не забудьте объяснить, как будет работать ваше приложение во время и после бесплатного испытательного срока, чтобы поведение приложения не стало неожиданностью для клиентов.

Дополнительные сведения об описании приложения см. в разделе [Создание описаний приложений](https://msdn.microsoft.com/library/windows/apps/mt148529).

## Связанные разделы

* [Пример для Магазина (демонстрация пробных версий и покупок из приложения)](http://go.microsoft.com/fwlink/p/?LinkID=627610)
* [Настройки цен и доступности приложений](https://msdn.microsoft.com/library/windows/apps/mt148548)
* [**CurrentApp**](https://msdn.microsoft.com/library/windows/apps/hh779765)
* [**CurrentAppSimulator**](https://msdn.microsoft.com/library/windows/apps/hh779766)
 

 






<!--HONumber=Mar16_HO1-->



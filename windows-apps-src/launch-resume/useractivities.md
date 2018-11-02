---
author: TylerMSFT
title: Продолжение активности пользователей даже на разных устройствах
description: В этом разделе рассказывается, как помочь пользователям возобновить свою активность в вашем приложении даже на нескольких устройствах.
keywords: активность пользователя, действия пользователя, временная шкала, кортана просмотр с места остановки, кортана продолжение работы, project rome
ms.author: twhitney
ms.date: 04/27/2018
ms.topic: article
ms.localizationpriority: medium
ms.openlocfilehash: 37b7ed35edecb95deb4b3ba568227f4cd8e32d9a
ms.sourcegitcommit: 70ab58b88d248de2332096b20dbd6a4643d137a4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/01/2018
ms.locfileid: "5927443"
---
# <a name="continue-user-activity-even-across-devices"></a>Продолжение активности пользователей даже на разных устройствах

В этом разделе рассказывается, как помочь пользователям возобновить свою активность в вашем приложении на своем ПК и на разных устройствах.

## <a name="user-activities-and-timeline"></a>Действия пользователя и временная шкала

В течение дня мы используем разные устройства. Мы пользуемся телефоном в автобусе, ПК в течение дня, а затем телефоном или планшетом в вечернее время. Начиная с Windows 10 сборки 1803 и в более поздних версиях создание [Активности пользователя](https://docs.microsoft.com/uwp/api/windows.applicationmodel.useractivities.useractivity) приводит к отображению этой активности на временной шкале Windows и в функции продолжения работы с места остановки в Кортане. Временная шкала — расширенное представление задач, использующее преимущества активности пользователя для отображения ваших действий в хронологическом порядке. Она также может включать ваши действия на разных устройствах.

![Временная шкала Windows](images/timeline.png)

Аналогичным образом, привязка телефона к ПК под управлением Windows позволит продолжить работу на устройствах с Android или iOS.

Рассматривайте **UserActivity** как конкретное занятие пользователя в вашем приложении. Например, если вы используете средство чтения RSS, **UserActivity** может обозначать канал, который вы читаете. Если вы играете в игру, **UserActivity** может быть уровнем, на котором вы находитесь. Если вы слушаете музыку в приложении, **UserActivity** может обозначать список воспроизведения, который вы прослушиваете. Если вы работаете с документом, **UserActivity** может быть местом, где вы приостановили работу, и т. п.  Одним словом, **UserActivity** соответствует назначению в приложении и позволяет пользователю возобновить свои действия.

При обращении к **UserActivity** путем вызова метод [UserActivity.CreateSession](https://docs.microsoft.com/uwp/api/windows.applicationmodel.useractivities.useractivity.createsession) система создает запись в журнале, указывающую время начала и окончания для этого **UserActivity**. По мере повторных обращений к **UserActivity** в журнал добавляются новые записи.

## <a name="add-user-activities-to-your-app"></a>Добавление действий пользователя в приложение

[UserActivity](https://docs.microsoft.com/uwp/api/windows.applicationmodel.useractivities.useractivity) — это единица взаимодействия с пользователем в Windows. Она состоит из трех частей: URI, используемый для активации приложения, к которому относится действие, визуальные элементы и метаданные, которые описывают действие.

1. [ActivationUri](https://docs.microsoft.com/uwp/api/windows.applicationmodel.useractivities.useractivity.activationuri#Windows_ApplicationModel_UserActivities_UserActivity_ActivationUri) используется, чтобы возобновить работу приложения в определенном контексте. Как правило, эта ссылка принимает форму обработчика протокола для схемы (например, my-app://page2?action=edit) или AppUriHandler (например, http://constoso.com/page2?action=edit).
2. [VisualElements](https://docs.microsoft.com/uwp/api/windows.applicationmodel.useractivities.useractivity.visualelements) предоставляет класс, который позволяет пользователю визуально распознать действие с помощью названия, описания или элементов адаптивной карты.
3. Наконец, [Содержимое](https://docs.microsoft.com/uwp/api/windows.applicationmodel.useractivities.useractivityvisualelements.content#Windows_ApplicationModel_UserActivities_UserActivityVisualElements_Content) — объект для хранения метаданных для этого действия, которые могут использоваться для группировки и получения действий в определенном контексте. Зачастую оно принимает форму данных [http://schema.org](http://schema.org).

Добавление **UserActivity** в приложение:

1. Создайте объекты **UserActivity** при изменении контекста пользователя в приложении (например, навигация по странице, новый уровень игры и т. п.)
2. Заполните объекты **UserActivity** минимальным набором обязательных полей: [ActivityId](https://docs.microsoft.com/uwp/api/windows.applicationmodel.useractivities.useractivity.activityid#Windows_ApplicationModel_UserActivities_UserActivity_ActivityId), [ActivationUri](https://docs.microsoft.com/uwp/api/windows.applicationmodel.useractivities.useractivity.activationuri) и [UserActivity.VisualElements.DisplayText](https://docs.microsoft.com/uwp/api/windows.applicationmodel.useractivities.useractivityvisualelements.displaytext#Windows_ApplicationModel_UserActivities_UserActivityVisualElements_DisplayText).
3. Добавьте собственный обработчик схемы для вашего приложения, чтобы его можно было повторно активировать с помощью **UserActivity**.

**UserActivity** можно интегрировать в приложение с помощью нескольких строк кода. Например, предположим, что этот код расположен в MainPage.xaml.cs, в классе MainPage (обратите внимание: предполагается `using Windows.ApplicationModel.UserActivities;`):

```csharp
UserActivitySession _currentActivity;
private async Task GenerateActivityAsync()
{
    // Get the default UserActivityChannel and query it for our UserActivity. If the activity doesn't exist, one is created.
    UserActivityChannel channel = UserActivityChannel.GetDefault();
    UserActivity userActivity = await channel.GetOrCreateUserActivityAsync("MainPage");
 
    // Populate required properties
    userActivity.VisualElements.DisplayText = "Hello Activities";
    userActivity.ActivationUri = new Uri("my-app://page2?action=edit");
     
    //Save
    await userActivity.SaveAsync(); //save the new metadata
 
    // Dispose of any current UserActivitySession, and create a new one.
    _currentActivity?.Dispose();
    _currentActivity = userActivity.CreateSession();
}
```

В первой строке указанного выше метода `GenerateActivityAsync()` происходит получение [UserActivityChannel](https://docs.microsoft.com/uwp/api/windows.applicationmodel.useractivities.useractivitychannel) пользователя. Это канал, в котором будут публиковаться действия в приложении. Следующая строка запрашивает канал действия под названием `MainPage`.

* Приложение должно именовать действия таким образом, чтобы при входе пользователя в определенное расположение в приложении создавался тот же идентификатор. Например, если ваше приложение имеет страничную структуру, используйте идентификатор для страницы, а если документную, используйте имя документа (или хэш имени).
* При наличии в канале действия с тем же идентификатором это действие будет возвращено из канала с заданным для `UserActivity.State` значением [Published](https://docs.microsoft.com/uwp/api/windows.applicationmodel.useractivities.useractivitystate)). Если действий с таким же именем нет, новое действие возвращается с заданным для `UserActivity.State` значением **New**.
* Действия относятся к приложению. Вам не нужно беспокоиться о конфликтах идентификатора действия с идентификаторами в других приложениях.

После получения или создания **UserActivity** задайте два других обязательных поля: `UserActivity.VisualElements.DisplayText` и `UserActivity.ActivationUri`.

Затем сохраните метаданные **UserActivity**, вызвав [SaveAsync](https://docs.microsoft.com/uwp/api/windows.applicationmodel.useractivities.useractivity.saveasync), и, наконец, [CreateSession](https://docs.microsoft.com/uwp/api/windows.applicationmodel.useractivities.useractivity.createsession), что возвращает [UserActivitySession](https://docs.microsoft.com/uwp/api/windows.applicationmodel.useractivities.useractivitysession). **UserActivitySession** — это объект, который можно использовать для управления, когда пользователь фактически взаимодействует с **UserActivity**. Например, мы вызываем `Dispose()` для **UserActivitySession**, когда пользователь покидает страницу. В приведенном выше примере мы также вызываем `Dispose()` для `_currentActivity` перед вызовом `CreateSession()`. Это обусловлено тем, что мы сделали `_currentActivity` полем-участником нашей страницы и хотим остановить все выполняемые действия перед запуском нового (обратите внимание: `?` — [условный оператор null](https://docs.microsoft.com/en-us/dotnet/csharp/language-reference/operators/null-conditional-operators), который проверяет на null, прежде чем выполнять доступ к элементу).

Поскольку в этом случае `ActivationUri` является нестандартной схемой, необходима также регистрация протокола в манифесте приложения. Это делается в XML-файле Package.appmanifest или с помощью конструктора.

Чтобы внести изменения с помощью конструктора, дважды щелкните файл Package.appmanifest в проекте для запуска конструктора, выберите вкладку **Объявления** и добавьте определение **Протокола**. Единственным свойством, которое должно быть заполнено в данный момент, является **Имя**. Оно должно соответствовать URI, указанному выше, `my-app`.

Теперь нам необходимо написать код, чтобы сообщить приложению, что делать, когда оно активируется при помощи протокола. Мы переопределим метод `OnActivated` в App.xaml.cs для передачи URI на главную страницу следующим образом:

```csharp
protected override void OnActivated(IActivatedEventArgs e)
{
    if (e.Kind == ActivationKind.Protocol)
    {
        var uriArgs = e as ProtocolActivatedEventArgs;
        if (uriArgs != null)
        {
            if (uriArgs.Uri.Host == "page2")
            {
                // Navigate to the 2nd page of the  app
            }
        }
    }
    Window.Current.Activate();
}
```

Что этот код делает — определяет, было ли приложение активировано при помощи протокола. Если это так, затем он ищет, что приложение должно сделать для возобновления задачи, ради которой оно активировано. Поскольку наше приложение простое, единственное возобновляемое действие — переход ко второй странице при активации приложения.

## <a name="use-adaptive-cards-to-improve-the-timeline-experience"></a>Используйте адаптивные карты для улучшения работы с временной шкалой

Действия пользователя отображаются в Кортане и на временной шкале. Когда действия появляются на временной шкале, мы отображаем их с помощью структуры [адаптивной карты](http://adaptivecards.io/). Если вы не предоставляете адаптивную карту для каждого действия, временная шкала автоматически создаст карту простого действия на основании имени и значка вашего приложения, поля заголовка и необязательного описания. Ниже приведен пример полезных данных адаптивной карты и получаемая с их помощью карта.

![Адаптивная карта](images/adaptivecard.png)]

Пример строки JSON с полезными данными адаптивной карты:

```json
{ 
  "$schema": "http://adaptivecards.io/schemas/adaptive-card.json", 
  "type": "AdaptiveCard", 
  "version": "1.0",
  "backgroundImage": "https://winblogs.azureedge.net/win/2017/11/eb5d872c743f8f54b957ff3f5ef3066b.jpg", 
  "body": [ 
    { 
      "type": "Container", 
      "items": [ 
        { 
          "type": "TextBlock", 
          "text": "Windows Blog", 
          "weight": "bolder", 
          "size": "large", 
          "wrap": true, 
          "maxLines": 3 
        }, 
        { 
          "type": "TextBlock", 
          "text": "Training Haiti’s radiologists: St. Louis doctor takes her teaching global", 
          "size": "default", 
          "wrap": true, 
          "maxLines": 3 
        } 
      ] 
    } 
  ]
}
```

Добавьте полезные данные адаптивной карты как строку JSON в **UserActivity** следующим образом:

```csharp
activity.VisualElements.Content = 
Windows.UI.Shell.AdaptiveCardBuilder.CreateAdaptiveCardFromJson(jsonCardText); // where jsonCardText is a JSON string that represents the card
```

## <a name="cross-platform-and-service-to-service-integration"></a>Интеграция между различными платформами и службами

Если ваше приложение работает на разных платформах (например, на Android и iOS) или хранит данные состояния пользователя в облаке, можно опубликовать UserActivities через [Microsoft Graph](https://developer.microsoft.com/graph/).
После проверки подлинности вашего приложения или службы с помощью учетной записи Майкрософт требуется всего два простых вызова REST для создания объектов [Действие](https://developer.microsoft.com/graph/docs/api-reference/beta/api/projectrome_put_activity) и [Журнал](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/projectrome_historyitem) с использованием тех же данных, что и выше.

## <a name="summary"></a>Краткий обзор

Можно использовать API [UserActivity](https://docs.microsoft.com/uwp/api/windows.applicationmodel.useractivities), чтобы ваше приложение отображалось на временной шкале и в Кортане.
* Подробные сведения о API **UserActivity** можно найти в [Центре разработки для Windows](https://docs.microsoft.com/uwp/api/windows.applicationmodel.useractivities).
* Ознакомьтесь с [примером кода](https://github.com/Microsoft/project-rome).
* Посмотрите [более сложные адаптивные карты](http://adaptivecards.io/).
* Опубликуйте **UserActivity** с iOS, Android или через веб-службу с помощью [Microsoft Graph](https://developer.microsoft.com/graph/).
* Узнайте больше о [Project Rome на GitHub](https://github.com/Microsoft/project-rome).

## <a name="key-apis"></a>Основные API-интерфейсы

* [Пространство имен UserActivities](https://docs.microsoft.com/uwp/api/windows.applicationmodel.useractivities)

## <a name="related-topics"></a>Смежные разделы

* [Действия пользователей (документы Project Rome)](https://docs.microsoft.com/windows/project-rome/user-activities/)
* [Адаптивные карты](https://docs.microsoft.com/adaptive-cards/)
* [Визуализатор адаптивных карт, примеры](http://adaptivecards.io/)
* [Обработка активации универсального кода ресурса (URI)](https://docs.microsoft.com/windows/uwp/launch-resume/handle-uri-activation)
* [Взаимодействие с пользователями на всех платформах с помощью Microsoft Graph, ленты действий и адаптивных карт](https://channel9.msdn.com/Events/Connect/2017/B111)
* [Microsoft Graph](https://developer.microsoft.com/graph/)
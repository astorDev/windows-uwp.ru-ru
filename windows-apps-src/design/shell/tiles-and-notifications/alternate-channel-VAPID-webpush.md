---
title: Альтернативные каналы push-уведомлений с использованием ВАПИД в UWP
description: Инструкции по использованию альтернативных каналов push-уведомлений с помощью протокола ВАПИД из приложения Windows
ms.date: 01/10/2017
ms.topic: article
keywords: Windows 10, UWP, API WinRT, WNS
localizationpriority: medium
ms.openlocfilehash: 382dca376e2393d83c2803043b61db76226b3995
ms.sourcegitcommit: 0dee502484df798a0595ac1fe7fb7d0f5a982821
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/08/2020
ms.locfileid: "82970869"
---
# <a name="alternate-push-channels-using-vapid-in-windows"></a>Альтернативные каналы push-уведомлений с помощью ВАПИД в Windows 
Начиная с обновления "Создатели", приложения для Windows могут использовать проверку подлинности ВАПИД для отправки push-уведомлений.  

> [!NOTE]
> Эти API предназначены для веб-браузеров, в которых размещаются другие веб-сайты и создаются каналы от их имени.  Если вы хотите добавить в веб-приложение уведомления для отправки сообщений через Интернет, мы рекомендуем использовать стандарты W3C и Вхатвг для создания работника службы и отправки уведомления.

## <a name="introduction"></a>Введение
Введение стандарта push-уведомлений через Интернет позволяет веб-сайтам работать больше, как приложения, отправляя уведомления, даже если пользователи не находятся на веб-сайте.

Протокол проверки подлинности ВАПИД был создан, чтобы разрешить веб-сайтам проходить проверку подлинности с помощью принудительных серверов независимо от поставщика. Все поставщики, использующие протокол ВАПИД, могут отправлять push-уведомления, не зная браузера, на котором он работает. Это значительное улучшение по сравнению с реализацией другого протокола push-уведомлений для каждой платформы. 

Приложения Windows могут использовать ВАПИД для отправки push-уведомлений с этими преимуществами. Эти протоколы могут сэкономить время разработки новых приложений и упростить межплатформенную поддержку существующих приложений. Кроме того, корпоративные приложения и приложения загруженные неопубликованные теперь могут отправлять уведомления без регистрации в Microsoft Store. Надеюсь, в этом случае будут открыты новые способы взаимодействия с пользователями на всех платформах.  

## <a name="alternate-channels"></a>Альтернативные каналы 
В UWP эти ВАПИД каналы называются альтернативными каналами и предоставляют аналогичные функции каналу веб-Push. Они могут запускать фоновую задачу приложения, включать шифрование сообщений и разрешать использование нескольких каналов из одного приложения. Дополнительные сведения о разнице между различными типами каналов см. [в разделе Выбор правильного канала](channel-types.md).

Использование альтернативных каналов — это отличный способ получить доступ к PUSH-уведомлениям, если ваше приложение не может использовать первичный канал или если вы хотите использовать код совместно с вашим сайтом и приложением. Настройка канала очень проста и знакома всем, кто либо использовал стандарт веб-Push, либо работал в Windows Push Notifications раньше.

## <a name="code-example"></a>Пример кода

Основной процесс настройки альтернативного канала для приложения Windows аналогичен настройке первичного или вторичного канала. Сначала зарегистрируйте канал на [сервере WNS](windows-push-notification-services--wns--overview.md). Затем зарегистрируйте для запуска в качестве фоновой задачи. После отправки уведомления и запуска фоновой задачи обработайте событие.  

### <a name="get-a-channel"></a>Получение канала 
Чтобы создать альтернативный канал, приложение должно предоставить два фрагмента информации: открытый ключ для сервера и имя создаваемого канала. Сведения о ключах сервера доступны в спецификации push-уведомлений в Интернете, но для создания ключей мы рекомендуем использовать стандартную веб-библиотеку принудительной отправки на сервере.  

Идентификатор канала особенно важен, так как приложение может создавать несколько альтернативных каналов. Каждый канал должен идентифицироваться по уникальному ИДЕНТИФИКАТОРу, который будет включаться в любые полезные данные уведомлений, отправляемые по этому каналу.  

```csharp
private async void AppCreateVAPIDChannelAsync(string appChannelId, IBuffer applicationServerKey) 
{ 
    // From the spec: applicationServer Key (p256dh):  
    //               An Elliptic curve Diffie–Hellman public key on the P-256 curve 
    //               (that is, the NIST secp256r1 elliptic curve).   
    //               The resulting key is an uncompressed point in ANSI X9.62 format             
    // ChannelId is an app provided value for it to identify the channel later.  
    // case of this app it is from the set { "Football", "News", "Baseball" } 
    PushNotificationChannel webChannel = await PushNotificationChannelManager.GetDefault().CreateRawPushNotificationChannelWithAlternateKeyForApplicationAsync(applicationServerKey, appChannelId); 
 
    //Save the channel  
    AppUpdateChannelMapping(appChannelId, webChannel); 
             
    //Tell our web service that we have a new channel to push notifications to 
    AppPassChannelToSite(webChannel.Uri); 
} 
```
Приложение отправляет канал обратно на сервер и сохраняет его локально. Сохранение идентификатора канала локально позволяет приложению различать каналы и обновлять каналы, чтобы предотвратить их истечение.

Как и в случае с любым другим типом канала push-уведомлений, срок их действия может истечь. Чтобы предотвратить истечение срока действия каналов без уведомления вашего приложения, создайте новый канал при каждом запуске приложения.    

### <a name="register-for-a-background-task"></a>Регистрация для фоновой задачи 

Когда приложение создаст альтернативный канал, оно должно зарегистрироваться для получения уведомлений на переднем плане или в фоновом режиме. В приведенном ниже примере регистрируется использование модели с одним процессом для получения уведомлений в фоновом режиме.  

```csharp
var builder = new BackgroundTaskBuilder(); 
builder.Name = "Push Trigger"; 
builder.SetTrigger(new PushNotificationTrigger()); 
BackgroundTaskRegistration task = builder.Register(); 
```
### <a name="receive-the-notifications"></a>Получение уведомлений 

После регистрации приложения для получения уведомлений необходимо иметь возможность обрабатывать входящие уведомления. Так как одно приложение может зарегистрировать несколько каналов, перед обработкой уведомления обязательно проверьте идентификатор канала.  

```csharp
protected override void OnBackgroundActivated(BackgroundActivatedEventArgs args) 
{ 
    base.OnBackgroundActivated(args); 
    var raw = args.TaskInstance.TriggerDetails as RawNotification; 
    switch (raw.ChannelId) 
    { 
        case "Football": 
            AppPostFootballScore(raw.Content); 
            break; 
        case "News": 
            AppProcessNewsItem(raw.Content); 
            break; 
        case "Baseball": 
            AppProcessBaseball(raw.Content); 
            break; 
 
        default: 
            //We don't have the channelID registered, should only happen in the case of a 
            //caching issue on the application server 
            break; 
    }                           
} 
```

Обратите внимание, что если уведомление поступает от основного канала, идентификатор канала не будет установлен.  

## <a name="note-on-encryption"></a>Примечание о шифровании 

Вы можете использовать любую схему шифрования, которая лучше всего подходит для вашего приложения. В некоторых случаях достаточно использовать стандарт TLS между сервером и любым устройством Windows. В других случаях может быть целесообразно использовать схему шифрования веб-Push или другую схему разработки.  

Если вы хотите использовать другую форму шифрования, ключом является использование необработанных данных. Свойство Headers. Он содержит все заголовки шифрования, включенные в запрос POST на принудительный сервер. После этого приложение сможет использовать ключи для расшифровки сообщения.  

## <a name="related-topics"></a>Связанные темы
- [Типы каналов уведомлений](channel-types.md)
- [Службы push-уведомлений Windows (WNS)](windows-push-notification-services--wns--overview.md)
- [Класс Пушнотификатиончаннел](https://docs.microsoft.com/uwp/api/windows.networking.pushnotifications.pushnotificationchannel)
- [Класс Пушнотификатиончаннелманажер](https://docs.microsoft.com/uwp/api/windows.networking.pushnotifications.pushnotificationchannelmanager)



---
title: Каналы альтернативный Push-уведомлений с помощью Webpush и VAPID в универсальной платформы Windows
description: Инструкции по использованию принудительной альтернативные каналы с протоколом VAPID из приложения UWP
ms.date: 01/10/2017
ms.topic: article
keywords: Windows 10, uwp, WinRT API, WNS
localizationpriority: medium
ms.openlocfilehash: ba8630a2e877345adeac7eb443dd3e418d3ed277
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57639529"
---
# <a name="alternate-push-channels-using-webpush-and-vapid-in-uwp"></a>Каналы альтернативный Push-уведомлений с помощью Webpush и VAPID в универсальной платформы Windows 
Начиная с Fall Creators Update, приложений универсальной платформы Windows можно использовать web Push-уведомлений с помощью VAPID проверки подлинности для отправки Push-уведомлений.  

## <a name="introduction"></a>Введение
Общие сведения о принудительной стандарта веб позволяет веб-сайтов могут действовать более похоже приложений, отправку уведомлений, даже в том случае, если пользователи не являются веб-сайта.

Протокол проверки подлинности VAPID был создан запретить веб-сайтам для проверки подлинности с серверами Push-уведомлений в поставщика узлам. Со всеми поставщиками по протоколу VAPID веб-сайтов можно отправлять извещающие уведомления, не зная браузера, на котором он выполняется. Это значительное улучшение по сравнению с реализации протокола различных Push-уведомлений для каждой платформы. 

Приложения универсальной платформы Windows можно использовать webpush и VAPID для отправки Push-уведомлений с помощью этих преимуществ. Эти протоколы можно сократить время разработки для новых приложений и упрощения поддержки кроссплатформенности для существующих приложений. Кроме того корпоративные приложения или загруженные неопубликованные приложения теперь можно отправлять уведомления без регистрации в Microsoft Store. Надеюсь это открывает новые возможности взаимодействовать с пользователями на всех платформах.  

## <a name="alternate-channels"></a>Альтернативные каналы 
В универсальной платформы Windows эти каналы VAPID называются альтернативные каналы и предоставляют аналогичные функциональные возможности в web канал Push-уведомлений. Можно активировать приложение фоновой задачи для запуска, включить шифрование сообщений, а также разрешить несколько каналов из одного приложения. Дополнительные сведения о различиях между различные типы каналов, см. в разделе [выбрав нужный канал](channel-types.md).

Использование альтернативного каналов — отличный способ доступа к Push-уведомления, если приложение не может использовать основной канал, или если вы хотите совместное использование кода на веб-сайт или приложение. Настройка канала является простым и понятным всем, кто используемых стандарте web Push-уведомлений или работали с Windows Push-уведомлений, прежде чем.

## <a name="code-example"></a>Пример кода

Базовый процесс настройки канала для приложения универсальной платформы Windows с помощью альтернативного аналогична настройке канала первичной или вторичной. Во-первых, зарегистрируйтесь для канал с [WNS сервера](windows-push-notification-services--wns--overview.md). Затем зарегистрируете для запуска в качестве фоновой задачи. После отправки уведомления и активации фоновой задачи, обработайте событие.  

### <a name="get-a-channel"></a>Получение канала 
Чтобы создать альтернативный канал, приложение необходимо предоставить два фрагмента информации: открытый ключ для своего сервера и имя канала, он создает. Подробные сведения о ключах server доступны в спецификации web Push-уведомлений, но мы рекомендуем использовать библиотеку принудительной стандартные веб-сайтов на сервере для создания ключей.  

Идентификатор канала особенно важна, так как приложение может создавать несколько альтернативных каналов. Каждый канал должен быть идентифицирован уникальный идентификатор, который будет включен в все полезные данные уведомлений, отправляемых по этому каналу.  

```csharp
private async void AppCreateVAPIDChannelAsync(string appChannelId, IBuffer applicationServerKey) 
{ 
    // From the spec: applicationServer Key (p256dh):  
    //               An Elliptic curve Diffie–Hellman public key on the P-256 curve 
    //               (that is, the NIST secp256r1 elliptic curve).   
    //               The resulting key is an uncompressed point in ANSI X9.62 format             
    // ChannelId is an app provided value for it to identify the channel later.  
    // case of this app it is from the set { "Football", "News", "Baseball" } 
    PushNotificationChannel webChannel = await PushNotificationChannelManager.Current.CreateRawPushNotificationChannelWithAlternateKeyForApplicationAsync(applicationServerKey, appChannelId); 
 
    //Save the channel  
    AppUpdateChannelMapping(appChannelId, webChannel); 
             
    //Tell our web service that we have a new channel to push notifications to 
    AppPassChannelToSite(webChannel.Uri); 
} 
```
Приложение отправляет канала резервное копирование на свой сервер и сохраняет их локально. Сохранение идентификатора канала локально позволяет приложению различать между каналами и обновлять каналов, чтобы предотвратить истечение срока действия.

Как и любого другого типа канал Push-уведомлений просрочены, веб-каналов. Чтобы предотвратить истечение срока действия без ведома пользователя приложения каналы, создайте новый канал, каждый раз при запуске приложения.    

### <a name="register-for-a-background-task"></a>Регистрация для фоновой задачи 

После создания канала с помощью альтернативного приложения его необходимо зарегистрировать для получения уведомлений, либо в обычном или фоновом режиме. В приведенном ниже примере регистрируется для использования модели один process для получения уведомлений в фоновом режиме.  

```csharp
var builder = new BackgroundTaskBuilder(); 
builder.Name = "Push Trigger"; 
builder.SetTrigger(new PushNotificationTrigger()); 
BackgroundTaskRegistration task = builder.Register(); 
```
### <a name="receive-the-notifications"></a>Получать уведомления 

После регистрации для получения уведомлений приложение ему необходимо иметь возможность обрабатывать входящие уведомления. Так как одно приложение может зарегистрировать несколько каналов, не забудьте проверить идентификатор канала перед обработкой уведомление.  

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

Обратите внимание, что если уведомление поступает из основного канала, затем идентификатор канала не устанавливается.  

## <a name="note-on-encryption"></a>Обратите внимание на шифрование 

Можно использовать независимо от схемы шифрования окажутся более удобными для вашего приложения. В некоторых случаях достаточно полагаться на стандарте TLS между сервером и любого устройства Windows. В других случаях может быть целесообразнее использовать схему шифрования web Push-уведомлений или другую схему компонентов.  

Если вы хотите использовать другой тип шифрования, ключом является использование необработанного. Свойство Headers. Он содержит все заголовки шифрования, которые были включены в запрос POST для Push-сервера. После этого приложение может использовать ключи для расшифровки сообщения.  

## <a name="related-topics"></a>Статьи по теме
- [Типы каналов уведомлений](channel-types.md)
- [Службы Push-уведомлений Windows (WNS)](windows-push-notification-services--wns--overview.md)
- [Класс PushNotificationChannel](https://docs.microsoft.com/uwp/api/windows.networking.pushnotifications.pushnotificationchannel)
- [Класс PushNotificationChannelManager](https://docs.microsoft.com/uwp/api/windows.networking.pushnotifications.pushnotificationchannelmanager)



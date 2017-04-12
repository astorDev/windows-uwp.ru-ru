---
author: QuinnRadich
title: "Что нового в предварительной версии Windows 10 (версия 1607)"
description: "Windows 10 предварительной версии (версия 1607) и новые средства разработчика обеспечивают доступ к средствам, компонентам и возможностям универсальной платформы Windows."
keywords: "новые возможности, что нового, обновление, обновления, компоненты, новые, Windows 10, предварительная версия 1607"
ms.author: quradic
ms.date: 02/08/2017
ms.topic: article
ms.prod: windows
ms.technology: uwp
ms.assetid: 835d5393-427f-4155-a737-d509ea1de99f
ms.openlocfilehash: 7e37846e7696213093e84d7409c6900e0aad5ed5
ms.sourcegitcommit: 909d859a0f11981a8d1beac0da35f779786a6889
translationtype: HT
---
# <a name="whats-new-in-windows"></a>Что нового в Windows

Windows 10 предварительной версии (версия 1607) и обновления средств разработчика Windows обеспечивают доступ к средствам, компонентам и возможностям универсальной платформы Windows. [Установив средства и пакет SDK](http://go.microsoft.com/fwlink/?LinkId=821431) в Windows 10, вы можете сразу приступить либо к [созданию нового проекта универсального приложения для Windows](https://msdn.microsoft.com/library/windows/apps/bg124288), либо к использованию [существующего кода приложения в ОС Windows](https://msdn.microsoft.com/library/windows/apps/mt238321).

## <a name="windows-10-version-1607-preview"></a>Windows 10 предварительной версии 1607

Компонент | Описание
 :---- | :----
Сеть | Теперь вы можете реализовать собственную настраиваемую проверку серверных сертификатов SSL/TLS, подписавшись на событие [HttpBaseProtocolFilter.ServerCustomValidationRequest](https://msdn.microsoft.com/library/windows/apps/windows.web.http.filters.httpbaseprotocolfilter.aspx#_blank). Вы также можете полностью отключить чтение ответов HTTP из кэша, задав в HTTP-запросе значение перечисления [HttpCacheReadBehavior.NoCache](https://msdn.microsoft.com/library/windows/apps/windows.web.http.filters.httpcachereadbehavior.aspx#_blank). Теперь можно очистить учетные данные проверки подлинности для выхода из система с помощью метода [HttpBaseProtocolFilter.ClearAuthenticationCache](https://msdn.microsoft.com/library/windows/apps/windows.web.http.filters.httpbaseprotocolfilter.aspx#_blank).
Расширения | Теперь в Microsoft Edge можно использовать расширения. С их помощью пользователи могут расширить возможности Microsoft Edge, предоставляя специализированные функции для определенной целевой аудитории. Подробнее см. в [документации для расширений](https://developer.microsoft.com/microsoft-edge/platform/documentation/extensions/#_blank).
API-интерфейсы Bluetooth | Теперь приложения могут получать доступ к службам RFCOMM на удаленных периферийных устройствах Bluetooth с помощью [Windows.Devices.Bluetooth and Windows.Devices.Bluetooth.Rfcomm](https://msdn.microsoft.com/library/windows/apps/windows.devices.bluetooth.aspx#_blank) без связывания с устройством. Новые методы позволяют находить и использовать службы RFCOMM на устройствах без связывания с ними.
API чатов | С помощью нового класса [ChatSyncManager](https://msdn.microsoft.com/library/windows/apps/mt414181.aspx#_blank) вы можете синхронизировать текстовые сообщения с облаком.
[Сопоставление концепции приложений для Windows для разработчиков iOS и Android](https://msdn.microsoft.com/windows/uwp/porting/android-ios-uwp-map#_blank) | Если вы являетесь разработчиком с навыками разработки или готовым кодом для Android и iOS и хотите перейти на Windows 10 и универсальную платформу Windows (UWP), этот ресурс содержит всю необходимую информацию для сопоставления функций— и ваших знаний— между тремя платформами.
[Защита корпоративных данных (EDP)](https://msdn.microsoft.com/windows/uwp/enterprise/wip-hub) | EDP — это набор функций для настольных компьютеров, ноутбуков, планшетов и телефонов для решений управления мобильными устройствами (MDM). EDP предоставляет предприятиям расширенные возможности контроля над обработкой данных (корпоративных файлов и BLOB-объектов) на устройствах, которыми управляет предприятие.
[Windows.ApplicationModel.AppExtensions](https://msdn.microsoft.com/library/windows/apps/windows.applicationmodel.appextensions.aspx#_blank) | Новое пространство имен AppExtensions позволяет приложению Магазина Windows размещать содержимое, предоставляемое другими приложениями Магазина Windows. Вы можете искать, перечислять содержимое только для чтения из этих приложений, а также получать к нему доступ.
Windows IoT | Операционная система Windows 10 IoT Core позволяет создавать приложения для Интернета вещей в знакомой среде Windows. Кроме того, теперь она доступна на новейших платах семейства Raspberry Pi — Raspberry Pi 3.
API мультимедиа | Новые API-интерфейсы MediaBreak в пространстве имен Windows.Media.Playback позволяют легко планировать и контролировать перерывы при воспроизведении мультимедиа с помощью MediaPlaybackItem и MediaSource. Используя новые API-интерфейсы AudioGraph в пространстве имен Windows.Media.Audio, вы получите возможности для пространственной обработки звука и сможете назначать узлам аудиографа источники звука и слушателей с трехмерным расположением.
API карт | Теперь [MapControl](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.maps.mapcontrol.aspx#_blank) позволяет разработчикам получить визуальную область, которая находится рядом с камерой, исключая области на большом расстоянии и на горизонте в сложных представлениях. С помощью расширенного класса [MapLocationFinder](https://msdn.microsoft.com/library/windows/apps/windows.services.maps.maplocationfinder.aspx#_blank) разработчики могут оптимизировать сетевой трафик при обратном геокодировании, указывая требуемую точность. Разработчики могут загружать автономные карты, используя метод [LaunchUriAsync](https://msdn.microsoft.com/library/windows/apps/hh701480.aspx#_blank) и задавая широту и долготу. Дополнительные сведения см. в разделе [Запуск приложения «Карты Windows»](https://msdn.microsoft.com/windows/uwp/launch-resume/launch-maps-app#_blank).

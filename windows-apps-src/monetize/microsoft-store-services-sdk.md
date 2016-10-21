---
author: mcleanbyron
Description: "Пакет Microsoft Store Services SDK предоставляет библиотеки и средства, которые вы можете использовать для добавления в приложения функций, помогающие заработать и привлечь пользователей."
title: Microsoft Store Services SDK
ms.assetid: 518516DB-70A7-49C4-B3B6-CD8A98320B9C
translationtype: Human Translation
ms.sourcegitcommit: 2f0835638f330de0ac2d17dae28347686cc7ed97
ms.openlocfilehash: 98675e9cb06b05e55d49ca625818626aea5a5346

---

# Microsoft Store Services SDK

Пакет Microsoft Store Services SDK предоставляет библиотеки и средства, которые помогают заработать и привлекать пользователей в приложения универсальной платформы Windows (UWP), например показ рекламы в приложениях и эксперименты с A/B-тестированием. Со временем в этом пакете SDK будут появляться новые функции привлечения и монетизации.


## Возможности, доступные в SDK

Пакет Microsoft Store Services SDK предоставляет библиотеки и средства, которые поддерживают следующие возможности.

### Проведение экспериментов с A/B-тестированием приложений UWP

Проводите A/B- тесты в приложениях универсальной платформы Windows, чтобы оценить эффективность функций на некоторых пользователях перед их выпуском для всех пользователей. После определения эксперимента в информационной панели Центра разработки используйте класс [StoreServicesExperimentVariation](https://msdn.microsoft.com/library/windows/apps/microsoft.services.store.engagement.storeservicesexperimentvariation.aspx), чтобы получить варианты эксперимента. Воспользуйтесь этими данными, чтобы изменить поведение тестируемой функции, а затем вызовите метод [LogForVariation](https://msdn.microsoft.com/library/windows/apps/microsoft.services.store.engagement.storeservicescustomeventlogger.logforvariation.aspx), чтобы отправить событие просмотра и события преобразования в Центр разработки. Наконец, используйте информационную панель для просмотра результатов и управления экспериментом.

Дополнительные сведения можно см. в разделе [Проведение экспериментов с использованием A/B-тестирования](run-app-experiments-with-a-b-testing.md).

### Отзывы о приложениях UWP

Используйте класс [StoreServicesFeedbackLauncher](https://msdn.microsoft.com/library/windows/apps/microsoft.services.store.engagement.storeservicesfeedbacklauncher.aspx) в своих приложениях UWP, чтобы направлять пользователей Windows 10 в Центр отзывов, где они смогут сообщать о проблемах, делиться предложениями и голосовать за комментарии других пользователей. Затем вы сможете проанализировать все эти данные в [отчете об отзывах](../publish/feedback-report.md) на информационной панели Центра разработки.

Дополнительные сведения см. в статье [Запуск Центра отзывов из приложения](launch-feedback-hub-from-your-app.md).

### Показ рекламы в приложениях

Повышайте свой доход, отображая в приложении рекламные баннеры или промежуточную видеорекламу от Майкрософт в приложения UWP. Вы также можете увеличить отношение показов к попыткам с помощью рекламного посредника для показа объявлений нескольких рекламных сетей.

Дополнительные сведения см. в статье [Показ рекламы в приложениях](display-ads-in-your-app.md).

>**Примечание.**&nbsp;&nbsp;Microsoft Store Services SDK поддерживает только приложения UWP. Чтобы показывать рекламу в приложениях Windows 8.1 и Windows Phone 8.x, следуйте инструкциям из раздела [Microsoft Advertising SDK для Windows и Windows Phone 8.x](http://aka.ms/store-8-sdk).

### Справочник по API

Справочную документацию по API-интерфейсам в пакете Microsoft Store Services SDK см. в [справочнике по API пакета Microsoft Store Services SDK](https://msdn.microsoft.com/library/windows/apps/mt691886.aspx).

## Установка SDK

Чтобы установить пакет Microsoft Store Services SDK, выполните следующие действия:

1.  Закройте все экземпляры Visual Studio 2013 или Visual Studio 2015 и удалите предыдущие версии Microsoft Store Engagement and Monetization SDK, Universal Ad Client SDK, расширения рекламного посредника или Microsoft Advertising SDK.
2.  Скачайте и установите пакет [SDK](http://aka.ms/store-em-sdk). Установка может занять несколько минут. Обязательно дождитесь завершения процесса.
3.  Перезапустите VisualStudio.

Корпорация Майкрософт периодически выпускает новые версии пакета Microsoft Store Services SDK для повышения производительности и расширения возможностей. Если в ваших существующих проектах используется пакет Microsoft Store Services SDK и вы хотите использовать последнюю версию, просто скачайте и установите последнюю версию SDK.

Возможности показа рекламы в приложениях UWP из предыдущих выпусков пакета Microsoft Store Engagement and Monetization SDK, Universal Ad Client SDK, расширения рекламного посредника и пакета Microsoft Advertising SDK теперь включены в пакет Microsoft Store Services SDK. Если у вас есть существующие проекты UWP, которые используют возможности рекламы предыдущих выпусков, вы можете продолжить работу с ними без изменений после установки пакета Microsoft Store Services SDK.

>**Примечание.** Для установки пакета Microsoft Store Services SDK с помощью VisualStudio2015 должны быть установлены инструментыVisualStudio для универсальных приложений для Windows версии 1.1 или более поздней. Дополнительные сведений об этом обновлении инструментов Visual Studio для универсальных приложений для Windows см. в [заметках о выпуске](http://go.microsoft.com/fwlink/?LinkID=624516).

## Пакеты платформы в SDK

Следующие библиотеки в пакете Microsoft Store Services SDK настроены как *пакеты платформы*:

* Microsoft.Advertising.dll. Эта библиотека содержит API-интерфейсы рекламы в пространствах имен [Microsoft.Advertising](https://msdn.microsoft.com/en-us/library/windows/apps/mt313187.aspx) и [Microsoft.Advertising.WinRT.UI](https://msdn.microsoft.com/en-us/library/windows/apps/microsoft.advertising.winrt.ui.aspx).
* Microsoft.Services.Store.Engagement.dll. Эта библиотека содержит API-интерфейсы в пространстве имен [Microsoft.Services.Store.Engagement](https://msdn.microsoft.com/en-us/library/windows/apps/microsoft.services.store.engagement.aspx).

Это означает, что после установки SDK на компьютере разработчика, эти библиотеки будут автоматически обновляться через Центр обновления Windows после публикации новых версий библиотек с исправлениями и улучшениями производительности. Это позволяет гарантировать, что на компьютере разработчика вам всегда будет доступна последняя версия библиотек.

Кроме того, после установки пользователем версии вашего приложения, которое применяет эти библиотеки, они также будет автоматически обновляться на устройстве пользователя, когда мы опубликуем их новые версии с исправлениями и улучшениями производительности. Это означает, что пользователям всегда будут доступна последняя текущая версии библиотек, а вам не потребуется публиковать обновленные версии приложения в Магазине.

Но если мы выпустим новую версию SDK с новыми API-интерфейсами или функциями в этих библиотеках, вам придется установить последнюю версию пакета SDK, чтобы использовать их. В этом случае вам также понадобится опубликовать обновленное приложение в Магазине.

## Связанные статьи

* [Справочник по API пакета Microsoft Store Services SDK](https://msdn.microsoft.com/library/windows/apps/mt691886.aspx)
* [Проведение экспериментов с использованием A/B-тестирования](run-app-experiments-with-a-b-testing.md)
* [Запуск Центра отзывов из приложения](launch-feedback-hub-from-your-app.md)
* [Показ рекламы в приложении](display-ads-in-your-app.md)



<!--HONumber=Sep16_HO2-->



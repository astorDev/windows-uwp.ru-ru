---
title: Приложения UWP для Xbox One
description: Вы узнаете, как создавать приложения универсальной платформы Windows (UWP) для Xbox One.
ms.date: 10/25/2017
ms.topic: article
keywords: windows 10, uwp
ms.assetid: 2d935f53-84db-4108-86dc-cb6a0749782f
ms.localizationpriority: medium
ms.openlocfilehash: aecb0a6e6a7d9dfbe05a43b760b044d032fff4e1
ms.sourcegitcommit: 26bb75084b9d2d2b4a76d4aa131066e8da716679
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/06/2020
ms.locfileid: "75685035"
---
# <a name="uwp-on-xbox-one"></a>Приложения UWP для Xbox One

Приступите к созданию приложений универсальной платформы Windows (UWP) для Xbox One.

Платформа UWP для Xbox One поддерживает разработку как приложений, так и игр. Вам не нужно участвовать в программе для разработчиков, чтобы экспериментировать, создавать и тестировать игры или приложения для Xbox. Все, что вам нужно — [учетная запись разработчика](https://developer.microsoft.com/store/register) в [Центре партнеров](https://partner.microsoft.com/dashboard). Когда вы будете готовы публиковать и продавать игры для Xbox One или использовать Xbox Live в Windows 10, вам нужно будет присоединиться к [Xbox Live Creators Program](https://developer.microsoft.com/games/xbox/xboxlive/creator) или стать разработчиком [ID@Xbox](https://www.xbox.com/Developers/id). Если вы планируете стать разработчиком для ID@Xbox, перед регистрацией учетной записи разработчика мы советуем подать заявку на участие в программе. Дополнительные сведения см. в статье [Developer program overview](https://docs.microsoft.com/gaming/xbox-live/developer-program-overview) (Обзор программы разработчика).

В этом разделе содержится пошаговая инструкция по настройке системы и процедуре аутентификации, информация об установке необходимых версий Visual Studio и средств Windows 10, а также руководство по созданию, запуску и отладке вашего первого простого приложения. 

| Раздел      | Описание |
|------------|-------------|
|[Начало работы](getting-started.md)| Руководство по началу разработки приложений UWP для Xbox One. |
|[Новые возможности](whats-new.md)| Новые возможности в UWP для Xbox One. |
|[Активация режима разработчика Xbox One](devkit-activation.md)| В этой статье описано, как включить режим разработчика в Xbox One. |
|[Отключение режима разработчика в Xbox One](devkit-deactivation.md)| В этой статье описано, как отключить режим разработчика в Xbox One. |
|[Настройка среды разработки UWP для Xbox](development-environment-setup.md)| В этом разделе описано, как настраивать и тестировать среду разработки Xbox One. |
|[Примеры](samples.md)| Указатель на местоположение GitHub (TVHelpers), где вы найдете полезные примеры XAML и JavaScript, чтобы начать разработку для Xbox. В этих примерах представлен полноценный шаблон мультимедийного приложения XAML, а также описаны процессы автоматической навигации с помощью контроллера, воспроизведения мультимедиа и поиска веб-технологий. |
|[Известные проблемы](known-issues.md)| Известные проблемы с UWP для Xbox One. |
|[Вопросы и ответы](frequently-asked-questions.md)| Вопросы и ответы, связанные с приложениями UWP для Xbox One. |
|[Инструменты](introduction-to-xbox-tools.md)| В разделе описывается инструмент для Xbox One под названием _Dev Home_, а также показано, как использовать портал устройств Windows и настраивать Visual Studio для разработки. Также в разделе даются рекомендации для начинающих разработчиков по созданию приложений UWP для Xbox и показано, как использовать инструмент Fiddler для просмотра данных о сетевом трафике. |
| [Мероприятие "Разработка приложений на Xbox"](https://developer.microsoft.com/windows/projects/campaigns/app-dev-on-xbox-event) | Мероприятие "Разработка приложений на Xbox" — превосходная отправная точка для разработчиков, которые хотят создавать приложения для Xbox. Посмотрите записи сеансов и прочитайте блоги о мероприятии. |
|[Проектирование для Xbox и ТВ](../design/devices/designing-for-tv.md)| В этом разделе даются рекомендации по проектированию приложения для просмотра на ТВ, ввод в котором будет осуществляться с помощью контроллера. |
|[Рекомендации для Xbox](tailoring-for-xbox.md)| Выключение режима мыши, рисование на краях экрана и отключение масштабирования. |
|[Использование голосовых функций для вызова элементов пользовательского интерфейса](ves-on-xbox.md)| Рекомендации для поддержки оболочки с поддержкой голосовых функций в приложениях UWP на Xbox. |
|[Системные ресурсы для приложений UWP и игр для Xbox One](system-resource-allocation.md)| В этом разделе описаны ресурсы, доступные для приложения при запуске на Xbox One. |
|[Вводные сведения о многопользовательских приложениях](multi-user-applications.md)| В этом разделе описаны многопользовательские приложения для Xbox One. |
| [Автоматизация задач разработки для Xbox One](https://github.com/Microsoft/WindowsDevicePortalWrapper/tree/v0.9.4) | В проекте WindowsDevicePortalWrapper на GitHub предоставлена библиотека, позволяющая автоматизировать распространенные задачи разработки, такие как развертывание и запуск приложений. Проект содержит пример XboxWdpDriver.exe, который показывает, как использовать интерфейсы API для распространенных задач. |
|[Перенос имеющихся игр на Xbox](development-lanes-landing.md)|В зависимости от технологии, лежащей в основе вашей игры, мы можем предоставить вам пошаговые инструкции по ускорению переноса вашей игры на Xbox с помощью UWP.|
|[Функции UWP, которые еще не поддерживаются в Xbox One](https://docs.microsoft.com/uwp/extension-sdks/uwp-limitations-on-xbox?redirectedfrom=MSDN)|  В этом разделе описаны функциональные области UWP, которые еще не полностью реализованы в Xbox One.|

## <a name="videos"></a>Видео

Следующие презентации на канале Channel 9 являются источником полезной информации о создании великолепных приложений для Xbox.

* [Building Great Universal Windows Platform (UWP) Apps for Xbox](https://channel9.msdn.com/Events/Build/2016/B883) (Создание отличных приложений универсальной платформы Windows (UWP) для Xbox)
* [Adapt Your App for Xbox One and TV](https://channel9.msdn.com/Events/Build/2016/T651-R1) (Адаптация приложения для Xbox One и телевизоров)
* [UWP Development 1: Building an Adaptive UI](https://channel9.msdn.com/Events/Build/2016/L724-R1) (Разработка UWP 1. Создание адаптивного пользовательского интерфейса)
* [Web Apps Beyond the Browser: Cross-Platform Meets Cross Device](https://channel9.msdn.com/Events/Build/2016/B888) (Веб-приложения не только для браузеров. Различные платформы и устройства)

## <a name="see-also"></a>См. также статью

- [Automate launching Windows 10 UWP apps](automate-launching-uwp-apps.md) (Автоматизация запуска приложений UWP для Windows 10)
- [CPUSets for game development](cpusets-games.md) (CPUSets для разработки игр)
- [Progressive Web Apps for Xbox One](https://docs.microsoft.com/microsoft-edge/progressive-web-apps/xbox-considerations) (Прогрессивные веб-приложения для Xbox One)
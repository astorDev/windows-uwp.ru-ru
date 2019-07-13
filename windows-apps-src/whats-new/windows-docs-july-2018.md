---
title: Дополнение документации по Windows в июле 2018 г. — разработка приложений UWP
description: В июле 2018 г документация для разработчиков Windows 10 пополнилась описанием новых возможностей, видеоматериалами, примерами и руководствами для разработчиков.
keywords: what's new, update, features, developer guidance, Windows 10, july
ms.date: 07/11/2018
ms.topic: article
ms.localizationpriority: medium
ms.openlocfilehash: 4030796e942c76ccf32e04dbf951cac68718a6a5
ms.sourcegitcommit: 6f32604876ed480e8238c86101366a8d106c7d4e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/21/2019
ms.locfileid: "67320387"
---
# <a name="whats-new-in-the-windows-developer-docs-in-july-2018"></a>Дополнение документации для разработчиков Windows в июле 2018 г.

Документация для разработчиков Windows непрерывно обновляется и дополняется информацией о новых функциях, доступных разработчикам на платформе Windows. В июле появились приведенные ниже обзоры функций, руководства для разработчиков, видео и примеры.

[Установив средства и пакет SDK](https://go.microsoft.com/fwlink/?LinkId=821431) в Windows 10, вы можете сразу приступить либо к [созданию нового проекта универсального приложения для Windows](../get-started/create-uwp-apps.md) либо к использованию [существующего кода приложения в ОС Windows](../porting/index.md).

## <a name="features"></a>Возможности

### <a name="progressive-web-apps-on-windows"></a>Прогрессивные веб-приложения в Windows

[Прогрессивные веб-приложения (PWA)](https://developer.microsoft.com/windows/pwa) — это обычные веб-приложения, которые [постепенно дополняются](https://www.wikipedia.org/wiki/Progressive_enhancement) функциями (например, запуск с главного экрана, поддержка в автономный режиме и push-уведомления), аналогичными доступным в собственных приложениях, на поддерживаемых платформах и в браузерных модулях. В Windows 10 с модулем Microsoft Edge (EdgeHTML) приложения PWA обладают дополнительным преимуществом — запуск [независимо от окна браузера, как у приложений UWP](https://docs.microsoft.com/microsoft-edge/progressive-web-apps/windows-features).

![Изображение приложения PWA в действии](images/progressive-web-apps.jpg)

Ознакомьтесь с нашими руководствами по PWA:

* [Создание простого веб-приложения как приложения PWA](https://docs.microsoft.com/microsoft-edge/progressive-web-apps/get-started)
* [Улучшение приложения PWA с помощью среды выполнения Windows ](https://docs.microsoft.com/en-us/microsoft-edge/progressive-web-apps/windows-features)
* [Публикация приложения PWA в Microsoft Store](https://docs.microsoft.com/microsoft-edge/progressive-web-apps/microsoft-store)

### <a name="notepad"></a>Блокнот

В Windows 10 Insider Preview сборки 17713 в [Блокнот добавлено множество новых функций](https://aka.ms/ant-man). Теперь [участникам программы предварительной оценки Windows](https://insider.windows.com/) доступны функции масштабирования, поиска и замены с обтеканием и поддержка окончания строк для Unix/Linux (LF) и Mac (CR). 

## <a name="developer-guidance"></a>Руководство для разработчиков

### <a name="design-landing-page"></a>Оформление целевой страницы

См. [обновленную статью об оформлении целевой страницы](https://developer.microsoft.com/windows/apps/design), в которой содержится краткий обзор областей оформления UWP и информация о последних дополнениях к Fluent Design.

### <a name="design-toolkits"></a>Наборы инструментов для проектирования

В наборы инструментов Adobe XD и Adobe Illustrator добавлены новые функции. Эти наборы инструментов для проектирования предоставляют элементы управления и макеты для проектирования приложений UWP. [Сведения о них см. здесь.](../design/downloads/index.md)

### <a name="webvr"></a>WebVR

Мы добавили несколько разделов в [документацию по WebVR ](https://docs.microsoft.com/microsoft-edge/webvr/):

* [Что такое WebVR?](https://docs.microsoft.com/microsoft-edge/webvr/what-is-webvr) Здесь объясняется, что такое WebVR, почему его следует использовать и как приступить к разработке с его помощью.

* [WebVR в прогрессивных веб-приложениях](https://docs.microsoft.com/microsoft-edge/webvr/webvr-in-pwas). Здесь содержатся сведения о добавлении WebVR для прогрессивных веб-приложений.

* [WebVR в WebView](https://docs.microsoft.com/microsoft-edge/webvr/webvr-in-webview). В этом разделе содержатся сведения о добавлении WebVR для элемента управления WebView в приложении для Windows 10.

* [Демонстрационные ролики WebVR](https://docs.microsoft.com/microsoft-edge/webvr/demos). С помощью этого раздела вы можете ознакомиться с некоторыми демонстрационными роликами WebVR, используя Microsoft Edge и иммерсивную гарнитуру Windows Mixed Reality.

Также мы внесли некоторые изменения в существующие страницы.

* Улучшено оглавление. Теперь оно упорядочено по четырем различным основным группам: **основы**, **разработка**, **ресурсы** и **ролики**.

* [Руководство для разработчиков WebVR (целевая страница)](https://docs.microsoft.com/microsoft-edge/webvr/). Обновлен внешний вид: добавлены изображения и значки большего размера, а также и новый ролик.

* [Использование WebVR с Microsoft Edge](https://docs.microsoft.com/microsoft-edge/webvr/webvr-with-edge). Добавлена информация об обновлении Windows 10 за апрель 2018 г.

## <a name="videos"></a>Видео

### <a name="get-started-for-devs-create-and-customize-a-form-on-windows-10"></a>Начало работы для разработчиков. Создание и настройка формы для системы Windows 10.

[Документация по началу работы](../get-started/index.md) для разработчиков Windows позволяет получить практический опыт по работе с базовой задачей разработки приложений. В этом видео рассматривается один из этих разделов и основы создания формы пользовательского интерфейса в приложении. [Просмотрите видео](https://www.youtube.com/watch?v=AgngKzq4hKI&feature=youtu.be) о коде в действии, а затем [просмотрите раздел самостоятельно](https://aka.ms/CreateForms).

### <a name="enhance-your-bot-with-project-personality-chat"></a>Улучшения бота с помощью Project Personality Chat

Project Personality Chat позволяет добавлять настраиваемого пользователя в чат-боты. Благодаря интеграции с SDK для Microsoft Bot Framework вы можете добавить возможности для небольших разговоров для более интерактивного взаимодействия с клиентами. [Просмотрите видео](https://www.youtube.com/watch?v=5C_uD8g2QKg&feature=youtu.be), чтобы узнать, как реализовать эти возможности, а затем [поработайте с демонстрационным роликом](https://aka.ms/PersonalityChat) для получения практического опыта.

### <a name="one-dev-question"></a>One Dev Question

В серии видеороликов One Dev Question опытные разработчики Майкрософт рассматривают серию вопросов, касающихся разработки Windows, командной культуры и истории. Вот последние вопросы, на которые мы ответили.

Рэймонд Чен (Raymond Chen):

* [Why did you apply to Microsoft?](https://www.youtube.com/watch?v=oL8ymamkEMU&feature=youtu.be) (Почему вы обратились в корпорацию Майкрософт?)

Ларри Остерман (Larry Osterman):

* [Why don't we let developers change the default audio device?](https://www.youtube.com/watch?v=6aNUoVfbnmg&feature=youtu.be) (Почему мы не позволяем разработчикам изменять звуковое устройство по умолчанию?)
* [Why are so many UWP functions async?](https://www.youtube.com/watch?v=5M724QIy1Mk&feature=youtu.be) (Почему такое так много функций универсальной платформы Windows выполняются асинхронно?)

## <a name="samples"></a>Примеры

### <a name="photo-editor-cwinrt"></a>Редактор фотографий C++/WinRT

В примере приложения "Редактор фотографий" демонстрируются возможности разработки с помощью проекции языка [C++/WinRT](../cpp-and-winrt-apis/intro-to-using-cpp-with-winrt.md). Приложение позволяет извлекать фотографии из библиотеки **Pictures** (Изображения), а затем редактировать выбранное изображение, используя разные фотографические эффекты. [Клонируйте или скачайте пример здесь.](https://github.com/Microsoft/Windows-appsample-photo-editor)

![Пример приложения в действии](images/photo-editor-banner.png)

---
title: Дополнение документации по Windows в июле 2017 г. — разработка приложений UWP
description: В июле 2017 г документация для разработчиков Windows 10 пополнилась описанием новых возможностей, примерами и руководствами для разработчиков.
keywords: what's new, update, features, developer guidance, Windows 10
ms.date: 07/05/2017
ms.topic: article
ms.localizationpriority: medium
ms.openlocfilehash: 65c3c2fb4b7a5a7f0b5f4b3c89773f3e21bd654d
ms.sourcegitcommit: 76e8b4fb3f76cc162aab80982a441bfc18507fb4
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/29/2020
ms.locfileid: "75684741"
---
# <a name="whats-new-in-the-windows-developer-docs-in-july-2017"></a>Дополнение документации для разработчиков Windows в июле 2017 г.

Документация для разработчиков Windows постоянно обновляется и дополняется информацией о новых функциях, доступных разработчикам на платформе Windows. Недавно стали доступны следующие обзоры функций, руководства для разработчиков и примеры кода, содержащие новые и обновленные сведения для разработчиков Windows.

[Установив средства и пакет SDK](https://developer.microsoft.com/windows/downloads#_blank) в Windows 10, вы можете сразу приступить к [созданию нового проекта универсального приложения для Windows](../get-started/your-first-app.md) или использованию [существующего кода приложения в Windows](../porting/index.md).

## <a name="features"></a>Возможности

### <a name="fluent-design"></a>Система проектирования Fluent Design

Чтобы помочь пользователям сосредоточиться на важных элементах пользовательского интерфейса, в новых эффектах, доступных [участникам программы предварительной оценки Windows](https://insider.windows.com/) в предварительных сборках SDK используются глубина, перспектива и движение.

[Акриловый материал](../design/style/acrylic.md) — это тип кисти, которая создает прозрачные текстуры. 

![Акрил в светлой теме](../design/style/images/Acrylic_DarkTheme_Base.png)

[Эффект параллакса](../design/motion/parallax.md) добавляет в приложение трехмерную глубину и перспективу.

![Пример эффекта параллакса со списком и фоновым изображением](../design/style/images/_Parallax_v2.gif)

[Эффект отображения](../design/style/reveal.md) выделяет важные элементы приложения. 

![Наглядное представление эффекта отображения](../design/style/images/Nav_Reveal_Animation.gif)

### <a name="ui-controls"></a>Элементы управления пользовательским интерфейсом

Доступные [участникам программы предварительной оценки Windows](https://insider.windows.com/) в предварительной сборке SDK, эти новые элементы управления существенно упрощают и ускоряют создание потрясающего пользовательского интерфейса.

[Элемент управления "Палитра"](../design/controls-and-patterns/color-picker.md) позволяет просматривать и выбирать цвета.  

![Палитра по умолчанию](../design/controls-and-patterns/images/color-picker-default.png)

[Элемент управления "Представление навигации"](../design/controls-and-patterns/navigationview.md) упрощает добавление навигации верхнего уровня в приложение.

![Разделы представления навигации](../design/controls-and-patterns/images/navview_sections.png)

[Элемент управления "Аватар пользователя"](../design/controls-and-patterns/person-picture.md) отображает аватар пользователя.

![Элемент управления "Аватар пользователя"](../design/controls-and-patterns/images/person-picture/person-picture_hero.png)

[Элемент управления "Оценка"](../design/controls-and-patterns/rating.md) позволяет легко просматривать и ставить оценки, отражающие степень удовлетворенности содержимым и службами.

![Пример элемента управления "Оценка"](../design/controls-and-patterns/images/rating_rs2_doc_ratings_intro.png)

### <a name="design-toolkits"></a>Наборы инструментов для проектирования

[Инструменты и ресурсы проектирования для приложений UWP](../design/downloads/index.md) были расширены путем добавления наборов инструментов для рисования эскизов и Adobe XD. Существовавшие ранее наборы инструментов также были обновлены и модернизированы и теперь предоставляют более надежные элементы управления и шаблоны макетов для приложений UWP.

### <a name="dashboard-monetization-and-store-services"></a>Информационная панель, монетизация и службы Store

Теперь доступны следующие новые функции:

* Microsoft Advertising SDK теперь позволяет отображать [собственные рекламные объявления](../monetize/native-ads.md) в приложениях. Собственные объявления — это формат на основе рекламных компонентов, где каждая часть рекламного объявления (например, заголовок, изображение, описание и призыв к действию) доставляется приложение в виде отдельного элемента. Возможность создавать собственные объявления в настоящее время доступна только отдельным разработчикам, участвующим в пилотной программе, но мы планируем сделать этот компонент доступным для всех разработчиков в скором времени.

* [API аналитики для Microsoft Store](../monetize/access-analytics-data-using-windows-store-services.md) теперь предоставляет способ для [загрузки связанного с ошибкой приложения CAB-файла](../monetize/download-the-cab-file-for-an-error-in-your-app.md).

* [Целевые предложения](../publish/use-targeted-offers-to-maximize-engagement-and-conversions.md) позволяют разделить пользователей на сегменты и предлагать им привлекательный, персонализированный контент, чтобы повысить вовлеченность, коэффициент удержания и монетизацию. 

* Описание приложения в Store теперь может включать [видеоанонсы](../publish/app-screenshots-and-images.md#trailers).

* Новые параметры цен и доступности позволяют [спланировать изменение цен](../publish/set-and-schedule-app-pricing.md) и [задать точные даты выпуска](..//publish/configure-precise-release-scheduling.md).

* Можно [импортировать и экспортировать описания в Store](../publish/import-and-export-store-listings.md), чтобы делать обновления быстрее, особенно если вы используете описания на нескольких языках.

### <a name="my-people"></a>"Близкие люди"

Функция "Близкие люди", доступная [участникам программы предварительной оценки Windows](https://insider.windows.com/) в предварительных сборках SDK, позволяет закреплять контакты из приложения непосредственно на панели задач. [Узнайте, как добавить в приложение поддержку функции "Близкие люди".](../contacts-and-calendar/my-people-support.md)

![Панель контактов "Близкие люди"](images/my-people.png)

Функция [Публикации близких людей](../contacts-and-calendar/my-people-sharing.md) позволяет делиться файлами с помощью приложения непосредственно с панели задач.

![Публикации близких людей](images/my-people-sharing.png)

[Уведомления близких людей](../contacts-and-calendar/my-people-support.md) — это новый вид всплывающих уведомлений, которые пользователи могут отправлять закрепленным контактам.

![Уведомление близких людей](images/my-people-notification.png)

### <a name="pin-to-taskbar"></a>Закрепление на панели задач

Новый класс TaskbarManager, доступный [участникам программы предварительной оценки Windows](https://insider.windows.com/) в предварительных сборках SDK, позволят попросить пользователя [закрепить приложение на панели задач](../design/shell/pin-to-taskbar.md).

## <a name="developer-guidance"></a>Руководство для разработчиков

### <a name="media-playback"></a>Воспроизведение мультимедиа

Новые разделы добавлены в основную статью о воспроизведении мультимедиа: [Воспроизведение звука и видео с помощью MediaPlayer](../audio-video-camera/play-audio-and-video-with-mediaplayer.md). В разделе [Воспроизведение сферической проекции видео с помощью MediaPlayer](../audio-video-camera/play-audio-and-video-with-mediaplayer.md) показано, как воспроизводить сферически кодированное видео, включая регулировку поля зрения и ориентацию представления для поддерживаемых форматов. В разделе [Использование MediaPlayer в режиме сервера кадров](../audio-video-camera/play-audio-and-video-with-mediaplayer.md#use-mediaplayer-in-frame-server-mode) показано, как копировать кадры из мультимедиа, воспроизводимого с помощью [MediaPlayer](https://docs.microsoft.com/uwp/api/Windows.Media.Playback.MediaPlayer), на поверхность Direct3D. Это позволяет реализовывать такие сценарии, как применение эффектов в режиме реального времени с помощью построителей текстуры. В примере кода показана быстрая реализация эффекта размытия для воспроизведения видео с помощью Win2D.

### <a name="media-capture"></a>Запись мультимедиа

Статья [Обработка мультимедийных кадров с помощью MediaFrameReader](../audio-video-camera/process-media-frames-with-mediaframereader.md) была обновлена, чтобы показать использование нового класса [MultiSourceMediaFrameReader](https://docs.microsoft.com/uwp/api/windows.media.capture.frames.multisourcemediaframereader), который позволяет получить коррелирующие по времени кадры из нескольких источников мультимедиа. Это полезно, если вам нужно обрабатывать кадры из разных источников, таких как камера глубины и камера цвета, и вам нужно убедиться, что кадры из каждого источника были записаны в близкие моменты времени. Дополнительные сведения см. в разделе [Использование MultiSourceMediaFrameReader для получения коррелирующих по времени кадров из нескольких источников](../audio-video-camera/process-media-frames-with-mediaframereader.md#use-multisourcemediaframereader-to-get-time-corellated-frames-from-multiple-sources).

### <a name="scoped-search"></a>Поиск по ограниченным областям

Область UWP была добавлена в документацию [Концептуальные аспекты UWP](../get-started/universal-application-platform-guide.md) и [Справочник по API](https://docs.microsoft.com/uwp/api/) на сайте docs.microsoft.com. Если эта область не отключена, поиск в этих областях будет возвращать только документы UWP.

![Поиск по ограниченным областям](images/scoped-search.png)

### <a name="test-your-windows-app-for-windows-10-s"></a>Тестирование приложения для Windows на Windows 10 S.

Протестируйте приложение Windows, чтобы убедиться, что оно будет работать правильно на устройствах под управлением Windows S. Используйте [это новое руководство](https://docs.microsoft.com/windows/msix/desktop/desktop-to-uwp-test-windows-s) для получения инструкций.

## <a name="samples"></a>примеры

### <a name="annotated-audio-app-sample"></a>Пример приложения Annotated Audio

[Пример мини-приложения с использованием звука, рукописного ввода и перемещения данных OneDrive](https://github.com/Microsoft/Windows-appsample-annotated-audio). В этом примере записывается звук и одновременно реализуется возможность синхронизированной записи рукописных примечаний, чтобы впоследствии вы могли вспомнить, что обсуждалось в момент записи заметок.

![Снимок экрана с примером приложения Annotated Audio](images/Playback.png)  

### <a name="shopping-app-sample"></a>Пример приложения "Покупки"

[Мини-приложение, предоставляющее базовые функции для совершения покупок, с помощью которого пользователь может приобрести эмодзи](https://github.com/Microsoft/Windows-appsample-shopping). Это приложение показывает, как использовать [API-интерфейсы запроса оплаты](https://docs.microsoft.com/uwp/api/windows.applicationmodel.payments) для оформления заказа.

![Снимок экрана: пример приложения "Покупки"](images/shoppingcart.png)  

## <a name="videos"></a>Видео

### <a name="accessibility"></a>Специальные возможности

Добавление специальных возможностей в ваши приложения делает их доступными для гораздо более широкой аудитории. [Посмотрите видео](https://channel9.msdn.com/Blogs/One-Dev-Minute/Developing-Apps-for-Accessibility) и узнайте больше о [разработке приложений со специальными возможностями](https://developer.microsoft.com/windows/accessible-apps).

### <a name="payments-request-api"></a>API запроса оплаты

API запроса оплаты помогает клиентам и продавцам легко выполнять процесс оформления заказа в Интернете. [Посмотрите это видео](https://channel9.msdn.com/Blogs/One-Dev-Minute/Using-the-Payments-Request-API), а затем изучите [документацию по запросу оплаты](https://channel9.msdn.com/Blogs/One-Dev-Minute/Using-the-Payments-Request-API).

### <a name="windows-10-iot-core"></a>Windows 10 IoT Базовая

В Windows 10 IoT Базовая и на универсальной платформе Windows можно быстро создавать прототипы и разрабатывать проекты с подключениями видения и компонентов, такими как Pet Recognition Door. [Посмотрите это видео](https://channel9.msdn.com/Blogs/One-Dev-Minute/Building-a-Pet-Recognition-Door-Using-Windows-10-IoT-Core) и узнайте больше о том, как [начать работу с Windows 10 IoT Базовая](https://developer.microsoft.com/windows/iot).
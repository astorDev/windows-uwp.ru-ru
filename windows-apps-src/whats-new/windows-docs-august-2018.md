---
author: QuinnRadich
title: 'Новые возможности Windows: документы в 2018 августа — разработка UWP приложений'
description: В документации для разработчиков Windows 10 августа 2018 были добавлены новые функции, видео, примеры и рекомендации для разработчиков.
keywords: новые возможности, обновления, средства, инструкции для разработчиков, Windows 10 августа
ms.author: quradic
ms.date: 08/14/2018
ms.topic: article
ms.prod: windows
ms.technology: uwp
ms.localizationpriority: medium
ms.openlocfilehash: c294dedc8e19605bc2cee0308022bed8624df57e
ms.sourcegitcommit: 753dfcd0f9fdfc963579dd0b217b445c4b110a18
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/27/2018
ms.locfileid: "2855360"
---
# <a name="whats-new-in-the-windows-developer-docs-in-august-2018"></a>Новые возможности документация для разработчиков Windows в 2018 августа

Документация для разработчиков Windows непрерывно обновляется и дополняется информацией о новых функциях, доступных разработчикам на платформе Windows. Компонент обзоры, рекомендации для разработчиков и видео доступному в месяц августа.

[Установив средства и пакет SDK](http://go.microsoft.com/fwlink/?LinkId=821431) в Windows 10, вы можете сразу приступить либо к [созданию нового проекта универсального приложения для Windows](../get-started/create-uwp-apps.md), либо к использованию [существующего кода приложения в ОС Windows](../porting/index.md).

## <a name="features"></a>Возможности

### <a name="design"></a>Оформление

Следующие возможности были добавлены к Windows доступных по программе [Windows изнутри](https://insider.windows.com/) построений изнутри предварительного просмотра.

* [Библиотека пользовательского интерфейса Windows](https://aka.ms/winui-docs) — это набор пакетов NuGet, которые обеспечивают элементы управления и другие элементы interfact UWP приложений. Эти пакеты могут также совместимые с более ранними версиями Windows 10, поэтому работает приложение даже в том случае, если пользователи не установлена последняя версия операционной системы.

* [DropDownButton](../design/controls-and-patterns/buttons.md#create-a-drop-down-button), [кнопкой-разделителем](../design/controls-and-patterns/buttons.md#create-a-split-button)и [ToggleSplitButton](../design/controls-and-patterns/buttons.md#create-a-toggle-split-button) предоставляют кнопок с специальные возможности для улучшения пользовательского интерфейса вашего приложения.

![Разворачивающаяся кнопка для выбора цвета переднего плана](../design/controls-and-patterns/images/split-button-rtb.png)

* NavigationView теперь поддерживает [верхней панели навигации](../design/controls-and-patterns/navigationview.md)для случаев, в которых ваше приложение имеет меньшее количество параметров навигации и требуется больше пространства для контента вашего приложения.

* TreeView был улучшен для поддержки [привязки данных, шаблоны, элементов и перетащите.](../design/controls-and-patterns/tree-view.md)

### <a name="package-support-framework"></a>Платформа поддержки пакетов

Платформа поддержки пакетов — это набор открытым исходным кодом, которые помогут вам применить исправления win32 приложения при отсутствии доступа к исходному коду, чтобы его можно запускать в контейнере MSIX.

Для получения дополнительных сведений, видеть [устраняет среды выполнения применить пакет MSIX с помощью пакета поддержки платформы](../porting/package-support-framework.md).

## <a name="developer-guidance"></a>Руководство для разработчиков

### <a name="web-api-extensions"></a>API-Интерфейс веб-расширения

Список [расширений прежних версий Microsoft API](https://developer.mozilla.org/docs/Web/API/Microsoft_API_extensions) добавлена в Mozilla разработчиков документации для разработки веб-браузеров. Эти расширения API являются уникальными для Internet Explorer или пограничного сервера Microsoft и дополнять существующие сведения о поддержке и broswer совместимости в веб-документы MDN. Прежних версий Microsoft [расширения CSS](https://developer.mozilla.org/docs/Web/CSS/Microsoft_Extensions) и [JavaScript расширения](https://developer.mozilla.org/docs/Web/JavaScript/Microsoft_JavaScript_extensions) также доступны, и можно найти непосредственно в предоставления расширенных web информации об API из MDN [кода Visual Studio.](https://code.visualstudio.com/updates/v1_25#_new-css-pseudo-selectors-and-pseudo-elements-from-mdn)

### <a name="cwinrt-code-examples"></a>C + +/ примеры кода WinRT

Мы добавили 250 [C + +/ WinRT](../cpp-and-winrt-apis/index.md) кода перечни на разделы, в нашей: документы, наглядные существующих C + +/ CX примеров кода.

### <a name="project-rome"></a>Project Rome

Сайт [: документы проекта рим](https://docs.microsoft.com/windows/project-rome/) были преобразованы в подход первоначального компонента. Это должно упростить для разработчиков для определения нужны их и реализовать функции по своему выбору на нескольких платформах.

## <a name="videos"></a>Видеоролики

### <a name="xbox-live-unity-plugin"></a>Подключаемый модуль Xbox Live единства

Подключаемый модуль Xbox Live для единства содержит поддерживает добавление Xbox Live подписи, stats, списках друзей, Облачное хранилище и списки лидеров название. [Просмотр видео](https://youtu.be/fVQZ-YgwNpY) с более подробной информацией, а затем [Загрузить пакет репозиториев](https://aka.ms/UnityPlugin) для начала работы.

### <a name="one-dev-question"></a>Один вопрос разработчиков

Один вопрос Dev серии видео привыкшим разработчиков Microsoft охватывают ряд вопросов о разработки, языка и региональных параметров группы и журнал Windows. Вот последние вопросы мы!

Рэймонд Чен:

* [Как ядра определяет, когда следует перезапустить видео драйвер?](https://youtu.be/3SNAdyO1l5c)

Ларри Osterman:

* [Что такое состоит суть объекта Burgermaster в Windows?](https://youtu.be/0TDSbyAIvX0)
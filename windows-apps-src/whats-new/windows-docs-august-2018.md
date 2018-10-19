---
author: QuinnRadich
title: Что нового в документации Windows в августе 2018 г. — Разработка приложений UWP
description: Новые возможности, видео, примеры и руководства для разработчиков добавлены в документацию для августа 2018 разработчиков Windows 10.
keywords: новые возможности, обновления, компоненты, руководство разработчика, Windows 10 августа
ms.author: quradic
ms.date: 08/14/2018
ms.topic: article
ms.prod: windows
ms.technology: uwp
ms.localizationpriority: medium
ms.openlocfilehash: c294dedc8e19605bc2cee0308022bed8624df57e
ms.sourcegitcommit: 310a4555fedd4246188a98b31f6c094abb33ec60
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/19/2018
ms.locfileid: "5126375"
---
# <a name="whats-new-in-the-windows-developer-docs-in-august-2018"></a>Что нового в документации для разработчиков Windows в августе 2018 г.

Документация для разработчиков Windows непрерывно обновляется и дополняется информацией о новых функциях, доступных разработчикам на платформе Windows. Обзоры, руководства для разработчиков и видео, которые стали доступны августа.

[Установив средства и пакет SDK](http://go.microsoft.com/fwlink/?LinkId=821431) в Windows 10, вы можете сразу приступить либо к [созданию нового проекта универсального приложения для Windows](../get-started/create-uwp-apps.md), либо к использованию [существующего кода приложения в ОС Windows](../porting/index.md).

## <a name="features"></a>Возможности

### <a name="design"></a>Оформление

Следующие возможности были добавлены Windows сборки Insider Preview, доступные через программу [Предварительной оценки Windows](https://insider.windows.com/) .

* [Библиотека пользовательского интерфейса Windows](https://aka.ms/winui-docs) — это набор пакетов NuGet, которые предоставляют элементы управления и другие элементы interfact пользователя для приложений UWP. Эти пакеты также являются совместимые с более ранними версиями Windows 10, чтобы ваше приложение работает, даже если пользователи не установлена последняя версия операционной системы.

* [DropDownButton](../design/controls-and-patterns/buttons.md#create-a-drop-down-button), [кнопкой-разделителем](../design/controls-and-patterns/buttons.md#create-a-split-button)и [ToggleSplitButton](../design/controls-and-patterns/buttons.md#create-a-toggle-split-button) предоставляют кнопок с специальные возможности для улучшения пользовательского интерфейса вашего приложения.

![Разворачивающуюся кнопку для выбора цвета переднего плана](../design/controls-and-patterns/images/split-button-rtb.png)

* NavigationView теперь поддерживает [верхней части](../design/controls-and-patterns/navigationview.md)для случаев, в которых ваше приложение имеет меньшего количества параметров навигации и потребуется больше места для содержимого приложения.

* TreeView был улучшен для поддержки [привязки данных, шаблоны, элементов и перетащите.](../design/controls-and-patterns/tree-view.md)

### <a name="package-support-framework"></a>Платформа поддержки пакетов

Платформа поддержки пакета — комплекту открытым исходным кодом, которое поможет вам применение исправлений в приложение win32, когда нет доступа к исходному коду, чтобы оно могло выполняться в контейнере MSIX.

Для получения дополнительных сведений, см. в разделе [среды выполнения применить их устранения пакета MSIX с помощью платформа поддержки пакетов](../porting/package-support-framework.md).

## <a name="developer-guidance"></a>Руководство для разработчиков

### <a name="web-api-extensions"></a>Веб-API расширения

Список [устаревших расширения Microsoft API](https://developer.mozilla.org/docs/Web/API/Microsoft_API_extensions) был добавлен в Mozilla Developer Network документацию для различных браузерах веб-разработки. Эти расширения API являются уникальными для Internet Explorer или Microsoft Edge и дополнять существующие сведения о поддержке совместимости и broswer в документации web MDN. Устаревшие Microsoft [расширения CSS](https://developer.mozilla.org/docs/Web/CSS/Microsoft_Extensions) и [JavaScript расширения](https://developer.mozilla.org/docs/Web/JavaScript/Microsoft_JavaScript_extensions) также доступны, и вы можете найти богатые сетевые API информации из MDN доступны непосредственно в [Visual Studio Code.](https://code.visualstudio.com/updates/v1_25#_new-css-pseudo-selectors-and-pseudo-elements-from-mdn)

### <a name="cwinrt-code-examples"></a>C + +/ WinRT примеры

Мы добавили 250 [C + +/ WinRT](../cpp-and-winrt-apis/index.md) кода описания на разделы в нашей документации, в дополнение к существующей C + +/ CX примеры кода.

### <a name="project-rome"></a>Project Rome

На сайте [документации Project Rome](https://docs.microsoft.com/windows/project-rome/) были преобразованы в подход с преимущественно компонентов. Это необходимо упростить для разработчиков, чтобы найти то, что они ищете и для реализации функций по своему выбору на нескольких платформах.

## <a name="videos"></a>Видеоролики

### <a name="xbox-live-unity-plugin"></a>Подключаемый модуль Xbox Live Unity

Подключаемого модуля Xbox Live для Unity содержит поддерживает добавление Xbox Live подписи, статистика, списки друзей, облачного хранения данных и списки лидеров заголовок. [Посмотрите видео,](https://youtu.be/fVQZ-YgwNpY) для получения дополнительных сведений, а затем [Загрузить пакет GitHub](https://aka.ms/UnityPlugin) для начала работы.

### <a name="one-dev-question"></a>Вопрос

В серии видео вопрос привыкшим разработчики Майкрософт охватывает ряд вопросов о разработке Windows, язык и региональные параметры для совместной работы и журнал. Вот последние вопросы, которые мы ответили!

Рэймонд Чен:

* [Как ядра определяет, когда следует перезапустить видеодрайвера?](https://youtu.be/3SNAdyO1l5c)

Ларри Osterman:

* [Что такое новости за Burgermaster объект в Windows](https://youtu.be/0TDSbyAIvX0)
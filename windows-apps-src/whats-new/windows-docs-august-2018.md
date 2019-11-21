---
title: Пополнение документации Windows в августе 2018 г. — разработка приложений UWP
description: В августе 2018 г документация для разработчиков Windows 10 пополнилась описанием новых возможностей, видеоматериалами, примерами и руководствами для разработчиков.
keywords: what's new, update, features, developer guidance, Windows 10, august
ms.date: 08/14/2018
ms.topic: article
ms.localizationpriority: medium
ms.openlocfilehash: 723cae783ba16fe5be9bb2076f96d4a5f823d733
ms.sourcegitcommit: b52ddecccb9e68dbb71695af3078005a2eb78af1
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/20/2019
ms.locfileid: "74258835"
---
# <a name="whats-new-in-the-windows-developer-docs-in-august-2018"></a>Пополнение документации Windows в августе 2018 г.

Документация для разработчиков Windows непрерывно обновляется и дополняется информацией о новых функциях, доступных разработчикам на платформе Windows. В августе появились приведенные ниже обзоры функций, руководства для разработчиков и видео.

[Установив средства и пакет SDK](https://developer.microsoft.com/windows/downloads#_blank) в Windows 10, вы можете сразу приступить либо к [созданию нового проекта универсального приложения для Windows](../get-started/create-uwp-apps.md) либо к использованию [существующего кода приложения в ОС Windows](../porting/index.md).

## <a name="features"></a>Возможности

### <a name="design"></a>Оформление

В сборки Windows Insider Preview были добавлены указанные ниже возможности, доступные в рамках программы [предварительной оценки Windows](https://insider.windows.com/).

* [Библиотека пользовательского интерфейса Windows](https://docs.microsoft.com/uwp/toolkits/winui/)— это набор пакетов NuGet, предоставляющих элементы управления и прочие элементы пользовательского интерфейса для приложений UWP. Эти пакеты также совместимы с предыдущими версиями Windows 10, поэтому ваше приложение будет работать, даже если пользователи используют не самую последнюю версию ОС.

* [DropDownButton](../design/controls-and-patterns/buttons.md#create-a-drop-down-button), [SplitButton](../design/controls-and-patterns/buttons.md#create-a-split-button) и [ToggleSplitButton](../design/controls-and-patterns/buttons.md#create-a-toggle-split-button) предоставляют элементы управления "Кнопка" со специальными функциями для расширения возможностей пользовательского интерфейса вашего приложения.

![Разворачивающаяся кнопка для выбора цвета переднего плана](../design/controls-and-patterns/images/split-button-rtb.png)

* NavigationView теперь поддерживает [верхнюю панель навигации](../design/controls-and-patterns/navigationview.md) на случай, если ваше приложение содержит меньшее количество параметров навигации и требует больше места для содержимого.

* TreeView был усовершенствован для поддержки [привязки данных, шаблонов элементов и перетаскивания](../design/controls-and-patterns/tree-view.md).

### <a name="package-support-framework"></a>Платформа поддержки пакетов

Платформа поддержки пакетов — это набор средств с открытым исходным кодом, который помогает применять исправления к приложениям win32 для выполнения в контейнере MSIX, когда у вас нет доступа к исходному коду.

Подробные сведения см. в статье о [применении исправлений среды выполнения к пакету MSIX с помощью платформы поддержки пакетов](../porting/package-support-framework.md).

## <a name="developer-guidance"></a>Руководство для разработчиков

### <a name="web-api-extensions"></a>Расширения веб-API

Список [устаревших расширений Microsoft API](https://developer.mozilla.org/docs/Web/API/Microsoft_API_extensions) был добавлен в документацию Mozilla Developer Network для кросс-браузерной веб-разработки. Эти расширения API уникальны для Internet Explorer или Microsoft Edge и дополняют существующую информацию о совместимости и поддержке браузера в веб-документах MDN. Доступны также устаревшие [CSS-расширения](https://developer.mozilla.org/docs/Web/CSS/Microsoft_Extensions) и [расширения JavaScript](https://developer.mozilla.org/docs/Web/JavaScript/Microsoft_JavaScript_extensions) корпорации Майкрософт, и вы можете найти обширную информацию о веб-API из MDN, которая появилась непосредственно в [коде Visual Studio](https://code.visualstudio.com/updates/v1_25#_new-css-pseudo-selectors-and-pseudo-elements-from-mdn).

### <a name="cwinrt-code-examples"></a>Примеры кода C++/WinRT

Мы добавили 250 примеров кода [C++/WinRT](../cpp-and-winrt-apis/index.md) в разделы нашей документации (в дополнение к существующим примерам кода C++/CX).

### <a name="project-rome"></a>Project Rome

Веб-сайт [документации по Project Rome](https://docs.microsoft.com/windows/project-rome/) был реорганизован и теперь первыми отображает сведения о возможностях. Это должно упростить разработчикам поиск нужной информации, а также реализацию функций на разных платформах.

## <a name="videos"></a>Видео

### <a name="xbox-live-unity-plugin"></a>Подключаемый модуль Xbox Live Unity

Подключаемый модуль Xbox Live для Unity включает в себя поддержку добавления подписи Xbox Live, статистики, списков друзей, облачного хранилища и списков лидеров в ваш заголовок. Подробные сведения см. в [этом видео](https://youtu.be/fVQZ-YgwNpY). Для начала работы [скачайте пакет GitHub](https://aka.ms/UnityPlugin).

### <a name="one-dev-question"></a>One Dev Question

В серии видеороликов One Dev Question опытные разработчики Майкрософт рассматривают серию вопросов, касающихся разработки Windows, командной культуры и истории. Вот последние вопросы, на которые мы ответили.

Рэймонд Чен (Raymond Chen):

* [Как ядро определяет, когда нужно перезапустить драйвер видео?](https://youtu.be/3SNAdyO1l5c)

Ларри Остерман (Larry Osterman):

* [Как появился объект Burgermaster в Windows?](https://youtu.be/0TDSbyAIvX0)
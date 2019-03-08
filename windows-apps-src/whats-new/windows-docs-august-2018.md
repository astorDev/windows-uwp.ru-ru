---
title: Новые возможности в документации Windows в августа 2018 - разработка приложений универсальной платформы Windows
description: В документации разработчика Windows 10 августа 2018 были добавлены новые функции, видео, примеры и материалы для разработчиков.
keywords: новые возможности, обновления, функции, материалы для разработчиков, Windows 10 августа
ms.date: 08/14/2018
ms.topic: article
ms.localizationpriority: medium
ms.openlocfilehash: 9922aa1ad2442153dcc2c13d05520c05c3b56d31
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57616489"
---
# <a name="whats-new-in-the-windows-developer-docs-in-august-2018"></a>Новые возможности в документации по Windows Developer в августа 2018

Документация для разработчиков Windows непрерывно обновляется и дополняется информацией о новых функциях, доступных разработчикам на платформе Windows. Обзор компонентов, материалы для разработчиков и видеоматериалы были выпущены в августа.

[Установив средства и пакет SDK](https://go.microsoft.com/fwlink/?LinkId=821431) в Windows 10, вы можете сразу приступить либо к [созданию нового проекта универсального приложения для Windows](../get-started/create-uwp-apps.md) либо к использованию [существующего кода приложения в ОС Windows](../porting/index.md).

## <a name="features"></a>Возможности

### <a name="design"></a>Оформление

Следующие функции были добавлены Windows Insider Preview сборок, доступных через [Windows Insider](https://insider.windows.com/) программы.

* [Библиотека пользовательского интерфейса Windows](https://aka.ms/winui-docs) — это набор пакетов NuGet, которые обеспечивают элементов управления и других элементов interfact приложений универсальной платформы Windows. Эти пакеты также являются предварительными с более ранними версиями Windows 10, поэтому приложение работает, даже если ваши пользователи не имеют последнюю версию ОС.

* [DropDownButton](../design/controls-and-patterns/buttons.md#create-a-drop-down-button), [SplitButton](../design/controls-and-patterns/buttons.md#create-a-split-button), и [ToggleSplitButton](../design/controls-and-patterns/buttons.md#create-a-toggle-split-button) предоставляют элементы управления кнопки с помощью специализированных функций, позволяющих улучшить ваше приложение пользовательский интерфейс.

![Разворачивающаяся кнопка для выбора цвета переднего плана](../design/controls-and-patterns/images/split-button-rtb.png)

* Теперь поддерживает NavigationView [Top навигации](../design/controls-and-patterns/navigationview.md), для случаев, в которых приложение имеет меньшее число параметров навигации и занимают больше места для содержимого вашего приложения.

* TreeView был улучшен для поддержки [привязки данных, шаблоны элементов, элементы и перетаскивания.](../design/controls-and-patterns/tree-view.md)

### <a name="package-support-framework"></a>Платформа поддержки пакетов

Платформа поддержки пакета — пакету средств открытым исходным кодом, который служит для применения исправления приложение win32, когда у вас нет доступа к исходному коду, могут выполняться в контейнере MSIX.

Дополнительные сведения см. в разделе [среды выполнения применить исправления в пакет MSIX при помощи пакета поддержки платформы](../porting/package-support-framework.md).

## <a name="developer-guidance"></a>Руководство для разработчиков

### <a name="web-api-extensions"></a>Расширения веб-API

Список [устаревшие расширения Microsoft API](https://developer.mozilla.org/docs/Web/API/Microsoft_API_extensions) была добавлена в Mozilla Developer Network документации для разработки веб-обозревателей. Эти расширения API являются уникальными для Internet Explorer или Microsoft Edge и дополнить существующие сведения о поддержке совместимости и анонимный в веб-документы MDN. Устаревшие Microsoft [расширений CSS](https://developer.mozilla.org/docs/Web/CSS/Microsoft_Extensions) и [расширения JavaScript](https://developer.mozilla.org/docs/Web/JavaScript/Microsoft_JavaScript_extensions) также доступны и вы найдете богатые сетевые сведения об API из MDN отображаются непосредственно в [Visual Studio Code.](https://code.visualstudio.com/updates/v1_25#_new-css-pseudo-selectors-and-pseudo-elements-from-mdn)

### <a name="cwinrt-code-examples"></a>C + +/ WinRT примеры

Мы добавили 250 [C + +/ WinRT](../cpp-and-winrt-apis/index.md) кода списки на разделы, в нашей документации, сопровождающие существующих C + +/ CX примеры кода.

### <a name="project-rome"></a>Project Rome

[Документы проекта рим](https://docs.microsoft.com/windows/project-rome/) реорганизовать сайта, в это функция первый подход. Это должно упростить для разработчиков, для поиска, что они ищут и для реализации функций по своему выбору на различных платформах.

## <a name="videos"></a>Видео

### <a name="xbox-live-unity-plugin"></a>Подключаемый модуль Xbox Live Unity

Подключаемый модуль Xbox Live для Unity включает в себя поддержку для добавления в заголовок вашей подписи Xbox Live, статистики, списки друзей, Облачное хранилище и списки лидеров. [Просмотрите видео](https://youtu.be/fVQZ-YgwNpY) для получения дополнительных сведений, затем [загрузить пакет GitHub](https://aka.ms/UnityPlugin) Чтобы приступить к работе.

### <a name="one-dev-question"></a>Один вопрос разработки

В серии видео один вопрос разработки привыкшим разработчиков Microsoft охватывают ряд вопросов о разработке Windows, команды языка и региональных параметров и журнал. Вот последние вопросы, которые мы выбрали ответ!

Рэймонд Чен:

* [Как ядро, что когда следует перезапустить видео драйвер?](https://youtu.be/3SNAdyO1l5c)

Блоге Ларри Остермана:

* [Что такое стоял за Burgermaster объекта в Windows?](https://youtu.be/0TDSbyAIvX0)
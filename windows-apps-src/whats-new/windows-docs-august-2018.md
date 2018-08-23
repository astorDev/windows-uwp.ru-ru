---
author: QuinnRadich
title: Новые возможности Windows документы в августе 2018 - разработка приложений UWP
description: В документации разработчика Windows 10 августа 2018 были добавлены новые возможности, видео, примеры и материалы для разработчиков.
keywords: новые возможности, обновления, средства, материалы для разработчиков Windows 10 августа
ms.author: quradic
ms.date: 08/14/2018
ms.topic: article
ms.prod: windows
ms.technology: uwp
ms.localizationpriority: medium
ms.openlocfilehash: c294dedc8e19605bc2cee0308022bed8624df57e
ms.sourcegitcommit: 9c79fdab9039ff592edf7984732d300a14e81d92
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/23/2018
ms.locfileid: "2815973"
---
# <a name="whats-new-in-the-windows-developer-docs-in-august-2018"></a>Новые возможности в документации разработчика Windows в августе 2018

Документация для разработчиков Windows непрерывно обновляется и дополняется информацией о новых функциях, доступных разработчикам на платформе Windows. Функций, руководство разработчика и видео были выпущены в месяц август.

[Установив средства и пакет SDK](http://go.microsoft.com/fwlink/?LinkId=821431) в Windows 10, вы можете сразу приступить либо к [созданию нового проекта универсального приложения для Windows](../get-started/create-uwp-apps.md), либо к использованию [существующего кода приложения в ОС Windows](../porting/index.md).

## <a name="features"></a>Возможности

### <a name="design"></a>Оформление

Следующие возможности были добавлены к Windows изнутри Просмотр построений доступны в программе [Windows: взгляд изнутри](https://insider.windows.com/) .

* [Библиотека пользовательского интерфейса Windows](https://aka.ms/winui-docs) — это набор пакетов NuGet, которые предоставляют элементы управления и другие элементы interfact для UWP приложений. Эти пакеты также являются совместимые с более ранними версиями Windows 10, поэтому приложение работает, даже если пользователи не установлена последняя версия операционной системы.

* [DropDownButton](../design/controls-and-patterns/buttons.md#create-a-drop-down-button), [SplitButton](../design/controls-and-patterns/buttons.md#create-a-split-button)и [ToggleSplitButton](../design/controls-and-patterns/buttons.md#create-a-toggle-split-button) обеспечивают кнопок с специальные возможности для улучшения интерфейса пользователя приложения.

![Разворачивающаяся кнопка для выбора основного цвета](../design/controls-and-patterns/images/split-button-rtb.png)

* NavigationView теперь поддерживает [верхней навигационной](../design/controls-and-patterns/navigationview.md)для случаев, в которых приложение имеет меньшее количество параметров навигации и требует больше места для содержимого вашего приложения.

* TreeView был улучшен для поддержки [привязки данных, шаблонов, элементов и перетащите.](../design/controls-and-patterns/tree-view.md)

### <a name="package-support-framework"></a>Платформа поддержки пакетов

Платформа поддержки пакетов представляет собой набор открытым исходным кодом, позволяющее применять исправления приложения win32 при отсутствии доступа к исходному коду, позволяющие выполнять в контейнере MSIX.

Для получения дополнительных сведений, см. [среда выполнения применить исправления к пакету MSIX с помощью платформа поддержки пакетов](../porting/package-support-framework.md).

## <a name="developer-guidance"></a>Руководство для разработчиков

### <a name="web-api-extensions"></a>API веб-расширения

Список [стандартных расширений интерфейса API Microsoft](https://developer.mozilla.org/docs/Web/API/Microsoft_API_extensions) был добавлен в сеть разработчиков Mozilla документации для разработки веб-обозревателей. Эти расширения API уникальны для Internet Explorer или Microsoft края и дополняют существующие сведения о поддержке совместимости и broswer в MDN веб-документы. [Расширения JavaScript](https://developer.mozilla.org/docs/Web/JavaScript/Microsoft_JavaScript_extensions) и прежних версий Microsoft [расширения CSS](https://developer.mozilla.org/docs/Web/CSS/Microsoft_Extensions) , также доступны и могут быть найдены богатые сетевые API сведения из MDN отображаются непосредственно в [кода Visual Studio.](https://code.visualstudio.com/updates/v1_25#_new-css-pseudo-selectors-and-pseudo-elements-from-mdn)

### <a name="cwinrt-code-examples"></a>C + +/ примеры кода WinRT

Мы добавили 250 [C + +/ WinRT](../cpp-and-winrt-apis/index.md) код программы передач на разделы в наших документах, сопровождающих существующих C + +/ CX примеры кода.

### <a name="project-rome"></a>Project Rome

В первой функции подход были реорганизованы [docs рим проекта](https://docs.microsoft.com/windows/project-rome/) веб-узла. Это должно упростить для разработчиков, чтобы найти то, что они хотят и для реализации функций по своему выбору на нескольких платформах.

## <a name="videos"></a>Видеоролики

### <a name="xbox-live-unity-plugin"></a>Подключаемый модуль Xbox Live Unity

Подключаемый модуль Xbox Live для Unity содержит поддерживает добавление подписи Xbox Live, статистика, списки друзей, облачного хранилища и списки лидеров название. Для получения дополнительных сведений [просмотрите видео](https://youtu.be/fVQZ-YgwNpY) , а затем [Загрузить пакет GitHub](https://aka.ms/UnityPlugin) , чтобы приступить к работе.

### <a name="one-dev-question"></a>Один вопрос разработки

В этой серии видео один вопрос Dev привыкшим разработчиков корпорации Майкрософт охватывают ряд вопросов об истории, языка и региональных параметров группы и разработки Windows. Вот последние вопросы, которые мы выбрали ответ!

Рэймонд Чен:

* [Как ядро определяет, когда следует перезапустить драйвер видеоадаптера?](https://youtu.be/3SNAdyO1l5c)

Ларри Osterman:

* [Что такое материала позади объекта Burgermaster в Windows?](https://youtu.be/0TDSbyAIvX0)
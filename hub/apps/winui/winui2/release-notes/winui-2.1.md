---
title: Заметки о выпуске WinUI 2.1
description: Заметки о выпуске для WinUI 2.1, включающие новые функции и исправления ошибок.
ms.date: 04/15/2020
ms.topic: article
ms.openlocfilehash: f62dd912d76f8045a46d467f49167cdc426b75c2
ms.sourcegitcommit: d0f479f1955881afb62c2af249db5d0b053b63e5
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/19/2020
ms.locfileid: "83580621"
---
# <a name="windows-ui-library-21"></a>Библиотека пользовательского интерфейса Windows 2.1

Последняя официальная версия библиотеки пользовательского интерфейса Windows — WinUI 2.1 — была выпущена 8 апреля 2019 г. 

WinUI предоставляет множество новейших функций платформы Windows UX, включая актуальные элементы управления и стили Fluent, которые можно использовать сразу, с обратной совместимостью для Юбилейного обновления Windows 10 (14393). [Коллекция элементов управления XAML](https://docs.microsoft.com/windows/uwp/design/controls-and-patterns/#xaml-controls-gallery) предоставляет примеры для изучения всех интересных новых функций, добавленных в библиотеку.

Скачайте [пакет NuGet WinUI 2.1](https://www.nuget.org/packages/Microsoft.UI.Xaml/2.1.190405004)

Вы можете использовать пакеты WinUI для приложения через диспетчера пакетов NuGet, как описано в статье [Начало работы с библиотекой пользовательского интерфейса Windows](https://docs.microsoft.com/uwp/toolkits/winui/getting-started).

WinUI — проект с открытым кодом, размещенный на сайте GitHub. Мы рады любым сообщениям об ошибках, запросам на добавление функций и вкладам сообщества в создание кода, вносимым в [репозиторий библиотеки пользовательского интерфейса Windows](https://aka.ms/winui).

## <a name="whats-new-in-this-release"></a>Что нового в этом выпуске

### <a name="itemsrepeater"></a>ItemsRepeater

Используйте ItemsRepeater для создания взаимодействия настраиваемых коллекций с помощью системы гибкого макета, пользовательских представлений и виртуализации.
В отличие от ListView, ItemsRepeater не обеспечивает комплексное взаимодействие с пользователем — он не содержит стандартного пользовательского интерфейса и не предоставляет политику в отношении фокуса, выбора или взаимодействия с пользователем. Вместо этого он является стандартным блоком, который можно использовать для создания собственных уникальных интерфейсов, основанных на коллекции, и пользовательских элементов управления. Он поддерживает создание более мощных и более производительных возможностей.

![Пример](../images/ItemsRepeater%20-%20MSN%20News.gif)

[Документация](https://docs.microsoft.com/windows/uwp/design/controls-and-patterns/items-repeater)

### <a name="animatedvisualplayer"></a>AnimatedVisualPlayer

AnimatedVisualPlayer размещает воспроизведение анимированных визуальных элементов и управляет ими, позволяя добавлять высокопроизводительную пользовательскую анимационную графику в приложение. Например, AnimatedVisualPlayer используется для вывода анимаций Lottie и управления ими.

![Пример](../images/AnimatedVisualPlayerUpdated.gif)

[Документация](https://docs.microsoft.com/windows/communitytoolkit/animations/lottie)

### <a name="teachingtip"></a>TeachingTip

TeachingTip предоставляет увлекательный и свободный способ применения приложений, чтобы направлять и информировать пользователей с помощью информативных и содержательных советов. TeachingTip поможет сосредоточиться на новых или важных функциях, научить пользователей выполнять задачи и улучшать рабочие процессы, предоставляя контекстно релевантную информацию вашей рассматриваемой задаче.

![Пример](../images/TeachingTipUpdated.gif)

[Документация](https://docs.microsoft.com/windows/uwp/design/controls-and-patterns/dialogs-and-flyouts/teaching-tip)

### <a name="radiomenuflyoutitem"></a>RadioMenuFlyoutItem

Включает в себя возможность иметь параметры стиля "Переключатель" в MenuBar. Это позволяет создавать группы параметров с маркерами, которые связаны друг с другом, как группа переключателей. Логика обрабатывается для разработчика.

![Пример](../images/RadioMenuFlyoutItem1.png)

[Документация](https://docs.microsoft.com/windows/uwp/design/controls-and-patterns/menus#create-a-menu-flyout-or-a-context-menu)

### <a name="compactdensity"></a>CompactDensity

Компактный режим позволяет разработчикам создавать удобные интерфейсы для любого числа сценариев. Просто добавив ресурсный словарь, ваше приложение может разместить в среднем на ~33 % больше пользовательского интерфейса.

![Пример плотности сжатия](../images/CompactDensityUpdated.png)

[Документация](https://docs.microsoft.com/windows/uwp/design/style/spacing )

### <a name="shadows"></a>Тени

![Пример](../images/shadow.gif)

Создание визуальной иерархии элементов в пользовательском интерфейсе делает пользовательский интерфейс удобным для просмотра и передает то, на чем важно сконцентрироваться. Повышение прав, действия по выделению элементов пользовательского интерфейса часто используются для достижения такой иерархии в программном обеспечении. 

В обновлении Windows 10 за май 2019 г. многие из наших стандартных элементов управления добавляют повышение прав с помощью z-depth и тени по умолчанию. При работе в ОС с обновлением Windows 10 за май 2019 г. элементы управления NavigationView и TeachingTip в WinUI 2.1 также будут иметь тени по умолчанию. Полный список элементов управления, имеющих тени по умолчанию, и способ использования дополнительных API будут доступны после выпуска обновления Windows 10 за май 2019 г., а ссылка будет размещена здесь.

## <a name="examples"></a>Примеры

Пример приложения XAML Controls Gallery включает интерактивные демонстрации и примеры кода для работы с элементами управления WinUI.

* Установите приложение XAML Controls Gallery из [Microsoft Store](
https://www.microsoft.com/p/xaml-controls-gallery/9msvh128x2zt).

* Также [на сайте GitHub](
https://github.com/Microsoft/Xaml-Controls-Gallery) доступен открытый код приложения Xaml Controls Gallery.

## <a name="documentation"></a>Документация

Практические руководства по элементам управления из библиотеки пользовательского интерфейса Windows вы найдете в [документации по элементам управления универсальной платформы Windows](/windows/uwp/design/controls-and-patterns/).

Справочная документация по API доступна по этой ссылке: [API библиотеки пользовательского интерфейса Windows](/uwp/api/overview/winui/).

## <a name="microsoftuixaml-21-version-history"></a>Журнал версий Microsoft.UI.Xaml 2.1

### <a name="microsoftuixaml-21-official-release"></a>Официальный выпуск Microsoft.UI.Xaml 2.1

Апрель 2019 г.

[Страница выпуска GitHub](https://github.com/Microsoft/microsoft-ui-xaml/releases)

[Скачивание пакета NuGet](https://www.nuget.org/packages/Microsoft.UI.Xaml/2.1.190405004)

#### <a name="new-feature-not-included-in-earlier-pre-releases"></a>Новая функция (не включена в более ранние предварительные версии)

* [CompactDensity](https://docs.microsoft.com/windows/uwp/design/style/spacing): компактный режим позволяет разработчикам создавать удобные возможности для любого числа сценариев. Просто добавив ресурсный словарь, ваше приложение может разместить в среднем на ~33 % больше пользовательского интерфейса.

* Тени: Создание визуальной иерархии элементов в пользовательском интерфейсе делает пользовательский интерфейс удобным для просмотра и передает то, на чем важно сконцентрироваться. Повышение прав, действия по выделению элементов пользовательского интерфейса часто используются для достижения такой иерархии в программном обеспечении. Многие из наших стандартных элементов управления добавляют повышение прав с помощью z-depth и тени по умолчанию.  

### <a name="microsoftuixaml-21190218001-prerelease"></a>Microsoft.UI.Xaml 2.1.190218001 — предварительная версия

Февраль 2019 г.

[Страница выпуска GitHub](https://github.com/Microsoft/microsoft-ui-xaml/releases/tag/v2.1.190219001-prerelease)

[Скачивание пакета NuGet](https://www.nuget.org/packages/Microsoft.UI.Xaml/2.1.190218001-prerelease)

Новые экспериментальные функции:

* [Элемент управления TeachingTip](https://github.com/Microsoft/microsoft-ui-xaml/issues/21)  
  Этот новый элемент управления позволяет вашему приложению направлять и информировать пользователей в вашем приложении с помощью ненавязчивых и содержательных уведомлений. TeachingTip можно использовать для того, чтобы сфокусировать внимание на новой или важной функции, научить пользователей, как выполнять задачу, или улучшить рабочий процесс пользователя, предоставляя контекстуально подходящую информацию для выполнения поставленной задачи.

### <a name="microsoftuixaml-21190131001-prerelease"></a>Microsoft.UI.Xaml 2.1.190131001 — предварительная версия

Февраль 2019 г.

[Страница выпуска GitHub](https://github.com/Microsoft/microsoft-ui-xaml/releases/tag/v2.1.190131001-prerelease)

[Скачивание пакета NuGet](https://www.nuget.org/packages/Microsoft.UI.Xaml/2.1.190131001-prerelease)

Новые экспериментальные функции:

* [AnimatedVisualPlayer](https://docs.microsoft.com/uwp/api/microsoft.ui.xaml.controls.animatedvisualplayer)  
  Этот новый элемент управления позволяет воспроизводить сложные высокопроизводительные векторные анимации, в том числе анимации [Lottie](https://github.com/airbnb/lottie), созданные с помощью [Lottie-Windows](https://docs.microsoft.com/windows/communitytoolkit/animations/lottie).

### <a name="microsoftuixaml-21181217001-prerelease"></a>Microsoft.UI.Xaml 2.1.181217001 — предварительная версия

Декабрь 2018 г.

[Страница выпуска GitHub](https://github.com/Microsoft/microsoft-ui-xaml/releases/tag/v2.1.181217001-prerelease)

[Скачивание пакета NuGet](https://www.nuget.org/packages/Microsoft.UI.Xaml/2.1.181217001-prerelease)

Новые экспериментальные функции:

* [ItemsRepeater](https://docs.microsoft.com/uwp/api/microsoft.ui.xaml.controls.itemsrepeater)

* [RadioButtons](https://docs.microsoft.com/uwp/api/microsoft.ui.xaml.controls.radiobuttons)

* [RadioMenuFlyoutItem](https://docs.microsoft.com/uwp/api/microsoft.ui.xaml.controls.radiomenuflyoutitem)

---
title: Заметки о выпуске WinUI 2.0
description: Заметки о выпуске для WinUI 2.0.
ms.date: 04/15/2020
ms.topic: article
ms.openlocfilehash: 300a8eaa0077b3a59ef2ac01213cd82b0c76c343
ms.sourcegitcommit: d0f479f1955881afb62c2af249db5d0b053b63e5
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/19/2020
ms.locfileid: "83580661"
---
# <a name="windows-ui-library-20"></a>Библиотека пользовательского интерфейса Windows 2.0

WinUI 2.0 — это первый общедоступный выпуск библиотеки пользовательского интерфейса Windows.

WinUI — это самый простой способ создания замечательных интерфейсов Fluent Design для разработки Windows.

Он включает два пакета NuGet:

* [Microsoft.UI.Xaml](https://www.nuget.org/packages/Microsoft.UI.Xaml) — элементы управления и Fluent Design для приложений UWP. Это основной пакет WinUI.

* [Microsoft.UI.Xaml.Core.Direct](https://www.nuget.org/packages/Microsoft.UI.Xaml.Core.Direct) — низкоуровневые API для использования в компонентах ПО промежуточного слоя.

Скачать и использовать пакеты WinUI для приложения можно через диспетчера пакетов NuGet, как описано в статье [о начале работы с библиотекой пользовательского интерфейса Windows](https://docs.microsoft.com/uwp/toolkits/winui/getting-started).

WinUI — проект с открытым кодом, размещенный на сайте GitHub. Мы рады любым сообщениям об ошибках, запросам на добавление функций и вкладам сообщества в создание кода, вносимым в [репозиторий библиотеки пользовательского интерфейса Windows](https://aka.ms/winui).

## <a name="microsoftuixaml-20181011001"></a>Microsoft.UI.Xaml 2.0.181011001

Октябрь 2018 г.

Это первый выпуск [пакета NuGet Microsoft.UI.Xaml](https://www.nuget.org/packages/Microsoft.UI.Xaml). Включает в себя официальные нативные элементы управления и функции в стиле Fluent для приложений Windows UWP.

### <a name="new-features"></a>Новые возможности

Элементы управления и шаблоны в этом выпуске включают:

| Функция | Описание |
| --- | --- |
|[AcrylicBrush]( https://docs.microsoft.com/uwp/api/microsoft.ui.xaml.media.acrylicbrush)| Закрашивает область полупрозрачным материалом, который использует несколько эффектов, включая размытие и текстуру шума.|
|[BitmapIconSource]( https://docs.microsoft.com/uwp/api/microsoft.ui.xaml.controls.bitmapiconsource)| Представляет источник значков, в котором в качестве содержимого используется точечный рисунок.|
|[ColorPicker]( https://docs.microsoft.com/uwp/api/microsoft.ui.xaml.controls.colorpicker)| Представляет элемент управления, позволяющий пользователю выбирать цвет с помощью цветового спектра, ползунков и ввода текста.|
|[CommandBarFlyout](https://docs.microsoft.com/uwp/api/microsoft.ui.xaml.controls.commandbarflyout)|Представляет специализированное всплывающее окно, которое предоставляет макет для AppBarButton и связанных элементов команды.|
|[DropDownButton](https://docs.microsoft.com/uwp/api/microsoft.ui.xaml.controls.dropdownbutton)|Представляет кнопку с шевроном, предназначенным для открытия меню.|
|[FontIconSource ](https://docs.microsoft.com/uwp/api/microsoft.ui.xaml.controls.fonticonsource)|Представляет источник значков, который использует глиф из указанного шрифта.|
|[MenuBar](https://docs.microsoft.com/uwp/api/microsoft.ui.xaml.controls.menubar)|Представляет специализированный контейнер, представляющий набор меню в горизонтальной строке, обычно в верхней части окна приложения.|
|[MenuBarItem](https://docs.microsoft.com/uwp/api/microsoft.ui.xaml.controls.menubaritem)|Представляет меню верхнего уровня в элементе управления MenuBar.|
|[NavigationView](https://docs.microsoft.com/uwp/api/microsoft.ui.xaml.controls.navigationview)|Представляет контейнер, позволяющий переходить по содержимому приложения. У него есть заголовок, представление для основного содержимого и панель меню для команд навигации.|
|[ParallaxView](https://docs.microsoft.com/uwp/api/microsoft.ui.xaml.controls.parallaxview)|Представляет контейнер, связывающий положение прокрутки элемента переднего плана, например списка, к фоновому элементу, например изображению. При прокрутке элемента переднего плана он анимирует фоновый элемент, создавая эффект параллакса.|
|[PersonPicture](https://docs.microsoft.com/uwp/api/microsoft.ui.xaml.controls.personpicture)|Представляет элемент управления аватаром пользователя отображает аватар для пользователя, если он доступен; в противном случае отображаются инициалы или универсальный глиф.|
|[RatingControl](https://docs.microsoft.com/uwp/api/microsoft.ui.xaml.controls.ratingcontrol)|Представляет элемент управления, позволяющий пользователю ввести оценку по звездам.|
|[RefreshContainer](https://docs.microsoft.com/uwp/api/microsoft.ui.xaml.controls.refreshcontainer)|Представляет элемент управления контейнера, предоставляющий функции RefreshVisualizer и "Потяните, чтобы обновить" для прокручиваемого содержимого.|
|[RefreshVisualizer](https://docs.microsoft.com/uwp/api/microsoft.ui.xaml.controls.refreshvisualizer)|Представляет элемент управления, предоставляющий индикаторы анимированного состояния для обновления содержимого.|
|[RevealBackgroundBrush](https://docs.microsoft.com/uwp/api/microsoft.ui.xaml.media.revealbackgroundbrush)|Рисует фон элемента управления с эффектом проявления с помощью кисти композиции и световых эффектов.|
|[RevealBorderBrush](https://docs.microsoft.com/uwp/api/microsoft.ui.xaml.media.revealborderbrush)|Рисует границу элемента управления с эффектом проявления с помощью кисти композиции и световых эффектов.|
|[RevealBrush](https://docs.microsoft.com/uwp/api/microsoft.ui.xaml.media.revealbrush)|Базовый класс для кистей, использующих эффекты композиции и подсвечивания для реализации визуального проектирования отображения.|
|[SplitButton](https://docs.microsoft.com/uwp/api/microsoft.ui.xaml.controls.splitbutton)|Представляет кнопку, состоящую из двух частей, которые можно использовать по-отдельности. Одна часть представляет собой стандартную кнопку, а другая часть вызывает всплывающее окно.|
|[SwipeControl](https://docs.microsoft.com/uwp/api/microsoft.ui.xaml.controls.swipecontrol)|Представляет контейнер, предоставляющий доступ к контекстным командам через сенсорные взаимодействия.|
|[SymbolIconSource](https://docs.microsoft.com/uwp/api/microsoft.ui.xaml.controls.symboliconsource)|Представляет источник значков, в которой в качестве содержимого используется глиф из средств шрифта Segoe MDL2.|
|[TextCommandBarFlyout](https://docs.microsoft.com/uwp/api/microsoft.ui.xaml.controls.textcommandbarflyout)|Представляет специальное всплывающее меню панели команд, содержащее команды для редактирования текста.|
|[ToggleSplitButton](https://docs.microsoft.com/uwp/api/microsoft.ui.xaml.controls.togglesplitbutton)|Представляет кнопку, состоящую из двух частей, которые можно использовать по-отдельности. Одна часть представляет собой выключатель, а другая часть вызывает всплывающее окно.|
|[TreeView](https://docs.microsoft.com/uwp/api/microsoft.ui.xaml.controls.treeview)|Представляет иерархический список с разворачиваемыми и сворачиваемыми узлами, которые содержат вложенные элементы.|

## <a name="examples"></a>Примеры

Пример приложения XAML Controls Gallery включает интерактивные демонстрации и примеры кода для работы с элементами управления WinUI.

* Установите приложение XAML Controls Gallery из [Microsoft Store](
https://www.microsoft.com/p/xaml-controls-gallery/9msvh128x2zt).

* Также [на сайте GitHub](
https://github.com/Microsoft/Xaml-Controls-Gallery) доступен открытый код приложения Xaml Controls Gallery.

## <a name="documentation"></a>Документация

Практические руководства по элементам управления из библиотеки пользовательского интерфейса Windows вы найдете в [документации по элементам управления универсальной платформы Windows](/windows/uwp/design/controls-and-patterns/).

Справочная документация по API доступна по этой ссылке: [API библиотеки пользовательского интерфейса Windows](/uwp/api/overview/winui/).

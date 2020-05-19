---
title: Заметки о выпуске WinUI 2.4
description: Заметки о выпуске для WinUI 2.4 с описанием новых функций и исправлений ошибок.
ms.date: 05/08/2020
ms.topic: reference
ms.openlocfilehash: 88b75ae5ec0fbda545106ec0e7f7b86033808f70
ms.sourcegitcommit: d0f479f1955881afb62c2af249db5d0b053b63e5
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/19/2020
ms.locfileid: "83580311"
---
# <a name="windows-ui-library-24"></a>Библиотека пользовательского интерфейса Windows 2.4

WinUI 2.4 — это последний официальный выпуск библиотеки пользовательского интерфейса Windows (WinUI).

WinUI — это проект с открытым исходным кодом, размещенный на сайте GitHub в [репозитории библиотеки пользовательского интерфейса Windows](https://aka.ms/winui). Зарегистрируйте все отчеты об ошибках, запросам на добавление функций и вкладам сообщества в создание кода, вносимым в этот репозиторий.

Выпуски WinUI: [Страница выпуска GitHub](https://github.com/microsoft/microsoft-ui-xaml/releases)

Пакеты WinUI можно добавить в проекты Visual Studio с помощью диспетчера пакетов NuGet. Дополнительные сведения см. в статье [Начало работы с библиотекой пользовательского интерфейса Windows](../getting-started.md)

Скачивание пакета NuGet: [Microsoft.UI.Xaml](https://www.nuget.org/packages/Microsoft.UI.Xaml)

## <a name="new-features"></a>Новые возможности

### <a name="radialgradientbrush"></a>RadialGradientBrush

Объект RadialGradientBrush заполняет область внутри эллипса, который определяется свойствами Center, RadiusX и RadiusY. Начальный цвет градиента определяется в центре эллипса, а конечный — в конце радиуса.

![Кисть радиального градиента](../images/radialgradientbrush.gif)<br>
*Кисть радиального градиента*

[Рекомендации по использованию](/windows/uwp/design/style/brushes#radial-gradient-brushes)

[Справочник по API](/uwp/api/microsoft.ui.xaml.media.radialgradientbrush)

### <a name="progressring"></a>ProgressRing

Элемент управления ProgressRing используется для модальных взаимодействий, при которых действия пользователя блокируются до исчезновения ProgressRing. Используйте этот элемент управления, если для операции требуется приостановить значительную часть взаимодействий с приложением до ее завершения.

![Элемент управления ProgressRing](../images/progressring.gif)<br>
*Элемент управления ProgressRing*

[Рекомендации по использованию](/windows/uwp/design/controls-and-patterns/progress-controls)

[Справочник по API](/uwp/api/microsoft.ui.xaml.controls.progressring)

### <a name="tabview-updates"></a>Обновления TabView

Обновления элемента управления TabView обеспечивают дополнительный контроль над отображением вкладок.

Вы можете задать ширину невыбранных вкладок или отобразить только значок, чтобы сэкономить место на экране:

![Размеры вкладок элемента управления TabView](..\images\tabview-sizing.gif)<br>
*Размеры вкладок элемента управления TabView*

Можно также скрыть кнопку закрытия на невыбранных вкладках, пока пользователь не наведет на эту вкладку указатель мыши (в предыдущих версиях она отображалась всегда):

![Элемент управления TabView: наведение курсора для закрытия](..\images\tabview-closebuttononhover.gif)<br>
*Элемент управления TabView: наведение курсора для закрытия*

[Рекомендации по использованию](/windows/uwp/design/controls-and-patterns/tab-view)

[Справочник по API](/uwp/api/microsoft.ui.xaml.controls.tabview)

### <a name="dark-theme-updates-to-textbox-family-of-controls"></a>Обновление темной темы для семейства элементов управления TextBox

Когда включена темная тема, цвет фона элементов управления семейства TextBox по умолчанию остается темным при вставке текста (в предыдущих версиях цвет фона менялся на белый во время вставки текста).

| До | После |
| - | - |
| ![Обновления темной темы для TextBox (до)](..\images\textbox-darkthemeupdates-before1.gif)<br>*Обновления темной темы для TextBox (до)* | ![Обновления темной темы для TextBox (после)](..\images\textbox-darkthemeupdates-after1.gif)<br>*Обновления темной темы для TextBox (после)* |
| ![Обновления темной темы для TextBox (до)](..\images\textbox-darkthemeupdates-before2.gif)<br>*Обновления темной темы для TextBox (до)* | ![Обновления темной темы для TextBox (после)](..\images\textbox-darkthemeupdates-after2.gif)<br>*Обновления темной темы для TextBox (после)* |

Ниже перечислены некоторые элементы управления, входящие в семейство элементов управления TextBox.

- [TextBox](/uwp/api/windows.ui.xaml.controls.textbox)
- [RichEditBox](/uwp/api/windows.ui.xaml.controls.richtextblock)
- [PasswordBox](/uwp/api/windows.ui.xaml.controls.passwordbox)
- [Редактируемое поле со списком](/uwp/api/windows.ui.xaml.controls.combobox)
- [AutoSuggestBox](/uwp/api/windows.ui.xaml.controls.autosuggestbox)

### <a name="hierarchical-navigation"></a>Иерархическая навигация

Элемент управления [NavigationView](/uwp/api/microsoft.ui.xaml.controls.navigationview?view=winui-2.4) теперь поддерживает иерархическую навигацию и режимы отображения Left, Top и LeftCompact. Иерархический элемент управления NavigationView полезен для отображения категорий страниц и определения страниц со связанными дочерними страницами, а также для использования приложений со звездообразной топологией страниц, когда одна страница может быть связана со многими другими страницами.

![Иерархический элемент управления NavigationView](..\images\HierarchicalNavView.gif)<br>*Иерархический элемент управления NavigationView*

[Рекомендации по использованию](/windows/uwp/design/controls-and-patterns/navigationview#hierarchical-navigation)

[Справочник по API](/uwp/api/microsoft.ui.xaml.controls.navigationview)

## <a name="samples"></a>примеры

Для каждой из описанных здесь функций WinUI 2.4 есть примеры в **коллекции элементов управления XAML**.

Установите приложение XAML Controls Gallery из [Microsoft Store](https://www.microsoft.com/p/xaml-controls-gallery/9msvh128x2zt), если оно еще не установлено.

Также на сайте [GitHub](https://github.com/Microsoft/Xaml-Controls-Gallery) доступен исходный код приложения XAML Controls Gallery.

---
title: Заметки о выпуске WinUI 2.2
description: Заметки о выпуске для WinUI 2.2, который включает новые функции и исправления ошибок.
ms.date: 04/15/2020
ms.topic: article
ms.openlocfilehash: 2272046bc59865ebcf7958a165805d9ca4c7efce
ms.sourcegitcommit: d0f479f1955881afb62c2af249db5d0b053b63e5
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/19/2020
ms.locfileid: "83580471"
---
# <a name="windows-ui-library-22"></a>Библиотека пользовательского интерфейса Windows 2.2

WinUI 2.2 — это последний официальный выпуск библиотеки пользовательского интерфейса Windows

Вы можете добавлять пакеты WinUI для приложения через диспетчер пакетов NuGet, как описано в статье [Начало работы с библиотекой пользовательского интерфейса Windows](../getting-started.md).

WinUI — проект с открытым кодом, размещенный на сайте GitHub. Мы рады любым сообщениям об ошибках, запросам на добавление функций и вкладам сообщества в создание кода, вносимым в [репозиторий библиотеки пользовательского интерфейса Windows](https://aka.ms/winui).

## <a name="microsoftuixaml-22-version-history"></a>Журнал версий Microsoft.UI.Xaml 2.2

### <a name="windows-ui-library-22-official-release"></a>Официальный выпуск библиотеки пользовательского интерфейса Windows 2.2

АВГУСТ 2019 г.

[Страница выпуска GitHub](https://github.com/microsoft/microsoft-ui-xaml/releases)

[Скачивание пакета NuGet](https://www.nuget.org/packages/Microsoft.UI.Xaml)

### <a name="new-features"></a>Новые возможности

#### <a name="tabview"></a>TabView

![Пример](../images/tabview-gif.gif)

#### <a name="description"></a>Описание

Элемент управления TabView — это коллекция вкладок, каждая из которых представляет новую страницу или документ в приложении. TabView полезен, если в приложении есть несколько страниц содержимого, и пользователь должен иметь возможность добавлять, закрывать и переупорядочивать вкладки. Новый [Терминал Windows](https://github.com/Microsoft/Terminal) использует TabView для отображения нескольких интерфейсов командной строки.

#### <a name="documentation"></a>Документация

https://docs.microsoft.com/uwp/api/microsoft.ui.xaml.controls.tabview?view=winui-2.2

#### <a name="navigationview-updates"></a>Обновления NavigationView

##### <a name="a-navigationviews-back-button-update"></a>a) Обновление кнопки "Назад" NavigationView

![Пример](../images/navigationview-back-button.gif)

##### <a name="description"></a>Описание

В минимальном режиме NavigationView кнопка "Назад" больше не исчезает. При открытии и закрытии панели пользователям больше не нужно перемещать курсор для нажатия кнопки-гамбургер. Эта функция будет работать по умолчанию. Для выполнения этой работы не нужно вносить изменения в код.

##### <a name="b-navigationview---no-auto-padding"></a>б) NavigationView — без автозаполнения

![Пример](../images/navigationview-no-auto-padding.png)

##### <a name="description"></a>Описание

Разработчики приложений теперь могут высвобождать все пиксели в окне приложения, если используют элемент управления NavigationView и переходят в область заголовка окна.

##### <a name="documentation"></a>Документация

https://docs.microsoft.com/windows/uwp/design/controls-and-patterns/navigationview#top-whitespace

#### <a name="visual-style-updates"></a>Обновления визуального стиля

##### <a name="a-corner-radius-update"></a>a) Обновление радиуса угла

![Пример](../images/corner-radius.png)

##### <a name="description"></a>Описание

Добавлен атрибут CornerRadius. Элементы управления по умолчанию обновлены для использования немного скругленных углов. Разработчики могут легко настроить радиус угла, чтобы при желании придать приложению уникальный вид.

##### <a name="github-spec-link"></a>Ссылка на спецификацию GitHub

https://github.com/microsoft/microsoft-ui-xaml/issues/524

##### <a name="b-border-thickness-update"></a>б) Изменение толщины границы

![Пример](../images/border-thickness.png)

##### <a name="description"></a>Описание

Свойство BorderThickness теперь проще настраивать. Элементы управления по умолчанию были обновлены, чтобы уменьшить контуры для более четкого и привычного вида.

##### <a name="github-spec-link"></a>Ссылка на спецификацию GitHub

https://github.com/microsoft/microsoft-ui-xaml/issues/835

##### <a name="c-button-visual-update"></a>c) Обновление визуального элемента кнопки

![Пример](../images/button-hover-visual-update.png)

##### <a name="description"></a>Описание: 
Визуальный элемент кнопки по умолчанию был обновлен для удаления структуры, которая отображалась во время наведения, чтобы придать ей более приятный вид.

##### <a name="github-spec-link"></a>Ссылка на спецификацию GitHub:  
https://github.com/microsoft/microsoft-ui-xaml/issues/953

##### <a name="d-splitbutton-visual-update"></a>г) Обновление визуального элемента SplitButton

![Пример](../images/splitbutton-visual-update.png)

##### <a name="description"></a>Описание: 
Стандартный визуальный элемент SplitButton обновлен, чтобы упростить его различение от DropDownButton.

##### <a name="github-spec-link"></a>Ссылка на спецификацию GitHub: 
https://github.com/microsoft/microsoft-ui-xaml/issues/986

##### <a name="e-toggleswitch-visual-update"></a>д) Обновление визуального элемента ToggleSwitch

![Пример](../images/toggleswitch-update.png)

##### <a name="description"></a>Описание: 
По умолчанию ширина ToggleSwitch уменьшена с 44 пкс до 40 пкс, поэтому она сбалансирована визуально, сохраняя при этом удобство использования.

##### <a name="github-spec-link"></a>Ссылка на спецификацию GitHub: 
https://github.com/microsoft/microsoft-ui-xaml/issues/836

##### <a name="f-checkbox-and-radiobutton-visual-update"></a>f) Обновление визуальных элементов CheckBox и RadioButton

![Пример](../images/checkbox-radiobutton.png)

##### <a name="description"></a>Описание: 
Визуальные элементы CheckBox и RadioButton обновлены, чтобы соответствовать остальным изменениям стиля визуальных элементов.

##### <a name="github-spec-link"></a>Ссылка на спецификацию GitHub: 
https://github.com/microsoft/microsoft-ui-xaml/issues/839

## <a name="examples"></a>Примеры

Пример приложения XAML Controls Gallery включает интерактивные демонстрации и примеры кода для работы с элементами управления WinUI.

* Установите приложение XAML Controls Gallery из [Microsoft Store](
https://www.microsoft.com/p/xaml-controls-gallery/9msvh128x2zt).

* Также [на сайте GitHub](
https://github.com/Microsoft/Xaml-Controls-Gallery) доступен открытый код приложения Xaml Controls Gallery.

## <a name="documentation"></a>Документация

Практические руководства по элементам управления из библиотеки пользовательского интерфейса Windows вы найдете в [документации по элементам управления универсальной платформы Windows](/windows/uwp/design/controls-and-patterns/).

Справочная документация по API доступна по этой ссылке: [API библиотеки пользовательского интерфейса Windows](/uwp/api/overview/winui/).

## <a name="microsoftuixaml-22-version-history"></a>Журнал версий Microsoft.UI.Xaml 2.2

### <a name="microsoftuixaml-22190702001-prerelease"></a>Microsoft.UI.Xaml 2.2.190702001 — предварительная версия

Июль 2019 г.

[Страница выпуска GitHub](https://github.com/microsoft/microsoft-ui-xaml/releases/tag/v2.2.190702001-prerelease)

[Скачивание пакета NuGet](https://www.nuget.org/packages/Microsoft.UI.Xaml/2.2.190702001-prerelease)

### <a name="experimental-feature"></a>Экспериментальные функции

* [TabView](https://docs.microsoft.com/uwp/api/microsoft.ui.xaml.controls.tabview?view=winui-2.2)

### <a name="microsoftuixaml-2220190416001-prerelease"></a>Microsoft.UI.Xaml 2.2.20190416001 — предварительная версия

Апрель 2019 г.

[Страница выпуска GitHub](https://github.com/Microsoft/microsoft-ui-xaml/releases/tag/v2.2.190416008-prerelease)

[Скачивание пакета NuGet](https://www.nuget.org/packages/Microsoft.UI.Xaml/2.2.190416008-prerelease)

#### <a name="experimental-features"></a>экспериментальные функции;

* [FlowLayout](https://docs.microsoft.com/uwp/api/microsoft.ui.xaml.controls.flowlayout)

* [LayoutPanel](https://docs.microsoft.com/uwp/api/microsoft.ui.xaml.controls.layoutpanel)

* [RadioButtons](https://docs.microsoft.com/uwp/api/microsoft.ui.xaml.controls.radiobuttons)

* [ScrollViewer](https://docs.microsoft.com/uwp/api/microsoft.ui.xaml.controls.scrollviewer)

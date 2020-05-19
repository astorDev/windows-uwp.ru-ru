---
title: Заметки о выпуске WinUI 2.3
description: Заметки о выпуске для WinUI 2.3, включающий новые функции и исправления ошибок.
ms.date: 04/15/2020
ms.topic: article
ms.openlocfilehash: a61932a6f0060a4be79424e02aad3dd312128aef
ms.sourcegitcommit: d0f479f1955881afb62c2af249db5d0b053b63e5
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/19/2020
ms.locfileid: "83580461"
---
# <a name="windows-ui-library-23"></a>Библиотека пользовательского интерфейса Windows версии 2.3

WinUI 2.3 — это последний официальный выпуск библиотеки пользовательского интерфейса Windows (WinUI).

WinUI — это проект с открытым исходным кодом, размещенный на сайте GitHub в [репозитории библиотеки пользовательского интерфейса Windows](https://aka.ms/winui). Зарегистрируйте все отчеты об ошибках, запросам на добавление функций и вкладам сообщества в создание кода, вносимым в этот репозиторий.

Выпуски WinUI: [Страница выпуска GitHub](https://github.com/microsoft/microsoft-ui-xaml/releases)

Пакеты WinUI можно добавить в проекты Visual Studio с помощью диспетчера пакетов NuGet. Дополнительные сведения см. в статье [Начало работы с библиотекой пользовательского интерфейса Windows](../getting-started.md)

Скачивание пакета NuGet: [Microsoft.UI.Xaml](https://www.nuget.org/packages/Microsoft.UI.Xaml)

## <a name="new-features"></a>Новые возможности

### <a name="progress-bar-visual-refresh"></a>Обновление визуального элемента индикатора выполнения

**ProgressBar** имеет два различные представления визуальных элементов.

#### <a name="indeterminate-progress-bar"></a>Неопределенный индикатор выполнения

Показывает, что задача является текущей, но не блокирует взаимодействие с пользователем.

![Неопределенный индикатор выполнения](../images/IndeterminateProgressBar.gif)

#### <a name="determinate-progress-bar"></a>Определенный индикатор выполнения

Показывает, какой объем работ выполнен на известном объеме работы. 

![Определенный индикатор выполнения](../images/DeterminateProgressBar.gif)

[Ссылка на документ](https://docs.microsoft.com/windows/uwp/design/controls-and-patterns/progress-controls)

[Ссылка на образец](https://docs.microsoft.com/windows/uwp/design/controls-and-patterns/progress-controls#examples)

### <a name="numberbox"></a>NumberBox

**NumberBox** представляет элемент управления, который можно использовать для отображения и редактирования чисел. Он поддерживает верификацию, пошаговое добавление и последовательные вычисления основных уравнений, таких как умножение, деление, сложение и вычитание.

![NumberBox](../images/NumberBoxGif.gif)

[Ссылка на документ и образец](https://docs.microsoft.com/windows/uwp/design/controls-and-patterns/number-box)

### <a name="radiobuttons"></a>RadioButtons

**RadioButtons** — это новый элемент управления контейнера, позволяющий легко создавать связанные группы элементов RadioButton, одновременно обеспечивая правильную поддержку ввода с клавиатуры и функции диктора / экранного считывателя.

![RadioButtons](../images/RadioButtons.png)

[Ссылка на документ и образец](https://github.com/microsoft/microsoft-ui-xaml-specs/blob/c8d3d3668af546091656dfc37436b13cd062f52d/active/radiobuttons/RadioButtons_Spec.md)

## <a name="examples"></a>Примеры

Пример приложения XAML Controls Gallery включает интерактивные демонстрации и примеры кода для работы с элементами управления WinUI.

* Установите приложение XAML Controls Gallery из [Microsoft Store](
https://www.microsoft.com/p/xaml-controls-gallery/9msvh128x2zt).

* Также [на сайте GitHub](
https://github.com/Microsoft/Xaml-Controls-Gallery) доступен открытый код приложения Xaml Controls Gallery.

## <a name="documentation"></a>Документация

Практические руководства по элементам управления из библиотеки пользовательского интерфейса Windows вы найдете в [документации по элементам управления универсальной платформы Windows](/windows/uwp/design/controls-and-patterns/).

Справочная документация по API доступна по этой ссылке: [API библиотеки пользовательского интерфейса Windows](/uwp/api/overview/winui/).

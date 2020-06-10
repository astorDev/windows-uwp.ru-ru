---
description: Из этого раздела вы узнаете, как добавить простую поддержку WinUI в проект C++/WinRT.
title: Простой пример библиотеки пользовательского интерфейса Windows для C++/WinRT
ms.date: 07/12/2019
ms.topic: article
keywords: windows 10, uwp, standard, c++, cpp, winrt, Windows UI Library, WinUI
ms.localizationpriority: medium
ms.openlocfilehash: 8242055e3c448e2720226859f2ea10e1ae54794f
ms.sourcegitcommit: db48036af630f33f0a2f7a908bfdfec945f3c241
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/05/2020
ms.locfileid: "84437138"
---
# <a name="a-simple-cwinrt-windows-ui-library-example"></a>Простой пример библиотеки пользовательского интерфейса Windows для C++/WinRT

Из этого раздела вы узнаете, как добавить простую поддержку [библиотеки пользовательского интерфейса Windows (WinUI)](https://github.com/Microsoft/microsoft-ui-xaml) в проект C++/WinRT. К слову говоря, сама библиотека пользовательского интерфейса Windows написана на C++/WinRT.

> [!NOTE]
> Набор средств библиотеки пользовательского интерфейса Windows (WinUI) доступен в виде пакетов NuGet. Их можно добавить в любой существующий или новый проект с помощью Visual Studio, как показано ниже. См. подробнее о настройке и использовании в руководстве по [началу работы с библиотекой пользовательского интерфейса Windows](/uwp/toolkits/winui/getting-started).

## <a name="create-a-blank-app-hellowinuicppwinrt"></a>Создание пустого приложения (HelloWinUICppWinRT)

В Visual Studio создайте проект с помощью шаблона проекта **Пустое приложение (C++/WinRT)** . Убедитесь, что вы используете шаблон **(C++/WinRT)** , а не **(Универсальное приложение для Windows)** .

Задайте для нового проекта имя *HelloWinUICppWinRT* и (чтобы структура папок соответствовала структуре, приведенной в руководстве) снимите флажок **Поместить решение и проект в одном каталоге**.

## <a name="install-the-microsoftuixaml-nuget-package"></a>Установка пакета Microsoft.UI.Xaml NuGet

Щелкните **Проект** \> **Управление пакетами NuGet** \> **Обзор**, введите или вставьте **Microsoft.UI.Xaml** в поле поиска, выберите элемент в результатах поиска, а затем щелкните **Установить**, чтобы установить пакет для проекта (отобразится лицензионное соглашение). Будьте внимательны — нужно установить пакет **Microsoft.UI.Xaml**, а не **Microsoft.UI.Xaml.Core.Direct**.

## <a name="declare-winui-application-resources"></a>Объявление ресурсов приложения WinUI

Откройте `App.xaml` и вставьте следующую разметку между существующими открывающим и закрывающим тегами **Application**.

```xaml
<Application.Resources>
    <XamlControlsResources xmlns="using:Microsoft.UI.Xaml.Controls"/>
</Application.Resources>
```

## <a name="add-a-winui-control-to-mainpage"></a>Добавление элемента управления WinUI в MainPage

Откройте `MainPage.xaml`. В открывающем теге **Page** есть объявления пространств имен XML. Добавьте `xmlns:muxc="using:Microsoft.UI.Xaml.Controls"` объявления пространства имен XML. Затем вставьте следующую разметку между открывающим и закрывающим тегами **Page**, чтобы перезаписать существующий элемент **StackPanel**.

```xaml
<muxc:NavigationView PaneTitle="Welcome">
    <TextBlock Text="Hello, World!" VerticalAlignment="Center" HorizontalAlignment="Center" Style="{StaticResource TitleTextBlockStyle}"/>
</muxc:NavigationView>
```

## <a name="edit-pchh-as-necessary"></a>Внесение изменений в файл pch.h

Когда вы добавляете пакет NuGet в проект C++/WinRT (например, пакет **Microsoft.UI.Xaml**, добавленный ранее) и создаете проект, средство создает набор заголовков проекции в папке проекта `\Generated Files\winrt`. Если вы выполнили инструкции из руководства, теперь у вас должна быть папка `\HelloWinUICppWinRT\HelloWinUICppWinRT\Generated Files\winrt`. Чтобы поместить эти файлы с заголовками в свой проект и чтобы ссылки на эти новые типы работали, можно перейти к предварительно откомпилированному файлу заголовка (обычно `pch.h`) и включить их в файл проекта.

Включите только заголовки для типов, которые используете. В примере ниже включены все созданные файлы заголовков для пакета **Microsoft.UI.Xaml**.

```cppwinrt
// pch.h
...
#include "winrt/Microsoft.UI.Xaml.Automation.Peers.h"
#include "winrt/Microsoft.UI.Xaml.Controls.h"
#include "winrt/Microsoft.UI.Xaml.Controls.Primitives.h"
#include "winrt/Microsoft.UI.Xaml.Media.h"
#include "winrt/Microsoft.UI.Xaml.XamlTypeInfo.h"
...
```

## <a name="edit-mainpagecpp"></a>Внесение изменений в файл MainPage.cpp

В `MainPage.cpp` удалите код внутри реализации **MainPage::ClickHandler**, так как *myButton* больше не находится в разметке XAML.

Теперь выполните сборку и запустите проект.

![Снимок экрана: простой пример библиотеки пользовательского интерфейса Windows для C++/WinRT](images/winui.png)

## <a name="related-topics"></a>Связанные темы
* [Начало работы с библиотекой пользовательского интерфейса Windows](/uwp/toolkits/winui/getting-started)
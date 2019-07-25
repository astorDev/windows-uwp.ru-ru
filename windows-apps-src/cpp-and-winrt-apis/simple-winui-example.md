---
description: Из этого раздела вы узнаете, как добавить простую поддержку WinUI в проект C++/WinRT.
title: Простой пример библиотеки пользовательского интерфейса Windows для C++/WinRT
ms.date: 07/12/2019
ms.topic: article
keywords: windows 10, uwp, standard, c++, cpp, winrt, Windows UI Library, WinUI
ms.localizationpriority: medium
ms.openlocfilehash: 082e7ca0684495e1f67c2fa79b448866f68a059c
ms.sourcegitcommit: cba3ba9b9a9f96037cfd0e07d05bd4502753c809
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/14/2019
ms.locfileid: "67870349"
---
# <a name="a-simple-cwinrt-windows-ui-library-example"></a>Простой пример библиотеки пользовательского интерфейса Windows для C++/WinRT

Из этого раздела вы узнаете, как добавить простую поддержку библиотеки пользовательского интерфейса Windows (WinUI) в проект C++/WinRT.

> [!NOTE]
> Набор средств библиотеки пользовательского интерфейса Windows (WinUI) доступен в виде пакетов NuGet. Их можно добавить в любой существующий или новый проект с помощью Visual Studio, как показано ниже. См. подробнее о настройке и использовании в руководстве по [началу работы с библиотекой пользовательского интерфейса Windows](/uwp/toolkits/winui/getting-started).

## <a name="create-a-blank-app-hellowinuicppwinrt"></a>Создание пустого приложения (HelloWinUICppWinRT)

В Visual Studio создайте проект с помощью шаблона проекта **Пустое приложение (C++/WinRT)** и назовите его *HelloWinUICppWinRT*.

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

Откройте `MainPage.xaml`. В открывающем теге **Application** есть объявления пространств имен XML. Добавьте `xmlns:muxc="using:Microsoft.UI.Xaml.Controls"` объявления пространства имен XML. Затем вставьте следующую разметку между открывающим и закрывающим тегами **Page**, чтобы перезаписать существующий элемент **StackPanel**.

```xaml
<muxc:NavigationView PaneTitle="Welcome">
    <TextBlock Text="Hello, World!" VerticalAlignment="Center" HorizontalAlignment="Center" Style="{StaticResource TitleTextBlockStyle}"/>
</muxc:NavigationView>
```

## <a name="edit-mainpageh-and-cpp-as-necessary"></a>Изменение MainPage.h и .cpp (при необходимости)

В `MainPage.h` измените все, что вы включили, чтобы код выглядел следующим образом.

```cppwinrt
#include "MainPage.g.h"
#include "winrt/Microsoft.UI.Xaml.Controls.h"
#include "winrt/Microsoft.UI.Xaml.XamlTypeInfo.h"
```

И, наконец, в `MainPage.cpp` удалите код внутри реализации **MainPage::ClickHandler**, так как *MyButton* больше не находится в разметке XAML.

Теперь выполните сборку и запустите проект.

![Снимок экрана: простой пример библиотеки пользовательского интерфейса Windows для C++/WinRT](images/winui.png)

## <a name="related-topics"></a>Статьи по теме
* [Начало работы с библиотекой пользовательского интерфейса Windows](/uwp/toolkits/winui/getting-started)
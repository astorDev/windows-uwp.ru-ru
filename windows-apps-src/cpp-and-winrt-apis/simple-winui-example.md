---
description: Из этого раздела вы узнаете, как добавить простую поддержку WinUI в проект C++/WinRT.
title: Простой пример библиотеки пользовательского интерфейса Windows для C++/WinRT
ms.date: 07/12/2019
ms.topic: article
keywords: windows 10, uwp, standard, c++, cpp, winrt, Windows UI Library, WinUI
ms.localizationpriority: medium
ms.openlocfilehash: aadf177bc4a44f67550dba1f6f706525b8460857
ms.sourcegitcommit: c9bab19599c0eb2906725fd86d0696468bb919fa
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/03/2020
ms.locfileid: "78256177"
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

## <a name="edit-mainpagecpp-and-h-as-necessary"></a>При необходимости измените MainPage.cpp и .h.

В `MainPage.cpp` удалите код внутри реализации **MainPage::ClickHandler**, так как *myButton* больше не находится в разметке XAML.

В `MainPage.h` измените все, что вы включили, чтобы код выглядел, как показано ниже. Если вам нужно использовать WinUI из нескольких страниц XAML, можно перейти к файлу предкомпилированных заголовков (обычно это `pch.h`) и включить в нем требуемые файлы.

```cppwinrt
#include "MainPage.g.h"
#include "winrt/Microsoft.UI.Xaml.Controls.h"
#include "winrt/Microsoft.UI.Xaml.XamlTypeInfo.h"
```

Теперь создайте проект.

Когда вы добавляете пакет NuGet в проект C++/WinRT (например, пакет **Microsoft.UI.Xaml**, добавленный ранее) и создаете проект, средство создает набор заголовков проекции в папке проекта `\Generated Files\winrt`. Если вы выполнили инструкции из руководства, теперь у вас должна быть папка `\HelloWinUICppWinRT\HelloWinUICppWinRT\Generated Files\winrt`. Изменения, внесенные в `MainPage.h` выше, приводят к тому, что эти файлы заголовков проекции включаются в проект. И это требуется для разрешения ссылок на типы в пакете NuGet.

Теперь вы можете запустить проект.

![Снимок экрана: простой пример библиотеки пользовательского интерфейса Windows для C++/WinRT](images/winui.png)

## <a name="related-topics"></a>Связанные темы
* [Начало работы с библиотекой пользовательского интерфейса Windows](/uwp/toolkits/winui/getting-started)
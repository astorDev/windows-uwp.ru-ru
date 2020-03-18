---
description: Из этого раздела вы узнаете, как добавить простую поддержку WinUI в проект C++/WinRT.
title: Простой пример библиотеки пользовательского интерфейса Windows для C++/WinRT
ms.date: 07/12/2019
ms.topic: article
keywords: windows 10, uwp, standard, c++, cpp, winrt, Windows UI Library, WinUI
ms.localizationpriority: medium
ms.openlocfilehash: 0dce8e7ea08b18921f228b3da2e679a9edb02228
ms.sourcegitcommit: ca1b5c3ab905ebc6a5b597145a762e2c170a0d1c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79200982"
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

В `MainPage.h` измените все, что вы включили, чтобы код выглядел, как показано ниже.

```cppwinrt
#include "MainPage.g.h"
#include "winrt/Microsoft.UI.Xaml.Controls.h"
#include "winrt/Microsoft.UI.Xaml.XamlTypeInfo.h"
```

Теперь создайте проект.

Когда вы добавляете пакет NuGet в проект C++/WinRT (например, пакет **Microsoft.UI.Xaml**, добавленный ранее) и создаете проект, средство создает набор заголовков проекции в папке проекта `\Generated Files\winrt`. Если вы выполнили инструкции из руководства, теперь у вас должна быть папка `\HelloWinUICppWinRT\HelloWinUICppWinRT\Generated Files\winrt`. В результате изменений, внесенных в `MainPage.h` выше, эти файлы заголовков проекции для WinUI становятся видимыми для **MainPage**. Это необходимо для разрешения ссылки на тип **Microsoft::UI::Xaml::Controls::NavigationView** в **MainPage**.

> [!IMPORTANT]
> В реальном приложении необходимо, чтобы файлы заголовков проекции WinUI были видимыми для *всех* страниц XAML в проекте, а не только для **MainPage**. В этом случае необходимо переместить включения двух файлов заголовков проекции WinUI в файл предкомпилированного заголовка (обычно это `pch.h`). Затем в любой части проекта будут разрешены ссылки на типы в пакете NuGet. Для минимального одностраничного приложения, создаваемого в этом пошаговом руководстве, не нужно использовать `pch.h`. Подходящим вариантом является включение заголовков в `MainPage.h`.

Теперь вы можете запустить проект.

![Снимок экрана: простой пример библиотеки пользовательского интерфейса Windows для C++/WinRT](images/winui.png)

## <a name="related-topics"></a>Связанные темы
* [Начало работы с библиотекой пользовательского интерфейса Windows](/uwp/toolkits/winui/getting-started)
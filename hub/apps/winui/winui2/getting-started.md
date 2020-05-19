---
title: Начало работы с библиотекой пользовательского интерфейса Windows
description: Установка и использование библиотеки пользовательского интерфейса Windows.
ms.topic: reference
ms.date: 05/08/2020
keywords: windows 10, uwp, пакет средств разработки
ms.openlocfilehash: 2205a95dfd342503674662d5cb0e4ed598eda870
ms.sourcegitcommit: d0f479f1955881afb62c2af249db5d0b053b63e5
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/19/2020
ms.locfileid: "83580411"
---
# <a name="getting-started-with-the-windows-ui-library"></a>Начало работы с библиотекой пользовательского интерфейса Windows

[WinUI 2.4](release-notes/winui-2.4.md) — это последняя стабильная версия WinUI, ее следует использовать для приложений в рабочей среде.

Эта библиотека доступна в виде пакета NuGet, который можно добавить в любой новый или существующий проект Visual Studio.

> [!NOTE]
> Дополнительные сведения о возможности ознакомиться с предварительными версиями WinUI 3.0 см. в статье [WinUI 3.0 Alpha](../winui3/index.md).

## <a name="download-and-install-the-windows-ui-library"></a>Загрузка и установка библиотеки пользовательского интерфейса Windows

1. Скачайте [Visual Studio 2019](https://developer.microsoft.com/windows/downloads) и убедитесь, что в установщике Visual Studio выбрана рабочая нагрузка **Разработка приложений для универсальной платформы Windows**.

2. Откройте существующий проект или создайте новый проект с помощью шаблона "Пустое приложение" в Visual C# -> Windows -> Универсальный, или соответствующий шаблон для вашей языковой проекции.  

    > [!IMPORTANT]
    > Чтобы использовать WinUI 2.4, в свойствах проекта нужно задать TargetPlatformVersion >= 10.0.18362.0 и TargetPlatformMinVersion >= 10.0.15063.0.

3. На панели обозревателя решений щелкните название проекта правой кнопкой мыши и выберите **Управление пакетами NuGet**. Перейдите на вкладку **Обзор** и выполните поиск **Microsoft.UI.Xaml** или **WinUI**. Затем выберите, какие [Пакеты NuGet для библиотеки пользовательского интерфейса Windows](nuget-packages.md) вы хотите использовать.
Пакет **Microsoft.UI.Xaml** содержит элементы управления Fluent и функции, которые подходят для всех приложений.  
При необходимости можно установить флажок "Включить предварительные выпуски", чтобы просмотреть последние предварительные версии, включающие экспериментальные новые функции.

    ![Пакеты NuGet](images/ManageNugetPackages.png "Управление образом пакетов NuGet")

    ![Пакеты NuGet](images/NugetPackages.png)

4. Добавьте ресурсы темы пользовательского интерфейса Windows (WinUI) в ресурсы App.xaml. Это можно сделать двумя способами, в зависимости от наличия дополнительных ресурсов приложения.

    a. Если у вас нет других ресурсов приложения, добавьте `<XamlControlsResources xmlns="using:Microsoft.UI.Xaml.Controls"/>` в Application.Resources: 

    ``` XAML
    <Application>
        <Application.Resources>
            <XamlControlsResources xmlns="using:Microsoft.UI.Xaml.Controls" /> 
        </Application.Resources>
    </Application>
    ```

    b. В противном случае, если имеется более одного набора ресурсов приложения, добавьте `<XamlControlsResources xmlns="using:Microsoft.UI.Xaml.Controls"/>` в Application.Resources.MergedDictionaries:

    ``` XAML
    <Application>
        <Application.Resources>
            <ResourceDictionary>
                <ResourceDictionary.MergedDictionaries>
                    <XamlControlsResources xmlns="using:Microsoft.UI.Xaml.Controls" />
                </ResourceDictionary.MergedDictionaries> 
            </ResourceDictionary>
        </Application.Resources>
    </Application>
    ```

    > [!IMPORTANT]
    > Порядок, в котором ресурсы добавляются в ResourceDictionary, влияет на порядок, в котором они применяются. Словарь `XamlControlsResources` переопределяет множество ключей ресурсов по умолчанию и поэтому должен быть добавлен к `Application.Resources`, чтобы не переопределять другие пользовательские стили или ресурсы в приложении. Дополнительные сведения о загрузке ресурсов см. в статье [Ссылки на ресурсы ResourceDictionary и XAML](https://docs.microsoft.com/windows/uwp/design/controls-and-patterns/resourcedictionary-and-xaml-resource-references).

5. Добавьте ссылку на набор средств к страницам XAML и страницам кода программной части.

    * На странице XAML добавьте ссылку в верхней части страницы.

        ```xaml
        xmlns:controls="using:Microsoft.UI.Xaml.Controls"
        ```

    * В коде (если вы хотите использовать имена типов без их уточнения), можно добавить директиву using.

        ```csharp
        using MUXC = Microsoft.UI.Xaml.Controls;
        ```

## <a name="additional-steps-for-a-cwinrt-project"></a>Дополнительные шаги для проекта C++/WinRT

Когда вы добавляете пакет NuGet в проект C++/WinRT, средство создает набор заголовков проекции в папке проекта `\Generated Files\winrt`. Чтобы поместить эти файлы с заголовками в свой проект и чтобы ссылки на эти новые типы работали, можно перейти к предварительно откомпилированному файлу заголовка (обычно `pch.h`) и включить их в файл проекта. Ниже приведен пример, включающий созданные файлы заголовков для пакета **Microsoft.UI.Xaml**.

```cppwinrt
// pch.h
...
#include "winrt/Microsoft.UI.Xaml.Automation.Peers.h"
#include "winrt/Microsoft.UI.Xaml.Controls.Primitives.h"
#include "winrt/Microsoft.UI.Xaml.Media.h"
#include "winrt/Microsoft.UI.Xaml.XamlTypeInfo.h"
...
```

Полное пошаговое руководство по добавлению простой поддержки библиотеки пользовательского интерфейса Windows в проект C++/WinRT см. в статье [Простой пример библиотеки пользовательского интерфейса Windows для C++/WinRT](/windows/uwp/cpp-and-winrt-apis/simple-winui-example).

## <a name="contributing-to-the-windows-ui-library"></a>Дополнение к библиотеке пользовательского интерфейса Windows

WinUI — проект с открытым кодом, размещенный на сайте GitHub.

Мы рады любым сообщениям об ошибках, запросам на добавление функций и вкладам сообщества в создание кода, вносимым в [репозиторий библиотеки пользовательского интерфейса Windows](https://aka.ms/winui).

## <a name="other-resources"></a>Другие ресурсы 

Если вы не знакомы с UWP, рекомендуем ознакомится со статьей [Приступая к работе с приложениями для Windows 10](https://developer.microsoft.com/windows/getstarted) на портале разработчика.

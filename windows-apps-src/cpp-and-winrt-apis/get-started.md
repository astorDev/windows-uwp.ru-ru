---
description: Чтобы приступить к работе с C++/WinRT, в этой статье описывается простой пример кода.
title: Начало работы с C++/WinRT
ms.date: 10/19/2018
ms.topic: article
keywords: Windows 10, UWP, стандартные, c++, cpp, winrt, проекция, начать, начало, работы
ms.localizationpriority: medium
ms.openlocfilehash: c0d11a8718f61666d6285d8a1c91b48992044b22
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57602239"
---
# <a name="get-started-with-cwinrt"></a>Начало работы с C++/WinRT

Которые помогут вам начать работу с с помощью [C + +/ WinRT](/windows/uwp/cpp-and-winrt-apis/intro-to-using-cpp-with-winrt), в этом разделе рассматривается простой пример кода на новый основе **консольное приложение Windows (C + +/ WinRT)** проекта. В этом разделе также показано, как [добавьте C + +/ WinRT поддержка в проект приложения Windows Desktop](#modify-a-windows-desktop-application-project-to-add-cwinrt-support).

> [!IMPORTANT]
> Если вы используете Visual Studio 2017 (версии 15.8.0 или более поздней версии) и определение целевых объектов пакет Windows SDK версии 10.0.17134.0 (Windows 10 версии 1803), затем только что созданный C + +/ WinRT проект может не компилироваться с ошибкой "*ошибки C3861: «from_abi»: Идентификатор не найден*«и других ошибок, происходящих в *base.h*. Решением является более поздней версии (лучше соответствует стандарту) либо целевой версии пакета SDK для Windows, или задать свойство проекта **C/C++** > **языка** > **режим совместимости: Не** (Кроме того, если **/ permissive-** отображается в свойстве проекта **C/C++** > **языка** > **командной строки**  под **Дополнительные параметры**, удалите его).

## <a name="a-cwinrt-quick-start"></a>Краткое руководство по C++/WinRT

> [!NOTE]
> Сведения об установке и использовании C + +/ WinRT Visual Studio Extension (VSIX) (который поддерживает шаблон проекта) см. в разделе [поддержка Visual Studio для C + +/ WinRT](intro-to-using-cpp-with-winrt.md#visual-studio-support-for-cwinrt-xaml-the-vsix-extension-and-the-nuget-package).

Создайте новый проект **консольного приложение для Windows (C++/WinRT)**.

Отредактируйте файлы `pch.h` и `main.cpp` следующим образом.

```cppwinrt
// pch.h
...
#include <iostream>
#include <winrt/Windows.Foundation.h>
#include <winrt/Windows.Web.Syndication.h>
...
```

```cppwinrt
// main.cpp
#include "pch.h"

using namespace winrt;
using namespace Windows::Foundation;
using namespace Windows::Web::Syndication;

int main()
{
    winrt::init_apartment();

    Uri rssFeedUri{ L"https://blogs.windows.com/feed" };
    SyndicationClient syndicationClient;
    SyndicationFeed syndicationFeed = syndicationClient.RetrieveFeedAsync(rssFeedUri).get();
    for (const SyndicationItem syndicationItem : syndicationFeed.Items())
    {
        winrt::hstring titleAsHstring = syndicationItem.Title().Text();
        std::wcout << titleAsHstring.c_str() << std::endl;
    }
}
```

Подробно рассмотрим небольшой пример кода выше и объясним, что происходит в каждой части.

```cppwinrt
#include <winrt/Windows.Foundation.h>
#include <winrt/Windows.Web.Syndication.h>
```

Включенные заголовки входят в состав пакета SDK, который находится в папке `%WindowsSdkDir%Include<WindowsTargetPlatformVersion>\cppwinrt\winrt`. Visual Studio включает этот путь в своем макросе *IncludePath*. Заголовки содержат API-интерфейсы Windows, проецируемые в C++/WinRT. Другими словами, для каждого типа Windows C++/WinRT определяет эквивалентный тип для C++ (*проецируемый тип*). Проецируемый тип имеет то же полное доменное имя, что и тип в Windows, но он размещается в пространстве имен C++ **winrt**. Добавление этих инструкций include в предварительно скомпилированный заголовок сокращает время сборки.

> [!IMPORTANT]
> Каждый раз, когда вы хотите использовать тип из пространств имен Windows, включайте соответствующий файл заголовков пространства имен C++/ WinRT для Windows, как показано здесь. *Соответствующий* заголовок — это заголовок с таким же именем, как у пространства имен типа. Например, чтобы использовать проекцию C++/WinRT для класса среды выполнения [**Windows::Foundation::Collections::PropertySet**](/uwp/api/windows.foundation.collections.propertyset), `#include <winrt/Windows.Foundation.Collections.h>`.

```cppwinrt
using namespace winrt;
using namespace Windows::Foundation;
using namespace Windows::Web::Syndication;
```

Директивы `using namespace` являются необязательными, но удобными. Приведенный выше шаблон для таких директив (позволяющий выполнять поиск любых элементов в пространстве имен **winrt** по неполному имени) подходит при начале работы с новым проектом, при этом C++/WinRT — единственная проекция языка, которую вы используете внутри этого проекта. Если, с другой стороны, вы объединяете код C++/WinRT с кодом [C++/CX](/cpp/cppcx/visual-c-language-reference-c-cx) или двоичным интерфейсом приложения SDK (ABI) (вы осуществляете перенос или взаимодействуете с этим кодом либо используете обе этих модели), изучите разделы [Взаимодействие между C++/WinRT и C++/CX](interop-winrt-cx.md), [Переход на C++/WinRT из C++/CX](move-to-winrt-from-cx.md) и [Взаимодействие между C++/WinRT и интерфейсом ABI](interop-winrt-abi.md).

```cppwinrt
winrt::init_apartment();
```

Вызов **winrt::init_apartment** инициализирует COM (по умолчанию — в многопотоковом подразделении).

```cppwinrt
Uri rssFeedUri{ L"https://blogs.windows.com/feed" };
SyndicationClient syndicationClient;
```

Разместите в стеке два объекта: они представляют URI блога о Windows и клиент синдикации. Мы создаем URI с помощью простого литерала широкой строки (дополнительные способы работы со строками см. в разделе [Обработка строк в C++/WinRT](strings.md)).

```cppwinrt
SyndicationFeed syndicationFeed = syndicationClient.RetrieveFeedAsync(rssFeedUri).get();
```

[**SyndicationClient::RetrieveFeedAsync** ](/uwp/api/windows.web.syndication.syndicationclient.retrievefeedasync) приведен пример асинхронной функции среды выполнения Windows. Этот пример кода получает объект асинхронной операцию от **RetrieveFeedAsync** и вызывает **get** для этого объекта, чтобы заблокировать вызывающий поток и дождаться результата (в данном случае это веб-канал синдикации). Дополнительные сведения о параллелизме и неблокирующих методах см. в разделе [Параллельная обработка и асинхронные операции с помощью C++/WinRT](concurrency.md).

```cppwinrt
for (const SyndicationItem syndicationItem : syndicationFeed.Items()) { ... }
```

[**SyndicationFeed.Items** ](/uwp/api/windows.web.syndication.syndicationfeed.items) представляет собой диапазон, определяемый итераторов, возвращенных **начать** и **окончания** функций (или их постоянное обратного и обратное константа вариантов). По этой причине вы может перечислять **Items** через основанный на диапазоне оператор `for` или с помощью функции шаблона **std::for_each**.

```cppwinrt
winrt::hstring titleAsHstring = syndicationItem.Title().Text();
std::wcout << titleAsHstring.c_str() << std::endl;
```

Получает текст заголовка канала в виде объекта [**winrt::hstring**](/uwp/cpp-ref-for-winrt/hstring) (см. раздел [Обработка строк в C++/WinRT](strings.md)). Затем **hstring** выводится с помощью функции **c_str**, которая отражает шаблон, используемый в строках стандартной библиотеки C++.

Как видно, C++/WinRT поощряет использование современных, аналогичных классам выражений C++, таких как `syndicationItem.Title().Text()`. Это более ясный стиль программирования, отличающийся от традиционного COM-программирования. Вам не нужно напрямую инициализировать COM и работать с указателями СОМ.

Вам также не нужно обрабатывать коды возврата HRESULT. C++/ WinRT преобразует ошибки HRESULT в исключения, такие как [**winrt::hresult-error**](/uwp/cpp-ref-for-winrt/error-handling/hresult-error), для использования естественного и современного стиля программирования. Дополнительные сведения об обработке ошибок и примеры кода см. в разделе [Обработка ошибок в C++/WinRT](error-handling.md).

## <a name="modify-a-windows-desktop-application-project-to-add-cwinrt-support"></a>Изменить проект приложения Windows Desktop Добавление C + +/ WinRT поддержка

В этом разделе показано, как можно добавить C + +/ WinRT поддержка, возможно, проект приложения Windows Desktop. Если у вас нет существующего проекта приложения Windows Desktop, а затем можно выполнить следующие действия, создание первого. Например, откройте Visual Studio и создайте **Visual C++** \> **Windows Desktop** \> **классическое приложение Windows** проекта.

При необходимости можно установить [C + +/ WinRT Visual Studio Extension (VSIX)](https://aka.ms/cppwinrt/vsix). Дополнительные сведения см. в разделе [поддержка Visual Studio для C + +/ WinRT](intro-to-using-cpp-with-winrt.md#visual-studio-support-for-cwinrt-xaml-the-vsix-extension-and-the-nuget-package).

### <a name="set-project-properties"></a>Задание свойств проекта

Перейдите к свойству проекта **Общие** \> **версия пакета SDK для Windows**и выберите **все конфигурации** и **всех платформ**. Убедитесь, что **версия пакета SDK для Windows** присваивается 10.0.17134.0 (Windows 10 версии 1803) или более поздней версии.

Убедитесь, что вы не зависит от [почему мой новый проект не будет компилироваться?](/windows/uwp/cpp-and-winrt-apis/faq).

Поскольку C + +/ WinRT использует функции из C ++ 17 project standard, задайте свойства **C/C++** > **языка** > **стандартом языка C++** для *Стандарт ISO C ++ 17 (/ std: c ++ 17)*.

### <a name="the-precompiled-header"></a>Предкомпилированный заголовок

Переименовать вашей `stdafx.h` и `stdafx.cpp` для `pch.h` и `pch.cpp`, соответственно. Установите свойство проекта **C/C++** > **предкомпилированные заголовки** > **файла предкомпилированного заголовка** для *pch.h*.

Найти и заменить все `#include "stdafx.h"` с `#include "pch.h"`.

В `pch.h`, включают `winrt/base.h`.

```cppwinrt
// pch.h
...
#include <winrt/base.h>
```

### <a name="linking"></a>Связывание

C + +/ WinRT проекция зависит от некоторых (не являющихся членами) функции среды выполнения Windows и точки входа, для которых требуется связывание с [WindowsApp.lib](/uwp/win32-and-com/win32-apis) обобщающий библиотеки. В этом разделе описаны три способа удовлетворяющие компоновщик.

Первый способ — добавление в Visual Studio проекта все C + +/ WinRT MSBuild свойств и целевых объектов. Чтобы сделать это, установите [пакет Microsoft.Windows.CppWinRT NuGet](https://www.nuget.org/packages/Microsoft.Windows.CppWinRT/) в проект. Откройте проект в Visual Studio щелкните **проекта** \> **управление пакетами NuGet...** \> **Обзор**введите или вставьте **Microsoft.Windows.CppWinRT** в поле поиска, выберите элемент в результатах поиска, а затем нажмите кнопку **установить** для установки пакета для этого проекта.

Параметры ссылок проекта также можно использовать для явного связывания `WindowsApp.lib`. Или это можно сделать в исходном коде (в `pch.h`, например) следующим образом.

```cppwinrt
#pragma comment(lib, "windowsapp")
```

Теперь можно скомпилировать и связать и добавить C + +/ WinRT кода в проект (например, код, показанный на [c + +/ WinRT краткое](#a-cwinrt-quick-start) выше, в разделе)

## <a name="important-apis"></a>Важные API
* [Метод SyndicationClient::RetrieveFeedAsync](/uwp/api/windows.web.syndication.syndicationclient.retrievefeedasync)
* [Свойство SyndicationFeed.Items](/uwp/api/windows.web.syndication.syndicationfeed.items)
* [Структура WinRT::hstring](/uwp/cpp-ref-for-winrt/hstring)
* [Структура WinRT::HRESULT ошибка](/uwp/cpp-ref-for-winrt/error-handling/hresult-error)

## <a name="related-topics"></a>Статьи по теме
* [C++/CX](/cpp/cppcx/visual-c-language-reference-c-cx)
* [Обработка ошибок в C++/WinRT](error-handling.md)
* [Взаимодействие между C++/WinRT и C++/CX](interop-winrt-cx.md)
* [Взаимодействие между C++/WinRT и интерфейсом ABI](interop-winrt-abi.md)
* [Переход на C++/WinRT из C++/CX](move-to-winrt-from-cx.md)
* [Строка, обработка в C + +/ WinRT](strings.md)

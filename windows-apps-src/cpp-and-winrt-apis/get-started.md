---
author: stevewhims
description: Чтобы приступить к работе с C++/WinRT, в этой статье описывается простой пример кода.
title: Начало работы с C++/WinRT
ms.author: stwhi
ms.date: 05/21/2018
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: Windows 10, UWP, стандартные, c++, cpp, winrt, проекция, начать, начало, работы
ms.localizationpriority: medium
ms.openlocfilehash: 13aa1e61a2d81cfa7faed0236551dad41bd00057
ms.sourcegitcommit: 7efffcc715a4be26f0cf7f7e249653d8c356319b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/30/2018
ms.locfileid: "3118086"
---
# <a name="get-started-with-cwinrtwindowsuwpcpp-and-winrt-apisintro-to-using-cpp-with-winrt"></a>Начало работы с [C++/WinRT](/windows/uwp/cpp-and-winrt-apis/intro-to-using-cpp-with-winrt)
Чтобы приступить к работе с C++/WinRT, в этой статье описывается простой пример кода.

## <a name="a-cwinrt-quick-start"></a>Краткое руководство по C++/WinRT
> [!NOTE]
> Сведения об установке и использовании расширения C++/WinRT для Visual Studio (VSIX) (которое обеспечивает поддержку шаблона проекта, а также свойств и целевых объектов MSBuild C++/WinRT) см. в разделе [Поддержка Visual Studio для C++/WinRT и VSIX](intro-to-using-cpp-with-winrt.md#visual-studio-support-for-cwinrt-and-the-vsix).

Создайте новый проект **консольного приложение для Windows (C++/WinRT)**. Отредактируйте файлы `pch.h` и `main.cpp` следующим образом.

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
> Каждый раз, когда вы хотите использовать тип из пространств имен Windows, включайте соответствующий файл заголовков пространства имен C++/ WinRT для Windows, как показано здесь. *Соответствующий* заголовок— это заголовок с таким же именем, как у пространства имен типа. Например, чтобы использовать проекцию C++/WinRT для класса среды выполнения [**Windows::Foundation::Collections::PropertySet**](/uwp/api/windows.foundation.collections.propertyset), `#include <winrt/Windows.Foundation.Collections.h>`.

```cppwinrt
using namespace winrt;
using namespace Windows::Foundation;
using namespace Windows::Web::Syndication;
```

Директивы `using namespace` являются необязательными, но удобными. Приведенный выше шаблон для таких директив (позволяющий выполнять поиск любых элементов в пространстве имен **winrt** по неполному имени) подходит при начале работы с новым проектом, при этом C++/WinRT— единственная проекция языка, которую вы используете внутри этого проекта. Если, с другой стороны, вы объединяете код C++/WinRT с кодом [C++/CX](/cpp/cppcx/visual-c-language-reference-c-cx) или двоичным интерфейсом приложения SDK (ABI) (вы осуществляете перенос или взаимодействуете с этим кодом либо используете обе этих модели), изучите разделы [Взаимодействие между C++/WinRT и C++/CX](interop-winrt-cx.md), [Переход на C++/WinRT из C++/CX](move-to-winrt-from-cx.md) и [Взаимодействие между C++/WinRT и интерфейсом ABI](interop-winrt-abi.md).

```cppwinrt
winrt::init_apartment();
```

Вызов **winrt::init_apartment** инициализирует COM (по умолчанию— в многопотоковом подразделении).

```cppwinrt
Uri rssFeedUri{ L"https://blogs.windows.com/feed" };
SyndicationClient syndicationClient;
```

Разместите в стеке два объекта: они представляют URI блога о Windows и клиент синдикации. Мы создаем URI с помощью простого литерала широкой строки (дополнительные способы работы со строками см. в разделе [Обработка строк в C++/WinRT](strings.md)).

```cppwinrt
SyndicationFeed syndicationFeed = syndicationClient.RetrieveFeedAsync(rssFeedUri).get();
```

[**SyndicationClient::RetrieveFeedAsync**](/uwp/api/windows.web.syndication.syndicationclient.retrievefeedasync) является примером асинхронной функции среды выполнения Windows. Этот пример кода получает объект асинхронной операцию от **RetrieveFeedAsync** и вызывает **get** для этого объекта, чтобы заблокировать вызывающий поток и дождаться результата (в данном случае это веб-канал синдикации). Дополнительные сведения о параллелизме и неблокирующих методах см. в разделе [Параллельная обработка и асинхронные операции с помощью C++/WinRT](concurrency.md).

```cppwinrt
for (const SyndicationItem syndicationItem : syndicationFeed.Items()) { ... }
```

[**SyndicationFeed.Items**](/uwp/api/windows.web.syndication.syndicationfeed.items) является диапазоном, определяемым итераторами, возвращенными функциями **begin** и **end** (или их постоянными, обратными, либо постоянно-обратными вариантами). По этой причине вы может перечислять **Items** через основанный на диапазоне оператор `for` или с помощью функции шаблона **std::for_each**.

```cppwinrt
winrt::hstring titleAsHstring = syndicationItem.Title().Text();
std::wcout << titleAsHstring.c_str() << std::endl;
```

Получает текст заголовка канала в виде объекта [**winrt::hstring**](/uwp/cpp-ref-for-winrt/hstring) (см. раздел [Обработка строк в C++/WinRT](strings.md)). Затем **hstring** выводится с помощью функции **c_str**, которая отражает шаблон, используемый в строках стандартной библиотеки C++.

Как видно, C++/WinRT поощряет использование современных, аналогичных классам выражений C++, таких как `syndicationItem.Title().Text()`. Это более ясный стиль программирования, отличающийся от традиционного COM-программирования. Вам не нужно напрямую инициализировать COM и работать с указателями СОМ.

Вам также не нужно обрабатывать коды возврата HRESULT. C++/ WinRT преобразует ошибки HRESULT в исключения, такие как [**winrt::hresult-error**](/uwp/cpp-ref-for-winrt/error-handling/hresult-error), для использования естественного и современного стиля программирования. Дополнительные сведения об обработке ошибок и примеры кода см. в разделе [Обработка ошибок в C++/WinRT](error-handling.md).

## <a name="important-apis"></a>Важные API
* [SyndicationClient::RetrieveFeedAsync](/uwp/api/windows.web.syndication.syndicationclient.retrievefeedasync)
* [SyndicationFeed.Items](/uwp/api/windows.web.syndication.syndicationfeed.items)
* [Структура winrt::hstring](/uwp/cpp-ref-for-winrt/hstring)
* [winrt::hresult-error](/uwp/cpp-ref-for-winrt/error-handling/hresult-error)

## <a name="related-topics"></a>Статьи по теме
* [C++/CX](/cpp/cppcx/visual-c-language-reference-c-cx)
* [Обработка ошибок в C++/WinRT](error-handling.md)
* [Взаимодействие между C++/WinRT и C++/CX](interop-winrt-cx.md)
* [Взаимодействие между C++/WinRT и интерфейсом ABI](interop-winrt-abi.md)
* [Переход на C++/WinRT из C++/CX](move-to-winrt-from-cx.md)
* [Обработка строк в C++/WinRT](strings.md)

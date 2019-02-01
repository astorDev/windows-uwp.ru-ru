---
description: Чтобы приступить к работе с C++/WinRT, в этой статье описывается простой пример кода.
title: Начало работы с C++/WinRT
ms.date: 10/19/2018
ms.topic: article
keywords: Windows 10, UWP, стандартные, c++, cpp, winrt, проекция, начать, начало, работы
ms.localizationpriority: medium
ms.openlocfilehash: c0d11a8718f61666d6285d8a1c91b48992044b22
ms.sourcegitcommit: 2d2483819957619b6de21b678caf887f3b1342af
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/01/2019
ms.locfileid: "9042356"
---
# <a name="get-started-with-cwinrt"></a>Начало работы с C++/WinRT

Чтобы приступить к работе с [C + +/ WinRT](/windows/uwp/cpp-and-winrt-apis/intro-to-using-cpp-with-winrt), этой статье описывается простой пример кода на основе на новый **Консольное приложение для Windows (C + +/ WinRT)** проекта. В этом разделе также показано, как [Добавьте C + +/ WinRT поддержки в проект приложения Windows Desktop](#modify-a-windows-desktop-application-project-to-add-cwinrt-support).

> [!IMPORTANT]
> Если вы используете Visual Studio 2017 (версии 15.8.0 или более поздней версии) и пакет Windows SDK версии 10.0.17134.0 (Windows 10, версия 1803), выберите только что созданный C + +/ WinRT проект может к ошибке при компиляции с ошибкой «ошибка*C3861: «from_abi»: идентификатор не найти*«и другие ошибки, которые происходят *base.h*. Решением является более поздней версии (Дополнительные совместимые) либо целевой версии пакета SDK для Windows, или задать свойство проекта **C/C++** > **язык** > **режим совместимости: нет** (Кроме того, если **/ permissive-** отображается в свойство проекта ** C/C++** > **язык** > **командной строки** в столбце **Дополнительные параметры**, удалите его).

## <a name="a-cwinrt-quick-start"></a>Краткое руководство по C++/WinRT

> [!NOTE]
> Сведения об установке и использовании C + +/ WinRT Visual Studio расширения (VSIX) (которое обеспечивает поддержку шаблона проекта) см. в разделе [Поддержка Visual Studio для C + +/ WinRT](intro-to-using-cpp-with-winrt.md#visual-studio-support-for-cwinrt-xaml-the-vsix-extension-and-the-nuget-package).

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

## <a name="modify-a-windows-desktop-application-project-to-add-cwinrt-support"></a>Изменение проекта приложения рабочего стола Windows для добавления C + +/ WinRT поддержки

В этом разделе показано, как добавить C + +/ WinRT поддержки в проект приложения рабочего стола Windows, у вас может быть. Если у вас нет существующий проект приложения Windows Desktop, после этого можно выполнить вместе эти действия, первый из них создавать. Например, откройте Visual Studio и создайте **Visual C++** \> **Рабочий стол Windows** \> проекта**Классических приложений для Windows** .

При необходимости можно установить [C + +/ WinRT Visual Studio расширения (VSIX)](https://aka.ms/cppwinrt/vsix). Дополнительные сведения см. в разделе [Поддержка Visual Studio для C + +/ WinRT](intro-to-using-cpp-with-winrt.md#visual-studio-support-for-cwinrt-xaml-the-vsix-extension-and-the-nuget-package).

### <a name="set-project-properties"></a>Задание свойств проекта

Перейдите в проект **Общие**свойства \> **Версия Windows SDK**и установите **Все конфигурации** и **Всех платформ**. Убедитесь, что **Версия Windows SDK** для 10.0.17134.0 (Windows 10, версия 1803) или более поздней версии.

Убедитесь, что вы не зависит от [Почему мое новый проект не будет скомпилирован?](/windows/uwp/cpp-and-winrt-apis/faq).

Поскольку C + +/ WinRT использует функции стандарта C ++ 17, задайте свойству проекта **C/C++** > **язык** > **Стандартный язык C++** *ISO C ++ 17 Standard (/ std: c ++ 17)*.

### <a name="the-precompiled-header"></a>Предварительно скомпилированных заголовков

Переименуйте вашего `stdafx.h` и `stdafx.cpp` для `pch.h` и `pch.cpp`соответственно. Задайте свойству проекта **C/C++** > **Предварительно скомпилированные заголовки** > **Скомпилированный файл заголовка** *pch.h*.

Поиск и замена всех `#include "stdafx.h"` с `#include "pch.h"`.

В `pch.h`, включают `winrt/base.h`.

```cppwinrt
// pch.h
...
#include <winrt/base.h>
```

### <a name="linking"></a>Связывание

C + +/ WinRT языковой проекции зависит от определенных бесплатные (не член) функции среды выполнения Windows и точки входа, которые требуют ссылки на [WindowsApp.lib](/uwp/win32-and-com/win32-apis) библиотеку. В этом разделе описаны три способа удовлетворить компоновщик.

Первый вариант является добавление в Visual Studio проект все C + +/ WinRT MSBuild свойств и целевых объектов. Чтобы сделать это, установите [пакет Microsoft.Windows.CppWinRT NuGet](https://www.nuget.org/packages/Microsoft.Windows.CppWinRT/) в свой проект. Откройте проект в Visual Studio, щелкните **проект** \> **Управление пакетами NuGet …**  \>  **Обзор**, введите или вставьте **Microsoft.Windows.CppWinRT** в поле поиска, выбрать элемент в результатах поиска и нажмите кнопку **установить** для установки пакета для данного проекта.

Можно также использовать параметры ссылки проекта явной ссылкой `WindowsApp.lib`. Или, это можно сделать в исходный код (в `pch.h`, например) следующим образом.

```cppwinrt
#pragma comment(lib, "windowsapp")
```

Теперь можно скомпилировать и связать и добавьте C + +/ WinRT код в проект (например, код, показанный на [c + +/ WinRT краткое](#a-cwinrt-quick-start) выше, в разделе)

## <a name="important-apis"></a>Важные API
* [Метод SyndicationClient::RetrieveFeedAsync](/uwp/api/windows.web.syndication.syndicationclient.retrievefeedasync)
* [Свойство SyndicationFeed.Items](/uwp/api/windows.web.syndication.syndicationfeed.items)
* [Структура winrt::hstring](/uwp/cpp-ref-for-winrt/hstring)
* [Структура WinRT::HRESULT ошибка](/uwp/cpp-ref-for-winrt/error-handling/hresult-error)

## <a name="related-topics"></a>Статьи по теме
* [C++/CX](/cpp/cppcx/visual-c-language-reference-c-cx)
* [Обработка ошибок в C++/WinRT](error-handling.md)
* [Взаимодействие между C++/WinRT и C++/CX](interop-winrt-cx.md)
* [Взаимодействие между C++/WinRT и интерфейсом ABI](interop-winrt-abi.md)
* [Переход на C++/WinRT из C++/CX](move-to-winrt-from-cx.md)
* [Обработка строк в C++/WinRT](strings.md)

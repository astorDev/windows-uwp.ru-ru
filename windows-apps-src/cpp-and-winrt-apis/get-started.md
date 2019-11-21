---
description: Чтобы вы могли быстро приступить к работе с C++/WinRT, в этой статье описывается простой пример кода.
title: Начало работы с C++/WinRT
ms.date: 04/18/2019
ms.topic: article
keywords: windows 10, uwp, standard, c++, cpp, winrt, projection, get, getting, started
ms.localizationpriority: medium
ms.openlocfilehash: c058a727e09f00e01664c314d8c198f3f25e841e
ms.sourcegitcommit: b52ddecccb9e68dbb71695af3078005a2eb78af1
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/20/2019
ms.locfileid: "74255135"
---
# <a name="get-started-with-cwinrt"></a>Начало работы с C++/WinRT

В этой статье рассматривается простой пример кода на основе нового проекта **консольного приложения для Windows (C++/WinRT)** , который поможет вам начать работу с [C++/WinRT](/windows/uwp/cpp-and-winrt-apis/intro-to-using-cpp-with-winrt). Здесь также показано, как [добавить поддержку C++/WinRT в проект классического приложения для Windows](#modify-a-windows-desktop-application-project-to-add-cwinrt-support).

> [!NOTE]
> Хотя мы рекомендуем выполнять разработку с последними версиями Visual Studio и пакетом Windows SDK, если вы используете Visual Studio 2017 (версия 15.8.0 или выше) и разрабатываете проект для пакета Windows SDK версии 10.0.17134.0 (Windows 10 версии 1803), то созданный проект C++/WinRT может не скомпилироваться и вызвать *ошибку C3861 о том, что идентификатор from_abi не найден*, и другие ошибки, полученные в *base.h*. Следует разрабатывать проект с более поздней (лучше соответствующей) версией Windows SDK или задать свойство проекта **C/C++**  > **Язык** > **Режим совместимости: Нет**. (Кроме того, если параметр **/permissive-** отображается в свойстве проекта **C/C++**  > **Язык** > **Командная строка** в разделе **Дополнительные параметры**, удалите его).

## <a name="a-cwinrt-quick-start"></a>Краткое руководство по C++/WinRT

> [!NOTE]
> Сведения об установке Visual Studio для разработки с использованием C++/WinRT, включая установку и использование расширения C++/WinRT для Visual Studio (VSIX) и пакета NuGet (которые вместе обеспечивают поддержку шаблона проекта и сборки), приведены в разделе [Поддержка Visual Studio для C++/WinRT, XAML, расширения VSIX и пакета NuGet](intro-to-using-cpp-with-winrt.md#visual-studio-support-for-cwinrt-xaml-the-vsix-extension-and-the-nuget-package).

Создайте проект **консольного приложения для Windows (C++/WinRT)** .

Отредактируйте файлы `pch.h` и `main.cpp` следующим образом.

```cppwinrt
// pch.h
#pragma once
#include <winrt/Windows.Foundation.Collections.h>
#include <winrt/Windows.Web.Syndication.h>
#include <iostream>
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

Подробно рассмотрим небольшой пример кода выше с объяснением того, что происходит в каждой части.

```cppwinrt
#include <winrt/Windows.Foundation.Collections.h>
#include <winrt/Windows.Web.Syndication.h>
```

В соответствии с параметрами проекта по умолчанию включенные заголовки берутся из пакета Windows SDK внутри папки `%WindowsSdkDir%Include<WindowsTargetPlatformVersion>\cppwinrt\winrt`. Visual Studio включает этот путь в своем макросе *IncludePath*. Строгой зависимости от пакета Windows SDK нет, потому что проект (с помощью средства `cppwinrt.exe`) создает те же самые заголовки в своей папке *$(GeneratedFilesDir)* . Они будут загружены из этой папки, если их невозможно найти в другом расположении или если вы измените параметры проекта.

Заголовки содержат API-интерфейсы Windows, проецируемые в C++/WinRT. Другими словами, для каждого типа Windows проекция C++/WinRT определяет эквивалентный тип для C++ (*проецируемый тип*). Проецируемый тип имеет то же полное доменное имя, что и тип в Windows, но он размещается в пространстве имен C++ **winrt**. Добавление этих инструкций include в предварительно скомпилированный заголовок сокращает время сборки.

> [!IMPORTANT]
> Каждый раз, когда вы хотите использовать тип из пространств имен Windows, включайте соответствующий файл заголовков пространства имен C++/ WinRT для Windows, как показано выше. *Соответствующий* заголовок — это заголовок с таким же именем, как у пространства имен типа. Например, чтобы использовать проекцию C++/WinRT для класса среды выполнения [**Windows::Foundation::Collections::PropertySet**](/uwp/api/windows.foundation.collections.propertyset), добавьте `#include <winrt/Windows.Foundation.Collections.h>`. Если вы добавляете `winrt/Windows.Foundation.Collections.h`, тогда вам не нужно *также* включать `winrt/Windows.Foundation.h`. Каждый проецируемый заголовок C++/WinRT автоматически включает файл заголовков его родительского пространства имен, и поэтому вам не *требуется* явным образом включать его. Хотя если вы это сделаете, ошибки не будет.

```cppwinrt
using namespace winrt;
using namespace Windows::Foundation;
using namespace Windows::Web::Syndication;
```

Директивы `using namespace` являются необязательными, но удобными. Приведенный выше шаблон для таких директив (позволяющий выполнять поиск любых элементов в пространстве имен **winrt** по неполному имени) подходит при начале работы с новым проектом, при этом C++/WinRT — единственная проекция языка, которую вы используете внутри этого проекта. Если, с другой стороны, вы объединяете код C++/WinRT с кодом [C++/CX](/cpp/cppcx/visual-c-language-reference-c-cx) или двоичным интерфейсом приложения SDK (ABI) (вы осуществляете перенос или взаимодействуете с этим кодом либо используете обе этих модели), изучите статьи [Взаимодействие между C++/WinRT и C++/CX](interop-winrt-cx.md), [Переход на C++/WinRT из C++/CX](move-to-winrt-from-cx.md) и [Взаимодействие между C++/WinRT и интерфейсом ABI](interop-winrt-abi.md).

```cppwinrt
winrt::init_apartment();
```

Вызов **winrt::init_apartment** инициализирует поток в среде выполнения Windows (по умолчанию — в многопотоковом подразделении). Вызов также инициализирует COM.

```cppwinrt
Uri rssFeedUri{ L"https://blogs.windows.com/feed" };
SyndicationClient syndicationClient;
```

Разместите в стеке два объекта: они представляют URI блога о Windows и клиент синдикации. Мы создаем URI с помощью простого литерала широкой строки (дополнительные способы работы со строками см. в статье [Обработка строк в C++/WinRT](strings.md)).

```cppwinrt
SyndicationFeed syndicationFeed = syndicationClient.RetrieveFeedAsync(rssFeedUri).get();
```

[**SyndicationClient::RetrieveFeedAsync**](/uwp/api/windows.web.syndication.syndicationclient.retrievefeedasync) является примером асинхронной функции среды выполнения Windows. Этот пример кода получает объект асинхронной операции от **RetrieveFeedAsync** и вызывает **get** для этого объекта, чтобы заблокировать вызывающий поток и дождаться результата (в данном случае это веб-канал синдикации). Дополнительные сведения о параллелизме и неблокирующих методах см. в статье [Параллельная обработка и асинхронные операции с помощью C++/WinRT](concurrency.md).

```cppwinrt
for (const SyndicationItem syndicationItem : syndicationFeed.Items()) { ... }
```

[**SyndicationFeed.Items**](/uwp/api/windows.web.syndication.syndicationfeed.items) является диапазоном, определяемым итераторами, возвращенными функциями **begin** и **end** (или их постоянными, обратными, либо постоянно-обратными вариантами). По этой причине вы можете перечислять **Items** с помощью основанного на диапазоне оператора `for` или функции шаблона **std::for_each**. Всякий раз, когда вы проходите подобную коллекцию среды выполнения Windows, вам требуется `#include <winrt/Windows.Foundation.Collections.h>`.

```cppwinrt
winrt::hstring titleAsHstring = syndicationItem.Title().Text();
std::wcout << titleAsHstring.c_str() << std::endl;
```

Этот код получает текст заголовка канала в виде объекта [**winrt::hstring**](/uwp/cpp-ref-for-winrt/hstring) (см. сведения в статье [Обработка строк в C++/WinRT](strings.md)). Затем **hstring** выводится с помощью функции **c_str**, которая отражает шаблон, используемый в строках стандартной библиотеки C++.

Как видно, C++/WinRT поощряет использование современных аналогичных классам выражений C++, таких как `syndicationItem.Title().Text()`. Это более ясный стиль программирования, отличающийся от традиционного COM-программирования. Вам не нужно напрямую инициализировать COM или работать с указателями СОМ.

Вам также не нужно обрабатывать коды возврата HRESULT. C++/ WinRT преобразует ошибки HRESULT в исключения, такие как [**winrt::hresult-error**](/uwp/cpp-ref-for-winrt/error-handling/hresult-error), для использования естественного и современного стиля программирования. Дополнительные сведения об обработке ошибок и примеры кода см. в статье [Обработка ошибок в C++/WinRT](error-handling.md).

## <a name="modify-a-windows-desktop-application-project-to-add-cwinrt-support"></a>Добавление поддержки C++/WinRT в проекте классического приложения для Windows

В этом разделе показано, как добавить поддержку C++/WinRT в проект классического приложения для Windows. Если у вас нет проекта классического приложения для Windows, вы можете выполнить эти шаги, чтобы создать его. Например, откройте Visual Studio и создайте проект **Visual C++** \> **Рабочий стол Windows** \> **Классическое приложение для Windows**.

При желании можно установить [расширение C++/WinRT для Visual Studio (VSIX)](https://marketplace.visualstudio.com/items?itemName=CppWinRTTeam.cppwinrt101804264) и пакет NuGet. Дополнительные сведения см. в разделе [о поддержке C++/WinRT в Visual Studio](intro-to-using-cpp-with-winrt.md#visual-studio-support-for-cwinrt-xaml-the-vsix-extension-and-the-nuget-package).

### <a name="set-project-properties"></a>Настройка свойств проекта

Перейдите к свойству проекта **Общие** \> **Версия пакета SDK для Windows** и выберите **Все конфигурации** и **Все платформы**. Убедитесь, что для **версии пакета Windows SDK** установлено значение 10.0.17134.0 (Windows 10 версии 1803) или выше.

Убедитесь, что у вас не возникает ситуация, описанная в разделе [Почему мой новый проект не компилируется?](/windows/uwp/cpp-and-winrt-apis/faq).

Так как C++/WinRT использует компоненты стандарта C++17, необходимо установить для свойства проекта **C/C++**  > **Язык** > **Стандарт языка C++** значение *Стандарт ISO C++17 (/std:c++17)* .

### <a name="the-precompiled-header"></a>Предварительно скомпилированный заголовок

Шаблон проекта по умолчанию создает для вас предварительно скомпилированный заголовок с именем `framework.h` или `stdafx.h`. Переименуйте его на `pch.h`. Если у вас есть файл `stdafx.cpp`, переименуйте его на `pch.cpp`. Установите для свойства проекта **C/C++**  > **Предварительно скомпилированные заголовки** > **Предварительно скомпилированный заголовок** значение *Create (/Yc)* (Создать (/Yc)), а для свойства **Предварительно скомпилированный заголовочный файл** значение *pch.h*.

Найдите и замените все элементы `#include "framework.h"` (или `#include "stdafx.h"`) на `#include "pch.h"`.

В `pch.h` добавьте `winrt/base.h`.

```cppwinrt
// pch.h
...
#include <winrt/base.h>
```

### <a name="linking"></a>Связывание

Языковая проекция C++/WinRT зависит от определенных свободных функций среды выполнения Windows (не являющихся членами) и точек входа, для которых требуется связывание с библиотекой [WindowsApp.lib](/uwp/win32-and-com/win32-apis). В этом разделе описываются три варианта соответствия компоновщику.

Первый вариант — добавить в ваш проект Visual Studio все свойства и цели C++/WinRT MSBuild. Для этого установите пакет NuGet [Microsoft.Windows.CppWinRT](https://www.nuget.org/packages/Microsoft.Windows.CppWinRT/) в проект. В Visual Studio откройте проект, щелкните **Проект** \> **Управление пакетами NuGet...** \> **Обзор**, введите или вставьте **Microsoft.Windows.CppWinRT** в поле поиска, выберите элемент в результатах поиска, а затем нажмите кнопку **Установить**, чтобы установить пакет для этого проекта.

Вы также можете использовать параметры компоновки проекта для явного включения `WindowsApp.lib`. Кроме того, это можно сделать в исходном коде (например, в `pch.h`) следующим образом.

```cppwinrt
#pragma comment(lib, "windowsapp")
```

Теперь вы можете выполнить компиляцию, связать и добавить код C++/WinRT в ваш проект (например, код, подобный тому, который показан в разделе [Краткое руководство по C++/WinRT](#a-cwinrt-quick-start) выше).

## <a name="the-three-main-scenarios-for-cwinrt"></a>Три основных сценария для C++/WinRT

По мере работы с C++/WinRT и ознакомления с остальной частью документации вы, скорее всего, заметите, что есть три основных сценария. Они описаны в следующих разделах.

### <a name="consuming-windows-runtime-apis-and-types"></a>Использование API и типов среды выполнения Windows

Иными словами, это *использование* и *вызовы* API. Например, вы можете выполнять вызовы API для обмена данными по Bluetooth, потоковую передачу и воспроизведение видео, интеграцию с оболочкой Windows и т. д. В C++/WinRT реализована полная поддержка этого сценария. Дополнительные сведения см. в статье [Использование API-интерфейсов с помощью C++/WinRT](/windows/uwp/cpp-and-winrt-apis/consume-apis).

### <a name="authoring-windows-runtime-apis-and-types"></a>Создание API и типов среды выполнения Windows

Иными словами, это *создание* API и типов. Например, вы можете создавать описанные в разделе выше типы API, графические API, API хранилища и файловой системы, сетевые API и т. д. Дополнительные сведения см. в статье [Создание API-интерфейсов в C++/WinRT](/windows/uwp/cpp-and-winrt-apis/author-apis).

Разработка API с помощью C++/WinRT — это более сложный процесс, чем их использование, так как вам нужно работать с IDL для определения API перед его реализацией. Описание процесса см. в статье [Элементы управления XAML; привязка к свойству C++/WinRT](/windows/uwp/cpp-and-winrt-apis/binding-property).

### <a name="xaml-applications"></a>Приложения XAML

Этот сценарий предполагает создания приложений и элементов управления в пользовательском интерфейсе на основе XAML. Работа с приложением, созданным с использованием XAML, включает процессы использования и разработки. Но так как сегодня XAML является основным средством описания пользовательского интерфейса в Windows, которое оказывает огромное влияние на среду выполнения Windows пропорционально этому, для него предусмотрен отдельный сценарий.

Помните, что XAML лучше всего сочетается с языками программирования, в которых используется рефлексия. В C++/WinRT, чтобы взаимодействовать с XAML, иногда необходимо выполнить некоторую дополнительную работу. Все эти случаи описаны в документации. Лучше всего начать со статей [Элементы управления XAML; привязка к свойству C++/WinRT](/windows/uwp/cpp-and-winrt-apis/binding-property) и [Создание пользовательских (на основе шаблона) элементов управления XAML с помощью C++/WinRT](/windows/uwp/cpp-and-winrt-apis/xaml-cust-ctrl).

## <a name="important-apis"></a>Важные API
* [Метод SyndicationClient::RetrieveFeedAsync](/uwp/api/windows.web.syndication.syndicationclient.retrievefeedasync)
* [Свойство SyndicationFeed.Items](/uwp/api/windows.web.syndication.syndicationfeed.items)
* [Структура WinRT::hstring](/uwp/cpp-ref-for-winrt/hstring)
* [Структура winrt::hresult_error](/uwp/cpp-ref-for-winrt/error-handling/hresult-error)

## <a name="related-topics"></a>Статьи по теме
* [C++/CX](/cpp/cppcx/visual-c-language-reference-c-cx)
* [Обработка ошибок в C++/WinRT](error-handling.md)
* [Взаимодействие между C++/WinRT и C++/CX](interop-winrt-cx.md)
* [Взаимодействие между C++/WinRT и интерфейсом ABI](interop-winrt-abi.md)
* [Переход на C++/WinRT из C++/CX](move-to-winrt-from-cx.md)
* [Обработка строк в C++/WinRT](strings.md)

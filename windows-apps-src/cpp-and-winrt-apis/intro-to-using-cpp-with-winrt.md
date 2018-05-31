---
author: stevewhims
description: Введение в C++/WinRT &mdash; стандартную проекцию языка C++ для API среды выполнения Windows.
title: Введение в C++/WinRT
ms.author: stwhi
ms.date: 05/01/2018
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: Windows 10, UWP, стандартные, c++, cpp, winrt, проекция
ms.localizationpriority: medium
ms.openlocfilehash: 968afd6fdad1e7bf6b3c38d929ab79eefa71819a
ms.sourcegitcommit: ab92c3e0dd294a36e7f65cf82522ec621699db87
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/03/2018
ms.locfileid: "1832328"
---
# <a name="introduction-to-cwinrt"></a>Введение в C++/WinRT
> [!NOTE]
> **Некоторые сведения относятся к предварительным версиям продуктов, в которые перед коммерческим выпуском могут быть внесены существенные изменения. Майкрософт не дает никаких гарантий, явных или подразумеваемых, в отношении предоставленной здесь информации.**

В версии 10.0.17134.0 (Windows 10 версии 1803) Windows SDK теперь включает C++/WinRT.

> [!IMPORTANT]
> Наиболее важные аспекты C++/ WinRT, которые следует учитывать, описаны в разделах [Поддержка пакетов SDK для C++/WinRT](#sdk-support-for-cwinrt) и [Поддержка Visual Studio для C++/ WinRT и VSIX](#visual-studio-support-for-cwinrt-and-the-vsix).

C++/WinRT — это полностью стандартная проекция языка C++17 для API среды выполнения Windows (WinRT), реализованная исключительно в файлах заголовков и предназначенная для предоставления вам первоклассного доступа к современным API-интерфейсам Windows. С помощью C++/WinRT можно создавать и использовать API среды выполнения Windows, используя любой совместимый со стандартами компилятор C++17.

## <a name="language-projections"></a>Языковые проекции
Среда выполнения Windows основывается на API-интерфейсах Component Object Model (COM) и предусматривает доступ к ней через *языковые проекции*. Проекция скрывает подробности COM и обеспечивает более естественный подход к программированию на используемом языке.

### <a name="the-cwinrt-language-projection-in-the-windows-uwp-api-reference-content"></a>Языковая проекция C++/WinRT в содержимом справочных материалов по API Windows UWP
При просмотре [API-интерфейсов Windows UWP](https://docs.microsoft.com/uwp/api/) щелкните поле со списком **Язык** в правом верхнем углу и выберите пункт **C++/WinRT**, чтобы просмотреть синтаксические блоки API в том виде, в котором они отражаются в языковой проекции C++/WinRT.

## <a name="sdk-support-for-cwinrt"></a>Поддержка пакетов SDK для C++/WinRT
Начиная с версии 10.0.17134.0 (Windows 10 версии 1803), Windows SDK содержит заголовки пространства имен Windows и инструменты проекции C++/WinRT. Одним из важных инструментов является `cppwinrt.exe`, который из `.winmd` создает файлы исходного кода, проецирующие метаданные в C++/WinRT. Файлы метаданных среды выполнения Windows (`.winmd`) предоставляют канонический способ описания поверхности API среды выполнения Windows. Из метаданных среды выполнения Windows `cppwinrt.exe` создает стандартную библиотеку C++, которая полностью описывает &mdash; или *проецирует* &mdash; поверхность API; будь то API-интерфейсы Windows или API-интерфейсы компонента среды выполнения Windows сторонних разработчиков. `Cppwinrt.exe` играет важную роль в процессе разработки, как для использования собственных и сторонних API-интерфейсов, так и для создания собственных API-интерфейсов в компонентах.

## <a name="visual-studio-support-for-cwinrt-and-the-vsix"></a>Поддержка Visual Studio для C++/WinRT и VSIX
Для шаблонов проектов C++/WinRT в Visual Studio, а также свойств и целей MSBuild C++/WinRT скачайте и установите [расширение Visual Studio (VSIX) C++/WinRT](https://aka.ms/cppwinrt/vsix) из [Visual Studio Marketplace](https://marketplace.visualstudio.com/).

Вам потребуется Visual Studio 2017 версии 15.6 или более поздней, а также Windows SDK версии 10.0.17134.0 (Windows 10 версии 1803). Затем можно создать новый проект в Visual Studio или преобразовать существующий проект, добавив свойство `<CppWinRTProject>true</CppWinRTProject>` в его файл `.vcxproj` в разделе Project > PropertyGroup. После добавления этого свойства вы получите поддержку MSBuild C++/WinRT для проекта, включая вызов инструмента `cppwinrt.exe`.

Поскольку C++/ WinRT использует функции стандарта C++17, необходимо проецировать свойство **C/C++** > **Language** > **ISO C++17 Standard (/std:c++17)**. Вы также можете установить **Conformance mode: Yes (/permissive-)**, что дополнительно обеспечит соответствие стандартам вашего кода.

Еще одно свойство, о котором следует знать, это **C/C++** > **General** > **Treat Warnings As Errors**. Задайте для него значение **Yes(/WX)** или **No (/WX-)** по вашему желанию. Иногда исходные файлы, созданные инструментом `cppwinrt.exe`, выдают предупреждения до тех пор, пока вы не добавите в них свою реализацию.

Далее приведены шаблоны проектов Visual Studio, предоставляемые VSIX.

### <a name="windows-console-application-cwinrt"></a>Консольное приложение для Windows (C++/WinRT)
Шаблон проекта для клиентского приложения C++/WinRT для Windows Desktop с пользовательским интерфейсом консоли.

### <a name="blank-app-cwinrt"></a>Пустое приложение (C++/WinRT)
Шаблон проекта для приложения универсальной платформы Windows (UWP), которое имеет пользовательский интерфейс XAML.

Visual Studio предоставляет поддержку компилятора XAML для создания заглушек реализаций и заголовков из файла языка описания интерфейса (IDL) (`.idl`) прилагаемого к каждому файлу разметки XAML. В файл IDL определите все классы локальной среды выполнения, на которые нужно сослаться на XAML-страница вашего приложения, а затем один раз выполните сборку приложения для создания шаблонов реализации в `Generated Files` и заглушек определений типов в `Generated Files\sources`. Затем используйте заглушки определений типов для справки при реализации классов локальной среды выполнения. Мы рекомендуем объявлять каждый класс среды выполнения в своем собственном файле IDL.

### <a name="core-app-cwinrt"></a>Приложение основных компонентов (C++/WinRT)
Шаблон проекта для приложения универсальной платформы Windows (UWP), которое не использует XAML.

Вместо этого оно использует заголовок пространства имен Windows проекции C++/WinRT для пространства имен Windows.ApplicationModel.Core. После выполнения сборки и запуска щелкните пустое пространство для добавления цветной квадрат; затем щелкните цветной квадрат, чтобы перетащить его.

### <a name="windows-runtime-component-cwinrt"></a>Компонент среды выполнения Windows (C++/WinRT)
Шаблон проекта для компонента; как правило, для использования из универсальной платформы Windows (UWP).

Этот шаблон демонстрирует цепочку инструментов `midl.exe` > `cppwinrt.exe`, где метаданные среды выполнения Windows (`.winmd`) создаются из IDL, а затем из метаданных среды выполнения Windows создаются заглушки реализаций и заголовков.

В файле IDL определите классы среды выполнения в вашем компоненте, их интерфейс по умолчанию и другие интерфейсы, которые они реализуют. Единожды выполните сборку проекта для создания шаблонов реализаций `module.g.cpp` и `module.h.cpp` в `Generated Files` и заглушек определений типов в `Generated Files\sources`. Затем используйте заглушки определений типов для справки при реализации классов среды выполнения в вашем компоненте. Мы рекомендуем объявлять каждый класс среды выполнения в своем собственном файле IDL.

Объедините в пакет собранный двоичный файл компонента среды выполнения Windows и его `.winmd` с использующим их приложением UWP.

## <a name="a-cwinrt-quick-start"></a>Краткое руководство по C++/WinRT
Создайте новый проект **консольного приложение для Windows (C++/WinRT)**. Отредактируйте файл `main.cpp` следующим образом.

```cppwinrt
// main.cpp

#include "pch.h"
#include <iostream>
#include <winrt/Windows.Foundation.h>
#include <winrt/Windows.Web.Syndication.h>

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
        hstring titleAsHstring = syndicationItem.Title().Text();
        std::wcout << titleAsHstring.c_str() << std::endl;
    }
}
```

Включенные заголовки `winrt/Windows.Foundation.h` и `winrt/Windows.Web.Syndication.h` входят в состав пакета SDK, который находится в папке `%WindowsSdkDir%Include<WindowsTargetPlatformVersion>\cppwinrt\winrt\`. Visual Studio включает этот путь в своем макросе *IncludePath*. Заголовки содержат API-интерфейсы Windows, проецируемые в C++/WinRT. Каждый раз, когда вам потребуется использовать типы из пространств имен Windows, включайте соответствующие заголовки пространства имен Windows проекции C++/WinRT следующим образом. Директивы `using namespace` являются необязательными, но удобными.

Все проецируемые типы находятся в корневом пространстве имен С++/WinRT **winrt**. [C++/CX](/cpp/cppcx/visual-c-language-reference-c-cx) и Windows SDK объявляют типы в корневом пространстве имен **Windows**. Эти различные пространства имен позволяют выполнять миграцию из C++/CX в C++/WinRT в удобном вам темпе.

[**SyndicationClient::RetrieveFeedAsync**](/uwp/api/windows.web.syndication.syndicationclient.retrievefeedasync) является примером асинхронной функции среды выполнения Windows. Этот пример кода получает объект асинхронной операцию от **RetrieveFeedAsync** и вызывает **get** для этого объекта, чтобы заблокировать вызывающий поток и дождаться результатов. Дополнительные сведения о параллелизме и неблокирующих методах см. в разделе [Параллельная обработка и асинхронные операции с помощью C++/WinRT](concurrency.md).

[**SyndicationFeed.Items**](/uwp/api/windows.web.syndication.syndicationfeed.items) является диапазоном, определяемым итераторами, возвращенными функциями **begin** и **end** (или их постоянными, обратными, либо постоянно-обратными вариантами). По этой причине вы может перечислять **Items** через основанный на диапазоне оператор `for` или с помощью функции шаблона **std::for_each**.

Затем код получает текст заголовка канала в виде объекта [**winrt::hstring**](/uwp/cpp-ref-for-winrt/hstring) (см. раздел [Обработка строк в C++/WinRT](strings.md)). Затем выводится **hstring** через **c_str**, что должно быть вам знакомо, если вы использовали строки из стандартной библиотеки C++.

Как видно, C++/WinRT поощряет использование современных, аналогичных классам выражений C++, таких как `syndicationItem.Title().Text()`. Это более ясный стиль программирования, отличающийся от традиционного COM-программирования. Вам не нужно явным образом инициализировать COM (за вас это делает **winrt::init_apartment**), работать с указателями СОМ и обрабатывать коды возврата HRESULT. C++/ WinRT преобразует ошибки HRESULT в исключения, чтобы обеспечить естественный и современный стиль программирования.

## <a name="custom-types-in-the-cwinrt-projection"></a>Пользовательские типы в проекции C++/WinRT
Вы можете использовать стандартные функции языка C++ и [стандартные типы данных C++ и C++/WinRT](std-cpp-data-types.md) &mdash; включая некоторые типы данных стандартной библиотеки C++ &mdash; при программировании на C++/WinRT. Однако вы также получите сведения о некоторых пользовательских типах данных в проекции, и сможете использовать их. Например, в кратком руководстве выше мы использовали [**winrt::hstring**](/uwp/cpp-ref-for-winrt/hstring).

[**winrt::com_array**](/uwp/cpp-ref-for-winrt/com-array) является еще одним типом, который вы, скорее всего, используете в определенный момент. При этом вы с меньшей вероятностью будете использовать напрямую такие типы, как [**winrt::array_view**](/uwp/cpp-ref-for-winrt/array-view). Вы можете решить не использовать его, чтобы у вас не было кода, требующего изменений, если эквивалентный тип появится в стандартной библиотеке C++.

Также существуют типы, которые можно обнаружить, если внимательно изучить заголовки пространства имен Windows проекции C++/WinRT. Один из них — **winrt::param::hstring**. Такие типы существуют только в целях повышения эффективности и их не следует использовать в коде.

## <a name="important-apis"></a>Важные API
* [SyndicationClient::RetrieveFeedAsync](/uwp/api/windows.web.syndication.syndicationclient.retrievefeedasync)
* [SyndicationFeed.Items](/uwp/api/windows.web.syndication.syndicationfeed.items)
* [Структура winrt::hstring](/uwp/cpp-ref-for-winrt/hstring)
* [пространство имен WinRT](/uwp/cpp-ref-for-winrt/winrt)

## <a name="related-topics"></a>Статьи по теме
* [C++/CX](/cpp/cppcx/visual-c-language-reference-c-cx)
* [Обработка строк в C++/WinRT](strings.md)
* [Visual Studio Marketplace](https://marketplace.visualstudio.com/)
* [API WindowsUWP](https://docs.microsoft.com/uwp/api/)

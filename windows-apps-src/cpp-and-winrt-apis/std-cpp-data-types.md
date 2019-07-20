---
description: С помощью C++/WinRT можно вызывать интерфейсы API среды выполнения Windows с использованием стандартных типов данных C++.
title: Стандартные типы данных C++ и C++/WinRT
ms.date: 04/23/2019
ms.topic: article
keywords: Windows 10, UWP, стандартный, c++, cpp, winrt, проекция, данные, типы
ms.localizationpriority: medium
ms.openlocfilehash: 8cb62f8386798fcb78a11be35ba951616c47039c
ms.sourcegitcommit: d37a543cfd7b449116320ccfee46a95ece4c1887
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/16/2019
ms.locfileid: "68270069"
---
# <a name="standard-c-data-types-and-cwinrt"></a>Стандартные типы данных C++ и C++/WinRT

С помощью [C++/WinRT](/windows/uwp/cpp-and-winrt-apis/intro-to-using-cpp-with-winrt) можно вызывать интерфейсы API среды выполнения Windows с использованием стандартных типов данных C++, включая некоторые типы данных стандартной библиотеки C++. Вы можете передать стандартные строки в интерфейсы API (см. раздел [Обработка строк в C++/WinRT](strings.md)), вы можете также передать списки инициализатора и стандартные контейнеры в интерфейсы API, ожидающие семантически эквивалентную коллекцию.

Также см. статью о [передаче параметров в интерфейс ABI](/windows/uwp/cpp-and-winrt-apis/pass-parms-to-abi).

## <a name="standard-initializer-lists"></a>Стандартные списки инициализатора
Список инициализатора (**std::initializer_list**) — это конструкция стандартной библиотеки C++. Списки инициализатора можно использовать при вызове определенных конструкторов и методов среды выполнения Windows. Например, с помощью одного из них можно вызвать [**DataWriter::WriteBytes**](/uwp/api/windows.storage.streams.datawriter.writebytes).

```cppwinrt
#include <winrt/Windows.Storage.Streams.h>

using namespace winrt::Windows::Storage::Streams;

int main()
{
    winrt::init_apartment();

    InMemoryRandomAccessStream stream;
    DataWriter dataWriter{stream};
    dataWriter.WriteBytes({ 99, 98, 97 }); // the initializer list is converted to a winrt::array_view before being passed to WriteBytes.
}
```

Это работает благодаря двум элементам. Во-первых, метод **DataWriter::WriteBytes** принимает параметр типа [**winrt::array_view**](/uwp/cpp-ref-for-winrt/array-view).

```cppwinrt
void WriteBytes(winrt::array_view<uint8_t const> value) const
```

**winrt::array_view** является настраиваемым типом C++/WinRT, который безопасно представляет непрерывный ряд значений (он определен в базовой библиотеке C++/WinRT — `%WindowsSdkDir%Include\<WindowsTargetPlatformVersion>\cppwinrt\winrt\base.h`).

Во-вторых, **winrt::array_view** имеет конструктор списков инициализатора.

```cppwinrt
template <typename T> winrt::array_view(std::initializer_list<T> value) noexcept
```

Во многих случаях вы можете выбрать, учитывать ли **winrt::array_view** при программировании. Если вы решите *не* учитывать array_view, то у вас не будет кода, требующего изменений, если эквивалентный тип появится в стандартной библиотеке C++.

Вы можете передать список инициализатора в API среды выполнения Windows, который ожидает параметр коллекции. Возьмем, например, **StorageItemContentProperties::RetrievePropertiesAsync**.

```cppwinrt
IAsyncOperation<IMap<winrt::hstring, IInspectable>> StorageItemContentProperties::RetrievePropertiesAsync(IIterable<winrt::hstring> propertiesToRetrieve) const;
```

Вы можете вызвать этот API с помощью списка инициализатора следующим образом.

```cppwinrt
IAsyncAction retrieve_properties_async(StorageFile const& storageFile)
{
    auto properties{ co_await storageFile.Properties().RetrievePropertiesAsync({ L"System.ItemUrl" }) };
}
```

Здесь действуют два фактора. Во-первых, вызываемый создает **std::vector** из списка инициализатора (это асинхронный вызываемый, поэтому он способен владеть этим объектом, что необходимо). Во-вторых, C++/WinRT прозрачно (и без создания копий) привязывает **std::vector** в качестве параметра коллекции среды выполнения Windows.

## <a name="standard-arrays-and-vectors"></a>Стандартные массивы и векторы
У [**winrt::array_view**](/uwp/cpp-ref-for-winrt/array-view) также имеются конструкторы преобразования из **std::vector** и **std::array**.

```cppwinrt
template <typename C, size_type N> winrt::array_view(std::array<C, N>& value) noexcept
template <typename C> winrt::array_view(std::vector<C>& vectorValue) noexcept
```

Таким образом, можно вместо этого вызвать **DataWriter::WriteBytes** с помощью **std::vector**.

```cppwinrt
std::vector<byte> theVector{ 99, 98, 97 };
dataWriter.WriteBytes(theVector); // theVector is converted to a winrt::array_view before being passed to WriteBytes.
```

Можно также использовать **std::array**.

```cppwinrt
std::array<byte, 3> theArray{ 99, 98, 97 };
dataWriter.WriteBytes(theArray); // theArray is converted to a winrt::array_view before being passed to WriteBytes.
```

C++/WinRT привязывает **std::vector** в качестве параметра коллекции среды выполнения Windows. Таким образом, можно передать **std::vector&lt;winrt::hstring&gt;** , и он будет преобразован в соответствующую коллекцию среды выполнения Windows типа **winrt::hstring**. Есть еще кое-что, о чем нужно помнить, если используется асинхронный вызываемый. Из-за особенностей реализации этого варианта нужно предоставить rvalue, поэтому необходимо обеспечить копирование или перемещение вектора. В следующем примере кода мы переместим владение вектором на объект с типом параметров, принимаемым асинхронным вызываемым (и затем мы проявим осторожность, чтобы не обратиться к `vecH` еще раз после перемещения). Если вы хотите узнать больше о значениях rvalue, ознакомьтесь с разделом [Категории значений и ссылки](cpp-value-categories.md).

```cppwinrt
IAsyncAction retrieve_properties_async(StorageFile const storageFile, std::vector<winrt::hstring> vecH)
{
    auto properties{ co_await storageFile.Properties().RetrievePropertiesAsync(std::move(vecH)) };
}
```

При этом нельзя передавать **std::vector&lt;std::wstring&gt;** там, где требуется коллекция среды выполнения Windows. Это связано с тем, что после преобразования в соответствующую коллекцию среды выполнения Windows **std::wstring** язык C++ не преобразовывает параметры типов этой коллекции. Следовательно, приведенный ниже пример кода не будет компилироваться (и решением является передача **std::vector&lt;winrt::hstring&gt;** , как показано выше).

```cppwinrt
IAsyncAction retrieve_properties_async(StorageFile const& storageFile, std::vector<std::wstring> const& vecW)
{
    auto properties{ co_await storageFile.Properties().RetrievePropertiesAsync(std::move(vecW)) }; // error! Can't convert from vector of wstring to async_iterable of hstring.
}
```

## <a name="raw-arrays-and-pointer-ranges"></a>Необработанные массивы и диапазоны указателей
Имея в виду, что эквивалентный тип может появиться в стандартной библиотеке C++ в будущем, можно также работать непосредственно с **winrt::array_view**, если это необходимо.

У **winrt::array_view** имеются конструкторы преобразования из необработанных массивов и из диапазона **T&ast;** (указатели на тип элемента).

```cppwinrt
using namespace winrt;
...
byte theRawArray[]{ 99, 98, 97 };
array_view<byte const> fromRawArray{ theRawArray };
dataWriter.WriteBytes(fromRawArray); // the winrt::array_view is passed to WriteBytes.

array_view<byte const> fromRange{ theArray.data(), theArray.data() + 2 }; // just the first two elements.
dataWriter.WriteBytes(fromRange); // the winrt::array_view is passed to WriteBytes.
```

## <a name="winrtarrayview-functions-and-operators"></a>Функции и операторы winrt::array_view
Для **winrt::array_view** реализован ряд конструкторов, операторов, функций и итераторов. Тип **winrt::array_view** является диапазоном, поэтому вы можете использовать его с основанным на диапазоне `for` или с **std::for_each**.

Дополнительные примеры и сведения приведены в справочнике по API [**winrt::array_view**](/uwp/cpp-ref-for-winrt/array-view).

## <a name="ivectorlttgt-and-standard-iteration-constructs"></a>**IVector&lt;T&gt;** и стандартные итерационные конструкции
[**SyndicationFeed.Items**](/uwp/api/windows.web.syndication.syndicationfeed.items) является примером API среды выполнения Windows, который возвращает коллекцию типа [**IVector&lt;T&gt;** ](/uwp/api/windows.foundation.collections.ivector_t_) (проецируемую в C++/WinRT как **winrt::Windows::Foundation::Collections::IVector&lt;T&gt;** ). Можно использовать этот тип со стандартными итерационными конструкциями, такими как `for` на основе диапазона.

```cppwinrt
// main.cpp
#include "pch.h"
#include <winrt/Windows.Web.Syndication.h>
#include <iostream>

using namespace winrt;
using namespace Windows::Web::Syndication;

void PrintFeed(SyndicationFeed const& syndicationFeed)
{
    for (SyndicationItem const& syndicationItem : syndicationFeed.Items())
    {
        std::wcout << syndicationItem.Title().Text().c_str() << std::endl;
    }
}
```

## <a name="c-coroutines-with-asynchronous-windows-runtime-apis"></a>Соподпрограммы C++ с асинхронными интерфейсами API среды выполнения Windows
Вы можете продолжать использовать [библиотеки параллельных шаблонов (PPL)](/cpp/parallel/concrt/parallel-patterns-library-ppl) при вызове асинхронных API среды выполнения Windows. Тем не менее, во многих случаях соподпрограммы C++ предоставляют эффективную и более удобную для программирования идиому взаимодействия с асинхронными объектами. Дополнительные сведения и примеры кода приведены в разделе [Параллельная обработка и асинхронные операции с помощью C++/WinRT](concurrency.md).

## <a name="important-apis"></a>Важные API
* Интерфейс [IVector&lt;T&gt;](/uwp/api/windows.foundation.collections.ivector_t_)
* [Шаблон структуры winrt::array_view](/uwp/cpp-ref-for-winrt/array-view)

## <a name="related-topics"></a>Статьи по теме
* [Обработка строк в C++/WinRT](strings.md)

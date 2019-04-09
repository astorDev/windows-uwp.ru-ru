---
description: С помощью C++/WinRT можно вызывать API среды выполнения Windows с использованием стандартных типов данных C++.
title: Стандартные типы данных C++ и C++/WinRT
ms.date: 05/07/2018
ms.topic: article
keywords: Windows 10, UWP, стандартные, c++, cpp, winrt, проекция, данные, типы
ms.localizationpriority: medium
ms.openlocfilehash: 44de7b61264f8e0e04d1de6d2b1101844656f28b
ms.sourcegitcommit: 99271798fe53d9768fc52b21366de05268cadcb0
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/20/2019
ms.locfileid: "58221460"
---
# <a name="standard-c-data-types-and-cwinrt"></a>Стандартные типы данных C++ и C++/WinRT

С помощью [ C++/WinRT](/windows/uwp/cpp-and-winrt-apis/intro-to-using-cpp-with-winrt), можно вызвать API среды выполнения Windows, с помощью стандартных C++ типы данных, включая некоторые C++ Стандартная библиотека типов данных. Стандартные строки можно передать в API-интерфейсы (см. в разделе [строки, обработка в C++/WinRT](strings.md)), и вы можете передать инициализатор списков и стандартных контейнеров к интерфейсам API, ожидающим коллекция семантически эквивалентны.

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

Существует два элемента, благодаря которым это работает. Во-первых, метод **DataWriter::WriteBytes** принимает параметр типа [**winrt::array_view** ](/uwp/cpp-ref-for-winrt/array-view).

```cppwinrt
void WriteBytes(winrt::array_view<uint8_t const> value) const
```

**WinRT::array_view** является пользовательским C++/WinRT тип, который безопасно представляет непрерывный ряд значений (он определен в C++/WinRT базовой библиотеки, который является `%WindowsSdkDir%Include\<WindowsTargetPlatformVersion>\cppwinrt\winrt\base.h`).

Во-вторых, **winrt::array_view** имеет конструктор списка инициализаторов.

```cppwinrt
template <typename T> winrt::array_view(std::initializer_list<T> value) noexcept
```

Во многих случаях вы можете ли учитывать **winrt::array_view** в программирования. Если вы решите *не* учитывать array_view, то у вас не будет кода, требующего изменений, если эквивалентный тип появится в стандартной библиотеке C++.

Вы можете передать список инициализатора в API-интерфейс среды выполнения Windows, который ожидает параметр коллекции. Возьмем, к примеру, **StorageItemContentProperties::RetrievePropertiesAsync**.

```cppwinrt
IAsyncOperation<IMap<winrt::hstring, IInspectable>> StorageItemContentProperties::RetrievePropertiesAsync(IIterable<winrt::hstring> propertiesToRetrieve) const;
```

Вы можете вызвать этот API-интерфейс с помощью списка инициализации следующим образом.

```cppwinrt
IAsyncAction retrieve_properties_async(StorageFile const& storageFile)
{
    auto properties{ co_await storageFile.Properties().RetrievePropertiesAsync({ L"System.ItemUrl" }) };
}
```

Здесь действуют два фактора. Во-первых, вызываемый объект создает **std::vector** из списка инициализаторов (этот вызываемый объект является асинхронным, поэтому может его владельцем этого объекта, который он должен). Во-вторых, C++/WinRT прозрачно (и без созданий копий) привязывает **std::vector** в качестве параметра коллекции среды выполнения Windows.

## <a name="standard-arrays-and-vectors"></a>Стандартные массивы и векторы
[**WinRT::array_view** ](/uwp/cpp-ref-for-winrt/array-view) также имеет конструкторы преобразования из **std::vector** и **std::array**.

```cppwinrt
template <typename C, size_type N> winrt::array_view(std::array<C, N>& value) noexcept
template <typename C> winrt::array_view(std::vector<C>& vectorValue) noexcept
```

Таким образом, можно вместо этого вызвать **DataWriter::WriteBytes** с помощью **std::vector**.

```cppwinrt
std::vector<byte> theVector{ 99, 98, 97 };
dataWriter.WriteBytes(theVector); // theVector is converted to a winrt::array_view before being passed to WriteBytes.
```

Также можно использовать **std::array**.

```cppwinrt
std::array<byte, 3> theArray{ 99, 98, 97 };
dataWriter.WriteBytes(theArray); // theArray is converted to a winrt::array_view before being passed to WriteBytes.
```

C++/WinRT привязывает **std::vector** в качестве параметра коллекции среды выполнения Windows. Таким образом, можно передать **std::vector&lt;winrt::hstring&gt;**, и он будет преобразована в соответствующую коллекцию среды выполнения Windows **winrt::hstring**. Нет дополнительных сведений, необходимо иметь в виду, если вызываемый объект является асинхронным. Из-за деталей реализации этого варианта необходимо предоставить rvalue, поэтому необходимо указать копирование или перемещение вектора. В следующем примере кода мы перенесите владение вектора на объект типа параметра, принят вызываемым async (и затем мы осторожность, чтобы не получить доступ к `vecH` еще раз после его перемещения). Если вы хотите узнать больше о значениях rvalue, см. в разделе [значение категории и ссылки на них](cpp-value-categories.md).

```cppwinrt
IAsyncAction retrieve_properties_async(StorageFile const storageFile, std::vector<winrt::hstring> vecH)
{
    auto properties{ co_await storageFile.Properties().RetrievePropertiesAsync(std::move(vecH)) };
}
```

При этом нельзя передавать **std::vector&lt;std::wstring&gt;** там, где требуется коллекция среды выполнения Windows. Это связано с тем, что после преобразования в соответствующую коллекцию среды выполнения Windows **std::wstring** язык C++ не преобразует параметры типов этой коллекции. Следовательно, не будет компилироваться в следующем примере кода (и решением является передача **std::vector&lt;winrt::hstring&gt;**  вместо этого, как показано выше).

```cppwinrt
IAsyncAction retrieve_properties_async(StorageFile const& storageFile, std::vector<std::wstring> const& vecW)
{
    auto properties{ co_await storageFile.Properties().RetrievePropertiesAsync(std::move(vecW)) }; // error! Can't convert from vector of wstring to async_iterable of hstring.
}
```

## <a name="raw-arrays-and-pointer-ranges"></a>Необработанные массивы и диапазоны указателей
Памятуя о том, следует учитывать, что в дальнейшем при может существовать эквивалентный тип C++ стандартной библиотеки, вы также можете работать непосредственно с **winrt::array_view** Если вы решили, или нужно.

**WinRT::array_view** имеет конструкторы преобразования из необработанного массива, а также из диапазона **T&ast;**  (указатели на тип элемента).

```cppwinrt
using namespace winrt;
...
byte theRawArray[]{ 99, 98, 97 };
array_view<byte const> fromRawArray{ theRawArray };
dataWriter.WriteBytes(fromRawArray); // the winrt::array_view is passed to WriteBytes.

array_view<byte const> fromRange{ theArray.data(), theArray.data() + 2 }; // just the first two elements.
dataWriter.WriteBytes(fromRange); // the winrt::array_view is passed to WriteBytes.
```

## <a name="winrtarrayview-functions-and-operators"></a>Функции и операторы функции WinRT::array_view
Множество конструкторов, операторов, функций и итераторы реализуются для **winrt::array_view**. Объект **winrt::array_view** представляет собой диапазон, в том случае, чтобы можно было использовать с по диапазону `for`, или с **std::for_each**.

Дополнительные примеры и сведения см. в справочнике по API [**winrt::array_view**](/uwp/cpp-ref-for-winrt/array-view).

## <a name="ivectorlttgt-and-standard-iteration-constructs"></a>**IVector&lt;T&gt;**  и стандартный итерационных конструкций
[**SyndicationFeed.Items** ](/uwp/api/windows.web.syndication.syndicationfeed.items) является примером API среды выполнения Windows, который возвращает коллекцию типа [ **IVector&lt;T&gt;**  ](/uwp/api/windows.foundation.collections.ivector_t_) (преобразования типа в C++/WinRT как **winrt::Windows::Foundation::Collections::IVector&lt;T&gt;**). Можно использовать этот тип с помощью стандартных итерационных конструкций, таких как по диапазону `for`.

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

## <a name="c-coroutines-with-asynchronous-windows-runtime-apis"></a>Соподпрограмм C++ с асинхронными интерфейсами API среды выполнения Windows
Вы можете продолжать использовать [библиотеки параллельных шаблонов (PPL)](/cpp/parallel/concrt/parallel-patterns-library-ppl) при вызове асинхронных API среды выполнения Windows. Однако во многих случаях соподпрограмм C++ предоставляет идиома эффективный и более легко закодированных для взаимодействия с объектами асинхронной. Дополнительные сведения и примеры кода см. в разделе [параллелизма и асинхронные операции с C++/WinRT](concurrency.md).

## <a name="important-apis"></a>Важные API
* [IVector&lt;T&gt; интерфейса](/uwp/api/windows.foundation.collections.ivector_t_)
* [Структура шаблона WinRT::array_view](/uwp/cpp-ref-for-winrt/array-view)

## <a name="related-topics"></a>См. также
* [Строка, обработка в C++/WinRT](strings.md)

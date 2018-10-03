---
author: stevewhims
description: С помощью C++/WinRT можно вызывать API среды выполнения Windows с использованием стандартных типов данных C++.
title: Стандартные типы данных C++ и C++/WinRT
ms.author: stwhi
ms.date: 05/07/2018
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: Windows 10, UWP, стандартные, c++, cpp, winrt, проекция, данные, типы
ms.localizationpriority: medium
ms.openlocfilehash: f9763e7f69b143dffe8fea611f25ae75284929cb
ms.sourcegitcommit: e6daa7ff878f2f0c7015aca9787e7f2730abcfbf
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/03/2018
ms.locfileid: "4316608"
---
# <a name="standard-c-data-types-and-cwinrt"></a>Стандартные типы данных C++ и C++/WinRT

С помощью [C + +/ WinRT](/windows/uwp/cpp-and-winrt-apis/intro-to-using-cpp-with-winrt), можно вызывать API среды выполнения Windows, используя стандартные типы данных C++, включая некоторые типы данных стандартной библиотеки C++. Стандартные строки можно передать в API-интерфейсы (см. в разделе [Обработка строк в C + +/ WinRT](strings.md)), и можно передать инициализатора списков и стандартных контейнеров для API, которые ожидают семантически эквивалентные коллекции.

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
    dataWriter.WriteBytes({ 99, 98, 97 }); // the initializer list is converted to an array_view before being passed to WriteBytes.
}
```

Существует два элемента, благодаря которым это работает. Во-первых, метод **DataWriter::WriteBytes** принимает параметр типа [**winrt::array_view **](/uwp/cpp-ref-for-winrt/array-view).

```cppwinrt
void WriteBytes(array_view<uint8_t const> value) const
```

 **array_view** является настраиваемым типом C++/WinRT, который безопасно представляет непрерывный ряд значений (он определяется в базовой библиотеке C++/WinRT — `%WindowsSdkDir%Include\<WindowsTargetPlatformVersion>\cppwinrt\winrt\base.h`).

Во-вторых, **array_view** имеет конструктор списков инициализатора.

```cppwinrt
template <typename T> array_view(std::initializer_list<T> value) noexcept
```

Во многих случаях вы можете выбрать, учитывать ли **array_view** при программировании. Если вы решите *не* учитывать array_view, то у вас не будет кода, требующего изменений, если эквивалентный тип появится в стандартной библиотеке C++.

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

Здесь действуют два фактора. Во-первых, вызываемый создает **std::vector** из списка инициализатора (это асинхронный вызываемый, поэтому он способен владеть этим объектом, что необходимо). Во-вторых, C++/WinRT прозрачно (и без созданий копий) привязывает **std::vector** в качестве параметра коллекции среды выполнения Windows.

## <a name="standard-arrays-and-vectors"></a>Стандартные массивы и векторы
**array_view** также имеет конструкторы преобразования из **std::vector** и **std::array **.

```cppwinrt
template <typename C, size_type N> array_view(std::array<C, N>& value) noexcept
template <typename C> array_view(std::vector<C>& vectorValue) noexcept
```

Таким образом, можно вместо этого вызвать **DataWriter::WriteBytes** с помощью **std::vector**.

```cppwinrt
std::vector<byte> theVector{ 99, 98, 97 };
dataWriter.WriteBytes(theVector); // theVector is converted to an array_view before being passed to WriteBytes.
```

Также можно использовать **std::array**.

```cppwinrt
std::array<byte, 3> theArray{ 99, 98, 97 };
dataWriter.WriteBytes(theArray); // theArray is converted to an array_view before being passed to WriteBytes.
```

C++/WinRT привязывает **std::vector** в качестве параметра коллекции среды выполнения Windows. Таким образом, можно передать **std::vector&lt;winrt::hstring&gt;**, и он будет преобразована в соответствующую коллекцию среды выполнения Windows **winrt::hstring**. Существует дополнительный сведений необходимо иметь в виду, если вызываемый является асинхронным. Из-за сведения о реализации этого варианта необходимо предоставить rvalue, поэтому вы должны предоставить копию перемещения вектора. В следующем примере кода мы переносим владение вектора объект типа параметра, принимаемый асинхронный вызываемый (и мы не должно получить доступ к `vecH` снова после перемещения его). Если вы хотите узнать больше о rvalues, см. в разделе [категории значение и ссылки на них](cpp-value-categories.md).

```cppwinrt
IAsyncAction retrieve_properties_async(StorageFile const storageFile, std::vector<winrt::hstring> vecH)
{
    auto properties{ co_await storageFile.Properties().RetrievePropertiesAsync(std::move(vecH)) };
}
```

При этом нельзя передавать **std::vector&lt;std::wstring&gt;** там, где требуется коллекция среды выполнения Windows. Это связано с тем, что после преобразования в соответствующую коллекцию среды выполнения Windows **std::wstring** язык C++ не преобразует параметры типов этой коллекции. Следовательно, не будет скомпилирован в следующем примере кода (и решением является передать **std::vector&lt;winrt::hstring&gt; ** вместо этого, как показано выше).

```cppwinrt
IAsyncAction retrieve_properties_async(StorageFile const& storageFile, std::vector<std::wstring> const& vecW)
{
    auto properties{ co_await storageFile.Properties().RetrievePropertiesAsync(std::move(vecW)) }; // error! Can't convert from vector of wstring to async_iterable of hstring.
}
```

## <a name="raw-arrays-and-pointer-ranges"></a>Необработанные массивы и диапазоны указателей
Имея в виду, что эквивалентный тип может появиться в стандартной библиотеке C++ в будущем, можно также работать непосредственно с **array_view**, если это необходимо.

**array_view** имеет конструкторы преобразования из необработанных массивов и из диапазона **T&ast; ** (указатели на тип элемента).

```cppwinrt
using namespace winrt;
...
byte theRawArray[]{ 99, 98, 97 };
array_view<byte const> fromRawArray{ theRawArray };
dataWriter.WriteBytes(fromRawArray); // the array_view is passed to WriteBytes.

array_view<byte const> fromRange{ theArray.data(), theArray.data() + 2 }; // just the first two elements.
dataWriter.WriteBytes(fromRange); // the array_view is passed to WriteBytes.
```

## <a name="winrtarrayview-functions-and-operators"></a>Функции и операторы функции WinRT::array_view
Для **array_view** реализован ряд конструкторов, операторов, функций и итераторов. **Array_view** является диапазоном, поэтому вы можете использовать его с основанным на диапазоне `for` или с **std::for_each**.

Дополнительные примеры и сведения см. в справочнике по API [**winrt::array_view**](/uwp/cpp-ref-for-winrt/array-view).

## <a name="ivectorlttgt-and-standard-iteration-constructs"></a>**IVector&lt;T&gt; ** и стандартные итерации конструкции
[**SyndicationFeed.Items**](/uwp/api/windows.web.syndication.syndicationfeed.items) является примером API среды выполнения Windows, который возвращает коллекцию типа [**IVector&lt;T&gt; **](/uwp/api/windows.foundation.collections.ivector_t_) (проецируемые в C + +/ WinRT как **winrt::Windows::Foundation::Collections::IVector&lt;T&gt; ** ). Можно использовать этот тип с помощью стандартных итерации конструкций, такие как основанным на диапазоне `for`.

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

## <a name="c-coroutines-with-asynchronous-windows-runtime-apis"></a>Сопрограммы C++ с асинхронными API среды выполнения Windows
Вы можете продолжать использовать [Библиотеки параллельных шаблонов (PPL)](/cpp/parallel/concrt/parallel-patterns-library-ppl) при вызове асинхронных API среды выполнения Windows. Тем не менее во многих случаях сопрограммы C++ обеспечивают эффективный и более легко закодированных идиом для взаимодействия с асинхронной объектов. Дополнительные сведения и примеры кода, см. в разделе [параллельная обработка и асинхронные операции с помощью C + +/ WinRT](concurrency.md).

## <a name="important-apis"></a>Важные API
* [IVector&lt;T&gt; интерфейс](/uwp/api/windows.foundation.collections.ivector_t_)
* [Шаблон структуры winrt::array_view struct template](/uwp/cpp-ref-for-winrt/array-view)

## <a name="related-topics"></a>Еще по теме
* [Обработка строк в C++/WinRT](strings.md)

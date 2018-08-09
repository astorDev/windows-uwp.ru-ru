---
author: stevewhims
description: С помощью C++/WinRT можно вызывать API среды выполнения Windows с использованием стандартных типов данных C++.
title: Стандартные типы данных C++ и C++/WinRT
ms.author: stwhi
ms.date: 04/10/2018
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: Windows 10, UWP, стандартные, c++, cpp, winrt, проекция, данные, типы
ms.localizationpriority: medium
ms.openlocfilehash: ccf79b1ec21688d9573e62777def8f15295c3fca
ms.sourcegitcommit: ab92c3e0dd294a36e7f65cf82522ec621699db87
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/03/2018
ms.locfileid: "1832078"
---
# <a name="standard-c-data-types-and-cwinrtwindowsuwpcpp-and-winrt-apisintro-to-using-cpp-with-winrt"></a>Стандартные типы данных [C++ и C++/WinRT](/windows/uwp/cpp-and-winrt-apis/intro-to-using-cpp-with-winrt)
С помощью C++/WinRT можно вызывать API среды выполнения Windows с использованием стандартных типов данных C++, включая некоторые типы данных стандартной библиотеки C++.

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
    auto properties = co_await storageFile.Properties().RetrievePropertiesAsync({ L"System.ItemUrl" });
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

C++/WinRT привязывает **std::vector** в качестве параметра коллекции среды выполнения Windows. Таким образом, можно передать **std::vector&lt;winrt::hstring&gt;**, и он будет преобразована в соответствующую коллекцию среды выполнения Windows **winrt::hstring**. Если вызываемый является асинхронным, необходимо скопировать или переместить вектор. В следующем примере кода мы переносим владение вектора асинхронному вызываемому.

```cppwinrt
IAsyncAction retrieve_properties_async(StorageFile const& storageFile, std::vector<winrt::hstring> const& vecH)
{
    auto properties = co_await storageFile.Properties().RetrievePropertiesAsync(std::move(vecH));
}
```

При этом нельзя передавать **std::vector&lt;std::wstring&gt;** там, где требуется коллекция среды выполнения Windows. Это связано с тем, что после преобразования в соответствующую коллекцию среды выполнения Windows **std::wstring** язык C++ не преобразует параметры типов этой коллекции. Следовательно, следующий пример кода скомпилировать не удастся.

```cppwinrt
IAsyncAction retrieve_properties_async(StorageFile const& storageFile, std::vector<std::wstring> const& vecW)
{
    auto properties = co_await storageFile.Properties().RetrievePropertiesAsync(std::move(vecW)); // error! Can't convert from vector of wstring to async_iterable of hstring.
}
```

## <a name="raw-arrays-and-pointer-ranges"></a>Необработанные массивы и диапазоны указателей
Имея в виду, что эквивалентный тип может появиться в стандартной библиотеке C++ в будущем, можно также работать непосредственно с **array_view**, если это необходимо.

**array_view** имеет конструкторы преобразования из необработанных массивов и из диапазона **T*** (указатели на тип элемента).

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

## <a name="important-apis"></a>Важные API
* [Шаблон структуры winrt::array_view struct template](/uwp/cpp-ref-for-winrt/array-view)
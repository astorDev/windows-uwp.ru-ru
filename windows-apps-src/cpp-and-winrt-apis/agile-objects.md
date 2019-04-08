---
description: Гибкий объект — это объект, доступ к которому может осуществляться из любого потока. Типы C++/WinRT являются гибкими по умолчанию, но вы можете это отключить.
title: Гибкие объекты в C++/WinRT
ms.date: 10/20/2018
ms.topic: article
keywords: Windows 10, uwp, стандартная, c++, cpp, winrt, проекция, гибкий, объект, гибкость, IAgileObject
ms.localizationpriority: medium
ms.openlocfilehash: 2481396d9348250e14ebfc2d1f940b663b405f77
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57639669"
---
# <a name="agile-objects-in-cwinrt"></a>Объекты в C + +/ WinRT

В подавляющем большинстве случаев экземпляр класса среды выполнения Windows может осуществляться из любого потока (так же, как можно чаще стандартными объектами C++). Такой класс среды выполнения Windows — *agile*. Только небольшое количество классов среды выполнения Windows, входящие в состав Windows, не являются гибкими, но при их использовании необходимо принять во внимание их потоковая модель и поведение маршалинга (маршалинг передача данных через границу подразделения). Это хороший по умолчанию для каждого объекта среды выполнения Windows, быть гибкими, чтобы собственные [C + +/ WinRT](/windows/uwp/cpp-and-winrt-apis/intro-to-using-cpp-with-winrt) типы являются гибкими по умолчанию.

Однако можно отказаться. Возможно веские основания требовать объект типа находятся, например, в заданной однопотоковое подразделение. Обычно это связано с требованиями повторного входа. Но все чаще даже API пользовательского интерфейса предоставляют гибкие объекты. Как правило, гибкость является самым простым и производительным вариантом. Кроме того, при реализации фабрики активации, она должна быть гибкой даже если соответствующий класс среды выполнения таковым не является.

> [!NOTE]
> Среда выполнения Windows основана от модели COM. С точки зрения модели COM гибкий класс регистрируется с помощью `ThreadingModel` = *Both*. Дополнительные сведения о потоковой модели и подразделения COM, см. в разделе [Understanding and Using COM Threading Models](https://msdn.microsoft.com/library/ms809971).

## <a name="code-examples"></a>Примеры кода

Чтобы продемонстрировать давайте используем пример реализации класса среды выполнения как C + +/ WinRT поддерживает гибкость.

```cppwinrt
#include <winrt/Windows.Foundation.h>

using namespace winrt;
using namespace Windows::Foundation;

struct MyType : winrt::implements<MyType, IStringable>
{
    winrt::hstring ToString(){ ... }
};
```

Так как мы не отказались от гибкости, эта реализация является гибкой. Базовая структура [**winrt::implements**](/uwp/cpp-ref-for-winrt/implements) реализует [**IAgileObject**](https://msdn.microsoft.com/library/windows/desktop/hh802476) и [**IMarshal**](/windows/desktop/api/objidl/nn-objidl-imarshal). Реализация **IMarshal** использует **CoCreateFreeThreadedMarshaler** для поддержки устаревшего кода, в котором нет сведений о **IAgileObject**.

Этот код проверяет объект на гибкость. Вызов [**IUnknown::as**](/uwp/cpp-ref-for-winrt/windows-foundation-iunknown#iunknownas-function) создает исключение, если `myimpl` не является гибким.

```cppwinrt
winrt::com_ptr<MyType> myimpl{ winrt::make_self<MyType>() };
winrt::com_ptr<IAgileObject> iagileobject{ myimpl.as<IAgileObject>() };
```

Вместо обработки исключения можно вызвать [**IUnknown::try_as**](/uwp/cpp-ref-for-winrt/windows-foundation-iunknown#iunknowntryas-function).

```cppwinrt
winrt::com_ptr<IAgileObject> iagileobject{ myimpl.try_as<IAgileObject>() };
if (iagileobject) { /* myimpl is agile. */ }
```

**IAgileObject** не имеет собственных методов, поэтому не дает больших возможностей для действий. Следующий вариант является более типичным.

```cppwinrt
if (myimpl.try_as<IAgileObject>()) { /* myimpl is agile. */ }
```

**IAgileObject** — это *интерфейс маркера*. Простой успех или сбой запроса **IAgileObject** указывает на объем информации и ее полезность.

## <a name="opting-out-of-agile-object-support"></a>Отказ от поддержки гибких объектов

Вы можете явным образом отказаться от поддержки гибких объектов, передав структуру маркера [**winrt::non_agile**](/uwp/cpp-ref-for-winrt/non-agile) в качестве аргумента шаблона базовому классу.

При непосредственном наследовании из **winrt::implements**.

```cppwinrt
struct MyImplementation: implements<MyImplementation, IStringable, winrt::non_agile>
{
    ...
}
```

При создании класса среды выполнения.

```cppwinrt
struct MyRuntimeClass: MyRuntimeClassT<MyRuntimeClass, winrt::non_agile>
{
    ...
}
```

Не имеет значения, где в пакете параметров располагается структура маркера.

Независимо от того, имеется ли отказаться гибкости, вы можете реализовать **IMarshal** самостоятельно. Например, можно использовать **winrt::non_agile** маркер, чтобы избежать гибкость реализация по умолчанию и реализовать **IMarshal** самостоятельно&mdash;возможно, для поддержки семантику маршалинг по значению.

## <a name="agile-references-winrtagileref"></a>Гибкие ссылки (winrt::agile_ref)

Если вы используете объект, который не является гибким, но вам необходимо передать его в каком-либо потенциально гибком контексте, то одним из вариантов будет использование шаблона структуры [**winrt::agile_ref**](/uwp/cpp-ref-for-winrt/agile-ref) для получения гибкой ссылки на экземпляр негибкого типа или на интерфейс негибкого объекта.

```cppwinrt
NonAgileType nonagile_obj;
winrt::agile_ref<NonAgileType> agile{ nonagile_obj };
```

Кроме того, можно использовать вспомогательную функцию [**winrt::make_agile**](/uwp/cpp-ref-for-winrt/make-agile).

```cppwinrt
NonAgileType nonagile_obj;
auto agile{ winrt::make_agile(nonagile_obj) };
```

В любом случае `agile` теперь можно свободно передавать в поток в другом подразделении и используется в нем.

```cppwinrt
co_await resume_background();
NonAgileType nonagile_obj_again{ agile.get() };
winrt::hstring message{ nonagile_obj_again.Message() };
```

Вызов [**agile_ref::get**](/uwp/cpp-ref-for-winrt/agile-ref#agilerefget-function) возвращает прокси, который можно безопасно использовать в контексте потока, в котором вызывается **get**.

## <a name="important-apis"></a>Важные API

* [Интерфейс IAgileObject](https://msdn.microsoft.com/library/windows/desktop/hh802476)
* [IMarshal-интерфейс](https://docs.microsoft.com/previous-versions/windows/embedded/ms887993)
* [Структура шаблона WinRT::agile_ref](/uwp/cpp-ref-for-winrt/agile-ref)
* [Структура шаблона WinRT::Implements](/uwp/cpp-ref-for-winrt/implements)
* [Шаблон функции WinRT::make_agile](/uwp/cpp-ref-for-winrt/make-agile)
* [Структура маркера WinRT::non_agile](/uwp/cpp-ref-for-winrt/non-agile)
* [WinRT::Windows::Foundation::IUnknown:: как функция](/uwp/cpp-ref-for-winrt/windows-foundation-iunknown#iunknownas-function)
* [функция WinRT::Windows::Foundation::IUnknown::try_as](/uwp/cpp-ref-for-winrt/windows-foundation-iunknown#iunknowntryas-function)

## <a name="related-topics"></a>Статьи по теме

* [Понимание и использование потоковой модели COM](https://msdn.microsoft.com/library/ms809971)

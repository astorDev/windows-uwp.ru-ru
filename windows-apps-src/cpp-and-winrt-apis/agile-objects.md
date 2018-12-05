---
description: Гибкий объект — это объект, доступ к которому может осуществляться из любого потока. Типы C++/WinRT являются гибкими по умолчанию, но вы можете это отключить.
title: Гибкие объекты в C++/WinRT
ms.date: 10/20/2018
ms.topic: article
keywords: Windows 10, uwp, стандартная, c++, cpp, winrt, проекция, гибкий, объект, гибкость, IAgileObject
ms.localizationpriority: medium
ms.openlocfilehash: 2711779f2f5fc13be19a4a10224b110564716477
ms.sourcegitcommit: c01c29cd97f1cbf050950526e18e15823b6a12a0
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/05/2018
ms.locfileid: "8700997"
---
# <a name="agile-objects-in-cwinrt"></a>Гибкие объекты в C++/WinRT

В большинстве случаев к экземплярам экземпляр класса среды выполнения Windows можно получить доступ из любого потока (так же, как можно наиболее стандартных объектов C++). Такие классы среды выполнения Windows являются *гибкими*. Лишь небольшое число классов среды выполнения Windows, которые поставляются с Windows, не являются гибкими, но при их следует принимать во внимание их потоковой модели и маршалинг (маршалинг — это передача данных через границы подразделении). Это хороший по умолчанию для каждого объекта среды выполнения Windows, были гибкими, поэтому собственные [C + +/ WinRT](/windows/uwp/cpp-and-winrt-apis/intro-to-using-cpp-with-winrt) типов являются гибкими по умолчанию.

Однако вы можете отказаться от этого. У вас может быть убедительная причина для того, чтобы объект вашего типа находился, к примеру, в заданном однопотоковом подразделении. Обычно это связано с требованиями повторного входа. Но все чаще даже API пользовательского интерфейса предоставляют гибкие объекты. Как правило, гибкость является самым простым и производительным вариантом. Кроме того, при реализации фабрики активации, она должна быть гибкой даже если соответствующий класс среды выполнения таковым не является.

> [!NOTE]
> Среда выполнения Windows основана от модели COM. С точки зрения модели COM гибкий класс регистрируется с помощью `ThreadingModel` = *Both*. Дополнительные сведения о модели потоков и подразделениями COM см. [Общие сведения о и с помощью модели COM потоков](https://msdn.microsoft.com/library/ms809971).

## <a name="code-examples"></a>Примеры кода

Мы будем использовать пример реализации класса среды выполнения для иллюстрации как C + +/ WinRT поддерживает гибкость.

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

**IAgileObject** не имеет собственных методов, поэтому не дает больших возможностей для действий.. Следующий вариант является более типичным.

```cppwinrt
if (myimpl.try_as<IAgileObject>()) { /* myimpl is agile. */ }
```

**IAgileObject** — это *интерфейс маркера*. Простой успех или сбой запроса **IAgileObject** указывает на объем информации и ее полезность.

## <a name="opting-out-of-agile-object-support"></a>Отказ от поддержки гибких объектов

Вы можете явным образом отказаться от поддержки гибких объектов, передав структуру маркера [**winrt::non_agile**](/uwp/cpp-ref-for-winrt/non_agile) в качестве аргумента шаблона базовому классу.

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

Независимо от наличия отказаться от гибкости, вы можете реализовать **интерфейса IMarshal** самостоятельно. Например, можно использовать маркера **winrt::non_agile** , чтобы избежать реализации гибкости по умолчанию и самостоятельно реализовать **интерфейс IMarshal** &mdash;например для поддержки семантики маршалирования по значению.

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
* [Интерфейс IMarshal](https://docs.microsoft.com/previous-versions/windows/embedded/ms887993)
* [Шаблон структуры winrt::agile_ref](/uwp/cpp-ref-for-winrt/agile-ref)
* [Шаблон структуры winrt::implements](/uwp/cpp-ref-for-winrt/implements)
* [Шаблон функции winrt::make_agile](/uwp/cpp-ref-for-winrt/make-agile)
* [Структура маркера winrt::non_agile](/uwp/cpp-ref-for-winrt/non_agile)
* [Функция winrt::Windows::Foundation::IUnknown::as](/uwp/cpp-ref-for-winrt/windows-foundation-iunknown#iunknownas-function)
* [Функция winrt::Windows::Foundation::IUnknown::try_as](/uwp/cpp-ref-for-winrt/windows-foundation-iunknown#iunknowntryas-function)

## <a name="related-topics"></a>Статьи по теме

* [Общее представление о моделях потоков COM и их использование](https://msdn.microsoft.com/library/ms809971)

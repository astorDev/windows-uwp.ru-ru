---
description: Гибкий объект — это объект, доступ к которому может осуществляться из любого потока. Типы C++/WinRT являются гибкими по умолчанию, но вы можете это отключить.
title: Гибкие объекты в C++/WinRT
ms.date: 04/24/2019
ms.topic: article
keywords: windows 10, uwp, standard, c++, cpp, winrt, projection, agile, object, agility, IAgileObject
ms.localizationpriority: medium
ms.openlocfilehash: 82dff619e6fa3934f69b93090bee90de6359ca07
ms.sourcegitcommit: aaa4b898da5869c064097739cf3dc74c29474691
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66360333"
---
# <a name="agile-objects-in-cwinrt"></a>Гибкие объекты в C++/WinRT

В подавляющем большинстве случаев к экземплярам класса среды выполнения Windows (как к стандартным объектам C++ ) можно получить доступ из любого потока. Таким классом среды выполнения Windows является *agile*. Лишь небольшое число классов среды выполнения Windows, которые поставляются с Windows, не являются гибкими, но при их использовании следует принимать во внимание их потоковую модель и поведение при маршалинге (маршалинг — это передача данных через границы подразделения). Желательно, чтобы все объекты среды выполнения Windows по умолчанию были гибкими, поэтому ваши собственные типы [C++/WinRT](/windows/uwp/cpp-and-winrt-apis/intro-to-using-cpp-with-winrt) являются гибкими по умолчанию.

При желании вы можете не использовать такую возможность. У вас может быть убедительная причина для того, чтобы объект вашего типа находился, к примеру, в заданном однопотоковом подразделении. Обычно это связано с требованиями повторного входа. Но все чаще даже API пользовательского интерфейса предоставляют гибкие объекты. Как правило, гибкость является самым простым и производительным вариантом. Кроме того, при реализации фабрики активации она должна быть гибкой, даже если соответствующий класс среды выполнения таковым не является.

> [!NOTE]
> Среда выполнения Windows основана на модели COM. В рамках модели COM гибкий класс регистрируется с помощью `ThreadingModel` = *Both*. Подробные сведения о моделях потоков COM см. в статье [Understanding and Using COM Threading Models](/previous-versions/ms809971(v=msdn.10)) (Общие сведения о потоковых моделях COM и их использовании).

## <a name="code-examples"></a>Примеры кода

Мы будем использовать пример реализации для класса среды выполнения, чтобы проиллюстрировать, как C++/WinRT поддерживает гибкость.

```cppwinrt
#include <winrt/Windows.Foundation.h>

using namespace winrt;
using namespace Windows::Foundation;

struct MyType : winrt::implements<MyType, IStringable>
{
    winrt::hstring ToString(){ ... }
};
```

Так как мы не отказались от гибкости, эта реализация является гибкой. Базовая структура [**winrt::implements**](/uwp/cpp-ref-for-winrt/implements) реализует [**IAgileObject**](https://docs.microsoft.com/windows/desktop/api/objidl/nn-objidl-iagileobject) и [**IMarshal**](/windows/desktop/api/objidl/nn-objidl-imarshal). Реализация **IMarshal** использует **CoCreateFreeThreadedMarshaler** для поддержки устаревшего кода, в котором нет данных об **IAgileObject**.

Этот код проверяет объект на гибкость. Вызов [**IUnknown::as**](/uwp/cpp-ref-for-winrt/windows-foundation-iunknown#iunknownas-function) создает исключение, если `myimpl` не является гибким.

```cppwinrt
winrt::com_ptr<MyType> myimpl{ winrt::make_self<MyType>() };
winrt::com_ptr<IAgileObject> iagileobject{ myimpl.as<IAgileObject>() };
```

Вместо обработки исключения вы можете вызвать [**IUnknown::try_as**](/uwp/cpp-ref-for-winrt/windows-foundation-iunknown#iunknowntry_as-function).

```cppwinrt
winrt::com_ptr<IAgileObject> iagileobject{ myimpl.try_as<IAgileObject>() };
if (iagileobject) { /* myimpl is agile. */ }
```

**IAgileObject** не имеет собственных методов, поэтому возможности для действий ограничены. Следующий вариант является более типичным.

```cppwinrt
if (myimpl.try_as<IAgileObject>()) { /* myimpl is agile. */ }
```

**IAgileObject** — это *интерфейс маркера*. Объект **IAgileObject** может предоставлять только данные об успехе или сбое выполняемого для него запроса.

## <a name="opting-out-of-agile-object-support"></a>Отказ от поддержки гибких объектов

Вы можете явным образом отказаться от поддержки гибких объектов, передав структуру маркера [**winrt::non_agile**](/uwp/cpp-ref-for-winrt/non-agile) в качестве аргумента шаблона базовому классу.

При непосредственном наследовании от **winrt::implements**:

```cppwinrt
struct MyImplementation: implements<MyImplementation, IStringable, winrt::non_agile>
{
    ...
}
```

При создании класса среды выполнения:

```cppwinrt
struct MyRuntimeClass: MyRuntimeClassT<MyRuntimeClass, winrt::non_agile>
{
    ...
}
```

Не имеет значения, где в пакете вариативных параметров располагается структура маркера.

Даже если вы отказались от гибкости, вы можете реализовать интерфейс **IMarshal** самостоятельно. Например, вы можете использовать маркер **winrt::non_agile**, чтобы избежать реализации гибкости по умолчанию и самостоятельно реализовать интерфейс **IMarshal**, например для поддержки семантики маршалирования по значению.

## <a name="agile-references-winrtagileref"></a>Гибкие ссылки (winrt::agile_ref)

Если вы используете объект, который не является гибким, но вам необходимо передать его в каком-либо потенциально гибком контексте, то одним из вариантов будет использование шаблона структуры [**winrt::agile_ref**](/uwp/cpp-ref-for-winrt/agile-ref) для получения гибкой ссылки на экземпляр негибкого типа или на интерфейс негибкого объекта.

```cppwinrt
NonAgileType nonagile_obj;
winrt::agile_ref<NonAgileType> agile{ nonagile_obj };
```

Кроме того, вы можете использовать вспомогательную функцию [**winrt::make_agile**](/uwp/cpp-ref-for-winrt/make-agile).

```cppwinrt
NonAgileType nonagile_obj;
auto agile{ winrt::make_agile(nonagile_obj) };
```

В любом случае `agile` теперь можно свободно передавать в поток в другом подразделении и использовать в нем.

```cppwinrt
co_await resume_background();
NonAgileType nonagile_obj_again{ agile.get() };
winrt::hstring message{ nonagile_obj_again.Message() };
```

Вызов [**agile_ref::get**](/uwp/cpp-ref-for-winrt/agile-ref#agile_refget-function) возвращает прокси, который можно безопасно использовать в контексте потока, в котором вызывается **get**.

## <a name="important-apis"></a>Важные API

* [Интерфейс IAgileObject](https://docs.microsoft.com/windows/desktop/api/objidl/nn-objidl-iagileobject)
* [Интерфейс IMarshal](/windows/desktop/api/objidl/nn-objidl-imarshal)
* [Шаблон структуры winrt::agile_ref](/uwp/cpp-ref-for-winrt/agile-ref)
* [Шаблон структуры winrt::implements](/uwp/cpp-ref-for-winrt/implements)
* [Шаблон функции winrt::make_agile](/uwp/cpp-ref-for-winrt/make-agile)
* [Структура маркера winrt::non_agile](/uwp/cpp-ref-for-winrt/non-agile)
* [Функция winrt::Windows::Foundation::IUnknown::as](/uwp/cpp-ref-for-winrt/windows-foundation-iunknown#iunknownas-function)
* [Функция winrt::Windows::Foundation::IUnknown::try_as](/uwp/cpp-ref-for-winrt/windows-foundation-iunknown#iunknowntry_as-function)

## <a name="related-topics"></a>Статьи по теме

* [Общие сведения о моделях потоков COM и их использовании](/previous-versions/ms809971(v=msdn.10))

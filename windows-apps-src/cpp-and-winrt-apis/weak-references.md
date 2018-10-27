---
author: stevewhims
description: Среда выполнения Windows — это система подсчетом ссылок; и в системе, важно знать о значимость и разницу между надежной и слабые ссылки.
title: Слабые ссылки в C++/WinRT
ms.author: stwhi
ms.date: 10/03/2018
ms.topic: article
keywords: Windows 10, uwp, стандартные, c ++, cpp, winrt, проекция, надежную, слабая, ссылка
ms.localizationpriority: medium
ms.openlocfilehash: c37319ce7f7d29acfc2c1822e76fadc29b5ec863
ms.sourcegitcommit: d0e836dfc937ebf7dfa9c424620f93f3c8e0a7e8
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/26/2018
ms.locfileid: "5664855"
---
# <a name="strong-and-weak-references-in-cwinrt"></a>Надежная и слабые ссылки в C + +/ WinRT

Среда выполнения Windows — это система подсчетом ссылок; и в таких систем важно знать о значимость и разницу между, strong и слабые ссылки (и ссылки, которые ни один из них, такие как неявный *Этот* указатель). Как вы увидите в этом разделе, знать, как правильно управлять эти ссылки может составлять разницу между надежную систему, который выполняется плавно, а тот, который дает сбой непредсказуемо. За счет вспомогательные функции, которые поддерживают подробный в языковой проекции [C + +/ WinRT](/windows/uwp/cpp-and-winrt-apis/intro-to-using-cpp-with-winrt) соответствует вы полупути в вашем действия по созданию более сложных систем просто и правильно.

## <a name="safely-accessing-the-this-pointer-in-a-class-member-coroutine"></a>Безопасный доступ к *этой* указателя в сопрограмме член класса

Приведенный ниже код показывает типичный пример сопрограммы, функция-член класса.

```cppwinrt
// pch.h
#pragma once
#include <iostream>
#include <winrt/Windows.Foundation.h>

// main.cpp : Defines the entry point for the console application.
#include "pch.h"

using namespace winrt;
using namespace Windows::Foundation;
using namespace std::chrono_literals;

struct MyClass : winrt::implements<MyClass, IInspectable>
{
    winrt::hstring m_value{ L"Hello, World!" };

    IAsyncOperation<winrt::hstring> RetrieveValueAsync()
    {
        co_await 5s;
        co_return m_value;
    }
};

int main()
{
    winrt::init_apartment();

    auto myclass_instance{ winrt::make_self<MyClass>() };
    auto async{ myclass_instance->RetrieveValueAsync() };

    winrt::hstring result{ async.get() };
    std::wcout << result.c_str() << std::endl;
}
```

**MyClass::RetrieveValueAsync** работать некоторое время, а затем в конечном итоге она возвращает копию `MyClass::m_value` элемента данных. Вызов **RetrieveValueAsync** вызывает асинхронный объект должен быть создан, и этот объект имеет неявный *Этот* указатель (через который, в конечном итоге `m_value` осуществляется).

Ниже приведен полный последовательность событий.

1. В **основной**, создается экземпляр класса **MyClass** (`myclass_instance`).
2. `async` Создается объект, указывающим (через его *Этот*) `myclass_instance`.
3. Функция **winrt::Windows::Foundation::IAsyncAction::get** блокирует на несколько секунд и возвращает результат **RetrieveValueAsync**.
4. **RetrieveValueAsync** возвращает значение `this->m_value`.

Шаг 4 безопасен, до тех пор, пока *это* является допустимым.

Но что делать, если уничтожения экземпляра класса до завершения асинхронной операции? Существует множество возможностей, которые может проходить экземпляр класса за пределы области до завершения асинхронного метода. Но мы можете имитировать, установив экземпляр класса `nullptr`.

```cppwinrt
int main()
{
    winrt::init_apartment();

    auto myclass_instance{ winrt::make_self<MyClass>() };
    auto async{ myclass_instance->RetrieveValueAsync() };
    myclass_instance = nullptr; // Simulate the class instance going out of scope.

    winrt::hstring result{ async.get() }; // Behavior is now undefined; crashing is likely.
    std::wcout << result.c_str() << std::endl;
}
```

После точки, где мы уничтожить экземпляр класса выглядит так, будто не упоминаться непосредственно к нему еще раз. Но конечно *Этот* указатель к нему асинхронного объекта и пытается использовать, копировать значению, сохраненному в экземпляр класса. Сопрограммы функция-член, и он ожидает, что сможет использовать его *для этого* указателя с impunity.

После этого изменения в код мы возникнут проблемы в шаге 4, так как удаления экземпляр класса, а *это* больше не является допустимым. Сразу же после асинхронного объекта пытается получить доступ к переменной внутри экземпляра класса, он будет сбой (или сделать что-то совершенно не определено).

Решение заключается в предоставлении асинхронной операции&mdash;сопрограмма&mdash;собственной строгую ссылку на экземпляр класса. Как в настоящее время письменного сопрограммы эффективно содержит необработанные *Этот* указатель на экземпляр класса; но это не достаточно поддерживать активность экземпляр класса.

Чтобы поддерживать активность экземпляр класса, измените реализацию **RetrieveValueAsync** на, показано ниже.

```cppwinrt
IAsyncOperation<winrt::hstring> RetrieveValueAsync()
{
    auto strong_this{ get_strong() }; // Keep *this* alive.
    co_await 5s;
    co_return m_value;
}
```

Поскольку C + +/ WinRT прямо или косвенно является производным от шаблона [**winrt::implements**](/uwp/cpp-ref-for-winrt/implements) C + +/ WinRT объекта можно вызвать его функцию-член [**implements.get_strong**](/uwp/cpp-ref-for-winrt/implements#implementsgetstrong-function) защищенные получить строгую ссылку на его *Этот* указатель. Обратите внимание, что нет необходимости использовать фактически `strong_this` переменной; вызывая **get_strong** увеличивает вашего счетчик ссылок и сохраняет вашего неявный *Этот* указатель допустимым.

Это устраняет проблему, который ранее был при полученном в шаге 4. Даже если все ссылки на экземпляр класса исчезнуть, сопрограммы предпринял гарантируя, что его зависимости стабильны меры предосторожности.

Если подходит не строгую ссылку, можно вместо этого вызвать [**implements::get_weak**](/uwp/cpp-ref-for-winrt/implements#implementsgetweak-function) для получения *этого*слабую ссылку. Просто убедитесь, что вы можете получить строгую ссылку перед получением доступа к *этой*.

```cppwinrt
IAsyncOperation<winrt::hstring> RetrieveValueAsync()
{
    auto weak_this{ get_weak() }; // Maybe keep *this* alive.

    co_await 5s;

    if (auto strong_this{ weak_this.get() })
    {
        co_return m_value;
    }
    else
    {
        co_return L"";
    }
}
```

В приведенном выше примере слабых ссылок не хранить экземпляр класса от уничтожения, когда строгой ссылок не останется. Однако она предоставляет способ проверки, можно ли получить строгую ссылку перед получением доступа к переменной-члена.

## <a name="safely-accessing-the-this-pointer-with-an-event-handling-delegate"></a>Безопасный доступ к *этой* указателя с помощью делегата обработки события

### <a name="the-scenario"></a>Сценарий

Общие сведения об обработке событий см. в разделе [Обработка событий с помощью делегатов в C + +/ WinRT](handle-events.md).

Предыдущий раздел выделенный потенциальных проблем с жизненным циклом в областях сопрограммы и параллелизм. Однако если обработки события с помощью функции-члена объекта, либо в лямбда-функции внутри функции-члена объекта, то вам необходимо подумать о относительный время существования получателя события (объект, обработав событие) и источник события (объект вызов события). Рассмотрим некоторые примеры кода.

Во-первых, пример кода ниже определяет простой **ИсточникСобытия** класс, который вызывает универсальный событие, которое обрабатывается любые делегаты, которые были добавлены к нему. Это событие примере происходит для использования типа делегата [**Windows::Foundation:: EventHandler**](/uwp/api/windows.foundation.eventhandler) , но проблем и способов их устранения ниже относятся к типам всех делегата.

Затем класс **EventRecipient** предоставляет обработчик для события **EventSource::Event** в виде лямбда-функции.

```cppwinrt
// pch.h
#pragma once
#include <iostream>
#include <winrt/Windows.Foundation.h>

// main.cpp : Defines the entry point for the console application.
#include "pch.h"

using namespace winrt;
using namespace Windows::Foundation;

struct EventSource
{
    winrt::event<EventHandler<int>> m_event;

    void Event(EventHandler<int> const& handler)
    {
        m_event.add(handler);
    }

    void RaiseEvent()
    {
        m_event(nullptr, 0);
    }
};

struct EventRecipient : winrt::implements<EventRecipient, IInspectable>
{
    winrt::hstring m_value{ L"Hello, World!" };

    void Register(EventSource& event_source)
    {
        event_source.Event([&](auto&& ...)
        {
            std::wcout << m_value.c_str() << std::endl;
        });
    }
};

int main()
{
    winrt::init_apartment();

    EventSource event_source;
    auto event_recipient{ winrt::make_self<EventRecipient>() };
    event_recipient->Register(event_source);
    event_source.RaiseEvent();
}
```

Процедура что получатель событий имеет обработчик событий лямбда-функция каких-либо зависимостей для его *этого* указателя. Всякий раз, когда получатель события время существования на источник события, время существования этих зависимостей. И в этих случаях, которые являются общими, шаблон работает правильно. Некоторые из таких случаев очевидны, например при обработке странице пользовательского интерфейса события, вызванного элементом управления, который находится на этой странице. Кнопка время существования страницы&mdash;таким образом, обработчик также время существования кнопки. Эта ситуация верна всегда, когда получатель является владельцем источника (например, в качестве элемента данных), или когда получатель и источник являются родственными и напрямую принадлежат другому объекту. Если вы уверены, что в вашей ситуации время существования обработчика не превышает времени существования объекта *this*, от которого он зависит, можно записать *this* обычным образом, без учета времени существования.

Однако в некоторых случаях, когда *это* не превышает время его использования в обработчике (включая обработчики для завершения и выполнения события, вызываемые асинхронных действий и операций), и очень важно знать, как их решения.

- Это возможно, если вы создаете сопрограмму для реализации асинхронного метода.
- В редких случаях с определенными объектами платформы пользовательского интерфейса XAML (например, [**SwapChainPanel**](/uwp/api/windows.ui.xaml.controls.swapchainpanel)) это возможно, если получатель завершается без отмены регистрации в источнике событий.

### <a name="the-issue"></a>Проблема

Этот следующей версии **Основная** функция имитирует, что происходит при уничтожении получатель событий (возможно оно выходит за пределы области) во время на источник события по-прежнему является создания событий.

```cppwinrt
int main()
{
    winrt::init_apartment();

    EventSource event_source;
    auto event_recipient{ winrt::make_self<EventRecipient>() };
    event_recipient->Register(event_source);
    event_recipient = nullptr; // Simulate the event recipient going out of scope.
    event_source.RaiseEvent(); // Behavior is now undefined within the lambda event handler; crashing is likely.
}
```

Получатель событий разрушается, но лямбда-функции обработчика событий в ней по-прежнему подписка на событие **событий** . При возникновении этого события, лямбда-выражение пытается разыменования *Этот* указатель, который на данный момент является недопустимым. Таким образом, нарушение прав доступа в результате код в обработчике (или в продолжении сопрограммы, который) пытается использовать его.

> [!IMPORTANT]
> При возникновении такой ситуации, вам следует подумать о времени существования *этого* объекта; и ли записанный *Этот* объект время существования захвата. Если нет, записывайте его с помощью строгой или слабой ссылки, мы продемонстрируем ниже.
>
> Или&mdash;Если имеет смысл для вашего сценария и рекомендации по позволяют даже&mdash;— вы можете отозвать обработчик после получатель завершил работу с событием, либо в деструкторе получателя. См. в разделе, [Отзыв зарегистрированного делегата](handle-events.md#revoke-a-registered-delegate).

Это, как регистрации обработчика.

```cppwinrt
event_source.Event([&](auto&& ...)
{
    std::wcout << m_value.c_str() << std::endl;
});
```

Лямбда-функция автоматически захватывает любые локальные переменные по ссылке. Таким образом в этом примере мы бы аналогично написать это.

```cppwinrt
event_source.Event([this](auto&& ...)
{
    std::wcout << m_value.c_str() << std::endl;
});
```

В обоих случаях просто захват необработанных *Этот* указатель. И, не влияет на подсчет ссылок, поэтому ничего не препятствует уничтожении текущего объекта.

### <a name="the-solution"></a>Решение

Решением является захват строгую ссылку. Строгую ссылку *делает* увеличить количество ссылок и оно *выполняет* Сохранение текущего объекта проверки активности. Просто объявите переменную захвата (под названием `strong_this` в этом примере) и инициализировать его с помощью вызова [**implements.get_strong**](/uwp/cpp-ref-for-winrt/implements#implementsgetstrong-function), который получает строгую ссылку на наших *Этот* указатель.

```cppwinrt
event_source.Event([this, strong_this { get_strong()}](auto&& ...)
{
    std::wcout << m_value.c_str() << std::endl;
});
```

Даже можно опустить автоматического захвата текущего объекта и доступом элемент данных через переменную захвата вместо через неявный *этого*.

```cppwinrt
event_source.Event([strong_this { get_strong()}](auto&& ...)
{
    std::wcout << strong_this->m_value.c_str() << std::endl;
});
```

Если подходит не строгую ссылку, можно вместо этого вызвать [**implements::get_weak**](/uwp/cpp-ref-for-winrt/implements#implementsgetweak-function) для получения *этого*слабую ссылку. Просто убедитесь, что вы можете по-прежнему получить строгую ссылку из него перед получением доступа к членам.

```cppwinrt
event_source.Event([weak_this{ get_weak() }](auto&& ...)
{
    if (auto strong_this{ weak_this.get() })
    {
        std::wcout << strong_this->m_value.c_str() << std::endl;
    }
});
```

### <a name="if-you-use-a-member-function-as-a-delegate"></a>Если вы используете функцию-член как делегат

А также лямбда-функции, эти принципы также применимы к используя функцию-член в качестве вашего делегата. Синтаксис отличается, поэтому давайте рассмотрим код. Во-первых Вот потенциально опасных член функции обработчик событий с помощью необработанных *Этот* указатель.

```cppwinrt
struct EventRecipient : winrt::implements<EventRecipient, IInspectable>
{
    winrt::hstring m_value{ L"Hello, World!" };

    void Register(EventSource& event_source)
    {
        event_source.Event({ this, &EventRecipient::OnEvent });
    }

    void OnEvent(IInspectable const& /* sender */, int /* args */)
    {
        std::wcout << m_value.c_str() << std::endl;
    }
};
```

Этот способ обычных, стандартные для ссылки на объект и его функцию-член. Чтобы сделать это безопасно, вы можете&mdash;начиная с версии 10.0.17763.0 (Windows 10, версия 1809) пакета Windows SDK&mdash;установить строгой или слабой ссылки в точке, где регистрируется обработчик. В этой точке объект получателя события известно, что оно по-прежнему проверки активности.

Для строгой ссылки просто вызовите [**get_strong**](/uwp/cpp-ref-for-winrt/implements#implementsgetstrong-function) вместо необработанных *Этот* указатель. C + +/ WinRT гарантирует, что получившийся делегат содержит строгую ссылку на текущий объект.

```cppwinrt
event_source.Event({ get_strong(), &EventRecipient::OnEvent });
```

Вызываем [**get_weak**](/uwp/cpp-ref-for-winrt/implements#implementsgetweak-function)слабой ссылки. C + +/ WinRT гарантирует, что получившийся делегат содержит слабую ссылку. В последний момент, незаметно, делегат пытается разрешить слабых ссылок до более надежной и вызывает функцию-член, только если попытка будет успешной.

```cppwinrt
event_source.Event({ get_weak(), &EventRecipient::OnEvent });
```

### <a name="a-weak-reference-example-using-swapchainpanelcompositionscalechanged"></a>Пример слабые ссылки, с помощью **SwapChainPanel::CompositionScaleChanged**

В этом примере кода мы используем событие [**SwapChainPanel::CompositionScaleChanged**](/uwp/api/windows.ui.xaml.controls.swapchainpanel.compositionscalechanged) через другой иллюстрация слабые ссылки. Этот код регистрирует обработчика событий с помощью лямбда-функции, создающей слабую ссылку на получателя.

```cppwinrt
winrt::Windows::UI::Xaml::Controls::SwapChainPanel m_swapChainPanel;
winrt::event_token m_compositionScaleChangedEventToken;

void RegisterEventHandler()
{
    m_compositionScaleChangedEventToken = m_swapChainPanel.CompositionScaleChanged([weak_this{ get_weak() }]
        (Windows::UI::Xaml::Controls::SwapChainPanel const& sender,
        Windows::Foundation::IInspectable const& object)
    {
        if (auto strong_this{ weak_this.get() })
        {
            strong_this->OnCompositionScaleChanged(sender, object);
        }
    });
}

void OnCompositionScaleChanged(Windows::UI::Xaml::Controls::SwapChainPanel const& sender,
    Windows::Foundation::IInspectable const& object)
{
    // Here, we know that the "this" object is valid.
}
```

В предложении захвата лямбда-функции создается временная переменная, представляющая собой слабую ссылки на объект *this*. В теле лямбда-функции, если можно получить строгую ссылку на объект *this*, вызывается функция **OnCompositionScaleChanged**. Таким образом внутри **OnCompositionScaleChanged** можно безопасно использовать объект *this*.

## <a name="weak-references-in-cwinrt"></a>Слабые ссылки в C++/WinRT

Выше мы увидели, используется слабые ссылки. Как правило они подходят для нарушения циклических ссылок. Например, для собственной реализации инфраструктуры пользовательского интерфейса на основе XAML&mdash;ввиду исторических особенностей проектирования инфраструктуры&mdash;механизма слабых ссылок в C + +/ WinRT необходим для обработки циклических ссылок. За пределами XAML, тем не менее, скорее всего не нужно использовать слабые ссылки (что существует ничего не по своей природе специфичными для XAML о них). Вместо чаще, можно создавать собственные C + +/ WinRT API-интерфейсы в таким образом, чтобы избежать необходимости циклических и слабых ссылок. 

Для конкретного декларируемого вами типа C++/WinRT на первый взгляд не очевидно, требуются ли слабые ссылки, и если да, то когда. Поэтому C++/ WinRT автоматически обеспечивает поддержку слабых ссылок в шаблоне структуры [**winrt::implements**](/uwp/cpp-ref-for-winrt/implements), от которого напрямую или косвенно наследуются ваши собственные типы C++/WinRT. Поддержка слабых ссылок не расходует ресурсов до тех пор, пока у вашего объекта не запрашивается [**IWeakReferenceSource**](/windows/desktop/api/weakreference/nn-weakreference-iweakreferencesource). Кроме того, вы можете явным образом [отказаться от этой поддержки](#opting-out-of-weak-reference-support).

### <a name="code-examples"></a>Примеры кода
Шаблон структуры [**winrt::weak_ref**](/uwp/cpp-ref-for-winrt/weak-ref) является одним из вариантов получения слабой ссылки на экземпляр класса.

```cppwinrt
Class c;
winrt::weak_ref<Class> weak{ c };
```

Кроме того, можно использовать вспомогательную функцию [**winrt::make_weak**](/uwp/cpp-ref-for-winrt/make-weak).

```cppwinrt
Class c;
auto weak = winrt::make_weak(c);
```

Создание слабой ссылки не влияет на количество ссылок на сам объект, а просто приводит к выделению блока управления. Этот блок управления отвечает за реализацию семантики слабой ссылки. Затем вы можете попытаться превратить слабую ссылку в строгую и в случае успеха ее использовать.

```cppwinrt
if (Class strong = weak.get())
{
    // use strong, for example strong.DoWork();
}
```

При условии, что существуют некоторые другие строгие ссылки, вызов [**weak_ref::get**](/uwp/cpp-ref-for-winrt/weak-ref#weakrefget-function) увеличивает количество ссылок и возвращает строгую ссылку в вызывающий код.

### <a name="opting-out-of-weak-reference-support"></a>Отказ от поддержки слабых ссылок.
Поддержка слабых ссылок осуществляется автоматически. Однако вы можете явным образом отказаться от этой поддержки, передав структуру маркера [**winrt::no_weak_ref**](/uwp/cpp-ref-for-winrt/no-weak-ref) в качестве аргумента шаблона базовому классу.

При непосредственном наследовании из **winrt::implements**.

```cppwinrt
struct MyImplementation: implements<MyImplementation, IStringable, no_weak_ref>
{
    ...
}
```

При создании класса среды выполнения.

```cppwinrt
struct MyRuntimeClass: MyRuntimeClassT<MyRuntimeClass, no_weak_ref>
{
    ...
}
```

Не имеет значения, где в пакете параметров располагается структура маркера. При запросе слабой ссылки для типа с отключенной поддержкой компилятор отобразит сообщение "*This is only for weak ref support*" ("Только для поддержки слабых ссылок").

## <a name="important-apis"></a>Важные API
* [implements::get_weak function](/uwp/cpp-ref-for-winrt/implements#implementsgetweak-function)
* [Шаблон функции winrt::make_weak](/uwp/cpp-ref-for-winrt/make-weak)
* [Структура маркера winrt::no_weak_ref](/uwp/cpp-ref-for-winrt/no-weak-ref)
* [Шаблон структуры winrt::weak_ref](/uwp/cpp-ref-for-winrt/weak-ref)

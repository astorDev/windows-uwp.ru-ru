---
description: Среда выполнения Windows — это система с подсчетом ссылок; и в системе, очень важно узнать о значимость и различие между сильные и слабые ссылки.
title: Слабые ссылки в C++/WinRT
ms.date: 10/03/2018
ms.topic: article
keywords: Windows 10, uwp, стандартные, c ++, cpp, winrt, проекции, надежный, ненадежным, ссылка
ms.localizationpriority: medium
ms.custom: RS5
ms.openlocfilehash: 507b3cee71819df1d0163380a494e6a15936109f
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57630819"
---
# <a name="strong-and-weak-references-in-cwinrt"></a>Сильные и слабые ссылки в C + +/ WinRT

Среда выполнения Windows — это система с подсчетом ссылок; и в системе, очень важно узнать о значимость и различие между сильные и слабые ссылки (и ссылки, ни одного, такие как неявный *это* указатель). Как вы увидите в этом разделе, нужно знать, как правильно управлять эти ссылки может означать разницу между надежную систему, прошел, а второе непредсказуемо аварийно завершает работу. Предоставляя вспомогательные функции, которые имеют глубокой поддержкой в проекции языка, [C + +/ WinRT](/windows/uwp/cpp-and-winrt-apis/intro-to-using-cpp-with-winrt) удовлетворяет вашим равном работы построения более сложных систем, просто и правильно.

## <a name="safely-accessing-the-this-pointer-in-a-class-member-coroutine"></a>Безопасного доступа к *это* указатель в соподпрограмме членов класса

Кода ниже показан типичный соподпрограммой, которая является функцией-членом класса.

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

**MyClass::RetrieveValueAsync** работает в течение некоторого времени, после чего в конечном итоге возвращает копию `MyClass::m_value` элемент данных. Вызов **RetrieveValueAsync** вызывает асинхронный объект должен быть создан, и этот объект содержит неявный *это* указатель (через который, в конечном итоге `m_value` осуществляется).

Ниже приведен полный последовательность событий.

1. В **основной**, экземпляр **MyClass** создается (`myclass_instance`).
2. `async` Создается объект, указывающий (через его *это*) для `myclass_instance`.
3. **Winrt::Windows::Foundation::IAsyncAction::get** функция блокирует на несколько секунд, а затем возвращает результат **RetrieveValueAsync**.
4. **RetrieveValueAsync** возвращает значение `this->m_value`.

Шаг 4 безопасна, пока *это* является допустимым.

Но что делать, если класс уничтожается до завершения асинхронной операции? Существует множество возможностей, которые экземпляр класса может выходить за пределы области до завершения асинхронного метода. Но ее можно имитировать, установив для экземпляра класса `nullptr`.

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

После запятой, где мы уничтожения экземпляра класса похоже, мы не ссылаются непосредственно на его снова. Но само собой асинхронного объекта имеет *это* указатель и пытается использовать, чтобы скопировать значение, хранящееся в экземпляре класса. Сопрограммы является функцией-членом, и он ожидает иметь возможность использовать его *это* указатель с impunity.

Это изменение в код, мы столкнулись с проблемами на шаге 4, так как экземпляр класса был удален, и *это* больше не является допустимым. Сразу же после асинхронного объекта пытается получить доступ к переменной внутри экземпляра класса, он будет о сбоях (либо каким-либо полностью не определено).

Решение заключается в предоставлении асинхронной операции&mdash;соподпрограмме&mdash;свой собственный строгую ссылку на экземпляр класса. В настоящее время записи, сопрограммы фактически содержит необработанные *это* указатель на экземпляр класса; но этого недостаточно для сохранения экземпляра класса.

Для сохранения экземпляра класса, измените реализацию **RetrieveValueAsync** показанному ниже.

```cppwinrt
IAsyncOperation<winrt::hstring> RetrieveValueAsync()
{
    auto strong_this{ get_strong() }; // Keep *this* alive.
    co_await 5s;
    co_return m_value;
}
```

Поскольку C + +/ WinRT объект прямо или косвенно является производным от [ **winrt::implements** ](/uwp/cpp-ref-for-winrt/implements) шаблона, C + +/ WinRT объекта можно вызвать его [ **implements.get_strong** ](/uwp/cpp-ref-for-winrt/implements#implementsgetstrong-function) защищенная функция-член для извлечения строгую ссылку на его *это* указатель. Обратите внимание, что нет необходимости использовать `strong_this` переменной; просто вызова **get_strong** увеличивает ваш счетчик ссылок, а также отслеживает ваш неявное *это* указатель допустимым.

Это позволяет устранить проблему, имевшийся ранее, когда у нас к шагу 4. Даже если все ссылки на экземпляр класса исчезают, сопрограммы были предприняты никакие меры предосторожности и гарантируя, что его зависимости стабильны.

Если строгую ссылку не подходит, то вместо этого можно вызвать [ **implements::get_weak** ](/uwp/cpp-ref-for-winrt/implements#implementsgetweak-function) извлекаемого слабой ссылки *это*. Просто убедитесь, что можно получить строгую ссылку перед доступом к *это*.

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

В приведенном выше примере слабой ссылки без сохранения экземпляра класса от уничтожения при отсутствии строгих ссылок остаются. Но он предоставляет способ проверки, можно ли получить строгую ссылку перед обращением к переменной-члена.

## <a name="safely-accessing-the-this-pointer-with-an-event-handling-delegate"></a>Безопасного доступа к *это* указатель с помощью делегата, обрабатывающего события

### <a name="the-scenario"></a>Сценарий

Общие сведения об обработке событий см. в разделе [обработки событий с помощью делегатов в C + +/ WinRT](handle-events.md).

Выше выделены потенциальные проблемы времени существования в областях сопрограммы и параллелизмом. Но если вы обрабатываете события с помощью функции-члена объекта или в лямбда-функции внутри функции-члена объекта, то необходимо думать о относительного времени существования получателя событий (объект, обрабатывающий событие) и источник события (объект порождает событие). Давайте рассмотрим несколько примеров кода.

Ниже первый листинг кода определяет простой **EventSource** класс, который вызывает общим событием, которое обрабатывается все делегаты, которые были добавлены к нему. Этот пример события происходит с помощью [ **Windows::Foundation::EventHandler** ](/uwp/api/windows.foundation.eventhandler) тип делегата, но проблемы и способы устранения здесь применяются к типам все делегата.

Затем **EventRecipient** класс предоставляет обработчик для **EventSource::Event** события в виде лямбда-функции.

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

Шаблон является то, что получатель событий обработчик событий лямбда-выражения с зависимостями его *это* указатель. Каждый раз, когда получатель событий, превышает источника событий, он превышает время жизни этих зависимостей. И в таких случаях, которые используются, шаблон работает хорошо. Некоторые из таких случаев очевидны, например при обработке странице пользовательского интерфейса события, вызванного элементом управления, который находится на этой странице. Страницы, превышает кнопки&mdash;таким образом, обработчик также превышает время жизни кнопки. Эта ситуация верна всегда, когда получатель является владельцем источника (например, в качестве элемента данных), или когда получатель и источник являются родственными и напрямую принадлежат другому объекту. Если вы уверены, что в вашей ситуации время существования обработчика не превышает времени существования объекта *this*, от которого он зависит, можно записать *this* обычным образом, без учета времени существования.

Однако в некоторых случаях где *это* не пережить его использования в обработчике (включая обработчики событий завершения и хода выполнения, вызываемых асинхронных действий и операций), и это важно знать, как работать с ними.

- Это возможно, если вы создаете сопрограмму для реализации асинхронного метода.
- В редких случаях с определенными объектами платформы пользовательского интерфейса XAML (например, [**SwapChainPanel**](/uwp/api/windows.ui.xaml.controls.swapchainpanel)) это возможно, если получатель завершается без отмены регистрации в источнике событий.

### <a name="the-issue"></a>Проблема

Следующая версия этой **основной** функция имитирует, что происходит при уничтожении получателя событий (возможно он выходит за пределы области) пока источник события по-прежнему является создание событий.

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

Получатель событий удаляется, но обработчик событий лямбда-выражения в ней по-прежнему является подписанным на **событий** событий. При возникновении этого события, лямбда-выражение пытается разыменования *это* указатель, который недопустим в этой точке. Таким образом, нарушение прав доступа в результате код в обработчике (или в продолжение соподпрограммы) попыткой его использования.

> [!IMPORTANT]
> При возникновении подобной ситуации, то вам необходимо подумать о времени существования *это* объекта; и ли записанные *это* объекта превышает время жизни записи. Если это не так, затем запишите ее с со строгим или слабую ссылку, мы продемонстрируем ниже.
>
> Или&mdash;имеет смысл для вашего сценария, если работа с потоками рекомендации позволяют даже&mdash;то другой вариант — отменить обработчик, после завершения получателя с событием, или в деструкторе получателя. См. в разделе [отозвать зарегистрированный делегат](handle-events.md#revoke-a-registered-delegate).

Это, как при регистрации обработчика.

```cppwinrt
event_source.Event([&](auto&& ...)
{
    std::wcout << m_value.c_str() << std::endl;
});
```

Лямбда-выражения автоматически захватывает всех локальных переменных по ссылке. Таким образом в этом примере мы было что эквивалентно бы написать следующее.

```cppwinrt
event_source.Event([this](auto&& ...)
{
    std::wcout << m_value.c_str() << std::endl;
});
```

В обоих случаях мы просто захватываются необработанный *это* указатель. И, не оказывает влияния на подсчет ссылок, поэтому ничего не препятствует уничтожения текущего объекта.

### <a name="the-solution"></a>Решение

Решение заключается в том, чтобы записать строгую ссылку. Строгая ссылка *does* увеличивать число ссылок и его *does* сохранения текущего объекта. Вы просто объявите переменную захвата (вызывается `strong_this` в этом примере) и инициализируйте его с помощью вызова [ **implements.get_strong**](/uwp/cpp-ref-for-winrt/implements#implementsgetstrong-function), который получает строгую ссылку на наш  *Это* указатель.

```cppwinrt
event_source.Event([this, strong_this { get_strong()}](auto&& ...)
{
    std::wcout << m_value.c_str() << std::endl;
});
```

Вы можете даже опустить автоматической записи текущего объекта и доступа к члену данных через переменную вместо записи с помощью неявного *это*.

```cppwinrt
event_source.Event([strong_this { get_strong()}](auto&& ...)
{
    std::wcout << strong_this->m_value.c_str() << std::endl;
});
```

Если строгую ссылку не подходит, то вместо этого можно вызвать [ **implements::get_weak** ](/uwp/cpp-ref-for-winrt/implements#implementsgetweak-function) извлекаемого слабой ссылки *это*. Просто убедитесь, что можно по-прежнему получить строгую ссылку из него перед доступом к членам.

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

Как и лямбда-функции, эти принципы также применимы к с помощью функции-члена представителя. Синтаксис отличается, так что давайте взглянем на код. Во-первых, вот потенциально небезопасных члена функции обработчик событий с помощью необработанного *это* указатель.

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

Это обычных, стандартный способ ссылки на объект и его функцию-член. Чтобы сделать это безопасно, вы можете&mdash;по состоянию на версию пакета SDK Windows (Windows 10, версия 1809) 10.0.17763.0&mdash;установить надежный или слабую ссылку в точке, в котором зарегистрирован обработчик. На этом этапе по-прежнему активно известен объект получателя событий.

Строгая ссылка, просто вызовите [ **get_strong** ](/uwp/cpp-ref-for-winrt/implements#implementsgetstrong-function) вместо необработанный *это* указатель. C + +/ WinRT гарантирует, что результирующий делегат содержит строгую ссылку на текущий объект.

```cppwinrt
event_source.Event({ get_strong(), &EventRecipient::OnEvent });
```

Слабую ссылку, вызовите [ **get_weak**](/uwp/cpp-ref-for-winrt/implements#implementsgetweak-function). C + +/ WinRT гарантирует, что результирующий делегат содержит слабой ссылки. В последнюю минуту, а также за кулисами делегат пытается разрешить слабая ссылка строгое тот и вызывает функцию-член, только при успешном выполнении.

```cppwinrt
event_source.Event({ get_weak(), &EventRecipient::OnEvent });
```

### <a name="a-weak-reference-example-using-swapchainpanelcompositionscalechanged"></a>Пример использования слабых ссылок **SwapChainPanel::CompositionScaleChanged**

В этом примере мы используем [ **SwapChainPanel::CompositionScaleChanged** ](/uwp/api/windows.ui.xaml.controls.swapchainpanel.compositionscalechanged) событий посредством другую иллюстрацию слабых ссылок. Этот код регистрирует обработчик событий, с помощью лямбда-выражение, позволяющее перехватывать слабую ссылку получателю.

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

Выше мы видели использования слабых ссылок. Как правило они хорошо подходят для разрыва циклических ссылок. Например, для собственной реализации инфраструктуры пользовательского интерфейса на основе XAML&mdash;из-за прошлых проектирования инфраструктуры&mdash;слабая ссылка механизма в C + +/ WinRT необходимо обработать циклических ссылок. За пределами XAML, однако вы скорее всего не потребуется использовать слабые ссылки (not, что нет действий по своей природе XAML конкретных о них). Вместо этого чаще, можно разработать собственный C + +/ WinRT API-интерфейсы таким образом, чтобы избежать циклических ссылок и слабых ссылок. 

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
* [функция Implements::get_weak](/uwp/cpp-ref-for-winrt/implements#implementsgetweak-function)
* [Шаблон функции WinRT::make_weak](/uwp/cpp-ref-for-winrt/make-weak)
* [winrt::no_weak_ref marker struct](/uwp/cpp-ref-for-winrt/no-weak-ref)
* [Структура шаблона WinRT::weak_ref](/uwp/cpp-ref-for-winrt/weak-ref)

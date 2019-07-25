---
description: Среда выполнения Windows — это система с учетом ссылок. В подобной системе очень важно знать о значении сильных и слабых ссылок, а также о различиях между ними.
title: Слабые ссылки в C++/WinRT
ms.date: 05/16/2019
ms.topic: article
keywords: windows 10, uwp, standard, c++, cpp, winrt, projection, strong, weak, reference
ms.localizationpriority: medium
ms.custom: RS5
ms.openlocfilehash: 3ad6bb9a98b0fe2a699580001698740e44cea14f
ms.sourcegitcommit: cba3ba9b9a9f96037cfd0e07d05bd4502753c809
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/14/2019
ms.locfileid: "67870324"
---
# <a name="strong-and-weak-references-in-cwinrt"></a>Сильные и слабые ссылки в C++/WinRT

Среда выполнения Windows — это система с учетом ссылок. В подобной системе очень важно знать о значении сильных и слабых ссылок, а также о различиях между ними (и о других ссылках, например о неявном указателе *this*). Сведения в этой статье помогут вам понять, что при правильном управлении этими ссылками вы получите надежную, эффективно функционирующую систему, а не систему, в которой происходят непредсказуемые сбои. Предоставление вспомогательных функций с мощной поддержкой в проекции языка [C++/WinRT](/windows/uwp/cpp-and-winrt-apis/intro-to-using-cpp-with-winrt) удовлетворит ваши требования к простой и правильной работе по построению более сложных систем.

## <a name="safely-accessing-the-this-pointer-in-a-class-member-coroutine"></a>Безопасный доступ к указателю *this* в сопрограмме членов класса

Дополнительные сведения о соподпрограммах и примеры кода приведены в разделе [Параллельная обработка и асинхронные операции с помощью C++/WinRT](/windows/uwp/cpp-and-winrt-apis/concurrency).

Ниже приведен типичный пример кода сопрограммы, которая является функцией-членом класса. Вы можете скопировать и вставить этот пример в указанные файлы нового проекта **консольного приложения для Windows (C++/WinRT)** .

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

**MyClass::RetrieveValueAsync** проработает некоторое время и в конечном итоге вернет копию элемента данных `MyClass::m_value`. Вызов **RetrieveValueAsync** приводит к созданию асинхронного объекта, который содержит неявный указатель *this* (через который в конечном итоге осуществляется доступ к `m_value`).

Учитывайте, что в соподпрограмме выполнение происходит синхронно до первой точки приостановки, где управление возвращается вызывающему объекту. В **RetrieveValueAsync** первое ключевое слово `co_await` — это первая точка приостановки. К тому времени, когда соподпрограмма возобновит работу (в нашем случае примерно через пять секунд), что угодно может повлиять на неявный указатель *this*, обеспечивающий доступ к `m_value`.

Ниже приведена полная последовательность событий.

1. В **main** создается экземпляр **MyClass** (`myclass_instance`).
2. Создается объект `async`, указывающий на `myclass_instance` (через *this*).
3. Функция **Winrt::Windows::Foundation::IAsyncAction::get** достигнет первой точки приостановки, заблокируется на несколько секунд, а затем вернет результат **RetrieveValueAsync**.
4. **RetrieveValueAsync** возвращает значение `this->m_value`.

Шаг 4 безопасен, только если указатель *this* остается допустимым.

Но что, если экземпляр класса уничтожается до завершения асинхронной операции? Существует множество возможностей, с помощью которых экземпляр класса может выходить за пределы области до завершения асинхронного метода. Но можно имитировать это, установив для экземпляра класса значение `nullptr`.

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

После уничтожения экземпляра класса все выглядит так, будто мы не ссылаемся непосредственно на него снова. Но конечно асинхронный объект содержит указатель *this* на него и пытается использовать его, чтобы скопировать значение, хранящееся в экземпляре класса. Сопрограмма является функцией-членом и она ожидает возможность, чтобы свободно использовать свой указатель *this*.

С таким изменением кода мы сталкиваемся с проблемой на шаге 4, так как экземпляр класса уничтожен и *this* больше не является допустимым. Как только асинхронный объект сразу попытается получить доступ к переменной внутри экземпляра класса, произойдет сбой (или случится что-то неопределенное).

Решение заключается в предоставлении асинхронной операции &mdash;сопрограмме &mdash; собственной сильной ссылки на экземпляр класса. В настоящее время запись сопрограммы фактически содержит необработанный указатель *this* на экземпляр класса, но этого недостаточно для сохранения экземпляра класса.

Для сохранения экземпляра класса измените реализацию **RetrieveValueAsync** как показано ниже.

```cppwinrt
IAsyncOperation<winrt::hstring> RetrieveValueAsync()
{
    auto strong_this{ get_strong() }; // Keep *this* alive.
    co_await 5s;
    co_return m_value;
}
```

Класс C++/WinRT прямо или косвенно является производным от шаблона [**winrt::implements**](/uwp/cpp-ref-for-winrt/implements). Поэтому объект C++/WinRT может вызывать свою защищенную функцию-член [**Implements::get_weak**](/uwp/cpp-ref-for-winrt/implements#implementsget_strong-function), чтобы получить сильную ссылку для своего указателя *this*. Обратите внимание, что нет необходимости использовать переменную `strong_this` в приведенном выше примере кода; просто вызов **get_strong** увеличивает количество ссылок объекта C++/WinRT и указатель *this* остается допустимым.

> [!IMPORTANT]
> Так как **get_strong** — это функция-член шаблона структуры **winrt::implements**, вы можете вызвать ее только из класса, который прямо или косвенно является производным от **winrt::implements**, например класс C++/WinRT. Дополнительные сведения о производных от **winrt::implements** и примерах см. в статье [Author APIs with C++/WinRT](/windows/uwp/cpp-and-winrt-apis/author-apis) (Создание API-интерфейсов с помощью C++/WinRT).

Это решает предыдущую проблему на шаге 4. Даже если остальные ссылки на экземпляр класса исчезнут, сопрограмма предпримет меры предосторожности, которые гарантируют, что ее зависимости стабильны.

Если сильная ссылка не подходит, то вместо этого вы можете вызвать [**implements::get_weak**](/uwp/cpp-ref-for-winrt/implements#implementsget_weak-function), чтобы получить слабую ссылку для *this*. Просто убедитесь, что можно получить сильную ссылку перед доступом к *this*. Опять же **get_weak** является функцией-членом шаблона структуры **winrt::implements**.

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

В приведенном выше примере слабая ссылка не предохраняет экземпляр класса от уничтожения, если не осталось сильных ссылок. Но вы сможете проверить, можно ли получить сильную ссылку до обращения к переменной-члену.

## <a name="safely-accessing-the-this-pointer-with-an-event-handling-delegate"></a>Безопасный доступ к указателю *this* с помощью делегата, обрабатывающего события

### <a name="the-scenario"></a>Сценарий

Общие сведения об обработке событий см. в статье [Handle events by using delegates in C++/WinRT](handle-events.md) (Обработка событий с помощью делегатов в C++/WinRT).

В предыдущем разделе выделены потенциальные проблемы времени существования в областях сопрограмм и параллельной обработки. Но если нужно обработать событие с функцией-членом объекта или из лямбда-функции внутри функции-члена объекта, необходимо подумать об относительном времени существования получателя события (объекта, обрабатывающего событие) и источника события (объекта, вызывающего событие). Рассмотрим несколько примеров кода.

Код ниже сначала определяет простой класс **EventSource**, вызывающий общее событие, которое обрабатывается добавленными к нему делегатами. В этом примере событие использует тип делегата [**Windows::Foundation::EventHandler**](/uwp/api/windows.foundation.eventhandler), но проблемы и способы их устранения, рассмотренные здесь, применимы ко всем типам делегатов.

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

Шаблоном является то, что получатель события содержит обработчик событий лямбда-выражений с зависимостями своего указателя *this*. Каждый раз, когда время существования получателя события превышает время существования источника события, оно превышает время существования зависимостей. И в таких распространенных случаях шаблон работает хорошо. Некоторые из таких случаев очевидны, например при обработке страницей пользовательского интерфейса события, вызванного элементом управления, который находится на этой странице. Время существования страницы превышает время существования кнопки.&mdash;Таким образом, обработчик также превышает время существования кнопки. Эта ситуация верна всегда, когда получатель является владельцем источника (например, в качестве элемента данных), или когда получатель и источник являются родственными и напрямую принадлежат другому объекту.

Если вы уверены, что в вашей ситуации время существования обработчика не превышает время существования объекта *this*, от которого он зависит, можно записать *this* обычным образом, без учета времени существования.

Однако бывают случаи, когда время существования объекта *this* не превышает время его использования в обработчике (включая обработчики событий завершения и выполнения, которые создаются асинхронными действиями и операциями), и важно знать как с ними обращаться.

- Когда события поступают из источника *синхронно*, вы можете отозвать свой дескриптор. При этом события больше поступать не будут. Но для асинхронных событий даже после отмены (и особенно после отмены в деструкторе) обрабатываемое событие может достичь объекта после начала уничтожения. Попытка отменить подписку перед уничтожением может решить проблему, но вам нужно найти надежное решение.
- Это возможно, если вы создаете сопрограмму для реализации асинхронного метода.
- В редких случаях с определенными объектами платформы пользовательского интерфейса XAML (например, [**SwapChainPanel**](/uwp/api/windows.ui.xaml.controls.swapchainpanel)) это возможно, если получатель завершается без отмены регистрации в источнике событий.

### <a name="the-issue"></a>Проблема

Следующая версия этой функции **main** имитирует, что случится при уничтожении получателя события (возможно с выходом за пределы области), пока источник событий по-прежнему их вызывает.

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

Получатель события уничтожается, но обработчик событий лямбда-выражений в нем по-прежнему является подписанным на событие **Event**. При возникновении этого события лямбда-выражение пытается разыменовать указатель *this*, который недопустим в этот момент. Таким образом, нарушение прав доступа происходит из-за кода в обработчике (или в продолжении сопрограммы), который пытается его использовать.

> [!IMPORTANT]
> Если вы столкнулись с подобной ситуацией, вам следует подумать о времени существования объекта *this* и о том, превышает ли время существования записанного объекта *this* время существования захвата. Если нет, записывайте его с помощью сильной или слабой ссылки, как будет показано ниже.
>
> Или &mdash;если это целесообразно для вашего сценария и потоковая модель это позволяет &mdash; вы можете отозвать обработчик после того, как получатель завершил работу с событием, либо в деструкторе получателя. Ознакомьтесь с разделом [Отзыв зарегистрированного делегата](handle-events.md#revoke-a-registered-delegate).

Вот как можно зарегистрировать обработчик.

```cppwinrt
event_source.Event([&](auto&& ...)
{
    std::wcout << m_value.c_str() << std::endl;
});
```

Лямбда-выражение автоматически захватывает все локальные переменные по ссылке. Таким образом, в этом примере мы могли бы записать это так.

```cppwinrt
event_source.Event([this](auto&& ...)
{
    std::wcout << m_value.c_str() << std::endl;
});
```

В обоих случаях мы просто захватываем необработанный указатель *this*. И это не оказывает влияния на учет ссылок, поэтому ничто не мешает уничтожить текущий объект.

### <a name="the-solution"></a>Решение

Решение заключается в записи строгой ссылки (или, как мы увидим, слабой ссылки, если это более уместно). Сильная ссылка *увеличивает* количество ссылок и *сохраняет* текущий объект. Вы просто объявляете переменную захвата (в этом примере вызывается `strong_this`) и инициализируете ее с помощью вызова функции-члена [**implements.get_strong**](/uwp/cpp-ref-for-winrt/implements#implementsget_strong-function), которая получает сильную ссылку на наш указатель *this*.

> [!IMPORTANT]
> Так как **get_strong** — это функция-член шаблона структуры **winrt::implements**, вы можете вызвать ее только из класса, который прямо или косвенно является производным от **winrt::implements**, например класс C++/WinRT. Дополнительные сведения о производных от **winrt::implements** и примерах см. в статье [Author APIs with C++/WinRT](/windows/uwp/cpp-and-winrt-apis/author-apis) (Создание API-интерфейсов с помощью C++/WinRT).

```cppwinrt
event_source.Event([this, strong_this { get_strong()}](auto&& ...)
{
    std::wcout << m_value.c_str() << std::endl;
});
```

Вы можете даже опустить автоматическую запись текущего объекта и доступ к элементу данных через запись переменной вместо неявного *this*.

```cppwinrt
event_source.Event([strong_this { get_strong()}](auto&& ...)
{
    std::wcout << strong_this->m_value.c_str() << std::endl;
});
```

Если сильная ссылка не подходит, то вместо этого вы можете вызвать [**implements::get_weak**](/uwp/cpp-ref-for-winrt/implements#implementsget_weak-function), чтобы получить слабую ссылку для *this*. Слабая ссылка *не* сохраняет текущий объект. Просто убедитесь, что вы можете получить сильную ссылку из слабой ссылки перед получением доступа к элементам.

```cppwinrt
event_source.Event([weak_this{ get_weak() }](auto&& ...)
{
    if (auto strong_this{ weak_this.get() })
    {
        std::wcout << strong_this->m_value.c_str() << std::endl;
    }
});
```

При записи необработанного указателя необходимо убедиться в наличии объекта, на который эта ссылка указывает.

### <a name="if-you-use-a-member-function-as-a-delegate"></a>Использование функции-члена в качестве делегата

Как и лямбда-функции, эти принципы также применимы к использованию функции-члена в качестве вашего делегата. Синтаксис отличается, поэтому рассмотрим код. Во-первых, вот потенциально небезопасный обработчик событий функции-члена, использующий необработанный указатель *this*.

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

Это стандартный, обычный способ ссылки на объект и его функцию-члена. Чтобы сделать это безопасно, вы можете &mdash; начиная с версии 10.0.17763.0 Windows SDK (Windows 10, версия 1809) &mdash; установить сильную или слабую ссылку там, где зарегистрирован обработчик. При этом объект получателя событий по-прежнему активен.

Для сильной ссылки просто вызовите [**get_strong**](/uwp/cpp-ref-for-winrt/implements#implementsget_strong-function) в место необработанного указателя *this*. C++/WinRT гарантирует, что результирующий делегат содержит сильную ссылку на текущий объект.

```cppwinrt
event_source.Event({ get_strong(), &EventRecipient::OnEvent });
```

Запись строгой ссылки означает, что объект можно будет уничтожить только после отмены регистрации дескриптора и получения всех невыполненных обратных вызовов. Но это справедливо только на момент возникновения события. Если дескриптор событий является асинхронным, вам нужно предоставить сопрограмме строгую ссылку на экземпляр класса перед первой точкой приостановки (дополнительные сведения и код см. в инструкциях по [безопасному доступу к указателю *this* в сопрограмме членов класса](#safely-accessing-the-this-pointer-in-a-class-member-coroutine) выше). Но это создает циклическую ссылку между источником события и объектом, которую нужно явно разорвать, отозвав событие.

Для слабой ссылки вызовите [**get_weak**](/uwp/cpp-ref-for-winrt/implements#implementsget_weak-function). C++/WinRT гарантирует, что результирующий делегат содержит слабую ссылку. Незаметно в последнюю минуту делегат пытается заменить слабую ссылку на сильную и вызвать функцию-член только при успешном выполнении.

```cppwinrt
event_source.Event({ get_weak(), &EventRecipient::OnEvent });
```

Если делегат *вызывает* функцию-член, C++/WinRT сохранит объект до возврата дескриптора. Но если дескриптор является асинхронным, он возвращается в точках приостановки, поэтому вам нужно предоставить сопрограмме строгую ссылку на экземпляр класса перед первой точкой приостановки. См. подробнее о [безопасном доступе к указателю *this* в сопрограмме членов класса](#safely-accessing-the-this-pointer-in-a-class-member-coroutine) выше.

### <a name="a-weak-reference-example-using-swapchainpanelcompositionscalechanged"></a>Пример слабых ссылок при использовании **SwapChainPanel::CompositionScaleChanged**

В этом примере кода в качестве другой иллюстрации слабой ссылки мы используем событие [**SwapChainPanel::CompositionScaleChanged**](/uwp/api/windows.ui.xaml.controls.swapchainpanel.compositionscalechanged). Код осуществляет регистрацию обработчика событий с помощью лямбда-функции, создающей слабую ссылку на получатель.

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

В предложении захвата лямбда-функции создается временная переменная, представляющая собой слабую ссылку на объект *this*. В теле лямбда-функции, если можно получить строгую ссылку на объект *this*, вызывается функция **OnCompositionScaleChanged**. Таким образом внутри **OnCompositionScaleChanged** можно безопасно использовать объект *this*.

## <a name="weak-references-in-cwinrt"></a>Слабые ссылки в C++/WinRT

Выше мы видели использование слабых ссылок. Как правило они хорошо подходят для разрыва циклических ссылок. Например, когда речь идет о собственной реализации платформы пользовательского интерфейса на основе XAML &mdash; ввиду исторических особенностей проектирования платформы &mdash; механизм слабых ссылок в C++/WinRT необходим для обработки циклических ссылок. За пределами XAML вам, скорее всего, не потребуется использовать слабые ссылки (хотя в них нет ничего, изначально относящегося непосредственно к XAML). Проектирование API-интерфейсов C++/WinRT чаще всего можно произвести таким образом, чтобы избежать необходимости циклических и слабых ссылок. 

Для конкретного объявляемого вами типа C++/WinRT на первый взгляд не очевидно, требуются ли слабые ссылки, и если да, то когда. Поэтому C++/ WinRT автоматически обеспечивает поддержку слабых ссылок в шаблоне структуры [**winrt::implements**](/uwp/cpp-ref-for-winrt/implements), от которого напрямую или косвенно наследуются ваши собственные типы C++/WinRT. Поддержка слабых ссылок не расходует ресурсов до тех пор, пока у вашего объекта не запрашивается [**IWeakReferenceSource**](/windows/desktop/api/weakreference/nn-weakreference-iweakreferencesource). Кроме того, вы можете явным образом [отказаться от этой поддержки](#opting-out-of-weak-reference-support).

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

Создание слабой ссылки не влияет на количество ссылок на сам объект, а просто приводит к выделению блока управления. Этот блок управления отвечает за реализацию семантики слабой ссылки. Затем вы можете попытаться превратить слабую ссылку в сильную и в случае успеха ее использовать.

```cppwinrt
if (Class strong = weak.get())
{
    // use strong, for example strong.DoWork();
}
```

При условии, что существуют другие строгие ссылки, вызов [**weak_ref::get**](/uwp/cpp-ref-for-winrt/weak-ref#weak_refget-function) увеличивает количество ссылок и возвращает сильную ссылку в вызывающий код.

### <a name="opting-out-of-weak-reference-support"></a>Отказ от поддержки слабых ссылок
Поддержка слабых ссылок осуществляется автоматически. Однако вы можете явным образом отказаться от этой поддержки, передав структуру маркера [**winrt::no_weak_ref**](/uwp/cpp-ref-for-winrt/no-weak-ref) в качестве аргумента шаблона базовому классу.

При непосредственном наследовании из **winrt::implements**.

```cppwinrt
struct MyImplementation: implements<MyImplementation, IStringable, no_weak_ref>
{
    ...
}
```

При создании класса среды выполнения:

```cppwinrt
struct MyRuntimeClass: MyRuntimeClassT<MyRuntimeClass, no_weak_ref>
{
    ...
}
```

Не имеет значения, где в пакете параметров располагается структура маркера. При запросе слабой ссылки для типа с отключенной поддержкой компилятор отобразит сообщение *This is only for weak ref support* (Только для поддержки слабых ссылок).

## <a name="important-apis"></a>Важные API
* [Функция implements::get_weak](/uwp/cpp-ref-for-winrt/implements#implementsget_weak-function)
* [Шаблон функции winrt::make_weak](/uwp/cpp-ref-for-winrt/make-weak)
* [Структура маркера winrt::no_weak_ref](/uwp/cpp-ref-for-winrt/no-weak-ref)
* [Шаблон структуры winrt::weak_ref](/uwp/cpp-ref-for-winrt/weak-ref)

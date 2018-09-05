---
author: stevewhims
description: В этом разделе показано, как создать компонент среды выполнения Windows, который содержит класс среды выполнения, создающий события. В нем также показано приложение, которое использует этот компонент и обрабатывает события.
title: Создание событий в C++/WinRT
ms.author: stwhi
ms.date: 07/18/2018
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: Windows 10, UWP, стандартная, c++, cpp, winrt, проекция, создание, событие
ms.localizationpriority: medium
ms.openlocfilehash: 3b52bf8e33bbf111dd02c695d8c3baf77e1338ac
ms.sourcegitcommit: 7aa1933e6970f878faf50d59e1f799b90afd7cc7
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/05/2018
ms.locfileid: "3380144"
---
# <a name="author-events-in-cwinrtwindowsuwpcpp-and-winrt-apisintro-to-using-cpp-with-winrt"></a>Создание событий в [C++/WinRT](/windows/uwp/cpp-and-winrt-apis/intro-to-using-cpp-with-winrt)

В этом разделе показано, как создать компонент среды выполнения Windows, который содержит класс среды выполнения, представляющий банковский счет, который вызывает событие при списании с баланса. В нем также показано базовое приложение, которое использует класс среды выполнения банковского счета, вызывает функцию для изменения баланса и обрабатывает все возникающие события.

> [!NOTE]
> Сведения об установке и использовании расширения C++/WinRT для Visual Studio (VSIX) (которое обеспечивает поддержку шаблона проекта, а также свойств и целевых объектов MSBuild C++/WinRT) см. в разделе [Поддержка Visual Studio для C++/WinRT и VSIX](intro-to-using-cpp-with-winrt.md#visual-studio-support-for-cwinrt-and-the-vsix).

> [!IMPORTANT]
> Основные понятия и термины, которые помогают понять, как использовать и создавать классы среды выполнения с помощью C++/WinRT, см. в разделах [Использование API-интерфейсов в C++/WinRT](consume-apis.md) и [Создание API-интерфейсов в C++/WinRT ](author-apis.md).

## <a name="create-a-windows-runtime-component-bankaccountwrc"></a>Создание компонента среды выполнения Windows (BankAccountWRC)

Начните с создания нового проекта в Microsoft Visual Studio. Создайте проект **Visual C++ компонента среды выполнения Windows (C++/ WinRT)** и назовите его *BankAccountWRC* (компонент среды выполнения Windows банковского счета).

Созданный проект содержит файл с именем `Class.idl`. Переименуйте этот файл `BankAccount.idl` (Переименование `.idl` файл автоматически переименует зависит от `.h` и `.cpp` файлы, слишком). Замените содержимое `BankAccount.idl` с описанием ниже.

```idl
// BankAccountWRC.idl
namespace BankAccountWRC
{
    runtimeclass BankAccount
    {
        BankAccount();
        event Windows.Foundation.EventHandler<Single> AccountIsInDebit;
        void AdjustBalance(Single value);
    };
}
```

Сохраните файл. Проект не будет создан до завершения в данный момент, но создание теперь — это полезно объект, так как он создает файлы исходного кода, в которых реализуем класс среды выполнения **BankAccount** . Таким образом построить сейчас и перейти к (ошибок построения, которую можно собрать см. на этом этапе требуется сделать с помощью `Class.h` и `Class.g.h` не найден). Во время сборки `midl.exe` запускается средство для создания файла метаданных вашего компонента среды выполнения Windows (который является `\BankAccountWRC\Debug\BankAccountWRC\BankAccountWRC.winmd`). Затем запускается средство `cppwinrt.exe` (с параметром `-component`) для создания файлов исходного кода для поддержки создания компонента. Эти файлы включают заглушки для начала реализации класса среды выполнения **BankAccount** , объявленного в вашем IDL. Это заглушки `\BankAccountWRC\BankAccountWRC\Generated Files\sources\BankAccount.h` и `BankAccount.cpp`.

В проводнике, скопируйте файлы заглушек `BankAccount.h` и `BankAccount.cpp` из папки `\BankAccountWRC\BankAccountWRC\Generated Files\sources\` в папку, содержащую файлы проекта, который является `\BankAccountWRC\BankAccountWRC\`и замените файлы в месте назначения. Теперь давайте откроем `BankAccount.h` и `BankAccount.cpp` и реализуем класс среды выполнения. В `BankAccount.h` добавьте два закрытых члена в реализацию (*не* фабричную реализацию) BankAccount.

```cppwinrt
// BankAccount.h
...
namespace winrt::BankAccountWRC::implementation
{
    struct BankAccount : BankAccountT<BankAccount>
    {
        ...

    private:
        winrt::event<Windows::Foundation::EventHandler<float>> m_accountIsInDebitEvent;
        float m_balance{ 0.f };
    };
}
...
```

Как видно выше, событие реализуется терминах шаблона структуры [**winrt::event**](/uwp/cpp-ref-for-winrt/event) , задаются указанного типа делегата.

В `BankAccount.cpp` реализуйте функции, как показано в следующем примере кода. В C++/WinRT объявленное в IDL событие реализуется как набор перегруженных функций (аналогично реализации свойства в виде пары перегруженных функций get и set). Одна перегрузка принимает делегат для регистрации и возвращает маркер. Другая получает маркер и отменяет регистрацию связанного делегата.

```cppwinrt
// BankAccount.cpp
...
namespace winrt::BankAccountWRC::implementation
{
    winrt::event_token BankAccount::AccountIsInDebit(Windows::Foundation::EventHandler<float> const& handler)
    {
        return m_accountIsInDebitEvent.add(handler);
    }

    void BankAccount::AccountIsInDebit(winrt::event_token const& token)
    {
        m_accountIsInDebitEvent.remove(token);
    }

    void BankAccount::AdjustBalance(float value)
    {
        m_balance += value;
        if (m_balance < 0.f) m_accountIsInDebitEvent(*this, m_balance);
    }
}
```

Вам не нужно реализовывать перегрузку для средства отзыва событий (дополнительные сведения см. в разделе [Отзыв зарегистрированного делегата](handle-events.md#revoke-a-registered-delegate))&mdash; это за вас делает проекция C++/WinRT. Другие перегрузки не прописываются в проекцию, чтобы предоставить вам возможность их реализации оптимальным для вашего сценария образом. Вызов функций [**event::add**](/uwp/cpp-ref-for-winrt/event#eventadd-function) и [**event::remove**](/uwp/cpp-ref-for-winrt/event#eventremove-function) таким образом является эффективным и безопасным с точки зрения параллельной обработки и многопотоковой обработки по умолчанию. Но если у вас очень большое количество событий, вам может не требоваться поле для каждого из них, и вы выберете вместо этого какой-либо другой вид выборочной реализации.

Выше также можно увидеть, что реализация функции **AdjustBalance** вызывает событие **AccountIsInDebit**, если баланс становится отрицательным.

Если какие-либо предупреждения не позволяют сборке, затем устранить их, или установите для свойства проекта **C/C++** > **Общие** > **Обрабатывать предупреждения как ошибки** **Нет (/ WX-)** и выполните сборку проекта заново.

## <a name="create-a-core-app-bankaccountcoreapp-to-test-the-windows-runtime-component"></a>Создайте приложение основных компонентов (BankAccountCoreApp) для тестирования компонента среды выполнения Windows.

Теперь создайте новый проект (в вашем решении `BankAccountWRC` или в новом). Создайте проект **приложения основных компонентов Visual C++ (C++/WinRT)** и назовите его *BankAccountCoreApp*.

Добавьте ссылку и перейдите к `\BankAccountWRC\Debug\BankAccountWRC\BankAccountWRC.winmd` (или добавьте ссылку на проект, если два проекта находятся в одном решении). Нажмите кнопку **Добавить**, а затем кнопку **OK**. Теперь выполните сборку BankAccountCoreApp. Маловероятно, что вы видите сообщение, файл полезных данных `readme.txt` не существует, исключите этот файл из проекта компонента среды выполнения Windows, заново выполните сборку, а затем снова выполните сборку BankAccountCoreApp.

Во время сборки запускается средство `cppwinrt.exe` для обработки указанного файла `.winmd` в файлы исходного кода, содержащие проецируемые типы для поддержки использования вашего компонента. Заголовок проецируемых типов для классов среды выполнения вашего компонента &mdash; с именем `BankAccountWRC.h` &mdash; создается в папке `\BankAccountCoreApp\BankAccountCoreApp\Generated Files\winrt\`.

Включите этот заголовок в `App.cpp`.

```cppwinrt
#include <winrt/BankAccountWRC.h>
```

Кроме того, добавьте в `App.cpp` следующий код для создания экземпляра BankAccount (с помощью конструктора по умолчанию проецируемого типа), зарегистрируйте обработчик событий, а затем произведите списание со счета.

```cppwinrt
struct App : implements<App, IFrameworkViewSource, IFrameworkView>
{
    BankAccountWRC::BankAccount m_bankAccount;
    winrt::event_token m_eventToken;
    ...
    
    void Initialize(CoreApplicationView const &)
    {
        m_eventToken = m_bankAccount.AccountIsInDebit([](const auto &, float balance)
        {
            WINRT_ASSERT(balance < 0.f);
        });
    }
    ...

    void Uninitialize()
    {
        m_bankAccount.AccountIsInDebit(m_eventToken);
    }
    ...

    void OnPointerPressed(IInspectable const &, PointerEventArgs const & args)
    {
        m_bankAccount.AdjustBalance(-1.f);
        ...
    }
    ...
};
```

Каждый раз при щелчке по окну вы вычитаете 1 с баланса банковского счета. Чтобы продемонстрировать, что событие вызывается должным образом, поместите точку останова внутри лямбда-выражение, которое обрабатывает событие **AccountIsInDebit** , запустите приложение и щелкните внутри окна.

## <a name="parameterized-delegates-and-simple-signals-across-an-abi"></a>Параметризованному делегаты и простого сигналы, через ABI

Если событие должен быть доступен через двоичного интерфейса приложений (ABI)&mdash;например между компонента и его использующем приложения&mdash;событий должны использовать тип делегата среды выполнения Windows. В приведенном выше примере используется тип делегата [**Windows::Foundation::EventHandler\ < T\ >**](/uwp/api/windows.foundation.eventhandler) среды выполнения Windows. [**TypedEventHandler\ < TSender, TResult\ >**](/uwp/api/windows.foundation.eventhandler) — еще один пример типа делегата среды выполнения Windows.

Параметры типа для этих типов два делегата имеют перекрестной ABI, поэтому параметры типа должны являться типами среды выполнения Windows, слишком. Включает в себя классы среды выполнения первого и сторонних поставщиков, а также как простые типы, такие как строки и числа. Компилятор поможет вам с ошибкой «*должны иметь тип WinRT*» Если вы забудете такое ограничение.

Если вам не нужно передать любые параметры или аргументов события, можно определить свой собственный простой тип делегата среды выполнения Windows. В примере ниже показано более простой версии **BankAccount** класса среды выполнения. Он объявляет тип делегата с именем **SignalDelegate** , а затем он использует, создать событие тип сигнала вместо события с параметром.

```idl
// BankAccountWRC.idl
namespace BankAccountWRC
{
    delegate void SignalDelegate();

    runtimeclass BankAccount
    {
        BankAccount();
        event BankAccountWRC.SignalDelegate SignalAccountIsInDebit;
        void AdjustBalance(Single value);
    };
}
```

```cppwinrt
// BankAccount.h
...
namespace winrt::BankAccountWRC::implementation
{
    struct BankAccount : BankAccountT<BankAccount>
    {
        ...

        winrt::event_token SignalAccountIsInDebit(BankAccountWRC::SignalDelegate const& handler);
        void SignalAccountIsInDebit(winrt::event_token const& token);
        void AdjustBalance(float value);

    private:
        winrt::event<BankAccountWRC::SignalDelegate> m_signal;
        float m_balance{ 0.f };
    };
}
```

```cppwinrt
// BankAccount.cpp
...
namespace winrt::BankAccountWRC::implementation
{
    winrt::event_token BankAccount::SignalAccountIsInDebit(BankAccountWRC::SignalDelegate const& handler)
    {
        return m_signal.add(handler);
    }

    void BankAccount::SignalAccountIsInDebit(winrt::event_token const& token)
    {
        m_signal.remove(token);
    }

    void BankAccount::AdjustBalance(float value)
    {
        m_balance += value;
        if (m_balance < 0.f)
        {
            m_signal();
        }
    }
}
```

```cppwinrt
// App.cpp
struct App : implements<App, IFrameworkViewSource, IFrameworkView>
{
    BankAccountWRC::BankAccount m_bankAccount;
    winrt::event_token m_eventToken;
    ...
    
    void Initialize(CoreApplicationView const &)
    {
        m_eventToken = m_bankAccount.SignalAccountIsInDebit([] { /* ... */ });
    }
    ...

    void Uninitialize()
    {
        m_bankAccount.SignalAccountIsInDebit(m_eventToken);
    }
    ...

    void OnPointerPressed(IInspectable const &, PointerEventArgs const & args)
    {
        m_bankAccount.AdjustBalance(-1.f);
        ...
    }
    ...
};
```

## <a name="parameterized-delegates-simple-signals-and-callbacks-within-a-project"></a>Параметризованному делегаты, простой сигналов и обратные вызовы в рамках проекта

Если используется только для внутреннего использования событий в C + +/ WinRT проекта (а не в разных двоичных файлах), а затем по-прежнему использовать шаблон структуры [**winrt::event**](/uwp/cpp-ref-for-winrt/event) , но его параметризации с помощью C + +/ WinRT Windows Runtime [**winrt::delegate&lt;... T&gt; **](/uwp/cpp-ref-for-winrt/delegate) структура шаблона, который является эффективным, с подсчетом ссылок делегата. Он поддерживает любое число параметров, и они не ограничиваются типы среды выполнения Windows.

Во-первых, в примере ниже показано делегата подписи, которую не принимает никаких параметров (по сути сигнал простой), а затем выберите один, принимающий строку.

```cppwinrt
winrt::event<winrt::delegate<>> signal;
signal.add([] { std::wcout << L"Hello, "; });
signal.add([] { std::wcout << L"World!" << std::endl; });
signal();

winrt::event<winrt::delegate<std::wstring>> log;
log.add([](std::wstring const& message) { std::wcout << message.c_str() << std::endl; });
log.add([](std::wstring const& message) { Persist(message); });
log(L"Hello, World!");
```

Обратите внимание, как добавить в событие столько мире делегаты по своему усмотрению. Тем не менее есть некоторые издержки, связанные с событием. Если все что вам нужно — это простой обратного вызова с помощью только одной подписки делегата, то вы можете использовать [**winrt::delegate&lt;... T&gt; **](/uwp/cpp-ref-for-winrt/delegate) самостоятельно.

```cppwinrt
winrt::delegate<> signalCallback;
signalCallback = [] { std::wcout << L"Hello, World!" << std::endl; };
signalCallback();

winrt::delegate<std::wstring> logCallback;
logCallback = [](std::wstring const& message) { std::wcout << message.c_str() << std::endl; }f;
logCallback(L"Hello, World!");
```

При переносе из C + +/ CX codebase где события и делегаты используются для внутренних целей внутри проекта, а затем **winrt::delegate** поможет вам выполнить репликацию этой модели в C + +/ WinRT.

## <a name="design-guidelines"></a>Рекомендации по проектированию

Мы рекомендуем пройти события и делегаты, в качестве параметров функции. **Добавление** функция [**winrt::event**](/uwp/cpp-ref-for-winrt/event) — одно исключение, поскольку в этом случае необходимо передать делегата. Причина для этой рекомендации том, что делегаты могут принимать различные формы среди других языков среды выполнения Windows (точки зрения ли они поддерживают регистрации клиента на один или несколько). События, с помощью их несколько подписчиков модель, составляют гораздо более прогнозируемым и согласованным параметр.

Подпись для делегата обработчика событий должна состоять из двух параметров: *отправителя* (**IInspectable**) и *args* (некоторые тип аргумента события, например [**RoutedEventArgs**](/uwp/api/windows.ui.xaml.routedeventargs)).

Обратите внимание, что эти правила не применяются обязательно, если вы разрабатываете внутренним API. Хотя внутренним API часто станет общедоступным со временем.

## <a name="related-topics"></a>Статьи по теме
* [Создание API-интерфейсов в C++/WinRT](author-apis.md)
* [Использование API-интерфейсов в C++/WinRT](consume-apis.md)
* [Обработка событий с помощью делегатов в C++/WinRT](handle-events.md)

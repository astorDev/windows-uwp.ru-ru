---
description: В этом разделе показано, как создать компонент среды выполнения Windows, который содержит класс среды выполнения, создающий события. В нем также показано приложение, которое использует этот компонент и обрабатывает события.
title: Создание событий в C++/WinRT
ms.date: 04/23/2019
ms.topic: article
keywords: windows 10, uwp, standard, c++, cpp, winrt, projection, news, what's, new
ms.localizationpriority: medium
ms.openlocfilehash: 4499b2191734c6ae66131ce92aa2654188313d5e
ms.sourcegitcommit: d37a543cfd7b449116320ccfee46a95ece4c1887
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/16/2019
ms.locfileid: "68270176"
---
# <a name="author-events-in-cwinrt"></a>Создание событий в C++/WinRT

В этом разделе показано, как создать компонент среды выполнения Windows, который содержит класс среды выполнения, представляющий банковский счет, который вызывает событие при списании с баланса. В нем также показано базовое приложение, которое использует класс среды выполнения банковского счета, вызывает функцию для изменения баланса и обрабатывает все возникающие события.

> [!NOTE]
> Сведения об установке и использовании расширения [C++/WinRT](/windows/uwp/cpp-and-winrt-apis/intro-to-using-cpp-with-winrt) для Visual Studio (VSIX) и пакета NuGet (которые вместе обеспечивают поддержку шаблона проекта и сборки) см. в разделе о [поддержке C++/WinRT в Visual Studio](intro-to-using-cpp-with-winrt.md#visual-studio-support-for-cwinrt-xaml-the-vsix-extension-and-the-nuget-package).

> [!IMPORTANT]
> Основные понятия и термины, которые помогают понять, как использовать и создавать классы среды выполнения с помощью C++/WinRT, см. в разделах [Использование API-интерфейсов в C++/WinRT](consume-apis.md)и [Создание API-интерфейсов в C++/WinRT](author-apis.md).

## <a name="create-a-windows-runtime-component-bankaccountwrc"></a>Создание компонента среды выполнения Windows (BankAccountWRC)

Начните с создания проекта в Microsoft Visual Studio. Создайте проект **компонента среды выполнения Windows (C++/WinRT)** и назовите его *BankAccountWRC* (компонент среды выполнения Windows банковского счета). На данный момент проект создавать не нужно.

Созданный проект содержит файл с именем `Class.idl`. Переименуйте этот файл `BankAccount.idl` (при переименовании `.idl`файл также автоматически переименовывает файл зависимой `.h` и `.cpp`). Замените содержимое `BankAccount.idl` на перечисленное ниже.

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

Сохраните файл. В настоящий момент сборка проекта не будет завершена, но она сейчас полезна, поскольку генерирует файлы с исходным кодом, в которых вы реализуете класс среды выполнения **BankAccount**. Так что действуйте и выполняйте сборку сейчас (ошибки сборки, которые можно ожидать на этом этапе, связанны с тем, что не удалось найти `Class.h` и `Class.g.h`).

Во время сборки запускается инструмент `midl.exe` для создания файла метаданных компонента среды выполнения Windows (`\BankAccountWRC\Debug\BankAccountWRC\BankAccountWRC.winmd`). Затем запускается средство `cppwinrt.exe` (с параметром `-component`) для создания файлов исходного кода для поддержки создания компонента. Эти файлы включают заглушки для начала реализации класса среды выполнения **BankAccount**, объявленного в вашем IDL. Это заглушки `\BankAccountWRC\BankAccountWRC\Generated Files\sources\BankAccount.h` и `BankAccount.cpp`.

Щелкните правой кнопкой мыши узел проекта и нажмите кнопку **Открыть папку в проводнике**. После этого в проводнике откроется папка проекта. Там скопируйте файлы заглушки `BankAccount.h` и `BankAccount.cpp` из папки `\BankAccountWRC\BankAccountWRC\Generated Files\sources\` в папку, содержащую файлы проекта (`\BankAccountWRC\BankAccountWRC\`), и замените файлы в месте назначения. Теперь давайте откроем `BankAccount.h` и `BankAccount.cpp` и реализуем класс среды выполнения. В `BankAccount.h` добавьте два закрытых члена в реализацию (*не* фабричную реализацию) BankAccount.

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

Как показано выше, событие реализуется на основе шаблона структуры [**winrt::event**](/uwp/cpp-ref-for-winrt/event), параметризованном по указанному типу делегата.

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

    void BankAccount::AccountIsInDebit(winrt::event_token const& token) noexcept
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

> [!NOTE]
> Подробные сведения о том, что собой представляет средство автоматической отмены событий, см. в разделе об [отмене зарегистрированного делегата](handle-events.md#revoke-a-registered-delegate). Вам бесплатно предоставляется реализация средства автоматической отмены для вашего события. Другими словами, не нужно реализовать перегрузку для средства отмены событий,&mdash;предоставляемого в проекции C++/WinRT.

Другие перегрузки (перегрузки регистрации и отзыва вручную) *не* помещаются в проекцию. Благодаря этому вы можете оптимально реализовать их в соответствии со своим сценарием. Вызов функций [**event::add**](/uwp/cpp-ref-for-winrt/event#eventadd-function) и [**event::remove**](/uwp/cpp-ref-for-winrt/event#eventremove-function) таким образом, как в этих реализациях, эффективен и безопасен с точки зрения параллельной обработки и многопоточной обработки по умолчанию. Но если у вас очень большое количество событий, вам может не требоваться поле для каждого из них, и вы выберете вместо этого какой-либо другой вид выборочной реализации.

Выше также можно увидеть, что реализация функции **AdjustBalance** вызывает событие **AccountIsInDebit**, если баланс становится отрицательным.

Если какие-либо предупреждения не позволяют выполнить сборку, тогда устраните их или установите для свойства проекта **C/C++**  > **Общие** > **Обрабатывать предупреждения как ошибки** значение **Нет (/WX-)** и выполните сборку проекта заново.

## <a name="create-a-core-app-bankaccountcoreapp-to-test-the-windows-runtime-component"></a>Создайте приложение основных компонентов (BankAccountCoreApp) для тестирования компонента среды выполнения Windows.

Теперь создайте новый проект (в вашем решении `BankAccountWRC` или в новом). Создайте проект **приложения основных компонентов (C++/WinRT)** и назовите его *BankAccountCoreApp*.

Добавьте ссылку и перейдите к `\BankAccountWRC\Debug\BankAccountWRC\BankAccountWRC.winmd` (или добавьте межпроектную ссылку, если два проекта находятся в одном решении). Нажмите кнопку **Добавить**, а затем кнопку **OK**. Теперь выполните сборку BankAccountCoreApp. В случае возникновения проблем, а именно сообщения о том, что файл полезных данных `readme.txt` не существует, исключите этот файл из проекта компонента среды выполнения Windows, заново выполните сборку проекта, а затем снова выполните сборку BankAccountCoreApp.

Во время сборки запускается средство `cppwinrt.exe` для обработки указанного файла `.winmd` в файлы исходного кода, содержащие проецируемые типы для поддержки использования вашего компонента. Заголовок проецируемых типов для классов среды выполнения вашего компонента &mdash; с именем `BankAccountWRC.h`&mdash; создается в папке `\BankAccountCoreApp\BankAccountCoreApp\Generated Files\winrt\`.

Включите этот заголовок в `App.cpp`.

```cppwinrt
#include <winrt/BankAccountWRC.h>
```

Кроме того, добавьте в `App.cpp` следующий код для создания экземпляра BankAccount (с помощью конструктора по умолчанию проецируемого типа), зарегистрируйте обработчик событий, а затем произведите списание со счета.

`WINRT_ASSERT` — это макроопределение, которое передается в [_ASSERTE](/cpp/c-runtime-library/reference/assert-asserte-assert-expr-macros).

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
            WINRT_ASSERT(balance < 0.f); // Put a breakpoint here.
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

Каждый раз при щелчке по окну вы вычитаете 1 с баланса банковского счета. Чтобы продемонстрировать, что событие вызывается должным образом, поместите точку останова внутри лямбда-выражения, которое обрабатывает событие **AccountIsInDebit**, запустите приложение и щелкните внутри окна.

## <a name="parameterized-delegates-and-simple-signals-across-an-abi"></a>Параметризованные делегаты и простые сигналы в ABI

Если ваше событие должно быть доступно через двоичный интерфейс приложения (ABI)&mdash;, например, между компонентом и его потребляющим приложением&mdash;, то ваше событие должно использовать тип делегата среды выполнения Windows. В примере выше используется делегат среды выполнения Windows [**Windows::Foundation::EventHandler\<T\>** ](/uwp/api/windows.foundation.eventhandler). [**TypedEventHandler\<TSender, TResult\>** ](/uwp/api/windows.foundation.eventhandler) является еще одним примером типа делегата среды выполнения Windows.

Параметры типа для этих двух типов делегатов должны пересекать ABI, поэтому параметры типа также должны быть типами среды выполнения Windows. Это включает в себя первичные и сторонние классы среды выполнения, а также примитивные типы, такие как числа и строки. Компилятор отобразит ошибку *must be WinRT type*, если вы забудете об этом ограничении.

Если нет необходимости в передаче каких-либо параметров или аргументов определенному событию, вы можете определить свой собственный простой тип делегата среды выполнения Windows. В приведенном ниже примере показана более простая версия класса среды выполнения **BankAccount**. Он объявляет тип делегата с именем **SignalDelegate**, а затем использует его, чтобы вызвать событие типа сигнала, вместо события с параметром.

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

## <a name="parameterized-delegates-simple-signals-and-callbacks-within-a-project"></a>Параметризованные делегаты, простые сигналы и обратные вызовы в проекте

Если событие используется только внутри вашего проекта C++/WinRT (не в двоичных файлах), вы все равно используете шаблон структуры [ **winrt::event** ](/uwp/cpp-ref-for-winrt/event), но параметризуете его с помощью среды выполнения для C++/WinRT's, отличной от среды выполнения Windows [**winrt::delegate&lt;... T&gt;** ](/uwp/cpp-ref-for-winrt/delegate) — шаблон структуры, который является эффективным делегатом с учетом ссылок. Он поддерживает любое количество параметров, и они не ограничены типами среды выполнения Windows.

В приведенном ниже примере сначала показана подпись делегата, которая не принимает никакие параметры (по сути, простой сигнал), а затем подпись, которая принимает строку.

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

Обратите внимание на то, как к событию можно добавить желаемое количество делегатов с подписью. Однако с событием связаны некоторые издержки. Если все, что нужно — это простой обратный вызов с одним делегатом с подписью, вы можете использовать собственный [**winrt::delegate&lt;... T&gt;** ](/uwp/cpp-ref-for-winrt/delegate).

```cppwinrt
winrt::delegate<> signalCallback;
signalCallback = [] { std::wcout << L"Hello, World!" << std::endl; };
signalCallback();

winrt::delegate<std::wstring> logCallback;
logCallback = [](std::wstring const& message) { std::wcout << message.c_str() << std::endl; }f;
logCallback(L"Hello, World!");
```

При переносе из базы кода C++/CX, где события и делегаты используются для внутренних целей проекта, **winrt::delegate**поможет скопировать этот шаблон в C++/WinRT.

## <a name="design-guidelines"></a>Рекомендации по проектированию

Мы рекомендуем передавать в качестве параметров функции события, а не делегаты. Функция **Добавить** для [**winrt::event**](/uwp/cpp-ref-for-winrt/event) является исключением, так как в этом случае необходимо выполнить передачу делегата. Причина этого правила заключается в том, что делегаты могут принимать разные формы на разных языках среды выполнения Windows (в зависимости от того, поддерживают они одну или несколько регистраций клиента). События с их моделью нескольких подписчиков представляют собой гораздо более предсказуемый и последовательный вариант.

Подпись делегата обработчика события должен состоять из двух параметров: *отправителя* (**IInspectable**), и *args* (некоторый тип аргумента события, например [**RoutedEventArgs**](/uwp/api/windows.ui.xaml.routedeventargs)).

Обратите внимание, что при проектировании внутреннего API, эти рекомендации не обязательно применять. Хотя внутренние API со временем часто становятся общедоступными.

## <a name="related-topics"></a>Статьи по теме
* [Создание интерфейсов API с помощью C++/WinRT](author-apis.md)
* [Использование интерфейсов API с помощью C++/WinRT](consume-apis.md)
* [Обработка событий с помощью делегатов в C++/WinRT](handle-events.md)

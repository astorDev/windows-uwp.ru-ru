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
ms.sourcegitcommit: f2f4820dd2026f1b47a2b1bf2bc89d7220a79c1a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/22/2018
ms.locfileid: "2789644"
---
# <a name="author-events-in-cwinrtwindowsuwpcpp-and-winrt-apisintro-to-using-cpp-with-winrt"></a>Создание событий в [C++/WinRT](/windows/uwp/cpp-and-winrt-apis/intro-to-using-cpp-with-winrt)

В этом разделе показано, как создать компонент среды выполнения Windows, который содержит класс среды выполнения, представляющий банковский счет, который вызывает событие при списании с баланса. В нем также показано базовое приложение, которое использует класс среды выполнения банковского счета, вызывает функцию для изменения баланса и обрабатывает все возникающие события.

> [!NOTE]
> Сведения об установке и использовании расширения C++/WinRT для Visual Studio (VSIX) (которое обеспечивает поддержку шаблона проекта, а также свойств и целевых объектов MSBuild C++/WinRT) см. в разделе [Поддержка Visual Studio для C++/WinRT и VSIX](intro-to-using-cpp-with-winrt.md#visual-studio-support-for-cwinrt-and-the-vsix).

> [!IMPORTANT]
> Основные понятия и термины, которые помогают понять, как использовать и создавать классы среды выполнения с помощью C++/WinRT, см. в разделах [Использование API-интерфейсов в C++/WinRT](consume-apis.md) и [Создание API-интерфейсов в C++/WinRT ](author-apis.md).

## <a name="create-a-windows-runtime-component-bankaccountwrc"></a>Создание компонента среды выполнения Windows (BankAccountWRC)

Начните с создания нового проекта в Microsoft Visual Studio. Создайте проект **Visual C++ компонента среды выполнения Windows (C++/ WinRT)** и назовите его *BankAccountWRC* (компонент среды выполнения Windows банковского счета).

Созданный проект содержит файл с именем `Class.idl`. Переименуйте `BankAccount.idl` (Переименование `.idl` файл автоматически переименовывает зависит от `.h` и `.cpp` файлы слишком). Замените содержимое `BankAccount.idl` с перечнем ниже.

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

Сохраните файл. Не выполняется построение проекта до завершения в данный момент, но теперь построение является полезной операцией стоит делать, поскольку он создает файлы исходного кода, в которых реализуется класс **BankAccount** среды выполнения. Поэтому отключать построения теперь (ошибки построения, можно ожидать на этом этапе необходимо сделать с помощью `Class.h` и `Class.g.h` не найден). Во время построения `midl.exe` запускается средство для создания файла метаданных компонента среды выполнения Windows (которая является `\BankAccountWRC\Debug\BankAccountWRC\BankAccountWRC.winmd`). Затем запускается средство `cppwinrt.exe` (с параметром `-component`) для создания файлов исходного кода для поддержки создания компонента. Эти файлы содержат заглушки для начала реализации класса среды выполнения **BankAccount** , указанному в вашей IDL. Это заглушки `\BankAccountWRC\BankAccountWRC\Generated Files\sources\BankAccount.h` и `BankAccount.cpp`.

В обозревателе файлов скопируйте файлы заглушка `BankAccount.h` и `BankAccount.cpp` из папки `\BankAccountWRC\BankAccountWRC\Generated Files\sources\` в папке, содержащей файлы проекта, которая является `\BankAccountWRC\BankAccountWRC\`и замените файлы в месте назначения. Теперь давайте откроем `BankAccount.h` и `BankAccount.cpp` и реализуем класс среды выполнения. В `BankAccount.h` добавьте два закрытых члена в реализацию (*не* фабричную реализацию) BankAccount.

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

Как вы видите выше, событие реализован в виде шаблона структуры [**winrt::event**](/uwp/cpp-ref-for-winrt/event) , параметризованный по типу определенного делегата.

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

Если все предупреждения останавливает построение, устраните их или установите для свойства проекта **C/C++** > **Общие** > **Обрабатывать предупреждения как ошибки** **No (/ WX-)** и снова выполните построение проекта.

## <a name="create-a-core-app-bankaccountcoreapp-to-test-the-windows-runtime-component"></a>Создайте приложение основных компонентов (BankAccountCoreApp) для тестирования компонента среды выполнения Windows.

Теперь создайте новый проект (в вашем решении `BankAccountWRC` или в новом). Создайте проект **приложения основных компонентов Visual C++ (C++/WinRT)** и назовите его *BankAccountCoreApp*.

Добавьте ссылку и перейдите к `\BankAccountWRC\Debug\BankAccountWRC\BankAccountWRC.winmd` (или Добавление ссылки проекта для проекта, если в одном решении два проекта). Нажмите кнопку **Добавить**, а затем кнопку **OK**. Теперь выполните сборку BankAccountCoreApp. Если вдруг, что появляется сообщение об ошибке, файл полезных данных `readme.txt` не существует, удалите этот файл из проекта компонента среды выполнения Windows, перестроить его, а затем перестроить BankAccountCoreApp.

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

Каждый раз при щелчке по окну вы вычитаете 1 с баланса банковского счета. Для демонстрации, как ожидалось вызывается событие, разместите точку останова в лямбда-выражения, который обрабатывает событие **AccountIsInDebit** , запустите приложение и щелкните внутри окна.

## <a name="parameterized-delegates-and-simple-signals-across-an-abi"></a>Параметризованный делегатов и простой сигналов через ABI

Если событие должен быть доступен через двоичный интерфейс приложений (ABI)&mdash;например между компонента и его использования приложения&mdash;события необходимо использовать среды выполнения Windows делегатами. В приведенном выше примере используется тип среды выполнения Windows [**Windows::Foundation::EventHandler\ < T\ >**](/uwp/api/windows.foundation.eventhandler) делегата. [**TypedEventHandler\ < TSender, TResult\ >**](/uwp/api/windows.foundation.eventhandler) — это еще один пример среды выполнения Windows делегатами.

Параметры типа для этих типов двух делегатов имеют преодолевают ABI, поэтому параметры типов должны быть слишком типы среды выполнения Windows. Который включает в себя классы среды выполнения первоначального и сторонних производителей, а также простые типы, такие как числа и строки. Компилятора помогает «*должен иметь тип WinRT*» ошибка, если вы забыли ограничения.

Если вам не нужно передавать любые параметры или аргументы с события, можно определить свои собственные простой среды выполнения Windows делегатами. В приведенном ниже примере показана простая версия класса **BankAccount** среды выполнения. Объявляет тип делегата с именем **SignalDelegate** и затем, который используется для создания события тип сигнала вместо события с параметром.

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

## <a name="parameterized-delegates-simple-signals-and-callbacks-within-a-project"></a>Параметризованный делегаты, простой сигналы и обратных вызовов в рамках проекта

Если события используется только внутренним образом в рамках C + +/ WinRT проекта (не в двоичные файлы), а затем по-прежнему использовать шаблон структуры [**winrt::event**](/uwp/cpp-ref-for-winrt/event) , но его параметризации с C + +/ WinRT's Windows среда [**winrt::delegate&lt;... T&gt; **](/uwp/cpp-ref-for-winrt/delegate) структура шаблона, который является эффективным, учет ссылок делегатом. Он поддерживает любое число параметров, и они не ограничиваются типы среды выполнения Windows.

В приведенном ниже примере сначала показано делегата подписи, который не принимает никаких параметров (по сути простой сигнал), а затем выберите один, который принимает строку.

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

Обратите внимание на то, как можно добавить к событию столько подписки делегатов по желанию. Тем не менее имеется определенная дополнительная нагрузка, связанный с событием. Если требуется простой обратного вызова с одной подписки делегата, то можно использовать [**winrt::delegate&lt;... T&gt; **](/uwp/cpp-ref-for-winrt/delegate) самостоятельно.

```cppwinrt
winrt::delegate<> signalCallback;
signalCallback = [] { std::wcout << L"Hello, World!" << std::endl; };
signalCallback();

winrt::delegate<std::wstring> logCallback;
logCallback = [](std::wstring const& message) { std::wcout << message.c_str() << std::endl; }f;
logCallback(L"Hello, World!");
```

Если перенос из C + +/ базы кода CX где событий и делегатов, используемых во внутренней сети в рамках проекта, а затем **winrt::delegate** поможет вам выполнить репликацию этого шаблона в C + +/ WinRT.

## <a name="design-guidelines"></a>Рекомендации по проектированию

Мы рекомендуем передачи событий и делегатов, как параметры функции. **Добавление** функции [**winrt::event**](/uwp/cpp-ref-for-winrt/event) является одно исключение, поскольку в этом случае необходимо передать делегат. Причина этой рекомендации, так как делегаты могут иметь разные формы на различных языках среды выполнения Windows (с точки зрения ли они поддерживают регистрации клиента один или несколько). События с их нескольких модель подписчика составляют намного более прогнозируемый и согласованные параметр.

Подпись для делегата обработчика событий должна состоять из двух параметров: *отправителя* (**IInspectable**) и *args* (некоторые тип аргумента события, например [**RoutedEventArgs**](/uwp/api/windows.ui.xaml.routedeventargs)).

Обратите внимание, что эти руководства неприменимы обязательно при создании внутренних API. Хотя внутренними интерфейсами API часто становятся общедоступный по времени.

## <a name="related-topics"></a>Статьи по теме
* [Создание API-интерфейсов в C++/WinRT](author-apis.md)
* [Использование API-интерфейсов в C++/WinRT](consume-apis.md)
* [Обработка событий с помощью делегатов в C++/WinRT](handle-events.md)

---
description: В этом разделе показано, как создать компонент среды выполнения Windows, который содержит класс среды выполнения, создающий события. В нем также показано приложение, которое использует этот компонент и обрабатывает события.
title: Создание событий в C++/WinRT
ms.date: 07/18/2018
ms.topic: article
keywords: Windows 10, UWP, стандартная, c++, cpp, winrt, проекция, создание, событие
ms.localizationpriority: medium
ms.openlocfilehash: 5c410d209972a0221928548901f79bd599c67eae
ms.sourcegitcommit: c315ec3e17489aeee19f5095ec4af613ad2837e1
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/04/2019
ms.locfileid: "58921700"
---
# <a name="author-events-in-cwinrt"></a>Создание событий в C++/WinRT

В этом разделе показано, как создать компонент среды выполнения Windows, который содержит класс среды выполнения, представляющий банковский счет, который вызывает событие при списании с баланса. В нем также показано базовое приложение, которое использует класс среды выполнения банковского счета, вызывает функцию для изменения баланса и обрабатывает все возникающие события.

> [!NOTE]
> Сведения об установке и использовании [ C++/WinRT](/windows/uwp/cpp-and-winrt-apis/intro-to-using-cpp-with-winrt) Visual Studio Extension (VSIX) и пакет NuGet (которые вместе обеспечивают шаблон проекта и поддержка сборки), см. в разделе [поддержка Visual Studio C++/ WinRT](intro-to-using-cpp-with-winrt.md#visual-studio-support-for-cwinrt-xaml-the-vsix-extension-and-the-nuget-package).

> [!IMPORTANT]
> Основные понятия и термины, которые помогают понять, как использовать и создавать классы среды выполнения с помощью C++/WinRT, см. в разделах [Использование API-интерфейсов в C++/WinRT](consume-apis.md) и [Создание API-интерфейсов в C++/WinRT ](author-apis.md).

## <a name="create-a-windows-runtime-component-bankaccountwrc"></a>Создание компонента среды выполнения Windows (BankAccountWRC)

Начните с создания нового проекта в Microsoft Visual Studio. Создание **Visual C++**   >  **универсальной Windows** > **компонента среды выполнения Windows (C++/WinRT)** проект и назовите его  *BankAccountWRC* (для «банк компонента среды выполнения Windows»).

Созданный проект содержит файл с именем `Class.idl`. Переименуйте этот файл `BankAccount.idl` (Переименование `.idl` файл автоматически переименовывает зависимой `.h` и `.cpp` файлы слишком). Замените содержимое файла `BankAccount.idl` со списком ниже.

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

Сохраните файл. Сборка проекта не до конца на данный момент, но создание теперь является полезным, что необходимо сделать, так как он создает файлы исходного кода, в которых вы реализуете **BankAccount** класса среды выполнения. Так что действуйте и построить сейчас (ошибки сборки, можно ожидать на этом этапе нужно сделать с помощью `Class.h` и `Class.g.h` не найден). Во время сборки `midl.exe` запускается средство для создания файла метаданных компонента среды выполнения Windows (который является `\BankAccountWRC\Debug\BankAccountWRC\BankAccountWRC.winmd`). Затем запускается средство `cppwinrt.exe` (с параметром `-component`) для создания файлов исходного кода для поддержки создания компонента. Эти файлы включают заглушки для начала работы реализации **BankAccount** класса среды выполнения, объявленного в вашей IDL. Это заглушки `\BankAccountWRC\BankAccountWRC\Generated Files\sources\BankAccount.h` и `BankAccount.cpp`.

Щелкните правой кнопкой мыши узел проекта и нажмите кнопку **открыть папку в проводнике**. Это открывает папку проекта в проводнике. Существует, скопируйте файлы заглушки `BankAccount.h` и `BankAccount.cpp` из папки `\BankAccountWRC\BankAccountWRC\Generated Files\sources\` и в папку, содержащую файлы проекта, который является `\BankAccountWRC\BankAccountWRC\`и заменить файлы в месте назначения. Теперь давайте откроем `BankAccount.h` и `BankAccount.cpp` и реализуем класс среды выполнения. В `BankAccount.h` добавьте два закрытых члена в реализацию (*не* фабричную реализацию) BankAccount.

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

Как показано выше, событие реализуется на основе [ **winrt::event** ](/uwp/cpp-ref-for-winrt/event) шаблон структуры, параметризованное по указанного типа делегата.

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

Вам не нужно реализовывать перегрузку для средства отзыва событий (дополнительные сведения см. в разделе [Отзыв зарегистрированного делегата](handle-events.md#revoke-a-registered-delegate)) &mdash; это за вас делает проекция C++/WinRT. Другие перегрузки не прописываются в проекцию, чтобы предоставить вам возможность их реализации оптимальным для вашего сценария образом. Вызов функций [**event::add**](/uwp/cpp-ref-for-winrt/event#eventadd-function) и [**event::remove**](/uwp/cpp-ref-for-winrt/event#eventremove-function) таким образом является эффективным и безопасным с точки зрения параллельной обработки и многопотоковой обработки по умолчанию. Но если у вас очень большое количество событий, вам может не требоваться поле для каждого из них, и вы выберете вместо этого какой-либо другой вид выборочной реализации.

Выше также можно увидеть, что реализация функции **AdjustBalance** вызывает событие **AccountIsInDebit**, если баланс становится отрицательным.

Если любые предупреждения останавливает построение, затем преобразовать их либо установить свойство project **C/C++** > **Общие** > **обрабатывать предупреждения как ошибки** для **нет (/ WX-)** и повторной сборки проекта.

## <a name="create-a-core-app-bankaccountcoreapp-to-test-the-windows-runtime-component"></a>Создайте приложение основных компонентов (BankAccountCoreApp) для тестирования компонента среды выполнения Windows.

Теперь создайте новый проект (в вашем решении `BankAccountWRC` или в новом). Создание **Visual C++**   >  **универсальной Windows** > **приложения Core (C++/WinRT)** проект и назовите его  *BankAccountCoreApp*.

Добавить ссылку и перейдите к `\BankAccountWRC\Debug\BankAccountWRC\BankAccountWRC.winmd` (или добавьте ссылку проекта на проект, в том случае, если есть проект, в том же решении). Нажмите кнопку **Добавить**, а затем кнопку **OK**. Теперь выполните сборку BankAccountCoreApp. Маловероятно, что появится сообщение об ошибке, полезные данные файла `readme.txt` не существует, удалите этот файл из проекта компонента среды выполнения Windows, перестройте его, а затем перестройте BankAccountCoreApp.

Во время сборки запускается средство `cppwinrt.exe` для обработки указанного файла `.winmd` в файлы исходного кода, содержащие проецируемые типы для поддержки использования вашего компонента. Заголовок проецируемых типов для классов среды выполнения вашего компонента &mdash; с именем `BankAccountWRC.h`&mdash; создается в папке `\BankAccountCoreApp\BankAccountCoreApp\Generated Files\winrt\`.

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

Каждый раз при щелчке по окну вы вычитаете 1 с баланса банковского счета. Чтобы продемонстрировать, что событие вызывается должным образом, установите точку останова внутри лямбда-выражение, которое обрабатывает **AccountIsInDebit** событий, запустите приложение и щелкните внутри окна.

## <a name="parameterized-delegates-and-simple-signals-across-an-abi"></a>Параметризованные делегатов и простой сигналов через интерфейс ABI

Если события должны быть доступны через двоичный интерфейс приложений (ABI)&mdash;например, между компонентом и его потребляющее приложение&mdash;события необходимо использовать тип делегата среды выполнения Windows. В примере выше используется [ **Windows::Foundation::EventHandler\<T\>**  ](/uwp/api/windows.foundation.eventhandler) среды выполнения Windows тип делегата. [**TypedEventHandler\<TSender, TResult\>**  ](/uwp/api/windows.foundation.eventhandler) является еще одним примером является типом делегата среды выполнения Windows.

Параметры типа для этих двух делегатов типов имеют пересекать интерфейса ABI, поэтому параметры типа должны быть типами среды выполнения Windows, слишком. Включая классы среды выполнения первый и независимых производителей, а также типы-примитивы, такие как числа и строки. Компилятор поможет вам с "*должен быть типа WinRT*" Ошибка, если вы забудете это ограничение.

Если не нужно передавать все параметры или аргументы с определенным событием, можно определить свой собственный простой тип делегата для среды выполнения Windows. В приведенном ниже примере показан более простой версии **BankAccount** класса среды выполнения. Он объявляется тип делегата с именем **SignalDelegate** , а затем он использует, для вызова события вместо события с параметром типа сигнала.

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

## <a name="parameterized-delegates-simple-signals-and-callbacks-within-a-project"></a>Параметризованные делегаты, простой сигналов и обратные вызовы в проекте

Если событие используется только внутренним образом в вашей C++/WinRT проекта (не для всех двоичных файлов), а затем по-прежнему использовать [ **winrt::event** ](/uwp/cpp-ref-for-winrt/event) структуры шаблона, но параметризовать с C++/WinRT элемента не Windows-Runtime [ **winrt::delegate&lt;... T&gt;**  ](/uwp/cpp-ref-for-winrt/delegate) шаблон структуры, делегат эффективных и подсчетом ссылок. Он поддерживает любое число параметров, и они не ограничены типами среды выполнения Windows.

В приведенном ниже примере сначала показано делегата, подпись, которая не принимает никаких параметров (по существу простого сигнала), а затем одно, который принимает строку.

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

Обратите внимание на то, как можно добавить к событию столько подписки делегаты по своему усмотрению. Однако есть некоторые издержки, связанные с событием. Если все, что нужно — это простой обратного вызова с одной подписывающийся делегат, то можно использовать [ **winrt::delegate&lt;... T&gt;**  ](/uwp/cpp-ref-for-winrt/delegate) сам по себе.

```cppwinrt
winrt::delegate<> signalCallback;
signalCallback = [] { std::wcout << L"Hello, World!" << std::endl; };
signalCallback();

winrt::delegate<std::wstring> logCallback;
logCallback = [](std::wstring const& message) { std::wcout << message.c_str() << std::endl; }f;
logCallback(L"Hello, World!");
```

Если вы переносите из C++/CX базы кода, где события и делегаты используются внутри проекта, затем **winrt::delegate** поможет вам выполнить репликацию этого шаблона в C++/WinRT.

## <a name="design-guidelines"></a>Рекомендации по проектированию

Мы рекомендуем передавать события и делегаты, как параметры функции. **Добавить** функции [ **winrt::event** ](/uwp/cpp-ref-for-winrt/event) является исключением, так как в этом случае необходимо передать делегат. Это правило связано с Поскольку делегаты может принимать разные формы для разных языков среды выполнения Windows (с точки зрения, поддерживают ли они регистрации клиента один или несколько). События с их нескольких подписчиков модель, составляют более предсказуемую и согласованную параметр.

Сигнатура делегата обработчика событий должен состоять из двух параметров: *отправителя* (**IInspectable**), и *args* (некоторый тип аргумента события, например [ **RoutedEventArgs**](/uwp/api/windows.ui.xaml.routedeventargs)).

Обратите внимание, что эти рекомендации не обязательно применимы при конструировании внутренним API. Хотя внутренние API часто станет общедоступным со временем.

## <a name="related-topics"></a>См. также
* [Создание API-интерфейсов с помощью C++/WinRT](author-apis.md)
* [Использование API-интерфейсов с помощью C++/WinRT](consume-apis.md)
* [Обработка событий с помощью делегатов в C++/WinRT](handle-events.md)

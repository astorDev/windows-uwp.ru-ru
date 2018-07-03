---
author: stevewhims
description: В этом разделе показано, как создать компонент среды выполнения Windows, который содержит класс среды выполнения, создающий события. В нем также показано приложение, которое использует этот компонент и обрабатывает события.
title: Создание событий в C++/WinRT
ms.author: stwhi
ms.date: 05/07/2018
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: Windows 10, UWP, стандартная, c++, cpp, winrt, проекция, создание, событие
ms.localizationpriority: medium
ms.openlocfilehash: 192000f937989d7218931ce1465bd96d5d9b71c6
ms.sourcegitcommit: 834992ec14a8a34320c96e2e9b887a2be5477a53
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/14/2018
ms.locfileid: "1880885"
---
# <a name="author-events-in-cwinrtwindowsuwpcpp-and-winrt-apisintro-to-using-cpp-with-winrt"></a>Создание событий в [C++/WinRT](/windows/uwp/cpp-and-winrt-apis/intro-to-using-cpp-with-winrt)
В этом разделе показано, как создать компонент среды выполнения Windows, который содержит класс среды выполнения, представляющий банковский счет, который вызывает событие при списании с баланса. В нем также показано базовое приложение, которое использует класс среды выполнения банковского счета, вызывает функцию для изменения баланса и обрабатывает все возникающие события.

> [!NOTE]
> Сведения об установке и использовании расширения C++/WinRT для Visual Studio (VSIX) (которое обеспечивает поддержку шаблона проекта, а также свойств и целевых объектов MSBuild C++/WinRT) см. в разделе [Поддержка Visual Studio для C++/WinRT и VSIX](intro-to-using-cpp-with-winrt.md#visual-studio-support-for-cwinrt-and-the-vsix).

> [!IMPORTANT]
> Основные понятия и термины, которые помогают понять, как использовать и создавать классы среды выполнения с помощью C++/WinRT, см. в разделах [Использование API-интерфейсов в C++/WinRT](consume-apis.md) и [Создание API-интерфейсов в C++/WinRT ](author-apis.md).

## <a name="windowsfoundationeventhandlerlttgt-and-typedeventhandlerlttgt"></a>Windows::Foundation::EventHandler&lt;T&gt; и TypedEventHandler&lt;T&gt;
Если вы хотите вызвать событие из класса среды выполнения, реализованного в компоненте среды выполнения Windows, необходимо использовать [**Windows::Foundation::EventHandler**](/uwp/api/windows.foundation.eventhandler) или [**TypedEventHandler**](/uwp/api/windows.foundation.eventhandler) для типа делегата вашего события. Параметры типа должны являться типами среды выполнения Windows, поэтому наряду со сторонними классами среды выполнения допустимы примитивные типы.

Компилятор отобразит ошибку "*must be WinRT type*", если вы забудете об этом ограничении.

## <a name="winrtdelegatelt-tgt"></a>winrt::delegate&lt;...T&gt;
Если вам необходимо вызвать событие из типа C++ (созданного и используемого в рамках того же проекта), вы можете использовать для типа делегата события [**winrt::delegate**](/uwp/cpp-ref-for-winrt/delegate) из C++/WinRT. В этом случае параметры типа делегата не должны являться типами среды выполнения Windows. При переносе из кода на C++/CX, где события и делегаты используются для внутренних целей (а не в разных двоичных файлах), **winrt::delegate** поможет вам выполнить репликацию этой модели в C++/WinRT.

## <a name="create-a-windows-runtime-component-bankaccountwrc"></a>Создание компонента среды выполнения Windows (BankAccountWRC)
Начните с создания нового проекта в Microsoft Visual Studio. Создайте проект **Visual C++ компонента среды выполнения Windows (C++/ WinRT)** и назовите его *BankAccountWRC* (компонент среды выполнения Windows банковского счета).

Созданный проект содержит файл с именем `Class.idl`. Переименуйте этот файл в `BankAccountWRC.idl` таким образом, чтобы при сборке файл метаданных вашего компонента среды выполнения Windows имел имя самого компонента. В файле `BankAccountWRC.idl` задайте свой интерфейс, как представлено ниже.

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

Сохраните файл и выполните сборку проекта. На данный момент сборка не будет успешной. Однако во время сборки запускается инструмент `midl.exe` для создания файла метаданных компонента среды выполнения Windows (`\BankAccountWRC\Debug\BankAccountWRC\BankAccountWRC.winmd`). Затем запускается средство `cppwinrt.exe` (с параметром `-component`) для создания файлов исходного кода для поддержки создания компонента. Эти файлы включают заглушки для начала реализации класса среды выполнения `BankAccount`, объявленного в вашем IDL. Это заглушки `\BankAccountWRC\BankAccountWRC\Generated Files\sources\BankAccount.h` и `BankAccount.cpp`.

Скопируйте файлы заглушек `BankAccount.h` и `BankAccount.cpp` из `\BankAccountWRC\BankAccountWRC\Generated Files\sources\` в папку проекта `\BankAccountWRC\BankAccountWRC\`. Убедитесь, что в **Обозревателе решений** включена функция **Показать все файлы**. Щелкните правой кнопкой мыши скопированные файлы заглушек и выберите **Включить в проект**. Кроме того, щелкните правой кнопкой мыши `Class.h` и `Class.cpp` и нажмите **Исключить из проекта **.

Теперь давайте откроем `BankAccount.h` и `BankAccount.cpp` и реализуем класс среды выполнения. В `BankAccount.h` добавьте два закрытых члена в реализацию (*не* фабричную реализацию) BankAccount.

```cppwinrt
// BankAccount.h
...
namespace winrt::BankAccountWRC::implementation
{
    struct BankAccount : BankAccountT<BankAccount>
    {
        ...

    private:
        winrt::event<Windows::Foundation::EventHandler<float>> accountIsInDebitEvent;
        float balance{ 0.f };
    };
}
...
```

В `BankAccount.cpp` реализуйте функции, как показано в следующем примере кода. В C++/WinRT объявленное в IDL событие реализуется как набор перегруженных функций (аналогично реализации свойства в виде пары перегруженных функций get и set). Одна перегрузка принимает делегат для регистрации и возвращает маркер. Другая получает маркер и отменяет регистрацию связанного делегата.

```cppwinrt
// BankAccount.cpp
...
namespace winrt::BankAccountWRC::implementation
{
    event_token BankAccount::AccountIsInDebit(Windows::Foundation::EventHandler<float> const& handler)
    {
        return accountIsInDebitEvent.add(handler);
    }

    void BankAccount::AccountIsInDebit(event_token const& token)
    {
        accountIsInDebitEvent.remove(token);
    }

    void BankAccount::AdjustBalance(float value)
    {
        balance += value;
        if (balance < 0.f) accountIsInDebitEvent(*this, balance);
    }
}
```

Вам не нужно реализовывать перегрузку для средства отзыва событий (дополнительные сведения см. в разделе [Отзыв зарегистрированного делегата](handle-events.md#revoke-a-registered-delegate))&mdash; это за вас делает проекция C++/WinRT. Другие перегрузки не прописываются в проекцию, чтобы предоставить вам возможность их реализации оптимальным для вашего сценария образом. Вызов функций [**event::add**](/uwp/cpp-ref-for-winrt/event#eventadd-function) и [**event::remove**](/uwp/cpp-ref-for-winrt/event#eventremove-function) таким образом является эффективным и безопасным с точки зрения параллельной обработки и многопотоковой обработки по умолчанию. Но если у вас очень большое количество событий, вам может не требоваться поле для каждого из них, и вы выберете вместо этого какой-либо другой вид выборочной реализации.

Выше также можно увидеть, что реализация функции **AdjustBalance** вызывает событие **AccountIsInDebit**, если баланс становится отрицательным.

Если какие-либо предупреждения не позволяют выполнить сборку, установите для свойства проекта **C/C++** > **Общие** > **Обрабатывать предупреждения как ошибки** значение **Нет (/WX-)** и выполните сборку проекта заново.

## <a name="create-a-core-app-bankaccountcoreapp-to-test-the-windows-runtime-component"></a>Создайте приложение основных компонентов (BankAccountCoreApp) для тестирования компонента среды выполнения Windows.
Теперь создайте новый проект (в вашем решении `BankAccountWRC` или в новом). Создайте проект **приложения основных компонентов Visual C++ (C++/WinRT)** и назовите его *BankAccountCoreApp*.

Добавьте ссылку и перейдите к `\BankAccountWRC\Debug\BankAccountWRC\BankAccountWRC.winmd` (или добавьте ссылку на проект, если два проекта находятся в одном решении). Нажмите кнопку **Добавить**, а затем кнопку **OK**. Теперь выполните сборку BankAccountCoreApp. Если вы видите сообщение о том, что файл полезных данных `readme.txt` не существует, исключите этот файл из проекта компонента среды выполнения Windows, заново выполните сборку проекта, а затем снова выполните сборку BankAccountCoreApp.

Во время сборки запускается средство `cppwinrt.exe` для обработки указанного файла `.winmd` в файлы исходного кода, содержащие проецируемые типы для поддержки использования вашего компонента. Заголовок проецируемых типов для классов среды выполнения вашего компонента &mdash; с именем `BankAccountWRC.h` &mdash; создается в папке `\BankAccountCoreApp\BankAccountCoreApp\Generated Files\winrt\`.

Включите этот заголовок в `App.cpp`.

```cppwinrt
#include <winrt/BankAccountWRC.h>
```

Кроме того, добавьте в `App.cpp` следующий код для создания экземпляра BankAccount (с помощью конструктора по умолчанию проецируемого типа), зарегистрируйте обработчик событий, а затем произведите списание со счета.

```cppwinrt
struct App : implements<App, IFrameworkViewSource, IFrameworkView>
{
    BankAccountWRC::BankAccount bankAccount;
    event_token eventToken;
    ...
    
    void Initialize(CoreApplicationView const &)
    {
        eventToken = bankAccount.AccountIsInDebit([](const auto &, float balance)
        {
            assert(balance < 0.f);
        });
    }
    ...

    void Uninitialize()
    {
        bankAccount.AccountIsInDebit(eventToken);
    }
    ...

    void OnPointerPressed(IInspectable const &, PointerEventArgs const & args)
    {
        bankAccount.AdjustBalance(-1.f);
        ...
    }
    ...
};
```

Каждый раз при щелчке по окну вы вычитаете 1 с баланса банковского счета. Чтобы продемонстрировать, что событие вызывается должным образом, поместите точку останова внутри лямбда-выражения, запустите приложение и щелкните внутри окна.

## <a name="related-topics"></a>Статьи по теме
* [Создание API-интерфейсов в C++/WinRT](author-apis.md)
* [Использование API-интерфейсов в C++/WinRT](consume-apis.md)
* [Обработка событий с помощью делегатов в C++/WinRT](handle-events.md)

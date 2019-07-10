---
description: В этом разделе показано, как использовать интерфейсы API C++/WinRT, если они реализованы Windows, сторонним поставщиком компонентов или вами самостоятельно.
title: Использование интерфейсов API с помощью C++/WinRT
ms.date: 04/23/2019
ms.topic: article
keywords: Windows 10, uwp, стандартная c++, cpp, winrt, проецируемый, проекция, реализация, класс среды выполнения, активация
ms.localizationpriority: medium
ms.openlocfilehash: e6bf1e7fb32533aa9d7b865ac7c8afc374290e54
ms.sourcegitcommit: aaa4b898da5869c064097739cf3dc74c29474691
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66360351"
---
# <a name="consume-apis-with-cwinrt"></a>Использование интерфейсов API с помощью C++/WinRT

В этом разделе показано, как использовать API-интерфейсы [C++/WinRT](/windows/uwp/cpp-and-winrt-apis/intro-to-using-cpp-with-winrt), если они являются компонентом Windows, реализованы сторонним поставщиком компонентов или вами самостоятельно.

## <a name="if-the-api-is-in-a-windows-namespace"></a>Если API находится в пространстве имен Windows
Это наиболее распространенный случай, в котором вы будете использовать API среды выполнения Windows. Для каждого типа в пространстве имен Windows, указанного в метаданных, C++/WinRT определяет подходящий для C++ эквивалент (называемый *проецируемый тип*). Проецируемый тип имеет то же полное доменное имя, что и тип в Windows, но он размещается в пространстве имен C++ **winrt** с использованием синтаксиса C++. Например, [**Windows::Foundation:: URI**](/uwp/api/windows.foundation.uri) проецируется в C++/WinRT как **winrt::Windows::Foundation::Uri**.

Приведем простой пример кода. Если вы хотите скопировать и вставить этот пример непосредственно в главный файл исходного кода проекта **консольного приложения Windows (C++/WinRT)** , сначала задайте параметр **Не использовать предварительно скомпилированные заголовки** в свойствах проекта.

```cppwinrt
// main.cpp
#include <winrt/Windows.Foundation.h>

using namespace winrt;
using namespace Windows::Foundation;

int main()
{
    winrt::init_apartment();
    Uri contosoUri{ L"http://www.contoso.com" };
    Uri combinedUri = contosoUri.CombineUri(L"products");
}
```

Включенный заголовок `winrt/Windows.Foundation.h` входит в состав пакета SDK, который находится в папке `%WindowsSdkDir%Include<WindowsTargetPlatformVersion>\cppwinrt\winrt\`. Заголовки в этой папке содержат типы пространства имен Windows, проецируемые в C++/WinRT. В этом примере `winrt/Windows.Foundation.h` содержит **winrt::Windows::Foundation::Uri**, который представляет собой проецируемый тип для класса среды выполнения [**Windows::Foundation::Uri**](/uwp/api/windows.foundation.uri).

> [!TIP]
> Каждый раз, когда вы хотите использовать тип из пространства имен Windows, включайте заголовок C++/WinRT, соответствующий этому пространству имен. Директивы `using namespace` являются необязательными, но удобными.

В примере кода выше после инициализации C++/WinRT значение проецируемого типа **winrt::Windows::Foundation::Uri** помещается в стек с помощью одного из его задокументированных конструкторов (в этом примере — [**Uri(String)** ](/uwp/api/windows.foundation.uri.-ctor#Windows_Foundation_Uri__ctor_System_String_)). Для данного наиболее распространенного случая использования обычно это все, что нужно сделать. Когда у вас есть значение проецируемого типа C++/WinRT, вы можете работать с ним, как будто это экземпляр фактического типа среды выполнения Windows, поскольку он имеет те же члены.

На самом деле это проецируемое значение является своего рода псевдонимом; по сути это просто смарт-указатель на расположенный за ним объект. Конструктор(ы) проецируемого значения вызывает [**RoActivateInstance**](https://docs.microsoft.com/windows/desktop/api/roapi/nf-roapi-roactivateinstance) для создания экземпляра опорного класса среды выполнения Windows (в этом случае — **Windows.Foundation.Uri**) и сохранения интерфейса по умолчанию этого объекта внутри нового проецируемого значения. Как показано ниже, ваши вызовы членов проецируемого значения фактически делегируются, с помощью смарт-указателя, на стоящий за ним объект, и в нем и происходят изменения состояния.

![Проецируемый тип Windows::Foundation::Uri](images/uri.png)

Когда значение `contosoUri` выходит за пределы области, оно разрушается и убирает ссылку на интерфейс по умолчанию. Если эта ссылка является последней ссылкой на опорный объект **Windows.Foundation.Uri** среды выполнения Windows, этот опорный объект также разрушается.

> [!TIP]
> *Проецируемый тип* является оболочкой класса среды выполнения для использования его API-интерфейсов. *Проецируемый интерфейс* является оболочкой интерфейса среды выполнения Windows.

## <a name="cwinrt-projection-headers"></a>Проецируемые заголовки C++/WinRT
Для использования API-интерфейсов пространства имен Windows из C++/WinRT требуется включить заголовки из папки `%WindowsSdkDir%Include<WindowsTargetPlatformVersion>\cppwinrt\winrt`. Ситуация, когда тип в подчиненном пространстве имен ссылается на типы в его ближайшем родительском пространстве имен, является типичной. Соответственно, каждый проецируемый заголовок C++/WinRT автоматически включает файл заголовков его родительского пространства имен, и поэтому вам не *требуется* явным образом включать его. Хотя если вы это сделаете, ошибки не будет.

Например, для пространства имен [**Windows::Security::Cryptography::Certificates**](/uwp/api/windows.security.cryptography.certificates) эквивалентные определения типов C++/WinRT располагаются в `winrt/Windows.Security.Cryptography.Certificates.h`. Типам в **Windows::Security::Cryptography::Certificates** требуются типы в родительском пространстве имен **Windows::Security::Cryptography**, а типам в этом пространстве имен могут потребоваться типы в своем собственном родительском пространстве имен **Windows::Security**.

Таким образом, при включении `winrt/Windows.Security.Cryptography.Certificates.h` этот файл в свою очередь содержит `winrt/Windows.Security.Cryptography.h`, и `winrt/Windows.Security.Cryptography.h` содержит в себе `winrt/Windows.Security.h`. И здесь цепочка прекращается, поскольку не содержит`winrt/Windows.h`. Этот процесс транзитивного включения останавливается на пространстве имен второго уровня.

Этот процесс транзитивно включает файлы заголовков, которые предоставляют необходимые *объявления* и *реализации* для классов, определенных в родительских пространствах имен.

Член типа в одном пространстве имен может ссылаться на один или несколько типов в другом несвязанном пространстве имен. Для того чтобы компилятор выполнил успешную компиляцию этих определений членов, компилятору нужно видеть объявления типов для закрытия всех этих типов. Соответственно, каждый проецируемый заголовок C++/WinRT включает заголовки пространства имен, которые ему требуются для *объявления* всех зависимых типов. В отличие от родительских пространств имен в этом процессе *не* перенимаются *реализации* для ссылочных типов.

> [!IMPORTANT]
> Если вы хотите действительно *использовать* тип (создать экземпляр, вызывать методы и т. п.), объявленный в несвязанном пространстве имен, вам необходимо включить файл заголовков нужного пространства имен для этого типа. Автоматически включаются только *объявления*, но не *реализации*.

Например, если вы только включите `winrt/Windows.Security.Cryptography.Certificates.h`, то это приведет к получению объявлений из этих пространств имен (и далее транзитивным образом).

- Windows.Foundation
- Windows.Foundation.Collections
- Windows.Networking
- Windows.Storage.Streams
- Windows.Security.Cryptography

Другими словами, некоторые API-интерфейсы объявляются в заголовке, который вы включили. Но их определения находятся в заголовке, который вы еще не включили. Поэтому, если затем вызвать [**Windows::Foundation::Uri::RawUri**](/uwp/api/windows.foundation.uri.rawuri), то вы получите ошибку компоновщика, указывающую, что элемент не определен. Решением является явное `#include <winrt/Windows.Foundation.h>`. Как правило при отображении ошибки компоновщика, такой как эта, включите заголовок с именем для пространства имен API и повторите сборку.

## <a name="accessing-members-via-the-object-via-an-interface-or-via-the-abi"></a>Доступ к членам через объект, через интерфейс или с помощью ABI
С помощью проекции C++/WinRT представление времени выполнения для класса среды выполнения Windows представляет собой не что иное, как лежащие в основе базовые интерфейсы ABI. Но для удобства можно использовать классы в коде так, как задумывалось их автором. Например, можно вызвать метод **ToString** для [**Uri**](/uwp/api/windows.foundation.uri), как если бы он был методом класса (на самом деле, по своей сути это метод отдельного интерфейса **IStringable**).

```cppwinrt
Uri contosoUri{ L"http://www.contoso.com" };
WINRT_ASSERT(contosoUri.ToString() == L"http://www.contoso.com/"); // QueryInterface is called at this point.
```

Такое удобство обеспечивается путем запроса к соответствующему интерфейсу. Но вы всегда сохраняете контроль. Вы можете пожертвовать частью этого удобства в пользу повышения производительности путем самостоятельного извлечения интерфейса IStringable и его непосредственного использования. В следующем примере кода вы получите фактический указатель на интерфейс IStringable во время выполнения (с помощью одноразового запроса). После этого ваш вызов **ToString** будет прямым и позволит избежать любых дальнейших обращений к **QueryInterface**.

```cppwinrt
...
IStringable stringable = contosoUri; // One-off QueryInterface.
WINRT_ASSERT(stringable.ToString() == L"http://www.contoso.com/");
```

Можно выбрать этот метод, если вы знаете, что будете вызывать несколько методов одного интерфейса.

Кстати, если вам потребуется доступ к членам на уровне ABI, его можно получить. В примере кода ниже показано, как это сделать, а дополнительные сведения и примеры кода можно найти в разделе [Взаимодействие между C++/WinRT и ABI](interop-winrt-abi.md).

```cppwinrt
#include <Windows.Foundation.h>
#include <unknwn.h>
#include <winrt/Windows.Foundation.h>
using namespace winrt::Windows::Foundation;

int main()
{
    winrt::init_apartment();
    Uri contosoUri{ L"http://www.contoso.com" };

    int port = contosoUri.Port(); // Access the Port "property" accessor via C++/WinRT.

    winrt::com_ptr<ABI::Windows::Foundation::IUriRuntimeClass> abiUri = contosoUri.as<ABI::Windows::Foundation::IUriRuntimeClass>();
    HRESULT hr = abiUri->get_Port(&port); // Access the get_Port ABI function.
}
```

## <a name="delayed-initialization"></a>Отложенная инициализация
Даже конструктор по умолчанию проецируемого типа вызывает создание опорного объекта среды выполнения Windows. Если вы хотите создать переменную проецируемого типа без создания объекта среды выполнения Windows (чтобы отложить эту работу на более позднее время), это можно сделать. Объявите переменную или поле, используя специальный конструктор C++/WinRT `nullptr_t` для проецируемого типа.

```cppwinrt
#include <winrt/Windows.Storage.Streams.h>
using namespace winrt::Windows::Storage::Streams;

#define MAX_IMAGE_SIZE 1024

struct Sample
{
    void DelayedInit()
    {
        // Allocate the actual buffer.
        m_gamerPicBuffer = Buffer(MAX_IMAGE_SIZE);
    }

private:
    Buffer m_gamerPicBuffer{ nullptr };
};

int main()
{
    winrt::init_apartment();
    Sample s;
    // ...
    s.DelayedInit();
}
```

Все конструкторы проецируемого типа, *кроме* конструктора `nullptr_t`, приводят к созданию опорного объекта среды выполнения Windows. Конструктор `nullptr_t` является по сути безоперационным. Он ожидает инициализацию проецируемого объекта в более поздний момент. Таким образом, независимо от того, имеет ли класс среды выполнения конструктор или нет, этот способ можно использовать для эффективной отложенной инициализации.

Это соображение влияет на другие места, где вы вызываете конструктор по умолчанию, например, на векторы и карты. Рассмотрим пример кода, для которого вам потребуется проект **Пустое приложение (C++/WinRT)** .

```cppwinrt
std::map<int, TextBlock> lookup;
lookup[2] = value;
```

Присвоение создает новый **TextBlock**, а затем немедленно перезаписывает его`value`. Вот средство.

```cppwinrt
std::map<int, TextBlock> lookup;
lookup.insert_or_assign(2, value);
```

## <a name="if-the-api-is-implemented-in-a-windows-runtime-component"></a>Если API-интерфейс реализован в компоненте среды выполнения Windows
Этот раздел применим, если вы создали компонент самостоятельно или получили его от поставщика.

> [!NOTE]
> Сведения об установке и использовании расширения C++/WinRT для Visual Studio (VSIX) и пакета NuGet (которые вместе обеспечивают поддержку шаблона проекта и сборки) см. в разделе о [поддержке C++/WinRT в Visual Studio](intro-to-using-cpp-with-winrt.md#visual-studio-support-for-cwinrt-xaml-the-vsix-extension-and-the-nuget-package).

В своем проекте приложения обратитесь к файлу метаданных среды выполнения Windows компонента для компонента среды выполнения Windows (`.winmd`) и выполните сборку. Во время сборки `cppwinrt.exe` средство создает стандартную C++ библиотеку, которая полностью описывает&mdash;или *проекты*&mdash;поверхность API для компонента. Другими словами, созданная библиотека содержит проецируемые типы для компонента.

Затем, как и в случае типа пространства имен Windows, необходимо включить заголовок и создать проецируемый типа через один из его конструкторов. Код запуска проекта вашего приложения регистрирует класс среды выполнения, а конструктор проецируемого типа вызывает [**RoActivateInstance**](https://docs.microsoft.com/windows/desktop/api/roapi/nf-roapi-roactivateinstance) для активации класса среды выполнения из указанного компонента.

```cppwinrt
#include <winrt/BankAccountWRC.h>

struct App : implements<App, IFrameworkViewSource, IFrameworkView>
{
    BankAccountWRC::BankAccount bankAccount;
    ...
};
```

Дополнительные сведения, код и пошаговое руководство по использованию API-интерфейсов, реализованных в компоненте среды выполнения Windows см. в разделе [Создание событий в C++/WinRT ](author-events.md#create-a-core-app-bankaccountcoreapp-to-test-the-windows-runtime-component).

## <a name="if-the-api-is-implemented-in-the-consuming-project"></a>Если API-интерфейс реализован в использующем проекте
Тип, используемый из пользовательского интерфейса XAML, должен являться классом среды выполнения, даже если он находится в том же проекте, что и XAML.

В этом сценарии создается проецируемый тип из метаданных среды выполнения Windows класса среды выполнения (`.winmd`). Необходимо опять же включить заголовок, но на этот раз проецируемый тип создается через конструктор `nullptr`. Этот конструктор не выполняет инициализацию, поэтому далее необходимо назначить значение экземпляру через вспомогательную функцию [**winrt::make**](/uwp/cpp-ref-for-winrt/make), передав все необходимые аргументы конструктора. Класс среды выполнения, реализованный в том же проекте, что и использующий его код, не обязательно регистрировать, а также необязательно создавать его экземпляр посредством активации среды выполнения Windows/COM.

Вам потребуется **Пустое приложение (C++/WinRT)** проект для этого примера кода.

```cppwinrt
// MainPage.h
...
struct MainPage : MainPageT<MainPage>
{
    ...
    private:
        Bookstore::BookstoreViewModel m_mainViewModel{ nullptr };
        ...
    };
}
...
// MainPage.cpp
...
#include "BookstoreViewModel.h"

MainPage::MainPage()
{
    m_mainViewModel = winrt::make<Bookstore::implementation::BookstoreViewModel>();
    ...
}
```

Дополнительные сведения, код и пошаговое руководство по использованию класса среды выполнения, реализованного в использующем его проекте, см. в разделе [Элементы управления XAML; привязка к свойству C++/WinRT ](binding-property.md#add-a-property-of-type-bookstoreviewmodel-to-mainpage).

## <a name="instantiating-and-returning-projected-types-and-interfaces"></a>Создание и возврат прогнозируемых типов и интерфейсов
Вот пример того, как проецируемые типы и интерфейсы могут выглядеть в вашем использующем их проекте. Помните, что проецируемых типы (например, указанному в этом примере), формируемые инструментами и не будут созданы самостоятельно.

```cppwinrt
struct MyRuntimeClass : MyProject::IMyRuntimeClass, impl::require<MyRuntimeClass,
    Windows::Foundation::IStringable, Windows::Foundation::IClosable>
```

**MyRuntimeClass** является проецируемым типом; проецируемые интерфейсы включают **IMyRuntimeClass**, **IStringable** и **IClosable**. В этом разделе были продемонстрированы различные способы создания экземпляра проецируемого типа. Вот напоминание и краткая сводка, использующая в качестве примера **MyRuntimeClass**.

```cppwinrt
// The runtime class is implemented in another compilation unit (it's either a Windows API,
// or it's implemented in a second- or third-party component).
MyProject::MyRuntimeClass myrc1;

// The runtime class is implemented in the same compilation unit.
MyProject::MyRuntimeClass myrc2{ nullptr };
myrc2 = winrt::make<MyProject::implementation::MyRuntimeClass>();
```

- Можно осуществлять доступ к членам всех интерфейсов проецируемого типа.
- Можно возвращать проецируемый тип вызывающему объекту.
- Проецируемые типы и интерфейсы являются производными от [**winrt::Windows::Foundation::IUnknown**](/uwp/cpp-ref-for-winrt/windows-foundation-iunknown). Поэтому можно вызывать [**IUnknown::as**](/uwp/cpp-ref-for-winrt/windows-foundation-iunknown#iunknownas-function) для проецируемого типа или интерфейса для запроса других проецируемых интерфейсов, которые также можно использовать или вернуть вызывающему объекту. Функция-член **as** работает как [**QueryInterface**](https://docs.microsoft.com/windows/desktop/api/unknwn/nf-unknwn-iunknown-queryinterface(q_)).

```cppwinrt
void f(MyProject::MyRuntimeClass const& myrc)
{
    myrc.ToString();
    myrc.Close();
    IClosable iclosable = myrc.as<IClosable>();
    iclosable.Close();
}
```

## <a name="activation-factories"></a>Фабрики активации
Удобный прямолинейный способ создания объекта C++/WinRT выглядит следующим образом.

```cppwinrt
using namespace winrt::Windows::Globalization::NumberFormatting;
...
CurrencyFormatter currency{ L"USD" };
```

Но бывают ситуации, в которых вам потребуется создать фабрику активации самостоятельно, а затем создавать объекты из нее по мере необходимости. Вот несколько примеров, показывающих использование шаблона функции [**winrt::get_activation_factory**](/uwp/cpp-ref-for-winrt/get-activation-factory).

```cppwinrt
using namespace winrt::Windows::Globalization::NumberFormatting;
...
auto factory = winrt::get_activation_factory<CurrencyFormatter, ICurrencyFormatterFactory>();
CurrencyFormatter currency = factory.CreateCurrencyFormatterCode(L"USD");
```

```cppwinrt
using namespace winrt::Windows::Foundation;
...
auto factory = winrt::get_activation_factory<Uri, IUriRuntimeClassFactory>();
Uri account = factory.CreateUri(L"http://www.contoso.com");
```

Классы в двух приведенных выше примерах относятся к типам из пространства имен Windows. В следующем примере **BankAccountWRC::BankAccount** — это пользовательский тип, реализованный в компоненте среды выполнения Windows.

```cppwinrt
auto factory = winrt::get_activation_factory<BankAccountWRC::BankAccount>();
BankAccountWRC::BankAccount account = factory.ActivateInstance<BankAccountWRC::BankAccount>();
```

## <a name="important-apis"></a>Важные API
* [Интерфейс QueryInterface](https://docs.microsoft.com/windows/desktop/api/unknwn/nf-unknwn-iunknown-queryinterface(q_))
* [Функция RoActivateInstance](https://docs.microsoft.com/windows/desktop/api/roapi/nf-roapi-roactivateinstance)
* [Класс Windows::Foundation::URI](/uwp/api/windows.foundation.uri)
* [Шаблон функции winrt::get_activation_factory](/uwp/cpp-ref-for-winrt/get-activation-factory)
* [Шаблон функции winrt::make](/uwp/cpp-ref-for-winrt/make)
* [Структура winrt::Windows::Foundation::IUnknown](/uwp/cpp-ref-for-winrt/windows-foundation-iunknown)

## <a name="related-topics"></a>Статьи по теме
* [Создание событий в C++/WinRT](author-events.md#create-a-core-app-bankaccountcoreapp-to-test-the-windows-runtime-component)
* [Взаимодействие между C++/WinRT и интерфейсом ABI](interop-winrt-abi.md)
* [Введение в C++/WinRT](intro-to-using-cpp-with-winrt.md)
* [Элементы управления XAML; привязка к свойству C++/WinRT](binding-property.md#add-a-property-of-type-bookstoreviewmodel-to-mainpage)

---
author: stevewhims
description: В этом разделе показано, как использовать API C++/WinRT, если они реализованы Windows, сторонним поставщиком компонентов или вами самостоятельно.
title: Использование API-интерфейсов с помощью C++/WinRT
ms.author: stwhi
ms.date: 04/18/2018
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: Windows 10, uwp, стандартная c++, cpp, winrt, проецируемый, проекция, реализация, класс среды выполнения, активация
ms.localizationpriority: medium
ms.openlocfilehash: e777aca8f1d9f3892f67b10c785c056b14c8070c
ms.sourcegitcommit: ab92c3e0dd294a36e7f65cf82522ec621699db87
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/03/2018
ms.locfileid: "1832248"
---
# <a name="consume-apis-with-cwinrtwindowsuwpcpp-and-winrt-apisintro-to-using-cpp-with-winrt"></a>Использование API-интерфейсов с помощью [C++/WinRT](/windows/uwp/cpp-and-winrt-apis/intro-to-using-cpp-with-winrt)
> [!NOTE]
> **Некоторые сведения относятся к предварительным версиям продуктов, в которые перед коммерческим выпуском могут быть внесены существенные изменения. Майкрософт не дает никаких гарантий, явных или подразумеваемых, в отношении предоставленной здесь информации.**

В этом разделе показано, как использовать API C++/WinRT, если они являются компонентом Windows, реализованы сторонним поставщиком компонентов или вами самостоятельно.

## <a name="if-the-api-is-in-a-windows-namespace"></a>Если API находится в пространстве имен Windows
Это наиболее распространенный случай, в котором вы будете использовать API среды выполнения Windows. Приведем простой пример кода.

```cppwinrt
#include <winrt/Windows.Foundation.h>

using namespace winrt;
using namespace Windows::Foundation;

int main()
{
    winrt::init_apartment();
    Uri contosoUri{ L"http://www.contoso.com" };
}
```

Включенный заголовок `winrt/Windows.Foundation.h` входит в состав пакета SDK, который находится в папке `%WindowsSdkDir%Include<WindowsTargetPlatformVersion>\cppwinrt\winrt\`. Заголовки в этой папке содержат API-интерфейсы Windows, проецируемые в C++/WinRT. Каждый раз, когда вы хотите использовать тип из пространства имен Windows, включайте заголовок проекции C++/ WinRT, соответствующий этому пространству имен. Директивы `using namespace` являются необязательными, но удобными.

В этом примере `winrt/Windows.Foundation.h` содержит проецируемый тип для класса среды выполнения [**Windows::Foundation::Uri**](/uwp/api/windows.foundation.uri).

> [!TIP]
> *Проецируемый тип* является оболочкой класса среды выполнения для целях использования его API. *Проецируемый интерфейс* является оболочкой интерфейса среды выполнения Windows.

В примере выше после инициализации C++/WinRT, создается проецируемый тип **Uri** через один из его открыто документированных конструкторов (в этом примере — [**Uri(String)**](/uwp/api/windows.foundation.uri#Windows_Foundation_Uri__ctor_System_String_)). Для данного наиболее распространенного случая использования это все, что нужно сделать.

## <a name="if-the-api-is-implemented-in-a-windows-runtime-component"></a>Если API-интерфейс реализован в компоненте среды выполнения Windows
Этот раздел применим, если вы создали компонент самостоятельно или получили его от поставщика.

> [!NOTE]
> Сведения о текущей доступности расширения C++/WinRT для Visual Studio (VSIX) (которое обеспечивает поддержку шаблона проекта, а также свойств и целевых объектов MSBuild C++/WinRT), см. в разделе [Поддержка Visual Studio для C++/WinRT и VSIX](intro-to-using-cpp-with-winrt.md#visual-studio-support-for-cwinrt-and-the-vsix).

В проекте приложения создайте ссылку на файл метаданных среды выполнения Windows (`.winmd`) компонента среды выполнения Windows и выполните сборку. Во время сборки средство `cppwinrt.exe` создает стандартную библиотеку C++, которая полностью описывает &mdash; или *проецирует *&mdash; поверхность API для компонента. Другими словами, созданная библиотека содержит проецируемые типы для компонента.

Затем, как и в случае типа пространства имен Windows, необходимо включить заголовок и создать проецируемый типа через один из его конструкторов. Код запуска проекта вашего приложения регистрирует класс среды выполнения, а конструктор проецируемого типа вызывает [**RoActivateInstance**](https://msdn.microsoft.com/library/br224646) для активации класса среды выполнения из указанного компонента.

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
    m_mainViewModel = make<Bookstore::implementation::BookstoreViewModel>();
    ...
}
```

Дополнительные сведения, код и пошаговое руководство по использованию класса среды выполнения, реализованного в использующем его проекте, см. в разделе [Элементы управления XAML; привязка к свойству C++/WinRT ](binding-property.md#add-a-property-of-type-bookstoreviewmodel-to-mainpage).

## <a name="instantiating-and-returning-projected-types-and-interfaces"></a>Создание экземпляров и возврат проецируемых типов и интерфейсов
Вот пример того, как проецируемые типы и интерфейсы могут выглядеть в вашем использующем их проекте.

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
- Проецируемые типы и интерфейсы являются производными от [**winrt::Windows::Foundation::IUnknown**](/uwp/cpp-ref-for-winrt/windows-foundation-iunknown). Поэтому можно вызывать [**IUnknown::as**](/uwp/cpp-ref-for-winrt/windows-foundation-iunknown#iunknownas-function) для проецируемого типа или интерфейса для запроса других проецируемых интерфейсов, которые также можно использовать или вернуть вызывающему объекту. Функция-член **as** работает как [**QueryInterface**](https://msdn.microsoft.com/library/windows/desktop/ms682521).

```cppwinrt
void f(MyProject::MyRuntimeClass const& myrc)
{
    myrc.ToString();
    myrc.Close();
    IClosable iclosable = myrc.as<IClosable>();
    iclosable.Close();
}
```

## <a name="important-apis"></a>Важные API
* [Шаблон функции winrt::make](/uwp/cpp-ref-for-winrt/make)
* [winrt::Windows::Foundation::IUnknown::as](/uwp/cpp-ref-for-winrt/windows-foundation-iunknown#iunknownas-function)

## <a name="related-topics"></a>Статьи по теме
* [Создание событий в C++/WinRT](author-events.md#create-a-core-app-bankaccountcoreapp-to-test-the-windows-runtime-component)
* [Элементы управления XAML; привязка к свойству C++/WinRT](binding-property.md#add-a-property-of-type-bookstoreviewmodel-to-mainpage)

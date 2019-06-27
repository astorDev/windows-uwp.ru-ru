---
description: В этом разделе показано, как создавать интерфейсы API C++/ WinRT, используя базовую структуру **winrt::implements** прямо или косвенно.
title: Создание интерфейсов API с помощью C++/WinRT
ms.date: 04/23/2019
ms.topic: article
keywords: Windows 10, uwp, стандартная, c++, cpp, winrt, проецируемый, проекция, реализация, реализовывать, класс среды выполнения, активация
ms.localizationpriority: medium
ms.openlocfilehash: 526c6fba76539a5d43231c29479621478b2dde59
ms.sourcegitcommit: aaa4b898da5869c064097739cf3dc74c29474691
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "65821080"
---
# <a name="author-apis-with-cwinrt"></a>Создание интерфейсов API с помощью C++/WinRT

В этом разделе показано, как создавать интерфейсы API [C++/ WinRT](/windows/uwp/cpp-and-winrt-apis/intro-to-using-cpp-with-winrt), используя базовую структуру [**winrt::implements**](/uwp/cpp-ref-for-winrt/implements) прямо или косвенно. Для термина *создавать* в этом контексте являются синонимами *производить* и *реализовывать*. В этом разделе рассматриваются следующие сценарии реализации интерфейсов API на C++/WinRT в указанном порядке.

- Вы *не* разрабатываете класс среды выполнения Windows; а просто реализуете один или нескольких интерфейсов среды выполнения Windows для локального использования в вашем приложении. Вы создаете производный класс непосредственно от **winrt::implements** и реализуете функции.
- Вы *создаете* класс среды выполнения. Возможно, вы разрабатываете компонент для использования в приложении. Вы также можете создать тип для использования из пользовательского интерфейса XAML, и в этом случае вы одновременно реализуете и используете класс среды выполнения в рамках одной и той же единицы компиляции. В этих случаях вы позволяете инструментам создавать для вас классы, производные от **winrt::implements** .

В обоих случаях тип, реализующий ваши API-интерфейсы C++/ WinRT, называется *типом реализации*.

> [!IMPORTANT]
> Очень важно различать понятия типа реализации и типа проекции. Тип проекции описан в статье [Использование API-интерфейсов в C++/WinRT ](consume-apis.md).

## <a name="if-youre-not-authoring-a-runtime-class"></a>Если вы *не* создаете класс среды выполнения
В самом простом варианте вы реализуете интерфейс среды выполнения Windows для локального использования. Вам не нужен класс среды выполнения, а нужен обычный класс C++. Например вы можете создать приложение на основе [**CoreApplication**](/uwp/api/windows.applicationmodel.core.coreapplication).

> [!NOTE]
> Сведения об установке и использовании расширения C++/WinRT для Visual Studio (VSIX) и пакета NuGet (которые вместе обеспечивают поддержку шаблона проекта и сборки) см. в разделе о [поддержке C++/WinRT в Visual Studio](intro-to-using-cpp-with-winrt.md#visual-studio-support-for-cwinrt-xaml-the-vsix-extension-and-the-nuget-package).

В Visual Studio шаблон проекта приложения основных компонентов **Visual C++**  > **Windows Universal** > **Базовое приложение (C++/WinRT)** иллюстрирует шаблон **CoreApplication**. Этот шаблон начинается с передачи реализации [**Windows::ApplicationModel::Core::IFrameworkViewSource**](/uwp/api/windows.applicationmodel.core.iframeworkviewsource) в [**CoreApplication::Run**](/uwp/api/windows.applicationmodel.core.coreapplication.run).

```cppwinrt
using namespace Windows::ApplicationModel::Core;
int __stdcall wWinMain(HINSTANCE, HINSTANCE, PWSTR, int)
{
    IFrameworkViewSource source = ...
    CoreApplication::Run(source);
}
```

**CoreApplication** использует интерфейс для создания первого представления в приложении. На обобщенном уровне **IFrameworkViewSource** выглядит следующим образом.

```cppwinrt
struct IFrameworkViewSource : IInspectable
{
    IFrameworkView CreateView();
};
```

На этом же уровне реализация **CoreApplication::Run** делает следующее.

```cppwinrt
void Run(IFrameworkViewSource viewSource) const
{
    IFrameworkView view = viewSource.CreateView();
    ...
}
```

Вы, то есть разработчик, реализуете интерфейс **IFrameworkViewSource**. C++/WinRT имеет шаблон базовой структуры [**winrt::implements**](/uwp/cpp-ref-for-winrt/implements), что позволяет упростить реализацию одного или нескольких интерфейсов, не прибегая к программированию в стиле COM. Вы просто создаете тип на основе **implements**, а затем реализуете функции интерфейса. Вот как это сделать.

```cppwinrt
// App.cpp
...
struct App : implements<App, IFrameworkViewSource>
{
    IFrameworkView CreateView()
    {
        return ...
    }
}
...
```

Мы разобрались с **IFrameworkViewSource**. На следующем этапе необходимо вернуть объект, реализующий интерфейс **IFrameworkView** . Вы можете также реализовать этот интерфейс в **App**. Следующий пример кода представляет собой минимальное приложение, которое отображает окно на рабочем столе.

```cppwinrt
// App.cpp
...
struct App : implements<App, IFrameworkViewSource, IFrameworkView>
{
    IFrameworkView CreateView()
    {
        return *this;
    }

    void Initialize(CoreApplicationView const &) {}

    void Load(hstring const&) {}

    void Run()
    {
        CoreWindow window = CoreWindow::GetForCurrentThread();
        window.Activate();

        CoreDispatcher dispatcher = window.Dispatcher();
        dispatcher.ProcessEvents(CoreProcessEventsOption::ProcessUntilQuit);
    }

    void SetWindow(CoreWindow const & window)
    {
        // Prepare app visuals here
    }

    void Uninitialize() {}
};
...
```

Так как **приложение***имеет тип***IFrameworkViewSource**, вы можете просто передать его в **Run**.

```cppwinrt
using namespace Windows::ApplicationModel::Core;
int __stdcall wWinMain(HINSTANCE, HINSTANCE, PWSTR, int)
{
    CoreApplication::Run(winrt::make<App>());
}
```

## <a name="if-youre-authoring-a-runtime-class-in-a-windows-runtime-component"></a>Если вы создаете класс среды выполнения в компоненте среды выполнения Windows
Если ваш тип упакован в компонент среды выполнения Windows для использования из приложения, он должен представлять собой класс среды выполнения.

> [!TIP]
> Мы рекомендуем объявлять каждый класс среды выполнения в отдельном файле (.idl) языка описания интерфейса (IDL), чтобы оптимизировать производительность сборки при редактировании файла IDL, а также для обеспечения логического соответствия файла IDL его сгенерированным файлам с исходным кодом. Visual Studio объединяет все полученные файлы `.winmd` в один файл с тем же именем, что у корневого пространства имен. Этот окончательный файл `.winmd` и будет тем, на который будут ссылаться потребители вашего компонента.

Рассмотрим пример.

```idl
// MyRuntimeClass.idl
namespace MyProject
{
    runtimeclass MyRuntimeClass
    {
        // Declaring a constructor (or constructors) in the IDL causes the runtime class to be
        // activatable from outside the compilation unit.
        MyRuntimeClass();
        String Name;
    }
}
```

Этот файл IDL объявляет класс среды выполнения Windows. Класс среды выполнения — это тип, который можно активировать и использовать через современные интерфейсы COM, обычно через границы исполняемого файла. Когда вы добавите файл IDL в проект и скомпилируете его, цепочка инструментов C++/WinRT (`midl.exe` и `cppwinrt.exe`) создаст для вас тип реализации. В представленном выше примере IDL типом реализации является заглушка структуры C++ под названием **winrt::MyProject::implementation::MyRuntimeClass**, которая размещена в файлах исходного кода с именами `\MyProject\MyProject\Generated Files\sources\MyRuntimeClass.h` и `MyRuntimeClass.cpp`.

Тип реализации выглядит следующим образом.

```cppwinrt
// MyRuntimeClass.h
...
namespace winrt::MyProject::implementation
{
    struct MyRuntimeClass : MyRuntimeClassT<MyRuntimeClass>
    {
        MyRuntimeClass() = default;

        winrt::hstring Name();
        void Name(winrt::hstring const& value);
    };
}

// winrt::MyProject::factory_implementation::MyRuntimeClass is here, too.
```

Обратите внимание, что используется шаблон F-ограниченного полиморфизма (**MyRuntimeClass** использует сам себя в качестве аргумента шаблона для базового **MyRuntimeClassT**). Он также называется странно рекурсивным шаблоном (CRTP). Если следовать по цепочке наследования вверх, мы дойдем до **MyRuntimeClass_base**.

```cppwinrt
template <typename D, typename... I>
struct MyRuntimeClass_base : implements<D, MyProject::IMyRuntimeClass, I...>
```

Поэтому в нашем примере в корне иерархии наследования снова находится шаблон базовой структуры [**winrt::implements**](/uwp/cpp-ref-for-winrt/implements).

Дополнительные сведения, код и пошаговое руководство по созданию API-интерфейсов в компоненте среды выполнения Windows см. в разделе [Создание событий в C++/WinRT](author-events.md#create-a-core-app-bankaccountcoreapp-to-test-the-windows-runtime-component).

## <a name="if-youre-authoring-a-runtime-class-to-be-referenced-in-your-xaml-ui"></a>Если вы создаете класс среды выполнения, указываемый в пользовательском интерфейсе XAML
Если на ваш тип ссылается пользовательский интерфейс XAML, то он должен представлять собой класс среды выполнения, даже если он находится в одном проекте с XAML. Несмотря на то, что классы среды выполнения обычно активируются через границы исполняемого файла, их можно использовать и в той единице компиляции, которая их реализует.

В этом сценарии создаются *и* используются API-интерфейсы. Процедура реализации класса среды выполнения в целом осуществляется так же, как реализация компонента среды выполнения Windows. Поэтому вы можете воспользоваться предыдущим разделом &mdash;[Если вы создаете класса среды выполнения в компоненте среды выполнения Windows](#if-youre-authoring-a-runtime-class-in-a-windows-runtime-component). Единственное отличие состоит в том, что из IDL цепочка инструментов C++/WinRT создает не только тип реализации, но проецируемый тип. Важно понимать, что понятие "**MyRuntimeClass**" может оказаться неоднозначным; существует несколько сущностей различных типов с этим именем.

- **MyRuntimeClass** — это имя класса среды выполнения. Но по сути это абстракция: он объявляется в IDL, а реализуется на каком-либо языке программирования.
- **MyRuntimeClass** — это имя структуры C++ **winrt::MyProject::implementation::MyRuntimeClass**, которая является реализацией класса среды выполнения C++/WinRT. Как мы уже выяснили, если существуют отдельные реализующие и использующие проекты, эта структура существует только в реализующем проекте. Она называется *тип реализации* или просто *реализация*. Этот тип создается средством `cppwinrt.exe` в файлах `\MyProject\MyProject\Generated Files\sources\MyRuntimeClass.h` и `MyRuntimeClass.cpp`.
- **MyRuntimeClass** — это имя проецируемого типа в виде структуры C++ **winrt::MyProject::MyRuntimeClass** . Если существуют отдельные реализующие и использующие проекты, эта структура существует только в использующем проекте. Она называется *тип проекции* или просто *проекция*. Этот тип создается средством `cppwinrt.exe` в файле `\MyProject\MyProject\Generated Files\winrt\impl\MyProject.2.h`.

![Тип проекции и тип реализации](images/myruntimeclass.png)

Здесь представлены элементы типа проекции, имеющие отношение к рассматриваемому вопросу.

```cppwinrt
// MyProject.2.h
...
namespace winrt::MyProject
{
    struct MyRuntimeClass : MyProject::IMyRuntimeClass
    {
        MyRuntimeClass(std::nullptr_t) noexcept {}
        MyRuntimeClass();
    };
}
```

Пошаговое руководство по реализации интерфейса **INotifyPropertyChanged** в классе среды выполнения см. в разделе [Элементы управления XAML; привязка к свойству C++/WinRT](binding-property.md).

Процедура использования класса среды выполнения для этого сценария описывается в разделе [Использование API-интерфейсов в C++/WinRT](consume-apis.md#if-the-api-is-implemented-in-the-consuming-project).

## <a name="runtime-class-constructors"></a>Конструкторы классов среды выполнения
Ниже приведены некоторые важные моменты, которые проиллюстрированы примерами кода выше.

- Каждый конструктор, которые вы объявляете в IDL, приводит к созданию конструктора и в типе реализации, и в типе проекции. Объявленные в IDL конструкторы применяются для использования класса среды выполнения из *другой* единицы компиляции.
- Независимо от наличия объявленного в IDL конструктора, для типа проекции создается перегрузка конструктора, принимающая `nullptr_t`. Вызов конструктора `nullptr_t` — это *первый из двух этапов* использования класса среды выполнения из *той же* единицы компиляции. Дополнительные сведения и пример кода см. в разделе [Использование API-интерфейсов в C++/WinRT](consume-apis.md#if-the-api-is-implemented-in-the-consuming-project).
- Если вы используете класс среды выполнения из *той же* единицы компиляции, то вы также можете реализовать нестандартные конструкторы непосредственно в типе реализации (как мы помним, это `MyRuntimeClass.h`).

> [!NOTE]
> Если предполагается, что класс среды выполнения будет использоваться из другой единицы компиляции (что достаточно распространено), то включите в IDL один или несколько конструкторов (по крайней мере, конструктор по умолчанию). Так вы получите фабричную реализацию вместе с вашим типом реализации.
> 
> Если вы хотите создать и использовать класс среды выполнения только в той же единице компиляции, не объявляйте никакие конструкторы в IDL. Фабричная реализация не требуется, и она не будет создана. Конструктор по умолчанию будет удален из типа реализации, но вы можете создать новый и назначить его конструктором по умолчанию.
> 
> Если вы хотите создать и использовать класс среды выполнения в одной и той же единице компиляции и вам нужны параметры конструктора, создайте необходимые конструкторы напрямую в типе реализации.

## <a name="instantiating-and-returning-implementation-types-and-interfaces"></a>Создание экземпляров, возврат типов реализации и интерфейсов
В этом разделе мы подробно рассмотрим в качестве примера тип реализации с именем **MyType**, который реализует интерфейсы [**IStringable**](/uwp/api/windows.foundation.istringable) и [**IClosable**](/uwp/api/windows.foundation.iclosable).

Вы можете унаследовать **MyType** непосредственно от [**winrt::implements**](/uwp/cpp-ref-for-winrt/implements) (это не класс среды выполнения).

```cppwinrt
#include <winrt/Windows.Foundation.h>

using namespace winrt;
using namespace Windows::Foundation;

struct MyType : implements<MyType, IStringable, IClosable>
{
    winrt::hstring ToString(){ ... }
    void Close(){}
};
```

Вы также можете создать его из IDL (это класс среды выполнения).

```idl
// MyType.idl
namespace MyProject
{
    runtimeclass MyType: Windows.Foundation.IStringable, Windows.Foundation.IClosable
    {
        MyType();
    }    
}
```

Чтобы перейти от **MyType** к объекту **IStringable** или **IClosable**, который можно использовать или вернуть в составе проекции, вызовите шаблон функции [**winrt::make**](/uwp/cpp-ref-for-winrt/make). **make** возвращает интерфейс по умолчанию для типа реализации.

```cppwinrt
IStringable istringable = winrt::make<MyType>();
```

> [!NOTE]
> Однако, если вы ссылаетесь на тип из пользовательского интерфейса XAML, то в одном проекте будут и тип реализации, и тип проекции. В этом случае **make** возвращает экземпляр типа проекции. Пример кода для этого сценария см. в разделе [Элементы управления XAML; привязка к свойству C++/WinRT](binding-property.md#add-a-property-of-type-bookstoreviewmodel-to-mainpage).

Мы можем использовать `istringable` (в примере выше) только для вызова членов интерфейса **IStringable**. Но интерфейс C++/WinRT (представляющий собой проецируемый интерфейс) является производным от [**winrt::Windows::Foundation::IUnknown**](/uwp/cpp-ref-for-winrt/windows-foundation-iunknown). Поэтому можно вызывать [**IUnknown::as**](/uwp/cpp-ref-for-winrt/windows-foundation-iunknown#iunknownas-function) (или [**IUnknown::try_as**](/uwp/cpp-ref-for-winrt/windows-foundation-iunknown#iunknowntry_as-function)) для проецируемого типа или интерфейса для запроса других проецируемых интерфейсов, которые также можно использовать или вернуть вызывающему объекту.

```cppwinrt
istringable.ToString();
IClosable iclosable = istringable.as<IClosable>();
iclosable.Close();
```

Если необходимо получить доступ ко всем членам реализации и затем вернуть интерфейс вызывающему объекту, используйте шаблон функции [**winrt::make_self**](/uwp/cpp-ref-for-winrt/make-self). **make_self** возвращает [**winrt::com_ptr**](/uwp/cpp-ref-for-winrt/com-ptr), который является оболочкой для типа реализации. Можно получить доступ к членам всех его интерфейсов (с помощью оператора косвенного обращения), вернуть его в вызывающий объект в исходном виде, или вызвать для него **as** и вернуть вызывающему объекту полученный объект интерфейса.

```cppwinrt
winrt::com_ptr<MyType> myimpl = winrt::make_self<MyType>();
myimpl->ToString();
myimpl->Close();
IClosable iclosable = myimpl.as<IClosable>();
iclosable.Close();
```

Класс **MyType** не является частью проекции; он представляет собой реализацию. Однако таким образом можно вызвать его методы реализации напрямую, без дополнительного вызова виртуальной функции. В примере выше **MyType::ToString** использует ту же подпись, что и проецируемый метод в **IStringable**, но мы вызываем невиртуальный метод напрямую, без пересечения границы двоичного интерфейса приложения (ABI). **Com_ptr** просто содержит указатель на структуру **MyType**, поэтому вы можете получить доступ к любым внутренним сведениям **MyType** через переменную `myimpl` и оператор косвенного обращения.

В случае, когда у вас есть объект интерфейса, который гарантированно является интерфейсом в вашей реализации, вы можете вернуться к реализации с помощью шаблона функции [**from_abi**](/uwp/cpp-ref-for-winrt/get-self). Этот метод также позволяет избежать вызовов виртуальных функций и перейти непосредственно к реализации.

> [!NOTE]
> Если вы еще не установили пакет Windows SDK версии 10.0.17763.0 (Windows 10, версия 1809) или более поздней версии, затем необходимо вызвать [ **winrt::from_abi** ](/uwp/cpp-ref-for-winrt/from-abi) вместо [ **winrt::get_ SELF**](/uwp/cpp-ref-for-winrt/get-self).

Рассмотрим пример. Еще один пример вы найдете в статье о [реализации пользовательского класса управления **BgLabelControl**](xaml-cust-ctrl.md#implement-the-bglabelcontrol-custom-control-class).

```cppwinrt
void ImplFromIClosable(IClosable const& from)
{
    MyType* myimpl = winrt::get_self<MyType>(from);
    myimpl->ToString();
    myimpl->Close();
}
```

Но ссылку сохраняет только исходный объект интерфейса. Если *вам* она тоже нужна, можно вызвать [**com_ptr::copy_from**](/uwp/cpp-ref-for-winrt/com-ptr#com_ptrcopy_from-function).

```cppwinrt
winrt::com_ptr<MyType> impl;
impl.copy_from(winrt::get_self<MyType>(from));
// com_ptr::copy_from ensures that AddRef is called.
```

Тип реализации сам по себе не является производным от **winrt::Windows::Foundation::IUnknown**, поэтому не имеет функции **as**. Но вы можете создать его экземпляр и получить доступ к членам всех его интерфейсов. Но в этом случае не следует возвращать необработанный экземпляр типа реализации вызывающему объекту. Вместо этого используйте один из показанных выше методов и возвращайте проецируемый интерфейс или **com_ptr**.

```cppwinrt
MyType myimpl;
myimpl.ToString();
myimpl.Close();
IClosable ic1 = myimpl.as<IClosable>(); // error
```

Если у вас есть экземпляр типа реализации и вам необходимо передать его функции, которая ожидает соответствующий тип проекции, это можно сделать. Существует оператор преобразования для типа реализации (при условии, что тип реализации был создан с помощью инструмента `cppwinrt.exe`), который делает это возможным. Значение типа реализации можно передать непосредственно в метод, который ожидает значение соответствующего типа проецируемых. Значение типа реализации можно передать непосредственно в метод `*this`, который ожидает значение соответствующего типа проекции.

```cppwinrt
// MyProject::MyType is the projected type; the implementation type would be MyProject::implementation::MyType.

void MyOtherType::DoWork(MyProject::MyType const&){ ... }

...

void FreeFunction(MyProject::MyOtherType const& ot)
{
    MyType myimpl;
    ot.DoWork(myimpl);
}

...

void MyType::MemberFunction(MyProject::MyOtherType const& ot)
{
    ot.DoWork(*this);
}
```

## <a name="deriving-from-a-type-that-has-a-non-default-constructor"></a>Наследование от типа, имеющего нестандартный конструктор
[**ToggleButtonAutomationPeer::ToggleButtonAutomationPeer(ToggleButton)** ](/uwp/api/windows.ui.xaml.automation.peers.togglebuttonautomationpeer.-ctor#Windows_UI_Xaml_Automation_Peers_ToggleButtonAutomationPeer__ctor_Windows_UI_Xaml_Controls_Primitives_ToggleButton_) является примером нестандартного конструктора. Конструктора по умолчанию здесь не существует, поэтому для создания **ToggleButtonAutomationPeer** нужно передать *owner*. Следовательно, для наследования от **ToggleButtonAutomationPeer** необходимо предоставить конструктор, который принимает *owner* и передает его базовому объекту. Давайте посмотрим, как это выглядит на практике.

```idl
// MySpecializedToggleButton.idl
namespace MyNamespace
{
    runtimeclass MySpecializedToggleButton :
        Windows.UI.Xaml.Controls.Primitives.ToggleButton
    {
        ...
    };
}
```

```idl
// MySpecializedToggleButtonAutomationPeer.idl
namespace MyNamespace
{
    runtimeclass MySpecializedToggleButtonAutomationPeer :
        Windows.UI.Xaml.Automation.Peers.ToggleButtonAutomationPeer
    {
        MySpecializedToggleButtonAutomationPeer(MySpecializedToggleButton owner);
    };
}
```

Создаваемый конструктор для типа реализации выглядит следующим образом.

```cppwinrt
// MySpecializedToggleButtonAutomationPeer.cpp
...
MySpecializedToggleButtonAutomationPeer::MySpecializedToggleButtonAutomationPeer
    (MyNamespace::MySpecializedToggleButton const& owner)
{
    ...
}
...
```

Осталось только одно — передать этот параметр конструктора базовому классу. Помните шаблон F-ограниченного полиморфизма, который мы упомянули выше? Когда вы разберетесь с описанием этого шаблона в том виде, в котором он используется в C++/WinRT, вы можете понять, как называется ваш базовый класс (также это имя можно просто посмотреть в файле заголовка класса реализации). Здесь описано, как вызвать конструктор базового класса в этом случае.

```cppwinrt
// MySpecializedToggleButtonAutomationPeer.cpp
...
MySpecializedToggleButtonAutomationPeer::MySpecializedToggleButtonAutomationPeer
    (MyNamespace::MySpecializedToggleButton const& owner) : 
    MySpecializedToggleButtonAutomationPeerT<MySpecializedToggleButtonAutomationPeer>(owner)
{
    ...
}
...
```

Конструктор базового класса ожидает **ToggleButton**. При этом **MySpecializedToggleButton** *имеет тип* **ToggleButton**.

До внесения изменений, описанных выше (для передачи параметра конструктора базовому классу), компилятор пометит конструктор укажет на отсутствие подходящего конструктора по умолчанию, доступного для типа под названием (в данном случае) **MySpecializedToggleButtonAutomationPeer_base&lt;MySpecializedToggleButtonAutomationPeer&gt;** . Фактически, это базовый класс базового класса вашего типа реализации.

## <a name="important-apis"></a>Важные API
* [шаблон структуры winrt::com_ptr](/uwp/cpp-ref-for-winrt/com-ptr)
* [функция winrt::com_ptr::copy_from](/uwp/cpp-ref-for-winrt/com-ptr#com_ptrcopy_from-function)
* [шаблон функции winrt::from_abi](/uwp/cpp-ref-for-winrt/from-abi)
* [шаблон функции winrt::get_self](/uwp/cpp-ref-for-winrt/get-self)
* [шаблон структуры winrt::implements](/uwp/cpp-ref-for-winrt/implements)
* [шаблон функции winrt::make](/uwp/cpp-ref-for-winrt/make)
* [шаблон функции winrt::make_self](/uwp/cpp-ref-for-winrt/make-self)
* [функция winrt::Windows::Foundation::IUnknown::as](/uwp/cpp-ref-for-winrt/windows-foundation-iunknown#iunknownas-function)

## <a name="related-topics"></a>Статьи по теме
* [Использование интерфейсов API с помощью C++/WinRT](consume-apis.md)
* [Элементы управления XAML; привязка к свойству C++/WinRT](binding-property.md#add-a-property-of-type-bookstoreviewmodel-to-mainpage)

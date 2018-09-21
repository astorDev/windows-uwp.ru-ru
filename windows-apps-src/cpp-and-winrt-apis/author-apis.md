---
author: stevewhims
description: В этом разделе показано, как создавать API-интерфейсы C++/ WinRT, используя базовую структуру **winrt::implements** прямо или косвенно.
title: Создание API-интерфейсов с помощью C++/WinRT
ms.author: stwhi
ms.date: 05/07/2018
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: Windows 10, uwp, стандартная, c++, cpp, winrt, проецируемый, проекция, реализация, реализовывать, класс среды выполнения, активация
ms.localizationpriority: medium
ms.openlocfilehash: 051c24e0acc645150f4ca7ff74480f7de3ce456b
ms.sourcegitcommit: 4f6dc806229a8226894c55ceb6d6eab391ec8ab6
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/20/2018
ms.locfileid: "4090377"
---
# <a name="author-apis-with-cwinrtwindowsuwpcpp-and-winrt-apisintro-to-using-cpp-with-winrt"></a>Создание API-интерфейсов с помощью [C++/WinRT](/windows/uwp/cpp-and-winrt-apis/intro-to-using-cpp-with-winrt)

> [!NOTE]
> **Некоторые сведения относятся к предварительным версиям продуктов, в которые перед коммерческим выпуском могут быть внесены существенные изменения. Майкрософт не дает никаких гарантий, явных или подразумеваемых, в отношении предоставленной здесь информации.**

В этом разделе показано, как создавать API-интерфейсы C++/ WinRT, используя базовую структуру [**winrt::implements**](/uwp/cpp-ref-for-winrt/implements) прямо или косвенно. Синонимами для *создавать* в данном контексте являются понятия *производить* и *реализовывать*. В этом разделе рассматриваются следующие сценарии реализации интерфейсов API на C++/WinRT в указанном порядке.

- Вы *не* разрабатываете класс среды выполнения Windows; а просто реализуете один или нескольких интерфейсов среды выполнения Windows для локального использования в вашем приложении. В этом случае вы создаете производный класс непосредственно от **winrt::implements** и реализуете функции.
- Вы *создаете* класс среды выполнения. Возможно, вы разрабатываете компонент для использования в приложении. А возможно, вы создаете тип для использования из пользовательского интерфейса XAML, и в этом случае вы одновременно реализуете и используете класс среды выполнения в рамках одной и той же единицы компиляции. В этих случаях вы позволяете инструментам создавать для вас классы, производные от **winrt::implements **.

В обоих случаях тип, реализующий ваши API-интерфейсы C++/ WinRT, называется *типом реализации*.

> [!IMPORTANT]
> Очень важно различать понятия типа реализации и типа проекции. Тип проекции описан в статье [Использование API-интерфейсов в C++/WinRT ](consume-apis.md).

## <a name="if-youre-not-authoring-a-runtime-class"></a>Если вы *не* создаете класс среды выполнения
В самом простом сценарии вы реализуете интерфейс среды выполнения Windows для локального использования. Вам не нужен класс среды выполнения; а нужен обычный класс C++. Например, возможно, что вы создаете приложение на основе [**CoreApplication**](/uwp/api/windows.applicationmodel.core.coreapplication).

> [!NOTE]
> Сведения об установке и использовании расширения C++/WinRT для Visual Studio (VSIX) (которое обеспечивает поддержку шаблона проекта, а также свойств и целевых объектов MSBuild C++/WinRT) см. в разделе [Поддержка Visual Studio для C++/WinRT и VSIX](intro-to-using-cpp-with-winrt.md#visual-studio-support-for-cwinrt-and-the-vsix).

В Visual Studio, **Visual C++** > **Универсальные** > **приложения основных компонентов (C + +/ WinRT)** шаблон **CoreApplication** иллюстрирует шаблон проекта. Шаблон начинается с передачи реализации [**Windows::ApplicationModel::Core::IFrameworkViewSource**](/uwp/api/windows.applicationmodel.core.iframeworkviewsource) в [**CoreApplication::Run**](/uwp/api/windows.applicationmodel.core.coreapplication.run).

```cppwinrt
using namespace Windows::ApplicationModel::Core;
int __stdcall wWinMain(HINSTANCE, HINSTANCE, PWSTR, int)
{
    IFrameworkViewSource source = ...
    CoreApplication::Run(source);
}
```

**CoreApplication** использует интерфейс для создания первого представления приложения. Концептуально **IFrameworkViewSource** выглядит следующим образом.

```cppwinrt
struct IFrameworkViewSource : IInspectable
{
    IFrameworkView CreateView();
};
```

И так же концептуально реализация **CoreApplication::Run** делает следующее.

```cppwinrt
void Run(IFrameworkViewSource viewSource) const
{
    IFrameworkView view = viewSource.CreateView();
    ...
}
```

Поэтому вы, как разработчик, реализуете интерфейс **IFrameworkViewSource**. C++/WinRT имеет шаблон базовой структуры [**winrt::implements**](/uwp/cpp-ref-for-winrt/implements), чтобы упростить реализацию одного или нескольких интерфейсов, не прибегая к программированию в стиле COM. Вы просто создаете тип на основе **implements**, а затем реализуйте функции интерфейса. Вот как это сделать.

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

Так как тип вашего **App** *— * **IFrameworkViewSource**, вы можете просто передать его в **Run**.

```cppwinrt
using namespace Windows::ApplicationModel::Core;
int __stdcall wWinMain(HINSTANCE, HINSTANCE, PWSTR, int)
{
    CoreApplication::Run(App{});
}
```

## <a name="if-youre-authoring-a-runtime-class-in-a-windows-runtime-component"></a>Если вы создаете класс среды выполнения в компоненте среды выполнения Windows
Если ваш тип упакован в компонент среды выполнения Windows для использования из приложения, он должен представлять собой класс среды выполнения.

> [!TIP]
> Мы рекомендуем объявлять каждый класс среды выполнения в отдельном файле (.idl) языка описания интерфейса (IDL), чтобы оптимизировать производительность сборки при редактировании файла IDL, а также для обеспечения логического соответствия файла IDL его сгенерированным файлам с исходным кодом. Visual Studio объединяет все полученные файлы `.winmd` в один файл с тем же именем, что у корневого пространства имен. Этот окончательный файл `.winmd` и будет тем, на который будут ссылаться потребители вашего компонента.

Вот пример.

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

Этот файл IDL объявляет класс среды выполнения Windows. Класс среды выполнения — это тип, который можно активировать и использовать через современные интерфейсы COM, обычно через границы исполняемого файла. При добавлении файла IDL в проект и его сборке, цепочка инструментов C++/WinRT (`midl.exe` и `cppwinrt.exe`) создает для вас тип реализации. В примере IDL выше тип реализации — это заглушка структуры C++ под названием **winrt::MyProject::implementation::MyRuntimeClass** в файлах исходного кода с именем `\MyProject\MyProject\Generated Files\sources\MyRuntimeClass.h` и `MyRuntimeClass.cpp`.

Тип реализации выглядит следующим образом.

```cppwinrt
// MyRuntimeClass.h
...
namespace winrt::MyProject::implementation
{
    struct MyRuntimeClass : MyRuntimeClassT<MyRuntimeClass>
    {
        MyRuntimeClass() = default;

        hstring Name();
        void Name(hstring const& value);
    };
}

// winrt::MyProject::factory_implementation::MyRuntimeClass is here, too.
```

Обратите внимание, что используется шаблон F-ограниченного полиморфизма (**MyRuntimeClass** использует сам себя в качестве аргумента шаблона для базового **MyRuntimeClassT**). Он также называется странно рекурсивным шаблоном (CRTP). Если следовать по цепочке наследования вверх, мы дойдем до **MyRuntimeClass_base**.

```cppwinrt
template <typename D, typename... I>
struct MyRuntimeClass_base : implements<D, MyProject::IMyRuntimeClass, I...>
```

Поэтому в данном сценарии в корне иерархии наследования снова находится шаблон базовой структуры [**winrt::implements**](/uwp/cpp-ref-for-winrt/implements).

Дополнительные сведения, код и пошаговое руководство по созданию API-интерфейсов в компоненте среды выполнения Windows см. в разделе [Создание событий в C++/WinRT](author-events.md#create-a-core-app-bankaccountcoreapp-to-test-the-windows-runtime-component).

## <a name="if-youre-authoring-a-runtime-class-to-be-referenced-in-your-xaml-ui"></a>Если вы создаете класс среды выполнения, указываемый в пользовательском интерфейсе XAML
Если на ваш тип ссылается пользовательский интерфейс XAML, то он должен представлять собой класс среды выполнения, даже если он находится в одном проекте с XAML. Несмотря на то, что классы среды выполнения обычно активируются через границы исполняемого файла, их можно использовать в единице компиляции, которая их реализует.

В этом сценарии создаются *и* используются API-интерфейсы. Процедура реализации класса среды выполнения в целом осуществляется так же, как реализация компонента среды выполнения Windows. Поэтому см. предыдущий раздел &mdash; Если вы создаете класса среды выполнения в компоненте среды выполнения Windows[](#if-youre-authoring-a-runtime-class-in-a-windows-runtime-component). Единственная отличающаяся деталь состоит в том, что из IDL цепочка инструментов C++/WinRT создает не только тип реализации, но проецируемый тип. Важно понимать, что в этом сценарии понятие "**MyRuntimeClass**" может оказаться неоднозначным; существует несколько сущностей различных типов с этим именем.

- **MyRuntimeClass** — это имя класса среды выполнения. Но в действительности это абстракция: объявление производится в IDL, а реализация — в каком-либо языке программирования.
- **MyRuntimeClass** — это имя структуры C++ **winrt::MyProject::implementation::MyRuntimeClass**, которая является реализацией класса среды выполнения C++/WinRT. Как мы выяснили, если существуют отдельные реализующие и использующие проекты, эта структура существует только в реализующем проекте. Это *тип реализации* или *реализация*. Этот тип создается (инструментом `cppwinrt.exe`) в файлах `\MyProject\MyProject\Generated Files\sources\MyRuntimeClass.h` и `MyRuntimeClass.cpp`.
- **MyRuntimeClass** — это имя проецируемого типа в виде структуры C++ **winrt::MyProject::MyRuntimeClass **. Если существуют отдельные реализующие и использующие проекты, эта структура существует только в использующем проекте. Это *тип проекции* или *проекция*. Этот тип создается (с помощью `cppwinrt.exe`) в файле `\MyProject\MyProject\Generated Files\winrt\impl\MyProject.2.h`.

![Тип проекции и тип реализации](images/myruntimeclass.png)

Ниже приведены части проецируемого типа, имеющие отношение к данной теме.

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

Процедура использования вашего класса среды выполнения в этом сценарии описывается в разделе [Использование API-интерфейсов в C++/WinRT](consume-apis.md#if-the-api-is-implemented-in-the-consuming-project).

## <a name="runtime-class-constructors"></a>Конструкторы классов среды выполнения
Ниже приведены некоторые пункты, которые следует учесть в примерах кода, приведенных выше.

- Каждый конструктор, которые вы объявляете в IDL приводит к созданию конструктора и в типе реализации, и в типе проекции. Объявленные в IDL конструкторы применяются для использования класса среды выполнения из *другой* единицы компиляции.
- Независимо от наличия у вас объявленного в IDL конструктора для типа проекции создается перегрузка конструктора, принимающая `nullptr_t`. Вызов конструктора `nullptr_t` — это *первый из двух этапов* использования класс среды выполнения из *той же* единицы компиляции. Дополнительные сведения и пример кода см. в разделе [Использование API-интерфейсов в C++/WinRT](consume-apis.md#if-the-api-is-implemented-in-the-consuming-project).
- Если вы используете класс среды выполнения из *той же* единицы компиляции, то вы также можете реализовать нестандартные конструкторы непосредственно в типе реализации (как мы помним, это `MyRuntimeClass.h`).

> [!NOTE]
> Если предполагается, что ваш класс среды выполнения будет использоваться из другой единицы компиляции (что достаточно распространено), то включите конструктор(ы) в вашем IDL (по крайней мере, конструктор по умолчанию). Сделав это, вы также получите фабричную реализацию вместе с вашим типом реализации.
> 
> Если вы хотите создать и использовать класс среды выполнения только в одной единице компиляции, не объявляйте никакие конструкторы в IDL. Фабричная реализация не требуется, и она не будет создана. Конструктор по умолчанию типа реализации будет удален, но вместо этого вы можете легко внести в него изменения и вернуть его к виду по умолчанию.
> 
> Если вы хотите создать и использовать класс среды выполнения в одной и той же единице компиляции и вам нужны параметры конструктора, создайте необходимые конструкторы напрямую в типе реализации.

## <a name="instantiating-and-returning-implementation-types-and-interfaces"></a>Создание экземпляров и возврат типов и интерфейсов реализации
В этом разделе мы подробно рассмотрим в качестве примера тип реализации с именем **MyType**, который реализует интерфейсы [**IStringable**](/uwp/api/windows.foundation.istringable) и [**IClosable**](/uwp/api/windows.foundation.iclosable).

Вы можете выполнить наследование **MyType** непосредственно из [**winrt::implements**](/uwp/cpp-ref-for-winrt/implements) (это не класс среды выполнения).

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

Или вы можете создать его из IDL (это класс среды выполнения).

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

Чтобы перейти от **MyType** к объекту **IStringable** или **IClosable**, который можно использовать или вернуть в качестве части вашей проекции, можно вызвать шаблон функции [**winrt::make**](/uwp/cpp-ref-for-winrt/make) шаблона функции. **сделать** возвращает интерфейс по умолчанию типа реализации.

```cppwinrt
IStringable istringable = winrt::make<MyType>();
```

> [!NOTE]
> Однако если вы ссылаетесь на тип из пользовательского интерфейса XAML, то в одном проекте будут и тип реализации, и тип проекции. В этом случае **сделать** возвращает экземпляр проецируемого типа. Пример кода для этого сценария см. в разделе [Элементы управления XAML; привязка к свойству C++/WinRT](binding-property.md#add-a-property-of-type-bookstoreviewmodel-to-mainpage).

Мы можем использовать `istringable` (в примере выше) только для вызова членов интерфейса **IStringable**. Но интерфейс C++/WinRT (представляющий собой проецируемый интерфейс) является производным от [**winrt::Windows::Foundation::IUnknown**](/uwp/cpp-ref-for-winrt/windows-foundation-iunknown). Таким образом можно вызвать [**IUnknown::as**](/uwp/cpp-ref-for-winrt/windows-foundation-iunknown#iunknownas-function) (или [**IUnknown::try_as**](/uwp/cpp-ref-for-winrt/windows-foundation-iunknown#iunknowntryas-function)) на нем для запроса для других проецируемых типов и интерфейсов, которые вы можете также использовать или вернуть.

```cppwinrt
istringable.ToString();
IClosable iclosable = istringable.as<IClosable>();
iclosable.Close();
```

Если необходимо получить доступ ко всем членам реализации и затем вернуть интерфейс вызывающему объекту, используйте шаблон функции [**winrt::make_self**](/uwp/cpp-ref-for-winrt/make-self). **make_self** возвращает [**winrt::com_ptr**](/uwp/cpp-ref-for-winrt/com-ptr), в который упакован тип реализации. Можно получить доступ к членам всех его интерфейсов (с помощью оператора косвенного обращения), вернуть его в вызывающий объект в исходном виде, или вызвать для него **as** и вернуть вызывающему объекту получившийся объект интерфейса.

```cppwinrt
winrt::com_ptr<MyType> myimpl = winrt::make_self<MyType>();
myimpl->ToString();
myimpl->Close();
IClosable iclosable = myimpl.as<IClosable>();
iclosable.Close();
```

Класс **MyType** не является частью проекции; он представляет собой реализацию. Однако таким образом можно вызвать его методы реализации напрямую, без необходимости вызова виртуальной функции. В примере выше, даже если **MyType::ToString** использует ту же подпись, что и проецируемый метод в **IStringable**, мы вызываем невиртуальный метод напрямую, без пересечения границы двоичного интерфейса приложения (ABI). **Com_ptr** просто содержит указатель на структуру **MyType**, поэтому можно также получить доступ к любым внутренним сведениям **MyType** через переменную `myimpl` и оператор косвенного обращения.

В случае, когда у вас есть объект интерфейса, и вам известно, что это интерфейс в вашей реализации, вы можете вернуться к реализации с помощью шаблона функции [**from_abi**](/uwp/cpp-ref-for-winrt/from-abi). Опять же, это метод, который позволяет избежать вызовов виртуальных функций и перейти непосредственно к реализации.

> [!NOTE]
> Если вы установили [Windows 10 SDK предварительную сборку 17661 пакета](https://www.microsoft.com/software-download/windowsinsiderpreviewSDK)или более поздней версии, затем вы можете вызвать [**winrt::get_self**](/uwp/cpp-ref-for-winrt/get-self) вместо [**winrt::from_abi**](/uwp/cpp-ref-for-winrt/from-abi).

Вот пример. Существует еще один пример в [Реализуйте класс пользовательского элемента управления **BgLabelControl** ](xaml-cust-ctrl.md#implement-the-bglabelcontrol-custom-control-class).

```cppwinrt
void ImplFromIClosable(IClosable const& from)
{
    MyType* myimpl = winrt::from_abi<MyType>(from);
    myimpl->ToString();
    myimpl->Close();
}
```

Однако только исходный объект интерфейса сохраняет ссылку. Если *вы* хотите сохранить его, можно вызвать [**com_ptr::copy_from**](/uwp/cpp-ref-for-winrt/com-ptr#comptrcopyfrom-function).

```cppwinrt
winrt::com_ptr<MyType> impl;
impl.copy_from(winrt::from_abi<MyType>(from));
// com_ptr::copy_from ensures that AddRef is called.
```

Тип реализации сам по себе не является производным от **winrt::Windows::Foundation::IUnknown**, поэтому он не имеет функции **as**. Несмотря на это, вы можете создать его экземпляр и получить доступ к членам всех его интерфейсов. Если вы так сделаете, не возвращайте необработанный экземпляр типа реализации вызывающему объекту. Вместо этого используйте один из показанных выше методов и возвращайте проецируемый интерфейс или **com_ptr**.

```cppwinrt
MyType myimpl;
myimpl.ToString();
myimpl.Close();
IClosable ic1 = myimpl.as<IClosable>(); // error
```

Если у вас есть экземпляр типа реализации и вам необходимо передать его функции, которая ожидает соответствующий тип проекции, это можно сделать. Существует оператор преобразования для типа реализации (при условии, что тип реализации был создан с `cppwinrt.exe` средство), делает это возможным.

## <a name="deriving-from-a-type-that-has-a-non-trivial-constructor"></a>Получение производного от типа, имеющего нестандартный конструктор
[**ToggleButtonAutomationPeer::ToggleButtonAutomationPeer(ToggleButton)**](/uwp/api/windows.ui.xaml.automation.peers.togglebuttonautomationpeer.-ctor#Windows_UI_Xaml_Automation_Peers_ToggleButtonAutomationPeer__ctor_Windows_UI_Xaml_Controls_Primitives_ToggleButton_) является примером нестандартного конструктора. Конструктора по умолчанию не существует, поэтому для создания **ToggleButtonAutomationPeer**, нужно передать *owner*. Следовательно, в случае наследования от **ToggleButtonAutomationPeer** необходимо предоставить конструктор, который принимает *owner* и передает его базовому объекту. Давайте посмотрим, как это выглядит на практике.

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

Единственная отсутствующая деталь заключается в необходимости передать этот параметр конструктора базовому классу. Помните шаблон F-ограниченного полиморфизма, который мы упомянули выше? После ознакомления со сведениями о этого шаблона в том виде, в котором он используется в C++/WinRT, вы можете понять, как называется ваш базовый класс (или просто посмотреть в файле заголовка класса реализации). Вот как в вызвать конструктор базового класса в этом случае.

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

Конструктор базового класса ожидает **ToggleButton**. А **MySpecializedToggleButton** *и представляет собой* **ToggleButton**.

До внесения изменений, описанных выше (для передачи параметра конструктора базовому классу), компилятор пометит конструктор укажет на отсутствие подходящего конструктора по умолчанию, доступного для типа под названием (в данном случае) **MySpecializedToggleButtonAutomationPeer_base&lt;MySpecializedToggleButtonAutomationPeer&gt;**. Фактически, это базовый класс базового класса вашего типа реализации.

## <a name="important-apis"></a>Важные API
* [Шаблон структуры winrt::com_ptr](/uwp/cpp-ref-for-winrt/com-ptr)
* [функция WinRT::com_ptr::copy_from](/uwp/cpp-ref-for-winrt/com-ptr#comptrcopyfrom-function)
* [Шаблон функции winrt::from_abi](/uwp/cpp-ref-for-winrt/from-abi)
* [Шаблон функции WinRT::get_self](/uwp/cpp-ref-for-winrt/get-self)
* [Шаблон структуры winrt::implements](/uwp/cpp-ref-for-winrt/implements)
* [Шаблон функции winrt::make](/uwp/cpp-ref-for-winrt/make)
* [Шаблон функции winrt::make_self](/uwp/cpp-ref-for-winrt/make-self)
* [Функция winrt::Windows::Foundation::IUnknown::as](/uwp/cpp-ref-for-winrt/windows-foundation-iunknown#iunknownas-function)

## <a name="related-topics"></a>Статьи по теме
* [Использование API-интерфейсов в C++/WinRT](consume-apis.md)
* [Элементы управления XAML; привязка к свойству C++/WinRT](binding-property.md#add-a-property-of-type-bookstoreviewmodel-to-mainpage)

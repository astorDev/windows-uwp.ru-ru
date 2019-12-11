---
description: В этом разделе показано, как создавать интерфейсы API C++/ WinRT, используя базовую структуру **winrt::implements** прямо или косвенно.
title: Создание интерфейсов API с помощью C++/WinRT
ms.date: 07/08/2019
ms.topic: article
keywords: Windows 10, uwp, стандартная, c++, cpp, winrt, проецируемый, проекция, реализация, реализовывать, класс среды выполнения, активация
ms.localizationpriority: medium
ms.openlocfilehash: 84c0e9315950541e51bf49f5c0eec370f3188c4d
ms.sourcegitcommit: 58f6643510a27d6b9cd673da850c191ee23b813e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/03/2019
ms.locfileid: "74701488"
---
# <a name="author-apis-with-cwinrt"></a>Создание интерфейсов API с помощью C++/WinRT

В этом разделе показано, как создавать интерфейсы API [C++/ WinRT](/windows/uwp/cpp-and-winrt-apis/intro-to-using-cpp-with-winrt), используя базовую структуру [**winrt::implements**](/uwp/cpp-ref-for-winrt/implements) прямо или косвенно. Для термина *создавать* в этом контексте являются синонимами *производить* и *реализовывать*. В этом разделе рассматриваются следующие сценарии реализации интерфейсов API на C++/WinRT в указанном порядке.

> [!NOTE]
> В этом разделе описаны компоненты среды выполнения Windows, но только в контексте C++/WinRT. См. описание [компонентов среды выполнения Windows](/windows/uwp/winrt-components/), которые поддерживают все соответствующие языки.

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

Если ваш тип упакован в компонент среды выполнения Windows для использования из приложения, то он должен представлять собой класс среды выполнения. Вам нужно объявить класс среды выполнения в файле языка Microsoft Interface Definition Language (IDL) (.idl) (см. раздел о [разделении классов среды выполнения на файлы Midl (.idl)](#factoring-runtime-classes-into-midl-files-idl)).

Каждый файл IDL создает файл `.winmd`, которые Visual Studio объединяет в один файл с именем корневого пространства имен. Этот окончательный файл `.winmd` и будет тем, на который будут ссылаться потребители вашего компонента.

Ниже приведен пример объявления класса среды выполнения в файле IDL.

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

Этот файл IDL объявляет класс среды выполнения Windows. Класс среды выполнения — это тип, который можно активировать и использовать через современные интерфейсы COM, обычно через границы исполняемого файла. Когда вы добавите файл IDL в проект и скомпилируете его, цепочка инструментов C++/WinRT (`midl.exe` и `cppwinrt.exe`) создаст для вас тип реализации. Пример рабочего процесса файла IDL см. в разделе [Элементы управления XAML; привязка к свойству C++/WinRT](binding-property.md).

В представленном выше примере IDL типом реализации является заглушка структуры C++ под названием **winrt::MyProject::implementation::MyRuntimeClass**, которая размещена в файлах исходного кода с именами `\MyProject\MyProject\Generated Files\sources\MyRuntimeClass.h` и `MyRuntimeClass.cpp`.

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

## <a name="factoring-runtime-classes-into-midl-files-idl"></a>Разделение классов среды выполнения на файлы Midl (.idl)

Шаблоны проектов и элементов Visual Studio создают отдельный файл IDL для каждого класса среды выполнения. Это позволяет установить логическое соответствие между файлом IDL и созданными им файлами исходного кода.

Но если вы консолидируете все классы среды выполнения проекта в один файл IDL, это может значительно ускорить сборку. Если вам нужно сохранить сложные (или циклические) зависимости инструкций `import` между ними, консолидация может даже требоваться. Кроме того, это может вам упростить подготовку и проверку классов среды выполнения.

## <a name="runtime-class-constructors"></a>Конструкторы классов среды выполнения

Ниже приведены некоторые важные моменты, которые проиллюстрированы примерами кода выше.

- Каждый конструктор, которые вы объявляете в IDL, приводит к созданию конструктора и в типе реализации, и в типе проекции. Объявленные в IDL конструкторы применяются для использования класса среды выполнения из *другой* единицы компиляции.
- Независимо от наличия объявленного в IDL конструктора, для типа проекции создается перегрузка конструктора, принимающая **std::nullptr_t**. Вызов конструктора **std::nullptr_t** — это *первый из двух этапов* использования класса среды выполнения из *той же* единицы компиляции. Дополнительные сведения и пример кода см. в разделе [Использование API-интерфейсов в C++/WinRT](consume-apis.md#if-the-api-is-implemented-in-the-consuming-project).
- Если вы используете класс среды выполнения из *той же* единицы компиляции, то вы также можете реализовать нестандартные конструкторы непосредственно в типе реализации (как мы помним, это `MyRuntimeClass.h`).

> [!NOTE]
> Если предполагается, что класс среды выполнения будет использоваться из другой единицы компиляции (что достаточно распространено), то включите в IDL один или несколько конструкторов (по крайней мере, конструктор по умолчанию). Так вы получите фабричную реализацию вместе с вашим типом реализации.
> 
> Если вы хотите создать и использовать класс среды выполнения только в той же единице компиляции, не объявляйте никакие конструкторы в IDL. Фабричная реализация не требуется, и она не будет создана. Конструктор по умолчанию будет удален из типа реализации, но вы можете создать новый и назначить его конструктором по умолчанию.
> 
> Если вы хотите создать и использовать класс среды выполнения в одной и той же единице компиляции и вам нужны параметры конструктора, создайте необходимые конструкторы напрямую в типе реализации.

## <a name="runtime-class-methods-properties-and-events"></a>Методы, свойства и события класса среды выполнения

Мы увидели, что рабочий процесс использует IDL для объявления класса среды выполнения и его членов, а затем средства создают прототипы и заглушки для вас. Вы *можете* изменять прототипы, автоматически генерируемые для членов класса среды выполнения, чтобы они передавались в разные типы из типов, которые вы объявили в IDL. Но это можно сделать, только если тип, который объявляется в IDL, может передаваться типу, объявленному в реализованной версии.

Вот несколько примеров.

- Типы параметров можно опускать. Например, если в IDL ваш метод принимает **SomeClass**, вы можете изменить его на **IInspectable** в реализации. Это возможно благодаря тому, что все экземпляры **SomeClass** могут передаваться **IInspectable** (это однонаправленное действие).
- Вы можете принять параметр, который можно скопировать, по значению, а не по ссылке. Например измените `SomeClass` на `SomeClass const&`. Это нужно сделать, чтобы предотвратить запись ссылки в сопрограмме (см. в руководство по [передаче параметров](/windows/uwp/cpp-and-winrt-apis/concurrency#parameter-passing)).
- Полученное значение можно опускать. Например, можно изменить **void** на [**winrt::fire_and_forget**](/uwp/cpp-ref-for-winrt/fire-and-forget).

Это важно в ситуации, когда вы создаете асинхронный обработчик событий.

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

Тип реализации нельзя выделить непосредственно.

```cppwinrt
MyType myimpl; // error C2259: 'MyType': cannot instantiate abstract class
```

Однако вы можете перейти от **MyType** к объекту **IStringable** или **IClosable**, который можно использовать или вернуть в составе проекции, вызвав шаблон функции [**winrt::make**](/uwp/cpp-ref-for-winrt/make). **make** возвращает интерфейс по умолчанию для типа реализации.

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

Тип реализации сам по себе не является производным от **winrt::Windows::Foundation::IUnknown**, поэтому не имеет функции **as**. Тем не менее, как вы видите в функции **ImplFromIClosable** выше, вы можете получить доступ ко всем участникам его интерфейсов. Однако в этом случае они не будут возвращать вызывающему объекту необработанный экземпляр типа реализации. Вместо этого используйте один из уже показанных методов и возвратите проецируемый интерфейс или **com_ptr**.

Если у вас есть экземпляр типа реализации и вам необходимо передать его функции, которая ожидает соответствующий тип проекции, это можно сделать, следуя примеру кода ниже. Существует оператор преобразования для типа реализации (при условии, что тип реализации был создан с помощью инструмента `cppwinrt.exe`), который делает это возможным. Значение типа реализации можно передать непосредственно в метод, который ожидает значение соответствующего типа проецируемых. Значение типа реализации можно передать непосредственно в метод `*this`, который ожидает значение соответствующего типа проекции.

```cppwinrt
// MyClass.idl
import "MyOtherClass.idl";
namespace MyProject
{
    runtimeclass MyClass
    {
        MyClass();
        void MemberFunction(MyOtherClass oc);
    }
}

// MyClass.h
...
namespace winrt::MyProject::implementation
{
    struct MyClass : MyClassT<MyClass>
    {
        MyClass() = default;
        void MemberFunction(MyProject::MyOtherClass const& oc) { oc.DoWork(*this); }
    };
}
...

// MyOtherClass.idl
import "MyClass.idl";
namespace MyProject
{
    runtimeclass MyOtherClass
    {
        MyOtherClass();
        void DoWork(MyClass c);
    }
}

// MyOtherClass.h
...
namespace winrt::MyProject::implementation
{
    struct MyOtherClass : MyOtherClassT<MyOtherClass>
    {
        MyOtherClass() = default;
        void DoWork(MyProject::MyClass const& c){ /* ... */ }
    };
}
...

//main.cpp
#include "pch.h"
#include <winrt/base.h>
#include "MyClass.h"
#include "MyOtherClass.h"
using namespace winrt;

// MyProject::MyClass is the projected type; the implementation type would be MyProject::implementation::MyClass.

void FreeFunction(MyProject::MyOtherClass const& oc)
{
    auto defaultInterface = winrt::make<MyProject::implementation::MyClass>();
    MyProject::implementation::MyClass* myimpl = winrt::get_self<MyProject::implementation::MyClass>(defaultInterface);
    oc.DoWork(*myimpl);
}
...
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

## <a name="namespaces-projected-types-implementation-types-and-factories"></a>Пространства имен: типы проекции, типы реализации и фабрики

Как описано ранее в этом разделе, класс среды выполнения C++/WinRT существует в виде нескольких классов C++ в нескольких пространствах имен. Следовательно, имя **MyRuntimeClass** имеет разные значения в пространствах имен **winrt::MyProject** и **winrt::MyProject::implementation**. Следите за текущим контекстом пространства имен, используя соответствующие префиксы, если вам нужно имя из другого пространства имен. Давайте рассмотрим пространства имен более подробно.

- **winrt::MyProject**. Это пространство имен содержит типы проекции. Объект типа проекции — это прокси-объект. По сути это смарт-указатель на базовый объект, который может быть реализован в текущем проекте или другой единице компиляции.
- **winrt::MyProject::implementation**. Это пространство имен содержит типы реализации. Объект типа реализации — это не указатель, а значение, представленное объектом полного стека C++. Не создавайте тип реализации напрямую. Вместо вызовите [**winrt::make**](/uwp/cpp-ref-for-winrt/make), передавая тип реализации как параметр шаблона. Выше мы показали примеры использования объекта **winrt::make**. Другой пример см. в статье [Элементы управления XAML; привязка к свойству C++/WinRT](binding-property.md#add-a-property-of-type-bookstoreviewmodel-to-mainpage). См. также: [Diagnosing direct allocations](/windows/uwp/cpp-and-winrt-apis/diag-direct-alloc) (Диагностика прямых выделений).
- **winrt::MyProject::factory_implementation**. Это пространство имен содержит фабрики. Объект в этом пространстве имен поддерживает [**IActivationFactory**](/windows/win32/api/activation/nn-activation-iactivationfactory).

В этой таблице показаны минимальные характеристики пространства имен для использования в разных контекстах.

|Пространство имен в контексте|Выбор типа проекции|Указание типа реализации|
|-|-|-|
|**winrt::MyProject**|`MyRuntimeClass`|`implementation::MyRuntimeClass`|
|**winrt::MyProject::implementation**|`MyProject::MyRuntimeClass`|`MyRuntimeClass`|

> [!IMPORTANT]
> Если вы хотите получить тип проекции из реализации, не создавайте экземпляр типа реализации с использованием `MyRuntimeClass myRuntimeClass;`. Правильный подход и пример кода для этого сценария приведены ранее в этой статье в разделе [Создание экземпляров, возврат типов и интерфейсов реализации](#instantiating-and-returning-implementation-types-and-interfaces).
>
> Проблема с использованием `MyRuntimeClass myRuntimeClass;` в этом сценарии заключается в создании объекта **winrt::MyProject::implementation::MyRuntimeClass** в стеке. Этот объект (типа реализации) в некотором смысле ведет себя как тип проекции — вы можете таким же образом вызывать методы в нем и даже преобразовывать в тип проекции. Но при выходе из области действия этот объект удаляется в соответствии с правилами C++. Следовательно, если для этого объекта вернуть тип проекции (смарт-указатель), этот указатель будет недействительным.
>
> Такой тип повреждения памяти трудно диагностировать. Но в отладочных сборках утверждение C++/WinRT помогает обнаружить эту ошибку с помощью средства обнаружения для стека. При этом сопрограммы выделяются для кучи, поэтому вы не сможете обработать эту ошибку за пределами сопрограммы. Дополнительные сведения см. в статье [Diagnosing direct allocations](/windows/uwp/cpp-and-winrt-apis/diag-direct-alloc) (Диагностика прямых выделений).

## <a name="using-projected-types-and-implementation-types-with-various-cwinrt-features"></a>Использование типов проекции и типов реализации с разными функциями C++/WinRT

Ниже указано, где функции C++/WinRT ожидают передачи типа и какие именно типы требуются (тип проекции, тип реализации или оба типа).

|Функция|Принимает|Заметки|
|-|-|-|
|`T` (представляет смарт-указатель)|Проекция|См. предупреждение из раздела [Пространства имен: типы проекции, типы реализации и фабрики](#namespaces-projected-types-implementation-types-and-factories) об ошибочном использовании типа реализации.|
|`agile_ref<T>`|Устройства обоих типов|Если вы используете тип реализации, аргумент конструктора должен быть `com_ptr<T>`.|
|`com_ptr<T>`|Реализация|Если вы используете тип проекции, возникает ошибка: `'Release' is not a member of 'T'`.|
|`default_interface<T>`|Устройства обоих типов|Если вы используете тип реализации, возвращается первый реализованный интерфейс.|
|`get_self<T>`|Реализация|Если вы используете тип проекции, возникает ошибка: `'_abi_TrustLevel': is not a member of 'T'`.|
|`guid_of<T>()`|Устройства обоих типов|Возвращает идентификатор GUID интерфейса по умолчанию.|
|`IWinRTTemplateInterface<T>`<br>|Проекция|Если вы используете тип реализации, компиляция выполняется, но возникает ошибка. См. предупреждение из раздела [Пространства имен: типы проекции, типы реализации и фабрики](#namespaces-projected-types-implementation-types-and-factories).|
|`make<T>`|Реализация|Если вы используете тип проекции, возникает ошибка: `'implements_type': is not a member of any direct or indirect base class of 'T'`.|
| `make_agile(T const&amp;)`|Устройства обоих типов|Если вы используете тип реализации, аргумент должен быть `com_ptr<T>`.|
| `make_self<T>`|Реализация|Если вы используете тип проекции, возникает ошибка: `'Release': is not a member of any direct or indirect base class of 'T'`.|
| `name_of<T>`|Проекция|Если вы используете тип реализации, вы получаете преобразованный в строку идентификатор GUID интерфейса по умолчанию.|
| `weak_ref<T>`|Устройства обоих типов|Если вы используете тип реализации, аргумент конструктора должен быть `com_ptr<T>`.|

## <a name="opt-in-to-uniform-construction-and-direct-implementation-access"></a>Предоставление согласия на использование универсального создания и прямого обращения к реализации

В этом разделе описана возможность C++/WinRT 2.0, которая предоставляется только по согласию, хотя она включена по умолчанию для новых проектов. Для существующего проекта вам нужно предоставить согласие путем настройки средства `cppwinrt.exe`. В Visual Studio задайте для свойства проекта **Общие свойства** > **C++/WinRT** > **Оптимизировано значение** *Да*. Это действие аналогично добавлению `<CppWinRTOptimized>true</CppWinRTOptimized>` в файл проекта. Кроме того, оно аналогично добавлению параметра при вызове `cppwinrt.exe` из командной строки.

Параметр `-opt[imize]` включает возможность, которая часто называется *универсальным созданием*. Благодаря универсальному (или *унифицированному*) созданию вы можете непосредственно использовать проекцию языка C++/WinRT для создания и использования своих типов реализации (реализованных вашим компонентом типов для потребления приложениями) с высокой эффективностью и без каких-либо проблем с загрузчиком.

Прежде чем перейти к описанию этой возможности, давайте рассмотрим ситуацию *без* применения универсального создания. Для примера мы начнем с класса среды выполнения Windows.

```idl
// MyClass.idl
namespace MyProject
{
    runtimeclass MyClass
    {
        MyClass();
        void Method();
        static void StaticMethod();
    }
}
```

Как разработчик на C++ вы должны быть знакомы с библиотекой C++/WinRT и, скорее всего, захотите использовать такой класс.

```cppwinrt
using namespace winrt::MyProject;

MyClass c;
c.Method();
MyClass::StaticMethod();
```

И это было бы вполне логично при условии, что использующий его код, приведенный выше, не располагается в том же компоненте, который реализует этот класс. C++/WinRT как проекция языка создает барьер между разработчиком и ABI (двоичный интерфейс приложения на основе модели COM, который определяется средой выполнения Windows). C++/WinRT не направляет вызов непосредственно в реализацию — он проходит через ABI.

То есть в строке кода, в которой вы создаете объект **MyClass** (`MyClass c;`), проекция C++/WinRT вызывает [**RoGetActivationFactory**](/windows/win32/api/roapi/nf-roapi-rogetactivationfactory) для получения класса или фабрики активации, а затем использует такую фабрику для создания объекта. В последней строке также используется фабрика для, по всей видимости, статического вызова метода. При этом требуется, чтобы классы были зарегистрированы, а ваш модуль реализовывал точку входа [**DllGetActivationFactory**](/previous-versions/br205771(v=vs.85)). C++/WinRT имеет очень быстрый кэш фабрики, поэтому такой код не создает проблем для приложения, использующего ваш компонент. Но дело в том, что в своем компоненте вы допустили некоторую ошибку.

Во-первых, даже при максимальной скорости кэша фабрики C++/WinRT вызов **RoGetActivationFactory** (или последующие вызовы через кэш фабрики) всегда будет выполняться медленнее вызова, направленного непосредственно в реализацию. Разумеется, что вызов к **RoGetActivationFactory**, после которого идут [**IActivationFactory::ActivateInstance**](/windows/win32/api/activation/nf-activation-iactivationfactory-activateinstance) и [**QueryInterface**](/windows/win32/api/unknwn/nf-unknwn-iunknown-queryinterface(refiid_void)), не будет таким эффективным, как использование выражения `new` C++ для локально определенного типа. Именно поэтому опытные разработчики C++/WinRT используют вспомогательные функции [**winrt::make**](/uwp/cpp-ref-for-winrt/make) или [**winrt::make_self**](/uwp/cpp-ref-for-winrt/make-self) при создании объектов в компоненте.

```cppwinrt
// MyClass c;
MyProject::MyClass c{ winrt::make<implementation::MyClass>() };
```

Но как можно видеть, это не очень удобно и не достаточно компактно. Чтобы создать объект, вы должны использовать вспомогательную функцию, а также четко разграничить тип реализации и проецируемый тип.

Во-вторых, использование проекции для создания класса приведет к кэшированию ее фабрики активации. Обычно именно это вам и нужно, но если фабрика располагается в том же модуле (DLL), который совершает вызов, вы фактически фиксируете библиотеку DLL и предотвращаете ее выгрузку. Во многих случаях это не имеет значения, но некоторые системные компоненты *должны* поддерживать выгрузку.

В таких случаях на помощь приходит *универсальное создание*. Вы можете использовать один и тот же синтаксис для создания объекта независимо от того, располагается ли код создания в проекте, который только использует класс, или в проекте, который *реализует* его.

```cppwinrt
// MyProject::MyClass c{ winrt::make<implementation::MyClass>() };
MyClass c;
```

При сборке проекта компонента с параметром `-opt[imize]` вызов через проекцию языка будет скомпилирован в такой же эффективный вызов к функции **winrt::make**, которая напрямую создает тип реализации. Это упрощает синтаксис, делает его более логичным, помогает избежать ухудшения производительности при вызове через фабрику, а также фиксации компонента при выполнении этого процесса. Помимо проектов компонентов, такой подход полезно использовать с приложениями на XAML. Обход **RoGetActivationFactory** для классов, реализованных в одном приложении, позволяет вам создавать их (без регистрации) всеми теми же способами, которые доступны вам за пределами компонента.

Универсальное создание применяется ко *всем* вызовам, которые отправляет фабрика на системном уровне. На практике это означает, что оптимизируются и конструкторы, и статические элементы. Приведем исходный пример еще раз.

```cppwinrt
MyClass c;
c.Method();
MyClass::StaticMethod();
```

Без `-opt[imize]` первое и последнее выражения требуют вызовов через объект фабрики. *С* `-opt[imize]` это не нужно. Такие вызовы компилируются непосредственно с реализацией и даже могут быть встроенными. Что подводит нас к другому термину, который часто используется при обсуждении `-opt[imize]`, — *прямому обращению к реализации*.

Проекции языка удобны в работе, но если вы можете напрямую обратиться к реализации, воспользуйтесь такой возможностью, чтобы сделать код максимально эффективным. C++/WinRT может сделать это за вас, причем вам даже не потребуется отказываться от безопасности и продуктивности, обеспечиваемых проекцией.

Это очень важное изменение, так как компонент должен иметь возможность взаимодействия, чтобы проекция языка сработала и напрямую обратилась к типам реализации. Так как C++/WinRT является библиотекой заголовков, вы можете заглянуть внутрь и изучить, как все работает. Без `-opt[imize]` конструктор **MyClass** и элемент **StaticMethod** определяются проекцией следующим образом.

```cppwinrt
namespace winrt::MyProject
{
    inline MyClass::MyClass() :
        MyClass(impl::call_factory<MyClass>([](auto&& f){
            return f.template ActivateInstance<MyClass>(); }))
    {
    }
    inline void MyClass::StaticMethod()
    {
        impl::call_factory<MyClass, MyProject::IClassStatics>([&](auto&& f) {
            return f.StaticMethod(); });
    }
}
```

Все описанные выше шаги выполнять необязательно — они призваны продемонстрировать, что оба вызова включают вызов к функции с именем **call_factory**. Это и есть признаком того, что эти вызовы используют кэш фабрики и не напрямую обращаются к реализации. *С* `-opt[imize]` эти же функции не имеют определений. Они объявляются проекцией, а их определение выполняет компонент.

Такой компонент может предоставить определения, которые напрямую направляют вызов в реализацию. Здесь мы подходим к важному изменению. Такие определения создаются автоматически, если вы используете `-component` и `-opt[imize]`. Они также присутствуют в файле с именем `Type.g.cpp`, где *Type* — это имя реализуемого класса среды выполнения. Именно поэтому в компоновщике возникают различные ошибки, когда вы впервые включаете `-opt[imize]` в существующем проекте. Вам нужно включить такой созданный файл в свою реализацию, чтобы исправить ситуацию.

В нашем примере `MyClass.h` может выглядеть таким образом (независимо от того, используется ли `-opt[imize]`).

```cppwinrt
// MyClass.h
#pragma once
#include "MyClass.g.h"
 
namespace winrt::MyProject::implementation
{
    struct MyClass : ClassT<MyClass>
    {
        MyClass() = default;
 
        static void StaticMethod();
        void Method();
    };
}
namespace winrt::MyProject::factory_implementation
{
    struct MyClass : ClassT<MyClass, implementation::MyClass>
    {
    };
}
```

Все это приводит нас к `MyClass.cpp`.

```cppwinrt
#include "pch.h"
#include "MyClass.h"
#include "MyClass.g.cpp" // !!It's important that you add this line!!
 
namespace winrt::MyProject::implementation
{
    void MyClass::StaticMethod()
    {
    }
 
    void MyClass::Method()
    {
    }
}
```

Чтобы использовать универсальное создание в существующем проекте, вам нужно изменить файл `.cpp` каждой реализации для применения `#include <Sub/Namespace/Type.g.cpp>` после включения (и определения) класса реализации. Такой файл содержит определения функций, которые не были определены проекцией. Вот как могут выглядеть такие определения в файле `MyClass.g.cpp`.

```cppwinrt
namespace winrt::MyProject
{
    MyClass::MyClass() :
        MyClass(make<MyProject::implementation::MyClass>())
    {
    }
    void MyClass::StaticMethod()
    {
        return MyProject::implementation::MyClass::StaticMethod();
    }
}
```

Это замечательно дополняет проекцию эффективными вызовами, которые направляются непосредственно к реализации, что позволяет избежать использования вызовов к кэшу фабрики и удовлетворяет требованиям компоновщика.

Наконец, `-opt[imize]` изменяет реализацию файла `module.g.cpp` (файл, который помогает вам реализовать экспортируемые функции ваших DLL **DllGetActivationFactory** и **DllCanUnloadNow**) таким способом, что инкрементные сборки выполняются намного быстрее благодаря устранению сильной связи между типами, которая требовалась в C++/WinRT 1.0. Они часто называются *фабриками с затиранием типов*. Без `-opt[imize]` файл `module.g.cpp`, который был создан для вашего компонента, сначала включает определения всех ваших классов реализации &mdash; в этом примере это `MyClass.h`. Затем он напрямую создает фабрику реализации для каждого класса следующим образом.

```cppwinrt
if (requal(name, L"MyProject.MyClass"))
{
    return winrt::detach_abi(winrt::make<winrt::MyProject::factory_implementation::MyClass>());
}
```

Опять же, вам не нужно абсолютно точно следовать этому. Достаточно учесть, что такой подход требует полного определения каждого и всех классов, реализованных вашим компонентом. Это может сильно повлиять на внутренний цикл, так как любые изменения в одной реализации приведут к необходимости повторной компиляции файла `module.g.cpp`. При использовании `-opt[imize]` это больше не нужно. В таком случае с созданным файлом `module.g.cpp` происходят две вещи. Во-первых, он больше не включает классы реализации. В этом примере он не включает `MyClass.h`. Вместо этого он создает фабрики реализаций без каких-либо данных об их реализациях.

```cppwinrt
void* winrt_make_MyProject_MyClass();
 
if (requal(name, L"MyProject.MyClass"))
{
    return winrt_make_MyProject_MyClass();
}
```

Само собой, включать их определения не нужно, а разрешение определения функции **winrt_make_Component_Class** выполнит компоновщик. Конечно же, вам не нужно беспокоиться об этом, так как файл `MyClass.g.cpp`, который создается для вас (и который вы ранее включили для поддержки универсального создания), также определяет эту функцию. Ниже приведено все содержимое файла `MyClass.g.cpp`, который был создан для этого примера.

```cppwinrt
void* winrt_make_MyProject_MyClass()
{
    return winrt::detach_abi(winrt::make<winrt::MyProject::factory_implementation::MyClass>());
}
namespace winrt::MyProject
{
    MyClass::MyClass() :
        MyClass(make<MyProject::implementation::MyClass>())
    {
    }
    void MyClass::StaticMethod()
    {
        return MyProject::implementation::MyClass::StaticMethod();
    }
}
```

Как видите, функция **winrt_make_MyProject_MyClass** напрямую создает фабрику реализации. Это означает, что вы можете запросто изменить любую реализацию и вам не потребуется повторно компилировать файл `module.g.cpp`. Обновить и повторно скомпилировать файл `module.g.cpp` нужно будет только при добавлении или удалении классов среды выполнения Windows.

## <a name="overriding-base-class-virtual-methods"></a>Переопределение виртуальных методов базового класса

Производный класс может некорректно обрабатывать виртуальные методы, если базовый и производный класс являются определяемыми приложением классами, но виртуальный метод определен в прародительском классе среды выполнения Windows. На практике это происходит при наследовании из классов XAML. Далее этот раздел продолжается с примера в разделе [Производные классы](/windows/uwp/cpp-and-winrt-apis/move-to-winrt-from-cx#derived-classes).

```cppwinrt
namespace winrt::MyNamespace::implementation
{
    struct BasePage : BasePageT<BasePage>
    {
        void OnNavigatedFrom(Windows::UI::Xaml::Navigation::NavigationEventArgs const& e);
    };

    struct DerivedPage : DerivedPageT<DerivedPage>
    {
        void OnNavigatedFrom(Windows::UI::Xaml::Navigation::NavigationEventArgs const& e);
    };
}
```

Иерархия имеет вид [**Windows::UI::Xaml::Controls::Page**](/uwp/api/windows.ui.xaml.controls.page) \<- **BasePage** \<- **DerivedPage**. Метод **BasePage::OnNavigatedFrom** корректно переопределяет [**Page::OnNavigatedFrom**](/uwp/api/windows.ui.xaml.controls.page.onnavigatedfrom), но **DerivedPage::OnNavigatedFrom** не переопределяет **BasePage::OnNavigatedFrom**.

Здесь **DerivedPage** повторно использует виртуальную таблицу **IPageOverrides** из **BasePage**, то есть метод **IPageOverrides::OnNavigatedFrom** не переопределяется. Одно из возможных решений заключается в том, что класс **BasePage** должен быть классом шаблона для самого себя, а также иметь реализацию, которая полностью находится в файле заголовка, но это сильно усложняет ситуацию.

В качестве временного решения объявите метод **OnNavigatedFrom** явно виртуальным в базовом классе. Таким образом, если запись виртуальной таблицы для **DerivedPage::IPageOverrides::OnNavigatedFrom** вызывает **BasePage::IPageOverrides::OnNavigatedFrom**, производитель вызывает класс **BasePage::OnNavigatedFrom**, который (из-за своей виртуальности) в итоге вызывает **DerivedPage::OnNavigatedFrom**.

```cppwinrt
namespace winrt::MyNamespace::implementation
{
    struct BasePage : BasePageT<BasePage>
    {
        // Note the `virtual` keyword here.
        virtual void OnNavigatedFrom(Windows::UI::Xaml::Navigation::NavigationEventArgs const& e);
    };

    struct DerivedPage : DerivedPageT<DerivedPage>
    {
        void OnNavigatedFrom(Windows::UI::Xaml::Navigation::NavigationEventArgs const& e);
    };
}
```

Для этого необходимо, чтобы для всех членов иерархии класса были согласованы возвращаемое значение и типы параметров метода **OnNavigatedFrom**. Если они не согласованы, используйте версию выше в качестве виртуального метода и упакуйте другие варианты.

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

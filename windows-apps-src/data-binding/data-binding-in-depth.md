---
ms.assetid: 41E1B4F1-6CAF-4128-A61A-4E400B149011
title: Подробно о привязке данных
description: Привязка данных обеспечивает отображение данных в пользовательском интерфейсе приложения и (дополнительно) синхронизацию с этими данными.
ms.date: 10/05/2018
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
dev_langs:
- csharp
- cppwinrt
ms.openlocfilehash: ac4ab9dc85292187055c7b4ef772cc64104f7944
ms.sourcegitcommit: a791269689f0494f2905ee9bdb3cb739dc33eda3
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/19/2019
ms.locfileid: "72601224"
---
# <a name="data-binding-in-depth"></a>Подробно о привязке данных

**Важные API**

-   [ **{Разметка расширения разметки КС:бинд}** ](../xaml-platform/x-bind-markup-extension.md)
-   [**Класс привязки**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Data.Binding)
-   [**DataContext**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.frameworkelement.datacontext)
-   [**INotifyPropertyChanged**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Data.INotifyPropertyChanged)

> [!NOTE]
> В этой статье подробно описаны средства привязки данных. Краткое практическое введение см. в [Обзоре привязки данных](data-binding-quickstart.md).

В этом разделе описывается привязка данных в приложениях универсальная платформа Windows (UWP). Интерфейсы API, обсуждаемые здесь, находятся в [пространстве имен **Windows. UI. XAML. Data** ](/uwp/api/windows.ui.xaml.data).

Привязка данных обеспечивает отображение данных в пользовательском интерфейсе приложения и (дополнительно) синхронизацию с этими данными. Используя привязку данных, вы можете отделить вопросы обработки данных от вопросов, связанных с пользовательским интерфейсом. Это упрощает концептуальную модель, а также улучшает удобочитаемость, возможности тестирования и удобство поддержки приложения.

Привязку данных можно использовать, чтобы просто отобразить значения из источника данных при первом отображении пользовательского интерфейса, не реагируя на изменения этих значений. Это режим привязки, называемый *одноразовым*, и он хорошо работает для значения, которое не изменяется во время выполнения. Кроме того, можно просмотреть значения и обновить пользовательский интерфейс при их изменении. Этот режим называется *односторонним*и хорошо подходит для данных, предназначенных только для чтения. Наконец, вы можете как отслеживать, так и обновлять данные, чтобы изменения, которые пользователь вносит в значения в пользовательском интерфейсе, автоматически отправлялись в источник данных. Этот режим называется *двусторонним*и хорошо работает для данных, предназначенных для чтения и записи. Вот несколько примеров.

-   Можно использовать одноразовый режим для привязки [**изображения**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Image) к фотографии текущего пользователя.
-   Можно использовать односторонний режим для привязки [**ListView**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.ListView) к коллекции статей новостей в режиме реального времени, сгруппированных по разделам газет.
-   Можно использовать двусторонний режим для привязки [**текстового поля**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.TextBox) к имени клиента в форме.

Независимо от режима существует два типа привязки, которые обычно объявляются в разметке пользовательского интерфейса. Вы можете использовать [расширение разметки {x:Bind}](https://docs.microsoft.com/windows/uwp/xaml-platform/x-bind-markup-extension) или [расширение разметки {Binding}](https://docs.microsoft.com/windows/uwp/xaml-platform/binding-markup-extension). Кроме того, можно одновременно использовать оба типа привязки в одном приложении и даже в одном элементе пользовательского интерфейса. Расширение разметки {x:Bind}, которое впервые появилось в Windows 10, отличается повышенной производительностью. Все сведения, описанные в этом разделе, касаются обоих типов привязки, если явно не указано иное.

**Примеры приложений, демонстрирующие {КС:бинд}**

-   [Пример {x:Bind}](https://go.microsoft.com/fwlink/p/?linkid=619989).
-   [QuizGame](https://github.com/microsoft/Windows-appsample-networkhelper).
-   [Пример основных элементов пользовательского интерфейса XAML](https://go.microsoft.com/fwlink/p/?linkid=619992).

**Примеры приложений, демонстрирующие {Binding}**

-   Скачайте приложение [Bookstore1](https://go.microsoft.com/fwlink/?linkid=532950).
-   Скачайте приложение [Bookstore2](https://go.microsoft.com/fwlink/?linkid=532952).

## <a name="every-binding-involves-these-pieces"></a>Неотъемлемые компоненты каждой привязки

-   *Источник привязки*. Это источник данных для привязки, в качестве которого может выступать экземпляр любого класса, содержащего члены, значения которых вы хотите отобразить в пользовательском интерфейсе.
-   *Целевой объект привязки*. Это [**DependencyProperty**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.DependencyProperty) класса [**FrameworkElement**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.FrameworkElement) в пользовательском интерфейсе, отвечающий за отображение данных.
-   *Объект привязки*. Это элемент, который передает значения данных из источника в целевой объект, а также (необязательно) из целевого объекта назад в источник. Объект привязки создается во время загрузки XAML из расширения разметки [{x:Bind}](https://docs.microsoft.com/windows/uwp/xaml-platform/x-bind-markup-extension) или [{Binding}](https://docs.microsoft.com/windows/uwp/xaml-platform/binding-markup-extension).

В следующих разделах мы подробнее рассмотрим источник, целевой объект и объект привязки. Кроме того, мы свяжем эти разделы с помощью примера привязки содержимого кнопки к строковому свойству **NextButtonText**, которое принадлежит классу **HostViewModel**.

### <a name="binding-source"></a>Источник привязки

Здесь приведен простейший пример реализации класса, который можно использовать в качестве источника привязки.

Если вы используете [ C++/WinRT](/windows/uwp/cpp-and-winrt-apis/intro-to-using-cpp-with-winrt), добавьте в проект новые элементы **файла MIDL (. IDL)** с именем, как показано в приведенном ниже C++примере кода/WinRT. Замените содержимое этих новых файлов кодом [MIDL 3,0](/uwp/midl-3/intro) , показанным в списке, выполните сборку проекта, чтобы создать `HostViewModel.h` и `.cpp`, а затем добавьте код в создаваемые файлы, чтобы они соответствовали списку. Дополнительные сведения о созданных файлах и их копировании в проект см. в разделе [элементы управления XAML; привязка к свойству C++/WinRT](/windows/uwp/cpp-and-winrt-apis/binding-property).

```csharp
public class HostViewModel
{
    public HostViewModel()
    {
        this.NextButtonText = "Next";
    }

    public string NextButtonText { get; set; }
}
```

```cppwinrt
// HostViewModel.idl
namespace DataBindingInDepth
{
    runtimeclass HostViewModel
    {
        HostViewModel();
        String NextButtonText;
    }
}

// HostViewModel.h
// Implement the constructor like this, and add this field:
...
HostViewModel() : m_nextButtonText{ L"Next" } {}
...
private:
    std::wstring m_nextButtonText;
...

// HostViewModel.cpp
// Implement like this:
...
hstring HostViewModel::NextButtonText()
{
    return hstring{ m_nextButtonText };
}

void HostViewModel::NextButtonText(hstring const& value)
{
    m_nextButtonText = value;
}
...
```

Эта реализация класса **HostViewModel** и его свойство **NextButtonText** подходят только для единовременной привязки. Однако односторонние и двусторонние привязки используются очень часто. Привязки этих типов обеспечивают автоматическое обновление пользовательского интерфейса в результате внесения изменений в значениях данных источника привязки. Чтобы эти типы привязки работали правильно, необходимо сделать так, чтобы объект привязки "отслеживал" ваш источник привязки. Поэтому если мы хотим выполнить одно- или двустороннюю привязку к свойству **NextButtonText** в нашем примере, объект привязки должен отслеживать все изменения, которые вносятся в значение этого свойства во время выполнения.

Для этого на основе класса [**DependencyObject**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.DependencyObject) можно создать производный класс, представляющий собой источник привязки, и предоставить значение данных с помощью класса [**DependencyProperty**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.DependencyProperty). Именно так класс [**FrameworkElement**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.FrameworkElement) становится доступным для отслеживания. **FrameworkElements** — хорошие источники привязки, готовые к немедленному применению.

Более легким способом сделать класс доступным для отслеживания (что также необходимо для классов, уже имеющих базовый класс) является реализация интерфейса [**System.ComponentModel.INotifyPropertyChanged**](https://docs.microsoft.com/dotnet/api/system.componentmodel.inotifypropertychanged). Для этого необходимо всего лишь реализовать одно событие с именем **PropertyChanged**. Пример использования класса **HostViewModel** см. ниже.

```csharp
...
using System.ComponentModel;
using System.Runtime.CompilerServices;
...
public class HostViewModel : INotifyPropertyChanged
{
    private string nextButtonText;

    public event PropertyChangedEventHandler PropertyChanged = delegate { };

    public HostViewModel()
    {
        this.NextButtonText = "Next";
    }

    public string NextButtonText
    {
        get { return this.nextButtonText; }
        set
        {
            this.nextButtonText = value;
            this.OnPropertyChanged();
        }
    }

    public void OnPropertyChanged([CallerMemberName] string propertyName = null)
    {
        // Raise the PropertyChanged event, passing the name of the property whose value has changed.
        this.PropertyChanged(this, new PropertyChangedEventArgs(propertyName));
    }
}
```

```cppwinrt
// HostViewModel.idl
namespace DataBindingInDepth
{
    runtimeclass HostViewModel : Windows.UI.Xaml.Data.INotifyPropertyChanged
    {
        HostViewModel();
        String NextButtonText;
    }
}

// HostViewModel.h
// Add this field:
...
    winrt::event_token PropertyChanged(Windows::UI::Xaml::Data::PropertyChangedEventHandler const& handler);
    void PropertyChanged(winrt::event_token const& token) noexcept;

private:
    winrt::event<Windows::UI::Xaml::Data::PropertyChangedEventHandler> m_propertyChanged;
...

// HostViewModel.cpp
// Implement like this:
...
void HostViewModel::NextButtonText(hstring const& value)
{
    if (m_nextButtonText != value)
    {
        m_nextButtonText = value;
        m_propertyChanged(*this, Windows::UI::Xaml::Data::PropertyChangedEventArgs{ L"NextButtonText" });
    }
}

winrt::event_token HostViewModel::PropertyChanged(Windows::UI::Xaml::Data::PropertyChangedEventHandler const& handler)
{
    return m_propertyChanged.add(handler);
}

void HostViewModel::PropertyChanged(winrt::event_token const& token) noexcept
{
    m_propertyChanged.remove(token);
}
...
```

Теперь свойство **NextButtonText** доступно для отслеживания. Когда вы создаете одно- или двустороннюю привязку к этому свойству (мы позже покажем, как сделать это), полученный объект привязки подписывается на событие **PropertyChanged**. При возникновении этого события обработчик объекта привязки получает аргумент, содержащий имя свойства, которое изменилось. Именно так объект привязки узнает, к какому значению свойства необходимо перейти для повторного считывания.

Поэтому не нужно реализовывать шаблон, показанный выше несколько раз, если вы используете C# , то можете просто наследовать от класса **биндаблебасе** басов, который можно найти в примере [куизгаме](https://github.com/microsoft/Windows-appsample-networkhelper) (в папке Common). Пример приведен ниже.

```csharp
public class HostViewModel : BindableBase
{
    private string nextButtonText;

    public HostViewModel()
    {
        this.NextButtonText = "Next";
    }

    public string NextButtonText
    {
        get { return this.nextButtonText; }
        set { this.SetProperty(ref this.nextButtonText, value); }
    }
}
```

```cppwinrt
// Your BindableBase base class should itself derive from Windows::UI::Xaml::DependencyObject. Then, in HostViewModel.idl, derive from BindableBase instead of implementing INotifyPropertyChanged.
```

> [!NOTE]
> Для C++/WinRT любой класс среды выполнения, объявленный в приложении, производном от базового класса, называется классом, поддерживающим *композицию* . На составные классы накладываются определенные ограничения. Чтобы приложение прошло тесты [комплекта сертификации приложений для Windows](../debug-test-perf/windows-app-certification-kit.md), используемые Visual Studio и Microsoft Store для проверки отправляемого кода (и, следовательно, чтобы оно было успешно принято в Microsoft Store), составляемый класс в конечном счете должен быть производным от базового класса Windows. Это означает, что класс с самого корня иерархии наследования должен иметь тип, полученный в пространстве имен Windows.*. Если вам нужно создать класс среды выполнения, производный от базового класса (например, чтобы реализовать класс **BindableBase** для всех моделей представления для создания производных объектов), то он может быть производным от [**Windows.UI.Xaml.DependencyObject**](/uwp/api/windows.ui.xaml.dependencyobject).

Вызов события **PropertyChanged** с аргументом [**String.Empty**](https://docs.microsoft.com/dotnet/api/system.string.empty) или значением **null** указывает, что не все свойства индексатора в объекте необходимо считать еще раз. Событие можно вызвать, чтобы указать, что свойства индексатора объекта изменились с помощью аргумента "Item \[*индексатор* \]" для конкретных индексаторов (где *индексатор* — это значение индекса) или значение "Item \[ \]" для всех индексаторов.

Источник привязки может рассматриваться как единичный объект, свойства которого содержат данные, или как коллекция объектов. В C# и Visual Basic коде можно выполнить однократную привязку к объекту, реализующему [**List (Of T)** ](https://docs.microsoft.com/dotnet/api/system.collections.generic.list-1) , для вывода коллекции, которая не изменяется во время выполнения. Если используется отслеживаемая коллекция (которая поддерживает отслеживание добавления и удаления ее элементов), следует выполнить одностороннюю привязку к классу [**ObservableCollection(Of T)** ](https://docs.microsoft.com/dotnet/api/system.collections.objectmodel.observablecollection-1). В C++коде/CX можно выполнить привязку к [**векторному &lt;T &gt;** ](https://docs.microsoft.com/cpp/cppcx/platform-collections-vector-class) для наблюдаемых и ненаблюдаемых коллекций, а C++/WinRT имеет собственные типы. Чтобы выполнить привязку к собственным классам коллекции, используйте рекомендации из следующей таблицы.

|Сценарий|C# и VB (CLR)|C++/WinRT|C++/CX|
|-|-|-|-|
|Привязка к объекту.|Любой объект.|Любой объект.|Объект должен иметь атрибут [**BindableAttribute**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Data.BindableAttribute) или реализовывать интерфейс [**ICustomPropertyProvider**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Data.ICustomPropertyProvider).|
|Получение уведомлений об изменении свойств из привязанного объекта.|Объект должен реализовывать [**INotifyPropertyChanged**](https://docs.microsoft.com/dotnet/api/system.componentmodel.inotifypropertychanged).| Объект должен реализовывать [**INotifyPropertyChanged**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Data.INotifyPropertyChanged).|Объект должен реализовывать [**INotifyPropertyChanged**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Data.INotifyPropertyChanged).|
|Привязка к коллекции.| [**List (Of T)** ](https://docs.microsoft.com/dotnet/api/system.collections.generic.list-1)|[**IVector**](/uwp/api/windows.foundation.collections.ivector_t_) [**IInspectable**](/windows/desktop/api/inspectable/nn-inspectable-iinspectable)или [**ибиндаблеобсерваблевектор**](/uwp/api/windows.ui.xaml.interop.ibindableobservablevector). См. раздел [элементы управления элементами XAML; C++привязка к коллекции/WinRT](../cpp-and-winrt-apis/binding-collection.md) и [коллекциям с помощью C++/WinRT](../cpp-and-winrt-apis/collections.md).| [ **@No__t_3 векторного &lt;T**](https://docs.microsoft.com/cpp/cppcx/platform-collections-vector-class)|
|Получение уведомлений об изменениях коллекции из привязанной коллекции.|[**ObservableCollection (Of T)** ](https://docs.microsoft.com/dotnet/api/system.collections.objectmodel.observablecollection-1)|[**Иобсерваблевектор**](/uwp/api/windows.foundation.collections.iobservablevector_t_) [**IInspectable**](/windows/desktop/api/inspectable/nn-inspectable-iinspectable). Например, [ **&lt;T WinRT:: single_threaded_observable_vector &gt;** ](https://docs.microsoft.com/en-us/uwp/cpp-ref-for-winrt/single-threaded-observable-vector).|[ **@No__t_3 &lt;T иобсерваблевектор**](https://docs.microsoft.com/uwp/api/Windows.Foundation.Collections.IObservableVector_T_).  Метод [**Vector &lt;T &gt;** ](https://docs.microsoft.com/cpp/cppcx/platform-collections-vector-class) реализует этот интерфейс.|
|Реализация коллекции с поддержкой привязки.|Расширьте [**List(Of T)** ](https://docs.microsoft.com/dotnet/api/system.collections.generic.list-1) либо реализуйте [**IList**](https://docs.microsoft.com/dotnet/api/system.collections.ilist), [**IList**](https://docs.microsoft.com/dotnet/api/system.collections.generic.ilist-1)(Of [**Object**](https://docs.microsoft.com/dotnet/api/system.object)), [**IEnumerable**](https://docs.microsoft.com/dotnet/api/system.collections.ienumerable) или [**IEnumerable**](https://docs.microsoft.com/dotnet/api/system.collections.generic.ienumerable-1)(Of **Object**). Привязка к универсальным интерфейсам **IList(Of T)** и **IEnumerable(Of T)** не поддерживается.|Реализуйте [**IVector**](/uwp/api/windows.foundation.collections.ivector_t_) из [**IInspectable**](/windows/desktop/api/inspectable/nn-inspectable-iinspectable). См. раздел [элементы управления элементами XAML; C++привязка к коллекции/WinRT](../cpp-and-winrt-apis/binding-collection.md) и [коллекциям с помощью C++/WinRT](../cpp-and-winrt-apis/collections.md).|Реализуйте [**IBindableVector**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Interop.IBindableVector), [**IBindableIterable**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Interop.IBindableIterable), [**IVector**](https://docs.microsoft.com/uwp/api/Windows.Foundation.Collections.IVector_T_)&lt;[**Object**](https://docs.microsoft.com/dotnet/api/system.object)^&gt;, [**IIterable**](https://docs.microsoft.com/uwp/api/Windows.Foundation.Collections.IIterable_T_)&lt;**Object**^&gt;, **IVector**&lt;[**IInspectable**](https://docs.microsoft.com/windows/desktop/api/inspectable/nn-inspectable-iinspectable)\*&gt; или **IIterable**&lt;**IInspectable**\*&gt;. Привязка к универсальным интерфейсам **IVector&lt;T&gt;** и **IIterable&lt;T&gt;** не поддерживается.|
| Реализуйте коллекцию, которая поддерживает уведомления об изменении коллекций. | Расширьте [**ObservableCollection(Of T)** ](https://docs.microsoft.com/dotnet/api/system.collections.objectmodel.observablecollection-1) или реализуйте (не универсальные) интерфейсы [**IList**](https://docs.microsoft.com/dotnet/api/system.collections.ilist) и [**INotifyCollectionChanged**](https://docs.microsoft.com/dotnet/api/system.collections.specialized.inotifycollectionchanged).|Реализуйте [**Иобсерваблевектор**](/uwp/api/windows.foundation.collections.iobservablevector_t_) [**IInspectable**](/windows/desktop/api/inspectable/nn-inspectable-iinspectable)или [**ибиндаблеобсерваблевектор**](/uwp/api/windows.ui.xaml.interop.ibindableobservablevector).|Реализуйте [**IBindableVector**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Interop.IBindableVector) и [**IBindableObservableVector**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Interop.IBindableObservableVector).|
|Реализуйте коллекцию с поддержкой инкрементной нагрузки.|Расширьте [**ObservableCollection(Of T)** ](https://docs.microsoft.com/dotnet/api/system.collections.objectmodel.observablecollection-1) или реализуйте (не универсальные) интерфейсы [**IList**](https://docs.microsoft.com/dotnet/api/system.collections.ilist) и [**INotifyCollectionChanged**](https://docs.microsoft.com/dotnet/api/system.collections.specialized.inotifycollectionchanged). Кроме того, реализуйте [**ISupportIncrementalLoading**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Data.ISupportIncrementalLoading).|Реализуйте [**Иобсерваблевектор**](/uwp/api/windows.foundation.collections.iobservablevector_t_) [**IInspectable**](/windows/desktop/api/inspectable/nn-inspectable-iinspectable)или [**ибиндаблеобсерваблевектор**](/uwp/api/windows.ui.xaml.interop.ibindableobservablevector). Кроме того, реализуйте [**ISupportIncrementalLoading**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Data.ISupportIncrementalLoading)|Реализуйте [**IBindableVector**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Interop.IBindableVector), [**IBindableObservableVector**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Interop.IBindableObservableVector) и [**ISupportIncrementalLoading**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Data.ISupportIncrementalLoading).|

Элементы управления списками можно привязать к источникам данных любого размера и сохранить высокую производительность благодаря использованию инкрементной нагрузки. Например, элементы управления списками можно привязать к результатам запроса изображений в Bing, причем одновременная загрузка всех результатов не требуется. Вместо этого сразу можно загрузить лишь некоторые результаты и загружать дополнительные результаты по мере необходимости. Для поддержки добавочной загрузки необходимо реализовать [**ISupportIncrementalLoading**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Data.ISupportIncrementalLoading) в источнике данных, который поддерживает уведомления об изменении коллекций. Когда механизм привязки данных запрашивает больше данных, источник данных должен делать соответствующие запросы, объединять результаты, а затем отправлять соответствующие уведомления для обновления пользовательского интерфейса.

### <a name="binding-target"></a>Целевой объект привязки

В двух приведенных ниже примерах свойство **Button. Content** является целевым объектом привязки, а для его значения задано расширение разметки, которое объявляет объект привязки. Сначала отображается расширение разметки [{x:Bind}](https://docs.microsoft.com/windows/uwp/xaml-platform/x-bind-markup-extension), а затем — [{Binding}](https://docs.microsoft.com/windows/uwp/xaml-platform/binding-markup-extension). Объявления привязок в разметке используется очень часто (благодаря удобству и удобочитаемости). Однако вы можете не использовать разметку, а вместо этого при необходимости императивно (программными средствами) создать экземпляр класса [**Binding**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Data.Binding).

```xaml
<Button Content="{x:Bind ...}" ... />
```

```xaml
<Button Content="{Binding ...}" ... />
```

Если вы используете C++ C++ /WinRT или расширения визуальных компонентов (C++/CX), необходимо добавить атрибут [**биндаблеаттрибуте**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Data.BindableAttribute) в любой класс среды выполнения, для которого требуется использовать расширение разметки [{Binding}](https://docs.microsoft.com/windows/uwp/xaml-platform/binding-markup-extension) с.

> [!IMPORTANT]
> Если вы используете [ C++/WinRT](/windows/uwp/cpp-and-winrt-apis/intro-to-using-cpp-with-winrt), то атрибут [**биндаблеаттрибуте**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Data.BindableAttribute) доступен, если вы установили Windows SDK версии 10.0.17763.0 (Windows 10, 1809) или более поздней версии. Без этого атрибута необходимо реализовать интерфейсы [ICustomPropertyProvider](/uwp/api/windows.ui.xaml.data.icustompropertyprovider) и [икустомпроперти](/uwp/api/windows.ui.xaml.data.icustomproperty) , чтобы иметь возможность использовать расширение разметки [{Binding}](https://docs.microsoft.com/windows/uwp/xaml-platform/binding-markup-extension) .

### <a name="binding-object-declared-using-xbind"></a>Объект привязки, объявленный с использованием расширения разметки {x:Bind}

Для создания разметки [{x:Bind}](https://docs.microsoft.com/windows/uwp/xaml-platform/x-bind-markup-extension) необходимо выполнить еще один шаг. Нужно предоставить класс источника привязки из класса, представляющего нашу страницу разметки. Это делается путем добавления свойства (типа **хоствиевмодел** в данном случае) в наш класс страницы **MainPage** .

```csharp
namespace DataBindingInDepth
{
    public sealed partial class MainPage : Page
    {
        public MainPage()
        {
            this.InitializeComponent();
            this.ViewModel = new HostViewModel();
        }
    
        public HostViewModel ViewModel { get; set; }
    }
}
```

```cppwinrt
// MainPage.idl
import "HostViewModel.idl";

namespace DataBindingInDepth
{
    runtimeclass MainPage : Windows.UI.Xaml.Controls.Page
    {
        MainPage();
        HostViewModel ViewModel{ get; };
    }
}

// MainPage.h
// Include a header, and add this field:
...
#include "HostViewModel.h"
...
    DataBindingInDepth::HostViewModel ViewModel();

private:
    DataBindingInDepth::HostViewModel m_viewModel{ nullptr };
...

// MainPage.cpp
// Implement like this:
...
MainPage::MainPage()
{
    InitializeComponent();

}

DataBindingInDepth::HostViewModel MainPage::ViewModel()
{
    return m_viewModel;
}
...
```

После этого мы можем подробнее рассмотреть разметку, объявляющую объект привязки. В приведенном ниже примере используется целевой объект привязки **Button.Content**, который мы ранее применяли в разделе "Целевой объект привязки". Он уже привязан к свойству **HostViewModel.NextButtonText**.

```xaml
<!-- MainPage.xaml -->
<Page x:Class="DataBindingInDepth.Mainpage" ... >
    <Button Content="{x:Bind Path=ViewModel.NextButtonText, Mode=OneWay}" ... />
</Page>
```

Обратите внимание на значение, указанное для свойства **Path**. Это значение интерпретируется в контексте самой страницы, и в этом случае путь начинается с ссылки на свойство **ViewModel** , которое мы только что добавили на страницу **MainPage** . Это свойство возвращает экземпляр **HostViewModel**, поэтому мы можем обратиться к этому объекту, чтобы получить доступ к свойству **HostViewModel.NextButtonText**. Кроме того, необходимо указать свойство **Mode**, чтобы переопределить значение [{x:Bind}](https://docs.microsoft.com/windows/uwp/xaml-platform/x-bind-markup-extension) по умолчанию для единовременной привязки.

Свойство [**Path**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.data.binding.path) поддерживает несколько вариантов синтаксиса для привязки к вложенным и подключенным свойствам, а также целочисленным и строковым индексаторам. Дополнительную информацию см. в разделе [Синтаксис Property-path](https://docs.microsoft.com/windows/uwp/xaml-platform/property-path-syntax). Привязка к строковым индексаторам позволяет выполнять привязку к динамическим свойствам без реализации интерфейса [**ICustomPropertyProvider**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Data.ICustomPropertyProvider). Другие параметры см. в [статье о расширении разметки {x:Bind}](https://docs.microsoft.com/windows/uwp/xaml-platform/x-bind-markup-extension).

Чтобы продемонстрировать, что свойство **хоствиевмодел. некстбуттонтекст** действительно является наблюдаемым, добавьте обработчик событий **Click** к кнопке и обновите значение **хоствиевмодел. некстбуттонтекст**. Выполните сборку, запустите и нажмите кнопку, чтобы увидеть значение обновления **содержимого** кнопки.

```csharp
// MainPage.xaml.cs
private void Button_Click(object sender, RoutedEventArgs e)
{
    this.ViewModel.NextButtonText = "Updated Next button text";
}
```

```cppwinrt
// MainPage.cpp
void MainPage::ClickHandler(IInspectable const&, RoutedEventArgs const&)
{
    ViewModel().NextButtonText(L"Updated Next button text");
}
```

> [!NOTE]
> Изменения в [**TextBox. Text**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.textbox.text) отправляются в двусторонний источник, когда [**текстовое поле**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.TextBox) теряет фокус, а не после каждого нажатия клавиши пользователем.

**DataTemplate и КС:дататипе**

В классе [**DataTemplate**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.DataTemplate) (который используется как шаблон элемента, содержимого или заголовка) значение **Path** интерпретируется не в контексте страницы, а в контексте объекта данных, для которого создается шаблон. При использовании {x:Bind} класс **DataTemplate** должен объявлять тип своего объекта данных с помощью атрибута **x:DataType**, чтобы его привязки (и созданный для них эффективный код) можно было проверить во время компиляции. Приведенный ниже пример можно использовать в качестве свойства **ItemTemplate** элемента управления, привязанного к коллекции объектов **SampleDataGroup**.

```xaml
<DataTemplate x:Key="SimpleItemTemplate" x:DataType="data:SampleDataGroup">
    <StackPanel Orientation="Vertical" Height="50">
      <TextBlock Text="{x:Bind Title}"/>
      <TextBlock Text="{x:Bind Description}"/>
    </StackPanel>
  </DataTemplate>
```

**Слабо типизированные объекты в пути**

Представьте, например, что у вас есть тип SampleDataGroup, который реализует строковое свойство Title. И у вас есть свойство MainPage. Сампледатаграупасобжект, которое имеет тип Object, но фактически возвращает экземпляр SampleDataGroup. Привязка `<TextBlock Text="{x:Bind SampleDataGroupAsObject.Title}"/>` приведет к ошибке компиляции, поскольку свойство Title не найдено в объекте типа. Поэтому необходимо добавить приведение в синтаксис пути, например: `<TextBlock Text="{x:Bind ((data:SampleDataGroup)SampleDataGroupAsObject).Title}"/>`. Вот другой пример, где Element объявлен как объект, но фактически это TextBlock: `<TextBlock Text="{x:Bind Element.Text}"/>`. Приведение устраняет проблему: `<TextBlock Text="{x:Bind ((TextBlock)Element).Text}"/>`.

**Если данные загружаются асинхронно**

Код для поддержки **{x:Bind}** создается во время компиляции в разделяемых классах для ваших страниц. Эти файлы можно найти в папке `obj` с такими именами, как `<view name>.g.cs` (для C#). Созданный код содержит обработчик события [**Loading**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.FrameworkElement) страницы, который вызывает метод **Initialize** в созданном классе, представляющем привязки вашей страницы. **Initialize** в свою очередь вызывает **Update** для перемещения данных между источником и целевым объектом привязки. **Loading** создается только перед первым этапом измерения страницы или пользовательского элемента управления. Поэтому если ваши данные загружаются асинхронно, они могут быть не готовы к моменту вызова **Initialize**. Поэтому после загрузки данных вы можете принудительно инициализировать однократные привязки, вызвав `this.Bindings.Update();`. Если для асинхронно загруженных данных требуются только однократные привязки, то они намного дешевле инициализировать их таким способом, чем наличие односторонних привязок и прослушивание изменений. Если в ваши данные не вносятся точечные изменения и они будут обновляться в рамках определенного действия, вы можете сделать привязки однократными и принудительно выполнять обновление вручную с помощью вызова **Update**.

> [!NOTE]
> **{КС:бинд}** не подходит для сценариев с поздним связыванием, таких как Навигация по структуре СЛОВАРЯ объекта JSON или утка ввода. «Утка типизация» — это слабая форма ввода, основанная на лексических совпадениях имен свойств (а в, «if», «if», «if», «купание» и «крякает», например утка, то это утка «»). При вводе утка привязка к свойству **Age** будет так же удовлетворена **лицом** или объектом **вина** (предполагая, что у каждого типа есть свойство **Age** ). Для этих сценариев используйте расширение разметки **{Binding}** .

### <a name="binding-object-declared-using-binding"></a>Объект привязки, объявленный с использованием расширения разметки {Binding}

Если вы C++используете/WinRT или расширения визуальных C++ компонентов (C++/CX), то для использования расширения разметки [{Binding}](https://docs.microsoft.com/windows/uwp/xaml-platform/binding-markup-extension) необходимо добавить атрибут [**биндаблеаттрибуте**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Data.BindableAttribute) в любой класс среды выполнения, к которому необходимо выполнить привязку. Чтобы использовать [{КС:бинд}](https://docs.microsoft.com/windows/uwp/xaml-platform/x-bind-markup-extension), этот атрибут не требуется.

```cppwinrt
// HostViewModel.idl
// Add this attribute:
[Windows.UI.Xaml.Data.Bindable]
runtimeclass HostViewModel : Windows.UI.Xaml.Data.INotifyPropertyChanged
...
```

> [!IMPORTANT]
> Если вы используете [ C++/WinRT](/windows/uwp/cpp-and-winrt-apis/intro-to-using-cpp-with-winrt), то атрибут [**биндаблеаттрибуте**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Data.BindableAttribute) доступен, если вы установили Windows SDK версии 10.0.17763.0 (Windows 10, 1809) или более поздней версии. Без этого атрибута необходимо реализовать интерфейсы [ICustomPropertyProvider](/uwp/api/windows.ui.xaml.data.icustompropertyprovider) и [икустомпроперти](/uwp/api/windows.ui.xaml.data.icustomproperty) , чтобы иметь возможность использовать расширение разметки [{Binding}](https://docs.microsoft.com/windows/uwp/xaml-platform/binding-markup-extension) .

Расширение разметки [{Binding}](https://docs.microsoft.com/windows/uwp/xaml-platform/binding-markup-extension) по умолчанию предполагает, что вы выполняете привязку к свойству [**DataContext**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.frameworkelement.datacontext) страницы разметки. Поэтому в качестве свойства **DataContext** страницы мы установим экземпляр нашего класса источника привязки (типа **HostViewModel** в данном случае). В примере ниже показана разметка, объявляющая объект привязки. Мы используем тот же целевой объект привязки **Button.Content**, который ранее использовался в разделе "Целевой объект привязки", и выполняем привязку к свойству **HostViewModel.NextButtonText**.

```xaml
<Page xmlns:viewmodel="using:DataBindingInDepth" ... >
    <Page.DataContext>
        <viewmodel:HostViewModel x:Name="viewModelInDataContext"/>
    </Page.DataContext>
    ...
    <Button Content="{Binding Path=NextButtonText}" ... />
</Page>
```

```csharp
// MainPage.xaml.cs
private void Button_Click(object sender, RoutedEventArgs e)
{
    this.viewModelInDataContext.NextButtonText = "Updated Next button text";
}
```

```cppwinrt
// MainPage.cpp
void MainPage::ClickHandler(IInspectable const&, RoutedEventArgs const&)
{
    viewModelInDataContext().NextButtonText(L"Updated Next button text");
}
```

Обратите внимание на значение, указанное для свойства **Path**. Это значение интерпретируется в контексте свойства [**DataContext**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.frameworkelement.datacontext) страницы, в качестве которого в этом примере задан экземпляр **HostViewModel**. Путь ссылается на свойство **HostViewModel.NextButtonText**. Свойство **Mode** можно опустить, так как здесь используется расширение разметки [{Binding}](https://docs.microsoft.com/windows/uwp/xaml-platform/binding-markup-extension) по умолчанию для односторонней привязки.

Значение свойства [**DataContext**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.frameworkelement.datacontext) по умолчанию для элемента пользовательского интерфейса наследуется от его родительского объекта. Конечно, вы можете переопределить это значение по умолчанию, явно задав свойство **DataContext**, которое в свою очередь по умолчанию наследуется дочерними элементами. Явное задание свойства **DataContext** для элемента полезно, когда вам нужны несколько привязок, использующих один источник.

У объекта привязки есть свойство **Source**, которое по умолчанию устанавливается на свойство [**DataContext**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.frameworkelement.datacontext) элемента пользовательского интерфейса, для которого объявлена привязка. Вы можете переопределить это значение по умолчанию, явно установив свойство **Source**, **RelativeSource** или **ElementName** для привязки (подробные сведения см. в статье о расширении разметки [{Binding}](https://docs.microsoft.com/windows/uwp/xaml-platform/binding-markup-extension)).

Внутри [**шаблона DataTemplate**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.DataTemplate) [**DataContext**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.frameworkelement.datacontext) автоматически устанавливается в шаблон объекта данных. Приведенный ниже пример можно использовать в качестве свойства **ItemTemplate** элемента управления, привязанного к коллекции любого типа, содержащей строковые свойства **Title** и **Description**.

```xaml
<DataTemplate x:Key="SimpleItemTemplate">
    <StackPanel Orientation="Vertical" Height="50">
      <TextBlock Text="{Binding Title}"/>
      <TextBlock Text="{Binding Description"/>
    </StackPanel>
  </DataTemplate>
```

> [!NOTE]
> По умолчанию изменения в [**TextBox. Text**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.textbox.text) отправляются в двусторонний источник, когда [**текстовое поле**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.TextBox) теряет фокус. Чтобы изменения отправлялись после каждого нажатия клавиши пользователем, установите для перечисления **UpdateSourceTrigger** событие **PropertyChanged** в привязке разметки. Вы также можете полностью взять на себя управление отправкой изменений в источник, установив для перечисления **UpdateSourceTrigger** значение **Explicit**. Затем необходимо обработать события в текстовом поле (обычно с помощью события [**TextBox.TextChanged**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.TextBox)), вызвать метод [**GetBindingExpression**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.frameworkelement.getbindingexpression) для целевого объекта, чтобы получить объект [**BindingExpression**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.data.bindingexpression), а в самом конце вызвать метод [**BindingExpression.UpdateSource**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.data.bindingexpression.updatesource) для обновления источника данных программными средствами.

Свойство [**Path**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.data.binding.path) поддерживает несколько вариантов синтаксиса для привязки к вложенным и подключенным свойствам, а также целочисленным и строковым индексаторам. Дополнительную информацию см. в разделе [Синтаксис Property-path](https://docs.microsoft.com/windows/uwp/xaml-platform/property-path-syntax). Привязка к строковым индексаторам позволяет выполнять привязку к динамическим свойствам без реализации интерфейса [**ICustomPropertyProvider**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Data.ICustomPropertyProvider). Свойство [**ElementName**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.data.binding.elementname) позволяет эффективно привязывать элементы один к другому. Свойство [**RelativeSource**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.data.binding.relativesource) можно использовать в различных целях, в том числе в качестве более эффективной альтернативы привязке шаблона внутри класса [**ControlTemplate**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.ControlTemplate). Описание других параметров см. в статьях о [расширении разметки {Binding}](https://docs.microsoft.com/windows/uwp/xaml-platform/binding-markup-extension) и классе [**Binding**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Data.Binding).

## <a name="what-if-the-source-and-the-target-are-not-the-same-type"></a>Что делать, если тип источника отличается от типа целевого объекта?

Если вы хотите управлять видимостью элемента пользовательского интерфейса на основе значения логического свойства, если элемент пользовательского интерфейса нужно преобразовать для просмотра с использованием цвета, являющегося функцией диапазона числовых значений или тренда, либо если вы хотите отобразить значение даты и/или времени в свойстве элемента пользовательского интерфейса, ожидающего строку, вам необходимо преобразовать значения из одного типа в другой. В некоторых случаях следует предоставить другое свойство правильного типа из класса источника привязки, после чего инкапсулировать логику преобразования и сделать ее возможной для тестирования. Однако это решение не отличается достаточной гибкостью и масштабируемостью, если имеется большое количество свойств источников и целевых объектов, а также их сочетаний. В этом случае есть несколько вариантов решений:

* При использовании {x:Bind} вы можете выполнить привязку напрямую к функции, чтобы осуществить это преобразование.
* Вы также можете указать преобразователь величин, который является объектом для выполнения преобразования. 

## <a name="value-converters"></a>Преобразователи величин

Ниже приведен преобразователь величин, который подходит для единовременной или односторонней привязки и преобразует значение [**DateTime**](https://docs.microsoft.com/dotnet/api/system.datetime) в строковое значение, содержащее месяц. Класс реализует интерфейс [**IValueConverter**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Data.IValueConverter).

```csharp
public class DateToStringConverter : IValueConverter
{
    // Define the Convert method to convert a DateTime value to 
    // a month string.
    public object Convert(object value, Type targetType, 
        object parameter, string language)
    {
        // value is the data from the source object.
        DateTime thisdate = (DateTime)value;
        int monthnum = thisdate.Month;
        string month;
        switch (monthnum)
        {
            case 1:
                month = "January";
                break;
            case 2:
                month = "February";
                break;
            default:
                month = "Month not found";
                break;
        }
        // Return the value to pass to the target.
        return month;
    }

    // ConvertBack is not implemented for a OneWay binding.
    public object ConvertBack(object value, Type targetType, 
        object parameter, string language)
    {
        throw new NotImplementedException();
    }
}
```

```cppwinrt
// See the "Formatting or converting data values for display" section in the "Data binding overview" topic.
```

Ниже показано, как использовать этот преобразователь величин в разметке объекта привязки.

```xaml
<UserControl.Resources>
  <local:DateToStringConverter x:Key="Converter1"/>
</UserControl.Resources>
...
<TextBlock Grid.Column="0" 
  Text="{x:Bind ViewModel.Month, Converter={StaticResource Converter1}}"/>
<TextBlock Grid.Column="0" 
  Text="{Binding Month, Converter={StaticResource Converter1}}"/>
```

Модуль привязки вызывает методы [**Convert**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.data.ivalueconverter.convert) и [**ConvertBack**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.data.ivalueconverter.convertback), если для привязки определен параметр [**Converter**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.data.binding.converter). Когда данные передаются из источника, модуль привязки вызывает метод **Convert** и передает возвращенные данные в целевой объект. Когда данные передаются из целевого объекта (при двусторонней привязке), модуль привязки вызывает метод **ConvertBack** и передает возвращенные данные в источник.

Преобразователь также включает дополнительные параметры: [**ConverterLanguage**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.data.binding.converterlanguage), позволяющий указать язык, который используется в преобразовании, и [**ConverterParameter**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.data.binding.converterparameter), позволяющий передать параметр для логики преобразования. Пример использования параметра преобразователя см. в описании [**IValueConverter**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Data.IValueConverter).

> [!NOTE]
> Если при преобразовании возникает ошибка, не вызывайте исключение. Вместо этого возвращайте свойство [**DependencyProperty.UnsetValue**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.dependencyproperty.unsetvalue), которое остановит передачу данных.

Чтобы отображать значение по умолчанию, которое используется, когда невозможно разрешить источник привязки, задайте свойство **FallbackValue** для объекта привязки в разметке. Это полезно при обработке ошибок преобразования и форматирования. Это также полезно при привязке к свойствам источника, которые могут существовать не у всех объектов в привязанной коллекции различных типов.

Если вы привязываете текстовый элемент управления к значению, которое не является строкой, модуль привязки данных будет преобразовывать значение в строку. Если значение является ссылочным типом, обработчик привязки данных будет извлекать строковое значение, вызывая [**ICustomPropertyProvider.GetStringRepresentation**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.data.icustompropertyprovider.getstringrepresentation) или [**IStringable.ToString**](https://docs.microsoft.com/windows/desktop/api/windows.foundation/nf-windows-foundation-istringable-tostring), если они доступны, а в противном случае вызывать [**Object.ToString**](https://docs.microsoft.com/dotnet/api/system.object.tostring#System_Object_ToString). Однако отметим, что обработчик привязки данных будет игнорировать любые реализации **ToString**, которые скрывают реализацию базового класса. Вместо этого реализации подкласса должны переопределять метод базового класса **ToString**. Аналогично, в машинных языках все управляемые объекты должны реализовывать интерфейсы [**ICustomPropertyProvider**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Data.ICustomPropertyProvider) и [**IStringable**](https://docs.microsoft.com/windows/desktop/api/windows.foundation/nn-windows-foundation-istringable). Однако все вызовы **GetStringRepresentation** и **IStringable.ToString** направляются к **Object.ToString** или переопределению этого метода, но никогда к новой реализации **ToString**, которая скрывает реализацию базового класса.

> [!NOTE]
> Начиная с Windows 10 версии 1607, платформа XAML предоставляет встроенный преобразователь Boolean в Visibility. Преобразователь сопоставляет значение **true** значению перечисления **Visible**, а значение **false** значению **Collapsed**, поэтому можно осуществить привязку свойства Visibility к Boolean без создания преобразователя. Для использования встроенного преобразователя минимальная версия целевого пакета SDK вашего приложения должна быть 14393 или более поздней. Вы не сможете использовать преобразователь, если ваше приложение предназначено для более ранних версий Windows 10. Дополнительные сведения о целевых версиях см. в статье [Адаптивный к версии код](https://docs.microsoft.com/windows/uwp/debug-test-perf/version-adaptive-code).

## <a name="function-binding-in-xbind"></a>Привязка функций в {x:Bind}

{x:Bind} позволяет использовать функцию на последнем этапе привязки. Этим можно пользоваться для выполнения преобразований, а также привязок, зависящих от нескольких свойств. См. раздел [ **функции в x:BIND**](function-bindings.md)

<span id="resource-dictionaries-with-x-bind"/>

## <a name="element-to-element-binding"></a>Привязка между элементами

Вы можете привязать свойство одного элемента XAML к свойству другого элемента XAML. Вот как это выглядит в разметке:

```xaml
<TextBox x:Name="myTextBox" />
<TextBlock Text="{x:Bind myTextBox.Text, Mode=OneWay}" />
```

> [!IMPORTANT]
> Сведения о том, как выполнить необходимый рабочий процесс для привязки элемента C++к элементу с помощью/WinRT, см. в разделе [Привязка элемента к](/windows/uwp/cpp-and-winrt-apis/binding-property#element-to-element-binding)элементу.

## <a name="resource-dictionaries-with-xbind"></a>Использование расширения разметки {x:Bind} в словарях ресурсов

[Расширение разметки {x:Bind}](https://docs.microsoft.com/windows/uwp/xaml-platform/x-bind-markup-extension) зависит от формирования кода, поэтому ему требуется файл кода программной части, содержащий конструктор, который вызывает метод **InitializeComponent** (для инициализации сформированного кода). Чтобы повторно использовать словарь ресурсов, нужно создать экземпляра его типа (для вызова метода **InitializeComponent**), а не ссылаться на его имя файла. Вот пример того, что делать при наличии имеющегося словаря ресурсов, в котором вы хотите использовать расширение разметки {x:Bind}.

TemplatesResourceDictionary.xaml

```xaml
<ResourceDictionary
    x:Class="ExampleNamespace.TemplatesResourceDictionary"
    .....
    xmlns:examplenamespace="using:ExampleNamespace">
    
    <DataTemplate x:Key="EmployeeTemplate" x:DataType="examplenamespace:IEmployee">
        <Grid>
            <TextBlock Text="{x:Bind Name}"/>
        </Grid>
    </DataTemplate>
</ResourceDictionary>
```

TemplatesResourceDictionary.xaml.cs

```csharp
using Windows.UI.Xaml.Data;
 
namespace ExampleNamespace
{
    public partial class TemplatesResourceDictionary
    {
        public TemplatesResourceDictionary()
        {
            InitializeComponent();
        }
    }
}
```

MainPage.xaml

```xaml
<Page x:Class="ExampleNamespace.MainPage"
    ....
    xmlns:examplenamespace="using:ExampleNamespace">

    <Page.Resources>
        <ResourceDictionary>
            .... 
            <ResourceDictionary.MergedDictionaries>
                <examplenamespace:TemplatesResourceDictionary/>
            </ResourceDictionary.MergedDictionaries>
        </ResourceDictionary>
    </Page.Resources>
</Page>
```

## <a name="event-binding-and-icommand"></a>Привязка событий и ICommand

Расширение разметки [{x:Bind}](https://docs.microsoft.com/windows/uwp/xaml-platform/x-bind-markup-extension) поддерживает функцию, которая называется привязкой событий. Эта функция позволяет указать обработчик для события посредством привязки, которая дополняет события обработки с методом в файле кода программной части. Предположим, у вас есть свойство **RootFrame** в классе **MainPage**.

```csharp
public sealed partial class MainPage : Page
{
    ...
    public Frame RootFrame { get { return Window.Current.Content as Frame; } }
}
```

Затем вы можете привязать событие **Click** кнопки к методу объекта **Frame**, возвращенного свойством **RootFrame**, как показано ниже. Обратите внимание, что мы также привязываем свойство **IsEnabled** кнопки к другому члену того же объекта **Frame**.

```xaml
<AppBarButton Icon="Forward" IsCompact="True"
IsEnabled="{x:Bind RootFrame.CanGoForward, Mode=OneWay}"
Click="{x:Bind RootFrame.GoForward}"/>
```

Для обработки события этим способом невозможно использовать перегруженные методы. Кроме того, если метод, который обрабатывает событие, содержит параметры, они должны поддерживать назначение из типов всех соответствующих параметров события. В этом случае метод [**Frame.GoForward**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.frame.goforward) не перегружен и не содержит параметров (но он оставался бы допустимым, даже если бы принимал два параметра **object**). Однако [**Frame. GoBack**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.frame.goback) перегружен, поэтому мы не можем использовать этот метод с этим методом.

Привязка событий похожа на реализацию и использование команд (команда — это свойство, которое возвращает объект, реализующий интерфейс [**ICommand**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.input.icommand)). С командами можно использовать как [{x:Bind}](https://docs.microsoft.com/windows/uwp/xaml-platform/x-bind-markup-extension), так и [{Binding}](https://docs.microsoft.com/windows/uwp/xaml-platform/binding-markup-extension). Чтобы приведенный выше шаблон команд не требовалось реализовывать несколько раз, вы можете использовать вспомогательный класс **DelegateCommand**, приведенный в примере [QuizGame](https://github.com/microsoft/Windows-appsample-networkhelper) (в папке "Общие").

## <a name="binding-to-a-collection-of-folders-or-files"></a>Привязка к коллекции папок или файлов

Для извлечения данных из папок и файлов можно использовать API в пространстве имен [**Windows.Storage**](https://docs.microsoft.com/uwp/api/Windows.Storage). Однако разные методы **GetFilesAsync**, **GetFoldersAsync** и **GetItemsAsync** не возвращают значения, которые подходят для привязки к элементам управления списком. Вместо этого необходимо выполнить привязку к возвращаемым значениям методов [**GetVirtualizedFilesVector**](https://docs.microsoft.com/uwp/api/windows.storage.bulkaccess.fileinformationfactory.getvirtualizedfilesvector), [**GetVirtualizedFoldersVector**](https://docs.microsoft.com/uwp/api/windows.storage.bulkaccess.fileinformationfactory.getvirtualizedfoldersvector) и [**GetVirtualizedItemsVector**](https://docs.microsoft.com/uwp/api/windows.storage.bulkaccess.fileinformationfactory.getvirtualizeditemsvector) класса [**FileInformationFactory**](https://docs.microsoft.com/uwp/api/Windows.Storage.BulkAccess.FileInformationFactory). В приведенном ниже примере кода из [статьи с примером StorageDataSource и GetVirtualizedFilesVector](https://go.microsoft.com/fwlink/p/?linkid=228621) демонстрируется типичное использование. Не забудьте объявить возможность **picturesLibrary** в манифесте пакета приложения и убедитесь, что в вашей папке библиотеки изображений есть изображения.

```csharp
protected override void OnNavigatedTo(NavigationEventArgs e)
{
    var library = Windows.Storage.KnownFolders.PicturesLibrary;
    var queryOptions = new Windows.Storage.Search.QueryOptions();
    queryOptions.FolderDepth = Windows.Storage.Search.FolderDepth.Deep;
    queryOptions.IndexerOption = Windows.Storage.Search.IndexerOption.UseIndexerWhenAvailable;

    var fileQuery = library.CreateFileQueryWithOptions(queryOptions);

    var fif = new Windows.Storage.BulkAccess.FileInformationFactory(
        fileQuery,
        Windows.Storage.FileProperties.ThumbnailMode.PicturesView,
        190,
        Windows.Storage.FileProperties.ThumbnailOptions.UseCurrentScale,
        false
        );

    var dataSource = fif.GetVirtualizedFilesVector();
    this.PicturesListView.ItemsSource = dataSource;
}
```

Этот подход обычно используется для создания доступных только для чтения сведений о файлах или папке. Можно создать двухсторонние привязки к свойствам файлов и папок, например, чтобы позволить пользователям оценивать композиции в музыкальном представлении. Однако любые изменения сохраняются только после вызова соответствующего метода **SavePropertiesAsync** (например, [**MusicProperties.SavePropertiesAsync**](https://docs.microsoft.com/uwp/api/windows.storage.fileproperties.musicproperties.savepropertiesasync)). Когда элемент теряет фокус, изменения следует принять, так как это инициирует сброс выбранных элементов.

Обратите внимание, что двухсторонняя привязка на основе этой методики работает только с проиндексированными расположениями, такими как "Музыка". Для проверки индексации расположения можно вызвать метод [**FolderInformation.GetIndexedStateAsync**](https://docs.microsoft.com/uwp/api/windows.storage.bulkaccess.folderinformation.getindexedstateasync).

Кроме того, следует иметь в виду, что виртуализированный вектор для некоторых элементов может возвратить значение **null** до заполнения их значений. Необходимо, например, выполнять проверку на **null** перед использованием значения [**SelectedItem**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.primitives.selector.selecteditem) в элементе управления "Список", который привязан к виртуализированному вектору, или использовать вместо этого свойство [**SelectedIndex**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.primitives.selector.selectedindex).

## <a name="binding-to-data-grouped-by-a-key"></a>Привязка к данным, сгруппированным на основании ключа

Если вы принимаете плоскую коллекцию элементов (книги, например, представленные классом **букску** ) и группируюте элементы, используя общее свойство в качестве ключа (например, свойство **букску. аусорнаме** ), результат называется сгруппированными данными. При группировке данные больше не являются плоской коллекцией. Сгруппированные данные — это коллекция объектов-групп, в которой каждый объект группы имеет

- ключ и
- Коллекция элементов, свойство которых соответствует этому ключу.

Чтобы снова взять пример книги, результат группирования книг по имени автора приведет к созданию коллекции групп имен авторов, в которой каждая группа имеет

- ключ, который является именем автора;
- Коллекция **букску**, свойство **аусорнаме** которых соответствует ключу группы.

В общем, для отображения коллекции необходимо привязать свойство [**ItemsSource**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.itemscontrol.itemssource) элемента управления (например, [**ListView**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.ListView) или [**GridView**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.GridView)) непосредственно к свойству, которое возвращает коллекцию. Если это плоская коллекция элементов, вам не нужно выполнять никаких особенных действий. Однако если это коллекция объектов группы (что наблюдается при привязке к сгруппированным данным), вам нужно воспользоваться объектом-посредником с именем [**CollectionViewSource**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Data.CollectionViewSource), который размещается между элементом управления и источником привязки. Объект **CollectionViewSource** следует привязать к свойству, возвращающему сгруппированные данные, а элемент управления — к объекту **CollectionViewSource**. Дополнительное добавление объекта **CollectionViewSource** обеспечивает отслеживание с его помощью текущего элемента, поэтому вы можете синхронизировать несколько элементов управления путем их привязки к **CollectionViewSource**. Кроме того, вы можете получить доступ к текущему элементу программными средствами с помощью свойства [**ICollectionView.CurrentItem**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.data.icollectionview.currentitem) объекта, возвращенного свойством [**CollectionViewSource.View**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.data.collectionviewsource.view).

Чтобы активировать возможность группирования с помощью класса [**CollectionViewSource**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Data.CollectionViewSource), установите для свойства [**IsSourceGrouped**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.data.collectionviewsource.issourcegrouped) значение **true**. Целесообразность одновременного задания свойства [**ItemsPath**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.data.collectionviewsource.itemspath) зависит от того, как именно вы создаете объекты группы. Объект группы можно создать двумя способами: с помощью шаблона "is-a-group" или "has-a-group". В шаблоне "is-a-group" объект группы создается на основе типа коллекции (например, **List&lt;T&gt;** ), поэтому сам объект группы является группой элементов. Если используется этот шаблон, не нужно задавать свойство **ItemsPath**. В шаблоне "has-a-group" объект группы включает одно или несколько свойств типа коллекции (например, **List&lt;T&gt;** ), поэтому группа включает группу элементов в форме свойства (или несколько групп элементов в виде ряда свойств). При использовании этого шаблона для **ItemsPath** необходимо задать имя свойства, содержащего группу элементов.

В примере ниже приведен шаблон has-a-group. Класс страницы имеет свойство [**ViewModel**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.frameworkelement.datacontext), которое возвращает экземпляр нашей модели представления. [  **CollectionViewSource**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Data.CollectionViewSource) привязывается к свойству **Authors** модели представления (**Authors** — это коллекция объектов группы), а также указывает, что это свойство **Author.BookSkus** содержит сгруппированные элементы. Наконец, класс [**GridView**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.GridView) привязан к классу **CollectionViewSource**, а его стиль группы определен таким образом, чтобы он мог обрабатывать элементы в группах.

```xaml
<Page.Resources>
    <CollectionViewSource
    x:Name="AuthorHasACollectionOfBookSku"
    Source="{x:Bind ViewModel.Authors}"
    IsSourceGrouped="true"
    ItemsPath="BookSkus"/>
</Page.Resources>
...
<GridView
ItemsSource="{x:Bind AuthorHasACollectionOfBookSku}" ...>
    <GridView.GroupStyle>
        <GroupStyle
            HeaderTemplate="{StaticResource AuthorGroupHeaderTemplateWide}" ... />
    </GridView.GroupStyle>
</GridView>
```

Вы можете реализовать шаблон "is-a-group" одним из двух способов. Первый способ — создать собственный класс группы. Создайте класс на основе **List&lt;T&gt;** (где *T* — это тип элементов). Например, `public class Author : List<BookSku>`. Второй способ — использовать выражение [LINQ](https://docs.microsoft.com/previous-versions/bb397926(v=vs.140)) для динамического создания объектов группы (и класса группы) из вероятных значений свойств элементов **BookSku**. Этот подход с поддержкой только плоского списка элементов и их группированием в ходе процесса обычно используется приложением, которое получает доступ к данным из облачной службы. Это позволяет гибко группировать книги по авторам или жанрам (к примеру) без специальных классов групп, таких как **Author** и **Genre**.

В примере ниже показан шаблон "is-a-group", использующий выражение [LINQ](https://docs.microsoft.com/previous-versions/bb397926(v=vs.140)). На этот раз мы группируем книги по жанрам, причем названия жанров отображаются в заголовках групп. На это указывает путь свойства Key в ссылке на значение [**Key**](https://docs.microsoft.com/dotnet/api/system.linq.igrouping-2.key#System_Linq_IGrouping_2_Key) группы.

```csharp
using System.Linq;
...
private IOrderedEnumerable<IGrouping<string, BookSku>> genres;

public IOrderedEnumerable<IGrouping<string, BookSku>> Genres
{
    get
    {
        if (this.genres == null)
        {
            this.genres = from book in this.bookSkus
                          group book by book.genre into grp
                          orderby grp.Key
                          select grp;
        }
        return this.genres;
    }
}
```

Помните, что при использовании расширения разметки [{x:Bind}](https://docs.microsoft.com/windows/uwp/xaml-platform/x-bind-markup-extension) с шаблонами данных необходимо указать тип, к которому выполняется привязка, установив значение **x:DataType**. Если это универсальный тип, мы не сможем отобразить это в разметке, поэтому в шаблоне заголовка стиля группы необходимо использовать расширение разметки [{Binding}](https://docs.microsoft.com/windows/uwp/xaml-platform/binding-markup-extension).

```xaml
    <Grid.Resources>
        <CollectionViewSource x:Name="GenreIsACollectionOfBookSku"
        Source="{x:Bind Genres}"
        IsSourceGrouped="true"/>
    </Grid.Resources>
    <GridView ItemsSource="{x:Bind GenreIsACollectionOfBookSku}">
        <GridView.ItemTemplate x:DataType="local:BookTemplate">
            <DataTemplate>
                <TextBlock Text="{x:Bind Title}"/>
            </DataTemplate>
        </GridView.ItemTemplate>
        <GridView.GroupStyle>
            <GroupStyle>
                <GroupStyle.HeaderTemplate>
                    <DataTemplate>
                        <TextBlock Text="{Binding Key}"/>
                    </DataTemplate>
                </GroupStyle.HeaderTemplate>
            </GroupStyle>
        </GridView.GroupStyle>
    </GridView>
```

С помощью элемента управления [**SemanticZoom**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.SemanticZoom) пользователи могут эффективно просматривать сгруппированные данные и переходить между ними. Пример приложения [Bookstore2](https://go.microsoft.com/fwlink/?linkid=532952) демонстрирует, как использовать элемент управления **SemanticZoom**. В этом приложении вы можете просмотреть список книг, сгруппированных по автору (представление с увеличенным масштабом). Уменьшите масштаб представления, чтобы просмотреть список переходов между авторами. Список переходов — это гораздо более быстрый способ навигации, чем прокрутка списка книг. Увеличенное и уменьшенное представления являются элементами управления **ListView** и **GridView**, привязанными к одному классу **CollectionViewSource**.

![Иллюстрация элемента управления SemanticZoom](images/sezo.png)

Привязывая иерархические данные, такие как подкатегории в пределах категории, вы можете отобразить уровни иерархии в своем пользовательском интерфейсе с помощью ряда элементов управления. Объекты, выбранные в одном элементе управления, определяют содержимое последующих элементов управления. Списки можно синхронизировать, привязав каждый из них к его классу [**CollectionViewSource**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Data.CollectionViewSource), а также привязав экземпляры **CollectionViewSource** в виде цепи. Это называется главным или подробным представлением (либо представлением списка или сведений). Дополнительные сведения см. в [статье, посвященной привязке к иерархическим данным и созданию главного или подробного представления](how-to-bind-to-hierarchical-data-and-create-a-master-details-view.md).

<span id="debugging"/>

## <a name="diagnosing-and-debugging-data-binding-problems"></a>Диагностика и отладка проблем, связанных с привязкой данных

Ваша разметка привязки включает имена свойств (а для C# иногда также поля и методы). Поэтому при переименовании свойства вам также понадобится изменить все привязки, которые ссылаются на него. Невыполнение этого требования может привести к типичному примеру ошибки при привязке данных. В таком случае приложение не будет скомпилировано или будет работать неправильно.

Как правило, объекты привязки, создаваемые с помощью расширений разметки [{x:Bind}](https://docs.microsoft.com/windows/uwp/xaml-platform/x-bind-markup-extension) и [{Binding}](https://docs.microsoft.com/windows/uwp/xaml-platform/binding-markup-extension), выполняют аналогичные функции. Однако расширение разметки {x:Bind} включает сведения о типе источника привязки, что обеспечивает формирование исходного кода во время компиляции. При использовании расширения разметки {x:Bind} обнаружение проблем выполняется так же, как с помощью остальной части кода. Действия по обнаружению включают проверку выражений привязки во время компиляции и отладку путем вставки точек останова в исходный код, сформированный в качестве разделяемого класса для страницы. Эти классы можно найти в папке `obj` в файлах с такими именами, как (для C#) `<view name>.g.cs`. Если при привязке возникает проблема, установите флажок **Останавливать при неперехваченных исключениях** в отладчике Microsoft Visual Studio. Отладчик прервет выполнение в этой точке, после чего вы можете перейти к отладке. В коде, формируемом расширением разметки {x:Bind}, применяется один шаблон для каждой части графа узлов источника привязки. С помощью сведений в окне **Стек вызовов** вы можете определить последовательность вызовов, которые привели к проблеме.

В расширении разметки [{Binding}](https://docs.microsoft.com/windows/uwp/xaml-platform/binding-markup-extension) не указываются сведения о типе источника привязки. Однако, когда приложение выполняется с подключенным отладчиком, любые ошибки привязки отображаются в окне **Вывод** приложения Visual Studio.

## <a name="creating-bindings-in-code"></a>Создание привязок в коде

**Примечание** .   This раздел применяется только к [{Binding}](https://docs.microsoft.com/windows/uwp/xaml-platform/binding-markup-extension), так как в коде нельзя создавать привязки [{КС:бинд}](https://docs.microsoft.com/windows/uwp/xaml-platform/x-bind-markup-extension) . Однако некоторые из преимуществ {x:Bind} можно получить с помощью метода [**DependencyObject.RegisterPropertyChangedCallback**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.dependencyobject.registerpropertychangedcallback), что позволяет зарегистрироваться для получения уведомлений об изменениях любого свойства зависимости.

Вы также можете связывать элементы пользовательского интерфейса в процедурах в коде вместо использования XAML. Для этого создайте новый объект [**Binding**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Data.Binding), задайте соответствующие свойства, а затем вызовите [**FrameworkElement.SetBinding**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.frameworkelement.setbinding) или [**BindingOperations.SetBinding**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.data.bindingoperations.setbinding). Создание привязок программными средствами полезно, когда требуется выбирать значения свойств привязки во время выполнения или совместно использовать одну привязку в нескольких элементах управления. Однако отметим, что невозможно использовать значения свойств привязки после вызова **SetBinding**.

В следующем примере показано, как реализовать привязку в коде.

```xaml
<TextBox x:Name="MyTextBox" Text="Text"/>
```

```csharp
// Create an instance of the MyColors class 
// that implements INotifyPropertyChanged.
MyColors textcolor = new MyColors();

// Brush1 is set to be a SolidColorBrush with the value Red.
textcolor.Brush1 = new SolidColorBrush(Colors.Red);

// Set the DataContext of the TextBox MyTextBox.
MyTextBox.DataContext = textcolor;

// Create the binding and associate it with the text box.
Binding binding = new Binding() { Path = new PropertyPath("Brush1") };
MyTextBox.SetBinding(TextBox.ForegroundProperty, binding);
```

```vbnet
' Create an instance of the MyColors class 
' that implements INotifyPropertyChanged. 
Dim textcolor As New MyColors()

' Brush1 is set to be a SolidColorBrush with the value Red. 
textcolor.Brush1 = New SolidColorBrush(Colors.Red)

' Set the DataContext of the TextBox MyTextBox. 
MyTextBox.DataContext = textcolor

' Create the binding and associate it with the text box.
Dim binding As New Binding() With {.Path = New PropertyPath("Brush1")}
MyTextBox.SetBinding(TextBox.ForegroundProperty, binding)
```

## <a name="xbind-and-binding-feature-comparison"></a>Сравнение компонентов расширений разметки {x:Bind} и {Binding}

| Возможность | {x:Bind} | {Binding} | Заметки |
|---------|----------|-----------|-------|
| Path — свойство по умолчанию | `{x:Bind a.b.c}` | `{Binding a.b.c}` | | 
| Свойство Path | `{x:Bind Path=a.b.c}` | `{Binding Path=a.b.c}` | В расширении разметки x:Bind свойство Path по умолчанию размещается в корне класса Page, а не DataContext. | 
| Индексатор | `{x:Bind Groups[2].Title}` | `{Binding Groups[2].Title}` | Привязывается к определенному элементу в коллекции. Поддерживаются только целочисленные индексы. | 
| Присоединенные свойства | `{x:Bind Button22.(Grid.Row)}` | `{Binding Button22.(Grid.Row)}` | Присоединенные свойства указываются в скобках. Если свойство не объявляется в пространстве имен XAML, нужно установить для него префикс пространства имен xml, которое необходимо сопоставить с пространством имен кода в заголовке документа. | 
| Приведение | `{x:Bind groups[0].(data:SampleDataGroup.Title)}` | Не требуется. | Приведения указываются в скобках. Если свойство не объявляется в пространстве имен XAML, нужно установить для него префикс пространства имен xml, которое необходимо сопоставить с пространством имен кода в заголовке документа. | 
| Преобразователь | `{x:Bind IsShown, Converter={StaticResource BoolToVisibility}}` | `{Binding IsShown, Converter={StaticResource BoolToVisibility}}` | Преобразователи необходимо объявлять в корне класса Page или ResourceDictionary или в App.xaml. | 
| ConverterParameter, ConverterLanguage | `{x:Bind IsShown, Converter={StaticResource BoolToVisibility}, ConverterParameter=One, ConverterLanguage=fr-fr}` | `{Binding IsShown, Converter={StaticResource BoolToVisibility}, ConverterParameter=One, ConverterLanguage=fr-fr}` | Преобразователи необходимо объявлять в корне класса Page или ResourceDictionary или в App.xaml. | 
| TargetNullValue | `{x:Bind Name, TargetNullValue=0}` | `{Binding Name, TargetNullValue=0}` | Используется, когда конечный объект выражения привязки имеет значение Null. Строковое значение заключается в одиночные кавычки. | 
| FallbackValue | `{x:Bind Name, FallbackValue='empty'}` | `{Binding Name, FallbackValue='empty'}` | Используется, когда любая часть пути для привязки (кроме конечного объекта) имеет значение Null. | 
| ElementName | `{x:Bind slider1.Value}` | `{Binding Value, ElementName=slider1}` | Если используется расширение разметки {x:Bind}, выполняется привязка к полю. Свойство Path по умолчанию размещается в корне класса Page, поэтому данное поле позволяет получить доступ к любому именованному элементу. | 
| RelativeSource: Self | `<Rectangle x:Name="rect1" Width="200" Height="{x:Bind rect1.Width}" ... />` | `<Rectangle Width="200" Height="{Binding Width, RelativeSource={RelativeSource Self}}" ... />` | Если используется расширение разметки {x:Bind}, присвойте элементу имя и укажите его в свойстве Path. | 
| RelativeSource: TemplatedParent | Не требуется | `{Binding <path>, RelativeSource={RelativeSource TemplatedParent}}` | With {КС:бинд} TargetType on ControlTemplate указывает на привязку к родительскому шаблону. Для большинства случаев в шаблонах элементов управления можно использовать обычную привязку шаблона {Binding}. Однако, если вам требуется преобразователь или двусторонняя привязка, используйте TemplatedParent.< | 
| Источник | Не требуется | `<ListView ItemsSource="{Binding Orders, Source={StaticResource MyData}}"/>` | Для {КС:бинд} можно напрямую использовать именованный элемент, использовать свойство или статический путь. | 
| "Режим" | `{x:Bind Name, Mode=OneWay}` | `{Binding Name, Mode=TwoWay}` | "Режим" может иметь значение OneTime, OneWay или TwoWay. В расширении разметки {x:Bind} по умолчанию используется значение OneTime, а в расширении разметки {Binding} — значение OneWay. | 
| UpdateSourceTrigger | `{x:Bind Name, Mode=TwoWay, UpdateSourceTrigger=PropertyChanged}` | `{Binding UpdateSourceTrigger=PropertyChanged}` | У UpdateSourceTrigger может быть значение "По умолчанию", LostFocus или PropertyChanged. Поведение разметки {x:Bind} не поддерживает UpdateSourceTrigger=Explicit. В расширении разметки {x:Bind} поведение PropertyChanged используется во всех случаях, кроме TextBox.Text, где используется поведение LostFocus. | 



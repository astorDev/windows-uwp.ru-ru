---
ms.assetid: 41E1B4F1-6CAF-4128-A61A-4E400B149011
title: Подробно о привязке данных
description:Привязка данных обеспечивает отображение данных в пользовательском интерфейсе приложения и (дополнительно) синхронизацию с этими данными.
---
# Подробно о привязке данных

\[ Обновлено для приложений UWP в Windows 10. Статьи о Windows 8.x см. в [архиве](http://go.microsoft.com/fwlink/p/?linkid=619132) \]


** Важные API **

-   [**Класс Binding**](https://msdn.microsoft.com/library/windows/apps/BR209820)
-   [**DataContext**](https://msdn.microsoft.com/library/windows/apps/BR208713)
-   [**INotifyPropertyChanged**](https://msdn.microsoft.com/library/windows/apps/BR209899)

**Примечание.** В этой статье подробно описаны средства привязки данных. Краткое практическое введение см. в [Обзоре привязки данных](data-binding-quickstart.md).

 

Привязка данных обеспечивает отображение данных в пользовательском интерфейсе приложения и (дополнительно) синхронизацию с этими данными. Используя привязку данных, вы можете отделить вопросы обработки данных от вопросов, связанных с пользовательским интерфейсом. Это упрощает концептуальную модель, а также улучшает удобочитаемость, возможности тестирования и удобство поддержки приложения.

Привязку данных можно использовать, чтобы просто отобразить значения из источника данных при первом отображении пользовательского интерфейса, не реагируя на изменения этих значений. Это называется единовременной привязкой. Такую привязку целесообразно использовать для данных, значения которых не меняются во время выполнения. Кроме того, вы можете "отслеживать" значения и обновлять пользовательский интерфейс при их изменении. Это называется односторонней привязкой. Такую привязку рекомендуется применять для данных, доступных только для чтения. Наконец, вы можете как отслеживать, так и обновлять данные, чтобы изменения, которые пользователь вносит в значения в пользовательском интерфейсе, автоматически отправлялись в источник данных. Это называется двусторонней привязкой. Такую привязку рекомендуется применять для данных, доступных только для записи. Вот несколько примеров.

-   С помощью единовременной привязки вы можете привязать объект [**Image**](https://msdn.microsoft.com/library/windows/apps/BR242752) к фотографии текущего пользователя.
-   Односторонняя привязка позволяет привязать объект [**ListView**](https://msdn.microsoft.com/library/windows/apps/BR242878) к коллекции актуальных новостных статей, сгруппированных по разделу газеты.
-   С помощью двусторонней привязки вы можете привязать объект [**TextBox**](https://msdn.microsoft.com/library/windows/apps/BR209683) к имени пользователя в форме.

Существует два типа привязки, для объявления которых обычно используется разметка пользовательского интерфейса. Вы можете использовать [расширение разметки {x:Bind}](https://msdn.microsoft.com/library/windows/apps/Mt204783) или [расширение разметки {Binding}](https://msdn.microsoft.com/library/windows/apps/Mt204782). Кроме того, можно одновременно использовать оба типа привязки в одном приложении и даже в одном элементе пользовательского интерфейса. Расширение разметки {x:Bind}, которое впервые появилось в ОС Windows 10, отличается улучшенной производительностью. Расширение разметки {Binding} выполняет больше функций. Все сведения, описанные в этом разделе, касаются обоих типов привязки, если явно не указано иное.

**Примеры приложений с расширением разметки {x:Bind}**

-   [Пример {x:Bind}](http://go.microsoft.com/fwlink/p/?linkid=619989).
-   [QuizGame](https://github.com/Microsoft/Windows-appsample-quizgame).
-   [Пример XAML UI Basics](http://go.microsoft.com/fwlink/p/?linkid=619992).

**Примеры приложений с расширением разметки {Binding}**

-   Скачайте приложение [Bookstore1](http://go.microsoft.com/fwlink/?linkid=532950).
-   Скачайте приложение [Bookstore2](http://go.microsoft.com/fwlink/?linkid=532952).

Неотъемлемые компоненты каждой привязки
------------------------------------

-   *Источник привязки*. Это источник данных для привязки, в качестве которого может выступать экземпляр любого класса, содержащего члены, значения которых вы хотите отобразить в пользовательском интерфейсе.
-   *Целевой объект привязки*. Это [**DependencyProperty**](https://msdn.microsoft.com/library/windows/apps/BR242362) класса [**FrameworkElement**](https://msdn.microsoft.com/library/windows/apps/BR208706) в пользовательском интерфейсе, отвечающий за отображение данных.
-   *Объект привязки*. Это элемент, который передает значения данных из источника в целевой объект, а также (необязательно) из целевого объекта назад в источник. Объект привязки создается во время загрузки XAML из расширения разметки [{x:Bind}](https://msdn.microsoft.com/library/windows/apps/Mt204783) или [{Binding}](https://msdn.microsoft.com/library/windows/apps/Mt204782).

В следующих разделах мы подробнее рассмотрим источник, целевой объект и объект привязки. Кроме того, мы свяжем эти разделы с помощью примера привязки содержимого кнопки к строковому свойству **NextButtonText**, которое принадлежит классу **HostViewModel**.

Источник привязки
--------------

Здесь приведен простейший пример реализации класса, который можно использовать в качестве источника привязки.

**Примечание.** Если вы используете [{Binding}](https://msdn.microsoft.com/library/windows/apps/Mt204782) с расширениями компонентов Visual C++ (C++/CX), вам также понадобится добавить атрибут [**BindableAttribute**](https://msdn.microsoft.com/library/windows/apps/Hh701872) в класс источника привязки. Если вы используете [{x:Bind}](https://msdn.microsoft.com/library/windows/apps/Mt204783), этот атрибут не нужен. Фрагмент кода см. в разделе [Добавление представления подробностей](data-binding-quickstart.md#adding-a-details-view).

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

Эта реализация класса **HostViewModel** и его свойство **NextButtonText** подходят только для единовременной привязки. Однако односторонние и двусторонние привязки используются очень часто. Привязки этих типов обеспечивают автоматическое обновление пользовательского интерфейса в результате внесения изменений в значениях данных источника привязки. Чтобы эти типы привязки работали правильно, необходимо сделать так, чтобы объект привязки "отслеживал" ваш источник привязки. Поэтому если мы хотим выполнить одно- или двустороннюю привязку к свойству **NextButtonText** в нашем примере, объект привязки должен отслеживать все изменения, которые вносятся в значение этого свойства во время выполнения.

Для этого на основе класса [**DependencyObject**](https://msdn.microsoft.com/library/windows/apps/BR242356) можно создать производный класс, представляющий собой источник привязки, и предоставить значение данных с помощью класса [**DependencyProperty**](https://msdn.microsoft.com/library/windows/apps/BR242362). Именно так класс [**FrameworkElement**](https://msdn.microsoft.com/library/windows/apps/BR208706) становится доступным для отслеживания. **FrameworkElements** — хорошие источники привязки, готовые к немедленному применению.

Более легким способом сделать класс доступным для отслеживания (что также необходимо для классов, уже имеющих базовый класс) является реализация интерфейса [**System.ComponentModel.INotifyPropertyChanged**](T:System.ComponentModel.INotifyPropertyChanged). Для этого необходимо всего лишь реализовать одно событие с именем **PropertyChanged**. Пример использования класса **HostViewModel** см. ниже.

**Примечание.** При использовании C++/CX необходимо реализовать интерфейс [**Windows::UI::Xaml::Data::INotifyPropertyChanged**](https://msdn.microsoft.com/library/windows/apps/BR209899), а класс источника привязки должен либо иметь атрибут [**BindableAttribute**](https://msdn.microsoft.com/library/windows/apps/Hh701872), либо реализовывать интерфейс [**ICustomPropertyProvider**](https://msdn.microsoft.com/library/windows/apps/BR209878).

``` csharp
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

Теперь свойство **NextButtonText** доступно для отслеживания. Когда вы создаете одно- или двустороннюю привязку к этому свойству (мы позже покажем, как сделать это), полученный объект привязки подписывается на событие **PropertyChanged**. При возникновении этого события обработчик объекта привязки получает аргумент, содержащий имя свойства, которое изменилось. Именно так объект привязки узнает, к какому значению свойства необходимо перейти для повторного считывания.

Чтобы приведенный выше шаблон не требовалось реализовывать несколько раз, вы можете просто выполнить наследование от базового класса **BindableBase**, доступного в примере [QuizGame](https://github.com/Microsoft/Windows-appsample-quizgame) (в папке Common). Пример приведен ниже.

``` csharp
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

Вызов события **PropertyChanged** с аргументом [**String.Empty**](T:System.String) или значением **null** указывает, что не все свойства индексатора в объекте необходимо считать еще раз. Это событие можно вызвать для указания того, что свойства индексатора в объекте изменены, с помощью аргумента «Item\[*indexer*\]» для конкретных индексаторов (где *indexer* — это значение индекса) или значения «Item\[\]» для всех индексаторов.

Источник привязки может рассматриваться как единичный объект, свойства которого содержат данные, или как коллекция объектов. В коде C# и Visual Basic вы можете выполнить единовременную привязку к объекту, реализующему класс [**List(Of T)**](T:System.Collections.Generic.List%601), чтобы отобразить коллекцию, которая не изменяется во время выполнения. Если используется отслеживаемая коллекция (которая поддерживает отслеживание добавления и удаления элементов), следует выполнить одностороннюю привязку к классу [**ObservableCollection(Of T)**](T:System.Collections.ObjectModel.ObservableCollection%601). В коде C++ вы можете выполнить привязку к классу [**Vector&lt;T&gt;**](https://msdn.microsoft.com/en-us/library/dn858385.aspx) как для отслеживаемых, так и неотслеживаемых коллекций. Чтобы выполнить привязку к собственным классам коллекции, используйте рекомендации из следующей таблицы.

| Сценарий                                                        | C# и VB (CLR)                                                                                                                                                                                                                                                                                                                                                                                                                   | C++/CX                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
|-----------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Привязка к объекту.                                              | Любой объект.                                                                                                                                                                                                                                                                                                                                                                                                                 | Объект должен иметь атрибут [**BindableAttribute**](https://msdn.microsoft.com/library/windows/apps/Hh701872) или реализовывать интерфейс [**ICustomPropertyProvider**](https://msdn.microsoft.com/library/windows/apps/BR209878).                                                                                                                                                                                                                                                                                                             |
| Получение обновлений об изменении свойств от привязанного объекта.                | Объект должен реализовывать интерфейс [**System.ComponentModel. INotifyPropertyChanged**](T:System.ComponentModel.INotifyPropertyChanged).                                                                                                                                                                                                                                                                                                         | Объект должен реализовывать [**Windows.UI.Xaml.Data. INotifyPropertyChanged**](https://msdn.microsoft.com/library/windows/apps/BR209899).                                                                                                                                                                                                                                                                                                                                                           |
| Привязка к коллекции.                                           | [**List(Of T)**](T:System.Collections.Generic.List%601)                                                                                                                                                                                                                                                                                                                                                                            | [**Platform::Collections::Vector&lt;T&gt;**](https://msdn.microsoft.com/en-us/library/windows/apps/xaml/hh441570.aspx)                                                                                                                                                                                                                                                                                                                                                                                         |
| Получение обновлений об изменении коллекции от привязанной коллекции.          | [**ObservableCollection(Of T)**](T:System.Collections.ObjectModel.ObservableCollection%601)                                                                                                                                                                                                                                                                                                                                        | [**Platform::Collections::Vector&lt;T&gt;**](https://msdn.microsoft.com/en-us/library/windows/apps/xaml/hh441570.aspx)                                                                                                                                                                                                                                                                                                                                                                                         |
| Реализация коллекции с поддержкой привязки.                   | Расширьте [**List(Of T)**](T:System.Collections.Generic.List%601) либо реализуйте [**IList**](T:System.Collections.IList), [**IList**](T:System.Collections.Generic.IList%601)(Of [**Object**](T:System.Object)), [**IEnumerable**](T:System.Collections.IEnumerable) или [**IEnumerable**](T:System.Collections.Generic.IEnumerable%601)(Of **Object**). Привязка к универсальным интерфейсам **IList(Of T)** и **IEnumerable(Of T)** не поддерживается. | Реализуйте [**IBindableVector**](https://msdn.microsoft.com/library/windows/apps/Hh701979), [**IBindableIterable**](https://msdn.microsoft.com/library/windows/apps/Hh701957), [**IVector**](https://msdn.microsoft.com/library/windows/apps/BR206631)&lt;[**Object**](T:System.Object)^&gt;, [**IIterable**](https://msdn.microsoft.com/library/windows/apps/BR226024)&lt;**Object**^&gt;, **IVector**&lt;[**IInspectable**](https://msdn.microsoft.com/library/BR205821)\*&gt; или **IIterable**&lt;**IInspectable**\*&gt;. Привязка к универсальным интерфейсам **IVector&lt;T&gt;** и **IIterable&lt;T&gt;** не поддерживается. |
| Реализация коллекции, которая поддерживает обновления изменения коллекции. | Расширьте [**ObservableCollection(Of T)**](T:System.Collections.ObjectModel.ObservableCollection%601) или реализуйте (не универсальные) интерфейсы [**IList**](T:System.Collections.IList) и [**INotifyCollectionChanged**](T:System.Collections.Specialized.INotifyCollectionChanged).                                                                                                                                                               | Реализуйте [**IBindableVector**](https://msdn.microsoft.com/library/windows/apps/Hh701979) и [**IBindableObservableVector**](https://msdn.microsoft.com/library/windows/apps/Hh701974).                                                                                                                                                                                                                                                                                                                       |
| Реализуйте коллекцию с поддержкой инкрементной нагрузки.       | Расширьте [**ObservableCollection(Of T)**](T:System.Collections.ObjectModel.ObservableCollection%601) или реализуйте (не универсальные) интерфейсы [**IList**](T:System.Collections.IList) и [**INotifyCollectionChanged**](T:System.Collections.Specialized.INotifyCollectionChanged). Кроме того, реализуйте [**ISupportIncrementalLoading**](https://msdn.microsoft.com/library/windows/apps/Hh701916).                                                          | Реализуйте [**IBindableVector**](https://msdn.microsoft.com/library/windows/apps/Hh701979), [**IBindableObservableVector**](https://msdn.microsoft.com/library/windows/apps/Hh701974) и [**ISupportIncrementalLoading**](https://msdn.microsoft.com/library/windows/apps/Hh701916).                                                                                                                                                                                                                                         |

 
Элементы управления списками можно привязать к источникам данных любого размера и сохранить высокую производительность благодаря использованию инкрементной нагрузки. Например, элементы управления списками можно привязать к результатам запроса изображений в Bing, причем одновременная загрузка всех результатов не требуется. Вместо этого сразу можно загрузить лишь некоторые результаты и загружать дополнительные результаты по мере необходимости. Для поддержки инкрементной нагрузки интерфейс [**ISupportIncrementalLoading**](https://msdn.microsoft.com/library/windows/apps/Hh701916) необходимо реализовать в источнике данных с уведомлением об изменении коллекции. Когда механизм привязки данных запрашивает больше данных, источник данных должен делать соответствующие запросы, объединять результаты, а затем отправлять соответствующие уведомления для обновления пользовательского интерфейса.

Целевой объект привязки
--------------

В двух примерах ниже свойство **Button.Content** представляет собой целевой объект привязки, и в качестве его значения устанавливается расширение разметки, которое объявляет объект привязки. Сначала отображается расширение разметки [{x:Bind}](https://msdn.microsoft.com/library/windows/apps/Mt204783), а затем — [{Binding}](https://msdn.microsoft.com/library/windows/apps/Mt204782). Объявления привязок в разметке используется очень часто (благодаря удобству и удобочитаемости). Однако вы можете не использовать разметку, а вместо этого при необходимости императивно (программными средствами) создать экземпляр класса [**Binding**](https://msdn.microsoft.com/library/windows/apps/BR209820).

<!-- XAML lang specifier not yet supported in OP. Using XML for now. -->
``` xml
<Button Content="{x:Bind ...}" ... />
```

``` xml
<Button Content="{Binding ...}" ... />
```

Binding object declared using {x:Bind}
--------------------------------------

There's one step we need to do before we author our [{x:Bind}](https://msdn.microsoft.com/library/windows/apps/Mt204783) markup. We need to expose our binding source class from the class that represents our page of markup. We do that by adding a property (of type **HostViewModel** in this case) to our **HostView** page class.

``` csharp
namespace QuizGame.View
{
    public sealed partial class HostView : Page
    {
        public HostView()
        {
            this.InitializeComponent();
            this.ViewModel = new HostViewModel();
        }
    
        public HostViewModel ViewModel { get; set; }
    }
}
```

После этого мы можем подробнее рассмотреть разметку, объявляющую объект привязки. В приведенном ниже примере используется целевой объект привязки **Button.Content**, который мы ранее применяли в разделе «Целевой объект привязки», уже привязанный к свойству **HostViewModel.NextButtonText**.

``` xml
<Page x:Class="QuizGame.View.HostView" ... >
    <Button Content="{x:Bind Path=ViewModel.NextButtonText, Mode=OneWay}" ... />
</Page>
```

Notice the value that we specify for **Path**. This value is interpreted in the context of the page itself, and in this case the path begins by referencing the **ViewModel** property that we just added to the **HostView** page. That property returns a **HostViewModel** instance, and so we can dot into that object to access the **HostViewModel.NextButtonText** property. And we specify **Mode**, to override the [{x:Bind}](https://msdn.microsoft.com/library/windows/apps/Mt204783) default of one-time.

The [**Path**](https://msdn.microsoft.com/library/windows/apps/BR209820-path) property supports a variety of syntax options for binding to nested properties, attached properties, and integer and string indexers. For more info, see [Property-path syntax](https://msdn.microsoft.com/library/windows/apps/Mt185586). Binding to string indexers gives you the effect of binding to dynamic properties without having to implement [**ICustomPropertyProvider**](https://msdn.microsoft.com/library/windows/apps/BR209878). For other settings, see [{x:Bind} markup extension](https://msdn.microsoft.com/library/windows/apps/Mt204783).

**Note**  Changes to [**TextBox.Text**](https://msdn.microsoft.com/library/windows/apps/BR209683-text) are sent to a two-way bound source when the [**TextBox**](https://msdn.microsoft.com/library/windows/apps/BR209683) loses focus, and not after every user keystroke.

**DataTemplate and x:DataType**

Inside a [**DataTemplate**](https://msdn.microsoft.com/library/windows/apps/BR242348) (whether used as an item template, a content template, or a header template), the value of **Path** is not interpreted in the context of the page, but in the context of the data object being templated. So that its bindings can be validated (and efficient code generated for them) at compile-time, a **DataTemplate** needs to declare the type of its data object using **x:DataType**. The example given below could be used as the **ItemTemplate** of an items control bound to a collection of **SampleDataGroup** objects.

``` xml
<DataTemplate x:Key="SimpleItemTemplate" x:DataType="data:SampleDataGroup">
    <StackPanel Orientation="Vertical" Height="50">
      <TextBlock Text="{x:Bind Title}"/>
      <TextBlock Text="{x:Bind Description}"/>
    </StackPanel>
  </DataTemplate>
```

**Weakly-typed objects in your Path**

Consider for example that you have a type named SampleDataGroup, which implements a string property named Title. And you have a property MainPage.SampleDataGroupAsObject, which is of type object but which actually returns an instance of SampleDataGroup. The binding `<TextBlock Text="{x:Bind SampleDataGroupAsObject.Title}"/>` will result in a compile error because the Title property is not found on the type object. The remedy for this is to add a cast to your Path syntax like this: `<TextBlock Text="{x:Bind SampleDataGroupAsObject.(data:SampleDataGroup.Title)}"/>`. Here's another example where Element is declared as object but is actually a TextBlock: `<TextBlock Text="{x:Bind Element.Text}"/>`. And a cast remedies the issue: `<TextBlock Text="{x:Bind Element.(TextBlock.Text)}"/>`.

**Если данные загружаются асинхронно**

Код для поддержки **{x:Bind}** создается во время компиляции в разделяемых классах для ваших страниц. Эти файлы можно найти в папке `obj` с такими именами, как (для C#) `<view name>.g.cs`. The generated code includes a handler for your page's [**Loading**](https://msdn.microsoft.com/library/windows/apps/BR208706-loading) event, and that handler calls the **Initialize** method on a generated class that represent's your page's bindings. **Initialize** in turn calls **Update** to begin moving data between the binding source and the target. **Loading** is raised just before the first measure pass of the page or user control. So if your data is loaded asynchronously it may not be ready by the time **Initialize** is called. So, after you've loaded data, you can force one-time bindings to be initialized by calling `this->Bindings->Update();`. Если вам нужны однократные привязки только для асинхронно загружаемых данных, намного эффективнее инициализировать их так, чем создать односторонние привязки и ожидать изменений. Если в ваши данные не вносятся точечные изменения и они будут обновляться в рамках определенного действия, вы можете сделать привязки однократными и принудительно выполнять обновление вручную с помощью вызова **Update**.

**Ограничения**

**{x:Bind}** не подходит для сценариев позднего связывания, таких как перемещение по структуре словаря объекта JSON или «утиная типизация», которая представляет собой форму типизации на основе словарных соответствий имен свойств («если это ходит, плавает и крякает как утка, это утка»). С утиной типизацией привязка к свойству Age одинаково удовлетворяется объектом Person или Wine. В этих сценариях используйте **{Binding}**.

Объект привязки, объявленный с использованием {Binding}
---------------------------------------

[{Binding}](https://msdn.microsoft.com/library/windows/apps/Mt204782) по умолчанию предполагает, что вы выполняете привязку к свойству [**DataContext**](https://msdn.microsoft.com/library/windows/apps/BR208713) страницы разметки. Поэтому в качестве свойства **DataContext** страницы мы установим экземпляр нашего класса источника привязки (типа **HostViewModel** в данном случае). В примере ниже показана разметка, объявляющая объект привязки. Мы используем тот же целевой объект привязки **Button.Content**, который ранее использовался в разделе «Целевой объект привязки», и выполняем привязку к свойству **HostViewModel.NextButtonText**.

``` xml
<Page xmlns:viewmodel="using:QuizGame.ViewModel" ... >
    <Page.DataContext>
        <viewmodel:HostViewModel/>
    </Page.DataContext>
    ...
    <Button Content="{Binding Path=NextButtonText}" ... />
</Page>
```

Notice the value that we specify for **Path**. This value is interpreted in the context of the page's [**DataContext**](https://msdn.microsoft.com/library/windows/apps/BR208713), which in this example is set to an instance of **HostViewModel**. The path references the **HostViewModel.NextButtonText** property. We can omit **Mode**, because the [{Binding}](https://msdn.microsoft.com/library/windows/apps/Mt204782) default of one-way works here.

The default value of [**DataContext**](https://msdn.microsoft.com/library/windows/apps/BR208713) for a UI element is the inherited value of its parent. You can of course override that default by setting **DataContext** explicitly, which is in turn inherited by children by default. Setting **DataContext** explicitly on an element is useful when you want to have multiple bindings that use the same source.

A binding object has a **Source** property, which defaults to the [**DataContext**](https://msdn.microsoft.com/library/windows/apps/BR208713) of the UI element on which the binding is declared. You can override this default by setting **Source**, **RelativeSource**, or **ElementName** explicitly on the binding (see [{Binding}](https://msdn.microsoft.com/library/windows/apps/Mt204782) for details).

Inside a [**DataTemplate**](https://msdn.microsoft.com/library/windows/apps/BR242348), the [**DataContext**](https://msdn.microsoft.com/library/windows/apps/BR208713) is set to the data object being templated. The example given below could be used as the **ItemTemplate** of an items control bound to a collection of any type that has string properties named **Title** and **Description**.

``` xml
<DataTemplate x:Key="SimpleItemTemplate">
    <StackPanel Orientation="Vertical" Height="50">
      <TextBlock Text="{Binding Title}"/>
      <TextBlock Text="{Binding Description"/>
    </StackPanel>
  </DataTemplate>
```

**Note**  By default, changes to [**TextBox.Text**](https://msdn.microsoft.com/library/windows/apps/BR209683-text) are sent to a two-way bound source when the [**TextBox**](https://msdn.microsoft.com/library/windows/apps/BR209683) loses focus. To cause changes to be sent after every user keystroke, set **UpdateSourceTrigger** to **PropertyChanged** on the binding in markup. You can also completely take control of when changes are sent to the source by setting **UpdateSourceTrigger** to **Explicit**. You then handle events on the text box (typically [**TextBox.TextChanged**](https://msdn.microsoft.com/library/windows/apps/BR209683-textchanged)), call [**GetBindingExpression**](https://msdn.microsoft.com/library/windows/apps/BR208706-getbindingexpression) on the target to get a [**BindingExpression**](https://msdn.microsoft.com/library/windows/apps/BR209820expression) object, and finally call [**BindingExpression.UpdateSource**](https://msdn.microsoft.com/library/windows/apps/BR209820expression-updatesource) to programmatically update the data source.

The [**Path**](https://msdn.microsoft.com/library/windows/apps/BR209820-path) property supports a variety of syntax options for binding to nested properties, attached properties, and integer and string indexers. For more info, see [Property-path syntax](https://msdn.microsoft.com/library/windows/apps/Mt185586). Binding to string indexers gives you the effect of binding to dynamic properties without having to implement [**ICustomPropertyProvider**](https://msdn.microsoft.com/library/windows/apps/BR209878). The [**ElementName**](https://msdn.microsoft.com/library/windows/apps/BR209820-elementname) property is useful for element-to-element binding. The [**RelativeSource**](https://msdn.microsoft.com/library/windows/apps/BR209820-relativesource) property has several uses, one of which is as a more powerful alternative to template binding inside a [**ControlTemplate**](https://msdn.microsoft.com/library/windows/apps/BR209391). For other settings, see [{Binding} markup extension](https://msdn.microsoft.com/library/windows/apps/Mt204782) and the [**Binding**](https://msdn.microsoft.com/library/windows/apps/BR209820) class.

What if the source and the target are not the same type?
--------------------------------------------------------

If you want to control the visibility of a UI element based on the value of a boolean property, or if you want to render a UI element with a color that's a function of a numeric value's range or trend, or if you want to display a date and/or time value in a UI element property that expects a string, then you'll need to convert values from one type to another. There will be cases where the right solution is to expose another property of the right type from your binding source class, and keep the conversion logic encapsulated and testable there. But that isn't flexible nor scalable when you have large numbers, or large combinations, of source and target properties. In that case you'll want to use something known as a value converter. This section describes how to implement and consume a value converter.

Here's a value converter, suitable for a one-time or a one-way binding, that converts a [**DateTime**](T:System.DateTime) value to a string value containing the month. The class implements [**IValueConverter**](https://msdn.microsoft.com/library/windows/apps/BR209903).

``` csharp
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

``` vbnet
Public Class DateToStringConverter
    Implements IValueConverter

    ' Define the Convert method to change a DateTime object to
    ' a month string.
    Public Function Convert(ByVal value As Object, -
        ByVal targetType As Type, ByVal parameter As Object, -
        ByVal language As String) As Object -
        Implements IValueConverter.Convert

        ' value is the data from the source object.
        Dim thisdate As DateTime = CType(value, DateTime)
        Dim monthnum As Integer = thisdate.Month
        Dim month As String
        Select Case (monthnum)
            Case 1
                month = "January"
            Case 2
                month = "February"
            Case Else
                month = "Month not found"
        End Select
        ' Return the value to pass to the target.
        Return month

    End Function

    ' ConvertBack is not implemented for a OneWay binding.
    Public Function ConvertBack(ByVal value As Object, -
        ByVal targetType As Type, ByVal parameter As Object, -
        ByVal language As String) As Object -
        Implements IValueConverter.ConvertBack

        Throw New NotImplementedException

    End Function
End Class
```

Ниже показано, как использовать этот преобразователь величин в разметке объекта привязки.

``` xml
<UserControl.Resources>
  <local:DateToStringConverter x:Key="Converter1"/>
</UserControl.Resources>

...

<TextBlock Grid.Column="0" 
  Text="{x:Bind ViewModel.Month, Converter={StaticResource Converter1}}"/>

<TextBlock Grid.Column="0" 
  Text="{Binding Month, Converter={StaticResource Converter1}}"/>
```

The binding engine calls the [**Convert**](https://msdn.microsoft.com/library/windows/apps/BR209903-convert) and [**ConvertBack**](https://msdn.microsoft.com/library/windows/apps/BR209903-convertback) methods if the [**Converter**](https://msdn.microsoft.com/library/windows/apps/BR209820-converter) parameter is defined for the binding. When data is passed from the source, the binding engine calls **Convert** and passes the returned data to the target. When data is passed from the target (for a two-way binding), the binding engine calls **ConvertBack** and passes the returned data to the source.

The converter also has optional parameters: [**ConverterLanguage**](https://msdn.microsoft.com/library/windows/apps/BR209820-converterlanguage), which allows specifying the language to be used in the conversion, and [**ConverterParameter**](https://msdn.microsoft.com/library/windows/apps/BR209820-converterparameter), which allows passing a parameter for the conversion logic. For an example that uses a converter parameter, see [**IValueConverter**](https://msdn.microsoft.com/library/windows/apps/BR209903).

**Note**  If there is an error in the conversion, do not throw an exception. Instead, return [**DependencyProperty.UnsetValue**](https://msdn.microsoft.com/library/windows/apps/BR242362-unsetvalue), which will stop the data transfer.

To display a default value to use whenever the binding source cannot be resolved, set the **FallbackValue** property on the binding object in markup. This is useful to handle conversion and formatting errors. It is also useful to bind to source properties that might not exist on all objects in a bound collection of heterogeneous types.

If you bind a text control to a value that is not a string, the data binding engine will convert the value to a string. If the value is a reference type, the data binding engine will retrieve the string value by calling [**ICustomPropertyProvider.GetStringRepresentation**](https://msdn.microsoft.com/library/windows/apps/BR209878-getstringrepresentation) or [**IStringable.ToString**](https://msdn.microsoft.com/library/Dn302136) if available, and will otherwise call [**Object.ToString**](M:System.Object.ToString). Note, however, that the binding engine will ignore any **ToString** implementation that hides the base-class implementation. Subclass implementations should override the base class **ToString** method instead. Similarly, in native languages, all managed objects appear to implement [**ICustomPropertyProvider**](https://msdn.microsoft.com/library/windows/apps/BR209878) and [**IStringable**](https://msdn.microsoft.com/library/Dn302135). However, all calls to **GetStringRepresentation** and **IStringable.ToString** are routed to **Object.ToString** or an override of that method, and never to a new **ToString** implementation that hides the base-class implementation.

Resource dictionaries with {x:Bind}
-----------------------------------

The [{x:Bind} markup extension](https://msdn.microsoft.com/library/windows/apps/Mt204783) depends on code generation, so it needs a code-behind file containing a constructor that calls **InitializeComponent** (to initialize the generated code). You re-use the resource dictionary by instantiating its type (so that **InitializeComponent** is called) instead of referencing its filename. Here's an example of what to do if you have an existing resource dictionary and you want to use {x:Bind} in it.

TemplatesResourceDictionary.xaml

``` xml
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

``` csharp
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

``` xml
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

Event binding and ICommand
--------------------------

[{x:Bind}](https://msdn.microsoft.com/library/windows/apps/Mt204783) supports a feature called event binding. With this feature, you can specify the handler for an event using a binding, which is an additional option on top of handling events with a method on the code-behind file. Let's say you have a **RootFrame** property on your **MainPage** class.

``` csharp
    public sealed partial class MainPage : Page
    {
        ....    
        public Frame RootFrame { get { return Window.Current.Content as Frame; } }
    }
```

Затем вы можете привязать событие **Click** кнопки к методу объекта **Frame**, возвращенного свойством **RootFrame**, как показано ниже. Обратите внимание, что мы также привязываем свойство **IsEnabled** кнопки к другому члену того же объекта **Frame**.

``` xml
    <AppBarButton Icon="Forward" IsCompact="True"
    IsEnabled="{x:Bind RootFrame.CanGoForward, Mode=OneWay}"
    Click="{x:Bind RootFrame.GoForward}"/>
```

Overloaded methods cannot be used to handle an event with this technique. Also, if the method that handles the event has parameters then they must all be assignable from the types of all of the event's parameters, respectively. In this case, [**Frame.GoForward**](https://msdn.microsoft.com/library/windows/apps/BR242693) is not overloaded and it has no parameters (but it would still be valid even if it took two **object** parameters). [**Frame.GoBack**](https://msdn.microsoft.com/library/windows/apps/Dn996568) is overloaded, though, so we can't use that method with this technique.

The event binding technique is similar to implementing and consuming commands (a command is a property that returns an object that implements the [**ICommand**](T:System.Windows.Input.ICommand) interface). Both [{x:Bind}](https://msdn.microsoft.com/library/windows/apps/Mt204783) and [{Binding}](https://msdn.microsoft.com/library/windows/apps/Mt204782) work with commands. So that you don't have to implement the command pattern multiple times, you can use the **DelegateCommand** helper class that you'll find in the [QuizGame](https://github.com/Microsoft/Windows-appsample-quizgame) sample (in the "Common" folder).

## Binding to a collection of folders or files

You can use the APIs in the [**Windows.Storage**](https://msdn.microsoft.com/library/windows/apps/BR227346) namespace to retrieve folder and file data. However, the various **GetFilesAsync**, **GetFoldersAsync**, and **GetItemsAsync** methods do not return values that are suitable for binding to list controls. Instead, you must bind to the return values of the [**GetVirtualizedFilesVector**](https://msdn.microsoft.com/library/windows/apps/Hh701422), [**GetVirtualizedFoldersVector**](https://msdn.microsoft.com/library/windows/apps/Hh701428), and [**GetVirtualizedItemsVector**](https://msdn.microsoft.com/library/windows/apps/Hh701430) methods of the [**FileInformationFactory**](https://msdn.microsoft.com/library/windows/apps/BR207501) class. The following code example from the [StorageDataSource and GetVirtualizedFilesVector sample](http://go.microsoft.com/fwlink/p/?linkid=228621) shows the typical usage pattern. Remember to declare the **picturesLibrary** capability in your app package manifest, and confirm that there are pictures in your Pictures library folder.

``` csharp
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

Этот подход обычно используется для создания доступных только для чтения сведений о файлах или папке. Можно создать двухсторонние привязки к свойствам файлов и папок, например, чтобы позволить пользователям оценивать композиции в музыкальном представлении. Однако любые изменения сохраняются только после вызова соответствующего метода **SavePropertiesAsync** (например, [**MusicProperties.SavePropertiesAsync**](https://msdn.microsoft.com/library/windows/apps/BR207760)). Когда элемент теряет фокус, изменения следует принять, так как это инициирует сброс выбранных элементов.

Обратите внимание, что двухсторонняя привязка на основе этой методики работает только с проиндексированными расположениями, такими как "Музыка". Для проверки индексации расположения можно вызвать метод [**FolderInformation.GetIndexedStateAsync**](https://msdn.microsoft.com/library/windows/apps/BR207627).

Кроме того, следует иметь в виду, что виртуализированный вектор для некоторых элементов может возвратить значение **null** до заполнения их значений. Необходимо, например, выполнять проверку на **null** перед использованием значения [**SelectedItem**](https://msdn.microsoft.com/library/windows/apps/BR209770) в элементе управления «Список», который привязан к виртуализированному вектору, или использовать вместо этого свойство [**SelectedIndex**](https://msdn.microsoft.com/library/windows/apps/BR209768).

Привязка к данным, сгруппированным за ключом
--------------------------------

Если взять плоскую коллекцию элементов, например книги, представленные классом **BookSku**, и сгруппировать элементы с помощью общего свойства в качестве ключа, например, свойства **BookSku.AuthorName**, то в результате будут получены сгруппированные данные. При группировке данные больше не являются плоской коллекцией. Сгруппированные данные — это коллекция объектов группы, каждый из которых имеет а) ключ и б) коллекцию элементов, свойства которых соответствуют этому ключу. Если вернуться к примеру с книгами, при группировании книг по имени автора будет получена коллекция групп с именами автора, причем каждая группа будет иметь а) ключ (в этом случае — имя автора) и б) коллекцию элементов **BookSku**, свойство **AuthorName** которых соответствует ключу группы.

В общем, для отображения коллекции необходимо привязать свойство [**ItemsSource**](https://msdn.microsoft.com/library/windows/apps/BR242828) элемента управления (например, [**ListView**](https://msdn.microsoft.com/library/windows/apps/BR242878) или [**GridView**](https://msdn.microsoft.com/library/windows/apps/BR242705)) непосредственно к свойству, которое возвращает коллекцию. Если это плоская коллекция элементов, вам не нужно выполнять никаких особенных действий. Однако если это коллекция объектов группы (что наблюдается при привязке к сгруппированным данным), вам нужно воспользоваться объектом-посредником с именем [**CollectionViewSource**](https://msdn.microsoft.com/library/windows/apps/BR209833), который размещается между элементом управления и источником привязки. Объект **CollectionViewSource** следует привязать к свойству, возвращающему сгруппированные данные, а элемент управления — к объекту **CollectionViewSource**. Дополнительное добавление объекта **CollectionViewSource** обеспечивает отслеживание с его помощью текущего элемента, поэтому вы можете синхронизировать несколько элементов управления путем их привязки к **CollectionViewSource**. Кроме того, вы можете получить доступ к текущему элементу программными средствами с помощью свойства [**ICollectionView.CurrentItem**](https://msdn.microsoft.com/library/windows/apps/BR209857) объекта, возвращенного свойством [**CollectionViewSource.View**](https://msdn.microsoft.com/library/windows/apps/BR209833-view).

Чтобы активировать возможность группирования с помощью класса [**CollectionViewSource**](https://msdn.microsoft.com/library/windows/apps/BR209833), установите для свойства [**IsSourceGrouped**](https://msdn.microsoft.com/library/windows/apps/BR209833-issourcegrouped) значение **true**. Целесообразность одновременного задания свойства [**ItemsPath**](https://msdn.microsoft.com/library/windows/apps/BR209833-itemspath) зависит от того, как именно вы создаете объекты группы. Объект группы можно создать двумя способами: с помощью шаблона "is-a-group" или "has-a-group". В шаблоне is-a-group объект группы создается на основе типа коллекции (например, **List&lt;T&gt;**), поэтому сам объект группы является группой элементов. Если используется этот шаблон, не нужно задавать свойство **ItemsPath**. В шаблоне has-a-group объект группы включает одно или несколько свойств типа коллекции (например, **List&lt;T&gt;**), поэтому группа включает группу элементов в форме свойства (или несколько групп элементов в виде ряда свойств). При использовании этого шаблона для **ItemsPath** необходимо задать имя свойства, содержащего группу элементов.

В примере ниже приведен шаблон has-a-group. Класс страницы имеет свойство [**ViewModel**](https://msdn.microsoft.com/library/windows/apps/BR208713), которое возвращает экземпляр нашей модели представления. [
            **CollectionViewSource**](https://msdn.microsoft.com/library/windows/apps/BR209833) привязывается к свойству **Authors** модели представления (**Authors** — это коллекция объектов группы), а также указывает, что это свойство **Author.BookSkus** содержит сгруппированные элементы. Наконец, класс [**GridView**](https://msdn.microsoft.com/library/windows/apps/BR242705) привязан к классу **CollectionViewSource**, а его стиль группы определен таким образом, чтобы он мог обрабатывать элементы в группах.

``` csharp
    <Page.Resources>
        <CollectionViewSource
        x:Name="AuthorHasACollectionOfBookSku"
        Source="{x:Bind ViewModel.Authors}"
        IsSourceGrouped="true"
        ItemsPath="BookSkus"/>
    </Page.Resources>
    ...

    <GridView
    ItemsSource="{Binding Source={StaticResource AuthorHasACollectionOfBookSku}}" ...>
        <GridView.GroupStyle>
            <GroupStyle
                HeaderTemplate="{StaticResource AuthorGroupHeaderTemplateWide}" ... />
        </GridView.GroupStyle>
    </GridView>
```

Note that the [**ItemsSource**](https://msdn.microsoft.com/library/windows/apps/BR242828) must use [{Binding}](https://msdn.microsoft.com/library/windows/apps/Mt204782) (and not [{x:Bind}](https://msdn.microsoft.com/library/windows/apps/Mt204783)) because it needs to set the **Source** property to a resource. To see the above example in the context of the complete app, download the [Bookstore2](http://go.microsoft.com/fwlink/?linkid=532952) sample app. Unlike the markup shown above, [Bookstore2](http://go.microsoft.com/fwlink/?linkid=532952) uses {Binding} exclusively.

You can implement the "is-a-group" pattern in one of two ways. One way is to author your own group class. Derive the class from **List&lt;T&gt;** (where *T* is the type of the items). For example, `public class Author : List<BookSku>`. Второй способ — использовать выражение [LINQ](http://msdn.microsoft.com/library/bb397926.aspx) для динамического создания объектов группы (и класса группы) из вероятных значений свойств элементов **BookSku**. Этот подход с поддержкой только плоского списка элементов и их группированием в ходе процесса обычно используется приложением, которое получает доступ к данным из облачной службы. Это позволяет гибко группировать книги по авторам или жанрам (к примеру) без специальных классов групп, таких как **Author** и **Genre**.

В примере ниже показан шаблон is-a-group, использующий выражение [LINQ](http://msdn.microsoft.com/library/bb397926.aspx). На этот раз мы группируем книги по жанрам, причем названия жанров отображаются в заголовках групп. На это указывает путь свойства Key в ссылке на значение [**Key**](P:System.Linq.IGrouping%602.Key) группы.

``` csharp
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
                orderby grp.Key select grp;
            }
            return this.genres;
        }
    }
```

Remember that when using [{x:Bind}](https://msdn.microsoft.com/library/windows/apps/Mt204783) with data templates we need to indicate the type being bound to by setting an **x:DataType** value. If the type is generic then we can't express that in markup so we need to use [{Binding}](https://msdn.microsoft.com/library/windows/apps/Mt204782) instead in the group style header template.

``` xml
    <Grid.Resources>
        <CollectionViewSource x:Name="GenreIsACollectionOfBookSku"
        Source="{Binding Genres}"
        IsSourceGrouped="true"/>
    </Grid.Resources>
    <GridView ItemsSource="{Binding Source={StaticResource GenreIsACollectionOfBookSku}}">
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

A [**SemanticZoom**](https://msdn.microsoft.com/library/windows/apps/Hh702601) control is a great way for your users to view and navigate grouped data. The [Bookstore2](http://go.microsoft.com/fwlink/?linkid=532952) sample app illustrates how to use the **SemanticZoom**. In that app, you can view a list of books grouped by author (the zoomed-in view) or you can zoom out to see a jump list of authors (the zoomed-out view). The jump list affords much quicker navigation than scrolling through the list of books. The zoomed-in and zoomed-out views are actually **ListView** or **GridView** controls bound to the same **CollectionViewSource**.

![An illustration of a SemanticZoom](images/sezo.png)

When you bind to hierarchical data—such as subcategories within categories—you can choose to display the hierarchical levels in your UI with a series of items controls. A selection in one items control determines the contents of subsequent items controls. You can keep the lists synchronized by binding each list to its own [**CollectionViewSource**](https://msdn.microsoft.com/library/windows/apps/BR209833) and binding the **CollectionViewSource** instances together in a chain. This is called a master/details (or list/details) view. For more info, see [How to bind to hierarchical data and create a master/details view](how-to-bind-to-hierarchical-data-and-create-a-master-details-view.md).

Diagnosing and debugging data binding problems
-----------------------------------------------

Your binding markup contains the names of properties (and, for C#, sometimes fields and methods). So when you rename a property, you'll also need to change any binding that references it. Forgetting to do that leads to a typical example of a data binding bug, and your app either won't compile or won't run correctly.

The binding objects created by [{x:Bind}](https://msdn.microsoft.com/library/windows/apps/Mt204783) and [{Binding}](https://msdn.microsoft.com/library/windows/apps/Mt204782) are largely functionally equivalent. But {x:Bind} has type information for the binding source, and it generates source code at compile-time. With {x:Bind} you get the same kind of problem detection that you get with the rest of your code. That includes compile-time validation of your binding expressions, and debugging by setting breakpoints in the source code generated as the partial class for your page. These classes can be found in the files in your `obj` folder, with names like (for C#) `<view name>.g.cs`). Если при привязке возникает проблема, установите флажок **Останавливать при неперехваченных исключениях** в отладчике Microsoft Visual Studio. Отладчик прервет выполнение в этой точке, после чего вы можете перейти к отладке. В коде, формируемом расширением разметки {x:Bind}, применяется один шаблон для каждой части графа узлов источника привязки. С помощью сведений в окне **Стек вызовов** вы можете определить последовательность вызовов, которые привели к проблеме.

В расширении разметки [{Binding}](https://msdn.microsoft.com/library/windows/apps/Mt204782) не указываются сведения о типе источника привязки. Однако когда приложение выполняется с подключенным отладчиком, любые ошибки привязки отображаются в окне **Вывод** приложения Visual Studio.

Создание привязок в коде
-------------------------

**Примечание.** Этот раздел относится только к [{Binding}](https://msdn.microsoft.com/library/windows/apps/Mt204782), потому что нельзя создать привязки [{x:Bind}](https://msdn.microsoft.com/library/windows/apps/Mt204783) в коде. Однако некоторые из преимуществ {x:Bind} можно получить с помощью [**DependencyProperty.RegisterPropertyChangedCallback**](https://msdn.microsoft.com/library/windows/apps/BR242356-registerpropertychangedcallback), что позволяет зарегистрироваться для получения уведомлений об изменениях любого свойства зависимости.

Вы также можете связывать элементы пользовательского интерфейса в процедурах в коде вместо использования XAML. Для этого создайте новый объект [**Binding**](https://msdn.microsoft.com/library/windows/apps/BR209820), задайте соответствующие свойства, а затем вызовите [**FrameworkElement.SetBinding**](https://msdn.microsoft.com/library/windows/apps/BR208706-setbinding) или [**BindingOperations.SetBinding**](https://msdn.microsoft.com/library/windows/apps/BR209820operations-setbinding). Создание привязок программными средствами полезно, когда требуется выбирать значения свойств привязки во время выполнения или совместно использовать одну привязку в нескольких элементах управления. Однако отметим, что невозможно использовать значения свойств привязки после вызова **SetBinding**.

В следующем примере показано, как реализовать привязку в коде.

``` xml
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

``` vbnet
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

Сравнение компонентов расширений разметки {x:Bind} и {Binding}
------------------------------------------

| Компонент | {x:Bind} | {Binding} | Примечания |
|---------|----------|-----------|-------|
| Path — свойство по умолчанию | `{x:Bind a.b.c}` | `{Binding a.b.c}` | | 
| Свойство Path | `{x:Bind Path=a.b.c}` | `{Binding Path=a.b.c}` | В расширении разметки x:Bind свойство Path по умолчанию размещается в корне класса Page, а не DataContext. | 
| Индексатор | `{x:Bind Groups[2].Title}` | `{Binding Groups[2].Title}` | Привязывается к определенному элементу в коллекции. Поддерживаются только целочисленные индексы. | 
| Подключенные свойства | `{x:Bind Button22.(Grid.Row)}` | `{Binding Button22.(Grid.Row)}` | Подключенные свойства указываются в скобках. Если свойство не объявляется в пространстве имен XAML, нужно установить для него префикс пространства имен xml, которое необходимо сопоставить с пространством имен кода в заголовке документа. | 
| Приведение типов | `{x:Bind groups[0].(data:SampleDataGroup.Title)}` | Не требуется< | Приведения указываются в скобках. Если свойство не объявляется в пространстве имен XAML, нужно установить для него префикс пространства имен xml, которое необходимо сопоставить с пространством имен кода в заголовке документа. | 
| Преобразователь | `{x:Bind IsShown, Converter={StaticResource BoolToVisibility}}` | `{Binding IsShown, Converter={StaticResource BoolToVisibility}}` | Преобразователи необходимо объявлять в корне класса Page или ResourceDictionary либо в файле App.xaml. | 
| ConverterParameter, ConverterLanguage | `{x:Bind IsShown, Converter={StaticResource BoolToVisibility}, ConverterParameter=One, ConverterLanguage=fr-fr}` | `{Binding IsShown, Converter={StaticResource BoolToVisibility}, ConverterParameter=One, ConverterLanguage=fr-fr}` | Преобразователи необходимо объявлять в корне класса Page или ResourceDictionary либо в файле App.xaml. | 
| TargetNullValue | `{x:Bind Name, TargetNullValue=0}` | `{Binding Name, TargetNullValue=0}` | Используется, когда конечный объект выражения привязки имеет значение null. Строковое значение заключается в одиночные кавычки. | 
| FallbackValue | `{x:Bind Name, FallbackValue='empty'}` | `{Binding Name, FallbackValue='empty'}` | Используется, когда любая часть пути для привязки (кроме конечного объекта) имеет значение null. | 
| ElementName | `{x:Bind slider1.Value}` | `{Binding Value, ElementName=slider1}` | Если используется расширение разметки {x:Bind}, привязка выполняется к полю; свойство Path по умолчанию размещается в корне класса Page, поэтому это поле позволяет получить доступ к любому именованному элементу. | 
| RelativeSource: Self | `<Rectangle x:Name="rect1" Width="200" Height="{x:Bind rect1.Width}" ... />` | `<Rectangle Width="200" Height="{Binding Width, RelativeSource={RelativeSource Self}}" ... />` | With {x:Bind}, name the element and use its name in Path. | 
| RelativeSource: TemplatedParent | Not supported | `{Binding <path>, RelativeSource={RelativeSource TemplatedParent}}` | Регулярную привязку шаблонов можно использовать в шаблонах элементов управления в большинстве сценариев. Однако используйте TemplatedParent, если вам требуется преобразователь или двусторонняя привязка.< | 
| Источник | Не поддерживается | `<ListView ItemsSource="{Binding Orders, Source={StaticResource MyData}}"/>` | For {x:Bind} use a property or a static path instead. | 
| Mode | `{x:Bind Name, Mode=OneWay}` | `{Binding Name, Mode=TwoWay}` | Mode can be OneTime, OneWay, or TwoWay. {x:Bind} defaults to OneTime; {Binding} defaults to OneWay. | 
| UpdateSourceTrigger | Not supported | `<Binding UpdateSourceTrigger="[Default | PropertyChanged | Explicit]"/>` | В расширении разметки {x:Bind} поведение PropertyChanged используется во всех случаях, кроме TextBox.Text, где оно ожидает потерю фокуса, чтобы обновить источник. | 




<!--HONumber=Mar16_HO1-->



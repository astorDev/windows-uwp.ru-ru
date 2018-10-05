---
author: mcleblanc
ms.assetid: 41E1B4F1-6CAF-4128-A61A-4E400B149011
title: Подробно о привязке данных
description: Привязка данных обеспечивает отображение данных в пользовательском интерфейсе приложения и (дополнительно) синхронизацию с этими данными.
ms.author: markl
ms.date: 10/03/2018
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: 559bbbc3421151a9055b89c94bc1293a950ccb5b
ms.sourcegitcommit: 63cef0a7805f1594984da4d4ff2f76894f12d942
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/05/2018
ms.locfileid: "4388825"
---
# <a name="data-binding-in-depth"></a>Подробно о привязке данных

**Важные API**

-   [**Расширение разметки {x:Bind}**](../xaml-platform/x-bind-markup-extension.md)
-   [**Класс Binding**](https://msdn.microsoft.com/library/windows/apps/BR209820)
-   [**DataContext**](https://msdn.microsoft.com/library/windows/apps/BR208713)
-   [**INotifyPropertyChanged**](https://msdn.microsoft.com/library/windows/apps/BR209899)

> [!Note]
> В этой статье подробно описаны средства привязки данных. Краткое практическое введение см. в [Обзоре привязки данных](data-binding-quickstart.md).

Привязка данных обеспечивает отображение данных в пользовательском интерфейсе приложения и (дополнительно) синхронизацию с этими данными. Используя привязку данных, вы можете отделить вопросы обработки данных от вопросов, связанных с пользовательским интерфейсом. Это упрощает концептуальную модель, а также улучшает удобочитаемость, возможности тестирования и удобство поддержки приложения.

Привязку данных можно использовать, чтобы просто отобразить значения из источника данных при первом отображении пользовательского интерфейса, не реагируя на изменения этих значений. Это режим привязки под названием *однократной*, и он лучше всего подойдет для значения, которые не изменяются во время выполнения. Кроме того вы можете «отслеживать» значения и обновлять пользовательский Интерфейс при их изменении. Это более называется *односторонней*, и хорошо подходит для данных только для чтения. Наконец, вы можете как отслеживать, так и обновлять данные, чтобы изменения, которые пользователь вносит в значения в пользовательском интерфейсе, автоматически отправлялись в источник данных. Этот режим называется *двусторонней*, и он лучше всего подойдет для чтения и записи данных. Вот несколько примеров.

-   Можно использовать одноразовый режим привязка [**изображения**](https://msdn.microsoft.com/library/windows/apps/BR242752) к фотографии текущего пользователя.
-   Чтобы привязать [**ListView**](https://msdn.microsoft.com/library/windows/apps/BR242878) к коллекции актуальных новостных статей, сгруппированных по разделу газеты можно использовать одностороннем режиме.
-   Двухсторонний режим можно использовать для привязки к имени пользователя в форме [**текстового поля**](https://msdn.microsoft.com/library/windows/apps/BR209683) .

Независимо от режима, существует два типа привязки, и они обе обычно объявляются в разметке пользовательского интерфейса. Вы можете использовать [расширение разметки {x:Bind}](https://msdn.microsoft.com/library/windows/apps/Mt204783) или [расширение разметки {Binding}](https://msdn.microsoft.com/library/windows/apps/Mt204782). Кроме того, можно одновременно использовать оба типа привязки в одном приложении и даже в одном элементе пользовательского интерфейса. Расширение разметки {x:Bind}, которое впервые появилось в ОС Windows10, отличается улучшенной производительностью. Все сведения, описанные в этом разделе, касаются обоих типов привязки, если явно не указано иное.

**Примеры приложений с расширением разметки {x:Bind}**

-   [Пример {x:Bind}](http://go.microsoft.com/fwlink/p/?linkid=619989).
-   [QuizGame](https://github.com/Microsoft/Windows-appsample-quizgame).
-   [Пример основных элементов пользовательского интерфейса XAML](http://go.microsoft.com/fwlink/p/?linkid=619992).

**Примеры приложений с расширением разметки {Binding}**

-   Скачайте приложение [Bookstore1](http://go.microsoft.com/fwlink/?linkid=532950).
-   Скачайте приложение [Bookstore2](http://go.microsoft.com/fwlink/?linkid=532952).

## <a name="every-binding-involves-these-pieces"></a>Неотъемлемые компоненты каждой привязки

-   *Источник привязки*. Это источник данных для привязки, в качестве которого может выступать экземпляр любого класса, содержащего члены, значения которых вы хотите отобразить в пользовательском интерфейсе.
-   *Целевой объект привязки*. Это [**DependencyProperty**](https://msdn.microsoft.com/library/windows/apps/BR242362) класса [**FrameworkElement**](https://msdn.microsoft.com/library/windows/apps/BR208706) в пользовательском интерфейсе, отвечающий за отображение данных.
-   *Объект привязки*. Это элемент, который передает значения данных из источника в целевой объект, а также (необязательно) из целевого объекта назад в источник. Объект привязки создается во время загрузки XAML из расширения разметки [{x:Bind}](https://msdn.microsoft.com/library/windows/apps/Mt204783) или [{Binding}](https://msdn.microsoft.com/library/windows/apps/Mt204782).

В следующих разделах мы подробнее рассмотрим источник, целевой объект и объект привязки. Кроме того, мы свяжем эти разделы с помощью примера привязки содержимого кнопки к строковому свойству **NextButtonText**, которое принадлежит классу **HostViewModel**.

### <a name="binding-source"></a>Источник привязки

Здесь приведен простейший пример реализации класса, который можно использовать в качестве источника привязки.

> [!Note]
> Если вы используете [{Binding}](https://msdn.microsoft.com/library/windows/apps/Mt204782) с расширениями компонентов Visual C++ (C + +/ CX), вам также понадобится добавить атрибут [**BindableAttribute**](https://msdn.microsoft.com/library/windows/apps/Hh701872) класс источника привязки. Если вы используете [{x:Bind}](https://msdn.microsoft.com/library/windows/apps/Mt204783), этот атрибут не нужен. Фрагмент кода см. в разделе [Добавление представления подробностей](data-binding-quickstart.md#adding-a-details-view).

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

Более легким способом сделать класс доступным для отслеживания (что также необходимо для классов, уже имеющих базовый класс) является реализация интерфейса [**System.ComponentModel.INotifyPropertyChanged**](https://msdn.microsoft.com/library/windows/apps/xaml/system.componentmodel.inotifypropertychanged.aspx). Для этого необходимо всего лишь реализовать одно событие с именем **PropertyChanged**. Пример использования класса **HostViewModel** см. ниже.

> [!Note]
> Для C + +/ CX необходимо реализовать [**Windows::UI::Xaml::Data::INotifyPropertyChanged**](https://msdn.microsoft.com/library/windows/apps/BR209899), и класс источника привязки не должны иметь [**BindableAttribute**](https://msdn.microsoft.com/library/windows/apps/Hh701872) или реализовать [**ICustomPropertyProvider**](https://msdn.microsoft.com/library/windows/apps/BR209878).

```csharp
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

Вызов события **PropertyChanged** с аргументом [**String.Empty**](https://msdn.microsoft.com/library/windows/apps/xaml/system.string.empty.aspx) или значением **null** указывает, что не все свойства индексатора в объекте необходимо считать еще раз. Это событие можно вызвать для указания того, что свойства индексатора в объекте изменены, с помощью аргумента "Item\[*indexer*\]" для конкретных индексаторов (где *indexer* — это значение индекса) или значения "Item\[\]" для всех индексаторов.

Источник привязки может рассматриваться как единичный объект, свойства которого содержат данные, или как коллекция объектов. В коде C# и Visual Basic вы можете выполнить единовременную привязку к объекту, реализующему класс [**List(Of T)**](https://msdn.microsoft.com/library/windows/apps/xaml/6sh2ey19.aspx), чтобы отобразить коллекцию, которая не изменяется во время выполнения. Если используется отслеживаемая коллекция (которая поддерживает отслеживание добавления и удаления ее элементов), следует выполнить одностороннюю привязку к классу [**ObservableCollection(Of T)**](https://msdn.microsoft.com/library/windows/apps/xaml/ms668604.aspx). В коде C++ вы можете выполнить привязку к классу [**Vector&lt;T&gt;**](https://msdn.microsoft.com/library/dn858385.aspx) как для отслеживаемых, так и для неотслеживаемых коллекций. Чтобы выполнить привязку к собственным классам коллекции, используйте рекомендации из следующей таблицы.

| Сценарий                                                        | C# и VB (CLR)                                                                                                                                                                                                                                                                                                                                                                                                                   | C++/CX                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
|-----------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Привязка к объекту.                                              | Любой объект.                                                                                                                                                                                                                                                                                                                                                                                                                 | Объект должен иметь атрибут [**BindableAttribute**](https://msdn.microsoft.com/library/windows/apps/Hh701872) или реализовывать интерфейс [**ICustomPropertyProvider**](https://msdn.microsoft.com/library/windows/apps/BR209878).                                                                                                                                                                                                                                                                                                             |
| Получение обновлений об изменении свойств от привязанного объекта.                | Объект должен реализовывать интерфейс [**System.ComponentModel. INotifyPropertyChanged**](https://msdn.microsoft.com/library/windows/apps/xaml/system.componentmodel.inotifypropertychanged.aspx).                                                                                                                                                                                                                                                                                                         | Объект должен реализовывать [**Windows.UI.Xaml.Data. INotifyPropertyChanged**](https://msdn.microsoft.com/library/windows/apps/BR209899).                                                                                                                                                                                                                                                                                                                                                           |
| Привязка к коллекции.                                           | [**List(Of T)**](https://msdn.microsoft.com/library/windows/apps/xaml/6sh2ey19.aspx)                                                                                                                                                                                                                                                                                                                                                                            | [**Platform::Collections::Vector&lt;T&gt;**](https://msdn.microsoft.com/library/windows/apps/xaml/hh441570.aspx)                                                                                                                                                                                                                                                                                                                                                                                         |
| Получение обновлений, касающихся изменения коллекции, от привязанной коллекции.          | [**ObservableCollection(Of T)**](https://msdn.microsoft.com/library/windows/apps/xaml/ms668604.aspx)                                                                                                                                                                                                                                                                                                                                        | [**Windows::Foundation::Collections::IObservableVector&lt;T&gt;**](https://msdn.microsoft.com/library/windows/apps/br226052.aspx)                                                                                                                                                                                                                                                                                                                                                                                         |
| Реализация коллекции с поддержкой привязки.                   | Расширьте [**List(Of T)**](https://msdn.microsoft.com/library/windows/apps/xaml/6sh2ey19.aspx) либо реализуйте [**IList**](https://msdn.microsoft.com/library/windows/apps/xaml/system.collections.ilist.aspx), [**IList**](https://msdn.microsoft.com/library/windows/apps/xaml/5y536ey6.aspx)(Of [**Object**](https://msdn.microsoft.com/library/windows/apps/xaml/system.object.aspx)), [**IEnumerable**](https://msdn.microsoft.com/library/windows/apps/xaml/system.collections.ienumerable.aspx) или [**IEnumerable**](https://msdn.microsoft.com/library/windows/apps/xaml/9eekhta0.aspx)(Of **Object**). Привязка к универсальным интерфейсам **IList(Of T)** и **IEnumerable(Of T)** не поддерживается. | Реализуйте [**IBindableVector**](https://msdn.microsoft.com/library/windows/apps/Hh701979), [**IBindableIterable**](https://msdn.microsoft.com/library/windows/apps/Hh701957), [**IVector**](https://msdn.microsoft.com/library/windows/apps/BR206631)&lt;[**Object**](https://msdn.microsoft.com/library/windows/apps/xaml/system.object.aspx)^&gt;, [**IIterable**](https://msdn.microsoft.com/library/windows/apps/BR226024)&lt;**Object**^&gt;, **IVector**&lt;[**IInspectable**](https://msdn.microsoft.com/library/BR205821)\*&gt; или **IIterable**&lt;**IInspectable**\*&gt;. Привязка к универсальным интерфейсам **IVector&lt;T&gt;** и **IIterable&lt;T&gt;** не поддерживается. |
| Реализация коллекции, которая поддерживает обновления, касающиеся изменения коллекции. | Расширьте [**ObservableCollection(Of T)**](https://msdn.microsoft.com/library/windows/apps/xaml/ms668604.aspx) или реализуйте (не универсальные) интерфейсы [**IList**](https://msdn.microsoft.com/library/windows/apps/xaml/system.collections.ilist.aspx) и [**INotifyCollectionChanged**](https://msdn.microsoft.com/library/windows/apps/xaml/system.collections.specialized.inotifycollectionchanged.aspx).                                                                                                                                                               | Реализуйте [**IBindableVector**](https://msdn.microsoft.com/library/windows/apps/Hh701979) и [**IBindableObservableVector**](https://msdn.microsoft.com/library/windows/apps/Hh701974).                                                                                                                                                                                                                                                                                                                       |
| Реализуйте коллекцию с поддержкой инкрементной нагрузки.       | Расширьте [**ObservableCollection(Of T)**](https://msdn.microsoft.com/library/windows/apps/xaml/ms668604.aspx) или реализуйте (не универсальные) интерфейсы [**IList**](https://msdn.microsoft.com/library/windows/apps/xaml/system.collections.ilist.aspx) и [**INotifyCollectionChanged**](https://msdn.microsoft.com/library/windows/apps/xaml/system.collections.specialized.inotifycollectionchanged.aspx). Кроме того, реализуйте [**ISupportIncrementalLoading**](https://msdn.microsoft.com/library/windows/apps/Hh701916).                                                          | Реализуйте [**IBindableVector**](https://msdn.microsoft.com/library/windows/apps/Hh701979), [**IBindableObservableVector**](https://msdn.microsoft.com/library/windows/apps/Hh701974) и [**ISupportIncrementalLoading**](https://msdn.microsoft.com/library/windows/apps/Hh701916).                                                                                                                                                                                                                                         |

Элементы управления списками можно привязать к источникам данных любого размера и сохранить высокую производительность благодаря использованию инкрементной нагрузки. Например, элементы управления списками можно привязать к результатам запроса изображений в Bing, причем одновременная загрузка всех результатов не требуется. Вместо этого сразу можно загрузить лишь некоторые результаты и загружать дополнительные результаты по мере необходимости. Для поддержки инкрементной нагрузки интерфейс [**ISupportIncrementalLoading**](https://msdn.microsoft.com/library/windows/apps/Hh701916) необходимо реализовать в источнике данных с уведомлением об изменении коллекции. Когда механизм привязки данных запрашивает больше данных, источник данных должен делать соответствующие запросы, объединять результаты, а затем отправлять соответствующие уведомления для обновления пользовательского интерфейса.

### <a name="binding-target"></a>Целевой объект привязки

В двух примерах ниже свойство **Button.Content** представляет собой целевой объект привязки, и в качестве его значения устанавливается расширение разметки, которое объявляет объект привязки. Сначала отображается расширение разметки [{x:Bind}](https://msdn.microsoft.com/library/windows/apps/Mt204783), а затем— [{Binding}](https://msdn.microsoft.com/library/windows/apps/Mt204782). Объявления привязок в разметке используется очень часто (благодаря удобству и удобочитаемости). Однако вы можете не использовать разметку, а вместо этого при необходимости императивно (программными средствами) создать экземпляр класса [**Binding**](https://msdn.microsoft.com/library/windows/apps/BR209820).

```xaml
<Button Content="{x:Bind ...}" ... />
```

```xaml
<Button Content="{Binding ...}" ... />
```

### <a name="binding-object-declared-using-xbind"></a>Объект привязки, объявленный с использованием расширения разметки {x:Bind}

Для создания разметки [{x:Bind}](https://msdn.microsoft.com/library/windows/apps/Mt204783) необходимо выполнить еще один шаг. Нужно предоставить класс источника привязки из класса, представляющего нашу страницу разметки. Для этого следует добавить свойство (типа **HostViewModel** в данном случае) к нашему классу страницы **HostView**.

```csharp
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

После этого мы можем подробнее рассмотреть разметку, объявляющую объект привязки. В приведенном ниже примере используется целевой объект привязки **Button.Content**, который мы ранее применяли в разделе "Целевой объект привязки". Он уже привязан к свойству **HostViewModel.NextButtonText**.

```xaml
<Page x:Class="QuizGame.View.HostView" ... >
    <Button Content="{x:Bind Path=ViewModel.NextButtonText, Mode=OneWay}" ... />
</Page>
```

Обратите внимание на значение, указанное для свойства **Path**. Это значение интерпретируется в контексте самой страницы. В этом случае в начале пути указывается ссылка на свойство **ViewModel**, которое мы только что добавили на страницу **HostView**. Это свойство возвращает экземпляр **HostViewModel**, поэтому мы можем обратиться к этому объекту, чтобы получить доступ к свойству **HostViewModel.NextButtonText**. Кроме того, необходимо указать свойство **Mode**, чтобы переопределить значение [{x:Bind}](https://msdn.microsoft.com/library/windows/apps/Mt204783) по умолчанию для единовременной привязки.

Свойство [**Path**](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.data.binding.path) поддерживает несколько вариантов синтаксиса для привязки к вложенным и подключенным свойствам, а также целочисленным и строковым индексаторам. Дополнительные сведения см. в разделе [Синтаксис Property-path](https://msdn.microsoft.com/library/windows/apps/Mt185586). Привязка к строковым индексаторам позволяет выполнять привязку к динамическим свойствам без реализации интерфейса [**ICustomPropertyProvider**](https://msdn.microsoft.com/library/windows/apps/BR209878). Другие параметры см. в [статье о расширении разметки {x:Bind}](https://msdn.microsoft.com/library/windows/apps/Mt204783).

> [!Note]
> Изменения в [**TextBox.Text**](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.textbox.text) отправляются двусторонней присоединенного источника, когда [**TextBox**](https://msdn.microsoft.com/library/windows/apps/BR209683) теряет фокус и не после каждого нажатия клавиши пользователем.

**DataTemplate и x:DataType**

В классе [**DataTemplate**](https://msdn.microsoft.com/library/windows/apps/BR242348) (который используется как шаблон элемента, содержимого или заголовка) значение **Path** интерпретируется не в контексте страницы, а в контексте объекта данных, для которого создается шаблон. При использовании {x:Bind} класс **DataTemplate** должен объявлять тип своего объекта данных с помощью атрибута **x:DataType**, чтобы его привязки (и созданный для них эффективный код) можно было проверить во время компиляции. Приведенный ниже пример можно использовать в качестве свойства **ItemTemplate** элемента управления, привязанного к коллекции объектов **SampleDataGroup**.

```xaml
<DataTemplate x:Key="SimpleItemTemplate" x:DataType="data:SampleDataGroup">
    <StackPanel Orientation="Vertical" Height="50">
      <TextBlock Text="{x:Bind Title}"/>
      <TextBlock Text="{x:Bind Description}"/>
    </StackPanel>
  </DataTemplate>
```

**Слабо типизированные объекты в пути**

Представьте, например, что у вас есть тип SampleDataGroup, который реализует строковое свойство Title. Также у вас есть свойство MainPage.SampleDataGroupAsObject объекта типа, которое фактически возвращает экземпляр SampleDataGroup. Привязка `<TextBlock Text="{x:Bind SampleDataGroupAsObject.Title}"/>` приведет к ошибке компиляции, поскольку свойство Title не найдено в объекте типа. Поэтому необходимо добавить приведение в синтаксис пути, например: `<TextBlock Text="{x:Bind ((data:SampleDataGroup)SampleDataGroupAsObject).Title}"/>`. Вот другой пример, где Element объявлен как объект, но фактически это TextBlock: `<TextBlock Text="{x:Bind Element.Text}"/>`. Приведение устраняет проблему: `<TextBlock Text="{x:Bind ((TextBlock)Element).Text}"/>`.

**Если данные загружаются асинхронно**

Код для поддержки **{x:Bind}** создается во время компиляции в разделяемых классах для ваших страниц. Эти файлы можно найти в папке `obj` с такими именами, как `<view name>.g.cs` (для C#). Созданный код содержит обработчик события [**Loading**](https://msdn.microsoft.com/library/windows/apps/BR208706) страницы, который вызывает метод **Initialize** в созданном классе, представляющем привязки вашей страницы. **Initialize** в свою очередь вызывает **Update** для перемещения данных между источником и целевым объектом привязки. **Loading** создается только перед первым этапом измерения страницы или пользовательского элемента управления. Поэтому если ваши данные загружаются асинхронно, они могут быть не готовы к моменту вызова **Initialize**. Поэтому после загрузки данных вы можете принудительно инициализировать однократные привязки, вызвав `this.Bindings.Update();`. Если вам нужны однократные привязки только для асинхронно загружаемых данных, намного эффективнее инициализировать их так, чем создать односторонние привязки и ожидать изменений. Если в ваши данные не вносятся точечные изменения и они будут обновляться в рамках определенного действия, вы можете сделать привязки однократными и принудительно выполнять обновление вручную с помощью вызова **Update**.

> [!Note]
> **{x:Bind}** не подходит для сценариев позднего связывания, таких как перемещение по структуре словаря объекта JSON или "утиная типизация", которая представляет собой слабую форму типизации на основе словарных соответствий имен свойств ("если это ходит, плавает и крякает как утка, это утка"). С утиной типизацией привязка к свойству Age одинаково удовлетворяется объектом Person или Wine. В этих сценариях используйте **{Binding}**.

### <a name="binding-object-declared-using-binding"></a>Объект привязки, объявленный с использованием расширения разметки {Binding}

Расширение разметки [{Binding}](https://msdn.microsoft.com/library/windows/apps/Mt204782) по умолчанию предполагает, что вы выполняете привязку к свойству [**DataContext**](https://msdn.microsoft.com/library/windows/apps/BR208713) страницы разметки. Поэтому в качестве свойства **DataContext** страницы мы установим экземпляр нашего класса источника привязки (типа **HostViewModel** в данном случае). В примере ниже показана разметка, объявляющая объект привязки. Мы используем тот же целевой объект привязки **Button.Content**, который ранее использовался в разделе "Целевой объект привязки", и выполняем привязку к свойству **HostViewModel.NextButtonText**.

```xaml
<Page xmlns:viewmodel="using:QuizGame.ViewModel" ... >
    <Page.DataContext>
        <viewmodel:HostViewModel/>
    </Page.DataContext>
    ...
    <Button Content="{Binding Path=NextButtonText}" ... />
</Page>
```

Обратите внимание на значение, указанное для свойства **Path**. Это значение интерпретируется в контексте свойства [**DataContext**](https://msdn.microsoft.com/library/windows/apps/BR208713) страницы, в качестве которого в этом примере задан экземпляр **HostViewModel**. Путь ссылается на свойство **HostViewModel.NextButtonText**. Свойство **Mode** можно опустить, так как здесь используется расширение разметки [{Binding}](https://msdn.microsoft.com/library/windows/apps/Mt204782) по умолчанию для односторонней привязки.

Значение свойства [**DataContext**](https://msdn.microsoft.com/library/windows/apps/BR208713) по умолчанию для элемента пользовательского интерфейса наследуется от его родительского объекта. Конечно, вы можете переопределить это значение по умолчанию, явно задав свойство **DataContext**, которое в свою очередь по умолчанию наследуется дочерними элементами. Явное задание свойства **DataContext** для элемента полезно, когда вам нужны несколько привязок, использующих один источник.

У объекта привязки есть свойство **Source**, которое по умолчанию устанавливается на свойство [**DataContext**](https://msdn.microsoft.com/library/windows/apps/BR208713) элемента пользовательского интерфейса, для которого объявлена привязка. Вы можете переопределить это значение по умолчанию, явно установив свойство **Source**, **RelativeSource** или **ElementName** для привязки (подробные сведения см. в статье о расширении разметки [{Binding}](https://msdn.microsoft.com/library/windows/apps/Mt204782)).

В классе [**DataTemplate**](https://msdn.microsoft.com/library/windows/apps/BR242348) для свойства [**DataContext**](https://msdn.microsoft.com/library/windows/apps/BR208713) устанавливается шаблонный объект данных. Приведенный ниже пример можно использовать в качестве свойства **ItemTemplate** элемента управления, привязанного к коллекции любого типа, содержащей строковые свойства **Title** и **Description**.

```xaml
<DataTemplate x:Key="SimpleItemTemplate">
    <StackPanel Orientation="Vertical" Height="50">
      <TextBlock Text="{Binding Title}"/>
      <TextBlock Text="{Binding Description"/>
    </StackPanel>
  </DataTemplate>
```

> [!Note]
> По умолчанию изменения [**TextBox.Text**](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.textbox.text) отправляются в источник двусторонней привязанного, когда [**TextBox**](https://msdn.microsoft.com/library/windows/apps/BR209683) теряет фокус. Чтобы изменения отправлялись после каждого нажатия клавиши пользователем, установите для перечисления **UpdateSourceTrigger** событие **PropertyChanged** в привязке разметки. Вы также можете полностью взять на себя управление отправкой изменений в источник, установив для перечисления **UpdateSourceTrigger** значение **Explicit**. Затем необходимо обработать события в текстовом поле (обычно с помощью события [**TextBox.TextChanged**](https://msdn.microsoft.com/library/windows/apps/BR209683)), вызвать метод [**GetBindingExpression**](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.frameworkelement.getbindingexpression) для целевого объекта, чтобы получить объект [**BindingExpression**](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.data.bindingexpression.aspx), а в самом конце вызвать метод [**BindingExpression.UpdateSource**](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.data.bindingexpression.updatesource.aspx) для обновления источника данных программными средствами.

Свойство [**Path**](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.data.binding.path) поддерживает несколько вариантов синтаксиса для привязки к вложенным и подключенным свойствам, а также целочисленным и строковым индексаторам. Дополнительные сведения см. в разделе [Синтаксис Property-path](https://msdn.microsoft.com/library/windows/apps/Mt185586). Привязка к строковым индексаторам позволяет выполнять привязку к динамическим свойствам без реализации интерфейса [**ICustomPropertyProvider**](https://msdn.microsoft.com/library/windows/apps/BR209878). Свойство [**ElementName**](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.data.binding.elementname) позволяет эффективно привязывать элементы один к другому. Свойство [**RelativeSource**](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.data.binding.relativesource) можно использовать в различных целях, в том числе в качестве более эффективной альтернативы привязке шаблона внутри класса [**ControlTemplate**](https://msdn.microsoft.com/library/windows/apps/BR209391). Описание других параметров см. в статьях о [расширении разметки {Binding}](https://msdn.microsoft.com/library/windows/apps/Mt204782) и классе [**Binding**](https://msdn.microsoft.com/library/windows/apps/BR209820).

## <a name="what-if-the-source-and-the-target-are-not-the-same-type"></a>Что делать, если тип источника отличается от типа целевого объекта?

Если вы хотите управлять видимостью элемента пользовательского интерфейса на основе значения логического свойства, если элемент пользовательского интерфейса нужно преобразовать для просмотра с использованием цвета, являющегося функцией диапазона числовых значений или тренда, либо если вы хотите отобразить значение даты и/или времени в свойстве элемента пользовательского интерфейса, ожидающего строку, вам необходимо преобразовать значения из одного типа в другой. В некоторых случаях следует предоставить другое свойство правильного типа из класса источника привязки, после чего инкапсулировать логику преобразования и сделать ее возможной для тестирования. Однако это решение не отличается достаточной гибкостью и масштабируемостью, если имеется большое количество свойств источников и целевых объектов, а также их сочетаний. В этом случае есть несколько вариантов решений:

* При использовании {x:Bind} вы можете выполнить привязку напрямую к функции, чтобы осуществить это преобразование.
* Вы также можете указать преобразователь величин, который является объектом для выполнения преобразования. 

## <a name="value-converters"></a>Преобразователи величин

Ниже приведен преобразователь величин, который подходит для единовременной или односторонней привязки и преобразует значение [**DateTime**](https://msdn.microsoft.com/library/windows/apps/xaml/system.datetime.aspx) в строковое значение, содержащее месяц. Класс реализует интерфейс [**IValueConverter**](https://msdn.microsoft.com/library/windows/apps/BR209903).

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

```vbnet
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

Модуль привязки вызывает методы [**Convert**](https://msdn.microsoft.com/library/windows/apps/hh701934) и [**ConvertBack**](https://msdn.microsoft.com/library/windows/apps/hh701938), если для привязки определен параметр [**Converter**](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.data.binding.converter). Когда данные передаются из источника, модуль привязки вызывает метод **Convert** и передает возвращенные данные в целевой объект. Когда данные передаются из целевого объекта (при двусторонней привязке), модуль привязки вызывает метод **ConvertBack** и передает возвращенные данные в источник.

Преобразователь также включает дополнительные параметры: [**ConverterLanguage**](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.data.binding.converterlanguage), позволяющий указать язык, который используется в преобразовании, и [**ConverterParameter**](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.data.binding.converterparameter), позволяющий передать параметр для логики преобразования. Пример использования параметра преобразователя см. в описании [**IValueConverter**](https://msdn.microsoft.com/library/windows/apps/BR209903).

> [!Note]
> Если в преобразовании возникает ошибка, не создавайте исключение. Вместо этого возвращайте свойство [**DependencyProperty.UnsetValue**](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.dependencyproperty.unsetvalue), которое остановит передачу данных.

Чтобы отображать значение по умолчанию, которое используется, когда невозможно разрешить источник привязки, задайте свойство **FallbackValue** для объекта привязки в разметке. Это полезно при обработке ошибок преобразования и форматирования. Это также полезно при привязке к свойствам источника, которые могут существовать не у всех объектов в привязанной коллекции различных типов.

Если вы привязываете текстовый элемент управления к значению, которое не является строкой, модуль привязки данных будет преобразовывать значение в строку. Если значение является ссылочным типом, обработчик привязки данных будет извлекать строковое значение, вызывая [**ICustomPropertyProvider.GetStringRepresentation**](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.data.icustompropertyprovider.getstringrepresentation) или [**IStringable.ToString**](https://msdn.microsoft.com/library/Dn302136), если они доступны, а в противном случае вызывать [**Object.ToString**](https://msdn.microsoft.com/library/windows/apps/system.object.tostring.aspx). Однако отметим, что обработчик привязки данных будет игнорировать любые реализации **ToString**, которые скрывают реализацию базового класса. Вместо этого реализации подкласса должны переопределять метод базового класса **ToString**. Аналогично, в машинных языках все управляемые объекты должны реализовывать интерфейсы [**ICustomPropertyProvider**](https://msdn.microsoft.com/library/windows/apps/BR209878) и [**IStringable**](https://msdn.microsoft.com/library/Dn302135). Однако все вызовы **GetStringRepresentation** и **IStringable.ToString** направляются к **Object.ToString** или переопределению этого метода, но никогда к новой реализации **ToString**, которая скрывает реализацию базового класса.

> [!NOTE]
> Начиная с Windows 10 версии 1607, платформа XAML предоставляет встроенный преобразователь Boolean в Visibility. Преобразователь сопоставляет значение **true** значению перечисления **Visible**, а значение **false** значению **Collapsed**, поэтому можно осуществить привязку свойства Visibility к Boolean без создания преобразователя. Для использования встроенного преобразователя минимальная версия целевого пакета SDK вашего приложения должна быть 14393 или более поздней. Вы не сможете использовать преобразователь, если ваше приложение предназначено для более ранних версий Windows 10. Дополнительные сведения о целевых версиях см. в статье [Адаптивный к версии код](https://msdn.microsoft.com/windows/uwp/debug-test-perf/version-adaptive-code).

## <a name="function-binding-in-xbind"></a>Привязка функций в {x:Bind}

{x:Bind} позволяет использовать функцию на последнем этапе привязки. Этим можно пользоваться для выполнения преобразований, а также привязок, зависящих от нескольких свойств. См. в разделе [ **функции в расширении разметки x: Bind**](function-bindings.md)

<span id="resource-dictionaries-with-x-bind"/>

## <a name="resource-dictionaries-with-xbind"></a>Использование расширения разметки {x:Bind} в словарях ресурсов

[Расширение разметки {x:Bind}](https://msdn.microsoft.com/library/windows/apps/Mt204783) зависит от формирования кода, поэтому ему требуется файл кода программной части, содержащий конструктор, который вызывает метод **InitializeComponent** (для инициализации сформированного кода). Чтобы повторно использовать словарь ресурсов, нужно создать экземпляра его типа (для вызова метода **InitializeComponent**), а не ссылаться на его имя файла. Вот пример того, что делать при наличии имеющегося словаря ресурсов, в котором вы хотите использовать расширение разметки {x:Bind}.

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

Расширение разметки [{x:Bind}](https://msdn.microsoft.com/library/windows/apps/Mt204783) поддерживает функцию, которая называется привязкой событий. Эта функция позволяет указать обработчик для события посредством привязки, которая дополняет события обработки с методом в файле кода программной части. Предположим, у вас есть свойство **RootFrame** в классе **MainPage**.

```csharp
    public sealed partial class MainPage : Page
    {
        ....    
        public Frame RootFrame { get { return Window.Current.Content as Frame; } }
    }
```

Затем вы можете привязать событие **Click** кнопки к методу объекта **Frame**, возвращенного свойством **RootFrame**, как показано ниже. Обратите внимание, что мы также привязываем свойство **IsEnabled** кнопки к другому члену того же объекта **Frame**.

```xaml
    <AppBarButton Icon="Forward" IsCompact="True"
    IsEnabled="{x:Bind RootFrame.CanGoForward, Mode=OneWay}"
    Click="{x:Bind RootFrame.GoForward}"/>
```

Для обработки события этим способом невозможно использовать перегруженные методы. Кроме того, если метод, который обрабатывает событие, содержит параметры, они должны поддерживать назначение из типов всех соответствующих параметров события. В этом случае метод [**Frame.GoForward**](https://msdn.microsoft.com/library/windows/apps/BR242693) не перегружен и не содержит параметров (но он оставался бы допустимым, даже если бы принимал два параметра **object**). Однако метод [**Frame.GoBack**](https://msdn.microsoft.com/library/windows/apps/Dn996568) перегружен, поэтому мы не можем использовать этот метод данным способом.

Привязка событий похожа на реализацию и использование команд (команда— это свойство, которое возвращает объект, реализующий интерфейс [**ICommand**](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.input.icommand.aspx)). С командами можно использовать как [{x:Bind}](https://msdn.microsoft.com/library/windows/apps/Mt204783), так и [{Binding}](https://msdn.microsoft.com/library/windows/apps/Mt204782). Чтобы приведенный выше шаблон команд не требовалось реализовывать несколько раз, вы можете использовать вспомогательный класс **DelegateCommand**, приведенный в примере [QuizGame](https://github.com/Microsoft/Windows-appsample-quizgame) (в папке "Общие").


## <a name="binding-to-a-collection-of-folders-or-files"></a>Привязка к коллекции папок или файлов

Для извлечения данных из папок и файлов можно использовать API в пространстве имен [**Windows.Storage**](https://msdn.microsoft.com/library/windows/apps/BR227346). Однако разные методы **GetFilesAsync**, **GetFoldersAsync** и **GetItemsAsync** не возвращают значения, которые подходят для привязки к элементам управления списком. Вместо этого необходимо выполнить привязку к возвращаемым значениям методов [**GetVirtualizedFilesVector**](https://msdn.microsoft.com/library/windows/apps/Hh701422), [**GetVirtualizedFoldersVector**](https://msdn.microsoft.com/library/windows/apps/Hh701428) и [**GetVirtualizedItemsVector**](https://msdn.microsoft.com/library/windows/apps/Hh701430) класса [**FileInformationFactory**](https://msdn.microsoft.com/library/windows/apps/BR207501). В приведенном ниже примере кода из [статьи с примером StorageDataSource и GetVirtualizedFilesVector](http://go.microsoft.com/fwlink/p/?linkid=228621) демонстрируется типичное использование. Не забудьте объявить возможность **picturesLibrary** в манифесте пакета приложения и убедитесь, что в вашей папке библиотеки изображений есть изображения.

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

Этот подход обычно используется для создания доступных только для чтения сведений о файлах или папке. Можно создать двухсторонние привязки к свойствам файлов и папок, например, чтобы позволить пользователям оценивать композиции в музыкальном представлении. Однако любые изменения сохраняются только после вызова соответствующего метода **SavePropertiesAsync** (например, [**MusicProperties.SavePropertiesAsync**](https://msdn.microsoft.com/library/windows/apps/BR207760)). Когда элемент теряет фокус, изменения следует принять, так как это инициирует сброс выбранных элементов.

Обратите внимание, что двухсторонняя привязка на основе этой методики работает только с проиндексированными расположениями, такими как "Музыка". Для проверки индексации расположения можно вызвать метод [**FolderInformation.GetIndexedStateAsync**](https://msdn.microsoft.com/library/windows/apps/BR207627).

Кроме того, следует иметь в виду, что виртуализированный вектор для некоторых элементов может возвратить значение **null** до заполнения их значений. Необходимо, например, выполнять проверку на **null** перед использованием значения [**SelectedItem**](https://msdn.microsoft.com/library/windows/apps/BR209770) в элементе управления "Список", который привязан к виртуализированному вектору, или использовать вместо этого свойство [**SelectedIndex**](https://msdn.microsoft.com/library/windows/apps/BR209768).

## <a name="binding-to-data-grouped-by-a-key"></a>Привязка к данным, сгруппированным на основании ключа

Если взять плоскую коллекцию элементов, например книги, представленные классом **BookSku**, и сгруппировать элементы с помощью общего свойства в качестве ключа, например свойства **BookSku.AuthorName**, то в результате будут получены сгруппированные данные. При группировке данные больше не являются плоской коллекцией. Сгруппированные данные— это коллекция объектов группы, каждый из которых имеет а) ключ и б) коллекцию элементов, свойства которых соответствуют этому ключу. Если вернуться к примеру с книгами, при группировании книг по имени автора будет получена коллекция групп с именами автора, причем каждая группа будет иметь а) ключ (в этом случае— имя автора) и б) коллекцию элементов **BookSku**, свойство **AuthorName** которых соответствует ключу группы.

В общем, для отображения коллекции необходимо привязать свойство [**ItemsSource**](https://msdn.microsoft.com/library/windows/apps/BR242828) элемента управления (например, [**ListView**](https://msdn.microsoft.com/library/windows/apps/BR242878) или [**GridView**](https://msdn.microsoft.com/library/windows/apps/BR242705)) непосредственно к свойству, которое возвращает коллекцию. Если это плоская коллекция элементов, вам не нужно выполнять никаких особенных действий. Однако если это коллекция объектов группы (что наблюдается при привязке к сгруппированным данным), вам нужно воспользоваться объектом-посредником с именем [**CollectionViewSource**](https://msdn.microsoft.com/library/windows/apps/BR209833), который размещается между элементом управления и источником привязки. Объект **CollectionViewSource** следует привязать к свойству, возвращающему сгруппированные данные, а элемент управления— к объекту **CollectionViewSource**. Дополнительное добавление объекта **CollectionViewSource** обеспечивает отслеживание с его помощью текущего элемента, поэтому вы можете синхронизировать несколько элементов управления путем их привязки к **CollectionViewSource**. Кроме того, вы можете получить доступ к текущему элементу программными средствами с помощью свойства [**ICollectionView.CurrentItem**](https://msdn.microsoft.com/library/windows/apps/BR209857) объекта, возвращенного свойством [**CollectionViewSource.View**](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.data.collectionviewsource.view).

Чтобы активировать возможность группирования с помощью класса [**CollectionViewSource**](https://msdn.microsoft.com/library/windows/apps/BR209833), установите для свойства [**IsSourceGrouped**](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.data.collectionviewsource.issourcegrouped) значение **true**. Целесообразность одновременного задания свойства [**ItemsPath**](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.data.collectionviewsource.itemspath) зависит от того, как именно вы создаете объекты группы. Объект группы можно создать двумя способами: с помощью шаблона "is-a-group" или "has-a-group". В шаблоне "is-a-group" объект группы создается на основе типа коллекции (например, **List&lt;T&gt;**), поэтому сам объект группы является группой элементов. Если используется этот шаблон, не нужно задавать свойство **ItemsPath**. В шаблоне "has-a-group" объект группы включает одно или несколько свойств типа коллекции (например, **List&lt;T&gt;**), поэтому группа включает группу элементов в форме свойства (или несколько групп элементов в виде ряда свойств). При использовании этого шаблона для **ItemsPath** необходимо задать имя свойства, содержащего группу элементов.

В примере ниже приведен шаблон has-a-group. Класс страницы имеет свойство [**ViewModel**](https://msdn.microsoft.com/library/windows/apps/BR208713), которое возвращает экземпляр нашей модели представления. [**CollectionViewSource**](https://msdn.microsoft.com/library/windows/apps/BR209833) привязывается к свойству **Authors** модели представления (**Authors** — это коллекция объектов группы), а также указывает, что это свойство **Author.BookSkus** содержит сгруппированные элементы. Наконец, класс [**GridView**](https://msdn.microsoft.com/library/windows/apps/BR242705) привязан к классу **CollectionViewSource**, а его стиль группы определен таким образом, чтобы он мог обрабатывать элементы в группах.

```csharp
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

Вы можете реализовать шаблон "is-a-group" одним из двух способов. Первый способ — создать собственный класс группы. Создайте класс на основе **List&lt;T&gt;** (где *T*— это тип элементов). Например, `public class Author : List<BookSku>`. Второй способ — использовать выражение [LINQ](http://msdn.microsoft.com/library/bb397926.aspx) для динамического создания объектов группы (и класса группы) из вероятных значений свойств элементов **BookSku**. Этот подход с поддержкой только плоского списка элементов и их группированием в ходе процесса обычно используется приложением, которое получает доступ к данным из облачной службы. Это позволяет гибко группировать книги по авторам или жанрам (к примеру) без специальных классов групп, таких как **Author** и **Genre**.

В примере ниже показан шаблон "is-a-group", использующий выражение [LINQ](http://msdn.microsoft.com/library/bb397926.aspx). На этот раз мы группируем книги по жанрам, причем названия жанров отображаются в заголовках групп. На это указывает путь свойства Key в ссылке на значение [**Key**](https://msdn.microsoft.com/library/windows/apps/bb343251.aspx) группы.

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
                orderby grp.Key select grp;
            }
            return this.genres;
        }
    }
```

Помните, что при использовании расширения разметки [{x:Bind}](https://msdn.microsoft.com/library/windows/apps/Mt204783) с шаблонами данных необходимо указать тип, к которому выполняется привязка, установив значение **x:DataType**. Если это универсальный тип, мы не сможем отобразить это в разметке, поэтому в шаблоне заголовка стиля группы необходимо использовать расширение разметки [{Binding}](https://msdn.microsoft.com/library/windows/apps/Mt204782).

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

С помощью элемента управления [**SemanticZoom**](https://msdn.microsoft.com/library/windows/apps/Hh702601) пользователи могут эффективно просматривать сгруппированные данные и переходить между ними. Пример приложения [Bookstore2](http://go.microsoft.com/fwlink/?linkid=532952) демонстрирует, как использовать элемент управления **SemanticZoom**. В этом приложении вы можете просмотреть список книг, сгруппированных по автору (представление с увеличенным масштабом). Уменьшите масштаб представления, чтобы просмотреть список переходов между авторами. Список переходов — это гораздо более быстрый способ навигации, чем прокрутка списка книг. Увеличенное и уменьшенное представления являются элементами управления **ListView** и **GridView**, привязанными к одному классу **CollectionViewSource**.

![Иллюстрация элемента управления SemanticZoom](images/sezo.png)

Привязывая иерархические данные, такие как подкатегории в пределах категории, вы можете отобразить уровни иерархии в своем пользовательском интерфейсе с помощью ряда элементов управления. Объекты, выбранные в одном элементе управления, определяют содержимое последующих элементов управления. Списки можно синхронизировать, привязав каждый из них к его классу [**CollectionViewSource**](https://msdn.microsoft.com/library/windows/apps/BR209833), а также привязав экземпляры **CollectionViewSource** в виде цепи. Это называется главным или подробным представлением (либо представлением списка или сведений). Дополнительные сведения см. в [статье, посвященной привязке к иерархическим данным и созданию главного или подробного представления](how-to-bind-to-hierarchical-data-and-create-a-master-details-view.md).

<span id="debugging"/>

## <a name="diagnosing-and-debugging-data-binding-problems"></a>Диагностика и отладка проблем, связанных с привязкой данных

Ваша разметка привязки включает имена свойств (а для C# иногда также поля и методы). Поэтому при переименовании свойства вам также понадобится изменить все привязки, которые ссылаются на него. Невыполнение этого требования может привести к типичному примеру ошибки при привязке данных. В таком случае приложение не будет скомпилировано или будет работать неправильно.

Как правило, объекты привязки, создаваемые с помощью расширений разметки [{x:Bind}](https://msdn.microsoft.com/library/windows/apps/Mt204783) и [{Binding}](https://msdn.microsoft.com/library/windows/apps/Mt204782), выполняют аналогичные функции. Однако расширение разметки {x:Bind} включает сведения о типе источника привязки, что обеспечивает формирование исходного кода во время компиляции. При использовании расширения разметки {x:Bind} обнаружение проблем выполняется так же, как с помощью остальной части кода. Действия по обнаружению включают проверку выражений привязки во время компиляции и отладку путем вставки точек останова в исходный код, сформированный в качестве разделяемого класса для страницы. Эти классы можно найти в папке `obj` в файлах с такими именами, как (для C#) `<view name>.g.cs`. Если при привязке возникает проблема, установите флажок **Останавливать при неперехваченных исключениях** в отладчике Microsoft Visual Studio. Отладчик прервет выполнение в этой точке, после чего вы можете перейти к отладке. В коде, формируемом расширением разметки {x:Bind}, применяется один шаблон для каждой части графа узлов источника привязки. С помощью сведений в окне **Стек вызовов** вы можете определить последовательность вызовов, которые привели к проблеме.

В расширении разметки [{Binding}](https://msdn.microsoft.com/library/windows/apps/Mt204782) не указываются сведения о типе источника привязки. Однако, когда приложение выполняется с подключенным отладчиком, любые ошибки привязки отображаются в окне **Вывод** приложения Visual Studio.

## <a name="creating-bindings-in-code"></a>Создание привязок в коде

**Примечание.** Этот раздел относится только к [{Binding}](https://msdn.microsoft.com/library/windows/apps/Mt204782), потому что невозможно создать привязки [{x:Bind}](https://msdn.microsoft.com/library/windows/apps/Mt204783) в коде. Однако некоторые из преимуществ {x:Bind} можно получить с помощью метода [**DependencyObject.RegisterPropertyChangedCallback**](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.dependencyobject.registerpropertychangedcallback.aspx), что позволяет зарегистрироваться для получения уведомлений об изменениях любого свойства зависимости.

Вы также можете связывать элементы пользовательского интерфейса в процедурах в коде вместо использования XAML. Для этого создайте новый объект [**Binding**](https://msdn.microsoft.com/library/windows/apps/BR209820), задайте соответствующие свойства, а затем вызовите [**FrameworkElement.SetBinding**](https://msdn.microsoft.com/library/windows/apps/br244257.aspx) или [**BindingOperations.SetBinding**](https://msdn.microsoft.com/library/windows/apps/br244376.aspx). Создание привязок программными средствами полезно, когда требуется выбирать значения свойств привязки во время выполнения или совместно использовать одну привязку в нескольких элементах управления. Однако отметим, что невозможно использовать значения свойств привязки после вызова **SetBinding**.

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

| Функция | {x:Bind} | {Binding} | Примечания |
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
| RelativeSource: TemplatedParent | Не требуется | `{Binding <path>, RelativeSource={RelativeSource TemplatedParent}}` | {X: Bind} TargetType на ControlTemplate означает привязку к родительского шаблона. Для {Binding} обычную привязку шаблонов можно использовать в шаблонах элементов управления в большинстве сценариев. Однако, если вам требуется преобразователь или двусторонняя привязка, используйте TemplatedParent.< | 
| Источник | Не требуется | `<ListView ItemsSource="{Binding Orders, Source={StaticResource MyData}}"/>` | Для {x: Bind} можно напрямую использовать именованный элемент, используйте свойство или статический путь. | 
| Режим | `{x:Bind Name, Mode=OneWay}` | `{Binding Name, Mode=TwoWay}` | "Режим" может иметь значение OneTime, OneWay или TwoWay. В расширении разметки {x:Bind} по умолчанию используется значение OneTime, а в расширении разметки {Binding} — значение OneWay. | 
| UpdateSourceTrigger | `{x:Bind Name, Mode=TwoWay, UpdateSourceTrigger=PropertyChanged}` | `{Binding UpdateSourceTrigger=PropertyChanged}` | У UpdateSourceTrigger может быть значение "По умолчанию", LostFocus или PropertyChanged. Поведение разметки {x:Bind} не поддерживает UpdateSourceTrigger=Explicit. В расширении разметки {x:Bind} поведение PropertyChanged используется во всех случаях, кроме TextBox.Text, где используется поведение LostFocus. | 



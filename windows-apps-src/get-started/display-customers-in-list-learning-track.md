---
title: 'Обучающий курс: отображение пользователей в виде списка'
description: Узнайте, что необходимо для отображения коллекции объектов Customer в списке.
ms.date: 05/07/2018
ms.topic: article
keywords: приступая к работе, uwp, windows 10, обучающий курс, привязка данных, список
ms.localizationpriority: medium
ms.custom: RS5
ms.openlocfilehash: a949479a021d4f8de592d1991773dd2e31e9769c
ms.sourcegitcommit: aaa4b898da5869c064097739cf3dc74c29474691
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "64564479"
---
# <a name="display-customers-in-a-list"></a>Отображение пользователей в виде списка

Отображение и обработка реальных данных в пользовательском интерфейсе крайне важны для работы многих приложений. В этой статье показано, что необходимо знать, чтобы отобразить коллекцию объектов Customer в списке.

Это не руководство. Если оно вам требуется, используйте наше [руководство по привязке данных](../data-binding/xaml-basics-data-binding.md), в котором приводятся пошаговые инструкции.

Мы начнем с краткого обсуждения привязки данных — что это такое и как это работает. Затем мы добавим **ListView** в пользовательский интерфейс, добавим привязку данных и настроим дополнительные функции для привязки данных.

## <a name="what-do-you-need-to-know"></a>Что необходимо знать?

Привязка данных — это способ отображения данных приложения в пользовательском интерфейсе. Она позволяет *разграничить задачи* в приложении, отделив пользовательский интерфейс от другого кода. Это создает более четкую концептуальную модель, которую проще читать и поддерживать.

Каждая привязка данных состоит из следующих двух компонентов:

* источник, который предоставляет данные для привязки;
* целевой объект в пользовательском интерфейсе, в котором отображаются данные.

Для реализации привязки данных вам необходимо добавить код для источника, который предоставляет данные для привязки. Вам также следует добавить одно из двух расширений разметки в XAML, чтобы указать свойства источника данных. Ниже приведены основные различия между этими двумя типами.

* [**x:Bind**](../xaml-platform/x-bind-markup-extension.md) — строго типизированный объект, который создает код во время компиляции для повышения производительности. x:Bind по умолчанию используется для единовременной привязки, которая оптимизирована для быстрого отображения данных только для чтения, которые не изменяются.
* [**Binding**](../xaml-platform/binding-markup-extension.md) — слабо типизированный объект, который собирается во время выполнения. Это приводит к более низкой производительности по сравнению с x:Bind. В большинстве случаев следует использовать x:Bind вместо Binding. Тем не менее вы скорее встретите этот объект в старом коде. По умолчанию Binding используется для односторонней передачи данных, оптимизированной для данных только для чтения, которые могут измениться в источнике.

Мы рекомендуем использовать **x:Bind**, когда это возможно, и будем показывать этот объект во фрагментах кода в этой статье. Дополнительные сведения о различиях см. в разделе [Сравнение компонентов расширений разметки {x:Bind} и {Binding}](../data-binding/data-binding-in-depth.md#xbind-and-binding-feature-comparison).

## <a name="create-a-data-source"></a>Создание источника данных

Во-первых, вам необходим класс, представляющий данные клиента. В качестве начальной точки мы покажем процесс на этом пустом примере.

```csharp
public class Customer
{
    public string Name { get; set; }
}
```

## <a name="create-a-list"></a>Создание списка

Для отображения клиентов необходимо создать список для их хранения. [ListView](../design/controls-and-patterns/listview-and-gridview.md) — базовый элемент управления XAML, который идеально подходит для выполнения этой задачи. Сейчас для ListView необходимо указать положение на странице, а через некоторое время — значение свойства **ItemSource**.

```xaml
<ListView ItemsSource=""
    HorizontalAlignment="Center"
    VerticalAlignment="Center"/>
```

После привязки данных с элементом ListView рекомендуется вернуться к документации и поэкспериментировать с настройкой его внешнего вида и макета в соответствии с вашими потребностями.

## <a name="bind-data-to-your-list"></a>Привязка данных к списку

После создания базового пользовательского интерфейса для хранения привязок необходимо настроить источник, который будет их предоставлять. Вот как это можно сделать.

```csharp
public sealed partial class MainPage : Page
{
    public ObservableCollection<Customer> Customers { get; }
        = new ObservableCollection<Customer>();

    public MainPage()
    {
        this.InitializeComponent();
          // Add some customers
        this.Customers.Add(new Customer() { Name = "NAME1"});
        this.Customers.Add(new Customer() { Name = "NAME2"});
        this.Customers.Add(new Customer() { Name = "NAME3"});
    }
}
```
```xaml
<ListView ItemsSource="{x:Bind Customers}"
    HorizontalAlignment="Center"
    VerticalAlignment="Center">
    <ListView.ItemTemplate>
        <DataTemplate x:DataType="local:Customer">
            <TextBlock Text="{x:Bind Name}"/>
        </DataTemplate>
    </ListView.ItemTemplate>
</ListView>
```

В разделе [Обзор привязки данных](../data-binding/data-binding-quickstart.md#binding-to-a-collection-of-items) описывается решение схожей задачи, в подразделе о привязке к коллекции элементов. В нашем примере ниже показаны следующие важные действия.

* В коде программной части пользовательского интерфейса создайте свойство типа **ObservableCollection<T>** для хранения объектов Customer.
* Привяжите объект **ItemSource** элемента ListView к этому свойству.
* Предоставьте базовый шаблон **ItemTemplate** для ListView, который определяет, как отображается каждый элемент в списке.

Вы можете снова изучить документацию по [представлению списка](../design/controls-and-patterns/listview-and-gridview.md), если хотите изменить макет, добавить выбор элементов или настроить **DataTemplate**. Но что делать, если вы хотите изменить клиентов?

## <a name="edit-your-customers-through-the-ui"></a>Изменение клиентов в пользовательском интерфейсе

Вы показали клиентов в списке, но привязка данных дает вам больше возможностей. Что, если бы вы могли редактировать данные непосредственно в пользовательском интерфейсе? Для этого сначала поговорим о трех режимах привязки данных.

* *Единовременная:* такая привязка данных включается только один раз и не реагирует на изменения.
* *Односторонняя*: такая привязка данных изменяет пользовательский интерфейс на основе изменений, внесенных в источник данных.
* *Двусторонняя*: такая привязка обновляет пользовательский интерфейс на основе всех изменений, внесенных в источник данных, а также дополняет данные любыми изменениями, внесенными в пользовательском интерфейсе.

Если вы изучили предыдущие фрагменты кода, вы знаете, что созданная привязка использует x:Bind и не указывает режим, т. е. она является единовременной привязкой. Если вы хотите изменить клиентов непосредственно в пользовательском интерфейсе, вам необходимо сделать привязку двусторонней, чтобы изменения данных передавались в объекты Customer. Дополнительные сведения см. в статье [Подробно о привязке данных](../data-binding/data-binding-in-depth.md).

Двусторонняя привязка также обновляет пользовательский интерфейс, если источник данных был изменен. Чтобы это работало, необходимо реализовать [**INotifyPropertyChanged**](https://msdn.microsoft.com/library/system.componentmodel.inotifypropertychanged(d=robot).aspx) в источнике и убедиться, что его методы задания свойств вызывают событие **PropertyChanged**. Общепринятая практика заключается в вызове вспомогательного метода, например **OnPropertyChanged**, как показано ниже.

```csharp
public class Customer : INotifyPropertyChanged
{
    private string _name;
    public string Name
    {
        get => _name;
        set
        {
            if (_name != value)
                {
                    _name = value;
                    this.OnPropertyChanged();
                }
        }
    }
    public event PropertyChangedEventHandler PropertyChanged;

    public void OnPropertyChanged([CallerMemberName] string propertyName = null) =>
        this.PropertyChanged?.Invoke(this, new PropertyChangedEventArgs(propertyName));
}
```
Затем сделайте текст в элементе ListView доступным для редактирования, используя **TextBox** вместо **TextBlock**, и установите для свойства **Mode** привязок данных значение **TwoWay**.

```xaml
<ListView ItemsSource="{x:Bind Customers}"
    HorizontalAlignment="Center"
    VerticalAlignment="Center">
    <ListView.ItemTemplate>
        <DataTemplate x:DataType="local:Customer">
            <TextBox Text="{x:Bind Name, Mode=TwoWay}"/>
        </DataTemplate>
    </ListView.ItemTemplate>
</ListView>
```

Быстрый способ убедиться, что это работает, — добавить второй ListView с элементами управления TextBox и привязками OneWay. Значения во втором списке будут изменяться автоматически при редактировании первого списка.

> [!NOTE]
> Редактирование непосредственно внутри ListView — это простой способ отображения двусторонней привязки в действии, но он может усложнить использование. Для дальнейшего развития приложения можно использовать [другие элементы управления XAML](../design/controls-and-patterns/controls-and-events-intro.md), которые позволяют изменять данные и сделать элемент ListView доступным только для отображения.

## <a name="going-further"></a>Дальнейшая работа

После создания списка клиентов с помощью двусторонней привязки вы можете вернуться к документам по ссылкам и поэкспериментировать. Вы также можете изучить [руководство по привязке данных](../data-binding/xaml-basics-data-binding.md), чтобы ознакомиться с пошаговыми инструкциями по использованию базовых и расширенных привязок или ознакомиться с такими элементами управления, как [шаблон основных и подробных данных](../design/controls-and-patterns/master-details.md), чтобы создать более надежный пользовательский интерфейс.

## <a name="useful-apis-and-docs"></a>Полезные интерфейсы API и документы

Вот краткое описание интерфейсов API и другая полезная документация, которая поможет вам приступить к работе с привязкой данных.

### <a name="useful-apis"></a>Полезные интерфейсы API

| API | Описание |
|------|---------------|
| [Шаблон данных](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.DataTemplate) | Описание визуальной структуры объекта данных, которая позволяет отображать определенные элементы в пользовательском интерфейсе. |
| [x:Bind](../xaml-platform/x-bind-markup-extension.md) | Документация по рекомендуемому расширению разметки x:Bind. |
| [Binding](../xaml-platform/binding-markup-extension.md) | Документация по старому расширению разметки Binding. |
| [ListView](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.ListView) | Элемент управления пользовательским интерфейсом, отображающий элементы данных в вертикальной стопке. |
| [TextBox](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.TextBox) | Базовый элемент управления текстом для отображения редактируемого текста в пользовательском интерфейсе. |
| [INotifyPropertyChanged](https://msdn.microsoft.com/library/system.componentmodel.inotifypropertychanged(d=robot).aspx) | Интерфейс для отображения данных, который предоставляет их для привязки данных. |
| [ItemsControl](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.ItemsControl) | Свойство **ItemsSource** этого класса позволяет привязать ListView к источнику данных. |

### <a name="useful-docs"></a>Полезные документы

| Раздел | Описание |
|-------|----------------|
| [Подробно о привязке данных](../data-binding/data-binding-in-depth.md) | Общий обзор принципов привязки данных. |
| [Общие сведения о привязке данных](../data-binding/data-binding-quickstart.md) | Подробные концептуальные сведения о привязке данных. |
| [Представление списка](../design/controls-and-patterns/listview-and-gridview.md) | Сведения о создании и настройке элемента управления ListView, в том числе о реализации **DataTemplate**. |

## <a name="useful-code-samples"></a>Полезные примеры кода

| Пример кода | Описание |
|-----------------|---------------|
| [Руководство по привязке данных](../data-binding/xaml-basics-data-binding.md) | Пошаговое руководство по основам привязки данных. |
| [ListView и GridView](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/XamlListView) | Изучите более сложные элементы ListView с привязкой данных. |
| [QuizGame](https://github.com/Microsoft/Windows-appsample-networkhelper) | Ознакомьтесь с привязкой данных в действии, включая класс **BindableBase** (в папке "Common"), для стандартной реализации **INotifyPropertyChanged**. |

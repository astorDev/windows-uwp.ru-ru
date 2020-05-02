---
Description: Фильтрация элементов в коллекции с помощью пользовательского ввода.
title: Фильтрация коллекций
label: Filtering collections
template: detail.hbs
ms.date: 12/3/2019
ms.topic: article
keywords: windows 10, uwp
pm-contact: anawish
ms.openlocfilehash: 24669b81c244339509e30a43a0da8a2b27e67eeb
ms.sourcegitcommit: 76e8b4fb3f76cc162aab80982a441bfc18507fb4
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/29/2020
ms.locfileid: "75302658"
---
# <a name="filtering-collections-and-lists-through-user-input"></a>Фильтрация коллекций и списков с помощью пользовательского ввода
Если в коллекции отображается много элементов или они сильно привязаны к взаимодействию пользователя, внедрение фильтрации будет полезным решением. Фильтрацию с помощью метода, описанного в этой статье, можно внедрить в большинство элементов управления коллекции, включая [ListView](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.ListView), [GridView](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.gridview) и [ItemsRepeater](https://docs.microsoft.com/uwp/api/microsoft.ui.xaml.controls.itemsrepeater?view=winui-2.2). Для фильтрации коллекции можно использовать множество типов пользовательского ввода, таких как флажки, переключатели и ползунки, но в этой статье основное внимание будет уделено получению пользовательского ввода на основе текста, а также его использованию для обновления ListView в режиме реального времени в соответствии с результатами поиска пользователя. 

> [!NOTE]
> В этой статье представлена фильтрация с помощью ListView. Обратите внимание, что метод фильтрации можно также применить к другим элементам управления коллекциями, например GridView, ItemsRepeater или TreeView.

## <a name="setting-up-the-ui-and-xaml-for-filtering"></a>Настройка пользовательского интерфейса и XAML для фильтрации
Для реализации фильтрации ваше приложение должно иметь ListView, который должен появиться рядом с текстовым полем или другим элементом управления, позволяющим вводить данные. В качестве фильтра будет использоваться текст, который пользователь вводит в текстовое поле, т. е. будут отображаться только результаты, содержащие запрос ввода/поиска текста. По мере ввода пользователем текста в текстовое поле ListView будет постоянно обновляться с помощью отфильтрованных результатов, в частности при каждом изменении текста в текстовом поле, даже если изменена только одна буква, ListView будет проходить через элементы и выполнять фильтрацию с помощью этого условия.

В приведенном ниже коде показан пользовательский интерфейс с простым ListView и его DataTemplate, а также прилагаемое текстовое поле. В этом примере в ListView представлено коллекцию объектов Person. Person — это класс, определенный в коде программной части (не показанный в примере кода ниже). Каждый объект Person имеет следующие свойства: FirstName, LastName и Company.

Используя текстовое поле, пользователи могут ввести термин для поиска/фильтрации, чтобы отфильтровать список объектов Person по фамилиям. Обратите внимание, что текстовое поле привязано к определенному имени (`FilterByLName`) и имеет собственное событие TextChanged (`FilteredLV_LNameChanged`). Привязка имени позволяет получить доступ к содержимому/тексту текстового поля в коде программной части, а событие TextChanged будет срабатывать при каждом вводе текста в текстовое поле, что позволяет выполнять операцию фильтрации после получения пользовательского ввода. 

Чтобы фильтрация сработала, ListView необходимо предоставить источник данных, которым можно управлять в коде программной части, например `ObservableCollection<>`. В этом случае в коде программной части `ObservableCollection<Person>` присваивается свойство ItemsSource ListView. 

```xaml
<Grid>
    <Grid.ColumnDefinitions>
        <ColumnDefinition Width="1*"></ColumnDefinition>
        <ColumnDefinition Width="1*"></ColumnDefinition>
    </Grid.ColumnDefinitions>
    <Grid.RowDefinitions>
            <RowDefinition Height="400"></RowDefinition>
            <RowDefinition Height="400"></RowDefinition>
    </Grid.RowDefinitions>

    <ListView x:Name="FilteredListView"
                Grid.Column="0"
                Margin="0,0,20,0">

        <ListView.ItemTemplate>
            <DataTemplate x:DataType="local:Person">
                <StackPanel>
                    <TextBlock Style="{ThemeResource BaseTextBlockStyle}" Margin="0,5,0,5">
                        <Run Text="{x:Bind FirstName}"></Run>
                        <Run Text="{x:Bind LastName}"></Run>
                    </TextBlock>
                    <TextBlock Style="{ThemeResource BodyTextBlockStyle}" Margin="0,5,0,5" Text="{x:Bind Company}"/>
                </StackPanel>
            </DataTemplate>
        </ListView.ItemTemplate>

    </ListView>

    <TextBox x:Name="FilterByLName" Grid.Column="1" Header="Last Name" Width="200"
             HorizontalAlignment="Left" VerticalAlignment="Top" Margin="0,0,0,20"
             TextChanged="FilteredLV_LNameChanged"/>
</Grid>
```
## <a name="filtering-the-data"></a>Фильтрация данных
Запросы [LINQ](https://docs.microsoft.com/dotnet/csharp/programming-guide/concepts/linq/introduction-to-linq-queries) позволяют группировать, упорядочивать и выбирать определенные элементы в коллекции. Для фильтрации списка мы создадим запрос LINQ, который будет выбирать только те условия, которые соответствуют введенному пользователем условию поискового запроса или фильтрации в текстовом поле `FilterByLName`. Результат запроса можно назначить объекту коллекции [IEnumerable<T>](https://docs.microsoft.com/dotnet/api/system.collections.generic.ienumerable-1). После создания этой коллекции мы можем использовать ее для сравнения с исходным списком, удаляя элементы, которые не совпадают, и добавляя обратно элементы, которые совпадают (при Backspace).

> [!NOTE]
> Чтобы обеспечить анимирование ListView при добавлении и вычитании элементов самым удобным и понятным способом, важно удалять и добавлять элементы в саму коллекцию ItemsSource ListView, а не создавать новую коллекцию отфильтрованных объектов и присваивать ее свойству ItemsSource ListView.

Для начала необходимо инициализировать исходный источник данных в отдельной коллекции, например `List<T>` или `ObservableCollection<T>`. В этом примере у нас есть `List<Person>`, называемый `People`, который содержит все объекты Person, показанные в ListView (на фрагменте кода ниже не показано заполнение/инициализацию этого списка). Нам также понадобится список для хранения отфильтрованных данных, который будет меняться при каждом применении фильтра. Этим списком будет `ObservableCollection<Person>`, вызываемый `PeopleFiltered`. При инициализации он будет иметь то же содержимое, что и `People`.
 
Приведенный ниже код выполняет операцию фильтрации с помощью следующих шагов, показанных ниже.
 - Присвойте свойству ItemsSource ListView значение `PeopledFiltered`. 
 - Определите событие TextChanged `FilteredLV_LNameChanged()` для текстового поля `FilterByLName`. Отфильтруйте данные в этой функции.
 - Чтобы отфильтровать данные, воспользуйтесь введенным пользователем поисковым запросом/условием фильтрации, используя `FilterByLName.Text`. Используйте запрос LINQ, чтобы выбрать элементы в `People`, чья фамилия содержит термин `FilterByLName.Text`, и добавьте соответствующие элементы в коллекцию с именем `TempFiltered`.
 - Сравните текущую коллекцию `PeopleFiltered` с только что отфильтрованными элементами в `TempFiltered`, при необходимости удаляя и добавляя элементы из `PeopleFiltered`.
 - По мере удаления и добавления элементов из `PeopleFiltered`ListView будет обновляться и выполнять анимирование соответствующим образом.

 ```csharp
using System.Linq;

public MainPage()
{
    // Define People collection to hold all Person objects. 
    // Populate collection - i.e. add Person objects (not shown)
    IList<Person> People = new List<Person>();

    // Create PeopleFiltered collection and copy data from original People collection
    ObservableCollection<Person> PeopleFiltered = new ObservableCollection<Person>(People);

    // Set the ListView's ItemsSource property to the PeopleFiltered collection
    FilteredListView.ItemsSource = PeopleFiltered;

    // ... 
}

private void FilteredLV_LNameChanged(object sender, TextChangedEventArgs e)
{
    /* Perform a Linq query to find all Person objects (from the original People collection)
    that fit the criteria of the filter, save them in a new List called TempFiltered. */
    List<Person> TempFiltered;
    
    /* Make sure all text is case-insensitive when comparing, and make sure 
    the filtered items are in a List object */
    TempFiltered = people.Where(contact => contact.LastName.Contains(FilterByLName.Text, StringComparison.InvariantCultureIgnoreCase)).ToList();
    
    /* Go through TempFiltered and compare it with the current PeopleFiltered collection,
    adding and subtracting items as necessary: */

    // First, remove any Person objects in PeopleFiltered that are not in TempFiltered
    for (int i = PeopleFiltered.Count - 1; i >= 0; i--)
    {
        var item = PeopleFiltered[i];
        if (!TempFiltered.Contains(item))
        {
            PeopleFiltered.Remove(item);
        }
    }

    /* Next, add back any Person objects that are included in TempFiltered and may 
    not currently be in PeopleFiltered (in case of a backspace) */

    foreach (var item in TempFiltered)
    {
        if (!PeopleFiltered.Contains(item))
        {
            PeopleFiltered.Add(item);
        }
    }
}
 ```

Теперь, когда пользователь вводит условия фильтрации в текстовое поле `FilterByLName`, ListView немедленно обновится, чтобы отображались только те пользователи, чьи фамилии содержат условие фильтрации.

## <a name="next-steps"></a>Дальнейшие действия

### <a name="get-the-sample-code"></a>Получение примера кода
- Если у вас установлено приложение </strong>XAML Controls Gallery, щелкните [здесь](xamlcontrolsgallery:/item/ListView), чтобы открыть приложение и увидеть более надежный, подробный пример фильтрации списка на странице ListView.
- Получите [приложение XAML Controls Gallery](https://www.microsoft.com/store/productId/9MSVH128X2ZT) в Microsoft Store.

### <a name="related-articles"></a>Похожие статьи
- [Списки](lists.md)
- [Представления списка и сетки](listview-and-gridview.md)
- [Командный интерфейс коллекции](collection-commanding.md)
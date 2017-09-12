---
author: mcleblanc
ms.assetid: A9D54DEC-CD1B-4043-ADE4-32CD4977D1BF
title: "Обзор привязки данных"
description: "В этом разделе показано, как привязать элемент управления (или другой элемент пользовательского интерфейса) к отдельному элементу или как привязать элемент управления к коллекции элементов в приложении универсальной платформы Windows (UWP)."
ms.author: markl
ms.date: 02/08/2017
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: windows 10, uwp
ms.openlocfilehash: 0acdcf6b9500c017cea70eebf9a41bec78e63ed2
ms.sourcegitcommit: 5ece992c31870df4c089360ef47501bd4ce14fa9
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/22/2017
---
<a name="data-binding-overview"></a>Общие сведения о привязке данных
=====================

\[ Обновлено для приложений UWP в Windows 10. Статьи о Windows 8.x см. в [архиве](http://go.microsoft.com/fwlink/p/?linkid=619132) \]


В этом разделе показано, как привязать элемент управления (или другой элемент пользовательского интерфейса) к отдельному элементу или как привязать элемент управления к коллекции элементов в приложении универсальной платформы Windows (UWP). Кроме того, здесь объясняется, как управлять обработкой элементов, реализовать представление подробностей на основе выбранных данных и преобразовать данные для отображения. Дополнительные сведения см. в статье [Подробно о привязке данных](data-binding-in-depth.md).

<a name="prerequisites"></a>Необходимые условия
-------------------------------------------------------------------------------------------------------------

Материал этого раздела предполагает, что вы умеете создавать простые приложения UWP. Инструкции по созданию первого приложения UWP см. в разделе [Начало работы с приложениями для Windows](https://developer.microsoft.com/windows/getstarted).

<a name="create-the-project"></a>Создание проекта
---------------------------------------------------------------------------------------------------------------------------------

Создайте проект **пустого приложения (Windows Universal)**. Назовите его Quickstart.

<a name="binding-to-a-single-item"></a>Привязка к отдельному элементу
---------------------------------------------------------------------------------------------------------------------------------------------------------

Каждая привязка состоит из целевого объекта привязки и источника привязки. Как правило, целевым объектом является свойство элемента управления или другой элемент пользовательского интерфейса, а источником— свойство экземпляра класса (модель данных или модель представления). В этом примере показано, как привязать элемент управления к отдельному элементу. Целью является свойство **Text** элемента **TextBlock**. Источник— это экземпляр простого класса с именем **Recording**, который представляет собой аудиозапись. Сначала рассмотрим класс.

Добавьте новый класс в свой проект, присвойте ему имя Recording.cs (если вы используете C#, фрагменты кода C++ также предоставлены ниже) и добавьте к нему следующий код.

> [!div class="tabbedCodeSnippets"]
```csharp
namespace Quickstart
{
    public class Recording
    {
        public string ArtistName { get; set; }
        public string CompositionName { get; set; }
        public DateTime ReleaseDateTime { get; set; }
        public Recording()
        {
            this.ArtistName = "Wolfgang Amadeus Mozart";
            this.CompositionName = "Andante in C for Piano";
            this.ReleaseDateTime = new DateTime(1761, 1, 1);
        }
        public string OneLineSummary
        {
            get
            {
                return $"{this.CompositionName} by {this.ArtistName}, released: "
                    + this.ReleaseDateTime.ToString("d");
            }
        }
    }
    public class RecordingViewModel
    {
        private Recording defaultRecording = new Recording();
        public Recording DefaultRecording { get { return this.defaultRecording; } }
    }
}
```
```cpp
    #include <sstream>
    namespace Quickstart
    {
        public ref class Recording sealed
        {
        private:
            Platform::String^ artistName;
            Platform::String^ compositionName;
            Windows::Globalization::Calendar^ releaseDateTime;
        public:
            Recording(Platform::String^ artistName, Platform::String^ compositionName,
                Windows::Globalization::Calendar^ releaseDateTime) :
                artistName{ artistName },
                compositionName{ compositionName },
                releaseDateTime{ releaseDateTime } {}
            property Platform::String^ ArtistName
            {
                Platform::String^ get() { return this->artistName; }
            }
            property Platform::String^ CompositionName
            {
                Platform::String^ get() { return this->compositionName; }
            }
            property Windows::Globalization::Calendar^ ReleaseDateTime
            {
                Windows::Globalization::Calendar^ get() { return this->releaseDateTime; }
            }
            property Platform::String^ OneLineSummary
            {
                Platform::String^ get()
                {
                    std::wstringstream wstringstream;
                    wstringstream << this->CompositionName->Data();
                    wstringstream << L" by " << this->ArtistName->Data();
                    wstringstream << L", released: " << this->ReleaseDateTime->MonthAsNumericString()->Data();
                    wstringstream << L"/" << this->ReleaseDateTime->DayAsString()->Data();
                    wstringstream << L"/" << this->ReleaseDateTime->YearAsString()->Data();
                    return ref new Platform::String(wstringstream.str().c-str());
                }
            }
        };
        public ref class RecordingViewModel sealed
        {
        private:
            Recording^ defaultRecording;
        public:
            RecordingViewModel()
            {
                Windows::Globalization::Calendar^ releaseDateTime = ref new Windows::Globalization::Calendar();
                releaseDateTime->Month = 1;
                releaseDateTime->Day = 1;
                releaseDateTime->Year = 1761;
                this->defaultRecording = ref new Recording{ L"Wolfgang Amadeus Mozart", L"Andante in C for Piano", releaseDateTime };
            }
            property Recording^ DefaultRecording
            {
                Recording^ get() { return this->defaultRecording; };
            }
        };
    }
```

После этого предоставьте класс источника привязки из класса, представляющего страницу разметки. Для этого следует добавить свойство типа **RecordingViewModel** к **MainPage**.

> [!div class="tabbedCodeSnippets"]
```csharp
    namespace Quickstart
    {
        public sealed partial class MainPage : Page
        {
            public MainPage()
            {
                this.InitializeComponent();
                this.ViewModel = new RecordingViewModel();
            }
            public RecordingViewModel ViewModel { get; set; }
        }
    }
```
```cpp
    namespace Quickstart
    {
        public ref class MainPage sealed
        {
        private:
            RecordingViewModel^ viewModel;
        public:
            MainPage()
            {
                InitializeComponent();
                this->viewModel = ref new RecordingViewModel();
            }
            property RecordingViewModel^ ViewModel
            {
                RecordingViewModel^ get() { return this->viewModel; };
            }
        };
    }
```

Последний шаг— привязка элемента **TextBlock** к свойству **ViewModel.DefaultRecording.OneLiner**.

```xml
    <Page x:Class="Quickstart.MainPage" ... >
        <Grid Background="{ThemeResource ApplicationPageBackgroundThemeBrush}">
            <TextBlock Text="{x:Bind ViewModel.DefaultRecording.OneLineSummary}"
            HorizontalAlignment="Center"
            VerticalAlignment="Center"/>
        </Grid>
    </Page>
```

Вот результат.

![Привязка блока текста](images/xaml-databinding0.png)

<a name="binding-to-a-collection-of-items"></a>Привязка к коллекции элементов
------------------------------------------------------------------------------------------------------------------

Распространенный сценарий— привязка к коллекции бизнес-объектов. В C# и Visual Basic универсальный класс [**ObservableCollection&lt;T&gt;**](https://msdn.microsoft.com/library/windows/apps/xaml/ms668604.aspx) является хорошим вариантом коллекции для привязки данных, поскольку реализует интерфейсы [**INotifyPropertyChanged**](https://msdn.microsoft.com/library/windows/apps/xaml/system.componentmodel.inotifypropertychanged.aspx) и [**INotifyCollectionChanged**](https://msdn.microsoft.com/library/windows/apps/xaml/system.collections.specialized.inotifycollectionchanged.aspx). Эти интерфейсы предоставляют уведомления об изменении для привязок при добавлении или удалении элементов, а также при изменении свойства самого списка. Если необходимо обновлять привязанные элементы управления при изменениях свойств объектов в коллекции, бизнес-объект также должен реализовать интерфейс **INotifyPropertyChanged**. Дополнительные сведения см. в статье [Подробно о привязке данных](data-binding-in-depth.md).

В следующем примере выполняется привязка класса [**ListView**](https://msdn.microsoft.com/library/windows/apps/BR242878) к коллекции объектов `Recording`. Сначала добавим коллекцию к нашей модели представления. Просто добавьте эти новые члены в класс **RecordingViewModel**.

> [!div class="tabbedCodeSnippets"]
```csharp
    public class RecordingViewModel
    {
        ...
        private ObservableCollection<Recording> recordings = new ObservableCollection<Recording>();
        public ObservableCollection<Recording> Recordings { get { return this.recordings; } }
        public RecordingViewModel()
        {
            this.recordings.Add(new Recording() { ArtistName = "Johann Sebastian Bach",
            CompositionName = "Mass in B minor", ReleaseDateTime = new DateTime(1748, 7, 8) });
            this.recordings.Add(new Recording() { ArtistName = "Ludwig van Beethoven",
            CompositionName = "Third Symphony", ReleaseDateTime = new DateTime(1805, 2, 11) });
            this.recordings.Add(new Recording() { ArtistName = "George Frideric Handel",
            CompositionName = "Serse", ReleaseDateTime = new DateTime(1737, 12, 3) });
        }
    }
```
```cpp
    public ref class RecordingViewModel sealed
    {
    private:
        ...
        Windows::Foundation::Collections::IVector<Recording^>^ recordings;
    public:
        RecordingViewModel()
        {
            ...
            releaseDateTime = ref new Windows::Globalization::Calendar();
            releaseDateTime->Month = 7;
            releaseDateTime->Day = 8;
            releaseDateTime->Year = 1748;
            Recording^ recording = ref new Recording{ L"Johann Sebastian Bach", L"Mass in B minor", releaseDateTime };
            this->Recordings->Append(recording);
            releaseDateTime = ref new Windows::Globalization::Calendar();
            releaseDateTime->Month = 2;
            releaseDateTime->Day = 11;
            releaseDateTime->Year = 1805;
            recording = ref new Recording{ L"Ludwig van Beethoven", L"Third Symphony", releaseDateTime };
            this->Recordings->Append(recording);
            releaseDateTime = ref new Windows::Globalization::Calendar();
            releaseDateTime->Month = 12;
            releaseDateTime->Day = 3;
            releaseDateTime->Year = 1737;
            recording = ref new Recording{ L"George Frideric Handel", L"Serse", releaseDateTime };
            this->Recordings->Append(recording);
        }
        ...
        property Windows::Foundation::Collections::IVector<Recording^>^ Recordings
        {
            Windows::Foundation::Collections::IVector<Recording^>^ get()
            {
                if (this->recordings == nullptr)
                {
                    this->recordings = ref new Platform::Collections::Vector<Recording^>();
                }
                return this->recordings;
            };
        }
    };
```

Затем привяжите класс [**ListView**](https://msdn.microsoft.com/library/windows/apps/BR242878) к свойству **ViewModel.Recordings**.

```xml
<Page x:Class="Quickstart.MainPage" ... >
    <Grid Background="{ThemeResource ApplicationPageBackgroundThemeBrush}">
        <ListView ItemsSource="{x:Bind ViewModel.Recordings}"
        HorizontalAlignment="Center" VerticalAlignment="Center"/>
    </Grid>
</Page>
```

Мы еще не указали шаблон данных для класса **Recording**, поэтому инфраструктура пользовательского интерфейса может только вызвать метод [**ToString**](https://msdn.microsoft.com/library/windows/apps/system.object.tostring.aspx) для каждого элемента в классе [**ListView**](https://msdn.microsoft.com/library/windows/apps/BR242878). По умолчанию метод **ToString** возвращает имя типа.

![Привязка списка](images/xaml-databinding1.png)

Чтобы решить эту проблему, мы можем переопределить метод [**ToString**](https://msdn.microsoft.com/library/windows/apps/system.object.tostring.aspx) таким образом, чтобы он возвращал значение **OneLineSummary**, или указать шаблон данных. Шаблон данных— более распространенное и, возможно, гибкое решение. Шаблон данных задается с помощью свойства [**ContentTemplate**](https://msdn.microsoft.com/library/windows/apps/BR209369) элемента управления содержимым или с помощью свойства [**ItemTemplate**](https://msdn.microsoft.com/library/windows/apps/BR242830) элемента управления элементами. Ниже приведены два способа разработки шаблона данных для класса **Recording**, а также показан результат его применения.

```xml
    <ListView ItemsSource="{x:Bind ViewModel.Recordings}"
        HorizontalAlignment="Center" VerticalAlignment="Center">
        <ListView.ItemTemplate>
            <DataTemplate x:DataType="local:Recording">
                <TextBlock Text="{x:Bind OneLineSummary}"/>
            </DataTemplate>
        </ListView.ItemTemplate>
    </ListView>
```

![Привязка списка](images/xaml-databinding2.png)

```xml
    <ListView ItemsSource="{x:Bind ViewModel.Recordings}"
    HorizontalAlignment="Center" VerticalAlignment="Center">
        <ListView.ItemTemplate>
            <DataTemplate x:DataType="local:Recording">
                <StackPanel Orientation="Horizontal" Margin="6">
                    <SymbolIcon Symbol="Audio" Margin="0,0,12,0"/>
                    <StackPanel>
                        <TextBlock Text="{x:Bind ArtistName}" FontWeight="Bold"/>
                        <TextBlock Text="{x:Bind CompositionName}"/>
                    </StackPanel>
                </StackPanel>
            </DataTemplate>
        </ListView.ItemTemplate>
    </ListView>
```

![Привязка списка](images/xaml-databinding3.png)

Дополнительные сведения о синтаксисе языка XAML см. в разделе [Создание пользовательского интерфейса с помощью XAML](https://msdn.microsoft.com/library/windows/apps/Mt228349). Дополнительные сведения о структуре элементов управления см. в разделе [Определение макетов с помощью XAML](https://msdn.microsoft.com/library/windows/apps/Mt228350).

<a name="adding-a-details-view"></a>Добавление представления подробностей
-----------------------------------------------------------------------------------------------------

Вы можете отобразить все сведения объектов **Recording** в элементах [**ListView**](https://msdn.microsoft.com/library/windows/apps/BR242878), но они занимают много места. Вместо этого в элементе можно отобразить данные об объеме, достаточном для определения элемента. Затем, когда пользователь выберет содержимое, вы можете отобразить все сведения о выбранном элементе в отдельном элементе пользовательского интерфейса, известном как представление подробностей. Оно также называется главным представлением либо представлением списка или сведений.

Этот вопрос можно решить двумя способами. Вы можете привязать представление подробностей к свойству [**SelectedItem**](https://msdn.microsoft.com/library/windows/apps/BR209770) класса [**ListView**](https://msdn.microsoft.com/library/windows/apps/BR242878). Вы также можете использовать класс [**CollectionViewSource**](https://msdn.microsoft.com/library/windows/apps/BR209833): привязать как класс **ListView**, так и представление подробностей к классу **CollectionViewSource** (который обеспечит обработку выбранного в настоящее время элемента). Оба способа описаны ниже. Они дают аналогичные результаты, показанные на иллюстрации.

> [!NOTE]
> Пока что в этом разделе мы использовали только [расширение разметки {x:Bind}](https://msdn.microsoft.com/library/windows/apps/Mt204783), но для обоих приведенных ниже методов требуется более гибкое (но менее производительное) [расширение разметки {Binding}](https://msdn.microsoft.com/library/windows/apps/Mt204782).

Сначала рассмотрим способ с использованием свойства [**SelectedItem**](https://msdn.microsoft.com/library/windows/apps/BR209770). Если вы используете расширениякомпонентов Visual C++ (C++/CX), вам потребуется добавить атрибут [**BindableAttribute**](https://msdn.microsoft.com/library/windows/apps/Hh701872) в класс **Recording**, так как мы будем использовать [{Binding}](https://msdn.microsoft.com/library/windows/apps/Mt204782).

```cpp
    [Windows::UI::Xaml::Data::Bindable]
    public ref class Recording sealed
    {
        ...
    };
```

Единственное изменение необходимо внести в разметку.

```xml
<Page x:Class="Quickstart.MainPage" ... >
    <Grid Background="{ThemeResource ApplicationPageBackgroundThemeBrush}">
        <StackPanel HorizontalAlignment="Center" VerticalAlignment="Center">
            <ListView x:Name="recordingsListView" ItemsSource="{x:Bind ViewModel.Recordings}">
                <ListView.ItemTemplate>
                    <DataTemplate x:DataType="local:Recording">
                        <StackPanel Orientation="Horizontal" Margin="6">
                            <SymbolIcon Symbol="Audio" Margin="0,0,12,0"/>
                            <StackPanel>
                                <TextBlock Text="{x:Bind CompositionName}"/>
                            </StackPanel>
                        </StackPanel>
                    </DataTemplate>
                </ListView.ItemTemplate>
            </ListView>
            <StackPanel DataContext="{Binding SelectedItem, ElementName=recordingsListView}"
            Margin="0,24,0,0">
                <TextBlock Text="{Binding ArtistName}"/>
                <TextBlock Text="{Binding CompositionName}"/>
                <TextBlock Text="{Binding ReleaseDateTime}"/>
            </StackPanel>
        </StackPanel>
    </Grid>
</Page>
```

Что касается способа с использованием класса [**CollectionViewSource**](https://msdn.microsoft.com/library/windows/apps/BR209833), сначала добавьте **CollectionViewSource** в качестве ресурса страницы.

```xml
    <Page.Resources>
        <CollectionViewSource x:Name="RecordingsCollection" Source="{x:Bind ViewModel.Recordings}"/>
    </Page.Resources>
```

Затем настройте привязки в классе [**ListView**](https://msdn.microsoft.com/library/windows/apps/BR242878) (который уже не должен быть именованным) и в представлении подробностей, чтобы использовать класс [**CollectionViewSource**](https://msdn.microsoft.com/library/windows/apps/BR209833). Обратите внимание, что при привязке представления подробностей непосредственно к классу **CollectionViewSource** подразумевается, что вы хотите выполнить привязку к текущему элементу в привязках, если невозможно найти путь в самой коллекции. Нет необходимости указывать свойство **CurrentItem** в качестве пути для привязки, хотя это можно сделать, если присутствует некая неоднозначность.

```xml
    ...

    <ListView ItemsSource="{Binding Source={StaticResource RecordingsCollection}}">

    ...

    <StackPanel DataContext="{Binding Source={StaticResource RecordingsCollection}}" ...>
    ...
```

Ниже приведены аналогичные результаты, полученные в каждом случае.

![Привязка списка](images/xaml-databinding4.png)

<a name="formatting-or-converting-data-values-for-display"></a>Форматирование или преобразование значений данных для отображения
--------------------------------------------------------------------------------------------------------------------------------------------

С описанной выше обработкой возникает одна небольшая проблема. Свойство **ReleaseDateTime** — это не просто дата, а [**DateTime**](https://msdn.microsoft.com/library/windows/apps/xaml/system.datetime.aspx), поэтому оно отображается с большей точностью, чем нам нужно. Возможное решение проблемы— добавить строковое свойство в класс **Recording**, который возвращает `this.ReleaseDateTime.ToString("d")`. Присвоение этому свойству имени **ReleaseDate** указывает, что оно возвращает дату, а не дату и время. Присвоение ему имени **ReleaseDateAsString** дополнительно указывает, что оно возвращает строку.

Более гибкое решение— использовать преобразователь величин. Вот пример того, как создать собственный преобразователь величин. Добавьте следующий код в файл исходного кода Recording.cs.

```csharp
public class StringFormatter : Windows.UI.Xaml.Data.IValueConverter
{
    // This converts the value object to the string to display.
    // This will work with most simple types.
    public object Convert(object value, Type targetType,
        object parameter, string language)
    {
        // Retrieve the format string and use it to format the value.
        string formatString = parameter as string;
        if (!string.IsNullOrEmpty(formatString))
        {
            return string.Format(formatString, value);
        }

        // If the format string is null or empty, simply
        // call ToString() on the value.
        return value.ToString();
    }

    // No need to implement converting back on a one-way binding
    public object ConvertBack(object value, Type targetType,
        object parameter, string language)
    {
        throw new NotImplementedException();
    }
}
```

Теперь мы можем добавить экземпляр **StringFormatter** в качестве ресурса страницы и использовать его в нашей привязке. В преобразователь мы передаем строку формата из разметки, чтобы максимально увеличить гибкость форматирования.

```xml
    <Page.Resources>
        <local:StringFormatter x:Key="StringFormatterValueConverter"/>
    </Page.Resources>
    ...

    <TextBlock Text="{Binding ReleaseDateTime,
        Converter={StaticResource StringFormatterValueConverter},
        ConverterParameter=Released: \{0:d\}}"/>

    ...
```

Вот результат.

![отображение даты в пользовательском форматировании](images/xaml-databinding5.png)

> [!NOTE]
> Начиная с Windows 10 версии 1607, платформа XAML предоставляет встроенный преобразователь Boolean в Visibility. Преобразователь сопоставляет значение **true** значению перечисления **Visible**, а значение **false** значению **Collapsed**, поэтому можно осуществить привязку свойства Visibility к Boolean без создания преобразователя. Для использования встроенного преобразователя минимальная версия целевого пакета SDK вашего приложения должна быть 14393 или более поздней. Вы не сможете использовать преобразователь, если ваше приложение предназначено для более ранних версий Windows 10. Дополнительные сведения о целевых версиях см. в статье [Адаптивный к версии код](https://msdn.microsoft.com/windows/uwp/debug-test-perf/version-adaptive-code).

## <a name="see-also"></a>См. также
- [Привязка данных](index.md)

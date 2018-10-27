---
author: stevewhims
ms.assetid: A9D54DEC-CD1B-4043-ADE4-32CD4977D1BF
title: Обзор привязки данных
description: В этом разделе показано, как привязать элемент управления (или другой элемент пользовательского интерфейса) к отдельному элементу или как привязать элемент управления к коллекции элементов в приложении универсальной платформы Windows (UWP).
ms.author: stwhi
ms.date: 10/05/2018
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
dev_langs:
- csharp
- cppwinrt
- cppcx
ms.openlocfilehash: 581c2bc4204a7eab002ef2680e335a4109716d74
ms.sourcegitcommit: 086001cffaf436e6e4324761d59bcc5e598c15ea
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/27/2018
ms.locfileid: "5707513"
---
# <a name="data-binding-overview"></a>Общие сведения о привязке данных

В этом разделе показано, как привязать элемент управления (или другой элемент пользовательского интерфейса) к отдельному элементу или как привязать элемент управления к коллекции элементов в приложении универсальной платформы Windows (UWP). Кроме того, здесь объясняется, как управлять обработкой элементов, реализовать представление подробностей на основе выбранных данных и преобразовать данные для отображения. Дополнительные сведения см. в статье [Подробно о привязке данных](data-binding-in-depth.md).

## <a name="prerequisites"></a>Необходимые условия

Материал этого раздела предполагает, что вы умеете создавать простые приложения UWP. Инструкции по созданию первого приложения UWP см. в разделе [Начало работы с приложениями для Windows](https://developer.microsoft.com/windows/getstarted).

## <a name="create-the-project"></a>Создание проекта

Создайте проект **пустого приложения (Windows Universal)**. Назовите его Quickstart.

## <a name="binding-to-a-single-item"></a>Привязка к отдельному элементу

Каждая привязка состоит из целевого объекта привязки и источника привязки. Как правило, целевым объектом является свойство элемента управления или другой элемент пользовательского интерфейса, а источником— свойство экземпляра класса (модель данных или модель представления). В этом примере показано, как привязать элемент управления к отдельному элементу. Целью является свойство **Text** элемента **TextBlock**. Источник— это экземпляр простого класса с именем **Recording**, который представляет собой аудиозапись. Сначала рассмотрим класс.

Если вы используете C# или C + +/ CX, затем добавьте новый класс в свой проект и назовите класс **записи**.

Если вы используете [C + +/ WinRT](/windows/uwp/cpp-and-winrt-apis/intro-to-using-cpp-with-winrt), затем добавит новые элементы **Файла Midl (.idl)** в проект с именем, как показано в C + +/ WinRT код примера ниже. Замените содержимое этих новых файлов с кодом [MIDL 3.0](/uwp/midl-3/intro) , отображаются в описании, соберите проект, чтобы создать `Recording.h` и `.cpp` и `RecordingViewModel.h` и `.cpp`, а затем добавьте код в созданные файлы в соответствии с описанием. Дополнительные сведения об этих созданных файлов и как копировать их в свой проект, см. в разделе [элементы управления XAML; привязка к C + +/ WinRT свойства](/windows/uwp/cpp-and-winrt-apis/binding-property).

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

```cppwinrt
// Recording.idl
namespace Quickstart
{
    runtimeclass Recording
    {
        Recording(String artistName, String compositionName, Windows.Globalization.Calendar releaseDateTime);
        String ArtistName{ get; };
        String CompositionName{ get; };
        Windows.Globalization.Calendar ReleaseDateTime{ get; };
        String OneLineSummary{ get; };
    }
}

// RecordingViewModel.idl
import "Recording.idl";

namespace Quickstart
{
    runtimeclass RecordingViewModel
    {
        RecordingViewModel();
        Quickstart.Recording DefaultRecording{ get; };
    }
}

// Recording.h
// Add these fields:
...
#include <sstream>
...
private:
    std::wstring m_artistName;
    std::wstring m_compositionName;
    Windows::Globalization::Calendar m_releaseDateTime;
...

// Recording.cpp
// Implement like this:
...
Recording::Recording(hstring const& artistName, hstring const& compositionName, Windows::Globalization::Calendar const& releaseDateTime) :
    m_artistName{ artistName.c_str() },
    m_compositionName{ compositionName.c_str() },
    m_releaseDateTime{ releaseDateTime } {}

hstring Recording::ArtistName(){ return hstring{ m_artistName }; }
hstring Recording::CompositionName(){ return hstring{ m_compositionName }; }
Windows::Globalization::Calendar Recording::ReleaseDateTime(){ return m_releaseDateTime; }

hstring Recording::OneLineSummary()
{
    std::wstringstream wstringstream;
    wstringstream << m_compositionName.c_str();
    wstringstream << L" by " << m_artistName.c_str();
    wstringstream << L", released: " << m_releaseDateTime.MonthAsNumericString().c_str();
    wstringstream << L"/" << m_releaseDateTime.DayAsString().c_str();
    wstringstream << L"/" << m_releaseDateTime.YearAsString().c_str();
    return hstring{ wstringstream.str().c_str() };
}
...

// RecordingViewModel.h
// Add this field:
...
#include "Recording.h"
...
private:
    Quickstart::Recording m_defaultRecording{ nullptr };
...

// RecordingViewModel.cpp
// Implement like this:
...
Quickstart::Recording RecordingViewModel::DefaultRecording()
{
    Windows::Globalization::Calendar releaseDateTime;
    releaseDateTime.Year(1761);
    releaseDateTime.Month(1);
    releaseDateTime.Day(1);
    m_defaultRecording = winrt::make<Recording>(L"Wolfgang Amadeus Mozart", L"Andante in C for Piano", releaseDateTime);
    return m_defaultRecording;
}
...
```

```cppcx
// Recording.h
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
                return ref new Platform::String(wstringstream.str().c_str());
            }
        }
    };
    public ref class RecordingViewModel sealed
    {
    private:
        Recording ^ defaultRecording;
    public:
        RecordingViewModel()
        {
            Windows::Globalization::Calendar^ releaseDateTime = ref new Windows::Globalization::Calendar();
            releaseDateTime->Year = 1761;
            releaseDateTime->Month = 1;
            releaseDateTime->Day = 1;
            this->defaultRecording = ref new Recording{ L"Wolfgang Amadeus Mozart", L"Andante in C for Piano", releaseDateTime };
        }
        property Recording^ DefaultRecording
        {
            Recording^ get() { return this->defaultRecording; };
        }
    };
}

// Recording.cpp
#include "pch.h"
#include "Recording.h"
```

После этого предоставьте класс источника привязки из класса, представляющего страницу разметки. Для этого следует добавить свойство типа **RecordingViewModel** к **MainPage**.

Если вы используете [C + +/ WinRT](/windows/uwp/cpp-and-winrt-apis/intro-to-using-cpp-with-winrt), а затем Первое обновление `MainPage.idl`. Создание проекта для повторного создания `MainPage.h` и `.cpp`и внести изменения в этих файлах, созданный в те в свой проект.

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
        public RecordingViewModel ViewModel{ get; set; }
    }
}
```

```cppwinrt
// MainPage.idl
// Add this property:
import "RecordingViewModel.idl";
...
RecordingViewModel ViewModel{ get; };
...

// MainPage.h
// Add this property and this field:
...
#include "RecordingViewModel.h"
...
    Quickstart::RecordingViewModel ViewModel();

private:
    Quickstart::RecordingViewModel m_viewModel{ nullptr };
...

// MainPage.cpp
// Implement like this:
...
MainPage::MainPage()
{
    InitializeComponent();
    m_viewModel = winrt::make<RecordingViewModel>();
}
Quickstart::RecordingViewModel MainPage::ViewModel()
{
    return m_viewModel;
}
...
```

```cppcx
// MainPage.h
...
#include "Recording.h"

namespace Quickstart
{
    public ref class MainPage sealed
    {
    private:
        RecordingViewModel ^ viewModel;
    public:
        MainPage();

        property RecordingViewModel^ ViewModel
        {
            RecordingViewModel^ get() { return this->viewModel; };
        }
    };
}

// MainPage.cpp
...
MainPage::MainPage()
{
    InitializeComponent();
    this->viewModel = ref new RecordingViewModel();
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

Если вы используете [C + +/ WinRT](/windows/uwp/cpp-and-winrt-apis/intro-to-using-cpp-with-winrt), вам следует удалить функцию **MainPage::ClickHandler** в для сборки проекта.

Вот результат.

![Привязка блока текста](images/xaml-databinding0.png)

## <a name="binding-to-a-collection-of-items"></a>Привязка к коллекции элементов

Распространенный сценарий— привязка к коллекции бизнес-объектов. В C# и Visual Basic универсальный класс [**ObservableCollection&lt;T&gt;**](https://msdn.microsoft.com/library/windows/apps/xaml/ms668604.aspx) является хорошим вариантом коллекции для привязки данных, поскольку реализует интерфейсы [**INotifyPropertyChanged**](https://msdn.microsoft.com/library/windows/apps/xaml/system.componentmodel.inotifypropertychanged.aspx) и [**INotifyCollectionChanged**](https://msdn.microsoft.com/library/windows/apps/xaml/system.collections.specialized.inotifycollectionchanged.aspx). Эти интерфейсы предоставляют уведомления об изменении для привязок при добавлении или удалении элементов, а также при изменении свойства самого списка. Если необходимо обновлять привязанные элементы управления при изменениях свойств объектов в коллекции, бизнес-объект также должен реализовать интерфейс **INotifyPropertyChanged**. Дополнительные сведения см. в статье [Подробно о привязке данных](data-binding-in-depth.md).

Если вы используете [C + +/ WinRT](/windows/uwp/cpp-and-winrt-apis/intro-to-using-cpp-with-winrt), а затем Дополнительные сведения о привязке к отслеживаемой коллекции в [элементы управления XAML; привязка к C + +/ WinRT коллекции](/windows/uwp/cpp-and-winrt-apis/binding-collection). Если вы Прочитайте этот раздел во-первых, затем намерение C + +/ WinRT код приведенных ниже будет более четкими.

В следующем примере выполняется привязка класса [**ListView**](https://msdn.microsoft.com/library/windows/apps/BR242878) к коллекции объектов `Recording`. Сначала добавим коллекцию к нашей модели представления. Просто добавьте эти новые члены в класс **RecordingViewModel**.

```csharp
public class RecordingViewModel
{
    ...
    private ObservableCollection<Recording> recordings = new ObservableCollection<Recording>();
    public ObservableCollection<Recording> Recordings{ get{ return this.recordings; } }
    public RecordingViewModel()
    {
        this.recordings.Add(new Recording(){ ArtistName = "Johann Sebastian Bach",
            CompositionName = "Mass in B minor", ReleaseDateTime = new DateTime(1748, 7, 8) });
        this.recordings.Add(new Recording(){ ArtistName = "Ludwig van Beethoven",
            CompositionName = "Third Symphony", ReleaseDateTime = new DateTime(1805, 2, 11) });
        this.recordings.Add(new Recording(){ ArtistName = "George Frideric Handel",
            CompositionName = "Serse", ReleaseDateTime = new DateTime(1737, 12, 3) });
    }
}
```

```cppwinrt
// RecordingViewModel.idl
// Add this property:
...
Windows.Foundation.Collections.IVector<IInspectable> Recordings{ get; };
...

// RecordingViewModel.h
// Change the constructor declaration, and add this property and this field:
...
    RecordingViewModel();
    Windows::Foundation::Collections::IVector<Windows::Foundation::IInspectable> Recordings();

private:
    Windows::Foundation::Collections::IVector<Windows::Foundation::IInspectable> m_recordings;
...

// RecordingViewModel.cpp
// Implement like this:
...
RecordingViewModel::RecordingViewModel()
{
    std::vector<Windows::Foundation::IInspectable> recordings;

    Windows::Globalization::Calendar releaseDateTime;
    releaseDateTime.Month(7); releaseDateTime.Day(8); releaseDateTime.Year(1748);
    recordings.push_back(winrt::make<Recording>(L"Johann Sebastian Bach", L"Mass in B minor", releaseDateTime));

    releaseDateTime = Windows::Globalization::Calendar{};
    releaseDateTime.Month(11); releaseDateTime.Day(2); releaseDateTime.Year(1805);
    recordings.push_back(winrt::make<Recording>(L"Ludwig van Beethoven", L"Third Symphony", releaseDateTime));

    releaseDateTime = Windows::Globalization::Calendar{};
    releaseDateTime.Month(3); releaseDateTime.Day(12); releaseDateTime.Year(1737);
    recordings.push_back(winrt::make<Recording>(L"George Frideric Handel", L"Serse", releaseDateTime));

    m_recordings = winrt::single_threaded_observable_vector<Windows::Foundation::IInspectable>(std::move(recordings));
}

Windows::Foundation::Collections::IVector<Windows::Foundation::IInspectable> RecordingViewModel::Recordings() { return m_recordings; }
...
```

```cppcx
// Recording.h
...
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
        releaseDateTime->Year = 1748;
        releaseDateTime->Month = 7;
        releaseDateTime->Day = 8;
        Recording^ recording = ref new Recording{ L"Johann Sebastian Bach", L"Mass in B minor", releaseDateTime };
        this->Recordings->Append(recording);
        releaseDateTime = ref new Windows::Globalization::Calendar();
        releaseDateTime->Year = 1805;
        releaseDateTime->Month = 2;
        releaseDateTime->Day = 11;
        recording = ref new Recording{ L"Ludwig van Beethoven", L"Third Symphony", releaseDateTime };
        this->Recordings->Append(recording);
        releaseDateTime = ref new Windows::Globalization::Calendar();
        releaseDateTime->Year = 1737;
        releaseDateTime->Month = 12;
        releaseDateTime->Day = 3;
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

Чтобы решить эту проблему, мы либо можно переопределить [**ToString**](https://msdn.microsoft.com/library/windows/apps/system.object.tostring.aspx) возвращает значение **OneLineSummary**или предусмотрен шаблон данных. Шаблон данных — более обычных решения и более гибкое из них. Шаблон данных задается с помощью свойства [**ContentTemplate**](https://msdn.microsoft.com/library/windows/apps/BR209369) элемента управления содержимым или с помощью свойства [**ItemTemplate**](https://msdn.microsoft.com/library/windows/apps/BR242830) элемента управления элементами. Ниже приведены два способа разработки шаблона данных для класса **Recording**, а также показан результат его применения.

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

## <a name="adding-a-details-view"></a>Добавление представления подробностей

Вы можете отобразить все сведения объектов **Recording** в элементах [**ListView**](https://msdn.microsoft.com/library/windows/apps/BR242878), но они занимают много места. Вместо этого в элементе можно отобразить данные об объеме, достаточном для определения элемента. Затем, когда пользователь выберет содержимое, вы можете отобразить все сведения о выбранном элементе в отдельном элементе пользовательского интерфейса, известном как представление подробностей. Оно также называется главным представлением либо представлением списка или сведений.

Этот вопрос можно решить двумя способами. Вы можете привязать представление подробностей к свойству [**SelectedItem**](https://msdn.microsoft.com/library/windows/apps/BR209770) класса [**ListView**](https://msdn.microsoft.com/library/windows/apps/BR242878). Или можно использовать [**CollectionViewSource**](https://msdn.microsoft.com/library/windows/apps/BR209833)в таком случае привязать **ListView** и представление подробностей к **CollectionViewSource** (проводя так позаботится выбранного в настоящее время элемента для вас). Оба способа показаны ниже, и они дают аналогичные результаты, (показанные на рисунке).

> [!NOTE]
> Пока что в этом разделе мы использовали только [расширение разметки {x:Bind}](https://msdn.microsoft.com/library/windows/apps/Mt204783), но для обоих приведенных ниже методов требуется более гибкое (но менее производительное) [расширение разметки {Binding}](https://msdn.microsoft.com/library/windows/apps/Mt204782).

Если вы используете C + +/ WinRT или VisualC ++ расширения компонентов (C + +/ CX) нажмите, чтобы использовать расширение разметки [{Binding}](https://msdn.microsoft.com/library/windows/apps/Mt204782) , вам потребуется добавить атрибут [**BindableAttribute**](https://msdn.microsoft.com/library/windows/apps/Hh701872) для любого класса среды выполнения, которое вы хотите выполнить привязку к. Чтобы использовать [{x: Bind}](https://msdn.microsoft.com/library/windows/apps/Mt204783), этот атрибут не нужен.

> [!IMPORTANT]
> Если вы используете [C + +/ WinRT](/windows/uwp/cpp-and-winrt-apis/intro-to-using-cpp-with-winrt), а затем атрибут [**BindableAttribute**](https://msdn.microsoft.com/library/windows/apps/Hh701872) доступен, если вы установили пакет Windows SDK версии 10.0.17763.0 (Windows 10, версия 1809) или более поздней версии. Без этого атрибута необходимо реализовывать интерфейсы [ICustomPropertyProvider](/uwp/api/windows.ui.xaml.data.icustompropertyprovider) и [ICustomProperty](/uwp/api/windows.ui.xaml.data.icustomproperty) , чтобы иметь возможность использовать расширение разметки [{Binding}](https://msdn.microsoft.com/library/windows/apps/Mt204782) .

Сначала рассмотрим способ с использованием свойства [**SelectedItem**](https://msdn.microsoft.com/library/windows/apps/BR209770).

```csharp
// No code changes necessary for C#.
```

```cppwinrt
// Recording.idl
// Add this attribute:
...
[Windows.UI.Xaml.Data.Bindable]
runtimeclass Recording
...
```

```cppcx
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

> [!NOTE]
> Если вы используете C++, пользовательский Интерфейс не будет выглядеть так же, как на рисунке ниже: отрисовки свойство **ReleaseDateTime** отличается. В следующем разделе более подробное описание этого.

![Привязка списка](images/xaml-databinding4.png)

## <a name="formatting-or-converting-data-values-for-display"></a>Форматирование или преобразование значений данных для отображения

Существует проблема с описанной выше. Свойство **ReleaseDateTime** не просто Дата, это [**Дата и время**](/uwp/api/windows.foundation.datetime) (Если вы используете C++, то, [**Календарь**](/uwp/api/windows.globalization.calendar)). Таким образом в C#, оно отображается с большей точностью, чем нам нужно. И на языке C++ оно будет отображаться как имя типа. Возможное решение проблемы — добавить строковое свойство в класс **записи** , который возвращает эквивалент `this.ReleaseDateTime.ToString("d")`. Присвоение этому свойству **ReleaseDate** указывает, что оно возвращает дату, а не даты и времени. Присвоение ему имени **ReleaseDateAsString** дополнительно указывает, что оно возвращает строку.

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

```cppwinrt
// StringFormatter.idl
namespace Quickstart
{
    runtimeclass StringFormatter : Windows.UI.Xaml.Data.IValueConverter
    {
        StringFormatter();
    }
}

// StringFormatter.h
#pragma once

#include "StringFormatter.g.h"
#include <sstream>

namespace winrt::Quickstart::implementation
{
    struct StringFormatter : StringFormatterT<StringFormatter>
    {
        StringFormatter() = default;

        Windows::Foundation::IInspectable Convert(Windows::Foundation::IInspectable const& value, Windows::UI::Xaml::Interop::TypeName const& targetType, Windows::Foundation::IInspectable const& parameter, hstring const& language);
        Windows::Foundation::IInspectable ConvertBack(Windows::Foundation::IInspectable const& value, Windows::UI::Xaml::Interop::TypeName const& targetType, Windows::Foundation::IInspectable const& parameter, hstring const& language);
    };
}

namespace winrt::Quickstart::factory_implementation
{
    struct StringFormatter : StringFormatterT<StringFormatter, implementation::StringFormatter>
    {
    };
}

// StringFormatter.cpp
#include "pch.h"
#include "StringFormatter.h"

namespace winrt::Quickstart::implementation
{
    Windows::Foundation::IInspectable StringFormatter::Convert(Windows::Foundation::IInspectable const& value, Windows::UI::Xaml::Interop::TypeName const& /* targetType */, Windows::Foundation::IInspectable const& /* parameter */, hstring const& /* language */)
    {
        // Retrieve the value as a Calendar.
        Windows::Globalization::Calendar valueAsCalendar{ value.as<Windows::Globalization::Calendar>() };

        std::wstringstream wstringstream;
        wstringstream << L"Released: ";
        wstringstream << valueAsCalendar.MonthAsNumericString().c_str();
        wstringstream << L"/" << valueAsCalendar.DayAsString().c_str();
        wstringstream << L"/" << valueAsCalendar.YearAsString().c_str();
        return winrt::box_value(hstring{ wstringstream.str().c_str() });
    }

    Windows::Foundation::IInspectable StringFormatter::ConvertBack(Windows::Foundation::IInspectable const& /* value */, Windows::UI::Xaml::Interop::TypeName const& /* targetType */, Windows::Foundation::IInspectable const& /* parameter */, hstring const& /* language */)
    {
        throw hresult_not_implemented();
    }
}
```

```cppcx
...
public ref class StringFormatter sealed : Windows::UI::Xaml::Data::IValueConverter
{
public:
    virtual Platform::Object^ Convert(Platform::Object^ value, TypeName targetType, Platform::Object^ parameter, Platform::String^ language)
    {
        // Retrieve the value as a Calendar.
        Windows::Globalization::Calendar^ valueAsCalendar = dynamic_cast<Windows::Globalization::Calendar^>(value);

        std::wstringstream wstringstream;
        wstringstream << L"Released: ";
        wstringstream << valueAsCalendar->MonthAsNumericString()->Data();
        wstringstream << L"/" << valueAsCalendar->DayAsString()->Data();
        wstringstream << L"/" << valueAsCalendar->YearAsString()->Data();
        return ref new Platform::String(wstringstream.str().c_str());
    }

    // No need to implement converting back on a one-way binding
    virtual Platform::Object^ ConvertBack(Platform::Object^ value, TypeName targetType, Platform::Object^ parameter, Platform::String^ language)
    {
        throw ref new Platform::NotImplementedException();
    }
};
...
```

Теперь мы можем добавить экземпляр **StringFormatter** в качестве ресурса страницы и использовать его в нашей привязке.

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

Как видно выше, для форматирования гибкость мы используем разметки передавать строку формата в преобразователь через параметр преобразователя. В примерах кода, показанный в этом разделе, только C# преобразователь значений упрощает использование этого параметра. Однако можно легко передавать строку формата стиле C++ в качестве параметра преобразователя и использовать этот символ в преобразователь значений с помощью функции форматирования, например **wprintf** или **swprintf**.

Вот результат.

![отображение даты в пользовательском форматировании](images/xaml-databinding5.png)

> [!NOTE]
> Начиная с Windows 10 версии 1607, платформа XAML предоставляет встроенный преобразователь Boolean в Visibility. Преобразователь сопоставляет **значение true,** значение перечисления **Visibility.Visible** и **значение false,** чтобы **Visibility.Collapsed** , чтобы привязать свойство Visibility к Boolean без создания преобразователя. Для использования встроенного преобразователя минимальная версия целевого пакета SDK вашего приложения должна быть 14393 или более поздней. Вы не сможете использовать преобразователь, если ваше приложение предназначено для более ранних версий Windows 10. Дополнительные сведения о версиях целевого объекта см. в разделе [версии адаптивного кода](https://msdn.microsoft.com/windows/uwp/debug-test-perf/version-adaptive-code).

## <a name="see-also"></a>См. также
* [Привязка данных](index.md)

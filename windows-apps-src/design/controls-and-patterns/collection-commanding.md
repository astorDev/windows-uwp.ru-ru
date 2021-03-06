---
description: В этой статье показано, как использовать контекстно-зависимые команды для реализации такого рода действий способом, который предоставляет наилучшее взаимодействие для всех типов ввода.
title: Контекстно-зависимые команды
ms.assetid: ''
label: Contextual commanding in collections
template: detail.hbs
ms.date: 10/25/2017
ms.topic: article
keywords: windows 10, uwp
pm-contact: chigy
design-contact: kimsea
dev-contact: niallm
doc-status: Published
ms.localizationpriority: medium
ms.openlocfilehash: b97041e305cfaac2a5fe202212741a282dccdb54
ms.sourcegitcommit: 0dee502484df798a0595ac1fe7fb7d0f5a982821
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/08/2020
ms.locfileid: "82968879"
---
# <a name="contextual-commanding-for-collections-and-lists"></a>Контекстно-зависимые команды для коллекций и списков



Многие приложения содержат коллекции содержимого в виде списков, сеток и деревьев, которыми пользователи могут управлять. Например, пользователи могут удалять, переименовывать, отмечать или обновлять элементы. В этой статье показано, как использовать контекстно-зависимые команды для реализации такого рода действий способом, который предоставляет наилучшее взаимодействие для всех типов ввода.  

> **Важные API**: [интерфейс ICommand](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Input.ICommand), [свойство UIElement.ContextFlyout](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.UIElement.ContextFlyout), [интерфейс INotifyPropertyChanged](https://docs.microsoft.com/uwp/api/windows.ui.xaml.data.inotifypropertychanged)

![Используйте различные методы ввода для выполнения команды Favorite](images/ContextualCommand_AddFavorites.png)

## <a name="creating-commands-for-all-input-types"></a>Создание команд для всех типов ввода

Так как пользователи могут взаимодействовать с приложением Windows с помощью [широкого спектра устройств и типов ввода](../devices/index.md), приложения должны предоставлять контекстные меню, не зависящие от метода ввода, и ускорители конкретного метода ввода. Включение обеих позволяет пользователю быстро вызывать команды на содержимом, независимо от типа ввода или устройства.

В этой таблице показаны некоторые стандартные команды коллекции и способы их отображения. 

| Команда          | Не зависит от ввода | Ускоритель мыши | Ускоритель клавиатуры | Ускоритель сенсорного ввода |
| ---------------- | -------------- | ----------------- | -------------------- | ----------------- |
| Удалить элемент      | Контекстное меню   | Кнопка при наведении      | Клавиша DEL              | Провести, чтобы удалить   |
| Отметить элемент        | Контекстное меню   | Кнопка при наведении      | CTRL+SHIFT+G         | Провести пальцем, чтобы пометить     |
| Обновить данные     | Контекстное меню   | Н/Д               | Клавиша F5               | Обновление путем оттягивания   |
| Добавить элемент в избранное | Контекстное меню   | Кнопка при наведении      | F, CTRL+S            | Провести, чтобы добавить в избранное |


* **Как правило, следует создать все команды для элемента, доступные в [контекстном меню](menus.md) элемента.** Контекстные меню доступны пользователям, независимо от типа ввода и должны содержать все контекстно-зависимые команды, которые пользователь может выполнить.

* **Для часто используемых команд рассмотрите возможность использования ускорителей ввода.** Ускорители ввода позволяют пользователю быстро выполнять действия на основе их устройства ввода. Ускорители ввода включают в себя:
    - действие проведения пальцем (ускоритель сенсорного ввода);
    - обновление путем оттягивания (ускоритель сенсорного ввода);
    - сочетания клавиш (ускоритель клавиатуры);
    - клавиши доступа (акселератор клавиатуры);
    - кнопки мыши и пера при наведении (ускоритель указателя).

> [!NOTE]
> Пользователи должны иметь возможность доступа ко всем командам из любого типа устройства. Например, если команды вашего приложения доступны только через ускорители указателя кнопки при наведении, пользователи сенсорного ввода не смогут получить к ним доступ. По крайней мере, используйте контекстное меню для предоставления доступа ко всем командам.  

## <a name="example-the-podcastobject-data-model"></a>Пример: Модель данных PodcastObject

Для демонстрации наших рекомендаций по командам в этой статье создан список подкастов для приложения подкастов. В примере кода показано, как разрешить пользователю "добавлять в избранное" определенный подкаст из списка.

Вот определение объекта подкаста, с которым мы будем работать: 

```csharp
public class PodcastObject : INotifyPropertyChanged
{
    // The title of the podcast
    public String Title { get; set; }

    // The podcast's description
    public String Description { get; set; }

    // Describes if the user has set this podcast as a favorite
    public bool IsFavorite
    {
        get
        {
            return _isFavorite;
        }
        set
        {
            _isFavorite = value;
            OnPropertyChanged("IsFavorite");
        }
    }
    private bool _isFavorite = false;

    public event PropertyChangedEventHandler PropertyChanged;

    private void OnPropertyChanged(String property)
    {
        PropertyChanged?.Invoke(this, new PropertyChangedEventArgs(property));
    }
}
```

Обратите внимание на то, что PodcastObject реализует [INotifyPropertyChanged](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Data.INotifyPropertyChanged), чтобы реагировать на изменения свойств, когда пользователь переключает свойство IsFavorite.

## <a name="defining-commands-with-the-icommand-interface"></a>Определение команд с помощью интерфейса ICommand

Интерфейс [ICommand](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Input.ICommand) помогает определить команду, которая доступна для нескольких типов ввода. Например, вместо написания одного и того же кода для команды удаления в двух разных обработчиках событий, одного — для нажатия клавиши Delete пользователем, и одного — для выбора команды "Удалить" в контекстном меню правой кнопки мыши, вы можете реализовать логику удаления один раз, как [ICommand](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Input.ICommand), и сделать ее доступной для различных типов ввода.

Необходимо определить ICommand, которая представляет действие "Favorite". Мы будем использовать метод [Execute](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Input.ICommand.Execute) команды для добавления подкаста в избранное. Определенный подкаст будет предоставлен методу execute через параметр команды, который может быть связан с помощью свойства CommandParameter.

```csharp
public class FavoriteCommand: ICommand
{
    public event EventHandler CanExecuteChanged;

    public bool CanExecute(object parameter)
    {
        return true;
    }
    public void Execute(object parameter)
    {
        // Perform the logic to "favorite" an item.
        (parameter as PodcastObject).IsFavorite = true;
    }
}
```

Для использования одной и той же команды с несколькими коллекциями и элементами вы можете сохранить команду как ресурс на странице или в приложении.

```xaml
<Application.Resources>
    <local:FavoriteCommand x:Key="favoriteCommand" />
</Application.Resources>
```

Чтобы выполнить команду, следует вызвать ее метод [Execute](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Input.ICommand.Execute).

```csharp
// Favorite the item using the defined command
var favoriteCommand = Application.Current.Resources["favoriteCommand"] as ICommand;
favoriteCommand.Execute(PodcastObject);
```


## <a name="creating-a-usercontrol-to-respond-to-a-variety-of-inputs"></a>Создание элемента UserControl для реагирования на различные методы ввода

Если у вас имеется список элементов и каждый из этих элементов должен реагировать на несколько типов ввода, можно упростить код путем определения [UserControl](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.UserControl) для элемента и его использования для определения контекстного меню и обработчиков событий своих элементов. 

Создание UserControl в Visual Studio.
1. В обозревателе решений щелкните проект правой кнопкой мыши. Откроется контекстное меню.
2. Выберите **Добавить > Новый элемент...** <br />Откроется диалоговое окно **Добавление нового элемента**. 
3. Выберите UserControl из списка элементов. Присвойте ему имя и нажмите кнопку **Добавить**. Visual Studio создаст заглушку UserControl. 

В нашем примере подкаста каждый подкаст будет отображаться в списке, который будет представлять различные способы добавления подкаста в "Избранное". Пользователь сможет выполнять следующие действия для добавления подкаста в "Избранное".
- Вызов контекстного меню
- Выполнение сочетаний клавиш
- Отображение кнопки при наведении
- Жест прокрутки

Чтобы инкапсулировать эти поведения и использовать FavoriteCommand, давайте создадим новый объект [UserControl](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.UserControl) с именем "PodcastUserControl" для представления подкастов в списке.

Элемент PodcastUserControl отображает поля объекта PodcastObject как TextBlocks и отвечает на различные виды взаимодействия с пользователем. Мы будем ссылаться и развертывать объект PodcastUserControl в этой статье.

**PodcastUserControl.xaml**
```xaml
<UserControl
    x:Class="ContextCommanding.PodcastUserControl"
    xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
    xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
    xmlns:d="http://schemas.microsoft.com/expression/blend/2008"
    xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
    mc:Ignorable="d"
    IsTabStop="True" UseSystemFocusVisuals="True"
    >
    <Grid Margin="12,0,12,0">
        <StackPanel>
            <TextBlock Text="{x:Bind PodcastObject.Title, Mode=OneWay}" Style="{StaticResource TitleTextBlockStyle}" />
            <TextBlock Text="{x:Bind PodcastObject.Description, Mode=OneWay}" Style="{StaticResource SubtitleTextBlockStyle}" />
            <TextBlock Text="{x:Bind PodcastObject.IsFavorite, Mode=OneWay}" Style="{StaticResource SubtitleTextBlockStyle}"/>
        </StackPanel>
    </Grid>
</UserControl>
```

**PodcastUserControl.xaml.cs**
```csharp
public sealed partial class PodcastUserControl : UserControl
{
    public static readonly DependencyProperty PodcastObjectProperty =
        DependencyProperty.Register(
            "PodcastObject",
            typeof(PodcastObject),
            typeof(PodcastUserControl),
            new PropertyMetadata(null));

    public PodcastObject PodcastObject
    {
        get { return (PodcastObject)GetValue(PodcastObjectProperty); }
        set { SetValue(PodcastObjectProperty, value); }
    }

    public PodcastUserControl()
    {
        this.InitializeComponent();

        // TODO: We will add event handlers here.
    }
}
```

Обратите внимание на то, что PodcastUserControl содержит ссылку на PodcastObject как DependencyProperty. Это позволяет нам связать PodcastObjects с PodcastUserControl.

После создания нескольких объектов PodcastObjects можно создать список подкастов с помощью привязки PodcastObjects к элементу управления ListView. Объекты PodcastUserControl описывают визуализацию объектов PodcastObjects и, таким образом, задаются с помощью элемента ItemTemplate ListView.

**MainPage.xaml**
```xaml
<ListView x:Name="ListOfPodcasts"
            ItemsSource="{x:Bind podcasts}">
    <ListView.ItemTemplate>
        <DataTemplate x:DataType="local:PodcastObject">
            <local:PodcastUserControl PodcastObject="{x:Bind Mode=OneWay}" />
        </DataTemplate>
    </ListView.ItemTemplate>
    <ListView.ItemContainerStyle>
        <!-- The PodcastUserControl will entirely fill the ListView item and handle tabbing within itself. -->
        <Style TargetType="ListViewItem" BasedOn="{StaticResource ListViewItemRevealStyle}">
            <Setter Property="HorizontalContentAlignment" Value="Stretch" />
            <Setter Property="Padding" Value="0"/>
            <Setter Property="IsTabStop" Value="False"/>
        </Style>
    </ListView.ItemContainerStyle>
</ListView>
```

## <a name="creating-context-menus"></a>Создание контекстных меню

В контекстных меню отображается список команд или параметров, когда пользователь запрашивает их. Контекстные меню предоставляют контекстно-зависимые команды, связанные с их присоединенным элементом, и обычно зарезервированы для дополнительных действий, относящихся к этому элементу.

![Отображение контекстного меню для элемента](images/ContextualCommand_RightClick.png)

Пользователь может вызвать контекстные меню с помощью следующих "контекстных действий".

| Ввод    | Контекстное действие                          |
| -------- | --------------------------------------- |
| Мышь    | Щелчок правой кнопкой мыши                             |
| Клавиатура | SHIFT+F10, кнопка меню                  |
| Touch    | Длительное нажатие элемента                      |
| Перо      | Нажатие кнопки пера, длительное нажатие элемента |
| Геймпад  | Кнопка меню                             |

**Так как пользователь может открыть контекстное меню независимо от типа ввода, ваше контекстное меню должно содержать все контекстно-зависимые команды, доступные для элемента списка.**

### <a name="contextflyout"></a>ContextFlyout

Свойство [ContextFlyout](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.UIElement.ContextFlyout), определенное в классе UIElement, позволяет легко создать контекстное меню, которое работает со всеми типами ввода. Вы предоставляете всплывающий элемент, представляющий ваше контекстное меню, с помощью MenuFlyout, и когда пользователь выполняет "контекстное действие", как указано выше, отображается объект MenuFlyout, соответствующий элементу.

Мы добавим ContextFlyout к PodcastUserControl. MenuFlyout, указанный как ContextFlyout, содержит один элемент для добавления подкаста в избранное. Обратите внимание на то, что этот MenuFlyoutItem использует действие favoriteCommand, определенное выше, с привязкой CommandParamter к PodcastObject.

**PodcastUserControl.xaml**
```xaml
<UserControl>
    <UserControl.ContextFlyout>
        <MenuFlyout>
            <MenuFlyoutItem Text="Favorite" Command="{StaticResource favoriteCommand}" CommandParameter="{x:Bind PodcastObject, Mode=OneWay}" />
        </MenuFlyout>
    </UserControl.ContextFlyout>
    <Grid Margin="12,0,12,0">
        <!-- ... -->
    </Grid>
</UserControl>

```

Обратите внимание на то, что вы также можете использовать [событие ContextRequested](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.UIElement.ContextRequested), чтобы реагировать на действия контекста. Событие ContextRequested не сработает, если был указан ContextFlyout.

## <a name="creating-input-accelerators"></a>Создание ускорителей ввода

Несмотря на то, что каждый элемент в коллекции должен иметь контекстное меню, содержащее все контекстно-зависимых команды, вы можете предоставить пользователям возможность быстро выполнять небольшой набор часто выполняемых команд. Например, почтовые приложения могут иметь дополнительные команды, такие как "Ответить", "Архивировать", "Переместить в папку", "Установить флажок" и "Удалить", которые отображаются в контекстном меню, но наиболее часто используемыми командами являются "Удалить" и "Пометить". После определения, какие команды используются чаще, можно использовать упростить ускорители ввода, чтобы упростить выполнение этих команд пользователями.

В приложении подкастов часто выполняемая команда — "Добавить в избранное".

### <a name="keyboard-accelerators"></a>Сочетания клавиш

#### <a name="shortcuts-and-direct-key-handling"></a>Сочетания клавиш и прямая обработка нажатия клавиш

![Нажатие клавиш CTRL и F для выполнения действия](images/ContextualCommand_Keyboard.png)

В зависимости от типа содержимого вы можете определить определенные сочетания клавиш, которые должны выполнять действие. В приложении электронной почты, например, клавиша DEL может использоваться для удаления выбранного сообщения. В приложении подкастов клавиши CTRL+S или F могут добавлять подкаст в избранное для использования в будущем. Хотя у некоторых команд есть общие, известные сочетания клавиш, например, DEL для удаления, другие команды могут использовать сочетания клавиш для конкретного приложения или домена. По возможности используйте известные клавиши или рассмотрите возможность предоставления текста напоминания в подсказке для обучения пользователя.

Ваше приложение может отвечать, когда пользователь нажимает клавишу, с помощью события [KeyDown](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.UIElement.KeyDownEvent). Как правило, пользователи ожидают, что приложение будет реагировать при первом нажатии клавиши, а не ждать, пока они отпустят ее.

В этом примере рассмотрено, как добавить обработчик KeyDown в PodcastUserControl, чтобы добавить подкаст в Избранное, когда пользователь нажимает клавиши CTRL+S. Используется та же команда, что и раньше.

**PodcastUserControl.xaml.cs**
```csharp
// Respond to the F and Ctrl+S keys to favorite the focused item.
protected override void OnKeyDown(KeyRoutedEventArgs e)
{
    var ctrlState = CoreWindow.GetForCurrentThread().GetKeyState(VirtualKey.Control);
    var isCtrlPressed = (ctrlState & CoreVirtualKeyStates.Down) == CoreVirtualKeyStates.Down || (ctrlState & CoreVirtualKeyStates.Locked) == CoreVirtualKeyStates.Locked;

    if (e.Key == Windows.System.VirtualKey.F || (e.Key == Windows.System.VirtualKey.S && isCtrlPressed))
    {
        // Favorite the item using the defined command
        var favoriteCommand = Application.Current.Resources["favoriteCommand"] as ICommand;
        favoriteCommand.Execute(PodcastObject);
    }
}
```

### <a name="mouse-accelerators"></a>Ускорители мыши

![Наведение указателя мыши на элемент, чтобы показать кнопки](images/ContextualCommand_HovertoReveal.png)

Пользователям знакомы контекстные меню правой кнопки мыши, но вы можете предоставить пользователям возможность выполнять распространенные команды с использованием только одного щелчка мыши. Для включения этой возможности вы можете включить специальные кнопки на холсте элемента коллекции. Чтобы предоставить пользователям возможность действовать быстро с помощью мыши и свести к минимуму визуальные помехи, вы можете выбрать только отображение этих кнопок, когда пользователь наводит указатель на определенный элемент в списке.

В этом примере команда "Добавить в избранное" представлена кнопкой, определенной непосредственно в PodcastUserControl. Отметим, что кнопка в этом примере использует ту же команду, FavoriteCommand, что и раньше. Для переключения видимости этой кнопки можно использовать VisualStateManager для переключения между визуальными состояниями, когда указатель входит в элемент управления или выходит из него.

**PodcastUserControl.xaml**
```xaml
<UserControl>
    <UserControl.ContextFlyout>
        <!-- ... -->
    </UserControl.ContextFlyout>
    <Grid Margin="12,0,12,0">
        <VisualStateManager.VisualStateGroups>
            <VisualStateGroup x:Name="HoveringStates">
                <VisualState x:Name="HoverButtonsShown">
                    <VisualState.Setters>
                        <Setter Target="hoverArea.Visibility" Value="Visible" />
                    </VisualState.Setters>
                </VisualState>
                <VisualState x:Name="HoverButtonsHidden" />
            </VisualStateGroup>
        </VisualStateManager.VisualStateGroups>
        <Grid.ColumnDefinitions>
            <ColumnDefinition Width="*" />
            <ColumnDefinition Width="Auto" />
        </Grid.ColumnDefinitions>
        <StackPanel>
            <TextBlock Text="{x:Bind PodcastObject.Title, Mode=OneWay}" Style="{StaticResource TitleTextBlockStyle}" />
            <TextBlock Text="{x:Bind PodcastObject.Description, Mode=OneWay}" Style="{StaticResource SubtitleTextBlockStyle}" />
            <TextBlock Text="{x:Bind PodcastObject.IsFavorite, Mode=OneWay}" Style="{StaticResource SubtitleTextBlockStyle}"/>
        </StackPanel>
        <Grid Grid.Column="1" x:Name="hoverArea" Visibility="Collapsed" VerticalAlignment="Stretch">
            <AppBarButton Icon="OutlineStar" Label="Favorite" Command="{StaticResource favoriteCommand}" CommandParameter="{x:Bind PodcastObject, Mode=OneWay}" IsTabStop="False" VerticalAlignment="Stretch"  />
        </Grid>
    </Grid>
</UserControl>
```

Кнопки при наведении должны отображаться и исчезать при входе мыши и ее выходе из элемента. Для реагирования на события мыши можно использовать события [PointerEntered](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.UIElement.PointerEnteredEvent) и [PointerExited](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.UIElement.PointerExitedEvent) в PodcastUserControl.

**PodcastUserControl.xaml.cs**
```csharp
protected override void OnPointerEntered(PointerRoutedEventArgs e)
{
    base.OnPointerEntered(e);

    // Only show hover buttons when the user is using mouse or pen.
    if (e.Pointer.PointerDeviceType == Windows.Devices.Input.PointerDeviceType.Mouse || e.Pointer.PointerDeviceType == Windows.Devices.Input.PointerDeviceType.Pen)
    {
        VisualStateManager.GoToState(this, "HoverButtonsShown", true);
    }
}

protected override void OnPointerExited(PointerRoutedEventArgs e)
{
    base.OnPointerExited(e);

    VisualStateManager.GoToState(this, "HoverButtonsHidden", true);
}
```

Кнопки, отображаемые в состоянии наведения, будут доступны только через тип ввода указателя. Так как эти кнопки ограничены вводом указателем, вы можете свести к минимуму или удалить заполнение вокруг значка кнопки, чтобы оптимизировать ввод указателем. Если вы решите сделать это, убедитесь, что размер кнопки не меньше 20х20 пикселей, чтобы ее можно было использовать с пером и мышью.

### <a name="touch-accelerators"></a>Ускорители сенсорного ввода

#### <a name="swipe"></a>Swipe

![Проведение пальцем по элементу для отображения команды](images/ContextualCommand_Swipe.png)

Команды проведения — это акселератор сенсорного ввода, который позволяет пользователям сенсорных устройств выполнять распространенные дополнительные действия с помощью сенсорного ввода. Проведение позволяет пользователям сенсорных устройств быстро и естественно взаимодействовать с содержимым с помощью общих операций, например, прокрутки для удаления или проведения для вызова. В статье [Команды прокрутки](swipe.md) приведены дополнительные сведения.

Чтобы интегрировать проведение в коллекцию, вам потребуются два компонента: SwipeItems, в котором размещается команды, и SwipeControl, который служит оболочкой для элемента и обеспечивает взаимодействие проведением.

SwipeItems может быть определен как Resource в PodcastUserControl. В этом примере SwipeItems содержит команду к добавления элемента в избранное.

```xaml
<UserControl.Resources>
    <SymbolIconSource x:Key="FavoriteIcon" Symbol="Favorite"/>
    <SwipeItems x:Key="RevealOtherCommands" Mode="Reveal">
        <SwipeItem IconSource="{StaticResource FavoriteIcon}" Text="Favorite" Background="Yellow" Invoked="SwipeItem_Invoked"/>
    </SwipeItems>
</UserControl.Resources>
```

SwipeControl создает программу-оболочку элемента, и позволяет пользователю взаимодействовать с ним с помощью жеста прокрутки. Обратите внимание на то, что SwipeControl содержит ссылку на SwipeItems как его RightItems. Элемент Favorite будет отображаться, когда пользователь будет проводить пальцем справа налево.

```xaml
<SwipeControl x:Name="swipeContainer" RightItems="{StaticResource RevealOtherCommands}">
   <!-- The visual state groups moved from the Grid to the SwipeControl, since the SwipeControl wraps the Grid. -->
   <VisualStateManager.VisualStateGroups>
       <VisualStateGroup x:Name="HoveringStates">
           <VisualState x:Name="HoverButtonsShown">
               <VisualState.Setters>
                   <Setter Target="hoverArea.Visibility" Value="Visible" />
               </VisualState.Setters>
           </VisualState>
           <VisualState x:Name="HoverButtonsHidden" />
       </VisualStateGroup>
   </VisualStateManager.VisualStateGroups>
   <Grid Margin="12,0,12,0">
       <Grid.ColumnDefinitions>
           <ColumnDefinition Width="*" />
           <ColumnDefinition Width="Auto" />
       </Grid.ColumnDefinitions>
       <StackPanel>
           <TextBlock Text="{x:Bind PodcastObject.Title, Mode=OneWay}" Style="{StaticResource TitleTextBlockStyle}" />
           <TextBlock Text="{x:Bind PodcastObject.Description, Mode=OneWay}" Style="{StaticResource SubtitleTextBlockStyle}" />
           <TextBlock Text="{x:Bind PodcastObject.IsFavorite, Mode=OneWay}" Style="{StaticResource SubtitleTextBlockStyle}"/>
       </StackPanel>
       <Grid Grid.Column="1" x:Name="hoverArea" Visibility="Collapsed" VerticalAlignment="Stretch">
           <AppBarButton Icon="OutlineStar" Command="{StaticResource favoriteCommand}" CommandParameter="{x:Bind PodcastObject, Mode=OneWay}" IsTabStop="False" LabelPosition="Collapsed" VerticalAlignment="Stretch"  />
       </Grid>
   </Grid>
</SwipeControl>
```

Когда пользователь проводит для вызова команды избранного, вызывается соответствующий метод.

```csharp
private void SwipeItem_Invoked(SwipeItem sender, SwipeItemInvokedEventArgs args)
{
    // Favorite the item using the defined command
    var favoriteCommand = Application.Current.Resources["favoriteCommand"] as ICommand;
    favoriteCommand.Execute(PodcastObject);
}
```

#### <a name="pull-to-refresh"></a>Обновление путем оттягивания

Обновление путем оттягивания позволяет пользователю потянуть коллекцию данных вниз, чтобы получить дополнительные данные. В статье [Обновление путем оттягивания](pull-to-refresh.md) содержатся дополнительные сведения.

### <a name="pen-accelerators"></a>Ускорители пера

Тип ввода пером обеспечивает точность ввода указателем. Пользователи могут выполнять основные действия, например, открывать контекстное меню, с помощью ускорителей пера. Чтобы открыть контекстное меню, пользователи могут коснуться экрана при нажатой кнопке пера или нажать и удерживать содержимое. Пользователи также могут использовать перо для наведения указателя на содержимое, чтобы получить более глубокое понимание пользовательского интерфейса, например, отобразить всплывающие подсказки или дополнительные действия при наведении, аналогично мыши.

Чтобы оптимизировать приложение для ввода с помощью пера, изучите статью [Взаимодействие пера и стилуса](../input/pen-and-stylus-interactions.md).


## <a name="dos-and-donts"></a>Что рекомендуется и что не рекомендуется делать

* Убедитесь, что пользователи имеют доступ ко всем командам со всех типов устройств Windows.
* Включайте контекстное меню, которое предоставляет доступ ко всем командам, доступным для элемента коллекции. 
* Предоставьте ускорители ввода для часто используемых команд. 
* Используйте [интерфейс ICommand](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Input.ICommand) для реализации команд. 

## <a name="related-topics"></a>Связанные темы
* [Интерфейс ICommand](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Input.ICommand)
* [Меню и контекстные меню](menus.md)
* [Проведение](swipe.md)
* [Обновление путем оттягивания](pull-to-refresh.md)
* [Взаимодействие с помощью пера](../input/pen-and-stylus-interactions.md)
* [Настройка приложения для геймпада и Xbox](../devices/designing-for-tv.md)

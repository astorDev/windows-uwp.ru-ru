---
Description: TabView — это гибкое средство, позволяющее упорядочить несколько документов на динамических вкладках.
title: Вкладки
template: detail.hbs
ms.date: 09/12/2019
ms.topic: article
keywords: windows 10, uwp
doc-status: Published
ms.localizationpriority: medium
ms.openlocfilehash: 24c7bd8828ec036135233f569ee7add5d39ffb32
ms.sourcegitcommit: 136416e8e2eb0565bb6eb99e42482c1723ccb8c7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/30/2020
ms.locfileid: "76890429"
---
# <a name="tabview"></a>TabView

Элемент управления TabView позволяет отобразить набор вкладок и их содержимое. Элементы TabView удобно использовать, чтобы отображать несколько страниц (или документов) содержимого, в то же время предоставляя пользователю возможность изменять порядок вкладок, а также открывать новые или закрывать имеющиеся вкладки.

> **Важные API**: [класс TabView](/uwp/api/microsoft.ui.xaml.controls.tabview), [класс TabViewItem](/uwp/api/microsoft.ui.xaml.controls.tabviewitem)

![Пример элемента TabView](images/tabview/tab-introduction.png)

## <a name="is-this-the-right-control"></a>Выбор правильного элемента управления

Как правило, пользовательские интерфейсы с вкладками содержат элементы управления двух разных типов, различающиеся по функциям и внешнему виду. **Статические вкладки** — это вкладки, которые часто встречаются в окнах параметров. На них располагается набор страниц в фиксированном порядке, которые обычно содержат предопределенное содержимое.
**Вкладки документов** — это вкладки, которые можно увидеть в браузере, например Microsoft Edge. Пользователи могут создавать, удалять и переупорядочивать вкладки, перемещать вкладки между окнами, а также и изменять содержимое вкладок.

[TabView](/uwp/api/microsoft.ui.xaml.controls.tabview) предоставляет вкладки документов для приложений UWP. Используйте TabView, если:

- пользователи должны иметь возможность динамически открывать, закрывать или переупорядочивать вкладки;
- пользователи должны иметь возможность открывать документы или веб-страницы непосредственно на вкладках;
- пользователи должны иметь возможность перетаскивать вкладки между окнами.

Если TabView не подходит для вашего приложения, рассмотрите возможность использования таких элементов управления, как [Pivot](https://docs.microsoft.com/windows/uwp/design/controls-and-patterns/pivot) или [NavigationView](https://docs.microsoft.com/windows/uwp/design/controls-and-patterns/navigationview).

## <a name="anatomy"></a>Структура

На рисунке ниже показаны части элемента управления [TabView](/uwp/api/microsoft.ui.xaml.controls.tabview). У элемента TabStrip есть верхний и нижний колонтитулы, но в отличие от документа, верхний и нижний колонтитулы TabStrip находятся в левом и правом концах полосы соответственно.

![Структура элемента управления TabView](images/tabview/tab-view-anatomy.png)

На рисунке ниже показаны части элемента управления [TabViewItem](/uwp/api/microsoft.ui.xaml.controls.tabviewitem). Обратите внимание на то, что хотя содержимое отображается внутри элемента управления TabView, на самом деле оно является частью TabViewItem.

![Структура элемента управления TabViewItem](images/tabview/tab-control-anatomy.png)

### <a name="create-a-tab-view"></a>Создание представления вкладок

В этом примере создается простой элемент [TabView](/uwp/api/microsoft.ui.xaml.controls.tabview) с обработчиками событий для реализации открытия и закрытия вкладок.

```xaml
<TabView AddTabButtonClick="Tabs_AddTabButtonClick"
         TabCloseRequested="Tabs_TabCloseRequested" />
```

```csharp
// Add a new Tab to the TabView
private void Tabs_AddTabButtonClick(TabView sender, TabViewAddTabButtonClickEventArgs e)
{
    var newTab = new TabViewItem();
    newTab.IconSource = new SymbolIconSource() { Symbol = Symbol.Document };
    newTab.Header = "New Document";

    // The Content of a TabViewItem is often a frame which hosts a page.
    Frame frame = new Frame();
    newTab.Content = frame;
    frame.Navigate(typeof(BaconIpsumPage));

    sender.TabItems.Add(newTab);
}

// Remove the requested tab from the TabView
private void Tabs_TabCloseRequested(TabView sender, TabViewTabCloseRequestedEventArgs args)
{
    sender.TabItems.Remove(args.Tab);
}
```

## <a name="behavior"></a>Поведение

Существует несколько способов использования или расширения функциональных возможностей [TabView](/uwp/api/microsoft.ui.xaml.controls.tabview).

### <a name="bind-tabitemssource-to-a-tabviewitemcollection"></a>Привязка TabItemsSource к TabViewItemCollection

```csharp
<TabView TabItemsSource="{x:Bind TabViewItemCollection}" />
```

### <a name="display-tabview-tabs-in-a-windows-titlebar"></a>Отображение вкладок TabView в строке заголовка окна

Вместо того чтобы отображать вкладки в отдельной строке под строкой заголовка окна, можно объединить эти две области в одну. Это позволит сэкономить пространство по вертикали для содержимого и придать приложению современный вид.

Так как пользователь может перетаскивать окно за строку заголовка, важно, чтобы строка заголовка не была полностью заполнена вкладками. Поэтому при отображении вкладок в строке заголовка необходимо указать часть заголовка, которая будет зарезервирована в качестве области для перетаскивания. Если вы не укажете область для перетаскивания, то для перетаскивания будет использоваться вся строка заголовка, что не позволит вкладкам обрабатывать события ввода. Если TabView будет отображаться в строке заголовка окна, то в [TabView](/uwp/api/microsoft.ui.xaml.controls.tabview) следует всегда добавлять элемент [TabStripFooter](/uwp/api/microsoft.ui.xaml.controls.tabview.tabstripfooter) и помечать его как область для перетаскивания.

Дополнительные сведения см. в статье [Настройка заголовка окна](https://docs.microsoft.com/windows/uwp/design/shell/title-bar).

![Вкладки в строке заголовка](images/tabview/tab-extend-to-title.png)

```xaml
<Page>
    <TabView HorizontalAlignment="Stretch" VerticalAlignment="Stretch">
        <TabViewItem Icon="Home" Header="Home" IsClosable="False" />
        <TabViewItem Icon="Document" Header="Document 1" />
        <TabViewItem Icon="Document" Header="Document 2" />
        <TabViewItem Icon="Document" Header="Document 3" />

        <TabView.TabStripHeader>
            <Grid x:Name="ShellTitlebarInset" Background="Transparent" />
        </TabView.TabStripHeader>
        <TabView.TabStripFooter>
            <Grid x:Name="CustomDragRegion" Background="Transparent" />
        </TabView.TabStripFooter>
    </TabView>
</Page>
```

```csharp
public MainPage()
{
    this.InitializeComponent();

    var coreTitleBar = CoreApplication.GetCurrentView().TitleBar;
    coreTitleBar.ExtendViewIntoTitleBar = true;
    coreTitleBar.LayoutMetricsChanged += CoreTitleBar_LayoutMetricsChanged;

    Window.Current.SetTitleBar(CustomDragRegion);
}

private void CoreTitleBar_LayoutMetricsChanged(CoreApplicationViewTitleBar sender, object args)
{
    if (FlowDirection == FlowDirection.LeftToRight)
    {
        CustomDragRegion.MinWidth = sender.SystemOverlayRightInset;
        ShellTitlebarInset.MinWidth = sender.SystemOverlayLeftInset;
    }
    else
    {
        CustomDragRegion.MinWidth = sender.SystemOverlayLeftInset;
        ShellTitlebarInset.MinWidth = sender.SystemOverlayRightInset;
    }

    CustomDragRegion.Height = ShellTitlebarInset.Height = sender.Height;
}
```

>[!NOTE]
> Чтобы вкладки в строке заголовка не перекрывались содержимым оболочки, необходимо учитывать наложения слева и справа. В макетах "слева направо" вставка справа включает в себя кнопки заголовков и область для перетаскивания. В макетах "справа налево" все наоборот. Значения SystemOverlayLeftInset и SystemOverlayRightInset задают отступы по левому и правому краю, поэтому для макета "справа налево" они задают отступы наоборот.

### <a name="control-overflow-behavior"></a>Поведение при переполнении элемента управления

С помощью параметра [TabView.TabWidthMode](/uwp/api/microsoft.ui.xaml.controls.tabview.tabwidthmode) можно управлять отображением вкладок, заполняющих строку вкладок.

| Значение TabWidthMode | Поведение                                                                                                                                                    |
|--------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Равно              | По мере добавления новых вкладок все вкладки будут сжиматься по горизонтали до тех пор, пока они не достигнут очень маленькой минимальной ширины.                                                       |
| SizeToContent      | Вкладки всегда отображаются в "естественном размере" — минимальном размере, необходимом для вывода значка и заголовка. Они не будут расширяться или сжиматься при добавлении или закрытии вкладок. |

Какое бы значение вы ни задали, в итоге может быть открыто слишком много вкладок для отображения на полосе вкладок. В этом случае отобразятся бамперы прокрутки, позволяющие пользователю прокручивать TabStrip влево и вправо.

### <a name="guidance-for-tab-selection"></a>Руководство по выбору вкладок

Большинство пользователей имеет опыт использования вкладок документов в веб-браузере. При использовании вкладок документов в приложении они исходят из имеющегося опыта использования вкладок.

Независимо от того, как пользователь взаимодействует с набором вкладок документов, всегда должна быть активная вкладка. Если пользователь закрывает выбранную вкладку или открывает выбранную вкладку в другом окне, то другая вкладка должна стать активной. Элемент [TabView](/uwp/api/microsoft.ui.xaml.controls.tabview) пытается сделать это автоматически, выбирая следующую вкладку. Если у вас есть веская причина для того, чтобы приложение допускало TabView с невыбранной вкладкой, то область содержимого TabView просто будет пустой.

## <a name="keyboard-navigation"></a>Навигация с помощью клавиатуры

[TabView](/uwp/api/microsoft.ui.xaml.controls.tabview) по умолчанию поддерживает многие распространенные сценарии навигации с помощью клавиатуры. В этом разделе описываются встроенные функции и приводятся рекомендации по дополнительным функциональным возможностям, которые могут быть полезны для некоторых приложений.

### <a name="tab-and-cursor-key-behavior"></a>Поведение клавиши TAB и клавиш курсора

Когда фокус перемещается в область _TabStrip_, он переводится на выбранный элемент [TabViewItem](/uwp/api/microsoft.ui.xaml.controls.tabviewitem). Затем пользователь может использовать клавиши со стрелками влево и вправо для перемещения фокуса на другие вкладки в TabStrip (не выбирая их). Фокус стрелки перемещается по полосе вкладок и кнопке добавления вкладки (+), если она есть. Чтобы переместить фокус из области TabStrip, пользователь может нажать клавишу TAB, которая переместит фокус на следующий элемент. поддерживающий фокус.

Перемещение фокуса с помощью клавиши TAB

![Перемещение фокуса с помощью клавиши TAB](images/tabview/tab-keyboard-behavior-1.png)

Клавиши со стрелками не перемещают фокус по кругу

![Клавиши со стрелками не перемещают фокус по кругу](images/tabview/tab-keyboard-behavior-3.png)

### <a name="selecting-a-tab"></a>Выбор вкладки

Если фокус находится на элементе TabViewItem, то нажатие клавиши ПРОБЕЛ или ВВОД позволит выбрать этот элемент TabViewItem.

Используйте клавиши со стрелками для перемещения фокуса, а затем нажмите клавишу ПРОБЕЛ, чтобы выбрать вкладку.

![Нажатие клавиши ПРОБЕЛ для выбора вкладки](images/tabview/tab-keyboard-behavior-2.png)

### <a name="shortcuts-for-selecting-adjacent-tabs"></a>Сочетания клавиш для выбора соседних вкладок

Нажатие клавиш CTRL+TAB позволит выбрать следующий элемент [TabViewItem](/uwp/api/microsoft.ui.xaml.controls.tabviewitem). Нажатие клавиш CTRL+SHIFT+TAB позволит выбрать предыдущий элемент TabViewItem. Для этих целей список вкладок "зациклен", поэтому при выборе следующей вкладки с выбранной последней вкладкой будет выбрана первая вкладка.

### <a name="closing-a-tab"></a>Закрытие вкладки

Нажатие клавиш CTRL+F4 вызовет событие [TabCloseRequested](/uwp/api/microsoft.ui.xaml.controls.tabview.tabcloserequested). Обработайте это событие и закройте вкладку, если это возможно.

### <a name="keyboard-guidance-for-app-developers"></a>Рекомендации по использованию клавиатуры для разработчиков приложений

Некоторые приложения могут потребовать более расширенного управления с клавиатуры. Рассмотрите возможность реализации следующих сочетаний клавиш, если они подходят для вашего приложения.

> [!WARNING]
> Если вы добавляете [TabView](/uwp/api/microsoft.ui.xaml.controls.tabview) в существующее приложение, возможно, вы уже создали сочетания клавиш, которые соответствуют рекомендуемым сочетаниям клавиш TabView. В этом случае необходимо решить, следует ли использовать существующие сочетания клавиш или предложить пользователю интуитивно понятный интерфейс для работы с вкладками.

- Нажатие клавиш CTRL+T должно открывать новую вкладку. Обычно эта вкладка заполняется предопределенным документом или создается пустой с простым способом выбора содержимого. Если пользователь должен выбрать содержимое для новой вкладки, рассмотрите возможность переноса фокуса ввода в элемент управления выбором содержимого.
- Нажатие клавиш CTRL+W должно закрывать выбранную вкладку. Помните, что TabView автоматически выберет следующую вкладку.
- Нажатие клавиш CTRL+SHIFT+T должно открывать недавно закрытые вкладки (или точнее, открывать новые вкладки с тем же содержимым, что и на недавно закрытых вкладках). Начните с последней закрытой вкладки и возвращайтесь назад по времени при каждом следующем нажатии этого сочетания клавиш. Обратите внимание на то, что для этого потребуется хранение списка недавно закрытых вкладок.
- Нажатие клавиш CTRL+1 должно выбирать первую вкладку в списке вкладок. Аналогичным образом, нажатие клавиш CTRL+2 должно выбирать вторую вкладку, CTRL+3 — третью, и так далее до CTRL+8.
- Нажатие клавиш CTRL+9 должно выбирать последнюю вкладку в списке вкладок независимо от их количества в списке.
- Если вкладки поддерживают не только команду закрытия (а, например, дублирование или закрепление вкладки), используйте контекстное меню для отображения всех доступных действий, которые можно выполнить с вкладкой.

### <a name="implement-browser-style-keyboarding-behavior"></a>Реализация поведения клавиатуры, аналогичного браузеру

В этом примере реализуется ряд описанных выше рекомендаций для [TabView](/uwp/api/microsoft.ui.xaml.controls.tabview). В частности, в этом примере реализуются сочетание клавиш CTRL+T, CTRL+W, CTRL+1…8 и CTRL+9.

```xaml
<controls:TabView x:Name="TabRoot">
    <controls:TabView.KeyboardAccelerators>
        <KeyboardAccelerator Key="T" Modifiers="Control" Invoked="NewTabKeyboardAccelerator_Invoked" />
        <KeyboardAccelerator Key="W" Modifiers="Control" Invoked="CloseSelectedTabKeyboardAccelerator_Invoked" />
        <KeyboardAccelerator Key="Number1" Modifiers="Control" Invoked="NavigateToNumberedTabKeyboardAccelerator_Invoked" />
        <KeyboardAccelerator Key="Number2" Modifiers="Control" Invoked="NavigateToNumberedTabKeyboardAccelerator_Invoked" />
        <KeyboardAccelerator Key="Number3" Modifiers="Control" Invoked="NavigateToNumberedTabKeyboardAccelerator_Invoked" />
        <KeyboardAccelerator Key="Number4" Modifiers="Control" Invoked="NavigateToNumberedTabKeyboardAccelerator_Invoked" />
        <KeyboardAccelerator Key="Number5" Modifiers="Control" Invoked="NavigateToNumberedTabKeyboardAccelerator_Invoked" />
        <KeyboardAccelerator Key="Number6" Modifiers="Control" Invoked="NavigateToNumberedTabKeyboardAccelerator_Invoked" />
        <KeyboardAccelerator Key="Number7" Modifiers="Control" Invoked="NavigateToNumberedTabKeyboardAccelerator_Invoked" />
        <KeyboardAccelerator Key="Number8" Modifiers="Control" Invoked="NavigateToNumberedTabKeyboardAccelerator_Invoked" />
        <KeyboardAccelerator Key="Number9" Modifiers="Control" Invoked="NavigateToNumberedTabKeyboardAccelerator_Invoked" />
    </controls:TabView.KeyboardAccelerators>
    <!-- ... some tabs ... -->
</controls:TabView>
```

```csharp
private void NewTabKeyboardAccelerator_Invoked(KeyboardAccelerator sender, KeyboardAcceleratorInvokedEventArgs args)
{
    // See previous sample
    CreateNewTab();
}

private void CloseSelectedTabKeyboardAccelerator_Invoked(KeyboardAccelerator sender, KeyboardAcceleratorInvokedEventArgs args)
{
    // Only close the selected tab if it is closeable
    if (((TabViewItem)TabRoot.SelectedItem).IsCloseable)
    {
        TabRoot.TabItems.Remove(TabRoot.SelectedItem);
    }
}

private void NavigateToNumberedTabKeyboardAccelerator_Invoked(KeyboardAccelerator sender, KeyboardAcceleratorInvokedEventArgs args)
{
    int tabToSelect = 0;

    switch (sender.Key)
    {
        case Windows.System.VirtualKey.Number1:
            tabToSelect = 0;
            break;
        case Windows.System.VirtualKey.Number2:
            tabToSelect = 1;
            break;
        case Windows.System.VirtualKey.Number3:
            tabToSelect = 2;
            break;
        case Windows.System.VirtualKey.Number4:
            tabToSelect = 3;
            break;
        case Windows.System.VirtualKey.Number5:
            tabToSelect = 4;
            break;
        case Windows.System.VirtualKey.Number6:
            tabToSelect = 5;
            break;
        case Windows.System.VirtualKey.Number7:
            tabToSelect = 6;
            break;
        case Windows.System.VirtualKey.Number8:
            tabToSelect = 7;
            break;
        case Windows.System.VirtualKey.Number9:
            // Select the last tab
            tabToSelect = TabRoot.TabItems.Count - 1;
            break;
    }

    // Only select the tab if it is in the list
    if (tabToSelect < TabRoot.TabItems.Count)
    {
        TabRoot.SelectedIndex = tabToSelect;
    }
}
```

## <a name="related-articles"></a>Похожие статьи

- [MasterDetails](https://docs.microsoft.com/windows/uwp/design/controls-and-patterns/master-details)
- [NavigationView](https://docs.microsoft.com/windows/uwp/design/controls-and-patterns/navigationview)
- [Сводка](https://docs.microsoft.com/windows/uwp/design/controls-and-patterns/pivot)

---
Description: Панели команд предоставляют пользователям удобный доступ к самым распространенным задачам приложения.
title: Панель команд
label: App bars/command bars
template: detail.hbs
op-migration-status: ready
ms.date: 05/19/2017
ms.topic: article
keywords: windows 10, uwp
ms.assetid: 868b4145-319b-4a97-82bd-c98d966144db
pm-contact: yulikl
design-contact: ksulliv
dev-contact: niallm
doc-status: Published
ms.localizationpriority: medium
ms.openlocfilehash: c32b60b3d9e717a916b5424f3b8bd78102439f30
ms.sourcegitcommit: af4050f69168c15b0afaaa8eea66a5ee38b88fed
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/21/2020
ms.locfileid: "80081969"
---
# <a name="command-bar"></a>Панель команд

Панели команд предоставляют пользователям удобный доступ к самым распространенным задачам приложения. Панели команд могут предоставлять доступ к командам приложения или страниц, работая с любым шаблоном навигации.

> **API платформы:** [класс CommandBar](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.commandbar), [класс AppBarButton](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.appbarbutton), [класс AppBarToggleButton](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.appbartogglebutton), [класс AppBarSeparator](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.appbarseparator)

![Пример панели команд со значками](images/controls_appbar_icons.png)

## <a name="is-this-the-right-control"></a>Выбор правильного элемента управления

CommandBar — это гибкий облегченный элемент управления общего назначения, способный отображать как сложное содержимое, такое как изображения и текстовые блоки, так и простые команды, такие как элементы управления [AppBarButton](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.appbarbutton), [AppBarToggleButton](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.appbartogglebutton) и [AppBarSeparator](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.appbarseparator).

> [!NOTE]
> XAML одновременно поддерживает элементы управления [AppBar](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.appbar) и [CommandBar](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.commandbar). Элемент AppBar следует использовать, только если вы выполняете обновление универсального приложения для Windows 8, использующего AppBar, и хотите свести изменения к минимуму. Для новых приложений Windows 10 рекомендуется использовать элемент управления CommandBar. В этом документе предполагается, что вы используете элемент управления CommandBar.

## <a name="examples"></a>Примеры

<table>
<th align="left">XAML Controls Gallery<th>
<tr>
<td><img src="images/xaml-controls-gallery-app-icon-sm.png" alt="XAML controls gallery"></img></td>
<td>
    <p>Если у вас установлено приложение <strong style="font-weight: semi-bold">галереи элементов управления XAML</strong>, щелкните здесь, чтобы <a href="xamlcontrolsgallery:/item/CommandBar">открыть приложение и увидеть CommandBar в действии</a>.</p>
    <ul>
    <li><a href="https://www.microsoft.com/store/productId/9MSVH128X2ZT">Получить приложение XAML Controls Gallery (Microsoft Store)</a></li>
    <li><a href="https://github.com/Microsoft/Xaml-Controls-Gallery">Получить исходный код (GitHub)</a></li>
    </ul>
</td>
</tr>
</table>

Расширенная панель команд в приложении Фотографии (Майкрософт).

![Панель команд в приложении Фотографии (Майкрософт)](images/control-examples/command-bar-photos.png)

Панель команд в Календаре Outlook на Windows Phone.

![Панель команд в приложении Календарь Outlook.](images/control-examples/command-bar-calendar-phone.png)

## <a name="anatomy"></a>Структура

По умолчанию панель команд отображает строку кнопок со значками и необязательную кнопку "Дополнительно", на которой отображается символ трех точек \[•••\]. Так выглядит панель команд, созданная с помощью кода из примера, показанного ниже. Она отображается в закрытом компактном состоянии.

![Закрытая панель команд](images/command-bar-compact.png)

Панель команд также может отображаться в закрытом минимальном представлении, которое выглядит следующим образом. Дополнительную информацию можно найти в разделе [Открытое и закрытое состояние](#open-and-closed-states).

![Закрытая панель команд](images/command-bar-minimal.png)

Так выглядит эта же панель команд в открытом состоянии. Подписями обозначены основные компоненты элемента управления.

![Закрытая панель команд](images/commandbar_anatomy_open.png)

Панель команд разделена на четыре основных области:
- Область содержимого находится в левой части панели. Она отображается, если заполнено значение свойства [Content](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.contentcontrol.content).
- Область главных команд выровнена по правому краю панели. Она отображается, если заполнено значение свойства [PrimaryCommands](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.commandbar.primarycommands).  
- Кнопка "Дополнительно" \[•••\] отображается в правой части панели. Кнопка &quot;Дополнительно&quot; \[•••\] отображает метки основных команд и открывает меню переполнения, если присутствуют второстепенные команды. Эта кнопка не будет видна при отсутствии меток основных команд или второстепенных меток. Чтобы изменить это поведение по умолчанию, используйте свойство [OverflowButtonVisibility](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.commandbar.overflowbuttonvisibility).
- Меню переполнения отображается только в том случае, если открыта панель команд и заполнено значение свойства [SecondaryCommands](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.commandbar.secondarycommands). Основные команды будут перемещаться в область SecondaryCommands, если пространство ограничено. Чтобы изменить это поведение по умолчанию, используйте свойство [IsDynamicOverflowEnabled](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.commandbar.isdynamicoverflowenabled).

Элементы макета отображаются в обратном порядке, когда для свойства [FlowDirection](https://docs.microsoft.com/uwp/api/windows.ui.xaml.frameworkelement.flowdirection) указано значение **RightToLeft**.

## <a name="create-a-command-bar"></a>Создание панели команд
В этом примере создается панель команд, показанная ранее.

```xaml
<CommandBar>
    <AppBarToggleButton Icon="Shuffle" Label="Shuffle" Click="AppBarButton_Click" />
    <AppBarToggleButton Icon="RepeatAll" Label="Repeat" Click="AppBarButton_Click"/>
    <AppBarSeparator/>
    <AppBarButton Icon="Back" Label="Back" Click="AppBarButton_Click"/>
    <AppBarButton Icon="Stop" Label="Stop" Click="AppBarButton_Click"/>
    <AppBarButton Icon="Play" Label="Play" Click="AppBarButton_Click"/>
    <AppBarButton Icon="Forward" Label="Forward" Click="AppBarButton_Click"/>

    <CommandBar.SecondaryCommands>
        <AppBarButton Label="Like" Click="AppBarButton_Click"/>
        <AppBarButton Label="Dislike" Click="AppBarButton_Click"/>
    </CommandBar.SecondaryCommands>

    <CommandBar.Content>
        <TextBlock Text="Now playing..." Margin="12,14"/>
    </CommandBar.Content>
</CommandBar>
```

## <a name="commands-and-content"></a>Команды и содержимое
Элемент управления CommandBar имеет три свойства, которые можно использовать для добавления команд и содержимого: [PrimaryCommands](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.commandbar.primarycommands), [SecondaryCommands](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.commandbar.secondarycommands) и [Content](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.contentcontrol.content).


### <a name="commands"></a>Команды

По умолчанию элементы панели команд добавляются в коллекцию **PrimaryCommands**. Команды следует добавлять в порядке их важности, чтобы наиболее важные команды всегда были видимы. При изменении ширины панели команд, например, когда пользователь изменяет размер окна приложения, основные команды динамически перемещаются между панелью команд и меню переполнения в точках разрыва. Чтобы изменить это поведение по умолчанию, используйте свойство [IsDynamicOverflowEnabled](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.commandbar.isdynamicoverflowenabled). 

На самых маленьких экранах (шириной в 320 эффективных пикселей) на панели команд умещаются не более 4 основных команд. 

Вы можете также добавлять команды в коллекцию **SecondaryCommands**, и эти элементы будут отображаться в меню переполнения.

![Пример панели команд с областью "Дополнительно" и значками](images/appbar_rs2_overflow_icons.png)

При необходимости можно перемещать команды между областями PrimaryCommands и SecondaryCommands программным путем.

- *Если существует команда, которая будет доступна на всех страницах, лучше размещать ее в одном и том же месте.*
- *Мы рекомендуем располагать команды "Принять", "Да" и "ОК" слева от команд "Отклонить", "Нет" и "Отмена". Стабильность позволяет пользователям более уверенно перемещаться по системе и применять полученный опыт навигации в разных приложениях.*

### <a name="app-bar-buttons"></a>Кнопки панели приложения

Свойствам PrimaryCommands и SecondaryCommands можно присвоить только значения [AppBarButton](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.AppBarButton), [AppBarToggleButton](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.AppBarToggleButton) и [AppBarSeparator](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.AppBarSeparator), соответствующие командным элементам. 

Кнопки панели приложения характеризуются значком и текстовой меткой. Эти элементы управления оптимизированы для использования на панели команд и меняют свой вид в зависимости от того, используется ли элемент управления на панели команд или в меню переполнения.

Размер значков в меню переполнения — 16x16 пикселей, что меньше, чем значки в области основных команд (20х20 пикселей). При использовании SymbolIcon, FontIcon или PathIcon, когда команда переходит в область второстепенной команды, значок автоматически масштабируется до правильного размера без потери качества. 

### <a name="button-labels"></a>Метки кнопок
Свойство [IsCompact](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.appbarbutton.IsCompact) элемента AppBarButton определяет, будет ли отображаться метка. В элементе управления CommandBar панель команд автоматически перезаписывает свойство IsCompact кнопки при открытии и закрытии панели команд.

Для размещения подписей кнопок панели приложения используйте свойство [DefaultLabelPosition](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.commandbar.defaultlabelposition) элемента управления CommandBar.

```xaml
<CommandBar DefaultLabelPosition="Right">
    <AppBarToggleButton Icon="Shuffle" Label="Shuffle"/>
    <AppBarToggleButton Icon="RepeatAll" Label="Repeat"/>
</CommandBar>
```

![Панель команд с подписями справа](images/app-bar-labels-on-right.png)

В больших окнах попробуйте переместить метки вправо от значков кнопок на панели приложения, чтобы улучшить восприятие. Подписи в нижней части требуют от пользователя открытия панели команд для их отображения, тогда как при расположении справа подписи видны, даже если панель команд закрыта.

В меню переполнения метки по умолчанию размещаются справа от значков, а **LabelPosition** игнорируется. Вы можете настроить оформление, задав для свойства [CommandBarOverflowPresenterStyle](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.CommandBar.CommandBarOverflowPresenterStyle) значение Style, которое указывает на [CommandBarOverflowPresenter](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.commandbaroverflowpresenter). 

Метки кнопок должны быть короткими; желательно не более одного слова. Длинные метки, расположенные под значком, будут переноситься на несколько строк, увеличивая общую высоту открытой панели команд. Вы можете включить в текст метки символ мягкого переноса (0x00AD), чтобы обозначить границу знака, после которой должен происходить разрыв слова. В XAML это выражается с помощью escape-последовательности следующим образом:

```xaml
<AppBarButton Icon="Back" Label="Areally&#x00AD;longlabel"/>
```

При переносе текста метки в указанном месте она выглядит следующим образом.

![Кнопка панели приложения с перенесенным текстом метки](images/app-bar-button-label-wrap.png)

### <a name="command-bar-flyouts"></a>Всплывающие элементы панели команд

Используйте логическую группировку команд. Например, поместите команды "Ответить", "Ответить всем" и "Переслать" в меню "Ответ". Хотя обычно кнопка панели приложения активирует одну команду, ее можно использовать для отображения элемента [MenuFlyout](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.menuflyout) или [Flyout](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.flyout) с пользовательским содержимым.

![Пример всплывающих элементов на панели команд](images/AppbarGuidelines_Flyouts.png)

### <a name="other-content"></a>Другое содержимое

Вы можете добавить в область содержимого любые элементы XAML, установив свойство **Content**. Если необходимо добавить более одного элемента, вам понадобится разместить элементы в контейнере панели и сделать панель единственным дочерним элементом свойства Content.

Содержимое не обрезается, если включено динамическое переполнение, поскольку основные команды перемещаются в меню переполнения. В противном случае основные команды имеют приоритет и это может привести к обрезке содержимого.

Если для свойства [ClosedDisplayMode](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.appbar.closeddisplaymode) установлено значение **Compact**, содержимое может обрезаться, при превышении компактного размера панели команд. Вы должны обрабатывать события [Opening](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.appbar.opening) и [Closed](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.appbar.closed), чтобы отображать и скрывать части пользовательского интерфейса в области содержимого так, чтобы они не обрезались. Дополнительную информацию можно найти в разделе [Открытое и закрытое состояние](#open-and-closed-states).


## <a name="open-and-closed-states"></a>Открытое и закрытое состояние

Панель команд может быть открыта или закрыта. Когда она открыта, здесь отображаются основные кнопки с текстовыми метками, а также она открывает меню переполнения (если имеются дополнительные команды).
В командной строке меню переполнения открывается вверх (выше основных команд) или вниз (ниже основных команд). По умолчанию используется направление вверх, но если для такого открытия меню переполнения места недостаточно, оно открывается вниз. 

Пользователь может переключиться между этими состояниями, выбрав кнопку "Дополнительно" \[•••\]. Вы можете переключаться между ними программным способом, используя свойство [IsOpen](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.appbar.isopen). 

События [Opening](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.appbar.opening), [Opened](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.appbar.opened), [Closing](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.appbar.closing) и [Closed](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.appbar.closed) позволяют реагировать на открытие и закрытие панели команд.  
- События Opening и Closing возникают до начала анимации перехода.
- События Opened и Closed возникают после завершения перехода.

В этом примере событий Opening и Closing используются для изменения уровня прозрачности панели команд. Если панель команд закрыта, она становится полупрозрачной, чтобы был виден фон приложения. Если панель команд открыта, она становится непрозрачной, чтобы сосредоточить внимание пользователя на командах.

```xaml
<CommandBar Opening="CommandBar_Opening"
            Closing="CommandBar_Closing">
    <AppBarButton Icon="Accept" Label="Accept"/>
    <AppBarButton Icon="Edit" Label="Edit"/>
    <AppBarButton Icon="Save" Label="Save"/>
    <AppBarButton Icon="Cancel" Label="Cancel"/>
</CommandBar>
```

```csharp
private void CommandBar_Opening(object sender, object e)
{
    CommandBar cb = sender as CommandBar;
    if (cb != null) cb.Background.Opacity = 1.0;
}

private void CommandBar_Closing(object sender, object e)
{
    CommandBar cb = sender as CommandBar;
    if (cb != null) cb.Background.Opacity = 0.5;
}

```

### <a name="issticky"></a>IsSticky

Если пользователь взаимодействует с другими частями приложения, когда открыта панель команд, она автоматически закрывается. Это поведение называется *исчезновением*. Вы можете управлять исчезновением, установив свойство [IsSticky](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.appbar.issticky). Если выбран параметр `IsSticky="true"`, панель остается открытой, пока пользователь не нажмет кнопку &quot;Дополнительно&quot; \[•••\] или не выберет элемент в меню переполнения. 

Мы рекомендуем избегать закрепленных панелей команд, так как они не соответствуют ожиданиям пользователей по [поведению исчезновения и фокуса ввода с клавиатуры](https://docs.microsoft.com/windows/uwp/design/controls-and-patterns/menus#light-dismiss).

### <a name="display-mode"></a>Режим отображения

Вы можете управлять отображением панели в закрытом состоянии, установив свойство [ClosedDisplayMode](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.appbar.closeddisplaymode). Вы можете выбрать один из трех режимов отображения:
- **Компактный**. Режим по умолчанию. Отображает содержимое, значки основных команд без меток и кнопку "Дополнительно" \[•••\].
- **Минимальный**. Отображает только тонкую панель, которая целиком действует как кнопка "Дополнительно" \[•••\]. Пользователь может открыть панель нажатием в любом месте панели.
- **Скрытый**. Панель команд в закрытом состоянии не отображается. Это может быть полезно для отображения контекстно-зависимых команд с помощью встроенной панели команд. В этом случае следует открыть панель команд программным способом, установив свойство **IsOpen** или изменив значение свойства ClosedDisplayMode на **Minimal** или **Compact**.

В этом примере панель команд используется для отображения простых команд форматирования для [RichEditBox](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.RichEditBox). Когда поле ввода не имеет фокуса, команды форматирования могут отвлекать, поэтому они скрыты. При использовании поля ввода значение свойства панели команд ClosedDisplayMode меняется на Compact, чтобы отобразить команды форматирования.

```xaml
<StackPanel Width="300"
            GotFocus="EditStackPanel_GotFocus"
            LostFocus="EditStackPanel_LostFocus">
    <CommandBar x:Name="FormattingCommandBar" ClosedDisplayMode="Hidden">
        <AppBarButton Icon="Bold" Label="Bold" ToolTipService.ToolTip="Bold"/>
        <AppBarButton Icon="Italic" Label="Italic" ToolTipService.ToolTip="Italic"/>
        <AppBarButton Icon="Underline" Label="Underline" ToolTipService.ToolTip="Underline"/>
    </CommandBar>
    <RichEditBox Height="200"/>
</StackPanel>
```

```csharp
private void EditStackPanel_GotFocus(object sender, RoutedEventArgs e)
{
    FormattingCommandBar.ClosedDisplayMode = AppBarClosedDisplayMode.Compact;
}

private void EditStackPanel_LostFocus(object sender, RoutedEventArgs e)
{
    FormattingCommandBar.ClosedDisplayMode = AppBarClosedDisplayMode.Hidden;
}
```

>**Примечание.** &nbsp;&nbsp;В нашем примере не рассматривается реализация команд редактирования. Дополнительные сведения см. в статье [RichEditBox](rich-edit-box.md).

Несмотря на то, что режимы Minimal и Hidden могут быть полезны в некоторых ситуациях, необходимо помнить о том, что сокрытие всех действий может поставить пользователя в замешательство.

Изменение значения ClosedDisplayMode для предоставления пользователю подсказки влияет на расположение окружающих элементов. Смена состояния панели команд с закрытого на открытое и обратно не влияет на расположение других элементов.

## <a name="placement"></a>Размещение
Панели команд можно размещать в верхней части окна приложения, в нижней части окна приложения и в строках.

![Пример 1 размещения панели приложения](images/AppbarGuidelines_Placement1.png)

-   Для небольших наладонных устройств мы рекомендуем размещать панели команд в нижней части экрана для удобства доступа.
-   На устройствах с более крупными экранами панели команд следует размещать ближе к верхней части окна, чтобы сделать их заметнее.

Используйте API [DiagonalSizeInInches](https://docs.microsoft.com/uwp/api/windows.graphics.display.displayinformation.diagonalsizeininches) для определения физических размеров экрана.

Панели команд можно размещать в следующих областях экранов с одним представлением (пример слева) и с несколькими представлениями (пример справа). Встроенные панели команд можно размещать в любом месте пространства действий.

![Пример 2 размещения панели приложения](images/AppbarGuidelines_Placement2.png)

>**Сенсорные устройства**. Если панель команд должна оставаться видимой пользователю при появлении сенсорной клавиатуры или панели функционального ввода (SIP), вы можете присвоить панель команд в качестве значения свойству [BottomAppBar](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.page.bottomappbar) объекта Page, и тогда панель будет перемещаться и сохранять видимость при появлении SIP. В противном случае следует встроить панель команд и позиционировать ее относительно содержимого приложения.

## <a name="get-the-sample-code"></a>Получение примера кода

- [Пример из коллекции элементов управления XAML](https://github.com/Microsoft/Xaml-Controls-Gallery) — ознакомьтесь со всеми элементами управления XAML в интерактивном режиме.
- [Пример команд XAML](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/XamlCommanding)

## <a name="related-articles"></a>Похожие статьи

* [Основы проектирования команд в приложениях UWP](../basics/commanding-basics.md)
* [CommandBar class](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.CommandBar) (Класс CommandBar)

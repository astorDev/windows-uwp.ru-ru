---
author: Karl-Bridge-Microsoft
Description: Learn how accelerator keys can improve the usability and accessibility of UWP apps.
title: Ускорители клавиатуры
label: Keyboard accelerators
template: detail.hbs
keywords: клавиатура, ускоритель, клавиша вызова, сочетания клавиш, специальные возможности, навигация, фокус, текст, ввод, взаимодействие с пользователем, геймпад, пульт управления
ms.author: kbridge
ms.date: 10/10/2017
ms.topic: article
ms.prod: windows
ms.technology: uwp
pm-contact: chigy
design-contact: miguelrb
doc-status: Draft
ms.localizationpriority: medium
ms.openlocfilehash: 8b4693c4ed6c02db9e4fe3f5f7fee6fe569c0e79
ms.sourcegitcommit: 8e30651fd691378455ea1a57da10b2e4f50e66a0
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/10/2018
ms.locfileid: "4500538"
---
# <a name="keyboard-accelerators"></a>Ускорители клавиатуры

![Клавиатура Surface](images/accelerators/accelerators_hero2.png)

Клавиши вызова (или ускорители клавиатуры) — это сочетания клавиш, которые повышают удобство использования и расширяют специальные возможности Windows-приложений, предоставляя пользователям интуитивно понятный способ выполнения распространенных действий или команд без необходимости навигации по пользовательскому интерфейсу приложения.

Подробные сведения о навигации по пользовательскому интерфейсу приложения для Windows с помощью сочетаний клавиш см. в разделе [Клавиши доступа](access-keys.md).

> [!NOTE]
> Клавиатура незаменима для пользователей с определенными ограниченными способностями (см. [Специальные возможности клавиатуры](https://docs.microsoft.com/windows/uwp/accessibility/keyboard-accessibility)), а также является важным средством для тех, кто считает ее более эффективным способом для взаимодействия с приложением.

## <a name="overview"></a>Обзор

Как правило, ускорители включают в себя функциональные клавиши F1–F12 или одно из сочетаний обычной клавиши с одной или несколькими клавишами-модификаторами (CTRL, SHIFT).

> [!NOTE]
> Элементы управления платформы UWP оснащены встроенными ускорителями клавиатуры. Например, ListView поддерживает сочетание клавиш CTRL+A для выбора всех элементов в списке, а RichEditBox поддерживает сочетание клавиш CTRL+TAB для вставки табуляции в текстовое поле. Эти встроенные ускорители клавиатуры называются **ускорителями элементов управления** и выполняются только в том случае, если фокус находится на элементе или одном из его дочерних элементов. Описываемые здесь ускорители, определенные пользователем с помощью API-интерфейсов ускорителей клавиатуры, называются **ускорителями приложений**.

Ускорители клавиатуры доступны не для всех действий, но часто связаны с командами в меню (и должны указываться в содержимом пункта меню). Также ускорители могут быть связаны с действиями, у которых нет эквивалентных пунктов меню. Тем не менее, пользователи находят и изучают доступный набор команд с помощью меню приложения, поэтому следует максимально упростить процесс обнаружения ускорителей (например, с помощью меток или проверенных шаблонов).

![Описание ускорителей клавиатуры в метке пункта меню](images/accelerators/accelerators_menuitemlabel.png)  
*Описание ускорителей клавиатуры в метке пункта меню*

## <a name="when-to-use-keyboard-accelerators"></a>Сценарии использования ускорителей клавиатуры

Рекомендуется указывать ускорители клавиатуры всегда, когда это уместно в пользовательском интерфейсе, и предусматривать поддержку ускорителей во всех пользовательских элементах управления.

- За счет ускорителей клавиатуры улучшаются специальные возможности приложений для пользователей с нарушениями двигательных функций, в том числе таких пользователей, которые могут нажимать только одну клавишу за один раз или испытывают трудности при работе с мышью**.

  Правильно разработанный пользовательский интерфейс клавиатуры является важным аспектом доступности программы. Он позволяет людям с ослабленным зрением или с определенными нарушениями двигательных способностей осуществлять переходы в приложении и взаимодействовать с его компонентами. Такие пользователи могут не иметь возможности использовать мышь. Вместо этого они могут рассчитывать на различные специальные возможности, такие как средства расширения функций клавиатуры, экранные клавиатуры, экранные лупы, средства чтения с экрана и средства голосового управления. Для этих пользователей крайне важно иметь доступ ко всем командам.

- Ускорители клавиатуры делают приложение более удобным для опытных пользователей, предпочитающих взаимодействовать с помощью клавиатуры.

  Опытные пользователи часто применяют клавиатуру, так как с ее помощью можно быстрее вводить команды, при этом не требуется убирать руки с клавиатуры. Для таких пользователей эффективность и согласованность критически важна. Полнота важна только для наиболее часто используемых команд.

## <a name="specify-a-keyboard-accelerator"></a>Указание ускорителя клавиатуры

Используйте API-интерфейсы [KeyboardAccelerator](https://docs.microsoft.com/en-us/uwp/api/windows.ui.xaml.input.keyboardaccelerator.-ctor) для создания ускорителей клавиатуры в приложениях UWP. Благодаря этим API-интерфейсам отпадает необходимость обработки нескольких событий KeyDown для обнаружения нажатого сочетания клавиш, а также появляется возможность локализовать ускорители в ресурсах приложения.

Рекомендуется установить ускорители клавиатуры для наиболее распространенных действий в приложении и задокументировать их с помощью метки или подсказки пункта меню. В этом примере мы объявляем ускорители клавиатуры только для команд "Переименовать" и "Копировать".

``` xaml
<CommandBar Margin="0,200" AccessKey="M">
  <AppBarButton 
    Icon="Share" 
    Label="Share" 
    Click="OnShare" 
    AccessKey="S" />
  <AppBarButton 
    Icon="Copy" 
    Label="Copy" 
    ToolTipService.ToolTip="Copy (Ctrl+C)" 
    Click="OnCopy" 
    AccessKey="C">
    <AppBarButton.KeyboardAccelerators>
      <KeyboardAccelerator 
        Modifiers="Control" 
        Key="C" />
    </AppBarButton.KeyboardAccelerators>
  </AppBarButton>

  <AppBarButton 
    Icon="Delete" 
    Label="Delete" 
    Click="OnDelete" 
    AccessKey="D" />
  <AppBarSeparator/>
  <AppBarButton 
    Icon="Rename" 
    Label="Rename" 
    ToolTipService.ToolTip="Rename (F2)" 
    Click="OnRename" 
    AccessKey="R">
    <AppBarButton.KeyboardAccelerators>
      <KeyboardAccelerator 
        Modifiers="None" Key="F2" />
    </AppBarButton.KeyboardAccelerators>
  </AppBarButton>

  <AppBarButton 
    Icon="SelectAll" 
    Label="Select" 
    Click="OnSelect" 
    AccessKey="A" />
  
  <CommandBar.SecondaryCommands>
    <AppBarButton 
      Icon="OpenWith" 
      Label="Sources" 
      AccessKey="S">
      <AppBarButton.Flyout>
        <MenuFlyout>
          <ToggleMenuFlyoutItem Text="OneDrive" />
          <ToggleMenuFlyoutItem Text="Contacts" />
          <ToggleMenuFlyoutItem Text="Photos"/>
          <ToggleMenuFlyoutItem Text="Videos"/>
        </MenuFlyout>
      </AppBarButton.Flyout>
    </AppBarButton>
    <AppBarToggleButton 
      Icon="Save" 
      Label="Auto Save" 
      IsChecked="True" 
      AccessKey="A"/>
  </CommandBar.SecondaryCommands>

</CommandBar>
```

![Описание ускорителя клавиатуры в подсказке](images/accelerators/accelerators_tooltip.png)  
***Описание ускорителя клавиатуры в подсказке***

У объекта [UIElement](https://docs.microsoft.com/uwp/api/windows.ui.xaml.uielement) есть коллекция [KeyboardAccelerator](https://docs.microsoft.com/uwp/api/windows.ui.xaml.input.keyboardaccelerator), [KeyboardAccelerators](https://docs.microsoft.com/uwp/api/windows.ui.xaml.uielement.KeyboardAccelerators), в которой указываются пользовательские объекты KeyboardAccelerator и определяются нажатия клавиш для ускорителя клавиатуры:

-   **[Key](https://docs.microsoft.com/uwp/api/windows.ui.xaml.input.keyboardaccelerator.Key)**— перечисление [VirtualKey](https://docs.microsoft.com/uwp/api/windows.system.virtualkey), используемое для ускорителя клавиатуры.

-   **[Modifiers](https://docs.microsoft.com/uwp/api/windows.ui.xaml.input.keyboardaccelerator.Modifiers)**— перечисление [VirtualKeyModifiers](https://docs.microsoft.com/uwp/api/windows.system.virtualkeymodifiers), используемое для ускорителя клавиатуры. Если свойству Modifiers не задано значение, используется значение по умолчанию None.

> [!NOTE]
> Поддерживаются ускорители, состоящие из одной клавиши (A, Delete, F2, ПРОБЕЛ, ESC, клавиша мультимедиа) и нескольких клавиш (CTRL+SHIFT+M). Тем не менее, виртуальные клавиши геймпада не поддерживаются.

## <a name="scoped-accelerators"></a>Ускорители с областью действия

Некоторые ускорители работают только в определенных областях, в то время как другие можно использовать в любой части приложения.

Например, Microsoft Outlook включает в себя следующие ускорители:
-   Сочетания клавиш CTRL+B, CTRL+I и ESC работают только в области формы отправки сообщения электронной почты
-   Сочетания клавиш CTRL+1 и CTRL+2 работают в любой части приложения

### <a name="context-menus"></a>Контекстные меню

Действия в контекстных меню влияют только на определенные области или элементы, такие как выбранные символы в текстовом редакторе или песня в списке воспроизведения. По этой причине рекомендуется в качестве области действия ускорителей клавиатуры для пунктов контекстного меню использовать его родительский элемент.

Используйте свойство [ScopeOwner](https://docs.microsoft.com/uwp/api/windows.ui.xaml.input.keyboardaccelerator.ScopeOwner), чтобы указать область действия ускорителя клавиатуры. В этом коде демонстрируется, как реализовать контекстное меню в элементе ListView с использованием ускорителей клавиатуры с областью действия:

``` xaml
<ListView x:Name="MyList">
  <ListView.ContextFlyout>
    <MenuFlyout>
      <MenuFlyoutItem Text="Share" Icon="Share"/>
      <MenuFlyoutItem Text="Copy" Icon="Copy">
        <MenuFlyoutItem.KeyboardAccelerators>
          <KeyboardAccelerator 
            Modifiers="Control" 
            Key="C" 
            ScopeOwner="{x:Bind MyList }" />
        </MenuFlyoutItem.KeyboardAccelerators>
      </MenuFlyoutItem>
      
      <MenuFlyoutItem Text="Delete" Icon="Delete" />
      <MenuFlyoutSeparator />
      
      <MenuFlyoutItem Text="Rename">
        <MenuFlyoutItem.KeyboardAccelerators>
          <KeyboardAccelerator 
            Modifiers="None" 
            Key="F2" 
            ScopeOwner="{x:Bind MyList}" />
        </MenuFlyoutItem.KeyboardAccelerators>
      </MenuFlyoutItem>
      
      <MenuFlyoutItem Text="Select" />
    </MenuFlyout>
    
  </ListView.ContextFlyout>
    
  <ListViewItem>Track 1</ListViewItem>
  <ListViewItem>Alternative Track 1</ListViewItem>

</ListView>
```

Атрибут ScopeOwner элемента MenuFlyoutItem.KeyboardAccelerators отмечает, что ускоритель имеет заданную область действия и не является глобальным (по умолчанию используется значение null, то есть ускоритель является глобальным). Дополнительные сведения см. в разделе **Разрешение ускорителей** в этой статье.

## <a name="invoke-a-keyboard-accelerator"></a>Вызов ускорителя клавиатуры 

Объект [KeyboardAccelerator](https://docs.microsoft.com/uwp/api/windows.ui.xaml.input.keyboardaccelerator) использует [шаблон элемента управления на основе модели автоматизации пользовательского интерфейса (UIA)](https://msdn.microsoft.com/library/windows/desktop/ee671194(v=vs.85).aspx) для выполнения действия при вызове ускорителя.

Элементы управления на основе модели автоматизации пользовательского интерфейса предоставляют общие возможности элементов управления. Например, элемент управления "Кнопка" реализовывает шаблон элемента управления [Вызов](https://msdn.microsoft.com/library/windows/desktop/ee671279(v=vs.85).aspx), чтобы обеспечить поддержку события "Щелчок" (как правило, элемент управления вызывается щелчком, двойным щелчком, нажатием клавиши ВВОД, предварительно заданным сочетанием клавиш или альтернативным сочетанием нажатий клавиш). Когда ускоритель клавиатуры используется для вызова элемента управления, платформа XAML проверяет, реализовывает ли этот элемент управления шаблон элемента управления "Вызов", и, если это так, активирует его (нет необходимости прослушивать событие KeyboardAcceleratorInvoked).

В следующем примере сочетание клавиш CTRL+S активирует событие "Щелчок", так как эта кнопка реализует шаблон "Вызов".

``` xaml 
<Button Content="Save" Click="OnSave">
  <Button.KeyboardAccelerators>
    <KeyboardAccelerator Key="S" Modifiers="Control" />
  </Button.KeyboardAccelerators>
</Button>
```

Если элемент реализует несколько шаблонов элемента управления, с помощью ускорителя можно активировать только один из них. Приоритет шаблонов элементов управления установлен в следующем порядке:
1.  Вызов (Кнопка)
2.  Переключение (Флажок)
3.  Выбор (ListView)
4.  Свертывание и развертывание (ComboBox) 

Если соответствий не обнаружено, ускоритель считается недействительным и отображается сообщение отладки ("Шаблоны автоматизации для этого компонента не найдены. Реализуйте все необходимое поведение в событии Invoked. Если присвоить свойству Handled значение true в обработчике событий, это сообщение будет отключено".)

## <a name="custom-keyboard-accelerator-behavior"></a>Поведение пользовательского ускорителя клавиатуры

Событие Invoked объекта [KeyboardAccelerator](https://docs.microsoft.com/uwp/api/windows.ui.xaml.input.keyboardaccelerator) активируется при выполнении ускорителя. Объект события [KeyboardAcceleratorInvokedEventArgs](https://docs.microsoft.com/uwp/api/windows.ui.xaml.input.keyboardacceleratorinvokedeventargs) включает в себя следующие свойства:
- **Handled** (Логическое). Если установить для этого свойства значение true, событие, активирующее шаблон элемента управления, не запускается, а восходящая маршрутизация события ускорителя останавливается. Значение по умолчанию — false.
- **Element** (DependencyObject). Объект, содержащий ускоритель.

Ниже демонстрируется, как определить коллекцию ускорителей клавиатуры и обработать событие Invoked.

``` xaml
<ListView x:Name="MyListView">
  <ListView.KeyboardAccelerators>
    <KeyboardAccelerator Key="A" Modifiers="Control,Shift" Invoked="SelectAllInvoked" />
    <KeyboardAccelerator Key="F5" Invoked="RefreshInvoked"  />
  </ListView.KeyboardAccelerators>
</ListView>   
```

``` csharp
void SelectAllInvoked (KeyboardAccelerator sender, KeyboardAcceleratorInvokedEventArgs args)
{
  CustomSelectAll(MyListView);
  args.Handled = true;
}

void RefreshInvoked(KeyboardAccelerator sender, KeyboardAcceleratorInvokedEventArgs args)
{
  Refresh(MyListView);
  args.Handled = true;
}
```

## <a name="override-default-keyboard-behavior"></a>Переопределение поведения клавиатуры по умолчанию

В некоторых случаях может потребоваться переопределить поведение по умолчанию для определенных клавиши, такие как клавиша Backspace или клавишу ВВОД. Например: 

## <a name="disable-a-keyboard-accelerator"></a>Отключение ускорителя клавиатуры 

Если элемент управления отключен, связанный ускоритель также отключается. В следующем примере свойству IsEnabled элемента ListView установлено значение, поэтому связанный ускоритель CTRL+A невозможно вызвать.

``` xaml
<ListView >
  <ListView.KeyboardAccelerators>
    <KeyboardAccelerator Key="A" 
      Modifiers="Control"
      Invoked="CustomListViewSelecAllInvoked" />
  </ListView.KeyboardAccelerators>
  
  <TextBox>
    <TextBox.KeyboardAccelerators>
      <KeyboardAccelerator 
        Key="A" 
        Modifiers="Control" 
        Invoked="CustomTextSelecAllInvoked" 
        IsEnabled="False" />
    </TextBox.KeyboardAccelerators>
  </TextBox>

<ListView>
```

Родительские и дочерние элементы управления используют один и тот же ускоритель. В этом случае родительский элемент управления можно вызвать, даже если фокус находится на дочернем элементе, а его ускоритель отключен.

## <a name="screen-readers-and-keyboard-accelerators"></a>Средства чтения с экрана и ускорители клавиатуры 

Средства чтения с экрана, такие как экранный диктор, могут озвучивать клавишу ускорителя клавиатуры для пользователя. По умолчанию озвучивается каждый модификатор (в порядке перечисления VirtualModifiers), за которым следует клавиша (с разделением знаками "+"). Можно настроить этот процесс с помощью присоединенного свойства AutomationProperties [AcceleratorKey](https://docs.microsoft.com/uwp/api/windows.ui.xaml.automation.automationproperties.AcceleratorKeyProperty). Если указано несколько ускорителей, озвучивается только первый.

В этом примере AutomationProperty.AcceleratorKey возвращает строку "CTRL+SHIFT+A":

``` xaml
<ListView x:Name="MyListView">
  <ListView.KeyboardAccelerators>

    <KeyboardAccelerator 
      Key="A" 
      Modifiers="Control,Shift" 
      Invoked="CustomSelectAllInvoked" />
      
    <KeyboardAccelerator 
      Key="F5" 
      Modifiers="None" 
      Invoked="RefreshInvoked" />

  </ListView.KeyboardAccelerators>

</ListView>   
```

> [!NOTE] 
> Настройка параметра AutomationProperties.AcceleratorKey не включает возможности клавиатуры, а лишь указывает платформе модели автоматизации пользовательского интерфейса, какие клавиши используются.

## <a name="common-keyboard-accelerators"></a>Распространенные ускорители клавиатуры

Мы рекомендуем использовать одинаковые ускорители клавиатуры в разных приложениях UWP. Пользователи запоминают ускорители клавиатуры и ожидают получать одни и те же (или похожие) результаты.

Это не всегда возможно из-за различий в возможностях приложений.

| **Редактирование** | **Распространенный ускоритель клавиатуры** |
| ------------- | ----------------------------------- |
| Открыть режим редактирования | CTRL+E |
| Выбрать все элементы в элементе управления или окне с фокусом | CTRL+A |
| Найти и заменить | CTRL+H |
| Отменить | CTRL+Z |
| Повторить | CTRL+Y |
| Удалить выделение и скопировать его в буфер обмена | CTRL+X |
| Скопировать выделение в буфер обмена | CTRL+C, CTRL+INSERT |
| Вставить содержимое буфера обмена | CTRL+V, SHIFT+INSERT |
| Вставить содержимое буфера обмена (с определенными параметрами) | CTRL+ALT+V |
| Переименовать элемент | F2 |
| Добавить новый элемент | CTRL+N |
| Добавить дополнительный элемент | CTRL+SHIFT+N |
| Удалить выбранный элемент (с возможностью отмены) | DEL, CTRL+D |
| Удалить выбранный элемент (без возможности отмены) | SHIFT+DEL |
| Полужирный | CTRL+B |
| Подчеркнутый | CTRL+U |
| Курсив | Ctrl+I |

| **Навигация** | |
| ------------- | ----------------------------------- |
| Найти содержимое в элементе управления или окне с фокусом | CTRL+F |
| Перейти к следующему результату поиска | F3 |

| **Другие действия** | |
| ------------- | ----------------------------------- |
| Добавить избранное | CTRL+D | 
| Обновить | F5 или CTRL+R | 
| Увеличить | CTRL++ | 
| Уменьшить | CTRL+- | 
| Масштаб по умолчанию | CTRL+0 | 
| Сохранить | CTRL+S | 
| Закрыть | CTRL+W | 
| Печать | CTRL+P | 

Обратите внимание, что некоторые сочетания недопустимы для локализованных версий Windows. Например, в версии Windows на испанском языке для выделения полужирным шрифтом вместо CTRL+B используется сочетание CTRL+N. Если приложение локализовано, мы рекомендуем предоставить локализованные ускорители клавиатуры.

## <a name="usability-affordances-for-keyboard-accelerators"></a>Повышение удобства использования ускорителей клавиатуры

### <a name="tooltips"></a>Подсказки

Так как обычно ускорители клавиатуры не описаны непосредственно в пользовательском интерфейсе приложения UWP, можно улучшить их обнаружение с помощью [подсказок](../controls-and-patterns/tooltips.md), которые автоматически появляются, когда пользователь перемещает фокус, нажимает и удерживает кнопку мыши или наводит указатель мыши на элемент управления. Всплывающая подсказка помогает определить, есть ли у элемента управления связанные с ним ускорители клавиатуры, и если они есть — указывает, какая для этого используется клавиша ускорителя клавиатуры.

**Windows 10, версия 1803 (обновление за апрель 2018 г.) и более поздних версий**

По умолчанию когда объявляются сочетания клавиш, все элементы управления (за исключением [MenuFlyoutItem](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.MenuFlyoutItem) и [ToggleMenuFlyoutItem](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.togglemenuflyoutitem)) предоставляют соответствующие сочетания клавиш в подсказке.

> [!NOTE] 
> Если для элемента управления определено несколько ускорителей, будет отображаться только первый.

![Подсказка сочетания клавиш](images/accelerators/accelerators_tooltip_savebutton_small.png)

*Комбинация сочетаний клавиш в подсказке*

Для [кнопки](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.button) [AppBarButton](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.appbarbutton)и [AppBarToggleButton](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.appbartogglebutton) объектов ускоритель клавиатуры добавляется к подсказки по умолчанию элемента управления. [MenuFlyoutItem](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.appbarbutton) и [ToggleMenuFlyoutItem](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.togglemenuflyoutitem)) объекты, ускоритель клавиатуры отображается текст всплывающий элемент.

> [!NOTE]
> Указание подсказки (см. раздел Button1 в следующем примере) переопределяет это поведение.

```xaml
<StackPanel x:Name="Container" Grid.Row="0" Background="AliceBlue">
    <Button Content="Button1" Margin="20"
            Click="OnSave" 
            KeyboardAcceleratorPlacementMode="Auto" 
            ToolTipService.ToolTip="Tooltip">
        <Button.KeyboardAccelerators>
            <KeyboardAccelerator  Key="A" Modifiers="Windows"/>
        </Button.KeyboardAccelerators>
    </Button>
    <Button Content="Button2"  Margin="20"
            Click="OnSave" 
            KeyboardAcceleratorPlacementMode="Auto">
        <Button.KeyboardAccelerators>
            <KeyboardAccelerator  Key="B" Modifiers="Windows"/>
        </Button.KeyboardAccelerators>
    </Button>
    <Button Content="Button3"  Margin="20"
            Click="OnSave" 
            KeyboardAcceleratorPlacementMode="Auto">
        <Button.KeyboardAccelerators>
            <KeyboardAccelerator  Key="C" Modifiers="Windows"/>
        </Button.KeyboardAccelerators>
    </Button>
</StackPanel>
```

![Подсказка сочетания клавиш](images/accelerators/accelerators-button-small.png)

*Сочетания клавиш, добавленная в подсказке по умолчанию кнопки*

```xaml
<AppBarButton Icon="Save" Label="Save">
    <AppBarButton.KeyboardAccelerators>
        <KeyboardAccelerator Key="S" Modifiers="Control"/>
    </AppBarButton.KeyboardAccelerators>
</AppBarButton>
```

![Подсказка сочетания клавиш](images/accelerators/accelerators-appbarbutton-small.png)

*Сочетания клавиш, добавленная для элемента AppBarButton подсказки по умолчанию*

```xaml
<AppBarButton AccessKey="R" Icon="Refresh" Label="Refresh" IsAccessKeyScope="True">
    <AppBarButton.Flyout>
        <MenuFlyout>
            <MenuFlyoutItem AccessKey="A" Icon="Refresh" Text="Refresh A">
                <MenuFlyoutItem.KeyboardAccelerators>
                    <KeyboardAccelerator Key="R" Modifiers="Control"/>
                </MenuFlyoutItem.KeyboardAccelerators>
            </MenuFlyoutItem>
            <MenuFlyoutItem AccessKey="B" Icon="Globe" Text="Refresh B" />
            <MenuFlyoutItem AccessKey="C" Icon="Globe" Text="Refresh C" />
            <MenuFlyoutItem AccessKey="D" Icon="Globe" Text="Refresh D" />
            <ToggleMenuFlyoutItem AccessKey="E" Icon="Globe" Text="ToggleMe">
                <MenuFlyoutItem.KeyboardAccelerators>
                    <KeyboardAccelerator Key="Q" Modifiers="Control"/>
                </MenuFlyoutItem.KeyboardAccelerators>
            </ToggleMenuFlyoutItem>
        </MenuFlyout>
    </AppBarButton.Flyout>
</AppBarButton>
```

![Подсказка сочетания клавиш](images/accelerators/accelerators-appbar-menuflyoutitem-small.png)

*Сочетания клавиш, добавленная к тексту MenuFlyoutItem*

Управление поведением отображения осуществляется с помощью свойства [KeyboardAcceleratorPlacementMode](https://docs.microsoft.com/uwp/api/windows.ui.xaml.uielement.KeyboardAcceleratorPlacementMode), которое принимает два значения: [Auto](https://docs.microsoft.com/uwp/api/windows.ui.xaml.input.keyboardacceleratorplacementmode) или [Hidden](https://docs.microsoft.com/uwp/api/windows.ui.xaml.input.keyboardacceleratorplacementmode).    

```xaml
<Button Content="Save" Click="OnSave" KeyboardAcceleratorPlacementMode="Auto">
    <Button.KeyboardAccelerators>
        <KeyboardAccelerator Key="S" Modifiers="Control" />
    </Button.KeyboardAccelerators>
</Button>
```

В некоторых случаях может потребоваться предоставить подсказку относительно другого элемента (как правило, объекта-контейнера). Например, элемент управления Pivot, отображающий подсказку для PivotItem с заголовком Pivot. 

Здесь мы покажем, как использовать свойство KeyboardAcceleratorPlacementTarget для отображения сочетания клавиш ускорителя клавиатуры для кнопки "Сохранить" с контейнером Grid вместо кнопки.

```xaml
<Grid x:Name="Container" Padding="30">
  <Button Content="Save"
    Click="OnSave"
    KeyboardAcceleratorPlacementMode="Auto"
    KeyboardAcceleratorPlacementTarget="{x:Bind Container}">
    <Button.KeyboardAccelerators>
      <KeyboardAccelerator  Key="S" Modifiers="Control" />
    </Button.KeyboardAccelerators>
  </Button>
</Grid>
```

### <a name="labels"></a>Метки

В некоторых случаях рекомендуется использовать метку элемента управления, чтобы определить, есть ли у элемента управления связанные с ним ускорители клавиатуры, и если они есть — указать, какая для этого используется клавиша ускорителя клавиатуры. 

Некоторые элементы управления платформы делают это по умолчанию, в частности, объекты [MenuFlyoutItem](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.MenuFlyoutItem) и [ToggleMenuFlyoutItem](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.togglemenuflyoutitem), а объект [AppBarButton](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.appbarbutton) и [AppBarToggleButton](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.appbartogglebutton) делает это, когда они отображаются в меню переполнения [CommandBar](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.commandbar).

![Описание ускорителей клавиатуры в метке пункта меню](images/accelerators/accelerators_menuitemlabel.png)  
*Описание ускорителей клавиатуры в метке пункта меню*

Вы можете переопределить текст ускорителя по умолчанию для метки с помощью свойства [KeyboardAcceleratorTextOverride](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.appbarbutton.KeyboardAcceleratorTextOverride) элементов управления [MenuFlyoutItem](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.MenuFlyoutItem), [ToggleMenuFlyoutItem](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.togglemenuflyoutitem), [AppBarButton](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.appbarbutton) и [AppBarToggleButton](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.appbartogglebutton) (используйте пробел для указания на отсутствие текста). 

> [!NOTE] 
> Переопределенный текст не будет отображаться, если система не сможет определить, подключена ли клавиатура (вы можете проверить это самостоятельно с помощью свойства [KeyboardPresent](https://docs.microsoft.com/uwp/api/windows.devices.input.keyboardcapabilities.KeyboardPresent)).

## <a name="advanced-concepts"></a>Сложные концепции

В этом разделе рассматриваются некоторые низкоуровневые аспекты ускорителей клавиатуры.

### <a name="when-an-accelerator-is-invoked"></a>Условия вызова ускорителя

Ускорители состоят из двух типов клавиш: модификаторов и немодификаторов. К клавишам-модификаторам относятся SHIFT, MENU, CTRL и клавиша Windows, предоставляемые через [VirtualKeyModifiers](http://msdn.microsoft.com/library/windows/apps/xaml/Windows.System.VirtualKeyModifiers). Немодификаторы— это любые виртуальные клавиши, такие как Delete, F3, ПРОБЕЛ, ESC, а также все буквенно-цифровые и пунктуационные клавиши. Ускоритель клавиатуры вызывается, когда пользователь нажимает клавишу-немодификатор, зажав одну или несколько клавиш-модификаторов. Например, если пользователь нажимает сочетание клавиш CTRL+SHIFT+M, то при нажатии клавиши M платформа проверяет модификаторы (CTRL и SHIFT) и активирует ускоритель, если он существует.

> [!NOTE]
> Ускоритель автоматически активируется повторно (например, когда пользователь нажимает сочетание клавиш CTRL+SHIFT, а затем зажимает M, ускоритель вызывается многократно, пока зажата клавиша M). Это поведение невозможно изменить.

### <a name="input-event-priority"></a>Приоритет событий ввода
События ввода происходят в определенной последовательности, которую можно перехватить и обработать с учетом требований приложения. 

#### <a name="the-keydownkeyup-bubbling-event"></a>Событие восходящей маршрутизации KeyDown/KeyUp 

В XAML нажатие клавиши обрабатывается так, как если бы существовал только один входной конвейер восходящей маршрутизации. Этот входной конвейер используется событиями KeyDown/KeyUp и при вводе символов. Например, если фокус находится на определенном элементе, и пользовать нажимает клавишу, вызывается событие KeyDown для этого элемента, затем для его родительского элемента и так далее вверх по дереву, пока свойству args.Handled не будет присвоено значение true.

Кроме того, событие KeyDown использует некоторыми элементами управления для реализации встроенных ускорителей элементов управления. Если у элемента управления есть ускоритель клавиатуры, он обрабатывает событие KeyDown, то есть восходящей маршрутизации события KeyDown не будет. Например, RichEditBox поддерживает копирование с помощью сочетания клавиш CTRL+C. При нажатии клавиши CTRL активируется событие KeyDown, и начинается его восходящая маршрутизация, но если пользователь одновременно нажимает клавишу C, событие KeyDown получается метку Handled ("Обработано") и не вызывается (если только параметру handledEventsToo метода [UIElement.AddHandler](http://msdn.microsoft.com/library/windows/apps/xaml/Windows.UI.Xaml.UIElement.AddHandler) не будет присвоено значение true).

#### <a name="the-characterreceived-event"></a>Событие CharacterReceived

Поскольку событие [CharacterReceived](https://docs.microsoft.com/uwp/api/windows.ui.core.corewindow.CharacterReceived) активируется после события [KeyDown](https://docs.microsoft.com/uwp/api/windows.ui.core.corewindow.KeyDown) для элементов управления текстом, таких как TextBox, можно отменить ввод символов в обработчике события KeyDown.

#### <a name="the-previewkeydown-and-previewkeyup-events"></a>События PreviewKeyDown и PreviewKeyUp

События ввода предварительного просмотра активируются перед всеми остальными событиями. Если эти события не обрабатываются, активируется ускоритель для элемента с фокусом, а за ним— событие KeyDown. Выполняется восходящая маршрутизация обоих событий, пока они не будут обработаны.


![Последовательность основных событий](images/accelerators/accelerators_keyevents.png)
***Последовательность основных событий***

Порядок событий:

Предварительный просмотр событий KeyDown...
Метод OnKeyDown ускорителя приложений Событие KeyDown Ускорители приложений на родительском элементе Метод OnKeyDown на родительском элементе Событие KeyDown на родительском элементе (восходящая маршрутизация до корневого элемента)…
Событие CharacterReceived События PreviewKeyUp События KeyUpEvent

После обработки события ускорителя событие KeyDown также помечается как обработанное. Событие KeyUp остается необработанным.

### <a name="resolving-accelerators"></a>Разрешение ускорителей

Событие ускорителя клавиатуры подвергается восходящей маршрутизации от элемента с фокусом до корневого элемента. Если событие не обработано, платформа XAML ищет другие ускорители приложения без заданной области действия вне пути восходящей маршрутизации.

Если два ускорителя клавиатуры определены одним и тем же сочетанием клавиш, вызывается первый ускоритель клавиатуры, найденный в визуальном дереве.

Ускорители клавиатуры с заданной областью действия вызываются, только если фокус находится внутри этой области. Например, в сетке, содержащей множество элементов управления, ускоритель клавиатуры может быть вызван для элемента управления, только если фокус находится внутри этой сетки (владельца области действия).

### <a name="scoping-accelerators-programmatically"></a>Определение области действия ускорителей программным образом

Метод [UIElement.TryInvokeKeyboardAccelerator](https://docs.microsoft.com/en-us/uwp/api/windows.ui.xaml.uielement.tryinvokekeyboardaccelerator) вызывает все соответствующие ускорители в поддереве элемента.

Метод [UIElement.OnProcessKeyboardAccelerators](https://docs.microsoft.com/en-us/uwp/api/windows.ui.xaml.uielement.onprocesskeyboardaccelerators) выполняется перед ускорителем клавиатуры. Этот метод передает объект [ProcessKeyboardAcceleratorArgs](https://docs.microsoft.com/uwp/api/windows.ui.xaml.input.processkeyboardacceleratoreventargs), содержащий ключ, модификатор и логическое значение, которое указывает, обработан ли ускоритель клавиатуры. Если ускоритель клавиатуры отмечен как обработанный, он подвергается восходящей маршрутизации (поэтому внешний ускоритель клавиатуры не вызывается).

> [!NOTE]
> Метод OnProcessKeyboardAccelerators активируется всегда независимо от выполнения обработки (аналогично событию OnKeyDown). Необходимо проверить, отмечено ли событие как обработанное.

В этом примере мы используем методы OnProcessKeyboardAccelerators и TryInvokeKeyboardAccelerator, чтобы задать область действия ускорителей клавиатуры для объекта Page:

``` csharp
protected override void OnProcessKeyboardAccelerators(
  ProcessKeyboardAcceleratorArgs args)
{
  if(args.Handled != true)
  {
    this.TryInvokeKeyboardAccelerator(args);
    args.Handled = true;
  }
}
```

### <a name="localize-the-accelerators"></a>Локализация ускорителей

Мы рекомендуем локализовывать все ускорители клавиатуры. Это можно сделать с помощью стандартного файла ресурсов UWP (.resw) и атрибута x:Uid в объявлениях XAML. В этом примере среды выполнения Windows автоматически загружает ресурсы.

![Локализация ускорителя клавиатуры с помощью файла ресурсов UWP](images/accelerators/accelerators_localization.png)
***Локализация ускорителя клавиатуры с помощью файла ресурсов UWP***

``` xaml
<Button x:Uid="myButton" Click="OnSave">
  <Button.KeyboardAccelerators>
    <KeyboardAccelerator x:Uid="myKeyAccelerator" Modifiers="Control"/>
  </Button.KeyboardAccelerators>
</Button>
```

### <a name="setup-an-accelerator-programmatically"></a>Определение области действия ускорителя программным образом

Вот пример программного определения ускорителя:

``` csharp
void AddAccelerator(
  VirtualKeyModifiers keyModifiers, 
  VirtualKey key, 
  TypedEventHandler<KeyboardAccelerator, KeyboardAcceleratorInvokedEventArgs> handler )
  {
    var accelerator = 
      new KeyboardAccelerator() 
      { 
        Modifiers = keyModifiers, Key = key
      };
    accelerator.Invoked = handler;
    this.KeyAccelerators.Add(accelerator);
  }
```

> [!NOTE]
> KeyboardAccelerator невозможно предоставить для общего доступа, один и тот ускоритель KeyboardAccelerator невозможно добавить к нескольким элементам.

### <a name="override-keyboard-accelerator-behavior"></a>Переопределение поведения ускорителя клавиатуры

Чтобы переопределить поведение KeyboardAccelerator по умолчанию, вы можете обработать событие [KeyboardAccelerator.Invoked](https://docs.microsoft.com/uwp/api/windows.ui.xaml.input.keyboardaccelerator.Invoked).

В этом примере показано, как переопределить команду "Выбрать все" (ускоритель клавиатуры CTRL + A) в пользовательском элементе управления ListView. Мы также присвоили свойству Handled значение true, чтобы остановить дальнейшую восходящую маршрутизацию события.

```csharp
public class MyListView : ListView
{
  …
  protected override void OnKeyboardAcceleratorInvoked(KeyboardAcceleratorInvokedEventArgs args) 
  {
    if(args.Accelerator.Key == VirtualKey.A 
      && args.Accelerator.Modifiers == KeyboardModifiers.Control)
    {
      CustomSelectAll(TypeOfSelection.OnlyNumbers); 
      args.Handled = true;
    }
  }
  …
}
```

## <a name="related-articles"></a>Еще по теме

* [Взаимодействие с помощью клавиатуры](keyboard-interactions.md)
* [Ключи доступа](access-keys.md)

**Примеры**
* [Галерея элементов управления XAML (то есть XamlUiBasics)](https://github.com/Microsoft/Windows-universal-samples/tree/c2aeaa588d9b134466bbd2cc387c8ff4018f151e/Samples/XamlUIBasics)


 


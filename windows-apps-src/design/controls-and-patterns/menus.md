---
Description: В меню и контекстных меню отображается список команд или параметров, когда пользователь запрашивает их.
title: Меню и контекстные меню
label: Menus and context menus
template: detail.hbs
ms.date: 04/19/2019
ms.topic: article
ms.custom: RS5, 19H1
keywords: windows 10, uwp
ms.assetid: 0327d8c1-8329-4be2-84e3-66e1e9a0aa60
pm-contact: yulikl
design-contact: kimsea
dev-contact: llongley
doc-status: Published
ms.localizationpriority: medium
ms.openlocfilehash: 5e3465d108fc725c45999782eff80593832208b5
ms.sourcegitcommit: 6f32604876ed480e8238c86101366a8d106c7d4e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/21/2019
ms.locfileid: "67319515"
---
# <a name="menus-and-context-menus"></a>Меню и контекстные меню

В меню и контекстных меню отображается список команд или параметров, когда пользователь запрашивает их. Для отображения отдельного встроенного меню используйте всплывающее меню. Используйте строку меню, чтобы отобразить набор меню в горизонтальной строке, обычно в верхней части окна приложения. Каждое меню может включать пункты меню и вложенные меню.

![Пример типичного контекстного меню](images/contextmenu_rs2_icons.png)

| **Получение библиотеки пользовательского интерфейса Windows** |
| - |
| Этот элемент управления является частью библиотеки пользовательского интерфейса Windows, пакета NuGet, который содержит новые элементы управления и компоненты пользовательского интерфейса для приложений UWP. Дополнительные сведения, включая инструкции по установке, см. в [обзорной статье о библиотеке пользовательского интерфейса Windows](https://docs.microsoft.com/uwp/toolkits/winui/). |

| **API платформы** | **API библиотеки пользовательского интерфейса Windows** |
| - | - |
| [Класс MenuFlyout](/uwp/api/windows.ui.xaml.controls.menuflyout), [класс MenuBar](/uwp/api/windows.ui.xaml.controls.menubar), [свойство ContextFlyout](/uwp/api/windows.ui.xaml.uielement.contextflyout), [свойство FlyoutBase.AttachedFlyout](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.primitives.flyoutbase.attachedflyout) | [Класс MenuBar](/uwp/api/microsoft.ui.xaml.controls.menubar) |

## <a name="is-this-the-right-control"></a>Выбор правильного элемента управления

Меню и контекстные меню экономят место, упорядочивая команды и скрывая их до востребования. Если определенная команда используется часто, и имеется достаточно свободного места, вы можете разместить ее на отдельном элементе, а не в меню, чтобы для ее использования пользователям не приходилось открывать меню.

Меню и контекстные меню предназначены для упорядочения команд. Для отображения произвольного содержимого, например уведомлений или запросов подтверждения, используйте [диалоговые окна и всплывающие элементы](dialogs.md).

### <a name="menubar-vs-menuflyout"></a>MenuBar и MenuFlyout

Чтобы отобразить меню во всплывающем окне, присоединенном к элементу пользовательского интерфейса на холсте, используйте элемент управления MenuFlyout для размещения пунктов меню. Вы можете вызывать всплывающее меню как обычное меню или контекстное меню. Всплывающее меню содержит единое меню верхнего уровня и вложенные меню (необязательно).

Строка меню (MenuBar) позволяет отображать набор из нескольких меню верхнего уровня в горизонтальной строке. Строка меню обычно размещается в верхней части окна приложения.

### <a name="menubar-vs-commandbar"></a>MenuBar и CommandBar

MenuBar и CommandBar представляют области, которые можно использовать для отображения команд пользователям. MenuBar обеспечивает простой и быстрый способ выводить набор команд в приложениях, которым могут понадобиться дополнительные функции упорядочения или группирования, недоступные в CommandBar.

MenuBar также можно использовать в сочетании с CommandBar. MenuBar позволяет отобразить все команды, а CommandBar — выделить наиболее часто используемые команды.

## <a name="examples"></a>Примеры

<table>
<th align="left">XAML Controls Gallery<th>
<tr>
<td><img src="images/xaml-controls-gallery-sm.png" alt="XAML controls gallery"></img></td>
<td>
    <p>Если у вас установлено приложение <strong style="font-weight: semi-bold">галереи элементов управления XAML</strong>, щелкните здесь, чтобы <a href="xamlcontrolsgallery:/item/MenuFlyout">открыть приложение и увидеть MenuFlyout в действии</a>.</p>
    <ul>
    <li><a href="https://www.microsoft.com/p/xaml-controls-gallery/9msvh128x2zt">Получить приложение XAML Controls Gallery (Microsoft Store)</a></li>
    <li><a href="https://github.com/Microsoft/Xaml-Controls-Gallery">Получить исходный код (GitHub)</a></li>
    </ul>
</td>
</tr>
</table>

## <a name="menus-vs-context-menus"></a>Сравнение меню и контекстных меню

Меню и контекстные меню сходны по внешнему виду и возможному содержимому. Фактически для их создания можно использовать один и тот же элемент управления — [MenuFlyout](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.MenuFlyout). Различие заключается в том, как пользователь получает к ним доступ.

Когда следует использовать меню или контекстное меню?

- Если главным элементом является кнопка или другой элемент команды, основная задача которого состоит в предоставлении дополнительных команд, используйте меню.
- Если главным элементом является другой тип элемента с иной основной задачей (такой как представление текста или изображения), используйте контекстное меню.

Например, меню можно использовать на кнопке для возможности фильтрации и сортировки списка. В этом случае основное назначение элемента управления кнопки — предоставление доступа к меню.

![Пример меню в "Почте"](images/Mail_Menu.png)

Если вам требуется добавить команды (такие как "вырезать", "копировать" и "вставить") к элементу текста, используйте контекстное меню вместо обычного. В этом случае основная задача элемента текста заключается в представлении и редактировании текста. Дополнительные команды (такие как "вырезать", "копировать" и "вставить") второстепенны и относятся к контекстному меню.

![Пример контекстного меню в фотоальбоме](images/ContextMenu_example.png)

### <a name="menus"></a>Меню

- обладают одной точкой входа (например, меню "Файл" в верхней части экрана), которая отображается постоянно;
- обычно прикреплены к кнопке или родительскому элементу меню;
- вызываются левым щелчком мыши (или эквивалентным действием, например нажатием пальцем);
- связаны с элементом посредством его свойств [Flyout](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.button.flyout) или [FlyoutBase.AttachedFlyout](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.primitives.flyoutbase.attachedflyout) либо сгруппированы в строку меню в верхней части окна приложения.

### <a name="context-menus"></a>Контекстные меню

- Подключаются к одному элементу и отображают вспомогательные команды.
- Вызываются щелчком правой кнопки мыши (или эквивалентным действием, например нажатием и удерживанием пальцем).
- связаны с элементом посредством его свойства [ContextFlyout](https://docs.microsoft.com/uwp/api/windows.ui.xaml.uielement.contextflyout).

## <a name="icons"></a>Значки

Рекомендуем добавлять значки пунктов меню для:

- наиболее часто используемых пунктов;
- пунктов меню, значок которых является стандартным или хорошо известным;
- пунктов меню, значок которых хорошо иллюстрирует предназначение команды.

Вы не обязаны добавлять значки для команд без стандартных зрительных образов. Загадочные значки бесполезны, создают визуальные помехи и не позволяют пользователям сосредоточиться на важных пунктах меню.

![Пример контекстного меню со значками](images/contextmenu_rs2_icons.png)

````xaml
<MenuFlyout>
  <MenuFlyoutItem Text="Share" >
    <MenuFlyoutItem.Icon>
      <FontIcon Glyph="&#xE72D;" />
    </MenuFlyoutItem.Icon>
  </MenuFlyoutItem>
  <MenuFlyoutItem Text="Copy" Icon="Copy" />
  <MenuFlyoutItem Text="Delete" Icon="Delete" />
  <MenuFlyoutSeparator />
  <MenuFlyoutItem Text="Rename" />
  <MenuFlyoutItem Text="Select" />
</MenuFlyout>
````

> [!TIP]
> Размер значков в MenuFlyoutItem составляет 16x16 пикселей. Если вы используете SymbolIcon, FontIcon или PathIcon, значок автоматически масштабируется до правильного размера без потери качества. При использовании BitmapIcon убедитесь, что размер элемента равен 16x16 пикселей.  

## <a name="create-a-menu-flyout-or-a-context-menu"></a>Создание всплывающего или контекстного меню

Для создания всплывающего или контекстного меню используется класс [MenuFlyout](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.menuflyout). Чтобы задать содержимое меню, необходимо добавить объекты [MenuFlyoutItem](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.menuflyoutitem), [MenuFlyoutSubItem](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.menuflyoutsubitem), [ToggleMenuFlyoutItem](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.togglemenuflyoutitem), [RadioMenuFlyoutItem](https://docs.microsoft.com/uwp/api/microsoft.ui.xaml.controls.radiomenuflyoutitem) и [MenuFlyoutSeparator](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.menuflyoutseparator) в элемент MenuFlyout.

Эти объекты предназначены для:

- [MenuFlyoutItem](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.menuflyoutitem) — выполнения немедленного действия;
- [MenuFlyoutSubItem](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.menuflyoutsubitem) — содержания каскадного списка пунктов меню.
- [ToggleMenuFlyoutItem](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.togglemenuflyoutitem) — включения или отключения параметра;
- [RadioMenuFlyoutItem](https://docs.microsoft.com/uwp/api/microsoft.ui.xaml.controls.radiomenuflyoutitem) — переключения между взаимоисключающими пунктами меню.
- [MenuFlyoutSeparator](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.menuflyoutseparator) — визуального разделения элементов меню.

В этом примере создается [MenuFlyout](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.menuflyout) и используется свойство [ContextFlyout](https://docs.microsoft.com/uwp/api/windows.ui.xaml.uielement.contextflyout), доступное для большинства элементов управления, для отображения MenuFlyout в качестве контекстного меню.

````xaml
<Rectangle
  Height="100" Width="100">
  <Rectangle.ContextFlyout>
    <MenuFlyout>
      <MenuFlyoutItem Text="Change color" Click="ChangeColorItem_Click" />
    </MenuFlyout>
  </Rectangle.ContextFlyout>
  <Rectangle.Fill>
    <SolidColorBrush x:Name="rectangleFill" Color="Red" />
  </Rectangle.Fill>
</Rectangle>
````

````csharp
private void ChangeColorItem_Click(object sender, RoutedEventArgs e)
{
    // Change the color from red to blue or blue to red.
    if (rectangleFill.Color == Windows.UI.Colors.Red)
    {
        rectangleFill.Color = Windows.UI.Colors.Blue;
    }
    else
    {
        rectangleFill.Color = Windows.UI.Colors.Red;
    }
}
````

Следующий пример почти идентичен, но вместо использования свойства [ContextFlyout](https://docs.microsoft.com/uwp/api/windows.ui.xaml.uielement.contextflyout) для отображения [класса MenuFlyout](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.menuflyout) как контекстного меню в нем используется свойство [FlyoutBase.ShowAttachedFlyout](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.primitives.flyoutbase.showattachedflyout) для его отображения как меню.

````xaml
<Rectangle
  Height="100" Width="100"
  Tapped="Rectangle_Tapped">
  <FlyoutBase.AttachedFlyout>
    <MenuFlyout>
      <MenuFlyoutItem Text="Change color" Click="ChangeColorItem_Click" />
    </MenuFlyout>
  </FlyoutBase.AttachedFlyout>
  <Rectangle.Fill>
    <SolidColorBrush x:Name="rectangleFill" Color="Red" />
  </Rectangle.Fill>
</Rectangle>
````

````csharp
private void Rectangle_Tapped(object sender, TappedRoutedEventArgs e)
{
    FlyoutBase.ShowAttachedFlyout((FrameworkElement)sender);
}

private void ChangeColorItem_Click(object sender, RoutedEventArgs e)
{
    // Change the color from red to blue or blue to red.
    if (rectangleFill.Color == Windows.UI.Colors.Red)
    {
        rectangleFill.Color = Windows.UI.Colors.Blue;
    }
    else
    {
        rectangleFill.Color = Windows.UI.Colors.Red;
    }
}
````

### <a name="light-dismiss"></a>Исчезновение

Элементы управления с исчезновением, такие как меню, контекстные меню и другие всплывающие элементы, захватывают фокус клавиатуры или геймпада внутри промежуточного пользовательского интерфейса, пока не будут закрыты. Для предоставления визуальной подсказки по этому поведению элементы управления с исчезновением на Xbox отображают наложение, затемняющее видимость вне области действия пользовательского интерфейса. Это поведение можно изменить с помощью свойства [LightDismissOverlayMode](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.primitives.flyoutbase.lightdismissoverlaymode). По умолчанию во временно отображаемых элементах пользовательского интерфейса наложение с исчезновением используется на Xbox (**автоматически**), но не в других семействах устройств. Вы можете принудительно настроить постоянное **включение** или **отключение** наложения.

```xaml
<MenuFlyout LightDismissOverlayMode="Off" />
```

## <a name="create-a-menu-bar"></a>Создание строки меню

> [!IMPORTANT]
> Для использования MenuBar требуется Windows 10, версия 1809 ([SDK 17763](https://developer.microsoft.com/windows/downloads/windows-10-sdk)) или более поздней версии либо [библиотека пользовательского интерфейса Windows](https://docs.microsoft.com/uwp/toolkits/winui/).

Для создания меню в строке меню используйте те же элементы, что и для всплывающего меню. Тем не менее вместо группирования объектов MenuFlyoutItem в элементе MenuFlyout следует сгруппировать их в элементе MenuBarItem. Каждый элемент MenuBarItem добавляется к MenuBar как меню верхнего уровня.

![Пример строки меню](images/menu-bar-submenu.png)

> [!NOTE]
> В этом примере показано только создание структуры пользовательского интерфейса, но не показана реализация всех команд.

```xaml
<muxc:MenuBar>
    <muxc:MenuBarItem Title="File">
        <MenuFlyoutSubItem Text="New">
            <MenuFlyoutItem Text="Plain Text Document"/>
            <MenuFlyoutItem Text="Rich Text Document"/>
            <MenuFlyoutItem Text="Other Formats..."/>
        </MenuFlyoutSubItem>
        <MenuFlyoutItem Text="Open..."/>
        <MenuFlyoutItem Text="Save"/>
        <MenuFlyoutSeparator />
        <MenuFlyoutItem Text="Exit"/>
    </muxc:MenuBarItem>

    <muxc:MenuBarItem Title="Edit">
        <MenuFlyoutItem Text="Undo"/>
        <MenuFlyoutItem Text="Cut"/>
        <MenuFlyoutItem Text="Copy"/>
        <MenuFlyoutItem Text="Paste"/>
    </muxc:MenuBarItem>

    <muxc:MenuBarItem Title="View">
        <MenuFlyoutItem Text="Output"/>
        <MenuFlyoutSeparator/>
        <muxc:RadioMenuFlyoutItem Text="Landscape" GroupName="OrientationGroup"/>
        <muxc:RadioMenuFlyoutItem Text="Portrait" GroupName="OrientationGroup" IsChecked="True"/>
        <MenuFlyoutSeparator/>
        <muxc:RadioMenuFlyoutItem Text="Small icons" GroupName="SizeGroup"/>
        <muxc:RadioMenuFlyoutItem Text="Medium icons" IsChecked="True" GroupName="SizeGroup"/>
        <muxc:RadioMenuFlyoutItem Text="Large icons" GroupName="SizeGroup"/>
    </muxc:MenuBarItem>

    <muxc:MenuBarItem Title="Help">
        <MenuFlyoutItem Text="About"/>
    </muxc:MenuBarItem>
</muxc:MenuBar>
```

## <a name="get-the-sample-code"></a>Получить пример кода

- [Пример из коллекции элементов управления XAML](https://github.com/Microsoft/Xaml-Controls-Gallery) — ознакомьтесь со всеми элементами управления XAML в интерактивном режиме.
- [Пример контекстного меню на XAML](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/XamlContextMenu)

## <a name="related-articles"></a>Связанные статьи

- [Класс MenuFlyout](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.menuflyout)
- [Класс MenuBar](https://docs.microsoft.com/uwp/api/microsoft.ui.xaml.controls.menubar)

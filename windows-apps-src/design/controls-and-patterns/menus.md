---
Description: В меню и контекстных меню отображается список команд или параметров, когда пользователь запрашивает их.
title: Меню и контекстные меню
label: Menus and context menus
template: detail.hbs
ms.date: 01/08/2019
ms.topic: article
keywords: windows 10, uwp
ms.assetid: 0327d8c1-8329-4be2-84e3-66e1e9a0aa60
pm-contact: yulikl
design-contact: kimsea
dev-contact: llongley
doc-status: Published
ms.localizationpriority: medium
ms.custom: RS5
ms.openlocfilehash: 194e8578244fee72d0f122ee96caaf22bf2b8807
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57610489"
---
# <a name="menus-and-context-menus"></a>Меню и контекстные меню

В меню и контекстных меню отображается список команд или параметров, когда пользователь запрашивает их. Для отображения одного, встроенного меню, используйте всплывающее меню. Используйте строку меню, чтобы показать набор меню в горизонтальной строке, обычно в верхней части окна приложения. Каждое меню могут иметь элементы меню и подменю.

![Пример типичного контекстного меню](images/contextmenu_rs2_icons.png)

| **Получение библиотеки пользовательского интерфейса Windows** |
| - |
| Этот элемент управления входит в состав библиотеки пользовательского интерфейса Windows, пакет NuGet, который содержит новые элементы управления и компоненты пользовательского интерфейса для приложений универсальной платформы Windows. Дополнительные сведения, включая инструкции по установке, см. в разделе [Общие сведения о библиотеке пользовательского интерфейса Windows](https://docs.microsoft.com/uwp/toolkits/winui/). |

| **API-интерфейсов платформы** | **Windows API-интерфейсов библиотеки пользовательского интерфейса** |
| - | - |
| [Класс MenuFlyout](/uwp/api/windows.ui.xaml.controls.menuflyout), [класс MenuBar](/uwp/api/windows.ui.xaml.controls.menubar), [свойство ContextFlyout](/uwp/api/windows.ui.xaml.uielement.contextflyout), [FlyoutBase.AttachedFlyout свойство](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.primitives.flyoutbase.attachedflyout.aspx) | [Класс MenuBar](/uwp/api/microsoft.ui.xaml.controls.menubar) |

## <a name="is-this-the-right-control"></a>Выбор правильного элемента управления

Меню и контекстные меню экономят место, упорядочивая команды и скрывая их до востребования. Если определенная команда используется часто, и имеется достаточно свободного места, вы можете разместить ее на отдельном элементе, а не в меню, чтобы для ее использования пользователям не приходилось открывать меню.

Меню и контекстные меню являются для организации команд; Чтобы отобразить произвольное содержимое, например запрос уведомления или подтверждения, используйте [диалогового или всплывающего меню](dialogs.md).

### <a name="menubar-vs-menuflyout"></a>Vs в строке меню. MenuFlyout

Чтобы отобразить меню со всплывающим окном, присоединенного к элементу пользовательского интерфейса на холсте, используйте элемент управления MenuFlyout для размещения пунктах меню. Вы можете вызывать всплывающее меню, как обычное меню или в качестве контекстного меню. Всплывающее меню размещается в едином меню верхнего уровня (и необязательно подменю).

Чтобы отобразить набор нескольких меню верхнего уровня в горизонтальной строке, используйте строку меню. Вы обычно размещается в строке меню в верхней части окна приложения.

### <a name="menubar-vs-commandbar"></a>Vs в строке меню. CommandBar

Строка меню и панели команд обе они представляют области, которые можно использовать для представления команд пользователям. Строки меню предоставляет простой и быстрый способ предоставления набора команд для приложений, которые могут понадобиться дополнительные организации или группы, чем позволяет среда CommandBar.

Строке меню также можно использовать в сочетании с панели команд. Позволяет указать основная часть команды и CommandBar, чтобы выделить наиболее часто используемых команд в строке меню.

## <a name="examples"></a>Примеры

<table>
<th align="left">Галерея элементов управления XAML<th>
<tr>
<td><img src="images/xaml-controls-gallery-sm.png" alt="XAML controls gallery"></img></td>
<td>
    <p>Если у вас установлено приложение <strong style="font-weight: semi-bold">галереи элементов управления XAML</strong>, щелкните здесь, чтобы <a href="xamlcontrolsgallery:/item/MenuFlyout">открыть приложение и увидеть MenuFlyout в действии</a>.</p>
    <ul>
    <li><a href="https://www.microsoft.com/store/productId/9MSVH128X2ZT">Получение коллекции элементов управления XAML приложения (Microsoft Store)</a></li>
    <li><a href="https://github.com/Microsoft/Xaml-Controls-Gallery">Получить исходный код (GitHub)</a></li>
    </ul>
</td>
</tr>
</table>

## <a name="menus-vs-context-menus"></a>Сравнение меню и контекстных меню

Меню и контекстные меню схожи в том, как они выглядят и что они могут содержать. На самом деле, можно использовать тот же элемент управления [MenuFlyout](https://msdn.microsoft.com/library/windows/apps/dn299030), для их создания. Разница заключается в том, как пользователю предоставляется возможность доступа к нему.

Когда следует использовать меню или контекстное меню?

- Если главным элементом является кнопка или другой элемент команды, основная задача которого состоит в предоставлении дополнительных команд, используйте меню.
- Если главным элементом является другой тип элемента с иной основной задачей (такой как представление текста или изображения), используйте контекстное меню.

Например используйте меню на кнопке для обеспечения фильтрации и сортировки для списка. В этом случае основное назначение элемента управления кнопки — предоставление доступа к меню.

![Пример меню в "Почте"](images/Mail_Menu.png)

Если вам требуется добавить команды (такие как "вырезать", "копировать" и "вставить") к элементу текста, используйте контекстное меню вместо обычного. В этом случае основная задача элемента текста заключается в представлении и редактировании текста. Дополнительные команды (такие как "вырезать", "копировать" и "вставить") второстепенны и относятся к контекстному меню.

![Пример контекстного меню в фотоальбоме](images/ContextMenu_example.png)

### <a name="menus"></a>Меню

- обладают одной точкой входа (например, меню "Файл" в верхней части экрана), которая отображается постоянно;
- обычно прикреплены к кнопке или родительскому элементу меню;
- вызываются левым щелчком мыши (или эквивалентным действием, например нажатием пальцем);
- Связанные с элементом с помощью его [всплывающего меню](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.button.flyout.aspx) или [FlyoutBase.AttachedFlyout](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.primitives.flyoutbase.attachedflyout.aspx) свойства, или сгруппированы в строке меню в верхней части окна приложения.

### <a name="context-menus"></a>Контекстные меню

- Подключаются к одному элементу и отображают вспомогательные команды.
- Вызываются правым щелчком мыши (или эквивалентным действием, например нажатием и удерживанием пальцем);
- связаны с элементом посредством его свойства [ContextFlyout](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.uielement.contextflyout.aspx).

## <a name="icons"></a>Значки

Рассмотрите возможность предоставления значков элементов меню для:

- Наиболее часто используемые элементы.
- Элементы меню, значок standard или известное.
- Элементы меню, значок также показано, что делает команда.

Вы не обязаны предоставлять значки для команд без стандартных зрительных образов. Загадочные значки бесполезны, создают визуальные помехи и не позволяют пользователям сосредоточиться на важных меню.

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
> Размер значка в MenuFlyoutItem — 16x16px. Если вы используете SymbolIcon, FontIcon или PathIcon, значок автоматически масштабируется до требуемых размеров без потери точности. При использовании BitmapIcon убедитесь, что ваше средство равно 16x16 пикселей.  

## <a name="create-a-menu-flyout-or-a-context-menu"></a>Создать всплывающее меню или контекстного меню

Чтобы создать всплывающее меню или контекстного меню, используйте [MenuFlyout класс](https://msdn.microsoft.com/library/windows/apps/dn299030). Чтобы задать содержимое меню, необходимо добавить объекты [MenuFlyoutItem](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.menuflyoutitem.aspx), [ToggleMenuFlyoutItem](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.togglemenuflyoutitem.aspx) и [MenuFlyoutSeparator](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.menuflyoutseparator.aspx) в элемент MenuFlyout.

Эти объекты предназначены для:

- [MenuFlyoutItem](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.menuflyoutitem.aspx) — выполнения немедленного действия;
- [ToggleMenuFlyoutItem](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.togglemenuflyoutitem.aspx) — включения или отключения параметра;
- [MenuFlyoutSeparator](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.menuflyoutseparator.aspx) — визуального разделения элементов меню.

В этом примере создается [MenuFlyout](https://msdn.microsoft.com/library/windows/apps/dn299030) и использует [ContextFlyout](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.uielement.contextflyout.aspx) свойства, свойства, доступные для большинства элементов управления, чтобы показать MenuFlyout в качестве контекстного меню.

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

Следующий пример почти идентичен, но вместо использования свойства [ContextFlyout](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.uielement.contextflyout.aspx) для отображения [класса MenuFlyout](https://msdn.microsoft.com/library/windows/apps/dn299030) как контекстного меню в нем используется свойство [FlyoutBase.ShowAttachedFlyout](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.primitives.flyoutbase.showattachedflyout) для его отображения как меню.

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

### <a name="light-dismiss"></a>Индикатор отклонить

Элементы управления с исчезновением, такие как меню, контекстные меню и другие всплывающие элементы, захватывают фокус клавиатуры или геймпада внутри промежуточного пользовательского интерфейса, пока не будут закрыты. Для предоставления визуальной подсказки по этому поведению элементы управления с исчезновением на Xbox отображают наложение, затемняющее видимость вне области действия пользовательского интерфейса. Это поведение можно изменить с помощью свойства [LightDismissOverlayMode](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.primitives.flyoutbase.lightdismissoverlaymode.aspx). По умолчанию промежуточные элементы пользовательского интерфейса отображают наложение с исчезновением на Xbox (**Auto**), но не на других семействах устройств, хотя приложения могут принудительно устанавливать наложению постоянное значение **Вкл** или **Выкл**.

```xaml
<MenuFlyout LightDismissOverlayMode="Off" />
```

## <a name="create-a-menu-bar"></a>Создать строку меню

> [!IMPORTANT]
> Строка меню требуется Windows 10, версия 1809 ([SDK 17763](https://developer.microsoft.com/windows/downloads/windows-10-sdk)) или более поздней версии, или [библиотека пользовательского интерфейса Windows](https://docs.microsoft.com/uwp/toolkits/winui/).

Используйте те же элементы для создания меню в строке меню, как показано всплывающее меню. Тем не менее вместо MenuFlyoutItem объектов в MenuFlyout группирования, можно сгруппировать их в элементе MenuBarItem. Каждый MenuBarItem добавляется к строке меню, как меню верхнего уровня.

![Пример строки меню](images/menu-bar-submenu.png)

> [!NOTE]
> В этом примере показано, как только для создания структуры пользовательского интерфейса, но не содержит реализации всех команд.

```xaml
<MenuBar>
    <MenuBarItem Title="File">
        <MenuFlyoutSubItem Text="New">
            <MenuFlyoutItem Text="Plain Text Document"/>
            <MenuFlyoutItem Text="Rich Text Document"/>
            <MenuFlyoutItem Text="Other Formats..."/>
        </MenuFlyoutSubItem>
        <MenuFlyoutItem Text="Open..."/>
        <MenuFlyoutItem Text="Save"/>
        <MenuFlyoutSeparator/>
        <MenuFlyoutItem Text="Exit"/>
    </MenuBarItem>

    <MenuBarItem Title="Edit">
        <MenuFlyoutItem Text="Undo"/>
        <MenuFlyoutItem Text="Cut"/>
        <MenuFlyoutItem Text="Copy"/>
        <MenuFlyoutItem Text="Paste"/>
    </MenuBarItem>

    <MenuBarItem Title="Help">
        <MenuFlyoutItem Text="About"/>
    </MenuBarItem>
</MenuBar>
```

## <a name="get-the-sample-code"></a>Получить пример кода

- [Образец галереи элементов управления XAML](https://github.com/Microsoft/Xaml-Controls-Gallery) — ознакомьтесь со всеми элементами управления XAML в интерактивном формате.
- [Пример XAML контекстное меню](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/XamlContextMenu)

## <a name="related-articles"></a>Связанные статьи

- [Класс MenuFlyout](https://msdn.microsoft.com/library/windows/apps/dn299030)
- [Класс MenuBar](/uwp/api/Windows.UI.Xaml.Controls.MenuBar)

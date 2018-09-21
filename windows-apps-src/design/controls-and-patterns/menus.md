---
author: mijacobs
Description: Menus and context menus display a list of commands or options when the user requests them.
title: Меню и контекстные меню
label: Menus and context menus
template: detail.hbs
ms.author: mijacobs
ms.date: 07/19/2018
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: windows 10, uwp
ms.assetid: 0327d8c1-8329-4be2-84e3-66e1e9a0aa60
pm-contact: yulikl
design-contact: kimsea
dev-contact: llongley
doc-status: Published
ms.localizationpriority: medium
ms.openlocfilehash: 703667bf22ce11c119463008e868a943d447c7ff
ms.sourcegitcommit: 5dda01da4702cbc49c799c750efe0e430b699502
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/21/2018
ms.locfileid: "4113642"
---
# <a name="menus-and-context-menus"></a>Меню и контекстные меню

> [!IMPORTANT]
> В этой статье описана еще не выпущенная функция, которая может быть существенно изменена до коммерческого выпуска. Майкрософт не дает никаких гарантий, явных или подразумеваемых, в отношении предоставленной здесь информации. Функции предварительного просмотра, требуют [последние сборки Windows 10 Insider Preview и пакет SDK](https://insider.windows.com/for-developers/) или [Библиотеке пользовательского интерфейса Windows](https://docs.microsoft.com/uwp/toolkits/winui/).

В меню и контекстных меню отображается список команд или параметров, когда пользователь запрашивает их. Используйте всплывающий элемент меню для отображения одного, встроенного меню. Используйте меню для отображения набор меню в горизонтальной строке, обычно в верхней части окна приложения. Каждое меню может иметь элементов меню и подменю.

![Пример типичного контекстного меню](images/contextmenu_rs2_icons.png)

| **Получить библиотеку пользовательского интерфейса Windows** |
| - |
| Этот элемент управления не включен в библиотеке Windows пользовательского интерфейса, пакет NuGet, содержащий новые элементы управления и функции пользовательского интерфейса для приложений UWP. Дополнительные сведения, включая инструкции по установке см. в разделе [Общие сведения о библиотеке пользовательского интерфейса Windows](https://docs.microsoft.com/uwp/toolkits/winui/). |

| **API-интерфейсы платформы** | **API библиотеки пользовательского интерфейса Windows** |
| - | - |
| [Класс MenuFlyout](/uwp/api/windows.ui.xaml.controls.menuflyout), [класс в строке](/uwp/api/windows.ui.xaml.controls.menubar), [свойство ContextFlyout](/uwp/api/windows.ui.xaml.uielement.contextflyout), [свойство FlyoutBase.AttachedFlyout](/uwp/api/windows.ui.xaml.controls.primitives.flyoutbase.attachedflyout) | [Класс строки меню](/uwp/api/microsoft.ui.xaml.controls.menubar) |

## <a name="is-this-the-right-control"></a>Выбор правильного элемента управления

Меню и контекстные меню экономят место, упорядочивая команды и скрывая их до востребования. Если определенная команда используется часто, и имеется достаточно свободного места, вы можете разместить ее на отдельном элементе, а не в меню, чтобы для ее использования пользователям не приходилось открывать меню.

Меню и контекстные меню предназначены для упорядочивая команды; для отображения произвольного содержимого, например уведомлений или подтверждения запроса, используйте [диалоговое окно или всплывающий элемент](dialogs.md).

### <a name="menubar-vs-menuflyout"></a>В строке и MenuFlyout

Для отображения меню в всплывающий элемент, присоединенного к элементу пользовательского интерфейса на холсте, используйте элемент управления MenuFlyout для размещения своих элементов меню. Вы можете вызывать всплывающее меню, как обычный меню или контекстного меню. Всплывающее меню размещается одного меню верхнего уровня (и дополнительных дочерних меню).

Для отображения набора нескольких меню верхнего уровня в горизонтальной строке, используйте строку меню. Обычно располагаются в строке меню в верхней части окна приложения.

### <a name="menubar-vs-commandbar"></a>В строке и CommandBar

В строке и CommandBar оба представления поверхностей, которые можно использовать для предоставления пользователям команд. В строке предоставляет быстрый и простой способ отображения набор команд для приложений, которые могут потребоваться дополнительные организации или группировка, а затем позволяет CommandBar.

Также можно использовать в строке вместе с CommandBar. Используйте строки меню для предоставления большую часть команды и CommandBar для выделения наиболее часто используемых команд.

## <a name="examples"></a>Примеры.

<table>
<th align="left">Галерея элементов управления XAML<th>
<tr>
<td><img src="images/xaml-controls-gallery-sm.png" alt="XAML controls gallery"></img></td>
<td>
    <p>Если у вас установлено приложение <strong style="font-weight: semi-bold">галереи элементов управления XAML</strong>, щелкните здесь, чтобы <a href="xamlcontrolsgallery:/item/MenuFlyout">открыть приложение и увидеть MenuFlyout в действии</a>.</p>
    <ul>
    <li><a href="https://www.microsoft.com/store/productId/9MSVH128X2ZT">Скачать приложение галереи элементов управления XAML (Microsoft Store)</a></li>
    <li><a href="https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/XamlUIBasics">Получить исходный код (GitHub)</a></li>
    </ul>
</td>
</tr>
</table>

## <a name="menus-vs-context-menus"></a>Сравнение меню и контекстных меню

Меню и контекстные меню похожи на виду и возможному. На самом деле можно использовать тот же элемент управления, [MenuFlyout](https://msdn.microsoft.com/library/windows/apps/dn299030), для их создания. Разница в том, как разрешить пользователю доступ к нему.

Когда следует использовать меню или контекстное меню?

- Если главным элементом является кнопка или другой элемент команды, основная задача которого состоит в предоставлении дополнительных команд, используйте меню.
- Если главным элементом является другой тип элемента с иной основной задачей (такой как представление текста или изображения), используйте контекстное меню.

Например используйте меню кнопки для предоставления фильтрованию параметры для списка. В этом случае основное назначение элемента управления кнопки— предоставление доступа к меню.

![Пример меню в "Почте"](images/Mail_Menu.png)

Если вам требуется добавить команды (такие как "вырезать", "копировать" и "вставить") к элементу текста, используйте контекстное меню вместо обычного. В этом случае основная задача элемента текста заключается в представлении и редактировании текста. Дополнительные команды (такие как "вырезать", "копировать" и "вставить") второстепенны и относятся к контекстному меню.

![Пример контекстного меню в фотоальбоме](images/ContextMenu_example.png)

### <a name="menus"></a>Меню

- обладают одной точкой входа (например, меню "Файл" в верхней части экрана), которая отображается постоянно;
- обычно прикреплены к кнопке или родительскому элементу меню;
- вызываются левым щелчком мыши (или эквивалентным действием, например нажатием пальцем);
- Связанный с элементом посредством его свойства [всплывающий элемент](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.button.flyout.aspx) или [FlyoutBase.AttachedFlyout](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.primitives.flyoutbase.attachedflyout.aspx) или сгруппированы в строке меню в верхней части окна приложения.

### <a name="context-menus"></a>Контекстные меню

- Подключаются к одному элементу и отображают вспомогательные команды.
- Вызываются правым щелчком мыши (или эквивалентным действием, например нажатием и удерживанием пальцем);
- связаны с элементом посредством его свойства [ContextFlyout](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.uielement.contextflyout.aspx).

## <a name="icons"></a>Значки

Рассмотрите возможность предоставления значков элементов меню для:

- Наиболее часто используемые элементы.
- Элементы меню, значок которых является стандартным или хорошо известным.
- Элементы меню, значок которых хорошо иллюстрирует предназначение команды.

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
> Размер значков в MenuFlyoutItem составляет 16 x 16 пикселей. Если вы используете SymbolIcon, FontIcon или PathIcon, значок автоматически масштабируется до правильного размера без потери качества. При использовании BitmapIcon убедитесь, что ваше средство равно 16x16 пикселей.  

## <a name="create-a-menu-flyout-or-a-context-menu"></a>Создание всплывающего меню или контекстного меню

Чтобы создать всплывающее меню или контекстного меню, используется [класс MenuFlyout](https://msdn.microsoft.com/library/windows/apps/dn299030). Чтобы задать содержимое меню, необходимо добавить объекты [MenuFlyoutItem](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.menuflyoutitem.aspx), [ToggleMenuFlyoutItem](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.togglemenuflyoutitem.aspx) и [MenuFlyoutSeparator](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.menuflyoutseparator.aspx) в элемент MenuFlyout.

Эти объекты предназначены для:

- [MenuFlyoutItem](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.menuflyoutitem.aspx)— выполнения немедленного действия;
- [ToggleMenuFlyoutItem](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.togglemenuflyoutitem.aspx)— включения или отключения параметра;
- [MenuFlyoutSeparator](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.menuflyoutseparator.aspx)— визуального разделения элементов меню.

В этом примере создает [MenuFlyout](https://msdn.microsoft.com/library/windows/apps/dn299030) и использует свойство [ContextFlyout](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.uielement.contextflyout.aspx) , доступное для большинства элементов управления, чтобы показать MenuFlyout как контекстного меню.

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

### <a name="light-dismiss"></a>С исчезновением

Элементы управления с исчезновением, такие как меню, контекстные меню и другие всплывающие элементы, захватывают фокус клавиатуры или геймпада внутри промежуточного пользовательского интерфейса, пока не будут закрыты. Для предоставления визуальной подсказки по этому поведению элементы управления с исчезновением на Xbox отображают наложение, затемняющее видимость вне области действия пользовательского интерфейса. Это поведение можно изменить с помощью свойства [LightDismissOverlayMode](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.primitives.flyoutbase.lightdismissoverlaymode.aspx). По умолчанию промежуточные элементы пользовательского интерфейса отображают наложение с исчезновением на Xbox (**Auto**), но не на других семействах устройств, хотя приложения могут принудительно устанавливать наложению постоянное значение **Вкл. ** или **Выкл.**.

```xaml
<MenuFlyout LightDismissOverlayMode="Off" />
```

## <a name="create-a-menu-bar"></a>Создать меню

> **Предварительный просмотр**: в строке требуется [последние сборки Windows 10 Insider Preview и пакет SDK](https://insider.windows.com/for-developers/) или [Библиотеке пользовательского интерфейса Windows](https://docs.microsoft.com/uwp/toolkits/winui/).

Используйте те же элементы для создания меню в строке меню как всплывающее меню. Тем не менее вместо группирование объектов MenuFlyoutItem в MenuFlyout, можно сгруппировать их в элемент MenuBarItem. Каждый MenuBarItem добавляется в строке как меню верхнего уровня.

![Пример панели меню](images/menu-bar-submenu.png)

> [!NOTE]
> В этом примере показано, как только для создания со структурой пользовательского интерфейса, но не показывать реализации всех команд.

```xaml
<MenuBar>
    <MenuBarItem Title="File">
        <MenuFlyoutSubItem Text="New">
            <MenuFlyoutItem Text="Plain Text Document"/>
            <MenuFlyoutItem Text="Rich Text Document"/>
            <MenuFlyoutItem Text="Other Formats..."/>
        </MenuFlyoutSubItem>
        <MenuFlyoutItem Text="Open..."/>
        <MenuFlyoutItem Text="Save">
        <MenuFlyoutSeparator />
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

- [Образец галереи элементов управления XAML](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/XamlUIBasics) — ознакомьтесь со всеми элементами управления XAML в интерактивном формате.
- [Пример контекстного меню на XAML](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/XamlContextMenu)

## <a name="related-articles"></a>Смежные разделы

- [Класс MenuFlyout](https://msdn.microsoft.com/library/windows/apps/dn299030)
- [Класс строки меню](/uwp/api/Windows.UI.Xaml.Controls.MenuBar)

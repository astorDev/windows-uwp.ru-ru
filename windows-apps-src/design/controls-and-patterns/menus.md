---
author: mijacobs
Description: A flyout is a lightweight popup that is used to temporarily show UI that is related to what the user is currently doing.
title: Меню и контекстные меню
label: Menus and context menus
template: detail.hbs
ms.author: mijacobs
ms.date: 05/19/2017
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
ms.openlocfilehash: e38e9d61e8546d412cc30bad26680243f3a188e4
ms.sourcegitcommit: 53ba430930ecec8ea10c95b390fe6e654fe363e1
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/06/2018
ms.locfileid: "3416615"
---
# <a name="menus-and-context-menus"></a>Меню и контекстные меню



В меню и контекстных меню отображается список команд или параметров, когда пользователь запрашивает их.

> **Важные API-интерфейсы**: [класс MenuFlyout](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.MenuFlyout), [свойство ContextFlyout](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.uielement.contextflyout.aspx), [свойство FlyoutBase.AttachedFlyout](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.primitives.flyoutbase.attachedflyout.aspx)

![Пример типичного контекстного меню](images/contextmenu_rs2_icons.png)


## <a name="is-this-the-right-control"></a>Выбор правильного элемента управления
Меню и контекстные меню экономят место, упорядочивая команды и скрывая их до востребования. Если определенная команда используется часто, и имеется достаточно свободного места, вы можете разместить ее на отдельном элементе, а не в меню, чтобы для ее использования пользователям не приходилось открывать меню.

Меню и контекстные меню предназначены для упорядочения команд. Для отображения произвольного содержимого, например уведомлений, или запросов подтверждения используйте [диалоговые окна и всплывающие элементы](dialogs.md).  

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

Меню и контекстные меню идентичны по внешнему виду и возможному содержимому. Фактически, для их создания вы используете один и тот же элемент управления— [MenuFlyout](https://msdn.microsoft.com/library/windows/apps/dn299030). Единственное различие в том, как пользователь получает к ним доступ.

Когда следует использовать меню или контекстное меню?
* Если главным элементом является кнопка или другой элемент команды, основная задача которого состоит в предоставлении дополнительных команд, используйте меню.
* Если главным элементом является другой тип элемента с иной основной задачей (такой как представление текста или изображения), используйте контекстное меню.

Например, используйте меню для кнопки на панели навигации для предоставления дополнительных параметров навигации. В этом случае основное назначение элемента управления кнопки— предоставление доступа к меню.

![Пример меню в "Почте"](images/Mail_Menu.png)

Если вам требуется добавить команды (такие как "вырезать", "копировать" и "вставить") к элементу текста, используйте контекстное меню вместо обычного. В этом случае основная задача элемента текста заключается в представлении и редактировании текста. Дополнительные команды (такие как "вырезать", "копировать" и "вставить") второстепенны и относятся к контекстному меню.

![Пример контекстного меню в фотоальбоме](images/ContextMenu_example.png) 

<div class="side-by-side">
<div class="side-by-side-content">
  <div class="side-by-side-content-left">
   <p><b>Меню</b></p>
<ul>
<li>обладают одной точкой входа (например, меню "Файл" в верхней части экрана), которая отображается постоянно;</li>
<li>обычно прикреплены к кнопке или родительскому элементу меню;</li>
<li>вызываются левым щелчком мыши (или эквивалентным действием, например нажатием пальцем);</li><li>связаны с элементом посредством его свойств <a href="https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.button.flyout.aspx">Flyout</a> или <a href="https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.primitives.flyoutbase.attachedflyout.aspx">FlyoutBase.AttachedFlyout</a>.</li>
</ul>
</div>
  <div class="side-by-side-content-right">
   <p><b>Контекстные меню</b></p>

<ul>
<li>Подключаются к одному элементу и отображают вспомогательные команды.</li>
<li>Вызываются правым щелчком мыши (или эквивалентным действием, например нажатием и удерживанием пальцем);</li><li>связаны с элементом посредством его свойства <a href="https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.uielement.contextflyout.aspx">ContextFlyout</a>.</li>
</ul>
  </div>
</div>
</div>

## <a name="icons"></a>Значки

Рассмотрите возможность предоставления значков элементов меню для:

<ul>
<li> Наиболее часто используемые элементы </li>
<li> Элементы меню, значок которых является стандартным или хорошо известным </li>
<li> Элементы меню, значок которых хорошо иллюстрирует предназначение команды </li>
</ul>

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
> Размер значков в MenuFlyoutItems составляет 16x16 пикселей. Если вы используете SymbolIcon, FontIcon или PathIcon, значок автоматически масштабируется до правильного размера без потери качества. При использовании BitmapIcon убедитесь, что ваше средство равно 16x16 пикселей.  

## <a name="create-a-menu-or-a-context-menu"></a>Создание меню или контекстного меню

Для создания меню или контекстного меню используется класс [MenuFlyout](https://msdn.microsoft.com/library/windows/apps/dn299030). Чтобы задать содержимое меню, необходимо добавить объекты [MenuFlyoutItem](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.menuflyoutitem.aspx), [ToggleMenuFlyoutItem](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.togglemenuflyoutitem.aspx) и [MenuFlyoutSeparator](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.menuflyoutseparator.aspx) в элемент MenuFlyout. Эти объекты предназначены для:
* [MenuFlyoutItem](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.menuflyoutitem.aspx)— выполнения немедленного действия;
* [ToggleMenuFlyoutItem](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.togglemenuflyoutitem.aspx)— включения или отключения параметра;
* [MenuFlyoutSeparator](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.menuflyoutseparator.aspx)— визуального разделения элементов меню.


В этом примере создается [класс MenuFlyout](https://msdn.microsoft.com/library/windows/apps/dn299030) и используется свойство [ContextFlyout](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.uielement.contextflyout.aspx), доступное для большинства элементов управления, для отображения [MenuFlyout class](https://msdn.microsoft.com/library/windows/apps/dn299030) в качестве контекстного меню.

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


> Элементы управления с исчезновением, такие как меню, контекстные меню и другие всплывающие элементы, захватывают фокус клавиатуры или геймпада внутри промежуточного пользовательского интерфейса, пока не будут закрыты. Для предоставления визуальной подсказки по этому поведению элементы управления с исчезновением на Xbox отображают наложение, затемняющее видимость вне области действия пользовательского интерфейса. Это поведение можно изменить с помощью свойства [LightDismissOverlayMode](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.primitives.flyoutbase.lightdismissoverlaymode.aspx). По умолчанию промежуточные элементы пользовательского интерфейса отображают наложение с исчезновением на Xbox (**Auto**), но не на других семействах устройств, хотя приложения могут принудительно устанавливать наложению постоянное значение **Вкл. ** или **Выкл.**.

> ```xaml
> <MenuFlyout LightDismissOverlayMode="Off" />
> ```

## <a name="get-the-sample-code"></a>Получить пример кода

- [Образец галереи элементов управления XAML](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/XamlUIBasics) — ознакомьтесь со всеми элементами управления XAML в интерактивном формате.
- [Пример контекстного меню на XAML](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/XamlContextMenu)

## <a name="related-articles"></a>Смежные разделы

- [Класс MenuFlyout](https://msdn.microsoft.com/library/windows/apps/dn299030)

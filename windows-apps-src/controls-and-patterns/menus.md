---
author: mijacobs
Description: "Всплывающий элемент — это облегченное всплывающее окно, которое используется для временного отображения элементов пользовательского интерфейса, связанных с выполняемой в данный момент задачей."
title: "Меню и контекстные меню"
label: Menus and context menus
template: detail.hbs
translationtype: Human Translation
ms.sourcegitcommit: 5f50e490caa5d1d88c2f8315dc47e15b0ae22a05
ms.openlocfilehash: badb03c97ae0f2350e5d7592f10168bb7d6e7d1a

---
# <a name="menus-and-context-menus"></a>Меню и контекстные меню

<link rel="stylesheet" href="https://az835927.vo.msecnd.net/sites/uwp/Resources/css/custom.css"> 

В меню и контекстных меню отображается список команд или параметров, когда пользователь запрашивает их.

![Пример типичного контекстного меню](images/controls_contextmenu_singlepane.png)

<div class="important-apis" >
<b>Важные API</b><br/>
<ul>
<li>[Класс MenuFlyout](https://msdn.microsoft.com/library/windows/apps/dn299030)</li>
<li>[Свойство ContextFlyout](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.uielement.contextflyout.aspx)</li>
<li>[Свойство FlyoutBase.AttachedFlyout](https://msdn.microsoft.com/en-us/library/windows/apps/windows.ui.xaml.controls.primitives.flyoutbase.attachedflyout.aspx)</li>
</ul>
</div>


## <a name="is-this-the-right-control"></a>Выбор правильного элемента управления
Меню и контекстные меню экономят место, упорядочивая команды и скрывая их до востребования. Если определенная команда используется часто, и имеется достаточно свободного места, вы можете разместить ее на отдельном элементе, а не в меню, чтобы для ее использования пользователям не приходилось открывать меню. 

Меню и контекстные меню предназначены для упорядочения команд. Для отображения произвольного содержимого, например уведомлений, или запросов подтверждения используйте [диалоговые окна и всплывающие элементы](dialogs.md).  


## <a name="menus-vs-context-menus"></a>Сравнение меню и контекстных меню

Меню и контекстные меню идентичны по внешнему виду и возможному содержимому. Фактически, для их создания вы используете один и тот же элемент управления — [MenuFlyout](https://msdn.microsoft.com/library/windows/apps/dn299030). Единственное различие в том, как пользователь получает к ним доступ. 

Когда следует использовать меню или контекстное меню?
* Если главным элементом является кнопка или другой элемент команды, основная задача которого состоит в предоставлении дополнительных команд, используйте меню.
* Если главным элементом является другой тип элемента с иной основной задачей (такой как представление текста или изображения), используйте контекстное меню. 

Например, используйте меню для кнопки на панели навигации для предоставления дополнительных параметров навигации. В этом случае основное назначение элемента управления кнопки — предоставление доступа к меню. 

Если вам требуется добавить команды (такие как "вырезать", "копировать" и "вставить") к элементу текста, используйте контекстное меню вместо обычного. В этом случае основная задача элемента текста заключается в представлении и редактировании текста. Дополнительные команды (такие как "вырезать", "копировать" и "вставить") второстепенны и относятся к контекстному меню. 

<div class="side-by-side">
<div class="side-by-side-content">
  <div class="side-by-side-content-left">
   <p><b>Меню</b></p>
<p>
<ul>
<li>обладают одной точкой входа (например, меню "Файл" в верхней части экрана), которая отображается постоянно;</li>
<li>обычно прикреплены к кнопке или родительскому элементу меню;</li>
<li>вызываются левым щелчком мыши (или эквивалентным действием, например нажатием пальцем);</li>  
<li>связаны с элементом посредством его свойств [Flyout](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.button.flyout.aspx) или [FlyoutBase.AttachedFlyout](https://msdn.microsoft.com/en-us/library/windows/apps/windows.ui.xaml.controls.primitives.flyoutbase.attachedflyout.aspx).</li> 
</ul>
</p><br/>

  </div>
  <div class="side-by-side-content-right">
   <p><b>Контекстные меню</b></p>
   
<ul>
<li>прикреплены к одному элементу, но открываются тогда, когда это необходимо в рамках контекста;</li>
<li>вызываются правым щелчком мыши (или эквивалентным действием, например нажатием и удерживанием пальцем);</li>
<li>связаны с элементом посредством его свойства [ContextFlyout](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.uielement.contextflyout.aspx).  
</ul><br/>

  </div>
</div>
</div>

## <a name="create-a-menu-or-a-context-menu"></a>Создание меню или контекстного меню

Для создания меню или контекстного меню используется [MenuFlyout class](https://msdn.microsoft.com/library/windows/apps/dn299030). Чтобы задать содержимое меню, необходимо добавить объекты [MenuFlyoutItem](https://msdn.microsoft.com/en-us/library/windows/apps/xaml/windows.ui.xaml.controls.menuflyoutitem.aspx), [ToggleMenuFlyoutItem](https://msdn.microsoft.com/en-us/library/windows/apps/xaml/windows.ui.xaml.controls.togglemenuflyoutitem.aspx) и [MenuFlyoutSeparator](https://msdn.microsoft.com/en-us/library/windows/apps/xaml/windows.ui.xaml.controls.menuflyoutseparator.aspx) в элемент MenuFlyout. Эти объекты предназначены для:
* [MenuFlyoutItem](https://msdn.microsoft.com/en-us/library/windows/apps/xaml/windows.ui.xaml.controls.menuflyoutitem.aspx) — выполнения немедленного действия;
* [ToggleMenuFlyoutItem](https://msdn.microsoft.com/en-us/library/windows/apps/xaml/windows.ui.xaml.controls.togglemenuflyoutitem.aspx) — включения или отключения параметра;
* [MenuFlyoutSeparator](https://msdn.microsoft.com/en-us/library/windows/apps/xaml/windows.ui.xaml.controls.menuflyoutseparator.aspx) — визуального разделения элементов меню.


В этом примере создается [класс MenuFlyout](https://msdn.microsoft.com/library/windows/apps/dn299030) и используется свойство [ContextFlyout](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.uielement.contextflyout.aspx), доступное для большинства элементов управления, для отображения [MenuFlyout class](https://msdn.microsoft.com/library/windows/apps/dn299030) в качестве контекстного меню.

````xaml
<Rectangle 
  Height="100" Width="100" 
  Tapped="Rectangle_Tapped">
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

Следующий пример почти идентичен, но вместо использования свойства [ContextFlyout](https://msdn.microsoft.com/en-us/library/windows/apps/windows.ui.xaml.uielement.contextflyout.aspx) для отображения [класса MenuFlyout](https://msdn.microsoft.com/library/windows/apps/dn299030) как контекстного меню в нем используется свойство [FlyoutBase.ShowAttachedFlyout](https://msdn.microsoft.com/en-us/library/windows/apps/windows.ui.xaml.controls.primitives.flyoutbase.showattachedflyout) для его отображения как меню. 

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


> Элементы управления с исчезновением, такие как меню, контекстные меню и другие всплывающие элементы, захватывают фокус клавиатуры или геймпада внутри промежуточного пользовательского интерфейса, пока не будут закрыты. Для предоставления визуальной подсказки по этому поведению элементы управления с исчезновением на Xbox отображают наложение, затемняющее видимость вне области действия пользовательского интерфейса. Это поведение можно изменить с помощью нового свойства [LightDismissOverlayMode](https://msdn.microsoft.com/en-us/library/windows/apps/windows.ui.xaml.controls.primitives.flyoutbase.lightdismissoverlaymode.aspx). По умолчанию промежуточные элементы пользовательского интерфейса отображают наложение с исчезновением на Xbox, но не на других семействах устройств, хотя приложения могут принудительно устанавливать наложению постоянное значение **Вкл.** или **Выкл.**.

> ```xaml
> <MenuFlyout LightDismissOverlayMode="Off">
> ```

## <a name="get-the-sample-code"></a>Получить пример кода
*   [Основы создания пользовательского интерфейса XAML](https://github.com/Microsoft/Windows-universal-samples/blob/master/Samples/XamlUIBasics)<br/>
    Ознакомьтесь со всеми элементами управления XAML в интерактивном формате.

## <a name="related-articles"></a>Связанные разделы

- [**Класс MenuFlyout**](https://msdn.microsoft.com/library/windows/apps/dn299030)



<!--HONumber=Dec16_HO2-->



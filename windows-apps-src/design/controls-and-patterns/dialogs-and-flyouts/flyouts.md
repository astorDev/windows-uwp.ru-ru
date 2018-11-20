---
author: mijacobs
Description: Dialogs and flyouts display transient UI elements that appear when the user requests them or when something happens that requires notification or approval.
title: Всплывающий элемент управления
template: detail.hbs
ms.author: mijacobs
ms.date: 05/19/2017
ms.topic: article
keywords: windows 10, uwp
ms.assetid: ad6affd9-a3c0-481f-a237-9a1ecd561be8
pm-contact: yulikl
design-contact: kimsea
dev-contact: niallm
doc-status: Published
ms.localizationpriority: medium
ms.openlocfilehash: e68f8f48ca9ba67a29c8a52a5d59767a080f642b
ms.sourcegitcommit: ed0304b8a214c03b8aab74b8ef12c9f82b8e3c5f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/19/2018
ms.locfileid: "7290886"
---
# <a name="flyouts"></a>Всплывающие элементы

Всплывающий элемент — это исчезающий контейнер, который показывает в качестве своего содержимого произвольный пользовательский интерфейс. Всплывающие элементы могут содержать другие всплывающих элементов или контекстное меню для создания вложенных взаимодействия.

![Контекстное меню размещается внутри всплывающего элемента](../images/flyout-nested.png)

> **Важные API -интерфейсы**: [класс Flyout](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Flyout)

## <a name="is-this-the-right-control"></a>Выбор подходящего элемента управления

* Не используйте всплывающий элемент вместо [подсказки](../tooltips.md) или [контекстного меню](../menus.md). Используйте подсказку, чтобы вывести краткое описание, которое исчезает через определенное время. Для контекстных действий, связанных с элементом пользовательского интерфейса, например копированием или вставкой, используйте контекстное меню.

Рекомендации о том, когда следует использовать всплывающий элемент и когда следует использовать диалоговое окно (аналогичного элемента управления), см. в разделе [диалоговые окна и всплывающие элементы](index.md). 

## <a name="examples"></a>Примеры.

<table>
<th align="left">Галерея элементов управления XAML<th>
<tr>
<td><img src="../images/xaml-controls-gallery-sm.png" alt="XAML controls gallery"></img></td>
<td>
    <p>Если у вас установлено приложение <strong style="font-weight: semi-bold">галереи элементов управления XAML</strong>, щелкните здесь, чтобы открыть приложение и увидеть <a href="xamlcontrolsgallery:/item/ContentDialog">ContentDialog</a> или <a href="xamlcontrolsgallery:/item/Flyout">Flyout</a> в действии.</p>
    <ul>
    <li><a href="https://www.microsoft.com/store/productId/9MSVH128X2ZT">Скачать приложение галереи элементов управления XAML (Microsoft Store)</a></li>
    <li><a href="https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/XamlUIBasics">Получить исходный код (GitHub)</a></li>
    </ul>
</td>
</tr>
</table>

##  <a name="how-to-create-a-flyout"></a>Создание всплывающего элемента


Всплывающие элементы связаны с определенными элементами управления. Вы можете использовать свойство [Placement](/uwp/api/Windows.UI.Xaml.Controls.Primitives.FlyoutBase.Placement) для указания места отображения всплывающего элемента: вверху, слева, внизу, справа или в центре. Если выбрать [полный режим размещения](/uwp/api/Windows.UI.Xaml.Controls.Primitives.FlyoutPlacementMode), приложение растягивает всплывающий элемент или размещает его в центре окна приложения. Некоторые элементы управления, например [Button](/uwp/api/Windows.UI.Xaml.Controls.Button), содержат свойство [Flyout](/uwp/api/Windows.UI.Xaml.Controls.Button.Flyout), которое можно использовать для привязки всплывающего элемента или [контекстного меню](../menus.md).

В этом примере создается простой всплывающий элемент, в котором отображается текст при нажатии кнопки.
````xaml
<Button Content="Click me">
  <Button.Flyout>
     <Flyout>
        <TextBlock Text="This is a flyout!"/>
     </Flyout>
  </Button.Flyout>
</Button>
````

Если элемент управления лишен свойства "Flyout", вы можете использовать присоединенное свойство [FlyoutBase.AttachedFlyout](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.primitives.flyoutbase.AttachedFlyoutProperty). При этом также следует вызвать метод [FlyoutBase.ShowAttachedFlyout](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Primitives.FlyoutBase#Windows_UI_Xaml_Controls_Primitives_FlyoutBase_ShowAttachedFlyout_Windows_UI_Xaml_FrameworkElement_) для отображения всплывающего элемента.

В этом примере к изображению добавляется простой всплывающий элемент. Когда пользователь нажимает на изображение, приложение отображает всплывающий элемент.

````xaml
<Image Source="Assets/cliff.jpg" Width="50" Height="50"
  Margin="10" Tapped="Image_Tapped">
  <FlyoutBase.AttachedFlyout>
    <Flyout>
      <TextBlock Text="This is some text in a flyout."  />
    </Flyout>        
  </FlyoutBase.AttachedFlyout>
</Image>
````

````csharp
private void Image_Tapped(object sender, TappedRoutedEventArgs e)
{
    FlyoutBase.ShowAttachedFlyout((FrameworkElement)sender);
}
````

В предыдущих примерах всплывающие элементы определялись внутренне. Вы также можете определить всплывающий элемент как статический ресурс и использовать его с несколькими элементами. В этом примере показано создание более сложного всплывающего элемента, в котором отображается увеличенный вариант изображения при нажатии его эскиза.

````xaml
<!-- Declare the shared flyout as a resource. -->
<Page.Resources>
    <Flyout x:Key="ImagePreviewFlyout" Placement="Right">
        <!-- The flyout's DataContext must be the Image Source
             of the image the flyout is attached to. -->
        <Image Source="{Binding Path=Source}"
            MaxHeight="400" MaxWidth="400" Stretch="Uniform"/>
    </Flyout>
</Page.Resources>
````

````xaml
<!-- Assign the flyout to each element that shares it. -->
<StackPanel>
    <Image Source="Assets/cliff.jpg" Width="50" Height="50"
           Margin="10" Tapped="Image_Tapped"
           FlyoutBase.AttachedFlyout="{StaticResource ImagePreviewFlyout}"
           DataContext="{Binding RelativeSource={RelativeSource Mode=Self}}"/>
    <Image Source="Assets/grapes.jpg" Width="50" Height="50"
           Margin="10" Tapped="Image_Tapped"
           FlyoutBase.AttachedFlyout="{StaticResource ImagePreviewFlyout}"
           DataContext="{Binding RelativeSource={RelativeSource Mode=Self}}"/>
    <Image Source="Assets/rainier.jpg" Width="50" Height="50"
           Margin="10" Tapped="Image_Tapped"
           FlyoutBase.AttachedFlyout="{StaticResource ImagePreviewFlyout}"
           DataContext="{Binding RelativeSource={RelativeSource Mode=Self}}"/>
</StackPanel>
````

````csharp
private void Image_Tapped(object sender, TappedRoutedEventArgs e)
{
    FlyoutBase.ShowAttachedFlyout((FrameworkElement)sender);  
}
````

## <a name="style-a-flyout"></a>Стилизация всплывающего элемента
Для стилизации всплывающего элемента изменяется его свойство [FlyoutPresenterStyle](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Flyout.FlyoutPresenterStyle). В этом примере показан абзац создания программы-оболочки текста и делает блок текста доступным для программ чтения с экрана.

![Всплывающий элемент со специальными возможностями с переносом текста](../images/flyout-wrapping-text.png)

````xaml
<Flyout>
  <Flyout.FlyoutPresenterStyle>
    <Style TargetType="FlyoutPresenter">
      <Setter Property="ScrollViewer.HorizontalScrollMode"
          Value="Disabled"/>
      <Setter Property="ScrollViewer.HorizontalScrollBarVisibility" Value="Disabled"/>
      <Setter Property="IsTabStop" Value="True"/>
      <Setter Property="TabNavigation" Value="Cycle"/>
    </Style>
  </Flyout.FlyoutPresenterStyle>
  <TextBlock Style="{StaticResource BodyTextBlockStyle}" Text="Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat."/>
</Flyout>
````

## <a name="styling-flyouts-for-10-foot-experiences"></a>Всплывающие элементы стиля для большом расстоянии

Элементы управления с исчезновением, например всплывающий элемент, захватывают фокус клавиатуры или геймпада внутри промежуточного пользовательского интерфейса, пока не будут закрыты. Для предоставления визуальной подсказки по этому поведению элементы управления с исчезновением на Xbox отображают наложение, затемняющее контрастность и видимость вне области действия пользовательского интерфейса. Это поведение можно изменить с помощью нового свойства [`LightDismissOverlayMode`](/uwp/api/Windows.UI.Xaml.Controls.Primitives.FlyoutBase.LightDismissOverlayMode). По умолчанию всплывающие элементы пользовательского интерфейса отображают наложение с исчезновением на Xbox, но не на других семействах устройств, хотя приложения могут принудительно устанавливать наложению постоянное значение **Вкл.** или **Выкл.**.

![Всплывающий элемент с наложением яркости](../images/flyout-smoke.png)

```xaml
<MenuFlyout LightDismissOverlayMode="On">
```

## <a name="light-dismiss-behavior"></a>Поведение с исчезновением
Всплывающие элементы могут быть закрытыми с подсветкой быстрого закрыть действие, в том числе
-   Коснитесь вне всплывающий элемент
-   Нажать клавишу Escape на клавиатуре
-   Нажмите кнопку Назад системы оборудования или программного обеспечения
-   Нажмите кнопку B игрового контроллера

После закрытия касанием этот жест поглощает обычно и не передается пользовательский Интерфейс под. Например если за открытых раскрывающихся отображается кнопка, пользователя при первом касании закрывает всплывающий элемент, но не активирует эту кнопку. Нажатие кнопки требуется второе касание.

Можно изменить это поведение, назначение кнопки в качестве элемента input сквозной всплывающий элемент. Всплывающий элемент закрыть в результате освещения закроет описанных выше действий и будет передавать события касания назначенным `OverlayInputPassThroughElement`. Рассмотрим внедряют это поведение, чтобы ускорить взаимодействия с пользователем на функционально сходных элементов. Если ваше приложение имеет коллекцию в Избранное и каждый элемент в коллекции содержит присоединенных всплывающего, следует предположить, что пользователям может потребоваться взаимодействовать с несколькими всплывающие элементы в последовательные.

[!NOTE] Старайтесь не назначить наложение сквозной элемента input что приводит к деструктивных действий. Пользователи становятся habituated отдельная света закрыть действия, которые не активирует основной пользовательский Интерфейс. Закрыть, удалить или аналогично деструктивными кнопки не должна активировать индикатор закрывать, чтобы избежать непредвиденных и нарушить работу.

В следующем примере будут активированы все три кнопки внутри FavoritesBar на первом касании.

````xaml
<Page>
    <Page.Resources>
        <Flyout x:Name="TravelFlyout" x:Key="TravelFlyout"
                OverlayInputPassThroughElement="{x:Bind FavoritesBar}">
            <StackPanel>
                <HyperlinkButton Content="Washington Trails Association"/>
                <HyperlinkButton Content="Washington Cascades - Go Northwest! A Travel Guide"/>  
            </StackPanel>
        </Flyout>
    </Page.Resources>

    <Grid>
        <Grid.RowDefinitions>
            <RowDefinition Height="Auto"/>
            <RowDefinition Height="*"/>
        </Grid.RowDefinitions>
        <StackPanel x:Name="FavoritesBar" Orientation="Horizontal">
            <HyperlinkButton x:Name="PageLinkBtn">Bing</HyperlinkButton>  
            <Button x:Name="Folder1" Content="Travel" Flyout="{StaticResource TravelFlyout}"/>
            <Button x:Name="Folder2" Content="Entertainment" Click="Folder2_Click"/>
        </StackPanel>
        <ScrollViewer Grid.Row="1">
            <WebView x:Name="WebContent"/>
        </ScrollViewer>
    </Grid>
</Page>
````
````csharp
private void Folder2_Click(object sender, RoutedEventArgs e)
{
     Flyout flyout = new Flyout();
     flyout.OverlayInputPassThroughElement = FavoritesBar;
     ...
     flyout.ShowAt(sender as FrameworkElement);
{
````

## <a name="get-the-sample-code"></a>Получить пример кода

- [Образец галереи элементов управления XAML](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/XamlUIBasics) — ознакомьтесь со всеми элементами управления XAML в интерактивном формате.

## <a name="related-articles"></a>Еще по теме
- [Подсказки](../tooltips.md)
- [Меню и контекстное меню](../menus.md)
- [Класс Flyout](/uwp/api/Windows.UI.Xaml.Controls.Flyout)
- [Класс ContentDialog](/uwp/api/Windows.UI.Xaml.Controls.ContentDialog)
---
Description: Диалоговые окна и всплывающие элементы используются для временного отображения элементов пользовательского интерфейса по запросу пользователя, либо если происходит нечто, требующее уведомления или подтверждения.
title: Всплывающие элементы
template: detail.hbs
ms.date: 05/19/2017
ms.topic: article
keywords: windows 10, uwp
ms.assetid: ad6affd9-a3c0-481f-a237-9a1ecd561be8
pm-contact: yulikl
design-contact: kimsea
dev-contact: niallm
doc-status: Published
ms.localizationpriority: medium
ms.openlocfilehash: 1e008f08d9bf98e309d895f2916ea8aaf84e8464
ms.sourcegitcommit: 0dee502484df798a0595ac1fe7fb7d0f5a982821
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/08/2020
ms.locfileid: "82969909"
---
# <a name="flyouts"></a>Всплывающие элементы

Всплывающий элемент — это контейнер с возможностью исчезновения, который отображает в качестве содержимого произвольный пользовательский интерфейс. Всплывающие элементы могут содержать другие вложенные всплывающие элементы или контекстные меню.

![Контекстное меню внутри всплывающего элемента](../images/flyout-nested.png)

**Получение библиотеки пользовательского интерфейса Windows**

|  |  |
| - | - |
| ![Логотип WinUI](../images/winui-logo-64x64.png) | Библиотека пользовательского интерфейса Windows 2.2 или более поздних версий содержит новый шаблон для этого элемента управления, который использует закругленные углы. Дополнительные сведения см. в разделе о [радиусе угла](/windows/uwp/design/style/rounded-corner). WinUI — это пакет NuGet, содержащий новые элементы управления и функции пользовательского интерфейса для приложений для Windows. Дополнительные сведения, включая инструкции по установке, см. в описании [библиотеки пользовательского интерфейса Windows](https://docs.microsoft.com/uwp/toolkits/winui/). |

> **API платформы:** [класс Flyout](/uwp/api/Windows.UI.Xaml.Controls.Flyout)

## <a name="is-this-the-right-control"></a>Выбор правильного элемента управления

* Не используйте всплывающий элемент вместо [подсказки](../tooltips.md) или [контекстного меню](../menus.md). Используйте подсказку, чтобы вывести краткое описание, которое исчезает через определенное время. Для контекстных действий, связанных с элементом пользовательского интерфейса, например копированием или вставкой, используйте контекстное меню.

Рекомендации по целесообразности использования диалоговых окон и всплывающих элементов (аналогичных элементов управления) приведены в статье [Диалоговые окна и всплывающие элементы](index.md).

## <a name="examples"></a>Примеры

<table>
<th align="left">XAML Controls Gallery<th>
<tr>
<td><img src="../images/xaml-controls-gallery-app-icon-sm.png" alt="XAML controls gallery"></img></td>
<td>
    <p>Если у вас установлено приложение <strong style="font-weight: semi-bold">XAML Controls Gallery</strong>, щелкните здесь, чтобы открыть его и увидеть <a href="xamlcontrolsgallery:/item/ContentDialog">ContentDialog</a> или <a href="xamlcontrolsgallery:/item/Flyout">Flyout</a> в действии.</p>
    <ul>
    <li><a href="https://www.microsoft.com/store/productId/9MSVH128X2ZT">Получить приложение XAML Controls Gallery (Microsoft Store)</a></li>
    <li><a href="https://github.com/Microsoft/Xaml-Controls-Gallery">Получить исходный код (GitHub)</a></li>
    </ul>
</td>
</tr>
</table>

##  <a name="how-to-create-a-flyout"></a>Как создать всплывающий элемент


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

## <a name="styling-flyouts-for-10-foot-experiences"></a>Изменение стиля всплывающих элементов для более наглядного отображения

Элементы управления с исчезновением, например всплывающие элементы, захватывают фокус клавиатуры или геймпада внутри промежуточного пользовательского интерфейса, пока не будут закрыты. Для предоставления визуальной подсказки по этому поведению элементы управления с исчезновением на приставке Xbox отображают наложение, затемняющее контрастность и видимость вне области действия пользовательского интерфейса. Это поведение можно изменить с помощью свойства [`LightDismissOverlayMode`](/uwp/api/Windows.UI.Xaml.Controls.Primitives.FlyoutBase.LightDismissOverlayMode). По умолчанию всплывающие элементы пользовательского интерфейса отображают наложение с исчезновением на приставке Xbox, но не на других семействах устройств, хотя приложения могут принудительно устанавливать наложению постоянное значение **Вкл.** или **Выкл.** .

![Всплывающий элемент с наложением яркости](../images/flyout-smoke.png)

```xaml
<MenuFlyout LightDismissOverlayMode="On">
```

## <a name="light-dismiss-behavior"></a>Поведение с исчезновением
Всплывающие элементы можно закрыть с помощью таких действий исчезновения:
-    Коснитесь области за пределами всплывающего элемента.
-    Нажмите клавишу Escape на клавиатуре.
-    Нажмите аппаратную или программную системную кнопку "Назад".
-    Нажмите кнопку геймпада "B".

При закрытии касанием этот жест обычно не передается пользовательскому интерфейсу под элементом. Например, если за открытым всплывающим элементом отображается кнопка, первое касание приведет к закрытию всплывающего элемента, но кнопка при этом не будет активирована. Для нажатия кнопки нужно второе касание.

Вы можете изменить это поведение, назначив кнопку в качестве прозрачного элемента ввода для всплывающего элемента. Всплывающий элемент закроется при выполнении действий исчезновения, описанных выше, и передаст событие касания назначенному элементу `OverlayInputPassThroughElement`. Такое поведение рекомендуется использовать для ускорения взаимодействия пользователей с функционально схожими элементами. Если ваше приложение имеет коллекцию избранного и каждый элемент в коллекции содержит присоединенный всплывающий элемент, логично предположить, что пользователям может потребоваться последовательно взаимодействовать с несколькими всплывающими элементами.

[!NOTE] Старайтесь не назначать прозрачный элемент ввода наложения, так как это приведет к деструктивным действиям. Пользователи привыкают к действиям незаметного исчезновения, которые активируют основной пользовательский интерфейс. Кнопки "Закрыть", "Удалить" или другие кнопки деструктивных действий не должны активироваться при исчезновении, так как это может стать причиной непредвиденного и ошибочного поведения.

В следующем примере будут активированы все три кнопки внутри FavoritesBar при первом касании.

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

## <a name="get-the-sample-code"></a>Получение примера кода

- [Пример из коллекции элементов управления XAML](https://github.com/Microsoft/Xaml-Controls-Gallery) — ознакомьтесь со всеми элементами управления XAML в интерактивном режиме.

## <a name="related-articles"></a>Похожие статьи
- [Подсказки](../tooltips.md)
- [Меню и контекстное меню](../menus.md)
- [Класс Flyout](/uwp/api/Windows.UI.Xaml.Controls.Flyout)
- [Класс ContentDialog](/uwp/api/Windows.UI.Xaml.Controls.ContentDialog)

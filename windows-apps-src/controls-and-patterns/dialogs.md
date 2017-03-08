---
author: mijacobs
Description: "Диалоговые окна и всплывающие элементы используются для временного отображения элементов пользовательского интерфейса по запросу пользователя, либо если происходит нечто, требующее уведомления или подтверждения."
title: "Диалоговые окна и всплывающие элементы"
label: Dialogs
template: detail.hbs
ms.author: mijacobs
ms.date: 02/08/2017
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: windows 10, uwp
ms.assetid: ad6affd9-a3c0-481f-a237-9a1ecd561be8
translationtype: Human Translation
ms.sourcegitcommit: 5645eee3dc2ef67b5263b08800b0f96eb8a0a7da
ms.openlocfilehash: e76ae1e85f1512a939f2b7ee50ed205c0c55605b
ms.lasthandoff: 02/08/2017

---
# <a name="dialogs-and-flyouts"></a>Диалоговые окна и всплывающие элементы

<link rel="stylesheet" href="https://az835927.vo.msecnd.net/sites/uwp/Resources/css/custom.css"> 

Диалоговые окна и всплывающие элементы используются для временного отображения элементов пользовательского интерфейса, если происходит нечто, требующее уведомления, подтверждения или дополнительных сведений от пользователя.

<div class="important-apis" >
<b>Важные API</b><br/>
<ul>
<li>[Класс ContentDialog](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.contentdialog.aspx)</li>
<li>[Класс Flyout](https://msdn.microsoft.com/library/windows/apps/dn279496)</li>
</ul>
</div>


<div class="side-by-side">
<div class="side-by-side-content">
  <div class="side-by-side-content-left">
   <p><b>Диалоговые окна</b> <br/><br/>
    ![Пример диалогового окна](images/dialogs/dialog-delete-file-example.png)</p>
<p>Диалоговые окна — это модальные наложения пользовательского интерфейса, которые предоставляют контекстную информацию о приложении. Диалоговые окна блокируют взаимодействие с окном приложения, пока пользователь явно не закроет окно. Они часто требуют от пользователя совершения каких-либо действий.   
</p><br/>

  </div>
  <div class="side-by-side-content-right">
   <p><b>Всплывающие элементы</b> <br/><br/>
   ![Пример всплывающего элемента](images/flyout-example.png)</p>
<p>Всплывающий элемент — это облегченное всплывающее контекстно-зависимое окно, отображающее элемент пользовательского интерфейса в зависимости от действий пользователя. Расположение и размер всплывающего элемента подчиняются определенным правилам. Он позволяет обнаружить скрытый элемент управления, отобразить дополнительные сведения об элементе либо запросить у пользователя подтверждение действия. 
</p><p>В отличие от диалогового окна, всплывающий элемент можно быстро закрыть нажатием или щелчком за его пределами, нажатием клавиши Escape или кнопки "Назад", изменением размера окна приложения или ориентации устройства.
</p><br/>

  </div>
</div>
</div>

## <a name="is-this-the-right-control"></a>Выбор правильного элемента управления

* Диалоговые окна и всплывающие элементы используются для уведомления пользователей о важной информации или запроса подтверждения либо дополнительных сведений перед совершением действия. 
* Не используйте всплывающий элемент вместо [подсказки](tooltips.md) или [контекстного меню](menus.md). Используйте подсказку, чтобы вывести краткое описание, которое исчезает через определенное время. Для контекстных действий, связанных с элементом пользовательского интерфейса, например копированием или вставкой, используйте контекстное меню.  


Диалоговые окна и всплывающие элементы позволяют пользователям быть в курсе важной информации, но также мешают взаимодействию с пользователем. Поскольку диалоговые окна модальны (приводят к блокировке), они прерывают работу пользователей и мешают им выполнять дальнейшие действия, пока не произойдет взаимодействие с диалоговым окном. Всплывающие элементы в меньшей степени мешают взаимодействию с пользователем, но отображение слишком большого числа всплывающих элементов может отвлекать. 

Учитывайте важность информации, которую требуется отобразить: важна ли она настолько, чтобы прерывать пользователя? Также учтите частоту отображения информации. Если диалоговое окно или уведомление отображается каждые несколько минут, возможно, следует выделить для этой информации место в основном пользовательском интерфейсе. Возьмем, к примеру, клиент чата. Вместо того чтобы отображать всплывающий элемент каждый раз, когда друг выполняет вход, можно отображать список друзей, находящихся на данный момент в сети, и выделять друзей, выполняющих вход. 

Всплывающие элементы и диалоговые окна часто используются для подтверждения действия (например, удаления файла) перед его выполнением. Если ожидается, что пользователь будет часто выполнять определенное действие, вы можете предоставить пользователю возможность отменить ошибочное действие, чем запрашивать его подтверждение каждый раз. 



## <a name="dialogs-vs-flyouts"></a>Диалоговые окна и всплывающие элементы

Определив, собираетесь ли вы использовать диалоговое окно или всплывающий элемент, необходимо выбрать один из этих вариантов. 

Учитывая то, что, в отличие от всплывающих элементов, диалоговые окна блокируют взаимодействие, последние следует использовать в тех случаях, когда пользователь должен уделить все внимание определенной информации или ответить на вопрос. С другой стороны, всплывающие элементы можно использовать для привлечения внимания к определенной информации, которую пользователь вполне может проигнорировать. 

<div class="side-by-side">
<div class="side-by-side-content">
  <div class="side-by-side-content-left">
   <p><b>Используйте диалоговое окно в следующих целях.</b> <br/>
<ul>
<li>Чтобы сообщить важную информацию, которую пользователь **должен** прочитать и осознать перед продолжением работы. Ниже перечислены примеры таких данных.
<ul>
  <li>угроза безопасности пользователя;</li>
  <li>намерение изменить ценные данные без возможности отмены действия;</li>
  <li>намерение удалить ценные данные;</li>
  <li>подтверждение покупки из приложения;</li>
</ul>

</li>
<li>Сообщения об ошибках, которые относятся к приложению в целом, например ошибка подключения.</li>
<li>Вопросы, если приложению нужно задать пользователю блокирующий вопрос, например, если приложение не может сделать выбор от имени пользователя. Блокирующий вопрос нельзя проигнорировать или отложить. У пользователя должны быть понятные варианты выбора.</li>
</ul> 
</p>
  </div>
  <div class="side-by-side-content-right">
   <p><b>Используйте всплывающий элемент в следующих целях.</b> <br/>
<ul>
<li>сбор дополнительных сведений для завершения действия;</li>
<li>отображение временно актуальных сведений; например, когда пользователь щелкает эскиз изображения в приложении фотоальбома, можно использовать всплывающий элемент для отображения увеличенного варианта этого изображения;</li>
<li>предупреждения и подтверждения, в том числе связанные с потенциально деструктивными действиями;</li>
<li>отображение дополнительной информации, например подробных сведений или более длинных описаний элемента на странице.</li>
</ul></p>
  </div>
</div>
</div>

<div class="microsoft-internal-note">
Элементы управления с исчезновением захватывают фокус клавиатуры или геймпада внутри промежуточного пользовательского интерфейса, пока не будут закрыты. Для предоставления визуальной подсказки по этому поведению элементы управления с исчезновением на Xbox отображают наложение, затемняющее видимость вне области действия пользовательского интерфейса. Это поведение можно изменить с помощью нового свойства `LightDismissOverlayMode`. По умолчанию промежуточные элементы пользовательского интерфейса отображают наложение с исчезновением на Xbox, но не на других семействах устройств, хотя приложения могут принудительно устанавливать наложению постоянное значение **Вкл.** или **Выкл.**.

```xaml
<MenuFlyout LightDismissOverlayMode=\"Off\">
```
</div>

## <a name="dialogs"></a>Диалоговые окна
### <a name="general-guidelines"></a>Общие рекомендации

-   В первой строке диалогового сообщения явно изложите, в чем проблема или что требуется от пользователя.
-   Заголовок диалогового окна — это основная инструкция. Он необязателен.
    -   Используйте краткий заголовок для пояснений, что пользователям следует сделать в диалоговом окне. Длинные заголовки не переносятся по строкам и будут усечены.
    -   Если вы используете диалоговое окно для простого сообщения, указания на ошибку или вопроса, заголовок можно опустить. Ключевую информацию должен передавать текст сообщения.
    -   Убедитесь, что заголовок соответствует предлагаемому выбору кнопок.
-   Содержимое диалогового окна включает текст описания. Оно обязательно.
    -   Изложите сообщение, ошибку или блокирующий вопрос как можно проще.
    -   Если имеется заголовок диалогового окна, используйте область содержимого для более подробных сведений или пояснения терминологии. Не повторяйте заголовок в немного измененной формулировке.
-   Должна иметься по крайней мере одна диалоговая кнопка.
    -   Кнопки — это единственный механизм, позволяющий пользователям закрыть диалоговое окно.
    -   Используйте кнопки с текстом, которые определяют конкретные варианты ответа на основную инструкцию или содержание. Например: "Разрешить приложению AppName доступ к вашему расположению?", после которого следуют кнопки "Разрешить" и "Запретить". Конкретные варианты ответа воспринимаются быстрее, что помогает эффективно принимать решения.
    - Располагайте кнопки фиксации в указанном порядке: 
        -   ОК/[Выполнить]/Да
        -   [Не выполнять]/Нет
        -   Отмена
        
        (Где [Выполнить] и [Не выполнять] — конкретные варианты ответа на основную инструкцию.)
   
-   Диалоговые окна ошибки отображают сообщение об ошибке в диалоговом окне, а также любые сопутствующие сведения. На единственной кнопке, используемой в диалоговом окне ошибки, должно быть написано «Закрыть» или аналогичное действие.
-   Не используйте диалоговые окна при возникновении контекстно-зависимых от определенной области на странице ошибок, таких как ошибки при проверке (например, в полях для ввода паролей). Используйте непосредственно элемент Canvas приложения для отображения внутренних ошибок.

### <a name="confirmation-dialogs-okcancel"></a>Диалоговые окна подтверждения ("ОК" и "Отмена")
Диалоговое окно подтверждения позволяет пользователям подтвердить действие. Они могут подтвердить выполнение действия или отменить его.  
Типичное диалоговое окно подтверждения обычно содержит две кнопки: подтверждения ("ОК") и отмены.  

<ul>
    <li>
        <p>В целом кнопка подтверждения должна быть слева (основная кнопка), а кнопка отмены (вспомогательная) — справа.</p>
         ![Диалоговое окно с кнопками "ОК" или "Отмена"](images/dialogs/dialog-delete-file-example.png)
        
    </li>
    <li>Как указано в разделе "Общие рекомендации", используйте кнопки с текстом, который описывает конкретные варианты ответа на основную инструкцию или содержимое.
    </li>
</ul>

> На некоторых платформах кнопка подтверждения размещена справа, а не слева. Так почему рекомендуется поместить ее слева?  Если предполагается, что большинство пользователей — правши и держат телефон в правой руке, им будет удобнее нажимать кнопку подтверждения слева, так как более вероятно, что она будет расположена в пределах досягаемости больших пальцев пользователя. Чтобы нажать кнопки в правой части экрана, пользователю необходимо переместить большой палец в менее удобное положение.

### <a name="create-a-dialog"></a>Создание диалогового окна
Для создания диалогового окна используется [класс ContentDialog](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.contentdialog.aspx). Диалоговое окно можно создать в виде кода или разметки. Хотя обычно проще определять элементы пользовательского интерфейса в XAML, при создании простого диалогового окна удобнее использовать обычный код. В этом примере показано создание диалогового окна для уведомления пользователя об отсутствии подключения к сети WiFi, а для отображения диалогового окна используется метод [ShowAsync](https://msdn.microsoft.com/en-us/library/windows/apps/windows.ui.xaml.controls.contentdialog.showasync.aspx).

```csharp
private async void displayNoWifiDialog()
{
    ContentDialog noWifiDialog = new ContentDialog()
    {
        Title = "No wifi connection",
        Content = "Check connection and try again",
        PrimaryButtonText = "Ok"
    };

    ContentDialogResult result = await noWifiDialog.ShowAsync();
}
```

Когда пользователь нажимает кнопку диалогового кона, метод [ShowAsync](https://msdn.microsoft.com/en-us/library/windows/apps/windows.ui.xaml.controls.contentdialog.showasync.aspx) возвращает [ContentDialogResult](https://msdn.microsoft.com/en-us/library/windows/apps/windows.ui.xaml.controls.contentdialogresult.aspx), чтобы сообщить вам, какую кнопку нажал пользователь. 

В этом примере в диалоговом окне задается вопрос и используется возвращенный [ContentDialogResult](https://msdn.microsoft.com/en-us/library/windows/apps/windows.ui.xaml.controls.contentdialogresult.aspx), чтобы определить ответ пользователя. 

```csharp
private async void displayDeleteFileDialog()
{
    ContentDialog deleteFileDialog = new ContentDialog()
    {
        Title = "Delete file permanently?",
        Content = "If you delete this file, you won't be able to recover it. Do you want to delete it?",
        PrimaryButtonText = "Delete",
        SecondaryButtonText = "Cancel"
    };

    ContentDialogResult result = await deleteFileDialog.ShowAsync();
    
    // Delete the file if the user clicked the primary button. 
    /// Otherwise, do nothing. 
    if (result == ContentDialogResult.Primary)
    {
        // Delete the file. 
    }
}
```

## <a name="flyouts"></a>Всплывающие элементы
###  <a name="create-a-flyout"></a>Создание всплывающего элемента

Всплывающий элемент — это контейнер с открытым окончанием, который показывает в качестве своего содержимого произвольный пользовательский интерфейс. 

<div class="microsoft-internal-note">
Сюда входят всплывающие элементы и контекстные меню, которые могут быть вложены в другие всплывающие элементы.
</div>

Всплывающие элементы связаны с определенными элементами управления. Вы можете использовать свойство [Placement](https://msdn.microsoft.com/en-us/library/windows/apps/windows.ui.xaml.controls.primitives.flyoutbase.placement.aspx) для указания места отображения всплывающего элемента: вверху, слева, внизу, справа или в центре. Если выбрать [полный режим размещения](https://msdn.microsoft.com/en-us/library/windows/apps/windows.ui.xaml.controls.primitives.flyoutplacementmode.aspx), приложение растягивает всплывающий элемент или размещает его в центре окна приложения. Если они видны, их необходимо прикрепить к вызывающему объекту и указать их предпочтительное расположение относительно объекта: сверху, слева, снизу или справа. Всплывающий элемент также имеет полный режим размещения, при котором всплывающий элемент растягивается на весь экран и выравнивается по центру внутри окна приложения. Некоторые элементы управления, например [Кнопка](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.button.aspx), содержат свойство [Flyout](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.button.flyout.aspx), которое можно использовать для привязки всплывающего элемента. 

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

Если элемент управления лишен свойства "Flyout", вы можете использовать присоединенное свойство [FlyoutBase.AttachedFlyout](https://msdn.microsoft.com/en-us/library/windows/apps/windows.ui.xaml.controls.primitives.flyoutbase.attachedflyout.aspx). При этом также следует вызвать метод [FlyoutBase.ShowAttachedFlyout](https://msdn.microsoft.com/en-us/library/windows/apps/windows.ui.xaml.controls.primitives.flyoutbase.showattachedflyout) для отображения всплывающего элемента. 

В этом примере к изображению добавляется простой всплывающий элемент. Когда пользователь нажимает на изображение, приложение отображает всплывающий элемент. 

````xaml
<Image Source="Assets/cliff.jpg" Width="50" Height="50" 
  Margin="10" Tapped="Image_Tapped">
  <FlyoutBase.AttachedFlyout>
    <Flyout>
      <TextBlock TextWrapping="Wrap" Text="This is some text in a flyout."  />
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
        <Flyout.FlyoutPresenterStyle>
            <Style TargetType="FlyoutPresenter">
                <Setter Property="ScrollViewer.ZoomMode" Value="Enabled"/>
                <Setter Property="Background" Value="Black"/>
                <Setter Property="BorderBrush" Value="Gray"/>
                <Setter Property="BorderThickness" Value="5"/>
                <Setter Property="MinHeight" Value="300"/>
                <Setter Property="MinWidth" Value="300"/>
            </Style>
        </Flyout.FlyoutPresenterStyle>
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

### <a name="style-a-flyout"></a>Стилизация всплывающего элемента
Для стилизации всплывающего элемента изменяется его свойство [FlyoutPresenterStyle](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.flyout.flyoutpresenterstyle.aspx). В этом примере показан абзац создания программы-оболочки текста и делает блок текста доступным для программ чтения с экрана.

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

## <a name="get-the-samples"></a>Получение примеров
*   [Основы создания пользовательского интерфейса XAML](https://github.com/Microsoft/Windows-universal-samples/blob/master/Samples/XamlUIBasics)<br/>
    Ознакомьтесь со всеми элементами управления XAML в интерактивном формате.

## <a name="related-articles"></a>Связанные разделы
- [Подсказки](tooltips.md)
- [Меню и контекстное меню](menus.md)
- [**Класс Flyout**](https://msdn.microsoft.com/library/windows/apps/dn279496)
- [**Класс ContentDialog**](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.contentdialog.aspx)


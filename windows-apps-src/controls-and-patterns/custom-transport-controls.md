---
author: Jwmsft
Description: "Проигрыватель мультимедиа имеет настраиваемые элементы управления транспортировкой XAML для управления аудио- и видеосодержимым."
title: "Создание пользовательских элементов управления транспортировкой мультимедиа"
ms.assetid: 6643A108-A6EB-42BC-B800-22EABD7B731B
label: Create custom media transport controls
template: detail.hbs
ms.sourcegitcommit: a4e9a90edd2aae9d2fd5d7bead948422d43dad59
ms.openlocfilehash: 5500f41b254b32b8d293181fba3acebbfffa90e7

---
# Создание пользовательских элементов управления транспортировкой

Класс MediaElement обладает настраиваемыми элементами управления транспортировкой XAML, позволяющими контролировать элементы управления аудио- и видеосодержимым в приложении универсальной платформы Windows (UWP). Здесь мы покажем, как настроить шаблон MediaTransportControls. Мы покажем, как работать с меню переполнения, добавлять пользовательские кнопки, модифицировать ползунок и изменять цвета.

Перед началом необходимо ознакомиться с классами MediaElement и MediaTransportControls. См. подробные сведения в руководстве по элементу управления MediaElement. 

> 
            **Подсказка**
            &nbsp;&nbsp;Примеры в этом разделе основаны на [примере элементов управления транспортировкой мультимедиа](http://go.microsoft.com/fwlink/p/?LinkId=620023). Вы можете скачать пример для просмотра и запуска полного кода.

<span class="sidebar_heading" style="font-weight: bold;">Важные API</span>

-   [**MediaElement**](https://msdn.microsoft.com/library/windows/apps/br242926)
-   [**MediaElement.AreTransportControlsEnabled**](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.mediaelement.aretransportcontrolsenabled.aspx)
-   [**MediaTransportControls**](https://msdn.microsoft.com/library/windows/apps/dn278677)

## Когда нужно настраивать шаблон?


            **MediaElement** обладает встроенными элементами управления транспортировкой, которые без каких-либо модификаций хорошо совместимы с большинством приложений для воспроизведения аудио и видео. Они предоставлены классом [**MediaTransportControls**](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.mediatransportcontrols.aspx) и имеют кнопки для воспроизведения, остановки, навигации по мультимедиа, регулировки громкости, переключения в полноэкранный режим, трансляции на второе устройство, включения заголовков, переключения звуковых дорожек и регулировки скорости воспроизведения. Класс MediaTransportControls имеет свойства, которые позволят вам отслеживать, отображается ли каждая кнопка и включена ли она. Можно также задать свойство [**IsCompact**](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.mediatransportcontrols.iscompact.aspx), чтобы указать, отображаются ли элементы управления в одну строку или в две.

Однако возможны сценарии, при которых необходимо настроить внешний вид элемента управления или изменить его поведение. Вот несколько примеров:
- Изменить значки, поведение ползунка и цвета.
- Переместить не часто используемые кнопки команд в меню переполнения.
- Изменить порядок, в котором команды раскрываются при изменении размеров элемента управления.
- Предоставить кнопку команды, не включенную в набор по умолчанию.

Вы можете настроить вид элемента управления с помощью изменения стандартного шаблона. Чтобы изменить поведение элемента управления либо добавить новые команды, можно создать производный от MediaTransportControls пользовательский элемент управления.

>
            **Подсказка**
            &nbsp;&nbsp;Настраиваемые шаблоны элементов управления — полезная функциональность платформы XAML, но необходимо принимать во внимание некоторые последствия их использования. При настройке шаблон становится статической частью приложения и потому не получает обновления платформы, которые Майкрософт вносит в шаблон. Если обновление шаблона выполнено компанией Майкрософт, вы должны взять новый шаблон и заново изменить его, чтобы воспользоваться преимуществами обновленного шаблона.

## Структура шаблона

Элемент [**ControlTemplate**](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.controltemplate.aspx) входит в состав стиля по умолчанию. Стиль по умолчанию элемента управления транспортировкой показан в разделе [**MediaTransportControls**](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.mediatransportcontrols.aspx). Вы можете скопировать этот стиль по умолчанию в ваш проект и изменить его. Шаблон ControlTemplate состоит из разделов, которые аналогичны другим шаблонам элементов управления XAML.
- Первый раздел шаблона содержит определения [**Style**](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.style.aspx) для различных компонентов MediaTransportControls.
- Во втором разделе определены различные визуальные состояния, используемые в MediaTransportControls.
- Третий раздел содержит сетку [**Grid**](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.grid.aspx), которая объединяет различные элементы MediaTransportControls и определяет расположение компонентов относительно друг друга.

> 
            **Примечание**
            &nbsp;&nbsp;Дополнительные сведения об изменении шаблонов см. в разделе [Шаблоны элементов управления](). Для открытия файлов XAML, расположенных в папке \(*Program Files*)\Windows Kits\10\DesignTime\CommonConfiguration\Neutral\UAP\\(*SDK version*)\Generic, можно использовать текстовый редактор или похожие программы редактирования из вашей среды разработки. Стиль и шаблон по умолчанию для каждого элемента управления определяются в файле **generic.xaml**. Чтобы найти шаблон MediaTransportControls в файле generic.xaml, используйте строку поиска «MediaTransportControls».

В следующих разделах вы научитесь настраивать ряд основных элементов управления транспортировкой: 
- 
            [
              **Slider**
            ](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.slider.aspx): позволяет вручную управлять воспроизведением мультимедиа и показывает ход выполнения
- 
            [
              **CommandBar**
            ](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.commandbar.aspx): содержит все кнопки.
Дополнительные сведения см. в разделе «Анатомия» справочного раздела по MediaTransportControls. 

## Настройка элементов управления транспортировкой

Если вы хотите изменить только вид MediaTransportControls, вы можете просто создать копию стиля и шаблона элемента управления по умолчанию и затем изменить ее. Однако если вы хотите добавить или изменить функционал элемента управления, необходимо создать новый класс, производный от MediaTransportControls.

### Изменение шаблона элемента управления

**Настройка стандартного стиля и шаблона MediaTransportControls**
1. Скопируйте стиль по умолчанию из раздела «Стили и шаблоны MediaTransportControls» в класс ResourceDictionary в своем проекте.
2. Для определения стиля присвойте ему значение x:Key, как показано далее. 
```xaml
<Style TargetType="MediaTransportControls" x:Key="myTransportControlsStyle">
    <!-- Style content ... -->
</Style>
```
3. Добавьте MediaElement с MediaTransportControls в пользовательский интерфейс.
4. Укажите для свойства Style элемента MediaTransportControls ваш пользовательский ресурс Style, как показано ниже. 
```xaml
<MediaElement AreTransportControlsEnabled="True">
    <MediaElement.TransportControls>
        <MediaTransportControls Style="{StaticResource myTransportControlsStyle}"/>
    </MediaElement.TransportControls>
</MediaElement>
```

Подробнее об изменении стилей и шаблонов см. в статьях [Настройка стиля элементов управления]() и [Шаблоны элементов управления]().

### Создание производного элемента управления

Чтобы добавить или изменить функционал элементов управления транспортировкой, необходимо создать новый класс, производный от MediaTransportControls. Производный класс под названием `CustomMediaTransportControls` показан в [примере Media Transport Controls](http://go.microsoft.com/fwlink/p/?LinkId=620023) и других примерах на этой странице.

**Создание нового класса, производного от MediaTransportControls**
1. Добавьте новый файл класса в проект.
    - В Visual Studio выберите элементы Проект > Добавить класс. Откроется диалоговое окно Добавление нового элемента.
    - В диалоговом окне «Добавление нового элемента» введите имя файла класса и нажмите кнопку «Добавить». (В примере Media Transport Controls класс имеет имя `CustomMediaTransportControls`.)
2. Измените код класса, чтобы он был производным от класса MediaTransportControls.
```csharp
public sealed class CustomMediaTransportControls : MediaTransportControls
{
}
```
3. Скопируйте стиль по умолчанию для [**MediaTransportControls**](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.mediatransportcontrols.aspx) в класс [ResourceDictionary](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.resourcedictionary.aspx) вашего проекта. Это стиль и шаблон, которые вы изменяете.
(В примере Media Transport Controls создается новая папка с именем Themes и в нее добавляется файл ResourceDictionary с именем generic.xaml.)
4. Измените значение свойства [**TargetType**](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.style.targettype.aspx) стиля на новый тип пользовательского элемента управления. (В примере значение TargetType меняется на `local:CustomMediaTransportControls`.)
```xaml
xmlns:local="using:CustomMediaTransportControls">
...
<Style TargetType="local:CustomMediaTransportControls">
```
5. Задайте значение свойства [**DefaultStyleKey**](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.control.defaultstylekey.aspx) своего пользовательского класса. Это указывает вашему пользовательскому классу использовать стиль со свойством TargetType, равным `local:CustomMediaTransportControls`.
```csharp
public sealed class CustomMediaTransportControls : MediaTransportControls
{
    public CustomMediaTransportControls()
    {
        this.DefaultStyleKey = typeof(CustomMediaTransportControls);
    }
}
```
6. Добавьте [**MediaElement**](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.mediaelement.aspx) к разметке XAML, а затем добавьте к ней пользовательские элементы управления транспортировкой. Обратите внимание, что API для скрытия, отображения, отключения и включения кнопок по умолчанию по-прежнему работают с пользовательским шаблоном.
```xaml
<MediaElement Name="MediaElement1" AreTransportControlsEnabled="True" Source="video.mp4">
    <MediaElement.TransportControls>
        <local:CustomMediaTransportControls x:Name="customMTC"
                                            IsFastForwardButtonVisible="True"
                                            IsFastForwardEnabled="True"
                                            IsFastRewindButtonVisible="True"
                                            IsFastRewindEnabled="True"
                                            IsPlaybackRateButtonVisible="True"
                                            IsPlaybackRateEnabled="True"
                                            IsCompact="False">
        </local:CustomMediaTransportControls>
    </MediaElement.TransportControls>
</MediaElement>
```
Теперь вы можете изменить стиль и шаблон элемента управления для обновления внешнего вида вашего пользовательского элемента управления и управляющего кода для обновления его поведения.

### Работа с меню переполнения

Вы можете переместить кнопки команд MediaTransportControls в меню переполнения — таким образом, не часто используемые команды будут скрыты до тех пор, пока пользователю они не понадобятся.

В шаблоне MediaTransportControls кнопки команд содержатся в элементе [**CommandBar**](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.commandbar.aspx). Панель команд базируется на концепции главных и вспомогательных команд. Основные команды — это кнопки, которые отображаются в элементе управления по умолчанию и всегда видны (если не отключить или не скрыть кнопку). Вспомогательные команды находятся в меню переполнения, которое отображается, когда пользователь нажимает на кнопку с многоточием (…) Подробнее см. в статье, посвященной [панели приложения и панели команд](app-bars.md).

Чтобы переместить элемент из раздела главных команд панели команд в меню переполнения, необходимо изменить шаблон элемента управления XAML. 

**Перемещение команды в меню переполнения**
1. В шаблоне элемента управления найдите элемент CommandBar с именем `MediaControlsCommandBar`.
2. Добавьте раздел [**SecondaryCommands**](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.commandbar.secondarycommands.aspx) к коду XAML элемента CommandBar. Поместите его после закрывающего тега для [**PrimaryCommands**](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.commandbar.primarycommands.aspx). 
```xaml
<CommandBar x:Name="MediaControlsCommandBar" ... >  
  <CommandBar.PrimaryCommands>
...
    <AppBarButton x:Name='PlaybackRateButton'
                    Style='{StaticResource AppBarButtonStyle}'
                    MediaTransportControlsHelper.DropoutOrder='4'
                    Visibility='Collapsed'>
      <AppBarButton.Icon>
        <FontIcon Glyph="&#xEC57;"/>
      </AppBarButton.Icon>
    </AppBarButton>
...
  </CommandBar.PrimaryCommands>
<!-- Add secondary commands (overflow menu) here -->
  <CommandBar.SecondaryCommands>
    ...
  </CommandBar.SecondaryCommands>
</CommandBar>
```
3. Для заполнения меню командами нужно вырезать и вставить XAML для нужных объектов [**AppBarButton**](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.appbarbutton.aspx) с PrimaryCommands в SecondaryCommands. В этом примере мы перемещаем `PlaybackRateButton` в меню переполнения.

4. Добавьте подпись к кнопке и удалите информацию о стиле, как показано ниже.
Поскольку меню переполнения состоит из кнопок текста, вы должны добавить текстовую подпись к кнопке, а также удалить стиль, который задает высоту и ширину кнопки. В противном случае кнопка не будет правильно отображаться в меню переполнения.
```xaml
<CommandBar.SecondaryCommands>
    <AppBarButton x:Name='PlaybackRateButton'
                  Label='Playback Rate'>
    </AppBarButton>
</CommandBar.SecondaryCommands>
```

> 
            **Важно**
            &nbsp;&nbsp;Чтобы использовать кнопку в меню переполнения, нужно сделать ее видимой и включить. В этом примере элемент PlaybackRateButton является невидимым в меню переполнения, если свойство IsPlaybackRateButtonVisible не имеет значение true. Она не будет включена, если свойство IsPlaybackRateEnabled не имеет значение true. Установка этих свойств была показана в предыдущем разделе.

### Добавление пользовательской кнопки

Одной из причин, по которой может потребоваться настройка MediaTransportControls, является добавление пользовательской команды к элементу управления. Независимо от того, добавляете ли вы ее как основную команду или как второстепенную, процедуры создания кнопки команды и изменения ее поведение одинаковы. В [примере Media Transport Controls](http://go.microsoft.com/fwlink/p/?LinkId=620023) кнопка оценки добавляется к основным командам. 

**Добавление пользовательской кнопки команды**
1. Создайте объект AppBarButton и добавьте его в CommandBar в шаблоне элемента управления. 
```xaml
<AppBarButton x:Name="LikeButton" 
              Icon="Like" 
              Style="{StaticResource AppBarButtonStyle}" 
              MediaTransportControlsHelper.DropoutOrder="3"
              VerticalAlignment="Center" />
```
    You must add it to the CommandBar in the appropriate location. (For more info, see the Working with the overflow menu section.) How it's positioned in the UI is determined by where the button is in the markup. For example, if you want this button to appear as the last element in the primary commands, add it at the very end of the primary commands list.
    
    You can also customize the icon for the button. For more info, see the [**AppBarButton**](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.appbarbutton.aspx) reference.

2. В переопределении метода [**OnApplyTemplate**](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.frameworkelement.onapplytemplate.aspx) получите кнопку из шаблона и зарегистрируйте обработчик для события [**Click**](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.primitives.buttonbase.click.aspx) этой кнопки. Этот код перейдет в класс `CustomMediaTransportControls`. 
```csharp
public sealed class CustomMediaTransportControls :  MediaTransportControls
{
    // ...

    protected override void OnApplyTemplate() 
    { 
        // Find the custom button and create an event handler for its Click event. 
        var likeButton = GetTemplateChild("LikeButton") as Button; 
        likeButton.Click += LikeButton_Click; 
        base.OnApplyTemplate(); 
    } 

    //...
}
```

3. Добавьте код в обработчик события Click, который будет выполнять действия, происходящие при нажатии кнопки.
Ниже приведен полный код класса.
```csharp
public sealed class CustomMediaTransportControls : MediaTransportControls
{
    public event EventHandler< EventArgs> Liked;

    public CustomMediaTransportControls() 
    {
        this.DefaultStyleKey = typeof(CustomMediaTransportControls);
    }

    protected override void OnApplyTemplate()
    {
        // Find the custom button and create an event handler for its Click event. 
        var likeButton = GetTemplateChild("LikeButton") as Button;
        likeButton.Click += LikeButton_Click;
        base.OnApplyTemplate();
    }

    private void LikeButton_Click(object sender, RoutedEventArgs e)
    {
        // Raise an event on the custom control when 'like' is clicked. 
        var handler = Liked;
        if (handler != null)
        {
            handler(this, EventArgs.Empty);
        }
    }
}
```

### Изменение ползунка

Навигация в MediaTransportControls обеспечивается элементом [**Slider**](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.slider.aspx). Один из способов его настройки — изменение точности навигации. 

По умолчанию ползунок поиска состоит из 100 частей, следовательно, поведение поиска ограничено данным количеством частей. Вы можете изменить точность навигации ползунка, получив элемент Slider из визуального дерева XAML в обработчике событий [**MediaOpened**](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.mediaelement.mediaopened.aspx). В этом примере показано, как использовать [**VisualTreeHelper**](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.media.visualtreehelper.aspx) для получения ссылки на элемент Slider, а затем изменить стандартный шаг перемещения ползунка с 1% до 0,1% (1000 шагов), если продолжительность мультимедиа превышает 120 минут. Элемент MediaElement имеет имя `MediaElement1`.

```csharp
private void MediaElement_MediaOpened(object sender, RoutedEventArgs e)
{
  FrameworkElement transportControlsTemplateRoot = (FrameworkElement)VisualTreeHelper.GetChild(MediaElement1.TransportControls, 0);
  Slider sliderControl = (Slider)transportControlsTemplateRoot.FindName("ProgressSlider");
  if (sliderControl != null && MediaElement1.NaturalDuration.TimeSpan.TotalMinutes > 120)
  {
    // Default is 1%. Change to 0.1% for more granular seeking.
    sliderControl.StepFrequency = 0.1;
  }
}
```



## Связанные разделы

- [Воспроизведение мультимедиа](media-playback.md)



<!--HONumber=Jun16_HO4-->



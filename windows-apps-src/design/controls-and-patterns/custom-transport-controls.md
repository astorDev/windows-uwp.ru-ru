---
Description: Проигрыватель мультимедиа имеет настраиваемые элементы управления транспортировкой XAML для управления аудио- и видеосодержимым.
title: Создание пользовательских элементов управления транспортировкой мультимедиа
ms.assetid: 6643A108-A6EB-42BC-B800-22EABD7B731B
label: Create custom media transport controls
template: detail.hbs
ms.date: 05/19/2017
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: 776ad2c3e5236f9ecee73ce35c818112862d4faf
ms.sourcegitcommit: 87fd0ec1e706a460832b67f936a3014f0877a88c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/12/2020
ms.locfileid: "83233713"
---
# <a name="create-custom-transport-controls"></a>Создание пользовательских элементов управления транспортировкой



MediaPlayerElement содержит настраиваемые элементы управления транспортировки XAML для управления аудио- и видео-содержимым в приложении для Windows. Здесь мы покажем, как настроить шаблон MediaTransportControls. Мы покажем, как работать с меню переполнения, добавлять пользовательские кнопки и модифицировать ползунок.

> **Важные API**: [MediaPlayerElement](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.mediaplayerelement), [MediaPlayerElement.AreTransportControlsEnabled](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.mediaplayerelement.aretransportcontrolsenabled) и [MediaTransportControls](https://docs.microsoft.com/uwp/api/Windows.Media.SystemMediaTransportControls)

Перед началом необходимо ознакомиться с классами MediaPlayerElement и MediaTransportControls. Подробнее см. в руководстве по элементу управления MediaPlayerElement.

> [!TIP]
> Примеры в этом разделе основаны на [Примере элементов управления транспортировкой мультимедиа](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/XamlCustomMediaTransportControls). Вы можете скачать пример для просмотра и запуска полного кода.

> [!NOTE]
> **MediaPlayerElement** доступен только в Windows 10 версии 1607 или выше. При разработке приложения для более ранней версии Windows 10 потребуется использовать [**MediaElement**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.MediaElement). Все примеры на этой странице также совместимы с **MediaElement**.

## <a name="when-should-you-customize-the-template"></a>Когда нужно настраивать шаблон?

**MediaPlayerElement** обладает встроенными элементами управления транспортировкой, которые без каких-либо модификаций хорошо совместимы с большинством приложений для воспроизведения аудио и видео. Они предоставлены классом [**MediaTransportControls**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.MediaTransportControls) и имеют кнопки для воспроизведения, остановки, навигации по мультимедиа, регулировки громкости, переключения в полноэкранный режим, трансляции на второе устройство, включения заголовков, переключения звуковых дорожек и регулировки скорости воспроизведения. Класс MediaTransportControls имеет свойства, которые позволят вам отслеживать, отображается ли каждая кнопка и включена ли она. Можно также задать свойство [**IsCompact**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.mediatransportcontrols.iscompact), чтобы указать, отображаются ли элементы управления в одну строку или в две.

Однако возможны сценарии, при которых необходимо настроить внешний вид элемента управления или изменить его поведение. Вот несколько примеров:
- Изменить значки, поведение ползунка и цвета.
- Переместить не часто используемые кнопки команд в меню переполнения.
- Изменить порядок, в котором команды раскрываются при изменении размеров элемента управления.
- Предоставить кнопку команды, не включенную в набор по умолчанию.

> [!NOTE]
> Кнопки на экране будут раскрываться из встроенных элементов управления транспортировкой в заранее определенном порядке, если на экране недостаточно места. Чтобы изменить этот порядок или разместить команды, не умещающиеся в меню переполнения, вам потребуется настроить элементы управления.

Вы можете настроить вид элемента управления с помощью изменения стандартного шаблона. Чтобы изменять поведение элемента управления либо добавить новые команды, вы можете создать пользовательский элемент управления, который является производным от MediaTransportControls.

> [!TIP]
> Настраиваемые шаблоны элементов управления — полезная функциональность платформы XAML, но необходимо принимать в расчет их последствия. При настройке шаблон становится статической частью приложения и потому не получает обновления платформы, которые Майкрософт вносит в шаблон. Если обновление шаблона выполнено компанией Майкрософт, вы должны взять новый шаблон и заново изменить его, чтобы воспользоваться преимуществами обновленного шаблона.

## <a name="template-structure"></a>Структура шаблона

Элемент [**ControlTemplate**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.ControlTemplate) входит в состав стиля по умолчанию. Вы можете скопировать этот стиль по умолчанию в ваш проект и изменить его. Шаблон ControlTemplate состоит из разделов, которые аналогичны другим шаблонам элементов управления XAML.
- Первый раздел шаблона содержит определения [**Style**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Style) для различных компонентов MediaTransportControls.
- Во втором разделе определены различные визуальные состояния, используемые в MediaTransportControls.
- Третий раздел содержит сетку [**Grid**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Grid), которая объединяет различные элементы MediaTransportControls и определяет расположение компонентов относительно друг друга.

> [!NOTE]
> Дополнительные сведения об изменении шаблонов см. в разделе [Шаблоны элементов управления](/windows/uwp/design/controls-and-patterns/control-templates). Для открытия файлов XAML, расположенных в папке \(*Program Files*)\Windows Kits\10\DesignTime\CommonConfiguration\Neutral\UAP\\(*SDK version*)\Generic, можно использовать текстовый редактор или похожие программы редактирования из вашей среды разработки. Стиль и шаблон по умолчанию для каждого элемента управления определяются в файле **generic.xaml**. Чтобы найти шаблон MediaTransportControls в файле generic.xaml, используйте строку поиска "MediaTransportControls".

В следующих разделах вы научитесь настраивать ряд основных элементов управления транспортировкой:
- [**Slider**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Slider): позволяет пользователю вручную управлять воспроизведением мультимедиа, а также показывает ход выполнения операции;
- [**CommandBar**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.CommandBar): содержит все необходимые кнопки.
Дополнительные сведения см. в разделе "Анатомия" справочного раздела по MediaTransportControls.

## <a name="customize-the-transport-controls"></a>Настройка элементов управления транспортировкой

Если вы хотите изменить только вид MediaTransportControls, вы можете просто создать копию стиля и шаблона элемента управления по умолчанию и затем изменить ее. Однако если вы хотите добавить или изменить функционал элемента управления, необходимо создать новый класс, производный от MediaTransportControls.

### <a name="re-template-the-control"></a>Изменение шаблона элемента управления

**Настройка стандартного стиля и шаблона MediaTransportControls**
1. Скопируйте стиль по умолчанию из раздела "Стили и шаблоны MediaTransportControls" в класс ResourceDictionary в своем проекте.
2. Для определения стиля присвойте ему значение x:Key, как показано далее.

```xaml
<Style TargetType="MediaTransportControls" x:Key="myTransportControlsStyle">
    <!-- Style content ... -->
</Style>
```

3. Добавьте MediaPlayerElement с MediaTransportControls в пользовательский интерфейс.
4. Укажите для свойства Style элемента MediaTransportControls ваш пользовательский ресурс Style, как показано ниже.

```xaml
<MediaPlayerElement AreTransportControlsEnabled="True">
    <MediaPlayerElement.TransportControls>
        <MediaTransportControls Style="{StaticResource myTransportControlsStyle}"/>
    </MediaPlayerElement.TransportControls>
</MediaPlayerElement>
```

Подробнее об изменении стилей и шаблонов см. в статьях [Настройка стиля элементов управления](/windows/uwp/design/controls-and-patterns/xaml-styles) и [Шаблоны элементов управления](/windows/uwp/design/controls-and-patterns/control-templates).

### <a name="create-a-derived-control"></a>Создание производного элемента управления

Чтобы добавить или изменить функционал элементов управления транспортировкой, необходимо создать новый класс, производный от MediaTransportControls. Производный класс под названием `CustomMediaTransportControls` показан в [примере Media Transport Controls](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/XamlCustomMediaTransportControls) и других примерах на этой странице.

**Создание нового класса, производного от MediaTransportControls**
1. Добавьте новый файл класса в проект.
    - В Visual Studio выберите элементы Проект > Добавить класс. Откроется диалоговое окно Добавление нового элемента.
    - В диалоговом окне "Добавление нового элемента" введите имя файла класса и нажмите кнопку "Добавить". (В примере Media Transport Controls класс имеет имя `CustomMediaTransportControls`.)
2. Измените код класса, чтобы он был производным от класса MediaTransportControls.

```csharp
public sealed class CustomMediaTransportControls : MediaTransportControls
{
}
```

3. Скопируйте стиль по умолчанию для [**MediaTransportControls**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.MediaTransportControls) в класс [ResourceDictionary](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.ResourceDictionary) вашего проекта. Это стиль и шаблон, которые вы изменяете.
(В примере Media Transport Controls создается новая папка с именем Themes и в нее добавляется файл ResourceDictionary с именем generic.xaml.)
4. Измените значение свойства [**TargetType**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.style.targettype) стиля на новый тип пользовательского элемента управления. (В примере значение TargetType меняется на `local:CustomMediaTransportControls`.)

```xaml
xmlns:local="using:CustomMediaTransportControls">
...
<Style TargetType="local:CustomMediaTransportControls">
```

5. Задайте значение свойства [**DefaultStyleKey**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.control.defaultstylekey) своего пользовательского класса. Это указывает вашему пользовательскому классу использовать стиль со свойством TargetType, равным `local:CustomMediaTransportControls`.

```csharp
public sealed class CustomMediaTransportControls : MediaTransportControls
{
    public CustomMediaTransportControls()
    {
        this.DefaultStyleKey = typeof(CustomMediaTransportControls);
    }
}
```

6. Добавьте [**MediaPlayerElement**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.mediaplayerelement) к разметке XAML, а затем добавьте к ней пользовательские элементы управления транспортировкой. Обратите внимание, что API для скрытия, отображения, отключения и включения кнопок по умолчанию по-прежнему работают с пользовательским шаблоном.

```xaml
<MediaPlayerElement Name="MediaPlayerElement1" AreTransportControlsEnabled="True" Source="video.mp4">
    <MediaPlayerElement.TransportControls>
        <local:CustomMediaTransportControls x:Name="customMTC"
                                            IsFastForwardButtonVisible="True"
                                            IsFastForwardEnabled="True"
                                            IsFastRewindButtonVisible="True"
                                            IsFastRewindEnabled="True"
                                            IsPlaybackRateButtonVisible="True"
                                            IsPlaybackRateEnabled="True"
                                            IsCompact="False">
        </local:CustomMediaTransportControls>
    </MediaPlayerElement.TransportControls>
</MediaPlayerElement>
```

Теперь вы можете изменить стиль и шаблон элемента управления для обновления внешнего вида вашего пользовательского элемента управления и управляющего кода для обновления его поведения.

### <a name="working-with-the-overflow-menu"></a>Работа с меню переполнения

Вы можете переместить кнопки команд MediaTransportControls в меню переполнения — таким образом, не часто используемые команды будут скрыты до тех пор, пока пользователю они не понадобятся.

В шаблоне MediaTransportControls кнопки команд содержатся в элементе [**CommandBar**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.CommandBar). Панель команд базируется на концепции главных и вспомогательных команд. Основные команды — это кнопки, которые отображаются в элементе управления по умолчанию и всегда видны (если не отключить или не скрыть кнопку, либо если недостаточно места). Вспомогательные команды находятся в меню переполнения, которое отображается, когда пользователь нажимает на кнопку с многоточием (…) Подробнее см. в статье, посвященной [панели приложения и панели команд](app-bars.md).

Чтобы переместить элемент из раздела главных команд панели команд в меню переполнения, необходимо изменить шаблон элемента управления XAML.

**Перемещение команды в меню переполнения**
1. В шаблоне элемента управления найдите элемент CommandBar с именем `MediaControlsCommandBar`.
2. Добавьте раздел [**SecondaryCommands**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.commandbar.secondarycommands) к коду XAML элемента CommandBar. Поместите его после закрывающего тега для [**PrimaryCommands**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.commandbar.primarycommands).

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

3. Для заполнения меню командами нужно вырезать и вставить XAML для нужных объектов [**AppBarButton**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.AppBarButton) с PrimaryCommands в SecondaryCommands. В этом примере мы перемещаем `PlaybackRateButton` в меню переполнения.

4. Добавьте подпись к кнопке и удалите информацию о стиле, как показано ниже.
Поскольку меню переполнения состоит из кнопок текста, вы должны добавить текстовую подпись к кнопке, а также удалить стиль, который задает высоту и ширину кнопки. В противном случае она не будет правильно отображаться в меню переполнения.

```xaml
<CommandBar.SecondaryCommands>
    <AppBarButton x:Name='PlaybackRateButton'
                  Label='Playback Rate'>
    </AppBarButton>
</CommandBar.SecondaryCommands>
```

> [!IMPORTANT]
> Вы должны сделать кнопку видимой и включить ее, чтобы использовать ее в меню переполнения. В этом примере элемент PlaybackRateButton является невидимым в меню переполнения, если свойство IsPlaybackRateButtonVisible не имеет значение true. Она не будет включена, если свойство IsPlaybackRateEnabled не имеет значение true. Установка этих свойств была показана в предыдущем разделе.

### <a name="adding-a-custom-button"></a>Добавление пользовательской кнопки

Одной из причин, по которой может потребоваться настройка MediaTransportControls, является добавление пользовательской команды к элементу управления. Независимо от того, добавляете ли вы ее как основную команду или как второстепенную, процедуры создания кнопки команды и изменения ее поведение одинаковы. В [примере Media Transport Controls](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/XamlCustomMediaTransportControls) кнопка оценки добавляется к основным командам.

**Добавление пользовательской кнопки команды**
1. Создайте объект AppBarButton и добавьте его в CommandBar в шаблоне элемента управления.

```xaml
<AppBarButton x:Name="LikeButton"
              Icon="Like"
              Style="{StaticResource AppBarButtonStyle}"
              MediaTransportControlsHelper.DropoutOrder="3"
              VerticalAlignment="Center" />
```

Добавьте его в CommandBar в соответствующем месте. (См. подробнее о работе с меню переполнения.) Его расположение в пользовательском интерфейсе определяется положением кнопки в разметке. Например, если требуется, чтобы эта кнопка отображалась в виде последнего элемента в основных командах, добавьте его в самом конце списка этих команд.

Можно также настроить значок для кнопки. См. подробнее в справочнике по <a href="https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.AppBarButton"><b>AppBarButton</b></a>.
    

2. В переопределении метода [**OnApplyTemplate**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.frameworkelement.onapplytemplate) получите кнопку из шаблона и зарегистрируйте обработчик для события [**Click**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.primitives.buttonbase.click) этой кнопки. Этот код перейдет в класс `CustomMediaTransportControls`.

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

**Пользовательские элементы управления транспортировкой мультимедиа с добавленной кнопкой "Нравится"** 
![Пользовательский элемент управления транспортировкой мультимедиа с дополнительной кнопкой "Нравится"](images/controls/mtc_double_custom_inprod.png)

### <a name="modifying-the-slider"></a>Изменение ползунка

Навигация в MediaTransportControls обеспечивается элементом [**Slider**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Slider). Один из способов его настройки — изменение точности навигации.

По умолчанию ползунок поиска состоит из 100 частей, следовательно, поведение поиска ограничено данным количеством частей. Вы можете изменить точность навигации ползунка, получив элемент Slider из визуального дерева XAML в обработчике событий [**MediaOpened**](https://docs.microsoft.com/uwp/api/windows.media.playback.mediaplayer.mediaopened) в [**MediaPlayerElement.MediaPlayer**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.mediaplayerelement). В этом примере показано, как использовать [**VisualTreeHelper**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.VisualTreeHelper) для получения ссылки на элемент Slider, а затем изменить стандартный шаг перемещения ползунка с 1 % до 0,1 % (1000 шагов), если продолжительность мультимедиа превышает 120 минут. Элемент MediaPlayerElement имеет имя `MediaPlayerElement1`.

```csharp
protected override void OnNavigatedTo(NavigationEventArgs e)
{
  MediaPlayerElement1.MediaPlayer.MediaOpened += MediaPlayerElement_MediaPlayer_MediaOpened;
  base.OnNavigatedTo(e);
}

private void MediaPlayerElement_MediaPlayer_MediaOpened(object sender, RoutedEventArgs e)
{
  FrameworkElement transportControlsTemplateRoot = (FrameworkElement)VisualTreeHelper.GetChild(MediaPlayerElement1.TransportControls, 0);
  Slider sliderControl = (Slider)transportControlsTemplateRoot.FindName("ProgressSlider");
  if (sliderControl != null && MediaPlayerElement1.NaturalDuration.TimeSpan.TotalMinutes > 120)
  {
    // Default is 1%. Change to 0.1% for more granular seeking.
    sliderControl.StepFrequency = 0.1;
  }
}
```
## <a name="related-articles"></a>Похожие статьи

- [Воспроизведение мультимедиа](media-playback.md)

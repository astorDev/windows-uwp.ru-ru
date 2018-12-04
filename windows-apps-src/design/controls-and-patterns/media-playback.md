---
Description: The media player is used to view and listen to video, audio, and images.
title: Проигрыватель мультимедиа
ms.assetid: 9AABB5DE-1D81-4791-AB47-7F058F64C491
dev.assetid: AF2F2008-9B53-430C-BBC3-8888F631B0B0
label: Media player
template: detail.hbs
ms.date: 05/19/2017
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: e8ed0c28199e49e9c4be69785a7af5985afae6a2
ms.sourcegitcommit: b4c502d69a13340f6e3c887aa3c26ef2aeee9cee
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/03/2018
ms.locfileid: "8483691"
---
# <a name="media-player"></a>Проигрыватель мультимедиа



Проигрыватель мультимедиа используется для просмотра и прослушивания видео и звука. Воспроизведение мультимедиа может быть встроенным (внедренным на страницу или в группу других элементов управления) либо выполняться в специальном полноэкранном режиме. Вы можете изменить набор кнопок проигрывателя и фон панели элементов управления, а также упорядочить макеты любым способом. Просто помните, что пользователи ожидают базовый набор элементов управления (кнопка воспроизведения/паузы, переход вперед, переход назад).

![Элемент проигрывателя мультимедиа с элементами управления транспортировкой](images/controls/mtc_double_video_inprod.png)

> **Важные API-интерфейсы**: [класс MediaPlayerElement](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.mediaplayerelement.aspx), [класс MediaTransportControls](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.mediatransportcontrols)


> [!NOTE]
> **MediaPlayerElement** доступен только в Windows 10 версии 1607 или выше. При разработке приложения для более ранней версии Windows 10 потребуется использовать [MediaElement](https://msdn.microsoft.com/library/windows/apps/br242926). Все рекомендации на этой странице применяются также и к элементу MediaElement.

## <a name="is-this-the-right-control"></a>Выбор правильного элемента управления

Для воспроизведения звука и видео в приложении используется проигрыватель мультимедиа. Для просмотра коллекции изображений используется [представление пролистывания](flipview.md).

## <a name="examples"></a>Примеры

<table>
<th align="left">Галерея элементов управления XAML<th>
<tr>
<td><img src="images/xaml-controls-gallery-sm.png" alt="XAML controls gallery"></img></td>
<td>
    <p>Если у вас установлено приложение <strong style="font-weight: semi-bold">галереи элементов управления XAML</strong>, щелкните здесь, чтобы открыть приложение и увидеть <a href="xamlcontrolsgallery:/item/MediaPlayerElement">MediaPlayerElement</a> или <a href="xamlcontrolsgallery:/item/MediaPlayer">MediaPlayer</a> в действии.</p>
    <ul>
    <li><a href="https://www.microsoft.com/store/productId/9MSVH128X2ZT">Скачать приложение галереи элементов управления XAML (Microsoft Store)</a></li>
    <li><a href="https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/XamlUIBasics">Получить исходный код (GitHub)</a></li>
    </ul>
</td>
</tr>
</table>

Элемент проигрывателя мультимедиа в приложении "Начало работы" для Windows 10.

![Элемент мультимедиа в приложении "Начало работы" для Windows 10.](images/control-examples/mtc_getstarted_example.png)

## <a name="create-a-media-player"></a>Создание проигрывателя мультимедиа
Чтобы добавить мультимедиа в свое приложение, создайте объект [MediaPlayerElement](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.mediaplayerelement.aspx) в XAML и задайте для параметра [Source](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.mediaplayerelement.source.aspx) значение [MediaSource](https://msdn.microsoft.com/library/windows/apps/windows.media.core.mediasource.aspx), указывающее на звуковой или видеофайл.

Этот код XAML создает объект [MediaPlayerElement](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.mediaplayerelement.aspx) и настраивает его свойство [Source](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.mediaplayerelement.source.aspx) на URI видеофайла, который является локальным относительно приложения. Объект **MediaPlayerElement** начинает воспроизведение в момент загрузки страницы. Чтобы заблокировать воспроизведение мультимедиа сразу, можно установить для свойства [AutoPlay](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.mediaplayerelement.autoplay.aspx) значение **false**.

```xaml
<MediaPlayerElement x:Name="mediaSimple"
                    Source="Videos/video1.mp4"
                    Width="400" AutoPlay="True"/>
```

Этот XAML создает объект [MediaPlayerElement](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.mediaplayerelement.aspx), у которого включены встроенные элементы управления транспортировкой, а для свойства [AutoPlay](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.mediaplayerelement.autoplay.aspx) установлено значение **false.**


```xaml
<MediaPlayerElement x:Name="mediaPlayer"
                    Source="Videos/video1.mp4"
                    Width="400"
                    AutoPlay="False"
                    AreTransportControlsEnabled="True"/>
```

### <a name="media-transport-controls"></a>Элементы управления транспортировкой мультимедиа
[MediaPlayerElement](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.mediaplayerelement.aspx) содержит встроенные элементы управления транспортировкой, которые контролируют воспроизведение, остановку, паузу, громкость, выключение звука, поиск и отображение хода выполнения, скрытые субтитры, а также выбор звуковых дорожек. Чтобы включить эти элементы управления, установите для свойства [AreTransportControlsEnabled](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.mediaplayerelement.AreTransportControlsEnabled.aspx) значение **true**. Чтобы отключить их, установите для свойства **AreTransportControlsEnabled** значение **false**. Элементы управления транспортировкой представлены классом [MediaTransportControls](https://msdn.microsoft.com/library/windows/apps/dn831962). Вы можете использовать элементы управления транспортировкой как есть или настраивать их различными способами. Подробнее см. в справочнике по классам [MediaTransportControls](https://msdn.microsoft.com/library/windows/apps/dn831962) и в разделе [Создание пользовательских элементов управления транспортировкой](custom-transport-controls.md).

Элементы управления транспортировкой поддерживает макеты с одной и двумя строками. Первый пример — это однострочный макет с кнопкой "Воспроизведение/пауза", расположенной слева от временной шкалы мультимедиа. Этот макет лучше всего подходит для встроенного воспроизведения мультимедиа и компактных экранов.

![Пример элементов управления MTC, одна строка](images/controls/mtc_single_inprod_02.png)

Макет элементов управления из двух строк (показан ниже) рекомендуется для большинства сценариев использования, особенно на больших экранах. Этот макет предоставляет больше места для элементов управления и упрощает работу пользователя с временной шкалой.

![Пример элементов управления MTC на телефоне, две строки](images/controls/mtc_double_inprod.png)

**Системные элементы управления передачей мультимедиа**

[MediaPlayerElement](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.mediaplayerelement.aspx) автоматически интегрируется с системными элементами управления транспортировкой мультимедиа. Системные элементы управления транспортировкой мультимедиа открываются при нажатии клавиши на мультимедийном оборудовании, например, клавиш мультимедиа на клавиатуре. Дополнительные сведения см. в разделе [SystemMediaTransportControls](https://msdn.microsoft.com/library/windows/apps/dn278677).

> **Примечание.**&nbsp;&nbsp; Элемент [MediaElement](https://msdn.microsoft.com/library/windows/apps/br242926) не интегрируется автоматически с системными элементами управления транспортировкой мультимедиа, поэтому их нужно подключить самостоятельно. Дополнительные сведения см. в разделе [System Media Transport Controls](https://msdn.microsoft.com/library/windows/apps/mt228338).


### <a name="set-the-media-source"></a>Задание источника мультимедиа
Для воспроизведения файлов, находящихся в сети или входящих в состав приложения, достаточно указать в свойстве [Source](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.mediaplayerelement.source.aspx) значение [MediaSource](https://msdn.microsoft.com/library/windows/apps/windows.media.core.mediasource.aspx) с путем к файлу.

**Совет**открывать файлы из Интернета, необходимо объявить возможность **Интернет (клиент)** в манифесте приложения (Package.appxmanifest). Подробнее об объявлении возможностей см. в разделе [Объявления возможностей приложения](https://msdn.microsoft.com/library/windows/apps/mt270968).

 

Этот код пытается установить в свойстве [Source](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.mediaplayerelement.source.aspx) объекта [MediaPlayerElement](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.mediaplayerelement.aspx), определенного в XAML, путь к файлу, введенный в поле [TextBox](https://msdn.microsoft.com/library/windows/apps/br209683).

```xaml
<TextBox x:Name="txtFilePath" Width="400"
         FontSize="20"
         KeyUp="TxtFilePath_KeyUp"
         Header="File path"
         PlaceholderText="Enter file path"/>
```

```csharp
private void TxtFilePath_KeyUp(object sender, KeyRoutedEventArgs e)
{
    if (e.Key == Windows.System.VirtualKey.Enter)
    {
        TextBox tbPath = sender as TextBox;

        if (tbPath != null)
        {
            LoadMediaFromString(tbPath.Text);
        }
    }
}

private void LoadMediaFromString(string path)
{
    try
    {
        Uri pathUri = new Uri(path);
        mediaPlayer.Source = MediaSource.CreateFromUri(pathUri);
    }
    catch (Exception ex)
    {
        if (ex is FormatException)
        {
            // handle exception.
            // For example: Log error or notify user problem with file
        }
    }
}
```

Чтобы задать файл, встроенный в приложение, в качестве источника мультимедиа, инициализируйте объект [Uri](https://msdn.microsoft.com/library/windows/apps/br226017) с использованием пути, начинающегося с **ms-appx:///**, создайте объект [MediaSource](https://msdn.microsoft.com/library/windows/apps/windows.media.core.mediasource.aspx) с этим Uri и задайте его в качестве объекта [Source](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.mediaplayerelement.source.aspx). Например, для файла **video1.mp4**, расположенного во вложенной папке **Videos**, путь будет выглядеть так: **ms-appx:///Videos/video1.mp4**

Этот код присваивает свойству [Source](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.mediaplayerelement.source.aspx) объекта [MediaPlayerElement](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.mediaplayerelement.aspx), заданного раньше в XAML, значение **ms-appx:///Videos/video1.mp4**.

```csharp
private void LoadEmbeddedAppFile()
{
    try
    {
        Uri pathUri = new Uri("ms-appx:///Videos/video1.mp4");
        mediaPlayer.Source = MediaSource.CreateFromUri(pathUri);
    }
    catch (Exception ex)
    {
        if (ex is FormatException)
        {
            // handle exception.
            // For example: Log error or notify user problem with file
        }
    }
}
```

### <a name="open-local-media-files"></a>Открытие локальных файлов мультимедиа
Чтобы открыть файлы в локальной системе или из OneDrive, можно использовать объект [FileOpenPicker](https://msdn.microsoft.com/library/windows/apps/br207847) для получения файла и метод [Source](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.mediaplayerelement.source.aspx) для установки источника мультимедиа. Также вы можете получить доступ к пользовательским папкам мультимедиа программным способом.

Если приложению нужен доступ без взаимодействия с пользователем к папкам **Музыка** или **Видео** (например, если вы перечисляете все файлы музыки и видео в коллекции пользователя и отображаете их в своем приложении), то необходимо объявить возможности **Music Library** и **Video Library**. Дополнительные сведения см. в статье [Файлы и папки в библиотеках музыки, изображений и видео](https://msdn.microsoft.com/library/windows/apps/mt188703).

[FileOpenPicker](https://msdn.microsoft.com/library/windows/apps/br207847) не требует специальных возможностей для доступа к файлам в локальной файловой системе, таким как папки **Музыка** или **Видео** пользователя, поскольку пользователь может полностью контролировать, к какому файлу выполняется доступ. С точки зрения безопасности и конфиденциальности лучше свести к минимуму число возможностей, используемых приложением.

**Открытие локального мультимедиа с помощью FileOpenPicker**

1.  Вызовите [FileOpenPicker](https://msdn.microsoft.com/library/windows/apps/br207847), чтобы пользователь мог выбрать файл мультимедиа.

    Вызовите класс [FileOpenPicker](https://msdn.microsoft.com/library/windows/apps/br207847), чтобы выбрать файл мультимедиа. Задайте значение [FileTypeFilter](https://msdn.microsoft.com/library/windows/apps/br207850), чтобы указать, какой тип файлов отображает **FileOpenPicker**. Вызовите метод [PickSingleFileAsync](https://msdn.microsoft.com/library/windows/apps/jj635275), чтобы открыть окно выбора файла и получить файл.

2.  Используйте [MediaSource](https://msdn.microsoft.com/library/windows/apps/windows.media.core.mediasource.aspx), чтобы установить выбранный файл мультимедиа в качестве [MediaPlayerElement.Source](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.mediaplayerelement.source.aspx).

    Чтобы использовать файл [StorageFile](https://msdn.microsoft.com/library/windows/apps/br227171), возвращенный объектом [FileOpenPicker](https://msdn.microsoft.com/library/windows/apps/br207847), потребуется вызвать метод [CreateFromStorageFile](https://msdn.microsoft.com/library/windows/apps/windows.media.core.mediasource.createfromstoragefile.aspx) в [MediaSource](https://msdn.microsoft.com/library/windows/apps/windows.media.core.mediasource.aspx) и задать его в качестве объекта [Source](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.mediaplayerelement.source.aspx) для [MediaPlayerElement](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.mediaplayerelement.aspx). После этого вы можете вызвать метод [Play](https://msdn.microsoft.com/library/windows/apps/windows.media.playback.mediaplayer.play.aspx) для объекта [MediaPlayerElement.MediaPlayer](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.mediaplayerelement.mediaplayer.aspx), чтобы запустить мультимедиа.


В этом примере продемонстрировано использование [FileOpenPicker](https://msdn.microsoft.com/library/windows/apps/br207847) для выбора файла и задания файла в качестве свойства [Source](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.mediaplayerelement.source.aspx) элемента [MediaPlayerElement](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.mediaplayerelement.aspx).

```xaml
<MediaPlayerElement x:Name="mediaPlayer"/>
...
<Button Content="Choose file" Click="Button_Click"/>
```

```csharp
private async void Button_Click(object sender, RoutedEventArgs e)
{
    await SetLocalMedia();
}

async private System.Threading.Tasks.Task SetLocalMedia()
{
    var openPicker = new Windows.Storage.Pickers.FileOpenPicker();

    openPicker.FileTypeFilter.Add(".wmv");
    openPicker.FileTypeFilter.Add(".mp4");
    openPicker.FileTypeFilter.Add(".wma");
    openPicker.FileTypeFilter.Add(".mp3");

    var file = await openPicker.PickSingleFileAsync();

    // mediaPlayer is a MediaPlayerElement defined in XAML
    if (file != null)
    {
        mediaPlayer.Source = MediaSource.CreateFromStorageFile(file);

        mediaPlayer.MediaPlayer.Play();
    }
}
```

### <a name="set-the-poster-source"></a>Задание источника плаката
Вы можете использовать свойство [PosterSource](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.mediaplayerelement.PosterSource.aspx), чтобы дать элементу [MediaPlayerElement](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.mediaplayerelement.aspx) визуальное представление до загрузки мультимедиа. **PosterSource** — это изображение, например снимок экрана или киноафиша, отображаемые вместо мультимедиа. **PosterSource** отображается в следующих случаях.

-   Если действительный источник не установлен. Например, [Source](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.mediaplayerelement.source.aspx) не установлен, для **Source** было установлено значение **NULL**, либо источник недопустим (как в случае возникновения события [MediaFailed](https://msdn.microsoft.com/library/windows/apps/windows.media.playback.mediaplayer.mediafailed.aspx)).
-   В ходе загрузки мультимедиа. Например, установлен действительный источник, но событие [MediaOpened](https://msdn.microsoft.com/library/windows/apps/windows.media.playback.mediaplayer.mediaopened.aspx) еще не произошло.
-   При потоковой передаче мультимедиа на другое устройство.
-   Если мультимедиа — только звук.

Вот элемент [MediaPlayerElement](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.mediaplayerelement.aspx), для параметра [Source](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.mediaplayerelement.source.aspx) которого установлена запись альбома, а для параметра [PosterSource](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.mediaplayerelement.PosterSource.aspx) установлена титульная страница альбома.

```xaml
<MediaPlayerElement Source="Media/Track1.mp4" PosterSource="Media/AlbumCover.png"/>
```

### <a name="keep-the-devices-screen-active"></a>Поддержание активности экрана устройства
Обычно устройства затемняют и затем отключают дисплей, чтобы увеличить время работы батареи, когда пользователь не совершает никаких операций. Но приложения для воспроизведения видео должны предотвращать отключение дисплея, чтобы пользователи могли смотреть видео. Чтобы предотвратить отключение дисплея, когда пользователь не работает (например, при воспроизведении видео), можно вызвать [DisplayRequest.RequestActive](https://msdn.microsoft.com/library/windows/apps/br241818). С помощью класса [DisplayRequest](https://msdn.microsoft.com/library/windows/apps/br241816) можно сообщить Windows, что дисплей не должен отключаться, чтобы пользователь мог смотреть видео.

Чтобы не отправлять запросы дисплею, если это больше не требуется, а также для экономии энергии и уровня заряда батареи, необходимо вызвать [DisplayRequest.RequestRelease](https://msdn.microsoft.com/library/windows/apps/br241819). Windows автоматически деактивирует активные запросы дисплея вашего приложения, когда оно удаляется с экрана, и снова активирует их, когда приложение возвращается на передний план.

Вот некоторые из ситуаций, при которых необходимо высвобождать запросы отображения:

-   Воспроизведение видео приостановлено. Например, действием пользователя, буферизацией или из-за ограниченной пропускной способности сети.
-   Воспроизведение остановлено. Например, файл видео закончился или презентация завершена.
-   Произошла ошибка воспроизведения. Например, из-за проблем подключения к сети или поврежденного файла.

> **Примечание.**&nbsp;&nbsp; Если для объекта [MediaPlayerElement.IsFullWindow](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.mediaplayerelement.IsFullWindow.aspx) задано значение true и воспроизводится мультимедиа, дисплей не отключается автоматически.

**Поддержание активности экрана**

1.  Создайте глобальную переменную [DisplayRequest](https://msdn.microsoft.com/library/windows/apps/br241816). Инициализируйте ее значением NULL.
```csharp
// Create this variable at a global scope. Set it to null.
private DisplayRequest appDisplayRequest = null;
```

2.  Вызовите метод [RequestActive](https://msdn.microsoft.com/library/windows/apps/br241818), чтобы уведомить Windows о том, что приложение требует не отключать дисплей.

3.  Вызовите метод [RequestRelease](https://msdn.microsoft.com/library/windows/apps/br241819), чтобы отменить запрос дисплея, если воспроизведение остановлено, приостановлено или прервано из-за ошибки воспроизведения. Если у вашего приложения больше нет активных запросов дисплея, Windows уменьшает яркость дисплея (и затем отключает его), чтобы увеличить время работы батареи, когда устройством не пользуются.

    Каждый объект [MediaPlayerElement.MediaPlayer](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.mediaplayerelement.mediaplayer.aspx) имеет объект [PlaybackSession](https://msdn.microsoft.com/library/windows/apps/windows.media.playback.mediaplayer.playbacksession.aspx) типа [MediaPlaybackSession](https://msdn.microsoft.com/library/windows/apps/windows.media.playback.mediaplaybacksession.aspx), который контролирует различные аспекты воспроизведения мультимедиа, такие как [PlaybackRate](https://msdn.microsoft.com/library/windows/apps/windows.media.playback.mediaplaybacksession.playbackrate.aspx), [PlaybackState](https://msdn.microsoft.com/library/windows/apps/windows.media.playback.mediaplaybacksession.playbackstate.aspx) и [Position](https://msdn.microsoft.com/library/windows/apps/windows.media.playback.mediaplaybacksession.position.aspx). Здесь используется событие [PlaybackStateChanged](https://msdn.microsoft.com/library/windows/apps/windows.media.playback.mediaplaybacksession.playbackstatechanged.aspx) в объекте [MediaPlayer.PlaybackSession](https://msdn.microsoft.com/library/windows/apps/windows.media.playback.mediaplayer.playbacksession.aspx), чтобы обнаруживать ситуации, когда нужно отменить запрос дисплея. Затем с помощью свойства [NaturalVideoHeight](https://msdn.microsoft.com/library/windows/apps/windows.media.playback.mediaplaybacksession.naturalvideoheight.aspx) определите тип воспроизводимого файла (аудио или видео) и поддерживайте экран активным, только если воспроизводится видео.
    ```xaml
<MediaPlayerElement x:Name="mpe" Source="Media/video1.mp4"/>
    ```

    ```csharp
    protected override void OnNavigatedTo(NavigationEventArgs e)
    {
        mpe.MediaPlayer.PlaybackSession.PlaybackStateChanged += MediaPlayerElement_CurrentStateChanged;
        base.OnNavigatedTo(e);
    }

    private void MediaPlayerElement_CurrentStateChanged(object sender, RoutedEventArgs e)
    {
        MediaPlaybackSession playbackSession = sender as MediaPlaybackSession;
        if (playbackSession != null && playbackSession.NaturalVideoHeight != 0)
        {
            if(playbackSession.PlaybackState == MediaPlaybackState.Playing)
            {
                if(appDisplayRequest == null)
                {
                    // This call creates an instance of the DisplayRequest object
                    appDisplayRequest = new DisplayRequest();
                    appDisplayRequest.RequestActive();
                }
            }
            else // PlaybackState is Buffering, None, Opening or Paused
            {
                if(appDisplayRequest != null)
                {
                      // Deactivate the displayr request and set the var to null
                      appDisplayRequest.RequestRelease();
                      appDisplayRequest = null;
                }
            }
        }

    }
    ```

### <a name="control-the-media-player-programmatically"></a>Управление проигрывателем мультимедиа программными средствами
[MediaPlayerElement](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.mediaplayerelement.aspx) через свойство [MediaPlayerElement.MediaPlayer](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.mediaplayerelement.mediaplayer.aspx) предоставляет многочисленные свойства, методы и события для управления воспроизведением звука и видео. Полный перечень свойств, методов и событий см. на странице справки по объекту [MediaPlayer](https://msdn.microsoft.com/library/windows/apps/windows.media.playback.mediaplayer.aspx).

### <a name="advanced-media-playback-scenarios"></a>Расширенные сценарии воспроизведения мультимедиа
Для более сложных сценариев воспроизведения мультимедиа, таких как воспроизведение списка воспроизведения, переключение между языками или создание пользовательских дорожек метаданных, задайте для объекта [MediaPlayerElement.Source](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.mediaplayerelement.source.aspx) свойство [MediaPlaybackItem](https://msdn.microsoft.com/library/windows/apps/windows.media.playback.mediaplaybackitem.aspx) или [MediaPlaybackList](https://msdn.microsoft.com/library/windows/apps/windows.media.playback.mediaplaybacklist.aspx). См. на странице [воспроизведения мультимедиа](https://msdn.microsoft.com/windows/uwp/audio-video-camera/media-playback-with-mediasource) , Дополнительные сведения о включении различных расширенных функций мультимедиа.

### <a name="enable-full-window-video-rendering"></a>Включение полнооконной прорисовки видео

Настройте свойство [IsFullWindow](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.mediaplayerelement.isfullwindow.aspx) для включения и отключения полнооконной отрисовки. Если полнооконная прорисовка задается в программном коде приложения, всегда следует использовать свойство **IsFullWindow**, а не включать прорисовку вручную. Использование свойства **IsFullWindow** гарантирует, что будет выполнена оптимизация на уровне системы для повышения производительности и времени работы от батареи. Если полнооконная прорисовка настроена неправильно, то такая оптимизация может не работать.

Вот код, в котором создается объект [AppBarButton](https://msdn.microsoft.com/library/windows/apps/dn279244), переключающий полнооконную прорисовку.

```xaml
<AppBarButton Icon="FullScreen"
              Label="Full Window"
              Click="FullWindow_Click"/>>
```

```csharp
private void FullWindow_Click(object sender, object e)
{
    mediaPlayer.IsFullWindow = !media.IsFullWindow;
}
```

### <a name="resize-and-stretch-video"></a>Изменение размера и растяжение видео

Свойство [Stretch](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.mediaplayerelement.stretch.aspx) позволяет изменить способ заполнения контейнера видеосодержимым и (или) [PosterSource](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.mediaplayerelement.postersource.aspx). Видео меняет размер и растягивается в зависимости от значения [Stretch](https://msdn.microsoft.com/library/windows/apps/br242968). Состояния свойства **Stretch** аналогичны параметрам размера картинки на многих телевизорах. Вы можете привязать это свойство к кнопке и дать пользователю возможность выбрать предпочтительный вариант.

-   [None](https://msdn.microsoft.com/library/windows/apps/br242968) — содержимое отображается в основном разрешении и исходном размере.
-   [Uniform](https://msdn.microsoft.com/library/windows/apps/br242968) — заполняется максимальное пространство с сохранением пропорций и без потери частей изображения. Это может привести к появлению горизонтальных или вертикальных черных полос по краям видео, как в широкоэкранных режимах.
-   [UniformToFill](https://msdn.microsoft.com/library/windows/apps/br242968) — заполняется все отведенное место, сохраняя пропорции. Это может привести к тому, что часть изображения будет обрезана, как в полноэкранных режимах.
-   [Fill](https://msdn.microsoft.com/library/windows/apps/br242968) — заполняется все отведенное место, но без сохранения пропорций. Изображение не обрезается, но может быть растянуто, как в режимах с растяжением.

![Различные значения свойства Stretch](images/Image_Stretch.jpg)

В этом примере [AppBarButton](https://msdn.microsoft.com/library/windows/apps/dn279244) используется, чтобы циклически отображать параметры [Stretch](https://msdn.microsoft.com/library/windows/apps/br242968). Оператор **switch** проверяет текущее состояние свойства [Stretch](https://msdn.microsoft.com/library/windows/apps/br227422) и задает для него следующее значение из перечисления **Stretch**. Таким образом пользователь может циклически проходить по различным состояниям растяжения.

```xaml
<AppBarButton Icon="Switch"
              Label="Resize Video"
              Click="PictureSize_Click" />
```

```csharp
private void PictureSize_Click(object sender, RoutedEventArgs e)
{
    switch (mediaPlayer.Stretch)
    {
        case Stretch.Fill:
            mediaPlayer.Stretch = Stretch.None;
            break;
        case Stretch.None:
            mediaPlayer.Stretch = Stretch.Uniform;
            break;
        case Stretch.Uniform:
            mediaPlayer.Stretch = Stretch.UniformToFill;
            break;
        case Stretch.UniformToFill:
            mediaPlayer.Stretch = Stretch.Fill;
            break;
        default:
            break;
    }
}
```

### <a name="enable-low-latency-playback"></a>Активация воспроизведения с низкой задержкой

Присвойте свойству [RealTimePlayback](https://msdn.microsoft.com/library/windows/apps/windows.media.playback.mediaplayer.realtimeplayback.aspx) значение **true** в элементе [MediaPlayerElement.MediaPlayer](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.mediaplayerelement.mediaplayer.aspx), чтобы позволить элементу проигрывателя мультимедиа уменьшить первоначальную задержку для воспроизведения. Это имеет первостепенное значение для приложений двусторонней связи и может быть необходимо в некоторых игровых ситуациях. Помните, что этот режим требует больше ресурсов и расходует больше заряда батареи.

В этом примере создается объект [MediaPlayerElement](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.mediaplayerelement.aspx), а его атрибуту [RealTimePlayback](https://msdn.microsoft.com/library/windows/apps/windows.media.playback.mediaplayer.realtimeplayback.aspx) присваивается значение **true**.


```csharp
MediaPlayerElement mp = new MediaPlayerElement();
mp.MediaPlayer.RealTimePlayback = true;
```

## <a name="recommendations"></a>Рекомендации

Проигрыватель мультимедиа поддерживает светлые и темные темы, однако темная тема удобнее для большинства сценариев, связанных с развлечениями. Темный фон обеспечивает более высокую контрастность, особенно в условиях слабого освещения. Кроме того, панель элементов управления в таком случае не мешает просмотру.

При воспроизведении видеосодержимого предложите максимальное удобство просмотра в полноэкранном режиме (по сравнению со встроенным режимом). Полноэкранный режим является оптимальным; во встроенном режиме возможности ограничены.

Если на экране достаточно места или вы проектируете приложение для больших экранов, выбирайте макет с двумя строками. Так у вас будет больше места для элементов управления, чем при использовании однострочного макета. Кроме того, в этом случае облегчается навигация по большому экрану с использованием геймпада.

> **Примечание.**&nbsp;&nbsp; Дополнительные сведения об оптимизации приложения для большого экрана см. в статье [Проектирование для Xbox и ТВ](../devices/designing-for-tv.md).

Элементы управления по умолчанию были оптимизированы для воспроизведения мультимедиа, однако вы можете добавить в проигрыватель мультимедиа пользовательские параметры, необходимые для оптимальной работы вашего приложения. Дополнительные сведения о добавлении пользовательских элементов управления см. на странице [Создание пользовательских элементов управления транспортом](custom-transport-controls.md).

## <a name="get-the-sample-code"></a>Получить пример кода

- [Образец галереи элементов управления XAML](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/XamlUIBasics) — ознакомьтесь со всеми элементами управления XAML в интерактивном формате.

## <a name="related-articles"></a>Еще по теме

- [Основы проектирования команд в приложениях UWP](https://msdn.microsoft.com/library/windows/apps/dn958433)
- [Основы проектирования содержимого для приложений UWP](https://msdn.microsoft.com/library/windows/apps/dn958434)

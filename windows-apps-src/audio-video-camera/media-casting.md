---
author: drewbatgit
ms.assetid: 40B97E0C-EB1B-40C2-A022-1AB95DFB085E
description: В этой статье показано, как транслировать мультимедиа в удаленные устройства из универсального приложения для Windows.
title: Трансляция мультимедиа
ms.author: drewbat
ms.date: 02/08/2017
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: da0bb4d25166dd62372d5902ff89221d20189c22
ms.sourcegitcommit: 086001cffaf436e6e4324761d59bcc5e598c15ea
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/27/2018
ms.locfileid: "5694931"
---
# <a name="media-casting"></a>Трансляция мультимедиа



В этой статье показано, как транслировать мультимедиа в удаленные устройства из универсального приложения для Windows.

## <a name="built-in-media-casting-with-mediaplayerelement"></a>Встроенная трансляция мультимедиа с помощью MediaPlayerElement

Самый простой способ трансляции мультимедиа из универсального приложения для Windows — использование возможностей встроенной трансляции элемента управления [**MediaPlayerElement**](https://msdn.microsoft.com/library/windows/apps/Windows.UI.Xaml.Controls.MediaPlayerElement).

Чтобы пользователь мог открыть видеофайл для воспроизведения в элементе управления **MediaPlayerElement**, добавьте в свой проект следующие пространства имен.

[!code-cs[BuiltInCastingUsing](./code/MediaCasting_RS1/cs/MainPage.xaml.cs#SnippetBuiltInCastingUsing)]

В XAML-файл вашего приложения добавьте **MediaPlayerElement** и установите для [**AreTransportControlsEnabled**](https://msdn.microsoft.com/library/windows/apps/dn298977) значение true.

[!code-xml[MediaElement](./code/MediaCasting_RS1/cs/MainPage.xaml#SnippetMediaElement)]

Добавьте кнопку, позволяющую пользователю инициировать выбор файла.

[!code-xml[OpenButton](./code/MediaCasting_RS1/cs/MainPage.xaml#SnippetOpenButton)]

В обработчике событий [**Click**](https://msdn.microsoft.com/library/windows/apps/br227737) для этой кнопки создайте новый экземпляр [**FileOpenPicker**](https://msdn.microsoft.com/library/windows/apps/br207847), добавьте типы видеофайлов в коллекцию [**FileTypeFilter**](https://msdn.microsoft.com/library/windows/apps/br207850) и укажите начальное расположение видеотеки пользователя.

Вызовите [**PickSingleFileAsync**](https://msdn.microsoft.com/library/windows/apps/jj635275), чтобы загрузить окно средства выбора файлов. Этот метод вернет объект [**StorageFile**](https://msdn.microsoft.com/library/windows/apps/br227171), представляющий видеофайл. Проверьте, что файл не пустой (это произойдет, если пользователь отменит операцию выбора). Вызовите метод [**OpenAsync**](https://msdn.microsoft.com/library/windows/apps/br227221.aspx) этого файла, чтобы получить [**IRandomAccessStream**](https://msdn.microsoft.com/library/windows/apps/br241731) файла. Создайте новый объект **MediaSource** из выбранного файла, вызвав метод [**CreateFromStorageFile**](https://msdn.microsoft.com/library/windows/apps/dn930909), и назначьте его свойству [**Источник**](https://msdn.microsoft.com/library/windows/apps/Windows.UI.Xaml.Controls.MediaPlayerElement.Source) объекта **MediaPlayerElement**, чтобы сделать видеофайл источником видео для элемента управления.

[!code-cs[OpenButtonClick](./code/MediaCasting_RS1/cs/MainPage.xaml.cs#SnippetOpenButtonClick)]

Загрузив видео в **MediaPlayerElement**, пользователь может просто нажать кнопку трансляции на элементах управления передачей, чтобы запустить встроенный диалог, который позволяет выбрать устройство, на которое необходимо транслировать загруженное мультимедиа.

![кнопка трансляции mediaelement](images/media-element-casting-button.png)

> [!NOTE] 
> Начиная с Windows 10 версии 1607 рекомендуется использовать класс **MediaPlayer**, чтобы воспроизводить элементы мультимедиа. **MediaPlayerElement** — это облегченный элемент управления XAML, который используется для отображения содержимого **MediaPlayer** на странице XAML. Элемент управления **MediaElement** продолжает поддерживаться для обратной совместимости. Дополнительные сведения об использовании объектов **MediaPlayer** и **MediaPlayerElement** для воспроизведения мультимедиа см. в разделе [Воспроизведение аудио и видео с помощью MediaPlayer](play-audio-and-video-with-mediaplayer.md). Сведения об использовании **MediaSource** и связанных API для работы с мультимедиа см. в разделе [Элементы, списки воспроизведения и звуковые дорожки мультимедиа](media-playback-with-mediasource.md).

## <a name="media-casting-with-the-castingdevicepicker"></a>Трансляция мультимедиа с помощью CastingDevicePicker

Второй способ трансляции мультимедиа на устройство заключается в использовании [**CastingDevicePicker**](https://msdn.microsoft.com/library/windows/apps/dn972525). Чтобы воспользоваться этим классом, включите в свой проект пространство имен [**Windows.Media.Casting**](https://msdn.microsoft.com/library/windows/apps/dn972568).

[!code-cs[CastingNamespace](./code/MediaCasting_RS1/cs/MainPage.xaml.cs#SnippetCastingNamespace)]

Объявите переменную-члена для объекта **CastingDevicePicker**.

[!code-cs[DeclareCastingPicker](./code/MediaCasting_RS1/cs/MainPage.xaml.cs#SnippetDeclareCastingPicker)]

Во время инициализации страницы создайте экземпляр средства выбора трансляции и установите значение [**Filter**](https://msdn.microsoft.com/library/windows/apps/dn972540) для свойства [**SupportsVideo**](https://msdn.microsoft.com/library/windows/apps/dn972526), чтобы указать, что транслирующие устройства, перечисленные средством выбора, должны поддерживать видео. Зарегистрируйте обработчик события [**CastingDeviceSelected**](https://msdn.microsoft.com/library/windows/apps/dn972539), которое вызывается, когда пользователь выбирает устройство для трансляции.

[!code-cs[InitCastingPicker](./code/MediaCasting_RS1/cs/MainPage.xaml.cs#SnippetInitCastingPicker)]

В XAML-файле добавьте кнопку, которая позволяет пользователю открывать средство выбора.

[!code-xml[CastPickerButton](./code/MediaCasting_RS1/cs/MainPage.xaml#SnippetCastPickerButton)]

В обработчике события **Click** для этой кнопки вызовите [**TransformToVisual**](https://msdn.microsoft.com/library/windows/apps/br208986), чтобы получить преобразование элемента пользовательского интерфейса по отношению к другому элементу. В данном примере преобразованием является положение кнопки средства выбора трансляции относительно визуального корня окна приложения. Вызовите метод [**Show**](https://msdn.microsoft.com/library/windows/apps/dn972542) объекта [**CastingDevicePicker**](https://msdn.microsoft.com/library/windows/apps/dn972525), чтобы открыть диалоговое окно средства выбора трансляции. Укажите расположение и размеры кнопки средства выбора трансляции, чтобы система могла создать всплывающее диалоговое окно, когда пользователь нажмет на эту кнопку.

[!code-cs[CastPickerButtonClick](./code/MediaCasting_RS1/cs/MainPage.xaml.cs#SnippetCastPickerButtonClick)]

В обработчике события **CastingDeviceSelected** вызовите метод [**CreateCastingConnection**](https://msdn.microsoft.com/library/windows/apps/dn972547) свойства [**SelectedCastingDevice**](https://msdn.microsoft.com/library/windows/apps/dn972546) аргументов события, которое обозначает выбранное пользователем транслирующее устройство. Зарегистрируйте обработчики для событий [**ErrorOccurred**](https://msdn.microsoft.com/library/windows/apps/dn972519) и [**StateChanged**](https://msdn.microsoft.com/library/windows/apps/dn972523). Вызовите метод [**RequestStartCastingAsync**](https://msdn.microsoft.com/library/windows/apps/dn972520), чтобы начать трансляцию, передавая результат методу [**GetAsCastingSource**](https://msdn.microsoft.com/library/windows/apps/dn920012) объекта **MediaPlayer** элемента управления **MediaPlayerElement**, чтобы указать, что транслируемое мультимедиа— это содержимое объекта **MediaPlayer**, связанного с объектом **MediaPlayerElement**.

> [!NOTE] 
> Подключение к трансляции должно быть инициировано в потоке пользовательского интерфейса. Так как поток пользовательского интерфейса не вызывает **CastingDeviceSelected**, вам необходимо разместить эти вызовы внутри вызова [**CoreDispatcher.RunAsync**](https://msdn.microsoft.com/library/windows/apps/hh750317), который вызовет их в потоке пользовательского интерфейса.

[!code-cs[CastingDeviceSelected](./code/MediaCasting_RS1/cs/MainPage.xaml.cs#SnippetCastingDeviceSelected)]

Чтобы проинформировать пользователя о текущем состоянии трансляции, обновите пользовательский интерфейс в обработчиках событий **ErrorOccurred** и **StateChanged**. Более подробно эти события рассматриваются в следующем разделе по созданию настраиваемого средства выбора транслирующего устройства.

[!code-cs[EmptyStateHandlers](./code/MediaCasting_RS1/cs/MainPage.xaml.cs#SnippetEmptyStateHandlers)]

## <a name="media-casting-with-a-custom-device-picker"></a>Трансляция мультимедиа с помощью настраиваемого средства выбора устройства

В следующем разделе описывается, как создать собственный пользовательский интерфейс для средства выбора транслирующего устройства с помощью перечисления транслирующих устройств и инициации подключения из своего кода.

Для перечисления доступных транслирующих устройств включите в свой проект пространство имен [**Windows.Devices.Enumeration**](https://msdn.microsoft.com/library/windows/apps/br225459).

[!code-cs[EnumerationNamespace](./code/MediaCasting_RS1/cs/MainPage.xaml.cs#SnippetEnumerationNamespace)]

Чтобы реализовать элементарный пользовательский интерфейс для данного примера, добавьте на свою страницу XAML следующие элементы управления:

-   Кнопку для запуска наблюдателя устройств, который осуществляет поиск доступных транслирующих устройств.
-   Элемент управления [**ProgressRing**](https://msdn.microsoft.com/library/windows/apps/br227538) для обратной связи с пользователем, для которого проходит перечисление трансляции.
-   [**ListBox**](https://msdn.microsoft.com/library/windows/apps/br242868) для перечисления обнаруженных транслирующих устройств. Определите [**ItemTemplate**](https://msdn.microsoft.com/library/windows/apps/br242830) для элемента управления, чтобы объекты транслирующих устройств можно было присвоить непосредственно элементу управления и по-прежнему отображать свойство [**FriendlyName**](https://msdn.microsoft.com/library/windows/apps/dn972549).
-   Кнопка для отключения от транслирующего устройства.

[!code-xml[CustomPickerXAML](./code/MediaCasting_RS1/cs/MainPage.xaml#SnippetCustomPickerXAML)]

В своем коде программной части объявите переменные-члены для [**DeviceWatcher**](https://msdn.microsoft.com/library/windows/apps/br225446) и [**CastingConnection**](https://msdn.microsoft.com/library/windows/apps/dn972510).

[!code-cs[DeclareDeviceWatcher](./code/MediaCasting_RS1/cs/MainPage.xaml.cs#SnippetDeclareDeviceWatcher)]

Сначала обновите пользовательский интерфейс в обработчике **Click** для *startWatcherButton*, отключив кнопку и активировав кольцевой индикатор выполнения во время перечисления устройств. Очистите список транслирующих устройств.

Затем создайте наблюдатель устройств, вызвав [**DeviceInformation.CreateWatcher**](https://msdn.microsoft.com/library/windows/apps/br225427). Этот метод можно использовать для отслеживания различных типов устройств. Укажите с помощью строки селектора устройств, которую возвращает [**CastingDevice.GetDeviceSelector**](https://msdn.microsoft.com/library/windows/apps/dn972551), что необходимо отслеживать устройства, поддерживающие трансляцию видео.

И, наконец, зарегистрируйте обработчики событий для [**Added**](https://msdn.microsoft.com/library/windows/apps/br225450), [**Removed**](https://msdn.microsoft.com/library/windows/apps/br225453), [**EnumerationCompleted**](https://msdn.microsoft.com/library/windows/apps/br225451) и [**Stopped**](https://msdn.microsoft.com/library/windows/apps/br225457).

[!code-cs[StartWatcherButtonClick](./code/MediaCasting_RS1/cs/MainPage.xaml.cs#SnippetStartWatcherButtonClick)]

Событие **Added** наступает, когда наблюдатель обнаруживает новое устройство. В обработчике для этого события создайте новый объект [**CastingDevice**](https://msdn.microsoft.com/library/windows/apps/dn972524), вызвав метод [**CastingDevice.FromIdAsync**](https://msdn.microsoft.com/library/windows/apps/dn972550) и передав идентификатор обнаруженного транслирующего устройства, который содержится в переданном обработчику объекте **DeviceInformation**.

Добавьте **CastingDevice** в транслирующее устройство **ListBox**, чтобы пользователь мог его выбрать. Из-за шаблона [**ItemTemplate**](https://msdn.microsoft.com/library/windows/apps/br242830), определенного в XAML, свойство [**FriendlyName**](https://msdn.microsoft.com/library/windows/apps/dn972549) будет использоваться в качестве текста элемента в списке. Поскольку этот обработчик событий не вызывается из потока пользовательского интерфейса, необходимо обновить пользовательский интерфейс внутри вызова [**CoreDispatcher.RunAsync**](https://msdn.microsoft.com/library/windows/apps/hh750317).

[!code-cs[WatcherAdded](./code/MediaCasting_RS1/cs/MainPage.xaml.cs#SnippetWatcherAdded)]

Событие **Removed** возникает, когда наблюдатель обнаруживает, что транслирующего устройства больше нет. Сравните свойство переданного обработчику идентификатора объекта **Added** с идентификатором каждого элемента **Added** в коллекции [**Items**](https://msdn.microsoft.com/library/windows/apps/br242823) списка. Если идентификатор совпадает, удалите этот объект из коллекции. И вновь, так как пользовательский интерфейс был обновлен, этот вызов должен выполняться из вызова **RunAsync**.

[!code-cs[WatcherRemoved](./code/MediaCasting_RS1/cs/MainPage.xaml.cs#SnippetWatcherRemoved)]

Событие **EnumerationCompleted** возникает, когда наблюдатель завершает поиск устройств. В обработчике для этого события обновите пользовательский интерфейс, чтобы пользователь знал, что процесс перечисления устройств завершен, и остановите наблюдателя устройств, вызвав [**Stop**](https://msdn.microsoft.com/library/windows/apps/br225456).

[!code-cs[WatcherEnumerationCompleted](./code/MediaCasting_RS1/cs/MainPage.xaml.cs#SnippetWatcherEnumerationCompleted)]

Событие Stopped возникает, когда наблюдатель устройств завершил процесс остановки. В обработчике для этого события остановите элемент управления [**ProgressRing**](https://msdn.microsoft.com/library/windows/apps/br227538) и повторно включите *startWatcherButton*, чтобы пользователь мог перезапустить процесс перечисления устройств.

[!code-cs[WatcherStopped](./code/MediaCasting_RS1/cs/MainPage.xaml.cs#SnippetWatcherStopped)]

Когда пользователь выбирает из списка одно из транслирующих устройств, возникает событие [**SelectionChanged**](https://msdn.microsoft.com/library/windows/apps/br209776). В рамках именно этого обработчика и будет создано подключение к трансляции, а трансляция — запущена.

Для начала убедитесь, что наблюдатель устройств остановлен, чтобы перечисление устройств не помешало трансляции мультимедиа. Создайте подключение к трансляции, вызвав [**CreateCastingConnection**](https://msdn.microsoft.com/library/windows/apps/dn972547) в выбранном пользователем объекте **CastingDevice**. Добавьте обработчиков событий для [**StateChanged**](https://msdn.microsoft.com/library/windows/apps/dn972523) и [**ErrorOccurred**](https://msdn.microsoft.com/library/windows/apps/dn972519).

Запустите трансляцию мультимедиа, вызвав [**RequestStartCastingAsync**](https://msdn.microsoft.com/library/windows/apps/dn972520), передав источник трансляции, который возвратился при вызове метода [**GetAsCastingSource**](https://msdn.microsoft.com/library/windows/apps/dn920012) объекта **MediaPlayer**. Наконец, сделайте видимой кнопку отключения, чтобы позволить пользователю остановить трансляцию мультимедиа.

[!code-cs[SelectionChanged](./code/MediaCasting_RS1/cs/MainPage.xaml.cs#SnippetSelectionChanged)]

В состоянии, измененном обработчиком, выполняемое вами действие зависит от нового состояния подключения к трансляции.

-   Если состояние равно **Connected** или **Rendering**, убедитесь, что элемент управления **ProgressRing** не активен, а кнопка отключения — видима.
-   Если состояние равно **Disconnected**, отмените выбор текущего транслирующего устройства в списке, деактивируйте элемент управления **ProgressRing** и скройте кнопку отключения.
-   Если состояние — **Connecting**, сделайте элемент управления **ProgressRing** активным и скройте кнопку отключения.
-   Если состояние — **Disconnecting**, сделайте элемент управления **ProgressRing** активным и скройте кнопку отключения.

[!code-cs[StateChanged](./code/MediaCasting_RS1/cs/MainPage.xaml.cs#SnippetStateChanged)]

В обработчике для события **ErrorOccurred** обновите свой пользовательский интерфейс, чтобы пользователь знал о возникновении ошибки при трансляции, и отмените выбор текущего объекта **CastingDevice** в списке.

[!code-cs[ErrorOccurred](./code/MediaCasting_RS1/cs/MainPage.xaml.cs#SnippetErrorOccurred)]

Наконец, реализуйте обработчик для кнопки отключения. Остановите трансляцию мультимедиа и отключитесь от транслирующего устройства, вызвав для объекта **CastingConnection** метод [**DisconnectAsync**](https://msdn.microsoft.com/library/windows/apps/dn972518). Этот вызов должен быть отправлен потоку пользовательского интерфейса с помощью вызова [**CoreDispatcher.RunAsync**](https://msdn.microsoft.com/library/windows/apps/hh750317).

[!code-cs[DisconnectButton](./code/MediaCasting_RS1/cs/MainPage.xaml.cs#SnippetDisconnectButton)]

 

 





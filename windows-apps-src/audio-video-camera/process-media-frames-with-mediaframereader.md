---
ms.assetid: a128edc8-8a80-4645-ac29-908ede2d1c72
description: В этой статье рассказывается, как использовать класс MediaFrameReader с MediaCapture для получения кадров мультимедиа из одного или нескольких доступных источников, включая цветные камеры, камеры с эффектом глубины, инфракрасные камеры, звуковые устройства и даже специальные источники кадров, например создающие скелетные кадры отслеживания.
title: Обработка кадров мультимедиа с помощью MediaFrameReader
ms.date: 02/08/2017
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: 9940367054ae8771355012492434e12aa97d43ad
ms.sourcegitcommit: d2517e522cacc5240f7dffd5bc1eaa278e3f7768
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/30/2018
ms.locfileid: "8329989"
---
# <a name="process-media-frames-with-mediaframereader"></a>Обработка кадров мультимедиа с помощью MediaFrameReader

В этой статье рассказывается, как использовать класс [**MediaFrameReader**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Capture.Frames.MediaFrameReader) с [**MediaCapture**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Capture.MediaCapture) для получения кадров мультимедиа из одного или нескольких доступных источников, включая цветные камеры, камеры с эффектом глубины, инфракрасные камеры, звуковые устройства и даже специальные источники кадров, например создающие скелетные кадры отслеживания. Эта возможность предназначена для использования приложениями, которые выполняют обработку кадров мультимедиа в режиме реального времени, например приложениями дополненной реальности или камеры с эффектом глубины.

Если вы хотите просто записать видео или сделать фотографии, как в обычном приложении для фотографирования, возможно, вам будет удобнее использовать какой-либо другой метод съемки, поддерживаемый классом [**MediaCapture**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Capture.MediaCapture). Список доступных методов захвата мультимедийных данных и статей по их использованию см. в разделе [**Камера**](camera.md).

> [!NOTE] 
> Описанные в этой статье функции доступны, только начиная c Windows 10 версии 1607.

> [!NOTE] 
> Существует пример универсального приложения для Windows, который демонстрирует использование **MediaFrameReader** для отображения кадров из разных источников, включая цветные и инфракрасные камеры и камеры с эффектом глубины. Дополнительные сведения см. в разделе [Пример кадров камеры](http://go.microsoft.com/fwlink/?LinkId=823230).

> [!NOTE] 
> Новый набор API-интерфейсов для использования **MediaFrameReader** со звуковыми данными появился в Windows 10 версии 1803. Дополнительные сведения см. в разделе [Обработка аудиокадров с помощью MediaFrameReader](process-audio-frames-with-mediaframereader.md).


## <a name="setting-up-your-project"></a>Настройка проекта
Как и в любом приложении, использующем **MediaCapture**, перед попыткой получить доступ к камере вам необходимо объявить, что ваше приложение использует возможность *webcam*. Если ваше приложение получает данные от звукового устройства, рекомендуется также объявить возможность устройства *microphone*. 

**Добавление возможностей в манифест приложения**

1.  В Microsoft Visual Studio откройте конструктор манифеста приложения, дважды щелкнув элемент **package.appxmanifest** в **Обозревателе решений**.
2.  Перейдите на вкладку **Возможности**.
3.  Выставьте флажок для пункта **Веб-камера** и поле для параметра **Микрофон**.
4.  Для доступа к библиотеке изображений и видео установите флажки **Библиотека изображений** и **Библиотека видео**.

В примере кода в этой статье используются API из следующих пространств имен в дополнение к включенным в шаблон проекта по умолчанию.

[!code-cs[FramesUsing](./code/Frames_Win10/Frames_Win10/MainPage.xaml.cs#SnippetFramesUsing)]

## <a name="select-frame-sources-and-frame-source-groups"></a>Выбор источников кадров и групп источников кадров
Многим приложениям, обрабатывающим кадры мультимедиа, требуется получать кадры из нескольких источников одновременно, например от цветных камер и камер с эффектом глубины. Объект [**MediaFrameSourceGroup**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Capture.Frames.MediaFrameSourceGroup) представляет набор источников кадров мультимедиа, которые могут быть использованы одновременно. Вызовите статический метод [**MediaFrameSourceGroup.FindAllAsync**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Capture.Frames.MediaFrameSourceGroup.FindAllAsync), чтобы получить список всех групп источников кадров, поддерживаемых данным устройством.

[!code-cs[FindAllAsync](./code/Frames_Win10/Frames_Win10/MainPage.xaml.cs#SnippetFindAllAsync)]

Вы также можете создать [**DeviceWatcher**](https://msdn.microsoft.com/library/windows/apps/Windows.Devices.Enumeration.DeviceWatcher) , с помощью [**DeviceInformation.CreateWatcher**](https://msdn.microsoft.com/library/windows/apps/br225427) и значение, возвращаемое при [**MediaFrameSourceGroup.GetDeviceSelector**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Capture.Frames.MediaFrameSourceGroup.GetDeviceSelector) получать уведомления, когда доступных групп источников кадров на устройстве изменения, например при подключении внешней камеры. Дополнительные сведения см. в разделе [**Перечисление устройств**](https://msdn.microsoft.com/windows/uwp/devices-sensors/enumerate-devices).

[**MediaFrameSourceGroup**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Capture.Frames.MediaFrameSourceGroup) содержит коллекцию объектов [**MediaFrameSourceInfo**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Capture.Frames.MediaFrameSourceInfo), описывающих источники кадров, включенные в эту группу. После получения групп источников кадров, доступных на устройстве, можно выбрать группу, которая предоставляет интересующие вас источники кадров.

В следующем примере показан простейший способ выбора группы источников кадров. Этот код просто перебирает все доступные группы и затем перебирает все элементы в коллекции [**SourceInfos**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Capture.Frames.MediaFrameSourceGroup.SourceInfos). Каждый элемент **MediaFrameSourceInfo** проверяется на предмет поддержки необходимых нам функций. В этом случае свойство [**MediaStreamType**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Capture.Frames.MediaFrameSourceInfo.MediaStreamType) проверяется по значению [**VideoPreview**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Capture.MediaStreamType); это означает, что устройство предоставляет поток предварительного просмотра видео, а свойство [**SourceKind**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Capture.Frames.MediaFrameSourceInfo.SourceKind) проверяется по значению [**Color**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Capture.Frames.MediaFrameSourceKind), указывая, что источник предоставляет цветные кадры.

[!code-cs[SimpleSelect](./code/Frames_Win10/Frames_Win10/MainPage.xaml.cs#SnippetSimpleSelect)]

Этот метод определения желаемой группы источников кадров и источников кадров работает в простых случаях, но если вы хотите выбрать источники кадров на основании более сложных критериев, он может быстро усложниться. Еще один способ — использовать синтаксис Linq и анонимные объекты для выполнения выбора. В следующем примере используется метод расширения **Select** для преобразования объектов **MediaFrameSourceGroup** в списке *frameSourceGroups* в анонимный объект с двумя полями: *sourceGroup*, представляющим саму группу, и *colorSourceInfo*, представляющим источник цветных кадров в этой группе. В поле *colorSourceInfo* содержится результат **FirstOrDefault**, который выбирает первый объект, для которого используемое условие возвращает значение true. В этом случае условие приобретает значение true, если типом потока является **VideoPreview**, типом источника — **Color**, а камерой является расположенная на лицевой панели устройства.

В списке анонимных объектов, возвращаемом по рассмотренному выше запросу, метод расширения **Where** используется для выбора только тех объектов, у которых в поле *colorSourceInfo* не стоит значение null. В заключение производится вызов **FirstOrDefault**, чтобы выбрать первый элемент в списке.

Теперь можно использовать поля выбранного объекта для получения ссылок на выбранную группу **MediaFrameSourceGroup** и объект **MediaFrameSourceInfo**, представляющие цветную камеру. Они будут использоваться в дальнейшем для инициализации объекта **MediaCapture** и создания **MediaFrameReader** для выбранного источника. В заключение необходимо проверить, не соответствует ли группе источников значение null, которое означает, что данное устройство не располагает запрошенными вами источниками захвата.

[!code-cs[SelectColor](./code/Frames_Win10/Frames_Win10/MainPage.xaml.cs#SnippetSelectColor)]

В следующем примере используется подобный описанному выше метод для выбора группы источников, содержащей цветные и инфракрасные камеры и камеры с эффектом глубины.

[!code-cs[ColorInfraredDepth](./code/Frames_Win10/Frames_Win10/MainPage.xaml.cs#SnippetColorInfraredDepth)]

> [!NOTE]
> Начиная с Windows 10 версии 1803, вы можете использовать класс [**MediaCaptureVideoProfile**](https://docs.microsoft.com/uwp/api/Windows.Media.Capture.MediaCaptureVideoProfile) для выбора источника кадров мультимедиа с набором требуемых возможностей. Дополнительные сведения см. в последующем разделе **Использование профилей видео для выбора источника кадров** этой статьи.


## <a name="initialize-the-mediacapture-object-to-use-the-selected-frame-source-group"></a>Инициализация объекта MediaCapture для использования выбранной группы источников кадров
Далее нужно инициализировать объект **MediaCapture** для использования выбранной на предыдущем этапе группы источников кадров.

Объект **MediaCapture** обычно используется из разных расположений внутри приложения, поэтому следует объявить переменную-член класса, чтобы хранить его.

[!code-cs[DeclareMediaCapture](./code/Frames_Win10/Frames_Win10/MainPage.xaml.cs#SnippetDeclareMediaCapture)]

Создайте экземпляр объекта **MediaCapture**, вызвав конструктор. Далее создайте объект [**MediaCaptureSettings**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Capture.MediaCaptureSettings), который будет использоваться для инициализации объекта **MediaCapture**. В этом примере использованы следующие параметры:

* [**SourceGroup**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Capture.MediaCaptureInitializationSettings.SourceGroup) — сообщает системе, какая группа источников будет использоваться для получения кадров. Не забывайте, что группа источников определяет набор источников кадров мультимедиа, которые можно использовать одновременно.
* [**SharingMode**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Capture.MediaCaptureInitializationSettings.SharingMode) — сообщает системе, требуется ли монопольное управление устройствами-источниками захвата. Если здесь задается [**ExclusiveControl**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Capture.MediaCaptureSharingMode), это означает, что вы можете изменить параметры устройства захвата, например формат производимых им кадров, но это также означает, что если другое приложение уже обладает монопольным управлением, ваше приложение получит сбой при попытке инициализации устройства захвата мультимедиа. Если здесь задается [**SharedReadOnly**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Capture.MediaCaptureSharingMode), вы можете получить кадры из источников, даже если они используются другим приложением, но не можете изменять параметры устройств.
* [**MemoryPreference**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Capture.MediaCaptureInitializationSettings.MemoryPreference) — если задается параметр [**CPU**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Capture.MediaCaptureMemoryPreference), система будет использовать память ЦП, что гарантирует доступность кадров как объектов [**SoftwareBitmap**](https://msdn.microsoft.com/library/windows/apps/Windows.Graphics.Imaging.SoftwareBitmap) при их поступлении. Если здесь задается [**Auto**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Capture.MediaCaptureMemoryPreference), система будет динамически выбирать оптимальный участок памяти для хранения кадров. Если система выбирает использование памяти GPU, кадры мультимедиа будут поступать как объект [**IDirect3DSurface**](https://msdn.microsoft.com/library/windows/apps/Windows.Graphics.DirectX.Direct3D11.IDirect3DSurface), а не **SoftwareBitmap**.
* [**StreamingCaptureMode**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Capture.MediaCaptureInitializationSettings.StreamingCaptureMode) — задайте здесь значение [**Video**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Capture.StreamingCaptureMode), чтобы указать, что передача потока аудио не требуется.

Вызовите [**InitializeAsync**](https://msdn.microsoft.com/library/windows/apps/br226598) для инициализации **MediaCapture** с требуемыми параметрами. Не забудьте поместить этот вызов в блок *try* на случай сбоя инициализации.

[!code-cs[InitMediaCapture](./code/Frames_Win10/Frames_Win10/MainPage.xaml.cs#SnippetInitMediaCapture)]

## <a name="set-the-preferred-format-for-the-frame-source"></a>Установка предпочтительного формата для источника кадров
Для установки предпочтительного формата для источника кадров необходимо получить объект [**MediaFrameSource**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Capture.Frames.MediaFrameSource), представляющий этот источник. Этот объект можно получить путем доступа к словарю [**Frames**](https://msdn.microsoft.com/library/windows/apps/Windows.Phone.Media.Capture.CameraCaptureSequence.Frames) инициализированного объекта **MediaCapture**, указав идентификатор источника кадров, который вы хотите использовать. Поэтому мы сохранили объект [**MediaFrameSourceInfo**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Capture.Frames.MediaFrameSourceInfo) при выборе группы источников кадров.

Свойство [**MediaFrameSource.SupportedFormats**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Capture.Frames.MediaFrameSource.SupportedFormats) содержит список объектов [**MediaFrameFormat**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Capture.Frames.MediaFrameFormat), описывающих поддерживаемые источником кадров форматы. Используйте метод расширения Linq **Where** для выбора формата на основе требуемых свойств. В этом примере выбран формат с шириной 1080 пикселей, позволяющий получать кадры в 32-разрядном формате RGB. Метод расширения **FirstOrDefault** выбирает первый элемент в списке. Если выбранный формат — null, запрошенный формат не поддерживается источником кадров. Если формат поддерживается, вы можете запросить использование источником этого формата путем вызова [**SetFormatAsync**](https://msdn.microsoft.com/library/windows/apps/).

[!code-cs[GetPreferredFormat](./code/Frames_Win10/Frames_Win10/MainPage.xaml.cs#SnippetGetPreferredFormat)]

## <a name="create-a-frame-reader-for-the-frame-source"></a>Создание средство чтения кадров для источника кадров
Для получения кадров от источника кадров мультимедиа используйте [**MediaFrameReader**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Capture.Frames.MediaFrameReader).

[!code-cs[DeclareMediaFrameReader](./code/Frames_Win10/Frames_Win10/MainPage.xaml.cs#SnippetDeclareMediaFrameReader)]

Создайте экземпляр средства чтения кадров путем вызова [**CreateFrameReaderAsync**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Capture.MediaCapture.CreateFrameReaderAsync) в инициализированном объекте **MediaCapture**. Первым аргументом этого метода должен быть источник кадров, из которого вы хотите получать кадры. Можно создать отдельный ридер кадров для каждого источника кадров, который вы хотите использовать. Второй аргумент сообщает системе формат вывода, в котором вы хотите получать кадры. Это поможет вам избежать необходимости самостоятельного преобразования кадров при их поступлении. Обратите внимание, что при выборе формата, не поддерживаемого источником кадров, возникнет исключение, поэтому убедитесь, что это значение входит в коллекцию [**SupportedFormats**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Capture.Frames.MediaFrameSource.SupportedFormats).  

После создания ридера кадров зарегистрируйте обработчик для события [**FrameArrived**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Capture.Frames.MediaFrameReader.FrameArrived), которое создается при поступлении нового кадра из источника.

Дайте системе команду на запуск чтения кадров из источника путем вызова [**StartAsync**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Capture.Frames.MediaFrameReader.StartAsync).

[!code-cs[CreateFrameReader](./code/Frames_Win10/Frames_Win10/MainPage.xaml.cs#SnippetCreateFrameReader)]

## <a name="handle-the-frame-arrived-event"></a>Обработка события поступления кадра
Событие [**MediaFrameReader.FrameArrived**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Capture.Frames.MediaFrameReader.FrameArrived) создается при поступлении каждого нового кадра. Вы можете решить обрабатывать каждый поступающий кадр или использовать кадры, только когда они вам нужны. Поскольку ридер кадров создает событие в своем собственном потоке, возможно, вам потребуется реализовать дополнительную логику синхронизации, чтобы предотвратить попытки доступа к тем же данным из нескольких потоков. В этом разделе рассказывается, как синхронизировать рисование цветных кадров с элементом управления изображением на странице XAML. В этом сценарии рассматривается дополнительное ограничение по синхронизации, требующее выполнение всех обновлений для элементов управления XAML в потоке пользовательского интерфейса.

Первым шагом при отображении кадров в XAML является создание элемента управления "Изображение". 

[!code-xml[ImageElementXAML](./code/Frames_Win10/Frames_Win10/MainPage.xaml#SnippetImageElementXAML)]

На странице программной части объявите переменную-член класса типа **SoftwareBitmap**, которая будет использоваться в качестве заднего буфера, в который будут копироваться все входящие изображения. Обратите внимание, что копируются не сами данные изображений, а только ссылки на объекты. Также объявите логическую переменную для отслеживания того, выполняется ли в данный момент наша операция пользовательского интерфейса.

[!code-cs[DeclareBackBuffer](./code/Frames_Win10/Frames_Win10/MainPage.xaml.cs#SnippetDeclareBackBuffer)]

Поскольку кадры будут поступать как объекты **SoftwareBitmap**, необходимо создать объект [**SoftwareBitmapSource**](https://msdn.microsoft.com/library/windows/apps/Windows.UI.Xaml.Media.Imaging.SoftwareBitmapSource), который позволяет использовать **SoftwareBitmap** в качестве источника для **элемента управления** XAML. Перед запуском средства чтения кадров необходимо задать где-либо в коде источник изображений.

[!code-cs[ImageElementSource](./code/Frames_Win10/Frames_Win10/MainPage.xaml.cs#SnippetImageElementSource)]

Теперь пора реализовать обработчик событий **FrameArrived**. Когда обработчик вызывается, параметр *sender* содержит ссылку на объект **MediaFrameReader**, создавший событие. Вызовите [**TryAcquireLatestFrame**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Capture.Frames.MediaFrameReader.TryAcquireLatestFrame) на этом объекте, чтобы попытаться получить новейший кадр. Как видно из названия, **TryAcquireLatestFrame** может не добиться успеха при возвращении кадра. Поэтому, когда вы получаете доступ к свойствам VideoMediaFrame, а затем SoftwareBitmap, обязательно выполняйте проверку на предмет значения null. В этом примере условный оператор null ? используется для получения доступа к **SoftwareBitmap**, а затем полученный объект проверяется на предмет значения null.

Элемент управления **Image** может отображать только изображения в формате BRGA8 с предварительным умножением альфа-канала или без него. Если поступающий кадр не соответствует этому формату, статический метод [**Convert**](https://msdn.microsoft.com/library/windows/apps/Windows.Graphics.Imaging.SoftwareBitmap.Covert) используется для преобразования программного точечного рисунка в правильный формат.

Далее метод [**Interlocked.Exchange**](https://msdn.microsoft.com/library/bb337971) используется для замены ссылки на поступающий точечный рисунок точечным рисунком из заднего буфера. Этот метод заменяет такие ссылки в атомарной операции, которая является потокобезопасной. После замены старое изображение из заднего буфера, которое теперь находится в переменной *softwareBitmap*, удаляется для высвобождения ресурсов.

Далее объект [**CoreDispatcher**](https://msdn.microsoft.com/library/windows/apps/Windows.UI.Core.CoreDispatcher), связанный с элементом **Image**, используется для создания задачи, которая будет выполняться в потоке пользовательского интерфейса, путем вызова [**RunAsync**](https://msdn.microsoft.com/library/windows/apps/hh750317). Поскольку асинхронные задачи будут выполняться в рамках задачи, передаваемое **RunAsync** лямбда-выражение объявляется с использованием ключевого слова *async*.

В рамках задачи переменная *_taskRunning* проверяется, чтобы обеспечить выполнение только одного экземпляра задачи в определенный момент времени. Если задача еще не выполняется, для *_taskRunning* устанавливается значение true, чтобы предотвратить повторный запуск задачи. В цикле *while* производится вызов **Interlocked.Exchange** для копирования из заднего буфера во временный объект **SoftwareBitmap**, пока изображение в заднем буфере не получит значение null. При каждом заполнении временного точечного рисунка свойство **Source** элемента **Image** приводится к типу **SoftwareBitmapSource**, а затем производится вызов [**SetBitmapAsync**](https://msdn.microsoft.com/library/windows/apps/dn997856) для установки источника изображения.

В заключение переменная *_taskRunning* снова получает значение false, чтобы задачу можно было запустить снова при следующем вызове обработчика.

> [!NOTE] 
> Если вы осуществляете доступ к объектам [**SoftwareBitmap**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Capture.Frames.VideoMediaFrame.SoftwareBitmap) или [**Direct3DSurface**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Capture.Frames.VideoMediaFrame.Direct3DSurface), предоставляемым свойством [**VideoMediaFrame**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Capture.Frames.MediaFrameReference.VideoMediaFrame) класса [**MediaFrameReference**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Capture.Frames.MediaFrameReference), система создает строгую ссылку на эти объекты, что означает, что они не будут удален при вызове метода [**Dispose**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Capture.Frames.MediaFrameReference.Close) в содержащем классе **MediaFrameReference**. Необходимо явно вызвать метод **Dispose** класса **SoftwareBitmap** или **Direct3DSurface** непосредственно для объектов, чтобы немедленно удалить их. В противном случае сборщик мусора в конечном итоге освободит память для этих объектов, однако узнать, когда это произойдет, невозможно, и если количество выделенных растровых изображений или поверхностей превышает максимальное количество, разрешенное системой, поток новых кадров будет остановлен. Вы можете скопировать полученные кадры, например методом [**SoftwareBitmap.Copy**](https://docs.microsoft.com/en-us/uwp/api/windows.graphics.imaging.softwarebitmap.copy), а затем освободить исходные кадры и обойти это ограничение. Также, если вы создали **MediaFrameReader** перегруженной версией метода [CreateFrameReaderAsync(Windows.Media.Capture.Frames.MediaFrameSource inputSource, System.String outputSubtype, Windows.Graphics.Imaging.BitmapSize outputSize)](https://docs.microsoft.com/uwp/api/windows.media.capture.mediacapture.createframereaderasync#Windows_Media_Capture_MediaCapture_CreateFrameReaderAsync_Windows_Media_Capture_Frames_MediaFrameSource_System_String_Windows_Graphics_Imaging_BitmapSize_) или [CreateFrameReaderAsync(Windows.Media.Capture.Frames.MediaFrameSource inputSource, System.String outputSubtype)](https://docs.microsoft.com/uwp/api/windows.media.capture.mediacapture.createframereaderasync#Windows_Media_Capture_MediaCapture_CreateFrameReaderAsync_Windows_Media_Capture_Frames_MediaFrameSource_System_String_), то будут возращены копии исходных кадров, поэтому их сохранение не приведет к остановке процесса получения кадров. 


[!code-cs[FrameArrived](./code/Frames_Win10/Frames_Win10/MainPage.xaml.cs#SnippetFrameArrived)]

## <a name="cleanup-resources"></a>Высвобождение ресурсов
По завершении чтения кадров не забудьте остановить ридер кадров мультимедиа путем вызова [**StopAsync**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Capture.Frames.MediaFrameReader.StopAsync), отмены регистрации обработчика **FrameArrived** и удаления объекта **MediaCapture**.

[!code-cs[Cleanup](./code/Frames_Win10/Frames_Win10/MainPage.xaml.cs#SnippetCleanup)]

Подробные сведения об удалении объектов захвата мультимедиа при приостановке вашего приложения см. в разделе [**Отображение просмотра камеры**](simple-camera-preview-access.md).

## <a name="the-framerenderer-helper-class"></a>Вспомогательный класс FrameRenderer
[Пример кадров камеры](http://go.microsoft.com/fwlink/?LinkId=823230) для универсальной платформы Windows содержит вспомогательный класс, который упрощает показ кадров, полученных от цветных и инфракрасных камер, а также источников с эффектом глубины в вашем приложении. Обычно данные с камеры с эффектом глубины или инфракрасной камеры подвергаются дополнительной обработке, а не просто отображаются на экране, но этот вспомогательный класс представляет собой полезное средство для демонстрации функции ридера кадров и для отладки собственной реализации ридера кадров.

Вспомогательный класс **FrameRenderer** реализует следующие методы.

* Конструктор **FrameRenderer** — конструктор инициализирует вспомогательный класс для использования элемента XAML [**Image**](https://msdn.microsoft.com/library/windows/apps/Windows.UI.Xaml.Controls.Image), который вы передаете для отображения кадров мультимедиа.
* **ProcessFrame** — этот метод отображает кадр мультимедиа, представленный [**MediaFrameReference**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Capture.Frames.MediaFrameReference), в элементе **Image**, переданном конструктору. Обычно этот метод необходимо вызывать из обработчика событий [**FrameArrived**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Capture.Frames.MediaFrameReader.FrameArrived), передавая кадр, возвращенный [**TryAcquireLatestFrame**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Capture.Frames.MediaFrameReader.TryAcquireLatestFrame).
* **ConvertToDisplayableImage** — этот метод проверяет формат кадра мультимедиа и при необходимости преобразовывает его в пригодный для отображения формат. Для цветных изображений это означает обеспечение формата цвета BGRA8 и предварительное умножение для режима альфа-канала точечного рисунка. Для кадров с камер с эффектом глубины или инфракрасных камер каждая растровая строка обрабатывается для преобразования значений глубины или инфракрасных значений в градиентный псевдоцвет с помощью класса **PsuedoColorHelper**, который также включен в пример и приведен ниже.

> [!NOTE] 
> Для обработки пикселей в изображениях **SoftwareBitmap** необходимо получить доступ к собственному буферу памяти. Для этого необходимо использовать интерфейс IMemoryBufferByteAccess COM, включенный в приведенный ниже код, а также необходимо изменить свойства проекта, чтобы разрешить компиляцию небезопасного кода. Дополнительные сведения см. в разделе [Создание, редактирование и сохранение точечных рисунков](imaging.md).

[!code-cs[IMemoryBufferByteAccess](./code/Frames_Win10/Frames_Win10/FrameRenderer.cs#SnippetIMemoryBufferByteAccess)]

[!code-cs[FrameArrived](./code/Frames_Win10/Frames_Win10/FrameRenderer.cs#SnippetFrameRenderer)]

## <a name="use-multisourcemediaframereader-to-get-time-corellated-frames-from-multiple-sources"></a>Используйте MultiSourceMediaFrameReader для получения коррелирующих по времени кадров из нескольких источников.
Начиная с Windows 10 версии 1607, можно использовать [**MultiSourceMediaFrameReader**](https://docs.microsoft.com/uwp/api/windows.media.capture.frames.multisourcemediaframereader) для получения коррелирующих по времени кадров из нескольких источников. Этот API упрощает обработку, если требуются кадры из нескольких источников, сделанные примерно в одно время, например с использованием класса [**DepthCorrelatedCoordinateMapper**](https://docs.microsoft.com/uwp/api/windows.media.devices.core.depthcorrelatedcoordinatemapper). Одним из ограничений этого нового метода является то, что события поступления кадра создаются со скоростью самого медленного источника записи. Дополнительные кадры из более быстрых источников не обрабатываются. Кроме того, поскольку система ожидает, что кадры будут поступать из разных источников с разной скоростью, она автоматически не распознает, прекратил ли источник генерировать кадры. В примере кода из этого раздела показано, как использовать событие для создания собственной логики тайм-аута, которая вызывается, если коррелирующие кадры не поступают в течение определяемого приложением лимита времени.

Действия по использованию [**MultiSourceMediaFrameReader**](https://docs.microsoft.com/uwp/api/windows.media.capture.frames.multisourcemediaframereader) совпадают с действиями по использованию [**MediaFrameReader**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Capture.Frames.MediaFrameReader), описанного ранее в этой статье. В этом примере будет использоваться источник цвета и источник глубины. Объявите несколько строчных переменных, чтобы сохранить идентификаторы источников мультимедийных кадров, которые будут использоваться для выбора кадров из каждого источника. Затем объявите [**ManualResetEventSlim**](https://docs.microsoft.com/dotnet/api/system.threading.manualreseteventslim?view=netframework-4.7), [**CancellationTokenSource**](https://msdn.microsoft.com/library/system.threading.cancellationtokensource.aspx) и [**EventHandler**](https://msdn.microsoft.com/library/system.eventhandler.aspx), которые будут использоваться для реализации логики тайм-аута в этом примере. 

[!code-cs[MultiFrameDeclarations](./code/Frames_Win10/Frames_Win10/MainPage.xaml.cs#SnippetMultiFrameDeclarations)]

Используя описанные ранее в этой статье техники, запросите [**MediaFrameSourceGroup**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Capture.Frames.MediaFrameSourceGroup), которая включает источники цвета и глубины, необходимые для этого сценария из примера. Выбрав нужную группу источника кадров, получите [**MediaFrameSourceInfo**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Capture.Frames.MediaFrameSourceInfo) для каждого источника кадров.

[!code-cs[SelectColorAndDepth](./code/Frames_Win10/Frames_Win10/MainPage.xaml.cs#SnippetSelectColorAndDepth)]

Создайте и инициализируйте объект **MediaCapture** передавая выбранную группу источника кадров в параметрах инициализации.

[!code-cs[MultiFrameInitMediaCapture](./code/Frames_Win10/Frames_Win10/MainPage.xaml.cs#SnippetMultiFrameInitMediaCapture)]

После инициализации объекта **MediaCapture** извлеките объекты [**MediaFrameSource**](https://docs.microsoft.com/uwp/api/Windows.Media.Capture.Frames.MediaFrameSource) для камер цвета и глубины. Сохраните идентификатор для каждого источника, чтобы можно было выбрать входящий кадр для соответствующего источника.

[!code-cs[GetColorAndDepthSource](./code/Frames_Win10/Frames_Win10/MainPage.xaml.cs#SnippetGetColorAndDepthSource)]

Создайте и инициализируйте **MultiSourceMediaFrameReader**, вызвав [**CreateMultiSourceFrameReaderAsync**](https://docs.microsoft.com/uwp/api/windows.media.capture.mediacapture.createmultisourceframereaderasync) и передав массив источников кадров, который будет использоваться считывателем. Зарегистрируйте обработчик событий для события [**FrameArrived**](https://docs.microsoft.com/uwp/api/windows.media.capture.frames.multisourcemediaframereader.FrameArrived). В этом примере создается экземпляр вспомогательного класса **FrameRenderer**, описанный ранее в этой статье, для отображения кадров элементу управления **Image**. Запустите ридер кадров вызовом метода [**StartAsync**](https://docs.microsoft.com/uwp/api/windows.media.capture.frames.multisourcemediaframereader.StartAsync).

Зарегистрируйте обработчик событий для события **CorellationFailed**, объявленного ранее в этом примере. Мы сигнализируем это событие, если один из используемых источников мультимедийных кадров прекратит создание кадров. Наконец, вызовите метод [**Task.Run**](https://msdn.microsoft.com/library/hh195051.aspx), чтобы вызвать вспомогательный метод тайм-аута (**NotifyAboutCorrelationFailure**) в отдельном потоке. Реализации этого метода приводится далее в этой статье.

[!code-cs[InitMultiFrameReader](./code/Frames_Win10/Frames_Win10/MainPage.xaml.cs#SnippetInitMultiFrameReader)]

Событие **FrameArrived** создается всякий раз, когда новый кадр поступает из всех источников мультимедийных кадров, управление которыми осуществляется с помощью **MultiSourceMediaFrameReader**. Это означает, что это событие будет вызвано с интервалом, соответствующим скорости самого медленного мультимедийного источника. Если один источник создает несколько кадров за время, когда более медленный источник создаст один кадр, дополнительные кадры более быстрого источника удаляются. 

Получите метод [**MultiSourceMediaFrameReference**](https://docs.microsoft.com/uwp/api/windows.media.capture.frames.multisourcemediaframereference), связанный с событием, вызвав метод [**TryAcquireLatestFrame**](https://docs.microsoft.com/uwp/api/windows.media.capture.frames.multisourcemediaframereader.TryAcquireLatestFrame). Получите метод **MediaFrameReference**, связанный с каждым источником мультимедийных кадров, вызвав [**TryGetFrameReferenceBySourceId**](https://docs.microsoft.com/uwp/api/windows.media.capture.frames.multisourcemediaframereference.trygetframereferencebysourceid) путем передачи строк идентификаторов, сохраненных при инициализации ридера кадров.

Вызовите метод [**Set**](https://msdn.microsoft.com/library/system.threading.manualreseteventslim.set.aspx) объекта **ManualResetEventSlim**, чтобы указать на поступление кадров. Мы проверим это событие в методе **NotifyCorrelationFailure**, который выполняется в отдельном потоке. 

Наконец, выполните обработку в коррелирующих по времени мультимедийных кадрах. В этом примере отображается кадр из источника глубины.

[!code-cs[MultiFrameArrived](./code/Frames_Win10/Frames_Win10/MainPage.xaml.cs#SnippetMultiFrameArrived)]

Вспомогательный метод **NotifyCorrelationFailure** выполнялся в отдельном потоке после запуска ридера кадров. В этом методе необходимо убедиться в том, что событие поступления кадра сигнализируется. Помните, что в обработчике **FrameArrived** мы задаем это событие при каждом получении набора коррелирующих кадров. Если событие не сигнализируется в течение некоторого определяемого приложением времени (целесообразно задать значение 5 секунд) и задача не отменена с помощью метода **CancellationToken**, высока вероятность, что один из источников мультимедийных кадров перестал считывать кадры. В этом случае рекомендуется завершить работу ридера кадров и вызвать определяемое приложением событие **CorrelationFailed**. В обработчике для этого события можно остановить ридер кадров и очистить соответствующие ресурсы, как показано ранее в этой статье.

[!code-cs[NotifyCorrelationFailure](./code/Frames_Win10/Frames_Win10/MainPage.xaml.cs#SnippetNotifyCorrelationFailure)]

[!code-cs[CorrelationFailure](./code/Frames_Win10/Frames_Win10/MainPage.xaml.cs#SnippetCorrelationFailure)]

## <a name="use-buffered-frame-acquisition-mode-to-preserve-the-sequence-of-acquired-frames"></a>Используйте буферизованный режим получения кадров, чтобы сохранить последовательность полученных кадров
Начиная с Windows 10 версии 1709, вы можете задать значение **Buffered** свойству **[AcquisitionMode](https://docs.microsoft.com/uwp/api/windows.media.capture.frames.mediaframereader.AcquisitionMode)** объекта **MediaFrameReader** или **MultiSourceMediaFrameReader**, чтобы переданная в приложение из источника последовательность кадров сохранялась.

[!code-cs[SetBufferedFrameAcquisitionMode](./code/Frames_Win10/Frames_Win10/MainPage.xaml.cs#SnippetSetBufferedFrameAcquisitionMode)]

В стандартном режиме получения кадров (**Realtime**) при получении из источника нескольких кадров в тот момент, когда ваше приложение все еще занято обработкой события **FrameArrived** для предыдущего кадра, то система отправит приложение самый последний полученный кадр и удалит остальные кадры, ожидающие обработки в буфере. Благодаря нововведению у приложения всегда есть доступ к последним полученным кадрам. Обычно такая функциональность нужна для приложений, реализующих компьютерное зрение в реальном времени. 

В режиме получения **Buffered** система держит все кадры в буфере и предоставляет их приложению с помощью события **FrameArrived** в порядке их поступления. Обратите внимание, что в этом режиме при заполнении системного буфера кадров, получение новых кадров будет остановлено до тех пока, пока приложение не завершит обработку события **FrameArrived**, тем самым освободив место в буфере.

## <a name="use-mediasource-to-display-frames-in-a-mediaplayerelement"></a>Использование MediaSource для отображения кадров в MediaPlayerElement
Начиная с Windows версии 1709, вы можете отображать кадры, полученные из **MediaFrameReader**, непосредственно в элементе управления **[MediaPlayerElement](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.mediaplayerelement)** XAML-страницы. Это реализуется с помощью метода **[MediaSource.CreateFromMediaFrameSource](https://docs.microsoft.com/uwp/api/windows.media.core.mediasource.createfrommediaframesource)**, в котором создается объект **[MediaSource](https://docs.microsoft.com/uwp/api/windows.media.core.mediasource)** Его может использовать непосредственно **[MediaPlayer](https://docs.microsoft.com/uwp/api/windows.media.playback.mediaplayer)**, связанный с **MediaPlayerElement**. Дополнительные сведения о работе **MediaPlayer** и **MediaPlayerElement** см. на странице [Воспроизведение аудио и видео с помощью MediaPlayer](play-audio-and-video-with-mediaplayer.md).

В следующих примерах кода показана простая реализация, в которой одновременно на XAML-странице отображаются кадры, полученные с передней и задней камеры.

Сперва, добавьте два элемента управления **MediaPlayerElement** на страницу XAML.

[!code-xml[MediaPlayerElement1XAML](./code/Frames_Win10/Frames_Win10/MainPage.xaml#SnippetMediaPlayerElement1XAML)]

[!code-xml[MediaPlayerElement2XAML](./code/Frames_Win10/Frames_Win10/MainPage.xaml#SnippetMediaPlayerElement2XAML)]

Затем, используя способы, описанные в предыдущих разделах этой статьи, выберите **MediaFrameSourceGroup**, содержащий объекты **MediaFrameSourceInfo** для цветных камер с передней и задней панели устройства. Обратите внимание, что **MediaPlayer** не выполняет автоматическое преобразование кадров из бесцветных форматов, например, с данными о глубине или с инфракрасным каналом , в цветной формат. Использование датчиков других типов может привести к непредсказуемым результатам. 

[!code-cs[MediaSourceSelectGroup](./code/Frames_Win10/Frames_Win10/MainPage.xaml.cs#SnippetMediaSourceSelectGroup)]

Инициализируйте объект **MediaCapture** так, чтобы в нем использовалась выбранная **MediaFrameSourceGroup**.

[!code-cs[MediaSourceInitMediaCapture](./code/Frames_Win10/Frames_Win10/MainPage.xaml.cs#SnippetMediaSourceInitMediaCapture)]

Наконец, вызовите метод **[MediaSource.CreateFromMediaFrameSource](https://docs.microsoft.com/uwp/api/windows.media.core.mediasource.createfrommediaframesource)**, чтобы создать **MediaSource** для каждого источника кадров, указав свойство **[Id](https://docs.microsoft.com/uwp/api/windows.media.capture.frames.mediaframesourceinfo.Id)** связанного объекта **MediaFrameSourceInfo** для выбора одного из источников кадров в коллекции **[FrameSources](https://docs.microsoft.com/uwp/api/windows.media.capture.mediacapture.FrameSources)** объекта **MediaCapture**. Инициализируйте новый объект **MediaPlayer** и назначьте его **MediaPlayerElement**, вызвав метод **[SetMediaPlayer](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.mediaplayerelement.MediaPlayer)**. Затем задайте свойство **[Source](https://docs.microsoft.com/uwp/api/windows.media.playback.mediaplayer.Source)** у созданного объекта **MediaSource**.

[!code-cs[MediaSourceMediaPlayer](./code/Frames_Win10/Frames_Win10/MainPage.xaml.cs#SnippetMediaSourceMediaPlayer)]

## <a name="use-video-profiles-to-select-a-frame-source"></a>Использование профилей видео для выбора источника кадров

Профиль камеры, представляемый объектом [**MediaCaptureVideoProfile**](https://msdn.microsoft.com/library/windows/apps/dn926694) — это набор возможностей, доступных в данном устройстве записи. К Это может быть, например, частота кадров, разрешение или дополнительные функции, такие как захват в HDR-формате. Устройство захвата может поддерживать несколько профилей, что позволяет выбрать тот, который лучше всего оптимизирован для вашего сценария захвата. Начиная с Windows 10 версии 1803, вы можете использовать класс **MediaCaptureVideoProfile** для выбора источника кадров мультимедиа с набором определенных возможностей до инициализации объекта **MediaCapture**. В следующем примере метод ищет видео профиль, который поддерживает HDR с широким диапазоном цвета (WCG), и возвращает объект **MediaCaptureInitializationSettings**, который можно передать в метод инициализации **MediaCapture** для использования выбранного устройства и профиля.

Сначала вызовите метод [**MediaFrameSourceGroup.FindAllAsync**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Capture.Frames.MediaFrameSourceGroup.FindAllAsync), чтобы получить список всех групп источников кадров мультимедиа, доступных на текущем устройстве. В цикле пройдитесь по каждой группе источников и вызовите метод [**MediaCapture.FindKnownVideoProfiles**](https://docs.microsoft.com/uwp/api/windows.media.capture.mediacapture.findknownvideoprofiles), чтобы получить список всех профилей видео для текущей группы источников, которые поддерживают указанный профиль (в данном случае это HDR с фотографией WCG). Если найден профиль, который соответствует критериям, создайте объект **MediaCaptureInitializationSettings** и укажите значение **VideoProfile**, чтобы выбрать профиль, а в значении **VideoDeviceId** свойства **Id** укажите текущую группу источников кадров мультимедиа.

[!code-cs[GetSettingsWithProfile](./code/Frames_Win10/Frames_Win10/MainPage.xaml.cs#SnippetGetSettingsWithProfile)]

Дополнительные сведения об использовании профилей камеры см. на странице [Профили камеры](camera-profiles.md).

## <a name="related-topics"></a>Статьи по теме

* [Камера](camera.md)
* [Основные принципы фото-, аудио- и видеозахвата с помощью MediaCapture](basic-photo-video-and-audio-capture-with-MediaCapture.md)
* [Пример кадров камеры](http://go.microsoft.com/fwlink/?LinkId=823230)
 

 





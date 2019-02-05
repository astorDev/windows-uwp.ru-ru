---
ms.assetid: D6A785C6-DF28-47E6-BDC1-7A7129EC40A0
description: В этой статье показано, как использовать класс MediaFrameReader и MediaCapture для получения кадров AudioFrames, содержащих звуковые данные из источника захвата.
title: Обработка аудиокадров с помощью MediaFrameReader
ms.date: 04/18/2018
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: e7082c3e572493656e8b109da35861ec820d4ec9
ms.sourcegitcommit: bf600a1fb5f7799961914f638061986d55f6ab12
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/05/2019
ms.locfileid: "9050837"
---
# <a name="process-audio-frames-with-mediaframereader"></a>Обработка аудиокадров с помощью MediaFrameReader

В этой статье показано, как использовать класс [**MediaFrameReader**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Capture.Frames.MediaFrameReader) и [**MediaCapture**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Capture.MediaCapture) для аудиоданных от источника кадров мультимедиа. Сведения об использовании **MediaFrameReader** для получения данных изображений, таких как цветная, инфракрасная камера или камера глубины, см. в разделе [Обработка кадров мультимедиа с помощью MediaFrameReader](process-media-frames-with-mediaframereader.md). Эта статья содержит общие сведения о шаблоне использования средства чтения кадров и описание некоторых дополнительных возможностей класса **MediaFrameReader**, таких как использование **MediaFrameSourceGroup** для получения кадров от нескольких источников в одно и то же время. 

> [!NOTE] 
> Описанные в этой статье функции доступны, только начиная c Windows 10 версии 1803.

> [!NOTE] 
> Существует пример универсального приложения для Windows, который демонстрирует использование **MediaFrameReader** для отображения кадров из разных источников, включая цветные и инфракрасные камеры и камеры с эффектом глубины. Дополнительные сведения см. в разделе [Пример кадров камеры](https://go.microsoft.com/fwlink/?LinkId=823230).

## <a name="setting-up-your-project"></a>Настройка проекта
Процесс получения аудиокадров во многом похож на получение других типов кадров мультимедиа. Как и в любом приложении, использующем **MediaCapture**, перед попыткой получить доступ к камере вам необходимо объявить, что ваше приложение использует возможность *webcam*. Если ваше приложение получает данные от звукового устройства, рекомендуется также объявить возможность устройства *microphone*. 

**Добавление возможностей в манифест приложения**

1.  В Microsoft Visual Studio откройте конструктор манифеста приложения, дважды щелкнув элемент **package.appxmanifest** в **Обозревателе решений**.
2.  Перейдите на вкладку **Возможности**.
3.  Выставьте флажок для пункта **Веб-камера** и поле для параметра **Микрофон**.
4.  Для доступа к библиотеке изображений и видео установите флажки **Библиотека изображений** и **Библиотека видео**.



## <a name="select-frame-sources-and-frame-source-groups"></a>Выбор источников кадров и групп источников кадров

Первым шагом при захвате аудиокадров — инициализация объекта [**MediaFrameSource**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Capture.Frames.MediaFrameSource), представляющего источник звуковых данных, такой как микрофон или другое устройство для записи звука. Для этого необходимо создать новый экземпляр объекта [**MediaCapture**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Capture.MediaCapture). В этом примере единственным параметром инициализации для **MediaCapture** является параметр [**StreamingCaptureMode**](https://docs.microsoft.com/uwp/api/windows.media.capture.mediacaptureinitializationsettings.streamingcapturemode), который указывается, что мы хотим осуществить потоковую передачу звука с устройства записи. 

После вызова метода [**MediaCapture.InitializeAsync**](https://docs.microsoft.com/uwp/api/windows.media.capture.mediacapture.initializeasync) можно получить список доступных источников кадров мультимедиа с помощью свойства [**FrameSources**](https://docs.microsoft.com/uwp/api/windows.media.capture.mediacapture.framesources). В этом примере запрос Linq используется для выбора всех источников кадров, в которых [**MediaFrameSourceInfo**](https://docs.microsoft.com/uwp/api/windows.media.capture.frames.mediaframesourceinfo), описывающий источник кадров, содержит свойство [**MediaStreamType**](https://docs.microsoft.com/uwp/api/windows.media.capture.frames.mediaframesourceinfo.mediastreamtype) со значением **Audio**, что указывает на, что источник мультимедиа создает звуковые данные.

Если запрос возвращает один или несколько источников кадров, вы можете просмотреть свойство [**CurrentFormat**](https://docs.microsoft.com/uwp/api/windows.media.capture.frames.mediaframesource.currentformat), чтобы узнать, поддерживает ли источник требуемый формат звука (в данном примере— аудио без сжатия). Проверьте свойство [**AudioEncodingProperties**](https://docs.microsoft.com/uwp/api/windows.media.capture.frames.mediaframeformat.audioencodingproperties), чтобы убедиться, что источник поддерживает требуемое кодирование.

[!code-cs[InitAudioFrameSource](./code/Frames_Win10/Frames_Win10/MainPage.xaml.cs#SnippetInitAudioFrameSource)]

## <a name="create-and-start-the-mediaframereader"></a>Создание и запуск MediaFrameReader

Получите новый экземпляр **MediaFrameReader**, вызвав метод [**MediaCapture.CreateFrameReaderAsync**](https://docs.microsoft.com/uwp/api/windows.media.capture.mediacapture.createframereaderasync#Windows_Media_Capture_MediaCapture_CreateFrameReaderAsync_Windows_Media_Capture_Frames_MediaFrameSource_) и передав объект **MediaFrameSource**, выбранный на предыдущем шаге. По умолчанию аудиокадры извлекаются в режиме буферизации, что снижает вероятность потери кадров, хотя это по-прежнему может произойти, если аудиокадры обрабатываются недостаточно быстро и заполняют доступный буфер памяти системы.

Зарегистрируйте обработчик для события [**MediaFrameReader.FrameArrived**](*https://docs.microsoft.com/uwp/api/windows.media.capture.frames.mediaframereader.framearrived), которое система вызывает при поступлении нового кадра аудиоданных. Вызовите метод [**StartAsync**](https://docs.microsoft.com/uwp/api/windows.media.capture.frames.mediaframereader.startasync), чтобы начать получать аудиокадры. Если средство чтения кадров не запускается, возвращенное значение состояния будет отличаться от [**Success**](https://docs.microsoft.com/uwp/api/windows.media.capture.frames.mediaframereaderstartstatus).

[!code-cs[CreateAudioFrameReader](./code/Frames_Win10/Frames_Win10/MainPage.xaml.cs#SnippetCreateAudioFrameReader)]

В обработчике событий **FrameArrived** вызовите метод [**TryAcquireLatestFrame**](https://docs.microsoft.com/uwp/api/windows.media.capture.frames.mediaframereader.tryacquirelatestframe) для объекта **MediaFrameReader**, который передается обработчику в качестве отправителя, чтобы попытаться получить ссылку на последний кадр мультимедиа. Обратите внимание, что этот объект может иметь значение null, поэтому всегда следует это проверять перед использованием объекта. Тип кадра мультимедиа в объекте **MediaFrameReference**, возвращенном **TryAcquireLatestFrame**, зависит от того, какой тип источника или источников кадров выбран в средстве чтения кадров. Так как средство чтения кадров в этом примере было настроено для получения аудиокадров, оно получает базовый кадр с помощью свойства [**AudioMediaFrame**](https://docs.microsoft.com/uwp/api/windows.media.capture.frames.mediaframereference.audiomediaframe). 

Во вспомогательном методе **ProcessAudioFrame** в примере ниже показано, как получить объект [**AudioFrame**](https://docs.microsoft.com/uwp/api/windows.media.audioframe), который предоставляет такие сведения, как метка времени кадра и отсутствие связи с объектом **AudioMediaFrame**. Чтобы прочитать или обработать аудиоданные, необходимо получить объект [**AudioBuffer**](https://docs.microsoft.com/uwp/api/windows.media.audiobuffer) из объекта **AudioMediaFrame**, создать [**IMemoryBufferReference**](https://docs.microsoft.com/uwp/api/windows.foundation.imemorybufferreference) и вызвать COM-метод **IMemoryBufferByteAccess::GetBuffer** для извлечения данных. Дополнительные сведения о доступе к собственным буферам см. в примечании под примером кода.

Формат данных зависит от источника кадров. В этом примере при выборе источника кадров мультимедиа мы явно убедились, что выбранный источник кадров использует один канал данных с плавающей запятой. В остальной части примера кода показано, как определить длительность и число выборок аудиоданных в кадре.  

[!code-cs[ProcessAudioFrame](./code/Frames_Win10/Frames_Win10/MainPage.xaml.cs#SnippetProcessAudioFrame)]

> [!NOTE] 
> Для работы с аудиоданными необходимо получить доступ к собственному буферу памяти. Для этого нужно использовать COM-интерфейс **IMemoryBufferByteAccess**, добавив следующий код. Операции с собственным буфером должны выполняться в методе, который использует ключевое слово **unsafe**. Вам также необходимо установить флажок, чтобы разрешить использование небезопасного кода, на вкладке **Сборка** в диалоговом окне **Проект -> Свойства**.

[!code-cs[IMemoryBufferByteAccess](./code/Frames_Win10/Frames_Win10/FrameRenderer.cs#SnippetIMemoryBufferByteAccess)]

## <a name="additional-information-on-using-mediaframereader-with-audio-data"></a>Дополнительные сведения об использовании MediaFrameReader с аудиоданными

Вы можете получить объект [**AudioDeviceController**](https://docs.microsoft.com/uwp/api/Windows.Media.Devices.AudioDeviceController), связанный с источником аудиокадра, с помощью свойства [**MediaFrameSource.Controller**](https://docs.microsoft.com/uwp/api/windows.media.capture.frames.mediaframesource.controller). Этот объект можно использовать для получения или установки свойств потока устройства записи или для управления уровнем записи. Следующий пример отключает звуковое устройство, чтобы средство чтения кадров по-прежнему могло получать кадры, но все выборки имеют значение 0.

[!code-cs[AudioDeviceControllerMute](./code/Frames_Win10/Frames_Win10/MainPage.xaml.cs#SnippetAudioDeviceControllerMute)]

Вы можете использовать объект [**AudioFrame**](https://docs.microsoft.com/uwp/api/windows.media.audioframe) для передачи аудиоданных, записанных источником кадров мультимедиа объекту [**AudioGraph**](https://docs.microsoft.com/uwp/api/windows.media.audio.audiograph). Передайте кадр в методу [**AddFrame**](https://docs.microsoft.com/uwp/api/windows.media.audio.audioframeinputnode.addframe) объекта [**AudioFrameInputNode**](https://docs.microsoft.com/en-us/uwp/api/windows.media.audio.audioframeinputnode). Дополнительные сведения об использовании аудиографов для записи, обработки и смешивания звуковых сигналов см. в разделе [Звуковые графы](audio-graphs.md).

## <a name="related-topics"></a>Статьи по теме

* [Обработка кадров мультимедиа с помощью MediaFrameReader](process-media-frames-with-mediaframereader.md)
* [Камера](camera.md)
* [Основные принципы фото-, аудио- и видеозахвата с помощью MediaCapture](basic-photo-video-and-audio-capture-with-MediaCapture.md)
* [Пример кадров камеры](https://go.microsoft.com/fwlink/?LinkId=823230)
* [Звуковые графы](audio-graphs.md)
 







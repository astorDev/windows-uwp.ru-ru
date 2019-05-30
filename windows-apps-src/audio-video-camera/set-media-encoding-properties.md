---
ms.assetid: 09BA9250-A476-4803-910E-52F0A51704B1
description: В этой статье рассказывается, как с помощью интерфейса IMediaEncodingProperties задать разрешение и частоту кадров потока предварительного просмотра камеры, а также снятых фотографий и видео.
title: Установка формата, разрешения и частоты кадров для MediaCapture
ms.date: 02/08/2017
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: 044d759d2e62dedf9660f2536eca9064dbf8315b
ms.sourcegitcommit: ac7f3422f8d83618f9b6b5615a37f8e5c115b3c4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/29/2019
ms.locfileid: "66361403"
---
# <a name="set-format-resolution-and-frame-rate-for-mediacapture"></a>Установка формата, разрешения и частоты кадров для MediaCapture



В этой статье рассказывается, как с помощью интерфейса [**IMediaEncodingProperties**](https://docs.microsoft.com/uwp/api/Windows.Media.MediaProperties.IMediaEncodingProperties) задать разрешение и частоту кадров потока предварительного просмотра камеры, а также снятых фотографий и видео. Кроме того, здесь рассказывается, как обеспечить соответствие пропорций потока предварительного просмотра пропорциям записи мультимедиа.

Профили камеры предлагают более современный способ обнаружения и задания свойств потока камеры, но они поддерживаются не на всех устройствах. Дополнительные сведения см. в разделе [Профили камеры](camera-profiles.md).

В этой статье используется код, адаптированный из [примера CameraResolution](https://go.microsoft.com/fwlink/p/?LinkId=624252&clcid=0x409). Вы можете скачать этот пример, чтобы просмотреть код в контексте или использовать пример как отправную точку для настройки собственного приложения.

> [!NOTE] 
> В этой статье используются понятия и код из статьи [Основные принципы фото-, аудио- и видеозахвата с помощью MediaCapture](basic-photo-video-and-audio-capture-with-MediaCapture.md), в которой описаны этапы реализации основных принципов фото- и видеозахвата. Мы рекомендуем ознакомиться с базовым шаблоном захвата мультимедиа в этой статье, прежде чем перейти к более сложным сценариям захвата. Код в этой статье подразумевает, что ваше приложение уже содержит экземпляр MediaCapture, инициализированный надлежащим образом.

## <a name="a-media-encoding-properties-helper-class"></a>Вспомогательный класс свойств кодирования мультимедиа

Если создать простой вспомогательный класс, включающий функции интерфейса [**IMediaEncodingProperties**](https://docs.microsoft.com/uwp/api/Windows.Media.MediaProperties.IMediaEncodingProperties), будет удобнее выбирать набор свойств кодирования, которые отвечают конкретным критериям. Этот вспомогательный класс особенно полезен для следующего поведения свойств кодирования.

**Предупреждение**    [ **VideoDeviceController.GetAvailableMediaStreamProperties** ](https://docs.microsoft.com/uwp/api/windows.media.devices.videodevicecontroller.getavailablemediastreamproperties) метод принимает членом [ **MediaStreamType**  ](https://docs.microsoft.com/uwp/api/Windows.Media.Capture.MediaStreamType) перечисления, таких как **VideoRecord** или **Photo**и возвращает список либо [ **ImageEncodingProperties** ](https://docs.microsoft.com/uwp/api/Windows.Media.MediaProperties.ImageEncodingProperties) или [ **VideoEncodingProperties** ](https://docs.microsoft.com/uwp/api/Windows.Media.MediaProperties.VideoEncodingProperties) объекты, которые передают параметры кодировки, например разрешения записанного фото или видео потока. Результаты вызова **GetAvailableMediaStreamProperties** могут включать **ImageEncodingProperties** или **VideoEncodingProperties**, независимо от того, какое указано значение **MediaStreamType**. По этой причине необходимо всегда проверять тип каждого возвращенного значения и приводить его к соответствующему типу, прежде чем пытаться получить доступ к какому-либо значению свойства.

Вспомогательный класс, определенный ниже, выполняет проверку и приведение типа для параметра [**ImageEncodingProperties**](https://docs.microsoft.com/uwp/api/Windows.Media.MediaProperties.ImageEncodingProperties) или [**VideoEncodingProperties**](https://docs.microsoft.com/uwp/api/Windows.Media.MediaProperties.VideoEncodingProperties), чтобы коду приложения не нужно было различать два типа. В дополнение к этому, вспомогательный класс представляет свойства для пропорций свойств, частоты кадров (только для свойств кодирования видео) и понятного имени, что упрощает отображение свойств кодирования в пользовательском интерфейсе приложения.

Вы должны включить пространство имен [**Windows.Media.MediaProperties**](https://docs.microsoft.com/uwp/api/Windows.Media.MediaProperties) в исходный файл для вспомогательного класса.

[!code-cs[MediaEncodingPropertiesUsing](./code/BasicMediaCaptureWin10/cs/MainPage.xaml.cs#SnippetMediaEncodingPropertiesUsing)]

[!code-cs[StreamPropertiesHelper](./code/BasicMediaCaptureWin10/cs/StreamPropertiesHelper.cs#SnippetStreamPropertiesHelper)]

## <a name="determine-if-the-preview-and-capture-streams-are-independent"></a>Определение независимости потоков предварительного просмотра и захвата

На некоторых устройствах один и тот же контакт оборудования используется как для потока предварительного просмотра, так и для потока захвата. На таких устройствах при задании свойств кодирования для одного потока свойства задаются и для другого потока. На устройствах, на которых для захвата и предварительного просмотра используются разные контакты, свойства можно задать для каждого потока независимо. Используйте следующий код, чтобы определить, независимы ли потоки предварительного просмотра и захвата. Необходимо настроить пользовательский интерфейс, чтобы он включал и отключал параметр потоков независимо на основе результата этой проверки.

[!code-cs[CheckIfStreamsAreIdentical](./code/BasicMediaCaptureWin10/cs/MainPage.xaml.cs#SnippetCheckIfStreamsAreIdentical)]

## <a name="get-a-list-of-available-stream-properties"></a>Получение списка доступных свойств потока

Чтобы получить список доступных свойств потока для устройства захвата, получите [**VideoDeviceController**](https://docs.microsoft.com/uwp/api/Windows.Media.Devices.VideoDeviceController) для объекта [MediaCapture](capture-photos-and-video-with-mediacapture.md) своего приложения, а затем вызовите [**GetAvailableMediaStreamProperties**](https://docs.microsoft.com/uwp/api/windows.media.devices.videodevicecontroller.getavailablemediastreamproperties) и передайте одно из значений [**MediaStreamType**](https://docs.microsoft.com/uwp/api/Windows.Media.Capture.MediaStreamType), **VideoPreview**, **VideoRecord** или **Photo**. В этом примере синтаксис Linq используется для создания списка объектов **StreamPropertiesHelper**, определенных ранее в этой статье, для каждого из значений [**IMediaEncodingProperties**](https://docs.microsoft.com/uwp/api/Windows.Media.MediaProperties.IMediaEncodingProperties), которые возвращаются функцией **GetAvailableMediaStreamProperties**. В этом примере методы расширения Linq сначала используются для расстановки возвращаемых свойств сперва на основе разрешения, а потом уже на основе частоты кадров.

Если у вашего приложения есть конкретные требования к разрешению или частоте кадров, можно выбрать набор свойств кодирования мультимедиа программным путем. Тогда обычное приложение камеры отобразит список доступных свойств в пользовательском интерфейсе и позволит пользователю выбрать нужные параметры. **ComboBoxItem** создается для каждого элемента в списке объектов **StreamPropertiesHelper** в списке. Для содержимого устанавливается понятное имя, возвращенное вспомогательным классом, а для тега устанавливается сам вспомогательный класс, чтобы его можно было использовать позже для получения связанных свойств кодирования. Каждый элемент **ComboBoxItem** затем добавляется в объект **ComboBox**, который передается в метод.

[!code-cs[PopulateStreamPropertiesUI](./code/BasicMediaCaptureWin10/cs/MainPage.xaml.cs#SnippetPopulateStreamPropertiesUI)]

## <a name="set-the-desired-stream-properties"></a>Установка необходимых свойств потока

Чтобы контроллер видеоустройств использовал нужные свойства кодирования, вызовите [**SetMediaStreamPropertiesAsync**](https://docs.microsoft.com/uwp/api/windows.media.devices.videodevicecontroller.setmediastreampropertiesasync) и передайте значение **MediaStreamType**, определяющее, нужно ли задавать свойства фотографии, видео или предварительного просмотра. В этом примере задаются запрашиваемые свойства кодирования, когда пользователь выбирает элемент в одном из объектов **ComboBox**, которые заполняются вспомогательным методом **PopulateStreamPropertiesUI**.

[!code-cs[PreviewSettingsChanged](./code/BasicMediaCaptureWin10/cs/MainPage.xaml.cs#SnippetPreviewSettingsChanged)]

[!code-cs[PhotoSettingsChanged](./code/BasicMediaCaptureWin10/cs/MainPage.xaml.cs#SnippetPhotoSettingsChanged)]

[!code-cs[VideoSettingsChanged](./code/BasicMediaCaptureWin10/cs/MainPage.xaml.cs#SnippetVideoSettingsChanged)]

## <a name="match-the-aspect-ratio-of-the-preview-and-capture-streams"></a>Сопоставление пропорций потоков предварительного просмотра и захвата

Типичное приложение камеры предоставляет пользовательский интерфейс для выбора разрешения захвата видео или фото, но разрешение предварительного просмотра устанавливает программным способом. Существуют несколько стратегий выбора наилучшего разрешения потока предварительного просмотра для вашего приложения.

-   Выберите наивысшее доступное разрешение предварительного просмотра, предоставив инфраструктуре пользовательского интерфейса выполнять необходимое масштабирование предварительного просмотра.

-   Выберите разрешение предварительного просмотра, наиболее близкое к разрешению захвата, чтобы предварительный просмотр был как можно ближе к итоговому захваченному мультимедиа.

-   Выберите разрешение предварительного просмотра, наиболее близкое к размеру [**CaptureElement**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.CaptureElement), чтобы через конвейер потока предварительного просмотра проходило столько пикселов, сколько нужно, но не более того.

**Важные**    это возможно, на некоторых устройствах, чтобы другие пропорции для камеры поток предварительного просмотра и записи потока. Обрезка кадра в результате такого несоответствия может привести к наличию в записи мультимедиа содержимого, которого не было видно в режиме предварительного просмотра, что может вызвать недовольство пользователей. Настоятельно рекомендуется использовать одинаковые пропорции (с небольшим диапазоном допуска) для потоков предварительного просмотра и захвата. Можно иметь совершенно различные разрешения, доступные для захвата и предварительного просмотра, пока их пропорции максимально соответствуют друг другу.


Чтобы обеспечить соответствие потоков фото- и видеозахвата пропорциям потока предварительного просмотра, в этом примере вызывается [**VideoDeviceController.GetMediaStreamProperties**](https://docs.microsoft.com/uwp/api/windows.media.devices.videodevicecontroller.getmediastreamproperties) и передается значение перечисления **VideoPreview** для запроса текущих свойств потока предварительного просмотра. Затем определяется небольшой диапазон допуска для пропорций, чтобы можно было включать пропорции, которые не совпадают с потоком предварительного просмотра полностью, но которые близки к этому. Далее метод расширения Linq используется для выбора только тех объектов **StreamPropertiesHelper**, пропорции которых входят в указанный диапазон допуска потока предварительного просмотра.

[!code-cs[MatchPreviewAspectRatio](./code/BasicMediaCaptureWin10/cs/MainPage.xaml.cs#SnippetMatchPreviewAspectRatio)]

 

 





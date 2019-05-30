---
ms.assetid: 7DBEE5E2-C3EC-4305-823D-9095C761A1CD
description: В данной статье рассказывается, как захватить переменную последовательность фотографий (то есть несколько снимков, быстро идущих один за другим) и настроить для каждого снимка различные параметры фокусировки, вспышки, ISO, экспозиции и коррекции экспозиции.
title: Переменная последовательность фотографий
ms.date: 02/08/2017
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: 506ea5f7fe199c3df0b6089da73a108d53d98ef3
ms.sourcegitcommit: ac7f3422f8d83618f9b6b5615a37f8e5c115b3c4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/29/2019
ms.locfileid: "66361378"
---
# <a name="variable-photo-sequence"></a>Переменная последовательность фотографий



В данной статье рассказывается, как захватить переменную последовательность фотографий (то есть несколько снимков, быстро идущих один за другим) и настроить для каждого снимка различные параметры фокусировки, вспышки, ISO, экспозиции и коррекции экспозиции. Эта функция позволяет использовать такие сценарии, как создание изображений с помощью технологии High Dynamic Range (HDR).

Если вам необходимо захватывать изображения с использованием технологии HDR, но вы не хотите создавать собственный алгоритм обработки, вы можете применять API [**AdvancedPhotoCapture**](https://docs.microsoft.com/uwp/api/Windows.Media.Capture.AdvancedPhotoCapture), чтобы использовать встроенные в ОС Windows возможности HDR. Дополнительные сведения см. в статье [Захват фотографий с использованием технологии High Dynamic Range (HDR)](high-dynamic-range-hdr-photo-capture.md).

> [!NOTE] 
> В этой статье используются понятия и код из статьи [Основные принципы фото-, аудио- и видеозахвата с помощью MediaCapture](basic-photo-video-and-audio-capture-with-MediaCapture.md), в которой описаны этапы реализации основных принципов фото- и видеозахвата. Мы рекомендуем ознакомиться с базовым шаблоном захвата мультимедиа в этой статье, прежде чем перейти к более сложным сценариям захвата. Код в этой статье подразумевает, что ваше приложение уже содержит экземпляр MediaCapture, инициализированный надлежащим образом.

## <a name="set-up-your-app-to-use-variable-photo-sequence-capture"></a>Настройка приложения для захвата переменной последовательности фотографий

Помимо пространств имен, необходимых для основных функций захвата мультимедиа, для реализации функции захвата переменной последовательности фотографий необходимы указанные ниже пространства имен.

[!code-cs[VPSUsing](./code/BasicMediaCaptureWin10/cs/MainPage.xaml.cs#SnippetVPSUsing)]

Объявите переменную-член для хранения объекта [**VariablePhotoSequenceCapture**](https://docs.microsoft.com/uwp/api/Windows.Media.Capture.Core.VariablePhotoSequenceCapture), который используется для инициализации функции захвата последовательности фотографий. Объявите массив объектов [**SoftwareBitmap**](https://docs.microsoft.com/uwp/api/Windows.Graphics.Imaging.SoftwareBitmap) для хранения каждого захваченного изображения в последовательности. Кроме того, объявите массив для хранения объекта [**CapturedFrameControlValues**](https://docs.microsoft.com/uwp/api/Windows.Media.Capture.CapturedFrameControlValues) для каждого кадра. Он может использоваться в вашем алгоритме обработки изображений для определения того, какие параметры были использованы для захвата каждого кадра. И, наконец, объявите индекс, который будет использоваться для отслеживания того, какое изображение в последовательности захватывается в данный момент.

[!code-cs[VPSMemberVariables](./code/BasicMediaCaptureWin10/cs/MainPage.xaml.cs#SnippetVPSMemberVariables)]

## <a name="prepare-the-variable-photo-sequence-capture"></a>Подготовка к захвату переменной последовательности фотографий

После успешной инициализации [MediaCapture](capture-photos-and-video-with-mediacapture.md) убедитесь, что текущее устройство поддерживает захват переменных последовательностей фотографий. Для этого получите экземпляр [**VariablePhotoSequenceController**](https://docs.microsoft.com/uwp/api/Windows.Media.Devices.Core.VariablePhotoSequenceController) из объекта [**VideoDeviceController**](https://docs.microsoft.com/uwp/api/Windows.Media.Devices.VideoDeviceController) для захвата мультимедиа и проверьте свойство [**Supported**](https://docs.microsoft.com/uwp/api/windows.media.devices.core.variablephotosequencecontroller.supported).

[!code-cs[IsVPSSupported](./code/BasicMediaCaptureWin10/cs/MainPage.xaml.cs#SnippetIsVPSSupported)]

Получите объект [**FrameControlCapabilities**](https://docs.microsoft.com/uwp/api/Windows.Media.Devices.Core.FrameControlCapabilities) из контроллера переменной последовательности фотографий. У этого объекта имеется свойство для каждого параметра, который можно настроить для каждого кадра в последовательности фотографий. К ним можно отнести следующие.

-   [**Раскрытие**](https://docs.microsoft.com/uwp/api/windows.media.devices.core.framecontrolcapabilities.exposure)
-   [**ExposureCompensation**](https://docs.microsoft.com/uwp/api/windows.media.devices.core.framecontrolcapabilities.exposurecompensation)
-   [**Flash**](https://docs.microsoft.com/uwp/api/windows.media.devices.core.framecontrolcapabilities.flash)
-   [**Фокус**](https://docs.microsoft.com/uwp/api/windows.media.devices.core.framecontrolcapabilities.focus)
-   [**IsoSpeed**](https://docs.microsoft.com/uwp/api/windows.media.devices.core.framecontrolcapabilities.isospeed)
-   [**PhotoConfirmation**](https://docs.microsoft.com/uwp/api/windows.media.devices.core.framecontrolcapabilities.photoconfirmationsupported)

В этом примере показано, как настроить разные значения коррекции экспозиции для каждого кадра. Чтобы убедиться, что функция коррекции экспозиции поддерживается для последовательности фотографий в текущем устройстве, проверьте свойство [**Supported**](https://docs.microsoft.com/uwp/api/windows.media.devices.exposurecompensationcontrol.supported) объекта [**FrameExposureCompensationCapabilities**](https://docs.microsoft.com/uwp/api/Windows.Media.Devices.Core.FrameExposureCompensationCapabilities), к которому можно получить доступ с помощью свойства **ExposureCompensation**.

[!code-cs[IsExposureCompensationSupported](./code/BasicMediaCaptureWin10/cs/MainPage.xaml.cs#SnippetIsExposureCompensationSupported)]

Создайте объект [**FrameController**](https://docs.microsoft.com/uwp/api/Windows.Media.Devices.Core.FrameController) для каждого кадра, который необходимо захватить. В этом примере выполняется захват трех кадров. Задайте значения для элементов управления, которые необходимо изменять для каждого кадра. Затем очистите коллекцию [**DesiredFrameControllers**](https://docs.microsoft.com/uwp/api/windows.media.devices.core.variablephotosequencecontroller.desiredframecontrollers) объекта **VariablePhotoSequenceController** и добавьте в коллекцию контроллер каждого кадра.

[!code-cs[InitFrameControllers](./code/BasicMediaCaptureWin10/cs/MainPage.xaml.cs#SnippetInitFrameControllers)]

Создайте объект [**ImageEncodingProperties**](https://docs.microsoft.com/uwp/api/Windows.Media.MediaProperties.ImageEncodingProperties), чтобы настроить кодировку, которую необходимо использовать для захваченных изображений. Вызовите статический метод [**MediaCapture.PrepareVariablePhotoSequenceCaptureAsync**](https://docs.microsoft.com/uwp/api/windows.media.capture.mediacapture.preparevariablephotosequencecaptureasync), передав ему свойства кодирования. Этот метод возвращает объект [**VariablePhotoSequenceCapture**](https://docs.microsoft.com/uwp/api/Windows.Media.Capture.Core.VariablePhotoSequenceCapture). И, наконец, зарегистрируйте обработчики событий [**PhotoCaptured**](https://docs.microsoft.com/uwp/api/windows.media.capture.core.variablephotosequencecapture.photocaptured) и [**Stopped**](https://docs.microsoft.com/uwp/api/windows.media.capture.core.variablephotosequencecapture.stopped).

[!code-cs[PrepareVPS](./code/BasicMediaCaptureWin10/cs/MainPage.xaml.cs#SnippetPrepareVPS)]

## <a name="start-the-variable-photo-sequence-capture"></a>Запуск захвата переменной последовательности фотографий

Чтобы запустить захват переменной последовательности фотографий, вызовите метод [**VariablePhotoSequenceCapture.StartAsync**](https://docs.microsoft.com/uwp/api/windows.media.capture.core.variablephotosequencecapture.startasync). Обязательно инициализируйте массивы для хранения захваченных изображений и значений элемента управления для работы с кадрами и присвойте текущему индексу значение 0. В приложении настройте переменную состояния записи и обновите пользовательский интерфейс, чтобы в процессе захвата невозможно было запустить еще одну операцию захвата.

[!code-cs[StartVPSCapture](./code/BasicMediaCaptureWin10/cs/MainPage.xaml.cs#SnippetStartVPSCapture)]

## <a name="receive-the-captured-frames"></a>Получение захваченных кадров

Для каждого захваченного кадра создается событие [**PhotoCaptured**](https://docs.microsoft.com/uwp/api/windows.media.capture.core.variablephotosequencecapture.photocaptured). Сохраните значения элемента управления для работы с кадрами и захваченное изображение для кадра, а затем увеличьте текущий индекс кадра на единицу. В этом примере показано, как получить представление [**SoftwareBitmap**](https://docs.microsoft.com/uwp/api/Windows.Graphics.Imaging.SoftwareBitmap) для каждого кадра. Дополнительные сведения об использовании **SoftwareBitmap** см. в статье [Обработка изображений](imaging.md).

[!code-cs[OnPhotoCaptured](./code/BasicMediaCaptureWin10/cs/MainPage.xaml.cs#SnippetOnPhotoCaptured)]

## <a name="handle-the-completion-of-the-variable-photo-sequence-capture"></a>Обработка завершения захвата переменной последовательности фотографий

После захвата всех кадров последовательности создается событие [**Stopped**](https://docs.microsoft.com/uwp/api/windows.media.capture.core.variablephotosequencecapture.stopped). Обновите состояние записи вашего приложения и пользовательский интерфейс, чтобы пользователь мог инициировать новые операции захвата. На этом этапе можно передать захваченные изображения и значения элемента управления для работы с кадрами в код обработки изображений.

[!code-cs[OnStopped](./code/BasicMediaCaptureWin10/cs/MainPage.xaml.cs#SnippetOnStopped)]

## <a name="update-frame-controllers"></a>Обновление контроллеров кадров

Если вам необходимо захватить еще одну переменную последовательность фотографий с различными параметрами для каждого кадра, то нет необходимости выполнять полную повторную инициализацию **VariablePhotoSequenceCapture**. Достаточно либо очистить коллекцию [**DesiredFrameControllers**](https://docs.microsoft.com/uwp/api/windows.media.devices.core.variablephotosequencecontroller.desiredframecontrollers) и добавить новые контроллеры кадров, либо изменить существующие значения контроллера кадра. В примере ниже показано, как проверить объект [**FrameFlashCapabilities**](https://docs.microsoft.com/uwp/api/Windows.Media.Devices.Core.FrameFlashCapabilities), чтобы убедиться, что текущее устройство поддерживает вспышку и управление ее мощностью для кадров переменной последовательности фотографий. Если это так, то каждый кадр будет обновлен, чтобы использовать вспышку на 100 % ее мощности. Значения коррекции экспозиции, ранее установленные для каждого кадра, будут по-прежнему активными.

[!code-cs[UpdateFrameControllers](./code/BasicMediaCaptureWin10/cs/MainPage.xaml.cs#SnippetUpdateFrameControllers)]

## <a name="clean-up-the-variable-photo-sequence-capture"></a>Очистка захвата переменной последовательности фотографий

После окончания захвата переменных последовательностей фотографий или в случае приостановки вашего приложения очистите объект переменной последовательности фотографий, вызвав метод [**FinishAsync**](https://docs.microsoft.com/uwp/api/windows.media.capture.core.variablephotosequencecapture.finishasync). Отмените регистрацию обработчиков событий для объекта и присвойте ему значение null.

[!code-cs[CleanUpVPS](./code/BasicMediaCaptureWin10/cs/MainPage.xaml.cs#SnippetCleanUpVPS)]

## <a name="related-topics"></a>См. также

* [Камера](camera.md)
* [Основные фото, видео и аудио захвата с MediaCapture](basic-photo-video-and-audio-capture-with-MediaCapture.md)
 

 





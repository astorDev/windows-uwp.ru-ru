---
author: TerryWarwick
title: Размещение предварительного изображения со сканера штрихкодов на базе камеры
description: Узнайте, как разместить в своем приложении предварительное изображение со сканера штрихкодов на базе камеры.
ms.author: jken
ms.date: 05/1/2018
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: Windows 10, UWP, точка обслуживания, POS
ms.localizationpriority: medium
ms.openlocfilehash: 4e5765a725ad99a1092ad8c56cef674ec6210a2c
ms.sourcegitcommit: ab92c3e0dd294a36e7f65cf82522ec621699db87
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/03/2018
ms.locfileid: "1833320"
---
# <a name="hosting-a-camera-barcode-scanner-preview-in-your-application"></a>Размещение в приложении предварительного изображения со сканера штрихкодов на базе камеры
## <a name="step-1-setup-your-camera-preview"></a>Шаг 1. Настройка предварительного изображения с камеры
Первый шаг, необходимый для добавления в приложение предварительного изображения со сканера штрихкодов на базе камеры, можно выполнить, следуя инструкциям в статье [Отображение предварительного изображения с камеры](../audio-video-camera/simple-camera-preview-access.md).  Выполнив этот шаг, вернитесь к этой статье для внесения изменений, связанных с использованием камеры в качестве сканера штрихкодов.

## <a name="step-2-update-capability-declarations"></a>Шаг 2. Обновление объявлений возможностей
Чтобы ваши пользователи не получали запрос согласия на использование микрофона, вы можете исключить его из возможностей, перечисленных в манифесте вашего приложения.

1. В Microsoft Visual Studio откройте конструктор манифеста приложения, дважды щелкнув элемент **package.appxmanifest** в **Обозревателе решений**.
2. Перейдите на вкладку **Возможности**.
3. Снимите флажок **Микрофон**.

 ## <a name="step-3-add-additional-using-directive-for-media-capture"></a>Шаг 3. Добавление дополнительной директивы using для захвата мультимедиа

```Csharp
using Windows.Media.Capture;
```

## <a name="step-4-set-up-your-mediacapture-initialization-settings"></a>Шаг 4. Настройка параметров инициализации MediaCapture
В приведенном ниже инициализируется [**MediaCaptureInitializationSettings**](https://docs.microsoft.com/uwp/api/windows.media.capture.mediacaptureinitializationsettings). 

```Csharp
 private void InitCaptureSettings()
{
    _captureInitSettings = new MediaCaptureInitializationSettings();
    _captureInitSettings.VideoDeviceId = ClaimedBarcodeScanner.VideoDeviceId;
    _captureInitSettings.StreamingCaptureMode = StreamingCaptureMode.Video;
    _captureInitSettings.PhotoCaptureSource = PhotoCaptureSource.VideoPreview;
    
    if (_deviceList.Count > 0)
    {
        _captureInitSettings.VideoDeviceId = _deviceList[0].Id;
    }
}
```
## <a name="step-5-associate-your-mediacapture-object-with-the-camera-barcode-scanner"></a>Шаг 5. Связывание объекта MediaCapture со сканером штрихкодов на базе камеры
Замените существующий метод mediaCapture.InitializeAsync() в *StartPreviewAsync()* следующим кодом:

```Csharp
try
    {

        mediaCapture = new MediaCapture();
        await mediaCapture.InitializeAsync(InitCaptureSettings());

        displayRequest.RequestActive();
        DisplayInformation.AutoRotationPreferences = DisplayOrientations.Landscape;
    }
```

> [!TIP]
> Более сложные схемы размещения предварительного изображения с камеры см. в разделе [Отображение просмотра камеры](https://docs.microsoft.com/windows/uwp/audio-video-camera/simple-camera-preview-access#add-capability-declarations-to-the-app-manifest).
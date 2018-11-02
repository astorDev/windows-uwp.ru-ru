---
author: PatrickFarley
ms.assetid: 374D1983-60E0-4E18-ABBB-04775BAA0F0D
title: Сканирование из приложения
description: Узнайте, как сканировать содержимое из приложения с помощью планшетного сканера, сканера с модулем подачи или автоматически настраиваемого источника сканирования.
ms.author: pafarley
ms.date: 02/08/2017
ms.topic: article
keywords: Windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: f9128056cbb3b9218d164b243948d9dd16af0786
ms.sourcegitcommit: 144f5f127fc4fbd852f2f6780ef26054192d68fc
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/02/2018
ms.locfileid: "5977440"
---
# <a name="scan-from-your-app"></a>Сканирование из приложения


**Важные API**

-   [**Windows.Devices.Scanners**](https://msdn.microsoft.com/library/windows/apps/Dn264250)
-   [**DeviceInformation**](https://msdn.microsoft.com/library/windows/apps/BR225393)
-   [**DeviceClass**](https://msdn.microsoft.com/library/windows/apps/BR225381)

Узнайте, как сканировать содержимое из приложения с помощью планшетного сканера, сканера с модулем подачи или автоматически настраиваемого источника сканирования.

**Важные** [**Windows.Devices.Scanners**](https://msdn.microsoft.com/library/windows/apps/Dn264250) API-интерфейсы являются частью рабочего стола [семейства устройств](https://msdn.microsoft.com/library/windows/apps/Dn894631). Приложения могут использовать эти API только в настольной версии Windows10.

Перед сканированием из приложения необходимо составить список доступных сканеров, объявив новый объект [**DeviceInformation**](https://msdn.microsoft.com/library/windows/apps/BR225393) и получив тип [**DeviceClass**](https://msdn.microsoft.com/library/windows/apps/BR225381). В список включаются только сканеры, установленные локально с драйверами WIA. Приложению будут доступны только такие сканеры.

После составления списка доступных сканеров ваше приложение может использовать автоматически настраиваемые параметры сканирования, зависящие от типа сканера, или просто сканировать с помощью доступного планшетного сканера или источника сканирования с модулем подачи. Для использования автоматически настраиваемых параметров сканер должен поддерживать автоматическую настройку и не может одновременно быть планшетным и иметь модуль подачи. Узнать больше об этом можно в разделе [автоматической настройке сканирования](https://msdn.microsoft.com/library/windows/hardware/Ff539393).

## <a name="enumerate-available-scanners"></a>Перечисление доступных сканеров

Windows не определяет сканеры автоматически. Чтобы ваше приложение могло взаимодействовать со сканером, необходимо выполнить следующие шаги. В данном примере перечисление сканеров выполняется с использованием пространства имен [**Windows.Devices.Enumeration**](https://msdn.microsoft.com/library/windows/apps/BR225459).

1.  Сначала добавьте эти операторы использования в ваш файл определения классов.

``` csharp
    using Windows.Devices.Enumeration;
    using Windows.Devices.Scanners;
```

2.  Затем примените наблюдатель устройства для запуска перечисления сканеров. Узнать больше об этом можно в разделе [Перечисление устройств](enumerate-devices.md).

```csharp
    void InitDeviceWatcher()
    {
       // Create a Device Watcher class for type Image Scanner for enumerating scanners
       scannerWatcher = DeviceInformation.CreateWatcher(DeviceClass.ImageScanner);

       scannerWatcher.Added += OnScannerAdded;
       scannerWatcher.Removed += OnScannerRemoved;
       scannerWatcher.EnumerationCompleted += OnScannerEnumerationComplete;
    }
```

3.  Создайте обработчик для события добавления сканера.

```csharp
    private async void OnScannerAdded(DeviceWatcher sender,  DeviceInformation deviceInfo)
    {
       await
       MainPage.Current.Dispatcher.RunAsync(
             Windows.UI.Core.CoreDispatcherPriority.Normal,
             () =>
             {
                MainPage.Current.NotifyUser(String.Format("Scanner with device id {0} has been added", deviceInfo.Id), NotifyType.StatusMessage);

                // search the device list for a device with a matching device id
                ScannerDataItem match = FindInList(deviceInfo.Id);

                // If we found a match then mark it as verified and return
                if (match != null)
                {
                   match.Matched = true;
                   return;
                }

                // Add the new element to the end of the list of devices
                AppendToList(deviceInfo);
             }
       );
    }
```

## <a name="scan"></a>Сканировать

1.  **Получение объекта ImageScanner**

Для каждого типа перечисления [**ImageScannerScanSource**](https://msdn.microsoft.com/library/windows/apps/Dn264238) будь то **По умолчанию**, **Автоопределение**, **Планшет** или **Податчик**, необходимо сначала создать объект [**ImageScanner**](https://msdn.microsoft.com/library/windows/apps/Dn263806), вызвав метод [**ImageScanner.FromIdAsync**](https://msdn.microsoft.com/library/windows/apps/windows.devices.scanners.imagescanner.fromidasync), как показано ниже.

 ```csharp
    ImageScanner myScanner = await ImageScanner.FromIdAsync(deviceId);
 ```

2.  **Обычное сканирование**

Чтобы выбрать сканер и сканировать с определенного источника с параметрами по умолчанию, приложение использует пространство имен [**Windows.Devices.Scanners**](https://msdn.microsoft.com/library/windows/apps/Dn264250). Никакие параметры сканирования не меняются. Возможные режимы сканирования: автовыбор, планшет, податчик бумаги При этом типе сканирования в большинстве случаев результаты успешны, даже если источник определен неправильно — например, планшет вместо податчика оригиналов.

**Примечание**Если пользователь документ в устройство подачи, сканер будет сканировать из планшета вместо. Если пользователь попытается сканировать из пустого податчика, задание сканирования не создаст ни одного отсканированного файла.
 
```csharp
    var result = await myScanner.ScanFilesToFolderAsync(ImageScannerScanSource.Default,
        folder).AsTask(cancellationToken.Token, progress);
```

3.  **Сканирование с автоматическим определением источника с планшетного сканера или со сканера с модулем автоподачи оригиналов**

Вы можете использовать в вашем приложении функцию [Автоматическое сканирование устройства](https://msdn.microsoft.com/library/windows/hardware/Ff539393), чтобы сканирование выполнялось с оптимальными параметрами. В этом случае, исходя из сканируемого содержимого, устройство само сможет определить наилучшие параметры сканирования, например цветовой режим и разрешение. Устройство выбирает параметры сканирования в процессе выполнения для каждого нового задания сканирования.

**Примечание**не все сканеры поддерживают эту функцию, поэтому приложение должно проверять, поддерживает ли сканер эту функцию перед использованием этого параметра.

В этом примере приложение сначала проверяет, поддерживает ли сканер возможность автовыбора, а затем сканирует. Чтобы указать планшетный сканер или сканер с модулем подачи, просто замените **Автоопределение** на **Планшет** или **Податчик**, соответственно.

```csharp
    if (myScanner.IsScanSourceSupported(ImageScannerScanSource.AutoConfigured))
    {
        ...
        // Scan API call to start scanning with Auto-Configured settings.
        var result = await myScanner.ScanFilesToFolderAsync(
            ImageScannerScanSource.AutoConfigured, folder).AsTask(cancellationToken.Token, progress);
        ...
    }
```

## <a name="preview-the-scan"></a>Предварительный просмотр при сканировании

Вы можете добавить код для предварительного просмотра результата сканирования перед запуском сканирования в папку. В следующем примере приложение проверяет, поддерживает ли сканер с параметром **Планшет** предварительный просмотр, а затем создает окно предварительного просмотра сканирования.

```csharp
if (myScanner.IsPreviewSupported(ImageScannerScanSource.Flatbed))
{
    rootPage.NotifyUser("Scanning", NotifyType.StatusMessage);
                // Scan API call to get preview from the flatbed.
                var result = await myScanner.ScanPreviewToStreamAsync(
                    ImageScannerScanSource.Flatbed, stream);
```

## <a name="cancel-the-scan"></a>Отмена сканирования

Вы можете предоставить пользователям возможность отмены задания сканирования до его окончания.

```csharp
void CancelScanning()
{
    if (ModelDataContext.ScenarioRunning)
    {
        if (cancellationToken != null)
        {
            cancellationToken.Cancel();
        }                
        DisplayImage.Source = null;
        ModelDataContext.ScenarioRunning = false;
        ModelDataContext.ClearFileList();
    }
}
```

## <a name="scan-with-progress"></a>Сканирование с индикатором выполнения

1.  Создайте объект **System.Threading.CancellationTokenSource**.

```csharp
cancellationToken = new CancellationTokenSource();
```

2.  Настройте обработчик события хода выполнения и получите индикатор выполнения сканирования.

```csharp
    rootPage.NotifyUser("Scanning", NotifyType.StatusMessage);
    var progress = new Progress<UInt32>(ScanProgress);
```

## <a name="scanning-to-the-pictures-library"></a>Сканирование в библиотеку изображений

Пользователи могут динамически выбирать папку для сканирования с помощью класса [**FolderPicker**](https://msdn.microsoft.com/library/windows/apps/BR207881), но, чтобы они могли сохранять результаты сканирования в библиотеке изображений, необходимо объявить возможность *Библиотека изображений* в манифесте. Дополнительные сведения о возможностях приложения см. в разделе [Объявление возможностей приложения](https://msdn.microsoft.com/library/windows/apps/Mt270968).

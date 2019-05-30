---
ms.assetid: 374D1983-60E0-4E18-ABBB-04775BAA0F0D
title: Сканирование из приложения
description: Узнайте, как сканировать содержимое из приложения с помощью планшетного сканера, сканера с модулем подачи или автоматически настраиваемого источника сканирования.
ms.date: 02/08/2017
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: 1a314d0acdc3df1e0b53b1d78445b6ab1b71bf92
ms.sourcegitcommit: ac7f3422f8d83618f9b6b5615a37f8e5c115b3c4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/29/2019
ms.locfileid: "66369739"
---
# <a name="scan-from-your-app"></a>Сканирование из приложения


**Важные API**

-   [**Windows.Devices.Scanners**](https://docs.microsoft.com/uwp/api/Windows.Devices.Scanners)
-   [**DeviceInformation**](https://docs.microsoft.com/uwp/api/Windows.Devices.Enumeration.DeviceInformation)
-   [**DeviceClass**](https://docs.microsoft.com/uwp/api/Windows.Devices.Enumeration.DeviceClass)

Узнайте, как сканировать содержимое из приложения с помощью планшетного сканера, сканера с модулем подачи или автоматически настраиваемого источника сканирования.

**Важные**   [ **Windows.Devices.Scanners** ](https://docs.microsoft.com/uwp/api/Windows.Devices.Scanners) API-интерфейсы являются частью рабочего стола [семейства устройств](https://docs.microsoft.com/windows/uwp/get-started/universal-application-platform-guide). Приложения могут использовать эти API-интерфейсы только для настольной версии Windows 10.

Перед сканированием из приложения необходимо составить список доступных сканеров, объявив новый объект [**DeviceInformation**](https://docs.microsoft.com/uwp/api/Windows.Devices.Enumeration.DeviceInformation) и получив тип [**DeviceClass**](https://docs.microsoft.com/uwp/api/Windows.Devices.Enumeration.DeviceClass). В список включаются только сканеры, установленные локально с драйверами WIA. Приложению будут доступны только такие сканеры.

После составления списка доступных сканеров ваше приложение может использовать автоматически настраиваемые параметры сканирования, зависящие от типа сканера, или просто сканировать с помощью доступного планшетного сканера или источника сканирования с модулем подачи. Для использования автоматически настраиваемых параметров сканер должен поддерживать автоматическую настройку и не может одновременно быть планшетным и иметь модуль подачи. Узнать больше об этом можно в разделе [автоматической настройке сканирования](https://docs.microsoft.com/windows-hardware/drivers/image/auto-configured-scanning).

## <a name="enumerate-available-scanners"></a>Перечисление доступных сканеров

Windows не определяет сканеры автоматически. Чтобы ваше приложение могло взаимодействовать со сканером, необходимо выполнить следующие шаги. В данном примере перечисление сканеров выполняется с использованием пространства имен [**Windows.Devices.Enumeration**](https://docs.microsoft.com/uwp/api/Windows.Devices.Enumeration).

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

## <a name="scan"></a>Scan

1.  **Получить объект ImageScanner**

Для каждого типа перечисления [**ImageScannerScanSource**](https://docs.microsoft.com/uwp/api/Windows.Devices.Scanners.ImageScannerScanSource) будь то **По умолчанию**, **Автоопределение**, **Планшет** или **Податчик**, необходимо сначала создать объект [**ImageScanner**](https://docs.microsoft.com/uwp/api/Windows.Devices.Scanners.ImageScanner), вызвав метод [**ImageScanner.FromIdAsync**](https://docs.microsoft.com/uwp/api/windows.devices.scanners.imagescanner.fromidasync), как показано ниже.

 ```csharp
    ImageScanner myScanner = await ImageScanner.FromIdAsync(deviceId);
 ```

2.  **Просканировать**

Чтобы выбрать сканер и сканировать с определенного источника с параметрами по умолчанию, приложение использует пространство имен [**Windows.Devices.Scanners**](https://docs.microsoft.com/uwp/api/Windows.Devices.Scanners). Никакие параметры сканирования не меняются. Возможные режимы сканирования: автовыбор, планшет, податчик бумаги При этом типе сканирования в большинстве случаев результаты успешны, даже если источник определен неправильно — например, планшет вместо податчика оригиналов.

**Примечание**  значение, если пользователь помещает документ в устройство, средство проверки проверит из устройства подачи, вместо этого. Если пользователь попытается сканировать из пустого податчика, задание сканирования не создаст ни одного отсканированного файла.
 
```csharp
    var result = await myScanner.ScanFilesToFolderAsync(ImageScannerScanSource.Default,
        folder).AsTask(cancellationToken.Token, progress);
```

3.  **Просмотр из автоматической настройки, планшетный или Фейерверк источника**

Вы можете использовать в вашем приложении функцию [Автоматическое сканирование устройства](https://docs.microsoft.com/windows-hardware/drivers/image/auto-configured-scanning), чтобы сканирование выполнялось с оптимальными параметрами. В этом случае, исходя из сканируемого содержимого, устройство само сможет определить наилучшие параметры сканирования, например цветовой режим и разрешение. Устройство выбирает параметры сканирования в процессе выполнения для каждого нового задания сканирования.

**Примечание**  не все сканеры поддерживают эту функцию, поэтому приложение должно проверять, если сканер поддерживает эту функцию, прежде чем использовать этот параметр.

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

Пользователи могут динамически выбирать папку для сканирования с помощью класса [**FolderPicker**](https://docs.microsoft.com/uwp/api/Windows.Storage.Pickers.FolderPicker), но, чтобы они могли сохранять результаты сканирования в библиотеке изображений, необходимо объявить возможность *Библиотека изображений* в манифесте. Дополнительные сведения о возможностях приложения см. в разделе [Объявление возможностей приложения](https://docs.microsoft.com/windows/uwp/packaging/app-capability-declarations).

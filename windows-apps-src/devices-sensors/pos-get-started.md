---
author: muhsinking
title: "Начало работы с точкой обслуживания"
description: "Эта статья содержит сведения о начале работы с API UWP точки обслуживания."
ms.author: mukin
ms.date: 08/2/2017
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: "Windows 10, UWP, точка обслуживания, POS"
ms.openlocfilehash: 9fb23d1b52938a364c7ad8dd2b01b3dade9046e1
ms.sourcegitcommit: 968187e803a866b60cda0528718a3d31f07dc54c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/03/2017
---
# <a name="getting-started-with-point-of-service"></a>Начало работы с точкой обслуживания

Точка обслуживания, точка продажи или устройства POS — это периферийные вычислительные устройства, облегчающие транзакции в розничной торговле. В качестве примеров устройств POS можно назвать электронные кассовые аппараты, сканеры штрихкодов, считыватели магнитных карт и чековые принтеры.

Здесь вы изучите основы взаимодействия с POS-устройствами с использованием API POS универсальной платформы Windows (UWP). Мы рассмотрим перечисление устройств, проверка возможностей устройства, присвоение устройств и предоставление общего доступа к устройствам. Мы используем сканер штрихкодов в качестве примера, но практически вся информация в этом руководстве применима к любым совместимым с UWP устройствам POS. (Список поддерживаемых устройств см. в разделе [Поддержка устройств POS](pos-device-support.md)).

## <a name="finding-and-connecting-to-pos-peripherals"></a>Поиск периферийных устройств POS и подключение к ним

Прежде чем устройство POS сможет использоваться каким-либо приложением, его необходимо связать с ПК, на котором выполняется приложение. Существует несколько способов подключения к устройствам POS программными средствами или через приложение "Параметры".

### <a name="connecting-to-devices-by-using-the-settings-app"></a>Подключение к устройствам с помощью приложения "Параметры"
При подключении устройства POS в качестве сканера штрихкодов к ПК, оно отображается как любое другое устройство. Его можно найти в разделе **Устройства > Bluetooth и другие устройства** приложения "Параметры" Там его можно связать устройство POS, выбрав команду **Добавление Bluetooth или другого устройства**.

Некоторые устройства POS могут не отображаться в приложении "Параметры", пока не будут перечислены программным способом с использованием API POS.

### <a name="getting-a-single-pos-device-with-getdefaultasync"></a>Получение одного устройства POS с GetDefaultAsync
В простом примере использования у вас, к примеру, может быть только одно периферийное устройство POS, подключенное к ПК, на котором выполняется приложение, и вы хотите настроить его максимально быстро. Для этого необходимо извлечь устройство "по умолчанию" с методом **GetDefaultAsync**, как показано здесь.

```Csharp
using Windows.Devices.PointOfService;

BarcodeScanner barcodeScanner = await BarcodeScanner.GetDefaultAsync();
```

Если устройство по умолчанию найдено, извлекаемый объект устройства готов к присвоению. "Присвоение" устройства дает приложению эксклюзивный доступ к нему, предотвращая конфликтующие команды из нескольких процессов.

> [!NOTE] 
> Если к ПК подключено несколько устройств POS, **GetDefaultAsync** возвращает первое найденное устройство. По этой причине необходимо использовать **FindAllAsync**, если вы не уверены, что приложению видно только одно устройство POS.

### <a name="enumerating-a-collection-of-devices-with-findallasync"></a>Перечисление коллекции устройств с FindAllAsync

Если подключение выполняется к нескольким устройствам, необходимо перечислить коллекцию объектов устройств **PointOfService**, чтобы найти нужное для присвоения. Например, в следующем коде создается коллекция из всех сканеров штрихкодов, которые подключены в настоящее время, а затем в коллекции выполняется поиск сканера с определенным именем.

```Csharp
using Windows.Devices.Enumeration;
using Windows.Devices.PointOfService;

string selector = BarcodeScanner.GetDeviceSelector();       
DeviceInformationCollection deviceCollection = await DeviceInformation.FindAllAsync(selector);

foreach (DeviceInformation devInfo in deviceCollection)
{
    Debug.WriteLine("{0} {1}", devInfo.Name, devInfo.Id);
    if (devInfo.Name.Contains("1200G"))
    {
        Debug.WriteLine(" Found one");
    }
}
```

### <a name="scoping-the-device-selection"></a>Определение области выбора устройств
При подключении к устройству может потребоваться ограничить поиск подмножеством периферийных устройств POS, к которым есть доступ у вашего приложения. С помощью метода **GetDeviceSelector** можно определить область выбора, чтобы извлекать только устройства, подключенные определенным способом (Bluetooth, USB и т. д.). Можно создать механизм выбора, который будет искать устройства по типам подключения: **Bluetooth**, **IP**, **Локальное** или **Все**. Это может оказаться полезным, поскольку обнаружение беспроводных устройств занимает много времени по сравнению с обнаружением локальных (проводных) устройств. Можно указать детерминированное время ожидания подключения локального устройства, ограничив значения **FindAllAsync** типом подключения **Локальное**. Например, этот код извлекает все сканеры штрихкодов, доступные по локальному подключению. 

```Csharp
string selector = BarcodeScanner.GetDeviceSelector(PosConnectionTypes.Local);
DeviceInformationCollection deviceCollection = await DeviceInformation.FindAllAsync(selector);
```

### <a name="reacting-to-device-connection-changes-with-devicewatcher"></a>Реагирование на изменения подключения устройств с DeviceWatcher

Во время работы приложения устройства могут отключаться или обновляться. Кроме того, может возникнуть необходимость в добавлении новых устройств. Можно использовать класс **DeviceWatcher** для доступа к событиям, связанным с устройствами, чтобы ваше приложение могло отвечать соответственно. Ниже приведен пример использования **DeviceWatcher**, когда заглушки метода нужно вызывать при добавлении, удалении или обновлении устройства.

```Csharp
DeviceWatcher deviceWatcher = DeviceInformation.CreateWatcher(selector);
deviceWatcher.Added += DeviceWatcher_Added;
deviceWatcher.Removed += DeviceWatcher_Removed;
deviceWatcher.Updated += DeviceWatcher_Updated;

void DeviceWatcher_Added(DeviceWatcher sender, DeviceInformation args)
{
    // TODO: Add the DeviceInformation object to your collection
}

void DeviceWatcher_Removed(DeviceWatcher sender, DeviceInformationUpdate args)
{
    // TODO: Remove the item in your collection associated with DeviceInformationUpdate
}

void DeviceWatcher_Updated(DeviceWatcher sender, DeviceInformationUpdate args)
{
    // TODO: Update your collection with information from DeviceInformationUpdate
}
```

## <a name="checking-the-capabilities-of-a-pos-device"></a>Проверка возможностей устройства POS
Даже в пределах класса устройств (например, сканеров штрихкодов) атрибуты устройства могут существенно варьироваться в зависимости от модели. Если вашему приложению требуется конкретный атрибут устройства, возможно, потребуется проверить каждый объект подключенного устройства, чтобы определить, поддерживается ли атрибут. Например, возможно, компании нужно, чтобы этикетки создавались по конкретному шаблону печати штрихкода. Вот как узнать, поддерживает ли подключенных сканер штрихкодов определенную символику. 

> [!NOTE]
> Символика— это языковое сопоставление, используемое штрихкодом для кодирования сообщений.

```Csharp
try
{
    BarcodeScanner barcodeScanner = await BarcodeScanner.FromIdAsync(deviceId);
    if (await barcodeScanner.IsSymbologySupportedAsync(BarcodeSymbologies.Code32))
    {
        Debug.WriteLine("Has symbology");
    }
}
catch (Exception ex)
{
    Debug.WriteLine("FromIdAsync() - " + ex.Message);
}
```

### <a name="using-the-devicecapabilities-class"></a>Использование класса Device.Capabilities
Класс **Device.Capabilities**— это атрибут всех классов устройств POS, который можно использовать для получения общих свойств о каждом устройстве. Например, в этом примере определяется, поддерживает ли устройство статистическую отчетность, и если да, извлекает статистику только для поддерживаемых типов.

```Csharp
try
{
    if (barcodeScanner.Capabilities.IsStatisticsReportingSupported)
    {
        Debug.WriteLine("Statistics reporting is supported");

        string[] statTypes = new string[] {""};
        IBuffer ibuffer = await barcodeScanner.RetrieveStatisticsAsync(statTypes);
    }
}
catch (Exception ex)
{
    Debug.WriteLine("EX: RetrieveStatisticsAsync() - " + ex.Message);
}
```

## <a name="claiming-a-pos-device"></a>Присвоение устройства POS
Прежде чем использовать устройств POS для активного ввода или вывода, его необходимо присвоить, предоставив приложению эксклюзивный доступ к его многочисленным функциям. Этот код показывает, как присваивать устройство сканирования штрихкодов после обнаружения устройства одним из вышеперечисленных методов.

```Csharp
try
{
    claimedBarcodeScanner = await barcodeScanner.ClaimScannerAsync();
}
catch (Exception ex)
{
    Debug.WriteLine("EX: ClaimScannerAsync() - " + ex.Message);
}
```

### <a name="retaining-the-device"></a>Сохранение устройства
При использовании устройства POS по сетевому подключению или подключению Bluetooth может возникнуть необходимость предоставить устройство другим приложениям в сети. (Дополнительные сведения об этом см. в разделе "Предоставление общего доступа к устройствам" [ссылка].) В другом случае целесообразно сохранить устройство для длительного использования. В этом примере показано, как сохранить присвоенный сканер штрихкодов после того, как другое приложение пришлет запрос на его освобождение.

```Csharp
claimedBarcodeScanner.ReleaseDeviceRequested += claimedBarcodeScanner_ReleaseDeviceRequested;

void claimedBarcodeScanner_ReleaseDeviceRequested(object sender, ClaimedBarcodeScanner e)
{
    e.RetainDevice();  // Retain exclusive access to the device
}
```

## <a name="input-and-output"></a>Вход и выход

Присвоив устройство, вы почти готовы его использовать. Чтобы получить входные данные с устройства, необходимо настроить его и включить делегат для получения данных. В приведенном ниже примере мы присваиваем сканер штрихкодов, задаем его свойство декодирования и вызываем метод **EnableAsync**, чтобы включить получение декодированных входных данных с устройства. Этот процесс варьируется в зависимости от классов устройств, поэтому инструкции по настройке делегата для устройств без штрихкода см. в соответствующем [примере приложения UWP](https://github.com/Microsoft/Windows-universal-samples#devices-and-sensors).

```Csharp
try
{
    claimedBarcodeScanner = await barcodeScanner.ClaimScannerAsync();
    if (claimedBarcodeScanner != null)
    {
        claimedBarcodeScanner.DataReceived += claimedBarcodeScanner_DataReceived;
        claimedBarcodeScanner.IsDecodeDataEnabled = true;
        await claimedBarcodeScanner.EnableAsync();
    }
}
catch (Exception ex)
{
    Debug.WriteLine("EX: ClaimScannerAsync() - " + ex.Message);
}


void claimedBarcodeScanner_DataReceived(ClaimedBarcodeScanner sender, BarcodeScannerDataReceivedEventArgs args)
{
    string symbologyName = BarcodeSymbologies.GetName(args.Report.ScanDataType);
    var scanDataLabelReader = DataReader.FromBuffer(args.Report.ScanDataLabel);
    string barcode = scanDataLabelReader.ReadString(args.Report.ScanDataLabel.Length);
}
```

## <a name="sharing-a-device-between-apps"></a>Предоставление приложениям общего доступа к устройству

Устройства POS часто используются в сценариях, когда доступ к ним потребуется осуществить нескольким приложениям за короткое время.  Общий доступ к устройству может быть предоставлен, если оно локально подключено к нескольким приложениям (USB или другое проводное подключение) либо по сети Bluetooth или IP. В зависимости от потребностей каждого приложения одному процессу, возможно, придется отказаться от присвоения устройства. Этот код удаляет наш присвоенный сканер штрихкода, после чего его смогут присвоить и использовать другие приложения.

```Csharp
if (claimedBarcodeScanner != null)
{
    claimedBarcodeScanner.Dispose();
    claimedBarcodeScanner = null;
}
```

> [!NOTE]
> Присвоенные и неприсвоенные классы устройства POS реализуют [интерфейс IClosable](https://docs.microsoft.com/en-us/uwp/api/windows.foundation.iclosable). Если устройство подключено к приложению через сеть или Bluetooth, присвоенные и неприсвоенные объекты необходимо удалить, прежде чем другое приложение сможет подключиться.

## <a name="see-also"></a>См. также:
+ [Образец сканера штрихкодов](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/BarcodeScanner)
+ [Образец кассового аппарата]( https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/CashDrawer)
+ [Пример строкового дисплея](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/LineDisplay)
+ [Пример считывателя магнитных карт](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/MagneticStripeReader)
+ [Пример принтера POS](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/PosPrinter)


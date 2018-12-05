---
title: Объявления Bluetooth
description: Этот раздел содержит статьи о том, как интегрировать объявления Bluetooth с низким энергопотреблением (LE) c приложениями универсальной платформы Windows (UWP) при помощи пользователя API AdvertisementWatcher и AdvertisementPublisher.
ms.date: 02/08/2017
ms.topic: article
keywords: windows 10, uwp
ms.assetid: ff10bbc0-03a7-492c-b5fe-c5b9ce8ca32e
ms.localizationpriority: medium
ms.openlocfilehash: e9eafde0596ad3156f52a7a2f0a1566444a9836a
ms.sourcegitcommit: d7613c791107f74b6a3dc12a372d9de916c0454b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/05/2018
ms.locfileid: "8738305"
---
# <a name="bluetooth-le-advertisements"></a>Объявления Bluetooth LE


**Важные API**

-   [**Windows.Devices.Bluetooth.Advertisement**](https://msdn.microsoft.com/library/windows/apps/windows.devices.bluetooth.advertisement.aspx)

В этой статье приводится обзор маяков объявлений Bluetooth с низким энергопотреблением (LE) для приложений универсальной платформы Windows (UWP).  

## <a name="overview"></a>Обзор

Существует две основные функции, которые разработчик может выполнять с помощью API Advertisement LE:

-   [Advertisement Watcher](https://msdn.microsoft.com/library/windows/apps/windows.devices.bluetooth.advertisement.bluetoothleadvertisementwatcher.aspx): прием сообщений близлежащих маяков и их фильтрация на основе полезной нагрузки или близости.  
-   [Advertisement Publisher](https://msdn.microsoft.com/library/windows/apps/windows.devices.bluetooth.advertisement.bluetoothleadvertisementpublisher.aspx): определение полезной нагрузки, чтобы Windows могла показывать рекламу от имени разработчика.  

Полный пример кода включен в [Пример объявления Bluetooth](http://go.microsoft.com/fwlink/p/?LinkId=619990) на Github

## <a name="basic-setup"></a>Базовая настройка

Чтобы использовать базовые функции Bluetooth LE в приложении универсальной платформы Windows, необходимо поставить флажок рядом с возможностью Bluetooth в Package.appxmanifest.

1. Откройте Package.appxmanifest
2. Перейдите на вкладку "Возможности".
3. Найдите Bluetooth в списке слева и поставьте флажок рядом.

## <a name="publishing-advertisements"></a>Публикация объявлений

С помощью объявлений Bluetooth LE устройство может постоянно устанавливать маяки в определенных полезных данных— объявлениях. Это объявление будет видно любому устройству с поддержкой Bluetooth LE поблизости, если оно настроено для прослушивания именно этого объявления.

> **Обратите внимание**: для конфиденциальности пользователя, продолжительность существования объявления привязана к продолжительности существования приложения. Вы можете создать BluetoothLEAdvertisementPublisher и вызвать задачу "Запуск в фоновом режиме" для объявления в фоновом режиме. Подробнее о фоновых задачах см. в разделе [Запуск, возобновление и фоновые задачи](https://msdn.microsoft.com/windows/uwp/launch-resume/index).

### <a name="basic-publishing"></a>Базовые возможности публикации

Существует много различных способов добавления данных в объявление. В этом примере показан распространенный способ создания объявления компании. 

Сначала создайте издатель объявлений, который управляет установкой маяков устройством в определенных объявлениях.

```csharp
BluetoothLEAdvertisementPublisher publisher = new BluetoothLEAdvertisementPublisher();
```

Затем создайте пользовательский раздел данных. В этом примере используется неназначенное значение **CompanyId** *0xFFFE*, а в объявление добавляется текст *Hello World*. 

```csharp
// Add custom data to the advertisement
var manufacturerData = new BluetoothLEManufacturerData();
manufacturerData.CompanyId = 0xFFFE;

var writer = new DataWriter();
writer.WriteString("Hello World");

// Make sure that the buffer length can fit within an advertisement payload (~20 bytes). 
// Otherwise you will get an exception.
manufacturerData.Data = writer.DetachBuffer();

// Add the manufacturer data to the advertisement publisher:
publisher.Advertisement.ManufacturerData.Add(manufacturerData);
```

Издатель создан и настроен, и вы можете вызвать **Запуск**, чтобы начать показ рекламы.

```csharp
publisher.Start();
```

## <a name="watching-for-advertisements"></a>Слежение за рекламой

### <a name="basic-watching"></a>Базовое слежение

В следующем коде показано, как создать наблюдатель объявлений Bluetooth LE, настроить обратный вызов и начать слежение за всеми объявлениями LE.

```csharp
BluetoothLEAdvertisementWatcher watcher = new BluetoothLEAdvertisementWatcher();
watcher.Received += OnAdvertisementReceived;
watcher.Start();
``` 

```csharp
private async void OnAdvertisementReceived(BluetoothLEAdvertisementWatcher watcher, BluetoothLEAdvertisementReceivedEventArgs eventArgs)
{
    // Do whatever you want with the advertisement
}
```

#### <a name="active-scanning"></a>Активное сканирование
Чтобы также полчать объявления ответов на сканирование, выполните следующую настройку после создания наблюдателя. Обратите внимание, что это приведет к увеличению энергопотребления и недоступно в фоновых режимах.

```csharp
watcher.ScanningMode = BluetoothLEScanningMode.Active;
```

### <a name="watching-for-a-specific-advertisement-pattern"></a>Слежение за определенным шаблоном объявления

Иногда требуется прослушивать определенное объявление. В этом случае прослушивается объявление, полезными данным в котором являются вымышленная компания (определяется как 0xFFFE) и строка *Hello World* в объявлении. Это можно связать с примером базовой публикации, чтобы один компьютер с Windows показывал рекламу, а второй — следил. Обязательно установите этот рекламный фильтр перед запуском наблюдателя!

```csharp
var manufacturerData = new BluetoothLEManufacturerData();
manufacturerData.CompanyId = 0xFFFE;

// Make sure that the buffer length can fit within an advertisement payload (~20 bytes). 
// Otherwise you will get an exception.
var writer = new DataWriter();
writer.WriteString("Hello World");
manufacturerData.Data = writer.DetachBuffer();

watcher.AdvertisementFilter.Advertisement.ManufacturerData.Add(manufacturerData);
```

### <a name="watching-for-a-nearby-advertisement"></a>Слежение за ближайшими объявлениями

Иногда требуется активировать наблюдатель только при появлении в пределах досягаемости устройства, показывающего рекламу. Вы можете самостоятельно определить зону действия, но учтите, что диапазон ограничен значениями от 0 до -128. 

```csharp
// Set the in-range threshold to -70dBm. This means advertisements with RSSI >= -70dBm 
// will start to be considered "in-range" (callbacks will start in this range).
watcher.SignalStrengthFilter.InRangeThresholdInDBm = -70;

// Set the out-of-range threshold to -75dBm (give some buffer). Used in conjunction 
// with OutOfRangeTimeout to determine when an advertisement is no longer 
// considered "in-range".
watcher.SignalStrengthFilter.OutOfRangeThresholdInDBm = -75;

// Set the out-of-range timeout to be 2 seconds. Used in conjunction with 
// OutOfRangeThresholdInDBm to determine when an advertisement is no longer 
// considered "in-range"
watcher.SignalStrengthFilter.OutOfRangeTimeout = TimeSpan.FromMilliseconds(2000);
```

### <a name="gauging-distance"></a>Дальность измерения

При активации обратного вызова наблюдателя Bluetooth LE eventArgs включают значение RSSI, указывая уровень полученного сигнала (уровень сигнала Bluetooth).

```csharp
private async void OnAdvertisementReceived(BluetoothLEAdvertisementWatcher watcher, BluetoothLEAdvertisementReceivedEventArgs eventArgs)
{
    // The received signal strength indicator (RSSI)
    Int16 rssi = eventArgs.RawSignalStrengthInDBm;
}
```

Этот показатель можно примерно перевести в расстояние, но его не следует использовать для измерения фактического расстояния, так как каждое радио уникально. Расстояние может быть сложно измерить в силу различных факторов окружающей среды (таких как стены, корпус вокруг радио или даже влажность воздуха).

Вместо определения фактического расстояния можно определять "сегменты". Как правило, уровень сигнала находится в диапазоне от 0 до -50 дБм, если радио находится близко, от -50 до -90 дБм, если радио находится на среднем расстоянии, и ниже -90 дБм, если радио находится далеко. Лучше всего определить оптимальные сегменты для конкретного приложения методом проб и ошибок.
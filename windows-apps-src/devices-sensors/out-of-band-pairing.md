---
author: IvorB
ms.assetid: E9ADC88F-BD4F-4721-8893-0E19EA94C8BA
title: Внеполосное связывание
description: Внеполосное связывание позволяет приложениям подключаться к периферийным устройствам точки обслуживания (POS) без необходимости обнаружения.
---
# Внеполосное связывание

Внеполосное связывание позволяет приложениям подключаться к периферийным устройствам точки обслуживания (POS) без необходимости обнаружения. Приложения должны использовать пространство имен [**Windows.Devices.PointOfService**](https://msdn.microsoft.com/library/windows/apps/windows.devices.pointofservice.aspx) и передавать особым образом форматированную строку (внеполосной большой двоичный объект) соответствующему методу **FromIdAsync** для нужного периферийного устройства. При выполнении **FromIdAsync** главное устройство связывается с периферийным устройством и подключается к нему, прежде чем операция возвращается вызывающей стороне.

## Формат внеполосных больших двоичных объектов

```json
    "connectionKind":"Network",
    "physicalAddress":"AA:BB:CC:DD:EE:FF",
    "connectionString":"192.168.1.1:9001",
    "peripheralKinds":"{C7BC9B22-21F0-4F0D-9BB6-66C229B8CD33}",
    "providerId":"{02FFF12E-7291-4A5D-ADFA-DA8FB7769CD2}",
    "providerName":"PrinterProtocolProvider.dll"
```

**connectionKind** — тип подключения. Допустимые значения: «Network» и «Bluetooth».

**physicalAddress** — MAC-адрес периферийного устройства. Например, в случае сетевого принтера это MAC-адрес, указанный в тестовой распечатке принтера в формате AA:BB:CC:DD:EE:FF.

**connectionString** — строка подключения периферийного устройства. Например, в случае сетевого принтера это IP-адрес, указанный в тестовой распечатке принтера в формате 192.168.1.1:9001. Это поле опускается для всех периферийных устройств Bluetooth.

**peripheralKinds** — GUID для типа устройства. Допустимые значения:

| Тип устройства | GUID |
| ---- | ---- |
| *Принтер POS* | C7BC9B22-21F0-4F0D-9BB6-66C229B8CD33 |
| *Сканер штрихкодов* | C243FFBD-3AFC-45E9-B3D3-2BA18BC7EBC5 |
| *Кассовый аппарат* | 772E18F2-8925-4229-A5AC-6453CB482FDA |


**providerId** — GUID класса поставщика протокола. Допустимые значения:

| Класс поставщика протокола | GUID |
| ---- | ---- |
| *Типовой сетевой принтер ESC/POS* | 02FFF12E-7291-4A5D-ADFA-DA8FB7769CD2 |
| *Типовой Bluetooth-принтер ESC/POS* | CCD5B810-95B9-4320-BA7E-78C223CAF418 |
| *Bluetooth-принтер Epson* | 94917594-544F-4AF8-B53B-EC6D9F8A4464 |
| *Сетевой принтер Epson* | 9F0F8BE3-4E59-4520-BFBA-AF77614A31CE |
| *Сетевой принтер Star* | 1E3A32C2-F411-4B8C-AC91-CC2C5FD21996 |
| *Bluetooth-сканер Socket* | 6E7C8178-A006-405E-85C3-084244885AD2 |
| *Сетевой кассовый аппарат APG* | E619E2FE-9489-4C74-BF57-70AED670B9B0 |
| *Кассовый Bluetooth-аппарат APG* | 332E6550-2E01-42EB-9401-C6A112D80185 |


**providerName** — имя библиотеки DLL поставщика. Поставщики по умолчанию:

| Поставщик | Имя DLL |
| ---- | ---- |
| Принтер | PrinterProtocolProvider.dll |
| Кассовый аппарат | CashDrawerProtocolProvider.dll |
| Сканер | BarcodeScannerProtocolProvider.dll |

## Пример использования: сетевой принтер

```csharp
String oobBlobNetworkPrinter =
  "{\"connectionKind\":\"Network\"," +
  "\"physicalAddress\":\"AA:BB:CC:DD:EE:FF\"," +
  "\"connectionString\":\"192.168.1.1:9001\"," +
  "\"peripheralKinds\":\"{C7BC9B22-21F0-4F0D-9BB6-66C229B8CD33}\"," +
  "\"providerId\":\"{02FFF12E-7291-4A5D-ADFA-DA8FB7769CD2}\"," +
  "\"providerName\":\"PrinterProtocolProvider.dll\"}";

printer = await PosPrinter.FromIdAsync(oobBlobNetworkPrinter);
```

## Пример использования: принтер Bluetooth

```csharp
string oobBlobBTPrinter =
    "{\"connectionKind\":\"Bluetooth\"," +
    "\"physicalAddress\":\"AA:BB:CC:DD:EE:FF\"," +
    "\"peripheralKinds\":\"{C7BC9B22-21F0-4F0D-9BB6-66C229B8CD33}\"," +
    "\"providerId\":\"{CCD5B810-95B9-4320-BA7E-78C223CAF418}\"," +
    "\"providerName\":\"PrinterProtocolProvider.dll\"}";

printer = await PosPrinter.FromIdAsync(oobBlobBTPrinter);

```


<!--HONumber=May16_HO2-->



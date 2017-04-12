---
author: mukin
title: "Сканер штрихкодов"
description: "Эта статья содержит сведения о семействе устройств— сканеров штрихкодов для POS-терминалов"
ms.author: wdg-dev-content
ms.date: 02/21/2017
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: windows 10, uwp
ms.openlocfilehash: 8d9ef9bc08fa666c2af1348450f7a5fb0a0c7b65
ms.sourcegitcommit: 909d859a0f11981a8d1beac0da35f779786a6889
translationtype: HT
---
# <a name="barcode-scanner"></a>Сканер штрихкодов
Предоставляет разработчикам приложений доступ к [сканерам штрихкодов](https://docs.microsoft.com/en-us/uwp/api/windows.devices.pointofservice.barcodescanner) для извлечения расшифрованных данных из различной символики штрихкодов, например UPC и QR-кодов, в зависимости от аппаратной поддержки. Полный список поддерживаемых символик см. в классе [BarcodeSymbologies](https://docs.microsoft.com/en-us/uwp/api/windows.devices.pointofservice.barcodesymbologies).

## <a name="requirements"></a>Требования
Приложения, которым нужно это пространство имен, требуют добавления "pointOfService" [DeviceCapability](https://msdn.microsoft.com/library/4353c4fd-f038-4986-81ed-d2ec0c6235ef) в манифест пакета приложения.

## <a name="device-support"></a>Поддержка устройств

### <a name="usb"></a>USB

#### <a name="hidscanner"></a>HID.Scanner
Windows содержит драйвер класса сканеров штрихкода, который основывается на странице использования HID.Scanner (8C), определенной по USB.org. Этот драйвер класса будет поддерживать любой сканер штрихкодов, который реализует этот стандарт, например: Изготовитель Модели Honeywell 1900GSR-2, 1200g-2 Intermec SG20

См. руководство для сканера штрихкодов или обратитесь к производителю, чтобы определить, можно ли его настроить как USB.HID.Scanner.

#### <a name="hidvendor-specific"></a>Специальный HID.Vendor
Windows поддерживает внедрение драйверов конкретных поставщиков для поддержки дополнительных сканеров штрихкодов. Если устройство не поддерживается входящим в комплект поставки USB.HID.Scanner, уточните наличие у изготовителя сканера штрихкодов.

### <a name="bluetooth"></a>Bluetooth
#### <a name="serial-port-protocol-spp--simple-serial-interface-ssi"></a>Протокол последовательного порта (SPP) — простой последовательный интерфейс (SSI)
Windows поддерживает сканеры штрихкодов Bluetooth на основе SPP-SSI.

| Изготовитель |    Модели |
|--------------|-----------|
| Socket Mobile |    **Серия CHS 7:** <br/> Сканер штрихкодов и формирователь изображений 7 Ci 1D <br/> Сканер штрихкодов и формирователь изображений длительного пользования 7Di 1D <br/> Лазерный сканер штрихкодов 7Mi 1D <br/> Лазерный сканер штрихкодов длительного пользования 7Pi 1D <br/> **Серия DuraScan 700:** <br/> Сканер штрихкодов и формирователь изображений D700 1D <br/> Лазерный сканер штрихкодов D730 1D <br/> **Серия SocketScan 800** <br/> Сканер штрихкодов и формирователь изображений S800 1D (прежнее название— CHS 8Ci) <br/> Сканер штрихкодов и формирователь изображений S850 2D (прежнее название— CHS 8Qi)

## <a name="examples"></a>Примеры
См. образец сканера штрихкодов в примере внедрения.
+    [Образец сканера штрихкодов](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/BarcodeScanner)

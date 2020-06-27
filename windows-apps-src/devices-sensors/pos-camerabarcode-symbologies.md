---
title: Символики, поддерживаемые сканером штрихкодов на базе камеры
description: Символики, поддерживаемые сканером штрихкодов на базе камеры
ms.date: 05/02/2018
ms.topic: article
keywords: Windows 10, UWP, точка обслуживания, POS
ms.localizationpriority: medium
ms.openlocfilehash: 481d10f2fea076f45124a3c75819dfe6494300bf
ms.sourcegitcommit: 48e047a581fcfcc9a4084d65a78b89f2c01cf4f3
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/26/2020
ms.locfileid: "85448404"
---
# <a name="symbologies"></a>Символики

В этой статье приведены примеры штрихкодов для каждой из символик, поддерживаемых программным декодером штрихкодов в Windows 10, в том числе UPC/EAN, Code 39, Code 128, Interleaved 2 of 5, Databar Omnidirectional, Databar Stacked, QR Code и GS1DWCode.

Для создания сканера штрихкодов в Windows 10 используется стандартная камера линза вместе с программным декодером. Эта статья относится к симбологиес, поддерживаемому программным декодером. Дополнительные симбологиес могут поддерживаться специальными устройствами со встроенными средствами проверки штрихкодов, имеющими встроенный аппаратный декодер. для получения дополнительных сведений обратитесь к изготовителю сканера штрихкодов. Перечисленные симбологиес поддерживаются во всех выпусках Windows 10 Build 17134 или более поздней версии, если не указано иное.

Используйте [жетсуппортедсимбологиесасинк](/uwp/api/windows.devices.pointofservice.barcodescanner.getsupportedsymbologiesasync) , чтобы определить конкретный симбологиес, поддерживаемый сканером штрихкодов.

> [!NOTE]
> Программный декодер, встроенный в Windows 10, предоставляется [*корпорацией Digimarc*](https://www.digimarc.com/).

## <a name="1d-symbologies"></a>Одномерные символики

### <a name="code-39"></a>Code 39
![Пример штрихкода — Code 39](images/pos/sample-barcode-code39.png)

### <a name="code-128"></a>Code 128
![Пример штрихкода — Code 128](images/pos/sample-barcode-code128.png)

### <a name="databar-omnidirectional"></a>Databar Omnidirectional
![Пример штрихкода — Databar Omnidirectional](images/pos/sample-barcode-databar-omnidirectional.png) 
### <a name="databar-stacked"></a>Databar Stacked
![Пример штрихкода — Databar Stacked](images/pos/sample-barcode-databar-stacked.png)

### <a name="ean-8"></a>EAN-8
![Пример штрихкода — EAN-8](images/pos/sample-barcode-ean8.png)

### <a name="ean-13"></a>EAN-13
![Пример штрихкода — EAN-13](images/pos/sample-barcode-ean13.png)

### <a name="interleaved-2-of-5"></a>Interleaved 2 of 5
![Пример штрихкода — Interleaved 2 of 5](images/pos/sample-barcode-interleaved-2-of-5.png)

### <a name="upc-a"></a>UPC-A
![Пример штрихкода — UPC A](images/pos/sample-barcode-upca.png)

### <a name="upc-e"></a>UPC-E
![Пример штрихкода — UPC E](images/pos/sample-barcode-upce.png)

## <a name="2d-symbologies"></a>Двухмерные символики
### <a name="qr-code"></a>QR-код
![Пример штрихкода — QR Code](images/pos/sample-barcode-qrcode.png)

## <a name="digital-watermark"></a>Digital Watermark
### <a name="gs1-dwcode"></a>GS1-DWCode

Отсканируйте изображение упаковки ниже с помощью своего приложения со сканером штрихкода на базе камеры, чтобы увидеть GS1DWCode в действии.  Изображение кодировано с использованием UPCA 856107006854.  Подробнее о возможностях GS1DWCode можно узнать на сайте http://www.digimarc.com.

![Пример штрихкода — GS1DWCode](images/pos/Rice-Box-V7.jpg)

## <a name="see-also"></a>См. также

### <a name="samples"></a>Примеры

- [Пример сканера штрихкодов](https://github.com/microsoft/Windows-universal-samples/tree/master/Samples/BarcodeScanner)

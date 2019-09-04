---
title: Символики, поддерживаемые сканером штрихкодов на базе камеры
description: Символики, поддерживаемые сканером штрихкодов на базе камеры
ms.date: 05/02/2018
ms.topic: article
keywords: Windows 10, UWP, точка обслуживания, POS
ms.localizationpriority: medium
ms.openlocfilehash: cc2aaaf4e9779cb2be712119fb1dacdf946952c5
ms.sourcegitcommit: 0dec04de501a3db6b22dfd4a320fc09b5c4a21b5
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/04/2019
ms.locfileid: "70243325"
---
# <a name="symbologies"></a>Символики
В этом разделе приводятся образцы штрихкодов для каждого симбологиес, поддерживаемого декодером штрихкодов программного обеспечения, который поставляется с Windows 10, включая: UPC/EAN, код 39, код 128, чередующиеся 2 из 5, столбчатых всенаправленные, столбчатых с накоплением, QR-код и GS1DWCode.

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
### <a name="qr-code"></a>QR Code
![Пример штрихкода — QR Code](images/pos/sample-barcode-qrcode.png)

## <a name="digital-watermark"></a>Digital Watermark
### <a name="gs1-dwcode"></a>GS1-DWCode

Отсканируйте изображение упаковки ниже с помощью своего приложения со сканером штрихкода на базе камеры, чтобы увидеть GS1DWCode в действии.  Изображение кодировано с использованием UPCA 856107006854.  Подробнее о возможностях GS1DWCode можно узнать на сайте http://www.digimarc.com.

![Пример штрихкода — GS1DWCode](images/pos/rice-box-v7.jpg)

> [!NOTE]
> Программный декодер, встроенный в Windows 10, любезно предоставлен [*Digimarc Corporation*](https://www.digimarc.com/).

## <a name="see-also"></a>См. также

### <a name="samples"></a>Примеры

- [Пример сканера штрихкодов](https://github.com/microsoft/Windows-universal-samples/tree/master/Samples/BarcodeScanner)

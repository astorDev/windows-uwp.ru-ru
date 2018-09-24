---
author: TerryWarwick
title: Работа с символиками сканера штрихкодов
description: Эта статья содержит сведения о символиках сканера штрихкодов.
ms.author: jken
ms.date: 08/29/2018
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: Windows 10, UWP, точка обслуживания, POS
ms.localizationpriority: medium
ms.openlocfilehash: 8bd1dffe4da7b3725ef7716fe9cf28bdf8eaf34f
ms.sourcegitcommit: 194ab5aa395226580753869c6b66fce88be83522
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/24/2018
ms.locfileid: "4151089"
---
# <a name="working-with-symbologies"></a>Работа с символиками
[Символика штрихкодов](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.barcodesymbologies) — это сопоставление данных с конкретным форматом штрихкодов. Распространенным символикам относятся UPC, Code 128, QR Code и т. д.  Сканер штрихкодов универсальной платформы Windows API-интерфейсы позволяют приложению управлять тем, как сканер обрабатывает эти символики, без ручной настройки сканера. 

## <a name="determine-which-symbologies-are-supported"></a>Определение поддерживаемых символик 
Поскольку ваше приложение может использоваться в сочетании со сканерами штрихкодов различных моделей и различных производителей, имеет смысл запросить сканер, чтобы определить список поддерживаемых им символик.  Это удобно делать, если вашему приложению требуется определенная символика, которая может не поддерживаться всеми сканерами, или если вам нужно включить символики, отключенные на сканере вручную или программным образом.

Как только вы получите объект [BarcodeScanner](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.barcodescanner) с помощью метода [BarcodeScanner.FromIdAsync](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.barcodescanner.fromidasync), вызовите метод [GetSupportedSymbologiesAsync](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.barcodescanner.getsupportedsymbologiesasync#Windows_Devices_PointOfService_BarcodeScanner_GetSupportedSymbologiesAsync), чтобы получить список символик, поддерживаемых устройством.

В следующем примере список символик, поддерживаемых сканером штрихкодов на базе получает и отображает их в текстовом блоке:

```cs
private void DisplaySupportedSymbologies(BarcodeScanner barcodeScanner, TextBlock textBlock) 
{
    var supportedSymbologies = await barcodeScanner.GetSupportedSymbologiesAsync();

    foreach (uint item in supportedSymbologies)
    {
        string symbology = BarcodeSymbologies.GetName(item);
        textBlock.Text += (symbology + "\n");
    }
}
```

## <a name="determine-if-a-specific-symbology-is-supported"></a>Определение того, поддерживается ли определенная символика
Чтобы определить, поддерживает ли сканер определенная символика можно вызвать [IsSymbologySupportedAsync](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.barcodescanner.issymbologysupportedasync#Windows_Devices_PointOfService_BarcodeScanner_IsSymbologySupportedAsync_System_UInt32_).

В следующем примере проверяется, поддерживает ли сканер штрихкодов символика **Code32** :

```cs
bool symbologySupported = await barcodeScanner.IsSymbologySupportedAsync(BarcodeSymbologies.Code32);
```

## <a name="change-which-symbologies-are-recognized"></a>Изменение распознаваемых символик
В некоторых случаях может потребоваться использовать подмножество символик, поддерживаемых сканером штрихкодов.  Это особенно удобно делать для блокирования символик, которые вы не планируете использовать в своем приложении. Например, чтобы гарантировать, что пользователь отсканирует нужный штрихкод, стоит ограничить сканирование символиками UPC или EAN, когда нужно получать SKU товаров, или ограничить сканирование символикой Code 128, когда стоит задача получать серийные номера.

Зная, какие символики поддерживает ваш сканер, вы можете задать символики, которые он должен распознавать.  Это можно сделать после [ClaimedBarcodeScanner](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.claimedbarcodescanner) объекта, с помощью [ClaimScannerAsync](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.barcodescanner.claimscannerasync#Windows_Devices_PointOfService_BarcodeScanner_ClaimScannerAsync). Вы можете вызвать метод [SetActiveSymbologiesAsync](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.claimedbarcodescanner.setactivesymbologiesasync#Windows_Devices_PointOfService_ClaimedBarcodeScanner_SetActiveSymbologiesAsync_Windows_Foundation_Collections_IIterable_System_UInt32__), чтобы включить конкретный набор символик. Символики, которых нет в заданном вами списке, отключаются.

В следующем примере задается active символиками сканера штрихкодов присвоенные [Code39](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.barcodesymbologies.code39#Windows_Devices_PointOfService_BarcodeSymbologies_Code39) и [Code39Ex](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.barcodesymbologies.code39ex):

```cs
private async void SetSymbologies(ClaimedBarcodeScanner claimedBarcodeScanner) 
{
    var symbologies = new List<uint>{ BarcodeSymbologies.Code39, BarcodeSymbologies.Code39Ex };
    await claimedBarcodeScanner.SetActiveSymbologiesAsync(symbologies);
}
```

## <a name="barcode-symbology-attributes"></a>Атрибуты символики штрихкода
Символики штрихкодов различных можно имеют различные атрибуты, такие как вспомогательные несколько декодировать длины, передающего контрольную цифру к узлу как часть необработанных данных и выполнить проверку цифр. В классе [BarcodeSymbologyAttributes](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.barcodesymbologyattributes) можно получить и задать эти атрибуты для данного символика [ClaimedBarcodeScanner](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.claimedbarcodescanner) и штрих-кода.

Вы можете получить атрибуты данного символика с [GetSymbologyAttributesAsync](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.claimedbarcodescanner.getsymbologyattributesasync#Windows_Devices_PointOfService_ClaimedBarcodeScanner_GetSymbologyAttributesAsync_System_UInt32_). В следующем фрагменте кода получает атрибуты символики использованием **ClaimedBarcodeScanner**.

```cs
BarcodeSymbologyAttributes barcodeSymbologyAttributes = 
    await claimedBarcodeScanner.GetSymbologyAttributesAsync(BarcodeSymbologies.Upca);
```

Когда вы закончите изменение атрибутов и готовы к задании, можно вызвать [SetSymbologyAttributesAsync](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.claimedbarcodescanner.setsymbologyattributesasync). Этот метод возвращает **bool**, который имеет **значение true** , если атрибуты были успешно.

```cs
bool success = await claimedBarcodeScanner.SetSymbologyAttributesAsync(
    BarcodeSymbologies.Upca, barcodeSymbologyAttributes);
```

### <a name="restrict-scan-data-by-data-length"></a>Ограничение сканируемых данных по длине
Некоторым символикам, например Code 39 и Code 128, свойственна переменная длина.  Штрихкоды эти символик могут находиться рядом друг с другом содержащие разные данные часто конкретной длины. Задание конкретной длины данных, которые вы хотите получить, помогает предотвратить недействительные считывания.

Перед установкой длины декодирования, проверьте, поддерживается ли символика штрихкодов несколько длины с [IsDecodeLengthSupported](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.barcodesymbologyattributes.isdecodelengthsupported#Windows_Devices_PointOfService_BarcodeSymbologyAttributes_IsDecodeLengthSupported). Когда вы знаете, что он поддерживается, вы можете задать [DecodeLengthKind](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.barcodesymbologyattributes.decodelengthkind#Windows_Devices_PointOfService_BarcodeSymbologyAttributes_DecodeLengthKind), которое относится к типу [BarcodeSymbologyDecodeLengthKind](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.barcodesymbologydecodelengthkind). Это свойство можно использовать любые из следующих значений:

* **AnyLength**: декодирования длины любого размера.
* **Дискретные**: декодирования длин [DecodeLength1](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.barcodesymbologyattributes.decodelength1) или [DecodeLength2](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.barcodesymbologyattributes.decodelength2) символов.
* **Диапазон**: декодирования длины между **DecodeLength1** и **DecodeLength2** символов. Порядок **DecodeLength1** и выполните **DecodeLength2** буквально несколько (либо может быть выше или ниже, чем другой).

Наконец можно задать значения **DecodeLength1** и **DecodeLength2** для управления длины данных, которые вам необходимы.

В следующем фрагменте кода показано, как задать размер декодирования:

```cs
private async Task<bool> SetDecodeLength(
    ClaimedBarcodeScanner scanner,
    uint symbology, 
    BarcodeSymbologyDecodeLengthKind kind, 
    uint decodeLength1, 
    uint decodeLength2)
{
    bool success = false;
    BarcodeSymbologyAttributes attributes = await scanner.GetSymbologyAttributesAsync(symbology);

    if (attributes.IsDecodeLengthSupported)
    {
        attributes.DecodeLengthKind = kind;
        attributes.DecodeLength1 = decodeLength1;
        attributes.DecodeLength2 = decodeLength2;
        success = await scanner.SetSymbologyAttributesAsync(symbology, attributes);
    }

    return success;
}
```

### <a name="check-digit-transmission"></a>Проверьте передачи цифр

Другой атрибут, который можно установить на определенную символику является ли контрольную цифру, будут отправлены в узле как часть необработанные данные. Прежде чем задавать это, убедитесь, что символика поддерживает проверку цифр передачу данных с [IsCheckDigitTransmissionSupported](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.barcodesymbologyattributes.ischeckdigittransmissionsupported). Затем установите ли проверка цифр передачи включается с помощью [IsCheckDigitTransmissionEnabled](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.barcodesymbologyattributes.ischeckdigittransmissionenabled).

В следующем фрагменте кода показано передачи цифр проверьте параметр:

```cs
private async Task<bool> SetCheckDigitTransmission(ClaimedBarcodeScanner scanner, uint symbology, bool isEnabled)
{
    bool success = false;
    BarcodeSymbologyAttributes attributes = await scanner.GetSymbologyAttributesAsync(symbology);

    if (attributes.IsCheckDigitTransmissionSupported)
    {
        attributes.IsCheckDigitTransmissionEnabled = isEnabled;
        success = await scanner.SetSymbologyAttributesAsync(symbology, attributes);
    }

    return success;
}
```

### <a name="check-digit-validation"></a>Выполнить проверку цифр

Можно также задать ли будут проверены контрольную цифру штрих-кода. Прежде чем задавать это, убедитесь, что символика поддерживает проверку проверки цифр с [IsCheckDigitValidationSupported](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.barcodesymbologyattributes.ischeckdigitvalidationsupported). Затем установите ли с [IsCheckDigitValidationEnabled](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.barcodesymbologyattributes.ischeckdigitvalidationenabled)включена проверка цифр проверки.

В следующем фрагменте кода показан параметр проверки цифр проверки:

```cs
private async Task<bool> SetCheckDigitValidation(ClaimedBarcodeScanner scanner, uint symbology, bool isEnabled)
{
    bool success = false;
    BarcodeSymbologyAttributes attributes = await scanner.GetSymbologyAttributesAsync(symbology);

    if (attributes.IsCheckDigitValidationSupported)
    {
        attributes.IsCheckDigitValidationEnabled = isEnabled;
        success = await scanner.SetSymbologyAttributesAsync(symbology, attributes);
    }

    return success;
}
```

[!INCLUDE [feedback](./includes/pos-feedback.md)]

## <a name="see-also"></a>См. также

* [Сканер штрихкодов](pos-barcodescanner.md)
* [Класс BarcodeSymbologies](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.barcodesymbologies)
* [Класс BarcodeScanner](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.barcodescanner)
* [Класс ClaimedBarcodeScanner](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.claimedbarcodescanner)
* [Класс BarcodeSymbologyAttributes](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.barcodesymbologyattributes)
* [Перечисление BarcodeSymbologyDecodeLengthKind](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.barcodesymbologydecodelengthkind)
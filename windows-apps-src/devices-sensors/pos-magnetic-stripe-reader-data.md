---
author: eliotcowley
title: Получение и данных считывателя магнитных карт
description: Узнайте, как получить и интерпретировать данные от считывателя магнитных карт.
ms.author: elcowle
ms.date: 10/04/2018
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: Windows 10, uwp, точка службы, pos, считыватель магнитных карт
ms.localizationpriority: medium
ms.openlocfilehash: ad954e8c03d92307fa72ead236d5428ac2bdddab
ms.sourcegitcommit: 106aec1e59ba41aae2ac00f909b81bf7121a6ef1
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/15/2018
ms.locfileid: "4624232"
---
# <a name="obtain-and-understand-magnetic-stripe-data"></a>Получение и данных считывателя магнитных карт

После настройки вашей считыватель магнитных карт в приложении с помощью действий, описанных в [Начало работы с POS-устройство](pos-basics.md), вы готовы начать извлечение данных из него.

## <a name="subscribe-to-datareceived-events"></a>Подпишитесь на * DataReceived событий

Всякий раз, когда средство чтения распознает карту перетаскиваемым, он вызовет одно из трех событий:

* [Событие AamvaCardDataReceived](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.claimedmagneticstripereader.aamvacarddatareceived): происходит, когда сдвинуты карту автомобилей.
* [Событие BankCardDataReceived](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.claimedmagneticstripereader.aamvacarddatareceived): происходит, когда сдвинуты на банковскую карту.
* [Событие VendorSpecificDataReceived](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.claimedmagneticstripereader.vendorspecificdatareceived): происходит, когда сдвинуты зависящие от поставщика карты.

Только приложению необходимо подписаться на события, которые поддерживаются считыватель магнитных карт. Вы можете увидеть, какие типы карт поддерживаются с помощью [MagneticStripeReader.SupportedCardTypes](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.magneticstripereader.supportedcardtypes
).

Приведенный ниже код демонстрирует подписывается на три ***DataReceived** событий:

```cs
private void SubscribeToEvents(ClaimedMagneticStripeReader claimedReader, MagneticStripeReader reader)
{
    foreach (var type in reader.SupportedCardTypes)
    {
        if (item == MagneticStripeReaderCardTypes.Aamva)
        {
            claimedReader.AamvaCardDataReceived += Reader_AamvaCardDataReceived;
        }
        else if (item == MagneticStripeReaderCardTypes.Bank)
        {
            claimedReader.BankCardDataReceived += Reader_BankCardDataReceived;
        }
        else if (item == MagneticStripeReaderCardTypes.ExtendedBase)
        {
            claimedReader.VendorSpecificDataReceived += Reader_VendorSpecificDataReceived;
        }
    }
}
```

Обработчик событий будет передаваться [ClaimedMagneticStripeReader](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.claimedmagneticstripereader) и объект *аргументов* , тип которого зависит от события:

* Событие **AamvaCardDataReceived** : [Класс MagneticStripeReaderAamvaCardDataReceivedEventArgs](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.magneticstripereaderaamvacarddatareceivedeventargs)
* Событие **BankCardDataReceived** : [Класс MagneticStripeReaderBankCardDataReceivedEventArgs](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.magneticstripereaderbankcarddatareceivedeventargs)
* Событие **VendorSpecificDataReceived** : [Класс MagneticStripeReaderVendorSpecificCardDataReceivedEventArgs](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.magneticstripereadervendorspecificcarddatareceivedeventargs)

## <a name="get-the-data"></a>Получение данных

Для событий **AamvaCardDataReceived** и **BankCardDataReceived** вы можете получить некоторые данные непосредственно из объекта *аргументов* . В следующем примере показано получение нескольких свойств и присвоение переменных-членов:

```cs
private string _accountNumber;
private string _expirationDate;
private string _firstName;

private void Reader_BankCardDataReceived(
    ClaimedMagneticStripeReader sender, 
    MagneticStripeReaderBankCardDataReceivedEventArgs args)
{
    _accountNumber = args.AccountNumber;
    _expirationDate = args.ExpirationDate;
    _firstName = args.FirstName;
    // etc...
}
```

Тем не менее некоторые данные, включая все данные из события **VendorSpecificDataReceived** , необходимо получить через объект **отчета** , который является свойством параметра *аргументов* . Это тип [MagneticStripeReaderReport](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.magneticstripereaderreport).

Используйте свойство [CardType](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.magneticstripereaderreport.cardtype) , чтобы определить, какой тип карточки были сдвинуты и использовать их для информирования как интерпретировать данные от [Track1](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.magneticstripereaderreport.track1), [Track2](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.magneticstripereaderreport.track2), [Track3](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.magneticstripereaderreport.track3)и [Track4](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.magneticstripereaderreport.track4).

Данные в каждом из дорожки представлены в виде объектов [MagneticStripeReaderTrackData](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.magneticstripereadertrackdata) . От этого класса можно получить следующие типы данных:

* [Данные](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.magneticstripereadertrackdata.data): необработанные или декодированные данные.
* [DiscretionaryData](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.magneticstripereadertrackdata.discretionarydata): избирательной данных. 
* [EncryptedData](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.magneticstripereadertrackdata.encrypteddata): зашифрованные данные.

В следующем фрагменте кода получает отчета и отслеживание данных, а затем проверяет тип карты:

```cs
private void GetTrackData(MagneticStripeReaderBankCardDataReceivedEventArgs args)
{
    MagneticStripeReaderReport report = args.Report;
    IBuffer track1 = report.Track1.Data;
    IBuffer track2 = report.Track2.Data;
    IBuffer track3 = report.Track3.Data;
    IBuffer track4 = report.Track4.Data;

    if (report.CardType == MagneticStripeReaderCardTypes.Aamva)
    {
        // Card type is AAMVA
    }
    else if (report.CardType == MagneticStripeReaderCardTypes.Bank)
    {
        // Card type is bank card
    }
    else if (report.CardType == MagneticStripeReaderCardTypes.ExtendedBase)
    {
        // Card type is vendor-specific
    }
    else if (report.CardType == MagneticStripeReaderCardTypes.Unknown)
    {
        // Card type is unknown
    }
}
```

[!INCLUDE [feedback](./includes/pos-feedback.md)]

## <a name="see-also"></a>См. также

* [Считыватель магнитных карт](pos-magnetic-stripe-reader.md)
* [Класс ClaimedMagneticStripeReader](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.claimedmagneticstripereader)
* [Класс MagneticStripeReaderAamvaCardDataReceivedEventArgs](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.magneticstripereaderaamvacarddatareceivedeventargs)
* [Класс MagneticStripeReaderBankCardDataReceivedEventArgs](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.magneticstripereaderbankcarddatareceivedeventargs)
* [Класс MagneticStripeReaderVendorSpecificCardDataReceivedEventArgs](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.magneticstripereadervendorspecificcarddatareceivedeventargs)
* [MagneticStripeReaderReport](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.magneticstripereaderreport)
* [MagneticStripeReaderTrackData](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.magneticstripereadertrackdata)
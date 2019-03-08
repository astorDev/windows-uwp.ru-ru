---
title: Настройка сканера штрихкодов на базе камеры
description: Включение и отключение сканера штрихкодов на базе камеры
ms.date: 05/02/2018
ms.topic: article
keywords: Windows 10, UWP, точка обслуживания, POS
ms.localizationpriority: medium
ms.openlocfilehash: 3db54caa5be88559aa44f9a99273e9d8d5d6a00d
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57610309"
---
# <a name="enable-or-disable-the-software-decoder-that-ships-with-windows"></a>Включение и отключение программного декодера, который входит в состав Windows
В Windows 10 версии 1803 программный декодер устанавливается и включается по умолчанию.  Вы можете отключить встроенный в Windows программный декодер, если не хотите использовать сканер штрихкодов на базе камеры или если вы приобрели сторонний декодер, который работает с API-интерфейсами Windows.Devices.PointOfService.BarcodeScanner, и вам не нужно использовать оба типа сканеров.

## <a name="enable-or-disable-using-the-system-registry"></a>Включение и отключение с помощью системного реестра
Программный декодер, который входит в состав Windows, можно включать и отключать с помощью системного реестра путем добавления раздела реестра *InboxDecoder* и *HKLM\Software\Microsoft\PointOfService\BarcodeScanner* и задания для него значения *Включить*, как показано ниже.

| Значение  | Тип значения | Значение | Состояние |
| ----------- | --------- | -------|--------|
| Включить      | DWORD     | 1 (по умолчанию)<br/>0 |  Включает программный декодер, который входит в состав Windows <br/> Отключает программный декодер, который входит в состав Windows |


Ниже приведен пример файла реестра, который можно использовать для **отключения** встроенного в Windows программного декодера:

```
Windows Registry Editor Version 5.00

[HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\PointOfService\BarcodeScanner\InboxDecoder]
"Enable"=dword:0000000


```  
    
Этот пример файла реестра можно использовать для **включения** встроенного в Windows программного декодера:

```
Windows Registry Editor Version 5.00

[HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\PointOfService\BarcodeScanner\InboxDecoder]
"Enable"=dword:0000001


```  

> [!Warning] 
> Неправильное изменение реестра может привести к серьезным проблемам.  В целях безопасности делайте резервную копию реестра, прежде чем вносить в него изменения.  Это позволит восстановить реестр, если возникнут проблемы.  Дополнительные сведения о резервном копировании и восстановлении реестра см. в следующей статье базы знаний Майкрософт: <br/><br/> [322756](https://support.microsoft.com/kb/322756) Резервное копирование и восстановление реестра Windows.

> [!NOTE]
> Программный декодер, встроенный в Windows 10, любезно предоставлен [**Digimarc Corporation**](https://www.digimarc.com/).

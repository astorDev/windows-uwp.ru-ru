---
ms.assetid: 98BD79B3-F420-43C5-98D3-52EBDDB479A0
description: В этой статье перечисляются параметры кодирования, которые можно использовать с BitmapEncoder.
title: Справочник по параметрам BitmapEncoder
ms.date: 02/08/2017
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: 07f5c6ef180cb4abe90a705e73be8d99ecbd2ca7
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57603189"
---
# <a name="bitmapencoder-options-reference"></a>Справочник по параметрам BitmapEncoder


В этой статье перечисляются параметры кодирования, которые можно использовать с [**BitmapEncoder**](https://msdn.microsoft.com/library/windows/apps/br226206). Параметр кодирования определяется именем в виде строки и значением в виде определенного типа данных ([**Windows.Foundation.PropertyType**](https://msdn.microsoft.com/library/windows/apps/br225871)). Сведения о работе с изображениями см. в разделе [Создание, редактирование и сохранение растровых изображений](imaging.md).

| Имя                    | PropertyType | Примечания по использованию                                                                                        | Допустимые форматы |
|-------------------------|--------------|----------------------------------------------------------------------------------------------------|---------------|
| ImageQuality            | single       | Допустимые значения от 0 до 1,0. Более высокие значения обозначают более высокое качество                                 | JPEG, JPEG-XR |
| CompressionQuality      | single       | Допустимые значения от 0 до 1,0. Более высокие значения обозначают более эффективную и медленную схему сжатия | TIFF          |
| Lossless                | Логический      | Если для параметра установлено значение "Истина", параметр ImageQuality игнорируется                                        | JPEG-XR       |
| InterlaceOption         | Логический      | Определяет, нужно ли чересстрочное отображение изображения                                                                    | PNG           |
| FilterOption            | uint8        | Использование перечисления [**PngFilterMode**](https://msdn.microsoft.com/library/windows/apps/br226389)                                | PNG           |
| TiffCompressionMethod   | uint8        | Использование перечисления [**TiffCompressionMode**](https://msdn.microsoft.com/library/windows/apps/br226399)                    | TIFF          |
| Luminance               | uint32Array  | Массив из 64 элементов, содержащий постоянные квантования яркости                               | JPEG          |
| Chrominance             | uint32Array  | Массив из 64 элементов, содержащий постоянные квантования цветопередачи                             | JPEG          |
| JpegYCrCbSubsampling    | uint8        | Использование перечисления [**JpegSubsamplingMode**](https://msdn.microsoft.com/library/windows/apps/br226386)                    | JPEG          |
| SuppressApp0            | Логический      | Определяет, нужно ли подавлять создание блока метаданных App0                                        | JPEG          |
| EnableV5Header32bppBGRA | Логический      | Определяет, нужно ли кодировать BMP 5-й версии с поддержкой альфа                                         | BMP           |

 

## <a name="related-topics"></a>Статьи по теме

* [Создавать, изменять и сохранять точечных рисунков](imaging.md)
* [Поддерживаемые кодеки](supported-codecs.md)

 





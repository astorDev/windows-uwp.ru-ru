---
ms.assetid: 98BD79B3-F420-43C5-98D3-52EBDDB479A0
description: В этой статье перечисляются параметры кодирования, которые можно использовать с BitmapEncoder.
title: Справочник по параметрам BitmapEncoder
ms.date: 02/08/2017
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: 34e2ac1531b496f076abbe8e23ffa1c0cb318373
ms.sourcegitcommit: ac7f3422f8d83618f9b6b5615a37f8e5c115b3c4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/29/2019
ms.locfileid: "66359045"
---
# <a name="bitmapencoder-options-reference"></a>Справочник по параметрам BitmapEncoder


В этой статье перечисляются параметры кодирования, которые можно использовать с [**BitmapEncoder**](https://docs.microsoft.com/uwp/api/Windows.Graphics.Imaging.BitmapEncoder). Параметр кодирования определяется именем в виде строки и значением в виде определенного типа данных ([**Windows.Foundation.PropertyType**](https://docs.microsoft.com/uwp/api/Windows.Foundation.PropertyType)). Сведения о работе с изображениями см. в разделе [Создание, редактирование и сохранение растровых изображений](imaging.md).

| Имя                    | PropertyType | Примечания по использованию                                                                                        | Допустимые форматы |
|-------------------------|--------------|----------------------------------------------------------------------------------------------------|---------------|
| ImageQuality            | single       | Допустимые значения от 0 до 1,0. Более высокие значения обозначают более высокое качество                                 | JPEG, JPEG-XR |
| CompressionQuality      | single       | Допустимые значения от 0 до 1,0. Более высокие значения обозначают более эффективную и медленную схему сжатия | TIFF          |
| Lossless                | Логический      | Если для параметра установлено значение "Истина", параметр ImageQuality игнорируется                                        | JPEG-XR       |
| InterlaceOption         | Логический      | Определяет, нужно ли чересстрочное отображение изображения                                                                    | PNG           |
| FilterOption            | uint8        | Использование перечисления [**PngFilterMode**](https://docs.microsoft.com/uwp/api/Windows.Graphics.Imaging.PngFilterMode)                                | PNG           |
| TiffCompressionMethod   | uint8        | Использование перечисления [**TiffCompressionMode**](https://docs.microsoft.com/uwp/api/Windows.Graphics.Imaging.TiffCompressionMode)                    | TIFF          |
| Luminance               | uint32Array  | Массив из 64 элементов, содержащий постоянные квантования яркости                               | JPEG          |
| Chrominance             | uint32Array  | Массив из 64 элементов, содержащий постоянные квантования цветопередачи                             | JPEG          |
| JpegYCrCbSubsampling    | uint8        | Использование перечисления [**JpegSubsamplingMode**](https://docs.microsoft.com/uwp/api/Windows.Graphics.Imaging.JpegSubsamplingMode)                    | JPEG          |
| SuppressApp0            | Логический      | Определяет, нужно ли подавлять создание блока метаданных App0                                        | JPEG          |
| EnableV5Header32bppBGRA | Логический      | Определяет, нужно ли кодировать BMP 5-й версии с поддержкой альфа                                         | BMP           |

 

## <a name="related-topics"></a>См. также

* [Создание, редактирование и сохранение точечных рисунков](imaging.md)
* [Поддерживаемые кодеки](supported-codecs.md)

 





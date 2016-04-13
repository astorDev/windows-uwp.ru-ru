---
ms.assetid: 98BD79B3-F420-43C5-98D3-52EBDDB479A0
description: В этой статье перечисляются параметры кодирования, которые можно использовать с BitmapEncoder.
title: Справочник по параметрам BitmapEncoder
---

# Справочник по параметрам BitmapEncoder

\[ Обновлено для приложений UWP в Windows 10. Статьи, касающиеся Windows 8.x, см. в разделе [Архив](http://go.microsoft.com/fwlink/p/?linkid=619132) \]

В этой статье перечисляются параметры кодирования, которые можно использовать с [**BitmapEncoder**](https://msdn.microsoft.com/library/windows/apps/br226206). Параметр кодирования определяется именем в виде строки и значением в виде определенного типа данных ([**Windows.Foundation.PropertyType**](https://msdn.microsoft.com/library/windows/apps/br225871)). Сведения о работе с изображениями см. в статье [Обработка изображений](imaging.md).

| Name                    | PropertyType | Примечания по использованию                                                                                        | Допустимые форматы |
|-------------------------|--------------|----------------------------------------------------------------------------------------------------|---------------|
| ImageQuality            | single       | Допустимые значения от 0 до 1,0. Более высокие значения обозначают более высокое качество.                                 | JPEG, JPEG-XR |
| CompressionQuality      | single       | Допустимые значения от 0 до 1,0. Более высокие значения обозначают более эффективную и медленную схему сжатия. | TIFF          |
| Lossless                | логический      | Если для параметра установлено значение "Истина", параметр ImageQuality игнорируется.                                        | JPEG-XR       |
| InterlaceOption         | логический      | Определяет, нужно ли чересстрочное отображение изображения.                                                                    | PNG           |
| FilterOption            | uint8        | Использование перечисления [**PngFilterMode**](https://msdn.microsoft.com/library/windows/apps/br226389).                                | PNG           |
| TiffCompressionMethod   | uint8        | Использование перечисления [**TiffCompressionMode**](https://msdn.microsoft.com/library/windows/apps/br226399).                    | TIFF          |
| Luminance               | uint32Array  | Массив из 64 элементов, содержащий постоянные квантования яркости.                               | JPEG          |
| Chrominance             | uint32Array  | Массив из 64 элементов, содержащий постоянные квантования цветопередачи.                             | JPEG          |
| JpegYCrCbSubsampling    | uint8        | Использование перечисления [**JpegSubsamplingMode**](https://msdn.microsoft.com/library/windows/apps/br226386).                    | JPEG          |
| SuppressApp0            | логический      | Определяет, нужно ли подавлять создание блока метаданных App0.                                        | JPEG          |
| EnableV5Header32bppBGRA | логический      | Определяет, нужно ли кодировать BMP 5-й версии с поддержкой альфа.                                         | BMP           |

 

## Связанные темы

* [Обработка изображений](imaging.md)
 

 






<!--HONumber=Mar16_HO1-->



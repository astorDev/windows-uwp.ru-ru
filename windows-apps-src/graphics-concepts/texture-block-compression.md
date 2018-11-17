---
title: Блочное сжатие текстур
description: Поддержка блочного сжатия (BC) текстур в Direct3D 11 была расширена, и теперь включает алгоритмы BC6H и BC7.
ms.assetid: 63506C46-BF14-464B-B20C-8B8F359E7AFE
keywords:
- Блочное сжатие текстур
author: michaelfromredmond
ms.author: mithom
ms.date: 02/08/2017
ms.topic: article
ms.localizationpriority: medium
ms.openlocfilehash: 46c58da3dbe425b055855423aa9e9cebaa06f929
ms.sourcegitcommit: 3257416aebb5a7b1515e107866806f8bd57845a8
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/16/2018
ms.locfileid: "7147602"
---
# <a name="texture-block-compression"></a>Блочное сжатие текстур


В Direct3D 11 расширена поддержка сжатия блоков для текстур и теперь она включает алгоритмы BC6H и BC7. Алгоритм BC6H поддерживает исходные данные с расширенным динамическими диапазоном, а BC7 обеспечивает качество сжатия выше среднего с меньшим количеством артефактов для исходных данных в формате RGB.

Более подробные сведения о поддержке алгоритма блочного сжатия в версиях до Direct3D 11 включая поддержку форматов BC1–BC5 см. в статье [Блочное сжатие (Direct3D 10)](https://msdn.microsoft.com/library/windows/desktop/bb694531).

**Примечание о форматах файлов:** Форматы сжатия текстур BC6H и BC7 используют для хранения сжатых данных текстур формат файлов DDS. Дополнительные сведения см. в статье [Руководство по программированию для DDS](https://msdn.microsoft.com/library/windows/desktop/bb943991).

## <a name="span-idblockcompressionformatssupportedindirect3d11spanspan-idblockcompressionformatssupportedindirect3d11spanspan-idblockcompressionformatssupportedindirect3d11spanblock-compression-formats-supported-in-direct3d-11"></a><span id="Block_Compression_Formats_Supported_in_Direct3D_11"></span><span id="block_compression_formats_supported_in_direct3d_11"></span><span id="BLOCK_COMPRESSION_FORMATS_SUPPORTED_IN_DIRECT3D_11"></span>Форматы блочного сжатия, поддерживаемые в Direct3D 11


| Исходные данные                                  | Минимальное требуемое разрешение сжатия данных                              | Рекомендуемый формат | Минимальный поддерживаемый уровень возможностей |
|----------------------------------------------|---------------------------------------------------------------------------|--------------------|---------------------------------|
| Трехканальный цвет с альфа-каналом       | Три цветовых канала (5 бит:6 бит:5 бит), с нулевым или 1-битным альфа-каналом  | BC1                | Direct3D9.1                    |
| Трехканальный цвет с альфа-каналом       | Три цветовых канала (5 бит:6 бит:5 бит), с 4-битным альфа-каналом         | BC2                | Direct3D9.1                    |
| Трехканальный цвет с альфа-каналом       | Три цветовых канала (5 бит:6 бит:5 бит) с 8-битным альфа-каналом          | BC3                | Direct3D9.1                    |
| Одноканальный цвет                            | Один цветовой канал (8 бит)                                                | BC4                | Direct3D10                     |
| Двухканальный цвет                            | Два цветовых канала (8 бит:8 бит)                                        | BC5                | Direct3D10                     |
| Трехканальный цвет с расширенным динамическим диапазоном (HDR) | Три цветовых канала (16 бит:16 бит:16 бит), число половинной точности\ * | BC6H               | Direct3D11                     |
| Трехканальный цвет, альфа-канал необязателен  | Три цветовых канала (4–7 бит на канал), альфа-канал 0–8 бит  | BC7                | Direct3D11                     |

 

\*Число половинной точности — это 16-разрядное значение, состоящее из необязательного бита знака, 5-разрядного смещенного порядка и 10- или 11-разрядной мантиссы.
## <a name="span-idbc1bc2andb3formatsspanspan-idbc1bc2andb3formatsspanspan-idbc1bc2andb3formatsspanbc1-bc2-and-b3-formats"></a><span id="BC1__BC2__and_B3_Formats"></span><span id="bc1__bc2__and_b3_formats"></span><span id="BC1__BC2__AND_B3_FORMATS"></span>Форматы BC1, BC2 и В3


Форматы BC1, BC2 и BC3 эквивалентны форматам сжатия текстур DXTn Direct3D 9 и совпадают с соответствующими форматами BC1, BC2, и BC3 в Direct3D. Поддержка этих трех форматов требуется для всех уровней возможностей (D3D\_FEATURE\_LEVEL\_9\_1, D3D\_FEATURE\_LEVEL\_9\_2, D3D\_FEATURE\_LEVEL\_9\_3, D3D\_FEATURE\_LEVEL\_10\_0, D3D\_FEATURE\_LEVEL\_10\_1, и D3D\_FEATURE\_LEVEL\_11\_0).

| Формат блочного сжатия | Формат DXGI                                                                           | Эквивалентный формат в Direct3D9                               | Байт в блоке 4×4 пикселя |
|--------------------------|---------------------------------------------------------------------------------------|------------------------------------------------------------|---------------------------|
| BC1                      | DXGI\_FORMAT\_BC1\_UNORM, DXGI\_FORMAT\_BC1\_UNORM\_SRGB, DXGI\_FORMAT\_BC1\_TYPELESS | D3DFMT\_DXT1, FourCC="DXT1"                                | 8                         |
| BC2                      | DXGI\_FORMAT\_BC2\_UNORM, DXGI\_FORMAT\_BC2\_UNORM\_SRGB, DXGI\_FORMAT\_BC2\_TYPELESS | D3DFMT\_DXT2\*, FourCC="DXT2", D3DFMT\_DXT3, FourCC="DXT3" | 16                        |
| BC3                      | DXGI\_FORMAT\_BC3\_UNORM, DXGI\_FORMAT\_BC3\_UNORM\_SRGB, DXGI\_FORMAT\_BC3\_TYPELESS | D3DFMT\_DXT4\*, FourCC="DXT4", D3DFMT\_DXT5, FourCC="DXT5" | 16                        |

 

\*В данных схемах сжатия (DXT2 и DXT4) нет делается различий между форматами альфа-канала с предварительным умножением в Direct3D 9 и стандартными форматами альфа-канала. Это различие должно обрабатываться программируемыми шейдерами во время отрисовки.

## <a name="span-idbc4andbc5formatsspanspan-idbc4andbc5formatsspanspan-idbc4andbc5formatsspanbc4-and-bc5-formats"></a><span id="BC4_and_BC5_Formats"></span><span id="bc4_and_bc5_formats"></span><span id="BC4_AND_BC5_FORMATS"></span>Форматы BC4 и BC5


| Форматы блочного сжатия | Формат DXGI                                                                     | Эквивалентный формат в Direct3D9 | Байт в блоке 4×4 пикселя |
|--------------------------|---------------------------------------------------------------------------------|------------------------------|---------------------------|
| BC4                      | DXGI\_FORMAT\_BC4\_UNORM, DXGI\_FORMAT\_BC4\_SNORM, DXGI\_FORMAT\_BC4\_TYPELESS | FourCC="ATI1"                | 8                         |
| BC5                      | DXGI\_FORMAT\_BC5\_UNORM, DXGI\_FORMAT\_BC5\_SNORM, DXGI\_FORMAT\_BC5\_TYPELESS | FourCC="ATI2"                | 16                        |

 

## <a name="span-idbc6hformatspanspan-idbc6hformatspanspan-idbc6hformatspanbc6h-format"></a><span id="BC6H_Format"></span><span id="bc6h_format"></span><span id="BC6H_FORMAT"></span>Формат BC6H


Дополнительные сведения об этом формате см. в документации по [формату BC6H](https://msdn.microsoft.com/library/windows/desktop/hh308952).

| Формат блочного сжатия | Формат DXGI                                                                      | Эквивалентный формат в Direct3D9 | Байт в блоке 4×4 пикселя |
|--------------------------|----------------------------------------------------------------------------------|------------------------------|---------------------------|
| BC6H                     | DXGI\_FORMAT\_BC6H\_UF16, DXGI\_FORMAT\_BC6H\_SF16, DXGI\_FORMAT\_BC6H\_TYPELESS | Н/Д                          | 16                        |

 

Для формата BC6H можно выбирать разные режимы кодирования для каждого блока размером 4×4 пикселя. Доступно 14 различных режимов кодирования, каждый из которых обладает собственными, немного отличающимися от других плюсами и минусами, касающимися итогового качества отображения текстуры. Выбор режима обеспечивает быстрое декодирование оборудованием с выбранным или адаптированным в соответствии с исходным содержимым уровнем качества, но при этом значительно увеличивает сложность пространства поиска.

## <a name="span-idbc7formatspanspan-idbc7formatspanspan-idbc7formatspanbc7-format"></a><span id="BC7_Format"></span><span id="bc7_format"></span><span id="BC7_FORMAT"></span>Формат BC7


Дополнительные сведения об этом формате см. в документации по [формату BC7](https://msdn.microsoft.com/library/windows/desktop/hh308953).

| Формат блочного сжатия | Формат DXGI                                                                           | Эквивалентный формат в Direct3D9 | Байт в блоке 4×4 пикселя |
|--------------------------|---------------------------------------------------------------------------------------|------------------------------|---------------------------|
| BC7                      | DXGI\_FORMAT\_BC7\_UNORM, DXGI\_FORMAT\_BC7\_UNORM\_SRGB, DXGI\_FORMAT\_BC7\_TYPELESS | Н/Д                          | 16                        |

 

Для формата BC7 можно выбирать разные режимы кодирования для каждого блока размером 4×4 пикселя. Доступно 8 различных режимов кодирования, каждый из которых обладает собственными, немного отличающимися от других плюсами и минусами, касающимися итогового качества отображения текстуры. Выбор режима обеспечивает быстрое декодирование оборудованием с выбранным или адаптированным в соответствии с исходным содержимым уровнем качества, но при этом значительно увеличивает сложность пространства поиска.

## <a name="span-idrelated-topicsspanrelated-topics"></a><span id="related-topics"></span>Статьи по теме


[Приложения](appendix.md)

[Текстуры](https://msdn.microsoft.com/library/windows/desktop/ff476902)

 

 





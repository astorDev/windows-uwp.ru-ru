---
title: Формат BC7
description: Формат BC7— это формат сжатия текстур, используемый для высококачественного сжатия данных RGB и RGBA.
ms.assetid: 788B6E8C-9A1F-45F9-BE49-742285E8D8A6
keywords:
- Формат BC7
author: michaelfromredmond
ms.author: mithom
ms.date: 02/08/2017
ms.topic: article
ms.localizationpriority: medium
ms.openlocfilehash: 70380dd0bd07cfe0c81e8339f8606029663b47d4
ms.sourcegitcommit: 38f06f1714334273d865935d9afb80efffe97a17
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/09/2018
ms.locfileid: "6184551"
---
# <a name="bc7-format"></a>Формат BC7


Формат BC7— это формат сжатия текстур, используемый для высококачественного сжатия данных RGB и RGBA.

Сведения о режимах блоков формата BC7 доступны в статье [Справочник по режимам формата BC7](https://msdn.microsoft.com/library/windows/desktop/hh308954).

## <a name="span-idabout-bc7-dxgi-format-bc7spanspan-idabout-bc7-dxgi-format-bc7spanspan-idabout-bc7-dxgi-format-bc7spanabout-bc7dxgiformatbc7"></a><span id="About-BC7-DXGI-FORMAT-BC7"></span><span id="about-bc7-dxgi-format-bc7"></span><span id="ABOUT-BC7-DXGI-FORMAT-BC7"></span>Сведения о BC7/DXGI\_FORMAT\_BC7


Формат BC7 задается с помощью следующих значений перечисления DXGI\_FORMAT:

-   **DXGI\_FORMAT\_BC7\_TYPELESS**.
-   **DXGI\_FORMAT\_BC7\_UNORM**.
-   **DXGI\_FORMAT\_BC7\_UNORM\_SRGB**.

Формат BC7 можно использовать для ресурсов текстуры [Texture2D](https://msdn.microsoft.com/library/windows/desktop/bb205277) (включая массивы), Texture3D или TextureCube (включая массивы). Аналогичным образом этот формат применяется к любым поверхностям MIP-карты, связанным с этими ресурсами.

В BC7 используется фиксируемый размер блоков— 16 байтов (128 битов) и фиксированный размер плитки— 4x4 текселя. Как и в случае с предыдущими форматами BC, изображения текстур крупнее, чем поддерживаемый размер плитки (4x4), сжимаются с использованием нескольких блоков. Это же удостоверение адресации также применяется к трехмерным изображениям, MIP-картам, картам кубов и массивам текстуры. Все плитки изображений должны иметь один формат.

BC7 сжимает трехканальные (RGB) и четырехканальные (RGBA) изображения данных с фиксированной запятой. Как правило, источник данных имеет 8 битов на компонент цвета (канал), хотя этот формат в состоянии шифровать исходные данные с большим числом битов на компонент цвета. Все плитки изображений должны иметь один формат.

Декодер BC7 выполняет распаковку до применения фильтрации текстур.

Оборудование для распаковки BC7 должно иметь битовую точность, то есть возвращать результаты, идентичные результатам, возвращаемым декодером, который описан в этом документе.

## <a name="span-idbc7-implementationspanspan-idbc7-implementationspanspan-idbc7-implementationspanbc7-implementation"></a><span id="BC7-Implementation"></span><span id="bc7-implementation"></span><span id="BC7-IMPLEMENTATION"></span>Реализация BC7


Реализация BC7 может указывать один из 8 режимов, при этом режим будет указан в наименее значимом бите из 16-байтового (128-битового) блока. Этот режим кодируется нулевым или большим количеством битов со значением 0, за которым следует 1.

Блок BC7 может содержать несколько пар конечных точек. Набора индексов, которые соответствуют паре конечных точек, может называться "субъект". Кроме того, в некоторых режимах блока представление конечной точки кодируется в форме, которую можно назвать RBGP, где бит "P" представляет общий наименее значимый бит для компонентов цвета конечной точки. Например, если представление конечной точки для формата имеет вид RGB 5.5.5.1, то конечная точка интерпретируется как значение RGB 6.6.6, где состояние P-бита определяет наименее значимый бит каждого компонента. Аналогично и для исходных данных с альфа-каналом: если представление формата имеет вид RGBAP 5.5.5.5.1, конечная точка интерпретируется как RGBA 6.6.6.6. В зависимости от режима блоков можно указать общий наименее значимый бит для обеих конечных точек подмножества по отдельности (2 P-бита на подмножество) или совместно (1 P-бит на подмножество).

Для блоков BC7, которые явно не кодируют альфа-компонент, блок BC7 состоит из битов режима, битов раздела, сжатых конечных точек, сжатых индексов и необязательного P-бита. В этих блоках конечные точки имеют представление только RGB, а альфа-компонент декодируется как 1.0 для всех текселей в исходных данных.

Для блоков BC7 с объединенными компонентами цвета и альфа-компонентами блок состоит из битов режима, сжатых конечных точек, сжатых индексов, необязательных битов раздела и P-бита. В этих блоках цвета конечной точки выражаются в формате RGBA, а значения альфа-компонента интерполируются вместе со значениями цветового компонента.

Для блоков BC7 с отдельными компонентами цвета и альфа-компонентами блок состоит из битов режима, битов поворота, сжатых конечных точек, сжатых индексов и необязательного бита средства выбора индексов. В этих блоках эффективный вектор RGB \[R, G, B\] и скалярный альфа-канал \[A\] кодируются по отдельности.

В следующей таблице перечислены компоненты каждого типа блоков.

| Блок BC7 содержит...     | биты режима | биты поворота | бит средства выбора индекса | биты разделов | сжатые конечные точки | P-бит    | сжатые индексы |
|---------------------------|-----------|---------------|--------------------|----------------|----------------------|----------|--------------------|
| только компоненты цвета     | обязательные  | Н/Д           | Н/Д                | обязательные       | обязательные             | необязательные | обязательные           |
| сочетание цвет + альфа    | обязательные  | Н/Д           | Н/Д                | необязательные       | обязательные             | необязательные | обязательные           |
| цвета и альфа по отдельности | обязательные  | обязательные      | необязательные           | Н/Д            | обязательные             | Н/Д      | обязательные           |

 

BC7 определяет палитру цветов по примерной линии между двумя конечными точками. Значение режима определяет количество интерполируемых пар конечных точек на блок. BC7 сохраняет один индекс палитры на тексель.

Для каждого подмножества индексов, соответствующего паре конечных точек, кодировщик фиксирует состояние одного бита сжатых данных индекса для этого подмножества. Для этого выбирается такой порядок конечных точек, который позволяет индексу так называемого назначенного индекса "исправления" задать свой самый значимый бит равным 0, и который затем можно удалить, оставив один бит на подмножество. Для режимов блоков с одним подмножеством индекс исправления— это всегда нулевой индекс.

## <a name="span-iddecoding-the-bc7-formatspanspan-iddecoding-the-bc7-formatspanspan-iddecoding-the-bc7-formatspandecoding-the-bc7-format"></a><span id="Decoding-the-BC7-Format"></span><span id="decoding-the-bc7-format"></span><span id="DECODING-THE-BC7-FORMAT"></span>Декодирование формата BC7


В псевдокоде ниже показаны действия по распаковке пикселя в точке (x,y) при наличии 16-байтового блока BC7

``` syntax
decompress_bc7(x, y, block)
{
    mode = extract_mode(block);
    
    //decode partition data from explicit partition bits
    subset_index = 0;
    num_subsets = 1;
    
    if (mode.type == 0 OR == 1 OR == 2 OR == 3 OR == 7)
    {
        num_subsets = get_num_subsets(mode.type);
        partition_set_id = extract_partition_set_id(mode, block);
        subset_index = get_partition_index(num_subsets, partition_set_id, x, y);
    }
    
    //extract raw, compressed endpoint bits
    UINT8 endpoint_array[num_subsets][4] = extract_endpoints(mode, block);
    
    //decode endpoint color and alpha for each subset
    fully_decode_endpoints(endpoint_array, mode, block);
    
    //endpoints are now complete.
    UINT8 endpoint_start[4] = endpoint_array[2 * subset_index];
    UINT8 endpoint_end[4]   = endpoint_array[2 * subset_index + 1];
        
    //Determine the palette index for this pixel
    alpha_index     = get_alpha_index(block, mode, x, y);
    alpha_bitcount  = get_alpha_bitcount(block, mode);
    color_index     = get_color_index(block, mode, x, y);
    color_bitcount  = get_color_bitcount(block, mode);

    //determine output
    UINT8 output[4];
    output.rgb = interpolate(endpoint_start.rgb, endpoint_end.rgb, color_index, color_bitcount);
    output.a   = interpolate(endpoint_start.a,   endpoint_end.a,   alpha_index, alpha_bitcount);
    
    if (mode.type == 4 OR == 5)
    {
        //Decode the 2 color rotation bits as follows:
        // 00 – Block format is Scalar(A) Vector(RGB) - no swapping
        // 01 – Block format is Scalar(R) Vector(AGB) - swap A and R
        // 10 – Block format is Scalar(G) Vector(RAB) - swap A and G
        // 11 - Block format is Scalar(B) Vector(RGA) - swap A and B
        rotation = extract_rot_bits(mode, block);
        output = swap_channels(output, rotation);
    }
    
}
```

В следующем псевдокоде показаны шаги, которые позволят полностью декодировать компоненты цвета и альфа-компоненты конечной точки для каждого подмножества с 16-байтовым блоком BC7.

``` syntax
fully_decode_endpoints(endpoint_array, mode, block)
{
    //first handle modes that have P-bits
    if (mode.type == 0 OR == 1 OR == 3 OR == 6 OR == 7)
    {
        for each endpoint i
        {
            //component-wise left-shift
            endpoint_array[i].rgba = endpoint_array[i].rgba << 1;
        }
        
        //if P-bit is shared
        if (mode.type == 1) 
        {
            pbit_zero = extract_pbit_zero(mode, block);
            pbit_one = extract_pbit_one(mode, block);
            
            //rgb component-wise insert pbits
            endpoint_array[0].rgb |= pbit_zero;
            endpoint_array[1].rgb |= pbit_zero;
            endpoint_array[2].rgb |= pbit_one;
            endpoint_array[3].rgb |= pbit_one;  
        }
        else //unique P-bit per endpoint
        {  
            pbit_array = extract_pbit_array(mode, block);
            for each endpoint i
            {
                endpoint_array[i].rgba |= pbit_array[i];
            }
        }
    }

    for each endpoint i
    {
        // Color_component_precision & alpha_component_precision includes pbit
        // left shift endpoint components so that their MSB lies in bit 7
        endpoint_array[i].rgb = endpoint_array[i].rgb << (8 - color_component_precision(mode));
        endpoint_array[i].a = endpoint_array[i].a << (8 - alpha_component_precision(mode));

        // Replicate each component's MSB into the LSBs revealed by the left-shift operation above
        endpoint_array[i].rgb = endpoint_array[i].rgb | (endpoint_array[i].rgb >> color_component_precision(mode));
        endpoint_array[i].a = endpoint_array[i].a | (endpoint_array[i].a >> alpha_component_precision(mode));
    }
        
    //If this mode does not explicitly define the alpha component
    //set alpha equal to 1.0
    if (mode.type == 0 OR == 1 OR == 2 OR == 3)
    {
        for each endpoint i
        {
            endpoint_array[i].a = 255; //i.e. alpha = 1.0f
        }
    }
}
```

Для создания каждого интерполированного компонента для каждого подмножества используйте следующий алгоритм: пусть c— это создаваемый компонент, e0— это компонент конечной точки 0 подмножества, а e1— это компонент конечной точки 1 подмножества.

``` syntax
UINT16 aWeight2[] = {0, 21, 43, 64};
UINT16 aWeight3[] = {0, 9, 18, 27, 37, 46, 55, 64};
UINT16 aWeight4[] = {0, 4, 9, 13, 17, 21, 26, 30, 34, 38, 43, 47, 51, 55, 60, 64};

UINT8 interpolate(UINT8 e0, UINT8 e1, UINT8 index, UINT8 indexprecision)
{
    if(indexprecision == 2)
        return (UINT8) (((64 - aWeights2[index])*UINT16(e0) + aWeights2[index]*UINT16(e1) + 32) >> 6);
    else if(indexprecision == 3)
        return (UINT8) (((64 - aWeights3[index])*UINT16(e0) + aWeights3[index]*UINT16(e1) + 32) >> 6);
    else // indexprecision == 4
        return (UINT8) (((64 - aWeights4[index])*UINT16(e0) + aWeights4[index]*UINT16(e1) + 32) >> 6);
}
```

В следующем псевдокоде показано извлечение индексов и число битов для компонентов цвета и альфа-компонентов. Блоки с отдельными компонентами цвета и альфа-компонентами также имеют два набора данных индекса: один— для векторного канала, другой— для скалярного. Для режима 4 эти индексы имеют разную ширину (2 или 3 бита), и также имеется однобитовое средство выбора, указывающее, используют ли векторные или скалярные данные 3-битовые индексы. (Извлечение количества альфа-битов аналогично извлечению количества цветовых битов, однако с обратным поведением в бите **idxMode**.)

``` syntax
bitcount get_color_bitcount(block, mode)
{
    if (mode.type == 0 OR == 1)
        return 3;
    
    if (mode.type == 2 OR == 3 OR == 5 OR == 7)
        return 2;
    
    if (mode.type == 6)
        return 4;
        
    //The only remaining case is Mode 4 with 1-bit index selector
    idxMode = extract_idxMode(block);
    if (idxMode == 0)
        return 2;
    else
        return 3;
}
```

## <a name="span-idbc7-format-mode-referencespanspan-idbc7-format-mode-referencespanspan-idbc7-format-mode-referencespanbc7-format-mode-reference"></a><span id="BC7-format-mode-reference"></span><span id="bc7-format-mode-reference"></span><span id="BC7-FORMAT-MODE-REFERENCE"></span>Справочник по режиму формата BC7


Этот раздел содержит список из 8 режимов блока и битовых выделений для блоков формата сжатия текстуры BC7.

Цвета для каждого подмножества в блоке представляются двумя явно заданными цветами конечных точек и подмножеством интерполированных цветов между ними. В зависимости от точности индекса блока каждое подмножество может иметь 4, 8 или 16 возможных цветов.

### <a name="span-idmode-0spanspan-idmode-0spanspan-idmode-0spanmode-0"></a><span id="Mode-0"></span><span id="mode-0"></span><span id="MODE-0"></span>Режим 0

Режим BC7 0 имеет следующие характеристики:

-   Только компоненты цвета (без альфа)
-   3 подмножества на каждый блок
-   Конечные точки RGBP 4.4.4.1 с уникальным P-битом на конечную точку
-   3-битовые индексы
-   16 разделов

![структура битов режима 0](images/bc7-mode0.png)

### <a name="span-idmode-1spanspan-idmode-1spanspan-idmode-1spanmode-1"></a><span id="Mode-1"></span><span id="mode-1"></span><span id="MODE-1"></span>Режим 1

Режим BC7 1 имеет следующие характеристики:

-   Только компоненты цвета (без альфа)
-   2 подмножества на каждый блок
-   Конечные точки RGBP 6.6.6.1 с общим P-битом на подмножество)
-   3-битовые индексы
-   64 разделов

![структура битов режима 1](images/bc7-mode1.png)

### <a name="span-idmode-2spanspan-idmode-2spanspan-idmode-2spanmode-2"></a><span id="Mode-2"></span><span id="mode-2"></span><span id="MODE-2"></span>Режим 2

Режим BC7 2 имеет следующие характеристики:

-   Только компоненты цвета (без альфа)
-   3 подмножества на каждый блок
-   Конечные точки RGB 5.5.5
-   2-битовые индексы
-   64 раздела

![структура битов режима 2](images/bc7-mode2.png)

### <a name="span-idmode-3spanspan-idmode-3spanspan-idmode-3spanmode-3"></a><span id="Mode-3"></span><span id="mode-3"></span><span id="MODE-3"></span>Режим 3

Режим BC7 3 имеет следующие характеристики:

-   Только компоненты цвета (без альфа)
-   2 подмножества на каждый блок
-   Конечные точки RGBP 7.7.7.1 с уникальным P-битом на подмножество)
-   2-битовые индексы
-   64 раздела

![структура битов режима 3](images/bc7-mode3.png)

### <a name="span-idmode-4spanspan-idmode-4spanspan-idmode-4spanmode-4"></a><span id="Mode-4"></span><span id="mode-4"></span><span id="MODE-4"></span>Режим 4

Режим BC7 4 имеет следующие характеристики:

-   Компоненты цвета с отдельным альфа-компонентом
-   1 подмножество на блок
-   Конечные точки цвета RGB 5.5.5
-   6-битовые конечные точки альфа
-   16 x 2-битовых индексов
-   16 x 3-битовых индексов
-   поворот 2-битового компонента
-   средство выбора 1-битового индекса (используются ли 2- или 3-битовые индексы)

![структура битов режима 4](images/bc7-mode4.png)

### <a name="span-idmode-5spanspan-idmode-5spanspan-idmode-5spanmode-5"></a><span id="Mode-5"></span><span id="mode-5"></span><span id="MODE-5"></span>Режим 5

Режим BC7 5 имеет следующие характеристики:

-   Компоненты цвета с отдельным альфа-компонентом
-   1 подмножество на блок
-   Конечные точки цвета RGB 7.7.7
-   6-битовые конечные точки альфа
-   16 x 2-битовые индексы цвета
-   16 x 2-битовые альфа-индексы
-   поворот 2-битового компонента

![структура битов режима 5](images/bc7-mode5.png)

### <a name="span-idmode-6spanspan-idmode-6spanspan-idmode-6spanmode-6"></a><span id="Mode-6"></span><span id="mode-6"></span><span id="MODE-6"></span>Режим 6

Режим BC7 6 имеет следующие характеристики:

-   Комбинированные компоненты цвета и альфа-компоненты
-   Одно подмножество на блок
-   Конечные точки цвета (и альфа) RGBAP 7.7.7.7.1 (уникальный P-бит на конечную точку)
-   16 x 4-битовых индексов

![структура битов режима 6](images/bc7-mode6.png)

### <a name="span-idmode-7spanspan-idmode-7spanspan-idmode-7spanmode-7"></a><span id="Mode-7"></span><span id="mode-7"></span><span id="MODE-7"></span>Режим7

Режим BC7 7 имеет следующие характеристики:

-   Комбинированные компоненты цвета и альфа-компоненты
-   2 подмножества на каждый блок
-   Конечные точки цвета (и альфа) RGBAP 5.5.5.5.1 (уникальный P-бит на конечную точку)
-   2-битовые индексы
-   64 раздела

![структура битов режима 7](images/bc7-mode7.png)

### <a name="span-idremarksspanspan-idremarksspanspan-idremarksspanremarks"></a><span id="Remarks"></span><span id="remarks"></span><span id="REMARKS"></span>Замечания

Режим 8 (наименее значимому байту присвоено значение 0x00) зарезервирован. Не используйте его в своем кодировщике. Если передать этот режим оборудованию, возвращается блок, инициализированный до всех нулей.

В BC7 можно зашифровать альфа-компонент одним из следующих способов:

-   Типы блоков без явной кодировки альфа-компонента. В этих блоках конечные точки цвета имеют только кодировку RGB, а альфа-компонент декодируется до 1.0 для всех текселей.
-   Типы блоков с объединенными компонентами цвета и альфа-компонентами. В этих блоках значения цвета конечной точки задаются в формате RGBA, а значения альфа-компонента интерполируются вместе со значениями цвета.
-   Типы блоков с отдельными компонентами цвета и альфа-компонентами. В этих блоках значения цвета и альфа задаются по отдельности— каждый со своим собственным набором индексов. В результате эффективный вектор и скалярный канал кодируются по отдельности, и вектор, как правило, задает каналы цвета \[R, G, B\], а скалярный канал задает альфа-канал \[A\]. В поддержку такого подхода в кодировке предоставляется отдельное 2-битовое поле, которое позволяет задать отдельную кодировку канала в виде скалярного значения. В результате блок может иметь одно из следующих четырех представлений кодировки альфа (как указано в 2-битовом поле):
    -   RGB|A: альфа-канал по отдельности
    -   AGB|R: "красный" канал цвета по отдельности
    -   RAB|G: "зеленый" канал цвета по отдельности
    -   RGA|B: "синий" канал цвета по отдельности

    После декодирования декодер снова упорядочивает каналы в порядке RGBA, поэтому внутренний формат блоков невидим разработчику. Блоки с отдельными компонентами цвета и альфа-компонентами также имеют два набора данных индекса: один— для векторного набора каналов, другой— для скалярного канала. (В случае режима 4 эти индексы имеют разную длину \[2 или 3 бита\]. Режим 4 также содержит 1-битовое средство выбора, указывающее, использует ли векторный или скалярный канал 3-битовые индексы.)

## <a name="span-idrelated-topicsspanrelated-topics"></a><span id="related-topics"></span>Статьи по теме


[Сжатие блоков текстур](texture-block-compression.md)

 

 





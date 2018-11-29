---
title: Уровень 1
description: В этом разделе описывается поддержка 1-го уровня.
ms.assetid: 153DA429-0C99-4691-AEB4-124FD9B17BE2
keywords:
- Уровень 1
ms.date: 02/08/2017
ms.topic: article
ms.localizationpriority: medium
ms.openlocfilehash: 0ae76111f6feefa0bb63fd18516e033050cc06fc
ms.sourcegitcommit: b5c9c18e70625ab770946b8243f3465ee1013184
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/28/2018
ms.locfileid: "7980292"
---
# <a name="tier-1"></a>Уровень 1


В этом разделе описывается поддержка 1-го уровня.

## <a name="span-idtier1generallimitationsspanspan-idtier1generallimitationsspanspan-idtier1generallimitationsspantier-1-general-limitations"></a><span id="Tier_1_general_limitations"></span><span id="tier_1_general_limitations"></span><span id="TIER_1_GENERAL_LIMITATIONS"></span>Общие ограничения уровня 1


-   Оборудование с уровнем компонентов не менее 11.0.
-   Отсутствует поддержка сшивания.
-   Отсутствует поддержка Texture1D и Texture3D.
-   Отсутствует поддержка сглаживания с множественной дискретизацией (MSAA) с 2, 8 и 16 выборками Требуется только сглаживание с 4 выборками, за исключением форматов 128 бит/пкс.
-   Отсутствует стандартный шаблон выборки (макет для плиток размером 64 КБ и упаковка хвостовых MIP-карт зависит от поставщика оборудования).
-   Ограничения на способы доступа к плиткам с повторяющимися сопоставлениями. См. раздел [Ограничения доступа к плиткам с повторяющимися сопоставлениями](tile-access-limitations-with-duplicate-mappings.md)

## <a name="span-idspecificlimitationsaffectingtier1onlyspanspan-idspecificlimitationsaffectingtier1onlyspanspan-idspecificlimitationsaffectingtier1onlyspanspecific-limitations-affecting-tier-1-only"></a><span id="Specific_limitations_affecting_tier_1_only"></span><span id="specific_limitations_affecting_tier_1_only"></span><span id="SPECIFIC_LIMITATIONS_AFFECTING_TIER_1_ONLY"></span>Особые ограничения, влияющие только на уровень 1


### <a name="span-idreadingwritingtostreamingresourcesthathavenullmappingsspanspan-idreadingwritingtostreamingresourcesthathavenullmappingsspanspan-idreadingwritingtostreamingresourcesthathavenullmappingsspanreadingwriting-to-streaming-resources-that-have-null-mappings"></a><span id="Reading_writing_to_streaming_resources_that_have_NULL_mappings"></span><span id="reading_writing_to_streaming_resources_that_have_null_mappings"></span><span id="READING_WRITING_TO_STREAMING_RESOURCES_THAT_HAVE_NULL_MAPPINGS"></span>Чтение и запись потоковых ресурсов с нулевыми сопоставлениями

Потоковые ресурсы могут иметь сопоставления **NULL**, но чтение из них или запись в них приводит к неопределенным результатам, вплоть до отключения устройства. Приложения могут обойти это путем сопоставления одной фиктивной страницы всем пустым областям. Необходимо с осторожностью производить запись и отрисовку страницы, которая сопоставлена нескольким расположениям целевого объекта отрисовки, так как порядок записи будет не определен.

### <a name="span-idnoshaderinstructionsforclampinglodandmappedstatusfeedbackspanspan-idnoshaderinstructionsforclampinglodandmappedstatusfeedbackspanspan-idnoshaderinstructionsforclampinglodandmappedstatusfeedbackspanno-shader-instructions-for-clamping-lod-and-mapped-status-feedback"></a><span id="No_shader_instructions_for_clamping_LOD_and_mapped_status_feedback"></span><span id="no_shader_instructions_for_clamping_lod_and_mapped_status_feedback"></span><span id="NO_SHADER_INSTRUCTIONS_FOR_CLAMPING_LOD_AND_MAPPED_STATUS_FEEDBACK"></span>Отсутствуют инструкции для закрепления уровня детализации и получения состояния сопоставления

Инструкции шейдеров для закрепления уровня детализации и получения состояния сопоставления недоступны. См. раздел [Экспозиция потоковых ресурсов HLSL](hlsl-streaming-resources-exposure.md).

### <a name="span-idalignmentconstraintsforstandardtileshapesspanspan-idalignmentconstraintsforstandardtileshapesspanspan-idalignmentconstraintsforstandardtileshapesspanalignment-constraints-for-standard-tile-shapes"></a><span id="Alignment_constraints_for_standard_tile_shapes"></span><span id="alignment_constraints_for_standard_tile_shapes"></span><span id="ALIGNMENT_CONSTRAINTS_FOR_STANDARD_TILE_SHAPES"></span>Ограничения выравнивания для стандартных форм плиток

Гарантируется лишь то, что MIP-карты (начиная с самых мелких), размер которых составляет множитель стандартного размера плитки, поддерживают стандартные формы плиток и могут сопоставляться с отдельными плитками произвольным образом. Первая MIP-карта в потоковом ресурсе, размеры которой не представляют собой множитель стандартного размера плитки, а также более крупные MIP-карты могут иметь нестандартную форму размещения на плитках, помещаясь на плитках N размером 64 КБ для всего набора MIP-карт одновременно (N сообщается приложению). Эти плитки N считаются упакованными одним блоком, который может быть полностью сопоставлен или полностью не сопоставлен приложением в любой момент времени. При этом сопоставления отдельных плиток N могут располагаться в различных произвольных местах пула плиток.

### <a name="span-idarrayofmipmapsthatarentamultipleofstandardtilesizespanspan-idarrayofmipmapsthatarentamultipleofstandardtilesizespanspan-idarrayofmipmapsthatarentamultipleofstandardtilesizespanarray-of-mipmaps-that-arent-a-multiple-of-standard-tile-size"></a><span id="Array_of_mipmaps_that_aren_t_a_multiple_of_standard_tile_size"></span><span id="array_of_mipmaps_that_aren_t_a_multiple_of_standard_tile_size"></span><span id="ARRAY_OF_MIPMAPS_THAT_AREN_T_A_MULTIPLE_OF_STANDARD_TILE_SIZE"></span>Массив MIP-карт, размер которого не кратен стандартному размеру плитки

Потоковые ресурсы с MIP-картами, размер которых не является кратным стандартному размеру плитки по всем измерениям, не могут иметь размер массива больше 1.

### <a name="span-idswitchingbetweenreferencingtilesinatilepoolviaabufferandtextureresourcespanspan-idswitchingbetweenreferencingtilesinatilepoolviaabufferandtextureresourcespanspan-idswitchingbetweenreferencingtilesinatilepoolviaabufferandtextureresourcespanswitching-between-referencing-tiles-in-a-tile-pool-via-a-buffer-and-texture-resource"></a><span id="Switching_between_referencing_tiles_in_a_tile_pool_via_a_Buffer_and_Texture_resource"></span><span id="switching_between_referencing_tiles_in_a_tile_pool_via_a_buffer_and_texture_resource"></span><span id="SWITCHING_BETWEEN_REFERENCING_TILES_IN_A_TILE_POOL_VIA_A_BUFFER_AND_TEXTURE_RESOURCE"></span>Переключение между ссылками на плитки в пуле плиток через ресурс буфера и ресурс текстуры

Чтобы переключиться со ссылок на плитки в пуле плиток через ресурс [Buffer](introduction-to-buffers.md) на ссылки на те же плитки через ресурс [Texture](introduction-to-textures.md) и наоборот, последнее обновление сопоставлений плитки или копирование сопоставлений плитки, определяющих сопоставления для этих плиток в пуле должно осуществляться для того же измерения ресурса (Buffer или Texture\*), что и измерение ресурса, которое будет использоваться для доступа к плиткам. В противном случае поведение не определено, включая вероятность сброса устройства.

Так, например, недопустимо обновлять сопоставления плиток, чтобы определить сопоставления плиток для буфера, а затем обновлять сопоставления тех же плиток в пуле плиток через ресурс [**Texture2D**](https://msdn.microsoft.com/library/windows/desktop/ff471525), после чего осуществлять доступ к плиткам через буфер. Обходные операции должны либо переопределить сопоставления плиток для ресурса при переключении между буфером и текстурой (или наоборот) или просто никогда не использовать совместно в одном пуле плитки буфера ресурсов и буфера текстур.

### <a name="span-idminmaxreductionfilteringspanspan-idminmaxreductionfilteringspanspan-idminmaxreductionfilteringspanminmax-reduction-filtering"></a><span id="Min_Max_reduction_filtering"></span><span id="min_max_reduction_filtering"></span><span id="MIN_MAX_REDUCTION_FILTERING"></span>Фильтрация сокращения минимумов и максимумов

Фильтрация сокращения минимумов/максимумов не поддерживается. См. раздел [Функции дискретизации текстур для потоковых ресурсов](streaming-resources-texture-sampling-features.md)

## <a name="span-idrelated-topicsspanrelated-topics"></a><span id="related-topics"></span>Статьи по теме


[Уровни функций для потоковых ресурсов](streaming-resources-features-tiers.md)

 

 





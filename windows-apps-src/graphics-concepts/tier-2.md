---
title: Уровень 2
description: Поддержка второго уровня потоковых ресурсов добавляет новые возможности по сравнению с первым уровнем, например обеспечивает не упакованные MIP-карты текстур при размере, превышающем размер одной стандартной формы плитки; инструкции шейдеров для закрепления уровня детализации (LOD) и получения состояния операции шейдера; а также обработка значения, выбранного при чтении из плиток с сопоставлением NULL, в качестве нуля.
ms.assetid: 111A28EA-661A-4D29-921A-F2E376A46DC5
keywords:
- Уровень 2
author: michaelfromredmond
ms.author: mithom
ms.date: 02/08/2017
ms.topic: article
ms.localizationpriority: medium
ms.openlocfilehash: fac8780995231ce56d1264ea8a1a5cb52fd9a3d0
ms.sourcegitcommit: e38b334edb82bf2b1474ba686990f4299b8f59c7
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/15/2018
ms.locfileid: "6841105"
---
# <a name="tier-2"></a>Уровень 2


Поддержка второго уровня потоковых ресурсов добавляет новые возможности по сравнению с первым уровнем, например обеспечивает не упакованные MIP-карты текстур при размере, превышающем размер одной стандартной формы плитки; инструкции шейдеров для закрепления уровня детализации (LOD) и получения состояния операции шейдера; а также обработка значения, выбранного при чтении из плиток с сопоставлением NULL, в качестве нуля.

## <a name="span-idtier2generalsupportspanspan-idtier2generalsupportspanspan-idtier2generalsupportspantier-2-general-support"></a><span id="Tier_2_general_support"></span><span id="tier_2_general_support"></span><span id="TIER_2_GENERAL_SUPPORT"></span>Общая поддержка второго уровня


Поддержка второго уровня включает следующие возможности.

-   Оборудование с уровнем компонентов не менее 11.1.
-   Все возможности предыдущего уровня (без особых ограничений [уровня 1](tier-1.md)), а также добавления в следующих элементах.
-   Доступны инструкции шейдеров для закрепления уровня детализации и получения состояния сопоставления. См. раздел [Экспозиция потоковых ресурсов HLSL](hlsl-streaming-resources-exposure.md).

В дополнение к вышеуказанным присутствуют некоторые особенности поддержки, представленные ниже.

## <a name="span-idnon-mappedtilesspanspan-idnon-mappedtilesspanspan-idnon-mappedtilesspannon-mapped-tiles"></a><span id="Non-mapped_tiles"></span><span id="non-mapped_tiles"></span><span id="NON-MAPPED_TILES"></span>Не сопоставленные плитки


При чтении из не сопоставленных плиток возвращается 0 во все присутствующие компоненты формата и значение по умолчанию для отсутствующих компонентов.

Операции записи в не сопоставленные плитки не сохраняются в памяти, но могут оказаться в кэшах, которые будут или не будут задействованы при последующих операциях чтения.

## <a name="span-idtexturefilteringspanspan-idtexturefilteringspanspan-idtexturefilteringspantexture-filtering"></a><span id="Texture_filtering"></span><span id="texture_filtering"></span><span id="TEXTURE_FILTERING"></span>Фильтрация текстур


Фильтрация текстур, затрагивающая одновременно плитки с сопоставлением **NULL** и отличным от **NULL** вносит 0 (и значения по умолчанию для отсутствующих компонентов формата) для текселей на плитках **NULL** в общей операции фильтрации. Некоторое раннее оборудование не соответствует этому требованию и возвращает значение 0 (и значение по умолчанию для отсутствующих компонентов формата) для всего результата фильтрации если любые тексели (с ненулевым весом) попадают на плитку **NULL**. Другому оборудованию запрещено не выполнять требование включать все тексели с ненулевым весом в операцию фильтрации.

Обращение к текселям **NULL** приводит к тому, что операция [**CheckAccessFullyMapped**](https://msdn.microsoft.com/library/windows/desktop/dn292083) при чтении информации о состоянии текстуры возвращает значение false. Это происходит независимо от того, как результат доступа к текстуре маскируется в шейдере и сколько компонентов включено в формат текстуры (сочетание этих факторов может привести к тому, что в доступе к текстуре нет необходимости).

## <a name="span-idalignmentconstraintsspanspan-idalignmentconstraintsspanspan-idalignmentconstraintsspanalignment-constraints"></a><span id="Alignment_constraints"></span><span id="alignment_constraints"></span><span id="ALIGNMENT_CONSTRAINTS"></span>Ограничения выравнивания


Ограничения выравнивания для стандартных форм плиток: MIP-карты, которые заполняют по крайней мере одну стандартную плитку во всех измерениях, гарантировано используют стандартное размещение на плитках, а их оставшаяся часть считается упакованной как **unit** в плитки N (N сообщаются приложению). Приложение может сопоставлять плитки N с произвольными несвязанными местами в пуле плиток, но должно сопоставить или все или ни одной из упакованных плиток. Упаковка MIP-карт — это уникальный набор упакованных плиток для каждого фрагмента массива.

## <a name="span-idminmaxreductionfilteringspanspan-idminmaxreductionfilteringspanspan-idminmaxreductionfilteringspanminmax-reduction-filtering"></a><span id="Min_Max_reduction_filtering"></span><span id="min_max_reduction_filtering"></span><span id="MIN_MAX_REDUCTION_FILTERING"></span>Фильтрация сокращения минимумов и максимумов


Фильтрация сокращения минимумов/максимумов поддерживается. См. раздел [Функции дискретизации текстур для потоковых ресурсов](streaming-resources-texture-sampling-features.md)

## <a name="span-idlimitationsspanspan-idlimitationsspanspan-idlimitationsspanlimitations"></a><span id="Limitations"></span><span id="limitations"></span><span id="LIMITATIONS"></span>Ограничения


Потоковые ресурсы с MIP-картами размера меньше стандартной плитки в любом измерении не могут иметь размер массива больше 1.

Ограничения на способ доступа к плиткам продолжают применяться при наличии повторяющихся сопоставлений. См. раздел [Ограничения доступа к плиткам с повторяющимися сопоставлениями](tile-access-limitations-with-duplicate-mappings.md)

## <a name="span-idrelated-topicsspanrelated-topics"></a><span id="related-topics"></span>Статьи по теме


[Уровни функций для потоковых ресурсов](streaming-resources-features-tiers.md)

 

 





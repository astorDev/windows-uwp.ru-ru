---
title: Потребность в потоковых ресурсах
description: Потоковые ресурсы нужны для того, чтобы память GPU не расходовалась на хранение областей поверхностей, к которым не будет осуществляться доступ, а также затем, чтобы указать аппаратному обеспечению как вести фильтрацию по близко расположенным плиткам.
ms.assetid: A88BE65B-104F-4176-9809-C12580A3684C
keywords:
- Потребность в потоковых ресурсах
ms.date: 02/08/2017
ms.topic: article
ms.localizationpriority: medium
ms.openlocfilehash: f5b44e60e3490f39a91724bf038aa8066de11bf0
ms.sourcegitcommit: ac7f3422f8d83618f9b6b5615a37f8e5c115b3c4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/29/2019
ms.locfileid: "66370892"
---
# <a name="the-need-for-streaming-resources"></a>Потребность в потоковых ресурсах


Потоковые ресурсы нужны для того, чтобы память GPU не расходовалась на хранение областей поверхностей, к которым не будет осуществляться доступ, а также затем, чтобы указать аппаратному обеспечению как вести фильтрацию по близко расположенным плиткам.

## <a name="span-idstreamingresourcesorsparsetexturesspanspan-idstreamingresourcesorsparsetexturesspanspan-idstreamingresourcesorsparsetexturesspanstreaming-resources-or-sparse-textures"></a><span id="Streaming_resources_or_sparse_textures"></span><span id="streaming_resources_or_sparse_textures"></span><span id="STREAMING_RESOURCES_OR_SPARSE_TEXTURES"></span>Потоковая передача ресурсов или разреженных текстуры


*Потоковые ресурсы* (называемые *ресурсы плиток* в Direct3D 11) — это крупные логические ресурсы, которые используют небольшое количество физической памяти.

Другое название потоковых ресурсов — *делимые текстуры*. "Делимые" означает одновременно мозаичный характер ресурсов, а также, возможно, основную причину разбиения их на плитки — не все из них должны сопоставляться одновременно. На самом деле приложение вполне может создать потоковый ресурс, в котором намеренно не созданы данные для всех регионов и MIP. Таким образом само содержимое может быть разреженным, и сопоставление содержимого в памяти графического процессора (GPU) в определенный момент будет представлять собой подмножество (еще более разреженное).

## <a name="span-idwithouttilingmemoryallocationsaremanagedatsubresourcegranularityspanspan-idwithouttilingmemoryallocationsaremanagedatsubresourcegranularityspanspan-idwithouttilingmemoryallocationsaremanagedatsubresourcegranularityspanwithout-tiling-memory-allocations-are-managed-at-subresource-granularity"></a><span id="Without_tiling__memory_allocations_are_managed_at_subresource_granularity"></span><span id="without_tiling__memory_allocations_are_managed_at_subresource_granularity"></span><span id="WITHOUT_TILING__MEMORY_ALLOCATIONS_ARE_MANAGED_AT_SUBRESOURCE_GRANULARITY"></span>Без заполнения, выделение памяти контролируется на уровне детализации subresource


В графической системе (представляющей собой операционную систему, видеодрайвер и графическое оборудование), которая не поддерживает потоковые ресурсы, сама система управляет выделением памяти Direct3D на уровне вложенных ресурсов.

Для [буфера](introduction-to-buffers.md) весь буфер является вложенным ресурсом.

Для [текстуры](textures.md), (например, [**Texture2D**](https://docs.microsoft.com/windows/desktop/direct3dhlsl/sm5-object-texture2d)), каждый уровень MIP является вложенным ресурсом; для массива текстур (например, [**Texture2DArray**](https://docs.microsoft.com/windows/desktop/direct3dhlsl/sm5-object-texture2darray)) каждый уровень MIP определенного фрагмента массива является вложенным ресурсом. Графическая система лишь предоставляет возможность управлять сопоставлениями выделения памяти на этом уровне вложенных ресурсов. В контексте потоковых ресурсов под «сопоставлением» понимается обеспечение доступности данных для графического процессора.

## <a name="span-idwithouttilingcantaccessonlyasmallportionofmipmapchainspanspan-idwithouttilingcantaccessonlyasmallportionofmipmapchainspanspan-idwithouttilingcantaccessonlyasmallportionofmipmapchainspanwithout-tiling-cant-access-only-a-small-portion-of-mipmap-chain"></a><span id="Without_tiling__can_t_access_only_a_small_portion_of_mipmap_chain"></span><span id="without_tiling__can_t_access_only_a_small_portion_of_mipmap_chain"></span><span id="WITHOUT_TILING__CAN_T_ACCESS_ONLY_A_SMALL_PORTION_OF_MIPMAP_CHAIN"></span>Без заполнения, невозможно получить доступ к только небольшую часть цепочку MIP-карт


Предположим, что приложение знает, что определенной операции отрисовки необходим доступ лишь к небольшой части цепочки MIP-карт (возможно, даже не к полной области определенной MIP-карты). В идеале приложение должно сообщить графической системе об этой потребности. Тогда графическая система обеспечит сопоставление лишь необходимого количества памяти графического процессора, не задействуя лишнюю память.

На самом деле без поддержки потоковых ресурсов графической системе можно сообщить лишь о памяти, которую графическому процессору необходимо сопоставить на уровне вложенных ресурсов (например, диапазона полных уровней MIP-карт, к которым может осуществляться доступ). В графической системе также невозможен отказ от требования выделения памяти, поэтому возможно использование излишне большого количества памяти графического процессора для полного сопоставления всех вложенных ресурсов перед выполнением команды отрисовки, ссылающейся на какую-либо часть памяти. Это только одна проблема, которая усложняет использование большого количества выделенной памяти в Direct3D без поддержки потоковых ресурсов.

## <a name="span-idsoftwarepagingtobreakthesurfaceintosmallertilesspanspan-idsoftwarepagingtobreakthesurfaceintosmallertilesspanspan-idsoftwarepagingtobreakthesurfaceintosmallertilesspansoftware-paging-to-break-the-surface-into-smaller-tiles"></a><span id="Software_paging_to_break_the_surface_into_smaller_tiles"></span><span id="software_paging_to_break_the_surface_into_smaller_tiles"></span><span id="SOFTWARE_PAGING_TO_BREAK_THE_SURFACE_INTO_SMALLER_TILES"></span>Разбиение по страницам Software переключиться в область меньшего размера плитки


Программная подкачка может использоваться для разбиения поверхности на плитки, размер которых достаточно мал для обработки оборудованием.

Direct3D поддерживает поверхности [**Texture2D**](https://docs.microsoft.com/windows/desktop/direct3dhlsl/sm5-object-texture2d) размером до 16 384 пикселей по заданной стороне. Изображение, размером 16 384 пкс в ширину, 16 384 в высоту и 4 байтами на пиксель потребляет 1 ГБ видеопамяти (и добавление MIP-карт удваивает это значение). На практике редко возникает необходимость использовать в одной операции отрисовки весь 1 ГБ памяти.

Некоторые разработчики игр создают модели поверхностей ландшафтов размером плоть до 128 на 128 тыс. пикселей. На существующих графических процессорах это становится возможным благодаря разбиению поверхности на плитки, которые достаточно малы для обработки оборудованием. Приложение должно определить, какие плитки могут потребоваться, и загрузить их в кэш текстур графического процессора — систему программной подкачки.

Значительные недостаток этого подхода поступает из оборудования, не зная ничего о разбиении на страницы, который происходит: Когда части изображения должен отображаться на экране, который служит связующим звеном плитки, оборудование не знает, как для выполнения основных функции (т. е эффективный) фильтрация во всех мозаиках. Это означает, что приложение, управляющее собственным программным разбиением на плитки, вынуждено использовать ручную фильтрацию текстур в коде шейдера (что требует очень большого количества ресурсов, если требуется высококачественная анизотропная фильтрация) и (или) тратить память на создание вокруг плиток полей, которые содержат данные из соседних плиток, чтобы аппаратная фильтрация с фиксированной функцией могла продолжать оказывать некоторую помощь.

## <a name="span-idmakingtiledrepresentationofsurfaceallocationsafirst-classfeaturespanspan-idmakingtiledrepresentationofsurfaceallocationsafirst-classfeaturespanspan-idmakingtiledrepresentationofsurfaceallocationsafirst-classfeaturespanmaking-tiled-representation-of-surface-allocations-a-first-class-feature"></a><span id="Making_tiled_representation_of_surface_allocations_a_first-class_feature"></span><span id="making_tiled_representation_of_surface_allocations_a_first-class_feature"></span><span id="MAKING_TILED_REPRESENTATION_OF_SURFACE_ALLOCATIONS_A_FIRST-CLASS_FEATURE"></span>Создание мозаичное представление поверхности выделений первостепенного компонента


Если плиточное представление выделения памяти для поверхностей является первоклассной возможностью графической системы, приложение может сообщить оборудованию, какие плитки необходимо сделать доступными. Таким образом меньшее количество памяти графического процессора тратится на хранение областей поверхности, доступ к которым, как известно приложению, осуществляться не будет. Оборудованию при этом известно, как производить фильтрацию соседних плиток, чтобы разрешить часть проблем, с которыми сталкиваются разработчики, выполняющие программное разделение на плитки самостоятельно.

Однако для того, чтобы решение было полнофункциональным, необходимо каким-то образом решить проблему того, что вне зависимости от наличия поддержки разбиения поверхности на плитки максимальные размеры поверхности в данный момент составляют 16 384 пикселей, что значительно меньше 128 тысяч, которые уже требуются приложениям. Одним из подходов может быть просто требование поддержки текстур большего размера оборудованием, однако такой подход связан с значительными расходами и компромиссами.

Пути фильтрации текстур и отрисовки Direct3D уже насыщены в плане точности ввиду поддержки текстур размером 16 тысяч пикселей и других требований, таких как поддержка размеров окна просмотра, выходящих за пределы поверхности в время отрисовки, или поддержка обтекания текстурой края поверхности во время фильтрации. Существует возможность найти компромисс так, чтобы по мере увеличения размера текстуры за пределы 16 тысяч пикселей определенным образом снижались функциональные возможности или точность. Однако даже при этой уступке могут потребоваться дополнительные затраты на оборудование в плане способности всей аппаратной системы к переходу к текстурам увеличенного размера.

## <a name="span-idissuewithlargetexturesprecisionforlocationsonsurfacespanspan-idissuewithlargetexturesprecisionforlocationsonsurfacespanspan-idissuewithlargetexturesprecisionforlocationsonsurfacespanissue-with-large-textures-precision-for-locations-on-surface"></a><span id="Issue_with_large_textures__precision_for_locations_on_surface"></span><span id="issue_with_large_textures__precision_for_locations_on_surface"></span><span id="ISSUE_WITH_LARGE_TEXTURES__PRECISION_FOR_LOCATIONS_ON_SURFACE"></span>Проблема с большой текстуры: точность для расположений в рабочей области


Со значительным увеличением размера текстур возникает проблема, состоящая в том, что координаты текстуры одинарной точности с плавающей запятой (и связанные интерполяторы для поддержки растеризации) не располагают достаточной точностью, чтобы безошибочно указывать расположение на поверхности. Это приводит к дрожанию при фильтрации текстур. Затратным решением является требование поддержки интерполятора двойной точности, но при наличии разумной альтернативы оно представляется избыточным.

## <a name="span-idenablingmultipleresourcesofdifferentdimensionstosharememoryspanspan-idenablingmultipleresourcesofdifferentdimensionstosharememoryspanspan-idenablingmultipleresourcesofdifferentdimensionstosharememoryspanenabling-multiple-resources-of-different-dimensions-to-share-memory"></a><span id="Enabling_multiple_resources_of_different_dimensions_to_share_memory"></span><span id="enabling_multiple_resources_of_different_dimensions_to_share_memory"></span><span id="ENABLING_MULTIPLE_RESOURCES_OF_DIFFERENT_DIMENSIONS_TO_SHARE_MEMORY"></span>Включение нескольких ресурсов из различных измерений для совместного использования памяти


Другим сценарием, который может использоваться благодаря потоковой передаче ресурсов, является включение совместного использования памяти несколькими ресурсами различных размеров или форматов. Иногда приложения имеют особые наборы ресурсов, которые не должны использоваться одновременно, или ресурсы, которые создаются только для очень краткого использования и затем удаляются, после чего создаются другие ресурсы. Общим местом "потоковых ресурсов" является возможность указывать на одну и ту же (перекрывающуюся) память нескольким различным ресурсам. Другими словами, создание и уничтожение "ресурсов" (которые определяют размер, формат и т. д.) может быть отделено от управления памятью, выделяемой для этих ресурсов, с точки зрения приложения.

## <a name="span-idrelated-topicsspanrelated-topics"></a><span id="related-topics"></span>Связанные разделы


[Потоковые ресурсы](streaming-resources.md)

 

 





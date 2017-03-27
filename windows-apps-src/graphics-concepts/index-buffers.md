---
title: "Буферы индексов"
description: "Буферы индексов — это буферы памяти, содержащие данные индексов, представляющие собой целочисленные указатели для буферов вершин, которые используются для отрисовки примитивов."
ms.assetid: 14D3DEC5-CF74-488B-BE41-16BF5E3201BE
keywords:
- "Буферы индексов"
author: PeterTurcan
ms.author: pettur
ms.date: 02/08/2017
ms.topic: article
ms.prod: windows
ms.technology: uwp
translationtype: Human Translation
ms.sourcegitcommit: c6b64cff1bbebc8ba69bc6e03d34b69f85e798fc
ms.openlocfilehash: 6aa62a7506b37314b1952a6687920a2cdf3deca3
ms.lasthandoff: 02/07/2017

---

# <a name="index-buffers"></a>Буферы индексов


*Буферы индексов* — это буферы памяти, содержащие данные индексов, представляющие собой целочисленные указатели для буферов вершин, которые используются для отрисовки примитивов.

Буферы индексов представляют собой буферы памяти, содержащие данные индексов. Данные индексов или индексы представляют собой целочисленные указатели для буферов вершин, которые используются для отрисовки примитивов.

Буфер вершин содержит вершины; соответственно, можно рисовать буферы вершин с помощью индексированных примитивов и без них. Однако, поскольку буфер индексов содержит индексы, невозможно использовать буфер индексов без соответствующего буфера вершин.

## <a name="span-idindexbufferdescriptionspanspan-idindexbufferdescriptionspanspan-idindexbufferdescriptionspanindex-buffer-description"></a><span id="Index_Buffer_Description"></span><span id="index_buffer_description"></span><span id="INDEX_BUFFER_DESCRIPTION"></span>Описание буфера индексов


Буфер индексов описывается с рассмотрением его возможностей: где он находится в памяти, поддерживает ли он чтение и запись, тип и число индексов, которые он может содержать.

Описания буферов индексов сообщают приложению, как был создан существующий буфер. Вы предоставляете пустую конструкцию для описания, которую система заполняет перечислением возможностей ранее созданного буфера индексов.

## <a name="span-idindexprocessingrequirementsspanspan-idindexprocessingrequirementsspanspan-idindexprocessingrequirementsspanindex-processing-requirements"></a><span id="Index_Processing_Requirements"></span><span id="index_processing_requirements"></span><span id="INDEX_PROCESSING_REQUIREMENTS"></span>Требования к обработке индексов


Производительность операций обработки индексов сильно зависит от расположения буфера индексов в памяти и от типа используемого устройства отрисовки. Приложения управляют выделением памяти для буферов индексов при их создании.

Приложение может записывать индексы непосредственно в буфер индексов, выделенный в оптимальном для драйвера участке памяти. Этот метод предотвращает избыточную операцию копирования на более позднем этапе. Этот прием оказывается менее полезен, если приложение снова считывает данные из буфера индексов, поскольку операции чтения, выполняемые хостом из оптимального для драйвера участка памяти, могут проходить очень медленно. Таким образом, если приложению необходимо выполнять чтение данных во время обработки или выполнять беспорядочную запись данных в буфер, размещение буфера индексов в памяти системы может стать оптимальным вариантом.

## <a name="span-idrelated-topicsspanrelated-topics"></a><span id="related-topics"></span>Статьи по теме


[Буферы вершин и индексов](vertex-and-index-buffers.md)

 

 





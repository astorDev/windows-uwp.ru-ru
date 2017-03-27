---
title: "Изменение размера пула плиток"
description: "Увеличьте размер пула плиток, если приложению требуется более широкий рабочий набор для сопоставленных потоковых ресурсов, или уменьшите его, если требуется меньше места."
ms.assetid: A54A06DC-BDDB-42DC-85E8-C64241100ED5
keywords:
- "Изменение размера пула плиток"
author: PeterTurcan
ms.author: pettur
ms.date: 02/08/2017
ms.topic: article
ms.prod: windows
ms.technology: uwp
translationtype: Human Translation
ms.sourcegitcommit: c6b64cff1bbebc8ba69bc6e03d34b69f85e798fc
ms.openlocfilehash: 918b9e7ada2d6c368bb51ff42139504e41c01f15
ms.lasthandoff: 02/07/2017

---

# <a name="tile-pool-resizing"></a>Изменение размера пула плиток


Увеличьте размер пула плиток, если приложению требуется более широкий рабочий набор для сопоставленных потоковых ресурсов, или уменьшите его, если требуется меньше места. Другой вариант для приложений — выделение дополнительных пулов плиток для новых потоковых ресурсов. Однако если одному из потоковых ресурсов требуется больше места, чем изначально доступно в его пуле плиток, увеличение пула плиток является хорошим решением. Потоковый ресурс не может быть сопоставлен с несколькими пулами плиток одновременно.

При увеличения пула плиток дополнительные плитки добавляются в конец драйвером дисплея посредством одного или нескольких выделений. Разбивка выделения памяти недоступна для приложения. Существующая память в пуле плиток не затрагивается, так же как и существующие сопоставления потоковых ресурсов с этой памятью.

При уменьшении пула плитки удаляются с конца. Плитки удаляются даже при размере ниже исходного, до 0. Это означает, что новые сопоставления не могут быть сделаны за пределами нового размера. При этом существующие сопоставления за пределами нового размера остаются без изменений и могут быть использованы. Драйвер дисплея сохраняет выделенную память до тех пор, пока существуют сопоставления с любой частью выделенной памяти, используемой драйвером в качестве памяти для пула плиток. Если после уменьшения часть памяти остается активной, поскольку на нее указывают сопоставления плиток, а затем пул плиток снова увеличивается (на любое значение), сначала повторно используется существующая память, а затем выделяется дополнительная память для обслуживания операции по увеличению.

Чтобы сэкономить память, приложение должно не только уменьшать пул плиток, но и удалять или преобразовывать существующие сопоставления за пределами нового уменьшенного размера пула плиток.

Процесс уменьшения (и удаления сопоставлений) не всегда непосредственно приводит к экономии памяти. Освобождение памяти зависит от того, насколько детально базовое распределение драйвером дисплея памяти для пула плиток. Если уменьшения пула достаточно, чтобы выделенная драйвером дисплея память перестала использоваться, драйвер может ее освободить. Если пул плиток увеличился, уменьшение его до предыдущего размера (и соответствующее удаление или переназначение сопоставлений плиток) с большей вероятностью приведет к экономии памяти, хотя и не гарантируется, если размеры в точности не соответствуют размерам базового распределения, выбранным драйвером дисплея.

## <a name="span-idrelated-topicsspanrelated-topics"></a><span id="related-topics"></span>Статьи по теме


[Сопоставления в пуле плиток](mappings-are-into-a-tile-pool.md)

 

 





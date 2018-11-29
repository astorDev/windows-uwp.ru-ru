---
title: Представление буфер констант (CBV)
description: Буферы констант содержат данные констант шейдеров. Их роль в том, что данные сохраняются и остаются доступными любому шейдеру графического процессора до тех пор, пока данные не нужно будет менять.
ms.assetid: 99AEC6B0-A43B-4B61-8C3A-ECC8DE1B69A7
keywords:
- Представление буфер констант (CBV)
ms.date: 02/08/2017
ms.topic: article
ms.localizationpriority: medium
ms.openlocfilehash: 33e850ba16be7a8d2621f061015d39c8b334cab2
ms.sourcegitcommit: b5c9c18e70625ab770946b8243f3465ee1013184
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/28/2018
ms.locfileid: "7976604"
---
# <a name="constant-buffer-view-cbv"></a>Представление буфер констант (CBV)


Буферы констант содержат данные констант шейдеров. Их роль в том, что данные сохраняются и остаются доступными любому шейдеру графического процессора до тех пор, пока данные не нужно будет менять.

Как правило, в буфере констант хранятся матрицы мира, проекции и представления, которые остаются неизменными на протяжении отрисовки одного кадра.

Структура буфера констант должна соответствовать структуре HLSL (см. раздел [Правила упаковки для константных переменных](https://msdn.microsoft.com/library/windows/desktop/bb509632.aspx)).

## <a name="span-idrelated-topicsspanrelated-topics"></a><span id="related-topics"></span>Статьи по теме


[Представления](views.md)

 

 





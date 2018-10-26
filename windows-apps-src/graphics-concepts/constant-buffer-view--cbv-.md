---
title: Представление буфер констант (CBV)
description: Буферы констант содержат данные констант шейдеров. Их роль в том, что данные сохраняются и остаются доступными любому шейдеру графического процессора до тех пор, пока данные не нужно будет менять.
ms.assetid: 99AEC6B0-A43B-4B61-8C3A-ECC8DE1B69A7
keywords:
- Представление буфер констант (CBV)
author: michaelfromredmond
ms.author: mithom
ms.date: 02/08/2017
ms.topic: article
ms.localizationpriority: medium
ms.openlocfilehash: cca90c705c7bb4dd1c7e283a9c3ed267cd282b56
ms.sourcegitcommit: 6cc275f2151f78db40c11ace381ee2d35f0155f9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/26/2018
ms.locfileid: "5554456"
---
# <a name="constant-buffer-view-cbv"></a>Представление буфер констант (CBV)


Буферы констант содержат данные констант шейдеров. Их роль в том, что данные сохраняются и остаются доступными любому шейдеру графического процессора до тех пор, пока данные не нужно будет менять.

Как правило, в буфере констант хранятся матрицы мира, проекции и представления, которые остаются неизменными на протяжении отрисовки одного кадра.

Структура буфера констант должна соответствовать структуре HLSL (см. раздел [Правила упаковки для константных переменных](https://msdn.microsoft.com/library/windows/desktop/bb509632.aspx)).

## <a name="span-idrelated-topicsspanrelated-topics"></a><span id="related-topics"></span>Статьи по теме


[Представления](views.md)

 

 





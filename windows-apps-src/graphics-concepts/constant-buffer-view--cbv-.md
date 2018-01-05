---
title: "Представление буфер констант (CBV)"
description: "Буферы констант содержат постоянные данные шейдера. Их роль в том, что данные сохраняются и остаются доступными любому шейдеру графического процессора до тех пор, пока данные не нужно будет менять."
ms.assetid: 99AEC6B0-A43B-4B61-8C3A-ECC8DE1B69A7
keywords: "Представление буфер констант (CBV)"
author: michaelfromredmond
ms.author: mithom
ms.date: 02/08/2017
ms.topic: article
ms.prod: windows
ms.technology: uwp
ms.localizationpriority: medium
ms.openlocfilehash: 9e26a446bdd1e5a692e826d2c0ba303688bbd3d7
ms.sourcegitcommit: c80b9e6589a1ee29c5032a0b942e6a024c224ea7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/22/2017
---
# <a name="constant-buffer-view-cbv"></a>Представление буфер констант (CBV)


Буферы констант содержат постоянные данные шейдера. Их роль в том, что данные сохраняются и остаются доступными любому шейдеру графического процессора до тех пор, пока данные не нужно будет менять.

Как правило, в буфере констант хранятся матрицы мира, проекции и представления, которые остаются неизменными на протяжении отрисовки одного кадра.

Структура буфера констант должна соответствовать структуре HLSL (см. раздел [Правила упаковки для константных переменных](https://msdn.microsoft.com/library/windows/desktop/bb509632.aspx)).

## <a name="span-idrelated-topicsspanrelated-topics"></a><span id="related-topics"></span>Статьи по теме


[Представления](views.md)

 

 





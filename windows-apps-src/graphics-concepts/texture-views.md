---
title: Представления текстур
description: В Direct3D доступ к ресурсам текстур осуществляется через представление, которое представляет собой механизм аппаратной интерпретации ресурсов в памяти.
ms.assetid: 18DABFCE-8A36-4C4E-B08E-10428B05D701
keywords:
- Представления текстур
author: michaelfromredmond
ms.author: mithom
ms.date: 02/08/2017
ms.topic: article
ms.prod: windows
ms.technology: uwp
ms.localizationpriority: medium
ms.openlocfilehash: 78263272c0a150bf7ccf06aa29cc66f13770ad19
ms.sourcegitcommit: 897a111e8fc5d38d483800288ad01c523e924ef4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/13/2018
ms.locfileid: "1043633"
---
# <a name="texture-views"></a>Представления текстур


В Direct3D доступ к ресурсам текстур осуществляется через представление, которое представляет собой механизм аппаратной интерпретации ресурсов в памяти. Представление позволяет определенному циклу конвейера получать доступ только к необходимым ему [вложенным ресурсам](resource-types.md) в виде, требуемом для приложения.

Представление поддерживает понятие не типизированного ресурса. Не типизированный ресурс — это ресурс, созданные с указание определенного размера, но без указания типа данных. Данные интерпретируется динамически при привязке к конвейеру.

Ниже представлен пример привязки массива двухмерных текстур с шестью текстурами в качестве ресурса шейдера путем создания для него представления ресурса шейдера. Затем обращение к ресурсу происходит как к массиву текстур. (Примечание. Вложенный ресурс невозможно одновременно привязать к конвейеру в качестве ввода и вывода.)

![Иллюстрация массива текстур с шестью текстурами](images/d3d10-cube-texture-faces.png)

При использовании массива двухмерных текстур в качестве цели отрисовки ресурс можно рассматривать как массив двухмерных текстур (в этом примере их 6) с об уровнями MIP (в этом примере их 3).

Создайте объект представления для целевого объекта отрисовки, вызвав метод CreateRenderTargetView. Затем вызовите OMSetRenderTargets, чтобы установить представление однобуферной отрисовки для конвейера. Вызовите Draw для отрисовки на целевых объектах и используйте RenderTargetArrayIndex для указания правильной текстуры в массиве. Можно использовать вложенный ресурс (уровень MIP-карты, комбинацию индекса массива) для привязки к любому массиву подчиненных ресурсов. При необходимости можно выполнить привязку ко второму уровню MIP-карты и обновить только этот уровень, как показано на следующем рисунке.

![Иллюстрация привязки только ко второму уровню MIP-карты массива текстур](images/d3d10-cube-texture-faces-subresource.png)

## <a name="span-idrelated-topicsspanrelated-topics"></a><span id="related-topics"></span>Статьи по теме


[Ресурсы](resources.md)

 

 




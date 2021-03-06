---
title: Представления текстуры
description: В Direct3D доступ к ресурсам текстур осуществляется через представление, которое представляет собой механизм аппаратной интерпретации ресурсов в памяти.
ms.assetid: 18DABFCE-8A36-4C4E-B08E-10428B05D701
keywords:
- Представления текстуры
ms.date: 02/08/2017
ms.topic: article
ms.localizationpriority: medium
ms.openlocfilehash: e9167db4648dd193acaff0a224f3378486d171ad
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57638449"
---
# <a name="texture-views"></a>Представления текстуры


В Direct3D доступ к ресурсам текстур осуществляется через представление, которое представляет собой механизм аппаратной интерпретации ресурсов в памяти. Представление позволяет определенному циклу конвейера получать доступ только к необходимым ему [вложенным ресурсам](resource-types.md) в виде, требуемом для приложения.

Представление поддерживает понятие не типизированного ресурса. Не типизированный ресурс — это ресурс, созданные с указание определенного размера, но без указания типа данных. Данные интерпретируется динамически при привязке к конвейеру.

Ниже представлен пример привязки массива двухмерных текстур с шестью текстурами в качестве ресурса шейдера путем создания для него представления ресурса шейдера. Затем обращение к ресурсу происходит как к массиву текстур. (Примечание. Вложенный ресурс невозможно одновременно привязать к конвейеру в качестве ввода и вывода.)

![Иллюстрация массива текстур с шестью текстурами](images/d3d10-cube-texture-faces.png)

При использовании массива двухмерных текстур в качестве цели отрисовки ресурс можно рассматривать как массив двухмерных текстур (в этом примере их 6) с об уровнями MIP (в этом примере их 3).

Создайте объект представления для целевого объекта отрисовки, вызвав метод CreateRenderTargetView. Затем вызовите OMSetRenderTargets, чтобы установить представление однобуферной отрисовки для конвейера. Вызовите Draw для отрисовки на целевых объектах и используйте RenderTargetArrayIndex для указания правильной текстуры в массиве. Можно использовать вложенный ресурс (уровень MIP-карты, комбинацию индекса массива) для привязки к любому массиву подчиненных ресурсов. При необходимости можно выполнить привязку ко второму уровню MIP-карты и обновить только этот уровень, как показано на следующем рисунке.

![Иллюстрация привязки только ко второму уровню MIP-карты массива текстур](images/d3d10-cube-texture-faces-subresource.png)

## <a name="span-idrelated-topicsspanrelated-topics"></a><span id="related-topics"></span>Связанные разделы


[Ресурсы](resources.md)

 

 





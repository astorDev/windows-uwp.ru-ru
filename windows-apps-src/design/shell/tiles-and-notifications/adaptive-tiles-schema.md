---
author: andrewleader
Description: Here are the elements and attributes you use to create adaptive tiles.
title: Шаблоны адаптивных плиток — схема и документация
ms.assetid: 858FB05E-87A2-49CF-BE48-570980AD36C8
label: Adaptive tile schema and templates
template: detail.hbs
ms.author: mijacobs
ms.date: 05/19/2017
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: 1ed938ec247f308559566ad299bec8eb8ea75674
ms.sourcegitcommit: 70ab58b88d248de2332096b20dbd6a4643d137a4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/01/2018
ms.locfileid: "5926928"
---
# <a name="adaptive-tile-templates-schema-and-guidance"></a>Шаблоны адаптивных плиток — схема и руководство

 

Ниже приводится список элементов и атрибутов, используемых для создания этих плиток. Дополнительные сведения и примеры см. в разделе [Создание адаптивных плиток](create-adaptive-tiles.md).

## <a name="tile-element"></a>элемент плитки


``` xml
<tile>
  
  <!-- Child elements -->
  visual
  
</tile>
```

## <a name="visual-element"></a>визуальный элемент


``` xml
<visual
  version? = integer
  lang? = string
  baseUri? = anyURI
  branding? = "none" | "logo" | "name" | "nameAndLogo"
  addImageQuery? = boolean
  contentId? = string
  displayName? = string >
    
  <!-- Child elements -->
  binding+

</visual>
```

## <a name="binding-element"></a>элемент привязки


``` xml
<binding
  template = tileTemplateNameV3
  fallback? = tileTemplateNameV1
  lang? = string
  baseUri? = anyURI
  branding? = "none" | "logo" | "name" | "nameAndLogo"
  addImageQuery? = boolean
  contentId? = string
  displayName? = string
  hint-textStacking? = "top" | "center" | "bottom"
  hint-overlay? = [0-100] >

  <!-- Child elements -->
  ( image
  | text
  | group
  )*

</binding>
```

## <a name="image-element"></a>элемент изображения


``` xml
<image
  src = string
  placement? = "inline" | "background" | "peek"
  alt? = string
  addImageQuery? = boolean
  hint-crop? = "none" | "circle"
  hint-removeMargin? = boolean
  hint-align? = "stretch" | "left" | "center" | "right" />
```

## <a name="text-element"></a>текстовый элемент


``` xml
<text
  lang? = string
  hint-style? = textStyle
  hint-wrap? = boolean
  hint-maxLines? = integer
  hint-minLines? = integer
  hint-align? = "left" | "center" | "right" >

  <!-- text goes here -->

</text>
```

Значения textStyle: caption captionSubtle body bodySubtle base baseSubtle subtitle subtitleSubtle title titleSubtle titleNumeral subheader subheaderSubtle subheaderNumeral header headerSubtle headerNumeral

## <a name="group-element"></a>элемент группы


``` xml
<group>

  <!-- Child elements -->
  subgroup+

</group>
```

## <a name="subgroup-element"></a>элемент вложенной группы


``` xml
<subgroup
  hint-weight? = [0-100]
  hint-textStacking? = "top" | "center" | "bottom" >

  <!-- Child elements -->
  ( text
  | image
  )*

</subgroup>
```

## <a name="related-topics"></a>Связанные статьи


* [Создание адаптивных плиток](create-adaptive-tiles.md)
 

 





---
author: mijacobs
Description: Ниже приводится список элементов и атрибутов, используемых для создания этих плиток.
title: Шаблоны адаптивных плиток: схема и документация
ms.assetid: 858FB05E-87A2-49CF-BE48-570980AD36C8
label: Adaptive tile schema and templates
template: detail.hbs
---

# Шаблоны адаптивных плиток: схема и руководство

Ниже приводится список элементов и атрибутов, используемых для создания этих плиток. Дополнительные сведения и примеры см. в разделе [Создание адаптивных плиток](tiles-and-notifications-create-adaptive-tiles.md).

## <span id="tile_element"></span><span id="TILE_ELEMENT"></span>элемент плитки


``` syntax
<tile>
  
  <!-- Child elements -->
  visual
  
</tile>
```

## <span id="visual_element"></span><span id="VISUAL_ELEMENT"></span>визуальный элемент


``` syntax
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

## <span id="binding_element"></span><span id="BINDING_ELEMENT"></span>элемент привязки


``` syntax
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

## <span id="image_element"></span><span id="IMAGE_ELEMENT"></span>элемент изображения


``` syntax
<image
  src = string
  placement? = "inline" | "background" | "peek"
  alt? = string
  addImageQuery? = boolean
  hint-crop? = "none" | "circle"
  hint-removeMargin? = boolean
  hint-align? = "stretch" | "left" | "center" | "right" />
```

## <span id="text_element"></span><span id="TEXT_ELEMENT"></span>текстовый элемент


``` syntax
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

Значения textStyle: caption captionSubtle body bodySubtle base baseSubtle subtitle subtitleSubtle title titleSubtle titleNumeral subheader subheaderSubtle subheaderNumeral header headerSubtle headerNumber

## <span id="group_element"></span><span id="GROUP_ELEMENT"></span>элемент группы


``` syntax
<group>

  <!-- Child elements -->
  subgroup+

</group>
```

## <span id="subgroup_element"></span><span id="SUBGROUP_ELEMENT"></span>элемент вложенной группы


``` syntax
<subgroup
  hint-weight? = [0-100]
  hint-textStacking? = "top" | "center" | "bottom" >

  <!-- Child elements -->
  ( text
  | image
  )*

</subgroup>
```

## <span id="related_topics"></span>Ссылки по теме


* [Создание адаптивных плиток](tiles-and-notifications-create-adaptive-tiles.md)
 

 






<!--HONumber=May16_HO2-->



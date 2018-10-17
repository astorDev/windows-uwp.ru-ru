---
author: jwmsft
description: Связывает значение свойства в шаблоне элемента управления и значение какого-либо другого предоставленного свойства элемента управления-шаблона. TemplateBinding может использоваться только в пределах определения ControlTemplate в XAML.
title: Расширение разметки TemplateBinding
ms.assetid: FDE71086-9D42-4287-89ED-8FBFCDF169DC
ms.author: jimwalk
ms.date: 02/08/2017
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: 8c1812adc9d5610fffd6f9d275b4e093a4fa96e6
ms.sourcegitcommit: 9354909f9351b9635bee9bb2dc62db60d2d70107
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/16/2018
ms.locfileid: "4691752"
---
# <a name="templatebinding-markup-extension"></a>Расширение разметки {TemplateBinding}


Связывает значение свойства в шаблоне элемента управления и значение какого-либо другого предоставленного свойства элемента управления-шаблона. **TemplateBinding** может использоваться только в пределах определения [**ControlTemplate**](https://msdn.microsoft.com/library/windows/apps/br209391) в XAML.

## <a name="xaml-attribute-usage"></a>Использование атрибутов XAML

``` syntax
<object propertyName="{TemplateBinding sourceProperty}" .../>
```

## <a name="xaml-attribute-usage-for-setter-property-in-template-or-style"></a>Использование атрибута XAML (для свойства Setter в шаблоне или стиле)

``` syntax
<Setter Property="propertyName" Value="{TemplateBinding sourceProperty}" .../>
```

## <a name="xaml-values"></a>Значения XAML

| Термин | Описание |
|------|-------------|
| propertyName | Имя свойства, задаваемого в синтаксисе метода присваивания. Это свойство должно быть свойством зависимостей. |
| sourceProperty | Имя другого свойства зависимостей, которое существует в типе, по которому создается шаблон. |

## <a name="remarks"></a>Комментарии

Использование **TemplateBinding** — фундаментальная часть процесса определения шаблона элемента управления независимо от того, создаете ли вы пользовательский элемент управления или заменяете шаблон для уже существующих элементов управления. Подробнее см. в разделе [Краткое руководство: шаблоны элементов управления](https://msdn.microsoft.com/library/windows/apps/xaml/hh465374).

Использование одного и того же имени свойства — довольно частая практика для *propertyName* и *targetProperty*. В данном случае элемент управления может определить свойство для самого себя и перенаправить это свойство существующему и интуитивно названному свойству одного из своих компонентов. Например, элемент управления, включающий в свою компоновку объект [**TextBlock**](https://msdn.microsoft.com/library/windows/apps/br209652), используемый для отображения собственного свойства **Text** элемента управления, может содержать этот XAML в составе шаблона элемента управления: `<TextBlock Text="{TemplateBinding Text}" .... />`

Типы, используемые в качестве значений исходного и целевого свойств, должны совпадать. При использовании **TemplateBinding** невозможно добавить конвертер. Несовпадение значений приводит к ошибке при синтаксическом анализе XAML. Если вам требуется конвертер, можно использовать подробный синтаксис для привязки шаблона, например:  `{Binding RelativeSource={RelativeSource TemplatedParent}, Converter="..." ...}`

Попытка использования **TemplateBinding** вне определения [**ControlTemplate**](https://msdn.microsoft.com/library/windows/apps/br209391) в XAML вызовет ошибку средства синтаксического анализа.

В случаях, когда родительское значение для шаблона также откладывается в качестве другой привязки, вы можете использовать **TemplateBinding**. Вычисление **TemplateBinding** можно отложить до момента, когда у требуемых привязок времени выполнения появятся значения.

Привязка **TemplateBinding** всегда действует только в одном направлении. Оба участвующих свойства должны быть свойствами зависимостей.

**TemplateBinding** является расширением разметки. Расширения разметки обычно реализуются, если необходимо, чтобы значения атрибутов являлись буквенными значениями или именами обработчиков, и это требование является более глобальным, чем простая настройка преобразователей типов для определенных типов или свойств. Для всех расширений разметки в XAML в синтаксисе атрибутов используются символы "{" и "}". Это соответствует соглашению, по которому процессор XAML распознает, что расширение разметки должно обработать атрибут.

**Примечание**. В реализации процессора XAML в среде выполнения Windows отсутствует представление класса резервирования для функций **TemplateBinding**. Расширение **TemplateBinding** предназначено исключительно для использования в разметке XAML. Не существует простого способа для воспроизведения этого поведения в коде.

### <a name="xbind-in-controltemplate"></a>x: Bind в ControlTemplate

Начиная с следующем крупной обновлении до Windows 10, можно использовать расширение разметки **x: Bind** в любом использовалось **TemplateBinding** [**ControlTemplate**](https://msdn.microsoft.com/library/windows/apps/br209391). 

Свойство [TargetType](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.controltemplate.targettype#Windows_UI_Xaml_Controls_ControlTemplate_TargetType) , должны будут (обязательно) на [ControlTemplate](https://msdn.microsoft.com/library/windows/apps/br209391) при использовании **x: Bind**.

Благодаря поддержке **x: Bind** теперь вы можете использовать оба [привязки функций](../data-binding/function-bindings.md) как хорошо, как двусторонней привязки в [ControlTemplate](https://msdn.microsoft.com/library/windows/apps/br209391)

В следующем примере TextBlock.Text равен Button.Content.ToString(). TargetType на ControlTemplate выступает в качестве источника данных и выполняет тот же результат TemplateBinding родительскому элементу.

```xaml
<ControlTemplate TargetType="Button">
    <Grid>
        <TextBlock Text="{x:Bind Content}" />
    </Grid>
</ControlTemplate>
```

## <a name="related-topics"></a>Ссылки по теме

* [Краткое руководство: шаблоны элементов управления](https://msdn.microsoft.com/library/windows/apps/xaml/hh465374)
* [Подробно о привязке данных](https://msdn.microsoft.com/library/windows/apps/mt210946)
* [**ControlTemplate**](https://msdn.microsoft.com/library/windows/apps/br209391)
* [Обзор языка XAML](xaml-overview.md)
* [Общие сведения о свойствах зависимостей](dependency-properties-overview.md)
 


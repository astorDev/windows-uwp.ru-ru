---
author: jwmsft
description: Связывает значение свойства в шаблоне элемента управления и значение какого-либо другого предоставленного свойства элемента управления-шаблона. TemplateBinding может использоваться только в пределах определения ControlTemplate в XAML.
title: Расширение разметки TemplateBinding
ms.assetid: FDE71086-9D42-4287-89ED-8FBFCDF169DC
ms.author: jimwalk
ms.date: 10/29/2018
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: d4aaca880caf30b46cb1ed26d66700bb12d76404
ms.sourcegitcommit: 38f06f1714334273d865935d9afb80efffe97a17
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/09/2018
ms.locfileid: "6205756"
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

**Примечание**реализации процессора в XAML среды выполнения Windows нет отсутствует представление класса резервирования для **TemplateBinding**. Расширение **TemplateBinding** предназначено исключительно для использования в разметке XAML. Не существует простого способа для воспроизведения этого поведения в коде.

### <a name="xbind-in-controltemplate"></a>x: Bind в ControlTemplate

> [!NOTE]
> С помощью x: Bind в ControlTemplate требуется Windows 10, версия 1809 ([SDK 17763](https://developer.microsoft.com/windows/downloads/windows-10-sdk)) или более поздней версии. Дополнительные сведения о целевых версиях см. в статье [Адаптивный к версии код](https://msdn.microsoft.com/windows/uwp/debug-test-perf/version-adaptive-code).

Начиная с Windows 10, версия 1809, можно использовать расширение разметки **x: Bind** везде, где использовать **TemplateBinding** в [**ControlTemplate**](https://msdn.microsoft.com/library/windows/apps/br209391). 

Свойство [TargetType](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.controltemplate.targettype) необходима (обязательно) на [ControlTemplate](https://msdn.microsoft.com/library/windows/apps/br209391) при использовании **x: Bind**.

С поддержкой **x: Bind** можно использовать как хорошо, как двухсторонние привязки в [ControlTemplate](https://msdn.microsoft.com/library/windows/apps/br209391)обеих [привязки функций](../data-binding/function-bindings.md) .

В этом примере свойство **TextBlock.Text** принимает значение **Button.Content.ToString**. TargetType на ControlTemplate выступает в качестве источника данных и выполняет тот же результат TemplateBinding родительскому элементу.

```xaml
<ControlTemplate TargetType="Button">
    <Grid>
        <TextBlock Text="{x:Bind Content, Mode=OneWay}"/>
    </Grid>
</ControlTemplate>
```

## <a name="related-topics"></a>Ссылки по теме

* [Краткое руководство: шаблоны элементов управления](https://msdn.microsoft.com/library/windows/apps/xaml/hh465374)
* [Подробно о привязке данных](https://msdn.microsoft.com/library/windows/apps/mt210946)
* [**ControlTemplate**](https://msdn.microsoft.com/library/windows/apps/br209391)
* [Обзор языка XAML](xaml-overview.md)
* [Общие сведения о свойствах зависимостей](dependency-properties-overview.md)
 


---
description: Связывает значение свойства в шаблоне элемента управления и значение какого-либо другого предоставленного свойства элемента управления-шаблона. TemplateBinding может использоваться только в пределах определения ControlTemplate в XAML.
title: Расширение разметки TemplateBinding
ms.assetid: FDE71086-9D42-4287-89ED-8FBFCDF169DC
ms.date: 10/29/2018
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: e784b14c30222a28a0e10f8ba0bcf96e6c7f9afd
ms.sourcegitcommit: ac7f3422f8d83618f9b6b5615a37f8e5c115b3c4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/29/2019
ms.locfileid: "66372309"
---
# <a name="templatebinding-markup-extension"></a>Расширение разметки {TemplateBinding}

Связывает значение свойства в шаблоне элемента управления и значение какого-либо другого предоставленного свойства элемента управления-шаблона. **TemplateBinding** может использоваться только в пределах определения [**ControlTemplate**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.ControlTemplate) в XAML.

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

## <a name="remarks"></a>Примечания

Использование **TemplateBinding** — фундаментальная часть процесса определения шаблона элемента управления независимо от того, создаете ли вы пользовательский элемент управления или заменяете шаблон для уже существующих элементов управления. Дополнительные сведения см. в разделе [краткое руководство: Шаблоны элементов управления](https://docs.microsoft.com/previous-versions/windows/apps/hh465374(v=win.10)).

Использование одного и того же имени свойства — довольно частая практика для *propertyName* и *targetProperty*. В данном случае элемент управления может определить свойство для самого себя и перенаправить это свойство существующему и интуитивно названному свойству одного из своих компонентов. Например, элемент управления, включающий в свою компоновку объект [**TextBlock**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.TextBlock), используемый для отображения собственного свойства **Text** элемента управления, может содержать этот XAML в составе шаблона элемента управления: `<TextBlock Text="{TemplateBinding Text}" .... />`

Типы, используемые в качестве значений исходного и целевого свойств, должны совпадать. При использовании **TemplateBinding** невозможно добавить конвертер. Несовпадение значений приводит к ошибке при синтаксическом анализе XAML. Если вам требуется конвертер, можно использовать подробный синтаксис для привязки шаблона, например: `{Binding RelativeSource={RelativeSource TemplatedParent}, Converter="..." ...}`

Попытка использования **TemplateBinding** вне определения [**ControlTemplate**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.ControlTemplate) в XAML вызовет ошибку средства синтаксического анализа.

В случаях, когда родительское значение для шаблона также откладывается в качестве другой привязки, вы можете использовать **TemplateBinding**. Вычисление **TemplateBinding** можно отложить до момента, когда у требуемых привязок времени выполнения появятся значения.

Привязка **TemplateBinding** всегда действует только в одном направлении. Оба участвующих свойства должны быть свойствами зависимостей.

**TemplateBinding** является расширением разметки. Расширения разметки обычно реализуются, если необходимо, чтобы значения атрибутов являлись буквенными значениями или именами обработчиков, и это требование является более глобальным, чем простая настройка преобразователей типов для определенных типов или свойств. Для всех расширений разметки в XAML в синтаксисе атрибутов используются символы "{" и "}". Это соответствует соглашению, по которому процессор XAML распознает, что расширение разметки должно обработать атрибут.

**Примечание**  реализации обработчика в среды выполнения XAML Windows никак не представлен класс резервного для **TemplateBinding**. Расширение **TemplateBinding** предназначено исключительно для использования в разметке XAML. Не существует простого способа для воспроизведения этого поведения в коде.

### <a name="xbind-in-controltemplate"></a>x: Bind в ControlTemplate

> [!NOTE]
> X: Bind для использования в ControlTemplate требуется Windows 10, версия 1809 ([SDK 17763](https://developer.microsoft.com/windows/downloads/windows-10-sdk)) или более поздней версии. Дополнительные сведения о целевых версиях см. в статье [Адаптивный к версии код](https://docs.microsoft.com/windows/uwp/debug-test-perf/version-adaptive-code).

Начиная с Windows 10, версия 1809, можно использовать **x: Bind** расширение разметки везде, где используются **TemplateBinding** в [ **ControlTemplate** ](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.ControlTemplate). 

[TargetType](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.controltemplate.targettype) необходимо свойство (обязательно) на [ControlTemplate](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.ControlTemplate) при использовании **x: Bind**.

С помощью **x: Bind** поддержки, можно использовать как [функцию привязки](../data-binding/function-bindings.md) а также имеют двухсторонние привязки в [ControlTemplate](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.ControlTemplate).

В этом примере **TextBlock.Text** значение свойства равно **Button.Content.ToString**. TargetType на ControlTemplate выступает в качестве источника данных и выполняет тот же результат, как TemplateBinding к родительскому элементу.

```xaml
<ControlTemplate TargetType="Button">
    <Grid>
        <TextBlock Text="{x:Bind Content, Mode=OneWay}"/>
    </Grid>
</ControlTemplate>
```

## <a name="related-topics"></a>См. также

* [Краткое руководство. Шаблоны элементов управления](https://docs.microsoft.com/previous-versions/windows/apps/hh465374(v=win.10))
* [Подробно о привязке данных](https://docs.microsoft.com/windows/uwp/data-binding/data-binding-in-depth)
* [**ControlTemplate**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.ControlTemplate)
* [Обзор языка XAML](xaml-overview.md)
* [Общие сведения о свойствах зависимостей](dependency-properties-overview.md)
 


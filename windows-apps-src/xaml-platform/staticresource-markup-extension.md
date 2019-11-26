---
description: Предоставляет значение для любого атрибута языка XAML путем оценки ссылки на уже определенный ресурс. Ресурсы определены в ResourceDictionary, а использование StaticResource определяет ссылку на ключ ресурса в ResourceDictionary.
title: Расширение разметки StaticResource
ms.assetid: D50349B5-4588-4EBD-9458-75F629CCC395
ms.date: 02/08/2017
ms.topic: article
keywords: Windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: 07d8e6c180f332e75852c6a6627004f0306e26d4
ms.sourcegitcommit: b52ddecccb9e68dbb71695af3078005a2eb78af1
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/20/2019
ms.locfileid: "74259846"
---
# <a name="staticresource-markup-extension"></a>Расширение разметки {StaticResource}


Предоставляет значение для любого атрибута языка XAML путем оценки ссылки на уже определенный ресурс. Ресурсы определены в [**ResourceDictionary**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.ResourceDictionary), а использование **StaticResource** определяет ссылку на ключ ресурса в **ResourceDictionary**.

## <a name="xaml-attribute-usage"></a>Использование атрибутов XAML

``` syntax
<object property="{StaticResource key}" .../>
```

## <a name="xaml-values"></a>Значения XAML

| Термин | Описание |
|------|-------------|
| key | Ключ для запрашиваемого ресурса. Изначально ключ назначается атрибутом [**ResourceDictionary**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.ResourceDictionary). Ключом ресурса может быть любая строка, определенная в грамматике XamlName. |

## <a name="remarks"></a>Примечания

**StaticResource** — это метод получения для атрибута XAML значений, которые определены где-либо в словаре ресурсов XAML. Эти значения могут быть добавлены в словарь ресурсов, так как они предназначены для использования несколькими значениями свойств; либо потому, что словарь ресурсов XAML используется как упаковка языка XAML или как метод разложения на элементарные операции. Примером метода упаковки XAML является словарь тем для элемента управления. Другим примером являются объединенные словари ресурсов, применяемые для резервирования ресурсов.

**StaticResource** принимает один аргумент, в котором задается ключ для запрашиваемого ресурса. Ключ ресурса всегда является строкой XAML в среде выполнения Windows. Дополнительные сведения о том, как изначально задается ключ ресурса, см. в разделе [Атрибут x:Key](x-key-attribute.md).

Правила, по которым **StaticResource** разрешается в элемент в словаре ресурсов, в этом разделе не описываются. Это зависит от наличия ссылки и ресурса в шаблоне, от применения объединенных словарей ресурсов и т. д. Дополнительные сведения о том, как определять ресурсы и правильно использовать [**ResourceDictionary**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.ResourceDictionary), включая образец кода, см. в разделе [Ссылки ResourceDictionary и XAML](https://docs.microsoft.com/windows/uwp/controls-and-patterns/resourcedictionary-and-xaml-resource-references).

**Важно** .   параметр **StaticResource** не должен пытаться выполнить прямую ссылку на ресурс, который ОПРЕДЕЛЕН лексически в файле XAML. Создание таких ссылок не поддерживается. Даже если опережающая ссылка не вызовет ошибку, ее создание приведет к снижению производительности. Для достижения наилучших результатов составляйте словари ресурсов так, чтобы обходиться без опережающих ссылок.

Если задать в **StaticResource** ключ, который не удается разрешить, то во время выполнения создается исключение синтаксического анализа XAML. Средства разработки также могут выдавать предупреждения и ошибки.

В реализации процессора XAML в среде выполнения Windows отсутствует представление класса резервирования для функций **StaticResource**. Расширение **StaticResource** предназначено исключительно для использования в XAML. Наиболее близким аналогом в коде является использование API из коллекции [**ResourceDictionary**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.ResourceDictionary), например вызов [**Contains**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.resourcedictionary.contains) или [**TryGetValue**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.resourcedictionary.trygetvalue).

[Расширение разметки {ThemeResource}](themeresource-markup-extension.md) представляет собой аналогичное расширение разметки, которое ссылается на указанные ресурсы с другим расположением. Разница в том, что расширение разметки {ThemeResource} может возвращать различные ресурсы в зависимости от активной системной темы. Подробнее см. в разделе [Расширение разметки {ThemeResource}](themeresource-markup-extension.md).

**StaticResource** является расширением разметки. Расширения разметки обычно реализуются, если необходимо, чтобы значения атрибутов являлись буквенными значениями или именами обработчиков, и это требование является более глобальным, чем простая настройка преобразователей типов для определенных типов или свойств. Все расширения разметки в XAML используют символы "\{" и "\}" в синтаксисе атрибутов, что является соглашением, по которому обработчик XAML распознает, что расширение разметки должно обработать атрибут.

### <a name="an-example-staticresource-usage"></a>Пример использования {StaticResource}

Этот образец XAML взят из [образца привязки данных XAML](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/XamlBind).

```xml
<StackPanel Margin="5">
    <!-- Add converter as a resource to reference it from a Binding. --> 
    <StackPanel.Resources>
        <local:S2Formatter x:Key="GradeConverter"/>
    </StackPanel.Resources>
    <TextBlock Style="{StaticResource BasicTextStyle}" Text="Percent grade:" Margin="5" />
    <Slider x:Name="sliderValueConverter" Minimum="1" Maximum="100" Value="70" Margin="5"/>
    <TextBlock Style="{StaticResource BasicTextStyle}" Text="Letter grade:" Margin="5"/>
    <TextBox x:Name="tbValueConverterDataBound"
      Text="{Binding ElementName=sliderValueConverter, Path=Value, Mode=OneWay,  
        Converter={StaticResource GradeConverter}}" Margin="5" Width="150"/> 
</StackPanel> 
```

В этом конкретном примере создается объект, подкрепленный пользовательским классом, причем объект создается как ресурс в [**ResourceDictionary**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.ResourceDictionary). Чтобы стать действительным ресурсом, этот элемент `local:S2Formatter` должен также иметь значение атрибута **x:Key**. Значением атрибута является GradeConverter.

Затем ресурс запрашивается чуть дальше в коде XAML, где вы видите `{StaticResource GradeConverter}`.

Обратите внимание, как использование расширения разметки {StaticResource} задает свойство другого [расширения разметки {Binding}](binding-markup-extension.md). Таким образом, здесь использованы два вложенных расширения разметки. Внутренний сегмент вычисляется первым, то есть сначала определяется ресурс, который затем можно использовать в качестве значения. Этот же пример показан в разделе "Расширение разметки {Binding}".

## <a name="design-time-tools-support-for-the-staticresource-markup-extension"></a>Поддержка средств разработки для расширения разметки **{StaticResource}**

Microsoft Visual Studio 2013 могут содержать возможные значения ключа в раскрывающихся списках Microsoft IntelliSense при использовании расширения разметки **{StaticResource}** на странице XAML. Например, когда вы вводите {StaticResource, в раскрывающемся списке IntelliSense появляются все ключи ресурса из текущей области подстановки. Помимо типичных ресурсов, присутствующих на уровне страницы ([**FrameworkElement.Resources**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.frameworkelement.resources)) и уровне приложения ([**Application.Resources**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.application.resources)), также отображаются [ресурсы темы XAML](https://docs.microsoft.com/windows/uwp/controls-and-patterns/xaml-theme-resources) и ресурсы из всех расширений, используемых в проекте.

Если при использовании **{StaticResource}** обнаруживается ключ ресурса, функция **Перейти к определению** (F12) может разрешить ресурс и показать словарь, в котором он определен. Для ресурсов темы во время разработки эта функция ведет к файлу generic.xaml.

## <a name="related-topics"></a>См. также

* [Ссылки на ресурсы ResourceDictionary и XAML](https://docs.microsoft.com/windows/uwp/controls-and-patterns/resourcedictionary-and-xaml-resource-references)
* [**ResourceDictionary**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.ResourceDictionary)
* [Атрибут x:Key](x-key-attribute.md)
* [Расширение разметки {Семересаурце}](themeresource-markup-extension.md)


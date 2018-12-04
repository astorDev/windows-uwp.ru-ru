---
description: Предоставляет значение для любого атрибута языка XAML путем оценки ссылки на уже определенный ресурс. Ресурсы определены в ResourceDictionary, а использование StaticResource определяет ссылку на ключ ресурса в ResourceDictionary.
title: Расширение разметки StaticResource
ms.assetid: D50349B5-4588-4EBD-9458-75F629CCC395
ms.date: 02/08/2017
ms.topic: article
keywords: Windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: 012827165aaa4067c9844af0491afb77a53c5f50
ms.sourcegitcommit: b4c502d69a13340f6e3c887aa3c26ef2aeee9cee
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/03/2018
ms.locfileid: "8486523"
---
# <a name="staticresource-markup-extension"></a>Расширение разметки {StaticResource}


Предоставляет значение для любого атрибута языка XAML путем оценки ссылки на уже определенный ресурс. Ресурсы определены в [**ResourceDictionary**](https://msdn.microsoft.com/library/windows/apps/br208794), а использование **StaticResource** определяет ссылку на ключ ресурса в **ResourceDictionary**.

## <a name="xaml-attribute-usage"></a>Использование атрибутов XAML

``` syntax
<object property="{StaticResource key}" .../>
```

## <a name="xaml-values"></a>Значения XAML

| Термин | Описание |
|------|-------------|
| key | Ключ для запрашиваемого ресурса. Изначально ключ назначается атрибутом [**ResourceDictionary**](https://msdn.microsoft.com/library/windows/apps/br208794). Ключом ресурса может быть любая строка, определенная в грамматике XamlName. |

## <a name="remarks"></a>Примечания

**StaticResource**— это метод получения для атрибута XAML значений, которые определены где-либо в словаре ресурсов XAML. Эти значения могут быть добавлены в словарь ресурсов, так как они предназначены для использования несколькими значениями свойств; либо потому, что словарь ресурсов XAML используется как упаковка языка XAML или как метод разложения на элементарные операции. Примером метода упаковки XAML является словарь тем для элемента управления. Другим примером являются объединенные словари ресурсов, применяемые для резервирования ресурсов.

**StaticResource** принимает один аргумент, в котором задается ключ для запрашиваемого ресурса. Ключ ресурса всегда является строкой XAML в среде выполнения Windows. Дополнительные сведения о том, как изначально задается ключ ресурса, см. в разделе [Атрибут x:Key](x-key-attribute.md).

Правила, по которым **StaticResource** разрешается в элемент в словаре ресурсов, в этом разделе не описываются. Это зависит от наличия ссылки и ресурса в шаблоне, от применения объединенных словарей ресурсов и т. д. Дополнительные сведения о том, как определять ресурсы и правильно использовать [**ResourceDictionary**](https://msdn.microsoft.com/library/windows/apps/br208794), включая образец кода, см. в разделе [Ссылки ResourceDictionary и XAML](https://msdn.microsoft.com/library/windows/apps/mt187273).

**Важные**  **StaticResource** не должно пытаться создать опережающую ссылку на ресурс, который определен лексически далее в XAML-файле. Создание таких ссылок не поддерживается. Даже если опережающая ссылка не вызовет ошибку, ее создание приведет к снижению производительности. Для достижения наилучших результатов составляйте словари ресурсов так, чтобы обходиться без опережающих ссылок.

Если задать в **StaticResource** ключ, который не удается разрешить, то во время выполнения создается исключение синтаксического анализа XAML. Средства разработки также могут выдавать предупреждения и ошибки.

В реализации процессора XAML в среде выполнения Windows отсутствует представление класса резервирования для функций **StaticResource**. Расширение **StaticResource** предназначено исключительно для использования в XAML. Наиболее близким аналогом в коде является использование API из коллекции [**ResourceDictionary**](https://msdn.microsoft.com/library/windows/apps/br208794), например вызов [**Contains**](https://msdn.microsoft.com/library/windows/apps/jj635925) или [**TryGetValue**](https://msdn.microsoft.com/library/windows/apps/jj603139).

[Расширение разметки {ThemeResource}](themeresource-markup-extension.md) представляет собой аналогичное расширение разметки, которое ссылается на указанные ресурсы с другим расположением. Разница в том, что расширение разметки {ThemeResource} может возвращать различные ресурсы в зависимости от активной системной темы. Подробнее см. в разделе [Расширение разметки {ThemeResource}](themeresource-markup-extension.md).

**StaticResource** является расширением разметки. Расширения разметки обычно реализуются, если необходимо, чтобы значения атрибутов являлись буквенными значениями или именами обработчиков, и это требование является более глобальным, чем простая настройка преобразователей типов для определенных типов или свойств. Для всех расширений разметки в XAML в синтаксисе атрибутов используются символы "\{" и "\}". Это соответствует соглашению, по которому процессор XAML распознает, что расширение разметки должно обработать атрибут.

### <a name="an-example-staticresource-usage"></a>Пример использования {StaticResource}

Этот образец XAML взят из [образца привязки данных XAML](http://go.microsoft.com/fwlink/p/?linkid=226854).

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

В этом конкретном примере создается объект, подкрепленный пользовательским классом, причем объект создается как ресурс в [**ResourceDictionary**](https://msdn.microsoft.com/library/windows/apps/br208794). Чтобы стать действительным ресурсом, этот элемент `local:S2Formatter` должен также иметь значение атрибута **x:Key**. Значением атрибута является GradeConverter.

Затем ресурс запрашивается чуть дальше в коде XAML, где вы видите `{StaticResource GradeConverter}`.

Обратите внимание, как использование расширения разметки {StaticResource} задает свойство другого [расширения разметки {Binding}](binding-markup-extension.md). Таким образом, здесь использованы два вложенных расширения разметки. Внутренний сегмент вычисляется первым, то есть сначала определяется ресурс, который затем можно использовать в качестве значения. Этот же пример показан в разделе "Расширение разметки {Binding}".

## <a name="design-time-tools-support-for-the-staticresource-markup-extension"></a>Поддержка средств разработки для расширения разметки **{StaticResource}**

Microsoft Visual Studio2013 может содержать возможные значения ключей в раскрывающихся списков Microsoft IntelliSense при использовании расширения разметки **{StaticResource}** в XAML-страницы. Например, когда вы вводите {StaticResource, в раскрывающемся списке IntelliSense появляются все ключи ресурса из текущей области подстановки. Помимо типичных ресурсов, присутствующих на уровне страницы ([**FrameworkElement.Resources**](https://msdn.microsoft.com/library/windows/apps/br208740)) и уровне приложения ([**Application.Resources**](https://msdn.microsoft.com/library/windows/apps/br242338)), также отображаются [ресурсы темы XAML](https://msdn.microsoft.com/library/windows/apps/mt187274) и ресурсы из всех расширений, используемых в проекте.

Если при использовании **{StaticResource}** обнаруживается ключ ресурса, функция **Перейти к определению** (F12) может разрешить ресурс и показать словарь, в котором он определен. Для ресурсов темы во время разработки эта функция ведет к файлу generic.xaml.

## <a name="related-topics"></a>Ссылки по теме

* [Ссылки на ресурсы ResourceDictionary и XAML](https://msdn.microsoft.com/library/windows/apps/mt187273)
* [**ResourceDictionary**](https://msdn.microsoft.com/library/windows/apps/br208794)
* [Атрибут x:Key](x-key-attribute.md)
* [Расширение разметки {ThemeResource}](themeresource-markup-extension.md)


---
Description: Используйте селекторы шаблонов данных, чтобы настроить стили элементов на основе свойств элемента.
title: Выбор шаблона данных
label: Data template selection
template: detail.hbs
ms.date: 10/18/2019
ms.topic: article
keywords: windows 10, uwp
pm-contact: anawish
ms.openlocfilehash: d388e1f4b3f1b1be4e265185934a02b6ccd20064
ms.sourcegitcommit: 76e8b4fb3f76cc162aab80982a441bfc18507fb4
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/29/2020
ms.locfileid: "76123856"
---
# <a name="data-template-selection-styling-items-based-on-their-properties"></a>Выбор шаблона данных: Задание стиля для элементов на основе их свойств

Настраиваемый дизайн элементов управления коллекциями управляется [DataTemplate](/uwp/api/windows.ui.xaml.datatemplate). Шаблоны данных определяют, как каждый элемент должен быть размещен и оформлен, и эта разметка применяется к каждому элементу в коллекции. В этой статье объясняется, как использовать [DataTemplateSelector](/uwp/api/windows.ui.xaml.controls.datatemplateselector) для применения различных шаблонов данных в коллекции и выбора используемого шаблона данных на основе определенных свойств или значений элементов.

> **Важные API**: [DataTemplateSelector](/uwp/api/windows.ui.xaml.controls.datatemplateselector), [DataTemplate](/uwp/api/windows.ui.xaml.datatemplate)

[DataTemplateSelector](/uwp/api/windows.ui.xaml.controls.datatemplateselector) — это класс, который позволяет выбрать настраиваемую логику выбора шаблона. Он позволяет определять правила, указывающие, какой шаблон данных следует использовать для определенных элементов в коллекции. Для реализации этой логики вы создаете подкласс DataTemplateSelector в коде программной части и выбираете логику, которая определяет, какой шаблон данных следует использовать для какой категории элементов (например, элементы определенного типа или элементов с определенным значением свойства и т. д.). Экземпляр этого класса объявляется в разделе "Ресурсы" файла XAML вместе с определениями шаблонов данных, которые вы будете использовать. Эти ресурсы указываются с помощью значения `x:Key`, которое позволяет ссылаться на них в XAML.

## <a name="prerequisites"></a>Предварительные условия

- Как [использовать и создавать элементы управления коллекцией, например ListView или GridView.](listview-and-gridview.md)
- Как [настроить внешний вид элементов с помощью DataTemplate.](item-containers-templates.md#data-template)

## <a name="when-not-to-use-a-datatemplateselector"></a>Когда не следует использовать DataTemplateSelector

Как правило, каждому элементу в ListView или GridView не следует давать совершенно другой макет или стиль — это ухудшит использование DataTemplateSelector и негативно скажется на производительности.

Некоторые элементы визуального отображения элемента списка можно контролировать с помощью всего одного шаблона данных путем привязки определенных свойств. Например, для элементов можно использовать разные значки. Для этого нужно привязать данные к свойству источника значка в шаблоне и присвоить элементам разные значения для этого свойства источника значка. Это позволит повысить производительность по сравнению с использованием DataTemplateSelector.

## <a name="when-to-use-a-datatemplateselector"></a>Когда следует использовать DataTemplateSelector

Если вы хотите использовать несколько шаблонов данных в одном элементе управления коллекцией, необходимо создать [DataTemplateSelector](/uwp/api/windows.ui.xaml.controls.datatemplateselector). `DataTemplateSelector` предоставляет гибкие возможности для выделения определенных элементов, сохраняя при этом элементы в одинаковом макете. Существует множество вариантов использования, в которых DataTemplateSelector полезен, и несколько сценариев, в которых следует лучше подумать об элементе управления и стратегии, которые вы используете.

Элементы управления коллекцией обычно привязаны к коллекции элементов, которые относятся к одному типу. Однако несмотря на то, что элементы могут принадлежать к одному и тому же типу, они могут иметь разные значения для определенных свойств или представлять различные величины. Некоторые элементы также могут быть более важными, чем другие, или один элемент может быть особенно важным, отличаться и иметь необходимость в визуальном выделении. В таких ситуациях DataTemplateSelector будет очень полезен.

Вы можете также выполнить привязку к коллекции, содержащей различные типы элементов. В привязанной коллекции могут содержаться сочетания строк,целые числа, объекты пользовательских классов и многое другое. Это делает DataTemplateSelector особенно полезным, поскольку он может назначать различные шаблоны данных на основе типа объекта элемента.

Вот несколько примеров использования селектора шаблонов данных:

- Представление различных уровней сотрудников в ListView — каждому типу/уровню сотрудника может потребоваться свой цветной фон, чтобы его было легко различить.
- Представление продаваемых товаров в галерее продуктов, в которой используется GridView — продаваемому товару может потребоваться красный фон или другой цветовой шрифт, чтобы он выделялся среди товаров по обычной цене.
- Представление победителей и верхних фотографий в фотогалерее с помощью FlipView.
- Необходимость представлять отрицательные/положительные числа в ListView по-разному, или короткие/длинные строки.

## <a name="create-a-datatemplateselector"></a>Создание DataTemplateSelector

При создании селектора шаблонов данных следует определить логику выбора шаблонов в своем коде и шаблоны данных в XAML.

### <a name="code-behind-component"></a>Компонент кода программной части

Чтобы использовать селектор шаблона данных, сначала необходимо создать подкласс [DataTemplateSelector](/uwp/api/windows.ui.xaml.controls.datatemplateselector) (производный от него класс) в коде программной части. В вашем классе вы объявляете каждый шаблон свойством класса. Затем вы переопределяете метод [SelectTemplateCore](/uwp/api/windows.ui.xaml.controls.datatemplateselector.selecttemplatecore), чтобы включить свою собственную логику выбора шаблона.

Вот пример простого `DataTemplateSelector` подкласса под названием `MyDataTemplateSelector`.

```csharp
public class MyDataTemplateSelector : DataTemplateSelector
{
    public DataTemplate Normal { get; set; }
    public DataTemplate Accent { get; set; }

    protected override DataTemplate SelectTemplateCore(object item)
    {
        if ((int)item % 2 == 0)
        {
            return Normal;
        }
        else
        {
            return Accent;
        }
    }
}
```

Класс `MyDataTemplateSelector` походит от класса `DataTemplateSelector`и сначала определяет два простые объекты [DataTemplate](/uwp/api/windows.ui.xaml.datatemplate): `Normal` и `Accent`. Пока это пустые объявления, но они будут "заполнены" разметкой в XAML-файле.

Метод `SelectTemplateCore` принимает объект элемента (т. е. каждый объект коллекции) и отменяется правилами, по которым `DataTemplate` будет возвращаться в зависимости от обстоятельств. В этом случае, если элемент имеет нечетный номер, он получает шаблон данных `Accent`, а если четный номер, то — `Normal`.

### <a name="xaml-component"></a>Компонент XAML

Во-вторых, вы должны создать экземпляр этого нового класса `MyDataTemplateSelector` в разделе "Ресурсы" своего файла XAML. Для всех ресурсов требуется `x:Key`, который вы используете для привязки его к свойству `ItemTemplateSelector` вашего элемента управления коллекцией (на следующем шаге). Вы также создаете два экземпляра объектов `DataTemplate` и определяете их макет в разделе ресурсов. Вы присваиваете эти шаблоны данных свойствам `Accent` и `Normal`, объявленным в классе `MyDataTemplateSelector`.

Вот пример необходимых ресурсов XAML и разметки:

```xaml
<Page.Resources>

<DataTemplate x:Key="NormalItemTemplate" x:DataType="x:Int32">
    <Button HorizontalAlignment="Stretch" VerticalAlignment="Stretch" Background="{ThemeResource SystemChromeLowColor}">
        <TextBlock Text="{x:Bind}" />
    </Button>
</DataTemplate>

<DataTemplate x:Key="AccentItemTemplate" x:DataType="x:Int32">
    <Button HorizontalAlignment="Stretch" VerticalAlignment="Stretch" Background="{ThemeResource SystemAccentColor}">
        <TextBlock Text="{x:Bind}" />
    </Button>
</DataTemplate>

<l:MyDataTemplateSelector x:Key="MyDataTemplateSelector"
    Normal="{StaticResource NormalItemTemplate}"
    Accent="{StaticResource AccentItemTemplate}"/>

</Page.Resources>
```

Как вы можете видеть выше, два шаблона данных `Normal` и `Accent` определены — они оба отображают элементы в виде кнопок, однако шаблон данных `Accent` использует кисть с цветовой темой для фона, а шаблон данных `Normal` использует кисть серого цвета (`SystemChromeLowColor`). Эти два шаблона данных затем присваиваются объектам DataTemplate `Normal` и `Accent`, которые являются атрибутами класса MyDataTemplateSelector, созданного в C# коде программной части.

Последний шаг — привязать ваш `DataTemplateSelector` к свойству `ItemTemplateSelector` вашего элемента управления коллекциями (в данном случае, ListView). Это заменяет необходимость присваивания значения свойству `ItemTemplate`. 

```xaml
<ListView x:Name = "TestListView"
          ItemsSource = "{x:Bind NumbersList}"
          ItemTemplateSelector = "{StaticResource MyDataTemplateSelector}">
</ListView>
```

Как только ваш код скомпилируется, каждый элемент коллекции будет проходить через переопределенный метод `SelectTemplateCore` в `MyDataTemplateSelector`, и отображаться с соответствующим DataTemplate.

> [!IMPORTANT]
> При использовании `DataTemplateSelector` с [ItemsRepeater](/uwp/api/microsoft.ui.xaml.controls.itemsrepeater?view=winui-2.2) вы связываете `DataTemplateSelector` со свойством `ItemTemplate`. `ItemsRepeater` не имеет свойства `ItemTemplateSelector`.

## <a name="datatemplateselector-performance-considerations"></a>Вопросы производительности DataTemplateSelector

При использовании ListView или GridView с большим объемом данных производительность прокрутки и панорамирования может стать проблемной. Чтобы обеспечить работоспособность больших коллекций, необходимо выполнить некоторые действия для повышения производительности шаблонов данных. Дополнительные сведения см. в разделе [Оптимизация пользовательского интерфейса ListView и GridView](/windows/uwp/debug-test-perf/optimize-gridview-and-listview).

- _Уменьшение элементов для каждого элемента_ — количество элементов пользовательского интерфейса в шаблоне данных должно быть минимальным.
- Повторное использование контейнеров с неоднородными коллекциями
  - Используйте _событие ChoosingItemContainer_ — это событие представляет собой высокопроизводительный способ использования разных шаблонов данных для разных элементов. Для достижения максимальной производительности, вы должны оптимизировать кэширование и выбор шаблонов данных для ваших конкретных данных.
  - Используйте _селектор шаблона элемента_ — селектора шаблона элемента (`DataTemplateSelector`) в некоторых случаях следует избегать из-за его влияния на производительность.

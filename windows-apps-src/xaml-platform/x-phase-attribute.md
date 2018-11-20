---
author: jwmsft
title: Атрибут xPhase
description: Использование xPhase с расширением разметки xBind позволяет выполнять постепенную отрисовку элементов ListView и GridView, улучшая качество процесса сдвига.
ms.assetid: BD17780E-6A34-4A38-8D11-9703107E247E
ms.author: jimwalk
ms.date: 02/08/2017
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: 17ee99553b5713acb1917ccb697abb2387d00da2
ms.sourcegitcommit: ed0304b8a214c03b8aab74b8ef12c9f82b8e3c5f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/19/2018
ms.locfileid: "7280232"
---
# <a name="xphase-attribute"></a>Атрибут x:Phase


Использование **x:Phase** с [расширением разметки {x:Bind}](x-bind-markup-extension.md) позволяет выполнять постепенную отрисовку элементов [**ListView**](https://msdn.microsoft.com/library/windows/apps/br242878) и [**GridView**](https://msdn.microsoft.com/library/windows/apps/br242705), улучшая тем самым качество процесса сдвига. **x:Phase** дает возможность декларативным способом добиться того же эффекта, что и при ручном управлении отрисовкой элементов списка с помощью события [**ContainerContentChanging**](https://msdn.microsoft.com/library/windows/apps/dn298914). См. также раздел [Добавочное обновление элементов ListView и GridView](../debug-test-perf/optimize-gridview-and-listview.md#update-items-incrementally).

## <a name="xaml-attribute-usage"></a>Использование атрибутов XAML


``` syntax
<object x:Phase="PhaseValue".../>
```

## <a name="xaml-values"></a>Значения XAML


| Термин | Описание |
|------|-------------|
| PhaseValue | Номер, обозначающий этап, на котором обрабатывается элемент. Значение по умолчанию:0. | 

## <a name="remarks"></a>Примечания.

Когда список быстро сдвигается с помощью касания или колесика мыши, то, в зависимости от сложности шаблона данных, отрисовка его элементов может выполняться медленнее, чем прокручивается список. Это в первую очередь касается переносных устройств с энергоэффективными процессорами, например телефонов или планшетов.

Фазирование дает возможность выполнять постепенную отрисовку шаблона данных способом, при котором в содержимом выделяются приоритетные части, поступающие в обработку первыми. Благодаря этому при быстром сдвиге для каждого элемента списка отображается определенная часть содержимого, а остальное обрабатывается в зависимости от запаса времени.

## <a name="example"></a>Пример.

```xml
<DataTemplate x:Key="PhasedFileTemplate" x:DataType="model:FileItem">
    <Grid Width="200" Height="80">
        <Grid.ColumnDefinitions>
           <ColumnDefinition Width="75" />
            <ColumnDefinition Width="*" />
        </Grid.ColumnDefinitions>
        <Grid.RowDefinitions>
            <RowDefinition Height="Auto" />
            <RowDefinition Height="Auto" />
            <RowDefinition Height="Auto" />
            <RowDefinition Height="*" />
        </Grid.RowDefinitions>
        <Image Grid.RowSpan="4" Source="{x:Bind ImageData}" MaxWidth="70" MaxHeight="70" x:Phase="3"/>
        <TextBlock Text="{x:Bind DisplayName}" Grid.Column="1" FontSize="12"/>
        <TextBlock Text="{x:Bind prettyDate}"  Grid.Column="1"  Grid.Row="1" FontSize="12" x:Phase="1"/>
        <TextBlock Text="{x:Bind prettyFileSize}"  Grid.Column="1"  Grid.Row="2" FontSize="12" x:Phase="2"/>
        <TextBlock Text="{x:Bind prettyImageSize}"  Grid.Column="1"  Grid.Row="3" FontSize="12" x:Phase="2"/>
    </Grid>
</DataTemplate>
```

Шаблон данных предусматривает 4 этапа:

1.  Появление текстового блока DisplayName. Все элементы управления, для которых не задан этап, по умолчанию относятся к этапу 0.
2.  Появление текстового блока prettyDate.
3.  Появление текстовых блоков prettyFileSize и prettyImageSize.
4.  Появление изображения.

Фазирование представляет собой функцию расширения [{x:Bind}](x-bind-markup-extension.md), взаимодействующую с элементами управления из [**ListViewBase**](https://msdn.microsoft.com/library/windows/apps/br242879), и обеспечивает постепенную обработку шаблона элемента для привязки данных. При отрисовке элементов списка **ListViewBase** применяет один этап ко всем видимым элементам, после чего переходит к следующему этапу. Отрисовка выполняется частями через определенные интервалы времени, благодаря чему по мере прокручивания списка требуемый объем работы повторно анализируется и элементы, которые уже вышли из области видимости, не обрабатываются.

Атрибут **x:Phase** можно указать в любом элементе в шаблоне данных, где используется [{x:Bind}](x-bind-markup-extension.md). Элемент, для которого задан этап, отличный от 0, будет скрыт (посредством свойства **Opacity**, а не **Visibility**) и появится только после завершения данного этапа и обновления привязок данных. При прокручивании элемента управления [**ListViewBase**](https://msdn.microsoft.com/library/windows/apps/br242879) обработка шаблонов для элементов, которых уже нет на экране, будет перезапущена для выполнения отрисовки только что появившихся элементов. Элементы пользовательского интерфейса в шаблоне сохраняют старые значения до тех пор, пока не будет выполнена повторная привязка данных. Из-за фазирования возникает задержка на этапе привязки данных, в связи с чем функция фазирования предусматривает скрытие элементов пользовательского интерфейса, если они устарели.

Для каждого элемента пользовательского интерфейса можно задать только один этап. Когда этап задан, он применяется ко всем привязкам этого элемента. Если этап не задан, его значение считается равным 0.

Номера этапов не обязательно должны следовать друг за другом в каком-либо порядке. Они соответствуют значению [**ContainerContentChangingEventArgs.Phase**](https://msdn.microsoft.com/library/windows/apps/dn298493). Событие [**ContainerContentChanging**](https://msdn.microsoft.com/library/windows/apps/dn298914) создается для каждого этапа перед обработкой привязок **x:Phase**.

Фазирование затрагивает только привязки [{x:Bind}](x-bind-markup-extension.md) и не касается привязок [{Binding}](binding-markup-extension.md).

Фазирование применяется только в тех случаях, когда для отрисовки шаблона элемента используется элемент управления с поддержкой фазирования. Для Windows10, то есть [**ListView**](https://msdn.microsoft.com/library/windows/apps/br242878) и [**GridView**](https://msdn.microsoft.com/library/windows/apps/br242705). Фазирование не применяется к шаблонам данных, задействованным в других элементах управления или сценариях (например, [**ContentTemplate**](https://msdn.microsoft.com/library/windows/apps/br209369) или разделы [**Hub**](https://msdn.microsoft.com/library/windows/apps/dn251843)). В этих случаях привязка к данным сразу выполняется для всех элементов пользовательского интерфейса.


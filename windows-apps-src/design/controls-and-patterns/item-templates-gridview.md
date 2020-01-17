---
Description: Шаблоны элементов для представления сетки
title: Шаблоны элементов для представления сетки
template: detail.hbs
ms.date: 11/03/2017
ms.topic: article
keywords: windows 10, uwp, fluent
ms.openlocfilehash: 17c14d7147cafa17e78bd13bf6684a99d87cbdfd
ms.sourcegitcommit: 26bb75084b9d2d2b4a76d4aa131066e8da716679
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/06/2020
ms.locfileid: "75684410"
---
# <a name="item-templates-for-grid-view"></a>Шаблоны элементов для представления сетки

В этом разделе вы найдете шаблоны элементов, которые можно использовать вместе с элементом управления [**GridView**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.GridView). Используйте эти шаблоны для создания интерфейса приложений стандартных типов.

Для демонстрации связывания данных эти шаблоны связывают тип **GridViewItems** с примером класса записи из раздела о [привязке данных](../../data-binding/data-binding-quickstart.md).

> [!NOTE] 
> Если тип **DataTemplate** содержит несколько элементов управления (например, больше, чем один элемент **TextBlock**), специальное имя по умолчанию для средств чтения с экрана можно получить с использованием .ToString() элемента. Для удобства можно задать имя [**AutomationProperties.Name**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.automation.automationproperties) в корневом элементе шаблона **DataTemplate**. См. подробнее о [специальных возможностях](../accessibility/accessibility-overview.md).

## <a name="icon-and-text"></a>Значок и текст
Эти шаблоны используются для отображения коллекции приложений в сетке со значком и текстом.

![Пример представления сетки с небольшим значком и текстом](images/listitems/icontext.png)
```xaml
<GridView ItemsSource="{x:Bind ViewModel.Recordings}">
    <GridView.ItemTemplate>
        <DataTemplate x:Name="IconTextTemplate" x:DataType="local:Recording">
            <StackPanel Width="264" Height="48" Padding="12" Orientation="Horizontal" AutomationProperties.Name="{x:Bind CompositionName}">
                <SymbolIcon Symbol="Audio" VerticalAlignment="Top"/>
                <TextBlock Margin="12,0,0,0" Width="204" Text="{x:Bind CompositionName}"/>
            </StackPanel>
        </DataTemplate>
    </GridView.ItemTemplate>
    <GridView.ItemsPanel>
        <ItemsPanelTemplate>
            <ItemsWrapGrid MaximumRowsOrColumns="4" Orientation="Horizontal" HorizontalAlignment="Center"/>
        </ItemsPanelTemplate>
    </GridView.ItemsPanel>
</GridView>
```

![Пример представления сетки со значком и двухстрочным текстом](images/listitems/icontext2.png)
```xaml
<GridView ItemsSource="{x:Bind ViewModel.Recordings}">
    <GridView.ItemTemplate>
        <DataTemplate x:Name="IconTextTemplate2" x:DataType="local:Recording">
            <StackPanel Width="264" Height="120" Padding="12" Orientation="Horizontal" AutomationProperties.Name="{x:Bind CompositionName}">
                <FontIcon Margin="0,6,0,0" FontSize="48" FontFamily="Segoe MDL2 Assets" FontWeight="Bold" Glyph="&#xE8D6;" VerticalAlignment="Top"/>
                <StackPanel Margin="16,1,0,0">
                    <TextBlock Width="176" Margin="0,0,0,2" TextWrapping="WrapWholeWords" TextTrimming="Clip" Text="{x:Bind CompositionName}"/>
                    <TextBlock Width="176" Height="48" Style="{ThemeResource CaptionTextBlockStyle}" Foreground="{ThemeResource SystemControlPageTextBaseMediumBrush}" TextWrapping="WrapWholeWords" TextTrimming="Clip" Text="{x:Bind ArtistName}" />
                </StackPanel>
            </StackPanel>
        </DataTemplate>
    </GridView.ItemTemplate>
    <GridView.ItemsPanel>
        <ItemsPanelTemplate>
            <ItemsWrapGrid MaximumRowsOrColumns="4" Orientation="Horizontal" HorizontalAlignment="Center" Margin="40,0"/>
        </ItemsPanelTemplate>
    </GridView.ItemsPanel>
</GridView>
```

## <a name="image-gallery"></a>Галерея изображений
Этот шаблон используется для отображения коллекции изображений в сетке с режимом множественного выбора.

![Структура элементов gridview](images/listitems/gridviewitems.png)
```xaml
<GridView SelectionMode="Multiple">
    <GridView.ItemTemplate>
        <DataTemplate x:Name="ImageGalleryDataTemplate">
            <Image Source="Placeholder.png" Height="180" Width="180" Stretch="UniformToFill"/>
        </DataTemplate>
    </GridView.ItemTemplate>
    <GridView.ItemsPanel>
        <ItemsPanelTemplate>
            <ItemsWrapGrid MaximumRowsOrColumns="3" Orientation="Horizontal"/>
        </ItemsPanelTemplate>
    </GridView.ItemsPanel>
</GridView>
```
## <a name="image-and-text"></a>Изображение и текст
Эти шаблоны используются для отображения медиаколлекции с текстом под ней.

![Пример представления gridview с квадратным изображением и текстом](images/listitems/imageandtext.png)
```xaml
<GridView ItemsSource="{x:Bind ViewModel.Recordings}">
    <GridView.ItemTemplate>
        <DataTemplate x:Name="ImageTextDataTemplate" x:DataType="local:Recording">
            <StackPanel Height="280" Width="180" Margin="12" AutomationProperties.Name="{x:Bind CompositionName}">
                <Image Source="Placeholder.png" Height="180" Width="180" Stretch="UniformToFill"/>
                <StackPanel Margin="0,12">
                    <TextBlock Text="{x:Bind CompositionName}"/>
                    <TextBlock Text="{x:Bind ArtistName}" Style="{ThemeResource CaptionTextBlockStyle}" Foreground="{ThemeResource SystemControlPageTextBaseMediumBrush}"/>
                </StackPanel>
            </StackPanel>
        </DataTemplate>
    </GridView.ItemTemplate>
    <GridView.ItemsPanel>
        <ItemsPanelTemplate>
            <ItemsWrapGrid MaximumRowsOrColumns="10" Orientation="Horizontal"/>
        </ItemsPanelTemplate>
    </GridView.ItemsPanel>
</GridView>
```

![Пример представления gridview с прямоугольным изображением и текстом](images/listitems/imageandtext2.png)
```xaml
<GridView ItemsSource="{x:Bind ViewModel.Recordings}">
    <GridView.ItemTemplate>
        <DataTemplate x:Name="ImageTextDataTemplate2" x:DataType="local:Recording">
            <StackPanel Height="280" Width="320" Margin="12" AutomationProperties.Name="{x:Bind CompositionName}">
                <Image Source="Placeholder.png" Height="180" Width="320" Stretch="UniformToFill"/>
                <StackPanel Margin="0,12">
                    <TextBlock Text="{x:Bind CompositionName}"/>
                    <TextBlock Text="{x:Bind ArtistName}" Style="{ThemeResource CaptionTextBlockStyle}" Foreground="{ThemeResource SystemControlPageTextBaseMediumBrush}"/>
                </StackPanel>
            </StackPanel>
        </DataTemplate>
    </GridView.ItemTemplate>
    <GridView.ItemsPanel>
        <ItemsPanelTemplate>
            <ItemsWrapGrid MaximumRowsOrColumns="4" Orientation="Horizontal"/>
        </ItemsPanelTemplate>
    </GridView.ItemsPanel>
</GridView>
```

## <a name="image-with-text-overlay"></a>Изображением с наложением текста
Этот шаблон используется для отображения медиаколлекции с наложением текста.

![Пример представления gridview с изображением и наложением текста](images/listitems/imageoverlay.png)
```xaml
<GridView ItemsSource="{x:Bind ViewModel.Recordings}">
    <GridView.ItemTemplate>
        <DataTemplate x:Name="ImageOverlayDataTemplate" x:DataType="local:Recording">
            <Grid Height="180" Width="320" AutomationProperties.Name="{x:Bind CompositionName}">
                <Image Source="Placeholder.png" Stretch="UniformToFill"/>
                <StackPanel Orientation="Vertical" Height="60" VerticalAlignment="Bottom" Background="{ThemeResource SystemBaseLowColor}" Padding="12">
                    <TextBlock Text="{x:Bind CompositionName}"/>
                    <TextBlock Text="{x:Bind ArtistName}" Style="{ThemeResource CaptionTextBlockStyle}" Foreground="{ThemeResource SystemControlPageTextBaseMediumBrush}"/>
                </StackPanel>
            </Grid>
        </DataTemplate>
    </GridView.ItemTemplate>
    <GridView.ItemsPanel>
        <ItemsPanelTemplate>
            <ItemsWrapGrid MaximumRowsOrColumns="4" Orientation="Horizontal"/>
        </ItemsPanelTemplate>
    </GridView.ItemsPanel>
</GridView>
```

## <a name="related-articles"></a>Похожие статьи
- [Класс GridView](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.GridView)
- [Общие сведения о привязке данных](../../data-binding/data-binding-quickstart.md)
- [Общие сведения о специальных возможностях](../accessibility/accessibility-overview.md)
- [Пример элементов ListView и GridView (Windows 10)](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/XamlListView)
- [Эскизы](../../files/thumbnails.md)

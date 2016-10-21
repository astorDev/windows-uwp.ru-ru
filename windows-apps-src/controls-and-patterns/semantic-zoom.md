---
author: Jwmsft
Description: "Элемент управления Semantic Zoom дает пользователю возможность изменять масштаб, переходя от одного семантического представления одного и того же набора данных к другому."
title: "Контекстное масштабирование"
ms.assetid: B5C21FE7-BA83-4940-9CC1-96F6A2DC28C7
label: Semantic zoom
template: detail.hbs
translationtype: Human Translation
ms.sourcegitcommit: eb6744968a4bf06a3766c45b73b428ad690edc06
ms.openlocfilehash: 125cb6e45defe3213af3f5cd20f524a5311241af

---
# Контекстное масштабирование

<link rel="stylesheet" href="https://az835927.vo.msecnd.net/sites/uwp/Resources/css/custom.css"> 

Контекстное масштабирование позволяет пользователю переключаться между двумя представлениями одного и того же содержимого с разной детализацией и быстро переходить между разделами большого набора сгруппированных данных.
 
- Представление с увеличенным масштабом — это основное представление содержимого. Это основное представление, где вы размещаете отдельные элементы данных. 
- Уменьшенное представления — это более высокий уровень представления того же содержимого. В этом представлении обычно отображаются заголовки группы для набора сгруппированных данных. 

Например, при просмотре адресной книги пользователь может уменьшить масштаб, чтобы быстро перейти к списку имен на букву "Б", а затем, увеличив масштаб представления, просмотреть имена на эту букву. 

<div class="important-apis" >
<b>Важные API</b><br/>
<ul>
<li><a href="https://msdn.microsoft.com/library/windows/apps/hh702601"><strong>Класс SemanticZoom</strong></a></li>
<li><a href="https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.listview.aspx"><strong>Класс ListView</strong></a></li>
<li><a href="https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.gridview.aspx"><strong>Класс GridView</strong></a></li>
</ul>

</div>
</div>






**Возможности**:

-   Размер уменьшенного представления не может выходить за границы элемента управления "Контекстное масштабирование".
-   Касание заголовка группы переключает представления. Как способ переключения между представлениями можно включить сжатие.
-   Активные заголовки переключаются между представлениями.

## Выбор правильного элемента управления

Используйте элемент управления **SemanticZoom**, если необходимо отображать набор сгруппированных данных, который настолько большой, что его не возможно полностью показать на одной или двух страницах.

Не путайте контекстное масштабирование с оптическим масштабированием. Хотя обе технологии используют одни и те же механизмы взаимодействия и базовое поведение (отображение более или менее детализированного представления с учетом коэффициента масштабирования), оптическим масштабированием называется настройка увеличения какой-либо области содержимого или объекта, например фотографии.  Подробнее об элементах управления для визуального масштабирования см. в разделе об элементе управления [**ScrollViewer**](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.scrollviewer.aspx).

## Примеры

**Приложение "Фотографии"**

Это контекстное масштабирование в приложении "Фотографии". Фотографии группируются по месяцам. При выборе названия месяца в представлении таблицы по умолчанию изображение уменьшается до представления списка месяцев для ускорения навигации.

![Контекстное масштабирование в приложении "Фотографии"](images/control-examples/semantic-zoom-photos.png)

**адресная книга;**

Адресная книга — еще один пример набора данных, по которому может быть гораздо удобнее перемещаться при помощи контекстного масштабирования. Уменьшенное представление можно использовать для быстрого перехода к определенной букве (левое изображение), а в представлении с увеличенным масштабом отображаются отдельные элементы данных (правое изображение).

![Пример контекстного масштабирования, используемого в списке контактов](images/semanticzoom-win10.png)

## Создание контекстного масштабирования

Элемент управления **SemanticZoom** не имеет собственного визуального представления. Это размещающий элемент управления, который управляет переходами между двумя другими элементами управления, позволяющими просматривать содержимое. Как правило, это элементы **ListView** или **GridView**.  Для элементов управления просмотром необходимо задать свойства [**ZoomedInView**](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.semanticzoom.zoomedinview.aspx) и [**ZoomedOutView**](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.semanticzoom.zoomedoutview.aspx) объекта SemanticZoom.

Для контекстного масштабирования необходимы три элемента:
- Сгруппированный источник данных
- Увеличенное представление, в котором отображаются данные на уровне элемента.
- Уменьшенное представление, в котором отображаются данные уровня группы.

Прежде чем использовать контекстное масштабирование, вы должны знать, как использовать представление списка со сгруппированными данными. Дополнительные сведения см. в разделах [Представление списка и представление сетки](listview-and-gridview.md) и [Группировка элементов в списке](). 

> **Примечание.**&nbsp;&nbsp;Чтобы определить увеличенное и уменьшенное представление элемента управления SemanticZoom, можно использовать любые два элемента управления, реализующие интерфейс [**ISemanticZoomInformation**](). Платформа XAML предоставляет три элемента управления, которые реализуют этот интерфейс: ListView, GridView и Hub.
 
 Данный пример XAML демонстрирует структуру элемента управления SemanticZoom. Другие элементы управления назначаются свойствам ZoomedInView и ZoomedOutView.
 
 **XAML**
 ```xaml
<SemanticZoom>
    <SemanticZoom.ZoomedInView>
        <!-- Put the GridView for the zoomed out view here. -->   
    </SemanticZoom.ZoomedInView>

    <SemanticZoom.ZoomedOutView>
        <!-- Put the ListView for the zoomed in view here. -->       
    </SemanticZoom.ZoomedOutView>
</SemanticZoom>
 ```
 
Приведенные здесь примеры взяты со страницы SemanticZoom [Пример базовых характеристик пользовательского интерфейса XAML](http://go.microsoft.com/fwlink/p/?LinkId=619992). Чтобы просмотреть код полностью, включая источник данных, нужно загрузить этот пример. Это контекстное масштабирование использует класс GridView для представления увеличенного представления, а класс ListView— для уменьшенного представления.
  
**Определение увеличенного представления**

Это элемент управления GridView для увеличенного представления. Увеличенное представление должно отображать отдельные элементы данных в группах. В этом примере показано, как отображать элементы в сетке с изображением и текстом. 

**XAML**
```xaml
<SemanticZoom.ZoomedInView>
    <GridView ItemsSource="{x:Bind cvsGroups.View}" 
              ScrollViewer.IsHorizontalScrollChainingEnabled="False" 
              SelectionMode="None" 
              ItemTemplate="{StaticResource ZoomedInTemplate}">
        <GridView.GroupStyle>
            <GroupStyle HeaderTemplate="{StaticResource ZoomedInGroupHeaderTemplate}"/>
        </GridView.GroupStyle>
    </GridView>
</SemanticZoom.ZoomedInView>
```
 
Внешний вид заголовков группы определяется в ресурсе `ZoomedInGroupHeaderTemplate`. Внешний вид элементов определяется в ресурсе `ZoomedInTemplate`. 

**XAML**   
```xaml
<DataTemplate x:Key="" x:DataType="data:ControlInfoDataGroup">
    <TextBlock Text="{x:Bind Title}" 
               Foreground="{ThemeResource ApplicationForegroundThemeBrush}" 
               Style="{StaticResource SubtitleTextBlockStyle}"/>
</DataTemplate>

<DataTemplate x:Key="ZoomedInTemplate" x:DataType="data:ControlInfoDataItem">
    <StackPanel Orientation="Horizontal" MinWidth="200" Margin="12,6,0,6">
        <Image Source="{x:Bind ImagePath}" Height="80" Width="80"/>
        <StackPanel Margin="20,0,0,0">
            <TextBlock Text="{x:Bind Title}" 
                       Style="{StaticResource BaseTextBlockStyle}"/>
            <TextBlock Text="{x:Bind Subtitle}" 
                       TextWrapping="Wrap" HorizontalAlignment="Left" 
                       Width="300" Style="{StaticResource BodyTextBlockStyle}"/>
        </StackPanel>
    </StackPanel>
</DataTemplate>
```

**Определение уменьшенного представления**

Этот код XAML определяет элемент управления ListView для уменьшенного представления. Этот пример показывает, как отобразить заголовки группы в качестве текста в списке.

**XAML**
```xaml
<SemanticZoom.ZoomedOutView>
    <ListView ItemsSource="{x:Bind cvsGroups.View.CollectionGroups}" 
              SelectionMode="None" 
              ItemTemplate="{StaticResource ZoomedOutTemplate}" />
</SemanticZoom.ZoomedOutView>
```

 Внешний вид определяется в ресурсе `ZoomedOutTemplate`.
 
 **XAML**   
```xaml    
<DataTemplate x:Key="ZoomedOutTemplate" x:DataType="wuxdata:ICollectionViewGroup">
    <TextBlock Text="{x:Bind Group.(data:ControlInfoDataGroup.Title)}" 
               Style="{StaticResource SubtitleTextBlockStyle}" TextWrapping="Wrap"/>
</DataTemplate>
```

**Синхронизация представлений**

Представления с увеличенным и с уменьшенным масштабом следует синхронизировать. Тогда, если пользователь выбирает группу в представлении с уменьшенным масштабом, подробная информация о той же группе выводится в представлении с увеличенным масштабом. Для синхронизации представлений можно использовать класс [**CollectionViewSource**](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.data.collectionviewsource.aspx) или добавить код.

Любые элементы управления, привязанные к одному и тому же классу CollectionViewSource, будут всегда иметь один и тот же текущий элемент. Если оба представления используют один и тот же CollectionViewSource в качестве источника данных, CollectionViewSource синхронизирует представления автоматически. Дополнительные сведения см. в разделе [**CollectionViewSource**](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.data.collectionviewsource.aspx).

Если вы не используете [**CollectionViewSource**](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.semanticzoom.viewchangestarted.aspx) для синхронизации представлений, вы должны обработать событие ViewChangeStarted и синхронизировать элементы в обработчике событий, как показано здесь.

**XAML**
```xaml
<SemanticZoom x:Name="semanticZoom" ViewChangeStarted="SemanticZoom_ViewChangeStarted">
```

**C#**
```csharp
private void SemanticZoom_ViewChangeStarted(object sender, SemanticZoomViewChangedEventArgs e)
{
    if (e.IsSourceZoomedInView == false)
    {
        e.DestinationItem.Item = e.SourceItem.Item;
    }
}
```

## Рекомендации

-   При использовании контекстного масштабирования в приложении убедитесь, что макет элемента и направление сдвига не зависят от уровня масштабирования. Макеты и сдвиг в обоих режимах масштабирования должны быть последовательными и предсказуемыми.
-   Контекстное масштабирование позволяет пользователю быстро переходить к необходимому содержимому, поэтому ограничьте число страниц или экранов в режиме масштабирования до трех. Необходимость выполнения большого количества сдвигов снижает практическую ценность контекстного масштабирования.
-   Не используйте контекстное масштабирование для изменения объема содержимого. Например, фотоальбом не должен переходить в режим представления папок в проводнике.
-   Используйте структуру и семантику, необходимые для представления.
-   Используйте групповые названия для элементов, объединенных в коллекцию.
-   Применяйте упорядочение для коллекции несгруппированных, но упорядоченных элементов (например, хронологический порядок для дат и алфавитный порядок для списка имен).

## Связанные статьи

- [Основы проектирования навигации](../layout/navigation-basics.md)
- [Представления списка и сетки](listview-and-gridview.md)
- [Шаблоны элементов представления списка](listview-item-templates.md)

**Примеры**

- [Пример базовых характеристик пользовательского интерфейса XAML](http://go.microsoft.com/fwlink/p/?LinkId=619992)

 







<!--HONumber=Aug16_HO3-->



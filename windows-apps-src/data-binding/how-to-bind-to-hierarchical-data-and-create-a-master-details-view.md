---
ms.assetid: 0C69521B-47E0-421F-857B-851B0E9605F2
title: Привязка к иерархическим данным и создание представления основных и подробных данных
description: Вы можете создать многоуровневое представление основных и подробных иерархических данных (также известное как "список/подробности") с помощью привязки элементов управления к экземплярам CollectionViewSource, связанным друг с другом в цепочку.
---
# Привязка к иерархическим данным и создание представления основных и подробных данных

\[ Обновлено для приложений UWP в Windows 10. Статьи, касающиеся Windows 8.x, см. в разделе [Архив](http://go.microsoft.com/fwlink/p/?linkid=619132) \]


> **Примечание.**  См. также [Пример основных и подробных данных](http://go.microsoft.com/fwlink/p/?linkid=619991).

Вы можете создать многоуровневое представление основных и подробных иерархических данных (также известное как "список/подробности") с помощью привязки элементов управления к экземплярам [**CollectionViewSource**](https://msdn.microsoft.com/library/windows/apps/BR209833), связанным друг с другом в цепочку. В этом разделе мы используем [расширение разметки {x:Bind}](https://msdn.microsoft.com/library/windows/apps/Mt204783) где возможно, и более гибкое (но менее производительное) [расширение разметки {Binding}](https://msdn.microsoft.com/library/windows/apps/Mt204782) там, где необходимо.

Общая структура приложений универсальной платформы Windows (UWP) такова, что при выборе элемента в основном списке происходит переход на другие страницы с более подробной информацией. Это удобно, если вы хотите обеспечить информативное визуальное представление каждого элемента на каждом уровне иерархии. Другой вариант — отображение нескольких уровней данных на одной странице. Это полезно, если вы хотите отобразить несколько простых списков, позволяющих пользователям быстро найти интересующий их элемент. В этом разделе описано, как применить это взаимодействие. Экземпляры [**CollectionViewSource**](https://msdn.microsoft.com/library/windows/apps/BR209833) отслеживают текущие выделенные фрагменты на каждом уровне иерархии.

Создадим представление иерархии спортивных команд, организованной в списки по лигам, дивизионам и командам, включающее в себя просмотр подробных сведений о каждой команде. При выборе элемента из любого списка происходит автоматическое обновление последующих представлений.

![основное/подробное представление спортивной иерархии](images/xaml-masterdetails.png)

## Необходимые условия

Материал этого раздела предполагает, что вы умеете создавать простые приложения UWP. Инструкции по созданию первого приложения UWP см. в [статье о создании первого приложения UWP на C# или Visual Basic](https://msdn.microsoft.com/library/windows/apps/Hh974581).

## Создание проекта

Создайте проект **пустого приложения (Windows Universal)**. Назовите его "MasterDetailsBinding".

## Создание модели данных

Добавьте новый класс в свой проект, назовите его ViewModel.cs и добавьте к нему следующий код. Это будет вашим классом источника привязки.

```cs
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;

namespace MasterDetailsBinding
{
    public class Team
    {
        public string Name { get; set; }
        public int Wins { get; set; }
        public int Losses { get; set; }
    }

    public class Division
    {
        public string Name { get; set; }
        public IEnumerable&lt;Team&gt; Teams { get; set; }
    }

    public class League
    {
        public string Name { get; set; }
        public IEnumerable&lt;Division&gt; Divisions { get; set; }
    }

    public class LeagueList : List&lt;League&gt;
    {
        public LeagueList()
        {
            this.AddRange(GetLeague().ToList());
        }

        public IEnumerable&lt;League&gt; GetLeague()
        {
            return from x in Enumerable.Range(1, 2)
                   select new League
                   {
                       Name = &quot;League &quot; + x,
                       Divisions = GetDivisions(x).ToList()
                   };
        }

        public IEnumerable&lt;Division&gt; GetDivisions(int x)
        {
            return from y in Enumerable.Range(1, 3)
                   select new Division
                   {
                       Name = String.Format(&quot;Division {0}-{1}&quot;, x, y),
                       Teams = GetTeams(x, y).ToList()
                   };
        }

        public IEnumerable&lt;Team&gt; GetTeams(int x, int y)
        {
            return from z in Enumerable.Range(1, 4)
                   select new Team
                   {
                       Name = String.Format(&quot;Team {0}-{1}-{2}&quot;, x, y, z),
                       Wins = 25 - (x * y * z),
                       Losses = x * y * z
                   };
        }
    }
}
```

## Создание представления

После этого предоставьте класс источника привязки из класса, представляющего страницу разметки. Сделаем это, добавив свойство типа **LeagueList** к **MainPage**.

```cs
namespace MasterDetailsBinding
{
    /// &lt;summary&gt;
    /// An empty page that can be used on its own or navigated to within a Frame.
    /// &lt;/summary&gt;
    public sealed partial class MainPage : Page
    {
        public MainPage()
        {
            this.InitializeComponent();
            this.ViewModel = new LeagueList();
        }
        public LeagueList ViewModel { get; set; }
    }
}
```

Наконец замените содержимое файла MainPage.xaml следующей разметкой, в которой объявляются три экземпляра [**CollectionViewSource**](https://msdn.microsoft.com/library/windows/apps/BR209833) и связываются друг с другом в цепочку. После этого можно привязывать другие элементы управления к соответствующему экземпляру **CollectionViewSource** в зависимости от его уровня в иерархии.

```xaml
<Page
    x:Class=&quot;MasterDetailsBinding.MainPage&quot;
    xmlns=&quot;http://schemas.microsoft.com/winfx/2006/xaml/presentation&quot;
    xmlns:x=&quot;http://schemas.microsoft.com/winfx/2006/xaml&quot;
    xmlns:local=&quot;using:MasterDetailsBinding&quot;
    xmlns:d=&quot;http://schemas.microsoft.com/expression/blend/2008&quot;
    xmlns:mc=&quot;http://schemas.openxmlformats.org/markup-compatibility/2006&quot;
    mc:Ignorable=&quot;d&quot;>

    <Page.Resources>
        <CollectionViewSource x:Name=&quot;Leagues&quot;
            Source=&quot;{x:Bind ViewModel}&quot;/>
        <CollectionViewSource x:Name=&quot;Divisions&quot;
            Source=&quot;{Binding Divisions, Source={StaticResource Leagues}}&quot;/>
        <CollectionViewSource x:Name=&quot;Teams&quot;
            Source=&quot;{Binding Teams, Source={StaticResource Divisions}}&quot;/>

        <Style TargetType=&quot;TextBlock&quot;>
            <Setter Property=&quot;FontSize&quot; Value=&quot;15&quot;/>
            <Setter Property=&quot;FontWeight&quot; Value=&quot;Bold&quot;/>
        </Style>

        <Style TargetType=&quot;ListBox&quot;>
            <Setter Property=&quot;FontSize&quot; Value=&quot;15&quot;/>
        </Style>

        <Style TargetType=&quot;ContentControl&quot;>
            <Setter Property=&quot;FontSize&quot; Value=&quot;15&quot;/>
        </Style>

    </Page.Resources>

    <Grid Background=&quot;{ThemeResource ApplicationPageBackgroundThemeBrush}&quot;>

        <StackPanel Orientation=&quot;Horizontal&quot;>

            <!-- All Leagues view -->

            <StackPanel Margin=&quot;5&quot;>
                <TextBlock Text=&quot;All Leagues&quot;/>
                <ListBox ItemsSource=&quot;{Binding Source={StaticResource Leagues}}&quot; 
                    DisplayMemberPath=&quot;Name&quot;/>
            </StackPanel>

            <!-- League/Divisions view -->

            <StackPanel Margin=&quot;5&quot;>
                <TextBlock Text=&quot;{Binding Name, Source={StaticResource Leagues}}&quot;/>
                <ListBox ItemsSource=&quot;{Binding Source={StaticResource Divisions}}&quot; 
                    DisplayMemberPath=&quot;Name&quot;/>
            </StackPanel>

            <!-- Division/Teams view -->

            <StackPanel Margin=&quot;5&quot;>
                <TextBlock Text=&quot;{Binding Name, Source={StaticResource Divisions}}&quot;/>
                <ListBox ItemsSource=&quot;{Binding Source={StaticResource Teams}}&quot; 
                    DisplayMemberPath=&quot;Name&quot;/>
            </StackPanel>

            <!-- Team view -->

            <ContentControl Content=&quot;{Binding Source={StaticResource Teams}}&quot;>
                <ContentControl.ContentTemplate>
                    <DataTemplate>
                        <StackPanel Margin=&quot;5&quot;>
                            <TextBlock Text=&quot;{Binding Name}&quot; 
                                FontSize=&quot;15&quot; FontWeight=&quot;Bold&quot;/>
                            <StackPanel Orientation=&quot;Horizontal&quot; Margin=&quot;10,10&quot;>
                                <TextBlock Text=&quot;Wins:&quot; Margin=&quot;0,0,5,0&quot;/>
                                <TextBlock Text=&quot;{Binding Wins}&quot;/>
                            </StackPanel>
                            <StackPanel Orientation=&quot;Horizontal&quot; Margin=&quot;10,0&quot;>
                                <TextBlock Text=&quot;Losses:&quot; Margin=&quot;0,0,5,0&quot;/>
                                <TextBlock Text=&quot;{Binding Losses}&quot;/>
                            </StackPanel>
                        </StackPanel>
                    </DataTemplate>
                </ContentControl.ContentTemplate>
            </ContentControl>

        </StackPanel>

    </Grid>
</Page>
```

Обратите внимание, что при привязке представления подробностей непосредственно к [**CollectionViewSource**](https://msdn.microsoft.com/library/windows/apps/BR209833) подразумевается, что вы хотите выполнить привязку к текущему элементу в привязках, если невозможно найти путь в самой коллекции. Нет необходимости указывать свойство **CurrentItem** в качестве пути для привязки, хотя это можно сделать, если присутствует некая неоднозначность. Например, элемент [**ContentControl**](https://msdn.microsoft.com/library/windows/apps/BR209365), отображающий представление команды, имеет свойство [**Content**](https://msdn.microsoft.com/library/windows/apps/BR209365-content), привязанное к `Teams`**CollectionViewSource**. Однако элементы управления в [**DataTemplate**](https://msdn.microsoft.com/library/windows/apps/BR242348) привязаны к свойствам класса `Team`, так как при необходимости **CollectionViewSource** автоматически отображает выбранную на данный момент команду из списка.

 

 



<!--HONumber=Mar16_HO1-->



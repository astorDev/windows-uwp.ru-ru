---
author: mijacobs
description: Подключенные анимации позволяют сделать навигацию динамичной и наглядной, анимируя переход элемента от одного представления к другому.
title: Подключенная анимация
template: detail.hbs
ms.author: jimwalk
ms.date: 10/25/2017
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: windows10, uwp
pm-contact: stmoy
design-contact: conrwi
doc-status: Published
ms.localizationpriority: medium
ms.openlocfilehash: a789a8f082192b79b3e96990827f9a4f6a0eacbc
ms.sourcegitcommit: e4f3e1b2d08a02b9920e78e802234e5b674e7223
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/26/2018
ms.locfileid: "4206400"
---
# <a name="connected-animation-for-uwp-apps"></a>Подключенная анимация для приложений UWP

## <a name="what-is-connected-animation"></a>Что такое подключенная анимация?

Подключенные анимации позволяют сделать навигацию динамичной и наглядной, анимируя переход элемента от одного представления к другому. Это помогает пользователю сохранять контекст и обеспечивает преемственность представлений.
В случае подключенной анимации элемент сохраняется между представлениями во время смены содержимого пользовательского интерфейса, двигаясь по экрану от места расположения в исходном представлении до места назначения в новом. Это подчеркивает общность содержимого в представлениях и создает прекрасный динамический эффект в рамках перехода.

## <a name="see-it-in-action"></a>Подключенная анимация в действии

В этом коротком видео приложение использует подключенную анимацию, чтобы анимировать изображение, которое сохраняется и становится частью заголовка следующей страницы. Этот эффект помогает сохранить контекст пользователя во время перехода.

![Пример непрерывного движения в пользовательском интерфейсе](images/continuous3.gif)

<!-- 
<iframe width=640 height=360 src='https://microsoft.sharepoint.com/portals/hub/_layouts/15/VideoEmbedHost.aspx?chId=552c725c%2De353%2D4118%2Dbd2b%2Dc2d0584c9848&amp;vId=b2daa5ee%2Dbe15%2D4503%2Db541%2D1328a6587c36&amp;width=640&amp;height=360&amp;autoPlay=false&amp;showInfo=true' allowfullscreen></iframe>
-->

## <a name="video-summary"></a>Видео с кратким описанием

> [!VIDEO https://channel9.msdn.com/Events/Windows/Windows-Developer-Day-Fall-Creators-Update/WinDev005/player]

## <a name="connected-animation-and-the-fluent-design-system"></a>Подключенная анимация и система проектирования Fluent Design

 Система Fluent Design позволяет создавать современные и эффективные пользовательские интерфейсы, которые отличаются яркостью, глубиной, движением, материальностью и масштабированием. Подключенная анимация— это компонент системы проектирования Fluent Design, добавляющий движение в ваше приложение. Дополнительные сведения см. в разделе [Обзор системы проектирования Fluent для UWP](../fluent-design-system/index.md).

## <a name="why-connected-animation"></a>Преимущества подключенной анимации

При переходе с одной страницы на другую пользователю важно понимать, какое новое содержимое представлено после перехода и как оно соотносится с целью перехода. Подключенная анимация— это яркая визуальная аналогия, которая подчеркивает связь между двумя представлениями, привлекая внимание пользователя к их общему содержимому. Кроме того, благодаря подключенной анимации навигация страницы выглядит интереснее и профессиональнее, выделяя ваше приложение благодаря проработке движения.

## <a name="when-to-use-connected-animation"></a>Сценарии использования подключенной анимации

Подключенные анимации обычно используются при переходе на другую страницу, хотя их можно применять при любой смене контента в пользовательском интерфейсе, если нужно сохранить контекст пользователя. Рекомендуем использовать подключенную анимацию вместо [эффекта винта в переходах навигации](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.media.animation.navigationthemetransition.aspx), если исходное и целевое представления содержат общее изображение или другой элемент пользовательского интерфейса.

## <a name="how-to-implement"></a>Реализация

Настройка подключенной анимации включает два этапа:

1.  *подготовка* объекта анимации на исходной странице, которая показывает системе, что исходный элемент участвует в подключенной анимации; 
2.  *запуск* анимации на странице назначения с передачей ссылки элементу назначения.

Между этими этапами исходный элемент будет казаться зависшим над другими элементами пользовательского интерфейса, давая вам возможность одновременно выполнять другие анимации перехода. По этой причине между этапами не стоит ждать дольше 250миллисекунд, поскольку наличие исходного элемента может отвлекать пользователя. Если анимация подготовлена, но не запущена в течение трех секунд, она удаляется из системы и любые последующие вызовы **TryStart** будут отклонены.

Доступ к текущему экземпляру ConnectedAnimationService можно получить, вызвав метод **ConnectedAnimationService.GetForCurrentView**. Чтобы подготовить анимацию, вызовите **PrepareToAnimate** в данном экземпляре, передав ссылку на уникальный ключ и элемент пользовательского интерфейса, который вы хотите использовать во время перехода. Уникальный ключ позволяет получить анимацию позже, например на другой странице.

```csharp
ConnectedAnimationService.GetForCurrentView().PrepareToAnimate("image", SourceImage);
```

Чтобы запустить анимацию, вызовите **ConnectedAnimation.TryStart**. Правильный экземпляр анимации можно извлечь, вызвав **ConnectedAnimationService.GetAnimation** с помощью уникального ключа, который вы предоставили при создании анимации.

```csharp
ConnectedAnimation imageAnimation = 
    ConnectedAnimationService.GetForCurrentView().GetAnimation("image");
if (imageAnimation != null)
{
    imageAnimation.TryStart(DestinationImage);
}
```

Вот полный пример использования ConnectedAnimationService для создания перехода между двумя страницами.

*SourcePage.xaml*

```xaml
<Image x:Name="SourceImage"
       Width="200"
       Height="200"
       Stretch="Fill"
       Source="Assets/StoreLogo.png" />
```

*SourcePage.xaml.cs*

```csharp
private void NavigateToDestinationPage()
{
    ConnectedAnimationService.GetForCurrentView().PrepareToAnimate("image", SourceImage);
    Frame.Navigate(typeof(DestinationPage));
}
```

*DestinationPage.xaml*

```xaml
<Image x:Name="DestinationImage"
       Width="400"
       Height="400"
       Stretch="Fill"
       Source="Assets/StoreLogo.png" />
```

*DestinationPage.xaml.cs*

```csharp
protected override void OnNavigatedTo(NavigationEventArgs e)
{
    base.OnNavigatedTo(e);

    ConnectedAnimation imageAnimation = 
        ConnectedAnimationService.GetForCurrentView().GetAnimation("image");
    if (imageAnimation != null)
    {
        imageAnimation.TryStart(DestinationImage);
    }
}
```

## <a name="connected-animation-in-list-and-grid-experiences"></a>Подключенная анимация при использовании списка и сетки

Часто нужно будет использовать подключенную анимацию для перехода к элементу управления списком или сеткой либо от него. Чтобы упростить этот процесс, можно использовать новые методы **ListView** и **GridView**, **PrepareConnectedAnimation** и **TryStartConnectedAnimationAsync**.
Предположим, **ListView** содержит элемент с именем PortraitEllipse в шаблоне данных.

```xaml
<ListView x:Name="ContactsListView" Loaded="ContactsListView_Loaded">
    <ListView.ItemTemplate>
        <DataTemplate x:DataType="vm:ContactsItem">
            <Grid>
                …
                <Ellipse x:Name="PortraitEllipse" … />
            </Grid>
        </DataTemplate> 
    </ListView.ItemTemplate>
</ListView>
```

Чтобы подготовить подключенную анимацию с эллипсом в соответствии с данным элементом списка, вызовите метод **PrepareConnectedAnimation** с уникальным ключом, элементом и именем PortraitEllipse.

```csharp
void PrepareAnimationWithItem(ContactsItem item)
{
     ContactsListView.PrepareConnectedAnimation("portrait", item, "PortraitEllipse");
}
```

Кроме того, можно использовать **TryStartConnectedAnimationAsync**, чтобы запустить анимацию, используя этот элемент в качестве назначения (например, при возврате из представления подробных сведений). Если вы только что загрузили источник данных для **ListView**, **TryStartConnectedAnimationAsync** не запустит анимацию, пока не будет создан соответствующий контейнер элементов.

```csharp
private void ContactsListView_Loaded(object sender, RoutedEventArgs e)
{
    ContactsItem item = GetPersistedItem(); // Get persisted item
    if (item != null)
    {
        ContactsListView.ScrollIntoView(item);
        ConnectedAnimation animation = 
            ConnectedAnimationService.GetForCurrentView().GetAnimation("portrait");
        if (animation != null)
        {
            await ContactsListView.TryStartConnectedAnimationAsync(
                animation, item, "PortraitEllipse");
        }
    }
}
```

## <a name="coordinated-animation"></a>Согласованная анимация

![Согласованная анимация](images/connected-animations/coordinated_example.gif)

<!--
<iframe width=640 height=360 src='https://microsoft.sharepoint.com/portals/hub/_layouts/15/VideoEmbedHost.aspx?chId=552c725c%2De353%2D4118%2Dbd2b%2Dc2d0584c9848&amp;vId=9066bbbe%2Dcf58%2D4ab4%2Db274%2D595616f5d0a0&amp;width=640&amp;height=360&amp;autoPlay=false&amp;showInfo=true' allowfullscreen></iframe>
-->

*Согласованная анимация*— особый тип анимации открытия, когда элемент отображается рядом с целью подключенной анимации, добавляя анимацию в сочетании с элементом подключенной анимации во время его движения по экрану. Согласованная анимация может сделать переход визуально интереснее и привлечь внимание пользователя к контексту, общему для исходного и целевого представлений. На этих изображениях интерфейс подписи элемента анимирует использование согласованной анимации.

Используйте перегрузку двух параметров **TryStart**, чтобы добавить согласованные элементы в подключенную анимацию. Этот пример показывает согласованную анимацию макета "Сетка" с именем "DescriptionRoot", которая откроется вместе с элементом подключенной анимации "CoverImage".

*DestinationPage.xaml*

```xaml
<Grid>
    <Image x:Name="CoverImage" />
    <Grid x:Name="DescriptionRoot" />
</Grid>
```

*DestinationPage.xaml.cs*

```csharp
void OnNavigatedTo(NavigationEventArgs e)
{
    var animationService = ConnectedAnimationService.GetForCurrentView();
    var animation = animationService.GetAnimation("coverImage");
    
    if (animation != null)
    {
        // Don’t need to capture the return value as we are not scheduling any subsequent
        // animations
        animation.TryStart(CoverImage, new UIElement[] { DescriptionRoot });
     }
}
```

## <a name="dos-and-donts"></a>Рекомендации

- Используйте подключенную анимацию для переходов между страницами, когда исходная и целевая страница содержат общий элемент.
- Не ждите сетевые запросы или другие долго выполняющиеся асинхронные операции между подготовкой и запуском подключенной анимации. Может потребоваться предварительно загрузить данные, необходимые для выполнения перехода, или использовать изображение-заполнитель с низким разрешением во время загрузки в представлении назначения изображения с высоким разрешением.
- Используйте [SuppressNavigationTransitionInfo](https://msdn.microsoft.com/en-us/library/windows/apps/windows.ui.xaml.media.animation.suppressnavigationtransitioninfo), чтобы избежать анимации перехода во **фрейме**, если применяется **ConnectedAnimationService**, поскольку подключенная анимация не предназначена для использования одновременно с переходами навигации по умолчанию. Дополнительную информацию об использовании переходов навигации см. [NavigationThemeTransition](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.media.animation.navigationthemetransition.aspx).


## <a name="download-the-code-samples"></a>Загрузка примеров кода

См. [примеры подключенной анимации](https://github.com/Microsoft/WindowsUIDevLabs/tree/master/SampleGallery/Samples/SDK%2014393/ConnectedAnimationSample) в коллекции примеров [WindowsUIDevLabs](https://github.com/Microsoft/WindowsUIDevLabs).

## <a name="related-articles"></a>Статьи по теме

- [ConnectedAnimation](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.media.animation.connectedanimation)
- [ConnectedAnimationService](https://msdn.microsoft.com/en-us/library/windows/apps/windows.ui.xaml.media.animation.connectedanimation.aspx)
- [NavigationThemeTransition](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.media.animation.navigationthemetransition.aspx)

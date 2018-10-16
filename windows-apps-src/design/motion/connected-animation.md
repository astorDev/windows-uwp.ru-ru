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
ms.openlocfilehash: 31e940c87626a05ee6911d3ffda36ab8dfd3fad0
ms.sourcegitcommit: 106aec1e59ba41aae2ac00f909b81bf7121a6ef1
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/15/2018
ms.locfileid: "4616187"
---
# <a name="connected-animation-for-uwp-apps"></a>Подключенная анимация для приложений UWP

Подключенные анимации позволяют сделать навигацию динамичной и наглядной, анимируя переход элемента от одного представления к другому. Это помогает пользователю сохранять контекст и обеспечивает преемственность представлений.

В случае подключенной анимации элемент отображается «продолжить» между двумя представлениями во время смены содержимого пользовательского интерфейса, двигаясь по экрану от места расположения в исходном представлении до места назначения в новом. Это подчеркивает содержимого между представлениями и создает прекрасный динамический эффект в рамках перехода.

> **Важные API -интерфейсы**: [ConnectedAnimation класс](/uwp/api/windows.ui.xaml.media.animation.connectedanimation), [класс ConnectedAnimationService](/uwp/api/windows.ui.xaml.media.animation.connectedanimationservice)

## <a name="see-it-in-action"></a>Подключенная анимация в действии

В этом коротком видео приложение использует подключенную анимацию, чтобы анимировать изображение элемента, так как «продолжается» становится частью заголовка следующей страницы. Этот эффект помогает сохранить контекст пользователя во время перехода.

![Подключенная анимация](images/connected-animations/example.gif)

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

## <a name="configure-connected-animation"></a>Настройка подключенной анимации

> [!IMPORTANT]
> Эта функция также требуется целевую версию вашего приложения RS5 (Windows SDK версии 10.0.NNNNN.0 (Windows 10 версии YYMM) или более поздней версии. Свойства конфигурации недоступен в более ранних версий пакетов SDK. Вы можете выбрать минимальной версией ниже, чем RS5 (10.0.NNNNN.0 (Windows 10, версии YYMM) версии пакета SDK для Windows с помощью адаптивного кода или условного XAML. Дополнительные сведения см. в разделе [адаптивные к версии приложения](/debug-test-perf/version-adaptive-apps).

Начиная с версии RS5, подключенные анимации дополнительно включающих проектирования Fluent, предоставляя анимации конфигураций, адаптированных специально для перемещения вперед и назад навигации между страницами.

Укажите XML конфигурации анимации, задав свойство конфигурации на ConnectedAnimation. (Мы покажем в качестве примера в следующем разделе.)

В этой таблице описаны доступные конфигурации. Дополнительные сведения о принципах движения, применяется в эти анимации см. в разделе [направление и сила притяжения](index.md).

| [GravityConnectedAnimationConfiguration]() |
| - |
| Это конфигурация по умолчанию и рекомендуется для переходов вперед. |
Когда пользователь переходит вперед в приложении (A-B), подключенных элемент отображается способы физически» на странице». Соответственно, элемент отображается для перемещения вперед в z пространстве и немного опускается как эффект выполнения удержание сила притяжения. Разработчики эффекты силы тяжести, элемент получает скорости и ускоряет в его конечное расположение. В результате получается анимацию «scale и dip». |

| [DirectConnectedAnimationConfiguration]() |
| - |
| Когда пользователь переходит назад в приложении (B, а), более прямолинейно анимации. Элемент подключенных линейно преобразуется из B замедлением кривая Безье третьего порядка функции для реалистичной анимации с помощью. Обратной визуальная возможность пользователь возвращается в предыдущее состояние как можно скорее сохраняя при этом контексте потока навигации. |

| [BasicConnectedAnimationConfiguration]() |
| - |
| Это значение по умолчанию (и только) анимации, используемые в версиях SDK до RS5 (Windows SDK версии 10.0.NNNNN.0 (Windows 10 версии YYMM). |

### <a name="connectedanimationservice-configuration"></a>ConnectedAnimationService конфигурации

Класс [ConnectedAnimationService](/uwp/api/windows.ui.xaml.media.animation.connectedanimationservice) имеет два свойства, которые применяются к отдельных анимаций, а не общую службу.

- [DefaultDuration](/uwp/api/windows.ui.xaml.media.animation.connectedanimationservice.defaultduration)
- [DefaultEasingFunction](/uwp/api/windows.ui.xaml.media.animation.connectedanimationservice.defaulteasingfunction)

Для достижения разных эффектов, в некоторых конфигурациях игнорировать этих свойств ConnectedAnimationService и использовать свои собственные значения, как описано в следующей таблице.

| Настройка | DefaultDuration отношениях? | DefaultEasingFunction отношениях? |
| - | - | - |
| Сила тяготения | Да | Да* <br/> **Базовый перевод от A к B использует эту функцию для реалистичной анимации, но «dip силы тяжести» имеет собственные функции для реалистичной анимации.*  |
| Прямой | Нет <br/> *Анимируется более 150 мс.*| Нет <br/> *Использует замедлением, функция для реалистичной анимации.* |
| Базовые сведения | Да | Да |

## <a name="how-to-implement-connected-animation"></a>Как реализовать подключенной анимации

Настройка подключенной анимации включает два этапа:

1. *Подготовка* объекта анимации на исходной странице, которая показывает системе, что исходный элемент участвует в подключенной анимации.
1. *Запустите* анимацию на странице назначения передачей ссылки элементу назначения.

При переходе на исходной странице, вызовите метод [ConnectedAnimationService.GetForCurrentView](/uwp/api/windows.ui.xaml.media.animation.connectedanimationservice.getforcurrentview) , чтобы получить экземпляр класса ConnectedAnimationService. Чтобы подготовить анимацию, вызовите [PrepareToAnimate](/uwp/api/windows.ui.xaml.media.animation.connectedanimationservice.preparetoanimate) в данном экземпляре и передайте в уникальный ключ и элемент пользовательского интерфейса, которые вы хотите использовать во время перехода. Уникальный ключ позволяет получить анимацию позже на странице назначения.

```csharp
ConnectedAnimationService.GetForCurrentView()
    .PrepareToAnimate("forwardAnimation", SourceImage);
```

Когда происходит переход, запустите анимацию на странице назначения. Чтобы запустить анимацию, вызовите [ConnectedAnimation.TryStart](/uwp/api/windows.ui.xaml.media.animation.connectedanimation.trystart). Правильный экземпляр анимации можно извлечь, вызвав [ConnectedAnimationService.GetAnimation](/uwp/api/windows.ui.xaml.media.animation.connectedanimationservice.getanimation) с помощью уникального ключа, который вы предоставили при создании анимации.

```csharp
ConnectedAnimation animation =
    ConnectedAnimationService.GetForCurrentView().GetAnimation("forwardAnimation");
if (animation != null)
{
    animation.TryStart(DestinationImage);
}
```

### <a name="forward-navigation"></a>Переходов вперед

В этом примере показано, как использовать ConnectedAnimationService для создания перехода вперед навигации между двумя страницами (Page_A для Page_B).

Рекомендованная анимация переходов вперед отдает [GravityConnectedAnimationConfiguration](). Это значение по умолчанию, поэтому вам не нужно задавать свойство [конфигурации](/uwp/api/windows.ui.xaml.media.animation.connectedanimation.configuration) , если это не требуется указать различные конфигурации.

Настройка анимации на исходной странице.

```xaml
<!-- Page_A.xaml -->

<Image x:Name="SourceImage"
       HorizontalAlignment="Left" VerticalAlignment="Top"
       Width="200" Height="200"
       Stretch="Fill"
       Source="Assets/StoreLogo.png"
       PointerPressed="SourceImage_PointerPressed"/>
```

```csharp
// Page_A.xaml.cs

private void SourceImage_PointerPressed(object sender, PointerRoutedEventArgs e)
{
    // Navigate to detail page.
    // Suppress the default animation to avoid conflict with the connected animation.
    Frame.Navigate(typeof(Page_B), null, new SuppressNavigationTransitionInfo());
}

protected override void OnNavigatingFrom(NavigatingCancelEventArgs e)
{
    ConnectedAnimationService.GetForCurrentView()
        .PrepareToAnimate("forwardAnimation", SourceImage);
    // You don't need to explicitly set the Configuration property because
    // the recommended Gravity configuration is default.
    // For custom animation, use:
    // animation.Configuration = new BasicConnectedAnimationConfiguration();
}
```

Запустите анимацию на странице назначения.

```xaml
<!-- Page_B.xaml -->

<Image x:Name="DestinationImage"
       Width="400" Height="400"
       Stretch="Fill"
       Source="Assets/StoreLogo.png" />
```

```csharp
// Page_B.xaml.cs

protected override void OnNavigatedTo(NavigationEventArgs e)
{
    base.OnNavigatedTo(e);

    ConnectedAnimation animation =
        ConnectedAnimationService.GetForCurrentView().GetAnimation("forwardAnimation");
    if (animation != null)
    {
        animation.TryStart(DestinationImage);
    }
}
```

### <a name="back-navigation"></a>Обратной навигации

Для переходов назад (Page_B для Page_A), выполните те же действия, но отменяются страниц источника и назначения.

Когда пользователь переходит назад, они ожидают приложение возвращается к предыдущему состоянию, как можно скорее. Поэтому рекомендуется использовать является [DirectConnectedAnimationConfiguration](). Эта анимация быстрее, более прямолинейно и замедлением в анимации.

Настройка анимации на исходной странице.

```csharp
// Page_B.xaml.cs

protected override void OnNavigatingFrom(NavigatingCancelEventArgs e)
{
    if (e.NavigationMode == NavigationMode.Back)
    {
        ConnectedAnimationService.GetForCurrentView()
            .PrepareToAnimate("backAnimation", DestinationImage);

        // Use the recommended configuration for back animation.
        animation.Configuration = new DirectConnectedAnimationConfiguration();
    }
}
```

Запустите анимацию на странице назначения.

```csharp
// Page_A.xaml.cs

protected override void OnNavigatedTo(NavigationEventArgs e)
{
    base.OnNavigatedTo(e);

    ConnectedAnimation animation =
        ConnectedAnimationService.GetForCurrentView().GetAnimation("backAnimation");
    if (animation != null)
    {
        animation.TryStart(SourceImage);
    }
}
```

Между временем, Настройка анимации и при запуске исходный элемент отображается зависшим над другими элементами пользовательского интерфейса приложения. Это позволяет одновременно выполнять другие анимации перехода. По этой причине не стоит ждать дольше 250 миллисекунд этапами, поскольку наличие исходного элемента может отвлекать. Если анимация подготовлена, но не запущена в течение трех секунд, она удаляется из системы и любые последующие вызовы [TryStart](/uwp/api/windows.ui.xaml.media.animation.connectedanimation.trystart) будут отклонены.

## <a name="connected-animation-in-list-and-grid-experiences"></a>Подключенная анимация при использовании списка и сетки

Часто нужно будет использовать подключенную анимацию для перехода к элементу управления списком или сеткой либо от него. [ListView](/uwp/api/windows.ui.xaml.controls.listview) и [GridView](/uwp/api/windows.ui.xaml.controls.gridview), [PrepareConnectedAnimation](/uwp/api/windows.ui.xaml.controls.listviewbase.prepareconnectedanimation) и [TryStartConnectedAnimationAsync](/uwp/api/windows.ui.xaml.controls.listviewbase.trystartconnectedanimationasync), можно использовать два метода для упрощения этого процесса.

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

Чтобы подготовить подключенную анимацию с данным элементом списка, вызовите метод [PrepareConnectedAnimation](/uwp/api/windows.ui.xaml.controls.listviewbase.prepareconnectedanimation) с уникальный ключ, элемент и имя «PortraitEllipse».

```csharp
void PrepareAnimationWithItem(ContactsItem item)
{
     ContactsListView.PrepareConnectedAnimation("portrait", item, "PortraitEllipse");
}
```

Чтобы запустить анимацию с помощью этого элемента в качестве цели, например при переходе "Назад" из представления подробных сведений, используйте [TryStartConnectedAnimationAsync](/uwp/api/windows.ui.xaml.controls.listviewbase.trystartconnectedanimationasync). Если вы только что загрузили источник данных для ListView, TryStartConnectedAnimationAsync не запустит анимацию, пока не будет создан соответствующий контейнер элементов.

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

*Согласованная анимация* — это специальный тип анимационный где элемента отображается вместе с целью подключенной анимации, анимацию в сочетании с элементом подключенной анимации при его перемещении по экрану. Согласованная анимация может сделать переход визуально интереснее и привлечь внимание пользователя к контексту, общему для исходного и целевого представлений. На этих изображениях интерфейс подписи элемента анимирует использование согласованной анимации.

Если согласованной анимации используется конфигурация силы тяжести, сила притяжения применяется к элементом подключенной анимации и согласованные элементы. Согласованные элементы будут «swoop» вместе с подключенного элемента Чтобы действительно скоординированы элементов.

Используйте перегрузку двух параметров **TryStart**, чтобы добавить согласованные элементы в подключенную анимацию. Этот пример показывает согласованную анимацию макета сетки с именем «DescriptionRoot», который входит в сочетании с элементом подключенной анимации «Coverimage».

```xaml
<!-- DestinationPage.xaml -->
<Grid>
    <Image x:Name="CoverImage" />
    <Grid x:Name="DescriptionRoot" />
</Grid>
```

```csharp
// DestinationPage.xaml.cs
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
- Используйте [GravityConnectedAnimationConfiguration]() для переходов вперед.
- Используйте [DirectConnectedAnimationConfiguration]() для обратной навигации.
- Не ждите сетевые запросы или другие долго выполняющиеся асинхронные операции между подготовкой и запуском подключенной анимации. Может потребоваться предварительно загрузить данные, необходимые для выполнения перехода, или использовать изображение-заполнитель с низким разрешением во время загрузки в представлении назначения изображения с высоким разрешением.
- Используйте [SuppressNavigationTransitionInfo](/uwp/api/windows.ui.xaml.media.animation.suppressnavigationtransitioninfo) , чтобы избежать анимации перехода в **кадр** , если вы используете **ConnectedAnimationService**, поскольку подключенная анимация не предназначена для использования одновременно с навигацией по умолчанию переходы. Дополнительную информацию об использовании переходов навигации см. [NavigationThemeTransition](/uwp/api/Windows.UI.Xaml.Media.Animation.NavigationThemeTransition).

## <a name="download-the-code-samples"></a>Загрузка примеров кода

См. [примеры подключенной анимации](https://github.com/Microsoft/WindowsUIDevLabs/tree/master/SampleGallery/Samples/SDK%2014393/ConnectedAnimationSample) в коллекции примеров [WindowsUIDevLabs](https://github.com/Microsoft/WindowsUIDevLabs).

## <a name="related-articles"></a>Статьи по теме

[ConnectedAnimation](/uwp/api/windows.ui.xaml.media.animation.connectedanimation)

[ConnectedAnimationService](/uwp/api/windows.ui.xaml.media.animation.connectedanimationservice)

[NavigationThemeTransition](/uwp/api/Windows.UI.Xaml.Media.Animation.NavigationThemeTransition)

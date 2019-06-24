---
description: Подключенные анимации позволяют сделать навигацию динамичной и наглядной, анимируя переход элемента от одного представления к другому.
title: Подключенная анимация
template: detail.hbs
ms.date: 10/04/2018
ms.topic: article
keywords: windows 10, uwp
pm-contact: stmoy
design-contact: conrwi
doc-status: Published
ms.localizationpriority: medium
ms.openlocfilehash: f448f481b2b55a42cbaa158cc4b07261e2d7717b
ms.sourcegitcommit: 6f32604876ed480e8238c86101366a8d106c7d4e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/21/2019
ms.locfileid: "67317138"
---
# <a name="connected-animation-for-uwp-apps"></a>Подключенная анимация для приложений UWP

Подключенные анимации позволяют сделать навигацию динамичной и наглядной, анимируя переход элемента от одного представления к другому. Это помогает пользователю сохранять контекст и обеспечивает преемственность представлений.

В подключенных анимации элемент отображается на «continue» между двумя представлениями во время изменения в содержимое пользовательского интерфейса, в движении по экрану с места в представлении источника в место назначения в новое представление. Это обычное содержимое между представлениями, создавая эффект привлекательных и динамических в процессе перехода.

> **Важные API**:  [Класс ConnectedAnimation](/uwp/api/windows.ui.xaml.media.animation.connectedanimation), [ConnectedAnimationService-класс](/uwp/api/windows.ui.xaml.media.animation.connectedanimationservice)


## <a name="examples"></a>Примеры

<table>
<th align="left">Галерея элементов управления XAML<th>
<tr>
<td><img src="images/xaml-controls-gallery-app-icon.png" alt="XAML controls gallery" width="168"></img></td>
<td>
    <p>Если у вас есть <strong style="font-weight: semi-bold">коллекции элементов управления XAML</strong> приложения. Щелкните здесь, чтобы <a href="xamlcontrolsgallery:/item/ConnectedAnimation">откройте приложение и увидеть подключенные анимации в действии</a>.</p>
    <ul>
    <li><a href="https://www.microsoft.com/p/xaml-controls-gallery/9msvh128x2zt">Получить приложение XAML Controls Gallery (Microsoft Store)</a></li>
    <li><a href="https://github.com/Microsoft/Xaml-Controls-Gallery">Получить исходный код (GitHub)</a></li>
    </ul>
</td>
</tr>
</table>

В этом коротком видеоролике приложение использует подключенные анимации для анимации элемента изображения по мере его «» в заголовке следующей страницы. Этот эффект помогает сохранить контекст пользователя во время перехода.

![Подключенная анимация](images/connected-animations/example.gif)

<!-- 
<iframe width=640 height=360 src='https://microsoft.sharepoint.com/portals/hub/_layouts/15/VideoEmbedHost.aspx?chId=552c725c%2De353%2D4118%2Dbd2b%2Dc2d0584c9848&amp;vId=b2daa5ee%2Dbe15%2D4503%2Db541%2D1328a6587c36&amp;width=640&amp;height=360&amp;autoPlay=false&amp;showInfo=true' allowfullscreen></iframe>
-->

## <a name="video-summary"></a>Видео с кратким описанием

> [!VIDEO https://channel9.msdn.com/Events/Windows/Windows-Developer-Day-Fall-Creators-Update/WinDev005/player]

## <a name="connected-animation-and-the-fluent-design-system"></a>Подключенная анимация и система проектирования Fluent Design

 Система Fluent Design позволяет создавать современные и эффективные пользовательские интерфейсы, которые отличаются яркостью, глубиной, движением, материальностью и масштабированием. Подключенная анимация — это компонент системы проектирования Fluent Design, добавляющий движение в ваше приложение. Дополнительные сведения см. в разделе [Обзор системы проектирования Fluent Design для UWP](/windows/apps/fluent-design-system).

## <a name="why-connected-animation"></a>Преимущества подключенной анимации

При переходе с одной страницы на другую пользователю важно понимать, какое новое содержимое представлено после перехода и как оно соотносится с целью перехода. Подключенная анимация — это яркая визуальная аналогия, которая подчеркивает связь между двумя представлениями, привлекая внимание пользователя к их общему содержимому. Кроме того, благодаря подключенной анимации навигация страницы выглядит интереснее и профессиональнее, выделяя ваше приложение благодаря проработке движения.

## <a name="when-to-use-connected-animation"></a>Сценарии использования подключенной анимации

Подключенные анимации обычно используются при переходе на другую страницу, хотя их можно применять при любой смене контента в пользовательском интерфейсе, если нужно сохранить контекст пользователя. Рекомендуем использовать подключенную анимацию вместо [эффекта винта в переходах навигации](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.Animation.NavigationThemeTransition), если исходное и целевое представления содержат общее изображение или другой элемент пользовательского интерфейса.

## <a name="configure-connected-animation"></a>Настройка подключенных анимации

> [!IMPORTANT]
> Эта функция требует вашего приложения целевой версии Windows 10, версия 1809 ([SDK 17763](https://developer.microsoft.com/windows/downloads/windows-10-sdk)) или более поздней версии. Свойство конфигурации недоступен в более ранних пакетов SDK. Минимальная версия, ниже, чем SDK 17763 можно назначить с помощью адаптивного кода или условного XAML. Дополнительные сведения см. в разделе [адаптивные приложения версии](/windows/uwp/debug-test-perf/version-adaptive-apps).

Начиная с Windows 10, версия 1809, включающих дальнейшей подключенные анимации Fluent разработки, предоставляя анимации конфигураций адаптированные специально для вперед и назад по страницам.

Указывается конфигурация, анимации, задав свойство конфигурации на ConnectedAnimation. (В качестве примера будет показано в следующем разделе.)

Эта таблица описывает доступные конфигурации. Дополнительные сведения о принципах движения, применяются в эти анимации см. в разделе [гравитации и принятый](index.md).

| [GravityConnectedAnimationConfiguration](/uwp/api/windows.ui.xaml.media.animation.gravityconnectedanimationconfiguration) |
| - |
| Это является конфигурацией по умолчанию и рекомендуется для перехода вперед. |
Когда пользователь переходит назад в приложение (от A до B), подключенных элемент появляется в том, чтобы физически «pull за пределы страницы». Таким образом, элемент отображается перемещение вперед в z пространстве и удаляет немного как эффект тяжести принимая удержание. Чтобы преодолеть него действует Гравитация, элемент набирает скорость и ускоряет в его последней позицией. Результатом является анимированный значок «масштаб и DIP-адрес». |

| [DirectConnectedAnimationConfiguration](/uwp/api/windows.ui.xaml.media.animation.directconnectedanimationconfiguration) |
| - |
| Когда пользователь переходит назад в приложении (B до A), более прямой анимации. Элемент подключенных линейно преобразуется из B decelerate кубические Безье функции плавности с помощью. Обратной visual affordance пользователь возвращается к предыдущим значениям максимально возможной скоростью сохраняя при этом контекст потока навигации. |

| [BasicConnectedAnimationConfiguration](/uwp/api/windows.ui.xaml.media.animation.basicconnectedanimationconfiguration) |
| - |
| Это значение по умолчанию (только и) анимация, которая используется в версиях, предшествующих Windows 10, версия 1809 ([SDK 17763](https://developer.microsoft.com/windows/downloads/windows-10-sdk)). |

### <a name="connectedanimationservice-configuration"></a>ConnectedAnimationService конфигурации

[ConnectedAnimationService](/uwp/api/windows.ui.xaml.media.animation.connectedanimationservice) класс имеет два свойства, которые применяются к отдельным анимации, а не общую службу.

- [DefaultDuration](/uwp/api/windows.ui.xaml.media.animation.connectedanimationservice.defaultduration)
- [DefaultEasingFunction](/uwp/api/windows.ui.xaml.media.animation.connectedanimationservice.defaulteasingfunction)

Для достижения различных эффектов, некоторые конфигурации игнорировать эти свойства на ConnectedAnimationService и использовать собственные значения, как описано в этой таблице.

| Конфигурация | DefaultDuration отношениях? | DefaultEasingFunction отношениях? |
| - | - | - |
| Сила тяготения | Да | Да* <br/> **Основные перевод от A до B использует эту функцию плавности, но «gravity DIP-адрес» имеет свои собственные функции плавности.*  |
| Прямой | Нет <br/> *Выполняет анимацию с более чем 150ms.*| Нет <br/> *Использует Decelerate, функция для реалистичной анимации.* |
| Базовый | Да | Да |

## <a name="how-to-implement-connected-animation"></a>Как реализовать подключенные анимации

Настройка подключенной анимации включает два этапа:

1. *Подготовка* объекта анимации на исходной странице, который указывает в системе, что исходный элемент будет участвовать в подключенные анимации.
1. *Запуск* анимации на странице назначения ссылки в конечном элементе.

При переходе из исходной страницы вызовите [ConnectedAnimationService.GetForCurrentView](/uwp/api/windows.ui.xaml.media.animation.connectedanimationservice.getforcurrentview) для получения экземпляра ConnectedAnimationService. Чтобы подготовить анимации, вызовите [PrepareToAnimate](/uwp/api/windows.ui.xaml.media.animation.connectedanimationservice.preparetoanimate) для данного экземпляра и передайте уникальным ключом и вы хотите использовать при переходе элемент пользовательского интерфейса. Уникальный ключ позволяет извлекать анимации позже на странице назначения.

```csharp
ConnectedAnimationService.GetForCurrentView()
    .PrepareToAnimate("forwardAnimation", SourceImage);
```

При переходе, запуска анимации на странице назначения. Чтобы запустить анимацию, вызовите [ConnectedAnimation.TryStart](/uwp/api/windows.ui.xaml.media.animation.connectedanimation.trystart). Правильный экземпляр анимации можно извлечь, вызвав [ConnectedAnimationService.GetAnimation](/uwp/api/windows.ui.xaml.media.animation.connectedanimationservice.getanimation) с помощью уникального ключа, который вы предоставили при создании анимации.

```csharp
ConnectedAnimation animation =
    ConnectedAnimationService.GetForCurrentView().GetAnimation("forwardAnimation");
if (animation != null)
{
    animation.TryStart(DestinationImage);
}
```

### <a name="forward-navigation"></a>Переходов вперед

В этом примере показано, как использовать ConnectedAnimationService, чтобы создать переход для перехода вперед между двумя страницами (Page_A для Page_B).

Конфигурация рекомендуемые анимации для переходов вперед [GravityConnectedAnimationConfiguration](/uwp/api/windows.ui.xaml.media.animation.gravityconnectedanimationconfiguration). Это значение по умолчанию, поэтому не нужно задавать [конфигурации](/uwp/api/windows.ui.xaml.media.animation.connectedanimation.configuration) свойства только если вы хотите указать другую конфигурацию.

Создать анимацию на исходной странице.

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

Запуск анимации в странице назначения.

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

### <a name="back-navigation"></a>Переходов назад

Для переходов назад (Page_B для Page_A), выполните те же действия, но страниц источника и назначения находятся в обратном порядке.

Когда пользователь переходит назад, они ожидают приложение возвращается к предыдущему состоянию, как можно скорее. Таким образом, рекомендуемая конфигурация — [DirectConnectedAnimationConfiguration](/uwp/api/windows.ui.xaml.media.animation.directconnectedanimationconfiguration). Эта анимация быстрее, более прямой и использует decelerate реалистичной анимации.

Создать анимацию на исходной странице.

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

Запуск анимации в странице назначения.

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

Между временем, которая настраивается анимация, и при запуске исходного элемента застывает выше другой пользовательский Интерфейс в приложении. Это позволяет одновременно выполнять любые другие анимации перехода. По этой причине не следует ждать более около 250 миллисекунд между двумя действия из-за наличия исходного элемента может стать отвлекает. Если анимация подготовлена, но не запущена в течение трех секунд, она удаляется из системы и любые последующие вызовы [TryStart](/uwp/api/windows.ui.xaml.media.animation.connectedanimation.trystart) будут отклонены.

## <a name="connected-animation-in-list-and-grid-experiences"></a>Подключенная анимация при использовании списка и сетки

Часто нужно будет использовать подключенную анимацию для перехода к элементу управления списком или сеткой либо от него. Можно использовать два метода на [ListView](/uwp/api/windows.ui.xaml.controls.listview) и [GridView](/uwp/api/windows.ui.xaml.controls.gridview), [PrepareConnectedAnimation](/uwp/api/windows.ui.xaml.controls.listviewbase.prepareconnectedanimation) и [TryStartConnectedAnimationAsync](/uwp/api/windows.ui.xaml.controls.listviewbase.trystartconnectedanimationasync), Чтобы упростить этот процесс.

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

Чтобы подготовить подключенные анимации с эллипсом, соответствующий данного элемента списка, вызовите [PrepareConnectedAnimation](/uwp/api/windows.ui.xaml.controls.listviewbase.prepareconnectedanimation) метода с уникальным ключом элемента и имя «PortraitEllipse».

```csharp
void PrepareAnimationWithItem(ContactsItem item)
{
     ContactsListView.PrepareConnectedAnimation("portrait", item, "PortraitEllipse");
}
```

Для запуска анимации с помощью этого элемента в качестве назначения, например переход обратно при подробное представление, использовать [TryStartConnectedAnimationAsync](/uwp/api/windows.ui.xaml.controls.listviewbase.trystartconnectedanimationasync). Если вы только что источник данных для ListView, TryStartConnectedAnimationAsync будет ожидать начала анимации, пока будет создан соответствующий контейнер элементов.

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

Объект *координируемых анимации* — это специальный тип, вступительной анимации, где отображается элемент вместе с цели подключенные анимации, анимации в сочетании с элементом подключенные анимации при их перемещении по экрану. Согласованная анимация может сделать переход визуально интереснее и привлечь внимание пользователя к контексту, общему для исходного и целевого представлений. На этих изображениях интерфейс подписи элемента анимирует использование согласованной анимации.

При скоординированной анимация использует конфигурацию гравитации, gravity применяется элемент подключенные анимации и скоординированной элементам. Скоординированной элементов будет «swoop» наряду с подключенной элемент, элементы остаются выровненными по-настоящему согласованной.

Используйте перегрузку двух параметров **TryStart**, чтобы добавить согласованные элементы в подключенную анимацию. В этом примере демонстрируется скоординированной анимация объекта с именем «DescriptionRoot», помещаемого в тандеме с элементом подключенные анимации с именем «CoverImage» макет сетки.

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
- Используйте [GravityConnectedAnimationConfiguration](/uwp/api/windows.ui.xaml.media.animation.gravityconnectedanimationconfiguration) для перехода вперед.
- Используйте [DirectConnectedAnimationConfiguration](/uwp/api/windows.ui.xaml.media.animation.directconnectedanimationconfiguration) для резервного перехода.
- Не ждите, на сетевые запросы или других долго выполняющихся асинхронных операций между Подготовка и запуск подключенные анимации. Может потребоваться предварительно загрузить данные, необходимые для выполнения перехода, или использовать изображение-заполнитель с низким разрешением во время загрузки в представлении назначения изображения с высоким разрешением.
- Используйте [SuppressNavigationTransitionInfo](/uwp/api/windows.ui.xaml.media.animation.suppressnavigationtransitioninfo) для предотвращения анимации перехода в **кадра** при использовании **ConnectedAnimationService**, так как подключенные анимации не рассчитаны на могут использоваться одновременно с переходами навигации по умолчанию. Дополнительную информацию об использовании переходов навигации см. [NavigationThemeTransition](/uwp/api/Windows.UI.Xaml.Media.Animation.NavigationThemeTransition).

## <a name="related-articles"></a>Связанные статьи

[ConnectedAnimation](/uwp/api/windows.ui.xaml.media.animation.connectedanimation)

[ConnectedAnimationService](/uwp/api/windows.ui.xaml.media.animation.connectedanimationservice)

[NavigationThemeTransition](/uwp/api/Windows.UI.Xaml.Media.Animation.NavigationThemeTransition)

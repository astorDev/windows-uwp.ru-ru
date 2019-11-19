---
description: Подключенные анимации позволяют сделать навигацию динамичной и наглядной, анимируя переход элемента от одного представления к другому.
title: Подключенная анимация
template: detail.hbs
ms.date: 10/04/2018
ms.topic: article
keywords: windows 10, uwp
pm-contact: stmoy
design-contact: conrwi
doc-status: Published
ms.localizationpriority: medium
ms.openlocfilehash: 6e17b1c18fc8e643ac788e5e13ac78cae49a35ef
ms.sourcegitcommit: 6d743cf9c3e09f87ea2879b8e1f2dc4a1b1a16fe
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/19/2019
ms.locfileid: "74166080"
---
# <a name="connected-animation-for-uwp-apps"></a>Подключенная анимация для приложений UWP

Подключенные анимации позволяют сделать навигацию динамичной и наглядной, анимируя переход элемента от одного представления к другому. Это помогает пользователю сохранять контекст и обеспечивает преемственность представлений.

В подключенной анимации элемент отображается как «продолжить» между двумя представлениями во время изменения содержимого пользовательского интерфейса, перелетая экран из его расположения в представлении исходного кода в его место назначения в новом представлении. Это подчеркивает общее содержимое между представлениями и создает прекрасный и динамический эффект в рамках перехода.

> **Важные API**: [класс коннектеданиматион](/uwp/api/windows.ui.xaml.media.animation.connectedanimation), [класс коннектеданиматионсервице](/uwp/api/windows.ui.xaml.media.animation.connectedanimationservice)


## <a name="examples"></a>Примеры.

<table>
<th align="left">XAML Controls Gallery<th>
<tr>
<td><img src="images/xaml-controls-gallery-app-icon.png" alt="XAML controls gallery" width="168"></img></td>
<td>
    <p>Если приложение <strong style="font-weight: semi-bold">коллекции элементов управления XAML</strong> установлено, щелкните здесь, чтобы <a href="xamlcontrolsgallery:/item/ConnectedAnimation">открыть приложение и просмотреть значок подключенная анимация в действии</a>.</p>
    <ul>
    <li><a href="https://www.microsoft.com/p/xaml-controls-gallery/9msvh128x2zt">Получить приложение XAML Controls Gallery (Microsoft Store)</a></li>
    <li><a href="https://github.com/Microsoft/Xaml-Controls-Gallery">Получить исходный код (GitHub)</a></li>
    </ul>
</td>
</tr>
</table>

В этом коротком видео приложение использует подключенную анимацию для анимации изображения элемента, так как "продолжение" становится частью заголовка следующей страницы. Этот эффект помогает сохранить контекст пользователя во время перехода.

![Подключенная анимация](images/connected-animations/example.gif)

<!-- 
<iframe width=640 height=360 src='https://microsoft.sharepoint.com/portals/hub/_layouts/15/VideoEmbedHost.aspx?chId=552c725c%2De353%2D4118%2Dbd2b%2Dc2d0584c9848&amp;vId=b2daa5ee%2Dbe15%2D4503%2Db541%2D1328a6587c36&amp;width=640&amp;height=360&amp;autoPlay=false&amp;showInfo=true' allowfullscreen></iframe>
-->

## <a name="video-summary"></a>Краткий видеообзор

> [!VIDEO https://channel9.msdn.com/Events/Windows/Windows-Developer-Day-Fall-Creators-Update/WinDev005/player]

## <a name="connected-animation-and-the-fluent-design-system"></a>Подключенная анимация и система проектирования Fluent Design

 Система Fluent Design позволяет создавать современные и эффективные пользовательские интерфейсы, которые отличаются яркостью, глубиной, движением, материальностью и масштабированием. Подключенная анимация — это компонент системы проектирования Fluent Design, добавляющий движение в ваше приложение. Дополнительные сведения см. в [обзоре системы Fluent Design для UWP](/windows/apps/fluent-design-system).

## <a name="why-connected-animation"></a>Преимущества подключенной анимации

При переходе с одной страницы на другую пользователю важно понимать, какое новое содержимое представлено после перехода и как оно соотносится с целью перехода. Подключенная анимация — это яркая визуальная аналогия, которая подчеркивает связь между двумя представлениями, привлекая внимание пользователя к их общему содержимому. Кроме того, благодаря подключенной анимации навигация страницы выглядит интереснее и профессиональнее, выделяя ваше приложение благодаря проработке движения.

## <a name="when-to-use-connected-animation"></a>Сценарии использования подключенной анимации

Подключенные анимации обычно используются при переходе на другую страницу, хотя их можно применять при любой смене контента в пользовательском интерфейсе, если нужно сохранить контекст пользователя. Рекомендуем использовать подключенную анимацию вместо [эффекта винта в переходах навигации](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.Animation.NavigationThemeTransition), если исходное и целевое представления содержат общее изображение или другой элемент пользовательского интерфейса.

## <a name="configure-connected-animation"></a>Настройка подключенной анимации

> [!IMPORTANT]
> Для этого компонента требуется, чтобы Целевая версия приложения была Windows 10, версия 1809 ([SDK 17763](https://developer.microsoft.com/windows/downloads/windows-10-sdk)) или более поздней версии. Свойство конфигурации недоступно в предыдущих пакетах SDK. Вы можете ориентироваться на минимальную версию, меньшую, чем SDK 17763, используя адаптивный код или условный XAML. Дополнительные сведения см. в разделе [адаптивные версии приложений](/windows/uwp/debug-test-perf/version-adaptive-apps).

Начиная с Windows 10, версия 1809, подключенная анимация Дополнительно реализуют Fluent, предоставляя конфигурации анимации, специально предназначенные для перемещения вперед и обратной навигации по страницам.

Вы указываете конфигурацию анимации, задав свойство Configuration для Коннектеданиматион. (Эти примеры будут показаны в следующем разделе.)

В этой таблице описаны доступные конфигурации. Дополнительные сведения о принципах движения, применяемых в этих анимациях, см. в разделе [направленность и сила притяжения](index.md).

| [GravityConnectedAnimationConfiguration](/uwp/api/windows.ui.xaml.media.animation.gravityconnectedanimationconfiguration) |
| - |
| Это конфигурация по умолчанию, и ее рекомендуется использовать для прямой навигации. |
По мере того, как пользователь переходит вперед в приложении (A в B), подключенный элемент физически «извлекается со страницы». При этом элемент перемещается вперед в z-пространстве и отбрасывается в результате удержания силы притяжения. Чтобы преодолеть последствия падения притяжения, элемент получает скорость и ускоряется до конечной должности. Результатом является анимация "Scale and DIP". |

| [DirectConnectedAnimationConfiguration](/uwp/api/windows.ui.xaml.media.animation.directconnectedanimationconfiguration) |
| - |
| По мере того, как пользователь переходит назад в приложении (B к), анимация является более прямой. Подключенный элемент линейно преобразует из B в объект с использованием функции плавности Безье третьего порядка. Обратная визуализация в обратном направлении возвращает пользователя к предыдущему состоянию как можно быстрее, при этом сохраняя контекст потока навигации. |

| [BasicConnectedAnimationConfiguration](/uwp/api/windows.ui.xaml.media.animation.basicconnectedanimationconfiguration) |
| - |
| Это только стандартная (и единственная) анимация, используемая в версиях до Windows 10, версия 1809 ([пакет SDK 17763](https://developer.microsoft.com/windows/downloads/windows-10-sdk)). |

### <a name="connectedanimationservice-configuration"></a>Конфигурация Коннектеданиматионсервице

Класс [коннектеданиматионсервице](/uwp/api/windows.ui.xaml.media.animation.connectedanimationservice) имеет два свойства, которые применяются к отдельным анимациям, а не к общей службе.

- [дефаултдуратион](/uwp/api/windows.ui.xaml.media.animation.connectedanimationservice.defaultduration)
- [дефаултеасингфунктион](/uwp/api/windows.ui.xaml.media.animation.connectedanimationservice.defaulteasingfunction)

Для достижения различных эффектов некоторые конфигурации игнорируют эти свойства в Коннектеданиматионсервице и вместо этого используют собственные значения, как описано в этой таблице.

| Настройка | Учитывает Дефаултдуратион? | Учитывает Дефаултеасингфунктион? |
| - | - | - |
| Сила тяготения | "Да", | Да* <br/> **базовый перевод из A в B использует эту функцию плавности, но "сила притяжения" имеет собственную функцию плавности.*  |
| Направлений | Нет <br/> *Выполняет анимацию по 150ms.*| Нет <br/> *Использует функцию плавности замедления.* |
| Базовый | "Да", | "Да", |

## <a name="how-to-implement-connected-animation"></a>Реализация подключенной анимации

Настройка подключенной анимации включает два этапа:

1. *Подготовка* объекта анимации на исходной странице, которая указывает системе, что элемент источника будет участвовать в подключенной анимации.
1. *Запустите* анимацию на странице назначения, передав ссылку на элемент назначения.

При переходе с исходной страницы вызовите метод [коннектеданиматионсервице. жетфоркуррентвиев](/uwp/api/windows.ui.xaml.media.animation.connectedanimationservice.getforcurrentview) , чтобы получить экземпляр коннектеданиматионсервице. Чтобы подготовить анимацию, вызовите [препаретоанимате](/uwp/api/windows.ui.xaml.media.animation.connectedanimationservice.preparetoanimate) в этом экземпляре и передайте уникальный ключ и элемент пользовательского интерфейса, который требуется использовать при переходе. Уникальный ключ позволяет получить анимацию позже на странице назначения.

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

### <a name="forward-navigation"></a>Переход вперед

В этом примере показано, как использовать Коннектеданиматионсервице для создания перехода для прямой навигации между двумя страницами (Page_A для Page_B).

Рекомендуемая конфигурация анимации для прямой навигации — [гравитиконнектеданиматионконфигуратион](/uwp/api/windows.ui.xaml.media.animation.gravityconnectedanimationconfiguration). Это значение по умолчанию, поэтому вам не нужно задавать свойство [конфигурации](/uwp/api/windows.ui.xaml.media.animation.connectedanimation.configuration) , если не требуется указывать другую конфигурацию.

Настройте анимацию на исходной странице.

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

### <a name="back-navigation"></a>Обратная Навигация

Для обратной навигации (Page_B Page_A) выполните те же действия, но исходная и целевая страницы меняются местами.

Когда пользователь переходит назад, он ожидается, чтобы приложение возвращалось в предыдущее состояние как можно скорее. Поэтому рекомендуемая конфигурация — [директконнектеданиматионконфигуратион](/uwp/api/windows.ui.xaml.media.animation.directconnectedanimationconfiguration). Эта анимация выполняется быстрее, более прямой и использует плавность замедления.

Настройте анимацию на исходной странице.

```csharp
// Page_B.xaml.cs

protected override void OnNavigatingFrom(NavigatingCancelEventArgs e)
{
    if (e.NavigationMode == NavigationMode.Back)
    {
        ConnectedAnimation animation = 
            ConnectedAnimationService.GetForCurrentView().PrepareToAnimate("backAnimation", DestinationImage);

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

Между моментом настройки анимации и ее запуском исходный элемент зафиксирован над другим пользовательским интерфейсом в приложении. Это позволяет выполнять любые другие анимации переходов одновременно. По этой причине не следует ожидать более ~ 250 миллисекунд между двумя шагами, так как присутствие исходного элемента может оказаться ненужным. Если анимация подготовлена, но не запущена в течение трех секунд, она удаляется из системы и любые последующие вызовы [TryStart](/uwp/api/windows.ui.xaml.media.animation.connectedanimation.trystart) будут отклонены.

## <a name="connected-animation-in-list-and-grid-experiences"></a>Подключенная анимация при использовании списка и сетки

Часто нужно будет использовать подключенную анимацию для перехода к элементу управления списком или сеткой либо от него. Для упрощения этого процесса можно использовать два метода в [ListView](/uwp/api/windows.ui.xaml.controls.listview) и [GridView](/uwp/api/windows.ui.xaml.controls.gridview), [препареконнектеданиматион](/uwp/api/windows.ui.xaml.controls.listviewbase.prepareconnectedanimation) и [тристартконнектеданиматионасинк](/uwp/api/windows.ui.xaml.controls.listviewbase.trystartconnectedanimationasync).

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

Чтобы подготовить подключенную анимацию с эллипсом, соответствующим заданному элементу списка, вызовите метод [препареконнектеданиматион](/uwp/api/windows.ui.xaml.controls.listviewbase.prepareconnectedanimation) с уникальным ключом, элементом и именем "портраителлипсе".

```csharp
void PrepareAnimationWithItem(ContactsItem item)
{
     ContactsListView.PrepareConnectedAnimation("portrait", item, "PortraitEllipse");
}
```

Чтобы запустить анимацию с этим элементом в качестве назначения, например при переходе назад из подробного представления, используйте [тристартконнектеданиматионасинк](/uwp/api/windows.ui.xaml.controls.listviewbase.trystartconnectedanimationasync). Если вы только что загрузили источник данных для ListView, Тристартконнектеданиматионасинк будет ожидать запуска анимации до тех пор, пока не будет создан соответствующий контейнер элементов.

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

*Координированная анимация* — это особый тип анимации входа, в которой элемент отображается вместе с целевым объектом «подключенная анимация», в сочетании с анимированным элементом «Connected Animation» в процессе перемещения по экрану. Согласованная анимация может сделать переход визуально интереснее и привлечь внимание пользователя к контексту, общему для исходного и целевого представлений. На этих изображениях интерфейс подписи элемента анимирует использование согласованной анимации.

Когда в координированной анимации используется конфигурация "сила притяжения", сила притяжения применяется как к подключенному элементу анимации, так и к скоординированным элементам. Координированные элементы будут "одновременно" рядом с подключенным элементом, чтобы элементы оставались полностью скоординированными.

Используйте перегрузку двух параметров **TryStart**, чтобы добавить согласованные элементы в подключенную анимацию. В этом примере показана скоординированная анимация макета сетки с именем "Дескриптионрут", которая входит в совместное использование с подключенным элементом анимации с именем "Коверимаже".

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
- Используйте [гравитиконнектеданиматионконфигуратион](/uwp/api/windows.ui.xaml.media.animation.gravityconnectedanimationconfiguration) для прямой навигации.
- Используйте [директконнектеданиматионконфигуратион](/uwp/api/windows.ui.xaml.media.animation.directconnectedanimationconfiguration) для обратной навигации.
- Не допускайте ожидания запросов к сети или других долгосрочных асинхронных операций в между подготовкой и запуском подключенной анимации. Может потребоваться предварительно загрузить данные, необходимые для выполнения перехода, или использовать изображение-заполнитель с низким разрешением во время загрузки в представлении назначения изображения с высоким разрешением.
- Используйте [суппресснавигатионтранситионинфо](/uwp/api/windows.ui.xaml.media.animation.suppressnavigationtransitioninfo) , чтобы предотвратить анимацию перехода в **кадре** , если используется **коннектеданиматионсервице**, так как подключенная анимация не предназначена для одновременного использования с переходами по умолчанию. Дополнительную информацию об использовании переходов навигации см. [NavigationThemeTransition](/uwp/api/Windows.UI.Xaml.Media.Animation.NavigationThemeTransition).

## <a name="related-articles"></a>Смежные разделы

[ConnectedAnimation](/uwp/api/windows.ui.xaml.media.animation.connectedanimation)

[коннектеданиматионсервице](/uwp/api/windows.ui.xaml.media.animation.connectedanimationservice)

[навигатионсеметранситион](/uwp/api/Windows.UI.Xaml.Media.Animation.NavigationThemeTransition)

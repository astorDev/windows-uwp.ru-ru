---
author: Jwmsft
Description: "Используйте модель с обновлением путем оттягивания для представления списка."
title: "Обновление путем оттягивания"
label: Pull-to-refresh
template: detail.hbs
ms.author: jimwalk
ms.date: 05/19/2017
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: windows 10, uwp
ms.assetid: aaeb1e74-b795-4015-bf41-02cb1d6f467e
pm-contact: predavid
design-contact: kimsea
dev-contact: stpete
doc-status: Published
ms.openlocfilehash: 51a8c9a2e4618e054374308918a74cf2095119ef
ms.sourcegitcommit: 10d6736a0827fe813c3c6e8d26d67b20ff110f6c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/22/2017
---
# <a name="pull-to-refresh"></a>Обновление путем оттягивания

<link rel="stylesheet" href="https://az835927.vo.msecnd.net/sites/uwp/Resources/css/custom.css"> 

Схема с обновлением путем оттягивания позволяет пользователю потянуть список данных вниз, чтобы получить дополнительные данные. Обновление путем оттягивания широко используется в мобильных приложениях, но имеет смысл только на устройствах с сенсорным экраном. Вы можете обрабатывать [события манипуляции](../input-and-devices/touch-interactions.md#manipulation-events) для реализации запроса на обновление путем оттягивания в своем приложении.

> **Важные API-интерфейсы**: [класс ListView](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.listview.aspx), [класс GridView](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.gridview.aspx)

[Пример обновления путем оттягивания](http://go.microsoft.com/fwlink/p/?LinkId=620635) демонстрирует, как дополнить элемент управления [ListView](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.listview.aspx), чтобы он поддерживал такую возможность. В этой статье мы используем этот пример для пояснения основных моментов реализации обновления путем оттягивания.

![Пример обновления путем оттягивания](images/ptr-phone-1.png)

## <a name="is-this-the-right-pattern"></a>Выбор правильного шаблона

Используйте модель с обновлением путем оттягивания при работе со списком или сеткой с данными, которые пользователю может потребоваться регулярно обновлять, и если ваше приложение предназначено для работы на мобильных устройствах с преимущественным сенсорным вводом.

## <a name="implement-pull-to-refresh"></a>Реализация обновления путем оттягивания

Для реализации обновления путем оттягивания необходимо обрабатывать события манипуляций, чтобы получать сведения о том, что пользователь оттянул список вниз, отображать визуальную обратную связь и обновлять данные. Здесь мы рассмотрим, как это делается в [примере обновления путем оттягивания](http://go.microsoft.com/fwlink/p/?LinkId=620635). Здесь приводится не весь код, поэтому вам следует либо скачать пример, либо просмотреть код на GitHub.

В примере обновления путем оттягивания создается собственный элемент управления под названием `RefreshableListView`, дополняющий элемент управления. **ListView**. В этом элементе управления добавлен индикатор обновления для предоставления визуальной обратной связи, а также реализована обработка событий манипуляции внутренним средством прокрутки списка. В нем также добавляется 2 события для уведомления об оттягивании списка и о необходимости обновления данных. RefreshableListView только предоставляет уведомление, что данные необходимо обновить. Вам необходимо самостоятельно обрабатывать это событие в своем приложении для обновления данных, и такой код будет различаться для разных приложений.

RefreshableListView предоставляет режим автоматического обновления, который отслеживает запрос на обновление и исчезновение индикатора обновления из представления. Автоматическое обновление можно включить или отключить.
- Выкл.: обновление запрашивается, только если пользователь отпускает список, когда значение `PullThreshold` превышено. Индикатор в анимированном виде выводится из представления, когда пользователь отпускает средство прокрутки. Отображается индикатор строки состояния, если она есть (на телефоне).
- Вкл.: обновление запрашивается, как только превышается значение `PullThreshold`, независимо от того, отпустил пользователь список или нет. Индикатор остается виден, пока новые данные подгружаются, а затем в анимированном виде выводится из представления. Параметр отсрочки **Deferral** используется для уведомления приложения о завершении получения данных.

> **Примечание.**&nbsp;&nbsp;Код в примере также подходит для [GridView](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.gridview.aspx). Для изменения GridView создайте производный класс от GridView, а не от ListView и измените шаблон GridView по умолчанию.

## <a name="add-a-refresh-indicator"></a>Добавление индикатора обновления

Важно предоставлять пользователю визуальную обратную связь, чтобы он понимал, что ваше приложение поддерживает обновление путем оттягивания. RefreshableListView имеет свойство `RefreshIndicatorContent`, которое позволяет задать визуальный индикатор в XAML. Здесь также включается текстовый индикатор по умолчанию, который используется, если свойство `RefreshIndicatorContent` не задано.

Ниже приведены рекомендации для индикатора обновления.

![красные линии индикатора обновления](images/ptr-redlines-1.png)

**Изменение шаблона представления списка**

В примере обновления путем оттягивания шаблон элемента управления `RefreshableListView` изменяет стандартный шаблон **ListView** путем добавления индикатора обновления. Индикатор обновления помещается в [Grid](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.grid.aspx) над элементом [ItemsPresenter](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.itemspresenter.aspx), который отвечает за отображение элементов списка.

> **Примечание.**&nbsp;&nbsp;Текстовое поле `DefaultRefreshIndicatorContent` содержит резервный текстовый индикатор, который отображается, только если свойство `RefreshIndicatorContent` не задано.

Ниже приводится часть шаблона элемента управления, которая отличается от шаблона ListView по умолчанию.

**XAML**
```xaml
<!-- Styles/Styles.xaml -->
<Grid x:Name="ScrollerContent" VerticalAlignment="Top">
    <Grid.RowDefinitions>
        <RowDefinition Height="Auto"/>
        <RowDefinition Height="*"/>
        <RowDefinition Height="Auto"/>
    </Grid.RowDefinitions>
    <Border x:Name="RefreshIndicator" VerticalAlignment="Top" Grid.Row="1">
        <Grid>
            <TextBlock x:Name="DefaultRefreshIndicatorContent" HorizontalAlignment="Center" 
                       Foreground="White" FontSize="20" Margin="20, 35, 20, 20"/>
            <ContentPresenter Content="{TemplateBinding RefreshIndicatorContent}"></ContentPresenter>
        </Grid>
    </Border>
    <ItemsPresenter FooterTransitions="{TemplateBinding FooterTransitions}" 
                    FooterTemplate="{TemplateBinding FooterTemplate}" 
                    Footer="{TemplateBinding Footer}" 
                    HeaderTemplate="{TemplateBinding HeaderTemplate}" 
                    Header="{TemplateBinding Header}" 
                    HeaderTransitions="{TemplateBinding HeaderTransitions}" 
                    Padding="{TemplateBinding Padding}"
                    Grid.Row="1"
                    x:Name="ItemsPresenter"/>
</Grid>
```

**Определение содержимого в XAML**

Вы задаете содержимое индикатора обновления в XAML для представления списка. Заданное вами содержимое XAML отображается индикатором обновления [ContentPresenter](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.contentpresenter.aspx) (`<ContentPresenter Content="{TemplateBinding RefreshIndicatorContent}">`). Если вы не зададите это содержимое, будет отображаться текстовый индикатор по умолчанию.

**XAML**
```xaml
<!-- MainPage.xaml -->
<c:RefreshableListView
    <!-- ... See sample for removed code. -->
    AutoRefresh="{x:Bind Path=UseAutoRefresh, Mode=OneWay}"
    ItemsSource="{x:Bind Items}"
    PullProgressChanged="listView_PullProgressChanged"
    RefreshRequested="listView_RefreshRequested">

    <c:RefreshableListView.RefreshIndicatorContent>
        <Grid Height="100" Background="Transparent">
            <FontIcon
                Margin="0,0,0,30"
                HorizontalAlignment="Center"
                VerticalAlignment="Bottom"
                FontFamily="Segoe MDL2 Assets"
                FontSize="20"
                Glyph="&#xE72C;"
                RenderTransformOrigin="0.5,0.5">
                <FontIcon.RenderTransform>
                    <RotateTransform x:Name="SpinnerTransform" Angle="0" />
                </FontIcon.RenderTransform>
            </FontIcon>
        </Grid>
    </c:RefreshableListView.RefreshIndicatorContent>
    
    <!-- ... See sample for removed code. -->

</c:RefreshableListView>
```

**Анимирование вращающегося индикатора**

Когда список оттягивается вниз, возникает событие `PullProgressChanged` элемента RefreshableListView. Вы обрабатываете это событие в своем приложении для управления индикатором обновления. В примере фрагмент кода Storyboard выполняется для анимирования объекта [RotateTransform](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.media.rotatetransform.aspx) индикатора и вращения индикатора обновления. 

**XAML**
```xaml
<!-- MainPage.xaml -->
<Storyboard x:Name="SpinnerStoryboard">
    <DoubleAnimation
        Duration="00:00:00.5"
        FillBehavior="HoldEnd"
        From="0"
        RepeatBehavior="Forever"
        Storyboard.TargetName="SpinnerTransform"
        Storyboard.TargetProperty="Angle"
        To="360" />
</Storyboard>
```

## <a name="handle-scroll-viewer-manipulation-events"></a>Обработка событий манипуляции средством прокрутки

Шаблон элемента управления listview включает в себя встроенный компонент [ScrollViewer](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.scrollviewer.aspx), позволяющий пользователю прокручивать элементы списка. Для реализации обновления путем оттягивания необходимо обрабатывать события манипуляции на встроенном средстве прокрутки, а также ряд связанных событий. Подробные сведения о событиях манипуляции см. в разделе [Сенсорное управление](../input-and-devices/touch-interactions.md).

** OnApplyTemplate**

Для доступа к средству прокрутки и другим частям шаблона в целях добавления обработчиков событий и вызова их в коде в дальнейшем необходимо переопределить метод [OnApplyTemplate](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.frameworkelement.onapplytemplate.aspx). В OnApplyTemplate вызовите [GetTemplateChild](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.control.gettemplatechild.aspx), чтобы получить ссылку на именованную часть в шаблоне элемента управления, которую можно сохранить для дальнейшего использования в коде.

В примере переменные, используемые для хранения частей шаблона, объявлены в области частных переменных. После их получения в методе OnApplyTemplate добавляются обработчики для событий [DirectManipulationStarted](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.scrollviewer.directmanipulationstarted.aspx), [DirectManipulationCompleted](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.scrollviewer.directmanipulationcompleted.aspx), [ViewChanged](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.scrollviewer.viewchanged.aspx) и [PointerPressed](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.uielement.pointerpressed.aspx).

**DirectManipulationStarted**

Для запуска действия обновления путем оттягивания содержимое должно быть прокручено в верхнюю часть средства прокрутки, когда пользователь начнет оттягивать список вниз. Иначе предполагается, что пользователь тянет список вниз, чтобы переместиться по нему вверх. Код в этом обработчике определяет, начата ли манипуляция с содержимым в верхней части средства прокрутки, и это может привести к обновлению списка. Состояние элемента управления "обновляемый" устанавливается соответствующим образом. 

Если элемент управления можно обновить, также добавляются обработчики событий для анимаций.

**DirectManipulationCompleted**

Когда пользователь прекращает оттягивать список вниз, код в этом обработчике проверяет, было ли активировано обновление во время манипуляции. Если обновление было активировано, возникает событие `RefreshRequested`, и выполняется команда `RefreshCommand`.

Обработчики событий для анимаций также удаляются.

В соответствии со значением свойства `AutoRefresh` список может с использованием анимации немедленно вернуться вверх либо дождаться завершения обновления и затем вернуться вверх. Объект [Deferral](https://msdn.microsoft.com/library/windows/apps/windows.foundation.deferral.aspx) Deferral используется для индикации завершения обновления. В этот момент индикатор обновления в пользовательском интерфейсе исчезает.

Эта часть обработчика событий DirectManipulationCompleted создает событие `RefreshRequested` и при необходимости получает объект Deferral.

**C#**
```csharp
if (this.RefreshRequested != null)
{
    RefreshRequestedEventArgs refreshRequestedEventArgs = new RefreshRequestedEventArgs(
        this.AutoRefresh ? new DeferralCompletedHandler(RefreshCompleted) : null);
    this.RefreshRequested(this, refreshRequestedEventArgs);
    if (this.AutoRefresh)
    {
        m_scrollerContent.ManipulationMode = ManipulationModes.None;
        if (!refreshRequestedEventArgs.WasDeferralRetrieved)
        {
            // The Deferral object was not retrieved in the event handler.
            // Animate the content up right away.
            this.RefreshCompleted();
        }
    }
}
```

**ViewChanged**

В обработчике событий ViewChanged обрабатываются два события.

Во-первых, если представление меняется из-за масштабирования средства прокрутки, состояние элемента управления "обновляемый" отменяется.

Во-вторых, если анимация содержимого завершается по окончании автоматического обновления, прямоугольники заполнения скрываются, сенсорные взаимодействия со средством повторно включаются, свойство [VerticalOffset](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.scrollviewer.verticaloffset.aspx) принимает значение 0.

**PointerPressed**

Обновление путем оттягивания происходит, только когда список оттягивается вниз сенсорным жестом. В обработчике событий PointerPressed код проверяет, какой указатель вызвал событие, и устанавливает переменную (`m_pointerPressed`) для указания, был ли это указатель касания. Эта переменная используется в обработчике DirectManipulationStarted. Если указатель не является указателем касания, обработчик DirectManipulationStarted возвращает управление, не выполняя никаких действий.

## <a name="add-pull-and-refresh-events"></a>Добавление событий обновления путем оттягивания

RefreshableListView добавляет 2 события, которые можно обрабатывать в приложении для обновления данных и управления индикатором обновления.

Подробнее о событиях см. в разделе [Общие сведения о событиях и перенаправленных событиях](https://msdn.microsoft.com/windows/uwp/xaml-platform/events-and-routed-events-overview).

**RefreshRequested**

Событие RefreshRequested уведомляет ваше приложение о том, что пользователь оттянул список, чтобы обновить его. Вы обрабатываете это событие для получения новых данных и обновления списка.

Ниже приведен обработчик события из примера. Важно помнить, что он проверяет свойство `AutoRefresh` представления списка и получает объект Deferral, если его значение равно **true**. При наличии объекта Deferral индикатор обновления не останавливается и не скрывается, пока обновление не завершается.

**C#**
```csharp
private async void listView_RefreshRequested(object sender, RefreshableListView.RefreshRequestedEventArgs e)
{
    using (Deferral deferral = listView.AutoRefresh ? e.GetDeferral() : null)
    {
        await FetchAndInsertItemsAsync(_rand.Next(1, 5));

        if (SpinnerStoryboard.GetCurrentState() != Windows.UI.Xaml.Media.Animation.ClockState.Stopped)
        {
            SpinnerStoryboard.Stop();
        }
    }
}
```

**PullProgressChanged**

В примере содержимое для индикатора обновления предоставляется и управляется приложением. Событие PullProgressChanged уведомляет ваше приложение, когда пользователь оттягивает список, чтобы вы могли запустить, остановить и сбросить индикатор обновления. 

## <a name="composition-animations"></a>Анимации композиции

По умолчанию содержимое в средстве прокрутки останавливается, когда полоса прокрутки достигает верхней точки. Чтобы пользователь мог продолжить оттягивать список вниз, вам необходимо получить доступ в визуальному уровню и анимировать содержимое списка. В примере для этого используются [анимации композиции](https://msdn.microsoft.com/windows/uwp/composition/composition-animation), в частности, [анимации с помощью выражений](https://msdn.microsoft.com/windows/uwp/composition/composition-animation#expression-animations).

В примере эта работа выполняется преимущественно в обработчике событий `CompositionTarget_Rendering` и методе `UpdateCompositionAnimations`.

## <a name="related-articles"></a>Еще по теме

- [Настройка стиля элементов управления](styling-controls.md)
- [Взаимодействие с помощью сенсорного экрана](../input-and-devices/touch-interactions.md)
- [Представление списка и сетки](listview-and-gridview.md)
- [Шаблоны элементов представления списка](listview-item-templates.md)
- [Анимация с помощью выражений](https://msdn.microsoft.com/windows/uwp/composition/composition-animation#expression-animations)
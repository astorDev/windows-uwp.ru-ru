---
author: Jwmsft
Description: "Помогите пользователям работать эффективнее, дав им возможность открывать несколько независимых компонентов приложения в отдельных окнах."
title: "Отображение нескольких представлений для приложения"
ms.assetid: BAF9956F-FAAF-47FB-A7DB-8557D2548D88
label: Show multiple views for an app
template: detail.hbs
op-migration-status: ready
ms.author: jimwalk
ms.date: 02/08/2017
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: Windows 10, uwp
ms.openlocfilehash: 87f3d5e75b361d1ba9d2c304e58542803da66cd4
ms.sourcegitcommit: 909d859a0f11981a8d1beac0da35f779786a6889
translationtype: HT
---
# <a name="show-multiple-views-for-an-app"></a>Отображение нескольких представлений для приложения

<link rel="stylesheet" href="https://az835927.vo.msecnd.net/sites/uwp/Resources/css/custom.css">

Вы можете помочь пользователям работать эффективнее, дав им возможность открывать несколько независимых компонентов приложения в отдельных окнах. Типичный пример— почтовое приложение, в главном интерфейсе которого отображается список адресов электронной почты и предварительный просмотр выбранной электронной почты. Однако пользователь может также открывать сообщения в отдельных окнах и просматривать их рядом.

<div class="important-apis" >
<b>Важные API</b><br/>
<ul>
<li>[**ApplicationViewSwitcher**](https://msdn.microsoft.com/library/windows/apps/dn281094)</li>
<li>[**CreateNewView**](https://msdn.microsoft.com/library/windows/apps/dn297278)</li>
</ul>
</div> 

Если создать для приложения несколько окон, каждое окно будет работать независимо. На панели задач каждое окно отображается отдельно. Пользователи могут перемещать, отображать, скрывать окна приложения и менять их размеры независимо друг от друга, а также переключаться между окнами, как будто это разные приложения. Каждое окно работает в собственном потоке.

## <a name="what-is-a-view"></a>Что такое представление приложения?


Представление приложения— это совокупность 1:1 потока и окна, которая используется приложением для отображения содержимого. Оно представляется объектом [**Windows.ApplicationModel.Core.CoreApplicationView**](https://msdn.microsoft.com/library/windows/apps/br225017).

Представления управляются объектом [**CoreApplication**](https://msdn.microsoft.com/library/windows/apps/br225016). Необходимо вызвать [**CoreApplication.CreateNewView**](https://msdn.microsoft.com/library/windows/apps/dn297278), чтобы создать объект [**CoreApplicationView**](https://msdn.microsoft.com/library/windows/apps/br225017). **CoreApplicationView** объединяет [**CoreWindow**](https://msdn.microsoft.com/library/windows/apps/br208225) и [**CoreDispatcher**](https://msdn.microsoft.com/library/windows/apps/br208211) (которые хранятся в свойствах [**CoreWindow**](https://msdn.microsoft.com/library/windows/apps/br225019) и [**Dispatcher**](https://msdn.microsoft.com/library/windows/apps/dn433264)). **CoreApplicationView** можно считать объектом, который использует среда выполнения Windows для взаимодействия с основной системой Windows.

Обычно работать непосредственно с [**CoreApplicationView**](https://msdn.microsoft.com/library/windows/apps/br225017) не нужно. Вместо этого среда выполнения Windows предоставляет класс [**ApplicationView**](https://msdn.microsoft.com/library/windows/apps/hh701658) в пространстве имен [**Windows.UI.ViewManagement**](https://msdn.microsoft.com/library/windows/apps/br242295). Этот класс предоставляет свойства, методы и события, которые используются при взаимодействии приложения с системой работы с окнами. Для работы с **ApplicationView** вызовите статический метод [**ApplicationView.GetForCurrentView**](https://msdn.microsoft.com/library/windows/apps/hh701672), который получает экземпляр **ApplicationView**, связанный с текущим потоком **CoreApplicationView**.

Аналогично, платформа XAML создает программу-оболочку для объекта [**CoreWindow**](https://msdn.microsoft.com/library/windows/apps/br208225) в объекте [**Windows.UI.XAML.Window**](https://msdn.microsoft.com/library/windows/apps/br209041). В приложении XAML обычно происходит взаимодействие с объектом **Window** вместо непосредственной работы с **CoreWindow**.

## <a name="show-a-new-view"></a>Отображение нового представления


Прежде чем продолжить, давайте рассмотрим, как создается новое представление. В данном примере новое представление запускается в ответ на нажатие кнопки.

```csharp
private async void Button_Click(object sender, RoutedEventArgs e)
{
    CoreApplicationView newView = CoreApplication.CreateNewView();
    int newViewId = 0;
    await newView.Dispatcher.RunAsync(CoreDispatcherPriority.Normal, () =>
    {
        Frame frame = new Frame();
        frame.Navigate(typeof(SecondaryPage), null);   
        Window.Current.Content = frame;
        // You have to activate the window in order to show it later.
        Window.Current.Activate();

        newViewId = ApplicationView.GetForCurrentView().Id;
    });
    bool viewShown = await ApplicationViewSwitcher.TryShowAsStandaloneAsync(newViewId);
}
```

**Отображение нового представления**

1.  Вызовите [**CoreApplication.CreateNewView**](https://msdn.microsoft.com/library/windows/apps/dn297291), чтобы создать новое окно и поток для содержимого представления.

    ```csharp
    CoreApplicationView newView = CoreApplication.CreateNewView();
    ```

2.  Отслеживайте [**Id**](https://msdn.microsoft.com/library/windows/apps/dn281120) нового представления. Это понадобится для отображения представления позже.

    Можно создать некоторую инфраструктуру в приложении, чтобы упростить отслеживание представлений, которые вы создаете. Пример: класс `ViewLifetimeControl` в разделе [Пример MultipleViews](http://go.microsoft.com/fwlink/p/?LinkId=620574).

    ```csharp
    int newViewId = 0;
    ```

3.  В новом потоке заполните окно.

    При помощи метода [**CoreDispatcher.RunAsync**](https://msdn.microsoft.com/library/windows/apps/hh750317) запланируйте задачу в потоке пользовательского интерфейса для нового представления. Используйте [лямбда-выражение](http://go.microsoft.com/fwlink/p/?LinkId=389615), чтобы передать функцию методу **RunAsync** как аргумент. Результаты работы лямбда-функции влияют на поток нового представления.

    В XAML [**Frame**](https://msdn.microsoft.com/library/windows/apps/br242682) обычно добавляется к свойству [**Content**](https://msdn.microsoft.com/library/windows/apps/br209051) [**Window**](https://msdn.microsoft.com/library/windows/apps/br209041), а затем выполняется переход **Frame** к [**Page**](https://msdn.microsoft.com/library/windows/apps/br227503), где определено содержимое приложения. Подробнее см. в разделе [Одноранговая навигация между двумя страницами](navigate-between-two-pages.md).

    После заполнения нового [**Window**](https://msdn.microsoft.com/library/windows/apps/br209041) необходимо вызвать метод [**Activate**](https://msdn.microsoft.com/library/windows/apps/br209046)&nbsp;**Window** для отображения **Window** позднее. Результаты влияют на поток нового представления, так что активируется новое **Window**.

    Наконец, скачайте [**Id**](https://msdn.microsoft.com/library/windows/apps/dn281120) нового используемого представления для его отображения позже. Результаты также влияют на поток нового представления, поэтому [**ApplicationView.GetForCurrentView**](https://msdn.microsoft.com/library/windows/apps/hh701672) получает **Id** нового представления.

    ```csharp
    await newView.Dispatcher.RunAsync(CoreDispatcherPriority.Normal, () =>
    {
        Frame frame = new Frame();
        frame.Navigate(typeof(SecondaryPage), null);   
        Window.Current.Content = frame;
        // You have to activate the window in order to show it later.
        Window.Current.Activate();

        newViewId = ApplicationView.GetForCurrentView().Id;
    });
    ```

4.  Отобразите новое представление, вызвав [**ApplicationViewSwitcher.TryShowAsStandaloneAsync**](https://msdn.microsoft.com/library/windows/apps/dn281101).

    После создания нового представления, вы сможете отобразить его в новом окне с помощью метода [**ApplicationViewSwitcher.TryShowAsStandaloneAsync**](https://msdn.microsoft.com/library/windows/apps/dn281101). Параметр *viewId* в этом методе представляет собой целое число, уникальным образом идентифицирующее каждое представление в приложении. Вы получите представление [**Id**](https://msdn.microsoft.com/library/windows/apps/dn281120), воспользовавшись свойством **ApplicationView.Id** или методом [**ApplicationView.GetApplicationViewIdForWindow**](https://msdn.microsoft.com/library/windows/apps/dn281109).

    ```csharp
    bool viewShown = await ApplicationViewSwitcher.TryShowAsStandaloneAsync(newViewId);
    ```

## <a name="the-main-view"></a>Главное представление


Первое представление, создаваемое при запуске приложения, называется *главным*. Это представление хранится в свойстве [**CoreApplication.MainView**](https://msdn.microsoft.com/library/windows/apps/hh700465), и его свойство [**IsMain**](https://msdn.microsoft.com/library/windows/apps/hh700452) имеет значение true. Вам не нужно создавать это представление, его создает приложение. Поток главного представления служит диспетчером для приложения, и события активации в приложении происходят в этом потоке.

Если открыты вспомогательные представления, окно главного представления может быть скрыто, например, по нажатию кнопки закрытия (x) на панели заголовка окна, но его поток остается активным. Вызов [**Close**](https://msdn.microsoft.com/library/windows/apps/br209049) в [**Window**](https://msdn.microsoft.com/library/windows/apps/br209041) главного представления приведет к возникновению **InvalidOperationException**. (Чтобы закрыть приложение, используйте [**Application.Exit**](https://msdn.microsoft.com/library/windows/apps/br242327).) Если работа потока главного представления завершается, приложение закрывается.

## <a name="secondary-views"></a>Дополнительные представления


Другие представления, в том числе все представления, создаваемые по вызову [**CreateNewView**](https://msdn.microsoft.com/library/windows/apps/dn297278) в коде приложения, являются дополнительными представлениями. Как главное представление, так и дополнительные представления хранятся в коллекции [**CoreApplication.Views**](https://msdn.microsoft.com/library/windows/apps/br205861). Обычно дополнительные представления создаются в ответ на действия пользователя. В некоторых случаях система создает дополнительные представления для приложения.

> [!NOTE]
> Вы можете использовать функцию *ограниченного доступа* Windows для запуска приложения в [режиме киоска](https://technet.microsoft.com/library/mt219050.aspx). После этого система создает дополнительное представление для представления пользовательского интерфейса приложения поверх экрана блокировки. Приложению не разрешается создавать дополнительные представления, поэтому при попытке отобразить собственное дополнительное представление в режиме киоска возникает исключение.

## <a name="switch-from-one-view-to-another"></a>Переключение между представлениями

Необходимо предоставить пользователю возможность вернуться в главное окно из дополнительного. Для этого используйте метод [**ApplicationViewSwitcher.SwitchAsync**](https://msdn.microsoft.com/library/windows/apps/dn281097). Вызовите этот метод из потока окна, из которого вы переключаетесь, и передайте идентификатор представления окна, на которое вы переключаетесь.

```csharp
await ApplicationViewSwitcher.SwitchAsync(viewIdToShow);
```

Используя [**SwitchAsync**](https://msdn.microsoft.com/library/windows/apps/dn281097), можно определить, следует ли закрыть начальное окно и удалить его из панели задач, указав значение [**ApplicationViewSwitchingOptions**](https://msdn.microsoft.com/library/windows/apps/dn281105).

 

 





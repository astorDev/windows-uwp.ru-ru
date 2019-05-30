---
Description: Просмотр нескольких частей приложения в отдельных окнах.
title: Отображение нескольких представлений для приложения
ms.date: 05/19/2017
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: 097ff0bb9e2ac8d36780a692172afb0a7933fdd1
ms.sourcegitcommit: ac7f3422f8d83618f9b6b5615a37f8e5c115b3c4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/29/2019
ms.locfileid: "66364964"
---
# <a name="show-multiple-views-for-an-app"></a>Отображение нескольких представлений для приложения

![Модель, показывающая приложение с несколькими окнами](images/multi-view.gif)

Помогите пользователям работать эффективнее, дав им возможность открывать независимые компоненты приложения в отдельных окнах. Если создать для приложения несколько окон, каждое окно будет работать независимо. На панели задач каждое окно отображается отдельно. Пользователи могут перемещать, отображать, скрывать окна приложения и менять их размеры независимо друг от друга, а также переключаться между окнами, как будто это разные приложения. Каждое окно работает в собственном потоке.

> **Важные API**: [**ApplicationViewSwitcher**](https://docs.microsoft.com/uwp/api/Windows.UI.ViewManagement.ApplicationViewSwitcher), [ **CreateNewView**](https://docs.microsoft.com/uwp/api/windows.applicationmodel.core.coreapplication.createnewview)

## <a name="when-should-an-app-use-multiple-views"></a>Когда приложение должно использовать несколько представлений?
Существует ряд сценариев, для которых может быть удобным использование нескольких представлений. Вот несколько примеров.
 - Приложение электронной почты, которое позволяет пользователям просматривать список полученных сообщений при составлении нового письма
 - Приложение адресной книги, которое позволяет пользователям сравнить контактные данные для нескольких параллельных пользователей
 - Приложение проигрывателя музыки, которое позволяет пользователям просматривать информацию о воспроизводимой музыке во время просмотра списка другой доступной музыки
 - Приложение заметок, которое позволяет пользователям копировать сведения с одной страницы заметок на другую
 - Приложение чтения, которое дает пользователям возможность просматривать все заголовки высокого уровня и позволяет открывать несколько статей для чтения позже

Для создания отдельных экземпляров приложения см. раздел [Создание нескольких экземпляров приложения UWP](../../launch-resume/multi-instance-uwp.md).

## <a name="what-is-a-view"></a>Что такое представление приложения?

Представление приложения — это совокупность 1:1 потока и окна, которая используется приложением для отображения содержимого. Оно представляется объектом [**Windows.ApplicationModel.Core.CoreApplicationView**](https://docs.microsoft.com/uwp/api/Windows.ApplicationModel.Core.CoreApplicationView).

Представления управляются объектом [**CoreApplication**](https://docs.microsoft.com/uwp/api/Windows.ApplicationModel.Core.CoreApplication). Необходимо вызвать [**CoreApplication.CreateNewView**](https://docs.microsoft.com/uwp/api/windows.applicationmodel.core.coreapplication.createnewview), чтобы создать объект [**CoreApplicationView**](https://docs.microsoft.com/uwp/api/Windows.ApplicationModel.Core.CoreApplicationView). **CoreApplicationView** объединяет [**CoreWindow**](https://docs.microsoft.com/uwp/api/Windows.UI.Core.CoreWindow) и [**CoreDispatcher**](https://docs.microsoft.com/uwp/api/Windows.UI.Core.CoreDispatcher) (которые хранятся в свойствах [**CoreWindow**](https://docs.microsoft.com/uwp/api/windows.applicationmodel.core.coreapplicationview.corewindow) и [**Dispatcher**](https://docs.microsoft.com/uwp/api/windows.applicationmodel.core.coreapplicationview.dispatcher)). **CoreApplicationView** можно считать объектом, который использует среда выполнения Windows для взаимодействия с основной системой Windows.

Обычно работать непосредственно с [**CoreApplicationView**](https://docs.microsoft.com/uwp/api/Windows.ApplicationModel.Core.CoreApplicationView) не нужно. Вместо этого среда выполнения Windows предоставляет класс [**ApplicationView**](https://docs.microsoft.com/uwp/api/Windows.UI.ViewManagement.ApplicationView) в пространстве имен [**Windows.UI.ViewManagement**](https://docs.microsoft.com/uwp/api/Windows.UI.ViewManagement). Этот класс предоставляет свойства, методы и события, которые используются при взаимодействии приложения с системой работы с окнами. Для работы с **ApplicationView** вызовите статический метод [**ApplicationView.GetForCurrentView**](https://docs.microsoft.com/uwp/api/windows.ui.viewmanagement.applicationview.getforcurrentview), который получает экземпляр **ApplicationView**, связанный с текущим потоком **CoreApplicationView**.

Аналогично, платформа XAML создает программу-оболочку для объекта [**CoreWindow**](https://docs.microsoft.com/uwp/api/Windows.UI.Core.CoreWindow) в объекте [**Windows.UI.XAML.Window**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Window). В приложении XAML обычно происходит взаимодействие с объектом **Window** вместо непосредственной работы с **CoreWindow**.

## <a name="show-a-new-view"></a>Отображение нового представления

Поскольку каждый макет приложения уникален, мы рекомендуем добавить кнопку «новое окно» в предсказуемые местоположение, например в правый верхний угол содержимого, которое можно открыть в новом окне. Также следует учесть необходимость добавления параметра контекстного меню «Открыть в новом окне».

Рассмотрим шаги для создания нового представления. В данном примере новое представление запускается в ответ на нажатие кнопки.

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

**Чтобы отобразить новое представление**

1.  Вызовите [**CoreApplication.CreateNewView**](https://docs.microsoft.com/uwp/api/windows.applicationmodel.core.coreapplication.createnewview), чтобы создать новое окно и поток для содержимого представления.

    ```csharp
    CoreApplicationView newView = CoreApplication.CreateNewView();
    ```

2.  Отслеживайте [**Id**](https://docs.microsoft.com/uwp/api/windows.ui.viewmanagement.applicationview.id) нового представления. Это понадобится для отображения представления позже.

    Можно создать некоторую инфраструктуру в приложении, чтобы упростить отслеживание представлений, которые вы создаете. Пример: класс `ViewLifetimeControl` в разделе [Пример MultipleViews](https://go.microsoft.com/fwlink/p/?LinkId=620574).

    ```csharp
    int newViewId = 0;
    ```

3.  В новом потоке заполните окно.

    При помощи метода [**CoreDispatcher.RunAsync**](https://docs.microsoft.com/uwp/api/windows.ui.core.coredispatcher.windows) запланируйте задачу в потоке пользовательского интерфейса для нового представления. Используйте [лямбда-выражение](https://go.microsoft.com/fwlink/p/?LinkId=389615), чтобы передать функцию методу **RunAsync** как аргумент. Результаты работы лямбда-функции влияют на поток нового представления.

    В XAML [**Frame**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Frame) обычно добавляется к свойству [**Content**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.window.content)[**Window**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Window), а затем выполняется переход **Frame** к [**Page**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Page), где определено содержимое приложения. Подробнее см. в разделе [Одноранговая навигация между двумя страницами](../basics/navigate-between-two-pages.md).

    После заполнения нового [**Window**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Window) необходимо вызвать метод [**Activate**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.window.activate)&nbsp;**Window** для отображения **Window** позднее. Результаты влияют на поток нового представления, так что активируется новое **Window**.

    Наконец, скачайте [**Id**](https://docs.microsoft.com/uwp/api/windows.ui.viewmanagement.applicationview.id) нового используемого представления для его отображения позже. Результаты также влияют на поток нового представления, поэтому [**ApplicationView.GetForCurrentView**](https://docs.microsoft.com/uwp/api/windows.ui.viewmanagement.applicationview.getforcurrentview) получает **Id** нового представления.

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

4.  Отобразите новое представление, вызвав [**ApplicationViewSwitcher.TryShowAsStandaloneAsync**](https://docs.microsoft.com/uwp/api/windows.ui.viewmanagement.applicationviewswitcher.tryshowasstandaloneasync).

    После создания нового представления, вы сможете отобразить его в новом окне с помощью метода [**ApplicationViewSwitcher.TryShowAsStandaloneAsync**](https://docs.microsoft.com/uwp/api/windows.ui.viewmanagement.applicationviewswitcher.tryshowasstandaloneasync). Параметр *viewId* в этом методе представляет собой целое число, уникальным образом идентифицирующее каждое представление в приложении. Вы получите представление [**Id**](https://docs.microsoft.com/uwp/api/windows.ui.viewmanagement.applicationview.id), воспользовавшись свойством **ApplicationView.Id** или методом [**ApplicationView.GetApplicationViewIdForWindow**](https://docs.microsoft.com/uwp/api/windows.ui.viewmanagement.applicationview.getapplicationviewidforwindow).

    ```csharp
    bool viewShown = await ApplicationViewSwitcher.TryShowAsStandaloneAsync(newViewId);
    ```

## <a name="the-main-view"></a>Главное представление


Первое представление, создаваемое при запуске приложения, называется *главным*. Это представление хранится в свойстве [**CoreApplication.MainView**](https://docs.microsoft.com/uwp/api/windows.applicationmodel.core.coreapplication.mainview), и его свойство [**IsMain**](https://docs.microsoft.com/uwp/api/windows.applicationmodel.core.coreapplicationview.ismain) имеет значение true. Вам не нужно создавать это представление, его создает приложение. Поток главного представления служит диспетчером для приложения, и события активации в приложении происходят в этом потоке.

Если открыты вспомогательные представления, окно главного представления может быть скрыто, например, по нажатию кнопки закрытия (x) на панели заголовка окна, но его поток остается активным. Вызов [**Close**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.window.close) в [**Window**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Window) главного представления приведет к возникновению **InvalidOperationException**. (Используйте [ **Application.Exit** ](https://docs.microsoft.com/uwp/api/windows.ui.xaml.application.exit) чтобы закрыть приложение.) Если основное представление поток завершается, приложение закрывается.

## <a name="secondary-views"></a>Дополнительные представления


Другие представления, в том числе все представления, создаваемые по вызову [**CreateNewView**](https://docs.microsoft.com/uwp/api/windows.applicationmodel.core.coreapplication.createnewview) в коде приложения, являются дополнительными представлениями. Как главное представление, так и дополнительные представления хранятся в коллекции [**CoreApplication.Views**](https://docs.microsoft.com/uwp/api/windows.applicationmodel.core.coreapplication.views). Обычно дополнительные представления создаются в ответ на действия пользователя. В некоторых случаях система создает дополнительные представления для приложения.

> [!NOTE]
> Вы можете использовать функцию *ограниченного доступа* Windows для запуска приложения в [режиме киоска](https://technet.microsoft.com/library/mt219050.aspx). После этого система создает дополнительное представление для представления пользовательского интерфейса приложения поверх экрана блокировки. Приложению не разрешается создавать дополнительные представления, поэтому при попытке отобразить собственное дополнительное представление в режиме киоска возникает исключение.

## <a name="switch-from-one-view-to-another"></a>Переключение между представлениями

Следует учесть необходимость предоставления пользователю возможности вернуться в главное окно из дополнительного. Для этого используйте метод [**ApplicationViewSwitcher.SwitchAsync**](https://docs.microsoft.com/uwp/api/windows.ui.viewmanagement.applicationviewswitcher.switchasync). Вызовите этот метод из потока окна, из которого вы переключаетесь, и передайте идентификатор представления окна, на которое вы переключаетесь.

```csharp
await ApplicationViewSwitcher.SwitchAsync(viewIdToShow);
```

Используя [**SwitchAsync**](https://docs.microsoft.com/uwp/api/windows.ui.viewmanagement.applicationviewswitcher.switchasync), можно определить, следует ли закрыть начальное окно и удалить его из панели задач, указав значение [**ApplicationViewSwitchingOptions**](https://docs.microsoft.com/uwp/api/Windows.UI.ViewManagement.ApplicationViewSwitchingOptions).

## <a name="dos-and-donts"></a>Возможности и ограничения

* Укажите четкую точку входа в дополнительное представление, используя значок «открыть новое окно».
* Обсудите цель предоставления дополнительного представления для пользователей.
* Убедитесь, что работающее приложение является полностью функциональным в отдельном представлении и пользователи будут открывать дополнительное представление только для удобства.
* Не полагайтесь на дополнительное представление в плане предоставления уведомлений и других промежуточный визуальных элементов.

## <a name="related-topics"></a>См. также

* [ApplicationViewSwitcher](https://docs.microsoft.com/uwp/api/Windows.UI.ViewManagement.ApplicationViewSwitcher)
* [CreateNewView](https://docs.microsoft.com/uwp/api/windows.applicationmodel.core.coreapplication.createnewview)
 

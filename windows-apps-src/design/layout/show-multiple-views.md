---
author: Jwmsft
Description: View multiple parts of your app in separate windows.
title: Отображение нескольких представлений для приложения
ms.author: mijacobs
ms.date: 05/19/2017
ms.topic: article
keywords: Windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: 048b62e5131852c917b6ec6cd66273760509ef02
ms.sourcegitcommit: f2c9a050a9137a473f28b613968d5782866142c6
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/10/2018
ms.locfileid: "6263067"
---
# <a name="show-multiple-views-for-an-app"></a>Отображение нескольких представлений для приложения

![Модель, показывающая приложение с несколькими окнами](images/multi-view.gif)

Помогите пользователям работать эффективнее, дав им возможность открывать независимые компоненты приложения в отдельных окнах. Если создать для приложения несколько окон, каждое окно будет работать независимо. На панели задач каждое окно отображается отдельно. Пользователи могут перемещать, отображать, скрывать окна приложения и менять их размеры независимо друг от друга, а также переключаться между окнами, как будто это разные приложения. Каждое окно работает в собственном потоке.

> **Важные API-интерфейсы**: [**ApplicationViewSwitcher**](https://msdn.microsoft.com/library/windows/apps/dn281094), [**CreateNewView**](https://msdn.microsoft.com/library/windows/apps/dn297278)

## <a name="when-should-an-app-use-multiple-views"></a>Когда приложение должно использовать несколько представлений?
Существует ряд сценариев, для которых может быть удобным использование нескольких представлений. Вот несколько примеров.
 - Приложение электронной почты, которое позволяет пользователям просматривать список полученных сообщений при составлении нового письма
 - Приложение адресной книги, которое позволяет пользователям сравнить контактные данные для нескольких параллельных пользователей
 - Приложение проигрывателя музыки, которое позволяет пользователям просматривать информацию о воспроизводимой музыке во время просмотра списка другой доступной музыки
 - Приложение заметок, которое позволяет пользователям копировать сведения с одной страницы заметок на другую
 - Приложение чтения, которое дает пользователям возможность просматривать все заголовки высокого уровня и позволяет открывать несколько статей для чтения позже

Для создания отдельных экземпляров приложения см. раздел [Создание нескольких экземпляров приложения UWP](../../launch-resume/multi-instance-uwp.md).

## <a name="what-is-a-view"></a>Что такое представление?

Представление приложения— это совокупность 1:1 потока и окна, которая используется приложением для отображения содержимого. Оно представляется объектом [**Windows.ApplicationModel.Core.CoreApplicationView**](https://msdn.microsoft.com/library/windows/apps/br225017).

Представления управляются объектом [**CoreApplication**](https://msdn.microsoft.com/library/windows/apps/br225016). Необходимо вызвать [**CoreApplication.CreateNewView**](https://msdn.microsoft.com/library/windows/apps/dn297278), чтобы создать объект [**CoreApplicationView**](https://msdn.microsoft.com/library/windows/apps/br225017). **CoreApplicationView** объединяет [**CoreWindow**](https://msdn.microsoft.com/library/windows/apps/br208225) и [**CoreDispatcher**](https://msdn.microsoft.com/library/windows/apps/br208211) (которые хранятся в свойствах [**CoreWindow**](https://msdn.microsoft.com/library/windows/apps/br225019) и [**Dispatcher**](https://msdn.microsoft.com/library/windows/apps/dn433264)). **CoreApplicationView** можно считать объектом, который использует среда выполнения Windows для взаимодействия с основной системой Windows.

Обычно работать непосредственно с [**CoreApplicationView**](https://msdn.microsoft.com/library/windows/apps/br225017) не нужно. Вместо этого среда выполнения Windows предоставляет класс [**ApplicationView**](https://msdn.microsoft.com/library/windows/apps/hh701658) в пространстве имен [**Windows.UI.ViewManagement**](https://msdn.microsoft.com/library/windows/apps/br242295). Этот класс предоставляет свойства, методы и события, которые используются при взаимодействии приложения с системой работы с окнами. Для работы с **ApplicationView** вызовите статический метод [**ApplicationView.GetForCurrentView**](https://msdn.microsoft.com/library/windows/apps/hh701672), который получает экземпляр **ApplicationView**, связанный с текущим потоком **CoreApplicationView**.

Аналогично, платформа XAML создает программу-оболочку для объекта [**CoreWindow**](https://msdn.microsoft.com/library/windows/apps/br208225) в объекте [**Windows.UI.XAML.Window**](https://msdn.microsoft.com/library/windows/apps/br209041). В приложении XAML обычно происходит взаимодействие с объектом **Window** вместо непосредственной работы с **CoreWindow**.

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

    В XAML [**Frame**](https://msdn.microsoft.com/library/windows/apps/br242682) обычно добавляется к свойству [**Content**](https://msdn.microsoft.com/library/windows/apps/br209051) [**Window**](https://msdn.microsoft.com/library/windows/apps/br209041), а затем выполняется переход **Frame** к [**Page**](https://msdn.microsoft.com/library/windows/apps/br227503), где определено содержимое приложения. Подробнее см. в разделе [Одноранговая навигация между двумя страницами](../basics/navigate-between-two-pages.md).

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

Следует учесть необходимость предоставления пользователю возможности вернуться в главное окно из дополнительного. Для этого используйте метод [**ApplicationViewSwitcher.SwitchAsync**](https://msdn.microsoft.com/library/windows/apps/dn281097). Вызовите этот метод из потока окна, из которого вы переключаетесь, и передайте идентификатор представления окна, на которое вы переключаетесь.

```csharp
await ApplicationViewSwitcher.SwitchAsync(viewIdToShow);
```

Используя [**SwitchAsync**](https://msdn.microsoft.com/library/windows/apps/dn281097), можно определить, следует ли закрыть начальное окно и удалить его из панели задач, указав значение [**ApplicationViewSwitchingOptions**](https://msdn.microsoft.com/library/windows/apps/dn281105).

## <a name="dos-and-donts"></a>Рекомендации

* Укажите четкую точку входа в дополнительное представление, используя значок «открыть новое окно».
* Обсудите цель предоставления дополнительного представления для пользователей.
* Убедитесь, что работающее приложение является полностью функциональным в отдельном представлении и пользователи будут открывать дополнительное представление только для удобства.
* Не полагайтесь на дополнительное представление в плане предоставления уведомлений и других промежуточный визуальных элементов.

## <a name="related-topics"></a>Статьи по теме

* [ApplicationViewSwitcher](https://msdn.microsoft.com/library/windows/apps/dn281094)
* [CreateNewView](https://msdn.microsoft.com/library/windows/apps/dn297278)
 

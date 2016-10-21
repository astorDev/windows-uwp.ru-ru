---
author: Jwmsft
Description: "Помогите пользователям работать эффективнее, дав им возможность открывать несколько независимых компонентов приложения в отдельных окнах."
title: "Отображение нескольких представлений для приложения"
ms.assetid: BAF9956F-FAAF-47FB-A7DB-8557D2548D88
label: Show multiple views for an app
template: detail.hbs
translationtype: Human Translation
ms.sourcegitcommit: 0d67e3cef26ca6aca07556312a18be93fe758c85
ms.openlocfilehash: ccbcb1f3f5ee31724416f512138757865ffabc98

---

# Отображение нескольких представлений для приложения

Вы можете помочь пользователям работать эффективнее, дав им возможность открывать несколько независимых компонентов приложения в отдельных окнах. Типичный пример— почтовое приложение, в главном интерфейсе которого отображается список адресов электронной почты и предварительный просмотр выбранной электронной почты. Однако пользователь может также открывать сообщения в отдельных окнах и просматривать их рядом.

**Важные API**

-   [**ApplicationViewSwitcher**](https://msdn.microsoft.com/library/windows/apps/dn281094)
-   [**CreateNewView**](https://msdn.microsoft.com/library/windows/apps/dn297278)

Если создать для приложения несколько окон, каждое окно будет работать независимо. На панели задач каждое окно отображается отдельно. Пользователи могут перемещать, отображать, скрывать окна приложения и менять их размеры независимо друг от друга, а также переключаться между окнами, как будто это разные приложения. Каждое окно работает в собственном потоке.

## <span id="What_is_a_view_"></span><span id="what_is_a_view_"></span><span id="WHAT_IS_A_VIEW_"></span>Что такое представление приложения?


Представление приложения–это совокупность 1:1 потока и окна, которая используется приложением для отображения содержимого. Оно представляется объектом [**CoreApplicationView**](https://msdn.microsoft.com/library/windows/apps/br225017).

Представления управляются объектом [**CoreApplication**](https://msdn.microsoft.com/library/windows/apps/br225016). Необходимо вызвать [**CoreApplication.CreateNewView**](https://msdn.microsoft.com/library/windows/apps/dn297278), чтобы создать объект [**CoreApplicationView**](https://msdn.microsoft.com/library/windows/apps/br225017). **CoreApplicationView** объединяет [**CoreWindow**](https://msdn.microsoft.com/library/windows/apps/br208225) и [**CoreDispatcher**](https://msdn.microsoft.com/library/windows/apps/br208211) (которые хранятся в свойствах [**CoreWindow**](https://msdn.microsoft.com/library/windows/apps/br225019) и [**Dispatcher**](https://msdn.microsoft.com/library/windows/apps/dn433264)). **CoreApplicationView** можно считать объектом, который использует среда выполнения Windows для взаимодействия с основной системой Windows.

Обычно работать непосредственно с [**CoreApplicationView**](https://msdn.microsoft.com/library/windows/apps/br225017) не нужно. Вместо этого среда выполнения Windows предоставляет класс [**ApplicationView**](https://msdn.microsoft.com/library/windows/apps/hh701658) в пространстве имен [**Windows.UI.ViewManagement**](https://msdn.microsoft.com/library/windows/apps/br242295). Этот класс предоставляет свойства, методы и события, которые используются при взаимодействии приложения с системой работы с окнами. Для работы с **ApplicationView** вызовите статический метод [**ApplicationView.GetForCurrentView**](https://msdn.microsoft.com/library/windows/apps/hh701672), который получает экземпляр **ApplicationView**, связанный с текущим потоком **CoreApplicationView**.

Аналогично, платформа XAML создает программу-оболочку для объекта [**CoreWindow**](https://msdn.microsoft.com/library/windows/apps/br208225) в объекте [**Windows.UI.XAML.Window**](https://msdn.microsoft.com/library/windows/apps/br209041). В приложении XAML обычно происходит взаимодействие с объектом **Window** вместо непосредственной работы с **CoreWindow**.

## <span id="Show_a_new_view"></span><span id="show_a_new_view"></span><span id="SHOW_A_NEW_VIEW"></span>Отображение нового представления


Прежде чем продолжить, давайте рассмотрим, как создается новое представление. В данном примере новое представление запускается в ответ на нажатие кнопки.

```CSharp
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

```    CSharp
CoreApplicationView newView = CoreApplication.CreateNewView();</code></pre></td>
    </tr>
    </tbody>
    </table>
```

2.  Отслеживайте [**Id**](https://msdn.microsoft.com/library/windows/apps/dn281120) нового представления. Это понадобится для отображения представления позже.

    Можно создать некоторую инфраструктуру в приложении, чтобы упростить отслеживание представлений, которые вы создаете. Пример: класс `ViewLifetimeControl` в разделе [Пример MultipleViews](http://go.microsoft.com/fwlink/p/?LinkId=620574).

    <span codelanguage="CSharp"></span>
```    CSharp
    <colgroup>
    <col width="100%" />
    </colgroup>
    <thead>
    <tr class="header">
    <th align="left">C#</th>
    </tr>
    </thead>
    <tbody>
    <tr class="odd">
int newViewId = 0;</code></pre></td>
    </tr>
    </tbody>
    </table>
```

3.  В новом потоке заполните окно.

    При помощи метода [**CoreDispatcher.RunAsync**](https://msdn.microsoft.com/library/windows/apps/hh750317) запланируйте задачу в потоке пользовательского интерфейса для нового представления. Используйте [лямбда-выражение](http://go.microsoft.com/fwlink/p/?LinkId=389615), чтобы передать функцию методу **RunAsync** как аргумент. Результаты работы лямбда-функции влияют на поток нового представления.

    В XAML [**Frame**](https://msdn.microsoft.com/library/windows/apps/br242682) обычно добавляется к свойству [**Content**](https://msdn.microsoft.com/library/windows/apps/br209051) [**Window**](https://msdn.microsoft.com/library/windows/apps/br209041), а затем выполняется переход **Frame** к [**Page**](https://msdn.microsoft.com/library/windows/apps/br227503), где определено содержимое приложения. Дополнительная информация: [Навигация между двумя страницами](navigate-between-two-pages.md).

    После заполнения нового [**Window**](https://msdn.microsoft.com/library/windows/apps/br209041) необходимо вызвать метод [**Activate**](https://msdn.microsoft.com/library/windows/apps/br209046) **Window** для отображения **Window** позднее. Результаты влияют на поток нового представления, так что активируется новое **Window**.

    Наконец, скачайте [**Id**](https://msdn.microsoft.com/library/windows/apps/dn281120) нового используемого представления для его отображения позже. Результаты также влияют на поток нового представления, поэтому [**ApplicationView.GetForCurrentView**](https://msdn.microsoft.com/library/windows/apps/hh701672) получает **Id** нового представления.

    <span codelanguage="CSharp"></span>
```    CSharp
    <colgroup>
    <col width="100%" />
    </colgroup>
    <thead>
    <tr class="header">
    <th align="left">C#</th>
    </tr>
    </thead>
    <tbody>
    <tr class="odd">
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

```    CSharp
bool viewShown = await ApplicationViewSwitcher.TryShowAsStandaloneAsync(newViewId);</code></pre></td>
    </tr>
    </tbody>
    </table>
```

## <span id="The_main_view"></span><span id="the_main_view"></span><span id="THE_MAIN_VIEW"></span>Главное представление


Первое представление, создаваемое при запуске приложения, называется *главным*. Это представление хранится в свойстве [**CoreApplication.MainView**](https://msdn.microsoft.com/library/windows/apps/hh700465), и его свойство [**IsMain**](https://msdn.microsoft.com/library/windows/apps/hh700452) имеет значение true. Вам не нужно создавать это представление, его создает приложение. Поток главного представления служит диспетчером для приложения, и события активации в приложении происходят в этом потоке.

Если открыты вспомогательные представления, окно главного представления может быть скрыто, например, по нажатию кнопки закрытия (x) на панели заголовка окна, но его поток остается активным. Вызов [**Close**](https://msdn.microsoft.com/library/windows/apps/br209049) в [**Window**](https://msdn.microsoft.com/library/windows/apps/br209041) главного представления приведет к возникновению **InvalidOperationException**. (Чтобы закрыть приложение, используйте [**Application.Exit**](https://msdn.microsoft.com/library/windows/apps/br242327).) Если работа потока главного представления завершается, приложение закрывается.

## <span id="Secondary_views"></span><span id="secondary_views"></span><span id="SECONDARY_VIEWS"></span>Дополнительные представления


Другие представления, в том числе все представления, создаваемые по вызову [**CreateNewView**](https://msdn.microsoft.com/library/windows/apps/dn297278) в коде приложения, являются дополнительными представлениями. Как главное представление, так и дополнительные представления хранятся в коллекции [**CoreApplication.Views**](https://msdn.microsoft.com/library/windows/apps/br205861). Обычно дополнительные представления создаются в ответ на действия пользователя. В некоторых случаях система создает дополнительные представления для приложения.

**Примечание.** Вы можете использовать функцию *ограниченного доступа* Windows для запуска приложения в [режиме киоска](https://technet.microsoft.com/library/mt219050.aspx). После этого система создает дополнительное представление для представления пользовательского интерфейса приложения поверх экрана блокировки. Приложению не разрешается создавать дополнительные представления, поэтому при попытке отобразить собственное дополнительное представление в режиме киоска возникает исключение.

 

## <span id="Switch_from_one_view_to_another"></span><span id="switch_from_one_view_to_another"></span><span id="SWITCH_FROM_ONE_VIEW_TO_ANOTHER"></span>Переключение между представлениями


Необходимо предоставить пользователю возможность вернуться в главное окно из дополнительного. Для этого используйте метод [**ApplicationViewSwitcher.SwitchAsync**](https://msdn.microsoft.com/library/windows/apps/dn281097). Вызовите этот метод из потока окна, из которого вы переключаетесь, и передайте идентификатор представления окна, на которое вы переключаетесь.

<span codelanguage="CSharp"></span>
```CSharp
<colgroup>
<col width="100%" />
</colgroup>
<thead>
<tr class="header">
<th align="left">C#</th>
</tr>
</thead>
<tbody>
<tr class="odd">
await ApplicationViewSwitcher.SwitchAsync(viewIdToShow);</code></pre></td>
</tr>
</tbody>
</table>
```

Используя [**SwitchAsync**](https://msdn.microsoft.com/library/windows/apps/dn281097), можно определить, следует ли закрыть начальное окно и удалить его из панели задач, указав значение [**ApplicationViewSwitchingOptions**](https://msdn.microsoft.com/library/windows/apps/dn281105).

 

 



<!--HONumber=Aug16_HO3-->



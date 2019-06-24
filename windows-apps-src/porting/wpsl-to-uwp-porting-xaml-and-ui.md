---
description: Способ определения пользовательского интерфейса в виде декларативная разметка XAML преобразует чрезвычайно эффективно с Windows Phone Silverlight для приложений универсальной платформы Windows (UWP).
title: Перенос Windows Phone Silverlight XAML и пользовательский Интерфейс для универсальной платформы Windows
ms.assetid: 49aade74-5dc6-46a5-89ef-316dbeabbebe
ms.date: 02/08/2017
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: a72a4e7349a5d72b7081873814389c0924ca9308
ms.sourcegitcommit: 6f32604876ed480e8238c86101366a8d106c7d4e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/21/2019
ms.locfileid: "67322289"
---
#  <a name="porting-windowsphone-silverlight-xaml-and-ui-to-uwp"></a>Перенос Windows Phone Silverlight XAML и пользовательский Интерфейс для универсальной платформы Windows



Предыдущий раздел называется [Устранение неполадок](wpsl-to-uwp-troubleshooting.md).

Способ определения пользовательского интерфейса в виде декларативная разметка XAML преобразует чрезвычайно эффективно с Windows Phone Silverlight для приложений универсальной платформы Windows (UWP). Вы увидите, что после обновления ключевых ссылок на системный ресурс, изменения некоторых имен типов элементов и изменения clr-namespace на using большие участки вашей разметки стали совместимы. Большую часть императивного кода в ваших моделях представления уровня представления и код, который управляет элементами пользовательского интерфейса, также будет очень просто перенести.

## <a name="a-first-look-at-the-xaml-markup"></a>Начало работы с разметкой XAML

Предыдущей статье было показано, как скопировать в XAML и кода файлы в новых Windows 10 проект Visual Studio. Одна из первых проблем, которую вы могли заметить выделенной в конструкторе XAML Visual Studio, — это то, что элемент `PhoneApplicationPage` в корне файла XAML является недействительным для проекта UWP. В предыдущем разделе вы сохранили копию файлов XAML, которые создаются в Visual Studio при создании проекта Windows 10. Если вы откроете эту версию MainPage.xaml, то увидите в корне тип [**Page**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Page), который принадлежит пространству имен [**Windows.UI.Xaml.Controls**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls). Таким образом, вы можете изменить все элементы `<phone:PhoneApplicationPage>` на `<Page>` (не забывайте о синтаксисе элементов свойств) и можете удалить объявление `xmlns:phone`.

Более общий подход к. определение типа универсальной платформы Windows, соответствующий типу Windows Phone Silverlight, см. в [сопоставления пространства имен и класс](wpsl-to-uwp-namespace-and-class-mappings.md).

## <a name="xaml-namespace-prefix-declarations"></a>Объявления префикса пространства имен XAML


Если вы используете экземпляры настраиваемых типов в представлениях (возможно, экземпляр модели представления или преобразователь значений), в вашей разметке XAML будут присутствовать объявления префикса пространства имен. Синтаксис из них отличается от Windows Phone Silverlight и UWP. Далее приводятся некоторые примеры.

```xml
    xmlns:ContosoTradingCore="clr-namespace:ContosoTradingCore;assembly=ContosoTradingCore"
    xmlns:ContosoTradingLocal="clr-namespace:ContosoTradingLocal"
```

Измените clr-namespace на using и удалите все маркеры сборки и точки с запятой (сборка будет указана). Результат выглядит следующим образом.

```xml
    xmlns:ContosoTradingCore="using:ContosoTradingCore"
    xmlns:ContosoTradingLocal="using:ContosoTradingLocal"
```

У вас может быть ресурс, чей тип определяется системой.

```xml
    xmlns:System="clr-namespace:System;assembly=mscorlib"
    /* ... */
    <System:Double x:Key="FontSizeLarge">40</System:Double>
```

В UWP снимите объявление префикса "Система" и используйте вместо него (уже объявленный) префикс "x".

```xml
    <x:Double x:Key="FontSizeLarge">40</x:Double>
```

## <a name="imperative-code"></a>Императивный код


Ваши модели представления являются единственным местом, в котором находится императивный код, который ссылается на типы пользовательского интерфейса. Другая область представляет собой все файлы кода программной части, которые непосредственно обрабатывают элементы пользовательского интерфейса. Например, вы можете обнаружить, что строка кода подобная этой еще не скомпилируется.


```csharp
    return new BitmapImage(new Uri(this.CoverImagePath, UriKind.Relative));
```

**BitmapImage** в **System.Windows.Media.Imaging** пространства имен в Windows Phone Silverlight и с помощью директивы в одном файле **BitmapImage** использоваться без пространства имен Квалификация как в приведенном выше фрагменте кода. В подобном случае можно щелкнуть правой кнопкой мыши имя типа (**BitmapImage**) в Visual Studio и с помощью команды контекстного меню **Разрешить** добавить новую директиву пространства имен в файл. В этом случае добавляется пространство имен [**Windows.UI.Xaml.Media.Imaging**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.Imaging), в котором находится тип в UWP. Вы можете удалить директиву using **System.Windows.Media.Imaging**, и этого будет достаточно для переноса кода, подобного коду в приведенном выше фрагменте. Когда все будет готово, будут удалены все пространства имен Windows Phone Silverlight.

В простых случаях, подобных этому, когда вы сопоставляете типы в старом пространстве имен с такими же типами в новом, можно использовать команду Visual Studio **Поиск и замена** для внесения массовых изменений в исходный код. Команда **Разрешить** является отличным способом обнаружения нового пространства имен для типа. Еще один пример: вы можете заменить все сочетания System.Windows на Windows.UI.Xaml. Это перенесет большую часть всех директив using и все полные имена типов, которые ссылаются на это пространство имен.

После того как все старые директивы using будут удалены, а новые добавлены, можно использовать команду Visual Studio **Упорядочение Using**, чтобы отсортировать директивы и удалить неиспользуемые.

Иногда правка императивного кода требует всего лишь изменения типа параметров. В других случаях необходимо будет использовать интерфейсы API универсальной платформы Windows вместо .NET API-интерфейсы для Windows 8.x приложения для среды выполнения. Чтобы определить какие API поддерживаются, использовать оставшуюся часть этого руководство по переносу в сочетании с [Обзор приложений .NET для выполнения Windows 8.x](https://docs.microsoft.com/previous-versions/windows/apps/br230302(v=vs.140)) и [на среды выполнения Windows](https://docs.microsoft.com/uwp/api/).

А если вы просто хотите дойти до этапа сборки проекта, можно закомментировать или снабдить заглушками фрагменты ненужного кода. Затем выполните итерацию, одна проблема одновременно и в следующих статьях в этом разделе (и в предыдущем разделе: [Устранение неполадок](wpsl-to-uwp-troubleshooting.md)), пока не порта завершения, и проблем сборки и выполнения ironed-out.

## <a name="adaptiveresponsive-ui"></a>Адаптивный пользовательский интерфейс с быстрым откликом

Так как приложение Windows 10 может выполняться на потенциально широкий спектр устройств, каждый из которых свой собственный размер и разрешение экрана — вы захотите выйти за рамки минимальной действия по портировать свое приложение и нужно будет настраивать пользовательский Интерфейс для поиска мы рекомендуем на этих устройствах. Вы можете использовать адаптивную функцию "Диспетчер визуальных состояний", чтобы динамически определять размер окна и изменять макет. Пример того, как это сделать, представлен в разделе [Адаптивный пользовательский интерфейс](wpsl-to-uwp-case-study-bookstore2.md) в примере Bookstore2.

## <a name="alarms-and-reminders"></a>Предупреждения и напоминания

Код, использующий классы **Alarm** или **Reminder**, необходимо перенести, чтобы использовать класс [**BackgroundTaskBuilder**](https://docs.microsoft.com/uwp/api/Windows.ApplicationModel.Background.BackgroundTaskBuilder) для создания и регистрации фоновой задачи и отображения всплывающих уведомлений в соответствующее время. См. разделы [Фоновая обработка](wpsl-to-uwp-business-and-data.md) и [Всплывающие уведомления](#toasts).

## <a name="animation"></a>Анимация

Теперь приложениям UWP доступна библиотека анимаций UWP в качестве предпочитаемой альтернативы анимации по ключевым кадрам и анимации "из/в". Эти анимации разработаны и настроены так, чтобы плавно выполняться, отлично выглядеть и придавать вашему приложению единый с Windows вид как у встроенных приложений. См. в разделе [краткое руководство: Анимация пользовательского интерфейса с помощью библиотеки анимации](https://docs.microsoft.com/previous-versions/windows/apps/hh452703(v=win.10)).

Если вы используете в своих приложениях UWP анимацию по ключевым кадрам и анимацию "из/в", вы, возможно, захотите понять разницу между независимой и зависимой анимацией, которую представляет новая платформа. См. раздел [Оптимизация анимаций и мультимедиа](https://docs.microsoft.com/windows/uwp/debug-test-perf/optimize-animations-and-media). Анимации, которые выполняются в потоке пользовательского интерфейса (те, которые анимируют свойства макета, например), известны как зависимые анимации, и при запуске на новой платформе они не будут работать, если вы не выполните одно из двух действий. Вы можете задать им для анимирования различные свойства, например [**RenderTransform**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.uielement.rendertransform), таким образом делая их независимыми. Или вы можете установить `EnableDependentAnimation="True"` для элемента анимации, чтобы подтвердить ваше намерение выполнить анимацию, работа которой не может быть гарантированно плавной. Если вы используете Blend для Visual Studio для создания новых анимаций, это свойство будет установлено для вас там, где это необходимо.

## <a name="back-button-handling"></a>Работа с кнопкой "Назад"

В приложении Windows 10 можно использовать единый подход к обработке "Назад", и он будет работать на всех устройствах. На мобильных устройствах эта кнопка выполняет роль емкостной кнопки на устройстве или кнопки в оболочке. На настольном ПК кнопка добавляется во внешнее оформление приложения, когда в приложении возможны переходы назад, и отображается в строке заголовка оконных приложений или на панели задач для режима планшета. Событие кнопки "Назад" — это универсальная концепция во всех семействах устройств, и кнопки, реализованные в аппаратном или программном обеспечении, создают одно и то же событие [**BackRequested**](https://docs.microsoft.com/uwp/api/windows.ui.core.systemnavigationmanager.backrequested).

Приведенный ниже пример работает для всех семейств устройств и хорошо подходит в случаях, когда ко всем страницам применяется один и тот же метод обработки и не нужно подтверждать переходы (например, для предупреждения о несохраненных изменениях).

```csharp
   // app.xaml.cs

    protected override void OnLaunched(LaunchActivatedEventArgs e)
    {
        [...]

        Windows.UI.Core.SystemNavigationManager.GetForCurrentView().BackRequested += App_BackRequested;
        rootFrame.Navigated += RootFrame_Navigated;
    }

    private void RootFrame_Navigated(object sender, NavigationEventArgs e)
    {
        Frame rootFrame = Window.Current.Content as Frame;

        // Note: On device families that have no title bar, setting AppViewBackButtonVisibility can safely execute 
        // but it will have no effect. Such device families provide a back button UI for you.
        if (rootFrame.CanGoBack)
        {
            Windows.UI.Core.SystemNavigationManager.GetForCurrentView().AppViewBackButtonVisibility = 
                Windows.UI.Core.AppViewBackButtonVisibility.Visible;
        }
        else
        {
            Windows.UI.Core.SystemNavigationManager.GetForCurrentView().AppViewBackButtonVisibility = 
                Windows.UI.Core.AppViewBackButtonVisibility.Collapsed;
        }
    }

    private void App_BackRequested(object sender, Windows.UI.Core.BackRequestedEventArgs e)
    {
        Frame rootFrame = Window.Current.Content as Frame;

        if (rootFrame.CanGoBack)
        {
            rootFrame.GoBack();
        }
    }
```

Существует также единый подход для всех семейств устройств для программного выхода из приложения.

```csharp
   Windows.UI.Xaml.Application.Current.Exit();
```

## <a name="binding-and-compiled-bindings-with-xbind"></a>Привязка и скомпилированные привязки с {x:Bind}

Раздел "Привязка" описывает следующие аспекты.

-   Привязка элемента пользовательского интерфейса к "данным" (то есть к свойствам и командам модели представления)
-   Привязка элемента пользовательского интерфейса к другому элементу пользовательского интерфейса
-   Написание модели представления, которая является наблюдаемой (то есть она создает уведомления при изменении значения свойств и при изменении доступности команд)

Все эти аспекты в основном поддерживаются, но пространства имен различаются. Например, **System.Windows.Data.Binding** сопоставляется с [**Windows.UI.Xaml.Data.Binding**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Data.Binding), **System.ComponentModel.INotifyPropertyChanged** сопоставляется с [**Windows.UI.Xaml.Data.INotifyPropertyChanged**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Data.INotifyPropertyChanged) и **System.Collections.Specialized.INotifyPropertyChanged** сопоставляется с [**Windows.UI.Xaml.Interop.INotifyCollectionChanged**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Interop.INotifyCollectionChanged).

Не может быть привязана панелей приложения Windows Phone Silverlight и кнопок панели приложения, как они могут в приложении UWP. Вы можете использовать императивный код, который создает панель приложения и ее кнопки, привязывает их к свойствам и локализованным строкам и обрабатывает их события. В этом случае у вас есть возможность перенести данный императивный код, заменив его декларативной разметкой, привязанной к свойствам и командам, и статическими ссылками на ресурсы, что сделает ваше приложение более безопасным и удобным в обслуживании. Для привязки и оформления кнопок панели приложения UWP, как и любого другого элемента XAML, можно использовать Visual Studio или Blend для Visual Studio. Обратите внимание, что в приложении UWP имена используемых вами типов — [**CommandBar**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.CommandBar) и [**AppBarButton**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.AppBarButton).

Связанные с привязкой функции приложений UWP сейчас имеют следующие ограничения:

-   Отсутствует встроенная поддержка проверки вводимых данных и интерфейсов [**IDataErrorInfo**](https://docs.microsoft.com/dotnet/api/system.componentmodel.idataerrorinfo?redirectedfrom=MSDN) и [**INotifyDataErrorInfo**](https://docs.microsoft.com/dotnet/api/system.componentmodel.inotifydataerrorinfo?redirectedfrom=MSDN).
-   [ **Привязки** ](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Data.Binding) класс не поддерживает расширенные свойства форматирования, доступные в Windows Phone Silverlight. Тем не менее для предоставления пользовательского форматирования все еще можно использовать [**IValueConverter**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Data.IValueConverter).
-   Методы [**IValueConverter**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Data.IValueConverter) принимают в качестве параметров языковые строки, а не объекты [**CultureInfo**](https://docs.microsoft.com/dotnet/api/system.globalization.cultureinfo?redirectedfrom=MSDN).
-   Класс [**CollectionViewSource**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Data.CollectionViewSource) не имеет встроенной поддержки сортировки и фильтрации, а группирование работает по-другому. Дополнительную информацию см. в разделах [Подробно о привязке данных](https://docs.microsoft.com/windows/uwp/data-binding/data-binding-in-depth) и [Образец привязки данных](https://go.microsoft.com/fwlink/p/?linkid=226854).

Несмотря на то, что по-прежнему во многом поддерживаются те же функции привязки, Windows 10 предлагает параметр нового и более высокую производительность привязки механизм, именуемый скомпилированные привязок, которые используют расширение разметки {x: Bind}. См. в разделе [привязки данных: Улучшения производительности приложений посредством новые улучшения привязка данных XAML](https://channel9.msdn.com/Events/Build/2015/3-635)и [x: Bind образец](https://go.microsoft.com/fwlink/p/?linkid=619989).

## <a name="binding-an-image-to-a-view-model"></a>Привязка изображения к модели представления

Свойство [**Image.Source**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.image.source) можно привязать к любому свойству модели представления типа [**ImageSource**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.ImageSource). Вот типичная реализация такого свойства в приложении Windows Phone Silverlight:

```csharp
    // this.BookCoverImagePath contains a path of the form "/Assets/CoverImages/one.png".
    return new BitmapImage(new Uri(this.CoverImagePath, UriKind.Relative));
```

В приложении UWP используется [схема URI](https://docs.microsoft.com/previous-versions/windows/apps/jj655406(v=win.10)) ms-appx. Чтобы сохранить остальную часть кода такой же, можно использовать другую перегрузку конструктора **System.Uri**, чтобы поместить схему URI ms-appx в базовый URI и добавить в него оставшуюся часть пути. Пример.

```csharp
    // this.BookCoverImagePath contains a path of the form "/Assets/CoverImages/one.png".
    return new BitmapImage(new Uri(new Uri("ms-appx://"), this.CoverImagePath));
```

Таким образом, остальная часть модели представления, значения пути в свойстве пути изображения и привязки в разметке XAML могут оставаться абсолютно такими же.

## <a name="controls-and-control-stylestemplates"></a>Элементы управления, стили и шаблоны элементов управления

Приложения Windows Phone Silverlight используют элементов управления, определенных в **Microsoft.Phone.Controls** пространства имен и **System.Windows.Controls** пространства имен. Приложения XAML UWP используют элементы управления, определенные в пространстве имен [**Windows.UI.Xaml.Controls**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls). Архитектура и Разработка элементов управления XAML в UWP — это практически так же, как элементы управления Windows Phone Silverlight. Но в них был внесены некоторые изменения для улучшения набора доступных элементов управления и объединениях их с приложениями Windows. Вот несколько конкретных примеров.

| Название элемента управления | Изменение |
|--------------|--------|
| ApplicationBar | Свойство [Page.TopAppBar](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.page.topappbar). |
| ApplicationBarIconButton | Эквивалентом в UWP является свойство [Glyph](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.fonticon.glyph). PrimaryCommands является свойством содержимого CommandBar. Анализатор XAML интерпретирует внутренний xml элемента как значение его свойства содержимого. |
| ApplicationBarMenuItem | Эквивалентом в UWP является элемент [AppBarButton.Label](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.appbarbutton.label), для которого настроен текст элемента меню. |
| ContextMenu (в наборе средств Windows Phone) | Для простого всплывающего элемента используйте [Flyout](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Flyout). |
| Класс ControlTiltEffect.TiltEffect | Анимации из библиотеки анимации UWP встроены в используемые по умолчанию стили стандартных элементов управления. См. [Анимация действий указателя](https://docs.microsoft.com/previous-versions/windows/apps/jj649432(v=win.10)). |
| LongListSelector со сгруппированными данными | Функции Windows Phone Silverlight LongListSelector двумя способами, которые могут использоваться совместно. Во-первых, она может отображать данные, которые сгруппированы по ключу (например, список имен, сгруппированный по первой букве). Во-вторых, она может переключаться между двумя контекстными представлениями: сгруппированный список элементов (например, имена) и список только самих ключей группы (например, первые буквы). В UWP сгруппированные данные можно отобразить с помощью элементов [Руководство по элементам управления просмотром в виде списка и сетки](https://docs.microsoft.com/windows/uwp/controls-and-patterns/lists). |
| LongListSelector с неструктурированными данными | Для повышения производительности в случае очень длинных списках LongListSelector рекомендуется вместо окно списка Windows Phone Silverlight, даже для неструктурированных, не сгруппированных данных. В приложении UWP для длинных списков элементов предпочтительнее использовать [GridView](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.GridView) независимо от того, поддаются данные группировке или нет. |
| Panorama | Элемент управления Windows Phone Silverlight Панорама сопоставляется [рекомендации для элементов управления hub в приложениях среды выполнения Windows 8.x](https://docs.microsoft.com/windows/uwp/controls-and-patterns/hub) и рекомендации для элемента управления hub. <br/> Обратите внимание: элемент управления Panorama окружен текстом от последнего раздела до первого, а его фоновое изображение перемещается в параллаксе относительно разделам. Разделы [Hub](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Hub) не создают обтекание текстом, и параллакс не используется. |
| Сводка | Эквивалентом UWP элемента управления Windows Phone Silverlight Pivot является [Windows.UI.Xaml.Controls.Pivot](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Pivot). Эта функция доступна для всех семейств устройств. |

**Примечание**    PointerOver визуальное состояние применима в пользовательские стили и шаблоны в приложениях Windows 10, но не в приложениях Windows Phone Silverlight. Это не единственная причина, почему существующие пользовательские стили и шаблоны могут оказаться пригодными для приложений Windows 10, включая ключи системных ресурсов, вы используете, изменения в наборы использовать визуальные состояния и повысить его производительность стили по умолчанию Windows 10 / шаблоны. Мы рекомендуем изменить новую копию шаблона элемента управления по умолчанию для Windows 10 и затем повторно применить настройку стиля и шаблона.

Дополнительные сведения об элементах управления UWP см. в разделах [Распределение элементов управления по функциям](https://docs.microsoft.com/windows/uwp/controls-and-patterns/controls-by-function), [Список элементов управления](https://docs.microsoft.com/windows/uwp/design/controls-and-patterns/) и [Руководство по элементам управления](https://docs.microsoft.com/windows/uwp/design/controls-and-patterns/index).

##  <a name="design-language-in-windows10"></a>Язык проектирования в Windows 10

Существуют некоторые различия в язык проектирования приложений Silverlight Windows Phone и приложений Windows 10. Все сведения см. в разделе [Дизайн](https://developer.microsoft.com/en-us/windows/apps/design). Несмотря на изменения языка дизайна, принципы дизайна остаются прежними: будьте внимательны к деталям, при этом всегда стремитесь к простоте и фокусируйте внимание на содержимом, а не внешнем оформлении, уменьшая количество визуальных элементов и сохраняя характерные особенности цифрового домена, используйте визуальную иерархию (особенно для шрифтового оформления), проектируйте, используя сетку, оживляйте интерфейс с помощью плавных анимаций.

## <a name="localization-and-globalization"></a>Локализация и глобализация

Для локализованных строк можно повторно использовать RESX-файл из проекта Windows Phone Silverlight в проекте приложения универсальной платформы Windows. Скопируйте файл, добавьте его в проект и измените его расширение на Resources.resw, чтобы механизм поиска находил его по умолчанию. Установите **Действие при сборке** на **PRIResource**, а **Копировать в выходной каталог** на **Не копировать**. Затем можно использовать строки в разметке, указывая атрибут **x:Uid** в элементах XAML. См. в разделе [краткое руководство: Использование строковых ресурсов](https://docs.microsoft.com/previous-versions/windows/apps/hh965329(v=win.10)).

Приложения Windows Phone Silverlight используют **CultureInfo** класс для глобализации приложения. Приложения UWP используют современную технологию ресурсов (MRT), которая поддерживает динамическую загрузку ресурсов приложения (локализации, масштабирования и темы) и в среду выполнения и в рабочую область конструирования Visual Studio. Дополнительные сведения см. в разделе [Руководство по файлам, данным и глобализации](https://docs.microsoft.com/windows/uwp/design/usability/index).

В разделе [**ResourceContext.QualifierValues**](https://docs.microsoft.com/uwp/api/windows.applicationmodel.resources.core.resourcecontext.qualifiervalues) описывается загрузка ресурсов для определенного семейства устройств с учетом коэффициента выбора ресурсов для семейств устройств.

## <a name="media-and-graphics"></a>Мультимедиа и графика

Читая о мультимедиа и изображениях UWP учитывайте, что принципы разработки Windows поощряют максимальное уменьшение всего излишнего, включая графическую сложность и излишества. Разработка Windows типизирована чистыми и четкими визуальными элементами, шрифтовым оформлением и перемещением. Если ваше приложение будет следовать тем же принципам, оно будет больше похоже на встроенные приложения.

Windows Phone Silverlight имеет **RadialGradientBrush** тип, который не отображается в приложениях UWP, хотя другие [ **кисть** ](/uwp/api/Windows.UI.Xaml.Media.Brush) типы. В некоторых случаях вы сможете достичь аналогичного эффекта с помощью растрового изображения. Обратите внимание, что вы можете [создать радиальную градиентную кисть](https://docs.microsoft.com/windows/desktop/Direct2D/how-to-create-a-radial-gradient-brush) с помощью Direct2D в [Microsoft DirectX](https://docs.microsoft.com/windows/desktop/directx) и XAML C++ UWP.

Windows Phone Silverlight имеет **System.Windows.UIElement.OpacityMask** свойство, но что если свойство не является членом UWP [**UIElement** ](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.UIElement) типа. В некоторых случаях вы сможете достичь аналогичного эффекта с помощью растрового изображения. Вы также можете [создать маску непрозрачности](https://docs.microsoft.com/windows/desktop/Direct2D/opacity-masks-overview) с помощью Direct2D в [Microsoft DirectX](https://docs.microsoft.com/windows/desktop/directx) и приложении UWP XAML C++. Но распространенным вариантом использования для **OpacityMask** является применение единого растрового изображения, адаптирующегося к светлой и темной темам. Для векторной графики можно использовать системные кисти, учитывающие тему (например, показанные ниже круговые диаграммы). Но создание растрового изображения, учитывающего тему (например, показанные ниже флажки), требует другого подхода.

![Учитывающее тему растровое изображение](images/wpsl-to-uwp-case-studies/wpsl-to-uwp-theme-aware-bitmap.png)

В приложении Windows Phone Silverlight, заключается в использовании маску альфа-канала (в виде растрового изображения) как **OpacityMask** для **прямоугольник** заполненный кисть переднего плана:

```xml
    <Rectangle Fill="{StaticResource PhoneForegroundBrush}" Width="26" Height="26">
        <Rectangle.OpacityMask>
            <ImageBrush ImageSource="/Assets/wpsl_check.png"/>
        </Rectangle.OpacityMask>
    </Rectangle>
```

Самый простой способ перенести его в приложение UWP — использовать [**BitmapIcon**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.BitmapIcon), например:

```xml
    <BitmapIcon UriSource="Assets/winrt_check.png" Width="21" Height="21"/>
```

В данном случае — winrt\_check.png является маску альфа-канала в виде растрового изображения так же, как wpsl\_является check.png и очень хорошо, это может быть тот же файл. Тем не менее, может потребоваться предоставить несколько различных размеров winrt\_check.png для разные коэффициенты масштабирования. Дополнительные сведения об этом и описание изменений в значениях **Width** и **Height** см. в подразделе [Пиксели представления или эффективные пиксели, расстояние от экрана и коэффициенты масштабирование](#view-or-effective-pixels-viewing-distance-and-scale-factors) в этом разделе.

Более общим способом, который подходит в случае различия между оформлением растрового изображения в светлой и темной теме, является использование двух ресурсов изображений — одного с темным передним планом (для светлой темы) и второго со светлым передним планом (для темной темы). Дополнительные сведения о том, как имя этого набора средств растрового изображения, см. в разделе [адаптировать ресурсы для языка, масштаба и квалификаторов](../app-resources/tailor-resources-lang-scale-contrast.md). После присвоения правильного имени набору файлов изображений вы можете ссылаться на них в общем с помощью их имени корневого элемента, например:

```xml
    <Image Source="Assets/winrt_check.png" Stretch="None"/>
```

В Windows Phone Silverlight **UIElement.Clip** свойство может быть любая форма, можно выразить с помощью **Geometry** и обычно сериализуется в разметку XAML в **StreamGeometry** мини-язык. В UWP тип свойства [**Clip**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.uielement.clip) — [**RectangleGeometry**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.RectangleGeometry), поэтому вы можете только вырезать прямоугольную область. Разрешение определить прямоугольник с помощью мини-языка будет слишком вольным. Таким образом, чтобы перенести область обрезки в разметку, замените синтаксис атрибута **Clip** и выполните его в синтаксисе элемента свойства следующим образом:

```xml
    <UIElement.Clip>
        <RectangleGeometry Rect="10 10 50 50"/>
    </UIElement.Clip>
```

Обратите внимание, что вы можете [использовать произвольную геометрию в качестве маски в уровне](https://docs.microsoft.com/windows/desktop/Direct2D/direct2d-layers-overview) с Direct2D в [Microsoft DirectX](https://docs.microsoft.com/windows/desktop/directx) C++ XAML приложении UWP.

## <a name="navigation"></a>Навигация

При переходе на страницу в приложении Windows Phone Silverlight, можно использовать схему адресации универсальный код ресурса (URI):

```csharp
    NavigationService.Navigate(new Uri("/AnotherPage.xaml", UriKind.Relative)/*, navigationState*/);
```

В приложении UWP вызовите метод [**Frame.Navigate**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.frame.navigate) и укажите тип страницы назначения (определяется атрибутом **x:Class** определения разметки XAML данной страницы):


```csharp
    // In a page:
    this.Frame.Navigate(typeof(AnotherPage)/*, parameter*/);

    // In a view model, perhaps inside an ICommand implementation:
    var rootFrame = Windows.UI.Xaml.Window.Current.Content as Windows.UI.Xaml.Controls.Frame;
    rootFrame.Navigate(typeof(AnotherPage)/*, parameter*/);
```

Определить начальную страницу для приложения Windows Phone Silverlight в WMAppManifest.xml:

```xml
    <DefaultTask Name="_default" NavigationPage="MainPage.xaml" />
```

В приложении UWP для определения стартовой страницы используется императивный код. Вот код из файла App.xaml.cs, в котором это проиллюстрировано:

```csharp
    if (!rootFrame.Navigate(typeof(MainPage), e.Arguments))
```

Сопоставление URI и навигация по фрагментам — это методы навигации URI, поэтому они неприменимы к навигации UWP, которая не базируется на URI. Сопоставление URI существует в ответ на слабо типизированную природу определения страницы назначения с помощью строки URI, что может привести к проблемам хрупкости и поддержки, если страница переместится в другую папку и, соответственно, на другой относительный путь. Приложения UWP Windows используют навигацию на основе типов, которая строго типизирована и проверяется компилятором, а также не имеет проблемы, которую решает сопоставление URI. Вариант использования для навигации по фрагментам — передать определенный контекст на странице назначения, чтобы страница могла представлять определенный фрагмент содержимого для прокрутки или отображать его иным образом. Альтернативным способом является передача параметра навигации при вызове метода [**Navigate**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.frame.navigate).

Подробнее: [Навигация](https://docs.microsoft.com/windows/uwp/layout/navigation-basics).

## <a name="resource-key-reference"></a>Ссылка на ключ ресурса

Язык проектирования развивалась для Windows 10 и поэтому были изменены некоторые системные стили и много ключей ресурсов системы были удалены или переименованы. Редактор разметки XAML в Visual Studio выделяет ссылки на ключи ресурсов, которые не могут быть разрешены. Например, редактор разметки XAML подчеркнет ссылку к ключу стиля `PhoneTextNormalStyle` волнистой линией красного цвета. Если она не будет исправлена, приложение немедленно завершит работу при попытке развернуть его в эмуляторе или на устройстве. Поэтому важно обращать внимание на правильность разметки XAML. И вы увидите, что Visual Studio — это отличный инструмент для нахождения подобных проблем.

См. также раздел [Текст](#text) ниже.

## <a name="status-bar-system-tray"></a>Строка состояния (панель задач)

Панель задач (установленная в разметке XAML с `shell:SystemTray.IsVisible`) теперь называется строкой состояния и отображается по умолчанию. Вы можете контролировать ее видимость в императивном коде, вызывая методы [**Windows.UI.ViewManagement.StatusBar.ShowAsync**](https://docs.microsoft.com/uwp/api/windows.ui.viewmanagement.statusbar.showasync) и [**HideAsync**](https://docs.microsoft.com/uwp/api/windows.ui.viewmanagement.statusbar.hideasync).

## <a name="text"></a>Text

Текст (или шрифтовое оформление) является важным аспектом для приложения UWP. Во время переноса, возможно, необходимо будет пересмотреть визуальные элементы в представлениях, чтобы они соответствовали новому языку дизайна. Используйте эти иллюстрации, чтобы найти стили системы UWP  **TextBlock**, которые доступны. Найти те, которые соответствуют стили Windows Phone Silverlight, которые вы использовали. Кроме того можно создавать собственные универсальные стили и скопировать свойства из системы стилей Windows Phone Silverlight в биты.

![стили системы textblock для приложений для windows 10](images/label-uwp10stylegallery.png)

Системные стили TextBlock для приложений Windows 10

В приложении Windows Phone Silverlight семейство шрифтов по умолчанию — Segoe WP. В приложении Windows 10 семейство шрифтов по умолчанию — Segoe UI. В результате метрика шрифта в приложении может выглядеть иначе. Если вы хотите воспроизвести внешний вид текста Windows Phone Silverlight, можно задать собственные метрики, такие как с помощью свойств [ **LineHeight** ](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.textblock.lineheight) и [ **LineStackingStrategy** ](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.textblock.linestackingstrategy). Дополнительные сведения см. в разделе [Рекомендации по шрифтам](https://docs.microsoft.com/windows/uwp/controls-and-patterns/fonts) и [Оформление приложений UWP](https://developer.microsoft.com/en-us/windows/apps/design).

## <a name="theme-changes"></a>Изменения тем

Для приложения Windows Phone Silverlight тему по умолчанию темно-по умолчанию. Для устройств Windows 10 тему по умолчанию был изменен, но вы можете управлять используемой темы, объявив запрошенной темы в файле App.xaml. Например, чтобы на всех устройствах отображалась темная тема, добавьте `RequestedTheme="Dark"` к корневому элементу Application.

## <a name="tiles"></a>Плитки

Плитки для приложений универсальной платформы Windows имеют поведения, подобно приложениям Live плитки для Windows Phone Silverlight, несмотря на то, что существуют некоторые различия. Например, код, который вызывает метод **Microsoft.Phone.Shell.ShellTile.Create** для создания вспомогательных плиток, должен быть перенесен для вызова [**SecondaryTile.RequestCreateAsync**](https://docs.microsoft.com/uwp/api/windows.ui.startscreen.secondarytile.requestcreateasync). Ниже приведен пример до и после, сначала версии Windows Phone Silverlight:


```csharp
    var tileData = new IconicTileData()
    {
        Title = this.selectedBookSku.Title,
        WideContent1 = this.selectedBookSku.Title,
        WideContent2 = this.selectedBookSku.Author,
        SmallIconImage = this.SmallIconImageAsUri,
        IconImage = this.IconImageAsUri
    };

    ShellTile.Create(this.selectedBookSku.NavigationUri, tileData, true);
```

И эквивалент UWP:

```csharp
    var tile = new SecondaryTile(
        this.selectedBookSku.Title.Replace(" ", string.Empty),
        this.selectedBookSku.Title,
        this.selectedBookSku.ArgumentString,
        this.IconImageAsUri,
        TileSize.Square150x150);

    await tile.RequestCreateAsync();
```

Код, обновляющий плитку с помощью метода **Microsoft.Phone.Shell.ShellTile.Update** или класса **Microsoft.Phone.Shell.ShellTileSchedule**, должен быть перенесен для использования класса [**TileUpdateManager**](https://docs.microsoft.com/uwp/api/Windows.UI.Notifications.TileUpdateManager), [**TileUpdater**](https://docs.microsoft.com/uwp/api/Windows.UI.Notifications.TileUpdater), [**TileNotification**](https://docs.microsoft.com/uwp/api/Windows.UI.Notifications.TileNotification) и/или [**ScheduledTileNotification**](https://docs.microsoft.com/uwp/api/Windows.UI.Notifications.ScheduledTileNotification).

Дополнительные сведения о плитках, всплывающих уведомлениях, индикаторах событий, баннерах и уведомлениях см. в разделах [Создание плиток](https://docs.microsoft.com/previous-versions/windows/apps/hh868260(v=win.10)) и [Работа с плитками, индикаторами событий и всплывающими уведомлениями](https://docs.microsoft.com/previous-versions/windows/apps/hh868259(v=win.10)). Особенности размеров визуальных ресурсов, используемых для плиток UWP см. в разделе [Визуальные активы плиток и всплывающих уведомлений](https://docs.microsoft.com/previous-versions/windows/apps/hh781198(v=win.10)).

## <a name="toasts"></a>Всплывающие уведомления

Код, отображающий всплывающее уведомление с помощью класса **Microsoft.Phone.Shell.ShellToast**, должен быть перенесен для использования класса [**ToastNotificationManager**](https://docs.microsoft.com/uwp/api/Windows.UI.Notifications.ToastNotificationManager), [**ToastNotifier**](https://docs.microsoft.com/uwp/api/Windows.UI.Notifications.ToastNotifier), [**ToastNotification**](https://docs.microsoft.com/uwp/api/Windows.UI.Notifications.ToastNotification) или [**ScheduledToastNotification**](https://docs.microsoft.com/uwp/api/Windows.UI.Notifications.ScheduledToastNotification). Обратите внимание, что на мобильных устройствах общеупотребительным синонимом "всплывающего уведомления" является "баннер".

См. раздел [Работа с плитками, индикаторами событий и всплывающими уведомлениями](https://docs.microsoft.com/previous-versions/windows/apps/hh868259(v=win.10)).

## <a name="view-or-effective-pixels-viewing-distance-and-scale-factors"></a>Пиксели представления или эффективные пиксели, расстояние от экрана и коэффициенты масштабирование

Приложения для Windows Phone Silverlight и Windows 10 различается способ их абстрактный, размера и размещения элементов пользовательского интерфейса от фактического физический размер и разрешение устройств. Приложения Windows Phone Silverlight использует представление пикселей для этого. С Windows 10 понятие представления пикселей преобразованы в, действующие пикселей. Вот описание этого термина, его значения и преимуществ.

Термин «разрешение» относится к измерению плотности пикселей, а не, как обычно считается, количества пикселей. «Эффективное разрешение» — это способ, которым физические пиксели, образующие изображение или глиф разрешают для глаза данные различия в расстоянии от экрана и физическом размере пикселей устройства (плотность пикселей обратна физическому размеру пикселей). Эффективное разрешение — это хорошая метрика для создания взаимодействия на ее основе, поскольку оно ориентируется на пользователя. Поняв все факторы и контролируя размер элементов пользовательского интерфейса, вы можете сделать взаимодействие с пользователем комфортным.

Приложение Windows Phone Silverlight все экраны телефонов, точно 480 пикселов представления, без исключения, независимо от того, какое количество физических пикселей имеет экрана, а также новые возможности ее зависимости от плотности пикселей или физический размер. Это означает, что **изображение** элемент с `Width="48"` будет ровно одной десятой от ширины экрана любой телефон, который можно запустить приложение Windows Phone Silverlight.

В приложении Windows 10, это *не* так, что все устройства, некоторые фиксированное число действующие пикселей в ширину. Скорее всего это очевидно с учетом диапазона ширины различных устройств, в которых можно выполнить приложение UWP. Количество эффективных пикселей в ширину разное для различных устройств, от 320 epx для самых маленьких устройств и до 1024 epx для мониторов скромных размеров и выходит далеко за этот предел. Вам потребуется всего лишь продолжать использовать элементы с автоматически устанавливаемым размером и динамические панели макета, как и раньше. В некоторых случаях в свойствах элементов пользовательского интерфейса в разметке XAML задается фиксированный размер. Коэффициент масштабирования автоматически применяется к приложению в зависимости от устройства, на котором оно запускается, и от настроенных пользователем параметров экрана. Коэффициент масштабирования служит для сохранения представления элементами пользовательского интерфейса цели сенсорного ввода (и чтения) с более или менее постоянным размером для пользователя устройств с различными размерами экрана. А благодаря динамическому макету вместо визуального масштабирования пользовательского интерфейса на разных устройствах, он будет вмещать желаемый объем содержимого в доступное пространство.

Поскольку 480 ранее называлась фиксированной ширины в представлении пикселей для экрана телефона среднего размера и это значение теперь обычно меньше, в пикселях эффективным, правило будет умножить коэффициентом 0,8 любого измерения в разметке приложения Windows Phone Silverlight.

Чтобы ваше приложение будет наилучшим образом выглядело на любых устройствах, мы рекомендуем создать каждый растровый ресурс в различных размерах для определенного коэффициента масштабирования. Если вы предоставите ресурсы с масштабом 100 %, 200 % и 400 % (в такой последовательности), это обеспечит отличные результаты в большинстве случаев при любых промежуточных коэффициентах масштабирования.

**Примечание**  Если для какой-либо причине вы невозможно создание ресурсов в более чем один размер, а затем создать масштаба 100%. В Microsoft Visual Studio шаблон проекта по умолчанию для приложений UWP предоставляет ресурсы официальной фирменной символики (изображения плиток и эмблемы) только в одном размере, но масштаб при этом не 100 %. При создании ресурсов для своего приложения следуйте рекомендациям в этом разделе и предусмотрите размеры 100 %, 200 % и 400 %, а также используйте пакеты ресурсов.

В случае использования сложного изображения, возможно, следует предусмотреть еще больше размеров. Если используются векторные изображения, тогда процесс создания высококачественных ресурсов при любом масштабе относительно прост.

Мы не рекомендуем попытаться поддерживают все коэффициенты масштабирования, что полный список коэффициенты масштабирования для приложений Windows 10 — 100%, 125%, 150%, 200%, 250%, 300% до 400%. Если вы все-таких их предоставляете, Магазин выберет правильные ресурсы для каждого устройства, и только они будут загружены. Магазин выбирает ресурсы для загрузки на основе DPI устройства.

Подробнее см. в разделе [Адаптивный дизайн 101 для приложений UWP](https://docs.microsoft.com/windows/uwp/layout/screen-sizes-and-breakpoints-for-responsive-design).

## <a name="window-size"></a>Размер окна

В приложении UWP можно задать минимальный размер окна (ширину и высоту) с помощью императивного кода. Стандартный минимальный размер — 500 x 320 эфф. пикселей. Это также самый маленький из допустимых минимальных размеров. Самый большой из допустимых минимальных размеров — 500 x 500 эффективных пикселей.

```csharp
   Windows.UI.ViewManagement.ApplicationView.GetForCurrentView().SetPreferredMinSize
        (new Size { Width = 500, Height = 500 });
```

Следующий раздел называется [Перенос для ввода-вывода, устройства и модели приложения](wpsl-to-uwp-input-and-sensors.md).

## <a name="related-topics"></a>См. также

* [Пространство имен и класс сопоставления](wpsl-to-uwp-namespace-and-class-mappings.md)

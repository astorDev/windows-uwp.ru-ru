---
description: На практике определение пользовательского интерфейса в виде декларативной разметки XAML является чрезвычайно эффективным из Windows Phone Silverlight в универсальная платформа Windows приложения (UWP).
title: Перенос Windows Phone XAML и пользовательский интерфейс Silverlight в UWP
ms.assetid: 49aade74-5dc6-46a5-89ef-316dbeabbebe
ms.date: 02/08/2017
ms.topic: article
keywords: Windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: eeb8cb8a8b71123c3a5a94eea316621e5f93fe8e
ms.sourcegitcommit: b52ddecccb9e68dbb71695af3078005a2eb78af1
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/20/2019
ms.locfileid: "74259073"
---
#  <a name="porting-windowsphone-silverlight-xaml-and-ui-to-uwp"></a>Перенос Windows Phone XAML и пользовательский интерфейс Silverlight в UWP



Предыдущий раздел называется [Устранение неполадок](wpsl-to-uwp-troubleshooting.md).

На практике определение пользовательского интерфейса в виде декларативной разметки XAML является чрезвычайно эффективным из Windows Phone Silverlight в универсальная платформа Windows приложения (UWP). Вы увидите, что после обновления ключевых ссылок на системный ресурс, изменения некоторых имен типов элементов и изменения clr-namespace на using большие участки вашей разметки стали совместимы. Большую часть императивного кода в ваших моделях представления уровня представления и код, который управляет элементами пользовательского интерфейса, также будет очень просто перенести.

## <a name="a-first-look-at-the-xaml-markup"></a>Начало работы с разметкой XAML

В предыдущем разделе показано, как скопировать код XAML и файлы кода программной части в новый проект Windows 10 Visual Studio. Одна из первых проблем, которую вы могли заметить выделенной в конструкторе XAML Visual Studio, — это то, что элемент `PhoneApplicationPage` в корне файла XAML является недействительным для проекта UWP. В предыдущем разделе вы сохранили копию XAML-файлов, созданных Visual Studio при создании проекта Windows 10. Если вы откроете эту версию MainPage.xaml, то увидите в корне тип [**Page**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Page), который принадлежит пространству имен [**Windows.UI.Xaml.Controls**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls). Таким образом, вы можете изменить все элементы `<phone:PhoneApplicationPage>` на `<Page>` (не забывайте о синтаксисе элементов свойств) и можете удалить объявление `xmlns:phone`.

Более общий подход к поиску типа UWP, соответствующего типу Windows Phone Silverlight, можно найти в разделе [сопоставление пространств имен и классов](wpsl-to-uwp-namespace-and-class-mappings.md).

## <a name="xaml-namespace-prefix-declarations"></a>Объявления префикса пространства имен XAML


Если вы используете экземпляры настраиваемых типов в представлениях (возможно, экземпляр модели представления или преобразователь значений), в вашей разметке XAML будут присутствовать объявления префикса пространства имен. Синтаксис этих различий отличается от Windows Phone Silverlight и UWP. Вот несколько примеров:

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

**BitmapImage** находится в пространстве имен **System. Windows. Media. Imaging** в Windows Phone Silverlight, а директива using в том же файле позволяет использовать **BitmapImage** без квалификации пространства имен, как в приведенном выше фрагменте кода. В подобном случае можно щелкнуть правой кнопкой мыши имя типа (**BitmapImage**) в Visual Studio и с помощью команды контекстного меню **Разрешить** добавить новую директиву пространства имен в файл. В этом случае добавляется пространство имен [**Windows.UI.Xaml.Media.Imaging**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.Imaging), в котором находится тип в UWP. Вы можете удалить директиву using **System.Windows.Media.Imaging**, и этого будет достаточно для переноса кода, подобного коду в приведенном выше фрагменте. По завершении вы удалите все пространства имен Windows Phone Silverlight.

В простых случаях, подобных этому, когда вы сопоставляете типы в старом пространстве имен с такими же типами в новом, можно использовать команду Visual Studio **Поиск и замена** для внесения массовых изменений в исходный код. Команда **Разрешить** является отличным способом обнаружения нового пространства имен для типа. Еще один пример: вы можете заменить все сочетания System.Windows на Windows.UI.Xaml. Это перенесет большую часть всех директив using и все полные имена типов, которые ссылаются на это пространство имен.

После того как все старые директивы using будут удалены, а новые добавлены, можно использовать команду Visual Studio **Упорядочение Using**, чтобы отсортировать директивы и удалить неиспользуемые.

Иногда правка императивного кода требует всего лишь изменения типа параметров. В других случаях для приложений среда выполнения Windows 8. x необходимо использовать API-интерфейсы UWP вместо API-интерфейсов .NET. Чтобы узнать, какие интерфейсы API поддерживаются, используйте оставшуюся часть этого руководство по переносу в сочетании с [.NET для приложений среда выполнения Windows 8. x](https://docs.microsoft.com/previous-versions/windows/apps/br230302(v=vs.140)) , а также [ссылку на среда выполнения Windows](https://docs.microsoft.com/uwp/api/).

А если вы просто хотите дойти до этапа сборки проекта, можно закомментировать или снабдить заглушками фрагменты ненужного кода. Затем последовательно устраняйте неполадку за неполадкой и см. следующие темы в данном разделе (включая предыдущий раздел [Устранение неполадок](wpsl-to-uwp-troubleshooting.md)), пока любые проблемы сборки и выполнения не будут решены и перенос не будет завершен.

## <a name="adaptiveresponsive-ui"></a>Адаптивный пользовательский интерфейс с быстрым откликом

Так как приложение Windows 10 может работать на потенциально широком спектре устройств, каждый из которых имеет свой собственный размер и разрешение экрана, вам потребуется больше, чем минимальное число шагов для переноса приложения, и вы захотите настроить пользовательский интерфейс для лучшего понимания этих устройств. Вы можете использовать адаптивную функцию "Диспетчер визуальных состояний", чтобы динамически определять размер окна и изменять макет. Пример того, как это сделать, представлен в разделе [Адаптивный пользовательский интерфейс](wpsl-to-uwp-case-study-bookstore2.md) в примере Bookstore2.

## <a name="alarms-and-reminders"></a>Предупреждения и напоминания

Код, использующий классы **Alarm** или **Reminder**, необходимо перенести, чтобы использовать класс [**BackgroundTaskBuilder**](https://docs.microsoft.com/uwp/api/Windows.ApplicationModel.Background.BackgroundTaskBuilder) для создания и регистрации фоновой задачи и отображения всплывающих уведомлений в соответствующее время. См. разделы [Фоновая обработка](wpsl-to-uwp-business-and-data.md) и [Всплывающие уведомления](#toasts).

## <a name="animation"></a>Анимация

Теперь приложениям UWP доступна библиотека анимаций UWP в качестве предпочитаемой альтернативы анимации по ключевым кадрам и анимации "из/в". Эти анимации разработаны и настроены так, чтобы плавно выполняться, отлично выглядеть и придавать вашему приложению единый с Windows вид как у встроенных приложений. См. раздел [Краткое руководство: Анимация пользовательского интерфейса с использованием библиотеки анимаций](https://docs.microsoft.com/previous-versions/windows/apps/hh452703(v=win.10)).

Если вы используете в своих приложениях UWP анимацию по ключевым кадрам и анимацию "из/в", вы, возможно, захотите понять разницу между независимой и зависимой анимацией, которую представляет новая платформа. См. раздел [Оптимизация анимаций и мультимедиа](https://docs.microsoft.com/windows/uwp/debug-test-perf/optimize-animations-and-media). Анимации, которые выполняются в потоке пользовательского интерфейса (те, которые анимируют свойства макета, например), известны как зависимые анимации, и при запуске на новой платформе они не будут работать, если вы не выполните одно из двух действий. Вы можете задать им для анимирования различные свойства, например [**RenderTransform**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.uielement.rendertransform), таким образом делая их независимыми. Или вы можете установить `EnableDependentAnimation="True"` для элемента анимации, чтобы подтвердить ваше намерение выполнить анимацию, работа которой не может быть гарантированно плавной. Если вы используете Blend для Visual Studio для создания новых анимаций, это свойство будет установлено для вас там, где это необходимо.

## <a name="back-button-handling"></a>Работа с кнопкой "Назад"

В приложении Windows 10 можно использовать один подход к обработке кнопки «назад», который будет работать на всех устройствах. На мобильных устройствах эта кнопка выполняет роль емкостной кнопки на устройстве или кнопки в оболочке. На настольном ПК кнопка добавляется во внешнее оформление приложения, когда в приложении возможны переходы назад, и отображается в строке заголовка оконных приложений или на панели задач для режима планшета. Событие кнопки "Назад" — это универсальная концепция во всех семействах устройств, и кнопки, реализованные в аппаратном или программном обеспечении, создают одно и то же событие [**BackRequested**](https://docs.microsoft.com/uwp/api/windows.ui.core.systemnavigationmanager.backrequested).

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

Windows Phone панели приложений Silverlight и кнопки панели приложений не могут быть привязаны так же, как и в приложении UWP. Вы можете использовать императивный код, который создает панель приложения и ее кнопки, привязывает их к свойствам и локализованным строкам и обрабатывает их события. В этом случае у вас есть возможность перенести данный императивный код, заменив его декларативной разметкой, привязанной к свойствам и командам, и статическими ссылками на ресурсы, что сделает ваше приложение более безопасным и удобным в обслуживании. Для привязки и оформления кнопок панели приложения UWP, как и любого другого элемента XAML, можно использовать Visual Studio или Blend для Visual Studio. Обратите внимание, что в приложении UWP имена используемых вами типов — [**CommandBar**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.CommandBar) и [**AppBarButton**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.AppBarButton).

Связанные с привязкой функции приложений UWP сейчас имеют следующие ограничения:

-   Отсутствует встроенная поддержка проверки вводимых данных и интерфейсов [**IDataErrorInfo**](https://docs.microsoft.com/dotnet/api/system.componentmodel.idataerrorinfo) и [**INotifyDataErrorInfo**](https://docs.microsoft.com/dotnet/api/system.componentmodel.inotifydataerrorinfo).
-   Класс [**привязки**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Data.Binding) не включает расширенные свойства форматирования, доступные в Windows Phone Silverlight. Тем не менее для предоставления пользовательского форматирования все еще можно использовать [**IValueConverter**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Data.IValueConverter).
-   Методы [**IValueConverter**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Data.IValueConverter) принимают в качестве параметров языковые строки, а не объекты [**CultureInfo**](https://docs.microsoft.com/dotnet/api/system.globalization.cultureinfo).
-   Класс [**CollectionViewSource**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Data.CollectionViewSource) не имеет встроенной поддержки сортировки и фильтрации, а группирование работает по-другому. Дополнительную информацию см. в разделах [Подробно о привязке данных](https://docs.microsoft.com/windows/uwp/data-binding/data-binding-in-depth) и [Образец привязки данных](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/XamlBind).

Хотя одни и те же функции привязки по-прежнему поддерживаются, Windows 10 предлагает вариант нового и более производительного механизма привязки, именуемого скомпилированными привязками, которые используют расширение разметки {КС:бинд}. См. разделы [Привязка данных. Поддержка производительности приложений по новым улучшениям для привязки данных XAML](https://channel9.msdn.com/Events/Build/2015/3-635) и [Пример привязки x:](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/XamlBind).

## <a name="binding-an-image-to-a-view-model"></a>Привязка изображения к модели представления

Свойство [**Image.Source**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.image.source) можно привязать к любому свойству модели представления типа [**ImageSource**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.ImageSource). Ниже приведена типичная реализация такого свойства в Windows Phone приложении Silverlight:

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

Windows Phone приложения Silverlight используют элементы управления, определенные в пространстве имен **Microsoft. Phone. Controls** и пространстве имен **System. Windows. Controls** . Приложения XAML UWP используют элементы управления, определенные в пространстве имен [**Windows.UI.Xaml.Controls**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls). Архитектура и структура элементов управления XAML в UWP практически не отличается от Windows Phone элементов управления Silverlight. Но в них был внесены некоторые изменения для улучшения набора доступных элементов управления и объединениях их с приложениями Windows. Вот несколько конкретных примеров.

| Название элемента управления | Изменение |
|--------------|--------|
| ApplicationBar | Свойство [Page.TopAppBar](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.page.topappbar). |
| ApplicationBarIconButton | Эквивалентом в UWP является свойство [Glyph](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.fonticon.glyph). PrimaryCommands является свойством содержимого CommandBar. Анализатор XAML интерпретирует внутренний xml элемента как значение его свойства содержимого. |
| ApplicationBarMenuItem | Эквивалентом в UWP является элемент [AppBarButton.Label](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.appbarbutton.label), для которого настроен текст элемента меню. |
| ContextMenu (в наборе средств Windows Phone) | Для простого всплывающего элемента используйте [Flyout](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Flyout). |
| Класс ControlTiltEffect.TiltEffect | Анимации из библиотеки анимации UWP встроены в используемые по умолчанию стили стандартных элементов управления. См. [Анимация действий указателя](https://docs.microsoft.com/previous-versions/windows/apps/jj649432(v=win.10)). |
| LongListSelector со сгруппированными данными | Функция Windows Phone Silverlight LongListSelector работает двумя способами, которые можно использовать совместно. Во-первых, она может отображать данные, которые сгруппированы по ключу (например, список имен, сгруппированный по первой букве). Во-вторых, она может переключаться между двумя контекстными представлениями: сгруппированный список элементов (например, имена) и список только самих ключей группы (например, первые буквы). В UWP сгруппированные данные можно отобразить с помощью элементов [Руководство по элементам управления просмотром в виде списка и сетки](https://docs.microsoft.com/windows/uwp/controls-and-patterns/lists). |
| LongListSelector с неструктурированными данными | По соображениям производительности в случае с очень длинными списками мы рекомендуем LongListSelector вместо списка Windows Phone Silverlight, даже для неструктурированных несгруппированных данных. В приложении UWP для длинных списков элементов предпочтительнее использовать [GridView](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.GridView) независимо от того, поддаются данные группировке или нет. |
| Panorama | Windows Phone элемент управления "Панорама Silverlight" сопоставляется с [рекомендациями по управлению центрами в приложениях среда выполнения Windows 8. x](https://docs.microsoft.com/windows/uwp/controls-and-patterns/hub) и рекомендациями для управления концентратором. <br/> Обратите внимание: элемент управления Panorama окружен текстом от последнего раздела до первого, а его фоновое изображение перемещается в параллаксе относительно разделам. Разделы [Hub](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Hub) не создают обтекание текстом, и параллакс не используется. |
| Сводка | Объект UWP, эквивалентный элементу управления PowerPivot Windows Phone Silverlight, — это [Windows. UI. XAML. Controls. Pivot](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Pivot). Эта функция доступна для всех семейств устройств. |

**Обратите внимание** ,   визуальное состояние Поинтеровер относится к пользовательским стилям и шаблонам в приложениях Windows 10, но не в Windows Phone приложениях Silverlight. Существуют и другие причины, по которым существующие пользовательские стили или шаблоны могут не подойти для приложений Windows 10, включая используемые ключи ресурсов системы, изменения в наборах визуальных состояний и улучшения производительности, внесенные в стили и шаблоны Windows 10 по умолчанию. Рекомендуется изменить новую копию шаблона элемента управления по умолчанию для Windows 10, а затем повторно применить настройку стиля и шаблона.

Дополнительные сведения об элементах управления UWP см. в разделах [Распределение элементов управления по функциям](https://docs.microsoft.com/windows/uwp/controls-and-patterns/controls-by-function), [Список элементов управления](https://docs.microsoft.com/windows/uwp/design/controls-and-patterns/) и [Руководство по элементам управления](https://docs.microsoft.com/windows/uwp/design/controls-and-patterns/index).

##  <a name="design-language-in-windows10"></a>Язык разработки в Windows 10

Существуют некоторые отличия языка разработки от Windows Phone приложений Silverlight и приложений Windows 10. Все сведения см. в разделе [Дизайн](https://developer.microsoft.com/en-us/windows/apps/design). Несмотря на изменения языка дизайна, принципы дизайна остаются прежними: будьте внимательны к деталям, при этом всегда стремитесь к простоте и фокусируйте внимание на содержимом, а не внешнем оформлении, уменьшая количество визуальных элементов и сохраняя характерные особенности цифрового домена, используйте визуальную иерархию (особенно для шрифтового оформления), проектируйте, используя сетку, оживляйте интерфейс с помощью плавных анимаций.

## <a name="localization-and-globalization"></a>Локализация и глобализация

Для локализованных строк можно повторно использовать RESX-файл из проекта Windows Phone Silverlight в проекте приложения UWP. Скопируйте файл, добавьте его в проект и измените его расширение на Resources.resw, чтобы механизм поиска находил его по умолчанию. Установите **Действие при сборке** на **PRIResource**, а **Копировать в выходной каталог** на **Не копировать**. Затем можно использовать строки в разметке, указывая атрибут **x:Uid** в элементах XAML. См. [Краткое руководство: использование строковых ресурсов](https://docs.microsoft.com/previous-versions/windows/apps/hh965329(v=win.10)).

Windows Phone приложения Silverlight используют класс **CultureInfo** для упрощения глобализации приложения. Приложения UWP используют современную технологию ресурсов (MRT), которая поддерживает динамическую загрузку ресурсов приложения (локализации, масштабирования и темы) и в среду выполнения и в рабочую область конструирования Visual Studio. Дополнительные сведения см. в разделе [Руководство по файлам, данным и глобализации](https://docs.microsoft.com/windows/uwp/design/usability/index).

В разделе [**ResourceContext.QualifierValues**](https://docs.microsoft.com/uwp/api/windows.applicationmodel.resources.core.resourcecontext.qualifiervalues) описывается загрузка ресурсов для определенного семейства устройств с учетом коэффициента выбора ресурсов для семейств устройств.

## <a name="media-and-graphics"></a>Мультимедиа и графика

Читая о мультимедиа и изображениях UWP учитывайте, что принципы разработки Windows поощряют максимальное уменьшение всего излишнего, включая графическую сложность и излишества. Разработка Windows типизирована чистыми и четкими визуальными элементами, шрифтовым оформлением и перемещением. Если ваше приложение будет следовать тем же принципам, оно будет больше похоже на встроенные приложения.

Windows Phone Silverlight имеет тип **RadialGradientBrush** , который отсутствует в UWP, хотя другие типы [**кисти**](/uwp/api/Windows.UI.Xaml.Media.Brush) —. В некоторых случаях вы сможете достичь аналогичного эффекта с помощью растрового изображения. Обратите внимание, что вы можете [создать радиальную градиентную кисть](https://docs.microsoft.com/windows/desktop/Direct2D/how-to-create-a-radial-gradient-brush) с помощью Direct2D в [Microsoft DirectX](https://docs.microsoft.com/windows/desktop/directx) и XAML C++ UWP.

Windows Phone Silverlight имеет свойство **System. Windows. UIElement. OpacityMask** , но это свойство не является членом типа [**UIElement**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.UIElement) объекта UWP. В некоторых случаях вы сможете достичь аналогичного эффекта с помощью растрового изображения. Вы также можете [создать маску непрозрачности](https://docs.microsoft.com/windows/desktop/Direct2D/opacity-masks-overview) с помощью Direct2D в [Microsoft DirectX](https://docs.microsoft.com/windows/desktop/directx) и приложении UWP XAML C++. Но распространенным вариантом использования для **OpacityMask** является применение единого растрового изображения, адаптирующегося к светлой и темной темам. Для векторной графики можно использовать системные кисти, учитывающие тему (например, показанные ниже круговые диаграммы). Но создание растрового изображения, учитывающего тему (например, показанные ниже флажки), требует другого подхода.

![Учитывающее тему растровое изображение](images/wpsl-to-uwp-case-studies/wpsl-to-uwp-theme-aware-bitmap.png)

В приложении Windows Phone Silverlight используется альфа-маска (в виде точечного рисунка) в качестве **OpacityMask** для **прямоугольника** , заполненного кистью переднего плана:

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

В этом случае WinRT\_Check. PNG — это альфа-маска в виде точечного рисунка, что и ВПСЛ\_Check. PNG —, и это может быть и тот же файл. Однако может потребоваться указать несколько различных размеров WinRT\_Check. PNG для различных факторов масштабирования. Дополнительные сведения об этом и описание изменений в значениях **Width** и **Height** см. в подразделе [Пиксели представления или эффективные пиксели, расстояние от экрана и коэффициенты масштабирование](#view-or-effective-pixels-viewing-distance-and-scale-factors) в этом разделе.

Более общим способом, который подходит в случае различия между оформлением растрового изображения в светлой и темной теме, является использование двух ресурсов изображений — одного с темным передним планом (для светлой темы) и второго со светлым передним планом (для темной темы). Дополнительные сведения о присвоении имени этому набору ресурсов точечного рисунка см. в разделе [адаптация ресурсов для языка, масштаба и других квалификаторов](../app-resources/tailor-resources-lang-scale-contrast.md). После присвоения правильного имени набору файлов изображений вы можете ссылаться на них в общем с помощью их имени корневого элемента, например:

```xml
    <Image Source="Assets/winrt_check.png" Stretch="None"/>
```

В Windows Phone Silverlight свойство **UIElement. Clip** может быть любой формой, которую можно выразить с помощью **геометрии** , и обычно сериализуется в разметке XAML на мини-языке **StreamGeometry** . В UWP тип свойства [**Clip**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.uielement.clip) — [**RectangleGeometry**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.RectangleGeometry), поэтому вы можете только вырезать прямоугольную область. Разрешение определить прямоугольник с помощью мини-языка будет слишком вольным. Таким образом, чтобы перенести область обрезки в разметку, замените синтаксис атрибута **Clip** и выполните его в синтаксисе элемента свойства следующим образом:

```xml
    <UIElement.Clip>
        <RectangleGeometry Rect="10 10 50 50"/>
    </UIElement.Clip>
```

Обратите внимание, что вы можете [использовать произвольную геометрию в качестве маски в уровне](https://docs.microsoft.com/windows/desktop/Direct2D/direct2d-layers-overview) с Direct2D в [Microsoft DirectX](https://docs.microsoft.com/windows/desktop/directx) C++ XAML приложении UWP.

## <a name="navigation"></a>Навигация

При переходе к странице в Windows Phone приложении Silverlight используется схема адресации универсального кода ресурса (URI):

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

Вы определяете стартовую страницу для Windows Phone приложения Silverlight в файле WMAppManifest. XML:

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

Язык разработки превратился в Windows 10 и, следовательно, изменились некоторые системные стили, а многие ключи системных ресурсов были удалены или переименованы. Редактор разметки XAML в Visual Studio выделяет ссылки на ключи ресурсов, которые не могут быть разрешены. Например, редактор разметки XAML подчеркнет ссылку к ключу стиля `PhoneTextNormalStyle` волнистой линией красного цвета. Если она не будет исправлена, приложение немедленно завершит работу при попытке развернуть его в эмуляторе или на устройстве. Поэтому важно обращать внимание на правильность разметки XAML. И вы увидите, что Visual Studio — это отличный инструмент для нахождения подобных проблем.

См. также раздел [Текст](#text) ниже.

## <a name="status-bar-system-tray"></a>Строка состояния (панель задач)

Панель задач (установленная в разметке XAML с `shell:SystemTray.IsVisible`) теперь называется строкой состояния и отображается по умолчанию. Вы можете контролировать ее видимость в императивном коде, вызывая методы [**Windows.UI.ViewManagement.StatusBar.ShowAsync**](https://docs.microsoft.com/uwp/api/windows.ui.viewmanagement.statusbar.showasync) и [**HideAsync**](https://docs.microsoft.com/uwp/api/windows.ui.viewmanagement.statusbar.hideasync).

## <a name="text"></a>Текста

Текст (или шрифтовое оформление) является важным аспектом для приложения UWP. Во время переноса, возможно, необходимо будет пересмотреть визуальные элементы в представлениях, чтобы они соответствовали новому языку дизайна. Используйте эти иллюстрации, чтобы найти стили системы UWP  **TextBlock**, которые доступны. Найдите те из них, которые соответствуют используемым вами стилям Windows Phone Silverlight. Кроме того, можно создать собственные универсальные стили и скопировать свойства из стилей системы Windows Phone Silverlight в них.

![стили системы textblock для приложений для windows 10](images/label-uwp10stylegallery.png)

Системные стили TextBlock для приложений Windows 10

В Windows Phone приложении Silverlight семейство шрифтов по умолчанию — Segoe WP. В приложении Windows 10 семейство шрифтов по умолчанию Segoe UI. В результате метрика шрифта в приложении может выглядеть иначе. Если вы хотите воспроизвести внешний вид Windows Phoneного текста Silverlight, можно задать собственные метрики с помощью таких свойств, как [**LineHeight**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.textblock.lineheight) и [**линестаккингстратеги**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.textblock.linestackingstrategy). Дополнительные сведения см. в разделе [Рекомендации по шрифтам](https://docs.microsoft.com/windows/uwp/controls-and-patterns/fonts) и [Оформление приложений UWP](https://developer.microsoft.com/en-us/windows/apps/design).

## <a name="theme-changes"></a>Изменения тем

Для Windows Phone приложения Silverlight тема по умолчанию темная по умолчанию. Для устройств Windows 10 тема по умолчанию изменилась, но можно управлять темой, используемой для объявления запрошенной темы в App. XAML. Например, чтобы на всех устройствах отображалась темная тема, добавьте `RequestedTheme="Dark"` к корневому элементу Application.

## <a name="tiles"></a>Плитки

Плитки для приложений UWP имеют поведение, аналогичное активным плиткам для Windows Phone приложений Silverlight, хотя существуют некоторые различия. Например, код, который вызывает метод **Microsoft.Phone.Shell.ShellTile.Create** для создания вспомогательных плиток, должен быть перенесен для вызова [**SecondaryTile.RequestCreateAsync**](https://docs.microsoft.com/uwp/api/windows.ui.startscreen.secondarytile.requestcreateasync). Ниже приведен пример до и после первого примера Windows Phone версии Silverlight:


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

Windows Phone приложения Silverlight и приложения Windows 10 отличаются от размера и структуры элементов пользовательского интерфейса от фактического физического размера и разрешения устройств. Для этого приложение Windows Phone Silverlight использует пикселы для просмотра. В Windows 10 концепция представлений пикселей была улучшена в отношении эффективных пикселей. Вот описание этого термина, его значения и преимуществ.

Термин «разрешение» относится к измерению плотности пикселей, а не, как обычно считается, количества пикселей. «Эффективное разрешение» — это способ, которым физические пиксели, образующие изображение или глиф разрешают для глаза данные различия в расстоянии от экрана и физическом размере пикселей устройства (плотность пикселей обратна физическому размеру пикселей). Эффективное разрешение — это хорошая метрика для создания взаимодействия на ее основе, поскольку оно ориентируется на пользователя. Поняв все факторы и контролируя размер элементов пользовательского интерфейса, вы можете сделать взаимодействие с пользователем комфортным.

Для Windows Phone приложения Silverlight все экраны телефонов представляют собой ровно 480 просмотра пикселей в ширину, без исключения, независимо от того, сколько физических пикселов имеет экран, а также от того, какова его плотность или физический размер в пикселях. Это означает, что элемент **изображения** с `Width="48"` будет находиться в одной десятой части экрана любого телефона, на котором можно запустить приложение Windows Phone Silverlight.

Для приложения Windows 10 *не* так, чтобы все устройства были фиксированным числом действующих пикселов в ширину. Скорее всего это очевидно с учетом диапазона ширины различных устройств, в которых можно выполнить приложение UWP. Количество эффективных пикселей в ширину разное для различных устройств, от 320 epx для самых маленьких устройств и до 1024 epx для мониторов скромных размеров и выходит далеко за этот предел. Вам потребуется всего лишь продолжать использовать элементы с автоматически устанавливаемым размером и динамические панели макета, как и раньше. В некоторых случаях в свойствах элементов пользовательского интерфейса в разметке XAML задается фиксированный размер. Коэффициент масштабирования автоматически применяется к приложению в зависимости от устройства, на котором оно запускается, и от настроенных пользователем параметров экрана. Коэффициент масштабирования служит для сохранения представления элементами пользовательского интерфейса цели сенсорного ввода (и чтения) с более или менее постоянным размером для пользователя устройств с различными размерами экрана. А благодаря динамическому макету вместо визуального масштабирования пользовательского интерфейса на разных устройствах, он будет вмещать желаемый объем содержимого в доступное пространство.

Так как 480 ранее была фиксированной шириной в пикселах просмотра для экрана с размером телефона, и это значение, как правило, меньше в эффективных пикселях, правило Thumb — это умножить любое измерение в Windows Phone разметки приложения Silverlight на множитель 0,8.

Чтобы ваше приложение будет наилучшим образом выглядело на любых устройствах, мы рекомендуем создать каждый растровый ресурс в различных размерах для определенного коэффициента масштабирования. Если вы предоставите ресурсы с масштабом 100 %, 200 % и 400 % (в такой последовательности), это обеспечит отличные результаты в большинстве случаев при любых промежуточных коэффициентах масштабирования.

**Обратите внимание** ,  если по какой-либо причине нельзя создать ресурсы более чем в одном размере, создайте 100%-масштабируемые ресурсы. В Microsoft Visual Studio шаблон проекта по умолчанию для приложений UWP предоставляет ресурсы официальной фирменной символики (изображения плиток и эмблемы) только в одном размере, но масштаб при этом не 100 %. При создании ресурсов для своего приложения следуйте рекомендациям в этом разделе и предусмотрите размеры 100 %, 200 % и 400 %, а также используйте пакеты ресурсов.

В случае использования сложного изображения, возможно, следует предусмотреть еще больше размеров. Если используются векторные изображения, тогда процесс создания высококачественных ресурсов при любом масштабе относительно прост.

Мы не рекомендуем попытаться поддерживать все коэффициенты масштабирования, но полный список факторов масштабирования для приложений Windows 10 составляет 100%, 125%, 150%, 200%, 250%, 300% и 400%. Если вы все-таких их предоставляете, Магазин выберет правильные ресурсы для каждого устройства, и только они будут загружены. Магазин выбирает ресурсы для загрузки на основе DPI устройства.

Подробнее см. в разделе [Адаптивный дизайн 101 для приложений UWP](https://docs.microsoft.com/windows/uwp/layout/screen-sizes-and-breakpoints-for-responsive-design).

## <a name="window-size"></a>Размер окна

В приложении UWP можно задать минимальный размер окна (ширину и высоту) с помощью императивного кода. Стандартный минимальный размер — 500 x 320 эфф. пикселей. Это также самый маленький из допустимых минимальных размеров. Самый большой из допустимых минимальных размеров — 500 x 500 эффективных пикселей.

```csharp
   Windows.UI.ViewManagement.ApplicationView.GetForCurrentView().SetPreferredMinSize
        (new Size { Width = 500, Height = 500 });
```

Следующий раздел называется [Перенос для ввода-вывода, устройства и модели приложения](wpsl-to-uwp-input-and-sensors.md).

## <a name="related-topics"></a>См. также

* [Сопоставления пространств имен и классов](wpsl-to-uwp-namespace-and-class-mappings.md)

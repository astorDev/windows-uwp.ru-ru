---
author: stevewhims
description: Практика определения пользовательского интерфейса в форме декларативной разметки XAML очень хорошо преобразуется из WindowsPhone Silverlight в приложения универсальной платформы Windows (UWP).
title: Перенос WindowsPhone Silverlight XAML и пользовательского интерфейса в UWP
ms.assetid: 49aade74-5dc6-46a5-89ef-316dbeabbebe
ms.author: stwhi
ms.date: 02/08/2017
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: d219a09ccca74c9fc513b7510c40ce0b90ad9f52
ms.sourcegitcommit: cbe7cf620622a5e4df7414f9e38dfecec1cfca99
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/20/2018
ms.locfileid: "7428640"
---
#  <a name="porting-windowsphone-silverlight-xaml-and-ui-to-uwp"></a>Перенос WindowsPhone Silverlight XAML и пользовательского интерфейса в UWP



Предыдущий раздел называется [Устранение неполадок](wpsl-to-uwp-troubleshooting.md).

Практика определения пользовательского интерфейса в форме декларативной разметки XAML очень хорошо преобразуется из WindowsPhone Silverlight в приложения универсальной платформы Windows (UWP). Вы увидите, что после обновления ключевых ссылок на системный ресурс, изменения некоторых имен типов элементов и изменения clr-namespace на using большие участки вашей разметки стали совместимы. Большую часть императивного кода в ваших моделях представления уровня представления и код, который управляет элементами пользовательского интерфейса, также будет очень просто перенести.

## <a name="a-first-look-at-the-xaml-markup"></a>Начало работы с разметкой XAML

Предыдущий раздел рассказывалось, как копировать XAML и код программной файлы в новом проекте Windows10 Visual Studio. Одна из первых проблем, которую вы могли заметить выделенной в конструкторе XAML Visual Studio, — это то, что элемент `PhoneApplicationPage` в корне файла XAML является недействительным для проекта UWP. В предыдущем разделе вы сохранили копию файлов XAML, сформированных Visual Studio при создании проекта Windows10. Если вы откроете эту версию MainPage.xaml, то увидите в корне тип [**Page**](https://msdn.microsoft.com/library/windows/apps/br227503), который принадлежит пространству имен [**Windows.UI.Xaml.Controls**](https://msdn.microsoft.com/library/windows/apps/br227716). Таким образом, вы можете изменить все элементы `<phone:PhoneApplicationPage>` на `<Page>` (не забывайте о синтаксисе элементов свойств) и можете удалить объявление `xmlns:phone`.

Более общий подход к поиску типа UWP, которое соответствует типу WindowsPhone Silverlight могут ссылаться [сопоставление пространства имен и класса](wpsl-to-uwp-namespace-and-class-mappings.md).

## <a name="xaml-namespace-prefix-declarations"></a>Объявления префикса пространства имен XAML


Если вы используете экземпляры настраиваемых типов в представлениях (возможно, экземпляр модели представления или преобразователь значений), в вашей разметке XAML будут присутствовать объявления префикса пространства имен. Их синтаксис различен WindowsPhone Silverlight и UWP. Вот несколько примеров:

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

**BitmapImage** находится в пространстве имен **System.Windows.Media.Imaging** в WindowsPhone Silverlight, а также использовать директивы в одном файле позволяет **BitmapImage** для использования без квалификации пространства имен, как показано в приведенном выше фрагменте. В подобном случае можно щелкнуть правой кнопкой мыши имя типа (**BitmapImage**) в Visual Studio и с помощью команды контекстного меню **Разрешить** добавить новую директиву пространства имен в файл. В этом случае добавляется пространство имен [**Windows.UI.Xaml.Media.Imaging**](https://msdn.microsoft.com/library/windows/apps/br243258), в котором находится тип в UWP. Вы можете удалить директиву using **System.Windows.Media.Imaging**, и этого будет достаточно для переноса кода, подобного коду в приведенном выше фрагменте. Когда все будет готово, будут удалены все пространства имен WindowsPhone Silverlight.

В простых случаях, подобных этому, когда вы сопоставляете типы в старом пространстве имен с такими же типами в новом, можно использовать команду Visual Studio **Поиск и замена** для внесения массовых изменений в исходный код. Команда **Разрешить** является отличным способом обнаружения нового пространства имен для типа. Еще один пример: вы можете заменить все сочетания System.Windows на Windows.UI.Xaml. Это перенесет большую часть всех директив using и все полные имена типов, которые ссылаются на это пространство имен.

После того как все старые директивы using будут удалены, а новые добавлены, можно использовать команду Visual Studio **Упорядочение Using**, чтобы отсортировать директивы и удалить неиспользуемые.

Иногда правка императивного кода требует всего лишь изменения типа параметров. В других случаях будет необходимо использовать API-интерфейсы UWP вместо .NET API-интерфейсы для приложений среды выполнения Windows 8.x. Чтобы определить, какие API поддерживаются, используйте остальная часть этого руководства по переносу в сочетании с [обзоре приложений .NET для среды выполнения Windows 8.x](https://msdn.microsoft.com/library/windows/apps/xaml/br230302.aspx) и [справке среды выполнения Windows](https://msdn.microsoft.com/library/windows/apps/br211377).

А если вы просто хотите дойти до этапа, на котором ваш проект собирается, можно закомментировать или снабдить заглушками фрагменты ненужного кода. Затем последовательно устраняйте неполадку за неполадкой и см. следующие темы в данном разделе (включая предыдущий раздел [Устранение неполадок](wpsl-to-uwp-troubleshooting.md)), пока любые проблемы сборки и выполнения не будут решены и перенос не будет завершен.

## <a name="adaptiveresponsive-ui"></a>Адаптивный пользовательский интерфейс с быстрым откликом

Так как приложение Windows10 может работать на потенциально широкий спектр устройств, каждый из которых имеет свой размер экрана и разрешение — вам потребуется выйдете за пределы минимальных действий для переноса приложения и вам потребуется адаптировать ваш пользовательский Интерфейс ему наилучшего вида на этих устройствах. Вы можете использовать адаптивную функцию "Диспетчер визуальных состояний", чтобы динамически определять размер окна и изменять макет. Пример того, как это сделать, представлен в разделе [Адаптивный пользовательский интерфейс](wpsl-to-uwp-case-study-bookstore2.md) в примере Bookstore2.

## <a name="alarms-and-reminders"></a>Предупреждения и напоминания

Код, использующий классы **Alarm** или **Reminder**, должен быть перенесен, чтобы использовать класс [**BackgroundTaskBuilder**](https://msdn.microsoft.com/library/windows/apps/br224768) для создания и регистрации фоновой задачи и отображения всплывающих уведомлений в соответствующее время. См. разделы [Фоновая обработка](wpsl-to-uwp-business-and-data.md) и [Всплывающие уведомления](#toasts).

## <a name="animation"></a>Анимация

Теперь в качестве предпочитаемой альтернативы анимации по ключевым кадрам и анимации "из/в" приложениям UWP доступна библиотека анимаций UWP. Эти анимации разработаны и настроены так, чтобы плавно выполняться, отлично выглядеть и придавать вашему приложению единый с Windows вид как у встроенных приложений. См. раздел [Краткое руководство: Анимация пользовательского интерфейса с использованием библиотеки анимаций](https://msdn.microsoft.com/library/windows/apps/xaml/hh452703).

Если вы используете в своих приложениях UWP анимацию по ключевым кадрам и анимацию "из/в", вы, возможно, захотите понять разницу между независимой и зависимой анимацией, которую представляет новая платформа. См. раздел [Оптимизация анимаций и мультимедиа](https://msdn.microsoft.com/library/windows/apps/mt204774). Анимации, которые выполняются в потоке пользовательского интерфейса (те, которые анимируют свойства макета, например), известны как зависимые анимации, и при запуске на новой платформе они не будут работать, если вы не выполните одно из двух действий. Вы можете задать им для анимирования различные свойства, например [**RenderTransform**](https://msdn.microsoft.com/library/windows/apps/br208980), таким образом делая их независимыми. Или вы можете установить `EnableDependentAnimation="True"` для элемента анимации, чтобы подтвердить ваше намерение выполнить анимацию, работа которой не может быть гарантированно плавной. Если вы используете Blend для Visual Studio для создания новых анимаций, это свойство будет установлено для вас там, где это необходимо.

## <a name="back-button-handling"></a>Работа с кнопкой "Назад"

В приложении для Windows10 можно использовать единый подход к обработке кнопки "Назад", и он будет работать на всех устройствах. На мобильных устройствах эта кнопка выполняет роль емкостной кнопки на устройстве или кнопки в оболочке. На настольном ПК кнопка добавляется во внешнее оформление приложения, когда в приложении возможны переходы назад, и отображается в строке заголовка оконных приложений или на панели задач для режима планшета. Событие кнопки "Назад"—это универсальная концепция во всех семействах устройств, и кнопки, реализованные в аппаратном или программном обеспечении, создают одно и то же событие [**BackRequested**](https://msdn.microsoft.com/library/windows/apps/dn893596).

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

Все эти аспекты в основном поддерживаются, но пространства имен различаются. Например, **System.Windows.Data.Binding** сопоставляется с [**Windows.UI.Xaml.Data.Binding**](https://msdn.microsoft.com/library/windows/apps/br209820), **System.ComponentModel.INotifyPropertyChanged** сопоставляется с [**Windows.UI.Xaml.Data.INotifyPropertyChanged**](https://msdn.microsoft.com/library/windows/apps/br209899) и **System.Collections.Specialized.INotifyPropertyChanged** сопоставляется с [**Windows.UI.Xaml.Interop.INotifyCollectionChanged**](https://msdn.microsoft.com/library/windows/apps/hh702001).

Панели приложения WindowsPhone Silverlight и кнопки панели приложения невозможно привязать, как это можно сделать в приложении UWP. Вы можете использовать императивный код, который создает панель приложения и ее кнопки, привязывает их к свойствам и локализованным строкам и обрабатывает их события. В этом случае у вас есть возможность перенести данный императивный код, заменив его декларативной разметкой, привязанной к свойствам и командам, и статическими ссылками на ресурсы, что сделает ваше приложение более безопасным и удобным в обслуживании. Для привязки и оформления кнопок панели приложения UWP, как и любого другого элемента XAML, можно использовать Visual Studio или Blend для Visual Studio. Обратите внимание, что в приложении UWP имена используемых вами типов—[**CommandBar**](https://msdn.microsoft.com/library/windows/apps/dn279427) и [**AppBarButton**](https://msdn.microsoft.com/library/windows/apps/dn279244).

Связанные с привязкой функции приложений UWP сейчас имеют следующие ограничения:

-   Отсутствует встроенная поддержка проверки вводимых данных и интерфейсов [**IDataErrorInfo**](https://msdn.microsoft.com/library/system.componentmodel.idataerrorinfo.aspx) и [**INotifyDataErrorInfo**](https://msdn.microsoft.com/library/system.componentmodel.inotifydataerrorinfo.aspx).
-   Класс [**Binding**](https://msdn.microsoft.com/library/windows/apps/br209820) не включает расширенные свойства форматирования, доступные в WindowsPhone Silverlight. Тем не менее для предоставления пользовательского форматирования все еще можно использовать [**IValueConverter**](https://msdn.microsoft.com/library/windows/apps/br209903).
-   Методы [**IValueConverter**](https://msdn.microsoft.com/library/windows/apps/br209903) принимают в качестве параметров языковые строки, а не объекты [**CultureInfo**](https://msdn.microsoft.com/library/system.globalization.cultureinfo.aspx).
-   Класс [**CollectionViewSource**](https://msdn.microsoft.com/library/windows/apps/br209833) не имеет встроенной поддержки сортировки и фильтрации, а группирование работает по-другому. Дополнительную информацию см. в разделах [Подробно о привязке данных](https://msdn.microsoft.com/library/windows/apps/mt210946) и [Образец привязки данных](http://go.microsoft.com/fwlink/p/?linkid=226854).

Несмотря на то, что такие же функции привязки по-прежнему широко поддерживаются, Windows10 возможность новый и более производительный механизм называется привязки скомпилированными привязками, использующими расширение разметки {x: Bind}. См. разделы [Привязка данных. Поддержка производительности приложений по новым улучшениям для привязки данных XAML](http://channel9.msdn.com/Events/Build/2015/3-635) и [Пример привязки x:](http://go.microsoft.com/fwlink/p/?linkid=619989).

## <a name="binding-an-image-to-a-view-model"></a>Привязка изображения к модели представления

Вы можете привязать свойство [**Image.Source**](https://msdn.microsoft.com/library/windows/apps/br242760) к любому свойству модели представления типа [**ImageSource**](https://msdn.microsoft.com/library/windows/apps/br210107). Вот типичная реализация этого свойства в приложении WindowsPhone Silverlight.

```csharp
    // this.BookCoverImagePath contains a path of the form "/Assets/CoverImages/one.png".
    return new BitmapImage(new Uri(this.CoverImagePath, UriKind.Relative));
```

В приложении UWP используется [схема URI](https://msdn.microsoft.com/library/windows/apps/jj655406) ms-appx. Чтобы сохранить остальную часть кода такой же, можно использовать другую перегрузку конструктора **System.Uri**, чтобы поместить схему URI ms-appx в базовый URI и добавить в него оставшуюся часть пути. Пример.

```csharp
    // this.BookCoverImagePath contains a path of the form "/Assets/CoverImages/one.png".
    return new BitmapImage(new Uri(new Uri("ms-appx://"), this.CoverImagePath));
```

Таким образом, остальная часть модели представления, значения пути в свойстве пути изображения и привязки в разметке XAML могут оставаться абсолютно такими же.

## <a name="controls-and-control-stylestemplates"></a>Элементы управления, стили и шаблоны элементов управления

Приложения WindowsPhone Silverlight используют элементы управления, определенные в пространстве имен **Microsoft.Phone.Controls** и пространство имен **System.Windows.Controls** . Приложения XAML UWP используют элементы управления, определенные в пространстве имен [**Windows.UI.Xaml.Controls**](https://msdn.microsoft.com/library/windows/apps/br227716). Архитектура и оформление элементов управления XAML в UWP является практически так же, как элементы управления WindowsPhone Silverlight. Но в них был внесены некоторые изменения для улучшения набора доступных элементов управления и объединениях их с приложениями Windows. Вот несколько конкретных примеров.

| Название элемента управления | Изменить |
|--------------|--------|
| ApplicationBar | Свойство [Page.TopAppBar](https://msdn.microsoft.com/library/windows/apps/hh702575). |
| ApplicationBarIconButton | Эквивалентом в UWP является свойство [Glyph](https://msdn.microsoft.com/library/windows/apps/dn279538). PrimaryCommands является свойством содержимого CommandBar. Анализатор XAML интерпретирует внутренний xml элемента как значение его свойства содержимого. |
| ApplicationBarMenuItem | Эквивалентом в UWP является элемент [AppBarButton.Label](https://msdn.microsoft.com/library/windows/apps/dn279261), для которого настроен текст элемента меню. |
| ContextMenu (в наборе средств Windows Phone) | Для простого всплывающего элемента используйте [Flyout](https://msdn.microsoft.com/library/windows/apps/dn279496). |
| Класс ControlTiltEffect.TiltEffect | Анимации из библиотеки анимации UWP встроены в используемые по умолчанию стили стандартных элементов управления. См. [Анимация действий указателя](https://msdn.microsoft.com/library/windows/apps/xaml/jj649432). |
| LongListSelector со сгруппированными данными | WindowsPhone Silverlight LongListSelector работает двумя способами, которые могут использоваться согласованно. Во-первых, она может отображать данные, которые сгруппированы по ключу (например, список имен, сгруппированный по первой букве). Во-вторых, она может переключаться между двумя контекстными представлениями: сгруппированный список элементов (например, имена) и список только самих ключей группы (например, первые буквы). В UWP сгруппированные данные можно отобразить с помощью элементов [Руководство по элементам управления просмотром в виде списка и сетки](https://msdn.microsoft.com/library/windows/apps/mt186889). |
| LongListSelector с неструктурированными данными | Для повышения быстродействия в случае очень длинных списков мы рекомендуем LongListSelector вместо WindowsPhone Silverlight список даже для неструктурированных, несгруппированных данных. В приложении UWP для длинных списков элементов предпочтительнее использовать [GridView](https://msdn.microsoft.com/library/windows/apps/br242705) независимо от того, поддаются данные группировке или нет. |
| Panorama | Элемент управления WindowsPhone Silverlight Panorama сопоставляется с [руководство по элементам управления hub в приложениях среды выполнения Windows 8.x](https://msdn.microsoft.com/library/windows/apps/dn449149) и рекомендации для элемента управления hub. <br/> Обратите внимание: элемент управления Panorama окружен текстом от последнего раздела до первого, а его фоновое изображение перемещается в параллаксе относительно разделам. Разделы [Hub](https://msdn.microsoft.com/library/windows/apps/dn251843) не создают обтекание текстом, и параллакс не используется. |
| Сводка | Эквивалентом в UWP элемента управления WindowsPhone Silverlight Pivot является [Windows.UI.Xaml.Controls.Pivot](https://msdn.microsoft.com/library/windows/apps/dn608241). Эта функция доступна для всех семейств устройств. |

**Примечание**  визуальное состояние PointerOver используется в пользовательские стили или шаблоны в Windows10 приложениях, но не в приложениях WindowsPhone Silverlight. По другим причинам, почему существующие пользовательские стили или шаблоны могут не подходить для приложений Windows10, включая ключи ресурсов системы, вы используете, изменения наборов визуальных состояний и улучшения производительности, внесенные в стили по умолчанию Windows10 / шаблоны. Мы рекомендуем изменить новую копию шаблона элемента управления по умолчанию для Windows10 и повторно примените свои настройки стиля и шаблона к ней.

Дополнительные сведения об элементах управления UWP см. в разделах [Распределение элементов управления по функциям](https://msdn.microsoft.com/library/windows/apps/mt185405), [Список элементов управления](https://msdn.microsoft.com/library/windows/apps/mt185406) и [Руководство по элементам управления](https://msdn.microsoft.com/library/windows/apps/dn611856).

##  <a name="design-language-in-windows10"></a>Язык дизайна в Windows10

Существуют некоторые отличия в языке дизайна между Windows10 и WindowsPhone Silverlight приложениями. Все сведения см. в разделе [Дизайн](http://dev.windows.com/design). Несмотря на изменения языка дизайна, принципы дизайна остаются прежними: будьте внимательны к деталям, при этом всегда стремитесь к простоте и фокусируйте внимание на содержимом, а не внешнем оформлении, уменьшая количество визуальных элементов и сохраняя характерные особенности цифрового домена, используйте визуальную иерархию (особенно для шрифтового оформления), проектируйте, используя сетку, оживляйте интерфейс с помощью плавных анимаций.

## <a name="localization-and-globalization"></a>Локализация и глобализация

Для локализованных строк можно повторно использовать файл .resx из проекта WindowsPhone Silverlight в проект приложения UWP. Скопируйте файл, добавьте его в проект и измените его расширение на Resources.resw, чтобы механизм поиска находил его по умолчанию. Установите **Действие при сборке** на **PRIResource**, а **Копировать в выходной каталог** на **Не копировать**. Затем можно использовать строки в разметке, указывая атрибут **x: Uid** в элементах XAML. См. [Краткое руководство: использование строковых ресурсов](https://msdn.microsoft.com/library/windows/apps/xaml/hh965329).

Приложения WindowsPhone Silverlight используют класс **CultureInfo** для помочь в глобализации приложения. Приложения UWP используют современную технологию ресурсов (MRT), которая поддерживает динамическую загрузку ресурсов приложения (локализации, масштабирования и темы) и в среду выполнения и в рабочую область конструирования Visual Studio. Дополнительные сведения см. в разделе [Руководство по файлам, данным и глобализации](https://msdn.microsoft.com/library/windows/apps/dn611859).

В разделе [**ResourceContext.QualifierValues**](https://msdn.microsoft.com/library/windows/apps/br206071) описывается загрузка ресурсов для определенного семейства устройств на основе коэффициента выбора ресурсов для семейств устройств.

## <a name="media-and-graphics"></a>Мультимедиа и графика

Читая о мультимедиа и изображениях UWP учитывайте, что принципы разработки Windows поощряют максимальное уменьшение всего излишнего, включая графическую сложность и излишества. Разработка Windows типизирована чистыми и четкими визуальными элементами, шрифтовым оформлением и перемещением. Если ваше приложение будет следовать тем же принципам, оно будет больше похоже на встроенные приложения.

WindowsPhone Silverlight есть тип **RadialGradientBrush** , который не отображается в UWP, несмотря на то, что другие типы [**кисти**](/uwp/api/Windows.UI.Xaml.Media.Brush) . В некоторых случаях вы сможете достичь аналогичного эффекта с помощью растрового изображения. Обратите внимание, что вы можете [создать радиальную градиентную кисть](https://msdn.microsoft.com/library/windows/desktop/dd756679) с помощью Direct2D в [Microsoft DirectX](https://msdn.microsoft.com/library/windows/desktop/ee663274) и XAML C++ UWP.

WindowsPhone Silverlight имеет свойство **System.Windows.UIElement.OpacityMask** , но это свойство не является членом типа UWP [**UIElement**](https://msdn.microsoft.com/library/windows/apps/br208911) . В некоторых случаях вы сможете достичь аналогичного эффекта с помощью растрового изображения. Вы также можете [создать маску непрозрачности](https://msdn.microsoft.com/library/windows/desktop/ee329947) с помощью Direct2D в [Microsoft DirectX](https://msdn.microsoft.com/library/windows/desktop/ee663274) и приложении UWP XAML C++. Но распространенным вариантом использования для **OpacityMask** является применение единого растрового изображения, адаптирующегося к светлой и темной темам. Для векторной графики можно использовать системные кисти, учитывающие тему (например, показанные ниже круговые диаграммы). Но создание растрового изображения, учитывающего тему (например, показанные ниже флажки), требует другого подхода.

![Учитывающее тему растровое изображение](images/wpsl-to-uwp-case-studies/wpsl-to-uwp-theme-aware-bitmap.png)

В приложении WindowsPhone Silverlight прием — для использования альфа-маска (в формате растрового изображения) в качестве **OpacityMask** **прямоугольник** , заполненная с помощью кисти переднего плана:

```xml
    <Rectangle Fill="{StaticResource PhoneForegroundBrush}" Width="26" Height="26">
        <Rectangle.OpacityMask>
            <ImageBrush ImageSource="/Assets/wpsl_check.png"/>
        </Rectangle.OpacityMask>
    </Rectangle>
```

Самый простой способ перенести его в приложение UWP—использовать [**BitmapIcon**](https://msdn.microsoft.com/library/windows/apps/dn279306), например:

```xml
    <BitmapIcon UriSource="Assets/winrt_check.png" Width="21" Height="21"/>
```

В данном примере winrt\_check.png—альфа-маска в формате растрового изображения, как и wpsl\_check.png. Причем это вполне может быть один и тот же файл. Но вам может понадобиться создать несколько разных размеров winrt\_check.png, используемых для различных коэффициентов масштабирования. Дополнительные сведения об этом и описание изменений в значениях **Width** и **Height** см. в подразделе [Пиксели представления или эффективные пиксели, расстояние от экрана и коэффициенты масштабирование](#view-or-effective-pixels-viewing-distance-and-scale-factors) в этом разделе.

Более общим способом, который подходит в случае различия между оформлением растрового изображения в светлой и темной теме, является использование двух ресурсов изображений—одного с темным передним планом (для светлой темы) и второго со светлым передним планом (для темной темы). Дополнительные сведения о присвоении имени этому набору ресурсов растровых изображений см. в разделе [адаптация ресурсов с учетом языка, масштаба и других квалификаторов](../app-resources/tailor-resources-lang-scale-contrast.md). После присвоения правильного имени набору файлов изображений вы можете ссылаться на них в общем с помощью их имени корневого элемента, например:

```xml
    <Image Source="Assets/winrt_check.png" Stretch="None"/>
```

В WindowsPhone Silverlight **UIElement.Clip** свойство может быть любой формы, которую можно выразить с **геометрии** и обычно сериализуется в разметке XAML в мини-языке **StreamGeometry** . В UWP тип свойства [**Clip**](https://msdn.microsoft.com/library/windows/apps/br208919) — [**RectangleGeometry**](https://msdn.microsoft.com/library/windows/apps/br210259), поэтому вы можете только вырезать прямоугольную область. Разрешение определить прямоугольник с помощью мини-языка будет слишком вольным. Таким образом, чтобы перенести область обрезки в разметку, замените синтаксис атрибута **Clip** и выполните его в синтаксисе элемента свойства следующим образом:

```xml
    <UIElement.Clip>
        <RectangleGeometry Rect="10 10 50 50"/>
    </UIElement.Clip>
```

Обратите внимание, что вы можете [использовать произвольную геометрию в качестве маски в уровне](https://msdn.microsoft.com/library/windows/desktop/dd756654) с Direct2D в [Microsoft DirectX](https://msdn.microsoft.com/library/windows/desktop/ee663274) C++ XAML приложении UWP.

## <a name="navigation"></a>Навигация

При переходе на страницу в приложении WindowsPhone Silverlight, вы используете схему адресации универсального кода ресурса (URI):

```csharp
    NavigationService.Navigate(new Uri("/AnotherPage.xaml", UriKind.Relative)/*, navigationState*/);
```

В приложении UWP вызовите метод [**Frame.Navigate**](https://msdn.microsoft.com/library/windows/apps/br242694) и укажите тип страницы назначения (определяется атрибутом **x:Class** определения разметки XAML данной страницы):


```csharp
    // In a page:
    this.Frame.Navigate(typeof(AnotherPage)/*, parameter*/);

    // In a view model, perhaps inside an ICommand implementation:
    var rootFrame = Windows.UI.Xaml.Window.Current.Content as Windows.UI.Xaml.Controls.Frame;
    rootFrame.Navigate(typeof(AnotherPage)/*, parameter*/);
```

Вы определяете стартовую страницу для приложения WindowsPhone Silverlight в файле WMAppManifest.xml:

```xml
    <DefaultTask Name="_default" NavigationPage="MainPage.xaml" />
```

В приложении UWP для определения стартовой страницы используется императивный код. Вот код из файла App.xaml.cs, в котором это проиллюстрировано:

```csharp
    if (!rootFrame.Navigate(typeof(MainPage), e.Arguments))
```

Сопоставление URI и навигация по фрагментам—это методы навигации URI, поэтому они неприменимы к навигации UWP, которая не базируется на URI. Сопоставление URI существует в ответ на слабо типизированную природу определения страницы назначения с помощью строки URI, что может привести к проблемам хрупкости и поддержки, если страница переместится в другую папку и, соответственно, на другой относительный путь. Приложения UWP Windows используют навигацию на основе типов, которая строго типизирована и проверяется компилятором, а также не имеет проблемы, которую решает сопоставление URI. Вариант использования для навигации по фрагментам — передать определенный контекст на странице назначения, чтобы страница могла представлять определенный фрагмент содержимого для прокрутки или отображать его иным образом. Та же цель может быть достигнута путем передачи параметра навигации при вызове метода [**Navigate**](https://msdn.microsoft.com/library/windows/apps/br242694).

Подробнее: [Навигация](https://msdn.microsoft.com/library/windows/apps/mt187344).

## <a name="resource-key-reference"></a>Ссылка на ключ ресурса

Язык дизайна эволюционировал для Windows10 и поэтому некоторые стили системы изменились, и были удалены или переименованы многие ключи ресурсов системы. Редактор разметки XAML в Visual Studio выделяет ссылки на ключи ресурсов, которые не могут быть разрешены. Например, редактор разметки XAML подчеркнет ссылку к ключу стиля `PhoneTextNormalStyle` волнистой линией красного цвета. Если она не будет исправлена, приложение немедленно завершит работу при попытке развернуть его в эмуляторе или на устройстве. Поэтому важно обращать внимание на правильность разметки XAML. И вы увидите, что Visual Studio— это отличный инструмент для нахождения подобных проблем.

См. также раздел [Текст](#text) ниже.

## <a name="status-bar-system-tray"></a>Строка состояния (панель задач)

Панель задач (установленная в разметке XAML с `shell:SystemTray.IsVisible`) теперь называется строкой состояния и отображается по умолчанию. Вы можете контролировать ее видимость в императивном коде, вызывая методы [**Windows.UI.ViewManagement.StatusBar.ShowAsync**](https://msdn.microsoft.com/library/windows/apps/dn610343) и [**HideAsync**](https://msdn.microsoft.com/library/windows/apps/dn610339).

## <a name="text"></a>Текст

Текст (или шрифтовое оформление) является важным аспектом для приложения UWP. Во время переноса, возможно, необходимо будет пересмотреть визуальные элементы в представлениях, чтобы они соответствовали новому языку дизайна. Используйте эти иллюстрации, чтобы найти стили системы UWP  **TextBlock**, которые доступны. Найдите те, которые соответствуют используемым стилям WindowsPhone Silverlight. Кроме того можно создать собственные универсальные стили и скопировать в них свойства из системных стилей WindowsPhone Silverlight.

![Стили системы TextBlock для приложений для Windows 10](images/label-uwp10stylegallery.png)

Стили системы TextBlock для Windows10 приложений

В приложении WindowsPhone Silverlight семейством шрифтов по умолчанию является Segoe WP. В приложении для Windows10 семейством шрифтов по умолчанию является Segoe UI. В результате метрика шрифта в приложении может выглядеть иначе. Если вы хотите воспроизвести вид текста WindowsPhone Silverlight, можно задать собственную метрику с помощью свойства, такие как [**LineHeight**](https://msdn.microsoft.com/library/windows/apps/br209671) и [**LineStackingStrategy**](https://msdn.microsoft.com/library/windows/apps/br244362). Дополнительные сведения см. в разделе [Рекомендации по шрифтам](https://msdn.microsoft.com/library/windows/apps/hh700394.aspx) и [Оформление приложений UWP](http://dev.windows.com/design).

## <a name="theme-changes"></a>Изменения тем

Для приложения WindowsPhone Silverlight тема по умолчанию используется Темная по умолчанию. Для устройств Windows10 тема по умолчанию было изменено, но вы можете управлять используемой темой, объявив запрошенную тему в файле App.xaml. Например, чтобы на всех устройствах отображалась темная тема, добавьте `RequestedTheme="Dark"` к корневому элементу Application.

## <a name="tiles"></a>Плитки

Плитки для приложений UWP поведение подобно поведению живых плиток для приложений WindowsPhone Silverlight, несмотря на то, что существуют некоторые различия. Например, код, который вызывает метод **Microsoft.Phone.Shell.ShellTile.Create** для создания вспомогательных плиток, должен быть перенесен для вызова [**SecondaryTile.RequestCreateAsync**](https://msdn.microsoft.com/library/windows/apps/br230606). Здесь приведен пример до и после, сначала версия WindowsPhone Silverlight.


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

Код, обновляющий плитку с помощью метода **Microsoft.Phone.Shell.ShellTile.Update** или класса **Microsoft.Phone.Shell.ShellTileSchedule**, должен быть перенесен для использования класса [**TileUpdateManager**](https://msdn.microsoft.com/library/windows/apps/br208622), [**TileUpdater**](https://msdn.microsoft.com/library/windows/apps/br208628), [**TileNotification**](https://msdn.microsoft.com/library/windows/apps/br208616) и/или [**ScheduledTileNotification**](https://msdn.microsoft.com/library/windows/apps/hh701637).

Дополнительные сведения о плитках, всплывающих уведомлениях, индикаторах событий, баннерах и уведомлениях см. в разделах [Создание плиток](https://msdn.microsoft.com/library/windows/apps/xaml/hh868260) и [Работа с плитками, индикаторами событий и всплывающими уведомлениями](https://msdn.microsoft.com/library/windows/apps/xaml/hh868259). Особенности размеров визуальных ресурсов, используемых для плиток UWP см. в разделе [Визуальные активы плиток и всплывающих уведомлений](https://msdn.microsoft.com/library/windows/apps/hh781198).

## <a name="toasts"></a>Всплывающие уведомления

Код, отображающий всплывающее уведомление с помощью класса **Microsoft.Phone.Shell.ShellToast**, должен быть перенесен для использования класса [**ToastNotificationManager**](https://msdn.microsoft.com/library/windows/apps/br208642), [**ToastNotifier**](https://msdn.microsoft.com/library/windows/apps/br208653), [**ToastNotification**](https://msdn.microsoft.com/library/windows/apps/br208641) или [**ScheduledToastNotification**](https://msdn.microsoft.com/library/windows/apps/br208607). Обратите внимание, что на мобильных устройствах ориентированный на потребителя термин для "всплывающего уведомления"—"баннер".

См. раздел [Работа с плитками, индикаторами событий и всплывающими уведомлениями](https://msdn.microsoft.com/library/windows/apps/xaml/hh868259).

## <a name="view-or-effective-pixels-viewing-distance-and-scale-factors"></a>Пиксели представления или эффективные пиксели, расстояние от экрана и коэффициенты масштабирование

WindowsPhone Silverlight и приложениями Windows10 отличаются тем, как они абстрагируют размер и макет элементов пользовательского интерфейса от физических размеров и разрешения устройств. Приложение WindowsPhone Silverlight использует пиксели представления для этого. В Windows10 принцип пикселей представления был доработан эффективных пикселей. Вот описание этого термина, его значения и преимуществ.

Термин «разрешение» относится к измерению плотности пикселей, а не, как обычно считается, количества пикселей. «Эффективное разрешение» — это способ, которым физические пиксели, образующие изображение или глиф разрешают для глаза данные различия в расстоянии от экрана и физическом размере пикселей устройства (плотность пикселей обратна физическому размеру пикселей). Эффективное разрешение — это хорошая метрика для создания взаимодействия на ее основе, поскольку оно ориентируется на пользователя. Поняв все факторы и контролируя размер элементов пользовательского интерфейса, вы можете сделать взаимодействие с пользователем комфортным.

Для приложения WindowsPhone Silverlight все экраны телефона являются строго 480 пикселей представления в ширину, без исключений, независимо от того, сколько физических пикселей в экране, или от плотности его пикселей или физического размера. Это означает, что элемент **изображения** с `Width="48"` будет составлять ровно одну десятую часть ширины экрана любого телефона, на котором может выполняться приложение WindowsPhone Silverlight.

Windows10 в приложении, это *не* так, что все устройства имеют фиксированное число ширины эффективных пикселей. Скорее всего это очевидно с учетом диапазона ширины различных устройств, в которых можно выполнить приложение UWP. Количество эффективных пикселей в ширину разное для различных устройств, от 320epx для самых маленьких устройств и до 1024epx для мониторов скромных размеров и выходит далеко за этот предел. Вам потребуется всего лишь продолжать использовать элементы с автоматически устанавливаемым размером и динамические панели макета, как и раньше. В некоторых случаях в свойствах элементов пользовательского интерфейса в разметке XAML задается фиксированный размер. Коэффициент масштабирования автоматически применяется к приложению в зависимости от устройства, на котором оно запускается, и от настроенных пользователем параметров экрана. Коэффициент масштабирования служит для сохранения представления элементами пользовательского интерфейса цели сенсорного ввода (и чтения) с более или менее постоянным размером для пользователя устройств с различными размерами экрана. А благодаря динамическому макету вместо визуального масштабирования пользовательского интерфейса на разных устройствах, он будет вмещать желаемый объем содержимого в доступное пространство.

Поскольку цифра 480 раньше была фиксированной шириной в представлении пикселях для экрана телефона и это значение теперь обычно меньше в эффективных пикселях, основным правилом является умножение любого размера в приложении WindowsPhone Silverlight на коэффициент 0,8.

Чтобы ваше приложение будет наилучшим образом выглядело на любых устройствах, мы рекомендуем создать каждый растровый ресурс в различных размерах для определенного коэффициента масштабирования. Если вы предоставите ресурсы с масштабом 100%, 200% и 400% (в такой последовательности), это обеспечит отличные результаты в большинстве случаев при любых промежуточных коэффициентах масштабирования.

**Примечание**Если по какой-либо причине вы не более одного размера, создайте ресурсы масштабом 100%. В Microsoft Visual Studio шаблон проекта по умолчанию для приложений UWP предоставляет ресурсы официальной фирменной символики (изображения плиток и эмблемы) только в одном размере, но масштаб при этом не 100%. При создании ресурсов для своего приложения следуйте рекомендациям в этом разделе и предусмотрите размеры 100%, 200% и 400%, а также используйте пакеты ресурсов.

В случае использования сложного изображения, возможно, следует предусмотреть еще больше размеров. Если используются векторные изображения, тогда процесс создания высококачественных ресурсов при любом масштабе относительно прост.

Мы не рекомендуем пытаться поддерживать все коэффициенты масштабирования, что полный список коэффициенты масштабирования в Windows10 — 100%, 125%, 150%, 200%, 250%, 300% и 400%. Если вы все-таких их предоставляете, Магазин выберет правильные ресурсы для каждого устройства, и только они будут загружены. Магазин выбирает ресурсы для загрузки на основе DPI устройства.

Подробнее см. в разделе [Адаптивный дизайн 101 для приложений UWP](https://msdn.microsoft.com/library/windows/apps/dn958435).

## <a name="window-size"></a>Размер окна

В приложении UWP можно задать минимальный размер окна (ширину и высоту) с помощью императивного кода. Стандартный минимальный размер— 500x320эфф. пикселей. Это также самый маленький из допустимых минимальных размеров. Самый большой из допустимых минимальных размеров—500 x 500 эфф. пикселей.

```csharp
   Windows.UI.ViewManagement.ApplicationView.GetForCurrentView().SetPreferredMinSize
        (new Size { Width = 500, Height = 500 });
```

Следующий раздел называется [Перенос для ввода-вывода, устройства и модели приложения](wpsl-to-uwp-input-and-sensors.md).

## <a name="related-topics"></a>Еще по теме

* [Сопоставление пространства имен и класса](wpsl-to-uwp-namespace-and-class-mappings.md)

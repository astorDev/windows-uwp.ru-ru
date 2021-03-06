---
ms.assetid: 333f67f5-f012-4981-917f-c6fd271267c6
description: В этом практическом примере, который строится на информации в книжном магазине, начинается с Windows Phone приложения Silverlight, отображающего сгруппированные данные в LongListSelector.
title: Windows Phone пример использования Silverlight с UWP, Bookstore2
ms.date: 02/08/2017
ms.topic: article
keywords: Windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: 1d1440bf3cfded6b50eb58feffd322ea484e488a
ms.sourcegitcommit: b52ddecccb9e68dbb71695af3078005a2eb78af1
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/20/2019
ms.locfileid: "74260106"
---
# <a name="windowsphone-silverlight-to-uwp-case-study-bookstore2"></a>Windows Phone пример внедрения из Silverlight в UWP: Bookstore2


В этом примере, который строится на информации в [Bookstore1](wpsl-to-uwp-case-study-bookstore1.md), начинается с Windows Phone приложения Silverlight, отображающего сгруппированные данные в **LongListSelector**. В модели представления каждый экземпляр класса **Author** представляет группу книг одного автора, а в классе **LongListSelector** мы можем просмотреть список книг, сгруппированных по автору, или уменьшить представление, чтобы просмотреть список переходов по авторам. Список переходов — это гораздо более быстрый способ навигации, чем прокрутка списка книг. Мы рассмотрим шаги переноса приложения в приложение Windows 10 универсальная платформа Windows (UWP).

**Обратите внимание** ,   при открытии Bookstore2Universal\_10 в Visual Studio, если отображается сообщение "требуется обновление Visual Studio", выполните действия по настройке целевой версии платформы в [TargetPlatformVersion](w8x-to-uwp-troubleshooting.md).

## <a name="downloads"></a>Загрузки

[Скачайте приложение Bookstore2WPSL8 Windows Phone Silverlight](https://codeload.github.com/MicrosoftDocs/windows-topic-specific-samples/zip/Bookstore2WPSL8).

[Скачайте приложение Bookstore2Universal\_10 Windows 10](https://codeload.github.com/MicrosoftDocs/windows-topic-specific-samples/zip/Bookstore2Universal_10).

##  <a name="the-windowsphone-silverlight-app"></a>Приложение Windows Phone Silverlight

На рисунке ниже показано приложение Bookstore2WPSL8, которое мы будем переносить. Это элемент **LongListSelector** для вертикальной прокрутки книг, сгруппированных по автору. Вы можете уменьшить представление и перейти в список переходов, откуда можно вернуться в любую группу. Приложение состоит из двух основных элементов: модели представления, которая дает сгруппированный источник данных, и пользовательского интерфейса, который привязывается к этой модели. Как мы увидим, оба этих фрагмента легко можно получить от Windows Phone технологии Silverlight до универсальная платформа Windows (UWP).

![how bookstore2wpsl8 looks](images/wpsl-to-uwp-case-studies/c02-01-wpsl-how-the-app-looks.png)

##  <a name="porting-to-a-windows10-project"></a>Перенос в проект Windows 10

Создание нового проекта в Visual Studio, копирование в него файлов Bookstore2WPSL8 и подключение скопированных файлов к новому проекту не займут много времени. Начните с создания нового проекта пустого приложения (Windows Universal). Назовите его Bookstore2Universal\_10. Это файлы для копирования из Bookstore2WPSL8 в Bookstore2Universal\_10.

-   Скопируйте папку, содержащую PNG-файлы с изображением книги (папка \\активы\\Коверимажес). После копирования убедитесь, что в **Обозревателе решений** включена функция **Показать все файлы**. Щелкните правой кнопкой мыши на скопированной папке и выберите **Включить в проект**. Под выполнением этой команды мы и понимаем включение файлов или папок в проект. Каждый раз, копируя файл или папку, нажимайте **Обновить** в **Обозревателе решений**, а затем включайте этот файл или папку в проект. Для тех файлов, которые вы заменяете в конечном местоположении, выполнять эту операцию не нужно.
-   Скопируйте папку, содержащую исходный файл модели представления (папка — \\ViewModel).
-   Скопируйте файл MainPage.xaml и замените на него файл в конечной папке.

Мы можем удержать App. XAML и App.xaml.cs, что Visual Studio создала для нас в проекте Windows 10.

Измените только что скопированный исходный код и файлы разметки и измените все ссылки на пространство имен Bookstore2WPSL8 на Bookstore2Universal\_10. Эту операцию можно быстро выполнить при помощи функции **Заменить в файлах**. В императивном коде в исходном файле модели представления необходимо сделать следующие изменения для переноса.

-   Измените `System.ComponentModel.DesignerProperties` на `DesignMode`, а затем примените к нему команду **Разрешить**. Удалите свойство `IsInDesignTool` и используйте функцию IntelliSense, чтобы добавить правильное имя свойства: `DesignModeEnabled`.
-   Примените команду **Разрешить** к `ImageSource`.
-   Примените команду **Разрешить** к `BitmapImage`.
-   Удалите `using System.Windows.Media;` и `using System.Windows.Media.Imaging;`.
-   Измените значение, возвращаемое свойством **Bookstore2Universal\_10. буксторевиевмодел. AppName** , с "BOOKSTORE2WPSL8" на "Bookstore2Universal".
-   Как и ранее для [Bookstore1](wpsl-to-uwp-case-study-bookstore1.md), измените реализацию свойства **BookSku.CoverImage** (см. раздел [Привязка изображения к модели представления](wpsl-to-uwp-case-study-bookstore1.md)).

В файле MainPage.xaml необходимо внести следующие изменения для переноса.

-   Измените `phone:PhoneApplicationPage` на `Page` (включая вхождения в синтаксисе элементов свойств).
-   Удалите объявления префикса пространства имен `phone` и `shell`.
-   Измените "clr-namespace" на "using" в остальных объявлениях префикса пространства имен.
-   Удалите `SupportedOrientations="Portrait"` и `Orientation="Portrait"`, а затем настройте **книжную** ориентацию в манифесте пакета приложения в новом проекте.
-   Удалите `shell:SystemTray.IsVisible="True"`.
-   Типы преобразователей элемента списка переходов (которые присутствуют в разметке как ресурсы) перемещены в пространство имен [**Windows.UI.Xaml.Controls.Primitives**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Primitives). Поэтому добавьте объявление префикса пространства имен\_пользовательского интерфейса\_XAML\_элементы управления\_примитивы и сопоставьте его с **Windows. UI. XAML. Controls. примитивы**. Для ресурсов преобразователя элемента списка переходов измените префикс с `phone:` на `Windows_UI_Xaml_Controls_Primitives:`.
-   Как и в примере [Bookstore1](wpsl-to-uwp-case-study-bookstore1.md), замените все ссылки на стиль `PhoneTextExtraLargeStyle` **TextBlock** ссылкой на `SubtitleTextBlockStyle`, замените `PhoneTextSubtleStyle` на `SubtitleTextBlockStyle`, `PhoneTextNormalStyle` на `CaptionTextBlockStyle`, а `PhoneTextTitle1Style` на `HeaderTextBlockStyle`.
-   Существует одно исключение в `BookTemplate`. Стиль второго элемента **TextBlock** должен ссылаться на `CaptionTextBlockStyle`.
-   Удалите атрибут FontFamily из **TextBlock** в `AuthorGroupHeaderTemplate` и настройте фон **Border** для ссылки на `SystemControlBackgroundAccentBrush` вместо `PhoneAccentBrush`.
-   Из-за [изменений, связанных с пикселями представления](wpsl-to-uwp-porting-xaml-and-ui.md), необходимо проверить разметку и умножить все фиксированные измерения размеров (поля, ширина, высота и т. д.) на 0,8.

## <a name="replacing-the-longlistselector"></a>Замена LongListSelector


Для замены **LongListSelector** элементом управления [**SemanticZoom**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.SemanticZoom) нужно выполнить несколько действий. Рассмотрим их подробнее. Класс **LongListSelector** привязывается непосредственно к сгруппированному источнику данных, но **SemanticZoom** содержит элементы управления [**ListView**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.ListView) или [**GridView**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.GridView), которые косвенно привязываются к данным через адаптер [**CollectionViewSource**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Data.CollectionViewSource). Класс **CollectionViewSource** должен быть размещен в разметке как ресурс, поэтому сначала добавим его в разметку в файле MainPage.xaml внутри `<Page.Resources>`.

```xml
    <CollectionViewSource
        x:Name="AuthorHasACollectionOfBookSku"
        Source="{Binding Authors}"
        IsSourceGrouped="true"/>
```

Обратите внимание, что привязка к **LongListSelector.ItemsSource** становится значением **CollectionViewSource.Source**, а **LongListSelector.IsGroupingEnabled** становится **CollectionViewSource.IsSourceGrouped**. У класса **CollectionViewSource** есть имя (не ключ, как можно было бы ожидать), которое можно использовать для привязки.

Затем замените `phone:LongListSelector` следующей разметкой, и мы получим предварительный **SemanticZoom** для работы.

```xml
    <SemanticZoom>
        <SemanticZoom.ZoomedInView>
            <ListView
                ItemsSource="{Binding Source={StaticResource AuthorHasACollectionOfBookSku}}"
                ItemTemplate="{StaticResource BookTemplate}">
                <ListView.GroupStyle>
                    <GroupStyle
                        HeaderTemplate="{StaticResource AuthorGroupHeaderTemplate}"
                        HidesIfEmpty="True"/>
                </ListView.GroupStyle>
            </ListView>
        </SemanticZoom.ZoomedInView>
        <SemanticZoom.ZoomedOutView>
            <ListView
                ItemsSource="{Binding CollectionGroups, Source={StaticResource AuthorHasACollectionOfBookSku}}"
                ItemTemplate="{StaticResource ZoomedOutAuthorTemplate}"/>
        </SemanticZoom.ZoomedOutView>
    </SemanticZoom>
```

Режимы плоского списка и списка переходов **LongListSelector** реализуются в увеличенном и уменьшенном представлении **SemanticZoom** соответственно. Увеличенное представление — это свойство, для которого вам нужно задать экземпляр **ListView**. В этом случае для уменьшенного представления также задается **ListView**, и оба элемента управления **ListView** привязываются к нашему элементу **CollectionViewSource**. При увеличенном представлении используются те же шаблон элементов, шаблон заголовка группы и параметр **HideEmptyGroups** (теперь он называется **HidesIfEmpty**), что и в плоском списке **LongListSelector**. В уменьшенном представлении используется шаблон элемента, очень похожий на шаблон в стиле списка переходов **LongListSelector** (`AuthorNameJumpListStyle`). Также обратите внимание, что уменьшенное представление привязывается к специальному свойству **CollectionViewSource** с именем **CollectionGroups**, которое представляет собой коллекцию групп, а не элементов.

Нам больше не требуется `AuthorNameJumpListStyle`, по крайней мере, не полностью. Нам нужен только шаблон данных для групп (в этом приложении это авторы) в уменьшенном представлении. Поэтому мы удалим стиль `AuthorNameJumpListStyle` и заменим его следующим шаблоном данных.

```xml
   <DataTemplate x:Key="ZoomedOutAuthorTemplate">
        <Border Margin="9.6,0.8" Background="{Binding Converter={StaticResource JumpListItemBackgroundConverter}}">
            <TextBlock Margin="9.6,0,9.6,4.8" Text="{Binding Group.Name}" Style="{StaticResource SubtitleTextBlockStyle}"
            Foreground="{Binding Converter={StaticResource JumpListItemForegroundConverter}}" VerticalAlignment="Bottom"/>
        </Border>
    </DataTemplate>
```

Обратите внимание, что так как контекст данных этого шаблона — это группа, а не элемент, мы осуществляем привязку к специальному свойству **Group**.

Теперь вы можете выполнить сборку и запустить приложение. Вот как это выглядит на мобильном эмуляторе.

![приложение uwp на мобильном устройстве с начальными изменениями исходного кода](images/wpsl-to-uwp-case-studies/c02-02-mob10-initial-source-code-changes.png)

Модель представления, а также увеличенное и уменьшенное представления интегрированы правильно, хотя присутствует одна проблема. Ее суть состоит в том, что нам необходимо сделать больше работы по оформлению стиля и шаблонов. Например, правильные стили и кисти еще не используются, поэтому текст невидим в заголовках групп, которые можно щелкнуть, чтобы уменьшить масштаб. При запуске приложения на настольном устройстве вы увидите вторую ошибку, которая заключается в том, что приложение еще не адаптирует свой интерфейс пользователя, чтобы обеспечить лучшую работу и использование пространства на больших устройствах, где Windows может быть значительно больше, чем экран мобильного устройства. Поэтому в следующих нескольких разделах ([Начальный стиль и шаблоны](#initial-styling-and-templating), [Адаптивный пользовательский интерфейс](#adaptive-ui) и [Окончательный стиль](#final-styling)) мы исправим эти проблемы.

## <a name="initial-styling-and-templating"></a>Начальный стиль и шаблоны

Чтобы придать заголовкам групп привлекательный вид, измените параметр `AuthorGroupHeaderTemplate` и задайте значение **Margin**`"0,0,0,9.6"` на **Border**.

Чтобы оптимально разместить элементы книги, измените параметр `BookTemplate` и задайте для **Margin** значение `"9.6,0"` на обоих **TextBlock**.

Для создания оптимальной структуры названия приложения и заголовка страницы, внутри `TitlePanel` удалите верхний элемент **Margin** на втором **TextBlock**, задав значение `"7.2,0,0,0"`. И на самом `TitlePanel` измените значение поля на `0` (или другое значение, которое, по вашему мнению, выглядит хорошо)

Измените фон `LayoutRoot` на `"{ThemeResource ApplicationPageBackgroundThemeBrush}"`.

## <a name="adaptive-ui"></a>Адаптивный пользовательский интерфейс

Так как мы начали работу с приложением для телефона, не удивительно, что макет пользовательского интерфейса наших перенесенных приложений подходит только для небольших устройств и узких окон на этом этапе процесса. Но мы действительно хотим, чтобы пользовательский интерфейс сам адаптировался и оптимально использовал пространство, когда приложение запускается в широком окне (это возможно только на устройстве с большим экраном). Мы также хотим, чтобы он использовал только текущий пользовательский интерфейс при узком окне приложения (это происходит на небольшом устройстве, а также может возникать на большом устройстве).

Для этих целей мы можем использовать адаптивную функцию диспетчера визуальных состояний. Зададим свойства визуальных элементов так, чтобы по умолчанию пользовательский интерфейс помещался в узком состоянии с использованием шаблонов, используемых сейчас. Затем необходимо определить, когда окно приложения "более широкое" или "эквивалентное" для конкретного размера (измеренного в [эффективных пикселях](wpsl-to-uwp-porting-xaml-and-ui.md)) и изменим свойства визуальных элементов, чтобы получить больший и более широкий макет. Мы внесем эти изменения свойств в визуальное состояние и будем использовать адаптивный триггер для постоянного контроля и определения, следует ли применять это визуальное состояние или нет, в зависимости от ширины окна в эффективные пикселях. В данном случае мы активируем на ширине окна, но можно также активировать на высоте окна.

Минимальная ширина окна в 548 epx подходит для нашего примера, так как это размер самого небольшого устройства, на котором мы хотели бы отображать широкий макет. Размер экрана телефона обычно не превышает 548 epx, поэтому на таком небольшом устройстве, мы можем использовать стандартный узкий макет. На компьютере окно по умолчанию запускается достаточно широким для активации переключения к широкому состоянию. Это означает отображение элементов размером 250x250. В этом состоянии вы сможете перетащить окно и сделать его достаточно узким для отображения минимум двух столбцов элементов размером 250x250. При более узком размере триггер будет отключен, широкое визуальное состояние будет удалено и стандартный узкий макет будет использоваться.

Перед выбором адаптивного элемента диспетчера визуальных состояний нам предварительно необходимо создать широкое состояние. Это означает добавление некоторых новых визуальных элементов и шаблонов в нашу разметку. В описанных ниже действиях указано, как это сделать. С помощью соглашений об именах для визуальных элементов и шаблонов мы включим слово "wide" в имена всех элементов или шаблонов, предназначенных для широкого состояния. Если элемент или шаблон не содержат слова "wide", тогда можно предположить, что он предназначен для узкого состояния, которое является состоянием по умолчанию и значения свойств которого задаются как локальные значения в визуальных элементах на странице. Только значения свойства для широкого состояния устанавливаются через реальное визуальное состояние в разметке.

-   Создайте копию элемента управления [**SemanticZoom**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.SemanticZoom) в разметке и задайте значение `x:Name="narrowSeZo"` на копии. В оригинале настройте `x:Name="wideSeZo"` и также задайте значение `Visibility="Collapsed"`, чтобы широкий элемент не отображался по умолчанию.
-   В `wideSeZo` измените объекты **ListView** на **GridView** в представлении с уменьшенным и увеличенным масштабом.
-   Создайте копии этих трех ресурсов `AuthorGroupHeaderTemplate`, `ZoomedOutAuthorTemplate` и `BookTemplate` и добавьте слово `Wide` к ключам копий. Также обновите элемент `wideSeZo`, чтобы он ссылался на ключи этих новых ресурсов.
-   Замените содержимое `AuthorGroupHeaderTemplateWide` на `<TextBlock Style="{StaticResource SubheaderTextBlockStyle}" Text="{Binding Name}"/>`.
-   Замените содержимое `ZoomedOutAuthorTemplateWide` на:

```xml
    <Grid HorizontalAlignment="Left" Width="250" Height="250" >
        <Border Background="{StaticResource ListViewItemPlaceholderBackgroundThemeBrush}"/>
        <StackPanel VerticalAlignment="Bottom" Background="{StaticResource ListViewItemOverlayBackgroundThemeBrush}">
          <TextBlock Foreground="{StaticResource ListViewItemOverlayForegroundThemeBrush}"
              Style="{StaticResource SubtitleTextBlockStyle}"
            Height="80" Margin="15,0" Text="{Binding Group.Name}"/>
        </StackPanel>
    </Grid>
```

-   Замените содержимое `BookTemplateWide` на:

```xml
    <Grid HorizontalAlignment="Left" Width="250" Height="250">
        <Border Background="{StaticResource ListViewItemPlaceholderBackgroundThemeBrush}"/>
        <Image Source="{Binding CoverImage}" Stretch="UniformToFill"/>
        <StackPanel VerticalAlignment="Bottom" Background="{StaticResource ListViewItemOverlayBackgroundThemeBrush}">
            <TextBlock Style="{StaticResource SubtitleTextBlockStyle}"
                Foreground="{StaticResource ListViewItemOverlaySecondaryForegroundThemeBrush}"
                TextWrapping="NoWrap" TextTrimming="CharacterEllipsis"
                Margin="12,0,24,0" Text="{Binding Title}"/>
            <TextBlock Style="{StaticResource CaptionTextBlockStyle}" Text="{Binding Author.Name}"
                Foreground="{StaticResource ListViewItemOverlaySecondaryForegroundThemeBrush}" TextWrapping="NoWrap"
                TextTrimming="CharacterEllipsis" Margin="12,0,12,12"/>
        </StackPanel>
    </Grid>
```

-   Для широкого состояния группам в представлении с увеличенным масштабом потребуется больше вертикального пространства вокруг них. Создав несколько шаблонов панелей элементов и добавив ссылки на них, мы получим нужные результаты. Вот как выглядит разметка.

```xml
   <ItemsPanelTemplate x:Key="ZoomedInItemsPanelTemplate">
        <ItemsWrapGrid Orientation="Horizontal" GroupPadding="0,0,0,20"/>
    </ItemsPanelTemplate>
    ...

    <SemanticZoom x:Name="wideSeZo" ... >
        <SemanticZoom.ZoomedInView>
            <GridView
            ...
            ItemsPanel="{StaticResource ZoomedInItemsPanelTemplate}">
            ...
```

-   Наконец, добавьте соответствующую разметку диспетчера визуальных состояний как первый дочерний элемент `LayoutRoot`.

```xml
    <Grid x:Name="LayoutRoot" ... >
        <VisualStateManager.VisualStateGroups>
            <VisualStateGroup>
                <VisualState x:Name="WideState">
                    <VisualState.StateTriggers>
                        <AdaptiveTrigger MinWindowWidth="548"/>
                    </VisualState.StateTriggers>
                    <VisualState.Setters>
                        <Setter Target="wideSeZo.Visibility" Value="Visible"/>
                        <Setter Target="narrowSeZo.Visibility" Value="Collapsed"/>
                    </VisualState.Setters>
                </VisualState>
            </VisualStateGroup>
        </VisualStateManager.VisualStateGroups>

    ...
```

## <a name="final-styling"></a>Окончательный стиль

Все, что осталось, — это окончательное оформление стиля.

-   В `AuthorGroupHeaderTemplate` установите для элемента `Foreground="White"` значение **TextBlock**, чтобы обеспечить правильное отображение на мобильных устройствах.
-   Добавьте `FontWeight="SemiBold"` к **TextBlock** в `AuthorGroupHeaderTemplate` и `ZoomedOutAuthorTemplate`.
-   В `narrowSeZo`заголовки групп и авторы в представлении с уменьшенным масштабом выровнены по левому краю, а не растянуты, поэтому давайте работать с этим. Мы создадим свойство [**HeaderContainerStyle**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.groupstyle.headercontainerstyle) для увеличенного представления со свойством [**HorizontalContentAlignment**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.control.horizontalcontentalignment), для которого задано значение `Stretch`. Мы также создадим свойство [**ItemContainerStyle**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.itemscontrol.itemcontainerstyle) для уменьшенного представления, содержащее тот же класс [**Setter**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Setter). Вот как это выглядит.

```xml
   <Style x:Key="AuthorGroupHeaderContainerStyle" TargetType="ListViewHeaderItem">
        <Setter Property="HorizontalContentAlignment" Value="Stretch"/>
    </Style>

    <Style x:Key="ZoomedOutAuthorItemContainerStyle" TargetType="ListViewItem">
        <Setter Property="HorizontalContentAlignment" Value="Stretch"/>
    </Style>

    ...

    <SemanticZoom x:Name="narrowSeZo" ... >
        <SemanticZoom.ZoomedInView>
            <ListView
            ...
                <ListView.GroupStyle>
                    <GroupStyle
                    ...
                    HeaderContainerStyle="{StaticResource AuthorGroupHeaderContainerStyle}"
                    ...
        <SemanticZoom.ZoomedOutView>
            <ListView
                ...
                ItemContainerStyle="{StaticResource ZoomedOutAuthorItemContainerStyle}"
                ...
```

После описанных выше действий приложение будет выглядеть так.

![Перенесенное приложение для Windows 10, запущенное на настольном устройстве, в увеличенном представлении, которое в два раза превышает размеры окна](images/w8x-to-uwp-case-studies/c02-07-desk10-zi-ported.png)

Приложение с переносом Windows 10, работающее на настольном устройстве, уменьшенное представление, два размера окна  
![приложение с переносом Windows 10, работающее на настольном устройстве, уменьшенное представление, два размера окна](images/w8x-to-uwp-case-studies/c02-08-desk10-zo-ported.png)

Приложение с переносом Windows 10, работающее на настольном устройстве, уменьшенное представление, два размера окна

![Перенесенное приложение для Windows 10 запущено на мобильном устройстве в увеличенном представлении](images/w8x-to-uwp-case-studies/c02-09-mob10-zi-ported.png)

Приложение с переносом Windows 10, работающее на мобильном устройстве, масштабное представление

![Перенесенное приложение для Windows 10 запущено на мобильном устройстве в уменьшенном представлении](images/w8x-to-uwp-case-studies/c02-10-mob10-zo-ported.png)

Приложение с переносом Windows 10, работающее на мобильном устройстве, уменьшенное представление

## <a name="making-the-view-model-more-flexible"></a>Создание более гибкой модели представления

В этом разделе представлен пример, который показывает все преимущества переноса приложения в UWP. Здесь мы описываем необязательные действия, которые можно выполнить, чтобы сделать модель представления более гибкой при доступе через **CollectionViewSource**. Модель представления (исходный файл — ViewModel\\BookstoreViewModel.cs), перенесенная из Windows Phone приложения Silverlight Bookstore2WPSL8 содержит класс с именем author, который является производным от **List&lt;t&gt;** , где **t** — это букску. Это означает, что класс Author *является* группой BookSku.

Когда мы привязываем свойство **CollectionViewSource.Source** к Authors, мы просто сообщаем, что каждый класс Author — это группа *неких элементов*. Класс **CollectionViewSource** определяет, что представляет собой Author. В данном случае это группа BookSku. Этот подход работает, но он не универсальный. Как быть, если Author должен *одновременно* быть группой BookSku *и* группой адресов, где данный автор жил? Author не может *быть* двумя этими группами одновременно. Однако *у* Author может быть любое количество групп. Это и есть решение: используйте шаблон *has-a-group* вместо шаблона *is-a-group*, который мы применяем сейчас, или в дополнение к нему. Вот как это сделать.

-   Измените класс Author, чтобы он не наследовался от **List&lt;T&gt;** .
-   Добавить это поле в 
-   Добавить это свойство в 
-   Конечно, мы можем повторить два указанные выше действия, чтобы добавить любое количество групп в класс Author.
-   Измените реализацию метода AddBookSku на `this.BookSkus.Add(bookSku);`.
-   Теперь Author *содержит* по крайней мере одну группу, и нам нужно сообщить классу **CollectionViewSource**, какую из групп следует использовать. Для этого добавьте это свойство к **CollectionViewSource**: `ItemsPath="BookSkus"`

После этих изменений приложение не изменится функционально, но теперь вы знаете, как расширить класс Author и **CollectionViewSource**, если потребуется. Внесем последние изменения в Author, чтобы использовать группу по умолчанию, если *не указано* свойство **CollectionViewSource.ItemsPath**:

```csharp
    public class Author : IEnumerable<BookSku>
    {
        ...

        public IEnumerator<BookSku> GetEnumerator()
        {
            return this.BookSkus.GetEnumerator();
        }
        System.Collections.IEnumerator System.Collections.IEnumerable.GetEnumerator()
        {
            return this.BookSkus.GetEnumerator();
        }
    }
```

Теперь мы можем при необходимости удалить `ItemsPath="BookSkus"`, а приложение будет работать так же.

## <a name="conclusion"></a>Заключение

В этом примере представлен более амбициозный пользовательский интерфейс, чем в предыдущем примере. Все средства и понятия Windows Phone Silverlight **LongListSelector**(и т. д.) были доступны для приложения UWP в форме **семантикзум**, **ListView**, **GridView**и **CollectionViewSource**. Мы показали, как многократно использовать или копировать и изменять императивный код и разметку в приложении UWP, чтобы расширить его возможности, пользовательский интерфейс и взаимодействие согласно самым узким и самым широким форм-факторам Windows и всем размерам.

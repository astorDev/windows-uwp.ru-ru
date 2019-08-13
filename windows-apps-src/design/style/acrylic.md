---
description: Тип кисти, создающий прозрачную текстуру.
title: Акриловый материал
template: detail.hbs
ms.date: 08/09/2017
ms.topic: article
keywords: windows 10, uwp
pm-contact: yulikl
design-contact: rybick
dev-contact: jevansa
doc-status: Published
ms.localizationpriority: medium
ms.openlocfilehash: 7ed9feb6edae58b906d4f37f0e1f011d4fa42ee9
ms.sourcegitcommit: 789bfe3756c5c47f7324b96f482af636d12c0ed3
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/09/2019
ms.locfileid: "68867736"
---
# <a name="acrylic-material"></a>Акриловый материал

![Главное изображение](images/header-acrylic.svg)

Акрил — это тип [кисти](https://docs.microsoft.com/en-us/uwp/api/Windows.UI.Xaml.Media.Brush), создающий прозрачную текстуру. Акрил можно применять в приложениях к поверхностям, чтобы добавить эффект глубины и поддержать визуальную иерархию.  <!-- By allowing user-selected wallpaper or colors to shine through, acrylic keeps users in touch with the OS personalization they've chosen. -->

> **Важные API**: [класс AcrylicBrush](https://docs.microsoft.com/en-us/uwp/api/windows.ui.xaml.media.acrylicbrush), [свойство Background](https://docs.microsoft.com/en-us/uwp/api/windows.ui.xaml.controls.control.Background)

:::row:::
    :::column:::
Акриловая кисть в светлой теме ![Акриловая в светлой теме](images/Acrylic_LightTheme_Base.png)
    :::column-end:::
    :::column:::
Акриловая кисть в темной теме ![Акриловая в темной теме](images/Acrylic_DarkTheme_Base.png)
    :::column-end:::
:::row-end:::

## <a name="acrylic-and-the-fluent-design-system"></a>Акрил и система проектирования Fluent Design

 Система Fluent Design позволяет создавать современные и эффективные пользовательские интерфейсы, которые отличаются яркостью, глубиной, движением, материальностью и масштабированием. Акрил — это компонент системы проектирования Fluent Design, добавляющий физическую текстуру (материал) и глубину в приложение. Подробные сведения см. в статье [The Fluent Design System for Windows app creators](/windows/apps/fluent-design-system) (Система проектирования Fluent Design для разработчиков приложений Windows).

 ## <a name="video-summary"></a>Видео с кратким описанием

> [!VIDEO https://channel9.msdn.com/Events/Windows/Windows-Developer-Day-Fall-Creators-Update/WinDev002/player]

## <a name="examples"></a>Примеры

:::row:::
    :::column span:::
![Пример изображения](images/XAML-controls-gallery-app-icon.png)
    :::column-end:::
    :::column span="2":::
**XAML Controls Gallery**<br>
Если у вас установлено приложение XAML Controls Gallery, нажмите <a href="xamlcontrolsgallery:/item/Acrylic">здесь</a>, чтобы открыть приложение и увидеть акрил в действии.

<a href="https://www.microsoft.com/store/productId/9MSVH128X2ZT">Получить приложение XAML Controls Gallery (Microsoft Store)</a><br>
<a href="https://github.com/Microsoft/Xaml-Controls-Gallery">Получить исходный код (GitHub)</a>
    :::column-end:::
:::row-end:::

## <a name="acrylic-blend-types"></a>Типы акрилового материала
Самая заметная характеристика акрила — его прозрачность. Для акриловых поверхностей возможны два типа, определяющих, какое изображение проступает через этот материал.
 - Сквозь **акрил фона** просвечивает фон рабочего стола или другие окна, расположенные за активным приложением, что позволяет создать ощущение глубины, пространства между окнами и в то же время учесть предпочтения пользователя в персонализации.
 - **Акрил приложения** создает ощущение глубины в структуре самого приложения, обеспечивая акцентирование внимания и иерархию.

 ![Акрил фона](images/BackgroundAcrylic_DarkTheme.png)

 ![Акрил приложения](images/AppAcrylic_DarkTheme.png)

 Соблюдайте осторожность при сочетании нескольких акриловых поверхностей: несколько слоев акрила фона могут создать некомфортные оптические иллюзии.

## <a name="when-to-use-acrylic"></a>Когда использовать акрил

* Используйте акрил приложения для вспомогательных пользовательских интерфейсов, например на поверхностях, которые могут перекрывать содержимое при прокрутке или взаимодействии.
* Используйте акрил фона для временных элементов пользовательского интерфейса, таких как контекстные меню, всплывающие окна и элементы пользовательского интерфейса с функцией исчезновения.<br />Использование акрила для временных элементов помогает поддерживать визуальную связь с содержимым, из которого вызывается этот временный интерфейс.

Если вы используете акрил приложения для поверхностей навигации, постарайтесь растянуть содержимое приложения на область под панелью с акрилом, чтобы улучшить визуальное восприятие приложения. При использовании NavigationView это выполняется автоматически. Чтобы избежать эффекта полос, старайтесь не помещать несколько панелей с акрилом встык друг к другу, поскольку при этом возникают лишние "швы" на границе между двумя размытыми поверхностями. Акрил позволяет сделать ваши проекты визуально гармоничными, однако при неправильном использовании может привести к визуальному шуму.

Ознакомьтесь с приведенными ниже шаблонами, чтобы выбрать оптимальный способ использования акрила в приложении.

### <a name="horizontal-navigation-or-commanding"></a>Навигация по горизонтали или командный интерфейс

Если ваше приложение не поддерживает NavigationView и вы хотите самостоятельно добавить акриловую поверхность, рекомендуем делать ее средне прозрачной, например со значением непрозрачности 60 %.
 - Когда панель открывается как наложение поверх другого содержимого приложения, используйте для [акрила приложения значение 60 %](#acrylic-theme-resources).

![Приложения карт со встроенными горизонтальными командными элементами](images/Maps_In_App_Acrylic_1.png)

Кроме того, расширение или прокрутка содержимого под акриловой поверхностью позволяет усилить и упростить взаимодействие с пользователем.

### <a name="vertical-panes"></a>Вертикальные панели

Для вертикальных панелей и областей, которые разделяют разные области содержимого в приложении, мы рекомендуем использовать вместо акрила непрозрачный фон. Если вертикальные панели открываются поверх содержимого, как в режимах **Compact** или **Minimal** в NavigationView, мы рекомендуем использовать акрил приложения, чтобы сохранять контекст страницы при открытии этой панели пользователем.

### <a name="transient-surfaces"></a>Временные поверхности

Для приложений со всплывающими окнами меню, немодальными всплывающими окнами и панелями с функцией исчезновения мы рекомендуем использовать акрил фона.

![Шаблон приложения электронной почты с информационным всплывающим меню](images/Mail_TransientContextMenu.png)

Многие наши элементы управления используют акрил по умолчанию. [MenuFlyouts](https://docs.microsoft.com/windows/uwp/design/controls-and-patterns/menus), [AutoSuggestBox](https://docs.microsoft.com/windows/uwp/design/controls-and-patterns/auto-suggest-box), [ComboBox](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.combobox) и аналогичные элементы управления со всплывающими окнами с функцией исчезновения будут при вызове использовать временные акриловые панели.

> [!Note]
> На некоторых устройствах отрисовка акриловых поверхностей существенно нагружает графический процессор, что увеличивает энергопотребление и сокращает время работы от батареи. Эффекты акриловых поверхностей автоматически отключаются, когда устройство переходит в режим экономии заряда. При желании пользователи также могут отключить эти эффекты для всех приложений.

## <a name="usability-and-adaptability"></a>Удобство использования и адаптируемость
Акрил автоматически адаптируется к разнообразным устройствам и контекстам.

В режиме высокой контрастности вместо акриловой поверхности пользователи видят знакомый цвет фона по своему выбору. Кроме того, как акрил фона, так и акрил приложения отображаются сплошным цветом в следующих случаях:
 - если пользователь отключает прозрачность в разделе "Настройки" > "Персонализация" > "Цвет";
 - если активирован режим экономии заряда;
 - если приложение работает на оборудовании с низким быстродействием.

Помимо этого, прозрачность и текстура акрила фона сменится сплошным цветом в следующих случаях:
 - если окно приложения на рабочем столе не активно;
 - если приложение UWP запущено на телефоне, устройстве HoloLens, Xbox или в режиме планшета.

### <a name="legibility-considerations"></a>Вопросы разборчивости
Важно, чтобы любой текст, который ваше приложение представляет пользователям, [соответствовал требованиям к коэффициенту контрастности](../accessibility/accessible-text-requirements.md). Мы подобрали оптимальный режим акрила, чтобы текст интенсивного черного, белого или даже средненасыщенного серого цвета соответствовал требованиям к коэффициенту контрастности. Ресурсы темы, предоставляемые платформой, по умолчанию используют контрастные цвета при 80 % непрозрачности. При размещении на акриловой поверхности текста интенсивного цвета этот показатель можно уменьшить без ущерба для четкости и разборчивости текста. В темном режиме непрозрачность оттенка может составлять 70 %, а в светлом режиме акрил соответствует нормам коэффициента контрастности при непрозрачности 50 %.

Мы не рекомендуем размещать на акриловой поверхности текст контрастного цвета, так как такое сочетание, скорее всего, не будет соответствовать минимальным требованиям к коэффициенту контрастности при размере шрифта 15 пикселей. Постарайтесь не размещать [гиперссылки](../controls-and-patterns/hyperlinks.md) поверх элементов акрила. Кроме того, если вы решите использовать настройки оттенка или уровня прозрачности, отличные от предусмотренных по умолчанию в ресурсах темы, проверьте, как это скажется на разборчивости текста.

## <a name="acrylic-theme-resources"></a>Ресурсы акриловой темы
Вы можете легко применить акрил к поверхностям приложения с помощью нового элемента XAML AcrylicBrush или заранее определенных ресурсов темы AcrylicBrush. Сначала выберите акрил фона или акрил приложения. Изучите рекомендации по представленным выше типичным шаблонам приложений.

Мы создали для акрила фона и для акрила приложения подборку ресурсов темы, которые учитывают тему приложения и при необходимости отображаются сплошным цветом. Ресурсы с именем *AcrylicWindow* относятся к акрилу фона, а с именем *AcrylicElement* — к акрилу приложения.

<table>
    <tr>
        <th align="center">Ключ ресурса</th>
        <th align="center">Непрозрачность оттенка</th>
        <th align="center"><a href="color.md">Резервный цвет</a> </th>
    </tr>
    <tr>
        <td> SystemControlAcrylicWindowBrush, SystemControlAcrylicElementBrush <br/> SystemControlChromeLowAcrylicWindowBrush, SystemControlChromeLowAcrylicElementBrush <br/> SystemControlBaseHighAcrylicWindowBrush, SystemControlBaseHighAcrylicElementBrush <br/> SystemControlBaseLowAcrylicWindowBrush, SystemControlBaseLowAcrylicElementBrush <br/> SystemControlAltHighAcrylicWindowBrush, SystemControlAltHighAcrylicElementBrush <br/> SystemControlAltLowAcrylicWindowBrush, SystemControlAltLowAcrylicElementBrush </td>
        <td align="center"> 80% </td>
        <td> ChromeMedium <br/> ChromeLow <br/><br/> BaseHigh <br/><br/> BaseLow <br/><br/> AltHigh <br/><br/> AltLow </td>
    </tr>
    </tr>
        <td> <b>Рекомендации по использованию:</b> Эти ресурсы акрила общего назначения прекрасно работают в широком диапазоне сценариев использования. Если приложение содержит второстепенный текст цвета AltMedium с размером текста менее 18 пикселей, разместите за этим текстом акриловый ресурс со значением 80 %, чтобы <a href="../accessibility/accessible-text-requirements.md">соблюсти требования к коэффициенту контрастности</a>. </td>
    </tr>
    <tr>
        <td> SystemControlAcrylicWindowMediumHighBrush, SystemControlAcrylicElementMediumHighBrush <br/> SystemControlBaseHighAcrylicWindowMediumHighBrush, SystemControlBaseHighAcrylicElementMediumHighBrush </td>
        <td align="center"> 70 % </td>
        <td> ChromeMedium <br/><br/> BaseHigh </td>
    </tr>
    <tr>
        <td> <b>Рекомендации по использованию:</b> Если приложение содержит второстепенный текст цвета AltMedium с размером текста 18 пикселей и более, за текстом можно расположить эти более прозрачные акриловые ресурсы со значением 70 %. Мы рекомендуем использовать эти ресурсы в верхней горизонтальной навигационной и командной областях вашего приложения.  </td>
    </tr>
    <tr>
        <td> SystemControlChromeHighAcrylicWindowMediumBrush, SystemControlChromeHighAcrylicElementMediumBrush <br/> SystemControlChromeMediumAcrylicWindowMediumBrush, SystemControlChromeMediumAcrylicElementMediumBrush <br/> SystemControlChromeMediumLowAcrylicWindowMediumBrush, SystemControlChromeMediumLowAcrylicElementMediumBrush <br/> SystemControlBaseHighAcrylicWindowMediumBrush, SystemControlBaseHighAcrylicElementMediumBrush <br/> SystemControlBaseMediumLowAcrylicWindowMediumBrush, SystemControlBaseMediumLowAcrylicElementMediumBrush <br/> SystemControlAltMediumLowAcrylicWindowMediumBrush, SystemControlAltMediumLowAcrylicElementMediumBrush  </td>
        <td align="center"> 60 % </td>
        <td> ChromeHigh <br/><br/> ChromeMedium <br/><br/> ChromeMediumLow <br/><br/> BaseHigh <br/><br/> BaseLow <br/><br/> AltMediumLow </td>
    </tr>
    <tr>
        <td> <b>Рекомендации по использованию:</b> Вы можете использовать в приложении эти ресурсы со значением 60 %, если поверх акриловой поверхности размещается только первичный текст цвета AltHigh. Мы рекомендуем рисовать <a href="../controls-and-patterns/navigationview.md">вертикальную панель навигации</a> ("кнопку-гамбургер") приложения, используя акрил со значением 60 %. </td>
    </tr>
</table>

Помимо акрила нейтрального цвета, мы также добавили ресурсы, которые дают пользователю возможность установить для акрила контрастный цвет. Однако использовать цветной акрил следует осмотрительно. Представленные варианты dark1 и dark2 подойдут для размещения поверх этих ресурсов белого или светлого текста, сочетающегося с цветом текста темной темы.
<table>
    <tr>
        <th align="center">Ключ ресурса</th>
        <th align="center">Непрозрачность оттенка</th>
        <th align="center"><a href="color.md">Оттенок и резервный цвет</a> </th>
    </tr>
    <tr>
        <td> SystemControlAccentAcrylicWindowAccentMediumHighBrush, SystemControlAccentAcrylicElementAccentMediumHighBrush  </td>
        <td align="center"> 70 % </td>
        <td> SystemAccentColor </td>
    </tr>
    <tr>
        <td> SystemControlAccentDark1AcrylicWindowAccentDark1Brush, SystemControlAccentDark1AcrylicElementAccentDark1Brush  </td>
        <td align="center"> 80% </td>
        <td> SystemAccentColorDark1 </td>
    </tr>
    <tr>
        <td> SystemControlAccentDark2AcrylicWindowAccentDark2MediumHighBrush, SystemControlAccentDark2AcrylicElementAccentDark2MediumHighBrush  </td>
        <td align="center"> 70 % </td>
        <td> SystemAccentColorDark2 </td>
    </tr>
</table>


Чтобы закрасить определенную поверхность, примените один из вышеупомянутых ресурсов темы к фону элемента так же, как любой другой ресурс кисти.

```xaml
<Grid Background="{ThemeResource SystemControlAcrylicElementBrush}">
```

## <a name="custom-acrylic-brush"></a>Настраиваемая акриловая кисть
Для акриловой поверхности в вашем приложении можно задать оттенок, чтобы придать ей фирменный стиль или добиться гармонии с другими элементами на странице. Чтобы перейти от черно-белой шкалы к цветной, необходимо определить собственные акриловые кисти с помощью указанных ниже свойств.
 - **TintColor** — слой наложения цвета/оттенка. Можно задать RGB-значение цвета и прозрачность альфа-канала.
 - **TintOpacity** — непрозрачность слоя оттенка. Рекомендуем начинать со значения 80 % для непрозрачности, хотя разные цвета могут выглядеть привлекательнее при других значениях.
 - **TintLuminosityOpacity** — управляет уровнем насыщенности, который пропускается через акриловую поверхность от фона.
 - **BackgroundSource** — флаг, указывающий тип акриловой поверхности (акрил фона или приложения).
 - **FallbackColor** — сплошной цвет, который заменяет акрил в режиме экономии заряда. Акрил фона также сменяется резервным цветом, если окно приложения на рабочем столе неактивно или приложение запущено на телефоне или Xbox.

![Акриловая палитра (светлая тема)](images/CustomAcrylic_Swatches_LightTheme.png)

![Акриловая палитра (темная тема)](images/CustomAcrylic_Swatches_DarkTheme.png)

![Сравнение непрозрачности оттенка с непрозрачностью оттенка](images/LuminosityVersusTint.png)

Чтобы добавить акриловую кисть, определите три ресурса для темной, светлой и высококонтрастной тем. Обратите внимание, что в режиме высокой контрастности мы рекомендуем использовать SolidColorBrush с тем же значением x:Key, что и для светлой/темной AcrylicBrush.

> [!Note] 
> Если значение TintLuminosityOpacity не указано, система автоматически подберет значение на основе TintColor и TintOpacity.

```xaml
<ResourceDictionary.ThemeDictionaries>
    <ResourceDictionary x:Key="Default">
        <AcrylicBrush x:Key="MyAcrylicBrush"
            BackgroundSource="HostBackdrop"
            TintColor="#FFFF0000"
            TintOpacity="0.8"
            TintLuminosityOpacity="0.5"
            FallbackColor="#FF7F0000"/>
    </ResourceDictionary>

    <ResourceDictionary x:Key="HighContrast">
        <SolidColorBrush x:Key="MyAcrylicBrush"
            Color="{ThemeResource SystemColorWindowColor}"/>
    </ResourceDictionary>

    <ResourceDictionary x:Key="Light">
        <AcrylicBrush x:Key="MyAcrylicBrush"
            BackgroundSource="HostBackdrop"
            TintColor="#FFFF0000"
            TintOpacity="0.8"
            TintLuminosityOpacity="0.5"
            FallbackColor="#FFFF7F7F"/>
    </ResourceDictionary>
</ResourceDictionary.ThemeDictionaries>
```

В следующем примере показано, как объявить AcrylicBrush в коде. Если приложение поддерживает несколько целевых операционных систем, убедитесь, что этот API доступен на компьютере пользователя.

```csharp
if (Windows.Foundation.Metadata.ApiInformation.IsTypePresent("Windows.UI.Xaml.Media.XamlCompositionBrushBase"))
{
    Windows.UI.Xaml.Media.AcrylicBrush myBrush = new Windows.UI.Xaml.Media.AcrylicBrush();
    myBrush.BackgroundSource = Windows.UI.Xaml.Media.AcrylicBackgroundSource.HostBackdrop;
    myBrush.TintColor = Color.FromArgb(255, 202, 24, 37);
    myBrush.FallbackColor = Color.FromArgb(255, 202, 24, 37);
    myBrush.TintOpacity = 0.6;

    grid.Fill = myBrush;
}
else
{
    SolidColorBrush myBrush = new SolidColorBrush(Color.FromArgb(255, 202, 24, 37));

    grid.Fill = myBrush;
}
```

## <a name="extend-acrylic-into-the-title-bar"></a>Применение акрила в заголовке окна

Чтобы окно вашего приложения выглядело максимально органично, можно использовать акриловую поверхность в области его заголовка. В этом примере акрил используется в заголовке окна путем задания определенным свойствам объекта [ApplicationViewTitleBar](https://docs.microsoft.com/uwp/api/Windows.UI.ViewManagement.ApplicationViewTitleBar) ([ButtonBackgroundColor](https://docs.microsoft.com/uwp/api/Windows.UI.ViewManagement.ApplicationViewTitleBar.ButtonBackgroundColor) и [ButtonInactiveBackgroundColor](https://docs.microsoft.com/uwp/api/Windows.UI.ViewManagement.ApplicationViewTitleBar.ButtonInactiveBackgroundColor)) значения [Colors.Transparent](https://docs.microsoft.com/uwp/api/Windows.UI.Colors.Transparent).

```csharp
private void ExtendAcrylicIntoTitleBar()
{
    CoreApplication.GetCurrentView().TitleBar.ExtendViewIntoTitleBar = true;
    ApplicationViewTitleBar titleBar = ApplicationView.GetForCurrentView().TitleBar;
    titleBar.ButtonBackgroundColor = Colors.Transparent;
    titleBar.ButtonInactiveBackgroundColor = Colors.Transparent;
}
```

Этот код можно разместить в методе [OnLaunched](https://docs.microsoft.com/uwp/api/windows.ui.xaml.application#Windows_UI_Xaml_Application_OnLaunched_Windows_ApplicationModel_Activation_LaunchActivatedEventArgs_) приложения (_App.xaml.cs_) после вызова [Window.Activate](https://docs.microsoft.com/uwp/api/windows.ui.xaml.window.Activate), как показано здесь, или на первой странице приложения.

```csharp
// Call your extend acrylic code in the OnLaunched event, after
// calling Window.Current.Activate.
protected override void OnLaunched(LaunchActivatedEventArgs e)
{
    Frame rootFrame = Window.Current.Content as Frame;

    // Do not repeat app initialization when the Window already has content,
    // just ensure that the window is active
    if (rootFrame == null)
    {
        // Create a Frame to act as the navigation context and navigate to the first page
        rootFrame = new Frame();

        rootFrame.NavigationFailed += OnNavigationFailed;

        if (e.PreviousExecutionState == ApplicationExecutionState.Terminated)
        {
            //TODO: Load state from previously suspended application
        }

        // Place the frame in the current Window
        Window.Current.Content = rootFrame;
    }

    if (e.PrelaunchActivated == false)
    {
        if (rootFrame.Content == null)
        {
            // When the navigation stack isn't restored navigate to the first page,
            // configuring the new page by passing required information as a navigation
            // parameter
            rootFrame.Navigate(typeof(MainPage), e.Arguments);
        }
        // Ensure the current window is active
        Window.Current.Activate();

        // Extend acrylic
        ExtendAcrylicIntoTitleBar();
    }
}
```

Кроме того, заголовок приложения (который обычно отображается в строке заголовка автоматически) придется отрисовать в TextBlock с помощью `CaptionTextBlockStyle`. Более подробную информацию см. в статье [Title bar customization](../shell/title-bar.md) (Настройка заголовка окна).

## <a name="dos-and-donts"></a>Возможности и ограничения
* Используйте акрил в качестве фонового материала для неосновных поверхностей приложения, например для панелей навигации.
* Расположите акриловую поверхность вдоль хотя бы одного края приложения. Оно будет выглядеть гармоничнее на окружающем фоне, слегка сливаясь с ним.
* Не помещайте акрил рабочего стола на больших фоновых поверхностях приложения — это нарушает логику использования акрила в первую очередь для временных поверхностей.
* Не размещайте акрил фона и акрил приложения в непосредственной близости, чтобы взгляд не "спотыкался" на стыках.
* Не размещайте рядом несколько акриловых панелей с одинаковым оттенком и прозрачностью — это приведет к нежелательной заметности стыка.
* Не размещайте на акриловых поверхностях текст контрастного цвета.

## <a name="how-we-designed-acrylic"></a>История создания акрила

Чтобы добиться уникальности акрила, мы тщательно проработали его основные компоненты. Мы начали с прозрачности, размытия и шума, чтобы добавить глубины и объема плоским поверхностям. Мы добавили слой с наложением путем исключения, чтобы обеспечить контрастность и разборчивость элементов пользовательского интерфейса, размещенных поверх акрила. Наконец, мы добавили оттенок цвета, предоставив возможность персонализации. В результате согласованного взаимодействия этих слоев и возник оригинальный полезный материал.

![Состав акрила](images/AcrylicRecipe_Diagram.jpg)
<br/>Состав акрила: фон, размытие, наложение путем исключения, цвет/оттенок, шум


## <a name="get-the-sample-code"></a>Получить пример кода

- [Пример из коллекции элементов управления XAML](https://github.com/Microsoft/Xaml-Controls-Gallery) — ознакомьтесь со всеми элементами управления XAML в интерактивном режиме.

## <a name="related-articles"></a>Связанные статьи

[**Эффект подсвечивания**](reveal.md)

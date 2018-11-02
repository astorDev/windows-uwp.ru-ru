---
author: mijacobs
description: Тип кисти, которая создает прозрачные текстуры.
title: Акриловый материал
template: detail.hbs
ms.author: mijacobs
ms.date: 08/9/2017
ms.topic: article
keywords: Windows10, uwp
pm-contact: yulikl
design-contact: rybick
dev-contact: jevansa
doc-status: Published
ms.localizationpriority: medium
ms.openlocfilehash: f9d56090e8fc1de83eeb4e8a68ca1830692c5b2f
ms.sourcegitcommit: 70ab58b88d248de2332096b20dbd6a4643d137a4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/02/2018
ms.locfileid: "5944608"
---
# <a name="acrylic-material"></a>Акриловый материал

![главное изображение](images/header-acrylic.svg)

Акрил — это тип [кисти](https://docs.microsoft.com/en-us/uwp/api/Windows.UI.Xaml.Media.Brush) , которая создает прозрачные текстуры. Акрил можно применять к поверхностям приложений, чтобы добавить эффект глубины и установить визуальную иерархию.  <!-- By allowing user-selected wallpaper or colors to shine through, acrylic keeps users in touch with the OS personalization they've chosen. -->

> **Важные API**: [класс AcrylicBrush](https://docs.microsoft.com/en-us/uwp/api/windows.ui.xaml.media.acrylicbrush), [свойство Background](https://docs.microsoft.com/en-us/uwp/api/windows.ui.xaml.controls.control.Background)

:::row:::
    :::column:::
        Acrylic in light theme
        ![Acrylic in light theme](images/Acrylic_LightTheme_Base.png)
    :::column-end:::
    :::column:::
        Acrylic in dark theme
        ![Acrylic in dark theme](images/Acrylic_DarkTheme_Base.png)
    :::column-end:::
:::row-end:::

## <a name="acrylic-and-the-fluent-design-system"></a>Акрил и система проектирования Fluent Design

 Система Fluent Design позволяет создавать современные и эффективные пользовательские интерфейсы, которые отличаются яркостью, глубиной, движением, материальностью и масштабированием. Акрил— это компонент системы проектирования Fluent Design, добавляющий физическую текстуру (материал) и глубину в приложение. Дополнительные сведения см. в разделе [Обзор системы проектирования Fluent для UWP](../fluent-design-system/index.md).

 ## <a name="video-summary"></a>Видео с кратким описанием

> [!VIDEO https://channel9.msdn.com/Events/Windows/Windows-Developer-Day-Fall-Creators-Update/WinDev002/player]

## <a name="examples"></a>Примеры:

:::row:::
    :::column span:::
        ![Some image](images/XAML-controls-gallery-app-icon.png)
    :::column-end:::
    :::column span="2":::
        **XAML Controls Gallery**<br>
        If you have the XAML Controls Gallery app installed, click <a href="xamlcontrolsgallery:/item/Acrylic">here</a> to open the app and see acrylic in action.

        <a href="https://www.microsoft.com/store/productId/9MSVH128X2ZT">Get the XAML Controls Gallery app (Microsoft Store)</a><br>
        <a href="https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/XamlUIBasics">Get the source code (GitHub)</a>
    :::column-end:::
:::row-end:::

## <a name="acrylic-blend-types"></a>Типы акрилового материала
Наиболее заметная характеристика акрила— его прозрачность. Для акриловых поверхностей возможны два типа, определяющих, какое изображение проступает через этот материал.
 - Сквозь **акрил фона** просвечивает фон рабочего стола или другие окна, расположенные за активным приложением, что создает ощущение глубины, пространства между окнами и в то же время учитывает предпочтения пользователя в персонализации.
 - **Акрил приложения** создает ощущение глубины в структуре самого приложения, обеспечивая акцентирование внимания и иерархию.

 ![Акрил фона](images/BackgroundAcrylic_DarkTheme.png)

 ![Акрил приложения](images/AppAcrylic_DarkTheme.png)

 Несколько акриловых поверхностей с осторожностью: несколько уровней акрил фона можно создать отвлекающие оптического illusions.

## <a name="when-to-use-acrylic"></a>Когда использовать акрил

* Используйте акрил приложения для поддержки пользовательского интерфейса, например NavigationView или командные элементы в строке. 
* Используйте акрил фона для отображения промежуточных элементов пользовательского интерфейса, такие как контекстные меню, всплывающие элементы и dimsissable свет пользовательского интерфейса.<br />С помощью Акрила в промежуточный сценариев обеспечивает визуальную взаимосвязь с содержимым, вызвавшим этот промежуточный Интерфейс.

Если вы используете акрил приложения на поверхностях навигации, рассмотрите возможность распространить содержимое под акриловая панель для улучшения поток в вашем приложении. С помощью NavigationView делает это для вас автоматически. Тем не менее чтобы избежать эффекту "зебры", не пытается разместить несколько объектов акриловый края до края - это может создать нежелательных границе между двумя размытое поверхности. Акрил — инструмент, призванный сделать ваши проекты, но при неправильном использовании, может привести к визуальному шуму.

Рассмотрите следующие шаблоны использования выбрать оптимальный способ включения акрил в приложение.

### <a name="horizontal-navigation-or-commanding"></a>Навигация по горизонтали или команды

Если ваше приложение не поддерживает NavigationView и вы планируете добавить акриловую поверхность, мы рекомендуем использовать акрил относительно Полупрозрачный с прозрачностью 60% оттенка.
 - Когда панель открывается как наложение поверх другого содержимого приложения— это [60% акрил приложения](#acrylic-theme-resources)
 - Когда панель открывается рядом с основным содержимым приложения— это [60% акрил фона](#acrylic-theme-resources)

![Приложение "карты", с помощью горизонтальной командный интерфейс приложения](images/Maps_In_App_Acrylic_1.png)

Кроме того наличие содержимого расширение или прокрутки в разделе акрил в верхней даст ваше приложение более современные методы и эффективной работы.

### <a name="vertical-panes"></a>Вертикальными панелями

Вертикальными панелями или поверхностей, которые помогают раздел отключать содержимого вашего приложения мы рекомендуем использовать непрозрачный фон вместо акрила. Если вертикальными панелями открыть поверх содержимого, например **Collapsed** или **Минимальный** режимы NavigationView мы рекомендуем использовать акрил приложения в целях обеспечения контекста страницы, когда пользователь в этой области откройте.

### <a name="transient-surfaces"></a>Промежуточный поверхностей

Для приложений с помощью всплывающих элементов меню, всплывающих окон немодальным, или исчезающий панелей, рекомендуется использовать акрил фона.

![Шаблон почтового приложения, с помощью информационных всплывающий элемент](images/Mail_TransientContextMenu.png)

Многие из наших элементов управления будет использовать акрил по умолчанию. [MenuFlyouts](https://docs.microsoft.com/windows/uwp/design/controls-and-patterns/menus), [AutoSuggestBox](https://docs.microsoft.com/windows/uwp/design/controls-and-patterns/auto-suggest-box), [поле со списком](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.combobox) и похожие элементы управления с помощью всплывающих окон dimiss свет будет использовать промежуточный акрил при их вызове.

> [!Note]
> Отрисовка акриловых поверхностей интенсивно использует графический Процессор, что устройство энергопотребление и сокращает время работы от батареи. Эффекты акриловых поверхностей автоматически отключаются, когда устройство переходит в режим экономии заряда, и пользователи могут отключить эти эффекты для всех приложений, при желании.

## <a name="usability-and-adaptability"></a>Удобство использования и адаптируемость
Акрил автоматически адаптируется к устройству и контексту в широком диапазоне.

В режиме высокой контрастности вместо акриловой поверхности пользователи видят знакомый цвет фона по своему выбору. Кроме того как акрил фона и акрил приложения отображаются сплошным цветом:
 - Когда пользователь отключает прозрачность в настройках > Персонализация > цвета
 - При активации режима экономии заряда
 - приложение запускается на оборудовании с низким быстродействием.

Кроме того акрила фона заменит его translucency и текстуры сплошным цветом:
 - окно приложения на рабочем столе не активно;
 - приложение UWP запущено на телефоне, устройстве HoloLens, Xbox или в режиме планшета.

### <a name="legibility-considerations"></a>Разборчивость текста
Важно, чтобы любой текст, который ваше приложение представляет пользователям, [соответствовал требованиям к коэффициенту контрастности](../accessibility/accessible-text-requirements.md). Мы подобрали оптимальный состав, чтобы на акриловом фоне текст интенсивного черного, белого или даже средненасыщенного серого цвета соответствовал требованиям к коэффициенту контрастности. Ресурсы темы, предоставляемые платформой, по умолчанию задают контрастные цвета при 80% непрозрачности. При размещении на акриловой поверхности текста интенсивного цвета этот показатель можно уменьшить без ущерба для четкости и разборчивости текста. В темном режиме непрозрачность тона может составлять 70%, а в светлом режиме акрил соответствует нормам коэффициента контрастности при непрозрачности 50%.

Не рекомендуется размещать на акриловой поверхности текст контрастного цвета, так как такое сочетание, скорее всего, не будет соответствовать минимальным требованиям к коэффициенту контрастности при размере шрифта 15пикселей. Постарайтесь не размещать [гиперссылки](../controls-and-patterns/hyperlinks.md) поверх элементов акрила. Кроме того, если вы решите использовать настройки цвета или уровня прозрачности, отличные от предусмотренных по умолчанию в ресурсах темы, проверьте, как это скажется на разборчивости текста.

## <a name="acrylic-theme-resources"></a>Акрил: ресурсы темы
Вы можете легко применять акрил к поверхностям своих приложений с помощью нового элемента XAML AcrylicBrush или заранее определенных ресурсов темы. Прежде всего, решите, будете ли вы использовать акрил фона или акрил приложения. Обязательно изучите общие шаблоны приложений, описанные выше в этой статье, и сопутствующие рекомендации.

Мы создали подборку ресурсов темы как для акрила фона, так и для акрила приложения, которые учитывают тему приложения и при необходимости отображаются сплошным цветом. Ресурсы с именем *AcrylicWindow* относятся к акрилу фона, а с именем *AcrylicElement*— к акрилу приложения.

<table>
    <tr>
        <th align="center">Ключ ресурса</th>
        <th align="center">Непрозрачность тона</th>
        <th align="center"><a href="color.md">Резервный цвет</a> </th>
    </tr>
    <tr>
        <td> SystemControlAcrylicWindowBrush SystemControlAcrylicElementBrush <br/> SystemControlChromeLowAcrylicWindowBrush, SystemControlChromeLowAcrylicElementBrush <br/> SystemControlBaseHighAcrylicWindowBrush, SystemControlBaseHighAcrylicElementBrush <br/> SystemControlBaseLowAcrylicWindowBrush, SystemControlBaseLowAcrylicElementBrush <br/> SystemControlAltHighAcrylicWindowBrush, SystemControlAltHighAcrylicElementBrush <br/> SystemControlAltLowAcrylicWindowBrush, SystemControlAltLowAcrylicElementBrush </td>
        <td align="center"> 80% </td>
        <td> ChromeMedium <br/> ChromeLow <br/><br/> BaseHigh <br/><br/> BaseLow <br/><br/> AltHigh <br/><br/> AltLow </td>
    </tr>
    </tr>
        <td> <b>Рекомендации по использованию.</b> Эти ресурсы общего назначения имеют широкий спектр применения и прекрасно работают. Если ваше приложение содержит второстепенный текст цвета AltMedium с размером текста менее 18пикселей, разместите за этим текстом 80% акриловый ресурс, чтобы <a href="../accessibility/accessible-text-requirements.md">соответствовать требованиям к коэффициенту контрастности</a>. </td>
    </tr>
    <tr>
        <td> SystemControlAcrylicWindowMediumHighBrush, SystemControlAcrylicElementMediumHighBrush <br/> SystemControlBaseHighAcrylicWindowMediumHighBrush, SystemControlBaseHighAcrylicElementMediumHighBrush </td>
        <td align="center"> 70% </td>
        <td> ChromeMedium <br/><br/> BaseHigh </td>
    </tr>
    <tr>
        <td> <b>Рекомендации по использованию:</b> Если ваше приложение использует дополнительный текст цвета AltMedium с размером текста 18 или больше, вы можете разместить эти более прозрачные 70% акриловые ресурсы второстепенный текст. Рекомендуем использовать эти ресурсы в верхней горизонтальной навигационной и командной областях вашего приложения.  </td>
    </tr>
    <tr>
        <td> SystemControlChromeHighAcrylicWindowMediumBrush, SystemControlChromeHighAcrylicElementMediumBrush <br/> SystemControlChromeMediumAcrylicWindowMediumBrush, SystemControlChromeMediumAcrylicElementMediumBrush <br/> SystemControlChromeMediumLowAcrylicWindowMediumBrush, SystemControlChromeMediumLowAcrylicElementMediumBrush <br/> SystemControlBaseHighAcrylicWindowMediumBrush, SystemControlBaseHighAcrylicElementMediumBrush <br/> SystemControlBaseMediumLowAcrylicWindowMediumBrush, SystemControlBaseMediumLowAcrylicElementMediumBrush <br/> SystemControlAltMediumLowAcrylicWindowMediumBrush, SystemControlAltMediumLowAcrylicElementMediumBrush  </td>
        <td align="center"> 60% </td>
        <td> ChromeHigh <br/><br/> ChromeMedium <br/><br/> ChromeMediumLow <br/><br/> BaseHigh <br/><br/> BaseLow <br/><br/> AltMediumLow </td>
    </tr>
    <tr>
        <td> <b>Рекомендации по использованию.</b> Вы можете использовать в приложении эти ресурсы с показателем 60%, только размещая первичный текст цвета AltHigh поверх акриловой поверхности. Рекомендуем рисовать <a href="../controls-and-patterns/navigationview.md">вертикальную панель навигации</a> ("кнопку-гамбургер"), используя 60% акрил. </td>
    </tr>
</table>

Помимо акрила нейтрального цвета мы также добавили ресурсы, которые дают пользователю возможность установить для акрила контрастный цвет. Однако использовать цветной акрил следует осмотрительно. Представленные варианты dark1 и dark2 подойдут для размещения поверх этих ресурсов белого или светлого текста, сочетающегося с цветом текста темной темы.
<table>
    <tr>
        <th align="center">Ключ ресурса</th>
        <th align="center">Непрозрачность тона</th>
        <th align="center"><a href="color.md">Оттенок и резервный цвет</a> </th>
    </tr>
    <tr>
        <td> SystemControlAccentAcrylicWindowAccentMediumHighBrush, SystemControlAccentAcrylicElementAccentMediumHighBrush  </td>
        <td align="center"> 70% </td>
        <td> SystemAccentColor </td>
    </tr>
    <tr>
        <td> SystemControlAccentDark1AcrylicWindowAccentDark1Brush, SystemControlAccentDark1AcrylicElementAccentDark1Brush  </td>
        <td align="center"> 80% </td>
        <td> SystemAccentColorDark1 </td>
    </tr>
    <tr>
        <td> SystemControlAccentDark2AcrylicWindowAccentDark2MediumHighBrush, SystemControlAccentDark2AcrylicElementAccentDark2MediumHighBrush  </td>
        <td align="center"> 70% </td>
        <td> SystemAccentColorDark2 </td>
    </tr>
</table>


Чтобы нарисовать определенную поверхность, примените один из вышеупомянутых ресурсов темы к фону элемента так же, как любой другой ресурс кисти.

```xaml
<Grid Background="{ThemeResource SystemControlAcrylicElementBrush}">
```

## <a name="custom-acrylic-brush"></a>Настраиваемая акриловая кисть
Акриловую поверхность в вашем приложении можно подкрасить в какой-либо цвет, чтобы придать ей фирменный стиль или добиться гармонии с другими элементами на странице. Чтобы перейти от черно-белой шкалы к цветной, необходимо определить собственные акриловые кисти с помощью указанных ниже свойств.
 - **TintColor**— слой наложения цвета/оттенка. Можно задать RGB-значение цвета и прозрачность альфа-канала.
 - **TintOpacity**— непрозрачность цветного слоя. Корпорация Майкрософт рекомендует 80% непрозрачности как отправную точку, несмотря на то, что разные цвета могут выглядеть привлекательнее при других translucencies.
 - **BackgroundSource**— флаг, указывающий тип акриловой поверхности (акрил фона или приложения).
 - **FallbackColor**: сплошной цвет, заменяющий акрил в режиме экономии заряда. Акрил фона также сменяется резервным цветом, если окно приложения на рабочем столе неактивно или приложение запущено на телефоне или Xbox.

![Акриловая палитра (светлая тема)](images/CustomAcrylic_Swatches_LightTheme.png)

![Акриловая палитра (темная тема)](images/CustomAcrylic_Swatches_DarkTheme.png)

Чтобы добавить акриловую кисть, определите три ресурса для темной, светлой и высококонтрастной тем. Обратите внимание, что в режиме высокой контрастности рекомендуется использовать SolidColorBrush с тем же значением x:Key, что и для светлой/темной AcrylicBrush.

```xaml
<ResourceDictionary.ThemeDictionaries>
    <ResourceDictionary x:Key="Default">
        <AcrylicBrush x:Key="MyAcrylicBrush"
            BackgroundSource="HostBackdrop"
            TintColor="#FFFF0000"
            TintOpacity="0.8"
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
            FallbackColor="#FFFF7F7F"/>
    </ResourceDictionary>
</ResourceDictionary.ThemeDictionaries>
```

В следующем примере показано, как объявить AcrylicBrush в коде. Если ваше приложение поддерживает несколько целевых объектов операционной системы, убедитесь, что этот API доступен на компьютере пользователя.

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

Чтобы взгляд легче скользил по окну вашего приложения, можно использовать акриловую поверхность в области его заголовка. В этом примере акрил используется в заголовке окна путем задания свойствам [ButtonBackgroundColor](https://docs.microsoft.com/uwp/api/Windows.UI.ViewManagement.ApplicationViewTitleBar) и [ButtonInactiveBackgroundColor](https://docs.microsoft.com/uwp/api/Windows.UI.ViewManagement.ApplicationViewTitleBar.ButtonBackgroundColor) объекта [ApplicationViewTitleBar](https://docs.microsoft.com/uwp/api/Windows.UI.ViewManagement.ApplicationViewTitleBar.ButtonInactiveBackgroundColor) значения [Colors.Transparent](https://docs.microsoft.com/uwp/api/Windows.UI.Colors.Transparent). 

```csharp
/// Extend acrylic into the title bar. 
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

Кроме того, понадобится написать заголовок приложения (обычно отображающийся в строке заголовка автоматически) в TextBlock с помощью `CaptionTextBlockStyle`. Более подробную информацию см. в статье [Настройка заголовка окна](../shell/title-bar.md).

## <a name="dos-and-donts"></a>Рекомендации
* Используйте акрил в качестве фонового материала для неосновных поверхностей приложения, например для панелей навигации.
* Расположите акриловую поверхность вдоль хотя бы одного края приложения. Оно будет выглядеть гармоничнее на окружающем фоне, слегка сливаясь с ним.
* Не следует размещать рабочего стола arylic на поверхностях большого фонового приложения — это нарушает модели акрил используется в основном для промежуточный поверхностей.
* Не размещайте акрил фона и приложения в непосредственной близости, чтобы взгляд не "спотыкался" на стыках.
* Не размещайте рядом несколько акриловых панелей одинаковых оттенка и прозрачности— это приведет к появлению нежелательного стыка.
* Не размещайте на акриловых поверхностях текст контрастного цвета.

## <a name="how-we-designed-acrylic"></a>История создания акрила

Чтобы добиться уникальных свойств и внешнего вида акрила, мы тщательно проработали его основные компоненты. Мы начали с translucency, размытия и шума, чтобы добавить глубины и плоские поверхности. Мы добавили исключающий смешивание слой, чтобы обеспечить контрастность и разборчивость элементов пользовательского интерфейса, размещенных поверх акрила. Наконец, мы добавили оттенок цвета, предоставив возможность персонализации. В результате согласованного взаимодействия этих слоев и возник оригинальный полезный материал.

![Состав акрила](images/AcrylicRecipe_Diagram.jpg)
<br/>Состав акрила: фон, размытие, исключение смешивания, цвет/оттенок, шум


## <a name="get-the-sample-code"></a>Получить пример кода

- [Образец галереи элементов управления XAML](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/XamlUIBasics) — ознакомьтесь со всеми элементами управления XAML в интерактивном формате.

## <a name="related-articles"></a>Еще по теме

[**Эффект отображения**](reveal.md)
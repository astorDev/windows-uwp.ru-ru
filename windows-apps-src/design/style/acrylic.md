---
author: mijacobs
description: Тип кисти, которая создает прозрачные текстуры.
title: Акриловый материал
template: detail.hbs
ms.author: mijacobs
ms.date: 08/9/2017
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: Windows10, uwp
pm-contact: yulikl
design-contact: rybick
dev-contact: jevansa
doc-status: Published
ms.localizationpriority: medium
ms.openlocfilehash: 8589a450b53a5ea028f8af2cee2aef7dc0816b52
ms.sourcegitcommit: f5cf806a595969ecbb018c3f7eea86c7a34940f6
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/10/2018
ms.locfileid: "3822118"
---
# <a name="acrylic-material"></a>Акриловый материал

![главное изображение](images/header-acrylic.svg)

Акрил — это тип [кисти](https://docs.microsoft.com/en-us/uwp/api/Windows.UI.Xaml.Media.Brush) , которая создает прозрачные текстуры. Акрил можно применять к поверхностям приложений, чтобы добавить эффект глубины и установить визуальную иерархию.  <!-- By allowing user-selected wallpaper or colors to shine through, acrylic keeps users in touch with the OS personalization they've chosen. -->

> **Важные API**: [класс AcrylicBrush](https://docs.microsoft.com/en-us/uwp/api/windows.ui.xaml.media.acrylicbrush), [свойство Background](https://docs.microsoft.com/en-us/uwp/api/windows.ui.xaml.controls.control.Background)

:::row:::
    :::column:::
        Акриловый в светлой теме ![Акриловый в светлой теме](images/Acrylic_LightTheme_Base.png)
    :::column-end:::
    :::column:::
        Акриловый в темной теме ![Акриловый в темной теме](images/Acrylic_DarkTheme_Base.png)
    :::column-end:::
:::row-end:::

## <a name="acrylic-and-the-fluent-design-system"></a>Акрил и система проектирования Fluent Design

 Система Fluent Design позволяет создавать современные и эффективные пользовательские интерфейсы, которые отличаются яркостью, глубиной, движением, материальностью и масштабированием. Акрил— это компонент системы проектирования Fluent Design, добавляющий физическую текстуру (материал) и глубину в приложение. Дополнительные сведения см. в разделе [Обзор системы проектирования Fluent для UWP](../fluent-design-system/index.md).

 ## <a name="video-summary"></a>Видео с кратким описанием

> [!VIDEO https://channel9.msdn.com/Events/Windows/Windows-Developer-Day-Fall-Creators-Update/WinDev002/player]

## <a name="examples"></a>Примеры:

:::row:::
    ::: column span::: ![некоторые изображения](images/XAML-controls-gallery-app-icon.png)
    :::column-end:::
    ::: column span = «2»::: **Галереи элементов управления XAML**<br>
        Если у вас установлено приложение галереи элементов управления XAML, щелкните <a href="xamlcontrolsgallery:/item/Acrylic">здесь</a> , чтобы открыть приложение и увидеть акрил в действии.

        <a href="https://www.microsoft.com/store/productId/9MSVH128X2ZT">Get the XAML Controls Gallery app (Microsoft Store)</a><br>
        <a href="https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/XamlUIBasics">Get the source code (GitHub)</a>
    :::column-end:::
:::row-end:::

## <a name="when-to-use-acrylic"></a>Когда использовать акрил

На акриловых поверхностях рекомендуется размещать вспомогательные элементы пользовательского интерфейса, например элементы навигации в приложении или командные элементы. Этот материал также подойдет для отображения промежуточных элементов пользовательского интерфейса, таких как диалоговые окна и всплывающие элементы, поскольку он обеспечивает визуальную взаимосвязь с содержимым, вызвавшим этот промежуточный интерфейс. Акрил создан для использования в качестве фонового материала и отображения неброских панелей, поэтому не стоит применять его для детализированных элементов переднего плана.

Для поверхностей, на которых располагается основное содержимое приложения, лучше выбирать сплошной непрозрачный фон.

Возможно, вам понравится идея добавить акриловые поля по одному или нескольким краям экрана приложения, включая панель заголовка окна, чтобы усилить зрительное восприятие. Старайтесь не располагать рядом несколько блоков акриловых поверхностей разного типа— это может привести к эффекту "зебры". Акрил— инструмент, призванный сделать ваши проекты визуально гармоничными, однако при неправильном использовании может привести к визуальному шуму.

Ознакомьтесь с приведенными ниже шаблонами, чтобы выбрать оптимальный способ использования акрила в своем приложении.

### <a name="vertical-acrylic-pane"></a>Вертикальная акриловая панель

В приложениях с вертикальной панелью навигации рекомендуется применять акрил для дополнительной панели с элементами навигации.

![Шаблон приложения с одной вертикальной акриловой панелью](images/acrylic_app-pattern_vertical.png)

[NavigationView](../controls-and-patterns/navigationview.md)— это новый общий элемент управления для добавления навигации в приложении, который использует акрил для визуального оформления. Панель NavigationView отображается как акрил поверх фонового изображения, если эта панель открывается рядом с основным содержимым, и автоматически сменяется акрилом поверх приложения, если панель накладывается на основное содержимое.

Если ваше приложение не поддерживает NavigationView и вы планируете добавить акриловую поверхность, мы рекомендуем использовать акрил Полупрозрачный относительно с прозрачностью 60% оттенка.
 - Когда панель открывается как наложение поверх другого содержимого приложения— это [60% акрил приложения](#acrylic-theme-resources)
 - Когда панель открывается рядом с основным содержимым приложения— это [60% акрил фона](#acrylic-theme-resources)

### <a name="multiple-acrylic-panes"></a>Несколько акриловых панелей

В приложениях с тремя отдельными вертикальными панелями рекомендуется применять акрил для неосновного содержимого.
 - Для дополнительной панели, расположенной непосредственно рядом с основным содержимым, используйте [80% акрил фона](#acrylic-theme-resources).
 - Для дополнительной панели, расположенной дальше от основного содержимого, используйте [60% акрил фона](#acrylic-theme-resources).

![Шаблон приложения с двумя вертикальными акриловыми панелями](images/acrylic_app-pattern_double-vertical.png)

### <a name="horizontal-acrylic-pane"></a>Горизонтальная акриловая панель

В приложениях с расположенными горизонтально панелью навигации, командными или другими важными элементами вдоль верхней части окна приложения рекомендуем применять к этим визуальным элементам [70% акрил](#acrylic-theme-resources).

![Шаблон приложения с горизонтальной акриловой панелью](images/acrylic_app-pattern_horizontal.png)

В использующих холст приложениях с акцентом на непрерывное масштабируемое содержимое для верхней панели следует использовать акрил приложения, чтобы пользователи видели связь с этим содержимым. К приложениям, использующим холст, относятся карты и приложения для рисования.

В приложениях без единого непрерывного холста рекомендуем использовать акрил фона для связи со всем рабочим столом.

### <a name="acrylic-in-utility-apps"></a>Акрил в служебных приложениях

Виджеты (облегченные приложения) будут активнее использоваться в качестве служебных, если все окно приложения будет занято акриловым дизайном. Время взаимодействия пользователя с этими приложениями очень короткое, эти приложения редко занимают весь экран рабочего стола пользователя. Например, калькулятор и центр уведомлений.

![Служебное приложение "Калькулятор" с полностью акриловым фоном](images/acrylic_app-pattern_full.png)

> [!Note]
> Отрисовка акриловых поверхностей интенсивно GPU, что устройство энергопотребление и сокращает время работы от батареи. Эффекты акриловых поверхностей автоматически отключаются, когда режим экономии заряда, и пользователи могут отключить эти эффекты для всех приложений, при желании.


## <a name="acrylic-blend-types"></a>Типы акрилового материала
Наиболее заметная характеристика акрила — его translucency. Для акриловых поверхностей возможны два типа, определяющих, какое изображение проступает через этот материал.
 - Сквозь **акрил фона** просвечивает фон рабочего стола или другие окна, расположенные за активным приложением, что создает ощущение глубины, пространства между окнами и в то же время учитывает предпочтения пользователя в персонализации.
 - **Акрил приложения** создает ощущение глубины в структуре самого приложения, обеспечивая акцентирование внимания и иерархию.

 ![Акрил фона](images/BackgroundAcrylic_DarkTheme.png)

 ![Акрил приложения](images/AppAcrylic_DarkTheme.png)

 Соблюдайте осторожность, используя несколько акриловых поверхностей. Акрил фона, как следует из его названия, не следует располагать ближе к пользователю в z-порядке. Использование нескольких слоев акрила фона ведет к непредвиденным оптическим иллюзиям, поэтому его лучше избегать. Если вы все же решите использовать несколько слоев, стоит применить акрил приложения, выбрав меньшую насыщенность тона, чтобы визуально приблизить слои к пользователю.


## <a name="usability-and-adaptability"></a>Удобство использования и адаптируемость
Акрил автоматически адаптируется к устройству и контексту в широком диапазоне.

В режиме высокой контрастности вместо акриловой поверхности пользователи видят знакомый цвет фона по своему выбору. Кроме того как акрил фона и акрил приложения отображаются сплошным цветом:
 - Когда пользователь отключает прозрачность в настройках > Персонализация > цвета
 - При активации режима экономии заряда
 - приложение запускается на оборудовании с низким быстродействием.

Кроме того акрила фона замените его translucency и текстуры сплошной цвет:
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
        <td> <b>Рекомендации по использованию:</b> Если ваше приложение использует дополнительный текст цвета AltMedium с размером текста 18 или больше, вы можете разместить эти более прозрачные 70% ресурсы второстепенный текст. Рекомендуем использовать эти ресурсы в верхней горизонтальной навигационной и командной областях вашего приложения.  </td>
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
 - **TintOpacity**— непрозрачность цветного слоя. Мы рекомендуем 80% непрозрачности как отправную точку, хотя разные цвета могут выглядеть привлекательнее при других translucencies.
 - **BackgroundSource**— флаг, указывающий тип акриловой поверхности (акрил фона или приложения).
 - **FallbackColor**: сплошной цвет, заменяющий акрил в режим экономии заряда. Акрил фона также сменяется резервным цветом, если окно приложения на рабочем столе неактивно или приложение запущено на телефоне или Xbox.


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

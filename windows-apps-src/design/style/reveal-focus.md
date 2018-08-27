---
author: cphilippona
description: Показать, что выделена эффекта освещения, который выполняет анимацию границы фокус элементов, когда пользователь перемещает фокус клавиатуры или игровой им.
title: Показать фокус
template: detail.hbs
ms.author: mijacobs
ms.date: 03/1/2018
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: windows 10, uwp
pm-contact: chphilip
design-contact: ''
dev-contact: stevenki
ms.localizationpriority: medium
ms.openlocfilehash: 7b5fa84efbe20368be55a50ce20c8e6e5d1fe439
ms.sourcegitcommit: 753dfcd0f9fdfc963579dd0b217b445c4b110a18
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/27/2018
ms.locfileid: "2861693"
---
# <a name="reveal-focus"></a>Показать фокус

![главное изображение](images/header-reveal-focus.svg)

Показать, что выделена эффекта освещения для [10 фута среды взаимодействия с пользователем](/windows/uwp/design/devices/designing-for-tv), например один Xbox и телевизор экранов. Этот эффект анимирует границу элементов для фокусировки, например кнопок, когда пользователь наводит на них фокус с геймпада или клавиатуры. По умолчанию эффект выключен, но включить его очень легко. 

(Показать выделите эффект, влияющие на освещение, в котором интерактивные элементы см. [Показать выделите статье](/windows/uwp/design/style/reveal)).


> **Важные API-интерфейсы**: [Application.FocusVisualKind property](https://docs.microsoft.com/uwp/api/windows.ui.xaml.application.FocusVisualKind), [FocusVisualKind enum](https://docs.microsoft.com/uwp/api/windows.ui.xaml.focusvisualkind), [Control.UseSystemFocusVisuals property](/uwp/api/Windows.UI.Xaml.Controls.Control.UseSystemFocusVisuals)

## <a name="how-it-works"></a>Принцип работы
Показать внимание звонки фокус фокус элементы, добавив анимационной свечения вокруг границы элемента:

![Наглядное представление эффекта отображения](images/traveling-focus-fullscreen-light-rf.gif)

Это особенно удобно в сценариях фута 10, где пользователь может не иметь внимания полный на весь экран TV. 

## <a name="examples"></a>Примеры.

<table>
<th align="left">Галерея элементов управления XAML<th>
<tr>
<td><img src="images/xaml-controls-gallery-sm.png" alt="XAML controls gallery"></img></td>
<td>
    <p>При наличии установленного приложения <strong style="font-weight: semi-bold">Коллекция элементов управления XAML</strong> , щелкните здесь, чтобы <a href="xamlcontrolsgallery:/item/RevealFocus">открыть приложение и посмотрите, будут открывать фокус в действии</a>.</p>
    <ul>
    <li><a href="https://www.microsoft.com/store/productId/9MSVH128X2ZT">Скачать приложение галереи элементов управления XAML (Microsoft Store)</a></li>
    <li><a href="https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/XamlUIBasics">Получить исходный код (GitHub)</a></li>
    </ul>
</td>
</tr>
</table>

## <a name="how-to-use-it"></a>Использование

Отображает фокус отключен по умолчанию. Чтобы включить его, выполните следующие действия:
1. В конструкторе приложения вызовите свойство [AnalyticsInfo.VersionInfo.DeviceFamily](/uwp/api/windows.system.profile.analyticsversioninfo.DeviceFamily) и проверьте, является ли текущее семейство устройств `Windows.Xbox`.
2. Если семейство устройств `Windows.Xbox`, задайте для свойства [Application.FocusVisualKind](/uwp/api/windows.ui.xaml.application.FocusVisualKind) значение `FocusVisualKind.Reveal`. 

```csharp
    if(AnalyticsInfo.VersionInfo.DeviceFamily == "Windows.Xbox")
    {
        this.FocusVisualKind = FocusVisualKind.Reveal;
    }
```

После установки свойства **FocusVisualKind** система автоматически применяет влияние фокус Показать все элементы управления, свойство [UseSystemFocusVisuals](/uwp/api/Windows.UI.Xaml.Controls.Control.UseSystemFocusVisuals) имеет значение **True** (значение по умолчанию для большинства элементов управления). 

## <a name="why-isnt-reveal-focus-on-by-default"></a>Почему Показать фокуса на по умолчанию? 
Как вы видите, довольно легко включать Показать фокус, когда приложение обнаруживает, что она выполняется на Xbox. Так почему система просто не включит для вас эту функцию? Показать фокус увеличивается размер visual фокус, что может вызвать проблемы с макетом пользовательского интерфейса. В некоторых случаях может потребоваться настройка влияние Показать фокус для оптимизации для вашего приложения.

## <a name="customizing-reveal-focus"></a>Настройка Показать фокус

Влияние Показать фокус можно настроить путем изменения фокуса визуальные свойства для каждого элемента управления: [FocusVisualPrimaryThickness](https://docs.microsoft.com/uwp/api/windows.ui.xaml.frameworkelement.FocusVisualPrimaryThickness), [FocusVisualSecondaryThickness](https://docs.microsoft.com/uwp/api/windows.ui.xaml.frameworkelement.FocusVisualSecondaryThickness), [FocusVisualPrimaryBrush](https://docs.microsoft.com/uwp/api/windows.ui.xaml.frameworkelement.FocusVisualPrimaryBrush)и [ FocusVisualSecondaryBrush](https://docs.microsoft.com/uwp/api/windows.ui.xaml.frameworkelement.FocusVisualSecondaryBrush). Эти свойства позволяют настраивать цвет и толщину прямоугольника фокуса. (Это те же свойства, что используются для создания [визуальных элементов дополнительного выделения фокуса](https://docs.microsoft.com/windows/uwp/design/input/guidelines-for-visualfeedback#high-visibility-focus-visuals).) 

Однако перед началом customzing, будет полезно знать немного больше о компонентах, которые составляют Показать фокус.

Существует три части визуальные элементы будут открывать фокус по умолчанию: границы основной, дополнительный границы и Показать свечения. Основная рамка толщиной **2 пикселя** проходит вдоль *внешней* стороны дополнительной рамки. Дополнительная рамка толщиной **1пиксель** проходит вдоль *внутренней* стороны основной рамки. Показать фокус свечения толщина пропорционально толщины границы основного и запускается вокруг *за пределами* основной границы.

В дополнение к статических элементов визуальные элементы будут открывать фокус компонента анимационной light, pulsates находясь за тесты, а затем в направлении фокус при перемещении фокус.

![Показать слои фокус](images/reveal-breakdown.svg)

## <a name="customize-the-border-thickness"></a>Настройка толщины границы

Чтобы изменить толщину разных типов границ элемента управления, воспользуйтесь этими свойствами:

| Тип границы | Свойство |
| --- | --- |
| Основная, свечение   | [FocusVisualPrimaryThickness](https://docs.microsoft.com/uwp/api/windows.ui.xaml.frameworkelement.FocusVisualPrimaryThickness)<br/> (При изменении основной границы толщина свечения меняется пропорционально.)   |
| Дополнительная   | [FocusVisualSecondaryThickness](https://docs.microsoft.com/uwp/api/windows.ui.xaml.frameworkelement.FocusVisualSecondaryThickness)   |


В этом примере изменяется толщина границы визуального элемента фокуса кнопки:

```xaml
<Button FocusVisualPrimaryThickness="2" FocusVisualSecondaryThickness="1"/>
```

## <a name="customize-the-margin"></a>Настройка поля

Поле— это пространство между визуальными границами элемента управления и началом дополнительной рамки. По умолчанию поле начинается через 1 пиксель от границ элемента управления. Можно изменять расположение поля для каждого элемента управления путем изменения свойства [FocusVisualMargin](https://docs.microsoft.com/uwp/api/windows.ui.xaml.frameworkelement.FocusVisualMargin):

```xaml
<Button FocusVisualPrimaryThickness="2" FocusVisualSecondaryThickness="1" FocusVisualMargin="-3"/>
```

Поле с отрицательным значением будет отстоять дальше от центра элемента управления, а поле с положительным значением будет расположено ближе к центру элемента управления.

## <a name="customize-the-color"></a>Настройка цвета

Для изменения цвета фокус Показать visual, используйте свойства [FocusVisualPrimaryBrush](https://docs.microsoft.com/uwp/api/windows.ui.xaml.frameworkelement.FocusVisualPrimaryBrush) и [FocusVisualSecondaryBrush](https://docs.microsoft.com/en-us/uwp/api/windows.ui.xaml.frameworkelement.FocusVisualSecondaryBrush) .

| Свойство | Ресурс по умолчанию | Значение ресурса по умолчанию |
| ---- | ---- | --- | 
| [FocusVisualPrimaryBrush](https://docs.microsoft.com/uwp/api/windows.ui.xaml.frameworkelement.FocusVisualPrimaryBrush) | SystemControlRevealFocusVisualBrush  | SystemAccentColor |
| [FocusVisualSecondaryBrush](https://docs.microsoft.com/uwp/api/windows.ui.xaml.frameworkelement.FocusVisualSecondaryBrush)  | SystemControlFocusVisualSecondaryBrush  | SystemAltMediumColor |

(Свойство FocusPrimaryBrush использует по умолчанию ресурсы **SystemControlRevealFocusVisualBrush**, только если **FocusVisualKind** имеет значение **Reveal**. В противном случае используется **SystemControlFocusVisualPrimaryBrush**.)


Чтобы изменить цвет визуального элемента фокуса отдельного элемента управления, настройте свойства непосредственно в элементе управления. В этом примере переопределяются цвета визуального элемента фокуса для кнопки.

```xaml

<!-- Specifying a color directly -->
<Button
    FocusVisualPrimaryBrush="DarkRed"
    FocusVisualSecondaryBrush="Pink"/>

<!-- Using theme resources -->
<Button
    FocusVisualPrimaryBrush="{ThemeResource SystemBaseHighColor}"
    FocusVisualSecondaryBrush="{ThemeResource SystemAccentColor}"/>    
```

Чтобы изменить цвет всех визуальных элементов во всем приложении, переопределите ресурсы **SystemControlRevealFocusVisualBrush** и **SystemControlFocusVisualSecondaryBrush** собственным определением:

```xaml
<!-- App.xaml -->
<Application.Resources>

    <!-- Override Reveal Focus default resources. -->
    <SolidColorBrush x:Key="SystemControlRevealFocusVisualBrush" Color="{ThemeResource SystemBaseHighColor}"/>
    <SolidColorBrush x:Key="SystemControlFocusVisualSecondaryBrush" Color="{ThemeResource SystemAccentColor}"/>
</Application.Resources>
```

Дополнительные сведения об изменении цвета визуального элемента фокуса см. в разделе [Цветовая фирменная символика и настройка](https://docs.microsoft.com/windows/uwp/design/input/guidelines-for-visualfeedback#color-branding--customizing).


## <a name="show-just-the-glow"></a>Показывать только свечение

Если вы хотите использовать только сияние без основного или дополнительного визуального элемента фокуса, просто задайте для свойства [FocusVisualPrimaryBrush](https://docs.microsoft.com/uwp/api/windows.ui.xaml.frameworkelement.FocusVisualPrimaryBrush) элемента управления значение `Transparent`, а для свойства [FocusVisualSecondaryThickness](https://docs.microsoft.com/uwp/api/windows.ui.xaml.frameworkelement.FocusVisualSecondaryThickness)— значение `0`. В этом случае свечение будет адаптироваться к цвету фона элемента управления, обеспечивая визуальное отсутствие границ. Изменить толщину свечения можно с помощью свойства [FocusVisualPrimaryThickness](https://docs.microsoft.com/uwp/api/windows.ui.xaml.frameworkelement.FocusVisualPrimaryThickness).

```xaml

<!-- Show just the glow -->
<Button
    FocusVisualPrimaryBrush="Transparent"
    FocusVisualSecondaryThickness="0" />    
```


## <a name="use-your-own-focus-visuals"></a>Использование собственных визуальных элементов фокуса

Другой способ настройки Показать фокус — отказаться от визуальные элементы системному фокус путем рисования на основе состояний. Дополнительные сведения приводятся в статье [Пример визуальных элементов фокуса](http://go.microsoft.com/fwlink/p/?LinkID=619895).


## <a name="reveal-focus-and-the-fluent-design-system"></a>Показать фокус и система Fluent разработки

Показать, что фокус находится компонент Fluent системы разработки, который добавляет light для вашего приложения. Дополнительные сведения о системе проектирования Fluent Design и ее других компонентах см. в [обзоре системы проектирования Fluent Design для UWP](../fluent-design-system/index.md).

## <a name="related-articles"></a>Связанные статьи

- [Показать выделение](https://docs.microsoft.com/windows/uwp/design/style/reveal)
- [Проектирование для Xbox и телевизора](/windows/uwp/design/devices/designing-for-tv)
- [Взаимодействие с помощью геймпада и пульта дистанционного управления](https://docs.microsoft.com/windows/uwp/design/input/gamepad-and-remote-interactions)
- [Пример визуальных элементов фокуса](http://go.microsoft.com/fwlink/p/?LinkID=619895)
- [Эффекты композиции](https://msdn.microsoft.com/windows/uwp/graphics/composition-effects)
- [Наука системы: система проектирования Fluent Design и глубина](https://medium.com/microsoft-design/science-in-the-system-fluent-design-and-depth-fb6d0f23a53f)
- [Наука системы: система проектирования Fluent и свет](https://medium.com/microsoft-design/the-science-in-the-system-fluent-design-and-light-94a17e0b3a4f)

---
description: Показать, что фокус находится эффекта освещения, который анимирует границы элементов, способному получать фокус, когда пользователь перемещает фокус клавиатуры или игровой к ним.
title: Показать фокус
template: detail.hbs
ms.date: 03/01/2018
ms.topic: article
keywords: windows 10, uwp
pm-contact: chphilip
design-contact: ''
dev-contact: stevenki
ms.localizationpriority: medium
ms.openlocfilehash: 7bcceb8d44b6d92cab05a9c077531b3fe1b05c79
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57651759"
---
# <a name="reveal-focus"></a>Показать фокус

![главное изображение](images/header-reveal-focus.svg)

Показывать главным образом нацелено эффекта освещения для [опыт 10 foot](/windows/uwp/design/devices/designing-for-tv), такие как Xbox One и экраны телевизора. Этот эффект анимирует границу элементов для фокусировки, например кнопок, когда пользователь наводит на них фокус с геймпада или клавиатуры. По умолчанию эффект выключен, но включить его очень легко. 

(Показать выделите эффект, эффект освещения, который выделяет интерактивные элементы, см. в разделе [Показать выделите статье](/windows/uwp/design/style/reveal).)


> **Важные API-интерфейсы**: [Свойство Application.FocusVisualKind](https://docs.microsoft.com/uwp/api/windows.ui.xaml.application.FocusVisualKind), [перечисления FocusVisualKind](https://docs.microsoft.com/uwp/api/windows.ui.xaml.focusvisualkind), [Control.UseSystemFocusVisuals свойство](/uwp/api/Windows.UI.Xaml.Controls.Control.UseSystemFocusVisuals)

## <a name="how-it-works"></a>Принцип работы
Показать сосредоточить внимание на вызовы к элементам с фокусом, добавив анимированных свечение вокруг границы этого элемента:

![Наглядное представление эффекта отображения](images/traveling-focus-fullscreen-light-rf.gif)

Это особенно полезно в сценариях 10 foot, где пользователь может не быть Брельсфорд полный на весь экран Телевизора. 

## <a name="examples"></a>Примеры

<table>
<th align="left">Галерея элементов управления XAML<th>
<tr>
<td><img src="images/xaml-controls-gallery-sm.png" alt="XAML controls gallery"></img></td>
<td>
    <p>Если у вас есть <strong style="font-weight: semi-bold">коллекции элементов управления XAML</strong> приложения. Щелкните здесь, чтобы <a href="xamlcontrolsgallery:/item/RevealFocus">откройте приложение и увидеть Показать фокус в действии</a>.</p>
    <ul>
    <li><a href="https://www.microsoft.com/store/productId/9MSVH128X2ZT">Получение коллекции элементов управления XAML приложения (Microsoft Store)</a></li>
    <li><a href="https://github.com/Microsoft/Xaml-Controls-Gallery">Получить исходный код (GitHub)</a></li>
    </ul>
</td>
</tr>
</table>

## <a name="how-to-use-it"></a>Использование

Показать, что фокус по умолчанию отключены. Чтобы включить его, выполните следующие действия:
1. В конструкторе приложения вызовите свойство [AnalyticsInfo.VersionInfo.DeviceFamily](/uwp/api/windows.system.profile.analyticsversioninfo.DeviceFamily) и проверьте, является ли текущее семейство устройств `Windows.Xbox`.
2. Если семейство устройств `Windows.Xbox`, задайте для свойства [Application.FocusVisualKind](/uwp/api/windows.ui.xaml.application.FocusVisualKind) значение `FocusVisualKind.Reveal`. 

```csharp
    if(AnalyticsInfo.VersionInfo.DeviceFamily == "Windows.Xbox")
    {
        this.FocusVisualKind = FocusVisualKind.Reveal;
    }
```

После установки **FocusVisualKind** свойство, система автоматически применяет эффект фокусировки Показать все элементы управления, [UseSystemFocusVisuals](/uwp/api/Windows.UI.Xaml.Controls.Control.UseSystemFocusVisuals) свойству **True** (значение по умолчанию для большинства элементов управления). 

## <a name="why-isnt-reveal-focus-on-by-default"></a>Почему Показать фокус на по умолчанию? 
Как вы видите, это довольно легко включать Показать фокус, когда приложение обнаруживает, что он работает под управлением Xbox. Так почему система просто не включит для вас эту функцию? Так как отобразить фокус увеличивает размер фокус визуальный элемент, который может вызвать проблемы с макетом пользовательского интерфейса. В некоторых случаях необходимо настроить эффект Показать фокус, оптимизировать его для своего приложения.

## <a name="customizing-reveal-focus"></a>Настройка раскрывают фокус

Показать фокус эффект можно настроить, изменив фокус визуальных свойств для каждого элемента управления: [FocusVisualPrimaryThickness](https://docs.microsoft.com/uwp/api/windows.ui.xaml.frameworkelement.FocusVisualPrimaryThickness), [FocusVisualSecondaryThickness](https://docs.microsoft.com/uwp/api/windows.ui.xaml.frameworkelement.FocusVisualSecondaryThickness), [FocusVisualPrimaryBrush](https://docs.microsoft.com/uwp/api/windows.ui.xaml.frameworkelement.FocusVisualPrimaryBrush), и [FocusVisualSecondaryBrush](https://docs.microsoft.com/uwp/api/windows.ui.xaml.frameworkelement.FocusVisualSecondaryBrush). Эти свойства позволяют настраивать цвет и толщину прямоугольника фокуса. (Это те же свойства, что используются для создания [визуальных элементов дополнительного выделения фокуса](https://docs.microsoft.com/windows/uwp/design/input/guidelines-for-visualfeedback#high-visibility-focus-visuals).) 

Но прежде чем настройка, полезно немного узнать о компонентах, составляющих Показать фокус.

Визуальные элементы Показать фокуса по умолчанию, состоит из трех частей: границе основной, дополнительный границы и отображения гало. Основная рамка толщиной **2 пикселя** проходит вдоль *внешней* стороны дополнительной рамки. Дополнительная рамка толщиной **1 пиксель** проходит вдоль *внутренней* стороны основной рамки. Показать фокус свечения толщина пропорционально толщины границы для первичного и запускает *за пределами* первичного границы.

Помимо статических элементов визуальные элементы фокуса Показать компонентов анимированных свет, который pulsates на месте остатки и перемещается в направлении фокус при перемещении фокуса.

![Показать слои фокус](images/reveal-breakdown.svg)

## <a name="customize-the-border-thickness"></a>Настройка толщины границы

Чтобы изменить толщину разных типов границ элемента управления, воспользуйтесь этими свойствами:

| Тип границы | Свойство |
| --- | --- |
| Основная, свечение   | [FocusVisualPrimaryThickness](https://docs.microsoft.com/uwp/api/windows.ui.xaml.frameworkelement.FocusVisualPrimaryThickness)<br/> (При изменении основной границы толщина свечения меняется пропорционально.)   |
| Сервер   | [FocusVisualSecondaryThickness](https://docs.microsoft.com/uwp/api/windows.ui.xaml.frameworkelement.FocusVisualSecondaryThickness)   |


В этом примере изменяется толщина границы визуального элемента фокуса кнопки:

```xaml
<Button FocusVisualPrimaryThickness="2" FocusVisualSecondaryThickness="1"/>
```

## <a name="customize-the-margin"></a>Настройка поля

Поле — это пространство между визуальными границами элемента управления и началом дополнительной рамки. По умолчанию поле начинается через 1 пиксель от границ элемента управления. Можно изменять расположение поля для каждого элемента управления путем изменения свойства [FocusVisualMargin](https://docs.microsoft.com/uwp/api/windows.ui.xaml.frameworkelement.FocusVisualMargin):

```xaml
<Button FocusVisualPrimaryThickness="2" FocusVisualSecondaryThickness="1" FocusVisualMargin="-3"/>
```

Поле с отрицательным значением будет отстоять дальше от центра элемента управления, а поле с положительным значением будет расположено ближе к центру элемента управления.

## <a name="customize-the-color"></a>Настройка цвета

Чтобы изменить цвета визуального отображения фокуса, используйте [FocusVisualPrimaryBrush](https://docs.microsoft.com/uwp/api/windows.ui.xaml.frameworkelement.FocusVisualPrimaryBrush) и [FocusVisualSecondaryBrush](https://docs.microsoft.com/en-us/uwp/api/windows.ui.xaml.frameworkelement.FocusVisualSecondaryBrush) свойства.

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

Если вы хотите использовать только сияние без основного или дополнительного визуального элемента фокуса, просто задайте для свойства [FocusVisualPrimaryBrush](https://docs.microsoft.com/uwp/api/windows.ui.xaml.frameworkelement.FocusVisualPrimaryBrush) элемента управления значение `Transparent`, а для свойства [FocusVisualSecondaryThickness](https://docs.microsoft.com/uwp/api/windows.ui.xaml.frameworkelement.FocusVisualSecondaryThickness) — значение `0`. В этом случае свечение будет адаптироваться к цвету фона элемента управления, обеспечивая визуальное отсутствие границ. Изменить толщину свечения можно с помощью свойства [FocusVisualPrimaryThickness](https://docs.microsoft.com/uwp/api/windows.ui.xaml.frameworkelement.FocusVisualPrimaryThickness).

```xaml

<!-- Show just the glow -->
<Button
    FocusVisualPrimaryBrush="Transparent"
    FocusVisualSecondaryThickness="0" />    
```


## <a name="use-your-own-focus-visuals"></a>Использование собственных визуальных элементов фокуса

Другим способом настройки Показать фокус — отказаться от визуальные элементы фокуса, предоставляемых системой, нарисовав собственные, используя визуальные состояния. Дополнительные сведения приводятся в статье [Пример визуальных элементов фокуса](https://go.microsoft.com/fwlink/p/?LinkID=619895).


## <a name="reveal-focus-and-the-fluent-design-system"></a>Показать фокус и Fluent Design System

Показать, что фокус является компонентом Fluent Design System, который добавляет свет в приложение. Дополнительные сведения о системе проектирования Fluent Design и ее других компонентах см. в [обзоре системы проектирования Fluent Design для UWP](../fluent-design-system/index.md).

## <a name="related-articles"></a>Связанные статьи

- [Отобразить выделение](https://docs.microsoft.com/windows/uwp/design/style/reveal)
- [Проектирование для Xbox и телевизора](/windows/uwp/design/devices/designing-for-tv)
- [Взаимодействия с игровой и удаленного управления](https://docs.microsoft.com/windows/uwp/design/input/gamepad-and-remote-interactions)
- [Пример визуальных элементов фокуса](https://go.microsoft.com/fwlink/p/?LinkID=619895)
- [Эффекты композиции](https://msdn.microsoft.com/windows/uwp/graphics/composition-effects)
- [В системе обработки и анализа: Fluent Design and глубины](https://medium.com/microsoft-design/science-in-the-system-fluent-design-and-depth-fb6d0f23a53f)
- [В системе обработки и анализа: Fluent Design and Light](https://medium.com/microsoft-design/the-science-in-the-system-fluent-design-and-light-94a17e0b3a4f)

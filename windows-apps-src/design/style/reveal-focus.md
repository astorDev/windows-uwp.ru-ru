---
description: Обозначение фокуса — это световой эффект, анимирующий границу фокусируемого элемента, когда пользователь наводит на него фокус с геймпада или клавиатуры.
title: Обозначение фокуса
template: detail.hbs
ms.date: 03/01/2018
ms.topic: article
keywords: windows 10, uwp
pm-contact: chphilip
design-contact: ''
dev-contact: stevenki
ms.localizationpriority: medium
ms.openlocfilehash: 098c46499e65c34e3699b09e137ea94c40590ef7
ms.sourcegitcommit: 0dee502484df798a0595ac1fe7fb7d0f5a982821
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/08/2020
ms.locfileid: "82968010"
---
# <a name="reveal-focus"></a>Обозначение фокуса

![Главное изображение](images/header-reveal-focus.svg)

Обозначение фокуса — это световой эффект для [больших экранов](/windows/uwp/design/devices/designing-for-tv), как в случае телевизора, используемого в паре с Xbox One. Этот эффект анимирует границу элементов для фокусировки, например кнопок, когда пользователь наводит на них фокус с геймпада или клавиатуры. По умолчанию эффект выключен, но включить его не составляет труда. 

(Описание эффекта подсвечивания, светового эффекта, выделяющего интерактивные элементы, см. [в статье о нем](/windows/uwp/design/style/reveal).)


> **Важные API**: [Application.FocusVisualKind property](https://docs.microsoft.com/uwp/api/windows.ui.xaml.application.FocusVisualKind), [FocusVisualKind enum](https://docs.microsoft.com/uwp/api/windows.ui.xaml.focusvisualkind), [Control.UseSystemFocusVisuals property](/uwp/api/Windows.UI.Xaml.Controls.Control.UseSystemFocusVisuals).

## <a name="how-it-works"></a>Принцип работы
Обозначение фокуса привлекает внимание к элементам в фокусе, добавляя анимированное сияние вокруг границы элемента:

![Пример обозначения фокуса](images/traveling-focus-fullscreen-light-rf.gif)

Эта функция особо полезна на больших экранах, когда пользователь может не обращать внимание на экран в целом. 

## <a name="examples"></a>Примеры

<table>
<th align="left">XAML Controls Gallery<th>
<tr>
<td><img src="images/xaml-controls-gallery-sm.png" alt="XAML controls gallery"></img></td>
<td>
    <p>Если у вас установлено приложение <strong style="font-weight: semi-bold">галереи элементов управления XAML</strong>, щелкните здесь, чтобы <a href="xamlcontrolsgallery:/item/RevealFocus">открыть приложение и увидеть обозначение фокуса в действии</a>.</p>
    <ul>
    <li><a href="https://www.microsoft.com/store/productId/9MSVH128X2ZT">Получить приложение XAML Controls Gallery (Microsoft Store)</a></li>
    <li><a href="https://github.com/Microsoft/Xaml-Controls-Gallery">Получить исходный код (GitHub)</a></li>
    </ul>
</td>
</tr>
</table>

## <a name="how-to-use-it"></a>Реализация

Обозначение фокуса выключено по умолчанию. Чтобы включить его, сделайте следующее:
1. В конструкторе приложения вызовите свойство [AnalyticsInfo.VersionInfo.DeviceFamily](/uwp/api/windows.system.profile.analyticsversioninfo.DeviceFamily) и проверьте, является ли текущее семейство устройств `Windows.Xbox`.
2. Если семейство устройств является `Windows.Xbox`, задайте для свойства [Application.FocusVisualKind](/uwp/api/windows.ui.xaml.application.FocusVisualKind) значение `FocusVisualKind.Reveal`. 

```csharp
    if(AnalyticsInfo.VersionInfo.DeviceFamily == "Windows.Xbox")
    {
        this.FocusVisualKind = FocusVisualKind.Reveal;
    }
```

После того как вы зададите свойство **FocusVisualKind**, система автоматически применит эффект обозначения фокуса ко всем элементам управления, у которых свойство [UseSystemFocusVisuals](/uwp/api/Windows.UI.Xaml.Controls.Control.UseSystemFocusVisuals) имеет значение **True** (значение по умолчанию для большинства элементов управления). 

## <a name="why-isnt-reveal-focus-on-by-default"></a>Почему отображение фокуса по умолчанию отключено? 
Включить обозначение фокуса достаточно просто, когда приложение обнаруживает, что оно выполняется на Xbox. Так почему бы системе просто не включать эту функцию? Потому что обозначение фокуса увеличивает размер фокусируемого элемента, что может быть неудобно при работе с пользовательским интерфейсом. В некоторых случаях может потребоваться настроить обозначение фокуса, чтобы оптимизировать его для вашего приложения.

## <a name="customizing-reveal-focus"></a>Настройка обозначения фокуса

Настроить обозначение фокуса можно путем изменения визуальных свойств каждого элемента управления: [FocusVisualPrimaryThickness](https://docs.microsoft.com/uwp/api/windows.ui.xaml.frameworkelement.FocusVisualPrimaryThickness), [FocusVisualSecondaryThickness](https://docs.microsoft.com/uwp/api/windows.ui.xaml.frameworkelement.FocusVisualSecondaryThickness), [FocusVisualPrimaryBrush](https://docs.microsoft.com/uwp/api/windows.ui.xaml.frameworkelement.FocusVisualPrimaryBrush) и [FocusVisualSecondaryBrush](https://docs.microsoft.com/uwp/api/windows.ui.xaml.frameworkelement.FocusVisualSecondaryBrush). Эти свойства позволяют настраивать цвет и толщину границы фокуса. (Это те же свойства, что используются для создания [визуальных элементов дополнительного выделения фокуса](https://docs.microsoft.com/windows/uwp/design/input/guidelines-for-visualfeedback#high-visibility-focus-visuals).) 

Но перед началом пользовательской настройки было бы полезно узнать больше о компонентах, составляющих обозначение фокуса.

Визуальные элементы обозначения фокуса по умолчанию состоят из трех частей: основная граница, дополнительная граница и свечение обозначения. Основная рамка толщиной **2 пикселя** проходит вдоль *внешней* стороны дополнительной рамки. Дополнительная рамка толщиной **1 пиксель** проходит вдоль *внутренней* стороны основной рамки. Свечение обозначения фокуса имеет толщину, пропорциональную толщине основной границы и циркулирует с *внешней стороны* основной границы.

Помимо статических элементов, визуальные элементы обозначения фокуса содержат анимированный свет, который пульсирует, если фокус не двигается, и перемещается в направлении перемещения фокуса.

![Слои обозначения фокуса](images/reveal-breakdown.svg)

## <a name="customize-the-border-thickness"></a>Настройка толщины границы

Чтобы изменить толщину разных типов границ элемента управления, используйте такие свойства:

| Тип границы | Свойство |
| --- | --- |
| Основная, свечение   | [FocusVisualPrimaryThickness](https://docs.microsoft.com/uwp/api/windows.ui.xaml.frameworkelement.FocusVisualPrimaryThickness)<br/> (При изменении основной границы толщина свечения меняется пропорционально.)   |
| Сервер   | [FocusVisualSecondaryThickness](https://docs.microsoft.com/uwp/api/windows.ui.xaml.frameworkelement.FocusVisualSecondaryThickness)   |


В этом примере изменяется толщина границы визуального элемента фокуса кнопки:

```xaml
<Button FocusVisualPrimaryThickness="2" FocusVisualSecondaryThickness="1"/>
```

## <a name="customize-the-margin"></a>Настройка поля

Поле — это пространство между визуальными границами элемента управления и началом дополнительной рамки. По умолчанию ширина поля составляет 1 пиксель. Вы можете изменять ширину поля для каждого элемента управления путем изменения свойства [FocusVisualMargin](https://docs.microsoft.com/uwp/api/windows.ui.xaml.frameworkelement.FocusVisualMargin):

```xaml
<Button FocusVisualPrimaryThickness="2" FocusVisualSecondaryThickness="1" FocusVisualMargin="-3"/>
```

Поле с отрицательным значением будет отстоять дальше от центра элемента управления, а поле с положительным значением будет расположено ближе к центру элемента управления.

## <a name="customize-the-color"></a>Настройка цвета

Чтобы изменить цвет визуального элемента обозначения фокуса, используйте свойства [FocusVisualPrimaryBrush](https://docs.microsoft.com/uwp/api/windows.ui.xaml.frameworkelement.FocusVisualPrimaryBrush) и [FocusVisualSecondaryBrush](https://docs.microsoft.com/uwp/api/windows.ui.xaml.frameworkelement.FocusVisualSecondaryBrush).

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

Дополнительные сведения об изменении цвета визуального элемента фокуса см. в разделе о [настройках границ и их цветов](https://docs.microsoft.com/windows/uwp/design/input/guidelines-for-visualfeedback#color-branding--customizing).


## <a name="show-just-the-glow"></a>Показывать только свечение

Если вы хотите использовать только свечение без основного или дополнительного визуального элемента фокуса, просто задайте для свойства [FocusVisualPrimaryBrush](https://docs.microsoft.com/uwp/api/windows.ui.xaml.frameworkelement.FocusVisualPrimaryBrush) элемента управления значение `Transparent`, а для свойства [FocusVisualSecondaryThickness](https://docs.microsoft.com/uwp/api/windows.ui.xaml.frameworkelement.FocusVisualSecondaryThickness) — значение `0`. В этом случае свечение будет адаптироваться к цвету фона элемента управления, обеспечивая визуальное отсутствие границ. Изменить толщину свечения можно с помощью свойства [FocusVisualPrimaryThickness](https://docs.microsoft.com/uwp/api/windows.ui.xaml.frameworkelement.FocusVisualPrimaryThickness).

```xaml

<!-- Show just the glow -->
<Button
    FocusVisualPrimaryBrush="Transparent"
    FocusVisualSecondaryThickness="0" />    
```


## <a name="use-your-own-focus-visuals"></a>Использование собственных визуальных элементов фокуса

Еще один способ настройки обозначения фокуса  — отказаться от использования системных визуальных элементов фокуса, нарисовав собственные с помощью визуальных состояний. Дополнительные сведения можно почерпнуть из [примера визуальных элементов фокуса](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/XamlFocusVisuals).


## <a name="reveal-focus-and-the-fluent-design-system"></a>Обозначение фокуса и система проектирования Fluent Design

Обозначение фокуса — это компонент системы проектирования Fluent Design, добавляющий свечение в ваше приложение. См. сведения о [системе проектирования Fluent Design и ее компонентах](/windows/apps/fluent-design-system).

## <a name="related-articles"></a>Похожие статьи

- [Reveal Highlight](https://docs.microsoft.com/windows/uwp/design/style/reveal) (Подсвечивание)
- [Проектирование для Xbox и ТВ](/windows/uwp/design/devices/designing-for-tv)
- [Взаимодействие с помощью геймпада и пульта дистанционного управления](https://docs.microsoft.com/windows/uwp/design/input/gamepad-and-remote-interactions)
- [Пример визуальных элементов фокуса](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/XamlFocusVisuals)
- [Composition Effects](https://docs.microsoft.com/windows/uwp/graphics/composition-effects) (Эффекты композиции)
- [Science in the System: Fluent Design and Depth](https://medium.com/microsoft-design/science-in-the-system-fluent-design-and-depth-fb6d0f23a53f) (Наука системы: глубина в Fluent Design)
- [Science in the System: Fluent Design and Light](https://medium.com/microsoft-design/the-science-in-the-system-fluent-design-and-light-94a17e0b3a4f) (Свет в Fluent Design)

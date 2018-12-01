---
description: Фокус отображения — это световой эффект, анимирующий границу элементов для фокусировки, когда пользователь наводит на них фокус с геймпада или клавиатуры.
title: Фокус отображения
template: detail.hbs
ms.date: 03/1/2018
ms.topic: article
keywords: windows 10, uwp
pm-contact: chphilip
design-contact: ''
dev-contact: stevenki
ms.localizationpriority: medium
ms.openlocfilehash: 311e5714c5428fac6509564fd00784299a02f630
ms.sourcegitcommit: d2517e522cacc5240f7dffd5bc1eaa278e3f7768
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/01/2018
ms.locfileid: "8351182"
---
# <a name="reveal-focus"></a>Фокус отображения

![главное изображение](images/header-reveal-focus.svg)

Фокус отображения — это световой эффект для [взаимодействия на большом расстоянии](/windows/uwp/design/devices/designing-for-tv), такие как Xbox One и телевизоров. Этот эффект анимирует границу элементов для фокусировки, например кнопок, когда пользователь наводит на них фокус с геймпада или клавиатуры. По умолчанию эффект выключен, но включить его очень легко. 

(Эффект отображения выделите световой эффект, который подсвечивает интерактивные элементы, см. в [Отображение выделите статьи](/windows/uwp/design/style/reveal).)


> **Важные API-интерфейсы**: [Application.FocusVisualKind property](https://docs.microsoft.com/uwp/api/windows.ui.xaml.application.FocusVisualKind), [FocusVisualKind enum](https://docs.microsoft.com/uwp/api/windows.ui.xaml.focusvisualkind), [Control.UseSystemFocusVisuals property](/uwp/api/Windows.UI.Xaml.Controls.Control.UseSystemFocusVisuals)

## <a name="how-it-works"></a>Принцип работы
Фокус отображения привлекает внимание к элементам в фокусе, добавляя анимированное сияние вокруг границы элемента:

![Наглядное представление эффекта отображения](images/traveling-focus-fullscreen-light-rf.gif)

Это особенно удобно в сценариях большом расстоянии, где пользователь может не быть уделяет особого внимания всей площади экрана Телевизора. 

## <a name="examples"></a>Примеры.

<table>
<th align="left">Галерея элементов управления XAML<th>
<tr>
<td><img src="images/xaml-controls-gallery-sm.png" alt="XAML controls gallery"></img></td>
<td>
    <p>Если у вас установлено приложение <strong style="font-weight: semi-bold">Галереи элементов управления XAML</strong> , щелкните здесь, чтобы <a href="xamlcontrolsgallery:/item/RevealFocus">открыть приложение и увидеть фокус отображения в действии</a>.</p>
    <ul>
    <li><a href="https://www.microsoft.com/store/productId/9MSVH128X2ZT">Скачать приложение галереи элементов управления XAML (Microsoft Store)</a></li>
    <li><a href="https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/XamlUIBasics">Получить исходный код (GitHub)</a></li>
    </ul>
</td>
</tr>
</table>

## <a name="how-to-use-it"></a>Использование

Фокус отображения — параметр по умолчанию. Чтобы включить его, выполните следующие действия:
1. В конструкторе приложения вызовите свойство [AnalyticsInfo.VersionInfo.DeviceFamily](/uwp/api/windows.system.profile.analyticsversioninfo.DeviceFamily) и проверьте, является ли текущее семейство устройств `Windows.Xbox`.
2. Если семейство устройств `Windows.Xbox`, задайте для свойства [Application.FocusVisualKind](/uwp/api/windows.ui.xaml.application.FocusVisualKind) значение `FocusVisualKind.Reveal`. 

```csharp
    if(AnalyticsInfo.VersionInfo.DeviceFamily == "Windows.Xbox")
    {
        this.FocusVisualKind = FocusVisualKind.Reveal;
    }
```

После задания свойства **FocusVisualKind** система автоматически применит эффект фокуса отображения ко всем элементам управления, свойство которого [UseSystemFocusVisuals](/uwp/api/Windows.UI.Xaml.Controls.Control.UseSystemFocusVisuals) имеет значение **True** (значение по умолчанию для большинства элементов управления). 

## <a name="why-isnt-reveal-focus-on-by-default"></a>Почему фокус отображения по умолчанию? 
Как видно, достаточно легко включить фокус отображения, когда приложение обнаруживает, что выполняется на Xbox. Так почему система просто не включит для вас эту функцию? Так как фокус отображения увеличивает размер визуального элемента фокуса, чего могут возникнуть проблемы со структурой пользовательского интерфейса. В некоторых случаях необходимо настроить фокус отображения для оптимизации для вашего приложения.

## <a name="customizing-reveal-focus"></a>Настройка фокуса отображения

Эффект отображения фокуса можно настроить, изменив визуальные свойства фокуса для каждого элемента управления: [FocusVisualPrimaryThickness](https://docs.microsoft.com/uwp/api/windows.ui.xaml.frameworkelement.FocusVisualPrimaryThickness), [FocusVisualSecondaryThickness](https://docs.microsoft.com/uwp/api/windows.ui.xaml.frameworkelement.FocusVisualSecondaryThickness), [FocusVisualPrimaryBrush](https://docs.microsoft.com/uwp/api/windows.ui.xaml.frameworkelement.FocusVisualPrimaryBrush)и [ FocusVisualSecondaryBrush](https://docs.microsoft.com/uwp/api/windows.ui.xaml.frameworkelement.FocusVisualSecondaryBrush). Эти свойства позволяют настраивать цвет и толщину прямоугольника фокуса. (Это те же свойства, что используются для создания [визуальных элементов дополнительного выделения фокуса](https://docs.microsoft.com/windows/uwp/design/input/guidelines-for-visualfeedback#high-visibility-focus-visuals).) 

Но перед началом пользовательской настройки, полезно знать чуть больше о компонентах, составляющих фокус отображения.

Существует визуальные элементы фокуса отображения по умолчанию состоит из трех частей: основная рамка, дополнительная граница и свечение отображения. Основная рамка толщиной **2 пикселя** проходит вдоль *внешней* стороны дополнительной рамки. Дополнительная рамка толщиной **1пиксель** проходит вдоль *внутренней* стороны основной рамки. Свечение фокуса отображения имеет толщину, пропорциональную толщине основной границы и толщиной *за пределами* основной границы.

Помимо статических элементов визуальные элементы фокуса отображения компонентов анимированный свет, который пульсирует при двигается и перемещается в направлении фокуса при перемещении фокуса.

![Слои фокуса отображения](images/reveal-breakdown.svg)

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

Чтобы изменить цвет отображения визуального элемента фокуса, используйте свойства [FocusVisualPrimaryBrush](https://docs.microsoft.com/uwp/api/windows.ui.xaml.frameworkelement.FocusVisualPrimaryBrush) и [FocusVisualSecondaryBrush](https://docs.microsoft.com/en-us/uwp/api/windows.ui.xaml.frameworkelement.FocusVisualSecondaryBrush) .

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

Еще один способ настройки фокуса отображения — отказаться от использования системных визуальных элементов, нарисовав собственные с помощью визуальных состояний. Дополнительные сведения приводятся в статье [Пример визуальных элементов фокуса](http://go.microsoft.com/fwlink/p/?LinkID=619895).


## <a name="reveal-focus-and-the-fluent-design-system"></a>Фокус отображения и система проектирования Fluent

Фокус отображения — это компонент системы проектирования Fluent Design, добавляющий свет в ваше приложение. Дополнительные сведения о системе проектирования Fluent Design и ее других компонентах см. в [обзоре системы проектирования Fluent Design для UWP](../fluent-design-system/index.md).

## <a name="related-articles"></a>Связанные статьи

- [Эффект](https://docs.microsoft.com/windows/uwp/design/style/reveal)
- [Проектирование для Xbox и телевизора](/windows/uwp/design/devices/designing-for-tv)
- [Взаимодействие с помощью геймпада и пульта дистанционного управления](https://docs.microsoft.com/windows/uwp/design/input/gamepad-and-remote-interactions)
- [Пример визуальных элементов фокуса](http://go.microsoft.com/fwlink/p/?LinkID=619895)
- [Эффекты композиции](https://msdn.microsoft.com/windows/uwp/graphics/composition-effects)
- [Наука системы: система проектирования Fluent Design и глубина](https://medium.com/microsoft-design/science-in-the-system-fluent-design-and-depth-fb6d0f23a53f)
- [Наука системы: система проектирования Fluent и свет](https://medium.com/microsoft-design/the-science-in-the-system-fluent-design-and-light-94a17e0b3a4f)

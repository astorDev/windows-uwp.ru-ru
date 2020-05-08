---
Description: Используйте визуальную обратную связь для отображения пользователей при обнаружении, интерпретации и обработке взаимодействия с приложением Windows.
title: Визуальная обратная связь
ms.assetid: bf2f3672-95f0-4c8c-9a72-0934f2d3b767
label: Visual feedback
template: detail.hbs
keywords: визуальная обратная связь, обратная связь для фокуса, обратная связь для касания, визуализация контакта, ввод, взаимодействие
ms.date: 02/08/2017
ms.topic: article
ms.localizationpriority: medium
ms.openlocfilehash: fcb6945c488bc1b715c339fa39949ea62bdb2a12
ms.sourcegitcommit: 0dee502484df798a0595ac1fe7fb7d0f5a982821
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/08/2020
ms.locfileid: "82970079"
---
# <a name="guidelines-for-visual-feedback"></a>Руководство по визуальной обратной связи

Используйте визуальную обратную связь, чтобы показать пользователю, что его взаимодействие с приложением обнаружено, интерпретировано и обработано. Визуальная обратная связь поможет пользователю и вдохновит его на дальнейшие действия. Она указывает на успешное взаимодействие, что повышает у пользователя чувство уверенности. Кроме того, визуальная обратная связь транслирует состояние системы и снижает количество ошибок.

> **Важные API-интерфейсы**: [**Windows.Devices.Input**](https://docs.microsoft.com/uwp/api/Windows.Devices.Input), [**Windows.UI.Input**](https://docs.microsoft.com/uwp/api/Windows.UI.Input), [**Windows.UI.Core**](https://docs.microsoft.com/uwp/api/Windows.UI.Core)

## <a name="recommendations"></a>Рекомендации

- Попробуйте разрешить изменения шаблона элемента управления, только если они напрямую связаны с вашей стратегией, так как существенные изменения могут повлиять на производительность и доступность элемента управления и приложения. 
    - Дополнительные сведения о настройке свойств элемента управления, в том числе свойств визуального состояния, см. в разделе [Стили XAML](https://docs.microsoft.com/windows/uwp/design/controls-and-patterns/xaml-styles).
    - Дополнительные сведения об изменении шаблона элемента управления см. в разделе [Класс UserControl](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.usercontrol).
    - Вы можете создать собственный элемент управления на основе шаблона, если вам потребуется внести значительные изменения в шаблон элемента управления. Пример пользовательского элемента управления на базе шаблона см. в разделе [Пример пользовательского элемента управления для редактирования](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/CustomEditControl).
- Не используйте визуализацию касаний в случаях, когда они могут помешать использованию приложения. Подробнее см. в разделе [**ShowGestureFeedback**](https://docs.microsoft.com/uwp/api/windows.ui.input.gesturerecognizer.showgesturefeedback).
- Не используйте визуальную обратную связь, если в этом нет абсолютной необходимости. Не засоряйте пользовательский интерфейс визуальной обратной связью, если она не добавляет чего-то, чего больше нет нигде.
- Старайтесь не вносить серьезных изменений в расширения функциональности визуальной обратной связи встроенных жестов Windows, поскольку это может вызвать несогласованность при взаимодействии и ввести пользователей в замешательство.

## <a name="additional-usage-guidance"></a>Дополнительное рекомендации по использованию

Зрительные образы контакта особенно важны для сенсорного взаимодействия, в котором требуется точность. Например, ваше приложение должно четко показывать место касания, чтобы пользователь знал, попал ли он в цель, насколько он промахнулся и какую поправку следует внести.

Использование элементов управления платформы XAML, доступных по умолчанию, обеспечивает корректную работу приложения на всех устройствах и с любыми вариантами ввода. Если в вашем приложении есть настраиваемые взаимодействия, требующие особой обратной связи, необходимо убедиться, что обратная связь адекватна, поддерживает различные устройства ввода и не отвлекает пользователя от работы. Это может иметь особое значение в игровых приложениях или графических редакторах, где визуальная обратная связь может закрывать или даже блокировать критически важные элементы интерфейса.

> [!Important]
> Мы не рекомендуем менять реакцию на события взаимодействия встроенных жестов.

**Обратная связь на различных устройствах**

Визуальная обратная связь обычно зависит от устройства ввода (сенсорный экран, сенсорная панель, мышь, перо, клавиатура и пр.). Например, встроенная обратная связь для мыши обычно предусматривает перемещение и изменение курсора, тогда как для касания и пера требуются зрительные образы контакта, а при вводе и навигации с клавиатуры используются прямоугольники фокуса и выделение.

Обратная связь для жестов платформы настраивается в свойстве [**ShowGestureFeedback**](https://docs.microsoft.com/uwp/api/windows.ui.input.gesturerecognizer.showgesturefeedback).

Настраивая интерфейс обратной связи, убедитесь, что обеспечивается обратная связь, адекватно поддерживающая все режимы ввода.

Вот несколько примеров встроенных зрительных образов контакта в Windows.

| ![Обратная связь сенсорного ввода](images/TouchFeedback.png) | ![Обратная связь мыши](images/MouseFeedback.png) | ![Обратная связь пера](images/PenFeedback.png) | ![Обратная связь клавиатуры](images/KeyboardFeedback.png) |
| --- | --- | --- | --- |
| Зрительные образы сенсорного ввода | Зрительные образы для мыши и сенсорной панели | Зрительные образы для пера | Зрительные образы для клавиатуры |

## <a name="high-visibility-focus-visuals"></a>Визуальные элементы дополнительного выделения

Во всех приложениях для Windows используется дополнительное визуальное выделение активных элементов управления в приложении. Эти новые визуальные элементы выделения можно полностью настраивать и при необходимости отключать.

Для использования на **большом расстоянии**, которое типично для Xbox и телевизоров, Windows поддерживает **фокус отображения**, эффект освещения, анимирующий границы элементов, которые могут получить фокус, такие как кнопки, когда они получают фокус с помощью геймпада или клавиатуры. Дополнительные сведения см. в разделе [Проектирование для Xbox и телевизора](https://docs.microsoft.com/windows/uwp/design/devices/designing-for-tv#reveal-focus).

## <a name="color-branding--customizing"></a>Цветовая фирменная символика и настройка

### <a name="border-properties"></a>Свойства рамок

Визуальные элементы дополнительного выделения состоят из двух частей: основная рамка и дополнительная рамка. Основная рамка толщиной **2 пикселя** проходит вдоль *внешней* стороны дополнительной рамки. Дополнительная рамка толщиной **1 пиксель** проходит вдоль *внутренней* стороны основной рамки.
![Красные линии визуальных элементов дополнительного выделения](images/FocusRectRedlines.png)

Чтобы изменить толщину любой из рамок (основной или дополнительной), используйте параметры **FocusVisualPrimaryThickness** или **FocusVisualSecondaryThickness** соответственно:
```XAML
<Slider Width="200" FocusVisualPrimaryThickness="5" FocusVisualSecondaryThickness="2"/>
```
![Толщина полей визуальных элементов дополнительного выделения](images/FocusMargin.png)

Поле — это свойство типа [**Thickness**](https://docs.microsoft.com/dotnet/api/system.windows.thickness), поэтому ее можно настроить так, чтобы она отображалась только с определенных сторон элемента управления. См. ниже ![: толщина визуального поля фокуса с высокой видимостью снизу](images/FocusThicknessSide.png)

Поле — это пространство между визуальными границами элемента управления и началом *вспомогательной границы*визуальных элементов фокуса. Поле по умолчанию **1px** от границ элемента управления. Это поле можно изменить отдельно для каждого элемента управления, изменив свойство **фокусвисуалмаргин** :
```XAML
<Slider Width="200" FocusVisualMargin="-5"/>
```
![Различие полей визуальных элементов дополнительного выделения](images/FocusPlusMinusMargin.png)

*Поле с отрицательным значением будет отстоять дальше от центра элемента управления, а поле с положительным значением будет расположено ближе к центру элемента управления.*

Чтобы полностью отключить дополнительное визуальное выделение элемента управления, просто отключите параметр **UseSystemFocusVisuals**:
```XAML
<Slider Width="200" UseSystemFocusVisuals="False"/>
```

Толщина, поле и само наличие визуальных элементов дополнительного элемента по замыслу разработчика приложения определяется для каждого элемента управления отдельно.

### <a name="color-properties"></a>Свойства цвета

У визуальных элементов дополнительного выделения есть только два свойства цвета: цвет основной рамки и цвет дополнительной рамки. Цвета рамок визуальных элементов дополнительного выделения можно изменять для каждого элемента управления отдельно на уровне страницы и глобально на уровне всего приложения:

Чтобы настроить фирменное оформление визуальных элементов дополнительного выделения в пределах всего приложения, переопределите системные кисти:
```XAML
<SolidColorBrush x:Key="SystemControlFocusVisualPrimaryBrush" Color="DarkRed"/>
<SolidColorBrush x:Key="SystemControlFocusVisualSecondaryBrush" Color="Pink"/>
```
![Изменения цвета визуальных элементов дополнительного выделения](images/FocusRectColorChanges.png)

Чтобы изменить цвет для каждого элемента управления отдельно, измените свойства визуального элемента дополнительного выделения нужного элемента управления:
```XAML
<Slider Width="200" FocusVisualPrimaryBrush="DarkRed" FocusVisualSecondaryBrush="Pink"/>
```

## <a name="related-articles"></a>Похожие статьи

### <a name="for-designers"></a>Проектировщикам

- [Руководство по функции сдвига](guidelines-for-panning.md)

### <a name="for-developers"></a>Для разработчиков

- [Настраиваемые взаимодействия с пользователем](https://docs.microsoft.com/windows/uwp/design/layout/index)

### <a name="samples"></a>Примеры

- [Пример базового ввода](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/BasicInput)
- [Пример ввода с малой задержкой](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/LowLatencyInput)
- [Пример режима взаимодействия с пользователем](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/UserInteractionMode)
- [Пример визуальных элементов фокуса](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/XamlFocusVisuals)

### <a name="archive-samples"></a>Примеры архива

- [Ввод: пример событий пользовательского ввода на XAML](https://github.com/microsoftarchive/msdn-code-gallery-microsoft/tree/411c271e537727d737a53fa2cbe99eaecac00cc0/Official%20Windows%20Platform%20Sample/Input%20XAML%20user%20input%20events%20sample)
- [Ввод: пример возможностей устройства](https://github.com/microsoftarchive/msdn-code-gallery-microsoft/tree/411c271e537727d737a53fa2cbe99eaecac00cc0/Official%20Windows%20Platform%20Sample/Windows%208%20app%20samples/%5BC%23%5D-Windows%208%20app%20samples/C%23/Windows%208%20app%20samples/Input%20Device%20capabilities%20sample%20(Windows%208))
- [Ввод: пример тестирования сенсорного ввода](https://github.com/microsoftarchive/msdn-code-gallery-microsoft/tree/411c271e537727d737a53fa2cbe99eaecac00cc0/Official%20Windows%20Platform%20Sample/Windows%208%20desktop%20samples/%5BC%2B%2B%5D-Windows%208%20desktop%20samples/C%2B%2B/Windows%208%20desktop%20samples/Input%20Touch%20hit%20testing%20sample)
- [Пример прокрутки XAML, панорамирования и масштабирования](https://github.com/microsoftarchive/msdn-code-gallery-microsoft/tree/411c271e537727d737a53fa2cbe99eaecac00cc0/Official%20Windows%20Platform%20Sample/Universal%20Windows%20app%20samples/111487-Universal%20Windows%20app%20samples/XAML%20scrolling%2C%20panning%2C%20and%20zooming%20sample)
- [Ввод: пример упрощенного рукописного ввода](https://github.com/microsoftarchive/msdn-code-gallery-microsoft/tree/411c271e537727d737a53fa2cbe99eaecac00cc0/Official%20Windows%20Platform%20Sample/Input%20Simplified%20ink%20sample)
- [Ввод: пример жестов в Windows 8](https://docs.microsoft.com/samples/browse/?redirectedfrom=MSDN-samples)
- [Входные данные: пример манипуляций и жестов](https://github.com/microsoftarchive/msdn-code-gallery-microsoft/tree/411c271e537727d737a53fa2cbe99eaecac00cc0/Official%20Windows%20Platform%20Sample/Input%20Gestures%20and%20manipulations%20with%20GestureRecognizer)
- [Пример сенсорного ввода в DirectX](https://github.com/microsoftarchive/msdn-code-gallery-microsoft/tree/411c271e537727d737a53fa2cbe99eaecac00cc0/Official%20Windows%20Platform%20Sample/Windows%208%20app%20samples/%5BC%2B%2B%5D-Windows%208%20app%20samples/C%2B%2B/Windows%208%20app%20samples/DirectX%20touch%20input%20sample%20(Windows%208))
 

 

---
Description: В этом разделе описано использование геометрии контакта для цели касания и приводятся рекомендации для целей касания в приложениях среды выполнения Windows.
title: Целевая настройка
ms.assetid: 93ad2232-97f3-42f5-9e45-3fc2143ac4d2
label: Targeting
template: detail.hbs
ms.date: 03/18/2019
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: 13737e9aeb906e0430b2a18dd75ae1fce3a75956
ms.sourcegitcommit: 87fd0ec1e706a460832b67f936a3014f0877a88c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/12/2020
ms.locfileid: "83234587"
---
# <a name="guidelines-for-touch-targets"></a>Рекомендации для целевых объектов сенсорного ввода

Все интерактивные элементы пользовательского интерфейса в приложении Windows должны быть достаточно большими, чтобы пользователи могли точно получить доступ и использовать, независимо от типа устройства или метода ввода.

Поддержка сенсорного ввода (и относительно неточная природа области сенсорного контакта) требует дополнительной оптимизации с учетом целевого размера и структуры элементов управления, так как более крупный, более сложный набор входных данных, сообщаемый сенсорным дигитайзером, используется для определения предполагаемого (или наиболее вероятного) целевого объекта пользователя.

Все элементы управления UWP спроектированы с учетом размеров и макетов управляющих объектов по умолчанию, позволяющих создавать визуально сбалансированные и привлекательные приложения, которые удобны, просты в использовании и вдохновить уверенность.

В этом разделе описываются эти поведения по умолчанию, позволяющие разрабатывать приложения для максимально удобного использования с помощью элементов управления платформы и пользовательских элементов управления (если это требуется вашему приложению).

> **Важные API-интерфейсы**: [**Windows.UI.Core**](https://docs.microsoft.com/uwp/api/Windows.UI.Core), [**Windows.UI.Input**](https://docs.microsoft.com/uwp/api/Windows.UI.Input), [**Windows.UI.Xaml.Input**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Input)

## <a name="fluent-standard-sizing"></a>Размеры Fluent Standard

*Размеры Fluent Standard* были разработаны, чтобы обеспечить баланс между плотностью информации и удобством пользователя. Фактически все элементы на экране пропорциональны эталонному размеру 40x40 эффективных пикселей (epx), что позволяет выравнивать элементы пользовательского интерфейса по сетке и правильно их масштабировать на уровне системы.

> [!NOTE]
> Дополнительные сведения об эффективных пикселях и масштабировании см. в разделе [Введение в проектирование приложений Windows](../basics/design-and-ui-intro.md#effective-pixels-and-scaling).
>
> Дополнительные сведения о масштабировании уровня системы см. в разделе [Выравнивание, поле и заполнение](../layout/alignment-margin-padding.md).

## <a name="fluent-compact-sizing"></a>Размеры Fluent Compact

Приложения могут отображать более высокий уровень плотности информации благодаря увеличению *размера в свободном размере*. Сжатие по размеру обеспечивает выравнивание элементов пользовательского интерфейса для целевого объекта EPX размером 32x32, что позволяет элементам пользовательского интерфейса выровняться по более плотной сетке и масштабироваться соответствующим образом на основе масштабирования системного уровня.

### <a name="examples"></a>Примеры

Компактный размер можно применить на уровне страницы или сетки.

### <a name="page-level"></a>Уровень страницы

```xaml
<Page.Resources>
    <ResourceDictionary Source="ms-appx:///Microsoft.UI.Xaml/DensityStyles/Compact.xaml" />
</Page.Resources>
```

### <a name="grid-level"></a>Уровень сетки

```xaml
<Grid>
    <Grid.Resources>
        <ResourceDictionary Source="ms-appx:///Microsoft.UI.Xaml/DensityStyles/Compact.xaml" />
    </Grid.Resources>
</Grid>
```

## <a name="target-size"></a>целевой размер;

В общем случае задайте размер сенсорного объекта в 7,5 мм в квадрате (40x40 пикселей на дисплее 135 на уровне 1,0 x плато). Как правило, элементы управления UWP выводятся с помощью управляющей цели 7,5 мм (это может варьироваться в зависимости от конкретного элемента управления и общих шаблонов использования). Дополнительные сведения см. в разделе [размер и плотность элемента управления](../style/spacing.md) .

Эти рекомендации по размеру цели можно скорректировать для конкретного сценария. При этом нужно помнить о следующем:

- Частота касаний. Рассмотрите возможность многократного или частого нажатия целевых объектов, размер которых превышает минимальный размер.
- Ошибка, результат — целевые объекты, имеющие серьезные последствия при возникновении ошибки, должны иметь больше заполнения и помещать дальше от края области содержимого. Это особенно важно для целей, которых часто касаются.
- Расположение в области содержимого.
- Форм фактор и размер экрана.
- Уровень пальца.
- Сенсорные визуализации.

## <a name="related-articles"></a>Похожие статьи

- [Введение в проектирование приложений Windows](../basics/design-and-ui-intro.md)
- [Размер и плотность элемента управления](../style/spacing.md)
- [Выравнивание, поля и заполнение](../layout/alignment-margin-padding.md)

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
* [Входные данные: пример манипуляций и жестов](https://github.com/microsoftarchive/msdn-code-gallery-microsoft/tree/411c271e537727d737a53fa2cbe99eaecac00cc0/Official%20Windows%20Platform%20Sample/Input%20Gestures%20and%20manipulations%20with%20GestureRecognizer)
- [Пример сенсорного ввода в DirectX](https://github.com/microsoftarchive/msdn-code-gallery-microsoft/tree/411c271e537727d737a53fa2cbe99eaecac00cc0/Official%20Windows%20Platform%20Sample/Windows%208%20app%20samples/%5BC%2B%2B%5D-Windows%208%20app%20samples/C%2B%2B/Windows%208%20app%20samples/DirectX%20touch%20input%20sample%20(Windows%208))

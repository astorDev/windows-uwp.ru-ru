---
title: Анимация свойств XAML
description: Анимация элементов XAML с помощью композиции анимаций.
ms.date: 09/13/2018
ms.topic: article
keywords: windows 10, uwp
pm-contact: stmoy
design-contact: jeffarn
ms.localizationpriority: medium
ms.custom: RS5
ms.openlocfilehash: 183a5433553ff6fdfcb09f6960f6a642f2c8bc08
ms.sourcegitcommit: cc0ef75f314658b14376eb60ef8e5bb4d7726e04
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/08/2019
ms.locfileid: "65444147"
---
# <a name="animating-xaml-elements-with-composition-animations"></a>Анимация элементов XAML с помощью композиции анимаций

В этой статье рассматриваются новые свойства, которые позволяют анимировать UIElement XAML производительности композиции анимаций и простота свойства XAML.

До Windows 10, версия 1809, имеется 2 вариантами выбора для создания анимации в приложениях универсальной платформы Windows:

- Использование конструкций XAML, таких как [выполнена его раскадровка анимации](storyboarded-animations.md), или _* ThemeTransition_ и _* ThemeAnimation_ классы в [ Windows.UI.Xaml.Media.Animation](/uwp/api/windows.ui.xaml.media.animation) пространства имен.
- Используйте композиции анимаций, как описано в [использование на визуальном уровне с XAML](../../composition/using-the-visual-layer-with-xaml.md).

С помощью на визуальном уровне обеспечивает более высокую производительность, чем использование XAML конструкций. Однако применение [ElementCompositionPreview](/uwp/api/Windows.UI.Xaml.Hosting.ElementCompositionPreview) нужно получить элемент основного композиции [Visual](/uwp/api/windows.ui.composition.visual) объекта, а затем анимации визуализацию с помощью композиции анимаций, сложнее использовать.

Начиная с Windows 10, версия 1809, можно анимировать свойства UIElement, напрямую с помощью композиции анимаций, не открывая для получения базового композиции Visual.

> [!NOTE]
> Чтобы использовать эти свойства на UIElement, требуемой версии проекта универсальной платформы Windows необходимо 1809 или более поздней версии. Дополнительные сведения о настройке версия проекта см. в разделе [адаптивные приложения версии](../../debug-test-perf/version-adaptive-apps.md).

## <a name="examples"></a>Примеры

<table>
<th align="left">Галерея элементов управления XAML<th>
<tr>
<td><img src="images/xaml-controls-gallery-app-icon.png" alt="XAML controls gallery" width="168"></img></td>
<td>
    <p>Если у вас есть <strong style="font-weight: semi-bold">коллекции элементов управления XAML</strong> приложения. Щелкните здесь, чтобы <a href="xamlcontrolsgallery:/item/XamlCompInterop">откройте приложение и анимации взаимодействия в действии см. в разделе</a>.</p>
    <ul>
    <li><a href="https://www.microsoft.com/store/productId/9MSVH128X2ZT">Получить приложение XAML Controls Gallery (Microsoft Store)</a></li>
    <li><a href="https://github.com/Microsoft/Xaml-Controls-Gallery">Получить исходный код (GitHub)</a></li>
    </ul>
</td>
</tr>
</table>

## <a name="new-rendering-properties-replace-old-rendering-properties"></a>Новые свойства отрисовки Замените старый свойства рендеринга

В этой таблице показаны свойства, можно использовать для изменения представления объекта UIElement, которое также может быть анимировано с [CompositionAnimation](/uwp/api/windows.ui.composition.compositionanimation).

| Свойство | Тип | Описание |
| -- | -- | -- |
| [Непрозрачности](/uwp/api/windows.ui.xaml.uielement.opacity) | Double | Степень непрозрачности объекта |
| [Перевод](/uwp/api/windows.ui.xaml.uielement.translation) | Vector3 (Вектор 3) | Положение элемента X, Y и Z |
| [TransformMatrix](/uwp/api/windows.ui.xaml.uielement.transformmatrix) | Matrix4x4 | Матрица преобразования, применяемая к элементу |
| [Масштаб](/uwp/api/windows.ui.xaml.uielement.scale) | Vector3 (Вектор 3) | Масштабирование элемента, по центру CenterPoint |
| [Поворот](/uwp/api/windows.ui.xaml.uielement.rotation) | Плавающий | Поворот элемента вокруг RotationAxis и CenterPoint |
| [RotationAxis](/uwp/api/windows.ui.xaml.uielement.rotationaxis) | Vector3 (Вектор 3) | Ось вращения |
| [CenterPoint](/uwp/api/windows.ui.xaml.uielement.centerpoint) | Vector3 (Вектор 3) | Центральную точку масштабирования и поворота |

Значение свойства TransformMatrix объединяется со свойствами масштабирования, вращения и перемещения в следующем порядке:  TransformMatrix, масштабирования, поворота, перевод.

Эти свойства не влияют на макет элемента, поэтому изменение этих свойств не вызывает новый [мер](/uwp/api/windows.ui.xaml.uielement.measure)/[расположение](/uwp/api/windows.ui.xaml.uielement.arrange) передачи.

Эти свойства имеют одно и то же назначение и поведение, как свойства таким же именем, в состав [Visual](/uwp/api/windows.ui.composition.visual) класса (за исключением перевода не оказывал на).

### <a name="example-setting-the-scale-property"></a>Пример. Свойства шкалы

В этом примере показано, как задать свойство шкалы на кнопке.

```xaml
<Button Scale="2,2,1" Content="I am a large button" />
```

```csharp
var button = new Button();
button.Content = "I am a large button";
button.Scale = new Vector3(2.0f,2.0f,1.0f);
```

### <a name="mutual-exclusivity-between-new-and-old-properties"></a>Взаимная Исключительность между старыми и новыми свойствами

> [!NOTE]
> **Непрозрачности** свойство не применяет принудительно взаимная Исключительность, описанные в этом разделе. Используйте одно и то же свойство непрозрачности, независимо от используемого метода XAML или композиции анимаций.

Свойства, которые могут быть анимированы с CompositionAnimation являются заменой несколько существующих свойств UIElement:

- [RenderTransform](/uwp/api/windows.ui.xaml.uielement.rendertransform)
- [RenderTransformOrigin](/uwp/api/windows.ui.xaml.uielement.rendertransformorigin)
- [Проекции](/uwp/api/windows.ui.xaml.uielement.projection)
- [Объект Transform3D](/uwp/api/windows.ui.xaml.uielement.transform3d)

При задании (или анимировать) каких-либо новых свойств, нельзя использовать старый свойства. И наоборот Если значение (или анимировать) каких-либо свойств старый, нельзя использовать новые свойства.

Также нельзя использовать новые свойства, если вы используете ElementCompositionPreview для получения и управлять визуальный самостоятельно с помощью этих методов:

- [ElementCompositionPreview.GetElementVisual](/uwp/api/windows.ui.xaml.hosting.elementcompositionpreview.getelementvisual)
- [ElementCompositionPreview.SetIsTranslationEnabled](/uwp/api/windows.ui.xaml.hosting.elementcompositionpreview.setistranslationenabled)

> [!IMPORTANT]
> Попытка одновременно использовать два набора свойств вызовет вызов API-интерфейса ошибкой и сообщения об ошибке.

Можно переключать из одного набора свойств, очистив их, то, что для простоты не рекомендуется. Если для свойства DependencyProperty (например, UIElement.Projection поддерживаемый UIElement.ProjectionProperty), затем вызовите ClearValue восстановить его в состояние «неиспользуемые». В противном случае (например, свойства масштаб), присвойте свойству значение по умолчанию.

## <a name="animating-uielement-properties-with-compositionanimation"></a>Анимация свойств UIElement с CompositionAnimation

Можно анимировать свойства отрисовки, перечисленные в таблице с CompositionAnimation. Эти свойства могут также ссылаться [ExpressionAnimation](/uwp/api/windows.ui.composition.expressionanimation).

Используйте [StartAnimation](/uwp/api/windows.ui.xaml.uielement.startanimation) и [StopAnimation](/uwp/api/windows.ui.xaml.uielement.stopanimation) методы UIElement для анимации свойств UIElement.

### <a name="example-animating-the-scale-property-with-a-vector3keyframeanimation"></a>Пример. Анимация свойства с Vector3KeyFrameAnimation масштаб

В этом примере демонстрируется анимация шкалы кнопки.

```csharp
var compositor = Window.Current.Compositor;
var animation = compositor.CreateVector3KeyFrameAnimation();

animation.InsertKeyFrame(1.0f, new Vector3(2.0f,2.0f,1.0f));
animation.Duration = TimeSpan.FromSeconds(1);
animation.Target = "Scale";

button.StartAnimation(animation);
```

### <a name="example-animating-the-scale-property-with-an-expressionanimation"></a>Пример. Анимация свойства с ExpressionAnimation масштаб

Страница содержит две кнопки. Вторая кнопка выполняет анимацию до быть дважды (с помощью масштабирования) как первая кнопка.

```xaml
<Button x:Name="sourceButton" Content="Source"/>
<Button x:Name="destinationButton" Content="Destination"/>
```

```csharp
var compositor = Window.Current.Compositor;
var animation = compositor.CreateExpressionAnimation("sourceButton.Scale*2");
animation.SetExpressionReferenceParameter("sourceButton", sourceButton);
animation.Target = "Scale";
destinationButton.StartAnimation(animation);
```

## <a name="related-topics"></a>См. также

- [Выполнена его раскадровка анимации](storyboarded-animations.md)
- [Использование на визуальном уровне с XAML](../../composition/using-the-visual-layer-with-xaml.md)
- [Общие сведения о преобразованиях](../layout/transforms.md)

---
author: jaster
ms.assetid: 
title: "Использование визуального уровня с помощью XAML"
description: "Узнайте методы использования интерфейсов API визуального уровня в сочетании с существующим содержимым XAML, чтобы создавать сложные анимации и эффекты."
translationtype: Human Translation
ms.sourcegitcommit: dfda33c70224f32d9c3e8877eabdfcd965521757
ms.openlocfilehash: 00d663b130202f4513cd1a9d82baed4068d909d3

---

# Использование визуального уровня с помощью XAML

## Введение

Большинство приложений, задействующих возможности визуального уровня, будут использовать XAML для определения содержимого основного пользовательского интерфейса. В юбилейном обновлении Windows 10 предусмотрены новые возможности платформы XAML и визуального уровня, которые упрощают объединение этих двух технологий для создания потрясающих элементов взаимодействия с пользователем.
Функциональность взаимодействия XAML и визуального уровня можно использовать для создания сложных анимаций и эффектов, недоступных при использовании только интерфейсов API XAML. Сюда входят следующие возможности.

-              Анимации и параллакс, управляемые прокруткой
-              Автоматические анимации макета
-              Точные до пикселя тени
-              Эффекты размытия и матированного стекла

Эти эффекты и анимации можно применять к существующему содержимому XAML, поэтому вам не придется кардинально менять структуру вашего приложения на XAML, чтобы использовать эти новые функции.
Анимации макета, тени и эффекты размытия описываются в разделе рецептов ниже. Пример кода с реализацией параллакса см. в разделе [Пример ParallaxingListItems](https://github.com/Microsoft/WindowsUIDevLabs/tree/master/SampleGallery/Samples/SDK%2010586/ParallaxingListItems). В [репозитории WindowsUIDevLabs](https://github.com/Microsoft/WindowsUIDevLabs) также есть ряд других примеров реализации анимаций, теней и эффектов.

## Класс **ElementCompositionPreview**

[**ElementCompositionPreview**](https://msdn.microsoft.com/en-us/library/windows/apps/windows.ui.xaml.hosting.elementcompositionpreview.aspx) — статический класс, обеспечивающий функции взаимодействия между XAML и визуальным уровнем. Обзор визуального уровня и его функций см. в разделе [Визуальный уровень](https://msdn.microsoft.com/en-us/windows/uwp/graphics/visual-layer). Класс **ElementCompositionPreview** предоставляет следующие методы.

-   [**GetElementVisual**](https://msdn.microsoft.com/en-us/library/windows/apps/windows.ui.xaml.hosting.elementcompositionpreview.getelementvisual.aspx): получение "выдаваемого" объекта Visual, используемого для отрисовки этого элемента
-   [**SetElementChildVisual**](https://msdn.microsoft.com/en-us/library/windows/apps/windows.ui.xaml.hosting.elementcompositionpreview.setelementchildvisual.aspx): определение "возвращаемого" объекта Visual как последнего дочернего элемента в визуальном дереве данного элемента. Этот объект Visual отрисовывается поверх остальных элементов. 
-   [**GetElementChildVisual**](https://msdn.microsoft.com/en-us/library/windows/apps/windows.ui.xaml.hosting.elementcompositionpreview.getelementvisual.aspx): получение набора объектов Visual с помощью **SetElementChildVisual**
-   [**GetScrollViewerManipulationPropertySet**](https://msdn.microsoft.com/en-us/library/windows/apps/windows.ui.xaml.hosting.elementcompositionpreview.getelementvisual.aspx): получение объекта, который может использоваться для создания анимаций 60 кадров в секунду на основании смещения прокрутки в **ScrollViewer**

## Примечания относительно **ElementCompositionPreview.GetElementVisual**

**ElementCompositionPreview.GetElementVisual** возвращает "выдаваемый" объект Visual, используемый для отрисовки данного **UIElement**. Такие свойства, как **Visual.Opacity**, **Visual.Offset** и **Visual.Size**, устанавливаются платформой XAML на основании состояния UIElement. Это позволяет использовать такие методы, как неявные анимации изменения положения (см. *Рецепты*).

Обратите внимание, что, поскольку свойства **Offset** и **Size** задаются в соответствии с макетом платформы на XAML, разработчикам следует осторожно подходить к изменению и анимации этих свойств. Разработчикам следует изменять и анимировать Offset, только когда верхний левый угол элемента совпадает с верхним левым углом его родительского элемента в макете. Размер обычно не следует менять, но доступ к этому свойству может быть полезен. Например, в приведенных ниже примерах с отбрасыванием тени и матированным стеклом свойство Size выданного объекта Visual используется в качестве входных данных для анимации.

Также следует помнить, что обновленные свойства выданного объекта Visual не будут отражаться в соответствующем UIElement. Так, например, установка для **UIElement.Opacity** значения 0,5 задаст прозрачности соответствующего выданного объекта Visual значение 0,5. Однако установка свойству **Opacity** выданного объекта Visual значения 0,5 приведет к тому, что содержимое будет отображаться с прозрачностью 50%, но это не изменит значение свойства Opacity соответствующего UIElement.

### Пример анимации **Offset**

#### Неверно

```xml
<Border>
      <Image x:Name="MyImage" Margin="5" />
</Border>
```

```csharp
// Doesn’t work because Image has a margin!
ElementCompositionPreview.GetElementVisual(MyImage).StartAnimation("Offset", parallaxAnimation);
```

#### Верно

```xml
<Border>
    <Canvas Margin="5">
        <Image x:Name="MyImage" />
    </Canvas>
</Border>
```

```csharp
// This works because the Canvas parent doesn’t generate a layout offset.
ElementCompositionPreview.GetElementVisual(MyImage).StartAnimation("Offset", parallaxAnimation);
```

## Метод **ElementCompositionPreview.SetElementChildVisual**

**ElementCompositionPreview.SetElementChildVisual** позволяет разработчику предоставить "возвращаемый" объект Visual, который будет отображаться как часть визуального дерева элемента. Это позволяет разработчикам создавать "Остров композиции", где содержимое на основе объекта Visual может размещаться в пользовательском интерфейсе на XAML. Разработчикам следует осторожно использовать этот подход, поскольку содержимое на основе объекта Visual не будет обладать теми же гарантиями по специальным возможностям и взаимодействию с пользователем, как содержимое на XAML. Соответственно, обычно рекомендуется использовать этот метод, только если это необходимо для реализации пользовательских эффектов, подобных приведенным ниже в разделе рецептов.

## 
              Методы **GetAlphaMask**


              Каждый из классов [**Image**](https://msdn.microsoft.com/library/windows/apps/br242752), [**TextBlock**](https://msdn.microsoft.com/library/windows/apps/br209652) и [**Shape**](https://msdn.microsoft.com/library/windows/apps/br243377) реализует метод под названием **GetAlphaMask**, возвращающий объект **CompositionBrush**, представляющий собой черно-белое изображение, по форме соответствующее данному элементу. Этот объект **CompositionBrush** может служить входными данными для **DropShadow** композиции, чтобы тень соответствовала форме элемента и не была просто прямоугольной. Это позволяет использовать точные до пикселя основанные на контурах тени для текста, изображений с альфа-каналом и различных фигур. См. раздел *Отбрасывание тени* ниже для ознакомления с примером этого API.

## Рецепты

### Анимация перемещения

Используя неявные анимации композиции, разработчик может автоматически анимировать изменения в макете элемента по отношению к его родительскому элементу. Например, если изменить свойство **Margin** приведенной ниже кнопки, она автоматически анимированным образом переместится на свое новое место в макете.

#### Обзор реализации

1.            Получение выдаваемого объекта **Visual** для целевого элемента
2.            Создание **ImplicitAnimationCollection**, что автоматически анимирует изменения в свойстве **Offset**
3.            Связывание **ImplicitAnimationCollection** с поддерживающим объектом Visual

#### XAML

```xml
<Button x:Name="RepositionTarget" Content="Click Me" />
```

#### C&#35;

```csharp
public MainPage()
{
    InitializeComponent();
    InitializeRepositionAnimation(RepositionTarget);
}

private void InitializeRepositionAnimation(UIElement repositionTarget)
{
    var targetVisual = ElementCompositionPreview.GetElementVisual(repositionTarget);
    Compositor compositor = targetVisual.Compositor;

    // Create an animation to animate targetVisual's Offset property to its final value
    var repositionAnimation = compositor.CreateVector3KeyFrameAnimation();
    repositionAnimation.Duration = TimeSpan.FromSeconds(0.66);
    repositionAnimation.Target = "Offset";
    repositionAnimation.InsertExpressionKeyFrame(1.0f, "this.FinalValue");

    // Run this animation when the Offset Property is changed
    var repositionAnimations = compositor.CreateImplicitAnimationCollection();
    repositionAnimations["Offset"] = repositionAnimation;

    targetVisual.ImplicitAnimations = repositionAnimations;
}
```

### Отбрасывание тени

Добавление точной до пикселя тени для **UIElement**, например **Ellipse** с картинкой. Поскольку тень требует создания **SpriteVisual** приложением, необходимо создать "принимающий" элемент, который будет содержать **SpriteVisual** с помощью **ElementCompositionPreview.SetElementChildVisual**.

#### Обзор реализации

1.            Получение выдаваемого объекта **Visual** для принимающего элемента
2.            Создание **DropShadow** из Windows.UI.Composition
3.            Настройка **DropShadow** для получения формы тени из целевого элемента через маску
    - **DropShadow** по умолчанию имеет прямоугольную форму, так что этот этап не требуется, если целевой объект прямоугольный
4.            Прикрепление тени к новому объекту **SpriteVisual** и назначение **SpriteVisual** дочерним элементом принимающего элемента
5.            Привязка размера **SpriteVisual** к размеру принимающего элемента с помощью **ExpressionAnimation**

#### XAML

```xml
<Grid Width="200" Height="200">
    <Canvas x:Name="ShadowHost" />
    <Ellipse x:Name="CircleImage">
        <Ellipse.Fill>
            <ImageBrush ImageSource="Assets/Images/2.jpg" Stretch="UniformToFill" />
        </Ellipse.Fill>
    </Ellipse>
</Grid>
```

#### C&#35;

```csharp
public MainPage()
{
    InitializeComponent();
    InitializeDropShadow(ShadowHost, CircleImage);
}

private void InitializeDropShadow(UIElement shadowHost, Shape shadowTarget)
{
    Visual hostVisual = ElementCompositionPreview.GetElementVisual(shadowHost);
    Compositor compositor = hostVisual.Compositor;

    // Create a drop shadow
    var dropShadow = compositor.CreateDropShadow();
    dropShadow.Color = Color.FromArgb(255, 75, 75, 80);
    dropShadow.BlurRadius = 15.0f;
    dropShadow.Offset = new Vector3(2.5f, 2.5f, 0.0f);
    // Associate the shape of the shadow with the shape of the target element
    dropShadow.Mask = shadowTarget.GetAlphaMask();

    // Create a Visual to hold the shadow
    var shadowVisual = compositor.CreateSpriteVisual();
    shadowVisual.Shadow = dropShadow;

    // Add the shadow as a child of the host in the visual tree
   ElementCompositionPreview.SetElementChildVisual(shadowHost, shadowVisual);

    // Make sure size of shadow host and shadow visual always stay in sync
    var bindSizeAnimation = compositor.CreateExpressionAnimation("hostVisual.Size");
    bindSizeAnimation.SetReferenceParameter("hostVisual", hostVisual);

    shadowVisual.StartAnimation("Size", bindSizeAnimation);
}
```

### Матированное стекло

Создание эффекта, размывающего и придающего оттенок расположенному за ним содержимому. Обратите внимание, что для использования эффектов разработчики должны установить пакет Win2D NuGet. Инструкции по установке см. на [Домашней странице Win2D](http://microsoft.github.io/Win2D/html/Introduction.htm).

#### Обзор реализации

1.            Получение выдаваемого объекта **Visual** для принимающего элемента
2.            Создание дерева эффекта размытия с помощью Win2D и **CompositionEffectSourceParameter**
3.            Создание **CompositionEffectBrush** на основании дерева эффекта
4.            Установка **CompositionBackdropBrush** в качестве входных данных для **CompositionEffectBrush**, что позволяет применить эффект к содержимому позади **SpriteVisual**.
5.            Установка **CompositionEffectBrush** в качестве содержимого нового объекта **SpriteVisual** и установка **SpriteVisual** в качестве дочернего элемента принимающего элемента
6.            Привязка размера **SpriteVisual** к размеру принимающего элемента с помощью **ExpressionAnimation**

#### XAML

```xml
<Grid Width="300" Height="300" Grid.Column="1">
    <Image
        Source="Assets/Images/2.jpg"
        Width="200"
        Height="200" />
    <Canvas
        x:Name="GlassHost"
        Width="150"
        Height="300"
        HorizontalAlignment="Right" />
</Grid>
```

#### C&#35;

```csharp
public MainPage()
{
    InitializeComponent();
    InitializeFrostedGlass(GlassHost);
}

private void InitializedFrostedGlass(UIElement glassHost)
{
    Visual hostVisual = ElementCompositionPreview.GetElementVisual(glassHost);
    Compositor compositor = hostVisual.Compositor;

    // Create a glass effect, requires Win2D NuGet package
    var glassEffect = new GaussianBlurEffect
    { 
        BlurAmount = 15.0f,
        BorderMode = EffectBorderMode.Hard,
        Source = new ArithmeticCompositeEffect
        {
            MultiplyAmount = 0,
            Source1Amount = 0.5f,
            Source2Amount = 0.5f,
            Source1 = new CompositionEffectSourceParameter("backdropBrush"),
            Source2 = new ColorSourceEffect
            {
                Color = Color.FromArgb(255, 245, 245, 245)
            }
        }
    };

    //  Create an instance of the effect and set its source to a CompositionBackdropBrush
    var effectFactory = compositor.CreateEffectFactory(glassEffect);
    var backdropBrush = compositor.CreateBackdropBrush();
    var effectBrush = effectFactory.CreateBrush();

    effectBrush.SetSourceParameter("backdropBrush", backdropBrush);

    // Create a Visual to contain the frosted glass effect
    var glassVisual = compositor.CreateSpriteVisual();
    glassVisual.Brush = effectBrush;

    // Add the blur as a child of the host in the visual tree
    ElementCompositionPreview.SetElementChildVisual(glassHost, glassVisual);

    // Make sure size of glass host and glass visual always stay in sync
    var bindSizeAnimation = compositor.CreateExpressionAnimation("hostVisual.Size");
    bindSizeAnimation.SetReferenceParameter("hostVisual", hostVisual);

    glassVisual.StartAnimation("Size", bindSizeAnimation);
}
```

## Дополнительные ресурсы:

-   [Обзор визуального уровня](https://msdn.microsoft.com/en-us/windows/uwp/graphics/visual-layer)
-   [
              Класс **ElementCompositionPreview**](https://msdn.microsoft.com/library/windows/apps/mt608976)
-   Расширенные примеры пользовательского интерфейса и композиции в [WindowsUIDevLabs GitHub](https://github.com/microsoft/windowsuidevlabs)
-   [Пример BasicXamlInterop](https://github.com/Microsoft/WindowsUIDevLabs/tree/master/SampleGallery/Samples/SDK%2010586/BasicXamlInterop)
-   [Пример ParallaxingListItems](https://github.com/Microsoft/WindowsUIDevLabs/tree/master/SampleGallery/Samples/SDK%2010586/ParallaxingListItems)



<!--HONumber=Aug16_HO3-->



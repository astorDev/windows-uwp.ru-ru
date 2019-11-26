---
ms.assetid: 6e9b9ff2-234b-6f63-0975-1afb2d86ba1a
title: Эффекты композиции
description: API эффектов позволяют разработчикам настраивать отрисовку пользовательского интерфейса.
ms.date: 02/08/2017
ms.topic: article
keywords: Windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: 57236b6780a7afe996fb1e68ac474d8d8077ca69
ms.sourcegitcommit: b52ddecccb9e68dbb71695af3078005a2eb78af1
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/20/2019
ms.locfileid: "74255903"
---
# <a name="composition-effects"></a>Эффекты композиции

API [**Windows.UI.Composition**](https://docs.microsoft.com/uwp/api/Windows.UI.Composition) позволяют применять эффекты в режиме реального времени к изображениям и пользовательскому интерфейсу, поддерживающему свойства анимации эффектов. В этом обзоре мы рассмотрим доступные функциональные возможности, позволяющие применять эффекты к визуальным элементам композиции.

Чтобы обеспечить совместимость с [универсальной платформой Windows (UWP)](https://docs.microsoft.com/windows/uwp/get-started/whats-a-uwp) для тех разработчиков, которые описывают эффекты в своих приложениях, эффекты композиции используют интерфейс IGraphicsEffect Win2D для описания эффектов с помощью пространства имен [Microsoft.Graphics.Canvas.Effects](https://microsoft.github.io/Win2D/html/N_Microsoft_Graphics_Canvas_Effects.htm).

Эффекты кисти используются для заливки областей приложения с помощью наложения эффектов на группу имеющихся изображений. API-интерфейсы эффектов композиции Windows 10 ориентированы на использование спрайтовых визуальных элементов. Объекты SpriteVisual обеспечивают гибкость и взаимодействие при создании цветов, изображений и эффектов. SpriteVisual — это тип визуальных элементов композиции, которые могут закрашивать двумерный прямоугольник с помощью кисти. Визуальный элемент определяет границы прямоугольника, а кисть определяет пиксели, используемые для его закрашивания.

Кисти эффектов используются с визуальными элементами дерева композиции, содержимое которых поступает от диаграммы эффекта. Эффекты могут ссылаться на имеющиеся поверхности или текстуры, но не данные, получаемые от других деревьев композиции.

Эффекты также можно применить к элементам пользовательского интерфейса XAML с помощью кисти эффекта с [**XamlCompositionBrushBase**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.media.xamlcompositionbrushbase).

## <a name="effect-features"></a>Функции эффекта

- [Библиотека эффектов](./composition-effects.md#effect-library)
- [Эффекты цепочки](./composition-effects.md#chaining-effects)
- [Поддержка анимации](./composition-effects.md#animation-support)
- [Свойства константы и анимированных эффектов](./composition-effects.md#constant-vs-animated-effect-properties)
- [Несколько экземпляров эффектов с независимыми свойствами](./composition-effects.md#multiple-effect-instances-with-independent-properties)

### <a name="effect-library"></a>Библиотека эффектов

В данный момент композиция поддерживает следующие эффекты.

| Результат               | Описание                                                                                                                                                                                                                |
|----------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Двумерное аффинное преобразование  | Применяет двумерную матрицу афинного преобразования к изображению. Мы использовали этот эффект, чтобы анимировать альфа-маску в наших [примерах](https://github.com/microsoft/WindowsCompositionSamples/tree/master/Demos/Reference Demos/BasicCompositonEffects) эффектов.       |
| Арифметическое композитное | Объединяет два изображения с помощью легко приспосабливаемого уравнения. Мы использовали арифметическое композитное для создания эффекта перехода в наших [примерах](https://github.com/microsoft/WindowsCompositionSamples/tree/master/Demos/Reference Demos/BasicCompositonEffects). |
| Эффект смешивания         | Создает эффект смешивания, который объединяет два изображения. Композиция позволяет использовать 21 из 26 [режимов смешивания](https://microsoft.github.io/Win2D/html/T_Microsoft_Graphics_Canvas_Effects_BlendEffectMode.htm), поддерживаемых Win2D.        |
| Источник цвета         | Создает изображение, закрашенное сплошным цветом.                                                                                                                                                                               |
| Объединение            | Объединяет два изображения. Композиция позволяет использовать все 13 [режимов объединения](https://microsoft.github.io/Win2D/html/T_Microsoft_Graphics_Canvas_CanvasComposite.htm), поддерживаемых Win2D.                                              |
| Контрастность             | Увеличивает или уменьшает контрастность изображения.                                                                                                                                                                           |
| Экспозиция             | Увеличивает или уменьшает экспозицию изображения.                                                                                                                                                                           |
| Оттенки серого            | Преобразовывает изображение с использованием монохромных серых оттенков.                                                                                                                                                                                   |
| Гамма-передача       | Изменяет цвета изображения, применяя к каждому каналу функцию гамма-передачи.                                                                                                                                           |
| Поворот цветового круга           | Изменяет цвет изображения, изменяя значения его оттенков на цветовом круге.                                                                                                                                                                   |
| Инверсия               | Инвертирует цвета изображения.                                                                                                                                                                                            |
| Изменение насыщенности             | Изменяет насыщенность изображения.                                                                                                                                                                                         |
| Сепия                | Преобразовывает изображение с использованием оттенков сепии.                                                                                                                                                                                          |
| Изменение температуры и оттенков | Изменяет температуру и (или) оттенки изображения.                                                                                                                                                                           |

Более подробные сведения см. в разделе, посвященном пространству имен [Microsoft.Graphics.Canvas.Effects](https://microsoft.github.io/Win2D/html/N_Microsoft_Graphics_Canvas_Effects.htm) Win2D. Эффекты, не поддерживаемые в композиции, отмечены как \[\]композиции.

### <a name="chaining-effects"></a>Создание цепочки эффектов

Эффекты можно объединять в цепочку, позволяя приложению применять к изображению сразу несколько эффектов. Диаграммы эффекта способны поддерживать несколько ссылающихся друг на друга эффектов. При описании эффекта просто добавьте его в качестве входного параметра.

```cs
IGraphicsEffect graphicsEffect =
new Microsoft.Graphics.Canvas.Effects.ArithmeticCompositeEffect
{
  Source1 = new CompositionEffectSourceParameter("source1"),
  Source2 = new SaturationEffect
  {
    Saturation = 0,
    Source = new CompositionEffectSourceParameter("source2")
  },
  MultiplyAmount = 0,
  Source1Amount = 0.5f,
  Source2Amount = 0.5f,
  Offset = 0
}
```

В примере выше описан эффект арифметического композитного, который имеет два набора входных данных. Второй набор входных данных включает эффект насыщенности со свойством насыщенности 0,5.

### <a name="animation-support"></a>Поддержка анимации

Свойства эффекта поддерживают анимацию. Во время компиляции эффекта можно указать, какие свойства эффекта будут анимированы, а какие — использованы в качестве постоянных. Анимируемые свойства можно указать с помощью строк формы (effect name.property name). Эти свойства можно анимировать независимо для нескольких экземпляров эффекта.

### <a name="constant-vs-animated-effect-properties"></a>Постоянные и анимированные свойства эффекта

Во время компиляции эффекта можно указать динамические свойства эффекта и свойства, которые будут использованы в качестве постоянных. Динамические свойства можно указать с помощью строк формы “<effect name>.<property name>”. Для динамических свойств можно задать конкретное значение, либо же их можно анимировать при помощи системы анимации композиции.

При компиляции эффекта, описанного выше, можно зафиксировать насыщенность, равную 0,5, или сделать ее динамической и устанавливать ее динамически или анимировать.

Компиляция эффекта с фиксированной насыщенностью:

```cs
var effectFactory = _compositor.CreateEffectFactory(graphicsEffect);
```

Компиляция эффекта с динамической насыщенностью:

```cs
var effectFactory = _compositor.CreateEffectFactory(graphicsEffect, new[]{"SaturationEffect.Saturation"});
_catEffect = effectFactory.CreateBrush();
_catEffect.SetSourceParameter("mySource", surfaceBrush);
_catEffect.Properties.InsertScalar("saturationEffect.Saturation", 0f);
```

Для свойства насыщенности эффекта выше можно установить статическое значение, либо же его можно анимировать при помощи анимаций Expression или ScalarKeyFrame.

Вы можете создать анимацию ScalarKeyFrame, которая будет использоваться для анимации свойства Saturation эффекта, следующим образом:

```cs
ScalarKeyFrameAnimation effectAnimation = _compositor.CreateScalarKeyFrameAnimation();
            effectAnimation.InsertKeyFrame(0f, 0f);
            effectAnimation.InsertKeyFrame(0.50f, 1f);
            effectAnimation.InsertKeyFrame(1.0f, 0f);
            effectAnimation.Duration = TimeSpan.FromMilliseconds(2500);
            effectAnimation.IterationBehavior = AnimationIterationBehavior.Forever;
```

Запустите анимацию для свойства Saturation эффекта следующим образом:

```cs
catEffect.Properties.StartAnimation("saturationEffect.Saturation", effectAnimation);
```

Сведения о свойствах эффекта, анимированных при помощи ключевых кадров, см. в примере [Уменьшение насыщенности — анимация](https://github.com/microsoft/WindowsCompositionSamples/tree/master/Demos/Reference Demos/BasicCompositonEffects/Desaturation - Animation), а сведения об использовании эффектов и выражений — в [примере альфа-маски](https://github.com/microsoft/WindowsCompositionSamples/tree/master/Demos/Reference Demos/BasicCompositonEffects/AlphaMask).

### <a name="multiple-effect-instances-with-independent-properties"></a>Несколько экземпляров эффектов с независимыми свойствами

Если во время компиляции указать, что параметр должен быть динамическим, его можно будет изменять для каждого экземпляра эффекта. Это позволит двум визуальным элементам использовать один и тот же эффект. При этом визуальные эффекты будут отрисовываться с использованием различных свойств эффекта. Для получения более подробных сведений см. [пример](https://github.com/microsoft/WindowsCompositionSamples/tree/master/Demos/Reference Demos/BasicCompositonEffects/ColorSource and Blend) источника цвета и смешивания.

## <a name="getting-started-with-composition-effects"></a>Начало работы с эффектами композиции

В данном руководстве по быстрому началу работы приводится информация об использовании некоторых основных возможностей эффектов.

- [Установка Visual Studio](./composition-effects.md#installing-visual-studio)
- [Создание нового проекта](./composition-effects.md#creating-a-new-project)
- [Установка Win2D](./composition-effects.md#installing-win2d)
- [Настройка основ композиции](./composition-effects.md#setting-your-composition-basics)
- [Создание кисти Компоситионсурфаце](./composition-effects.md#creating-a-compositionsurface-brush)
- [Создание, компиляция и применение эффектов](./composition-effects.md#creating-compiling-and-applying-effects)

### <a name="installing-visual-studio"></a>Установка Visual Studio

- Если у вас еще не установлена поддерживаемая версия Visual Studio, перейдите на страницу [загрузки](https://visualstudio.microsoft.com/downloads/download-visual-studio-vs).

### <a name="creating-a-new-project"></a>Создание нового проекта

- Выберите команду «Файл» -> «Создать» -> «Проект»
- Выберите «Visual C#»
- Создайте пустое приложение (универсальное приложение Windows) (Visual Studio 2015)
- Введите выбранное название проекта
- Нажмите кнопку OК

### <a name="installing-win2d"></a>Установка Win2D

Win2D выпускается в виде пакета Nuget.org. Прежде чем использовать эффекты, необходимо установить этот пакет.

Имеется две версии пакета: одна — для Windows 10, вторая — для Windows 8.1. Для эффектов композиции необходимо использовать версию для Windows 10.

- Запустите диспетчер пакетов NuGet, перейдя к разделу «Инструменты» → «Диспетчер пакетов NuGet» → «Управление пакетами NuGet для решения».
- Найдите Win2D и выберите подходящий пакет для своей целевой версии Windows. Поскольку Windows.UI. Composition поддерживает Windows 10 (но не Windows 8.1), выберите Win2D.uwp.
- Примите условия лицензионного соглашения
- Нажмите «Закрыть».

На следующих этапах нам потребуется использовать API-интерфейсы композиции, чтобы применить эффект и установить минимальное значение насыщенности для данного изображения с котом. В данном сценарии эффект сначала создается, а затем применяется к изображению.

![Исходное изображение](images/composition-cat-source.png)
### <a name="setting-your-composition-basics"></a>Настройка основных параметров композиции

См. [пример визуального дерева композиции](https://github.com/microsoft/WindowsCompositionSamples/tree/master/Demos/Reference Demos/CompositionImageSample) на нашей странице на GitHub. Этот пример показывает, как настроить конструктор Windows.UI.Composition и корневой элемент ContainerVisual и связать их с основным окном.

```cs
_compositor = new Compositor();
_root = _compositor.CreateContainerVisual();
_target = _compositor.CreateTargetForCurrentView();
_target.Root = _root;
_imageFactory = new CompositionImageFactory(_compositor)
Desaturate();
```

### <a name="creating-a-compositionsurface-brush"></a>Создание кисти CompositionSurface

```cs
CompositionSurfaceBrush surfaceBrush = _compositor.CreateSurfaceBrush();
LoadImage(surfaceBrush);
```

### <a name="creating-compiling-and-applying-effects"></a>Создание, компиляция и применение эффектов

1. Создайте графический эффект.

    ```cs
    var graphicsEffect = new SaturationEffect
    {
      Saturation = 0.0f,
      Source = new CompositionEffectSourceParameter("mySource")
    };
    ```

1. Скомпилируйте эффект и создайте кисть эффекта.

    ```cs
    var effectFactory = _compositor.CreateEffectFactory(graphicsEffect);

    var catEffect = effectFactory.CreateBrush();
    catEffect.SetSourceParameter("mySource", surfaceBrush);
    ```

1. Создайте объект SpriteVisual в дереве композиции и примените эффект.

    ```cs
    var catVisual = _compositor.CreateSpriteVisual();
      catVisual.Brush = catEffect;
      catVisual.Size = new Vector2(219, 300);
      _root.Children.InsertAtBottom(catVisual);
    }
    ```

1. Создайте источник изображения для загрузки.

    ```cs
    CompositionImage imageSource = _imageFactory.CreateImageFromUri(new Uri("ms-appx:///Assets/cat.png"));
    CompositionImageLoadResult result = await imageSource.CompleteLoadAsync();
    if (result.Status == CompositionImageLoadStatus.Success)
    ```

1. Измените размер поверхности объекта SpriteVisual и примените к ней кисть.

    ```cs
    brush.Surface = imageSource.Surface;
    ```

1. Запустите приложение. В результате вы должны получить изображение с минимальным уровнем насыщенности.

![Изображение с минимальным уровнем насыщенности](images/composition-cat-desaturated.png)

## <a name="more-information"></a>Дополнительные сведения

- [Microsoft — GitHub, композиция](https://github.com/microsoft/WindowsCompositionSamples)
- [**Windows. UI. композиция**](https://docs.microsoft.com/uwp/api/Windows.UI.Composition)
- [Группа композиции Windows в Twitter](https://twitter.com/wincomposition)
- [Общие сведения о композиции](https://blogs.windows.com/buildingapps/2015/12/08/awaken-your-creativity-with-the-new-windows-ui-composition/)
- [Основы визуального дерева](composition-visual-tree.md)
- [Составные кисти](composition-brushes.md)
- [ксамлкомпоситионбрушбасе](https://docs.microsoft.com/uwp/api/windows.ui.xaml.media.xamlcompositionbrushbase)
- [Общие сведения об анимации](composition-animation.md)
- [Взаимодействие встроенных DirectX и Direct2D с Бегиндрав и EndDraw](composition-native-interop.md)

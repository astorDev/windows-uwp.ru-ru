---
ms.assetid: f1297b7d-1a10-52ae-dd84-6d1ad2ae2fe6
title: Визуальный компонент композиции
description: В композиции объекты класса Visual составляют визуальную древовидную структуру, которую используют и на которую опираются другие функции API композиции. API позволяет разработчикам определить и создать один или несколько объектов класса Visual, каждый из которых представляет отдельный узел визуального дерева.
ms.date: 02/08/2017
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: d6d150f2f882348bffb36dd2918f0f61ea1586c7
ms.sourcegitcommit: ac7f3422f8d83618f9b6b5615a37f8e5c115b3c4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/29/2019
ms.locfileid: "66360489"
---
# <a name="composition-visual"></a>Визуальный компонент композиции

В композиции объекты класса Visual составляют визуальную древовидную структуру, которую используют и на которую опираются другие функции API композиции. API позволяет разработчикам определить и создать один или несколько объектов класса Visual, каждый из которых представляет отдельный узел визуального дерева.

## <a name="visuals"></a>Объекты класса Visual

Визуальная древовидная структура включает три типа объектов класса Visual, а также базовый класс кисти с несколькими подклассами, которые влияют на содержимое визуального объекта.

- [**Visual** ](https://docs.microsoft.com/uwp/api/Windows.UI.Composition.Visual) — базовый объект, большинство свойств являются здесь и наследоваться от других визуальных объектов.
- [**ContainerVisual** ](https://docs.microsoft.com/uwp/api/Windows.UI.Composition.ContainerVisual) — является производным от [ **Visual**](https://docs.microsoft.com/uwp/api/Windows.UI.Composition.Visual)и добавляет возможность создания дочерних элементов.
- [**SpriteVisual** ](https://docs.microsoft.com/uwp/api/Windows.UI.Composition.SpriteVisual) — является производным от [ **ContainerVisual** ](https://docs.microsoft.com/uwp/api/Windows.UI.Composition.ContainerVisual) и добавляет возможность сопоставить кисти, что визуальный элемент можно построить пикселей, включая изображения, эффекты или надежную Цвет.

Контент и эффекты можно применить к объекту SpriteVisuals при помощи [**CompositionBrush**](https://docs.microsoft.com/uwp/api/Windows.UI.Composition.CompositionBrush) и его подклассам, в том числе [**CompositionColorBrush**](https://docs.microsoft.com/uwp/api/Windows.UI.Composition.CompositionColorBrush),[**CompositionSurfaceBrush**](https://docs.microsoft.com/uwp/api/Windows.UI.Composition.CompositionSurfaceBrush) и [**CompositionEffectBrush**](https://docs.microsoft.com/uwp/api/Windows.UI.Composition.CompositionEffectBrush). Дополнительные сведения о кистях см. в статье [**Обзор CompositionBrush**](https://docs.microsoft.com/windows/uwp/composition/composition-brushes).

## <a name="the-compositionvisual-sample"></a>Пример CompositionVisual

Здесь мы рассмотрим пример кода, демонстрирующий три различных визуальных типа, перечисленные выше. Этот пример не охватывает такие понятия как анимация или более сложные эффекты, но он содержит стандартные элементы, которые используются всеми этими системами. (Полный код примера приведен в конце статьи.)

В примере отображается несколько заполненных сплошным цветом квадратов, которые можно нажимать и перетаскивать по экрану. Если щелкнуть на квадрате, он переместится на передний план, повернется на 45 градусов и станет непрозрачным при перетаскивании.

Здесь демонстрируются основные принципы работы с API, в том числе следующие.

- Создание компоновщика
- Создание объекта SpriteVisual и CompositionColorBrush
- Обрезка объекта класса Visual
- Поворот объекта класса Visual
- Настройка уровня прозрачности
- Изменение положения объекта класса Visual в коллекции.

## <a name="creating-a-compositor"></a>Создание компоновщика

Вы можете с легкостью создать объект [**Compositor**](https://docs.microsoft.com/uwp/api/Windows.UI.Composition.Compositor) и сохранить его в переменной для использования в качестве фабрики. В следующем фрагменте кода показано создание нового объекта **Compositor**.

```cs
_compositor = new Compositor();
```

## <a name="creating-a-spritevisual-and-colorbrush"></a>Создание объектов SpriteVisual и ColorBrush

С помощью компоновщика [**Compositor**](https://docs.microsoft.com/uwp/api/Windows.UI.Composition.Compositor) можно при необходимости с легкостью создавать такие объекты, как [**SpriteVisual**](https://docs.microsoft.com/uwp/api/Windows.UI.Composition.SpriteVisual) и [**CompositionColorBrush**](https://docs.microsoft.com/uwp/api/Windows.UI.Composition.CompositionColorBrush).

```cs
var visual = _compositor.CreateSpriteVisual();
visual.Brush = _compositor.CreateColorBrush(Color.FromArgb(0xFF, 0xFF, 0xFF, 0xFF));
```

Это всего несколько строк кода, он демонстрирует это мощная концепция: [**SpriteVisual** ](https://docs.microsoft.com/uwp/api/Windows.UI.Composition.SpriteVisual) объекты являются основой система эффектов. **SpriteVisual** обеспечивает гибкость при использовании и комбинировании цветов, изображений и создаваемых эффектов. **SpriteVisual** является единственным типом визуального объекта, с помощью которого можно заполнить двумерный прямоугольник содержимым кисти, в данном случае — сплошным цветом.

## <a name="clipping-a-visual"></a>Обрезка объекта класса Visual

[  **Compositor**](https://docs.microsoft.com/uwp/api/Windows.UI.Composition.Compositor) также можно использовать для обрезки объектов класса [**Visual**](https://docs.microsoft.com/uwp/api/Windows.UI.Composition.Visual). Ниже приведен фрагмент примера, в котором [**InsetClip**](https://docs.microsoft.com/uwp/api/Windows.UI.Composition.InsetClip) используется для усечения каждой стороны визуального объекта.

```cs
var clip = _compositor.CreateInsetClip();
clip.LeftInset = 1.0f;
clip.RightInset = 1.0f;
clip.TopInset = 1.0f;
clip.BottomInset = 1.0f;
_currentVisual.Clip = clip;
```

Как и другие объекты в API, объект [**InsetClip**](https://docs.microsoft.com/uwp/api/Windows.UI.Composition.InsetClip) позволяет анимировать свои свойства.

## <a name="span-idrotatingaclipspanspan-idrotatingaclipspanspan-idrotatingaclipspanrotating-a-clip"></a><span id="Rotating_a_Clip"></span><span id="rotating_a_clip"></span><span id="ROTATING_A_CLIP"></span>Вращение картинки

К объекту класса [**Visual**](https://docs.microsoft.com/uwp/api/Windows.UI.Composition.Visual) можно применить поворот. Обратите внимание, что свойство [**RotationAngle**](https://docs.microsoft.com/uwp/api/windows.ui.composition.visual.rotationangle) поддерживает значения как в радианах, так и в градусах. По умолчанию используются значения в радианах, но их можно указать и в градусах, как показано в следующем фрагменте кода.

```cs
child.RotationAngleInDegrees = 45.0f;
```

Rotation (Поворот) — это лишь один из компонентов преобразования, которые предоставляет данный API для упрощения таких задач. Сюда также относятся Offset (Смещение), Scale (Масштабирование), Orientation (Ориентация), RotationAxis (Ось поворота) и 4x4 TransformMatrix (Матрица преобразования 4 x 4).

## <a name="setting-opacity"></a>Настройка уровня прозрачности

Установка уровня прозрачности визуального объекта — это простая операция, использующая значения с плавающей точкой. Например, в этом примере начальный уровень прозрачности всех квадратов равен 0,8.

```cs
visual.Opacity = 0.8f;
```

Как и поворот, свойство [**Opacity**](https://docs.microsoft.com/uwp/api/windows.ui.composition.visual.opacity) может быть анимировано.

## <a name="changing-the-visuals-position-in-the-collection"></a>Изменение положения объекта класса Visual в коллекции

API композиции позволяет изменить положение объекта класса Visual в коллекции [**VisualCollection**](https://docs.microsoft.com/uwp/api/windows.ui.composition.visualcollection) несколькими способами. Его можно разместить над другим объектом Visual с помощью [**InsertAbove**](https://docs.microsoft.com/uwp/api/windows.ui.composition.visualcollection.insertabove), разместить ниже с помощью [**InsertBelow**](https://docs.microsoft.com/uwp/api/windows.ui.composition.visualcollection.insertbelow), переместить вверх с помощью [**InsertAtTop**](https://docs.microsoft.com/uwp/api/windows.ui.composition.visualcollection.insertattop) или вниз с помощью [**InsertAtBottom**](https://docs.microsoft.com/uwp/api/windows.ui.composition.visualcollection.insertatbottom).

В этом примере объект класса [**Visual**](https://docs.microsoft.com/uwp/api/Windows.UI.Composition.Visual), на который нажимает пользователь, перемещается наверх.

```cs
parent.Children.InsertAtTop(_currentVisual);
```

## <a name="full-example"></a>Полный пример

В полном примере все описанные выше принципы используются совместно для построения и просмотра простого дерева объектов класса [**Visual**](https://docs.microsoft.com/uwp/api/Windows.UI.Composition.Visual) для изменения уровня прозрачности без использования XAML, WWA или DirectX. В следующем примере показано, как создаются и добавляются дочерние объекты класса **Visual** и как меняются свойства.

```cs
using System;
using System.Collections.Generic;
using System.Linq;
using System.Numerics;
using System.Text;
using System.Threading.Tasks;
using Windows.ApplicationModel.Core;
using Windows.Foundation;
using Windows.UI;
using Windows.UI.Composition;
using Windows.UI.Core;

namespace compositionvisual
{
    class VisualProperties : IFrameworkView
    {
        //------------------------------------------------------------------------------
        //
        // VisualProperties.Initialize
        //
        // This method is called during startup to associate the IFrameworkView with the
        // CoreApplicationView.
        //
        //------------------------------------------------------------------------------

        void IFrameworkView.Initialize(CoreApplicationView view)
        {
            _view = view;
            _random = new Random();
        }

        //------------------------------------------------------------------------------
        //
        // VisualProperties.SetWindow
        //
        // This method is called when the CoreApplication has created a new CoreWindow,
        // allowing the application to configure the window and start producing content
        // to display.
        //
        //------------------------------------------------------------------------------

        void IFrameworkView.SetWindow(CoreWindow window)
        {
            _window = window;
            InitNewComposition();
            _window.PointerPressed += OnPointerPressed;
            _window.PointerMoved += OnPointerMoved;
            _window.PointerReleased += OnPointerReleased;
        }

        //------------------------------------------------------------------------------
        //
        // VisualProperties.OnPointerPressed
        //
        // This method is called when the user touches the screen, taps it with a stylus
        // or clicks the mouse.
        //
        //------------------------------------------------------------------------------

        void OnPointerPressed(CoreWindow window, PointerEventArgs args)
        {
            Point position = args.CurrentPoint.Position;

            //
            // Walk our list of visuals to determine who, if anybody, was selected
            //
            foreach (var child in _root.Children)
            {
                //
                // Did we hit this child?
                //
                Vector3 offset = child.Offset;
                Vector2 size = child.Size;

                if ((position.X >= offset.X) &&
                    (position.X < offset.X + size.X) &&
                    (position.Y >= offset.Y) &&
                    (position.Y < offset.Y + size.Y))
                {
                    //
                    // This child was hit. Since the children are stored back to front,
                    // the last one hit is the front-most one so it wins
                    //
                    _currentVisual = child as ContainerVisual;
                    _offsetBias = new Vector2((float)(offset.X - position.X),
                                              (float)(offset.Y - position.Y));
                }
            }

            //
            // If a visual was hit, bring it to the front of the Z order
            //
            if (_currentVisual != null)
            {
                ContainerVisual parent = _currentVisual.Parent as ContainerVisual;
                parent.Children.Remove(_currentVisual);
                parent.Children.InsertAtTop(_currentVisual);
            }
        }

        //------------------------------------------------------------------------------
        //
        // VisualProperties.OnPointerMoved
        //
        // This method is called when the user moves their finger, stylus or mouse with
        // a button pressed over the screen.
        //
        //------------------------------------------------------------------------------

        void OnPointerMoved(CoreWindow window, PointerEventArgs args)
        {
            //
            // If a visual is selected, drag it with the pointer position and
            // make it opaque while we drag it
            //
            if (_currentVisual != null)
            {
                //
                // Set up the properties of the visual the first time it is
                // dragged. This will last for the duration of the drag
                //
                if (!_dragging)
                {
                    _currentVisual.Opacity = 1.0f;

                    //
                    // Transform the first child of the current visual so that
                    // the image is rotated
                    //
                    foreach (var child in _currentVisual.Children)
                    {
                        child.RotationAngleInDegrees = 45.0f;
                        child.CenterPoint = new Vector3(_currentVisual.Size.X / 2, _currentVisual.Size.Y / 2, 0);
                        break;
                    }

                    //
                    // Clip the visual to its original layout rect by using an inset
                    // clip with a one-pixel margin all around
                    //
                    var clip = _compositor.CreateInsetClip();
                    clip.LeftInset = 1.0f;
                    clip.RightInset = 1.0f;
                    clip.TopInset = 1.0f;
                    clip.BottomInset = 1.0f;
                    _currentVisual.Clip = clip;

                    _dragging = true;
                }

                Point position = args.CurrentPoint.Position;
                _currentVisual.Offset = new Vector3((float)(position.X + _offsetBias.X),
                                                    (float)(position.Y + _offsetBias.Y),
                                                    0.0f);
            }
        }

        //------------------------------------------------------------------------------
        //
        // VisualProperties.OnPointerReleased
        //
        // This method is called when the user lifts their finger or stylus from the
        // screen, or lifts the mouse button.
        //
        //------------------------------------------------------------------------------

        void OnPointerReleased(CoreWindow window, PointerEventArgs args)
        {
            //
            // If a visual was selected, make it transparent again when it is
            // released and restore the transform and clip
            //
            if (_currentVisual != null)
            {
                if (_dragging)
                {
                    //
                    // Remove the transform from the first child
                    //
                    foreach (var child in _currentVisual.Children)
                    {
                        child.RotationAngle = 0.0f;
                        child.CenterPoint = new Vector3(0.0f, 0.0f, 0.0f);
                        break;
                    }

                    _currentVisual.Opacity = 0.8f;
                    _currentVisual.Clip = null;
                    _dragging = false;
                }

                _currentVisual = null;
            }
        }

        //------------------------------------------------------------------------------
        //
        // VisualProperties.Load
        //
        // This method is called when a specific page is being loaded in the
        // application.  It is not used for this application.
        //
        //------------------------------------------------------------------------------

        void IFrameworkView.Load(string unused)
        {

        }

        //------------------------------------------------------------------------------
        //
        // VisualProperties.Run
        //
        // This method is called by CoreApplication.Run() to actually run the
        // dispatcher's message pump.
        //
        //------------------------------------------------------------------------------

        void IFrameworkView.Run()
        {
            _window.Activate();
            _window.Dispatcher.ProcessEvents(CoreProcessEventsOption.ProcessUntilQuit);
        }

        //------------------------------------------------------------------------------
        //
        // VisualProperties.Uninitialize
        //
        // This method is called during shutdown to disconnect the CoreApplicationView,
        // and CoreWindow from the IFrameworkView.
        //
        //------------------------------------------------------------------------------

        void IFrameworkView.Uninitialize()
        {
            _window = null;
            _view = null;
        }

        //------------------------------------------------------------------------------
        //
        // VisualProperties.InitNewComposition
        //
        // This method is called by SetWindow(), where we initialize Composition after
        // the CoreWindow has been created.
        //
        //------------------------------------------------------------------------------

        void InitNewComposition()
        {
            //
            // Set up Windows.UI.Composition Compositor, root ContainerVisual, and associate with
            // the CoreWindow.
            //

            _compositor = new Compositor();

            _root = _compositor.CreateContainerVisual();



            _compositionTarget = _compositor.CreateTargetForCurrentView();
            _compositionTarget.Root = _root;

            //
            // Create a few visuals for our window
            //
            for (int index = 0; index < 20; index++)
            {
                _root.Children.InsertAtTop(CreateChildElement());
            }
        }

        //------------------------------------------------------------------------------
        //
        // VisualProperties.CreateChildElement
        //
        // Creates a small sub-tree to represent a visible element in our application.
        //
        //------------------------------------------------------------------------------

        Visual CreateChildElement()
        {
            //
            // Each element consists of three visuals, which produce the appearance
            // of a framed rectangle
            //
            var element = _compositor.CreateContainerVisual();
            element.Size = new Vector2(100.0f, 100.0f);

            //
            // Position this visual randomly within our window
            //
            element.Offset = new Vector3((float)(_random.NextDouble() * 400), (float)(_random.NextDouble() * 400), 0.0f);

            //
            // The outer rectangle is always white
            //
            //Note to preview API users - SpriteVisual and Color Brush replace SolidColorVisual
            //for example instead of doing
            //var visual = _compositor.CreateSolidColorVisual() and
            //visual.Color = Color.FromArgb(0xFF, 0xFF, 0xFF, 0xFF);
            //we now use the below

            var visual = _compositor.CreateSpriteVisual();
            element.Children.InsertAtTop(visual);
            visual.Brush = _compositor.CreateColorBrush(Color.FromArgb(0xFF, 0xFF, 0xFF, 0xFF));
            visual.Size = new Vector2(100.0f, 100.0f);

            //
            // The inner rectangle is inset from the outer by three pixels all around
            //
            var child = _compositor.CreateSpriteVisual();
            visual.Children.InsertAtTop(child);
            child.Offset = new Vector3(3.0f, 3.0f, 0.0f);
            child.Size = new Vector2(94.0f, 94.0f);

            //
            // Pick a random color for every rectangle
            //
            byte red = (byte)(0xFF * (0.2f + (_random.NextDouble() / 0.8f)));
            byte green = (byte)(0xFF * (0.2f + (_random.NextDouble() / 0.8f)));
            byte blue = (byte)(0xFF * (0.2f + (_random.NextDouble() / 0.8f)));
            child.Brush = _compositor.CreateColorBrush(Color.FromArgb(0xFF, red, green, blue));

            //
            // Make the subtree root visual partially transparent. This will cause each visual in the subtree
            // to render partially transparent, since a visual's opacity is multiplied with its parent's
            // opacity
            //
            element.Opacity = 0.8f;

            return element;
        }

        // CoreWindow / CoreApplicationView
        private CoreWindow _window;
        private CoreApplicationView _view;

        // Windows.UI.Composition
        private Compositor _compositor;
        private CompositionTarget _compositionTarget;
        private ContainerVisual _root;
        private ContainerVisual _currentVisual;
        private Vector2 _offsetBias;
        private bool _dragging;

        // Helpers
        private Random _random;
    }


    public sealed class VisualPropertiesFactory : IFrameworkViewSource
    {
        //------------------------------------------------------------------------------
        //
        // VisualPropertiesFactory.CreateView
        //
        // This method is called by CoreApplication to provide a new IFrameworkView for
        // a CoreWindow that is being created.
        //
        //------------------------------------------------------------------------------

        IFrameworkView IFrameworkViewSource.CreateView()
        {
            return new VisualProperties();
        }


        //------------------------------------------------------------------------------
        //
        // main
        //
        //------------------------------------------------------------------------------

        static int Main(string[] args)
        {
            CoreApplication.Run(new VisualPropertiesFactory());

            return 0;
        }
    }
}
```

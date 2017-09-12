---
author: jebishop
ms.assetid: fb8ae71d-5c88-4c85-9257-a9607d5179b1
title: "освещение"
description: "Объекты Light используются в сочетании с SceneLightingEffect для имитации динамического освещения и отражений."
ms.author: jimwalk
ms.date: 03/29/2017
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: windows 10, uwp
ms.openlocfilehash: 3f922cae8aa0787f8be6496997df1021dda8e142
ms.sourcegitcommit: b42d14c775efbf449a544ddb881abd1c65c1ee86
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/20/2017
---
# <a name="lighting"></a>Освещение

Объекты [**CompositionLight**](https://docs.microsoft.com/uwp/api/Windows.UI.Composition.CompositionLight) используются в сочетании с [**SceneLightingEffect**](https://docs.microsoft.com/uwp/api/Windows.UI.Composition.Effects.SceneLightingEffect) для имитации динамического освещения и отражений.

Освещение можно применять к [**объектам класса Visual**](https://msdn.microsoft.com/library/windows/apps/Dn706858) и объектам XAML [**UIElement**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.UIElement).

## <a name="applying-lights-to-xaml-uielements"></a>Применение освещения к XAML UIElements

Объекты [**XamlLight**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.media.xamllight) позволяют применять [**CompositionLights**](https://docs.microsoft.com/uwp/api/Windows.UI.Composition.CompositionLight) для динамического освещения объектов XAML UIElement. XamlLight предоставляет методы для целевых объектов UIElement или других кистей XAML, применяет освещение к деревьям объектов UIElement и помогает управлять жизненным циклом ресурсов CompositionLight в зависимости от того, используются ли они в данный момент.

* Если в качестве целевого объекта XamlLight выбрана кисть (**Brush**), будут освещены все части объектов UIElements, для которых использовалась эта кисть.
* Если в качестве целевого объекта XamlLight выбран **UIElement**, будет освещен весь этот объект UIElement вместе со своими дочерними объектами UIElement.

## <a name="creating-and-using-a-xamllight"></a>Создание и использование XamlLight

[**XamlLight**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.media.xamllight)— это базовый класс, который можно использовать для создания пользовательских источников света.

Вот пример пользовательского класса XamlLight, который использует присоединенное свойство для освещения определенных элементов:

```csharp
public sealed class FancyOrangeSpotLight : XamlLight
{
    // Register an attached proeprty that enables apps to set a UIElement or Brush as a target for this light type in markup.
    public static readonly DependencyProperty IsTargetProperty =
        DependencyProperty.RegisterAttached(
        "IsTarget",
        typeof(Boolean),
        typeof(FancyOrangeSpotLight),
        new PropertyMetadata(null, OnIsTargetChanged)
    );
    public static void SetIsTarget(DependencyObject target, Boolean value)
    {
        target.SetValue(IsTargetProperty, value);
    }
    public static Boolean GetIsTarget(DependencyObject target)
    {
        return (Boolean) target.GetValue(IsTargetProperty);
    }

    // Handle attached property changed to automatically target and untarget UIElements and Brushes.
    private static void OnIsTargetChanged(DependencyObject obj,
                                            DependencyPropertyChangedEventArgs e)
    {
        var isAdding = (Boolean)e.NewValue;

        if (isAdding)
        {
            if (obj is UIElement)
            {
                XamlLight.AddTargetElement(GetIdStatic(), obj as UIElement);
            }
            else if (obj is Brush)
            {
                XamlLight.AddTargetBrush(GetIdStatic(), obj as Brush);
            }
        }
        else
        {
            if (obj is UIElement)
            {
                XamlLight.RemoveTargetElement(GetIdStatic(), obj as UIElement);
            }
            else if (obj is Brush)
            {
                XamlLight.RemoveTargetBrush(GetIdStatic(), obj as Brush);
            }
        }
    }

    protected override void OnConnected(UIElement newElement)
    {
        // OnConnected is called when the first target UIElement is shown on the screen. This enables delaying composition object creation until it's actually necessary.
        CompositionLight = Window.Current.Compositor.CreateSpotLight();
        CompositionLight.InnerConeColor = Colors.Orange;
        CompositionLight.OuterConeColor = Colors.Yellow;
        CompositionLight.InnerConeAngleInDegrees = 30;
        CompositionLight.OuterConeAngleInDegrees = 45;
    }

    protected override void OnDisconnected(UIElement oldElement)
    {
        // OnDisconnected is called when there are no more target UIElements on the screen. The CompositionLight should be disposed when no longer required.
        CompositionLight.Dispose();
        CompositionLight = null;
    }

    protected override string GetId()
    {
        return GetIdStatic();
    }

    private static string GetIdStatic()
    {
        // This specifies the unique name of the light. In most cases you should use the type's FullName.
        return typeof(FancyPointerTrackerLight).FullName;
    }
}
```

Еще один пример иллюстрирует различные сценарии использования вышеупомянутого пользовательского источника света:

```xml
<Page>
    <Page.Lights>
        <local:SimpleOrangeSpotLight />
    </Page.Lights>

    <StackPanel>
        <!-- this border will be lit by a FancyOrangeSpotLight, but not its children -->
        <Border BorderThickness="1">
            <Border.BorderBrush>
                <SolidColorBrush Color="Orange" local:FancyOrangeSpotLight.IsTarget="true" />
            </Border.BorderBrush>
            <TextBlock Text="hello world" />
        </Border>

        <!-- this border and its children will be lit by FancyOrangeSpotLight -->
        <Border BorderThickness="2" local:FancyOrangeSpotLight.IsTarget="true">
            <Border.BorderBrush>
                <SolidColorBrush Color="Purple" />
            </Border.BorderBrush>
            <TextBlock Text="hello world" />
        </Border>

        <!-- this border will not be lit -->
        <Border BorderThickness="2">
            <Border.BorderBrush>
                <SolidColorBrush Color="Green" />
            </Border.BorderBrush>
            <TextBlock Text="hello world" />
        </Border>
    </StackPanel>
<Page>
```

> [!Important]
> Элемент разметки UIElement.Lights, показанный в приведенном выше примере, поддерживается только для приложений, минимальная версия которых соответствует Windows 10 Creators Update или более поздней версии. Для приложений, предназначенных для более ранних версий, необходимо создать источники освещения в программной части кода.

## <a name="additional-resources"></a>Дополнительные ресурсы

* Расширенные примеры пользовательского интерфейса и композиции в [WindowsUIDevLabs GitHub](https://github.com/microsoft/windowsuidevlabs).
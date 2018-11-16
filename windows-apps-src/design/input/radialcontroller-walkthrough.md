---
author: Karl-Bridge-Microsoft
ms.assetid: ''
title: Поддержка Surface Dial (и других устройств с колесиком) в приложении UWP
description: Пошаговое руководство по добавлению поддержки для Surface Dial (и других устройств с колесиком) в приложении UWP.
keywords: dial, радиальный, учебник
ms.author: kbridge
ms.date: 01/25/2018
ms.topic: article
ms.localizationpriority: medium
ms.openlocfilehash: 3df0f37fda62a7b673e28a6198758365886b6783
ms.sourcegitcommit: e2fca6c79f31e521ba76f7ecf343cf8f278e6a15
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/15/2018
ms.locfileid: "6987171"
---
# <a name="tutorial-support-the-surface-dial-and-other-wheel-devices-in-your-uwp-app"></a>Учебник: "Поддержка Surface Dial (и других устройств с колесиком) в приложении UWP"

![Изображение Surface Dial с Surface Studio](images/radialcontroller/dial-pen-studio-600px.png)  
*Surface Dial с Surface Studio и ручкой Surface* (доступны для приобретения в [магазине Майкрософт](https://aka.ms/purchasesurfacedial)).

В данной статье приводится пошаговое руководство по настройке способов взаимодействия с пользователем, поддерживаемых устройствами с колесиком, такими как Surface Dial. Мы используем фрагменты из примера приложения, который можно скачать с GitHub (см. [Пример кода](#sample-code)), чтобы продемонстрировать различные функции и связанные API-интерфейсы [**RadialController**](https://docs.microsoft.com/uwp/api/windows.ui.input.radialcontroller), описанные в каждом шаге.

Мы обращаем внимание на следующее:
* указание встроенных средств, которые отображаются в меню [**RadialController**](https://docs.microsoft.com/uwp/api/windows.ui.input.radialcontroller);
* добавление в меню специального инструмента;
* управление тактильной обратной связью;
* настройка взаимодействий с помощью нажатия;
* настройка взаимодействий с помощью поворота.

Дополнительные сведения об использовании этих и других функций см. в разделе [Взаимодействие с Surface Dial в приложениях UWP](windows-wheel-interactions.md).

## <a name="introduction"></a>Введение

Surface Dial является дополнительным устройством ввода, которое при использовании вместе с основным устройством ввода, таким как перо, сенсорный ввод или мышь, помогает пользователям работать эффективнее. В качестве дополнительного устройства ввода Dial обычно используется недоминантной рукой для предоставления доступа как для системных команд, так и для других контекстно-зависимых инструментов и функций. 

Dial поддерживает три основных жеста. 
- Нажмите и удерживайте для отображения встроенного меню команд.
- Поверните, чтобы выделить пункт меню (если меню активно) или изменить текущее действие в приложении (если меню не активно).
- Щелкните, чтобы выбрать выделенный пункт меню (если меню активно) или вызвать команду в приложении (если меню не активно).

## <a name="prerequisites"></a>Необходимые компоненты

* Компьютер (или виртуальная машина) под управлением Windows10 Creators Update или более поздней версии
* [Visual Studio 2017 (10.0.15063.0)](https://developer.microsoft.com/windows/downloads)
* [Windows10 SDK (10.0.15063.0)](https://developer.microsoft.com/windows/downloads/windows-10-sdk)
* Устройство с колесиком (в настоящее время только [Surface Dial](https://aka.ms/purchasesurfacedial))
* Если вы еще не знакомы с разработкой приложений универсальной платформы Windows (UWP) с помощью Visual Studio, изучите следующие разделы, прежде чем использовать этот учебник.  
    * [Подготовка](https://docs.microsoft.com/windows/uwp/get-started/get-set-up)
    * [Создание приложения Hello, world (XAML)](https://docs.microsoft.com/windows/uwp/get-started/create-a-hello-world-app-xaml-universal)

## <a name="set-up-your-devices"></a>Настройка устройств

1. Убедитесь, что устройство Windows включено.
2. В меню **Пуск** выберите **Параметры** > **Устройства** > **Bluetooth и другие устройства** и включите **Bluetooth**.
3. Снимите нижнюю часть Dial Surface, чтобы открыть аккумуляторный отсек, и убедитесь, что в нем есть две батареи AAA.
4. Если аккумуляторный отсек находится на нижней стороне Dial, откройте его.
5. Нажмите и удерживайте маленькую, внутреннюю кнопку рядом с батареями, пока не загорится индикатор Bluetooth.
6. Вернитесь к устройству Windows и выберите **Добавить Bluetooth или другое устройство**.
7. В диалоговом окне **Добавить устройство** выберите **Bluetooth** > **Surface Dial**. Теперь Surface Dial должен подключиться и появиться в списке устройств в разделе **Мышь, клавиатура и перо** на странице параметров **Bluetooth и другие устройства**.
8. Проверьте Dial, нажав и удерживая его в течение нескольких секунд для отображения встроенного меню.
9. Если меню не отображается на экране (Dial должен также вибрировать), перейдите к параметрам Bluetooth, удалите устройство и повторите попытку подключения устройства.

> [!NOTE]
> Устройства с колесиком можно настроить с помощью параметров **колесика**.
> 1. В меню **Пуск** выберите **Параметры**.
> 2. Выберите **Устройства** > **Колесико**.    
> ![Экран параметров колесика](images/radialcontroller/wheel-settings.png)

Теперь вы готовы запускать этот учебник. 

## <a name="sample-code"></a>Пример кода
В этом руководстве мы используем пример приложения для демонстрации описанных основных понятий и функций.

Скачайте пример Visual Studio и исходный код с [GitHub](https://github.com/) в [примере windows-appsample-get работы radialcontroller](https://aka.ms/appsample-radialcontroller):

1. Выберите зеленую кнопку **Клонировать или скачать**.  
![Клонирование репозитория](images/radialcontroller/wheel-clone.png)
2. Если у вас есть учетная запись GitHub, вы можете клонировать репозиторий на локальный компьютер, выбрав **Открыть в Visual Studio**. 
3. Если у вас нет учетной записи GitHub или вам просто нужна локальная копия проекта, выберите **Скачать ZIP-файл** (чтобы скачивать последние обновления, необходимо будет регулярно следить за ними).

> [!IMPORTANT]
> Большая часть кода в примере закомментирована. На каждом этапе этого раздела будет предложено раскомментировать различные части кода. В Visual Studio просто выделите строки кода и нажмите клавишу CTRL-K, а затем CTRL-U.

## <a name="components-that-support-wheel-functionality"></a>Компоненты, которые поддерживают работу колесика

Эти объекты составляют основную часть устройства с колесиком для приложений UWP.

| Компонент | Описание |
| --- | --- |
| [Класс **RadialController**](https://msdn.microsoft.com/library/windows/apps/Windows.UI.Input.RadialController) и связанные компоненты | Представляет устройство ввода с колесиком или периферийное устройство, например Surface Dial. |
| [**IRadialControllerConfigurationInterop**](https://msdn.microsoft.com/library/windows/desktop/mt790709) / [**IRadialControllerInterop**](https://msdn.microsoft.com/library/windows/desktop/mt790711)<br/>Здесь не описаны эти функции. Дополнительные сведения см. в разделе [Пример классического приложения для Windows](https://aka.ms/radialcontrollerclassicsample). | Обеспечивает взаимодействие с приложением UWP. |

## <a name="step-1-run-the-sample"></a>Шаг1. Запуск примера

После загрузки примера приложения RadialController убедитесь, что оно запускается.
1. Откройте скачанный проект в Visual Studio.
2. В раскрывающемся списке **Платформы решений** выберите вариант без ARM.
3. Нажмите клавишу F5, чтобы скомпилировать, развернуть и запустить. 

> [!NOTE]
> Кроме того, можно выбрать пункты меню **Отладка** > **Начать отладку** или нажать кнопку запуска **локального компьютера**, показанную здесь: ![кнопка проекта построения Visual Studio](images/radialcontroller/wheel-vsrun.png)

Откроется окно приложения и после появления экрана-заставки на несколько секунд вы увидите этот начальный экран.

![Пустое приложение](images/radialcontroller/wheel-app-step1-empty.png)

Итак, теперь у нас есть базовое приложение UWP, которое будет использоваться в остальных пунктах настоящего руководства. В следующих шагах мы добавим функции **RadialController**.

## <a name="step-2-basic-radialcontroller-functionality"></a>Шаг2. Основные функции RadialController

Запустив приложение на переднем плане, нажмите и удерживайте Surface Dial для отображения меню ** RadialController **.

Мы еще не задали никаких настроек для приложения, поэтому меню содержит стандартный набор контекстно-зависимых инструментов. 

На этих изображениях представлены два варианта меню по умолчанию. (Существуют многие другие, в том числе только базовые системные средства, когда активен рабочий стол Windows и на переднем плане нет приложений, дополнительные инструменты рукописного ввода при наличии InkToolbar и инструменты сопоставления во время использования приложения «Карты».

| Меню RadialController (по умолчанию)  | Меню RadialController (по умолчанию с воспроизведением мультимедиа)  |
|---|---|
| ![Меню RadialController по умолчанию](images/radialcontroller/wheel-app-step2-basic-default.png) | ![Меню RadialController по умолчанию с музыкой](images/radialcontroller/wheel-app-step2-basic-withmusic.png) |

Теперь зададим некоторые базовые настройки.

## <a name="step-3-add-controls-for-wheel-input"></a>Шаг 3. Добавление элементов управления для ввода с помощью колесика

Сначала добавим для приложения пользовательский интерфейс.

1. Откройте файл MainPage_Basic.xaml.
2. Найдите код с заголовком этого шага («\<!-- Шаг3. Добавление элементов управления для ввода с помощью колесика -->»).
3. Раскомментируйте следующие строки.

    ```xaml
    <Button x:Name="InitializeSampleButton" 
            HorizontalAlignment="Center" 
            Margin="10" 
            Content="Initialize sample" />
    <ToggleButton x:Name="AddRemoveToggleButton"
                    HorizontalAlignment="Center" 
                    Margin="10" 
                    Content="Remove Item"
                    IsChecked="True" 
                    IsEnabled="False"/>
    <Button x:Name="ResetControllerButton" 
            HorizontalAlignment="Center" 
            Margin="10" 
            Content="Reset RadialController menu" 
            IsEnabled="False"/>
    <Slider x:Name="RotationSlider" Minimum="0" Maximum="10"
            Width="300"
            HorizontalAlignment="Center"/>
    <TextBlock Text="{Binding ElementName=RotationSlider, Mode=OneWay, Path=Value}"
                Margin="0,0,0,20"
                HorizontalAlignment="Center"/>
    <ToggleSwitch x:Name="ClickToggle"
                    MinWidth="0" 
                    Margin="0,0,0,20"
                    HorizontalAlignment="center"/>
    ```
На данном этапе включены только кнопка **Пример инициализации**, ползунок и переключатель. Остальные кнопки используются в последующих шагах для добавления и удаления пунктов меню **RadialController**, которые предоставляют доступ к ползунку и переключателю.

![Базовый пример пользовательского интерфейса приложения](images/radialcontroller/wheel-app-step3-basicui.png)

## <a name="step-4-customize-the-basic-radialcontroller-menu"></a>Шаг4. Настройка базового меню RadialController

Теперь добавим код, необходимый для включения доступа **RadialController** к элементам управления.

1. Откройте файл MainPage_Basic.xaml.cs.
2. Найдите код с заголовком этого шага («// Шаг4. Настройка базового меню RadialController»).
3. Раскомментируйте следующие строки.
    - Ссылки на тип [Windows.UI.Input](https://docs.microsoft.com/uwp/api/windows.ui.input) и [Windows.Storage.Streams](https://docs.microsoft.com/uwp/api/windows.storage.streams) используются для функциональности в последующих шагах.  
    
        ```csharp
        // Using directives for RadialController functionality.
        using Windows.UI.Input;
        ```

    - Эти глобальные объекты ([RadialController](https://docs.microsoft.com/uwp/api/windows.ui.input.radialcontroller), [RadialControllerConfiguration](https://docs.microsoft.com/uwp/api/windows.ui.input.radialcontrollerconfiguration), [RadialControllerMenuItem](https://docs.microsoft.com/uwp/api/windows.ui.input.radialcontrollermenuitem)) используются в нашем приложении.
    
        ```csharp
        private RadialController radialController;
        private RadialControllerConfiguration radialControllerConfig;
        private RadialControllerMenuItem radialControllerMenuItem;
        ```

    - Здесь мы указываем обработчик **нажатия** для кнопки, с помощью которой включаются элементы управления и запускается пункт настраиваемого меню **RadialController**.

        ```csharp
        InitializeSampleButton.Click += (sender, args) =>
        { InitializeSample(sender, args); };
        ``` 

    - Затем мы инициализируем объект [RadialController](https://docs.microsoft.com/uwp/api/windows.ui.input.radialcontroller) и задаем обработчики для событий [RotationChanged](https://docs.microsoft.com/uwp/api/windows.ui.input.radialcontroller.RotationChanged) и [ButtonClicked](https://docs.microsoft.com/uwp/api/windows.ui.input.radialcontroller.ButtonClicked).

        ```csharp
        // Set up the app UI and RadialController.
        private void InitializeSample(object sender, RoutedEventArgs e)
        {
            ResetControllerButton.IsEnabled = true;
            AddRemoveToggleButton.IsEnabled = true;

            ResetControllerButton.Click += (resetsender, args) =>
            { ResetController(resetsender, args); };
            AddRemoveToggleButton.Click += (togglesender, args) =>
            { AddRemoveItem(togglesender, args); };

            InitializeController(sender, e);
        }
        ```

    - Здесь мы инициализируем пользовательский пункт меню RadialController. Мы используем [CreateForCurrentView](https://docs.microsoft.com/uwp/api/windows.ui.input.radialcontroller.CreateForCurrentView) для получения ссылки на объект [RadialController](https://docs.microsoft.com/uwp/api/windows.ui.input.radialcontroller), устанавливаем значение «1» для чувствительности вращения, используя свойство [RotationResolutionInDegrees](https://docs.microsoft.com/uwp/api/windows.ui.input.radialcontroller.RotationResolutionInDegrees), после чего создаем [RadialControllerMenuItem](https://docs.microsoft.com/uwp/api/windows.ui.input.radialcontrollermenuitem) с помощью [CreateFromFontGlyph](https://docs.microsoft.com/uwp/api/windows.ui.input.radialcontrollermenuitem.CreateFromFontGlyph), добавляем пункт меню в коллекцию пунктов меню **RadialController** и, наконец, используем [SetDefaultMenuItems](https://docs.microsoft.com/uwp/api/windows.ui.input.radialcontrollerconfiguration.setdefaultmenuitems) для удаления пунктов меню по умолчанию и оставляем только специальный инструмент. 

        ```csharp
        // Configure RadialController menu and custom tool.
        private void InitializeController(object sender, RoutedEventArgs args)
        {
            // Create a reference to the RadialController.
            radialController = RadialController.CreateForCurrentView();
            // Set rotation resolution to 1 degree of sensitivity.
            radialController.RotationResolutionInDegrees = 1;

            // Create the custom menu items.
            // Here, we use a font glyph for our custom tool.
            radialControllerMenuItem =
                RadialControllerMenuItem.CreateFromFontGlyph("SampleTool", "\xE1E3", "Segoe MDL2 Assets");

            // Add the item to the RadialController menu.
            radialController.Menu.Items.Add(radialControllerMenuItem);

            // Remove built-in tools to declutter the menu.
            // NOTE: The Surface Dial menu must have at least one menu item. 
            // If all built-in tools are removed before you add a custom 
            // tool, the default tools are restored and your tool is appended 
            // to the default collection.
            radialControllerConfig =
                RadialControllerConfiguration.GetForCurrentView();
            radialControllerConfig.SetDefaultMenuItems(
                new RadialControllerSystemMenuItemKind[] { });

            // Declare input handlers for the RadialController.
            // NOTE: These events are only fired when a custom tool is active.
            radialController.ButtonClicked += (clicksender, clickargs) =>
            { RadialController_ButtonClicked(clicksender, clickargs); };
            radialController.RotationChanged += (rotationsender, rotationargs) =>
            { RadialController_RotationChanged(rotationsender, rotationargs); };
        }

        // Connect wheel device rotation to slider control.
        private void RadialController_RotationChanged(
            object sender, RadialControllerRotationChangedEventArgs args)
        {
            if (RotationSlider.Value + args.RotationDeltaInDegrees >= RotationSlider.Maximum)
            {
                RotationSlider.Value = RotationSlider.Maximum;
            }
            else if (RotationSlider.Value + args.RotationDeltaInDegrees < RotationSlider.Minimum)
            {
                RotationSlider.Value = RotationSlider.Minimum;
            }
            else
            {
                RotationSlider.Value += args.RotationDeltaInDegrees;
            }
        }

        // Connect wheel device click to toggle switch control.
        private void RadialController_ButtonClicked(
            object sender, RadialControllerButtonClickedEventArgs args)
        {
            ClickToggle.IsOn = !ClickToggle.IsOn;
        }
        ```
4. Теперь повторно запустите приложение.
5. Нажмите кнопку **Инициализация радиального контроллера**.  
6. Запустив приложение на переднем плане, нажмите и удерживайте Surface Dial для отображения меню. Обратите внимание, что были удалены все инструменты по умолчанию (с помощью метода **RadialControllerConfiguration.SetDefaultMenuItems**), остался только специальный инструмент. Вот меню со специальным инструментом. 

| Меню RadialController (настраиваемое)  | 
|---|
| ![Настраиваемое меню RadialController](images/radialcontroller/wheel-app-step3-custom.png) |

7. Выберите специальный инструмент и протестируйте взаимодействия, поддерживаемые на этом этапе в Surface Dial.
    * Действие поворота перемещает ползунок. 
    * С помощью щелчка можно установить переключатель в положение «включено» или «отключено».

Хорошо, теперь подключим эти кнопки.

## <a name="step-5-configure-menu-at-runtime"></a>Шаг5. Настройка меню во время выполнения

На этом этапе мы подключим кнопки **Добавление и удаление элемента** и **Меню сброса RadialController**, чтобы показать, как можно динамически настроить меню.

1. Откройте файл MainPage_Basic.xaml.cs.
2. Найдите код с заголовком этого шага («// Шаг5. Настройка меню во время выполнения»).
3. Раскомментируйте код в следующих методах и снова запустите приложение, но не выбирайте никаких кнопок (оставьте это для следующего шага).  

    ``` csharp
    // Add or remove the custom tool.
    private void AddRemoveItem(object sender, RoutedEventArgs args)
    {
        if (AddRemoveToggleButton?.IsChecked == true)
        {
            AddRemoveToggleButton.Content = "Remove item";
            if (!radialController.Menu.Items.Contains(radialControllerMenuItem))
            {
                radialController.Menu.Items.Add(radialControllerMenuItem);
            }
        }
        else if (AddRemoveToggleButton?.IsChecked == false)
        {
            AddRemoveToggleButton.Content = "Add item";
            if (radialController.Menu.Items.Contains(radialControllerMenuItem))
            {
                radialController.Menu.Items.Remove(radialControllerMenuItem);
                // Attempts to select and activate the previously selected tool.
                // NOTE: Does not differentiate between built-in and custom tools.
                radialController.Menu.TrySelectPreviouslySelectedMenuItem();
            }
        }
    }

    // Reset the RadialController to initial state.
    private void ResetController(object sender, RoutedEventArgs arg)
    {
        if (!radialController.Menu.Items.Contains(radialControllerMenuItem))
        {
            radialController.Menu.Items.Add(radialControllerMenuItem);
        }
        AddRemoveToggleButton.Content = "Remove item";
        AddRemoveToggleButton.IsChecked = true;
        radialControllerConfig.SetDefaultMenuItems(
            new RadialControllerSystemMenuItemKind[] { });
    }
    ```
4. Выберите кнопку **Удалить элемент**, а затем нажмите и удерживайте Dial для повторного отображения меню.

    Обратите внимание, что меню теперь содержит набор инструментов по умолчанию. Как мы уже знаем, в шаге3 во время настройки настраиваемого меню мы удалили все инструменты по умолчанию и добавили только специальный инструмент. Мы также говорили, что, когда меню настроено на пустую коллекцию, восстанавливаются элементы по умолчанию для текущего контекста. (Прежде чем удалить инструменты по умолчанию, мы добавили специальный инструмент.)

5. Выберите кнопку **Добавить элемент**, а затем нажмите и удерживайте Dial.

    Обратите внимание, что меню теперь содержит набор инструментов по умолчанию и специальный инструмент.

6. Выберите кнопку **Меню сброса RadialController**, а затем нажмите и удерживайте Dial.

    Обратите внимание, что меню вернулось в исходное состояние.

## <a name="step-6-customize-the-device-haptics"></a>Шаг6. Настройка тактильной обратной связи устройств
Surface Dial и другие устройства с колесиком могут предоставлять пользователям тактильную обратную связь, соответствующую текущему взаимодействию (на основе щелчка или поворота).

На этом этапе мы покажем, как настроить тактильную обратную связь посредством связывания ползунка и элементов управления переключателя, а также использовать их для динамического указания поведения тактильной обратной связи. В этом примере переключатель должен быть установлен в положение «включено», чтобы обратная связь была включена, а значение ползунка указывало, как часто повторяется обратная связь щелчка. 

> [!NOTE]
> Пользователь может отключить тактильную обратную связь на странице **Параметры** >  **Устройства** > **Колесико**.

1. Откройте файл App.xaml.cs.
2. Найдите код с заголовком этого шага («Шаг6. Настройка тактильной обратной связи устройств»).
3. Закомментируйте первую и третью строки («MainPage_Basic» и «MainPage») и раскомментируйте вторую («MainPage_Haptics»).  

    ``` csharp
    rootFrame.Navigate(typeof(MainPage_Basic), e.Arguments);
    rootFrame.Navigate(typeof(MainPage_Haptics), e.Arguments);
    rootFrame.Navigate(typeof(MainPage), e.Arguments);
    ```
4. Откройте файл MainPage_Haptics.xaml.
5. Найдите код с заголовком этого шага («\<!-- Шаг6. Настройка тактильной обратной связи устройств -->»).
6. Раскомментируйте следующие строки. (Этот код пользовательского интерфейса просто показывает, какие функции тактильной обратной связи поддерживаются текущим устройством.)    

    ```xaml
    <StackPanel x:Name="HapticsStack" 
                Orientation="Vertical" 
                HorizontalAlignment="Center" 
                BorderBrush="Gray" 
                BorderThickness="1">
        <TextBlock Padding="10" 
                    Text="Supported haptics properties:" />
        <CheckBox x:Name="CBDefault" 
                    Content="Default" 
                    Padding="10" 
                    IsEnabled="False" 
                    IsChecked="True" />
        <CheckBox x:Name="CBIntensity" 
                    Content="Intensity" 
                    Padding="10" 
                    IsEnabled="False" 
                    IsThreeState="True" 
                    IsChecked="{x:Null}" />
        <CheckBox x:Name="CBPlayCount" 
                    Content="Play count" 
                    Padding="10" 
                    IsEnabled="False" 
                    IsThreeState="True" 
                    IsChecked="{x:Null}" />
        <CheckBox x:Name="CBPlayDuration" 
                    Content="Play duration" 
                    Padding="10" 
                    IsEnabled="False" 
                    IsThreeState="True" 
                    IsChecked="{x:Null}" />
        <CheckBox x:Name="CBReplayPauseInterval" 
                    Content="Replay/pause interval" 
                    Padding="10" 
                    IsEnabled="False" 
                    IsThreeState="True" 
                    IsChecked="{x:Null}" />
        <CheckBox x:Name="CBBuzzContinuous" 
                    Content="Buzz continuous" 
                    Padding="10" 
                    IsEnabled="False" 
                    IsThreeState="True" 
                    IsChecked="{x:Null}" />
        <CheckBox x:Name="CBClick" 
                    Content="Click" 
                    Padding="10" 
                    IsEnabled="False" 
                    IsThreeState="True" 
                    IsChecked="{x:Null}" />
        <CheckBox x:Name="CBPress" 
                    Content="Press" 
                    Padding="10" 
                    IsEnabled="False" 
                    IsThreeState="True" 
                    IsChecked="{x:Null}" />
        <CheckBox x:Name="CBRelease" 
                    Content="Release" 
                    Padding="10" 
                    IsEnabled="False" 
                    IsThreeState="True" 
                    IsChecked="{x:Null}" />
        <CheckBox x:Name="CBRumbleContinuous" 
                    Content="Rumble continuous" 
                    Padding="10" 
                    IsEnabled="False" 
                    IsThreeState="True" 
                    IsChecked="{x:Null}" />
    </StackPanel>
    ```
7. Откройте файл MainPage_Haptics.xaml.cs.
8. Найдите код с заголовком этого шага («Шаг6. Настройка тактильной обратной связи»)
9. Раскомментируйте следующие строки.  

    - Ссылка типа [Windows.Devices.Haptics](https://docs.microsoft.com/uwp/api/windows.devices.haptics) используется для функциональности в последующих шагах.  
    
        ```csharp
        using Windows.Devices.Haptics;
        ```

    - Здесь мы определяем обработчика для события [ControlAcquired](https://docs.microsoft.com/uwp/api/windows.ui.input.radialcontroller.ControlAcquired), которое инициируется при выборе пункта настраиваемого меню **RadialController**.

        ```csharp
        radialController.ControlAcquired += (rc_sender, args) =>
        { RadialController_ControlAcquired(rc_sender, args); };
        ``` 

    - Затем мы определяем обработчика [ControlAcquired](https://docs.microsoft.com/uwp/api/windows.ui.input.radialcontroller.ControlAcquired), где отключаем тактильную обратную связь по умолчанию и инициализируем тактильную обратную пользовательского интерфейса.

        ```csharp
        private void RadialController_ControlAcquired(
            RadialController rc_sender,
            RadialControllerControlAcquiredEventArgs args)
        {
            // Turn off default haptic feedback.
            radialController.UseAutomaticHapticFeedback = false;

            SimpleHapticsController hapticsController =
                args.SimpleHapticsController;

            // Enumerate haptic support.
            IReadOnlyCollection<SimpleHapticsControllerFeedback> supportedFeedback =
                hapticsController.SupportedFeedback;

            foreach (SimpleHapticsControllerFeedback feedback in supportedFeedback)
            {
                if (feedback.Waveform == KnownSimpleHapticsControllerWaveforms.BuzzContinuous)
                {
                    CBBuzzContinuous.IsEnabled = true;
                    CBBuzzContinuous.IsChecked = true;
                }
                else if (feedback.Waveform == KnownSimpleHapticsControllerWaveforms.Click)
                {
                    CBClick.IsEnabled = true;
                    CBClick.IsChecked = true;
                }
                else if (feedback.Waveform == KnownSimpleHapticsControllerWaveforms.Press)
                {
                    CBPress.IsEnabled = true;
                    CBPress.IsChecked = true;
                }
                else if (feedback.Waveform == KnownSimpleHapticsControllerWaveforms.Release)
                {
                    CBRelease.IsEnabled = true;
                    CBRelease.IsChecked = true;
                }
                else if (feedback.Waveform == KnownSimpleHapticsControllerWaveforms.RumbleContinuous)
                {
                    CBRumbleContinuous.IsEnabled = true;
                    CBRumbleContinuous.IsChecked = true;
                }
            }

            if (hapticsController?.IsIntensitySupported == true)
            {
                CBIntensity.IsEnabled = true;
                CBIntensity.IsChecked = true;
            }
            if (hapticsController?.IsPlayCountSupported == true)
            {
                CBPlayCount.IsEnabled = true;
                CBPlayCount.IsChecked = true;
            }
            if (hapticsController?.IsPlayDurationSupported == true)
            {
                CBPlayDuration.IsEnabled = true;
                CBPlayDuration.IsChecked = true;
            }
            if (hapticsController?.IsReplayPauseIntervalSupported == true)
            {
                CBReplayPauseInterval.IsEnabled = true;
                CBReplayPauseInterval.IsChecked = true;
            }
        }
        ```

    - В обработчиках событий [RotationChanged](https://docs.microsoft.com/uwp/api/windows.ui.input.radialcontroller.RotationChanged) и [ButtonClicked](https://docs.microsoft.com/uwp/api/windows.ui.input.radialcontroller.ButtonClicked) мы подключаем соответствующие элементы управления ползунка и переключателя к нашей настраиваемой тактильной обратной связи. 

        ```csharp
        // Connect wheel device rotation to slider control.
        private void RadialController_RotationChanged(
            object sender, RadialControllerRotationChangedEventArgs args)
        {
            ...
            if (ClickToggle.IsOn && 
                (RotationSlider.Value > RotationSlider.Minimum) && 
                (RotationSlider.Value < RotationSlider.Maximum))
            {
                SimpleHapticsControllerFeedback waveform = 
                    FindWaveform(args.SimpleHapticsController, 
                    KnownSimpleHapticsControllerWaveforms.BuzzContinuous);
                if (waveform != null)
                {
                    args.SimpleHapticsController.SendHapticFeedback(waveform);
                }
            }
        }

        private void RadialController_ButtonClicked(
            object sender, RadialControllerButtonClickedEventArgs args)
        {
            ...

            if (RotationSlider?.Value > 0)
            {
                SimpleHapticsControllerFeedback waveform = 
                    FindWaveform(args.SimpleHapticsController, 
                    KnownSimpleHapticsControllerWaveforms.Click);

                if (waveform != null)
                {
                    args.SimpleHapticsController.SendHapticFeedbackForPlayCount(
                        waveform, 1.0, 
                        (int)RotationSlider.Value, 
                        TimeSpan.Parse("1"));
                }
            }
        }
        ```
    - Наконец, мы получаем запрашиваемый элемент **[Waveform](https://docs.microsoft.com/uwp/api/windows.devices.haptics.simplehapticscontrollerfeedback.Waveform)** (если поддерживается) для тактильной обратной связи. 

        ```csharp
        // Get the requested waveform.
        private SimpleHapticsControllerFeedback FindWaveform(
            SimpleHapticsController hapticsController,
            ushort waveform)
        {
            foreach (var hapticInfo in hapticsController.SupportedFeedback)
            {
                if (hapticInfo.Waveform == waveform)
                {
                    return hapticInfo;
                }
            }
            return null;
        }
        ```

Теперь запустите приложение еще раз, чтобы протестировать настраиваемую тактильную обратную связь, изменив значение ползунка и положение переключателя.

## <a name="step-7-define-on-screen-interactions-for-surface-studio-and-similar-devices"></a>Шаг7. Определение взаимодействия на экране для Surface Studio и подобных устройств
При совместном использовании Surface Studio и Surface Dial могут расширить возможности взаимодействия с пользователем. 

В дополнению к действию нажатия и удерживания по умолчанию, описанному выше, Surface Dial можно также разместить прямо на экране Surface Studio. Это позволяет реализовать специальное меню "на экране". 

Определяя расположение контакта и границы Surface Dial, система обрабатывает перекрытие устройством и отображает более крупную версию меню вокруг Surface Dial. Эти же сведения также могут использоваться приложением для адаптации пользовательского интерфейса, если устройство присутствует или предполагается, что оно будет использоваться, например из-за положения руки. 

Пример, приведенный в этом руководстве, включает немного более сложный пример, демонстрирующий некоторые из этих возможностей.

Чтобы увидеть это в действии (потребуется Surface Studio), выполните указанные ниже действия.

1. Скачайте пример на устройстве Surface Studio (с помощью установленного Visual Studio).
2. Откройте пример в Visual Studio.
3. Откройте файл App.xaml.cs.
4. Найдите код с заголовком этого шага («Шаг7. Определение взаимодействия на экране для Surface Studio и подобных устройств»)
5. Закомментируйте первую и вторую строки («MainPage_Basic» и «MainPage_Haptics») и раскомментируйте третью («MainPage»).  

    ``` csharp
    rootFrame.Navigate(typeof(MainPage_Basic), e.Arguments);
    rootFrame.Navigate(typeof(MainPage_Haptics), e.Arguments);
    rootFrame.Navigate(typeof(MainPage), e.Arguments);
    ```
6. Запустите приложение и поместите Surface Dial в каждой из двух областей управления, переключаясь между ними.    
![На экране RadialController](images/radialcontroller/wheel-app-step5-onscreen2.png) 

    Вот видео этого примера в действии.  

    <iframe src="https://channel9.msdn.com/Blogs/One-Dev-Minute/Programming-the-Microsoft-Surface-Dial/player" width="600" height="400" allowFullScreen frameBorder="0"></iframe>  

## <a name="summary"></a>Краткий обзор

Поздравляем, вы ознакомились с *Руководством по началу работы: поддержка Surface Dial (и других устройств с колесиком) в приложении UWP*! Мы показали вам базовый код, необходимый для поддержки устройств с колесиком в приложениях UWP, а также способы предоставления некоторых сложных примеров взаимодействия с пользователем, которые поддерживаются API-интерфейсами **RadialController**.

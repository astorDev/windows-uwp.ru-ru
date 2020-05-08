---
title: Взаимодействие с помощью взгляда
Description: Узнайте, как проектировать и оптимизировать приложения Windows, чтобы обеспечить наилучшую производительность для пользователей, которые используют входные данные взгляда от глазных и головных датчиков.
label: Gaze interactions
template: detail.hbs
keywords: взгляд, отслеживание движения глаз, отслеживание движения головы, точка взгляда, ввод, взаимодействие с пользователем, специальные возможности, удобство использования
ms.date: 05/01/2018
ms.topic: article
pm-contact: Jake Cohen
dev-contact: Austin Hodges
doc-status: Draft
ms.localizationpriority: medium
ms.custom: RS5
ms.openlocfilehash: 4cfd84d54ecd1425b3b7e66c54c96fbd78c2dd46
ms.sourcegitcommit: 0dee502484df798a0595ac1fe7fb7d0f5a982821
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/08/2020
ms.locfileid: "82970129"
---
# <a name="gaze-interactions-and-eye-tracking-in-windows-apps"></a>Взгляд на взаимодействия и отслеживание взгляда в приложениях Windows

![Главное изображение отслеживания движения глаз](images/gaze/eyecontrolbanner1.png)

Реализуйте поддержку отслеживания взгляда, внимания и присутствия пользователя в зависимости от расположения и движения глаз.

> [!NOTE]
> Сведения об использовании взгляда для ввода в [Windows Mixed Reality](https://docs.microsoft.com/windows/mixed-reality/) см. в разделе [Взгляд](https://docs.microsoft.com/windows/mixed-reality/gaze).

**Важные API-интерфейсы**: [Windows.Devices.Input.Preview](https://docs.microsoft.com/uwp/api/windows.devices.input.preview), [GazeDevicePreview](https://docs.microsoft.com/uwp/api/windows.devices.input.preview.gazedevicepreview), [GazePointPreview](https://docs.microsoft.com/uwp/api/windows.devices.input.preview.gazepointpreview), [GazeInputSourcePreview](https://docs.microsoft.com/uwp/api/windows.devices.input.preview.gazeinputsourcepreview)

## <a name="overview"></a>Обзор

Входные данные взгляда — это мощный способ взаимодействия и использования приложений Windows, которые особенно полезны в качестве вспомогательной технологии для пользователей с нейро-мускулар заболеваниях (например, кроме) и другими ограниченными возможностями, включающими непарные функции «мощность» или «нервный».

Кроме того, ввод взглядом также предоставляет привлекательным возможности для игр (в том числе обнаружение и отслеживание цели) и традиционных офисных приложений, киосков и других интерактивных сценариев, в которых традиционные устройства ввода (клавиатура, мышь, сенсорный экран) недоступны или в которых может быть полезно освободить руки пользователя для других задач, например для удерживания сумок для покупок.

> [!NOTE]
> Поддержка оборудования для отслеживания движения глаз впервые появилась в **Windows 10 Fall Creators Update** вместе с [управлением глазами](https://support.microsoft.com/help/4043921/windows-10-get-started-eye-control), встроенной функцией, которая позволяет использовать глаза для управления указателем на экране, ввода с помощью экранной клавиатуры и общения с пользователями с помощью преобразования текста в речь. Набор среда выполнения Windowsных API-интерфейсов ([Windows. Devices. input. Preview](https://docs.microsoft.com/uwp/api/windows.devices.input.preview)) для создания приложений, которые могут взаимодействовать с аппаратным обеспечением отслеживания взгляда, доступны в **обновлении Windows 10 от апреля 2018 (версия 1803, сборка 17134)** и более поздние версии.

## <a name="privacy"></a>Конфиденциальность

Из-за потенциально конфиденциальных персональных данных, собираемых устройствами отслеживания взгляда, необходимо объявить `gazeInput` возможность в манифесте приложения (см. следующий раздел **программы установки** ). Если эта возможность объявлена, Windows автоматически предлагает пользователям предоставить согласие (при первом запуске приложения) в диалоговом окне, в котором им необходимо дать приложению разрешение на связь с устройством для отслеживания глаз и на доступ к этим данным.

Кроме того, если ваше приложение собирает, хранит или передает данные по отслеживанию глаз передачи, вам необходимо описать это в заявлении о конфиденциальности приложения и выполнить все другие требования, связанные с **личными сведениями**, описанные в [соглашении с разработчиком приложений](https://docs.microsoft.com/legal/windows/agreements/app-developer-agreement) и [политиках Microsoft Store](https://docs.microsoft.com/legal/windows/agreements/store-policies).

## <a name="setup"></a>Настройка

Для использования API-интерфейсов ввода с помощью взгляда в приложении Windows необходимо следующее: 

- Возможность `gazeInput` в манифесте приложения.

    Откройте файл **Package.appxmanifest** в конструкторе манифестов Visual Studio или добавьте возможность вручную, выбрав параметр **Просмотреть код** и вставив следующее объявление `DeviceCapability` в узел `Capabilities`:

    ```xaml
    <Capabilities>
       <DeviceCapability Name="gazeInput" />
    </Capabilities>
    ```

- Совместимое с Windows устройство для отслеживания движения глаз, подключенное к системе (встроенное или периферийное) и включенное.

    Список поддерживаемых устройств для отслеживания глаз см. в разделе [Начало работы с управлением глазами в Windows 10](https://support.microsoft.com/help/4043921/windows-10-get-started-eye-control#supported-devices ).

## <a name="basic-eye-tracking"></a>Основы отслеживания движения глаз

В этом примере показано, как отслеживание взгляда пользователя в приложении Windows и использование функции времени с базовой проверкой нажатия для указания того, насколько хорошо они могут поддерживать фокус на определенном элементе.

Маленький эллипс используется для отображения точки взгляда внутри окна просмотра приложения, а элемент управления [RadialProgressBar](https://docs.microsoft.com/windows/communitytoolkit/controls/radialprogressbar) из [набора средств сообщества Windows](https://docs.microsoft.com/windows/communitytoolkit/) случайным образом помещается на холсте. При обнаружении фокуса взгляда на индикаторе выполнения, запускается таймер, а индикатор выполнения случайным образом перемещается по холсту, когда индикатор достигает 100 %.

![Пример отслеживания взгляда с таймером](images/gaze/gaze-input-timed2.gif)

*Пример отслеживания взгляда с таймером*

**Скачайте этот пример из [примера входных данных взгляда (базовый)](https://github.com/MicrosoftDocs/windows-topic-specific-samples/archive/uwp-gazeinput-basic.zip)**

1. Сначала мы настраиваем пользовательский интерфейс (MainPage.xaml).

    ```xaml
    <Page
        x:Class="gazeinput.MainPage"
        xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
        xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
        xmlns:local="using:gazeinput"
        xmlns:d="http://schemas.microsoft.com/expression/blend/2008"
        xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
        xmlns:controls="using:Microsoft.Toolkit.Uwp.UI.Controls"    
        mc:Ignorable="d">
    
        <Grid Background="{ThemeResource ApplicationPageBackgroundThemeBrush}">
            <Grid x:Name="containerGrid">
                <Grid.RowDefinitions>
                    <RowDefinition Height="Auto"/>
                    <RowDefinition Height="*"/>
                </Grid.RowDefinitions>
                <StackPanel x:Name="HeaderPanel" 
                        Orientation="Horizontal" 
                        Grid.Row="0">
                    <StackPanel.Transitions>
                        <TransitionCollection>
                            <AddDeleteThemeTransition/>
                        </TransitionCollection>
                    </StackPanel.Transitions>
                    <TextBlock x:Name="Header" 
                           Text="Gaze tracking sample" 
                           Style="{ThemeResource HeaderTextBlockStyle}" 
                           Margin="10,0,0,0" />
                    <TextBlock x:Name="TrackerCounterLabel"
                           VerticalAlignment="Center"                 
                           Style="{ThemeResource BodyTextBlockStyle}"
                           Text="Number of trackers: " 
                           Margin="50,0,0,0"/>
                    <TextBlock x:Name="TrackerCounter"
                           VerticalAlignment="Center"                 
                           Style="{ThemeResource BodyTextBlockStyle}"
                           Text="0" 
                           Margin="10,0,0,0"/>
                    <TextBlock x:Name="TrackerStateLabel"
                           VerticalAlignment="Center"                 
                           Style="{ThemeResource BodyTextBlockStyle}"
                           Text="State: " 
                           Margin="50,0,0,0"/>
                    <TextBlock x:Name="TrackerState"
                           VerticalAlignment="Center"                 
                           Style="{ThemeResource BodyTextBlockStyle}"
                           Text="n/a" 
                           Margin="10,0,0,0"/>
                </StackPanel>
                <Canvas x:Name="gazePositionCanvas" Grid.Row="1">
                    <controls:RadialProgressBar
                        x:Name="GazeRadialProgressBar" 
                        Value="0"
                        Foreground="Blue" 
                        Background="White"
                        Thickness="4"
                        Minimum="0"
                        Maximum="100"
                        Width="100"
                        Height="100"
                        Outline="Gray"
                        Visibility="Collapsed"/>
                    <Ellipse 
                        x:Name="eyeGazePositionEllipse"
                        Width="20" Height="20"
                        Fill="Blue" 
                        Opacity="0.5" 
                        Visibility="Collapsed">
                    </Ellipse>
                </Canvas>
            </Grid>
        </Grid>
    </Page>
    ```

2. Затем мы инициализируем наше приложение.

    В этом фрагменте кода мы объявляем глобальные объекты и переопределяем событие страницы [OnNavigatedTo](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.page.onnavigatedto) для запуска [наблюдателя за устройством отслеживания взгляда](https://docs.microsoft.com/uwp/api/windows.devices.input.preview.gazedevicewatcherpreview) и событие страницы [OnNavigatedFrom](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.page.onnavigatedfrom) для остановки [этого наблюдателя](https://docs.microsoft.com/uwp/api/windows.devices.input.preview.gazedevicewatcherpreview).

    ```csharp
    using System;
    using Windows.Devices.Input.Preview;
    using Windows.UI.Xaml.Controls;
    using Windows.UI.Xaml;
    using Windows.Foundation;
    using System.Collections.Generic;
    using Windows.UI.Xaml.Media;
    using Windows.UI.Xaml.Navigation;
    
    namespace gazeinput
    {
        public sealed partial class MainPage : Page
        {
            /// <summary>
            /// Reference to the user's eyes and head as detected
            /// by the eye-tracking device.
            /// </summary>
            private GazeInputSourcePreview gazeInputSource;
    
            /// <summary>
            /// Dynamic store of eye-tracking devices.
            /// </summary>
            /// <remarks>
            /// Receives event notifications when a device is added, removed, 
            /// or updated after the initial enumeration.
            /// </remarks>
            private GazeDeviceWatcherPreview gazeDeviceWatcher;
    
            /// <summary>
            /// Eye-tracking device counter.
            /// </summary>
            private int deviceCounter = 0;
    
            /// <summary>
            /// Timer for gaze focus on RadialProgressBar.
            /// </summary>
            DispatcherTimer timerGaze = new DispatcherTimer();
    
            /// <summary>
            /// Tracker used to prevent gaze timer restarts.
            /// </summary>
            bool timerStarted = false;
    
            /// <summary>
            /// Initialize the app.
            /// </summary>
            public MainPage()
            {
                InitializeComponent();
            }

            /// <summary>
            /// Override of OnNavigatedTo page event starts GazeDeviceWatcher.
            /// </summary>
            /// <param name="e">Event args for the NavigatedTo event</param>
            protected override void OnNavigatedTo(NavigationEventArgs e)
            {
                // Start listening for device events on navigation to eye-tracking page.
                StartGazeDeviceWatcher();
            }
    
            /// <summary>
            /// Override of OnNavigatedFrom page event stops GazeDeviceWatcher.
            /// </summary>
            /// <param name="e">Event args for the NavigatedFrom event</param>
            protected override void OnNavigatedFrom(NavigationEventArgs e)
            {
                // Stop listening for device events on navigation from eye-tracking page.
                StopGazeDeviceWatcher();
            }
        }
    }
    ```

3. Затем мы добавляем методы наблюдателя за устройством отслеживания взгляда. 
    
    В `StartGazeDeviceWatcher` мы вызываем метод [CreateWatcher](https://docs.microsoft.com/uwp/api/windows.devices.input.preview.gazeinputsourcepreview.createwatcher) и объявляем прослушиватели событий устройства отслеживания взгляда ([DeviceAdded](https://docs.microsoft.com/uwp/api/windows.devices.input.preview.gazedevicewatcherpreview.added), [DeviceUpdated](https://docs.microsoft.com/uwp/api/windows.devices.input.preview.gazedevicewatcherpreview.updated) и [DeviceRemoved](https://docs.microsoft.com/uwp/api/windows.devices.input.preview.gazedevicewatcherpreview.removed)).

    В `DeviceAdded` мы проверяем состояние устройства отслеживания глаз. Если устройство работоспособно, мы увеличиваем счетчик устройств и включаем отслеживание взгляда. Дополнительные сведения см. в описании следующего шага.

    В `DeviceUpdated` мы также включаем отслеживания взгляда, так как это событие вызывается при повторной калибровке устройства.

    В событии `DeviceRemoved` мы уменьшаем значение счетчика устройств и удаляем обработчики событий устройства.

    В событии `StopGazeDeviceWatcher` мы завершаем работу наблюдателя за устройством отслеживания взгляда. 

```csharp
    /// <summary>
    /// Start gaze watcher and declare watcher event handlers.
    /// </summary>
    private void StartGazeDeviceWatcher()
    {
        if (gazeDeviceWatcher == null)
        {
            gazeDeviceWatcher = GazeInputSourcePreview.CreateWatcher();
            gazeDeviceWatcher.Added += this.DeviceAdded;
            gazeDeviceWatcher.Updated += this.DeviceUpdated;
            gazeDeviceWatcher.Removed += this.DeviceRemoved;
            gazeDeviceWatcher.Start();
        }
    }

    /// <summary>
    /// Shut down gaze watcher and stop listening for events.
    /// </summary>
    private void StopGazeDeviceWatcher()
    {
        if (gazeDeviceWatcher != null)
        {
            gazeDeviceWatcher.Stop();
            gazeDeviceWatcher.Added -= this.DeviceAdded;
            gazeDeviceWatcher.Updated -= this.DeviceUpdated;
            gazeDeviceWatcher.Removed -= this.DeviceRemoved;
            gazeDeviceWatcher = null;
        }
    }

    /// <summary>
    /// Eye-tracking device connected (added, or available when watcher is initialized).
    /// </summary>
    /// <param name="sender">Source of the device added event</param>
    /// <param name="e">Event args for the device added event</param>
    private void DeviceAdded(GazeDeviceWatcherPreview source, 
        GazeDeviceWatcherAddedPreviewEventArgs args)
    {
        if (IsSupportedDevice(args.Device))
        {
            deviceCounter++;
            TrackerCounter.Text = deviceCounter.ToString();
        }
        // Set up gaze tracking.
        TryEnableGazeTrackingAsync(args.Device);
    }

    /// <summary>
    /// Initial device state might be uncalibrated, 
    /// but device was subsequently calibrated.
    /// </summary>
    /// <param name="sender">Source of the device updated event</param>
    /// <param name="e">Event args for the device updated event</param>
    private void DeviceUpdated(GazeDeviceWatcherPreview source,
        GazeDeviceWatcherUpdatedPreviewEventArgs args)
    {
        // Set up gaze tracking.
        TryEnableGazeTrackingAsync(args.Device);
    }

    /// <summary>
    /// Handles disconnection of eye-tracking devices.
    /// </summary>
    /// <param name="sender">Source of the device removed event</param>
    /// <param name="e">Event args for the device removed event</param>
    private void DeviceRemoved(GazeDeviceWatcherPreview source,
        GazeDeviceWatcherRemovedPreviewEventArgs args)
    {
        // Decrement gaze device counter and remove event handlers.
        if (IsSupportedDevice(args.Device))
        {
            deviceCounter--;
            TrackerCounter.Text = deviceCounter.ToString();

            if (deviceCounter == 0)
            {
                gazeInputSource.GazeEntered -= this.GazeEntered;
                gazeInputSource.GazeMoved -= this.GazeMoved;
                gazeInputSource.GazeExited -= this.GazeExited;
            }
        }
    }
```

4. Здесь мы проверяем, доступно ли устройство в `IsSupportedDevice`. Если это так, мы пытаемся включить отслеживание взгляда в `TryEnableGazeTrackingAsync`.

    В `TryEnableGazeTrackingAsync` мы объявляем обработчики событий взгляда и вызываем [GazeInputSourcePreview.GetForCurrentView()](https://docs.microsoft.com/uwp/api/windows.devices.input.preview.gazeinputsourcepreview.getforcurrentview) для получения ссылки на источник входных данных (эту функцию следует вызвать в потоке пользовательского интерфейса, см. раздел [Обеспечение быстрого отклика потока пользовательского интерфейса](https://docs.microsoft.com/windows/uwp/debug-test-perf/keep-the-ui-thread-responsive)).

    > [!NOTE]
    > Функцию [GazeInputSourcePreview.GetForCurrentView()](https://docs.microsoft.com/uwp/api/windows.devices.input.preview.gazeinputsourcepreview.getforcurrentview) следует вызывать, только если совместимое устройство для отслеживания глаз подключено и необходимо вашему приложению. В противном случае диалоговое окно запроса разрешения не требуется.

```csharp
    /// <summary>
    /// Initialize gaze tracking.
    /// </summary>
    /// <param name="gazeDevice"></param>
    private async void TryEnableGazeTrackingAsync(GazeDevicePreview gazeDevice)
    {
        // If eye-tracking device is ready, declare event handlers and start tracking.
        if (IsSupportedDevice(gazeDevice))
        {
            timerGaze.Interval = new TimeSpan(0, 0, 0, 0, 20);
            timerGaze.Tick += TimerGaze_Tick;

            SetGazeTargetLocation();

            // This must be called from the UI thread.
            gazeInputSource = GazeInputSourcePreview.GetForCurrentView();

            gazeInputSource.GazeEntered += GazeEntered;
            gazeInputSource.GazeMoved += GazeMoved;
            gazeInputSource.GazeExited += GazeExited;
        }
        // Notify if device calibration required.
        else if (gazeDevice.ConfigurationState ==
                    GazeDeviceConfigurationStatePreview.UserCalibrationNeeded ||
                    gazeDevice.ConfigurationState ==
                    GazeDeviceConfigurationStatePreview.ScreenSetupNeeded)
        {
            // Device isn't calibrated, so invoke the calibration handler.
            System.Diagnostics.Debug.WriteLine(
                "Your device needs to calibrate. Please wait for it to finish.");
            await gazeDevice.RequestCalibrationAsync();
        }
        // Notify if device calibration underway.
        else if (gazeDevice.ConfigurationState == 
            GazeDeviceConfigurationStatePreview.Configuring)
        {
            // Device is currently undergoing calibration.  
            // A device update is sent when calibration complete.
            System.Diagnostics.Debug.WriteLine(
                "Your device is being configured. Please wait for it to finish"); 
        }
        // Device is not viable.
        else if (gazeDevice.ConfigurationState == GazeDeviceConfigurationStatePreview.Unknown)
        {
            // Notify if device is in unknown state.  
            // Reconfigure/recalbirate the device.  
            System.Diagnostics.Debug.WriteLine(
                "Your device is not ready. Please set up your device or reconfigure it."); 
        }
    }

    /// <summary>
    /// Check if eye-tracking device is viable.
    /// </summary>
    /// <param name="gazeDevice">Reference to eye-tracking device.</param>
    /// <returns>True, if device is viable; otherwise, false.</returns>
    private bool IsSupportedDevice(GazeDevicePreview gazeDevice)
    {
        TrackerState.Text = gazeDevice.ConfigurationState.ToString();
        return (gazeDevice.CanTrackEyes &&
                    gazeDevice.ConfigurationState == 
                    GazeDeviceConfigurationStatePreview.Ready);
    }
```

5. Затем мы настраиваем обработчики событий взгляда.

    Мы отображаем и скрываем эллипс отслеживания взгляд в `GazeEntered` и `GazeExited` соответственно.

    В `GazeMoved` мы перемещаем эллипс отслеживания взгляда на основе свойства [EyeGazePosition](https://docs.microsoft.com/uwp/api/windows.devices.input.preview.gazepointpreview.eyegazeposition), предоставленного [CurrentPoint](https://docs.microsoft.com/uwp/api/windows.devices.input.preview.gazeenteredprevieweventargs.currentpoint) объекта [GazeEnteredPreviewEventArgs](https://docs.microsoft.com/uwp/api/windows.devices.input.preview.gazeenteredprevieweventargs). Мы также управляем таймером фокусировки взгляд в элементе [RadialProgressBar](https://docs.microsoft.com/windows/communitytoolkit/controls/radialprogressbar), который активирует изменения положение индикатора выполнения. Дополнительные сведения см. в описании следующего шага.

    ```csharp
    /// <summary>
    /// GazeEntered handler.
    /// </summary>
    /// <param name="sender">Source of the gaze entered event</param>
    /// <param name="e">Event args for the gaze entered event</param>
    private void GazeEntered(
        GazeInputSourcePreview sender, 
        GazeEnteredPreviewEventArgs args)
    {
        // Show ellipse representing gaze point.
        eyeGazePositionEllipse.Visibility = Visibility.Visible;

        // Mark the event handled.
        args.Handled = true;
    }

    /// <summary>
    /// GazeExited handler.
    /// Call DisplayRequest.RequestRelease to conclude the 
    /// RequestActive called in GazeEntered.
    /// </summary>
    /// <param name="sender">Source of the gaze exited event</param>
    /// <param name="e">Event args for the gaze exited event</param>
    private void GazeExited(
        GazeInputSourcePreview sender, 
        GazeExitedPreviewEventArgs args)
    {
        // Hide gaze tracking ellipse.
        eyeGazePositionEllipse.Visibility = Visibility.Collapsed;

        // Mark the event handled.
        args.Handled = true;
    }

    /// <summary>
    /// GazeMoved handler translates the ellipse on the canvas to reflect gaze point.
    /// </summary>
    /// <param name="sender">Source of the gaze moved event</param>
    /// <param name="e">Event args for the gaze moved event</param>
    private void GazeMoved(GazeInputSourcePreview sender, GazeMovedPreviewEventArgs args)
    {
        // Update the position of the ellipse corresponding to gaze point.
        if (args.CurrentPoint.EyeGazePosition != null)
        {
            double gazePointX = args.CurrentPoint.EyeGazePosition.Value.X;
            double gazePointY = args.CurrentPoint.EyeGazePosition.Value.Y;

            double ellipseLeft = 
                gazePointX - 
                (eyeGazePositionEllipse.Width / 2.0f);
            double ellipseTop = 
                gazePointY - 
                (eyeGazePositionEllipse.Height / 2.0f) - 
                (int)Header.ActualHeight;

            // Translate transform for moving gaze ellipse.
            TranslateTransform translateEllipse = new TranslateTransform
            {
                X = ellipseLeft,
                Y = ellipseTop
            };

            eyeGazePositionEllipse.RenderTransform = translateEllipse;

            // The gaze point screen location.
            Point gazePoint = new Point(gazePointX, gazePointY);

            // Basic hit test to determine if gaze point is on progress bar.
            bool hitRadialProgressBar = 
                DoesElementContainPoint(
                    gazePoint, 
                    GazeRadialProgressBar.Name, 
                    GazeRadialProgressBar); 

            // Use progress bar thickness for visual feedback.
            if (hitRadialProgressBar)
            {
                GazeRadialProgressBar.Thickness = 10;
            }
            else
            {
                GazeRadialProgressBar.Thickness = 4;
            }

            // Mark the event handled.
            args.Handled = true;
        }
    }
    ```
6. Наконец, вот методы, используемые для управления таймером фокусировки взгляда для этого приложения.

    `DoesElementContainPoint`проверяет, находится ли указатель указателя мыши над индикатором выполнения. Если это так, запускается таймер взгляда и увеличивается индикатор выполнения на каждый ход таймера взгляд.

    `SetGazeTargetLocation`Задает начальное расположение индикатора выполнения и, при завершении индикатора выполнения (в зависимости от таймера фокуса), перемещает индикатор выполнения в случайное расположение.

    ```csharp
    /// <summary>
    /// Return whether the gaze point is over the progress bar.
    /// </summary>
    /// <param name="gazePoint">The gaze point screen location</param>
    /// <param name="elementName">The progress bar name</param>
    /// <param name="uiElement">The progress bar UI element</param>
    /// <returns></returns>
    private bool DoesElementContainPoint(
        Point gazePoint, string elementName, UIElement uiElement)
    {
        // Use entire visual tree of progress bar.
        IEnumerable<UIElement> elementStack = 
            VisualTreeHelper.FindElementsInHostCoordinates(gazePoint, uiElement, true);
        foreach (UIElement item in elementStack)
        {
            //Cast to FrameworkElement and get element name.
            if (item is FrameworkElement feItem)
            {
                if (feItem.Name.Equals(elementName))
                {
                    if (!timerStarted)
                    {
                        // Start gaze timer if gaze over element.
                        timerGaze.Start();
                        timerStarted = true;
                    }
                    return true;
                }
            }
        }

        // Stop gaze timer and reset progress bar if gaze leaves element.
        timerGaze.Stop();
        GazeRadialProgressBar.Value = 0;
        timerStarted = false;
        return false;
    }

    /// <summary>
    /// Tick handler for gaze focus timer.
    /// </summary>
    /// <param name="sender">Source of the gaze entered event</param>
    /// <param name="e">Event args for the gaze entered event</param>
    private void TimerGaze_Tick(object sender, object e)
    {
        // Increment progress bar.
        GazeRadialProgressBar.Value += 1;

        // If progress bar reaches maximum value, reset and relocate.
        if (GazeRadialProgressBar.Value == 100)
        {
            SetGazeTargetLocation();
        }
    }

    /// <summary>
    /// Set/reset the screen location of the progress bar.
    /// </summary>
    private void SetGazeTargetLocation()
    {
        // Ensure the gaze timer restarts on new progress bar location.
        timerGaze.Stop();
        timerStarted = false;

        // Get the bounding rectangle of the app window.
        Rect appBounds = Windows.UI.ViewManagement.ApplicationView.GetForCurrentView().VisibleBounds;

        // Translate transform for moving progress bar.
        TranslateTransform translateTarget = new TranslateTransform();

        // Calculate random location within gaze canvas.
            Random random = new Random();
            int randomX = 
                random.Next(
                    0, 
                    (int)appBounds.Width - (int)GazeRadialProgressBar.Width);
            int randomY = 
                random.Next(
                    0, 
                    (int)appBounds.Height - (int)GazeRadialProgressBar.Height - (int)Header.ActualHeight);

        translateTarget.X = randomX;
        translateTarget.Y = randomY;

        GazeRadialProgressBar.RenderTransform = translateTarget;

        // Show progress bar.
        GazeRadialProgressBar.Visibility = Visibility.Visible;
        GazeRadialProgressBar.Value = 0;
    }
    ```

## <a name="see-also"></a>См. также

### <a name="resources"></a>Ресурсы

- [Библиотека Gaze из набора средств сообщества Windows](https://docs.microsoft.com/windows/communitytoolkit/gaze/gazeinteractionlibrary)

### <a name="topic-samples"></a>Примеры в статье

- [Пример использования взгляда (базовый) (C#)](https://github.com/MicrosoftDocs/windows-topic-specific-samples/archive/uwp-gazeinput-basic.zip)

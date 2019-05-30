---
title: Более продолжительное отображение экрана-заставки
description: Увеличьте длительность отображения экрана-заставки, создав и использовав расширенный экран-заставку для приложения. Этот расширенный экран имитирует экран-заставку, отображаемый при запуске приложения, но его можно настраивать.
ms.assetid: CD3053EB-7F86-4D74-9C5A-950303791AE3
ms.date: 02/19/2019
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: bbc0c7c695a99354ee389118087773440b60fb20
ms.sourcegitcommit: ac7f3422f8d83618f9b6b5615a37f8e5c115b3c4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/29/2019
ms.locfileid: "66366284"
---
# <a name="display-a-splash-screen-for-more-time"></a>Более продолжительное отображение экрана-заставки

**Важные API**

-   [SplashScreen-класс](https://docs.microsoft.com/uwp/api/Windows.ApplicationModel.Activation.SplashScreen)
-   [Событие Window.SizeChanged](https://docs.microsoft.com/uwp/api/windows.ui.xaml.window.sizechanged)
-   [Метод Application.OnLaunched](https://docs.microsoft.com/uwp/api/windows.ui.xaml.application.onlaunched)

Увеличьте длительность отображения экрана-заставки, создав и использовав расширенный экран-заставку для приложения. Этот расширенный экран имитирует экран-заставку, отображаемый при запуске приложения, но его можно настраивать. Если вы хотите показывать информацию о загрузке в реальном времени или дать приложению дополнительное время на подготовку начального пользовательского интерфейса, расширенный экран-заставка позволит определить взаимодействие с пользователем при запуске.

> [!NOTE]
> Фраза «расширенные экрана-заставки» в этом разделе относится к экрана-заставки, которая остается на экране в течение продолжительного периода времени. Это не значит, подкласс, производный от [экран-заставка](https://docs.microsoft.com/uwp/api/Windows.ApplicationModel.Activation.SplashScreen) класса.

Убедитесь, что ваш расширенный экран-заставка точно имитирует экран-заставку, отображаемый по умолчанию. Для этого следуйте приведенным ниже рекомендациям.

-   Страница расширенного экрана-заставки должна использовать изображение размером 620 x 300 пикселей, соответствующее изображению, которое указано для экрана-заставки в манифесте приложения (изображение экрана-заставки вашего приложения). В Microsoft Visual Studio 2015, параметры экрана заставки, хранятся в **экран-заставка** раздел **визуальные активы** вкладку в манифесте приложения (файл Package.appxmanifest).
-   Расширенный экран-заставка должен использовать цвет фона, соответствующий цвету фона, который указан для экрана-заставки в манифесте приложения (фон экрана-заставки вашего приложения).
-   Код должен использовать [экран-заставка](https://docs.microsoft.com/uwp/api/Windows.ApplicationModel.Activation.SplashScreen) класс позволяет помещать изображение экрана-заставки приложения на одном экране координирует как экрана-заставки по умолчанию.
-   Ваш код должен реагировать на события изменения размера окна (например, если повернуть экран или приложение перемещается рядом с другим приложением на экране) с помощью [экран-заставка](https://docs.microsoft.com/uwp/api/Windows.ApplicationModel.Activation.SplashScreen) класс изменение положения элементов на экране расширенной заставки.

Чтобы создать расширенный экран-заставку, имитирующий экран-заставку, который отображается по умолчанию, используйте процедуру, описанную ниже.

## <a name="add-a-blank-page-item-to-your-existing-app"></a>Добавление элемента **Пустая страница** в существующее приложение


В этой статье подразумевается, что вы хотите добавить расширенный экран-заставку в существующий проект приложения универсальной платформы Windows (UWP) на C#, Visual Basic или C++.

-   Откройте приложение в Visual Studio.
-   Откройте пункт **Проект** в строке меню и нажмите кнопку **Добавить новый элемент**. Откроется диалоговое окно **Добавление нового элемента**.
-   В этом диалоговом окне добавьте новую **пустую страницу** в приложение. В этом разделе страница расширенного экрана-заставки называется ExtendedSplash.

При добавлении элемента **пустая страница** создаются два файла: один для разметки (ExtendedSplash.xaml) и второй для кода (ExtendedSplash.xaml.cs).

## <a name="essential-xaml-for-an-extended-splash-screen"></a>Базовый код XAML для расширенного экрана-заставки


Выполните эти действия, чтобы добавить изображение и элемент управления «Ход выполнения» на расширенный экран-заставку.

В файле ExtendedSplash.xaml:

-   Изменение [фона](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.control.backgroundproperty) свойство по умолчанию [сетки](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Grid) элемента в соответствии с цвет фона, задайте для экрана-заставки приложения в манифесте приложения (в **визуальные активы**раздела файла Package.appxmanifest). Светло-серого является цвет экрана-заставки по умолчанию (шестнадцатеричное значение \#464646). Обратите внимание, что этот элемент **Grid** предоставляется по умолчанию при создании новой **пустой страницы**. Не нужно обязательно использовать **Grid**, — это просто удобная основа для создания расширенного экрана-заставки.
-   Добавить [холст](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Canvas) элемент [сетки](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Grid). **Canvas** используется для размещения изображения расширенного экрана-заставки.
-   Добавить [изображение](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Image) элемент [холст](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Canvas). Используйте для расширенного экрана-заставки то же изображение размером 600 x 320 пикселей, которое вы выбрали для экрана-заставки, отображаемого по умолчанию.
-   (Необязательно) Добавьте элемент управления «Ход выполнения», который покажет пользователям, что ваше приложение загружается. В этом разделе добавляет [ProgressRing](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.ProgressRing), а не определенный или неопределенным [ProgressBar](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.ProgressBar).

В следующем примере демонстрируется [сетки](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Grid) с следующие изменения и дополнения.

```xaml
    <Grid Background="#464646">
        <Canvas>
            <Image x:Name="extendedSplashImage" Source="Assets/SplashScreen.png"/>
            <ProgressRing Name="splashProgressRing" IsActive="True" Width="20" HorizontalAlignment="Center"></ProgressRing>
        </Canvas>
    </Grid>
```

> [!NOTE]
> В этом примере задает ширину [ProgressRing](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.ProgressRing) до 20 пикселей. Можно вручную задать для ширины значение, подходящее для вашего приложения, но элемент управления не будет выполнять отрисовку при ширине меньше 20 пикселей.

## <a name="essential-code-for-an-extended-splash-screen-class"></a>Базовый код для класса расширенного экрана-заставки


Расширенный экран-заставка должен реагировать на любые изменения размера (только Windows) или ориентации окна. Необходимо обновлять положение используемого изображения таким образом, чтобы расширенный экран-заставка смотрелся хорошо независимо от изменения размера окна.

Используйте эту процедуру, чтобы определить методы правильного отображения расширенного экрана-заставки.

1.  **Добавьте необходимые пространства имен**

    Вам потребуется добавить следующие пространства имен для **ExtendedSplash.xaml.cs** для доступа к [экран-заставка](https://docs.microsoft.com/uwp/api/Windows.ApplicationModel.Activation.SplashScreen) класс, [Rect](https://docs.microsoft.com/uwp/api/windows.foundation.rect) структуры и [ Window.SizeChanged](https://docs.microsoft.com/uwp/api/windows.ui.xaml.window.sizechanged) события.

    ```cs
    using Windows.ApplicationModel.Activation;
    using Windows.Foundation;
    using Windows.UI.Core;
    ```

2.  **Создания разделяемого класса и объявлять переменные класса**

    Включите следующий код в файл ExtendedSplash.xaml.cs, чтобы создать разделяемый класс для представления расширенного экрана-заставки.

    ```cs
    partial class ExtendedSplash : Page
    {
        internal Rect splashImageRect; // Rect to store splash screen image coordinates.
        private SplashScreen splash; // Variable to hold the splash screen object.
        internal bool dismissed = false; // Variable to track splash screen dismissal status.
        internal Frame rootFrame;

       // Define methods and constructor
    }
    ```

    Эти переменные класса используются несколькими методами. В переменной `splashImageRect` хранятся координаты, в которых система показывает изображение экрана-заставки для приложения. `splash` Переменной хранилищ [экран-заставка](https://docs.microsoft.com/uwp/api/Windows.ApplicationModel.Activation.SplashScreen) объекта и `dismissed` отслеживаются в переменной, независимо от того, имеется ли на экране-заставке, которое отображается в системе были отключены.

3.  **Определение конструктора для вашего класса, который правильно определяет положение изображения**

    Следующий код определяет конструктор для класса расширенного экрана-заставки, который ожидает передачи данных для событий изменения размера окна, размещает изображение и (необязательно) элемент управления «Ход выполнения» на расширенном экране-заставке, создает фрейм для навигации и вызывает асинхронный метод для восстановления сохраненного состояния сеанса.

    ```cs
    public ExtendedSplash(SplashScreen splashscreen, bool loadState)
    {
        InitializeComponent();

        // Listen for window resize events to reposition the extended splash screen image accordingly.
        // This ensures that the extended splash screen formats properly in response to window resizing.
        Window.Current.SizeChanged += new WindowSizeChangedEventHandler(ExtendedSplash_OnResize);

        splash = splashscreen;
        if (splash != null)
        {
            // Register an event handler to be executed when the splash screen has been dismissed.
            splash.Dismissed += new TypedEventHandler<SplashScreen, Object>(DismissedEventHandler);

            // Retrieve the window coordinates of the splash screen image.
            splashImageRect = splash.ImageLocation;
            PositionImage();

            // If applicable, include a method for positioning a progress control.
            PositionRing();
        }

        // Create a Frame to act as the navigation context
        rootFrame = new Frame();            
    }
    ```

    Убедитесь, что для регистрации вашей [Window.SizeChanged](https://docs.microsoft.com/uwp/api/windows.ui.xaml.window.sizechanged) обработчик (`ExtendedSplash_OnResize` в примере) в конструкторе класса, чтобы приложение помещает изображение правильно в качестве расширенных заставки.

4.  **Определите метод класса для размещения изображения в качестве расширенных заставки**

    Этот код демонстрирует размещение изображения на странице расширенного экрана-заставки с использованием переменной класса `splashImageRect`.

    ```cs
    void PositionImage()
    {
        extendedSplashImage.SetValue(Canvas.LeftProperty, splashImageRect.X);
        extendedSplashImage.SetValue(Canvas.TopProperty, splashImageRect.Y);
        extendedSplashImage.Height = splashImageRect.Height;
        extendedSplashImage.Width = splashImageRect.Width;
    }
    ```

5.  **(Необязательно) Определение метода класса для размещения элемента управления хода выполнения в качестве расширенных заставки**

    Если вы решили добавить [ProgressRing](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.ProgressRing) на экране расширенной заставки, расположите его относительно изображение экрана-заставки. Добавьте следующий код в файл ExtendedSplash.xaml.cs, чтобы расположить **ProgressRing** по центру на 32 пикселя ниже изображения.

    ```cs
    void PositionRing()
    {
        splashProgressRing.SetValue(Canvas.LeftProperty, splashImageRect.X + (splashImageRect.Width*0.5) - (splashProgressRing.Width*0.5));
        splashProgressRing.SetValue(Canvas.TopProperty, (splashImageRect.Y + splashImageRect.Height + splashImageRect.Height*0.1));
    }
    ```

6.  **Внутри класса определите обработчик для события отклонено**

    В ExtendedSplash.xaml.cs, отвечать при [SplashScreen.Dismissed](https://docs.microsoft.com/uwp/api/windows.applicationmodel.activation.splashscreen.dismissed) событием, задав `dismissed` переменной класса значение true. Если ваше приложение предусматривает операции настройки, добавьте их в этот обработчик событий.

    ```cs
    // Include code to be executed when the system has transitioned from the splash screen to the extended splash screen (application's first view).
    void DismissedEventHandler(SplashScreen sender, object e)
    {
        dismissed = true;

        // Complete app setup operations here...
    }
    ```

    По завершении настройки приложения перейдите с расширенного экрана-заставки на другую страницу. Следующий код определяет метод `DismissExtendedSplash` для перехода на страницу `MainPage`, определенную в файле MainPage.xaml приложения.

    ```cs
    async void DismissExtendedSplash()
      {
         await Windows.ApplicationModel.Core.CoreApplication.MainView.CoreWindow.Dispatcher.RunAsync(CoreDispatcherPriority.Normal,() =>            {
              rootFrame = new Frame();
              rootFrame.Content = new MainPage(); Window.Current.Content = rootFrame;
            });
      }
      ```

7.  **Внутри класса определите обработчик событий Window.SizeChanged**

    Подготовьте расширенный экран-заставку к изменению положения его элементов, если пользователь изменит размер окна. Этот код реагирует при [Window.SizeChanged](https://docs.microsoft.com/uwp/api/windows.ui.xaml.window.sizechanged) событие происходит путем записи новые координаты и расположения изображения. Если вы добавили на экран-заставку элемент управления «Ход выполнения», также измените его положение внутри этого обработчика событий.

    ```cs
    void ExtendedSplash_OnResize(Object sender, WindowSizeChangedEventArgs e)
    {
        // Safely update the extended splash screen image coordinates. This function will be executed when a user resizes the window.
        if (splash != null)
        {
            // Update the coordinates of the splash screen image.
            splashImageRect = splash.ImageLocation;
            PositionImage();

            // If applicable, include a method for positioning a progress control.
            // PositionRing();
        }
    }
    ```

    > [!NOTE]
    > Прежде чем пытаться получить расположение образа убедитесь, что переменная класса (`splash`) содержит допустимое [экран-заставка](https://docs.microsoft.com/uwp/api/Windows.ApplicationModel.Activation.SplashScreen) объекта, как показано в примере.

     

8.  **(Необязательно) Добавьте метод класса, чтобы восстановить состояние сохраненный сеанс**

    Код, добавляемый [OnLaunched](https://docs.microsoft.com/uwp/api/windows.ui.xaml.application.onlaunched) метод на шаге 4: [Измените обработчик активации запуска](#modify-the-launch-activation-handler) приложение для отображения расширенных заставку при запуске. Чтобы консолидировать все методы, связанные с запуск приложений в вашем классе расширенных заставка экрана, можно рассмотреть добавление метода в файл ExtendedSplash.xaml.cs для восстановления состояния приложения.

    ```cs
    void RestoreState(bool loadState)
    {
        if (loadState)
        {
             // code to load your app's state here
        }
    }
    ```

    При изменении обработчик запуска активации в App.xaml.cs, необходимо также установить `loadstate` значение true, если предыдущий [ApplicationExecutionState](https://docs.microsoft.com/uwp/api/Windows.ApplicationModel.Activation.ApplicationExecutionState) вашего приложения был **Terminated**. В этом случае метод `RestoreState` восстанавливает предыдущее состояние приложения. Обзор запуска, приостановки и завершения приложения см. в статье [Жизненный цикл приложения](app-lifecycle.md).

## <a name="modify-the-launch-activation-handler"></a>Измените обработчик события активации запуска


При запуске приложения система передает информацию об экране-заставке обработчику событий активации запуска приложения. Эту информацию можно использовать, чтобы правильно расположить изображение на странице расширенного экрана-заставки. Можно получить эту информацию экрана заставки из активации аргументы события, которые передаются в приложение [OnLaunched](https://docs.microsoft.com/uwp/api/windows.ui.xaml.application.onlaunched) обработчик (см. в разделе `args` переменной в следующем коде).

Если вы уже не переопределены [OnLaunched](https://docs.microsoft.com/uwp/api/windows.ui.xaml.application.onlaunched) обработчик для своего приложения, см. в разделе [жизненный цикл приложений](app-lifecycle.md) вы научитесь обрабатывать события активации.

Добавьте в файл App.xaml.cs следующий код для создания и отображения расширенного экрана-заставки.

```cs
protected override void OnLaunched(LaunchActivatedEventArgs args)
{
    if (args.PreviousExecutionState != ApplicationExecutionState.Running)
    {
        bool loadState = (args.PreviousExecutionState == ApplicationExecutionState.Terminated);
        ExtendedSplash extendedSplash = new ExtendedSplash(args.SplashScreen, loadState);
        Window.Current.Content = extendedSplash;
    }

    Window.Current.Activate();
}
```

## <a name="complete-code"></a>Полный код

Приведенный ниже немного отличается от фрагменты, приведенные на предыдущих шагах.
-   ExtendedSplash.xaml включает кнопку `DismissSplash`. При нажатии этой кнопки обработчик событий `DismissSplashButton_Click` вызывает метод `DismissExtendedSplash`. В приложении вызовите `DismissExtendedSplash`, когда приложение закончит загрузку ресурсов или инициализацию пользовательского интерфейса.
-   Это приложение также использует шаблон проекта приложения UWP, который использует [кадра](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Frame) навигации. Таким образом, в файле App.xaml.cs обработчик активации запуска ([OnLaunched](https://docs.microsoft.com/uwp/api/windows.ui.xaml.application.onlaunched)) определяет `rootFrame` и использует его для задания содержимого окна приложения.

### <a name="extendedsplashxaml"></a>ExtendedSplash.xaml

Этот пример включает `DismissSplash` кнопку, поскольку он не имеет ресурсы приложения, чтобы загрузить. В вашем приложении расширенный экран-заставка должен автоматически закрыться, когда приложение закончит загрузку ресурсов или подготовку начального пользовательского интерфейса.

```xml
<Page
    x:Class="SplashScreenExample.ExtendedSplash"
    xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
    xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
    xmlns:local="using:SplashScreenExample"
    xmlns:d="http://schemas.microsoft.com/expression/blend/2008"
    xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
    mc:Ignorable="d">

    <Grid Background="#464646">
        <Canvas>
            <Image x:Name="extendedSplashImage" Source="Assets/SplashScreen.png"/>
            <ProgressRing Name="splashProgressRing" IsActive="True" Width="20" HorizontalAlignment="Center"/>
        </Canvas>
        <StackPanel HorizontalAlignment="Center" VerticalAlignment="Bottom">
            <Button x:Name="DismissSplash" Content="Dismiss extended splash screen" HorizontalAlignment="Center" Click="DismissSplashButton_Click" />
        </StackPanel>
    </Grid>
</Page>
```

### <a name="extendedsplashxamlcs"></a>ExtendedSplash.xaml.cs

Обратите внимание, что `DismissExtendedSplash` метод вызывается из обработчика события click для `DismissSplash` кнопки. В вашем приложении кнопка `DismissSplash` не нужна. Вместо этого вызовите `DismissExtendedSplash`, когда приложение закончит загрузку ресурсов и вам потребуется перейти на его главную страницу.

```cs
using System;
using System.Collections.Generic;
using System.IO;
using System.Linq;
using System.Runtime.InteropServices.WindowsRuntime;
using Windows.Foundation;
using Windows.Foundation.Collections;
using Windows.UI.Xaml;
using Windows.UI.Xaml.Controls;
using Windows.UI.Xaml.Controls.Primitives;
using Windows.UI.Xaml.Data;
using Windows.UI.Xaml.Input;
using Windows.UI.Xaml.Media;
using Windows.UI.Xaml.Navigation;

using Windows.ApplicationModel.Activation;
using SplashScreenExample.Common;
using Windows.UI.Core;

// The Blank Page item template is documented at https://go.microsoft.com/fwlink/p/?LinkID=234238

namespace SplashScreenExample
{
    /// <summary>
    /// An empty page that can be used on its own or navigated to within a Frame.
    /// </summary>
    partial class ExtendedSplash : Page
    {
        internal Rect splashImageRect; // Rect to store splash screen image coordinates.
        private SplashScreen splash; // Variable to hold the splash screen object.
        internal bool dismissed = false; // Variable to track splash screen dismissal status.
        internal Frame rootFrame;

        public ExtendedSplash(SplashScreen splashscreen, bool loadState)
        {
            InitializeComponent();

            // Listen for window resize events to reposition the extended splash screen image accordingly.
            // This is important to ensure that the extended splash screen is formatted properly in response to snapping, unsnapping, rotation, etc...
            Window.Current.SizeChanged += new WindowSizeChangedEventHandler(ExtendedSplash_OnResize);

            splash = splashscreen;

            if (splash != null)
            {
                // Register an event handler to be executed when the splash screen has been dismissed.
                splash.Dismissed += new TypedEventHandler<SplashScreen, Object>(DismissedEventHandler);

                // Retrieve the window coordinates of the splash screen image.
                splashImageRect = splash.ImageLocation;
                PositionImage();

                // Optional: Add a progress ring to your splash screen to show users that content is loading
                PositionRing();
            }

            // Create a Frame to act as the navigation context
            rootFrame = new Frame();

            // Restore the saved session state if necessary
            RestoreState(loadState);
        }

        void RestoreState(bool loadState)
        {
            if (loadState)
            {
                // TODO: write code to load state
            }
        }

        // Position the extended splash screen image in the same location as the system splash screen image.
        void PositionImage()
        {
            extendedSplashImage.SetValue(Canvas.LeftProperty, splashImageRect.X);
            extendedSplashImage.SetValue(Canvas.TopProperty, splashImageRect.Y);
            extendedSplashImage.Height = splashImageRect.Height;
            extendedSplashImage.Width = splashImageRect.Width;

        }

        void PositionRing()
        {
            splashProgressRing.SetValue(Canvas.LeftProperty, splashImageRect.X + (splashImageRect.Width*0.5) - (splashProgressRing.Width*0.5));
            splashProgressRing.SetValue(Canvas.TopProperty, (splashImageRect.Y + splashImageRect.Height + splashImageRect.Height*0.1));
        }

        void ExtendedSplash_OnResize(Object sender, WindowSizeChangedEventArgs e)
        {
            // Safely update the extended splash screen image coordinates. This function will be fired in response to snapping, unsnapping, rotation, etc...
            if (splash != null)
            {
                // Update the coordinates of the splash screen image.
                splashImageRect = splash.ImageLocation;
                PositionImage();
                PositionRing();
            }
        }

        // Include code to be executed when the system has transitioned from the splash screen to the extended splash screen (application's first view).
        void DismissedEventHandler(SplashScreen sender, object e)
        {
            dismissed = true;

            // Complete app setup operations here...
        }

        void DismissExtendedSplash()
        {
            // Navigate to mainpage
            rootFrame.Navigate(typeof(MainPage));
            // Place the frame in the current Window
            Window.Current.Content = rootFrame;
        }

        void DismissSplashButton_Click(object sender, RoutedEventArgs e)
        {
            DismissExtendedSplash();
        }
    }
}
```

### <a name="appxamlcs"></a>App.xaml.cs

Этот проект был создан с помощью приложения UWP **пустое приложение (XAML)** шаблона проекта в Visual Studio. Оба обработчика событий, `OnNavigationFailed` и `OnSuspending`, создаются автоматически, и их не нужно менять для реализации расширенного экрана-заставки. В этом разделе меняется только `OnLaunched`.

Если вы не использовали шаблон проекта для приложения, см. шаг 4: [Измените обработчик активации запуска](#modify-the-launch-activation-handler) пример измененного `OnLaunched` , которые не используют [кадра](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Frame) навигации.

```cs
using System;
using System.Collections.Generic;
using System.IO;
using System.Linq;
using System.Runtime.InteropServices.WindowsRuntime;
using Windows.ApplicationModel;
using Windows.ApplicationModel.Activation;
using Windows.Foundation;
using Windows.Foundation.Collections;
using Windows.UI.Xaml;
using Windows.UI.Xaml.Controls;
using Windows.UI.Xaml.Controls.Primitives;
using Windows.UI.Xaml.Data;
using Windows.UI.Xaml.Input;
using Windows.UI.Xaml.Media;
using Windows.UI.Xaml.Navigation;

// The Blank Application template is documented at https://go.microsoft.com/fwlink/p/?LinkID=234227

namespace SplashScreenExample
{
    /// <summary>
    /// Provides application-specific behavior to supplement the default Application class.
    /// </summary>
    sealed partial class App : Application
    {
        /// <summary>
        /// Initializes the singleton application object.  This is the first line of authored code
        /// executed, and as such is the logical equivalent of main() or WinMain().
        /// </summary>
        public App()
        {
            Microsoft.ApplicationInsights.WindowsAppInitializer.InitializeAsync(
            Microsoft.ApplicationInsights.WindowsCollectors.Metadata |
            Microsoft.ApplicationInsights.WindowsCollectors.Session);
            this.InitializeComponent();
            this.Suspending += OnSuspending;
        }

        /// <summary>
        /// Invoked when the application is launched normally by the end user.  Other entry points
        /// will be used such as when the application is launched to open a specific file.
        /// </summary>
        /// <param name="e">Details about the launch request and process.</param>
        protected override void OnLaunched(LaunchActivatedEventArgs e)
        {
#if DEBUG
            if (System.Diagnostics.Debugger.IsAttached)
            {
                this.DebugSettings.EnableFrameRateCounter = true;
            }
#endif

            Frame rootFrame = Window.Current.Content as Frame;

            // Do not repeat app initialization when the Window already has content,
            // just ensure that the window is active
            if (rootFrame == null)
            {
                // Create a Frame to act as the navigation context and navigate to the first page
                rootFrame = new Frame();
                // Set the default language
                rootFrame.Language = Windows.Globalization.ApplicationLanguages.Languages[0];

                rootFrame.NavigationFailed += OnNavigationFailed;

                //  Display an extended splash screen if app was not previously running.
                if (e.PreviousExecutionState != ApplicationExecutionState.Running)
                {
                    bool loadState = (e.PreviousExecutionState == ApplicationExecutionState.Terminated);
                    ExtendedSplash extendedSplash = new ExtendedSplash(e.SplashScreen, loadState);
                    rootFrame.Content = extendedSplash;
                    Window.Current.Content = rootFrame;
                }
            }

            if (rootFrame.Content == null)
            {
                // When the navigation stack isn't restored navigate to the first page,
                // configuring the new page by passing required information as a navigation
                // parameter
                rootFrame.Navigate(typeof(MainPage), e.Arguments);
            }
            // Ensure the current window is active
            Window.Current.Activate();
        }

        /// <summary>
        /// Invoked when Navigation to a certain page fails
        /// </summary>
        /// <param name="sender">The Frame which failed navigation</param>
        /// <param name="e">Details about the navigation failure</param>
        void OnNavigationFailed(object sender, NavigationFailedEventArgs e)
        {
            throw new Exception("Failed to load Page " + e.SourcePageType.FullName);
        }

        /// <summary>
        /// Invoked when application execution is being suspended.  Application state is saved
        /// without knowing whether the application will be terminated or resumed with the contents
        /// of memory still intact.
        /// </summary>
        /// <param name="sender">The source of the suspend request.</param>
        /// <param name="e">Details about the suspend request.</param>
        private void OnSuspending(object sender, SuspendingEventArgs e)
        {
            var deferral = e.SuspendingOperation.GetDeferral();
            // TODO: Save applicaiton state and stop any background activity
            deferral.Complete();
        }
    }
}
```

## <a name="related-topics"></a>См. также


* [Жизненный цикл приложения](app-lifecycle.md)

**Reference**

* [Пространство имен Windows.ApplicationModel.Activation](https://docs.microsoft.com/uwp/api/Windows.ApplicationModel.Activation)
* [Класс Windows.ApplicationModel.Activation.SplashScreen](https://docs.microsoft.com/uwp/api/Windows.ApplicationModel.Activation.SplashScreen)
* [Свойство Windows.ApplicationModel.Activation.SplashScreen.ImageLocation](https://docs.microsoft.com/uwp/api/windows.applicationmodel.activation.splashscreen.imagelocation)
* [Событие Windows.ApplicationModel.Core.CoreApplicationView.Activated](https://docs.microsoft.com/uwp/api/windows.applicationmodel.core.coreapplicationview.activated)

 

 

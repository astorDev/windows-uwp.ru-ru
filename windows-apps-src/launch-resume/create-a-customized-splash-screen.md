---
author: TylerMSFT
title: Более продолжительное отображение экрана-заставки
description: Увеличьте длительность отображения экрана-заставки, создав и использовав расширенный экран-заставку для приложения. Этот расширенный экран имитирует экран-заставку, отображаемый при запуске приложения, но его можно настраивать.
ms.assetid: CD3053EB-7F86-4D74-9C5A-950303791AE3
ms.author: twhitney
ms.date: 02/08/2017
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: 80242b95e64f0d642df0284c94455d60825f6daf
ms.sourcegitcommit: 70ab58b88d248de2332096b20dbd6a4643d137a4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/02/2018
ms.locfileid: "5936968"
---
# <a name="display-a-splash-screen-for-more-time"></a>Более продолжительное отображение экрана-заставки




**Важные API**

-   [**Класс SplashScreen**](https://msdn.microsoft.com/library/windows/apps/br224763)
-   [**Событие Window.SizeChanged**](https://msdn.microsoft.com/library/windows/apps/br209055)
-   [**Метод Application.OnLaunched**](https://msdn.microsoft.com/library/windows/apps/br242335)

Увеличьте длительность отображения экрана-заставки, создав и использовав расширенный экран-заставку для приложения. Этот расширенный экран имитирует экран-заставку, отображаемый при запуске приложения, но его можно настраивать. Если вы хотите показывать информацию о загрузке в реальном времени или дать приложению дополнительное время на подготовку начального пользовательского интерфейса, расширенный экран-заставка позволит определить взаимодействие с пользователем при запуске.

> **Примечание**фразу «расширенного экрана-заставки» в этом разделе относится экран-заставку, который остается на экране более продолжительное время. Он не представляет подкласс, производный от класса [**SplashScreen**](https://msdn.microsoft.com/library/windows/apps/br224763).

 

Убедитесь, что ваш расширенный экран-заставка точно имитирует экран-заставку, отображаемый по умолчанию. Для этого следуйте приведенным ниже рекомендациям.

-   Страница расширенного экрана-заставки должна использовать изображение размером 620x300пикселей, соответствующее изображению, которое указано для экрана-заставки в манифесте приложения (изображение экрана-заставки вашего приложения). В Microsoft Visual Studio2015 параметры экрана-заставки хранятся в разделе **Экрана-заставки** вкладке **Visual Assets** в манифесте приложения (файл Package.appxmanifest).
-   Расширенный экран-заставка должен использовать цвет фона, соответствующий цвету фона, который указан для экрана-заставки в манифесте приложения (фон экрана-заставки вашего приложения).
-   Чтобы расположить изображение расширенного экрана-заставки вашего приложения в тех экранных координатах, в которых располагается экран-заставка, отображаемый по умолчанию, в коде необходимо использовать класс [**SplashScreen**](https://msdn.microsoft.com/library/windows/apps/br224763).
-   Код должен реагировать на события изменения размера окна (например, при повороте экрана или при размещении вашего приложения рядом с другим приложением, открытым на экране), изменяя положение элементов на расширенном экране-заставке при помощи класса [**SplashScreen**](https://msdn.microsoft.com/library/windows/apps/br224763).

Чтобы создать расширенный экран-заставку, имитирующий экран-заставку, который отображается по умолчанию, используйте процедуру, описанную ниже.

## <a name="add-a-blank-page-item-to-your-existing-app"></a>Добавление элемента **Пустая страница** в существующее приложение


В этой статье подразумевается, что вы хотите добавить расширенный экран-заставку в существующий проект приложения универсальной платформы Windows (UWP) на C#, Visual Basic или C++.

-   Откройте приложение в Visual Studio2015.
-   Откройте пункт **Проект** в строке меню и нажмите кнопку **Добавить новый элемент**. Откроется диалоговое окно **Добавление нового элемента**.
-   В этом диалоговом окне добавьте новую **пустую страницу** в приложение. В этом разделе страница расширенного экрана-заставки называется ExtendedSplash.

При добавлении элемента **пустая страница** создаются два файла: один для разметки (ExtendedSplash.xaml) и второй для кода (ExtendedSplash.xaml.cs).

## <a name="essential-xaml-for-an-extended-splash-screen"></a>Базовый код XAML для расширенного экрана-заставки


Выполните эти действия, чтобы добавить изображение и элемент управления «Ход выполнения» на расширенный экран-заставку.

В файле ExtendedSplash.xaml:

-   Измените свойство [**Background**](https://msdn.microsoft.com/library/windows/apps/br209396) элемента по умолчанию [**Grid**](https://msdn.microsoft.com/library/windows/apps/br242704) в соответствии с цветом фона, настроенным для экрана-заставки вашего приложения в его манифесте (в разделе **Визуальные активы** файла Package.appxmanifest). По умолчанию для экрана-заставки используется светло-серый цвет (шестнадцатеричное значение \#464646). Обратите внимание, что этот элемент **Grid** предоставляется по умолчанию при создании новой **пустой страницы**. Не нужно обязательно использовать **Grid**,— это просто удобная основа для создания расширенного экрана-заставки.
-   Добавьте элемент [**Canvas**](https://msdn.microsoft.com/library/windows/apps/br209267) в [**Grid**](https://msdn.microsoft.com/library/windows/apps/br242704). **Canvas** используется для размещения изображения расширенного экрана-заставки.
-   Добавьте элемент [**Image**](https://msdn.microsoft.com/library/windows/apps/br242752) в [**Canvas**](https://msdn.microsoft.com/library/windows/apps/br209267). Используйте для расширенного экрана-заставки то же изображение размером 600x320пикселей, которое вы выбрали для экрана-заставки, отображаемого по умолчанию.
-   (Необязательно) Добавьте элемент управления «Ход выполнения», который покажет пользователям, что ваше приложение загружается. В этом разделе добавляется элемент [**ProgressRing**](https://msdn.microsoft.com/library/windows/apps/br227538) вместо определенного или неопределенного элемента [**ProgressBar**](https://msdn.microsoft.com/library/windows/apps/br227529).

Добавьте следующий код для определения элементов [**Canvas**](https://msdn.microsoft.com/library/windows/apps/br209267) и [**Image**](https://msdn.microsoft.com/library/windows/apps/br242752), а также элемента управления [**ProgressRing**](https://msdn.microsoft.com/library/windows/apps/br227538) в файле ExtendedSplash.xaml:

```xml
    <Grid Background="#464646">
        <Canvas>
            <Image x:Name="extendedSplashImage" Source="Assets/SplashScreen.png"/>
            <ProgressRing Name="splashProgressRing" IsActive="True" Width="20" HorizontalAlignment="Center"></ProgressRing>
        </Canvas>
    </Grid>
```

**Примечание**этот код присваивает ширину [**ProgressRing**](https://msdn.microsoft.com/library/windows/apps/br227538) до 20 пикселей. Можно вручную задать для ширины значение, подходящее для вашего приложения, но элемент управления не будет выполнять отрисовку при ширине меньше 20пикселей.

 

## <a name="essential-code-for-an-extended-splash-screen-class"></a>Базовый код для класса расширенного экрана-заставки


Расширенный экран-заставка должен реагировать на любые изменения размера (только Windows) или ориентации окна. Необходимо обновлять положение используемого изображения таким образом, чтобы расширенный экран-заставка смотрелся хорошо независимо от изменения размера окна.

Используйте эту процедуру, чтобы определить методы правильного отображения расширенного экрана-заставки.

1.  **Добавление обязательных пространств имен**

    Вам потребуется добавить следующие пространства имен в файл ExtendedSplash.xaml.cs для получения доступа к классу [**SplashScreen**](https://msdn.microsoft.com/library/windows/apps/br224763), событиям [**Window.SizeChanged**](https://msdn.microsoft.com/library/windows/apps/br209055).

    ```cs
    using Windows.ApplicationModel.Activation;
    using Windows.UI.Core;
    ```

2.  **Создайте разделяемый класс и объявите его переменные**

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

    Эти переменные класса используются несколькими методами. В переменной `splashImageRect` хранятся координаты, в которых система показывает изображение экрана-заставки для приложения. В переменной `splash` хранится объект [**SplashScreen**](https://msdn.microsoft.com/library/windows/apps/br224763), а переменная `dismissed` отслеживает, был ли закрыт экран-заставка, отображаемый системой.

3.  **Определите конструктор для своего класса, который правильно размещает изображение**

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

    Необходимо зарегистрировать обработчик [**Window.SizeChanged**](https://msdn.microsoft.com/library/windows/apps/br209055) (`ExtendedSplash_OnResize` в этом примере) в конструкторе класса, чтобы приложение правильно размещало изображение на расширенном экране-заставке.

4.  **Определите метод класса, чтобы расположить изображение на расширенном экране-заставке**

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

5.  **(Необязательно) Определение метода класса, чтобы расположить элемент управления «Ход выполнения» на расширенном экране-заставке**

    Если вы решили добавить элемент [**ProgressRing**](https://msdn.microsoft.com/library/windows/apps/br227538) на расширенный экран-заставку, определите его положение относительно изображения. Добавьте следующий код в файл ExtendedSplash.xaml.cs, чтобы расположить **ProgressRing** по центру на 32пикселя ниже изображения.

    ```cs
    void PositionRing()
    {
        splashProgressRing.SetValue(Canvas.LeftProperty, splashImageRect.X + (splashImageRect.Width*0.5) - (splashProgressRing.Width*0.5));
        splashProgressRing.SetValue(Canvas.TopProperty, (splashImageRect.Y + splashImageRect.Height + splashImageRect.Height*0.1));
    }
    ```

6.  **Внутри класса определите обработчик события Dismissed**

    В файле ExtendedSplash.xaml.cs определите реакцию на событие [**SplashScreen.Dismissed**](https://msdn.microsoft.com/library/windows/apps/br224764), задав для переменной класса `dismissed` значение true. Если ваше приложение предусматривает операции настройки, добавьте их в этот обработчик событий.

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

7.  **Внутри класса определите обработчик события Window.SizeChanged**

    Подготовьте расширенный экран-заставку к изменению положения его элементов, если пользователь изменит размер окна. Этот код определяет реакцию на событие [**Window.SizeChanged**](https://msdn.microsoft.com/library/windows/apps/br209055), фиксируя новые координаты и изменяя расположение изображения. Если вы добавили на экран-заставку элемент управления «Ход выполнения», также измените его положение внутри этого обработчика событий.

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

    **Примечание**перед попыткой получить данные о расположении изображения убедитесь, что переменная класса (`splash`) содержит допустимый объект [**экран-заставка**](https://msdn.microsoft.com/library/windows/apps/br224763) , как показано в примере.

     

8.  **(Необязательно) Добавьте метода класса для восстановления сохраненного состояния сеанса**

    В результате выполнения кода, добавленного в метод [**OnLaunched**](https://msdn.microsoft.com/library/windows/apps/br242335) в разделе «Шаг 4. [Изменение обработчика события активации запуска](#modify-the-launch-activation-handler)», при запуске приложение отображает расширенный экран-заставку. Чтобы консолидировать все методы, связанные с запуском приложения, в классе расширенного экрана-заставки, можно добавить следующий асинхронный метод в файл ExtendedSplash.xaml.cs для восстановления состояния приложения.

    ```cs
    async void RestoreStateAsync(bool loadState)
    {
        if (loadState)
        {
             // code to load your app's state here
        }
    }
    ```

    При изменении обработчика активации запуска в файле App.xaml.cs также задайте для `loadstate` значение true, если предыдущее состояние [**ApplicationExecutionState**](https://msdn.microsoft.com/library/windows/apps/br224694) приложения было **Terminated**. В этом случае метод `RestoreStateAsync` восстанавливает предыдущее состояние приложения. Обзор запуска, приостановки и завершения приложения см. в статье [Жизненный цикл приложения](app-lifecycle.md).

## <a name="modify-the-launch-activation-handler"></a>Измените обработчик события активации запуска


При запуске приложения система передает информацию об экране-заставке обработчику событий активации запуска приложения. Эту информацию можно использовать, чтобы правильно расположить изображение на странице расширенного экрана-заставки. Такую информацию об экране-заставке можно получить из аргументов события активации, которые передаются обработчику события [**OnLaunched**](https://msdn.microsoft.com/library/windows/apps/br242335) приложения (см. переменную `args` в следующем коде).

Если вы еще не переопределили обработчик [**OnLaunched**](https://msdn.microsoft.com/library/windows/apps/br242335) для своего приложения, обратитесь к статье [Жизненный цикл приложения](app-lifecycle.md), чтобы узнать, как обрабатывать события активации.

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


> **Примечание**следующий код немного отличается от фрагментов, приведенных в предыдущих шагах.
-   ExtendedSplash.xaml включает кнопку `DismissSplash`. При нажатии этой кнопки обработчик событий `DismissSplashButton_Click` вызывает метод `DismissExtendedSplash`. В приложении вызовите `DismissExtendedSplash`, когда приложение закончит загрузку ресурсов или инициализацию пользовательского интерфейса.
-   В этом приложении также используется шаблон приложения UWP с навигацией [**Frame**](https://msdn.microsoft.com/library/windows/apps/br242682). В результате в файле App.xaml.cs обработчик активации запуска ([**OnLaunched**](https://msdn.microsoft.com/library/windows/apps/br242335)) определяет элемент `rootFrame` и использует его для настройки содержимого окна приложения.

ExtendedSplash.xaml: этот пример включает кнопку `DismissSplash`, так как у приложения нет загружаемых ресурсов. В вашем приложении расширенный экран-заставка должен автоматически закрыться, когда приложение закончит загрузку ресурсов или подготовку начального пользовательского интерфейса.

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

ExtendedSplash.xaml.cs: обратите внимание, что метод `DismissExtendedSplash` вызывается из обработчика события нажатия кнопки `DismissSplash`. В вашем приложении кнопка `DismissSplash` не нужна. Вместо этого вызовите `DismissExtendedSplash`, когда приложение закончит загрузку ресурсов и вам потребуется перейти на его главную страницу.

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

// The Blank Page item template is documented at http://go.microsoft.com/fwlink/p/?LinkID=234238

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
            await RestoreStateAsync(loadState);
        }

        async void RestoreStateAsync(bool loadState)
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

App.XAML.cs: Этот проект создан с помощью шаблона проекта **Пустое приложение (XAML)** приложения UWP в Visual Studio2015. Оба обработчика событий, `OnNavigationFailed` и `OnSuspending`, создаются автоматически, и их не нужно менять для реализации расширенного экрана-заставки. В этом разделе меняется только `OnLaunched`.

Если вы не использовали шаблон проекта для своего приложения, посмотрите в разделе «Шаг 4. [Изменение обработчика активации запуска](#modify-the-launch-activation-handler)» пример измененного обработчика `OnLaunched`, который не использует навигацию [**Frame**](https://msdn.microsoft.com/library/windows/apps/br242682).

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

// The Blank Application template is documented at http://go.microsoft.com/fwlink/p/?LinkID=234227

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

## <a name="related-topics"></a>Ссылки по теме


* [Жизненный цикл приложения](app-lifecycle.md)

**Справочник**

* [**Пространство имен Windows.ApplicationModel.Activation**](https://msdn.microsoft.com/library/windows/apps/br224766)
* [**Класс Windows.ApplicationModel.Activation.SplashScreen**](https://msdn.microsoft.com/library/windows/apps/br224763)
* [**Свойство Windows.ApplicationModel.Activation.SplashScreen.ImageLocation**](https://msdn.microsoft.com/library/windows/apps/br224765)
* [**Событие Windows.ApplicationModel.Core.CoreApplicationView.Activated**](https://msdn.microsoft.com/library/windows/apps/br225018)

 

 

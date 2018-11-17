---
author: muhsinking
ms.assetid: 454953E1-DD8F-44B7-A614-7BAD8C683536
title: Использование гирометра
description: Узнайте, как использовать гирометр для отслеживания изменений в движении пользователя.
ms.author: mukin
ms.date: 06/06/2017
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: 84c27e35e7e9cc3763a4d1d86348da14f3a18910
ms.sourcegitcommit: 3257416aebb5a7b1515e107866806f8bd57845a8
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/17/2018
ms.locfileid: "7153383"
---
# <a name="use-the-gyrometer"></a>Использование гирометра


**Важные API**

-   [**Windows.Devices.Sensors**](https://msdn.microsoft.com/library/windows/apps/BR206408)
-   [**Gyrometer (гирометр)**](https://msdn.microsoft.com/library/windows/apps/BR225718)

**Пример**

-   Более полные сведения о внедрении см. в [примере с гирометром](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/gyrometer).

Узнайте, как использовать гирометр для отслеживания изменений в движениях пользователя.

Гирометры используются как игровые контроллеры в дополнение к акселерометрам. Акселерометр измеряет линейное перемещение, тогда как гирометр измеряет угловую скорость или вращательное движение.

## <a name="prerequisites"></a>Необходимые условия

Вы должны быть знакомы с разметки языка XAML (Extensible Application), Microsoft VisualC # и событиями.

Используемые вами устройство или эмулятор должны поддерживать гирометр.

## <a name="create-a-simple-gyrometer-app"></a>Создание простого приложения гирометра

Данный раздел состоит из двух подразделов. В первом подразделе описаны необходимые этапы создания простого приложения гирометра с нуля. В следующем подразделе описано приложение, которое вы только что создали.

###  <a name="instructions"></a>Инструкции

-   Для создания нового проекта выберите **Пустое приложение (универсальное приложение Windows)** из шаблонов проектов **Visual C#**.

-   Откройте файл проекта MainPage.xaml.cs и замените существующий код следующим.

```csharp
    using System;
    using System.Collections.Generic;
    using System.IO;
    using System.Linq;
    using Windows.Foundation;
    using Windows.Foundation.Collections;
    using Windows.UI.Xaml;
    using Windows.UI.Xaml.Controls;
    using Windows.UI.Xaml.Controls.Primitives;
    using Windows.UI.Xaml.Data;
    using Windows.UI.Xaml.Input;
    using Windows.UI.Xaml.Media;
    using Windows.UI.Xaml.Navigation;

    using Windows.UI.Core; // Required to access the core dispatcher object
    using Windows.Devices.Sensors; // Required to access the sensor platform and the gyrometer


    namespace App1
    {
        /// <summary>
        /// An empty page that can be used on its own or navigated to within a Frame.
        /// </summary>
        public sealed partial class MainPage : Page
        {
            private Gyrometer _gyrometer; // Our app' s gyrometer object

            // This event handler writes the current gyrometer reading to
            // the three textblocks on the app' s main page.

            private async void ReadingChanged(object sender, GyrometerReadingChangedEventArgs e)
            {
                await Dispatcher.RunAsync(CoreDispatcherPriority.Normal, () =>
                {
                    GyrometerReading reading = e.Reading;
                    txtXAxis.Text = String.Format("{0,5:0.00}", reading.AngularVelocityX);
                    txtYAxis.Text = String.Format("{0,5:0.00}", reading.AngularVelocityY);
                    txtZAxis.Text = String.Format("{0,5:0.00}", reading.AngularVelocityZ);
                });
            }

            public MainPage()
            {
                this.InitializeComponent();
                _gyrometer = Gyrometer.GetDefault(); // Get the default gyrometer sensor object

                if (_gyrometer != null)
                {
                    // Establish the report interval for all scenarios
                    uint minReportInterval = _gyrometer.MinimumReportInterval;
                    uint reportInterval = minReportInterval > 16 ? minReportInterval : 16;
                    _gyrometer.ReportInterval = reportInterval;

                    // Assign an event handler for the gyrometer reading-changed event
                    _gyrometer.ReadingChanged += new TypedEventHandler<Gyrometer, GyrometerReadingChangedEventArgs>(ReadingChanged);
                }

            }
        }
    }
```

Имя пространства имен в предыдущем фрагменте нужно заменить именем вашего проекта. Например, если вы создали проект под названием **GyrometerCS**, необходимо заменить `namespace App1` на `namespace GyrometerCS`.

-   Откройте файл MainPage.xaml и замените исходное содержимое следующим XML-кодом.

```xml
        <Page
        x:Class="App1.MainPage"
        xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
        xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
        xmlns:local="using:App1"
        xmlns:d="http://schemas.microsoft.com/expression/blend/2008"
        xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
        mc:Ignorable="d">

        <Grid x:Name="LayoutRoot" Background="#FF0C0C0C">
            <TextBlock HorizontalAlignment="Left" Height="23" Margin="8,8,0,0" TextWrapping="Wrap" Text="X-Axis:" VerticalAlignment="Top" Width="46" Foreground="#FFFDFDFD"/>
            <TextBlock x:Name="txtXAxis" HorizontalAlignment="Left" Height="23" Margin="67,8,0,0" TextWrapping="Wrap" VerticalAlignment="Top" Width="88" Foreground="#FFFDFAFA"/>
            <TextBlock HorizontalAlignment="Left" Height="20" Margin="8,52,0,0" TextWrapping="Wrap" Text="Y Axis:" VerticalAlignment="Top" Width="46" Foreground="White"/>
            <TextBlock x:Name="txtYAxis" HorizontalAlignment="Left" Height="24" Margin="54,48,0,0" TextWrapping="Wrap" VerticalAlignment="Top" Width="80" Foreground="#FFFBFBFB"/>
            <TextBlock HorizontalAlignment="Left" Height="21" Margin="8,93,0,0" TextWrapping="Wrap" Text="Z Axis:" VerticalAlignment="Top" Width="46" Foreground="#FFFEFBFB"/>
            <TextBlock x:Name="txtZAxis" HorizontalAlignment="Left" Height="21" Margin="54,93,0,0" TextWrapping="Wrap" VerticalAlignment="Top" Width="63" Foreground="#FFF8F3F3"/>

        </Grid>
    </Page>
```

Первую часть имени класса из предыдущего фрагмента нужно заменить на пространство имен вашего приложения. Например, если вы создали проект под названием **GyrometerCS**, необходимо заменить `x:Class="App1.MainPage"` на `x:Class="GyrometerCS.MainPage"`. Кроме того, необходимо заменить `xmlns:local="using:App1"` на `xmlns:local="using:GyrometerCS"`.

-   Нажмите клавишу F5 или выберите **Отладка** > **Начать отладку**, чтобы выполнить сборку, развернуть и запустить приложение.

После запуска приложения можно изменить значения гирометра, перемещая устройство или используя средства эмулятора.

-   Остановите приложение, вернувшись в Visual Studio и нажав клавиши Shift+F5 или выбрав **Отладка** > **Остановить отладку**, чтобы остановить приложение.

###  <a name="explanation"></a>Объяснение

Из предыдущего примера видно, какой небольшой объем кода требуется написать, чтобы включить в ваше приложение обработку входных данных гирометра.

Приложение устанавливает связь с гирометром по умолчанию в методе **MainPage**.

```csharp
_gyrometer = Gyrometer.GetDefault(); // Get the default gyrometer sensor object
```

Приложение устанавливает интервал передачи данных в методе **MainPage**. Этот код позволяет получить значение минимально допустимого для данного устройства интервала и сравнить его с требуемым интервалом в 16миллисекунд (что приблизительно соответствует частоте обновления 60Гц). Если минимально допустимый интервал больше требуемого, то код задает значение интервала, равное минимальному. В противном случае задается значение интервала, равное необходимому.

```csharp
uint minReportInterval = _gyrometer.MinimumReportInterval;
uint reportInterval = minReportInterval > 16 ? minReportInterval : 16;
_gyrometer.ReportInterval = reportInterval;
```

Новые данные от гирометра принимаются в методе **ReadingChanged**. Каждый раз, когда драйвер датчика получает от датчика новые данные, он передает их вашему приложению с помощью этого обработчика событий. Приложение регистрирует этот обработчик событий в следующей строке:

```csharp
_gyrometer.ReadingChanged += new TypedEventHandler<Gyrometer,
GyrometerReadingChangedEventArgs>(ReadingChanged);
```

Новые значения записываются в элементы TextBlock в XAML-коде проекта.

```xml
        <TextBlock HorizontalAlignment="Left" Height="23" Margin="8,8,0,0" TextWrapping="Wrap" Text="X-Axis:" VerticalAlignment="Top" Width="46" Foreground="#FFFDFDFD"/>
        <TextBlock x:Name="txtXAxis" HorizontalAlignment="Left" Height="23" Margin="67,8,0,0" TextWrapping="Wrap" VerticalAlignment="Top" Width="88" Foreground="#FFFDFAFA"/>
        <TextBlock HorizontalAlignment="Left" Height="20" Margin="8,52,0,0" TextWrapping="Wrap" Text="Y Axis:" VerticalAlignment="Top" Width="46" Foreground="White"/>
        <TextBlock x:Name="txtYAxis" HorizontalAlignment="Left" Height="24" Margin="54,48,0,0" TextWrapping="Wrap" VerticalAlignment="Top" Width="80" Foreground="#FFFBFBFB"/>
        <TextBlock HorizontalAlignment="Left" Height="21" Margin="8,93,0,0" TextWrapping="Wrap" Text="Z Axis:" VerticalAlignment="Top" Width="46" Foreground="#FFFEFBFB"/>
        <TextBlock x:Name="txtZAxis" HorizontalAlignment="Left" Height="21" Margin="54,93,0,0" TextWrapping="Wrap" VerticalAlignment="Top" Width="63" Foreground="#FFF8F3F3"/>
```

 ## <a name="related-topics"></a>Еще по теме

* [Пример работы с гирометром](http://go.microsoft.com/fwlink/p/?linkid=241379)

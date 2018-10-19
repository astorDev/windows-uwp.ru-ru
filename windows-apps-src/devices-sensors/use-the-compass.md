---
author: muhsinking
ms.assetid: 5B30E32F-27E0-4656-A834-391A559AC8BC
title: Использование компаса
description: Узнайте, как использовать компас для определения текущего направления движения.
ms.author: mukin
ms.date: 06/06/2017
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: f2bddb9ae3adf8ef6cfdf1b6c078db5eb026c93d
ms.sourcegitcommit: e16c9845b52d5bd43fc02bbe92296a9682d96926
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/19/2018
ms.locfileid: "4957814"
---
# <a name="use-the-compass"></a>Использование компаса


**Важные API**

-   [**Windows.Devices.Sensors**](https://msdn.microsoft.com/library/windows/apps/BR206408)
-   [**Compass (компас)**](https://msdn.microsoft.com/library/windows/apps/BR225705)

**Пример**

-   Более полные сведения о внедрении см. в [примере с компасом](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/Compass).

Узнайте, как с помощью компаса можно определить текущее направление движения.

Текущее направление определяется по отношению к магнитному (истинному) северу. Навигационные приложения используют компас, чтобы определить, в каком направлении обращено устройство, а затем поворачивают карту соответствующим образом.

## <a name="prerequisites"></a>Необходимые условия

Вы должны быть знакомы с языком XAML, Microsoft Visual C# и событиями.

Используемые вами устройство или эмулятор должны поддерживать компас.

## <a name="create-a-simple-compass-app"></a>Создание простого приложения компаса

Данный раздел состоит из двух подразделов. В первом подразделе описаны необходимые этапы создания простого приложения компаса с нуля. В следующем подразделе описано приложение, которое вы только что создали.

### <a name="instructions"></a>Инструкции

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
    using Windows.Devices.Sensors; // Required to access the sensor platform and the compass


    namespace App1
    {
        /// <summary>
        /// An empty page that can be used on its own or navigated to within a Frame.
        /// </summary>
        public sealed partial class MainPage : Page
        {
            private Compass _compass; // Our app' s compass object

            // This event handler writes the current compass reading to
            // the textblocks on the app' s main page.

            private async void ReadingChanged(object sender, CompassReadingChangedEventArgs e)
            {
               await Dispatcher.RunAsync(CoreDispatcherPriority.Normal, () =>
                {
                    CompassReading reading = e.Reading;
                    txtMagnetic.Text = String.Format("{0,5:0.00}", reading.HeadingMagneticNorth);
                    if (reading.HeadingTrueNorth.HasValue)
                        txtNorth.Text = String.Format("{0,5:0.00}", reading.HeadingTrueNorth);
                    else
                        txtNorth.Text = "No reading.";
                });
            }

            public MainPage()
            {
                this.InitializeComponent();
               _compass = Compass.GetDefault(); // Get the default compass object

                // Assign an event handler for the compass reading-changed event
                if (_compass != null)
                {
                    // Establish the report interval for all scenarios
                    uint minReportInterval = _compass.MinimumReportInterval;
                    uint reportInterval = minReportInterval > 16 ? minReportInterval : 16;
                    _compass.ReportInterval = reportInterval;
                    _compass.ReadingChanged += new TypedEventHandler<Compass, CompassReadingChangedEventArgs>(ReadingChanged);
                }
            }
        }
    }
    ```

You'll need to rename the namespace in the previous snippet with the name you gave your project. For example, if you created a project named **CompassCS**, you'd replace `namespace App1` with `namespace CompassCS`.

-   Open the file MainPage.xaml and replace the original contents with the following XML.

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
            <TextBlock HorizontalAlignment="Left" Height="22" Margin="8,18,0,0" TextWrapping="Wrap" Text="Magnetic Heading:" VerticalAlignment="Top" Width="104" Foreground="#FFFBF9F9"/>
            <TextBlock HorizontalAlignment="Left" Height="18" Margin="8,58,0,0" TextWrapping="Wrap" Text="True North Heading:" VerticalAlignment="Top" Width="104" Foreground="#FFF3F3F3"/>
            <TextBlock x:Name="txtMagnetic" HorizontalAlignment="Left" Height="22" Margin="130,18,0,0" TextWrapping="Wrap" Text="TextBlock" VerticalAlignment="Top" Width="116" Foreground="#FFFBF6F6"/>
            <TextBlock x:Name="txtNorth" HorizontalAlignment="Left" Height="18" Margin="130,58,0,0" TextWrapping="Wrap" Text="TextBlock" VerticalAlignment="Top" Width="116" Foreground="#FFF5F1F1"/>

         </Grid>
    </Page>
```

Первую часть имени класса из предыдущего фрагмента нужно заменить на пространство имен вашего приложения. Например, если вы создали проект под названием **CompassCS**, необходимо заменить `x:Class="App1.MainPage"` на `x:Class="CompassCS.MainPage"`. Кроме того, необходимо заменить `xmlns:local="using:App1"` на `xmlns:local="using:CompassCS"`.

-   Нажмите клавишу F5 или выберите **Отладка** > **Начать отладку**, чтобы выполнить сборку, развернуть и запустить приложение.

После запуска приложения можно изменить значения компаса, перемещая устройство или используя средства эмулятора.

-   Остановите приложение, вернувшись в Visual Studio и нажав клавиши Shift+F5 или выбрав **Отладка** > **Остановить отладку**.

### <a name="explanation"></a>Объяснение

Из предыдущего примера видно, какой небольшой объем кода требуется написать, чтобы включить в ваше приложение обработку входных данных компаса.

Приложение устанавливает связь с компасом по умолчанию в методе **MainPage**.

```csharp
_compass = Compass.GetDefault(); // Get the default compass object
```

Приложение устанавливает интервал передачи данных в методе **MainPage**. Этот код позволяет получить значение минимально допустимого для данного устройства интервала и сравнить его с требуемым интервалом в 16миллисекунд (что приблизительно соответствует частоте обновления 60Гц). Если минимально допустимый интервал больше требуемого, то код задает значение интервала, равное минимальному. В противном случае задается значение интервала, равное необходимому.

```csharp
uint minReportInterval = _compass.MinimumReportInterval;
uint reportInterval = minReportInterval > 16 ? minReportInterval : 16;
_compass.ReportInterval = reportInterval;
```

Новые данные от компаса принимаются в методе **ReadingChanged**. Каждый раз, когда драйвер датчика получает от датчика новые данные, он передает их вашему приложению с помощью этого обработчика событий. Приложение регистрирует этот обработчик событий в следующей строке:

```csharp
_compass.ReadingChanged += new TypedEventHandler<Compass,
CompassReadingChangedEventArgs>(ReadingChanged);
```

Новые значения записываются в элементы TextBlock в XAML-коде проекта.

```xml
 <TextBlock HorizontalAlignment="Left" Height="22" Margin="8,18,0,0" TextWrapping="Wrap" Text="Magnetic Heading:" VerticalAlignment="Top" Width="104" Foreground="#FFFBF9F9"/>
 <TextBlock HorizontalAlignment="Left" Height="18" Margin="8,58,0,0" TextWrapping="Wrap" Text="True North Heading:" VerticalAlignment="Top" Width="104" Foreground="#FFF3F3F3"/>
 <TextBlock x:Name="txtMagnetic" HorizontalAlignment="Left" Height="22" Margin="130,18,0,0" TextWrapping="Wrap" Text="TextBlock" VerticalAlignment="Top" Width="116" Foreground="#FFFBF6F6"/>
 <TextBlock x:Name="txtNorth" HorizontalAlignment="Left" Height="18" Margin="130,58,0,0" TextWrapping="Wrap" Text="TextBlock" VerticalAlignment="Top" Width="116" Foreground="#FFF5F1F1"/>
```
 

 

---
author: muhsinking
ms.assetid: F90686F5-641A-42D9-BC44-EC6CA11B8A42
title: Использование акселерометра
description: Узнайте, как использовать акселерометр для реагирования на движения пользователя.
ms.author: mukin
ms.date: 06/06/2017
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: 2848b9a7326bdac084120ec9b75f067718f14853
ms.sourcegitcommit: 933e71a31989f8063b020746fdd16e9da94a44c4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2018
ms.locfileid: "4541146"
---
# <a name="use-the-accelerometer"></a>Использование акселерометра


**Важные API**

-   [**Windows.Devices.Sensors**](https://msdn.microsoft.com/library/windows/apps/BR206408)
-   [**Accelerometer (акселерометр)**](https://msdn.microsoft.com/library/windows/apps/BR225687)

**Пример**

-   Более полные сведения о внедрении см. в [примере с акселерометром](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/Accelerometer).

Научитесь использовать акселерометр для реагирования на движения пользователя.

Простое игровое приложение может использовать в качестве устройства ввода единственный датчик, например акселерометр. Такие приложения обычно используют входные данные только по одной или двум осям, но могут также использовать событие встряхивания как дополнительный источник входных данных.

## <a name="prerequisites"></a>Необходимые условия

Вы должны быть знакомы с языком XAML, Microsoft Visual C# и событиями.

Используемые вами устройство или эмулятор должны поддерживать акселерометр.

## <a name="create-a-simple-accelerometer-app"></a>Создание простого приложения акселерометра

Данный раздел состоит из двух подразделов. В первом подразделе описаны необходимые этапы создания простого приложения акселерометра с нуля. В следующем подразделе описано приложение, которое вы только что создали.

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

    // Required to support the core dispatcher and the accelerometer

    using Windows.UI.Core;
    using Windows.Devices.Sensors;

    namespace App1
    {

        public sealed partial class MainPage : Page
        {
            // Sensor and dispatcher variables
            private Accelerometer _accelerometer;

            // This event handler writes the current accelerometer reading to
            // the three acceleration text blocks on the app' s main page.

            private async void ReadingChanged(object sender, AccelerometerReadingChangedEventArgs e)
            {
                await Dispatcher.RunAsync(CoreDispatcherPriority.Normal, () =>
                {
                    AccelerometerReading reading = e.Reading;
                    txtXAxis.Text = String.Format("{0,5:0.00}", reading.AccelerationX);
                    txtYAxis.Text = String.Format("{0,5:0.00}", reading.AccelerationY);
                    txtZAxis.Text = String.Format("{0,5:0.00}", reading.AccelerationZ);

                });
            }

            public MainPage()
            {
                this.InitializeComponent();
                _accelerometer = Accelerometer.GetDefault();

                if (_accelerometer != null)
                {
                    // Establish the report interval
                    uint minReportInterval = _accelerometer.MinimumReportInterval;
                    uint reportInterval = minReportInterval > 16 ? minReportInterval : 16;
                    _accelerometer.ReportInterval = reportInterval;

                    // Assign an event handler for the reading-changed event
                    _accelerometer.ReadingChanged += new TypedEventHandler<Accelerometer, AccelerometerReadingChangedEventArgs>(ReadingChanged);
                }
            }
        }
    }
```

Имя пространства имен в предыдущем фрагменте нужно заменить именем вашего проекта. Например, если вы создали проект под названием **AccelerometerCS**, необходимо заменить `namespace App1` на `namespace AccelerometerCS`.

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
            <TextBlock HorizontalAlignment="Left" Height="25" Margin="8,20,0,0" TextWrapping="Wrap" Text="X-axis:" VerticalAlignment="Top" Width="62" Foreground="#FFEDE6E6"/>
            <TextBlock HorizontalAlignment="Left" Height="27" Margin="8,49,0,0" TextWrapping="Wrap" Text="Y-axis:" VerticalAlignment="Top" Width="62" Foreground="#FFF5F2F2"/>
            <TextBlock HorizontalAlignment="Left" Height="23" Margin="8,80,0,0" TextWrapping="Wrap" Text="Z-axis:" VerticalAlignment="Top" Width="62" Foreground="#FFF6F0F0"/>
            <TextBlock x:Name="txtXAxis" HorizontalAlignment="Left" Height="15" Margin="70,16,0,0" TextWrapping="Wrap" Text="TextBlock" VerticalAlignment="Top" Width="61" Foreground="#FFF2F2F2"/>
            <TextBlock x:Name="txtYAxis" HorizontalAlignment="Left" Height="15" Margin="70,49,0,0" TextWrapping="Wrap" Text="TextBlock" VerticalAlignment="Top" Width="53" Foreground="#FFF2EEEE"/>
            <TextBlock x:Name="txtZAxis" HorizontalAlignment="Left" Height="15" Margin="70,80,0,0" TextWrapping="Wrap" Text="TextBlock" VerticalAlignment="Top" Width="53" Foreground="#FFFFF8F8"/>

        </Grid>
    </Page>
```

Первую часть имени класса из предыдущего фрагмента нужно заменить на пространство имен вашего приложения. Например, если вы создали проект под названием **AccelerometerCS**, необходимо заменить `x:Class="App1.MainPage"` на `x:Class="AccelerometerCS.MainPage"`. Замените также `xmlns:local="using:App1"` на `xmlns:local="using:AccelerometerCS"`.

-   Нажмите клавишу F5 или выберите **Отладка** &gt; **Начать отладку**, чтобы выполнить сборку, развернуть и запустить приложение.

После запуска приложения можно изменить значения акселерометра, перемещая устройство или используя средства эмулятора.

-   Остановите приложение, вернувшись в Visual Studio и нажав клавиши Shift+F5 или выбрав **Отладка** &gt; **Остановить отладку**.

### <a name="explanation"></a>Объяснение

Из предыдущего примера видно, какой небольшой объем кода требуется написать, чтобы включить в ваше приложение обработку входных данных акселерометра.

Приложение устанавливает связь с акселерометром по умолчанию в методе **MainPage**.

```csharp
_accelerometer = Accelerometer.GetDefault();
```

Приложение устанавливает интервал передачи данных в методе **MainPage**. Этот код позволяет получить значение минимально допустимого для данного устройства интервала и сравнить его с требуемым интервалом в 16миллисекунд (что приблизительно соответствует частоте обновления 60Гц). Если минимально допустимый интервал больше требуемого, то код задает значение интервала, равное минимальному. В противном случае задается значение интервала, равное необходимому.

```csharp
uint minReportInterval = _accelerometer.MinimumReportInterval;
uint reportInterval = minReportInterval > 16 ? minReportInterval : 16;
_accelerometer.ReportInterval = reportInterval;
```

Новые данные от акселерометра принимаются в методе **ReadingChanged**. Каждый раз, когда драйвер датчика получает от датчика новые данные, он передает их вашему приложению с помощью этого обработчика событий. Приложение регистрирует этот обработчик событий в следующей строке:

```csharp
_accelerometer.ReadingChanged += new TypedEventHandler<Accelerometer,
AccelerometerReadingChangedEventArgs>(ReadingChanged);
```

Новые значения записываются в элементы TextBlock в XAML-коде проекта.

```xml
<TextBlock x:Name="txtXAxis" HorizontalAlignment="Left" Height="15" Margin="70,16,0,0" TextWrapping="Wrap" Text="TextBlock" VerticalAlignment="Top" Width="61" Foreground="#FFF2F2F2"/>
 <TextBlock x:Name="txtYAxis" HorizontalAlignment="Left" Height="15" Margin="70,49,0,0" TextWrapping="Wrap" Text="TextBlock" VerticalAlignment="Top" Width="53" Foreground="#FFF2EEEE"/>
 <TextBlock x:Name="txtZAxis" HorizontalAlignment="Left" Height="15" Margin="70,80,0,0" TextWrapping="Wrap" Text="TextBlock" VerticalAlignment="Top" Width="53" Foreground="#FFFFF8F8"/>
```

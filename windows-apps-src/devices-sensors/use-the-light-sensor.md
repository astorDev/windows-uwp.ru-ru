---
author: muhsinking
ms.assetid: 15BAB25C-DA8C-4F13-9B8F-EA9E4270BCE9
title: Использование датчика освещенности
description: Узнайте, как использовать датчик освещенности для обнаружения изменений уровня освещенности.
ms.author: mukin
ms.date: 06/06/2017
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: bfb0fe768651e76d18c9c88a2dd36032be21977d
ms.sourcegitcommit: 897a111e8fc5d38d483800288ad01c523e924ef4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/13/2018
ms.locfileid: "958729"
---
# <a name="use-the-light-sensor"></a>Использование датчика освещенности


**Важные API**

-   [**Windows.Devices.Sensors**](https://msdn.microsoft.com/library/windows/apps/BR206408)
-   [**LightSensor**](https://msdn.microsoft.com/library/windows/apps/BR225790)

**Пример**

-   Более полные сведения о внедрении см. в [примере с датчиком света](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/LightSensor).

Узнайте, как использовать датчик освещенности для обнаружения изменений уровня освещенности.

Датчик освещенности— это один из типов датчиков, которые позволяют приложению реагировать на изменение условий окружающей пользователя среды.

## <a name="prerequisites"></a>Необходимые условия

Вы должны быть знакомы с языком XAML, Microsoft Visual C# и событиями.

Используемые вами устройство или эмулятор должны поддерживать датчик освещенности.

## <a name="create-a-simple-light-sensor-app"></a>Создание простого приложения датчика освещенности

Данный раздел состоит из двух подразделов. В первом подразделе описаны необходимые этапы создания простого приложения датчика освещения с нуля. В следующем подразделе описано приложение, которое вы только что создали.

###  <a name="instructions"></a>Инструкции

-   Для создания нового проекта выберите **Пустое приложение (универсальное приложение Windows)** из шаблонов проектов **Visual C#**.

-   Откройте файл BlankPage.xaml.cs вашего проекта и замените существующий код следующим:

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
    using Windows.Devices.Sensors; // Required to access the sensor platform and the ALS

    // The Blank Page item template is documented at http://go.microsoft.com/fwlink/p/?linkid=234238

    namespace App1
    {
        /// <summary>
        /// An empty page that can be used on its own or navigated to within a Frame.
        /// </summary>
        public sealed partial class BlankPage : Page
        {
            private LightSensor _lightsensor; // Our app' s lightsensor object

            // This event handler writes the current light-sensor reading to
            // the textbox named "txtLUX" on the app' s main page.

            private void ReadingChanged(object sender, LightSensorReadingChangedEventArgs e)
            {
                Dispatcher.RunAsync(CoreDispatcherPriority.Normal, (s, a) =>
                {
                    LightSensorReading reading = (a.Context as LightSensorReadingChangedEventArgs).Reading;
                    txtLuxValue.Text = String.Format("{0,5:0.00}", reading.IlluminanceInLux);
                });
            }

            public BlankPage()
            {
                InitializeComponent();
                _lightsensor = LightSensor.GetDefault(); // Get the default light sensor object

                // Assign an event handler for the ALS reading-changed event
                if (_lightsensor != null)
                {
                    // Establish the report interval for all scenarios
                    uint minReportInterval = _lightsensor.MinimumReportInterval;
                    uint reportInterval = minReportInterval > 16 ? minReportInterval : 16;
                    _lightsensor.ReportInterval = reportInterval;

                    // Establish the even thandler
                    _lightsensor.ReadingChanged += new TypedEventHandler<LightSensor, LightSensorReadingChangedEventArgs>(ReadingChanged);
                }

            }

        }
    }
```

Имя пространства имен в предыдущем фрагменте нужно заменить именем вашего проекта. Например, если вы создали проект под названием **LightingCS**, необходимо заменить `namespace App1` на `namespace LightingCS`.

-   Откройте файл MainPage.xaml и замените исходное содержимое следующим XML-кодом.

```xml
    <Page
        x:Class="App1.BlankPage"
        xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
        xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
        xmlns:local="using:App1"
        xmlns:d="http://schemas.microsoft.com/expression/blend/2008"
        xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
        mc:Ignorable="d">

        <Grid x:Name="LayoutRoot" Background="Black">
            <TextBlock HorizontalAlignment="Left" Height="44" Margin="52,38,0,0" TextWrapping="Wrap" Text="LUX Reading" VerticalAlignment="Top" Width="150"/>
            <TextBlock x:Name="txtLuxValue" HorizontalAlignment="Left" Height="44" Margin="224,38,0,0" TextWrapping="Wrap" Text="TextBlock" VerticalAlignment="Top" Width="217"/>


        </Grid>

    </Page>
```

Первую часть имени класса из предыдущего фрагмента нужно заменить на пространство имен вашего приложения. Например, если вы создали проект под названием **LightingCS**, необходимо заменить `x:Class="App1.MainPage"` на `x:Class="LightingCS.MainPage"`. Кроме того, необходимо заменить `xmlns:local="using:App1"` на `xmlns:local="using:LightingCS"`.

-   Нажмите клавишу F5 или выберите **Отладка** > **Начать отладку**, чтобы выполнить сборку, развернуть и запустить приложение.

После запуска приложения можно изменить значения датчика освещенности, меняя доступное датчику количество света или используя средства эмулятора.

-   Остановите приложение, вернувшись в Visual Studio и нажав клавиши Shift+F5 или выбрав **Отладка** > **Остановить отладку**.

###  <a name="explanation"></a>Объяснение

Из предыдущего примера видно, какой небольшой объем кода требуется написать, чтобы включить в ваше приложение обработку входных данных датчика освещения.

Приложение устанавливает связь с датчиком положения в пространстве по умолчанию в методе **BlankPage**.

```csharp
_lightsensor = LightSensor.GetDefault(); // Get the default light sensor object
```

Приложение устанавливает интервал передачи данных в методе **BlankPage**. Этот код позволяет получить значение минимально допустимого для данного устройства интервала и сравнить его с требуемым интервалом в 16миллисекунд (что приблизительно соответствует частоте обновления 60Гц). Если минимально допустимый интервал больше требуемого, то код задает значение интервала, равное минимальному. В противном случае задается значение интервала, равное необходимому.

```csharp
uint minReportInterval = _lightsensor.MinimumReportInterval;
uint reportInterval = minReportInterval > 16 ? minReportInterval : 16;
_lightsensor.ReportInterval = reportInterval;
```
Новые данные от датчика освещения принимаются в методе **ReadingChanged**. Каждый раз, когда драйвер датчика получает от датчика новые данные, он передает их вашему приложению с помощью этого обработчика событий. Приложение регистрирует этот обработчик событий в следующей строке:

```csharp
_lightsensor.ReadingChanged += new TypedEventHandler<LightSensor,
LightSensorReadingChangedEventArgs>(ReadingChanged);
```

Эти новые значения записываются в элемент TextBlock в XAML-коде проекта.

```xml
<TextBlock HorizontalAlignment="Left" Height="44" Margin="52,38,0,0" TextWrapping="Wrap" Text="LUX Reading" VerticalAlignment="Top" Width="150"/>
 <TextBlock x:Name="txtLuxValue" HorizontalAlignment="Left" Height="44" Margin="224,38,0,0" TextWrapping="Wrap" Text="TextBlock" VerticalAlignment="Top" Width="217"/>
```
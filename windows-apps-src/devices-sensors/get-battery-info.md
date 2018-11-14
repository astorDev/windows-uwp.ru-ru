---
author: muhsinking
ms.assetid: 90BB59FC-90FE-453E-A8DE-9315E29EB98C
title: Получение сведений о батарее
description: Узнайте, как получить подробные сведения об аккумуляторе с помощью API в пространстве имен Windows.Devices.Power.
ms.author: mukin
ms.date: 02/08/2017
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: c745b99104495b4d0b3c60202c378285dbfdd7b6
ms.sourcegitcommit: 4d88adfaf544a3dab05f4660e2f59bbe60311c00
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/12/2018
ms.locfileid: "6446001"
---
# <a name="get-battery-information"></a>Получение сведений об аккумуляторе


** Важные API **

-   [**Windows.Devices.Power**](https://msdn.microsoft.com/library/windows/apps/Dn895017)
-   [**DeviceInformation.FindAllAsync**](https://msdn.microsoft.com/library/windows/apps/BR225432)

Узнайте, как получить подробные сведения о батарее с помощью API в пространстве имен [**Windows.Devices.Power**](https://msdn.microsoft.com/library/windows/apps/Dn895017). В *отчете о батарее* ([**BatteryReport**](https://msdn.microsoft.com/library/windows/apps/Dn895005)) содержатся сведения о заряде, емкости и состоянии батареи или системы батарей. В этой статье рассказывается, как приложение может получать отчеты о батарее и уведомления об изменениях ее параметров. Примеры кода взяты из основного приложения батареи, приведенного в конце данной статьи.

## <a name="get-aggregate-battery-report"></a>Получение отчета о системе батарей


В некоторых устройствах используется несколько батарей. При этом не всегда очевидно, какой вклад делает каждая из них в общую энергоемкость устройства. Поэтому следует использовать класс [**AggregateBattery**](https://msdn.microsoft.com/library/windows/apps/Dn895011). *Система батарей* содержит все контроллеры батарей, подключенные к устройству. Она предоставляет один общий объект [**BatteryReport**](https://msdn.microsoft.com/library/windows/apps/Dn895005).

**Примечание** [**батареи**](https://msdn.microsoft.com/library/windows/apps/Dn895004) фактически соответствует контроллеру батареи. В зависимости от устройства контроллер может быть подключен как к физической батарее, так и к корпусу устройства. Таким образом, можно создать объект батареи даже при отсутствии батарей. В других случаях объект батареи может иметь значение **null**.

Если у вас есть объект системы батарей, то вызвав метод [**GetReport**](https://msdn.microsoft.com/library/windows/apps/windows.devices.power.battery.getreport), можно получить соответствующий класс [**BatteryReport**](https://msdn.microsoft.com/library/windows/apps/Dn895005).

```csharp
private void RequestAggregateBatteryReport()
{
    // Create aggregate battery object
    var aggBattery = Battery.AggregateBattery;

    // Get report
    var report = aggBattery.GetReport();

    // Update UI
    AddReportUI(BatteryReportPanel, report, aggBattery.DeviceId);
}
```

## <a name="get-individual-battery-reports"></a>Получение отчетов об отдельных батареях

Вы также можете создать объект [**BatteryReport**](https://msdn.microsoft.com/library/windows/apps/Dn895005) для отдельных батарей. Используйте методы [**GetDeviceSelector**](https://msdn.microsoft.com/library/windows/apps/windows.devices.power.battery.getdeviceselector.aspx) и [**FindAllAsync**](https://msdn.microsoft.com/library/windows/apps/BR225432) вместе, чтобы получить коллекцию объектов [**DeviceInformation**](https://msdn.microsoft.com/library/windows/apps/BR225393), представляющих все контроллеры батарей, подключенные к устройству. Затем, используя свойство **Id** необходимого объекта **DeviceInformation**, создайте соответствующий класс [**Battery**](https://msdn.microsoft.com/library/windows/apps/Dn895004) с помощью метода [**FromIdAsync**](https://msdn.microsoft.com/library/windows/apps/windows.devices.power.battery.fromidasync.aspx). И наконец вызовите метод [**GetReport**](https://msdn.microsoft.com/library/windows/apps/windows.devices.power.battery.getreport), чтобы получить отчет об отдельной батарее.

В этом примере показано, как создать отчет о батарее для всех батарей, подключенных к устройству.

```csharp
async private void RequestIndividualBatteryReports()
{
    // Find batteries 
    var deviceInfo = await DeviceInformation.FindAllAsync(Battery.GetDeviceSelector());
    foreach(DeviceInformation device in deviceInfo)
    {
        try
        {
        // Create battery object
        var battery = await Battery.FromIdAsync(device.Id);

        // Get report
        var report = battery.GetReport();

        // Update UI
        AddReportUI(BatteryReportPanel, report, battery.DeviceId);
        }
        catch { /* Add error handling, as applicable */ }
    }
}
```

## <a name="access-report-details"></a>Получение доступа к сведениям отчета

Объект [**BatteryReport**](https://msdn.microsoft.com/library/windows/apps/Dn895005) предоставляет большое количество сведений о батарее. Подробнее о его свойствах **Status** (перечисление [**BatteryStatus**](https://msdn.microsoft.com/library/windows/apps/Dn818458)), [**ChargeRateInMilliwatts**](https://msdn.microsoft.com/library/windows/apps/windows.devices.power.batteryreport.chargerateinmilliwatts.aspx), [**DesignCapacityInMilliwattHours**](https://msdn.microsoft.com/library/windows/apps/windows.devices.power.batteryreport.designcapacityinmilliwatthours.aspx), [**FullChargeCapacityInMilliwattHours**](https://msdn.microsoft.com/library/windows/apps/windows.devices.power.batteryreport.fullchargecapacityinmilliwatthours.aspx) и [**RemainingCapacityInMilliwattHours**](https://msdn.microsoft.com/library/windows/apps/windows.devices.power.batteryreport.remainingcapacityinmilliwatthours) см. в справочнике по API. В этом примере показаны некоторые свойства отчета о батарее, используемые основным приложением батареи, которое описано далее в этой статье.

```csharp
...
TextBlock txt3 = new TextBlock { Text = "Charge rate (mW): " + report.ChargeRateInMilliwatts.ToString() };
TextBlock txt4 = new TextBlock { Text = "Design energy capacity (mWh): " + report.DesignCapacityInMilliwattHours.ToString() };
TextBlock txt5 = new TextBlock { Text = "Fully-charged energy capacity (mWh): " + report.FullChargeCapacityInMilliwattHours.ToString() };
TextBlock txt6 = new TextBlock { Text = "Remaining energy capacity (mWh): " + report.RemainingCapacityInMilliwattHours.ToString() };
...
...
```

## <a name="request-report-updates"></a>Запрос обновлений отчета

При изменении заряда, емкости или состояния батареи объект [**Battery**](https://msdn.microsoft.com/library/windows/apps/Dn895004) создает событие [**ReportUpdated**](https://msdn.microsoft.com/library/windows/apps/windows.devices.power.battery.reportupdated). Обычно это происходит немедленно при изменениях состояния и периодически— при всех других изменениях. В этом примере показано, как зарегистрироваться для получения обновлений отчета о батарее.

```csharp
...
Battery.AggregateBattery.ReportUpdated += AggregateBattery_ReportUpdated;
...
```

## <a name="handle-report-updates"></a>Обработка обновлений отчета

При обновлении сведений о батарее событие [**ReportUpdated**](https://msdn.microsoft.com/library/windows/apps/windows.devices.power.battery.reportupdated) передает соответствующий объект [**Battery**](https://msdn.microsoft.com/library/windows/apps/Dn895004) в метод обработчика события. Но этот обработчик события не вызывается из потока пользовательского интерфейса. Чтобы вызвать любые изменения пользовательского интерфейса, вам придется использовать объект [**Dispatcher**](https://msdn.microsoft.com/library/windows/apps/BR208211), как показано в этом примере.

```csharp
async private void AggregateBattery_ReportUpdated(Battery sender, object args)
{
    if (reportRequested)
    {

        await Dispatcher.RunAsync(CoreDispatcherPriority.Normal, () =>
        {
            // Clear UI
            BatteryReportPanel.Children.Clear();


            if (AggregateButton.IsChecked == true)
            {
                // Request aggregate battery report
                RequestAggregateBatteryReport();
            }
            else
            {
                // Request individual battery report
                RequestIndividualBatteryReports();
            }
        });
    }
}
```

## <a name="example-basic-battery-app"></a>Пример: основное приложение для работы с батареей

Попробуйте использовать эти API, создав указанное ниже основное приложение батареи в MicrosoftVisualStudio. На начальной странице Visual Studio щелкните **Новый проект**, а затем в разделе шаблонов **Visual C# &gt; Windows &gt; Universal** создайте приложение на базе шаблона **Пустое приложение**.

Затем откройте файл **MainPage.xaml** и скопируйте в него указанный ниже XML-код (заменив исходное содержимое файла).

```xml
<Page
    x:Class="App1.MainPage"
    xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
    xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
    xmlns:local="using:App1"
    xmlns:d="http://schemas.microsoft.com/expression/blend/2008"
    xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
    mc:Ignorable="d">

    <StackPanel Background="{ThemeResource ApplicationPageBackgroundThemeBrush}" >
        <StackPanel VerticalAlignment="Center" Margin="15,30,0,0" >
            <RadioButton x:Name="AggregateButton" Content="Aggregate results" GroupName="Type" IsChecked="True" />
            <RadioButton x:Name="IndividualButton" Content="Individual results" GroupName="Type" IsChecked="False" />
        </StackPanel>
        <StackPanel Orientation="Horizontal">
        <Button x:Name="GetBatteryReportButton" 
                Content="Get battery report" 
                Margin="15,15,0,0" 
                Click="GetBatteryReport"/>
        </StackPanel>
        <StackPanel x:Name="BatteryReportPanel" Margin="15,15,0,0"/>
    </StackPanel>
</Page>
```

Если ваше приложение называется не **App1**, первую часть имени класса из предыдущего фрагмента нужно заменить на пространство имен вашего приложения. Например, если вы создали проект с именем **BasicBatteryApp**, замените `x:Class="App1.MainPage"` на `x:Class="BasicBatteryApp.MainPage"`. Кроме того, необходимо заменить `xmlns:local="using:App1"` на `xmlns:local="using:BasicBatteryApp"`.

После этого откройте файл **MainPage.xaml.cs** своего проекта и замените существующий код указанным ниже.

```csharp
using System;
using Windows.UI.Xaml;
using Windows.UI.Xaml.Controls;
using Windows.UI.Xaml.Media;
using Windows.Devices.Enumeration;
using Windows.Devices.Power;
using Windows.UI.Core;

namespace App1
{
    public sealed partial class MainPage : Page
    {
        bool reportRequested = false;
        public MainPage()
        {
            this.InitializeComponent();
            Battery.AggregateBattery.ReportUpdated += AggregateBattery_ReportUpdated;
        }


        private void GetBatteryReport(object sender, RoutedEventArgs e)
        {
            // Clear UI
            BatteryReportPanel.Children.Clear();


            if (AggregateButton.IsChecked == true)
            {
                // Request aggregate battery report
                RequestAggregateBatteryReport();
            }
            else
            {
                // Request individual battery report
                RequestIndividualBatteryReports();
            }

            // Note request
            reportRequested = true;
        }

        private void RequestAggregateBatteryReport()
        {
            // Create aggregate battery object
            var aggBattery = Battery.AggregateBattery;

            // Get report
            var report = aggBattery.GetReport();

            // Update UI
            AddReportUI(BatteryReportPanel, report, aggBattery.DeviceId);
        }

        async private void RequestIndividualBatteryReports()
        {
            // Find batteries 
            var deviceInfo = await DeviceInformation.FindAllAsync(Battery.GetDeviceSelector());
            foreach(DeviceInformation device in deviceInfo)
            {
                try
                {
                // Create battery object
                var battery = await Battery.FromIdAsync(device.Id);

                // Get report
                var report = battery.GetReport();

                // Update UI
                AddReportUI(BatteryReportPanel, report, battery.DeviceId);
                }
                catch { /* Add error handling, as applicable */ }
            }
        }


        private void AddReportUI(StackPanel sp, BatteryReport report, string DeviceID)
        {
            // Create battery report UI
            TextBlock txt1 = new TextBlock { Text = "Device ID: " + DeviceID };
            txt1.FontSize = 15;
            txt1.Margin = new Thickness(0, 15, 0, 0);
            txt1.TextWrapping = TextWrapping.WrapWholeWords;

            TextBlock txt2 = new TextBlock { Text = "Battery status: " + report.Status.ToString() };
            txt2.FontStyle = Windows.UI.Text.FontStyle.Italic;
            txt2.Margin = new Thickness(0, 0, 0, 15);

            TextBlock txt3 = new TextBlock { Text = "Charge rate (mW): " + report.ChargeRateInMilliwatts.ToString() };
            TextBlock txt4 = new TextBlock { Text = "Design energy capacity (mWh): " + report.DesignCapacityInMilliwattHours.ToString() };
            TextBlock txt5 = new TextBlock { Text = "Fully-charged energy capacity (mWh): " + report.FullChargeCapacityInMilliwattHours.ToString() };
            TextBlock txt6 = new TextBlock { Text = "Remaining energy capacity (mWh): " + report.RemainingCapacityInMilliwattHours.ToString() };

            // Create energy capacity progress bar & labels
            TextBlock pbLabel = new TextBlock { Text = "Percent remaining energy capacity" };
            pbLabel.Margin = new Thickness(0,10, 0, 5);
            pbLabel.FontFamily = new FontFamily("Segoe UI");
            pbLabel.FontSize = 11;

            ProgressBar pb = new ProgressBar();
            pb.Margin = new Thickness(0, 5, 0, 0);
            pb.Width = 200;
            pb.Height = 10;
            pb.IsIndeterminate = false;
            pb.HorizontalAlignment = HorizontalAlignment.Left;

            TextBlock pbPercent = new TextBlock();
            pbPercent.Margin = new Thickness(0, 5, 0, 10);
            pbPercent.FontFamily = new FontFamily("Segoe UI");
            pbLabel.FontSize = 11;

            // Disable progress bar if values are null
            if ((report.FullChargeCapacityInMilliwattHours == null)||
                (report.RemainingCapacityInMilliwattHours == null))
            {
                pb.IsEnabled = false;
                pbPercent.Text = "N/A";
            }
            else
            {
                pb.IsEnabled = true;
                pb.Maximum = Convert.ToDouble(report.FullChargeCapacityInMilliwattHours);
                pb.Value = Convert.ToDouble(report.RemainingCapacityInMilliwattHours);
                pbPercent.Text = ((pb.Value / pb.Maximum) * 100).ToString("F2") + "%";
            }

            // Add controls to stackpanel
            sp.Children.Add(txt1);
            sp.Children.Add(txt2);
            sp.Children.Add(txt3);
            sp.Children.Add(txt4);
            sp.Children.Add(txt5);
            sp.Children.Add(txt6);
            sp.Children.Add(pbLabel);
            sp.Children.Add(pb);
            sp.Children.Add(pbPercent);
        }

        async private void AggregateBattery_ReportUpdated(Battery sender, object args)
        {
            if (reportRequested)
            {

                await Dispatcher.RunAsync(CoreDispatcherPriority.Normal, () =>
                {
                    // Clear UI
                    BatteryReportPanel.Children.Clear();


                    if (AggregateButton.IsChecked == true)
                    {
                        // Request aggregate battery report
                        RequestAggregateBatteryReport();
                    }
                    else
                    {
                        // Request individual battery report
                        RequestIndividualBatteryReports();
                    }
                });
            }
        }
    }
}
```

Если ваше приложение называется не **App1**, вам потребуется переименовать пространство имен в предыдущем примере, используя имя своего проекта. Например, если вы создали проект с именем **BasicBatteryApp**, необходимо заменить пространство имен `App1` пространством имен `BasicBatteryApp`.

И наконец для запуска основного приложения батареи сделайте следующее: в меню **Отладка** выберите пункт **Начать отладку**, чтобы протестировать решение.

**Совет**Чтобы получить числовые значения из объекта [**BatteryReport**](https://msdn.microsoft.com/library/windows/apps/Dn895005) , выполнить отладку приложения на **Локальном компьютере** или внешнее **устройство** (например, Windows Phone). При отладке в эмуляторе устройства для свойств емкости и уровня заряда объект **BatteryReport** возвращает значение **null**.

 


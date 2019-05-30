---
ms.assetid: B4A550E7-1639-4C9A-A229-31E22B1415E7
title: Положение датчиков в пространстве
description: Данные датчиков классов Accelerometer, Gyrometer, Compass, Inclinometer и OrientationSensor определяются их опорными осями. Эти оси определяются альбомной ориентацией устройства и поворачиваются вместе с ним.
ms.date: 05/24/2017
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: 8e75ab94c6f1c8c4560854fd4f5264c313657ba9
ms.sourcegitcommit: ac7f3422f8d83618f9b6b5615a37f8e5c115b3c4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/29/2019
ms.locfileid: "66369892"
---
# <a name="sensor-orientation"></a>Положение датчиков в пространстве


**Важные API**

-   [**Windows.Devices.Sensors**](https://docs.microsoft.com/uwp/api/Windows.Devices.Sensors)
-   [**Windows.Devices.Sensors.Custom**](https://docs.microsoft.com/uwp/api/Windows.Devices.Sensors.Custom)

Данные датчиков классов [**Accelerometer**](https://docs.microsoft.com/uwp/api/Windows.Devices.Sensors.Accelerometer), [**Gyrometer**](https://docs.microsoft.com/uwp/api/Windows.Devices.Sensors.Gyrometer), [**Compass**](https://docs.microsoft.com/uwp/api/Windows.Devices.Sensors.Compass), [**Inclinometer**](https://docs.microsoft.com/uwp/api/Windows.Devices.Sensors.Inclinometer) и [**OrientationSensor**](https://docs.microsoft.com/uwp/api/Windows.Devices.Sensors.OrientationSensor) определяются их опорными осями. Эти оси определяются опорным кадром устройства и поворачиваются вместе с устройством, когда пользователь поворачивает последнее. Если ваше приложение поддерживает автоматический поворот и автоматически меняет ориентацию, когда пользователь поворачивает устройство, необходимо скорректировать данные датчиков для поворота до начала его использования.

## <a name="display-orientation-vs-device-orientation"></a>Ориентация дисплея и ориентация устройства

Чтобы получить представление об опорных осях для датчиков, необходимо понимать разницу между ориентацией дисплея и ориентацией устройства. Ориентация дисплея — это направление, в котором на экране отображаются текст и изображения, а ориентация устройства — это его физическое положение. На следующем изображении ориентация устройства и дисплея — **Альбомная** (обратите внимание, что показанные оси датчиков применимы только к устройствам с преимущественно альбомной ориентацией).

![Альбомная ориентация дисплея и устройства](images/sensor-orientation-a.PNG)

На следующем изображении ориентация как устройства, так и дисплея — **LandscapeFlipped**.

![Альбомная (перевернутая) ориентация дисплея и устройства](images/sensor-orientation-b.PNG)

На следующем изображении вы видите дисплей в альбомной ориентации, а устройство — в альбомной (перевернутой).

![Дисплей в альбомной ориентации, а устройство — в альбомной (перевернутой)](images/sensor-orientation-c.PNG)

Можно запросить значения ориентации при помощи класса [**DisplayInformation**](https://docs.microsoft.com/uwp/api/Windows.Graphics.Display.DisplayInformation), используя метод [**GetForCurrentView**](https://docs.microsoft.com/uwp/api/windows.graphics.display.displayinformation.getforcurrentview) со свойством [**CurrentOrientation**](https://docs.microsoft.com/uwp/api/windows.graphics.display.displayinformation.currentorientation). Затем можно создать логику путем сравнения с перечислением [**DisplayOrientations**](https://docs.microsoft.com/uwp/api/Windows.Graphics.Display.DisplayOrientations). Следует помнить, что для каждой поддерживаемой ориентации необходимо предусмотреть преобразование опорных осей.

## <a name="landscape-first-vs-portrait-first-devices"></a>Устройства с преимущественно альбомной и преимущественно книжной ориентацией

Изготовители создают устройства как с преимущественно альбомной, так и с преимущественно книжной ориентацией. Опорный кадр различается в зависимости от преимущественной ориентации устройств: альбомной, как у настольных компьютеров и ноутбуков, или книжной, как у телефонов и некоторых планшетов. В следующей таблице представлены оси датчиков для устройств обоих видов.

| Orientation | Преимущественно альбомная | Преимущественно книжная |
|-------------|-----------------|----------------|
| **Альбомная ориентация** | ![Устройства с преимущественно альбомной ориентацией в альбомной ориентации](images/sensor-orientation-0.PNG) | ![Устройства с преимущественно книжной ориентацией в альбомной ориентации](images/sensor-orientation-1.PNG) |
| **Книжная ориентация** | ![Устройства с преимущественно альбомной ориентацией в книжной ориентации](images/sensor-orientation-2.PNG) | ![Устройства с преимущественно книжной ориентацией в книжной ориентации](images/sensor-orientation-3.PNG) |
| **LandscapeFlipped** | ![Устройства с преимущественно альбомной ориентацией в альбомной (перевернутой) ориентации](images/sensor-orientation-4.PNG) | ![Устройства с преимущественно книжной ориентацией в альбомной (перевернутой) ориентации](images/sensor-orientation-5.PNG) | 
| **PortraitFlipped** | ![Устройства с преимущественно альбомной ориентацией в книжной (перевернутой) ориентации](images/sensor-orientation-6.PNG)| ![Устройства с преимущественно книжной ориентацией в книжной (перевернутой) ориентации](images/sensor-orientation-7.PNG) |

## <a name="devices-broadcasting-display-and-headless-devices"></a>Устройства, транслирующие изображение, и устройства без монитора

Некоторые устройства имеют возможность транслировать изображение на другое устройство. Например, можно транслировать изображение дисплея планшета на экран проектора, который находится в альбомной ориентации. В этом сценарии важно помнить, что ориентация изображения зависит от ориентации исходного устройства, а не того устройства, на которое транслируется изображение. Таким образом, акселерометр предоставляет данные для планшета.

Кроме того, некоторые устройства не имеют дисплея. Для этих устройств ориентация по умолчанию — книжная.

## <a name="display-orientation-and-compass-heading"></a>Ориентация экрана и направление по компасу


Направление по компасу зависит от опорных осей и, следовательно, меняется вместе с ориентацией устройства. Компенсация отклонения компаса определяется по следующей таблице (предполагается, что пользователь стоит лицом к северу).

| Ориентация экрана | Опорная ось для направления по компасу | Направление по компасу, когда пользователь стоит лицом к северу, согласно API (преимущественно альбомная) | Направление по компасу, когда пользователь стоит лицом к северу, согласно API (преимущественно книжная) |Компенсация отклонения компаса (преимущественно альбомная) | Компенсация отклонения компаса (преимущественно книжная) |
|---------------------|------------------------------------|---------------------------------------------------------|--------------------------------------------------------|------------------------------------------------|-----------------------------------------------|
| Альбомная           | -Z | 0   | 270 | Направление               | (Направление + 90) % 360  |
| Книжная            |  Y | 90  | 0   | (Направление + 270) % 360 |  Направление              |
| LandscapeFlipped    |  Z | 180 | 90  | (Направление + 180) % 360 | (Направление + 270) % 360 |
| PortraitFlipped     |  Y | 270 | 180 | (Направление + 90) % 360  | (Направление + 180) % 360 |

Измените направление по компасу согласно таблице, чтобы правильно отобразить курс. В приведенном ниже фрагменте кода показано, как это сделать.

```csharp
private void ReadingChanged(object sender, CompassReadingChangedEventArgs e)
{
    double heading = e.Reading.HeadingMagneticNorth;        
    double displayOffset;
    
    // Calculate the compass heading offset based on
    // the current display orientation.
    DisplayInformation displayInfo = DisplayInformation.GetForCurrentView();
    
    switch (displayInfo.CurrentOrientation) 
    { 
        case DisplayOrientations.Landscape: 
            displayOffset = 0; 
            break;
        case DisplayOrientations.Portrait: 
            displayOffset = 270; 
            break; 
        case DisplayOrientations.LandscapeFlipped: 
            displayOffset = 180; 
            break; 
        case DisplayOrientations.PortraitFlipped: 
            displayOffset = 90; 
            break; 
     } 
    

    double displayCompensatedHeading = (heading + displayOffset) % 360;
    
    // Update the UI...
}
```

## <a name="display-orientation-with-the-accelerometer-and-gyrometer"></a>Ориентация экрана с использованием акселерометра и гирометра

В следующей таблице приведено преобразование данных акселерометра и гирометра для ориентации экрана.

| Опорные оси        |  X |  Y | Z |
|-----------------------|----|----|---|
| **Альбомная ориентация**         |  X |  Y | Z |
| **Книжная ориентация**          |  Y | -X | Z |
| **LandscapeFlipped**  | -X | -Y | Z |
| **PortraitFlipped**   | -Y |  X | Z |

В следующем примере кода эти преобразования применяются к гирометру.

```csharp
private void ReadingChanged(object sender, GyrometerReadingChangedEventArgs e)
{
    double x_Axis;
    double y_Axis;
    double z_Axis;

    GyrometerReading reading = e.Reading;  
    
    // Calculate the gyrometer axes based on
    // the current display orientation.
    DisplayInformation displayInfo = DisplayInformation.GetForCurrentView();
    switch (displayInfo.CurrentOrientation) 
    { 
        case DisplayOrientations.Landscape: 
            x_Axis = reading.AngularVelocityX;
            y_Axis = reading.AngularVelocityY;
            z_Axis = reading.AngularVelocityZ;
            break;
        case DisplayOrientations.Portrait: 
            x_Axis = reading.AngularVelocityY;
            y_Axis = -1 * reading.AngularVelocityX;
            z_Axis = reading.AngularVelocityZ;
            break; 
        case DisplayOrientations.LandscapeFlipped: 
            x_Axis = -1 * reading.AngularVelocityX;
            y_Axis = -1 * reading.AngularVelocityY;
            z_Axis = reading.AngularVelocityZ;
            break; 
        case DisplayOrientations.PortraitFlipped: 
            x_Axis = -1 * reading.AngularVelocityY;
            y_Axis = reading.AngularVelocityX;
            z_Axis = reading.AngularVelocityZ;
            break; 
     } 
    
    
    // Update the UI...
}
```

## <a name="display-orientation-and-device-orientation"></a>Ориентация экрана и ориентация устройства

Данные [**OrientationSensor**](https://docs.microsoft.com/uwp/api/Windows.Devices.Sensors.OrientationSensor) необходимо изменять по-другому. Рассматривайте разные ориентации как повороты против часовой стрелки вокруг оси Z, поэтому для того, чтобы вернуть заданную пользователем ориентацию, нужно изменить направление вращения на противоположное. Для данных кватерниона можно использовать формулу Эйлера, чтобы определить поворот с опорным кватернионом. Можно также использовать опорную матрицу вращения.

![Формула Эйлера](images/eulers-formula.png)

Чтобы получить желаемую относительную ориентацию, умножьте опорный объект на абсолютный объект. Обратите внимание, что эта формула не обладает свойством коммутативности.

![Умножение опорного объекта на абсолютный объект](images/orientation-formula.png)

В предыдущем выражении абсолютный объект возвращается данными датчика.


| Ориентация экрана  | Поворот против часовой стрелки вокруг оси Z | Опорный кватернион (поворот в противоположном направлении) | Опорная матрица вращения (поворот в противоположном направлении) | 
|----------------------|------------------------------------|-----------------------------------------|----------------------------------------------|
| **Альбомная ориентация**        | 0                                  | 1 + 0i + 0j + 0k                        | \[1 0 0<br/> 0 1 0<br/> 0 0 1\]               |
| **Книжная ориентация**         | 90                                 | cos(-45⁰) + (i + j + k)*sin(-45⁰)       | \[0 1 0<br/>-1 0 0<br/>0 0 1]              |
| **LandscapeFlipped** | 180                                | 0 - i - j - k                           | \[1 0 0<br/> 0 1 0<br/> 0 0 1]               |
| **PortraitFlipped**  | 270                                | cos(-135⁰) + (i + j + k)*sin(-135⁰)     | \[0 -1 0<br/> 1  0 0<br/> 0  0 1]             |


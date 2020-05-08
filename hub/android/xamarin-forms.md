---
title: Создание простого приложения Android с помощью Xamarin. Forms
description: Как приступить к написанию приложений Android с помощью Xamarin. Forms
author: hickeys
ms.author: hickeys
manager: jken
ms.topic: article
keywords: Android, Windows, Xamarin. Forms, XAML, учебник
ms.date: 04/28/2020
ms.openlocfilehash: a1426bfef9863227c1ac110bc295536786695df7
ms.sourcegitcommit: 24b19e7ee06e5bb11a0dae334806741212490ee9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/29/2020
ms.locfileid: "82255198"
---
# <a name="get-started-developing-for-android-using-xamarinforms"></a>Приступая к разработке для Android с помощью Xamarin. Forms

Это руководство поможет приступить к работе с Xamarin. Forms в Windows для создания кросс-платформенного приложения, которое будет работать на устройствах Android.

В этой статье вы создадите простое приложение Android с помощью Xamarin. Forms и Visual Studio 2019.

## <a name="requirements"></a>Требования

Для работы с этим руководством вам потребуется следующее:

- быть под управлением ОС Windows 10;
- [Visual Studio 2019: Community, Professional или Enterprise](https://visualstudio.microsoft.com/downloads/) (см. Примечание)
- Рабочая нагрузка "Разработка мобильных приложений с помощью .NET" для Visual Studio 2019

> [!NOTE]
> Это руководством будет работать с Visual Studio 2017 или 2019. Если вы используете Visual Studio 2017, некоторые инструкции могут быть неправильными из-за различий пользовательского интерфейса между двумя версиями Visual Studio.

Вам также потребуется телефон Android или настроенный эмулятор для запуска приложения. См. статью [тестирование на устройстве или эмуляторе Android](emulator.md).

## <a name="create-a-new-xamarinforms-project"></a>Создание нового проекта Xamarin. Forms

Запустите Visual Studio. Щелкните файл > создать > проект, чтобы создать новый проект.

В диалоговом окне Новый проект выберите шаблон **мобильное приложение (Xamarin. Forms)** и нажмите кнопку **Далее**.

Присвойте проекту имя **тимечанжерформс** и нажмите кнопку **создать**.

В диалоговом окне Создание межплатформенного приложения выберите **пустое**. В разделе Платформа установите флажки **Android** и снимите флажки для всех остальных полей. Нажмите кнопку **ОК**.

Xamarin создаст новое решение с двумя проектами: **тимечанжерформс** и **тимечанжерформс. Android.**

## <a name="create-a-ui-with-xaml"></a>Создание пользовательского интерфейса с помощью XAML

Разверните проект **тимечанжерформс** и откройте **MainPage. XAML**. XAML в этом файле определяет первый экран, который пользователь увидит при открытии Тимечанжер.

Пользовательский интерфейс Тимечанжер прост. Он отображает текущее время и содержит кнопки для корректировки времени с шагом в один час. Он использует вертикальный StackLayout для выравнивания времени над кнопками, а горизонтальное StackLayout — для параллельного расположения кнопок. Содержимое выравнивается по центру экрана путем установки параметра вертикальной StackLayout **хоризонталоптионс** и **Вертикалоптионс** в значение **центерандекспанд**.

Замените содержимое файла MainPage. XAML следующим кодом.

```xml
<?xml version="1.0" encoding="utf-8" ?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:d="http://xamarin.com/schemas/2014/forms/design"
             xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
             mc:Ignorable="d"
             x:Class="TimeChangerForms.MainPage">

    <StackLayout HorizontalOptions="CenterAndExpand"
                 VerticalOptions="CenterAndExpand">
        <Label x:Name="time"
               HorizontalOptions="CenterAndExpand"
               VerticalOptions="CenterAndExpand"
               Text="At runtime, this Label will display the current time.">
        </Label>
        <StackLayout Orientation="Horizontal">
            <Button HorizontalOptions="End"
                    VerticalOptions="End"
                    Text="Up"
                    Clicked="OnUpButton_Clicked"/>
            <Button HorizontalOptions="Start"
                    VerticalOptions="End"
                    Text="Down"
                    Clicked="OnDownButton_Clicked"/>
        </StackLayout>
    </StackLayout>
</ContentPage>
```

На этом этапе пользовательский интерфейс завершен. Однако Тимечанжерформс не будет собираться, так как в XAML имеются ссылки на методы **UpButton_Clicked** и **DownButton_Clicked** , но не определены в любом месте. Даже если приложение было запущено, текущее время не будет отображаться. В следующем разделе вы исправите эти ошибки и добавите функции в пользовательский интерфейс.

## <a name="add-logic-code-with-c"></a>Добавление кода логики с помощью C #

В обозреватель решений щелкните правой кнопкой мыши MainPage. XAML и выберите пункт **Просмотреть код**. Этот файл содержит код программной части, который будет добавлять функции в пользовательский интерфейс.

### <a name="set-the-current-time"></a>Установка текущего времени

Код в этом файле может ссылаться на элементы управления, объявленные в XAML, с помощью значения атрибута **x:Name** элемента управления. В этом случае вызывается `time`метка, отображающая текущее время.

Элементы управления ИП должны быть обновлены в основном потоке. Изменения, внесенные из другого потока, могут неправильно обновлять элемент управления, как он отображается на экране. Поскольку нет никакой гарантии, что этот код всегда будет выполняться в основном потоке, используйте метод **бегининвокеонмаинсреад** , чтобы убедиться, что обновления отображаются правильно. Ниже приведен полный метод Упдатетимелабел.

```csharp
private void UpdateTimeLabel(object state = null)
{
    Device.BeginInvokeOnMainThread(() =>
        {
            time.Text = DateTime.Now.ToLongTimeString();
        }
    );
}
```

### <a name="update-the-current-time-once-every-second"></a>Обновлять текущее время каждую секунду

На этом этапе текущее время будет точным для (не более одной секунды после запуска Тимечанжерформс). Для сохранения точности времени метка должна быть периодически обновлена. Объект **таймера** будет периодически вызывать метод обратного вызова, который обновляет метку текущим временем.

```csharp
var clockRefresh = new Timer(dueTime: 0, period: 1000, callback: UpdateTimeLabel, state: null);
```

### <a name="add-houroffset"></a>Добавить Хауроффсет

Кнопки вверх и вниз корректируют время с шагом в один час. Добавьте свойство **хауроффсет** для контроля текущей корректировки.

```csharp
public int HourOffset { get; private set; }
```

Теперь обновите метод Упдатетимелабел, чтобы он знал о свойстве Хауроффсет.

```csharp
currentTime.Text = DateTime.Now.AddHours(HourOffset).ToLongTimeString();
```

### <a name="add-button-click-event-handlers"></a>Обработчики событий нажатия кнопки "Добавить"

Все кнопки вверх и вниз необходимо выполнить, увеличив или уменьшая свойство Хауроффсет и вызовите Упдатетимелабел.

```csharp
private void UpButton_Clicked(object sender, EventArgs e)
{
    HourOffset++;
    UpdateTimeLabel();
}
```

Когда все будет готово, MainPage.xaml.cs должен выглядеть следующим образом:

```csharp
using System;
using System.ComponentModel;
using System.Threading;
using Xamarin.Forms;

namespace TimeChangerForms
{
    // Learn more about making custom code visible in the Xamarin.Forms previewer
    // by visiting https://aka.ms/xamarinforms-previewer
    [DesignTimeVisible(false)]
    public partial class MainPage : ContentPage
    {
        public int HourOffset { get; private set; }

        public MainPage()
        {
            InitializeComponent();
        }

        protected override void OnAppearing()
        {
            base.OnAppearing();
            var clockRefresh = new Timer(dueTime: 0, period: 1000, callback: UpdateTimeLabel, state: null);
        }

        private void UpdateTimeLabel(object state = null)
        {
            Device.BeginInvokeOnMainThread(() =>
                {
                    time.Text = DateTime.Now.AddHours(HourOffset).ToLongTimeString();
                }
            );
        }

        private void OnUpButton_Clicked(object sender, EventArgs e)
        {
            HourOffset++;
            UpdateTimeLabel();
        }

        private void OnDownButton_Clicked(object sender, EventArgs e)
        {
            HourOffset--;
            UpdateTimeLabel();
        }
    }
}
```

## <a name="run-the-app"></a>Запуск приложения

Чтобы запустить приложение, нажмите клавишу **F5** или кнопку отладка > начать отладку. В зависимости от [настройки отладчика](emulator.md)приложение запустится на устройстве или в эмуляторе.

## <a name="related-links"></a>Связанные ссылки

- [Тестирование на устройстве или эмуляторе Android](emulator.md).

- [Создание примера приложения Android с помощью Xamarin. Android](xamarin-android.md)

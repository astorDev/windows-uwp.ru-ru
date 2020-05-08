---
title: Создание простого приложения Android с помощью Xamarin. Android
description: Как приступить к написанию приложений Android с помощью Xamarin. Android
author: hickeys
ms.author: hickeys
manager: jken
ms.topic: article
keywords: Android, Windows, Xamarin. Android, учебник, XAML
ms.date: 04/28/2020
ms.openlocfilehash: c731b5f96243333e4a4ad150de499ac9459113bc
ms.sourcegitcommit: 24b19e7ee06e5bb11a0dae334806741212490ee9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/29/2020
ms.locfileid: "82255210"
---
# <a name="get-started-developing-for-android-using-xamarinandroid"></a>Приступая к разработке для Android с помощью Xamarin. Android

Это руководство поможет приступить к работе с Xamarin. Android в Windows для создания кросс-платформенного приложения, которое будет работать на устройствах Android.

В этой статье вы создадите простое приложение Android с помощью Xamarin. Android и Visual Studio 2019.

## <a name="requirements"></a>Требования

Для работы с этим руководством вам потребуется следующее:

- быть под управлением ОС Windows 10;
- [Visual Studio 2019: Community, Professional или Enterprise](https://visualstudio.microsoft.com/downloads/) (см. Примечание)
- Рабочая нагрузка "Разработка мобильных приложений с помощью .NET" для Visual Studio 2019

> [!NOTE]
> Это руководством будет работать с Visual Studio 2017 или 2019. Если вы используете Visual Studio 2017, некоторые инструкции могут быть неправильными из-за различий пользовательского интерфейса между двумя версиями Visual Studio.

Вам также потребуется телефон Android или настроенный эмулятор для запуска приложения. См. раздел [Настройка эмулятора Android](emulator.md).

## <a name="create-a-new-xamarinandroid-project"></a>Создание нового проекта Xamarin.Android

Запустите Visual Studio. Выберите файл > создать > проект, чтобы создать новый проект.

В диалоговом окне Новый проект выберите шаблон **приложение Android (Xamarin)** и нажмите кнопку **Далее**.

Присвойте проекту имя **тимечанжерандроид** и нажмите кнопку **создать**.

В диалоговом окне Создание кросс — платформенного приложения выберите **пустое приложение**. В **минимальной версии Android**выберите **Android 5,0 (без описания операций)**. Нажмите кнопку **ОК**.

Xamarin создаст новое решение с одним проектом с именем **тимечанжерандроид**.

## <a name="create-a-ui-with-xaml"></a>Создание пользовательского интерфейса с помощью XAML

В каталоге **ресаурцес\лайаут** проекта откройте **файл activity_main. XML**. XML-код в этом файле определяет первый экран, который пользователь увидит при открытии Тимечанжер.

Пользовательский интерфейс Тимечанжер прост. Он отображает текущее время и содержит кнопки для корректировки времени с шагом в один час. Он использует вертикальный `LinearLayout` для выравнивания времени над кнопками и горизонтально `LinearLayout` , чтобы расположить кнопки рядом друг с другом. Содержимое выравнивается по центру экрана путем задания для атрибута **Android: тяжести** **центрирования** по вертикали `LinearLayout`.

Замените содержимое файла **activity_main. XML** следующим кодом.

```xml
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical"
    android:gravity="center">
    <TextView
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="At runtime, I will display current time"
        android:id="@+id/timeDisplay"
    />
    <LinearLayout
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:orientation="horizontal">
        <Button
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="Up"
            android:id="@+id/upButton"/>
        <Button
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="Down"
            android:id="@+id/downButton"/>
    </LinearLayout>
</LinearLayout>
```

На этом этапе можно запустить **тимечанжерандроид** и просмотреть созданный пользовательский интерфейс. В следующем разделе вы добавите в пользовательский интерфейс функциональные возможности, отображающие текущее время и позволяющие кнопкам выполнить действие.

## <a name="add-logic-code-with-c"></a>Добавление кода логики с помощью C #

Откройте файл **MainActivity.cs**. Этот файл содержит логику кода программной части, которая будет добавлять функции в пользовательский интерфейс.

### <a name="set-the-current-time"></a>Установка текущего времени

Сначала получите ссылку на объект `TextView` , который будет отображать время. Используйте **финдвиевбид** для поиска всех элементов пользовательского интерфейса с правильным **идентификатором Android: ID** (который был задан `"@+id/timeDisplay"` в XML-коде из предыдущего шага). Это то `TextView` , что будет отображать текущее время.

```csharp
var timeDisplay = FindViewById<TextView>(Resource.Id.timeDisplay);
```

Элементы управления ИП должны быть обновлены в потоке пользовательского интерфейса. Изменения, внесенные из другого потока, могут неправильно обновлять элемент управления, как он отображается на экране. Поскольку нет никакой гарантии, что этот код всегда будет выполняться в потоке пользовательского интерфейса, используйте метод **рунонуисреад** , чтобы убедиться, что обновления отображаются правильно. Ниже приведен полный `UpdateTimeLabel` метод.

```csharp
private void UpdateTimeLabel(object state = null)
{
    RunOnUiThread(() =>
    {
        TimeDisplay.Text = DateTime.Now.ToLongTimeString();
    });
}
```

### <a name="update-the-current-time-once-every-second"></a>Обновлять текущее время каждую секунду

На этом этапе текущее время будет точным для (не более одной секунды после запуска Тимечанжерандроид). Для сохранения точности времени метка должна быть периодически обновлена. Объект **таймера** будет периодически вызывать метод обратного вызова, который обновляет метку текущим временем.

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
TimeDisplay.Text = DateTime.Now.AddHours(HourOffset).ToLongTimeString();
```

### <a name="create-the-button-click-event-handlers"></a>Создание обработчиков событий нажатия кнопки

Все кнопки вверх и вниз необходимо выполнить, увеличив или уменьшая свойство Хауроффсет и вызовите Упдатетимелабел.

```csharp
public void UpButton_Click(object sender, System.EventArgs e)
{
    HourOffset++;
    UpdateTimeLabel();
}
```

### <a name="wire-up-the-up-and-down-buttons-to-their-corresponding-event-handlers"></a>Подключайте кнопки вверх и вниз к соответствующим обработчикам событий

Чтобы связать кнопки с соответствующими обработчиками событий, сначала используйте Финдвиевбид, чтобы найти кнопки по их идентификаторам. После получения ссылки на объект Button можно добавить обработчик событий к его `Click` событию.

```csharp
Button upButton = FindViewById<Button>(Resource.Id.upButton);
upButton.Click += UpButton_Click;
```

## <a name="completed-mainactivitycs-file"></a>Завершенный файл MainActivity.cs

Когда все будет готово, MainActivity.cs должен выглядеть следующим образом:

```csharp
using Android.App;
using Android.OS;
using Android.Support.V7.App;
using Android.Runtime;
using Android.Widget;
using System;
using System.Threading;

namespace TimeChangerAndroid
{
    [Activity(Label = "@string/app_name", Theme = "@style/AppTheme", MainLauncher = true)]
    public class MainActivity : AppCompatActivity
    {
        public TextView TimeDisplay { get; private set; }
        public int HourOffset { get; private set; }

        protected override void OnCreate(Bundle savedInstanceState)
        {
            base.OnCreate(savedInstanceState);

            // Set the view from the "main" layout resource
            SetContentView(Resource.Layout.activity_main);

            var clockRefresh = new Timer(dueTime: 0, period: 1000, callback: UpdateTimeLabel, state: null);

            Button upButton = FindViewById<Button>(Resource.Id.upButton);
            upButton.Click += OnUpButton_Click;

            Button downButton = FindViewById<Button>(Resource.Id.downButton);
            downButton.Click += OnDownButton_Click;

            TimeDisplay = FindViewById<TextView>(Resource.Id.timeDisplay);
        }

        private void UpdateTimeLabel(object state = null)
        {
            // Timer callbacks run on a background thread, but UI updates must run on the UI thread.
            RunOnUiThread(() =>
            {
                TimeDisplay.Text = DateTime.Now.AddHours(HourOffset).ToLongTimeString();
            });
        }

        public void OnUpButton_Click(object sender, System.EventArgs e)
        {
            HourOffset++;
            UpdateTimeLabel();
        }

        public void OnDownButton_Click(object sender, System.EventArgs e)
        {
            HourOffset--;
            UpdateTimeLabel();
        }
    }
}
```

## <a name="run-your-app"></a>Запуск приложения для Android

Чтобы запустить приложение, нажмите клавишу **F5** или кнопку отладка > начать отладку. В зависимости от [настройки отладчика](emulator.md)приложение запустится на устройстве или в эмуляторе.

## <a name="related-links"></a>Связанные ссылки

- [Тестирование на устройстве или эмуляторе Android](emulator.md).
- [Создание примера приложения Android с помощью Xamarin. Forms](xamarin-forms.md)

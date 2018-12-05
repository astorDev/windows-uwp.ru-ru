---
description: Пространство имен Windows.ApplicationModel.Appointments позволяет создавать встречи в приложении календаря пользователя и управлять ими.
title: Управление встречами
ms.assetid: 292E9249-07C3-4791-B32C-6EC153C2B538
ms.date: 02/08/2017
ms.topic: article
keywords: windows 10, uwp, встречи, календарь
ms.localizationpriority: medium
ms.openlocfilehash: 805383d243008f839b04c98906edd3beb52d581a
ms.sourcegitcommit: d7613c791107f74b6a3dc12a372d9de916c0454b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/05/2018
ms.locfileid: "8732568"
---
# <a name="manage-appointments"></a>Управление встречами



Пространство имен [**Windows.ApplicationModel.Appointments**](https://msdn.microsoft.com/library/windows/apps/Dn263359) позволяет создавать встречи в приложении календаря пользователя и управлять ими. Здесь мы покажем, как создать встречу, добавить ее в приложение календаря, заменить и удалить из него. Кроме того, вы узнаете, как отобразить интервал времени в календаре и создать объект повторяющейся встречи.

## <a name="create-an-appointment-and-apply-data-to-it"></a>Создание встречи и применение к ней данных

Создайте объект [**Windows.ApplicationModel.Appointments.Appointment**](https://msdn.microsoft.com/library/windows/apps/Dn297221) и назначьте его переменной. Затем примените к **Appointment** свойства встречи, предоставленные пользователем через пользовательский интерфейс.

```cs
private void Create-Click(object sender, RoutedEventArgs e)
{
    bool isAppointmentValid = true;
    var appointment = new Windows.ApplicationModel.Appointments.Appointment();

    // StartTime
    var date = StartTimeDatePicker.Date;
    var time = StartTimeTimePicker.Time;
    var timeZoneOffset = TimeZoneInfo.Local.GetUtcOffset(DateTime.Now);
    var startTime = new DateTimeOffset(date.Year, date.Month, date.Day, time.Hours, time.Minutes, 0, timeZoneOffset);
    appointment.StartTime = startTime;

    // Subject
    appointment.Subject = SubjectTextBox.Text;

    if (appointment.Subject.Length > 255)
    {
        isAppointmentValid = false;
        ResultTextBlock.Text = "The subject cannot be greater than 255 characters.";
    }

    // Location
    appointment.Location = LocationTextBox.Text;

    if (appointment.Location.Length > 32768)
    {
        isAppointmentValid = false;
        ResultTextBlock.Text = "The location cannot be greater than 32,768 characters.";
    }

    // Details
    appointment.Details = DetailsTextBox.Text;

    if (appointment.Details.Length > 1073741823)
    {
        isAppointmentValid = false;
        ResultTextBlock.Text = "The details cannot be greater than 1,073,741,823 characters.";
    }

    // Duration
    if (DurationComboBox.SelectedIndex == 0)
    {
        // 30 minute duration is selected
        appointment.Duration = TimeSpan.FromMinutes(30);
    }
    else
    {
        // 1 hour duration is selected
        appointment.Duration = TimeSpan.FromHours(1);
    }

    // All Day
    appointment.AllDay = AllDayCheckBox.IsChecked.Value;

    // Reminder
    if (ReminderCheckBox.IsChecked.Value)
    {
        switch (ReminderComboBox.SelectedIndex)
        {
            case 0:
                appointment.Reminder = TimeSpan.FromMinutes(15);
                break;
            case 1:
                appointment.Reminder = TimeSpan.FromHours(1);
                break;
            case 2:
                appointment.Reminder = TimeSpan.FromDays(1);
                break;
        }
    }

    //Busy Status
    switch (BusyStatusComboBox.SelectedIndex)
    {
        case 0:
            appointment.BusyStatus = Windows.ApplicationModel.Appointments.AppointmentBusyStatus.Busy;
            break;
        case 1:
            appointment.BusyStatus = Windows.ApplicationModel.Appointments.AppointmentBusyStatus.Tentative;
            break;
        case 2:
            appointment.BusyStatus = Windows.ApplicationModel.Appointments.AppointmentBusyStatus.Free;
            break;
        case 3:
            appointment.BusyStatus = Windows.ApplicationModel.Appointments.AppointmentBusyStatus.OutOfOffice;
            break;
        case 4:
            appointment.BusyStatus = Windows.ApplicationModel.Appointments.AppointmentBusyStatus.WorkingElsewhere;
            break;
    }

    // Sensitivity
    switch (SensitivityComboBox.SelectedIndex)
    {
        case 0:
            appointment.Sensitivity = Windows.ApplicationModel.Appointments.AppointmentSensitivity.Public;
            break;
        case 1:
            appointment.Sensitivity = Windows.ApplicationModel.Appointments.AppointmentSensitivity.Private;
            break;
    }

    // Uri
    if (UriTextBox.Text.Length > 0)
    {
        try
        {
            appointment.Uri = new System.Uri(UriTextBox.Text);
        }
        catch (Exception)
        {
            isAppointmentValid = false;
            ResultTextBlock.Text = "The Uri provided is invalid.";
        }
    }

    // Organizer
    // Note: Organizer can only be set if there are no invitees added to this appointment.
    if (OrganizerRadioButton.IsChecked.Value)
    {
        var organizer = new Windows.ApplicationModel.Appointments.AppointmentOrganizer();

        // Organizer Display Name
        organizer.DisplayName = OrganizerDisplayNameTextBox.Text;

        if (organizer.DisplayName.Length > 256)
        {
            isAppointmentValid = false;
            ResultTextBlock.Text = "The organizer display name cannot be greater than 256 characters.";
        }
        else
        {
            // Organizer Address (e.g. Email Address)
            organizer.Address = OrganizerAddressTextBox.Text;

            if (organizer.Address.Length > 321)
            {
                isAppointmentValid = false;
                ResultTextBlock.Text = "The organizer address cannot be greater than 321 characters.";
            }
            else if (organizer.Address.Length == 0)
            {
                isAppointmentValid = false;
                ResultTextBlock.Text = "The organizer address must be greater than 0 characters.";
            }
            else
            {
                appointment.Organizer = organizer;
            }
        }
    }

    // Invitees
    // Note: If the size of the Invitees list is not zero, then an Organizer cannot be set.
    if (InviteeRadioButton.IsChecked.Value)
    {
        var invitee = new Windows.ApplicationModel.Appointments.AppointmentInvitee();

        // Invitee Display Name
        invitee.DisplayName = InviteeDisplayNameTextBox.Text;

        if (invitee.DisplayName.Length > 256)
        {
            isAppointmentValid = false;
            ResultTextBlock.Text = "The invitee display name cannot be greater than 256 characters.";
        }
        else
        {
            // Invitee Address (e.g. Email Address)
            invitee.Address = InviteeAddressTextBox.Text;

            if (invitee.Address.Length > 321)
            {
                isAppointmentValid = false;
                ResultTextBlock.Text = "The invitee address cannot be greater than 321 characters.";
            }
            else if (invitee.Address.Length == 0)
            {
                isAppointmentValid = false;
                ResultTextBlock.Text = "The invitee address must be greater than 0 characters.";
            }
            else
            {
                // Invitee Role
                switch (RoleComboBox.SelectedIndex)
                {
                    case 0:
                        invitee.Role = Windows.ApplicationModel.Appointments.AppointmentParticipantRole.RequiredAttendee;
                        break;
                    case 1:
                        invitee.Role = Windows.ApplicationModel.Appointments.AppointmentParticipantRole.OptionalAttendee;
                        break;
                    case 2:
                        invitee.Role = Windows.ApplicationModel.Appointments.AppointmentParticipantRole.Resource;
                        break;
                }

                // Invitee Response
                switch (ResponseComboBox.SelectedIndex)
                {
                    case 0:
                        invitee.Response = Windows.ApplicationModel.Appointments.AppointmentParticipantResponse.None;
                        break;
                    case 1:
                        invitee.Response = Windows.ApplicationModel.Appointments.AppointmentParticipantResponse.Tentative;
                        break;
                    case 2:
                        invitee.Response = Windows.ApplicationModel.Appointments.AppointmentParticipantResponse.Accepted;
                        break;
                    case 3:
                        invitee.Response = Windows.ApplicationModel.Appointments.AppointmentParticipantResponse.Declined;
                        break;
                    case 4:
                        invitee.Response = Windows.ApplicationModel.Appointments.AppointmentParticipantResponse.Unknown;
                        break;
                }

                appointment.Invitees.Add(invitee);
            }
        }
    }

    if (isAppointmentValid)
    {
        ResultTextBlock.Text = "The appointment was created successfully and is valid.";
    }
}
```

## <a name="add-an-appointment-to-the-users-calendar"></a>Добавление встречи в календарь пользователя

Создайте объект [**Windows.ApplicationModel.Appointments.Appointment**](https://msdn.microsoft.com/library/windows/apps/Dn297221) и назначьте его переменной. Затем вызовите метод [**AppointmentManager.ShowAddAppointmentAsync(Appointment, Rect, Placement)**](https://msdn.microsoft.com/library/windows/apps/dn297261), чтобы отобразить пользовательский интерфейс добавления встречи поставщика встреч по умолчанию, который позволит пользователю добавить встречу. Если пользователь нажимает кнопку **Добавить**, пример выводит идентификатор встречи, возвращенный **ShowAddAppointmentAsync**.

```cs
private async void Add-Click(object sender, RoutedEventArgs e)
{
    // Create an Appointment that should be added the user's appointments provider app.
    var appointment = new Windows.ApplicationModel.Appointments.Appointment();

    // Get the selection rect of the button pressed to add this appointment
    var rect = GetElementRect(sender as FrameworkElement);

    // ShowAddAppointmentAsync returns an appointment id if the appointment given was added to the user's calendar.
    // This value should be stored in app data and roamed so that the appointment can be replaced or removed in the future.
    // An empty string return value indicates that the user canceled the operation before the appointment was added.
    String appointmentId = await Windows.ApplicationModel.Appointments.AppointmentManager.ShowAddAppointmentAsync(
                           appointment, rect, Windows.UI.Popups.Placement.Default);
    if (appointmentId != String.Empty)
    {
        ResultTextBlock.Text = "Appointment Id: " + appointmentId;
    }
    else
    {
        ResultTextBlock.Text = "Appointment not added.";
    }
}
```

**Примечание**для приложений магазина Windows Phone, [**ShowAddAppointment**](https://msdn.microsoft.com/library/windows/apps/windows.applicationmodel.appointments.appointmentmanager.showaddappointmentasync) функции так же, как [**ShowEditNewAppointment**](https://msdn.microsoft.com/library/windows/apps/windows.applicationmodel.appointments.appointmentmanager.showeditnewappointmentasync) в том, что диалоговое окно добавления встречи.

## <a name="replace-an-appointment-in-the-users-calendar"></a>Замена встречи в календаре пользователя

Создайте объект [**Windows.ApplicationModel.Appointments.Appointment**](https://msdn.microsoft.com/library/windows/apps/Dn297221) и назначьте его переменной. Затем вызовите соответствующий метод [**AppointmentManager.ShowReplaceAppointmentAsync**](https://msdn.microsoft.com/library/windows/apps/windows.applicationmodel.appointments.appointmentmanager.showreplaceappointmentasync), чтобы отобразить пользовательский интерфейс замены встречи поставщика встреч по умолчанию, который позволит пользователю заменить встречу. Пользователь предоставляет идентификатор встречи, которую хочет заменить. Это идентификатор, который был возвращен [**AppointmentManager.ShowAddAppointmentAsync**](https://msdn.microsoft.com/library/windows/apps/windows.applicationmodel.appointments.appointmentmanager.showaddappointmentasync). Если пользователь нажал кнопку **Заменить**, пример сообщает, что идентификатор встречи обновлен.

```cs
private async void Replace-Click(object sender, RoutedEventArgs e)
{
    // The appointment id argument for ReplaceAppointmentAsync is typically retrieved from AddAppointmentAsync and stored in app data.
    String appointmentIdOfAppointmentToReplace = AppointmentIdTextBox.Text;

    if (String.IsNullOrEmpty(appointmentIdOfAppointmentToReplace))
    {
        ResultTextBlock.Text = "The appointment id cannot be empty";
    }
    else
    {
        // The Appointment argument for ReplaceAppointmentAsync should contain all of the Appointment' s properties including those that may have changed.
        var appointment = new Windows.ApplicationModel.Appointments.Appointment();

        // Get the selection rect of the button pressed to replace this appointment
        var rect = GetElementRect(sender as FrameworkElement);

        // ReplaceAppointmentAsync returns an updated appointment id when the appointment was successfully replaced.
        // The updated id may or may not be the same as the original one retrieved from AddAppointmentAsync.
        // An optional instance start time can be provided to indicate that a specific instance on that date should be replaced
        // in the case of a recurring appointment.
        // If the appointment id returned is an empty string, that indicates that the appointment was not replaced.
        String updatedAppointmentId;
        if (InstanceStartDateCheckBox.IsChecked.Value)
        {
            // Replace a specific instance starting on the date provided.
            var instanceStartDate = InstanceStartDateDatePicker.Date;
            updatedAppointmentId = await Windows.ApplicationModel.Appointments.AppointmentManager.ShowReplaceAppointmentAsync(
                                   appointmentIdOfAppointmentToReplace, appointment, rect, Windows.UI.Popups.Placement.Default, instanceStartDate);
        }
        else
        {
            // Replace an appointment that occurs only once or in the case of a recurring appointment, replace the entire series.
            updatedAppointmentId = await Windows.ApplicationModel.Appointments.AppointmentManager.ShowReplaceAppointmentAsync(
                                   appointmentIdOfAppointmentToReplace, appointment, rect, Windows.UI.Popups.Placement.Default);
        }

        if (updatedAppointmentId != String.Empty)
        {
            ResultTextBlock.Text = "Updated Appointment Id: " + updatedAppointmentId;
        }
        else
        {
            ResultTextBlock.Text = "Appointment not replaced.";
        }
    }
}
```

## <a name="remove-an-appointment-from-the-users-calendar"></a>Удаление встречи из календаря пользователя

Вызовите соответствующий метод [**AppointmentManager.ShowRemoveAppointmentAsync**](https://msdn.microsoft.com/library/windows/apps/windows.applicationmodel.appointments.appointmentmanager.showremoveappointmentasync), чтобы отобразить пользовательский интерфейс удаления встречи поставщика встреч по умолчанию, который позволит пользователю удалить встречу. Пользователь предоставляет идентификатор встречи, которую хочет заменить. Это идентификатор, который был возвращен [**AppointmentManager.ShowAddAppointmentAsync**](https://msdn.microsoft.com/library/windows/apps/windows.applicationmodel.appointments.appointmentmanager.showaddappointmentasync). Если пользователь нажимает кнопку **Удалить**, пример сообщает, что встреча с этим идентификатором удалена.

```cs
private async void Remove-Click(object sender, RoutedEventArgs e)
{
    // The appointment id argument for ShowRemoveAppointmentAsync is typically retrieved from AddAppointmentAsync and stored in app data.
    String appointmentId = AppointmentIdTextBox.Text;

    // The appointment id cannot be null or empty.
    if (String.IsNullOrEmpty(appointmentId))
    {
        ResultTextBlock.Text = "The appointment id cannot be empty";
    }
    else
    {
        // Get the selection rect of the button pressed to remove this appointment
        var rect = GetElementRect(sender as FrameworkElement);

        // ShowRemoveAppointmentAsync returns a boolean indicating whether or not the appointment related to the appointment id given was removed.
        // An optional instance start time can be provided to indicate that a specific instance on that date should be removed
        // in the case of a recurring appointment.
        bool removed;
        if (InstanceStartDateCheckBox.IsChecked.Value)
        {
            // Remove a specific instance starting on the date provided.
            var instanceStartDate = InstanceStartDateDatePicker.Date;
            removed = await Windows.ApplicationModel.Appointments.AppointmentManager.ShowRemoveAppointmentAsync(
                      appointmentId, rect, Windows.UI.Popups.Placement.Default, instanceStartDate);
        }
        else
        {
            // Remove an appointment that occurs only once or in the case of a recurring appointment, replace the entire series.
            removed = await Windows.ApplicationModel.Appointments.AppointmentManager.ShowRemoveAppointmentAsync(
                      appointmentId, rect, Windows.UI.Popups.Placement.Default);
        }

        if (removed)
        {
            ResultTextBlock.Text = "Appointment removed";
        }
        else
        {
            ResultTextBlock.Text = "Appointment not removed";
        }
    }
}
```

## <a name="show-a-time-span-for-the-appointments-provider"></a>Отображение интервала времени для поставщика встреч

Вызовите метод [**AppointmentManager.ShowTimeFrameAsync**](https://msdn.microsoft.com/library/windows/apps/windows.applicationmodel.appointments.appointmentmanager.showtimeframeasync), чтобы отобразить конкретный интервал времени для основного пользовательского интерфейса поставщика встреч по умолчанию, если пользователь нажал кнопку **Показать**. Пример сообщает, что поставщик встреч по умолчанию отображен на экране.

```cs
private async void Show-Click(object sender, RoutedEventArgs e)
{
    var dateToShow = new DateTimeOffset(2015, 6, 12, 18, 32, 0, 0, TimeSpan.FromHours(-8));
    var duration = TimeSpan.FromHours(1);
    await Windows.ApplicationModel.Appointments.AppointmentManager.ShowTimeFrameAsync(dateToShow, duration);
    ResultTextBlock.Text = "The default appointments provider should have appeared on screen.";
}
```

## <a name="create-an-appointment-recurrence-object-and-apply-data-to-it"></a>Создание объекта повторяющейся встречи и применение к нему данных

Создайте объект [**Windows.ApplicationModel.Appointments.AppointmentRecurrence**](https://msdn.microsoft.com/library/windows/apps/windows.applicationmodel.appointments.appointmentrecurrence) и назначьте его переменной. Затем примените к **AppointmentRecurrence** свойства повторения, предоставленные пользователем через пользовательский интерфейс.

```cs
private void Create-Click(object sender, RoutedEventArgs e)
{
    bool isRecurrenceValid = true;
    var recurrence = new Windows.ApplicationModel.Appointments.AppointmentRecurrence();

    // Unit
    switch (UnitComboBox.SelectedIndex)
    {
        case 0:
            recurrence.Unit = Windows.ApplicationModel.Appointments.AppointmentRecurrenceUnit.Daily;
            break;
        case 1:
            recurrence.Unit = Windows.ApplicationModel.Appointments.AppointmentRecurrenceUnit.Weekly;
            break;
        case 2:
            recurrence.Unit = Windows.ApplicationModel.Appointments.AppointmentRecurrenceUnit.Monthly;
            break;
        case 3:
            recurrence.Unit = Windows.ApplicationModel.Appointments.AppointmentRecurrenceUnit.MonthlyOnDay;
            break;
        case 4:
            recurrence.Unit = Windows.ApplicationModel.Appointments.AppointmentRecurrenceUnit.Yearly;
            break;
        case 5:
            recurrence.Unit = Windows.ApplicationModel.Appointments.AppointmentRecurrenceUnit.YearlyOnDay;
            break;
    }

    // Occurrences
    // Note: Occurrences and Until properties are mutually exclusive.
    if (OccurrencesRadioButton.IsChecked.Value)
    {
        recurrence.Occurrences = (uint)OccurrencesSlider.Value;
    }

    // Until
    // Note: Until and Occurrences properties are mutually exclusive.
    if (UntilRadioButton.IsChecked.Value)
    {
        recurrence.Until = UntilDatePicker.Date;
    }

    // Interval
    recurrence.Interval = (uint)IntervalSlider.Value;

    // Week of the month
    switch (WeekOfMonthComboBox.SelectedIndex)
    {
        case 0:
            recurrence.WeekOfMonth = Windows.ApplicationModel.Appointments.AppointmentWeekOfMonth.First;
            break;
        case 1:
            recurrence.WeekOfMonth = Windows.ApplicationModel.Appointments.AppointmentWeekOfMonth.Second;
            break;
        case 2:
            recurrence.WeekOfMonth = Windows.ApplicationModel.Appointments.AppointmentWeekOfMonth.Third;
            break;
        case 3:
            recurrence.WeekOfMonth = Windows.ApplicationModel.Appointments.AppointmentWeekOfMonth.Fourth;
            break;
        case 4:
            recurrence.WeekOfMonth = Windows.ApplicationModel.Appointments.AppointmentWeekOfMonth.Last;
            break;
    }

    // Days of the Week
    // Note: For Weekly, MonthlyOnDay or YearlyOnDay recurrence unit values, at least one day must be specified.
    if (SundayCheckBox.IsChecked.Value) { recurrence.DaysOfWeek |= Windows.ApplicationModel.Appointments.AppointmentDaysOfWeek.Sunday; }
    if (MondayCheckBox.IsChecked.Value) { recurrence.DaysOfWeek |= Windows.ApplicationModel.Appointments.AppointmentDaysOfWeek.Monday; }
    if (TuesdayCheckBox.IsChecked.Value) { recurrence.DaysOfWeek |= Windows.ApplicationModel.Appointments.AppointmentDaysOfWeek.Tuesday; }
    if (WednesdayCheckBox.IsChecked.Value) { recurrence.DaysOfWeek |= Windows.ApplicationModel.Appointments.AppointmentDaysOfWeek.Wednesday; }
    if (ThursdayCheckBox.IsChecked.Value) { recurrence.DaysOfWeek |= Windows.ApplicationModel.Appointments.AppointmentDaysOfWeek.Thursday; }
    if (FridayCheckBox.IsChecked.Value) { recurrence.DaysOfWeek |= Windows.ApplicationModel.Appointments.AppointmentDaysOfWeek.Friday; }
    if (SaturdayCheckBox.IsChecked.Value) { recurrence.DaysOfWeek |= Windows.ApplicationModel.Appointments.AppointmentDaysOfWeek.Saturday; }

    if (((recurrence.Unit == Windows.ApplicationModel.Appointments.AppointmentRecurrenceUnit.Weekly) ||
         (recurrence.Unit == Windows.ApplicationModel.Appointments.AppointmentRecurrenceUnit.MonthlyOnDay) ||
         (recurrence.Unit == Windows.ApplicationModel.Appointments.AppointmentRecurrenceUnit.YearlyOnDay)) &&
        (recurrence.DaysOfWeek == Windows.ApplicationModel.Appointments.AppointmentDaysOfWeek.None))
    {
        isRecurrenceValid = false;
        ResultTextBlock.Text = "The recurrence specified is invalid. For Weekly, MonthlyOnDay or YearlyOnDay recurrence unit values, " +
                               "at least one day must be specified.";
    }

    // Month of the year
    recurrence.Month = (uint)MonthSlider.Value;

    // Day of the month
    recurrence.Day = (uint)DaySlider.Value;

    if (isRecurrenceValid)
    {
        ResultTextBlock.Text = "The recurrence specified was created successfully and is valid.";
    }
}
```

## <a name="add-a-new-editable-appointment"></a>Добавление новой редактируемой встречи

Метод [**ShowEditNewAppointmentAsync**](https://msdn.microsoft.com/library/windows/apps/windows.applicationmodel.appointments.appointmentmanager.showeditnewappointmentasync) работает так же, как [**ShowAddAppointmentAsync**](https://msdn.microsoft.com/library/windows/apps/windows.applicationmodel.appointments.appointmentmanager.showaddappointmentasync), но диалоговое окно для добавления встречи редактируется, так что пользователь может изменить данные встречи перед ее сохранением.

``` cs
private async void AddAndEdit-Click(object sender, RoutedEventArgs e)
{
    // Create an Appointment that should be added the user' s appointments provider app.
    var appointment = new Windows.ApplicationModel.Appointments.Appointment();

    appointment.StartTime = DateTime.Now + TimeSpan.FromDays(1);
    appointment.Duration = TimeSpan.FromHours(1);
    appointment.Location = "Meeting location";
    appointment.Subject = "Meeting subject";
    appointment.Details = "Meeting description";
    appointment.Reminder = TimeSpan.FromMinutes(15); // Remind me 15 minutes prior


    // ShowAddAppointmentAsync returns an appointment id if the appointment given was added to the user' s calendar.
    // This value should be stored in app data and roamed so that the appointment can be replaced or removed in the future.
    // An empty string return value indicates that the user canceled the operation before the appointment was added.
    String appointmentId =
        await Windows.ApplicationModel.Appointments.AppointmentManager.ShowEditNewAppointmentAsync(appointment);

    if (appointmentId != String.Empty)
    {
        ResultTextBlock.Text = "Appointment Id: " + appointmentId;
    }
    else
    {
        ResultTextBlock.Text = "Appointment not added.";
    }
}
```

## <a name="show-appointment-details"></a>Просмотр информации о встрече

Метод [**ShowAppointmentDetailsAsync**](https://msdn.microsoft.com/library/windows/apps/windows.applicationmodel.appointments.appointmentmanager.showappointmentdetailsasync) приводит к отображению информации об определенной встрече в системе. Приложение, которое реализует календари приложения, может выбрать активацию для отображения информации для встреч в календарях, которыми оно владеет. В противном случае информацию о встрече будет показывать система. Перегрузка метода, который принимает аргумент даты начала, предоставляется, чтобы показать информацию о встрече для экземпляра повторяющейся встречи.

```cs
private async void ShowAppointmentDetails-Click(object sender, RoutedEventArgs e)
{

    if (instanceStartTime == null)
    {
        await Windows.ApplicationModel.Appointments.AppointmentManager.ShowAppointmentDetailsAsync(
            currentAppointment.LocalId);
    }
    else
    {
        // Specify a start time to show an instance of a recurring appointment
        await Windows.ApplicationModel.Appointments.AppointmentManager.ShowAppointmentDetailsAsync(
            currentAppointment.LocalId, instanceStartTime);
    }

}
```

## <a name="summary-and-next-steps"></a>Сводка и дальнейшие действия

Теперь вы имеете общее представление об управлении встречами. Скачайте [Примеры универсальных приложений для Windows](http://go.microsoft.com/fwlink/p/?linkid=619979) с GitHub, чтобы просмотреть дополнительные примеры управления встречами.

## <a name="related-topics"></a>Еще по теме

* [Пример API встреч](http://go.microsoft.com/fwlink/p/?linkid=309836)
 

 

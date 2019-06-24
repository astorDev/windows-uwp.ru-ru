---
description: В этом разделе приведено полное сопоставление API-интерфейсы для Windows Phone Silverlight в их эквиваленты универсальной платформы Windows (UWP).
title: Windows Phone Silverlight для сопоставления пространства имен и класса универсальной платформы Windows
ms.assetid: 33f06706-4790-48f3-a2e4-ebef9ddb61a4
ms.date: 02/08/2017
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: 649062c8d1901a7b0f24a69378e13a7725d7c84c
ms.sourcegitcommit: 6f32604876ed480e8238c86101366a8d106c7d4e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/21/2019
ms.locfileid: "67322319"
---
# <a name="windowsphone-silverlight-to-uwp-api-mappings"></a>Windows Phone Silverlight для сопоставления API универсальной платформы Windows


В этом разделе приведено полное сопоставление API-интерфейсы для Windows Phone Silverlight в их эквиваленты универсальной платформы Windows (UWP). Обычно не существует полных функциональных аналогов, так как одна платформа может иметь большую или меньшую функциональность по сравнению с ее аналогом в пространстве имен или классе.

Таблица сопоставления поможет вам при работе в проекте UWP и повторно используется исходный код из проекта Windows Phone Silverlight. Между двумя платформами есть различия в именах пространств имен и классов (в том числе в элементах управления пользовательского интерфейса). В большинстве случаев нужно лишь изменить имя пространства имен, и код будет успешно работать. Иногда изменяется не только имя пространства имен, но и имя класса или API. Иногда сопоставление требует многих усилий, а в некоторых случаях требуется изменить подход.

**Как использовать в таблице:  ** Во-первых поиск по имени класса, который вы используете. Классы указываются, когда для сопоставления требуются более сложные действия, чем просто изменение имени пространства имен. Если класс не указан, тогда сопоставление заключается лишь в изменении пространства имен. Найдите имя пространства имен класса, и таким образом вы получите соответствующее имя пространства имен в UWP. Ваш класс будет указан в этом пространстве имен. Если вашего пространства имен нет в списке, это значит, что его имя не изменялось.

**Примечание**  Windows 10 поддерживает платформы .NET Framework гораздо больше, чем приложения Windows Phone Store. Например Windows 10 имеет несколько System.ServiceModel. \* пространств имен System.Net, System.Net.NetworkInformation и System.Net.Sockets.
Кроме того, в приложении Windows 10, будет обеспечен машинного кода .NET, который это компиляции ahead-of-time-технология, которая преобразует MSIL в машинный код, скомпилированных в собственном коде runnable. Приложения .NET Native запускаются быстрее, используют меньше памяти и меньше заряда батареи, чем аналоги MSIL.

| Windows Phone Silverlight | Среда выполнения Windows |
| ------------------------- | --------------- |
| Реклама | |
| Класс **Microsoft.Advertising.Mobile.UI.AdControl** | Класс [AdControl](https://docs.microsoft.com/windows/uwp/monetize/display-ads-using-the-microsoft-advertising-libraries) |
| Сигналы, напоминания и фоновые агенты | |
| Класс **Microsoft.Phone.BackgroundAgent** | [**BackgroundTaskBuilder** ](https://docs.microsoft.com/uwp/api/Windows.ApplicationModel.Background.BackgroundTaskBuilder) класса |
| Пространство имен **Microsoft.Phone.Scheduler** | [**Windows.ApplicationModel.Background** ](https://docs.microsoft.com/uwp/api/Windows.ApplicationModel.Background) пространства имен |
| Класс **Microsoft.Phone.Scheduler.Alarm** | [**BackgroundTaskBuilder** ](https://docs.microsoft.com/uwp/api/Windows.ApplicationModel.Background.BackgroundTaskBuilder) и [ **ToastNotificationManager** ](https://docs.microsoft.com/uwp/api/Windows.UI.Notifications.ToastNotificationManager) классы |
| Классы **Microsoft.Phone.Scheduler.PeriodicTask**, **ScheduledAction**, **ScheduledActionService**, **ScheduledTask**, **ScheduledTaskAgent** | [**BackgroundTaskBuilder** ](https://docs.microsoft.com/uwp/api/Windows.ApplicationModel.Background.BackgroundTaskBuilder) класса |
| Класс **Microsoft.Phone.Scheduler.Reminder** | [**BackgroundTaskBuilder** ](https://docs.microsoft.com/uwp/api/Windows.ApplicationModel.Background.BackgroundTaskBuilder) и [ **ToastNotificationManager** ](https://docs.microsoft.com/uwp/api/Windows.UI.Notifications.ToastNotificationManager) классы |
| Класс **Microsoft.Phone.PictureDecoder** | [**BitmapDecoder** ](https://docs.microsoft.com/uwp/api/Windows.Graphics.Imaging.BitmapDecoder) класса |
| Пространство имен **Microsoft.Phone.BackgroundAudio** | [**Windows.Media.Playback** ](https://docs.microsoft.com/uwp/api/Windows.Media.Playback) пространства имен |
| Пространство имен **Microsoft.Phone.BackgroundTransfer** | [**Windows.Networking.BackgroundTransfer** ](https://docs.microsoft.com/uwp/api/Windows.Networking.BackgroundTransfer) пространства имен |
| Модель и среда приложения | |
| Класс **System.AppDomain** | Нет прямого эквивалента. См. классы [**Application**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Application), [**CoreApplication**](https://docs.microsoft.com/uwp/api/Windows.ApplicationModel.Core.CoreApplication) |
| Класс **System.Environment** | Нет прямого эквивалента |
| Класс **System.ComponentModel.Annotations**  | Нет прямого эквивалента |
| Класс **System.ComponentModel.BackgroundWorker** | [**ThreadPool** ](https://docs.microsoft.com/uwp/api/Windows.System.Threading.ThreadPool) класса |
| Класс **System.ComponentModel.DesignerProperties** | [**DesignMode** ](https://docs.microsoft.com/uwp/api/Windows.ApplicationModel.DesignMode) класса |
| Классы **System.Threading.Thread**, **System.Threading.ThreadPool** | [**ThreadPool** ](https://docs.microsoft.com/uwp/api/Windows.System.Threading.ThreadPool) класса |
| (ST = **System.Threading**) <br/> Метод **ST.Thread.MemoryBarrier** | (ST = **System.Threading**) <br/> Метод **ST.Interlocked.MemoryBarrier** |
| (ST = **System.Threading**) <br/> Свойство **ST.Thread.ManagedThreadId** | (S = **System**) <br/> Свойство **S.Environment.ManagedThreadId** |
| Класс **System.Threading.Timer** | [**ThreadPoolTimer** ](https://docs.microsoft.com/uwp/api/Windows.System.Threading.ThreadPoolTimer) класса |
| (SWT = **System.Windows.Threading**) <br/> Класс **SWT.Dispatcher** | [**CoreDispatcher** ](https://docs.microsoft.com/uwp/api/Windows.UI.Core.CoreDispatcher) класса |
| (SWT = **System.Windows.Threading**) <br/> Класс **SWT.DispatcherTimer** | [**DispatcherTimer** ](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.DispatcherTimer) класса |
| Blend для Visual Studio | |
| (MEDC = **Microsoft.Expression.Drawing.Core**) <br/> Класс **MEDC.GeometryHelper** | Нет прямого эквивалента |
| Пространство имен **Microsoft.Expression.Interactivity** | Пространство имен [Microsoft.Xaml.Interactivity](https://go.microsoft.com/fwlink/p/?LinkId=328776) |
| Пространство имен **Microsoft.Expression.Interactivity.Core** | Пространство имен [Microsoft.Xaml.Interactions.Core](https://go.microsoft.com/fwlink/p/?LinkId=328773) |
| (MEIC = **Microsoft.Expression.Interactivity.Core**) <br/> Класс **MEIC.ExtendedVisualStateManager** | Нет прямого эквивалента |
| Пространство имен **Microsoft.Expression.Interactivity.Input** | Нет прямого эквивалента |
| Пространство имен **Microsoft.Expression.Interactivity.Media** | Пространство имен [Microsoft.Xaml.Interactions.Media](https://go.microsoft.com/fwlink/p/?LinkId=328775) |
| Пространство имен **Microsoft.Expression.Shapes** | Нет прямого эквивалента |
| (MI = **Microsoft.Internal**) <br/> Интерфейс **MI.IManagedFrameworkInternalHelper** | Нет прямого эквивалента |
| Данные контактов и календаря | |
| Пространство имен **Microsoft.Phone.UserData** | [**Windows.ApplicationModel.Contacts**](https://docs.microsoft.com/uwp/api/Windows.ApplicationModel.Contacts), [ **Windows.ApplicationModel.Appointments** ](https://docs.microsoft.com/uwp/api/Windows.ApplicationModel.Appointments) пространств имен |
| (MPU = **Microsoft.Phone.UserData**) <br/> Классы **MPU.Account**, **ContactAddress**, **ContactCompanyInformation**, **ContactEmailAddress**, **ContactPhoneNumber** | [**Обратитесь к** ](https://docs.microsoft.com/uwp/api/Windows.ApplicationModel.Contacts.Contact) класса |
| (MPU = **Microsoft.Phone.UserData**) <br/> Класс **MPU.Appointments** | [**AppointmentCalendar** ](https://docs.microsoft.com/uwp/api/Windows.ApplicationModel.Appointments.AppointmentCalendar) класса |
| (MPU = **Microsoft.Phone.UserData**) <br/> Класс **MPU.Contacts** | [**ContactStore** ](https://docs.microsoft.com/uwp/api/Windows.ApplicationModel.Contacts.ContactStore) класса |
| Элементы управления и инфраструктура пользовательского интерфейса | |
| Класс **ControlTiltEffect.TiltEffect** | Анимации из библиотеки анимации среды выполнения Windows встроены в стили стандартных элементов управления по умолчанию. См. раздел [Анимация](wpsl-to-uwp-porting-xaml-and-ui.md). |
| Пространство имен **Microsoft.Phone.Controls** | [**Windows.UI.Xaml.Controls** ](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls) пространства имен |
| (MPC = **Microsoft.Phone.Controls**) <br/> Класс **MPC.ContextMenu** | [**PopupMenu** ](https://docs.microsoft.com/uwp/api/Windows.UI.Popups.PopupMenu) класса |
| (MPC = **Microsoft.Phone.Controls**) <br/>Класс **MPC.DatePickerPage** | [**DatePickerFlyout** ](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.DatePickerFlyout) класса |
| (MPC = **Microsoft.Phone.Controls**) <br/>Класс **MPC.GestureListener** | [**GestureRecognizer** ](https://docs.microsoft.com/uwp/api/Windows.UI.Input.GestureRecognizer) класса |
| (MPC = **Microsoft.Phone.Controls**) <br/>Класс **MPC.LongListSelector** | [**Элемент управления SemanticZoom** ](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.SemanticZoom) класса |
| (MPC = **Microsoft.Phone.Controls**) <br/>Класс **MPC.ObscuredEventArgs** | [**SystemProtection**](https://docs.microsoft.com/uwp/api/Windows.Phone.System.SystemProtection), [ **WindowActivatedEventArgs** ](https://docs.microsoft.com/uwp/api/Windows.UI.Core.WindowActivatedEventArgs) классы |
| (MPC = **Microsoft.Phone.Controls**) <br/>Класс**MPC.Panorama** | [**Центр** ](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Hub) класса |
| (MPC = **Microsoft.Phone.Controls**) <br/>**MPC.PhoneApplicationFrame**,<br/>(SWN = **System.Windows.Navigation**) <br/>Классы **SWN.NavigationService** | [**Кадр** ](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Frame) класса |
| (MPC = **Microsoft.Phone.Controls**) <br/>Класс **MPC.PhoneApplicationPage** | [**Страница** ](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Page) класса |
| (MPC = **Microsoft.Phone.Controls**) <br/>Класс **MPC.TiltEffect** | [**PointerDownThemeAnimation** ](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.Animation.PointerDownThemeAnimation) класса |
| (MPC = **Microsoft.Phone.Controls**) <br/>Класс **MPC.TimePickerPage** | [**TimePickerFlyout** ](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.TimePickerFlyout) класса |
| (MPC = **Microsoft.Phone.Controls**) <br/>Класс **MPC.WebBrowser** | [**Веб-представления** ](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.WebView) класса |
| (MPC = **Microsoft.Phone.Controls**) <br/>Класс **MPC.WebBrowserExtensions** | Нет прямого эквивалента |
| (MPC = **Microsoft.Phone.Controls**) <br/>Класс **MPC.WrapPanel** | Нет прямого эквивалента для макетирования. [**ItemsWrapGrid** ](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.ItemsWrapGrid) и [ **WrapGrid** ](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.WrapGrid) может использоваться в шаблоне элементов панели элементов элемента управления. |
| (MPD = **Microsoft.Phone.Data**) <br/>Пространство имен **MPD.Linq** | Нет прямого эквивалента |
| (MPD = **Microsoft.Phone.Data**) <br/>Пространство имен **MPD.Linq.Mapping** | Нет прямого эквивалента |
| Пространство имен **Microsoft.Phone.Globalization** | Нет прямого эквивалента |
| (MPI = **Microsoft.Phone.Info**) <br/>Классы **MPI.DeviceExtendedProperties**, **DeviceStatus** | [**EasClientDeviceInformation**](https://docs.microsoft.com/uwp/api/Windows.Security.ExchangeActiveSyncProvisioning.EasClientDeviceInformation), [ **MemoryManager** ](https://docs.microsoft.com/uwp/api/Windows.System.MemoryManager) классы. Дополнительные сведения см. в разделе [Состояние устройства](wpsl-to-uwp-input-and-sensors.md). |
| (MPI = **Microsoft.Phone.Info**) <br/>Класс **MPI.MediaCapabilities** | Нет прямого эквивалента |
| (MPI = **Microsoft.Phone.Info**) <br/>Класс **MPI.UserExtendedProperties** | [**AdvertisingManager** ](https://docs.microsoft.com/uwp/api/Windows.System.UserProfile.AdvertisingManager) класса |
| Пространство имен **System.Windows** | [**Windows.UI.Xaml** ](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml) пространства имен |
| Пространство имен **System.Windows.Automation** | [**Windows.UI.Xaml.Automation** ](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Automation) пространства имен |
| Пространства имен **System.Windows.Controls**, **System.Windows.Input** | [**Windows.UI.Core**](https://docs.microsoft.com/uwp/api/Windows.UI.Core), [ **Windows.UI.Input**](https://docs.microsoft.com/uwp/api/Windows.UI.Input), [ **Windows.UI.Xaml.Controls** ](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls) пространств имен |
| Классы **System.Windows.Controls.DrawingSurface**, **DrawingSurfaceBackgroundGrid** | [**SwapChainPanel** ](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.SwapChainPanel) класса |
| Класс **System.Windows.Controls.RichTextBox** | [**RichEditBox** ](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.RichEditBox) класса |
| Класс **System.Windows.Controls.WrapPanel** | Нет прямого эквивалента для макетирования. [**ItemsWrapGrid** ](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.ItemsWrapGrid) и [ **WrapGrid** ](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.WrapGrid) может использоваться в шаблоне элементов панели элементов элемента управления. |
| Пространство имен **System.Windows.Controls.Primitives** | [**Windows.UI.Xaml.Controls.Primitives** ](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Primitives) пространства имен |
| Пространство имен **System.Windows.Controls.Shapes** | [**Windows.UI.Xaml.Controls.Shapes** ](/uwp/api/Windows.UI.Xaml.Shapes) пространства имен |
| Пространство имен **System.Windows.Data** | [**Windows.UI.Xaml.Data** ](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Data) пространства имен |
| Пространство имен **System.Windows.Documents** | [**Windows.UI.Xaml.Documents** ](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Documents) пространства имен |
| Пространство имен **System.Windows.Ink** | Нет прямого эквивалента |
| Пространство имен **System.Windows.Markup** | [**Windows.UI.Xaml.Markup** ](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Markup) пространства имен | 
| Пространство имен **System.Windows.Navigation** | [**Windows.UI.Xaml.Navigation** ](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Navigation) пространства имен |
| Событие **System.Windows.UIElement.Tap**, делегат **EventHandler&lt;GestureEventArgs&gt;** | [**Касание** ](https://docs.microsoft.com/uwp/api/windows.ui.xaml.uielement.tapped) событий, [ **TappedEventHandler** ](https://docs.microsoft.com/uwp/api/windows.ui.xaml.input.tappedeventhandler) делегата |
| Данные и службы |  |
| Класс **System.Data.Linq.DataContext** | Нет прямого эквивалента |
| Класс **System.Data.Linq.Mapping.ColumnAttribute** | Нет прямого эквивалента |
| Класс **System.Data.Linq.SqlClient.SqlHelpers** | Нет прямого эквивалента |
| Устройства | |
| Пространства имен **Microsoft.Devices**, **Microsoft.Devices.Sensors** | [**Windows.Devices.Enumeration**](https://docs.microsoft.com/uwp/api/Windows.Devices.Enumeration), [ **Windows.Devices.Enumeration.Pnp**](https://docs.microsoft.com/uwp/api/Windows.Devices.Enumeration.Pnp), [ **Windows.Devices.Input** ](https://docs.microsoft.com/uwp/api/Windows.Devices.Input), [ **Windows.Devices.Sensors** ](https://docs.microsoft.com/uwp/api/Windows.Devices.Sensors) пространств имен |
| Классы **Microsoft.Devices.Camera**, **Microsoft.Devices.PhotoCamera** | [**MediaCapture** ](https://docs.microsoft.com/uwp/api/Windows.Media.Capture.MediaCapture) класса. Также класс [**CameraCaptureUI**](https://docs.microsoft.com/uwp/api/Windows.Media.Capture.CameraCaptureUI) (только Windows). |
| Класс **Microsoft.Devices.CameraButtons** | [**HardwareButtons** ](https://docs.microsoft.com/uwp/api/Windows.Phone.UI.Input.HardwareButtons) класса |
| Класс **Microsoft.Devices.CameraVideoBrushExtensions** | [**CaptureElement** ](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.CaptureElement) класса |
| Класс **Microsoft.Devices.Environment** | Нет прямого эквивалента. В качестве обходного пути используйте условную компиляцию и укажите настраиваемый символ. Или можно создать обходной путь с помощью свойства [IsAttached](https://docs.microsoft.com/dotnet/api/system.diagnostics.debugger.isattached?redirectedfrom=MSDN#System_Diagnostics_Debugger_IsAttached). |
| Класс **Microsoft.Devices.MediaHistory** | Нет прямого эквивалента |
| Класс **Microsoft.Devices.VibrateController** | [**VibrationDevice** ](https://docs.microsoft.com/uwp/api/Windows.Phone.Devices.Notification.VibrationDevice) класса |
| Класс **Microsoft.Devices.Radio.FMRadio** | Нет прямого эквивалента |
| Классы **Microsoft.Devices.Sensors.Accelerometer**, **Compass** | В пространстве имен [**Windows.Devices.Sensors**](https://docs.microsoft.com/uwp/api/Windows.Devices.Sensors) |
| Класс **Microsoft.Devices.Sensors.Gyroscope** | [**Гирометр** ](https://docs.microsoft.com/uwp/api/Windows.Devices.Sensors.Gyrometer) класса |
| Класс **Microsoft.Devices.Sensors.Motion** | [**Инклинометра** ](https://docs.microsoft.com/uwp/api/Windows.Devices.Sensors.Inclinometer) класса |
| Глобализация | |
| Пространство имен **System.Globalization** | [**Windows.Globalization** ](https://docs.microsoft.com/uwp/api/Windows.Globalization) пространства имен |
| (ST = **System.Threading**) <br/> Свойство **ST.Thread.CurrentCulture** | (SG = **System.Globalization**) <br/> Свойство **S.CultureInfo.CurrentCulture** |
| (ST = **System.Threading**) <br/> Свойство **ST.Thread.CurrentUICulture** | (SG = **System.Globalization**) <br/> Свойство **S.CultureInfo.CurrentUICulture** |
| Графика и анимация | |
| **Microsoft.Xna.Framework. \***  пространства имен, [библиотеки классов XNA Framework](https://go.microsoft.com/fwlink/p/?LinkId=263769), [содержимого библиотеки классов конвейера](https://go.microsoft.com/fwlink/p/?LinkId=263770) | Нет прямого эквивалента. Обычно используется [Microsoft DirectX](https://docs.microsoft.com/windows/desktop/directx) и C++. См. разделы [Разработка игр](https://docs.microsoft.com/previous-versions/windows/apps/hh452744(v=win.10)) и [Взаимодействие DirectX и XAML](https://docs.microsoft.com/previous-versions/windows/apps/hh825871(v=win.10)). |
| Класс **Microsoft.Xna.Framework.Audio.Microphone** | [**MediaCapture** ](https://docs.microsoft.com/uwp/api/Windows.Media.Capture.MediaCapture) класса |
| Класс **Microsoft.Xna.Framework.Audio.SoundEffect** | [**MediaElement** ](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.MediaElement) класса |
| Пространство имен **Microsoft.Xna.Framework.GamerServices** | (WPS = **Windows.Phone.System**) <br/> [**WPS.UserProfile.GameServices.Core**](https://docs.microsoft.com/uwp/api/Windows.Phone.System.UserProfile.GameServices.Core) namespace |
| Класс **Microsoft.Xna.Framework.GamerServices.Guide** | Нет прямого эквивалента |
| Класс **Microsoft.Xna.Framework.Input.GamePad** | [**HardwareButtons** ](https://docs.microsoft.com/uwp/api/Windows.Phone.UI.Input.HardwareButtons) класса |
| Класс **Microsoft.Xna.Framework.Input.Touch.TouchPanel** | [**GestureRecognizer** ](https://docs.microsoft.com/uwp/api/Windows.UI.Input.GestureRecognizer) класса |
| (MXFM = **Microsoft.Xna.Framework.Media**) <br/> Классы **MXFM.MediaLibrary**, **MXFM.PhoneExtensions.MediaLibraryExtensions** | [**KnownFolders** ](https://docs.microsoft.com/uwp/api/Windows.Storage.KnownFolders) класса |
| Класс **Microsoft.Xna.Framework.Media.MediaQueue** | [**SystemMediaTransportControls** ](https://docs.microsoft.com/uwp/api/Windows.Media.SystemMediaTransportControls) класса |
| Класс **Microsoft.Xna.Framework.Media.Playlist** | [**BackgroundMediaPlayer** ](https://docs.microsoft.com/uwp/api/Windows.Media.Playback.BackgroundMediaPlayer) класса |
| Пространство имен **System.Windows.Media** | [**Windows.UI.Xaml.Media** ](/uwp/api/Windows.UI.Xaml.Media) пространства имен |
| Класс **System.Windows.Media.RadialGradientBrush** | Нет прямого эквивалента. См. раздел [Мультимедиа и графика](wpsl-to-uwp-porting-xaml-and-ui.md). |
| Пространство имен **System.Windows.Media.Animation** | [**Windows.UI.Xaml.Media.Animation** ](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.Animation) пространства имен |
| Пространство имен **System.Windows.Media.Effects** | Нет прямого эквивалента |
| Пространство имен **System.Windows.Media.Imaging** | [**Windows.UI.Xaml.Media.Imaging** ](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.Imaging) пространства имен |
| Пространство имен **System.Windows.Media.Media3D** | [**Windows.UI.Xaml.Media.Media3D** ](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.Media3D) пространства имен |
| Пространство имен **System.Windows.Shapes** | [**Windows.UI.Xaml.Shapes** ](/uwp/api/Windows.UI.Xaml.Shapes) пространства имен |
| Средства запуска и выбора | |
| Классы **Microsoft.Phone.Tasks.AddressChooserTask**, **EmailAddressChooserTask**, **PhoneNumberChooserTask** | [**ContactPicker** ](https://docs.microsoft.com/uwp/api/Windows.ApplicationModel.Contacts.ContactPicker) класса |
| Классы **Microsoft.Phone.Tasks.AddWalletItemTask**, **AddWalletItemResult** | [**Windows.ApplicationModel.Wallet** ](https://docs.microsoft.com/uwp/api/Windows.ApplicationModel.Wallet) пространства имен |
| Классы **Microsoft.Phone.Tasks.BingMapsDirectionsTask**, **BingMapsTask** | Нет прямого эквивалента |
| Класс **Microsoft.Phone.Tasks.CameraCaptureTask** | [**MediaCapture** ](https://docs.microsoft.com/uwp/api/Windows.Media.Capture.MediaCapture) класса. Также класс [**CameraCaptureUI**](https://docs.microsoft.com/uwp/api/Windows.Media.Capture.CameraCaptureUI) (только Windows). |
| **Microsoft.Phone.Tasks.MarketplaceDetailTask** | [**CurrentApp** ](https://docs.microsoft.com/uwp/api/Windows.ApplicationModel.Store.CurrentApp) класс ([**RequestAppPurchaseAsync** ](https://docs.microsoft.com/uwp/api/windows.applicationmodel.store.currentapp.requestapppurchaseasync) метод) |
| Классы **Microsoft.Phone.Tasks.ConnectionSettingsTask**, **MarketplaceHubTask**, **MarketplaceReviewTask**, **MarketplaceSearchTask**, **MediaPlayerLauncher**, **SearchTask**, **SmsComposeTask**, **WebBrowserTask** | [**Средство запуска** ](https://docs.microsoft.com/uwp/api/Windows.System.Launcher) класса |
| Класс **Microsoft.Phone.Tasks.EmailComposeTask** | [**EmailMessage** ](https://docs.microsoft.com/uwp/api/Windows.ApplicationModel.Email.EmailMessage) класса |
| Класс **Microsoft.Phone.Tasks.GameInviteTask** | Нет прямого эквивалента |
| Классы **Microsoft.Phone.Tasks.MapDownloaderTask**, **MapsDirectionsTask**, **MapsTask**, **MapUpdaterTask** | Нет прямого эквивалента |
| Класс **Microsoft.Phone.Tasks.PhoneCallTask** | [**PhoneCallManager** ](https://docs.microsoft.com/uwp/api/Windows.ApplicationModel.Calls.PhoneCallManager) класса |
| Класс **Microsoft.Phone.Tasks.PhotoChooserTask** | [**FileOpenPicker** ](https://docs.microsoft.com/uwp/api/Windows.Storage.Pickers.FileOpenPicker) класса |
| Класс **Microsoft.Phone.Tasks.SaveAppointmentTask** | [**AppointmentManager** ](https://docs.microsoft.com/uwp/api/Windows.ApplicationModel.Appointments.AppointmentManager) класса |
| Классы **Microsoft.Phone.Tasks.SaveContactTask**, **SaveEmailAddressTask**, **SavePhoneNumberTask** | [**StoredContact** ](https://docs.microsoft.com/uwp/api/Windows.Phone.PersonalInformation.StoredContact) класс (только для Windows Phone) | 
| Класс **Microsoft.Phone.Tasks.SaveRingtoneTask** | Нет прямого эквивалента |
| Классы **Microsoft.Phone.Tasks.ShareLinkTask**, **ShareMediaTask**, **ShareStatusTask** | [**DataPackage** ](https://docs.microsoft.com/uwp/api/Windows.ApplicationModel.DataTransfer.DataPackage) класса |
| Location | |
| Пространство имен **System.Device.Location** | [**Windows.Devices.Geolocation** ](https://docs.microsoft.com/uwp/api/Windows.Devices.Geolocation) пространства имен |
| Класс **System.Device.GeoCoordinateWatcher** | [**Geolocator** ](https://docs.microsoft.com/uwp/api/Windows.Devices.Geolocation.Geolocator) класса |
| Карты | |
| Пространства имен **Microsoft.Phone.Maps** | [**Windows.Services.Maps** ](https://docs.microsoft.com/uwp/api/Windows.Services.Maps) пространства имен |
| Пространство имен **Microsoft.Phone.Maps.Controls** | [**Windows.UI.Xaml.Controls.Maps** ](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Maps) пространства имен |
| Класс **Microsoft.Phone.Maps.Controls.Map** | [**MapControl** ](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Maps.MapControl) класса |
| Пространство имен **Microsoft.Phone.Maps.Services** | [**Windows.Services.Maps** ](https://docs.microsoft.com/uwp/api/Windows.Services.Maps) пространства имен |
| Классы **Microsoft.Phone.Maps.Services.GeocodeQuery**, **ReverseGeocodeQuery** | [**MapLocationFinder** ](https://docs.microsoft.com/uwp/api/Windows.Services.Maps.MapLocationFinder) класса |
| Класс **System.Device.Location.GeoCoordinate** | [**Geopoint** ](https://docs.microsoft.com/uwp/api/Windows.Devices.Geolocation.Geopoint) класса |
| Класс **Microsoft.Phone.Maps.Services.Route** | [**MapRoute** ](https://docs.microsoft.com/uwp/api/Windows.Services.Maps.MapRoute) класса |
| Класс **Microsoft.Phone.Maps.Services.RouteQuery** | [**MapRouteFinder** ](https://docs.microsoft.com/uwp/api/Windows.Services.Maps.MapRouteFinder) класса |
| Получение дохода | |
| Пространство имен **Microsoft.Phone.Marketplace** | [**Windows.ApplicationModel.Store** ](https://docs.microsoft.com/uwp/api/Windows.ApplicationModel.Store) пространства имен |
| Media | |
| Пространство имен **Microsoft.Phone.Media** | [**MediaElement** ](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.MediaElement) класса |
| Сеть | |
| (MPNN = **Microsoft.Phone.Net.NetworkInformation**) <br/> Класс **MPNN.DeviceNetworkInformation** | [**Имя узла**](https://docs.microsoft.com/uwp/api/Windows.Networking.HostName), [ **NetworkInformation, соответствующей выбранному** ](https://docs.microsoft.com/uwp/api/Windows.Networking.Connectivity.NetworkInformation) классы |
| (MPNN = **Microsoft.Phone.Net.NetworkInformation**) <br/> Класс **MPNN.NetworkInterface** | [**NetworkInformation, соответствующей выбранному** ](https://docs.microsoft.com/uwp/api/Windows.Networking.Connectivity.NetworkInformation) класса |
| (MPNN = **Microsoft.Phone.Net.NetworkInformation**) <br/> Класс **MPNN.NetworkInterfaceInfo** | [**ConnectionProfile** ](https://docs.microsoft.com/uwp/api/Windows.Networking.Connectivity.ConnectionProfile) класса |
| (MPNN = **Microsoft.Phone.Net.NetworkInformation**) <br/> Класс **MPNN.NetworkInterfaceList** | [**NetworkInformation, соответствующей выбранному** ](https://docs.microsoft.com/uwp/api/Windows.Networking.Connectivity.NetworkInformation) класса |
| (MPNN = **Microsoft.Phone.Net.NetworkInformation**) <br/> Класс **MPNN.SocketExtensions** | Нет прямого эквивалента |
| (MPNN = **Microsoft.Phone.Net.NetworkInformation**) <br/> Класс **MPNN.WebRequestExtensions** | Нет прямого эквивалента |
| Пространство имен **Microsoft.Phone.Networking.Voip** | Нет прямого эквивалента |
| Класс **System.Net.CookieCollection** | По-прежнему поддерживается, только некоторые свойства отсутствуют (например, IsReadOnly) |
| Класс **System.Net.DownloadProgressChangedEventArgs** и похожие классы, связанные с **System.Net.WebClient** | [**HttpClient** ](https://docs.microsoft.com/uwp/api/Windows.Web.Http.HttpClient) класс (или [System.Net.Http.HttpClient](https://docs.microsoft.com/previous-versions/visualstudio/hh193681(v=vs.118))). Производные от [System.Net.Http.StreamContent](https://docs.microsoft.com/previous-versions/visualstudio/hh138119(v=vs.118)) для измерения хода выполнения. |
| Классы **System.Net.DnsEndPoint**, **IPAddress** | Эти классы все еще поддерживаются, но некоторые свойства отсутствуют. Можно также перенести в класс [**HostName**](https://docs.microsoft.com/uwp/api/Windows.Networking.HostName). |
| Класс **System.Net.HttpUtility** | [**HtmlFormatHelper** ](https://docs.microsoft.com/uwp/api/Windows.ApplicationModel.DataTransfer.HtmlFormatHelper) класса |
| Класс **System.Net.HttpWebRequest** | Частичная поддержка, при этом рекомендованной перспективной альтернативой является класс [**HttpClient**](https://docs.microsoft.com/uwp/api/Windows.Web.Http.HttpClient) (или [System.Net.Http.HttpClient](https://docs.microsoft.com/previous-versions/visualstudio/hh193681(v=vs.118))). Эти API-интерфейсы используют [System.Net.Http.HttpRequestMessage](https://docs.microsoft.com/previous-versions/visualstudio/hh159020(v=vs.118)) для представления запроса HTTP. |
| Класс **System.Net.HttpWebResponse** | По-прежнему поддерживается, но вместо Close() используйте Dispose(). Рекомендуемой перспективной альтернативой является класс [**HttpClient**](https://docs.microsoft.com/uwp/api/Windows.Web.Http.HttpClient) (или [System.Net.Http.HttpClient](https://docs.microsoft.com/previous-versions/visualstudio/hh193681(v=vs.118))). Эти API используют [System.Net.Http.HttpResponseMessage](https://docs.microsoft.com/dotnet/api/system.net.http.httpresponsemessage?redirectedfrom=MSDN) для представления ответа HTTP. |
| (SNN = **System.Net.NetworkInformation**) <br/> Класс **SNN.NetworkChange** | По-прежнему поддерживается за исключением конструктора. |
| Класс **System.Net.OpenReadCompletedEventArgs** и похожие классы, связанные с **System.Net.WebClient** | [**HttpClient** ](https://docs.microsoft.com/uwp/api/Windows.Web.Http.HttpClient) класс (или [System.Net.Http.HttpClient](https://docs.microsoft.com/previous-versions/visualstudio/hh193681(v=vs.118))) |
| Класс **System.Net.Sockets.Socket** | По-прежнему поддерживается, но вместо Close() используйте Dispose(). Можно также перенести в класс [**StreamSocket**](https://docs.microsoft.com/uwp/api/Windows.Networking.Sockets.StreamSocket). |
| Класс **System.Net.Sockets.SocketException** | По-прежнему поддерживается, но используйте свойство SocketErrorCode вместо ErrorCode. |
| Классы **System.Net.Sockets.UdpAnySourceMulticastClient**, **UdpSingleSourceMulticastClient** | [**DatagramSocket** ](https://docs.microsoft.com/uwp/api/Windows.Networking.Sockets.DatagramSocket) класса |
| Класс **System.Net.UploadProgressChangedEventArgs** и похожие классы, связанные с **System.Net.WebClient** | [**HttpClient** ](https://docs.microsoft.com/uwp/api/Windows.Web.Http.HttpClient) класс (или [System.Net.Http.HttpClient](https://docs.microsoft.com/previous-versions/visualstudio/hh193681(v=vs.118))) |
| Класс **System.Net.WebClient** | [**HttpClient** ](https://docs.microsoft.com/uwp/api/Windows.Web.Http.HttpClient) класс (или [System.Net.Http.HttpClient](https://docs.microsoft.com/previous-versions/visualstudio/hh193681(v=vs.118))) |
| Класс **System.Net.WebRequest** | Частичная поддержка (другой набор свойств), при этом рекомендованная перспективная альтернатива — класс [**HttpClient**](https://docs.microsoft.com/uwp/api/Windows.Web.Http.HttpClient) (или [System.Net.Http.HttpClient](https://docs.microsoft.com/previous-versions/visualstudio/hh193681(v=vs.118))). Эти API-интерфейсы используют [System.Net.Http.HttpRequestMessage](https://docs.microsoft.com/previous-versions/visualstudio/hh159020(v=vs.118)) для представления запроса HTTP. |
| Класс **System.Net.WebResponse** | По-прежнему поддерживается, но вместо Close() используйте Dispose(). Рекомендуемой перспективной альтернативой является класс [**HttpClient**](https://docs.microsoft.com/uwp/api/Windows.Web.Http.HttpClient) (или [System.Net.Http.HttpClient](https://docs.microsoft.com/previous-versions/visualstudio/hh193681(v=vs.118))). Эти API используют [System.Net.Http.HttpResponseMessage](https://docs.microsoft.com/dotnet/api/system.net.http.httpresponsemessage?redirectedfrom=MSDN) для представления ответа HTTP. |
| (SN = **System.Net**) <br/> Класс **SN.WriteStreamClosedEventArgs** | [**HttpClient** ](https://docs.microsoft.com/uwp/api/Windows.Web.Http.HttpClient) класс (или [System.Net.Http.HttpClient](https://docs.microsoft.com/previous-versions/visualstudio/hh193681(v=vs.118))) |
| (SN = **System.Net**) <br/> Класс **SN.WriteStreamClosedEventHandler** | [**HttpClient** ](https://docs.microsoft.com/uwp/api/Windows.Web.Http.HttpClient) класс (или [System.Net.Http.HttpClient](https://docs.microsoft.com/previous-versions/visualstudio/hh193681(v=vs.118))) |
| Класс **System.UriFormatException** | Класс **System.FormatException** |
| Уведомления | |
| Пространство имен MPN = **Microsoft.Phone.Notification** | [**Windows.UI.Notifications**](https://docs.microsoft.com/uwp/api/Windows.UI.Notifications), [ **Windows.Networking.PushNotifications** ](https://docs.microsoft.com/uwp/api/Windows.Networking.PushNotifications) пространств имен |
| MPN = **Microsoft.Phone.Notification** <br/> Класс **MPN.HttpNotification** | [**TileNotification** ](https://docs.microsoft.com/uwp/api/Windows.UI.Notifications.TileNotification) класса |
| MPN = **Microsoft.Phone.Notification** <br/> Класс **MPN.HttpNotificationChannel** | [**PushNotificationChannel** ](https://docs.microsoft.com/uwp/api/Windows.Networking.PushNotifications.PushNotificationChannel) класса |
| Программирование | |
| Пространство имен **System** | [**Windows.Foundation** ](https://docs.microsoft.com/uwp/api/Windows.Foundation) пространства имен |
| Классы **System.Diagnostics.StackFrame**, **StackTrace** | Нет прямого эквивалента |
| Пространство имен **System.Diagnostics** | [**Windows.Foundation.Diagnostics** ](https://docs.microsoft.com/uwp/api/Windows.Foundation.Diagnostics) пространства имен |
| Интерфейс **System.ICloneable** | Пользовательский метод, который возвращает соответствующий тип. |
| Класс **System.Reflection.Emit.ILGenerator** | Нет прямого эквивалента |
| Реактивные расширения | |
| Пространство имен **Microsoft.Phone.Reactive** | Нет прямого эквивалента |
| Отражение | |
| Класс **System.Type** | Класс **System.Reflection.TypeInfo** См. раздел [Отражение в .NET Framework для приложений UWP](https://docs.microsoft.com/dotnet/framework/reflection-and-codedom/reflection-for-windows-store-apps). |
| Ресурсы | |
| Класс **System.Resources.ResourceManager** | (WA = **Windows.ApplicationModel**)<br/>[**ШТАТ ВАШИНГТОН. Resources.Core** ](https://docs.microsoft.com/uwp/api/Windows.ApplicationModel.Resources.Core) и [ **штат Вашингтон. Ресурсы** ](https://docs.microsoft.com/uwp/api/Windows.ApplicationModel.Resources) пространства имен, [ **ResourceManager** ](https://docs.microsoft.com/uwp/api/Windows.ApplicationModel.Resources.Core.ResourceManager) класса. См. раздел [Создание и получение ресурсов в приложениях среды выполнения Windows](https://docs.microsoft.com/previous-versions/windows/apps/hh694557(v=vs.140)). |
| Защищенный элемент | |
| (MPS = **Microsoft.Phone.SecureElement**) <br/> Классы **MPS.SecureElementChannel**, **MPS.SecureElementSession** | [**SmartCardConnection** ](https://docs.microsoft.com/uwp/api/Windows.Devices.SmartCards.SmartCardConnection) класса |
| (MPS = **Microsoft.Phone.SecureElement**) <br/> Класс **MPS.SecureElementReader** | [**SmartCardReader** ](https://docs.microsoft.com/uwp/api/Windows.Devices.SmartCards.SmartCardReader) класса |
| Безопасность | |
| (SSC = **System.Security.Cryptography**) <br/> Классы **SSC.Aes**, **SSC.RSA** | [**CryptographicEngine** ](https://docs.microsoft.com/uwp/api/Windows.Security.Cryptography.Core.CryptographicEngine) класса |
| (SSC = **System.Security.Cryptography**) <br/> Классы **SSC.HMACSHA256**, **SSC.SHA256** | [**HashAlgorithmProvider** ](https://docs.microsoft.com/uwp/api/Windows.Security.Cryptography.Core.HashAlgorithmProvider) класса |
| (SSC = **System.Security.Cryptography**) <br/> Класс **SSC.ProtectedData** | [**DataProtectionProvider** ](https://docs.microsoft.com/uwp/api/Windows.Security.Cryptography.DataProtection.DataProtectionProvider) класса |
| (SSC = **System.Security.Cryptography**) <br/> Класс **SSC.RandomNumberGenerator** | [**CryptographicBuffer** ](https://docs.microsoft.com/uwp/api/Windows.Security.Cryptography.CryptographicBuffer) класса |
| (SSC = **System.Security.Cryptography**) <br/> Класс **SSC.X509Certificates.X509Certificate** | [**CertificateEnrollmentManager** ](https://docs.microsoft.com/uwp/api/Windows.Security.Cryptography.Certificates.CertificateEnrollmentManager) класса |
| Shell | |
| (MPSh = **Microsoft.Phone.Shell**) <br/> Класс **MPSh.ApplicationBar** | [**CommandBar** ](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.CommandBar) класса |
| (MPSh = **Microsoft.Phone.Shell**) <br/> Класс **MPSh.ApplicationBarIconButton** | [**AppBarButton** ](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.AppBarButton) класса (при использовании внутри [ **PrimaryCommands** ](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.commandbar.primarycommands) свойство) |
| (MPSh = **Microsoft.Phone.Shell**) <br/> Класс **MPSh.ApplicationBarMenuItem** | [**AppBarButton** ](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.AppBarButton) класса (при использовании внутри [ **SecondaryCommands** ](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.commandbar.secondarycommands) свойство) |
| (MPSh = **Microsoft.Phone.Shell**) <br/> Классы **MPSh.CycleTileData**, **MPSh.FlipTileData**, **MPSh.IconicTileData**, **MPSh.ShellTileData**, **MPSh.StandardTileData** | [**TileTemplateType** ](https://docs.microsoft.com/uwp/api/Windows.UI.Notifications.TileTemplateType) класса |
| (MPSh = **Microsoft.Phone.Shell**) <br/> Класс **MPSh.PhoneApplicationService** | [**CoreApplication**](https://docs.microsoft.com/uwp/api/Windows.ApplicationModel.Core.CoreApplication), [ **DisplayRequest** ](https://docs.microsoft.com/uwp/api/Windows.System.Display.DisplayRequest) классы |
| (MPSh = **Microsoft.Phone.Shell**) <br/> Класс **MPSh.ProgressIndicator** | [**StatusBarProgressIndicator** ](https://docs.microsoft.com/uwp/api/Windows.UI.ViewManagement.StatusBarProgressIndicator) класса |
| (MPSh = **Microsoft.Phone.Shell**) <br/> Класс **MPSh.ShellTile** | [**SecondaryTile** ](https://docs.microsoft.com/uwp/api/Windows.UI.StartScreen.SecondaryTile) класса |
| (MPSh = **Microsoft.Phone.Shell**) <br/> Класс **MPSh.ShellTileSchedule** | [**TileUpdater** ](https://docs.microsoft.com/uwp/api/Windows.UI.Notifications.TileUpdater) класса |
| (MPSh = **Microsoft.Phone.Shell**) <br/> Класс **MPSh.ShellToast** | [**ToastNotificationManager** ](https://docs.microsoft.com/uwp/api/Windows.UI.Notifications.ToastNotificationManager) класса |
| (MPSh = **Microsoft.Phone.Shell**) <br/> Класс **MPSh.SystemTray** | [**StatusBar** ](https://docs.microsoft.com/uwp/api/Windows.UI.ViewManagement.StatusBar) класса |
| Хранилище и ввод-вывод | |
| Классы **Microsoft.Phone.Storage.ExternalStorage**, **ExternalStorageDevice**, **ExternalStorageFile**, **ExternalStorageFolder** | [**KnownFolders** ](https://docs.microsoft.com/uwp/api/Windows.Storage.KnownFolders) класса |
| Пространство имен **System.IO** | [**Windows.Storage**](https://docs.microsoft.com/uwp/api/Windows.Storage), [ **Windows.Storage.Streams** ](https://docs.microsoft.com/uwp/api/Windows.Storage.Streams) пространств имен |
| Класс **System.IO.Directory** | [**StorageFolder** ](https://docs.microsoft.com/uwp/api/Windows.Storage.StorageFolder) класса |
| Класс **System.IO.File** | [**StorageFile** ](https://docs.microsoft.com/uwp/api/Windows.Storage.StorageFile) и [ **PathIO** ](https://docs.microsoft.com/uwp/api/Windows.Storage.PathIO) классы
| (SII = **System.IO.IsolatedStorage**) <br/> Класс **SII.IsolatedStorageFile** |[**ApplicationData.LocalFolder** ](https://docs.microsoft.com/uwp/api/windows.storage.applicationdata.localfolder) свойство |
| (SII = **System.IO.IsolatedStorage**) <br/> Класс **SII.IsolatedStorageSettings** | [**ApplicationData.LocalSettings** ](https://docs.microsoft.com/uwp/api/windows.storage.applicationdata.localsettings) свойство |
| Класс **System.IO.Stream** | По-прежнему поддерживается, используйте ReadAsync() и WriteAsync() вместо BeginRead()/EndRead() и BeginWrite()/EndWrite(). |
| Кошелек | |
| Пространство имен **Microsoft.Phone.Wallet** | [**Windows.ApplicationModel.Wallet** ](https://docs.microsoft.com/uwp/api/Windows.ApplicationModel.Wallet) пространства имен |
| Xml | |
| (SX = **System.Xml**) | Метод **SX.XmlConvert.ToDateTime** |
| (SX = **System.Xml**) | Метод **SX.XmlConvert.ToDateTimeOffset** |


Следующий раздел называется [Перенос проекта](wpsl-to-uwp-porting-to-a-uwp-project.md).


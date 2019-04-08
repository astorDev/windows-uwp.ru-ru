---
description: В этом разделе приведено полное сопоставление API-интерфейсы для Windows Phone Silverlight в их эквиваленты универсальной платформы Windows (UWP).
title: Windows Phone Silverlight для сопоставления пространства имен и класса универсальной платформы Windows
ms.assetid: 33f06706-4790-48f3-a2e4-ebef9ddb61a4
ms.date: 02/08/2017
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: 9acd42f57117fb01eef4ba8f87d35664be21cf32
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57634979"
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
| Класс **Microsoft.Advertising.Mobile.UI.AdControl** | Класс [AdControl](https://msdn.microsoft.com/library/advertising-windows-sdk-api-reference-adcontrol.aspx) |
| Сигналы, напоминания и фоновые агенты | |
| Класс **Microsoft.Phone.BackgroundAgent** | [**BackgroundTaskBuilder** ](https://msdn.microsoft.com/library/windows/apps/br224768) класса |
| Пространство имен **Microsoft.Phone.Scheduler** | [**Windows.ApplicationModel.Background** ](https://msdn.microsoft.com/library/windows/apps/br224847) пространства имен |
| Класс **Microsoft.Phone.Scheduler.Alarm** | [**BackgroundTaskBuilder** ](https://msdn.microsoft.com/library/windows/apps/br224768) и [ **ToastNotificationManager** ](https://msdn.microsoft.com/library/windows/apps/br208642) классы |
| Классы **Microsoft.Phone.Scheduler.PeriodicTask**, **ScheduledAction**, **ScheduledActionService**, **ScheduledTask**, **ScheduledTaskAgent** | [**BackgroundTaskBuilder** ](https://msdn.microsoft.com/library/windows/apps/br224768) класса |
| Класс **Microsoft.Phone.Scheduler.Reminder** | [**BackgroundTaskBuilder** ](https://msdn.microsoft.com/library/windows/apps/br224768) и [ **ToastNotificationManager** ](https://msdn.microsoft.com/library/windows/apps/br208642) классы |
| Класс **Microsoft.Phone.PictureDecoder** | [**BitmapDecoder** ](https://msdn.microsoft.com/library/windows/apps/br226176) класса |
| Пространство имен **Microsoft.Phone.BackgroundAudio** | [**Windows.Media.Playback** ](https://msdn.microsoft.com/library/windows/apps/dn640562) пространства имен |
| Пространство имен **Microsoft.Phone.BackgroundTransfer** | [**Windows.Networking.BackgroundTransfer** ](https://msdn.microsoft.com/library/windows/apps/br207242) пространства имен |
| Модель и среда приложения | |
| Класс **System.AppDomain** | Нет прямого эквивалента. См. классы [**Application**](https://msdn.microsoft.com/library/windows/apps/br242324), [**CoreApplication**](https://msdn.microsoft.com/library/windows/apps/br225016) |
| Класс **System.Environment** | Нет прямого эквивалента |
| Класс **System.ComponentModel.Annotations**  | Нет прямого эквивалента |
| Класс **System.ComponentModel.BackgroundWorker** | [**ThreadPool** ](https://msdn.microsoft.com/library/windows/apps/br229621) класса |
| Класс **System.ComponentModel.DesignerProperties** | [**DesignMode** ](https://msdn.microsoft.com/library/windows/apps/br224664) класса |
| Классы **System.Threading.Thread**, **System.Threading.ThreadPool** | [**ThreadPool** ](https://msdn.microsoft.com/library/windows/apps/br229621) класса |
| (ST = **System.Threading**) <br/> Метод **ST.Thread.MemoryBarrier** | (ST = **System.Threading**) <br/> Метод **ST.Interlocked.MemoryBarrier** |
| (ST = **System.Threading**) <br/> Свойство **ST.Thread.ManagedThreadId** | (S = **System**) <br/> Свойство **S.Environment.ManagedThreadId** |
| Класс **System.Threading.Timer** | [**ThreadPoolTimer** ](https://msdn.microsoft.com/library/windows/apps/br230587) класса |
| (SWT = **System.Windows.Threading**) <br/> Класс **SWT.Dispatcher** | [**CoreDispatcher** ](https://msdn.microsoft.com/library/windows/apps/br208211) класса |
| (SWT = **System.Windows.Threading**) <br/> Класс **SWT.DispatcherTimer** | [**DispatcherTimer** ](https://msdn.microsoft.com/library/windows/apps/br244250) класса |
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
| Пространство имен **Microsoft.Phone.UserData** | [**Windows.ApplicationModel.Contacts**](https://msdn.microsoft.com/library/windows/apps/br225002), [ **Windows.ApplicationModel.Appointments** ](https://msdn.microsoft.com/library/windows/apps/dn263359) пространств имен |
| (MPU = **Microsoft.Phone.UserData**) <br/> Классы **MPU.Account**, **ContactAddress**, **ContactCompanyInformation**, **ContactEmailAddress**, **ContactPhoneNumber** | [**Обратитесь к** ](https://msdn.microsoft.com/library/windows/apps/br224849) класса |
| (MPU = **Microsoft.Phone.UserData**) <br/> Класс **MPU.Appointments** | [**AppointmentCalendar** ](https://msdn.microsoft.com/library/windows/apps/dn596134) класса |
| (MPU = **Microsoft.Phone.UserData**) <br/> Класс **MPU.Contacts** | [**ContactStore** ](https://msdn.microsoft.com/library/windows/apps/dn624859) класса |
| Элементы управления и инфраструктура пользовательского интерфейса | |
| Класс **ControlTiltEffect.TiltEffect** | Анимации из библиотеки анимации среды выполнения Windows встроены в стили стандартных элементов управления по умолчанию. См. раздел [Анимация](wpsl-to-uwp-porting-xaml-and-ui.md). |
| Пространство имен **Microsoft.Phone.Controls** | [**Windows.UI.Xaml.Controls** ](https://msdn.microsoft.com/library/windows/apps/br227716) пространства имен |
| (MPC = **Microsoft.Phone.Controls**) <br/> Класс **MPC.ContextMenu** | [**PopupMenu** ](https://msdn.microsoft.com/library/windows/apps/br208693) класса |
| (MPC = **Microsoft.Phone.Controls**) <br/>Класс **MPC.DatePickerPage** | [**DatePickerFlyout** ](https://msdn.microsoft.com/library/windows/apps/dn625013) класса |
| (MPC = **Microsoft.Phone.Controls**) <br/>Класс **MPC.GestureListener** | [**GestureRecognizer** ](https://msdn.microsoft.com/library/windows/apps/br241937) класса |
| (MPC = **Microsoft.Phone.Controls**) <br/>Класс **MPC.LongListSelector** | [**Элемент управления SemanticZoom** ](https://msdn.microsoft.com/library/windows/apps/hh702601) класса |
| (MPC = **Microsoft.Phone.Controls**) <br/>Класс **MPC.ObscuredEventArgs** | [**SystemProtection**](https://msdn.microsoft.com/library/windows/apps/jj585394), [ **WindowActivatedEventArgs** ](https://msdn.microsoft.com/library/windows/apps/br208377) классы |
| (MPC = **Microsoft.Phone.Controls**) <br/>Класс**MPC.Panorama** | [**Центр** ](https://msdn.microsoft.com/library/windows/apps/dn251843) класса |
| (MPC = **Microsoft.Phone.Controls**) <br/>**MPC.PhoneApplicationFrame**,<br/>(SWN = **System.Windows.Navigation**) <br/>Классы **SWN.NavigationService** | [**Кадр** ](https://msdn.microsoft.com/library/windows/apps/br242682) класса |
| (MPC = **Microsoft.Phone.Controls**) <br/>Класс **MPC.PhoneApplicationPage** | [**Страница** ](https://msdn.microsoft.com/library/windows/apps/br227503) класса |
| (MPC = **Microsoft.Phone.Controls**) <br/>Класс **MPC.TiltEffect** | [**PointerDownThemeAnimation** ](https://msdn.microsoft.com/library/windows/apps/hh969164) класса |
| (MPC = **Microsoft.Phone.Controls**) <br/>Класс **MPC.TimePickerPage** | [**TimePickerFlyout** ](https://msdn.microsoft.com/library/windows/apps/dn608313) класса |
| (MPC = **Microsoft.Phone.Controls**) <br/>Класс **MPC.WebBrowser** | [**Веб-представления** ](https://msdn.microsoft.com/library/windows/apps/br227702) класса |
| (MPC = **Microsoft.Phone.Controls**) <br/>Класс **MPC.WebBrowserExtensions** | Нет прямого эквивалента |
| (MPC = **Microsoft.Phone.Controls**) <br/>Класс **MPC.WrapPanel** | Нет прямого эквивалента для макетирования. [**ItemsWrapGrid** ](https://msdn.microsoft.com/library/windows/apps/dn298849) и [ **WrapGrid** ](https://msdn.microsoft.com/library/windows/apps/br227717) может использоваться в шаблоне элементов панели элементов элемента управления. |
| (MPD = **Microsoft.Phone.Data**) <br/>Пространство имен **MPD.Linq** | Нет прямого эквивалента |
| (MPD = **Microsoft.Phone.Data**) <br/>Пространство имен **MPD.Linq.Mapping** | Нет прямого эквивалента |
| Пространство имен **Microsoft.Phone.Globalization** | Нет прямого эквивалента |
| (MPI = **Microsoft.Phone.Info**) <br/>Классы **MPI.DeviceExtendedProperties**, **DeviceStatus** | [**EasClientDeviceInformation**](https://msdn.microsoft.com/library/windows/apps/hh701390), [ **MemoryManager** ](https://msdn.microsoft.com/library/windows/apps/dn633831) классы. Дополнительные сведения см. в разделе [Состояние устройства](wpsl-to-uwp-input-and-sensors.md). |
| (MPI = **Microsoft.Phone.Info**) <br/>Класс **MPI.MediaCapabilities** | Нет прямого эквивалента |
| (MPI = **Microsoft.Phone.Info**) <br/>Класс **MPI.UserExtendedProperties** | [**AdvertisingManager** ](https://msdn.microsoft.com/library/windows/apps/dn363391) класса |
| Пространство имен **System.Windows** | [**Windows.UI.Xaml** ](https://msdn.microsoft.com/library/windows/apps/br209045) пространства имен |
| Пространство имен **System.Windows.Automation** | [**Windows.UI.Xaml.Automation** ](https://msdn.microsoft.com/library/windows/apps/br209179) пространства имен |
| Пространства имен **System.Windows.Controls**, **System.Windows.Input** | [**Windows.UI.Core**](https://msdn.microsoft.com/library/windows/apps/br208383), [ **Windows.UI.Input**](https://msdn.microsoft.com/library/windows/apps/br242084), [ **Windows.UI.Xaml.Controls** ](https://msdn.microsoft.com/library/windows/apps/br227716) пространств имен |
| Классы **System.Windows.Controls.DrawingSurface**, **DrawingSurfaceBackgroundGrid** | [**SwapChainPanel** ](https://msdn.microsoft.com/library/windows/apps/dn252834) класса |
| Класс **System.Windows.Controls.RichTextBox** | [**RichEditBox** ](https://msdn.microsoft.com/library/windows/apps/br227548) класса |
| Класс **System.Windows.Controls.WrapPanel** | Нет прямого эквивалента для макетирования. [**ItemsWrapGrid** ](https://msdn.microsoft.com/library/windows/apps/dn298849) и [ **WrapGrid** ](https://msdn.microsoft.com/library/windows/apps/br227717) может использоваться в шаблоне элементов панели элементов элемента управления. |
| Пространство имен **System.Windows.Controls.Primitives** | [**Windows.UI.Xaml.Controls.Primitives** ](https://msdn.microsoft.com/library/windows/apps/br209818) пространства имен |
| Пространство имен **System.Windows.Controls.Shapes** | [**Windows.UI.Xaml.Controls.Shapes** ](/uwp/api/Windows.UI.Xaml.Shapes) пространства имен |
| Пространство имен **System.Windows.Data** | [**Windows.UI.Xaml.Data** ](https://msdn.microsoft.com/library/windows/apps/br209917) пространства имен |
| Пространство имен **System.Windows.Documents** | [**Windows.UI.Xaml.Documents** ](https://msdn.microsoft.com/library/windows/apps/br209984) пространства имен |
| Пространство имен **System.Windows.Ink** | Нет прямого эквивалента |
| Пространство имен **System.Windows.Markup** | [**Windows.UI.Xaml.Markup** ](https://msdn.microsoft.com/library/windows/apps/br228046) пространства имен | 
| Пространство имен **System.Windows.Navigation** | [**Windows.UI.Xaml.Navigation** ](https://msdn.microsoft.com/library/windows/apps/br243300) пространства имен |
| Событие **System.Windows.UIElement.Tap**, делегат **EventHandler&lt;GestureEventArgs&gt;** | [**Касание** ](https://msdn.microsoft.com/library/windows/apps/br208985) событий, [ **TappedEventHandler** ](https://msdn.microsoft.com/library/windows/apps/br227993) делегата |
| Данные и службы |  |
| Класс **System.Data.Linq.DataContext** | Нет прямого эквивалента |
| Класс **System.Data.Linq.Mapping.ColumnAttribute** | Нет прямого эквивалента |
| Класс **System.Data.Linq.SqlClient.SqlHelpers** | Нет прямого эквивалента |
| Устройства | |
| Пространства имен **Microsoft.Devices**, **Microsoft.Devices.Sensors** | [**Windows.Devices.Enumeration**](https://msdn.microsoft.com/library/windows/apps/br225459), [ **Windows.Devices.Enumeration.Pnp**](https://msdn.microsoft.com/library/windows/apps/br225517), [ **Windows.Devices.Input** ](https://msdn.microsoft.com/library/windows/apps/br225648), [ **Windows.Devices.Sensors** ](https://msdn.microsoft.com/library/windows/apps/br206408) пространств имен |
| Классы **Microsoft.Devices.Camera**, **Microsoft.Devices.PhotoCamera** | [**MediaCapture** ](https://msdn.microsoft.com/library/windows/apps/br241124) класса. Также класс [**CameraCaptureUI**](https://msdn.microsoft.com/library/windows/apps/br241030) (только Windows). |
| Класс **Microsoft.Devices.CameraButtons** | [**HardwareButtons** ](https://msdn.microsoft.com/library/windows/apps/jj207557) класса |
| Класс **Microsoft.Devices.CameraVideoBrushExtensions** | [**CaptureElement** ](https://msdn.microsoft.com/library/windows/apps/br209278) класса |
| Класс **Microsoft.Devices.Environment** | Нет прямого эквивалента. В качестве обходного пути используйте условную компиляцию и укажите настраиваемый символ. Или можно создать обходной путь с помощью свойства [IsAttached](https://msdn.microsoft.com/library/e299w87h.aspx). |
| Класс **Microsoft.Devices.MediaHistory** | Нет прямого эквивалента |
| Класс **Microsoft.Devices.VibrateController** | [**VibrationDevice** ](https://msdn.microsoft.com/library/windows/apps/jj207230) класса |
| Класс **Microsoft.Devices.Radio.FMRadio** | Нет прямого эквивалента |
| Классы **Microsoft.Devices.Sensors.Accelerometer**, **Compass** | В пространстве имен [**Windows.Devices.Sensors**](https://msdn.microsoft.com/library/windows/apps/br206408) |
| Класс **Microsoft.Devices.Sensors.Gyroscope** | [**Гирометр** ](https://msdn.microsoft.com/library/windows/apps/br225718) класса |
| Класс **Microsoft.Devices.Sensors.Motion** | [**Инклинометра** ](https://msdn.microsoft.com/library/windows/apps/br225766) класса |
| Глобализация | |
| Пространство имен **System.Globalization** | [**Windows.Globalization** ](https://msdn.microsoft.com/library/windows/apps/br206813) пространства имен |
| (ST = **System.Threading**) <br/> Свойство **ST.Thread.CurrentCulture** | (SG = **System.Globalization**) <br/> Свойство **S.CultureInfo.CurrentCulture** |
| (ST = **System.Threading**) <br/> Свойство **ST.Thread.CurrentUICulture** | (SG = **System.Globalization**) <br/> Свойство **S.CultureInfo.CurrentUICulture** |
| Графика и анимация | |
| **Microsoft.Xna.Framework. \***  пространства имен, [библиотеки классов XNA Framework](https://go.microsoft.com/fwlink/p/?LinkId=263769), [содержимого библиотеки классов конвейера](https://go.microsoft.com/fwlink/p/?LinkId=263770) | Нет прямого эквивалента. Обычно используется [Microsoft DirectX](https://msdn.microsoft.com/library/windows/desktop/ee663274) и C++. См. разделы [Разработка игр](https://msdn.microsoft.com/library/windows/apps/hh452744) и [Взаимодействие DirectX и XAML](https://msdn.microsoft.com/library/windows/apps/hh825871). |
| Класс **Microsoft.Xna.Framework.Audio.Microphone** | [**MediaCapture** ](https://msdn.microsoft.com/library/windows/apps/br241124) класса |
| Класс **Microsoft.Xna.Framework.Audio.SoundEffect** | [**MediaElement** ](https://msdn.microsoft.com/library/windows/apps/br242926) класса |
| Пространство имен **Microsoft.Xna.Framework.GamerServices** | (WPS = **Windows.Phone.System**) <br/> [**WPS. UserProfile.GameServices.Core** ](https://msdn.microsoft.com/library/windows/apps/jj207609) пространства имен |
| Класс **Microsoft.Xna.Framework.GamerServices.Guide** | Нет прямого эквивалента |
| Класс **Microsoft.Xna.Framework.Input.GamePad** | [**HardwareButtons** ](https://msdn.microsoft.com/library/windows/apps/jj207557) класса |
| Класс **Microsoft.Xna.Framework.Input.Touch.TouchPanel** | [**GestureRecognizer** ](https://msdn.microsoft.com/library/windows/apps/br241937) класса |
| (MXFM = **Microsoft.Xna.Framework.Media**) <br/> Классы **MXFM.MediaLibrary**, **MXFM.PhoneExtensions.MediaLibraryExtensions** | [**KnownFolders** ](https://msdn.microsoft.com/library/windows/apps/br227151) класса |
| Класс **Microsoft.Xna.Framework.Media.MediaQueue** | [**SystemMediaTransportControls** ](https://msdn.microsoft.com/library/windows/apps/dn278677) класса |
| Класс **Microsoft.Xna.Framework.Media.Playlist** | [**BackgroundMediaPlayer** ](https://msdn.microsoft.com/library/windows/apps/dn652527) класса |
| Пространство имен **System.Windows.Media** | [**Windows.UI.Xaml.Media** ](/uwp/api/Windows.UI.Xaml.Media) пространства имен |
| Класс **System.Windows.Media.RadialGradientBrush** | Нет прямого эквивалента. См. раздел [Мультимедиа и графика](wpsl-to-uwp-porting-xaml-and-ui.md). |
| Пространство имен **System.Windows.Media.Animation** | [**Windows.UI.Xaml.Media.Animation** ](https://msdn.microsoft.com/library/windows/apps/br243232) пространства имен |
| Пространство имен **System.Windows.Media.Effects** | Нет прямого эквивалента |
| Пространство имен **System.Windows.Media.Imaging** | [**Windows.UI.Xaml.Media.Imaging** ](https://msdn.microsoft.com/library/windows/apps/br243258) пространства имен |
| Пространство имен **System.Windows.Media.Media3D** | [**Windows.UI.Xaml.Media.Media3D** ](https://msdn.microsoft.com/library/windows/apps/br243274) пространства имен |
| Пространство имен **System.Windows.Shapes** | [**Windows.UI.Xaml.Shapes** ](/uwp/api/Windows.UI.Xaml.Shapes) пространства имен |
| Средства запуска и выбора | |
| Классы **Microsoft.Phone.Tasks.AddressChooserTask**, **EmailAddressChooserTask**, **PhoneNumberChooserTask** | [**ContactPicker** ](https://msdn.microsoft.com/library/windows/apps/br224913) класса |
| Классы **Microsoft.Phone.Tasks.AddWalletItemTask**, **AddWalletItemResult** | [**Windows.ApplicationModel.Wallet** ](https://msdn.microsoft.com/library/windows/apps/dn631399) пространства имен |
| Классы **Microsoft.Phone.Tasks.BingMapsDirectionsTask**, **BingMapsTask** | Нет прямого эквивалента |
| Класс **Microsoft.Phone.Tasks.CameraCaptureTask** | [**MediaCapture** ](https://msdn.microsoft.com/library/windows/apps/br241124) класса. Также класс [**CameraCaptureUI**](https://msdn.microsoft.com/library/windows/apps/br241030) (только Windows). |
| **Microsoft.Phone.Tasks.MarketplaceDetailTask** | [**CurrentApp** ](https://msdn.microsoft.com/library/windows/apps/hh779765) класс ([**RequestAppPurchaseAsync** ](https://msdn.microsoft.com/library/windows/apps/hh967813) метод) |
| Классы **Microsoft.Phone.Tasks.ConnectionSettingsTask**, **MarketplaceHubTask**, **MarketplaceReviewTask**, **MarketplaceSearchTask**, **MediaPlayerLauncher**, **SearchTask**, **SmsComposeTask**, **WebBrowserTask** | [**Средство запуска** ](https://msdn.microsoft.com/library/windows/apps/br241801) класса |
| Класс **Microsoft.Phone.Tasks.EmailComposeTask** | [**EmailMessage** ](https://msdn.microsoft.com/library/windows/apps/dn631270) класса |
| Класс **Microsoft.Phone.Tasks.GameInviteTask** | Нет прямого эквивалента |
| Классы **Microsoft.Phone.Tasks.MapDownloaderTask**, **MapsDirectionsTask**, **MapsTask**, **MapUpdaterTask** | Нет прямого эквивалента |
| Класс **Microsoft.Phone.Tasks.PhoneCallTask** | [**PhoneCallManager** ](https://msdn.microsoft.com/library/windows/apps/dn624832) класса |
| Класс **Microsoft.Phone.Tasks.PhotoChooserTask** | [**FileOpenPicker** ](https://msdn.microsoft.com/library/windows/apps/br207847) класса |
| Класс **Microsoft.Phone.Tasks.SaveAppointmentTask** | [**AppointmentManager** ](https://msdn.microsoft.com/library/windows/apps/dn297254) класса |
| Классы **Microsoft.Phone.Tasks.SaveContactTask**, **SaveEmailAddressTask**, **SavePhoneNumberTask** | [**StoredContact** ](https://msdn.microsoft.com/library/windows/apps/jj207727) класс (только для Windows Phone) | 
| Класс **Microsoft.Phone.Tasks.SaveRingtoneTask** | Нет прямого эквивалента |
| Классы **Microsoft.Phone.Tasks.ShareLinkTask**, **ShareMediaTask**, **ShareStatusTask** | [**DataPackage** ](https://msdn.microsoft.com/library/windows/apps/br205873) класса |
| Location | |
| Пространство имен **System.Device.Location** | [**Windows.Devices.Geolocation** ](https://msdn.microsoft.com/library/windows/apps/br225603) пространства имен |
| Класс **System.Device.GeoCoordinateWatcher** | [**Geolocator** ](https://msdn.microsoft.com/library/windows/apps/br225534) класса |
| Карты | |
| Пространства имен **Microsoft.Phone.Maps** | [**Windows.Services.Maps** ](https://msdn.microsoft.com/library/windows/apps/dn636979) пространства имен |
| Пространство имен **Microsoft.Phone.Maps.Controls** | [**Windows.UI.Xaml.Controls.Maps** ](https://msdn.microsoft.com/library/windows/apps/dn610751) пространства имен |
| Класс **Microsoft.Phone.Maps.Controls.Map** | [**MapControl** ](https://msdn.microsoft.com/library/windows/apps/dn637004) класса |
| Пространство имен **Microsoft.Phone.Maps.Services** | [**Windows.Services.Maps** ](https://msdn.microsoft.com/library/windows/apps/dn636979) пространства имен |
| Классы **Microsoft.Phone.Maps.Services.GeocodeQuery**, **ReverseGeocodeQuery** | [**MapLocationFinder** ](https://msdn.microsoft.com/library/windows/apps/dn627550) класса |
| Класс **System.Device.Location.GeoCoordinate** | [**Geopoint** ](https://msdn.microsoft.com/library/windows/apps/dn263675) класса |
| Класс **Microsoft.Phone.Maps.Services.Route** | [**MapRoute** ](https://msdn.microsoft.com/library/windows/apps/dn636937) класса |
| Класс **Microsoft.Phone.Maps.Services.RouteQuery** | [**MapRouteFinder** ](https://msdn.microsoft.com/library/windows/apps/dn636938) класса |
| Получение дохода | |
| Пространство имен **Microsoft.Phone.Marketplace** | [**Windows.ApplicationModel.Store** ](https://msdn.microsoft.com/library/windows/apps/br225197) пространства имен |
| Media | |
| Пространство имен **Microsoft.Phone.Media** | [**MediaElement** ](https://msdn.microsoft.com/library/windows/apps/br242926) класса |
| Сеть | |
| (MPNN = **Microsoft.Phone.Net.NetworkInformation**) <br/> Класс **MPNN.DeviceNetworkInformation** | [**Имя узла**](https://msdn.microsoft.com/library/windows/apps/br207113), [ **NetworkInformation, соответствующей выбранному** ](https://msdn.microsoft.com/library/windows/apps/br207293) классы |
| (MPNN = **Microsoft.Phone.Net.NetworkInformation**) <br/> Класс **MPNN.NetworkInterface** | [**NetworkInformation, соответствующей выбранному** ](https://msdn.microsoft.com/library/windows/apps/br207293) класса |
| (MPNN = **Microsoft.Phone.Net.NetworkInformation**) <br/> Класс **MPNN.NetworkInterfaceInfo** | [**ConnectionProfile** ](https://msdn.microsoft.com/library/windows/apps/br207249) класса |
| (MPNN = **Microsoft.Phone.Net.NetworkInformation**) <br/> Класс **MPNN.NetworkInterfaceList** | [**NetworkInformation, соответствующей выбранному** ](https://msdn.microsoft.com/library/windows/apps/br207293) класса |
| (MPNN = **Microsoft.Phone.Net.NetworkInformation**) <br/> Класс **MPNN.SocketExtensions** | Нет прямого эквивалента |
| (MPNN = **Microsoft.Phone.Net.NetworkInformation**) <br/> Класс **MPNN.WebRequestExtensions** | Нет прямого эквивалента |
| Пространство имен **Microsoft.Phone.Networking.Voip** | Нет прямого эквивалента |
| Класс **System.Net.CookieCollection** | По-прежнему поддерживается, только некоторые свойства отсутствуют (например, IsReadOnly) |
| Класс **System.Net.DownloadProgressChangedEventArgs** и похожие классы, связанные с **System.Net.WebClient** | [**HttpClient** ](https://msdn.microsoft.com/library/windows/apps/dn298639) класс (или [System.Net.Http.HttpClient](https://msdn.microsoft.com/library/system.net.http.httpclient(v=vs.118).aspx)). Производные от [System.Net.Http.StreamContent](https://msdn.microsoft.com/library/system.net.http.streamcontent.aspx) для измерения хода выполнения. |
| Классы **System.Net.DnsEndPoint**, **IPAddress** | Эти классы все еще поддерживаются, но некоторые свойства отсутствуют. Можно также перенести в класс [**HostName**](https://msdn.microsoft.com/library/windows/apps/br207113). |
| Класс **System.Net.HttpUtility** | [**HtmlFormatHelper** ](https://msdn.microsoft.com/library/windows/apps/hh738437) класса |
| Класс **System.Net.HttpWebRequest** | Частичная поддержка, при этом рекомендованной перспективной альтернативой является класс [**HttpClient**](https://msdn.microsoft.com/library/windows/apps/dn298639) (или [System.Net.Http.HttpClient](https://msdn.microsoft.com/library/system.net.http.httpclient(v=vs.118).aspx)). Эти API-интерфейсы используют [System.Net.Http.HttpRequestMessage](https://msdn.microsoft.com/library/system.net.http.httprequestmessage.aspx) для представления запроса HTTP. |
| Класс **System.Net.HttpWebResponse** | По-прежнему поддерживается, но вместо Close() используйте Dispose(). Рекомендуемой перспективной альтернативой является класс [**HttpClient**](https://msdn.microsoft.com/library/windows/apps/dn298639) (или [System.Net.Http.HttpClient](https://msdn.microsoft.com/library/system.net.http.httpclient(v=vs.118).aspx)). Эти API используют [System.Net.Http.HttpResponseMessage](https://msdn.microsoft.com/library/system.net.http.httpresponsemessage.aspx) для представления ответа HTTP. |
| (SNN = **System.Net.NetworkInformation**) <br/> Класс **SNN.NetworkChange** | По-прежнему поддерживается за исключением конструктора. |
| Класс **System.Net.OpenReadCompletedEventArgs** и похожие классы, связанные с **System.Net.WebClient** | [**HttpClient** ](https://msdn.microsoft.com/library/windows/apps/dn298639) класс (или [System.Net.Http.HttpClient](https://msdn.microsoft.com/library/system.net.http.httpclient.aspx)) |
| Класс **System.Net.Sockets.Socket** | По-прежнему поддерживается, но вместо Close() используйте Dispose(). Можно также перенести в класс [**StreamSocket**](https://msdn.microsoft.com/library/windows/apps/br226882). |
| Класс **System.Net.Sockets.SocketException** | По-прежнему поддерживается, но используйте свойство SocketErrorCode вместо ErrorCode. |
| Классы **System.Net.Sockets.UdpAnySourceMulticastClient**, **UdpSingleSourceMulticastClient** | [**DatagramSocket** ](https://msdn.microsoft.com/library/windows/apps/br241319) класса |
| Класс **System.Net.UploadProgressChangedEventArgs** и похожие классы, связанные с **System.Net.WebClient** | [**HttpClient** ](https://msdn.microsoft.com/library/windows/apps/dn298639) класс (или [System.Net.Http.HttpClient](https://msdn.microsoft.com/library/system.net.http.httpclient.aspx)) |
| Класс **System.Net.WebClient** | [**HttpClient** ](https://msdn.microsoft.com/library/windows/apps/dn298639) класс (или [System.Net.Http.HttpClient](https://msdn.microsoft.com/library/system.net.http.httpclient.aspx)) |
| Класс **System.Net.WebRequest** | Частичная поддержка (другой набор свойств), при этом рекомендованная перспективная альтернатива — класс [**HttpClient**](https://msdn.microsoft.com/library/windows/apps/dn298639) (или [System.Net.Http.HttpClient](https://msdn.microsoft.com/library/system.net.http.httpclient(v=vs.118).aspx)). Эти API-интерфейсы используют [System.Net.Http.HttpRequestMessage](https://msdn.microsoft.com/library/system.net.http.httprequestmessage.aspx) для представления запроса HTTP. |
| Класс **System.Net.WebResponse** | По-прежнему поддерживается, но вместо Close() используйте Dispose(). Рекомендуемой перспективной альтернативой является класс [**HttpClient**](https://msdn.microsoft.com/library/windows/apps/dn298639) (или [System.Net.Http.HttpClient](https://msdn.microsoft.com/library/system.net.http.httpclient(v=vs.118).aspx)). Эти API используют [System.Net.Http.HttpResponseMessage](https://msdn.microsoft.com/library/system.net.http.httpresponsemessage.aspx) для представления ответа HTTP. |
| (SN = **System.Net**) <br/> Класс **SN.WriteStreamClosedEventArgs** | [**HttpClient** ](https://msdn.microsoft.com/library/windows/apps/dn298639) класс (или [System.Net.Http.HttpClient](https://msdn.microsoft.com/library/system.net.http.httpclient.aspx)) |
| (SN = **System.Net**) <br/> Класс **SN.WriteStreamClosedEventHandler** | [**HttpClient** ](https://msdn.microsoft.com/library/windows/apps/dn298639) класс (или [System.Net.Http.HttpClient](https://msdn.microsoft.com/library/system.net.http.httpclient.aspx)) |
| Класс **System.UriFormatException** | Класс **System.FormatException** |
| Уведомления | |
| Пространство имен MPN = **Microsoft.Phone.Notification** | [**Windows.UI.Notifications**](https://msdn.microsoft.com/library/windows/apps/br208661), [ **Windows.Networking.PushNotifications** ](https://msdn.microsoft.com/library/windows/apps/br241307) пространств имен |
| MPN = **Microsoft.Phone.Notification** <br/> Класс **MPN.HttpNotification** | [**TileNotification** ](https://msdn.microsoft.com/library/windows/apps/br208616) класса |
| MPN = **Microsoft.Phone.Notification** <br/> Класс **MPN.HttpNotificationChannel** | [**PushNotificationChannel** ](https://msdn.microsoft.com/library/windows/apps/br241283) класса |
| Программирование | |
| Пространство имен **System** | [**Windows.Foundation** ](https://msdn.microsoft.com/library/windows/apps/br226021) пространства имен |
| Классы **System.Diagnostics.StackFrame**, **StackTrace** | Нет прямого эквивалента |
| Пространство имен **System.Diagnostics** | [**Windows.Foundation.Diagnostics** ](https://msdn.microsoft.com/library/windows/apps/br206677) пространства имен |
| Интерфейс **System.ICloneable** | Пользовательский метод, который возвращает соответствующий тип. |
| Класс **System.Reflection.Emit.ILGenerator** | Нет прямого эквивалента |
| Реактивные расширения | |
| Пространство имен **Microsoft.Phone.Reactive** | Нет прямого эквивалента |
| Отражение | |
| Класс **System.Type** | Класс **System.Reflection.TypeInfo** См. раздел [Отражение в .NET Framework для приложений UWP](https://msdn.microsoft.com/library/hh535795.aspx). |
| Ресурсы | |
| Класс **System.Resources.ResourceManager** | (WA = **Windows.ApplicationModel**)<br/>[**ШТАТ ВАШИНГТОН. Resources.Core** ](https://msdn.microsoft.com/library/windows/apps/br225039) и [ **штат Вашингтон. Ресурсы** ](https://msdn.microsoft.com/library/windows/apps/br206022) пространства имен, [ **ResourceManager** ](https://msdn.microsoft.com/library/windows/apps/br206078) класса. См. раздел [Создание и получение ресурсов в приложениях среды выполнения Windows](https://msdn.microsoft.com/library/windows/apps/xaml/hh694557.aspx). |
| Защищенный элемент | |
| (MPS = **Microsoft.Phone.SecureElement**) <br/> Классы **MPS.SecureElementChannel**, **MPS.SecureElementSession** | [**SmartCardConnection** ](https://msdn.microsoft.com/library/windows/apps/dn608002) класса |
| (MPS = **Microsoft.Phone.SecureElement**) <br/> Класс **MPS.SecureElementReader** | [**SmartCardReader** ](https://msdn.microsoft.com/library/windows/apps/dn263857) класса |
| Безопасность | |
| (SSC = **System.Security.Cryptography**) <br/> Классы **SSC.Aes**, **SSC.RSA** | [**CryptographicEngine** ](https://msdn.microsoft.com/library/windows/apps/br241490) класса |
| (SSC = **System.Security.Cryptography**) <br/> Классы **SSC.HMACSHA256**, **SSC.SHA256** | [**HashAlgorithmProvider** ](https://msdn.microsoft.com/library/windows/apps/br241511) класса |
| (SSC = **System.Security.Cryptography**) <br/> Класс **SSC.ProtectedData** | [**DataProtectionProvider** ](https://msdn.microsoft.com/library/windows/apps/br241559) класса |
| (SSC = **System.Security.Cryptography**) <br/> Класс **SSC.RandomNumberGenerator** | [**CryptographicBuffer** ](https://msdn.microsoft.com/library/windows/apps/br227092) класса |
| (SSC = **System.Security.Cryptography**) <br/> Класс **SSC.X509Certificates.X509Certificate** | [**CertificateEnrollmentManager** ](https://msdn.microsoft.com/library/windows/apps/br212075) класса |
| Оболочка | |
| (MPSh = **Microsoft.Phone.Shell**) <br/> Класс **MPSh.ApplicationBar** | [**CommandBar** ](https://msdn.microsoft.com/library/windows/apps/dn279427) класса |
| (MPSh = **Microsoft.Phone.Shell**) <br/> Класс **MPSh.ApplicationBarIconButton** | [**AppBarButton** ](https://msdn.microsoft.com/library/windows/apps/dn279244) класса (при использовании внутри [ **PrimaryCommands** ](https://msdn.microsoft.com/library/windows/apps/dn279430) свойство) |
| (MPSh = **Microsoft.Phone.Shell**) <br/> Класс **MPSh.ApplicationBarMenuItem** | [**AppBarButton** ](https://msdn.microsoft.com/library/windows/apps/dn279244) класса (при использовании внутри [ **SecondaryCommands** ](https://msdn.microsoft.com/library/windows/apps/dn279434) свойство) |
| (MPSh = **Microsoft.Phone.Shell**) <br/> Классы **MPSh.CycleTileData**, **MPSh.FlipTileData**, **MPSh.IconicTileData**, **MPSh.ShellTileData**, **MPSh.StandardTileData** | [**TileTemplateType** ](https://msdn.microsoft.com/library/windows/apps/br208621) класса |
| (MPSh = **Microsoft.Phone.Shell**) <br/> Класс **MPSh.PhoneApplicationService** | [**CoreApplication**](https://msdn.microsoft.com/library/windows/apps/br225016), [ **DisplayRequest** ](https://msdn.microsoft.com/library/windows/apps/br241816) классы |
| (MPSh = **Microsoft.Phone.Shell**) <br/> Класс **MPSh.ProgressIndicator** | [**StatusBarProgressIndicator** ](https://msdn.microsoft.com/library/windows/apps/dn633865) класса |
| (MPSh = **Microsoft.Phone.Shell**) <br/> Класс **MPSh.ShellTile** | [**SecondaryTile** ](https://msdn.microsoft.com/library/windows/apps/br242183) класса |
| (MPSh = **Microsoft.Phone.Shell**) <br/> Класс **MPSh.ShellTileSchedule** | [**TileUpdater** ](https://msdn.microsoft.com/library/windows/apps/br208628) класса |
| (MPSh = **Microsoft.Phone.Shell**) <br/> Класс **MPSh.ShellToast** | [**ToastNotificationManager** ](https://msdn.microsoft.com/library/windows/apps/br208642) класса |
| (MPSh = **Microsoft.Phone.Shell**) <br/> Класс **MPSh.SystemTray** | [**StatusBar** ](https://msdn.microsoft.com/library/windows/apps/dn633864) класса |
| Хранилище и ввод-вывод | |
| Классы **Microsoft.Phone.Storage.ExternalStorage**, **ExternalStorageDevice**, **ExternalStorageFile**, **ExternalStorageFolder** | [**KnownFolders** ](https://msdn.microsoft.com/library/windows/apps/br227151) класса |
| Пространство имен **System.IO** | [**Windows.Storage**](https://msdn.microsoft.com/library/windows/apps/br227346), [ **Windows.Storage.Streams** ](https://msdn.microsoft.com/library/windows/apps/br241791) пространств имен |
| Класс **System.IO.Directory** | [**StorageFolder** ](https://msdn.microsoft.com/library/windows/apps/br227230) класса |
| Класс **System.IO.File** | [**StorageFile** ](https://msdn.microsoft.com/library/windows/apps/br227171) и [ **PathIO** ](https://msdn.microsoft.com/library/windows/apps/hh701663) классы
| (SII = **System.IO.IsolatedStorage**) <br/> Класс **SII.IsolatedStorageFile** |[**ApplicationData.LocalFolder** ](https://msdn.microsoft.com/library/windows/apps/br241621) свойство |
| (SII = **System.IO.IsolatedStorage**) <br/> Класс **SII.IsolatedStorageSettings** | [**ApplicationData.LocalSettings** ](https://msdn.microsoft.com/library/windows/apps/windows.storage.applicationdata.localsettings.aspx) свойство |
| Класс **System.IO.Stream** | По-прежнему поддерживается, используйте ReadAsync() и WriteAsync() вместо BeginRead()/EndRead() и BeginWrite()/EndWrite(). |
| Кошелек | |
| Пространство имен **Microsoft.Phone.Wallet** | [**Windows.ApplicationModel.Wallet** ](https://msdn.microsoft.com/library/windows/apps/dn631399) пространства имен |
| Xml | |
| (SX = **System.Xml**) | Метод **SX.XmlConvert.ToDateTime** |
| (SX = **System.Xml**) | Метод **SX.XmlConvert.ToDateTimeOffset** |


Следующий раздел называется [Перенос проекта](wpsl-to-uwp-porting-to-a-uwp-project.md).


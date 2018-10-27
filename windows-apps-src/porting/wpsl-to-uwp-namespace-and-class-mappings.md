---
author: stevewhims
description: Этот раздел содержит подробно описывается сопоставление API-интерфейсы WindowsPhone Silverlight с их эквивалентами универсальной платформы Windows (UWP).
title: Сопоставление пространства имен и класса UWP Silverlight и WindowsPhone
ms.assetid: 33f06706-4790-48f3-a2e4-ebef9ddb61a4
ms.author: stwhi
ms.date: 02/08/2017
ms.topic: article
keywords: Windows10, UWP
ms.localizationpriority: medium
ms.openlocfilehash: 54118b41fc1f3036dddba9a0cfb8ecd860c1e233
ms.sourcegitcommit: 086001cffaf436e6e4324761d59bcc5e598c15ea
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/27/2018
ms.locfileid: "5690697"
---
# <a name="windowsphone-silverlight-to-uwp-api-mappings"></a>API-Интерфейс UWP сопоставление Silverlight и WindowsPhone


Этот раздел содержит подробно описывается сопоставление API-интерфейсы WindowsPhone Silverlight с их эквивалентами универсальной платформы Windows (UWP). Обычно не существует полных функциональных аналогов, так как одна платформа может иметь большую или меньшую функциональность по сравнению с ее аналогом в пространстве имен или классе.

Таблица сопоставления поможет при работе в проекте UWP и повторном использовании исходного кода из проекта WindowsPhone Silverlight. Между двумя платформами есть различия в именах пространств имен и классов (в том числе в элементах управления пользовательского интерфейса). В большинстве случаев нужно лишь изменить имя пространства имен, и код будет успешно работать. Иногда изменяется не только имя пространства имен, но и имя класса или API. Иногда сопоставление требует многих усилий, а в некоторых случаях требуется изменить подход.

**Использование таблицы:** Во-первых найдите имя класса, которые вы используете. Классы указываются, когда для сопоставления требуются более сложные действия, чем просто изменение имени пространства имен. Если класс не указан, тогда сопоставление заключается лишь в изменении пространства имен. Найдите имя пространства имен класса, и таким образом вы получите соответствующее имя пространства имен в UWP. Ваш класс будет указан в этом пространстве имен. Если вашего пространства имен нет в списке, это значит, что его имя не изменялось.

**Примечание**Windows10 поддерживает значительно большую часть .NET Framework, чем приложение магазина Windows Phone. Например Windows10 имеет несколько System.ServiceModel.\* пространств имен, а также System.Net, System.Net.NetworkInformation и пространства имен System.Net.Sockets.
Кроме того, в приложении для Windows10, вы сможете воспользоваться возможностями .NET Native, что это технология вперед-компиляции, которая преобразует MSIL изначально встроенный готовый к запуску машинный код. Приложения .NET Native запускаются быстрее, используют меньше памяти и меньше заряда батареи, чем аналоги MSIL.

| WindowsPhone Silverlight | Среда выполнения Windows |
| ------------------------- | --------------- |
| Реклама | |
| Класс **Microsoft.Advertising.Mobile.UI.AdControl** | Класс [AdControl](http://msdn.microsoft.com/library/advertising-windows-sdk-api-reference-adcontrol.aspx) |
| Сигналы, напоминания и фоновые агенты | |
| Класс **Microsoft.Phone.BackgroundAgent** | Класс [**BackgroundTaskBuilder**](https://msdn.microsoft.com/library/windows/apps/br224768) |
| Пространство имен **Microsoft.Phone.Scheduler** | Пространство имен [**Windows.ApplicationModel.Background**](https://msdn.microsoft.com/library/windows/apps/br224847) |
| Класс **Microsoft.Phone.Scheduler.Alarm** | Классы [**BackgroundTaskBuilder**](https://msdn.microsoft.com/library/windows/apps/br224768) и [**ToastNotificationManager**](https://msdn.microsoft.com/library/windows/apps/br208642) |
| Классы **Microsoft.Phone.Scheduler.PeriodicTask**, **ScheduledAction**, **ScheduledActionService**, **ScheduledTask**, **ScheduledTaskAgent** | Класс [**BackgroundTaskBuilder**](https://msdn.microsoft.com/library/windows/apps/br224768) |
| Класс **Microsoft.Phone.Scheduler.Reminder** | Классы [**BackgroundTaskBuilder**](https://msdn.microsoft.com/library/windows/apps/br224768) и [**ToastNotificationManager**](https://msdn.microsoft.com/library/windows/apps/br208642) |
| Класс **Microsoft.Phone.PictureDecoder** | Класс [**BitmapDecoder**](https://msdn.microsoft.com/library/windows/apps/br226176) |
| Пространство имен **Microsoft.Phone.BackgroundAudio** | Пространство имен [**Windows.Media.Playback**](https://msdn.microsoft.com/library/windows/apps/dn640562) |
| Пространство имен **Microsoft.Phone.BackgroundTransfer** | Пространство имен [**Windows.Networking.BackgroundTransfer**](https://msdn.microsoft.com/library/windows/apps/br207242) |
| Модель и среда приложения | |
| Класс **System.AppDomain** | Нет прямого эквивалента. См. классы [**Application**](https://msdn.microsoft.com/library/windows/apps/br242324), [**CoreApplication**](https://msdn.microsoft.com/library/windows/apps/br225016) |
| Класс **System.Environment** | Нет прямого эквивалента |
| Класс **System.ComponentModel.Annotations**  | Нет прямого эквивалента |
| Класс **System.ComponentModel.BackgroundWorker** | Класс [**ThreadPool**](https://msdn.microsoft.com/library/windows/apps/br229621) |
| Класс **System.ComponentModel.DesignerProperties** | Класс [**DesignMode**](https://msdn.microsoft.com/library/windows/apps/br224664) |
| Классы **System.Threading.Thread**, **System.Threading.ThreadPool** | Класс [**ThreadPool**](https://msdn.microsoft.com/library/windows/apps/br229621) |
| (ST = **System.Threading**) <br/> Метод **ST.Thread.MemoryBarrier** | (ST = **System.Threading**) <br/> Метод **ST.Interlocked.MemoryBarrier** |
| (ST = **System.Threading**) <br/> Свойство **ST.Thread.ManagedThreadId** | (S = **System**) <br/> Свойство **S.Environment.ManagedThreadId** |
| Класс **System.Threading.Timer** | Класс [**ThreadPoolTimer**](https://msdn.microsoft.com/library/windows/apps/br230587) |
| (SWT = **System.Windows.Threading**) <br/> Класс **SWT.Dispatcher** | Класс [**CoreDispatcher**](https://msdn.microsoft.com/library/windows/apps/br208211) |
| (SWT = **System.Windows.Threading**) <br/> Класс **SWT.DispatcherTimer** | Класс [**DispatcherTimer**](https://msdn.microsoft.com/library/windows/apps/br244250) |
| Blend для Visual Studio | |
| (MEDC = **Microsoft.Expression.Drawing.Core**) <br/> Класс **MEDC.GeometryHelper** | Нет прямого эквивалента |
| Пространство имен **Microsoft.Expression.Interactivity** | Пространство имен [Microsoft.Xaml.Interactivity](http://go.microsoft.com/fwlink/p/?LinkId=328776) |
| Пространство имен **Microsoft.Expression.Interactivity.Core** | Пространство имен [Microsoft.Xaml.Interactions.Core](http://go.microsoft.com/fwlink/p/?LinkId=328773) |
| (MEIC = **Microsoft.Expression.Interactivity.Core**) <br/> Класс **MEIC.ExtendedVisualStateManager** | Нет прямого эквивалента |
| Пространство имен **Microsoft.Expression.Interactivity.Input** | Нет прямого эквивалента |
| Пространство имен **Microsoft.Expression.Interactivity.Media** | Пространство имен [Microsoft.Xaml.Interactions.Media](http://go.microsoft.com/fwlink/p/?LinkId=328775) |
| Пространство имен **Microsoft.Expression.Shapes** | Нет прямого эквивалента |
| (MI = **Microsoft.Internal**) <br/> Интерфейс **MI.IManagedFrameworkInternalHelper** | Нет прямого эквивалента |
| Данные контактов и календаря | |
| Пространство имен **Microsoft.Phone.UserData** | Пространство имен [**Windows.ApplicationModel.Contacts**](https://msdn.microsoft.com/library/windows/apps/br225002), [**Windows.ApplicationModel.Appointments**](https://msdn.microsoft.com/library/windows/apps/dn263359) |
| (MPU = **Microsoft.Phone.UserData**) <br/> Классы **MPU.Account**, **ContactAddress**, **ContactCompanyInformation**, **ContactEmailAddress**, **ContactPhoneNumber** | Класс [**Contact**](https://msdn.microsoft.com/library/windows/apps/br224849) |
| (MPU = **Microsoft.Phone.UserData**) <br/> Класс **MPU.Appointments** | Класс [**AppointmentCalendar**](https://msdn.microsoft.com/library/windows/apps/dn596134) |
| (MPU = **Microsoft.Phone.UserData**) <br/> Класс **MPU.Contacts** | Класс [**ContactStore**](https://msdn.microsoft.com/library/windows/apps/dn624859) |
| Элементы управления и инфраструктура пользовательского интерфейса | |
| Класс **ControlTiltEffect.TiltEffect** | Анимации из библиотеки анимации среды выполнения Windows встроены в стили стандартных элементов управления по умолчанию. См. раздел [Анимация](wpsl-to-uwp-porting-xaml-and-ui.md). |
| Пространство имен **Microsoft.Phone.Controls** | Пространство имен [**Windows.UI.Xaml.Controls**](https://msdn.microsoft.com/library/windows/apps/br227716) |
| (MPC = **Microsoft.Phone.Controls**) <br/> Класс **MPC.ContextMenu** | Класс [**PopupMenu**](https://msdn.microsoft.com/library/windows/apps/br208693) |
| (MPC = **Microsoft.Phone.Controls**) <br/>Класс **MPC.DatePickerPage** | Класс [**DatePickerFlyout**](https://msdn.microsoft.com/library/windows/apps/dn625013) |
| (MPC = **Microsoft.Phone.Controls**) <br/>Класс **MPC.GestureListener** | Класс [**GestureRecognizer**](https://msdn.microsoft.com/library/windows/apps/br241937) |
| (MPC = **Microsoft.Phone.Controls**) <br/>Класс **MPC.LongListSelector** | Класс [**SemanticZoom**](https://msdn.microsoft.com/library/windows/apps/hh702601) |
| (MPC = **Microsoft.Phone.Controls**) <br/>Класс **MPC.ObscuredEventArgs** | Классы [**SystemProtection**](https://msdn.microsoft.com/library/windows/apps/jj585394), [**WindowActivatedEventArgs**](https://msdn.microsoft.com/library/windows/apps/br208377) |
| (MPC = **Microsoft.Phone.Controls**) <br/>Класс**MPC.Panorama** | Класс [**Hub**](https://msdn.microsoft.com/library/windows/apps/dn251843) |
| (MPC = **Microsoft.Phone.Controls**) <br/>**MPC.PhoneApplicationFrame**,<br/>(SWN = **System.Windows.Navigation**) <br/>Классы **SWN.NavigationService** | Класс [**Frame**](https://msdn.microsoft.com/library/windows/apps/br242682) |
| (MPC = **Microsoft.Phone.Controls**) <br/>Класс **MPC.PhoneApplicationPage** | Класс [**Page**](https://msdn.microsoft.com/library/windows/apps/br227503) |
| (MPC = **Microsoft.Phone.Controls**) <br/>Класс **MPC.TiltEffect** | Класс [**PointerDownThemeAnimation**](https://msdn.microsoft.com/library/windows/apps/hh969164) |
| (MPC = **Microsoft.Phone.Controls**) <br/>Класс **MPC.TimePickerPage** | Класс [**TimePickerFlyout**](https://msdn.microsoft.com/library/windows/apps/dn608313) |
| (MPC = **Microsoft.Phone.Controls**) <br/>Класс **MPC.WebBrowser** | Класс [**WebView**](https://msdn.microsoft.com/library/windows/apps/br227702) |
| (MPC = **Microsoft.Phone.Controls**) <br/>Класс **MPC.WebBrowserExtensions** | Нет прямого эквивалента |
| (MPC = **Microsoft.Phone.Controls**) <br/>Класс **MPC.WrapPanel** | Нет прямого эквивалента для макетирования. [**ItemsWrapGrid**](https://msdn.microsoft.com/library/windows/apps/dn298849) и [**WrapGrid**](https://msdn.microsoft.com/library/windows/apps/br227717) могут быть использованы в шаблоне панели элементов и элементах управления. |
| (MPD = **Microsoft.Phone.Data**) <br/>Пространство имен **MPD.Linq** | Нет прямого эквивалента |
| (MPD = **Microsoft.Phone.Data**) <br/>Пространство имен **MPD.Linq.Mapping** | Нет прямого эквивалента |
| Пространство имен **Microsoft.Phone.Globalization** | Нет прямого эквивалента |
| (MPI = **Microsoft.Phone.Info**) <br/>Классы **MPI.DeviceExtendedProperties**, **DeviceStatus** | Классы [**EasClientDeviceInformation**](https://msdn.microsoft.com/library/windows/apps/hh701390), [**MemoryManager**](https://msdn.microsoft.com/library/windows/apps/dn633831). Дополнительные сведения см. в разделе [Состояние устройства](wpsl-to-uwp-input-and-sensors.md). |
| (MPI = **Microsoft.Phone.Info**) <br/>Класс **MPI.MediaCapabilities** | Нет прямого эквивалента |
| (MPI = **Microsoft.Phone.Info**) <br/>Класс **MPI.UserExtendedProperties** | Класс [**AdvertisingManager**](https://msdn.microsoft.com/library/windows/apps/dn363391) |
| Пространство имен **System.Windows** | Пространство имен [**Windows.UI.Xaml**](https://msdn.microsoft.com/library/windows/apps/br209045) |
| Пространство имен **System.Windows.Automation** | Пространство имен [**Windows.UI.Xaml.Automation**](https://msdn.microsoft.com/library/windows/apps/br209179) |
| Пространства имен **System.Windows.Controls**, **System.Windows.Input** | Пространства имен [**Windows.UI.Core**](https://msdn.microsoft.com/library/windows/apps/br208383), [**Windows.UI.Input**](https://msdn.microsoft.com/library/windows/apps/br242084), [**Windows.UI.Xaml.Controls**](https://msdn.microsoft.com/library/windows/apps/br227716) |
| Классы **System.Windows.Controls.DrawingSurface**, **DrawingSurfaceBackgroundGrid** | Класс [**SwapChainPanel**](https://msdn.microsoft.com/library/windows/apps/dn252834) |
| Класс **System.Windows.Controls.RichTextBox** | Класс [**RichEditBox**](https://msdn.microsoft.com/library/windows/apps/br227548) |
| Класс **System.Windows.Controls.WrapPanel** | Нет прямого эквивалента для макетирования. [**ItemsWrapGrid**](https://msdn.microsoft.com/library/windows/apps/dn298849) и [**WrapGrid**](https://msdn.microsoft.com/library/windows/apps/br227717) могут быть использованы в шаблоне панели элементов и элементах управления. |
| Пространство имен **System.Windows.Controls.Primitives** | Пространство имен [**Windows.UI.Xaml.Controls.Primitives**](https://msdn.microsoft.com/library/windows/apps/br209818) |
| Пространство имен **System.Windows.Controls.Shapes** | Пространство имен [**Windows.UI.Xaml.Controls.Shapes**](/uwp/api/Windows.UI.Xaml.Shapes) |
| Пространство имен **System.Windows.Data** | Пространство имен [**Windows.UI.Xaml.Data**](https://msdn.microsoft.com/library/windows/apps/br209917) |
| Пространство имен **System.Windows.Documents** | Пространство имен [**Windows.UI.Xaml.Documents**](https://msdn.microsoft.com/library/windows/apps/br209984) |
| Пространство имен **System.Windows.Ink** | Нет прямого эквивалента |
| Пространство имен **System.Windows.Markup** | Пространство имен [**Windows.UI.Xaml.Markup**](https://msdn.microsoft.com/library/windows/apps/br228046) | 
| Пространство имен **System.Windows.Navigation** | Пространство имен [**Windows.UI.Xaml.Navigation**](https://msdn.microsoft.com/library/windows/apps/br243300) |
| Событие **System.Windows.UIElement.Tap**, делегат **EventHandler&lt;GestureEventArgs&gt;** | Событие [**Tapped**](https://msdn.microsoft.com/library/windows/apps/br208985), делегат [**TappedEventHandler**](https://msdn.microsoft.com/library/windows/apps/br227993) |
| Данные и службы |  |
| Класс **System.Data.Linq.DataContext** | Нет прямого эквивалента |
| Класс **System.Data.Linq.Mapping.ColumnAttribute** | Нет прямого эквивалента |
| Класс **System.Data.Linq.SqlClient.SqlHelpers** | Нет прямого эквивалента |
| Устройства | |
| Пространства имен **Microsoft.Devices**, **Microsoft.Devices.Sensors** | Пространства имен [**Windows.Devices.Enumeration**](https://msdn.microsoft.com/library/windows/apps/br225459), [**Windows.Devices.Enumeration.Pnp**](https://msdn.microsoft.com/library/windows/apps/br225517), [**Windows.Devices.Input**](https://msdn.microsoft.com/library/windows/apps/br225648), [**Windows.Devices.Sensors**](https://msdn.microsoft.com/library/windows/apps/br206408) |
| Классы **Microsoft.Devices.Camera**, **Microsoft.Devices.PhotoCamera** | Класс [**MediaCapture**](https://msdn.microsoft.com/library/windows/apps/br241124). Также класс [**CameraCaptureUI**](https://msdn.microsoft.com/library/windows/apps/br241030) (только Windows). |
| Класс **Microsoft.Devices.CameraButtons** | Класс [**HardwareButtons**](https://msdn.microsoft.com/library/windows/apps/jj207557) |
| Класс **Microsoft.Devices.CameraVideoBrushExtensions** | Класс [**CaptureElement**](https://msdn.microsoft.com/library/windows/apps/br209278) |
| Класс **Microsoft.Devices.Environment** | Нет прямого эквивалента. В качестве обходного пути используйте условную компиляцию и укажите настраиваемый символ. Или можно создать обходной путь с помощью свойства [IsAttached](http://msdn.microsoft.com/library/e299w87h.aspx). |
| Класс **Microsoft.Devices.MediaHistory** | Нет прямого эквивалента |
| Класс **Microsoft.Devices.VibrateController** | Класс [**VibrationDevice**](https://msdn.microsoft.com/library/windows/apps/jj207230) |
| Класс **Microsoft.Devices.Radio.FMRadio** | Нет прямого эквивалента |
| Классы **Microsoft.Devices.Sensors.Accelerometer**, **Compass** | В пространстве имен [**Windows.Devices.Sensors**](https://msdn.microsoft.com/library/windows/apps/br206408) |
| Класс **Microsoft.Devices.Sensors.Gyroscope** | Класс [**Gyrometer**](https://msdn.microsoft.com/library/windows/apps/br225718) |
| Класс **Microsoft.Devices.Sensors.Motion** | Класс [**Inclinometer**](https://msdn.microsoft.com/library/windows/apps/br225766) |
| Globalization | |
| Пространство имен **System.Globalization** | Пространство имен [**Windows.Globalization**](https://msdn.microsoft.com/library/windows/apps/br206813) |
| (ST = **System.Threading**) <br/> Свойство **ST.Thread.CurrentCulture** | (SG = **System.Globalization**) <br/> Свойство **S.CultureInfo.CurrentCulture** |
| (ST = **System.Threading**) <br/> Свойство **ST.Thread.CurrentUICulture** | (SG = **System.Globalization**) <br/> Свойство **S.CultureInfo.CurrentUICulture** |
| Графика и анимация | |
| Пространства имен **Microsoft.Xna.Framework.\***, [XNA Framework Class Library](http://go.microsoft.com/fwlink/p/?LinkId=263769), [Content Pipeline Class Library](http://go.microsoft.com/fwlink/p/?LinkId=263770) | Нет прямого эквивалента. Обычно используется [Microsoft DirectX](https://msdn.microsoft.com/library/windows/desktop/ee663274) и C++. См. разделы [Разработка игр](https://msdn.microsoft.com/library/windows/apps/hh452744) и [Взаимодействие DirectX и XAML](https://msdn.microsoft.com/library/windows/apps/hh825871). |
| Класс **Microsoft.Xna.Framework.Audio.Microphone** | Класс [**MediaCapture**](https://msdn.microsoft.com/library/windows/apps/br241124) |
| Класс **Microsoft.Xna.Framework.Audio.SoundEffect** | Класс [**MediaElement**](https://msdn.microsoft.com/library/windows/apps/br242926) |
| Пространство имен **Microsoft.Xna.Framework.GamerServices** | (WPS = **Windows.Phone.System**) <br/> Пространство имен [**WPS.UserProfile.GameServices.Core**](https://msdn.microsoft.com/library/windows/apps/jj207609) |
| Класс **Microsoft.Xna.Framework.GamerServices.Guide** | Нет прямого эквивалента |
| Класс **Microsoft.Xna.Framework.Input.GamePad** | Класс [**HardwareButtons**](https://msdn.microsoft.com/library/windows/apps/jj207557) |
| Класс **Microsoft.Xna.Framework.Input.Touch.TouchPanel** | Класс [**GestureRecognizer**](https://msdn.microsoft.com/library/windows/apps/br241937) |
| (MXFM = **Microsoft.Xna.Framework.Media**) <br/> Классы **MXFM.MediaLibrary**, **MXFM.PhoneExtensions.MediaLibraryExtensions** | Класс [**KnownFolders**](https://msdn.microsoft.com/library/windows/apps/br227151) |
| Класс **Microsoft.Xna.Framework.Media.MediaQueue** | Класс [**SystemMediaTransportControls**](https://msdn.microsoft.com/library/windows/apps/dn278677) |
| Класс **Microsoft.Xna.Framework.Media.Playlist** | Класс [**BackgroundMediaPlayer**](https://msdn.microsoft.com/library/windows/apps/dn652527) |
| Пространство имен **System.Windows.Media** | Пространство имен [**Windows.UI.Xaml.Media**](/uwp/api/Windows.UI.Xaml.Media) |
| Класс **System.Windows.Media.RadialGradientBrush** | Нет прямого эквивалента. См. раздел [Мультимедиа и графика](wpsl-to-uwp-porting-xaml-and-ui.md). |
| Пространство имен **System.Windows.Media.Animation** | Пространство имен [**Windows.UI.Xaml.Media.Animation**](https://msdn.microsoft.com/library/windows/apps/br243232) |
| Пространство имен **System.Windows.Media.Effects** | Нет прямого эквивалента |
| Пространство имен **System.Windows.Media.Imaging** | Пространство имен [**Windows.UI.Xaml.Media.Imaging**](https://msdn.microsoft.com/library/windows/apps/br243258) |
| Пространство имен **System.Windows.Media.Media3D** | Пространство имен [**Windows.UI.Xaml.Media.Media3D**](https://msdn.microsoft.com/library/windows/apps/br243274) |
| Пространство имен **System.Windows.Shapes** | Пространство имен [**Windows.UI.Xaml.Shapes**](/uwp/api/Windows.UI.Xaml.Shapes) |
| Средства запуска и выбора | |
| Классы **Microsoft.Phone.Tasks.AddressChooserTask**, **EmailAddressChooserTask**, **PhoneNumberChooserTask** | Класс [**ContactPicker**](https://msdn.microsoft.com/library/windows/apps/br224913) |
| Классы **Microsoft.Phone.Tasks.AddWalletItemTask**, **AddWalletItemResult** | Пространство имен [**Windows.ApplicationModel.Wallet**](https://msdn.microsoft.com/library/windows/apps/dn631399) |
| Классы **Microsoft.Phone.Tasks.BingMapsDirectionsTask**, **BingMapsTask** | Нет прямого эквивалента |
| Класс **Microsoft.Phone.Tasks.CameraCaptureTask** | Класс [**MediaCapture**](https://msdn.microsoft.com/library/windows/apps/br241124). Также класс [**CameraCaptureUI**](https://msdn.microsoft.com/library/windows/apps/br241030) (только Windows). |
| **Microsoft.Phone.Tasks.MarketplaceDetailTask** | Класс [**CurrentApp**](https://msdn.microsoft.com/library/windows/apps/hh779765) (метод [**RequestAppPurchaseAsync**](https://msdn.microsoft.com/library/windows/apps/hh967813)) |
| Классы **Microsoft.Phone.Tasks.ConnectionSettingsTask**, **MarketplaceHubTask**, **MarketplaceReviewTask**, **MarketplaceSearchTask**, **MediaPlayerLauncher**, **SearchTask**, **SmsComposeTask**, **WebBrowserTask** | Класс [**Launcher**](https://msdn.microsoft.com/library/windows/apps/br241801) |
| Класс **Microsoft.Phone.Tasks.EmailComposeTask** | Класс [**EmailMessage**](https://msdn.microsoft.com/library/windows/apps/dn631270) |
| Класс **Microsoft.Phone.Tasks.GameInviteTask** | Нет прямого эквивалента |
| Классы **Microsoft.Phone.Tasks.MapDownloaderTask**, **MapsDirectionsTask**, **MapsTask**, **MapUpdaterTask** | Нет прямого эквивалента |
| Класс **Microsoft.Phone.Tasks.PhoneCallTask** | Класс [**PhoneCallManager**](https://msdn.microsoft.com/library/windows/apps/dn624832) |
| Класс **Microsoft.Phone.Tasks.PhotoChooserTask** | Класс [**FileOpenPicker**](https://msdn.microsoft.com/library/windows/apps/br207847) |
| Класс **Microsoft.Phone.Tasks.SaveAppointmentTask** | Класс [**AppointmentManager**](https://msdn.microsoft.com/library/windows/apps/dn297254) |
| Классы **Microsoft.Phone.Tasks.SaveContactTask**, **SaveEmailAddressTask**, **SavePhoneNumberTask** | Класс [**StoredContact**](https://msdn.microsoft.com/library/windows/apps/jj207727) (только Windows Phone) | 
| Класс **Microsoft.Phone.Tasks.SaveRingtoneTask** | Нет прямого эквивалента |
| Классы **Microsoft.Phone.Tasks.ShareLinkTask**, **ShareMediaTask**, **ShareStatusTask** | Классы [**DataPackage**](https://msdn.microsoft.com/library/windows/apps/br205873) |
| Расположение | |
| Пространство имен **System.Device.Location** | Пространство имен [**Windows.Devices.Geolocation**](https://msdn.microsoft.com/library/windows/apps/br225603) |
| Класс **System.Device.GeoCoordinateWatcher** | Класс [**Geolocator**](https://msdn.microsoft.com/library/windows/apps/br225534) |
| Карты | |
| Пространства имен **Microsoft.Phone.Maps** | Пространство имен [**Windows.Services.Maps**](https://msdn.microsoft.com/library/windows/apps/dn636979) |
| Пространство имен **Microsoft.Phone.Maps.Controls** | Пространство имен [**Windows.UI.Xaml.Controls.Maps**](https://msdn.microsoft.com/library/windows/apps/dn610751) |
| Класс **Microsoft.Phone.Maps.Controls.Map** | Класс [**MapControl**](https://msdn.microsoft.com/library/windows/apps/dn637004) |
| Пространство имен **Microsoft.Phone.Maps.Services** | Пространство имен [**Windows.Services.Maps**](https://msdn.microsoft.com/library/windows/apps/dn636979) |
| Классы **Microsoft.Phone.Maps.Services.GeocodeQuery**, **ReverseGeocodeQuery** | Класс [**MapLocationFinder**](https://msdn.microsoft.com/library/windows/apps/dn627550) |
| Класс **System.Device.Location.GeoCoordinate** | Класс [**Geopoint**](https://msdn.microsoft.com/library/windows/apps/dn263675) |
| Класс **Microsoft.Phone.Maps.Services.Route** | Класс [**MapRoute**](https://msdn.microsoft.com/library/windows/apps/dn636937) |
| Класс **Microsoft.Phone.Maps.Services.RouteQuery** | Класс [**MapRouteFinder**](https://msdn.microsoft.com/library/windows/apps/dn636938) |
| Получение дохода | |
| Пространство имен **Microsoft.Phone.Marketplace** | Пространство имен [**Windows.ApplicationModel.Store**](https://msdn.microsoft.com/library/windows/apps/br225197) |
| Мультимедиа | |
| Пространство имен **Microsoft.Phone.Media** | Класс [**MediaElement**](https://msdn.microsoft.com/library/windows/apps/br242926) |
| Сеть | |
| (MPNN = **Microsoft.Phone.Net.NetworkInformation**) <br/> Класс **MPNN.DeviceNetworkInformation** | Классы [**Hostname**](https://msdn.microsoft.com/library/windows/apps/br207113), [**NetworkInformation**](https://msdn.microsoft.com/library/windows/apps/br207293) |
| (MPNN = **Microsoft.Phone.Net.NetworkInformation**) <br/> Класс **MPNN.NetworkInterface** | Класс [**NetworkInformation**](https://msdn.microsoft.com/library/windows/apps/br207293) |
| (MPNN = **Microsoft.Phone.Net.NetworkInformation**) <br/> Класс **MPNN.NetworkInterfaceInfo** | Класс [**ConnectionProfile**](https://msdn.microsoft.com/library/windows/apps/br207249) |
| (MPNN = **Microsoft.Phone.Net.NetworkInformation**) <br/> Класс **MPNN.NetworkInterfaceList** | Класс [**NetworkInformation**](https://msdn.microsoft.com/library/windows/apps/br207293) |
| (MPNN = **Microsoft.Phone.Net.NetworkInformation**) <br/> Класс **MPNN.SocketExtensions** | Нет прямого эквивалента |
| (MPNN = **Microsoft.Phone.Net.NetworkInformation**) <br/> Класс **MPNN.WebRequestExtensions** | Нет прямого эквивалента |
| Пространство имен **Microsoft.Phone.Networking.Voip** | Нет прямого эквивалента |
| Класс **System.Net.CookieCollection** | По-прежнему поддерживается, только некоторые свойства отсутствуют (например, IsReadOnly) |
| Класс **System.Net.DownloadProgressChangedEventArgs** и похожие классы, связанные с **System.Net.WebClient** | Класс [**HttpClient**](https://msdn.microsoft.com/library/windows/apps/dn298639) (или [System.Net.Http.HttpClient](https://msdn.microsoft.com/library/system.net.http.httpclient(v=vs.118).aspx)). Производные от [System.Net.Http.StreamContent](https://msdn.microsoft.com/library/system.net.http.streamcontent.aspx) для измерения хода выполнения. |
| Классы **System.Net.DnsEndPoint**, **IPAddress** | Эти классы все еще поддерживаются, но некоторые свойства отсутствуют. Можно также перенести в класс [**HostName**](https://msdn.microsoft.com/library/windows/apps/br207113). |
| Класс **System.Net.HttpUtility** | Класс [**HtmlFormatHelper**](https://msdn.microsoft.com/library/windows/apps/hh738437) |
| Класс **System.Net.HttpWebRequest** | Частичная поддержка, при этом рекомендованнойперспективной альтернативой является класс [**HttpClient**](https://msdn.microsoft.com/library/windows/apps/dn298639) (или [System.Net.Http.HttpClient](https://msdn.microsoft.com/library/system.net.http.httpclient(v=vs.118).aspx)). Эти API-интерфейсы используют [System.Net.Http.HttpRequestMessage](https://msdn.microsoft.com/library/system.net.http.httprequestmessage.aspx) для представления запроса HTTP. |
| Класс **System.Net.HttpWebResponse** | По-прежнему поддерживается, но вместо Close() используйте Dispose(). Рекомендуемой перспективной альтернативой является класс [**HttpClient**](https://msdn.microsoft.com/library/windows/apps/dn298639) (или [System.Net.Http.HttpClient](https://msdn.microsoft.com/library/system.net.http.httpclient(v=vs.118).aspx)). Эти API используют [System.Net.Http.HttpResponseMessage](https://msdn.microsoft.com/library/system.net.http.httpresponsemessage.aspx) для представления ответа HTTP. |
| (SNN = **System.Net.NetworkInformation**) <br/> Класс **SNN.NetworkChange** | По-прежнему поддерживается за исключением конструктора. |
| Класс **System.Net.OpenReadCompletedEventArgs** и похожие классы, связанные с **System.Net.WebClient** | Класс [**HttpClient**](https://msdn.microsoft.com/library/windows/apps/dn298639) (или [System.Net.Http.HttpClient](https://msdn.microsoft.com/library/system.net.http.httpclient.aspx)) |
| Класс **System.Net.Sockets.Socket** | По-прежнему поддерживается, но вместо Close() используйте Dispose(). Можно также перенести в класс [**StreamSocket**](https://msdn.microsoft.com/library/windows/apps/br226882). |
| Класс **System.Net.Sockets.SocketException** | По-прежнему поддерживается, но используйте свойство SocketErrorCode вместо ErrorCode. |
| Классы **System.Net.Sockets.UdpAnySourceMulticastClient**, **UdpSingleSourceMulticastClient** | Классы [**DatagramSocket**](https://msdn.microsoft.com/library/windows/apps/br241319) |
| Класс **System.Net.UploadProgressChangedEventArgs** и похожие классы, связанные с **System.Net.WebClient** | Класс [**HttpClient**](https://msdn.microsoft.com/library/windows/apps/dn298639) (или [System.Net.Http.HttpClient](https://msdn.microsoft.com/library/system.net.http.httpclient.aspx)) |
| Класс **System.Net.WebClient** | Класс [**HttpClient**](https://msdn.microsoft.com/library/windows/apps/dn298639) (или [System.Net.Http.HttpClient](https://msdn.microsoft.com/library/system.net.http.httpclient.aspx)) |
| Класс **System.Net.WebRequest** | Частичная поддержка (другой набор свойств), при этом рекомендованная перспективная альтернатива— класс [**HttpClient**](https://msdn.microsoft.com/library/windows/apps/dn298639) (или [System.Net.Http.HttpClient](https://msdn.microsoft.com/library/system.net.http.httpclient(v=vs.118).aspx)). Эти API-интерфейсы используют [System.Net.Http.HttpRequestMessage](https://msdn.microsoft.com/library/system.net.http.httprequestmessage.aspx) для представления запроса HTTP. |
| Класс **System.Net.WebResponse** | По-прежнему поддерживается, но вместо Close() используйте Dispose(). Рекомендуемой перспективной альтернативой является класс [**HttpClient**](https://msdn.microsoft.com/library/windows/apps/dn298639) (или [System.Net.Http.HttpClient](https://msdn.microsoft.com/library/system.net.http.httpclient(v=vs.118).aspx)). Эти API используют [System.Net.Http.HttpResponseMessage](https://msdn.microsoft.com/library/system.net.http.httpresponsemessage.aspx) для представления ответа HTTP. |
| (SN = **System.Net**) <br/> Класс **SN.WriteStreamClosedEventArgs** | Класс [**HttpClient**](https://msdn.microsoft.com/library/windows/apps/dn298639) (или [System.Net.Http.HttpClient](https://msdn.microsoft.com/library/system.net.http.httpclient.aspx)) |
| (SN = **System.Net**) <br/> Класс **SN.WriteStreamClosedEventHandler** | Класс [**HttpClient**](https://msdn.microsoft.com/library/windows/apps/dn298639) (или [System.Net.Http.HttpClient](https://msdn.microsoft.com/library/system.net.http.httpclient.aspx)) |
| Класс **System.UriFormatException** | Класс **System.FormatException** |
| Уведомления | |
| Пространство имен MPN = **Microsoft.Phone.Notification** | Пространства имен [**Windows.UI.Notifications**](https://msdn.microsoft.com/library/windows/apps/br208661), [**Windows.Networking.PushNotifications**](https://msdn.microsoft.com/library/windows/apps/br241307) |
| MPN = **Microsoft.Phone.Notification** <br/> Класс **MPN.HttpNotification** | Класс [**TileNotification**](https://msdn.microsoft.com/library/windows/apps/br208616) |
| MPN = **Microsoft.Phone.Notification** <br/> Класс **MPN.HttpNotificationChannel** | Класс [**PushNotificationChannel**](https://msdn.microsoft.com/library/windows/apps/br241283) |
| Программирование | |
| Пространство имен **System** | Пространство имен [**Windows.Foundation**](https://msdn.microsoft.com/library/windows/apps/br226021) |
| Классы **System.Diagnostics.StackFrame**, **StackTrace** | Нет прямого эквивалента |
| Пространство имен **System.Diagnostics** | Пространство имен [**Windows.Foundation.Diagnostics**](https://msdn.microsoft.com/library/windows/apps/br206677) |
| Интерфейс **System.ICloneable** | Пользовательский метод, который возвращает соответствующий тип. |
| Класс **System.Reflection.Emit.ILGenerator** | Нет прямого эквивалента |
| Реактивные расширения | |
| Пространство имен **Microsoft.Phone.Reactive** | Нет прямого эквивалента |
| Отражение | |
| Класс **System.Type** | Класс **System.Reflection.TypeInfo** См. раздел [Отражение в .NET Framework для приложений UWP](https://msdn.microsoft.com/library/hh535795.aspx). |
| Ресурсы | |
| Класс **System.Resources.ResourceManager** | (WA = **Windows.ApplicationModel**)<br/>Пространства имен [**WA.Resources.Core**](https://msdn.microsoft.com/library/windows/apps/br225039) и [**WA.Resources**](https://msdn.microsoft.com/library/windows/apps/br206022), класс [**ResourceManager**](https://msdn.microsoft.com/library/windows/apps/br206078). См. раздел [Создание и получение ресурсов в приложениях среды выполнения Windows](https://msdn.microsoft.com/library/windows/apps/xaml/hh694557.aspx). |
| Защищенный элемент | |
| (MPS = **Microsoft.Phone.SecureElement**) <br/> Классы **MPS.SecureElementChannel**, **MPS.SecureElementSession** | Класс [**SmartCardConnection**](https://msdn.microsoft.com/library/windows/apps/dn608002) |
| (MPS = **Microsoft.Phone.SecureElement**) <br/> Класс **MPS.SecureElementReader** | Класс [**SmartCardReader**](https://msdn.microsoft.com/library/windows/apps/dn263857) |
| Безопасность | |
| (SSC = **System.Security.Cryptography**) <br/> Классы **SSC.Aes**, **SSC.RSA** | Класс [**CryptographicEngine**](https://msdn.microsoft.com/library/windows/apps/br241490) |
| (SSC = **System.Security.Cryptography**) <br/> Классы **SSC.HMACSHA256**, **SSC.SHA256** | Класс [**HashAlgorithmProvider**](https://msdn.microsoft.com/library/windows/apps/br241511) |
| (SSC = **System.Security.Cryptography**) <br/> Класс **SSC.ProtectedData** | Класс [**DataProtectionProvider**](https://msdn.microsoft.com/library/windows/apps/br241559) |
| (SSC = **System.Security.Cryptography**) <br/> Класс **SSC.RandomNumberGenerator** | Класс [**CryptographicBuffer**](https://msdn.microsoft.com/library/windows/apps/br227092) |
| (SSC = **System.Security.Cryptography**) <br/> Класс **SSC.X509Certificates.X509Certificate** | Класс [**CertificateEnrollmentManager**](https://msdn.microsoft.com/library/windows/apps/br212075) |
| Оболочка | |
| (MPSh = **Microsoft.Phone.Shell**) <br/> Класс **MPSh.ApplicationBar** | Класс [**CommandBar**](https://msdn.microsoft.com/library/windows/apps/dn279427) |
| (MPSh = **Microsoft.Phone.Shell**) <br/> Класс **MPSh.ApplicationBarIconButton** | Класс [**AppBarButton**](https://msdn.microsoft.com/library/windows/apps/dn279244) (при использовании в свойстве [**PrimaryCommands**](https://msdn.microsoft.com/library/windows/apps/dn279430)) |
| (MPSh = **Microsoft.Phone.Shell**) <br/> Класс **MPSh.ApplicationBarMenuItem** | Класс [**AppBarButton**](https://msdn.microsoft.com/library/windows/apps/dn279244) (при использовании в свойстве [**SecondaryCommands**](https://msdn.microsoft.com/library/windows/apps/dn279434)) |
| (MPSh = **Microsoft.Phone.Shell**) <br/> Классы **MPSh.CycleTileData**, **MPSh.FlipTileData**, **MPSh.IconicTileData**, **MPSh.ShellTileData**, **MPSh.StandardTileData** | Класс [**TileTemplateType**](https://msdn.microsoft.com/library/windows/apps/br208621) |
| (MPSh = **Microsoft.Phone.Shell**) <br/> Класс **MPSh.PhoneApplicationService** | Классы [**CoreApplication**](https://msdn.microsoft.com/library/windows/apps/br225016), [**DisplayRequest**](https://msdn.microsoft.com/library/windows/apps/br241816) |
| (MPSh = **Microsoft.Phone.Shell**) <br/> Класс **MPSh.ProgressIndicator** | Класс [**StatusBarProgressIndicator**](https://msdn.microsoft.com/library/windows/apps/dn633865) |
| (MPSh = **Microsoft.Phone.Shell**) <br/> Класс **MPSh.ShellTile** | Класс [**SecondaryTile**](https://msdn.microsoft.com/library/windows/apps/br242183) |
| (MPSh = **Microsoft.Phone.Shell**) <br/> Класс **MPSh.ShellTileSchedule** | Класс [**TileUpdater**](https://msdn.microsoft.com/library/windows/apps/br208628) |
| (MPSh = **Microsoft.Phone.Shell**) <br/> Класс **MPSh.ShellToast** | Класс [**ToastNotificationManager**](https://msdn.microsoft.com/library/windows/apps/br208642) |
| (MPSh = **Microsoft.Phone.Shell**) <br/> Класс **MPSh.SystemTray** | Класс [**StatusBar**](https://msdn.microsoft.com/library/windows/apps/dn633864) |
| Хранилище и ввод-вывод | |
| Классы **Microsoft.Phone.Storage.ExternalStorage**, **ExternalStorageDevice**, **ExternalStorageFile**, **ExternalStorageFolder** | Класс [**KnownFolders**](https://msdn.microsoft.com/library/windows/apps/br227151) |
| Пространство имен **System.IO** | Пространства имен [**Windows.Storage**](https://msdn.microsoft.com/library/windows/apps/br227346), [**Windows.Storage.Streams**](https://msdn.microsoft.com/library/windows/apps/br241791) |
| Класс **System.IO.Directory** | Класс [**StorageFolder**](https://msdn.microsoft.com/library/windows/apps/br227230) |
| Класс **System.IO.File** | Классы [**StorageFile**](https://msdn.microsoft.com/library/windows/apps/br227171) и [**PathIO**](https://msdn.microsoft.com/library/windows/apps/hh701663)
| (SII = **System.IO.IsolatedStorage**) <br/> Класс **SII.IsolatedStorageFile** |Свойство [**ApplicationData.LocalFolder**](https://msdn.microsoft.com/library/windows/apps/br241621) |
| (SII = **System.IO.IsolatedStorage**) <br/> Класс **SII.IsolatedStorageSettings** | Свойство [**ApplicationData.LocalSettings**](https://msdn.microsoft.com/library/windows/apps/windows.storage.applicationdata.localsettings.aspx) |
| Класс **System.IO.Stream** | По-прежнему поддерживается, используйте ReadAsync() и WriteAsync() вместо BeginRead()/EndRead() и BeginWrite()/EndWrite(). |
| Бумажник | |
| Пространство имен **Microsoft.Phone.Wallet** | Пространство имен [**Windows.ApplicationModel.Wallet**](https://msdn.microsoft.com/library/windows/apps/dn631399) |
| Xml | |
| (SX = **System.Xml**) | Метод **SX.XmlConvert.ToDateTime** |
| (SX = **System.Xml**) | Метод **SX.XmlConvert.ToDateTimeOffset** |


Следующий раздел называется [Перенос проекта](wpsl-to-uwp-porting-to-a-uwp-project.md).


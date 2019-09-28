---
description: В этом разделе содержится комплексное сопоставление интерфейсов API Windows Phone Silverlight с их эквивалентами универсальная платформа Windows (UWP).
title: Windows Phone сопоставления Silverlight с пространством имен и классами UWP
ms.assetid: 33f06706-4790-48f3-a2e4-ebef9ddb61a4
ms.date: 02/08/2017
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: 2e02d74df59bae4dd4bdaa909c97866da754db93
ms.sourcegitcommit: a20457776064c95a74804f519993f36b87df911e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/27/2019
ms.locfileid: "71339922"
---
# <a name="windowsphone-silverlight-to-uwp-api-mappings"></a>Windows Phone сопоставления Silverlight с UWP API


В этом разделе содержится комплексное сопоставление интерфейсов API Windows Phone Silverlight с их эквивалентами универсальная платформа Windows (UWP). Обычно не существует полных функциональных аналогов, так как одна платформа может иметь большую или меньшую функциональность по сравнению с ее аналогом в пространстве имен или классе.

Таблица сопоставлений поможет вам при работе в проекте UWP и повторном использовании исходного кода из проекта Windows Phone Silverlight. Между двумя платформами есть различия в именах пространств имен и классов (в том числе в элементах управления пользовательского интерфейса). В большинстве случаев нужно лишь изменить имя пространства имен, и код будет успешно работать. Иногда изменяется не только имя пространства имен, но и имя класса или API. Иногда сопоставление требует многих усилий, а в некоторых случаях требуется изменить подход.

**Как использовать таблицу:  ** Сначала найдите имя класса, который вы используете. Классы указываются, когда для сопоставления требуются более сложные действия, чем просто изменение имени пространства имен. Если класс не указан, тогда сопоставление заключается лишь в изменении пространства имен. Найдите имя пространства имен класса, и таким образом вы получите соответствующее имя пространства имен в UWP. Ваш класс будет указан в этом пространстве имен. Если вашего пространства имен нет в списке, это значит, что его имя не изменялось.

**Примечание**. @no__t 1Windows 10 поддерживает гораздо больше .NET Framework чем приложение для Магазина Windows Phone. Например, в Windows 10 имеется несколько пространств имен System. ServiceModel. \*, а также System.Net, System .NET. NetworkInformation и System .NET. Sockets.
Кроме того, в приложении Windows 10 вы получите преимущества от .NET Native, которая является технологией предварительной компиляции, преобразующей MSIL в собственный код машинного кода. Приложения .NET Native запускаются быстрее, используют меньше памяти и меньше заряда батареи, чем аналоги MSIL.

| Windows Phone Silverlight | Среда выполнения Windows |
| ------------------------- | --------------- |
| Реклама | |
| Класс **Microsoft.Advertising.Mobile.UI.AdControl** | Класс [AdControl](https://docs.microsoft.com/windows/uwp/monetize/display-ads-using-the-microsoft-advertising-libraries) |
| Сигналы, напоминания и фоновые агенты | |
| Класс **Microsoft.Phone.BackgroundAgent** | Класс [**баккграундтаскбуилдер**](https://docs.microsoft.com/uwp/api/Windows.ApplicationModel.Background.BackgroundTaskBuilder) |
| Пространство имен **Microsoft.Phone.Scheduler** | Пространство имен [**Windows. ApplicationModel. Background**](https://docs.microsoft.com/uwp/api/Windows.ApplicationModel.Background) |
| Класс **Microsoft.Phone.Scheduler.Alarm** | Классы [**баккграундтаскбуилдер**](https://docs.microsoft.com/uwp/api/Windows.ApplicationModel.Background.BackgroundTaskBuilder) и [**тоастнотификатионманажер**](https://docs.microsoft.com/uwp/api/Windows.UI.Notifications.ToastNotificationManager) |
| Классы **Microsoft.Phone.Scheduler.PeriodicTask**, **ScheduledAction**, **ScheduledActionService**, **ScheduledTask**, **ScheduledTaskAgent** | Класс [**баккграундтаскбуилдер**](https://docs.microsoft.com/uwp/api/Windows.ApplicationModel.Background.BackgroundTaskBuilder) |
| Класс **Microsoft.Phone.Scheduler.Reminder** | Классы [**баккграундтаскбуилдер**](https://docs.microsoft.com/uwp/api/Windows.ApplicationModel.Background.BackgroundTaskBuilder) и [**тоастнотификатионманажер**](https://docs.microsoft.com/uwp/api/Windows.UI.Notifications.ToastNotificationManager) |
| Класс **Microsoft.Phone.PictureDecoder** | Класс [**BitmapDecoder завершает**](https://docs.microsoft.com/uwp/api/Windows.Graphics.Imaging.BitmapDecoder) |
| Пространство имен **Microsoft.Phone.BackgroundAudio** | Пространство имен [**Windows. Media. воспроизведения**](https://docs.microsoft.com/uwp/api/Windows.Media.Playback) |
| Пространство имен **Microsoft.Phone.BackgroundTransfer** | Пространство имен [**Windows. Networking. баккграундтрансфер**](https://docs.microsoft.com/uwp/api/Windows.Networking.BackgroundTransfer) |
| Модель и среда приложения | |
| Класс **System.AppDomain** | Нет прямого эквивалента. См. классы [**Application**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Application), [**CoreApplication**](https://docs.microsoft.com/uwp/api/Windows.ApplicationModel.Core.CoreApplication) |
| Класс **System.Environment** | Нет прямого эквивалента |
| Класс **System.ComponentModel.Annotations**  | Нет прямого эквивалента |
| Класс **System.ComponentModel.BackgroundWorker** | Класс [**ThreadPool**](https://docs.microsoft.com/uwp/api/Windows.System.Threading.ThreadPool) |
| Класс **System.ComponentModel.DesignerProperties** | Класс [**десигнмоде**](https://docs.microsoft.com/uwp/api/Windows.ApplicationModel.DesignMode) |
| Классы **System.Threading.Thread**, **System.Threading.ThreadPool** | Класс [**ThreadPool**](https://docs.microsoft.com/uwp/api/Windows.System.Threading.ThreadPool) |
| (ST = **System.Threading**) <br/> Метод **ST.Thread.MemoryBarrier** | (ST = **System.Threading**) <br/> Метод **ST.Interlocked.MemoryBarrier** |
| (ST = **System.Threading**) <br/> Свойство **ST.Thread.ManagedThreadId** | (S = **System**) <br/> Свойство **S.Environment.ManagedThreadId** |
| Класс **System.Threading.Timer** | Класс [**ThreadPool**](https://docs.microsoft.com/uwp/api/Windows.System.Threading.ThreadPoolTimer) |
| (SWT = **System.Windows.Threading**) <br/> Класс **SWT.Dispatcher** | Класс [**CoreDispatcher**](https://docs.microsoft.com/uwp/api/Windows.UI.Core.CoreDispatcher) |
| (SWT = **System.Windows.Threading**) <br/> Класс **SWT.DispatcherTimer** | Класс [**DispatcherTimer**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.DispatcherTimer) |
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
| Пространство имен **Microsoft.Phone.UserData** | Пространства имен [**Windows. ApplicationModel. Contacts**](https://docs.microsoft.com/uwp/api/Windows.ApplicationModel.Contacts), [**Windows. ApplicationModel. Встреча**](https://docs.microsoft.com/uwp/api/Windows.ApplicationModel.Appointments) |
| (MPU = **Microsoft.Phone.UserData**) <br/> Классы **MPU.Account**, **ContactAddress**, **ContactCompanyInformation**, **ContactEmailAddress**, **ContactPhoneNumber** | Класс [**Contact**](https://docs.microsoft.com/uwp/api/Windows.ApplicationModel.Contacts.Contact) |
| (MPU = **Microsoft.Phone.UserData**) <br/> Класс **MPU.Appointments** | Класс [**аппоинтменткалендар**](https://docs.microsoft.com/uwp/api/Windows.ApplicationModel.Appointments.AppointmentCalendar) |
| (MPU = **Microsoft.Phone.UserData**) <br/> Класс **MPU.Contacts** | Класс [**контактсторе**](https://docs.microsoft.com/uwp/api/Windows.ApplicationModel.Contacts.ContactStore) |
| Элементы управления и инфраструктура пользовательского интерфейса | |
| Класс **ControlTiltEffect.TiltEffect** | Анимации из библиотеки анимации среды выполнения Windows встроены в стили стандартных элементов управления по умолчанию. См. раздел [Анимация](wpsl-to-uwp-porting-xaml-and-ui.md). |
| Пространство имен **Microsoft.Phone.Controls** | Пространство имен [**Windows. UI. XAML. Controls**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls) |
| (MPC = **Microsoft.Phone.Controls**) <br/> Класс **MPC.ContextMenu** | Класс [**всплывающее меню**](https://docs.microsoft.com/uwp/api/Windows.UI.Popups.PopupMenu) |
| (MPC = **Microsoft.Phone.Controls**) <br/>Класс **MPC.DatePickerPage** | Класс [**датепиккерфлйоут**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.DatePickerFlyout) |
| (MPC = **Microsoft.Phone.Controls**) <br/>Класс **MPC.GestureListener** | Класс [**GestureRecognizer**](https://docs.microsoft.com/uwp/api/Windows.UI.Input.GestureRecognizer) |
| (MPC = **Microsoft.Phone.Controls**) <br/>Класс **MPC.LongListSelector** | Класс [**семантикзум**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.SemanticZoom) |
| (MPC = **Microsoft.Phone.Controls**) <br/>Класс **MPC.ObscuredEventArgs** | [**Системпротектион**](https://docs.microsoft.com/uwp/api/Windows.Phone.System.SystemProtection), классы [**виндовактиватедевентаргс**](https://docs.microsoft.com/uwp/api/Windows.UI.Core.WindowActivatedEventArgs) |
| (MPC = **Microsoft.Phone.Controls**) <br/>Класс**MPC.Panorama** | Класс [**Hub**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Hub) |
| (MPC = **Microsoft.Phone.Controls**) <br/>**MPC.PhoneApplicationFrame**,<br/>(SWN = **System.Windows.Navigation**) <br/>Классы **SWN.NavigationService** | Класс [**Frame**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Frame) |
| (MPC = **Microsoft.Phone.Controls**) <br/>Класс **MPC.PhoneApplicationPage** | Класс [**Page**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Page) |
| (MPC = **Microsoft.Phone.Controls**) <br/>Класс **MPC.TiltEffect** | Класс [**поинтердовнсемеаниматион**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.Animation.PointerDownThemeAnimation) |
| (MPC = **Microsoft.Phone.Controls**) <br/>Класс **MPC.TimePickerPage** | Класс [**тимепиккерфлйоут**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.TimePickerFlyout) |
| (MPC = **Microsoft.Phone.Controls**) <br/>Класс **MPC.WebBrowser** | Класс [**WebView**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.WebView) |
| (MPC = **Microsoft.Phone.Controls**) <br/>Класс **MPC.WebBrowserExtensions** | Нет прямого эквивалента |
| (MPC = **Microsoft.Phone.Controls**) <br/>Класс **MPC.WrapPanel** | Нет прямого эквивалента для макетирования. [**Итемсврапгрид**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.ItemsWrapGrid) и [**врапгрид**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.WrapGrid) можно использовать в шаблоне панели элементы элемента управления Items. |
| (MPD = **Microsoft.Phone.Data**) <br/>Пространство имен **MPD.Linq** | Нет прямого эквивалента |
| (MPD = **Microsoft.Phone.Data**) <br/>Пространство имен **MPD.Linq.Mapping** | Нет прямого эквивалента |
| Пространство имен **Microsoft.Phone.Globalization** | Нет прямого эквивалента |
| (MPI = **Microsoft.Phone.Info**) <br/>Классы **MPI.DeviceExtendedProperties**, **DeviceStatus** | [**Еасклиентдевицеинформатион**](https://docs.microsoft.com/uwp/api/Windows.Security.ExchangeActiveSyncProvisioning.EasClientDeviceInformation), [**мемориманажер**](https://docs.microsoft.com/uwp/api/Windows.System.MemoryManager) классы. Дополнительные сведения см. в разделе [Состояние устройства](wpsl-to-uwp-input-and-sensors.md). |
| (MPI = **Microsoft.Phone.Info**) <br/>Класс **MPI.MediaCapabilities** | Нет прямого эквивалента |
| (MPI = **Microsoft.Phone.Info**) <br/>Класс **MPI.UserExtendedProperties** | Класс [**адвертисингманажер**](https://docs.microsoft.com/uwp/api/Windows.System.UserProfile.AdvertisingManager) |
| Пространство имен **System.Windows** | Пространство имен [**Windows. UI. XAML**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml) |
| Пространство имен **System.Windows.Automation** | Пространство имен [**Windows. UI. XAML. Automation**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Automation) |
| Пространства имен **System.Windows.Controls**, **System.Windows.Input** | Пространства имен Windows [ **. UI. Core**](https://docs.microsoft.com/uwp/api/Windows.UI.Core), [**Windows. UI. Input**](https://docs.microsoft.com/uwp/api/Windows.UI.Input), [**Windows. UI. XAML. Controls**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls) |
| Классы **System.Windows.Controls.DrawingSurface**, **DrawingSurfaceBackgroundGrid** | Класс [**SwapChainPanel**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.SwapChainPanel) |
| Класс **System.Windows.Controls.RichTextBox** | Класс [**ричедитбокс**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.RichEditBox) |
| Класс **System.Windows.Controls.WrapPanel** | Нет прямого эквивалента для макетирования. [**Итемсврапгрид**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.ItemsWrapGrid) и [**врапгрид**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.WrapGrid) можно использовать в шаблоне панели элементы элемента управления Items. |
| Пространство имен **System.Windows.Controls.Primitives** | Пространство имен [**Windows. UI. XAML. Controls. примитивы**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Primitives) |
| Пространство имен **System.Windows.Controls.Shapes** | Пространство имен [**Windows. UI. XAML. Controls. Shapes**](/uwp/api/Windows.UI.Xaml.Shapes) |
| Пространство имен **System.Windows.Data** | Пространство имен [**Windows. UI. XAML. Data**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Data) |
| Пространство имен **System.Windows.Documents** | Пространство имен [**Windows. UI. XAML. Documents**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Documents) |
| Пространство имен **System.Windows.Ink** | Нет прямого эквивалента |
| Пространство имен **System.Windows.Markup** | Пространство имен [**Windows. UI. XAML. Markup**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Markup) | 
| Пространство имен **System.Windows.Navigation** | Пространство имен [**Windows. UI. XAML. Navigation**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Navigation) |
| Событие **System.Windows.UIElement.Tap**, делегат **EventHandler&lt;GestureEventArgs&gt;** | Событие [**касания**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.uielement.tapped) , делегат [**таппедевенсандлер**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.input.tappedeventhandler) |
| Данные и службы |  |
| Класс **System.Data.Linq.DataContext** | Нет прямого эквивалента |
| Класс **System.Data.Linq.Mapping.ColumnAttribute** | Нет прямого эквивалента |
| Класс **System.Data.Linq.SqlClient.SqlHelpers** | Нет прямого эквивалента |
| Устройства | |
| Пространства имен **Microsoft.Devices**, **Microsoft.Devices.Sensors** | Пространства имен Windows [ **. Devices. Enumeration**](https://docs.microsoft.com/uwp/api/Windows.Devices.Enumeration), [**Windows. Devices. reenumeration. PnP**](https://docs.microsoft.com/uwp/api/Windows.Devices.Enumeration.Pnp), [**Windows. Devices. Input**](https://docs.microsoft.com/uwp/api/Windows.Devices.Input), [**Windows. Devices. Sensors**](https://docs.microsoft.com/uwp/api/Windows.Devices.Sensors) . |
| Классы **Microsoft.Devices.Camera**, **Microsoft.Devices.PhotoCamera** | Класс [**медиакаптуре**](https://docs.microsoft.com/uwp/api/Windows.Media.Capture.MediaCapture) . Также класс [**CameraCaptureUI**](https://docs.microsoft.com/uwp/api/Windows.Media.Capture.CameraCaptureUI) (только Windows). |
| Класс **Microsoft.Devices.CameraButtons** | Класс [**HardwareButtons**](https://docs.microsoft.com/uwp/api/Windows.Phone.UI.Input.HardwareButtons) |
| Класс **Microsoft.Devices.CameraVideoBrushExtensions** | Класс [**каптурилемент**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.CaptureElement) |
| Класс **Microsoft.Devices.Environment** | Нет прямого эквивалента. В качестве обходного пути используйте условную компиляцию и укажите настраиваемый символ. Или можно создать обходной путь с помощью свойства [IsAttached](https://docs.microsoft.com/dotnet/api/system.diagnostics.debugger.isattached#System_Diagnostics_Debugger_IsAttached). |
| Класс **Microsoft.Devices.MediaHistory** | Нет прямого эквивалента |
| Класс **Microsoft.Devices.VibrateController** | Класс [**вибратиондевице**](https://docs.microsoft.com/uwp/api/Windows.Phone.Devices.Notification.VibrationDevice) |
| Класс **Microsoft.Devices.Radio.FMRadio** | Нет прямого эквивалента |
| Классы **Microsoft.Devices.Sensors.Accelerometer**, **Compass** | В пространстве имен [**Windows.Devices.Sensors**](https://docs.microsoft.com/uwp/api/Windows.Devices.Sensors) |
| Класс **Microsoft.Devices.Sensors.Gyroscope** | Класс [**гирометр**](https://docs.microsoft.com/uwp/api/Windows.Devices.Sensors.Gyrometer) |
| Класс **Microsoft.Devices.Sensors.Motion** | Класс [**Уклономер**](https://docs.microsoft.com/uwp/api/Windows.Devices.Sensors.Inclinometer) |
| Глобализация | |
| Пространство имен **System.Globalization** | Пространство имен [**Windows. Globalization**](https://docs.microsoft.com/uwp/api/Windows.Globalization) |
| (ST = **System.Threading**) <br/> Свойство **ST.Thread.CurrentCulture** | (SG = **System.Globalization**) <br/> Свойство **S.CultureInfo.CurrentCulture** |
| (ST = **System.Threading**) <br/> Свойство **ST.Thread.CurrentUICulture** | (SG = **System.Globalization**) <br/> Свойство **S.CultureInfo.CurrentUICulture** |
| Графика и анимация | |
| Пространства имен **Microsoft. XNA. Framework. \*** , [Библиотека классов XNA Framework](https://go.microsoft.com/fwlink/p/?LinkId=263769), [Библиотека классов конвейера содержимого](https://go.microsoft.com/fwlink/p/?LinkId=263770) | Нет прямого эквивалента. Обычно используется [Microsoft DirectX](https://docs.microsoft.com/windows/desktop/directx) и C++. См. разделы [Разработка игр](https://docs.microsoft.com/previous-versions/windows/apps/hh452744(v=win.10)) и [Взаимодействие DirectX и XAML](https://docs.microsoft.com/previous-versions/windows/apps/hh825871(v=win.10)). |
| Класс **Microsoft.Xna.Framework.Audio.Microphone** | Класс [**медиакаптуре**](https://docs.microsoft.com/uwp/api/Windows.Media.Capture.MediaCapture) |
| Класс **Microsoft.Xna.Framework.Audio.SoundEffect** | Класс [**MediaElement**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.MediaElement) |
| Пространство имен **Microsoft.Xna.Framework.GamerServices** | (WPS = **Windows.Phone.System**) <br/> [**WPS. Пространство имен UserProfile. Гамесервицес. Core**](https://docs.microsoft.com/uwp/api/Windows.Phone.System.UserProfile.GameServices.Core) |
| Класс **Microsoft.Xna.Framework.GamerServices.Guide** | Нет прямого эквивалента |
| Класс **Microsoft.Xna.Framework.Input.GamePad** | Класс [**HardwareButtons**](https://docs.microsoft.com/uwp/api/Windows.Phone.UI.Input.HardwareButtons) |
| Класс **Microsoft.Xna.Framework.Input.Touch.TouchPanel** | Класс [**GestureRecognizer**](https://docs.microsoft.com/uwp/api/Windows.UI.Input.GestureRecognizer) |
| (MXFM = **Microsoft.Xna.Framework.Media**) <br/> Классы **MXFM.MediaLibrary**, **MXFM.PhoneExtensions.MediaLibraryExtensions** | Класс [**кновнфолдерс**](https://docs.microsoft.com/uwp/api/Windows.Storage.KnownFolders) |
| Класс **Microsoft.Xna.Framework.Media.MediaQueue** | Класс [**системмедиатранспортконтролс**](https://docs.microsoft.com/uwp/api/Windows.Media.SystemMediaTransportControls) |
| Класс **Microsoft.Xna.Framework.Media.Playlist** | Класс [**баккграундмедиаплайер**](https://docs.microsoft.com/uwp/api/Windows.Media.Playback.BackgroundMediaPlayer) |
| Пространство имен **System.Windows.Media** | Пространство имен [**Windows. UI. XAML. Media**](/uwp/api/Windows.UI.Xaml.Media) |
| Класс **System.Windows.Media.RadialGradientBrush** | Нет прямого эквивалента. См. раздел [Мультимедиа и графика](wpsl-to-uwp-porting-xaml-and-ui.md). |
| Пространство имен **System.Windows.Media.Animation** | Пространство имен [**Windows. UI. XAML. Media. Animation**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.Animation) |
| Пространство имен **System.Windows.Media.Effects** | Нет прямого эквивалента |
| Пространство имен **System.Windows.Media.Imaging** | Пространство имен [**Windows. UI. XAML. Media. Imaging**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.Imaging) |
| Пространство имен **System.Windows.Media.Media3D** | Пространство имен [**Windows. UI. XAML. Media. Media3D**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.Media3D) |
| Пространство имен **System.Windows.Shapes** | Пространство имен [**Windows. UI. XAML. Shapes**](/uwp/api/Windows.UI.Xaml.Shapes) |
| Средства запуска и выбора | |
| Классы **Microsoft.Phone.Tasks.AddressChooserTask**, **EmailAddressChooserTask**, **PhoneNumberChooserTask** | Класс [**контактпиккер**](https://docs.microsoft.com/uwp/api/Windows.ApplicationModel.Contacts.ContactPicker) |
| Классы **Microsoft.Phone.Tasks.AddWalletItemTask**, **AddWalletItemResult** | Пространство имен [**Windows. ApplicationModel. бумажник**](https://docs.microsoft.com/uwp/api/Windows.ApplicationModel.Wallet) |
| Классы **Microsoft.Phone.Tasks.BingMapsDirectionsTask**, **BingMapsTask** | Нет прямого эквивалента |
| Класс **Microsoft.Phone.Tasks.CameraCaptureTask** | Класс [**медиакаптуре**](https://docs.microsoft.com/uwp/api/Windows.Media.Capture.MediaCapture) . Также класс [**CameraCaptureUI**](https://docs.microsoft.com/uwp/api/Windows.Media.Capture.CameraCaptureUI) (только Windows). |
| **Microsoft. Phone. Tasks. Маркетплацедетаилтаск** | Класс [**куррентапп**](https://docs.microsoft.com/uwp/api/Windows.ApplicationModel.Store.CurrentApp) (метод[**рекуестапппурчасеасинк**](https://docs.microsoft.com/uwp/api/windows.applicationmodel.store.currentapp.requestapppurchaseasync) ) |
| Классы **Microsoft.Phone.Tasks.ConnectionSettingsTask**, **MarketplaceHubTask**, **MarketplaceReviewTask**, **MarketplaceSearchTask**, **MediaPlayerLauncher**, **SearchTask**, **SmsComposeTask**, **WebBrowserTask** | Класс [**запуска**](https://docs.microsoft.com/uwp/api/Windows.System.Launcher) |
| Класс **Microsoft.Phone.Tasks.EmailComposeTask** | Класс [**емаилмессаже**](https://docs.microsoft.com/uwp/api/Windows.ApplicationModel.Email.EmailMessage) |
| Класс **Microsoft.Phone.Tasks.GameInviteTask** | Нет прямого эквивалента |
| Классы **Microsoft.Phone.Tasks.MapDownloaderTask**, **MapsDirectionsTask**, **MapsTask**, **MapUpdaterTask** | Нет прямого эквивалента |
| Класс **Microsoft.Phone.Tasks.PhoneCallTask** | Класс [**фонекаллманажер**](https://docs.microsoft.com/uwp/api/Windows.ApplicationModel.Calls.PhoneCallManager) |
| Класс **Microsoft.Phone.Tasks.PhotoChooserTask** | Класс [**филеопенпиккер**](https://docs.microsoft.com/uwp/api/Windows.Storage.Pickers.FileOpenPicker) |
| Класс **Microsoft.Phone.Tasks.SaveAppointmentTask** | Класс [**аппоинтментманажер**](https://docs.microsoft.com/uwp/api/Windows.ApplicationModel.Appointments.AppointmentManager) |
| Классы **Microsoft.Phone.Tasks.SaveContactTask**, **SaveEmailAddressTask**, **SavePhoneNumberTask** | Класс [**сторедконтакт**](https://docs.microsoft.com/uwp/api/Windows.Phone.PersonalInformation.StoredContact) (только Windows Phone) | 
| Класс **Microsoft.Phone.Tasks.SaveRingtoneTask** | Нет прямого эквивалента |
| Классы **Microsoft.Phone.Tasks.ShareLinkTask**, **ShareMediaTask**, **ShareStatusTask** | Класс [**Package**](https://docs.microsoft.com/uwp/api/Windows.ApplicationModel.DataTransfer.DataPackage) |
| Location | |
| Пространство имен **System.Device.Location** | Пространство имен [**Windows. Devices. Географическое расположение**](https://docs.microsoft.com/uwp/api/Windows.Devices.Geolocation) |
| Класс **System.Device.GeoCoordinateWatcher** | Класс [**геоуказателя**](https://docs.microsoft.com/uwp/api/Windows.Devices.Geolocation.Geolocator) |
| Карты | |
| Пространства имен **Microsoft.Phone.Maps** | Пространство имен [**Windows. Services. Maps**](https://docs.microsoft.com/uwp/api/Windows.Services.Maps) |
| Пространство имен **Microsoft.Phone.Maps.Controls** | Пространство имен [**Windows. UI. XAML. Controls. Maps**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Maps) |
| Класс **Microsoft.Phone.Maps.Controls.Map** | Класс [**MapControl**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Maps.MapControl) |
| Пространство имен **Microsoft.Phone.Maps.Services** | Пространство имен [**Windows. Services. Maps**](https://docs.microsoft.com/uwp/api/Windows.Services.Maps) |
| Классы **Microsoft.Phone.Maps.Services.GeocodeQuery**, **ReverseGeocodeQuery** | Класс [**маплокатионфиндер**](https://docs.microsoft.com/uwp/api/Windows.Services.Maps.MapLocationFinder) |
| Класс **System.Device.Location.GeoCoordinate** | Класс [**геоточек**](https://docs.microsoft.com/uwp/api/Windows.Devices.Geolocation.Geopoint) |
| Класс **Microsoft.Phone.Maps.Services.Route** | Класс [**файл MapRoute**](https://docs.microsoft.com/uwp/api/Windows.Services.Maps.MapRoute) |
| Класс **Microsoft.Phone.Maps.Services.RouteQuery** | Класс [**мапраутефиндер**](https://docs.microsoft.com/uwp/api/Windows.Services.Maps.MapRouteFinder) |
| Получение дохода | |
| Пространство имен **Microsoft.Phone.Marketplace** | Пространство имен [**Windows. ApplicationModel. Store**](https://docs.microsoft.com/uwp/api/Windows.ApplicationModel.Store) |
| Media | |
| Пространство имен **Microsoft.Phone.Media** | Класс [**MediaElement**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.MediaElement) |
| Сеть | |
| (MPNN = **Microsoft.Phone.Net.NetworkInformation**) <br/> Класс **MPNN.DeviceNetworkInformation** | [**Имя узла**](https://docs.microsoft.com/uwp/api/Windows.Networking.HostName), классы [**NetworkInformation**](https://docs.microsoft.com/uwp/api/Windows.Networking.Connectivity.NetworkInformation) |
| (MPNN = **Microsoft.Phone.Net.NetworkInformation**) <br/> Класс **MPNN.NetworkInterface** | Класс [**NetworkInformation**](https://docs.microsoft.com/uwp/api/Windows.Networking.Connectivity.NetworkInformation) |
| (MPNN = **Microsoft.Phone.Net.NetworkInformation**) <br/> Класс **MPNN.NetworkInterfaceInfo** | Класс [**ConnectionProfile**](https://docs.microsoft.com/uwp/api/Windows.Networking.Connectivity.ConnectionProfile) |
| (MPNN = **Microsoft.Phone.Net.NetworkInformation**) <br/> Класс **MPNN.NetworkInterfaceList** | Класс [**NetworkInformation**](https://docs.microsoft.com/uwp/api/Windows.Networking.Connectivity.NetworkInformation) |
| (MPNN = **Microsoft.Phone.Net.NetworkInformation**) <br/> Класс **MPNN.SocketExtensions** | Нет прямого эквивалента |
| (MPNN = **Microsoft.Phone.Net.NetworkInformation**) <br/> Класс **MPNN.WebRequestExtensions** | Нет прямого эквивалента |
| Пространство имен **Microsoft.Phone.Networking.Voip** | Нет прямого эквивалента |
| Класс **System.Net.CookieCollection** | По-прежнему поддерживается, только некоторые свойства отсутствуют (например, IsReadOnly) |
| Класс **System.Net.DownloadProgressChangedEventArgs** и похожие классы, связанные с **System.Net.WebClient** | Класс [**HttpClient**](https://docs.microsoft.com/uwp/api/Windows.Web.Http.HttpClient) (или [System .NET. http. HttpClient](https://docs.microsoft.com/previous-versions/visualstudio/hh193681(v=vs.118))). Производные от [System.Net.Http.StreamContent](https://docs.microsoft.com/previous-versions/visualstudio/hh138119(v=vs.118)) для измерения хода выполнения. |
| Классы **System.Net.DnsEndPoint**, **IPAddress** | Эти классы все еще поддерживаются, но некоторые свойства отсутствуют. Можно также перенести в класс [**HostName**](https://docs.microsoft.com/uwp/api/Windows.Networking.HostName). |
| Класс **System.Net.HttpUtility** | Класс [**хтмлформаселпер**](https://docs.microsoft.com/uwp/api/Windows.ApplicationModel.DataTransfer.HtmlFormatHelper) |
| Класс **System.Net.HttpWebRequest** | Частичная поддержка, при этом рекомендованной перспективной альтернативой является класс [**HttpClient**](https://docs.microsoft.com/uwp/api/Windows.Web.Http.HttpClient) (или [System.Net.Http.HttpClient](https://docs.microsoft.com/previous-versions/visualstudio/hh193681(v=vs.118))). Эти API-интерфейсы используют [System.Net.Http.HttpRequestMessage](https://docs.microsoft.com/previous-versions/visualstudio/hh159020(v=vs.118)) для представления запроса HTTP. |
| Класс **System.Net.HttpWebResponse** | По-прежнему поддерживается, но вместо Close() используйте Dispose(). Рекомендуемой перспективной альтернативой является класс [**HttpClient**](https://docs.microsoft.com/uwp/api/Windows.Web.Http.HttpClient) (или [System.Net.Http.HttpClient](https://docs.microsoft.com/previous-versions/visualstudio/hh193681(v=vs.118))). Эти API используют [System.Net.Http.HttpResponseMessage](https://docs.microsoft.com/dotnet/api/system.net.http.httpresponsemessage) для представления ответа HTTP. |
| (SNN = **System.Net.NetworkInformation**) <br/> Класс **SNN.NetworkChange** | По-прежнему поддерживается за исключением конструктора. |
| Класс **System.Net.OpenReadCompletedEventArgs** и похожие классы, связанные с **System.Net.WebClient** | Класс [**HttpClient**](https://docs.microsoft.com/uwp/api/Windows.Web.Http.HttpClient) (или [System .NET. http. HttpClient](https://docs.microsoft.com/previous-versions/visualstudio/hh193681(v=vs.118))) |
| Класс **System.Net.Sockets.Socket** | По-прежнему поддерживается, но вместо Close() используйте Dispose(). Можно также перенести в класс [**StreamSocket**](https://docs.microsoft.com/uwp/api/Windows.Networking.Sockets.StreamSocket). |
| Класс **System.Net.Sockets.SocketException** | По-прежнему поддерживается, но используйте свойство SocketErrorCode вместо ErrorCode. |
| Классы **System.Net.Sockets.UdpAnySourceMulticastClient**, **UdpSingleSourceMulticastClient** | Класс [**датаграмсоккет**](https://docs.microsoft.com/uwp/api/Windows.Networking.Sockets.DatagramSocket) |
| Класс **System.Net.UploadProgressChangedEventArgs** и похожие классы, связанные с **System.Net.WebClient** | Класс [**HttpClient**](https://docs.microsoft.com/uwp/api/Windows.Web.Http.HttpClient) (или [System .NET. http. HttpClient](https://docs.microsoft.com/previous-versions/visualstudio/hh193681(v=vs.118))) |
| Класс **System.Net.WebClient** | Класс [**HttpClient**](https://docs.microsoft.com/uwp/api/Windows.Web.Http.HttpClient) (или [System .NET. http. HttpClient](https://docs.microsoft.com/previous-versions/visualstudio/hh193681(v=vs.118))) |
| Класс **System.Net.WebRequest** | Частичная поддержка (другой набор свойств), при этом рекомендованная перспективная альтернатива — класс [**HttpClient**](https://docs.microsoft.com/uwp/api/Windows.Web.Http.HttpClient) (или [System.Net.Http.HttpClient](https://docs.microsoft.com/previous-versions/visualstudio/hh193681(v=vs.118))). Эти API-интерфейсы используют [System.Net.Http.HttpRequestMessage](https://docs.microsoft.com/previous-versions/visualstudio/hh159020(v=vs.118)) для представления запроса HTTP. |
| Класс **System.Net.WebResponse** | По-прежнему поддерживается, но вместо Close() используйте Dispose(). Рекомендуемой перспективной альтернативой является класс [**HttpClient**](https://docs.microsoft.com/uwp/api/Windows.Web.Http.HttpClient) (или [System.Net.Http.HttpClient](https://docs.microsoft.com/previous-versions/visualstudio/hh193681(v=vs.118))). Эти API используют [System.Net.Http.HttpResponseMessage](https://docs.microsoft.com/dotnet/api/system.net.http.httpresponsemessage) для представления ответа HTTP. |
| (SN = **System.Net**) <br/> Класс **SN.WriteStreamClosedEventArgs** | Класс [**HttpClient**](https://docs.microsoft.com/uwp/api/Windows.Web.Http.HttpClient) (или [System .NET. http. HttpClient](https://docs.microsoft.com/previous-versions/visualstudio/hh193681(v=vs.118))) |
| (SN = **System.Net**) <br/> Класс **SN.WriteStreamClosedEventHandler** | Класс [**HttpClient**](https://docs.microsoft.com/uwp/api/Windows.Web.Http.HttpClient) (или [System .NET. http. HttpClient](https://docs.microsoft.com/previous-versions/visualstudio/hh193681(v=vs.118))) |
| Класс **System.UriFormatException** | Класс **System.FormatException** |
| Уведомления | |
| Пространство имен MPN = **Microsoft.Phone.Notification** | Пространства имен [**Windows. UI. Notifications**](https://docs.microsoft.com/uwp/api/Windows.UI.Notifications), [**Windows. Networking. пушнотификатионс**](https://docs.microsoft.com/uwp/api/Windows.Networking.PushNotifications) |
| MPN = **Microsoft.Phone.Notification** <br/> Класс **MPN.HttpNotification** | Класс [**тиленотификатион**](https://docs.microsoft.com/uwp/api/Windows.UI.Notifications.TileNotification) |
| MPN = **Microsoft.Phone.Notification** <br/> Класс **MPN.HttpNotificationChannel** | Класс [**пушнотификатиончаннел**](https://docs.microsoft.com/uwp/api/Windows.Networking.PushNotifications.PushNotificationChannel) |
| Программирование | |
| Пространство имен **System** | Пространство имен [**Windows. Foundation**](https://docs.microsoft.com/uwp/api/Windows.Foundation) |
| Классы **System.Diagnostics.StackFrame**, **StackTrace** | Нет прямого эквивалента |
| Пространство имен **System.Diagnostics** | Пространство имен [**Windows. Foundation. Diagnostics**](https://docs.microsoft.com/uwp/api/Windows.Foundation.Diagnostics) |
| Интерфейс **System.ICloneable** | Пользовательский метод, который возвращает соответствующий тип. |
| Класс **System.Reflection.Emit.ILGenerator** | Нет прямого эквивалента |
| Реактивные расширения | |
| Пространство имен **Microsoft.Phone.Reactive** | Нет прямого эквивалента |
| Отражение | |
| Класс **System.Type** | Класс **System.Reflection.TypeInfo** См. раздел [Отражение в .NET Framework для приложений UWP](https://docs.microsoft.com/dotnet/framework/reflection-and-codedom/reflection-for-windows-store-apps). |
| Ресурсы | |
| Класс **System.Resources.ResourceManager** | (WA = **Windows.ApplicationModel**)<br/>[**WA. Resources. Core**](https://docs.microsoft.com/uwp/api/Windows.ApplicationModel.Resources.Core) и [**WA. Пространства имен Resources**](https://docs.microsoft.com/uwp/api/Windows.ApplicationModel.Resources) , класс [**ResourceManager**](https://docs.microsoft.com/uwp/api/Windows.ApplicationModel.Resources.Core.ResourceManager) . См. раздел [Создание и получение ресурсов в приложениях среды выполнения Windows](https://docs.microsoft.com/previous-versions/windows/apps/hh694557(v=vs.140)). |
| Защищенный элемент | |
| (MPS = **Microsoft.Phone.SecureElement**) <br/> Классы **MPS.SecureElementChannel**, **MPS.SecureElementSession** | Класс [**смарткардконнектион**](https://docs.microsoft.com/uwp/api/Windows.Devices.SmartCards.SmartCardConnection) |
| (MPS = **Microsoft.Phone.SecureElement**) <br/> Класс **MPS.SecureElementReader** | Класс [**смарткардреадер**](https://docs.microsoft.com/uwp/api/Windows.Devices.SmartCards.SmartCardReader) |
| Безопасность | |
| (SSC = **System.Security.Cryptography**) <br/> Классы **SSC.Aes**, **SSC.RSA** | Класс [**криптографиценгине**](https://docs.microsoft.com/uwp/api/Windows.Security.Cryptography.Core.CryptographicEngine) |
| (SSC = **System.Security.Cryptography**) <br/> Классы **SSC.HMACSHA256**, **SSC.SHA256** | Класс [**хашалгорисмпровидер**](https://docs.microsoft.com/uwp/api/Windows.Security.Cryptography.Core.HashAlgorithmProvider) |
| (SSC = **System.Security.Cryptography**) <br/> Класс **SSC.ProtectedData** | Класс [**датапротектионпровидер**](https://docs.microsoft.com/uwp/api/Windows.Security.Cryptography.DataProtection.DataProtectionProvider) |
| (SSC = **System.Security.Cryptography**) <br/> Класс **SSC.RandomNumberGenerator** | Класс [**криптографикбуффер**](https://docs.microsoft.com/uwp/api/Windows.Security.Cryptography.CryptographicBuffer) |
| (SSC = **System.Security.Cryptography**) <br/> Класс **SSC.X509Certificates.X509Certificate** | Класс [**цертификатинроллментманажер**](https://docs.microsoft.com/uwp/api/Windows.Security.Cryptography.Certificates.CertificateEnrollmentManager) |
| Shell | |
| (MPSh = **Microsoft.Phone.Shell**) <br/> Класс **MPSh.ApplicationBar** | Класс [**CommandBar**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.CommandBar) |
| (MPSh = **Microsoft.Phone.Shell**) <br/> Класс **MPSh.ApplicationBarIconButton** | Класс [**AppBarButton**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.AppBarButton) (при использовании в свойстве [**примарикоммандс**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.commandbar.primarycommands) ) |
| (MPSh = **Microsoft.Phone.Shell**) <br/> Класс **MPSh.ApplicationBarMenuItem** | Класс [**AppBarButton**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.AppBarButton) (при использовании в свойстве [**секондарикоммандс**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.commandbar.secondarycommands) ) |
| (MPSh = **Microsoft.Phone.Shell**) <br/> Классы **MPSh.CycleTileData**, **MPSh.FlipTileData**, **MPSh.IconicTileData**, **MPSh.ShellTileData**, **MPSh.StandardTileData** | Класс [**тилетемплатетипе**](https://docs.microsoft.com/uwp/api/Windows.UI.Notifications.TileTemplateType) |
| (MPSh = **Microsoft.Phone.Shell**) <br/> Класс **MPSh.PhoneApplicationService** | [**CoreApplication**](https://docs.microsoft.com/uwp/api/Windows.ApplicationModel.Core.CoreApplication), классы [**дисплайрекуест**](https://docs.microsoft.com/uwp/api/Windows.System.Display.DisplayRequest) |
| (MPSh = **Microsoft.Phone.Shell**) <br/> Класс **MPSh.ProgressIndicator** | Класс [**статусбарпрогрессиндикатор**](https://docs.microsoft.com/uwp/api/Windows.UI.ViewManagement.StatusBarProgressIndicator) |
| (MPSh = **Microsoft.Phone.Shell**) <br/> Класс **MPSh.ShellTile** | Класс [**секондаритиле**](https://docs.microsoft.com/uwp/api/Windows.UI.StartScreen.SecondaryTile) |
| (MPSh = **Microsoft.Phone.Shell**) <br/> Класс **MPSh.ShellTileSchedule** | Класс [**тилеупдатер**](https://docs.microsoft.com/uwp/api/Windows.UI.Notifications.TileUpdater) |
| (MPSh = **Microsoft.Phone.Shell**) <br/> Класс **MPSh.ShellToast** | Класс [**тоастнотификатионманажер**](https://docs.microsoft.com/uwp/api/Windows.UI.Notifications.ToastNotificationManager) |
| (MPSh = **Microsoft.Phone.Shell**) <br/> Класс **MPSh.SystemTray** | Класс [**StatusBar**](https://docs.microsoft.com/uwp/api/Windows.UI.ViewManagement.StatusBar) |
| Хранилище и ввод-вывод | |
| Классы **Microsoft.Phone.Storage.ExternalStorage**, **ExternalStorageDevice**, **ExternalStorageFile**, **ExternalStorageFolder** | Класс [**кновнфолдерс**](https://docs.microsoft.com/uwp/api/Windows.Storage.KnownFolders) |
| Пространство имен **System.IO** | Пространства имен [**Windows. Storage**](https://docs.microsoft.com/uwp/api/Windows.Storage), [**Windows. Storage. Streams**](https://docs.microsoft.com/uwp/api/Windows.Storage.Streams) |
| Класс **System.IO.Directory** | Класс [**сторажефолдер**](https://docs.microsoft.com/uwp/api/Windows.Storage.StorageFolder) |
| Класс **System.IO.File** | Классы [**StorageFile**](https://docs.microsoft.com/uwp/api/Windows.Storage.StorageFile) и [**пасио**](https://docs.microsoft.com/uwp/api/Windows.Storage.PathIO)
| (SII = **System.IO.IsolatedStorage**) <br/> Класс **SII.IsolatedStorageFile** |[**ApplicationData. локалфолдер,** ](https://docs.microsoft.com/uwp/api/windows.storage.applicationdata.localfolder) свойство |
| (SII = **System.IO.IsolatedStorage**) <br/> Класс **SII.IsolatedStorageSettings** | [**ApplicationData. LocalSettings,** ](https://docs.microsoft.com/uwp/api/windows.storage.applicationdata.localsettings) свойство |
| Класс **System.IO.Stream** | По-прежнему поддерживается, используйте ReadAsync() и WriteAsync() вместо BeginRead()/EndRead() и BeginWrite()/EndWrite(). |
| Кошелек | |
| Пространство имен **Microsoft.Phone.Wallet** | Пространство имен [**Windows. ApplicationModel. бумажник**](https://docs.microsoft.com/uwp/api/Windows.ApplicationModel.Wallet) |
| Xml | |
| (SX = **System.Xml**) | Метод **SX.XmlConvert.ToDateTime** |
| (SX = **System.Xml**) | Метод **SX.XmlConvert.ToDateTimeOffset** |


Следующий раздел называется [Перенос проекта](wpsl-to-uwp-porting-to-a-uwp-project.md).


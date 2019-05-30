---
description: Код, интегрирующийся с устройством и его датчиками, включает ввод от пользователя и вывод к нему.
title: Перенос для модели ввода-вывода, устройств и приложений Windows Phone Silverlight для универсальной платформы Windows "
ms.assetid: bf9f2c03-12c1-49e4-934b-e3fa98919c53
ms.date: 02/08/2017
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: a62fcb4a208a52fd77be2a9913e265b12bf31f43
ms.sourcegitcommit: ac7f3422f8d83618f9b6b5615a37f8e5c115b3c4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/29/2019
ms.locfileid: "66372187"
---
#  <a name="porting-windowsphone-silverlight-to-uwp-for-io-device-and-app-model"></a>Перенос для ввода-вывода, устройства и модель приложений Windows Phone Silverlight для универсальной платформы Windows


Предыдущий раздел назывался [Перенос XAML и пользовательского интерфейса](wpsl-to-uwp-porting-xaml-and-ui.md).

Код, интегрирующийся с устройством и его датчиками, включает ввод от пользователя и вывод к нему. Он может также включать обработку данных. Но в целом этот код не считается уровнем пользовательского интерфейса или уровнем данных. Этот код включает интеграцию с контроллером вибрации, акселерометром, гироскопом, микрофоном и динамиками (которые могут пересекаться с распознаванием речи и синтезом), (географическим) положением и модальностями ввода, такими как сенсорный ввод, мышь, клавиатура и перо.

## <a name="application-lifecycle-process-lifetime-management"></a>Жизненный цикл приложения (управление жизненным циклом процесса)

Приложение Windows Phone Silverlight содержит код для сохранения и восстановления состояния приложения и его состоянии представления для поддержки захоронения и впоследствии повторной активации. Жизненный цикл приложения приложений универсальной платформы Windows (UWP) имеет надежный parallels, приложения Windows Phone Silverlight, поскольку они оба предназначены с одной и той же цели которого — максимально увеличить ресурсы, доступные для любого приложения пользователь выбрал в передний план в любой момент. Вы увидите, что код достаточно легко адаптируется к новой системе.

**Примечание**    нажав оборудования **обратно** кнопку автоматически завершает приложение Windows Phone Silverlight. Нажатие аппаратной кнопки **Назад** на мобильном устройстве *не* завершает автоматически работу приложения UWP. Вместо этого оно будет приостановлено, а затем его можно будет закрыть. Но эти детали понятны для приложения, которое соответствующим образом отвечает на события жизненного цикла приложения.

Окно между моментом — это период между временем, когда приложение становится неактивным, и вызовом системного события приостановки. Для приложения UWP отсутствует окно между моментом; событие приостановки возникает, когда приложение становится неактивным.

Подробнее см. в разделе [Жизненный цикл приложения](https://docs.microsoft.com/windows/uwp/launch-resume/app-lifecycle).

## <a name="camera"></a>Камера

Windows Phone Silverlight камеры записи код использует **Microsoft.Devices.Camera**, **Microsoft.Devices.PhotoCamera**, или **Microsoft.Phone.Tasks.CameraCaptureTask**классы. Чтобы перенести этот код в UWP, можно использовать класс [**MediaCapture**](https://docs.microsoft.com/uwp/api/Windows.Media.Capture.MediaCapture). Пример кода приведен в разделе [**CapturePhotoToStorageFileAsync**](https://docs.microsoft.com/uwp/api/windows.media.capture.mediacapture.capturephototostoragefileasync). Этот метод позволяет записать фотографию в файл хранилища, и он требует, чтобы  [**возможности устройства**](https://docs.microsoft.com/uwp/schemas/appxpackage/uapmanifestschema/element-devicecapability)**Микрофон** и **Веб-камера** были установлены в манифесте пакета приложения.

Другой вариант — класс [**CameraCaptureUI**](https://docs.microsoft.com/uwp/api/Windows.Media.Capture.CameraCaptureUI), который также требует установки  [**возможностей устройства**](https://docs.microsoft.com/uwp/schemas/appxpackage/uapmanifestschema/element-devicecapability)**Микрофон** и **Веб-камера**.

Приложения-объективы не поддерживаются для приложений UWP.

## <a name="detecting-the-platform-your-app-is-running-on"></a>Определение платформы, на которой работает приложение

Способ мышления об изменениях для различных версий приложений с Windows 10. Новая модель подразумевает, что приложение предназначено для универсальной платформы Windows (UWP) и работает на всех устройствах Windows. Оно может активировать возможности, поддерживаемые только определенным семейством устройств. При необходимости приложение также может быть предназначено только для одного или нескольких семейств устройств. Подробнее о семействах устройств и выборе нужного семейства устройств см. в [Руководстве по приложениям UWP](https://docs.microsoft.com/windows/uwp/get-started/universal-application-platform-guide).

**Примечание**    мы рекомендуем не использовать операционной системы или семейства устройств для определения наличия функций. Определение текущей операционной системы или семейства устройств — обычно не лучший способ определить наличие указанной функции ОС или семейства устройств. Вместо обнаружения ОС или семейства устройств (и номера версии), проверьте наличие самой функции (см. раздел [Условная компиляция и адаптивный код](wpsl-to-uwp-porting-to-a-uwp-project.md)). Если вам необходимо требовать использование определенной операционной системы или семейства устройств, убедитесь, что определяется минимально поддерживаемая версия, а не проверяйте наличие именно этой версии.

Чтобы адаптировать пользовательский интерфейс приложения к различным устройствам, мы рекомендуем использовать несколько методов. Продолжайте использовать элементы с автоматически устанавливаемым размером и динамические панели макета, как и раньше. В разметке XAML продолжайте использовать размеры в эффективных пикселях (ранее — пиксели просмотра), чтобы пользовательский интерфейс адаптировался к различным разрешениям и коэффициентам масштабирования (см. раздел [Просмотр/эффективные пиксели, расстояние от экрана и коэффициенты масштабирования](wpsl-to-uwp-porting-xaml-and-ui.md)). Кроме того, используйте адаптивные триггеры и параметры диспетчера визуальных состояний для адаптации пользовательского интерфейса к размеру окна (см. [Руководство по приложениям UWP](https://docs.microsoft.com/windows/uwp/get-started/universal-application-platform-guide)).

Однако если вы используете сценарий, где обнаружение семейства устройства неизбежно, вы можете сделать это. В этом примере мы используем класс [**AnalyticsVersionInfo**](https://docs.microsoft.com/uwp/api/Windows.System.Profile.AnalyticsVersionInfo) для перехода на страницу, предназначенную для семейства мобильных устройств, если это возможно, а в противном случае возвращаемся на страницу по умолчанию.

```csharp
   if (Windows.System.Profile.AnalyticsInfo.VersionInfo.DeviceFamily == "Windows.Mobile")
        rootFrame.Navigate(typeof(MainPageMobile), e.Arguments);
    else
        rootFrame.Navigate(typeof(MainPage), e.Arguments);
```

Приложение может также определить семейство устройств, на котором оно работает, на основе применяемых факторов выбора ресурсов. В следующем примере показано, как сделать это императивно, а в разделе [**ResourceContext.QualifierValues**](https://docs.microsoft.com/uwp/api/windows.applicationmodel.resources.core.resourcecontext.qualifiervalues) описан более типичный вариант использования класса для загрузки ресурсов для определенного семейства устройств на основе фактора семейства устройств.

```csharp
var qualifiers = Windows.ApplicationModel.Resources.Core.ResourceContext.GetForCurrentView().QualifierValues;
string deviceFamilyName;
bool isDeviceFamilyNameKnown = qualifiers.TryGetValue("DeviceFamily", out deviceFamilyName);
```

См. также раздел [Условная компиляция и адаптивный код](wpsl-to-uwp-porting-to-a-uwp-project.md).

## <a name="device-status"></a>Состояние устройства

Можно использовать в приложении Windows Phone Silverlight **Microsoft.Phone.Info.DeviceStatus** класс для получения сведений об устройстве, на котором выполняется приложение. Если нет прямого эквивалента UWP пространства имен **Microsoft.Phone.Info**, ниже указаны некоторые свойства и события, которые можно использовать в приложении UWP вместо вызовов к членам класса **DeviceStatus**.

| Windows Phone Silverlight                                                               | UWP                                                                                                                                                                                                                                                                                                                                |
|-----------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Свойства **ApplicationCurrentMemoryUsage** и **ApplicationCurrentMemoryUsageLimit** | [**MemoryManager.AppMemoryUsage** ](https://docs.microsoft.com/uwp/api/windows.system.memorymanager.appmemoryusage) и [ **AppMemoryUsageLimit** ](https://docs.microsoft.com/uwp/api/windows.system.memorymanager.appmemoryusagelimit) свойства                                                                                                                                    |
| Свойство **ApplicationPeakMemoryUsage**                                                 | Используйте инструменты профилирования памяти в Visual Studio. Подробнее см. в разделе [Анализ использования памяти](https://docs.microsoft.com/visualstudio/welcome-to-visual-studio-2015?view=vs-2015).                                                                                                                                                                          |
| Свойство **DeviceFirmwareVersion**                                                      | [**EasClientDeviceInformation.SystemFirmwareVersion** ](https://docs.microsoft.com/uwp/api/windows.security.exchangeactivesyncprovisioning.easclientdeviceinformation.systemfirmwareversion) свойства (только для семейства настольных устройств)                                                                                                                                                                             |
| Свойство **DeviceHardwareVersion**                                                      | [**EasClientDeviceInformation.SystemHardwareVersion** ](https://docs.microsoft.com/uwp/api/windows.security.exchangeactivesyncprovisioning.easclientdeviceinformation.systemhardwareversion) свойства (только для семейства настольных устройств)                                                                                                                                                                             |
| Свойство **DeviceManufacturer**                                                         | [**EasClientDeviceInformation.SystemManufacturer** ](https://docs.microsoft.com/uwp/api/windows.security.exchangeactivesyncprovisioning.easclientdeviceinformation.systemmanufacturer) свойства (только для семейства настольных устройств)                                                                                                                                                                                |
| Свойство **DeviceName**                                                                 | [**EasClientDeviceInformation.SystemProductName** ](https://docs.microsoft.com/uwp/api/windows.security.exchangeactivesyncprovisioning.easclientdeviceinformation.systemproductname) свойства (только для семейства настольных устройств)                                                                                                                                                                                 |
| Свойство **DeviceTotalMemory**                                                          | Нет эквивалента                                                                                                                                                                                                                                                                                                                      |
| Свойство **IsKeyboardDeployed**                                                         | Нет эквивалента. Это свойство содержит сведения об аппаратных клавиатурах для мобильных устройств, которые обычно не используются.                                                                                                                                                                                                        |
| Свойство **IsKeyboardPresent**                                                          | Нет эквивалента. Это свойство содержит сведения об аппаратных клавиатурах для мобильных устройств, которые обычно не используются.                                                                                                                                                                                                        |
| Событие **KeyboardDeployedChanged**                                                       | Нет эквивалента. Это свойство содержит сведения об аппаратных клавиатурах для мобильных устройств, которые обычно не используются.                                                                                                                                                                                                        |
| Свойство **PowerSource**                                                                | Нет эквивалента                                                                                                                                                                                                                                                                                                                      |
| Событие **PowerSourceChanged**                                                            | Обрабатывайте событие [**RemainingChargePercentChanged**](https://docs.microsoft.com/uwp/api/windows.phone.devices.power.battery.remainingchargepercentchanged) (только семейство мобильных устройств). Событие возникает, когда значение свойства [**RemainingChargePercent**](https://docs.microsoft.com/uwp/api/windows.phone.devices.power.battery.remainingchargepercent) (только для семейства мобильных устройств) сокращается на 1%. |

## <a name="location"></a>Location

Когда приложение, которое объявляет возможности расположения в его пакет манифеста выполнении приложений в Windows 10, система попросит согласия конечного пользователя. Поэтому, если ваше приложение отображает собственный настраиваемый запрос на продолжение или если оно предоставляет переключатель включения и выключения, следует удалить этот запрос, чтобы пользователь получал только один запрос.

## <a name="orientation"></a>Orientation

Эквивалентом для приложений UWP свойств **PhoneApplicationPage.SupportedOrientations** и **Orientation** является элемент [**uap:InitialRotationPreference**](https://docs.microsoft.com/uwp/schemas/appxpackage/uapmanifestschema/element-uap-splashscreen) в манифесте пакета приложений. Выберите вкладку **Приложение**, если она еще не выбрана, и установите один или несколько флажков в разделе **Поддерживаемые ориентации** для регистрации своих предпочтений.

Рекомендуется разрабатывать такой пользовательский интерфейс приложения UWP, который будет иметь превосходный вид в любой ориентации и при любом размере экрана. Подробнее об этом см. в разделе [Перенос для форм-факторов и взаимодействия с пользователем](wpsl-to-uwp-form-factors-and-ux.md), который следует за следующим разделом.

Следующий раздел посвящен [переносу уровней бизнеса и данных](wpsl-to-uwp-business-and-data.md).


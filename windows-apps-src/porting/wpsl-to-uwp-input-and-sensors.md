---
description: Код, интегрирующийся с устройством и его датчиками, включает ввод от пользователя и вывод к нему.
title: Перенос Windows Phone Silverlight в UWP для операций ввода-вывода, устройства и модели приложений
ms.assetid: bf9f2c03-12c1-49e4-934b-e3fa98919c53
ms.date: 02/08/2017
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: a62fcb4a208a52fd77be2a9913e265b12bf31f43
ms.sourcegitcommit: 0426013dc04ada3894dd41ea51ed646f9bb17f6d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2020
ms.locfileid: "78853149"
---
#  <a name="porting-windowsphone-silverlight-to-uwp-for-io-device-and-app-model"></a>Перенос Windows Phone Silverlight в UWP для операций ввода-вывода, устройства и модели приложений


Предыдущий раздел назывался [Перенос XAML и пользовательского интерфейса](wpsl-to-uwp-porting-xaml-and-ui.md).

Код, интегрирующийся с устройством и его датчиками, включает ввод от пользователя и вывод к нему. Он может также включать обработку данных. Но в целом этот код не считается уровнем пользовательского интерфейса или уровнем данных. Этот код включает интеграцию с контроллером вибрации, акселерометром, гироскопом, микрофоном и динамиками (которые могут пересекаться с распознаванием речи и синтезом), (географическим) положением и модальностями ввода, такими как сенсорный ввод, мышь, клавиатура и перо.

## <a name="application-lifecycle-process-lifetime-management"></a>Жизненный цикл приложения (управление жизненным циклом процесса)

Приложение Windows Phone Silverlight содержит код для сохранения и восстановления состояния приложения и его состояния просмотра, чтобы поддерживать захоронение и последующее повторное активирование. Жизненный цикл приложения для приложений универсальная платформа Windows (UWP) имеет строгие возможности для Windows Phone приложений Silverlight, так как они разработаны с той же целью максимизации ресурсов, доступных для любого приложения, которое пользователь выбрал в передний план в любой момент. Вы увидите, что код достаточно легко адаптируется к новой системе.

**Примечание** .   нажатии аппаратной кнопки **Back** автоматически завершает приложение Windows Phone Silverlight. Нажатие аппаратной кнопки **Назад** на мобильном устройстве *не* завершает автоматически работу приложения UWP. Вместо этого оно будет приостановлено, а затем его можно будет закрыть. Но эти детали понятны для приложения, которое соответствующим образом отвечает на события жизненного цикла приложения.

Окно между моментом — это период между временем, когда приложение становится неактивным, и вызовом системного события приостановки. Для приложения UWP отсутствует окно между моментом; событие приостановки возникает, когда приложение становится неактивным.

Подробнее см. в разделе [Жизненный цикл приложения](https://docs.microsoft.com/windows/uwp/launch-resume/app-lifecycle).

## <a name="camera"></a>Камера

В Windows Phone коде для записи камеры Silverlight используются классы **Microsoft. Devices. Camera**, **Microsoft. Devices.** Camera или **Microsoft. Phone. Tasks. CameraCaptureTask** . Чтобы перенести этот код в UWP, можно использовать класс [**MediaCapture**](https://docs.microsoft.com/uwp/api/Windows.Media.Capture.MediaCapture). Пример кода приведен в разделе [**CapturePhotoToStorageFileAsync**](https://docs.microsoft.com/uwp/api/windows.media.capture.mediacapture.capturephototostoragefileasync). Этот метод позволяет записать фотографию в файл хранилища, и он требует, чтобывозможности устройства [Микрофон**и**Веб-камера](https://docs.microsoft.com/uwp/schemas/appxpackage/uapmanifestschema/element-devicecapability) были установлены в манифесте пакета приложения.

Другой вариант — класс [**CameraCaptureUI**](https://docs.microsoft.com/uwp/api/Windows.Media.Capture.CameraCaptureUI), который также требует установкивозможностей устройства [Микрофон**и**Веб-камера](https://docs.microsoft.com/uwp/schemas/appxpackage/uapmanifestschema/element-devicecapability).

Приложения-объективы не поддерживаются для приложений UWP.

## <a name="detecting-the-platform-your-app-is-running-on"></a>Определение платформы, на которой работает приложение

Способ думать об изменениях в разработке приложений с Windows 10. Новая модель подразумевает, что приложение предназначено для универсальной платформы Windows (UWP) и работает на всех устройствах Windows. Оно может активировать возможности, поддерживаемые только определенным семейством устройств. При необходимости приложение также может быть предназначено только для одного или нескольких семейств устройств. Подробнее о семействах устройств и выборе нужного семейства устройств см. в [Руководстве по приложениям UWP](https://docs.microsoft.com/windows/uwp/get-started/universal-application-platform-guide).

**Обратите внимание** ,   не рекомендуется использовать операционную систему или семейство устройств для обнаружения наличия компонентов. Определение текущей операционной системы или семейства устройств — обычно не лучший способ определить наличие указанной функции ОС или семейства устройств. Вместо обнаружения ОС или семейства устройств (и номера версии), проверьте наличие самой функции (см. раздел [Условная компиляция и адаптивный код](wpsl-to-uwp-porting-to-a-uwp-project.md)). Если вам необходимо требовать использование определенной операционной системы или семейства устройств, убедитесь, что определяется минимально поддерживаемая версия, а не проверяйте наличие именно этой версии.

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

Приложение Windows Phone Silverlight может использовать класс **Microsoft. Phone. info. девицестатус** для получения сведений об устройстве, на котором работает приложение. Если нет прямого эквивалента UWP пространства имен **Microsoft.Phone.Info**, ниже указаны некоторые свойства и события, которые можно использовать в приложении UWP вместо вызовов к членам класса **DeviceStatus**.

| Windows Phone Silverlight                                                               | UWP                                                                                                                                                                                                                                                                                                                                |
|-----------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Свойства **ApplicationCurrentMemoryUsage** и **ApplicationCurrentMemoryUsageLimit** | Свойства [**мемориманажер. аппмеморюсаже**](https://docs.microsoft.com/uwp/api/windows.system.memorymanager.appmemoryusage) и [**аппмеморюсажелимит**](https://docs.microsoft.com/uwp/api/windows.system.memorymanager.appmemoryusagelimit)                                                                                                                                    |
| Свойство **ApplicationPeakMemoryUsage**                                                 | Используйте инструменты профилирования памяти в Visual Studio. Подробнее см. в разделе [Анализ использования памяти](https://docs.microsoft.com/visualstudio/welcome-to-visual-studio-2015?view=vs-2015).                                                                                                                                                                          |
| Свойство **DeviceFirmwareVersion**                                                      | Свойство [**еасклиентдевицеинформатион. системфирмвареверсион**](https://docs.microsoft.com/uwp/api/windows.security.exchangeactivesyncprovisioning.easclientdeviceinformation.systemfirmwareversion) (только семейство устройств для настольных компьютеров)                                                                                                                                                                             |
| Свойство **DeviceHardwareVersion**                                                      | Свойство [**еасклиентдевицеинформатион. системхардвареверсион**](https://docs.microsoft.com/uwp/api/windows.security.exchangeactivesyncprovisioning.easclientdeviceinformation.systemhardwareversion) (только семейство устройств для настольных компьютеров)                                                                                                                                                                             |
| Свойство **DeviceManufacturer**                                                         | Свойство [**еасклиентдевицеинформатион. системмануфактурер**](https://docs.microsoft.com/uwp/api/windows.security.exchangeactivesyncprovisioning.easclientdeviceinformation.systemmanufacturer) (только семейство устройств для настольных компьютеров)                                                                                                                                                                                |
| Свойство **DeviceName**                                                                 | Свойство [**еасклиентдевицеинформатион. системпродуктнаме**](https://docs.microsoft.com/uwp/api/windows.security.exchangeactivesyncprovisioning.easclientdeviceinformation.systemproductname) (только семейство устройств для настольных компьютеров)                                                                                                                                                                                 |
| Свойство **DeviceTotalMemory**                                                          | Нет эквивалента                                                                                                                                                                                                                                                                                                                      |
| Свойство **IsKeyboardDeployed**                                                         | Нет эквивалента. Это свойство содержит сведения об аппаратных клавиатурах для мобильных устройств, которые обычно не используются.                                                                                                                                                                                                        |
| Свойство **IsKeyboardPresent**                                                          | Нет эквивалента. Это свойство содержит сведения об аппаратных клавиатурах для мобильных устройств, которые обычно не используются.                                                                                                                                                                                                        |
| Событие **KeyboardDeployedChanged**                                                       | Нет эквивалента. Это свойство содержит сведения об аппаратных клавиатурах для мобильных устройств, которые обычно не используются.                                                                                                                                                                                                        |
| Свойство **PowerSource**                                                                | Нет эквивалента                                                                                                                                                                                                                                                                                                                      |
| Событие **PowerSourceChanged**                                                            | Обрабатывайте событие [**RemainingChargePercentChanged**](https://docs.microsoft.com/uwp/api/windows.phone.devices.power.battery.remainingchargepercentchanged) (только семейство мобильных устройств). Событие возникает, когда значение свойства [**RemainingChargePercent**](https://docs.microsoft.com/uwp/api/windows.phone.devices.power.battery.remainingchargepercent) (только для семейства мобильных устройств) сокращается на 1%. |

## <a name="location"></a>Расположение

Когда приложение, которое объявляет возможность расположения в манифесте пакета приложения, выполняется в Windows 10, система предложит пользователю предоставить согласие. Поэтому, если ваше приложение отображает собственный настраиваемый запрос на продолжение или если оно предоставляет переключатель включения и выключения, следует удалить этот запрос, чтобы пользователь получал только один запрос.

## <a name="orientation"></a>Ориентация

Эквивалентом для приложений UWP свойств **PhoneApplicationPage.SupportedOrientations** и **Orientation** является элемент [**uap:InitialRotationPreference**](https://docs.microsoft.com/uwp/schemas/appxpackage/uapmanifestschema/element-uap-splashscreen) в манифесте пакета приложений. Выберите вкладку **Приложение**, если она еще не выбрана, и установите один или несколько флажков в разделе **Поддерживаемые ориентации** для регистрации своих предпочтений.

Рекомендуется разрабатывать такой пользовательский интерфейс приложения UWP, который будет иметь превосходный вид в любой ориентации и при любом размере экрана. Подробнее об этом см. в разделе [Перенос для форм-факторов и взаимодействия с пользователем](wpsl-to-uwp-form-factors-and-ux.md), который следует за следующим разделом.

Следующий раздел посвящен [переносу уровней бизнеса и данных](wpsl-to-uwp-business-and-data.md).


---
title: Добавление в приложение функций демонстрации розничной торговли (RDX)
description: Подготовьте приложение к демонстрационному режиму розничной торговли, помогая продемонстрировать ваше приложение в розничной продаже.
ms.assetid: f83f950f-7fdd-4f18-8127-b92a8f400061
ms.date: 10/02/2018
ms.topic: article
keywords: windows 10, uwp, приложение для демонстрации в розничных магазинах
ms.localizationpriority: medium
ms.openlocfilehash: 5be39760ee2b8837cfb9b0809a354262e790970b
ms.sourcegitcommit: 5dfa98a80eee41d97880dba712673168070c4ec8
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/29/2019
ms.locfileid: "73051994"
---
# <a name="add-retail-demo-rdx-features-to-your-app"></a>Добавление в приложение функций демонстрации розничной торговли (RDX)

Включите режим демонстрации в розничных магазинах в свое приложение для Windows, чтобы клиенты, выбирая компьютеры и устройства в торговом зале, могли сразу же использовать ваше предложение.

Когда клиенты находятся в розничном магазине, они хотят испытать демонстрации ПК и устройств. Они часто тратят много времени на работу с приложениями с помощью демонстрационной версии для [розничной торговли (RDX)](https://docs.microsoft.com/windows-hardware/customize/desktop/retail-demo-experience).

Вы можете настроить приложение для предоставления различных возможностей в _обычных_ и _розничных_ режимах. Например, если приложение запускается с помощью процесса установки, вы можете пропустить его в розничном режиме и предварительно заполнить приложение образцами данных и параметрами по умолчанию, чтобы они могли перейти сразу же к.

С точки зрения клиентов существует только одно приложение. Чтобы помочь клиентам отличать два режима, рекомендуется, чтобы приложение в режиме розничной торговли отображалось слово «розница», выразительное в заголовке окна или в подходящем расположении.

Помимо Microsoft Store требований для приложений, приложения, поддерживающие RDX, также должны быть совместимы с процессами установки, очистки и обновления RDX, чтобы обеспечить единообразную работу клиентов в розничном магазине.

## <a name="design-principles"></a>Принципы проектирования

* **Покажите лучшее**. С помощью демонстрационного примера розничной торговли можно продемонстрировать, почему ваше приложение Rocks. Это, скорее всего, первый раз, когда ваш клиент увидит ваше приложение, поэтому покажите им лучшее.

* **Покажите его быстро**. Покупатель может быть нетерпелив, поэтому чем быстрее он ощутит преимущества вашего приложения, тем лучше.

* **Старайтесь**не усложнять историю. Демонстрационная версия розничной торговли — это Лифт для ценности вашего приложения.

* **Сосредоточьтесь на опыте**. Дайте покупателю время «переварить» то, что он видит в вашем приложении. Хотя ваша задача — как можно быстрее добраться до самого интересного в вашем приложении, возможно, имеет смысл предусмотреть логические паузы.

## <a name="technical-requirements"></a>Технические требования

Так как приложения, поддерживающие RDX, предназначены для того, чтобы продемонстрировать лучшие преимущества вашего приложения розничным клиентам, они должны удовлетворять техническим требованиям и соблюдать нормы конфиденциальности, которые Microsoft Store для всех приложений для демонстрации розничной торговли.

Его можно использовать в качестве контрольного списка, который поможет подготовиться к процессу проверки и обеспечить ясность процесса тестирования. Обратите внимание, что приложение должно соответствовать этим требованиям не только при прохождении проверки, но и в течение всего жизненного цикла RDX-приложения, т. е. всего времени его использования на демонстрационных устройствах в розничных магазинах.

### <a name="critical-requirements"></a>Критические требования

Приложения, поддерживающие RDX, которые не соответствуют этим критическим требованиям, будут удалены из всех демонстрационных устройств розничной торговли как можно скорее.

* **Не задавайте личные сведения (PII)** . Сюда входят сведения для входа, сведения о учетная запись Майкрософт или контактные данные.

* **Без ошибок**. Ваше приложение должно работать без ошибок. Кроме того, на демонстрационных устройствах не должны выводиться никакие всплывающие или иные уведомления об ошибках. Ошибки отражены в самом приложении, торговой марке, торговой марке устройства, торговой марке устройства мануфактурер'с и торговой марке корпорации Майкрософт.

* **Платные приложения должны иметь пробный режим**. Ваше приложение либо должно быть бесплатным, либо включать [пробный режим](https://docs.microsoft.com/windows/uwp/monetize/exclude-or-limit-features-in-a-trial-version-of-your-app). Покупатели вряд ли захотят оплачивать работу с приложением в магазине.

### <a name="high-priority-requirements"></a>Требования с высоким приоритетом

Приложения, поддерживающие RDX, которые не соответствуют этим требованиям с высоким приоритетом, необходимо исследовать немедленно. Если сразу же исправить приложение не удается, возможно, оно будет удалено со всех демонстрационных устройств для розничных магазинов.

* **Автономная работа с запоминающими возможностями**. Приложение должно продемонстрировать отличную работу в автономном режиме, так как около 50% устройств находятся вне сети в розничных расположениях. Так покупатели, взаимодействующие с вашим приложением, все равно смогут получить положительные впечатления от работы с ним.

* **Обновлен интерфейс содержимого**. Приложение никогда не должно запрашивать обновления в сети. Если требуются обновления, их следует выполнять автоматически.

* **Анонимная связь отсутствует**. Так как клиент, использующий демонстрационное устройство розничной торговли, является анонимным пользователем, он не должен иметь возможности отправлять сообщения или обмениваться содержимым с устройства.

* **Используйте процесс очистки, чтобы обеспечить единообразное взаимодействие**. Состояние устройства и приложений на нем должно быть одинаковым для всех покупателей, подходящих к устройству. Приложение должно использовать [процесс очистки](#cleanup-process) , чтобы вернуться к тому же состоянию по умолчанию после каждого использования. Мы не хотим, чтобы следующий клиент не был видеть, чем последний пользователь вышел за. Такими результатами могут быть набранные очки, достигнутые уровни и разблокировки.

* **Возрастает соответствующее содержимое**. Всем содержимому приложения должна быть назначена категория Тин или более низкого рейтинга. Дополнительные сведения см. в статье [Получение оценки приложения по иарк](https://www.globalratings.com/for-developers.aspx) и [есрб](https://www.esrb.org/ratings/ratings_guide.aspx).

### <a name="medium-priority-requirements"></a>Требования среднего приоритета

Если приложение не соответствует этим требованиям, сотрудники розничного магазина Windows могут связаться с разработчиком напрямую, чтобы обсудить устранения возможные способы устранения проблемы.

* **Возможность успешного выполнения через ряд устройств**. Приложения должны быть хорошо запущены на всех устройствах, включая устройства с низкими характеристиками. Если приложение установлено на устройствах, которые не соответствуют минимальным спецификациям, приложение должно ясно информировать пользователя об этом. Должны быть известны минимальные требования к устройствам, чтобы приложение всегда могло работать с должной производительностью.

* **Соответствие требованиям к размеру приложения розничного магазина**. Размер приложения не должен превышать 800 МБ. Если приложение, поддерживающее RDX, не соответствует требованиям к размеру, обратитесь к группе Windows Retail Store напрямую.

## <a name="retailinfo-api-preparing-your-code-for-demo-mode"></a>API Ретаилинфо: подготовка кода для демонстрационного режима

### <a name="isdemomodeenabled"></a>исдемомодинаблед
Свойство [**исдемомодинаблед**](https://docs.microsoft.com/uwp/api/windows.system.profile.retailinfo.isdemomodeenabled) в классе служебной программы [**ретаилинфо**](https://docs.microsoft.com/uwp/api/Windows.System.Profile.RetailInfo) , которое является частью пространства имен [Windows. System. Profile](https://docs.microsoft.com/uwp/api/windows.system.profile) в пакете SDK для Windows 10, используется в качестве логического индикатора для указания того, какой путь кода ваше приложение выполняется в _нормальном режиме._ или в режиме _розничной торговли_ .

``` csharp
using Windows.Storage;

StorageFolder folder = ApplicationData.Current.LocalFolder;

if (Windows.System.Profile.RetailInfo.IsDemoModeEnabled) 
{
    // Use the demo specific directory
    folder = await folder.GetFolderAsync("demo");
}

StorageFile file = await folder.GetFileAsync("hello.txt");
// Now read from file
```

``` cpp
using namespace Windows::Storage;

StorageFolder^ localFolder = ApplicationData::Current->LocalFolder;

if (Windows::System::Profile::RetailInfo::IsDemoModeEnabled) 
{
    // Use the demo specific directory
    create_task(localFolder->GetFolderAsync("demo").then([this](StorageFolder^ demoFolder)
    {
        return demoFolder->GetFileAsync("hello.txt");
    }).then([this](task<StorageFile^> fileTask)
    {
        StorageFile^ file = fileTask.get();
    });
    // Do something with file
}
else
{
    create_task(localFolder->GetFileAsync("hello.txt").then([this](StorageFile^ file)
    {
        // Do something with file
    });
}
```

``` javascript
if (Windows.System.Profile.retailInfo.isDemoModeEnabled) {
    console.log("Retail mode is enabled.");
} else {
    Console.log("Retail mode is not enabled.");
}
```

### <a name="retailinfoproperties"></a>Ретаилинфо. Properties

Когда свойство [**IsDemoModeEnabled**](https://docs.microsoft.com/uwp/api/windows.system.profile.retailinfo.isdemomodeenabled) возвращает значение true, можно запросить с помощью свойства [**RetailInfo.Properties**](https://docs.microsoft.com/uwp/api/windows.system.profile.retailinfo.properties) набор свойств демонстрационного устройства для настройки приложения в соответствии с его характеристиками. К таким свойствам относятся [**ManufacturerName**](https://docs.microsoft.com/uwp/api/windows.system.profile.knownretailinfoproperties.manufacturername), [**Screensize**](https://docs.microsoft.com/uwp/api/windows.system.profile.knownretailinfoproperties.screensize), [**Memory**](https://docs.microsoft.com/uwp/api/windows.system.profile.knownretailinfoproperties.memory) и др.

```csharp
using Windows.UI.Xaml.Controls;
using Windows.System.Profile

TextBlock priceText = new TextBlock();
priceText.Text = RetailInfo.Properties[KnownRetailInfo.Price];
// Assume infoPanel is a StackPanel declared in XAML
this.infoPanel.Children.Add(priceText);
```

```cpp
using namespace Windows::UI::Xaml::Controls;
using namespace Windows::System::Profile;

TextBlock ^manufacturerText = ref new TextBlock();
manufacturerText.set_Text(RetailInfo::Properties[KnownRetailInfoProperties::Price]);
// Assume infoPanel is a StackPanel declared in XAML
this->infoPanel->Children->Add(manufacturerText);
```

```javascript
var pro = Windows.System.Profile;
console.log(pro.retailInfo.properties[pro.KnownRetailInfoProperties.price);
```

#### <a name="idl"></a>IDL

```cpp
//  Copyright (c) Microsoft Corporation. All rights reserved.
//
//  WindowsRuntimeAPISet

import "oaidl.idl";
import "inspectable.idl";
import "Windows.Foundation.idl";
#include <sdkddkver.h>

namespace Windows.System.Profile
{
    runtimeclass RetailInfo;
    runtimeclass KnownRetailInfoProperties;

    [version(NTDDI_WINTHRESHOLD), uuid(0712C6B8-8B92-4F2A-8499-031F1798D6EF), exclusiveto(RetailInfo)]
    [version(NTDDI_WINTHRESHOLD, Platform.WindowsPhone)]
    interface IRetailInfoStatics : IInspectable
    {
        [propget] HRESULT IsDemoModeEnabled([out, retval] boolean *value);
        [propget] HRESULT Properties([out, retval, hasvariant] Windows.Foundation.Collections.IMapView<HSTRING, IInspectable *> **value);
    }

    [version(NTDDI_WINTHRESHOLD), uuid(50BA207B-33C4-4A5C-AD8A-CD39F0A9C2E9), exclusiveto(KnownRetailInfoProperties)]
    [version(NTDDI_WINTHRESHOLD, Platform.WindowsPhone)]
    interface IKnownRetailInfoPropertiesStatics : IInspectable
    {
        [propget] HRESULT RetailAccessCode([out, retval] HSTRING *value);
        [propget] HRESULT ManufacturerName([out, retval] HSTRING *value);
        [propget] HRESULT ModelName([out, retval] HSTRING *value);
        [propget] HRESULT DisplayModelName([out, retval] HSTRING *value);
        [propget] HRESULT Price([out, retval] HSTRING *value);
        [propget] HRESULT IsFeatured([out, retval] HSTRING *value);
        [propget] HRESULT FormFactor([out, retval] HSTRING *value);
        [propget] HRESULT ScreenSize([out, retval] HSTRING *value);
        [propget] HRESULT Weight([out, retval] HSTRING *value);
        [propget] HRESULT DisplayDescription([out, retval] HSTRING *value);
        [propget] HRESULT BatteryLifeDescription([out, retval] HSTRING *value);
        [propget] HRESULT ProcessorDescription([out, retval] HSTRING *value);
        [propget] HRESULT Memory([out, retval] HSTRING *value);
        [propget] HRESULT StorageDescription([out, retval] HSTRING *value);
        [propget] HRESULT GraphicsDescription([out, retval] HSTRING *value);
        [propget] HRESULT FrontCameraDescription([out, retval] HSTRING *value);
        [propget] HRESULT RearCameraDescription([out, retval] HSTRING *value);
        [propget] HRESULT HasNfc([out, retval] HSTRING *value);
        [propget] HRESULT HasSdSlot([out, retval] HSTRING *value);
        [propget] HRESULT HasOpticalDrive([out, retval] HSTRING *value);
        [propget] HRESULT IsOfficeInstalled([out, retval] HSTRING *value);
        [propget] HRESULT WindowsVersion([out, retval] HSTRING *value);
    }

    [version(NTDDI_WINTHRESHOLD), static(IRetailInfoStatics, NTDDI_WINTHRESHOLD)]
    [version(NTDDI_WINTHRESHOLD, Platform.WindowsPhone), static(IRetailInfoStatics, NTDDI_WINTHRESHOLD, Platform.WindowsPhone)]
    [threading(both)]
    [marshaling_behavior(agile)]
    runtimeclass RetailInfo
    {
    }

    [version(NTDDI_WINTHRESHOLD), static(IKnownRetailInfoPropertiesStatics, NTDDI_WINTHRESHOLD)]
    [version(NTDDI_WINTHRESHOLD, Platform.WindowsPhone), static(IKnownRetailInfoPropertiesStatics, NTDDI_WINTHRESHOLD, Platform.WindowsPhone)]
    [threading(both)]
    [marshaling_behavior(agile)]
    runtimeclass KnownRetailInfoProperties
    {
    }
}
```

## <a name="cleanup-process"></a>Процесс очистки

Очистка начнется через две минуты после того, как покупатель прекратит взаимодействие с устройством. Будет воспроизведен демонстрационный ролик, и Windows начнет сброс всех примеров данных в контактах, фотографиях и других приложениях. В зависимости от устройства это может занять от 1-5 минут до полного сброса всех значений в нормальное состояние. Это гарантирует, что каждый клиент в магазине розничной торговли сможет выполнить переход на устройство и будет иметь те же возможности при взаимодействии с устройством.

Шаг 1. Очистка
* Все приложения Win32 и приложения Магазина закрываются
* Все файлы в известных папках, таких как __Изображения__, __Видео__, __Музыка__, __Документы__, __Сохраненные изображения__, __Альбом камеры__, __Рабочий стол__ и __Загрузки__ удаляются
* Неструктурированные и структурированные состояния перемещения удалены
* Структурированные локальные состояния удалены

Шаг 2. Настройка
* Для автономных устройств: папки остаются пустыми
* Для устройств, подключенных к Интернету: на устройство могут быть отправлены демонстрационные ресурсы для розничных магазинов из Microsoft Store

### <a name="store-data-across-user-sessions"></a>Хранение данных в пользовательских сеансах

Для хранения данных в пользовательских сеансах можно хранить сведения в __ApplicationData. Current. темпорарифолдер__ , так как процесс очистки по умолчанию не удаляет данные в этой папке автоматически. Обратите внимание, что сведения, хранящиеся с помощью *локалстате* , удаляются в процессе очистки.

### <a name="customize-the-cleanup-process"></a>Настройка процесса очистки

Чтобы настроить процесс очистки, реализуйте в приложении службу `Microsoft-RetailDemo-Cleanup` приложений.

Сценарии, в которых требуется пользовательская логика очистки, включают в себя выполнение обширной установки, загрузку и кэширование данных или отсутствие необходимости удалять *локалстате* данные.

Шаг 1. Объявите службу _Microsoft-ретаилдемо-Cleanup_ в манифесте приложения.
``` CSharp
  <Applications>
      <Extensions>
        <uap:Extension Category="windows.appService" EntryPoint="MyCompany.MyApp.RDXCustomCleanupTask">
          <uap:AppService Name="Microsoft-RetailDemo-Cleanup" />
        </uap:Extension>
      </Extensions>
   </Application>
  </Applications>

```

Шаг 2. реализуйте собственную логику очистки в функции _аппдатаклеануп_ Case, используя приведенный ниже пример шаблона.
``` CSharp
using System;
using System.IO;
using System.Runtime.Serialization.Json;
using System.Threading;
using System.Threading.Tasks;
using Windows.ApplicationModel.AppService;
using Windows.ApplicationModel.Background;
using Windows.Foundation.Collections;
using Windows.Storage;

namespace MyCompany.MyApp
{
    public sealed class RDXCustomCleanupTask : IBackgroundTask
    {
        BackgroundTaskCancellationReason _cancelReason = BackgroundTaskCancellationReason.Abort;
        BackgroundTaskDeferral _deferral = null;
        IBackgroundTaskInstance _taskInstance = null;
        AppServiceConnection _appServiceConnection = null;

        const string MessageCommand = "Command";

        public void Run(IBackgroundTaskInstance taskInstance)
        {
            // Get the deferral object from the task instance, and take a reference to the taskInstance;
            _deferral = taskInstance.GetDeferral();
            _taskInstance = taskInstance;
            _taskInstance.Canceled += new BackgroundTaskCanceledEventHandler(OnCanceled);

            AppServiceTriggerDetails appService = _taskInstance.TriggerDetails as AppServiceTriggerDetails;
            if ((appService != null) && (appService.Name == "Microsoft-RetailDemo-Cleanup"))
            {
                _appServiceConnection = appService.AppServiceConnection;
                _appServiceConnection.RequestReceived += _appServiceConnection_RequestReceived;
                _appServiceConnection.ServiceClosed += _appServiceConnection_ServiceClosed;
            }
            else
            {
                _deferral.Complete();
            }
        }

        void _appServiceConnection_ServiceClosed(AppServiceConnection sender, AppServiceClosedEventArgs args)
        {
        }

        async void _appServiceConnection_RequestReceived(AppServiceConnection sender, AppServiceRequestReceivedEventArgs args)
        {
            //Get a deferral because we will be calling async code
            AppServiceDeferral requestDeferral = args.GetDeferral();
            string command = null;
            var returnData = new ValueSet();

            try
            {
                ValueSet message = args.Request.Message;
                if (message.ContainsKey(MessageCommand))
                {
                    command = message[MessageCommand] as string;
                }

                if (command != null)
                {
                    switch (command)
                    {
                        case "AppdataCleanup":
                            {
                                // Do custom clean up logic here
                                break;
                            }
                    }
                }
            }
            catch (Exception e)
            {
            }
            finally
            {
                requestDeferral.Complete();
                // Also release the task deferral since we only process one request per instance.
                _deferral.Complete();
            }
        }

        private void OnCanceled(IBackgroundTaskInstance sender, BackgroundTaskCancellationReason reason)
        {
            _cancelReason = reason;
        }
    }
}
```

## <a name="related-links"></a>Дополнительные ссылки

* [Хранение и извлечение данных приложения](https://docs.microsoft.com/windows/uwp/app-settings/store-and-retrieve-app-data)
* [Создание и использование службы приложений](https://docs.microsoft.com/windows/uwp/launch-resume/how-to-create-and-consume-an-app-service)
* [Локализация содержимого приложения](https://docs.microsoft.com/windows/uwp/globalizing/globalizing-portal)
* [Демонстрационный опыт розничной торговли (RDX)](https://docs.microsoft.com/windows-hardware/customize/desktop/retail-demo-experience)

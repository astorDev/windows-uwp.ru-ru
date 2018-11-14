---
author: joannaleecy
title: Добавление функций розничных магазинах (RDX) в приложение
description: Подготовка приложения для демонстрации коммерческий режим, помогая демонстрации приложение на полу розничной продажи.
ms.assetid: f83f950f-7fdd-4f18-8127-b92a8f400061
ms.author: joanlee
ms.date: 10/02/2018
ms.topic: article
keywords: windows 10, uwp, приложение для демонстрации в розничных магазинах
ms.localizationpriority: medium
ms.openlocfilehash: ee0344d521b4c31449a2291517b20a09280818fc
ms.sourcegitcommit: 71e8eae5c077a7740e5606298951bb78fc42b22c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/13/2018
ms.locfileid: "6659431"
---
# <a name="add-retail-demo-rdx-features-to-your-app"></a>Добавление функций розничных магазинах (RDX) в приложение

Включите режиме демонстрации retail в приложении для Windows, таким образом пользователи, которые испытать компьютеров и устройств на продажу низ можно перейти непосредственно к.

Когда клиенты находятся в розничном магазине, они ожидают возможность опробовать демонстрации компьютеров и устройств. Они часто проводят немало времени Экспериментируя с приложениями с помощью [сталкиваются демонстрации в розничных магазинах (RDX)](https://docs.microsoft.com/windows-hardware/customize/desktop/retail-demo-experience).

Можно настроить приложение для предоставления различных возможностей в _обычной_ или _розничной торговли_ . Например если приложение запускается с процессом установки, может пропустить в коммерческом режиме и предустановить приложения с помощью параметров образцов данных и по умолчанию, поэтому они могут перейти непосредственно к.

С точки зрения наших клиентов существует только одно приложение. Чтобы помочь им различать два режима, мы рекомендуем, во время работы приложения в коммерческом режиме, он показывает слово «Retail» демо в строке заголовка или другом заметном месте.

Помимо требований к Microsoft Store для приложений приложениям, учитывающим RDX также должны быть совместимы с RDX настройки, очистки и процессами обновления, чтобы убедиться, что клиенты будут иметь неизменно получали положительные впечатления.

## <a name="design-principles"></a>Принципы проектирования

* **Показать максимально**. Используйте демонстрационный для покупателю понять, почему вашего приложения. Это скорее, впервые ваш клиент увидит вашего приложения, поэтому покажите выгодной!

* **Быстрый его отображение**. Покупатель может быть нетерпелив, поэтому чем быстрее он ощутит преимущества вашего приложения, тем лучше.

* **Не усложняйте процесс**. Демонстрационный является лифте вашего приложения.

* **Фокус на работу**. Дайте покупателю время «переварить» то, что он видит в вашем приложении. Хотя ваша задача— как можно быстрее добраться до самого интересного в вашем приложении, возможно, имеет смысл предусмотреть логические паузы.

## <a name="technical-requirements"></a>Технические требования

Как приложений, поддерживающих RDX предназначены для демонстрации лучше всего приложения для розничных покупателей, они технические требованиям и соблюдать конфиденциальность Германии в магазин Майкрософт содержит для всех RDX-приложения.

Это может использоваться как контрольный список для подготовки к процессу проверки и внесения ясности в тестирование. Обратите внимание, что приложение должно соответствовать этим требованиям не только при прохождении проверки, но и в течение всего жизненного цикла RDX-приложения, т.е. всего времени его использования на демонстрационных устройствах в розничных магазинах.

### <a name="critical-requirements"></a>Критические требования

К приложениям, учитывающим RDX, которые не соответствуют этим критическим требованиям удаляется из всех демонстрационных устройств как можно скорее.

* **Не запрашивайте личных персональные данные (Персональные)**. Сюда входят учетные данные, сведения об учетной записи Майкрософт или контакт сведения.

* , **Без ошибок возникают**. Ваше приложение должно работать без ошибок. Кроме того, на демонстрационных устройствах не должны выводиться никакие всплывающие или иные уведомления об ошибках. Ошибки негативно отражать в приложении, сам, фирменной символики, устройства, торговой марки Изготовитель устройства и корпорации Майкрософт марки.

* **Платным приложения должны иметь пробным режимом**. Либо ваше приложение должно быть бесплатной или включить в [режиме пробной версии](https://msdn.microsoft.com/windows/uwp/monetize/exclude-or-limit-features-in-a-trial-version-of-your-app). Покупатели вряд ли захотят оплачивать работу с приложением в магазине.

### <a name="high-priority-requirements"></a>Высокоприоритетные требования

Приложениям с определением RDX, которые не соответствует этим требованиям высоким приоритетом необходимо исследовать исправление немедленно. Если сразу же исправить приложение не удается, возможно, оно будет удалено со всех демонстрационных устройств для розничных магазинов.

* **Автономный возникнуть Memorable**. Ваше приложение должно предусматривать комфортную работу в автономном режиме, поскольку около 50% от устройства находятся в автономном режиме в расположениях розничной торговли. Так покупатели, взаимодействующие с вашим приложением, все равно смогут получить положительные впечатления от работы с ним.

* **Updated взаимодействие**. Ваше приложение не должно быть запрос обновлений при подключении к сети. Если обновления, они должны выполняться без вмешательства пользователя.

* **Без возможностей для анонимной связи**. Поскольку покупатель, пользующийся демонстрационным устройством является анонимным пользователем, они не сможете сообщение или общей папке содержимое с устройства.

* **Доставлять согласованное взаимодействие с помощью процесса очистки**. Состояние устройства и приложений на нем должно быть одинаковым для всех покупателей, подходящих к устройству. Ваше приложение должно использовать [процесс очистки](#clean-up-process) для возврата в состояние после каждого использования. Мы не хотим, чтобы узнать, какие последнего клиента, оставленных следующего клиента. Такими результатами могут быть набранные очки, достигнутые уровни и разблокировки.

* **Возраст соответствующего содержимого**. Все содержимое приложения должно быть ниже возрастную категорию или «для подростков». Для получения дополнительных сведений, см. в разделе [Приступая к приложения оценкой по IARC](https://www.globalratings.com/for-developers.aspx) и [ESRB ratings](https://www.esrb.org/ratings/ratings_guide.aspx).

### <a name="medium-priority-requirements"></a>Средний приоритет требования

Если приложение не соответствует этим требованиям, сотрудники розничного магазина Windows могут связаться с разработчиком напрямую, чтобы обсудить устранения возможные способы устранения проблемы.

* **Возможность выполнения успешно на различных устройствах**. Приложения должны хорошо работать на всех устройствах, включая бюджетные устройства с невысокими. Если приложение установлено на устройствах, которые не соответствуют минимальным требованиям, приложение должно четко информировать пользователя об этом. Должны быть известны минимальные требования к устройствам, чтобы приложение всегда могло работать с должной производительностью.

* **Соответствие требованиям к размеру приложений магазина розничной торговли**. Размер приложения не должен превышать 800МБ. Если RDX-учитывать приложение не соответствует требованиям размер, обратитесь непосредственно к обсудить этот вопрос команде розничного магазина Windows.

## <a name="retailinfo-api-preparing-your-code-for-demo-mode"></a>RetailInfo API: Подготовка код для демонстрационный режим

### <a name="isdemomodeenabled"></a>IsDemoModeEnabled
Свойство [**IsDemoModeEnabled**](https://docs.microsoft.com/uwp/api/windows.system.profile.retailinfo.isdemomodeenabled) в [**RetailInfo**](https://docs.microsoft.com/uwp/api/Windows.System.Profile.RetailInfo) служебный класс, который является частью пространства имен [Windows.System.Profile](https://docs.microsoft.com/uwp/api/windows.system.profile) в Windows 10 SDK, используется в качестве логического индикатора для указания какому пути кода работает приложение — обычный _ _режима или режима _розничной торговли_ .

``` csharp
using Windows.Storage;

StorageFolder folder = ApplicationData.Current.LocalFolder;

if (Windows.System.Profile.RetailInfo.IsDemoModeEnabled) 
{
    // Use the demo specific directory
    folder = await folder.GetFolderAsync(“demo”);
}

StorageFile file = await folder.GetFileAsync(“hello.txt”);
// Now read from file
```

``` cpp
using namespace Windows::Storage;

StorageFolder^ localFolder = ApplicationData::Current->LocalFolder;

if (Windows::System::Profile::RetailInfo::IsDemoModeEnabled) 
{
    // Use the demo specific directory
    create_task(localFolder->GetFolderAsync(“demo”).then([this](StorageFolder^ demoFolder)
    {
        return demoFolder->GetFileAsync(“hello.txt”);
    }).then([this](task<StorageFile^> fileTask)
    {
        StorageFile^ file = fileTask.get();
    });
    // Do something with file
}
else
{
    create_task(localFolder->GetFileAsync(“hello.txt”).then([this](StorageFile^ file)
    {
        // Do something with file
    });
}
```

``` javascript
if (Windows.System.Profile.retailInfo.isDemoModeEnabled) {
    console.log(“Retail mode is enabled.”);
} else {
    Console.log(“Retail mode is not enabled.”);
}
```

### <a name="retailinfoproperties"></a>RetailInfo.Properties

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

```
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

Очистка начинается двух минут после остановки покупатель взаимодействует с устройством. Воспроизводит демонстрации в розничных магазинах, и Windows начнет сброса любого демонстрационные данные в контакты, фотографий и других приложений. В зависимости от устройства это может занять между 1-5 минут, чтобы полностью сбросьте все обратно в обычный. Это гарантирует, что каждый пользователь в розничном магазине к устройству и быть одинаковым при взаимодействии с устройством.

Шаг 1: очистка
* Все приложения Win32 и приложения Магазина закрываются
* Все файлы в известных папках, таких как __Изображения__, __Видео__, __Музыка__, __Документы__, __Сохраненные изображения__, __Альбом камеры__, __Рабочий стол__ и __Загрузки__ удаляются
* Неструктурированные и структурированные состояния перемещения удалены
* Структурированные локальные состояния удалены

Шаг 2: Настройка
* Для автономных устройств: папки остаются пустыми
* Для устройств, подключенных к Интернету: на устройство могут быть отправлены демонстрационные ресурсы для розничных магазинов из Microsoft Store

### <a name="store-data-across-user-sessions"></a>Хранить данные между сеансами пользователя

Для сохранения данных между сеансами пользователя, можно хранить сведения в __ApplicationData.Current.TemporaryFolder__ , поскольку процесс очистки по умолчанию автоматически не удаляет данные в этой папке. Обратите внимание, что информация, хранимая с помощью *LocalState* удаляется в процессе очистки.

### <a name="customize-the-cleanup-process"></a>Настроить процесс очистки

Чтобы настроить процесс очистки, реализовать `Microsoft-RetailDemo-Cleanup` службы приложения в приложение.

Сценарии, где необходим логику пользовательского очистки включает в себя широко настройкой, скачивание и кэширование данных или не хотят *LocalState* данных.

Шаг 1: Объявите службу _Microsoft-RetailDemo-Cleanup_ в манифесте приложения.
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

Шаг 2: Реализуйте логику пользовательского очистки в случае функция _AppdataCleanup_ , руководствуясь шаблоном ниже.
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

* [Сохранение и получение данных приложения](https://msdn.microsoft.com/windows/uwp/app-settings/store-and-retrieve-app-data)
* [Создание и использование службы приложений](https://msdn.microsoft.com/windows/uwp/launch-resume/how-to-create-and-consume-an-app-service)
* [Локализация содержимого приложения](https://msdn.microsoft.com/windows/uwp/globalizing/globalizing-portal)
* [Демонстрационный (RDX)](https://docs.microsoft.com/windows-hardware/customize/desktop/retail-demo-experience)

---
title: Добавление функции demo (RDX) розничной торговли в приложение
description: Подготовка приложения к демонстрационном режиме розничной торговли, помогая Продемонстрируйте свое приложение на продажу в торговом зале.
ms.assetid: f83f950f-7fdd-4f18-8127-b92a8f400061
ms.date: 10/02/2018
ms.topic: article
keywords: windows 10, uwp, приложение для демонстрации в розничных магазинах
ms.localizationpriority: medium
ms.openlocfilehash: b66435dd7c94762874461b48e19e9a60224f287b
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57596759"
---
# <a name="add-retail-demo-rdx-features-to-your-app"></a>Добавление функции demo (RDX) розничной торговли в приложение

Включает режим Демонстрация розничной торговли в приложении Windows, поэтому клиенты, которые опробовать компьютерами и устройствами на продажу floor можно перейти непосредственно к.

Когда клиенты находятся в розничном магазине, они хотят иметь возможность испытать демонстрации компьютеров и устройств. Они часто тратят значительную часть своего времени, начав с приложениями с помощью [розничной торговли — Демонстрация качества (RDX)](https://docs.microsoft.com/windows-hardware/customize/desktop/retail-demo-experience).

Приложение можно настроить для предоставления различных функциями и возможностью в _обычный_ или _розничной торговли_ режимы. Например если приложение запускается с процессом установки, может пропустить его в режиме розничной торговли и заполнить приложение с помощью примера данных и по умолчанию параметры, поэтому они могут перейти непосредственно к.

С точки зрения наших заказчиков есть только одно приложение. Чтобы помочь клиентам различения двух режимов, мы рекомендуем, которое пока приложение находится в режиме розничной торговли, содержит слово «Retail» видном месте в строке заголовка или в подходящее расположение.

Помимо требований к Microsoft Store для приложений приложений, использующих RDX также должен быть совместим с установки RDX, очистки и процессов обновления, убедитесь, что клиенты имеют постоянно позитивная в розничном магазине.

## <a name="design-principles"></a>Принципы проектирования

* **Показать наиболее**. Используйте взаимодействие в розничной торговле demo для демонстрации, почему приложение крут хотите знать?. Это скорее всего, первый раз клиент будет см. в разделе приложения, чтобы отобразить их рекомендации фрагмент!

* **Показать быстрое**. Покупатель может быть нетерпелив, поэтому чем быстрее он ощутит преимущества вашего приложения, тем лучше.

* **Усложнять истории**. Демонстрация взаимодействие в розничной торговле является зацепки, для приложения значения.

* **Сосредоточьтесь на работу**. Дайте покупателю время «переварить» то, что он видит в вашем приложении. Хотя ваша задача — как можно быстрее добраться до самого интересного в вашем приложении, возможно, имеет смысл предусмотреть логические паузы.

## <a name="technical-requirements"></a>Технические требования

Как приложений, использующих RDX предназначены для демонстрации лучшее из приложения, чтобы розничные клиенты, они технических требований и Обеспечьте соответствие нормам конфиденциальности, доступные для всех розничных demo качества приложений Microsoft Store.

Это может использоваться как контрольный список для подготовки для процесса проверки и обеспечить ясность в процессе тестирования. Обратите внимание, что приложение должно соответствовать этим требованиям не только при прохождении проверки, но и в течение всего жизненного цикла RDX-приложения, т. е. всего времени его использования на демонстрационных устройствах в розничных магазинах.

### <a name="critical-requirements"></a>Важным требованиям

RDX с поддержкой приложений, не соответствующий требованиям этих важных удаляется со всех устройств Демонстрация розничной торговли — как можно скорее.

* **Больше не спрашивать персональных данных (PII)**. Сюда входят сведения о входе, сведения учетной записи Майкрософт или обратитесь в службу сведения.

* **Возможность работы без ошибок**. Ваше приложение должно работать без ошибок. Кроме того, на демонстрационных устройствах не должны выводиться никакие всплывающие или иные уведомления об ошибках. Ошибки отрицательно отражения в приложении, сам, свой бренд, торговой марки устройства, изготовителя устройства и корпорации Майкрософт марки.

* **Платные приложения должен иметь режим пробной версии**. Приложение либо должно быть бесплатной или включать [режиме пробной версии](https://msdn.microsoft.com/windows/uwp/monetize/exclude-or-limit-features-in-a-trial-version-of-your-app). Покупатели вряд ли захотят оплачивать работу с приложением в магазине.

### <a name="high-priority-requirements"></a>Требования к высоким приоритетом

Приложения с поддержкой RDX, которые не соответствуют этим требованиям с высоким приоритетом необходимо определять незамедлительно по исправлению. Если сразу же исправить приложение не удается, возможно, оно будет удалено со всех демонстрационных устройств для розничных магазинов.

* **Запоминающийся автономной работы**. Вашему приложению для демонстрации удобной вне сети, как в пунктах розничной торговли около 50% устройства находятся в автономном режиме. Так покупатели, взаимодействующие с вашим приложением, все равно смогут получить положительные впечатления от работы с ним.

* **Обновлены возможности работы с содержимым**. Ваше приложение никогда не будет запрашивать обновления при работе в сети. Если обновление требуется, они должны выполняться без вмешательства пользователя.

* **Нет связи анонимный**. Так как клиент, использующий устройство розничной торговли Демонстрация является анонимным, они не сможете содержимое сообщения или общей папки с устройства.

* **Предоставлять единообразный способ взаимодействия с помощью процесса очистки**. Состояние устройства и приложений на нем должно быть одинаковым для всех покупателей, подходящих к устройству. Приложение должно использовать [процесса очистки](#cleanup-process) для возврата к состоянию по умолчанию после каждого использования. Мы не хотим Далее клиенту последнего клиента остаются на прежнем см. в разделе. Такими результатами могут быть набранные очки, достигнутые уровни и разблокировки.

* **Возраст соответствующее содержимое**. Все содержимое приложения должно быть назначены подростка или ниже категории оценки. Дополнительные сведения см. в разделе [размещение приложений оценкой по IARC](https://www.globalratings.com/for-developers.aspx) и [оценки ESRB](https://www.esrb.org/ratings/ratings_guide.aspx).

### <a name="medium-priority-requirements"></a>Требования к средним приоритетом

Если приложение не соответствует этим требованиям, сотрудники розничного магазина Windows могут связаться с разработчиком напрямую, чтобы обсудить устранения возможные способы устранения проблемы.

* **Возможность успешного выполнения с использованием разнообразных устройств**. Приложения должны работать на всех устройствах, включая устройства с низким быстродействием спецификации. Если приложение устанавливается на устройствах, не соответствует минимальным спецификациям, приложению необходимо четко информировать пользователей об этом. Должны быть известны минимальные требования к устройствам, чтобы приложение всегда могло работать с должной производительностью.

* **Соответствовать требованиям к размеру приложения магазин розничной торговли**. Размер приложения не должен превышать 800 МБ. Обратитесь в службу Windows Store розничной торговли — непосредственно для дальнейшего обсуждения Если RDX с поддержкой приложения не соответствует требованиям размер.

## <a name="retailinfo-api-preparing-your-code-for-demo-mode"></a>RetailInfo API: Подготовке кода в демонстрационном режиме

### <a name="isdemomodeenabled"></a>IsDemoModeEnabled
[ **IsDemoModeEnabled** ](https://docs.microsoft.com/uwp/api/windows.system.profile.retailinfo.isdemomodeenabled) свойство в [ **RetailInfo** ](https://docs.microsoft.com/uwp/api/Windows.System.Profile.RetailInfo) служебный класс, входящий в состав из [ Windows.System.Profile](https://docs.microsoft.com/uwp/api/windows.system.profile) пространства имен в пакете SDK для Windows 10, используется как логический индикатор для указания какой путь кода, ваше приложение работает на - _обычный_ режиме или _розничной торговли_ режим.

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

Она начнет две минуты после остановки покупатель, взаимодействующий с устройством. Воспроизводит Демонстрация розничной торговли, начнет Сброс все добавленные данные контактов, фотографий и других приложений Windows. В зависимости от устройства это может занять от 1 до 5 минут до полностью сбросьте все обратно в нормальное состояние. Это гарантирует, что каждый клиент в розничном магазине можно переместиться вверх на устройство и сделали то же самое при взаимодействии с устройством.

Шаг 1. Очистка
* Все приложения Win32 и приложения Магазина закрываются
* Все файлы в известных папках, таких как __Изображения__, __Видео__, __Музыка__, __Документы__, __Сохраненные изображения__, __Альбом камеры__, __Рабочий стол__ и __Загрузки__ удаляются
* Неструктурированные и структурированные состояния перемещения удалены
* Структурированные локальные состояния удалены

Шаг 2.  Установка
* Для автономных устройств: Папки остаются пустыми
* Для устройств в сети: Демонстрация средств розничной торговли можно отправить на устройство из Microsoft Store

### <a name="store-data-across-user-sessions"></a>Store данных между пользовательскими сессиями

Чтобы хранить данные в рамках пользовательских сеансов, можно сохранить сведения в __ApplicationData.Current.TemporaryFolder__ по умолчанию процесс очистки автоматически не удаляет данные в этой папке. Обратите внимание, эти сведения, сохраненные с использованием *LocalState* удаляется во время процесса очистки.

### <a name="customize-the-cleanup-process"></a>Настройка процесса очистки

Чтобы настроить процесс очистки, реализуйте `Microsoft-RetailDemo-Cleanup` службы приложений в веб-приложения.

Сценарии, когда требуется логики пользовательскую очистку включает в себя выполнение Расширенная настройка, скачивание и кэширование данных не желая *LocalState* удаления данных.

Шаг 1. Объявите _Microsoft RetailDemo очистки_ службы в манифесте приложения.
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

Шаг 2. Реализовать логику пользовательскую очистку под _AppdataCleanup_ функция case, используя представленный ниже шаблон.
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

* [Store и извлечения данных приложения](https://msdn.microsoft.com/windows/uwp/app-settings/store-and-retrieve-app-data)
* [Как создать и использовать службу приложений](https://msdn.microsoft.com/windows/uwp/launch-resume/how-to-create-and-consume-an-app-service)
* [Локализация содержимого приложения](https://msdn.microsoft.com/windows/uwp/globalizing/globalizing-portal)
* [Демонстрация розничной торговли (RDX)](https://docs.microsoft.com/windows-hardware/customize/desktop/retail-demo-experience)

---
author: TylerMSFT
title: Преобразование службы приложения для ее запуска в одном процессе с основным приложением
description: Преобразуйте код службы приложений, выполняемый в отдельном фоновом процессе, в код, выполняемый в одном процессе с поставщиком службы приложений.
ms.author: twhitney
ms.date: 11/03/2017
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: Windows 10, uwp, службы приложений
ms.assetid: 30aef94b-1b83-4897-a2f1-afbb4349696a
ms.localizationpriority: medium
ms.openlocfilehash: a77ea3cefcc423e710ab0afebb3fa064e61507ec
ms.sourcegitcommit: f5cf806a595969ecbb018c3f7eea86c7a34940f6
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/10/2018
ms.locfileid: "3821688"
---
# <a name="convert-an-app-service-to-run-in-the-same-process-as-its-host-app"></a>Преобразование службы приложений для ее запуска в одном процессе с ведущим приложением

[AppServiceConnection](https://msdn.microsoft.com/library/windows/apps/windows.applicationmodel.appservice.appserviceconnection.aspx) позволяет другому приложению выводить ваше приложение из фонового режима и устанавливать прямую линию связи с ним.

С появлением служб приложений, работающих в процессе, два запущенных на переднем плане приложения могут устанавливать прямую линию обмена данными через подключение служб приложения. Службы приложения теперь могут выполняется в том же процессе, что и приложение переднего плана, что упрощает обмен данными между приложениями и исключает необходимость выделения кода службы в отдельный проект.

Для перехода от модели службы приложения, выполняемой вне процесса, к модели с выполнением внутри процесса необходимо сделать два изменения. Первое изменение вносится в манифест.

> ```xml
> <Package
>    ...
>   <Applications>
>       <Application Id=...
>           ...
>           EntryPoint="...">
>           <Extensions>
>               <uap:Extension Category="windows.appService">
>                   <uap:AppService Name="InProcessAppService" />
>               </uap:Extension>
>           </Extensions>
>           ...
>       </Application>
>   </Applications>
> ```

Удалить `EntryPoint` атрибут из `<Extension>` элемента, так как теперь [OnBackgroundActivated()](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.application.onbackgroundactivated.aspx) является точкой входа, который будет использоваться при вызове службы приложения.

Второе изменение заключается в переносе логики службы из отдельного проекта фоновой задачи в методы, которые можно вызывать из метода **OnBackgroundActivated()**.

Теперь приложение может напрямую выполнять службу приложения. Например в файле App.xaml.cs:

``` cs
using Windows.ApplicationModel.AppService;
using Windows.ApplicationModel.Background;
...

sealed partial class App : Application
{
  private AppServiceConnection _appServiceConnection;
  private BackgroundTaskDeferral _appServiceDeferral;

  ...

  protected override void OnBackgroundActivated(BackgroundActivatedEventArgs args)
  {
      base.OnBackgroundActivated(args);
      IBackgroundTaskInstance taskInstance = args.TaskInstance;
      AppServiceTriggerDetails appService = taskInstance.TriggerDetails as AppServiceTriggerDetails;
      _appServiceDeferral = taskInstance.GetDeferral();
      taskInstance.Canceled += OnAppServicesCanceled;
      _appServiceConnection = appService.AppServiceConnection;
      _appServiceConnection.RequestReceived += OnAppServiceRequestReceived;
      _appServiceConnection.ServiceClosed += AppServiceConnection_ServiceClosed;
  }

  private async void OnAppServiceRequestReceived(AppServiceConnection sender, AppServiceRequestReceivedEventArgs args)
  {
      AppServiceDeferral messageDeferral = args.GetDeferral();
      ValueSet message = args.Request.Message;
      string text = message["Request"] as string;

      if ("Value" == text)
      {
          ValueSet returnMessage = new ValueSet();
          returnMessage.Add("Response", "True");
          await args.Request.SendResponseAsync(returnMessage);
      }
      messageDeferral.Complete();
  }

  private void OnAppServicesCanceled(IBackgroundTaskInstance sender, BackgroundTaskCancellationReason reason)
  {
      _appServiceDeferral.Complete();
  }

  private void AppServiceConnection_ServiceClosed(AppServiceConnection sender, AppServiceClosedEventArgs args)
  {
      _appServiceDeferral.Complete();
  }
}
```

В приведенном выше коде метод `OnBackgroundActivated` обрабатывает активацию службы приложения. Регистрируются все события, необходимые для обмена данными через [AppServiceConnection](https://msdn.microsoft.com/library/windows/apps/windows.applicationmodel.appservice.appserviceconnection.aspx), и сохраняется объект откладывания задачи, чтобы ее можно было пометить как выполненную после завершения обмена данными между приложениями.

Когда приложение получает запрос, оно считывает предоставленный [ValueSet](https://msdn.microsoft.com/library/windows/apps/windows.foundation.collections.valueset.aspx) для проверки наличия строк `Key` и `Value`. Если они существуют, служба приложения возвращает пару строковых значений `Response` и `True` в приложение на другой стороне **AppServiceConnection**.

Подробнее о подключении к другим приложением и обмене данными с ними см. в разделе [Создание и использование службы приложения](https://msdn.microsoft.com/windows/uwp/launch-resume/how-to-create-and-consume-an-app-service?f=255&MSPPError=-2147217396).

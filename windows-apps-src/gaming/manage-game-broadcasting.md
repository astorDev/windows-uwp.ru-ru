---
author: drewbatgit
ms.assetid: 
description: "Здесь рассказывается, как управлять функцией трансляции игрового процесса для приложения UWP."
title: "Управление трансляциями игр"
ms.author: drewbat
ms.date: 09/27/2017
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: "windows 10, игра, трансляция"
localizationpriority: medium
ms.openlocfilehash: de1d91e86826109a4e8939df17e938123ad8eb34
ms.sourcegitcommit: 44a24b580feea0f188c7eae36e72e4a4f412802b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/31/2017
---
# <a name="manage-game-broadcasting"></a>Управление трансляциями игр
В этой статье рассказывается, как управлять функцией трансляции игрового процесса для приложения UWP. Пользователи должны запускать трансляцию игрового процесса с помощью системного пользовательского интерфейса, встроенного в Windows, но, начиная с Windows 10 версии 1709, приложения могут запускать системный пользовательский интерфейса трансляции и получать уведомления о начале и прекращении трансляции.

## <a name="add-the-windows-desktop-extensions-for-the-uwp-to-your-app"></a>Добавление расширений рабочего стола Windows для UWP в приложение
API-интерфейсы для управления трансляцией приложения, представленные в пространстве имен **[Windows.Media.AppBroadcasting](https://docs.microsoft.com/uwp/api/windows.media.appbroadcasting)**, не включаются в универсальный контракт API. Для доступа к этим API необходимо добавить ссылку на расширения рабочего стола Windows для UWP в приложение, выполнив следующие действия.

1. В Visual Studio в **обозревателе решений** разверните свой проект UWP и щелкните правой кнопкой мыши папку **Ссылки**, а затем выберите пункт **Добавить ссылку...**. 
2. Разверните узел **универсальных приложений для Windows** и выберите **Расширения**.
3. В списке расширений установите флажок рядом с пунктом **расширения рабочего стола Windows для UWP**, который соответствует целевой сборке для проекта. Для функций трансляции приложения версия должна быть 1709 или более поздней.
4. Нажмите **OК**.

## <a name="launch-the-system-ui-to-allow-the-user-to-initiate-broadcasting"></a>Запуск системного пользовательского интерфейса, позволяющего пользователю начать трансляцию
Существует несколько причин, по которым приложение может в определенный момент не иметь возможности выполнять трансляцию; в их число входит несоответствие устройства требованиям к оборудованию для трансляции и осуществление трансляции другим приложением. Перед запуском системного пользовательского интерфейса можно проверить, имеет ли ваше приложение возможность трансляции в данный момент. Во-первых, убедитесь, что API-интерфейсы трансляции доступны на текущем устройстве. API-интерфейсы недоступны на устройствах под управлением версий ОС более ранних, чем Windows 10, версия 1709. Вместо того чтобы проверить наличие определенной версии операционной системы, используйте метод **[ApiInformation.IsApiContractPresent](https://docs.microsoft.com/uwp/api/windows.foundation.metadata.apiinformation#Windows_Foundation_Metadata_ApiInformation_IsApiContractPresent_System_String_System_UInt16_System_UInt16_)** для запроса *Windows.Media.AppBroadcasting.AppBroadcastingContract* версии 1.0. Если этот контракт представлен, API-интерфейсы трансляции доступны на этом устройстве.

Затем получите экземпляр класса **[AppBroadcastingUI](https://docs.microsoft.com/uwp/api/windows.media.appbroadcasting.appbroadcastingui)**, вызвав фабричный метод **[GetDefault](https://docs.microsoft.com/uwp/api/windows.media.appbroadcasting.appbroadcastingui#Windows_Media_AppBroadcasting_AppBroadcastingUI_GetDefault)** на компьютере, где вход выполнен только одним пользователем. На консоли XBox, где несколько пользователей могут находиться в системе одновременно, вместо этого вызовите **[GetForUser](https://docs.microsoft.com/uwp/api/windows.media.appbroadcasting.appbroadcastingui#Windows_Media_AppBroadcasting_AppBroadcastingUI_GetForUser_Windows_System_User_)**. Затем вызовите **[GetStatus](https://docs.microsoft.com/uwp/api/windows.media.appbroadcasting.appbroadcastingui#Windows_Media_AppBroadcasting_AppBroadcastingUI_GetStatus)** для получения состояния трансляция вашего приложения.

Свойство **[CanStartBroadcast](https://docs.microsoft.com/uwp/api/windows.media.appbroadcasting.appbroadcastingstatus#Windows_Media_AppBroadcasting_AppBroadcastingStatus_CanStartBroadcast)** класса **AppBroadcastingStatus** сообщает, может ли приложение в настоящее время начать трансляцию. Если нет, вы можете проверить свойство **[Details](https://docs.microsoft.com/uwp/api/windows.media.appbroadcasting.appbroadcastingstatus#Windows_Media_AppBroadcasting_AppBroadcastingStatus_Details)**, чтобы определить причину недоступности трансляции. В зависимости от причины, возможно, вы решите отобразить состояние пользователю или показать инструкции для включения трансляции.

[!code-cpp[CanStartBroadcast](./code/AppBroadcast/cpp/AppBroadcastExampleApp/App.cpp#SnippetCanStartBroadcast)]

Запросить отображение системой пользовательского интерфейса для трансляции приложения можно, вызвав **[ShowBroadcastUI](https://docs.microsoft.com/uwp/api/windows.media.appbroadcasting.appbroadcastingui#Windows_Media_AppBroadcasting_AppBroadcastingUI_ShowBroadcastUI)**.

> [!NOTE] 
> Метод **ShowBroadcastUI** представляет запрос, который может завершиться сбоем, в зависимости от текущего состояния системы. Ваше приложение не должно предполагать, что после вызова этого метода началась трансляция. Используйте событие **[IsCurrentAppBroadcastingChanged](https://docs.microsoft.com/uwp/api/windows.media.appbroadcasting.appbroadcastingmonitor#Windows_Media_AppBroadcasting_AppBroadcastingMonitor_IsCurrentAppBroadcastingChanged)**, чтобы получать уведомления, когда трансляция запускается или останавливается.

[!code-cpp[LaunchBroadcastUI](./code/AppBroadcast/cpp/AppBroadcastExampleApp/App.cpp#SnippetLaunchBroadcastUI)]

## <a name="receive-notifications-when-broadcasting-starts-and-stops"></a>Получение уведомлений при запуске и остановке трансляции
Зарегистрируйтесь, чтобы получать уведомления, когда пользователь использует системный пользовательский интерфейс для запуска или остановки трансляции приложения, путем инициализации экземпляра класса **[AppBroadcastingMonitor](https://docs.microsoft.com/uwp/api/windows.media.appbroadcasting.appbroadcastingmonitor)** и регистрации обработчика для события **[IsCurrentAppBroadcastingChanged](https://docs.microsoft.com/uwp/api/windows.media.appbroadcasting.appbroadcastingmonitor#Windows_Media_AppBroadcasting_AppBroadcastingMonitor_IsCurrentAppBroadcastingChanged)**. Как отмечено в предыдущем разделе, не забудьте в определенный момент использовать **[ApiInformation.IsApiContractPresent](https://docs.microsoft.com/uwp/api/windows.foundation.metadata.apiinformation#Windows_Foundation_Metadata_ApiInformation_IsApiContractPresent_System_String_System_UInt16_System_UInt16_)**, чтобы убедиться, что API-интерфейсы трансляции представлены на устройстве, прежде чем их использовать. 

[!code-cpp[AppBroadcastingRegisterChangedHandler](./code/AppBroadcast/cpp/AppBroadcastExampleApp/App.cpp#SnippetAppBroadcastingRegisterChangedHandler)]

В обработчике для события **IsCurrentAppBroadcastingChanged** может потребоваться обновление пользовательского интерфейса приложения для отражения текущего состояния трансляции.

[!code-cpp[AppBroadcastingChangedHandler](./code/AppBroadcast/cpp/AppBroadcastExampleApp/App.cpp#SnippetAppBroadcastingChangedHandler)]

## <a name="related-topics"></a>Статьи по теме

* [Игры](index.md)

 

 




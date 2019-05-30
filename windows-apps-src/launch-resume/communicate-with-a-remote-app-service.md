---
title: Обмен данными с удаленной службой приложений
description: Обмен сообщениями со службой приложения на удаленном устройстве с помощью Project Rome.
ms.assetid: a0261e7a-5706-4f9a-b79c-46a3c81b136f
ms.date: 02/08/2017
ms.topic: article
keywords: подключение к Windows 10, uwp, устройств, удаленных систем, Рим, проект Рим, фоновая задача, службы приложений
ms.localizationpriority: medium
ms.openlocfilehash: 067b465feccda424dd6a8e3f44e784166afe6d48
ms.sourcegitcommit: ac7f3422f8d83618f9b6b5615a37f8e5c115b3c4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/29/2019
ms.locfileid: "66366422"
---
# <a name="communicate-with-a-remote-app-service"></a>Обмен данными с удаленной службой приложений

Помимо запуска приложения на удаленном устройстве с помощью универсального кода ресурса (URI), можно также запускать на удаленных устройствах *службы приложений* и обмениваться данными с этими службами. Любое устройство на основе Windows можно использовать как клиентское или главное устройство. Это дает практически неограниченное количество способов взаимодействия с подключенными устройствами без необходимости переноса приложения на передний план.

## <a name="set-up-the-app-service-on-the-host-device"></a>Настройка службы приложений на главном устройстве
Чтобы запустить службу приложений на удаленном устройстве, необходимо, чтобы на нем уже был установлен поставщик этой службы приложений. В этом руководстве используется версия [образца службы приложений "генератор случайных чисел"](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/AppServices) на CSharp, которая доступна в [репозитории примеров универсальных приложений Windows](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/AppServices). Инструкции по созданию собственной службы приложений см. в разделе [Создание и использование службы приложений](how-to-create-and-consume-an-app-service.md).

Используется ли готовая служба приложения или создается собственная служба, необходимо внести несколько изменений, чтобы сделать службу совместимой с удаленными системами. В Visual Studio перейдите в проект поставщика службы приложения (в примере он называется AppServicesProvider) и выберите соответствующий файл _Package.appxmanifest_. Щелкните правой кнопкой мыши и выберите **Перейти к коду** для просмотра всего содержимого файла. Создание **расширения** элемент в главном **приложения** элемент (или найдите его, если он уже существует). Затем создайте **расширение** определение проекта службы приложений и ссылаться на родительский проект.

``` xml
...
<Extensions>
    <uap:Extension Category="windows.appService" EntryPoint="RandomNumberService.RandomNumberGeneratorTask">
        <uap3:AppService Name="com.microsoft.randomnumbergenerator"/>
    </uap:Extension>
</Extensions>
...
```

Рядом с полем **AppService** элемента, добавьте **SupportsRemoteSystems** атрибут:

``` xml
...
<uap3:AppService Name="com.microsoft.randomnumbergenerator" SupportsRemoteSystems="true"/>
...
```

Чтобы использовать элементы в этом **uap3** пространства имен, необходимо добавить определение пространства имен в верхней части файла манифеста, если она еще не создана.

```xml
<?xml version="1.0" encoding="utf-8"?>
<Package
  xmlns="http://schemas.microsoft.com/appx/manifest/foundation/windows10"
  xmlns:mp="http://schemas.microsoft.com/appx/2014/phone/manifest"
  xmlns:uap="http://schemas.microsoft.com/appx/manifest/uap/windows10"
  xmlns:uap3="http://schemas.microsoft.com/appx/manifest/uap/windows10/3">
  ...
</Package>
```

Затем постройте проект приложения службы поставщика и развернуть его на узел устройства.

## <a name="target-the-app-service-from-the-client-device"></a>Выбор службы приложений с клиентского устройства
На устройстве, с которого должна вызываться удаленная служба приложений, необходимо приложение с поддержкой функциональности удаленных систем. Эту функциональность можно добавить в приложение, которое обеспечивает работу службы приложений на главном устройстве (в этом случае на оба устройства устанавливается одно и то же приложение), или реализовать в совершенно другом приложении.

Чтобы код из этого раздела работал в приведенном виде, необходимы следующие операторы **using**:

[!code-cs[Main](./code/RemoteAppService/MainPage.xaml.cs#SnippetUsings)]


Сначала необходимо создать экземпляр объекта [**AppServiceConnection**](https://docs.microsoft.com/uwp/api/Windows.ApplicationModel.AppService.AppServiceConnection), точно так же, как и при локальном вызове службы приложения. Этот процесс подробно описан в разделе [Создание и использование службы приложений](how-to-create-and-consume-an-app-service.md). В этом примере в качестве целевой службы приложений используется служба генератора случайных чисел.

> [!NOTE]
> Предполагается, что объект [RemoteSystem](https://docs.microsoft.com/uwp/api/Windows.System.RemoteSystems.RemoteSystem) уже каким-то образом получен в коде, который будет вызывать следующий метод. Инструкции относительно того, как это сделать, см. в разделе [Запуск удаленного приложения](launch-a-remote-app.md).

[!code-cs[Main](./code/RemoteAppService/MainPage.xaml.cs#SnippetAppService)]

Далее создается объект [**RemoteSystemConnectionRequest**](https://docs.microsoft.com/uwp/api/Windows.System.RemoteSystems.RemoteSystemConnectionRequest) для требуемого удаленного устройства. Затем он используется для открытия **AppServiceConnection** с этим устройством. Обратите внимание, что для краткости в приведенном ниже примере обработка ошибок и отчетность значительно упрощены.

[!code-cs[Main](./code/RemoteAppService/MainPage.xaml.cs#SnippetRemoteConnection)]

На этом этапе у вас должно иметься открытое подключение к службе приложений на удаленном компьютере.

## <a name="exchange-service-specific-messages-over-the-remote-connection"></a>Обмен специфичными для службы сообщениями по удаленному подключению

Отсюда можно отправлять в службу и получать из службы сообщения в форме объектов [**ValueSet**](https://docs.microsoft.com/uwp/api/windows.foundation.collections.valueset) (дополнительные сведения см. в разделе [Создание и использование службы приложения](how-to-create-and-consume-an-app-service.md)). Служба генератора случайных чисел принимает 2 целых числа с ключами `"minvalue"` и `"maxvalue"` в качестве входных данных, случайным образом выбирает целое число внутри диапазона и возвращает это число в вызывающий процесс с ключом `"Result"`.

[!code-cs[Main](./code/RemoteAppService/MainPage.xaml.cs#SnippetSendMessage)]

Теперь вы подключены к службе приложений на целевом главном устройстве, выполнили операцию на этом устройстве и получили данные на клиентском устройстве в ответном сообщении.

## <a name="related-topics"></a>См. также

[Подключенных приложений и устройств (проект рим) Обзор](connected-apps-and-devices.md)  
[Запуск удаленного приложения](launch-a-remote-app.md)  
[Создание и использование службы приложений](how-to-create-and-consume-an-app-service.md)  
[Удаленный Справочник по API системы](https://docs.microsoft.com/uwp/api/Windows.System.RemoteSystems)  
[Пример удаленных систем](https://github.com/Microsoft/Windows-universal-samples/tree/dev/Samples/RemoteSystems)

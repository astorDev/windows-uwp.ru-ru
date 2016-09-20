---
author: PatrickFarley
title: "Обмен данными с удаленной службой приложения"
description: "Обмен сообщениями со службой приложения на удаленном устройстве с помощью проекта «Рим»."
translationtype: Human Translation
ms.sourcegitcommit: c90304b7ca3f7185fca9146aa2303b09cba5ab9a
ms.openlocfilehash: bff77a63d0f88907410c74d4dce19fb422c1bd3f

---

# Обмен данными с удаленной службой приложения

Помимо запуска приложения на удаленном устройстве с помощью URI, можно также запускать на удаленных устройствах *службы приложений* и обмениваться данными с этими службами. Любое устройство на основе Windows можно использовать как домашнее или целевое устройство, а также и как домашнее, и как целевое. Это дает практически неограниченное количество способов взаимодействия с подключенными устройствами без необходимости переноса приложения на передний план.

## Настройка службы приложения на целевом устройстве
Чтобы запустить службу приложения на удаленном устройстве, необходимо, чтобы на целевом устройстве уже был установлен поставщик этой службы приложения. В этом руководстве используется служба приложения «генератор случайных чисел», которая доступна в [репозитории примеров универсальных приложений Windows](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/AppServices). Инструкции по созданию собственной службы приложения см. в разделе [Создание и использование службы приложения](how-to-create-and-consume-an-app-service.md).

Используется ли готовая служба приложения или создается собственная служба, необходимо внести несколько изменений, чтобы сделать службу совместимой с удаленными системами. В Visual Studio перейдите в проект поставщика службы приложения и выберите соответствующий файл Package.appxmanifest. Щелкните правой кнопкой мыши и выберите **Перейти к коду** для просмотра всего содержимого файла. Найдите элемент **Расширение**, определяющий проект как службу приложения и задающий имя родительского проекта.

``` xml
...
<Extensions>
    <uap:Extension Category="windows.appService" EntryPoint="RandomNumberService.RandomNumberGeneratorTask">
        <uap:AppService Name="com.microsoft.randomnumbergenerator"/>
    </uap:Extension>
</Extensions>
...
```

Измените пространство имен элемента **AppService** на **uap3** и добавьте атрибут **SupportsRemoteSystems**:

``` xml
...
<uap3:AppService Name="com.microsoft.randomnumbergenerator" SupportsRemoteSystems="true"/>
...
```

Чтобы использовать элементы в этом новом пространстве имен, необходимо добавить определение пространства имен в начало файла манифеста.

``` xml
<?xml version="1.0" encoding="utf-8"?>
<Package
  xmlns="http://schemas.microsoft.com/appx/manifest/foundation/windows10"
  xmlns:mp="http://schemas.microsoft.com/appx/2014/phone/manifest"
  xmlns:uap="http://schemas.microsoft.com/appx/manifest/uap/windows10"
  xmlns:uap3="http://schemas.microsoft.com/appx/manifest/uap/windows10/3">
  ...
</Package>
```

Выполните сборку проекта поставщика услуг приложения и разверните проект на целевых устройствах.

## Выбор службы приложения с домашнего устройства
На устройстве, *с которого* должна вызываться удаленная служба приложения, необходимо приложение с поддержкой функциональности удаленных систем. Эту функциональность можно добавить в приложение, которое обеспечивает работу службы приложения на целевом устройстве (в этом случае на оба устройства устанавливается одно и то же приложение), или в совершенно другое приложение.

Чтобы код из этого раздела работал в приведенном виде, необходимы следующие операторы **using**:

[!code-cs[Основной блок](./code/RemoteAppService/MainPage.xaml.cs#SnippetUsings)]


Сначала необходимо создать экземпляр объекта [**AppServiceConnection**](https://msdn.microsoft.com/library/windows/apps/Windows.ApplicationModel.AppService.AppServiceConnection), точно так же, как и при локальном вызове службы приложения. Этот процесс подробно описан в разделе [Создание и использование службы приложения](how-to-create-and-consume-an-app-service.md). В этом примере в качестве целевой службы приложения используется служба генератора случайных чисел.

> [!NOTE]
> Предполагается, что объект [RemoteSystem](https://msdn.microsoft.com/library/windows/apps/Windows.System.RemoteSystems.RemoteSystem) уже каким-то образом получен в коде, который будет вызывать следующий метод. Инструкции относительно того, как это сделать, см. в разделе [Запуск удаленного приложения](launch-a-remote-app.md).

[!code-cs[Основной блок](./code/RemoteAppService/MainPage.xaml.cs#SnippetAppService)]

Далее создается объект [**RemoteSystemConnectionRequest**](https://msdn.microsoft.com/library/windows/apps/Windows.System.RemoteSystems.RemoteSystemConnectionRequest) для требуемого удаленного устройства. Затем он используется для открытия **AppServiceConnection** с этим устройством. Обратите внимание, что для краткости в приведенном ниже примере обработка ошибок и отчетность значительно упрощены.

[!code-cs[Основной блок](./code/RemoteAppService/MainPage.xaml.cs#SnippetRemoteConnection)]

На этом этапе у вас должно иметься открытое подключение к службе приложения на удаленном компьютере.

## Обмен специфичными для службы сообщениями по удаленному подключению

Отсюда можно отправлять в службу и получать из службы сообщения в форме объектов [**ValueSet**](https://msdn.microsoft.com/library/windows/apps/windows.foundation.collections.valueset) (дополнительные сведения см. в разделе [Создание и использование службы приложения](how-to-create-and-consume-an-app-service.md)). Служба генератора случайных чисел принимает 2 целых числа с ключами `"minvalue"` и `"maxvalue"` в качестве входных данных, случайным образом выбирает целое число внутри диапазона и возвращает это число в вызывающий процесс с ключом `"Result"`.

[!code-cs[Основной блок](./code/RemoteAppService/MainPage.xaml.cs#SnippetSendMessage)]

Теперь вы подключены к службе приложения на целевом удаленном устройстве, выполнили операцию на этом устройстве и получили данные на домашнем устройстве в ответном сообщении.

## Связанные разделы

[Обзор подключенных приложений и устройств (проект «Рим»)](connected-apps-and-devices.md)  
[Запуск удаленного приложения](launch-a-remote-app.md)  
[Создание и использование службы приложения](how-to-create-and-consume-an-app-service.md)  
[Справочник по API удаленных систем](https://msdn.microsoft.com/library/windows/apps/Windows.System.RemoteSystems)  
[Пример удаленных систем](https://github.com/Microsoft/Windows-universal-samples/tree/dev/Samples/RemoteSystems ) демонстрирует, как обнаружить удаленную систему, запустить приложение в удаленной системе и использовать службы приложений для передачи сообщений между приложениями, работающими в двух системах.



<!--HONumber=Aug16_HO3-->



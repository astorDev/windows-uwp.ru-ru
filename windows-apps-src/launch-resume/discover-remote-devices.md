---
author: PatrickFarley
title: "Обнаружение удаленных устройств"
description: "Узнайте, как обнаруживать удаленные устройства из приложения с помощью проекта &quot;Рим&quot;."
translationtype: Human Translation
ms.sourcegitcommit: ff8e16d0e376d502157ae42b9cdae11875008554
ms.openlocfilehash: cb1f9cf6915378203919fdf63bcebc935af74a30

---

# Обнаружение удаленных устройств
Ваше приложение может использовать беспроводную сеть, Bluetooth и облачное подключение для обнаружения устройств, работающих под управлением Windows, вход с которых выполнен с той же учетной записью Майкрософт, что и у обнаруживающего устройства. Устройства общего пользования, которые могут принимать анонимные подключения (например Surface Hub и Xbox One), также поддерживают обнаружение. Обнаружение удаленных устройств возможно без установки на них какого-либо специального программного обеспечения.

> [!NOTE]
> В этом руководстве предполагается, что вы уже получили доступ к функции удаленных систем, выполнив шаги раздела [Запуск удаленного приложения](launch-a-remote-app.md).

## Фильтрация набора устройств, доступных для обнаружения
Набор доступных для обнаружения устройств можно ограничить, используя [**RemoteSystemWatcher**](https://msdn.microsoft.com/library/windows/apps/Windows.System.RemoteSystems.RemoteSystemWatcher) с фильтрами. Фильтры могут определять тип обнаружения (локальная сеть или облачное подключение), типа устройства (компьютер, мобильное устройство, Xbox, Hub и Holographic) и состояние доступности (состояние доступности устройства для использования функций удаленной системы).

Объекты фильтра должны быть созданы до инициализации объекта **RemoteSystemWatcher**, поскольку они передаются как параметры в его конструктор. Следующий код создает фильтры каждого доступного типа, затем добавляет их в список.

> [!NOTE]
> В коде из этих примеров предполагается, что в вашем файле имеется оператор `using Windows.System.RemoteSystems`.

[!code-cs[Основной блок](./code/DiscoverDevices/MainPage.xaml.cs#SnippetMakeFilterList)]

После создания списка объектов [**IRemoteSystemFilter**](https://msdn.microsoft.com/library/windows/apps/Windows.System.RemoteSystems.IRemoteSystemFilter) его можно передать в конструктор объекта **RemoteSystemWatcher**.

[!code-cs[Основной блок](./code/DiscoverDevices/MainPage.xaml.cs#SnippetCreateWatcher)]

При вызове метода [**Start**](https://msdn.microsoft.com/library/windows/apps/Windows.System.RemoteSystems.RemoteSystemWatcher.Start) этого наблюдателя он инициирует событие [**RemoteSystemAdded**](https://msdn.microsoft.com/library/windows/apps/Windows.System.RemoteSystems.RemoteSystemWatcher.RemoteSystemAdded) только в случае обнаружения устройства, которое соответствует всем указанным ниже критериям.
* Оно может быть обнаружено с помощью подключения близкого радиуса действия
* Это компьютер или телефон
* Оно классифицируется как доступное

С этого момента процедура обработки событий, извлечения объектов [**RemoteSystem**](https://msdn.microsoft.com/library/windows/apps/Windows.System.RemoteSystems.RemoteSystem) и подключения к удаленным устройствам полностью совпадает с процедурой из раздела [Запуск удаленного приложения](launch-a-remote-app.md). Если коротко, объекты **RemoteSystem** хранятся в виде свойств объектов [**RemoteSystemAddedEventArgs**](https://msdn.microsoft.com/library/windows/apps/Windows.System.RemoteSystems.RemoteSystemAddedEventArgs), которые являются параметрами каждого из событий **RemoteSystemAdded**.

## Обнаружение устройств путем ввода адреса
Некоторые устройства могут быть не связаны с пользователем или могут не обнаруживаться сканированием, но с ними все равно можно установить связь, если обнаруживающее приложение использует прямой адрес. Класс [**HostName**](https://msdn.microsoft.com/library/windows/apps/windows.networking.hostname.aspx) используется для представления адреса удаленного устройства. Он часто хранится в виде IP-адреса, но допускается также ряд других форматов (см. раздел [**Конструктор HostName**](https://msdn.microsoft.com/library/windows/apps/br207118.aspx)).

Объект **RemoteSystem** извлекается, если указан действительный объект **HostName**. Если сведения об адресе неправильные, возвращается ссылка на объект `null`.

[!code-cs[Основной блок](./code/DiscoverDevices/MainPage.xaml.cs#SnippetFindByHostName)]

## Связанные разделы
[Подключенные приложения и устройства (проект «Рим»)](connected-apps-and-devices.md)  
[Запуск удаленного приложения](launch-a-remote-app.md)  
[Справочник по API удаленных систем](https://msdn.microsoft.com/library/windows/apps/Windows.System.RemoteSystems)  
[Пример удаленных систем](https://github.com/Microsoft/Windows-universal-samples/tree/dev/Samples/RemoteSystems ) демонстрирует, как обнаружить удаленную систему, запустить приложение в удаленной системе и использовать службы приложений для передачи сообщений между приложениями, работающими в двух системах.



<!--HONumber=Aug16_HO5-->



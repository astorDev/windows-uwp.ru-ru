---
ms.assetid: 4311D293-94F0-4BBD-A22D-F007382B4DB8
title: Перечисление устройств
description: Пространство имен перечисления позволяет обнаружить устройства, которые подключены к системе изнутри, внешним образом или могут быть обнаружены по протоколам проводной или беспроводной сети.
ms.date: 02/08/2017
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: b562dd139705e983bc8a8ad10962d923cff55559
ms.sourcegitcommit: b52ddecccb9e68dbb71695af3078005a2eb78af1
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/20/2019
ms.locfileid: "74259678"
---
# <a name="enumerate-devices"></a>Перечисление устройств


## <a name="samples"></a>Примеры

Самый простой способ перечисления всех доступных устройств — это сделать снимок с помощью команды [**FindAllAsync**](https://docs.microsoft.com/uwp/api/windows.devices.enumeration.deviceinformation.findallasync) (описание приведено в разделе ниже).

```CSharp
async void enumerateSnapshot(){
  DeviceInformationCollection collection = await DeviceInformation.FindAllAsync();
}
```

Чтобы скачать пример, показывающий более сложное применение API [**Windows.Devices.Enumeration**](https://docs.microsoft.com/uwp/api/Windows.Devices.Enumeration), перейдите [по этой ссылке](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/DeviceEnumerationAndPairing).

## <a name="enumeration-apis"></a>Интерфейсы API перечисления

Пространство имен перечисления позволяет обнаружить устройства, которые подключены к системе изнутри, внешним образом или могут быть обнаружены по протоколам проводной или беспроводной сети. Для перечисления возможных устройств используются API пространства имен [**Windows.Devices.Enumeration**](https://docs.microsoft.com/uwp/api/Windows.Devices.Enumeration). Ниже приводятся некоторые случаи, в которых следует использовать эти API.

-   Поиск устройства для подключения с помощью вашего приложения.
-   Получение информации об устройствах, подключенных или обнаруженных системой.
-   Получение приложением уведомлений о добавлении, подключении, отключении, изменении состояния или других свойств устройств.
-   Получение приложением фоновых триггеров о подключении, отключении, изменении состояния или других свойств устройств.

Эти API могут перечислять устройства по любому из следующих протоколов и шин ниже при условии, что конкретное устройство и система, в которой работает приложение, поддерживают соответствующую технологию. Этот список не является исчерпывающим, и конкретное устройство может поддерживать другие протоколы.

-   Физически подключенные шины. К ним относятся PCI и USB. Например, все, что отображается в **диспетчере устройств**.
-   [Входящий](https://docs.microsoft.com/windows/desktop/UPnP/universal-plug-and-play-start-page)
-   Digital Living Network Alliance (DLNA)
-   [**Обнаружение и запуск (DIAL)** ](https://docs.microsoft.com/uwp/api/Windows.Media.DialProtocol)
-   [**Обнаружение службы DNS (DNS-SD)** ](https://docs.microsoft.com/uwp/api/Windows.Networking.ServiceDiscovery.Dnssd)
-   [Веб-службы на устройствах (WSD)](https://docs.microsoft.com/windows/desktop/WsdApi/wsd-portal)
-   [Bluetooth](https://docs.microsoft.com/windows/desktop/Bluetooth/bluetooth-start-page)
-   [**Wi-Fi Direct**](https://docs.microsoft.com/uwp/api/Windows.Devices.WiFiDirect)
-   WiGig
-   [**Точка обслуживания**](https://docs.microsoft.com/uwp/api/Windows.Devices.PointOfService)

Во многих случаях вам незачем беспокоиться об использовании API перечисления. Это связано с тем, что многие интерфейсы API, использующие устройства, автоматически выбирают подходящее устройство по умолчанию или предоставляют упрощенный API перечисления. Например, [**MediaElement**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.MediaElement) автоматически использует устройство обработки звука по умолчанию. Если ваше приложение может использовать устройство по умолчанию, нет необходимости использовать в нем API перечисления. API перечисления предоставляют универсальный гибкий способ обнаружения доступных устройств и соединения с ними. В этом разделе представлены сведения о перечислении устройств и четыре стандартных способа перечисления.

-   Применение пользовательского интерфейса [**DevicePicker**](https://docs.microsoft.com/uwp/api/Windows.Devices.Enumeration.DevicePicker)
-   Перечисление снимка устройств, которые могут быть обнаружены системой в текущий момент
-   Перечисление устройств, которые могут быть обнаружены в текущий момент, и ожидание изменений
-   Перечисление устройств, которые могут быть обнаружены в текущий момент, и ожидание изменений в фоновой задаче

## <a name="deviceinformation-objects"></a>Объекты DeviceInformation


При работе с API перечисления часто приходится использовать объекты [**DeviceInformation**](https://docs.microsoft.com/uwp/api/Windows.Devices.Enumeration.DeviceInformation). Эти объекты содержат большинство доступных сведений об устройстве. В таблице ниже описаны некоторые свойства **DeviceInformation**, которые вам пригодятся. Полный список см. на странице со справочной документацией по **DeviceInformation**.

| Свойство                         | Комментарии                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |
|----------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **DeviceInformation.Id**         | Это уникальный идентификатор устройства в виде строковой переменной. В большинстве случаев вы будете просто передавать это непрозрачное значение от одного метода к другому, чтобы указать конкретное устройство, которое вам необходимо. Это свойство и свойство **DeviceInformation.Kind** также можно использовать после закрытия приложения и его повторного открытия. Это гарантирует, что вы сможете восстановить и повторно использовать тот же самый объект [**DeviceInformation**](https://docs.microsoft.com/uwp/api/Windows.Devices.Enumeration.DeviceInformation). |
| **Девицеинформатион. Kind**       | Указывает вид объекта устройства, представленного объектом [**DeviceInformation**](https://docs.microsoft.com/uwp/api/Windows.Devices.Enumeration.DeviceInformation). Это не категория или тип устройства. Одно устройство может быть представлено несколькими различными объектами **DeviceInformation** разных видов. Возможные значения этого свойства и их взаимосвязи приводятся в разделе [**DeviceInformationKind**](https://docs.microsoft.com/uwp/api/windows.devices.enumeration.deviceinformationkind).                           |
| **Девицеинформатион. Properties** | Этот контейнер свойств содержит сведения, запрашиваемые для объекта [**DeviceInformation**](https://docs.microsoft.com/uwp/api/Windows.Devices.Enumeration.DeviceInformation). На наиболее распространенные свойства можно легко ссылаться как на свойства объекта **DeviceInformation**, например [**DeviceInformation.Name**](https://docs.microsoft.com/uwp/api/windows.devices.enumeration.deviceinformation.name). Дополнительные сведения см. в статье [Свойства сведений об устройстве](device-information-properties.md).                                                                |

 

## <a name="devicepicker-ui"></a>Пользовательский интерфейс DevicePicker


[  **DevicePicker**](https://docs.microsoft.com/uwp/api/Windows.Devices.Enumeration.DevicePicker) — это элемент управления в Windows, который создает небольшой пользовательский интерфейс, чтобы пользователь мог выбирать устройство из списка. Есть несколько способов настройки окна **DevicePicker**.

-   Вы можете управлять устройствами, отображаемыми в этом пользовательском интерфейсе, добавив [**SupportedDeviceSelectors**](https://docs.microsoft.com/uwp/api/windows.devices.enumeration.devicepickerfilter.supporteddeviceselectors), [**SupportedDeviceClasses**](https://docs.microsoft.com/uwp/api/windows.devices.enumeration.devicepickerfilter.supporteddeviceclasses) или оба этих параметра в [**DevicePicker.Filter**](https://docs.microsoft.com/uwp/api/windows.devices.enumeration.devicepicker.filter). В большинстве случаев требуется добавить только один селектор или класс, но если вам нужно больше, вы можете добавить несколько элементов. Если вы добавляете несколько селекторов или классов, они соединяются с помощью функции логики ИЛИ.
-   Вы можете указать свойства устройств, которые требуется получить. Для этого добавьте свойства в [**DevicePicker.RequestedProperties**](https://docs.microsoft.com/uwp/api/windows.devices.enumeration.devicepicker.requestedproperties).
-   Вы можете изменить вид элемента [**DevicePicker**](https://docs.microsoft.com/uwp/api/Windows.Devices.Enumeration.DevicePicker) с помощью свойства [**Appearance**](https://docs.microsoft.com/uwp/api/windows.devices.enumeration.devicepicker.appearance).
-   Вы можете указать размер и расположение элемента [**DevicePicker**](https://docs.microsoft.com/uwp/api/Windows.Devices.Enumeration.DevicePicker).

При отображении элемента [**DevicePicker**](https://docs.microsoft.com/uwp/api/Windows.Devices.Enumeration.DevicePicker) произойдет автоматическое обновление содержимого пользовательского интерфейса, если устройства добавляются, удаляются или обновляются.

**Обратите внимание** ,  нельзя указать [**Девицеинформатионкинд**](https://docs.microsoft.com/uwp/api/windows.devices.enumeration.deviceinformationkind) с помощью [**девицепиккер**](https://docs.microsoft.com/uwp/api/Windows.Devices.Enumeration.DevicePicker). Если вам необходимы устройства определенного вида **DeviceInformationKind**, вам понадобится создать объект [**DeviceWatcher**](https://docs.microsoft.com/uwp/api/Windows.Devices.Enumeration.DeviceWatcher) и предоставить собственный пользовательский интерфейс.

 

Если вы захотите использовать трансляцию мультимедийного содержимого и DIAL, то они также имеют собственные средства выбора. Это [**CastingDevicePicker**](https://docs.microsoft.com/uwp/api/Windows.Media.Casting.CastingDevicePicker) и [**DialDevicePicker**](https://docs.microsoft.com/uwp/api/Windows.Media.DialProtocol.DialDevicePicker), соответственно.

## <a name="enumerate-a-snapshot-of-devices"></a>Перечисление снимка устройств


В некоторых сценариях [**DevicePicker**](https://docs.microsoft.com/uwp/api/Windows.Devices.Enumeration.DevicePicker) может не подойти для ваших потребностей, и вам понадобится что-то более универсальное. Возможно, вы хотите создать собственный пользовательский интерфейс или перечислить устройства без отображения интерфейса для пользователя. В таких случаях можно перечислить снимок устройств. Для этого вы просматриваете устройства, которые в данный момент подключены к системе или связаны с ней. Однако следует помнить, что этот метод анализирует только снимок доступных устройств, поэтому вы не сможете обнаружить устройства, подключаемые после получения перечисления. Вы также не будете получать уведомления об обновлении или удалении устройства. Другим потенциальным недостатком, о котором следует упомянуть, является то, что данный метод удерживает все результаты до полного завершения перечисления. По этой причине не следует использовать данный метод, если требуется получить объекты **AssociationEndpoint**, **AssociationEndpointContainer** или **AssociationEndpointService**, поскольку они определяются по протоколу проводной или беспроводной связи. Этот процесс может занять до 30 секунд. В этом случае для перечисления возможных устройств необходимо использовать объект [**DeviceWatcher**](https://docs.microsoft.com/uwp/api/Windows.Devices.Enumeration.DeviceWatcher).

Чтобы выполнить перечисление посредством снимка устройств, используйте метод [**FindAllAsync**](https://docs.microsoft.com/uwp/api/windows.devices.enumeration.deviceinformation.findallasync). Этот метод ждет, пока весь процесс перечисления не будет завершен, и возвращает все результаты как один объект [**DeviceInformationCollection**](https://docs.microsoft.com/uwp/api/windows.devices.enumeration.deviceinformationcollection). Этот метод также перегружен и имеет несколько параметров для фильтрации результатов и выделения из них только нужных устройств. Для этого нужно предоставить значение [**DeviceClass**](https://docs.microsoft.com/uwp/api/Windows.Devices.Enumeration.DeviceClass) или передать средство выбора устройств. Средство выбора устройств — это строка AQS, определяющая устройства для перечисления. Подробнее: [Создание средства выбора устройств](build-a-device-selector.md).

Ниже приводится пример снимка перечисления устройств:



Помимо ограничения результатов, также можно указать, какие свойства устройств необходимо получить. В этом случае указанные свойства будут доступны в контейнере свойств для каждого из объектов [**DeviceInformation**](https://docs.microsoft.com/uwp/api/Windows.Devices.Enumeration.DeviceInformation), возвращаемых в коллекцию. Важно помнить, что не все свойства доступны для всех видов устройств. Чтобы узнать, какие свойства доступны для тех или иных типов устройств, изучите раздел [Свойства сведений об устройстве](device-information-properties.md).



## <a name="enumerate-and-watch-devices"></a>Перечисление и отслеживание устройств


Более мощным и гибким способом перечисления устройств является создание объекта [**DeviceWatcher**](https://docs.microsoft.com/uwp/api/Windows.Devices.Enumeration.DeviceWatcher). Этот вариант обеспечивает максимальную гибкость при перечислении устройств. Он позволяет перечислять устройства, присутствующие в настоящий момент, а также получать уведомления при добавлении и удалении устройств, которые соответствуют параметрам средства выбора устройств, а также при изменении свойств устройств. При создании **DeviceWatcher** вы предоставляете средство выбора устройств. Подробнее о средствах выбора устройств: [Создание средства выбора устройств](build-a-device-selector.md). После создания наблюдателя вы будете получать следующие уведомления для любого устройства, которое соответствует указанным критериям.

-   Уведомление о добавлении устройства.
-   Уведомление об обновлении, если отслеживаемое свойство изменяется.
-   Уведомление об удалении, если устройство больше недоступно или не соответствует вашему фильтру.

В большинстве случаев использования [**DeviceWatcher**](https://docs.microsoft.com/uwp/api/Windows.Devices.Enumeration.DeviceWatcher) вы храните список устройств и добавляете, удаляете или обновляете его элементы по мере того, как наблюдатель получает обновления от отслеживаемых устройств. При получении уведомления об обновлении измененная информация будет доступна как объект [**DeviceInformationUpdate**](https://docs.microsoft.com/uwp/api/windows.devices.enumeration.deviceinformationupdate). Чтобы обновить список устройств, сначала найдите соответствующий измененный объект [**DeviceInformation**](https://docs.microsoft.com/uwp/api/Windows.Devices.Enumeration.DeviceInformation). Затем вызовите метод [**Update**](https://docs.microsoft.com/uwp/api/windows.devices.enumeration.deviceinformation.update) для этого объекта, предоставив объект **DeviceInformationUpdate**. Это удобная функция, которая автоматически обновляет объект **DeviceInformation**.

Поскольку [**DeviceWatcher**](https://docs.microsoft.com/uwp/api/Windows.Devices.Enumeration.DeviceWatcher) рассылает уведомления при добавлении и изменении устройств, рекомендуется использовать этот метод перечисления устройств, если вас интересуют объекты **AssociationEndpoint**, **AssociationEndpointContainer** или **AssociationEndpointService**, так как они перечисляются по сетевым или беспроводным протоколам.

Для создания объекта [**DeviceWatcher**](https://docs.microsoft.com/uwp/api/Windows.Devices.Enumeration.DeviceWatcher) используйте один из методов [**CreateWatcher**](https://docs.microsoft.com/uwp/api/windows.devices.enumeration.deviceinformation.createwatcher). Эти методы перегружены, что предоставляет возможность указать требуемые устройства. Для этого нужно предоставить значение [**DeviceClass**](https://docs.microsoft.com/uwp/api/Windows.Devices.Enumeration.DeviceClass) или передать средство выбора устройств. Средство выбора устройств — это строка AQS, определяющая устройства для перечисления. Подробнее: [Создание средства выбора устройств](build-a-device-selector.md). Вы также можете задать свойства устройств, которые требуется получить. В этом случае указанные свойства будут доступны в контейнере свойств для каждого из объектов [**DeviceInformation**](https://docs.microsoft.com/uwp/api/Windows.Devices.Enumeration.DeviceInformation), возвращаемых в коллекцию. Важно помнить, что не все свойства доступны для всех видов устройств. Чтобы узнать, какие свойства доступны для тех или иных типов устройств, изучите раздел [Свойства сведений об устройстве](device-information-properties.md).

## <a name="watch-devices-as-a-background-task"></a>Наблюдение за устройствами как фоновая задача


Отслеживание устройств в фоновой задаче очень похоже на создание [**DeviceWatcher**](https://docs.microsoft.com/uwp/api/Windows.Devices.Enumeration.DeviceWatcher), рассмотренное выше. К слову, вам все равно придется сначала создать обычный объект **DeviceWatcher**, как описано в предыдущем разделе. После его создания вызовите [**GetBackgroundTrigger**](https://docs.microsoft.com/uwp/api/windows.devices.enumeration.devicewatcher.enumerationcompleted) вместо [**DeviceWatcher.Start**](https://docs.microsoft.com/uwp/api/windows.devices.enumeration.devicewatcher.start). При вызове метода **GetBackgroundTrigger** необходимо определить, какие из уведомлений вас интересуют: уведомления о добавлении, удалении или обновлении. Невозможно запросить обновление или удаление, не запросив также и добавление. После регистрации триггера объект **DeviceWatcher** сразу же начнет свою работу в фоновом режиме. С этого момента при получении нового уведомления для вашего приложения, которое соответствует заданным условиям, активируется фоновая задача, которая предоставит вам последние изменения с момента последней активации вашего приложения.

**Важно**  при первом запуске [**девицеватчертригжер**](https://docs.microsoft.com/uwp/api/Windows.ApplicationModel.Background.DeviceWatcherTrigger) приложением, когда наблюдатель достигнет состояния **енумератионкомплетед** . Это означает, что он будет содержать все исходные результаты. При последующей активации приложения он будет содержать только уведомления о добавлении, обновлении и удалении, полученные с момента последней активации. Это немного отличается от объекта переднего плана [**DeviceWatcher**](https://docs.microsoft.com/uwp/api/Windows.Devices.Enumeration.DeviceWatcher), так как исходные результаты поступают не по одному, а только пакетом после перехода в состояние **EnumerationCompleted**.

 

Некоторые беспроводные протоколы ведут себя по-другому, если ведут сканирование не в приоритетном, а фоновом режиме; они также могут совсем не поддерживать сканирование в фоновом режиме. Существует три варианта относительно возможности фонового сканирования. В следующей таблице перечислены возможности и их последствия для приложения. Например, Bluetooth и Wi-Fi Direct не поддерживают фоновое сканирование, поэтому они не поддерживают [**DeviceWatcherTrigger**](https://docs.microsoft.com/uwp/api/Windows.ApplicationModel.Background.DeviceWatcherTrigger).

| Поведение                                  | Влияние                                                                                                                                  |
|-------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------|
| Такое же поведение в фоновом режиме               | Нет                                                                                                                                    |
| В фоновом режиме возможно только пассивное сканирование | Для обнаружения устройства может потребоваться больше времени из-за ожидания пассивного сканирования.                                                           |
| Фоновое сканирование не поддерживается            | С помощью [**DeviceWatcherTrigger**](https://docs.microsoft.com/uwp/api/Windows.ApplicationModel.Background.DeviceWatcherTrigger) не будут обнаруживаться никакие устройства, уведомления об обновлении не будут отправляться. |

 

Если [**DeviceWatcherTrigger**](https://docs.microsoft.com/uwp/api/Windows.ApplicationModel.Background.DeviceWatcherTrigger) использует протокол, который не поддерживает сканирование в качестве фоновой задачи, ваш триггер все же будет работать. Однако вы не сможете получать обновления или результаты по этому протоколу. Обновления для других протоколов и устройств будут по-прежнему работать нормально.

## <a name="using-deviceinformationkind"></a>Использование DeviceInformationKind


В большинстве сценариев вам незачем беспокоиться о виде [**DeviceInformationKind**](https://docs.microsoft.com/uwp/api/windows.devices.enumeration.deviceinformationkind) объекта [**DeviceInformation**](https://docs.microsoft.com/uwp/api/Windows.Devices.Enumeration.DeviceInformation). Это связано с тем, что средство выбора устройств, возвращаемое используемым API устройства, во многих случаях гарантирует получение правильных видов объектов устройств для использования с соответствующими API. Однако в некоторых сценариях вам может потребоваться получить **DeviceInformation** для устройств, притом что соответствующий API устройства для предоставления средства выбора устройств отсутствует. В этом случае вам потребуется создать собственное средство выбора. Например, у Web Services on Devices нет специализированного API, но вы можете обнаружить эти устройства и получить информацию о них с помощью API [**Windows.Devices.Enumeration**](https://docs.microsoft.com/uwp/api/Windows.Devices.Enumeration), а затем использовать их с помощью API сокетов.

Если вы создаете собственное средство выбора устройств для перечисления объектов устройств, необходимо тщательно изучить [**DeviceInformationKind**](https://docs.microsoft.com/uwp/api/windows.devices.enumeration.deviceinformationkind). Все возможные виды, а также их взаимосвязи описаны на странице справки для **DeviceInformationKind**. Один из самых распространенных способов использования **DeviceInformationKind** — определение вида устройств, которые вы ищете, при отправке запроса вместе со средством выбора устройств. Таким образом вы будете уверены, что перечисляете только те устройства, которые соответствуют указанному виду **DeviceInformationKind**. Например, вы можете найти объект **DeviceInterface**, а затем выполнить запрос, чтобы получить сведения для родительского объекта **Device**. Этот родительский объект может содержать дополнительные сведения.

Важно отметить, что свойства, доступные в контейнере свойств объекта [**DeviceInformation**](https://docs.microsoft.com/uwp/api/Windows.Devices.Enumeration.DeviceInformation), могут отличаться в зависимости от значения [**DeviceInformationKind**](https://docs.microsoft.com/uwp/api/windows.devices.enumeration.deviceinformationkind) устройства. Некоторые свойства доступны только для определенных видов. Подробнее о том, какие свойства доступны для каких видов, см. в разделе [Свойства сведений об устройстве](device-information-properties.md). Следовательно, в примере выше поиск родительского элемента **Device** даст доступ к дополнительным сведениям, которые не были доступны из объекта устройства **DeviceInterface**. Поэтому когда вы создаете строки фильтра AQS, запрошенные свойства должны быть доступны для перечисляемых объектов **DeviceInformationKind**. Подробнее о создании фильтра: [Создание средства выбора устройств](build-a-device-selector.md).

Перечисление объектов **AssociationEndpoint**, **AssociationEndpointContainer** или **AssociationEndpointService** происходит по сетевому или беспроводному протоколу. В таких случаях вместо [**FindAllAsync**](https://docs.microsoft.com/uwp/api/windows.devices.enumeration.deviceinformation.findallasync) рекомендуется использовать [**CreateWatcher**](https://docs.microsoft.com/uwp/api/windows.devices.enumeration.deviceinformation.createwatcher). Дело в том, что при поиске по сети время ожидания операций поиска может достигать 10 и более секунд, прежде чем будет получено событие [**EnumerationCompleted**](https://docs.microsoft.com/uwp/api/windows.devices.enumeration.devicewatcher.enumerationcompleted). **FindAllAsync** будет работать до тех пор, пока не активируется событие **EnumerationCompleted**. Если вы используете [**DeviceWatcher**](https://docs.microsoft.com/uwp/api/Windows.Devices.Enumeration.DeviceWatcher), вы получите результаты, которые будут ближе к реальному времени, вне зависимости от того, когда вызывается событие **EnumerationCompleted**.

## <a name="save-a-device-for-later-use"></a>Сохранение устройства для использования в будущем


Любой объект [**DeviceInformation**](https://docs.microsoft.com/uwp/api/Windows.Devices.Enumeration.DeviceInformation) однозначно определяется сочетанием двух параметров: [**DeviceInformation.Id**](https://docs.microsoft.com/uwp/api/windows.devices.enumeration.deviceinformation.id) и [**DeviceInformation.Kind**](https://docs.microsoft.com/uwp/api/windows.devices.enumeration.deviceinformation.kind). Если сохранить эти два значения, то можно будет воссоздать утерянный объект **DeviceInformation**, передав их в метод [**CreateFromIdAsync**](https://docs.microsoft.com/uwp/api/windows.devices.enumeration.deviceinformation.createfromidasync). В этом случае вы сможете сохранить настройки пользователя для устройства, которое интегрируется с вашим приложением.


 

 





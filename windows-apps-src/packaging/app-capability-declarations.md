---
ms.assetid: 25B18BA5-E584-4537-9F19-BB2C8C52DFE1
title: Объявления возможностей приложения
description: Возможности должны быть заявлены в манифесте пакета приложения универсальной платформы Windows (UWP) для получения доступа к определенным API или ресурсам, например, изображениям, музыке или устройствам, в частности, камере или микрофону.
---
# Объявление возможностей приложения

\[ Обновлено для приложений UWP в Windows 10. Статьи о Windows 8.x см. в [архиве](http://go.microsoft.com/fwlink/p/?linkid=619132) \]

Возможности должны быть заявлены в [манифесте пакета](https://msdn.microsoft.com/library/windows/apps/BR211474) приложения UWP для получения доступа к определенным API или ресурсам, например, изображениям, музыке или устройствам, в частности, камере или микрофону.

Доступ к определенным ресурсам или API запрашивается при помощи объявления возможностей в [манифесте пакета](https://msdn.microsoft.com/library/windows/apps/BR211474) приложения. Общие возможности можно объявлять с помощью [Конструктора манифестов](https://msdn.microsoft.com/en-us/library/windows/apps/xaml/br230259.aspx) в Microsoft Visual Studio или добавлять вручную. Подробнее: [Указание возможностей в манифесте пакета](https://msdn.microsoft.com/library/windows/apps/BR211477). Не забывайте, что когда пользователи получают ваше приложение из Магазина, они уведомляются обо всех объявленных возможностях приложения. Поэтому не стоит объявлять возможности, которые вашему приложению не требуются.

Некоторые возможности обеспечивают приложениям доступ к *конфиденциальному ресурсу*. Эти ресурсы считаются конфиденциальными, так как они могут получать доступ к личным данным пользователя или стоить пользователю денег. Параметры конфиденциальности, управляемые приложением "Параметры", позволяют пользователю динамически управлять доступом к конфиденциальным ресурсам. Таким образом, важно, чтобы приложение не считало конфиденциальный ресурс всегда доступным. Подробнее о доступе к конфиденциальным ресурсам см. в разделе [Руководство по приложениям, учитывающим требования конфиденциальности](https://msdn.microsoft.com/library/windows/apps/Hh768223). Возможности, которые предоставляют приложения с доступом к *конфиденциальным ресурсам*, помечены звездочкой (\*) около сценария возможности.

В этой статье рассматриваются четыре категории возможностей, описанные ниже.

-   Возможности общего применения, которые используются в большей части сценариев приложений.

-   Возможности устройства, которые позволяют вашему приложению получить доступ к периферийным и внутренним устройствам.

-   Особые возможности, для использования которых приложение необходимо отправить в Магазин из особой учетной записи компании. Подробнее об учетных записях компаний см. в разделе [Типы, доступность и стоимость учетных записей](https://msdn.microsoft.com/library/windows/apps/JJ863494).

-   Ограниченные возможности, доступные только компании Майкрософт и ее партнерам.

## Возможности общего применения

Возможности общего применения используются в большей части сценариев приложений.

<table>
        <thead>
            <tr>
                <th>Сценарий возможности</th>
                <th>Использование возможности</th>
            </tr>
        </thead>
        <tbody>
            <tr>
                <td>**Музыка***</td>
                <td>
                    The **musicLibrary** capability provides programmatic access to the user's Music, allowing the app to enumerate and access all files in the library without user interaction. This capability is typically used in jukebox apps that make use of the entire Music library.

                    The [**file picker**](https://msdn.microsoft.com/library/windows/apps/BR207847) provides a robust UI mechanism that lets users open files for use with an app. Declare the **musicLibrary** capability only when the scenarios for your app require programmatic access and can't be realized by using the **file picker**.

                    The **musicLibrary** capability must include the **uap** namespace when you declare it in your app's package manifest as shown below.
                    <table>
                        <thead>
                            <tr>
                                <th>XML</th>
                            </tr>
                        </thead>
                        <tbody>
                            <tr>
                                <td>
                                    <pre><code>&lt;Capabilities&gt;
    &lt;uap:Capability Name="musicLibrary"/&gt;
&lt;/Capabilities&gt;</code></pre>
                                </td>
                            </tr>
                        </tbody>
                    </table>
                </td>
            </tr>
            <tr>
                <td>**Изображения***</td>
                <td>
                    Возможность **picturesLibrary** обеспечивает программный доступ к изображениям пользователя, позволяя приложению перечислять все файлы в библиотеке и обращаться к ним без участия пользователя. Обычно данная возможность используется в приложениях для работы с фотографиями, которым необходим доступ ко всей библиотеке изображений.

                    The [**file picker**](https://msdn.microsoft.com/library/windows/apps/BR207847) provides a robust UI mechanism that lets users open files for use with an app. Declare the **picturesLibrary** capability only when the scenarios for your app require programmatic access and can't be realized them by using the **file picker**.

                    The **picturesLibrary** capability must include the **uap** namespace when you declare it in your app's package manifest as shown below.
                    <table>
                        <thead>
                            <tr>
                                <th>XML</th>
                            </tr>
                        </thead>
                        <tbody>
                            <tr>
                                <td>
<pre><code>&lt;Capabilities&gt;
    &lt;uap:Capability Name="picturesLibrary"/&gt;
&lt;/Capabilities&gt;</code></pre>
                                </td>
                            </tr>
                        </tbody>
                    </table>
                </td>
            </tr>
            <tr>
                <td>**Видео***</td>
                <td>
                    The **videosLibrary** capability provides programmatic access to the user's Videos, allowing the app to enumerate and access all files in the library without user interaction. This capability is typically used in movie-playback apps that make use of the entire Videos library.

                    The [**file picker**](https://msdn.microsoft.com/library/windows/apps/BR207847) provides a robust UI mechanism that lets users open files for use with an app. Declare the **videosLibrary** capability only when the scenarios for your app require programmatic access and can't be realized by using the **file picker**.

                    The **videosLibrary** capability must include the **uap** namespace when you declare it in your app's package manifest as shown below.
                    <table>
                        <thead>
                            <tr>
                                <th>XML</th>
                            </tr>
                        </thead>
                        <tbody>
                            <tr>
                                <td>
<pre><code>&lt;Capabilities&gt;
    &lt;uap:Capability Name="videosLibrary"/&gt;
&lt;/Capabilities&gt;</code></pre>
                                </td>
                            </tr>
                        </tbody>
                    </table>
                </td>
            </tr>
            <tr>
                <td>**Съемные носители**</td>
                <td>
                    The **removableStorage** capability provides programmatic access to files on removable storage, like USB keys and external hard drives, filtered to the file-type associations declared in the package manifest. For example, if a document-reader app declares a .doc file-type association, it can open .doc files on the removable storage device, but not other types of files. Be careful when you declare this capability, because users may include a variety of info in their removable storage devices, and will expect your app to provide a valid justification for programmatic access to the removable storage for all files of the declared type.

                    Users will expect your app to handle any file associations that you declare. So don't declare file associations that your app cannot handle responsibly. The [**file picker**](https://msdn.microsoft.com/library/windows/apps/BR207847) provides a robust UI mechanism that lets users open files for use with an app.

                    Declare the **removableStorage** capability only when the scenarios for your app require programmatic access and can't be realized by using the [**file picker**](https://msdn.microsoft.com/library/windows/apps/BR207847).

                    The **removableStorage** capability must include the **uap** namespace when you declare it in your app's package manifest as shown below.
                    <table>
                        <thead>
                            <tr>
                                <th>XML</th>
                            </tr>
                        </thead>
                        <tbody>
                            <tr>
                                <td>
<pre><code>&lt;Capabilities&gt;
    &lt;uap:Capability Name="removableStorage"/&gt;
&lt;/Capabilities&gt;</code></pre>
                                </td>
                            </tr>
                        </tbody>
                    </table>
                </td>
            </tr>
            <tr>
                <td>**Интернет и общедоступные сети***</td>
                <td>
                    There are two capabilities that provide different levels of access to the Internet and public networks.

                    The **internetClient** capability indicates that apps can receive incoming data from the Internet. Cannot act as a server. No local network access.

                    The **internetClientServer** capability indicates that apps can receive incoming data from the Internet. Can act as a server. No local network access.

                    Most apps that have a web service component will use **internetClient**. Apps that enable peer-to-peer (P2P) scenarios where the app needs to listen for incoming network connections should use **internetClientServer**. The **internetClientServer** capability includes the access that the **internetClient** capability provides, so you don't need to specify **internetClient** when you specify **internetClientServer**.
                </td>
            </tr>
            <tr>
                <td>**Homes and work networks***</td>
                <td>
                    The **privateNetworkClientServer** capability provides inbound and outbound access to home and work networks through the firewall. This capability is typically used for games that communicate across the local area network (LAN), and for apps that share data across a variety of local devices. If your app specifies **musicLibrary**, **picturesLibrary**, or **videosLibrary**, you don't need to use this capability to access the corresponding library in a Home Group. On Windows, this capability does not provide access to the Internet.
                </td>
            </tr>
            <tr>
                <td>**Appointments**</td>
                <td>
                    The **appointments** capability provides access to the user’s appointment store. This capability allows read access to appointments obtained from the synced network accounts and to other apps that write to the appointment store. With this capability, your app can create new calendars and write appointments to calendars that it creates.

                    The **appointments** capability must include the **uap** namespace when you declare it in your app's package manifest as shown below.
                    <table>
                        <thead>
                            <tr>
                                <th>XML</th>
                            </tr>
                        </thead>
                        <tbody>
                            <tr>
                                <td>
<pre><code>&lt;Capabilities&gt;
    &lt;uap:Capability Name="appointments"/&gt;
&lt;/Capabilities&gt;</code></pre>
                                </td>
                            </tr>
                        </tbody>
                    </table>
                </td>
            </tr>
            <tr>
                <td>**Контакты***</td>
                <td>
                    The **contacts** capability provides access to the aggregated view of the contacts from various contacts stores. This capability gives the app limited access (network permitting rules apply) to contacts that were synced from various networks and the local contact store.

                    The **contacts** capability must include the **uap** namespace when you declare it in your app's package manifest as shown below.
                    <table>
                        <thead>
                            <tr>
                                <th>XML</th>
                            </tr>
                        </thead>
                        <tbody>
                            <tr>
                                <td>
<pre><code>&lt;Capabilities&gt;
    &lt;uap:Capability Name="contacts"/&gt;
&lt;/Capabilities&gt;</code></pre>
                                </td>
                            </tr>
                        </tbody>
                    </table>
                </td>
            </tr>
            <tr>
                <td>**Создание кода**</td>
                <td>
                    The **codeGeneration** capability allows apps to access the following functions which provide JIT capabilities to apps.

                    - [**VirtualProtectFromApp**](https://msdn.microsoft.com/library/windows/desktop/Mt169846)
                    - [**CreateFileMappingFromApp**](https://msdn.microsoft.com/library/windows/desktop/Hh994453)
                    - [**OpenFileMappingFromApp**](https://msdn.microsoft.com/library/windows/desktop/Mt169844)
                    - [**MapViewOfFileFromApp**](https://msdn.microsoft.com/library/windows/desktop/Hh994454)
                </td>
            </tr>
            <tr>
                <td>**AllJoyn**</td>
                <td>
                    The **allJoyn** capability allows AllJoyn-enabled apps and devices on a network to discover and interact with each other.

                    All apps that access APIs in the [**Windows.Devices.AllJoyn**](https://msdn.microsoft.com/library/windows/apps/Dn894971) namespace must use this capability.
                </td>
            </tr>
            <tr>
                <td>**Phone calls**</td>
                <td>
                    The **phoneCall** capability allows apps to access all of the phone lines on the device and perform the following functions.

                    - Place a call on the phone line and show the system dialer without prompting the user.
                    - Access line-related metadata.
                    - Access line-related triggers.
                    - Allows the user-selected spam filter app to set and check block list and call origin information.

                    The **phoneCall** capability must include the **uap** namespace when you declare it in your app's package manifest as shown below.
                    <table>
                        <thead>
                            <tr>
                                <th>XML</th>
                            </tr>
                        </thead>
                        <tbody>
                            <tr>
                                <td>
<pre><code>&lt;Capabilities&gt;
    &lt;uap:Capability Name="phoneCall"/&gt;
&lt;/Capabilities&gt;</code></pre>
                                </td>
                            </tr>
                        </tbody>
                    </table>
                    The **phoneCallHistoryPublic** capability allows apps to read cellular and some VOIP call history information on the device. This capability also allows the app to write VOIP call history entries. This capability is required to access all members of the [**PhoneCallHistoryStore**](https://msdn.microsoft.com/library/windows/apps/Dn705931) class.
                </td>
            </tr>
            <tr>
                <td>**Папка записанных вызовов***</td>
                <td>
                    <p>Возможность устройства **recordedCallsFolder** предоставляет приложениям доступ к папке записанных вызовов.</p>
                    <p>Возможность **recordedCallsFolder** должна содержать пространство имен **mobile** при объявлении ее в манифесте пакета приложения, как показано ниже.</p>
                    <table>
                        <thead>
                            <tr>
                                <th>XML</th>
                            </tr>
                        </thead>
                        <tbody>
                            <tr>
                                <td>
                                    <pre><code>&lt;Capabilities&gt;
    &lt;mobile:Capability Name="recordedCallsFolder"/&gt;
&lt;/Capabilities&gt;</code></pre>
                                </td>
                            </tr>
                        </tbody>
                    </table>
                </td>
            </tr>
            <tr>
                <td>**Сведения об учетной записи пользователя***</td>
                <td>
                    <p>Возможность **userAccountInformation** позволяет приложениям получать доступ к имени и изображению пользователя.</p>
                    <p>Эта возможность требуется для доступа к некоторым API в пространстве имен Windows.System.User.</p>
                    <p>Возможность **userAccountInformation** должна содержать пространство имен **uap** при объявлении ее в манифесте пакета приложения, как показано ниже.</p>
                    <table>
                        <colgroup>
                            <col width="100%" />
                        </colgroup>
                        <thead>
                            <tr>
                                <th>XML</th>
                            </tr>
                        </thead>
                        <tbody>
                            <tr>
                                <td>
                                    <pre><code>&lt;Capabilities&gt;
    &lt;uap:Capability Name="userAccountInformation"/&gt;
&lt;/Capabilities&gt;</code></pre>
                                </td>
                            </tr>
                        </tbody>
                    </table>
                </td>
            </tr>
            <tr>
                <td>**Звонки VOIP**</td>
                <td>
                    <p>Возможность **voipCall** предоставляет приложениям доступ к вызову API по протоколу VoIP в пространстве имен [**Windows.ApplicationModel.Calls**](https://msdn.microsoft.com/library/windows/apps/Dn297266).</p>
                    <p>Возможность **voipCall** должна содержать пространство имен **uap** при объявлении ее в манифесте пакета приложения, как показано ниже.</p>
                    <table>
                        <colgroup>
                            <col width="100%" />
                        </colgroup>
                        <thead>
                            <tr>
                                <th>XML</th>
                            </tr>
                        </thead>
                        <tbody>
                            <tr>
                                <td>
                                    <pre><code>&lt;Capabilities&gt;
    &lt;uap:Capability Name="voipCall"/&gt;
&lt;/Capabilities&gt;</code></pre>
                                </td>
                            </tr>
                        </tbody>
                    </table>
                </td>
            </tr>
            <tr>
                <td>**Трехмерные объекты**</td>
                <td>
                    <p>Возможность **objects3D** позволяет приложениям программным способом получать доступ к файлам трехмерных объектов. Обычно данная возможность используется в приложениях и играх, работающих с трехмерной графикой, которым необходим доступ ко всей библиотеке трехмерных объектов.</p>
                    <p>Эта возможность необходима для доступа к папке, содержащей трехмерные объекты, с помощью API в пространстве имен [**Windows.Storage**](https://msdn.microsoft.com/library/windows/apps/BR227346).</p>
                    <p>Возможность **objects3D** должна содержать пространство имен **uap** при объявлении ее в манифесте пакета приложения, как показано ниже.</p>
                    <table>
                        <colgroup>
                            <col width="100%" />
                        </colgroup>
                        <thead>
                            <tr>
                                <th>XML</th>
                            </tr>
                        </thead>
                        <tbody>
                            <tr>
                                <td>
                                    <pre><code>&lt;Capabilities&gt;
    &lt;uap:Capability Name="objects3d"/&gt;
&lt;/Capabilities&gt;</code></pre>
                                </td>
                            </tr>
                        </tbody>
                    </table>
                </td>
            </tr>
            <tr>
                <td>**Читать заблокированные сообщения***</td>
                <td>
                    <p>Возможность **blockedChatMessages** позволяет приложениям читать SMS- и MMS-сообщения, заблокированные приложением фильтра нежелательной почты.</p>
                    <p>Эта возможность необходима для доступа к заблокированным сообщениям с помощью API в пространстве имен [**Windows.ApplicationModel.Chat**](https://msdn.microsoft.com/library/windows/apps/Dn642321).</p>
                    <p>Возможность **blockedChatMessages** должна содержать пространство имен **uap** при объявлении ее в манифесте пакета приложения, как показано ниже.</p>
                    <table>
                        <colgroup>
                            <col width="100%" />
                        </colgroup>
                        <thead>
                            <tr>
                                <th>XML</th>
                            </tr>
                        </thead>
                        <tbody>
                            <tr>
                                <td>
                                    <pre><code>&lt;Capabilities&gt;
    &lt;uap:Capability Name="blockedChatMessages"/&gt;
&lt;/Capabilities&gt;</code></pre>
                                </td>
                            </tr>
                        </tbody>
                    </table>
                </td>
            </tr>
            <tr>
                <td>**Доступ к сообщениям чата**</td>
                <td>
                    <p>Возможность **chat** позволяет приложениям читать и удалять текстовые сообщения. Эта возможность также позволяет приложениям хранить сообщения чата в хранилище системных данных.</p>
                    <p>Эта возможность требуется для использования некоторых API в пространстве имен [**Windows.ApplicationModel.Chat**](https://msdn.microsoft.com/library/windows/apps/Dn642321).</p>
                    <p>Возможность **chat** должна содержать пространство имен **uap** при объявлении ее в манифесте пакета приложения, как показано ниже.</p>
                    <table>
                        <colgroup>
                            <col width="100%" />
                        </colgroup>
                        <thead>
                            <tr>
                                <th>XML</th>
                            </tr>
                        </thead>
                        <tbody>
                            <tr>
                                <td>
                                    <pre><code>&lt;Capabilities&gt;
    &lt;uap:Capability Name="chat"/&gt;
&lt;/Capabilities&gt;</code></pre>
                                </td>
                            </tr>
                        </tbody>
                    </table>
                </td>
            </tr>
            <tr>
                <td>**Оборудование шины нижнего уровня IoT**</td>
                <td>
                    <p>Возможность **lowLevelDevices** позволяет приложениям, запущенным на устройствах IoT, получать доступ к оборудованию шины нижнего уровня, например, GPIO, I2C, SPI, ADC и PWM.</p>
                    <p>Эта возможность требуется для доступа к некоторым API в пространствах имен [**Windows.Devices.Spi**](https://msdn.microsoft.com/library/windows/apps/Dn708178).</p>
                    <p>Возможность **lowLevelDevices** должна содержать пространство имен **iot** при объявлении ее в манифесте пакета приложения, как показано ниже.</p>
                    <table>
                        <colgroup>
                            <col width="100%" />
                        </colgroup>
                        <thead>
                            <tr>
                                <th>XML</th>
                            </tr>
                        </thead>
                        <tbody>
                            <tr>
                                <td>
                                    <pre><code>&lt;Capabilities&gt;
    &lt;iot:Capability Name="lowLevelDevices"/&gt;
&lt;/Capabilities&gt;</code></pre>
                                </td>
                            </tr>
                        </tbody>
                    </table>
                </td>
            </tr>
            <tr>
                <td>**Администрирование системы IoT**</td>
                <td>
                    <p>Возможность **systemManagement** позволяет приложениям иметь базовые права на администрирование системы, в частности, на завершение работы или перезагрузку, изменение языкового стандарта и часового пояса.</p>
                    <p>Эта возможность требуется для доступа к некоторым API в пространстве имен [**Windows.System**](https://msdn.microsoft.com/library/windows/apps/BR241814).</p>
                    <p>Возможность **systemManagement** должна содержать пространство имен **iot** при объявлении ее в манифесте пакета приложения, как показано ниже.</p>
                    <table>
                        <colgroup>
                            <col width="100%" />
                        </colgroup>
                        <thead>
                            <tr>
                                <th>XML</th>
                            </tr>
                        </thead>
                        <tbody>
                            <tr>
                                <td>
                                    <pre><code>&lt;Capabilities&gt;
    &lt;iot:Capability Name="systemManagement"/&gt;
&lt;/Capabilities&gt;</code></pre>
                                </td>
                            </tr>
                        </tbody>
                    </table>
                </td>
            </tr>
        </tbody>
</table>

 
## Возможности устройств

Возможности устройств позволяют вашему приложению получить доступ к периферийным и внутренним устройствам. Возможности устройства указываются с помощью элемента **DeviceCapability** в манифесте пакета приложения. Этот элемент может требовать дополнительных дочерних элементов, а некоторые возможности устройства необходимо добавлять в манифест пакета вручную. Дополнительные сведения см. в разделах [Определение возможностей устройств в манифесте пакета](https://msdn.microsoft.com/library/windows/apps/Dn263092) и [**Справочник по схеме DeviceCapability**](https://msdn.microsoft.com/library/windows/apps/BR211430).

| Сценарий возможности | Использование возможности |
|---------------------|------------------|
| **Расположение**\* | Возможность **location** предоставляет доступ к функции определения расположения, которая поступает со специального оборудования (например, с датчика GPS в компьютере) или использует сведения из доступной сети. Приложения должны обрабатывать ситуации, когда пользователь отключает услуги по определению расположения с помощью чудо-кнопки **Параметры**. |
| **Микрофон** | Возможность **microphone** предоставляет доступ к аудиоканалу микрофона, что позволяет приложению осуществлять запись с подключенных микрофонов. Приложения должны обрабатывать ситуации, когда пользователь отключает микрофон с помощью чудо-кнопки **Параметры**. |
| **Близкое взаимодействие** | Возможность **proximity** позволяет нескольким близко расположенным устройствам взаимодействовать друг с другом. Данная характеристика обычно используется в казуальных многопользовательских играх, а также в приложениях, обменивающихся информацией. Устройства стремятся использовать технологии связи, предоставляющие наилучшее из возможных подключений, включая Bluetooth, Wi-Fi и Интернет. Эта возможность используется только для установки связи между устройствами. |
| **Веб-камера** | Возможность **webcam** предоставляет доступ к видеоканалу встроенной камеры или внешней веб-камеры, благодаря чему приложение может захватывать фотографии и видео. В Windows приложения должны справляться с ситуациями, когда пользователь отключает камеру с помощью чудо-кнопки **Параметры**.<br/>Возможность **webcam** предоставляет доступ только к потоку видео. Для предоставления доступа и к потоку аудио, нужно добавить возможность **microphone**. |
| **USB** | Возможность устройства **usb** разрешает доступ к API в пространстве имен Windows.Devices.Usb. Дополнительные сведения см. в разделе [Обновление пакета манифеста приложения для устройства USB](http://go.microsoft.com/fwlink/p/?LinkId=302259). |
| **Устройство HID** | Возможность устройства **humaninterfacedevice** разрешает доступ к API в пространстве имен Windows.Devices.HumanInterfaceDevice. Подробнее: [Определение возможностей устройств для HID.](https://msdn.microsoft.com/library/windows/apps/Dn263091). |
| **POS-терминал (POS)** | Возможность устройства **pointOfService** разрешает доступ к API в пространстве имен [**Windows.Devices.PointOfService**](https://msdn.microsoft.com/library/windows/apps/Dn298071). Это пространство имен обеспечивает доступ к сканерам штрихкодов и считывателям магнитных карт на терминалах точек обслуживания (POS). Пространство имен предоставляет независимый от поставщика интерфейс для доступа из приложения Магазина Windows к POS-устройствам разных изготовителей. |
| **Bluetooth** | Возможность устройства **bluetooth** позволяет приложениям обмениваться данными со связанными Bluetooth-устройствами по протоколам Generic Attribute (GATT) и Classic Basic Rate (RFCOMM).<br/>Эта возможность требуется для использования некоторых API в пространстве имен [**Windows.Devices.Bluetooth**](https://msdn.microsoft.com/library/windows/apps/Dn263413). |
| **Сети Wi-Fi** | Возможность устройства **wiFiControl** позволяет приложениям сканировать сети Wi-Fi и подключаться к ним.<br/>Эта возможность требуется для использования некоторых API в пространстве имен [**Windows.Devices.WiFi**](https://msdn.microsoft.com/library/windows/apps/Dn975224). |
| **Состояние радиоканала** | Возможность устройства **radios** позволяет приложениям переключать радиосвязь Wi-Fi и радиомодуль Bluetooth.<br/>Эта возможность требуется для использования некоторых API в пространстве имен [**Windows.Devices.Radios**](https://msdn.microsoft.com/library/windows/apps/Dn996447).  |
| **Оптический диск** | Возможность устройства **optical** предоставляет приложениям доступ к функциям на оптических дисках, таких как CD, DVD и Blu-ray.<br/>Эта возможность требуется для использования некоторых API в пространстве имен [**Windows.Devices.Custom**](https://msdn.microsoft.com/library/windows/apps/Dn263667). |
| **Активность движения** | Возможность устройства **activity** позволяет приложениям обнаруживать текущее перемещение устройства.<br/>Эта возможность требуется для использования некоторых API в пространстве имен [**Windows.Devices.Sensors**](https://msdn.microsoft.com/library/windows/apps/BR206408). |

## Особые и ограниченные возможности

**Важно**  
Особые и ограниченные возможности предназначены для конкретных сценариев. Использование этих возможностей весьма ограничено, они проверяются на соответствие требованиям политики выставления Магазина и анализируются дополнительно.

Они могут использоваться в банковских операциях с двухфакторной проверкой подлинности, а также в ситуациях, при которых пользователям выдается смарт-карта с цифровым сертификатом, подтверждающим их личность. Другие приложения могут первоначально предназначаться для корпоративных клиентов. Таким приложениям нужен доступ к корпоративным ресурсам с учетными данными пользователя домена.

Для отправки в Магазин приложений, использующих особые возможности, требуется учетная запись компании. Напротив, для отправки в Магазин приложений, использующих возможности с ограниченным доступом, специальная учетная запись компании не требуется — они недоступны для использования разработчиками. Возможности с ограниченным доступом используются только в приложениях, которые разрабатываются корпорацией Майкрософт и ее партнерами. Подробнее об учетных записях компаний см. в разделе [Типы, доступность и стоимость учетных записей](https://msdn.microsoft.com/library/windows/apps/JJ863494).

Все возможности с ограниченным доступом должны содержать пространство имен **rescap** при объявлении их в манифесте пакета приложения отлично от других возможностей. Ниже приведен пример объявления возможности **appCaptureSettings**.

```xml
<Capabilities>
    <rescap:Capability Name="appCaptureSettings"/>
</Capabilities>
```

Также необходимо добавить и объявление пространства имен **xmlns:rescap** в верхней части файла Package.appxmanifest, как показано ниже.

```xml
<Package
    xmlns="http://schemas.microsoft.com/appx/manifest/foundation/windows10"
    xmlns:mp="http://schemas.microsoft.com/appx/2014/phone/manifest"
    xmlns:uap="http://schemas.microsoft.com/appx/manifest/uap/windows10"
    xmlns:rescap="http://schemas.microsoft.com/appx/manifest/foundation/windows10/restrictedcapabilities"
    IgnorableNamespaces="uap mp wincap rescap">
```

<table>
<colgroup>
<col width="50%" />
<col width="50%" />
</colgroup>
<thead>
<tr class="header">
<th align="left">Сценарий возможности</th>
<th align="left">Использование возможности</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td align="left">**Корпоративная**</td>
<td align="left"><p>Учетные данные домена Windows позволяют пользователю входить на удаленные ресурсы при помощи учетных данных и действовать так, как будто он предоставил имя пользователя и пароль. Специальная возможность **enterpriseAuthentication** обычно используется в бизнес-приложениях, которые подключаются к серверам предприятия.</p>
<p>Эта возможность не обязательна для обычной передачи данных через Интернет.</p>

<p>Специальная возможность **enterpriseAuthentication** предназначена для поддержки распространенных бизнес-приложений. Не объявляйте ее в приложениях, которым не требуется доступ к корпоративным ресурсам. Возможность [**file picker**](https://msdn.microsoft.com/library/windows/apps/BR207847) предоставляет надежный механизм пользовательского интерфейса, позволяющий пользователям открывать файлы в сетевой папке для использования в приложении. Специальную возможность **enterpriseAuthentication** следует объявлять, только если по сценарию работы приложения требуется программный доступ и его невозможно предоставить, используя **file picker**.</p>
<p>Возможность **enterpriseAuthentication** должна содержать пространство имен **uap** при объявлении ее в манифесте пакета приложения, как показано ниже.</p>
<div class="code">
<span codelanguage="XML"></span>
<table>
<colgroup>
<col width="100%" />
</colgroup>
<thead>
<tr class="header">
<th align="left">XML</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td align="left"><pre><code>&lt;Capabilities&gt;
    &lt;uap:Capability Name="enterpriseAuthentication"/&gt;
&lt;/Capabilities&gt;</code></pre></td>
</tr>
</tbody>
</table>
</div>
<p>Возможность **enterpriseDataPolicy** позволяет приложениям определять и использовать политики предприятия для устройства. Эта возможность необходима, чтобы использовать все члены следующих классов.</p>
<ul>
<li>[**FileProtectionManager**](https://msdn.microsoft.com/library/windows/apps/Dn705151)</li>
<li>[**DataProtectionManager**](https://msdn.microsoft.com/library/windows/apps/Dn706017)</li>
<li>[**ProtectionPolicyManager**](https://msdn.microsoft.com/library/windows/apps/Dn705170)</li>
</ul></td>
</tr>
<tr class="even">
<td align="left">**Общие сертификаты пользователей**</td>
<td align="left"><p>Специальная возможность **sharedUserCertificates** позволяет приложению добавлять и получать доступ к сертификатам программного обеспечения и оборудования в хранилище общих сертификатов пользователей, например к сертификатам, хранящимся на смарт-карте. Эта возможность обычно используется для финансовых или корпоративных приложений, требующих проверки подлинности с помощью смарт-карты.</p>
<p>Возможность **sharedUserCertificates** должна содержать пространство имен **uap** при объявлении ее в манифесте пакета приложения, как показано ниже.</p>
<div class="code">
<span codelanguage="XML"></span>
<table>
<colgroup>
<col width="100%" />
</colgroup>
<thead>
<tr class="header">
<th align="left">XML</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td align="left"><pre><code>&lt;Capabilities&gt;
    &lt;uap:Capability Name="sharedUserCertificates"/&gt;
&lt;/Capabilities&gt;</code></pre></td>
</tr>
</tbody>
</table>
</div></td>
</tr>
<tr class="odd">
<td align="left">**Документы***</td>
<td align="left"><p>Специальная возможность **documentsLibrary** предоставляет программный доступ к библиотеке документов пользователя, отфильтрованный на основе сопоставлений типов файлов, которые объявлены в манифесте пакета, для поддержки автономного доступа к OneDrive. Например, если для средства просмотра DOC-файлов объявлено сопоставление типов файлов с расширением DOC, оно сможет открывать DOC-файлы из библиотеки документов, но не сможет работать с другими типами файлов.</p>
<p>Приложения с объявленной специальной возможностью **documentsLibrary** не будут иметь доступа к библиотеке документов на компьютерах домашней группы. Средство [file picker](https://msdn.microsoft.com/library/windows/apps/Hh465174) предоставляет надежный механизм пользовательского интерфейса, позволяющий пользователям открывать файлы для использования в приложении. Объявляйте специальную возможность **documentsLibrary**, только если вы не можете использовать file picker.</p>
<p>Чтобы использовать специальную возможность **documentsLibrary**, приложение должно:</p>
<ul>
<li>обеспечивать кроссплатформенный автономный доступ к определенному содержимому OneDrive с помощью действительных URL-адресов или идентификаторов ресурсов OneDrive;</li>
<li>автоматически сохранять открытые файлы в OneDrive пользователя в автономном режиме.</li>
</ul>
<p>Приложения, использующие специальную возможность **documentsLibrary** для этих двух целей, могут также дополнительно использовать эту возможность, чтобы открывать внедренное содержимое в других документах. Только перечисленные выше способы использования специальной возможности **documentsLibrary** являются допустимыми.</p>
<ul>
<li><p>Приложение не сможет получить доступ к библиотеке документов во внутренней памяти телефона. Однако если другое приложение создаст папку Documents на дополнительной SD-карте, ваше приложение сможет увидеть эту папку.</p></li>
</ul>
<p>Возможность **documentsLibrary** должна содержать пространство имен **uap** при объявлении ее в манифесте пакета приложения, как показано ниже.</p>
<div class="code">
<span codelanguage="XML"></span>
<table>
<colgroup>
<col width="100%" />
</colgroup>
<thead>
<tr class="header">
<th align="left">XML</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td align="left"><pre><code>&lt;Capabilities&gt;
    &lt;uap:Capability Name="documentsLibrary"/&gt;
&lt;/Capabilities&gt;</code></pre></td>
</tr>
</tbody>
</table>
</div></td>
</tr>
<tr class="even">
<td align="left">**Параметры DVR для игр**</td>
<td align="left"><p>Возможность с ограниченным доступом **appCaptureSettings** позволяет приложениям управлять параметрами пользователя для Game DVR.</p>
<p>Эта возможность требуется для использования некоторых API в пространстве имен [**Windows.Media.Capture**](https://msdn.microsoft.com/library/windows/apps/BR226738).</p></td>
</tr>
<tr class="odd">
<td align="left">**Мобильная связь**</td>
<td align="left"><p>Возможность с ограниченным доступом **cellularDeviceControl** позволяет приложениям контролировать сотовое устройство.</p>
<p>Возможность **cellularDeviceIdentity** предоставляет приложениям доступ к сотовым идентификационным данным.</p>
<p>Возможность **cellularMessaging** позволяет приложениям использовать SMS и RCS.</p>
<p>Эти возможности требуются для использования некоторых API в пространстве имен [**Windows.Devices.Sms**](https://msdn.microsoft.com/library/windows/apps/BR206567).</p>
<p>Начиная c Windows 10, приложения, которые вызывают Windows.Networking.Connectivity.ConnectivityManager.AcquireConnectionAsync, должны или объявить возможность **cellularDeviceControl**, или находиться в списке приложений, включенных в список разрешенных контекстом протокола пакетных данных (PDP) ([**AppIDList**](https://msdn.microsoft.com/library/windows/apps/Dn393996)).</p></td>
</tr>
<tr class="even">
<td align="left">**Разблокировка устройства**</td>
<td align="left"><p>Возможность с ограниченным доступом **deviceUnlock** позволяет приложениям снимать блокировку устройства для сценариев загрузки неопубликованного приложения разработчика и неопубликованных корпоративных приложений.</p></td>
</tr>
<tr class="odd">
<td align="left">**Плитки двойной SIM-карты**</td>
<td align="left"><p>Возможность с ограниченным доступом **dualSimTiles** позволяет приложениям создавать дополнительную запись списка приложений на устройствах с несколькими SIM-картами.</p>
<p>Эта возможность требуется для использования некоторых API в пространстве имен [**Windows.UI.StartScreen**](https://msdn.microsoft.com/library/windows/apps/BR242235).</p></td>
</tr>
<tr class="even">
<td align="left">**Общее корпоративное хранилище**</td>
<td align="left"><p>Возможность с ограниченным доступом **enterpriseDeviceLockdown** позволяет приложениям использовать API блокировки устройства и предоставляет им доступ к корпоративным папкам общего хранилища.</p></td>
</tr>
<tr class="odd">
<td align="left">**Вставка системного ввода**</td>
<td align="left"><p>Возможность с ограниченным доступом **inputInjection** позволяет приложениям внедрять различные формы ввода, такие как HID, сенсорный ввод, перо, клавиатура или мышь, в систему программным путем. Обычно данная возможность используется в приложениях совместной работы, которые могут управлять системой.</p>
<div class="alert">
**Примечание**  Для компьютера внедрение ввода из приложения, имеющего эту возможность, обеспечивается только процессами, которые относятся к одному контейнеру приложения.
</div>
</td>
</tr>
<tr class="even">
<td align="left">**Проверка ввода***</td>
<td align="left"><p>Возможность с ограниченным доступом **inputObservation** позволяет приложениям проверять различные формы необработанных входных данных (например, HID, сенсорный ввод, перо, клавиатура или мышь), получаемых системой, независимо от конечного места назначения.</p></td>
</tr>
<tr class="odd">
<td align="left">**Подавление ввода**</td>
<td align="left"><p>Возможность с ограниченным доступом **inputSuppression** позволяет приложениям подавлять получение различных форм необработанных входных данных (например, HID, сенсорный ввод, перо, клавиатура или мышь) системой.</p></td>
</tr>
<tr class="even">
<td align="left">**Приложение VPN**</td>
<td align="left"><p>Возможность с ограниченным доступом **networkingVpnProvider** предоставляет приложениям полный доступ к функциям VPN, включая возможность управлять подключениями и обеспечивать функциональность подключаемого модуля VPN.</p>
<p>Эта возможность требуется для использования некоторых API в пространстве имен [**Windows.Networking.Vpn**](https://msdn.microsoft.com/library/windows/apps/Dn434040).</p></td>
</tr>
<tr class="odd">
<td align="left">**Управление другими приложениями**</td>
<td align="left"><p>Возможность с ограниченным доступом **packageManagement** позволяет приложениям управлять другими приложениями напрямую.</p>
<p>Возможность устройства **packageQuery** позволяет приложениям собирать сведения о других приложениях.</p>
<p>Эти возможности требуются для доступа к некоторым методам и свойствам в классе [**PackageManager**](https://msdn.microsoft.com/library/windows/apps/BR240960).</p></td>
</tr>
<tr class="even">
<td align="left">**Проецирование экрана**</td>
<td align="left"><p>Возможность с ограниченным доступом **screenDuplication** позволяет приложениям проецировать экран на другое устройство.</p>
<p>Эта возможность требуется для использования API в пространстве имен DirectX.</p></td>
</tr>
<tr class="odd">
<td align="left">**Имя участника-пользователя**</td>
<td align="left"><p>Возможность с ограниченным доступом **userPrincipalName** позволяет приложениям изменять кэш эскизов и получать к нему доступ из фотографий.</p>
<p>Эта возможность требуется для вызова функции [**GetUserNameEx**](https://msdn.microsoft.com/library/windows/desktop/ms724435).</p></td>
</tr>
<tr class="even">
<td align="left">**Кошелек**</td>
<td align="left"><p>Возможность с ограниченным доступом **walletSystem** позволяет приложениям получать полный доступ к сохраненным картам для бумажника.</p>
<p>Эта возможность требуется для использования некоторых API в пространстве имен [**Windows.ApplicationModel.Wallet.System**](https://msdn.microsoft.com/library/windows/apps/Mt171610).</p></td>
</tr>
<tr class="odd">
<td align="left">**Журнал расположений**</td>
<td align="left"><p>Возможность с ограниченным доступом **locationHistory** позволяет приложениям получать доступ к журналу расположения устройства.</p>
<p>Эта возможность требуется для использования некоторых API в пространстве имен [**Windows.Devices.Geolocation**](https://msdn.microsoft.com/library/windows/apps/BR225603).</p></td>
</tr>
<tr class="even">
<td align="left">**Подтверждение закрытия приложения**</td>
<td align="left"><p>Возможность ограниченным доступом **confirmAppClose** позволяет приложениям закрываться и закрывать свои окна, а также задерживать закрытие приложения.</p>
<p>Эта возможность требуется для использования некоторых API в пространстве имен [**Windows.UI.ViewManagement**](https://msdn.microsoft.com/library/windows/apps/BR242295).</p></td>
</tr>
<tr class="odd">
<td align="left">**Журнал вызовов***</td>
<td align="left"><p>Возможность с ограниченным доступом **phoneCallHistory** позволяет приложениям читать журнал вызовов и удалять записи в журнале.</p>
<p>Эта возможность требуется для использования некоторых API в пространстве имен [**Windows.ApplicationModel.Chat**](https://msdn.microsoft.com/library/windows/apps/Dn642321).</p></td>
</tr>
<tr class="even">
<td align="left">**Доступ к встречам системного уровня**</td>
<td align="left"><p>Возможность ограниченным доступом **appointmentsSystem** позволяет приложениям читать и изменить все встречи в календаре пользователя.</p>
<p>Эта возможность требуется для использования некоторых API в пространстве имен [**Windows.ApplicationModel.Appointment**](https://msdn.microsoft.com/library/windows/apps/Dn263359).</p></td>
</tr>
<tr class="odd">
<td align="left">**Доступ к сообщениям чата системного уровня***</td>
<td align="left"><p>Возможность ограниченным доступом **chatSystem** позволяет приложениям читать и писать все SMS- и MMS-сообщения.</p>
<p>Эта возможность требуется для использования некоторых API в пространстве имен [**Windows.ApplicationModel.Chat**](https://msdn.microsoft.com/library/windows/apps/Dn642321).</p></td>
</tr>
<tr class="even">
<td align="left">**Доступ к контактам системного уровня**</td>
<td align="left"><p>Возможность ограниченным доступом **contactsSystem** позволяет приложениям читать контактные данные, которые были обозначены как данные с ограниченным доступом или конфиденциальные, и изменять существующие контактные данные.</p>
<p>Эта возможность требуется для использования некоторых API в пространстве имен [**Windows.ApplicationModel.Chat**](https://msdn.microsoft.com/library/windows/apps/Dn642321).</p></td>
</tr>
<tr class="odd">
<td align="left">**Доступ к электронной почте****</td>
<td align="left"><p>Возможность ограниченным доступом **email** позволяет приложениям читать, рассматривать и отправлять электронные сообщения пользователя.</p>
<p>Эта возможность требуется для использования некоторых API в пространстве имен [**Windows.ApplicationModel.Email**](https://msdn.microsoft.com/library/windows/apps/Dn631285).</p></td>
</tr>
<tr class="even">
<td align="left">**Доступ к электронной почте системного уровня**</td>
<td align="left"><p>Возможность ограниченным доступом **emailSystem** позволяет приложениям читать, рассматривать и отправлять электронные сообщения пользователя с ограниченным доступом или конфиденциальные сообщения.</p>
<p>Эта возможность требуется для использования некоторых API в пространстве имен [**Windows.ApplicationModel.Email**](https://msdn.microsoft.com/library/windows/apps/Dn631285).</p></td>
</tr>
<tr class="odd">
<td align="left">**Доступ к журналу вызовов системного уровня**</td>
<td align="left"><p>Возможность с ограниченным доступом **phoneCallHistorySystem** позволяет приложениям полностью изменять журнал вызовов путем изменения существующих записей и записи новых.</p>
<p>Эта возможность требуется для использования некоторых API в пространстве имен [**Windows.ApplicationModel.Calls**](https://msdn.microsoft.com/library/windows/apps/Dn297266).</p></td>
</tr>
<tr class="even">
<td align="left">**Отправка текстовых сообщений***</td>
<td align="left"><p>Возможность ограниченным доступом **smsSend** позволяет приложениям отправлять SMS- и MMS-сообщения.</p>
<p>Эта возможность требуется для использования некоторых API в пространстве имен [**Windows.ApplicationModel.Chat**](https://msdn.microsoft.com/library/windows/apps/Dn642321).</p></td>
</tr>
<tr class="odd">
<td align="left">**Доступ ко всем данным пользователя системного уровня**</td>
<td align="left"><p>Возможность с ограниченным доступом **userDataSystem** предоставляет приложениям доступ к данным пользователя в хранилище системных данных.</p></td>
</tr>
<tr class="even">
<td align="left">**Функции предварительного просмотра хранилища**</td>
<td align="left"><p>Возможность с ограниченным доступом **previewStore** позволяет приложениям получать и приобретать SKU продуктов из приложения.</p>
<p>Эта возможность требуется для использования определенных API в пространстве имен [**Windows.ApplicationModel.Store.Preview**](https://msdn.microsoft.com/library/windows/apps/Mt185546).</p></td>
</tr>
<tr class="odd">
<td align="left">**Параметры первого входа**</td>
<td align="left"><p>Возможность с ограниченным доступом **firstSignInSettings** позволяет приложениям получать доступ к настройкам пользователя, которые были установлены во время первого входа пользователя на устройство.</p></td>
</tr>
<tr class="even">
<td align="left">**Интерфейс команды разработчиков Windows**</td>
<td align="left"><p>Возможность с ограниченным доступом **teamEditionExperience** позволяет приложениям получать доступ ко внутренним API, которые контролируют многие экспериментальные аспекты сеанса Windows Team. Сеанс Windows Team, вероятнее всего, запущен на устройстве группы, например, Microsoft Surface Hub.</p></td>
</tr>
<tr class="odd">
<td align="left">**Удаленная разблокировка**</td>
<td align="left"><p>Возможность с ограниченным доступом **remotePassportAuthentication** позволяет приложениям получать доступ к учетным данным, которые могут использоваться для разблокировки удаленного ПК.</p></td>
</tr>
<tr class="even">
<td align="left">**Композиция предварительного просмотра**</td>
<td align="left"><p>Возможность с ограниченным доступом **previewUiComposition** позволяет приложениям предварительно просматривать пространство имен [**Windows.UI.Composition**](https://msdn.microsoft.com/library/windows/apps/Dn706878) для интерфейса пользователя, чтобы можно было предоставить отзыв по API до завершения его работы. Для получения дополнительных сведения свяжитесь с wincomposition@microsoft.com.</p></td>
</tr>
<tr class="odd">
<td align="left">**Блокировка безопасной оценки**</td>
<td align="left"><p>Возможность с ограниченным доступом **secureAssessment** позволяет приложениям заблокировать Windows в одном режиме приложения для обеспечения безопасности оценок.</p></td>
</tr>
<tr class="even">
<td align="left">**Подготовка диспетчера подключений**</td>
<td align="left"><p>Возможность с ограниченным доступом **networkConnectionManagerProvisioning** позволяет приложениям определять политики, которые связывают устройство с интерфейсами беспроводных сетей и беспроводных глобальных сетей. Приложения, использующие эту возможность, создаются мобильными операторами для администрирования устройств, подключенных к их мобильным сетям.</p></td>
</tr>
<tr class="odd">
<td align="left">**Подготовка тарифного плана**</td>
<td align="left"><p>Возможность с ограниченным доступом **networkDataPlanProvisioning** позволяет приложениям собирать сведения о тарифных планах на устройстве и считывать трафик. Приложения, использующие эту возможность, создаются мобильными операторами, чтобы интегрировать фактический трафик своих клиентов в параметр трафика операционной системы.</p></td>
</tr>
<tr class="even">
<td align="left">**Лицензирование ПО**</td>
<td align="left"><p>Возможность с ограниченным доступом **slapiQueryLicenseValue** позволяет приложениям запрашивать политики лицензирования программного обеспечения.</p></td>
</tr>
<tr class="odd">
<td align="left">**Расширенное выполнение**</td>
<td align="left"><p>Возможность с ограниченным доступом **extendedExecutionBackgroundAudio** позволяет приложениям воспроизводить аудио, когда приложения не запущены на переднем плане.</p>
<p>Возможность с ограниченным доступом **extendedExecutionCritical** позволяет приложениям начинать критичный расширенный сеанс выполнения.</p>
<p>Возможность с ограниченным доступом **extendedExecutionUnconstrained** позволяет приложениям начинать неограниченный расширенный сеанс выполнения.</p></td>
</tr>
<tr class="even">
<td align="left">**Управление мобильными устройствами**</td>
<td align="left"><p>Возможность с ограниченным доступом **deviceManagementDmAccount** позволяет приложениям готовить и настраивать учетные записи MO OMA-DM.</p>
<p>Возможность с ограниченным доступом **deviceManagementFoundation** позволяет приложениям получать базовый доступ к инфраструктуре поставщика услуг конфигурации (CSP) управления мобильными устройствами (MDM) на устройстве. Обратите внимание, что другие возможности необходимы для получения доступа к конкретным CSP.</p>
<p>Возможность с ограниченным доступом **deviceManagementWapSecurityPolicies** позволяет приложениям настраивать службы протокола на основе WAP, например, MM, индикацию служб/загрузку служб (SI/SL) и OMA-CP.</p>
<p>Возможность с ограниченным доступом **deviceManagementEmailAccount** позволяет приложениям, созданным мобильными операторами, добавлять учетную запись электронной почты на устройства, которые они готовят для пользователей, и управлять ими.</p></td>
</tr>
<tr class="odd">
<td align="left">**Управление политикой пакета**</td>
<td align="left"><p>Возможность с ограниченным доступом **packagePolicySystem** позволяет приложениям контролировать политики системы, связанные с приложениями, установленными на устройстве.</p></td>
</tr>
<tr class="even">
<td align="left">**Список игр**</td>
<td align="left"><p>Возможность с ограниченным доступом **gameList** позволяет приложениям получать список известных игр, установленных в системе.</p></td>
</tr>
<tr class="odd">
<td align="left">**Приложение Xbox**</td>
<td align="left"><p>Возможность с ограниченным доступом **xboxAccessoryManagement** позволяет приложениям напрямую управлять устройствами Xbox, соответствующими спецификациям оборудования Xbox.</p></td>
</tr>
<tr class="even">
<td align="left">**Распознавание речи для стандартных программ**</td>
<td align="left"><p>Возможность с ограниченным доступом **cortanaSpeechAccessory** позволяет приложениям вызывать и передавать команды в Кортану.</p></td>
</tr>
<tr class="odd">
<td align="left">**Вспомогательное управление**</td>
<td align="left"><p>Ограниченная возможность **accessoryManager** позволяет приложениям регистрироваться в качестве дополнительного приложения и подписываться на конкретные уведомления приложений, поэтому их можно переадресовывать в меню стандартных приложений и отображать для пользователя.</p></td>
</tr>
<tr class="even">
<td align="left">**Доступ к драйверу**</td>
<td align="left"><p>Возможность с ограниченным доступом **interopServices** позволяет приложениям взаимодействовать непосредственно с драйверами.</p></td>
</tr>
<tr class="odd">
<td align="left">**Наблюдение переднего плана**</td>
<td align="left"><p>Возможность с ограниченным доступом **inputForegroundObservation** позволяет приложениям на переднем плане перехватывать ввод с клавиатуры и обходит всю обработку ввода с клавиатуры не из приложения. Специальные сочетания клавиш (SAS) не могут быть перехвачены этой возможностью. Эта возможность необходима для получения доступа ко всем членам класса [**KeyboardDeliveryInterceptor**](https://msdn.microsoft.com/library/windows/apps/Mt608395).</p></td>
</tr>
<tr class="even">
<td align="left">**Приложения партнеров изготовителей оборудования и операторов мобильной связи**</td>
<td align="left"><p>Возможность с ограниченным доступом **oemDeployment** позволяет приложениям, созданным партнерами корпорации Майкрософт устанавливать новые приложения и отправлять запросы установленным в данный момент приложениям на устройстве.</p>
<p>Возможность с ограниченным доступом **oemPublicDirectory** позволяет приложениям, созданным партнерами корпорации Майкрософт, получать доступ к общей папке приложения.</p></td>
</tr>
<tr class="odd">
<td align="left">**Лицензирование приложений**</td>
<td align="left"><p>Возможность с ограниченным доступом **appLicensing** разрешает запуск приложений без лицензии. Если вы объявите эту возможность в манифесте, то не сможете отправить свое приложение в магазин. Любые запросы на доступ к этой возможности для отправки в магазин всегда будут отклоняться.</p></td>
</tr>
<tr class="even">
<td align="left">**Система определения расположения**</td>
<td align="left"><p>Возможность с ограниченным доступом **locationSystem** позволяет приложениям вносить определенные привилегированные изменения в настройки расположения, такие как установка расположения по умолчанию для устройства. Если вы объявите эту возможность в манифесте, то не сможете отправить свое приложение в магазин. Любые запросы на доступ к этой возможности для отправки в магазин всегда будут отклоняться.</p></td>
</tr>
</tbody>
</table>

**Примечание.**  
Эта статья адресована разработчикам приложений UWP для Windows 10. В случае разработки приложений для Windows 8.x или Windows Phone 8.x см. раздел [архивной документации](http://go.microsoft.com/fwlink/p/?linkid=619132).

## Связанные темы

* [Конструктор манифестов](https://msdn.microsoft.com/en-us/library/windows/apps/xaml/br230259.aspx)
* [Руководство по приложениям с определением параметров конфиденциальности](https://msdn.microsoft.com/library/windows/apps/Hh768223)
* [Указание возможностей в манифесте пакета](https://msdn.microsoft.com/library/windows/apps/BR211477)
* [Указание возможностей устройства в манифесте пакета](https://msdn.microsoft.com/library/windows/apps/Dn263092)
 


<!--HONumber=Mar16_HO5-->



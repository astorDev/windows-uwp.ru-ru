---
title: Межпроцессное взаимодействие (IPC)
description: В этом разделе объясняются различные способы выполнения межпроцессного взаимодействия (IPC) между приложениями универсальная платформа Windows (UWP) и приложениями Win32.
ms.date: 03/23/2020
ms.topic: article
keywords: windows 10, uwp
ms.openlocfilehash: 5db029db3ffb538802f39aa616c96dbe75601eac
ms.sourcegitcommit: bf7d4f6739aeeaac735aae3dd0dcbda63a8c5e69
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/23/2020
ms.locfileid: "85256384"
---
# <a name="interprocess-communication-ipc"></a>Межпроцессное взаимодействие (IPC)

В этом разделе объясняются различные способы выполнения межпроцессного взаимодействия (IPC) между приложениями универсальная платформа Windows (UWP) и приложениями Win32.

## <a name="app-services"></a>Услуги для приложений

Службы приложений позволяют приложениям предоставлять службы, которые принимают и возвращают контейнеры свойств примитивов (набор[**значений**](/uwp/api/Windows.Foundation.Collections.ValueSet)) в фоновом режиме. Объекты с широкими возможностями могут передаваться при [сериализации](https://stackoverflow.com/questions/46367985/how-to-make-a-class-that-can-be-added-to-the-windows-foundation-collections-valu).

Службы приложений могут выполнять как фоновые задачи [, так и](/windows/uwp/launch-resume/how-to-create-and-consume-an-app-service) [процессы](/windows/uwp/launch-resume/convert-app-service-in-process) в рамках приложения переднего плана.

Службы приложений лучше использовать для совместного использования небольших объемов данных, где задержка почти в реальном времени не требуется.

## <a name="com"></a>COM

[Com](/windows/win32/com/component-object-model--com--portal) — это распределенная объектно-ориентированная система для создания двоичных программных компонентов, которые могут взаимодействовать и взаимодействовать. Как разработчик вы используете COM для создания многократно используемых программных компонентов и слоев автоматизации для приложения. COM-компоненты могут быть в процессе или вне процесса и могут взаимодействовать через [клиентскую и серверную](/windows/win32/com/com-clients-and-servers) модель. Необработанные серверы COM долго использовались в качестве средства для [обмена данными между объектами](/windows/win32/com/inter-object-communication).

Упакованные приложения с возможностью [рунфуллтруст](/windows/uwp/packaging/app-capability-declarations#restricted-capabilities) могут регистрировать необработанные COM-серверы для IPC с помощью [манифеста пакета](/uwp/schemas/appxpackage/uapmanifestschema/element-com-extension). Это называется [упакованным com](https://blogs.windows.com/windowsdeveloper/2017/04/13/com-server-ole-document-support-desktop-bridge/).

## <a name="filesystem"></a>Файловая система

### <a name="broadfilesystemaccess"></a>броадфилесистемакцесс

Упакованные приложения могут выполнять IPC, используя обширную файловую систему, объявляя возможность ограничения [броадфилесистемакцесс](/windows/uwp/files/file-access-permissions#accessing-additional-locations) . Эта возможность предоставляет интерфейсам [Windows. Storage](/uwp/api/Windows.Storage) API и API [ксксксфромапп](/previous-versions/windows/desktop/legacy/mt846585(v=vs.85)) Win32 доступ к широкой файловой системе.

По умолчанию IPC через файловую систему для упакованных приложений ограничена другими механизмами, описанными в этом разделе.

### <a name="publishercachefolder"></a>публишеркачефолдер

[Публишеркачефолдер](/uwp/api/windows.storage.applicationdata.getpublishercachefolder) позволяет упакованным приложениям объявлять папки в своем манифесте, которые могут использоваться совместно с другими пакетами одним и тем же издателем.

Папка общего хранилища имеет следующие требования и ограничения.

* Данные в папке общего хранилища не архивируются и не перемещаются.
* Пользователь может очистить содержимое папки общего хранилища.
* Нельзя использовать папку общего хранилища для обмена данными между приложениями разных издателей.
* Нельзя использовать папку общего хранилища для обмена данными между разными пользователями.
* В папке общего хранилища нет управления версиями.

Если вы публикуете несколько приложений и ищете простой механизм обмена данными между ними, Публишеркачефолдер является простым параметром на основе файловой системы.

### <a name="sharedaccessstoragemanager"></a>шаредакцесссторажеманажер

[Шаредакцесссторажеманажер](/uwp/api/Windows.ApplicationModel.DataTransfer.SharedStorageAccessManager) используется совместно со службами приложений, активацией протоколов (например, лаунчурифорресултсасинк) и т. д., чтобы совместно использовать сторажефилес через маркеры.

## <a name="fulltrustprocesslauncher"></a>фуллтрустпроцесслаунчер

Благодаря возможности [рунфуллтруст](/windows/uwp/packaging/app-capability-declarations#restricted-capabilities) Упакованные приложения могут [запускать процессы с полным доверием](/uwp/api/Windows.ApplicationModel.FullTrustProcessLauncher) в одном пакете.

В сценариях, где ограничения пакета являются косвенными, или отсутствуют параметры IPC, приложение может использовать процесс полного доверия в качестве прокси-сервера для взаимодействия с системой, а затем IPC с полным уровнем доверия через службы приложений или другой хорошо поддерживаемый механизм IPC.

## <a name="launchuriforresultsasync"></a>LaunchUriForResultsAsync

[Лаунчурифорресултсасинк](/windows/uwp/launch-resume/how-to-launch-an-app-for-results) используется для простого обмена данными[ValueSet](/uwp/api/Windows.Foundation.Collections.ValueSet)с другими упакованными приложениями, реализующими контракт активации [протоколфорресултс](/windows/uwp/launch-resume/how-to-launch-an-app-for-results#step-2-override-applicationonactivated-in-the-app-that-youll-launch-for-results) . В отличие от служб приложений, которые обычно выполняются в фоновом режиме, целевое приложение запускается на переднем плане.

Для совместного использования файлов можно передавать маркеры [шаредсторажеакцессманажер](/uwp/api/Windows.ApplicationModel.DataTransfer.SharedStorageAccessManager) в приложение через его значение.

## <a name="loopback"></a>Замыкание на себя

Замыкание на себя — это процесс связи с сетевым сервером, который прослушивает localhost (адрес замыкания на себя).

Для обеспечения безопасности и сетевой изоляции подключения по обратной связи для IPC по умолчанию блокируются для упакованных приложений. Можно включить замыкание соединений между доверенным упакованным приложением с помощью [возможностей](/previous-versions/windows/apps/hh770532(v=win.10)) и [свойств манифеста](/uwp/schemas/appxpackage/uapmanifestschema/element-uap4-loopbackaccessrules).

* Все Упакованные приложения, участвующие в подключениях замыкания на себя, должны объявлять `privateNetworkClientServer` возможности в [манифестах пакетов](/uwp/schemas/appxpackage/uapmanifestschema/element-capability).
* Два упакованных приложения могут обмениваться данными через замыкание на себя, объявляя [лупбаккакцессрулес](/uwp/schemas/appxpackage/uapmanifestschema/element-uap4-loopbackaccessrules) в манифестах пакетов.
    * Каждое приложение должно перечислить в [лупбаккакцессрулес](/uwp/schemas/appxpackage/uapmanifestschema/element-uap4-loopbackaccessrules). Клиент объявляет правило "out" для сервера, а сервер объявляет правила "in" для поддерживаемых клиентов.

> [!NOTE]
> Имя семейства пакетов, необходимое для распознавания приложения в этих правилах, можно найти с помощью редактора манифеста пакета в Visual Studio во время разработки, в [центре партнеров](/windows/uwp/publish/view-app-identity-details) для приложений, опубликованных с помощью Microsoft Store, или с помощью команды PowerShell [Get-AppxPackage](/powershell/module/appx/get-appxpackage?view=win10-ps) для уже установленных приложений.

Неупакованные приложения и службы не имеют удостоверения пакета, поэтому их нельзя объявлять в [лупбаккакцессрулес](/uwp/schemas/appxpackage/uapmanifestschema/element-uap4-loopbackaccessrules). Вы можете настроить упакованное приложение для подключения через замыкание с помощью неупакованных приложений и служб с помощью [CheckNetIsolation.exe](/previous-versions/windows/apps/hh780593(v=win.10)), однако это возможно только в сценариях загружать неопубликованные или отладки, где у вас есть локальный доступ к компьютеру и у вас есть права администратора.

* Все Упакованные приложения, участвующие в подключениях замыкания на себя, должны объявлять `privateNetworkClientServer` возможности в [манифестах пакетов](/uwp/schemas/appxpackage/uapmanifestschema/element-capability).
* Если упакованное приложение подключается к неупакованному приложению или службе, выполните команду, `CheckNetIsolation.exe LoopbackExempt -a -n=<PACKAGEFAMILYNAME>` чтобы добавить исключение замыкания на себя для упакованного приложения.
* Если неупакованное приложение или служба подключается к упакованному приложению, выполните команду, `CheckNetIsolation.exe LoopbackExempt -is -n=<PACKAGEFAMILYNAME>` чтобы позволить упакованному приложению принимать входящие подключения с замыканием на себя.
    * [CheckNetIsolation.exe](/previous-versions/windows/apps/hh780593(v=win.10)) должны выполняться постоянно, пока упакованное приложение прослушивает подключения.
    * Этот `-is` флаг появился в Windows 10 версии 1607 (10,0; Сборка 14393).

> [!NOTE]
> Имя семейства пакетов, необходимое для `-n` флага [CheckNetIsolation.exe](/previous-versions/windows/apps/hh780593(v=win.10)) , можно найти с помощью редактора манифеста пакета в Visual Studio во время разработки, через [Центр партнеров](/windows/uwp/publish/view-app-identity-details) для приложений, опубликованных с помощью Microsoft Store, или с помощью команды PowerShell [Get-AppxPackage](/powershell/module/appx/get-appxpackage?view=win10-ps) для уже установленных приложений.

[CheckNetIsolation.exe](/previous-versions/windows/apps/hh780593(v=win.10)) также полезна при [отладке проблем сетевой изоляции](/previous-versions/windows/apps/hh780593(v=win.10)#debug-network-isolation-issues).

## <a name="pipes"></a>Каналы

[Каналы](/windows/win32/ipc/pipes) обеспечивают простое взаимодействие между сервером канала и одним или несколькими клиентами канала.

[Анонимные каналы](/windows/win32/ipc/anonymous-pipes) и [именованные каналы](/windows/win32/ipc/named-pipes) поддерживают следующие ограничения:

* По умолчанию именованные каналы в упакованных приложениях поддерживаются только между процессами в одном пакете, если только процесс не имеет полного доверия.
* Именованные каналы можно совместно использовать в пакетах, следуя рекомендациям по [предоставлению общего доступа к именованным объектам](/windows/uwp/communication/sharing-named-objects).
* Именованные каналы в упакованных приложениях должны использовать синтаксис `\\.\pipe\LOCAL\` для имени канала.

## <a name="registry"></a>Реестр

Использование [реестра](/windows/win32/sysinfo/registry-functions) для IPC, как правило, не рекомендуется, но поддерживается для существующего кода. Упакованные приложения имеют доступ только к тем разделам реестра, для доступа к которым у них есть разрешение.

[Классические приложения, Упакованные в MSIX,](/windows/msix/desktop/desktop-to-uwp-root) используют [виртуализацию реестра](/windows/msix/desktop/desktop-to-uwp-behind-the-scenes#registry) , так что глобальные записи реестра содержатся в частном кусте в пакете MSIX. Это обеспечивает совместимость исходного кода при минимизации влияния на глобальные реестры и может использоваться для IPC между процессами в одном пакете. Если необходимо использовать реестр, то эта модель является предпочтительной, а управление глобальным реестром — с помощью.

## <a name="rpc"></a>RPC

[RPC](/windows/win32/rpc/rpc-start-page) можно использовать для подключения упакованного приложения к КОНЕЧНОЙ точке RPC Win32 при условии, что Пакетное приложение имеет правильные возможности для сопоставления ACL в КОНЕЧНОЙ точке RPC.

Пользовательские возможности позволяют производителям оборудования и независимым поставщикам программно [определять произвольные возможности](/windows-hardware/drivers/devapps/hardware-support-app--hsa--steps-for-driver-developers#reserving-a-custom-capability), предоставлять [им конечные точки RPC](/windows-hardware/drivers/devapps/hardware-support-app--hsa--steps-for-driver-developers#allowing-access-to-an-rpc-endpoint-to-a-uwp-app-using-the-custom-capability), а затем [предоставить эти возможности полномочным клиентским приложениям](/windows-hardware/drivers/devapps/hardware-support-app--hsa--steps-for-driver-developers#preparing-the-signed-custom-capability-descriptor-sccd-file). Полный пример приложения см. в примере [кустомкапабилити](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/CustomCapability) .

Конечные точки RPC также могут быть доступен для конкретных упакованных приложений, чтобы ограничить доступ к конечной точке только этими приложениями без необходимости управления дополнительными возможностями. Вы можете использовать API [деривеаппконтаинерсидфромаппконтаинернаме](/windows/win32/api/userenv/nf-userenv-deriveappcontainersidfromappcontainername) для получения идентификатора безопасности из имени семейства пакетов, а затем в списке ACL КОНЕЧНОЙ точки RPC с ИД безопасности, как показано в примере [кустомкапабилити](https://github.com/Microsoft/Windows-universal-samples/blob/master/Samples/CustomCapability/Service/Server/RpcServer.cpp) .

## <a name="shared-memory"></a>Общая память

[Сопоставление файлов](/windows/win32/memory/sharing-files-and-memory) можно использовать для совместного использования файла или памяти между двумя или более процессами со следующими ограничениями:

* По умолчанию сопоставления файлов в упакованных приложениях поддерживаются только между процессами в одном пакете, если только процесс не имеет полного доверия.
* Сопоставления файлов можно совместно использовать в пакетах, следуя рекомендациям по [предоставлению общего доступа к именованным объектам](/windows/uwp/communication/sharing-named-objects).

Для эффективного совместного использования больших объемов данных и управления ими рекомендуется использовать общую память.

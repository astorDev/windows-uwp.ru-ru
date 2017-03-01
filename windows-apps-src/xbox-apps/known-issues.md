---
author: Mtoepke
title: "Известные проблемы с программой для разработчиков UWP для Xbox One"
description: 
ms.author: mtoepke
ms.date: 02/08/2017
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: windows 10, uwp
ms.assetid: a7b82570-1f99-4bc3-ac78-412f6360e936
translationtype: Human Translation
ms.sourcegitcommit: 5645eee3dc2ef67b5263b08800b0f96eb8a0a7da
ms.openlocfilehash: 4b13b9bbbc75de47ed69112680894d5e3f34d8a1
ms.lasthandoff: 02/08/2017

---

# <a name="known-issues-with-uwp-on-xbox-developer-program"></a>Известные проблемы с программой для разработчиков UWP для Xbox

В этом разделе описываются известные проблемы с программой для разработчиков UWP для Xbox One. Дополнительные сведения об этой программе см. в разделе [UWP для Xbox](index.md). 

\ [Если вы перешли сюда по ссылке в справочном разделе об API и ищете сведения об API для всех семейств устройств, см. раздел [Функции UWP, которые в настоящее время не поддерживаются в Xbox](http://go.microsoft.com/fwlink/?LinkID=760755).\]

В следующем списке приведены некоторые известные проблемы, которые могут возникнуть при работе (список не является исчерпывающим). 

**Мы хотим услышать ваши отзывы**, поэтому сообщайте о любых возникающих проблемах на форуме [Разработка приложений для универсальной платформы Windows](https://social.msdn.microsoft.com/forums/windowsapps/home?forum=wpdevelop). 

Если ваша проблема не решена, прочитайте информацию в этом разделе и обратитесь к разделу [Вопросы и ответы](frequently-asked-questions.md), а также используйте форумы, чтобы получить помощь.


<!--## Developing games-->

## <a name="issue-when-leaving-dev-mode"></a>Проблема при выходе из режима разработчика
Иногда может случиться, что вы не сможете выйти из режима разработчика ни с помощью средства DevHome, ни с помощью параметров разработчика.
Существует два способа устранения этой проблемы. 
1. Снимите флажок **Удалять неопубликованные игры и приложения** при выходе из режима разработчика.
2. Перейдите в раздел "Мои игры и приложения" и удалите все установленные на консоли приложения разработчика.
 
<!--## Memory limits for background apps are partially enforced
 
The maximum memory footprint for apps running in the background is 128 megabytes. In the current version of UWP on Xbox One, your app will be suspended if it is above this limit when it is moved to the background. This limit is not currently enforced if your app exceeds the limit while it is already running in the background—this means that if your app exceeds 128 MB while running in the background, it will still be able to allocate memory.
 
There is currently no workaround for this issue. Apps should govern their memory usage accordingly and continue to stay under the 128 MB limit while running in the background.-->
 
## <a name="deploying-from-vs-fails-with-parental-controls-turned-on"></a>Развертывание из VS не выполняется при включенном родительском контроле

Запустить приложение из VS не удастся, если в параметрах консоли включена функция родительского контроля.

Чтобы решить эту проблему, временно отключите родительский контроль, либо выполните следующие действия.
1. Разверните приложение на консоли с выключенным родительским контролем.
2. Включите родительский контроль.
3. Запустите приложение с консоли.
4. Введите ПИН-код или пароль, чтобы разрешить запуск приложения.
5. Приложение запустится.
6. Закройте приложение.
7. Запустите из VS, нажав F5, и приложение запустится без каких-либо предварительных запросов.

На этом этапе разрешение является _закрепленным_ до тех пор, пока не будет выполнен выход пользователя, даже если удалить и переустановить приложение.
 
Существует еще один тип исключения, доступный только для детских учетных записей. Учетная запись ребенка требует, чтобы родитель выполнил вход для выдачи разрешения, однако когда это происходит, родитель может выбрать параметр **Всегда**, чтобы разрешить ребенку запускать приложение. Это исключение хранится в облаке и останется в силе, даже если ребенок выйдет из приложения и снова войдет.   

<!--### x86 vs. x64

By the time we release later this year, we will have great support for both x86 and x64, and we do support x86 in this preview. 
However, x64 has had much more testing to date (the Xbox shell and all of the apps running on the console today are x64), and so we recommend using x64 for your projects. 
This is particularly true for games.

If you decide to use x86, please report any issues you see on the forum.

Also see [Switching build flavors can cause deployment failures](known-issues.md#switching-build-flavors-can-cause-deployment-failures) later on this page.-->

<!--### Game engines

We have tested some popular game engines, but not all of them, and our test coverage for this preview has not been comprehensive. 
Your mileage may vary. 

The following game engines have been confirmed to work:
* [Construct 2](https://www.scirra.com/)

There are likely others that are working too. We would love to get your feedback on what you find. 
Please use the forum to report any issues you see.-->

## <a name="directx-12-support"></a>Поддержка DirectX 12

UWP для Xbox One поддерживает DirectX 11 с уровнем компонентов 10. DirectX 12 в настоящее время не поддерживается. 

Xbox One, как и все традиционные игровые консоли, — это специализированный аппаратный компонент, для реализации полного потенциала которого требуется специальный комплект SDK. Если вы работаете с игрой, которая требует максимального использования возможностей оборудования Xbox One, можно зарегистрироваться в программе [ID@XBOX](http://www.xbox.com/Developers/id), чтобы получить доступ к этому комплекту SDK (в котором реализована поддержка DirectX 12).

<!-- ### Xbox One Developer Preview disables game streaming to Windows 10

Activating the Xbox One Developer Preview on your console will prevent you from streaming games from your Xbox One to the Xbox app on Windows 10, even if your console is set to retail mode. 
To restore the game streaming feature, you must leave the developer preview. -->

<!--## System resources for UWP apps and games on Xbox One

UWP apps and games running on Xbox One share resources with the system and other apps, and so the system governs the resources that are available to any one game or app. 
If you are running into memory or performance issues, this may be why. 
For more details, see [System resources for UWP apps and games on Xbox One](system-resource-allocation.md).-->

<!--
## Networking using traditional sockets

In this developer preview, inbound and outbound network access from the console that uses traditional TCP/UDP sockets (WinSock, Windows.Networking.Sockets) is not available. 
Developers can still use HTTP and WebSockets.
--> 

## <a name="blocked-networking-ports-on-xbox-one"></a>Заблокированные сетевые порты на Xbox One

Приложения для универсальной платформы Windows (UWP) на устройствах Xbox One не могут выполнять привязку к портам в диапазоне [57344, 65535]. Несмотря на то, что во время выполнения кажется, что привязка к таким портам выполнена успешно, сетевой трафик отбрасывается прежде, чем достигнет вашего приложения. По возможности ваше приложение должно осуществлять привязку к порту 0, что позволяет системе выбрать локальный порт. Если требуется использовать определенный порт, номер порта должен лежать в диапазоне [1025, 49151] и необходимо проверять и избегать возможных конфликтов с реестром IANA. Дополнительные сведения см. в разделе [Имя службы и реестр номера порта транспортного протокола](http://www.iana.org/assignments/service-names-port-numbers/service-names-port-numbers.xhtml).

## <a name="uwp-api-coverage"></a>Охват API платформы UWP

Не все API UWP поддерживаются на Xbox. Список API, которые, по имеющимся у нас сведениям, не работают, см. в разделе [Функции UWP, которые еще не поддерживаются на Xbox](http://go.microsoft.com/fwlink/p/?LinkId=760755). При обнаружении проблем с другими API следует сообщить о них на форумах. 

<!--## XAML controls do not look like or behave like the controls in the Xbox One shell

In this developer preview, the XAML controls are not in their final form. In particular:
* Gamepad X-Y navigation does not work reliably for all controls.
* Controls do not look like controls in the Xbox shell. This includes the control focus rectangle.
* Navigating between controls does not automatically make “navigation sounds.”

These issues will be addressed in a future developer preview.-->

<!--## Visual Studio and deployment issues

### Switching build flavors can cause deployment failures

Switching between Debug and Release builds, or between x86 and x64, or between Managed and .Net Native builds, can cause deployment failures. 

The simplest way to avoid these issues for this preview is to stick to Debug and one architecture. 

If you do hit this issue, uninstalling your app in the Collections app on your Xbox One will typically resolve it.

> ****&nbsp;&nbsp;Uninstalling your app from Windows Device Portal (WDP) will not resolve the issue.

If your issues persist, uninstall your app or game in the Collections app, leave Developer Mode, restart to Retail Mode and then switch back to Developer Mode.
You may also need to restart Visual Studio and clean your solution.

For more information, see the “Fixing deployment failures” section in [Frequently asked questions](frequently-asked-questions.md).

### Uninstalling an app while you are debugging it in Visual Studio will cause it to fail silently

Attempting to uninstall an app that is running under the debugger via the WDP “Installed Apps” tool will cause it to silently fail. 
The workaround is to stop debugging the app in Visual Studio before attempting to remove it via WDP.

### Visual Studio/Xbox PIN pairing failures

It is possible to get into a state where the PIN pairing between Visual Studio and your Xbox One gets out of sync. 
If PIN pairing fails, use the “Remove all pairings” button in Dev Home, restart Xbox One, restart your development PC, and then try again.--> 


<!--## Windows Device Portal (WDP) preview-->

<!--### Starting WDP from Dev Home crashes Dev Home

When you start WDP in Dev Home, it will cause Dev Home to crash after you have entered your user name and password and selected **Save**. 
The credentials are saved but WDP is not started. 
You can start WDP by restarting Xbox One.--> 

<!--### Disabling WDP in Dev Home does not work

If you disable WDP in Dev Home, it will be turned off. 
However, when you restart your Xbox One, WDP will be started again. 
You can work around this issue by using **Reset and keep my games & apps** to delete any stored state on your Xbox One. 
Go to Settings > System > Console info & updates > Reset console, and then select the **Reset and keep my games & apps** button.

> **Caution**&nbsp;&nbsp;Doing this will delete all saved settings on your Xbox One including wireless settings, user accounts and any game progress that has not been saved to cloud storage.

> **Caution**&nbsp;&nbsp;DO NOT select the **Reset and remove everything** button.
This will delete all of your games, apps, settings and content, deactivate Developer Mode, and remove you console from the Developer Preview group.

### The columns in the “Running Apps” table do not update predictably. 

Sometimes this is resolved by sorting a column on the table.-->

## <a name="navigating-to-wdp-causes-a-certificate-warning"></a>Переход к WDP вызывает предупреждение о сертификате

Вы получите предупреждение о предоставленном сертификате (см. пример на следующем снимке), потому что сертификат безопасности подписан вашей консолью Xbox One, а она не считается известным и доверенным издателем. Чтобы перейти на портал устройств Windows, щелкните **Перейти к этому веб-сайту**.

![Предупреждение о сертификате безопасности веб-сайта](images/security_cert_warning.jpg)

<!--## Dev Home

Occasionally, selecting the “Manage Windows Device Portal” option in Dev Home will cause Dev Home to silently exit to the Home screen. 
This is caused by a failure in the WDP infrastructure on the console and can be resolved by restarting the console.-->

## <a name="knownfoldersmediaserverdevices-caveat-on-xbox"></a>Пояснение для KnownFolders.MediaServerDevices на Xbox

На рабочем столе серверы мультимедиа «связываются» с ПК, а служба сопоставления устройств постоянно отслеживает, какие серверы сейчас активны, поэтому первоначальный запрос файловой системы может немедленно вернуть список связанных серверов, активных в настоящее время.

На Xbox не предусмотрены элементы пользовательского интерфейса для добавления или удаления серверов, поэтому первоначальный запрос файловой системы всегда будет возвращаться пустым. Необходимо создать запрос и подписаться на событие ContentsChanged, а затем обновлять запрос при каждом получении уведомления. Серверы будут постепенно добавляться, и большинство из них будут обнаружены в течение 3 секунд.

Простой пример кода:

```
namespace TestDNLA {

    public sealed partial class MainPage : Page {
        public MainPage() {
            this.InitializeComponent();
        }

        private async void FindFiles_Click(object sender, RoutedEventArgs e) {
            try {
                StorageFolder library = KnownFolders.MediaServerDevices;
                var folderQuery = library.CreateFolderQuery();
                folderQuery.ContentsChanged += FolderQuery_ContentsChanged;
                IReadOnlyList<StorageFolder> rootFolders = await folderQuery.GetFoldersAsync();
                if (rootFolders.Count == 0) {
                    Debug.WriteLine("No Folders found");
                } else {
                    Debug.WriteLine("Folders found");
                }
            } catch (Exception ex) {
                Debug.WriteLine("Error: " + ex.Message);
            } finally {
                Debug.WriteLine("Done");
            }
        }

        private async void FolderQuery_ContentsChanged(Windows.Storage.Search.IStorageQueryResultBase sender, object args) {
            Debug.WriteLine("Folder added " + sender.Folder.Name);
            IReadOnlyList<StorageFolder> topLevelFolders = await sender.Folder.GetFoldersAsync();
            foreach (StorageFolder topLevelFolder in topLevelFolders) {
                Debug.WriteLine(topLevelFolder.Name);
            }
        }
    }
}
```

## <a name="see-also"></a>См. также
- [Вопросы и ответы](frequently-asked-questions.md)
- [Приложения UWP для Xbox One](index.md)


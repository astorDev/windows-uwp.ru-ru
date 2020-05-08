---
title: Известные проблемы с программой для разработчиков UWP для Xbox
description: Ниже перечислены известные проблемы в программе разработчиков UWP для Xbox.
ms.date: 03/29/2017
ms.topic: article
keywords: windows 10, uwp
ms.assetid: a7b82570-1f99-4bc3-ac78-412f6360e936
ms.localizationpriority: medium
ms.openlocfilehash: dbf9d40d4dc2cfedaa78cbca5b16c4cc26d2d4e1
ms.sourcegitcommit: ef723e3d6b1b67213c78da696838a920c66d5d30
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/02/2020
ms.locfileid: "82730077"
---
# <a name="known-issues-with-uwp-on-xbox-developer-program"></a>Известные проблемы с программой для разработчиков UWP для Xbox

В этом разделе описываются известные проблемы с программой для разработчиков UWP для Xbox One. Дополнительные сведения об этой программе см. в разделе [UWP для Xbox](index.md). 

\[Если вы поступили отсюда по ссылке в справочном разделе по API и ищете сведения об API универсального семейства устройств, см. раздел [функции UWP, которые еще не поддерживаются в Xbox](https://docs.microsoft.com/uwp/extension-sdks/uwp-limitations-on-xbox?redirectedfrom=MSDN).\]

В следующем списке приведены некоторые известные проблемы, которые могут возникнуть при работе (список не является исчерпывающим). 

**Мы хотим услышать ваши отзывы**, поэтому сообщайте о любых возникающих проблемах на форуме [Разработка приложений для универсальной платформы Windows](https://social.msdn.microsoft.com/forums/windowsapps/home?forum=wpdevelop). 

Если ваша проблема не решена, прочитайте информацию в этом разделе и обратитесь к разделу [Вопросы и ответы](frequently-asked-questions.md), а также используйте форумы, чтобы получить помощь.

 
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

## <a name="storagefilecopyasync-fails-to-copy-encrypted-files-to-unencrypted-destination"></a>StorageFile.CopyAsync не удается скопировать зашифрованные файлы в незашифрованное место назначения 

Когда StorageFile.CopyAsync используется для копирования зашифрованного файла в незашифрованное место назначения, вызов завершается ошибкой, за исключением следующих случаев:

```
System.UnauthorizedAccessException: Access is denied. (Excep_FromHResult 0x80070005)
```

Это может повлиять на разработчиков Xbox, которым нужно скопировать файлы, развернутые в составе пакета приложения, в другом расположении. Причина в том, что содержимое пакета зашифровано в коммерческом режиме Xbox, а не в режиме разработки. В результате приложение может работать должным образом во время разработки и тестирования, но затем произойдет сбой после публикации и установки на коммерческой консоли Xbox.
 

## <a name="blocked-networking-ports-on-xbox-one"></a>Заблокированные сетевые порты на Xbox One

Приложения для универсальной платформы Windows (UWP) на устройствах Xbox One не могут выполнять привязку к портам в диапазоне [57344, 65535]. Несмотря на то, что во время выполнения кажется, что привязка к таким портам выполнена успешно, сетевой трафик отбрасывается прежде, чем достигнет вашего приложения. По возможности ваше приложение должно осуществлять привязку к порту 0, что позволяет системе выбрать локальный порт. Если требуется использовать определенный порт, номер порта должен лежать в диапазоне [1025, 49151] и необходимо проверять и избегать возможных конфликтов с реестром IANA. Дополнительные сведения см. в разделе [Имя службы и реестр номера порта транспортного протокола](https://www.iana.org/assignments/service-names-port-numbers/service-names-port-numbers.xhtml).

## <a name="windows-runtime-api-coverage"></a>Покрытие среда выполнения Windows API

Не все среда выполнения Windows API поддерживаются в Xbox. Список API, которые, по имеющимся у нас сведениям, не работают, см. в разделе [Функции UWP, которые еще не поддерживаются на Xbox](https://docs.microsoft.com/uwp/extension-sdks/uwp-limitations-on-xbox?redirectedfrom=MSDN). При обнаружении проблем с другими API следует сообщить о них на форумах. 


## <a name="navigating-to-wdp-causes-a-certificate-warning"></a>Переход к WDP вызывает предупреждение о сертификате

Вы получите предупреждение о предоставленном сертификате (см. пример на следующем снимке), потому что сертификат безопасности подписан вашей консолью Xbox One, а она не считается известным и доверенным издателем. Чтобы перейти на портал устройств Windows, щелкните **Перейти к этому веб-сайту**.

![Предупреждение о сертификате безопасности веб-сайта](images/security_cert_warning.jpg)


## <a name="knownfoldersmediaserverdevices-caveat-on-xbox"></a>Пояснение для KnownFolders.MediaServerDevices на Xbox

На рабочем столе серверы мультимедиа "связываются" с ПК, а служба сопоставления устройств постоянно отслеживает, какие серверы сейчас активны, поэтому первоначальный запрос файловой системы может немедленно вернуть список связанных серверов, активных в настоящее время.

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
- [Часто задаваемые вопросы](frequently-asked-questions.md)
- [Приложения UWP для Xbox One](index.md)

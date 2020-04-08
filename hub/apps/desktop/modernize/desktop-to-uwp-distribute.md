---
Description: Распространение приложения, упакованного с помощью моста для классических приложений
title: Опубликуйте упакованное классическое приложение в Microsoft Store или загрузите его в неопубликованном виде на одно или несколько устройств.
ms.date: 05/18/2018
ms.topic: article
keywords: windows 10, uwp
ms.assetid: edff3787-cecb-4054-9a2d-1fbefa79efc4
ms.author: mcleans
author: mcleanbyron
ms.localizationpriority: medium
ms.openlocfilehash: 14ad6707b7203dddd9aa7be186e76da677bbd675
ms.sourcegitcommit: cc108c791842789464c38a10e5d596c9bd878871
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/20/2019
ms.locfileid: "75302708"
---
# <a name="distribute-your-packaged-desktop-app"></a>Распространение упакованного классического приложения

Если вы решите [упаковать классическое приложение в пакет MSIX](/windows/msix/desktop/desktop-to-uwp-root), то сможете опубликовать такое приложение в Microsoft Store или загрузить в неопубликованном виде на одно или несколько устройств.

> [!NOTE]
> У вас есть план, как перевести пользователей на упакованное приложение? Перед распространением ознакомьтесь с разделом [Перевод пользователей на упакованное приложение](#transition-users) этого руководства, чтобы получить несколько идей.

## <a name="distribute-your-application-by-publishing-it-to-the-microsoft-store"></a>Распространение приложения путем его публикации в Microsoft Store

[Microsoft Store](https://www.microsoft.com/store/apps) — это привычный для пользователей способ получать приложения.

Опубликуйте приложение в Microsoft Store, чтобы охватить самую широкую аудиторию. Корпоративные клиенты также могут приобрести ваше приложение, чтобы распространять его внутри своих организаций через [Microsoft Store для бизнеса](https://businessstore.microsoft.com/store).

Если вы планируете публиковать приложение в Microsoft Store, в ходе процесса отправки вам будет предложено ответить на несколько дополнительных вопросов. Это связано с тем, что манифест пакета объявляет возможность с ограниченным доступом под названием **runFullTrust**, и мы должны утвердить использование этой возможности приложением. Подробнее об этом вы можете прочитать здесь: [Ограниченные возможности](/windows/uwp/packaging/app-capability-declarations#restricted-capabilities).

Подписывать приложение перед отправкой в Store не требуется.

>[!IMPORTANT]
> Если вы планируете опубликовать приложение в Microsoft Store, проверьте исправность его работы на устройствах под управлением Windows 10 S (это обязательное требование для Store). См. статью [Тестирование приложения для Windows на Windows 10 S](/windows/msix/desktop/desktop-to-uwp-test-windows-s).

<a id="side-load" />

## <a name="distribute-your-application-without-placing-it-onto-the-microsoft-store"></a>Распространение приложения без размещения в Microsoft Store

Если вы не хотите использовать Store, можно вручную распространить приложения на одно или несколько устройств.

Это имеет смысл, если вам нужен более жесткий контроль над процессом распространения либо вы не хотите участвовать в процессе сертификации Microsoft Store.

Чтобы распространить приложение на другие устройства, не размещая его в Store, вам следует получить сертификат, подписать с его помощью приложение и загрузить это приложение как неопубликованное на нужные устройства.

Вы можете [создать сертификат](/windows/msix/package/create-certificate-package-signing) или получить его от известного поставщика, например [Verisign](https://www.verisign.com/).

Если ваше приложение будет распространяться на устройства под управлением Windows 10 S, оно должно быть подписано Microsoft Store, поэтому вам придется отправить его в Store перед распространением.

Если вы создаете сертификат, его необходимо установить в хранилище сертификатов **Доверенный корневой сертификат** или **Доверенные лица** на каждом устройстве, которое запускает ваше приложение. Если сертификат получен от известного поставщика, в других системах достаточно установить только ваше приложение.  

> [!IMPORTANT]
> Убедитесь, что имя издателя на сертификате совпадает с именем издателя вашего приложения.

Подпишите приложение с помощью этого сертификата, используя средство SignTool, как описано в [этой статье](/windows/msix/package/sign-app-package-using-signtool).

Сведения о загрузке неопубликованного приложения на другие устройства см. в статье [Загрузка неопубликованных бизнес-приложений в Windows 10](/windows/application-management/sideload-apps-in-windows-10).

<a id="transition-users" />

## <a name="transition-users-to-your-packaged-app"></a>Перевод пользователей на упакованное приложение

Перед распространением приложения рекомендуется добавить несколько расширений в ваш манифест пакета, чтобы пользователям было проще привыкнуть к работе с упакованным приложением. Вот пара советов о том, что можно сделать.

* Задайте ссылки на упакованное приложение для существующих плиток начального экрана и кнопок на панели задач.
* Создайте связь между упакованным приложением и некоторыми типами файлов.
* Укажите упакованное приложение как приложение по умолчанию для открытия некоторых типов файлов.

Полный перечень расширений и рекомендации по их использованию см. в разделе [Переход пользователей на ваше приложение](desktop-to-uwp-extensions.md#transition-users-to-your-app).

Кроме того, в упакованное приложение можно добавить код, который выполняет следующие задачи:

* Переносит пользовательские данные, связанные с классическим приложением, в соответствующие папки для упакованного приложения.
* Позволяет пользователям удалить классическую версию приложения.

Давайте подробнее рассмотрим каждую из этих задач. Начнем с переноса пользовательских данных.

### <a name="migrate-user-data"></a>Перенос пользовательских данных

Если вы решили добавить код, который переносит пользовательские данные, его лучше выполнять только при первом запуске приложения. Прежде чем переносить данные, отобразите пользователю диалоговое окно с информацией о том, что происходит, почему вы рекомендуете это выполнить и что будет дальше с существующими данными.

Вот пример такого процесса в упакованном приложении на основе .NET.

```csharp
private void MigrateUserData()
{
    String sourceDir = Environment.GetFolderPath
        (Environment.SpecialFolder.ApplicationData) + "\\AppName";

    if (sourceDir != null)
    {
        DialogResult migrateResult = MessageBox.Show
            ("Would you like to migrate your data from the previous version of this app?",
             "Data Migration", MessageBoxButtons.YesNo);

        if (migrateResult.Equals(DialogResult.Yes))
        {
            String destinationDir =
                Windows.Storage.ApplicationData.Current.LocalFolder.Path + "\\AppName";

            Process process = new Process();
            process.StartInfo.FileName = "robocopy.exe";
            process.StartInfo.Arguments = "%LOCALAPPDATA%\\AppName " + destinationDir + " /move";
            process.StartInfo.CreateNoWindow = true;
            process.Start();
            process.WaitForExit();

            if (process.ExitCode > 1)
            {
                //Migration was unsuccessful -- you can choose to block/retry/other action
            }
        }
    }
}
```

### <a name="uninstall-the-desktop-version-of-your-app"></a>Удаление классической версии приложения

Желательно не удалять классическое приложение, не запросив разрешения пользователя. Отобразите диалоговое окно с запросом соответствующего разрешения. Пользователи могут решить не удалять классическую версию. В этом случае вам нужно решить, блокировать ли использование классического приложения или поддерживать параллельное использование обеих версий.

Вот пример такого процесса в упакованном приложении на основе .NET.

Полный контекст этого фрагмента см. в файле **MainWindow.cs** примера [Средство просмотра изображений WPF с переходом, переносом и удалением](https://github.com/Microsoft/DesktopBridgeToUWP-Samples/tree/master/Samples/DesktopAppTransition).

```csharp
private void RemoveDesktopApp()
{              
    //Typically, you can find your uninstall string at this location.
    String uninstallString = (String)Microsoft.Win32.Registry.GetValue
        (@"HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\Microsoft\Windows\CurrentVersion" +
         @"\Uninstall\{7AD02FB8-B85E-44BC-8998-F4803BA5A0E3}\", "UninstallString", null);

    //Detect if the previous version of the Desktop application is installed.
    if (uninstallString != null)
    {
        DialogResult uninstallResult = MessageBox.Show
            ("To have the best experience, consider uninstalling the "
              + " previous version of this app. Would you like to do that now?",
              "Uninstall the previous version", MessageBoxButtons.YesNo);

        if (uninstallResult.Equals(DialogResult.Yes))
        {
                    string[] uninstallArgs = uninstallString.Split(' ');

            Process process = new Process();
            process.StartInfo.FileName = uninstallArgs[0];
            process.StartInfo.Arguments = uninstallArgs[1];
            process.StartInfo.CreateNoWindow = true;

            process.Start();
            process.WaitForExit();

            if (process.ExitCode != 0)
            {
                //Uninstallation was unsuccessful - You can choose to block the application here.
            }
        }
    }

}
```

## <a name="next-steps"></a>Дальнейшие действия

Есть вопросы? Задайте их на Stack Overflow. Наша команда следит за этими [тегами](https://stackoverflow.com/questions/tagged/project-centennial+or+desktop-bridge). Вы также можете задать нам вопросы [здесь](https://social.msdn.microsoft.com/Forums/en-US/home?filter=alltypes&sort=relevancedesc&searchTerm=%5BDesktop%20Converter%5D).

Если при публикации приложения в Store возникнут проблемы, вы найдете полезные советы в этой [записи блога](https://blogs.msdn.microsoft.com/appconsult/2017/09/25/preparing-a-desktop-bridge-application-for-the-store-submission/).

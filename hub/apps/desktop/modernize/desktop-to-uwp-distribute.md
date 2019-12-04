---
Description: Распространение приложения из пакета Desktop Bridge
title: Опубликуйте упакованное настольное приложение на Microsoft Store или загружать неопубликованные его на одном или нескольких устройствах.
ms.date: 05/18/2018
ms.topic: article
keywords: windows 10, uwp
ms.assetid: edff3787-cecb-4054-9a2d-1fbefa79efc4
ms.author: mcleans
author: mcleanbyron
ms.localizationpriority: medium
ms.openlocfilehash: 15970afbeb5d9dee1c2079cd5933b1250ecb2f09
ms.sourcegitcommit: ae9c1646398bb5a4a888437628eca09ae06e6076
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/03/2019
ms.locfileid: "74734780"
---
# <a name="distribute-your-packaged-desktop-app"></a>Распространение упакованного классического приложения

Если вы решили [упаковать классическое приложение в пакет MSIX](/windows/msix/desktop/desktop-to-uwp-root), вы можете опубликовать упакованное приложение на Microsoft Store или загружать неопубликованные его на одном или нескольких устройствах.

> [!NOTE]
> Есть ли у вас план перехода пользователей в упакованное приложение? Перед распространением ознакомьтесь с разделом [Перевод пользователей на упакованное приложение](#transition-users) данного руководства, чтобы получить несколько идей.

## <a name="distribute-your-application-by-publishing-it-to-the-microsoft-store"></a>Распространение приложения путем его публикации в Microsoft Store

[Microsoft Store](https://www.microsoft.com/store/apps) — это самый удобный для пользователей способ получить ваше приложение.

Опубликуйте приложение в Microsoft Store для достижения самой широкой аудитории. Кроме того, клиенты организации могут получить ваше приложение для внутреннего распространения в своих организациях через [Microsoft Store для бизнеса](https://businessstore.microsoft.com/store).

Если вы планируете публиковать в Microsoft Store, вам будет предложено ответить на несколько дополнительных вопросов в ходе процесса отправки. Это означает, что манифест пакета объявляет возможность с ограниченным доступом под названием **runFullTrust**, и мы должны утвердить использование этой возможности приложением. Подробнее об этом сценарии требовании см. здесь [Ограниченные возможности](/windows/uwp/packaging/app-capability-declarations#restricted-capabilities).

Вам не нужно подписывать приложение перед его отправкой в магазин.

>[!IMPORTANT]
> Если вы планируете опубликовать приложение на Microsoft Store, убедитесь, что приложение работает правильно на устройствах под управлением Windows 10 S. Это требование к хранению. См. статью [Тестирование приложения для Windows на Windows 10 S](/windows/msix/desktop/desktop-to-uwp-test-windows-s).

<a id="side-load" />

## <a name="distribute-your-application-without-placing-it-onto-the-microsoft-store"></a>Распространение приложения без помещения его на Microsoft Store

Если вы предпочитаете распространять приложение без использования магазина, вы можете вручную распространить приложения на одно или несколько устройств.

Это имеет смысл, если вам необходим более жесткий контроль над процессом распространения, либо если вы не хотите участвовать в процессе сертификации Microsoft Store.

Чтобы распространить приложение на другие устройства без помещения его в хранилище, необходимо получить сертификат, подписать приложение с помощью этого сертификата, а затем загружать неопубликованные приложение на эти устройства.

Вы можете [создать сертификат](/windows/msix/package/create-certificate-package-signing) или получить его от известного поставщика, такого как [Verisign](https://www.verisign.com/).

Если вы планируете распространить приложение на устройства под управлением Windows 10 S, приложение должно быть подписано Microsoft Store поэтому перед распространением приложения на эти устройства необходимо пройти процесс отправки магазина.

Если вы создаете сертификат, его необходимо установить в хранилище сертификатов **Доверенные корневые** или **Доверенные лица** на каждом устройстве, которое запускает ваше приложение. Если сертификат получен от известного поставщика, в других системах необходимо установить только ваше приложение.  

> [!IMPORTANT]
> Убедитесь, что имя издателя на сертификате совпадает с именем издателя вашего приложения.

Чтобы подписать приложение с помощью сертификата, см. раздел [Подписывание пакета приложения с помощью средства SignTool](/windows/msix/package/sign-app-package-using-signtool).

Чтобы загружать неопубликованные приложение на другие устройства, см. статью [загружать НЕОПУБЛИКОВАННЫЕ LOB Apps в Windows 10](/windows/application-management/sideload-apps-in-windows-10).

<a id="transition-users" />

## <a name="transition-users-to-your-packaged-app"></a>Перевод пользователей на ваше упакованное приложение

Перед распространением приложения рекомендуется добавить несколько расширений в ваш манифест пакета, чтобы помочь пользователям привыкнуть использовать ваше упакованное приложение. Вот пара советов, что можно сделать.

* Определите существующие плитки начального экрана и кнопки на панели задач в качестве указателей на упакованное приложение.
* Свяжите упакованное приложение с набором типов файлов.
* По умолчанию заставить упакованное приложение открывать определенные типы файлов.

Полный перечень расширений и рекомендации по их использованию см. в разделе [Переход пользователей на ваше приложение](desktop-to-uwp-extensions.md#transition-users-to-your-app).

Кроме того, рассмотрите возможность добавления кода в упакованное приложение, которое выполняет следующие задачи:

* Переносит данные пользователя, связанные с настольным приложением, в соответствующие расположения папок упакованного приложения.
* Позволяет пользователям удалить классическую версию вашего приложения.

Рассмотрим каждую из этих задач. Начнем с переноса пользовательских данных.

### <a name="migrate-user-data"></a>Перенос пользовательских данных

Если вы собираетесь добавить код, который выполняет миграцию пользовательских данных, лучше запускать этот код только при первом запуске приложения. Прежде чем переносить данные, отобразите пользователю диалоговое окно, в котором сообщите о том, что происходит, почему этим рекомендуется воспользоваться и что случится с их имеющимися данными.

Вот пример того, как это можно выполнить в упакованном приложении на основе .NET.

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

### <a name="uninstall-the-desktop-version-of-your-app"></a>Удаление классической версии вашего приложения

Лучше не удалять классическое приложение "Пользователи" без предварительного запроса разрешения. Отобразите диалоговое окно с запросом соответствующего разрешения. Пользователи могут решить не удалять классическую версию. В таком случае необходимо решить, следует ли блокировать использование классического приложения или поддерживать параллельное использование обоих приложений.

Вот пример того, как это можно выполнить в упакованном приложении на основе .NET.

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

**Поиск ответов на вопросы**

Есть вопросы? Задайте их на Stack Overflow. Наша команда следит за этими [тегами](https://stackoverflow.com/questions/tagged/project-centennial+or+desktop-bridge). Вы также можете задать нам вопросы [здесь](https://social.msdn.microsoft.com/Forums/en-US/home?filter=alltypes&sort=relevancedesc&searchTerm=%5BDesktop%20Converter%5D).

Если при публикации вашего приложения в Store возникнут проблемы, вы найдете полезные советы в этой [записи в блоге](https://blogs.msdn.microsoft.com/appconsult/2017/09/25/preparing-a-desktop-bridge-application-for-the-store-submission/).

**Отправьте отзыв или получите предложения по функциям**

См. раздел [UserVoice](https://wpdev.uservoice.com/forums/110705-universal-windows-platform/category/161895-desktop-bridge-centennial)

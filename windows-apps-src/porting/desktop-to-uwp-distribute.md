---
Description: Распространение упакованного приложения рабочего стола (мост для классических приложений)
Search.Product: eADQiWindows 10XVcnh
title: Публикация упакованные приложения рабочего стола в Microsoft Store или загружать неопубликованные его на одном или нескольких устройствах.
ms.date: 05/18/2018
ms.topic: article
keywords: windows 10, uwp
ms.assetid: edff3787-cecb-4054-9a2d-1fbefa79efc4
ms.localizationpriority: medium
ms.openlocfilehash: 8968864a0ff4bcf9e27f75a44a0a500736bb54b8
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57619699"
---
# <a name="distribute-a-packaged-desktop-application"></a>Распределить упакованные настольного приложения

Публикация упакованные приложения рабочего стола в Microsoft Store или загружать неопубликованные его на одном или нескольких устройствах.  

> [!NOTE]
> У вас есть план как пользователи могут перейти на упакованные приложения? Перед распространением ознакомьтесь с разделом [Перевод пользователей на упакованное приложение](#transition-users) данного руководства, чтобы получить несколько идей.

## <a name="distribute-your-application-by-publishing-it-to-the-microsoft-store"></a>Распространение приложения путем публикации Microsoft Store

[Microsoft Store](https://www.microsoft.com/store/apps) — это самый удобный для пользователей способ получить ваше приложение.

Опубликуйте приложение в Microsoft Store для достижения широчайшее. Кроме того, пользователи организации могут получить приложения для распространения внутри организации через [Microsoft Store для бизнеса](https://www.microsoft.com/business-store).

Если вы планируете публиковать в Microsoft Store, вам будет предложено ответить на несколько дополнительных вопросов в ходе процесса отправки. Это означает, что манифест пакета объявляет возможность с ограниченным доступом под названием **runFullTrust**, и мы должны утвердить использование этой возможности приложением. Дополнительные сведения об этом требовании здесь: [Ограниченные возможности](https://docs.microsoft.com/windows/uwp/packaging/app-capability-declarations#restricted-capabilities).

Регистрация приложения перед отправкой Store не требуется.

>[!IMPORTANT]
> Если вы планируете опубликовать приложение в Microsoft Store, убедитесь, что приложение правильно работает на устройствах под управлением Windows 10 S. Это обязательное требование Store. См. статью [Тестирование приложения для Windows на Windows 10 S](desktop-to-uwp-test-windows-s.md).

<a id="side-load" />

## <a name="distribute-your-application-without-placing-it-onto-the-microsoft-store"></a>Распространение приложения без его помещения в Microsoft Store

Если бы вместо этого распространения приложения без использования Store, можно вручную распространять приложения для одного или нескольких устройств.

Это имеет смысл, если вам необходим более жесткий контроль над процессом распространения, либо если вы не хотите участвовать в процессе сертификации Microsoft Store.

Чтобы распространять приложения с другими устройствами без его помещения в Store, вы должны получить сертификат, подписать приложение с помощью этого сертификата, а затем загружать неопубликованные приложения на этих устройствах.

Вы можете [создать сертификат](../packaging/create-certificate-package-signing.md) или получить его от известного поставщика, такого как [Verisign](https://www.verisign.com/).

Если вы планируете распространять приложения на устройствах под управлением Windows 10 S, приложение должно быть подписаны Microsoft Store, поэтому вы должны пройти процесс отправки Store, чтобы иметь возможность распространения приложения на этих устройствах.

Если вы создаете сертификат, его необходимо установить в хранилище сертификатов **Доверенные корневые** или **Доверенные лица** на каждом устройстве, которое запускает ваше приложение. Если сертификат получен от известного поставщика, в других системах необходимо установить только ваше приложение.  

> [!IMPORTANT]
> Убедитесь, что имя издателя на сертификате совпадает с именем издателя вашего приложения.

Чтобы подписать приложение с помощью сертификата, см. в разделе [подписи пакета приложения с помощью SignTool](../packaging/sign-app-package-using-signtool.md).

Загрузка неопубликованного приложения на другие устройства, см. в разделе [неопубликованные бизнес-приложений в Windows 10](https://technet.microsoft.com/itpro/windows/deploy/sideload-apps-in-windows-10).

**Просмотр видео**

|Опубликуйте приложение в Microsoft Store |Распространения корпоративного приложения  |
|---|---|
|<iframe src="https://mva.microsoft.com/en-US/training-courses-embed/developers-guide-to-the-desktop-bridge-17373/Demo-Windows-Store-Publication-3cWyG5WhD_5506218965"      width="426" height="472" allowFullScreen frameBorder="0"></iframe>|<iframe src="https://mva.microsoft.com/en-US/training-courses-embed/developers-guide-to-the-desktop-bridge-17373/Video-Distribution-for-Enterprise-Apps-XJ5Hd5WhD_1106218965" width="426" height="472" allowFullScreen frameBorder="0"></iframe>|

<a id="transition-users" />

## <a name="transition-users-to-your-packaged-app"></a>Перевод пользователей на ваше упакованное приложение

Перед распространением приложения рекомендуется добавить несколько расширений в ваш манифест пакета, чтобы помочь пользователям привыкнуть использовать ваше упакованное приложение. Вот пара советов, что можно сделать.

* Определите существующие плитки начального экрана и кнопки на панели задач в качестве указателей на упакованное приложение.
* Упакованные приложения следует сопоставить с набором типов файлов.
* Сделать упакованные приложения откройте определенных типов файлов по умолчанию.

Полный перечень расширений и рекомендации по их использованию см. в разделе [Переход пользователей на ваше приложение](desktop-to-uwp-extensions.md#transition-users-to-your-app).

Кроме того рассмотрите возможность добавления кода в пакете приложения, которые выполняют эти задачи:

* Перенос данных пользователя, связанный с приложением рабочего стола в соответствующую папку расположения упакованные приложения.
* Позволяет пользователям удалить классическую версию вашего приложения.

Рассмотрим каждую из этих задач. Начнем с переноса пользовательских данных.

### <a name="migrate-user-data"></a>Перенос пользовательских данных

Если вы собираетесь добавить код, который переносит данные пользователя, лучше запустить этот код только в том случае, при первом запуске приложения. Прежде чем переносить данные, отобразите пользователю диалоговое окно, в котором сообщите о том, что происходит, почему этим рекомендуется воспользоваться и что случится с их имеющимися данными.

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

Лучше не удалить пользователей классическое приложение без подтверждения их разрешения. Отобразите диалоговое окно с запросом соответствующего разрешения. Пользователи могут решить не удалять классическую версию. Если это произойдет, вам придется решить, хотите ли вы блокирует использование классического приложения или поддерживает использование side-by-side оба приложения.

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

### <a name="video"></a>Видео

<iframe src="https://mva.microsoft.com/en-US/training-courses-embed/developers-guide-to-the-desktop-bridge-17373/Demo-Transition-Taskbar-Pins-Start-Tiles-File-Type-Associations-and-Protocol-Handlers-MD5mv5WhD_2406218965" width="636" height="480" allowFullScreen frameBorder="0"></iframe>

## <a name="next-steps"></a>Дальнейшие действия

**Найдите ответы на ваши вопросы**

Есть вопросы? Задайте их на Stack Overflow. Наша команда следит за этими [тегами](https://stackoverflow.com/questions/tagged/project-centennial+or+desktop-bridge). Вы также можете задать нам вопросы [здесь](https://social.msdn.microsoft.com/Forums/en-US/home?filter=alltypes&sort=relevancedesc&searchTerm=%5BDesktop%20Converter%5D).

Если при публикации вашего приложения в Store возникнут проблемы, вы найдете полезные советы в этой [записи в блоге](https://blogs.msdn.microsoft.com/appconsult/2017/09/25/preparing-a-desktop-bridge-application-for-the-store-submission/).

**Отправить отзыв или предложения по функциям**

См. раздел [UserVoice](https://wpdev.uservoice.com/forums/110705-universal-windows-platform/category/161895-desktop-bridge-centennial)

---
author: mcleanbyron
ms.assetid: 414ACC73-2A72-465C-BD15-1B51CB2334F2
title: "Скачивание и установка обновлений пакетов для приложения"
description: "Узнайте, как пометить пакеты как обязательные на информационной панели Центра разработки и написать код в приложении для загрузки и установки обновлений пакетов."
ms.author: mcleans
ms.date: 03/15/2017
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: Windows 10, uwp
ms.openlocfilehash: 07b8b769cbcaf86bfa70a562de568cab65c91a77
ms.sourcegitcommit: 909d859a0f11981a8d1beac0da35f779786a6889
translationtype: HT
---
# <a name="download-and-install-package-updates-for-your-app"></a>Скачивание и установка обновлений пакетов для приложения

\[ Обновлено для приложений UWP в Windows10. Статьи для Windows 8.x см. в [архиве](http://go.microsoft.com/fwlink/p/?linkid=619132) \]

Начиная с Windows 10 версии 1607, можно использовать API-интерфейс в пространстве имен [Windows.Services.Store](https://docs.microsoft.com/uwp/api/windows.services.store), чтобы программным способом проверить наличие пакета обновления для текущего приложения и загрузить и установить обновленные пакеты. Можно также запрашивать пакеты, которые были [помечены как обязательные на информационной панели Центра разработки для Windows](#mandatory-dashboard), и отключать функциональные возможности приложения, пока обязательное обновление не будет установлено.

Эти функции помогают автоматически поддерживать базу пользователей в актуальном состоянии, используя последние версии приложения и сопутствующих услуг.

## <a name="api-overview"></a>Обзор языка API

Приложения, предназначенные для Windows 10 версии 1607 или более поздней, могут использовать следующие методы класса [StoreContext](https://docs.microsoft.com/uwp/api/windows.services.store.storecontext) для скачивания и установки обновлений пакетов.

|  Метод  |  Описание  |
|----------|---------------|
| [GetAppAndOptionalStorePackageUpdatesAsync](https://docs.microsoft.com/uwp/api/windows.services.store.storecontext#Windows_Services_Store_StoreContext_GetAppAndOptionalStorePackageUpdatesAsync) | Вызовите этот метод, чтобы получить список доступных обновлений пакетов. Обратите внимание, что может быть задержка продолжительности до одного дня между временем прохождения процесса сертификации приложением и временем, когда метод **GetAppAndOptionalStorePackageUpdatesAsync** обнаружит обновление пакета для приложения. |
| [RequestDownloadStorePackageUpdatesAsync](https://docs.microsoft.com/uwp/api/windows.services.store.storecontext#Windows_Services_Store_StoreContext_RequestDownloadStorePackageUpdatesAsync_Windows_Foundation_Collections_IIterable_Windows_Services_Store_StorePackageUpdate__) | Вызовите этот метод, чтобы скачать (но не устанавливать) доступные обновления пакета. Эта операционная система отображает диалоговое окно с запросом разрешения пользователя на скачивание обновлений. |
| [RequestDownloadAndInstallStorePackageUpdatesAsync](https://docs.microsoft.com/uwp/api/windows.services.store.storecontext#Windows_Services_Store_StoreContext_RequestDownloadAndInstallStorePackageUpdatesAsync_Windows_Foundation_Collections_IIterable_Windows_Services_Store_StorePackageUpdate__) | Вызовите этот метод, чтобы скачать и установить доступные обновления пакета. Операционная система отображает диалоговые окна с запросом разрешения пользователя на скачивание и установку обновлений. Если вы уже скачали обновления пакета, вызвав метод **RequestDownloadStorePackageUpdatesAsync**, этот метод пропускает процесс скачивания и только устанавливает обновления.  |

<span/>

Эти методы используют объекты [StorePackageUpdate](https://docs.microsoft.com/uwp/api/windows.services.store.storepackageupdate) для представления доступных пакетов обновления. Используйте следующие свойства **StorePackageUpdate** для получения сведений о пакете обновления.

|  Свойство  |  Описание  |
|----------|---------------|
| [Обязательное](https://docs.microsoft.com/uwp/api/windows.services.store.storepackageupdate#Windows_Services_Store_StorePackageUpdate_Mandatory) | Используйте это свойство, чтобы определить, помечен ли пакет как обязательный на панели мониторинга Центра разработки. |
| [Package](https://docs.microsoft.com/uwp/api/windows.services.store.storepackageupdate#Windows_Services_Store_StorePackageUpdate_Package) | Используйте этой свойство, чтобы получить доступ к базовым данным пакета. |

<span/>

## <a name="code-examples"></a>Примеры кода

В следующем примере кода показано, как скачать и установить пакет обновления в приложении. В этом примере предполагается следующее:
* Код выполняется в контексте [страницы](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.page.aspx).
* **Страница** содержит панель [ProgressBar](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.progressbar.aspx) с именем ```downloadProgressBar``` для предоставления состояния операции загрузки.
* Файл кода имеет заявление **using** для пространств имен **Windows.Services.Store**, **Windows.Threading.Tasks** и **Windows.UI.Popups**.
* Приложение — однопользовательское и выполняется только в контексте пользователя, запустившего его. Для [многопользовательского приложения](https://msdn.microsoft.com/windows/uwp/xbox-apps/multi-user-applications) используйте метод [GetForUser](https://docs.microsoft.com/uwp/api/windows.services.store.storecontext#Windows_Services_Store_StoreContext_GetForUser_Windows_System_User_), чтобы получить объект **StoreContext**, а не метод [GetDefault](https://docs.microsoft.com/uwp/api/windows.services.store.storecontext#Windows_Services_Store_StoreContext_GetDefault).

<span/>

### <a name="download-and-install-all-package-updates"></a>Скачивание и установка всех обновлений пакетов

В следующем примере кода показано, как загрузить и установить все доступные обновления пакета.  

```csharp
private StoreContext context = null;

public async Task DownloadAndInstallAllUpdatesAsync()
{
    if (context == null)
    {
        context = StoreContext.GetDefault();
    }

    // Get the updates that are available.
    IReadOnlyList<StorePackageUpdate> updates =
        await context.GetAppAndOptionalStorePackageUpdatesAsync();

    if (updates.Count > 0)
    {
        // Alert the user that updates are available and ask for their consent
        // to start the updates.
        MessageDialog dialog = new MessageDialog(
            "Download and install updates now? This may cause the application to exit.", "Download and Install?");
        dialog.Commands.Add(new UICommand("Yes"));
        dialog.Commands.Add(new UICommand("No"));
        IUICommand command = await dialog.ShowAsync();

        if (command.Label.Equals("Yes", StringComparison.CurrentCultureIgnoreCase))
        {
            // Download and install the updates.
            IAsyncOperationWithProgress<StorePackageUpdateResult, StorePackageUpdateStatus> downloadOperation =
                context.RequestDownloadAndInstallStorePackageUpdatesAsync(updates);

            // The Progress async method is called one time for each step in the download
            // and installation process for each package in this request.
            downloadOperation.Progress = async (asyncInfo, progress) =>
            {
                await this.Dispatcher.RunAsync(Windows.UI.Core.CoreDispatcherPriority.Normal,
                () =>
                {
                    downloadProgressBar.Value = progress.PackageDownloadProgress;
                });
            };

            StorePackageUpdateResult result = await downloadOperation.AsTask();
        }
    }
}
```

### <a name="handle-mandatory-package-updates"></a>Обработка обязательных обновлений пакета

Следующий пример кода основан на предыдущем примере и показывает, как определить, были ли пакты обновлений [помечены как обязательные на информационной панели Центра разработки для Windows](#mandatory-dashboard). Как правило, если обязательное обновление пакета не скачивается или не устанавливается успешно, необходимо правильно понизить версию приложения для пользователя.

```csharp
private StoreContext context = null;

// Downloads and installs package updates in separate steps.
public async Task DownloadAndInstallAllUpdatesAsync()
{
    if (context == null)
    {
        context = StoreContext.GetDefault();
    }  
    // Get the updates that are available.
    IReadOnlyList<StorePackageUpdate> updates =
        await context.GetAppAndOptionalStorePackageUpdatesAsync();

    if (updates.Count != 0)
    {
        // Download the packages.
        bool downloaded = await DownloadPackageUpdatesAsync(updates);

        if (downloaded)
        {
            // Install the packages.
            await InstallPackageUpdatesAsync(updates);
        }
    }
}

// Helper method for downloading package updates.
private async Task<bool> DownloadPackageUpdatesAsync(IEnumerable<StorePackageUpdate> updates)
{
    bool downloadedSuccessfully = false;

    IAsyncOperationWithProgress<StorePackageUpdateResult, StorePackageUpdateStatus> downloadOperation =
        this.context.RequestDownloadStorePackageUpdatesAsync(updates);

    // The Progress async method is called one time for each step in the download process for each
    // package in this request.
    downloadOperation.Progress = async (asyncInfo, progress) =>
    {
        await this.Dispatcher.RunAsync(Windows.UI.Core.CoreDispatcherPriority.Normal,
        () =>
        {
            downloadProgressBar.Value = progress.PackageDownloadProgress;
        });
    };

    StorePackageUpdateResult result = await downloadOperation.AsTask();

    switch (result.OverallState)
    {
        case StorePackageUpdateState.Completed:
            downloadedSuccessfully = true;
            break;
        default:
            // Get the failed updates.
            var failedUpdates = result.StorePackageUpdateStatuses.Where(
                status => status.PackageUpdateState != StorePackageUpdateState.Completed);

            // See if any failed updates were mandatory
            if (updates.Any(u => u.Mandatory && failedUpdates.Any(
                failed => failed.PackageFamilyName == u.Package.Id.FamilyName)))
            {
                // At least one of the updates is mandatory. Perform whatever actions you
                // want to take for your app: for example, notify the user and disable
                // features in your app.
                HandleMandatoryPackageError();
            }
            break;
    }

    return downloadedSuccessfully;
}

// Helper method for installing package updates.
private async Task InstallPackageUpdatesAsync(IEnumerable<StorePackageUpdate> updates)
{
    IAsyncOperationWithProgress<StorePackageUpdateResult, StorePackageUpdateStatus> installOperation =
        this.context.RequestDownloadAndInstallStorePackageUpdatesAsync(updates);

    // The package updates were already downloaded separately, so this method skips the download
    // operatation and only installs the updates; no download progress notifications are provided.
    StorePackageUpdateResult result = await installOperation.AsTask();

    switch (result.OverallState)
    {
        case StorePackageUpdateState.Completed:
            break;
        default:
            // Get the failed updates.
            var failedUpdates = result.StorePackageUpdateStatuses.Where(
                status => status.PackageUpdateState != StorePackageUpdateState.Completed);

            // See if any failed updates were mandatory
            if (updates.Any(u => u.Mandatory && failedUpdates.Any(failed => failed.PackageFamilyName == u.Package.Id.FamilyName)))
            {
                // At least one of the updates is mandatory, so tell the user.
                HandleMandatoryPackageError();
            }
            break;
    }
}

// Helper method for handling the scenario where a mandatory package update fails to
// download or install. Add code to this method to perform whatever actions you want
// to take, such as notifying the user and disabling features in your app.
private void HandleMandatoryPackageError()
{
}
```

### <a name="display-progress-info-for-the-download-and-install"></a>Отображение хода выполнения скачивания и установки

При вызове **RequestDownloadStorePackageUpdatesAsync** или **RequestDownloadAndInstallStorePackageUpdatesAsync** можно назначить обработчик [Progress](https://docs.microsoft.com/uwp/api/windows.foundation.iasyncoperationwithprogress_tresult_tprogress_#Windows_Foundation_IAsyncOperationWithProgress_2_Progress), который будет вызываться один раз для каждого шага при скачивании (или скачивании и установке) для каждого пакета в этом запросе. Обработчик получает объект [StorePackageUpdateStatus](https://docs.microsoft.com/uwp/api/windows.services.store.storepackageupdatestatus), который предоставляет сведения о пакете обновления, создавшем уведомление о ходе выполнения. В предыдущих примерах используется поле **PackageDownloadProgress** объекта **StorePackageUpdateStatus** для отображения хода выполнения скачивания и установки.

Не забывайте, что при вызове **RequestDownloadAndInstallStorePackageUpdatesAsync** для скачивания и установки обновлений пакета в одной операции поле **PackageDownloadProgress** увеличивается с 0.0 до 0.8 во время скачивания для пакета, а затем увеличивается с 0.8 до 1.0 во время установки. Поэтому при сопоставлении процента, показанного в специальном интерфейсе хода выполнения для значения поля **PackageDownloadProgress** ваш интерфейс будет отображать 80%, когда скачивание пакета закончится, а ОС отобразит диалоговое окно установки. Если вы хотите, чтобы специальный интерфейс хода выполнения отображал 100%, когда пакет будет скачан и готов к установке, вы можете изменить код и назначить 100% интерфейсу хода выполнения, когда значение поля **PackageDownloadProgress** достигнет 0.8.

<span id="mandatory-dashboard" />
## <a name="make-a-package-submission-mandatory-in-the-dev-center-dashboard"></a>Как сделать отправку пакета обязательной на информационной панели Центра разработки

При создании отправки пакета для приложения, которое предназначено для Windows 10 версии 1607 или выше, можно пометить пакет как обязательный и указать дату и время, когда он становится обязательным. Если это свойство задано и приложение обнаружит, что обновление пакета доступно, используя API-интерфейс, описанный ранее в этой статье, приложение может определить, является ли пакет обновлений одинаковым, и изменить его поведение до тех пор, пока обновление не будет установлено (например, приложение может отключить те или иные компоненты).

> [!NOTE]
> Обязательный статус обновления пакета не реализуется Майкрософт принудительно, и ОС не предоставляет пользовательский интерфейс, указывающий пользователям, что обязательное обновление приложения необходимо установить. Разработчики должны использовать обязательную настройку, чтобы принудительно реализовать обязательные обновления приложений в своем коде.  

Чтобы пометить отправку пакета как обязательную, выполните следующие действия:

1. Выполните вход на [информационную панель Центра разработки](https://dev.windows.com/overview) и перейдите на страницу обзора для вашего приложения.
2. Щелкните имя отправки, содержащий пакет обновления, которые вы хотите сделать обязательным.
3. Перейдите на страницу **Пакеты** для этой отправки. В нижней части страницы выберите **Сделать это обновление обязательным** и выберите день и время, когда пакет станет обязательным. Этот параметр применяется ко всем пакетам UWP в отправке.

Дополнительные сведения о настройке пакетов на информационной панели Центра разработки см. в разделе [Отправка пакетов приложения](../publish/upload-app-packages.md).

  > [!NOTE]
  > Если вы создаете [тестовый пакет](../publish/package-flights.md), то можете отметить пакеты как обязательные с помощью аналогичного пользовательского интерфейса на странице **Packages** для тестируемой возможности. В этом случае обязательное обновление пакета применяется только к клиентам, которые являются частью тестовой группы.
